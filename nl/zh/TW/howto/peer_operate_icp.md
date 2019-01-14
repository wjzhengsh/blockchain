---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 使用多雲端網路在 {{site.data.keyword.cloud_notm}} Private 中操作對等節點
{: #peer-operate_icp}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

在 {{site.data.keyword.cloud_notm}} Private (ICP) 對等節點上設定 {{site.data.keyword.blockchainfull}} Platform 之後，您需要先完成數個作業步驟，對等節點才能發出交易，以查詢及呼叫區塊鏈網路的分類帳。這些步驟包括將組織新增至頻道、將對等節點加入頻道、在對等節點上安裝鏈碼、在頻道上實例化鏈碼，以及將應用程式連接至對等節點。如果您要將對等節點連接至「入門範本方案」或「企業方案」網路，請參閱[使用入門範本方案或企業方案在 {{site.data.keyword.cloud_notm}} Private 上操作對等節點](peer_operate_ibp.html#peer-operate_icp)。
{:shortdesc}

您需要從「ICP 叢集」完成一些必要步驟，才能操作對等節點。

**必要條件：**
- [配置 CLI](#peer-kubectl-configure)
- [擷取對等節點端點資訊](#peer-endpoint)
- [下載對等節點 TLS 憑證](#peer-tls)

然後，您可以使用下列其中一種方法來操作對等節點。

**作業路徑：**
- [使用 Fabric SDK](#peer-operate-with-sdk)
- [使用指令行](#peer-cli-operate)

雖然指示假設熟悉 SDK 的作業，但 Fabric SDK 是建議的路徑。如果您偏好使用指令行，則可以使用 Fabric 對等節點二進位檔。

如果您的組織還不是聯盟或頻道的成員，您可以利用這些步驟來[建立頻道](#create-channel)。這些指示將引導您如何[準備組織定義](#organization-definition)。透過新增至排序節點系統頻道，此定義可用來讓您成為該聯盟的成員。之後，您就能夠透過[建立頻道交易](#peer-icp-channeltx)來組成新頻道
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

## 必要條件

不論您計劃使用 SDK 還是指令行，都需要在操作對等節點的過程中完成本節中的步驟。您可以先完成所有這些步驟，再開始進行。

### 配置 CLI
{: #peer-kubectl-configure}

您需要使用 **kubectl** 指令行工具，以連接至 ICP 中執行的對等節點容器。

1. 登入 ICP 叢集使用者介面。導覽至**指令行工具**標籤，然後按一下 **Cloud Private CLI**。您將看到下列可下載的工具。

   * 安裝 IBM Cloud Private CLI 及外掛程式
   * 安裝 Kubectl CLI
   * 安裝 Helm
   * 安裝 Istio CLI

  若要操作對等節點，您需要使用前**三個**工具，而 Helm 在其中為選用項目。按一下每個項目，然後針對您所使用的機型，執行 `curl` 指令。接著，針對每個工具發出 `chmod` 及 `sudo mv` 指令。`chmod` 指令將變更所提及之 CLI 的許可權以使其可執行，而 `sudo mv` 指令將移動該檔案並予以重新命名。

  第一個工具 **cloudctl** 的指令可能類似下列範例：

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  第二個工具 **kubectl** 的指令可能類似下列範例：

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. 在您配置 **kubectl** 之後，請執行下列指令：

  ```
  kubectl get pods
  ```
  {:codeblock}

  如果順利完成，則此指令會傳回與下列範例類似的回應，其中 `peer-6cf85f6687-hcxpl` 是對等節點容器的名稱。

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  您現在可以使用 **kubectl** 工具來取得對等節點端點資訊。

3. 如果您要選擇性地使用 **Helm**，則請另外完成一些步驟。請注意，Helm 是選用的安裝項目，您不需要在這些指示中使用該工具。不過，若要管理 Helm 版本以及建立您自己的保存檔以便在 ICP 中進行部署時，它十分有用。

  1. 按一下「安裝 Helm」，然後從 ICP 使用者介面中執行 `curl` 指令。
  2. 執行下列指令來解壓縮 `tar` 檔案：

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. 將 `/helm` 附加至 `darwin-amd64`，以執行 `sudo mv` 指令。請注意，您不需要對 Helm 執行 `chmod` 指令。例如：

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  您可以執行 `helm help` 指令，確認已順利安裝 Helm。

### 擷取對等節點端點資訊
{: #peer-endpoint}

您需要從 SDK 或 Fabric CA 用戶端中將對等節點端點設為目標，以便加入頻道或安裝智慧型合約。您可以使用 ICP 主控台使用者介面來尋找對等節點的端點。您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能完成下列步驟：

1. 登入 ICP 主控台，然後按一下左上角的**功能表**圖示。
2. 按一下**工作負載** > **Helm 版本**。
3. 尋找「Helm 版本」的名稱，然後開啟「Helm 版本詳細資料」畫面。
4. 向下捲動至畫面底端的**附註**區段。在**附註**區段中，您會看到一組可協助您操作對等節點部署的指令。
5. 如果您還沒有這麼做，請遵循步驟來配置 [kubeclt CLI](#peer-kubectl-configure)。您將使用 kubectl 與對等節點容器進行互動。
6. 在 CLI 中，執行 **1. 執行下列指令，以取得應用程式 URL：**之後附註中的第一個指令。此指令會印出對等節點 URL 及埠，其與下面範例類似。請儲存此 URL 以供未來指令使用。

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**附註：**如果您部署受防火牆保護的對等節點，則需要開啟透通功能來啟用平台上的網路，以完成與對等節點的 TlS 信號交換。您只需要針對連結至對等節點埠 7051 的 Node 埠啟用透通功能。如需相關資訊，請參閱[尋找對等節點端點資訊](peer_operate_ibp.html#peer-endpoint)。

### 下載對等節點 TLS 憑證
{: #peer-tls}

您需要下載對等節點 TLS 憑證，並將其傳遞至指令，才能從遠端用戶端與您的對等節點通訊。

1. 登入 ICP 主控台，然後按一下左上角的**功能表**圖示。
2. 按一下**工作負載** > **Helm 版本**。
3. 尋找「Helm 版本」的名稱，然後開啟「Helm 版本詳細資料」畫面。
4. 向下捲動至畫面底端的**附註**區段。在**附註**區段中，您會看到一組可協助您操作對等節點部署的指令。
5. 如果您還沒有這麼做，請遵循步驟來配置 [kubeclt CLI](#peer-kubectl-configure)。您將使用 kubectl 與對等節點容器進行互動。
6. 在 CLI 中，執行 **3. 取得對等節點 TLS 根憑證檔案**之後附註中的第一個指令。此指令會將您的 TLS 憑證儲存為本端機器上的 `cacert.pem` 檔案。
7. 將憑證移至您可以在未來指令中參照它的位置，並將其重新命名為 `peertls.pem`。

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## 使用 Fabric SDK 來操作對等節點
{: #peer-operate-with-sdk}

Hyperledger Fabric SDK 提供一組功能強大的 API，可讓應用程式與區塊鏈網路互動，以及操作區塊鏈網路。您可以在 [Hyperledger Fabric SDK 社群文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 社群文件") 中，找到所支援語言的最新清單，以及 Fabric SDK 內可用 API 的完整清單。您可以使用 Fabric SDK，將對等節點加入至 {{site.data.keyword.blockchainfull_notm}} Platform 上的頻道、在對等節點上安裝鏈碼，以及在頻道上實例化鏈碼。

下列指示會使用 [Fabric Node SDK ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Fabric Node SDK") 來操作對等節點，並假設事先已熟悉 SDK。您可以使用[開發應用程式指導教學](../v10_application.html)，瞭解如何在開始之前使用 Node SDK，並引導您在準備好呼叫及查詢鏈碼時，使用對等節點來開發應用程式。

### 安裝 Node SDK

您可以使用 NPM 來安裝 [Node SDK ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Node SDK")：

```
npm install fabric-client@1.2
```
{:codeblock}

建議您使用 1.2 版的 Node SDK。

### 準備 SDK 以使用對等節點
{: #peer-node-sdk}

您的對等節點已隨內部對等節點管理者的 signCert 一起部署。這可讓您使用對等節點管理者的憑證及 MSP 資料夾來操作該對等節點。

尋找您[登記對等節點管理者](CA_operate.html#enroll-admin)時所建立的憑證。如果您已使用指令範例，則可以在 `$HOME/fabric-ca-client/peer-admin` 找到對等節點管理者 MSP 資料夾。
  - 您可以利用 MSP 資料夾中的 signCert（公開金鑰）及私密金鑰，透過 SDK 建置對等節點管理使用者的環境定義。您可以在下列位置找到那些金鑰：
    - 可在 **signcerts** 資料夾中找到 signCert：`$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - 可在 **keystore** 資料夾中找到私密金鑰：`$HOME/fabric-ca-client/peer-admin/msp/keystore`
如需示範如何構成使用者環境定義以及只使用公開和私密金鑰來操作 SDK 的範例，請參閱[使用網路監視器產生憑證](../v10_application.html#enroll-panel)。

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](CA_operate.html#register-admin). -->

### 將對等節點的 TLS 憑證傳遞至 SDK
{: #icp-peer-download-tlscert}

您需要參照對等節點的 TLS 憑證，以鑑別來自 SDK 的通訊。請找到您[從對等節點容器下載](#peer-tls)的 TLS 憑證，然後將其儲存在您的應用程式可以參照它的位置。如果您已使用指令範例，則可以在 `$HOME/fabric-ca-client/peer-tls/peertls.pem` 找到對等節點 TLS 憑證。然後，您可以使用簡單的讀取檔案指令，將 TLS 憑證匯入至應用程式。

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### 將對等節點端點資訊提供給 SDK
{: #peer-SDK-endpoints}

請尋找[對等節點的端點資訊](#peer-endpoint)，並透過宣告新的對等節點變數，將它提供給 SDK。下列範例將對等節點定義為光纖網路上的端點，並將您匯入的 TLS 憑證傳遞給它。

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

如果您有大型網路，且有多個對等節點屬於需要為您的交易背書的不同組織，則您可能會想要使用聯盟來[建置一般連線設定檔 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-network-config.html "一般連線設定檔")

### 將排序節點的 TLS 憑證傳遞至 SDK
{: #icp-orderer-download-tlscert}

您還需要聯盟之排序節點的 TLS 憑證，才能加入頻道及提交交易。如果您是排序節點的管理者，請遵循[下載排序節點 TLS 憑證](orderer_operate.html#orderer-tls)的指示。如果您已使用指令範例，則可以在 `$HOME/fabric-ca-client/orderer-tls/orderertls.pem` 找到對等節點 TLS 憑證。如果排序節點是由另一個組織所控管，他們需要在頻內及頻外作業中為您提供 TLS 憑證。然後，您可以將 TLS 憑證匯入至應用程式。

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### 將排序節點資訊提供給 SDK
{: #orderer-SDK-endpoints}

若要使用 SDK，您還需要聯盟中排序節點的端點資訊。如果您是排序節點的管理者，可以使用這些指示來[擷取排序節點端點資訊](orderer_operate.html#orderer-endpoint)。如果排序節點是由另一個組織所控管，他們需要為您提供頻內及頻外作業的排序節點 URL。下列範例將排序節點定義為端點，並將排序節點 TLS 憑證傳遞給它。

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### 使用 SDK 來加入頻道
{: #peer-join-channel-sdk}

您的組織必須是頻道的成員，您才能將對等節點加入至該頻道。如果您不是頻道的成員，則可以遵循[建立新頻道](#create-channel)的指示。

當您的組織變成頻道的成員之後，請遵循指示使用 SDK [將對等節點加入頻道](../v10_application.html#join-channel-sdk)。

### 使用 SDK 在對等節點上安裝鏈碼
{: #peer-install-cc-sdk}

請遵循下列指示使用 SDK，在您的對等節點上[安裝鏈碼](../v10_application.html#install-cc-sdk)。

### 使用 SDK 在頻道上實例化鏈碼
{: #peer-instantiate-cc-sdk}

只有一個頻道成員需要實例化或更新鏈碼。因此，已在其對等節點上安裝鏈碼的頻道的任何成員都可以將鏈碼實例化，並指定背書原則。不過，如果您想要使用對等節點在頻道上將鏈碼實例化，則可以使用 SDK，並遵循指示[將鏈碼實例化](../v10_application.html#instantiate-cc-sdk)。

## 使用 CLI 來操作對等節點
{: #peer-cli-operate}

您也可以使用 Fabric 對等節點二進位檔，從指令行操作對等節點。

您的對等節點會隨內部對等節點管理者的 signCert 一起部署，因而容許該身分操作對等節點。下列指示將使用在您[部署對等節點](CA_operate.html#register-admin)時產生的對等節點管理者 MSP 資料夾，將對等節點加入頻道，並在對等節點上安裝鏈碼，然後在頻道上將鏈碼實例化。

### 下載 Fabric 對等節點用戶端
{: #peer-client}

若要從遠端用戶端與對等節點互動，您需要下載 [Fabric 對等節點用戶端 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "Fabric 對等節點指令")。

取得對等節點用戶端的最簡單方法是從 Hyperledger Project 下載 Fabric 工具二進位檔。建立一個您想要使用指令行下載二進位檔的目錄，然後發出下列指令來提取它們。您必須先安裝 [Curl ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl")。

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

該指令將在 `bin/` 目錄中安裝二進位檔。設定工具的路徑，亦即下載上述 Fabric 工具之處：
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

比方說，如果您在起始目錄中安裝了二進位檔，您要將 `PATH` 設為：

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

下載二進位檔會建立一個 config 資料夾，其中包含 core.yaml、order.yaml 及 configtx.yaml 檔案。將 Fabric 配置路徑設為下列配置目錄：

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

例如：
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}

### 在本端系統上管理憑證
{: #manage-certs}

切換產生對等節點管理者 MSP 資料夾的目錄。如果您遵循本文件的範例步驟，則可以在類似下面的目錄中找到 MSP 資料夾：

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

在可以操作對等節點之前，您需要先在本端機器上執行一些憑證管理。您還需要確保可以從對等節點存取 TLS 憑證。如需所要使用之憑證的相關資訊，請參閱[在 {{site.data.keyword.cloud_notm}} Private 上操作憑證管理中心](CA_operate.html)中的[成員資格服務提供者](CA_operate.html#msp)。

1. 將您的對等節點管理者的 signCert 移至名稱為 `admincerts` 的新資料夾中：

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
    mkdir admincerts
    cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. 務必[下載您的對等節點 TLS 憑證](#peer-tls)，然後才可從指令行參照它。如果您遵循了本文件中的指令範例，則可以在 `$HOME/fabric-ca-client/peer-tls/peertls.pem` 檔案中找到這個 TLS 憑證。

3. 您還需要參照排序節點的 TLS 憑證。如果您是排序節點的管理者，請遵循[下載排序節點 TLS 憑證](orderer_operate.html#orderer-tls)的指示。如果排序節點是由另一個組織所控管，他們需要在頻內及頻外作業中為您提供 TLS 憑證。請將此憑證儲存至您可在未來指令中參照它的位置。

您可以執行 tree 指令來驗證您已完成這些步驟。請導覽至儲存憑證的目錄。tree 指令應該會產生與下列結構類似的結果：
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### 設定 CLI 環境變數
{: #environment-variables}

將我們的所有憑證移至必要位置之後，我們需要設定一些由我們的對等節點 CLI 指令使用的環境變數。然後，我們就可以使用 Fabric 對等節點用戶端來操作我們的對等節點。

1. 請確定您已[在 CLI 中設定環境變數](#environment-variables)

2. 將 Fabric config 路徑設為您[下載 Fabric 工具二進位檔](#peer-client)時建立的這個 config 目錄：

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    設定此變數可讓您在任何目錄中使用對等節點用戶端來執行指令。

3. 您需要排序節點的端點資訊。如果您是排序節點的管理者，可以使用這些指示來[擷取排序節點端點資訊](orderer_operate.html#orderer-endpoint)。如果排序節點是由另一個組織所控管，他們需要為您提供頻內及頻外作業的排序節點 URL。

4. [擷取對等節點端點資訊](#peer-endpoint)。我們將使用此 URL 來設定 `PEERADDR` 環境變數。您需要拿掉開頭的 `http://`。

5. 執行下列指令以設定環境變數：

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  將欄位取代為您自己的資訊。
    - 將 `<CHANNEL_NAME>` 取代為對等節點加入的頻道名稱。
    - 將 `<CC_NAME>` 取代為用來參照鏈碼的任何名稱。
    - 將 `<PEERADDR>` 取代為前一個步驟的對等節點端點主機名稱及埠。
    - 將 `<ORDERER_URL>` 取代為聯盟排序節點的主機名稱及埠。
    - 將 `<PATH_TO_ADMIN_MSP>` 取代為對等節點管理者 MSP 資料夾的路徑。
    - 將 `<MSPID_OF_PEER_BEING_USED>` 取代為您用來提取初始區塊、加入頻道或安裝鏈碼之對等節點的組織 MSPID。此值是在 Helm 圖表部署期間提供的。

  例如：

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  您也可以啟用 TLS，您可以透過發出下列指令來執行：

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### 使用 CLI 將對等節點加入頻道
{: #icp-cli-join-peer-to-channel}

您的組織必須是頻道的成員，您才能將對等節點加入至該頻道。如果您不是頻道的成員，則可以遵循[建立新頻道](#create-channel)的指示。

1. 請確定您已[在 CLI 中設定環境變數](#environment-variables)。

2. 提取頻道的初始區塊，以在對等節點上建置頻道分類帳。將 `CORE_PEER_TLS_ROOTCERT_FILE` 設為排序節點 TLS 憑證的路徑，並執行下列指令：

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  如果順利完成，您會看到與下面類似的結果：
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **附註：**當您執行任何這些 CLI 指令時，您可能會看到下列警告，您可以放心地忽略它。

  ```
[msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

{:codeblock}

  執行下列指令，以驗證初始區塊已順利新增至對等節點容器：

  ```
  ls *.block
  ```
  {:codeblock}

  看到類似下列範例的內容時，您知道已順利新增初始區塊：

  ```
  defaultchannel_0.block
  ```

3. 當您提取頻道的初始區塊之後，即可將對等節點加入至該頻道。使用下列指令，將 `CORE_PEER_TLS_ROOTCERT_FILE` 設為對等節點 SSL 憑證的路徑並加入頻道：

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  當此工作順利運作時，您應該會看到類似下列範例的資訊：

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### 使用 CLI 在對等節點安裝鏈碼
{: #icp-toolcontainer-install-cc}

我們現在準備好要在對等節點上安裝鏈碼並進行實例化。根據這些指示，我們將從 `fabric-samples` 儲存庫安裝 `fabcar` 鏈碼。請確定您之前已[配置您的 GOPATH![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "設定您的 GOPATH")，然後再利用下列指令，從 github 下載 `fabric-samples` 鏈碼：

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

執行下列對等節點 CLI 指令，將 `fabcar` 鏈碼安裝至對等節點。將 `CORE_PEER_TLS_ROOTCERT_FILE` 設為對等節點 TLS 憑證的路徑。

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  當此指令順利完成時，您應該會看到下列類似內容：

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

<!--
**Note:** If this chaincode has already been installed and instantiated on another peer running in IBP Starter or Enterprise Plan, there are additional steps that must be performed before installing the chaincode on the peer. For more instructions about how to avoid errors that are associated with how this chaincode is installed, see the [troubleshooting topic](#icp-cc-install-error).
-->

### 使用 CLI 在頻道上將鏈碼實例化
{: #icp-toolcontainer-instantiate-cc}

因為只有一個對等節點必須在頻道上將鏈碼實例化，所以您不必使用您的對等節點將鏈碼實例化。{{site.data.keyword.blockchainfull_notm}} Platform 上管理的對等節點可以這樣做。不過，如果您想要對等節點在頻道上將鏈碼實例化，您可以執行下列指令來達成此目的。將 `CORE_PEER_TLS_ROOTCERT_FILE` 的值設為對等節點 TLS 憑證的路徑。將 `--cafile` 的值設為排序節點 TLS 憑證。

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

當此指令順利完成時，您應該會看到下列類似內容：

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

將鏈碼實例化之後，您可以使用 chaincode query 及 invoke 指令，在頻道分類帳上讀取及寫入資料。如需相關資訊，請參閱 Hyperledger Fabric 文件中的[對等節點鏈碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) 指令。您需要使用 Proxy IP 及外部排序節點埠，將排序節點端點傳遞至您的 invoke 指令。您只需要將對等節點端點傳遞給 query 指令。

## 檢視 {{site.data.keyword.cloud_notm}} Private 中的對等節點日誌
{: #peer-log-icp}

您可以從 ICP 主控台存取對等節點日誌，並在 Kibana 介面中檢查日誌。

1. 在 ICP 主控台中，按一下左上角的**功能表**圖示。
2. 從功能表清單中，按一下**工作負載** > **Helm 版本**。
3. 在「Helm 版本」表格中，按一下您的 Helm 版本名稱。
4. 向下捲動至 **Pod** 區段，然後按一下表格列尾端的**檢視日誌**鏈結。對等節點日誌會在 Kibana 介面中開啟。

## 更新鏈碼

一段時間後，您可能需要修改正在對等節點上執行的鏈碼。因為鏈碼已安裝在對等節點上，並在頻道上將其實例化，所以您需要在頻道的所有對等節點上更新鏈碼。

完成下列步驟來更新鏈碼：

1. 要更新每一個對等節點上的鏈碼，只需使用一個用戶端應用程式或 CLI 指令，重新執行您用於對等節點上安裝鏈碼的處理程序。請務必指定原先使用的相同鏈碼名稱。不過，此次會遞增鏈碼 `Version`。所有對等節點都需要使用相同的鏈碼名稱和版本。

2. 在頻道的所有對等節點上安裝新的鏈碼之後，請使用 [peer chaincode upgrade ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 指令，來更新頻道以使用新的鏈碼。

## 重新啟動在 ICP 中執行的對等節點
{: #peer-restart}

每當將憑證新增至對等節點時，需要重新啟動對等節點，它才能辨識新的憑證。

您可以使用 **kubectl** 指令來重新啟動在 ICP 中執行的對等節點。該指令需要對等節點 **Pod** 及**容器**的名稱作為指令參數。如需使用 kubectl 指令的相關資訊，請參閱[配置 kibectl CLI](#peer-kubectl-configure)。

1. 在 ICP 主控台中，尋找 **Pod** 名稱及**容器**名稱，以進行對等節點部署。

  1. 按一下主控台左上角的功能表圖示，然後按一下**工作負載 > 部署**。
  2. 在表格中找出並按一下您的對等節點版本，以將它開啟。
  3. 向下捲動以檢視關聯的 **Pod** 名稱。請記下 **Pod** 名稱。
  4. 按一下 Pod，以將它開啟。
  5. 按一下**容器**標籤。請記下您的對等節點**容器**名稱。

2. 從指令行中執行下列指令，來重新啟動對等節點，並將 `<PEER_POD_NAME>` 及 `<PEER_CONTAINER_NAME>` 取代為上述的值。

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. 您可以執行 `kubectl get pods` 指令，並檢查 Pod 的 **RESTART** 計數的輸出，以驗證對等節點已重新啟動。

## 建立頻道
{: #create-channel}

如果您的組織還不是聯盟或頻道的成員，您可以利用下列步驟來建立頻道。您也可以使用這些步驟來更新現有的頻道。這些指示將引導您[準備組織定義](#organization-definition)。透過新增至排序節點系統頻道，此定義將用來讓您成為[該聯盟的成員](orderer_operate.html#add-organizations-to-consortium)。之後，您就可以透過[建立頻道交易](#peer-icp-channeltx)來形成新頻道

如果您已加入該聯盟，則只需完成準備頻道交易的步驟。聯盟的另一個成員也可以與成員身分的您的組織形成一個新頻道。當您需要對聯盟傳送更新項目時（例如定義新的錨點對等節點、新增管理憑證或更新加密資料），您可以更新組織定義。

### 下載 configtxgen 工具
{: #peer-icp-configtxgen}

如果您的組織需要加入聯盟或頻道，您需要下載 [configtxgen ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen") 工具。

取得 configtxgen 的最簡單方法是從 Hyperledger Project 下載所有 Fabric 工具二進位檔。導覽至您要使用指令行下載二進位檔的目錄，然後發出下列指令來提取它們。您必須先安裝 [Curl ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl")。

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

指令會在 `bin/` 目錄中安裝 configtxgen。將 `PATH` 路徑設為您下載 Fabric 工具二進位檔的目錄：

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

比方說，如果您在起始目錄中安裝了二進位檔，您要將 `PATH` 設為：

```
export PATH=$PATH:$HOME/bin
```

## 準備組織定義
{: #organization-definition}

部署元件之後，您的組織就可以準備組織定義檔，以加入聯盟。此定義包含您要參與聯盟控管所需要的一切資訊，例如，透過建立或加入新頻道、將對等節點新增至頻道或將鏈碼實例化。此定義包含您的組織名稱、MSPID 及 MSP 憑證。所有組織都需要完成此步驟。

### 準備加密資料

在準備組織定義之前，您需要登錄及登記[對等節點的管理者](CA_operate.html#register-admin)。導覽至您建立對等管理管理者 MSP 資料夾的目錄。範例步驟在 `$HOME/fabric-ca-client/peer-admin/msp` 中建立此資料夾。您需要在此資料夾內採取一些其他步驟，`configtxgen` 工具才能使用 MSP。

1. 從您的 CA 複製 TLS 憑證，並放在一個名稱為 `tlscacerts` 的新資料夾中。

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  然後，您需要將憑證複製到您所建立的 `tlscacerts` 目錄：

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  您的指令看起來會像這樣：

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. 將 signCert 從 `signcerts` 資料夾移至一個名稱為 `admincerts` 的新資料夾。

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

`configtxgen` 工具會透過取用 configtx.yaml 檔案來建立定義。您可以在下面找到此檔案的範例版本：

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

此檔案包含在聯盟內定義您組織的資訊。在[您下載的 Fabric 對等節點用戶端](peer_operate_icp.html#peer-client)的 `/config` 資料夾中，也提供了此檔案的較複雜版本。您可以選擇編輯該檔案，或將其取代為上述的範例。請注意這個 `/config` 資料夾的位置，它是用來設定下面的 `FABRIC_CFG_PATH` 值。編輯此檔案的 `Organizations` 區段，並設定下列值：

- `Name:` 可以是您要對組織使用的任何名稱。

- 您的 MSPID 的 `ID:` 值，它會成為組織的唯一 ID。這個 Organization MSP 值是您在部署對等節點 helm 圖表時所指定。也可以透過執行下列指令，在對等節點容器內看到它，請將 `<peer pod name>` 取代為對等節點 的 Pod 值：

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  其輸出可能類似下列內容：
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  因此，MSP `ID` 是 org1。

- `MSPDir:` 是管理者 MSP 的完整路徑，例如：`/Users/chandra/fabric-ca-client/peer-admin/msp`

- `AnchorPeers:` 欄位是您的組織透過聊天選取作為組織間通訊之主導對等節點的對等節點。這用於諸如專用資料或服務探索之類的特性；不過，對等節點 Helm 圖表目前並不支援專用資料和服務探索，但此欄位仍然是必要的。ICP 對等節點「Helm 版本」頁面的這個「附註」區段下有提供 `Host` 和 `Port` 值：`#1. Get the application URL by running these commands`。其輸出類似：

```
http://9.30.250.70:30481
```

其中，`Host:` = `9.30.250.70`，`Port:` = `30481`

然後，使用下列指令來列印組織定義：

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

您的呼叫可能與下列指令範例類似：

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

如果此指令順利完成，`configtxgen` 會以 JSON 格式列印組織定義。您需要將此檔案傳送至頻內及頻外作業的排序節點組織，以加入聯盟。然後，排序節點組織可以透過此方式[形成聯盟或新增至現有的聯盟](orderer_operate.html#consortium)：將此定義新增至系統頻道，容許您建立新頻道以及由其他聯盟成員新增至頻道。

## 建立頻道交易
{: #peer-icp-channeltx}

在建立新頻道之前，您的組織應該先準備好[組織定義](#organization-definition)，並成為聯盟的成員。如果您需要[形成聯盟或新增至聯盟](orderer_operate.html#consortium)，請遵循下列指示。如果其組織已新增至系統頻道，則該聯盟的成員也可以輕易新增至新頻道。不是系統頻道成員的組織只能手動加入頻道，方法是使用[頻道更新要求 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html)，將其組織定義新增至頻道。您也可以使用這些步驟來更新現有的頻道。

### 形成新頻道
{: #peer-icp-create-channel}

若要組成新頻道，組織需要使用 [configtxgen 工具 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen")，建立一個頻道建立交易提案。此工具會取用一個定義新頻道成員的 `configtx.yaml` 檔案。下面提供範例 `configtx.yaml` 檔案。在[您下載的 Fabric 對等節點用戶端](peer_operate_icp.html#peer-client)的 `/config` 資料夾中，也提供了此檔案的較複雜版本。您可以選擇編輯該檔案，或將它取代為此範例。請注意這個 `/config` 資料夾的位置，它是用來設定下面的 `FABRIC_CFG_PATH` 值。
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

與建立新頻道有關的三個區段是 **Organizations**、**Capabilities** 及 **Profiles**：

- **Organizations：**本區段定義聯盟的所有成員。每一個組織都有一個錨點，例如 `&Org1`。在此錨點下，您可以找到組織的名稱、MSPID、其 MSP 資料夾的目錄及其組織的錨點對等節點（以進行對等節點互聊）。您可以使用下列步驟，來填寫每個聯盟成員的組織設定檔：
  1. 使用其 MSPID 來指定組織的 `Name` 和 `ID`。建立頻道的組織需要知道在部署對等節點 Helm 圖表時所指定的 MSPID。
  2. 在 `MSPDir` 中，指定您用來建立[組織定義](#organization-definition)的 MSP 資料夾的完整路徑。請注意，在頻道建立交易中不使用您的任何加密資料。*configtxgen* 工具將忽略 MSP 的實際內容。不過，它預期 MSP 資料夾會在該位置，並具有正確的資料夾子結構。
  3. `AnchorPeers` 參數是用來識別每個組織用於組織間聊天通訊的主導對等節點。請指定將作為此組織之[錨點對等節點 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html) 的對等節點主機名稱及埠。此值對於使用諸如專用資料或服務探索等特性來說很重要；不過，對等節點 Helm 圖表目前並不支援專用資料及服務探索。

- **Capabilities：**本區段必須出現在 `configtx.yaml` 中，但不需要變更。

- **Profiles：**區段。本區段包含建立新頻道所需的資訊。設定檔包含下列資訊：
  1. 新頻道的名稱。
  2. 定義在系統頻道內且將用來建立頻道的聯盟名稱。
  3. 將新增至頻道的組織清單。所列出的組織名稱將使用 `Organizations` 區段列出的錨點對等節點來尋找所列出組織的 MSPID 及相關聯的錨點對等節點。

  *configtxgen* 工具使用這個 `Profiles` 區段來建立此頻道建立提案。

更新配置檔之後，使用下列指令來產生頻道更新交易提案：

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

實際的指令可能與下列範例類似：
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


如果順利完成，您將看到與下列輸出類似的結果。此指令會將頻道更新交易提案寫在 `FABRIC_CFG_PATH` 目錄中。

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

如果您的背書原則不需要聯盟其他成員的其他簽章，您可以將提案直接提交給排序節點來形成新頻道。在與您產生頻道更新交易的相同位置執行下列指令，以建立頻道。

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

其中 `<ORG_MSPID>` 是組織的 MSPID，`<PATH_TO_ADMIN_MSP>` 是對等節點管理者 MSP 資料夾的路徑。在此指令內，`<orderer_url>` 是將建立頻道的排序節點的 URL，`<orderer_tls_cert>` 是排序節點 TLS 憑證的路徑。實際的指令可能類似下列範例：
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## 為提案背書及提交要求

當有足夠的組織簽署新頻道建立提案而符合系統頻道背書原則時，您可以將頻道更新交易提交給排序節點，以形成新頻道

您可以使用 Node Fabric SDK API，在單一交易中完成簽署及提交。如需相關資訊，請造訪 Node SDK 文件中的 [如何建立 Hyperledger Fabric 頻道 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) 指導教學。

## 檢視對等節點日誌
{: #peer-icp-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](#peer-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。

- 使用 `kubectl logs` 指令，以檢視 Pod 內的容器日誌。如果您不確定 Pod 名稱，則請執行下列指令來檢視 Pod 清單。

  ```
  kubectl get pods
  ```
  {:codeblock}

  然後，執行下列指令，以擷取位於 Pod 內之對等節點容器的日誌，方法是將 `<pod_name>` 取代為上述指令輸出中的 Pod 名稱：

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  如需 `kubectl logs` 指令的相關資訊，請參閱[Kubernetes 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- 或者，您也可以使用 [ICP 叢集管理主控台](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)來存取日誌，這會在 Kibana 中開啟日誌。

  **附註：**當您在 Kibana 中檢視日誌時，可能會收到`找不到結果`回應。如果 ICP 使用您的工作者節點 IP 位址作為其主機名稱，則會發生此狀況。若要解決此問題，請移除畫面頂端開頭為 `node.hostname.keyword` 的過濾器，這樣日誌就會變成可見。


## 疑難排解
{: #peer-icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```
{:codeblock}
2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the selected version is compatible with ICP 2.1.0.3. `v2.7.2 ` is recommended.

3.  After the helm installation completes, [login](#peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```
 {:codeblock}
 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```
 {:codeblock}

5. Check helm version:

  ```
  helm version --tls
  ```
  {:codeblock}

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **問題：**登入對等節點容器時發生錯誤
{: #icp-bash-error}

當您使用 kubectl 工具，藉由執行下列指令來執行至對等節點時，會發生此問題：

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}

失敗並產生與下列範例類似的錯誤訊息：

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**解決方案：**

在相同的 Docker 容器中開啟兩個 Docker Bash 階段作業時，若在大序排列法系統（例如 IBM System Z）上，即會發生此錯誤。第二個 Bash 階段作業可能無法連接至執行中的容器。若要解決此問題，請[重新啟動對等節點容器](#peer-restart)，然後在對等節點重新啟動之後，重試 `kubbectl exec` 指令。

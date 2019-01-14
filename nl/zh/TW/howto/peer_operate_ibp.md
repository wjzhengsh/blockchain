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

# 使用入門範本方案或企業方案在 {{site.data.keyword.cloud_notm}} Private 上操作對等節點
{: #peer-operate_icp}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

在 {{site.data.keyword.cloud_notm}} Private (ICP) 對等節點上設定 {{site.data.keyword.blockchainfull}} Platform 之後，您需要先完成數個作業步驟，對等節點才能將交易提交至「入門範本方案」或「企業方案」網路。這些步驟包括將組織新增至頻道、將對等節點加入頻道、在對等節點上安裝鏈碼、在頻道上實例化鏈碼，以及將應用程式連接至對等節點。
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

雖然指示假設熟悉 SDK 的作業，但 Fabric SDK 是建議的路徑。如果您偏好使用指令行，則可以使用 Fabric 對等節點用戶端。

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

**附註**：{{site.data.keyword.blockchainfull_notm}} Platform 對等節點無權存取完整功能，也不支援在 {{site.data.keyword.blockchainfull_notm}} Platform 上管理的對等節點。因此，您無法使用「網路監視器」在 ICP 上操作對等節點。在開始執行對等節點之前，請務必先檢閱[注意事項及限制](../ibp-for-icp-about.html#ibp-icp-considerations)。

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

**附註：**如果您部署受防火牆保護的對等節點，則需要開啟透通功能來啟用平台上的網路，以完成與對等節點的 TlS 信號交換。您只需要針對連結至對等節點埠 7051 的 Node 埠啟用透通功能。如需相關資訊，請參閱[尋找對等節點端點資訊](#peer-endpoint)。

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
{: #remote-peer-node-sdk}

您的對等節點會隨內部對等節點管理者的 signCert 一起部署。這可讓您使用對等節點管理者的憑證及 MSP 資料夾來操作該對等節點。

尋找您[登記對等節點管理者](peer_deploy_ibp.html#enroll-admin)時所建立的憑證。如果您已使用指令範例，則可以在 `$HOME/fabric-ca-client/peer-admin` 找到對等節點管理者 MSP 資料夾。
  - 您可以利用 MSP 資料夾中的 signCert（公開金鑰）和私密金鑰，透過 SDK 建置對等節點管理使用者的環境定義。您可以在下列位置找到那些金鑰：
    - 可在 **signcerts** 資料夾中找到 signCert：`$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - 可在 **keystore** 資料夾中找到私密金鑰：`$HOME/fabric-ca-client/peer-admin/msp/keystore`
您可以在[開發應用程式指導教學這一節](../v10_application.html#enroll-panel)中找到示範如何構成使用者環境定義，以及只使用公開和私密金鑰來操作 SDK 的範例。

您也可以使用 SDK 來產生對等節點管理者 signCert 及私密金鑰，方法是使用「入門範本方案」或「企業方案」上 CA 的端點資訊，以及您的[對等節點管理使用者名稱和密碼](peer_deploy_ibp.html#register-admin)。

### 將對等節點管理者 signCert 上傳至入門範本方案或企業方案
{: #remote-peer-upload-sdk}

您需要將對等節點管理者 signCert 上傳至 {{site.data.keyword.blockchainfull_notm}} Platform 上的網路，您的應用程式才有權操作網路。

- 您可以在 SDK 產生加密資料的目錄下以對等節點管理者命名的檔案中找到 signCert。複製 `certificate` 欄位之後引號內的憑證，其開頭為 `-----BEGIN CERTIFICATE-----`，結尾為 `-----END CERTIFICATE-----`。您可以使用 CLI，藉由發出 `echo -e "<CERT>" > admin.pem` 指令，將憑證轉換成 PEM 格式。然後，您可以使用「網路監視器」將憑證的內容貼至區塊鏈網路。登入 {{site.data.keyword.blockchainfull_notm}} Platform 上的網路。在「網路監視器」的「成員」畫面上，按一下**憑證** > **新增憑證**。為您的憑證指定任何名稱，並將內容貼至**憑證**欄位。當系統詢問您是否要重新啟動對等節點時，請按一下**重新啟動**。必須在組織加入頻道之前執行此動作。

### 將對等節點的 TLS 憑證傳遞至 SDK
{: #icp-peer-download-tlscert}

您需要參照您對等節點的 TLS 憑證，才能鑑別與 SDK 的通訊。請找到您[從對等節點容器下載](#peer-tls)的 TLS 憑證，然後將其儲存在您的應用程式可以參照它的位置。如果您已使用指令範例，則可以在 `$HOME/fabric-ca-client/peer-tls/peertls.pem` 找到對等節點 TLS 憑證。然後，您可以使用簡單的讀取檔案指令，將 TLS 憑證匯入至應用程式。

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### 將對等節點端點資訊提供給 SDK
{: #peer-SDK-endpoints}

請尋找[對等節點的端點資訊](#peer-endpoint)，並透過宣告新的對等節點變數或更新您的「連線設定檔」，將它提供給 SDK。下列範例將對等節點定義為光纖網路上的端點，並將您匯入的 TLS 憑證傳遞給它。

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**附註：**因為對等節點位於 {{site.data.keyword.cloud_notm}} 之外，所以 {{site.data.keyword.blockchainfull_notm}} Platform 網路上的其他組織無法在其「連線設定檔」中找到您對等節點的端點資訊。如果其他組織需要將要背書的交易傳送至您的對等節點，則您需要在頻內及頻外作業中提供對等節點 URL。

### 使用 SDK 來加入頻道
{: #peer-join-channel-sdk}

您的組織必須是頻道的成員，您才能將對等節點加入至該頻道。

  - 您可以為對等節點啟動新的頻道。如果您是頻道起始者，則可以在[建立頻道](create_channel.html#creating-a-channel)期間自動包含您的組織。

  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。

    當您的組織新增至頻道之後，您需要將您對等節點的 signCert 新增至該頻道，這樣其他成員才可以在交易期間驗證您的數位簽章。對等節點會在安裝期間上傳其 signCert，這表示您只需要使憑證與該頻道同步化。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。您可能需要等幾分鐘，才能讓頻道同步在您發出 `join channel` 指令之前完成。

    **附註：**只有當您要新增至 ICP 且連接到「入門範本方案」或「企業方案」網路的對等節點，與已透過「網路監視器」新增的對等節點屬於相同組織的一部分，您才能夠在「網路監視器」的「頻道」畫面中檢視要新增至該頻道的新區塊、要實例化的鏈碼以及其他頻道更新。這是因為「網路監視器」只會從對等節點中收集「頻道」畫面的相關資訊，而看不到 {{site.data.keyword.cloud_notm}} 以外的對等節點。如果您的對等節點都沒有使用「專用資料」特性，則組織中的一個對等節點與另一個對等節點，其在「網路監視器」中的資訊是相同的。

當組織變成頻道的成員之後，請遵循指示使用 SDK [將對等節點加入頻道](../v10_application.html#join-channel-sdk)。您需要提供排序服務的 URL 及頻道名稱。

### 使用 SDK 在對等節點上安裝鏈碼
{: #peer-install-cc-sdk}

請遵循下列指示使用 SDK，在您的對等節點上[安裝鏈碼](../v10_application.html#install-cc-sdk)。

### 使用 SDK 在頻道上實例化鏈碼
{: #peer-instantiate-cc-sdk}

只有一個頻道成員需要實例化或更新鏈碼。因此，在 {{site.data.keyword.blockchainfull_notm}} Platform 上具有對等節點之頻道的任何網路成員，都可以使用「網路監視器」來實例化鏈碼並指定背書原則。不過，如果您想要使用對等節點在頻道上實例化鏈碼，則可以使用 SDK，並遵循[實例化鏈碼](../v10_application.html#instantiate-cc-sdk)的指示。

## 使用 CLI 來操作對等節點
{: #peer-cli-operate}

您也可以使用 Fabric `peer` 用戶端，從指令行操作對等節點。

您的對等節點會隨內部對等節點管理者的 signCert 一起部署，因而容許該身分操作對等節點。下列指示將使用在您[部署對等節點](peer_deploy_ibp.html#register-admin)時產生的對等節點管理者 MSP 資料夾，將對等節點加入頻道，並在對等節點上安裝鏈碼，然後在頻道上將鏈碼實例化。

### 下載 Fabric 對等節點用戶端
{: #peer-client}

取得對等節點用戶端的最簡單方法是從 Hyperledger 下載所有 Fabric 工具二進位檔。導覽至您要使用指令行下載二進位檔的目錄，然後發出下列指令來提取它們。如果您尚未安裝 [Curl ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl")，則您需要先執行該作業。

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

下載二進位檔會建立 `config` 資料夾，其中包含 core.yaml、orderer.yaml 及 configtx.yaml 檔案。將 Fabric config 路徑設為這個 `config` 目錄：

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

在可以操作對等節點之前，您需要先在本端機器上執行一些憑證管理。例如，您需要將對等節點管理者 signCert 上傳至「入門範本方案」或「企業方案」，並確保您可以從對等節點和您在 {{site.data.keyword.cloud_notm}} 上的網路存取 TLS 憑證。如需要使用之憑證及要執行之作業的相關資訊，請參閱[在 {{site.data.keyword.blockchainfull_notm}} Platform 上管理憑證](../certificates.html)。

1. 將您的對等節點管理者的 signCert 移至名稱為 `admincerts` 的新資料夾中：

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. 將此 signCert 上傳至 {{site.data.keyword.cloud_notm}} 上的網路，使遠端 CLI 或應用程式可以執行頻道作業，例如提取頻道初始區塊或將鏈碼實例化。
    1. 在本端機器上，開啟 `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` 檔案，並將它複製到剪貼簿。
    2. 進入 {{site.data.keyword.blockchainfull_notm}} Platform 上您網路的「網路監視器」。
    3. 按一下左導覽器中的**成員**，然後按一下**憑證**標籤。
    4. 按一下**新增憑證**按鈕。
    5. 在**新增憑證**視窗中，為您的憑證提供一個名稱（例如 "peer-admin"），貼上您剛才複製到剪貼簿的憑證，然後按一下**提交**。
    6. 系統會詢問您是否要重新啟動對等節點。按一下**重新啟動**。
    7. 按一下左導覽器中的**頻道**，找出對等節點將加入的頻道。
    8. 按一下**動作**標頭下的三個點，然後按一下**同步憑證**。在**同步憑證**視窗中，按一下**提交**。

    **附註**：請務必在對等節點加入頻道或在頻道上實例化鏈碼之前，先將頻道憑證同步化。您可能需要等幾分鐘，才能讓頻道同步在您發出 join channel 或 instantiate chaincode 指令之前完成。

3. 務必[下載您的對等節點 TLS 憑證](#peer-tls)，然後才可從指令行參照它。如果您遵循了指令範例，則可以在 `$HOME/fabric-ca-client/peer-tls/peertls.pem` 檔案中找到這個 TLS 憑證。

4. 當您[證記對等節點管理者](peer_deploy_ibp.html#enroll-admin)時，您也需要參照用來與「入門範本方案」或「企業方案」CA 進行通訊的 TLS 憑證。如果您遵循了本文件中的指令範例，則可以在 `$HOME/fabric-ca-client/tls-ibp/tls.pem` 檔案中找到 TLS 憑證。

您可以執行 tree 指令來驗證您已完成這些步驟。請導覽至儲存憑證的目錄。tree 指令應該會產生與下列結構類似的結果：
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### 設定 CLI 環境變數
{: #environment-variables}

將我們的所有憑證移至必要位置之後，您必須設定一些環境變數，供您的指令使用。然後，您就可以使用 Fabric 對等節點用戶端來操作您的對等節點。

1. 從「網路監視器」的**概觀**畫面所提供的**連線設定檔**檔案中，擷取「入門範本方案」或「企業方案」中的配置資訊。按一下**連線設定檔**，然後按一下**下載**。

  - 搜尋**排序節點**來找出排序節點 URL，其位於 `orderers > url` 下。請記下以網路名稱開頭的 URL。此 URL 類似下列範例：

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - 搜尋**組織**來找出您組織的名稱。這應該與您用來登錄對等節點的組織相同。您可以同時找到組織的名稱及其關聯的 `mspid`。請記下 `mspid` 的值。

2. [尋找對等節點的端點資訊](#peer-endpoint)。您需要使用對等節點端點來設定 `PEERADDR` 環境變數。請確定您拿掉了開頭的 `http://`。

3. 執行下列指令以設定環境變數。

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  將欄位取代為您自己的資訊。
    - 將 `<full_path_to_config_folder>` 取代為您[下載對等節點用戶端](#peer-client)時建立的 config 資料夾
    - 將 `<CHANNEL_NAME>` 取代為對等節點加入的頻道名稱。
    - 將 `<CC_NAME>` 取代為用來參照鏈碼的任何名稱。
    - 將 `<PEERADDR>` 取代為前一個步驟的對等節點端點（針對您目前使用的對等節點）。
    - 將 `<ORDERER_URL>` 取代為「連線設定檔」中排序節點的主機名稱及埠。
    - 將 `<PATH_TO_ADMIN_MSP>` 取代為對等節點管理者 MSP 資料夾的路徑。
    - 將 `<PATH_TO_PEER_TLS_CERT>` 取代為您下載之對等節點 TLS 憑證的路徑。
    - 將 `<MSPID_OF_PEER_BEING_USED>` 取代為您用來提取初始區塊、加入頻道或安裝鏈碼之對等節點的組織 MSPID。

  例如：

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
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

在您可以執行 CLI 指令將對等節點加入頻道之前，您的組織需要以下列其中一種方式新增至網路中的頻道。

  - 您可以為對等節點啟動新的頻道。當您作為頻道起始者時，可以在[建立頻道](create_channel.html#creating-a-channel)期間自動併入您的組織。
  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。

    當您的組織新增至頻道之後，您需要將您對等節點的 signCert 新增至該頻道，這樣其他成員才可以在交易期間驗證您的數位簽章。對等節點會在安裝期間上傳其 signCert，因此您只需要將憑證同步化至該頻道。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。

    **附註：**如果連接至「入門範本方案」或「企業方案」網路的 ICP 對等節點，與使用「網路監視器」新增的另一個對等節點屬於相同組織的一部分，則您只能在「網路監視器」的「頻道」畫面中檢視新增至該頻道的新區塊、實例化的鏈碼以及其他頻道更新。這是因為「網路監視器」只會從對等節點中收集「頻道」畫面的相關資訊，而看不到 {{site.data.keyword.cloud_notm}} 之外的對等節點。如果您的對等節點都沒有使用「專用資料」特性，則組織中的某個對等節點與另一個對等節點，其在「網路監視器」中的資訊是相同的。

1. 確定您已設定 [CLI 中的環境變數](#environment-variables)。

2. 提取頻道的初始區塊，以在對等節點上建置頻道分類帳。請注意，`$HOME/fabric-ca-client/tls-ibp/tls.pem` 代表「入門範本方案」或「企業方案」TLS 憑證的路徑。如果您的路徑不同，請務必設定正確的路徑。

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
  ```
  {:codeblock}

  **附註：**您可能會在執行任何上述 CLI 指令時，看到可以放心忽略的下列警告。

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```

  執行下列指令，以驗證初始區塊已順利新增至對等節點容器：

  ```
  ls *.block
  ```
  {:codeblock}

  看到類似下列範例的內容時，您知道已順利新增初始區塊：

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. 提取頻道的初始區塊之後，您可以使用下列指令，將對等節點加入該頻道：

  ```
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

我們現在準備好要在對等節點上安裝鏈碼並進行實例化。根據這些指示，我們將從 `fabric-samples` 儲存庫安裝 `fabcar` 鏈碼。請確定您之前已[配置您的 GOPATH ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "設定您的 GOPATH")，然後再利用下列指令，從 github 下載 `fabric-samples` 鏈碼：

```
cd $GOPATH/src
git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

執行下列對等節點 CLI 指令，將 `fabcar` 鏈碼安裝至對等節點：

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

當此指令順利完成時，您應該會看到下列類似內容：

```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

**附註：**如果已安裝此鏈碼，並已在「IBP 入門範本」或「企業方案」中執行的另一個對等節點上實例化，則在對等節點上安裝此鏈碼之前必須先執行其他步驟。請參閱這個[疑難排解主題](#peer-ibp-troubleshooting)，以取得如何避免與如何安裝此鏈碼相關之錯誤的其他指示。

### 使用 CLI 在頻道上將鏈碼實例化
{: #icp-toolcontainer-instantiate-cc}

因為只有一個對等節點必須在頻道上將鏈碼實例化，所以您不必使用您的對等節點將鏈碼實例化。{{site.data.keyword.blockchainfull_notm}} Platform 上管理的對等節點可以這樣做。不過，如果您想要對等節點在頻道上將鏈碼實例化，您可以執行下列指令來達成此目的。請確定您已將 `CORE_PEER_TLS_ROOTCERT_FILE` 的值設為「入門範本方案」或「企業方案」上的 CA 的 TLS 憑證路徑。

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

當此指令順利完成時，您應該會看到下列類似內容：

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

將鏈碼實例化之後，您可以使用 chaincode query 及 invoke 指令，在頻道分類帳上讀取及寫入資料。如需相關資訊，請參閱 Hyperledger Fabric 文件中的[對等節點鏈碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) 指令。您需要使用 Proxy IP 及外部排序節點埠，將排序節點端點傳遞至您的 invoke 指令。您只需要將對等節點端點傳遞給 query 指令。

## 更新鏈碼

一段時間後，您可能需要修改正在對等節點上執行的鏈碼。因為鏈碼已安裝在對等節點上，並在頻道上將其實例化，所以您需要在頻道的所有對等節點上更新鏈碼。

完成下列步驟來更新鏈碼：

1. 要更新每一個對等節點上的鏈碼，只需使用一個用戶端應用程式或 CLI 指令，重新執行您用於對等節點上安裝鏈碼的處理程序。請務必指定原先使用的相同鏈碼名稱。不過，此次會遞增鏈碼 `Version`。所有對等節點都需要使用相同的鏈碼名稱和版本。

2. 在頻道的所有對等節點上安裝新的鏈碼之後，請使用「網路監視器」或 [peer chaincode upgrade ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 指令，來更新頻道以使用新的鏈碼。

請參閱這些[指示](install_instantiate_chaincode.html#updating-a-chaincode)的步驟 2，以取得使用「網路監視器」的「安裝程式碼」畫面，來更新頻道上鏈碼的相關資訊。

## 檢視對等節點日誌
{: #peer-ibp-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](#ca-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。

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

  如需 `kubectl logs` 指令的相關資訊，請參閱 [Kubernetes 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- 或者，您也可以使用 [ICP 叢集管理主控台](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)來存取日誌，這會在 Kibana 中開啟日誌。

   **附註：**當您在 Kibana 中檢視日誌時，可能會收到`找不到結果`回應。如果 ICP 使用您的工作者節點 IP 位址作為其主機名稱，則會發生此狀況。若要解決此問題，請移除畫面頂端開頭為 `node.hostname.keyword` 的過濾器，這樣日誌就會變成可見。

## 疑難排解
{: #peer-ibp-troubleshooting}

### **問題：**在對等節點上呼叫指令失敗，並出現 `chaincode fingerprint mismatch` 錯誤
{: #icp-cc-install-error}

在執行於 {{site.data.keyword.cloud_notm}} Private 中的對等節點上執行 `peer chaincode invoke` 要求時，您可能會接收到 `chaincode fingerprint mismatch` 錯誤：

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

如果執行鏈碼的對等節點在下列其中一項有不一致的情況，則可能會導致此錯誤：

- 鏈碼名稱
- 鏈碼版本
- 鏈碼路徑
- 鏈碼二進位檔

如果已使用「網路監視器」使用者介面在「入門範本」或「企業」方案中執行的對等節點上安裝鏈碼並實例化，之後您又將鏈碼安裝在 {{site.data.keyword.cloud_notm}} Private 上執行的對等節點上，則會發生此錯誤。因為在對等節點上產生的鏈碼路徑不同，所以 `invoke` 要求會發生此錯誤。

**解決方案：**如果您想要同時在 {{site.data.keyword.cloud_notm}}（例如，「入門範本方案」或「企業方案」）和 {{site.data.keyword.cloud_notm}} Private 的對等節點上執行鏈碼，請勿使用「網路監視器」使用者介面來安裝此鏈碼。請改為使用 [`peer chaincodepackage `![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) 指令來包裝鏈碼，然後執行 [`peer chaincode install`](#icp-toolcontainer-install-cc) 指令，在所有對等節點上安裝所有套件。

在您嘗試在 {{site.data.keyword.cloud_notm}} Private 對等節點上安裝鏈碼之前，如果已在頻道上安裝鏈碼並實例化，則您需要完成下列步驟以避免該問題：

1. 使用 [`peer chaincode package`![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) 指令來包裝鏈碼。
2. 透過執行 `peer chaincode install` 指令，在 {{site.data.keyword.cloud_notm}} Private 上執行的對等節點上安裝鏈碼套件。
3. 如果您有特定平台專用的二進位檔，您可以執行 [`peer chaincodeupgrade `![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) 指令，來升級在「入門範本」或「企業」方案對等節點上執行的鏈碼（其使用鏈碼套件）。
4. 使用「網路監視器」使用者介面或 CLI，在頻道上將新安裝的鏈碼實例化。

也可以在 Hyperledger Fabric 文件的[`操作員的鏈碼 `![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) 中找到升級鏈碼的處理程序。

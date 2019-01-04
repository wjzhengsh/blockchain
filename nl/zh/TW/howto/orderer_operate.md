---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 在 {{site.data.keyword.cloud_notm}} Private 上操作排序節點
{: #orderer-operate}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

在 ICP 中安裝 {{site.data.keyword.blockchainfull}} Platform 排序節點之後，即會建立 configmap，它包含環境變數的預設值。然後，您就可以變更或新增排序節點的環境變數，以配置其行為。

一般而言，排序節點的管理者必須負責引導及維護排序節點，但這在 SOLO 部署中並非必要，因為其中只有一個排序節點存在。在 SOLO 部署中，排序節點管理者需負責將組織新增至排序節點系統頻道。

## 必要條件

您需要從 ICP 叢集中完成一些必要步驟，才能操作您的排序節點。

**必要條件：**
- [配置 CLI](#orderer-kubectl-configure)
- [擷取排序節點端點資訊](#orderer-endpoint)
- [下載排序節點 TLS 憑證](#orderer-tls)

然後，您可以使用本主題中的指示來操作您的排序節點。請注意，您將透過指令行操作您的排序節點，這需要取得 `peer` CLI 二進位檔。`peer` CLI 二進位檔會連同其他二進位檔一起下載，例如 `configtxlator`。因此，本主題中許多指令的開頭都是 "peer" 一字。實際上它並未使用對等節點 (peer)，而只是二進位檔的名稱。

### 配置 CLI
{: #orderer-kubectl-configure}

您需要使用 **kubectl** 指令行工具，以連接至 ICP 中所執行的排序節點容器。

1. 登入 ICP 叢集使用者介面。導覽至**指令行工具**標籤，然後按一下 **Cloud Private CLI**。您將看到下列可下載的工具。

   * 安裝 IBM Cloud Private CLI 及外掛程式
   * 安裝 Kubectl CLI
   * 安裝 Helm
   * 安裝 Istio CLI

  若要操作排序節點，您需要使用前**三個**工具，而 Helm 在其中為選用項目。按一下每個項目，然後針對您所使用的機型，執行 `curl` 指令。接著，針對每個工具發出 `chmod` 及 `sudo mv` 指令。`chmod` 指令將變更所提及之 CLI 的許可權以使其可執行，而 `sudo mv` 指令將移動該檔案並予以重新命名。

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

  如果順利完成，則此指令應該傳回與下列範例類似的回應，其中 `orderer-6cf85f6687-hcxpl` 是排序節點容器的名稱。

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  您現在可以使用 **kubectl** 工具來取得排序節點端點資訊。

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

### 擷取排序節點端點資訊
{: #orderer-endpoint}

您需要將排序節點端點設為目標，以進行排序節點系統頻道的更新。您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能完成下列步驟：

1. 登入 ICP 主控台，然後按一下左上角的**功能表**圖示。
2. 按一下**工作負載** > **Helm 版本**。
3. 尋找「Helm 版本」的名稱，然後開啟「Helm 版本詳細資料」畫面。
4. 向下捲動至畫面底端的**附註**區段。**附註**區段包含一組指令，可協助您操作排序節點部署。
5. 如果您還沒有這麼做，請遵循指示來配置 [kubeclt CLI](#orderer-kubectl-configure)。您需要使用它來與排序節點容器互動。
6. 在 CLI 中，執行 **1. 執行下列指令，以取得應用程式 URL：**之後附註中的第一個指令。此指令會印出排序節點 URL 及埠，其與下面範例類似。 請儲存此 URL，您需要在未來的指令中使用它，以設定您的 Proxy 位址及外部節點埠。

  ```
  http://9.30.94.174:30159
  ```

在此範例中，Proxy IP 位址是 `9.30.94.174`，而對應於 7050 的外部節點埠為 `30159`。  

**附註：**如果您要部署受防火牆保護的排序節點，則需要開啟透通功能來啟用平台上的網路，以完成這項作業。

### 下載排序節點 TLS 憑證
{: #orderer-tls}

您需要下載排序節點 TLS 憑證，並將其傳遞至指令，才能從遠端用戶端與您的排序節點通訊。您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能完成下列步驟：

1. 登入 ICP 主控台，然後按一下左上角的**功能表**圖示。
2. 按一下**工作負載** > **Helm 版本**。
3. 尋找「Helm 版本」的名稱，然後開啟「Helm 版本詳細資料」畫面。
4. 向下捲動至畫面底端的**附註**區段。**附註**區段包含一組指令，可協助您操作排序節點部署。
5. 如果您還沒有這麼做，請遵循指示來配置 [kubeclt CLI](#ca-kubectl-configure)。您需要使用它來與排序節點容器互動。
6. 在 CLI 中，執行 **3. 取得排序節點 TLS 根憑證檔案**之後附註中的第三個指令。此指令會將您的 TLS 憑證儲存為本端機器上的 `cert.pem` 檔案。

  **附註：**在執行指令之前，您可以移除 `app=orderer`，如下列指令所示：

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. 將產生的憑證移至您可以在未來指令中參照它的位置，並將其重新命名為 `orderertls.pem`。

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### 在本端系統上管理憑證
{: #manage-certs}

切換至產生排序節點管理者 MSP 資料夾的目錄。根據您如何遵循本文件的範例步驟，或您部署的元件數量，您可以在 `$HOME/fabric-ca-client/orderer-admin/msp` 或 `$HOME/fabric-ca-client/peer-admin/msp` 中找到 MSP 資料夾

您必須先管理本端機器上的憑證，然後才能操作排序節點。您還需要確保可以從排序節點存取 TLS 憑證。如需所要使用之憑證的相關資訊，請參閱[在 {{site.data.keyword.cloud_notm}} Private 上操作憑證管理中心](CA_operate.html)中的[成員資格服務提供者](CA_operate.html#msp)。

1. 將您排序節點管理者的 signCert 移至名稱為 `admincerts` 的新資料夾中：

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. 確保您[下載排序節點 TLS 憑證](#orderer-tls)，而且可以從指令行參照它。如果您遵循本文件中的指令範例，則可以在 `$HOME/fabric-ca-client/orderer-tls/orderertls.pem` 檔案中找到此 TLS 憑證。

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
├── orderer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
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

## 將組織新增至排序節點系統頻道
{: #add-organizations-to-consortium}

**附註：**如果您計劃部署對等節點，並將其連接至「入門範本方案」或「企業方案」網路，則可以跳過此步驟。

當您使用 {{site.data.keyword.blockchainfull_notm}} Platform for ICP Helm 圖表部署排序節點時，會自動建立初始區塊，並以排序節點組織作為系統頻道的唯一管理者。這表示排序節點組織管理者是唯一有能力將組織新增至聯盟的管理者。在新組織新增至排序節點系統頻道之後，不得變更此控管原則，即排序節點系統頻道所謂的 `mod_policy`。

將組織新增至排序節點系統頻道，會使它們成為「聯盟」的一部分，而聯盟是依預設可建立頻道的成員清單。身為聯盟的成員，也可以使用系統頻道中列出的憑證及資訊，輕鬆地將其新增至頻道。

排序節點系統頻道的更新是透過「配置更新交易」來達成，其中組織的相關 MSP 資訊會新增至排序節點系統頻道的頻道配置。

將組織新增至排序節點系統頻道，基本上與更新任何頻道配置以新增組織的流程相同。不過，您需要進行一些變更，因為要更新的頻道不是應用程式頻道，且相關的管理者是排序節點管理者而非對等節點組織。

請注意，您不需要先加入系統頻道，就可以將組織新增至頻道。如需相關資訊，請參閱 Hyperledger Fabric 文件中的[將組織新增至頻道指導教學](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html)。

下列清單顯示聯盟各組不同的組織將執行的一般步驟及作業。

1. 要加入聯盟的每個組織必須[準備組織定義](peer_operate_icp.html#organization-definition)。
2. 排序節點組織管理者透過將組織新增至排序節點系統頻道來[組成聯盟](#consortium)。
3. 聯盟的任何組織可以透過準備頻道配置交易來[建立新頻道](peer_operate_icp.html#peer-icp-channeltx)。

## 取得 Fabric 工具
{: #get-fabric-tools}

您需要下載下列 Hyperledger Fabric 工具來更新系統頻道。
- [peer](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html)，可讓您提取初始區塊並更新系統頻道。
- [configtxlator](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html)，可將頻道配置的 protobuf 格式轉換為更容易讀取及更新的 JSON 格式。

1. 決定您要儲存工具的位置，然後執行下列指令：

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. 設定工具的路徑，亦即下載上述 Fabric 工具的位置：

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  比方說，如果您已在起始目錄中安裝二進位檔，則會將 `PATH` 設為：

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. 下載二進位檔會建立一個 config 資料夾，其中包含 core.yaml、order.yaml 及 configtx.yaml 檔案。將 Fabric 配置路徑設為下列配置目錄：

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  例如：
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## 建立組織定義
{: #org-definition}

組織的**定義**包含組織名稱 (MSP ID) 及相關憑證。系統頻道及應用程式頻道將使用此定義，將您的組織併入控制頻道建立、更新及交易背書的原則中。想要加入聯盟的每個組織都必須完成此步驟。若要進一步瞭解，請參閱[準備組織定義](peer_operate_icp.html#organization-definition)。

## 組成聯盟
{: #consortium}

回顧組成聯盟的高階流程：

1. 形成排序節點系統頻道時只會將排序節點組織作為系統頻道的成員。該組織可以進行更新，而不需要其他簽章。
2. 排序節點組織管理者會接收想要加入聯盟之成員的組織定義。排序節點組織會使用組織定義來更新排序節點系統頻道的配置。

### 取得組織定義

排序節點需要接收想要加入聯盟之成員的[組織定義](peer_operate_icp.html#organization-definition)。這需要在頻外作業中完成，且其他成員會傳送包含其 MSP ID 及加密資料的 JSON 檔給您。如需參照下列指令，我們假設您已建立一個名稱為 `org-definitions` 的資料夾，並已將所有相關檔案放置於該目錄中。

### 提取系統頻道的初始區塊

1. 使用您在部署期間所建立的排序節點組織 MSP 名稱、排序節點 MSP 的路徑，以及排序節點的 TLSCA 憑證路徑，來設定下列環境變數。

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  將欄位取代為您自己的資訊。

    - 將 `<CORE_PEER_LOCALMSPID>` 取代為排序節點組織的 MSP ID。透過執行下列指令，並將 `<orderer pod name>` 取代為排序節點的 Pod 值，也可以在排序節點容器中看到它：

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      其輸出可能與下列內容類似：

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      因此，`CORE_PEER_LOCALMSPID` 是 ordererOrg。

    - 將 `<CORE_PEER_MSPCONFIGPATH>` 取代為排序節點組織之管理者 MSP 資料夾的路徑。
    - 將 `<CORE_PEER_TLS_ROOTCERT_FILE>` 取代為 TLS CA 憑證的路徑。
    - 將 `<PROXY_IP>` 取代為[排序節點端點資訊](#orderer-endpoint)中的 Proxy IP 位址
    - 將 `<EXTERNAL_NODE_PORT>` 取代為[排序節點端點資訊](#orderer-endpoint)中的外部節點埠

  您的環境變數可能與下列範例類似：

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  您可以發出下列指令，隨時檢查這些環境變數：

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. 將頻道名稱設為環境變數。排序節點系統頻道的名稱一律為 `test-system-channel-name`。

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. 提取系統頻道的初始區塊。首先，在 `org-definitions` 內建立一個稱為 `configupdate` 的目錄，以儲存在配置更新程序期間產生的配置檔。您可以任意命名此目錄，但下列指令範例將使用 `configupdate`。

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  將 `<orderer_TLS_root_cert_file>` 取代為您在這個[步驟](#orderer-tls)中建立之 `orderertls.pem` 檔案的路徑。例如，您的指令可能與下列範例類似：

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  如果順利完成，此指令將產生下列輸出。

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  您之後也可以在檔案系統中找到 `genesis.pb` 區塊。

### 建立系統頻道更新
{: #system-channel-update}

下載的 [Fabric 工具](#get-fabric-tools) `configtxtlator`  可將頻道配置的 protobuf 格式轉換為 JSON 格式，或將 JSON 格式轉換為 protobuf 格式。

這些步驟遵循有關[將區塊轉換成 JSON 格式]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down)之頻道更新指導教學的一般流程。您需要對指導教學中的指令進行一些變更，以反映您要更新排序節點系統頻道而非應用程式頻道的事實。您可以造訪本指導教學，以取得此處理程序的更多詳細資料。本節僅為您提供指令。

1. 將組織定義 JSON 檔案從您在其中[建立組織](peer_operate_icp.html#organization-definition)的資料夾複製到 `configupdate` 資料夾。在下面的指令範例中，組織定義 JSON 檔案是 `org1definition.json`：

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. 將初始區塊轉換為 JSON 格式。請執行下列指令：

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. 執行下列指令，將組織的加密資料新增至聯盟配置。將 <NEWORGMSP> 取代為[所建立組織](peer_operate_icp.html#organization-definition)的組織 MSP ID。

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  此指令可能與下列範例類似：

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. 針對需要加入聯盟的每個組織重複此指令。請確保您將 `./orgdefinition.json` 檔案變更為新的組織 JSON 檔案。

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. 執行下列指令，將 `modified_config.json` 檔案轉換為 JSON 格式的配置更新。

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. 完成這些指令之後，將封套置於 `config_update`。

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. 執行下列指令，將配置更新轉換回 protobuf 格式：

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### 將更新傳送至系統頻道

完成這些步驟之前，請確保已正確設定 `FABRIC_CFG_PATH`、`$PROXY` 及 `ORDERER_PORT`。請執行下列指令：

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](#orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](#orderer-endpoint)>
```
{:codeblock}

您的環境變數應該與下列範例類似：

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

建立頻道配置更新（其為 `config_update_in_envelope.json`）之後，排序節點組織可以使用下列指令來提交頻道更新：

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

此指令會同步簽署更新要求，並將其傳送至排序節點。因為排序節點組織是系統頻道的唯一管理者，所以此更新必須只有一個簽章才是有效的要求。如果系統頻道更新順利完成，您會看到下列輸出：

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

在單一排序節點系統頻道配置更新中，可以包含多個組織定義，但是在更新頻道時最好一次使用一個組織，並檢查以確定更新順利完成。

## 檢視排序節點日誌
{: #orderer-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](#ca-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。

- 使用 `kubectl logs` 指令，以檢視 Pod 內的容器日誌。如果您不確定 Pod 名稱，則請執行下列指令來檢視 Pod 清單。

  ```
  kubectl get pods
  ```
  {:codeblock}

  然後，執行下列指令，將 `<pod_name>` 取代為上述指令輸出中的 Pod 名稱，以擷取位於該 Pod 內之排序節點容器的日誌：

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  如需 `kubectl logs` 指令的相關資訊，請參閱 [Kubernetes 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- 或者，您也可以使用 [ICP 叢集管理主控台](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)來存取日誌，這會在 Kibana 中開啟日誌。

  **附註：**當您在 Kibana 中檢視日誌時，可能會收到`找不到結果`回應。如果 ICP 使用您的工作者節點 IP 位址作為其主機名稱，則會發生此狀況。若要解決此問題，請移除畫面頂端開頭為 `node.hostname.keyword` 的過濾器，這樣日誌就會變成可見。

## 疑難排解
{: #orderer-troubleshooting}

### **問題：**`peer channel update` 指令失敗，並發生錯誤。
{: #orderer-peer-channel-update-error}

當您執行 `peer channel update` 指令時，可能會收到下列錯誤：

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

當對等節點使用與排序節點相同的組織 MSP ID 時，可能會導致此錯誤。  

**解決方案：**  

若要解決此問題，您需要重新部署對等節點，並指定不同於部署排序節點時所使用的組織 MSP ID。

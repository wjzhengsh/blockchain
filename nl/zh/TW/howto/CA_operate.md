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

# 在 {{site.data.keyword.cloud_notm}} Private 上操作憑證管理中心
{: #ca-operate}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

「憑證管理中心 (CA)」會提供網路上的身分。CA 可以視為與公開信任的公證人類似，其可用來建立多方之間的信任。網路中的每個實體都會獲得主要 CA 簽署來封裝實體數位身分的憑證。此憑證是信任網路上執行之所有簽署及驗證作業的根源。
{:shortdesc}

多雲端區塊鏈網路中的每個組織都應該部署自己的 CA。您組織的 CA 會針對您組織所操作的元件（例如，排序服務、對等節點或應用程式）來簽署要求。因此，在部署任何其他元件之前，您需要部署 CA。

您需要採取一些必要步驟，才能操作「憑證管理中心」：

- [配置 CLI](#ca-kubectl-configure)
- [擷取憑證管理中心 URL](#ca-url)
- [下載 CA TLS 憑證](#ca-tls)
- [設定 Fabric CA 用戶端](#fabric-ca-client)
- [使用 CA 管理者產生憑證](#enroll-admin)

完成必要步驟之後，您可以使用 CA 在網路中向組織登錄新身分，並產生應用程式可使用的憑證。

- [使用 CA 來部署排序節點或對等節點](#deploy-orderer-peer)
<!-- [Register and enroll client applications](#register-enroll-app)-->

本手冊也提供操作 CA，以及如何使用其憑證來管理區塊鏈網路的一些重要概念資訊。

- [成員資格服務提供者 (MSP)](#msp)
- [TLS 憑證](#tls)

## 必要條件
{: #prerequisites}

不論您是要創立或加入網路，都需要完成這些必要步驟，才能使用您的 CA，以及部署其他屬於您組織的元件和身分。

### 配置 CLI
{: #ca-kubectl-configure}

您需要使用 **kubectl** 指令行工具，以連接至 ICP 中所執行的 CA 容器。

1. 登入 ICP 叢集使用者介面。導覽至**指令行工具**標籤，然後按一下 **Cloud Private CLI**。您將看到下列可下載的工具。

   * 安裝 IBM Cloud Private CLI 及外掛程式
   * 安裝 Kubectl CLI
   * 安裝 Helm
   * 安裝 Istio CLI

  若要操作 CA，您需要使用前**三個**工具，而 Helm 在其中為選用項目。按一下每個項目，然後針對您所使用的機型，執行 `curl` 指令。接著，針對每個工具發出 `chmod` 及 `sudo mv` 指令。`chmod` 指令將變更所提及之 CLI 的許可權以使其可執行，而 `sudo mv` 指令將移動該檔案並予以重新命名。

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

  如果順利完成，則此指令應該傳回與下列範例類似的回應，其中 `ca-6cf85f6687-hcxpl` 是 CA 容器的名稱。

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  您現在可以使用 **kubectl** 工具來取得 CA URL。

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

### 擷取憑證管理中心 URL
{: #ca-url}

您需要將 CA URL 設為憑證產生要求的目標，或使用新的身分登錄。您可以使用 ICP 主控台使用者介面來尋找 CA URL。您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能完成下列步驟：

1. 登入 ICP 主控台，然後按一下左上角的**功能表**圖示。
2. 按一下**工作負載** > **Helm 版本**。
3. 尋找「Helm 版本」的名稱，然後開啟「Helm 版本詳細資料」畫面。
4. 向下捲動至畫面底端的**附註**區段。在**附註**區段中，您會看到一組可協助您操作 CA 部署的指令。
5. 如果您還沒有這麼做，請遵循步驟來配置 [kubectl CLI](#ca-kubectl-configure)，您需要使用此 CLI 來與 CA 容器互動。
6. 在 CLI 中，執行 **1. 執行下列指令，以取得應用程式 URL：**之後附註中的第一個指令。此指令會印出 CA URL 及埠，其與下列範例類似。將此值儲存為應用程式 URL，以與後續指令搭配使用。

  ```
  http://9.30.94.174:30167
  ```

**附註：**如果您部署受防火牆保護的 CA，則需要開啟透通功能來啟用平台上的網路，以完成與 CA 的 TLS 信號交換。您只需要針對與 CA URL 連結的埠啟用透通功能。


### 擷取 CA TLS 憑證
{: #ca-tls}

您需要下載 CA TLS 憑證，並將它與您的指令一起傳遞，以從遠端用戶端與 CA 進行通訊。

1. 登入 ICP 主控台。按一下左上角的**功能表**圖示。
2. 按一下**工作負載** > **Helm 版本**。
3. 尋找「Helm 版本」的名稱，然後開啟「Helm 版本詳細資料」畫面。
4. 向下捲動至畫面底端的**附註**區段。在**附註**區段中，您會看到一組可協助您操作 CA 部署的指令。
5. 如果您還沒有這麼做，請遵循步驟來配置 [kubectl CLI](#ca-kubectl-configure)，您需要使用此 CLI 來與 CA 容器互動。
6. 在 CLI 中，執行 **3. 取得 TLS 憑證：**之後第三個附註中的指令。此指令會將 TLS 憑證儲存為本端目錄上的 `tls.pem` 檔案。您需要在未來指令中參照此憑證。請記下其位置。
7. 此指令也會將憑證轉換為 base64 格式，並將其印出。結果與下面的字串類似：

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  **重要事項：**您需要複製此字串，並將其儲存為 `ICP CA TLS 憑證`。當您部署其他元件時，將需要使用該憑證。

### 設定 Fabric CA 用戶端
{: #fabric-ca-client}

您可以使用 Fabric CA 用戶端來操作 CA。這些指示說明如何針對屬於您組織的其他元件，使用 Fabric CA 用戶端來登記及登錄其身分。您也可以使用 Fabric SDK 來完成必要步驟。

1. 您需要將 [Fabric CA 用戶端 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-client "下載 Fabric CA 用戶端") 下載至本端檔案系統。

  取得 Fabric CA 用戶端的最簡單方法是直接下載所有的 Fabric 工具二進位檔。請導覽至您要使用指令行下載二進位檔的目錄，然後執行下列 [Curl ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") 指令來提取這些檔案。

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

  此指令會在 `bin/` 目錄中安裝二進位檔。

2. 將 `PATH` 路徑設為已下載 Fabric 工具二進位檔的目錄：

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  比方說，如果您已在起始目錄中安裝二進位檔，則會將 `PATH` 設為：

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. 建立將在其中儲存 CA 管理者憑證的資料夾。

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. 將 `$FABRIC_CA_CLIENT_HOME` 環境變數的值，設為 CA 用戶端將在其中儲存所產生 [MSP](#msp) 憑證的路徑。請務必移除先前嘗試可能建立的配置資料。如果您之前未執行 `enroll` 指令，則 `msp` 資料夾與 `.yaml` 檔案不存在。

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. 將[下載自 ICP](#ca-tls) 的 TLS 憑證複製到您可在未來指令中參照它的目錄。請確定您位於 `$HOME/fabric-ca-client` 目錄中。

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### 使用 CA 管理者產生憑證
{: #enroll-ca-admin}

使用 CA 部署元件或用戶端應用程式之前，您需要產生憑證，以將您鑑別為具有登錄新使用者能力的管理者。此處理程序稱為**登記**，其會產生必要憑證、私密金鑰及公用憑證（也稱為登記憑證或 signCert）。

您只能使用已向「憑證管理中心」登錄的身分來產生憑證。請提供身分的名稱及密碼來產生憑證。如果您已部署 CA，則會為您自動登錄 **CA 管理者**身分。您現在可以使用該管理者名稱及密碼透過 Fabric CA 用戶端來發出 `enroll` 指令，以使用憑證來產生 MSP 資料夾，接著使用該憑證來登錄其他對等節點或排序節點身分。

1. 確定您已完成[設定 Fabric CA 用戶端](#fabric-ca-client)的步驟，並確定 `$FABRIC_CA_CLIENT_HOME` 設為您要在其中儲存 CA 管理者憑證的目錄。

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. 執行下列指令以產生憑證：

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  此指令中的 `<enroll_id>` 及 `<enroll_password>` 是當您部署「憑證管理中心」時傳遞給 Kubernetes 密碼的 [CA 管理使用者名稱及密碼](CA_deploy_icp.html#admin-secret)。請在 `<ca_url_with_port>` 內插入 [CA URL](#ca-url)。請拿掉開頭的 `http://`。`<ca_name>` 是當您部署 CA 時提供給 `CA 名稱`欄位的值。

  `<ca_tls_cert_path>` 是您 [CA TLS 憑證](#ca-tls)的完整路徑。

  實際的呼叫可能類似下列指令範例：

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  `enroll` 指令會產生一組完整的憑證（稱為「成員資格服務提供者 (MSP)」資料夾），其位於您針對 Fabric CA 用戶端設為 `$HOME` 路徑的目錄內。例如，`$HOME/fabric-ca-client/ca-admin`。如需 MSP 及 MSP 資料夾所含內容的相關資訊，請參閱[成員資格服務提供者](#msp)。

  如果 `enroll` 指令失敗，請參閱[疑難排解主題](#ca-enroll-error)以取得可能的原因。

您可以執行 tree 指令來驗證您已完成所有必要步驟。請導覽至儲存憑證的目錄。tree 指令應該會產生與下列結構類似的結果：

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
└── catls
    └── tls.pem
```

## 使用 CA 來部署排序節點或對等節點
{: #deploy-orderer-peer}

部署排序節點或對等節點之前，您需要建立一個配置 JSON 檔案，其中包含元件身分及 CA 的重要資訊。然後，您必須使用 [Kubernetes 密碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/configuration/secret/) 物件，在配置期間將此檔案傳遞給 Helm 圖表。這個檔案可讓您的排序節點或對等節點從 CA 取得它要加入區塊鏈網路所需的憑證。此檔案還包含管理者憑證，可讓您以管理使用者的身分操作元件。

下列指示提供您可編輯及儲存至本端檔案系統的[範本 JSON 配置檔](#config-file-template)，並引導您如何使用 CA 來完成此檔案。

- 如果您要在 ICP 上部署排序節點，或部署對等節點來連接至 ICP 上所管理的聯盟，請遵循下列指示。
- 如果您要部署對等節點來連接至「入門範本方案」或「企業方案」，請改為遵循[在 IBM Cloud Private 中部署對等節點來連接至入門範本方案或企業方案](peer_deploy_ibp.html)的相關指示。這些步驟會引導您如何在 {{site.data.keyword.blockchainfull_notm}} Platform 上使用 CA，以便在 ICP 上部署對等節點。

### 配置檔
{: #config-file-template}

您可以在下面找到配置檔的範本：
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

將這個整個檔案複製到文字編輯器，您可以在其中編輯該檔案，並以 JSON 檔案將其儲存至本端檔案系統。

### CA 連線資訊

首先，我們需要提供 CA 在 ICP 上的連線資訊。請使用下列資訊，完成該檔案之 `"component"` 區段中的下列值：

- `"cahost"` 及 `"caport"` 值是 [CA URL](#ca-url) 中的 URL 及埠。例如，如果您的 CA URL 是 http://9.30.94.174:30167，則 `"cahost"` 的值會是 `9.30.94.174`，而 `"caport"` 會是 `30167`。
- `"caname"` 是您在部署 CA Helm 圖表時所指定的 CA 名稱。當您使用 [CA 管理者身分](#enroll-admin)登記時，即參照指令中的 CA 名稱。
- `"cacert"` 是您 CA 的 base64 編碼 TLS 憑證。使用 Helm 版本中的附註從 ICP 主控台[擷取 CA 的 TLS 憑證](#ca-tls)，以及在下列區段中插入附註指令的 base64 輸出。
  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  更新之後，`"cacert"` 欄位會與下列範例類似：

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

- 下面 `"tls"` 區段中的欄位需要您傳遞至上述 component 區段的相同資訊，但您必須使用在 CA 部署期間指定的 CA TLS 實例名稱值。請對 `"cacert"` 值使用相同的 TLS 憑證。
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

擷取「憑證管理中心」連線資訊之後，您需要使用 Fabric CA 用戶端來完成數個作業步驟：

1. [向 CA 登錄新元件](#register-component)。

2. 您也需要[登錄元件管理者](#register-admin)，然後在 MSP 資料夾內[產生元件管理者的憑證](#enroll-admin)。如果您已登錄管理者來部署另一個元件，則不需要完成此步驟。

3. [向 TLS CA 登錄新元件](#tls-register-component)。

### 向 CA 登錄元件身分
{: #register-component}

如果您要透過部署排序服務並在其中新增組織來找到聯盟，或是部署對等節點並將它們加入頻道，則需要先向 CA 登錄元件身分。您的元件部署接著就可以產生對等節點或排序節點參與網路所需的憑證。

1. 透過 Fabric CA 用戶端，[使用 CA 管理者身分產生憑證](#enroll-ca-admin)。使用這些管理者憑證來發出下列指令。確定 `$FABRIC_CA_CLIENT_HOME` 設為 `$HOME/fabric-ca-client/ca-admin`。

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. 發出下列指令，以尋找您的連結及組織名稱。
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  您的指令可能與下列範例類似：
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  您應該會看到類似下列範例的資訊：
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  請記下第二個**連結**值，例如 `org1.department1`。您需要在下面的指令中使用此值。

3. 執行下列指令來登錄排序節點或對等節點。

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  建立元件的名稱及密碼，然後使用它們來取代 `name` 及 `secret`。**重要事項：**請記下此資訊。如果您要部署排序節點，則請將 `--id.type` 設為 `orderer`，或者，如果您要部署對等節點，則請將它設為 `peer`。此指令可能與下列範例類似：

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer --id.secret peerpw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  執行此指令之後，您需要在配置檔的 `"component"` 區段中針對 `"enrollid"` 及 `"enrollsecret"` 輸入 `name` 及 `secret`：

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peerpw",
  ```

  您只能登錄身分一次。如果您遇到問題，請嘗試使用新的使用者名稱和密碼來嘗試指令。為了安全起見，每個身分及隨附的 enrollID 和密碼只用來部署一個對等節點。雖然您可以對數個元件使用一個**管理者**身分（這是我們建議的部署策略），但請勿重複使用對等節點 ID 及密碼。

  當指令順利完成時，您應該會看到類似下列範例的資訊：

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### 登錄管理者
{: #register-admin}

登錄元件之後，您還需要建立可用來操作元件的管理者身分。首先，您需要向 CA 登錄這個新身分，並使用它來產生 MSP 資料夾。接著，您需要將管理使用者的 signCert 新增至配置檔，在部署期間，它會成為排序節點或對等節點的管理者憑證。這可讓您使用管理者身分的憑證來操作區塊鏈網路，例如啟動新的頻道或在對等節點上安裝鏈碼。

您只需要為屬於您組織的元件建立一個管理者身分。如果您要部署多個對等節點或一個排序節點，則只需要完成這些步驟一次。您可以使用針對某個元件所產生的 signCert 來部署任何對等節點或排序節點。

請確定 `$FABRIC_CA_CLIENT_HOME` 設為「CA 管理者」之 MSP 的路徑。

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

請執行下列指令，向 CA 登錄元件管理者身分：

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

請建立管理者的新使用者身分 `name` 及 `secret`。此身分將成為您部署之所有元件的管理者。請務必使用與您剛才登錄之對等節點或排序節點身分不同的值。此指令與下列範例類似。

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**重要事項：**請記下此資訊。您將使用此 `name` 及 `secret` 來產生 MSP 資料夾，方法是使用 `enroll` 指令。

#### 產生管理者 MSP 資料夾
{: #enroll-admin}

登錄元件管理者之後，您可以使用下列指令來產生憑證：

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

例如：

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

此指令順利完成之後，會在您使用 `-M` 旗標所指定的目錄中產生新的 MSP 資料夾。此目錄包含您將用來操作元件的憑證。您可以使用 tree 指令來驗證 enroll 指令是否運作。請導覽至儲存憑證的目錄。tree 指令應該會產生與下列結構類似的結果：

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
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
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

在此 MSP 目錄中，使用下列指令開啟新使用者的 signCert 檔案，並將其轉換為 base64 格式：

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**附註：**使用上述指令所產生的字串必須為單行格式，這點很重要。它看起來應如下所示：

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
 ```
 而不是類似下面這樣：

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
 ```

例如：

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

此指令會印出與下列範例類似的字串：

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

請將此字串複製到配置檔中 component 區段下的 `"admincerts"` 欄位。

#### 向 TLS CA 登錄元件身分
{: #tls-register-component}

您也需要向 TLS CA 登錄排序節點或對等節點。若要這麼做，您需要先使用 TLS CA 的管理者進行登記。請將 `$FABRIC_CA_CLIENT_HOME` 變更為您要在其中儲存 TLS CA 管理者憑證的目錄。

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

請執行下列指令，以您的管理者身分向 TLS CA 登記。此指令與您用來以 [CA 管理者](#enroll-ca-admin)身分登記的指令相同，差異只在於您將使用 [CA 配置](CA_deploy_icp.html#icp-ca-configuration-parms)期間所指定的 CA TLS 實例名稱。請務必在 CA 密碼中使用相同的 `ca-admin-name` 及 `ca-admin-password`。

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

實際的呼叫可能與下列範例類似：

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

登記之後，您就具有用來向 TLS CA 登錄元件的必要憑證。請執行下列指令來登錄排序節點或對等節點：

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

此指令與用來向 CA 登錄元件身分的指令類似，但使用 TLS CA 名稱。如果您要部署排序節點而非對等節點，請將 `--id.type` 設為 `orderer`，而非 `peer`。您必須為此身分提供不同的使用者名稱及密碼，然後才能用於預設 CA。實際的登錄可能與下列指令類似：

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

在配置檔的 `"tls"` 區段下，將 `name` 及 `secret` 的值複製到 `"enrollid"` 及 `"enrollsecret"`：

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### CSR（憑證簽署要求）主機
{: #csr-hosts}

您需要提供 CSR 主機名稱，才能部署排序節點或對等節點。此主機名稱包括您將部署元件之叢集的 Proxy IP 位址，以及部署 Helm 圖表時所產生的`服務主機名稱`。

#### 找到叢集 Proxy IP 位址的值

如果您要在已部署 CA 的相同 ICP 叢集上部署排序節點或對等節點，則請確定具有將部署排序節點或對等節點之 ICP 叢集的[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作") 角色。然後，輸入您在[針對 CA 配置](CA_deploy_icp.html#icp-ca-configuration-parms)時所使用的相同 Proxy IP。如果您要在不同的叢集上部署排序節點或對等節點，則可以完成下列步驟，從 ICP 主控台擷取叢集 Proxy IP 位址的值：

1. 登入 ICP 主控台。在左導覽畫面中，按一下**平台**，然後按一下**節點**以檢視叢集中所定義的節點。
2. 按一下具有 `Proxy` 角色的節點，然後從表格中複製`主機 IP` 的值。
3. 在下面配置檔的 `"csr"` 區段中，將`主機 IP` 插入為 `"hosts"` 的值：

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### 判定服務主機名稱

部署 Helm 圖表時，會產生`服務主機名稱`。它是根據您部署 Helm 圖表時所使用的 `Helm 版本名稱`。

- 如果您要部署排序節點，則`服務主機名稱`是您在部署期間所指定的 `helm_release_name`，而且結尾會新增 `-orderer` 字串。比方說，如果您的叢集 IP Proxy 位址是 `9.42.134.44`，而且您指定 `Helm 版本名稱` `org1orderer`，則可以在此檔案的 `"csr"` 區段中插入下列值：

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- 如果您要部署對等節點，則`服務主機名稱`是您在部署期間所指定的 `Helm 版本名稱`，但沒有補充字串。比方說，如果叢集 IP Proxy 位址是 9.42.134.44，而且 `Helm 版本名稱`是 `org1peer1`，則可以在 csr "hosts" 中插入下列值：

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
      "org1peer1"
    ]
  }
  ```
  {:codeblock}

### 完成配置檔

完成上述所有步驟之後，已更新的配置檔可能與下列範例類似：

```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer",
            "enrollsecret": "peerpw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}

您可以將其他欄位保留空白。請儲存此檔案，您需要在部署[排序節點](orderer_deploy_icp.html)或[對等節點](peer_deploy_icp.html)時使用此檔案。

## 成員資格服務提供者 (MSP)
{: #msp}

{{site.data.keyword.blockchainfull_notm}} Platform 的元件透過「成員資格服務提供者 (MSP)」來取用身分。MSP 會讓 CA 發出的憑證與許可權及角色產生關聯。如需 MSP 的相關資訊，請參閱 [Hyperledger Fabric 文件中的成員資格主題 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Hyperledger Fabric 文件中關於成員資格的主題")。

MSP 資料夾必須具有 Fabric 元件要使用的已定義結構。Fabric CA 用戶端會建立下列 MSP 資料夾來建立此結構：

- **cacerts：**包含您網路之主要 CA 的憑證。
- **intermediatecerts：**這些是您信任鏈中任何中繼 CA 的憑證（回給一個以上的主要 CA）。每個「企業方案」組織都有兩個中繼 CA，可進行失效接手以及獲得高可用性。
- **signCerts：**此資料夾包含您的公用簽署憑證，也稱為您的 signCert 或登記憑證。當您從指令行參照 MSP 目錄，或使用 SDK 建置使用者環境定義物件時，此憑證會附加至您對網路的呼叫（例如，鏈碼呼叫）。如果您要從 SDK 或指令行操作網路，則可以將此憑證上傳至 IBP。
- **keystore：**此資料夾包含您的私密金鑰。如果您從指令行參照 MSP 目錄，或使用 SDK 建置使用者環境定義物件，但未附加至 signCert 的呼叫，則此金鑰用來簽署您對網路的呼叫。此金鑰保持安全十分**重要**。如果它受損，則可能會用來假冒您的身分。

您也可以使用「網路監視器」及 Swagger API，建置 fabric-ca-client 可參照的 MSP 資料夾。

- **cacerts** 及 **intermediatecerts**：您可以對 MSP API 發出 `Get` 要求，以利用 [Swagger API](swagger_apis.html) 來提取這些憑證。
- **signCerts** 及 **keystore**：您可以按一下「憑證管理中心」畫面上的**產生憑證**按鈕，來產生這些憑證。即會開啟一個蹦現視窗，其中列出兩個憑證。請複製**憑證**並將其儲存至 signCerts，以及複製**私密金鑰**並將其儲存至金鑰儲存庫。將這些憑證保存在安全的地方，因為它們未儲存在平台上。

許多 Fabric 元件在其 MSP 資料夾內包含其他資訊。例如，如果您操作遠端對等節點，則可能會看到下列資料夾：

- **admincerts：**此資料夾包含此組織或元件的管理者清單。如果您要從指令行或 SDK 來操作遠端對等節點，則需要將 signCert 上傳至此資料夾。如果您使用 Fabric CA 用戶端，則您的 MSP 中也需要 admincerts 資料夾，其中包含辨識為管理者憑證的對應 signCert。
- **tls：**這是儲存 TLS 憑證的資料夾，而這些憑證用於與其他網路元件通訊。

如需 MSP 結構的相關資訊，請參閱 Hyperledger Fabric 文件中的[成員資格 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "成員資格") 及[成員資格服務提供者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "成員資格服務提供者")。


## 檢視 CA 日誌
{: #ca-operate-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](#ca-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。

- 使用 `kubectl logs` 指令，以檢視 Pod 內的容器日誌。如果您不確定 Pod 名稱，則請執行下列指令來檢視 Pod 清單。

   ```
   kubectl get pods
   ```
   {:codeblock}

   然後，執行下列指令，將 `pod_name` 取代為上述指令輸出中的 Pod 名稱，以擷取位於該 Pod 內之 CA 容器的日誌：

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   如需 `kubectl logs` 指令的相關資訊，請參閱 [Kubernetes 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- 或者，您也可以使用 [ICP 叢集管理主控台](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)來存取部署事件及日誌，這會在 Kibana 中開啟日誌。

  **附註：**當您在 Kibana 中檢視日誌時，可能會收到`找不到結果`回應。如果 ICP 使用您的工作者節點 IP 位址作為其主機名稱，則會發生此狀況。若要解決此問題，請移除畫面頂端開頭為 `node.hostname.keyword` 的過濾器，這樣日誌就會變成可見。

## 疑難排解
{: #ca-operate-troubleshooting}

### **問題：**執行 `enroll` 指令時發生錯誤
{: #ca-enroll-error}

執行 Fabric CA 用戶端 enroll 指令時，指令可能會失敗，並出現下列錯誤：

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**解決方案：**

當您的 Fabric CA 用戶端嘗試登記但無法連接至您的 CA 時，會發生此錯誤。如果有下列情形，便可能會發生這種狀況：   

- `enroll` 指令在 `-u` 參數上包含額外的 `https://`。
- 您的 CA 名稱不正確。
- 您的使用者名稱或密碼不正確。

請檢閱您在 `enroll` 指令上指定的參數，並確定這些狀況都不存在。

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

# 在 {{site.data.keyword.cloud_notm}} Private 中部署對等節點，並連接至入門範本方案或企業方案
{: #peer-ibp}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

下列指示說明如何在 {{site.data.keyword.cloud_notm}} Private (ICP) 上部署 {{site.data.keyword.blockchainfull}} Platform 對等節點，以連接至 {{site.data.keyword.cloud_notm}} 或本端 ICP 上的「入門範本方案」或「企業方案」網路。
{:shortdesc}

在部署對等節點之前，請先檢閱[注意事項及限制](../ibp-for-icp-about.html#ibp-icp-considerations)。

您的「入門範本方案」或「企業方案」網路必須執行 Hyperledger Fabric 1.1 版或 1.2.1 版。您可1以在「網路監視器」中開啟[網路喜好設定視窗](../v10_dashboard.html#network-preferences)來找到 Hyperledger Fabric 版本。

## 所需資源
{: #peer-resources-required}

請確定您的 ICP 系統符合最低硬體資源需求：

| 元件 | vCPU | RAM | 資料儲存空間的磁碟 |
|-----------|------|-----|-----------------------|
| 對等節點  | 2 | 2 GB | 50 GB（可擴充）|
| 對等節點的 CouchDB | 2 | 2 GB | 50 GB（可擴充）|

 **附註：**
 - 如果未針對虛擬機器分割伺服器，則 vCPU 是指派給虛擬機器或實體處理器核心的虛擬核心。當您決定部署在 ICP 中的虛擬處理器核心 (VPC) 時，需要考量 vCPU 需求。VPC 是決定 IBM 產品授權成本的度量單位。如需決定 VPC 之情境的相關資訊，請參閱[虛擬處理器核心 (VPC) ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)。
 - 這些最低資源層次對於測試和實驗而言便已足夠。對於具有大量交易的環境，請務必配置夠大的儲存空間數量；例如，250 GB 用於對等節點。要使用的儲存空間數量將視網路所需的交易數目及簽章數目而定。如果您即將耗盡對等節點上的儲存空間，則必須部署具有較大檔案系統的新對等節點，並讓它透過相同頻道上的其他元件進行同步。

## 儲存空間
{: #storage}

您需要判斷對等節點將使用的儲存空間。如果您使用預設值，則 Helm 圖表將為您的對等節點資料建立一個新的 8 Gi 持續性磁區要求 (PVC)（名稱為 `my-data-pvc`），並為您的狀態資料庫建立另一個 8 Gi PVC（名稱為 `stagedb-pvc`）。

如果您不想使用預設儲存空間設定，請確定在 ICP 安裝期間設定*新的* `storageClass`，否則 Kubernetes 系統管理者需要在您部署之前建立 storageClass。

您可以選擇在 amd64 或 s390x 平台上部署對等節點。不過，請注意，[動態佈建](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)只適用於 ICP 中的 amd64 節點。如果您的叢集混合了 s390x 及 amd64 工作者節點，則無法使用動態佈建。

如果您未使用動態佈建，則必須使用可用來修正 Kubernetes PVC 連結處理程序的標籤，建立及設定[持續性磁區 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。

## 部署對等節點的必要條件
{: #prerequisites-peer-ibp}

1. 在 ICP 上安裝對等節點之前，您必須先[安裝 ICP](../ICP_setup.html) 並[安裝 {{site.data.keyword.blockchainfull_notm}} Platform Helm 圖表](helm_install_icp.html)。

2. 如果您使用 Community Edition，而且想要在沒有網際網路連線功能的 ICP 叢集上執行此 Helm 圖表，則需要先在連接網際網路的機器上建立保存檔，才能在 ICP 叢集上安裝保存檔。如需相關資訊，請參閱[將精選應用程式新增至沒有網際網路連線功能的叢集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "將精選應用程式新增至沒有網際網路連線功能的叢集"){:new_window}。請注意，您可以在 Helm 圖表的 ibm-blockchain-platform-dev/ibm_cloud_pak 下找到規格檔案 manifest.yaml。

3. 您的組織必須是 {{site.data.keyword.cloud_notm}} 上的「入門範本方案」或「企業方案」網路成員。對等節點會利用 {{site.data.keyword.blockchainfull_notm}} Platform 網路的 API 端點、Hyperledger Fabric CA 及「排序服務」來操作。如果您不是任何區塊鏈網路的成員，則需要建立或加入網路。如需相關資訊，請參閱[建立網路](../get_start.html#creating-a-network)或[加入網路](../get_start.html#joining-a-network)。

4. 您必須先在 ICP 上[部署 CA](CA_deploy_icp.html)。您將使用此 CA 作為 TLS CA。在部署對等節點之前，請先遵循[在 ICP 上操作 CA](CA_operate.html#prerequisites) 的必要步驟。您只需要執行這些步驟。

5. 從 ICP 主控台擷取 TLS CA 的叢集 Proxy IP 位址值。**附註：**您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能存取 Proxy IP。請登入 ICP 叢集。在左導覽畫面中，按一下**平台**，然後按一下**節點**以檢視叢集中所定義的節點。按一下具有 `Proxy` 角色的節點，然後從表格中複製`主機 IP` 的值。**重要事項：**請儲存此值，您將在配置 Helm 圖表的 `Proxy IP` 欄位時加以使用。

6. 建立對等節點配置檔，並將其儲存為 ICP 中的 Kubernetes 密碼。您可以在[下節](#peer-config-file)找到建立此檔案的步驟。

## 建置配置檔
{: #peer-config-file}

在部署對等節點之前，您需要建立配置 JSON 檔案，其中包含有關對等節點身分以及您在 {{site.data.keyword.cloud_notm}} 上的憑證管理中心等重要資訊。然後，您必須使用 [Kubernetes 密碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/configuration/secret/) 物件，在配置期間將此檔案傳遞給 Helm 圖表。這個檔案可讓您的對等節點從 {{site.data.keyword.cloud_notm}} 的憑證管理中心取得它要加入「入門範本方案」或「企業方案」網路所需的憑證。此檔案還包含管理者憑證，可讓您以管理使用者的身分操作對等節點。

我們將在這些指示中提供範本 JSON，您可加以編輯並儲存至本端檔案系統。然後，我們將引導您如何使用 CA 來完成配置檔。

### 配置檔

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

將這個整個檔案複製到文字編輯器，您可以在其中編輯該檔案，並以 JSON 檔案將其儲存至本端檔案系統。您只需要填寫這個檔案的最前面兩個區段：`"enrollment"` 及 `"tls"`。

若要完成配置檔，您需要從「入門範本方案」或「企業方案」網路的「網路監視器」中完成幾個步驟。

1. [擷取「入門範本方案」或「企業方案」CA 的端點資訊](#ibp-ca-endpoint)。
2. [向 CA 登錄對等節點](#register-peer)。
3. [登錄對等節點管理者](#register-admin)，您將使用它來操作對等節點。如果您已登錄管理者來部署另一個對等節點，則不需要完成此步驟。

您還需要使用 Fabric CA 用戶端及 ICP 上部署的 TLS CA 來完成幾個步驟。

1. 使用 Fabric CA 用戶端來[產生對等節點管理者 MSP 資料夾](#enroll-admin)。
2. [擷取 TLS CA 的端點資訊](#tls-ca-endpoint)。
3. 使用 Fabric CA 用戶端[向 TLS CA 登錄對等節點](#tls-register-peer)。


### 入門範本方案或企業方案 CA 資訊
{: #ibp-ca-endpoint}

首先，我們需要將您 CA 在 {{site.data.keyword.cloud_notm}} 上的連線資訊提供給配置檔。請登入「入門範本方案」或「企業方案」的「網路監視器」使用者介面。在「網路監視器」的**概觀**畫面上，按一下**遠端對等節點配置**按鈕。這會開啟一個蹦現畫面，其中包含 CA 的必要資訊。

![遠端對等節點配置](../images/myresources_starter.png "遠端對等節點配置")
*圖 1. 遠端對等節點配置畫面*

蹦現視窗包含下列欄位：

  - **組織 MSP**
  - **憑證管理中心 (CA) 名稱**
  - **憑證管理中心 (CA) URL**
  - **憑證管理中心 (CA) TLS 憑證**

在該檔案的 `"components"` 區段下，利用上述資訊輸入下列值：
- `"caname"` 是**憑證管理中心 (CA) 名稱**的值
- `"cahost"` 是 CA URL 的主機名稱。比方說，如果 CA URL 是 `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`，則 `"cahost"` 的值會是 `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com`
- `"caport"` 是 `"cahost"` 中的埠。比方說，如果 CA URL 是 `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`，則 `"caport"` 會是 `31011`。
- `"cacert"` 是來自**憑證管理中心 (CA) TLS 憑證**欄位的值。在該檔案中插入憑證之前，您需要先執行下列指令，並將 `<paste in Certificate Authority (CA) TLS Certificate>` 字串取代為您從「網路監視器」中複製的值，將它編碼為 base64 格式。

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
  ```
  {:codeblock}

  **附註：**上述指令產生的字串必須為單行格式，這點很重要。它看起來應如下所示：

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

將產生的字串貼到該檔案中 `"catls"` 下的 `"cacert"` 欄位。更新之後，`"cacert"` 欄位會與下列範例類似：

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### 登錄對等節點
{: #register-peer}

若要將對等節點加入頻道，並安裝及實例化鏈碼，您需要先在 {{site.data.keyword.cloud_notm}} 上向 CA 登錄對等節點。您的對等節點部署接著就可以產生對等節點參與「入門範本方案」或「企業方案」網路所需的憑證。請完成下列步驟，以 `enroll ID` 及 `enroll secret` 登錄對等節點。您將在配置檔中貼入這兩個值。

1. 登入 {{site.data.keyword.blockchainfull_notm}} Platform，並存取「網路監視器」。在「憑證管理中心」畫面上，您可以檢視已向組織登錄的所有身分，例如管理者或用戶端應用程式。
  ![CA 畫面](../images/CA_screen_starter.png "CA 畫面")
  *圖 2. CA 畫面*

2. 按一下畫面上的**新增使用者**按鈕。蹦現畫面即會開啟，並可讓您在填寫下列欄位之後，登錄您的對等節點。儲存 ID 及「密碼」的值，您在配置對等節點時需要使用它們。
  - **登記 ID：**配置對等節點時，對等節點的使用者名稱，稱為 `enroll ID`。請為配置檔**儲存此值**。
  - **登記密碼：**配置對等節點時，對等節點的密碼，稱為 `enroll Secret`。請為配置檔**儲存此值**。
  - **類型：**為此欄位選取 `Peer`。
  - **連結：**這是組織下對等節點所屬的連結，例如 `org1`。請從下拉清單中選取現有的連結，或鍵入新的連結。
  - **登記數上限：**您可以使用此欄位，限制您可以使用此身分來登記或產生憑證的次數。如果未指定，則此值預設為不限次數的登記。

  完成這些欄位之後，請按一下**提交**來登錄對等節點。登錄的對等節點即會列示在表格中，作為組織中的一個身分。為了安全起見，每個身分及隨附的 enrollID 和密碼只用來部署一個對等節點。請勿重複使用對等節點的 ID 及密碼。

3. 在配置檔的 `"components"` 區段下，輸入下列值：
  - `"enrollid"` 是前一個步驟的 `enroll ID` 值。
  - `"enrollsecret"` 是前一個步驟的 `enroll secret` 值。


### 建立管理者
{: #register-admin}

登錄對等節點身分之後，您還需要建立將用來操作對等節點的管理者身分。首先，您需要向 CA 登錄這個新身分，並使用它來產生 MSP 資料夾。接著，您需要將管理使用者的 signCert 新增至配置檔，在部署期間，它會成為對等節點的管理者憑證。這可讓您使用管理者身分的憑證來操作區塊鏈網路，例如啟動新的頻道或在對等節點上安裝鏈碼。

您只需要為屬於您組織的元件建立一個管理者身分。如果您要部署多個對等節點，則只需要完成這些步驟一次。您可以使用針對某個對等節點所產生的 signCert，來部署屬於您組織的其他任何對等節點。

1. 登入 {{site.data.keyword.blockchainfull_notm}} Platform，並存取「網路監視器」。在「憑證管理中心」畫面上，您可以檢視已向組織登錄的所有身分，例如管理者或用戶端應用程式。
  ![CA 畫面](../images/CA_screen_starter.png "CA 畫面")
  *圖 2. CA 畫面*

2. 按一下畫面上的**新增使用者**按鈕。蹦現畫面即會開啟，並可讓您在填寫下列欄位之後，登錄您的對等節點。儲存 ID 及「密碼」的值，您在配置對等節點時需要使用它們。
  - **登記 ID：**配置對等節點時，對等節點管理者的使用者名稱，稱為 `enroll ID`。請**儲存此值**，以供您產生管理者 MSP 資料夾時使用。
  - **登記密碼：**配置對等節點時，對等節點管理者的密碼，稱為 `enroll Secret`。請**儲存此值**，以供您產生管理者 MSP 資料夾時使用。
  - **類型：**為此欄位選取 `Peer`。
  - **連結：**這是組織下對等節點所屬的連結，例如 `org1`。請從下拉清單中選取現有的連結，或鍵入新的連結。
  - **登記數上限：**您可以使用此欄位，限制您可以使用此身分來登記或產生憑證的次數。如果未指定，則此值預設為不限次數的登記。

  輸入這些欄位之後，請按一下**提交**來建立管理者。所建立的管理者隨即會列在表格中，作為組織中的身分。

### 產生對等節點管理者 MSP 資料夾
{: #enroll-admin}

在登錄管理者身分之後，您需要產生對等節點管理者 MSP 資料夾及 signCert。因此，您需要對「入門範本方案」或「企業方案」CA 執行 enroll 指令。

1. 如果您尚未下載 [Fabric CA 用戶端](CA_operate.html#fabric-ca-client)，請下載該用戶端。
2. 導覽至您將儲存加密資料的目錄，並建立資料夾來儲存對等節點管理者的 MSP 資料夾。

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. 設定用戶端可在其中將憑證建立為 `$FABRIC_CA_CLIENT_HOME` 的路徑。請務必移除先前嘗試可能建立的配置資料。如果您之前未執行 `enroll` 指令，則 `msp` 資料夾與 `.yaml` 檔案不存在。

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. 從「網路監視器」的「概觀」畫面中，開啟**連線設定檔** JSON 檔案，並尋找下列變數：
  - CA 的 URL：`certificateAuthorities` 下的 `url`
  - CA 名稱：`caName`

5. 根據您所使用的服務方案、位置及叢集，從 {{site.data.keyword.cloud_notm}} 下載 TLS 憑證。例如，您可以根據憑證管理中心 URL 的網域名稱來找到您的叢集：`us01.blockchain.ibm.com:31011` 或 `us02.blockchain.ibm.com:31011`。

  - 入門範本方案的主要 TLS 憑證
    - 美國：[us01.blockchain.ibm.com.cert ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert")；[us02.blockchain.ibm.com.cert ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - 英國：[uk01.blockchain.ibm.com.cert ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert")；[uk02.blockchain.ibm.com.cert ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - 雪梨：[aus01.blockchain.ibm.com.cert ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--；[aus02.blockchain.ibm.com.cert ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [企業方案的主要 TLS 憑證 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  將內容儲存至您可在未來指令中參照它的目錄。

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. 發出下列指令，以您的對等節點管理者身分產生憑證：

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  上述的 `<enroll_id>` 及 `<enroll_password>` 是[使用網路監視器登錄](#register-admin)之對等節點管理者的 **ID** 及**密碼**。`<ca_name>` 及 `<ca_url_with_port>` 是來自連線設定檔的 `caName` 及 `url` 值。請拿掉 CA URL 開頭的 `http://`。

  實際的呼叫可能類似下列指令範例：

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  此指令順利完成之後，它會在您指定為 `$FABRIC_CA_CLIENT_HOME` 的目錄中產生新的 MSP 資料夾。此目錄包含您將用來操作對等節點的憑證。

7. 在剛剛建立的 MSP 資料夾中，開啟新使用者的 signCert 檔案，並將其轉換為 base64 格式。您將在 MSP 目錄的 `signcerts` 資料夾中找到 signCert。如果您要使用範例步驟，則可以使用下列指令：

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
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

  此指令會印出與下列範例類似的字串：

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  請將此字串輸入到配置檔中 component 區段下的 `"admincerts"` 欄位。

### TLS CA 資訊
{: #tls-ca-endpoint}

配置檔中的 `"tls"` 欄位需要您在 ICP 上所部署 CA 的資訊。您將使用此 CA 作為不同的 TLS CA，這可讓您的對等節點更安全。請使用下列指示來產生相關資訊：

- `"cahost"` 及 `"caport"` 值是 [CA URL](CA_operate.html#ca-url) 的 URL 及埠。比方說，如果 CA URL 是 `http://9.30.94.174:30167`，`cahost` 的值會是 `9.30.94.174`，而 `caport` 則是 `30167`。
- `"caname"` 是您在 ICP 上所部署 CA 的 TLS CA 名稱。在 CA 配置期間，TLS CA 名稱是您提供給 `CA TLS 實例名稱`欄位的值。
- `"cacert"` 是您 CA 的 base64 編碼 TLS 憑證。請將下列區段更新為您擷取 [CA 的 TLS 憑證](CA_operate.html#ca-tls)作為必備項目時的指令輸出值。

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

### 向 TLS CA 登錄對等節點
{: #tls-register-peer}

您需要使用 Fabric CA 用戶端，向 ICP 上的 TLS CA 登錄您的對等節點。

1. 現在，您在 `$HOME/fabric-ca-client/catls` 資料夾中應該具有 TLS 憑證檔案 `tls.pem`。如果沒有，您可以將您[從 ICP 下載](CA_operate.html#ca-tls)的 TLS 憑證複製到某個目錄，讓您在下列指令中能夠參照它。請確定您位於 `$HOME/fabric-ca-client` 目錄中。

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. 您需要使用 TLS CA 的管理者進行登記。請將 `$FABRIC_CA_CLIENT_HOME` 變更為您要在其中儲存 TLS CA 管理者憑證的目錄。

  ```
  cd $HOME/fabric-ca-client
  mkdir tlsca-admin
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. 執行下列指令，以使用 TLS CA 管理者產生憑證。

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  指令中的 `<enroll_id>` 及 `<enroll_password>` 是當您部署「憑證管理中心」時傳遞給 Kubernetes 密碼的 [CA 管理使用者名稱及密碼](CA_deploy_icp.html#admin-secret)。請在 `<ca_url_with_port>` 內插入 [CA URL](CA_operate.html#ca-url)。請拿掉開頭的 `http://`。`<tls_ca_name>` 是您在 [CA 配置](CA_deploy_icp.html#icp-ca-configuration-parms)期間所指定的值。

  `<ca_tls_cert_file>` 是您的 [CA TLS 憑證](CA_operate.html#ca-tls)檔名及其完整路徑。

  實際的呼叫可能與下列範例類似：

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  登記之後，您就具有用來向 TLS CA 登錄對等節點的必要憑證。

4. 發出下列指令，以尋找您的連結及組織名稱。

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  您的指令可能與下列範例類似：

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  您應該會看到類似下列範例的資訊：

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  請記下您組織的**連結**值，例如上述範例中組織 `org1` 的 `org1.department1`。您需要在下面的指令中使用此值。

5. 執行下列指令來登錄對等節點。

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  您需要在 `--caname` 欄位中使用您的 TLS CA 名稱。建立對等節點的名稱及密碼，然後利用它們來取代 `name` 和 `secret`。**重要事項：**請記下此資訊。您需要在配置檔的 `"tls"` 區段中針對 `"enrollid"` 及 `"enrollsecret"` 輸入 `name` 及 `secret`。

  指令範例可能與下列內容類似：

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  發出這個指令之後，您可以使用上述的值來更新該檔案：

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

  您只能登錄一次身分。如果您遇到問題，請嘗試使用新的使用者名稱和密碼來嘗試指令。為了安全起見，每個身分及隨附的 enrollID 和密碼只用來部署一個對等節點。請勿重複使用對等節點的 ID 及密碼。

  當指令順利完成時，您應該會看到類似下列範例的資訊：

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

您可以執行 tree 指令來驗證您為了準備配置檔所完成的工作。請導覽至儲存憑證的目錄。tree 指令應該會產生與下列結構類似的結果：

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
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
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

### CSR（憑證簽署要求）主機
{: #csr-hosts}

您需要提供 CSR 主機名稱才能部署對等節點。CSR 主機名稱包括您將部署元件之叢集的 Proxy IP 位址，以及將成為 Helm 圖表主機名稱的`服務主機名稱`。

#### 找到叢集 Proxy IP 位址的值

如果您想要在已部署 TLS CA 的同一個 ICP 叢集上部署對等節點，請輸入您在[針對 TLS CA 配置](CA_deploy_icp.html#icp-ca-configuration-parms)時所使用的相同 Proxy IP。如果您要在不同的叢集上部署元件，則可以從 ICP 主控台中擷取叢集 Proxy IP 位址的值。您必須對於將在其中部署對等節點的 ICP 叢集具有叢集管理者角色。

1. 登入 ICP 主控台。在左導覽畫面中，按一下**平台**，然後按一下**節點**以檢視叢集中所定義的節點。
2. 按一下具有 `Proxy` 角色的節點，然後從表格中複製`主機 IP` 的值。
3. 在下面配置檔的 `"csr"` 區段中，將`主機 IP` 插入為 `"hosts"` 的值：

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### 判定服務主機名稱

`服務主機名稱`將是您在部署期間指定的 `Helm 版本名稱`。如果叢集 IP Proxy 位址為 9.42.134.44，且 `Helm 版本名稱`是 `org1peer1`，則您將插入該檔案的 `"csr"` 區段：

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

完成所有步驟之後，已更新的配置檔將與下列範例類似：

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer",
			"enrollsecret": "peerpw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
           "org1peer1"
        ]
			}
		}
  }
}
```
{:codeblock}

在您完成此檔案的填寫之後，您需要以 JSON 格式儲存它，並將它傳遞至對等節點部署作為 Kurbernetes 密碼。請使用[下節](#peer-config-file-ibp)的步驟來建立密碼。

## 建立配置密碼
{: #peer-config-file-ibp}

[Kubernetes 密碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/configuration/secret/) 可讓您保護及共用資訊，而不必將資訊直接傳遞至部署。儲存配置檔之後，您需要以 `base64` 將其編碼。

1. 若要以 base64 格式將配置檔編碼，請從終端機視窗執行下列指令：

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
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

  儲存產生的輸出供下面的步驟 4 使用。

2. 登入 ICP 主控台。在左導覽畫面中，按一下**配置**，然後按一下**密碼**。按一下**建立密碼**按鈕來開啟蹦現畫面，讓您能夠產生新的密碼物件。

3. 在**一般**標籤上，完成下列欄位：
  - **名稱：**為您的密碼提供叢集內的唯一名稱。當您部署對等節點時，會使用這個名稱。此名稱必須全部為小寫。  
  **附註：**當您部署對等節點時，此部署會以 `<helm_release_name>-secret` 名稱自動產生新密碼。因此，當您為密碼命名時，密碼的名稱必須不同於 `<helm_release_name>-secret`。否則，Helm 圖表部署將會失敗，因為它嘗試建立的密碼已存在。
  - **名稱空間：**要新增密碼的名稱空間。請選取您要在其中部署對等節點的`名稱空間`。
  - **類型：**輸入 `Opaque` 值。

4. 按一下此視窗之左導覽窗格中的**資料**標籤。在`名稱`欄位中指定 `secret.json`，然後在值欄位中，貼上配置檔的 `base64` 編碼字串。

5. （選用）如果您計劃使用 `CouchDB` 作為狀態資料庫，則需要在這個密碼物件中新增另外兩個值。在**資料**標籤中，按一下**新增資料**按鈕，以新增在部署容器時要用於資料庫的 CouchDB 使用者 ID 及密碼。
  1. 建立您的使用者名稱及密碼，並以 base64 格式將這些值編碼。在終端機視窗中執行下列指令，並將 `admin` 及 `adminpw` 取代為您要使用的值。
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

   2. 在**名稱**欄位中，輸入 `couchdbuser` 值。在對應的**值**欄位中，輸入上一個步驟的 `echo -n 'couch' | base64 $FLAG` 結果。
   3. 按一下**新增資料**按鈕，以新增第二個鍵值組。
   4. 在第二個**名稱**欄位中，輸入 `couchdbpwd` 值。在對應的**值**欄位中，輸入上一個步驟的 `echo -n 'couchpw' | base64 $FLAG` 結果。

6. 按一下**建立**以儲存密碼物件。

**附註：**當您刪除 Helm 版本時，不會從 ICP 叢集中移除對等節點配置密碼。如果您刪除對等節點，也必須一併刪除這個密碼。

## 配置
{: #peer-configuration}

建立您的對等節點配置密碼物件之後，您可以使用下列步驟，在 ICP 中配置及安裝您的對等節點。您一次只能安裝一個對等節點。

1. 登入 ICP 主控台，然後按一下右上角的**型錄**鏈結。
2. 如果您已下載 Community Edition，則請按一下左導覽畫面中的 `Blockchain`，以找到標示 `ibm-blockchain-platform-prod` 或 `ibm-blockchain-platform-dev` 的磚。按一下此磚加以開啟，您就可以看到 Readme 檔，其中包括安裝及配置 Helm 圖表的相關資訊。
3. 按一下畫面頂端的**配置**標籤，或按一下右下角的**配置**按鈕。
4. 指定[一般配置參數](#peer-global-parameters)的值，並接受授權合約。
5. 開啟`所有參數`小三角形，並指定[廣域配置參數](#peer-global-parameters)的值。
6. 向下捲動至**對等節點配置**區段。選取`安裝對等節點`勾選框，然後完成對等節點的[配置設定](#peer-parameters)。
7. 按一下**安裝**。

### 配置參數
{: #ibp-peer-configuration-parms}

下表列出 {{site.data.keyword.blockchainfull_notm}} Platform 的可配置參數（**對等節點元件特有**）及其預設值。如果您是第一次實驗對等節點或開始使用，請使用資料庫及儲存空間參數的預設值。請捲動至對等節點元件區段，以勾選`安裝對等節點`勾選框，並只為該元件提供相關聯的配置資訊。**雖然 Helm 圖表使用者介面指出不需要進一步配置，但您需要輸入特定參數來部署對等節點。**

#### 一般及廣域配置參數
{: #peer-global-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
| `Helm 版本名稱`| Helm 版本的名稱。開頭必須為小寫字母、結尾必須為任何英數字元，而且只能包含連字號及小寫英數字元。每次您嘗試安裝元件時，都必須使用唯一的 Helm 版本名稱。**重要事項：**此值必須符合您在 [JSON 機密檔](#csr-hosts)中針對 "hosts" 欄位用來產生「服務主機名稱」的值。| 無 | 是 |
| `目標名稱空間`| 選擇 Kubernetes 名稱空間來安裝 Helm 圖表。| 無 | 是 |
| **廣域配置**| 適用於 Helm 圖表中所有元件的參數 |||
| `服務帳戶名稱`| 輸入您將用來執行 Pod 的[服務帳戶 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) 名稱。| 預設值 | 否 |

#### 對等節點配置參數
{: #peer-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
|**叢集配置** |**叢集配置資訊** | ||
| `安裝對等節點` | 選取要安裝對等節點 | 不勾選 | 是（如果您要安裝對等節點的話）|
| `對等節點工作者節點架構`| 選取您的雲端平台架構（AMD64 或 S390x）| AMD64 | 是 |
| `對等節點映像檔儲存庫`| 對等節點 Helm 圖表的位置。此欄位會自動填寫至已安裝的路徑。如果您使用 Community Edition，而且沒有網際網路存取權，則它應該符合您下載 Fabric 對等節點映像檔的目錄。| ibmcom/ibp-fabric-peer | 是 |
| `對等節點 Docker 映像檔標籤`|與對等節點映像檔相關聯的標籤值|1.2.1（自動填入正確值）。| 是 |
| `對等節點配置`| 您可以在這個欄位中貼上您自己的 `core.yaml` 配置檔，來自訂對等節點的配置。若要查看 `core.yaml` 檔案範例，請參閱 [`core.yaml` 範例配置 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **僅適用於進階使用者**。| 無 | 否 |
| `對等節點配置密碼（必要）`|您在 ICP 中所建立之[對等節點配置密碼](#peer-config-secret)的名稱。| 無 | 是 |
|`組織 MSP（必要）`|按一下「概觀」畫面上的「遠端對等節點配置」，即可在「網路監視器」（IBP 使用者介面）中找到此值。| 無 | 是 |
|`對等服務程式類型`| 用來指定在對等節點上是否[應該公開外部埠 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)。選取 NodePort 可在外部公開埠（建議使用），而選取 ClusterIP 則不會公開埠。此版本不支援 LoadBalancer 及 ExternalName。| NodePort | 是 |
| `狀態資料庫`| 用來儲存頻道分類帳的[狀態資料庫](../glossary.html#state-database)。對等節點需要使用與[區塊鏈網路](../v10_dashboard.html#network-preferences)相同的資料庫。|LevelDB| 是 |
|`CouchDB 映像檔儲存庫`| 只有在已選取 CouchDB 作為分類帳資料庫時才適用。此欄位會自動填寫至已安裝的路徑。如果您使用 Community Edition，而且沒有網際網路存取權，則它應該符合您下載 Fabric CouchDB 映像檔的目錄。| ibmcom/ibp-fabric-couchdb | 是 |
| `CouchDB Docker 映像檔標籤`| 只有在已選取 CouchDB 作為分類帳資料庫時才適用。與 CouchDB 映像檔相關聯的標籤值。| 自動填入正確值。| 是 |
| `已啟用對等節點資料持續性`| 啟用在叢集重新啟動或失敗之後持續保存資料的能力。如需相關資訊，請參閱 [Kubernetes 中的儲存空間 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/ "磁區")。*如果不勾選，則會在發生失效接手或 Pod 重新啟動時遺失所有資料。*| 已勾選 | 否 |
| `對等節點使用動態佈建`| 勾選以啟用儲存空間磁區的動態佈建。| 已勾選 | 否 |
| `對等節點持續性磁區要求`| 僅適用於新要求。輸入要建立的新「持續性磁區要求 (PVC)」名稱。| my-data-pvc | 否 |
| `對等節點儲存空間類別名稱`| 指定對等節點的儲存空間類別名稱。| 如果建立新的 PVC，則為空白；否則，請指定與現有 PVC 相關聯的儲存空間類別。| 否 |
| `對等節點現有磁區要求`| 指定現有「磁區要求」的名稱，並將所有其他欄位保留為空白。| 無 | 否 |
| `對等節點選取器標籤`| [PVC 的選取器標籤 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "標籤與選取器")。| 無 | 否 |
| `對等節點選取器值`| [PVC 的選取器值 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "標籤與選取器")。| 無 | 否 |
| `對等節點儲存空間存取模式`| 指定 PVC 的儲存空間[存取模式 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "存取模式")。| ReadWriteMany| 否 |
| `對等節點磁區要求大小`|「磁區要求」的大小，必須大於 2Gi。| 8Gi  | 是 |
| `狀態資料庫持續性磁區要求`| 僅適用於新要求。輸入要建立的新「持續性磁區要求 (PVC)」名稱。| statedb-pvc | 否 |
| `狀態資料庫儲存空間類別名稱`| 指定狀態資料庫的儲存空間類別名稱。| 無 | 否 |
| `狀態資料庫現有磁區要求`| 指定現有「磁區要求」的名稱，並將所有其他欄位保留為空白。| 無 | 否 |
| `狀態資料庫選取器標籤`| PVC 的[選取器標籤](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| 無 | 否 |
| `狀態資料庫選取器值`| PVC 的[選取器值](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| 無 | 否 |
| `狀態資料庫儲存空間存取模式`| 指定 PVC 的儲存空間[存取模式 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "存取模式")。| ReadWriteMany| 否 |
| `狀態資料庫磁區要求大小`| 選擇要使用的磁碟大小。| 8Gi | 是 |
| `CouchDB - 已啟用資料持續性`| 若為 CouchDB 容器，當容器重新啟動時，分類帳資料將可供使用。*如果不勾選，則會在發生失效接手或 Pod 重新啟動時遺失所有資料。*| 已勾選 | 否 |
| `CouchDB - 使用動態佈建`| 若為 CouchDB 容器，請使用 Kubernetes 動態儲存空間。| 已勾選 | 否 |
| `對等節點 CPU 要求` | 要配置給對等節點的 CPU 數目下限。| 1 | 是 |
| `對等節點 CPU 限制` | 要配置給對等節點的 CPU 數目上限。| 2 | 是 |
| `對等節點記憶體要求` | 要配置給對等節點的記憶體數量下限。| 1Gi | 是 |
| `對等節點記憶體限制` | 要配置給對等節點的記憶體數量上限。| 4Gi | 是 |
| `CouchDB CPU 要求` | 要配置給 CouchDB 的 CPU 數目下限。| 1 | 是 |
| `CouchDB CPU 限制` | 要配置給 CouchDB 的 CPU 數目上限。| 2 | 是 |
| `CouchDB 記憶體要求` | 要配置給 CouchDB 的記憶體數量下限。| 1Gi | 是 |
| `CouchDB 記憶體限制` | 要配置給 CouchDB 的記憶體數量上限。| 4Gi | 是 |


選取 CouchDB 作為對等節點資料庫時，會在 Pod 中建立兩個容器，一個用於對等節點，一個用於 CouchDB。「對等節點」容器包括「對等節點 PVC」的單一磁區裝載，其會將區塊及交易儲存在檔案系統上。CouchDB 容器會裝載對等節點狀態資料庫 PVC，其中包含分類帳資料。

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### 使用 Helm 指令行來安裝 Helm 版本
{: #ibp-helm-cli}

或者，您也可以使用 Helm CLI 來安裝 Helm 版本。執行 `helm install` 指令之前，請確定您[將叢集的 Helm 儲存庫新增至 Helm CLI 環境 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "將內部 Helm 儲存庫新增至 Helm CLI")。

您可以建立 `yaml` 檔案並將其傳遞至下列 `helm install` 指令，來設定安裝所需的參數。

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

其中：
- `<helm_release name>` 代表您要提供給 Helm 版本的名稱。
- `<helm_chart>` 代表匯入至型錄的 Helm 圖表名稱。
- `<helm_chart_version>` 代表匯入至型錄的 Helm 圖表版本。
- `<customvalues.yaml>` 是包含配置參數的 yaml 檔案名稱。

例如：

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```
您可以編輯所下載保存檔中包含的 `values.yaml` 來建立新的 `yaml` 檔案，其中包括所有必要的參數及其預設值。

## 驗證對等節點安裝
{: #verify-installation-ibp}

完成配置參數並按一下**安裝**按鈕之後，請按一下**檢視 Helm 版本**按鈕，以檢視您的部署。如果已順利完成，您應該會在「部署」表格的 `DESIRED`、`CURRENT`、`UP TO DATE` 及 `AVAILABLE` 欄位中看到值 1。您可能需要按一下「重新整理」，並等待更新表格。您也可以按一下 ICP 主控台左上角的**功能表**圖示，來尋找「部署」表格。從功能表清單中，按一下**工作負載**，然後按一下 **Helm 版本**。

如果您往下捲動至`附註`區段，則會有您在[操作對等節點](peer_operate_ibp.html)時將使用的重要資訊。

<!--
### Verifying the peer can connect to Starter or Enterprise Plan network

You can run a peer CLI command from inside the peer container to verify that your peer has connected to your network on the {{site.data.keyword.blockchainfull_notm}} Platform. Complete the following instructions to run the `peer channel fetch` command to fetch the genesis block from a channel:

1. If you have not already connected to your ICP cluster, follow these [instructions](peer_operate_ibp.html#peer-kubectl-configure) to connect to it and use the cli from inside the peer container.

2. If you deploy your peer behind a firewall, you need to open a passthru to enable the network on the platform to complete a TlS handshake with your peer. You only need to enable a passthru for the Node port bound to port 7051 of your peer. For more information, see [finding your peer endpoint information](peer_operate_ibp.html#peer-endpoint).

3. Your organization needs to be added to a channel in the network before you can fetch the genesis block.

  - You can start a new channel for the peer. If you are using IBP Starter or Enterprise Plan, you can automatically include your organization during [channel creation](create_channel.html#creating-a-channel).

  - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](create_channel.html#updating-a-channel).

    The peer uploads its signCert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joined and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

4. You also need to upload the signCert of your peer admin to Starter Plan or Enterprise Plan in order for a remote CLI or application to perform channel operations such as fetching the channel genesis block and instantiating chaincode.
    1. On your local machine, cat the file `HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem` and then copy it to the clipboard.
    2. Enter the Network Monitor of your network on {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Click **Members** in the left navigator and click the **Certificates** tab.
    4. Click the **Add Certificate** button.
    5. In the **Add certificate** window, give your certificate a name, such as "peer-admin", paste in the certificate you just copied to the clipboard and click **Submit**.
    6. You will be asked whether you want to restart the peers. Click **Restart**.
    7. Click **Channels** in the left navigator and locate the channel that the peer will join.
    8. Click the three dots under the **Actions** header and click **Sync Certificate**. In the **Sync certificate** window, click **Submit**.

5. Retrieve configuration information from your Connection profile available on the "Overview" screen of your Network Monitor. Click **Connection Profile** and then **Download**.

  - Find the orderer URL by searching for **orderers**, which is located under `orderers > url`. Make a note of the URL that begins with the network name. The URL is similar to the following example:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Find the name of your organization by searching for **organizations**. This should be the same organization as you use to register your peer. You can find your organization's name together with its associated `mspid`. Make a note of the value of the `mspid`.

6. Run the following commands to set the environment variables in the peer container.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Replace the fields with your own information.
  - Replace `<ORDERER_URL>` with the hostname and port of the orderer from the `creds.json` file.
  - Replace `<CHANNEL_NAME>` with the name of the channel that the peer joins.
  - Replace `<CC_NAME>` with any name to refer to your chaincode.
  - Replace `<ORGANIZATION_MSP_ID>` with the name of the organization from the `creds.json` file.
  - Replace `<PEER_ADDR>` with `localhost:7051`

  For example:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=org1
  export PEERADDR=localhost:7051
  ```
  {:codeblock}

7. Run the following peer CLI command to fetch the genesis block of the channel.

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
  ```
  {:codeblock}

  **Note:** It is possible that when you run any of these CLI commands, you might experience the following warning that can be safely ignored.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```

  Verify that the command worked successfully and that genesis block is added to your peer container by running the following command:

  ```
  ls *.block
  ```
  {:codeblock}

  You know that the genesis block is added successfully when you see something that is similar to the following example:

  ```
  defaultchannel_0.block
  ```

  Successfully fetching the genesis block indicates that your peer can connect to your Starter or Enterprise Plan network.
  You still need to join the peer to the channel and install chaincode. See [operating your peer](peer_operate_ibp.html) for more information.

-->

## 檢視對等節點日誌
{: #peer-ibp-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](peer_operate_ibp.html#peer-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。如需相關資訊，請參閱這些[存取日誌指示](peer_operate_ibp.html#peer-ibp-view-logs)。

## 下一步為何？

部署對等節點之後，您需要完成數個作業步驟，然後才能將交易提交至區塊鏈網路，並從區塊鏈網路讀取分散式分類帳。如需相關資訊，請參閱[使用入門範本方案或企業方案操作對等節點](peer_operate_ibp.html)。

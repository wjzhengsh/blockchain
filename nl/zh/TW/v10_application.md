---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 開發應用程式
{: #dev_app}
利用應用程式，您可以在 {{site.data.keyword.blockchain}} 網路中，呼叫鏈碼來查詢或更新通道特定的分類帳。
{:shortdesc}

## 設定應用程式開發環境
您需要安裝軟體必備項目，才能開發可在 {{site.data.keyword.Bluemix}} 上與 {{site.data.keyword.blockchain}} 網路互動的應用程式。
{:shortdesc}

*	Git（[Git 下載頁面 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://git-scm.com/downloads){:new_window}）

	Git 是一種版本控制工具，可讓您大略熟悉鏈碼開發和軟體開發。此外，隨著 Git 一起安裝在 Windows 上的 Git Bash，也是 Windows 命令提示字元的絕佳替代方案。

	使用下列指令來驗證 Git 安裝。您應該會看到類似下面的輸出：
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang（[GoLang 下載頁面 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://golang.org/dl){:new_window}）

	GoLang 安裝程式會安裝一組 Go CLI 工具，在撰寫鏈碼時非常好用。例如，`go build` 指令可讓您先檢查鏈碼是否已確實編譯，再嘗試將它部署至網路。在撰寫本文時，已知此鏈碼可使用 `1.8.3` 版順利建置。

	使用下列指令來驗證 GoLang 版本。您應該會看到類似下面的輸出：
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}

	遵循[安裝指示 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://golang.org/doc/install){:new_window}，以適當設定環境變數。使用下列指令來檢查您的 `GOPATH`。請注意，您的 `GOPATH` 不需要與範例一模一樣，唯一重要的是，您要將此變數設為檔案系統上的有效目錄。
	```
	$ echo $GOPATH
	C:\gopath
	```
	{:screen}

	然後，您可以使用 [hello world ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://golang.org/doc/install#testing){:new_window} 範例來建置 GoLang 程式碼，以驗證 GoLang 安裝。

*	Node.js（[Node.js 下載頁面 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://nodejs.org/en/download/){:new_window}）。請選擇 6.9.5 與 7 之間的版本。如果節點版本大於 7，在下載 SDK 模組時可能會導致錯誤。

	使用下列指令來驗證 Node.js 安裝。您應該會看到類似下面的輸出：
	```
	$ node -v
	v6.10.1
	
	$ npm -v
	3.10.10
	```
	{:screen}

## 產生用戶端憑證
我們將不會探究 x509 和公開金鑰基礎架構的細節，因為已經有大量的外部資源可以參考。只要說 Fabric 中的通訊流程會在每個接觸點使用簽署/驗證作業就夠了。因此，傳送呼叫（即交易）至網路的任何用戶端都需要簽署有效負載（私密金鑰），並附加適當簽署的 x509 憑證以供驗證（已簽章的憑證）。私密金鑰和已簽章的憑證，加上 MSP ID 和「憑證管理中心 (CA)」主要憑證，共同構成所謂的「使用者環境定義」物件。同樣地，不需要無關的詳細資料。我們只會與適當的「憑證管理中心」通訊，並擷取可構成物件的金鑰和憑證，而此程序就稱為「登記」。構成使用者環境定義物件之後，當您要「設定」或「取得」此使用者環境定義時，就會像從您的應用程式呼叫 API 一樣簡單。此時，應用程式（亦即，用戶端）已配備所有必要構件，並準備好可與網路通訊。我們要來看看擷取金鑰和憑證的兩種方法。

### 指令行
這是兩種方法中比較簡單的一種。首先，請遵循指示來建置 [Fabric CA 用戶端 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html)。此步驟可讓您與 CA 伺服器通訊，並收回適當格式化的憑證和金鑰。

接著，從[您的 {{site.data.keyword.Bluemix_notm}} ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) 下載 TLS 憑證，並將內容儲存至資料夾，例如 ``$HOME/tls``。此步驟可讓資料流在通訊連線上加密。

最後，從「網路監視器」中的**概觀**畫面，開啟**連線設定檔** JSON 檔案，並尋找相關的變數：
* CA 的 URL：``certificateAuthorities`` 下的 ``url``
* 管理使用者 ID：``enrollId``
* 管理密碼：``enrollSecret``
* CA 名稱：``caName``

透過 Fabric CA 用戶端，我們可以使用下列指令來傳入 TLS 憑證路徑及上述四個字串，以將 "enroll" 呼叫傳送至我們的「憑證管理中心」：
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

``fabric-ca-client`` 二進位檔放在 ``$GOPATH/bin`` 中，因此當您執行此指令時，需要位於本端機器上的適當位置。實際的呼叫可能類似下列指令範例：
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```

在 `$HOME/.fabric-ca-client/msp/signcerts/cert.pem` 中尋找您的管理憑證。然後，您可以從「網路監視器」將管理憑證上傳至區塊鏈網路。如需新增憑證的相關資訊，請參閱「網路監視器」中[「成員」畫面的「憑證」標籤](v10_dashboard.html#members)。

您也可以在下列目錄中找到 CA 主要憑證和管理私密金鑰：
* CA 主要憑證：`$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* 管理私密金鑰：`$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
有兩個 Hyperledger 儲存庫包含絕佳的資源和 Script，可讓您瞭解如何以程式設計的方式來與「憑證管理中心」互動。``fabric-samples`` 儲存庫包含「平衡轉移」範例，而 ``fabric-sdk-node`` 儲存庫有一系列的「CA 服務」測試。如果您想要在應用程式端發出登記要求，就必須完全瞭解需要在 ``fabric-ca-client`` 和 ``fabric-client`` 套件中公開的 API。請使用這些 Script 和儲存庫作為您建構應用程式的基準。

讓我們快速看一下「平衡轉移」範例中的幾個重要 Snippet：

首先，我們需要建立自己的用戶端物件，並設定要用來放置憑證和金鑰的金鑰/值儲存庫實例。我們可以利用簡單的 Factory 方法 (``newCryptoSuite``) 來執行此作業，它會從 ``BaseClient`` 擴充為 ``Client`` 類別。以下可快速看一下該程式碼：

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

一般作法是匯出您的機器上用來定義金鑰/值路徑的環境變數，並將它傳遞至上述函數。現在我們已經定義好 KVS，可以使用 ``FabricCAServices`` 類別中的幾個方法。此類別是 Fabric CA 用戶端的實作，因此它可以讓我們與 CA 伺服器通訊。首先，我們需要傳遞一些資訊到 CA 用戶端，即 CA URL：

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

接著，我們要將 "enroll" 呼叫實際傳送到 CA 伺服器。這樣會將包裝在 x509 憑證中，並由目標 CA 簽章的私密金鑰和公開金鑰傳回用戶端，我們將其稱之為 signcert 或登記憑證。這個登記憑證或 "eCert" 是重要組件，因為它可讓網路實體驗證來自用戶端的交易和呼叫：

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

而最後一項作業其實是設定加密套組，以及建置使用者環境定義：

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

然後，您可以從「網路監視器」將管理憑證上傳至區塊鏈網路。如需新增憑證的相關資訊，請參閱「網路監視器」中[「成員」畫面的「憑證」標籤](v10_dashboard.html#members)。

## 開發應用程式
{: #developing-applications}
您可以用 JavaScript 或 Java 來開發應用程式，並利用 Hyperledger Fabric Client SDK 中可用的 API 來啟用應用程式與網路之間的互動。應用程式至少需要包含下列資訊：
* 所要呼叫之鏈碼的名稱和版本。
* 網路資源的 API 端點資訊，包括排序節點、CA 和對等節點。
* 要在網路中查詢或更新分類帳的功能。如果您想要高可用性，則需要考量應用程式中的節點失效接手。

您可以在[範例應用程式](howto/sample_applications.html)中找到 {{site.data.keyword.Bluemix_short}} 上的**企業方案**範例應用程式。請使用這些範例鏈碼和應用程式作為範本，以建立您自己的商業解決方案。

## 將網路 API 端點新增至應用程式
您需要將網路資源的 API 端點新增至應用程式，這樣它才能夠與 {{site.data.keyword.Bluemix_short}} 上的 {{site.data.keyword.blockchain}} 網路互動。如果您在 {{site.data.keyword.Bluemix_short}} 上沒有 {{site.data.keyword.blockchain}} 網路，可以使用「入門範本方案」或「企業方案」來建立網路。如需相關資訊，請參閱[控管入門範本方案網路](get_start_starter_plan.html)和[控管企業方案網路](get_start.html)。

您可以在網路的連線設定檔中找到 API 端點。連線設定檔為 JSON 格式，並包含您的網路資源（包括排序節點、CA 及對等節點）的 API 端點資訊和 enrollID/secret。您的應用程式會透過這些 API 端點來與對等節點和其他網路資源互動。

1. 使用下列其中一種方法，從您的「網路監視器」擷取網路資源的 API 端點資訊：
	* 若要取得鏈碼特定的 API 端點資訊，請在該鏈碼執行所在的特定通道畫面上找出該鏈碼，然後按一下 **JSON** 按鈕。
	![每個鏈碼的 API 端點](images/channel_chaincode_detail.png "每個鏈碼的 API 端點")
	* 若要針對您的所有網路資源，取得一組完整的 API 端點資訊，請按一下「概觀」畫面中的**連線設定檔**按鈕。
	![網路監視器中的連線設定檔](images/service_credentials.png "網路監視器中的連線設定檔")

2. 尋找網路資源的 API 端點資訊，其類似下列範例：
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**附註**：如果您要將目標設為網路中的其他對等節點（例如，需要不屬於您的組織的對等節點背書時），則需要取得這些對等節點的正確 API 端點資訊。您也需要儲存其他組織的 CA 憑證，才能驗證傳回至應用程式的回應。此資訊未公開在您的連線設定檔中，因此，您必須聯絡「CLoud Foundry 組織」的適當管理者，並在頻外作業中取得此資訊。排序服務 URL 通用於整個網路；您不需要排序服務的任何成員特定資訊。

3. 將 API 端點資訊插入應用程式的配置檔中，如下列範例所示：
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## 管理應用程式
您可以在本端檔案系統上管理您的應用程式，也可以將它推送至 {{site.data.keyword.Bluemix_notm}}。若要將應用程式推送至 {{site.data.keyword.Bluemix_notm}}，請完成下列步驟：
1. 安裝 [Cloud Foundry 指令行安裝程式 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://github.com/cloudfoundry/cli/releases)。使用 `cf` 指令來測試您的安裝。
    * 如果安裝成功，您應該會在終端機中看到一堆文字輸出。
    * 如果您看到 "command not found"（找不到指令），則表示您的安裝不成功，或是未將 CF 新增至您的系統路徑。
2. 發出下列指令，以設定 API 端點，並使用您的 {{site.data.keyword.Bluemix_notm}} ID 和密碼登入：
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
3. 瀏覽至應用程式的目錄，然後發出下列指令來推送應用程式。根據您的應用程式大小，這可能需要幾分鐘的時間。您會在終端機中看到 {{site.data.keyword.Bluemix_notm}} 產生的日誌；一旦順利啟動應用程式，日誌隨即停止。
	```
	> cf push YOUR_APP_NAME_HERE
	```
	您可以發出下列其中一個指令來查看應用程式日誌：
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## 中斷應用程式的網路連線
{: #disconnect}
完成下列步驟，以移除應用程式與 {{site.data.keyword.Bluemix_short}} 上的 {{site.data.keyword.blockchain}} 網路之間的連線。
1. 從應用程式配置檔中移除 API 端點資訊。如需參考資訊，請參閱[將網路 API 端點新增至應用程式](#adding-network-api-endpoints-to-your-application)。
2. 刪除鏈碼容器。
	1. 在「網路監視器」的「通道」畫面中，找出安裝鏈碼的通道。
	2. 在特定通道畫面中，找出您要停用的鏈碼。
	3. 按一下**刪除**按鈕，然後在鏈碼刪除畫面中按一下**提交**。您的鏈碼容器將會移除。
	![刪除鏈碼](images/channel_chaincode_detail.png "刪除鏈碼")

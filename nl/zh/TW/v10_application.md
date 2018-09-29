---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 開發應用程式
{: #dev_app}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


「{{site.data.keyword.blockchainfull}} 平台」提供您可以用來將應用程式連接至區塊鏈網路的 API。您可以使用連線設定檔中的網路 API 端點來呼叫鏈碼，以及更新或查詢對等節點上的頻道特定分類帳。您也可以使用 [Swagger 使用者介面](howto/swagger_apis.html)中的 API，來管理網路的節點、頻道及成員。
{:shortdesc}

您可以使用本指導教學，學習如何存取「{{site.data.keyword.blockchainfull_notm}} 平台」API，並使用它們來向網路登記及登錄應用程式。您也將學習如何與網路互動，以及從應用程式發出交易。本指導教學是以 Hyperledger Fabric 文件中的[撰寫第一個應用程式 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) 指導教學為基礎。您將使用**撰寫第一個應用程式**指導教學中許多相同的檔案及指令，但將使用它們來與「{{site.data.keyword.blockchainfull_notm}} 平台」上的網路互動。本指導教學說明使用 Hyperledger Fabric Node SDK 開發應用程式的每一個步驟。您還將學習如何使用「Fabric CA 用戶端」，作為使用 SDK 的替代方案，來登記及登錄使用者。

當您建立自己的商業解決方案時，除了本指導教學外，您還可以使用「{{site.data.keyword.blockchainfull}}平台」提供作為範本的範例應用程式及鏈碼。如需相關資訊，請參閱[部署範例應用程式](howto/prebuilt_samples.html)。

## 必要條件
您需要下列必要條件，才能在「{{site.data.keyword.blockchainfull_notm}} 平台」上使用**撰寫第一個應用程式**指導教學。

- 如果您在 {{site.data.keyword.cloud_notm}} 上沒有區塊鏈網路，則需要使用「入門範本方案」或「企業成員資格方案」來建立一個。如需相關資訊，請參閱[建立入門範本方案網路](get_start_starter_plan.html#creating-a-network)或[建立企業方案網路](get_start.html#creating-a-network)。

  在您進入網路的「網路監視器」之後，請在「概觀」畫面上至少為您的組織新增一個對等節點。然後，在您的網路中至少建立一個頻道。如需相關資訊，請參閱[建立頻道](howto/create_channel.html#creating-a-channel)。**請注意**，如果您使用「入門範本方案」網路，則您的網路已有一個頻道，其名稱為 `defaultchannel`，您可以用來部署鏈碼。

- 安裝必要的工具，以下載 Hyperledger Fabric 範例，並使用 Node SDK。
  * [Curl ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") 或 [Git ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- 下載 `fabric-samples` 目錄，來安裝 Hyperledger Fabric 範例。您可以遵循 Hyperledger Fabric 文件中的[入門指南 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html)。

  在本端機器下載 `fabric-samples` 目錄之後，請導覽至 `fabric-samples/fabcar`。您可以製作這個目錄的副本，並將它重新命名，以便您可以在全新的目錄中，嘗試並測試範例應用程式。

  在 `fabcar` 目錄中，執行 `npm install` 指令來安裝必要套件，以使用 Fabric SDK，其中包含 `fabric-client` 及 `fabric-ca-client`。

- 使用[網路監視器](howto/install_instantiate_chaincode.html#installchaincode)，在頻道上安裝及實例化 fabchar 鏈碼。您可以在 `fabric-samples` 資料夾的 `fabric-samples > chaincode > fabcar > go` 下找到 fabcar 鏈碼。

- 在「網路監視器」的「概觀」畫面上，擷取網路的「連線設定檔」。將「連線設定檔」儲存至 `fabcar` 目錄，並將它重新命名為 `creds.json`。

## 使用 Fabric SDK
{: #using-the-fabric-sdks}

Hyperledger Fabric SDK 提供一組功能強大的 API，可讓應用程式與區塊鏈網路互動。您可以在 [Hyperledger Fabric SDK 社群文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 社群文件"){:new_window} 中，找到所支援語言的最新清單。建議使用 Node SDK 或 Java SDK 與「{{site.data.keyword.blockchainfull_notm}} 平台」搭配。您可以進一步瞭解 SDK 在 SDK 個別儲存庫中提供的 API。

本指導教學使用 [Node SDK ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Node SDK"){:new_window}，來登錄並登記您的應用程式，然後使用應用程式，藉由呼叫並查詢鏈碼來發出交易。本指導教學說明您需要提供給 SDK 的資訊，以便應用程式可以連接至區塊鏈網路。它也會介紹一些您可以使用的 API，以及 SDK 如何與區塊鏈網路互動及如何將交易提交給區塊鏈網路。

## 將網路 API 端點新增至應用程式
{: #api-endpoints}

您需要在 {{site.data.keyword.cloud_notm}} 的區塊鏈網路中，利用特定網路資源的 API 端點（包括排序節點、CA 及對等節點）來提供應用程式。您的應用程式可以透過這些 API 端點與網路互動。您可以在網路的「連線設定檔」中找到 API 端點。「連線設定檔」採用 JSON 格式，且包含網路資源的 API 端點資訊及登記 ID/密碼。

1. 使用下列其中一種方法，從您的「網路監視器」擷取網路資源的 API 端點資訊：
  * 在「概觀」畫面上，按一下**連線設定檔**。「連線設定檔」包含所有網路資源的完整 API 端點資訊集。
    ![網路監視器中的連線設定檔](images/service_credentials.png "網路監視器中的連線設定檔")

  * 如果您在網路中具有執行中的鏈碼，則可以取得鏈碼特定的 API 端點資訊。在「頻道」畫面上，按一下鏈碼執行所在的頻道列來開啟特定的頻道畫面。然後，找出鏈碼並按一下 **JSON** 按鈕。
    ![每個鏈碼的 API 端點](images/channel_chaincode.png "每個鏈碼的 API 端點")

2. 尋找網路資源的 API 端點資訊，其類似於下列範例中 `peer1-org1` 列的 URL：
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **附註**：您可能想要利用應用程式，將組織外的網路資源設為目標。比方說，如果鏈碼[背書原則](howto/install_instantiate_chaincode.html#specifying-chaincode-endorsement-policies)需要來自頻道上其他組織的背書，則您需要取得其對等節點的端點資訊，以及隨附的 TLS 憑證。您可以在「連線設定檔」的對等節點區段中找到此資訊。不過，您需要聯絡其他組織的管理者，以瞭解他們已將哪些對等節點加入至特定頻道。

3. 將 API 端點資訊插入應用程式的配置檔中，如下列範例所示：
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  您也可以將 [HEAD 要求](howto/monitor_network.html#monitor-nodes)傳送至這些端點，以檢查網路資源的可用性。

  如果您是使用 Fabric SDK，則也可以使用「連線設定檔」來連接至您的網路。本指導教學會手動將網路的端點資訊提供給 SDK。不過，您可以在後面的章節中，找到[使用連線設定檔與 SDK 搭配](#using-your-connection-profile-with-the-sdk)的指導教學及指引。

## 登記應用程式
{: #enroll-app}

在您將應用程式連接至「{{site.data.keyword.blockchainfull_notm}} 平台」上的網路之前，您需要向網路證明應用程式的確實性。我們將不會探究 x509 和公開金鑰基礎架構的細節，因為您可以為此找到大量的[外部資源 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window}。簡言之，Fabric 中的通訊流程會在每個接觸點使用簽署/驗證作業。因此，任何將呼叫（例如，分類帳查詢或更新）傳送至網路的應用程式都需要使用其私密金鑰簽署有效負載，並附加適當簽署的 x509 憑證以進行驗證。**登記**是從適當的「憑證管理中心」產生必要金鑰及憑證的處理程序。登記之後，您的應用程式已準備好與網路進行通訊。

本節說明如何使用 Fabric SDK，作為**撰寫第一個應用程式**指導教學的一部分，來擷取金鑰及憑證。您也可以學習如何從指令行中使用「Fabric CA 用戶端」來登記應用程式。

<!-- this last sentance when the certificates article goes live --->

### 使用 Fabric SDK 登記
{: #enroll-app-sdk}

從 `fabric-samples` 資料夾的 `fabcar` 目錄中，於文字編輯器中開啟 `enrollAdmin.js` 檔。

1. 該檔案首先建立「Fabric 用戶端」的實例。
  ```
  var fabric_client = new Fabric_Client();
  ```

2. 然後，該檔案會建立金鑰值儲存庫 (KVS) 來管理您的憑證。SDK 會使用 [KeyValueStore ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} 類別，來建立金鑰值儲存庫，以及使用 [CryptoSuite ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} 類別，來執行加密計算。您可以查看下面相關的程式碼區塊。
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. 在定義 KVS 之後，您可以從 [Fabric 用戶端 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Client.html "Fabric 用戶端") 類別及 Fabric-CA-Client API <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> 使用一些方法，來與「CA 伺服器」通訊。您需要將「憑證管理中心」的名稱和 URL 提供給 SDK。從「網路監視器」的**概觀**畫面中，開啟**連線設定檔** JSON 檔案，並在 `certificateAuthorites` 區段下尋找下列變數：
  * CA 的 URL：``certificateAuthorities`` 下的 ``url``
  * 管理使用者 ID：``enrollId``
  * 管理密碼：``enrollSecret``
  * CA 名稱：`caName`

  以下列方式使用此資訊，**編輯** `enrollAdmin.js` 檔中的相關行：
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  例如：
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```
  {:codeblock}

  然後，「Fabric 用戶端」會檢查是否已登記您的應用程式。使用來自連線設定檔的 `enrollID` **編輯**下行：
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. 您需要將「登記」呼叫傳送至「CA伺服器」。已向您的網路登錄您的 `admin`。登記呼叫會擷取包裝在 x509 憑證中且由目標 CA 簽署的公開金鑰及私密金鑰。此包裝且簽署的憑證稱為 signCert。signCert 可讓網路成員驗證源自用戶端的呼叫。您需要從認證檔提供組織名稱、密碼及 MSP ID。在網路認證的 `certificateAuthorities` 區段中，尋找 `enrollID`、`enrollSecret` 和 `x-mspid`。使用那些值**編輯**下面的程式碼區塊，並取代您檔案的相關區段。
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. 儲存 `enrollAdmin.js` 檔。

在 `fabcar` 目錄中，發出下列指令來登記管理者：
```
node enrollAdmin.js
```
{:codeblock}

此登記指令會產生 signCert，並將它匯出至名稱為 `hfc-key-store` 的資料夾。本指導教學中的未來檔案將會在此資料夾中尋找您的憑證。如果您可以在 `hfc-key-store` 資料夾中找到管理者憑證，表示登記指令運作正常。

如果想要[使用 SDK 操作您的網路](#operate-sdk)，您需要將管理者 signCert 上傳至「{{site.data.keyword.blockchainfull}} 平台」。您可以在 `hfc-key-store` 資料夾中找到管理者 signCert。開啟 `admin` 檔，並將引號內的憑證複製在 `certificate` 欄位後面。使用工具或文字編輯器，將憑證轉換為 PEM 格式。然後，您可以從「網路監視器」將管理憑證上傳至區塊鏈網路。如需新增憑證的相關資訊，請參閱「網路監視器」中[「成員」畫面的「憑證」標籤](v10_dashboard.html#members)。如果您只是使用 SDK 來呼叫或查詢鏈碼，則不需要執行此作業。

<!-- you can eliminate this section when the certificates article goes live --->

### 使用 Fabric CA 用戶端登記
{: #enroll-app-caclient}

1. 將 [fabric-ca inaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) 下載至本端機器，並解壓縮它們。將可執行的 `fabric-ca` 用戶端從其下載所在的目錄，移至您可以在其中使用它來執行指令的目錄。

2. 根據您所使用的服務方案、位置及叢集，從 {{site.data.keyword.cloud_notm}} 下載 TLS 憑證。
  - 入門範本方案的主要 TLS 憑證  
    - 美國：[us01.blockchain.ibm.com.cert ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert")；[us02.blockchain.ibm.com.cert ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - 英國：[uk01.blockchain.ibm.com.cert ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert")；[uk02.blockchain.ibm.com.cert ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - 雪梨：[aus01.blockchain.ibm.com.cert ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")；[aus02.blockchain.ibm.com.cert ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [企業方案的主要 TLS 憑證 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  將內容儲存至資料夾，例如 ``$HOME/tls``。此步驟可讓資料流在通訊連線上加密。

3. 從「網路監視器」的**概觀**畫面中，開啟**連線設定檔** JSON 檔案，並尋找下列變數：
  * CA 的 URL：``certificateAuthorities`` 下的 ``url``
  * 管理使用者 ID：``enrollId``
  * 管理密碼：``enrollSecret``
  * CA 名稱：``caName``

4. 您可以使用 Fabric CA 用戶端，搭配下列指令來傳入 TLS 憑證路徑及上述四個字串，以將 `enroll` 呼叫傳送至「憑證管理中心」：
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  您需要在將「Fabric CA 用戶端」移至其中的目錄中執行此指令。實際的呼叫可能類似下列指令範例：
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. 在 `$HOME/.fabric-ca-client/msp/signcerts/cert.pem` 中尋找您的管理憑證。然後，您可以從「網路監視器」將管理憑證上傳至區塊鏈網路。如需新增憑證的相關資訊，請參閱「網路監視器」中[「成員」畫面的「憑證」標籤](v10_dashboard.html#members)。

  您也可以在下列目錄中找到 CA 主要憑證和管理私密金鑰：
  * CA 主要憑證：`$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * 管理私密金鑰：`$HOME/.fabric-ca-client/msp/keystore/<>_sk file`


## 登錄應用程式
{: #register-app}

在產生用戶端憑證之後，您需要向網路「憑證管理中心」登錄應用程式。登錄會將您的應用程式新增至網路可以辨識的元件清單。最佳作法是將您的應用程式登錄為個別身分，而非使用 `admin` 來簽署要求。

### 使用 SDK 登錄
{: #register-app-sdk}

您可以使用 `registerUser.js` 檔，利用 `admin` signCert，將應用程式登錄並登記為 `user1`。在文字編輯器中開啟 `registerUser.js`。

1. 將 CA URL 及名稱提供給新的「Fabric CA 用戶端」實例。

2. 從 [Fabric 用戶端 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Client.html) 將 enrollID 提供給 `getUserContext` 方法，來檢查是否已登記 `admin`，並容許發出此要求。根據下列範例**編輯**您檔案中的相關程式碼區塊：
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. 使用 **Fabric CA 用戶端**向 CA 登錄並登記使用者，然後使用 **Fabric 用戶端** 來建立新的 signCert。使用您的 MSP ID 及 組織連結來**編輯**下列區塊。您可以在網路認證的憑證管理中心區段中找到 `x-affiliations`，並使用任何列出的連結。新增您要建立的使用者名稱。依預設，fabcar 範例會使用 `user1`。
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. 儲存 `registerUser.js` 檔。

執行 `node registerUser.js` 指令來登錄並登記 `user1`。如果您可以在 `hfc-key-store` 資料夾中找到 `user1` 憑證，表示此指令運作正常。您只能登錄一次身分。如果您遇到問題，請嘗試以新的使用者名稱來執行 `registerUser.js`。

<!-- you can eliminate this section when the certificates article goes live --->

### 使用 Fabric CA 用戶端登錄
{: #register-app-caclient}

1. 發出下列指令，以在您的區塊鏈網路中尋找您的連結與組織名稱。
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  您應該會看到類似下列範例的資訊：
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  請記下第二個**連結**值，例如 `ibp.PeerOrg1`。您需要在下面的指令中使用此值。

2. 執行下列指令來登錄對等節點。
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  指定對等節點的名稱和密碼，並將 `name` 和 `password` 取代為對等節點名稱和密碼。記下此資訊。當您配置對等節點時，需要它。例如：
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  您只能登錄一次身分。如果您遇到問題，請嘗試使用新的使用者名稱和密碼來嘗試指令。

  當指令順利完成時，您應該會看到類似下列範例的資訊：
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

### 使用網路監視器登錄

或者，您可以使用「網路監視器」的**憑證管理中心**標籤，來登錄並登記用戶端應用程式。如需相關指示，請參閱此[資訊](v10_dashboard.html#ca)。

## 透過呼叫並查詢鏈碼來發出交易
{: #invoke-query}

您的應用程式需要與完整區塊鏈網路互動，才能提交交易。

1. 應用程式會傳送要由頻道上對等節點背書的交易提案。
2. 背書對等節點會將背書的交易傳回至應用程式。
3. 應用程式會將背書的交易傳送至排序服務，以將交易新增至分類帳。

如需完整交易流程的相關資訊，請參閱 Hyperledger Fabric 文件中的[交易流程 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "交易流程"){:new_window}。開始使用本指導教學之後，請造訪[應用程式連線功能及可用性](#app-connectivity-availability)小節，以取得管理 SDK 如何與網路互動的提示。

下列範例示範 Node SDK 如何設定網路拓蹼、定義交易提案，然後將交易提交至網路。您可以使用 `invoke.js` 檔，在 `fabcar` 鏈碼內呼叫函數。這些函數可讓您在區塊鏈分類帳上建立及轉移資產。本指導教學使用 `initLedger` 函數將新資料新增至您的頻道，然後使用 `query.js` 檔來查詢資料。

### 呼叫鏈碼
{: #invoke}

在文字編輯器中開啟 `invoke.js` 檔。

1. 將 `var creds = require('./creds.json')` 新增至檔案的頂端。此程式碼行容許 `invoke.js` 檔從 `creds.json` 認證檔讀取資訊。

2. 使用 [Fabric 用戶端 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Client.html "Fabric 用戶端"){:new_window} 類別，利用網路資源的 API 端點來設定光纖網路。此步驟定義您的用戶端將提案提交至其中的頻道和對等節點，以及 SDK 接著將背書的交易傳送至其中的排序服務。**編輯**下面的相關程式碼區塊。`creds.peers["org1-peer1"].url` 一行會從您的認證檔匯入對等節點 URL。
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  當您將對等節點 URL 新增至 `fabric_client.newPeer`方法時，也會使用下面的程式碼 Snippet，從連線設定檔匯入相關的 TLS 憑證。先前您新增排序服務 URL 時也做了相同動作。您需要使用這些 TLS 憑證來鑑別與網路的通訊。
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  如果背書原則需要頻道中的其他組織背書交易，則在設定網路時，您將需要使用 `newPeer()` 及 `channel.addPeer()` 方法，來新增那些組織對等節點。這些組織需要將已加入至特定頻道之對等節點的清單傳送給您。連線設定檔中將提供端點資訊及 TLS 憑證。SDK 會將交易傳送至已新增至頻道的所有對等節點。

  您也可以新增屬於組織且已加入至頻道的其他對等節點，作為[使應用程式高度可用](#ha-app)的步驟。若您的其中一個對等節點中斷，這可讓 SDK 進行失效接手。

3. 在設定光纖網路，並從登錄步驟中匯入應用程式身分和 signCert 之後，`invoke.js` 檔會定義您將提交至網路的提案。您可以使用 `fabcar` 鏈碼中的 `initLedger` 函數，將部分起始資料新增至分類帳。您也可以編輯程式碼區塊，以呼叫可在 `fabcar` 鏈碼中找到的其他函數。
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  在定義要求之後，您可以將 [交易提案 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") 傳送至頻道上的對等節點。從對等節點傳回提案之後，您可以[將交易傳送至 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") 排序服務。

4. 您可以新增事件服務，使交易流程更有效率。**編輯**下列區段：
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  雖然範例使用對等節點型事件服務，但是您應該使用頻道型接聽器。如需進一步瞭解，請參閱[管理交易](#managing-transactions)小節及 [Node SDK 文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "頻道型事件服務"){:new_window}。

5. 依預設，`invoke.js` 會以 `user1` 身分提交交易。如果您已登錄不同的名稱，則可以編輯 `invoke.js` 檔。

完成編輯檔案之後，請發出 `node invoke.js` 指令，將交易提交至網路。您應該會看到下列訊息，確認呼叫順利完成。
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

這表示您的應用程式已順利呼叫鏈碼，並將資料新增至分類帳。

### 查詢鏈碼
{: #query}

現在，您可以使用 `query.js` 來讀取分類帳。在文字編輯器中開啟 `query.js` 檔。

1. 將 `var creds = require('./creds.json')` 新增至檔案的頂端。
2. 利用頻道名稱及對等節點的端點資訊來更新此檔案。因為此作業只會讀取對等節點上儲存的資料，所以您不需要新增排序服務的端點資訊。`query.js` 也假設您以 `user1` 身分傳送提案。
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

完成編輯檔案之後，請發出 `node query.js` 指令，且您應該在分類帳上看到汽車清單，類似下列範例。
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

如需 fabcar 應用程式及其使用之函數的相關資訊，您可以造訪 Hyperledger Fabric 文件中的[撰寫第一個應用程式 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "撰寫第一個應用程式"){:new_window} 的完整指導教學。

## 使用您的連線設定檔與 SDK 搭配
{: #using-your-connection-profile-with-the-sdk}

您可以從「網路監視器」的**概觀**畫面使用**連線設定檔**，讓 SDK 連接至網路，而非手動匯入網路的端點資訊。這簡化了連接至「憑證管理中心」以進行登記及登錄的處理程序。在提交交易之前，也不需要定義您的光纖網路。SDK 將直接從「連線設定檔」中尋找相關頻道上的對等節點及排序節點。您可以在 [Node SDK 文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-network-config.html "連線設定檔指導教學"){:new_window} 中尋找如何使用「連線設定檔」的相關資訊。

讓我們使用 `invoke.js` 檔作為範例，以瞭解使用「連線設定檔」而非手動端點的效率。您可以使用 `loadFromConfig` 類別，來建立新的 Fabric 用戶端實例。將 `var fabric_client = new Fabric_Client();` 取代為下列程式碼。
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

您可以使用下列來建立新頻道，而非建立對等節點及排序節點，然後將它們新增至頻道來設定光纖網路。

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

然後，SDK 會使用「連線設定檔」，新增在頻道上定義的對等節點及排序服務。這可讓您更有效率撰寫應用程式，並可讓您在網路成員加入、離開及啟動新頻道時更輕鬆地更新應用程式。請參閱「節點 SDK 文件」中的[連線設定檔指導教學 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-network-config.html "連線設定檔指導教學"){:new_window}，來瞭解其他涉及的步驟。您可以使用這個 [fabcar 指導教學版本 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window}，其使用連線設定檔，而不是手動端點連線。

您可以編輯「連線設定檔」，將交易傳送至組織外的對等節點，以進行背書。「連線設定檔」已包含來自「{{site.data.keyword.blockchainfull}} 平台」網路上其他組織之對等節點的端點資訊及 TLS 憑證。在設定檔的 `channels` 區段中，將對等節點的名稱新增至相關頻道，以將對等節點新增至該頻道。

## 應用程式連線功能及可用性
{: #app-connectivity-availability}

Hyperledger Fabric [交易流程 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "交易流程"){:new_window} 跨越多個元件，用戶端應用程式會扮演唯一角色。SDK 會將交易提案提交至對等節點進行背書。然後，它會收集要傳送至排序服務的背書提案，然後將要新增至頻道分類帳的交易區塊傳送至對等節點。應該準備正式作業應用程式的開發人員，來管理 SDK 與其網路之間的互動，以提高效率與可用性。

### 管理交易
{: #managing-transactions}

應用程式用戶端應該確定已驗證其交易提案，且提案順利完成。提案延遲或遺失可能有多個原因，例如網路中斷或元件故障。您應該準備[高可用性](#ha-app)的應用程式來處理元件故障。您也可以在應用程式中[增加逾時值](#set-timeout-in-sdk)，以防止提案在網路可以回應之前逾時。

如果鏈碼未在執行中，則第一個傳送至這個鏈碼的交易提案將啟動鏈碼。當鏈碼啟動時，所有其他提議都會遭到拒絕，錯誤指出鏈碼目前正在啟動。這與交易失效不同。如果在鏈碼啟動時拒絕任何提案，則在鏈碼開始之後，應用程式用戶端必須再次傳送拒絕的提議。應用程式用戶端可以使用訊息佇列來避免失去交易提案。

您可以使用頻道型事件服務來監視交易及建置訊息佇列。應該使用頻道型接聽器，而不是對等節點型服務，因為其可擴充至多個頻道，並區分不同頻道的資料流量。[channelEventHub ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} 類別可以根據交易、區塊及鏈碼事件來登錄接聽器。您可以使用「Node SDK 文件」，學習如何設定[頻道型事件服務 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "頻道型事件服務"){:new_window}。

### 關閉網路連線

如果在提交交易提案之前將對等節點及排序節點新增至 SDK，則您會開啟與網路元件的 grpc 串流連線。例如，指令
```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
將開啟與 `org1-peer1` 的連線。如果您有持續執行中的應用程式，當它們不需要使用 `peer.close()` 和 `order.close()` 指令來釋放資源並防止效能降低時，您應該關閉連線。您可以在 Node SDK 文件的[對等節點](https://fabric-sdk-node.github.io/Peer.html#close__anchor)及[排序節點](https://fabric-sdk-node.github.io/Orderer.html#close__anchor)類別中找到詳細資料。

### 高度可用的應用程式
{: #ha-app}

作為高可用性最佳作法，強烈建議每個組織最少部署兩個對等節點，以進行失效接手。您也需要針對高可用性調整應用程式。在兩個對等節點上安裝鏈碼，並將它們新增至您的頻道。然後，在設定網路和建置對等節點目標清單時，準備好[將交易提案提交至](#invoke)這兩個對等端點。如果您有「企業方案」網路，也可以將另一個排序節點新增至您的網路。如果您是使用[連線設定檔](#using-your-connection-profile-with-the-sdk)，而非手動新增網路端點，請確保您的設定檔是最新的，並已將其他對等節點及排序節點新增至設定檔的 `channels` 區段中的相關頻道。然後，SDK 將使用「連線設定檔」新增在頻道上加入的元件。

## 啟用相互 TLS
{: #mutual-tls}

如果您執行的是 Fabric 1.1 版層次的「企業方案」網路，則可以選擇為您的應用程式[啟用相互 TLS](v10_dashboard.html#network-preferences)。如果您啟用相互 TLS，則需要更新應用程式，才能支援此功能。否則，您的應用程式無法與您的網路通訊。

在「連線設定檔」中，找出 `certificateAuthorities` 區段，您可以在其中找到登記及取得憑證以使用相互 TLS 與網路通訊所需的下列屬性。

- `url`：用於連接至可用盡相互 TLS 憑證之 CA 的 URL
- `enrollId`：用於取得憑證的登記 ID
- `enrollSecret`：用於取得憑證的登記密碼
- `x-tlsCAName`：用於取得憑證的 CA 名稱，而憑證容許應用程式使用「相互 TLS」進行通訊。

如需更新應用程式以支援相互 TLS 的相關資訊，請參閱[如何配置相互 TLS ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "相互 tls"){:new_window}。

## （選用）使用 SDK 操作您的網路
{: #operate-sdk}

您也可以使用 SDK 來操作區塊鏈網路。本指導教學說明如何使用 SDK 將對等節點加入至頻道、在對等節點上安裝鏈碼，以及在頻道上實例化鏈碼。這些是選用步驟，因為如果所有對等節點都在「{{site.data.keyword.blockchainfull}} 平台」上執行，您也可以使用「網路監視器」或 [Swagger 使用者介面](howto/swagger_apis.html)中的 API 來執行這些作業。

您需要將管理者 signCert 上傳至「{{site.data.keyword.blockchainfull}} 平台」，才能完成這些步驟。您可以在[登記一節](#enroll-app-sdk)的結尾找到如何上傳 signCert 的指示

### 加入頻道
{: #join-channel-sdk}

使用「網路監視器」或 API 建立頻道之後，您可以使用 SDK 將對等節點加入至頻道。

1. 從排序服務中[提取頻道的初始區塊 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "提取初始區塊"){:new_window}。
2. 將初始區塊傳遞至[加入頻道 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} 方法，以將您的對等節點加入至頻道。

若要使用 `fabcar` 範例來加入頻道，請使用 `invoke.js` 檔作為起始點。您需要以管理者而不是應用程式的身分傳送此要求，因此在 `getUserContext` 方法中，將 `user1` 取代為 `admin`。從您在 `var request = {` 定義鏈碼呼叫要求的位置開始，根據下面來自 [Node SDK 文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "相互 tls"){:new_window} 的程式碼 Snippet，將交易流程取代為頻道加入要求。
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```
### 安裝鏈碼
{: #install-cc-sdk}

您可以從 [Fabric 用戶端 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Client.html "Fabric 用戶端"){:new_window} 類別使用 [安裝鏈碼 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} 方法，在對等節點上安裝鏈碼。

若要使用 `fabcar` 範例，在對等節點上安裝 `fabcar` 鏈碼，請使用 `query.js` 檔作為基準線並編輯它。您需要以管理者而不是應用程式的身分傳送此要求，因此在 `getUserContext` 方法中，將 `user1` 取代為 `admin`。使用下列範例，將交易提案物件取代為安裝鏈碼要求：
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

將此物件傳送至 `return fabric_client.installChaincode(request);`，而非目前位於檔案的 `return channel.queryByChaincode(request);` 一行。

### 實例化鏈碼
{: #instantiate-cc-sdk}

若要實例化鏈碼，您需要將[實例化提案 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} 傳送至對等節點，然後將 [交易要求 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} 傳送至排序服務。

若要使用 `fabcar` 範例來實例化鏈碼，請使用 `invoke.js` 檔作為起始點。您需要以管理者而不是應用程式的身分傳送此要求，因此在 `getUserContext` 方法中，將 `user1` 取代為 `admin`。使用下列範例，將交易提案物件取代為安裝鏈碼要求：
```
var request = {
		targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
		channelNames: 'mychannel'
		};

```
{:codeblock}

將此要求傳送至 `return channel.sendInstantiateProposal(request);`，而非目前位於檔案的 `return channel.sendTransactionProposal(request);`。將實例化要求傳送至頻道之後，您必須將背書提案作為交易傳送至排序服務。這會使用與傳送交易相同的方法，因此您可以讓檔案的其餘部分保持不變。您可能想要在實例化提案中[增加逾時值](#set-timeout-in-sdk)。否則，要求可能會在平台可以啟動鏈碼容器之前逾時。

## （選用）在 Fabric SDK 中設定逾時值
{: #set-timeout-in-sdk}

Fabric SDK 會在用戶端應用程式中設定區塊鏈網路中事件的預設逾時值。請參閱下列範例中關於 Fabric Java SDK 中的預設逾時設定。檔案路徑是 `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`。

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";
    
    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

不過，您可能需要變更您自己的應用程式中的預設逾時值。例如，應用程式呼叫的交易需要超過 5000 毫秒（即事件中心連線的預設逾時值）的回應時間時，您可能會收到失敗的錯誤，因為呼叫事件會在交易完成之前結束於 5000 毫秒。您可以設定系統內容，以改寫用戶端應用程式中的預設值。因為預設值會在您設定系統內容之前起始設定，所以系統內容可能不會生效。因此，您需要在用戶端應用程式的靜態結構中設定逾時的系統內容。請參閱下列範例，以將 Fabric Java SDK 中的事件中心連線逾時值變更為 15000 毫秒。檔案路徑是 `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`。

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

如果您是使用 Node SDK，則可以直接在呼叫的方法中指定逾時值。舉例來說，您可以使用下行，將 [實例化鏈碼 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") 的逾時值增加至 5 分鐘。
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## （選用）使用 CouchDB 索引
{: #couchdb-indices}

如果您的網路使用 CouchDB 並想要利用 CouchDB 檢索功能來改善 CouchDB 的效能，您需要使用鏈碼來包裝索引。

如需 CouchDB 及如何設定索引的相關資訊，請參閱 Hyperledger Fabric 文件中的 [CouchDB 即狀態資料庫 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB 即狀態資料庫"){:new_window}。您也可以在此 [Fabric 指導教學 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window} 中找到使用索引與鏈碼搭配的範例。

<!--# Next steps-->

## 管理應用程式
{: #host-app}

您可以在本端檔案系統上管理您的應用程式，也可以將它推送至 {{site.data.keyword.Bluemix_notm}}。若要將應用程式推送至 {{site.data.keyword.Bluemix_notm}}，請完成下列步驟：
1. 安裝 [Cloud Foundry 指令行安裝程式 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://github.com/cloudfoundry/cli/releases)。使用 `cf` 指令來測試您的安裝。
    * 如果安裝成功，您應該會在終端機中看到一堆文字輸出。
    * 如果您看到 "command not found"（找不到指令），則表示您的安裝不成功，或是未將 CF 新增至您的系統路徑。
2. 發出下列指令，以設定 API 端點，並使用您的 {{site.data.keyword.Bluemix_notm}} ID 和密碼登入：
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. 瀏覽至應用程式的目錄，然後發出下列指令來推送應用程式。根據您的應用程式大小，這可能需要幾分鐘的時間。您可以在終端機中查看來自 {{site.data.keyword.Bluemix_notm}} 的日誌。當應用程式順利啟動時，日誌就會停止。
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	您可以發出下列其中一個指令來查看應用程式日誌：
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`

## 中斷應用程式的網路連線
{: #disconnect-app}

完成下列步驟，以移除應用程式與 {{site.data.keyword.cloud_notm}} 上的區塊鏈網路。
1. 從應用程式配置檔中移除 API 端點資訊。如需參考資訊，請參閱[將網路 API 端點新增至應用程式](#api-endpoints)。
2. 刪除鏈碼容器。
  1. 在「網路監視器」的「頻道」畫面中，找出安裝鏈碼的頻道。
  2. 在特定頻道畫面中，找出您要停用的鏈碼。
  3. 按一下**刪除**按鈕，然後在鏈碼刪除畫面中按一下**提交**。您的鏈碼容器將會移除。
	![刪除鏈碼](images/channel_chaincode.png "刪除鏈碼")

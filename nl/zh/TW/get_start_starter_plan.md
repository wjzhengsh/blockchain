---

copyright:
  years: 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 控管入門範本方案網路
{: #getting-started-with-starter-plan}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


只要按一下，「{{site.data.keyword.blockchainfull}} 平台入門範本方案」即可提供您預先配置的區塊鏈網路。它會<!--offers you a free trial of 30 days and -->佈建具有許可權的網路，其預設配置包含兩個[組織](glossary.html#organization)（每個組織各一個[對等節點](glossary.html#peer)），以及一個[頻道](glossary.html#channel)。建立網路之後，您可以擴充並新增更多組織和對等節點到您的網路。<!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**附註**：「{{site.data.keyword.blockchainfull}} 平台入門範本方案」是開發和測試環境，不適用於正式作業工作負載。如果您需要正式作業環境，請參閱[關於企業方案](enterprise_plan.html)。

「入門範本方案」可讓您學習及培養「{{site.data.keyword.blockchainfull_notm}} 平台」的技能、執行範例應用程式、測試自己的應用程式，以及模擬多組織情境。本入門指導教學介紹所需遵循的必要條件和步驟，以建立及使用「入門範本方案」網路。

如果您是第一次使用「{{site.data.keyword.blockchainfull_notm}} 平台」和區塊鏈，可以閱讀[名詞解釋](glossary.html)來熟悉本文件和 [Hyperledger Fabric 文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/blockchain.html) 中的術語，以進一步瞭解區塊鏈。


## 建立網路
建立「{{site.data.keyword.blockchainfull_notm}} 平台」服務實例之後，即可取得具有預設配置的「入門範本方案」[網路](glossary.html#network)。

1. 在「{{site.data.keyword.cloud_notm}} 型錄」中尋找[區塊鏈服務 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain)。   
    **附註**：您需要使用您的 {{site.data.keyword.cloud_notm}} 付費帳戶登入。如果您沒有帳戶，請按一下**註冊以建立**按鈕。建立免費試用帳戶之後，請將它升級為**隨收隨付制**類型，方法是移至 {{site.data.keyword.cloud_notm}} 主控台中的**管理** > **計費及用量** > **計費**，然後按一下**新增信用卡**。
2. 選擇 {{site.data.keyword.cloud_notm}} 中的地區，以建立網路。
3. 選取您的 Cloud Foundry 組織和空間，以建立網路。
4. 從定價方案表格中選擇**入門範本成員資格方案**。
5. 按一下**建立**按鈕。請注意，如果您是受邀加入網路，您會看到蹦現的歡迎使用畫面。若要建立網路，請選擇**繼續您的網路**，然後按一下**繼續**。若要加入網路，請參閱[加入網路](#joining-a-network)的步驟 5。現在您已準備好開始使用具有預設配置的「入門範本方案」網路。此網路的運作包括一個排序節點（即所謂的 "SOLO" 排序服務）、兩個組織、一個 CA，以及每個組織各一個對等節點。另外還會建立一個預設頻道。
6. 按一下**啟動**按鈕。

您可以在 [{{site.data.keyword.Bluemix_notm}} 服務儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} 服務儀表板") 中找到區塊鏈服務實例。


## 邀請成員
您可以邀請其他[組織](glossary.html#organization)加入，成為您的「入門範本方案」網路[成員](glossary.html#member)，以便彼此進行[交易](glossary.html#transaction)。此外，如果您想要使用「入門範本方案」來進行學習及測試，可以自行新增成員至網路，以模擬多組織網路。

1. 在「網路監視器」的「成員」畫面中，按一下**邀請成員**按鈕。
2. 「邀請成員」視窗隨即開啟。
    - 如果您想要邀請另一個組織，請選擇「邀請成員」。指定您要邀請之組織的名稱和操作員電子郵件位址。您也可以在「新增附註」欄位中輸入您要包含在邀請中的其他資訊。按一下**傳送邀請**按鈕。受邀的組織將會收到邀請電子郵件，然後可以遵循電子郵件中的指示加入您的網路。
    - 如果您想要新增可以新增至頻道的其他組織，請選擇「新增成員」。為您的新組織指定名稱。您可以選擇新增對等節點至您的新組織，或是稍後再於「網路監視器」中進行。按一下**建立**按鈕。請注意，如果您為新組織新增對等節點，則需要切換至這個新組織，才能看到您的對等節點。如需切換組織的相關資訊，請參閱[切換組織](dashboard.html#switch-organizations)。


## 加入網路
如果您是受到網路起始者的邀請，您將會收到電子郵件通知，其中會提供如何加入網路的指示。遵循電子郵件中的指示，即可成為網路的成員之一。

您需要使用 {{site.data.keyword.cloud_notm}} 中的「入門範本方案」成員資格選項來建立 [{{site.data.keyword.blockchain}} 平台服務實例 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain)。

1. 使用您的 {{site.data.keyword.cloud_notm}} 帳戶登入。如果您沒有帳戶，請按一下**註冊以建立**按鈕。
2. 選取您要用來儲存 {{site.data.keyword.blockchain}} 網路的 Cloud Foundry 組織。
3. 從定價方案表格中選取**入門範本成員資格方案**。
4. 按一下**建立**按鈕。隨即顯示服務實例頁面，並蹦現歡迎使用畫面。請注意，您可以選擇加入網路，或是繼續建立自己的網路。若要建立網路，請參閱[建立網路](#creating-a-network)中的步驟 4。
5. 在歡迎使用畫面中，選擇**加入現有網路**，從下拉清單中選取網路，然後按一下**繼續**。

您可以在 [{{site.data.keyword.cloud_notm}} 服務儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 服務儀表板") 中找到區塊鏈服務實例。

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
-->


## 擷取網路認證和連線設定檔
在 {{site.data.keyword.cloud_notm}} 中建立「入門範本方案」網路之後，您可以從服務實例頁面或在「網路監視器」中擷取網路認證和連線設定檔。

### 從服務實例頁面擷取
建立服務實例之後，您就會在服務實例頁面上。您也可以在 [{{site.data.keyword.cloud_notm}} 服務儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 服務儀表板") 中按一下您的服務，以開啟服務實例頁面。

使用下列步驟來擷取您的服務認證：
1. 在服務實例頁面上，按一下左導覽器中的**服務認證**，以顯示「服務認證」畫面。
2. 在「服務認證」畫面中，按一下**新建認證**。
3. 在「新增認證」畫面中，為認證命名，並在「新增線型配置參數」欄位中輸入 **{"type": "service_instance_token"}**。按一下**新增**。新建的認證會新增在表格中。您可以按一下「動作」直欄下的**檢視認證**，以檢視認證詳細資料。此認證包含 API 金鑰和密碼，可用來授權 API。  

![擷取網路認證](images/service_credentials.gif "擷取網路認證")

### 在網路監視器中擷取
您可以在「網路監視器」的「API」畫面中找到網路認證。如需使用 API 的相關資訊，請參閱[使用 Swagger API 與網路互動](howto/swagger_apis.html)。

您可以在「網路監視器」的「概觀」畫面中擷取連線設定檔。按一下「概觀」畫面上的**連線設定檔**按鈕，連線設定檔就會顯示在新頁面中。


## 部署範例應用程式
「入門範本方案」可讓您將範例應用程式部署至網路，只要按幾下即可完成。透過這些範例，您可以輕鬆地瞭解鏈碼和應用程式在網路中的運作方式。

如需相關資訊，請參閱[部署範例應用程式](howto/prebuilt_samples.html)。


## 開發及部署自訂商業網路
「入門範本方案」整合「{{site.data.keyword.blockchainfull_notm}} 平台：開發」開發人員環境和 Hyperledger Composer 開發人員工具集。您可以根據您的商業需求來開發區塊鏈網路。
針對您的業務來開發網路之後，可以將您的商業網路部署至「入門範本方案」網路。

如需相關資訊，請參閱[開發網路](develop.html)和[使用入門範本方案來部署商業網路](develop_starter.html)。


## 監視網路資源
如果您的應用程式要求交易，您可以在「網路監視器」中檢視交易狀態資訊。如需網路監視的相關資訊，請參閱[監視網路](howto/monitor_network.html)。


## 重設網路
如果您要清除自訂的配置、執行中的鏈碼，或是已部署的應用程式，則可以將網路重設回起始的預設配置。如需相關資訊，請參閱[重設網路](dashboard.html#reset-network)。


## 從入門範本方案移轉至企業方案
{: #migrate}

您可以將針對「入門範本方案」網路測試的 `.bna`、鏈碼及應用程式部署至「企業方案」網路中。

如果您有自己的商業網路保存檔 (`.bna`)，請遵循[將商業網路部署至企業方案](./develop_enterprise.html)的指示進行。如果沒有自己的 `.bna` 檔案，請使用 `composer network download` 指令，以從「入門範本方案」實例擷取該檔案。如需 `composer network download` 指令的相關資訊，請參閱 [Hyperledger Composer 指令行文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}。

鏈碼（類似 `.bna` 檔案）是在外部開發的。若要將您針對「入門範本方案」網路測試的鏈碼部署至「企業方案」中，請遵循[安裝、實例化及更新鏈碼](howto/install_instantiate_chaincode.html#installchaincode)中的指示。

<!--
As you can see in [Deploying sample applications](howto/prebuilt_samples.html), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](howto/prebuilt_samples.html#deploy_sample_applications_manually).
-->

如果您在「入門範本方案」網路上部署任何範例應用程式，而且要在「企業方案」網路上部署這個應用程式，您可以將分出的應用程式儲存庫複製到新的儲存庫中，然後遵循範例應用程式中的指示，在「企業方案」網路上部署它們。


## 刪除或離開網路
如果您想要刪除或離開網路，可以從 {{site.data.keyword.cloud_notm}} 儀表板中刪除區塊鏈服務實例。

**附註**：在您離開網路之前，請確定您不是網路任何頻道中的成員。否則，您會在離開網路時收到錯誤。頻道成員移除需要完成頻道更新處理程序。如需頻道更新處理程序的相關資訊，請參閱[更新頻道](howto/create_channel.html#updating-a-channel)。


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/){:new_window}.
-->

---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於入門範本方案
{: #overview}

「{{site.data.keyword.blockchainfull}} 平台入門範本方案」是入門級選項，可讓組織模擬多組織區塊鏈網路、快速開發應用程式，以及使用範例智慧型合約和商業網路。它還包含與其他成員資格選項相同的使用者介面體驗，以協助消除任何學習曲線。
「入門範本方案」網路是以 Hyperledger Fabric 1.1 版為建置基礎。
{:shortdesc}

**附註**：
1. 「{{site.data.keyword.blockchainfull_notm}} 平台入門範本方案」是開發和測試環境，不適用於正式作業工作負載。如果您需要正式作業環境，請參閱[關於企業方案](enterprise_plan.html)。您可能會先檢查[入門範本方案考量](#considerations)，再使用「入門範本方案」。
2. 「入門範本方案」在 2018 年 6 月 14 日從測試版進展到 GA。如果您有測試版網路，則請閱讀[移轉考量](#beta-to-ga)。

註冊您的 [{{site.data.keyword.blockchainfull_notm}} 成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)，立即開始試用具有[試用額度](howto/pricing.html#starter-plan-pricing)的「入門範本方案」！<!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## 入門範本方案提供的內容

- **_一鍵備妥網路_**  
    只要按一下，「入門範本方案」即會佈建即時區塊鏈網路。每一個網路都會隨附排序服務、CA、兩個組織（每個組織各一個對等節點），以及在其上交易及部署鏈碼的預設頻道。「{{site.data.keyword.blockchainfull_notm}} 平台」會處理這個網路的建立和配置作業（您可以在其上線之後進行更新），讓您專注於開發。「入門範本方案」網路是以 Hyperledger Fabric 1.1 版為建置基礎，並使用 CouchDB 作為分類帳資料庫。<!--The free trial provides you up to two organizations and two peers.-->
- **_成本效率_**  
「入門範本方案」成員資格選項提供許多與「企業方案」成員資格選項相同的區塊鏈功能，但成本較低。在「入門範本方案」的試用期間，您可以免費佈建具有基本網路資源的區塊鏈網路。
- **_多組織網路模擬_**  
您可以使用「入門範本方案」來模擬建置具有多個組織的網路。您不需要實際邀請其他組織加入您的網路，但是可以自己充當其他組織。此機制可讓您學習新的組織如何加入網路、多個組織如何在網路中合作等等。您可以透過「網路監視器」在組織之間切換，以從不同組織的視角來檢視及管理網路。**附註**：如果您新增的資源超過兩個組織及兩個對等節點的免費試用基本資源，則可能會產生額外成本。
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- 「**_{{site.data.keyword.blockchainfull_notm}} 平台：開發」應用程式整合_**  
    「入門範本方案」可讓您部署已在 [{{site.data.keyword.blockchainfull_notm}} 平台：開發](https://blockchaindevelop.mybluemix.net/login)上開發的商業網路。如需相關資訊，請參閱[匯入 IBP：開發應用程式](link)。

## 目標對象

如果您符合下列其中一種狀況，則適合使用「入門範本方案」來開始您的區塊鏈之旅。
- **_正在學習「{{site.data.keyword.blockchainfull_notm}} 平台」。_**  
如果您對「{{site.data.keyword.blockchainfull_notm}} 平台」感到好奇，甚至是區塊鏈的新手，「入門範本方案」都是您學習如何開發及控管實際區塊鏈網路的絕佳方式。您可以瞭解組成網路的元件、管理成員資格、學習如何部署及管理鏈碼（又稱為「智慧型合約」）、如何新增頻道（和管理頻道許可權），以及追蹤新區塊產生的時間，就像在正式作業網路中一樣。
- **_在即時網路上建置示範解決方案。_**  
    「入門範本方案」提供功能強大的環境，可用來示範區塊鏈應用程式。立即可用的區塊鏈網路可讓您透過「網路監視器」提供的作業和管理工具，快速呈現給同事、主管及夥伴。
- **_超越單一組織開發。_**  
    「入門範本方案」可讓您充當多個組織，以查看「IBM Blockchain 平台」如何管理協同作業，如建立頻道和實例化鏈碼，以及測試應用程式和呼叫交易。您也可以邀請其他人在「入門範本方案」網路中（如同在「企業方案」中）分工合作。這可協助您在更真實的環境中建置，並有多人背書鏈碼及交易。
- **_反覆開發及測試區塊鏈應用程式。_**  
「入門範本方案」提供您一個暫置區，以在區塊鏈網路上持續開發及測試您的程式碼。移至雲端可容許開發人員和測試人員專注在功能上，並從單元測試輕鬆移至功能測試。「入門範本方案」提供與「企業方案」相同的區塊鏈網路功能以及作業和管理工具。在準備好將您的專案推送至其中一個「企業方案」之後，您可以使用「入門範本方案」中的相同方式來操作，但有更多機會擴展您的網路。


## 入門範本方案考量
{: #considerations}

「入門範本方案」是「{{site.data.keyword.blockchainfull_notm}} 平台」的進入點，可用於進行開發及測試。請先檢查下列項目，再使用「入門範本方案」。

- **{{site.data.keyword.cloud_notm}} 帳戶需求**  	
    您必須具有付費 {{site.data.keyword.cloud_notm}} 帳戶（例如，**隨收隨付制**類型）。「{{site.data.keyword.blockchainfull_notm}} 平台」提供的所有成員資格方案都需要 {{site.data.keyword.cloud_notm}} 付費帳戶。若要將帳戶升級至「隨收隨付制」類型，請移至 {{site.data.keyword.cloud_notm}} 主控台中的**管理** > **計費及用量** > **計費**，然後按一下**新增信用卡**。  
- **與企業方案的差異**
    - [CA](glossary.html#ca) 和[排序服務](glossary.html#orderer)沒有容錯功能，因為每個組織都只有一個 CA，而且一個網路只有一個[排序節點](glossary.html#orderer)。
    - 排序服務只使用 [SOLO](glossary.html#SOLO) [共識](glossary.html#consensus)。「入門範本方案」網路只包含一個[排序節點](glossary.html#orderer)來為所有對等節點執行共識。
    - 無法使用[硬體安全模組 (HSM)](glossary.html#hsm) 來保護及管理用於強型態鑑別和加密處理的數位金鑰。
- **網路資源限制**  
    「入門範本方案」會為每一個對等節點指派 1 個 CPU 和 2 Gi RAM，以及為每一個 {{site.data.keyword.cloud_notm}} 服務實例指派 20 Gi 儲存空間。 
- **非作用中網路刪除**  
    在建立「入門範本方案」網路之後，如果您在 15 天內未造訪過該網路或未對它發出任何交易，則「{{site.data.keyword.blockchainfull_notm}} 平台」會刪除該網路。
    - 如果您不再需要此網路，則不需要採取任何動作，就會自動刪除該網路。
    - 如果您仍然需要使用此網路，則可以對其發出交易，讓它保持作用中。您可以在 [{{site.data.keyword.cloud_notm}} 儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/apps/){:new_window} 中找到「入門範本方案」網路。如果您沒有要發出的定期交易，請遵循[指示](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan)來嘗試範例應用程式。
- **維護及升級**
    「入門範本方案」的維護和網路更新項目會依固定排程執行。在維護期間，您無法佈建新的網路，而且可能會遇到網路短時間中斷的情形。
- **資料保留**
    「入門範本方案」不保證版本升級（包括從「測試版」進展到 GA）後會保留資料。
- **移轉考量**
    -     目前不支援將資料從「入門範本方案」網路移轉至其他成員資格方案。不過，您可以移轉在「入門範本方案」上測試過的 `.bna` 檔案、鏈碼及應用程式。如需相關資訊，請參閱[從入門範本方案移轉至企業方案](get_start_starter_plan.html#migrate)。

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## 從測試版進展到 GA
{: #beta-to-ga}

「入門範本方案」在 2018 年 6 月 14 日進展到 GA 階段。GA 後，「IBM Blockchain 平台」會為每一個 IBM Cloud 帳戶提供 $500 試用額度，以建立具有「入門範本方案」的區塊鏈網路。「入門範本方案」在 2018 年 6 月 14 日進展到 GA 階段。GA 後，「IBM Blockchain 平台」會為每一個 IBM Cloud 帳戶提供 $500 試用額度，以建立具有「入門範本方案」的區塊鏈網路。如需試用額度的相關資訊，請參閱[入門範本方案定價](howto/pricing.html#starter-plan-pricing)中的*入門範本方案試用* 小節。請確定您具有付費 IBM Cloud 帳戶（例如，**隨收隨付制**類型）。請確定您具有付費 IBM Cloud 帳戶（例如，**隨收隨付制**類型）。


任何使用「入門範本方案測試版」所建立的區塊鏈網路都會保持**免費**，直到在「入門範本方案 GA」後的 **30 天**予以刪除為止。不支援將資料從「入門範本方案測試版」網路移轉至 GA 網路。**您在「測試版」網路中的資料會遺失。**不過，您可以手動移轉鏈碼、商業網路及應用程式。
- 如果您已在「測試版」網路中執行鏈碼，則請在 GA 網路中安裝及實例化鏈碼。如需相關資訊，請參閱[安裝、實例化及更新鏈碼](howto/install_instantiate_chaincode.html)。
- 如果您已在「測試版」網路上部署商業網路，則請在 GA 網路上部署具有 `.bna` 檔案的商業網路。如需相關資訊，請參閱[在入門範本方案上部署商業網路](develop_starter.html)。
- 如果您已對「測試版」網路執行自我開發的應用程式，則請更新應用程式中的 API 端點以指向 GA 網路節點。如需相關資訊，請參閱[將網路 API 端點新增至應用程式](v10_application.html#adding-network-api-endpoints-to-your-application)。

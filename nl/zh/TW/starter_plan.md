---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於入門範本方案
{: #overview}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


「{{site.data.keyword.blockchainfull}} 平台入門範本方案」是入門級選項，可讓組織模擬多組織區塊鏈網路、快速開發應用程式，以及使用範例智慧型合約和商業網路。它還包含與其他成員資格選項相同的使用者介面體驗，以協助消除任何學習曲線。
「入門範本方案」網路是以 Hyperledger Fabric 1.1 版為建置基礎。
{:shortdesc}

**附註：**
- 「{{site.data.keyword.blockchainfull_notm}} 平台入門範本方案」是開發和測試環境，不適用於正式作業工作負載。如果您需要正式作業環境，請參閱[關於企業方案](enterprise_plan.html)。您可能會先檢查[入門範本方案考量](#considerations)，再使用「入門範本方案」。  
- 「{{site.data.keyword.blockchainfull_notm}} 平台」是 {{site.data.keyword.cloud_notm}} 上的平台服務，而且所有成員資格供應項目都遵循服務水準合約 (SLA) 上的 [{{site.data.keyword.cloud_notm}} 服務條款 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "{{site.data.keyword.cloud_notm}} 服務條款")。「入門範本方案」網路跨**多個環境**在不同地理位置的資料中心進行佈建。

## 入門範本方案提供的內容

- **_一鍵備妥網路_**  
    只要按一下，「入門範本方案」即會佈建即時區塊鏈網路。每一個網路都會隨附排序服務、CA、兩個組織（每個組織各一個對等節點），以及在其上交易及部署鏈碼的預設頻道。「{{site.data.keyword.blockchainfull_notm}} 平台」會處理這個網路的建立和配置作業（您可以在其上線之後進行更新），讓您專注於開發。「入門範本方案」網路是以 Hyperledger Fabric 1.1 版為建置基礎，並使用 CouchDB 作為狀態資料庫。<!--The free trial provides you up to two organizations and two peers.-->
- **_成本效率_**  
「入門範本方案」成員資格選項提供許多與「企業方案」成員資格選項相同的區塊鏈功能，但成本較低。「{{site.data.keyword.blockchainfull_notm}} 平台」提供 $500 雲端額度給「入門範本方案」的新使用者。這些額度可讓您免費使用基本網路資源來佈建區塊鏈網路，為期一個月。如需詳細資料，請參閱[入門範本方案考量](#starter-plan-considerations)。
- **_多組織網路模擬_**  
您可以使用「入門範本方案」來模擬建置具有多個組織的網路。您不需要實際邀請其他組織加入您的網路，但是可以自己充當其他組織。此機制可讓您學習新的組織如何加入網路、多個組織如何在網路中合作等等。您可以透過「網路監視器」在組織之間切換，以從不同組織的視角來檢視及管理網路。  
    **附註**：如果您新增的資源超過兩個組織及兩個對等節點的免費試用基本資源，則可能會產生額外成本。
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- 「**_{{site.data.keyword.blockchainfull_notm}} 平台：開發」應用程式整合_**  
    「入門範本方案」可讓您部署已在 [{{site.data.keyword.blockchainfull_notm}} 平台：開發](https://blockchaindevelop.mybluemix.net/login)上開發的商業網路。如需相關資訊，請參閱[在入門範本方案上部署商業網路](develop_starter.html)。

## 入門範本方案適合您嗎

如果您符合下列其中一種狀況，則適合使用「入門範本方案」來開始您的區塊鏈之旅。
- **_正在學習「{{site.data.keyword.blockchainfull_notm}} 平台」。_**  
如果您對「{{site.data.keyword.blockchainfull_notm}} 平台」感到好奇，甚至是區塊鏈的新手，「入門範本方案」都是您學習如何開發及控管實際區塊鏈網路的絕佳方式。您可以瞭解組成網路的元件、管理成員資格、學習如何部署及管理鏈碼（又稱為「智慧型合約」）、如何新增頻道（和管理頻道許可權），以及追蹤新區塊產生的時間，就像在正式作業網路中一樣。
- **_在即時網路上建置示範解決方案。_**  
    「入門範本方案」提供功能強大的環境，可用來示範區塊鏈應用程式。立即可用的區塊鏈網路可讓您透過「網路監視器」提供的作業和管理工具，快速呈現給同事、主管及夥伴。
- **_超越單一組織開發。_**  
「入門範本方案」可讓您充當多個組織，以查看「{{site.data.keyword.blockchainfull_notm}} 平台」如何管理協同作業，如建立頻道和實例化鏈碼，以及測試應用程式和呼叫交易。您也可以邀請其他人在「入門範本方案」網路中（如同在「企業方案」中）分工合作。這可協助您在更真實的環境中建置，並有多人背書鏈碼及交易。
- **_反覆開發及測試區塊鏈應用程式。_**  
「入門範本方案」提供您一個暫置區，以在區塊鏈網路上持續開發及測試您的程式碼。移至雲端可容許開發人員和測試人員專注在功能上，並從單元測試輕鬆移至功能測試。「入門範本方案」提供與「企業方案」相同的區塊鏈網路功能以及作業和管理工具。在準備好將您的專案推送至其中一個「企業方案」之後，您可以使用「入門範本方案」中的相同方式來操作，但有更多機會擴展您的網路。


## 入門範本方案考量
{: #considerations}

「入門範本方案」是「{{site.data.keyword.blockchainfull_notm}} 平台」的進入點，可用於進行開發及測試。請先檢查下列項目，再使用「入門範本方案」。

- **{{site.data.keyword.cloud_notm}} 帳戶需求**  	
    您必須具有付費 {{site.data.keyword.cloud_notm}} 帳戶（例如，**隨收隨付制**類型）。「{{site.data.keyword.blockchainfull_notm}} 平台」提供的所有成員資格方案都需要 {{site.data.keyword.cloud_notm}} 付費帳戶。若要將帳戶升級至「隨收隨付制」類型，請移至 {{site.data.keyword.cloud_notm}} 主控台中的**管理** > **計費及用量** > **計費**，然後按一下**新增信用卡**。  
- **{{site.data.keyword.blockchainfull_notm}} 平台雲端額度**  
    「{{site.data.keyword.blockchainfull_notm}} 平台」提供 $500 雲端額度給「入門範本方案」的新使用者。這些額度可涵蓋預設網路配置的成本來協助使用者開始，為期一個月。
    - 新使用者必須註冊，才能取得雲端額度。不會自動套用它們。如果還沒有額度，請[註冊 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "註冊")，以索取額度。在您存取「入門範本方案」之前，請等待 24 小時，以確定您的[帳戶 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "帳戶") 中有這些額度。否則，可能會在套用額度之前向您收取費用。
    - 雲端額度適用於所有 {{site.data.keyword.cloud_notm}} 服務。除「{{site.data.keyword.blockchainfull_notm}} 平台」以外，您在 {{site.data.keyword.cloud_notm}} 上使用的產品可以耗用它們。
    - 此供應項目提供終生額度。只要您保持 {{site.data.keyword.cloud_notm}} 帳戶，就可以繼續保有額度。您不必在第一個月就使用它們。
    使用[定價指南](howto/pricing.html#starter-plan-pricing)來進一步瞭解如何使用試用額度。
- **與企業方案的差異**
    - [CA](glossary.html#ca) 和[排序服務](glossary.html#orderer)沒有容錯功能，因為每個組織都只有一個 CA，而且一個網路只有一個[排序節點](glossary.html#orderer)。
    - 排序服務只使用 [SOLO](glossary.html#solo) [共識](glossary.html#consensus)。「入門範本方案」網路只包含一個[排序節點](glossary.html#orderer)來為所有對等節點執行共識。
    - 無法使用[硬體安全模組 (HSM)](glossary.html#hsm) 來保護及管理用於強型態鑑別和加密處理的數位金鑰。
- **網路資源限制**  
「入門範本方案」會為每一個對等節點指派 1 個 CPU 和 4 Gi RAM，以及為每一個 {{site.data.keyword.cloud_notm}} 服務實例指派 20 Gi 儲存空間。鏈碼容器及分類帳區塊是最耗費資源的網路元件。在其網路上具有許多對等節點、產生大量區塊，或使用大型鏈碼檔案的使用者可能會遭遇效能受到資源限制的影響。
- **維護及升級**  
「入門範本方案」的維護和網路更新項目會依固定排程執行。在維護期間，您無法佈建新的網路，而且可能會遇到網路短時間中斷的情形。
- **資料保留**  
「入門範本方案」不保證版本升級後可保留資料。
- **移轉考量**  
    目前不支援將資料從「入門範本方案」網路移轉至其他成員資格方案。不過，您可以移轉在「入門範本方案」上測試過的 `.bna` 檔案、鏈碼及應用程式。如需相關資訊，請參閱[從入門範本方案移轉至企業方案](howto/migrate_sp_ep.html)。

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](v10_application.html#adding-network-api-endpoints-to-your-application).
-->

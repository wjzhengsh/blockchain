---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於入門範本方案
{: #overview}

「{{site.data.keyword.blockchainfull}} 平台入門範本方案」是入門級選項，可讓組織模擬多組織區塊鏈網路、快速開發應用程式，以及使用所提供的範例。它還包含與其他成員資格選項相同的使用者介面體驗，以協助消除任何學習曲線。
「入門範本方案」網路是以 Hyperledger Fabric 1.1 版為建置基礎。
{:shortdesc}

**附註**：「{{site.data.keyword.blockchainfull_notm}} 平台入門範本方案」是開發和測試環境，不適用於正式作業工作負載。如果您需要正式作業環境，請參閱[關於企業方案](enterprise_plan.html)。您可能會先檢查[入門範本方案考量](#considerations)，再使用「入門範本方案」。

註冊您的 [{{site.data.keyword.blockchainfull_notm}} 成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)，立即開始試用「入門範本方案」！請注意，您必須在 {{site.data.keyword.cloud_notm}} 中選擇**美國南部**作為地區，才能使用「入門範本方案測試版」來建立區塊鏈網路。


## 目標對象

如果您符合下列其中一種狀況，則適合使用「入門範本方案」來開始您的區塊鏈之旅。
- **_學習 {{site.data.keyword.blockchainfull_notm}} 平台。_**  
如果您對「{{site.data.keyword.blockchainfull_notm}} 平台」感到好奇，甚至是區塊鏈的新手，「入門範本方案」都是您學習如何開發及控管實際區塊鏈網路的絕佳方式。您可以瞭解組成網路的元件、學習如何部署及管理鏈碼（又稱為「智慧型合約」）、如何新增頻道（和管理頻道許可權），以及追蹤新區塊產生的時間，就像在正式作業網路中一樣。
- **_想要充當多個組織來協助網路開發。_**  
   「入門範本方案」可讓您充當多個組織，以查看「IBM Blockchain 平台 (IBP)」如何管理協同作業，如建立頻道和實例化鏈碼，以及測試應用程式和呼叫交易。您也可以邀請其他人在「入門範本方案」網路中（如同在「企業方案」中）分工合作。
- **_在即時網路中建置示範解決方案。_**  
「入門範本方案」提供功能強大的環境，可用來測試網路定義（藉由整合使用「{{site.data.keyword.blockchainfull_notm}} 平台：開發」來開發的 `.bna` 檔案）和區塊鏈應用程式。立即可用的區塊鏈網路可讓您透過「網路監視器」提供的作業和管理工具，快速呈現給同事、主管及夥伴。
- **_反覆開發及測試區塊鏈應用程式。_**  
「入門範本方案」提供您一個暫置區，以在區塊鏈網路上持續開發及測試您的程式碼。您可以反覆開發您的程式碼，並部署至連續整合及連續部署架構中。「入門範本方案」提供與「企業方案」相同的區塊鏈網路功能以及作業和管理工具。在準備好將您的專案推送至其中一個「企業方案」之後，您可以使用「入門範本方案」中的相同方式來操作，但有更多機會擴展您的網路。
- **_在正式作業之前，先測試專案。_**  
「入門範本方案」提供的環境可讓開發人員和測試人員從其本端環境快速移動至實際的雲端區塊鏈環境。此機制可讓開發人員和測試人員專注在功能上，並從單元測試輕鬆移動至功能測試。系統、解決方案及效能測試團隊也可以直接使用此環境，而不需要花費額外的力氣在本端設定網路。
- **_教育作業的「{{site.data.keyword.blockchainfull_notm}} 平台」。_**  
    「{{site.data.keyword.blockchainfull_notm}} 平台」在「入門範本方案」和「企業方案」中提供幾乎相同的使用者介面<!--the same user interface-->，確保您自訂訓練所使用的工作流程，與您組織要用於「企業方案」的工作流程相同。
- **_使用「工具鏈」快速部署範例應用程式。_**  
「入門範本方案」可讓您使用「工具鏈」來部署範例應用程式，只要按幾下即可完成。這些範例會提供不斷擴增的一組程式碼範例，以供修改及精進，藉以協助開發人員。


## 入門範本方案特殊化

「入門範本方案」可讓您使用「憑證管理中心 (CA)」來管理成員資格、執行交易背書、提供排序服務、建置專用頻道、管理鏈碼生命週期，以及在即時網路中與其他人分工合作，就像在「企業方案」中一樣。

特別是，「入門範本方案」提供預先配置的區塊鏈網路，可讓您透過「網路監視器」來開發、控管及操作。它還提供簡單的方法來部署範例應用程式，並整合您以「{{site.data.keyword.blockchainfull_notm}} 平台：開發」來開發的應用程式。

- **_一鍵備妥網路_**  
您只要按一下，「入門範本方案」就會為您佈建具有排序服務、CA、一個預設頻道及兩個組織（每個組織各一個對等節點）的網路。「{{site.data.keyword.blockchainfull_notm}} 平台」會處理這個網路的建立和配置作業（您可以在其上線之後進行更新）。<!--The free trial provides you up to two organizations and two peers.-->
- **_成本效率_**  
「入門範本方案」成員資格選項提供許多與「企業方案」成員資格選項相同的區塊鏈功能，但成本較低。<!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->在「測試版」階段，您可以免費使用「入門範本方案」。
- **_多組織網路模擬_**  
您可以使用「入門範本方案」來模擬建置具有多個組織的網路。您不需要實際邀請其他組織加入您的網路，但是可以自己充當其他組織。此機制可讓您學習新的組織如何加入網路、多個組織如何在網路中合作等等。您可以透過「網路監視器」在組織之間切換，以從不同組織的視角來檢視及管理網路。
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_Swagger API_**  
「入門範本方案」公開數個 REST API，可讓您從 Swagger 介面來試用。如需相關資訊，請參閱[使用 Swagger API](swagger_apis.html)。
- **_範例應用程式_**  
「入門範本方案」運用 {{site.data.keyword.cloud_notm}} 中的「工具鏈」服務，只要簡單按幾下，即可啟用範例部署。在您部署並啟動範例之後，就會自動針對您的區塊鏈網路執行鏈碼和應用程式。如需範例應用程式的相關資訊，請參閱[部署範例應用程式](howto/prebuilt_samples.html)。
- 「**_{{site.data.keyword.blockchainfull_notm}} 平台：開發」應用程式整合_**  
「入門範本方案」可讓您將「{{site.data.keyword.blockchainfull_notm}} 平台：開發」應用程式部署至您的網路。如需相關資訊，請參閱[匯入 IBP：開發應用程式](link)。

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## 入門範本方案考量
{: #considerations}

「入門範本方案」是「{{site.data.keyword.blockchainfull_notm}} 平台」的進入點，可用於進行開發及測試。請先檢查下列項目，再使用「入門範本方案」。

- **{{site.data.keyword.cloud_notm}} 帳戶需求**  
    在測試版期間，「入門範本方案」是免費的。不過，您必須將 {{site.data.keyword.cloud_notm}} 帳戶升級為付費帳戶（例如，**隨收隨付制**類型）。「{{site.data.keyword.blockchainfull_notm}} 平台」提供的所有成員資格方案都需要 {{site.data.keyword.cloud_notm}} 付費帳戶。若要將帳戶升級至「隨收隨付制」類型，請移至 {{site.data.keyword.cloud_notm}} 主控台中的**管理** > **計費及用量** > **計費**，然後按一下**新增信用卡**。
- **與企業方案的差異**
    - [CA](glossary.html#ca) 和[排序服務](glossary.html#orderer)沒有容錯功能，因為每個組織都只有一個 CA，而且一個網路只有一個[排序節點](glossary.html#orderer)。
    - 排序服務只使用 [SOLO](glossary.html#SOLO) [共識](glossary.html#consensus)。「入門範本方案」網路只包含一個[排序節點](glossary.html#orderer)來為所有對等節點執行共識。
    - 無法使用[硬體安全模組 (HSM)](glossary.html#hsm) 來保護及管理用於強型態鑑別和加密處理的數位金鑰。
- **網路資源限制**  
    「入門範本方案」會為每一個 {{site.data.keyword.cloud_notm}} 服務實例持續指派 1 個 CPU、2 GB RAM 及 20 Gi 儲存空間的硬體。如果您將更多網路資源新增至相同的網路，則資源會共用硬體指派。不過，當您邀請成員加入網路時，成員會建立新的服務實例。因此，「入門範本方案」會將另一組的 1 個 CPU、2 GB RAM 及 20 Gi 儲存空間硬體指派給新的成員。
- **非作用中網路刪除**  
    在建立「入門範本方案（測試版）」網路之後，如果您在 15 天內未造訪過該網路或未對它發出任何交易，則「{{site.data.keyword.blockchainfull_notm}} 平台」會刪除該網路。
    - 如果您不再需要此網路，則不需要採取任何動作，就會自動刪除該網路。
    - 如果您仍然需要使用此網路，則可以對其發出交易，讓它保持作用中。您可以在 [{{site.data.keyword.cloud_notm}} 儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/apps/){:new_window} 中找到「入門範本方案（測試版）」網路。如果您沒有要發出的定期交易，請遵循[指示](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan)來嘗試範例應用程式。
- **維護及升級**  
「入門範本方案」的維護和網路更新項目會依固定排程執行。在維護期間，您無法佈建新的網路，而且可能會遇到網路短時間中斷的情形。
- **資料保留**  
「入門範本方案」不保證版本升級（包括從「測試版」移至 GA）後會保留資料。
- **移轉考量**  
    目前不支援將資料從「入門範本方案」網路移轉至其他成員資格方案。不過，您可以移轉在「入門範本方案」上測試過的 `.bna` 檔案、鏈碼及應用程式。如需相關資訊，請參閱[從入門範本方案移轉至企業方案](get_start_starter_plan.html#migrate)。
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

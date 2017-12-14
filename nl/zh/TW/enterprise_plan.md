---

copyright:
  years: 2017
lastupdated: "2017-09-20"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} 平台企業方案

針對想要建立或加入區塊鏈商業網路的組織，「{{site.data.keyword.blockchainfull}} 平台企業方案」是他們第一個可用的成員資格選項。此方案提供金鑰基礎架構，附有工具和支援，可輕鬆部署高度安全的正式作業備妥區塊鏈網路。

IBM 為即將起始網路的成員提供圖形使用者介面，可引導網路起始者完成設定及佈建網路的重要步驟。其中包括邀請其他成員及設定控管規則。網路部署完成之後，即可使用互動式圖形使用者介面來監視網路的性能和活動；管理重要的網路活動，包括新的部署、新增/移除成員、鏈碼生命週期和通道管理；以及尋求技術支援。尋找如何取得[支援](ibmblockchain_support.html)的相關資訊。

「{{site.data.keyword.blockchainfull_notm}} 平台」是以重要的 Hyperledger Fabric 元件建置而成，其中包括「憑證管理中心 (CA)」，以及至少 1 個對等節點（最多 6 個）。IBM 也為網路成員提供損毀容錯 (CFT) Kafka 排序服務。 

Fabric CA 是隨附於企業方案的憑證管理中心。針對每個成員，會各提供兩個中繼 CA，以授予網路的成員資格。成員也可以藉由使用 CA 來提供成員資格（憑證）給網路的一般使用者。

您必須瞭解，若要將交易附加至分類帳，將會經歷三個階段：  
1. 交易模擬和背書（對等節點）
2. 排序（排序服務）
3. 驗證和確定（對等節點）

成員所擁有的 Fabric 對等節點是應用程式用來執行鏈碼的介面或閘道，提供用來對分類帳執行交易的商業邏輯。所有交易都必須經過背書。由網路的其他成員來進行這項背書。背書之後，就會將交易傳送至 IBM 提供的排序服務 (Kafka)。

除了核心區塊鏈元件之外，「企業成員資格」選項還提供具有安全資料儲存與通訊 (TLS) 功能的基礎架構，以及高可用性。雖然 Fabric 網路會共用這些基礎架構資源，但會在網路中為 Fabric 元件節點提供隔離，而且每個節點都會在保護執行環境的安全 Docker 容器中執行。

唯一必須決定的層面是網路所需的對等節點大小。此決策取決於所需的通道數目，加上每個通道的工作負載、記憶體用量以及磁碟空間（儲存空間）。 

IBM Blockchain 平台應該用於更穩定、正式作業，或幾乎正式作業層次的部署。若為測試目的，請使用 IBM Container 服務，或本端可下載的映像檔。

企業方案提供排序服務和 CA。成員資格費用是 $1,000，以及針對與網路相關聯的每個對等節點 $1,000。如果您想要具有高可用性 (HA)，必須購買一個額外的對等節點以提供 HA 功能。例如，一個組織（相關聯的成員資格費用 $1,000），具有兩個對等節點（$1,000 X 2 對等節點）和 HA（$1,000 X 2 HA 對等節點）需要每月支付的費用為 $5,000。

立即註冊您的 {{site.data.keyword.IBM_notm}} [{{site.data.keyword.blockchainfull_notm}} 成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)。

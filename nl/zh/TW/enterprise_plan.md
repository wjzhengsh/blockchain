---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於企業方案

針對想要建立或加入區塊鏈網路以從事實際商業活動的組織，「{{site.data.keyword.blockchainfull}} 平台企業方案」是可用於正式作業的供應項目。此方案提供金鑰基礎架構，附有工具和支援，可輕鬆啟動高度安全且可用於正式作業的網路。2018 年 5 月 15 日，「企業方案」會從 Hyperledger Fabric 1.0 版升級至 1.1 版。在 2018 年 5 月 15 日之後建立的所有網路都是 Fabric 1.1 版層次。不過，先前建立的網路將會保留為 Fabric 1.0 版層次。
{:shortdesc}

**附註**：「{{site.data.keyword.blockchainfull_notm}} 平台企業方案」提供正式作業環境。如果您需要開發及測試環境，請參閱[關於入門範本方案](starter_plan.html)。

IBM 為即將起始網路的成員提供圖形使用者介面，可引導網路起始者完成設定及佈建網路的重要步驟。其中包括邀請其他成員及設定控管規則。如需相關資訊，請參閱[控管企業方案網路](get_start.html)。網路部署完成之後，即可使用互動式圖形使用者介面（網路監視器）來監視網路的性能和活動；管理重要的網路活動，包括新的部署、新增或移除成員、鏈碼生命週期和頻道管理；以及尋求技術支援。如需「網路監視器」的相關資訊，請參閱[操作企業方案網路](v10_dashboard.html)。

立即註冊您的 [{{site.data.keyword.blockchainfull_notm}} 成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)。

「{{site.data.keyword.blockchainfull_notm}} 平台」是以重要的 Hyperledger Fabric 元件建置而成，其中包括「憑證管理中心 (CA)」，以及至少 1 個對等節點（最多 6 個）。「企業方案」也為網路成員提供損毀容錯 (CFT) Kafka 排序服務。

Fabric CA 是隨附於「企業方案」的憑證管理中心。針對每個成員，會各提供兩個中繼 CA，以授予網路的成員資格。成員也可以使用 CA 來提供成員資格（憑證）給網路的使用者。

您必須瞭解，若要將交易附加至分類帳，需要經歷三個階段：  
1. 交易模擬和背書（對等節點）
2. 排序（排序服務）
3. 驗證和確定（對等節點）

成員所擁有的 Fabric 對等節點是應用程式用來執行鏈碼的介面或閘道，提供用來對分類帳執行交易的商業邏輯。所有交易都必須經過背書。由網路的其他成員來進行這項背書。背書之後，就會將交易傳送至 IBM 提供的排序服務 (Kafka)。

除了核心區塊鏈元件之外，「企業成員資格」選項還提供具有安全資料儲存與通訊 (TLS) 功能的基礎架構，以及高可用性。雖然 Fabric 網路會共用這些基礎架構資源，但會在網路中為 Fabric 元件節點提供隔離，而且每個節點都會在保護執行環境的安全 Docker 容器中執行。

唯一必須決定的層面是網路所需的對等節點大小。此決策取決於所需的頻道數目，加上每個頻道的工作負載、記憶體用量以及磁碟空間（儲存空間）。

您應該將「企業方案」用於較穩定、正式作業或幾乎是正式作業層次的部署。若為測試目的，請使用[入門範本方案](starter_plan.html)、[在 IBM Container 服務中開發](https://ibm-blockchain.github.io/)，或[在本端安裝 Docker 映像檔](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html)。

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## 定價  
若要使用「企業方案」，網路成員必須每個月支付 $1,000 美元的會員費，針對網路中的每個對等節點，每個月需外加 $1,000 美元。月費是按日數比例計費。例如，一個成員（相關會員費為 $1,000 美元）有兩個對等節點（每個對等節點費用 $1,000 美元 X 2 個對等節點），則每個月需支付的費用為 $3,000 美元。如果該月份有 30 天，則該成員每日支付 $100 美元（$3,000 美元/30）。請注意，如果您需要高可用性 (HA)，則必須將必要的對等節點數目乘以兩倍，才能提供 HA 功能。

網路成員可以使用自己的 {{site.data.keyword.cloud_notm}} 帳戶（包含用來建立網路實例的空間）來付費。或者，可以由某位網路成員負責網路中所有成員的帳單。如需如何支付區塊鏈網路費用的相關資訊，請參閱[支付網路費用](howto/pay_for_the_network.html)。

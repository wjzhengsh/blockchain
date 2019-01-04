---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**注意：**在使用 {{site.data.keyword.blockchainfull}} Platform 供應項目之前，請先閱讀[免責聲明](needtoknow.html)一節中的技術和支援資訊。
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform (IBP) 是唯一整合式商業即用平台，可處理多組織區塊鏈網路的整個生命週期。其設計是要透過每個階段的協同作業，加速建立「專為商務建置」的廣域區塊鏈網路，即使是要求最嚴苛的使用案例和受監管行業，都能提供其所需的效能和安全性。{{site.data.keyword.cloud_notm}} 上的 {{site.data.keyword.IBM_notm}} 管理網路供應項目適合不熟悉區塊鏈的客戶。具有 Hyperledger Fabric 使用經驗的客戶可以透過 {{site.data.keyword.cloud_notm}} Private (ICP) 或 Amazon Web Services (AWS)，在他們自己的基礎架構上部署網路元件或網路。

{{site.data.keyword.blockchainfull_notm}} Platform 提供不同的供應項目，可協助所有類型的使用者開始進行其區塊鏈行程，並將其應用程式移至正式作業。

|       |[**入門範本方案**](#starter-enterprise)      |[ **企業方案**](#starter-enterprise)  | [**IBP for ICP**](#ibp-for-icp) | [**IBP for AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **包含的內容** | **基本服務水準、開發及測試環境** | **進階服務水準及企業正式作業備妥環境** |**可部署的 CA、排序節點及對等節點 Helm 圖表** | **AWS 快速入門對等節點**  |
| **計費原則** | 可以使用雲端額度的**每月訂閱[](howto/pricing.html#starter-plan-pricing)** |**每月訂閱** |  **[VPC 定價](ibp-for-icp-about.html#ibp-icp-pricing)及免費的 Community Edition** | **免費** |
| **雲端平台**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud Private**| **AWS**|

**警告：**不要將**入門範本方案**用於正式作業。它是開發和測試環境，不適用於正式作業工作負載。

## {{site.data.keyword.blockchainfull_notm}} Platform 功能

{{site.data.keyword.blockchainfull_notm}} 供應項目以 [Hyperledger Fabric](reference/v10_fabric.html) 程式碼庫為建置基礎，運用模組架構來達到企業層級的安全性、資料完整性、可調整性及效能，以符合您的商業需求。

{{site.data.keyword.blockchainfull_notm}} Platform 提供高度安全且具有許可權的區塊鏈網路，而已鑑別成員可以在其上輕鬆地定義資產，並且建立商業解決方案來修改及交換資產。透過 {{site.data.keyword.blockchainfull_notm}} Platform 供應項目，您可以利用編排架構，讓您能夠**快速地將您的聯盟組織成作用中的區塊鏈網路**。{{site.data.keyword.blockchainfull_notm}} Platform 提供容易使用的工具，其設計目的是要能夠輕易地讓多個機構一起加入，並建立以民主方式控管的網路。透過內建的儀表板監視器和所佈建的公用程式，網路的建立、控管及操作作業變得直觀且清楚易懂。放棄原本缺乏效率的建立網路及實作控管程序，聯盟成員反而能夠**專注在智慧型合約的部署，以及資產和資訊的傳送**。

網路必要資源（對等節點、排序節點、憑證管理中心）的**高可用性**可排除因單一失敗點而引發的嚴重後果。內建儀表板監視器可讓您輕鬆地管理這些資源，並提供功能強大的機制來視覺化資產和智慧型合約。*只有在「入門範本」及「企業」供應項目上才受支援*。

Hyperledger Fabric 架構的**模組性**以及網路角色的明確區分，所提供的基礎架構讓各種使用案例都能有可調整性及應變能力。*在所有 {{site.data.keyword.blockchainfull_notm}} Platform 供應項目上都可供使用並受支援*。

整個交易生命週期發生的檢查及平衡可確保一致且完整審查過的結果，而且分類帳會透過實作聊天通訊協定來持續保持同步。身分及存取控制是透過網路上不斷發生的**簽署/驗證**作業輕鬆地施行，這在*所有 {{site.data.keyword.blockchainfull_notm}} Platform 供應項目上都可供使用並受支援*。

提供的**控管工具**可讓成員管理其網路的重要商業規則。例如，您可能想要實作原則，而此原則定義必須要有多少位網路成員同意，新成員才能加入。或者，可能有需要每個參與者都背書才能進行修改的資產。控管規則是任何類型之商業網路的基礎，而且通常極為精密。控管工具（例如，原則編輯器）可大幅簡化此處理程序。*只有在「入門範本」及「企業」供應項目上才可供使用*。

「企業方案」網路會在不含網路資源外部存取權（包括 root 存取權）的**高度安全且隔離**環境中執行。資料會在進行中和靜止時加密，而且硬體安全模組可讓數位金鑰依據行業法規受到保護。硬體虛擬化會用來執行隔離環境中的每一個節點，藉此保護網路中的其他節點，避開可能發生故障或含有惡意鏈碼的對等節點。由於進階加密法的實作，讓雜湊、簽署/驗證作業，以及節點對節點的通訊都得以加速。*只有「企業」才可供使用*。

如需所有 Hyperledger Fabric 特性及功能的詳細資料，請參閱 [Hyperledger Fabric 文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/ "Hyperledger Fabric 文件")。

**圖 1** 說明已部署區塊鏈網路的範例，此網路包含四個成員（各自擁有兩個對等節點）、多個負責配送加密身分資料的「憑證管理中心」，以及一個定義原則和網路參與者的排序服務。藍色頻道包含所有四個網路成員，黃色頻道則僅限於三個成員：銀行 B、C 及 D。您也會看到銀行 A 扮演網路起始者的角色，而銀行 D 在黃色頻道的環境定義中，只是以成員的角色存在。最後，擁有正確簽署 x509 憑證的使用者或應用程式，可以將呼叫傳送至網路上的對等節點。

![區塊鏈網路](images/blockchain_network_2-01.png "區塊鏈網路範例")

*圖 1. 具有運用頻道來隔離資料之四個成員的區塊鏈網路範例*

## 開發要在網路上執行的應用程式

請運用由超過 400 個參與用戶端精鍊出來的最佳作法，探索並加速區塊鏈開發：

* 透過可大幅減少區塊鏈應用程式開發時間的技術，確保企業間的密切結盟（本來需要六週時間來開發的應用程式，現在不到兩天就能創造出來）。
* 運用 {{site.data.keyword.blockchainfull_notm}} 開發人員工具，在您現有的程式設計師名冊中，快速培養區塊鏈技術人才。
* 提供彈性給開發人員，讓他們運用開放且現代的工具集，在其偏好的環境中學習及發展。

身為企業擁有者，您可以開發您的使用案例，IBM 有眾多聚集在「{{site.data.keyword.blockchainfull_notm}} 車庫」中的產業和區塊鏈專家，可協助您充分利用 {{site.data.keyword.blockchainfull_notm}} Platform 的完整功能。

身為開發人員，您可以利用互動式遊樂場來開發、反覆運算及測試商業網路，以在 {{site.data.keyword.blockchainfull_notm}} Platform 網路環境中，快速且輕鬆地達到商業需求，並加速區塊鏈應用程式開發。這些工具的設計，是要將商業規則轉變成您偏好環境中的商業網路程式碼：

* **線上探索**
  運用 [{{site.data.keyword.blockchainfull_notm}} Platform：開發](./develop.html)<!--, which is powered by an open source development tool-->來學習重要的區塊鏈概念、建立網路定義，以及運用可重複使用的產業模型和智慧型合約檔案庫。

  開發商業網路之後，您可以將其部署至在 {{site.data.keyword.blockchainfull_notm}} Platform 上執行的即時網路。如需相關資訊，請參閱[使用入門範本方案來部署商業網路](./develop_starter.html)和[使用企業方案來部署商業網路](./develop_enterprise.html)。

* **在本端安裝**
  運用 Hyperledger Fabric 直接在筆記型電腦上開發及測試。如需相關資訊，請參閱[建置您的第一個網路](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html)。

* **在雲端環境中分工合作**
  使用立即可用的即時網路搭配「入門範本方案」及「企業方案」選項，與其他人一起開發及共用您的程式碼。如需相關資訊，請參閱[關於入門範本方案](starter_plan.html)和[關於企業方案](enterprise_plan.html)。

## {{site.data.keyword.IBM_notm}} 支援

{{site.data.keyword.IBM_notm}} 提供 {{site.data.keyword.IBM_notm}} 所實作之區塊鏈解決方案的各種支援選項。如需「{{site.data.keyword.blockchainfull_notm}} 支援」的相關資訊，請參閱[取得支援](ibmblockchain_support.html)。


## 入門範本方案及企業方案
{: #starter-enterprise}

「入門範本方案」及「企業方案」可讓您利用可用於正式作業的安全服務，來部署及操作非集中式區塊鏈網路。**入門範本方案**是一個學習或開始開發區塊鏈網路的環境，而**企業方案**是一種提供高階安全及支援的正式作業環境。從小型網路開始，隨著成員和交易量增加，再利用下列特性，彈性擴充您的網路：

* 超高安全環境，具有許多硬體、韌體和軟體安全特性。
* 針對可調整性、備援和可用性而強化的架構。
* 為獲得最佳效能以及在世界最快的 Linux 上運算而最佳化。

在 {{site.data.keyword.blockchainfull_notm}} Platform 上操作您的網路，包括可簡化管理作業的工具和功能：

* 儀表板，可監視及管理網路上的資源。
* 可無縫升級完整的程式碼堆疊。
* 整合到入口網站的全年無休技術支援。
* 無特許存取權的強化安全堆疊、惡意軟體與竄改防制、100% 加密，以及許多其他特性，適用於受監管行業中含有機密資料的網路。

「入門範本方案」及「企業方案」是 {{site.data.keyword.cloud_notm}} 上的平台服務，而且這兩個方案都遵循服務水準合約 (SLA) 上的 [{{site.data.keyword.cloud_notm}} 服務條款 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "{{site.data.keyword.cloud_notm}} 服務條款")。請注意，「入門範本方案」及「企業方案」網路都跨**多個環境**在不同地理位置的資料中心進行佈建。

如需「入門範本方案」及「企業方案」的相關資訊，請參閱[關於入門範本方案](starter_plan.html)及[關於企業方案](enterprise_plan.html)。如果您準備好開始，請立即在 {{site.data.keyword.cloud_notm}} 上註冊 [IBP 入門範本或企業成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)！

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-for-icp}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP 提供在您自己的基礎架構上透過 ICP 執行區塊鏈網路所需的元件。這些元件包括 Hyperledger Fabric 的一個「憑證管理中心 (CA)」、一個排序節點及一個對等節點，您可以使用 Kubernetes Helm 圖表進行部署、管理及設定。**此供應項目適用於具有進階 Hyperledger Fabric 經驗的客戶。**

IBP for ICP 可讓區塊鏈網路部署至專用雲端，以因應資料常駐需求、市場規定及基礎架構喜好設定。它可透過 {{site.data.keyword.cloud_notm}} Private 簡化您自己的基礎架構中區塊鏈網路的基本元素部署，而此 IBM Cloud Private 是一種 Kubernetes 型應用程式平台，用於開發及管理內部部署的容器化應用程式。

 * 讓用戶端管理具有其本身基礎架構的 IBP 網路。免費的 Community Edition 可讓客戶在他們自己的隔離且安全環境下執行，但不提供任何支援。
 * 讓用戶端使用 Helm 圖表及詳細作業文件，在 Kubernetes 上配置 Fabric。
 * 除非您使用 Community Edition，否則將進階技術支援授權給用戶端。

 如需 IBP for ICP 的相關資訊，請參閱[關於 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](ibp-for-icp-about.html)。

## {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #ibp-for-aws}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 可讓對等節點在 {{site.data.keyword.cloud_notm}} 上運用連線設定檔、Hyperledger Fabric 憑證管理中心 (CA) 以及現有「入門範本方案」或「企業方案」網路的排序服務，透過「AWS 快速入門」範本來處理交易。「快速入門」可讓您使用 AWS CloudFormation 範本來部署對等節點。此範本適用於 IT 基礎架構決策者及系統管理者，這些人員想要快速配置、部署及執行由 AWS 所管理並連接至「入門範本方案」或「企業方案」網路的 IBP 對等節點。您可以使用該範本在 AWS 中建置新的虛擬專用雲端 (VPC)，或將對等節點部署至現有 VPC。

「快速入門」將完成下列配置：
 * 跨兩個「可用性區域」的高可用性架構。
 * 根據 AWS 最佳作法配置公用子網路的 VPC。這會在 AWS 上提供您自己的虛擬網路。
 * 容許存取網際網路的網際網路閘道。
 * 公用子網路中，跨兩個「可用性區域」的兩個對等節點（每個子網路中有一個對等節點）。
 * 每個公用子網路中，具有內嵌 LevelDB 資料庫或次要 CouchDB 容器的對等節點容器。

如需 IBP for AWS 的相關資訊，請參閱[關於 {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](howto/remote_peer.html)。

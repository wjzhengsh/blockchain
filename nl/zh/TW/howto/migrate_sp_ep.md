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

# 從入門範本方案移轉至企業方案
{: #migrate_starter_to_enterprise}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform 的[入門範本方案](../starter_plan.html)提供測試及開發環境，以執行您的 PoC、展示，以及試用鏈碼及應用程式。當準備好移至正式作業環境時，您可以從「入門範本方案」網路移轉至[企業方案](../enterprise_plan.html)網路。
{:shortdesc}

「企業方案」網路提供下列正式作業可用的特性，以支援您的正式作業工作負載：

- 區塊鏈網路可在除了「入門範本方案」網路之外的其他廣域位置中使用。
- 沒有儲存空間限制，「入門範本方案」只有 20GB 儲存空間。
- 加強的 CPU 及 RAM 管理，以確保所有網路都順利執行。
- 加強的安全，包括下列功能：
  - 「安全服務容器 (SSC)」確保區塊鏈映像檔不得在任何給定的時間點進行竄改及載入，而且裝置碼及資料不管是在進行中還是休止中都會受到機密保護。
  - [硬體安全模組 (HSM)](../glossary.html#hsm)，適用於金鑰管理及金鑰儲存。
  - 通用磁碟加密。
- 「高可用性」、「災難回復」、「當機容錯」及漸進式升級。
- 選用的進階支援。

## 考量
{: #migrate_starter_to_enterprise_considerations}

從「入門範本方案」網路移轉至「企業方案」網路之前，您可能會閱讀下列考量。

- **定價：**您組織使用「企業方案」網路的每月費用包括每個實例 $1000 會員資格費用，以及每個對等節點 $1000 對等節點費用。如需相關資訊，請參閱[企業方案定價](pricing.html#enterprise-plan-pricing)。
- **Hyperledger Fabric 版本：**「企業方案」網路在 Hyperledger Fabric 1.1 版上執行。「入門範本方案」網路在 Fabric 1.2 版上執行。例如，使用「入門範本方案」網路的[專用資料](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html)這類元件（表示開發來使用「專用資料」的鏈碼）將不適用於「企業方案」。
- **受影響的資源：**鏈碼（智慧型合約）、商業網路定義、用戶端應用程式。同樣地，請注意您的鏈碼是否利用與 1.1 版網路不相容的 Fabric 1.2 版元件或功能。
- **所需時間：**至少需要半天，才能將基本網路從「入門範本方案」移轉至「企業方案」。
- **現有的分類帳資料：**無法從「入門範本方案」網路移至「企業方案」網路，因為測試資料不適合存在於正式作業環境中。

**附註：**一個*基本* 網路包括兩個組織，其中具有兩個對等節點、單一頻道，以及單一鏈碼或「商業網路保存檔」(`.bna`)。實際移轉時間可能有所不同，視您在「企業方案」網路中所需之網路元件的大小及複雜性而定。

## 移轉核對清單
{: #migrate_starter_to_enterprise_checklist}

從「入門範本」網路移至「企業方案」網路時，有一些必要的準備作業：您可以在稍後的章節中找到詳細指示，但摘要如下：

- 建立「企業方案」網路。
- 如果已使用 Hyperledger Composer 開發 `.bna`，請找出並移轉 `.bna` 檔案。
- 從測試「入門範本方案」網路重建組織和對等節點所需的配置。
- 識別將在「企業方案」網路中執行的鏈碼（以 Go 或 Node 撰寫）。
- 使用「企業方案」網路的新 API 端點資訊來更新用戶端應用程式。

### 建立企業方案網路
{: #migrate_starter_to_enterprise_create_network}

您需要先建立「企業方案」網路，才能進行移轉。如需相關資訊，請參閱[建立企業方案網路](../get_start.html#creating-a-network)。

### 移轉 `.bna` 檔案
{: #migrate_starter_to_enterprise_bna}

**附註：**如果您未在「入門範本方案」網路中使用 `.bna` 檔案，請跳過此步驟。

如果您已使用 Hyperleger Composer 來定義商業網路，並已將 `.bna` 檔案部署至「入門範本方案」網路，則可以將相同的 `.bna` 檔案部署至「企業方案」網路。

- 如果您有自己的商業網路保存檔 (`.bna`)，請遵循[在企業方案上部署商業網路](../develop_enterprise.html)中的指示。
- 如果沒有自己的 `.bna` 檔案，請使用 `composer network download` 指令，以從「入門範本方案」實例擷取該檔案。如需 `composer network download` 指令的相關資訊，請參閱 [Hyperledger Composer 指令行文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}。然後，您可以遵循[在企業方案上部署商業網路](../develop_enterprise.html)中的指示。

### 重建網路配置
{: #migrate_starter_to_enterprise_config_network}

您可以在「企業方案」網路中，重建「入門範本方案」網路的組織（成員）、頻通和對等節點的配置。您可以使用「網路監視器」使用者介面，透過邀請適當的組織來重建這些網路資源（請注意，您無法如同在「入門範本」一般**切換**組織）、建立頻道，以及建立對等節點（受邀的組織必須重新建立自己的對等節點）。

1. 在 {{site.data.keyword.cloud_notm}} 上登入「企業方案」網路，並進入「網路監視器」。
2. 在「成員」畫面上重建組織（成員）、在「頻道」畫面上重建頻道，以及在「概觀」畫面上重建對等節點。如需建立網路資源的相關資訊，請參閱[使用網路監視器](../v10_dashboard.html#overview)。
3. 依照「入門範本方案」網路中的相同方式，新增成員和設定頻道原則來配置頻道。

**附註：**若要達到「高可用性」，您必須為組織建立至少兩個對等節點、將它們加入至相同的頻道，並在必要時適當地將程式碼用戶端應用程式從某個對等節點切換至另一個對等節點。

### 移轉鏈碼
{: #migrate_starter_to_enterprise_cc}

鏈碼是在本端環境之外開發，且由用戶端應用程式呼叫。若要將「入門範本方案」網路中測試的鏈碼安裝至「企業方案」網路上選取的對等節點，並實例化此鏈碼，請遵循[安裝、實例化及更新鏈碼](./install_instantiate_chaincode.html#installchaincode)中的指示。

### 更新用戶端應用程式
{: #migrate_starter_to_enterprise_app}

您需要使用「企業方案」網路的新 API 端點資訊，來更新已針對「入門範本方案」網路測試的現有用戶端應用程式。如需相關資訊，請參閱[擷取網路認證及連線設定檔](../get_start.html#retrieving-network-credentials-and-connection-profile)。

對於「高可用性」，用戶端應用程式必須負責偵測對等節點何時沒有回應，以及將交易遞送至不同的對等節點。

## 如何處理現有入門範本方案網路
{: #migrate_starter_to_enterprise_existing_network}

您可以繼續使用現有的「入門範本方案」網路作為沙盤推演環境，以包括新的專案，並測試現有鏈碼的變更。此外，您也可以在「入門範本方案」網路中維護未移轉至「企業方案」網路的測試分類帳資料。

在完成所有測試並驗證所有工作正常運作之前，請不要刪除您的「入門範本」網路。不過，一旦您不再需要「入門範本」網路以及其中的分類帳資料，就可以安全地刪除網路。如需相關資訊，請參閱[刪除或離開網路](../get_start_starter_plan.html#deleting-or-leaving-a-network)。

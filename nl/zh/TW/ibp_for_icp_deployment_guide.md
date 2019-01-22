---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform for ICP 部署手冊

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

基於 [Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.2/) 的區塊鏈網路可以部署於幾乎無限制的配置陣列，來支援最多的使用案例。儘管如此，仍有一些最佳作法，尤其是關於設定及部署網路元件的適當**順序**。

除了部署時所考量的最佳作法及事項之外，此部署手冊還會顯示在 {{site.data.keyword.cloud_notm}} Private (ICP) 上設定可運作之 {{site.data.keyword.blockchainfull}} Platform 網路的適當順序。不過，如果您計劃僅設定可運作的 CA、排序節點或對等節點，則基本規則仍適用。請注意，SOLO 排序服務（只會部署單一排序節點）不適用於正式作業環境。任何執行 SOLO 的網路或頻道都不能視為「正式作業」環境。不過，可以在正式作業環境中部署對等節點及 CA，尤其是當它們高度可用時。

**重要事項**：用於在 ICP 上部署 IBP 的處理程序十分困難，並假定需要有 Fabric 的高階專業知識。如果您不熟悉 Fabric、IBP 或 ICP，而且您的目標是設定開發環境或概念驗證，則請考量改為查看[入門範本方案](starter_plan.html)。另請注意，不支援每個潛在的部署配置。由於困難之故，因此假設 Fabric 專家會執行下列處理程序：先部署並將元件連接在一起，再將管理責任移交給其他參與方。這些專家是本手冊的目標讀者，也是一般針對 Helm 圖表元件所具有文件的目標讀者。

## 決定網路配置

區塊鏈網路的結構必須由使用案例指示。在不同的情況下，這些基礎商業決策會有所不同，但請仔細考量。

* 設定**開發環境**

  這是為其建置[入門範本方案](starter_plan.html)的環境，但是，如果您要在開發環境中管理自己的元件，則需要包含排序節點及對等節點的基本配置（每個組織各有一個 CA）。對於每個組織，您可能會決定僅具有單一排序節點或單一對等節點，類似於 [fabcar 網路](https://hyperledger-fabric.readthedocs.io/en/release-1.2/understand_fabcar_network.html)的配置。同樣地，您可能會決定只需要單一管理使用者來處理所有這些元件，而不是為每個元件建立個別的使用者。

* 部署新網路或現有網路的**正式作業元件**

  正式作業元件及正式作業網路，其需求與開發環境或概念驗證不同。首先，高可用性會成為優先順序。SOLO 排序服務（只包括單一排序節點，因此是單一失敗點）在定義上不適用於正式作業。

**附註：**此部署手冊不會執行每個反覆運算及潛在的網路配置，但需要考量一般準則及規則。

## 設定 ICP Kubernetes 叢集

決定網路結構之後，請在 ICP 上設定 Kubernetes 叢集，以因應您的使用案例。如需相關資訊，請參閱[設定 {{site.data.keyword.cloud_notm}} Private](ICP_setup.html)。

## 設定 CA

在 Fabric 型區塊鏈網路中，必須部署的第一個元件是 CA。這是因為元件的配置必須至少包括一個獲授權的使用者身分，才能在部署元件之前操作它。

這有時在概念上很難理解。例如，任何人在不熟悉時通常都可以存取電子裝置（例如筆記型電腦）。假設第一位開啟裝置的人員為「擁有者」，因此可以登錄並建立每個後續使用者必須知道的密碼。不過，這不是在 Fabric 型區塊鏈網路中設定元件的方式。對等節點及排序節點在第一次部署時，必須具有其內建的管理資訊。

**建議每個組織各部署一個 CA。**比方說，如果您部署與一個組織相關聯的三個對等節點，以及與另一個組織相關聯的一個排序節點，則需要部署兩個 CA。一個 CA 用於對等節點組織，另一個 CA 則用於排序節點組織。

為了防止 CA 的無限回歸（其中，每個 CA 都必須永遠地鏈結至另一個 CA），CA 具有與其相關聯的預設使用者名稱及密碼配對。部署 CA 時，您可以指定此使用者名稱及密碼。此 CA 是「主要 CA」，即信任元件的根源。在正式作業網路中，建議至少部署一個其他 CA，以從此主要 CA 取得其憑證，並使用稱為「中繼 CA」的 CA 將憑證發給使用者及元件。不過，在此版本中，不支援使用中繼 CA。

每個組織都必須有一個用於登記的 CA 以及一個 TLS CA。當您在 ICP 上部署 CA 時，依預設也會將 TLS CA 部署至相同的容器。此 TLS CA 會產生及管理 TLS 憑證。「入門範本方案」或「企業方案」網路上的 CA 未包含 TLS CA，但確實包含用於登記的 CA。因此，如果您計劃將對等節點連接至「入門範本方案」或「企業方案」網路，則在部署對等節點之前，也必須[在 ICP 中部署 CA](howto/CA_deploy_icp.html)，以充當該對等節點的 TLS CA。另請參閱[將 ICP 對等節點連接至 IBP 的必要條件](howto/peer_deploy_ibp#prerequisites-peer-ibp)。請注意，TLS CA 僅用於發出憑證，而且可能會在該活動完成時關閉。

如需 TLS 的相關資訊，請參閱「Hyperledger Fabric 文件」中的[使用傳輸層安全 (TLS) 保護通訊安全 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/enable_tls.html "使用傳輸層安全 (TLS) 保護通訊安全")。

### 準備排序節點及對等節點的 MSP

因為 IBP for ICP 處理程序十分複雜，所以建議在起始設定期間，使用單一管理使用者身分作為所有網路元件節點的管理者。這應該會減少部署及連線錯誤，方法是確定一位使用者可以設定各種元件之間的配置及連線，確保它們可以正常運作。不過，每個元件都有不同的憑證格外重要。這裡的區別有時可能很容易被遺漏。簽署交易提案的實體不是對等節點的管理者，而是**對等節點本身**。因此，對等節點必須予以登記，並且具有它附加到任何所執行業的憑證，以及可用來產生特定類型簽章的私密金鑰。如需 Fabric 型區塊鏈網路中身分及許可權的相關資訊，請參閱「Fabric 文件」中的[身分 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/identity/identity.html "身分") 及[成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/membership/membership.html "成員資格")。

Fabric CA 用戶端（應該遵循[關於操作 CA 的文件](howto/CA_operate.html#fabric-ca-client)中的指示予以安裝）用來登錄及登記身分。此身分是您將部署之網路元件的管理者，可讓您變更配置以及將元件連接在一起。如果您稍後要將這些元件的管理轉移給其他使用者，則可以登錄及登記這些元件的新管理者，並視需要移除您的管理者身分。

部署排序節點或對等節點時，與排序節點或對等節點相關聯的 `init` 容器將會使用 Kubernetes 密碼物件來建立元件的 MSP。若要瞭解如何建立密碼物件，請參閱[操作 CA](howto/CA_operate.html)。如上所述，請記住，您需要為每個組織設定 CA 並重複此流程。

## 部署排序節點及對等節點

建立 Kubernetes 密碼之後，即表示準備好部署元件。如果您的計劃是要建立頻道，則建議在任何對等節點之前部署排序節點。請務必針對所有元件使用不同的組織名稱。

- **[部署排序節點](howto/orderer_deploy_icp.html)**。請注意，現在只支援 SOLO 排序服務。您在部署處理程序中有與運算相關的選項。

- **[部署要連接至 ICP 排序節點的對等節點](howto/peer_deploy_icp.html)**。在 Helm 圖表中，可以指定用於部署對等節點的許多選項（包括資料庫類型）。請確保對等節點的組織 MSP ID 與排序節點的組織 MSP ID 不同。

- **[部署要連接至 IBP 網路的對等節點](howto/peer_deploy_ibp.html)**。在 {{site.data.keyword.cloud_notm}} 中部署對等節點並將它連接至[入門範本方案](starter_plan.html)或[企業方案](enterprise_plan.html)網路的處理程序會不同，因為它使用「連線設定檔」及「網路監視器」使用者介面。請注意，對等節點所屬的組織必須已加入網路中的頻道。同樣地，請確定對等節點的組織 MSP ID 與排序節點的組織 MSP ID 不同。

## 擴展網路

如果您的目標是設定開發環境或概念驗證，則您需要將對等節點組織新增至部署排序節點期間所建立的排序節點系統頻道。這是利用每個元件且在相關作業主題中記載的多步驟處理程序。

1. 您需要先建立組織並設定其 MSP ID，才能新增組織。請參閱[操作 CA](howto/CA_operate.html#deploy-orderer-peer)。

2. 建立組織之後，可以將它新增至排序節點系統頻道。如需相關資訊，請參閱[操作排序節點](howto/orderer_operate.html#add-organizations-to-consortium)。

3. 在排序節點系統頻道上列出組織時，它是「聯盟」的成員，並且可建立應用程式頻道（即在其上發生交易的頻道類型）。如需如何建立頻道的相關資訊，請參閱[操作對等節點](howto/peer_operate_icp.html#peer-icp-channeltx)。

---

copyright:
  years: 2017
lastupdated: "2017-09-04"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 控管網路
{: #getting-started-with-blockchain}

{{site.data.keyword.Bluemix_notm}} 上的「{{site.data.keyword.blockchainfull}} 平台」提供具有高度安全性、完整性、可調整性和效能的 {{site.data.keyword.blockchain}} 網路。您可以快速佈建全功能的 {{site.data.keyword.blockchain}} 網路，並使用「網路監視器」來立即執行鏈碼和應用程式，而不需要設計及配置專用區塊鏈網路。
{:shortdesc}
 
這個開始使用指導教學介紹所需要遵循的必要條件和步驟，以在 IBM 的高可用性安全環境中管理 {{site.data.keyword.blockchain}} 網路。  

下列步驟指出啟動具有多個成員組織的 {{site.data.keyword.blockchain}} 網路的基本流程：
1. **網路起始者**（一種特殊類型的網路成員）建立 {{site.data.keyword.blockchain}} 網路，並定義控管原則。接著，網路起始者可以邀請其他機構加入這個 {{site.data.keyword.blockchain}} 網路，成為網路成員。如需詳細資料，請參閱[建立網路](#creating-a-network)。
2. **網路成員**收到一封電子郵件通知，提供他們加入 {{site.data.keyword.blockchain}} 網路的指示。除了電子郵件通知中的指示，您也可以遵循[加入網路](#joining-a-network)中的步驟操作。
3. 在建立或加入網路之後，所有**網路成員**可以進入「網路監視器」（這是一個 GUI 儀表板）來配置及管理其網路資源。您可以設定含有一組網路成員的通道，以在通道特定的分類帳（僅限通道成員存取）上執行私人交易。在「網路監視器」中，您也可以將自己的對等節點加入通道，並在那些對等節點上安裝及實例化鏈碼。如需詳細資料，請參閱[配置網路資源和環境](#configuring-network-resources-and-environment)。
4. **應用程式開發人員**建立可與 {{site.data.keyword.blockchain}} 網路互動的應用程式。如需相關資訊，請參閱[使應用程式可與網路互動](#enabling-applications-to-interact-with-the-network)。
5. **網路操作員**使用「網路監視器」來監視其通道上的交易。如需詳細資料，請參閱[監視網路資源](#monitoring-network-resources)。

## 建立網路
開始之前，您需要在 {{site.data.keyword.Bluemix_notm}} 上建立「[{{site.data.keyword.blockchain}} 平台」服務實例 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain)。您需要使用您的 {{site.data.keyword.Bluemix_notm}} ID 登入。如果您沒有 ID，請按一下**註冊以建立**按鈕。將實例的服務和認證名稱重新命名，以便您未來能夠輕易辨識。選取您可以在其中部署 {{site.data.keyword.blockchain}} 網路的 {{site.data.keyword.Bluemix_notm}} 地區、組織和空間。然後從定價方案表格選取**企業方案**，並按一下**建立**按鈕。  

您可以在 [{{site.data.keyword.Bluemix_notm}} 服務儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/services "Bluemix 服務儀表板") 中找到您的「{{site.data.keyword.blockchain}} 平台」服務實例。  

如果您是網路起始者，請按一下**建立網路**按鈕，以起始 {{site.data.keyword.blockchain}} 網路。遵循精靈指示，完成網路和資源的基本配置。  
![建立網路精靈](images/create_network_name.png "建立網路精靈")  

1. 在「讓我們開始使用吧！」畫面中，為您的網路命名，選擇 {{site.data.keyword.Bluemix_notm}} 組織的位置，並新增您的機構名稱。當您邀請其他網路成員時，他們將會尋找此網路名稱來加入。按**下一步**。
2. （選用）在「邀請成員」畫面上，輸入您想要邀請加入網路之成員的機構名稱和電子郵件位址。您指定的機構名稱並不是正式稱號。它只是用來讓您輕易地辨識該機構，在成員加入網路之後，可再加以變更。請注意，一個網路最多可以有 15 個成員（包括您自己）。此為選用步驟，您之後可以在「網路監視器」中，邀請成員加入您的網路。按**下一步**。在您完成建立網路的所有步驟之後，您邀請的成員將會收到關於邀請的電子郵件通知。
3. 在「定義控管規則」畫面中，建立成員資格、通道建立和鏈碼的原則。依預設，所有網路成員都可以邀請其他成員加入網路、建立通道及實例化鏈碼。就此 GA 而言，您的網路是使用預設控管原則。按**下一步**。
4. 在「檢閱摘要」畫面中，驗證您的網路配置。如果您要進行修改，請按一下區段標頭旁邊的**編輯**，或按**上一步**按鈕，回到前一個畫面。完成網路配置後，請按一下**完成**。  
5. 您會在「已建立網路」畫面上收到通知，指出已順利建立您的網路。您可以按一下**新增對等節點**，以配置網路資源，或直接按一下**進入監視器**，以開啟「網路監視器」。如果不先新增對等節點，稍後可以在「網路監視器」中新增。如需對等節點的相關資訊，請參閱[概觀](v10_dashboard.html#overview)。
    
現在您已備妥，可開始部署能夠支援下列網路資源的 {{site.data.keyword.blockchain}} 網路：  
* 成員特定的憑證管理中心 (CA)
* 預設控管原則
* 最多 15 個網路成員  
* 三個排序節點和兩個中繼 CA 節點
* 每個成員最多 6 個小型對等節點  
* 損毀容錯排序服務
* 最多 150 個通道
* 最多 10 個鏈碼實例化作業


## 加入網路
與建立網路類似，您需要在 {{site.data.keyword.Bluemix_notm}} 上建立「[{{site.data.keyword.blockchain}} 平台」服務實例 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain)。您需要使用您的 {{site.data.keyword.Bluemix_notm}} ID 登入。如果您沒有 ID，請按一下**註冊以建立**按鈕。將實例的服務和認證名稱重新命名，以便您未來能夠輕易辨識。選取您可以在其中部署 {{site.data.keyword.blockchain}} 網路的 {{site.data.keyword.Bluemix_notm}} 地區、組織和空間。然後從定價方案表格選取**企業方案**，並按一下**建立**按鈕。  

您將能夠在 [{{site.data.keyword.Bluemix_notm}} 服務儀表板 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/services "Bluemix 服務儀表板") 中找到您的「{{site.data.keyword.blockchain}} 平台」服務實例。 

如果您是受邀的網路成員，請按一下**擱置邀請 ->** 按鈕，從下拉清單中選取您要加入的網路，然後按一下**加入網路！**按鈕。遵循精靈指示，檢視您網路的網路基本配置，並配置您自己的網路元件。  
![加入網路精靈](images/join_network_name.png "加入網路精靈")  

1. 在「讓我們開始使用吧！」畫面中，新增您的機構名稱，並驗證 {{site.data.keyword.Bluemix_notm}} 組織的位置。按**下一步**。
2. 在「檢閱控管規則」畫面中，檢視成員資格、通道建立和鏈碼的網路控管原則。按**下一步**。
3. （選用）在「新增對等節點」畫面中，選擇您要新增的對等節點大小和數量。按**下一步**。網路中的每個成員最多可以新增 3 個對等節點；在這個時間點上，只能使用「小型」對等節點。此為選用步驟，您之後可以在「網路監視器」中新增對等節點。如需對等節點的相關資訊，請參閱[概觀](v10_dashboard.html#overview)。
4. 在「檢閱網路摘要」畫面中，驗證網路配置。如果您要進行修改，請按一下區段標頭旁邊的**編輯**，或按**上一步**按鈕，回到前一個畫面。完成資源配置後，請按一下**完成**。您將收到您已順利加入網路的通知。接著，您就可以按一下**進入監視器**，以開啟「網路監視器」，或按一下**建立通道**，以起始通道建立要求。您之後可以在「網路監視器」中建立通道。如需相關資訊，請參閱[通道](v10_dashboard.html#channels)。
 

## 配置網路資源和環境

1. 在建立或加入 {{site.data.keyword.blockchain}} 網路之後，進入您的「網路監視器」。「網路監視器」是一種 GUI 儀表板，您可以在其中管理及追蹤網路狀態資訊。如需相關資訊，請參閱[網路監視器](v10_dashboard.html)。
2. 將您自己的對等節點新增至網路。如果您已新增足夠的對等節點，請跳過此步驟。對等節點會執行鏈碼，且其為與應用程式互動的端點。在「概觀」畫面中按一下**新增對等節點**，然後選取對等節點的數量和大小。如需相關資訊，請參閱[概觀](v10_dashboard.html#resources)。
3. 設定通道。相同通道中的所有成員都會佈建通道特定的分類帳，其可提供資料隔離和機密性。如需如何建立通道的相關資訊，請參閱[建立通道](howto/create_channel.html#creating-a-channel)。  
	
	如果您是受邀加入通道的通道成員，您會收到電子郵件通知，其中的鏈結會連接至可讓您加入通道的精靈。
4. 將對等節點加入通道。唯有與通道相關聯的對等節點可以存取其分類帳。如需相關資訊，請參閱[通道](v10_dashboard.html#channels)。
5. 安裝及實例化鏈碼。所有通道成員都需要在將執行鏈碼的每一個對等節點上，安裝具有相同名稱和版本的相同鏈碼。安裝鏈碼之後，必須先在通道上將其實例化，才能加以使用。如需相關資訊，請參閱[安裝及實例化鏈碼](howto/install_instantiate_chaincode.html)。  

**附註**：若要達到高可用性，每個機構必須購買至少 2 個對等節點，而在通道中，每個參與的機構必須加入至少 2 個對等節點。

## 使應用程式可與網路互動
應用程式會利用 SDK API 來與您的 {{site.data.keyword.blockchain}} 網路元件互動。您必須在應用程式中新增網路元件的 API 端點資訊，這樣應用程式最終才能以您的對等節點作為交易要求的目標。然後，您可以從「網路監視器」新增 API 端點資訊。應用程式可以在您的本端檔案系統或 {{site.data.keyword.Bluemix_notm}} 上進行管理。如需相關資訊，請參閱[開發應用程式](v10_application.html)。

## 監視網路資源  
從您的應用程式觸發交易之後，您可以在「網路監視器」中檢視交易狀態資訊。如需網路監視的相關資訊，請參閱[監視網路](howto/monitor_network.html)。

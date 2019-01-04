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

# 付費模式
{: #paying-mode}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform 會每月收取會員費和對等節點費用。網路成員可以使用自己的 {{site.data.keyword.cloud_notm}} 帳戶（包含用來建立網路實例的空間）來付費。或者，可以由某位網路成員負責網路中其他成員的帳單，或甚至所有成員都要支付整個網路的費用。必要的話，付款人還可以將帳單轉讓給其他成員。
{:shortdesc}

依據您選擇的網路方案，以及您使用的資源數量，支付的款項會有所不同。如需定價的相關資訊，請參閱[定價](pricing.html)。

## 必要條件
{: #prereq}

每個網路成員都必須有一個付費 {{site.data.keyword.cloud_notm}} 帳戶（例如，**隨收隨付制**帳戶），他們才可以在 {{site.data.keyword.cloud_notm}} 上建立 {{site.data.keyword.blockchainfull_notm}} Platform 服務實例。如果您沒有帳戶，請[登錄](https://console.bluemix.net/registration/)帳戶，並將它升級為付費帳戶。若要將帳戶升級至「隨收隨付制」類型，請移至 {{site.data.keyword.cloud_notm}} 主控台中的**管理** > **計費及用量** > **計費**，然後按一下**新增信用卡**。


## 支付自己的費用
{: #pay-your-own}

在 {{site.data.keyword.blockchainfull_notm}} Platform 上建立或加入任何區塊鏈網路之後，即會向您的 {{site.data.keyword.cloud_notm}} 帳戶收取您的成員資格費用和對等節點費用。


## 支付其他網路成員的費用
{: #pay-for-others}

可以由網路中的某個成員來負責同一個區塊鏈網路中一個以上其他網路成員的費用。若要以這種模式付費，付款人和其他網路成員必須完成下列步驟。

1. 付款人在 {{site.data.keyword.cloud_notm}} 中針對付款人將為其付費的成員建立個別的 Cloud Foundry 空間。
   1. 登入 {{site.data.keyword.cloud_notm}}。
   2. 從功能表列中按一下**管理** > **帳戶** > **Cloud Foundry 組織**。
   3. 按一下將建立網路之「Cloud Foundry 組織」的**檢視詳細資料**。如果該網路尚無組織，請按一下**新增 Cloud Foundry 組織**按鈕來建立一個組織。
   4. 按一下**新增 Cloud Foundry 空間**按鈕，為另一個網路成員建立空間。選取該空間的地區、為其命名，然後按一下**新增**。請注意，只有空間建立者可以存取所建立的空間。  
   **附註**：如果付款人為多個成員付費，則付款人需要重複此步驟，針對付款人將為其付費的每一個成員建立個別空間。
2. 付款人邀請其他成員加入付款人的 {{site.data.keyword.cloud_notm}} 帳戶，並提供其特定空間的存取權。請確定每個成員只能存取一個空間。
   1. 從功能表列中按一下**管理** > **帳戶** > **使用者**。  
   2. 按一下**邀請使用者**，並指派使用者存取權：
      1. 指定您將邀請加入「Cloud Foundry 組織」之單一使用者的電子郵件位址。
      2. 在 **Cloud Foundry 存取**區段中，選取下列項目：
         - **組織**：要建立網路的「Cloud Foundry 組織」。
         - **組織角色**：審核員。
         - **地區**：您為此使用者建立空間的地區。
         - **空間**：您為此使用者建立的空間。
         - **空間角色**：開發人員。
      3. 按一下**邀請使用者**。  
   **附註**：如果付款人為多個成員付費，則付款人需要重複此步驟以邀請其他成員，並為其中每一個成員指派存取權。
3. 付款人即會建立區塊鏈網路 {{site.data.keyword.blockchainfull_notm}} Platform，並邀請其他成員加入網路的「網路監視器」中的區塊鏈網路。如需邀請網路成員的相關資訊，請參閱[成員](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members)。
4. 然後，每個成員會收到通知電子郵件，其中包含加入網路的邀請。成員可以完成下列步驟來加入網路。
   1. 按一下通知電子郵件中的「繼續」按鈕，您將會進入 {{site.data.keyword.cloud_notm}} 中的區塊鏈服務頁面。
   2. 登入 {{site.data.keyword.cloud_notm}}，並確定您使用正確的 {{site.data.keyword.cloud_notm}} 組織和空間（付款人將您新增至其中）。
      1. 在 {{site.data.keyword.cloud_notm}} 主控台中，按一下右上角的設定檔虛擬人像。
      2. 從**帳戶**下拉清單選擇付款人的帳戶。請注意，我們將會針對該帳戶以及您佈建區塊鏈服務實例的組織收費。  
   3. 在 {{site.data.keyword.cloud_notm}} 型錄中，搜尋 **Blockchain** 並建立服務實例。
      1. 在**服務名稱**欄位中，鍵入未來用於識別的敘述性名稱。
      2. 驗證組織和空間是付款人將您新增至其中的組織和空間。
      3. 選取要用來建立網路實例的成員資格方案。您的成員資格方案必須與付款人建立網路時所使用的成員資格方案相同。
      4. 按一下**建立**。
   4. 建立區塊鏈服務實例之後，請遵循精靈的指示加入網路。如需相關資訊，請參閱[加入網路](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network)。

### 已知限制
{: #pay-for-others-limitation}
- 因為所有成員都包含在付款人的 {{site.data.keyword.cloud_notm}} 帳戶中，所以付款人可以存取所有成員的區塊鏈實例，並可假冒其身分。因此，這種付費模式最適合用於 PoC 環境，或是由付款人處理 {{site.data.keyword.blockchainfull_notm}} Platform 所有管理工作，並只提供成員應用程式的情況。  
- 付款人的「Cloud Foundry 組織」中的任何成員，都可以查看該組織中的所有空間，但無法編輯或修改組織，因為他們沒有存取權。  
- 將所有成員新增至付款人的 {{site.data.keyword.cloud_notm}} 帳戶，並為其指派佈建區塊鏈實例和加入網路的存取權之後，付款人同時也賦予成員建立其他服務的存取權，而這會產生額外的費用。所有成員都會簽署一份信任合約，他們不會在其帳戶中建立任何未獲授權的服務，從而導致付款人帳戶的額外成本。  

## 讓成員為您付費
{: #let-other-pay}

將其他成員的帳戶新增至付款人帳戶的替代方案，即其他成員也可以**帳單管理員**身分，將付款人帳戶新增至其帳戶。若要以這種模式付費，付款人和其他網路成員必須完成下列步驟。

1. 付款人使用付款人的帳戶來建立區塊鏈網路 {{site.data.keyword.blockchainfull_notm}} Platform。
2. 其他成員以**帳單管理員**身分，將付款人帳戶新增至其 {{site.data.keyword.cloud_notm}} 帳戶。
  1. 從 {{site.data.keyword.cloud_notm}} 主控台頂端的功能表列中，按一下**管理** > **帳戶** > **使用者**。
  2. 在**使用者**頁面上，按一下**邀請使用者**按鈕。
  3. 在**電子郵件位址**欄位下新增付款人的帳戶。
  4. 在 **Cloud Foundry 存取**區段下，從下拉清單中選擇您要將付款人帳戶新增至其中的組織。
  5. 選擇**帳單管理員**作為**組織角色**。
  6. 相應地配置其他欄位，然後按一下**邀請使用者**。  
3. 付款人即會邀請其他成員加入網路的「網路監視器」中的區塊鏈網路。如需邀請網路成員的相關資訊，請參閱[成員](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members)。
4. 然後，每個成員會收到通知電子郵件，其中包含加入網路的邀請。然後，成員就可以加入網路。如需相關資訊，請參閱[為其他網路成員付費](#pay-for-others)下的**步驟 4**。

### 已知限制
{: #let-other-pay-limitation}

以帳單管理員身分將付款人的 {{site.data.keyword.cloud_notm}} 帳戶新增至其他成員帳戶，而且其他成員使用其帳戶來購買任何其他服務之後，該付款人也需要涵蓋那些費用。所有成員都會簽署一份信任合約，他們不會在其帳戶中建立任何未獲授權的服務，從而導致付款人帳戶的額外成本。  


## 將帳單轉讓給其他成員
{: #transfer-billing}

雖然網路成員可有為其付費的成員，但其他成員稍後可以接管帳單。您可以從帳戶中移除付款人的帳戶，讓付款人可以停止為您付款。

1. 從 {{site.data.keyword.cloud_notm}} 主控台頂端的功能表列中，按一下**管理** > **帳戶** > **Cloud Foundry 組織**。
2. 按一下您將付款人帳戶新增至其中的組織。
3. 切換至**使用者**標籤。
4. 在使用者表格中尋找付款人的帳戶，然後按一下**從組織中移除**鏈結。

從您的帳戶中移除付款人帳戶之後，您的帳戶將支付您的區塊鏈網路成員資格費用及對等節點費用。

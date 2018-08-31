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

# 付費模式

「{{site.data.keyword.blockchainfull}} 平台」會每月收取會員費和對等節點費用。網路成員可以使用自己的 IBM Cloud 帳戶（包含用來建立網路實例的空間）來付費。或者，可以由某位網路成員負責網路中所有成員的帳單，並支付整個網路的費用。{:shortdesc}

依據您選擇的網路方案，以及您使用的資源數量，支付的款項會有所不同。如需定價的相關資訊，請參閱[定價](pricing.html)。

## 必要條件
每個網路成員都必須有一個付費 {{site.data.keyword.cloud_notm}} 帳戶（例如，**隨收隨付制**帳戶）。如果您沒有帳戶，請[登錄](https://console.bluemix.net/registration/)帳戶，並將它升級為付費帳戶。若要將帳戶升級至「隨收隨付制」類型，請移至 IBM Cloud 主控台中的**管理** > **計費及用量** > **計費**，然後按一下**新增信用卡**。


## 支付自己的費用
在建立具有「{{site.data.keyword.blockchainfull_notm}} 平台」的任何區塊鏈網路之後，即會按日向您的 {{site.data.keyword.cloud_notm}} 帳戶收取您的會員費和對等節點費用。 


## 支付整個網路的費用
可以由網路中的某個成員來負責所有網路成員的費用。若要以這種單一付款人的模式付費，付款人和其他網路成員必須完成下列步驟。

1. 付款人在 {{site.data.keyword.cloud_notm}} 中為將受邀加入網路的每一個使用者建立個別的 Cloud Foundry 空間：
   1. 登入 {{site.data.keyword.cloud_notm}}。
   2. 從功能表列中按一下**管理** > **帳戶** > **Cloud Foundry 組織**。
   3. 按一下將建立網路之「Cloud Foundry 組織」的**檢視詳細資料**。如果該網路尚無組織，請按一下**新增 Cloud Foundry 組織**按鈕來建立一個組織。
   4. 按一下**新增 Cloud Foundry 空間**按鈕，為某個網路成員建立空間。選取該空間的地區、為其命名，然後按一下**新增**。請注意，只有空間建立者可以存取所建立的空間。
   5. 重複步驟 4，為受邀加入網路的所有使用者建立個別的空間。
2. 付款人邀請其他使用者加入付款人的 {{site.data.keyword.cloud_notm}} 帳戶，並提供其特定空間的存取權。每個使用者只能存取一個空間。
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
   3. 重複邀請使用者的步驟，並為每個使用者指派使用者存取權。
3. 在「{{site.data.keyword.blockchainfull_notm}} 平台」儀表板中，付款人邀請使用者加入區塊鏈網路。如需邀請網路成員的相關資訊，請參閱[成員](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members)。
4. 然後，每個使用者會收到通知電子郵件，其中包含加入網路的邀請。使用者可以完成下列步驟來加入網路。
   1. 按一下通知電子郵件中的「繼續」按鈕，您將會進入 {{site.data.keyword.cloud_notm}} 中的區塊鏈服務頁面。
   2. 確定您使用正確的 {{site.data.keyword.cloud_notm}} 組織和空間。
      1. 登入 {{site.data.keyword.cloud_notm}}，然後按一下右上角的設定檔虛擬人像。
      2. 從**帳戶**下拉清單選擇付款人的帳戶。請注意，我們將會針對該帳戶以及您佈建區塊鏈服務實例的組織收費。  
   4. 在區塊鏈服務頁面上，建立您的區塊鏈服務實例：
      1. 在**服務名稱**欄位中，鍵入未來用於識別的敘述性名稱。
      2. 驗證組織和空間是付款人邀請您加入的組織和空間。
      3. 選取要用來建立網路實例的成員資格方案。
      4. 按一下**建立**。
   5. 建立區塊鏈服務實例之後，請遵循精靈的指示加入網路。如需相關資訊，請參閱[加入網路](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network)。

### 已知限制
- 因為所有成員都包含在付款人的 {{site.data.keyword.cloud_notm}} 帳戶中，所以付款人可以存取所有成員的區塊鏈實例，並可假冒其身分。因此，這種付費模式最適合用於 POC 環境，或是由付款人處理「{{site.data.keyword.blockchainfull_notm}} 平台」所有管理工作，並只提供成員應用程式的情況。  
- 將所有成員新增至付款人的 {{site.data.keyword.cloud_notm}} 帳戶，並為其指派佈建區塊鏈實例和加入網路的存取權之後，付款人同時也賦予成員建立其他服務的存取權，而這會產生額外的費用。  
- 付款人的「Cloud Foundry 組織」中的任何成員，都可以查看該組織中的所有空間，但無法編輯或修改組織，因為他們沒有存取權。

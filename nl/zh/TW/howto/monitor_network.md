---

copyright:
  years: 2017
lastupdated: "2017-07-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 監視 {{site.data.keyword.blockchain}} 網路

本指導教學顯示如何在 {{site.data.keyword.Bluemix_short}} 上檢視及監視 {{site.data.keyword.blockchain}} 網路的狀態資訊。
{:shortdesc}

進入「網路監視器」，並且在「通道」畫面中找出您要檢視及監視的通道。在特定通道畫面中，您可以在三個標籤中檢視此通道的資料狀態資訊、成員和實例化的鏈碼：

* **通道概觀**  
「通道概觀」標籤顯示此通道的區塊資訊：
    * 一系列資料點，包括已建立的區塊總數、自前次交易以來的時間間隔、鏈碼實例化數目，以及鏈碼呼叫數目。
    * 一份表格，列出此通道上的所有區塊。展開區塊，即可檢視該區塊的詳細資訊。  

  ![通道概觀](../images/channel_overview_detail.png "通道概觀")  

* **成員**  
「成員」標籤顯示此通道之成員的資訊，包括組織操作員的電子郵件位址。
  ![通道成員](../images/channel_members.png "通道成員")  
  
* **鏈碼**  
「鏈碼」標籤列出此通道上已實例化的所有鏈碼，以及鏈碼 ID、版本和正在執行該鏈碼的對等節點數目。   
    
  展開鏈碼列，以取得鏈碼的詳細資訊：  
    * 您可以按一下 **JSON**，以檢視鏈碼的 JSON 檔案。
    * 您可以按一下**日誌**，以檢視鏈碼的日誌。
    * 您可以按一下**刪除**，以移除執行中的鏈碼容器。
  
  ![通道鏈碼](../images/channel_chaincode.png "通道鏈碼") 
  

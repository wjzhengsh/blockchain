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

# 監視區塊鏈網路

本指導教學顯示如何在 {{site.data.keyword.cloud_short}} 上檢視及監視 {{site.data.keyword.blockchain}} 網路的狀態資訊。
{:shortdesc}


## 監視對等節點、排序及 CA
{: #monitor-nodes}

您可以對其中一個網路節點發出 HTTP **HEAD** 要求，以檢查節點狀態。網路節點可以是區塊鏈網路中的對等節點、排序節點或 CA。**HEAD** 要求類似於 GET 要求，而且只會傳送不含內文的標頭。如果節點正常運作，則您會取得 200 回應。

1. 在「網路監視器」的「概觀」畫面中，按一下**連線設定檔**。然後，您可以按一下**原始 JSON** 以在 Web 瀏覽器中檢視連線設定檔，或按一下**下載**以在本端儲存連線設定檔。
2. 在連線設定檔中，尋找您要檢查之網路節點的 URL 資訊。例如，`fabric-orderer-20190b` 排序節點的 URL 是 `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`。  
    ![排序節點 URL 範例](../images/orderer_url.png "排序節點 URL 範例")
3. 將 URL 中的 **grpcs** 取代為 **https**。在上述範例中，URL 會變成 `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`。
4. 使用工具（例如 curl 或 Chrome Postman 應用程式），對 URL 發出 **HEAD** 要求。
    - 如果您取得 200 狀態回應，表示您的網路節點正常運作。
    - 如果 **HEAD** 要求因連線錯誤而失敗，則您的網路節點可能未執行、節點 URL 錯誤，或防火牆封鎖您對節點的存取。您必須解決此錯誤；否則，您的應用程式會無法連接節點。

下列範例顯示 curl 中具有 200 回應的 **HEAD** 要求。請注意，您可以忽略 grpc 錯誤，因為 HTTP **HEAD** 要求會檢查是否可存取節點。如果是，則對節點的 grpc 要求也會作用於您的應用程式。

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

下列範例顯示 curl 中具有連線錯誤的 **HEAD** 要求。

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

下圖顯示 Chrome Postman 應用程式中具有 200 回應的 **HEAD** 要求。  

![HEAD 要求 Postman 範例](../images/orderer_head_postman.png "HEAD 要求 Postman 範例")


## 監視頻道
{: #monitor-channnels}

進入「網路監視器」，並且在「頻道」畫面中找出您要檢視及監視的頻道。在特定頻道畫面中，您可以在三個標籤中檢視此頻道的資料狀態資訊、成員和實例化的鏈碼：

* **頻道概觀**  
「頻道概觀」標籤顯示此頻道的區塊資訊：
    * 一系列資料點，其中包括已建立的區塊總數、自前次交易以來的時間間隔、鏈碼實例化數目，以及鏈碼呼叫數目。
    * 一份表格，列出此頻道上的所有區塊。展開區塊，即可檢視該區塊的詳細資訊。  

  ![頻道概觀](../images/channel_overview_detail.png "頻道概觀")  

* **成員**  
「成員」標籤顯示此頻道之成員的資訊，包括組織操作員的電子郵件位址。
  ![頻道成員](../images/channel_members.png "頻道成員")  

* **鏈碼**  
 「鏈碼」標籤列出此頻道上已實例化的所有鏈碼，以及鏈碼 ID、版本和正在執行該鏈碼的對等節點數目。   

  展開鏈碼列，以取得鏈碼的詳細資訊：  
    * 您可以按一下 **JSON**，以檢視鏈碼的 JSON 檔案。
    * 您可以按一下**日誌**，以檢視鏈碼的日誌。
    * 您可以按一下**刪除**，以移除執行中的鏈碼容器。**附註**：刪除執行中鏈碼容器，實際上並不會刪除鏈碼。無法刪除區塊鏈網路上的實例化鏈碼。

  ![頻道鏈碼](../images/channel_chaincode.png "頻道鏈碼")


## 監視鏈碼
{: #monitor-chaincode}

進入「網路監視器」，並開啟「安裝程式碼」畫面。如果您有執行中的鏈碼，請從下拉清單中選擇對等節點，就會在表格中看到此對等節點的所有鏈碼，以及鏈碼 ID 和版本。您可以在這個畫面上執行鏈碼的安裝和實例化作業。如需相關資訊，請參閱[安裝、實例化及更新鏈碼](install_instantiate_chaincode.html)。

  ![鏈碼](../images/chaincode_install_overview.png "鏈碼")


## 監視範例應用程式
{: #monitor-apps}

在「入門範本方案」網路中，您可以在「網路監視器」的「嘗試範例」畫面中檢視及存取範例應用程式。部署範例應用程式之後，您可以按一下**啟動**按鈕，以進入您的應用程式介面，或按一下**在 GitHub 上檢視**鏈結，以造訪程式碼儲存庫。如需相關資訊，請參閱[部署範例應用程式](prebuilt_samples.html)。

  ![範例應用程式](../images/sampleappflow0.png "範例應用程式")

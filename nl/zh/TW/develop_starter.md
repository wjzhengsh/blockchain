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
{:tip: .tip}

# 在入門範本方案上部署商業網路
{: #deploying-a-business-network}

您可以使用「{{site.data.keyword.blockchainfull}} 平台」開發人員環境和 Hyperledger Composer 開發人員工具集來開發商業網路，並將其部署至「入門範本方案」環境。

使用開發人員環境可讓您快速模型化及測試 {{site.data.keyword.blockchain}} 商業網路，並將其部署至「{{site.data.keyword.blockchainfull_notm}} 平台」的實例。

## 開始之前

請務必閱讀[關於入門範本方案](./starter_plan.html)和[開始使用入門範本方案](./get_start_starter_plan.html)。另外，也請確定您已遵循[控管入門範本方案網路](./get_start_starter_plan.html)中的指示，安裝[「{{site.data.keyword.blockchainfull_notm}} 平台」開發人員環境](./develop_install.html)，並建立「{{site.data.keyword.blockchainfull_notm}} 平台入門範本方案」的實例。請確定您具有 Node 8.9 版或更新版本、npm 5.x 版及 Hyperledger Composer 0.19.x 版。


## 步驟 1：擷取管理者密碼

1. 從「入門範本方案」概觀畫面中，按一下**連線設定檔**，然後下載。將此檔案重新命名為 'connection-profile.json'。

2. 將此檔案移至與 `.bna` 檔案相同的目錄中。

3. 在連線設定檔內，一直往下，直到您看到 'registrar'。在 'registrar' 的 'enrollId' 下，有 **enrollSecret** 內容。請擷取該密碼，並儲存一份副本。

    ![D8KBag](https://i.makeagif.com/media/4-12-2018/D8KBag.gif)


## 步驟 2：建立憑證管理中心卡

在前一個步驟中擷取的密碼，將會用來建立憑證管理中心 (CA) 的商業網路卡。然後會匯入該 CA 卡，並使用該卡來將 **enrollSecret** 交換成「入門範本方案」憑證管理中心提供的有效憑證。

1. 使用在步驟 1 中記下的 **enrollSecret**，執行下列指令來建立 CA 商業網路卡：

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

將前述指令中的 `enrollSecret` 取代為從連線設定檔中擷取的管理者密碼。

2. 使用下列指令來匯入該卡：

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. 現在已匯入該卡，接著就可以使用它來將 **enrollSecret** 交換成 CA 提供的有效憑證。請執行下列指令，以向憑證管理中心要求憑證：

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

將前述指令中的 `enrollSecret` 取代為從連線設定檔中擷取的管理者密碼。`composer identity request` 指令會建立 `credentials` 目錄，其中包含憑證的 `.pem` 檔案。

## 步驟 3：將憑證新增至入門範本方案實例

憑證必須新增至「入門範本方案」實例。為了方便起見，可以使用「{{site.data.keyword.blockchainfull_notm}} 平台使用者介面」予以新增。必須新增憑證，接著必須重新啟動對等節點，然後必須將頻道上的憑證同步化。所需的憑證是從先前的指令產生的 `admin-pub.pem` 檔案，其位於 `credentials` 目錄中。

1. 在「入門範本方案使用者介面」中，按一下**成員**標籤，並按一下**憑證**，然後按一下**新增憑證**。移至 `credentials` 目錄，然後在憑證方框中複製並貼上 `admin-pub.pem` 檔案的內容。提交憑證，並重新啟動對等節點。附註：重新啟動對等節點需要一分鐘。

    ![jlEb2y](https://i.makeagif.com/media/4-12-2018/jlEb2y.gif)

2. 接下來，必須將頻道上的憑證同步化。按一下**頻道**標籤，並按一下**動作**按鈕，然後按一下**同步化憑證**及**提交**。

    ![E-sVV5](https://i.makeagif.com/media/4-12-2018/E-sVV5.gif)

## 步驟 4：建立管理者商業網路卡

現在已將正確的憑證與對等節點同步化，接著就可以建立商業網路卡，使其具有安裝 Hyperledger Composer 運行環境及啟動鏈碼的許可權。

1. 使用下列指令，以頻道管理者角色和對等節點管理者角色來建立管理卡：

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   此卡會用來將商業網路部署至「入門範本方案」。

2. 使用下列指令，匯入在前一個步驟中建立的管理卡：

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

## 步驟 5：安裝及啟動商業網路

接著，可以使用在前一個步驟中建立的管理卡來安裝及啟動商業網路。在此手冊中，我們將安裝車輛製造網路範例，請使用車輛製造範例，或安裝您自己的商業網路，但務必變更指令中所指定的商業網路名稱。用來啟動商業網路的指令也會建立一張卡。就「入門範本方案」而言，此卡必須刪除，所給定的範例指令將此卡命名為 `delete_me.card`，所以很容易分辨。

1. 使用下列指令來安裝 Hyperledger Composer 運行環境：

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   請記下執行此指令時所傳回的商業網路版本號碼。下一步驟中，將需要它。

2. 使用下面的指令來啟動商業網路。如果您收到錯誤，請稍候，然後再試一次。在 `-V` 選項之後，使用最後一個步驟中的版本號碼。

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. 刪除名為 `delete_me.card` 的商業網路卡。

4. 使用下列指令來建立新的商業網路卡，並參照先前擷取的憑證：

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. 使用下列指令來匯入商業網路卡：

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

商業網路現在已部署至「入門範本方案」實例。

## 步驟六：對商業網路進行連線測試，以確保其正確執行

1. 執行下列指令，以對商業網路進行連線測試：

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

若要檢視鏈碼日誌，請按一下**頻道**，然後選取您的頻道。按一下下拉箭頭以檢視日誌，或按一下「動作」符號更詳細地進行檢視。

![fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif)

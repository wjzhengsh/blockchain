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
{:tip: .tip}

# 在企業方案上部署商業網路
{: #deploying-a-business-network}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop 的開發人員工具可協助您建立**商業網路定義**，然後再將其包裝在商業網路保存檔 (`.bna`) 中。開發人員環境可讓您將 `.bna` 檔案部署至本端或雲端 {{site.data.keyword.blockchain}}，以供開發及共用。

此指導教學是要處理商業網路生命週期的下一個步驟，也就是將 `.bna` 部署至 {{site.data.keyword.blockchainfull_notm}} Platform 企業方案，以啟動您的商業網路。

## 開始之前

請確定您已安裝 {{site.data.keyword.blockchainfull_notm}} 開發人員環境，並可對開發及部署商業網路應付自如。如需撰寫商業網路的指引，請參閱 [Hyperledger Composer 文件](https://hyperledger.github.io/composer/latest/business-network/business-network-index)。

您需要具有 {{site.data.keyword.blockchainfull_notm}} Platform 的「企業方案」實例存取權，以及具有預先建立對等節點的存取權。如需 {{site.data.keyword.blockchainfull_notm}} Platform 企業方案的相關資訊，請參閱[企業方案概觀](./enterprise_plan.html)。

## 步驟一：建立 {{site.data.keyword.blockchainfull_notm}} Platform 的連線設定檔

1. 建立一個目錄來儲存您的連線詳細資料，例如：

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    每個連線設定檔都應該包含 `connection.json` 檔案。請在 `.composer-connection-profiles` 之下建立新的目錄，在此實例中為 `bmx-hlfv11`。這將會是您在使用 Hyperledger Composer 和 {{site.data.keyword.blockchainfull_notm}} Platform 時，所要使用的設定檔名稱。

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    您現在應該有下列目錄結構：

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

## 步驟二：擷取連線設定檔

1. 從 {{site.data.keyword.blockchainfull_notm}} Platform 儀表板中，依序選取**概觀**、**連線設定檔**及**下載**按鈕，以擷取連線設定檔。

2. 將連線設定檔儲存到前一個步驟所建立的目錄結構中。請將它命名為 **connection.json**。

## 步驟三：新增頻道資訊

1. 在 `connection.json` 中新增頻道值，以符合您計劃要建立並將商業網路部署至其中的頻道名稱。在提供的範例連線設定檔範本中，channels 元素如下：`"channels": {},`。

## 步驟四：準備對等節點

**請注意**：在您建立要用來部署商業網路的頻道之前，**必須**先執行此步驟。如果在建立頻道之後才執行此步驟，所部署的商業網路**可能不會啟動**。

在「連線設定檔」文件的 **certificateAuthorities** 之下，有一個 **registrar** 屬性包含 **enrollId** 及 **enrollSecret** 的屬性，格式如下：

 ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```

1. 使用下列指令，為 CA 建立商業網路卡：

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  請確定您是在與連線設定檔相同的目錄中執行指令。

2. 使用下列指令來匯入商業網路卡：

    ```
   composer card import -f ca.card -c ca
   ```
    {:codeblock}

3. 匯入該卡之後，就可以使用下列指令，利用它從憑證管理中心獲得憑證：

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    `composer identity request` 指令會建立包含相關憑證檔案的 credentials 目錄。

4. 從導覽功能表中選取**成員**，然後選取**憑證**功能表選項，再按一下**新增憑證**按鈕。

5. 在**名稱**欄位中輸入此憑證的唯一名稱，此名稱不能包含橫線或連字號。

6. 開啟先前建立的 `admin-pub.pem` 檔案，並將此檔案的內容複製到**金鑰**欄位中，然後按**提交**按鈕。

7. 透過使用者介面來停止對等節點，然後啟動對等節點。

8. 現在，該憑證應該會出現在憑證清單中。

## 步驟五5：建立頻道

1. 從左畫面的導覽功能表中選取**頻道**，然後按一下**新建頻道**按鈕。

2. 輸入「頻道名稱」和選擇性說明，並按**下一步**。請注意，「頻道名稱」必須符合您在連線設定檔中為頻道屬性指定的名稱。

3. 依需要提供許可權，並按**下一步**。

4. 選取需要接受頻道更新項目的操作員數目原則，並提交要求。

5. 您現在應該會進入**通知**區段，其中應該會有一個需要檢閱的新要求。按一下**檢閱要求**按鈕。

6. 按一下**接受**按鈕，您會再回到**通知**區段，在其中將看到現在可以提交要求了。按一下**提交要求**按鈕，以開啟提交對話框，然後按一下**提交**按鈕。新頻道即已建立。

7. 從導覽功能表中選取**頻道**。新頻道會出現在頻道清單中，並且應該會有「尚未新增對等節點」。按一下它旁邊的動作功能表，然後選取**加入對等節點**。勾選您要新增之「對等節點」旁邊的勾選框，然後按**新增選定項目**。

## 步驟六：建立新身分來管理商業網路

使用所要求的憑證來建立商業網路卡。此商業網路卡有權將鏈碼安裝在具有您所上傳之公用憑證的對等節點上，並且會成為憑證管理中心的發證者。

1. 若要建立該卡，請執行下列指令：

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    其中 `bmx-hlfv11` 是您先前建立的設定檔名稱。

2. 建立該卡之後，請使用下列指令將其匯入：

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    現在我們已準備好將 `.bna` 檔案部署至 {{site.data.keyword.blockchainfull_notm}} Platform。


## 步驟七：部署商業網路

現在您可以將 `.bna` 檔案部署至 {{site.data.keyword.blockchainfull_notm}} Platform。

1. 若要使用前一個步驟所建立的卡片，您需要先安裝後啟動商業網路。請使用下列指令來安裝商業網路：

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. 安裝商業網路之後，請使用下列指令來啟動商業網路：

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. 若要檢查是否已正確部署商業網路，請建立可用來對網路進行連線測試的身分及相關聯的商業網路卡。

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. 使用下列指令來匯入商業網路卡：

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. 對網路進行連線測試，以檢查網路是否正在執行：

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

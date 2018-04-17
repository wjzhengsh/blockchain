---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 在企業方案上部署商業網路

「{{site.data.keyword.blockchainfull}} 平台：開發」的開發人員工具可協助您建立**商業網路定義**，然後再將其包裝在商業網路保存檔 (`.bna`) 中。開發人員環境可讓您將 `.bna` 檔案部署至本端或雲端 {{site.data.keyword.blockchain}}，以供開發及共用。

此指導教學是要處理商業網路生命週期的下一個步驟，也就是將 `.bna` 部署至「{{site.data.keyword.blockchainfull_notm}} 平台企業方案」，以啟動您的商業網路。

## 開始之前

請確定您已安裝「{{site.data.keyword.blockchainfull}}：開發」開發人員環境，並可對開發及部署商業網路應付自如。如需撰寫商業網路的指引，請參閱 [Hyperledger Composer 文件](https://hyperledger.github.io/composer/latest/business-network/business-network-index)。

您需要有「{{site.data.keyword.blockchainfull_notm}} 平台」的「企業方案」實例存取權。如需「{{site.data.keyword.blockchainfull_notm}} 平台企業方案」的相關資訊，請參閱[企業方案概觀](./enterprise_plan.html)。

## 步驟 1：建立「{{site.data.keyword.blockchainfull_notm}} 平台」的連線設定檔

1. 建立一個目錄來儲存您的連線詳細資料，例如：

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    每個連線設定檔都應該包含 `connection.json` 檔案。請在 `.composer-connection-profiles` 之下建立新的目錄，在此實例中為 `bmx-hlfv1`。這將會是您在使用 Hyperledger Composer 和「{{site.data.keyword.blockchainfull_notm}} 平台」時，所要使用的設定檔名稱。

        mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1

2. 您現在應該有下列目錄結構：

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    在新建的目錄中，建立一個名為 `connection.json` 的檔案。您可以使用下列範本來建立您的 `connection.json` 檔案：

        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }

    您要在新建的 `connection.json` 檔案中移入透過「{{site.data.keyword.blockchainfull_notm}} 平台」儀表板提供的屬性。從您的儀表板中選取**概觀**，然後按一下**服務認證**按鈕，以顯示通道成員的端點及憑證資訊。

## 步驟 2：新增排序節點資訊

1. 現在我們可以開始使用「服務認證」提供的資訊來修改範本。「服務認證」中可能有多個排序節點，但只需要將一個排序節點用於 `connection.json` 檔案。

    使用服務認證提供的相關資訊來取代範本中的排序節點 URL 值，格式如下：

        “url”: “grpcs://abca.4.secure.blockchain.ibm.com:12345”

## 步驟 3：新增憑證管理中心資訊

1. 使用 **certificateAuthorities** 區段中任一項目的 **url** 和 **caName** 來取代 `connection.json` 中的 ca 值。

## 步驟 4：新增對等節點資訊

1. 每個對等節點的 **requestURL** 和 **eventURL** 都必須設定。請使用在「服務認證」中找到的 **url** 值來取代 **url** 屬性。使用在「服務認證」中找到的 **eventUrl** 來取代 **eventURL** 屬性。進行變更之後，`connection.json` 的 peers 區段格式應如下所示：

        "peers": [
          {
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"

## 步驟 5：新增 keyValStore 資訊

1. 將 **keyValStore** 屬性設定為指向適當的目錄。建立一個要用於 **keyValStore** 的目錄。例如，在起始目錄之下建立一個名為 `.composer-credentials-mychannel` 的新目錄。確定 **keyValStore** 屬性指向新建的目錄，格式如下：

        "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",

## 步驟 6：新增通道資訊

1. 取代 `connection.json` 中的 channel 值，以符合您計劃要建立並將商業網路部署至其中的通道名稱。

## 步驟 7：新增 mspID

`connection.json` 檔案中的 **mspID** 值應該設為 mspID 或您的組織。服務認證提供組織及其相關聯 mspid 值的清單。您應該要為您的組織使用 **mspid** 屬性中的值。

## 步驟 8：新增 globalCert
1. 「{{site.data.keyword.blockchainfull_notm}} 平台」為排序節點和對等節點使用一般 TLS 憑證。每個排序節點和對等節點各有一個 **tlsCACerts** 屬性，全都包含相同的憑證。請將 `connection.json` 檔案中的 dummy 值取代為 **tlsCACerts** 值。其應採用下列格式：

        "globalCert": "-----BEGIN CERTIFICATE-----\r\.......

## 步驟 9：準備您的對等節點

**請注意**：在您建立要用來部署商業網路的通道之前，**必須**先執行此步驟。如果在建立通道之後才執行此步驟，所部署的商業網路**將不會啟動**。

在服務認證文件中的 **certificateAuthorities** 之下，有一個 **registrar** 屬性包含 **enrollId** 和 **enrollSecret** 的屬性，格式如下：

        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],

1. 使用下列指令來要求憑證：

        composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12

    這會將三個檔案下載至起始目錄下的 `.identityCredentials` 目錄中。所需的檔案是以 **enrollId** 為基礎。因此在上述範例中，會有名為 **admin-pub.pem** 和 **admin-priv.pem** 的兩個檔案。

3. 從導覽功能表中選取**成員**，然後選取**憑證**功能表選項，再按一下**新增憑證**按鈕。

4. 在**名稱**欄位中輸入此憑證的唯一名稱，此名稱不能包含橫線或連字號。

5. 開啟先前建立的 `admin-pub.pem` 檔案，並將此檔案的內容複製到**金鑰**欄位中，然後按**提交**按鈕。

6. 透過使用者介面來停止對等節點，然後啟動對等節點。

7. 現在，該憑證應該會出現在憑證清單中。

## 步驟 10：建立您的通道

1. 從左畫面的導覽功能表中選取**通道**，然後按一下**新建通道**按鈕。

2. 輸入「通道名稱」和選擇性說明，並按**下一步**。請注意，「通道名稱」必須符合您在連線設定檔中為通道屬性指定的名稱。

3. 依需要提供許可權，並按**下一步**。

4. 選取需要接受通道更新項目的操作員數目原則，並提交要求。

5. 您現在應該會進入**通知**區段，其中應該會有一個需要檢閱的新要求。按一下**檢閱要求**按鈕。

6. 按一下**接受**按鈕，您會再回到**通知**區段，在其中將看到現在可以提交要求了。按一下**提交要求**按鈕，以開啟提交對話框，然後按一下**提交**按鈕。新通道即已建立。

7. 從導覽功能表中選取**通道**。新通道會出現在通道清單中，並且應該會有「尚未新增對等節點」。按一下它旁邊的動作功能表，然後選取**加入對等節點**。勾選您要新增之「對等節點」旁邊的勾選框，然後按**新增選定項目**。

## 步驟 11：匯入新身分來管理您的商業網路

使用先前要求的憑證，在 Composer 中建立身分。這個新身分有權將鏈碼安裝在具有您所上傳之公用憑證的對等節點上，並且會成為憑證管理中心的發證者。

1. 若要建立新身分，請執行下列指令：

        composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem

    其中 `bmx-hlfv1` 是您先前建立的設定檔名稱。現在我們已準備好將 `.bna` 檔案部署至「{{site.data.keyword.blockchainfull_notm}} 平台」。


## 步驟 12：部署商業網路

現在您可以將 `.bna` 檔案部署至「{{site.data.keyword.blockchainfull_notm}} 平台」。

1. 以在前一個步驟中建立的身分，使用下列指令來部署商業網路：

        composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString

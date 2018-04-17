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

# 在入門範本方案上部署商業網路
{: #deploying-a-business-networks-on-starter-plan}

您可以使用「{{site.data.keyword.blockchainfull}} 平台：開發」開發人員環境和 Hyperledger Composer 開發人員工具集來開發商業網路，並將其部署至「入門範本方案」環境。

使用開發人員環境可讓您快速模型化及測試 {{site.data.keyword.blockchain}} 商業網路，並將其部署至「{{site.data.keyword.blockchainfull_notm}} 平台」的實例。

## 開始之前

請務必閱讀[關於入門範本方案](./starter_plan.html)和[開始使用入門範本方案](./get_start_starter_plan.html)。另外，也請確定您已遵循[控管入門範本方案網路](./get_start_starter_plan.html)中的指示，安裝[「{{site.data.keyword.blockchainfull_notm}} 平台：開發」開發人員環境](./develop_install.html)，並建立「{{site.data.keyword.blockchainfull_notm}} 平台入門範本方案」的實例。


## 步驟 1：擷取管理者密碼

1. 從「入門範本方案」概觀畫面中，按一下**連線設定檔**。

2. 連線設定檔中有一項**管理者密碼**內容。請擷取該密碼，並儲存一份副本。

## 步驟 2：建立憑證管理中心卡

在前一個步驟中擷取的密碼，將會用來建立憑證管理中心 (CA) 的商業網路卡。然後會匯入該 CA 卡，並使用該卡來將**管理者密碼**交換成「入門範本方案」憑證管理中心提供的有效憑證。

1. 使用在步驟 1 中記下的密碼，執行下列指令來建立 CA 商業網路卡：

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. 使用下列指令來匯入該卡：

        composer card import -f ca.card -n ca

3. 現在已匯入該卡，接著就可以使用它來將**管理者密碼**交換成 CA 提供的有效憑證。請執行下列指令，以向憑證管理中心要求憑證：

        composer identity request --card ca --path ./credentials

`composer identity request` 指令會建立 `credentials` 目錄，其中包含憑證的 `.pem` 檔案。

## 步驟 3：將憑證新增至入門範本方案實例

憑證必須新增至「入門範本方案」實例。為了方便，可以使用「{{site.data.keyword.blockchainfull_notm}} 平台」API 來新增。必須新增憑證，然後必須停止並重新啟動對等節點，接著再將憑證同步化。API 呼叫的變數中所需要的資料，可以從「入門範本方案」之「網路監視器」中的**連線設定檔**擷取。

1. 使用下列 API 呼叫來新增憑證：

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    上述範例中的主體 (body) 部分留白，下列程式碼是 API 要求的主體範例：

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. 使用下列 API 呼叫來停止對等節點：

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. 使用下列 API 呼叫來重新啟動對等節點：

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. 使用下列 API 呼叫來將憑證同步化：

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## 步驟 4：建立管理者商業網路卡

現在已將正確的憑證與對等節點同步化，接著就可以建立商業網路卡，使其具有安裝 Hyperledger Composer 運行環境及啟動鏈碼的許可權。

1. 使用下列指令，以通道管理者角色和對等節點管理者角色來建立管理卡：

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    此卡會用來將商業網路部署至「入門範本方案」。

2. 使用下列指令，匯入在前一個步驟中建立的管理卡：

        composer card import -f adminCard.card -n adminCard

## 步驟 5：安裝及啟動商業網路

接著，可以使用在前一個步驟中建立的管理卡來安裝及啟動商業網路。在本手冊中，我們將會安裝汽車製造網路範例。用來啟動商業網路的指令也會建立一張卡。就「入門範本方案」而言，此卡必須刪除，所給定的範例指令將此卡命名為 `delete_me.card`，所以很容易分辨。

1. 使用下列指令來安裝 Hyperledger Composer 運行環境：

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. 使用下列指令來啟動商業網路：

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. 刪除名為 `delete_me.card` 的商業網路卡。

4. 使用下列指令來建立新的商業網路卡，並參照先前擷取的憑證：

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. 使用下列指令來匯入商業網路卡：

        composer card import -f ./admin@vehicle-manufacture-network.card

商業網路現在已部署至「入門範本方案」實例。

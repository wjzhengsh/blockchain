---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 使用 Swagger API 來建立或加入網路

「{{site.data.keyword.blockchainfull}} 平台」公開若干 REST API，可讓您用來在 {{site.data.keyword.cloud_notm}} 上建立或加入區塊鏈網路。您可以使用與您的網路相關聯的 [Swagger 使用者介面](swagger_apis.html)來嘗試這些 API。
{:shortdesc}


## 擷取 API 的基本鑑別認證
{: #retrieve-id-token}

開始之前，您需要在 {{site.data.keyword.cloud_notm}} 中建立具有「入門範本方案」或「企業方案」的 [{{site.data.keyword.blockchainfull_notm}} 平台服務實例 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/blockchain)。

若要使用 Swagger API 來建立或加入網路，您需要一個基本鑑別認證，以確保您可以在 {{site.data.keyword.cloud_notm}} 中存取服務實例。

1. 在 [{{site.data.keyword.cloud_notm}} 儀表板 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/dashboard/apps/) 中，開啟您所建立的服務實例。
2. 從左導覽器中，按一下**服務認證**。
3. 按一下**服務認證**頁面上的「新建認證」按鈕來建立新認證。提供認證名稱（例如，*CreateJoin*），然後按一下**新增**按鈕。您不需要在「新增線型配置參數」欄位中輸入任何內容。
    ![擷取服務認證](../images/service_credentials.gif "擷取服務認證")
4. 建立新的認證之後，請按一下這個認證的**動作**標頭下的**檢視認證**。認證的內容與下列範例類似：

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    `service_instance_id` 作為基本鑑別使用者 ID，而 `service_instance_token` 作為基本鑑別密碼。當您呼叫**建立網路**或**加入網路** API 時，請使用這些值作為基本鑑別認證。

    如果您在遵循先前的步驟來建立服務認證之前建立區塊鏈網路，則認證的內容也會包括網路資訊，並與下列範例類似：

    ```
    {
      "PeerOrg1": {
        "url": "https://ibmblockchain_xyz.ng.bluemix.net",
        "network_id": "92d511f7e587413c8a9848fdae595ef2",
        "key": "PeerOrg1",
        "secret": "T8eUA65l-qtznUHL10KzQ7IK-3BVWWfHu5-hpCiDdXCRQyNfeyIm1p5NT7g17l6U"
      }
    }
    ```
    {:codeblock}

    **附註**：針對**邀請** API，`key` 作為基本鑑別使用者 ID，而 `secret` 作為基本鑑別密碼。


## 檢查可用的網路位置
{: #check-location}

您可以使用 API 只在可用網路位置中建立區塊鏈網路。建立網路之前，請使用下列 API 來檢查可用的網路位置。

```
/network-locations/available
```
{:codeblock}


## 建立網路

**附註**：如果您使用「入門範本方案」，則不需要使用 API 來建立網路，因為在 {{site.data.keyword.cloud_notm}} 中建立區塊鏈服務實例時會提供預設網路。

如果您使用「企業方案」，則需要完成兩個步驟，以使用 API 來建立網路。

1. 在具有「企業方案」<!-- or Enterprise Plus Plan-->的 {{site.data.keyword.cloud_notm}} 上建立區塊鏈服務實例。擷取服務實例 ID 及記號作為基本鑑別使用者名稱和密碼。如需相關資訊，請參閱[擷取 API 的基本鑑別認證](#retrieve-id-token)。

2. 使用這些服務認證來呼叫**建立網路** API。

```
/networks
```
{:codeblock}

**參數**：
- `location_id`：可用網路位置的 ID。如需相關資訊，請參閱[檢查可用的網路位置](check-location)。
- `company_name`：您作為網路中成員的 ID。
- `email`：用來接收通知的電子郵件位址。
- `peers`：您要為此成員建立的對等節點數目。有效值為 0 - 6。您稍後也可以在「網路監視器」使用者介面中建立成員的對等節點。
- `ledger_type`：此網路的分類帳類型。有效值為 levelDB 及 couchDB。**levelDB** 是預設值。


## 邀請新成員加入網路

建立區塊鏈網路之後，您可以邀請其他成員加入您的網路。您需要指定要邀請新成員加入的網路 ID。邀請成員所需的基本鑑別認證與**建立網路** API 中所使用的基本鑑別認證不同。<!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". -->您可以透過 [Swagger 使用者介面](swagger_apis##retrieving-network-credentials)上的**擷取網路認證** API 或 {{site.data.keyword.cloud_notm}} 之服務實例中的[擷取 API 的基本鑑別資訊](#retrieve-id-token)，以取得認證來邀請成員。

```
/networks/{networkID}/invite
```
{:codeblock}

**參數**：
- `email`：要邀請加入網路的成員的電子郵件位址。
- `company_name`：您定義讓成員加入網路的 ID。受邀成員可以在加入網路時變更其 ID。

受邀成員將會收到電子郵件邀請，其中包含如何加入網路的指示。


## 加入網路

如果您受邀加入區塊鏈網路，則會收到電子郵件邀請。您可以遵循電子郵件中的指示，或使用下列 API 來加入網路。

**附註**：在您加入網路之前，需要先建立「{{site.data.keyword.blockchainfull_notm}} 平台」服務實例，並擷取服務實例 ID 及記號作為基本鑑別使用者名稱和密碼。如需相關資訊，請參閱[擷取 API 的基本鑑別資訊](#retrieve-id-token)。您需要指定要加入的網路 ID。

```
/networks/{networkID}/join
```
{:codeblock}

**參數**：
- `company_name`：您作為網路中成員的 ID。這會取代指派給邀請者的名稱。
- `email`：用來接收通知的電子郵件位址。這應該符合邀請通知中的電子郵件位址。
- `peers`：您要為此成員建立的對等節點數目。有效值為 0 到 6。您稍後也可以在「網路監視器」中建立成員的對等節點。


---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Swagger API を使用したネットワークの作成または参加
{: #swagger-network}


***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform では、{{site.data.keyword.cloud_notm}} でブロックチェーン・ネットワークの作成または参加に使用できるように、さまざまな REST API が公開されています。 これらの API を、ネットワークに関連付けられた [Swagger UI](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) を使用して試すことができます。
{:shortdesc}


## API の基本認証資格情報の取得
{: #swagger-network-retrieve-id-token}

始める前に、{{site.data.keyword.cloud_notm}} でスターター・プランまたはエンタープライズ・プランを使用して [{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/catalog/services/blockchain) を作成する必要があります

Swagger API を使用してネットワークの作成または参加を行うには、{{site.data.keyword.cloud_notm}} のサービス・インスタンスに確実にアクセスできるようにするために基本認証資格情報が必要です。

1. [{{site.data.keyword.cloud_notm}} ダッシュボード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/apps/) で、作成したサービス・インスタンスを開きます。
2. 左側のナビゲーターで**「サービス資格情報」**をクリックします。
3. **「サービス資格情報」**ページの「新規資格情報」ボタンをクリックして、新規資格情報を作成します。
    1. 資格情報に *CreateJoin* などの名前を付けます。
    2. 「インラインの構成パラメーターの追加」フィールドに **{"type": "service_instance_token"}** と入力します。
    3. **「追加」**ボタンをクリックします。
    ![サービス資格情報の取得](../images/service_credentials.gif "サービス資格情報の取得")
4. 新しい資格情報が作成されたら、この資格情報の**「アクション」**ヘッダーの下にある**「資格情報の表示」**をクリックします。 資格情報の内容は、以下の例のようになります。

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```

    `service_instance_id` が基本認証ユーザー ID として機能し、`service_instance_token` が基本認証パスワードとして機能します。 これらの値を、**ネットワーク作成 (Create network)** API または**ネットワーク参加 (Join network)** API を呼び出すときに基本認証資格情報として使用します。

    ブロックチェーン・ネットワークを作成した後に前述の手順に従ってサービス資格情報を作成した場合は、ネットワーク情報も資格情報の内容に含まれています。以下の例のようになります。

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

    **注**: **Invite** API の場合は、`key` が基本認証ユーザー ID として機能し、`secret` が基本認証パスワードとして機能します。


## 使用可能なネットワーク・ロケーションの確認
{: #swagger-network-check-location}

使用可能なネットワーク・ロケーションでのみ、API を使用してブロックチェーン・ネットワークを作成できます。 ネットワークを作成する前に、以下の API を使用して、使用可能なネットワーク・ロケーションの現行リストを取得します。 この API を実行するのに資格情報は必要ありません。

```
https://ibmblockchain-v2.ng.bluemix.net/api/v1/network-locations/available
```
{:codeblock}

使用可能なネットワーク・ロケーションの以下の例のようなリストが返されます。

```
{
  "DAL": {
    "location_id": "DAL",
    "description": "Dallas",
    "status": "available"
    "swagger_url": "https://ibmblockchain-v2-dal.4.secure.blockchain.ibm.com/api-docs"
  },
  "FFT": {
    "location_id": "FFT",
    "description": "Frankfurt",
    "status": "available"
    "swagger_url": "https://ibmblockchain-v2-fft.2.secure.blockchain.ibm.com/api-docs"
  },
  "TOR": {
    "location_id": "TOR",
    "description": "Toronto",
    "status": "available"
    "swagger_url": "https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api-docs"
  }
}
```

ネットワークを作成する予定の場合は、API によって返されたリストから、ネットワークを作成するロケーションを選択します。 ``location_id`` および ``swagger_url`` はそのロケーションと関連付けられています。

ネットワークに参加する予定の場合は、招待メールに指定されている ``location_id`` に関連付けられた ``swagger_url`` をメモします。

``swagger_url`` は、下記の API を使用してネットワークを作成したり、ネットワークに参加したりする場合に使用する API エンドポイントを表します。


## ネットワークの作成

**注**: スターター・プランを使用する場合、{{site.data.keyword.cloud_notm}} でブロックチェーン・サービス・インスタンスを作成するときにデフォルト・ネットワークが提供されるため、API を使用してネットワークを作成する必要はありません。

エンタープライズ・プランを使用する場合、 API を使用してネットワークを作成するには、次の 2 つの手順が必要です。

1. {{site.data.keyword.cloud_notm}} でエンタープライズ・プランを使用してブロックチェーン・サービス・インスタンスを作成します<!-- or Enterprise Plus Plan-->。  サービス・インスタンスの ID およびトークンを基本認証ユーザー名およびパスワードとして取得します。 詳しくは、[API の基本認証資格情報の取得](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token)を参照してください。

2. これらのサービス資格情報を使用して**ネットワーク作成 (Create network) ** API を呼び出します。 この API を、[使用可能なネットワーク・ロケーションの確認](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location)で取得した ``swagger_url`` に対して発行します。 ``swagger_url リンク``にナビゲートし、Swagger UI を使用してネットワークの作成 API を発行するか、``/api-docs`` なしの URL アドレスを使用してプログラマチックにコマンドを発行します。次に例を示します。

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api/v1/networks
    ```
    {:codeblock}

**パラメーター**:
- `location_id`: 使用可能なネットワーク・ロケーションの ID。 [使用可能なネットワーク・ロケーションの確認](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location)でメモした `loation_id` の値を指定します。
- `company_name`: ネットワーク内のメンバーとしての ID。
- `email`: 通知を受け取る E メール・アドレス。
- `peers`: このメンバーに作成するピアの数。 有効な値は 0 から 6 までです。 後からネットワーク・モニターでメンバーのピアを作成することもできます。
- `ledger_type`: このネットワークの台帳のタイプ。 有効な値は levelDB および couchDB です。 **levelDB** はデフォルト値です。


## ネットワークへの新規メンバーの招待

ブロックチェーン・ネットワークを作成した後、他のメンバーをネットワークに参加するように招待できます。 新規メンバーの参加を招待するネットワークの ID を指定する必要があります。 メンバーを招待するために必要な基本認証資格情報は、**ネットワーク作成 (Create network)** API で使用したものとは異なります。<!--In order to get the basic auth information, you need to follow the same steps in "Retrieving basic auth information for API". --> [Swagger UI](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-retrieving-network-credentials) で**ネットワーク資格情報取得 (Retrieve network credentials)** API を使用するか、{{site.data.keyword.cloud_notm}} 内のサービス・インスタンスから [API の基本認証情報を取得](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token)して、メンバーを招待するための資格情報を取得できます。

```
/networks/{networkID}/invite
```
{:codeblock}

**パラメーター**:
- `email`: ネットワークに招待するメンバーの E メール・アドレス。
- `company_name`: ネットワークに参加できるようにメンバーのために定義した ID。 招待されたメンバーは、ネットワークに参加するときに自分の ID を変更できます。

招待されたメンバーは、ネットワークへの参加方法に関する説明が記載された招待メールを受け取ります。


## ネットワークへの参加

ブロックチェーン・ネットワークに参加するよう招待されると、`location_id` および `network id` が含まれるネットワーク招待メールを受信します。

1. ネットワークに参加する前に、{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを作成し、基本認証ユーザー名とパスワードであるサービス・インスタンスの ID とトークンを取得する必要があります。 詳しくは、[API の基本認証情報の取得](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token)を参照してください。

2. [使用可能なネットワーク・ロケーションを確認](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location)し、招待メール内の `location_id` の `swagger_url` を取得します。 以下のようになります。

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api-docs
    ```
    {:codeblock}

3. ``swagger_url`` にナビゲートし、Swagger UI を使用して参加 API を発行するか、``swagger_url`` を使用してプログラマチックに参加要求を送信します。 ``/api-docs``を ``/api/v1/networks/[network_id]]/join``
に置き換え、招待メールからの値を使用して `network_id` を入力します。 結果の URL は以下のようになります。

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api/v1/networks/56102acee0e4487889ef09db681bada0/join
    ```
    {:codeblock}

    **注**: **参加** API の場合、ステップ 1 で取得したサービス・インスタンス ID とトークンを基本認証のユーザー名およびパスワードとして使用します。

**パラメーター**:
- `company_name`: ネットワーク内のメンバーとしての ID。 これは招待者が割り当てた名前を置き換えます。
- `email`: 通知を受け取る E メール・アドレス。  これは、招待通知の E メール・アドレスと一致する必要があります。
- `peers`: このメンバーに作成するピアの数。 有効な値は 0 から 6 までです。後からネットワーク・モニターでメンバーのピアを作成することもできます。

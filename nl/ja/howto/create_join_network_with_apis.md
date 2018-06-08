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

# Swagger API を使用したネットワークの作成または参加

{{site.data.keyword.blockchainfull}} Platform では、{{site.data.keyword.cloud_notm}} でブロックチェーン・ネットワークの作成または参加に使用できるように、さまざまな REST API が公開されています。これらの API を、ネットワークに関連付けられた [Swagger UI](swagger_apis.html) を使用して試すことができます。{:shortdesc}


## API の基本認証資格情報の取得
{: #retrieve-id-token}

始める前に、{{site.data.keyword.cloud_notm}} で Starter Plan または Enterprise Plan を使用して [{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/catalog/services/blockchain) を作成する必要があります

Swagger API を使用してネットワークの作成または参加を行うには、{{site.data.keyword.cloud_notm}} のサービス・インスタンスにアクセスするための基本認証資格情報が必要です。

1. [{{site.data.keyword.cloud_notm}} ダッシュボード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/apps/) で、作成したサービス・インスタンスを開きます。
2. 左側のナビゲーターで**「サービス資格情報」**をクリックします。
3. **「サービス資格情報」**ページの「新規資格情報」ボタンをクリックして、新規資格情報を作成します。資格情報に *CreateJoin* などの名前を付け、**「追加」**ボタンをクリックします。「インライン構成パラメーターの追加」フィールドには何も入力する必要はありません。
![サービス資格情報の取得](../images/service_credentials.gif "サービス資格情報の取得")
4. 新しい資格情報が作成されたら、この資格情報の**「アクション」**ヘッダーの下にある**「資格情報の表示」**をクリックします。資格情報の内容は、以下の例のようになります。

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    `service_instance_id` が基本認証ユーザー ID として機能し、`service_instance_token` が基本認証パスワードとして機能します。これらの値を、**ネットワーク作成 (Create network)** API または**ネットワーク参加 (Join network)** API を呼び出すときに基本認証資格情報として使用します。

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
    {:codeblock}

    **注**: **Invite** API の場合は、`key` が基本認証ユーザー ID として機能し、`secret` が基本認証パスワードとして機能します。


## 使用可能なネットワーク・ロケーションの確認
{: #check-location}

API を使用してブロックチェーン・ネットワークを作成できるのは、使用可能なネットワーク・ロケーションのみです。ネットワークを作成する前に、以下の API を使用して、使用可能なネットワーク・ロケーションを確認してください。

```
/network-locations/available
```
{:codeblock}


## ネットワークの作成

**注**: Starter Plan を使用する場合、{{site.data.keyword.cloud_notm}} でブロックチェーン・サービス・インスタンスを作成するときにデフォルト・ネットワークが提供されるため、API を使用してネットワークを作成する必要はありません。

Enterprise Plan を使用する場合、 API を使用してネットワークを作成するには、次の 2 つの手順が必要です。

1. {{site.data.keyword.cloud_notm}} で Enterprise Plan を使用してブロックチェーン・サービス・インスタンスを作成します<!-- or Enterprise Plus Plan-->。サービス・インスタンスの ID およびトークンを基本認証ユーザー名およびパスワードとして取得します。詳しくは、[API の基本認証資格情報の取得](#retrieve-id-token)を参照してください。

2. これらのサービス資格情報を使用して**ネットワーク作成 (Create network) ** API を呼び出します。

```
/networks
```
{:codeblock}

**パラメーター**:
- `location_id`: 使用可能なネットワーク・ロケーションの ID。詳しくは、[使用可能なネットワーク・ロケーションの確認](check-location)を参照してください。
- `company_name`: ネットワーク内のメンバーとしての ID。
- `email`: 通知を受け取る E メール・アドレス。
- `peers`: このメンバーに作成するピアの数。 有効な値は 0 から 6 までです。 後からネットワーク・モニターでメンバーのピアを作成することもできます。
- `ledger_type`: このネットワークの台帳のタイプ。有効な値は levelDB および couchDB です。**levelDB** はデフォルト値です。


## ネットワークへの新規メンバーの招待

ブロックチェーン・ネットワークを作成した後、他のメンバーをネットワークに参加するように招待できます。新規メンバーの参加を招待するネットワークの ID を指定する必要があります。メンバーを招待するために必要な基本認証資格情報は、**ネットワーク作成 (Create network)** API で使用したものとは異なります。<!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". -->[Swagger UI](swagger_apis##retrieving-network-credentials) で**ネットワーク資格情報取得 (Retrieve network credentials)** API を使用するか、{{site.data.keyword.cloud_notm}} 内のサービス・インスタンスから [API の基本認証情報を取得](#retrieve-id-token)して、メンバーを招待するための資格情報を取得できます。

```
/networks/{networkID}/invite
```
{:codeblock}

**パラメーター**:
- `email`: ネットワークに招待するメンバーの E メール・アドレス。
- `company_name`: ネットワークに参加できるようにメンバーのために定義した ID。招待されたメンバーは、ネットワークに参加するときに自分の ID を変更できます。

招待されたメンバーは、ネットワークへの参加方法に関する説明が記載された招待メールを受け取ります。


## ネットワークへの参加

ブロックチェーン・ネットワークへの参加を招待されると、招待メールを受け取ります。E メールに記載された説明に従うか、以下の API を使用して、ネットワークに参加できます。

**注**: ネットワークに参加する前に、{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを作成し、基本認証ユーザー名とパスワードであるサービス・インスタンスの ID とトークンを取得する必要があります。詳しくは、[API の基本認証情報の取得](#retrieve-id-token)を参照してください。参加するネットワークの ID を指定する必要があります。

```
/networks/{networkID}/join
```
{:codeblock}

**パラメーター**:
- `company_name`: ネットワーク内のメンバーとしての ID。これは招待者が割り当てた名前を置き換えます。
- `email`: 通知を受け取る E メール・アドレス。これは、招待通知の E メール・アドレスと一致する必要があります。
- `peers`: このメンバーに作成するピアの数。有効な値は 0 から 6 までです。後からネットワーク・モニターでメンバーのピアを作成することもできます。


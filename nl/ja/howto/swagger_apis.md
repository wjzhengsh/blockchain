---

copyright:
  years: 2018,2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Swagger API を使用したネットワークとの対話
{: #ibp-swagger}

{{site.data.keyword.blockchainfull_notm}} Platform では多数の REST API を Swagger で公開しており、これらを使用してノード、チャネル、ピア、およびネットワーク・メンバーを管理できます。 アプリケーションではこれらの API を使用して、ネットワーク・モニターを使用せずに重要なネットワーク・リソースを管理できます。

{:shortdesc}

始める前に、{{site.data.keyword.cloud_notm}} 上に [{{site.data.keyword.blockchain}} Platform サービス・インスタンス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) を作成し、Starter Plan <!--or Enterprise Plan -->ブロックチェーン・ネットワークを作成するか結合しておく必要があります。


## ネットワーク資格情報の取得
{: #ibp-swagger-retrieving-network-credentials}

ブロックチェーン・ネットワークのネットワーク・モニターに入り、左側のナビゲーターから「API」画面を開きます。 REST API のネットワーク資格情報が表示されます。 後で、ここに表示された「key」と「secret」の値を使用して API を許可し、「network_id」をパラメーターとして使用して API を実行します。 **「secret の表示 (Show secret)」**をクリックして secret フィールドの値を表示します。 key、secret、および network_id の各フィールドの値をコピーします。これらの値は、後で Swagger UI で使用できます。

**図 1** に「API」画面を示します。
![「API」画面](../images/API_screen_starter.png "「API」画面")
*図 1. API*

Starter Plan を使用している場合は、ネットワーク・モニターで組織を切り替えることができます。 Starter Plan では、デフォルトで 2 つの組織が構成されています。 組織の切り替えは、各組織の観点から REST API を試す場合に役立ちます。 ネットワーク内の別の組織の資格情報を取得するには、ネットワーク・モニター・コンソールの右上隅に表示されるユーザー名をクリックします。 開いたメニューで、組織の横にあるドロップダウン矢印をクリックして、すべての組織を表示します。 切り替え先の組織を選択して、関連するネットワーク資格情報を表示します。

次の**図 2** は、組織を切り替える方法を示しています。

![組織間の切り替え](../images/switch_orgs_starter.gif "組織間の切り替え")  
*図 2. 組織間の切り替え*


## Swagger API の許可
{: #ibp-swagger-authorizing-swagger}

「API」画面の**「Swagger UI」**リンクをクリックして Swagger UI を開きます。  

Swagger UI で、**「許可 (Authorize)」**ボタンをクリックします。許可ウィンドウがポップアップ表示されます。 ネットワーク資格情報の「key」と「secret」の値をユーザー名とパスワードとして入力し、**「許可 (Authorize)」**をクリックしてから**「完了」**をクリックします。 これで、API を実行する準備ができました。 ブラウザーを最新表示すると、資格情報を使用して再許可する必要があることに注意してください。

認証として「基本認証」を使用した場合、「許可 (Authorize)」ウィンドウで指定した資格情報は、**「許可 (Authorize)」**ボタンをクリックしてから**「完了」**ボタンをクリックすると保管され、各 REST API 呼び出しで渡されます。

**図 3** は、Swagger API を許可するプロセスを示しています。

![API の許可](../images/swaggerUIAuthorize.gif "API の許可")  
*図 3. API の許可*


## API の試行
{: #ibp-swagger-try-out}

実行する REST API をクリックして、**「試行する」**ボタンをクリックします。

**図 4** に、「Swagger UI」での「試行する」ボタンを示します。

![Swagger UI での「試行する」ボタン](../images/swaggerUITryItOut.png "Swagger UI での「試行する」ボタン")  
*図 4. 「Swagger UI」での「試行する」ボタン*

**「Try it out」**ボタンをクリックした後、API を使用するための必須パラメーターを入力することができます。 `networkID` はネットワーク資格情報で、その他のパラメーターはネットワーク・モニターで見つけることができます。 パラメーターを入力したら、**「実行」**をクリックしてネットワークに対して REST API 呼び出しを実行します。

**図 5** に「Swagger UI」でのパラメーターを示します。

![Swagger UI でのパラメーター](../images/swaggerUIParams.png "Swagger UI でのパラメーター")  
*図 5. パラメーターの入力*  

**「Execute」**をクリックすると、ネットワークに対する API 呼び出しの応答を確認することができます。 また、コマンド・ラインから API を直接呼び出すことができる CURL コマンドも確認することができます。

**図 6** に、API 応答本文、URL、および CURL コマンドを示します。

![Swagger UI での API 応答](../images/swaggerUICurlResponse.png "Swagger UI での API 応答")  
*図 6. API 応答*    

## API アクセスの無効化
{: #ibp-swagger-turn-off}

デフォルトでは、IBM Cloud の監査員以外の役割を持つすべてのユーザーが、Swagger API パネルに表示される**ネットワーク資格情報**を参照および使用できるので、Swagger API を使用してネットワークを管理できます。しかし、Swagger API のネットワーク資格情報を UI で公開したくない場合は、既存のキーとシークレットの値をコピーして安全に保管したうえで、Swagger API を使用できない資格情報を新たに生成することもできます。このアクセスを制御できるように resetCredentials という名前のフラグが用意されています。制御する手順は以下のとおりです。

1. [サービス資格情報ダッシュボード](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token)に記載している手順で、新しいネットワーク資格情報を生成します。
2. ただし、**「インラインの構成パラメーターの追加」**ボックスには以下の値を貼り付けます。
   ```
   {
     "resetCredentials": true
   }
   ```
   {:codeblock}
3. **「追加」**をクリックします。

これで、ユーザーが UI から Swagger API パネルにアクセスすると、UI の**ネットワーク資格情報**には、ネットワークを管理できない汎用的なキーとシークレット値が表示されるようになります。その資格情報を使用して API 要求を送信しても、処理されません。  

後から有効なネットワーク資格情報を UI で公開したくなった場合は、単に上記の手順を繰り返して新しい資格情報を生成します。ただし、そのときは**「インラインの構成パラメーターの追加」**ボックスをブランクのままにします。パラメーターを指定する必要はありません。

これで、元の有効な資格情報が UI の**ネットワーク資格情報**に表示され、Swagger API の認証に使用できるようになります。

## トラブルシューティングのヒント
{: #ibp-swagger-troubleshooting}

### 401 認証が必要  
{: #ibp-swagger-401}

  ネットワーク資格情報を指定して REST API を許可したことを確認してください。 詳しくは、[Swagger API の許可](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-authorizing-swagger)を参照してください。

### 400 エラー: 間違った要求
{: #ibp-swagger-400}

  API によっては、要求の本体で引数が使用される場合があります。この引数は、特定のピアに関する結果のみを表示するためのフィルターの機能を果たします。 本体の中にサンプル・スニペットが記述されています。このサンプル・スニペットが使用されている場合は、編集して、フィルターに掛けるピアまたはピアのリストを指定する必要があります。 このエラーを回避するには、スニペットを編集してネットワーク内のピアを指定するか、スニペット全体を削除します。

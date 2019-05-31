---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: APIs, build a network, authentication, service credentials, API key, API endpoint, IAM access token, Fabric CA client, import a network, generate certificates

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:curl: #curl .ph data-hd-programlang='curl'}

# API を使用したネットワークの構築
{: #ibp-v2-apis}

{{site.data.keyword.blockchainfull}} Platform では、ロギング、通知、およびコンソール設定の管理に加えて、ブロックチェーン・コンポーネントの作成、インポート、編集、および削除を行うことができる RESTful API が公開されます。API および対応する SDK を使用して、ブロックチェーン・ネットワークと対話するアプリケーションを開発することができます。
{: shortdesc}

このチュートリアルでは、{{site.data.keyword.blockchainfull_notm}} Platform API を使用してブロックチェーン・ネットワークを構築する一般的な流れについて説明します。各 API について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform API 参照資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform API 参照資料"){: new_window} を参照してください。

これらの API は、{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} v0.1.77 以降のみと互換性があります。バージョンを確認するには、`https://[your_console_url]/version.txt` を参照します。ここで *`[your_console_url]`* は {{site.data.keyword.blockchainfull_notm}} Platform コンソールの URL です。例えば、https://1ee1869ffa6496d6bc1ce4b-optools.bp01.blockchain.cloud.ibm.com/version.txt などです。
{:note}

## 始めに
{: #ibp-v2-apis-prereq}

API 呼び出しを発行してネットワークと対話できるように、{{site.data.keyword.cloud_notm}} に {{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスが必要です。まだサービス・インスタンスがない場合は、[概説](/docs/services/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks)に従って作成します。

## 認証
{: #ibp-v2-apis-authentication}

{{site.data.keyword.blockchainfull_notm}} Platform で作成するブロックチェーン・ネットワークへのアクセスに API を使用するには、ユーザーのアプリケーションが {{site.data.keyword.cloud_notm}} で認証してサービス・インスタンスに接続できる必要があります。

### サービス資格情報の取得
{: #ibp-v2-apis-retrieve-service-credentials}

{{site.data.keyword.cloud_notm}} の {{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスに確実にアクセスできるようにするために基本認証資格情報が必要です。

1. [{{site.data.keyword.cloud_notm}} リソース・リスト ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/resources) で、作成したブロックチェーン・サービス・インスタンスを開きます。
2. 左側のナビゲーターで**「サービス資格情報」**をクリックします。
3. **「サービス資格情報」**ページの「新規資格情報」ボタンをクリックして、新規資格情報を作成します。
  1. 資格情報に *UseAPIs* などの名前を付けます。
  2. 「インラインの構成パラメーターの追加」フィールドはブランクのままで構いません。
  3. **「追加」**ボタンをクリックします。
4. 新しい資格情報が作成されたら、この資格情報の**「アクション」**ヘッダーの下にある**「資格情報の表示」**をクリックします。 資格情報の内容は、以下の例のようになります。

  ```
  {
    "api_endpoint": "https://1088ac8563e44f5a92539d946733ad7e-optools.so01.blockchain.test.cloud.ibm.com"
    "apikey": "nvASKts6B6lMZGZUNTGVP7MLK2BujMnxz0plSPYaqc3R",
    "configtxlator": "https://1088ac8563e44f5a92539d946733ad7e-configtxlator.so01.blockchain.test.cloud.ibm.com",
    "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:blockchain:us-south:a/9d46037caee397faa45c55e087d26baa:1088ac85-63e4-4f5a-9253-9d946733ad7e::",
    "iam_apikey_name": "auto-generated-apikey-c1981f5c-cff0-464f-9a78-ed2f52e24d1a",
    "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
    "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/9d46037caee397faa45c55e087d26baa::serviceid:ServiceId-774190c5-9c37-4f68-8572-8d6e2aabbc7e",
  }
  ```

サービス資格情報には、{{site.data.keyword.iamshort}} (IAM) アクセス・トークンを取得するために必要な **API キー** (`apikey`) と **API エンドポイント** (`api_endpoint`) があります。

### アクセス・トークンの取得
{: #ibp-v2-apis-retrieve-token}

IAM からアクセス・トークンを取得して、{{site.data.keyword.blockchainfull_notm}} Platform で認証できます。アクセス・トークンを使用して、{{site.data.keyword.cloud_notm}} 上または外部にあるサービスまたはアプリケーションの代わりに、{{site.data.keyword.blockchainfull_notm}} Platform API で作業できます。個人のユーザー資格情報を共有する必要はありません。  

{{site.data.keyword.iamshort}} API を呼び出して、アクセス・トークンを取得します。

```cURL
curl -X POST \
  "https://iam.cloud.ibm.com/identity/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  -d "grant_type=urn%3Aibm%3Aparams%3Aoauth%3Agrant-type%3Aapikey&apikey=<API_KEY>" \
```
{: codeblock}

要求で、`<API_KEY>` を、サービス資格情報の `apikey` の値に置き換えます。次の省略された例に、トークン出力を示しています。

```
{
"access_token": "eyJraWQiOiIyM...",
"refresh_token": "...",
"expires_in":3600,
"expiration":1555558683,
"scope":"ibm openid"}
```
{: screen}

API を使用して、プログラマチックにブロックチェーン・ネットワークで作業するには、先頭に `Bearer` トークン・タイプが付いた完全な `access_token` 値を使用します。

アクセス・トークンが有効なのは 1 時間ですが、必要に応じて再生成できます。サービスへのアクセス権限を維持するには、{{site.data.keyword.iamshort}} API を呼び出すことによって、定期的に API キーのアクセス・トークンをリフレッシュしてください。   
{: tip}

## API 要求の作成
{: #ibp-v2-apis-form-api-request}

サービスに対して API 呼び出しを行う場合、{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを最初にプロビジョンした方法に応じて、API 要求を構成します。

要求を作成するには、以下の形式で API エンドポイントと適切な認証資格情報を組み合わせます。

```cURL
curl -X <API method> \
    <API_endpoint>/<API> \
    -H 'authorization: Bearer <access_token>' \
    -H 'Content-Type: application/json' \
    -d '<request body>' \
```
{: codeblock}

各 API の curl コマンドの例については、[{{site.data.keyword.blockchainfull_notm}} Platform API 参照資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform API 参照資料") を参照してください。

API 参照資料の**「Try it out」**機能を使用して、アプリケーションに追加する前に API 呼び出しをテストすることもできます。**「Try it out」**機能を使用するには、{{site.data.keyword.cloud_notm}} にログインする必要があります。ドロップダウン・リストからサービス・インスタンスを選択できます。すべての API 要求は、API エンドポイントで指定されたネットワークに送信されます。

## 制限
{: #ibp-v2-apis-limitations}

他の {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} ネットワークから、既存の CA、ピア、および順序付けプログラムのノードのみをインポートできます。

## API を使用したネットワークの構築
{: #ibp-v2-apis-build-with-apis}

{{site.data.keyword.blockchainfull_notm}} Platform のインスタンスで、API を使用してブロックチェーン・コンポーネントを作成できます。{{site.data.keyword.blockchainfull_notm}} API を使用してブロックチェーン・ネットワークを構築するには、以下の手順を使用します。

1. [`POST /ak/api/v1/kubernetes/components/ca`](/apidocs/blockchain?code=try#create-a-ca) を呼び出して、認証局 (CA) を作成します。

  入力と応答は後で必要になるため、覚えておいてください。
  {: tip}

  CA が始動するまで待機する必要があります。これは、環境に応じて数分かかる可能性があります。`GET <ca_url>/cainfo` API を呼び出して CA の状況を確認できます。エラーが繰り返し表示された後で、`200` 状況コードが表示されます。このコードは、次のステップに進むことができることを意味します。この API 呼び出しは、1 分後にタイムアウトになることに注意してください。

2. CA を使用して、コンポーネントと管理者の ID を登録し、必要な証明書を生成します。Fabric CA クライアントを使用して、以下の手順を実行できます。

  - [Fabric CA クライアントをセットアップします](#ibp-v2-apis-config-fabric-ca-client)。
  - [CA 管理者で証明書を生成します](#ibp-v2-apis-enroll-ca-admin)。
  - [新規コンポーネントを CA に登録します](#ibp-v2-apis-config-register-component)。
  - [組織管理者を登録](#ibp-v2-apis-config-register-admin)し、MSP フォルダー内に[管理者の証明書を生成](#ibp-v2-apis-config-enroll-admin)する必要もあります。既に管理者 ID を登録している場合は、このステップを実行する必要はありません。
  - [新規コンポーネントを TLS CA に登録します](#ibp-v2-apis-config-register-component-tls)。

  これらの手順は、{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用して実行することもできます。詳しくは、[アイデンティティーの作成および管理](/docs/services/blockchain/howto/ibp-console-identities.html)を参照してください。

3. [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?#import-a-membership-service-provide-msp) を呼び出して、[組織の MSP 定義を作成します](#ibp-v2-apis-msp)。

4. 順序付けプログラムまたはピアを作成するために必要な[構成ファイルを作成します](#ibp-v2-apis-config)。作成する順序付けプログラムまたはピアごとに固有の構成ファイルを作成する必要があります。

5. [`POST /ak/api/v1/kubernetes/components/orderer`](/apidocs/blockchain?code=try#create-an-orderer) を呼び出して、順序付けプログラムを作成します。

6. [`POST /ak/api/v1/kubernetes/components/peer`](/apidocs/blockchain?code=try#create-a-peer) を呼び出して、ピアを作成します。

7. コンソールを使用してブロックチェーン・コンポーネントを操作する場合は、コンソール・ウォレットに管理者 ID をインポートする必要があります。ウォレットのタブを使用して、ノード管理者の証明書と秘密鍵をコンソールにインポートして、ID を作成します。その後、コンソールを使用して、作成したコンポーネントにこの ID を関連付ける必要があります。詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform コンソールへの管理者 ID のインポート](#ibp-v2-apis-admin-console)を参照してください。

8. ネットワークをデプロイしたら、Fabric SDK、ピア CLI、またはコンソール UI を使用して、チャネルを作成し、スマート・コントラクトをインストールまたはインスタンス化できます。

コンポーネントを作成できるように、API 認証に使用されるサービス資格情報には、IAM での `Manager` の役割が必要です。詳しくは、[ユーザーの役割](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)に関するトピックの表を参照してください。
{: note}

## API を使用したネットワークのインポート
{: #ibp-v2-apis-import-with-apis}

API を使用して、API または {{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用して作成された {{site.data.keyword.blockchainfull_notm}} コンポーネントを、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 の別のサービス・インスタンスにインポートすることもできます。

1. [`POST /ak/api/v1/components/ca`](/apidocs/blockchain?code=try#import-a-ca) を呼び出して、CA をインポートします。

  入力と応答は後で必要になるため、覚えておいてください。
  {: tip}

  CA が始動するまで待機する必要があります。これは、環境に応じて数分かかる可能性があります。[`GET /components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) を呼び出して CA の状況を確認できます。エラーが繰り返し表示された後で `200` 状況コードが表示されると、次のステップに進むことができます。この API 呼び出しは、1 分後にタイムアウトになることに注意してください。

2. [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp) を呼び出して、組織の MSP 定義をインポートします。

3. [`POST /ak/api/v1/components/orderer`](/apidocs/blockchain?code=try#import-a-orderer) を呼び出して、順序付けプログラムをインポートします。

4. [`POST /ak/api/v1/components/peer`](/apidocs/blockchain?code=try#import-a-peer) を呼び出して、ピアをインポートします。

5. {{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用してブロックチェーン・コンポーネントを操作する予定の場合は、コンソール・ウォレットにコンポーネント管理者 ID をインポートする必要があります。詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform コンソールへの管理者 ID のインポート](#ibp-v2-apis-admin-console)を参照してください。

6. ネットワークをデプロイしたら、Fabric SDK、ピア CLI、またはコンソール UI を使用して、チャネルを作成し、スマート・コントラクトをインストールまたはインスタンス化できます。

コンポーネントをインポートできるように、API 認証に使用されるサービス資格情報には、IAM での `Writer` の役割が必要です。詳しくは、[ユーザーの役割](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)に関するトピックの表を参照してください。
{: note}

## Fabric CA クライアントを使用した CA の操作
{: #ibp-v2-apis-config-fabric-ca-client}

Fabric CA クライアントを使用して CA を操作できます。 次の Fabric CA クライアントのコマンドを実行して、コンポーネントと管理者 ID を登録し、必要な証明書を生成します。

### Fabric CA クライアントのセットアップ
{: #ibp-v2-apis-setup-fabric-ca-client}

1. [Fabric CA クライアント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Fabric CA クライアントのダウンロード") をローカル・ファイル・システムにダウンロードします。

  Fabric CA クライアントを入手する最も簡単な方法は、すべての Fabric ツール・バイナリーを直接ダウンロードすることです。 コマンド・ラインを使用してバイナリーをダウンロードするディレクトリーにナビゲートし、以下の [Curl ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl") コマンドを実行して、バイナリーを取り出します。

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
  ```
  {:codeblock}

  このコマンドは、`bin/` ディレクトリーにバイナリーをインストールします。

2. `PATH` パスを、Fabric ツール・バイナリーをダウンロードしたディレクトリーに設定します。

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  例えば、ホーム・ディレクトリーにバイナリーをインストールした場合、`PATH` を以下のように設定します。

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. CA 管理者の証明書を保管するフォルダーを作成します。

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. `$FABRIC_CA_CLIENT_HOME` 環境変数の値を、生成された [MSP](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) 証明書を CA クライアントが保管するパスに設定します。 以前の試行で作成された構成素材を必ず削除してください。 以前に `enroll` コマンドを実行したことがない場合は、`msp` フォルダーと `.yaml` ファイルは存在しません。

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Fabric CA クライアントによって使用される CA の TLS 証明書を取得します。{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用している場合は、CA を開き、**「設定」**をクリックして、**「TLS 証明書 (TLS Certificate)」**フィールドで base64 形式の証明書を探します。API を使用している場合は、[`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) を呼び出して、`"PEM"` フィールドで CA TLS 証明書を見つけることができます。`Create a Fabric CA` API を使用して CA を作成した場合は、応答本文で TLS 証明書を見つけることもできます。

  証明書は、CA との通信で使用するために、base64 形式から PEM 形式に変換する必要があります。以下のコマンドに、TLS 証明書の base64 でエンコードされたストリングを挿入します。`$HOME/fabric-ca-client` ディレクトリーにいることを確認してください。

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > tls.pem
  ```
  {:codeblock}

### CA 管理者による証明書の生成
{: #ibp-v2-apis-enroll-ca-admin}

**CA 管理者**の ID は、CA の作成時に自動的に登録されています。 ここでその管理者名とパスワードを使用して、Fabric CA クライアントで `enroll` コマンドを実行し、その他のピアまたは順序付けプログラムの ID を登録するために使用できる証明書を含む MSP フォルダーを生成できます。

1. [Fabric CA クライアントをセットアップ](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)するためのステップを完了していること、および `$FABRIC_CA_CLIENT_HOME` が CA 管理者証明書を保管するディレクトリーに設定されていることを確認します。

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. 以下のコマンドを実行して証明書を生成します。

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  このコマンドの `<enroll_id>` と `<enroll_password>` は、CA の作成時に指定した `enroll_id` と `enroll_secret` です。{{site.data.keyword.blockchainfull_notm}} Platform コンソールからの**認証局エンドポイント URL** または API 呼び出しによって返される `"ca_url"` を `<ca_url_with_port>` の値として使用します。先頭の `http://` は省略します。 `<ca_name>` は、コンソールからの **CA 名**または API によって返される `"ca_name"` です。

  `<ca_tls_cert_path>` は、CA TLS 証明書の絶対パスです。

  実際の呼び出しは以下のコマンド例のようになります。

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```  
  {:codeblock}

**ヒント:** 登録 url の値 (`-u` パラメーターの値) に特殊文字が含まれている場合は、特殊文字をエンコードするか、url を単一引用符で囲む必要があります。例えば、`!` を `%21` にするか、コマンドを次のように入力します。

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname ca
  ```
  {:codeblock}

  `enroll` コマンドは、メンバーシップ・サービス・プロバイダー (MSP) フォルダーと呼ばれる、証明書の完全なセットを生成します。これは、Fabric CA クライアントの `$HOME` パスに設定したディレクトリー内にあります。 例えば、`$HOME/fabric-ca-client/ca-admin` です。 MSP および MSP フォルダーの内容について詳しくは、[メンバーシップ・サービス・プロバイダー](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)を参照してください。

  `enroll` コマンドが失敗した場合は、考えられる原因について[トラブルシューティングのトピック](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting)を参照してください。

  tree コマンドを実行して、すべての前提条件ステップを完了したことを確認できます。 証明書を格納したディレクトリーにナビゲートします。 tree コマンドを実行すると、以下の構造に似た結果が生成されます。

  ```
  cd $HOME/fabric-ca-client
tree
.
  ├── ca-admin
  │   ├── fabric-ca-client-config.yaml
  │   └── msp
  │       ├── cacerts
  │       │   └── 9-30-250-70-30395-SampleOrgCA.pem
  │       ├── IssuerPublicKey
  │       ├── IssuerRevocationPublicKey
  │       ├── keystore
  │       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
  │       ├── signcerts
  │       │   └── cert.pem
  │       └── user
  └── catls
      └── tls.pem    
  ```

### CA へのコンポーネント ID の登録
{: #ibp-v2-apis-config-register-component}

まず、コンポーネント ID を CA に登録する必要があります。コンポーネントのデプロイ時に必要な証明書を生成するために、この ID が使用されます。

1. Fabric CA クライアントを使用して、[CA 管理者で証明書を生成します](#ibp-v2-apis-enroll-ca-admin)。これらの管理者証明書を使用して、以下のコマンドを実行します。 `$FABRIC_CA_CLIENT_HOME` が `$HOME/fabric-ca-client/ca-admin` に設定されていることを確認してください。

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. 以下のコマンドを実行して、所属団体名と組織名を確認します。
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  コマンドは次の例のようになります。
  ```
  fabric-ca-client affiliation list --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  以下の例のような情報が表示されます。
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  `org1.department1` のような 2 番目の **affiliation** 値をメモします。 この値を、以下のコマンドで使用する必要があります。

3. 次のコマンドを実行して、順序付けプログラムまたはピアを登録します。

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  コンポーネントの名前とパスワードを作成し、それらを使用して `name` および `secret` を置換します。  この情報はメモしてください。 順序付けプログラムをデプロイする場合は `--id.type` を `orderer` に設定し、ピアをデプロイする場合はこれを `peer` に設定します。 コマンドは以下の例のようになります。

  ```
  fabric-ca-client register --caname ca --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  構成ファイルを作成するときのために、上記のコマンドからの `"enrollid"` と `"enrollsecret"` を保存する必要があります。
  {: important}

  一度に登録できる ID は 1 つだけです。 問題が発生した場合は、別のユーザー名とパスワードを使用してコマンドを試行してください。 セキュリティー対策として、各 ID および付随する登録 ID と機密事項を使用してデプロイするピアは 1 つのみにします。 複数のコンポーネントに対して 1 つの**管理者** ID を使用することはできますが (これが推奨されるデプロイメント方針です)、ピア ID とパスワードは再使用しないでください。

  コマンドが正常に完了すると、以下の例のような情報が表示されます。

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

### 組織管理者の登録
{: #ibp-v2-apis-config-register-admin}

ネットワークの操作に使用できる管理者 ID も作成する必要があります。この ID は、ピアにスマート・コントラクトをインストールするなど、特定のコンポーネントを操作する場合に使用します。この ID は、組織の管理者として使用することも、チャネルを作成および編集するために使用することもできます。  

この新しい ID を CA に登録し、この ID を使用して MSP フォルダーを生成する必要があります。 この ID を組織管理者にするには、その署名付き証明書を組織の MSP に追加します。デプロイメント時に順序付けプログラムまたはピアの管理者証明書にすることができるように、署名付き証明書を構成ファイルに追加する必要もあります。組織に対して、管理者 ID は 1 つのみ作成する必要があります。 そのため、これらの手順は 1 回のみ実行する必要があります。生成した署名付き証明書を使用して、多数のピアまたは順序付けプログラムをデプロイできます。

`$FABRIC_CA_CLIENT_HOME` が CA 管理者の MSP のパスに設定されていることを確認します。

```
echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
```
{:codeblock}

以下のコマンドを実行して、コンポーネント管理者 ID を CA に登録します。

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type client --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

管理者の新しいユーザー ID の `name` と `secret` を作成します。 必ず、先ほど登録したピアまたは順序付けプログラムの ID とは異なる値を使用してください。 コマンドは、以下の例のようになります。

```
fabric-ca-client register --caname ca --id.name peeradmin --id.affiliation org1.department1 --id.type client --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

この情報はメモしてください。 この `name` と `secret` は、`enroll` コマンドで MSP フォルダーを生成するために使用します。
{: important}

### 管理者メンバーシップ・サービス・プロバイダー (MSP) フォルダーの生成
{: #ibp-v2-apis-config-enroll-admin}

コンポーネント管理者を登録したら、以下のコマンドを実行して証明書を生成できます。

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

例えば、次のようにします。

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname ca -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```
{:codeblock}

このコマンドが正常に完了すると、`-M` フラグを使用して指定したディレクトリーに新しい MSP フォルダーが生成されます。 このディレクトリーには、コンポーネントの操作に使用する必要がある証明書が含まれています。tree コマンドを使用して、enroll コマンドが機能したことを確認できます。 証明書を格納したディレクトリーにナビゲートします。 tree コマンドを実行すると、以下の構造に似た結果が生成されます。


```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

組織の MSP 定義と構成ファイルを作成するときは、このフォルダーに戻る必要があります。
{: important}

### TLS CA へのコンポーネント ID の登録
{: #ibp-v2-apis-config-register-component-tls}

CA を作成したときに、TLS CA がデフォルト CA とともにデプロイされています。順序付けプログラムまたはピアを TLS CA に登録する必要もあります。 これを行うには、まず TLS CA の管理者を使用してエンロールする必要があります。 `$FABRIC_CA_CLIENT_HOME` を、TLS CA 管理者証明書を保管するディレクトリーに変更します。

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

以下のコマンドを実行して、TLS CA に対する管理者としてエンロールします。 TLS CA 管理者の登録 ID とパスワードは、デフォルト CA と同じです。そのため、以下のコマンドは、[CA 管理者](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin)としてエンロールするために使用したものと同じですが、CA TLS の名前を使用する点のみが異なります。TLS CA 名は、コンソールの CA **設定**パネルの**「TLS CA 名 (TLS CA Name)」**の値、または `Create a CA` API によって返される `"tlsca_name"` の値です。

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

実際の呼び出しは以下の例のようになります。

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

エンロールが完了すると、コンポーネントを TLS CA に登録するために必要な証明書が得られます。 次のコマンドを実行して、順序付けプログラムまたはピアを登録します。

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

このコマンドは、TLS CA 名を使用する必要がある点を除き、コンポーネント ID を CA に登録するために使用したコマンドと似ています。ピアではなく順序付けプログラムをデプロイする場合は、`--id.type` を `peer` ではなく `orderer` に設定してください。 この ID には、デフォルト CA に対して使用したものとは異なるユーザー名とパスワードを指定する必要があります。実際の登録は、以下のコマンドのようになります。

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

構成ファイルを作成するときのために、上記のコマンドからの `"enrollid"` と `"enrollsecret"` を保存する必要があります。
{: important}

## 組織 MSP 定義の作成
{: #ibp-v2-apis-msp}

API を使用して、[`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp) を呼び出すことで、組織の MSP 定義を作成できます。この MSP には、ブロックチェーン・コンソーシアムの組織を定義する証明書と、ネットワークを操作するために使用できる管理者証明書が含まれます。上記のステップを実行した場合は、組織の MSP を作成するために必要な証明書が既に生成されています。以下の手順を使用して、API 呼び出しの要求本文を完成させます。

1. 要求の `type` として `msp` を入力します。

2. 組織の MSP ID を選択します。MSP ID は、コンソーシアム内の組織の正式名です。 組織の MSP を作成するために使用される MSP ID は、ピアのデプロイに使用するものと同じにする必要があります。

3. MSP の表示名を選択します。

4. Fabric CA クライアントを使用して登録およびエンロールした組織管理者の base64 形式の署名付き証明書を提供する必要があります。  

  [組織管理者を使用して証明書を生成した](#ibp-v2-apis-config-enroll-admin)ときに作成された MSP ディレクトリーにナビゲートします。
  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  ```
  この MSP ディレクトリーで、以下のコマンドを使用して、新規ユーザーの署名付き証明書ファイルを開き、それを base64 形式に変換します。

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。 以下のようになります。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```
  以下のようにはなりません。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

  例えば、次のようにします。

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  このコマンドは、以下の例のようなストリングを出力します。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  API 要求の `admins` フィールドにこのストリングを指定します。

5. CA のルート証明書を指定する必要もあります。この証明書は、[CA 管理者を使用して証明書を生成した](#ibp-v2-apis-enroll-ca-admin)ときに作成されています。

  CA 管理者の MSP ディレクトリーにナビゲートします。
  ```
  cd $HOME/fabric-ca-client/ca-admin/msp
  ```
  このディレクトリーで、以下のコマンドを使用して、cacerts フォルダーを開き、その中の証明書を base64 形式に変換します。
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-ca-admin>/msp/cacerts/<ca_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  これにより、ルート証明書が base64 でエンコードされたストリングとして出力されます。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGekNDQWIyZ0F3SUJBZ0lVQmZnZzcvVnIrL25OVEFNQlQ4UUtHL00wQU8wd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU1TURVd016RXpNamt3TUZvWERUTTBNRFF5T1RFek1qa3dNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVXMUtvN2lWeVE2VWkwdDVqbU5KaWVuSUwKR3pNM1BDWHlhL2VSQ0NWMmFQb0dTZ1lrVUg2UWN5RjAzbFlMZFU4Y0drNTQ0alViVC9KT1lYeVgzTWc4bHFORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZDK2lJR0NSb2Zvb3FsVkZoU3dOMmk2MXNJaVBNQW9HQ0NxR1NNNDlCQU1DQTBnQU1FVUNJUURTYW9RL1E0QzkKbFl1VGNhVXVHb3d6YmhUZHBuN2F3S2lHN1Nvd2lSQXVld0lnUWlyM3RNR3IvYWo2aU5lRXJFN2NyOVowQ0gvTwp3QnNQcWd4RVR3MjVqZUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  API 要求の `root_certs` フィールドにこのストリングを指定します。

6. TLS CA のルート証明書を指定する必要もあります。TLS ルート証明書により、ピアがチャネルのゴシップに参加できます。

  [TLS CA 管理者をエンロールした](#ibp-v2-apis-config-register-component-tls)ときに生成された MSP ディレクトリーにナビゲートします。
  ```
  cd $HOME/fabric-ca-client/tlsca-admin/msp
  ```
  このディレクトリーで、以下のコマンドを使用して、cacerts フォルダーを開き、その中の証明書を base64 形式に変換します。
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-tlsca-admin>/msp/cacerts/<tls_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  これにより、TLS CA ルート証明書が base64 でエンコードされたストリングとして出力されます。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVWUVQWnprNXV2b3dobEtacG5JMXplODdIQUlnd0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWURWUVFERXdWMGJITmpZVEFlCkZ3MHhPVEExTURNeE16STVNREJhRncwek5EQTBNamt4TXpJNU1EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdzVPYjNKMGFDQkRZWEp2YkdsdVlURVVNQklHQTFVRUNoTUxTSGx3WlhKc1pXUm5aWEl4RHpBTgpCZ05WQkFzVEJrWmhZbkpwWXpFT01Bd0dBMVVFQXhNRmRHeHpZMkV3V1RBVEJnY3Foa2pPUFFJQkJnZ3Foa2pPClBRTUJCd05DQUFRdSs2UnZWd2w5T2dDVlAraEVxbjVxdExRVG9LWkw4a1lic0pOeU1JbERoc3hlNWx6cW1zQkoKbTk2eUR2TVV6OSsxL2pzb1M4M1JqMVAwc3M2TnJNb3FvMXd3V2pBT0JnTlZIUThCQWY4RUJBTUNBUVl3RWdZRApWUjBUQVFIL0JBZ3dCZ0VCL3dJQkFUQWRCZ05WSFE0RUZnUVVnUEc4anJEK1BxVjdoelc3WDlsbTFrMS91WjR3CkZRWURWUjBSQkE0d0RJY0VxVGJESW9jRUNwbnBkVEFLQmdncWhrak9QUVFEQWdOSUFEQkZBaUVBenk3cHJZaVMKQmlDVWdYeWRkY09WMm9mZmtqaEI0N091QXFjQWNqZS9SWkVDSUdKZFgzZ1ErTDRIN3duY1RoZkwrenU1ejV1UApGUWhXTmlNS3hQWEYrZnYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  API 要求の `tls_root_certs` フィールドにこのストリングを指定します。

## 構成ファイルの作成
{: #ibp-v2-apis-config}

API を使用してピアまたは順序付けプログラムを作成するには、構成ファイルを完成させる必要があります。このファイルは、API 呼び出しの要求本文の `config` オブジェクトとして API に提供されます。ファイルを完成させる前に、CA を {{site.data.keyword.cloud_notm}} Platform サービス・インスタンスにデプロイして、必要な ID を登録およびエンロールする手順を実行する必要があります。

構成ファイルのテンプレートを以下に示します。
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

このファイル全体をテキスト・エディターにコピーしてください。テキスト・エディターでこのファイルを編集して、ローカル・ファイル・システムに JSON ファイルとして保存できます。 以下の手順を使用してこの構成ファイルを完成させて、その構成ファイルを使用して順序付けプログラムまたはピアをデプロイします。

### CA 接続情報の取得
{: #ibp-v2-apis-config-connx-info}

まず、{{site.data.keyword.cloud_notm}} Platform で CA の接続情報を指定する必要があります。 コンソール UI または API を使用して、CA に関する必要な情報を取得できます。

**{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用している場合:**
コンソールで CA を開き、**「設定」**をクリックしてから**「エクスポート」**ボタンをクリックして、CA 情報を JSON ファイルにエクスポートします。このファイルからの値を使用して、構成ファイルを完成させます。

**API を使用している場合:**
[`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) を呼び出して、CA の接続情報を取得できます。`Create a Fabric CA` API を使用して CA を作成した場合は、応答本文で必要な情報を見つけることもできます。

- `"cahost"` および `"caport"` の値は、応答本文の `ca_url` フィールドまたはエクスポートした CA JSON ファイルに表示されます。例えば、`ca_url` が https://9.30.94.174:30167 の場合、`"cahost"` の値は `9.30.94.174` になり、`"caport"` は `30167` になります。
- `"caname"` は、CA のデプロイ時に指定した CA の名前です。これは、応答本文の `ca_name` フィールドまたはエクスポートした JSON ファイルの値です。
- `"cacert"` は、CA の base64 でエンコードされた TLS 証明書です。 これは、応答本文の `pem` フィールドまたはエクスポートした JSON ファイルの値です。

- 以下の `"tls"` セクションのフィールドには、上記の component セクションに渡したものと同じ情報が必要です。ただし、CA のデプロイメント時に指定された CA TLS インスタンス名の値を使用する必要があります。 `caname` を、応答本文の `tlsca_name` またはエクスポートした JSON ファイルの値に置き換えます。`"cacert"` 値には同じ TLS 証明書を使用します。
  ```
  "tls": {
    "cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
      "cacert": ""
  ```
  {:codeblock}

### コンポーネントの登録 ID と機密事項の指定

1. [コンポーネントをデフォルト CA に登録する](#ibp-v2-apis-config-register-component)ときに使用した `name` および `secret` を、構成ファイルの `"component"` セクションの下で、`"enrollid"` および `"enrollsecret"` として貼り付けます。

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```
2. [コンポーネントを TLS CA に登録する](#ibp-v2-apis-config-register-component-tls)ときに使用した `name` および `secret` を、構成ファイルの `"tls"` セクションの下で、`"enrollid"` および `"enrollsecret"` として貼り付けます。

  ```
  "tls": {...
    },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
  ```

### 組織管理者の署名付き証明書の提供

[組織管理者を使用して証明書を生成した](#ibp-v2-apis-config-enroll-admin)ときに作成された MSP ディレクトリーにナビゲートします。
```
cd $HOME/fabric-ca-client/peer-admin/msp
```
この MSP ディレクトリーで、以下のコマンドを使用して、新規ユーザーの署名付き証明書ファイルを開き、それを base64 形式に変換します。

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。 以下のようになります。

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```
以下のようにはなりません。

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
```

例えば、次のようにします。

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

このコマンドは、以下の例のようなストリングを出力します。

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

このストリングを、構成ファイルの component セクションの下にある `"admincerts"` フィールドにコピーします。

### CSR (証明書署名要求) ホスト

コンポーネント・ファイルの `"csr"` セクションを使用して、コンポーネントにカスタム・ドメインを指定できます。

```
"csr": {
  "hosts": [""]
			}
```
{:codeblock}

このセクションは、上級者がピアのエンドポイントのアドレス指定に使用できるカスタムのホスト名を指定するためのものです。ほとんどのユーザーは、このセクションをブランクのままにすることができます。

### 構成ファイルの完成
{: #ibp-v2-apis-config-file}

上記のすべてのステップを完了すると、更新された構成ファイルは以下の例のようになります。


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "ca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer1",
            "enrollsecret": "peer1pw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
		"tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                ]
            }
        }
    }
}
```
{:codeblock}

他のフィールドはブランクのままで構いません。 このファイルが完成したら、`config` フィールドとして、`Create an orderer` または `Create a peer` API の要求本文にこのファイルを渡すことができます。

### {{site.data.keyword.blockchainfull_notm}} Platform コンソールへの管理者 ID のインポート
{: #ibp-v2-apis-admin-console}

{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用してブロックチェーン・コンポーネントを操作する場合は、コンソール・ウォレットに管理者 ID をインポートする必要があります。コンソールでウォレット・パネルを開きます。概要画面の**「アイデンティティーの追加 (Add identity)」**ボタンをクリックします。ボタンをクリックするとサイド・パネルが開き、そこでアイデンティティーの署名証明書と秘密鍵を直接コンソールに追加できます。
- **名前:** 参照のためだけに使用されるアイデンティティーの表示名を入力します。
- **証明書:** 管理者の署名証明書をアップロードします。上記の手順に従った場合、この鍵は `$HOME/fabric-ca-client/peer-admin/msp/signcerts/`フォルダーにあります。
- **秘密鍵:** 管理者の秘密鍵をアップロードします。上記の手順に従った場合、この鍵は `$HOME/fabric-ca-client/peer-admin/msp/keystore/`フォルダーにあります。

管理者 ID をインポートしたら、作成したコンポーネントにこの ID を関連付けることができます。これにより、コンソールを使用してネットワークを操作できます。

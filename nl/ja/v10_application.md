---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# アプリケーションの開発
{: #dev_app}
アプリケーションを使用すると、チェーンコードを呼び出して、ブロックチェーン・ネットワーク内のチャネル固有の台帳を照会したり更新したりできます。
{:shortdesc}

## アプリケーション開発環境のセットアップ
{{site.data.keyword.cloud}} 上のブロックチェーン・ネットワークと対話できるアプリケーションを開発するには、ソフトウェア前提条件をインストールする必要があります。
{:shortdesc}

*	Git ([Git のダウンロード・ページ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://git-scm.com/downloads){:new_window})  
	Git はバージョン管理ツールの 1 つで、簡単に言うと、チェーンコードの開発とソフトウェア開発の両方を把握するためのツールです。 また Git bash は、Git と一緒に Windows にインストールされ、Windows コマンド・プロンプトの代わりに使用できる優良なツールです。

*	GoLang ([GoLang のダウンロード・ページ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://golang.org/dl){:new_window})  
	GoLang をインストールすると、Go の CLI ツールのセットがインストールされます。これらのツールは、チェーンコードの作成に非常に役立ちます。 例えば、`go build` コマンドを使用すると、チェーンコードをネットワークにデプロイしようとする前に、チェーンコードが実際にコンパイルされるかどうかを検査できます。

	[インストールの指示 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://golang.org/doc/install){:new_window} に従って、環境変数を適切に設定します。 以下のコマンドを使用して、`GOPATH` を検査します。 `GOPATH` がこの例と一致している必要はないことに注意してください。ただ一つ重要なこととして、この変数はファイル・システム上の有効なディレクトリーに設定する必要があります。

	```
	$ echo $GOPATH
C:\gopath
	```
	{:codeblock}

	その後、[hello world ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://golang.org/doc/install#testing){:new_window} の例を使用して GoLang コードを構築し、GoLang のインストールを確認できます。

* Java ([Java のダウンロード・ページ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://java.com/en/download/){:new_window})。
*	Node.js ([Node.js のダウンロード・ページ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://nodejs.org/en/download/){:new_window})。

現在、Node.js、Go、Java によるチェーンコード開発がサポートされています。

## クライアント・サイドの証明書の生成
x509 と Public Key Infrastructure の詳細についてはここでは扱いません。これらについての一般資料は多数存在します。 Fabric の通信フローではタッチポイントごとに署名/検証操作が使用されることを知っていれば十分です。 したがって、ネットワークに呼び出し (つまり、台帳の照会または更新) を送信するクライアントは、ペイロードに署名し、適切に署名された x509 証明書を検証のために付加する必要があります。 秘密鍵と署名付き証明書は、MSP 識別子と認証局 (CA) ルート証明書とともに、「ユーザー・コンテキスト」オブジェクトと呼ばれるものを構成します。

ここで重要なのは「登録」のプロセスです。このプロセスで、このオブジェクトを形成するために必要な鍵と証明書が適切な CA から取得されます。 ユーザー・コンテキスト・オブジェクトを形成した後、アプリケーションから API を呼び出して、このユーザー・コンテキストに対して「set」または「get」を実行できます。 この時点で、アプリケーション (つまりクライアント) に必要な成果物がすべて装備され、ネットワークと通信する準備ができます。 鍵と証明書を取得するための 2 つのアプローチであるコマンド・ラインと SDK について見ていきます。

### コマンド・ライン
2 つのアプローチのうち、コマンド・ラインを使用する方が簡単です。

1. [Fabric CA クライアント ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html) を指示に従って構築します。 このステップにより、CA サーバーと通信し、適切にフォーマットされた証明書と鍵を受け取ることができます。

2. 使用するサービス・プラン、ロケーション、およびクラスターに応じて {{site.data.keyword.cloud_notm}} から TLS 証明書をダウンロードします。
  - スターター・プラン用のルート TLS 証明書  
    - 米国: [us01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - 英国: [uk01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - シドニー: [aus01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Enterprise Plan 用のルート TLS 証明書 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  コンテンツをフォルダー (例えば ``$ HOME/tls``) に保存します。 このステップにより、データの流れがワイヤー上で暗号化されます。

3. ネットワーク・モニターの**「概要」**画面から **Connection Profile** JSON ファイルを開き、以下の変数を見つけます。
  * CA の URL: ``certificateAuthorities`` の下の ``url``
  * 管理者のユーザー ID: ``enrollId``
  * 管理者のパスワード: ``enrollSecret``
  * CA 名: ``caName``

4. Fabric CA クライアントを使用し、以下のコマンドを使用して TLS 証明書のパスと上記の 4 つのストリングを渡すことにより、認証局に「登録」呼び出しを送信できます。
  ```
  $GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  ``fabric-ca-client`` バイナリーは ``$GOPATH/bin`` 内にあるので、このコマンドは、ローカル・マシン上の適切な場所から実行する必要があります。  実際の呼び出しは以下のコマンド例のようになります。
  ```
  ./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
  ```
  {:codeblock}

5. `$HOME/.fabric-ca-client/msp/signcerts/cert.pem` 内で管理者証明書を見つけます。 見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。 証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」画面の「証明書」タブ](v10_dashboard.html#members)を参照してください。

  さらに、以下のディレクトリーで CA ルート証明書と管理者秘密鍵を見つけることができます。
  * CA ルート証明書: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * 管理者秘密鍵: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
いくつかの Fabric リポジトリーに、認証局とプログラマチックに対話する方法を理解するために役立つ優れたリソースとスクリプトが含まれています。 ``fabric-samples`` リポジトリーには``残高繰越``の例が含まれ、``fabric-sdk-node`` リポジトリーには一連の CA サービス・テストが含まれています。 アプリケーション・サイドで登録要求を発行しようとしている場合は、``fabric-ca-client`` と ``fabric-client`` のパッケージで公開されている必要のある API について完全に理解している必要があります。 アプリを構成するための基礎として、これらのスクリプトとリポジトリーを使用してください。

`残高繰越`の例の鍵のスニペットをいくつか概観してみましょう。

最初に、クライアント・オブジェクトを作成し、キーと値のストア・インスタンスを設定する必要があります。このインスタンスに証明書と鍵が保持されます。 そのためには単純なファクトリー・メソッド ``newCryptoSuite`` を使用します。このファクトリー・メソッドは、``BaseClient`` から ``Client`` クラスに拡張します。 コードは次のようになっています。

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```
{:codeblock}

マシン上の鍵/値のパスを定義している環境変数をエクスポートし、上記の関数に渡すのが一般的な手法です。 この時点で KVS は定義済みになっているので、``FabricCAServices`` クラスのいくつかのメソッドを使用します。 このクラスは Fabric CA クライアントの実装なので、これにより CA サーバーと通信できるようになります。 最初に多少の情報、つまり CA URL を CA クライアントに渡す必要があります。

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```
{:codeblock}

次に、「登録」呼び出しを CA サーバーに実際に送信します。 x509 証明書内にラップされ、ターゲットの CA により署名された秘密鍵と公開鍵がクライアントに返されます。これを署名付き証明書または登録証明書と呼びます。 この登録証明書つまり「eCert」は鍵の部分です。ネットワーク・エンティティーがこれを使用してクライアントから発信されるトランザクションや呼び出しを検証できるからです。

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```
{:codeblock}

最終作業として、暗号スイートを実際に設定し、ユーザー・コンテキストを構築します。

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```
{:codeblock}

見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。 証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」画面の「証明書」タブ](v10_dashboard.html#members)を参照してください。

## アプリケーションの開発
{: #developing-applications}

アプリケーションの開発を簡単に始める方法は、サンプルのチェーンコードとアプリケーションを、独自のビジネス・ソリューションを作成するためのテンプレートとして使用する方法です。 [サンプル・アプリケーション](howto/prebuilt_samples.html)に、{{site.data.keyword.cloud_notm}} でのブロックチェーン・ネットワークのサンプル・アプリケーションがあります。 独自のビジネス・ニーズに基づいて、アプリケーションを最初から開発することもできます。

JavaScript または Java でアプリケーションを開発し、Hyperledger Fabric Client SDK 内の使用可能な API を利用して、アプリケーションとネットワークの間で対話できるようにすることができます。  少なくとも以下の情報がアプリケーションに含まれている必要があります。
* 呼び出すチェーンコードの名前とバージョン。
* 順序付けサービス、CA、ピアなどのネットワーク・リソースの API エンドポイント情報。
* ネットワーク内で台帳を照会したり更新したりする関数。  高可用性が必要な場合は、アプリケーション内でノード・フェイルオーバーを考慮する必要があります。

### Fabric SDK の使用
{: #use-sdks}

Fabric では、現在、Node.js SDK と Java SDK を提供しており、将来のリリースでは Python、REST、GO などの追加のプログラミング言語をサポートする予定です。Fabric SDK の詳細とこの使用方法について詳しくは、以下の資料を参照してください。

- [Hyperledger Fabric Node SDK の資料![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/){:new_window}
- [Hyperledger Fabric Java SDK の資料![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric-sdk-java){:new_window}

### Fabric SDK でのタイムアウト値の設定
{: #set-timeout-in-sdk}

Fabric SDK は、クライアント・アプリケーションにブロックチェーン・ネットワーク内のイベントについてのデフォルトのタイムアウト値を設定します。 Fabric Java SDK でのデフォルトのタイムアウト設定については、次の例を参照してください。 ファイル・パスは `src&#xa5;main&#xa5;java&#xa5;org&#xa5;hyperledger&#xa5;fabric&#xa5;sdk&#xa5;helper&#xa5;Config.java` です。

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

しかし、独自のアプリケーションでデフォルトのタイムアウト値を変更しなければならない場合があります。 例えば、応答に要する時間が 5000 ミリ秒 (イベント・ハブ接続のデフォルトのタイムアウト値) を超えるトランザクションをアプリケーションが呼び出すと、トランザクションが完了する前に 5000 ミリ秒で呼び出しイベントが終了するため、失敗エラーを受け取ります。 システム・プロパティーを設定して、クライアント・アプリケーションのデフォルト値を上書きできます。 システム・プロパティーを設定する前にデフォルト値が初期設定されるため、システム・プロパティーが有効にならない場合があります。 したがって、クライアント・アプリケーション内の静的構造でタイムアウトのシステム・プロパティーを設定する必要があります。 Fabric Java SDK でイベント・ハブ接続のタイムアウト値を 15000 ミリ秒に変更する場合の以下の例を参照してください。 ファイル・パスは `src&#xa5;main&#xa5;java&#xa5;org&#xa5;hyperledger&#xa5;fabric&#xa5;sdk&#xa5;helper&#xa5;Config.java` です。

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}


## アプリケーションへのネットワーク API エンドポイントの追加
ネットワーク・リソースの API エンドポイントをアプリケーションに追加して、{{site.data.keyword.Bluemix_short}} 上の{{site.data.keyword.blockchain}}・ネットワーク・リソースと対話できるようにする必要があります。  {{site.data.keyword.Bluemix_short}} 上に{{site.data.keyword.blockchain}}・ネットワークがない場合は、Starter Plan または Enterprise Plan のいずれかを使用して作成できます。 詳しくは、[スターター・プラン・ネットワークの管理](get_start_starter_plan.html)および [Enterprise Plan ネットワークのガバナンス](get_start.html)を参照してください。

API エンドポイントの情報は、ネットワークの接続プロファイルにあります。 接続プロファイルは JSON 形式で記述されており、順序付けサービス、CA、ピア・ノードなどのネットワーク・リソースの API エンドポイント情報や登録 ID/機密事項が含まれています。 アプリケーションはこれらの API エンドポイントを介してピアなどのネットワーク・リソースと対話します。

1. 以下のいずれかの方式で、ネットワーク・モニターからネットワーク・リソースの API エンドポイント情報を取得します。
	* チェーンコード固有の API エンドポイント情報を取得するには、チェーンコードを実行している特定のチャネル画面で、そのチェーンコードを見つけて、**「JSON」**ボタンをクリックします。
	![チェーンコードごとの API エンドポイント](images/channel_chaincode.png "チェーンコードごとの API エンドポイント")
	* すべてのネットワーク・リソースに関する完全な API エンドポイント情報のセットを取得するには、「概要」画面で**「接続プロファイル」**ボタンをクリックします。
	![ネットワーク・モニターの「接続プロファイル」](images/service_credentials.png "ネットワーク・モニターの「接続プロファイル」")

2. ネットワーク・リソースの API エンドポイント情報を見つけます。次の例のような情報です。
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```
	{:codeblock}
	**注**: 自分の組織に属していないピアからの承認を必要とするときなど、ネットワーク内の追加のピアをターゲットにする場合は、それらのピアの正しい API エンドポイントを取得する必要があります。  アプリケーションに返された応答を検証するために、他の組織の CA 証明書を保管する必要もあります。 この情報は、自分の接続プロファイルには表示されないため、CLoud Foundry 組織の該当する管理者に連絡して、この情報をアウト・オブ・バンドの操作で取得する必要があります。 順序付けサービス URL は、ネットワーク全体で共通であるため、順序付けサービスにはメンバー固有の情報は必要ありません。

3. 次の例のように、API エンドポイント情報をアプリケーションの構成ファイルにプラグインします。
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```
	{:codeblock}

## CouchDB 索引の使用

ネットワークで CouchDB を使用している場合に (CouchDB のパフォーマンスを向上させる) CouchDB 索引作成機能を利用するには、チェーンコードと一緒に索引をパッケージ化する必要があります。

CouchDB、および索引のセットアップ方法について詳しくは、[CouchDB に関する Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html) を参照してください。

## アプリケーションのホスティング
アプリケーションをローカル・ファイル・システム上でホストするか、{{site.data.keyword.Bluemix_notm}} にプッシュすることができます。 アプリケーションを {{site.data.keyword.Bluemix_notm}} にプッシュするには、以下のステップを実行します。
1. [Cloud Foundry コマンド・ライン・インストーラー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/cloudfoundry/cli/releases) をインストールします。  `cf` コマンドを使用してインストールをテストします。
    * 正常にインストールされた場合は、端末に一連のテキスト出力が表示されます。
    * 「command not found」と表示された場合は、正常にインストールされなかったか、CF がシステム・パスに追加されていません。
2. 以下のコマンドを発行して、API エンドポイントをセットアップし、ご使用の {{site.data.keyword.Bluemix_notm}} ID とパスワードでログインします。
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. アプリケーションのディレクトリーを参照し、以下のコマンドを発行してアプリケーションをプッシュします。 これには、アプリケーションのサイズにより数分かかることがあります。 {{site.data.keyword.Bluemix_notm}} から端末にログが記録されます。これらのログは、アプリケーションが正常に起動されると終了します。
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	以下のいずれかのコマンドを発行して、アプリケーション・ログを確認できます。
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## ネットワークからのアプリケーションの切断
{: #disconnect}
アプリケーションと {{site.data.keyword.Bluemix_short}} 上の{{site.data.keyword.blockchain}}・ネットワークとの間の接続を削除するには、以下のステップを実行します。
1. アプリケーション構成ファイルから API エンドポイント情報を削除します。 参照情報として、[アプリケーションへの API エンドポイントの追加](#adding-network-api-endpoints-to-your-application)を参照してください。
2. チェーンコード・コンテナーを削除します。
	1. ネットワーク・モニターの「チャネル」画面で、チェーンコードがインストールされているチャネルを見つけます。
	2. 特定のチャネル画面で、無効にしようとしているチェーンコードを見つけます。
	3. **「削除」**ボタンをクリックし、チェーンコード削除パネルで**「送信」**をクリックします。 チェーンコード・コンテナーが削除されます。
	![チェーンコードの削除](images/channel_chaincode.png "チェーンコードの削除")

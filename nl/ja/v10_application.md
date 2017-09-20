---

copyright:
  years: 2017
lastupdated: "2017-08-30"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# アプリケーションの開発

アプリケーションを使用すると、チェーンコードを呼び出して、{{site.data.keyword.blockchain}}・ネットワーク内のチャネル固有の台帳を照会したり更新したりできます。
{:shortdesc}

## アプリケーション開発環境のセットアップ
{{site.data.keyword.Bluemix}} 上の{{site.data.keyword.blockchain}}・ネットワークと対話できるアプリケーションを開発するには、ソフトウェア前提条件をインストールする必要があります。
{:shortdesc}

*	Git ([Git のダウンロード・ページ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://git-scm.com/downloads){:new_window})
	
	Git はバージョン管理ツールの 1 つで、簡単に言うと、チェーンコードの開発とソフトウェア開発の両方を把握するためのツールです。また Git bash は、Git と一緒に Windows にインストールされ、Windows コマンド・プロンプトの代わりに使用できる優良なツールです。
 
	Git のインストールを確認するには、以下のコマンドを使用します。下記のような出力が表示されます。  
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([GoLang のダウンロード・ページ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://golang.org/dl){:new_window})

	GoLang をインストールすると、Go の CLI ツールのセットがインストールされます。これらのツールは、チェーンコードの作成に非常に役立ちます。例えば、`go build` コマンドを使用すると、チェーンコードをネットワークにデプロイしようとする前に、チェーンコードが実際にコンパイルされるかどうかを検査できます。このドキュメントの執筆時点では、このチェーンコードはバージョン `1.8.3` で正常にビルドされることが確認されています。

	GoLang のバージョンを確認するには、以下のコマンドを使用します。下記のような出力が表示されます。
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}
	
	[インストールの指示 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://golang.org/doc/install){:new_window} に従って、環境変数を適切に設定します。以下のコマンドを使用して、`GOPATH` を検査します。`GOPATH` がこの例と一致している必要はないことに注意してください。ただ一つ重要なこととして、この変数はファイル・システム上の有効なディレクトリーに設定する必要があります。   
	```
	$ echo $GOPATH
C:\gopath
```
	{:screen}

	その後、[hello world ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://golang.org/doc/install#testing){:new_window} の例を使用して GoLang コードを構築し、GoLang のインストールを確認できます。

*	Node.js ([Node.js のダウンロード・ページ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://nodejs.org/en/download/){:new_window})。6.9.5 から 7 までの間のバージョンを選択します。ノードのバージョンが 7 より大きいと、SDK モジュールをダウンロードする際にエラーが発生します。  

	Node.js のインストールを確認するには、以下のコマンドを使用します。下記のような出力が表示されます。  
	```
	$ node -v
	v6.10.1
	
	$ npm -v
	3.10.10
	```
	{:screen}

## クライアント・サイドの証明書の生成
x509 と Public Key Infrastructure の詳細についてはここでは扱いません。これらについての一般資料は多数存在します。Fabric の通信フローではタッチポイントごとに署名/検証操作が実装されることを知っていれば十分です。したがって、ネットワークに呼び出し (つまりトランザクション) を送信するクライアントは、ペイロードに署名し (秘密鍵)、検証のために適切に署名された x509 証明書 (署名付き証明書) を付加する必要があります。秘密鍵と署名付き証明書は、MSP 識別子と認証局 (CA) ルート証明書とともに、「ユーザー・コンテキスト」オブジェクトと呼ばれるものを構成します。これについても、過剰な詳細情報は不要です。行うのは、該当する認証局と通信して、このオブジェクトを形成するために必要な鍵と証明書を取得することだけです。このプロセスのことを、登録と呼んでいます。ユーザー・コンテキスト・オブジェクトを形成したら、その後の作業は、アプリケーションから API を呼び出してこのユーザー・コンテキスト・オブジェクトの「set」や「get」を行うという簡単なものです。この時点で、アプリケーション (つまりクライアント) に必要な成果物がすべて装備され、ネットワークと通信する準備ができます。鍵と証明書を取得する方法は 2 つあります。続く部分でそれらについて説明します。

### コマンド・ライン
これは 2 つのうち、より簡単な方法です。まず、 [Fabric CA クライアント ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html)を指示に従って構築します。このステップにより、CA サーバーと通信し、適切にフォーマットされた証明書と鍵を受け取ることができます。  

次に、TLS 証明書を [Bluemix ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) からダウンロードし、そのコンテンツを ``$HOME/tls`` などのフォルダーに保存します。このステップにより、データの流れがワイヤー上で暗号化されます。

最後に、ネットワーク・モニターの**「概要」**画面から **Service Credentials** JSON ファイルを開き、以下の情報を確認します。
* CA の URL: ``certificateAuthorities`` の下の ``url``
* 管理者のユーザー ID: ``enrollId``
* 管理者のパスワード: ``enrollSecret``
* CA 名: ``caName``

Fabric CA クライアントを使用し、以下のコマンドを使用して TLS 証明書のパスと上記の 4 つのストリングを渡すことにより、認証局に「登録」呼び出しを送信できます。  
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

``fabric-ca-client`` バイナリーは ``$GOPATH/bin`` 内にあるので、このコマンドは、ローカル・マシン上の適切な場所から実行する必要があります。実際の呼び出しは以下のコマンド例のようになります。  
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```
  
`$HOME/.fabric-ca-client/msp/signcerts/cert.pem` 内で管理者証明書を見つけます。見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」画面の「証明書」タブ](v10_dashboard.html#members)を参照してください。

さらに、以下のディレクトリーで CA ルート証明書と管理者秘密鍵を見つけることができます。
* CA ルート証明書: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* 管理者秘密鍵: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Hyperledger には、認証局とプログラム的に対話する方法を理解するのに役立つ優れたリソースとスクリプトが含まれている 2 つのリポジトリーがあります。``fabric-samples`` リポジトリーには「残高繰越」の例が含まれ、``fabric-sdk-node`` リポジトリーには一連の CA サービス・テストが含まれています。アプリケーション・サイドで登録要求を発行しようとしている場合は、``fabric-ca-client`` と ``fabric-client`` のパッケージで公開されている必要のある API について完全に理解している必要があります。アプリを構成するための基礎として、これらのスクリプトとリポジトリーを使用してください。

「残高繰越」の例の鍵のスニペットをいくつか概観してみましょう。

最初に、クライアント・オブジェクトを作成し、証明書と鍵を保持する鍵/値ストアのインスタンスを設定する必要があります。そのためには単純なファクトリー・メソッド ``newCryptoSuite`` を使用します。このファクトリー・メソッドは、``BaseClient`` から ``Client`` クラスに拡張します。コードは次のようになっています。

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

マシン上の鍵/値のパスを定義している環境変数をエクスポートし、上記の関数に渡すのが一般的な手法です。この時点で KVS は定義済みになっているので、``FabricCAServices`` クラスのいくつかのメソッドを使用します。このクラスは Fabric CA クライアントの実装なので、これにより CA サーバーと通信できるようになります。最初に多少の情報、つまり CA URL を CA クライアントに渡す必要があります。

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

次に、「登録」呼び出しを CA サーバーに実際に送信します。x509 証明書内にラップされ、ターゲットの CA により署名された秘密鍵と公開鍵がクライアントに返されます。これを署名付き証明書または登録証明書と呼びます。この登録証明書つまり「eCert」は鍵の部分です。ネットワーク・エンティティーがこれを使用してクライアントから発信されるトランザクションや呼び出しを検証できるからです。

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

最終作業として、暗号スイートを実際に設定し、ユーザー・コンテキストを構築します。

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」画面の「証明書」タブ](v10_dashboard.html#members)を参照してください。

## アプリケーションの開発
Javascript または Java でアプリケーションを開発し、Hyperledger Fabric Client SDK 内の使用可能な API を利用して、アプリケーションとネットワークの間で対話できるようにすることができます。少なくとも以下の情報がアプリケーションに含まれている必要があります。
* 呼び出すチェーンコードの名前とバージョン。
* 順序付けサービス、CA、ピアなどのネットワーク・リソースの API エンドポイント情報。
* ネットワーク内で台帳を照会したり更新したりする関数。高可用性が必要な場合は、アプリケーション内でノード・フェイルオーバーを考慮する必要があります。

[サンプル・アプリケーション](howto/sample_applications.html)に、{{site.data.keyword.Bluemix_short}} での**エンタープライズ・プラン**のサンプル・アプリケーションがあります。これらのサンプルのチェーンコードとアプリケーションをテンプレートとして使用して、独自のビジネス・ソリューションを作成できます。

## アプリケーションへのネットワーク・サービス資格情報の追加
ネットワーク・コンポーネントのサービス資格情報をアプリケーションに追加して、{{site.data.keyword.Bluemix_short}} 上の{{site.data.keyword.blockchain}}・ネットワーク・コンポーネントと対話できるようにする必要があります。{{site.data.keyword.Bluemix_short}} 上に{{site.data.keyword.blockchain}}・ネットワークがない場合は、[IBM Blockchain Platform の概説](get_start.html)を参照してください。 

サービス資格情報は JSON 形式で記述されており、順序付けサービス、CA、ピア・ノードなどのネットワーク・コンポーネントの API エンドポイント情報や登録 ID/機密事項が含まれています。アプリケーションはこれらの API エンドポイントを介してピアなどのネットワーク・コンポーネントと対話します。  

1. 以下のいずれかの方式で、ネットワーク・モニターからネットワーク・コンポーネントの API エンドポイント情報を取得します。
	* チェーンコード固有の API エンドポイント情報を取得するには、チェーンコードを実行している特定のチャネル画面で、そのチェーンコードを見つけて、**「JSON」**ボタンをクリックします。![チェーンコードごとのサービス資格情報](images/channel_chaincode_detail.png "チェーンコードごとのサービス資格情報")
	* すべてのネットワーク・コンポーネントに関する完全な API エンドポイント情報のセットを取得するには、「リソース」画面で**「サービス資格情報」**ボタンをクリックします。![ネットワーク・モニター内のサービス資格情報](images/service_credentials.png "サービス資格情報ネットワーク・モニター")
	 
2. ネットワーク・リソースの資格情報と API エンドポイント情報を見つけます。次の例を参照してください。
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**注**: 自分の組織に属していないピアからの承認を必要とするときなど、ネットワーク内の追加のピアをターゲットにする場合は、それらのピアの正しい API エンドポイントを取得する必要があります。アプリケーションに返された応答を検証するために、他の組織の CA 証明書を保管する必要もあります。この情報は、自分のサービス資格情報には表示されないため、Bluemix 組織の該当する管理者に連絡して、この情報をアウト・オブ・バンド操作で取得する必要があります。順序付けサービス URL は、ネットワーク全体で共通であるため、順序付けサービスにはメンバー固有の情報は必要ありません。  

3. 次の例のように、API エンドポイント情報をアプリケーションの構成ファイルにプラグインします。  
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## アプリケーションのホスティング
アプリケーションをローカル・ファイル・システム上でホストするか、{{site.data.keyword.Bluemix_notm}} にプッシュすることができます。アプリケーションを {{site.data.keyword.Bluemix_notm}} にプッシュするには、以下のステップを実行します。
1. [Cloud Foundry コマンド・ライン・インストーラー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/cloudfoundry/cli/releases) をインストールします。`cf` コマンドを使用してインストールをテストします。	
    * 正常にインストールされた場合は、端末に一連のテキスト出力が表示されます。 
    * 「command not found」と表示された場合は、正常にインストールされなかったか、CF がシステム・パスに追加されていません。	
2. 以下のコマンドを発行して、API エンドポイントをセットアップし、ご使用の {{site.data.keyword.Bluemix_notm}} ID とパスワードでログインします。
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```  
3. アプリケーションのディレクトリーを参照し、以下のコマンドを発行してアプリケーションをプッシュします。これには、アプリケーションのサイズにより数分かかることがあります。{{site.data.keyword.Bluemix_notm}} から端末にログが記録されます。これらのログは、アプリケーションが正常に起動されると終了します。  
	```
	> cf push YOUR_APP_NAME_HERE
	```  
	以下のいずれかのコマンドを発行して、アプリケーション・ログを確認できます。
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## ネットワークからのアプリケーションの切断
アプリケーションと {{site.data.keyword.Bluemix_short}} 上の{{site.data.keyword.blockchain}}・ネットワークとの間の接続を削除するには、以下のステップを実行します。
1. アプリケーション構成ファイルから API エンドポイント情報を削除します。参照情報として、[アプリケーションへのネットワーク・サービス資格情報の追加](#adding-network-service-credentials-to-your-application)を参照してください。
2. チェーンコード・コンテナーを削除します。
	1. ネットワーク・モニターの「チャネル」画面で、チェーンコードがインストールされているチャネルを見つけます。
	2. 特定のチャネル画面で、無効にしようとしているチェーンコードを見つけます。
	3. **「削除」**ボタンをクリックし、チェーンコード削除パネルで**「送信」**をクリックします。チェーンコード・コンテナーが削除されます。  
![チェーンコードの削除](images/channel_chaincode_detail.png "チェーンコードの削除")

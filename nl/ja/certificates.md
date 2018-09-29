---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# {{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理
{: #certificates}


***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Hyperledger Fabric をベースとする {{site.data.keyword.blockchainfull}} Platform は、許可制ブロックチェーン・ネットワークを構築します。参加者はネットワークのメンバーとして認識され、ネットワーク・リソースに対する参加者のアクティビティーやアクセスは絶えず検証されます。参加者の ID は、信頼できる x509 デジタル証明書の形式で提供されます。検証は、基礎を成す Public Key Infrastructure と、ネットワーク内のトランザクションや管理を保護する署名操作/検証操作によって実装されています。
{:shortdesc}

証明書操作のほとんどは、{{site.data.keyword.blockchainfull_notm}} Platform によって処理されるので、ユーザーが証明書を扱う必要はありません。しかし、アプリケーションを開発したり、リモート・ピアを追加してネットワークを拡張したりするために、ネットワークとの通信を許可する証明書の処理が必要になる場合があります。認証局と、基礎を成す証明書インフラストラクチャーについて、以下に簡単に解説します。このチュートリアルは、使用する証明書や、実行する必要がある作業について理解するのに役立ちます。

## 認証局
{: #network-ca}

認証局 (CA) は、ネットワーク上の ID を提供します。CA は、複数の団体の間でトラスト・アンカーとしての役割を果たす、信頼できる公証人と見なすことができます。ネットワーク内のすべてのエンティティーに、各自のデジタル ID が組み込まれた証明書が、ルート CA の署名付きで付与されます。この証明書が、ネットワーク上で実行されるすべての署名操作と検証操作のトラスト・ルートです。認証局を使用して ID を設定する方法について詳しくは、[Hyperledger Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) を参照してください。

ネットワーク内のメンバーは各自で CA を所有します。メンバーの CA には、ネットワークのルート CA から証明書が発行されます。各組織にとっては、ルート CA とのこの結び付きがトラスト・アンカーになります。組織の CA は、組織が所有するすべてのエンティティー (管理者、ピア、アプリケーションなど) に対する要求に署名します。リモート・ピアや新しいアプリケーションをネットワークに追加する場合は、新しい ID を組織の認証局に登録する必要があります (登録)。それにより、CA は、その新しいエンティティーに、ネットワークと対話するために必要な証明書を付与できます (エンロール)。

### ネットワーク・モニターを使用した登録
{: #ca-panel}

{{site.data.keyword.blockchainfull_notm}} Platform のネットワーク・モニターを使用して、CA に登録されている ID を表示したり、新しい ID を追加したりできます。ネットワーク・モニターの「認証局」パネルにナビゲートします。このパネルには、組織の管理者、ピア、クライアント・アプリケーションを含め、CA に登録されているすべての ID が表示されます。組織の新しい ID を登録するには、パネルの上部の**「ユーザーの追加」**ボタンをクリックします。ポップアップ・ウィンドウが開き、新しい ID の登録に必要な次のフィールドが表示されます。
  - **ID:** 新しい ID の名前です。`エンロール ID` と呼ぶこともあります。 リモート・ピアを構成したり新しいアプリケーションをエンロールしたりするときのために、**この値を保存してください**。
  - **シークレット:** ID に対するパスワードです。`エンロール・シークレット`と呼ぶこともあります。リモート・ピアを構成したり新しいアプリケーションをエンロールしたりするときのために、**この値を保存してください**。  
  - **タイプ:** 登録する ID のタイプを選択します。ピアまたはクライアント・アプリケーションのいずれかです。
  - **所属団体名 (Affiliation):** ID を所属させる組織内の団体名 (`org1` など) です。
  - **最大エンロール回数 (Maximum Enrollments):** このフィールドを使用すると、この ID を使用してエンロールする回数、つまり、証明書を生成する回数を制限できます。このフィールドをブランクのままにすると、エンロール回数はデフォルト値の無制限になります。

[リモート・ピア](howto/remote_peer.html)をデプロイする場合は、このパネルを使用して、新しいピア ID を登録できます。一方、トランザクションをネットワークに送信できるアプリケーションを開発する場合は、クライアントを登録できます。プラットフォームで Fabric SDK を使用する方法について詳しくは、[「アプリケーションの開発」のチュートリアル](v10_application.html)を参照してください。

### クライアント・サイド証明書の生成 (エンロール)
{: #enrollment}
サード・パーティーのクライアントを {{site.data.keyword.blockchainfull_notm}} Platform に接続するには、その前に認証される必要があります。必要な証明書、秘密鍵、パブリック証明書 (エンロール証明書または署名付き証明書とも呼ばれる) を生成するプロセスのことを、エンロールと呼びます。クライアントがネットワークと通信する際には、必ずこれらの証明書が必要になります。ネットワークに対して呼び出しを送信するクライアントは、秘密鍵を使用してペイロードに署名し、正しい署名のある x509 証明書を付加する必要があります。

[Fabric Node SDK を使用してエンロールする](v10_application.html)方法について詳しくは、[「アプリケーションの開発」のチュートリアル](v10_application.html)を参照してください。SDK を使用してエンロールすると、署名付き証明書、その署名付き証明書の作成に使用された公開鍵、および秘密鍵が生成されます。

Fabric CA クライアントを使用して、[コマンド・ライン](#enroll-register-caclient)から証明書を生成することもできます。Fabric CA クライアントは、より包括的な証明書のセットをメンバーシップ・サービス・プロバイダー (MSP) のフォルダー内に戻します。このフォルダーには、CA によって署名されたルート証明書、所属団体に結び付けられた中間証明書、秘密鍵、署名付き証明書が含まれます。MSP やこのフォルダーの内容について詳しくは、下記の[メンバーシップ・サービス・プロバイダー](#msp)のセクションを参照してください。

### 署名付き証明書の {{site.data.keyword.blockchainfull_notm}} Platform へのアップロード
{: #upload-certs}

ネットワークにトランザクションを送信するためにアプリケーションに必要なものは、有効な署名付き証明書のみです。しかし、ピアにチェーンコードをインストールしたり、ピアをチャネルに参加させたりして、クライアントからネットワークを操作する場合は、そのクライアントが管理者として認識されていなければなりません。すべてのコンポーネントは、管理者が所有している署名付き証明書のセットを認識します。クライアントからネットワークを操作する必要がある場合は、署名付き証明書をアップロードし、管理者証明書のリストに追加する必要があります。プラットフォーム上でこの作業を行うには、ネットワーク・モニターの[「概説」パネル](v10_dashboard.html#members)の**「証明書」**タブで署名付き証明書をアップロードします。アップロード後に表示される再始動ボタンを押して、この証明書をピアと同期します。その後、クライアントはネットワークの管理者として認識されるようになります。

<!----
When you add an organization to a channel, this will upload the organization signCert to the channel MSP. Synching the Sign Certs does something.
--->

## TLS 証明書の使用
{: #tls}

[Transport Layer Security ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) は、Hyperledger Fabric の信頼モデルに組み込まれています。{{site.data.keyword.blockchainfull_notm}} Platform のすべてのコンポーネントは、TLS を使用して認証し、相互に通信します。したがって、通信を検証したり暗号化したりするには、プラットフォームから発行された TLS 証明書を呼び出しに付加する必要があります。このチュートリアルで説明してきたその他の証明書は、ネットワークによる取引やネットワークの管理を行う能力を保護するものです。TLS 証明書は、ネットワークに対する呼び出しを保護するためのものです。

TLS 証明書は、プラットフォームから公的に発行されます。すべてのネットワーク・コンポーネントで同じものを使用します。メンバーシップ・プランとクラウドの場所に応じて、以下のリンクから TLS 証明書をダウンロードできます。TLS 証明書は、[資格情報プロファイル](v10_dashboard.html#enterprise-connection-profile "資格情報プロファイル")内にもあります。この証明書は、アプリケーションまたはコマンド・ラインから参照できる場所であればどこに置いてもかまいません。

- スターター・プラン用のルート TLS 証明書  
  - 米国: [us01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - 英国: [uk01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - シドニー: [aus01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
- [エンタープライズ・プラン用のルート TLS 証明書 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

すべての {{site.data.keyword.blockchainfull_notm}} Platform ネットワークはサーバー・サイド TLS を使用するため、ネットワークがクライアントを認証する必要があります。エンタープライズ・プラン・ネットワークでは相互 TLS を有効にすることもできます。この場合は、クライアントとサーバーが互いを認証するので、アプリケーションの保護が強化されます。クライアント・サイド TLS 証明書 (相互 TLS の場合) は、クライアント CA で発行するので、ネットワークに固有の証明書です。エンタープライズ・プラン・ネットワークを使用する場合は、相互 TLS を有効にすることをお勧めします。相互 TLS について詳しくは、これらの[説明](v10_dashboard.html#mutual-tls "相互 TLS の説明")を参照してください。

## メンバーシップ・サービス・プロバイダー (MSP)
{: #msp}

{{site.data.keyword.blockchainfull_notm}} Platform のコンポーネントは、メンバーシップ・サービス・プロバイダー (MSP) を介して ID を使用します。MSP は、CA から発行された証明書を、ネットワークとチャネルの役割に関連付けます。MSP について詳しくは、[Hyperledger Fabric 資料のメンバーシップの概念についてのトピック ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Hyperledger Fabric 資料のメンバーシップの概念についてのトピック") を参照してください。

Fabric の MSP フォルダーには、定義済みの構造があります。Fabric CA クライアントを使用してエンロールした場合は、ローカル・ファイル・システム上の MSP フォルダー内に、以下のサブフォルダーを使用して証明書が格納されます。

- **cacerts:** このフォルダーには、ネットワークのルート CA のルート証明書が含まれます。
- **intermediatecerts:** ネットワークの中間 CA の証明書があります。これらの中間 CA は、ルート CA にリンクされ、トラスト・チェーンを形成します。フェイルオーバーと高可用性を実現するために、中間 CA はエンタープライズ・プラン組織ごとに 2 つあります。
- **signcerts:** このフォルダーには、署名付き公開証明書 (署名付き証明書またはエンロール証明書とも呼ばれる) が含まれます。この証明書は、コマンド・ラインから MSP ディレクトリーを参照したり、SDK を使用してユーザー・コンテキスト・オブジェクトを作成したりする際に、ネットワークに対する呼び出し (チェーンコード呼び出しなど) に付加されます。SDK またはコマンド・ラインからネットワークを操作する場合は、この証明書をプラットフォームにアップロードします。
- **keystore:** このフォルダーには秘密鍵が含まれます。この鍵は、コマンド・ラインから MSP ディレクトリーを参照したり、SDK を使用してユーザー・コンテキスト・オブジェクトを作成したりする際に、ネットワークに対する呼び出しに署名するために使用されます。ネットワークやデータを保護するために、この鍵を安全に保管してください。

ネットワーク・モニターや Swagger API を使用して、Fabric CA クライアントから参照できる MSP フォルダーを作成することもできます。

- **cacerts** および **intermediatecerts**: [Swagger API](howto/swagger_apis.html) で GET 要求を MSP API に発行すると、これらの証明書を取得できます。
- **signcerts** および **keystore**: 「認証局」パネル上の**「証明書の生成 (Generate Certificates)」**ボタンをクリックすると、これらの証明書を生成できます。ポップアップ・ウィンドウが開き、2 つの証明書がリストされます。signcerts に**証明書**を、keystore に **秘密鍵**をコピーして格納してください。これらの証明書はプラットフォーム上には保管されないので、安全な場所に保管してください。

多くの Fabric コンポーネントの MSP フォルダー内には追加情報が含まれています。例えば、リモート・ピアを操作する場合、以下のフォルダーを参照することもできます。

- **admincerts:** このフォルダーには、この組織またはコンポーネントの管理者のリストが含まれています。コマンド・ラインまたは SDK からリモート・ピアを操作するユーザーは、このフォルダーに自分の署名証明書をアップロードする必要があります。
- **tls:** その他のネットワーク・コンポーネントとの通信に使用する TLS 証明書を、このフォルダーに格納します。

MSP の構造について詳しくは、Hyperledger Fabric 資料で[メンバーシップ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "メンバーシップ") および[メンバーシップ・サービス・プロバイダー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "メンバーシップ・サービス・プロバイダー") について参照してください。

## Fabric CA クライアントを使用したエンロールと登録
{: #enroll-register-caclient}

Fabric CA クライアントを使用して、証明書を生成し、新しい ID を認証局に登録することもできます。以下の手順は、管理者 ID を使用して証明書を生成してから、それらの証明書を使用して新しいクライアントを登録する手順です。Fabric CA クライアントを使用してエンロールした場合は、[メンバーシップ・サービス・プロバイダー](#msp)のセクションで説明している証明書が生成されます。

### Fabric CA クライアントを使用したエンロール
{: #enroll-app-caclient}

1. [fabric-ca バイナリー](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/)をローカル・マシンにダウンロードして解凍し、`$HOME/fabric-ca-platform/` などのフォルダー上に移動します。コマンドでクライアント・バイナリーを直接参照できるように、移動先のディレクトリーに変更します。
    ```
    cd $HOME/fabric-ca-platform/
    ```

2.  クライアントが鍵を作成できる場所のパスを設定します。過去にエンロールしようとして作成された構成素材を確実に削除します。前に `enroll` コマンドを実行したことがない場合は、`msp` フォルダーと `.yaml` ファイルは存在しません。
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```

3. 使用するサービス・プラン、ロケーション、およびクラスターに応じて {{site.data.keyword.cloud_notm}} から TLS 証明書をダウンロードします。
  - スターター・プラン用のルート TLS 証明書  
    - 米国: [us01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - 英国: [uk01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - シドニー: [aus01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [エンタープライズ・プラン用のルート TLS 証明書 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  コンテンツをフォルダー (例えば ``$ HOME/tls``) に保存します。 このステップにより、データの流れがワイヤー上で暗号化されます。

4. ネットワーク・モニターの「概説」パネルから**「構成プロファイル」**の JSON ファイルを開き、以下の変数を見つけます。
  * CA の URL: ``certificateAuthorities`` の下の ``url``
  * 管理者のユーザー ID: ``enrollId``
  * 管理者のパスワード: ``enrollSecret``
  * CA 名: ``caName``

5. Fabric CA クライアントを使用して認証局に `enroll` 呼び出しを送信できます。そのためには、以下のコマンドを使用して TLS 証明書のパスと上記の 4 つのストリングを渡します。
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  このコマンドは、Fabric CA クライアントを移動したディレクトリー内で実行する必要があります。実際の呼び出しは以下のコマンド例のようになります。
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

6. `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem` 内で管理者証明書を見つけます。 見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。 証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」パネルの「証明書」タブ](v10_dashboard.html#members)を参照してください。

  さらに、以下のディレクトリーで CA ルート証明書と管理者秘密鍵を見つけることができます。
  * CA ルート証明書: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * 管理者秘密鍵: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Fabric CA クライアントを使用してエンロールし、生成された証明書を使用してネットワーク・コンポーネントを操作する例については、[リモート・ピアを操作する](howto/remote_peer_operate_icp.html#remote-peer-cli-operate)手順を参照してください。

### Fabric CA クライアントを使用した登録
{: #register-app-caclient}

1. 以下のコマンドを実行して、ブロックチェーン・ネットワーク内の所属団体名と組織名を確認します。
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  以下の例のような情報が表示されます。
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  `ibp.PeerOrg1` のような 2 番目の **affiliation** 値をメモします。この値を、以下のコマンドで使用する必要があります。

2. 以下のコマンドを実行して、ピアを登録します。
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  ピアの名前とパスワードを指定します。`name` をピア名に置き換え、`password` をパスワードに置き換えます。この情報はメモしてください。ピアを構成するときに必要になります。 例えば、次のようにします。
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  一度に登録できる ID は 1 つだけです。問題が発生した場合は、別のユーザー名とパスワードを使用してコマンドを試行してください。

  コマンドが正常に完了すると、以下の例のような情報が表示されます。
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

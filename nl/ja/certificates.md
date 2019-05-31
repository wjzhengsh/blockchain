---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: TLS, TLS certificates, client applications, digital certificates, certificate authority, intermediate certificate, client-side certificate, generate certificates, manage certificates

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# {{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理
{: #managing-certificates}

Hyperledger Fabric をベースとする {{site.data.keyword.blockchainfull}} Platform は、許可制ブロックチェーン・ネットワークを構築します。 参加者はネットワークのメンバーとして認識され、ネットワーク・リソースに対する参加者のアクティビティーやアクセスは絶えず検証されます。 参加者の ID は、信頼できる x509 デジタル証明書の形式で提供されます。 検証は、基礎を成す Public Key Infrastructure と、ネットワーク内のトランザクションや管理を保護する署名操作/検証操作によって実装されています。
{:shortdesc}

証明書操作のほとんどは、{{site.data.keyword.blockchainfull_notm}} Platform によって処理されるので、ユーザーが証明書を扱う必要はありません。 しかし、アプリケーションを開発したり、リモート・ピアを追加してネットワークを拡張したりするために、ネットワークとの通信を許可する証明書の処理が必要になる場合があります。 認証局と、基礎を成す証明書インフラストラクチャーについて、以下に簡単に解説します。 このチュートリアルは、使用する証明書や、実行する必要がある作業について理解するのに役立ちます。

## 認証局
{: #managing-certificates-network-ca}

認証局 (CA) は、ネットワーク上の ID を提供します。 CA は、複数の団体の間でトラスト・アンカーとしての役割を果たす、信頼できる公証人と見なすことができます。 ネットワーク内のすべてのエンティティーに、各自のデジタル ID が組み込まれた証明書が、ルート CA の署名付きで付与されます。 この証明書が、ネットワーク上で実行されるすべての署名操作と検証操作のトラスト・ルートです。 認証局を使用して ID を設定する方法について詳しくは、[Hyperledger Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/identity/identity.html) を参照してください。

ネットワーク内のメンバーは各自で CA を所有します。 組織の CA は、所有するすべてのエンティティーおよびコンポーネント (管理者、ピア、アプリケーションなど) に対する要求に署名します。 リモート・ピアや新しいアプリケーションをネットワークに追加する場合は、新しい ID を組織の認証局に登録する必要があります (登録)。 それにより、CA は、エンティティーがネットワークと対話するために必要な証明書を新規エンティティーに付与できます (エンロール)。

### ネットワーク・モニターを使用した登録
{: #managing-certificates-ca-panel}

{{site.data.keyword.blockchainfull_notm}} Platform のネットワーク・モニターを使用して、CA に登録されている ID を表示したり、新しい ID を追加したりできます。 ネットワーク・モニターの「認証局」パネルにナビゲートします。 このパネルには、組織の管理者、ピア、クライアント・アプリケーションを含め、CA に登録されているすべての ID が表示されます。 組織の新しい ID を登録するには、パネルの**「ユーザーの追加」**ボタンをクリックします。 ポップアップ・ウィンドウが開き、新しい ID の登録に必要な次のフィールドが表示されます。
  - **登録 ID:** 新しい ID の名前です。`エンロール ID` と呼ぶこともあります。 リモート・ピアを構成したり新しいアプリケーションをエンロールしたりするときのために、**この値を保存してください**。
  - **機密事項の登録:** ID に対するパスワードです。`エンロール・シークレット`と呼ぶこともあります。 リモート・ピアを構成したり新しいアプリケーションをエンロールしたりするときのために、**この値を保存してください**。
  - **タイプ:** 登録する ID のタイプを選択します。ピアまたはクライアント・アプリケーションのいずれかです。
  - **所属団体名 (Affiliation):** ID を所属させる組織内の団体名 (`org1` など) です。
  - **最大エンロール回数 (Maximum Enrollments):** このフィールドを使用すると、この ID を使用してエンロールする回数、つまり、証明書を生成する回数を制限できます。 このフィールドをブランクのままにすると、エンロール回数はデフォルト値の無制限になります。

[リモート・ピア](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about)をデプロイする場合は、このパネルを使用して、新しいピア ID を登録できます。 一方、トランザクションをネットワークに送信できるアプリケーションを開発する場合は、クライアントを登録できます。プラットフォームで Fabric SDK を使用する方法について詳しくは、[アプリケーションの開発チュートリアル](/docs/services/blockchain/v10_application.html#dev-app)を参照してください。

### クライアント・サイド証明書の生成 (エンロール)
{: #managing-certificates-enrollment}
サード・パーティーのクライアントを {{site.data.keyword.blockchainfull_notm}} Platform に接続するには、その前に認証される必要があります。 必要な証明書、秘密鍵および証明書 (エンロール証明書または署名付き証明書とも呼ばれる) を生成するプロセスをエンロールと呼びます。クライアントがネットワークと通信する際には、必ずこれらの証明書が必要になります。 ネットワークに対して呼び出しを送信するクライアントは、秘密鍵を使用してペイロードに署名し、正しい署名のある x509 証明書を付加する必要があります。

[Fabric Node SDK を使用してエンロールする](/docs/services/blockchain/v10_application.html#dev-app)方法について詳しくは、[「アプリケーションの開発」のチュートリアル](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk)を参照してください。 SDK を使用してエンロールすると、秘密鍵、署名付き証明書、および署名付き証明書の作成に使用された公開鍵の 3 つの別個の項目が生成されます。

[Fabric CA クライアント](/docs/services/blockchain/certificates.html#managing-certificates-enroll-register-caclient)を使用して、コマンド・ラインから証明書を生成することもできます。 Fabric CA クライアントは、より包括的な証明書のセットをメンバーシップ・サービス・プロバイダー (MSP) のフォルダー内に戻します。 このフォルダーには、CA が署名したルート証明書、中間証明書、秘密鍵、および署名付き証明書が含まれています。 MSP および MSP フォルダーの内容について詳しくは、[メンバーシップ・サービス・プロバイダー](/docs/services/blockchain/certificates.html#managing-certificates-msp)を参照してください。

認証局に登録されている ID でのみ (つまり、その ID の名前と秘密を使用してのみ) 証明書を生成できます。 デフォルトでは、**管理者** ID が既に CA に登録されており、「認証局」画面にリストされます。 ネットワーク・モニターの「概要」画面にある**「接続プロファイル」**ボタンをクリックすると、接続プロファイル内の管理者 ID の秘密を確認できます。 ネットワーク・モニターの「認証局」画面で[「ユーザーの追加」](/docs/services/blockchain/certificates.html#managing-certificates-ca-panel)ボタンをクリックして新規 ID を登録し、新規 ID の名前と秘密を使用して証明書を生成することもできます。

**注:** Fabric Node SDK または Fabric CA クライアントを使用して上記の証明書を生成する手順に従う場合、管理者 ID を使用してエンロールすることから開始します。 次に、それらの証明書を使用して、CA に新しいクライアント ID を登録します。 [アプリケーションの開発](/docs/services/blockchain/v10_application.html#dev-app)の SDK 手順を使用する場合、クライアント ID を使用して再度エンロールします。 次に、それらの証明書を使用して、ネットワークにトランザクションを送信できます。<!---You can an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

### ネットワーク・モニターを使用した証明書の生成
{: #managing-certificates-certs-panel}

ネットワーク・モニターでは、管理者 ID を使用して証明書を生成し、それらの証明書を SDK に直接渡すことができます。 管理者 ID の横にある**「証明書の生成」**ボタンをクリックして、CA から新しい署名付き証明書と秘密鍵を取得します。 **「証明書」**フィールドには、署名付き証明書が含まれており、**「秘密鍵」**のすぐ上にあります。 各フィールドの端にあるコピー・アイコンをクリックすると、そのフィールドの値をコピーできます。 その後、アプリケーションにインポートできる場所にこれらの証明書を保存する必要があります。詳しくは、[アプリケーションの開発チュートリアル](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel)を参照してください。 {{site.data.keyword.blockchainfull_notm}} Platform にはそれらの証明書が保管されないことに**注意してください**。 ユーザーがそれらを安全に保存して保管する必要があります。

### 署名付き証明書の {{site.data.keyword.blockchainfull_notm}} Platform へのアップロード
{: #managing-certificates-upload-certs}

ネットワークにトランザクションを送信するためにアプリケーションに必要なものは、有効な署名付き証明書のみです。 しかし、ピアにチェーンコードをインストールしたり、ピアをチャネルに参加させたりして、クライアントからネットワークを操作する場合は、そのクライアントが管理者として認識されていなければなりません。 各コンポーネントは、管理者が所有する一連の署名付き証明書を認識します。 クライアントからネットワークを操作する必要がある場合は、署名付き証明書をアップロードし、管理者証明書のリストに追加する必要があります。 プラットフォーム上でこの作業を行うには、ネットワーク・モニターの[「概説」パネル](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members)の**「証明書」**タブで署名付き証明書をアップロードします。 アップロード後に表示される再始動ボタンを押して、この証明書をピアと同期します。 その後、クライアントはネットワークを操作できるようになります。 また、[Swagger API](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) を使用して署名付き証明書をアップロードし、管理者証明書を追加することもできます。

チャネルも、チャネルの操作 (チャネルでチェーンコードをインスタンス化できることを含む) を許可された ID からの一連の管理者証明書を認識します。 リモート・クライアントで新しい署名付き証明書を使用する場合、チェーンコードをインスタンス化する前に、その証明書をチャネルに同期する必要があります。 証明書をチャネルに追加するには、ネットワーク・モニターで以下のステップを実行します。

1. 「メンバー」画面の**「証明書」**タブにナビゲートします。 **「証明書の追加」**ボタンをクリックして、署名付き証明書をプラットフォームにアップロードします。
2. 「チャネル」画面にナビゲートし、関連するチャネルの名前を見つけます。
3. **「アクション」**ヘッダーの下のドロップダウン・リストから**「証明書の同期」**をクリックして、新しい証明書をチャネル管理者証明書のリストに追加します。

**注:** 組織の管理者 ID を使用して生成される証明書は、自動的には管理者証明書と見なされません。 **「証明書の生成」**ボタンを使用して生成される署名付き証明書と秘密鍵では、SDK によるネットワークの操作は許可されません。 それらは、Fabric CA クライアント、SDK、またはネットワーク・モニターによって生成されており、ネットワーク・コンポーネントが認識する管理者証明書の既存のリストには接続されていません。 SDK でネットワークを操作する前に、ネットワーク・モニターで署名付き証明書をアップロードする必要があります。

### 証明書の有効期限
{: #managing-certificates-expiration}

CA が {{site.data.keyword.blockchainfull_notm}} Platform で生成する証明書は、1 年または 3 年後に有効期限が切れます。 この有効期限は、Fabric SDK、Fabric CA クライアント、または[ネットワーク・モニター](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel)を使用して生成される証明書の有効期限と同じです。 証明書の有効期限が切れると、アプリケーションはネットワークと対話できなくなります。 新しい証明書を生成するために再エンロールする必要があります。 ユーザーのエンロール制限に達している場合は、新規ユーザーを登録してエンロールすることができます。 以前の証明書を使用してネットワークを操作していた場合は、新しい証明書をプラットフォームにアップロードする必要もあります。

コマンド・ラインを使用して、証明書の有効期限日付を確認できます。 以下のコマンドを実行して、人間が読める形式で証明書を表示します。
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

クライアント証明書は以下の例のようになります。

```
Certificate:
    Data:
        Version: 3 (0x2)
    Serial Number:
        20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18
Signature Algorithm: ecdsa-with-SHA256
    Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA
    Validity
        Not Before: Nov 28 19:18:00 2018 GMT
        Not After : Nov 28 19:23:00 2019 GMT
    Subject: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin
    ...
    ...
```

有効期限日付は、**Validity** セクションの `Not After:` の後にあります。この例では、証明書の有効期限日付は 2019 年 11月 28 日です。

## TLS 証明書の使用
{: #managing-certificates-tls}

[Transport Layer Security ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) は、Hyperledger Fabric の信頼モデルに組み込まれています。 {{site.data.keyword.blockchainfull_notm}} Platform のすべてのコンポーネントは、TLS を使用して認証し、相互に通信します。 したがって、通信を検証したり暗号化したりするには、プラットフォームから発行された TLS 証明書を呼び出しに付加する必要があります。 このチュートリアルで説明してきたその他の証明書は、ネットワークによる取引やネットワークの管理を行う能力を保護するものです。 TLS 証明書は、ネットワークに対する呼び出しを保護するためのものです。

TLS 証明書は、プラットフォームから公的に発行されます。すべてのネットワーク・コンポーネントで同じものを使用します。 メンバーシップ・プランとクラウドの場所に応じて、以下のリンクから TLS 証明書をダウンロードできます。 TLS 証明書は、[資格情報プロファイル](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-connection-profile)内にもあります。 この証明書は、アプリケーションまたはコマンド・ラインから参照できる場所であればどこに置いてもかまいません。

- スターター・プラン用の TLS 証明書
  - 米国: [us01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
    [us03.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
  [us05.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
    [us07.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
  - 英国: [uk01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
    [uk03.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
  - シドニー: [aus01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")
- [エンタープライズ・プラン用の TLS 証明書 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

すべての {{site.data.keyword.blockchainfull_notm}} Platform ネットワークはサーバー・サイド TLS を使用するため、ネットワークがクライアントを認証する必要があります。 エンタープライズ・プラン・ネットワークでは相互 TLS を有効にすることもできます。この場合は、クライアントとサーバーが互いを認証するので、アプリケーションの保護が強化されます。 クライアント・サイド TLS 証明書 (相互 TLS の場合) は、クライアント CA で発行するので、ネットワークに固有の証明書です。 エンタープライズ・プラン・ネットワークを使用する場合は、相互 TLS を有効にすることをお勧めします。 相互 TLS について詳しくは、これらの[説明](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-mutual-tls)を参照してください。

### TLS 証明書からのドメイン・ネームの取得
{: #managing-certificates-retrieve-domain}

{{site.data.keyword.blockchainfull_notm}} Platform 外部にある Hyperledger Fabric コンポーネントと通信する場合は、正しいドメイン・ネームを使用して呼び出しを送信する必要があります。 コンポーネントのドメイン・ネームを解決せずにコンポーネントの IP アドレスに呼び出しを送信すると、呼び出しは拒否され、エラーで返されます。

プラットフォームでホストされるコンポーネントの完全な URL およびドメイン・ネームは、接続プロファイルで確認できます。 以下の例は、ピアの完全な URL を示しており、ピアのドメイン・ネーム `us01.blockchain.ibm.com` と連結されています。
```
grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us01.blockchain.ibm.com:31002"
```

コンポーネントのドメイン・ネームは、TLS 証明書でも確認できます。

1. 上記のリストからルート TLS 証明書の 1 つをダウンロードし、ローカル・マシンに保存します。
2. ルート TLS 証明書と同じディレクトリーで、以下のコマンドを実行します。 このコマンドは、コマンド・ラインに、人間が読める形式で証明書を表示できます。 コマンド・ラインで、ドメイン・ネームなどの重要な情報を確認できます。
  ```
  openssl x509 -in <certificate file> -text
  ```
  {:codeblock}

例えば、リストされている最初の証明書をダウンロードし、コマンド `openssl x509 -in us01.blockchain.ibm.com.cert -text` を発行すると、出力の一部として以下のコードが表示されます。

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            05:5c:42:fb:90:b9:cb:3d:60:7c:e4:ec:7f:7f:8e:38
    Signature Algorithm: ecdsa-with-SHA256
        Issuer: C=US, O=DigiCert Inc, CN=DigiCert ECC Secure Server CA
        Validity
            Not Before: Jun 11 00:00:00 2018 GMT
            Not After : Jun 19 12:00:00 2019 GMT
        Subject: C=US, ST=New York, L=Armonk, O=International Business Machines Corporation, CN=*.us01.blockchain.ibm.com
    ...
    ...
```

ドメイン・ネームは、サブジェクト行に `CN=*.us01.blockchain.ibm.com` としてリストされます。 証明書のさらに下の方にリストされている代替ドメイン・ネームも確認できます。

TLS 証明書からコンポーネントのドメイン・ネームを取得すると、{{site.data.keyword.blockchainfull_notm}} Platform 外部でホストされているリモート・ピアまたは Fabric コンポーネントと通信する場合に役立ちます。 その後、SDK の使用時にドメイン・ネームを SSL オーバーライドに追加することも、ドメイン・ネームおよび対応 IP アドレスを `etc.hosts` ファイルに追加することもできます。

## メンバーシップ・サービス・プロバイダー (MSP)
{: #managing-certificates-msp}

{{site.data.keyword.blockchainfull_notm}} Platform のコンポーネントは、メンバーシップ・サービス・プロバイダー (MSP) を介して ID を使用します。 MSP は、CA から発行された証明書を、ネットワークとチャネルの役割に関連付けます。 MSP について詳しくは、[Hyperledger Fabric 資料のメンバーシップの概念についてのトピック ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html "Hyperledger Fabric 資料のメンバーシップの概念についてのトピック") を参照してください。

Fabric の MSP フォルダーには、定義済みの構造があります。 Fabric CA クライアントを使用してエンロールした場合は、ローカル・ファイル・システム上の MSP フォルダー内に、以下のサブフォルダーを使用して証明書が格納されます。

- **cacerts:** このフォルダーには、ネットワークのルート CA のルート証明書が含まれます。
- **intermediatecerts:** ネットワークの中間 CA の証明書があります。 これらの中間 CA は、ルート CA にリンクされ、トラスト・チェーンを形成します。 フェイルオーバーと高可用性を実現するために、中間 CA はエンタープライズ・プラン組織ごとに 2 つあります。
- **signcerts:** このフォルダーには、署名証明書 (署名付き証明書またはエンロール証明書とも呼ばれる) が含まれます。この証明書は、コマンド・ラインから MSP ディレクトリーを参照したり、SDK を使用してユーザー・コンテキスト・オブジェクトを作成したりする際に、ネットワークに対する呼び出し (チェーンコード呼び出しなど) に付加されます。 SDK またはコマンド・ラインからネットワークを操作する場合は、この証明書をプラットフォームにアップロードします。
- **keystore:** このフォルダーには秘密鍵が含まれます。 この鍵は、コマンド・ラインから MSP ディレクトリーを参照したり、SDK を使用してユーザー・コンテキスト・オブジェクトを作成したりする際に、ネットワークに対する呼び出しに署名するために使用されます。 ネットワークやデータを保護するために、この鍵を安全に保管してください。

ネットワーク・モニターや Swagger API を使用して、Fabric CA クライアントが参照できる MSP フォルダーを作成することもできます。

- **cacerts** および **intermediatecerts**: [Swagger API](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) で GET 要求を MSP API に発行すると、これらの証明書を取得できます。
- **signcerts** および **keystore**: 「認証局」パネル上の**「証明書の生成 (Generate Certificates)」**ボタンをクリックすると、これらの証明書を生成できます。 ポップアップ・ウィンドウが開き、2 つの証明書がリストされます。 signcerts に**証明書**を、keystore に **秘密鍵**をコピーして格納してください。 これらの証明書はプラットフォーム上には保管されないので、安全な場所に保管してください。

多くの Fabric コンポーネントの MSP フォルダー内には追加情報が含まれています。 例えば、リモート・ピアを操作する場合、以下のフォルダーを参照することもできます。

- **admincerts:** このフォルダーには、この組織またはコンポーネントの管理者のリストが含まれています。 コマンド・ラインまたは SDK からリモート・ピアを操作する場合は、署名付き証明書をこのフォルダーにアップロードする必要があります。 Fabric CA クライアントを使用する場合は、MSP に、管理者証明書として認識される対応する署名付き証明書を含む admincerts フォルダーも必要です。
- **tls:** その他のネットワーク・コンポーネントとの通信に使用する TLS 証明書を、このフォルダーに格納します。

MSP の構造について詳しくは、Hyperledger Fabric 資料で[メンバーシップ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html "メンバーシップ") および[メンバーシップ・サービス・プロバイダー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/msp.html "メンバーシップ・サービス・プロバイダー") について参照してください。

## Fabric CA クライアントを使用したエンロールと登録
{: #managing-certificates-enroll-register-caclient}

Fabric CA クライアントを使用して、証明書を生成し、新しい ID を認証局に登録することもできます。 以下の手順は、管理者 ID を使用して証明書を生成してから、それらの証明書を使用して新しいクライアントを登録する手順です。 Fabric CA クライアントを使用したエンロールおよび証明書の生成について詳しくは、[メンバーシップ・サービス・プロバイダー](/docs/services/blockchain/certificates.html#managing-certificates-msp)を参照してください。

### Fabric CA クライアントを使用したエンロール
{: #managing-certificates-enroll-app-caclient}

1. [Fabric CA バイナリー](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/)をローカル・マシンにダウンロードして解凍し、`$HOME/fabric-ca-platform/` などのフォルダーに移動します。 コマンドでクライアント・バイナリーを直接参照できるように、移動先のディレクトリーに変更します。
    ```
    cd $HOME/fabric-ca-platform/
    ```
    {:codeblock}

2.  クライアントが鍵を作成できる場所のパスを設定します。 過去にエンロールしようとして作成された構成素材を確実に削除します。 前に `enroll` コマンドを実行したことがない場合は、`msp` フォルダーと `.yaml` ファイルは存在しません。
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3. 使用するサービス・プラン、ロケーション、およびクラスターに応じて {{site.data.keyword.cloud_notm}} から TLS 証明書をダウンロードします。 認証局の URL に基づいてサービス・プランを確認できます。
  - スターター・プラン用の TLS 証明書
    - 米国: [us01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert"); [us03.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert"); [us05.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert"); [us07.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - 英国: [uk01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
    [uk03.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - シドニー: [aus01.blockchain.ibm.com.cert ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")
  - [エンタープライズ・プラン用の TLS 証明書 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

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

  このコマンドは、Fabric CA クライアントを移動したディレクトリー内で実行する必要があります。 実際の呼び出しは以下のコマンド例のようになります。
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

6. `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem` 内で管理者証明書を見つけます。 見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。 証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」パネルの「証明書」タブ](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members)を参照してください。

  さらに、以下のディレクトリーで CA ルート証明書と管理者秘密鍵を見つけることができます。
  * CA ルート証明書: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * 管理者秘密鍵: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Fabric CA クライアントを使用してエンロールし、生成された証明書を使用してネットワーク・コンポーネントを操作する例については、[リモート・ピアを操作する](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-cli-operate)手順を参照してください。

### Fabric CA クライアントを使用した登録
{: #register-app-caclient}

1. 以下のコマンドを実行して、ブロックチェーン・ネットワーク内の所属団体名と組織名を確認します。
  ```
  ./fabric-ca-client affiliation list --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  以下の例のような情報が表示されます。
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```

  `ibp.PeerOrg1` のような 2 番目の **affiliation** 値をメモします。 この値を、以下のコマンドで使用する必要があります。

2. 以下のコマンドを実行して、ピアを登録します。
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  ピアの名前とパスワードを指定します。`name` をピア名に置き換え、`password` をパスワードに置き換えます。 この情報はメモしてください。 ピアを構成するときに必要になります。 例えば、次のようにします。
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

  一度に登録できる ID は 1 つだけです。 問題が発生した場合は、別のユーザー名とパスワードを使用してコマンドを試行してください。

  コマンドが正常に完了すると、以下の例のような情報が表示されます。
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```

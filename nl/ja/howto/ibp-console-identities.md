---

copyright:
  years: 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:important: .important}
{:note: .note}
{:pre: .pre}

# アイデンティティーの作成および管理
{: #ibp-console-identities}

Hyperledger Fabric をベースとする {{site.data.keyword.blockchainfull_notm}} Platform のノードは、許可制ブロックチェーン・ネットワークを構築します。 つまり、ブロックチェーン共同事業体のすべての参加者には Public Key Infrastructure によって継続的に検証されるアイデンティティーが必要となります。 {{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用すれば、組織の認証局 (CA) によってこのようなアイデンティティーを作成できます。 これらのアイデンティティーをコンソール・ウォレット内に保管し、ネットワークの操作に使用できるようにする必要があります。

**対象者:** このトピックは、ブロックチェーン・ネットワークの作成、モニター、管理を担当するネットワーク・オペレーターを対象に設計されています。

## 認証局の管理
{: #ibp-console-identities-manage-ca}

CA は、複数の当事者の間でトラスト・アンカーとしての役割を果たす信頼できる公証人のようなものと考えることができ、共同事業体内の組織ごとに独自の CA が保守されます。 CA によって、組織に属するアイデンティティーが作成され、各 ID に公開鍵と秘密鍵が発行されます。 これらの鍵を使用して、すべてのノードおよびアプリケーションにおけるアクションに対する署名および検証が可能になります。 CA を使用してアイデンティティーを設定する方法について詳しくは、Hyperledger Fabric 資料の [アイデンティティーに関するトピック ![外部リンク・アイコン ](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "アイデンティティー") を参照してください。

{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用して CA を作成すると、ルート CA と TLS CA という、同じエンドポイント URL を持つ 2 つの CA が作成されます。 これら両方の CA は、コンソールの**「ノード」**ページの同じ表示名の下に表示されます。 ルート CA は、ノードおよびアプリケーションに鍵を提供します。 TLS CA は、ネットワーク内の通信を保護するために使用される証明書を提供します。 ネットワークの TLS について詳しくは、[TLS CA の使用](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca)を参照してください。

ノードを作成する場合、ルート CA を使用して、ネットワークでのデプロイ、操作、および対話に必要な以下のアイデンティティーを作成する必要があります。
- **CA 管理者:** CA にはデフォルトの CA 管理者アイデンティティーが含まれます。 このアイデンティティーには登録 ID と機密事項があり、これらはユーザー名とパスワードに類似したもので、CA のデプロイメント時にこれらを指定します。 この管理者ユーザー名とパスワードを使用して、CA を操作したり新規アイデンティティーを作成したりすることができます。 コンソールを使用して CA を作成した場合、別個の TLS CA 管理者を作成しない限り、ルート CA の CA 管理者が TLS CA の管理者にもなります。
- **ピアまたは順序付けプログラム:** 組織に属するピアおよび順序付けプログラムごとにアイデンティティーを登録する必要があります。 デプロイメント時にこれらのアイデンティティーがノードで使用され、ネットワークに参加するために必要な鍵が生成されます。 セキュリティーのため、デプロイするノードごとに固有の登録 ID と機密事項を作成します。
- **ピアまたは順序付けプログラムの管理者:** {{site.data.keyword.blockchainfull_notm}} Platform のノードは、内部にあるコンポーネント管理者アイデンティティーの公開鍵を使用してデプロイされます。 これらの証明書によって、管理者はリモート・クライアントから、またはコンソールを使用してコンポーネントを操作できます。
- **組織管理者:** 順序付けサービスによってホストされる共同事業体に参加する場合、組織の管理者になるアイデンティティーの公開鍵を提供します。 これらのアイデンティティーを使用して、チャネルを作成したり編集したりすることができます。
- **アプリケーション:** トランザクションは送信される前にアプリケーションによって署名され、ネットワークによって検証される必要があります。 クライアント・アプリケーションからのトランザクションの署名に使用できるアイデンティティーを作成する必要があります。

[登録の処理](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)で、コンソールを使用してこれらのアイデンティティーを作成できます。 管理者アイデンティティーを登録した後、各 ID に公開鍵と秘密鍵を発行し、組織の MSP 定義に公開鍵を提供し、コンソール・ウォレットにアイデンティティーを追加する必要があります。 [組織の MSP を作成する](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)場合、1 つの管理者アイデンティティーでこれらのステップを実行できます。 組織管理者とノード管理者の別個のアイデンティティーを使用することも、1 つのアイデンティティーを使用して両方の作業を実行することもできます。 [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) では、チュートリアルで作成する組織ごとに、管理者になる 1 つのアイデンティティーを使用します。

## CA アイデンティティーの設定
{: #ibp-console-identities-ca-identity}

アイデンティティーを使用して作業する前に、CA 管理者アイデンティティーを設定する必要があり、CA 作成時に作成された管理者アイデンティティーを使用するか、または新規アイデンティティーを設定します。 **「ノード」**タブで CA を開きます。 パネル上部の CA 名の隣に、現在アクティブなアイデンティティーの登録 ID が表示されます。 その管理者アイデンティティーを使用して、**「登録」**ボタンによって他のアイデンティティーを作成して組織管理者およびノード・アイデンティティーになるアイデンティティーを登録したり、**「エンロール (Enroll)」**ボタンによってアイデンティティーを生成してウォレットにエクスポートしたりすることができます。

別のアイデンティティーに切り替えて CA 管理者として使用するには、**「設定」**アイコンをクリックし、スライダーの**「アイデンティティーの設定 (Set Identity)」**をクリックします。 **「既存のアイデンティティー (Existing identity)」**タブを使用して、ウォレットに存在するアイデンティティーを指定できます。 また、**「新規アイデンティティー (New identity)」**タブを使用して、新規管理者の証明書 (base64 形式または PEM 形式) が含まれているファイルをアップロードするか、証明書が含まれている JSON ファイルをアップロードすることができます。

すべてのアイデンティティーで新規ユーザーを登録できるわけではありません。 追加の CA 管理者の設定方法を含め、詳しくは、[新規 CA 管理者の作成](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin)を参照してください。
{: note}

## アイデンティティーの登録
{: #ibp-console-identities-register}

アイデンティティー作成の最初のステップは、**登録**と呼ばれます。 登録中に、登録 ID と機密事項が作成され、ノードまたは組織管理者によってこれらが使用されて公開鍵と秘密鍵が生成されることによって、このアイデンティティーは**エンロール**されます。

CA で新規アイデンティティーを登録する場合、以下の情報を入力する必要があります。

- **登録 ID** および**機密事項の登録**: アイデンティティーには ID と機密事項があり、これらはユーザー名とパスワードに類似したもので、CA のデプロイメント時にこれらを指定します。 この管理者 ID と機密事項を使用し、このコンソールまたは Fabric CA クライアントを使用してこのアイデンティティーで証明書を作成できます。
- **タイプ**: CA のデプロイ時に、管理者は CA によって発行されるアイデンティティーのタイプを指定します。 アイデンティティー・タイプの一般的な例は、ピア、順序付けプログラム、クライアント (アプリケーション) などです。 使用可能なタイプのリストから、このユーザーのアイデンティティー・タイプを選択します。
- **アフィリエーション**: このユーザーを所属させる組織の部分。 例えば、これはアプリケーションまたはピアを操作する部門やユニットになる場合があります。 特定の CA 管理者が組織の独自部門内のユーザーのみを登録できるように制限できます。
- **最大登録数**: オプションとして、このアイデンティティーを使用して証明書を登録または生成できる回数を入力します。 登録の回数制限を指定することによって、ノードやアプリケーションのセキュリティーを保護できます。 デフォルトでは登録の制限はありません。
- **属性 (Attributes)**: オプションとして、ユーザーに[属性ベース・アクセス制御 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "属性ベース・アクセス制御") 属性を指定できます。 例えば、このセクションを使用して、新規アイデンティティーを登録およびエンロールする権限を持つ[別の CA 管理者を作成](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)できます。 使用可能な Fabric CA 属性の全リストについては、Fabric CA ユーザーズ・ガイドの [Registering a new identity ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registering a new identity") セクションを参照してください。

CA の概要パネルで**「ユーザーの登録」**ボタンをクリックして、新規ユーザーを作成します。 この作業を始める前に、新規ユーザーを登録できるアイデンティティーを使用して[アイデンティティーが設定](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)されていることを確認してください。 通常、これは `admin` ユーザーです。 ボタンがグレー表示されている場合は、アイデンティティーが設定されていないか、またはアイデンティティーで新規アイデンティティーを作成できないことを意味します。  

**「ユーザーの登録」**をクリックすると、一連のサイド・パネルが開きます。
1. 最初のサイド・パネルでは、新規アイデンティティーの**「登録 ID」**および**「機密事項の登録」**を入力します。 これらはコンソールに保管されないため、**これらの値を保存してください**。
2. 2 番目のサイド・パネルでは、アイデンティティーの**「タイプ」**を選択します。 ドロップダウン・リストには、この CA でサポートされるタイプのリストが含まれています。 次に、新規アイデンティティーが属する**「アフィリエーション」**を選択します。 ドロップダウン・リストから既存のアフィリエーションを選択するか、新しいアフィリエーションを入力することができます。
3. 3 番目のサイド・パネルでは、このアイデンティティーで許可される**「最大登録数」**を入力します。 指定しない場合、値はデフォルトで無制限の登録に設定されます。
4. 最後のサイド・パネルでは、作成するアイデンティティーの**「属性 (Attributes)」**を追加します。

**「登録」**をクリックすると、CA によって作成された**「認証済みユーザー (Authenticated users)」**のリストに新規アイデンティティーが追加されます。 アイデンティティーは、**「登録 ID」**順に、それらの**「タイプ」**と**「アフィリエーション」**とともにリストされます。 表のアイデンティティーをクリックすると、登録中に作成された**「最大登録数」**の数と**「属性 (Attributes)」**が表示されたサイド・パネルが開きます。

### 新規 CA 管理者の作成
{: #ibp-console-identities-ca-admin}

デフォルトでは、デプロイメント中に作成された CA 管理者のみが新規アイデンティティーを登録できます。 また、登録処理の**「属性 (Attributes)」**パネルを使用して、新規ユーザーを登録できる他のアイデンティティーを作成することができます。

サイド・パネル 4 で、**「属性の追加 (Add Attribute)」**ボタンをクリックします。 **属性名** `hf.Registrar.Roles` を指定します。 **属性値** `*` を入力します。 このパネルを使用して、特定のアイデンティティー・タイプ (クライアントやピアなど) のみ、または特定のアフィリエーション内にのみ登録できるアイデンティティーを作成することもできます。 また、アイデンティティー、およびアイデンティティーが発行したすべての証明書を取り消すことができるアイデンティティーを作成できます。 属性の全リストについては、Fabric CA ユーザーズ・ガイドの [Registering a new identity ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registering a new identity") セクションを参照してください。

## アイデンティティーのエンロール
{: #ibp-console-identities-enroll}

CA で登録されたアイデンティティーごとにパブリック証明書と秘密鍵を生成できます。 CA で追加の管理者アイデンティティーを登録した場合は、[組織の MSP を作成](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)する際に、追加した管理者アイデンティティー用の鍵を生成し、それを追加で組み込むことができます。

アイデンティティーをエンロールする前に、CA を操作できるように[アイデンティティーを設定](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)する必要があります。 通常は、CA の作成時に指定した管理者アイデンティティーに設定することになります。 CA の詳細ページを調べて、CA 名の隣にある現在アクティブなアイデンティティーの登録 ID を参照すると、CA がこのアイデンティティーに設定されていることを確認できます。 アイデンティティーが管理者アイデンティティーに設定されていることを確認し終えたら、ユーザーのオーバーフロー・メニュー上の**「アイデンティティーのエンロール (Enroll identity)」**をクリックして、CA で登録されたユーザーに関する証明書と鍵を生成します。

- ユーザーの`登録機密事項`を入力します。
- 次のステップで、生成された鍵が表示されます。
  - 公開鍵は**「証明書」**フィールドに表示されます。 この証明書は、エンロールメント証明書、署名証明書、または署名付き証明書とも呼ばれます。 署名付き証明書をローカル・システム上のファイルにエクスポートして、VSCode 拡張機能を使用してクライアント・アプリケーションを作成する際に使用できるようにする必要があります。
  - 対応する秘密鍵は**「秘密鍵」**フィールドに表示されます。 再度、秘密鍵をローカル・システムにエクスポートして、VSCode 拡張機能を使用して作成したクライアント・アプリケーションと併用できるようにする必要があります。
  - **「アイデンティティーのエンロール (Enroll Identity)」**をクリックすると作成される証明書と秘密鍵は 1 回のみ生成され、コンソールやブラウザーでは保管されません。 **「アイデンティティーのエンロール (Enroll Identity)」**ボタンをクリックすると、CA 管理者で設定した最大登録数にもカウントされます。 このエンロールの一部として、アイデンティティーをローカル・ファイル・システムにダウンロードして鍵ペアを保管するか、コンソール・ウォレットに追加する必要があります。 この公開鍵と秘密鍵のペアの新規名を**「名前」**フィールドに入力して、取得します。
- **重要:** **「アイデンティティーのエクスポート (Export identity)」**をクリックして、この証明書をローカル・ファイル・システムに JSON 形式の単一のファイルとしてダウンロードします。 これらの鍵の保護と管理は、お客様の責任となります。
- **「アイデンティティーのウォレットへの追加 (Add identity to wallet)」**をクリックして、これらの証明書をコンソール・ウォレットに追加します。 その後、[ウォレットのタブ](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)の新規タイルに、このアイデンティティーの名前と鍵が表示されます。

コンソールで作成したアイデンティティーを Fabric CA クライアントまたは Fabric SDK を使用してエンロールすることもできます。 コンソールでは、これらのステップの実行に必要なすべての情報が提供されます。 登録時に指定した**「登録 ID」**および**「機密事項の登録」**が保存されていることを確認してください。

## TLS CA の使用
{: #ibp-console-identities-tlsca}

ネットワーク内の通信は TLS 証明書によって保護されます。 TLS によってノード間およびノードとアプリケーション間の通信は暗号化されます。 TLS を使用すると、攻撃者がノード間の通信を妨害したり、アプリケーションから送信されたトランザクションを読み取ったりしないように防止できます。 TLS に使用される鍵と証明書は、トランザクションの署名や検証に使用される証明書とは異なり、別個の認証局によって発行されます。

{{site.data.keyword.blockchainfull_notm}} Platform コンソールで作成される各 CA には、ルート CA と TLS CA が含まれます。 これら両方の CA は、コンソールのノードのタブの同じ表示名の下に表示されます。 CA の概要パネルをクリックしてから、**「TLS 認証局 (TLS Certificate Authority)」**をクリックして TLS CA を操作します。 TLS CA の[登録](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)および[エンロール](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll)の処理は、ルート CA と同じです。 両方の CA は、同じ CA 管理者 ID と機密事項を使用してデプロイされます。

デプロイする各ピアまたは順序付けプログラム・ノードでは、公開 TLS 証明書が生成される必要があります。 コンソールを使用してノードを作成する場合、TLS CA で登録されたアイデンティティーの登録 ID と機密事項を指定するように要求されます。 [ネットワーク構築のチュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)では、便宜上、CA 管理者アイデンティティーを使用します。 ただし、ノードごとに TLS CA で固有のアイデンティティーを登録することがベスト・プラクティスです。 ノードでは、デプロイメント時にこのアイデンティティーを使用して TLS 証明書が生成されます。 この証明書は、順序付けプログラムまたはピアと通信する必要があるアプリケーションには必須です。 ノードの概要パネルにナビゲートして「設定」をクリックすることによって、ノードの TLS 証明書を表示できます。 また、[スマート・コントラクトのタブ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK)からダウンロードできる接続プロファイルで、ピアおよび順序付けプログラムの TLS 証明書を表示することもできます。

コンソールを使用してピアまたは順序付けプログラムを作成する場合、TLS CA を使用して、ノードごとに追加のドメイン・ネームも指定できます。 順序付けプログラムまたはピアのデプロイ時に、**「TLS CSR ホスト名 (TLS CSR hostname)」**フィールドに新規ドメイン・ネームを入力します。 このホスト名は、ノードに発行される TLS 証明書の共通名リストに追加されます。


## 証明書の有効期限
{: #ibp-console-identities-expiration}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 CA を使用して生成した証明書は、1 年後に有効期限が切れます。 有効期限の期間は、Fabric SDK、Fabric CA クライアント、またはコンソールを使用して生成される証明書で同じです。 証明書の有効期限が切れると、アプリケーションはネットワークと対話できなくなり、新規証明書を生成するために再度エンロールする必要があります。 ユーザーのエンロール制限に達している場合は、新規ユーザーを登録してエンロールすることができます。

コマンド・ラインを使用して証明書の有効期限を確認できます。 まず、ローカル・マシンで以下のコマンドを実行して、base64 形式の証明書を PEM 形式に変換する必要があります。

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

以下のコマンドを実行して、PEM エンコードされた証明書を人間が読める形式で表示します。
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

証明書は、以下の例のようになります。

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

期限日付は、**Validity** セクションの `Not After:` に続いて表示されています。この例では、証明書は 2019 年 11 月 28 日に有効期限が切れます。

## アイデンティティーのコンソール・ウォレットへの保管
{: #ibp-console-identities-wallet}

ウォレットには、{{site.data.keyword.blockchainfull_notm}} Platform コンソールでネットワークのノードを操作するために使用するアイデンティティーと鍵が保管されます。 コンソールを使用してチャネルおよびスマート・コントラクトで作業する前に、このウォレットにピア、順序付けプログラム、および組織管理者を追加する必要があります。 また、ウォレットを使用して、アプリケーションで使用するアイデンティティーを保管できます。 それらは、ウォレットを使用していつでもエクスポートできます。 ウォレットの概要パネルを表示するには、左側のナビゲーションを使用します。 概要画面を使用して、このウォレットでアイデンティティーを追加、更新、およびエクスポートできます。

ウォレットは、コンソールのコンポーネントであり、別個のサービスではありません。 ローカル・ファイル・システムの代わりに、コンソールへのアクセスに使用するブラウザーのローカル・ストレージに鍵を保管します。 別のブラウザーからコンソールにアクセスする場合、またはプライベート表示セッションを開始する場合は、ウォレットに保管したアイデンティティーにアクセスできません。 **管理者アイデンティティーをコンソールからエクスポートして、安全な場所に保管することをお勧めします**。
{:important}

### ID の追加
{: #ibp-console-identities-add}

[組織の MSP を作成する](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)場合、ウォレットに管理者アイデンティティーを追加できます。 また、コンソールで管理される CA は、[エンロール処理](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll)時に、ウォレットにアイデンティティーを追加できます。

概要画面の**「アイデンティティーの追加 (Add identity)」**ボタンを使用して、ウォレットにアイデンティティーを直接インポートできます。 ボタンをクリックするとサイド・パネルが開き、そこでアイデンティティーの公開鍵と秘密鍵のペアを追加できます。
- **名前:** 参照のためだけに使用されるアイデンティティーの名前を入力します。
- **証明書:** CA を使用して生成したアイデンティティーの公開鍵 (base64 形式または PEM 形式) が含まれているファイルをアップロードします。
- **秘密鍵:** CA を使用して生成したアイデンティティーの秘密鍵 (base64 形式または PEM 形式) が含まれているファイルをアップロードします。


以下の形式の JSON ファイルをアップロードして、アイデンティティーを追加することもできます。 また、**「JSON のアップロード (Upload JSON)」**ボタンを使用して、複数のアイデンティティーを 1 回でアップロードできます。

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Fabric CA クライアントまたは Fabric SDK を使用してアイデンティティーをエンロールした場合、鍵を PEM 形式から base64 形式に変換する必要があります。 ローカル・マシンで以下のコマンドを実行することによって、証明書を base64 形式に変換できます。
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

### アイデンティティーの表示と更新
{: #ibp-console-identities-update-identities}

**「ウォレット (Wallet)」**タブでタイルをクリックして、ウォレットのアイデンティティーを表示したり、更新したり、削除したりします。 証明書の有効期限が切れた場合、アイデンティティーの更新が必要なことがあり、CA から新規鍵を発行してもらう必要があります。 また、このタブを使用して、コンソールやローカル・システムから鍵を削除することもできます。

アイデンティティーをクリックすると、サイド・パネルが開き、公開鍵と秘密鍵が base64 形式で表示されます。 **「エクスポート」**をクリックして、アイデンティティーの証明書をローカル・ファイル・システムにダウンロードします。 **「更新」**をクリックして、ウォレットのアイデンティティーの名前を変更したり、パネルに鍵の新規セットを貼り付けたりします。 このアイデンティティーを使用する必要がなくなり、鍵を削除する場合は、**「削除」**をクリックします。

## アイデンティティーの関連付け
{: #ibp-console-identities-associate-admin}

組織およびノード管理者の公開鍵を[組織の MSP 定義](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)に提供する必要があります。 コンソールで作成されたノードまたはチャネルでは、MSP 定義からの証明書を使用して、誰が有効な管理者であるかが検査されます。 結果として、MSP 定義に管理者証明書を追加するために使用したのと同じ公開鍵と秘密鍵のペアが、コンソール・ウォレット内に保管されている必要があります。

コンソールを使用して順序付けプログラムまたはピアを作成すると、**「ID の関連付け (Associate identity)」**パネルが表示されます。 証明書が組織の MSP 定義内部にもある、ウォレット内のアイデンティティーを選択します。 また、チャネルを作成または編集する場合に、**「ID の関連付け (Associate identity)」**セクションで管理者アイデンティティーを選択する必要があります。 これによって、ピア、順序付けプログラム、および順序付けサービス共同事業体と通信する場合に使用するアイデンティティーをコンソールで識別できます。

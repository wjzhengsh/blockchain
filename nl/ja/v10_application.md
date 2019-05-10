---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Fabric SDK を使用したアプリケーションの開発
{: #dev-app}

{{site.data.keyword.blockchainfull}} Platform には、アプリケーションをブロックチェーン・ネットワークに接続するために使用できる API が用意されています。 接続プロファイル内にあるネットワーク API エンドポイントを使用して、チェーンコードを起動し、ピアにあるチャネル固有の台帳を更新または照会することができます。 また、[Swagger UI](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) の API を使用して、ネットワークのノード、チャネル、およびメンバーを管理することもできます。
{:shortdesc}

このチュートリアルを使用して、{{site.data.keyword.blockchainfull_notm}} Platform API にアクセスし、それらを使用してアプリケーションをネットワークにエンロールおよび登録する方法を学習できます。 また、ネットワークと対話する方法や、アプリケーションからトランザクションを発行する方法を学習することもできます。 このチュートリアルは、Hyperledger Fabric の資料の [Writing Your First Application ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "writing your first application"){:new_window} チュートリアルに基づいています。 使用するファイルとコマンドの多くは **Writing Your First Application** チュートリアルのものと同じですが、それらを {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークと対話するために使用します。 このチュートリアルでは、Hyperledger Fabric Node SDK を使用してアプリケーションを開発する手順について 1 つずつ説明します。 また、SDK を使用する代わりに Fabric CA クライアントを使用してユーザーのエンロールおよび登録を行う方法についても学習します。

独自のビジネス・ソリューションを作成するときには、このチュートリアルに加えて、{{site.data.keyword.blockchainfull_notm}} Platform に用意されているサンプルのアプリケーションとチェーンコードをテンプレートとして使用できます。 詳しくは、[サンプル・アプリケーションのデプロイ](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications)を参照してください。

アプリケーションを拡大する準備が整ったら、[アプリケーション開発のベスト・プラクティス](/docs/services/blockchain/best_practices.html#best-practices-app)にアクセスしてください。

## 前提条件
{: #dev-app-prerequisites}

**Writing Your First Application** チュートリアルを {{site.data.keyword.blockchainfull_notm}} Platform で使用するには、その前に以下の前提条件を満たす必要があります。

- {{site.data.keyword.cloud_notm}} 上にブロックチェーン・ネットワークがない場合は、スターターまたはエンタープライズのメンバーシップ・プランを使用して作成する必要があります。 詳しくは、[スターター・プラン・ネットワークの作成](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network)または [エンタープライズ・プラン・ネットワークの作成](/docs/services/blockchain/get_start.html) を参照してください。

  使用するネットワークのネットワーク・モニターを開き、「概説」画面で組織用のピアを 1 つ以上追加します。 そして、ネットワーク内にチャネルを 1 つ以上作成します。 詳しくは、[チャネルの作成](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel)を参照してください。 スターター・プラン・ネットワークを使用している場合は、チェーンコードをデプロイするために使用できる `defaultchannel` という名前のチャネルがネットワークに既にあることに**注意してください**。

- Hyperledger Fabric のサンプルをダウンロードするため、および Node SDK を使用するために必要なツールをインストールします。
  * [Curl ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") または [Git ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

- `fabric-samples` ディレクトリーをダウンロードして Hyperledger Fabric のサンプルをインストールします。 Hyperledger Fabric の資料の [Getting Started ガイド ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/install.html "Getting Started ガイド"){:new_window} に従うことができます。

- ローカル・マシン上の `fabric-samples` ディレクトリーにナビゲートします。
  * `git checkout` コマンドを使用して、ネットワークの Hyperledger Fabric バージョンに一致するブランチを使用します。 Fabric のバージョンは、ネットワーク・モニターで[「ネットワーク設定 (Network preferences)」ウィンドウ](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)を開くことで確認できます。
    - ネットワークが Fabric バージョン 1.2 上にある場合は、メイン・ブランチを使用できます。
    - ネットワークが Fabric バージョン 1.1 上にある場合は、`git checkout v1.1.0` を実行します。
    - ネットワークが Fabric バージョン 1.0 上にある場合は、`git checkout v1.0.6` を実行します。

  * `fabric-samples/fabcar` にナビゲートします。 このディレクトリーのコピーを作成して名前を変更することで、サンプル・アプリケーションをクリーンなディレクトリーで試用し、テストすることができます。

  * `fabcar` ディレクトリーの中で、`npm install` コマンドを実行して Fabric SDK を使用するために必要なパッケージ (`fabric-client` や `fabric-ca-client` など) をインストールします。

- [ネットワーク・モニター](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc)を使用して、チャネルに fabcar チェーンコードをインストールし、インスタンス化します。 fabcar チェーンコードは、`fabric-samples` フォルダー内の、`fabric-samples > chaincode > fabcar > go` の下にあります。

- ネットワーク・モニターの「概説」画面で接続プロファイルを取得します。 接続プロファイルを `fabcar` ディレクトリーに保存し、名前を `creds.json` に変更します。

## Fabric SDK の使用
{: #dev-app-fabric-sdks}

Hyperledger Fabric SDK には、アプリケーションでブロックチェーン・ネットワークと対話するための強力な API セットが用意されています。 サポートされる言語の最新リストは、[Hyperledger Fabric SDK Community の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community の資料"){:new_window} にあります。 {{site.data.keyword.blockchainfull_notm}} Platform では、Node SDK または Java SDK を使用することをお勧めします。 各 SDK に用意されている API について詳しくは、SDK 別のリポジトリーを参照してください。

このチュートリアルでは、[Node SDK ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Node SDK"){:new_window} を使用してアプリケーションを登録およびエンロールし、そのアプリケーションでチェーンコードを起動および照会してトランザクションを発行します。 このチュートリアルでは、アプリケーションをブロックチェーン・ネットワークに接続するために SDK に渡す必要がある情報について説明します。 また、使用できる API や、SDK でブロックチェーン・ネットワークと対話してトランザクションを送信する方法についても紹介します。

## アプリケーションへのネットワーク API エンドポイントの追加
{: #dev-app-api-endpoints}

アプリケーションには、{{site.data.keyword.cloud_notm}} のブロックチェーン・ネットワークに含まれている特定のネットワーク・リソースの API エンドポイントを渡す必要があります。例えば、順序付けサービス、CA、ピア・ノードなどのリソースがあります。 これらの API エンドポイントを使用することで、アプリケーションはネットワークと対話できます。 API エンドポイントの情報は、ネットワークの接続プロファイルにあります。 接続プロファイルは JSON 形式で記述されており、ネットワーク・リソースの API エンドポイント情報や登録 ID/機密事項が含まれています。

1. 以下のいずれかの方式で、ネットワーク・モニターからネットワーク・リソースの API エンドポイント情報を取得します。
  * 「概説」画面で、**「接続プロファイル」**をクリックします。 接続プロファイルには、すべてのネットワーク・リソースの API エンドポイント情報が一式含まれています。
    ![ネットワーク・モニターの「接続プロファイル」](images/service_credentials.png "ネットワーク・モニターの「接続プロファイル」")

  * ネットワーク内で実行中のチェーンコードがある場合は、そのチェーンコードに固有の API エンドポイント情報を取得できます。 「チャネル」画面で、チェーンコードを実行しているチャネルの行をクリックしてチャネル別の画面を開きます。 そして、そのチェーンコードを見つけて、**「JSON」**ボタンをクリックします。
	![チェーンコード別の API エンドポイント](images/channel_chaincode.png "チェーンコード別の API エンドポイント")

2. ネットワーク・リソースの API エンドポイント情報を見つけます。これは、次の例にある `peer1-org1` 行の URL のような情報です。
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```

自分の組織の外部にあるネットワーク・リソースをアプリケーションのターゲットにすることもできます。 例えば、チェーンコードの[エンドースメント・ポリシー](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-endorsement-policy)が、チャネルの他の組織からの承認を必須としている場合は、ポリシーに準拠するために、各組織の十分な数のピアにトランザクションを送信する必要があります。 Hyperledger Fabric の[サービス・ディスカバリー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Service discovery") は、スターター・プランでもエンタープライズ・プランでもサポートされていません。 接続プロファイルの「ピア」セクションを使用して、他の組織のピアのエンドポイント情報とそれに付随する TLS 証明書を入手する必要があります。 どのピアがどのチャネルに参加しているかについて、他の組織の管理者に問い合わせることができます。{:note}

3. 次の例のように、API エンドポイント情報をアプリケーションの構成ファイルにプラグインします。
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```

  また、それらのエンドポイントに [HEAD 要求](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-nodes)を送信して、ネットワーク・リソースが使用可能かどうかを調べることもできます。

  Fabric SDK を使用する場合は、接続プロファイルを使用してネットワークに接続することもできます。 このチュートリアルでは、手動でネットワークのエンドポイント情報を SDK に渡します。 ただし、後のセクションに、[接続プロファイルを SDK で使用する](/docs/services/blockchain/v10_application.html#dev-app-connection-profile)ためのチュートリアルとガイドを記載しています。

## アプリケーションのエンロール
{: #dev-app-enroll}

アプリケーションを {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークに接続する前に、ネットワークに対してアプリケーションの真正性を証明する必要があります。 x509 証明書と Public Key Infrastructure の詳細についてはここでは扱いません。詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理](/docs/services/blockchain/certificates.html#managing-certificates)チュートリアルを参照してください。 簡単に説明すると、Fabric の通信フローではタッチポイントごとに署名/検証操作が使用されます。 そのため、台帳の照会や更新などの呼び出しをネットワークに送信するアプリケーションは、秘密鍵を使用してペイロードに署名し、正しい署名のある x509 証明書を検証のために付加する必要があります。 **エンロール**とは、必要な鍵と証明書を適切な認証局から生成するプロセスです。 エンロールの後に、アプリケーションはネットワークと通信できるようになります。

このセクションでは、**Writing Your First Application** チュートリアルの一部であるサンプル・コードを使用して、Fabric Node SDK で鍵と証明書を取得する方法を説明します。 証明書を生成するには、認証局に登録されている ID を使用する必要があります。 以下のチュートリアルでは、まず CA に既に登録されている管理者 ID を使用してエンロールします。 次に、それらの証明書を使用して、新しいクライアント ID を登録します。 このチュートリアルでは、新しい ID を使用して再度エンロールし、それらの証明書を使用してネットワークにトランザクションを送信します。<!---You can find an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

ネットワーク・モニターの「認証局」画面を使用して証明書を生成し、それらの証明書を使用してネットワークと対話することもできます。 その方法については、[ネットワーク・モニターを使用した証明書の生成](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel)を参照してください。 [証明書の管理](/docs/services/blockchain/certificates.html#managing-certificates)チュートリアルでは、コマンド・ラインから [Fabric CA クライアント](/docs/services/blockchain/certificates.html#managing-certificates-enroll-register-caclient)を使用して証明書を生成し、ユーザーを登録する方法についても説明します。

### Fabric SDK を使用したエンロール
{: #dev-app-enroll-sdk}

`fabric-samples` フォルダーの `fabcar` ディレクトリーにある `enrollAdmin.js` ファイルをテキスト・エディターで開きます。

1. このファイルは最初に Fabric クライアントのインスタンスを作成します。
  ```
  var fabric_client = new Fabric_Client();
  ```
  {:codeblock}

2. その後、このファイルは、証明書を管理するためのキー値ストア (KVS) を作成します。 SDK は [KeyValueStore ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} クラスを使用してキー値ストアを作成し、[CryptoSuite ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} クラスを使用して暗号計算を実行します。 該当するコード・ブロックを以下に示します。
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. KVS を定義したら、[Fabric クライアント ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html "Fabric クライアント"){:new_window} クラスおよび Fabric-CA-Client API <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> のいくつかのメソッドを使用して CA サーバーと通信できるようになります。 SDK に認証局の名前と URL を渡す必要があります。 ネットワーク・モニターの**「概説」**画面から**「接続プロファイル」**の JSON ファイルを開き、`certificateAuthorites` セクションの下で以下の変数を見つけます。
  * CA の URL: ``certificateAuthorities`` の下の ``url``
  * 管理者のユーザー ID: ``enrollId``
  * 管理者のパスワード: ``enrollSecret``
  * CA 名: `caName`

  この情報を使用して、`enrollAdmin.js` ファイル内の該当行を以下のように**編集**します。
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  例えば、次のようにします。
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```

  その後、Fabric クライアントでアプリケーションが既にエンロールされているかどうかを確認します。 接続プロファイルの `enrollID` を使用して、以下の行を**編集**します。
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. 「エンロール」呼び出しを CA サーバーに実際に送信する必要があります。 `admin` は、ネットワークに既に登録されています。 「エンロール」呼び出しは、x509 証明書としてラップされた形式で、ターゲットの CA の署名付きの秘密鍵および公開鍵を取得します。 このラップされて署名された証明書を、署名付き証明書と呼びます。 この署名付き証明書により、ネットワーク・メンバーはクライアントからの呼び出しを検証できます。 資格情報ファイルにある組織名、パスワード、および MSP ID を指定する必要があります。 ネットワーク資格情報の `certificateAuthorities` セクションで、`enrollID`、`enrollSecret`、および `x-mspid` を見つけます。 それらの値を使用して以下のコード・ブロックを**編集**し、ファイル内の該当するセクションを置き換えます。
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. `enrollAdmin.js` ファイルを保存します。

`fabcar` ディレクトリーで、以下のコマンドを実行して管理者をエンロールします。
```
node enrollAdmin.js
```
{:codeblock}

エンロール・コマンドは、署名付き証明書を生成し、`hfc-key-store` という名前のフォルダーにその証明書をエクスポートします。 このチュートリアルで後で使用する複数のファイルが、このフォルダーにある証明書を必要とします。 管理者の証明書が `hfc-key-store` フォルダーにあれば、このエンロール・コマンドは成功です。

[SDK を使用してネットワークを操作](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk)する場合は、管理者の署名付き証明書を {{site.data.keyword.blockchainfull_notm}} Platform にアップロードする必要があります。 管理者の署名付き証明書は `hfc-key-store` フォルダー内にあります。 `admin` ファイルを開いて、`certificate` フィールドの後にある引用符で囲まれた証明書をコピーします。 ツールまたはテキスト・エディターを使用して証明書を PEM 形式に変換します。 見つけたら、ネットワーク・モニターから管理者証明書をブロックチェーン・ネットワークにアップロードできます。 証明書の追加に関する詳細情報については、ネットワーク・モニターの[「メンバー」画面の「証明書」タブ](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members)を参照してください。 チェーンコードを起動または照会するためだけに SDK を使用する場合、この操作は必要ありません。

## アプリケーションの登録
{: #dev-app-register}

クライアント・サイド証明書を生成したら、アプリケーションをネットワーク認証局に登録する必要があります。 登録すると、ネットワークが認識できるコンポーネントのリストに、アプリケーションが追加されます。 ベスト・プラクティスは、`admin` を使用して要求に署名するのではなく、別の ID としてアプリケーションを登録することです。

### SDK を使用した登録
{: #dev-app-register-sdk}

`registerUser.js` ファイルで、`admin` の署名付き証明書を使用して、アプリケーションを `user1` として登録およびエンロールできます。 テキスト・エディターで `registerUser.js` を開きます。

1. CA の URL と名前を Fabric CA クライアントの新しいインスタンスに渡します。

2. `admin` がエンロールされていてこの要求の実行を許可されているかどうかを調べるために、[Fabric Client クラス ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html "Fabric Client クラス"){:new_window} の `getUserContext` メソッドに enrollID を渡します。 以下のサンプルに基づいて、ファイル内の該当するコード・ブロックを**編集**します。
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. **Fabric CA クライアント**を使用してユーザーを CA に登録し、エンロールしてから、**Fabric クライアント**を作成して新しい署名付き証明書を作成します。 MSP ID および組織の所属団体名を使用して、以下のブロックを**編集**します。 `x-affiliations` はネットワーク資格情報の認証局セクションにあります。リストされている任意の所属団体名を使用できます。 作成するユーザーの名前を追加します。 fabcar のサンプルでは、デフォルトでは `user1` が使用されます。
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. `registerUser.js` ファイルを保存します。

`node registerUser.js` コマンドを実行して、`user1` をエンロールし、登録します。 `user1` の証明書が `hfc-key-store` フォルダーにあれば、コマンドは成功です。 一度に登録できる ID は 1 つだけです。 問題が発生した場合、新しいユーザー名を使用して `registerUser.js` を実行してみてください。

### ネットワーク・モニターを使用した登録

代わりに、ネットワーク・モニターの**「認証局」**タブを使用して、クライアント・アプリケーションのエンロールおよび登録を行うこともできます。 詳しくは、この[情報](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-ca)を参照してください。

## チェーンコードの起動と照会によるトランザクションの発行
{: #dev-app-invoke-query}

アプリケーションは、トランザクションを送信するために、ブロックチェーン・ネットワーク全体と対話する必要があります。

1. アプリケーションが、チャネル上のピアから承認を受けるためにトランザクション提案を送信します。
2. エンドース・ピアが、承認されたトランザクションをアプリケーションに返します。
3. アプリケーションが、トランザクションを台帳に追加するために、承認されたトランザクションを順序付けサービスに送信します。

トランザクション・フローの全体について詳しくは、Hyperledger Fabric の資料で [トランザクション・フロー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "トランザクション・フロー"){:new_window} を参照してください。 このチュートリアルを開始した後に、[アプリケーションの接続性と可用性](/docs/services/blockchain/best_practices.html#best-practices-app-connectivity-availability)のセクションを参照することにより、SDK がネットワークと対話する方法の管理に関するヒントを学べます。

次のサンプルでは、Node SDK でネットワーク・トポロジーをセットアップし、トランザクション提案を定義し、トランザクションをネットワークに送信する方法を示しています。 `invoke.js` ファイルを使用して、`fabcar` チェーンコード内の関数を呼び出せます。 それらの関数により、ブロックチェーン台帳上に資産を作成および転送できます。 このチュートリアルでは、`initLedger` 関数を使用して新しいデータをチャネルに追加してから、`query.js` ファイルを使用してそのデータを照会します。

### チェーンコードの起動
{: #dev-app-invoke}

テキスト・エディターで `invoke.js` ファイルを開きます。

1. ファイルの先頭に `var creds = require('./creds.json')` を追加します。 このコード行により、`invoke.js` ファイルは `creds.json` 資格情報ファイルの情報を読み取ることができます。

2. [Fabric クライアント ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html "Fabric クライアント"){:new_window} クラスで、ネットワーク・リソースの API エンドポイントを使用して Fabric ネットワークをセットアップします。 この手順では、クライアントが提案を送信するチャネルとピア、および、承認されたトランザクションを SDK が送信する順序付けサービスを定義します。 以下の該当するコード・ブロックを**編集**します。 `creds.peers["org1-peer1"].url` の行は、資格情報ファイルからピア URL をインポートします。
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  新しいピアと順序付けプログラムの変数により、ブロックチェーン・ネットワークへの GRPC 接続がオープンされます。 これらの接続の管理について詳しくは、[ネットワーク接続のオープンとクローズ](/docs/services/blockchain/best_practices.html#best-practices-app-connections)を参照してください。

  ピア URL を `fabric_client.newPeer` メソッドに追加するときには、以下のコード・スニペットを使用して、該当する TLS 証明書も接続プロファイルからインポートします。 これは、順序付けサービス URL を追加したときにも行っています。 これらの TLS 証明書は、ネットワークとの通信を認証するために必要です。
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  エンドースメント・ポリシーでトランザクションにチャネル内の他の組織からの承認が必要であることを規定する場合は、ネットワークをセットアップするときに、`newPeer()` および `channel.addPeer()` メソッドを使用してそれらの組織ピアを追加する必要があります。 各組織が特定のチャネルに参加させているピアのリストを、対象組織から送信してもらう必要があります。 エンドポイント情報と TLS 証明書は接続プロファイル内にあります。 SDK はチャネルに追加されているすべてのピアにトランザクションを送信します。

  [アプリケーションの可用性を高める](/docs/services/blockchain/best_practices.html#best-practices-ha-app)ための手順として、組織に属し、チャネルに参加するピアを追加することもできます。 これにより、SDK は、いずれかのピアに問題が発生した場合にフェイルオーバーを実行できます。

3. Fabric ネットワークをセットアップし、登録手順で生成したアプリケーション ID と署名付き証明書をインポートしたら、`invoke.js` ファイルでネットワークに送信する提案を定義します。 `fabcar` チェーンコード内の `initLedger` 関数を使用して、いくつかの初期データを台帳に追加できます。 また、コード・ブロックを編集して、`fabcar` チェーンコード内にある他の関数を呼び出すこともできます。
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'defaultchannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  要求を定義した後に、[トランザクション提案 ![外部リンク・アイコンxternal link icon](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") をチャネル上のピアに送信できます。 提案がピアから返されたら、順序付けサービスに[トランザクションを送信 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") できます。

4. イベント・サービスを追加して、トランザクション・フローの効率を高めることができます。 以下のセクションを**編集**してください。
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  このサンプルではピア・ベースのイベント・サービスを使用していますが、実際にはチャネル・ベースのリスナーを使用する必要があります。 詳しくは、[トランザクションの管理](/docs/services/blockchain/best_practices.html#best-practices-managing-transactions)のセクション、および [Node SDK の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "チャネル・ベースのイベント・サービス"){:new_window} を参照してください。

5. デフォルトでは、`invoke.js` はトランザクションを `user1` として送信します。 別の名前を登録した場合は、`invoke.js` ファイルを編集できます。

ファイルの編集が完了したら、`node invoke.js` コマンドを実行してトランザクションをネットワークに送信します。 起動が成功したことの確認として、以下が表示されます。
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

これは、アプリケーションが正常にチェーンコードを起動してデータを台帳に追加したことを示しています。

### チェーンコードの照会
{: #dev-app-query}

これで、`query.js` を使用して台帳を読むことができます。 テキスト・エディターで `query.js` ファイルを開いてください。

1. ファイルの先頭に `var creds = require('./creds.json')` を追加します。
2. ピアのチャネル名とエンドポイント情報を使用してファイルを更新します。 この操作ではピアに保管されたデータだけを読み取るので、順序付けサービスのエンドポイント情報を追加する必要はありません。 また、`query.js` では、提案が `user1` として送信されることを想定しています。
  ```
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

ファイルの編集を完了したら、`node query.js` コマンドを実行します。以下の例のような、台帳上の車のリストが表示されるはずです。
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

fabcar アプリケーションとこのアプリケーションが使用する関数について詳しくは、Hyperledger Fabric の資料で、[Writing Your First Application ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "writing your first application"){:new_window} のチュートリアルの全体を参照してください。

## SDK での接続プロファイルの使用
{: #dev-app-connection-profile}

ネットワークのエンドポイント情報を手動でインポートする代わりに、ネットワーク・モニターの**「概説」**画面にある**「接続プロファイル」**を使用して、SDK をネットワークに接続させることもできます。 これにより、エンロールおよび登録のために認証局に接続する処理が簡素化されます。 また、トランザクションを送信する前に Fabric ネットワークを定義する必要もなくなります。 SDK は、接続プロファイルから直接、該当するチャネル上のピアと順序付けプログラムを見つけます。 接続プロファイルの使用方法について詳しくは、[Node SDK の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-network-config.html "接続プロファイルのチュートリアル"){:new_window} を参照してください。

手動のエンドポイントではなく接続プロファイルを使用することの効率の良さを理解するために、`invoke.js` ファイルを例として使用できます。 `loadFromConfig` クラスを使用して、Fabric クライアントの新しいインスタンスを設定できます。 `var fabric_client = new Fabric_Client();` を以下のコードに置き換えてください。
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

ピアと順序付けプログラムを作成し、チャネルに追加して Fabric ネットワークをセットアップする代わりに、次の行を使用して新しいチャネルを作成できます。

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

これにより、SDK が、接続プロファイルを使用してチャネルに定義されているピアと順序付けサービスを追加します。 これを利用すると、アプリケーションの作成効率が高まるため、ネットワーク・メンバーの参加や離脱、新しいチャネルの開始などのためのアプリケーションの更新が簡単になります。 関連する他の手順について詳しくは、Node SDK 資料の[接続プロファイルのチュートリアル ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-network-config.html "接続プロファイルのチュートリアル"){:new_window} を参照してください。 手動のエンドポイント接続ではなく接続プロファイルを使用する、この[fabcar チュートリアルのバージョン ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} を使用できます。

Hyperledger Fabric のエンドースメント・ポリシーの [ サービス・ディスカバリー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Service discovery") は、スターター・プランでもエンタープライズ・プランでもサポートされていません。 ただし、接続プロファイルを編集して、組織外のピアにトランザクションを送信して承認を受けることができます。 接続プロファイルには、{{site.data.keyword.blockchainfull_notm}} Platform ネットワーク上に存在する他の組織のピアのエンドポイント情報と TLS 証明書が既に含まれています。 ピアをチャネルに追加するには、プロファイルの「channels」セクションで、ピアの名前を該当するチャネルに追加します。 どのピアがどのチャネルに参加しているかについて、他の組織の管理者に問い合わせる必要があります。

## ネットワーク・モニターを使用した証明書の生成
{: #dev-app-enroll-panel}

ネットワーク・モニターでは、管理者 ID を使用して証明書を生成し、それらの証明書を SDK に直接渡すことができます。 つまり、SDK を使用して証明書を生成しなくても、ネットワークとの対話を素早く開始できます。

ネットワーク・モニターの「認証局」パネルにナビゲートします。 管理者 ID の横にある**「証明書の生成」**ボタンをクリックして、CA から新しい署名付き証明書と秘密鍵を取得します。 **「証明書」**フィールドには、署名付き証明書が含まれており、**「秘密鍵」**のすぐ上にあります。 各フィールドの端にあるコピー・アイコンをクリックすると、そのフィールドの値をコピーできます。 これらの証明書は、アプリケーションに提供できる場所に保存します。 {{site.data.keyword.blockchainfull_notm}} Platform にはそれらの証明書が保管されないことに**注意してください**。 ユーザーがそれらを安全に保存して保管する必要があります。

Node SDK 内で要求に署名できるユーザー・コンテキストを作成するには、署名付き証明書と秘密鍵で十分です。 Client クラスの [createUser ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html#createUser__anchor "createUser"){:new_window} メソッドを使用して、ユーザー・コンテキスト・オブジェクトを作成します。 `creatUser` メソッド内で、ID 名と mspid を [user ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/global.html#UserOpts "user"){:new_window} オブジェクトに渡し、秘密鍵と署名付き証明書のパスを [cryptoContent ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/global.html#CryptoContent "cryptoContent"){:new_window} オブジェクトに渡します。

例えば、アプリケーション開発チュートリアルの一部として「認証局」パネルと `createUser` クラスを使用できます。 チュートリアルを既に完了している場合は、`fabcar` チェーンコードがインストールおよびインスタンス化され、いくつかのデータが台帳に追加されています。 証明書を使用して、`admin` ユーザーとして台帳を照会できます。 ネットワーク・モニターを使用して証明書をまだ生成していない場合は、上記の手順に従ってください。

秘密鍵を privateKey.pem として、署名付き証明書を certificate.pem として `query.js` と同じディレクトリーに保存します。 テキスト・エディターで `query.js` を開きます。 ファイルの先頭に以下の行を追加します。
```
var fs = require('fs');
```
パーシスタンスからユーザー・コンテキストをインポートする以下の行があります。
```
return fabric_client.getUserContext('user1', true);
```
この行を、署名付き証明書と秘密鍵を使用して新しいユーザー・コンテキストを作成する以下のコードに置き換えます。
```
return fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
上記のスニペットは、証明書を PEM ファイルとして `cryptoContent` クラスに直接読み取ります。 証明書は `admin` ID を使用して生成されたため、ユーザー名は `admin` になります。 mspid は、接続プロファイルの `certificateAuthorites` セクションにあります。 ファイルを保存し、`node query.js` コマンドを実行します。 成功した場合、照会により以前と同じ結果が返されます。

## (オプション) SDK を使用したネットワークの操作
{: #dev-app-operate-sdk}

また、SDK を使用してブロックチェーン・ネットワークを操作することもできます。 このチュートリアルでは、SDK を使用してピアをチャネルに参加させる方法、チェーンコードをピアにインストールする方法、およびチャネル上のチェーンコードをインスタンス化する方法を説明します。 これらは任意の手順です。すべてのピアが {{site.data.keyword.blockchainfull_notm}} Platform で実行されるものである場合は、ネットワーク・モニターや [Swagger UI](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) の API でもこれらの操作を実行できるからです。

これらの手順を実行するためには、管理者の署名付き証明書を {{site.data.keyword.blockchainfull_notm}} Platform にアップロードする必要があります。 署名付き証明書をアップロードする方法について詳しくは、[エンロール・セクション](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk)の最後を参照してください。

### チャネルへの参加
{: #dev-app-join-channel-sdk}

組織がネットワーク・モニターまたは API を使用してチャネルを作成するかチャネルに参加した後に、SDK を使用してピアをチャネルに参加させることができます。

1. 順序付けサービスからチャネルの[ジェネシス・ブロックを取得します ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "ジェネシス・ブロックの取得"){:new_window}。
2. ジェネシス・ブロックを [チャネル参加 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} メソッドに渡して、ピアをチャネルに参加させます。

`fabcar` サンプルを使用してチャネルに参加するには、`invoke.js` ファイルを開始点として使用してください。 この要求はアプリケーションではなく管理者として送信する必要があるので、`getUserContext` メソッドの `user1` は `admin` に置き換えます。 `var request = {` でチェーンコード起動要求を定義した場所から開始して、[ノード SDK の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "相互 tls"){:new_window} にある以下のコード・スニペットに基づき、トランザクション・フローをチャネル参加要求に置き換えます。
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```

ジェネシス・ブロックをフェッチするには、まず、署名付き証明書をチャネルに追加する必要があります。 組織がチャネルに参加した後に証明書を生成した場合は、署名付き証明書をプラットフォームにアップロードしてから、「チャネル」画面で**「証明書の同期」**ボタンをクリックする必要があります。 チャネル同期が完了するまで数分待ってから、チャネル参加コマンドを実行する必要がある場合があります。 詳しくは、[証明書の管理](/docs/services/blockchain/certificates.html#managing-certificates)チュートリアルの[署名付き証明書の {{site.data.keyword.blockchainfull_notm}} Platform へのアップロード](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs)を参照してください。

### チェーンコードのインストール
{: #dev-app-install-cc-sdk}

[Fabric クライアント ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html "Fabric クライアント"){:new_window} クラスの [チェーンコード・インストール ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} メソッドを使用して、チェーンコードをピアにインストールできます。

`fabcar` サンプルを使用して `fabcar` チェーンコードをピアにインストールするには、`query.js` ファイルをベースラインとして使用して編集してください。 この要求はアプリケーションではなく管理者として送信する必要があるので、`getUserContext` メソッドの `user1` は `admin` に置き換えます。 以下の例を使用して、トランザクション提案オブジェクトをチェーンコード・インストール要求に置き換えてください。
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'defaultchannel'
	 };
```
{:codeblock}

このオブジェクトを、現在ファイル内にある `return channel.queryByChaincode(request);` 行ではなく、`return fabric_client.installChaincode(request);` に送信します。

### チェーンコードのインスタンス化
{: #dev-app-instantiate-cc-sdk}

チェーンコードをインスタンス化するには、[インスタンス化の提案 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} をピアに送信してから、[トランザクション要求 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} を順序付けサービスに送信する必要があります。

`fabcar` サンプルを使用してチェーンコードをインスタンス化するには、`invoke.js` ファイルを開始点として使用してください。 この要求はアプリケーションではなく管理者として送信する必要があるので、`getUserContext` メソッドの `user1` は `admin` に置き換えます。 以下の例を使用して、トランザクション提案オブジェクトをチェーンコード・インストール要求に置き換えてください。
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'defaultchannel',
    txId : tx_id
};
```
{:codeblock}

この要求を、現在ファイル内にある `return channel.sendTransactionProposal(request);` ではなく、`return channel.sendInstantiateProposal(request);` に送信します。 インスタンス化要求をチャネルに送信した後に、承認された提案をトランザクションとして順序付けサービスに送信する必要があります。 これにはトランザクションの送信と同じ方式が使用されるので、ファイルの他の部分を変更する必要はありません。 インスタンス化提案で、[タイムアウト値を大きくする](/docs/services/blockchain/best_practices.html#best-practices-set-timeout-in-sdk)ことができます。 そうしないと、プラットフォームがチェーンコード・コンテナーを開始する前に要求がタイムアウトになる可能性があります。

チェーンコードをインスタンス化するには、まず、署名付き証明書をチャネルに追加する必要があります。 チャネルに参加した後に証明書を生成した場合は、署名付き証明書をプラットフォームにアップロードしてから、「チャネル」画面で**「証明書の同期」**ボタンをクリックする必要があります。 チャネル同期が完了するまで数分待ってから、チェーンコードのインスタンス化コマンドを実行する必要がある場合があります。 詳しくは、[証明書の管理](/docs/services/blockchain/certificates.html#managing-certificates)チュートリアルの[署名付き証明書の {{site.data.keyword.blockchainfull_notm}} Platform へのアップロード](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs)を参照してください。

## ネットワークからのアプリケーションの切断
{: #dev-app-disconnect-app}

アプリケーションと {{site.data.keyword.cloud_notm}} 上のブロックチェーン・ネットワークの間の接続を削除するには、以下の手順を実行します。
1. アプリケーション構成ファイルから API エンドポイント情報を削除します。 参照情報として、[アプリケーションへの API エンドポイントの追加](/docs/services/blockchain/v10_application.html#dev-app-api-endpoints)を参照してください。
2. チェーンコード・コンテナーを削除します。
  1. ネットワーク・モニターの「チャネル」画面で、チェーンコードがインストールされているチャネルを見つけます。
  2. 特定のチャネル画面で、無効にしようとしているチェーンコードを見つけます。
  3. **「削除」**ボタンをクリックし、チェーンコード削除パネルで**「送信」**をクリックします。 チェーンコード・コンテナーが削除されます。
	![チェーンコードの削除](images/channel_chaincode.png "チェーンコードの削除")

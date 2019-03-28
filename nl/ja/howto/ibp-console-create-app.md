---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# アプリケーションの作成
{: #ibp-console-app}

スマート・コントラクトをインストールしてノードをデプロイしたら、クライアント・アプリケーションを使用して、ネットワークの他のメンバーと取引を行うことができます。 アプリケーションから、スマート・コントラクトに含まれているビジネス・ロジックを呼び出して、ブロックチェーン台帳上の資産を作成、移転、更新できます。 このチュートリアルでは、クライアント・アプリケーションを使用して、{{site.data.keyword.blockchainfull}} Platform コンソールから管理しているネットワークと対話する方法について学習します。
{:shortdesc}

**対象者:** このトピックは、ブロックチェーン・ネットワークと対話するクライアント・アプリケーションの作成方法について詳しく知りたいと考えるアプリケーション開発者を対象に設計されています。

## 学習用資料
{: #ibp-console-app-learning-resources}

アプリケーションとスマート・コントラクトがどのように連携するかについては、Hyperledger Fabric 資料の[アプリケーションの開発に関するトピック ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "developing applications") で学習できます。 このトピックでは、銀行と企業がコマーシャル・ペーパーを取引するという仮想のユース・ケースを基に、スマート・コントラクトの中にトランザクションをエンコードする方法について説明しています。 [コマーシャル・ペーパーのチュートリアル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") は、このトピックを拡張したものであり、ユーザーが Fabric ネットワークにスマート・コントラクトをデプロイし、サンプル・アプリケーション・コードを使用してコマーシャル・ペーパーを作成したりネットワーク・メンバー間で転送したりできるようになっています。

**アプリケーションのサンプルとチュートリアル**

|  サンプル  |  説明    |  難易度    | 言語 | ロケーション |
| -----------------|---------|---------|---------|
| [FabCar ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://developer.ibm.com/patterns/write-a-smart-contract-for-the-fabcarcommercial-paper-or-iks-cluster-with-saas-v2-beta-network-think/ "Blockchain")| IKS クラスターを使用して、単純な Fabric ネットワーク・スマート・コントラクトを Blockchain Platform 上にデプロイします。| 中級 | Node.js | DeveloperWorks 資料|
| [コマーシャル・ペーパー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Commercial Paper")| コマーシャル・ペーパーのスマート・コントラクトを使用して、Hyperledger Fabric のローカル・インスタンスで取引します。| 中級 | Node.js | Hyperledger Fabric の資料|
| **近日公開:** 上級コマーシャル・ペーパー  | プライベート・データ・コレクションと状態ベースの承認を使用した所有権とプライバシーのベスト・プラクティスがある、上級向けのサンプル。| 上級 | Node.js | Hyperledger Fabric の資料|
| [{{site.data.keyword.blockchainfull_notm}} VSCode を使用したコマーシャル・ペーパーのスマート・コントラクトの実行 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://developer.ibm.com/tutorials/run-commercial-paper-smart-contract-with-ibm-blockchain-vscode-extension/ "Run a commercial paper smart contract with VSCode")|  VScode 拡張機能を使用して、Hyperledger Fabric のローカル・インスタンス上でコマーシャル・ペーパーのスマート・コントラクトを呼び出します。| 中級 | Node.js | IBM Developer|
| [{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版でのコマーシャル・ペーパーのサンプルの実行](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)| {{site.data.keyword.blockchainfull_notm}} Platform 2.0 ネットワーク上でコマーシャル・ペーパーのコントラクトをデプロイして呼び出すことについて学習します。| 中級 |  Node.js | {{site.data.keyword.blockchainfull_notm}} Platform の資料|  

アプリケーションを開発するときには、ネットワーク・オペレーターとアプリケーション開発者というまったく異なる 2 人のネットワーク・ユーザー間の調整が必要になる場合があります。
- **ネットワーク・オペレーター**は、{{site.data.keyword.blockchainfull_notm}} Platform コンソールを使用して組織のノードをデプロイしたり、ネットワークにスマート・コントラクトをインストールしたりする管理者です。
- **アプリケーション開発者**は、エンド・ユーザーが使用するクライアント・アプリケーションを構築します。 開発者は、[Hyperledger Fabric SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK"){:new_window} を使用して、スマート・コントラクトの中に記述されているトランザクションを呼び出します。

アプリケーション開発者がネットワークと対話できるようにするには、**ネットワーク・オペレーター**が以下の手順を実行する必要があります。
1. 組織の CA を使用して、[アプリケーション ID を登録](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)します。
2. スマート・コントラクトのパネルから[接続プロファイルをダウンロード](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)します。
3. 以下のオブジェクトと情報をアプリケーション開発者に送信します。
  - アプリケーション ID の登録 ID とシークレット。
  - 接続プロファイル。
  - スマート・コントラクト名。
  - スマート・コントラクトがインスタンス化されているチャネルの名前。  

**アプリケーション開発者**は、ネットワーク・オペレーターから提供された情報を使用して、以下の手順を実行します。
1. アプリケーション ID の登録 ID およびシークレットと、接続プロファイル内の CA エンドポイント情報を一緒に使用して、公開鍵と秘密鍵のペアを生成します。
2. 接続プロファイル、チャネル名、スマート・コントラクト名、アプリケーション鍵を使用してスマート・コントラクトを呼び出します。  

アプリケーション開発者は、以下の 2 つのプログラミング・モデルを使用してネットワークと対話できます。

**高水準の Fabric SDK API**

Fabric v1.4 以降、ユーザーはシンプルになったアプリケーションとスマート・コントラクトのプログラミング・モデルを利用できるようになりました。 この新しいモデルでは、トランザクションを送信するために必要な工程の数やコード量が少なくなっています。 このモデルは、**Node.js** で作成するアプリケーションでのみサポートされます。 この新しいモデルを利用する場合は、このチュートリアルを使用して、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 ネットワークに対して以下の操作を実行できます。

- SDK を使用して[アプリケーション用の証明書を生成](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll)する。
- [SDK からスマート・コントラクトを呼び出す](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke)。
- [IBM Blockchain VScode 拡張機能](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode)を使用して、スマート・コントラクトを作成してデプロイし、開発とテストを繰り返す。
- コンソールで管理しているノードに[コマーシャル・ペーパーのチュートリアル](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)をデプロイし、アプリケーションの開発について学習する。 このチュートリアルには、Fabric のウォレットとゲートウェイの使用法に関する詳細な背景情報が記載されています。


**低水準の Fabric SDK API**

既存のスマート・コントラクトとアプリケーション・コードを使用し続ける場合や、Hyperledger コミュニティーで提供されているその他の Fabric SDK 言語を使用する場合は、[低水準の Fabric SDK API](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level) を使用してネットワークへ接続します。

## アプリケーション ID の登録
{: #ibp-console-app-identities}

アプリケーションは、{{site.data.keyword.blockchainfull_notm}} ノードに送信するトランザクションに署名し、公開鍵を添付する必要があります。ノードは、この公開鍵を使用して、トランザクションを送信しているのが正当な関係者であることを検証します。 これにより、参加権限を持つ組織がトランザクションを送信していることが確認されます。

ネットワーク・オペレーターは、組織の CA を使用してアプリケーション ID を登録する必要があります。アプリケーション開発者は、その登録された ID を使用して、公開鍵と秘密鍵を生成できます。 オペレーターは、ID の登録 ID およびシークレットを CA エンドポイント情報と一緒に渡すことができます。SDK でこれらを使用して証明書を生成できます。 アプリケーション開発者がクライアント・サイドでエンロールすれば、他の団体は一切そのアプリケーションの秘密鍵にアクセスできません。 セキュリティーを強化するために、ネットワーク・オペレーターは登録時のエンロール制限を 1 に設定できます。アプリケーション開発者がエンロールした後に、同じ登録 ID とシークレットを使用して別の秘密鍵を生成することはできません。

セキュリティーについてあまり心配していない場合は、ネットワーク・オペレーターが [CA タブ](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll)を使用してアプリケーション ID をエンロールしてもかまいません。 その後、オペレーターは ID をダウンロードするか、コンソール・ウォレットにエクスポートすることができます。 SDK の証明書を使用するには、鍵を base64 形式から PEM 形式にデコードする必要があります。 ローカル・マシン上で以下のコマンドを実行すると、証明書をデコードできます。

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

## 接続プロファイルのダウンロード
{: #ibp-console-app-profile}

アプリケーションは、チャネル上でインスタンス化されているスマート・コントラクトだけにトランザクションを送信できます。 そのため、スマート・コントラクトに接続して対話するために必要な情報は、コンソール内のインスタンス化されたスマート・コントラクトのリストにあります。 したがって、スマート・コントラクトが既にインストールされ、インスタンス化されていなければなりません。

Hyperledger Fabric の[トランザクション・フロー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaction Flow"){:new_window} には複数のコンポーネントが関わります。その中でクライアント・アプリケーションは複数のピアから承認を収集し、承認されたトランザクションを順序付けサービスに送信します。 接続プロファイルによって、アプリケーションは、トランザクションを送信するために必要なピアと順序付けノードのエンドポイントを取得します。 このプロファイルには、認証局や MSP ID などのユーザー組織に関する情報も含まれています。 Fabric SDK が接続プロファイルを直接読み取るので、ユーザーがトランザクションと承認のフローを管理するコードを作成する必要はありません。

チャネルの作成時にアンカー・ピアを構成した場合は、Hyperledger Fabric の [サービス・ディスカバリー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery") 機能を利用できます。 サービス・ディスカバリーを使用すると、アプリケーションが、チャネル上の自分の組織外のピアのうちトランザクションを承認する必要があるものを識別できるようになります。 サービス・ディスカバリーを使用しない場合は、他の組織から帯域外の方法でこれらのピアのエンドポイント情報を取得し、接続プロファイルに追加する必要があります。 アンカー・ピアを構成する方法について詳しくは、スマート・コントラクトのデプロイ・チュートリアルの[「プライベート・データ」トピック](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)の 3 つの手順を参照してください。

Platform コンソールでスマート・コントラクトのタブにナビゲートします。 インスタンス化された各スマート・コントラクトの隣にあるオーバーフロー・メニューにナビゲートします。 **「SDK を使用した接続 (Connect with SDK)」**というボタンをクリックします。クリックすると、接続プロファイルを作成してダウンロードできるサイド・パネルが開きます。 まず、アプリケーション ID の登録に使用した組織の CA を選択する必要があります。 また、組織の MSP 定義を選択する必要もあります。 それにより、証明書の生成やスマート・コントラクトの呼び出しに使用できる接続プロファイルをダウンロードできるようになります。

## SDK を使用したエンロール
{: #ibp-console-app-enroll}

ネットワーク・オペレーターからアプリケーション ID の登録 ID およびシークレットとネットワーク接続プロファイルが提供されたら、アプリケーション開発者は Fabric SDK または Fabric CA クライアントを使用してクライアント・サイドの証明書を生成できます。 [Node.js 用の Fabric SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Node.js 用の Fabric SDK") を使用してアプリケーション ID をエンロールするには、以下の手順を使用できます。

1. 接続プロファイルをローカル・マシンに保存し、`connection.json` に名前変更します。
2. 以下のコード・ブロックを `enrollUser.js` として、接続プロファイルと同じディレクトリー内に保存します。

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Create a new CA client for interacting with the CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

      // Create a new file system based wallet for managing identities.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Check to see if we've already enrolled the admin user.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

      // Enroll the admin user, and import the new identity into the wallet.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. `enrollUser.js` を編集して、以下の値を置き換えます。
  - ``<CA_Name>`` を組織の CA の名前に置き換えます。 CA 名は接続プロファイルの「organizations」セクションの「Certificate Authorities」の下にあります。 「Certificate Authorities」セクションの「caName」は使用しないでください。
  - ``<app_enroll_id>`` を、ネットワーク・オペレーターから提供されたアプリケーション登録 ID に置き換えます。
  - ``<app_enroll_secret>`` を、ネットワーク・オペレーターから提供されたアプリケーション登録シークレットに置き換えます。
  - ``<msp_id>`` を組織の MSP ID に置き換えます。 MSP ID は接続プロファイルの「organizations」セクションの下にあります。
4. 端末を使用して `enrollUser.js` にナビゲートし、`node enrollUser.js` を実行します。 このコマンドが正常に実行された場合、以下の出力が表示されます。

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
  SDK は、証明書を作成し、このコマンドで作成される `wallet/user1/` ディレクトリー内に保管します。 このディレクトリーが、将来のトランザクションの送信に使用されるファイル・システム・ウォレットになります。

Fabric SDK で使用されるウォレットは、{{site.data.keyword.blockchainfull_notm}} Platform コンソール内のウォレットとは異なります。コンソールのウォレット内に保管される ID を、直接 SDK で使用することはできません。
{: note}

## SDK を使用したスマート・コントラクトの呼び出し
{: #ibp-console-app-invoke}

アプリケーションの公開鍵と秘密鍵を生成してウォレット内に保管したら、トランザクションを送信するための準備は完了です。 スマート・コントラクトの名前と、スマート・コントラクトがインスタンス化されているチャネルの名前を知っている必要があります。 [Node.js 用の Fabric SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Node.js 用の Fabric SDK") を使用してスマート・コントラクトを呼び出すには、以下の手順を使用します。


1. 以下のファイルを `invoke.js` としてローカル・マシンに保存します。 `enrollUser.js` と同じディレクトリー内に保存してください。

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Parse the connection profile. This would be the path to the file downloaded
        // from the IBM Blockchain Platform operational console.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Configure a wallet. This wallet must already be primed with an identity that
        // the application can use to interact with the peer node.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Create a new gateway, and connect to the gateway peer node(s). The identity
        // specified must already exist in the specified wallet.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {enabled: true, asLocalhost:false }});

        // Get the network channel that the smart contract is deployed to.
        const network = await gateway.getNetwork('<channel_name>');

        // Get the smart contract from the network channel.
        const contract = network.getContract('<smart_contract_name>');

        // Submit the 'createCar' transaction to the smart contract, and wait for it
        // to be committed to the ledger.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. `invoke.js` を編集して、以下の値を置き換えます。
  - ``<channel_name>`` を、スマート・コントラクトがインスタンス化されたチャネルの名前に置き換えます。 CA 名は接続プロファイルの「Certificate Authorities」セクションの下にあります。
  - ``<smart_contract_name>`` を、インストール済みのスマート・コントラクトの名前に置き換えます。 この値は、ネットワーク・オペレーターから入手できます。
  - `submitTransaction` のコンテンツを編集して、スマート・コントラクト内の関数を呼び出します。 `invoke.js` ファイルは、[fabcar スマート・コントラクト ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar) を呼び出すために作成されています。 以下のファイルを実行してトランザクションを送信するには、fabcar をインストールし、いずれかのチャネルでそのスマート・コントラクトをインスタンス化してください。

3. 端末を使用して `invoke.js` にナビゲートし、`node invoke.js` を実行します。 このコマンドが正常に実行された場合、以下の出力が表示されます。

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  コンソールを使用してチャネルにナビゲートすると、トランザクションによって別のブロックが追加されていることを確認できます。


## {{site.data.keyword.blockchainfull_notm}} VScode 拡張機能を使用した接続
{: #ibp-console-app-vscode}

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 拡張機能は、スマート・コントラクト・パッケージを開発、パッケージ化、およびデプロイするための、Visual Studio Code 内の環境を提供します。 接続プロファイルと、CA を使用して生成した一連の鍵ファイルがあることを確認してください。 それから、VScode 拡張機能を使用して、コンソールで管理しているネットワークに接続できます。

Visual Studio Code のマーケットプレイスで説明に従って [VScode 拡張機能 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode 拡張機能") をダウンロードします。 拡張機能をインストールしたら、VScode を開くと**「表示」>「コマンド パレット」**をクリックして拡張機能にアクセスできます。 *「IBM Blockchain Platform: スマート・コントラクト・プロジェクトの作成 (IBM Blockchain Platform: Create Smart Contract Project)」*コマンドを入力し、新しいスマート・コントラクト・プロジェクトを作成します。

新しいプロジェクトを作成したら、コンソールでインスタンス化したスマート・コントラクトの情報を使用して、Visual Studio Code から直接ネットワークに接続できます。 スマート・コントラクトのタブの**「インスタンス化されたスマート・コントラクト (Instantiated smart contracts)」**テーブルを使用して、[接続プロファイル](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)をローカル・ファイル・システムにダウンロードします。 それから、[アプリケーション ID を作成し](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)、CA を使用して証明書 (公開鍵) と秘密鍵を作成してダウンロードします。 前述の手順に従って、秘密鍵と証明書を PEM 形式に変換します。 接続プロファイルと鍵をダウンロードしたら、以下の手順を使用してネットワークに接続します。

1. Visual Studio Code で_「{{site.data.keyword.blockchainfull_notm}} Platform」_タブを開きます。
2. _「{{site.data.keyword.blockchainfull_notm}} Platform」_ペインで、**「Add new connection」**をクリックします。
3. 接続の名前を入力します。 この名前は、_「{{site.data.keyword.blockchainfull_notm}} Platform」_ペインに表示されます。
4. 接続プロファイルの完全修飾ファイル・パスを入力します。
5. PEM 形式の証明書 (公開鍵) の完全修飾ファイル・パスを入力します。
6. PEM 形式の秘密鍵の完全修飾ファイル・パスを入力します。
7. この時点で、接続が `local_fabric` の下の接続リストに表示されているはずです。 接続する接続名をダブルクリックします。

VScode から接続した場合は、自分の組織のピアと、ブロックチェーン接続ペインでピアが参加したチャネルのリストが表示されます。 各ピアの下に、インストール済みのスマート・コントラクトのリストが表示されます。 プロジェクトからネットワークに新しいスマート・コントラクトをインストールするには、まずピアを右クリックして**「スマート・コントラクトのインストール (Install smart contract)」**を選択します。 その後に、チャネルを右クリックして**「スマート・コントラクトのインストール (Install smart contract)」**を選択することで、スマート・コントラクトをインスタンス化できます。 この {{site.data.keyword.blockchainfull_notm}} Platform 拡張機能の使用方法を詳しく学習したい場合は、[Visual Studio Code のマーケットプレイス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode 拡張機能") 内の資料を利用してください。


## コマーシャル・ペーパーのサンプルの実行
{: #ibp-console-app-commercial-paper}

Hyperledger Fabric 資料内の[コマーシャル・ペーパー・チュートリアル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") では、複数の団体がコマーシャル・ペーパーの売買や換金を行うというユース・ケースを開発者用に説明しています。 これは [ アプリケーションの開発に関するトピック ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "developing applications") を拡張したものであり、ユーザーが Fabric のローカル・インスタンスで資産を作成して取引できるようにするサンプルのスマート・コントラクトとアプリケーション・コードが提供されています。

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 ネットワークにコマーシャル・ペーパーのチュートリアルのサンプル・コードをデプロイすることもできます。 そのため、すぐにネットワークとの対話を始められます。サンプルを使用して必要な依存関係をダウンロードすることもできます。 また、サンプル・コードには、証明書を [ウォレット ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") 内にインポートしたり、接続プロファイルを使用して [Fabric ゲートウェイ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway") を作成したりする方法の例も含まれています。 2 つの異なる組織でこのチュートリアルを使用すれば、単一の資産を使用した別々のトランザクションを実行できます。 [ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)を使用して、1 つのチャネルに接続する 2 つのピア組織を既にデプロイしている場合は、両方の組織でこのチュートリアルを利用できます。

ネットワークにサンプルをデプロイするには、以下の手順に従います。 スマート・コントラクトとアプリケーション構造の詳細については、Fabric 資料の [コマーシャル・ペーパー・チュートリアル ![外部リンク・アイコン ](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") でチュートリアルを確認してください。

### 前提条件

コマーシャル・ペーパーのサンプルをデプロイするためには、ローカル・マシン上に以下の必要なツールをインストールしておく必要があります。
  * [Git ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

テキスト・エディターを使用して、サンプル内のファイルを編集して保存する必要もあります。 [Atom ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://atom.io/ "atom")、[Sublime Text ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](http://www.sublimetext.com/ "Git")、[Brackets ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](http://brackets.io/ "Brackets") など、多数の高品質の無料エディターを使用できます。

### 手順 1: サンプルをダウンロードする

[Fabric サンプルのリポジトリー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric-samples "Fabric サンプルのリポジトリー") を複製して、コマーシャル・ペーパーのサンプルをダウンロードします。

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

Fabric サンプルをダウンロードしたら、以下のコマンドを実行して、Fabric v1.4 と互換性のあるバージョンのサンプルを使用していることを確認します。

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

サンプルは、`fabric-samples` の `commercial paper` フォルダーに含まれています。

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

チュートリアルには **magnetocorp** と **digibank** という 2 つのサンプルの組織が含まれています。 組織ごとに固有のサンプル・アプリケーション・コードがあり、`organization` ディレクトリーの下の 2 つのフォルダーに別々に格納されています。

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

このチュートリアルでは、最初に magnetocorp アプリケーション・コードを使用してコマーシャル・ペーパーを発行します。 次に、digibank アプリケーション・コードを使用してペーパーを購入して換金します。 両方のディレクトリーに同じスマート・コントラクトが含まれています。

magnetocorp ディレクトリー内のアプリケーション・コードにナビゲートします。

```
cd magnetocorp/application
```
{:codeblock}

このディレクトリー内から、以下のコマンドを実行してアプリケーションの依存関係をダウンロードできます。

```
npm install
```
{:codeblock}

### 手順 2: スマート・コントラクトをインストールしてインスタンス化する

コマーシャル・ペーパーのスマート・コントラクトは、`digibank` ディレクトリーと `magnetocorp` ディレクトリーの `contract` フォルダー内にあります。 チュートリアルを使用する組織のすべてのピアに、このスマート・コントラクトをインストールする必要があります。 その後に、チャネルでコマーシャル・ペーパーのコントラクトをインスタンス化する必要があります。 コンソールを使用してインストールするには、スマート・コントラクトを [.cds 形式 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "packaging smart contracts") でパッケージ化する必要があります。

[IBM Blockchain VScode 拡張機能](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode)を使用してスマート・コントラクトをパッケージ化できます。 この拡張機能のインストール後に、Visual Studio Code を使用してワークスペース内で `contracts` フォルダーを開きます。 _「{{site.data.keyword.blockchainfull_notm}} Platform」_タブを開きます。 _「{{site.data.keyword.blockchainfull_notm}} Platform」_ペインで、スマート・コントラクト・パッケージのセクションにナビゲートし、**「スマート・コントラクト・プロジェクトのパッケージ化 (Package a Smart Contract Project)」**をクリックします。 VScode 拡張機能は `contracts` フォルダー内のファイルを使用して、`papernet-js@.0.0.1.cds` という名前の新しいパッケージを作成します。 このパッケージを右クリックして、ローカル・ファイル・システムにエクスポートします。 そして、コンソールを使用して [ピアにスマート・コントラクトをインストール](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install)してから、[チャネルでスマート・コントラクトをインスタンス化](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate)できます。

### 手順 3: ウォレットの証明書を生成する

アプリケーションは、Fabric コンポーネントに送信する要求に署名する必要があります。 トランザクションを送信した組織がコンポーネントに認識されないと、トランザクションは拒否され、エラーが戻されます。 コマーシャル・ペーパーのサンプルでは、ファイル・システム・ウォレットが作成されます。このウォレットによって証明書が保管され、トランザクションに署名が付けられます。 アプリケーションでどのようにウォレットが使用されるかについて詳しくは、Fabric 資料内の [ウォレット ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") のトピックを参照してください。 Fabric SDK で使用されるウォレットは、{{site.data.keyword.blockchainfull_notm}} Platform コンソール内のウォレットとは異なります。コンソールのウォレット内に保管される ID を、直接 SDK で使用することはできません。

オリジナルのサンプルでは、`addToWallet.js` ファイルで、Fabric サンプルのフォルダーにある証明書を使用してファイル・システム・ウォレットを作成しています。 ここでは、新たにファイルを作成し、SDK を使用してクライアント・サイドの証明書を生成して新しいウォレット内に直接格納します。

magnetocorp としてチュートリアルを進めるために使用する組織の CA を選択します。 例えば、ネットワーク構築チュートリアルを既に実行した場合は、Org1 を使用すると良いでしょう。 CA を使用して、[アプリケーション ID を作成](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)します。 登録 ID とシークレットを**保存**します。

コンソールを使用して、[接続プロファイルをダウンロード](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)します。 接続プロファイルをローカル・ファイル・システムに保存し、`connection.json` に名前変更します。 そして、以下のコマンドを使用して、将来のコマンドで参照するディレクトリーに接続プロファイルを移動します。

  ```
  mv $HOME/<path_to_creds>/connection.json /gateway/connection.json
  ```
  {:codeblock}

`/magnetocorp/application` ディレクトリーにナビゲートし、以下のコード・ブロックを `enrollUser.js` として保存します。

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Create a new CA client for interacting with the CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Create a new file system based wallet for managing identities.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Check to see if we've already enrolled the admin user.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Enroll the admin user, and import the new identity into the wallet.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

編集する前に、少し時間を取ってこのファイルの動作を確認しましょう。 まず、`enrollUser.js` は `FileSystemWallet` クラスと `X509WalletMixin` クラスを `fabric-network` ライブラリーからインポートします。

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

次に、このファイルは `FileSystemWallet` クラスを使用して、ローカル・ファイル・システム上にウォレットを作成します。 以下の行を編集すれば、別の場所にウォレットを保管できます。

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

ウォレットを作成した後のコード・スニペットは、登録 ID とシークレットを使用して、組織 CA でエンロールしています。 その次に、公開鍵/秘密鍵のペアの ID を作成し、ウォレットにインポートしています。 このファイルで組織の MSP ID をウォレットに渡していることにも注目してください。

```
// Enroll the admin user, and import the new identity into the wallet.
const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

`enrollUser.js` を**編集**して、以下の値を置き換えます。
- `'<CA_Name>'` を組織の CA の名前に置き換えます。 CA 名は接続プロファイルの「organizations」セクションの「Certificate Authorities」の下にあります。 「Certificate Authorities」セクションの「caName」は使用しないでください。
- `'<app_enroll_id>` を、ネットワーク・オペレーターから提供されたアプリケーション登録 ID に置き換えます。
- `'<app_enroll_secret>'` を、ネットワーク・オペレーターから提供されたアプリケーション登録シークレットに置き換えます。
- `'<msp_id>'` を組織の MSP ID に置き換えます。 この MSP ID は接続プロファイルの「organizations」セクションの下にあります。

`enrollUser.js` を保存して閉じます。 `magnetocorp` ディレクトリー内で、以下のコマンドを実行します。
```
node enrollUser.js
```
{:codeblock}

このコマンドが正常に完了すると、以下の出力が表示されます。

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

ウォレットが `magnetocorp` ディレクトリーの `identity` フォルダー内に作成されたことを確認できます。

### 手順 4: 接続プロファイルを使用して Fabric ゲートウェイを作成する

Hyperledger Fabric の[トランザクション・フロー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "トランザクション・フロー"){:new_window} には複数のコンポーネントが関わり、その中でクライアント・アプリケーションはユニークな役割を果たします。 アプリケーションは、トランザクションの承認を行うピアと、トランザクションを順序付けしてブロックに追加する順序付けサービスとに接続する必要があります。これらのノードのエンドポイントをアプリケーションに提供するために、接続プロファイルを使用して Fabric ゲートウェイを構成します。 そうすると、そのゲートウェイが Fabric ネットワークとの低水準の対話を実行します。 詳細については、Fabric 資料内の [Fabric ゲートウェイ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway") のトピックを参照してください。

既に接続プロファイルをダウンロードし、組織の認証局に接続するために使用しています。 これから、この接続プロファイルを使用してゲートウェイを作成します。

テキスト・エディターで `issue.js` を開いてください。 ファイルを編集する前に、fabric-network ライブラリーから `FileSystemWallet` クラスと `Gateway` クラスをインポートしていることに注目してください。

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

`Gateway` クラスは、トランザクションの送信に使用するゲートウェイを構成するために使用します。

```
const gateway = new Gateway()
```
{:codeblock}

`FileSystemWallet` クラスは、前の手順で作成したウォレットをロードするために使用します。 ファイル・システム上のウォレットの場所を変更した場合は、以下の行を**編集**します。

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

ウォレットのインポート後に、以下のコードを使用して、接続プロファイルとウォレットを新しいゲートウェイに渡します。下記のようなコード・スニペットになるように、コードに次のような**編集**を行う必要があります。ログを出力する行は、簡略化のために省略しています。
- `enrollUser.js` の `identityLabel` で選択した値と一致するように、`userName` を更新します。
- ネットワークでサービス・ディスカバリーを利用するには、ディスカバリー・オプションを更新します。 `discovery: { enabled: true, asLocalhost: false }` を設定してください。  
- この接続プロファイルをインポートするセクションを更新します。 コンソールの接続プロファイルは、サンプルで使用される YAML ファイルではなく、JSON 形式です。  

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const ccp = JSON.parse(ccpJSON);

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

このコード・スニペットは、ゲートウェイを使用してピアおよび順序付けプログラム・ノードに対して GRPC 接続を開き、ネットワークと対話します。

### 手順 5: スマート・コントラクトを呼び出す

コンソールで管理しているネットワークに接続するためのゲートウェイを構成したら、`issue.js` ファイル内の、コマーシャル・ペーパー・スマート・コントラクトに接続する部分を編集する必要があります。 コントラクト名と、スマート・コントラクトをインスタンス化したチャネルをゲートウェイに提供する必要があります。

以下の行を**編集**して、`mychannel` をチャネル名に置き換えます。

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

メッセージをコンソールに出力するコード行の後で、スマート・コントラクト名をゲートウェイに渡す行があります。 以下の行を**編集**して、名前 `papercontract` を、インストールしたコントラクトの名前に変更します。

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

これで、トランザクションを送信するために必要なすべての情報がゲートウェイに与えられました。 以下の行で、新しいコマーシャル・ペーパー資産を定義する引数を指定して、コマーシャル・ペーパーのコントラクトの `issue` 関数を呼び出します。

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

ゲートウェイを使用してトランザクションを送信した後に、`issue.js` ファイルはゲートウェイ接続を切断します。

```
gateway.disconnect();
```
{:codeblock}

このコマンドは、ゲートウェイが開いた GRPC 接続を閉じます。 接続を閉じると、ネットワーク・リソースが節約されるので、パフォーマンスが向上します。

この手順と**手順 4** の編集が完了したら、`issue.js` を保存して閉じます。 以下のコマンドを使用して、新しいコマーシャル・ペーパーを作成するトランザクションを送信します。

```
node issue.js
```
{:codeblock}

トランザクションが成功すると、端末に以下の出力が表示されます。

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### 手順 6: digibank としてサンプルを進める

magnetocorp としてコマーシャル・ペーパーを作成したら、digibank としてチュートリアルを進め、コマーシャル・ペーパーを購入して換金することができます。 digibank のアプリケーション・コードを使用するときには、magnetocorp と同じ組織を使用しても、別の組織の CA、ピア、接続プロファイルを使用してもかまいません。 [ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)を完了している場合は、この機会に Org2 としてチュートリアルを進めると良いでしょう。

`digibank/application` ディレクトリーに移動します。 **手順 3** の説明に従って、digibank としてトランザクションに署名するための証明書とウォレットを生成します。 そして、`buy.js` ファイルを使用して magnetocorp からコマーシャル・ペーパーを購入し、`redeem.js` を使用してそのペーパーを換金します。 **手順 4** と**手順 5** に従って、正しい接続プロファイル、チャネル、スマート・コントラクトを指すように各ファイルを編集します。

## 低水準の Fabric SDK API を使用したネットワークへの接続
{: #ibp-console-app-low-level}

既存のアプリケーション・コードを保持したい場合や、Node.js 以外の言語用の Fabric SDK を使用したい場合には、以前と変わらず低水準の Fabric SDK API を使用してネットワークに接続できます。 コンソールを使用して[接続プロファイルをダウンロード](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)します。 そして、チャネルのピアと順序付けノードのエンドポイントを接続プロファイルから直接インポートするか、ノードのエンドポイント情報を使用してピアと順序付けプログラム・オブジェクトを手動で追加します。 CA を使用して[アプリケーション ID を作成](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)してから、CA エンドポイント情報を使用してクライアント・サイドでエンロールするか、コンソールを使用して証明書を生成する必要もあります。

[Fabric Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io "Fabric Node SDK") の資料には、[接続プロファイルを使用してネットワークに接続する ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-network-config.html "接続プロファイルのチュートリアル"){:new_window} 方法についてのチュートリアルが記載されています。 このチュートリアルでは、接続プロファイル内の CA エンドポイント情報を使用して、SDK で鍵を生成しています。 コンソールで公開鍵と秘密鍵を生成してから PEM 形式に変換することもできます。 そして、以下のコードを使用して鍵を直接 SDK [Fabric クライアント・クラス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Client.html "Fabric クライアント・クラス") に渡して、ユーザー・コンテキストを設定できます。

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

低水準の SDK API を使用してネットワークに接続する場合は、アプリケーションのパフォーマンスと可用性を管理するために追加の手順を取る必要があります。 詳しくは、[アプリケーションの接続性と可用性のためのベスト・プラクティス](/docs/services/blockchain/v10_application.html#dev-app-connectivity-availability) を参照してください。

## CouchDB での索引の使用
{: #console-app-couchdb}

CouchDB を状態データベースとして使用すると、スマート・コントラクトからチャネルの状態データに対して JSON データ照会を実行できます。 JSON 照会用に索引を作成してスマート・コントラクトで使用することを強くお勧めします。 索引があれば、ネットワークがトランザクションとエントリーの追加ブロックをワールド・ステートに追加するときに、アプリケーションで効率的にデータを取得できます。 スマート・コントラクトやアプリケーションで索引を使用する方法について詳しくは、[CouchDB を使用する場合のベスト・プラクティス](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices)を参照してください。

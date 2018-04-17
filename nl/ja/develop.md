---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# ネットワークの開発
{: #develop-the-network}

{{site.data.keyword.blockchainfull}} Platform を使用したブロックチェーン・ソリューションの開発では、Hyperledger Composer のオープン・ソース開発ツール・セットを利用します。JavaScript トランザクションとアクセス制御ルールを組み合わせたカスタム・モデリング言語を Hyperledger Composer で使用すると、実際のブロックチェーン・ネットワークに何かをデプロイする_前 _ に、ブロックチェーン・ビジネス・ネットワーク全体をモデル化できます。
{:shortdesc}

**注**: スターター・プランには、学習して試すことができるサンプル・アプリケーションが用意されています。カスタマイズされたビジネス・ネットワークを開発する前に、スターター・プランでサンプル・アプリケーションを実行できます。詳しくは、[スターター・プランについて](starter_plan.html)を参照してください。

このチュートリアルでは、{{site.data.keyword.blockchain}} ソリューションの開発手順を説明します。まず、ビジネス・ネットワークの作成とモデリングを行い、次にそれを実行中のブロックチェーン・ネットワークにデプロイします。

以下のステップは、{{site.data.keyword.blockchainfull_notm}} Platform でホストされる {{site.data.keyword.blockchain}} ソリューション開発の基本的な手順の概要を示しています。

1. ビジネス・ネットワーク定義を含めるデータ構造を作成する。ビジネス・ネットワークは、[モデリング言語](https://hyperledger.github.io/composer/latest/reference/cto_language)を使用して、資産、参加者、トランザクションに関して定義されます。

2. デプロイメント後に使用される資産クラス、参加クラス、トランザクション・クラス、イベント・クラス、アクセス制御ルールを定義して、ビジネス・ネットワークをモデル化する。

3. 定義されたビジネス・ネットワークを 1 つのデプロイ可能ファイルにパッケージする。

4. パッケージ化されたビジネス・ネットワークを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイする。ビジネス・ネットワークがデプロイされると、外部アプリケーションとの統合を可能にする REST サーバーを生成できます。

## 始めに

[{{site.data.keyword.blockchainfull_notm}}: Develop 開発ツール](./develop_install.html)がインストールされていることを確認します。

## ステップ 1: ビジネス・ネットワーク構造の作成

{{site.data.keyword.blockchain}} ソリューション開発の主要な概念の一つは、**ビジネス・ネットワーク定義 (BND)** です。BND は、ブロックチェーン・ソリューションにおけるデータ・モデル、トランザクション・ロジック、アクセス制御ルールを定義します。BND を作成するには、ディスク上に適切なプロジェクト構造を作成する必要があります。

開始する最も簡単な方法は、Yeoman ジェネレーターを使用してスケルトン・ビジネス・ネットワークを作成する方法です。Yeoman ジェネレーターは、ビジネス・ネットワークのすべてのコンポーネントを入れたディレクトリーを作成します。このディレクトリーは、特定のユースケースに合わせて変更できます。

1. Yeoman を使用してスケルトン・ビジネス・ネットワークを作成します。以下のコマンドには、ビジネス・ネットワーク名、説明、作成者名、作成者 E メール・アドレス、ライセンス選択、名前空間が必要です。

        yo hyperledger-composer:businessnetwork

2. ネットワーク名には `tutorial-network` と入力し、説明、作成者名、作成者 E メールには必要な情報を入力します。

3. ライセンスとして `Apache-2.0` を選択します。

4. 名前空間として `org.acme.biznet` を選択します。

## ステップ 2: ビジネス・ネットワークの定義

ビジネス・ネットワークは、資産、参加者、トランザクション、アクセス制御ルールで構成され、オプションでイベントと照会を追加できます。ステップ 1 で作成したスケルトン・ビジネス・ネットワークにはモデル・ファイル (`.cto`) があり、そこには、ビジネス・ネットワーク内のすべての資産、参加者、トランザクションのクラス定義が含まれています。スケルトン・ビジネス・ネットワークには、基本的なアクセス制御ルールを記述するアクセス制御文書 (`permissions.acl`)、トランザクション・プロセッサー機能を記述するスクリプト・ファイル (`logic.js`)、ビジネス・ネットワーク・メタデータを入れたパッケージ・ファイル (`package.json`) も含まれています。

### 資産、参加者、トランザクションのモデル化

更新する最初の文書は、モデル・ファイル (`.cto`) です。このファイルは、[Hyperledger Composer モデリング言語](https://hyperledger.github.io/composer/latest/reference/cto_language)を使用して作成されます。モデル・ファイルには、資産、トランザクション、参加者、イベントの各クラスの定義が含まれています。これは、モデリング言語文書に記載されているシステム・モデルを暗黙的に拡張します。

1. `org.acme.biznet.cto` モデル・ファイルを開きます。

2. 内容を以下の情報に置き換えます。

        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }

3. `org.acme.biznet.cto` ファイルへの変更を保存します。


### JavaScript トランザクション・ロジックの追加

モデル・ファイルでは、資産と参加者との関係を指定する `Trade` トランザクションが定義されました。トランザクション・プロセッサー機能ファイルには、モデル・ファイルに定義されているトランザクションを実行するための JavaScript ロジックが含まれています。

`Trade` トランザクションは、取り引きされる `Commodity` 資産の ID と、新規所有者として設定する `Trader` 参加者の ID を単に受け入れることを目的としています。

1. `lib` ディレクトリーの `logic.js` スクリプト・ファイルを開きます。

2. 内容を以下の情報に置き換えます。

        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }

3. `logic.js` への変更を保存します。

### アクセス制御の追加

アクセス制御は、ビジネス・ネットワークの重要な部分です。すべてのビジネス・ネットワークには、`permissions.acl` というアクセス制御ファイルが必要です。デプロイされたビジネス・ネットワークでプロセスが呼び出されるたびに、呼び出し元の ID または参加者がその操作を呼び出せることを確証するために、アクセス制御リストが検査されます。

このチュートリアルの目的のために、単純な ACL をセットアップします。これは実稼働環境にデプロイしてはならないことに注意してください。

1. `tutorial-network` ディレクトリーに `permissions.acl` ファイルを作成します。

2. 以下のアクセス制御ルールを `permissions.acl` に追加します。

        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }

3. `permissions.acl` への変更を保存します。

## ステップ 3: ビジネス・ネットワークのパッケージ

ビジネス・ネットワークを定義したら、デプロイ可能なビジネス・ネットワーク・アーカイブ・ファイル (`.bna`) にパッケージする必要があります。

1. コマンド・ラインを使用して、`tutorial-network` ディレクトリーにナビゲートします。

2. `tutorial-network` ディレクトリーから次のコマンドを実行します。

        composer archive create -t dir -n .

コマンドを実行すると、`tutorial-network` ディレクトリーにビジネス・ネットワーク・アーカイブ・ファイル (`tutorial-network@0.0.1.bna`) が作成されます。

## ステップ 4: ビジネス・ネットワークのデプロイと REST サーバーの生成

[スターター・プラン](./develop_starter.html)または[エンタープライズ・プラン](./develop_enterprise.html)を使用して、ビジネス・ネットワークを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイします。

ビジネス・ネットワークを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイしたら、カスタム REST サーバーを生成できます。REST サーバーは、ビジネス・ネットワークの内容を調べ、ネットワークの資産、トランザクション、参加者のための RESTful API 呼び出しを作成します。REST サーバーの生成手順は、[Hyperledger Composer の資料](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api)から参照できます。

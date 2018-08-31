---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 開発環境のインストール
{: #installing-a-development-environment}

ビジネス・ネットワークの作成とテストを行うための {{site.data.keyword.blockchainfull}} Platform: Develop 開発ツールを入手するには、以下の手順に従います。 開発、テスト、デプロイメントにおけるできるだけ多くのシナリオに柔軟に対応するため、開発ツールは、コマンド・ラインで制御する npm モジュールとして提供されます。

Starter Plan と Enterprise Plan で異なるバージョンを使用しなければならないコンポーネントがあります。 Starter Plan で開発環境を使用するには、バージョン `0.19.x` のモジュールをインストールする必要があります。 Enterprise Plan で開発環境を使用するには、バージョン `0.16.x` のモジュールをインストールする必要があります。

## 始めに

以下の前提条件を必ずインストールしてください。

- Ubuntu Linux 14.04 / 16.04 LTS (どちらも 64 ビット)、または Mac OS 10.12
- Node v8.9 以上 (v9 はサポートされていません)
- npm v5.x
- git v2.9.x 以上
- Python v2.7.x
- 任意のコード・エディター (VSCode など)

Enterprise Plan を使用する場合は、以下の追加の前提条件をインストールします。

- Docker Engine v17.03 以上
- Docker-Compose v1.8 以上

VSCode を使用している場合は、Hyperledger Composer VSCode 拡張機能を[ここで](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client)入手できます。

## ステップ 1: コマンド・ライン・ツールのインストール

コマンド・ライン・ツールには、**composer-cli**、メインの {{site.data.keyword.blockchainfull_notm}} Platform:Develop コマンド・ライン・モジュール、**generator-hyperledger-composer**、**composer-rest-server**、**Yeoman** が含まれます。これらのモジュールは、ビジネス・ネットワーク、カスタム REST サーバー、Angular アプリケーションの生成に使用されます。

1. Starter Plan で使用するには、次のコマンドを使用して **composer-cli** をインストールします。

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

    Enterprise Plan で使用するには、次のコマンドを使用して **composer-cli** をインストールします。

    ```
    npm install -g composer-cli@0.16.x
    ```
    {:codeblock}

2. Starter Plan で使用するには、次のコマンドを使用して **composer-rest-server** をインストールします。

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

    Enterprise Plan で使用するには、次のコマンドを使用して **composer-rest-server** をインストールします。

    ```
    npm install -g composer-rest-server@0.16.x
    ```
    {:codeblock}

    **composer-rest-server** モジュールを使用して、ビジネス・ネットワークを RESTful API として公開するための REST サーバーをマシン上に作成します。

3. Starter Plan で使用するには、次のコマンドを使用して **generator-hyperledger-composer** をインストールします。

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Enterprise Plan で使用するには、次のコマンドを使用して **generator-hyperledger-composer** をインストールします。

    ```
    npm install -g generator-hyperledger-composer@0.16.x
    ```
    {:codeblock}

    **generator-hyperledger-composer** を使用して、スケルトン・ビジネス・ネットワーク構造、モデル、Angular アプリケーションを生成します。

4. Yeoman はアプリケーションを生成するためのツールで、`generator-hyperledger-composer` を使用します。

    ```
    npm install -g yo
    ```
    {:codeblock}

## ステップ 2: ローカル・プレイグラウンドのインストール

Hyperledger Composer プレイグラウンドというユーザー・インターフェースを使用すると、実際の {{site.data.keyword.blockchain}} に接続することができます。また、これをビジネス・ネットワークのテスト用シミュレート環境として使用することもできます。 Starter Plan で使用するには、次のコマンドを使用してプレイグラウンドをインストールします。

```
npm install -g composer-playground@0.19.x
```
{:codeblock}


Enterprise Plan で使用するには、次のコマンドを使用してプレイグラウンドをインストールします。


```
npm install -g composer-playground@0.16.x
```
{:codeblock}

## オプション: IDE のセットアップ

プレイグラウンドを使用して、ビジネス・ネットワークの開発、編集、テストを行うことができます。 ただし、IDE でビジネス・ネットワークを開発する必要がある場合は、VSCode 拡張機能を使用することで、Hyperledger Composer モデル化言語の構文の強調表示機能が追加されます。

1. 次の URL から VSCode を取得してインストールします: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. VSCode を開き、「Extensions」に移動し、マーケットプレイスから Hyperledger Composer 拡張機能を検索してインストールします。


## Enterprise Plan のみ: ローカル Hyperledger Fabric のインストールおよびプレイグラウンドを使用した接続

ローカル Hyperledger Fabric インスタンスをデプロイすることにより、アクセス制御ルールとビジネス・ネットワークを十分にテストできます。 以下のステップを実行する前に、Docker を開始していることを確認してください。

1. 次のコマンドを使用して `fabric-tools` ディレクトリーを作成します。

   ```
   mkdir ~/fabric-tools &&  cd ~/fabric-tools
   ```
   {:codeblock}

選択したディレクトリー (例えば `~/fabric-tools`) の中に、Hyperledger Fabric をインストールするためのツールを収めた `.tar.gz` ファイルを入れます。

2. Docker 環境で Hyperledger Fabric をインストールするためのツールを収めた `tar.gz` ファイルをダウンロードします。

   ```
   curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz
   ```
   {:codeblock}

    これは `.zip` でも入手可能です。 上記のスニペットの `.tar.gz` ファイルを `fabric-dev-servers.zip` に置き換え、`tar -xvf` コマンドを `unzip` コマンドに置き換えてください。

3. 次のコマンドを使用してローカル Hyperledger Fabric v1 ランタイムをダウンロードします。

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./downloadFabric.sh
   ```
   {:codeblock}

   `fabric-tools` ディレクトリーには、ダウンロードした Hyperledger Fabric インスタンスを制御するスクリプト一式が格納されています。 `./createPeerAdminCard.sh` コマンドを実行してビジネス・ネットワーク・カードを生成する必要があります。このカードを使用すると、Hyperledger Fabric インスタンスをホストする Docker にビジネス・ネットワークをデプロイできます。

4. 次のコマンドを実行して Hyperledger Fabric v1 インスタンスを開始します。

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./startFabric.sh
   ./createPeerAdminCard.sh
   ```
   {:codeblock}

   ビジネス・ネットワークの開発を開始するには、Hyperledger Composer プレイグラウンドまたは独自の IDE を使用します。

5. プレイグラウンドを開始するには、次のコマンドを実行します。

    ```
    composer-playground
    ```
    {:codeblock}

    URL: http://localhost:8080/login でプレイグラウンドが開きます。 前のステップで作成した **PeerAdmin@hlfv1** カードを使用して、ビジネス・ネットワークをデプロイできます。


### Hyperledger Fabric の開始と停止

`fabric-tools` ディレクトリーには、Hyperledger Fabric インスタンスを制御するスクリプト一式が格納されています。 `~/fabric-tools/stopFabric.sh` と `~/fabric-tools/startFabric.sh` を使用すると、ランタイムの停止と開始を実行できます。

`~/fabric-tools/teardownFabric.sh` を使用すると、次回に環境を開始するときに新しい PeerAdmin カードを作成する必要があります。

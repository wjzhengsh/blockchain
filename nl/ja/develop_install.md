---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# ビジネス・ネットワーク開発環境のインストール
{: #installing-a-development-environment}


***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


ビジネス・ネットワークの作成とテストを行うための {{site.data.keyword.blockchainfull}} Platform: Develop 開発ツールを入手するには、以下の手順に従います。 開発、テスト、デプロイメントにおけるできるだけ多くのシナリオに柔軟に対応するため、開発ツールは、コマンド・ラインで制御する npm モジュールとして提供されます。

## 始めに

以下の前提条件を必ずインストールしてください。

- Ubuntu Linux 14.04 / 16.04 LTS (どちらも 64 ビット)、または Mac OS 10.12
- Node v8.9 以上 (v9 はサポートされていません)
- npm v5.x
- git v2.9.x 以上
- Python v2.7.x
- 任意のコード・エディター (VSCode など)

エンタープライズ・プランを使用する場合は、以下の追加の前提条件をインストールします。

- Docker Engine v17.03 以上
- Docker-Compose v1.8 以上

VSCode を使用している場合は、Hyperledger Composer VSCode 拡張機能を[ここで](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client)入手できます。

## ステップ 1: コマンド・ライン・ツールのインストール

コマンド・ライン・ツールには、**composer-cli**、メインの {{site.data.keyword.blockchainfull_notm}} Platform:Develop コマンド・ライン・モジュール、**generator-hyperledger-composer**、**composer-rest-server**、**Yeoman** が含まれます。これらのモジュールは、ビジネス・ネットワーク、カスタム REST サーバー、Angular アプリケーションの生成に使用されます。 インストールするバージョンは、スターター・プランまたはエンタープライズ・プランが実行されている Hyperledger Fabric のバージョンによって異なります。Fabric のバージョンは、ネットワーク・モニターで[「ネットワーク設定 (Network preferences)」ウィンドウ](/docs/services/blockchain/v10_dashboard.html#network-preferences)を開くことで確認できます。

1. Hyperledger Fabric v1.2.1 を実行しているスターター・プランの新規インスタンスで使用する場合は、以下のコマンドを使用して **composer-cli** をインストールします。

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Hyperledger Fabric v1.1 を実行しているエンタープライズ・プランおよびスターター・プランのインスタンスで使用する場合は、以下のコマンドを使用して **composer-cli** をインストールします。

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Hyperledger Fabric v1.2.1 を実行しているスターター・プランの新規インスタンスで使用する場合は、以下のコマンドを使用して **composer-rest-server** をインストールします。

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Hyperledger Fabric v1.1 を実行しているエンタープライズ・プランおよびスターター・プランのインスタンスで使用する場合は、以下のコマンドを使用して **composer-rest-server** をインストールします。

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 **composer-rest-server** モジュールを使用して、ビジネス・ネットワークを RESTful API として公開するための REST サーバーをマシン上に作成します。

3. Hyperledger Fabric v1.2.1 を実行しているスターター・プランの新規インスタンスで使用する場合は、以下のコマンドを使用して **generator-hyperledger-composer** をインストールします。

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Hyperledger Fabric v1.1 を実行しているエンタープライズ・プランおよびスターター・プランのインスタンスで使用する場合は、以下のコマンドを使用して **generator-hyperledger-composer** をインストールします。

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    **generator-hyperledger-composer** を使用して、スケルトン・ビジネス・ネットワーク構造、モデル、Angular アプリケーションを生成します。

4. Yeoman はアプリケーションを生成するためのツールで、`generator-hyperledger-composer` を使用します。

    ```
    npm install -g yo
    ```
    {:codeblock}

## ステップ 2: ローカル・プレイグラウンドのインストール

Hyperledger Composer プレイグラウンドというユーザー・インターフェースを使用すると、これをビジネス・ネットワークのテスト用シミュレート環境として使用することができます。 以下のコマンドを使用して、プレイグラウンドをインストールします。

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## オプション: IDE のセットアップ

プレイグラウンドを使用して、ビジネス・ネットワークの開発、編集、テストを行うことができます。 ただし、IDE でビジネス・ネットワークを開発する必要がある場合は、VSCode 拡張機能を使用することで、Hyperledger Composer モデル化言語の構文の強調表示機能が追加されます。

1. 次の URL から VSCode を取得してインストールします: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. VSCode を開き、「Extensions」に移動し、マーケットプレイスから Hyperledger Composer 拡張機能を検索してインストールします。


### Hyperledger Fabric の開始と停止

`fabric-tools` ディレクトリーには、Hyperledger Fabric インスタンスを制御するスクリプト一式が格納されています。 `~/fabric-tools/stopFabric.sh` と `~/fabric-tools/startFabric.sh` を使用すると、ランタイムの停止と開始を実行できます。

`~/fabric-tools/teardownFabric.sh` を使用すると、次回に環境を開始するときに新しい PeerAdmin カードを作成する必要があります。

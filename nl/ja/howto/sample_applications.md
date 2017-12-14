---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# サンプル・アプリケーション

サンプル・アプリケーションをデプロイしてテストすると、{{site.data.keyword.blockchainfull}}・ネットワークとアプリケーション開発についての理解を深めることができます。{:shortdesc}

## 始めに

前提条件となるすべてのソフトウェアがローカル・ファイル・システムにインストールされていることを確認します。詳しくは、[アプリケーション開発環境のセットアップ](../v10_application.html#setting-up-application-development-environment)を参照してください。

また、チャネルとそのピアが構成された{{site.data.keyword.blockchain}}・ネットワークが {{site.data.keyword.Bluemix_short}} に存在する必要があります。
詳しくは、[ネットワークの管理](../get_start.html)を参照してください。ネットワークを使用する準備ができたら、アプリケーションがアクセスするネットワーク・リソースの API エンドポイントを取得します。詳しくは、[アプリケーションへのネットワーク・サービス資格情報の追加](../v10_application.html#adding-network-service-credentials-to-your-application)を参照してください。


## マーブル・アプリケーション

マーブル・アプリケーションでは、複数のユーザーがさまざまなプロパティーを使用してマーブルを作成し、ユーザー間でマーブルを転送することができます。
マーブル・アプリケーションは Javascript で作成されており、チェーンコードは Go で作成されています。

サンプル・コードと説明が [GitHub の Marbles ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM-Blockchain/marbles) にあります。


## Fabric 車両アプリケーション

Fabric 車両アプリケーションでは、台帳の車両記録に対する**照会**と**更新**を実行できます。Fabric 車両アプリケーションは Javascript で作成されており、チェーンコードは Go で作成されています。

[GitHub の Fabric 車両 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) にサンプル・コードがあります。説明は [Writing Your First Application ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html) にあります。

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

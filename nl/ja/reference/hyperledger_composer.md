---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}

{{site.data.keyword.blockchainfull}} Platform: Develop は、Linux Foundation の Hyperledger Project 内のプロジェクトの 1 つである、オープン・ソースの Hyperledger Composer ツール・セット上に構築されています。 Hyperledger Composer により、アーキテクトと開発者は、Web アプリケーションやモバイル・アプリケーションにビジネス・ロジックを公開する REST API を使用して、{{site.data.keyword.blockchain}} ソリューションを迅速に作成できるだけでなく、ブロックチェーンをレコードの既存のエンタープライズ・システムと統合できます。
{:shortdesc}

Hyperledger Composer は、ランタイム、コマンド・ライン・インターフェース、REST サーバー、LoopBack コネクター、プレイグラウンド・ユーザー・インターフェース、Yeoman コード生成プログラムと、VSCode および Atom エディター・プラグインで構成されています。 Hyperledger Composer について詳しくは、[Hyperledger Composer の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger.github.io/composer/latest/introduction/introduction.html) を参照してください。


## ビジネス・ネットワーク

Hyperledger Composer を使用すると、既存の資産とそれらに関連するトランザクションを含む現在のビジネス・ネットワークを迅速にモデル化できます。資産とは、有形または無形の商品、サービス、所有権のことです。 ビジネス・ネットワーク・モデルの一部として、資産とやり取りできるトランザクションを定義します。 ビジネス・ネットワークには、それとやり取りする参加者も含まれ、それぞれの参加者に複数のビジネス・ネットワーク上の固有 ID を関連付けることができます。

ビジネス・ネットワークの構築について詳しくは、[ビジネス・ネットワークの開発](./develop.html)または [Hyperledger Composer の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger.github.io/composer/latest/introduction/introduction.html) を参照してください。

## 照会

照会は、ブロックチェーン・ワールド・ステートに関するデータを返すために使用されます。 照会はビジネス・ネットワーク内で定義され、単純なカスタマイズのための変数パラメーターを含めることができます。 照会を使用することにより、{{site.data.keyword.blockchain}} ネットワークからデータを容易に抽出できます。 照会は [Hyperledger Composer API ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger.github.io/composer/latest/api/api-doc-index) を使用して送信されます。

ビジネス・ネットワークでの照会の使用について詳しくは、[Querying and filtering business network data ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger.github.io/composer/business-network/query) を参照してください。

## ID とビジネス・ネットワーク・カード

ビジネス・ネットワーク内では、参加者に ID が関連付けられます。 ビジネス・ネットワーク・カードは ID、接続プロファイル、メタデータの組み合わせであり、ビジネス・ネットワークに接続してトランザクションの ID を実証するために使用されます。 ビジネス・ネットワーク・カードにより、ビジネス・ネットワークに接続するプロセスが単純になります。また、この ID の概念をビジネス・ネットワーク外に広げて、それぞれが特定のビジネス・ネットワークと接続プロファイルに関連付けられた ID の入った「ウォレット」が構成されます。

ID とビジネス・ネットワーク・カードについて詳しくは、[Participants and identities ![外部リンク・アイコン ](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger.github.io/composer/managing/participantsandidentities) を参照してください。

## Hyperledger Composer REST サーバー

Hyperledger Composer REST サーバーは、ビジネス・ネットワーク用の Swagger REST API を自動的に生成します。 REST サーバーは LoopBack に基づいており、ビジネス・ネットワーク・モデルを Open API 定義に変換します。 実行時に REST サーバーは、資産と参加者の作成、読み取り、更新、削除のサポートを実装し、トランザクションの処理を実行依頼したり、照会を使用してトランザクションを取得したりすることができるようにします。

Hyperledger Composer REST サーバーについて詳しくは、[Generating a REST API ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger.github.io/composer/integrating/getting-started-rest-api) を参照してください。

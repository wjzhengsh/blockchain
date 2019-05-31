---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

keywords: IBM Blockchain offerings, Linux Foundation, Hyperledger Fabric, open source, community contribution

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# 特記事項
{: #disclaimer}

**重要:** どの {{site.data.keyword.blockchainfull}} プランを使用する場合も、まず、以下の情報を確認してください。

## IBM サポート・ステートメント
{: #disclaimer-support-statement}

イノベーションを牽引してきた {{site.data.keyword.IBM}} の長い歴史は、{{site.data.keyword.cloud_notm}} の {{site.data.keyword.blockchainfull_notm}} Platform オファリングについても続いています。 ブロックチェーンは急速に成長を続けているテクノロジーです。金融業、ローカルおよびグローバルなサプライ・チェーン、あらゆる業界の物流サポートを劇的に変革することが予測されます。 さまざまな早期導入プログラムによって、{{site.data.keyword.IBM_notm}} のお客様およびビジネス・パートナー様は、産業用ソリューションとしてブロックチェーンを積極的に推進してきました。 {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} はそのようなプログラムの 1 つです。 **他の新しいテクノロジーと同様に、{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} ユーザーも急速な根本的変化の可能性を認識しておく必要があります**。
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} の背景にあるアーキテクチャーは Linux Foundation の Hyperledger Fabric プロジェクトです。 各オープン・ソース・コミュニティーのコントリビューションによって、Hyperledger Fabric は改良されましたが、導入の課題が残っています。 **{{site.data.keyword.IBM_notm}} では、直接 Hyperledger Fabric ブロックチェーン・ネットワークで金融資産<!--, or any assets of value,-->を定義または交換することは避けるように注意を促しています。**

{{site.data.keyword.IBM_notm}} では、実動の Hyperledger Composer を使用するネットワークをサポートしていません。これには Composer CLI、JavaScript API、REST サーバー、および Web Playground が含まれます。
{:note}

## オープン・ソースのステートメント
{: #disclaimer-open-source-statement}

{{site.data.keyword.cloud_notm}} の {{site.data.keyword.blockchainfull_notm}} オファリング・プランは、Linux Foundation の Hyperledger Fabric スタックの上に構築されています。 {{site.data.keyword.IBM_notm}} を含む Hyperledger Project メンバーは継続して Hyperledger の傘下のさまざまなサブプロジェクトにコントリビューションを提供します。  いずれのコントリビューションも、コミュニティーで検討、精査、テストされます。

## チェーンコード・サポート・ステートメント
{: #disclaimer-chaincode-support-statement}

コーディングに関する以下の慣例は {{site.data.keyword.blockchainfull_notm}} ネットワークではサポートされません。

1. 反復のある結合配列の使用 (Go では順序がランダムになってしまいます)。
2. KVS 表の項目リストの読み込み (順序が保証されません)。
3. スレッド・セーフでないチェーンコードの書き込み (照会と呼び出しが並行して呼び出されます)。
4. チェーンコードの台帳の状態変数の代わりにグローバル・メモリーまたはキャッシュ・ストレージを使用すること。
5. チェーンコードから直接、データベースなどの外部サービスにアクセスすること。
6. 非決定論的になる可能性のあるライブラリーまたはグローバル変数の使用 (「random」や「time」などの使用)。

また、データの一貫性や保全性を脅かす非決定論的チェーンコードを作成することは推奨されません。 Hyperledger Fabric アーキテクチャーは、一連の承認や有効性検査を通して非決定論的チェーンコードに対応するように設計されています。 それでも、非静的なグローバル変数 (例えば、時刻) に依存しない決定論的な機能をコーディングすることを強くお勧めします。

---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 特記事項
{: #disclainer}

**重要:** どの {{site.data.keyword.blockchainfull}} プランを使用する場合も、まず、以下の情報を確認してください。

## IBM サポート・ステートメント

イノベーションを牽引してきた IBM の長い歴史は、{{site.data.keyword.Bluemix_notm}} の {{site.data.keyword.blockchainfull_notm}} オファリング・プランについても続いています。 {{site.data.keyword.blockchain}} は急速に成長を続けているテクノロジーです。金融業、ローカルおよびグローバルなサプライ・チェーン、あらゆる業界の物流サポートを劇的に変革することが予測されます。 さまざまな早期導入プログラムによって、IBM のお客様およびビジネス・パートナー様は、産業用ソリューションとしてブロックチェーンを積極的に推進してきました。 {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} はそのようなプログラムの 1 つです。 **他の新しいテクノロジーと同様に、{{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} ユーザーも急速な根本的変化の可能性を認識しておく必要があります**。  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} の背景にあるアーキテクチャーは Linux Foundation の Hyperledger Fabric プロジェクトです。 各オープン・ソース・コミュニティーのコントリビューションによって、Hyperledger Fabric は改良されましたが、導入の課題が残っています。 **IBM では、直接 Hyperledger Fabric ブロックチェーン・ネットワークで金融資産などの価値ある資産を定義または交換することは避けるように注意を促しています**。  

## オープン・ソース・ステートメント

{{site.data.keyword.Bluemix_notm}} の {{site.data.keyword.blockchainfull_notm}} オファリング・プランは、Linux Foundation の Hyperledger Fabric スタックの上に構築されています。 IBM を含む Hyperledger Project メンバーは継続して Hyperledger の傘下のさまざまなサブプロジェクトにコントリビューションを提供します。  いずれのコントリビューションも、コミュニティーで検討、精査、テストされます。

## チェーンコード・サポート・ステートメント

コーディングに関する以下の慣例は {{site.data.keyword.blockchainfull_notm}} ネットワークではサポートされません。

1. 反復のある結合配列の使用 (Go では順序がランダムになってしまいます)。
2. KVS 表の項目リストの読み込み (順序が保証されません)。
3. スレッド・セーフでないチェーンコードの書き込み (照会と呼び出しが並行して呼び出されます)。
4. チェーンコードの台帳の状態変数の代わりにグローバル・メモリーまたはキャッシュ・ストレージを使用すること。
5. チェーンコードから直接、データベースなどの外部サービスにアクセスすること。
6. 非決定論的になる可能性のあるライブラリーまたはグローバル変数の使用 (「random」や「time」などの使用)。  

また、データの一貫性や保全性を脅かす非決定論的チェーンコードを作成することは推奨されません。  Hyperledger Fabric アーキテクチャーは、一連の承認や有効性検査を通して非決定論的チェーンコードに対応するように設計されています。それでも、非静的なグローバル変数 (例えば、時刻) に依存しない決定論的な機能をコーディングすることを強くお勧めします。  

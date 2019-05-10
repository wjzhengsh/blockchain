---

copyright:
  years: 2019
lastupdated: "2019-04-18"

---

{:new_window: target="_blank"}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}

# リリース情報
{: #release-notes-saas-20}

ここでは、Hyperledger Fabric v1.4.0 を基盤にした {{site.data.keyword.blockchainfull}} Platform 2.0 無料ベータ版の最新の変更内容を確認できるように、リリース情報を日付ごとにまとめています。
{:shortdesc}


## 2019 年 4 月 17 日

**ノード・リソースのサイズとスケールが指定可能に**  

ノードをデプロイする時に、必要に応じて、コンテナーの CPU、メモリー、ストレージの量を指定できるようになりました。使用状況のパターンに基づいて、後からリソースをスケールアップ/スケールダウンすることもできます。詳しくは、[リソースの割り振り](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-allocate-resources)を参照してください。

**IAM の使用: IBM Cloud IAM (ID およびアクセス管理)**  

コンソール UI へのユーザー・アクセスの制御、および UI で実行可能なユーザー・アクションの制限を行うために、IAM が使用されます。[コンソールでのユーザーの追加と削除](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)の方法に関するトピックを参照してください。

## 2019 年 4 月 3 日
{: #04-03-2019}

**外部 CA のサポート**

ピア・ノードや順序付けプログラム・ノードを追加するときに、外部 CA ({{site.data.keyword.IBM_notm}} がホストする CA ではないもの) で作成された証明書を使用できます。詳しくは、[外部 CA からの証明書をピアまたは順序付けプログラムに使用する](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca)のトピックを参照してください。

**順序付けプログラムのパフォーマンスの調整**

順序付けプログラムのスループットやパフォーマンスを細かく制御できるように、順序付けプログラムの新しいチューニング・パラメーターがコンソールに用意されました。パラメーターの構成方法については、[順位付けプログラムのチューニング](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning)に関するトピックを参照してください。

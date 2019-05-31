---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: release note, latest changes, Hyperledger Fabric

subcollection: blockchain

---

{:new_window: target="_blank"}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}

# リリース情報
{: #release-notes-saas-20}

ここでは、Hyperledger Fabric v1.4.1 を基盤にした {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} の最新の変更内容を確認できるように、リリース情報を日付ごとにまとめています。
{:shortdesc}


## 2019 年 5 月 9 日
{: #05-09-2019}

**{{site.data.keyword.blockchainfull_notm}} Platform コンソール API の導入**

ピア、順序付けプログラム、および CA のノードのプロビジョン、編集、および削除に API を使用できるようになったため、ブロックチェーン・ネットワークの構築をスクリプト化することができます。API の詳細を確認して試行するには、[{{site.data.keyword.cloud_notm}} API 資料のリポジトリー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](/apidocs/blockchain#introduction "概要") の資料を使用します。また、API を使用してネットワークを構築する手順については、[API を使用したネットワークの構築](/docs/services/blockchain?topic=blockchain-ibp-v2-apis)のトピックを参照してください。  

**チャネル・ガバナンス**  

チャネル・ガバナンスの更新により、ポリシーを再構成できます。チャネルの更新に署名する必要があるチャネル・メンバーを制御して、署名の収集を調整することもできます。

## 2019 年 4 月 17 日
{: #04-17-2019}

**ノード・リソースのサイズとスケールが指定可能に**  

ノードをデプロイする時に、必要に応じて、コンテナーの CPU、メモリー、ストレージの量を指定できるようになりました。 使用状況のパターンに基づいて、後からリソースをスケールアップ/スケールダウンすることもできます。 詳しくは、[リソースの割り振り](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources)を参照してください。

**IAM の使用: IBM Cloud IAM (ID およびアクセス管理)**  

コンソール UI へのユーザー・アクセスの制御、および UI で実行可能なユーザー・アクションの制限を行うために、IAM が使用されます。  [コンソールでのユーザーの追加と削除](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)の方法に関するトピックを参照してください。

## 2019 年 4 月 3 日
{: #04-03-2019}

**外部 CA のサポート**

ピア・ノードや順序付けプログラム・ノードを追加するときに、外部 CA ({{site.data.keyword.IBM_notm}} がホストする CA ではないもの) で作成された証明書を使用できます。 詳しくは、[外部 CA からの証明書をピアまたは順序付けプログラムに使用する](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca)のトピックを参照してください。

**順序付けプログラムのパフォーマンスの調整**

順序付けプログラムのスループットやパフォーマンスを細かく制御できるように、順序付けプログラムの新しいチューニング・パラメーターがコンソールに用意されました。 パラメーターの構成方法については、[順位付けプログラムのチューニング](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning)に関するトピックを参照してください。

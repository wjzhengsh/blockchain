---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: FAQs, can I, upgrade, what version, peer ledger database, supported languages, why do I, regions

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# FAQ
{: #ibp-v2-faq}

- [ピアはどのデータベースを台帳用に使用しますか?](#ibp-v2-faq-v2-IBP-Overview-1-3)
- [スマート・コントラクトの言語として、どの言語がサポートされていますか?](#ibp-v2-faq-v2-IBP-Overview-1-4)
- [V1.0 から新しい {{site.data.keyword.blockchainfull_notm}} Platform にアップグレードできますか?](#ibp-v2-faq-v2-IBP-Overview-1-5)
- [ネイティブの Hyperledger Fabric よりも {{site.data.keyword.blockchainfull_notm}} Platform を使用するメリットとして、どのようなものがありますか?](#ibp-v2-faq-v2-IBP-Overview-1-7)
- [{{site.data.keyword.blockchainfull_notm}} Platform サービスを削除したらどうなりますか?](#ibp-v2-faq-v2-IBP-Overview-1-8)
- [{{site.data.keyword.cloud_notm}} で実行されているブロックチェーン・サービスにはどの地域を使用できますか?](#ibp-v2-faq-v2-IBP-Overview-1-9)
- [どのバージョンの Hyperledger Fabric を {{site.data.keyword.blockchainfull_notm}} Platform と併用できますか?](#ibp-v2-faq-v2-Hyperledger-Fabric-3-1)
- [既存の {{site.data.keyword.cloud_notm}} Kubernetes Service クラスターを使用できますか?](#ibp-v2-faq-v2-Infrastructure-4-2)
- [ロギング・サービスにはアクセスできますか? どのログが使用可能ですか?](#ibp-v2-faq-v2-Logging-and-Monitoring-11-6)


## ピアはどのデータベースを台帳用に使用しますか?
{: #ibp-v2-faq-v2-IBP-Overview-1-3}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform でデプロイされたすべてのピアでは、台帳のデータベースとして CouchDB が使用されます。

## スマート・コントラクトの言語として、どの言語がサポートされていますか?
{: #ibp-v2-faq-v2-IBP-Overview-1-4}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform は、Go および Node.js で作成されたスマート・コントラクトをサポートしています。新しい Hyperledger Fabric プログラミング・モデルは、現在は Node.js のみサポートしており、それ以外は近日サポートされます。既存のアプリケーション・コードを保持や、Node.js 以外の言語用の Fabric SDK の使用に関心がある場合には、以前と変わらず低水準の Fabric SDK API を使用して {{site.data.keyword.blockchainfull_notm}} Platform ネットワークに接続できます。

## V1.0 から新しい {{site.data.keyword.blockchainfull_notm}} Platform にアップグレードできますか?
{: #ibp-v2-faq-v2-IBP-Overview-1-5}
{: faq}

スターター・プランではできません。スターター・プランから新しい {{site.data.keyword.blockchainfull_notm}} Platform にアップグレードすることはできません。
エンタープライズ・プランの場合は、将来的に新しい {{site.data.keyword.blockchainfull_notm}} Platform にアップグレードできます。アカウント所有者に、{{site.data.keyword.blockchainfull_notm}} Platform チームから、支援のための E メールが送信されます。

## ネイティブの Hyperledger Fabric よりも {{site.data.keyword.blockchainfull_notm}} Platform を使用するメリットとして、どのようなものがありますか?
{: #ibp-v2-faq-v2-IBP-Overview-1-7}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform を使用すると、お客様はカスタマイズされたブロックチェーン・ネットワークを簡単にデプロイできます。直観的なコンソール UI を使用して、迅速にネットワークをデプロイし、容易にスマート・コントラクトをインストールおよびインスタンス化して、トランザクションをモニターできます。

## {{site.data.keyword.blockchainfull_notm}} Platform サービスを削除したらどうなりますか?
{: #ibp-v2-faq-v2-IBP-Overview-1-8}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを削除すると、コンソールのみが削除されます。コンソールで作成されたすべてのコンポーネントとその関連するストレージは、削除されません。Kubernetes クラスターおよびインポートされたコンポーネントも削除されません。Kubernetes ダッシュボードまたは CLI コマンドを使用して、自分自身で Kubernetes ノードおよびストレージを削除するまで、Kubernetes ノードおよびストレージに対して引き続き課金されます。

## {{site.data.keyword.cloud_notm}} で実行されているブロックチェーン・サービスにはどの地域を使用できますか?
{: #ibp-v2-faq-v2-IBP-Overview-1-9}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform に使用可能な地域は、[{{site.data.keyword.blockchainfull_notm}} Platform のロケーション](/docs/services/blockchain?topic=blockchain-ibp-regions-locations)にリストされます。クラスターが認識されるように、ブロックチェーン・サービスと同じ地域に {{site.data.keyword.cloud_notm}} Kubernetes Service クラスターを作成する必要があることに注意してください。近日中に追加の地域が使用可能になります。

## どのバージョンの Hyperledger Fabric を {{site.data.keyword.blockchainfull_notm}} Platform と併用できますか?
{: #ibp-v2-faq-v2-Hyperledger-Fabric-3-1}
{: faq}

2019 年 2 月時点では、ベータ版が Hyperledger Fabric 1.4.0 を使用して起動されていました。
2019 年 5 月 29 日時点では、GA が Hyperledger Fabric 1.4.1 を使用して起動されていました。GA 製品では Hyperledger Fabric 1.4.0 はサポートされていません。

## 既存の {{site.data.keyword.cloud_notm}} Kubernetes Service クラスターを使用できますか?
{: #ibp-v2-faq-v2-Infrastructure-4-2}
{: faq}

既存の Kubernetes クラスターが以下の条件を満たしている限り、そのクラスターは {{site.data.keyword.blockchainfull_notm}} Platform で使用できます。
- Kubernetes v1.11 以降の安定バージョンを実行している。
- クラスター内に使用できるリソースが十分ある。

## ロギング・サービスにはアクセスできますか? どのログが使用可能ですか?
{: #ibp-v2-faq-v2-Logging-and-Monitoring-11-6}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform をご利用の場合、現在 Kubernetes ダッシュボードからピア、CA、順序付けプログラムのログに直接アクセスできます。 ログをリアルタイムで簡単に解析できる {{site.data.keyword.cloud_notm}} LogDNA サービスを利用することをお勧めします。

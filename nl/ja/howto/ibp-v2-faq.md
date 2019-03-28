---

copyright:
  years: 2019
lastupdated: "2019-03-05"

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

## どのバージョンの Hyperledger Fabric を {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版と併用できますか?
{: #ibp-v2-faq-fabric-version}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版は、Hyperledger Fabric v1.4 を使用しており、ゴシップ、サービス・ディスカバリー、プライベート・データのサポートが組み込まれています。

## {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版を利用する場合、何らかの支払いが発生しますか?
{: #ibp-v2-faq-cost}
{: faq}

ベータ版は無料です。ただし、無料のクラスターを選択していない場合は {{site.data.keyword.cloud_notm}} Kubernetes クラスターの料金が発生することがあります。しかし、無料のクラスターは {{site.data.keyword.cloud_notm}} Kubernetes サービス・ポリシーに基づき 30 日後に自動的に削除される点と、パフォーマンスや容量が限定されている点にご注意ください。30 日経過後もサービスの継続を希望する場合は、Kubernetes サービスの標準インスタンスに対する支払いが発生します。

## 既存の {{site.data.keyword.cloud_notm}} Kubernetes Service クラスターを使用できますか?
{: #ibp-v2-faq-existing-cluster}
{: faq}

既存の Kubernetes クラスターが以下の条件を満たしている限り、そのクラスターは {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版で使用できます。
- {{site.data.keyword.cloud_notm}} ダラス地域にある。
- Kubernetes v1.11 以降を実行している。[クラスターの Kubernetes バージョンの更新](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)方法に関する情報については、このリンクを参照してください。
- クラスター内に使用できるリソースが十分ある。詳しくは、[リソースの最小要件](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required)のトピックを参照してください。

## ピアはどのデータベースを台帳用に使用しますか?
{: #ibp-v2-faq-couchDB}
{: faq}

 {{site.data.keyword.blockchainfull_notm}} Platform  2.0 無料ベータ版でデプロイされるすべてのピアでは、CouchDB データベースが使用されます。ただし、直接 CouchDB を照会することはできません。

## スマート・コントラクトの言語として、どの言語がサポートされていますか?
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform は、Go、Node.js、Java で作成されたスマート・コントラクトをサポートしています。新しい Hyperledger Fabric プログラミング・モデルは、現在は Node.js のみサポートしており、それ以外は近日サポートされます。既存のアプリケーション・コードを保持したい場合や、Node.js 以外の言語用の Fabric SDK を使用したい場合には、以前と変わらず低水準の Fabric SDK API を使用して {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版ネットワークに接続できます。

## V2.0 ベータ版から v2.0 GA にマイグレーションできますか? 
{: #ibp-v2-faq-migrate}
{: faq}

エンタープライズ・プランのお客様は、一般提供 (GA) が開始された時点で IBM Blockchain Platform 2.0 にマイグレーションできます。しかし、2.0 ベータ版オファリングを 2.0 GA オファリングにマイグレーションすることはできません。また、スターター・プラン・インスタンスを 、2.0 ベータ版や、2.0 GA 製品 (使用可能になった時点で) にマイグレーションすることもできません。

## ロギング・サービスにはアクセスできますか? どのログが使用可能ですか?
{: #ibp-v2-faq-logs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 をご利用の場合、現在 Kubernetes ダッシュボードからピア、CA、順序付けプログラムのログに直接アクセスできます。ログをリアルタイムで簡単に構文解析できる、[{{site.data.keyword.cloud_notm}} LogDNA ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis with LogDNA") サービスの活用をお勧めします。

## ネイティブの Hyperledger Fabric よりも {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版を使用するメリットとして、どのようなものがありますか?
{: #ibp-v2-faq-native-fabric}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版を使用すると、クライアントでカスタムのブロックチェーン・ネットワークを簡単にデプロイできます。このプラットフォームには、ネットワークを即時にデプロイするための直感的なコンソールや、スマート・コントラクトを簡単にインストールしてインスタンス化する機能が含まれています。プラットフォームを使用して、{{site.data.keyword.IBM_notm}} の専門家からサポートを受けて、スマート・コントラクトの開発やネットワークの構築に役立てることもできます。

## どうすれば {{site.data.keyword.blockchainfull_notm}} Platform ソリューションのパフォーマンス最大限に生かし、拡大することができますか?
{: #ibp-v2-faq-perf-scale}
{: faq}

ソリューションのパフォーマンスに影響を与える要因や、ブロックチェーンを拡張する方法に関する情報については、こちらの [一連のブログ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Answering your questions on Hyperledger Fabric performance and scale") を確認してください。

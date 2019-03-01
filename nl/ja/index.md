---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform の概説
{: #get-started-ibp}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform には、任意の環境にブロックチェーン・コンポーネントをデプロイできる、フルスタックのマネージド Blockchain as a Service (BaaS) オファリングが用意されています。{{site.data.keyword.cloud_notm}} 環境にも、{{site.data.keyword.cloud_notm}} Private 経由でオンプレミス環境にも、サード・パーティーのクラウド (Amazon Web Services (AWS) など) 環境にもデプロイできます。このチュートリアルでは、{{site.data.keyword.blockchainfull_notm}} Platform を使用して基本的なブロックチェーン・ネットワークをセットアップする一般的な方法について順を追って説明します。
{:shortdesc}

**重要:** {{site.data.keyword.blockchainfull_notm}} Platform オファリングを使用する前に、[特記事項](/docs/services/blockchain/needtoknow.html#disclaimer)のセクションにある技術情報とサポート情報をお読みください。


## 始めに
{: #get-started-ibp-prereqs}

あらゆるタイプのユーザーがブロックチェーンの利用を開始してアプリケーションを実稼働環境に移行できるように、{{site.data.keyword.blockchainfull_notm}} Platform にはさまざまなオファリングが用意されています。 使用する環境およびオファリングを決定する必要があります。図 1 に、各種オファリングの機能、対象者、価格、およびクラウド・プラットフォームを示します。それぞれのオファリングについて詳しくは、以下の表のオファリングをクリックしてください。

| **オファリング** | **組み込まれている機能** | **請求ポリシー** | **クラウド・プラットフォーム** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**Starter Plan**](/docs/services/blockchain/starter_plan.html#starter-plan-about) |基本サービス・レベル、開発/テスト環境の {{site.data.keyword.IBM_notm}} 管理のネットワーク | 月単位のサブスクリプション | {{site.data.keyword.cloud_notm}} |
| [**Enterprise Plan**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) |拡張サービス・レベル、エンタープライズ実稼働対応環境の {{site.data.keyword.IBM_notm}} 管理のネットワーク | 月単位のサブスクリプション | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | ユーザーの {{site.data.keyword.cloud_notm}} Kubernetes クラスターにブロックチェーン・コンポーネントをデプロイして管理できる {{site.data.keyword.blockchainfull_notm}} Platform コンソール|無料のベータ版 | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | デプロイ可能な CA、順序付けプログラム、およびピアの Helm チャート | [VPC 料金](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing)および無料の Community Edition | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | {{site.data.keyword.cloud_notm}} の外部にリモート・ピアをデプロイするための AWS クイック・スタート・テンプレート | 無料 | AWS |

*図 1. {{site.data.keyword.blockchainfull_notm}} Platform オファリング*

実動使用にスターター・プランおよび無料の 2.0 ベータ版を使用しないでください。これは、開発/テスト環境であり、実動ワークロードには適していません。
{: important}

## 手順 1: オファリングを取得する
{: #get-started-ibp-step1}

{{site.data.keyword.blockchainfull_notm}} Platform オファリングを取得するためのクラウド・アカウントまたは PPA ライセンスを持っていることを確認します。

* **スターター・プラン**、**エンタープライズ・プラン**、および **{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版**

  これらのオファリングは {{site.data.keyword.cloud_notm}} で提供されています。{{site.data.keyword.cloud_notm}} の[「カタログ」ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog "カタログ") にあります。

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  このオファリングは、デプロイ可能な Helm チャートとして提供され、有料版と無料のコミュニティー版の両方が用意されています。Enterprise Edition を[パスポート・アドバンテージ・オンライン ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/software/passportadvantage/pao_customer.html) からダウンロードするか、無料のコミュニティー版を [GitHub ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz) からダウンロードできます。

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  このオファリングは AWS で提供されています。[クイック・スタート・テンプレート ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/) を使用して、ブロックチェーン・ピアを AWS にデプロイできます。

## 手順 2: {{site.data.keyword.blockchainfull_notm}} Platform をデプロイする
{: #get-started-ibp-step2}

* **スターター・プラン**、**エンタープライズ・プラン**、および **{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版**

  {{site.data.keyword.cloud_notm}} にログインし、オファリングを使用してサービス・インスタンスを作成します。スターター・プランを使用する場合は、[サービス・インスタンスを作成](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan)するとすぐに、デフォルトの構成のブロックチェーン・ネットワークを利用できます。エンタープライズ・プランまたは {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版を使用する場合は、ウィザードに従って、ネットワークの初期構成を実行する必要があります。詳しくは、[エンタープライズ・プラン・ネットワークの作成](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network)または [{{site.data.keyword.cloud_notm}} Kubernetes サービスへの {{site.data.keyword.blockchainfull_notm}} Platform のデプロイ](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)を参照してください。

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  ネットワークをデプロイする前に、[Helm チャートを {{site.data.keyword.cloud_notm}} Private にインポート](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)する必要があります。その後、[{{site.data.keyword.blockchainfull_notm}} Platform を構成およびインストール](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-deploy-icp)することができます。

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  このオファリングは AWS で提供されています。[クイック・スタート・テンプレート ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/) を使用して、ブロックチェーン・ピアを AWS にデプロイできます。

## 次のステップ
{: #get-started-ibp-next-steps}

選択した環境に {{site.data.keyword.blockchainfull_notm}} Platform をデプロイしたら、ネットワークのコンソーシアム、ノード、チャネル、およびスマート・コントラクトをセットアップし、ネットワーク上でトランザクションを開始できます。詳しくは、この文書の左側のナビゲーションの**「方法」**セクションにあるタスクのトピックを参照してください。

## サポートについて
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}} では、{{site.data.keyword.IBM_notm}} が実装したブロックチェーン・ソリューションのさまざまなサポート・オプションを提供しています。 {{site.data.keyword.blockchainfull_notm}} Platform サポートの詳細については、[サポートのページ](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support)を参照してください。

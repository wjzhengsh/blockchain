---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# リモート・ピアについて
{: #remote-peer-overview}


***[このページは参考になりましたか。ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform リモート・ピアは、既存のブロックチェーン・ネットワークに接続した後に {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> で実行できます。{{site.data.keyword.cloud_notm}} の外部でピアを実行すると、{{site.data.keyword.cloud_notm}} 内部の既存のネットワークを利用しながら、ブロックチェーン・ネットワークの拡張や参加をより柔軟に行うことができます。リモート・ピアではプラットフォームの認証局 (CA) および順序付けサービスを利用しますが、ピアを {{site.data.keyword.cloud_notm}} 外部の他のアプリケーションとコロケーションすることができます。
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} Private (ICP) は、オンプレミス環境でプライベート・クラウドを構築するための Kubernetes ベースのプラットフォームです。ICP を使用してリモート・ピアを実行し、リモート・ピアを {{site.data.keyword.blockchainfull_notm}} Platform 上のブロックチェーン・ネットワークに接続します。ICP 向けの {{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアは、ICP のストレージ、セキュリティー、ロギング、およびサポート・サービスを利用するため、リモート・ピアをオンプレミス環境で管理することができます。ICP について詳しくは、[{{site.data.keyword.cloud_notm}} Private 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 資料") を参照してください。  

**注:** {{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアは、現在、評価および試験に適したベータ版となっています。**このベータ版は実動使用を想定したものではありません。** アクセスおよび詳細については、[ライセンスおよび料金](#remote-peer-license-pricing)を参照してください。

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

このベータ版では、{{site.data.keyword.cloud_notm}} Private (ICP) v2.1.0.3 のクラウド・プラットフォームがサポートされます。{{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークおよび ICP 内のリモート・ピア・ノードは、同じ **Fabric バージョン 1.1** で実行されている必要があります。

## 考慮事項
{: #remote-peer-limitations}

リモート・ピアは、{{site.data.keyword.blockchainfull_notm}} Platform でホストされるピアの全機能またはサポートへのアクセス権限がありません。リモート・ピアを実行する前に、以下の制限事項および制約を理解していることを確認します。
- 他のクラウド環境で実行されているリモート・ピアは、{{site.data.keyword.cloud_notm}} 上のブロックチェーン・ネットワークのネットワーク・モニターに表示されません。
- ネットワーク・モニター UI で Swagger UI を使用してリモート・ピアをアドレス指定することはできません。
- リモート・ピアの正常性モニター、セキュリティー、ロギング、およびリソース使用量を管理するのはお客様の責任です。
- リモート・ピアは Hyperledger Fabric v1.1 レベルのブロックチェーン・ネットワークにのみ接続できます。
- リモート・ピアのデータベース・タイプがブロックチェーン・ネットワークのデータベース・タイプと一致する必要があります (LevelDB または CouchDB)。
- CouchDB Fauxton インターフェースは、リモート・ピアでは使用できません。
- リモート・ピアの[ゴシップ](../glossary.html#gossip)は現在サポートされていません。


## 前提条件
{: #remote-peer-prereq}

リモート・ピアを使用するには、{{site.data.keyword.blockchainfull_notm}} Platform のスターター・プランまたはエンタープライズ・プラン・ネットワークのメンバーである組織が必要です。リモート・ピアは、{{site.data.keyword.blockchainfull_notm}} Platform のプラン・ネットワークの API エンドポイント、Hyperledger Fabric CA、および順序付けサービスを利用して作動します。ブロックチェーン・ネットワークのメンバーではない場合は、ネットワークを作成するか、ネットワークに参加する必要があります。詳しくは、[ネットワークの作成](../get_start.html#creating-a-network)または[ネットワークへの参加](../get_start.html#joining-a-network)を参照してください。


## ライセンスおよび料金
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->リモート・ピアにアクセスして ICP で実行するには、[ICP のリモート・ピアのライセンスおよび料金](remote_peer_icp.html#license-pricing-icp "ICP のリモート・ピアのライセンスおよび料金")を参照してください。リモート・ピアのベータ版のライセンスは無料です。<!--[AWS] for both platforms.-->今後、一般出荷可能 (GA) オファリングによってベータ版が置き換えられます。

ベータ版から GA への移行はサポートされていません。GA オファリングがリリースされた際にこれを使用するには、新しいリモート・ピアをダウンロードおよびインストールする必要があります。新しいリモート・ピアは、ベータ版のリモート・ピアと同じネットワークの同じチャネルに参加させることができます。

**注:** リモート・ピアを操作するには、{{site.data.keyword.blockchainfull_notm}} Platform のスターター・プランまたはエンタープライズ・プラン・ネットワークに属する組織が必要です。つまり、ユーザーまたはネットワークの別のメンバーが組織の IBM Blockchain [メンバーシップ料金](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing)を支払う必要があります。料金の支払方法について詳しくは、[支払いモード](paying_mode.html)を参照してください。  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## リモート・ピアのデプロイ
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

以下の図は、{{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアを ICP にデプロイするステップを説明しています。ICP でのリモート・ピアのデプロイ方法について詳しくは、[{{site.data.keyword.cloud_notm}} Private でのリモート・ピアのデプロイ](remote_peer_icp.html "{{site.data.keyword.cloud_notm}} Private でのリモート・ピアのデプロイ")を参照してください。

![ICP でのリモート・ピアのデプロイメント・フロー](../images/remote_peer_ICP_flow.png "ICP でのリモート・ピアのデプロイメント・フロー")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*図 1. ICP でのリモート・ピアのデプロイメント・フロー*


## リモート・ピアの操作
{: #operate-remote-peer}

リモート・ピアをデプロイした後に、ピアがネットワークにトランザクションを送信できるようにするには、いくつかの操作ステップを実行する必要があります。この操作ステップでは、チャネルへの組織の追加、チャネルへのリモート・ピアの参加、リモート・ピアでのチェーンコードのインストール、チャネルでのチェーンコードのインスタンス化、およびリモート・ピアへのアプリケーションの接続を行います。詳しくは、<!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[{{site.data.keyword.cloud_notm}}Private でのリモート・ピアの操作](remote_peer_operate_icp.html#remote-peer-operate)を参照してください。

## データの常駐
{: #data-residency}

データの常駐要件は、データの常駐場所を管理する制限です。データの常駐に関する最も一般的な要件は、IT システムで処理および保管されるすべてのお客様データを特定の国の境界内にとどめることを義務付ける、特定の国の法律に関連付けられています。同様に、政府、医療、金融サービスなど、規制の厳しい業界の企業は、すべてのデータを完全にファイアウォールの内側に保管することが求められます。また、一部の企業には、特定のタイプのデータ (典型的なものは個人情報) を機密とし、完全に企業ファイアウォールの内側に保管することを義務付ける企業ポリシーがあります。したがって、データの常駐を実現するには、ブロックチェーン・ネットワークのすべてのコンポーネントが同じ[チャネル](../glossary.html#channel)の一部であり、1 つの国の域内に常駐する必要があります。

データの常駐要件に対応するには、{{site.data.keyword.blockchainfull_notm}} Platform の基礎となっている Hyperledger Fabric アーキテクチャーを理解することが重要です。このアーキテクチャーは、順序付けサービス、認証局 (CA)、ピアという 3 つの主要コンポーネントを中核としています。ピアは順序付けサービスから順序付け状態の更新をブロックの形式で受け取り、状態および台帳を維持します。したがって、ピアと順序付けサービスには直接的な関係があります。台帳には、トランザクション・ログに含まれるすべてのキーおよびデータの最新の値が含まれます。

さらに、クライアント・アプリケーションは、[Fabric SDK](../v10_application.html#using-the-fabric-sdks) を使用してトランザクションをピアおよび順序付けサービスに送信します。これらのトランザクションには、台帳のキーと値のペアを含む[読み取り/書き込みセット ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "読み取り/書き込みセットのセマンティクス") のデータが含まれます。

業務に国内データの常駐が必要な場合、順序付けサービス、ピア・ノード、およびクライアント・アプリケーションが同じ国に常駐する必要があります。{{site.data.keyword.blockchainfull_notm}} Platform ネットワークが {{site.data.keyword.cloud_notm}} で作成されている場合、ネットワークのロケーションを選択することができます。<!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->地域とロケーションについて詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の地域とロケーション](../reference/ibp_regions.html)を参照してください。これらのいずれかの国でデータの常駐を実現するには、リモート・ピアが {{site.data.keyword.blockchainfull_notm}} Platform ネットワークのロケーションと同じ国に常駐する必要があります。

{{site.data.keyword.blockchainfull_notm}} Platform ネットワークに国をまたぐリモート・ピアおよび順序付けサービス・ノードが含まれる場合は、チャネルを使用して、データをネットワーク上のピアのサブセットに分割できます。順序付けノードは、常に、ネットワークをホストするように選択したデータ・センターに配置されます。国をまたいで順序付けプログラムを保持することはできません。ただし、ピアは、データ・センターまたは {{site.data.keyword.cloud_notm}} の外側のリモート・ロケーションに配置できます。したがって、データの常駐を実現するには、順序付けプログラムおよびすべてのピア (データ・センターに対してローカルなピアまたはリモート・ピア) が同じ国に常駐するチャネルを作成します。この方法では、順序付けプログラムおよびピアが共有するすべてのデータが 1 つの国で保管されます。順序付けプログラムおよびリモート・ピアが国をまたいで配置され、データの常駐が不要な場合は、必要に応じてその他のチャネルも存在できます。

今後、Hyperledger Fabric の新しいテクノロジーによって、[プライベート・データ・コレクション ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "プライベート・データ・コレクション") およびゼロ知識証明を使用して、さらなるデータの常駐を実現する機能が改善されます。

- プライベート・データ・コレクションによって、プライベート・データが、国内のピアなど、表示を許可されたピアにのみピアツーピアで共有されます (ゴシップ・プロトコルによって)。データはピアのプライベート・データベースに保管されます。ここでは関与しない順序付けサービスに対しては、プライベート・データは表示されません。チャネルのすべてのピアの台帳にこのデータのハッシュが書き込まれます。状態検証で使用されるハッシュは、トランザクションの証拠として機能し、監査目的で使用することができます。

- ゼロ知識証明 (ZKP) により、「証明者」は「検証者」に、機密事項自体を示すことなく、機密事項に関する知識があることを保証できます。何を知っているかを示すことなく、何かを知っていることを示してステートメントを満たす方法です。

これらのテクノロジーについて詳しくは、[Hyperledger Fabric を使用したプライベートおよび機密トランザクション ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Hyperledger Fabric を使用したプライベートおよび機密トランザクション") に関するホワイト・ペーパーを参照してください。

## サポートについて
{: #remote-peer-support}

ベータ版の {{site.data.keyword.blockchainfull_notm}} リモート・ピア・オファリングは、探索、開発、およびテストを目的としています。**このベータ版を実動に使用しないでください。**{{site.data.keyword.blockchainfull_notm}} Platform では、このベータ版をサポートしていません。リモート・ピアに関連する問題が発生した場合は、[ブロックチェーンのリソースおよびサポート・フォーラム](../v10_dashboard.html#support-forums)を参照してください。ネットワーク・モニターの**「ヘルプの利用」**画面でもサポート・リソースを確認できます。  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

{{site.data.keyword.cloud_notm}} Private に関連する問題の場合は、ICP によって、[無料のデジタル・サポートおよび Community Edition サポート ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "ICP の無料のデジタル・サポートおよび Community Edition サポート") が提供されています。

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

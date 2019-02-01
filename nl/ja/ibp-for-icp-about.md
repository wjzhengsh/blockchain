---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private について
{: #overview}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform から、Kubernetes に基づいてコンテナー化されたアプリケーションを開発および管理するためのアプリケーション・プラットフォームである、IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private (ICP) がリリースされました。これにより、ユーザーは、認証局 (CA)、順序付けプログラム、およびピアを x86、LinuxONE、および IBM Z にデプロイできます。{{site.data.keyword.blockchainfull_notm}} Platform for ICP は、Hyperledger Fabric v1.2.1 に基づいており、Kubernetes Helm チャートを使用してデプロイされます。
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private は、ICP のお客様がブロックチェーン・コンポーネントをローカル環境にデプロイするためのバンドル製品です。 Helm チャートをインポートすると、ICP カタログに {{site.data.keyword.blockchainfull_notm}} Platform タイルとして表示されます。 ICP について詳しくは、[{{site.data.keyword.cloud_notm}} Private バージョン 3.1.0 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0") の資料を参照してください。

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP の提供内容

{{site.data.keyword.blockchainfull_notm}} Platform for IBM Cloud Private (ICP) を使用すると、Hyperledger Fabric ブロックチェーンのすべての基礎コンポーネント (認証局、順序付けサービス、およびピア) をデプロイできます。 これにより、ビジネス・ニーズに応じて、さまざまなコンポーネントを柔軟にデプロイできます。 {{site.data.keyword.blockchainfull_notm}} for ICP を使用すると、組織をブロックチェーン共同事業体にバインドする順序付けサービスをデプロイおよび構成することにより、ブロックチェーン・ネットワークを開始できます。 また、ピアをデプロイし、Fabric に基づくコンポーネントを使用するその他のネットワーク (ICP を使用してクラウド間にデプロイされた {{site.data.keyword.blockchainfull_notm}} Platform ネットワークや、IBM Cloud でホストされているスターター・プラン・ネットワークおよびエンタープライズ・プラン・ネットワークなど) に参加することもできます。 Hyperledger Fabric ネットワークのビルディング・ブロックについて詳しくは、[ブロックチェーン・コンポーネントの概要](/docs/services/blockchain/blockchain_component_overview.html)を参照してください。

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP の使用に適した状況

{{site.data.keyword.cloud_notm}} 外部で {{site.data.keyword.blockchainfull_notm}} Platform コンポーネントを実行すると、ブロックチェーン・ネットワークの拡張や参加をより柔軟に行うことができます。 これは、新規メンバーが自分で選択したプラットフォームを使用しながら参加できるようにすることで、ネットワーク設立者がネットワークを拡張するのに役立ちます。 これにより、ブロックチェーン・ネットワークへの参加に関心のある組織は、ピアを既存のアプリケーションと連結したり、組織を SoR (Systems of Record、定型業務処理システム) と統合したりできるようになります。

**重要:** {{site.data.keyword.blockchainfull_notm}} Platform for ICP をデプロイするプロセスは複雑であり、Fabric について高度な専門知識があることを想定しています。 Fabric、ICP、または {{site.data.keyword.blockchainfull_notm}} Platform を使用するのが初めてで、開発環境や PoC (概念検証) の設定が目標である場合は、代わりに[スターター・プラン](/docs/services/blockchain/starter_plan.html)を使用することを検討してください。 また、すべての潜在的なデプロイメント構成が {{site.data.keyword.blockchainfull_notm}} Platform for ICP でサポートされるわけではないことにも注意してください。

このオファリングのユーザーは、独自のセキュリティーとインフラストラクチャーを管理することになります。 {{site.data.keyword.cloud_notm}} では、これらのサービスは提供されません。 開始する前に、次のセクションの[考慮事項と制限](#ibp-icp-considerations)を確認してください。

## 考慮事項と制限
{: #ibp-icp-considerations}

開始する前に、以下の**考慮事項**と**制限**を理解していることを確認します。

- コンポーネントの正常性モニター、セキュリティー、ロギング、およびリソース使用量は、ユーザーが管理する必要があります。
- 他のクラウド環境で実行されているコンポーネントは、{{site.data.keyword.cloud_notm}} 上で実行中のネットワークのネットワーク・モニターに表示されません。
- Helm チャートは、順序付けプログラム、ピア、または CA の単一インスタンスをデプロイします。
- コンポーネントのリリース名が異なる場合、複数のコンポーネントを ICP 内の単一の名前空間にデプロイできます。
- ネットワーク・モニター UI で Swagger UI を使用してコンポーネントをアドレス指定することはできません。
- 相互 TLS はサポートされていません。

**CA に関する考慮事項**
- この Helm チャートは、CA の単一インスタンスをデプロイします。 組織ごとに個別の CA を設定することがベスト・プラクティスであると考えられるため、複数の CA をデプロイすることが必要になる場合があります。 例えば、1 つの順序付けプログラムと 3 つのピアをデプロイする予定である場合、少なくとも 2 つの CA が必要になります (順序付けプログラム組織用とピア組織用にそれぞれ 1 つずつ)。
- 別の MySQL データベースを実行することもできますが、Helm チャートにはこのオプションはありません。 ただし、Helm チャートでは、CA 内に SQLite データベースをデプロイして、ユーザー当たりのエンロール数や取り消された証明書のトラッキングなど、CA のデータベースでの必須作業を処理します。

**順序付けプログラムに関する考慮事項**
- 順序付けサービスは、Hyperledger Fabric の v1.2 のすべてのコンポーネントと互換性があります。
- この Helm チャートは、SOLO 順序付けサービス (1 つの順序付けプログラム) の単一インスタンスをデプロイします。 順序付けサービスの可用性を高めるために、チャネル上に複数の SOLO 順序付けプログラムをデプロイすることはできません。 これは、SOLO 順序付けサービスが実動環境用ではなく開発環境用であると見なされる 1 つの理由です。 ただし、SOLO 順序付けサービスの複数のインスタンスを異なるネットワーク (つまり、別個の共同事業体) にデプロイできます。

**ピアに関する考慮事項**

- ピアは Fabric レベル v1.1 または v1.2.1 のブロックチェーン・ネットワークにのみ接続できます。 Hyperledger Fabric のバージョンを確認するには、ネットワーク・モニターで[「ネットワーク設定 (Network Preferences)」ウィンドウ](/docs/services/blockchain/v10_dashboard.html#network-preferences)を開きます。[手順](#starter-enterprise-network-endpoints)に従って、スターター・ネットワークまたはエンタープライズ・ネットワークからピア接続情報を取得します。
- ピアのデータベース・タイプがブロックチェーン・ネットワークのデータベース・タイプと一致する必要があります (LevelDB または CouchDB)。
- CouchDB Fauxton インターフェースは、ピアでは使用できません。
- ピアの[ゴシップ](/docs/services/blockchain/glossary.html#gossip)は現在サポートされていません。 これは、[プライベート・データ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "プライベート・データ") や [サービス・ディスカバリー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "サービス・ディスカバリー") などのゴシップに依存する Fabric 機能もサポートされていないことを意味します。

## システム前提条件
{: #prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP では、以下のオペレーティング・システムがサポートされています。
- Red Hat Enterprise Linux (RHEL) 7.3、7.4、7.5
- Ubuntu 18.04 LTS および 16.04 LTS
- SUSE Linux Enterprise Server (SLES) 12 SP3

{{site.data.keyword.blockchainfull_notm}} Platform for ICP Helm チャートは、以下のワーカー・ノードおよび補助ストレージを使用して、Ubuntu Linux 上の {{site.data.keyword.cloud_notm}} Private v3.1.0 クラスターで稼働することが検証されました。

- **LinuxONE および IBM Z**: NFS を使用する z/VM および KVM
- **x86**: GlusterFS を使用する Linux 64 ビット

## ライセンスおよび料金
{: #ibp-icp-license-pricing}
{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private は、パスポート・アドバンテージからダウンロード可能な有料のオファリングと [GitHub ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts") で入手可能な無料の Community Edition の 2 つのエディションで構成されています。

### 使用許諾条件
{: #ibp-icp-license}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private は、独自のインフラストラクチャーでブロックチェーン・ネットワークを実行するために必要なコンポーネントを提供しており、ICP アプリケーションとしてデプロイされます。 PPA にアクセスして、[Helm チャートをダウンロード](/docs/services/blockchain/howto/helm_install_icp.html)できます。 {{site.data.keyword.blockchainfull_notm}} からの技術サポートが購入内容に含まれています。

{{site.data.keyword.blockchainfull_notm}} Platform on IBM Cloud Private Community Edition は、評価および試験に適した無料のオファリングであり、ICP アプリケーションとしてデプロイされます。 実動には、Community Edition を使用しないでください。 {{site.data.keyword.blockchainfull_notm}} Platform では、Community Edition をサポートしていません。 [GitHub パッケージ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts") にアクセスし、Helm チャートをダウンロードできます。

### 料金
{: #ibp-icp-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform on ICP の料金は、使用される仮想プロセッサー・コア (VPC) の数に基づきます。 VPC は、仮想サーバーに割り当てられた仮想コアか、非パーティション化サーバーの物理プロセッサー・コアのいずれかです。 {{site.data.keyword.blockchainfull_notm}} Platform で使用可能な VPC ごとに、ライセンス交付を受けた使用権を取得する必要があります。<!-- A VPC is a unit of measurement by which a program can be licensed.-->

VPC の使用法の判別方法について詳しくは、IBM Knowledge Center の [Virtual Processor Cores (VPC) ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Virtual Processor Cores (VPC's)") に関するこの記事を参照してください。 [IBM License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) を使用して、ライセンスが必要な VPC の数を判別するために使用できるレポートを構成および作成できます。


## {{site.data.keyword.blockchainfull_notm}} Platform for ICP のインストール
{: #ibp-icp-install}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private は、ローカル ICP クラスターにインストール可能な Helm チャート・ファイルとして提供されます。 Helm チャートをインストールすると、ICP カタログで {{site.data.keyword.blockchainfull_notm}} Platform をアプリケーションとして見つけることができます。

- {{site.data.keyword.blockchainfull_notm}} Platform for ICP は、パスポート・アドバンテージによって提供されます。 [パスポート・アドバンテージ・オンライン](https://www.ibm.com/software/passportadvantage/pao_customer.html)にアクセスするには、そのためのライセンスが必要です。 購入内容には、{{site.data.keyword.blockchainfull_notm}} Platform の技術サポートが含まれています。

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition は、探索、開発、およびテスト用です。 この IBM Blockchain Platform for ICP の無料バージョンには、GitHub からアクセスできます。 **注:** {{site.data.keyword.blockchainfull_notm}} Platform では、Community Edition をサポートしていません。

Helm チャートのインストール方法に関する手順および必要な前提条件については、[{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private のインストール](/docs/services/blockchain/howto/helm_install_icp.html)を参照してください。

{{site.data.keyword.cloud_notm}} Private の新規ユーザーであり、ICP のインストールおよびデプロイに関する情報とヒントが必要な場合は、[{{site.data.keyword.cloud_notm}} Private のセットアップ](/docs/services/blockchain/ICP_setup.html)を参照してください。

{{site.data.keyword.blockchainfull_notm}} Platform for ICP をインストールした後、ネットワークの各コンポーネント個別にデプロイする必要があります。 一度に複数のコンポーネントをデプロイすることはできません。 開始する前に、ブロックチェーン・ネットワークを設立または参加するためのベスト・プラクティスについて学習するために、[{{site.data.keyword.blockchainfull_notm}} Platform for ICP デプロイメント・ガイド](/docs/services/blockchain/ibp_for_icp_deployment_guide.html)を参照してください。 その後、以下のセクションで、個別のコンポーネントをデプロイおよび操作するためのステップを確認します。

### ファイアウォールの内側での {{site.data.keyword.blockchainfull_notm}} Platform for ICP のインストール
{: #ibp-icp-firewall}

パブリック・インターネットへのアクセス権限がなくても、ファイアウォールの内側に {{site.data.keyword.blockchainfull_notm}} Platform コンポーネントをデプロイできます。 ダウンロードされた Helm チャート・パッケージには、{{site.data.keyword.blockchainfull_notm}} Platform で使用するすべての Fabric コンポーネントの Docker イメージが、デプロイメント時に DockerHub からプルされることなく含まれます。

{{site.data.keyword.blockchainfull_notm}} Platform Community Edition Helm チャート・パッケージには、必要な Fabric コンポーネントの Docker イメージは含まれません。 これは、デプロイメント時に DockerHub からこれらのイメージをダウンロードするように構成され、パブリック・インターネットへのアクセス権限がない場合には失敗します。 ただし、いくつかの追加ステップを実行して、Community Edition コンポーネントをファイアウォールの内側にデプロイできます。 詳しくは、[ファイアウォールの内側での Community Edition のインストール](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-prereqs-firewall)を参照してください。


## ICP の認証局について
{: #ibp-icp-ca}

認証局 (CA) は、ネットワーク上の ID を提供します。 CA は、複数の団体の間でトラスト・アンカーとしての役割を果たす、公証人のようなものと考えることができます。 ネットワーク内のすべてのエンティティーに、各自のデジタル ID が組み込まれた証明書が、ルート CA の署名付きで付与されます。 この証明書が、ネットワーク上で実行されるすべての署名操作と検証操作のトラスト・ルートです。 認証局およびそれらがブロックチェーン・ネットワークで果たす役割について詳しくは、[ブロックチェーン・コンポーネントの概要](/docs/services/blockchain/blockchain_component_overview.html)を参照してください。

CA は、ID を検証し、デプロイする必要があるネットワーク内の他のコンポーネントについて証明書を発行します。 そのため、他のコンポーネントをデプロイする前に、組織の CA をデプロイする必要があります。

- Helm チャートのインストール後に CA を構成およびデプロイする方法については、[{{site.data.keyword.cloud_notm}} Private への認証局のデプロイ](/docs/services/blockchain/howto/CA_deploy_icp.html)を参照してください。

- CA を使用して証明書を生成し、追加コンポーネントをデプロイするための前提条件ステップを実行する方法については、[{{site.data.keyword.cloud_notm}} Private での認証局の操作](/docs/services/blockchain/howto/CA_operate.html)を参照してください。

## ICP の順序付けプログラムについて
{: #ibp-icp-orderer}

順序付けプログラムは、ブロックチェーン・ネットワーク内のクライアント、順序付けトランザクション、およびブロードキャスト・トランザクションを認証します。 これらは、Hyperledger Fabric に基づくブロックチェーン・ネットワークの共通バインディングです。 結果として、ネットワークを設立する組織は、他の組織がそのピアをデプロイし、チャネルに参加して、ネットワーク上でトランザクションを開始する前に、「順序付けサービス」(順序付けを行うノードまたはノードの集合) をデプロイして開始する必要があります。 順序付けプログラムと、ブロックチェーン・ネットワーク内でのその役割について詳しくは、[ブロックチェーン・コンポーネントの概要](/docs/services/blockchain/blockchain_component_overview.html#orderer)を参照してください。

ブロックチェーン・ネットワークを設立する場合、順序付けプログラムをデプロイする必要があります。 順序付けプログラムをデプロイした後、他の組織を共同事業体に招待してから、共同事業体で独自のチャネルを作成できます。

- Helm チャートのインストール後に順序付けプログラムを構成およびデプロイする方法については、[ICP での順序付けプログラムのデプロイ](/docs/services/blockchain/howto/orderer_deploy_icp.html)を参照してください。

- 共同事業体の構築方法については、[ICP での順序付けプログラムの操作](/docs/services/blockchain//docs/services/blockchain/howto/orderer_operate.html)を参照してください。

## ICP のピアについて
{: #ibp-icp-peer}

ピアは、台帳およびスマート・コントラクトをホストするため、ネットワークの基本的な要素です。 スマート・コントラクトおよび台帳は、ネットワークで共有プロセスおよび共有情報をそれぞれカプセル化するために使用されます。 ピアおよびそれらがブロックチェーン・ネットワークで果たす役割について詳しくは、[ブロックチェーン・コンポーネントの概要](/docs/services/blockchain/blockchain_component_overview.html)を参照してください。

- ネットワークに参加する準備ができたら、チャネルに参加してトランザクションを承認し、チャネル台帳を保管するピアをデプロイできます。 ICP 上の他のコンポーネントに接続するピアを ICP にデプロイする方法について詳しくは、[ICP でのピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_icp.html)を参照してください。 スターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークに接続するピアを ICP にデプロイする方法について詳しくは、[スターターまたはエンタープライズに接続するピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_ibp.html)を参照してください。

- ピアをセットアップした後、トランザクションを送信し、ブロックチェーン・ネットワークからの分散台帳を読み取るには、いくつかの操作ステップを完了する必要があります。

  - ICP にデプロイされた {{site.data.keyword.blockchainfull_notm}} Platform にピアを接続する場合は、[マルチクラウド・ネットワークを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作](/docs/services/blockchain/howto/peer_operate_icp.html)を参照してください。
  - {{site.data.keyword.cloud_notm}} にデプロイされたスターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークにピアを接続する場合は、[スターター・プランまたはエンタープライズ・プランを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作](/docs/services/blockchain/howto/peer_operate_ibp.html)を参照してください。

## セキュリティーに関する考慮事項
{: #icp-security}

これらのコンポーネントは {{site.data.keyword.cloud_notm}} 外部にデプロイされるため、そのセキュリティーはユーザーが管理する必要があります。 これには、鍵管理やデータ暗号化など、セキュリティーの重要な領域が含まれます。 コンポーネントのセキュリティーを考慮する場合は、以下のトピックを確認してください。

### データ・セキュリティー
{: #icp-security-data}

{{site.data.keyword.blockchainfull_notm}} Platform のスターター・プランおよびエンタープライズ・プランでは、[対称鍵暗号 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "対称暗号方") に基づくディスク全体の暗号化を使用して、ネットワークで使用されるすべてのデータを保護します。 ご使用の環境でも同様のステップを実行して、ピア・データを保護する必要があります。

状態データベース内のデータは、LevelDB と CouchDB のどちらを使用するかに関係なく、暗号化されません。 アプリケーション・レベルの暗号化によって、状態データベースに保管されているデータを保護できます。

### データの常駐
{: #data-residency}

データの常駐要件により、すべてのブロックチェーン台帳データの処理および保管を 1 つの国 (または定義された他の境界内) にとどめることを義務付けることができます。 データの常駐の実現方法について詳しくは、[データの常駐](#icp-data-residency)を参照してください。

### 鍵管理
{: #icp-security-key-management}

鍵管理はセキュリティーの重要な側面です。 秘密鍵が漏えいしたり、失われたりすると、悪意を持つアクターがデータおよび機能にアクセスできる可能性があります。 IBM では、[ハードウェア・セキュリティー・モジュール](/docs/services/blockchain/glossary.html#hsm) (HSM) と呼ばれる物理アプライアンスを使用して、{{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プラン・ネットワークの秘密鍵を保管します。

ICP にコンポーネントをデプロイする場合は、ユーザーが秘密鍵を管理する必要があります。 {{site.data.keyword.blockchainfull_notm}} Platform によって秘密鍵が生成されますが、鍵は Platform には保管されません。 鍵が漏えいしないように、鍵を安全に保管することが重要です。 コンポーネントの秘密鍵は、ピア MSP の keystore フォルダー (コンポーネント内の `/mnt/crypto/peer/peer/msp/keystore/` ディレクトリー) にあります。 ピア内部の証明書について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理](/docs/services/blockchain/certificates.html)チュートリアルの[メンバーシップ・サービス・プロバイダー](/docs/services/blockchain/certificates.html#msp)のセクションを参照してください。

鍵エスクローを使用して、失われた秘密鍵を復旧することができます。 これは、鍵が失われる前に実行する必要があります。 秘密鍵を復旧できない場合は、認証局に新しい ID を登録して、新しい秘密鍵を取得する必要があります。 また、参加しているチャネルから signCert を削除し、置き換える必要があります。

### TLS
{: #icp-security-tls}

[Transport Layer Security![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL または TLS ハンドシェークの概要") (TLS) は、Hyperledger Fabric の信頼モデルに組み込まれています。 {{site.data.keyword.blockchainfull_notm}} Platform のすべてのスターター・コンポーネントとエンタープライズ・コンポーネントでは、TLS を使用して認証し、相互に通信します。 したがって、Platform のネットワーク・コンポーネントは、ピアとの TLS ハンドシェークを実行できる必要があります。 これに関連して、ホワイト・リストなどを使用して、クライアント・アプリからピアまでファイアウォールでパススルーできる必要があります。

<!--
You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](../v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.
-->

### メンバーシップ・サービス・プロバイダーの構成
{: #icp-security-MSP}

{{site.data.keyword.blockchainfull_notm}} Platform のコンポーネントは、メンバーシップ・サービス・プロバイダー (MSP) を介して ID を使用します。 MSP は、CA から発行された証明書をネットワークおよびチャネルの役割に関連付けます。 MSP がピアを使用する方法について詳しくは、[メンバーシップ・サービス・プロバイダー (MSP)](/docs/services/blockchain/certificates.html#msp) を参照してください。

### アプリケーション・セキュリティー
{: #icp-security-appl}

すべてのチェーンコード呼び出しは署名されているため、Fabric によってアプリケーション・セキュリティーが管理されます。 また、Fabric には ACL ベースのアプリケーション・レベル・チェックも含まれています。

## データの常駐
{: #icp-data-residency}

ブロックチェーン・ネットワークでは、処理されるデータのタイプが認識されないため、特定の種類のデータを保護するために追加のステップが必要になる場合があります。 データの常駐に関する最も一般的な要件は、IT システムで処理および保管されるすべてのデータを特定の国の中にとどめることを義務付ける、特定の国の法律に関連付けられています。 同様に、政府、医療、金融サービスなど、規制の厳しい業界の企業は、データを完全にファイアウォールの内側に保管することが求められます。 したがって、データの常駐を実現するには、ブロックチェーン・ネットワークのすべてのコンポーネントが同じ[チャネル](/docs/services/blockchain/glossary.html#channel)の一部であり、1 つの国に常駐する必要があります。

データの常駐要件に対応するには、{{site.data.keyword.blockchainfull_notm}} Platform の基礎となっている Hyperledger Fabric アーキテクチャーを理解することが重要です。 このアーキテクチャーは、順序付けサービス (順序付けプログラムで構成)、認証局 (CA)、およびピアという 3 つの主要コンポーネントを中核としています。 ピアは順序付けサービスから順序付け状態の更新をブロックの形式で受け取り、状態および台帳を維持します。 したがって、ピアと順序付けサービスには直接的な関係があります。 台帳には、トランザクション・ログに含まれるすべてのキーおよびデータの最新の値が含まれます。

さらに、クライアント・アプリケーションは、[Fabric SDK](/docs/services/blockchain/v10_application.html#using-the-fabric-sdks) を使用してトランザクションをピアおよび順序付けサービスに送信します。 これらのトランザクションには、台帳のキーと値のペアを含む[読み取り/書き込みセット ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "読み取り/書き込みセットのセマンティクス") のデータが含まれます。

データの国内常駐が要件となっている場合、順序付けプログラム、ピア、およびクライアント・アプリケーションが同じ国に常駐する必要があります。 {{site.data.keyword.blockchainfull_notm}} Platform ネットワークが {{site.data.keyword.cloud_notm}} で作成されている場合、ネットワークのロケーションを選択することができます。 <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->地域とロケーションについて詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の地域とロケーション](/docs/services/blockchain/reference/ibp_regions.html)を参照してください。

### データの常駐のユース・ケース

4 つの組織の共同事業体とともに順序付けプログラムと CA を含む {{site.data.keyword.blockchainfull_notm}} Platform ネットワークを考えてみましょう。 組織には 1 つ以上のピア・ノードがあり、4 つの組織はすべて単一チャネルの一部で、ネットワークのすべてのコンポーネントは {{site.data.keyword.blockchainfull_notm}} Platform ネットワークがデプロイされた地域に常駐します (フランクフルトなど)。 最後に、ピアと相互動作するクライアント・アプリケーションもドイツ内にあります。 

この例では、データの常駐は維持されます。

![すべてのコンポーネントが同じ国内に存在する場合のデータの常駐](images/remote_peer_data_res_1.png "すべてのコンポーネントが同じ国内に存在する場合のデータの常駐")
*図 1. すべてのコンポーネントが同じ国内に存在する場合のデータの常駐*

ここで、**分散ピア**がいずれかの組織に参加した場合の影響について考えてみましょう。 分散ピアは、ネットワークの他の部分と同じ地域に常駐することも、{{site.data.keyword.blockchainfull_notm}} Platform ネットワークの地域外の任意の場所に常駐することもできます。

-	このピアがネットワークの残り部分と同じ国にある場合は、データの常駐は維持されます。 上記の**図 1** のように、すべての台帳データはドイツ内にとどまります。
-	このピアが別の国 (米国など) に存在する場合は、ピア台帳上のデータは国境にまたがって共有されるため、データの常駐は維持されなくなります。

この問題を解決するために、**チャネル**を使用してネットワーク上のピアのサブセットに対してデータを分離できます。{{site.data.keyword.blockchainfull_notm}} Platform ネットワークに、国をまたぐ分散ピアおよび順序付けプログラムが含まれる場合、チャネルを使用して、国外のピアを含む組織から元帳データを分離できます。

**注:** 順序付けプログラムは常に、ネットワークをホストするように選択したデータ・センターの地域に配置されます。 国境にまたがる複数の順序付けプログラムを保持することはできません。ただし、ピアは、データ・センターと {{site.data.keyword.cloud_notm}} の外側のリモート・ロケーションのいずれにも配置できます。

![ピアが IBM Blockchain Platform の地域の国外に存在する場合のデータの常駐](images/remote_peer_data_res_2.png "ピアが IBM Blockchain Platform の地域の国外に存在する場合のデータの常駐")
*図 2. ピアが IBM Blockchain Platform の地域の国外に存在する場合のデータの常駐*

**図 2** では、`OrgC` および `OrgD` にデータの常駐は必要ありません。 実際、`OrgD` には現在、*米国*に常駐する `OrgD-peer1` と `OrgD-peer2` の 2 つの分散ピアが含まれています。 したがって、`OrgA`、`OrgB`、およびドイツに常駐するそれぞれのクライアント・アプリケーションとピアがチャネル `X` の元帳データを分離できるように、新規チャネル `Y` が `OrgC` および `OrgD` 用に作成されます。

{{site.data.keyword.blockchainfull_notm}} Platform ネットワーク上のデータのフローについて詳しくは、[トランザクション・フローに関する Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Transaction Flow") を参照してください。

今後、Hyperledger Fabric の新しいテクノロジーによって、[プライベート・データ・コレクション ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "プライベート・データ・コレクション") およびゼロ知識証明を使用して、さらなるデータの常駐を実現する機能が改善されます。

- プライベート・データ・コレクションによって、プライベート・データが、国内のピアなど、表示を許可されたピアにのみピアツーピアで共有されます (ゴシップ・プロトコルによって)。 データはピアのプライベート・データベースに保管されます。 順序付けサービスは、ここでは関与しません。またプライベート・データを認識することもありません。 チャネルのすべてのピアの台帳にこのデータのハッシュが書き込まれます。 状態検証で使用されるハッシュは、トランザクションの証拠として機能し、監査目的で使用することができます。 プライベート・データは、Fabric バージョン 1.2.1 上で稼働している {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークで使用できます。ただし、ICP ネットワークでは現在ゴシップを使用していないため、ICP で実行されているピアに対してプライベート・データ機能を使用することはできません。

- ゼロ知識証明 (ZKP) により、「証明者」は「検証者」に、機密事項自体を示すことなく、機密事項に関する知識があることを保証できます。

これらのテクノロジーについて詳しくは、[Hyperledger Fabric を使用したプライベートおよび機密トランザクション ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Hyperledger Fabric を使用したプライベートおよび機密トランザクション") に関するホワイト・ペーパーを参照してください。

## サポートについて
{: #ibp-icp-support}

デジタル・サポート・オファリングについて詳しくは、IBM Blockchain Platform サポートの[リソースおよびサポート・フォーラム](/docs/services/blockchain/ibmblockchain_support.html#resources)を参照してください。

### {{site.data.keyword.blockchainfull_notm}} Platform for ICP

IBM Blockchain Platform for ICP ライセンスを購入し、お客様サポートに連絡する場合は、[IBM Support Community へのアクセスおよびサポート・チケットのオープン![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "{{site.data.keyword.blockchainfull_notm}} Platform for ICP Support") に関する情報を参照してください。

### {{site.data.keyword.blockchainfull_notm}} Platform Community Edition

Community Edition は探索、開発、およびテストを目的としたものです。 IBM Blockchain Platform では、{{site.data.keyword.blockchainfull_notm}} Platform Community Edition をサポートしていません。

ブロックチェーン・コンポーネントに関連する問題が発生した場合は、無料のブロックチェーン開発者リソースとサポート・フォーラムを使用して、IBM および Fabric コミュニティーから支援を受けることができます。 詳しくは、[ブロックチェーンのリソースおよびサポート・フォーラム](/docs/services/blockchain/ibmblockchain_support.html#resources)を参照してください。

IBM Cloud Private に関連する問題の場合は、[無料デジタル・サポートと ICP が提供する有料サポート](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us)の両方をご利用できます。

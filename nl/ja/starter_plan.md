---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Starter Plan について
{: #starter-plan-about}

{{site.data.keyword.blockchainfull}} Platform スターター・プランは、組織が複数組織のブロックチェーン・ネットワークをシミュレートしたり、アプリケーションを迅速に開発したり、サンプルのスマート・コントラクトやビジネス・ネットワークを使用したりできるようにする、エントリー・レベルのオプションです。 また、他のメンバーシップ・オプションと同じ UI エクスペリエンスが提供されるので、効率良く習得できます。 2018 年 10 月 4 日より後に作成された新しいスターター・プラン・ネットワークは、Hyperledger Fabric V1.2.1 でビルドされます。 古いスターター・プラン・ネットワークは、Fabric V1.1.0 レベルのままです。
{:shortdesc}

**スターター・プラン**は、ブロックチェーン・ネットワークの開発を始めたいお客様や、ブロックチェーン・テクノロジーの学習を始めたいお客様向けの環境です。 スターター・プランを開発環境またテスト環境として使用して、小規模な環境から開始し、ネットワークのメンバーシップまたはトランザクション・ボリュームを拡張して実稼働環境に移行できます。

 スターター・プラン・ネットワークをデプロイするには、{{site.data.keyword.cloud_notm}} の[スターター・メンバーシップ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) に今すぐ登録してください。 ネットワークの開発を開始できる状態になったら、[スターター・プランの概説](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan)を参照してください。


**注:**
- {{site.data.keyword.blockchainfull_notm}} Platform スターター・プランは、開発/テスト環境であり、実動ワークロードには適していません。 実稼働環境が必要な場合は、[エンタープライズ・プランについて](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)を参照してください。
- {{site.data.keyword.blockchainfull_notm}} Platform は {{site.data.keyword.cloud_notm}} のプラットフォーム・サービスであり、すべてのメンバーシップ・オファリングはサービス・レベル・アグリーメント (SLA) に関する [{{site.data.keyword.cloud_notm}} サービスのご利用条件 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} サービスのご利用条件") に従っています。 スターター・プラン・ネットワークは、地理的に分離されたデータ・センターの**複数の環境**にまたがってプロビジョンされます。

## スターター・プランでの提供内容
{: #starter-plan-about-what-starter-plan-offers}

- **_ワンクリックで使用開始できるネットワーク_**
    スターター・プランでは、シングルクリックによるライブ・ブロックチェーン・ネットワークがプロビジョンされます。 各ネットワークには CA、順序付けプログラム、2 つの組織 (組織ごとに 1 つのピアを含む)、およびチェーンコードのトランザクション処理とデプロイを行うデフォルトのチャネルが付随しています。 {{site.data.keyword.blockchainfull_notm}} Platform では、このネットワークの作成と構成が処理されるため (稼働中になった後に更新できます)、ユーザーは開発に集中することができます。 スターター・プラン・ネットワークは Fabric V1.2 上に構築されており、CouchDB を状態データベースとして使用します。
- **_コスト効率_**
     Starter Plan メンバーシップ・オプションでは、エンタープライズ・プラン・メンバーシップ・オプションと同じさまざまなブロックチェーン機能を低コストで使用できます。 詳しくは、[スターター・プランの料金](/docs/services/blockchain/howto/pricing.html#ibp-pricing-starter-pricing)を参照してください。
- **_複数組織ネットワークのシミュレーション_**
    Starter Plan を使用して、複数の組織とネットワークを構築する作業をシミュレートできます。 ネットワークに他の組織を実際に招待する必要はなく、自分自身が他の組織の役割を果たすことができます。 このメカニズムにより、どのようにすれば新しい組織がネットワークに参加できるのか、複数の組織がネットワーク内でどのように連携するのかなどを知ることができます。 ネットワーク・モニターで組織を切り替えることで、異なる組織の視点からネットワークを調べて管理できます。

{{site.data.keyword.IBM_notm}} では、実動の Hyperledger Composer を使用するネットワークをサポートしていません。これには Composer CLI、JavaScript API、REST サーバー、および Web Playground が含まれます。
{:note}

## スターター・プランの使用に適した状況
{: #starter-plan-about-is-starter-suitable-for-you}

以下のいずれかの状況に当てはまれば、ブロックチェーンの学習を始めるには Starter Plan が適しています。
- **_{{site.data.keyword.blockchainfull_notm}} Platform について学習する。_**
    {{site.data.keyword.blockchainfull_notm}} Platform に興味がある場合も、ブロックチェーンに初めて触れる場合も、Starter Plan は実際のブロックチェーン・ネットワークの開発と管理の方法を学ぶのに非常に適した手段になります。 実稼働ネットワークを使用する場合と同じように、ネットワークを構成するコンポーネントを検出し、メンバーシップを管理し、チェーンコード (「スマート・コントラクト」とも呼ばれる) をデプロイして管理する方法、チャネルを追加 (およびチャネル・アクセス権を管理) する方法、新規ブロックが生成されるときに追跡する方法を学ぶことができます。
- **_ライブ・ネットワークでデモ・ソリューションを構築する。_**
    スターター・プランでは、ブロックチェーン・アプリケーションのデモを行うための強力な環境を提供します。 ブロックチェーン・ネットワークをすぐに使用できるので、ネットワーク・モニターに用意されている運用ツールや管理ツールで、同僚、管理職、パートナーに対して迅速にプレゼンテーションを行えます。
- **_1 つの組織開発を越えて先に進む。_**
    Starter Plan では、複数の組織の役割を果たすことができます。これにより、チャネルの作成やチェーンコードのインスタンス化のような共同作業を {{site.data.keyword.blockchainfull_notm}} Platform がどのように管理するかが分かるだけでなく、アプリケーションのテストやトランザクションの実行についても知ることができます。 また他のユーザーを招待して、スターター・プラン・ネットワークで共同作業を行うこともできます (Enterprise プランの場合と同様)。 これにより、チェーンコードとトランザクションを承認するパーティーが複数存在する、より現実的な環境で構築することができます。
- **_ブロックチェーン・アプリケーションを反復的に開発してテストする。_**
    Starter Plan は、ブロックチェーン・ネットワーク上で継続的にコードを開発してテストするためのステージング領域を提供します。 クラウドに移動することにより、開発者とテスターは機能に集中し、単体テストから機能テストに簡単に進むことができます。 Starter Plan には、エンタープライズ・プランと同じブロックチェーン・ネットワーク機能とともに、運用や管理のための同じツールも用意されています。 いずれかの Enterprise プランにプロジェクトをプッシュする準備ができたら、 Starter Plan と同じように運用しながら、ネットワークを拡張する機会を増やすことができます。

## Starter Plan に関する考慮事項
{: #starter-plan-about-considerations}

Starter Plan は {{site.data.keyword.blockchainfull_notm}} Platform へのエントリー・ポイントであり、開発とテストのためのものです。  Starter Plan を使用する前に、以下の項目を確認してください。

- **{{site.data.keyword.cloud_notm}} アカウント要件**
    **従量課金 (PAYG)** タイプなどの {{site.data.keyword.cloud_notm}} アカウントがある必要があります。 {{site.data.keyword.blockchainfull_notm}} Platform に用意されているすべてのメンバーシップ・プランは、{{site.data.keyword.cloud_notm}} 有料アカウントを必要とします。 アカウントを従量課金タイプにアップグレードするには、{{site.data.keyword.cloud_notm}} コンソールで**「管理」** > **「請求および使用量」** > **「請求処理」**に移動し、**「クレジット・カードの追加」**をクリックします。
- **エンタープライズ・プランとの相違点**
    - [CA](/docs/services/blockchain/glossary.html#glossary-CA) と順序付けサービスはフォールト・トレラントではありません。各組織の CA は 1 つだけであり、ネットワークの[順序付けサービス](/docs/services/blockchain/glossary.html#glossary-orderer)も 1 つだけだからです。
    - 順序付けサービスは [SOLO](/docs/services/blockchain/glossary.html#glossary-solo) [コンセンサス](/docs/services/blockchain/glossary.html#glossary-consensus)のみを使用します。 スターター・プラン・ネットワークは 1 つの順序付けサービスだけで構成され、その順序付けサービスがすべてのピアに対してコンセンサスを得るための処理を実行します。
    - 厳密な認証と暗号処理のためのデジタル鍵の保護と管理には、[ハードウェア・セキュリティー・モジュール (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) を使用することはできません。
- **スターター・プランのバージョンおよびアップグレード**
    - 2018 年 10 月 4 日より後に作成された新しいスターター・プラン・ネットワークは、Hyperledger Fabric V1.2.1 でビルドされます。 古いスターター・プラン・ネットワークは、Fabric V1.1.0 レベルのままです。
    - 古いスターター・プラン・ネットワークに追加された新しいピアは、Fabric v1.2.1 でビルドされます。 後方互換性があるため、ネットワークのパフォーマンスに影響はありません。
    - チャネルを作成または更新する場合は、より高度な[チャネル構成 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "チャネル構成") 設定および[アクセス制御リスト ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-v1.2/access_control.html "アクセス制御リスト") を使用できます。
    - Hyperledger Fabric v1.2 の [サービス・ディスカバリー ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "service discovery") および [プライベート・データ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "private data") 機能は、スターター・プランではサポートされません。
    - Fabric V1.1.0 の古いスターター・プラン・ネットワークを[リセット](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network)すると、新規ネットワークは Fabric V1.2 レベルになります。 ネットワークをリセットする場合は、チェーンコードまたは .bna ファイルを新規ネットワークにインストールし、古いネットワークのメンバーを再招待する必要があります。
- **ネットワーク・リソースの制限**
    スターター・プランにより、ピアごとに 1 CPU と 4 Gi RAM が割り当てられ、ピアを含む {{site.data.keyword.cloud_notm}} サービス・インスタンスごとに 20 Gi ストレージが割り当てられます。 チェーンコード・コンテナーと台帳のブロックは、最もリソースを消費するネットワーク・コンポーネントです。 ネットワーク内に多くのピアを持ち、多くのブロックを生成したり大規模なチェーンコード・ファイルを使用したりするユーザーは、パフォーマンスでリソースの制限を経験する場合があります。 ストレージのネットワーク使用量は、[ネットワーク・モニターの「概説」画面](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-storage)で確認できます。
- **保守とアップグレード**
    Starter Plan の保守とネットワーク更新は、固定スケジュールで行われます。 保守期間中は、新しいネットワークのプロビジョニングは行えません。また、ネットワーク中断が短期間発生する可能性もあります。
- **データ保存**
    スターター・プランでは、リリース・アップグレードでのデータ保存は保証されません。
- **マイグレーションに関する考慮事項**
    スターター・プラン・ネットワークから他のメンバーシップ・プランへのデータのマイグレーションは、現在サポートされていません。 ただし、Starter Plan でテストされた `.bna` ファイル、チェーンコード、アプリケーションをマイグレーションすることは可能です。 詳しくは、[スターター・プランからエンタープライズ・プランへのマイグレーション](/docs/services/blockchain/howto/migrate_sp_ep.html#migrate_starter_to_enterprise)を参照してください。

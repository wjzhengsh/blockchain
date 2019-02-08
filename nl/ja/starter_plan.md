---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Starter Plan について
{: #overview}


***[このページは参考になりましたか。ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform スターター・プランは、組織が複数組織のブロックチェーン・ネットワークをシミュレートしたり、アプリケーションを迅速に開発したり、サンプルのスマート・コントラクトやビジネス・ネットワークを使用したりできるようにする、エントリー・レベルのオプションです。 また、他のメンバーシップ・オプションと同じ UI エクスペリエンスが提供されるので、効率良く習得できます。 スターター・プラン・ネットワークは Hyperledger Fabric V1.1 上に構築されます。
{:shortdesc}

**注:**
- {{site.data.keyword.blockchainfull_notm}} Platform スターター・プランは、開発/テスト環境であり、実動ワークロードには適していません。 実稼働環境が必要な場合は、[エンタープライズ・プランについて](/docs/services/blockchain/enterprise_plan.html)を参照してください。 Starter Plan を使用する前に、[Starter Plan に関する考慮事項](#considerations)を確認することをお勧めします。  
- {{site.data.keyword.blockchainfull_notm}} Platform は {{site.data.keyword.cloud_notm}} のプラットフォーム・サービスであり、すべてのメンバーシップ・オファリングはサービス・レベル・アグリーメント (SLA) に関する [{{site.data.keyword.cloud_notm}} サービスのご利用条件 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} サービスのご利用条件") に従っています。スターター・プラン・ネットワークは、地理的に分離されたデータ・センターの**複数の環境**にまたがってプロビジョンされます。

## スターター・プランでの提供内容

- **_ワンクリックで使用開始できるネットワーク_**  
    スターター・プランでは、シングルクリックによるライブ・ブロックチェーン・ネットワークがプロビジョンされます。 各ネットワークには順序付けサービス、CA、2 つの組織 (組織ごとに 1 つのピアを含む)、およびチェーンコードのトランザクション処理とデプロイを行うデフォルトのチャネルが付随しています。 {{site.data.keyword.blockchainfull_notm}} Platform では、このネットワークの作成と構成が処理されるため (稼働中になった後に更新できます)、ユーザーは開発に集中することができます。 スターター・プラン・ネットワークは Hyperledger Fabric V1.1 上に構築されており、CouchDB を状態データベースとして使用します。<!--The free trial provides you up to two organizations and two peers.-->
- **_コスト効率_**  
    Starter Plan メンバーシップ・オプションでは、エンタープライズ・プラン・メンバーシップ・オプションと同じさまざまなブロックチェーン機能を低コストで使用できます。 {{site.data.keyword.blockchainfull_notm}} Platform では、スターター・プランの新規ユーザーに対してクラウド・クレジットの $500 が提供されます。このクレジットを利用すると、1 カ月間無料で基本ネットワーク・リソースを使用してブロックチェーン・ネットワークをプロビジョンすることができます。詳しくは、[Starter Plan に関する考慮事項](#starter-plan-considerations)を参照してください。
- **_複数組織ネットワークのシミュレーション_**  
    Starter Plan を使用して、複数の組織とネットワークを構築する作業をシミュレートできます。 ネットワークに他の組織を実際に招待する必要はなく、自分自身が他の組織の役割を果たすことができます。 このメカニズムにより、どのようにすれば新しい組織がネットワークに参加できるのか、複数の組織がネットワーク内でどのように連携するのかなどを知ることができます。 ネットワーク・モニターで組織を切り替えることで、異なる組織の視点からネットワークを調べて管理できます。  
    **注**: 無料トライアルの基本リソースである 2 つの組織と 2 つのピアより多くのリソースを追加すると、追加のコストが発生する可能性があります。
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html). -->
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop アプリケーション統合_**  
    スターター・プランでは、[{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login) で開発したビジネス・ネットワークをデプロイすることができます。 詳しくは、[スターター・プランでのビジネス・ネットワークのデプロイ](/docs/services/blockchain/develop_starter.html)を参照してください。

## スターター・プランの使用に適した状況

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
{: #considerations}

Starter Plan は {{site.data.keyword.blockchainfull_notm}} Platform へのエントリー・ポイントであり、開発とテストのためのものです。  Starter Plan を使用する前に、以下の項目を確認してください。

- **{{site.data.keyword.cloud_notm}} アカウント要件**  	
    **従量課金 (PAYG)** タイプなどの {{site.data.keyword.cloud_notm}} アカウントがある必要があります。 {{site.data.keyword.blockchainfull_notm}} Platform に用意されているすべてのメンバーシップ・プランは、{{site.data.keyword.cloud_notm}} 有料アカウントを必要とします。 アカウントを従量課金タイプにアップグレードするには、{{site.data.keyword.cloud_notm}} コンソールで**「管理」** > **「請求および使用量」** > **「請求処理」**に移動し、**「クレジット・カードの追加」**をクリックします。  
- **{{site.data.keyword.blockchainfull_notm}} Platform クラウド・クレジット**  
    {{site.data.keyword.blockchainfull_notm}} Platform では、スターター・プランの新規ユーザーに対してクラウド・クレジットの $500 が提供されます。このクレジットによって、デフォルト・ネットワーク構成の 1 カ月のコストがカバーされるため、ユーザーは開始が容易になります。
    - 新規ユーザーは、クラウド・クレジットに登録する必要があります。自動的には適用されません。まだ登録していない場合は、[登録 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "登録") してクレジットの請求を行ってください。24 時間待機してクレジットが確実に[アカウント ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "アカウント") に含まれることを確認してから、スターター・プランにアクセスしてください。そうでない場合、クレジットが適用される前に課金されることがあります。
    - クラウド・クレジットはすべての {{site.data.keyword.cloud_notm}} サービスに適用されます。{{site.data.keyword.cloud_notm}} で使用する {{site.data.keyword.blockchainfull_notm}} Platform 以外の製品で消費されることがあります。
    - このオファーでは、ライフタイム・クレジットが提供されます。クレジットは、{{site.data.keyword.cloud_notm}} アカウントを維持している限り保持できます。最初の 1 カ月で使用する必要はありません。
    トライアル・クレジットの使用について詳しくは、[料金ガイド](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing)を参照してください。
- **エンタープライズ・プランとの相違点**
    - [CA](/docs/services/blockchain/glossary.html#ca) と[順序付けサービス](/docs/services/blockchain/glossary.html#orderer)はフォールト・トレラントではありません。各組織の CA は 1 つだけであり、ネットワークの[順序付けサービス](/docs/services/blockchain/glossary.html#orderer)も 1 つだけだからです。
    - 順序付けサービスは [SOLO](/docs/services/blockchain/glossary.html#solo) [コンセンサス](/docs/services/blockchain/glossary.html#consensus)のみを使用します。 スターター・プラン・ネットワークは 1 つの[順序付けサービス](/docs/services/blockchain/glossary.html#orderer)だけで構成され、その順序付けサービスがすべてのピアに対してコンセンサスを得るための処理を実行します。
    - 厳密な認証と暗号処理のためのデジタル鍵の保護と管理には、[ハードウェア・セキュリティー・モジュール (HSM)](/docs/services/blockchain/glossary.html#hsm) を使用することはできません。
- **ネットワーク・リソースの制限**  
    スターター・プランにより、ピアごとに 1 CPU と 4 Gi RAM が割り当てられ、ピアを含む {{site.data.keyword.cloud_notm}} サービス・インスタンスごとに 20 Gi ストレージが割り当てられます。チェーンコード・コンテナーと台帳のブロックは、最もリソースを消費するネットワーク・コンポーネントです。ネットワーク内に多くのピアを持ち、多くのブロックを生成したり大規模なチェーンコード・ファイルを使用したりするユーザーは、パフォーマンスでリソースの制限を経験する場合があります。
- **保守とアップグレード**  
    Starter Plan の保守とネットワーク更新は、固定スケジュールで行われます。 保守期間中は、新しいネットワークのプロビジョニングは行えません。また、ネットワーク中断が短期間発生する可能性もあります。
- **データ保存**  
    スターター・プランでは、リリース・アップグレードでのデータ保存は保証されません。
- **マイグレーションに関する考慮事項**  
    スターター・プラン・ネットワークから他のメンバーシップ・プランへのデータのマイグレーションは、現在サポートされていません。 ただし、Starter Plan でテストされた `.bna` ファイル、チェーンコード、アプリケーションをマイグレーションすることは可能です。 詳しくは、[スターター・プランからエンタープライズ・プランへのマイグレーション](/docs/services/blockchain/howto/migrate_sp_ep.html)を参照してください。

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](/docs/services/blockchain/knownissues.html/update-chaincode-fabric11).
-->

<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](/docs/services/blockchain/develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](/docs/services/blockchain/v10_application.html#adding-network-api-endpoints-to-your-application).
-->

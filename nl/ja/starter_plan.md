---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Starter Plan について
{: #overview}

{{site.data.keyword.blockchainfull}} Platform スターター・プランは、組織が複数組織のブロックチェーン・ネットワークをシミュレートしたり、アプリケーションを迅速に開発したり、サンプルのスマート・コントラクトやビジネス・ネットワークを使用したりできるようにする、エントリー・レベルのオプションです。また、他のメンバーシップ・オプションと同じ UI エクスペリエンスが提供されるので、効率良く習得できます。 スターター・プラン・ネットワークは Hyperledger Fabric V1.1 上に構築されます。
{:shortdesc}

**注意**:
1. {{site.data.keyword.blockchainfull_notm}} Platform スターター・プランは、開発/テスト環境であり、実動ワークロードには適していません。 実稼働環境が必要な場合は、[エンタープライズ・プランについて](enterprise_plan.html)を参照してください。 Starter Plan を使用する前に、[Starter Plan に関する考慮事項](#considerations)を確認することをお勧めします。
2. スターター・プランは 2018 年 6 月 14 日にベータ版から GA に移行しました。ベータ版のネットワークを使用している場合は、[マイグレーションに関する考慮事項](#beta-to-ga)を参照してください。

今すぐ [{{site.data.keyword.blockchainfull_notm}} メンバーシップ ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) に登録し、[トライアル・クレジット](howto/pricing.html#starter-plan-pricing)が含まれるスターター・プランをお試しください。<!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## スターター・プランでの提供内容

- **_ワンクリックで使用開始できるネットワーク_**  
    スターター・プランでは、シングルクリックによるライブ・ブロックチェーン・ネットワークがプロビジョンされます。各ネットワークには順序付けサービス、CA、2 つの組織 (組織ごとに 1 つのピアを含む)、およびチェーンコードのトランザクション処理とデプロイを行うデフォルトのチャネルが付随しています。{{site.data.keyword.blockchainfull_notm}} Platform では、このネットワークの作成と構成が処理されるため (稼働中になった後に更新できます)、ユーザーは開発に集中することができます。スターター・プラン・ネットワークは Hyperledger Fabric V1.1 上に構築されており、CouchDB を台帳データベースとして使用します。<!--The free trial provides you up to two organizations and two peers.-->
- **_コスト効率_**  
    Starter Plan メンバーシップ・オプションでは、Enterprise Plan メンバーシップ・オプションと同じさまざまなブロックチェーン機能を低コストで使用できます。 スターター・プランの試用期間中は、無料で基本ネットワーク・リソースを使用してブロックチェーン・ネットワークをプロビジョンすることができます。
- **_複数組織ネットワークのシミュレーション_**  
    Starter Plan を使用して、複数の組織とネットワークを構築する作業をシミュレートできます。 ネットワークに他の組織を実際に招待する必要はなく、自分自身が他の組織の役割を果たすことができます。 このメカニズムにより、どのようにすれば新しい組織がネットワークに参加できるのか、複数の組織がネットワーク内でどのように連携するのかなどを知ることができます。 ネットワーク・モニターで組織を切り替えることで、異なる組織の視点からネットワークを調べて管理できます。
    **注**: 無料トライアルの基本リソースである 2 つの組織と 2 つのピアより多くのリソースを追加すると、追加のコストが発生する可能性があります。
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop アプリケーション統合_**  
    スターター・プランでは、[{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login) で開発したビジネス・ネットワークをデプロイすることができます。詳しくは、[IBP: Develop アプリケーションのインポート](link)を参照してください。

## 対象者

以下のいずれかの状況に当てはまれば、ブロックチェーンの学習を始めるには Starter Plan が適しています。
- **_{{site.data.keyword.blockchainfull_notm}} Platform について学習する。_**  
    {{site.data.keyword.blockchainfull_notm}} Platform に興味がある場合も、ブロックチェーンに初めて触れる場合も、Starter Plan は実際のブロックチェーン・ネットワークの開発と管理の方法を学ぶのに非常に適した手段になります。 実稼働ネットワークを使用する場合と同じように、ネットワークを構成するコンポーネントを検出し、メンバーシップを管理し、チェーンコード (「スマート・コントラクト」とも呼ばれる) をデプロイして管理する方法、チャネルを追加 (およびチャネル・アクセス権を管理) する方法、新規ブロックが生成されるときに追跡する方法を学ぶことができます。
- **_ライブ・ネットワークでデモ・ソリューションを構築する。_**  
    スターター・プランでは、ブロックチェーン・アプリケーションのデモを行うための強力な環境を提供します。ブロックチェーン・ネットワークをすぐに使用できるので、ネットワーク・モニターに用意されている運用ツールや管理ツールで、同僚、管理職、パートナーに対して迅速にプレゼンテーションを行えます。
- **_1 つの組織開発を越えて先に進む。_**  
    Starter Plan では、複数の組織の役割を果たすことができます。これにより、チャネルの作成やチェーンコードのインスタンス化のような共同作業を IBM Blockchain Platform がどのように管理するかが分かるだけでなく、アプリケーションのテストやトランザクションの実行についても知ることができます。 また他のユーザーを招待して、スターター・プラン・ネットワークで共同作業を行うこともできます (Enterprise プランの場合と同様)。 これにより、チェーンコードとトランザクションを承認するパーティーが複数存在する、より現実的な環境で構築することができます。
- **_ブロックチェーン・アプリケーションを反復的に開発してテストする。_**  
    Starter Plan は、ブロックチェーン・ネットワーク上で継続的にコードを開発してテストするためのステージング領域を提供します。 クラウドに移動することにより、開発者とテスターは機能に集中し、単体テストから機能テストに簡単に進むことができます。Starter Plan には、Enterprise Plan と同じブロックチェーン・ネットワーク機能とともに、運用や管理のための同じツールも用意されています。 いずれかの Enterprise プランにプロジェクトをプッシュする準備ができたら、 Starter Plan と同じように運用しながら、ネットワークを拡張する機会を増やすことができます。


## Starter Plan に関する考慮事項
{: #considerations}

Starter Plan は {{site.data.keyword.blockchainfull_notm}} Platform へのエントリー・ポイントであり、開発とテストのためのものです。  Starter Plan を使用する前に、以下の項目を確認してください。

- **{{site.data.keyword.cloud_notm}} アカウント要件**  	
    **従量課金 (PAYG)** タイプなどの {{site.data.keyword.cloud_notm}} アカウントがある必要があります。{{site.data.keyword.blockchainfull_notm}} Platform に用意されているすべてのメンバーシップ・プランは、{{site.data.keyword.cloud_notm}} 有料アカウントを必要とします。 アカウントを従量課金タイプにアップグレードするには、{{site.data.keyword.cloud_notm}} コンソールで**「管理」** > **「請求および使用量」** > **「請求処理」**に移動し、**「クレジット・カードの追加」**をクリックします。  
- **Enterprise Plan との相違点**
    - [CA](glossary.html#ca) と[順序付けサービス](glossary.html#orderer)はフォールト・トレラントではありません。各組織の CA は 1 つだけであり、ネットワークの[順序付けサービス](glossary.html#orderer)も 1 つだけだからです。
    - 順序付けサービスは [SOLO](glossary.html#SOLO) [コンセンサス](glossary.html#consensus)のみを使用します。 スターター・プラン・ネットワークは 1 つの[順序付けサービス](glossary.html#orderer)だけで構成され、その順序付けサービスがすべてのピアに対してコンセンサスを得るための処理を実行します。
    - 厳密な認証と暗号処理のためのデジタル鍵の保護と管理には、[ハードウェア・セキュリティー・モジュール (HSM)](glossary.html#hsm) を使用することはできません。
- **ネットワーク・リソースの制限**  
    スターター・プランにより、ピアごとに 1 CPU と 2 Gi RAM が割り当てられ、{{site.data.keyword.cloud_notm}} サービス・インスタンスごとに 20 Gi ストレージが割り当てられます。 
- **非アクティブ・ネットワークの削除**  
    スターター・プラン・ネットワークを作成した後、15 日間そのネットワークにアクセスすることも、トランザクションを発行することもなかった場合、{{site.data.keyword.blockchainfull_notm}} Platform はそのネットワークを削除します。
    - ネットワークが不要になった場合にユーザーが取るべきアクションはありません。ネットワークは自動的に削除されます。
    - ネットワークを使用し続ける必要がある場合は、ネットワーク上でトランザクションを発行することでネットワークをアクティブに維持します。 スターター・プラン・ネットワークは [{{site.data.keyword.cloud_notm}} ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/apps/){:new_window} に表示されます。 定期的に発行するトランザクションがない場合は、[説明](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan)に従ってサンプル・アプリケーションを試してください。
- **保守とアップグレード**
    Starter Plan の保守とネットワーク更新は、固定スケジュールで行われます。 保守期間中は、新しいネットワークのプロビジョニングは行えません。また、ネットワーク中断が短期間発生する可能性もあります。
- **データ保存**
    Starter Plan では、ベータ版から GA への移行を含め、リリース・アップグレードでのデータ保存は保証されません。
- **マイグレーションに関する考慮事項**
    - スターター・プラン・ネットワークから他のメンバーシップ・プランへのデータのマイグレーションは、現在サポートされていません。 ただし、Starter Plan でテストされた `.bna` ファイル、チェーンコード、アプリケーションをマイグレーションすることは可能です。 詳しくは、[スターター・プランからエンタープライズ・プランへのマイグレーション](get_start_starter_plan.html#migrate)を参照してください。

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## ベータ版から GA へのマイグレーション
{: #beta-to-ga}

スターター・プランは 2018 年 6 月 14 日に GA ステージに移行します。GA への移行時に、IBM Blockchain Platform では、スターター・プランでブロックチェーン・ネットワークを作成するために、IBM Cloud アカウントごとに ＄500 のトライアル・クレジットを提供します。<! ※TCT確認済_BM130AB#493※ !><! ※TCT確認済_BM130AB#493※ !>トライアル・クレジットについて詳しくは、[スターター・プランの料金](howto/pricing.html#starter-plan-pricing)の『*スターター・プラン・トライアル*』セクションを参照してください。**従量課金 (PAYG)** タイプなど、支払い済みの IBM Cloud アカウントを持っていることを確認します。
. <! ※TCT確認済_BM130AB#493※ !>

スターター・プラン・ベータ版を使用して作成されたすべてのブロックチェーン・ネットワークは、スターター・プラン GA 後 **30 日**で削除されるまで**無料**のままです。スターター・プラン・ベータ版のネットワークから GA のネットワークへのデータ・マイグレーションはサポートされていません。**ベータ版のネットワークでのデータは失われます。**ただし、チェーンコード、ビジネス・ネットワーク、およびアプリケーションを手動でマイグレーションすることができます。
- ベータ版のネットワークで実行中のチェーンコードがある場合は、GA のネットワークにチェーンコードをインストールしてインスタンス化します。詳しくは、[チェーンコードのインストール、インスタンス化、および更新](howto/install_instantiate_chaincode.html)を参照してください。
- ベータ版のネットワークにビジネス・ネットワークをデプロイした場合は、GA のネットワークに `.bna` ファイルとともにビジネス・ネットワークをデプロイします。詳しくは、[スターター・プランでのビジネス・ネットワークのデプロイ](develop_starter.html)を参照してください。
- ベータ版のネットワークに対して自身で開発したアプリケーションを実行した場合は、アプリケーション内の API エンドポイントを、GA のネットワーク・ノードを指すように更新します。詳しくは、[アプリケーションへの API エンドポイントの追加](v10_application.html#adding-network-api-endpoints-to-your-application)を参照してください。

---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Starter Plan について
{: #overview}

{{site.data.keyword.blockchainfull}} Platform はエントリー・レベルのオプションであり、このオプションを選択した組織は、複数組織のブロックチェーン・ネットワークをシミュレートし、アプリケーションを迅速に開発し、提供されるサンプルを使用して作業できます。また、他のメンバーシップ・オプションと同じ UI エクスペリエンスが提供されるので、効率良く習得できます。
{:shortdesc}

**注**: {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan は、開発とテストの環境です。実稼働環境が必要な場合は、[エンタープライズ・プランについて](enterprise_plan.html)を参照してください。

今すぐ [{{site.data.keyword.blockchainfull_notm}} メンバーシップ ![外部リンク・アイコン ](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) に登録して、Starter Plan をお試しください。Starter Plan ベータ版を使用してブロックチェーン・ネットワークを作成するには、{{site.data.keyword.cloud_notm}} のリージョンとして**「US South」**を選択する必要があります。


## 対象者

以下のいずれかの状況に当てはまれば、ブロックチェーンの学習を始めるには Starter Plan が適しています。
- **_{{site.data.keyword.blockchainfull_notm}} Platform を学習中である。_**  
    {{site.data.keyword.blockchainfull_notm}} Platform に興味がある場合も、ブロックチェーンに初めて触れる場合も、Starter Plan は実際のブロックチェーン・ネットワークの開発と管理の方法を学ぶのに非常に適した手段になります。実稼働ネットワークを使用する場合と同じように、ネットワークを構成するコンポーネントを理解し、チェーンコード (「スマート・コントラクト」とも呼ばれる) をデプロイして管理する方法や、チャネルを追加 (およびチャネル・アクセス権を管理) する方法、新しいブロックが生成されたときに追跡する方法を学ぶことができます。 
- **_複数の組織の役割を果たしてネットワーク開発を促進したい。_**  
    Starter Plan では、複数の組織の役割を果たすことができます。これにより、チャネルの作成やチェーンコードのインスタンス化のような共同作業を IBM Blockchain Platform (IBP) がどのように管理するかが分かるだけでなく、アプリケーションのテストやトランザクションの実行についても知ることができます。また他のユーザーを招待して、Starter Plan ネットワークで共同作業を行うこともできます (Enterprise プランの場合と同様)。
- **_ライブ・ネットワークでデモ・ソリューションを構築中である。_**  
    Starter Plan は、ネットワーク定義をテストしたり ({{site.data.keyword.blockchainfull_notm}} Platform: Develop を使用して作成された `.bna` ファイルを統合してテスト)、ブロックチェーン・アプリケーションをテストしたりするための強力な環境を提供します。ブロックチェーン・ネットワークをすぐに使用できるので、ネットワーク・モニターに用意されている運用ツールや管理ツールで、同僚、管理職、パートナーに対して迅速にプレゼンテーションを行えます。
- **_ブロックチェーン・アプリケーションを反復的に開発してテストする。_**  
    Starter Plan は、ブロックチェーン・ネットワーク上で継続的にコードを開発してテストするためのステージング領域を提供します。反復的にコードを開発し、継続的な統合アーキテクチャーや継続的なデプロイメント・アーキテクチャーにデプロイすることができます。Starter Plan には、Enterprise Plan と同じブロックチェーン・ネットワーク機能とともに、運用や管理のための同じツールも用意されています。いずれかの Enterprise プランにプロジェクトをプッシュする準備ができたら、 Starter Plan と同じように運用しながら、ネットワークを拡張する機会を増やすことができます。
- **_実稼働にプロジェクトをテストする。_**  
    Starter Plan には、開発者とテスターがそれぞれのローカル環境から実際のクラウド・ブロックチェーン環境に迅速に移行するための環境が用意されています。このメカニズムにより、開発者とテスターは機能に集中でき、単体テストから機能テストに簡単に移行できます。また、システム、ソリューション、パフォーマンスの各テスト・チームは、この環境を使用して簡単にネットワークをローカルにセットアップすることができます。
- **_教育目的の作動可能な {{site.data.keyword.blockchainfull_notm}} Platform。_**  
    {{site.data.keyword.blockchainfull_notm}} Platform の Starter Plan と Enterprise プランは仮想的に同じユーザー・インターフェースを備えているので、カスタマイズされたトレーニングでは、Enterprise プランで組織が使用するのと同じワークフローが使用されるようになります。<!--the same user interface-->
- **_Toolchain を使用してサンプル・アプリケーションを迅速にデプロイする。_**  
    Starter Plan では、Toolchain を使用して数回のクリックだけでサンプル・アプリケーションをデプロイできます。これらのサンプルは、変更して前に進めるためのコードを含むサンプルのセットが拡張されていくので、開発者の助けとなります。


## Starter Plan の仕様

Starter Plan では、認証局 (CA) を使用してメンバーシップを管理したり、トランザクションの承認を行ったり、順序付けサービスを提供したり、プライベート・チャネルを構築したり、チェーンコード・ライフサイクルを管理したり、ライブ・ネットワーク内の他のユーザーと共同作業したりすることができます。これらは Enterprise プランの場合と同様です。

具体的には、Starter Plan には事前構成されたブロックチェーン・ネットワークが用意されています。このブロックチェーン・ネットワークは、ネットワーク・モニターを介して開発、管理、操作することができます。また、サンプル・アプリケーションをデプロイしたり、開発されるアプリケーションを {{site.data.keyword.blockchainfull_notm}} Platform: Develop と統合したりといった作業を簡単に実行することができます。

- **_ワンクリックで使用開始できるネットワーク_**  
    Starter Plan では、順序付けサービス、CA、デフォルト・チャネル、2 つの組織 (組織ごとに 1 つのピアが存在) が備わったネットワークをクリック 1 つで使用できるようになります。{{site.data.keyword.blockchainfull_notm}} Platform は、このネットワークの作成と構成を扱います (ネットワークはライブになった後で更新可能)。<!--The free trial provides you up to two organizations and two peers.-->
- **_コスト効率_**  
    Starter Plan メンバーシップ・オプションでは、Enterprise Plan メンバーシップ・オプションと同じさまざまなブロックチェーン機能を低コストで使用できます。<!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->ベータ・ステージでは、無料で Starter Plan を使用できます。
- **_複数組織ネットワークのシミュレーション_**  
    Starter Plan を使用して、複数の組織とネットワークを構築する作業をシミュレートできます。ネットワークに他の組織を実際に招待する必要はなく、自分自身が他の組織の役割を果たすことができます。このメカニズムにより、どのようにすれば新しい組織がネットワークに参加できるのか、複数の組織がネットワーク内でどのように連携するのかなどを知ることができます。ネットワーク・モニターで組織を切り替えることで、異なる組織の視点からネットワークを調べて管理できます。
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_Swagger API_**  
    Starter Plan では、Swagger インターフェースで試すことができる REST API がいくつか公開されています。詳しくは、[Swagger API の使用](swagger_apis.html)を参照してください。
- **_サンプル・アプリケーション_**  
    Starter Plan は {{site.data.keyword.cloud_notm}} の Toolchain サービスを利用するので、いくつかクリックするだけでサンプルをデプロイできます。サンプルをデプロイして起動すると、ブロックチェーン・ネットワークに対してチェーンコードとアプリケーションが自動的に実行されます。サンプル・アプリケーションについて詳しくは、[サンプル・アプリケーションのデプロイ](howto/prebuilt_samples.html)を参照してください。
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop アプリケーション統合_**  
    Starter Plan では、{{site.data.keyword.blockchainfull_notm}} Platform: Develop アプリケーションをネットワークにデプロイできます。詳しくは、[IBP: Develop アプリケーションのインポート](link)を参照してください。

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## 知っておくべきこと

- Starter Plan は {{site.data.keyword.blockchainfull_notm}} Platform へのエントリー・ポイントであり、以下の点で Enterprise Plan とは異なります。
    - [CA](glossary.html#ca) と[順序付けサービス](glossary.html#orderer)はフォールト・トレラントではありません。各組織の CA は 1 つだけであり、ネットワークの[順序付けサービス](glossary.html#orderer)も 1 つだけだからです。
    - 順序付けサービスは [SOLO](glossary.html#SOLO) [コンセンサス](glossary.html#consensus)のみを使用します。Starter Plan ネットワークは 1 つの[順序付けサービス](glossary.html#orderer)だけで構成され、その順序付けサービスがすべてのピアに対してコンセンサスを得るための処理を実行します。
    - 厳密な認証と暗号処理のためのデジタル鍵の保護と管理には、[ハードウェア・セキュリティー・モジュール (HSM)](glossary.html#hsm) を使用することはできません。
- Starter Plan の保守とネットワーク更新は、固定スケジュールで行われます。保守期間中は、新しいネットワークのプロビジョニングは行えません。また、ネットワーク中断が短期間発生する可能性もあります。
- Starter Plan では、ベータ版から GA への移行を含め、リリース・アップグレードでのデータ保存は保証されません。
- Starter Plan ネットワークから他のメンバーシップ・オプションへのデータのマイグレーションは、現在サポートされていません。ただし、Starter Plan でテストされた `.bna` ファイル、チェーンコード、アプリケーションをマイグレーションすることは可能です。詳しくは、[スターター・プランからエンタープライズ・プランへのマイグレーション](get_start_starter_plan.html#migrate)を参照してください。

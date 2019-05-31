---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: getting started tutorials, videos, web browsers

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} の概説
{: #ibp-v2-deploy-iks}

{{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} には、{{site.data.keyword.blockchainfull_notm}} Platform コンソールという、ブロックチェーン・コンポーネントをデプロイして管理するための手順を簡単に素早く行えるようにするユーザー・インターフェースが組み込まれています。このチュートリアルでは、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 を開始し、コンソールを使用して {{site.data.keyword.cloud_notm}} 上の {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターにブロックチェーン・コンポーネントをデプロイして管理する方法について説明します。 Kubernetes と {{site.data.keyword.cloud_notm}} Kubernetes Service について詳しくは、[Kubernetes](/docs/services/blockchain/reference/k8s.html "Kubernetes") を参照してください。
{:shortdesc}

**対象者:** このトピックは、{{site.data.keyword.cloud_notm}} での Kubernetes クラスターのセットアップと {{site.data.keyword.blockchainfull_notm}} Platform のデプロイを担当するシステム管理者を対象にしています。

{{site.data.keyword.blockchainfull_notm}} Platform を {{site.data.keyword.cloud_notm}} Kubernetes クラスターにリンクしたら、コンソールを起動して、ブロックチェーン・コンポーネントの作成と管理を行うことができ、以下の重要な利点が得られます。

- **制御:** 中央の単一コンソールからブロックチェーン・コンポーネントと証明書を制御および管理できます。 ビジネスに必要なコンポーネントだけをデプロイし、ニーズの拡大に応じてコンポーネントを追加できます。
- **柔軟な Kubernetes ベースのデプロイメント:** Kubernetes クラスターのコンピュート (CPU、メモリー、ストレージ) のオプションを活用し、組み込みの HA オプションと DR オプションを利用できます。


## 考慮事項
{: #ibp-v2-deploy-iks-considerations}

コンソールをデプロイする前に、以下の考慮事項を理解しておいてください。

- {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} は、Hyperledger Fabric v1.4 を使用して構築されています。
- コンソールまたは API でデプロイされるすべてのピアでは、状態データベースとして CouchDB が使用されます。
- オファリングの評価のために、{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを無料の Kubernetes クラスターにリンクすることもできますが、キャパシティーとパフォーマンスには制限があり、データはマイグレーションできず、30 日後にクラスターは削除されます。
- ベータ版トライアルは無料ですが、有料のクラスターを選択した場合は、Kubernetes クラスターに対する支払いが必要になります。
- Kubernetes クラスターの正常性モニター、セキュリティー、およびロギングの管理は、お客様が行う作業です。 {{site.data.keyword.cloud_notm}} が管理する範囲と、お客様が行う作業範囲について詳しくは、こちらの[情報 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "クラスター管理の責任") を参照してください。
- Kubernetes ダッシュボードで Kubernetes クラスターのリソース使用量をモニターすることもお客様の作業です。 クラスターのストレージ容量またはパフォーマンスを向上させる必要がある場合は、[既存のボリュームの変更方法に関するこちらの情報 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "既存のストレージ・デバイスのサイズと IOPS の変更") を参照してください。
- 証明書および秘密鍵の管理および保護は、お客様が行う作業です。 IBM は、お客様の証明書を Kubernetes クラスターに保管しません。
- {{site.data.keyword.blockchainfull_notm}} Platform は、特定の地域でのみ使用可能です。最新のリストは、[{{site.data.keyword.blockchainfull_notm}} Platform のロケーション](/docs/services/blockchain/howto?topic=blockchain-ibp-regions-locations)に関するトピックを参照してください。
- {{site.data.keyword.cloud_notm}} Kubernetes クラスターの Kubernetes のバージョンは 1.11 以上でなければなりません。 [アップグレード手順](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)に従って、新規および既存のクラスターをこのバージョンにアップグレードしてください。
- Kubernetes クラスター用のストレージをプロビジョンして、それをクラスターのデフォルトのストレージ・クラスにする必要があります。このストレージは、ピア、順序付けサービス、および認証局のノードによって使用されます。プロビジョンするストレージのタイプを決定するには、[Kubernetes ストレージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/containers?topic=containers-kube_concepts "Kubernetes ストレージの基本について") に関するトピックを参照してください。

## ビデオのチュートリアル
{: #ibp-v2-deploy-video}

{{site.data.keyword.blockchainfull_notm}} Platform コンソールの詳細や {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} のデプロイを開始する方法については、以下の[一連のビデオ]( http://ibm.biz/BlockchainPlatformSeries)をご覧ください。

## 始めに
{: #ibp-v2-deploy-iks-prereq}

開始前に、以下のことを行います。

- [{{site.data.keyword.cloud_notm}} 有料アカウント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/blockchain "カタログ") を持っている必要があります。 アカウントがない場合は、以下のようにします。
   1. **「登録」**ボタンをクリックします。
   2. 無料トライアル・アカウントを作成した後、{{site.data.keyword.cloud_notm}} コンソールで**「管理」** > **「請求および使用量」** > **「請求処理」**に移動し、**「クレジット・カードの追加」**をクリックして、**「従量課金 (PAYG)」**タイプにアップグレードします。
   3. ブロックチェーン・サービス・インスタンスにリンクする Kubernetes クラスターに対する管理者の役割とマネージャーの役割の両方がユーザーにあることを確認してください。 詳しくは、[Kubernetes のアクセス役割を割り当てる方法](#ibp-v2-deploy-iks-k8x-access-roles)の手順を参照してください。

組織規模の広範囲のソリューションとしてサービス・インスタンスを使用する場合は、ネットワークを作成する際に組織用の E メール・アドレスを使用することを参加組織にお勧めします。 そうすれば、一個人の都合によってネットワークの利用可否が左右されることがありません。
{:tip}  

- 既存の {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターを使用する場合は、Kubernetes のバージョンを確認し、必要に応じて、1.11 以降にアップグレードします。 クラスターで実行されている Kubernetes バージョンの確認方法、およびバージョンのアップグレード方法について詳しくは、[クラスターの Kubernetes バージョンの更新](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)を参照してください。

### ブラウザー
{: #ibp-v2-deploy-iks-browsers}
以下のリストに、{{site.data.keyword.blockchainfull_notm}} Platform コンソールに最小限必要なブラウザー・ソフトウェアを指定します。

- Chrome: ご使用のオペレーティング・システムの最新バージョン
- Firefox: ご使用のオペレーティング・システム用の最新の通常 (非 ESR) バージョン
- Safari: Mac の最新バージョン
- Edge: v44.17763.1.0 以上

### 必要なリソース
{: #ibp-v2-deploy-iks-resources-required}

#### フリー・クラスター
{: #ibp-v2-deploy-iks-resources-required-free}

{{site.data.keyword.blockchainfull_notm}} Platform コンソールを {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターにデプロイするには、Kubernetes クラスターがハードウェア・リソースの最小要件を満たしていることを確認する必要があります。

|Kubernetes クラスターのタイプ | ユース・ケース | CPU | RAM | ワーカー・ノード |
|-----------|------|-----|-----------------------|
|標準 (推奨) | MVP に適しています | 4 (共有) | 16 GB (共有)|複数|
|無料** | 評価に適しています | 2 | 4 GB | 1 |  
** {{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを無料の {{site.data.keyword.cloud_notm}} Kubernetes クラスターにリンクすると、30 日間料金なしで {{site.data.keyword.blockchainfull_notm}} Platform をプレビューできます。スループット、ストレージ、および機能性の面でパフォーマンスが制限されます。{{site.data.keyword.cloud_notm}} では、30 日後に Kubernetes クラスターが削除され、無料のクラスターから有料のクラスターにノードまたはデータを移行することはできません。

テストおよび試験目的には、これらのリソースで十分です。 [ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)では、ピアを 2 つ、CA を 2 つ、順序付けプログラムを 1 つ作成しますが、所要量は約 1.1 CPU であるため、テストの目的に使用できる余裕がいくらかあります (例えば、それぞれに独自の台帳を持つチャネルをいくつか作成したりできます)。 無料の Kubernetes クラスターを使用する場合は、30 日間の試用期間後にクラスターが削除され、関連する資産がすべて除去されることに注意してください。 また、無料のクラスターではパフォーマンスが大幅に低下します。
{:note}

#### 有料クラスター
{: #ibp-v2-deploy-iks-resources-required-paid}

{{site.data.keyword.blockchainfull_notm}} Platform の実動レベルのデプロイメントは、{{site.data.keyword.cloud_notm}} Kubernetes Service の有料クラスターにデプロイします。 このクラスターのサイズや構成は、それぞれのユース・ケースのニーズによって決まります。 大規模なデプロイメントは、必然的に大規模なクラスターにデプロイする必要があります。 計画しているデプロイメントの規模よりもクラスターの規模をどれほど大きくするかは、お客様次第です。 少なくともいくらかの余裕を持たせることが理想です。そうすれば、ノードのサイズを調整する**前に**、Kubernetes クラスターに追加のリソースをデプロイしなくても、ピアや順序付けプログラムが追加のチャネルに参加してスループットを向上させられるからです。 こうした値の調整方法の詳細については、[リソースの再割り振り](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-reallocate-resources)を参照してください。

追加のノードやポッドをデプロイする作業をいくらか自動で実行する [{{site.data.keyword.cloud_notm}} Kubernetes Service 自動スケール機能 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/containers?topic=containers-ca#ca "{{site.data.keyword.cloud_notm}} Kubernetes Service 自動スケール機能") を使用しないお客様の場合は、拡大できる余地を残した十分なサイズの初期デプロイメントを作成することが特に重要になります。

最初から {{site.data.keyword.cloud_notm}} Kubernetes Service に十分なリソースをデプロイして、必要に応じてポッドやワーカー・ノードを拡張できるようにしておくほうが、Kubernetes クラスターのデプロイメントを大きくする必要がないので簡単ではありますが、Kubernetes クラスターのデプロイメントが大きくなるほど、費用が高くなります。 選択肢について慎重に検討し、どのような選択を行うにしても、生じるトレードオフを認識する必要があります。

クラスターに必要なストレージ量およびコンピュート量の目安として、このリストの後の表を参照してください。ピア、順序付けプログラム、CA の現在のデフォルト値を記載しています。

| **コンポーネント** (すべてのコンテナー) | CPU  | メモリー (GB) | ストレージ (GB) |
|--------------------------------|---------------|-----------------------|------------------------|
| **ピア**                       |  1.1          | 2.2                   | 200 (ピア用に 100 GB と CouchDB 用に 100 GB を含む)|
| **CA**                         | .1            | .2                    | 20                     |
| **順序付けプログラム (Orderer)**                    | .45           | .9                    | 100                    |

## 手順 1: {{site.data.keyword.cloud_notm}} でサービス・インスタンスを作成する
{: #ibp-v2-deploy-iks-create-service-instance}

以下の手順を使用して、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 のサービス・インスタンスを {{site.data.keyword.cloud_notm}} に作成します。

1. {{site.data.keyword.cloud_notm}} カタログで[ブロックチェーン・サービス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/blockchain) を見つけるか、自分の {{site.data.keyword.cloud_notm}} カタログ・ページで`ブロックチェーン`を探します。
2. インスタンスの**サービス名**を、わかりやすい名前に変更することをお勧めします。
3. ベータ版では、利用可能な地域は**ダラス**のみです。これは変更できません。
4. リソース・グループとタグ・フィールドはそのままでかまいません。
5. **「作成」**をクリックして、サービス・インスタンスをプロビジョンします。

## 手順 2: {{site.data.keyword.blockchainfull_notm}} Platform をデプロイする
{: #ibp-v2-deploy-iks-steps}

サービス・インスタンスを作成した直後に、以下のガイダンスに従って {{site.data.keyword.blockchainfull_notm}} Platform をデプロイできます。

1. **「ようこそ」&「前提条件」**ステップ。 **ダラス**地域に既に {{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターがあり、ブロックチェーン・サービス用にそのクラスターを使用する場合は、該当するチェック・ボックスを選択します。 **既存のクラスターを使用する場合は、次のステップをスキップできますが、Kubernetes バージョンが v1.11 以降であることを確認してください**。 **「続行 (Continue)」**をクリックします。
2. **「クラスターの作成」**ステップ。 ステップ 1 で既存の Kubernetes クラスターを使用するチェック・ボックスを選択した場合は、このステップはスキップしてください。 そうでない場合は、**「クラスターの新規作成 (Create a new cluster)」**をクリックします。クラスターを作成するための {{site.data.keyword.cloud_notm}} Kubernetes ダッシュボードが起動します。 詳しくは、[{{site.data.keyword.cloud_notm}} Kubernetes サービスの概説 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/containers/getting-started.html) を参照してください。 このプロセスの実行時間には余裕をみておいてください。
  - どのクラスター・タイプを選択しようと、ベータ版リリースでは、Kubernetes クラスターのロケーションに**ダラス**を選択する必要があります。
  - **標準クラスター (推奨):** 複数ノードで高可用性を実現できる長期用のオプションが必要な場合に選択してください。 **Kubernetes バージョン v1.11 以降を選択していることを確認してください。** 有料クラスターをデプロイする場合は、[標準クラスターの作成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/containers?topic=containers-clusters#clusters_ui_standard "標準クラスターの作成") を参照してください。 ただし、高可用性や災害復旧が必要な場合は、使用するストレージ・クラスを決定する必要があります。 動的プロビジョニングでは、クラスターの`デフォルト`のストレージ・クラスが使用されます。 そのため、お客様はデフォルトとして任意のストレージ・クラスを設定できます。 詳しくは、[ファイル・ストレージ構成の決定 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/containers?topic=containers-file_storage#file_predefined_storageclass "ファイル・ストレージ構成の決定") を参照してください。
  - **無料クラスター (Free cluster):** プラットフォームのプレビュー期間が 30 日未満の場合に選択してください。無料のクラスターから有料のクラスターには移行できないので**注意してください**。 無料タイプのクラスターでは、利用できるストレージとトランザクションのスループットが限られています。 Kubernetes クラスターの有効期限が切れた場合の対応手順については、[Kubernetes クラスターの有効期限](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration)に関するトピックを参照してください。
  - {{site.data.keyword.cloud_notm}} 上の無料と有料の Kubernetes クラスターの違いについて詳しくは、[フリー・クラスターと標準クラスターの比較 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "フリー・クラスターと標準クラスターの比較") を参照してください。  

   {{site.data.keyword.blockchainfull_notm}} Platform 2.0 デプロイメント・プロセスを完了できるように、クラスターを作成したら、必ず、ブラウザーでこのタブに戻ってください。  
   {:important}  

  クラスターが正常にデプロイされるまで待つ必要があります。 次に、**「クラスターがある (I Have a Cluster)」**ボタンをクリックします。
3. クラスターで実行されている Kubernetes バージョンは、バージョン 1.11 以降でなければなりません。 こちらの[手順](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)に従って、クラスターのバージョンを確認し、必要に応じてアップグレードしてください。 その後、ここに戻り、以下の手順を進めてください。
4. **「クラスターへのデプロイ (Deploy onto cluster)」**ステップ。 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 をデプロイする Kubernetes クラスターをドロップダウン・リストから選択し、**「クラスターへのデプロイ (Deploy to cluster)」**をクリックします。  

  ドロップダウン・リストに Kubernetes クラスターが表示されない場合は、以下の状況が原因である可能性があります。
  - クラスターの作成プロセスが完了するのには、最大で 60 分かかることがあります。 クラスターを作成した場合は、そのクラスターの状態が**「正常」**になるまで、ある程度時間がかかることを見越しておいてください。
  - **ダラス**地域以外のクラスターは表示されないので使用できません。
  - ESR バージョンの Firefox を使用していないことを確認してください。 ESR バージョンを使用している場合は、Chrome などの別のブラウザーに変更して再試行してください。

5. **「コンソールの起動 (Launch console)」**ステップ。 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 が正常にデプロイされたら、**「{{site.data.keyword.blockchainfull_notm}} Platform の起動 (Launch the {{site.data.keyword.blockchainfull_notm}} Platform)」**をクリックして、{{site.data.keyword.blockchainfull_notm}} Platform コンソールを開きます。 コンソールのプロビジョン中に、ボタンが有効になるまでに数分かかる場合があります。

## (オプション) コンソールへのユーザーの追加
{: #ibp-v2-deploy-iks-add-users}

デフォルトでは、コンソールでは {{site.data.keyword.cloud_notm}} の ID サービス・プロバイダーとして [{{site.data.keyword.cloud_notm}} IAM (ID およびアクセス管理) ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/iam?topic=iam-iamoverview#iamoverview "IBM Cloud ID およびアクセス管理") が使用されます。 {{site.data.keyword.blockchainfull_notm}} Platform コンソールは、コンソールの管理者として {{site.data.keyword.IBM_notm}} 製品所有者の E メール・アドレスを構成してプロビジョンされます。 このユーザーは、管理者として、ユーザーの E メール・アドレスを介して他のユーザーにコンソールへのアクセス権を付与できます。  詳しくは、[コンソールでのユーザーの追加と削除](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)の方法を参照してください。

## 次のステップ
{: #ibp-v2-deploy-iks-next-steps}

コンソールが使用できる状態になったので、[ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)に進むことができます。
必要に応じて後で戻ることができるように、コンソールの URL をブックマークすることを検討してください。そうしない場合は、[インストール後の手順](#ibp-v2-deploy-iks-post-install)の手順に従って、ブラウザーから戻ることができます。

## クラスターの Kubernetes バージョンの更新
{: #ibp-v2-deploy-iks-updating-kubernetes}

既存の {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターを使用する場合、Kubernetes バージョンが 1.11 以降であることを確認してください。

クラスターの Kubernetes バージョンは、{{site.data.keyword.cloud_notm}} の [Kubernetes クラスター・ページ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/kubernetes/clusters) で確認できます。このページでは、すべてのクラスターがテーブルにリストされています。

Kubernetes のバージョンがバージョン 1.11 以降でない場合は、以下の手順を実行して、クラスターの Kubernetes バージョンを更新する必要があります。

1. 行末にあるオーバーフロー・メニュー・アイコンをクリックし、**「バージョンの更新 (Update version)」**を選択します。 このプロセスは完了までに約 1 時間かかります。 バージョンが正常に更新されると、クラスターの更新バージョンが**「Kubernetes バージョン」**列に表示されます。  
2. Kubernetes バージョンのドロップダウン・リストから 1.11 以降の Kubernetes バージョンを選択し、**「更新」**をクリックします。
3. クラスターをクリックし、**「ワーカー・ノード」**タブに移動します。 更新するワーカー・ノードの前のチェック・ボックスを選択し、ポップアップ・メニュー・バーから**「Kubernetes の更新 (Update Kubernetes)」**をクリックします。 クラスターに複数のワーカー・ノードが含まれている場合、そのすべてを更新する必要があります。

  ワーカー・ノードを更新すると、アプリとサービスにダウン時間が発生する可能性があります。 ワーカー・ノード・マシンが再イメージ化されるので、[ポッドの外部に保管 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview) されていないデータは削除されます。
  {:important}

![Kubernetes バージョンの更新](../images/update_k8s_version.gif)

{{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターおよびワーカー・ノードの Kubernetes バージョンの更新方法について詳しくは、[クラスター、ワーカー・ノード、アドオンの更新 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](/docs/containers?topic=containers-update#update) を参照してください。  

更新が完了するのを待ってから、[{{site.data.keyword.blockchainfull_notm}} Platform 2.0 のデプロイメントを再開](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps)できます。

## Kubernetes のアクセス役割を割り当てる方法
{: #ibp-v2-deploy-iks-k8x-access-roles}

ブロックチェーン・サービス・インスタンスを Kubernetes クラスターにリンクするユーザーには、Kubernetes の管理者とマネージャーの役割が必要です。
このアクセス権限を構成するには、以下の手順を実行します。
   1. {{site.data.keyword.cloud_notm}} ダッシュボードの**「管理」**ドロップダウン・リストをクリックし、**「アクセス (IAM) (Access (IAM))」**をクリックします。
   2. 左側のナビゲーション・メニューで**「ユーザー」**をクリックし、サービス・インスタンスを Kubernetes クラスターにリンクするユーザーの ID をクリックします。
   3. **「アクセス・ポリシー」**をクリックし、次に**「アクセス権限の割り当て」**をクリックします。
   4. **「リソースへのアクセス権限の割り当て」**タイルをクリックします。
   5. **「サービス」**ドロップダウン・リストから**「Kubernetes Service」**を選択します。
   6. このユーザーの**「管理者」**と**「マネージャー」**の役割にチェック・マークを付けます。
   7. **「割り当て」**をクリックします。

![Kubernetes バージョンの更新](../images/k8sAccess.gif)

Kubernetes のアクセス制御の詳細については、[ユーザーに適したアクセス・ポリシーと役割を選択する方法](/docs/containers?topic=containers-users#access_roles)を参照してください。

## インストール後の手順
{: #ibp-v2-deploy-iks-post-install}

### コンソールに戻る
{: #ibp-v2-deploy-iks-rtn-to-console}

サービス・インスタンスのデプロイ後にコンソールに戻る必要がある場合は、{{site.data.keyword.cloud_notm}} ダッシュボードからアクセスできます。
1. ブラウザーで、https://cloud.ibm.com/resources にナビゲートして、ログインします。
2. **「サービス」**ツイスティーの下に、{{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスが表示されます。デプロイした {{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを見つけて、クリックします。
3. 後続のパネルで、**「{{site.data.keyword.blockchainfull_notm}} Platform の起動 (Launch the IBM Blockchain Platform)」**をクリックします。

コンソールが再度表示されます。

### 価格設定および請求処理の情報
{: #ibp-v2-deploy-iks-pricing-billing}

- {{site.data.keyword.blockchainfull_notm}} Platform の価格情報に再度アクセスする必要がある場合は、[料金](/docs/services/blockchain/howto?topic=blockchain-ibp-saas-pricing)のトピックを参照してください。
- 現在の {{site.data.keyword.cloud_notm}} の使用量情報は {{site.data.keyword.cloud_notm}} ダッシュボードの[使用量のタイル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/billing/ "請求および使用量") で取得でき、料金は[請求先情報 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/billing/billing-items "請求項目") に表示されます。{{site.data.keyword.blockchainfull_notm}} Platform の請求処理の仕組みについて詳しくは、[課金](/docs/services/blockchain/howto?topic=blockchain-ibp-saas-pricing#ibp-saas-pricing-billing)のトピックを参照してください。

### サービス・インスタンスの削除
{: #ibp-v2-deploy-iks-delete-service-instance}

サービス・インスタンスが不要になった場合は、Kubernetes クラスターから削除してリソースを解放できます。 {{site.data.keyword.cloud_notm}} ダッシュボードを使用して {{site.data.keyword.blockchainfull_notm}} Platform サービス・インスタンスを削除できます。

1. [{{site.data.keyword.cloud_notm}} ダッシュボード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/ "ダッシュボード") にナビゲートします。
2. **「リソースの要約」**タイルを見つけて、**「サービス」**をクリックします。
3. **「サービス」**ツイスティーの下で、削除するサービス・インスタンスを見つけて、「アクション」メニューで**「削除」**をクリックします。

サービス・インスタンスの削除に失敗した場合、Kubernetes クラスターにアクセスできないことが原因である可能性があります。 そのような場合は、[サポート・チケット](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases)を開いて、サービス・インスタンスの削除を依頼してください。

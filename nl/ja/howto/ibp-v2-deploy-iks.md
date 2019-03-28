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
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版の概説
{: #ibp-v2-deploy-iks}

{{site.data.keyword.blockchainfull}} Platform 2.0 は、{{site.data.keyword.blockchainfull_notm}} Platform コンソールという、ブロックチェーン・コンポーネントをデプロイして管理するための手順を簡単に素早く行えるようにする GUI が組み込まれている無料のベータ版リリースです。 このチュートリアルでは、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 を開始し、コンソールを使用して {{site.data.keyword.cloud_notm}} 上の {{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターにブロックチェーン・コンポーネントをデプロイして管理する方法について説明します。
{:shortdesc}

**対象者:** このトピックは、{{site.data.keyword.IBM_notm}} での Kubernetes クラスターのセットアップと {{site.data.keyword.blockchainfull_notm}} Platform のデプロイを担当するシステム管理者を対象に設計されています。

 {{site.data.keyword.blockchainfull}} Platform を Kubernetes クラスターにデプロイしたら、コンソールを起動して、ブロックチェーン・コンポーネントを作成および管理できます。 {{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターを使用して {{site.data.keyword.blockchainfull_notm}} Platform 2.0 をデプロイすることには、以下の大きなメリットが得られます。

- **制御:** 中央の単一コンソールからブロックチェーン・コンポーネントと証明書を制御および管理できます。 ビジネスに必要なコンポーネントだけをデプロイし、ニーズの拡大に応じてコンポーネントを追加できます。
- **柔軟な Kubernetes ベースのデプロイメント:** Kubernetes クラスターのコンピュート (CPU、メモリー、ストレージ) のオプションを活用し、組み込みの HA オプションと DR オプションを利用できます。


## 考慮事項
{: #ibp-v2-deploy-iks-considerations}

コンソールをデプロイする前に、以下の考慮事項を理解しておいてください。

- {{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版は、Hyperledger Fabric v1.4 を使用して構築されています。
- この無料の 2.0 ベータ版でデプロイされるすべてのピアでは、状態データベースとして CouchDB が使用されます。
- ベータ版オファリングの評価に無料の Kubernetes クラスターを使用することもできますが、キャパシティーとパフォーマンスには制限があり、データはマイグレーションできず、30 日後にクラスターは削除されます。
- Kubernetes クラスターの正常性モニター、セキュリティー、およびロギングの管理は、お客様が行う作業です。 {{site.data.keyword.cloud_notm}} が管理する範囲と、お客様が行う作業範囲について詳しくは、こちらの[情報 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "クラスター管理の責任") を参照してください。
- Kubernetes ダッシュボードで Kubernetes クラスターのリソース使用量をモニターすることもお客様の作業です。 クラスターのストレージ容量またはパフォーマンスを向上させる必要がある場合は、[既存のボリュームの変更方法に関するこちらの情報 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "既存のストレージ・デバイスのサイズと IOPS の変更") を参照してください。
- 証明書、公開鍵、秘密鍵の管理および保護は、お客様が行う作業です。 IBM は、お客様の証明書を Kubernetes クラスターに保管しません。
- 無料の 2.0 ベータ版オファリングは、{{site.data.keyword.cloud_notm}} Kubernetes サービスの**ダラス**地域でのみ利用可能です。 この地域には、ダラス、サンノゼ、ヒューストン、ブラジルのデータ・センターが含まれています。そのため、すべてのブロックチェーン・コンポーネントを、これら 4 つのデータセンターのいずれに置くこともできます。それ以外の地域にはデプロイされません。
- ベータ版オファリングは無料ですが、有料のクラスターを選択した場合は、Kubernetes クラスターに対する支払いが必要になります。
- {{site.data.keyword.cloud_notm}} Kubernetes クラスターの Kubernetes のバージョンは 1.11 以上でなければなりません。 [アップグレード手順](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)に従って、新規および既存のクラスターをこのバージョンにアップグレードしてください。

## ビデオのチュートリアル
{: #ibp-v2-deploy-video}

{{site.data.keyword.blockchainfull}} Platform 2.0 無料ベータ版の詳細や、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版のデプロイを開始する方法については、以下のビデオをご覧ください。

<iframe class="embed-responsive-item" id="youtubeplayer" title="IBM Blockchain Platform free 2.0 beta video - deployment tutorial" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/gPnkVQiHRqk" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

## 始めに
{: #ibp-v2-deploy-iks-prereq}

開始前に、以下のことを行います。

- [{{site.data.keyword.cloud_notm}} 有料アカウント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.cloud.ibm.com/catalog/services/blockchain "カタログ") を持っている必要があります。 アカウントがない場合は、以下のようにします。
1. **「登録」**ボタンをクリックします。
2. 無料トライアル・アカウントを作成した後、{{site.data.keyword.cloud_notm}} コンソールで**「管理」** > **「請求および使用量」** > **「請求処理」**に移動し、**「クレジット・カードの追加」**をクリックして、**「従量課金 (PAYG)」**タイプにアップグレードします。

組織規模の広範囲のソリューションとしてサービス・インスタンスを使用する場合は、ネットワークを作成する際に組織用の E メール・アドレスを使用することを参加組織にお勧めします。 そうすれば、一個人の都合によってネットワークの利用可否が左右されることがありません。
{:tip}  

- 既存の {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターを使用する場合は、Kubernetes のバージョンを確認し、必要に応じて、1.11 以降にアップグレードします。 クラスターで実行されている Kubernetes バージョンの確認方法、およびバージョンのアップグレード方法について詳しくは、[クラスターの Kubernetes バージョンの更新](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)を参照してください。

### ブラウザー
{: #ibp-v2-deploy-iks-browsers}
以下のリストに、{{site.data.keyword.blockchainfull_notm}} Platform コンソールに最小限必要なブラウザー・ソフトウェアを指定します。

- Chrome: ご使用のオペレーティング・システムの最新バージョン
- Firefox: ご使用のオペレーティング・システム用の最新の通常 (非 ESR) バージョン
- Safari: Mac の最新バージョン

### 必要なリソース
{: #ibp-v2-deploy-iks-resources-required}

{{site.data.keyword.blockchainfull_notm}} Platform コンソールを {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターにデプロイするには、Kubernetes クラスターがハードウェア・リソースの最小要件を満たしていることを確認する必要があります。

|Kubernetes クラスターのタイプ | ユース・ケース | CPU | RAM | ワーカー・ノード |
|-----------|------|-----|-----------------------|
|標準 (推奨) | MVP に適しています | 4 (共有) | 16 GB (共有)|複数|
|無料 | 評価に適しています | 2 | 4 GB | 1 |  

テストおよび試験目的には、これらのリソースで十分です。 無料の Kubernetes クラスターを使用する場合は、30 日間の試用期間後にクラスターが削除され、関連する資産がすべて除去されることに注意してください。 無料のクラスターではパフォーマンスは大幅に低下します。
{:note}

## 手順 1: {{site.data.keyword.cloud_notm}} でサービス・インスタンスを作成する
{: #ibp-v2-deploy-iks-create-service-instance}

以下の手順を使用して、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 のサービス・インスタンスを {{site.data.keyword.cloud_notm}} に作成します。

1. {{site.data.keyword.cloud_notm}} カタログで[ブロックチェーン・サービス ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.cloud.ibm.com/catalog/services/blockchain) を見つけるか、自分の {{site.data.keyword.cloud_notm}} カタログ・ページで`ブロックチェーン`を探します。
2. 任意で、インスタンスの**サービス名**を、わかりやすい名前に変更できます。
3. ベータ版では、利用可能な地域は**ダラス**のみです。これは変更できません。
4. リソース・グループとタグ・フィールドはそのままでかまいません。
5. **「作成」**をクリックして、サービス・インスタンスをプロビジョンします。

## 手順 2: {{site.data.keyword.blockchainfull_notm}} Platform 2.0 をデプロイする
{: #ibp-v2-deploy-iks-steps}

サービス・インスタンスを作成した直後に、以下のガイダンスに従って {{site.data.keyword.blockchainfull_notm}} Platform 2.0 をデプロイできます。

1. **「ようこそ」&「前提条件」**ステップ。 **ダラス**地域に既に {{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターがあり、ブロックチェーン・サービス用にそのクラスターを使用する場合は、該当するチェック・ボックスを選択します。**既存のクラスターを使用する場合は、次のステップをスキップできますが、Kubernetes バージョンが v1.11 以降であることを確認してください**。**「続行 (Continue)」**をクリックします。
2. **「クラスターの作成」**ステップ。 ステップ 1 で既存の Kubernetes クラスターを使用するチェック・ボックスを選択した場合は、このステップはスキップしてください。 そうでない場合は、**「クラスターの新規作成 (Create a new cluster)」**をクリックします。クラスターを作成するための {{site.data.keyword.cloud_notm}} Kubernetes ダッシュボードが起動します。 詳しくは、[{{site.data.keyword.cloud_notm}} Kubernetes サービスの概説 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/container_index.html) を参照してください。 このプロセスの実行時間には余裕をみておいてください。
  - どのクラスター・タイプを選択しようと、ベータ版リリースでは、Kubernetes クラスターのロケーションに**ダラス**を選択する必要があります。
  - **標準クラスター (推奨) (Standard cluster (recommended)):** 複数ノードで高可用性を実現でき、GA オファリングの提供が開始されたらベータ版のオファリングから移行できる長期用の選択肢が必要な場合に選択してください。 **Kubernetes バージョン v1.11 以降を選択していることを確認してください。**
  - **無料クラスター (Free cluster):** クラスターの使用予定期間が 30 日未満の場合に選択してください。 無料のクラスターから有料のクラスターには移行できないので**注意してください**。 無料タイプのクラスターでは、利用できるストレージとトランザクションのスループットが限られています。
  - {{site.data.keyword.cloud_notm}} 上の無料と有料の Kubernetes クラスターの違いについて詳しくは、[フリー・クラスターと標準クラスターの比較 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "フリー・クラスターと標準クラスターの比較") を参照してください。  

   {{site.data.keyword.blockchainfull_notm}} Platform 2.0 デプロイメント・プロセスを完了できるように、クラスターを作成したら、必ず、ブラウザーでこのタブに戻ってください。  
   {:important}  

  クラスターが正常にデプロイされるまで待つ必要があります。 次に、**「クラスターがある (I Have a Cluster)」**ボタンをクリックします。
3. クラスターで実行されている Kubernetes バージョンは、バージョン 1.11 以降でなければなりません。 こちらの[手順](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)に従って、クラスターのバージョンを確認し、必要に応じてアップグレードしてください。 その後、ここに戻り、以下の手順を進めてください。
4. **「クラスターへのデプロイ (Deploy onto cluster)」**ステップ。 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 をデプロイする Kubernetes クラスターをドロップダウン・リストから選択し、**「クラスターへのデプロイ (Deploy to cluster)」**をクリックします。  

  ドロップダウン・リストに Kubernetes クラスターが表示されない場合は、以下の状況が原因である可能性があります。
  - クラスターの作成プロセスが完了するのには、最大で 60 分かかることがあります。クラスターを作成した場合は、そのクラスターの状態が**「正常」**になるまで、ある程度時間がかかることを見越しておいてください。
  - **ダラス**地域以外のクラスターは表示されないので使用できません。
  - ESR バージョンの Firefox を使用していないことを確認してください。 ESR バージョンを使用している場合は、Chrome などの別のブラウザーに変更して再試行してください。

5. **「コンソールの起動 (Launch console)」**ステップ。 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 が正常にデプロイされたら、**「{{site.data.keyword.blockchainfull_notm}} Platform の起動 (Launch the {{site.data.keyword.blockchainfull_notm}} Platform)」**をクリックして、{{site.data.keyword.blockchainfull_notm}} Platform コンソールを開きます。 コンソールのプロビジョン中に、ボタンが有効になるまでに数分かかる場合があります。

## (オプション) コンソールへのユーザーの追加
{: #ibp-v2-deploy-iks-add-users}

デフォルトでは、コンソールの ID サービス・プロバイダーとして [{{site.data.keyword.IBM_notm}} ID ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/iam/index.html#iamoverview "IBM Cloud Identity and Access Management") が使用されます。 {{site.data.keyword.blockchainfull_notm}} Platform コンソールは、コンソールの管理者として {{site.data.keyword.IBM_notm}} 製品所有者の E メール・アドレスを構成してプロビジョンされます。 このユーザーは、管理者として、ユーザーの E メール・アドレスを介して他のユーザーにコンソールへのアクセス権を付与できます。  

### 新しいユーザーを追加する方法
{: #ibp-v2-deploy-iks-add-users-howto}

左側ナビゲーションの**「ユーザー」**アイコンをクリックして、コンソールにログインできるユーザーのリストを表示します。 コンソール管理者としてログインしている場合は、**「新規ユーザーの追加」**をクリックして、新しいユーザーにコンソールの使用を許可し、**「管理者」**または**「一般」**のアクセス権を付与できます。 **「管理者」**の役割を持つユーザーは、ユーザーの追加と削除を行うことができます。

## 次のステップ
{: #ibp-v2-deploy-iks-next-steps}

コンソールが使用できる状態になったので、[ネットワーク構築チュートリアル](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)に進むことができます。

## クラスターの Kubernetes バージョンの更新
{: #ibp-v2-deploy-iks-updating-kubernetes}

既存の {{site.data.keyword.cloud_notm}} Kubernetes サービス・クラスターを使用する場合、Kubernetes バージョンが 1.11 以降であることを確認してください。

クラスターの Kubernetes バージョンは、{{site.data.keyword.cloud_notm}} の [Kubernetes クラスター・ページ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://console.cloud.ibm.com/containers-kubernetes/clusters) で確認できます。このページでは、すべてのクラスターがテーブルにリストされています。

Kubernetes のバージョンがバージョン 1.11 以降でない場合は、以下の手順を実行して、クラスターの Kubernetes バージョンを更新する必要があります。

1. 行末にあるオーバーフロー・メニュー・アイコンをクリックし、**「バージョンの更新 (Update version)」**を選択します。 このプロセスは完了までに約 1 時間かかります。 バージョンが正常に更新されると、クラスターの更新バージョンが**「Kubernetes バージョン」**列に表示されます。  
2. Kubernetes バージョンのドロップダウン・リストから 1.11 以降の Kubernetes バージョンを選択し、**「更新」**をクリックします。
3. クラスターをクリックし、**「ワーカー・ノード」**タブに移動します。 更新するワーカー・ノードの前のチェック・ボックスを選択し、ポップアップ・メニュー・バーから**「Kubernetes の更新 (Update Kubernetes)」**をクリックします。 クラスターに複数のワーカー・ノードが含まれている場合、そのすべてを更新する必要があります。

  ワーカー・ノードを更新すると、アプリとサービスにダウン時間が発生する可能性があります。 ワーカー・ノード・マシンが再イメージ化されるので、[ポッドの外部に保管 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview) されていないデータは削除されます。
  {:important}

![Kubernetes バージョンの更新](../images/update_k8s_version.gif)

{{site.data.keyword.IBM_notm}} Kubernetes サービス・クラスターおよびワーカー・ノードの Kubernetes バージョンの更新方法について詳しくは、[クラスター、ワーカー・ノード、アドオンの更新 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/containers/cs_cluster_update.html#update) を参照してください。  

更新が完了するのを待ってから、[{{site.data.keyword.blockchainfull_notm}} Platform 2.0 のデプロイメントを再開](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps)できます。

## サービス・インスタンスの削除
{: #ibp-v2-deploy-iks-delete-service-instance}

サービス・インスタンスが不要になった場合は、Kubernetes クラスターから削除してリソースを解放できます。IBM Cloud ユーザー・インターフェースを使用して、{{site.data.keyword.blockchainfull_notm}} Platform 2.0 無料ベータ版サービス・インスタンスを削除できます。

1. [{{site.data.keyword.cloud_notm}} ダッシュボード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/ "ダッシュボード") にナビゲートします。
2. **「リソースの要約」**タイルを見つけて、**「サービス」**をクリックします。
3. **「サービス」**ツイスティーの下で、削除するサービス・インスタンスを見つけて、「アクション」メニューで**「削除」**をクリックします。

サービス・インスタンスの削除に失敗した場合、Kubernetes クラスターにアクセスできないことが原因である可能性があります。そのような場合は、[サポート・チケット](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases)を開いて、サービス・インスタンスの削除を依頼してください。

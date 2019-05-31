---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: blockchain network, Enterprise Plan, getting started tutorial

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# エンタープライズ・プランの概説
{: #getting-started-with-enterprise-plan}

{{site.data.keyword.blockchainfull}} Platform エンタープライズ・プランは、高いセキュリティー、完全性、スケーラビリティー、パフォーマンスを備えたブロックチェーン・ネットワークを提供します。 機能を完備したネットワークを迅速にプロビジョンし、ネットワーク・モニター (GUI ダッシュボード) を使用して[チェーンコード](/docs/services/blockchain/glossary.html#glossary-chaincode)とアプリケーションをすぐに実行できます。ネットワークを最初から設計して構成する必要はありません。
{:shortdesc}

**注**: {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランは実稼働環境を提供します。 開発/テスト用の環境が必要な場合は、[スターター・プランについて](/docs/services/blockchain/starter_plan.html#starter-plan-about)を参照してください。

このチュートリアルでは、{{site.data.keyword.IBM_notm}} が提供する安全で可用性が高い環境でホストされるエンタープライズ・プラン・ネットワークを実現するために従う必要のある前提条件と手順について説明します。

以下のステップは、複数のネットワーク・[メンバー](/docs/services/blockchain/glossary.html#glossary-member)を持つエンタープライズ・プラン・ネットワークを起動する基本的なフローを示しています。
1. ネットワーク・メンバーの特殊なタイプである**ネットワーク・イニシエーター**が、ネットワークを作成し、ガバナンス・ポリシーを定義します。 次にネットワーク・イニシエーターは、このネットワークにネットワーク・メンバーとして参加するよう他の[組織](/docs/services/blockchain/glossary.html#glossary-organization)を招待できます。 詳しくは、[ネットワークの作成](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network)を参照してください。
2. 招待された**ネットワーク・メンバー**は、{{site.data.keyword.blockchain}} ネットワークへの参加手順が記載された E メール通知を受け取ります。 この E メール通知の手順のほかに、[ネットワークへの参加](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw)の手順に従うこともできます。
3. すべての**ネットワーク・メンバー**は、ネットワークを作成した後またはネットワークに参加した後に、ネットワーク・モニターを開始し、ネットワーク・リソースを構成して管理することができます。 ネットワーク・メンバーのグループを指定して[チャネル](/docs/services/blockchain/glossary.html#glossary-channel)をセットアップし、チャネル・メンバーのみがアクセスできるチャネル固有の台帳でプライベート・トランザクションを実行できます。 ネットワーク・モニターでは、自分のピアをチャネルに参加させ、ピアにチェーンコードをインストールしてインスタンス化することもできます。 詳しくは、[ネットワーク・リソースと環境の構成](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-config)を参照してください。
4. **アプリケーション開発者**は、アプリケーションを開発した後、アプリケーションとネットワークの間の対話を有効にします。 詳しくは、[アプリケーションがネットワークと対話できるようにする](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-enable-apps)を参照してください。
5. **ネットワーク・オペレーター**がネットワーク・モニターでチャネル上のトランザクションをモニターします。 詳しくは、[ネットワーク・リソースのモニター](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-monitor-resources)を参照してください。


## ネットワークの作成
{: #getting-started-with-enterprise-plan-create-network}

始める前に、{{site.data.keyword.cloud_notm}} で [{{site.data.keyword.blockchain}} Platform サービス・インスタンス ![外部リンク・アイコン ](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) を作成しておく必要があります。 {{site.data.keyword.cloud_notm}} ID でログインする必要があります。 ID がない場合は、**「登録して作成」**ボタンをクリックします。 インスタンスのサービスと資格情報の名前は、見分けやすいものに変更してください。 {{site.data.keyword.blockchain}} ネットワークをデプロイする {{site.data.keyword.cloud_notm}} の地域、組織、スペースを選択します。 次に、料金設定プランの表から**「エンタープライズ・メンバーシップ・プラン」**を選択し、**「作成」**ボタンをクリックします。

{{site.data.keyword.blockchain}} Platform サービス・インスタンスが、[{{site.data.keyword.cloud_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/resources "{{site.data.keyword.cloud_notm}} サービス・ダッシュボード") に表示されます。

ネットワーク・イニシエーターである場合は、**「ネットワークの作成」**ボタンをクリックして {{site.data.keyword.blockchain}} ネットワークを開始します。 ウィザードに従って、ネットワークとリソースの基本的な構成を完了します。
![「ネットワークの作成」ウィザード](images/create_network_name.png "「ネットワークの作成」ウィザード")

1. 「始めに」画面で、ネットワークの名前を指定し、{{site.data.keyword.cloud_notm}} 組織の場所を選択し、機関の名前を追加します。 他のネットワーク・メンバーを招待する場合、招待されたメンバーはこのネットワーク名を探して参加することになります。 **「次へ」**をクリックします。
2. (オプション) 「メンバーの招待」画面で、ネットワークに招待する機関の名前と E メール・アドレスを入力します。 指定する機関の名前は、正式なタイトルではありません。 この名前は、機関を簡単に識別できるようにするためのもので、機関がネットワークに参加するときに変更できます。 ネットワークに参加できるメンバーは、自分を含めて最大で 15 メンバーであることに注意してください。 この手順はオプションです。ネットワークへのメンバーの招待は、後でネットワーク・モニターから行うことができます。 **「次へ」**をクリックします。
	ネットワークの作成手順をすべて終えると、招待されたメンバーは、招待に関する E メール通知を受け取ります。
3. 「ガバナンス・ルールの定義」画面で、メンバーシップ、チャネル作成、チェーンコードについてのポリシーを設定します。 デフォルトでは、すべてのネットワーク・メンバーが、ネットワークへの他のメンバーの招待、チャネルの作成、チェーンコードのインスタンス化を行えます。 現在、デフォルトのガバナンス・ポリシーをネットワークで使用しています。 **「次へ」**をクリックします。
4. 「サマリーの確認」画面で、ネットワーク構成を確認します。 変更を加える場合は、セクション・ヘッダーの横の**「編集」**をクリックするか、**「前へ」**ボタンをクリックして前の画面に戻ります。 ネットワーク構成が完了したら、**「完了」**をクリックします。
5. 「ネットワークが作成されました」画面で、ネットワークが正常に作成されたことが通知されます。 ネットワーク・リソースを構成する場合は、**「[ピア](/docs/services/blockchain/glossary.html#glossary-peer)の追加」**をクリックし、ネットワーク・モニターを開く場合は**「モニターを開始する (Enter Monitor)」**を直接クリックします。 また、後でネットワーク・モニターでピアを追加することもできます。 ピアについて詳しくは、[ピアの追加](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-peers)を参照してください。

これで、次のネットワーク・リソースに対応した {{site.data.keyword.blockchain}} ネットワークが正常にデプロイされます。
* メンバー固有の認証局 (CA)
* デフォルトのガバナンス・ポリシー
* 最大 15 のネットワーク・メンバー
* 順序付けサービス 3 つ、中間 CA ノード 2 つ
* メンバーごとに最大 3 つの小さなピア
* 異常終了フォールト・トレラント順序付けサービス
* 最大 150 のチャネル
* ネットワーク・モニター内のメンバーあたり最大 10 個のチェーンコード・インスタンス


## ネットワークへの参加
{: #getting-started-with-enterprise-plan-join-nw}

ネットワークを作成する場合と同じように、{{site.data.keyword.cloud_notm}} で [{{site.data.keyword.blockchain}} Platform サービス・インスタンス ![外部リンク・アイコン ](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) を作成する必要があります。 {{site.data.keyword.cloud_notm}} ID でログインする必要があります。 ID がない場合は、**「登録して作成」**ボタンをクリックします。 インスタンスのサービスと資格情報の名前は、見分けやすいものに変更してください。 {{site.data.keyword.blockchain}} ネットワークをデプロイする {{site.data.keyword.cloud_notm}} の地域、組織、スペースを選択します。 次に、料金設定プランの表から**「エンタープライズ・メンバーシップ・プラン」**を選択し、**「作成」**ボタンをクリックします。

{{site.data.keyword.blockchain}} Platform サービス・インスタンスが、[{{site.data.keyword.cloud_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/resources "{{site.data.keyword.cloud_notm}} サービス・ダッシュボード") に表示されます。

招待されたネットワーク・メンバーである場合は、**「保留中の招待 ->」**ボタンをクリックして、ドロップダウン・リストから参加するネットワークを選択し、**「ネットワークへの参加!」**ボタンをクリックします。 ウィザードに従って、ネットワークの基本的な構成を確認し、自分のネットワーク・リソースを構成します。
![「ネットワークへの参加」ウィザード](images/join_network_name.png "「ネットワークへの参加」ウィザード")

1. 「それでは始めましょう」画面で、組織の名前を入力して**「次へ」**をクリックします。
2. 「ガバナンス・ルールの確認」画面で、メンバーシップ、チャネル作成、チェーンコードについてのネットワークのガバナンス・ポリシーを確認します。 **「次へ」**をクリックします。
3. (オプション)「ピアの追加」画面で、追加するピアの数量を選択します。 **「次へ」**をクリックします。 ネットワーク内の各メンバーは、最大 3 つのピアを追加できます。 この手順はオプションです。ピアの追加は、後でネットワーク・モニターから行うことができます。 ピアについて詳しくは、[ピアの追加](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-peers)を参照してください。
4. 「ネットワーク・サマリーの確認」画面で、ネットワーク構成を確認します。 変更を加える場合は、**「前へ」**ボタンをクリックして前の画面に戻ります。 リソース構成が完了したら、**「完了」**をクリックします。 ネットワークに正常に参加したことが通知されます。 その後、**「モニターの入力」**をクリックしてネットワーク・モニターを開くことができます。

<!-- or click **Create a Channel** to initiate a channel creation request. You can create channels later in the Network Monitor. For more information, see [Channels](v10_dashboard.html#channels).  -->


## ネットワーク・リソースと環境の構成
{: #getting-started-with-enterprise-plan-config}

1. {{site.data.keyword.blockchain}} ネットワークを作成したら、またはネットワークに参加したら、ネットワーク・モニターを開始します。 ネットワーク・モニターは、ネットワーク状況情報を管理および追跡できる GUI ダッシュボードです。 詳しくは、[ネットワーク・モニター](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard)を参照してください。
2. 自分のピアをネットワークに追加します。 既に十分な数のピアを追加してある場合、この手順はスキップしてください。 ピアとは、チェーンコードを実行し、アプリケーションと対話するエンドポイントです。 「概要」画面で**「ピアの追加」**をクリックし、ピアの数量とサイズを選択します。 詳しくは、[概要](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-overview)を参照してください。
3. チャネルをセットアップします。 同じチャネルのすべてのメンバーに、データの分離と機密性を実現する、チャネル固有の台帳がプロビジョンされます。 チャネルの作成方法について詳しくは、[チャネルの作成](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel)を参照してください。 チャネルに参加するように招待されたチャネル・メンバーの場合は、チャネルに参加するためのウィザードへのリンクが記載された E メール通知を受け取ります。
4. ピアをチャネルに参加させます。 チャネルと関連付けられたピアのみが、チャネルの台帳にアクセスできます。 詳しくは、[チャネル](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-channels)を参照してください。
5. チェーンコードをインストールし、インスタンス化します。 すべてのチャネル・メンバーが、チェーンコードを実行する予定のすべてのピアに、名前とバージョンが同じである同一のチェーンコードをインストールする必要があります。 チェーンコードをインストールしたら、それを使用する前にチャネル上でインスタンス化する必要があります。 詳しくは、[チェーンコードのインストール、インスタンス化、および更新](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode)を参照してください。

**注**: 高可用性を実現するには、各組織が少なくとも 2 つのピアを購入し、1 つのチャネル内で各参加メンバーが少なくとも 2 つのピアに参加する必要があります。


## ネットワーク資格情報と接続プロファイルの取得
{: #getting-started-with-enterprise-plan-retrieve-credentials}

{{site.data.keyword.cloud_notm}} でエンタープライズ・プラン・ネットワークを作成した後、サービス・インスタンス・ページまたはネットワーク・モニターからネットワーク資格情報と接続プロファイルを取得できます。

### サービス・インスタンス・ページからの取得
{: #getting-started-with-enterprise-plan-retrieve-svc-ins}
サービス・インスタンスを作成した直後に、サービス・インスタンス・ページが表示されます。 また、[{{site.data.keyword.cloud_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/resources "{{site.data.keyword.cloud_notm}} サービス・ダッシュボード") でサービスをクリックして、サービス・インスタンス・ページを開くこともできます。

以下の手順でサービス資格情報を取得します。
1. サービス・インスタンス・ページで、左側のナビゲーターの**「サービス資格情報」**をクリックして、「サービス資格情報」画面を表示します。
2. 「サービス資格情報」画面で**「新規資格情報」**をクリックします。
3. 「新規資格情報の追加」画面で、資格情報に名前を付けて、「インラインの構成パラメーターの追加」フィールドに **{"type": "service_instance_token"}** と入力します。 **「追加」**をクリックすると、新規資格情報がテーブルに追加されます。 「アクション」列の下の**「資格情報の表示」**をクリックすると、資格情報の詳細を表示できます。 この資格情報には、API に権限を与えるために使用できる API キーとシークレットが含まれています。

### ネットワーク・モニターでの取得
{: #getting-started-with-enterprise-plan-retrieve-nw}
ネットワーク・モニターの「API」画面でネットワーク資格情報を確認することができます。 API の使用について詳しくは、[Swagger を使用した API の試行](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger)を参照してください。

ネットワーク・モニターの「概説」画面で接続プロファイルを取り出すことができます。 「概説」画面の**「接続プロファイル」**ボタンをクリックすると、接続プロファイルが新しいページに表示されます。


## アプリケーションがネットワークと対話できるようにする
{: #getting-started-with-enterprise-plan-enable-apps}
アプリケーションは SDK API を活用して、{{site.data.keyword.blockchain}} ネットワークのリソースと対話します。 アプリケーションがトランザクション要求で最終的にピアをターゲットにできるように、ネットワーク・リソースの API エンドポイントの情報をアプリケーションに追加する必要があります。 その後、その API エンドポイントの情報をネットワーク・モニターから追加することができます。 アプリケーションは、ローカル・ファイル・システムまたは {{site.data.keyword.cloud_notm}} でホストできます。 詳しくは、
[アプリケーションの開発](/docs/services/blockchain/v10_application.html#dev-app)を参照してください。

## ネットワーク・リソースのモニター
{: #getting-started-with-enterprise-plan-monitor-resources}
トランザクションがアプリケーションからトリガーされたら、トランザクションの状況情報をネットワーク・モニターで確認できます。 ネットワークのモニターについて詳しくは、[ネットワークのモニター](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network)を参照してください。

## ネットワークからの離脱
{: #getting-started-with-enterprise-plan-leave-nw}
ネットワークから離脱する場合は、ブロックチェーン・サービス・インスタンスを {{site.data.keyword.cloud_notm}} ダッシュボードから削除します。

ネットワークから離脱する前に、ネットワークのいずれのチャネルのメンバーでもないことを確認してください。 そうでない場合、ネットワークを離脱する際にエラーが発生します。 チャネル・メンバーを除去する場合は、チャネル更新処理を実行する必要があります。 チャネル更新処理について詳しくは、[チャネルの更新](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)を参照してください。
{:note}

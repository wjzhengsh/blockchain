---

copyright:
  years: 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# スターター・プラン・ネットワークの管理
{: #getting-started-with-starter-plan}


***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform Starter Plan は、構成済みのブロックチェーン・ネットワークをクリック 1 回でユーザーに提供します。 <!--offers you a free trial of 30 days and --> デフォルトでは 2 つの[組織](glossary.html#organization)、それぞれの組織ごとに 1 つの [ピア](glossary.html#peer)、そして 1 つの[チャネル](glossary.html#channel)で構成される許可制ネットワークをプロビジョンします。 ネットワークが作成されたら、これを拡張してさらに組織やピアをネットワークに追加できます。 <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**注**: {{site.data.keyword.blockchainfull}} Platform Starter Plan は開発/テスト環境であり、実動ワークロードには適していません。 実稼働環境が必要な場合は、[エンタープライズ・プランについて](enterprise_plan.html)を参照してください。

スターター・プランを使用すると、{{site.data.keyword.blockchainfull_notm}} プラットフォームのスキルを習得し、サンプル・アプリケーションを実行し、独自のアプリケーションをテストし、複数組織のシナリオをシミュレートできます。  この入門チュートリアルでは、スターター・プラン・ネットワークを作成して使用するために従う必要のある前提条件と手順を紹介します。

{{site.data.keyword.blockchainfull_notm}} プラットフォームおよびブロックチェーンが初めてという方は、[用語集](glossary.html)を参照してこの資料の用語をよく理解し、[Hyperledger Fabric の資料 ![「外部リンク」アイコン](images/external_link.svg "「外部リンク」アイコン")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/blockchain.html) を参照してブロックチェーンについて理解してください。


## ネットワークの作成
{{site.data.keyword.blockchainfull_notm}} プラットフォーム・サービス・インスタンスを作成するとすぐに、デフォルト構成のスターター・プラン・[ネットワーク](glossary.html#network)を取得できます。

1. {{site.data.keyword.cloud_notm}} カタログで [ブロックチェーン・サービス![「外部リンク」アイコン](images/external_link.svg "「外部リンク」アイコン")](https://console.bluemix.net/catalog/services/blockchain) を探します。   
    **注**: {{site.data.keyword.cloud_notm}} の有料アカウントを使用してログインする必要があります。 アカウントがない場合は、**「登録して作成」**ボタンをクリックします。 無料トライアル・アカウントを作成した後、{{site.data.keyword.cloud_notm}} コンソールで**「管理」** > **「請求および使用量」** > **「請求処理」**に移動し、**「クレジット・カードの追加」**をクリックして、**「従量課金 (PAYG)」**タイプにアップグレードします。
2. {{site.data.keyword.cloud_notm}} の地域を選択し、ネットワークを作成します。
3. Cloud Foundry 組織とスペースを選択し、ネットワークを作成します。
4. 価格プランの表から**「スターター・メンバーシップ・プラン (Starter Membership Plan)」**を選択します。
5. **「作成」**ボタンをクリックします。 ネットワークに参加するよう招待されている場合は、ポップアップの「ようこそ」パネルが表示されます。 ネットワークを作成するには、**「ネットワークに進む (Proceed to your network)」**を選択し、**「続行」**をクリックします。 ネットワークに参加するには、[ネットワークへの参加](#joining-a-network)のステップ 5 を参照してください。
  これで、デフォルト構成でスターター・プラン・ネットワークを使用する準備ができました。 ネットワークは 1 つの順序付けプログラム (「SOLO」順序付けサービスと呼ばれる)、2 つの組織、1 つの CA、および組織ごとに 1 つのピアによって実行されます。 デフォルト・チャネルも作成されます。
6. **「起動」**ボタンをクリックします。

ブロックチェーン・サービス・インスタンスが、[{{site.data.keyword.Bluemix_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} サービス・ダッシュボード") に表示されます。


## メンバーの招待
他の組織と[トランザクション](glossary.html#transaction)を相互に実行できるように、 その[組織](glossary.html#organization)をスターター・プラン・ネットワークに[メンバー](glossary.html#member)として招待し、参加させることができます。 さらに、学習やテストのためにスターター・プランを使用する場合は、自分自身のメンバーをネットワークに追加することで、複数組織のネットワークをシミュレートできます。

1. ネットワーク・モニターの「メンバー」画面で、**「メンバーの招待 (Invite Members)」**ボタンをクリックします。
2. 「メンバーの招待 (Invite member)」ウィンドウが開きます。
    - 別の組織を招待する場合は、「メンバーの招待 (Invite a member)」を選択します。  招待する組織の名前とオペレーターの E メール・アドレスを指定します。  また、「メモの追加 ( Add a note)」フィールドに、招待に含める追加情報を入力することもできます。  **「招待の送信」**ボタンをクリックします。  招待された組織は招待メールを受け取り、その E メールの指示に従ってネットワークに参加できます。
    - チャネルに追加できる組織をさらに追加する場合は、「メンバーの追加 (Add a member)」を選択します。  新しい組織の名前を指定します。 オプションで組織にピアを追加できますが、これは後でネットワーク・モニターでも追加できます。  **「作成」**ボタンをクリックします。 新しい組織にピアを追加する場合は、ピアを表示するためにこの新しい組織に切り替える必要があります。 組織の切り替えについて詳しくは、[組織の切り替え](dashboard.html#switch-organizations)を参照してください。


## ネットワークへの参加
ネットワーク・イニシエーターによって招待された場合は、ネットワークへの参加方法に関する指示が記載された E メール通知を受け取ります。 E メールの指示に従って、ネットワーク内のメンバーの 1 員になります。

{{site.data.keyword.cloud_notm}} で、スターター・プラン・メンバーシップ・オプションを使用して [{{site.data.keyword.blockchain}} プラットフォーム・サービス・インスタンス![「外部リンク」アイコン](images/external_link.svg "「外部リンク」アイコン")](https://console.bluemix.net/catalog/services/blockchain) を作成する必要があります。

1. {{site.data.keyword.cloud_notm}} アカウントでログインします。 アカウントがない場合は、**「登録して作成」**ボタンをクリックします。
2. {{site.data.keyword.blockchain}} ネットワークを保管する Cloud Foundry 組織を選択します。
3. 価格プランの表から**「スターター・メンバーシップ・プラン (Starter Membership Plan)」**を選択します。
4. **「作成」**ボタンをクリックします。 サービス・インスタンス・ページが開き、ポップアップで「ようこそ」パネルが表示されます。 ネットワークを結合するか、独自のネットワークを作成し続けるかを選択できます。 ネットワークを作成するには、[ネットワークの作成](#creating-a-network)のステップ 4 を参照してください。
5. ウェルカム・パネルで、**「既存のネットワークに参加 (Join existing network)」**を選択し、参加するネットワークをドロップダウン・リストから選択してから、**「続行」**をクリックします。

ブロックチェーン・サービス・インスタンスが、[{{site.data.keyword.cloud_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} サービス・ダッシュボード") に表示されます。

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
-->


## ネットワーク資格情報および接続プロファイルの取得
{{site.data.keyword.cloud_notm}} でスターター・プラン・ネットワークを作成した後、サービス・インスタンス・ページまたはネットワーク・モニターからネットワーク資格情報と接続プロファイルを取得できます。

### サービス・インスタンス・ページからの取得
サービス・インスタンスを作成した直後に、サービス・インスタンス・ページが表示されます。 また、[{{site.data.keyword.cloud_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} サービス・ダッシュボード") でサービスをクリックして、サービス・インスタンス・ページを開くこともできます。

以下の手順でサービス資格情報を取得します。
1. サービス・インスタンス・ページで、左側のナビゲーターの**「サービス資格情報」**をクリックして、「サービス資格情報」画面を表示します。
2. 「サービス資格情報」画面で**「新規資格情報」**をクリックします。
3. 「新規資格情報の追加」画面で、資格情報に名前を付けて、「インラインの構成パラメーターの追加」フィールドに **{"type": "service_instance_token"}** と入力します。 **「追加」**をクリックします。 新しい資格情報がテーブルに追加されます。 「アクション」列の下の**「資格情報の表示」**をクリックすると、資格情報の詳細を表示できます。 この資格情報には、API に権限を与えるために使用できる API キーとシークレットが含まれています。  

![ネットワーク資格情報の取得](images/service_credentials.gif "ネットワーク資格情報の取得")

### ネットワーク・モニターでの取得
ネットワーク・モニターの「API」画面でネットワーク資格情報を確認することができます。 API の使用について詳しくは、[Swagger API を使用したネットワークとの対話](howto/swagger_apis.html)を参照してください。

ネットワーク・モニターの「概説」画面で接続プロファイルを取り出すことができます。 「概説」画面の**「接続プロファイル」**ボタンをクリックすると、接続プロファイルが新しいページに表示されます。


## サンプル・アプリケーションのデプロイ
スターター・プランを使用すると、数回のクリックだけでサンプル・アプリケーションをネットワークにデプロイできます。 サンプルを使用すると、チェーンコードとアプリケーションがネットワーク内でどのように機能するかを簡単に学習できます。

詳しくは、
[サンプル・アプリケーションの開発](howto/prebuilt_samples.html)を参照してください。


## カスタマイズされたビジネス・ネットワークの開発とデプロイ
スターター・プランは、開発者環境と Hyperledger Composer 開発者ツール・セットを開発する {{site.data.keyword.blockchainfull_notm}} プラットフォームを統合します。 ユーザーのビジネス・ニーズに基づいて、独自のブロックチェーン・ネットワークを開発できます。  ビジネス用のネットワークを開発した後に、そのビジネス・ネットワークをスターター・プラン・ネットワークにデプロイできます。

詳しくは、[ネットワークの開発](develop.html)および[スターター・プランによるビジネス・ネットワークのデプロイ](develop_starter.html)を参照してください。


## ネットワーク・リソースのモニター
アプリケーションがトランザクションを要求する場合、ネットワーク・モニターでトランザクション状況情報を表示できます。 ネットワークのモニターについて詳しくは、[ネットワークのモニター](howto/monitor_network.html)を参照してください。


## ネットワークのリセット
カスタマイズした構成や実行中のチェーンコード、またはデプロイしたアプリケーションをクリーンアップする場合は、作成したネットワークを最初のデフォルト構成に戻すことができます。  詳しくは、[ネットワークのリセット](dashboard.html#reset-network)を参照してください。


## スターター・プランからエンタープライズ・プランへのマイグレーション
{: #migrate}

スターター・プラン・ネットワークに対してテストする `.bna`、チェーンコード、およびアプリケーションを、エンタープライズ・プラン・ネットワークにデプロイできます。

ビジネス・ネットワーク・アーカイブ・ファイル (`.bna`) がある場合は、[エンタープライズ・プランへのビジネス・ネットワークのデプロイ](./develop_enterprise.html)の説明に従ってください。 `.bna` ファイルがない場合は、`composer network download` コマンドを使用してスターター・プラン・インスタンスから取得します。 `composer network download` コマンドについて詳しくは、[Hyperledger Composer コマンド・ラインの資料![「外部リンク」アイコン](images/external_link.svg "「外部リンク」アイコン")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window} を参照してください。

チェーンコードは `.bna` ファイルに似ていますが、外部で開発されます。 スターター・プラン・ネットワークでテストするチェーンコードをエンタープライズにデプロイするには、[チェーンコードのインストール、インスタンス化、および更新](howto/install_instantiate_chaincode.html#installchaincode)の説明に従ってください。

<!--
As you can see in [Deploying sample applications](howto/prebuilt_samples.html), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](howto/prebuilt_samples.html#deploy_sample_applications_manually).
-->

スターター・プラン・ネットワークにサンプル・アプリケーションをデプロイし、そのアプリケーションをエンタープライズ・プラン・ネットワークにデプロイする場合は、fork したアプリケーション・リポジトリーを新規リポジトリーにコピーしてから、サンプル・アプリケーションの説明に従ってエンタープライズ・プラン・ネットワークにデプロイすることができます。


## ネットワークの削除または離脱
ネットワークを削除または離脱する場合は、{{site.data.keyword.cloud_notm}} ダッシュボードからブロックチェーン・サービス・インスタンスを削除します。

**注**: ネットワークから離脱する前に、ネットワークのいずれのチャネルのメンバーでもないことを確認してください。 そうでない場合、ネットワークを離脱する際にエラーが発生します。 チャネル・メンバーを除去する場合は、チャネル更新処理を実行する必要があります。 チャネル更新処理について詳しくは、[チャネルの更新](howto/create_channel.html#updating-a-channel)を参照してください。


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/){:new_window}.
-->

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
{:tip: .tip}

# スターター・プランの概説
{: #getting-started-with-starter-plan}


***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform Starter Plan は、構成済みのブロックチェーン・ネットワークをクリック 1 回でユーザーに提供します。 デフォルトでは 2 つの[組織](glossary.html#organization)、それぞれの組織ごとに 1 つの [ピア](glossary.html#peer)、そして 1 つの[チャネル](glossary.html#channel)で構成される許可制ネットワークをプロビジョンします。 ネットワークが作成されたら、これを拡張してさらに組織やピアをネットワークに追加できます。 これらのネットワークは {{site.data.keyword.blockchainfull_notm}} Platform を初めて使用する初心者ユーザーを対象としています。<!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

スターター・プランを使用すると、{{site.data.keyword.blockchainfull_notm}} プラットフォームのスキルを習得し、サンプル・アプリケーションを実行し、独自のアプリケーションをテストし、複数組織のシナリオをシミュレートできます。 この入門チュートリアルは、スターター・プランを使用してブロックチェーン・ネットワークで開発およびトランザクションを開始するステップを案内します。

{{site.data.keyword.blockchainfull_notm}} Platform およびブロックチェーンが初めてという方は、オープン・ソースの [Hyperledger Fabric](reference/v10_fabric.html) 上に構築されたネットワークの[基礎コンポーネントの概要](blockchain_component_overview.html)を参照し、ブロックチェーンについての詳細を確認することができます。また、[Hyperledger Fabric の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/blockchain.html "Blockchain の紹介") も確認してください。

**注**: {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan は開発/テスト環境であり、実動ワークロードには適していません。 実稼働環境が必要な場合は、[エンタープライズ・プランについて](enterprise_plan.html)を参照してください。

## 概要

**共同事業体の構築**

ブロックチェーンを使用して構築を行うための最初のステップは、トランザクションにブロックチェーンを使用することを希望する組織の共同事業体を形成し、ネットワークに招待することです。テクノロジーを試験しているスターター・プラン・ユーザーは、新規組織自体を作成することにより、複数組織の共同事業体をシミュレートすることができます。

- [スターター・プラン・ネットワークの作成](#creating-a-network)
- [ネットワークへの組織の招待](#inviting-members)
- [スターター・プラン・ネットワークへの参加](#joining-a-network)

ネットワークに参加した組織の共同事業体内では、チャネルを作成することでこれらの組織のセットとプライベートでトランザクションを行うことができます。

- [チャネルを作成する](#create-channels)

**ネットワークおよびアプリケーションの開発**

共同事業体を形成したら、チェーンコード (スマート・コントラクトとも呼ばれる) を作成する必要があります。これは、ネットワークのビジネス・ロジックを含み、ブロックチェーン台帳のデータとの対話を可能にするものです。その後、クライアント・サイド・アプリケーションからネットワークにトランザクションを送信するには、Fabric SDK をこれらのスマート・コントラクトとともに使用することが必要になります。

- [チェーンコードの開発](#develop-chaincode)
- [Fabric SDK を使用したアプリケーションの開発](#develop-apps)

また、Hyperledger Fabric のオープン・ソース・ツール・セットである Hyperledger Composer を使用して、ビジネス・ロジックを開発し、ロジックをネットワークにデプロイして、アプリケーションからこれに接続することができます。

- [カスタマイズされたビジネス・ネットワークの Hyperledger Composer による開発とデプロイ](#develop-composer)

{{site.data.keyword.blockchainfull_notm}} Platform には、Hyperledger Composer を使用して構築されたサンプル・アプリケーションと、チェーンコードを使用して構築されたサンプルが用意されており、これらを数クリックのみでスターター・プラン・ネットワークにデプロイできます。

- [サンプル・アプリケーションのデプロイ](#sample-applications)

**ネットワークの運用と管理**

{{site.data.keyword.blockchainfull_notm}} Platform には、ブロックチェーン・ネットワークのメンバーシップ、ライフサイクル、および正常性を管理するために使用できるツールと API が用意されています。これらの機能の多くは、ネットワーク・モニター・ユーザー・インターフェースを使用してアクセス可能です。

- [ネットワーク・リソースのモニター](#monitoring-resources)
- [ネットワーク資格情報および接続プロファイルの取得](#retrieving-network-credentials)
- [Swagger API を使用したネットワークの管理](#swagger)
- [ネットワークのリセット](#resetting-network)
- [スターター・プランからエンタープライズ・プランへのマイグレーション](#migrate)
- [ネットワークの削除または離脱](#delete-network)


## ネットワークの作成
{: #creating-a-network}

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
{: #inviting-members}

他の組織と[トランザクション](glossary.html#transaction)を相互に実行できるように、 その[組織](glossary.html#organization)をスターター・プラン・ネットワークに[メンバー](glossary.html#member)として招待し、参加させることができます。 さらに、学習やテストのためにスターター・プランを使用する場合は、自分自身のメンバーをネットワークに追加することで、複数組織のネットワークをシミュレートできます。

1. ネットワーク・モニターの「メンバー」画面で、**「メンバーの招待 (Invite Members)」**ボタンをクリックします。
2. 「メンバーの招待 (Invite member)」ウィンドウが開きます。
    - 別の組織を招待する場合は、「メンバーの招待 (Invite a member)」を選択します。  招待する組織の名前とオペレーターの E メール・アドレスを指定します。  また、「メモの追加 ( Add a note)」フィールドに、招待に含める追加情報を入力することもできます。  **「招待の送信」**ボタンをクリックします。  招待された組織は招待メールを受け取り、その E メールの指示に従ってネットワークに参加できます。
    - チャネルに追加できる組織をさらに追加する場合は、「メンバーの追加 (Add a member)」を選択します。  新しい組織の名前を指定します。 オプションで組織にピアを追加できますが、これは後でネットワーク・モニターでも追加できます。  **「作成」**ボタンをクリックします。 新しい組織にピアを追加する場合は、ピアを表示するためにこの新しい組織に切り替える必要があります。 組織の切り替えについて詳しくは、[組織の切り替え](v10_dashboard.html#switch-organizations)を参照してください。


## ネットワークへの参加
{: #joining-a-network}

ネットワーク・イニシエーターによって招待された場合は、ネットワークへの参加方法に関する指示が記載された E メール通知を受け取ります。 E メールの指示に従って、ネットワーク内のメンバーの 1 員になります。

{{site.data.keyword.cloud_notm}} で、スターター・プラン・メンバーシップ・オプションを使用して [{{site.data.keyword.blockchain}} プラットフォーム・サービス・インスタンス![「外部リンク」アイコン](images/external_link.svg "「外部リンク」アイコン")](https://console.bluemix.net/catalog/services/blockchain) を作成する必要があります。

1. {{site.data.keyword.cloud_notm}} アカウントでログインします。 アカウントがない場合は、**「登録して作成」**ボタンをクリックします。
2. {{site.data.keyword.blockchain}} ネットワークを保管する Cloud Foundry 組織を選択します。
3. 価格プランの表から**「スターター・メンバーシップ・プラン (Starter Membership Plan)」**を選択します。
4. **「作成」**ボタンをクリックします。 サービス・インスタンス・ページが開き、ポップアップで「ようこそ」パネルが表示されます。 ネットワークを結合するか、独自のネットワークを作成し続けるかを選択できます。 ネットワークを作成するには、[ネットワークの作成](#creating-a-network)のステップ 4 を参照してください。
5. ウェルカム・パネルで、**「既存のネットワークに参加 (Join existing network)」**を選択し、参加するネットワークをドロップダウン・リストから選択してから、**「続行」**をクリックします。

ブロックチェーン・サービス・インスタンスが、[{{site.data.keyword.cloud_notm}} サービス・ダッシュボード ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} サービス・ダッシュボード") に表示されます。


## チャネルを作成する
{: #create-channels}

チャネルは、データを他の組織に公開せずにトランザクションを実行するために、一連の組織によって使用されます。スターター・プラン・ネットワークの選択したメンバーと、誰がチャネルを更新できるかに関するポリシーを使用して[チャネル](glossary.html#channel)を作成できます。

詳しくは、[チャネルの作成](howto/create_channel.html#creating-a-channel)を参照してください。 別の組織からチャネルに招待された場合は、招待を受け入れてピアをチャネルに参加させる手順も確認できます。

## チェーンコードの開発
{: #develop-chaincode}

[チェーンコード](glossary.html#chaincode) (スマート・コントラクトとも呼ばれる) は、ブロックチェーン台帳のデータの読み取りおよび更新を可能にするソフトウェアです。チェーンコードは、ブロックチェーン・ネットワークのすべてのメンバーによって合意および検証された実行可能プログラムにビジネス・ロジックを変換できます。

詳しくは、[チェーンコードの開発](howto/develop_chaincode.html)チュートリアルで、チェーンコードの作成を開始する方法やチェーンコードによりアクセス可能な Fabric の機能について学習してください。

## チェーンコードのインストールとインスタンス化
{: #install-instantiate-chaincode}
チャネルに参加し、ビジネス・ロジックを開発した後、チェーンコードをネットワーク内のピアにインストールする必要があります。ネットワーク・モニターを使用して、組織のピア上でチェーンコードをインストールおよびインスタンス化したり、継続的な開発を促進するためにチェーンコードを更新したりできます。

事前作成されたサンプルのデプロイについて詳しくは、[チェーンコードのインストール、インスタンス化、および更新](howto/install_instantiate_chaincode.html)を参照してください。


## Fabric SDK を使用したアプリケーションの開発
{: #develop-apps}

{{site.data.keyword.blockchainfull_notm}} Platform には、Fabric SDK を使用して接続できる API エンドポイントが用意されています。SDK を使用して、アプリケーションからチェーンコードを呼び出し、ブロックチェーン・ネットワークにトランザクションを送信できます。

詳しくは、[アプリケーションの開発](v10_application.html)チュートリアルを参照してください。


## カスタマイズされたビジネス・ネットワークの Hyperledger Composer による開発とデプロイ
{: #develop-composer}

スターター・プランは、開発者環境と Hyperledger Composer 開発者ツール・セットを開発する {{site.data.keyword.blockchainfull_notm}} プラットフォームを統合します。 ユーザーのビジネス・ニーズに基づいて、独自のブロックチェーン・ネットワークを開発できます。  ビジネス用のネットワークを開発した後に、そのビジネス・ネットワークをスターター・プラン・ネットワークにデプロイできます。

詳しくは、[ネットワークの開発](develop.html)および[スターター・プランによるビジネス・ネットワークのデプロイ](develop_starter.html)を参照してください。


## サンプル・アプリケーションのデプロイ
{: #sample-applications}

スターター・プランを使用すると、数回のクリックだけでサンプル・アプリケーションをネットワークにデプロイできます。 これらのサンプルを使用して、チェーンコードおよび Hyperledger Composer ビジネス・ネットワークについての詳細を学習し、これらを独自の開発の開始点として使用することができます。

詳しくは、
[サンプル・アプリケーションの開発](howto/prebuilt_samples.html)を参照してください。


## ネットワーク・リソースのモニター
{: #monitoring-resources}

アプリケーションがトランザクションを要求する場合、ネットワーク・モニターでトランザクション状況情報を表示できます。 ネットワークのモニターについて詳しくは、[ネットワークのモニター](howto/monitor_network.html)を参照してください。


## ネットワーク資格情報および接続プロファイルの取得
{: #retrieving-network-credentials}

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

## Swagger API を使用したネットワークの管理
{: #swagger}

{{site.data.keyword.blockchainfull_notm}} Platform では多数の REST API を Swagger で公開しており、これらを使用してノード、チャネル、ピア、およびネットワーク・メンバーを管理できます。 アプリケーションではこれらの API を使用して、ネットワーク・モニターを使用せずに重要なネットワーク・リソースを制御できます。

詳しくは、[Swagger API を使用したネットワークとの対話](howto/swagger_apis.html)を参照してください。


## ネットワークのリセット
{: #resetting-network}

カスタマイズした構成や実行中のチェーンコード、またはデプロイしたアプリケーションをクリーンアップする場合は、作成したネットワークを最初のデフォルト構成に戻すことができます。 詳しくは、[ネットワークのリセット](v10_dashboard.html#reset-network)を参照してください。


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
{: #delete-network}

ネットワークを削除または離脱する場合は、{{site.data.keyword.cloud_notm}} ダッシュボードからブロックチェーン・サービス・インスタンスを削除します。

**注**: ネットワークから離脱する前に、ネットワーク上のどのチャネルのメンバーでもないことを確認してください。そうでない場合、ネットワークを離脱する際にエラーが発生します。 チャネル・メンバーを除去する場合は、チャネル更新処理を実行する必要があります。 チャネル更新処理について詳しくは、[チャネルの更新](howto/create_channel.html#updating-a-channel)を参照してください。


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.2, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/){:new_window}.
-->

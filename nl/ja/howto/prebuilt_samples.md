---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# サンプル・アプリケーションのデプロイ
{: #overview}

{{site.data.keyword.blockchainfull}} Platform には、ブロックチェーン・ネットワークとアプリケーション開発をより深く理解できるように、デプロイしたりテストしたりといった操作ができるサンプル・アプリケーションが用意されています。
{: shortdesc}

Starter Plan ネットワークをプロビジョンしたら、ネットワーク・モニターでサンプル・アプリケーションをデプロイできます。ネットワーク・モニターにより、ネットワーク上でサンプル・アプリケーションを実行できるようにするためのステップが自動化されます。また、サンプルを段階的に有効にすることで、実際のアプリケーションをデプロイするときに実行するアプリケーション・デプロイメントのプロセス全体を知ることができます。

## Starter Plan でのサンプル・アプリケーションのデプロイ

Starter Plan では、{{site.data.keyword.cloud_notm}} 上の Toolchain サービスを利用して、たった数回のクリックでサンプル・アプリケーションを簡単にデプロイできます。サンプル・アプリケーションをデプロイして起動すると、ブロックチェーン・ネットワーク上で自動的に実行されます。

Starter Plan には、最初に使用するサンプル・アプリケーションが 2 つ用意されています。

* **Marbles**

  Marbles サンプルでは、ユーザーはさまざまなプロパティーを使用して仮想マーブルを作成し、他のユーザーに転送することができます。Marbles について詳しくは、[Marbles のデモ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM-Blockchain/marbles) を参照してください。

* **Vehicle Manufacture**

  Vehicle Manufacture サンプルでは、ユーザーは仮想車両のライフサイクルを体験できます。このサンプルについて詳しくは、[Vehicle Manufacture![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM-Blockchain/vehicle-manufacture) を参照してください。 

サンプル・アプリケーションをデプロイするには、以下のステップを実行します。

1. Starter Plan ネットワークの **ネットワーク・モニター**に入ります。ネットワークがない場合は、[ネットワークの作成](../get_start_starter_plan.html#creating-a-network)を参照してください。

2. ネットワーク・モニターで、「サンプルの試行」画面を開きます。デプロイするサンプル・アプリケーションを選択して、**「Toolchain を使用してデプロイ (Deploy via Toolchain)」**ボタンをクリックします。
<!--
    ![sampleappflow0](../images/sampleappflow0.png)
-->
3. Toolchain サービス構成ウィンドウが開きます。必要なすべてのツールを正しく統合してください。複数の組織がある場合は、正しい組織名を入力する必要があります。組織名は、ネットワークに登録する際に使用する E メール・アドレスでなければなりません。
**ヒント**: Toolchain サービス構成ページを開くためには、ポップアップ・ブロッカーを無効にする必要があります。
<!--
    ![sampleappflow1](../images/sampleappflow1.png)
-->
これが Toolchain を介してデプロイする最初のアプリケーションである場合は、GitHub リポジトリーへのアクセス権限を Toolchain に付与する必要があります。

  ![sampleappflow2](../images/sampleappflow2.png)

  「許可 (Authorize)」ボタンをクリックすると、GitHub に移動します。GitHub アカウントがない場合は、作成する必要があります。アカウント情報を入力して、リポジトリーへのアクセス権限を Toolchain に付与します。このアクセス権限を Toolchain に付与したくない場合は、手動でサンプル・アプリケーションをデプロイできます。詳しくは、[サンプル・アプリケーションの手動デプロイ](#deploy_sample_applications_manually)を参照してください。

5. Toolchain ページの下部にある**「作成」**ボタンをクリックします。この操作でネットワーク・モニターに戻ります。ネットワーク・モニターでは Marbles のデプロイメントが処理中になっています。このプロセスには 5 分から 10 分かかります。
<!--
    ![sampleappflow3](../images/sampleappflow3.png)
-->
デプロイメントが完了したら、Starter Plan ネットワーク上で Marbles サンプルを使い始めることができます。

このプロセスでは、アクセスと制御が可能な fork された GitHub リポジトリーが作成されるので、その fork されたリポジトリー内の Marbles に変更を加えてコミットすることができます。これらのコミットにより Marbles アプリケーションの自動ビルドがトリガーされ、{{site.data.keyword.cloud_notm}} で Marbles アプリケーションをデモできるようになります。

## サンプル・アプリケーションの手動デプロイ
{: #deploy_sample_applications_manually}

ネットワーク・モニターを使用せずにサンプル・アプリケーションをデプロイする場合は、ローカル・ファイル・システムにすべてのソフトウェア前提条件をインストールしておく必要があります。詳しくは、[アプリケーション開発環境のセットアップ](../v10_application.html#setting-up-application-development-environment)を参照してください。

また、Starter Plan または Enterprise Plan のいずれかとともに、{{site.data.keyword.cloud_notm}} 上にブロックチェーン・ネットワークが必要であり、チャネルとそのピアを構成する必要があります。詳しくは、[スターター・プラン・ネットワークの管理](../get_start_starter_plan.html)および [Enterprise Plan ネットワークのガバナンス](../get_start.html)を参照してください。ネットワークをプロビジョンしてネットワーク上にアプリケーションをデプロイできるようになったら、アプリケーションがアクセスするネットワーク・リソースの API エンドポイントを取得します。詳しくは、[アプリケーションへの API エンドポイントの追加](../v10_application.html#adding-network-api-endpoints-to-your-application)を参照してください。

以下のサンプル・アプリケーションのいずれかをネットワークにデプロイできます。

- **Marbles**

  Marbles アプリケーションでは、複数のユーザーがさまざまなプロパティーを使用してマーブルを作成し、他のユーザーに転送することができます。
Marbles アプリケーションは JavaScript で書かれており、チェーンコードは Go で書かれています。

  サンプル・コードと説明が [GitHub の Marbles ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM-Blockchain/marbles) にあります。

  Marbles をローカルにホストするための説明ではなく Bluemix の説明に従い、使用しているネットワークの関連情報を入力してください。Marbles GitHub の画面キャプチャーでは Enterprise Plan UI が表示されていることに注意してください (Enterprise Plan は Marbles をデプロイするための手動パスのみサポートしているため)。Enterprise Plan UI は Starter Plan UI とは少し異なります。それでも、どちらの UI も基本パーツは同じであり、ピアやチャネルなどのサービス資格情報の名前を該当画面で参照できます。

- **Fabcar**

  Fabcar では、台帳の車両記録に対して**照会**や**台帳更新**を行えます。Fabcar は JavaScript で書かれており、チェーンコードは Go で書かれています。

  [GitHub の Fabric 車両 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) にサンプル・コードがあります。説明は [Writing Your First Application ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html) にあります。

- **その他のアプリケーション**

  {{site.data.keyword.cloud_notm}} 内で独自のアプリケーションをホストする方法について詳しくは、[アプリケーションのホスティング](../v10_application.html#hosting-applications)を参照してください。

## サンプル・アプリケーションの削除

Toolchain プロセスを通して取得されたサンプル・アプリケーションを削除するには、UI でサンプルがある場所にナビゲートします。サンプル・アプリケーションはチャネル上でインスタンス化されるため、チャネルでサンプルを見つけることができます。左側のナビゲーションで**「チャネル」** をクリックして、「チャネル」画面を開きます。サンプルがインスタンス化されている該当チャネルをクリックしてから、**「チェーンコード」**をクリックします。このチャネル上でインスタンス化されたチェーンコードが表示されます。

アプリケーションのチェーンコードをクリックすると、**「削除」**タブが表示されます。ただし、**「削除」**をクリックしただけでは、 サンプル・アプリケーションは削除されず、チェーンコード・コンテナーだけが削除されます。それとは別に、{{site.data.keyword.cloud_notm}} ダッシュボードと Toolchain ダッシュボードにナビゲートして、そこでサンプルを削除する必要があります。

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

# {{site.data.keyword.cloud_notm}} Private での {{site.data.keyword.blockchainfull_notm}} Platform コンソールのデプロイ
{: #ibp-console-deploy-icp}

ここでは、{{site.data.keyword.cloud_notm}} Private で独自のインフラストラクチャーに {{site.data.keyword.blockchainfull}} Platform コンソールをデプロイする手順を説明します。 このコンソールは、他のブロックチェーン・コンポーネントと同じ環境にデプロイする必要はありません。
{:shortdesc}

{{site.data.keyword.cloud_notm}} Private で {{site.data.keyword.blockchainfull_notm}} Platform コンソールをデプロイするには、Helm チャートを使用します。 各デプロイメントでは、{{site.data.keyword.cloud_notm}} Private 名前空間にコンソールの単一インスタンスをインストールします。

## 考慮事項
{: #ibp-console-deploy-icp-considerations}

コンソールをデプロイする前に、以下の考慮事項を理解しておいてください。

- ブロックチェーン・コンポーネントが {{site.data.keyword.cloud_notm}} Private または Amazon Web Service (AWS) に既にデプロイされていなければなりません。 まだデプロイしていない場合は、『{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private について』または『IBM Blockchain Platform for AWS について』で詳細を確認してください。
- コンソールの正常性モニター、セキュリティー、ロギング、リソース使用状況を管理するのはお客様の責任です。
- コンソールを接続できるのは、Hyperledger Fabric v1.2.1 レベルのブロックチェーン・コンポーネントだけです。

## 必要なリソース
{: #ibp-console-deploy-icp-resources-required}

{{site.data.keyword.cloud_notm}} Private システムが最小ハードウェア・リソース要件を満たしていることを確認します。

| コンポーネント | vCPU | RAM | データ・ストレージ用ディスク |
|-----------|------|-----|-----------------------|
| IBP コンソール | 1 | 192 MB | 1 GB |
| IBP コンソール用 CouchDB | 1 | 1 GB | 1 GB |

 **注:**
 - vCPU は仮想マシンに割り当てられる仮想コアであるか、サーバーが仮想マシン用にパーティション化されていない場合は物理プロセッサー・コアです。 {{site.data.keyword.cloud_notm}} Private でのデプロイメントの仮想プロセッサー・コア (VPC) を決定する場合は、vCPU 要件を考慮する必要があります。 VPC は IBM 製品のライセンス交付コストを決定する単位です。 VPC を決定するシナリオについて詳しくは、[仮想プロセッサー・コア (VPC) ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html) を参照してください。
 - テストおよび試験目的には、これらの最小リソース・レベルで十分です。 大量のトランザクションがある環境の場合は、十分な量のストレージを割り振ることが重要です。例えば、{{site.data.keyword.blockchainfull_notm}} Platform コンソール用に 250 GB を割り振ります。 使用するストレージの量は、ネットワークから要求されるトランザクションの数と署名の数によって異なります。 ピアまたは順序付けプログラムのストレージが満杯に近付いたら、さらに大きいファイル・システムを使用して新しいコンソールをデプロイし、適宜、ネットワーク・コンポーネントを再インポートする必要があります。

## ストレージ
{: #ibp-console-deploy-icp-resources-required-storage}

コンソールが使用するストレージを設定する必要があります。 デフォルト設定を使用する場合、Helm チャートによって、コンソール・データ用に `console-pvc` という名前の新規 8 Gi Persistent Volume Claim (PVC) と、CouchDB 用に `couchdb-pvc` という名前の別の 8 Gi PVC が作成されます。

デフォルトのストレージ設定を使用しない場合は、{{site.data.keyword.cloud_notm}} Private のインストール時に*新しい* `storageClass` がセットアップされたことを確認します。セットアップされていない場合は、デプロイの前に Kubernetes システム管理者が storageClass を作成する必要があります。

コンソールは、AMD64 プラットフォームまたは S390X プラットフォームのいずれかにデプロイできます。 ただし、[動的プロビジョニング](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)は、{{site.data.keyword.cloud_notm}} Private では AMD64 ノードでのみ使用可能であることに注意してください。 クラスターに S390X と AMD64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。

動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) を作成し、Kubernetes PVC バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。

## 前提条件
{: #ibp-console-icp-prereq}

{{site.data.keyword.cloud_notm}} Private に {{site.data.keyword.blockchainfull_notm}} Platform コンソールをデプロイする前に、[{{site.data.keyword.cloud_notm}}Private のセットアップ](/docs/services/blockchain/ICP_setup.html#icp-setup) のチュートリアルの手順を完了したことを確認してください。

{{site.data.keyword.blockchainfull_notm}} Platform コンソールをデプロイする前に、以下の依存関係をインストールまたは構成する必要もあります。

- [IBM App ID サービスでの登録](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Docker のインストール](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### App ID サービスへの登録
{: #ibp-console-icp-prereq-app-id}

[App ID ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/docs/services/appid/index.html#gettingstarted "App ID") は、{{site.data.keyword.cloud_notm}} が提供しているユーザー管理サービスです。 {{site.data.keyword.blockchainfull_notm}} Platform コンソールでは、ダッシュボードを使用してブロックチェーン・ネットワークを操作できる人を、App ID サービスを使用して管理します。 この方法により、UI のユーザーは、独自の組織またはサード・パーティー (Google や Facebook など) によって発行された資格情報を使用できるので、IBM ID を用意する必要がありません。 コンソールをデプロイするユーザーのみ、このサービスに登録するために {{site.data.keyword.IBM_notm}} ID が必要になります。

コンソールをデプロイする前に、IBM ID を取得して、App ID クラウド・ディレクトリーに登録する必要があります。この登録は一度行えばよい作業です。 登録したら、APP ID サービスの資格情報を取得して、コンソールに渡す必要があります。 以下の手順を使用して、サービスに登録し、サービス資格情報を取得してください。

1. まだ [{{site.data.keyword.IBM_notm}} ID ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid) を持っていない場合は、作成する必要があります。

2. {{site.data.keyword.IBM_notm}} ID を使用して、[{{site.data.keyword.cloud_notm}} ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/app-id "IBM Cloud の App ID") にログインまたは登録します。 そして、「カタログ」の [App ID ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/app-id "IBM Cloud の App ID") サービスに進みます。 サービスの要件に応じて、`Lite` プランまたは`段階的層`を選択します。 そして、**「作成」**ボタンをクリックして「ようこそ」画面を起動します。

3. 「ようこそ」画面で、左側のナビゲーション・パネル上の**「管理」**リンクをクリックして、希望の ID プロバイダーを表示または更新します。 Google などのサード・パーティー・プロバイダーにすることも、組織独自の ID サービスにすることもできます。

4. テーブルの**「設定」**タブをクリックし、リダイレクト URL を構成します。 これが、{{site.data.keyword.blockchainfull_notm}} Platform コンソールをデプロイしてアクセスする URL になります。 ローカル・ホストを使用するには、`http://localhost/auth/cb` を指定し、`+` 符号をクリックします。

5. E メール・アドレスをサービスに追加して、初めてコンソールを構成する際にログインできるようにします。 左側のナビゲーション・パネルの**「ユーザー」**リンクをクリックし、**「ユーザーの追加」**ボタンをクリックします。 E メールの情報を入力し、**「保存」**をクリックします。

6. 左側のナビゲーション・パネルの**「サービス資格情報」**リンクをクリックします。 初めてサービスを使用する場合は、**「新規資格情報」**をクリックして資格情報を生成する必要があります。 **「資格情報の表示」**をクリックして、サービスへのアクセスに必要な資格情報をコピーします。  新しい資格情報の作成に関する詳細情報が表示されたら、`reader` オプションを選択します。UI に特定の名前を割り当てる場合は「サービス ID」も選択します。 App ID の資格情報は、以下のような JSON 値になります。
  ![サービス資格情報](../images/service_credentials.gif "サービス資格情報")

7. 「コピー」アイコンをクリックし、資格情報を保存します。 この情報をコンソールの構成時に使用します。

### Docker のインストール
{: #ibp-console-icp-prereq-docker}

[Docker ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.docker.com/get-started) バージョン 17.06.2-ce 以上を、{{site.data.keyword.blockchainfull_notm}} Platform コンソールをデプロイする環境にインストールします。 端末ウィンドウから以下のコマンドを使用して、インストール済みの Docker のバージョンを確認できます。

```
docker --version
```
{:codeblock}

**注:** Docker for Mac、Docker for Windows、または Docker Toolbox をインストールすると、Docker Compose もインストールされます。 既に Docker をインストール済みの場合は、Docker Compose バージョン 1.14.0 以上がインストールされていることを確認する必要があります。 1.14.0 以上でない場合は、最新バージョンの Docker をインストールすることをお勧めします。 端末ウィンドウから以下のコマンドを使用して、インストール済みの Docker Compose のバージョンを確認できます。

```
docker-compose --version
```
{:codeblock}


## 手順 1: {{site.data.keyword.IBM_notm}} パスポート・アドバンテージから {{site.data.keyword.blockchainfull_notm}} Platform コンソール・パッケージをダウンロードする
{: #ibp-console-icp-download-icp}

{{site.data.keyword.IBM_notm}} 販売員に連絡して、[Passport Advantage ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online for customers") から {{site.data.keyword.blockchainfull_notm}} Platform コンソール・パッケージをダウンロードします。 IBM 販売員の連絡先がわからない場合は、 この <link> にアクセスし、登録して取得してください ...

## 手順 2: {{site.data.keyword.blockchainfull_notm}} Platform コンソールの Helm チャートを {{site.data.keyword.cloud_notm}} Private にインポートする
{: #ibp-console-icp-import-helmchart}

[{{site.data.keyword.cloud_notm}} Private への {{site.data.keyword.blockchainfull_notm}} Platform のインストール](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)を参照してください。

## 手順 3: {{site.data.keyword.cloud_notm}} Private に {{site.data.keyword.blockchainfull_notm}} Platform コンソールをデプロイする
{: #ibp-console-deploy-helmchart-icp}

{{site.data.keyword.blockchainfull_notm}} Platform コンソールの Helm チャートをインポートしたら、以下の手順でコンソールを構成してインストールすることができます。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、右上隅の**「カタログ」**リンクをクリックします。
2. 左側のナビゲーション・パネルで`「ブロックチェーン」`をクリックし、`ibm-blockchain-platform-ui` というラベルのタイルを見つけます。 タイルをクリックして開くと、Helm チャートの構成およびインストールに関する情報を含む Readme ファイルが表示されます。
3. パネル上部にある**「構成」**タブをクリックするか、右下隅にある**「構成」**ボタンをクリックします。
4. [構成設定](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters)に入力します。
5. **「インストール」**をクリックします。

### {{site.data.keyword.blockchainfull_notm}} Platform コンソールのパラメーター
{: #ibp-console-icp-parameters}

以下の表は、{{site.data.keyword.blockchainfull_notm}} Platform コンソールの構成可能パラメーターとそのデフォルト値を示しています。

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
| `Helm リリース名 (Helm release name)`| Helm リリースの名前。 小文字で始まり、任意の英数字で終わる必要があり、ハイフンと小文字の英数字のみを含む必要があります。 コンソールをインストールするときには、毎回、固有の Helm リリース名を使用する必要があります。 | なし | はい |
| `ターゲット名前空間 (Target namespace)`| Helm チャートをインストールする Kubernetes 名前空間を選択します。 | なし | はい |
| `イメージ・リポジトリー`| {{site.data.keyword.blockchainfull_notm}} Platform コンソール Helm チャートの場所。 | registry.ng.bluemix.net/op-tools/op-tools | はい |
| `イメージ・タグ (Image tag)`| {{site.data.keyword.blockchainfull_notm}} Platform コンソール・イメージに関連付けられたタグの値。 このフィールドには、イメージ・バージョンが自動入力されます。 これを変更しないでください。 | v0.0.34 | はい |
| `サービス・タイプ` | ピアで[外部ポートを公開 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) するかどうかを指定するために使用します。 ポートを外部に対して公開する場合は NodePort を選択し (推奨)、ポートを公開しない場合は ClusterIP を選択します。 LoadBalancer および ExternalName はこのリリースではサポートされていません。 | NodePort | はい |
| `コンソール入り口 (Console ingress)` | {{site.data.keyword.blockchainfull_notm}} Platform コンソールへのアクセスに使用するホスト名を指定します。 | なし | いいえ |
| `Configtxlator 入り口 (Configtxlator ingress)` | configtxlator サービスへのアクセスに使用するホスト名を指定します。 | なし | いいえ |
| `永続性を有効にする (Persistence enabled)` | チェックすると、コンテナーの再始動時にデータが使用可能になります。 チェックしない場合は、フェイルオーバーやポッドの再始動時にすべてのデータが失われます。 | オン | いいえ |
| `動的プロビジョニングを使用 (Use dynamic provisioning)` | ストレージ・ボリュームの動的プロビジョニングを有効にする場合にチェック・マークを付けます。 | オン | いいえ |
| `ストレージ・クラス名 (Storage class name)`| 固有のストレージ・クラス名を指定します。 指定しない場合は、クラスターのデフォルト・ストレージ・クラスが使用されます。 | なし | {{site.data.keyword.cloud_notm}} Private クラスターの構成方法によって異なります。 クラスター管理者に確認してください。 |
| `既存のボリューム・クレーム (Existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。 | なし | いいえ |
| `セレクター・ラベル (Selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。 | なし | いいえ |
| `セレクター値 (Selector value)`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。 | なし | いいえ |
| `ストレージ・アクセス・モード (Storage access mode)`| PVC のストレージ・[アクセス・モード](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード")を指定します。 | ReadWriteMany | はい |
| `ボリューム名`| PVC の名前を指定します。 | なし | はい |
| `CPU 要求` | コンソールに割り当てる CPU の最小数。 | 250m | はい |
| `CPU リミット` | コンソールに割り当てる CPU の最大数。| 500m | はい |
| `メモリー要求` | コンソールに割り当てるメモリーの最小量。 | 256 Mi | はい |
| `メモリー制限` | コンソールに割り当てるメモリーの最大量。 | 1Gi | はい |

## 手順 4: プロキシー IP を構成する
{: #ibp-console-icp-config-proxy-ip}

インストールが完了した後に、**「Helm リリースの表示」**ボタンをクリックして、コンソールの Helm リリースのページを開きます。 {{site.data.keyword.cloud_notm}} Private で**「メニュー」**>**「ワークロード」**>**「Helm リリース」**をクリックし、Helm リリースをクリックすることもできます。

コンソールの Helm リリースのページで、{{site.data.keyword.cloud_notm}} Private クラスターのプロキシー IP (`9.12.19.115` など) を見つけます。

コンソールにアクセスするクライアント・マシンで、`/etc/hosts` 内にエントリーを追加し、前の手順で入力した optools サービスと configtxlator サービスのホスト名をプロキシー IP にマップします。 例えば、次のようにします。
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## 手順 5: {{site.data.keyword.blockchainfull_notm}} Platform コンソールをセットアップする
{: #ibp-console-icp-setup}

*このセクションでは、gif アニメーションを活用します。*

1. {{site.data.keyword.cloud_notm}} で、左側のナビゲーションから**「ID プロバイダー」**>**「管理」**に進み、**「認証設定」**タブを選択します。
2. **「Web リダイレクト URL の追加」**フィールドに、App ID を使用してコンソールにアクセスするときに使用する URL を指定します。
3. Web ブラウザーで、{{site.data.keyword.cloud_notm}} Private で構成したホスト名を使用して、コンソールにアクセスします。 初めてログインする場合は、コンソールのセットアップ・パネルが表示されます。
  1. **「認証 (Authentication)」**タブに、App ID を入力します。
  2. **「構成」**タブに、[手順 1: App ID サービスに登録する](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)でコピーした App ID サービスの資格情報を貼り付けます。
  3. **「ユーザーの追加」**タブで、コンソールへのアクセスを許可する`管理者`ユーザーと`一般 `ユーザーの E メール・アドレスのリストを指定します。 E メール・ドメインは、App ID サービスを {{site.data.keyword.cloud_notm}} で登録したときに選択した ID プロバイダー (自社、Google、 Facebook など) に制限されます。
    - `管理者`の役割は、コンソールの許可リストに対して新規ユーザーの追加または既存ユーザーの削除を行うために必要です。   **ヒント:** コンソールの管理者は、必ず、このリストに自分の E メール・アドレスを指定してください。
    - `一般`の役割のユーザーには、コンソールのコンポーネントに対して**参照専用**アクセスを行えます。

    最後に、上記のいずれのリストにもない無許可のユーザーがコンソールにアクセスしようとした場合に連絡先 E メール・アドレスとして表示される `管理者の連絡先 E メール`を 1 つ指定する必要があります。 これはコンソールの管理者ユーザーの E メール・アドレスにする必要はありません。 また、この情報は、後でコンソールの**「メンバー」**タブで作成したり変更したりできます。
  4. **「ログインに進む (Proceed to Login)」**をクリックします。 これで、上記のリストに指定したすべてのユーザーが、自分の E メール・アドレスを使用して {{site.data.keyword.blockchainfull_notm}} Platform コンソールにログインできるようになります。

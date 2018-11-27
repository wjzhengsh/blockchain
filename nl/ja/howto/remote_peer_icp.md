---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private でのリモート・ピアのデプロイ
{: #remote-peer-icp}


***[このページは参考になりましたか。ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


以下の手順では、{{site.data.keyword.cloud_notm}} Private (ICP) に {{site.data.keyword.blockchainfull}} Platform リモート・ピアをインストールし、これを {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークに接続する方法を説明します。
{:shortdesc}

{{site.data.keyword.cloud_notm}} Private は、オンプレミスのコンテナー化されたアプリケーションを開発および管理するためのアプリケーション・プラットフォームです。{{site.data.keyword.cloud_notm}} Private の {{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアは、お客様がローカル環境にピアをデプロイできるように ICP とバンドルされた {{site.data.keyword.blockchainfull_notm}} 製品です。リモート・ピアの Helm チャートをインストールした後、これを ICP カタログでアプリケーションとして見つけることができます。ICP のリモート・ピアは、ICP のストレージ、セキュリティー、ロギング、およびサポート・サービスをピア管理に活用します。ICP について詳しくは、[{{site.data.keyword.cloud_notm}} Private 資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 資料") を参照してください。

ICP にリモート・ピアをインストールする前に、[リモート・ピアに関する考慮事項](remote_peer.html#remote-peer-limitations)を確認してください。

## 前提条件
{: #prerequisites-icp}

ICP にリモート・ピアをインストールする前に、{{site.data.keyword.cloud_notm}} Private、ローカル・システム、および {{site.data.keyword.blockchainfull_notm}} Platform で以下のステップを実行する必要があります。

1. ローカル・システム

  {{site.data.keyword.cloud_notm}} Private CLI [2.1.0.3 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "{{site.data.keyword.cloud_notm}} Private CLI のインストール") をインストールする必要があります。  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} Private

  [{{site.data.keyword.cloud_notm}} Private ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v2.1.0.3 資料") クラスター・バージョン 2.1.0.3 が必要です。リモート・ピアを使用して開発、テスト、または試験を行う場合は、[{{site.data.keyword.cloud_notm}} Private Community Edition ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "{{site.data.keyword.cloud_notm}} Private-CE のインストール") を無料でインストールできます。

  **注意**:
    - ピアが使用するストレージを設定する必要があります。デフォルト設定を使用すると、Helm チャートによって、ピア・データ用に `my-data-pvc` という名前の新しい 8 Gi Persistent Volume Claim が作成されます。台帳データベースとして CouchDB を選択すると、Helm チャートによって、台帳データベース用に `statedb-pvc` という名前の別の 8 Gi Persistent Volume Claim が作成されます。
    - デフォルトのストレージ設定を使用しない場合は、ICP のインストール時に*新しい* storageClass がセットアップされたことを確認します。セットアップされていない場合は、デプロイの前に Kubernetes システム管理者が storageClass を作成する必要があります。
    - [動的プロビジョニング ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "動的ボリューム・プロビジョニング") は、ICP の amd64 ノードにのみ使用可能です。したがって、クラスターに s390x と amd64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。
    - 動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "永続ボリューム") を作成し、Kubernetes Persistent Volume Claim (PVC) バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。

  ICP ハードウェア要件に加えて、リモート・ピアには少なくとも以下が必要です。
    - 2x vCPU
    - 2 GB RAM
    - チェーンコード用の 4 GB のスペース
    - 台帳の拡張に合わせて拡大できる台帳用の 10 GB のスペース

3. {{site.data.keyword.blockchainfull_notm}} プラットフォーム  

  リモート・ピアを使用するには、{{site.data.keyword.blockchainfull_notm}} Platform でホストされるブロックチェーン・ネットワークのメンバーである組織が必要です。{{site.data.keyword.cloud_notm}} 上のネットワーク・モニターを使用して、ネットワーク資格情報およびネットワークの API エンドポイントにアクセスする必要があります。ブロックチェーン・ネットワークのメンバーではない場合は、ネットワークを作成するか、ネットワークに参加する必要があります。詳しくは、[ネットワークの作成](../get_start.html#creating-a-network)または[ネットワークへの参加](../get_start.html#joining-a-network)を参照してください。

## ICP への Helm チャートのインポート

{{site.data.keyword.blockchainfull_notm}} Platform リモート・ピア・パッケージは、[Github ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain リモート・ピア") にあります。Helm チャートは、コマンド・ラインを使用して ICP コンソールにインポートします。ローカル・マシンから、以下のステップを実行して Helm チャートをインポートします。

1. [Github ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "IBM Blockchain リモート・ピア Helm チャート") から `.tgz` Helm チャート・ファイルをダウンロードします。<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. まだログインしていない場合は、ICP CLI から ICP クラスターにログインしてから、Docker プライベート・イメージ・レジストリーにログインします。  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  コード内の `<cluster_CA_domain>` は認証局 (CA) ドメインです。CA ドメインを指定しない場合、
デフォルト値は `mycluster.icp` です。

3. ダウンロードした Helm チャートを保管したディレクトリーから、ICP CLI で以下のコマンドを実行して、Helm チャートを ICP クラスターにインポートします。`<helm_chart_from_github>` を、GitHub からダウンロードした `.tgz` ファイルの名前に置き換えます。

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  このコマンドが正常に完了すると、以下のような情報が表示されます。

  ```  
  Helm チャートのロード中
  ロードされた Helm チャート

  チャートの同期
  同期が開始されました
  OK
  ```  

  これで、{{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアの Helm チャートのタイルが「ICP カタログ (ICP Catalog)」ページに表示されるようになります。ICP コンソールの**「カタログ」**ボタンをクリックして、インポートが成功したことを確認します。

### リモート・ピアのデプロイ

以下のステップでは、ICP でリモート・ピアをデプロイするために必要な処理を説明します。ICP でリモート・ピアを構成する前に、{{site.data.keyword.blockchainfull_notm}} Platform に関する一部のエンドポイント情報を取得し、リモート・ピアのユーザー ID を作成します。後のステップでこの情報を使用する必要があります。

## ステップ 1: リモート・ピア構成情報の取得
{: #network-endpoints}

構成時に、ネットワークの API エンドポイントをリモート・ピアに提供する必要があります。リモート・ピアはこれらのエンドポイントを使用し、{{site.data.keyword.blockchainfull_notm}} Platform のネットワークを検索して接続できます。ネットワーク・モニターの**「概要」**画面で、**「リモート・ピアの構成 (Remote Peer Configuration)」**ボタンをクリックします。

![リモート・ピアの構成 (Remote Peer Configuration)](../images/myresources_starter.png "リモート・ピアの構成 (Remote Peer Configuration)")
*図 1. リモート・ピアの構成 (Remote Peer Configuration)*

ポップアップ・ウィンドウが開き、以下のフィールドの値が表示されます。リモート・ピアを構成するときのために、これらの値を保存します。

  - **ネットワーク ID**
  - **組織 MSP (Organization MSP)**
  - **認証局 (CA) 名 (Certificate Authority (CA) Name)**
  - **認証局 (CA) URL (Certificate Authority (CA) URL)**
  - **認証局 (CA) TLS 証明書 (Certificate Authority (CA) TLS Certificate)**

フィールドは、個別にコピーして貼り付けるか、**「ダウンロード」**リンクをクリックして JSON ファイルとして保存できます。情報を JSON にダウンロードする場合は、TLS 証明書を PEM 形式に変換してからリモート・ピアに提供する必要があることに**注意**してください。CLI を使用して `echo -e "<CERT>" > catls.pem` コマンドを発行すると、証明書を PEM 形式に変換できます。

## ステップ 2: リモート・ピアの登録
{: #register-peer}

リモート・ピアをネットワークに参加させるには、まず、{{site.data.keyword.blockchainfull_notm}} Platform で組織に新しいピア ID を追加する必要があります。以下のステップを実行して、リモート・ピアを登録します。

1. {{site.data.keyword.blockchainfull_notm}} Platform にログインして、ネットワーク・モニターにアクセスします。「認証局」画面では、管理またはクライアント・アプリケーションなど、組織に登録されたすべての ID を確認できます。
  ![CA 画面](../images/CA_screen_starter.png "CA 画面")

*図 2. CA 画面*

2. 画面上部の**「ユーザーの追加」**ボタンをクリックします。ポップアップ画面が開き、以下のフィールドに入力した後に、リモート・ピアを登録できます。「ID」および「機密事項」の値を保存します。ピアの構成時に、これらの値を使用する必要があります。
  - **ID:** ピアのユーザー名で、ピアの構成時には `enroll ID` と呼ばれます。今後、リモート・ピアのデプロイ時に使用するため、**この値を保存します**。
  - **機密事項:** ピアのパスワードで、ピアの構成時には `enroll Secret` と呼ばれます。今後、リモート・ピアのデプロイ時に使用するため、**この値を保存します**。
  - **タイプ:** このフィールドには `peer` を選択します。
  - **所属 (Affiliation):** これは `org1` など、リモート・ピアが属する組織での所属です。ドロップダウン・リストから既存の所属を選択するか、新しい所属を入力します。
  - **最大登録数 (Maximum Enrollments):** このフィールドを使用して、この ID を使用した証明書の登録または生成の回数を制限できます。指定しない場合、値はデフォルトで無制限の登録に設定されます。

  フィールドに入力した後に、**「送信」**をクリックしてリモート・ピアを登録します。登録されたピアは、組織の ID としてテーブルに表示されます。

## ステップ 3: リモート・ピアの構成とインストール

ネットワーク・モニターでピア ID を登録した後に、以下の ICP コンソールでの手順に従って、ICP でリモート・ピアを構成およびインストールできます。

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. ICP コンソールにログインし、右上隅の**「カタログ」**リンクをクリックします。
2. {{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアの Helm チャートを見つけ、クリックして開きます。Helm チャートのインストールおよび構成に関する情報を含む readme ファイルが表示されます。
3. **「構成」**をクリックします。「構成」ページで、Helm チャートの構成設定を行い、使用許諾契約書を読んで同意したことを確認します。
4. **「インストール」**をクリックします。

### 構成パラメーター
{: #icp-configuration-parms}

リモート・ピアが {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークに接続するために必要な情報を指定する必要があります。以下の表は、{{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアのチャートの構成可能パラメーターとそのデフォルト値を示しています。


|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
|**構成** | **リモート・ピアのリリース名およびターゲット名前空間の構成** |  ||
| `リリース名 (Release name)`| リリースの名前。 | なし | はい |
| `ターゲット名前空間 (Target namespace)`| Kubernetes 名前空間。 | デフォルト | はい |
| | | | |
|**クラスター構成** |**クラスター構成情報** | ||
| `ワーカー・ノードのアーキテクチャー (Worker node architecture)`| クラウド・プラットフォーム・アーキテクチャーを選択します (amd64 または S390x)。| マスター・ノードに基づく自動検出されたアーキテクチャー | はい |
| `イメージ` | Helm チャートのパス。 | インストール・パスが自動入力されます。この値を変更しないでください。 | はい |
| | | | |
|**ブロックチェーン・ネットワーク** | **リモート・ピアに必要なネットワーク構成情報**| | |
| `ネットワーク ID`| {{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターで検出されたネットワーク ID の値。「概要」画面の`「リモート・ピアの構成 (Remote Peer Configuration)」`ボタンをクリックすると、ここの情報がコピー・アンド・ペーストされます。| なし | はい |
| `組織 MSP (Organization MSP)`| {{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターで検出された組織 MSP ID の値。「概要」画面の`「リモート・ピアの構成 (Remote Peer Configuration)」`ボタンをクリックすると、ここの情報がコピー・アンド・ペーストされます。| なし | はい |
| `認証局 (CA) 名 (Certificate Authority (CA) Name)`| {{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターで検出された CA 名の値。「概要」画面の`「リモート・ピアの構成 (Remote Peer Configuration)」`ボタンをクリックすると、ここの情報がコピー・アンド・ペーストされます。| なし | はい |
| `認証局 (CA) URL (Certificate Authority (CA) URL)`|{{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターで検出された CA URL の値。「概要」画面の`「リモート・ピアの構成 (Remote Peer Configuration)」`ボタンをクリックすると、ここの情報がコピー・アンド・ペーストされます。| なし | はい |
| `認証局 (CA) TLS 証明書 (Certificate Authority (CA) TLS Certificate)` | {{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターの CA TLS 証明書ストリング。「概要」画面の`「リモート・ピアの構成 (Remote Peer Configuration)」`ボタンをクリックすると、ここの情報がコピー・アンド・ペーストされます。| なし | はい |
| | | | |
|**リモート・ピア ID** | **リモート・ピアの登録に使用される登録 ID と機密事項**| | |
| `ピア登録 ID (Peer enroll ID)`| {{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターの「認証局」画面で入力した登録 ID。 | なし | はい |
| `ピア登録機密事項 (Peer enroll secret)`| {{site.data.keyword.blockchainfull_notm}} Platform ネットワーク・モニターの「認証局」画面で入力した登録機密事項。| なし | はい |
| | | | |
|**リモート・ピア・データベース** | **台帳データベース・タイプ**| | |
| `台帳データベース`| チャネル台帳の保管に使用される[状態データベース](../glossary.html#state-database)。リモート・ピアは、[ブロックチェーン・ネットワーク](../v10_dashboard.html#network-preferences)と同じデータベースを使用する必要があります。 | なし | はい |
| `CouchDB イメージ (CouchDB image)`| 台帳データベースとして CouchDB が選択されている場合にのみ適用されます。| インストール・パスが自動入力されます。この値を変更しないでください。 | はい |
|**データの永続性** | クラスターが再始動または失敗した後にデータを維持する機能を有効にします。詳しくは、[Kubernetes のストレージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/ "ボリューム") を参照してください。 | | |
| `データの永続性が有効 (Data persistence enabled)`| コンテナーの再始動時にデータが利用可能であることを示します。*チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。* | オン | いいえ |
| `動的プロビジョニングを使用 (Use dynamic provisioning)`| Kubernetes 動的ストレージを使用します。 | オン | いいえ |
| | | | |
|**永続ボリュームの構成** | **ピアで使用される Persistent Volume Claim** |  |  |
| `Persistent volume claim`| 作成される新しい Persistent Volume Claim (PVC) の名前を入力します。 | my-data-pvc | いいえ |
| `ストレージ・クラス名 (Storage class name)`| ストレージ・クラス名を選択します。 | 新しい PVC を作成する場合は空のままにします。それ以外の場合は、既存の PVC に関連付けられているストレージ・クラスを指定します。 | いいえ |
| `既存のボリューム・クレーム (Existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。 | 新しいクレーム名 | いいえ |
| `ストレージ・アクセス・モード (Storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。  | ReadOnlyMany| いいえ |
| `セレクター・ラベル (Selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。| デフォルト | いいえ |
| `セレクター値 (Selector value)`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。 | デフォルト | いいえ |
| `ボリューム・クレーム・サイズ (Volume claim size)`| ボリューム・クレームのサイズ。2Gi を超える必要があります。 | 8Gi | はい |
| | | | |
|**状態データベースのデータ永続性** | **コンテナーが再始動または失敗した後にデータを保持します。詳しくは、[Kubernetes のストレージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/ "ボリューム") を参照してください。 | | |
| `データの永続性が有効 (Data persistence enabled)`| コンテナーの再始動時に台帳データが利用可能です。*チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。*| オン | いいえ |
| `動的プロビジョニングを使用 (Use dynamic provisioning)`| Kubernetes 動的ストレージを使用します。 | オン | いいえ |
| | | | |
|**状態データベースの永続ボリュームの構成** |**状態データベースで使用される永続ボリューム・クレーム。詳しくは、[Kubernetes のストレージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/ "ボリューム") を参照してください。** |||
| `Persistent volume claim`| 作成される新しい CouchDB Persistent Volume Claim の名前を入力するか、デフォルトを使用します。 | statedb-pvc | いいえ |
| `状態データベースのストレージ・クラス名 (State database storage class name)`|ストレージ・クラス名を選択します。| なし | いいえ |
| `状態データベースの既存のボリューム・クレーム (State database existing volume claim)`| 既存のボリューム・クレームを使用するには、pvc 名を入力し、他のすべてのフィールドは空のままにします。 | なし | いいえ |
| `状態データベースのストレージ・アクセス・モード (State database storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。  | ReadOnlyMany| いいえ |
| `セレクター・ラベル (Selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。| デフォルト | いいえ |
| `セレクター値 (Selector value)`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。| デフォルト | いいえ |
| `状態データベースのボリューム・クレーム・サイズ (State database volume claim size)`| Persistent Volume Claim のサイズ。 | 8Gi | いいえ |
| | | | |
| **ピア・リソース** | **ピア・コンテナーの最小および最大の CPU およびメモリー** | | |
| `ピア CPU 要求 (Peer CPU request)` | ピアに割り当てる CPU の最小数。 | 2 | はい |
| `ピア CPU 制限 (Peer CPU limit)` | ピアに割り当てる CPU の最大数。| 2 | はい |
| `ピア・メモリー要求 (Peer Memory request)` | ピアに割り当てるメモリーの最小量。 | 4Gi | はい |
| `ピア・メモリー制限 (Peer Memory limit)` | ピアに割り当てるメモリーの最大量。 | 4Gi | はい |
| | | | |
|**CouchDB リソース** | **CouchDB コンテナーに割り振られている最小および最大の CPU およびメモリー。このセクションは、状態データベースとして CouchDB が選択されている場合にのみ適用されます。**| | | |
| `CouchDB CPU 要求 (CouchDB CPU request)` | CouchDB に割り当てる CPU の最小数。| 2 | はい |
| `CouchDB CPU 制限 (CouchDB CPU limit)` | CouchDB に割り当てる CPU の最大数。 | 2 | はい |
| `CouchDB メモリー要求 (CouchDB Memory request)` | CouchDB に割り当てるメモリーの最小量。| 4Gi | はい |
| `CouchDB メモリー制限 (CouchDB Memory limit)` | CouchDB に割り当てるメモリーの最大量。 | 4Gi | はい |

リモート・ピア・データベースとして CouchDB が選択されている場合、ピア用と CouchDB 用にそれぞれ 1 つずつ、2 つのコンテナーがポッドに作成されます。ピア・コンテナーには、ファイル・システムのブロックおよびトランザクションが保管されるピア PVC に対する単一のボリューム・マウントが含まれます。CouchDB コンテナーは、台帳データを含むピア状態データベース PVC をマウントします。

リモート・ピア・データベースとして LevelDB が選択されている場合、ピアと LevelDB の両方の処理を実行するために単一のコンテナーがポッドに作成されます。このコンテナーには 2 つのボリューム・マウントがあり、1 つはピア PVC 用、2 つ目のボリューム・マウントは台帳データを含むピア状態データベース PVC 用です。

| リモート・ピア・データベースの選択  | コンテナー 1 のコンテンツ  | コンテナー 2 のコンテンツ |
| --------------|-----------------|---------------|
| CouchDB | ピア PVC をマウントするリモート・ピア| 状態データベース PVC をマウントする CouchDB |
| LevelDB | ピア PVC および状態データベース PVC をマウントするリモート・ピアおよび LevelDB | 該当なし |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  このコマンドは、ピア TLS 証明書である peer-cert.pem ファイルを生成します。

--->

## ステップ 4: リモート・ピアのインストールの確認
{: #verify-installation-icp}

リモート・ピアをインストールした後、ピア・ログを表示して、ピアのインストールが成功したかどうかを確認できます。

1. **「インストール」**をクリックした後に、ポップアップ・ウィンドウで**「Helm リリースの表示」**ボタンをクリックします。**「ポッド」**セクションまでスクロールダウンします。ポッド・ステータスが**「実行中」**になったら、**「デプロイメント」**セクションにリモート・ピア・インスタンスが表示されます。

2. ICP コンソールで、左上隅の**「メニュー」**アイコンをクリックします。メニュー・リストで**「ワークロード」** > **「デプロイメント」**をクリックします。デプロイメント・テーブルで、作成するリモート・ピア・インスタンスをクリックすると、デプロイメントの概要画面が開きます。**「ログ」**タブをクリックして、リモート・ピア・ログを表示します。検索フィールドにストリング `Started` を入力できます。

  - リモート・ピアが正常に開始されている場合は、以下の例のようなログが表示されます。
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - 画面にログが表示されない場合は、リモート・ピアが正常に開始されていません。追加のログを表示して問題を確認するには、左上隅の**「メニュー」**アイコンをクリックして、**「ワークロード」** > **「Helm リリース」**をクリックします。helm リリースをクリックして開きます。関連する**ポッド**の横の**「ログの表示」**リンクをクリックして、Kibana インターフェースで追加のチャート・ログを表示します。

3. リモート・ピアが {{site.data.keyword.blockchainfull}} Platform ネットワークに接続できることを確認するには、リモート・ピア・コンテナー内部からピア CLI コマンドを実行します。`peer channel fetch` CLI コマンドを実行して、チャネルからジェネシス・ブロックをフェッチします。

   1. まだ ICP クラスターに接続していない場合は、この[指示](remote_peer_operate_icp.html#remote-peer-kubectl-configure)に従って接続し、ピア・コンテナー内部から cli を使用します。

   2. ジェネシス・ブロックをフェッチするには、まず、組織をネットワーク内のチャネルに追加する必要があります。

     - リモート・ピアから新しいチャネルを開始できます。チャネル・イニシエーターは、[チャネル作成](create_channel.html#creating-a-channel)時に組織を自動的に含めることができます。ネットワーク・モニターでチャネルを作成するには、{{site.data.keyword.blockchainfull_notm}} Platform 上に少なくとも 1 つのピアが存在する必要があることに注意してください。  

     - ブロックチェーン・ネットワークの別のメンバーも[チャネルの更新](create_channel.html#updating-a-channel)を使用して、既存のチャネルに組織を追加できます。{{site.data.keyword.blockchainfull_notm}} Platform 上にピアがあるチャネルのメンバーは、プラットフォームでピアをホストしていない場合でも、ネットワーク・モニターを使用して組織をチャネルに追加できます。

      証明書とチャネルを同期するだけで良いように、リモート・ピアはインストール時にその署名証明書をアップロードします。ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。このアクションによって、チャネルのすべてのピアで証明書が同期されます。

   3. ネットワーク・モニターの「概要」画面にある `creds.json` ファイルから構成情報を取得します。**「接続プロファイル」**、**「ダウンロード」**の順にクリックします。

     - `orderers > url` の下に配置されている **orderers** を検索して、順序付けプログラムの URL を見つけます。ネットワーク名で始まる URL をメモしておきます。URL は以下の例のようになります。

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - **organizations** を検索して、組織の名前を見つけます。この組織はリモート・ピアの登録に使用した組織と同じである必要があります。組織の名前をその関連 `mspid` とともに見つけることができます。`mspid` の値をメモしておきます。

    4. 以下のコマンドを実行して、ピア・コンテナーの環境変数を設定します。

       ```
       export ORDERER_1=<ORDERER_URL>
       export CHANNEL=<CHANNEL_NAME>
       export CC_NAME=<CC_NAME>
       export ORGID=<ORGANIZATION_MSP_ID>
       export PEERADDR=<PEER_ADDR>
       ```
       {:codeblock}

       フィールドを独自の情報に置き換えます。
         - `<ORDERER_URL>` を `creds.json` ファイルの順序付けプログラムのホスト名およびポートで置き換えます。
         - `<CHANNEL_NAME>` をピアが参加しているチャネルの名前で置き換えます。
         - `<CC_NAME>` をチェーンコードを参照する任意の名前で置き換えます。
         - `<ORGANIZATION_MSP_ID>` を `creds.json` ファイルの組織の名前で置き換えます。
         - `<PEER_ADDR>` を `localhost:7051` で置き換えます。

       例:

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. 以下のピア CLI コマンドを実行して、チャネルのジェネシス・ブロックをフェッチします。

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **注:** これらのいずれかの CLI コマンドを実行すると、以下の警告が表示される場合がありますが、無視しても安全です。

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     以下のコマンドを実行して、コマンドが正常に機能し、ジェネシス・ブロックがピア・コンテナーに追加されたことを確認します。

     ```
     ls *.block
     ```
     {:codeblock}

     以下の例と同様のものが表示された場合は、ジェネシス・ブロックが正常に追加されています。

     ```
     defaultchannel_0.block
     ```

## 次のステップ
{: #whats-next-icp}

ICP でリモート・ピアをセットアップした後に、いくつかの操作ステップを完了してからトランザクションを実行し、ブロックチェーン・ネットワークからの分散台帳を読み取ることができます。詳しくは、[リモート・ピアの操作](remote_peer_operate_icp.html)を参照してください。

## 高可用性
{: #high-availability}

高可用性を実現するためのベスト・プラクティスとして、組織ごとに少なくとも 2 つのピアをデプロイすることを強くお勧めします。

以下のステップを実行して、ICP に高可用性リモート・ピアをデプロイします。

1. [高可用性を実現するための ICP クラスターの構成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "高可用性 {{site.data.keyword.cloud_notm}} Private クラスター") 方法を確認します。

2. クラスターにピアを複数回インストールして、さまざまなワーカー・ノードにピアをデプロイします。Helm チャートには、ネットワーク ID および OrgID に基づくアンチアフィニティー・ポリシーが含まれています。ICP クラスターは、ピアが同じ組織から同じネットワークに接続しているかどうかを検出し、これらのピアをさまざまなワーカー・ノードに配置しようと試みます。  
  **注:** ポリシーではピアをさまざまなワーカー・ノードに配置することを推奨していますが、これは必須ではありません。例えば、ワーカー・ノードが 1 つのみである場合、すべてのピアがこの同じノードにデプロイされます。

2 つ以上のピアをデプロイする場合は、[高可用性を実現するためのアプリケーション](../v10_application.html#ha-app)を構成する必要もあります。

## セキュリティー上の考慮事項
{: #icp-security}

リモート・ピアは、{{site.data.keyword.blockchainfull}} Platform の外側にデプロイされます。そのため、リモート・ピアのセキュリティーを管理する必要があります。これには、鍵管理やデータ暗号化など、エンタープライズ・プラン・ネットワークが提供するセキュリティーの重要な領域が含まれます。リモート・ピアのセキュリティーを考慮する場合は、以下のトピックを確認してください。

### ID およびアクセス制御
{: #icp-security-node}

{{site.data.keyword.cloud_notm}} の外側で、リモート・ピアとやり取りするユーザーの ID 管理、認証、およびアクセス制御をセットアップする必要があります。[ICP ユーザー管理 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "セキュリティー") を使用して、これらの機能を構成できます。

### データ・セキュリティー
{: #icp-security-data}

{{site.data.keyword.blockchainfull}} Platform エンタープライズ・プランでは、[対称鍵暗号 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "対称暗号方式") に基づくディスク全体の暗号化を使用して、ネットワークで使用されるすべてのデータを保護します。ご使用の環境でも同様のステップを実行して、リモート・ピア・データを保護する必要があります。

### データの常駐
{: #icp-data-residency}

データの常駐では、すべてのブロックチェーン台帳データの処理およびストレージが 1 つの国にとどまることが求められます。データの常駐の実現方法について詳しくは、[データの常駐](remote_peer.html#data-residency)を参照してください。

### 鍵管理
{: #icp-security-key-management}

鍵管理はリモート・ピア・セキュリティーの重要な側面です。秘密鍵が漏えいしたり、失われたりすると、悪意を持つアクターがリモート・ピアのデータおよび機能にアクセスできる可能性があります。{{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランでは、[ハードウェア・セキュリティー・モジュール](../glossary.html#hsm) (HSM) を使用して、ネットワークの秘密鍵を保管します。HSM は、他者による秘密鍵へのアクセスを防ぐ物理アプライアンスです。

ICP でリモート・ピアをデプロイする場合は、秘密鍵を管理する必要があります。{{site.data.keyword.blockchainfull_notm}} Platform によって秘密鍵が生成されますが、鍵は Platform には保管されません。鍵が漏えいしないように、安全な場所に保管することが重要です。リモート・ピアの秘密鍵は、ピア MSP の keystore フォルダー (ピア・コンテナー内の `/mnt/crypto/peer/peer/msp/keystore/` ディレクトリー) にあります。リモート・ピア内部の証明書について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理](../certificates.html)チュートリアルの[メンバーシップ・サービス・プロバイダー](../certificates.html#msp)のセクションを参照してください。

鍵エスクローを使用して、失われた秘密鍵を復旧することができます。これは、鍵が失われる前に実行する必要があります。秘密鍵を復旧できない場合は、認証局に新しい ID を登録して、新しい秘密鍵を取得する必要があります。また、参加しているチャネルから signCert を削除し、置き換える必要があります。

<!---
IBP では、秘密鍵が作成されるときに 2 つの異なるエンティティーで 2 セットの個別の鍵情報が生成されます。その後、これら 2 セットの鍵情報が結合されて秘密鍵が作成されます。
--->

### TLS
{: #icp-security-tls}

[Transport Layer Security![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL または TLS ハンドシェークの概要") (TLS) は、Hyperledger Fabric の信頼モデルに組み込まれています。{{site.data.keyword.blockchainfull_notm}} Platform のすべてのコンポーネントは、TLS を使用して認証し、相互に通信します。したがって、{{site.data.keyword.blockchainfull_notm}} Platform のネットワーク・コンポーネントは、リモート・ピアとの TLS ハンドシェークを実行できる必要があります。これに関連して、ホワイト・リストなどを使用して、クライアント・アプリからピアまで Web ファイアウォールでパススルーできる必要があります。

片方向 (サーバーのみ) ではなく双方向 (サーバー - クライアント) 認証が必要な相互 TLS を使用して、アプリケーションとエンタープライズ・プラン・ネットワークの間の通信を保護できます。ネットワーク・モニターを使用して、{{site.data.keyword.blockchainfull_notm}} Platform でピアの[相互 TLS](../v10_dashboard.html#mutual-tls) を有効にすることができます。リモート・ピアで相互 TLS を有効にするには、Hyperledger Fabric 資料の[ピア・ノードの相互 TLS を有効にする ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Transport Layer Security による通信の保護") 手順に従います。アプリケーションの相互 TLS を有効にすることを強くお勧めします。

### メンバーシップ・サービス・プロバイダーの構成
{: #icp-security-MSP}

{{site.data.keyword.blockchainfull_notm}} Platform のコンポーネントは、メンバーシップ・サービス・プロバイダー (MSP) を介して ID を使用します。MSP は、CA から発行された証明書をネットワークおよびチャネルの役割に関連付けます。MSP がリモート・ピアを使用する方法について詳しくは、[メンバーシップ・サービス・プロバイダー (MSP)](../certificates.html#msp) を参照してください。

### アプリケーション・セキュリティー
{: #icp-security-appl}

すべてのチェーンコード呼び出しは署名されているため、Fabric によってアプリケーション・セキュリティーが管理されます。また、Fabric には ACL ベースのアプリケーション・レベル・チェックも含まれています。

## ライセンスおよび料金
{: #license-pricing-icp}

{{site.data.keyword.cloud_notm}} Private の {{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアは無料のベータ版です。これは評価および試験に適しており、ICP アプリケーションとしてデプロイされます。[Github パッケージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and-->にアクセスし、[Helm チャート ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアの Helm チャート") をダウンロードできます。

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

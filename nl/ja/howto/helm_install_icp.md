---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: IBM Blockchain Platform, Helm chart file, local ICP cluster, IBM Cloud Private, upgrade Helm chart

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

# {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private のインストール
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private は、ローカル {{site.data.keyword.cloud_notm}} Private クラスターにインストールできる Helm チャート・ファイルとして提供されます。 Helm チャートをインストールすると、{{site.data.keyword.cloud_notm}} Private カタログで {{site.data.keyword.blockchainfull_notm}} Platform をアプリケーションとして見つけることができます。

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private をインストールする前に、[考慮事項と制限](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)を確認してください。 Helm チャート、料金、およびサポートに含まれるブロックチェーン・コンポーネントのデプロイ方法について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private について](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about)を参照してください。

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private では、以下の 2 つのエディションを提供します。

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private は、パスポート・アドバンテージ (PPA) を通じて提供されます。 [パスポート・アドバンテージ・オンライン ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/software/passportadvantage/pao_customer.html "パスポート・アドバンテージ・オンライン") にアクセスするには、所定のライセンスが必要です。 購入時に、{{site.data.keyword.blockchainfull_notm}} Platform のテクニカル・サポートが含まれています。

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition は、探索、開発、およびテスト用に無料で提供されています。 この無料バージョンには、[GitHub ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts") からアクセスできます。 {{site.data.keyword.IBM_notm}} では、Community Edition のサポートを提供していないことに注意してください。

## Helm チャートをインストールするための前提条件
{: #helm-install-prereqs}

Helm チャートをインストールする前に、{{site.data.keyword.cloud_notm}} Private クラスターを構成し、ポッド・セキュリティー・ポリシーにバインドされた新しいターゲット名前空間を作成しておく必要があります。 [{{site.data.keyword.cloud_notm}} Private クラスターのセットアップおよび構成](/docs/services/blockchain/ICP_setup.html#icp-setup)手順を確認してください。

## ファイアウォールの内側での {{site.data.keyword.blockchainfull_notm}} Platform のインストール
{: #helm-install-prereqs-firewall}

インターネット接続がなくても、ファイアウォールの内側で {{site.data.keyword.blockchainfull_notm}} Platform コンポーネントをデプロイできます。 PPA パッケージには、{{site.data.keyword.blockchainfull_notm}} Platform が使用するすべての Fabric コンポーネント Docker イメージが含まれているため、デプロイ時に DockerHub からダウンロードする必要はありません。

ただし、Community Edition では、デプロイ時に DockerHub から必要な Fabric コンポーネント Docker イメージをダウンロードするように構成されているため、Community Edition Helm チャートにはこれらのイメージが含まれていません。 使用可能なインターネット接続がない場合、デプロイメントは失敗します。 したがって、{{site.data.keyword.cloud_notm}} Private クラスターにアーカイブをインストールするには、その前に、インターネットに接続されたマシンでアーカイブを作成するための追加ステップを実行する必要があります。 以下のイメージが必要です。
- [Fabric ピア ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic ピア")
- [Fabric CA ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric 順序付けプログラム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric 順序付けプログラム")
- [Fabric Couch データベース ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch データベース")
- [MSP フォルダーを含む、コンポーネントのブートストラップおよび構成に使用される `Init` イメージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [チェーンコード・コンテナーを実行するためにピアによって使用される Docker-in-Docker (DinD) イメージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-dind/)

これらのイメージの使用方法について詳しくは、[インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html) を参照してください。 仕様ファイル `manifest.yaml` は、Helm チャート内の `ibm-blockchain-platform-dev/ibm_cloud_pak` ディレクトリーにあります。

## {{site.data.keyword.cloud_notm}} Private への Helm チャートのインポート
{: #helm-install-importing}

1. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private の Helm チャート・ファイルを[パスポート・アドバンテージ・オンライン ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/software/passportadvantage/pao_customer.html "パスポート・アドバンテージ・オンライン") からダウンロードします。無料の Community Edition の場合は、[GitHub ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts") からダウンロードします。この Helm チャート・パッケージには、CA、順序付けプログラム、およびピア用の 3 つのサブ Helm チャートが含まれています。

2. {{site.data.keyword.cloud_notm}} Private クラスターにまだログインしていない場合は、ログインします。

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html) が構成されていることを確認します。 Docker CLI を構成したら、以下のコマンドを使用してクラスター上のイメージ・レジストリーにアクセスします。
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. 以下のコマンドを使用して、Helm チャートをアップロードする {{site.data.keyword.cloud_notm}} Private 内のリポジトリーの名前を見つけます。
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  このコマンドが正常に完了すると、クラスター内のリポジトリーのリストが表示されます。 ターゲット・リポジトリーの名前を選択して保存します。 これは、以下のコマンドで使用する必要があります。

5. コマンド・ラインを使用して Helm チャートをインポートします。
  Helm チャートをインポートするために実行するコマンドは、Helm チャートがパスポート・アドバンテージ (PPA) または GitHub のどちらからダウンロードされたかによって異なります。

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Helm チャートを PPA からダウンロードした場合は、以下の手順に従います。
    PPA からダウンロードした Helm チャート・パッケージを保管したディレクトリーから、{{site.data.keyword.cloud_notm}} Private CLI で以下のコマンドを実行して、Helm チャートを {{site.data.keyword.cloud_notm}} Private クラスターにインポートします。

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    以下の値を置き換えます。
    - `<archive-name>` は、ダウンロードした `.tgz` ファイルの名前で置き換えます。
    - `<cluster_CA_domain>:8500` は、{{site.data.keyword.cloud_notm}} Private クラスターにログインするために使用するドメインで置き換えます。
    - `<repo-name>` は、チャートをアップロードする Helm リポジトリーで置き換えます。 「cloudctl catalog repos」を実行してリポジトリーをリストします。

    このコマンドが正常に完了すると、以下のような情報が表示されます。

    ```  
    アーカイブの解凍中
    OK

    Docker イメージのインポート中
      イメージの処理中: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0 としてプッシュ中
    イメージの mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0 としてプッシュ中
    イメージの mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-fabric-ca-amd64:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0 としてプッシュ中
    イメージの mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-fabric-ca-s390x:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0 としてプッシュ中
    イメージの mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-fabric-peer-amd64:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0 としてプッシュ中
    イメージの mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-fabric-peer-s390x:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0 としてプッシュ中
    イメージ mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-couchdb-amd64:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0 としてプッシュ中
    イメージ mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-couchdb-s390x:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0 としてプッシュ中
    イメージ mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-dind-amd64:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0 としてプッシュ中
    イメージ mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0 のプッシュ中
      イメージの処理中: ibmcom/ibp-dind-s390x:1.4.0
      イメージのロード中
      イメージのタグ付け中
      イメージを mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0 としてプッシュ中
    イメージ mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0 のプッシュ中
    OK

    Helm チャートのアップロード中
      チャートの処理中: charts/ibm-blockchain-platform-1.0.2.tgz
      チャートの values.yaml を更新中
      チャートのアップロード中
    ロードされた Helm チャート
    OK

    チャートの同期
  同期が開始されました
  OK
    ```  
    </details>

  - **GitHub からダウンロードした Community Edition**
    GitHub から Helm チャートをダウンロードした場合は、以下の手順に従います。

    GitHub からダウンロードした Helm チャート・パッケージを保管したディレクトリーから、{{site.data.keyword.cloud_notm}} Private CLI で以下のコマンドを実行して、Helm チャートを {{site.data.keyword.cloud_notm}} Private クラスターにインポートします。 GitHub からダウンロードした Helm チャートをインポートするには、以下のコマンドを実行します。
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    以下の値を置き換えます。
    - `<helm_chart_from_github>` は、ダウンロードした .tgz ファイルの名前で置き換えます。
    - `<repo-name>` は、チャートをアップロードする Helm リポジトリーで置き換えます。 「cloudctl catalog repos」を実行してリポジトリーをリストします。

    このコマンドが正常に完了すると、以下のような情報が表示されます。
    ```
    Helm チャートのロード中
  ロードされた Helm チャート

    チャートの同期
  同期が開始されました
  OK
    ```

{{site.data.keyword.cloud_notm}} Private コンソールで**「カタログ」**ボタンをクリックし、左側のナビゲーション・パネルで**「ブロックチェーン」**をクリックして、インポートが成功したことを確認します。 成功した場合は、**ibm-blockchain-platform-prod** タイルまたは **ibm-blockchain-platform-dev** タイルが「{{site.data.keyword.cloud_notm}} Private カタログ ({{site.data.keyword.cloud_notm}} Private Catalog)」ページに表示されます。

## 個々のコンポーネントのデプロイ
{: #helm-install-deploying-components}

Helm チャートをインストールしたら、{{site.data.keyword.cloud_notm}} Private カタログ内の **ibm-blockchain-platform-prod** タイルまたは **ibm-blockchain-platform-dev** タイルをクリックして開きます。 構成ページを使用して、ブロックチェーン・ネットワークの個々のコンポーネントをデプロイできます。 ブロックチェーン・ソリューションに必要なコンポーネント、およびそれらをデプロイする順序について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private の概説](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp)を参照してください。

次に、個々のコンポーネントをデプロイします。

- 順序付けプログラムをデプロイする場合は、まず順序付けプログラムの認証局をセットアップする必要があります。 CA は、組織内の他のコンポーネントによって使用される証明書を生成します。 詳しくは、[{{site.data.keyword.cloud_notm}} Private への {{site.data.keyword.blockchainfull_notm}} Platform 認証局のデプロイ](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)を参照してください。 その後、ネットワークの共通バインディングとなる順序付けプログラムをデプロイできます。 詳しくは、[{{site.data.keyword.cloud_notm}} Private での {{site.data.keyword.blockchainfull_notm}} Platform 順序付けプログラムのデプロイ](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)を参照してください。

- ピアをデプロイする場合は、まずピアの認証局をセットアップする必要があります。 CA は、ピアによって使用される証明書を生成します。 詳しくは、[{{site.data.keyword.cloud_notm}} Private への {{site.data.keyword.blockchainfull_notm}} Platform 認証局のデプロイ](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)を参照してください。 その後、ネットワークに参加する準備ができたら、チャネルに参加し、トランザクションを承認し、データを保管するピアをデプロイできます。 詳しくは、ピアが参加するブロックチェーン・ネットワークに応じて、[{{site.data.keyword.cloud_notm}} Private での {{site.data.keyword.blockchainfull_notm}} ピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy)または[スターター・プランまたはエンタープライズ・プラン・ネットワークのための {{site.data.keyword.blockchainfull_notm}} ピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)を参照してください。

## {{site.data.keyword.cloud_notm}} Private での Helm チャートのアップグレード
{: #helm-install-upgrading}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.1 を使用している場合は、{{site.data.keyword.cloud_notm}} Private の資料の [バンドルされた製品のアップグレード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "バンドルされた製品のアップグレード") の手順に従い、Helm チャートを {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 にアップグレードできます。 その手順の `helm upgrade` コマンドのほかにも、[{{site.data.keyword.cloud_notm}} Private コンソールを使用して Helm リリースをアップグレードする](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui)こともできます。

Helm チャートをアップグレードするためには、{{site.data.keyword.cloud_notm}} Private に対する**クラスター管理者**役割または**チーム管理者**役割が必要です。
{:note}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 から v1.0.1 へのロールバックはサポートされていません。

### {{site.data.keyword.cloud_notm}} Private コンソールからの Helm リリースのアップグレード
{: #helm-install-upgrading-ui}

コンポーネントの Helm リリースを {{site.data.keyword.cloud_notm}} Private コンソールからアップグレードする場合は、`「値を再利用する (Reuse values)」`にチェックを入れて、パラメーターを変更しないようにする必要があります。
{:important}  

Helm リリースを {{site.data.keyword.cloud_notm}} Private コンソールからアップグレードするには、以下の手順を実行します。
1. メニュー・バーから、**「ワークロード」** > **「Helm リリース」**をクリックします。
2. アップグレードする Helm リリースを選択します。
3. **「詳細およびアップグレード」**セクションで、`「使用可能バージョン」`の下のバージョン番号を確認します。
4. バージョン番号の下の**「ReadMe」**をクリックし、リリース・ノートを参照して重大な変更がないか確認します。
5. **「アップグレード」**をクリックし、ドロップダウン・リストからリポジトリーおよびバージョンを正確に選択します。
6. `「値を再利用する (Reuse values)」`が選択されていることを確認します。
7. **「アップグレード」**をクリックします。

確認メッセージが表示され、コンソールの左上隅のリリース名の横に表示されるリリース状況が更新されるまで待ってください。

### ピアをアップグレードするための追加手順の実行
{: #helm-install-upgrading-peer}

アップグレード・プロセスを完了するためには、ピアをアップグレードした後にいくつかの追加の手順を実行する必要があります。 ピアの Helm リリースのページの**「ポッド」**セクションの下に、以下の 2 つのピア・ポッドが表示されています。
- 状況が `CrashLoopBackOff` の新しいピア・ポッド
- 状況が `Running` の元のピア・ポッド

関連付けられているピア名をメモしてください。以下の手順で使用する必要があります。
{:tip}

ピアのアップグレード・プロセスを完了するために、CLI から以下の手順を実行します。
1. {{site.data.keyword.cloud_notm}} Private CLI からクラスターにログインして、`kubectl get replicaset` コマンドを実行します。
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. 元のピアに対応するピアのレプリカセットを見つけます。 ピアのレプリカセットの名前は、{{site.data.keyword.cloud_notm}} Private コンソールの**「ポッド」**セクションに表示されるピア名の先頭部分と一致している必要があります。
3. ピアのレプリカセットを削除します。
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. 新しいピア・ポッドを削除します。 これは UI で状況が `CrashLoopBackOff` のピア・ポッドです。
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

新しいピア・ポッドが作成され、状況が `Running` と表示されたら、ピアは正常にアップグレードされています。

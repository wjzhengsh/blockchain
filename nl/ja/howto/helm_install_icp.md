---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private への {{site.data.keyword.blockchainfull_notm}} Platform のインストール
{: #remote-peer-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private は、ローカル ICP クラスターにインストールできる Helm チャート・ファイルとして提供されます。Helm チャートをインストールすると、ICP カタログで {{site.data.keyword.blockchainfull_notm}} Platform をアプリケーションとして見つけることができます。

{{site.data.keyword.blockchainfull_notm}} Platform for ICP をインストールする前に、[考慮事項と制限](../ibp-for-icp-about.html#ibp-icp-considerations)を確認してください。Helm チャート、料金、およびサポートに含まれるブロックチェーン・コンポーネントのデプロイ方法について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform for ICP について](../ibp-for-icp-about.html)を参照してください。

{{site.data.keyword.blockchainfull_notm}} Platform for ICP では、以下の 2 つのエディションを提供します。

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private は、パスポート・アドバンテージ (PPA) を通じて提供されます。[パスポート・アドバンテージ・オンライン ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/software/passportadvantage/pao_customer.html "パスポート・アドバンテージ・オンライン") にアクセスするには、所定のライセンスが必要です。購入時に、{{site.data.keyword.blockchainfull_notm}} Platform のテクニカル・サポートが含まれています。

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition は、探索、開発、およびテスト用に無料で提供されています。この無料バージョンには、[GitHub ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts") からアクセスできます。{{site.data.keyword.IBM_notm}} では、Community Edition のサポートを提供していないことに注意してください。

## Helm チャートをインストールするための前提条件
{: #helm-install-prereqs}

Helm チャートをインストールする前に、ICP クラスターを構成しておく必要があります。[ICP クラスターのセットアップおよび構成](../ICP_setup.html)手順を確認してください。

## ファイアウォールの内側での {{site.data.keyword.blockchainfull_notm}} Platform のインストール
{: #helm-install-prereqs-firewall}

インターネット接続がなくても、ファイアウォールの内側で {{site.data.keyword.blockchainfull_notm}} Platform コンポーネントをデプロイできます。PPA パッケージには、{{site.data.keyword.blockchainfull_notm}} Platform が使用するすべての Fabric コンポーネント Docker イメージが含まれているため、デプロイ時に DockerHub からダウンロードする必要はありません。

ただし、Community Edition では、デプロイ時に DockerHub から必要な Fabric コンポーネント Docker イメージをダウンロードするように構成されているため、Community Edition Helm チャートにはこれらのイメージが含まれていません。使用可能なインターネット接続がない場合、デプロイメントは失敗します。したがって、ICP クラスターにアーカイブをインストールするには、その前に、インターネットに接続されたマシンでアーカイブを作成するための追加ステップを実行する必要があります。以下のイメージが必要です。
- [Fabric ピア ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic ピア")
- [Fabric CA ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric 順序付けプログラム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric 順序付けプログラム")
- [Fabric Couch データベース ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch データベース")
- [MSP フォルダーを含む、コンポーネントのブートストラップおよび構成に使用される `Init` イメージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [チェーンコード・コンテナーを実行するためにピアによって使用される Docker-in-Docker (DinD) イメージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hub.docker.com/r/ibmcom/ibp-dind/)

これらのイメージの使用方法について詳しくは、[インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html) を参照してください。仕様ファイル `manifest.yaml` は、Helm チャート内の `ibm-blockchain-platform-dev/ibm_cloud_pak` ディレクトリーにあります。

## ICP への Helm チャートのインポート

1. IBM Blockchain Platform for ICP の Helm チャート・ファイルを[パスポート・アドバンテージ・オンライン ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/software/passportadvantage/pao_customer.html "パスポート・アドバンテージ・オンライン") からダウンロードします。無料の Community Edition の場合は、[GitHub ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts") からダウンロードします。この Helm チャート・パッケージには、CA、順序付けプログラム、およびピア用の 3 つのサブ Helm チャートが含まれています。

2. ICP クラスターにまだログインしていない場合は、ログインします。

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html) が構成されていることを確認します。Docker CLI を構成したら、以下のコマンドを使用してクラスター上のイメージ・レジストリーにアクセスします。
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. 以下のコマンドを使用して、Helm チャートをアップロードする ICP 内のリポジトリーの名前を見つけます。
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  このコマンドが正常に完了すると、クラスター内のリポジトリーのリストが表示されます。ターゲット・リポジトリーの名前を選択して保存します。これは、以下のコマンドで使用する必要があります。

6. コマンド・ラインを使用して Helm チャートをインポートします。
Helm チャートをインポートするために実行するコマンドは、Helm チャートがパスポート・アドバンテージ (PPA) または GitHub のどちらからダウンロードされたかによって異なります。

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Helm チャートを PPA からダウンロードした場合は、以下の手順に従います。
PPA からダウンロードした Helm チャート・パッケージを保管したディレクトリーから、ICP CLI で以下のコマンドを実行して、Helm チャートを ICP クラスターにインポートします。

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    以下の値を置き換えます。
    - `<archive-name>` は、ダウンロードした `.tgz` ファイルの名前で置き換えます。
    - `<cluster_CA_domain>:8500` は、ICP クラスターにログインするために使用するドメインで置き換えます。
    - `<repo-name>` は、チャートをアップロードする Helm リポジトリーで置き換えます。「cloudctl catalog repos」を実行してリポジトリーをリストします。

    このコマンドが正常に完了すると、以下のような情報が表示されます。
    ```
    アーカイブの解凍中
    OK

    Docker イメージのインポート中
      イメージの処理中: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      イメージの処理中: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      イメージの処理中: ibmblockchain/hlfabric-ca-amd64:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      イメージの処理中: ibmblockchain/hlfabric-ca-s390x:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      イメージの処理中: ibmblockchain/v1fabric-peer-amd64:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      イメージの処理中: ibmblockchain/v1fabric-peer-s390x:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      イメージの処理中: ibmblockchain/fabric-couchdb-amd64:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      イメージの処理中: ibmblockchain/fabric-couchdb-s390x:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      イメージの処理中: ibmcom/icp-dind-amd64:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      イメージの処理中: ibmcom/icp-dind-s390x:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      イメージの処理中: ibmcom/icp-busybox-s390x:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      イメージの処理中: ibmcom/icp-busybox-amd64:1.2.1
      イメージのロード中
      イメージのタグ付け中
      以下としてイメージをプッシュ中 (Pushing image as): mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    イメージのプッシュ中 (Pushing image) mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Helm チャートのアップロード中
      チャートの処理中: charts/ibm-blockchain-platform-0.1.5.tgz
      チャートの values.yaml を更新中
      チャートのアップロード中
    ロードされた Helm チャート
    OK

    チャートの同期
  同期が開始されました
  OK
    ```

  - **GitHub からダウンロードした Community Edition**
    GitHub から Helm チャートをダウンロードした場合は、以下の手順に従います。

    GitHub からダウンロードした Helm チャート・パッケージを保管したディレクトリーから、ICP CLI で以下のコマンドを実行して、Helm チャートを ICP クラスターにインポートします。GitHub からダウンロードした Helm チャートをインポートするには、以下のコマンドを実行します。
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    以下の値を置き換えます。
    - `<helm_chart_from_github>` は、ダウンロードした .tgz ファイルの名前で置き換えます。
    - `<repo-name>` は、チャートをアップロードする Helm リポジトリーで置き換えます。「cloudctl catalog repos」を実行してリポジトリーをリストします。

    このコマンドが正常に完了すると、以下のような情報が表示されます。
    ```
    Helm チャートのロード中
  ロードされた Helm チャート

    チャートの同期
  同期が開始されました
  OK
    ```

ICP コンソールで**「カタログ」**ボタンをクリックし、左側のナビゲーション・パネルで**「ブロックチェーン」**をクリックして、インポートが成功したことを確認します。成功した場合は、**ibm-blockchain-platform-prod** タイルまたは **ibm-blockchain-platform-dev** タイルが「ICP カタログ (ICP Catalog)」ページに表示されます。


## PodSecurityPolicy の要件

Helm チャートを {{site.data.keyword.cloud_notm}} Private にインポートしたら、コンポーネントをインストールする前に、[PodSecurityPolicy ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "ポッド・セキュリティー・ポリシー") をターゲット名前空間にバインドする必要があります。事前定義された PodSecurityPolicy を選択するか、クラスター管理者にカスタム PodSecurityPolicy の作成を依頼してください。
- 事前定義された PodSecurityPolicy 名: [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- カスタム PodSecurityPolicy 定義:
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- カスタム PodSecurityPolicy のカスタム ClusterRole:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- カスタム ClusterRole のカスタム ClusterRoleBinding:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## 個々のコンポーネントのデプロイ

Helm チャートをインストールしたら、ICP カタログ内の **ibm-blockchain-platform-prod** タイルまたは **ibm-blockchain-platform-dev** タイルをクリックして開きます。構成ページを使用して、ブロックチェーン・ネットワークの個々のコンポーネントをデプロイできます。ブロックチェーン・ソリューションに必要なコンポーネント、およびそれらをデプロイする順序について詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform for ICP デプロイメント・ガイド](../ibp_for_icp_deployment_guide.html)を参照してください。

次に、個々のコンポーネントをデプロイします。

- 順序付けプログラムをデプロイする場合は、まず順序付けプログラムの認証局をセットアップする必要があります。CA は、組織内の他のコンポーネントによって使用される証明書を生成します。詳しくは、[ICP への {{site.data.keyword.blockchainfull_notm}} Platform 認証局のデプロイ](CA_deploy_icp.html)を参照してください。その後、ネットワークの共通バインディングとなる順序付けプログラムをデプロイできます。詳しくは、[ICP での {{site.data.keyword.blockchainfull_notm}} Platform 順序付けプログラムのデプロイ](orderer_deploy_icp.html)を参照してください。

- ピアをデプロイする場合は、まずピアの認証局をセットアップする必要があります。CA は、ピアによって使用される証明書を生成します。詳しくは、[ICP への {{site.data.keyword.blockchainfull_notm}} Platform 認証局のデプロイ](CA_deploy_icp.html)を参照してください。その後、ネットワークに参加する準備ができたら、チャネルに参加し、トランザクションを承認し、データを保管するピアをデプロイできます。詳しくは、ピアが参加するブロックチェーン・ネットワークに応じて、[ICP での {{site.data.keyword.blockchainfull_notm}} ピアのデプロイ](peer_deploy_icp.html)または[スターター・プランまたはエンタープライズ・プラン・ネットワークのための {{site.data.keyword.blockchainfull_notm}} ピアのデプロイ](peer_deploy_ibp.html)を参照してください。

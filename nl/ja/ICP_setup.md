---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-03"

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

# {{site.data.keyword.cloud_notm}} Private のセットアップ
{: #icp-setup}

{{site.data.keyword.blockchainfull}} Platform コンポーネントをデプロイし、{{site.data.keyword.cloud_notm}} Private にブロックチェーン・ネットワークを構築する前に、ご使用の環境で {{site.data.keyword.cloud_notm}} Private をセットアップする必要があります。
{:shortdesc}

## 前提条件
{: #icp-setup-prerequisites}

以下の前提条件を満たし、{{site.data.keyword.cloud_notm}} Private をインストールするための環境を準備します。

### Docker
{{site.data.keyword.cloud_notm}} Private では、Docker をインストールする必要があります。 [{{site.data.keyword.cloud_notm}} Private のインストール ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html " {{site.data.keyword.cloud_notm}} Private のインストール") の関連手順に従って、Docker をインストールします。

### {{site.data.keyword.cloud_notm}} Private 設定
{{site.data.keyword.cloud_notm}} Private をインストールする前に、以下のヒントを参考にして {{site.data.keyword.cloud_notm}} Private インストール用にノードを準備します。 {{site.data.keyword.cloud_notm}} Private の追加の前提条件については、[{{site.data.keyword.cloud_notm}} Private の資料 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "インストールのためのクラスターの準備") を参照してください。

#### `vm.max_map_count` 設定の更新
{{site.data.keyword.cloud_notm}} Private は、ロギングおよび計量に Elastic Search を使用します。 メモリー不足例外を回避するには、Elastic Search で `vm.max_map_count` システム・プロパティーを構成する必要があります。 {{site.data.keyword.cloud_notm}} Private をインストールする前に、[Elastic Search configuration instructions ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Virtual memory") を参照して、各ノードでこのプロパティーを構成してください。 以下のコマンドを使用して、このプロパティーを完全に設定できます。

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### クラスター内の各ノードの `/etc/hosts` ファイルの構成

- {{site.data.keyword.cloud_notm}} Private は [Kubernetes ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Learn Kubernetes Basics") を使用してコンテナー化アプリケーションを管理します。 各ノードの `/etc/hosts` ファイルでホスト名が構成されていない場合、Kubernetes ドメイン・ネーム・サーバー (DNS) は失敗します。 [クラスター内の各ノードの IP アドレス、ホスト名、および短縮名 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "クラスターの構成") を各ノードの `/etc/hosts` ファイルに挿入します。

- [IPv6 は {{site.data.keyword.cloud_notm}} Private ではサポートされません ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "IPv6 はサポートされない")。 {{site.data.keyword.cloud_notm}} Private クラスター内の DNS サービスに関する問題を回避するには、以下のように行の先頭に `#` 記号を付けてこの行をコメント化し、各ノードの `/etc/hosts` ファイルの IPv6 設定を無効にします。
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### 必要なリソース
{: #icp-setup-resources}

{{site.data.keyword.cloud_notm}} Private システムが各 Fabric ランタイム・コンポーネントの最小ハードウェア・リソース要件を満たしていることを確認します。

| コンポーネント | vCPU | RAM | データ・ストレージ用ディスク |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |
| 順序付けプログラム | 2 | 512 MB | 100 GB (拡張可能であること) |
| ピア | 2 | 2 GB | 50 GB (拡張可能であること) |
| ピア用 CouchDB | 2| 2 GB |50 GB (拡張可能であること) |

 **注:**
 - vCPU は仮想マシンに割り当てられる仮想コアであるか、サーバーが仮想マシン用にパーティション化されていない場合は物理プロセッサー・コアです。 {{site.data.keyword.cloud_notm}} Private でのデプロイメントの仮想プロセッサー・コア (VPC) を決定する場合は、vCPU 要件を考慮する必要があります。 VPC は {{site.data.keyword.IBM_notm}} 製品のライセンス交付コストを決定する単位です。 VPC を決定するシナリオについて詳しくは、[仮想プロセッサー・コア (VPC) ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html) を参照してください。
 - テストおよび試験目的には、これらの最小リソース・レベルで十分です。 トランザクションが大量にある環境では、十分に大きいストレージを割り振ることが重要です。例えば、ピア用に 250 GB、順序付けサービス用に 500 GB などです。 使用するストレージの量は、ネットワークから要求されるトランザクションの数と署名の数によって異なります。 ピア上または順序付けプログラム上のストレージをまもなく使い切る場合は、より大きいファイル・システムを持つ新しいピアまたは順序付けプログラムをデプロイして、同じチャネル上の他のコンポーネントと同期する必要があります。

#### ストレージについての考慮事項
{: #icp-setup-storage-considerations}

* コンポーネントが使用するストレージを設定する必要があります。 デフォルト設定を使用すると、ピアの Helm チャートによって、ピア・データ用に `my-data-pvc` という名前の新しい永続ボリューム・クレームが作成されます。 台帳データベースとして CouchDB を選択すると、Helm チャートによって、台帳データベース用に `statedb-pvc` という名前の別の永続ボリューム・クレームが作成されます。
* デフォルトのストレージ設定を使用しない場合は、{{site.data.keyword.cloud_notm}} Private のインストール時に*新しい* storageClass がセットアップされたことを確認します。セットアップされていない場合は、デプロイの前に Kubernetes システム管理者が storageClass を作成する必要があります。
* [動的プロビジョニング ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "動的ボリューム・プロビジョニング") は、{{site.data.keyword.cloud_notm}} Private の amd64 ノードにのみ使用可能です。 したがって、クラスターに s390x と amd64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。
* 動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "永続ボリューム") を作成し、Kubernetes Persistent Volume Claim (PVC) バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。
* NFS v2/v3 永続ボリュームを使用する場合は、NFS ファイル・システムが存在するホスト・システムで、**rpc-statd** とも呼ばれる **NFS status monitor for NFSv2/v3 Filesystem Locks** モジュールを有効にする必要があります。 このモジュールにより、NFS ファイル・システムは、他のプロセスが保持しているファイルの排他ロックを検査できます。 以下のコマンドを実行して、このモジュールを有効にします。

  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## {{site.data.keyword.cloud_notm}} Private のインストール
{: #icp-setup-install}

以下の手順を実行して、ご使用の環境に {{site.data.keyword.cloud_notm}} Private をインストールし、セットアップします。

1. バージョン 3.1.0 の [{{site.data.keyword.cloud_notm}} Private ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) クラスターをインストールします。 Helm チャートを開発、テスト、または実験用に使用する場合は、[{{site.data.keyword.cloud_notm}} Private Community Edition バージョン 3.1.0 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE バージョン 3.1.0") を無料でインストールできます。

2. {{site.data.keyword.cloud_notm}} Private CLI [3.1.0 ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html) をインストールして、CA をインストールし、操作します。

3. ターゲット名前空間のポッド・セキュリティー・ポリシーをセットアップします。[次のセクション](/docs/services/blockchain/howto/ICP_setup.html#icp-setup-psp)に説明があります。

{{site.data.keyword.cloud_notm}} Private をインストールして、ポッド・セキュリティー・ポリシーをターゲット名前空間にバインドしたら、[{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Helm チャート](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)を {{site.data.keyword.cloud_notm}} Private クラスターにインポートする作業に進むことができます。

## PodSecurityPolicy の要件
{: #icp-setup-psp}

Helm チャートを使用してコンポーネントをデプロイする前に、新しいターゲット名前空間を作成し、[PodSecurityPolicy ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "ポッド・セキュリティー・ポリシー") をその名前空間にバインドする必要があります。事前定義された PodSecurityPolicy を選択するか、クラスター管理者にカスタム PodSecurityPolicy の作成を依頼してください。
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

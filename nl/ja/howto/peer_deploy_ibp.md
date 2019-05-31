---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: TLS CA, IBM Blockchain Platform, peer, deploy peers, CouchDB container use Kubernetes, IBM Cloud

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

# {{site.data.keyword.cloud_notm}} Private でのピアのデプロイおよびスターター・プランまたはエンタープライズ・プランへの接続
{: #ibp-peer-deploy}

以下の手順では、{{site.data.keyword.blockchainfull}} Platform ピアを {{site.data.keyword.cloud_notm}} Private にデプロイして、このピアを {{site.data.keyword.cloud_notm}} 上またはローカル {{site.data.keyword.cloud_notm}} Private 上のスターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークに接続する方法を説明します。
{:shortdesc}

ピアをデプロイする前に、[考慮事項と制限](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)を確認してください。

スターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークでは、Hyperledger Fabric v1.1 または v1.2.1 が実行されている必要があります。 Hyperledger Fabric のバージョンを確認するには、ネットワーク・モニターで[「ネットワーク設定 (Network Preferences)」ウィンドウ](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)を開きます。

## 必要なリソース
{: #ibp-peer-deploy-resources-required}

{{site.data.keyword.cloud_notm}} Private システムが最小ハードウェア・リソース要件を満たしていることを確認します。

| コンポーネント | vCPU | RAM | データ・ストレージ用ディスク |
|-----------|------|-----|-----------------------|
| ピア | 2 | 2 GB | 50 GB (拡張可能であること) |
| ピア用 CouchDB<br>(CouchDB を使用する場合だけに該当) | 2| 2 GB | 50 GB (拡張可能であること) |

 **注:**
 - vCPU は仮想マシンに割り当てられる仮想コアであるか、サーバーが仮想マシン用にパーティション化されていない場合は物理プロセッサー・コアです。 {{site.data.keyword.cloud_notm}} Private でのデプロイメントの仮想プロセッサー・コア (VPC) を決定する場合は、vCPU 要件を考慮する必要があります。 VPC は IBM 製品のライセンス交付コストを決定する単位です。 VPC を決定するシナリオについて詳しくは、[仮想プロセッサー・コア (VPC) ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html) を参照してください。
 - テストおよび試験目的には、これらの最小リソース・レベルで十分です。 大量のトランザクションがある環境の場合は、十分な量のストレージを割り振ることが重要です。例えば、ピア用に 250 GB を割り振ります。 使用するストレージの量は、ネットワークから要求されるトランザクションの数と署名の数によって異なります。 ピア上のストレージをまもなく使い切る場合は、より大きいファイル・システムを持つ新規ピアをデプロイして、同じチャネル上の他のコンポーネントと同期する必要があります。

## ストレージ
{: #ibp-peer-deploy-storage}

ピアが使用するストレージを設定する必要があります。 デフォルト設定を使用する場合、Helm チャートによって、ピア・データ用に `my-data-pvc` という名前の新規 8 Gi Persistent Volume Claim (PVC) と、状態データベース用に `statedb-pvc` という名前の別の 8 Gi PVC が作成されます。

デフォルトのストレージ設定を使用しない場合は、{{site.data.keyword.cloud_notm}} Private のインストール時に*新しい* `storageClass` がセットアップされたことを確認します。セットアップされていない場合は、デプロイの前に Kubernetes システム管理者が storageClass を作成する必要があります。

ピアのデプロイ先として、amd64 または s390x のプラットフォームを選択できます。 ただし、[動的プロビジョニング ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "動的プロビジョニング") は {{site.data.keyword.cloud_notm}} Private 内の amd64 ノードのみで使用可能であることに注意してください。 クラスターに s390x と amd64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。

動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "永続ボリューム") を作成し、Kubernetes PVC バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。

## ピアをデプロイするための前提条件
{: #ibp-peer-deploy-prerequisites}

1. {{site.data.keyword.cloud_notm}} Private にピアをインストールするには、その前に[{{site.data.keyword.cloud_notm}} Private をインストール](/docs/services/blockchain/ICP_setup.html#icp-setup)し、[{{site.data.keyword.blockchainfull_notm}} Platform Helm チャートをインストール](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)する必要があります。

2. Community Edition を使用しており、インターネットに接続されていない {{site.data.keyword.cloud_notm}} Private クラスター上でこの Helm チャートを実行する場合は、アーカイブを {{site.data.keyword.cloud_notm}} Private クラスターにインストールするためには、インターネットに接続されたマシン上でそれらのアーカイブを事前に作成する必要があります。 詳しくは、[インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加"){:new_window} を参照してください。 仕様ファイル manifest.yaml は、Helm チャート内の ibm-blockchain-platform-dev/ibm_cloud_pak にあります。

3. {{site.data.keyword.cloud_notm}} のスターター・プランまたはエンタープライズ・プラン・ネットワークのメンバーである組織が必要です。 ピアは、{{site.data.keyword.blockchainfull_notm}} Platform のネットワークの API エンドポイント、Hyperledger Fabric CA、および順序付けサービスを利用して作動します。 ブロックチェーン・ネットワークのメンバーではない場合は、ネットワークを作成するか、ネットワークに参加する必要があります。 詳しくは、[ネットワークの作成](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network)または[ネットワークへの参加](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw)を参照してください。

4. まず {{site.data.keyword.cloud_notm}} Private に [CA をデプロイ](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)する必要があります。 この CA を TLS CA として使用します。 ピアをデプロイする前に、{{site.data.keyword.cloud_notm}} Private 上で CA を操作するための[前提条件となる手順](/docs/services/blockchain/howto/CA_operate.html#ca-operate-prerequisites)を実行してください。 これらの手順以外の操作を実行する必要はありません。

5. {{site.data.keyword.cloud_notm}} Private コンソールから、TLS CA のクラスター・プロキシー IP アドレスの値を取得します。 **注:** プロキシー IP にアクセスするには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。 {{site.data.keyword.cloud_notm}} Private クラスターにログインします。 左側のナビゲーション・パネルで**「プラットフォーム」**、**「ノード」**の順にクリックし、クラスターで定義されているノードを表示します。 `proxy` 役割を持つノードをクリックし、テーブルから`「ホスト IP」`の値をコピーします。 **重要:** この値を保存して、Helm チャートの`「プロキシー IP (Proxy IP)」`フィールドを構成する際に使用します。

6. ピアの構成ファイルを作成して、Kubernetes 秘密として {{site.data.keyword.cloud_notm}} Private に保管します。 このファイルを作成する手順については、[次のセクション](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file)を参照してください。

## 構成ファイルの作成
{: #ibp-peer-deploy-config-file}

ピアをデプロイする前に、{{site.data.keyword.cloud_notm}} 上の認証局とピア ID に関する重要な情報が含まれた構成 JSON ファイルを作成する必要があります。 次に、[Kubernetes 秘密 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/configuration/secret/ "秘密") オブジェクトを使用して、構成時にこのファイルを Helm チャートに渡す必要があります。 このファイルによって、ピアは、スターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークに参加するために必要な証明書を {{site.data.keyword.cloud_notm}} 上の認証局から取得できます。 このファイルには、管理者ユーザーとしてピアを操作することを可能にする管理者証明書も含まれています。

以下の手順では、編集してローカル・ファイル・システムに保存できるテンプレート JSON が得られます。 次に、CA を使用して構成ファイルを完成させる方法について説明します。

### 構成ファイル

構成ファイルのテンプレートを以下に示します。
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

このファイル全体をテキスト・エディターにコピーしてください。テキスト・エディターでこのファイルを編集して、ローカル・ファイル・システムに JSON ファイルとして保存できます。 このファイルの先頭の 2 つのセクションである `"enrollment"` と `"tls"` のみに入力する必要があります。

構成ファイルを完成させるには、スターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークのネットワーク・モニターからいくつかのステップを実行する必要があります。

1. [スターター・プランまたはエンタープライズ・プランの CA のエンドポイント情報を取得します](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-ibp-ca-endpoint)。
2. [ピアを CA に登録します](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-peer)。
3. ピアを操作するために使用する[ピア管理者を登録します](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)。 別のピアをデプロイするために既に管理者を登録している場合は、このステップを実行する必要はありません。

また、{{site.data.keyword.cloud_notm}} Private にデプロイされた Fabric CA クライアントと TLS CA を使用して、いくつかのステップを実行する必要もあります。

1. Fabric CA クライアントを使用して、[ピア管理者の MSP フォルダーを生成します](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin)。
2. [TLS CA のエンドポイント情報を取得します](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-ca-endpoint)。
3. Fabric CA クライアントを使用して、[ピアを TLS CA に登録します](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-register-peer)。


### スターター・プランまたはエンタープライズ・プランの CA 情報
{: #ibp-peer-deploy-ibp-ca-endpoint}

まず、{{site.data.keyword.cloud_notm}} 上の CA の接続情報を構成ファイルに入力する必要があります。 スターター・プランまたはエンタープライズ・プラン上のネットワーク・モニター UI にログインします。 ネットワーク・モニターの**「概要」**画面で、**「リモート・ピアの構成」**ボタンをクリックします。 これにより、CA に関する必要な情報が含まれたポップアップが開きます。

![リモート・ピアの構成](../images/myresources_starter.png "リモート・ピアの構成")
*図 1. 「リモート・ピアの構成」パネル*

このポップアップ・ウィンドウには、次のフィールドがあります。

  - **組織 MSP (Organization MSP)**
  - **認証局 (CA) 名 (Certificate Authority (CA) Name)**
  - **認証局 (CA) URL (Certificate Authority (CA) URL)**
  - **認証局 (CA) TLS 証明書 (Certificate Authority (CA) TLS Certificate)**

ファイルの `"components"` セクションに、上記のフィールドから次の値を入力します。
- `"caname"` は**「認証局 (CA) 名」**の値です。
- `"cahost"` は CA URL のホスト名です。 例えば、CA URL が `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011` の場合は、`"cahost"` の値は `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com` となります。
- `"caport"` は `"cahost"` のポートです。 例えば、CA URL が `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011` の場合は、`"caport"` は `31011` となります。
- `"cacert"` は**「認証局 (CA) TLS 証明書」**フィールドの値です。 証明書をファイルに挿入する前に、次のコマンドを実行して、`<paste in Certificate Authority (CA) TLS Certificate>` というストリングをネットワーク・モニターからコピーした値に置換することで、証明書を base64 形式にエンコードする必要があります。

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
  ```
  {:codeblock}

  **注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。 以下のようになります。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```

  しかし、以下のようにはなりません。
  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

生成されるストリングをファイル内の `"catls"` の下の `"cacert"` フィールドに貼り付けます。 更新後、`"cacert"` フィールドは以下の例のようになります。

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### ピアの登録
{: #ibp-peer-deploy-register-peer}

ピアをチャネルに参加させて、チェーンコードをインストールおよびインスタンス化するには、まずピアを {{site.data.keyword.cloud_notm}} 上の CA に登録する必要があります。 登録すると、ピア・デプロイメントで、ピアがスターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークに参加するために必要な証明書を生成できるようになります。 以下のステップを実行して、`登録 ID` および`登録機密事項`を持つピアを登録します。 これら 2 つの値を構成ファイルに貼り付けます。

1. {{site.data.keyword.blockchainfull_notm}} Platform にログインして、ネットワーク・モニターにアクセスします。 「認証局」画面では、管理またはクライアント・アプリケーションなど、組織に登録されたすべての ID を確認できます。
![CA 画面](../images/CA_screen_starter.png "CA 画面")
  *図 2. CA 画面*

2. 画面の**「ユーザーの追加」**ボタンをクリックします。 ポップアップ画面が開き、以下のフィールドに入力した後に、ピアを登録できます。 「ID」および「機密事項」の値を保存します。ピアの構成時に、これらの値を使用する必要があります。
  - **登録 ID:** ピアのユーザー名であり、ピアの構成時には`登録 ID` と呼ばれます。 構成ファイル用に**この値を保存します**。
  - **機密事項の登録:** ピアのパスワードであり、ピアの構成時には`登録機密事項`と呼ばれます。 構成ファイル用に**この値を保存します**。
  - **タイプ:** このフィールドには `Peer` を選択します。
  - **所属 (Affiliation):** これは `org1` など、ピアが属する組織での所属です。 ドロップダウン・リストから既存の所属を選択するか、新しい所属を入力します。
  - **最大登録数 (Maximum Enrollments):** このフィールドを使用して、この ID を使用した証明書の登録または生成の回数を制限できます。 指定しない場合、値はデフォルトで無制限の登録に設定されます。

  フィールドに入力した後に、**「送信」**をクリックしてピアを登録します。 登録されたピアは、組織の ID としてテーブルに表示されます。 セキュリティー対策として、各 ID および付随する登録 ID と機密事項を使用してデプロイするピアは 1 つのみにします。 ピア ID とパスワードは再使用しないでください。

3. 構成ファイルの `"components"` セクションに、次の値を入力します。
  - `"enrollid"` は、前のステップの`登録 ID` の値です。
  - `"enrollsecret"` は、前のステップの`登録機密事項`の値です。


### 管理者の作成
{: #ibp-peer-deploy-register-admin}

ピア ID を登録したら、ピアを操作するために使用する管理者 ID も作成する必要があります。 まず、この新しい ID を CA に登録し、この ID を使用して MSP フォルダーを生成する必要があります。 次に、管理者ユーザーの署名付き証明書を構成ファイルに追加します。デプロイメント時には、この署名付き証明書がピアの管理証明書となります。 これにより、新規チャネルの開始やピアへのチェーンコードのインストールなどのブロックチェーン・ネットワークの操作を、管理者 ID の証明書を使用して実行できるようになります。

組織に属するコンポーネントに対して、管理者 ID は 1 つのみ作成する必要があります。 複数のピアをデプロイする場合でも、これらのステップは 1 回のみ実行する必要があります。 いずれかのピアに対して生成した署名付き証明書を使用して、組織に属する他の任意のピアをデプロイできます。

1. {{site.data.keyword.blockchainfull_notm}} Platform にログインして、ネットワーク・モニターにアクセスします。 「認証局」画面では、管理またはクライアント・アプリケーションなど、組織に登録されたすべての ID を確認できます。
![CA 画面](../images/CA_screen_starter.png "CA 画面")
  *図 2. CA 画面*

2. 画面の**「ユーザーの追加」**ボタンをクリックします。 ポップアップ画面が開き、以下のフィールドに入力した後に、ピアを登録できます。 「ID」および「機密事項」の値を保存します。ピアの構成時に、これらの値を使用する必要があります。
  - **登録 ID:** ピア管理者のユーザー名であり、ピアの構成時には`登録 ID` と呼ばれます。 管理者の MSP フォルダーを生成するときのために、**この値を保存してください**。
  - **機密事項の登録:** ピア管理者のパスワードであり、ピアの構成時には`登録機密事項`と呼ばれます。 管理者の MSP フォルダーを生成するときのために、**この値を保存してください**。
  - **タイプ:** このフィールドには `Peer` を選択します。
  - **所属 (Affiliation):** これは `org1` など、ピアが属する組織での所属です。 ドロップダウン・リストから既存の所属を選択するか、新しい所属を入力します。
  - **最大登録数 (Maximum Enrollments):** このフィールドを使用して、この ID を使用した証明書の登録または生成の回数を制限できます。 指定しない場合、値はデフォルトで無制限の登録に設定されます。

  これらのフィールドに入力したら、**「送信」**をクリックして管理者を作成します。 作成された管理者は、組織内の ID としてテーブルに表示されます。

### ピア管理者の MSP フォルダーの生成
{: #ibp-peer-deploy-enroll-admin}

管理者 ID を登録したら、ピア管理者の MSP フォルダーと署名付き証明書を生成する必要があります。 したがって、スターター・プランまたはエンタープライズ・プランの CA に対して登録コマンドを実行する必要があります。

1. [Fabric CA クライアント](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)をダウンロードします (まだダウンロードしていない場合)。
2. 暗号情報を保管するディレクトリーにナビゲートして、ピア管理者の MSP フォルダーを格納するフォルダーを作成します。

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. クライアントが証明書を作成できるパスを `$FABRIC_CA_CLIENT_HOME` として設定します。 以前の試行で作成された構成素材を必ず削除してください。 以前に `enroll` コマンドを実行したことがない場合は、`msp` フォルダーと `.yaml` ファイルは存在しません。

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. ネットワーク・モニターの「概説」パネルから**「構成プロファイル」**の JSON ファイルを開き、以下の変数を見つけます。
  - CA の URL: `certificateAuthorities` の下の `url`
  - CA 名: `caName`

5. 使用するサービス・プラン、ロケーション、およびクラスターに応じて {{site.data.keyword.cloud_notm}} から TLS 証明書をダウンロードします。 認証局 URL (例: `us01.blockchain.ibm.com:31011` や `us02.blockchain.ibm.com:31011`) のドメイン名に基づいてクラスターを見つけることができます。

  - スターター・プラン用の TLS 証明書
    - 米国: [us01.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
    [us03.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
    [us05.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
    [us07.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - 英国: [uk01.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
    [uk03.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - シドニー: [aus01.blockchain.ibm.com.cert ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert");
  - [エンタープライズ・プラン用の TLS 証明書 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

  後で実行するコマンドで参照できるディレクトリーに内容を保存します。

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. 次のコマンドを実行して、自身のピア管理者 ID を使用して証明書を生成します。

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  上記の `<enroll_id>` と `<enroll_password>` は、[ネットワーク・モニターを使用して登録された](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)ピア管理者の **ID** と**機密事項**です。 `<ca_name>` と `<ca_url_with_port>` は、接続プロファイル内の `caName` と `url` の値です。 CA URL の先頭にある `http://` は省略します。

  実際の呼び出しは以下のコマンド例のようになります。

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  このコマンドが正常に完了すると、`$FABRIC_CA_CLIENT_HOME` として指定したディレクトリー内に新しい MSP フォルダーが生成されます。 このディレクトリーには、ピアを操作するために使用する証明書が含まれています。

7. 作成したばかりの MSP フォルダーで、新規ユーザーの署名付き証明書ファイルを開き、このファイルを base64 形式に変換します。 署名付き証明書は、MSP ディレクトリーの `signcerts` フォルダー内にあります。 サンプル・ステップを使用している場合は、以下のコマンドを使用できます。

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。 以下のようになります。

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   以下のようにはなりません。

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  このコマンドは、以下の例のようなストリングを出力します。

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  このストリングを、構成ファイルの component セクションの下にある `"admincerts"` フィールドに入力します。

### TLS CA 情報
{: #ibp-peer-deploy-tls-ca-endpoint}

構成ファイル内の `"tls"` フィールドには、{{site.data.keyword.cloud_notm}} Private にデプロイした CA から得られる情報を入力する必要があります。 この CA を別個の TLS CA として使用します。これにより、ピアのセキュリティーが向上します。 以下の指示に従って、関連情報を生成してください。

- `"cahost"` および `"caport"` の値は、[CA URL](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) の URL とポートです。 例えば、CA URL が `http://9.30.94.174:30167` の場合は、`cahost` の値は `9.30.94.174` となり、`caport` は `30167` となります。
- `"caname"` は、{{site.data.keyword.cloud_notm}} Private にデプロイした CA の TLS CA 名です。 この TLS CA 名は、CA の構成時に`「CA TLS インスタンス名 (CA TLS instance name)」`フィールドに指定した値です。
- `"cacert"` は、CA の base64 でエンコードされた TLS 証明書です。 前提条件として [CA の TLS 証明書](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)を取得したときのコマンド出力の値によって、次のセクションを更新します。

  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  更新後、`"cacert"` フィールドは以下の例のようになります。

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### TLS CA へのピアの登録
{: #ibp-peer-deploy-tls-register-peer}

Fabric CA クライアントを使用して、{{site.data.keyword.cloud_notm}} Private 上の TLS CA にピアを登録する必要があります。

1. これで、TLS 証明書ファイル `tls.pem` が `$HOME/fabric-ca-client/catls` フォルダーに作成されました。 このファイルが作成されていない場合は、[{{site.data.keyword.cloud_notm}} Private からダウンロードした](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) TLS 証明書を、以下のコマンドで参照できるディレクトリーにコピーします。 `$HOME/fabric-ca-client` ディレクトリーにいることを確認してください。

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. TLS CA の管理者を使用して登録する必要があります。 `$FABRIC_CA_CLIENT_HOME` を、TLS CA 管理者証明書を保管するディレクトリーに変更します。

  ```
  cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. 以下のコマンドを実行して、TLS CA 管理者を使用して証明書を生成します。

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  このコマンドの `<enroll_id>` および `<enroll_password>` は、認証局のデプロイ時に Kubernetes シークレットに渡した [CA 管理者ユーザーの名前とパスワード](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret)です。 `<ca_url_with_port>` 内に [CA URL](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) を挿入します。 先頭の `http://` は省略します。 `<tls_ca_name>` は、[CA の構成](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms)時に指定した値です。

  `<ca_tls_cert_file>` は、[CA TLS 証明書](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)のファイル名と絶対パスです。

  実際の呼び出しは以下の例のようになります。

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  エンロールが完了すると、ピアを TLS CA に登録するために必要な証明書が得られます。

4. 以下のコマンドを実行して、所属団体名と組織名を確認します。

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  コマンドは次の例のようになります。

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  以下の例のような情報が表示されます。

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  自組織の **affiliation** の値をメモします。例えば、上記の例では `org1` という組織の値は `org1.department1` です。 この値を、以下のコマンドで使用する必要があります。

5. 以下のコマンドを実行して、ピアを登録します。

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  `--caname` フィールドで TLS CA 名を使用する必要があります。  ピアの名前とパスワードを作成し、それらを使用して `name` および `secret` を置換します。 **重要:** この情報はメモしてください。 `name` と `secret` を、構成ファイルの `"tls"` セクションにある `"enrollid"` と `"enrollsecret"` として入力する必要があります。

  コマンドの例は、以下のようになります。

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  このコマンドを実行した後、上記の値を使用してファイルを更新できます。

  ```
  "tls": {...
    },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
  ```

  一度に登録できる ID は 1 つだけです。 問題が発生した場合は、別のユーザー名とパスワードを使用してコマンドを試行してください。 セキュリティー対策として、各 ID および付随する登録 ID と機密事項を使用してデプロイするピアは 1 つのみにします。 ピア ID とパスワードは再使用しないでください。

  コマンドが正常に完了すると、以下の例のような情報が表示されます。

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

tree コマンドを実行して、構成ファイルを準備するために行った作業を確認できます。 証明書を格納したディレクトリーにナビゲートします。 tree コマンドを実行すると、以下の構造に似た結果が生成されます。

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CSR (証明書署名要求) ホスト
{: #ibp-peer-deploy-csr-hosts}

ピアをデプロイするための CSR ホスト名を指定する必要があります。 CSR ホスト名には、コンポーネントのデプロイ先となるクラスターのプロキシー IP アドレスと、Helm チャートのホスト名となる`サービス・ホスト名`が含まれます。

#### クラスター・プロキシー IP アドレスの値の取得
{: #ibp-peer-deploy-cluster-proxy-ip}

TLS CA をデプロイしたのと同じ {{site.data.keyword.cloud_notm}} Private クラスターにピアをデプロイする場合は、[TLS CA 向けに構成](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms)したときに使用したものと同じプロキシー IP を入力します。 コンポーネントを別のクラスターにデプロイする場合は、{{site.data.keyword.cloud_notm}} Private コンソールからクラスター・プロキシー IP アドレスの値を取得できます。 ピアのデプロイ先となる {{site.data.keyword.cloud_notm}} Private クラスターのクラスター管理者役割が必要です。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインします。 左側のナビゲーション・パネルで**「プラットフォーム」**、**「ノード」**の順にクリックし、クラスターで定義されているノードを表示します。
2. `proxy` 役割を持つノードをクリックし、テーブルから`「ホスト IP」`の値をコピーします。
3. 以下に示す構成ファイルの `"csr"` セクションで、`"hosts"` の値として`ホスト IP` を挿入します。

  ```
  "csr": {
    "hosts": [""]
			}
  ```

#### サービス・ホスト名の判別
{: #ibp-peer-deploy-determine-svc-host-name}

`サービス・ホスト名` は、デプロイメント時に指定する `Helm リリース名`となります。 クラスター IP プロキシー・アドレスが "9.42.134.44" であり、`Helm リリース名`が `org1peer1` である場合は、ファイルの `"csr"` セクションを次のように挿入します。

```
"csr": {
  "hosts": [
     "9.42.134.44",
      "org1peer1"
    ]
}
```
{:codeblock}

### 構成ファイルの完成
{: #ibp-peer-deploy-complete-config}

上記のすべてのステップを完了すると、更新された構成ファイルは以下の例のようになります。

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer1",
			"enrollsecret": "peer1pw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
      "org1peer1"
    ]
			}
		}
  }
}
```
{:codeblock}

このファイルへの入力が完了したら、このファイルを JSON 形式で保存して、Kubernetes 秘密としてピア・デプロイメントに渡す必要があります。 [次のセクション](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp)の手順に従って、この秘密を作成します。

## 構成の秘密の作成
{: #ibp-peer-deploy-config-file-ibp}

[Kubernetes 秘密 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/configuration/secret/ "秘密") を使用すると、情報をデプロイメントに直接渡すことなく保護および共有できます。 構成ファイルを保存したら、その構成ファイルを `base64` でエンコードする必要があります。

1. 構成ファイルを base64 形式でエンコードするには、端末ウィンドウから以下のコマンドを実行します。

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。 以下のようになります。

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   以下のようにはなりません。

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  結果の出力を下記のステップ 4 のために保存します。

2. {{site.data.keyword.cloud_notm}} Private コンソールにログインします。 左側のナビゲーション・パネルで、**「構成」**、**「秘密」**の順にクリックします。 **「秘密の作成」**ボタンをクリックし、新規秘密オブジェクトを生成できるポップ・パネルを開きます。

3. **「一般」**タブで、以下のフィールドに次のように入力します。
  - **名前:** 秘密にクラスター内で固有の名前を付けます。 この名前は、ピアのデプロイ時に使用します。 名前はすべて小文字でなければなりません。  
  **注:** ピアをデプロイすると、デプロイメントによって `<helm release name you intend to use>-secret` という名前で新規秘密が自動的に生成されます。 したがって、この秘密には、`<helm release name you intend to use>-secret` とは異なる名前を指定するようにしてください。 そうしないと、作成しようとする秘密が既に存在するため Helm チャートのデプロイメントが失敗します。
  - **名前空間:** 秘密を追加する名前空間。 ピアのデプロイ先となる `namespace` を選択します。
  - **タイプ:** 値`「不透明 (Opaque)」`を入力します。

4. このウィンドウの左側のナビゲーション・ペインにある**「データ」**タブをクリックします。 `「名前」`フィールドで、`secret.json` を指定し、値フィールドで構成ファイル内の `base64` でエンコードされたストリングを貼り付けます。

5. (オプション) `CouchDB` を状態データベースとして使用する予定の場合は、この秘密オブジェクトに 2 つの値を追加する必要があります。 **「データ」**タブで、**「データの追加」**ボタンをクリックして、コンテナーのデプロイ時にデータベース用に使用する CouchDB ユーザー ID とパスワードを追加します。
  1. 自身のユーザー名とパスワードを作成し、これらの値を base64 形式でエンコードします。 端末ウィンドウで以下のコマンドを実行して、`admin` と `adminpw` を希望の値に置き換えます。
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

   2. **「名前」**フィールドに、`couchdbusr` という値を入力します。 対応する**「値」**フィールドに、上記のステップ 1 で実行した `echo -n 'couch' | base64 $FLAG` の結果を入力します。
   3. **「データの追加」**ボタンをクリックして、2 番目のキーと値のペアを追加します。
   4. 2 つ目の**「名前」**フィールドに、`couchdbpwd` という値を入力します。 対応する**「値」**フィールドに、上記のステップ 1 で実行した `echo -n 'couchpw' | base64 $FLAG` の結果を入力します。

6. **「作成」**をクリックして秘密オブジェクトを保存します。

**注:** Helm リリースを削除しても、ピア構成の秘密は {{site.data.keyword.cloud_notm}} Private クラスターから削除されません。 ピアを削除する場合は、この秘密も削除する必要があります。

## 構成
{: #ibp-peer-deploy-peer-configuration}

ピア構成の秘密オブジェクトを作成したら、以下のステップを実行して {{site.data.keyword.cloud_notm}} Private 内でピアを構成およびインストールできます。 一度にインストールできるピアは 1 つだけです。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、右上隅の**「カタログ」**リンクをクリックします。
2. 左側のナビゲーション・パネルで `Blockchain` をクリックし、`ibm-blockchain-platform-prod` または `ibm-blockchain-platform-dev` (Community Edition をダウンロードした場合) というラベルのタイルを見つけます。 タイルをクリックして開くと、Helm チャートのインストールおよび構成に関する情報を含む Readme ファイルが表示されます。
3. パネル上部にある**「構成」**タブをクリックするか、右下隅にある**「構成」**ボタンをクリックします。
4. [一般構成パラメーター](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-configuration-parms)の値を指定し、ご使用条件に同意します。
5. `「すべてのパラメーター」`ツイスティーを開き、[グローバル構成パラメーター](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-global-parameters)の値を指定します。
6. **「ピア構成 (Peer configuration)」**セクションまでスクロールダウンします。 `「ピアのインストール (Install Peer)」`チェック・ボックスを選択し、ピアの[構成設定](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-parameters)を完了します。
7. **「インストール」**をクリックします。

### 構成パラメーター
{: #ibp-peer-deploy-peer-configuration-parms}

以下の表は、**ピア・コンポーネントに固有の** {{site.data.keyword.blockchainfull_notm}} Platform の構成可能パラメーターと、これらのパラメーターのデフォルト値を示しています。 ピアを試験的に使用する場合や、初めてピアを使用する場合は、データベースとストレージのパラメーターのデフォルト値を使用してください。 ピア・コンポーネント・セクションまでスクロールして、`「ピアのインストール (Install Peer)」`チェック・ボックスを選択して、そのコンポーネントのみに関連する構成情報を入力します。 **Helm チャート UI では追加の構成は不要と示されますが、ピアをデプロイするには特定のパラメーターを入力する必要があります。**

#### 一般およびグローバル構成パラメーター
{: #ibp-peer-deploy-global-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
|**一般パラメーター**| Helm チャートを構成するパラメーター | | |
| `Helm リリース名 (Helm release name)`| Helm リリースの名前。 小文字で始まり、任意の英数字で終わる必要があり、ハイフンと小文字の英数字のみを含む必要があります。 コンポーネントのインストールを試行するたびに固有の Helm リリース名を使用する必要があります。 **重要:** この値は、[JSON 秘密ファイル](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-csr-hosts)の「hosts」フィールドで「サービス・ホスト名」を生成する際に使用した値と一致する必要があります。 | なし | はい  |
| `ターゲット名前空間 (Target namespace)`| Helm チャートをインストールする Kubernetes 名前空間を選択します。 | なし | はい |
| `ターゲット名前空間のポリシー (Target namespace policies)`| 選択した名前空間のポッド・セキュリティー・ポリシーが表示されます。**`ibm-privileged-psp`** ポリシーが含まれているはずです。 そうでない場合は、その名前空間に [PodSecurityPolicy をバインド](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp)してください。 | なし | いいえ |
|**グローバル構成 (Global configuration)**| Helm チャート内のすべてのコンポーネントに適用されるパラメーター|||
| `サービス・アカウント名 (Service account name)`| ポッドの実行に使用する[サービス・アカウント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "ポッドのサービス・アカウントの構成") の名前を入力します。 | デフォルト | いいえ |

#### ピア構成パラメーター
{: #ibp-peer-deploy-peer-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
|**クラスター構成** |**クラスター構成情報** | ||
| `ピアのインストール (Install Peer)` | ピアをインストールするために選択します。|チェック・マークなし | はい (ピアをインストールする場合) |
| `ピア・ワーカー・ノード・アーキテクチャー (Peer worker node architecture)`| クラウド・プラットフォーム・アーキテクチャーを選択します (AMD64 または S390x)| AMD64 | はい |
| `ピア・イメージ・リポジトリー (Peer image repository)`| ピア Helm チャートの場所。 このフィールドにはインストール・パスが自動入力されます。 Community Edition を使用しており、インターネットにアクセスできない場合は、このフィールドでは、Fabric ピアのイメージをダウンロードしたディレクトリーを指定する必要があります。 | ibmcom/ibp-fabric-peer | はい |
| `ピア Docker イメージ・タグ (Peer Docker image tag)`|ピア・イメージに関連付けられたタグの値 |1.4.0 (正しい値が自動入力されます)|はい|
| `ピア構成 (Peer configuration)`|独自の `core.yaml` 構成ファイルをこのフィールドに貼り付けることで、ピアの構成をカスタマイズできます。 サンプルの `core.yaml` ファイルを表示するには、[`core.yaml` のサンプル構成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/core.yaml "hyperledger/fabric/core.yaml") を参照してください。**上級者専用**。 |なし|いいえ|
| `ピア構成の秘密 (Peer configuration secret) (必須)`| {{site.data.keyword.cloud_notm}} Private 内で作成した[ピア構成の秘密](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp)の名前。 | なし | はい |
|`組織 MSP (Organization MSP) (必須)`|この値は、「概要」画面の「リモート・ピアの構成」をクリックして、ネットワーク・モニター (スターター・プランおよびエンタープライズ・プラン UI) で確認できます。  |なし|はい|
|`ピア・サービス・タイプ (Peer service type)`| ピアで[外部ポートを公開 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types "公開サービス - サービス・タイプ") するかどうかを指定するために使用します。 ポートを外部に対して公開する場合は NodePort を選択し (推奨)、ポートを公開しない場合は ClusterIP を選択します。 LoadBalancer および ExternalName はこのリリースではサポートされていません。 | NodePort |はい|
| `状態データベース`| チャネル台帳の保管に使用される[状態データベース](/docs/services/blockchain/glossary.html#glossary-state-database)。 ピアは、[ブロックチェーン・ネットワーク](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)と同じデータベースを使用する必要があります。 | LevelDB | はい |
|`CouchDB イメージ・リポジトリー (CouchDB image repository)`| 台帳データベースとして CouchDB が選択されている場合にのみ適用されます。 このフィールドにはインストール・パスが自動入力されます。 Community Edition を使用しており、インターネットにアクセスできない場合は、このフィールドでは、Fabric CouchDB のイメージをダウンロードしたディレクトリーを指定する必要があります。| ibmcom/ibp-fabric-couchdb | はい |
| `CouchDB Docker イメージ・タグ (CouchDB Docker image tag)`| 台帳データベースとして CouchDB が選択されている場合にのみ適用されます。 CouchDB イメージに関連付けられたタグの値。 | 正しい値が自動入力されます。| はい |
| `ピア・データの永続性が有効 (Peer Data persistence enabled)`| クラスターが再始動または失敗した後にデータを維持する機能を有効にします。 詳しくは、[Kubernetes のストレージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/ "ボリューム") を参照してください。  *チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。* | オン | いいえ |
| `ピアで動的プロビジョニングを使用 (Peer use dynamic provisioning)`| ストレージ・ボリュームの動的プロビジョニングを有効にする場合にチェック・マークを付けます。 | オン | いいえ |
| `ピアの永続ボリューム・クレーム (Peer persistent volume claim)`| 新規クレーム専用。 作成される新しい Persistent Volume Claim (PVC) の名前を入力します。 | my-data-pvc | いいえ |
| `ピアのストレージ・クラス名 (Peer storage class name)`| ピアのストレージ・クラス名を指定します。 | 新しい PVC を作成する場合は空のままにします。それ以外の場合は、既存の PVC に関連付けられているストレージ・クラスを指定します。 | いいえ |
| `ピアの既存ボリューム・クレーム (Peer existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。 | なし | いいえ |
| `ピアのセレクター・ラベル (Peer selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。| なし | いいえ |
| `ピアのセレクター値 (Peer selector value)`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。 | なし | いいえ |
| `ピアのストレージ・アクセス・モード (Peer storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。  | ReadWriteMany| いいえ |
| `ピアのボリューム・クレーム・サイズ (Peer volume claim size)`| ボリューム・クレームのサイズ。2Gi を超える必要があります。 | 8Gi  | はい |
| `状態データベースの永続ボリューム・クレーム (State database persistent volume claim)`| 新規クレーム専用。 作成される新しい Persistent Volume Claim (PVC) の名前を入力します。 | statedb-pvc | いいえ |
| `状態データベースのストレージ・クラス名 (State database storage class name)`| 状態データベースのストレージ・クラス名を指定します。 | なし | いいえ |
| `状態データベースの既存のボリューム・クレーム (State database existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。 | なし | いいえ |
| `状態データベースのセレクター・ラベル (State database selector label)`| PVC の[セレクター・ラベル](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| なし | いいえ |
| `状態データベースのセレクター値 (State database selector value)`| PVC の[セレクター値](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) | なし | いいえ |
| `状態データベースのストレージ・アクセス・モード (State database storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。 | ReadWriteMany| いいえ |
| `状態データベースのボリューム・クレーム・サイズ (State database volume claim size)`| 使用するディスクのサイズを選択します。 | 8Gi | はい |
| `CouchDB - データの永続性が有効 (CouchDB - Data persistence enabled)`| CouchDB コンテナーの場合は、コンテナーの再始動時に台帳データが使用可能になります。 *チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。*| オン | いいえ |
| `CouchDB - 動的プロビジョニングを使用 (CouchDB - Use dynamic provisioning)`| CouchDB コンテナーの場合は、Kubernetes 動的ストレージを使用します。| オン | いいえ |
| `Docker-in-Docker CPU 要求 (Docker-in-Docker CPU request)`| チェーンコードを実行するコンテナーに割り振る CPU の最小数を指定します。 | 1 | はい |
| `Docker-in-Docker CPU 制限 (Docker-in-Docker CPU limit)`| チェーンコードを実行するコンテナーに割り振る CPU の最大数を指定します。 | 2 | はい |
| `Docker-in-Docker メモリー要求 (Docker-in-Docker memory request)`| チェーンコードを実行するコンテナーに割り振るメモリーの最小量を指定します。 | 1Gi | はい |
| `Docker-in-Docker メモリー制限 (Docker-in-Docker memory limit)`| チェーンコードを実行するコンテナーに割り振るメモリーの最大量を指定します。 | 4Gi | はい |
| `gRPC Web プロキシー CPU 要求 (gRPC web proxy CPU request)`| gRPC Web プロキシーに割り振る CPU の最小数 (millicpu (m) 単位) を指定します。 | 100m | はい |
| `gRPC Web プロキシー CPU 制限 (gRPC web proxy CPU limit)`| gRPC Web プロキシーに割り振る CPU の最大数 (millicpu (m) 単位) を指定します。 | 200m | はい |
| `gRPC Web プロキシー・メモリー要求 (gRPC web proxy memory request)`| gRPC Web プロキシーに割り振る最小メモリー量を指定します。 | 100Mi | はい |
| `gRPC Web プロキシー・メモリー制限 (gRPC web proxy memory limit)`| gRPC Web プロキシーに割り振る最大メモリー量を指定します。 | 200Mi | はい |
| `ピア CPU 要求 (Peer CPU request)` | ピアに割り当てる CPU の最小数。 | 1 | はい |
| `ピア CPU 制限 (Peer CPU limit)` | ピアに割り当てる CPU の最大数。| 2 | はい |
| `ピア・メモリー要求 (Peer Memory request)` | ピアに割り当てるメモリーの最小量。 | 1Gi | はい |
| `ピア・メモリー制限 (Peer Memory limit)` | ピアに割り当てるメモリーの最大量。 | 4Gi | はい |
| `CouchDB CPU 要求 (CouchDB CPU request)` | CouchDB に割り当てる CPU の最小数。| 1 | はい |
| `CouchDB CPU 制限 (CouchDB CPU limit)` | CouchDB に割り当てる CPU の最大数。 | 2 | はい |
| `CouchDB メモリー要求 (CouchDB Memory request)` | CouchDB に割り当てるメモリーの最小量。| 1Gi | はい |
| `CouchDB メモリー制限 (CouchDB Memory limit)` | CouchDB に割り当てるメモリーの最大量。 | 4Gi | はい |


ピア・データベースとして CouchDB が選択されている場合、ピア用と CouchDB 用にそれぞれ 1 つずつ、2 つのコンテナーがポッドに作成されます。 ピア・コンテナーには、ファイル・システムのブロックおよびトランザクションが保管されるピア PVC に対する単一のボリューム・マウントが含まれます。 CouchDB コンテナーは、台帳データを含むピア状態データベース PVC をマウントします。

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Helm コマンド・ラインを使用した Helm リリースのインストール
{: #ibp-peer-deploy-helm-cli}

また、Helm CLI を使用して Helm リリースをインストールすることもできます。 `helm install` コマンドを実行する前に、[Helm CLI 環境にクラスターの Helm リポジトリーを追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Helm CLI への内部 Helm リポジトリーの追加") してください。

インストールに必要なパラメーターを設定するには、`yaml` ファイルを作成し、以下の `helm install` コマンドにこれを渡します。

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

説明:
- `<helm_release name>`: Helm リリースに付与する名前を表します。
- `<helm_chart>`: カタログにインポートされる Helm チャートの名前を表します。
- `<helm_chart_version>`: カタログにインポートされる Helm チャートのバージョンを表します。
- `<customvalues.yaml>`: 構成パラメーターを含む yaml ファイルの名前です。

例えば、次のようにします。

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```
ダウンロードしたアーカイブ・ファイルに含まれる、必要なパラメーターとそのデフォルト設定をすべて含む `values.yaml` を編集することで、新規 `yaml` ファイルを作成することができます。

## ピアのインストールの確認
{: #ibp-peer-deploy-verify-installation-ibp}

構成パラメーターを入力し、**「インストール」**ボタンをクリックした後、**「Helm リリースの表示」**ボタンをクリックしてデプロイメントを表示します。 成功した場合は、「デプロイメント」テーブルの`「必要数」`、`「現行」`、`「最新」`、および`「使用可能」`の各フィールドに値 1 が表示されます。 最新表示をクリックしてテーブルが更新されるまで待つ必要がある場合があります。 「デプロイメント」テーブルは、{{site.data.keyword.cloud_notm}} Private コンソールの左上隅にある**「メニュー」**アイコンをクリックして確認することもできます。 メニュー・リストから、**「ワークロード」**、**「Helm リリース」**の順にクリックします。

`「メモ」`セクションまでスクロールダウンすると、[ピアを操作](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate)する際に使用する重要な情報があります。

## ピア・ログの表示
{: #ibp-peer-deploy-view-logs}

コンポーネント・ログは、[`kubectl CLI コマンド`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)を使用してコマンド・ラインから、または [Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Elastic Search への開かれた窓") ({{site.data.keyword.cloud_notm}} Private クラスターに含まれています) を使用して表示できます。 詳しくは、こちらの[ログへのアクセス手順](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-view-logs)を参照してください。

## 次の作業
{: #ibp-peer-deploy-whats-next}

ピアをデプロイした後に、いくつかの操作ステップを完了する必要があります。その後、ブロックチェーン・ネットワークにトランザクションを送信したり、ブロックチェーン・ネットワークから分散台帳を読み取ったりできるようになります。 詳しくは、[スターター・プランまたはエンタープライズ・プランを使用したピアの操作](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate)を参照してください。


## トラブルシューティング
{: #ibp-peer-deploy-troubleshooting}

### **問題:** `enroll` コマンド実行時の CA URL に関するエラー
{: #ibp-peer-deploy-ca-enroll-error}

登録 URL (`-u` パラメーターの値) に特殊文字が含まれている場合は、Fabric CA クライアントの enroll コマンドが正常に実行されないことがあります。 例えば、次のコマンドでは登録 ID とパスワードとして `admin:C25A06287!0` を指定しています。

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

このコマンドは正常に実行されず、以下のエラーが発生します。

```
!pw@9.12.19.115: event not found
```

### **解決策:**
{: #ibp-peer-deploy-ca-enroll-error-solution}

特殊文字をコード表現するか、URL を単一引用符で囲む必要があります。 例えば、`!` を `%21` にするか、コマンドを次のように入力します。

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

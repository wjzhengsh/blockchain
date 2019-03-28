---

copyright:
  years: 2018, 2019
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

# {{site.data.keyword.cloud_notm}} Private への認証局のデプロイ
{: #ca-deploy}

{{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private Helm チャートをインポートすると、個別のコンポーネントをデプロイできるようになります。 認証局 (CA) は組織のトラスト・ルートであり、ユーザーがデプロイする他のコンポーネントの資格情報を生成できるようにします。 そのため、他のコンポーネントをデプロイする前に、CA をデプロイする必要があります。 マルチクラウド・ブロックチェーン・ネットワーク内の各組織は、それぞれ独自の CA をデプロイする必要があります。  CA と、それらがブロックチェーン・ネットワークで果たす役割について詳しくは、[認証局](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca)を参照してください。
{:shortdesc}

認証局をデプロイする前に、[考慮事項と制限](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)を確認してください。

## 必要なリソース
{: #ca-deploy-resources-required}

ご使用の {{site.data.keyword.cloud_notm}} Private クラスターが最小ハードウェア・リソース要件を満たしていることを確認してください。

| コンポーネント | vCPU | RAM | データ・ストレージ用ディスク |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |

**注:**
- vCPU は仮想マシンに割り当てられる仮想コアであるか、サーバーが仮想マシン用にパーティション化されていない場合は物理プロセッサー・コアです。 {{site.data.keyword.cloud_notm}} Private でのデプロイメントの仮想プロセッサー・コア (VPC) を決定する場合は、vCPU 要件を考慮する必要があります。 VPC は IBM 製品のライセンス交付コストを決定する単位です。 VPC を決定するシナリオについて詳しくは、[仮想プロセッサー・コア (VPC) ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "仮想プロセッサー・コア") を参照してください。
- データ・ストレージ要件は、保管される ID と証明書の数によって異なります。 CA ストレージは、ピア・ストレージや順序付けプログラム・ストレージほど大きくはありませんが、ユース・ケースによって異なります。 ユーザー数が多いほど、必要なストレージも増えます。
- テストおよび試験目的には、これらの最小リソース・レベルで十分です。 トランザクション数が多い環境では、CA に十分な量のストレージを割り振ることが重要です。 使用するストレージの量は、ネットワークから要求されるトランザクションの数と署名の数によって異なります。 CA のストレージを使い果たした場合は、より大きなファイル・システムで新規 CA をデプロイし、同じチャネル上の他の CA を介してそれを同期させる必要があります。

## ストレージ
{: #ca-deploy-storage}

CA が使用するストレージを設定する必要があります。 デフォルト設定を使用すると、Helm チャートによって、CA 用に `fabric-ca-pvc` という名前の新しい 2 Gi 永続ボリューム・クレーム (PVC) が作成されます。

デフォルトのストレージ設定を使用しない場合は、{{site.data.keyword.cloud_notm}} Private のインストール時に*新しい* `storageClass` がセットアップされたことを確認します。セットアップされていない場合は、CA をデプロイする前に Kubernetes システム管理者が storageClass を作成する必要があります。

CA は、AMD64 プラットフォームまたは S390X プラットフォームのいずれかにデプロイできます。 ただし、[動的プロビジョニング ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) は {{site.data.keyword.cloud_notm}} Private の AMD64 ノードでのみ使用可能であることに注意してください。 クラスターに S390X と AMD64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。

動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) を作成し、Kubernetes PVC バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。

## CA をデプロイするための前提条件
{: #ca-deploy-prerequisites}

1. {{site.data.keyword.cloud_notm}} Private に CA をインストールするには、その前に[{{site.data.keyword.cloud_notm}} Private をインストール](/docs/services/blockchain/ICP_setup.html#icp-setup)し、[{{site.data.keyword.blockchainfull_notm}} Platform Helm チャートをインストール](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)する必要があります。

2. Community Edition を使用し、インターネット接続なしでこの Helm チャートを {{site.data.keyword.cloud_notm}} Private クラスター上で実行する場合は、インターネットに接続されたマシン上にアーカイブを作成した後に、アーカイブを {{site.data.keyword.cloud_notm}} Private クラスターにインストールすることができます。 詳しくは、[インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加"){:new_window} を参照してください。 仕様ファイル manifest.yaml は、Helm チャート内の ibm-blockchain-platform-dev/ibm_cloud_pak にあります。

3. {{site.data.keyword.cloud_notm}} Private コンソールからクラスター・プロキシー IP アドレスの値を取得します。 **注:** プロキシー IP にアクセスするには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。 {{site.data.keyword.cloud_notm}} Private クラスターにログインします。 左側のナビゲーション・パネルで**「プラットフォーム」**、**「ノード」**の順にクリックし、クラスターで定義されているノードを表示します。 `proxy` 役割を持つノードをクリックし、テーブルから`「ホスト IP」`の値をコピーします。

  この値を保存して、Helm チャートの`「プロキシー IP (Proxy IP)」`フィールドを構成する際に使用します。
  {:important}

4. CA 管理者ユーザー名とパスワードを作成し、{{site.data.keyword.cloud_notm}} Private の秘密オブジェクト内に格納します。 秘密の作成手順は、[次のセクション](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret)を参照してください。

## CA 管理者秘密の作成
{: #ca-deploy-admin-secret}

CA の初回始動時には、ユーザーが CA を操作するために作成した管理者 ID が含まれています。 CA をデプロイする前に、この管理者 ID のユーザー名とパスワードを作成する必要があります。 これらの値をメモすることが重要です。 これらは、後で CA を操作して追加のネットワーク・コンポーネントをデプロイするときに使用されます。 CA デプロイメント用の管理者の`ユーザー名`と`パスワード`を格納する [Kubernetes 秘密 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/configuration/secret/ "秘密") を作成します。 Kubernetes 秘密により、情報をデプロイメントに直接渡すことなく、情報を保護および共有できます。

1. 管理者ユーザー名とパスワードを作成し、値を base64 形式でエンコードします。 端末ウィンドウで以下のコマンドを実行します。`admin` と `adminpw` の値は、使用する値に置き換えます。
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **重要:** 上記で作成した `admin` と `adminpw` の値を保存してください。 また、これらのフィールドのエンコードされた値も保存してください。これらは、Kubernetes 秘密オブジェクトの作成時に必要です。

2. {{site.data.keyword.cloud_notm}} Private コンソールにログインします。 左側のナビゲーション・パネルで、**「構成」**、**「秘密」**の順にクリックします。 **「秘密の作成」**ボタンをクリックし、新規秘密オブジェクトを生成できるポップ・パネルを開きます。

3. **「一般」**タブで、以下のフィールドに次のように入力します。
  - **名前:** 秘密にクラスター内で固有の名前を付けます。 この名前は CA の構成で使用します。 名前はすべて小文字でなければなりません。
  - **名前空間**: 秘密を追加する名前空間。 CA をデプロイする`名前空間`を選択します。
  - **タイプ:** 値`「不透明 (Opaque)」`を入力します。

4. **注釈**タブはブランクのままにします。

5. **データ**タブに、キーと値のペアとしてユーザー名とパスワードを追加します。
  1. 最初の**「名前」**フィールドに、`ca-admin-name` と入力します。
  2. 最初の**「値」**フィールドに、上記のステップ 1 の `echo -n admin | base64` の結果を入力します。
  3. **「データの追加」**ボタンをクリックして、2 番目のキーと値のペアを追加します。
  4. 2 番目の**「名前」**フィールドに、`ca-admin-password` と入力します。
  5. 2 番目の**「値」**フィールドに、上記のステップ 1 の `echo -n adminpw | base64` の結果を入力します。  
    **図 1** は、`ca-admin-name` キーおよび `ca-admin-password` キーに指定した値に応じて、{{site.data.keyword.cloud_notm}} Private コンソール秘密に何が含まれるかを示しています。

    ![{{site.data.keyword.cloud_notm}} Private コンソール秘密](../images/CreateCASecret.png "{{site.data.keyword.cloud_notm}} Private コンソール秘密")  
    *図 1. {{site.data.keyword.cloud_notm}} Private コンソール秘密*
  6. **「作成」**をクリックして、新しい秘密オブジェクトを作成します。

CA Helm チャートでは、Helm リリースに秘密をデプロイするために、キー値として `ca-admin-name` および `ca-admin-password` という名前を使用する必要があります。

**注:** Helm リリースを削除しても、CA 管理者秘密は {{site.data.keyword.cloud_notm}} Private クラスターから削除されません。 {{site.data.keyword.cloud_notm}} Private クラスターの秘密はユーザーが管理する必要があります。 今後別の CA をデプロイする予定の場合は、CA 管理者の秘密を再利用できます。 再利用しない場合は、ユーザーが {{site.data.keyword.cloud_notm}} Private クラスターから秘密を削除する必要があります。

## 構成
{: #ca-deploy-configuration}

CA 管理者秘密を作成したら、以下のステップを使用して CA を構成およびインストールできます。 一度にインストールできる CA は 1 つのみです。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、右上隅の**「カタログ」**リンクをクリックします。
2. 左側のナビゲーション・パネルで`「ブロックチェーン」`をクリックし、`ibm-blockchain-platform-prod` または `ibm-blockchain-platform-dev` (Community Edition をダウンロードした場合) というラベルのタイルを見つけます。 タイルをクリックして開くと、Helm チャートのインストールおよび構成に関する情報を含む Readme ファイルが表示されます。
3. パネル上部にある**「構成」**タブをクリックするか、右下隅にある**「構成」**ボタンをクリックします。
4. [一般構成パラメーター](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters)の値を指定し、ご使用条件に同意します。
5. `「すべてのパラメーター」`ツイスティーを開き、[グローバル構成パラメーター](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters)の値を指定します。
6. **「CA 構成 (CA configuration)」**セクションまでスクロールダウンします。 `「CA のインストール (Install CA)」`チェック・ボックスを選択し、CA の[構成設定](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-parameters)を完了します。  
7. **「インストール」**をクリックします。

### 構成パラメーター
{: #ca-deploy-configuration-parms}

以下の表は、**CA コンポーネントに固有の** {{site.data.keyword.blockchainfull_notm}} Platform の構成可能パラメーターとそのデフォルト値を示しています。  

**Helm チャート UI ではこれ以上の構成は不要と示されますが、CA をデプロイするには特定のパラメーターを入力する必要があります。**

#### 一般およびグローバル構成パラメーター
{: #ca-deploy-global-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
| `Helm リリース名 (Helm release name)`| Helm リリースの名前。 小文字で始まり、任意の英数字で終わる必要があり、ハイフンと小文字の英数字のみを含む必要があります。 コンポーネントのインストールを試行するたびに固有の Helm リリース名を使用する必要があります。 | なし | はい |
| `ターゲット名前空間 (Target namespace)`| Helm チャートをインストールする Kubernetes 名前空間を選択します。 | なし | はい |
|**グローバル構成 (Global configuration)**| Helm チャート内のすべてのコンポーネントに適用されるパラメーター|||
| `サービス・アカウント名 (Service account name)`| ポッドの実行に使用する[サービス・アカウント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "ポッドのサービス・アカウントの構成") の名前を入力します。 | デフォルト | いいえ |

#### CA 構成パラメーター
{: #ca-deploy-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
| `CA のインストール (Install CA)`| CA をインストールする場合に選択します。 | チェック・マークなし | はい (CA をデプロイする場合) |
| `CA 名 (CA name)`| 認証局に使用する名前を指定します。 **重要:** この値はメモしてください。 これは、後で順序付けプログラムまたはピアを構成するときに必要です。 | SampleOrgCA | はい |
| `CA ワーカー・ノード・アーキテクチャー (CA worker node architecture)`| {{site.data.keyword.cloud_notm}} Private ワーカー・ノード・アーキテクチャーを選択します (ADM64 または S390X)。 | AMD64 | はい|
| `CA データベース・タイプ`| CA データを格納するデータベースのタイプ。 SQLite のみがサポートされます。 | SQLite | はい |
| `CA データの永続性が有効 (CA data persistence enabled)` | チェックすると、コンテナーの再始動時にデータが使用可能になります。 チェックしない場合は、フェイルオーバーやポッドの再始動時にすべてのデータが失われます。 | オン | いいえ |
| `CA で動的プロビジョニングを使用 (CA use dynamic provisioning)` | ストレージ・ボリュームの動的プロビジョニングを有効にする場合にチェック・マークを付けます。 | オン | いいえ |
| `CA ストレージ・クラス名 (CA storage class name)`| 固有のストレージ・クラス名を指定します。 指定しない場合は、クラスターのデフォルト・ストレージ・クラスが使用されます。 | なし | {{site.data.keyword.cloud_notm}} Private クラスターの構成方法によって異なります。 クラスター管理者に確認してください。 |
| `CA の既存ボリューム・クレーム (CA existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。 | なし | いいえ |
| `CA セレクター・ラベル (CA selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。 | なし | いいえ |
| `CA セレクター値`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。 | なし | いいえ |
| `CA ストレージ・アクセス・モード (CA storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。 | ReadWriteMany | はい |
| `CA ボリューム・クレーム・サイズ (CA volume claim size)`| 使用するディスクのサイズを選択します。 | 2Gi | はい |
| `CA イメージ・リポジトリー (CA image repository)`| CA Helm チャートの場所。 | ibmcom/ibp-fabric-ca | はい |
| `CA Docker イメージ・タグ (CA Docker image tag)`| CA イメージに関連付けられているタグの値。 このフィールドには、イメージ・バージョンが自動入力されます。 これを変更しないでください。| 1.2.1 | はい |
| `CA サービス・タイプ (CA service type)` | ピアで[外部ポートを公開 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) するかどうかを指定するために使用します。 ポートを外部に対して公開する場合は NodePort を選択し (推奨)、ポートを公開しない場合は ClusterIP を選択します。 LoadBalancer および ExternalName はこのリリースではサポートされていません。 | NodePort | はい |
| `CA 秘密 (CA secret) (必須)`| `ca-admin-name` および `ca-admin-password` で作成した Kubernetes 秘密オブジェクトの名前を入力します。 | なし | はい |
| `CA CPU 要求 (CA CPU request)`| CA に割り当てる CPU の最小数を指定します。 | 1 | はい |
| `CA CPU 制限 (CA CPU limit)`| CA に割り当てる CPU の最大数を指定します。 | 2 | はい |
| `CA メモリー要求 (CA memory request)`| CA に割り当てるメモリーの最小量を指定します。 | 1Gi | はい |
| `CA メモリー制限 (CA memory limit)`| CA に割り当てるメモリーの最大量を指定します。 | 4Gi | はい |
| `CA TLS インスタンス名 (CA TLS instance name)`| 順序付けプログラムまたはピアのエンロールに使用する CA TLS インスタンスの名前を指定します。 | tlsca | はい |
| `CSR 共通名 (CSR common name)`| 生成された CA ルート証明書が接続時に提示する共通名 (CN) を指定します。 | tlsca-common | はい |
| `プロキシー IP (Proxy IP)`| CA がデプロイされる[クラスターのプロキシー・ノード IP ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install_proxy.html "HTTP プロキシーの背後での IBM Cloud Private のインストール") を入力します。 | 127.0.0.1 | はい |


### Helm コマンド・ラインを使用した Helm リリースのインストール
{: #ca-deploy-helm-cli}

また、Helm CLI を使用して Helm リリースをインストールすることもできます。 `helm install` コマンドを実行する前に、[Helm CLI 環境にクラスターの Helm リポジトリーを追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Helm CLI への内部 Helm リポジトリーの追加") してください。

`yaml` ファイルを作成して、以下の `helm install` コマンドに渡すことで、インストールに必要なパラメーターを設定できます。

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

説明:

- `<helm_release name>`: Helm リリースに指定する名前を表します。
- `<helm_chart>`: カタログにインポートされる Helm チャートの名前を表します。
- `<helm_chart_version>`: カタログにインポートされる Helm チャートのバージョンを表します。
- `<customvalues.yaml>`: 構成パラメーターを含む yaml ファイルの名前です。

例えば、次のようにします。

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values ca-s390x-values.yaml \
--tls
```

ダウンロードしたアーカイブ・ファイルに含まれている `values.yaml` を編集して、新しい `yaml` ファイルを作成できます。 `values.yaml` ファイルには、必要なすべてのパラメーターとそのデフォルト設定が含まれています。

## CA インストールの検証
{: #verifiying-ca-installation}

構成パラメーターを入力し、**「インストール」**ボタンをクリックした後、**「Helm リリースの表示」**ボタンをクリックしてデプロイメントを表示します。 成功した場合は、「デプロイメント」テーブルの`「必要数」`、`「現行」`、`「最新」`、および`「使用可能」`の各フィールドに値 1 が表示されます。 最新表示をクリックしてテーブルが更新されるまで待つ必要がある場合があります。 「デプロイメント」テーブルは、{{site.data.keyword.cloud_notm}} Private コンソールの左上隅にある**「メニュー」**アイコンをクリックして確認することもできます。 メニュー・リストから、**「ワークロード」**、**「Helm リリース」**の順にクリックします。

`「メモ」`セクションまでスクロールダウンすると、[CA の操作](/docs/services/blockchain/howto/CA_operate.html#ca-operate)に使用する重要な情報を確認できます。

{{site.data.keyword.cloud_notm}} Private に {{site.data.keyword.blockchainfull_notm}} Platform CA をインストールすると、デフォルトの環境変数設定で configmap が作成されます。 その後、CA サーバーの環境変数を変更または追加して、その動作を構成できます。 CA サーバー構成パラメーターについて詳しくは、[Fabric CA サーバーの資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-server "Fabric CA サーバー") を参照してください。

configmap を構成したら、変更を有効にするために CA サーバーを再始動する必要があります。 CA サーバーを再始動するには、Fabric CA サーバーのポッドを削除します。 {{site.data.keyword.cloud_notm}} Private によって、変更を反映した新しいポッドが作成されます。

## CA ログの表示
{: #ca-deploy-view-logs}

コンポーネント・ログは、コマンド・ラインから [`kubectl CLI コマンド`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure)を使用して表示することも、[Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Your Window intothe Elastic Search") ({{site.data.keyword.cloud_notm}} Private クラスターに含まれています) を使用して表示することもできます。 詳しくは、こちらの[ログへのアクセス手順](/docs/services/blockchain/howto/CA_operate.html#ca-operate-view-logs)を参照してください。


## CA の操作
{: #ca-deploy-operate}

CA は、組織のトラスト・ルートになります。 CA を使用して、他のコンポーネントの証明書を生成する必要があります。  そのため、順序付けプログラムまたはピアをデプロイする前に、[CA をセットアップして、いくつかの操作手順を実行する](/docs/services/blockchain/howto/CA_operate.html#ca-operate)必要があります。

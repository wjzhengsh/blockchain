---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private での順序付けプログラムのデプロイ
{: #icp-orderer-deploy}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

順序付けプログラムは、順序付けプログラム・コンポーネントを含むブロックチェーン・ネットワーク内のクライアント、順序付けトランザクション、およびブロードキャスト・トランザクションを認証します。 順序付けプログラムと、ブロックチェーン・ネットワーク内でのその役割について詳しくは、[ブロックチェーン・コンポーネントの概要](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview)を参照してください。
{:shortdesc}

順序付けサービスをデプロイする前に、[考慮事項と制限事項](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)を確認してください。

## 必要なリソース
{: #icp-orderer-deploy-resources-required}

{{site.data.keyword.cloud_notm}} Private システムが最小ハードウェア・リソース要件を満たしていることを確認します。

| コンポーネント | vCPU | RAM | データ・ストレージ用ディスク |
|-----------|------|-----|-----------------------|
| 順序付けプログラム | 2 | 512 MB | 100 GB (拡張可能であること) |


 **注:**
 - vCPU は仮想マシンに割り当てられる仮想コアであるか、サーバーが仮想マシン用にパーティション化されていない場合は物理プロセッサー・コアです。 {{site.data.keyword.cloud_notm}} Private でのデプロイメントの仮想プロセッサー・コア (VPC) を決定する場合は、vCPU 要件を考慮する必要があります。 VPC は IBM 製品のライセンス交付コストを決定する単位です。 VPC を決定するシナリオについて詳しくは、[仮想プロセッサー・コア (VPC) ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html) を参照してください。
 - テストおよび試験目的には、これらの最小リソース・レベルで十分です。 大量のトランザクションがある環境の場合は、十分な量のストレージを割り振ることが重要です。例として、順序付けサービスの場合は 500 GB などになります。 使用するストレージの量は、ネットワークから要求されるトランザクションの数と署名の数によって異なります。 順序付けプログラム上のストレージをまもなく使い切る場合は、より大きいファイル・システムを持つ新規順序付けプログラムをデプロイし、同じチャネル上の他のコンポーネントと同期する必要があります。

## ストレージ
{: #icp-orderer-deploy-storage}

順序付けプログラムが使用するストレージを決定する必要があります。 デフォルト設定を使用すると、Helm チャートによって、順序付けプログラム・データ用に `orderer-data` という名前の新しい 8 Gi 永続ボリューム・クレーム (PVC) が作成されます。

デフォルトのストレージ設定を使用しない場合は、{{site.data.keyword.cloud_notm}} Private のインストール時に*新しい* `storageClass` がセットアップされたことを確認します。セットアップされていない場合は、デプロイの前に Kubernetes システム管理者が storageClass を作成する必要があります。

順序付けプログラムは、amd64 または s390x のいずれかのプラットフォームを選択してデプロイできます。 但し、[動的プロビジョニング ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "動的ボリューム・プロビジョニング") は {{site.data.keyword.cloud_notm}} Private の AMD64 ノードでのみ使用可能であることに注意してください。 クラスターに s390x と amd64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。

動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "永続ボリューム") を作成し、Kubernetes PVC バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。


## 順序付けプログラムをデプロイするための前提条件
{: #icp-orderer-deploy-prerequisites}

1. {{site.data.keyword.cloud_notm}} Private に順序付けプログラムをインストールするには、その前に[{{site.data.keyword.cloud_notm}} Private をインストール](/docs/services/blockchain/ICP_setup.html#icp-setup)し、[{{site.data.keyword.blockchainfull_notm}} Platform Helm チャートをインストール](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)する必要があります。

2. Community Edition を使用し、インターネット接続なしでこの Helm チャートを {{site.data.keyword.cloud_notm}} Private クラスター上で実行する場合は、インターネットに接続されたマシン上にアーカイブを作成した後に、アーカイブを {{site.data.keyword.cloud_notm}} Private クラスターにインストールすることができます。 詳しくは、[インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加"){:new_window} を参照してください。 仕様ファイル `manifest.yaml` は、Helm チャート内の ibm-blockchain-platform-dev/ibm_cloud_pak にあります。

3. {{site.data.keyword.cloud_notm}} Private コンソールから、CA のクラスター・プロキシー IP アドレスの値を取得します。 **注:** プロキシー IP にアクセスするには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。 {{site.data.keyword.cloud_notm}} Private クラスターにログインします。 左側のナビゲーション・パネルで**「プラットフォーム」**、**「ノード」**の順にクリックし、クラスターで定義されているノードを表示します。 `proxy` 役割を持つノードをクリックし、テーブルから`「ホスト IP」`の値をコピーします。 **重要:** この値を保存して、Helm チャートの`「プロキシー IP (Proxy IP)」`フィールドを構成する際に使用します。

4. [順序付けプログラムの構成ファイルを作成し、{{site.data.keyword.cloud_notm}} Private に Kubernetes 秘密として格納](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file)します。

## 順序付けプログラムの構成ファイルの作成
{: #icp-orderer-deploy-config-file}

順序付けプログラムをデプロイする前に、順序付けプログラムの ID および CA に関する重要な情報を含む構成ファイルを作成する必要があります。 次に、[Kubernetes Secret ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/configuration/secret/) オブジェクトを使用して、構成時にこのファイルを Helm チャートに渡す必要があります。 このファイルにより、順序付けプログラムは、ブロックチェーン・ネットワークに参加するために必要な証明書を CA から取得できるようになります。 これには、順序付けプログラムを管理ユーザーとして操作できるようにする管理証明書も含まれます。 順序付けプログラムを構成する前に、[CA を使用した順序付けプログラムまたはピアのデプロイ](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)の手順に従ってください。

構成ファイルに CSR ホスト名を指定する必要があります。 これには、デプロイメント時に指定した `Helm リリース名` に基づく`サービス・ホスト名`が含まれます。 `サービス・ホスト名`は、末尾に `-orderer` を追加したストリングを使用して指定する `Helm リリース名`です。 例えば、`orderer1` の `Helm リリース名`を指定する場合、ファイルの `"csr"` セクションに以下の値を挿入できます。

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

構成ファイルを保存した後、そのファイルを秘密オブジェクトとして {{site.data.keyword.cloud_notm}} Private に提供する前に、これを base64 フォーマットでエンコードする必要があります。 Kubernetes 秘密により、情報をデプロイメントに直接渡すことなく、情報を保護および共有できます。

1. 端末ウィンドウから以下のコマンドを実行し、構成ファイルを base64 フォーマットでエンコードします。

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
  - **名前:** 秘密にクラスター内で固有の名前を付けます。 この名前は、順序付けプログラムをデプロイするときに使用します。 名前はすべて小文字でなければなりません。  
  **注:** 順序付けプログラムをデプロイする際、デプロイメントによって `<helm_release_name>-orderer-mspsecret` という名前で自動的に新規秘密が生成されます。 したがって、秘密には、`<helm_release_name>-orderer-mspsecret` とは異なる名前を指定するようにしてください。 そうしないと、作成しようとする秘密が既に存在するため Helm チャートのデプロイメントが失敗します。
  - **名前空間:** 秘密を追加する名前空間。 順序付けプログラムのデプロイ先となる `namespace` を選択してください。
  - **タイプ:** 値`「不透明 (Opaque)」`を入力します。

4. このウィンドウの左側のナビゲーション・ペインにある**「データ」**タブをクリックします。 `「名前」`フィールドで、`secret.json` を指定し、値フィールドで構成ファイル内の `base64` でエンコードされたストリングを貼り付けます。

5. **「作成」**をクリックして秘密オブジェクトを保存します。

**注:** 順序付けプログラム構成の秘密は、Helm リリースを削除しても {{site.data.keyword.cloud_notm}} Private クラスターから除去されません。 順序付けプログラムを削除する場合は、この秘密も削除する必要があります。

## 構成
{: #icp-orderer-deploy-configuration}


順序付けプログラム構成の秘密オブジェクトを作成したら、以下のステップを実行して {{site.data.keyword.cloud_notm}} Private で順序付けプログラムを構成およびインストールできます。 一度にインストールできる順序付けプログラムは 1 つのみです。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、右上隅の**「カタログ」**リンクをクリックします。
2. 左側のナビゲーション・パネルで`「ブロックチェーン」`をクリックし、`ibm-blockchain-platform-prod` または `ibm-blockchain-platform-dev` (Community Edition をダウンロードした場合) というラベルのタイルを見つけます。 タイルをクリックして開くと、Helm チャートのインストールおよび構成に関する情報を含む Readme ファイルが表示されます。
3. パネル上部にある**「構成」**タブをクリックするか、右下隅にある**「構成」**ボタンをクリックします。
4. [一般構成パラメーター](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-configuration-parms)の値を指定し、ご使用条件に同意します。
5. `「すべてのパラメーター」`ツイスティーを開き、[グローバル構成パラメーター](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-global-parameters)の値を指定します。
6. **「順序付けプログラム構成 (Orderer configuration)」**セクションまでスクロールダウンします。 `「順序付けプログラムのインストール (Install Orderer)」`チェック・ボックスを選択し、順序付けプログラムの[構成設定](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-parameters)を入力します。
7. **「インストール」**をクリックします。


**注:** 順序付けプログラムを s390x にデプロイする際には、コンテナーの初期化により以下のエラーが返されることがあります。

`panic: DB を開く際にエラーが発生しました。使用可能なロックがありません (panic: Error while trying to open DB: no locks available)`

これは、順序付けプログラムがフラット・ファイル DB を使用しており、順序付けサービスがファイルを照会して排他ロックがあるかどうかチェックしたり、排他ロックを作成したりできるようにするには、NFS ファイル・システムに追加のパッケージが必要であるためです。 この問題を修正するには、`rpc-statd` パッケージを有効にする必要があります。

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

これは、NFS ファイル・システムの提供元のシステムで行う必要があります。

### 構成パラメーター
{: #icp-orderer-configuration-parms}

以下の表に、**順序付けプログラム・コンポーネントに固有の** {{site.data.keyword.blockchainfull_notm}} Platform の構成可能パラメーターのリストとそのデフォルト値を示します。 **Helm チャート UI では追加の構成が不要であると示されますが、順序付けプログラムをデプロイするには特定のパラメーターを入力する必要があります。**

#### 一般およびグローバル構成パラメーター
{: #icp-orderer-deploy-global-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
| `Helm リリース名 (Helm release name)`| リリースの名前。 小文字で始まり、任意の英数字で終わる必要があり、ハイフンと小文字の英数字のみを含む必要があります。 コンポーネントのインストールを試行するたびに固有の Helm リリース名を使用する必要があります。 **重要:** この値は、[JSON 秘密ファイル](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file)の「hosts」フィールドで「サービス・ホスト名」を生成する際に使用した値と一致する必要があります。 | なし | はい  |
| `ターゲット名前空間 (Target namespace)`| Helm チャートをインストールする Kubernetes 名前空間を選択します。 | なし | はい |
|**グローバル構成 (Global configuration)**| Helm チャート内のすべてのコンポーネントに適用されるパラメーター|||
| `サービス・アカウント名 (Service account name)`| ポッドの実行に使用する[サービス・アカウント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) の名前を入力します。 | デフォルト | いいえ |

#### 順序付けプログラムの構成パラメーター
{: #icp-orderer-deploy-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
| `順序付けプログラムのインストール (Install Orderer)`| 順序付けプログラムをインストールする場合に選択します。 | チェック・マークなし | はい (順序付けプログラムをデプロイする場合) |
| `順序付けプログラム・ワーカー・ノード・アーキテクチャー (Orderer worker node architecture)`| {{site.data.keyword.cloud_notm}} Private ワーカー・ノード・アーキテクチャーを選択します (AMD64 または S390X)。 | マスター・ノードに基づく自動検出されたアーキテクチャー | はい |
| `順序付けプログラムの構成 (Orderer configuration)`| 独自の `orderer.yaml` 構成ファイルをこのフィールドに貼り付けることで、順序付けプログラムの構成をカスタマイズできます。 サンプル `orderer.yaml` ファイルについては、[`orderer.yaml` サンプル構成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/orderer.yaml) を参照してください。**上級者専用**。 | なし | いいえ |
| `組織 MSP 秘密 (必須) (Organization MSP secret (Required))`| 組織 MSP の証明書と鍵が含まれる秘密オブジェクトの名前を指定します。 | なし | はい |
| `順序付けプログラム・データの永続性が有効 (Orderer data persistence enabled)` | コンテナーの再始動時にデータが利用可能です。 チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。 | オン | いいえ |
| `順序付けプログラムで動的プロビジョニングを使用 (Orderer use dynamic provisioning)` | ストレージ・ボリュームの動的プロビジョニングを有効にする場合にチェック・マークを付けます。 | オン | いいえ |
| `順序付けプログラム・イメージ・リポジトリー (Orderer image repository)` | 順序付けプログラムの Helm チャートの場所。 このフィールドにはインストール・パスが自動入力されます。 Community Edition を使用しており、インターネット・アクセスがない場合は、このフィールドを、Fabric 順序付けプログラムのイメージをダウンロードした場所に変更します。 | ibmcom/ibp-fabric-orderer | いいえ |
| `順序付けプログラム Docker イメージ・タグ (Orderer Docker image tag)`| Docker イメージのレコード。 このフィールドには、イメージ・バージョンが自動入力されます。 これを変更しないでください。| 1.2.1 | はい |
| `順序付けプログラム・コンセンサス・タイプ (Orderer consensus type)`| 順序付けサービスのコンセンサス・タイプ。 | SOLO | はい |
| `順序付けプログラム組織名 (Orderer organization name)`| 順序付けプログラム組織に使用する名前を指定します。 ピアもデプロイする予定の場合は、ピアに指定するものとは異なる名前を使用するようにしてください。 例えば、順序付けプログラム組織に、`ordererOrg` のような名前を指定します。 | なし | はい |
| `順序付けプログラム組織 MSP ID (Orderer Org MSP ID)`| 順序付けプログラム組織の MSP ID に使用する名前を指定します。 これは順序付けプログラム組織に付けたものと同じ名前である必要があり、デプロイメント・プロセスにより環境変数として設定されます。 この値をメモしておいてください。後で参照する必要があります。 | なし | はい |
| `順序付けプログラム・ストレージ・クラス名 (Orderer storage class name)`| 順序付けプログラムのストレージ・クラス名を指定します。 | なし | {{site.data.keyword.cloud_notm}} Private クラスターの構成方法によって異なります。 クラスター管理者に確認してください。 |
| `順序付けプログラム既存ボリューム・クレーム (Orderer existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。 | なし | いいえ |
| `順序付けプログラム・セレクター・ラベル (Orderer selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)。 | なし | いいえ |
| `順序付けプログラム・セレクター値 (Orderer selector value)`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)。 | なし | いいえ |
| `順序付けプログラム・ストレージ・アクセス・モード (Orderer storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) を指定します。 | ReadWriteMany | はい |
| `順序付けプログラム・ボリューム・クレーム・サイズ (Orderer volume claim size)`| 使用するディスクのサイズを選択します。これは 2 Gi 以上である必要があります。 | 8 Gi | はい |
| 順序付けプログラム・サービス・タイプ (Orderer service type)` | ピアで[外部ポートを公開 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) するかどうかを指定するために使用します。 ポートを外部に対して公開する場合は NodePort を選択し (推奨)、ポートを公開しない場合は ClusterIP を選択します。 LoadBalancer および ExternalName はこのリリースではサポートされていません。 | NodePort | はい |
| `順序付けプログラム CPU 要求 (Orderer CPU request)`| 順序付けプログラムに割り振る最小 CPU 数を指定します。 | 1 | はい |
| `順序付けプログラム CPU 制限 (Orderer CPU limit)`| 順序付けプログラムに割り振る最大 CPU 数を指定します。 | 2 | はい |
| `順序付けプログラム・メモリー要求 (Orderer memory request)`| 順序付けプログラムに割り振る最小メモリー量を指定します。 | 1Gi | はい |
| `順序付けプログラム・メモリー制限 (Orderer memory limit)`| 順序付けプログラムに割り振る最大メモリー量を指定します。 | 2Gi | はい |

### Helm コマンド・ラインを使用した Helm リリースのインストール
{: #icp-orderer-deploy-helm-cli}

また、Helm CLI を使用して Helm リリースをインストールすることもできます。 `helm install` コマンドを実行する前に、[Helm CLI 環境にクラスターの Helm リポジトリーを追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Helm CLI への内部 Helm リポジトリーの追加") してください。

インストールに必要なパラメーターを設定するには、`yaml` ファイルを作成し、以下の `helm install` コマンドにこれを渡します。

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
--values orderer-s390x-values.yaml \
--tls
```

ダウンロードしたアーカイブ・ファイルに含まれる、必要なパラメーターとそのデフォルト設定をすべて含む `values.yaml` を編集することで、新規 `yaml` ファイルを作成することができます。

## 順序付けプログラムのインストールの検証
{: #icp-orderer-deploy-verify-install}

構成パラメーターを入力し、**「インストール」**ボタンをクリックした後、**「Helm リリースの表示」**ボタンをクリックしてデプロイメントを表示します。 成功した場合は、「デプロイメント」テーブルの`「必要数」`、`「現行」`、`「最新」`、および`「使用可能」`の各フィールドに値 1 が表示されます。 最新表示をクリックしてテーブルが更新されるまで待つ必要がある場合があります。 「デプロイメント」テーブルは、{{site.data.keyword.cloud_notm}} Private コンソールの左上隅にある**「メニュー」**アイコンをクリックして確認することもできます。 メニュー・リストから、**「ワークロード」**、**「Helm リリース」**の順にクリックします。

## 順序付けプログラムのログの表示
{: #icp-orderer-deploy-view-logs}

コンポーネント・ログは、コマンド・ラインから [`kubectl CLI コマンド`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)を使用して表示することも、[Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Your Window intothe Elastic Stack") ({{site.data.keyword.cloud_notm}} Private クラスターに含まれています) を使用して表示することもできます。詳しくは、こちらの[ログへのアクセス手順](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-view-logs)を参照してください。

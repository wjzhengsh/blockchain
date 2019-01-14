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

# {{site.data.keyword.cloud_notm}} Private でのピアのデプロイ
{: #peer-icp}


***[このページは参考になりましたか。ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

以下の手順では、{{site.data.keyword.cloud_notm}} Private (ICP) に {{site.data.keyword.blockchainfull}} Platform ピアをデプロイする方法を説明します。これらの手順に従って、ICP 上の {{site.data.keyword.blockchainfull_notm}} Platform に接続できます。{{site.data.keyword.cloud_notm}} 上のスターター・プラン・ネットワークまたはエンタープライズ・プラン・ネットワークにピアを接続するには、[スターター・プランまたはエンタープライズ・プランに接続するためのピアのデプロイ](peer_deploy_ibp.html)を参照してください。
{:shortdesc}

ピアをデプロイする前に、[考慮事項と制限](../ibp-for-icp-about.html#ibp-icp-considerations)を確認してください。

## 必要なリソース
{: #peer-resources-required}

ICP システムが最小ハードウェア・リソース要件を満たしていることを確認します。

| コンポーネント | vCPU | RAM | データ・ストレージ用ディスク |
|-----------|------|-----|-----------------------|
| ピア | 2 | 2 GB | 50 GB (拡張可能であること) |
| ピア用 CouchDB | 2| 2 GB | 50 GB (拡張可能であること) |

 **注:**
 - vCPU は仮想マシンに割り当てられる仮想コアであるか、サーバーが仮想マシン用にパーティション化されていない場合は物理プロセッサー・コアです。ICP でのデプロイメントの仮想プロセッサー・コア (VPC) を決定する場合は、vCPU 要件を考慮する必要があります。VPC は IBM 製品のライセンス交付コストを決定する単位です。VPC を決定するシナリオについて詳しくは、[仮想プロセッサー・コア (VPC) ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html) を参照してください。
 - テストおよび試験目的には、これらの最小リソース・レベルで十分です。大量のトランザクションがある環境の場合は、十分な量のストレージを割り振ることが重要です。例えば、ピア用に 250 GB を割り振ります。使用するストレージの量は、ネットワークから要求されるトランザクションの数と署名の数によって異なります。ピア上または順序付けプログラム上のストレージをまもなく使い切る場合は、より大きいファイル・システムを持つ新しいピアまたは順序付けプログラムをデプロイして、同じチャネル上の他のコンポーネントと同期する必要があります。

## ストレージ
{: #storage}

ピアが使用するストレージを設定する必要があります。デフォルト設定を使用する場合、Helm チャートによって、ピア・データ用に `my-data-pvc` という名前の新規 8 Gi Persistent Volume Claim (PVC) と、状態データベース用に `statedb-pvc` という名前の別の 8 Gi PVC が作成されます。

デフォルトのストレージ設定を使用しない場合は、ICP のインストール時に*新しい* `storageClass` がセットアップされたことを確認します。セットアップされていない場合は、デプロイの前に Kubernetes システム管理者が storageClass を作成する必要があります。

ピアのデプロイ先として、amd64 または s390x のプラットフォームを選択できます。ただし、[動的プロビジョニング](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)は、ICP では amd64 ノードでのみ使用可能であることに注意してください。クラスターに s390x と amd64 ワーカー・ノードが混在している場合は、動的プロビジョニングを使用できません。

動的プロビジョニングを使用しない場合は、[永続ボリューム ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) を作成し、Kubernetes PVC バインド処理を改善するために使用できるラベルを使用してセットアップする必要があります。

## ピアをデプロイするための前提条件
{: #prerequisites-peer-icp}

1. ICP にピアをインストールするには、その前に [ICP をインストール](../ICP_setup.html)し、[{{site.data.keyword.blockchainfull_notm}} Platform Helm チャートをインストール](helm_install_icp.html)する必要があります。

2. Community Edition を使用しており、インターネットに接続されていない ICP クラスター上でこの Helm チャートを実行する場合は、アーカイブを ICP クラスターにインストールするためには、インターネットに接続されたマシン上でそれらのアーカイブを事前に作成する必要があります。詳しくは、[インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "インターネット接続がないクラスターへのフィーチャー・アプリケーションの追加"){:new_window} を参照してください。仕様ファイル `manifest.yaml` は、Helm チャート内の `ibm-blockchain-platform-dev/ibm_cloud_pak` にあります。

3. まず ICP に [CA をデプロイ](CA_deploy_icp.html)する必要があります。この CA を使用して[ピア構成ファイルを作成して、このファイルを Kubernetes 秘密として ICP に保管](#peer-config-file)する必要があります。

4. ICP コンソールから、CA のクラスター・プロキシー IP アドレスの値を取得します。**注:** プロキシー IP にアクセスするには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。ICP クラスターにログインします。左側のナビゲーション・パネルで**「プラットフォーム」**、**「ノード」**の順にクリックし、クラスターで定義されているノードを表示します。`proxy` 役割を持つノードをクリックし、テーブルから`「ホスト IP」`の値をコピーします。**重要:** この値を保存して、Helm チャートの`「プロキシー IP (Proxy IP)」`フィールドを構成する際に使用します。


## ピア構成の秘密の作成
{: #peer-config-file}

ピアをデプロイするには、ピア ID と認証局に関する重要な情報が含まれた構成ファイルを作成する必要があります。次に、[Kubernetes Secret ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/configuration/secret/) オブジェクトを使用して、構成時にこのファイルを Helm チャートに渡す必要があります。このファイルにより、ピアは、ブロックチェーン・ネットワークに参加するために必要な証明書を認証局から取得できるようになります。このファイルには、ピアの操作を可能にする管理者証明書も含まれています。ピアを構成する前に、[CA を使用した順序付けプログラムまたはピアのデプロイ](CA_operate.html#deploy-orderer-peer)の手順に従ってください。

構成ファイルに CSR ホスト名を指定する必要があります。これには、デプロイメント時に指定する `Helm リリース名` と同じ値になる`サービス・ホスト名`が含まれます。例えば、`org1peer1` という `Helm リリース名`を指定する場合、ファイルの `"csr"` セクションに以下の値を挿入してください。
```
"csr": {
  "hosts": [
     "9.42.134.44",
      "org1peer1"
    ]
}
```
{:codeblock}

構成ファイルを保存した後、そのファイルを秘密オブジェクトとして ICP に渡す前に、そのファイルを base64 形式でエンコードする必要があります。Kubernetes 秘密により、情報をデプロイメントに直接渡すことなく、情報を保護および共有できます。

1. 構成ファイルを base64 形式でエンコードするには、端末ウィンドウから以下のコマンドを実行します。
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
```
{:codeblock}

**注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。以下のようになります。

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

結果の出力を下記のステップ 4 のために保存します。

2. ICP コンソールにログインします。左側のナビゲーション・パネルで、**「構成」**、**「秘密」**の順にクリックします。**「秘密の作成」**ボタンをクリックし、新規秘密オブジェクトを生成できるポップ・パネルを開きます。

3. **「一般」**タブで、以下のフィールドに次のように入力します。
  - **名前:** 秘密にクラスター内で固有の名前を付けます。この名前は、ピアをデプロイするときに使用します。名前はすべて小文字でなければなりません。  
  **注:** ピアをデプロイすると、デプロイメントによって `<helm_release_name>-secret` という名前で新規秘密が自動的に生成されます。したがって、秘密には、`<helm_release_name>-secret` とは異なる名前を指定するようにしてください。そうしないと、作成しようとする秘密が既に存在するため Helm チャートのデプロイメントが失敗します。
  - **名前空間:** 秘密を追加する名前空間。ピアのデプロイ先となる `namespace` を選択します。
  - **タイプ:** 値`「不透明 (Opaque)」`を入力します。

4. このウィンドウの左側のナビゲーション・ペインにある**「データ」**タブをクリックします。
  - `「名前」`フィールドで、`secret.json` を指定し、値フィールドで構成ファイル内の `base64` でエンコードされたストリングを貼り付けます。

5. (オプション) `CouchDB` を状態データベースとして使用する予定の場合は、この秘密オブジェクトに 2 つの値を追加する必要があります。**「データ」**タブで、**「データの追加」**ボタンをクリックして、コンテナーのデプロイ時にデータベース用に使用する CouchDB ユーザー ID とパスワードを追加します。
  1. 自身のユーザー名とパスワードを作成し、これらの値を base64 形式でエンコードします。端末ウィンドウで以下のコマンドを実行して、`admin` と `adminpw` を希望の値に置き換えます。
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. **「名前」**フィールドに、`couchdbuser` という値を入力します。対応する**「値」**フィールドに、上記のステップ 1 で実行した `echo -n 'couch' | base64 $FLAG` の結果を入力します。
  3. **「データの追加」**ボタンをクリックして、2 番目のキーと値のペアを追加します。
  4. 2 つ目の**「名前」**フィールドに、`couchdbpwd` という値を入力します。対応する**「値」**フィールドに、上記のステップ 1 で実行した `echo -n 'couchpw' | base64 $FLAG` の結果を入力します。

6. **「作成」**をクリックして秘密オブジェクトを保存します。

**注:** Helm リリースを削除しても、ピア構成の秘密は ICP クラスターから削除されません。ピアを削除する場合は、この秘密も削除する必要があります。

## 構成
{: #peer-configuration}

ピア構成の秘密オブジェクトを作成したら、以下のステップを実行して ICP 内でピアを構成およびインストールできます。一度にインストールできるピアは 1 つだけです。


1. ICP コンソールにログインし、右上隅の**「カタログ」**リンクをクリックします。
2. 左側のナビゲーション・パネルで`「ブロックチェーン」`をクリックし、`ibm-blockchain-platform-prod` または `ibm-blockchain-platform-dev` (Community Edition をダウンロードした場合) というラベルのタイルを見つけます。タイルをクリックして開くと、Helm チャートのインストールおよび構成に関する情報を含む Readme ファイルが表示されます。
3. パネル上部にある**「構成」**タブをクリックするか、右下隅にある**「構成」**ボタンをクリックします。
4. [一般構成パラメーター](#peer-global-parameters)の値を指定し、ご使用条件に同意します。
5. `「すべてのパラメーター」`ツイスティーを開き、[グローバル構成パラメーター](#peer-global-parameters)の値を指定します。
6. **「ピア構成 (Peer configuration)」**セクションまでスクロールダウンします。`「ピアのインストール (Install Peer)」`チェック・ボックスを選択し、ピアの[構成設定](#peer-parameters)を完了します。
7. **「インストール」**をクリックします。

### 構成パラメーター
{: #icp-peer-configuration-parms}

以下の表は、**ピア・コンポーネントに固有の** {{site.data.keyword.blockchainfull_notm}} Platform の構成可能パラメーターと、これらのパラメーターのデフォルト値を示しています。ピアを試験的に使用する場合や、初めてピアを使用する場合は、データベースとストレージのパラメーターのデフォルト値を使用してください。ピア・コンポーネント・セクションまでスクロールして、`「ピアのインストール (Install Peer)」`チェック・ボックスを選択して、そのコンポーネントのみに関連する構成情報を入力します。**Helm チャート UI では追加の構成は不要と示されますが、ピアをデプロイするには特定のパラメーターを入力する必要があります。**

#### 一般およびグローバル構成パラメーター
{: #peer-global-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
| `Helm リリース名 (Helm release name)`| Helm リリースの名前。小文字で始まり、任意の英数字で終わる必要があり、ハイフンと小文字の英数字のみを含む必要があります。コンポーネントのインストールを試行するたびに固有の Helm リリース名を使用する必要があります。**重要:** この値は、[JSON 秘密ファイル](#peer-config-file)の「hosts」フィールドで「サービス・ホスト名」を生成する際に使用した値と一致する必要があります。| なし | はい |
| `ターゲット名前空間 (Target namespace)`| Helm チャートをインストールする Kubernetes 名前空間を選択します。| なし | はい |
|**グローバル構成 (Global configuration)**| Helm チャート内のすべてのコンポーネントに適用されるパラメーター|||
| `サービス・アカウント名 (Service account name)`| ポッドの実行に使用する[サービス・アカウント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) の名前を入力します。| デフォルト | いいえ |

#### ピア構成パラメーター
{: #peer-parameters}

|  パラメーター     | 説明    | デフォルト  | 必須 |
| --------------|-----------------|-------|------- |
|**クラスター構成** |**クラスター構成情報** | ||
| `ピアのインストール (Install Peer)` | ピアをインストールするために選択します。| チェック・マークなし | はい (ピアをデプロイする場合) |
| `ピア・ワーカー・ノード・アーキテクチャー (Peer worker node architecture)`| クラウド・プラットフォーム・アーキテクチャーを選択します (AMD64 または S390x)| AMD64 | はい |
|`ピア・イメージ・リポジトリー (Peer image repository)`| ピア Helm チャートの場所。このフィールドにはインストール・パスが自動入力されます。| ibmcom/ibp-fabric-peer | はい |
|`ピア Docker イメージ・タグ (Peer Docker image tag)`|ピア・イメージに関連付けられたタグの値。 |1.2.1 (正しい値が自動入力されます)|はい|
|`ピア構成 (Peer configuration)`| 独自の `core.yaml` 構成ファイルをこのフィールドに貼り付けることで、ピアの構成をカスタマイズできます。サンプルの `core.yaml` ファイルを表示するには、[`core.yaml` のサンプル構成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) を参照してください。**上級者専用**。| なし | いいえ |
|`ピア構成の秘密 (Peer configuration secret) (必須)`|ICP 内で作成した[ピア構成の秘密](#peer-config-secret)の名前。| なし | はい |
|`組織 MSP (Organization MSP) (必須)`| 「org1」などの新しい組織 MSPID 値を作成することも、ピアの所属先となる既存の組織 MSP を指定することもできます。順序付けプログラム組織をデプロイした場合は、どのピアの MSPID も順序付けプログラムの MSPID とは異なる値になるようにしてください。また、この値は後で `CORE_PEER_LOCALMSPID` および `configtx.yaml` 用に必要になるため、この値をメモしておいてください。| なし | はい |
|`ピア・サービス・タイプ (Peer service type)`| ピアで[外部ポートを公開 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) するかどうかを指定するために使用します。ポートを外部に対して公開する場合は NodePort を選択し (推奨)、ポートを公開しない場合は ClusterIP を選択します。LoadBalancer および ExternalName はこのリリースではサポートされていません。| NodePort | はい |
| `状態データベース`| チャネル台帳の保管に使用される[状態データベース](../glossary.html#state-database)。ピアは、[ブロックチェーン・ネットワーク](../v10_dashboard.html#network-preferences)と同じデータベースを使用する必要があります。| なし | はい |
|`CouchDB イメージ・リポジトリー (CouchDB image repository)`| 台帳データベースとして CouchDB が選択されている場合にのみ適用されます。このフィールドにはインストール・パスが自動入力されます。Community Edition を使用しており、インターネットにアクセスできない場合は、このフィールドでは、Fabric CouchDB のイメージをダウンロードしたディレクトリーを指定する必要があります。| ibmcom/ibp-fabric-couchdb | はい |
| `CouchDB Docker イメージ・タグ (CouchDB Docker image tag)`| 台帳データベースとして CouchDB が選択されている場合にのみ適用されます。CouchDB イメージに関連付けられたタグの値。|正しい値が自動入力されます。| はい |
|`ピア・データの永続性が有効 (Peer Data persistence enabled)`| クラスターが再始動または失敗した後にデータを維持する機能を有効にします。詳しくは、[Kubernetes のストレージ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/ "ボリューム") を参照してください。*チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。* | オン | いいえ |
|`ピアで動的プロビジョニングを使用 (Peer use dynamic provisioning)`| ストレージ・ボリュームの動的プロビジョニングを有効にする場合にチェック・マークを付けます。| オン | いいえ |
|`ピアの永続ボリューム・クレーム (Peer persistent volume claim)`| 新規クレーム専用。作成される新しい Persistent Volume Claim (PVC) の名前を入力します。| my-data-pvc | いいえ |
|`ピアのストレージ・クラス名 (Peer storage class name)`|ピアのストレージ・クラス名を指定します。| 新しい PVC を作成する場合は空のままにします。それ以外の場合は、既存の PVC に関連付けられているストレージ・クラスを指定します。| いいえ |
|`ピアの既存ボリューム・クレーム (Peer existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。| なし | いいえ |
|`ピアのセレクター・ラベル (Peer selector label)`| PVC の[セレクター・ラベル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。| なし | いいえ |
|`ピアのセレクター値 (Peer selector value)`| PVC の[セレクター値 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "ラベルおよびセレクター")。| なし | いいえ |
|`ピアのストレージ・アクセス・モード (Peer storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。| ReadWriteMany | いいえ |
|`ピアのボリューム・クレーム・サイズ (Peer volume claim size)`| ボリューム・クレームのサイズ。2Gi を超える必要があります。| 8Gi  | はい |
|`状態データベースの永続ボリューム・クレーム (State database persistent volume claim)`| 新規クレーム専用。作成される新しい Persistent Volume Claim (PVC) の名前を入力します。| statedb-pvc | いいえ |
| `状態データベースのストレージ・クラス名 (State database storage class name)`| 状態データベースのストレージ・クラス名を指定します。| なし | いいえ |
| `状態データベースの既存のボリューム・クレーム (State database existing volume claim)`| 既存のボリューム・クレームの名前を指定し、他のすべてのフィールドは空のままにします。| なし | いいえ |
|`状態データベースのセレクター・ラベル (State database selector label)`| PVC の[セレクター・ラベル](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| なし | いいえ |
|`状態データベースのセレクター値 (State database selector value)`| PVC の[セレクター値](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| なし | いいえ |
| `状態データベースのストレージ・アクセス・モード (State database storage access mode)`| PVC のストレージ・[アクセス・モード ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "アクセス・モード") を指定します。| ReadWriteMany | いいえ |
| `状態データベースのボリューム・クレーム・サイズ (State database volume claim size)`| 使用するディスクのサイズを選択します。 | 8Gi | はい |
|`CouchDB - データの永続性が有効 (CouchDB - Data persistence enabled)`|CouchDB コンテナーの場合は、コンテナーの再始動時に台帳データが使用可能になります。*チェック・マークを外すと、フェイルオーバーやポッドの再始動の場合にすべてのデータが失われます。*| オン | いいえ |
|`CouchDB - 動的プロビジョニングを使用 (CouchDB - Use dynamic provisioning)`|CouchDB コンテナーの場合は、Kubernetes 動的ストレージを使用します。| オン | いいえ |
| `ピア CPU 要求 (Peer CPU request)` | ピアに割り当てる CPU の最小数。| 1 | はい |
| `ピア CPU 制限 (Peer CPU limit)` | ピアに割り当てる CPU の最大数。| 2 | はい |
| `ピア・メモリー要求 (Peer Memory request)` | ピアに割り当てるメモリーの最小量。| 1Gi | はい |
| `ピア・メモリー制限 (Peer Memory limit)` | ピアに割り当てるメモリーの最大量。| 4Gi | はい |
| `CouchDB CPU 要求 (CouchDB CPU request)` | CouchDB に割り当てる CPU の最小数。| 1 | はい |
| `CouchDB CPU 制限 (CouchDB CPU limit)` | CouchDB に割り当てる CPU の最大数。| 2 | はい |
| `CouchDB メモリー要求 (CouchDB Memory request)` | CouchDB に割り当てるメモリーの最小量。| 1Gi | はい |
| `CouchDB メモリー制限 (CouchDB Memory limit)` | CouchDB に割り当てるメモリーの最大量。| 4Gi | はい |


ピア・データベースとして CouchDB が選択されている場合、ピア用と CouchDB 用にそれぞれ 1 つずつ、2 つのコンテナーがポッドに作成されます。ピア・コンテナーには、ファイル・システムのブロックおよびトランザクションが保管されるピア PVC に対する単一のボリューム・マウントが含まれます。CouchDB コンテナーは、台帳データを含むピア状態データベース PVC をマウントします。

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
{: #icp-helm-cli}

また、`helm` CLI を使用して Helm リリースをインストールすることもできます。`helm install` コマンドを実行する前に、[Helm CLI 環境にクラスターの Helm リポジトリーを追加 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Helm CLI への内部 Helm リポジトリーの追加") してください。

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
--values peer-s390x-values.yaml \
--tls
```

ダウンロードしたアーカイブ・ファイルに含まれる、必要なパラメーターとそのデフォルト設定をすべて含む `values.yaml` を編集することで、新規 `yaml` ファイルを作成することができます。

## ピアのインストールの確認
{: #verify-installation-icp}

構成パラメーターを入力し、**「インストール」**ボタンをクリックした後、**「Helm リリースの表示」**ボタンをクリックしてデプロイメントを表示します。成功した場合は、「デプロイメント」テーブルの`「必要数」`、`「現行」`、`「最新」`、および`「使用可能」`の各フィールドに値 1 が表示されます。最新表示をクリックしてテーブルが更新されるまで待つ必要がある場合があります。「デプロイメント」テーブルは、ICP コンソールの左上隅にある**「メニュー」**アイコンをクリックして確認することもできます。メニュー・リストから、**「ワークロード」**、**「Helm リリース」**の順にクリックします。

`「メモ」`セクションまでスクロールダウンすると、[ピアを操作](peer_operate_icp.html)する際に使用する重要な情報があります。

## ピア・ログの表示
{: #peer-deploy-view-logs}

ピアのログを表示するには、[kubectl CLI コマンド](peer_operate_icp.html#peer-kubectl-configure)を使用するか、[Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Elastic Stack への開かれた窓") を使用します。詳しくは、こちらの[ログへのアクセス手順](peer_operate_icp.html#peer-icp-view-logs)を参照してください。

## 次の作業

ピアをデプロイした後に、いくつかの操作ステップを完了する必要があります。その後、ブロックチェーン・ネットワークにトランザクションを送信したり、ブロックチェーン・ネットワークから分散台帳を読み取ったりできるようになります。詳しくは、[マルチクラウド・ネットワークを使用したピアの操作](peer_operate_icp.html)を参照してください。

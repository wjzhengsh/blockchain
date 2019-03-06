---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private での順序付けプログラムの操作
{: #icp-orderer-operate}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform の順序付けプログラムを {{site.data.keyword.cloud_notm}} Private にインストールした後、configmap が作成されます。これには、環境変数のデフォルト設定が含まれます。 順序付けプログラムの環境変数を変更したり追加したりして、順序付けプログラムの動作を構成できます。

一般に、順序付けプログラム管理者が順序付けプログラムのブートストラッピングと保守の責任を持ちますが、存在する順序付けプログラムが 1 つのみの SOLO デプロイメントではこれは必要ではありません。 SOLO デプロイメントでは、順序付けプログラム管理者は、順序付けプログラムのシステム・チャネルへの新規組織の追加の責任を持ちます。

## 前提条件
{: #icp-orderer-operate-prerequisites}

順序付けプログラムを操作するには、{{site.data.keyword.cloud_notm}} Private クラスターからいくつかの前提条件ステップを実行する必要があります。

**前提条件:**
- [CLI の構成](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)
- [順序付けプログラムのエンドポイント情報の取得](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
- [順序付けプログラムの TLS 証明書のダウンロード](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)

これで、このトピックの手順を使用して順序付けプログラムを操作できるようになります。 順序付けプログラムの操作はコマンド・ラインを介して行いますが、このためには `peer` CLI バイナリーを取得する必要があります。 `peer` CLI バイナリーは、`configtxlator` などの他のバイナリーとともにダウンロードされます。 したがって、このトピックのコマンドの多くは「peer」という語で始まります。 実際にはピアを使用しているわけではなく、単にバイナリーの名前です。

### CLI の構成
{: #icp-orderer-operate-kubectl-configure}

{{site.data.keyword.cloud_notm}} Private で実行されている順序付けプログラムのコンテナーに接続するには、**kubectl** コマンド・ライン・ツールを使用する必要があります。

1. {{site.data.keyword.cloud_notm}} Private クラスター UI にログインします。 **「コマンド・ライン・ツール」**タブにナビゲートし、**「Cloud Private CLI」**をクリックします。 ダウンロード可能な以下のツールが表示されます。

   * IBM Cloud Private CLI およびプラグインのインストール
   * Kubectl CLI のインストール
   * Helm のインストール
   * Istio CLI のインストール

  順序付けプログラムを操作するには、最初の **3 つ**のツールを使用する必要があります。このうち、Helm はオプションです。 それぞれをクリックして、使用しているマシン・タイプ向けの `curl` コマンドを実行します。 次に、ツールごとに `chmod` コマンドと `sudo mv` コマンドを発行します。 `chmod` コマンドは該当の CLI の権限を変更して実行可能にし、`sudo mv` コマンドはファイルを移動して名前変更します。

  最初のツールである **cloudctl** のコマンドは、以下の例のようになります。

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  2 番目のツールである **kubectl** のコマンドは以下の例のようになります。

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. **kubectl** を構成した後、以下のコマンドを実行します。

  ```
  kubectl get pods
  ```
  {:codeblock}

  コマンドが成功すると、以下の例のような応答が返されます。この場合、`orderer-6cf85f6687-hcxpl` は順序付けプログラムのコンテナーの名前です。

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  これで、**kubectl** ツールを使用して順序付けプログラムのエンドポイント情報を取得する準備ができました。

3. オプションで、**Helm** を使用する場合は、さらにいくつかのステップを実行します。 Helm のインストールはオプションであり、これらの手順では使用する必要がありません。 ただし、Helm リリースを管理し、{{site.data.keyword.cloud_notm}} Private にデプロイする独自のアーカイブを作成するために役立ちます。

  1. {{site.data.keyword.cloud_notm}} Private UI から「Helm のインストール (Install Helm)」をクリックし、`curl` コマンドを実行します。
  2. 以下のコマンドを実行して、`tar` ファイルを解凍します。

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. `/helm` を `darwin-amd64` に付加して、`sudo mv` コマンドを実行します。 Helm に対して `chmod` コマンドを実行する必要はありません。 例えば、次のようにします。

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  `helm help` コマンドを実行し、Helm が正常にインストールされたことを確認できます。

### 順序付けプログラムのエンドポイント情報の取得
{: #icp-orderer-operate-orderer-endpoint}

順序付けプログラムのシステム・チャネルに対して更新を行うには、順序付けプログラムのエンドポイントをターゲットにする必要があります。 以下のステップを実行するには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。 **「メモ」**セクションには、順序付けプログラムのデプロイメントを操作するために使用できるコマンド・セットが含まれます。
5. まだ行っていない場合は、手順に従って [kubectl CLI](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) を構成してください。 これは、順序付けプログラムのコンテナーと対話するために使用する必要があります。
6. CLI で、**1. Get the application URL by running these commands:** の後に続く、メモに示される最初のコマンドを実行します。このコマンドにより、以下の例のように、順序付けプログラムの URL とポートが出力されます。 この URL を保存します。これは、将来のコマンドでプロキシー・アドレスと外部ノード・ポートを設定するために使用する必要があります。

  ```
  http://9.30.94.174:30159
  ```

この例で、プロキシー IP アドレスは `9.30.94.174` であり、7050 に対応する外部ノード・ポートは `30159` です。  

**注:** 順序付けプログラムをファイアウォールの内側にデプロイしている場合は、プラットフォーム上のネットワークがこのタスクを実行できるようにパススルーをオープンする必要があります。

### 順序付けプログラムの TLS 証明書のダウンロード
{: #icp-orderer-operate-tls-cert}

リモート・クライアントから順序付けプログラムと通信するためには、順序付けプログラムの TLS 証明書をダウンロードして、コマンドにこれを渡す必要があります。 以下のステップを実行するには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。 **「メモ」**セクションには、順序付けプログラムのデプロイメントを操作するために使用できるコマンド・セットが含まれます。
5. まだ行っていない場合は、手順に従って [kubectl CLI](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) を構成してください。 これは、順序付けプログラムのコンテナーと対話するために使用する必要があります。
6. CLI で、**3. Get orderer TLS root cert file** の後に続く、メモに示される 3 番目のコマンドを実行します。  このコマンドは、TLS 証明書をファイル `cert.pem` としてローカル・マシンに保存します。

  **注:** コマンドを実行する前に、以下のコマンドに示すように `app=orderer` を削除できます。

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. 生成された証明書を、将来のコマンドで参照できる場所に移動し、`orderertls.pem` に名前変更します。

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### 証明書のローカル・システムでの管理
{: #manage-certs}

順序付けプログラム管理者の MSP フォルダーが生成されたディレクトリーに移動します。 この資料のサンプル・ステップにどのように従ったか、またはデプロイしているコンポーネントの数に応じて、MSP フォルダーは `$HOME/fabric-ca-client/orderer-admin/msp` または `$HOME/fabric-ca-client/peer-admin/msp` にあります。

順序付けプログラムを操作するためには、まず、ローカル・マシンでいくつかの証明書管理作業を行う必要があります。 また、順序付けプログラムから TLS 証明書にアクセスできることを確認する必要もあります。 使用する証明書について詳しくは、[{{site.data.keyword.cloud_notm}} Private での認証局の操作](/docs/services/blockchain/howto/CA_operate.html#ca-operate)の[メンバーシップ・サービス・プロバイダー](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)を参照してください。

1. 順序付けプログラム管理者の署名付き証明書を `admincerts` という名前の新規フォルダーに移動します。

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. [TLS 証明書をダウンロード](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)し、コマンド・ラインからこれを参照できることを確認します。 この資料のコマンド例に従った場合、この TLS 証明書は `$HOME/fabric-ca-client/orderer-tls/orderertls.pem` ファイルにあります。

tree コマンドを実行して、これらのステップを完了したことを検証できます。 証明書を格納したディレクトリーにナビゲートします。 tree コマンドを実行すると、以下の構造に似た結果が生成されます。
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

## 順序付けプログラムのシステム・チャネルへの組織の追加
{: #icp-orderer-operate-add-organizations-to-consortium}

**注:** ピアをデプロイしてこれをスターター・ネットワークまたはエンタープライズ・プラン・ネットワークに接続する予定の場合は、このステップをスキップできます。

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Helm チャートを使用して順序付けプログラムをデプロイする場合、システム・チャネルの唯一の管理者として、起源ブロックが順序付けプログラム組織とともに自動的に作成されます。 つまり、順序付けプログラムの組織管理のみが、共同事業体に組織を追加することができます。 これを管理するポリシー (順序付けプログラムのシステム・チャネルの `mod_policy` と呼ばれる) は、順序付けプログラムのシステム・チャネルに新規組織を追加した後は変更することができません。

組織を順序付けプログラムのシステム・チャネルに追加すると、これらは「共同事業体」の一部になります。共同事業体は、デフォルトでチャネルを作成できるメンバーのリストです。 また、共同事業体のメンバーになっていると、証明書や、システム・チャネルにリストされている情報を使用してチャネルに追加しやすくなります。

順序付けプログラムのシステム・チャネルの更新は、「構成可能な更新トランザクション」を使用して実行します。この場合、組織の関連 MSP 情報が順序付けプログラムのシステム・チャネルのチャネル構成に追加されます。

順序付けプログラムのシステム・チャネルに組織を追加することは、基本的に、チャネルの構成を更新して組織を追加するフローと同じです。 ただし、更新対象のチャネルがアプリケーション・チャネルではないこと、および関連管理がピア組織ではなく順序付けプログラム管理であることから、いくつかの変更を行う必要があります。

最初にシステム・チャネルに参加しなくても、チャネルに組織を追加することができます。 詳しくは、Hyperledger Fabric の資料で[チャネルへの組織の追加に関するチュートリアル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html "チャネルへの組織の追加")を参照してください。

以下のリストに、全般的なステップと、共同事業体のさまざまな組織セットによって実行されるタスクを示します。

1. 共同事業体に参加する各組織は、[組織定義を準備](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)する必要があります。
2. 順序付けプログラムの組織管理は、順序付けプログラムのシステム・チャネルに組織を追加することによって、[共同事業体](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium)を形成します。
3. チャネル構成トランザクションを準備することで、共同事業体のすべての組織が[新規チャネルを作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx)できます。

## Fabric ツールの取得
{: #icp-orderer-operate-get-fabric-tools}

システム・チャネルを更新するには、以下の Hyperledger Fabric ツールをダウンロードする必要があります。
- [peer ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html)。これを使用すると、起源ブロックをフェッチし、システム・チャネルを更新できるようになります。
- [configtxlator ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html)。これにより、protobuf フォーマットのチャネル構成が JSON フォーマットに変換され、読み取りや更新がより簡単になります。

1. ツールを保管する場所を決定し、以下のコマンドを実行します。

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. 上記の Fabric ツールがダウンロードされる、ツールのパスを設定します。

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  例えば、ホーム・ディレクトリーにバイナリーをインストールした場合、`PATH` を以下のように設定します。

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. バイナリーをダウンロードすると、core.yaml、orderer.yaml、および configtx.yaml ファイルが含まれる config フォルダーが作成されます。 Fabric config パスをこの config ディレクトリーに設定します。

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  例えば、次のようにします。
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## 組織定義の作成
{: #icp-orderer-operate-org-definition}

組織の**定義**には、組織名 (MSP ID) および関連する証明書が含まれます。 システム・チャネルおよびアプリケーション・チャネルはこの定義を使用して、チャネル作成、更新、およびトランザクション・エンドースメントを管理するポリシーに組織を含めます。 共同事業体に参加を希望する各組織は、このステップを実行する必要があります。 詳しくは、[組織定義の準備](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)を参照してください。

## 共同事業体の形成
{: #icp-orderer-operate-consortium}

共同事業体を形成する全体的な流れを再確認します。

1. 順序付けプログラムのシステム・チャネルは、システム・チャネルのメンバーとして順序付けプログラム組織のみを使用して形成されます。 その組織は、追加の署名を必要なしで更新を行うことができます。
2. 順序付けプログラムの組織管理は、共同事業体に参加を希望するメンバーから組織定義を受け取ります。 順序付けプログラム組織はその組織定義を使用して、順序付けプログラムのシステム・チャネルの構成を更新します。

### 組織定義の取得

順序付けプログラムは、共同事業体への参加を希望するメンバーから[組織定義](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)を受け取る必要があります。 これは、MSP ID と暗号情報を含む JSON ファイルを送信する他のメンバーとのアウト・オブ・バンド操作で実行する必要があります。 以下のコマンドでの参照用に、`org-definitions` という名前のフォルダーを作成し、そのディレクトリーにすべての関連ファイルを配置したと想定します。

### システム・チャネルの起源ブロックのフェッチ

1. デプロイメント時に設定された順序付けプログラム組織の MSP 名、順序付けプログラムの MSP のパス、および順序付けプログラムの TLSCA 証明書のパスを使用して、以下の環境変数を設定します。

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  フィールドを独自の情報に置き換えます。

    - `<CORE_PEER_LOCALMSPID>` を順序付けプログラム組織の MSP ID に置き換えます。 これは、`<orderer pod name>` を順序付けプログラムのポッドの値に置き換えて以下のコマンドを実行することで、順序付けプログラムのコンテナー内部でも表示されるようになります。

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      出力は以下のようになります。

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      したがって、`CORE_PEER_LOCALMSPID` は ordererOrg です。

    - `<CORE_PEER_MSPCONFIGPATH>` を順序付けプログラム組織の管理 MSP フォルダーのパスに置き換えます。
    - `<CORE_PEER_TLS_ROOTCERT_FILE>` を TLS CA 証明書のパスに置き換えます。
    - `<PROXY_IP>` を、[順序付けプログラムのエンドポイント情報](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)からのプロキシー IP アドレスに置き換えます。
    - `<EXTERNAL_NODE_PORT>` を、[順序付けプログラムのエンドポイント情報](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)からの外部ノード・ポートに置き換えます。

  環境変数は、以下の例のようになります。

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  以下のコマンドを発行すればいつでもこれらの環境変数を確認することができます。

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. チャネル名を環境変数として設定します。 順序付けプログラムのシステム・チャネルの名前は、常に `test-system-channel-name` になります。

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. システム・チャネルの起源ブロックをフェッチします。 まず、`org-definitions` 内に `configupdate` という名前のディレクトリーを作成し、更新処理中に生成された構成ファイルを保管します。 このディレクトリーには任意の名前を付けられますが、以下のコマンド例では `configupdate` を使用します。

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  `<orderer_TLS_root_cert_file>` を、この[ステップ](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)で作成した `orderertls.pem` ファイルのパスに置き換えます。 例えば、コマンドは以下の例のようになります。

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  正常に実行された場合、コマンドにより以下の出力が生成されます。

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  ファイル・システムで後から `genesis.pb` ブロックを見つけることもできます。

### システム・チャネル更新の作成
{: #icp-orderer-operate-system-channel-update}

ダウンロードした [Fabric ツール](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-get-fabric-tools)の `configtxtlator` によって、protobuf フォーマットのチャネル構成が JSON フォーマット、またはその逆に変換されます。

これらの手順は、チャネル更新チュートリアルの [JSON 形式へのブロックの変換 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down "Convert the Configuration to JSON and Trim It Down") の大まかな手順と同じです。ただし、更新しているのがアプリケーション・チャネルではなく順序付けプログラムのシステム・チャネルであるという事実が反映されるように、いくつかの変更をチュートリアルのコマンドに対して行う必要があります。 このプロセスについて詳しくは、チュートリアルを参照してください。 このセクションは単にコマンドを示します。

1. [組織を作成した](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)フォルダーから `configupdate` フォルダーに、組織定義の JSON ファイルをコピーします。 以下のコマンド例では、組織定義の JSON ファイルは `org1definition.json` です。

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. 起源ブロックを JSON フォーマットに変換します。 以下のコマンドを実行します。

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. 以下のコマンドを実行して、組織の暗号情報を共同事業体構成に追加します。 <NEWORGMSP> を、[作成した組織](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)の組織 MSP ID に置き換えます。

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  コマンドは以下の例のようになります。

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. 共同事業体に参加させる必要のある組織ごとにこのコマンドを繰り返します。 `./orgdefinition.json` ファイルが新しい組織 JSON ファイルになるように変更してください。

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. 以下のコマンドを実行して、`modified_config.json` ファイルを JSON フォーマットの構成の更新に変換します。

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. これらのコマンドが完了したら、`config_update` にエンベロープを配置します。

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. 以下のコマンドを実行して、構成の更新を変換して protobuf フォーマットに戻します。

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### システム・チャネルへの更新の送信

これらのステップを実行する前に、`FABRIC_CFG_PATH`、`$PROXY`、および `ORDERER_PORT` が正しく設定されていることを確認してください。 以下のコマンドを実行します。

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
```
{:codeblock}

環境変数は、以下の例のようになります。

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

チャネル構成の更新 `config_update_in_envelope.json` を作成した後、順序付けプログラム組織は、以下のコマンドを使用してチャネル更新を送信できます。

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

このコマンドは同時に更新要求に署名し、順序付けプログラムに送信します。 順序付けプログラム組織はシステム・チャネルの唯一の管理者であることから、この更新を有効な要求にするために必要な署名は 1 つのみです。 システム・チャネル更新が成功した場合は、以下の出力が表示されます。

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

順序付けプログラムのシステム・チャネルの単一の構成の更新に複数の組織定義を含めることは可能ですが、一度に 1 つの組織でチャネルを更新し、更新が正常に行われたことを確認するのがベスト・プラクティスです。

## 順序付けプログラムのログの表示
{: #icp-orderer-operate-orderer-view-logs}

コンポーネント・ログは、コマンド・ラインから [`kubectl CLI コマンド`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)を使用して表示することも、[Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Your Window intothe Elastic Stack") ({{site.data.keyword.cloud_notm}} Private クラスターに含まれています) を使用して表示することもできます。

- `kubectl logs` コマンドを使用して、ポッド内のコンテナー・ログを表示します。 ポッド名が不明な場合は、以下のコマンドを実行してポッドのリストを表示します。

  ```
  kubectl get pods
  ```
  {:codeblock}

  次に、`<pod_name>` を上記のコマンド出力にあるポッドの名前に置き換えて以下のコマンドを実行し、順序付けプログラムのコンテナーのログを取得します。

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  `kubectl logs` コマンドについて詳しくは、[Kubernetes の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”) を参照してください。

- または、[{{site.data.keyword.cloud_notm}} Private クラスター管理コンソール ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html) を使用してログにアクセスすることもできます。この場合、ログは Kibana で開きます。

  **注:** Kibana でログを表示する場合、`「No results found」`という応答を受け取ることがあります。 この状態は、{{site.data.keyword.cloud_notm}} Private がワーカー・ノードの IP アドレスをそのホスト名として使用する場合に起こることがあります。 この問題を解決するには、パネルの上部で `node.hostname.keyword` で始まるフィルターを削除すると、ログが表示されるようになります。

## トラブルシューティング
{: #icp-orderer-operate-troubleshooting}

### **問題:** `peer channel update` コマンドがエラーで失敗する。
{: #icp-orderer-operate-peer-channel-update-error}

`peer channel update` コマンドを実行すると、以下のエラーを受け取る可能性があります。

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

このエラーは、ピアが順序付けプログラムと同じ組織 MSP ID を使用している場合に発生することがあります。  

**解決策:**  

この問題を解決するには、順序付けプログラムのデプロイ時に使用したものとは異なる組織 MSP ID を指定して、ピアを再デプロイする必要があります。

---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# マルチクラウド・ネットワークを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作
{: #icp-peer-operate}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private ピアをセットアップした後、ピアでブロックチェーン・ネットワークの台帳の照会と呼び出しのためのトランザクションを発行できるようにするには、いくつかの操作ステップを実行する必要があります。 このステップでは、チャネルへの組織の追加、チャネルへのピアの参加、ピアでのチェーンコードのインストール、チャネルでのチェーンコードのインスタンス化、およびピアへのアプリケーションの接続を行います。 ピアをスターター・プランまたはエンタープライズ・プランのネットワークに接続する必要がある場合は、[スターター・プランまたはエンタープライズ・プランを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate)を参照してください。
{:shortdesc}

ピアを操作するには、{{site.data.keyword.cloud_notm}} Private クラスターからいくつかの前提条件ステップを実行する必要があります。

**前提条件:**
- [CLI の構成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure)
- [ピアのエンドポイント情報の取得](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-peer-endpoint)
- [ピアの TLS 証明書のダウンロード](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-tls-cert)

次に、以下のいずれかの方法を使用してピアを操作します。

**操作パス:**
- [Fabric SDK の使用](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-with-sdk)
- [コマンド・ラインの使用](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-cli-operate)

推奨されるパスは Fabric SDK ですが、この手順では、SDK の操作に精通していることが前提となっています。 コマンド・ラインを使用する場合は、Fabric ピア・バイナリーを使用できます。

組織がまだ共同事業体またはチャネルのメンバーでない場合は、以下のステップを使用して、[チャネルを作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel)できます。 この手順では、[組織定義を準備する](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)方法について説明します。 この定義は、ユーザーを順序付けプログラムのシステム・チャネルに追加して共同事業体のメンバーにする際に使用されます。 その後、[チャネル・トランザクションを作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx)することで、新しいチャネルを形成できます。
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../best_practices.html#best-practices-app-ha-app).
-->

## 前提条件
{: #icp-peer-operate-prerequisites}

SDK またはコマンド・ラインのどちらを使用する予定であるかにかかわらず、ピアを操作する過程でこのセクションのステップを実行する必要があります。 これらすべてのステップを完了すると、開始できるようになります。

### CLI の構成
{: #icp-peer-operate-kubectl-configure}

{{site.data.keyword.cloud_notm}} Private で実行されるピア・コンテナーに接続するには、**kubectl** コマンド・ライン・ツールを使用する必要があります。

1. {{site.data.keyword.cloud_notm}} Private クラスター UI にログインします。 **「コマンド・ライン・ツール」**タブにナビゲートし、**「Cloud Private CLI」**をクリックします。 ダウンロード可能な以下のツールが表示されます。

   * IBM Cloud Private CLI およびプラグインのインストール
   * Kubectl CLI のインストール
   * Helm のインストール
   * Istio CLI のインストール

  ピアを操作するには、最初の **3 つ**のツールを使用する必要がありますが、そのうち Helm はオプションです。 それぞれをクリックして、使用しているマシン・タイプ向けの `curl` コマンドを実行します。 次に、ツールごとに `chmod` コマンドと `sudo mv` コマンドを発行します。 `chmod` コマンドは該当の CLI の権限を変更して実行可能にし、`sudo mv` コマンドはファイルを移動して名前変更します。

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

2. **kubectl** を構成した後、次のコマンドを実行します。

  ```
  kubectl get pods
  ```
  {:codeblock}

  正常に実行された場合、このコマンドにより、次の例のような応答が返されます。ここで、`peer-6cf85f6687-hcxpl` はピア・コンテナーの名前です。

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  これで、**kubectl** ツールを使用してピアのエンドポイント情報を取得する準備ができました。

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

### ピアのエンドポイント情報の取得
{: #icp-peer-operate-peer-endpoint}

チャネルに参加したり、スマート・コントラクトをインストールしたりするには、SDK または Fabric CA クライアントからピア・エンドポイントをターゲットにする必要があります。 ピアのエンドポイントを見つけるには、{{site.data.keyword.cloud_notm}} Private コンソール UI を使用します。 以下のステップを実行するには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。 **「メモ」**セクションには、ピア・デプロイメントの操作に役立つ一連のコマンドが表示されます。
5. [kubectl CLI](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) をまだ構成していない場合には、ステップに従って構成します。 kubectl を使用してピア・コンテナーと対話します。
6. CLI で、メモの**「1. Get the application URL by running these commands:」**に続く最初のコマンドを実行します。このコマンドにより、次の例のようなピアの URL とポートが出力されます。 今後のコマンドのために、この URL を保存します。

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

ファイアウォールの内側にピアをデプロイする場合は、パススルーを開き、プラットフォーム上のネットワークでピアとの TLS ハンドシェークを実行できるようにする必要があります。 ピアのポート 7051 にバインドされたノード・ポートのパススルーのみを有効にする必要があります。 詳しくは、[ピアのエンドポイント情報の検索](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)を参照してください。
{:note}

### ピアの TLS 証明書のダウンロード
{: #icp-peer-operate-tls-cert}

リモート・クライアントからピアと通信するには、ピアの TLS 証明書をダウンロードしてコマンドに渡す必要があります。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。 **「メモ」**セクションには、ピア・デプロイメントの操作に役立つ一連のコマンドが表示されます。
5. [kubectl CLI](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) をまだ構成していない場合には、ステップに従って構成します。 kubectl を使用してピア・コンテナーと対話します。
6. CLI で、メモの**「3. Get peer TLS root cert file」**に続く最初のコマンドを実行します。 このコマンドは、TLS 証明書を `cacert.pem` ファイルとしてローカル・マシン上に保存します。
7. この証明書を今後のコマンドで参照できる場所に移動し、その名前を `peertls.pem` に変更します。

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Fabric SDK を使用したピアの操作
{: #icp-peer-operate-with-sdk}

Hyperledger Fabric SDK には強力な API のセットが用意されており、これらの API により、アプリケーションはブロックチェーン・ネットワークと対話したりこれを操作したりすることができます。 サポートされている言語の最新リストおよび Fabric SDK 内で使用可能な API の完全なリストについては、[Hyperledger Fabric SDK コミュニティーの資料 ![外部リンク・アイコン](../images/external_link.svg " 外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK コミュニティーの資料") を参照してください。 Fabric SDK を使用して、ピアを {{site.data.keyword.blockchainfull_notm}} Platform 上のチャネルに参加させたり、チェーンコードをピアにインストールしたり、チェーンコードをチャネル上でインスタンス化したりできます。

以下の手順では、[Fabric Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Fabric Node SDK") を使用してピアを操作するため、事前に SDK に精通していることが前提となっています。 開始前に Node SDK の使用方法を学習するために、およびチェーンコードを呼び出して照会する準備ができたら、ピアを使用してアプリケーションを開発する際のガイドとして、[アプリケーションの開発チュートリアル](/docs/services/blockchain/v10_application.html#dev-app)を使用できます。

### Node SDK のインストール
{: #icp-peer-operate-install-sdk}

NPM を使用して [Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Node SDK") をインストールできます。

```
npm install fabric-client@1.4.0
```
{:codeblock}

バージョン 1.4.0 の Node SDK を使用することをお勧めします。

### ピアを使用するための SDK の準備
{: #icp-peer-operate-node-sdk}

ピアは、ピア管理者の署名付き証明書を組み込むようにデプロイされています。 これにより、ピア管理者の証明書および MSP フォルダーを使用してピアを操作できるようになります。

[ピア管理者の登録](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin)時に作成した証明書を見つけます。 コマンド例を使用した場合、ピア管理者の MSP フォルダーは `$HOME/fabric-ca-client/peer-admin` にあります。
  - MSP フォルダー内の署名付き証明書 (公開鍵) および秘密鍵を使用して、SDK でピア管理者のユーザー・コンテキストを作成できます。 これらのキーは、以下の場所にあります。
    - 署名付き証明書は、**signcerts** フォルダー (`$HOME/fabric-ca-client/peer-admin/msp/signcerts`) 内にあります。
    - 秘密鍵は、**keystore:** フォルダー (`$HOME/fabric-ca-client/peer-admin/msp/keystore`) 内にあります。

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin). -->

### SDK へのピアの TLS 証明書の引き渡し
{: #icp-peer-operate-download-peer-tlscert}

SDK からの通信を認証するには、ピアの TLS 証明書を参照する必要があります。 [ピア・コンテナーからダウンロードした](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-tls-cert) TLS 証明書を見つけて、アプリケーションから参照できる場所に保存します。 コマンド例を使用した場合、ピアの TLS 証明書は `$HOME/fabric-ca-client/peer-tls/peertls.pem` にあります。 次に、単純なファイル読み取りコマンドを使用して、TLS 証明書をアプリケーションにインポートできます。

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### SDK へのピアのエンドポイント情報の提供
{: #icp-peer-operate-SDK-endpoints}

[ピアのエンドポイント情報](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-peer-endpoint)を検索し、新しいピア変数を宣言して、SDK にこの情報を提供します。 以下の例では、Fabric ネットワーク上のエンドポイントとしてピアを定義し、インポートした TLS 証明書をピアに渡します。

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

大規模ネットワークが存在し、トランザクションを承認する必要がある異なる組織に属する複数のピアが含まれている場合、共同事業体と連携して、[共通接続プロファイルの作成![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/tutorial-network-config.html "共通接続プロファイル")が必要になることがあります。

### SDK への順序付けプログラムの TLS 証明書の引き渡し
{: #icp-peer-operate-download-orderer-tlscert}

チャネルに参加してトランザクションを送信するには、共同事業体の順序付けプログラムの TLS 証明書も必要です。 順序付けプログラムの管理者である場合は、手順に従って[順序付けプログラムの TLS 証明書をダウンロード](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)してください。  コマンド例を使用した場合、ピアの TLS 証明書は `$HOME/fabric-ca-client/orderer-tls/orderertls.pem` にあります。 順序付けプログラムが別の組織によって管理されている場合は、アウト・オブ・バンド操作で TLS 証明書を提供してもらう必要があります。 その後、TLS 証明書をアプリケーションにインポートできます。

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### SDK への順序付けプログラム情報の提供
{: #icp-peer-operate-orderer-SDK-endpoints}

SDK を使用するには、共同事業体の順序付けプログラムのエンドポイント情報も必要です。 順序付けプログラムの管理者である場合は、手順に従って[順序付けプログラムのエンドポイント情報を取得](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)できます。 順序付けプログラムが別の組織によって管理されている場合は、アウト・オブ・バンド操作で順序付けプログラムの URL を提供してもらう必要があります。 以下の例では、順序付けプログラムをエンドポイントとして定義し、そのエンドポイントに順序付けプログラムの TLS 証明書を渡します。

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### SDK を使用したチャネルへの参加
{: #icp-peer-operate-join-channel-sdk}

チャネルをピアと結合するには、組織がチャネルのメンバーであることが必要です。 チャネルのメンバーでない場合は、手順に従って[新しいチャネルを作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel)できます。

組織がチャネルのメンバーになったら、手順に従って、SDK を使用して[ピアをチャネルに参加](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk)させます。

### SDK を使用したピアへのチェーンコードのインストール
{: #icp-peer-operate-install-cc-sdk}

以下の手順に従って、SDK を使用してピアに[チェーンコードをインストール](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk)します。

### SDK を使用したチャネルでのチェーンコードのインスタンス化
{: #icp-peer-operate-instantiate-cc-sdk}

チェーンコードをインスタンス化または更新する必要があるのは、チャネルのいずれかのメンバーのみです。 したがって、ピアにチェーンコードがインストールされているチャネルのすべてのメンバーは、チェーンコードをインスタンス化してエンドースメント・ポリシーを指定できます。 ただし、ピアを使用してチャネルでチェーンコードをインスタンス化する必要がある場合は、SDK を使用し、手順に従って[チェーンコードをインスタンス化](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk)できます。

## CLI を使用したピアの操作
{: #icp-peer-operate-cli-operate}

また、Fabric ピア・バイナリーを使用して、コマンド・ラインからピアを操作することもできます。

ピアはピア管理者の署名付き証明書を組み込むようにデプロイされており、これにより、その ID を使用してピアを操作できます。 以下の手順では、ピアをチャネルに参加させたり、ピア上にチェーンコードをインストールしてからチャネル上でチェーンコードをインスタンス化したりするために、[ピアのデプロイ](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin)時に生成されたピア管理者の MSP フォルダーを使用します。

### Fabric ピア・クライアントのダウンロード
{: #icp-peer-operate-fabric-client}

リモート・クライアントからピアと対話するには、[Fabric ピア・クライアント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peercommand.html "Fabric ピア・コマンド") をダウンロードする必要があります。

ピア・クライアントを入手する最も簡単な方法は、Fabric ツール・バイナリーを Hyperledger プロジェクトからダウンロードすることです。 コマンド・ラインでバイナリーをダウンロードするディレクトリーを作成し、以下のコマンドを発行して、これらのバイナリーをフェッチします。 最初に [Curl ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl") をインストールする必要があります。

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
```
{:codeblock}

このコマンドにより、バイナリーは `bin/` ディレクトリーにインストールされます。 上記の Fabric ツールがダウンロードされる、ツールのパスを設定します。
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

例えば、ホーム・ディレクトリーにバイナリーをインストールした場合、`PATH` を以下のように設定します。

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

バイナリーをダウンロードすると、core.yaml、orderer.yaml、および configtx.yaml ファイルが含まれる config フォルダーが作成されます。 Fabric config パスをこの config ディレクトリーに設定します。

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

例えば、次のようにします。
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}

### 証明書のローカル・システムでの管理
{: #icp-peer-operate-manage-certs}

ピア管理者の MSP フォルダーが生成されるディレクトリーに切り替えます。 この資料のステップ例に従う場合、MSP フォルダーは次のようなディレクトリーにあります。

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

ピアを操作するには、ローカル・マシン上で証明書の一部の管理を行う必要があります。 また、ピアから TLS 証明書にアクセスできることを確認する必要もあります。 使用する証明書について詳しくは、[{{site.data.keyword.cloud_notm}} Private での認証局の操作](/docs/services/blockchain/howto/CA_operate.html#ca-operate)の[メンバーシップ・サービス・プロバイダー](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)を参照してください。

1. ピア管理者の署名付き証明書を `admincerts` という名前の新規フォルダーに移動します。

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. [ピアの TLS 証明書をダウンロード](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-tls-cert)し、コマンド・ラインからこれを参照できることを確認します。 この資料のコマンド例に従った場合、この TLS 証明書は `$HOME/fabric-ca-client/peer-tls/peertls.pem` ファイルにあります。

3. 順序付けプログラムの TLS 証明書を参照する必要もあります。 順序付けプログラムの管理者である場合は、手順に従って[順序付けプログラムの TLS 証明書をダウンロード](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)してください。 順序付けプログラムが別の組織によって管理されている場合は、アウト・オブ・バンド操作で TLS 証明書を提供してもらう必要があります。 この証明書を、今後のコマンドで参照できる場所に保存します。

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
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CLI 環境変数の設定
{: #icp-peer-operate-environment-variables}

すべての証明書を必要な場所に移動した後、ピア CLI コマンドで使用する環境変数をいくつか設定する必要があります。 これで、Fabric ピア・クライアントを使用してピアを操作する準備ができました。

1. [CLI](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) がインストールされて構成されていることを確認します。

2. Fabric config パスを、[Fabric ツール・バイナリーのダウンロード](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-fabric-client)時に作成した、この config ディレクトリーに設定します。

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    この変数を設定すると、任意のディレクトリーでピア・クライアントを使用してコマンドを実行できるようになります。

3. 順序付けプログラムのエンドポイント情報が必要です。 順序付けプログラムの管理者である場合は、手順に従って[順序付けプログラムのエンドポイント情報を取得](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)できます。 順序付けプログラムが別の組織によって管理されている場合は、アウト・オブ・バンド操作で順序付けプログラムの URL を提供してもらう必要があります。

4. [ピアのエンドポイント情報を取得します](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-peer-endpoint)。 この URL を使用して、`PEERADDR` 環境変数を設定します。 先頭の `http://` は省略する必要があります。

5. 以下のコマンドを実行して、環境変数を設定します。

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  フィールドを独自の情報に置き換えます。
    - `<CHANNEL_NAME>` を、ピアが参加するチャネルの名前に置き換えます。
    - `<CC_NAME>` を、チェーンコードを参照するための任意の名前に置き換えます。
    - `<PEERADDR>` を、前のステップからのピアのエンドポイント・ホスト名およびポートに置き換えます。
    - `<ORDERER_URL>` を、共同事業体の順序付けプログラムのホスト名およびポートに置き換えます。
    - `<PATH_TO_ADMIN_MSP>` を、ピア管理者の MSP フォルダーへのパスに置き換えます。
    - `<MSPID_OF_PEER_BEING_USED>` を、ジェネシス・ブロックのフェッチ、チャネルへの参加、またはチェーンコードのインストールに使用しているピアの組織 MSPID に置き換えます。 この値は、Helm チャートのデプロイメント時に指定されたものです。

  例えば、次のようにします。

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  また、次のコマンドを発行して、TLS を有効にすることが必要になる場合もあります。

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### CLI を使用したチャネルへのピアの参加
{: #icp-peer-operate-cli-join-channel}

チャネルをピアと結合するには、組織がチャネルのメンバーであることが必要です。 チャネルのメンバーでない場合は、手順に従って[新しいチャネルを作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel)できます。

1. [CLI で環境変数](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-environment-variables)が設定されていることを確認します。

2. チャネルのジェネシス・ブロックをフェッチして、ピア上にチャネル台帳を作成します。 `CORE_PEER_TLS_ROOTCERT_FILE` を順序付けプログラムの TLS 証明書のパスに設定し、以下のコマンドを実行します。

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  これが成功すると、以下のような結果が表示されます。
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **注:** これらのいずれかの CLI コマンドを実行すると、以下の警告が表示される場合がありますが、無視しても安全です。

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```
  {:codeblock}

  以下のコマンドを実行して、ジェネシス・ブロックがピア・コンテナーに正常に追加されたことを確認します。

  ```
  ls *.block
  ```
  {:codeblock}

  以下の例のように表示された場合、ジェネシス・ブロックは正常に追加されています。

  ```
  defaultchannel_0.block
  ```

3. チャネルのジェネシス・ブロックをフェッチした後、ピアをチャネルに参加させることができます。  `CORE_PEER_TLS_ROOTCERT_FILE` をピアの TLS 証明書のパスに設定し、以下のコマンドを使用してチャネルに参加します。

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  これが正常に機能した場合、以下の例のような情報が表示されます。

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### CLI を使用したピアへのチェーンコードのインストール
{: #icp-peer-operate-toolcontainer-install-cc}

これで、ピアにチェーンコードをインストールおよびインスタンス化する準備ができました。 これらの手順のために、`fabcar` チェーンコードを `fabric-samples` リポジトリーからインストールします。 [GOPATH の構成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Set your GOPATH") が完了していることを確認してから、次のコマンドを使用して、GitHub から `fabric-samples` をダウンロードします。

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

以下のピア CLI コマンドを実行して、ピアに `fabcar` チェーンコードをインストールします。 `CORE_PEER_TLS_ROOTCERT_FILE` をピアの TLS 証明書のパスに設定します。

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  このコマンドが正常に完了した場合、以下のように表示されます。

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

### CLI を使用したチャネルでのチェーンコードのインスタンス化
{: #icp-peer-operate-toolcontainer-instantiate-cc}

チャネルでチェーンコードをインスタンス化する必要があるのは 1 つのピアのみであるため、ピアを使用してチェーンコードをインスタンス化する必要はありません。 {{site.data.keyword.blockchainfull_notm}} Platform でホストされているピアでこの操作を行うことができます。 ただし、ピアでチェーンコードをチャネルに対してインスタンス化する必要がある場合は、次のコマンドを実行してインスタンス化できます。 `CORE_PEER_TLS_ROOTCERT_FILE` の値をピアの TLS 証明書のパスに設定します。 `--cafile` の値を順序付けプログラムの TLS 証明書に設定します。

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

このコマンドが正常に完了した場合、以下のように表示されます。

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

チェーンコードをインスタンス化した後、チェーンコード照会を使用してコマンドを呼び出し、チャネル台帳に対してデータの読み取りと書き込みを行うことができます。 詳しくは、Hyperledger Fabric の資料で [peer chaincode ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html) コマンドを参照してください。 プロキシー IP および外部順序付けプログラム・ポートを使用して、呼び出しコマンドに順序付けプログラム・エンドポイントを渡す必要があります。 ピア・エンドポイントを照会コマンドに渡すだけで済みます。

## {{site.data.keyword.cloud_notm}} Private でのピアのログの表示
{: #peer-log-icp}

{{site.data.keyword.cloud_notm}} Private コンソールからピアのログにアクセスし、Kibana インターフェースでログを確認できます。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. メニュー・リストから、**「ワークロード」** > **「Helm リリース」**をクリックします。
3. 「Helm リリース」テーブルで、Helm リリースの名前をクリックします。
4. **「ポッド」**セクションまでスクロールダウンして、表の行の末尾にある**「ログの表示」**リンクをクリックします。 ピアのログが Kibana インターフェースで開きます。

## チェーンコードの更新

時間の経過とともに、ピア上で実行されているチェーンコードを変更することが必要になる場合があります。 チェーンコードはピアにインストールされ、チャネルでインスタンス化されるため、チャネル上のすべてのピアでチェーンコードを更新する必要があります。

チェーンコードを更新するには、以下の手順を実行します。

1. 各ピアでチェーンコードを更新するには、クライアント・アプリケーションまたは CLI コマンドを使用して、チェーンコードをピアにインストールする際に使用した処理を再実行するだけです。 必ず、当初使用したものと同じチェーンコード名を指定してください。 ただし、今回は、チェーンコードの `Version` を増分します。 すべてのピアで、同じチェーンコード名とバージョンを使用する必要があります。

2. チャネル内のすべてのピアに新しいチェーンコードをインストールした後、
[peer chaincode upgrade ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html#peer-chaincode-upgrade) コマンドを使用して、新しいチェーンコードを使用するようにチャネルを更新します。

## {{site.data.keyword.cloud_notm}} Private で実行されるピアの再始動
{: #peer-restart}

証明書がピアに追加される場合は常に、ピア・ノードで新しい証明書が認識されるようピア・ノードを再始動する必要があります。

**kubectl** コマンドを使用して、{{site.data.keyword.cloud_notm}} Private で実行されるピアを再始動できます。 このコマンドには、コマンド・パラメーターとして、ピアの**ポッド**とその**コンテナー**の名前が必要です。 kubectl コマンドの使用について詳しくは、[kubectl CLI の構成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure)を参照してください。

1. {{site.data.keyword.cloud_notm}} Private コンソールで、ピア・デプロイメント用の**ポッド**名および**コンテナー**名を検索します。

  1. コンソールの左上隅の「メニュー」アイコンをクリックしてから、**「ワークロード」>「デプロイメント」**をクリックします。
  2. テーブルでピアのリリースを見つけてクリックし、そのピアを開きます。
  3. スクロールダウンして、関連する**ポッド**名を表示します。 **ポッド**名をメモします。
  4. ポッドをクリックして開きます。
  5. **「コンテナー」**タブをクリックします。 ピアの**コンテナー**名をメモします。

2. コマンド・ラインから、以下のコマンドを実行してピア再始動します。このとき、`<PEER_POD_NAME>` および `<PEER_CONTAINER_NAME>` を上記からの値に置き換えます。

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. `kubectl get pods` コマンドを実行し、出力でポッドの **RESTART** 数を調べて、ピアが再始動されたことを確認できます。

## チャネルの作成
{: #icp-peer-operate-create-channel}

組織がまだ共同事業体またはチャネルのメンバーでない場合は、以下のステップを使用してチャネルを作成できます。 これらのステップを使用して、既存のチャネルを更新することもできます。 この手順では、[組織定義を準備する](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)方法について説明します。 この定義は、ユーザーを順序付けプログラムのシステム・チャネルに追加して[共同事業体のメンバー](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-add-organizations-to-consortium)にする際に使用されます。 その後、[チャネル・トランザクションを作成](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx)することで、新しいチャネルを形成できます。

既に共同事業体に参加している場合は、チャネル・トランザクションを準備するステップのみを完了する必要があります。 共同事業体の別のメンバーも、組織をメンバーとして新しいチャネルを形成することができます。 新しいアンカー・ピアの定義、新しい管理者証明書の追加、暗号情報の更新など、共同事業体に更新を送信する必要がある場合は、組織定義を更新できます。

### configtxgen ツールのダウンロード
{: #icp-peer-operate-configtxgen}

組織が共同事業体またはチャネルに参加する必要がある場合は、[configtxgen ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/configtxgen.html "configtxgen") ツールをダウンロードする必要があります。

configtxgen を入手する最も簡単な方法は、すべての Fabric ツール・バイナリーを Hyperledger プロジェクトからダウンロードすることです。 コマンド・ラインでバイナリーをダウンロードするディレクトリーにナビゲートし、以下のコマンドを発行して、これらのバイナリーをフェッチします。 最初に [Curl ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl") をインストールする必要があります。

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
```
{:codeblock}

このコマンドは、`bin/` ディレクトリーに configtxgen をインストールします。 `PATH` パスを、Fabric ツール・バイナリーをダウンロードしたディレクトリーに設定します。

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

例えば、ホーム・ディレクトリーにバイナリーをインストールした場合、`PATH` を以下のように設定します。

```
export PATH=$PATH:$HOME/bin
```

## 組織定義の準備
{: #icp-peer-operate-organization-definition}

コンポーネントをデプロイすると、組織は、組織定義ファイルを準備することにより共同事業体に参加できます。 この定義には、新規チャネルの作成、新規チャネルへの参加、チャネルへのピアの追加、チェーンコードのインスタンス化など、共同事業体ガバナンスに参加するのに必要なすべての情報が含まれます。 また、この定義には、組織名、MSPID 証明書、および MSP 証明書が含まれます。 すべての組織がこのステップを実行する必要があります。

### 暗号情報の準備
{: #icp-peer-operate-prepare-crypto}

組織定義を準備する前に、[ピアの管理者](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin)に登録してエンロールする必要があります。 ピア管理者の MSP フォルダーを作成したディレクトリーにナビゲートします。 この例のステップでは、`$HOME/fabric-ca-client/peer-admin/msp` にこのフォルダーを作成しました。 `configtxgen` ツールで MSP を使用するには、このフォルダー内でいくつかの追加ステップを実行する必要があります。

1. TLS 証明書を CA からコピーし、`tlscacerts` という名前の新規フォルダーに配置します。

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```
  {:codeblock}
  次に、作成した `tlscacerts` ディレクトリーにこの証明書をコピーする必要があります。

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/msp/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```
  {:codeblock}
  コマンドは以下のようになります。

  ```
  cp fabric-ca-client/tlsca-admin/msp/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. 署名付き証明書を `signcerts` フォルダーから `admincerts` という名前の新規フォルダーに移動します。

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

`configtxgen` ツールにより、configtx.yaml ファイルを使用して定義が作成されます。 このファイルのサンプル・バージョンは、以下のとおりです。

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

このファイルには、共同事業体内の組織を定義する情報が含まれます。 このファイルより複雑なバージョンは、[ダウンロードした Fabric ピア・クライアント](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-fabric-client)の、`/config` フォルダーにもあります。 このファイルを編集することも、上記のサンプルに置き換えることもできます。 この `/config` フォルダーの場所をメモして、以下の `FABRIC_CFG_PATH` の値を設定します。 このファイルの `Organizations` セクションを編集し、以下の値を設定します。

- `Name:` には、組織に使用する任意の名前を指定できます。

- `ID:` は MSPID の値です。これは、組織の固有 ID となります。 この Organization MSP 値は、ピアの Helm チャートのデプロイ時に指定されたものです。 これは、`<peer pod name>` をピアのポッドの値に置き換えて以下のコマンドを実行することで、ピア・コンテナー内部でも表示されるようになります。

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  出力は以下のようになります。
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  したがって、MSP `ID` は org1 です。

- `MSPDir:` は、管理者の MSP への完全修飾パスです (例: `/Users/chandra/fabric-ca-client/peer-admin/msp`)。

- `AnchorPeers:` フィールドは、ゴシップによる組織間通信のためのリード・ピアとして組織が選択したピアです。 これは、プライベート・データやサービス・ディスカバリーなどの機能に使用されます。ただし、プライベート・データおよびサービス・ディスカバリーは、ピアの Helm チャートでは現在サポートされていません (それでも、このフィールドは引き続き必要です)。 `Host` 値と `Port` 値は、「{{site.data.keyword.cloud_notm}} Private ピア Helm リリース ({{site.data.keyword.cloud_notm}} Private peer Helm Release)」ページの「メモ」セクションの `#1. Get the application URL by running these commands` で入手できます。 出力は以下のようになります。

```
http://9.30.250.70:30481
```

ここで、`Host:` は `9.30.250.70` で `Port:` は `30481` です。

次に、以下のコマンドを使用して、組織定義を出力します。

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

呼び出しは以下のコマンド例のようになります。

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

このコマンドが正常に実行されると、`configtxgen` は組織定義を JSON 形式で出力します。 共同事業体に参加するには、このファイルを順序付けプログラム組織にアウト・オブ・バンド操作で送信する必要があります。 その後、順序付けプログラム組織は、システム・チャネルに定義を追加することによって、[共同事業体を形成したり、既存の共同事業体に追加してもらったり](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium)することができるようになります。これにより、ユーザーは、新規チャネルを作成したり、他の共同事業体メンバーによってチャネルに追加してもらったりすることができるようになります。

## チャネル・トランザクションの作成
{: #icp-peer-operate-channeltx}

新規チャネルを作成する前に、組織は[組織定義](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)を準備し、共同事業体のメンバーとなる必要があります。 [共同事業体を形成するか、共同事業体に追加してもらう](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium)必要がある場合は、以下の手順に従います。 組織が既にシステム・チャネルに追加されている場合は、共同事業体のメンバーを簡単に新規チャネルに追加することもできます。 システム・チャネルのメンバーでない組織は、[チャネル更新要求 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channel_update_tutorial.html) を使用してその組織定義をチャネルに追加することで、手動でのみチャネルに参加できます。 これらのステップを使用して、既存のチャネルを更新することもできます。

### 新規チャネルの形成
{: #icp-peer-operate-form-channel}

新規チャネルを形成するには、組織は [configtxgen ツール ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/configtxgen.html "configtxgen") を使用して、チャネル作成トランザクション・プロポーザルを作成する必要があります。 このツールでは、新規チャネル・メンバーを定義する `configtx.yaml` ファイルを使用します。 サンプルの `configtx.yaml` ファイルを以下に示します。 このファイルより複雑なバージョンは、[ダウンロードした Fabric ピア・クライアント](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-fabric-client)の、`/config` フォルダーにもあります。 このファイルを編集することも、サンプルに置き換えることもできます。 この `/config` フォルダーの場所をメモして、以下の `FABRIC_CFG_PATH` の値を設定します。
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

新規チャネルの作成に関連する 3 つのセクションは、**Organizations**、**Capabilities**、および **Profiles** です。

- **Organizations:** このセクションでは、共同事業体のすべてのメンバーを定義します。 各組織には、`&Org1` などのアンカーが含まれます。 このアンカーの下に、組織の名前、MSPID、MSP フォルダーへのディレクトリー、および相互ピア・ゴシップのための組織のアンカー・ピアがあります。 以下のステップに従って、各共同事業体メンバーの組織プロファイルに入力できます。
  1. 組織の `Name` と `ID` をその MSPID を使用して指定します。 チャネルを作成する組織は、ピアの Helm チャートのデプロイ時に指定された MSPID を認識している必要があります。
  2. `MSPDir` では、[組織定義](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)の作成に使用した MSP フォルダーへの完全修飾パスを指定します。 チャネル作成トランザクションでは、暗号化情報は一切使用されないことに注意してください。 *configtxgen* ツールでは、 MSP の実際の内容は無視されます。 ただし、MSP フォルダーが適切なフォルダー副構造とともにその場所に存在する必要があります。
  3. `AnchorPeers` パラメーターは、各組織がゴシップによる組織間通信に使用するリード・ピアを識別するために使用されます。 この組織の[アンカー・ピア ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/glossary.html) として機能するピアのホスト名およびポートを指定します。 この値は、プライベート・データやサービス・ディスカバリーなどの機能を使用する際に重要です。ただし、プライベート・データおよびサービス・ディスカバリーは、ピアの Helm チャートでは現在サポートされていません。

- **Capabilities:** このセクションは `configtx.yaml` に含まれている必要がありますが、変更する必要はありません。

- **Profiles:** セクション。 このセクションには、新規チャネルの作成に必要な情報が含まれます。 このプロファイルには以下の情報が含まれています。
  1. 新規チャネルの名前。
  2. システム・チャネル内で定義されている共同事業体の名前。チャネルの作成に使用されます。
  3. チャネルに追加される組織のリスト。 リストされている組織名では、`Organizations` セクションにリストされたアンカー・ピアを使用して、リストされている組織の MSPID および関連付けられるアンカー・ピアを検索します。

  *configtxgen* ツールでは、この `Profiles` セクションを使用して、チャネル作成プロポーザルを作成します。

構成ファイルを更新した後、以下のコマンドを使用して、チャネル更新トランザクション・プロポーザルを生成します。

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

実際のコマンドは、以下の例のようになります。
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


成功すると、以下の出力のような結果が表示されます。 このコマンドは、チャネル更新トランザクション・プロポーザルを `FABRIC_CFG_PATH` ディレクトリーに書き込みます。

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

エンドースメント・ポリシーで共同事業体の他のメンバーからの追加署名が必要でない場合、プロポーザルを順序付けプログラムに直接送信して新規チャネルを形成できます。 チャネル更新トランザクションを生成したのと同じ場所から以下のコマンドを実行して、チャネルを作成します。

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

ここで、`<ORG_MSPID>` は組織の MSPID であり、`<PATH_TO_ADMIN_MSP>` はピア管理者の MSP フォルダーへのパスです。 コマンド内で、`<orderer_url>` はチャネルを作成する順序付けプログラムの URL であり、`<orderer_tls_cert>` は順序付けプログラムの TLS 証明書へのパスです。 実際のコマンドは、以下の例のようになります。
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## プロポーザルの承認および要求の送信
{: #icp-peer-operate-endorse-proposal}

新規チャネル作成プロポーザルが、システム・チャネルのエンドースメント・ポリシーを満たすのに十分な組織によって署名されている場合、チャネル更新トランザクションを順序付けプログラムに送信して、新規チャネルを形成できます。

Node Fabric SDK API を使用して、単一トランザクションで署名と送信を実行できます。 詳しくは、Node SDK の資料で [How to create a Hyperledger Fabric Channel ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")]](https://fabric-sdk-node.github.io/release-1.4/tutorial-channel-create.html) チュートリアルを参照してください。

## ピア・ログの表示
{: #icp-peer-operate-view-logs}

コンポーネント・ログは、コマンド・ラインから [`kubectl CLI コマンド`](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure)を使用して表示することも、[Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Your Window into the Elastic Stack") ({{site.data.keyword.cloud_notm}} Private クラスターに含まれています) を使用して表示することもできます。

- `kubectl logs` コマンドを使用して、ポッド内のコンテナー・ログを表示します。 ポッド名が不明な場合は、以下のコマンドを実行してポッドのリストを表示します。

  ```
  kubectl get pods
  ```
  {:codeblock}

  次に、`<pod_name>` を上記のコマンド出力からのポッド名に置き換えて次のコマンドを実行し、ポッド内にあるピア・コンテナーのログを取得します。

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  `kubectl logs` コマンドについて詳しくは、[Kubernetes の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”) を参照してください。

- また、[{{site.data.keyword.cloud_notm}} Private クラスター管理コンソール ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/troubleshoot/events.html "イベントおよびログ") を使用してログにアクセスすることもできます。この場合、ログは Kibana で開きます。

  **注:** Kibana でログを表示する場合、`「No results found」`という応答を受け取ることがあります。 この状態は、{{site.data.keyword.cloud_notm}} Private がワーカー・ノードの IP アドレスをそのホスト名として使用する場合に起こることがあります。 この問題を解決するには、パネルの上部で `node.hostname.keyword` で始まるフィルターを削除すると、ログが表示されるようになります。


## トラブルシューティング
{: #icp-peer-operate-troubleshooting}

### **問題:** ピア・コンテナーにログインする際のエラー
{: #icp-peer-operate-bash-error}

この問題は、以下のコマンドを実行して、kubectl ツールを使用してピア内でコマンドを実行したときに発生します。

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}

失敗すると、以下の例のようなエラー・メッセージが生成されます。

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**解決策:**

このエラーは、ビッグ・エンディアン・システム (IBM System Z など) で、2 つの Docker bash セッションが同じ Docker コンテナー内で開いている場合に発生します。 2 つ目の bash セッションは、実行中のコンテナーに接続できない場合があります。 この問題を解決するには、[ピア・コンテナーを再始動](/docs/services/blockchain/howto/peer_operate_icp.html#peer-restart)して、ピアの再始動後に `kubectl exec` コマンドを再試行します。

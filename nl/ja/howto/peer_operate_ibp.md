---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
# スターター・プランまたはエンタープライズ・プランを使用した {{site.data.keyword.cloud_notm}} Private でのピアの操作
{: #ibp-peer-operate}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private ピアをセットアップした後、ピアでスターター・プランまたはエンタープライズ・プランのネットワークにトランザクションを送信できるようにするには、いくつかの操作ステップを実行する必要があります。 このステップでは、チャネルへの組織の追加、チャネルへのピアの参加、ピアでのチェーンコードのインストール、チャネルでのチェーンコードのインスタンス化、およびピアへのアプリケーションの接続を行います。
{:shortdesc}

ピアを操作するには、{{site.data.keyword.cloud_notm}} Private クラスターからいくつかの前提条件ステップを実行する必要があります。

**前提条件:**
- [CLI の構成](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)
- [ピアのエンドポイント情報の取得](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)
- [ピアの TLS 証明書のダウンロード](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)

次に、以下のいずれかの方法を使用してピアを操作します。

**操作パス:**
- [Fabric SDK の使用](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-operate-with-sdk)
- [コマンド・ラインの使用](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-cli-operate)

推奨されるパスは Fabric SDK ですが、この手順では、SDK の操作に精通していることが前提となっています。 コマンド・ラインを使用する場合は、Fabric ピア・クライアントを使用できます。

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

**注**: {{site.data.keyword.blockchainfull_notm}} Platform ピアには、{{site.data.keyword.blockchainfull_notm}} Platform でホストされるピアのすべての機能およびサポートへのアクセス権限があるわけではありません。 結果として、ネットワーク・モニターを使用して {{site.data.keyword.cloud_notm}} Private 上のピアを操作することはできません。 ピアの実行を開始する前に、必ず[考慮事項と制限](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)を確認してください。

## 前提条件
{: #ibp-peer-operate-prerequisites}

SDK またはコマンド・ラインのどちらを使用する予定であるかにかかわらず、ピアを操作する過程でこのセクションのステップを実行する必要があります。 これらすべてのステップを完了すると、開始できるようになります。

### CLI の構成
{: #ibp-peer-operate-kubectl-configure}

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
{: #ibp-peer-operate-endpoint}

チャネルに参加したり、スマート・コントラクトをインストールしたりするには、SDK または Fabric CA クライアントからピア・エンドポイントをターゲットにする必要があります。 ピアのエンドポイントを見つけるには、{{site.data.keyword.cloud_notm}} Private コンソール UI を使用します。 以下のステップを実行するには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。 **「メモ」**セクションには、ピア・デプロイメントの操作に役立つ一連のコマンドが表示されます。
5. [kubectl CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) をまだ構成していない場合には、ステップに従って構成します。 kubectl を使用してピア・コンテナーと対話します。
6. CLI で、メモの**「1. Get the application URL by running these commands:」**に続く最初のコマンドを実行します。このコマンドにより、次の例のようなピアの URL とポートが出力されます。 今後のコマンドのために、この URL を保存します。

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**注:** ファイアウォールの内側にピアをデプロイする場合は、パススルーを開き、プラットフォーム上のネットワークでピアとの TLS ハンドシェークを実行できるようにする必要があります。 ピアのポート 7051 にバインドされたノード・ポートのパススルーのみを有効にする必要があります。 詳しくは、[ピアのエンドポイント情報の検索](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)を参照してください。

### ピアの TLS 証明書のダウンロード
{: #ibp-peer-operate-tls-cert}

リモート・クライアントからピアと通信するには、ピアの TLS 証明書をダウンロードしてコマンドに渡す必要があります。

1. {{site.data.keyword.cloud_notm}} Private コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。 **「メモ」**セクションには、ピア・デプロイメントの操作に役立つ一連のコマンドが表示されます。
5. [kubectl CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) をまだ構成していない場合には、ステップに従って構成します。 kubectl を使用してピア・コンテナーと対話します。
6. CLI で、メモの**「3. Get peer TLS root cert file」**に続く最初のコマンドを実行します。 このコマンドは、TLS 証明書を `cacert.pem` ファイルとしてローカル・マシン上に保存します。
7. この証明書を今後のコマンドで参照できる場所に移動し、その名前を `peertls.pem` に変更します。

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Fabric SDK を使用したピアの操作
{: #ibp-peer-operate-operate-with-sdk}

Hyperledger Fabric SDK には強力な API のセットが用意されており、これらの API により、アプリケーションはブロックチェーン・ネットワークと対話したりこれを操作したりすることができます。 サポートされている言語の最新リストおよび Fabric SDK 内で使用可能な API の完全なリストについては、[Hyperledger Fabric SDK コミュニティーの資料 ![外部リンク・アイコン](../images/external_link.svg " 外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK コミュニティーの資料") を参照してください。 Fabric SDK を使用して、ピアを {{site.data.keyword.blockchainfull_notm}} Platform 上のチャネルに参加させたり、チェーンコードをピアにインストールしたり、チェーンコードをチャネル上でインスタンス化したりできます。

以下の手順では、[Fabric Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Fabric Node SDK") を使用してピアを操作するため、事前に SDK に精通していることが前提となっています。 開始前に Node SDK の使用方法を学習するために、およびチェーンコードを呼び出して照会する準備ができたら、ピアを使用してアプリケーションを開発する際のガイドとして、[アプリケーションの開発チュートリアル](/docs/services/blockchain/v10_application.html#dev-app)を使用できます。

### Node SDK のインストール
{: #ibp-peer-operate-install-sdk}

NPM を使用して [Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Node SDK") をインストールできます。

```
npm install fabric-client@1.2
```
{:codeblock}

バージョン 1.2 の Node SDK を使用することをお勧めします。

### ピアを使用するための SDK の準備
{: #ibp-peer-operate-prepare-node-sdk}

ピアは、ピア管理者の署名付き証明書を組み込むようにデプロイされます。 これにより、ピア管理者の証明書および MSP フォルダーを使用してピアを操作できるようになります。

[ピア管理者の登録](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin)時に作成した証明書を見つけます。 コマンド例を使用した場合、ピア管理者の MSP フォルダーは `$HOME/fabric-ca-client/peer-admin` にあります。
  - MSP フォルダー内の署名付き証明書 (公開鍵) および秘密鍵を使用して、SDK でピア管理者のユーザー・コンテキストを作成できます。 これらのキーは、以下の場所にあります。
    - 署名付き証明書は、**signcerts** フォルダー (`$HOME/fabric-ca-client/peer-admin/msp/signcerts`) 内にあります。
    - 秘密鍵は、**keystore:** フォルダー (`$HOME/fabric-ca-client/peer-admin/msp/keystore`) 内にあります。
    公開鍵と秘密鍵のみを使用し、ユーザー・コンテキストを作成して SDK を操作する方法の例については、[アプリケーションの開発チュートリアルのこのセクション](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel)を参照してください。

また、SDK で、スターター・プランまたはエンタープライズ・プランでの CA のエンドポイント情報および [ピア管理者のユーザー名とパスワード](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)を使用して、ピア管理者の署名付き証明書および秘密鍵を生成することもできます。

### ピア管理者の署名付き証明書をスターター・プランまたはエンタープライズ・プランにアップロード
{: #ibp-peer-operate-upload-sdk}

アプリケーションにネットワークを操作する権限を付与するには、ピア管理者の署名付き証明書を {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークにアップロードする必要があります。

- 署名付き証明書は、SDK によって暗号情報が生成されたディレクトリー内のピア管理者の名前が付けられたファイル内にあります。 `-----BEGIN CERTIFICATE-----` で始まり `-----END CERTIFICATE-----` で終わる `certificate` フィールドの後の引用符内で囲まれた証明書をコピーします。 CLI を使用してコマンド `echo -e "<CERT>" > admin.pem` を発行し、証明書を PEM 形式に変換できます。 次に、ネットワーク・モニターを使用して、証明書の内容をブロックチェーン・ネットワークに貼り付けることができます。 {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークにログインします。 ネットワーク・モニターの「メンバー」画面で、**「証明書」** > **「証明書の追加」**をクリックします。 証明書に任意の名前を指定して、内容を**「証明書」**フィールドに貼り付けます。 ピアを再始動するかどうかを尋ねられたら、**「再始動」**をクリックします。 このアクションは、組織がチャネルに参加する前に実行する必要があります。

### SDK へのピアの TLS 証明書の引き渡し
{: #ibp-peer-operate-download-tlscert}

SDK との通信を認証するには、ピアの TLS 証明書を参照する必要があります。 [ピア・コンテナーからダウンロードした](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) TLS 証明書を見つけて、アプリケーションから参照できる場所に保存します。コマンド例を使用した場合、ピアの TLS 証明書は `$HOME/fabric-ca-client/peer-tls/peertls.pem` にあります。 次に、単純なファイル読み取りコマンドを使用して、TLS 証明書をアプリケーションにインポートできます。

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### SDK へのピアのエンドポイント情報の提供
{: #ibp-peer-operate-SDK-endpoints}

[ピアのエンドポイント情報](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)を検索し、新しいピア変数を宣言するか、接続プロファイルを更新して、SDK にこの情報を提供します。 以下の例では、Fabric ネットワーク上のエンドポイントとしてピアを定義し、インポートした TLS 証明書をピアに渡します。

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**注:** ピアは {{site.data.keyword.cloud_notm}} の外部にあるため、{{site.data.keyword.blockchainfull_notm}} Platform ネットワーク上のその他の組織は、ピアのエンドポイント情報を接続プロファイル内で見つけることができません。その他の組織がトランザクションを承認のためにピアに送信する必要がある場合は、ピア URL をアウト・オブ・バンド操作で指定する必要があります。

### SDK を使用したチャネルへの参加
{: #ibp-peer-operate-peer-join-channel-sdk}

チャネルをピアと結合するには、組織がチャネルのメンバーであることが必要です。

  - ピアに対して新しいチャネルを開始できます。 チャネル・イニシエーターである場合は、[チャネル作成](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel)時に組織を自動的に含めることができます。

  - ブロックチェーン・ネットワークの別のメンバーが、[チャネル更新](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)を使用して、組織を既存のチャネルに追加することもできます。

    組織がチャネルに追加されたら、他のメンバーがトランザクション中にデジタル署名を検証できるように、ピアの署名付き証明書をチャネルに追加する必要があります。 ピアではインストール時にその署名付き証明書がアップロードされるため、証明書とチャネルを同期するだけで済みます。 ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。 このアクションによって、チャネルのすべてのピアで証明書が同期されます。 `join channel` コマンドを発行する前にチャネル同期が完了できるように、数分待機することが必要になる場合があります。

    **注:** {{site.data.keyword.cloud_notm}} Private に追加してスターター・プランまたはエンタープライズ・プランに接続するピアが、ネットワーク・モニターで追加されたピアと同じ組織に含まれている場合、ネットワーク・モニターの「チャネル」画面では、チャネルに追加される新規ブロック、インスタンス化されるチェーンコード、およびその他のチャネル更新のみを表示できます。 これは、ネットワーク・モニターでは「チャネル」画面でピアからの情報を収集しますが、{{site.data.keyword.cloud_notm}} 外部のピアを表示することはできないためです。 いずれのピアもプライベート・データ機能を使用していない場合、ネットワーク・モニター内の情報は、組織内のピア間で同じものになります。

組織がチャネルのメンバーになったら、SDK を使用して、[ピアをチャネルに参加させる](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk)手順に従います。 順序付けサービスの URL とチャネル名を指定する必要があります。

### SDK を使用したピアへのチェーンコードのインストール
{: #ibp-peer-operate-install-cc-sdk}

以下の手順に従って、SDK を使用してピアに[チェーンコードをインストール](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk)します。

### SDK を使用したチャネルでのチェーンコードのインスタンス化
{: #ibp-peer-operate-instantiate-cc-sdk}

チェーンコードをインスタンス化または更新する必要があるのは、チャネルのいずれかのメンバーのみです。 したがって、{{site.data.keyword.blockchainfull_notm}} Platform 上にピアがあるチャネルのどのネットワーク・メンバーも、ネットワーク・モニターを使用してチェーンコードをインスタンス化し、エンドースメント・ポリシーを指定できます。 ただし、ピアを使用してチャネルでチェーンコードをインスタンス化する必要がある場合は、SDK を使用し、手順に従って[チェーンコードをインスタンス化](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk)できます。

## CLI を使用したピアの操作
{: #ibp-peer-operate-cli-operate}

また、Fabric `peer` クライアントを使用して、コマンド・ラインからピアを操作することもできます。

ピアはピア管理者の署名付き証明書を組み込むようにデプロイされており、これにより、その ID を使用してピアを操作できます。 以下の手順では、ピアをチャネルに参加させたり、ピア上にチェーンコードをインストールしてからチャネル上でチェーンコードをインスタンス化したりするために、[ピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)時に生成されたピア管理者の MSP フォルダーを使用します。

### Fabric ピア・クライアントのダウンロード
{: #ibp-peer-operate-download-fabric-client}

ピア・クライアントを入手するには、Hyperledger からすべての Fabric ツール・バイナリーをダウンロードする方法が最も簡単です。 コマンド・ラインでバイナリーをダウンロードするディレクトリーにナビゲートし、次のコマンドを発行して、これらのバイナリーをフェッチします。 [Curl ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") をインストールしていない場合は、最初にインストールする必要があります。

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
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

バイナリーをダウンロードすると、core.yaml ファイル、orderer.yaml ファイル、および configtx.yaml ファイルを含む `config` フォルダーが作成されます。 Fabric config パスをこの `config` ディレクトリーに設定します。

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
{: #manage-certs}

ピア管理者の MSP フォルダーが生成されるディレクトリーに切り替えます。 この資料のステップ例に従う場合、MSP フォルダーは次のようなディレクトリーにあります。

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

ピアを操作するには、ローカル・マシン上で証明書の一部の管理を行う必要があります。 例えば、ピア管理者の署名付き証明書をスターター・プランまたはエンタープライズ・プランにアップロードし、{{site.data.keyword.cloud_notm}}上のピアおよびネットワークから TLS 証明書にアクセスできるようにする必要があります。 使用する証明書および実行するタスクについて詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理](/docs/services/blockchain/certificates.html#managing-certificates)を参照してください。

1. ピア管理者の署名付き証明書を `admincerts` という名前の新規フォルダーに移動します。

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. この署名付き証明書を {{site.data.keyword.cloud_notm}} 上のネットワークにアップロードして、リモートの CLI またはアプリケーションでチャネル操作 (チャネルのジェネシス・ブロックをフェッチしたりチェーンコードをインスタンス化したりするなど) を実行できるようにします。
    1. ローカル・マシンで、ファイル `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` を開き、クリップボードにコピーします。
    2. {{site.data.keyword.blockchainfull_notm}} Platform で、ネットワークのネットワーク・モニターを実行します。
    3. 左側のナビゲーターで**「メンバー」**をクリックし、**「証明書」**タブをクリックします。
    4. **「証明書の追加」**ボタンをクリックします。
    5. **「証明書の追加」**ウィンドウで、証明書に「peer-admin」などの名前を付け、クリップボードにコピーした証明書を貼り付けて**「送信」**をクリックします。
    6. ピアを再始動する必要があるかどうかを尋ねられます。 **「再始動」**をクリックします。
    7. 左側のナビゲーターで**「チャネル」**をクリックして、ピアが参加するチャネルを見つけます。
    8. **「アクション」**ヘッダーの下の 3 点リーダーをクリックし、**「証明書の同期」**をクリックします。 **「証明書の同期」**ウィンドウで、**「送信」**をクリックします。

    **注**: ピアがチャネルに参加するか、チャネル上でチェーンコードをインスタンス化する前に、チャネル証明書を同期することが重要です。 join channel コマンドまたは instantiate chaincode コマンドを発行する前にチャネル同期が完了できるように、数分待機することが必要になる場合があります。

3. [ピアの TLS 証明書をダウンロードし](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)、コマンド・ラインからこの証明書を参照できることを確認します。 コマンド例に従った場合、この TLS 証明書は `$HOME/fabric-ca-client/peer-tls/peertls.pem` ファイルにあります。

4. また、[ピア管理者の登録](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin)時にスターター・プランまたはエンタープライズ・プランの CA との通信に使用した TLS 証明書を参照する必要もあります。 この資料のコマンド例に従った場合、TLS 証明書は `$HOME/fabric-ca-client/tls-ibp/tls.pem` ファイルにあります。

tree コマンドを実行して、これらのステップを完了したことを検証できます。 証明書を格納したディレクトリーにナビゲートします。 tree コマンドを実行すると、以下の構造に似た結果が生成されます。
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
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
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CLI 環境変数の設定
{: #ibp-peer-operate-environment-variables}

すべての証明書を必要な場所に移動した後、コマンドで使用する環境変数をいくつか設定する必要があります。 これで、Fabric ピア・クライアントを使用してピアを操作する準備ができました。

1. ネットワーク・モニターの**「概要」**画面にある**接続プロファイル**・ファイルから、スターター・プランまたはエンタープライズ・プランの構成情報を取得します。 **「接続プロファイル」**、**「ダウンロード」**の順にクリックします。

  - `orderers > url` の下に配置されている **orderers** を検索して、順序付けプログラムの URL を見つけます。 ネットワーク名で始まる URL をメモしておきます。 URL は以下の例のようになります。

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - **organizations** を検索して、組織の名前を見つけます。 この組織はピアの登録に使用した組織と同じである必要があります。 組織の名前をその関連 `mspid` とともに見つけることができます。 `mspid` の値をメモしておきます。

2. [ピアのエンドポイント情報を検索します](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)。 ピア・エンドポイントを使用して、`PEERADDR` 環境変数を設定する必要があります。 必ず、冒頭の `http://` を除外してください。

3. 以下のコマンドを実行して、環境変数を設定します。

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  フィールドを独自の情報に置き換えます。
    - `<full_path_to_config_folder>` を、[ピア・クライアントのダウンロード](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-download-fabric-client)時に作成された構成フォルダーに置き換えます。
    - `<CHANNEL_NAME>` を、ピアが参加するチャネルの名前に置き換えます。
    - `<CC_NAME>` を、チェーンコードを参照するための任意の名前に置き換えます。
    - `<PEERADDR>` を、前のステップで取得した現在使用しているピアのピア・エンドポイントに置き換えます。
    - `<ORDERER_URL>` を、接続プロファイルからの順序付けプログラムのホスト名とポートに置き換えます。
    - `<PATH_TO_ADMIN_MSP>` を、ピア管理者の MSP フォルダーへのパスに置き換えます。
    - `<PATH_TO_PEER_TLS_CERT>` を、ダウンロードしたピアの TLS 証明書へのパスに置き換えます。
    - `<MSPID_OF_PEER_BEING_USED>` を、ジェネシス・ブロックのフェッチ、チャネルへの参加、またはチェーンコードのインストールに使用しているピアの組織 MSPID に置き換えます。

  例えば、次のようにします。

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  また、次のコマンドを発行して、TLS を有効にすることが必要になる場合もあります。

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### CLI を使用したチャネルへのピアの参加
{: #ibp-peer-operate-cli-join-channel}

CLI コマンドを実行してピアをチャネルに参加させる前に、次のいずれかの方法で、組織をネットワーク内のチャネルに追加する必要があります。

  - ピアに対して新しいチャネルを開始できます。 チャネル・イニシエーターは、[チャネル作成](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel)時に組織を自動的に含めることができます。
  - ブロックチェーン・ネットワークの別のメンバーが、[チャネル更新](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)を使用して、組織を既存のチャネルに追加することもできます。

    組織がチャネルに追加されたら、他のメンバーがトランザクション中にデジタル署名を検証できるように、ピアの署名付き証明書をチャネルに追加する必要があります。 ピアではインストール時にその署名付き証明書がアップロードされるため、証明書とチャネルを同期するだけで済みます。 ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。 このアクションによって、チャネルのすべてのピアで証明書が同期されます。

    **注:** スターター・プランまたはエンタープライズ・プランのネットワークに接続する {{site.data.keyword.cloud_notm}} Private ピアが、ネットワーク・モニターで追加された別のピアと同じ組織に含まれている場合、ネットワーク・モニターの「チャネル」画面では、チャネルに追加される新規ブロック、インスタンス化されるチェーンコード、およびその他のチャネル更新のみを表示できます。 これは、ネットワーク・モニターでは「チャネル」画面でピアからの情報を収集しますが、{{site.data.keyword.cloud_notm}} 外部のピアを表示することはできないためです。 いずれのピアもプライベート・データ機能を使用していない場合、ネットワーク・モニター内の情報は、組織内のピア間で同じものになります。

1. [CLI で環境変数](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-environment-variables)が設定されていることを確認します。

2. チャネルのジェネシス・ブロックをフェッチして、ピア上にチャネル台帳を作成します。 `$HOME/fabric-ca-client/tls-ibp/tls.pem` は、スターター・プランまたはエンタープライズ・プランの TLS 証明書へのパスを表していることに注意してください。 パスが異なる場合は、正しいパスになるよう設定してください。

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
  ```
  {:codeblock}

  **注:** これらのいずれかの CLI コマンドを実行すると、以下の警告が表示される場合がありますが、無視しても安全です。

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  以下のコマンドを実行して、ジェネシス・ブロックがピア・コンテナーに正常に追加されたことを確認します。

  ```
  ls *.block
  ```
  {:codeblock}

  以下の例のように表示された場合、ジェネシス・ブロックは正常に追加されています。

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. チャネルのジェネシス・ブロックをフェッチした後、次のコマンドを使用して、ピアをチャネルに参加させることができます。

  ```
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
{: #ibp-peer-operate-toolcontainer-install-cc}

これで、ピアにチェーンコードをインストールおよびインスタンス化する準備ができました。 これらの手順のために、`fabcar` チェーンコードを `fabric-samples` リポジトリーからインストールします。 [GOPATH の構成 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Set your GOPATH") が完了していることを確認してから、次のコマンドを使用して、GitHub から `fabric-samples` をダウンロードします。

```
cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

以下のピア CLI コマンドを実行して、ピアに `fabcar` チェーンコードをインストールします。

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

このコマンドが正常に完了した場合、以下のように表示されます。

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**注:** このチェーンコードが、スターター・プランまたはエンタープライズ・プランで実行中の別のピア上で既にインストールおよびインスタンス化されている場合は、ピア上にチェーンコードをインストールする前に、追加のステップを実行する必要があります。 このチェーンコードのインストール方法に関連付けられるエラーをどのように回避するかの手順について詳しくは、この[トラブルシューティングに関するトピック](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-troubleshooting)を参照してください。

### CLI を使用したチャネルでのチェーンコードのインスタンス化
{: #ibp-peer-operate-toolcontainer-instantiate-cc}

チャネルでチェーンコードをインスタンス化する必要があるのは 1 つのピアのみであるため、ピアを使用してチェーンコードをインスタンス化する必要はありません。 {{site.data.keyword.blockchainfull_notm}} Platform でホストされているピアでこの操作を行うことができます。 ただし、ピアでチェーンコードをチャネルに対してインスタンス化する必要がある場合は、次のコマンドを実行してインスタンス化できます。 `CORE_PEER_TLS_ROOTCERT_FILE` の値が、スターター・プランまたはエンタープライズ・プランでの CA の TLS 証明書のパスに設定されていることを確認します。

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

このコマンドが正常に完了した場合、以下のように表示されます。

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

チェーンコードをインスタンス化した後、チェーンコード照会を使用してコマンドを呼び出し、チャネル台帳に対してデータの読み取りと書き込みを行うことができます。 詳しくは、Hyperledger Fabric の資料で [peer chaincode ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) コマンドを参照してください。 プロキシー IP および外部順序付けプログラム・ポートを使用して、呼び出しコマンドに順序付けプログラム・エンドポイントを渡す必要があります。 ピア・エンドポイントを照会コマンドに渡すだけで済みます。

## チェーンコードの更新
{: #ibp-peer-operate-update-chaincode}

時間の経過とともに、ピア上で実行されているチェーンコードを変更することが必要になる場合があります。 チェーンコードはピアにインストールされ、チャネルでインスタンス化されるため、チャネル上のすべてのピアでチェーンコードを更新する必要があります。

チェーンコードを更新するには、以下の手順を実行します。

1. 各ピアでチェーンコードを更新するには、クライアント・アプリケーションまたは CLI コマンドを使用して、チェーンコードをピアにインストールする際に使用した処理を再実行するだけです。 必ず、当初使用したものと同じチェーンコード名を指定してください。 ただし、今回は、チェーンコードの `Version` を増分します。 すべてのピアで、同じチェーンコード名とバージョンを使用する必要があります。

2. チャネル内のすべてのピアに新しいチェーンコードをインストールした後、ネットワーク・モニターまたは [peer chaincode upgrade ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) コマンドを使用して、新しいチェーンコードを使用するようにチャネルを更新します。

ネットワーク・モニターの「コードのインストール」パネルを使用してチェーンコードをチャネルで更新する方法について詳しくは、これらの[手順](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc)のステップ 2 を参照してください。

## ピア・ログの表示
{: #ibp-peer-operate-view-logs}

コンポーネント・ログは、コマンド・ラインから [`kubectl` CLI コマンド ](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)を使用して表示することも、[Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Your Window into the Elastic Stack") ({{site.data.keyword.cloud_notm}} Private クラスターに含まれています) を使用して表示することもできます。

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

- または、[{{site.data.keyword.cloud_notm}} Private クラスター管理コンソール](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)を使用してログにアクセスすることもできます。この場合、ログは Kibana で開きます。

   **注:** Kibana でログを表示する場合、`「No results found」`という応答を受け取ることがあります。 この状態は、{{site.data.keyword.cloud_notm}} Private がワーカー・ノードの IP アドレスをそのホスト名として使用する場合に起こることがあります。 この問題を解決するには、パネルの上部で `node.hostname.keyword` で始まるフィルターを削除すると、ログが表示されるようになります。

## トラブルシューティング
{: #ibp-peer-operate-troubleshooting}

### **問題:** 呼び出しコマンドが、ピア上で `chaincode fingerprint mismatch` というエラーで失敗する
{: #ibp-peer-operate
-install-error}

{{site.data.keyword.cloud_notm}} Private で実行中のピアで `peer chaincode invoke` 要求を実行すると、`chaincode fingerprint mismatch` というエラーを受け取る可能性があります。

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

このエラーは、次のいずれかの領域でチェーンコードを実行しているピア間に不整合が存在する場合に発生することがあります。

- チェーンコード名
- チェーンコード・バージョン
- チェーンコード・パス
- チェーンコード・バイナリー

このエラーは、スターター・プランまたはエンタープライズ・プランで実行中のピア上でネットワーク・モニター UI を使用してチェーンコードをインストールおよびインスタンス化した後、{{site.data.keyword.cloud_notm}} Private で実行中のピア上にチェーンコードをインストールすると、発生する可能性があります。 このエラーは、`invoke` 要求時に、生成されるピア上のチェーンコード・パスが異なるために発生します。

**解決策:**
{{site.data.keyword.cloud_notm}} (スターター・プランやエンタープライズ・プランなど) と {{site.data.keyword.cloud_notm}} Private の両方で、チェーンコードをピア上で実行する場合は、チェーンコードのインストールにネットワーク・モニター UI を使用しないでください。 代わりに、[`peer chaincode package `![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) コマンドを使用してチェーンコードをパッケージ化してから、[`peer chaincode install`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-toolcontainer-install-cc) コマンドを実行して、すべてのピア上にこのパッケージをインストールします。

チェーンコードが既にチャネル上でインストールおよびインスタンス化されている場合は、{{site.data.keyword.cloud_notm}} Private ピア上にチェーンコードをインストールしようとする前に、以下のステップを実行して問題を回避する必要があります。

1. [`peer chaincode package `![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) コマンドを使用して、チェーンコードをパッケージ化します。
2. `peer chaincode install` コマンドを実行して、{{site.data.keyword.cloud_notm}} Private で実行中のピア上にチェーンコード・パッケージをインストールします。
3. プラットフォーム固有のバイナリーが存在する場合は、[`peer chaincode upgrade `![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン ")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) コマンドを実行して、チェーンコード・パッケージを使用する、スターター・プランまたはエンタープライズ・プランのピアで実行中のチェーンコードをアップグレードできます。
4. ネットワーク・モニター UI または CLI のいずれかを使用して、チャネル上に新しくインストールされたチェーンコードをインスタンス化します。

チェーンコードをアップグレードするためのプロセスについては、Hyperledger Fabric の資料の [`Chaincode for Operators `![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) も参照してください。

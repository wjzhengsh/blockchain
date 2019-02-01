---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private でのリモート・ピアの操作
{: #remote-peer-operate}


***[このページは参考になりましたか。ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform リモート・ピアを {{site.data.keyword.cloud_notm}} Private (ICP) 上にセットアップした後、ピアがブロックチェーン・ネットワークの台帳を照会または呼び出すためのトランザクションを発行できるようにするには、いくつかの操作ステップを実行する必要があります。このステップでは、チャネルへの組織の追加、チャネルへのリモート・ピアの参加、リモート・ピアでのチェーンコードのインストール、チャネルでのチェーンコードのインスタンス化、およびリモート・ピアへのアプリケーションの接続を行います。
{:shortdesc}

リモート・ピアを操作するには、ICP クラスターからいくつかの前提条件ステップを実行する必要があります。

**前提条件:**
- [リモート・ピアのエンドポイント情報の取得](#remote-peer-retrieve-endpoint-info)
- [kubectl を使用したピア・コンテナーへのログイン](#remote-peer-kubectl-configure)
- [リモート・ピアの再始動](#remote-peer-restart)

次に、以下のいずれかの方法を使用してリモート・ピアを操作します。

**操作パス:**
- [Fabric SDK の使用](#remote-peer-operate-with-sdk)
- [コマンド・ラインの使用](#remote-peer-cli-operate)

推奨されるパスは Fabric SDK ですが、この手順では、SDK の操作に精通していることが前提となっています。

[高可用性](/docs/services/blockchain/howto/remote_peer_icp.html#high-availability)を実現するために、リモート・ピアの Helm チャートのインスタンスを少なくとも 2 つデプロイすることをお勧めします。したがって、ピアごとに 1 回、これらの操作ステップに従う必要があります。アプリケーションからチェーンコードの呼び出しと照会を行う準備ができたら、両方のピアに接続して、[アプリケーションの可用性が高い](/docs/services/blockchain/v10_application.html#ha-app)ことを確認します。

**注**: {{site.data.keyword.blockchainfull_notm}} Platform リモート・ピアには、{{site.data.keyword.blockchainfull_notm}} Platform でホストされるピアの全機能またはサポートへのアクセス権限がありません。結果として、ネットワーク・モニターを使用してリモート・ピアを操作することはできません。リモート・ピアの実行を開始する前に、必ず[考慮事項](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations)を確認してください。

## 前提条件

SDK またはコマンド・ラインのどちらを使用する予定であるかにかかわらず、ピアを操作する過程でこのセクションのステップを実行する必要があります。開始する前に、最初の 2 つのステップであるピアのエンドポイントの取得と kubectl の構成を実行できます。

### リモート・ピアのエンドポイント情報の取得
{: #remote-peer-retrieve-endpoint-info}

ICP コンソールにログインします。左上隅の**「メニュー」**アイコンをクリックします。**「ワークロード (Workload)」** > **「デプロイメント」**をクリックします。次に、リモート・ピア・インスタンスの名前を検索してクリックし、デプロイメントの概要画面を開きます。**「詳細の表示」**までスクロールダウンし、リモート・ピアの`「入口 IP」`と`「ポート」`を見つけます。

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

リモート・ピアに接続するための入口 IP アドレスおよびノード・ポートをメモします。リモート・ピアでチェーンコードをインストールおよびインスタンス化する際は、この情報を使用する必要があります。以下の例では、ピア・アドレスは `9.46.126.89:30162`、`9.46.126.89:30260`、または `9.46.126.89:32051` のいずれかとなります。

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### kubectl CLI の構成
{: #remote-peer-kubectl-configure}

ICP で実行されるピア・コンテナー内でいくつかの必要な操作を実行するには、**kubectl** コマンド・ライン・ツールを使用する必要があります。{{site.data.keyword.cloud_notm}} Private CLI ツールを使用して、クラスターにログインします。

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

次に、ICP クラスターのダッシュボードにログインします。ICP UI の右上隅のユーザー・アバターをクリックしてから、**「クライアントの構成」**をクリックします。ローカル・マシン上の端末ウィンドウで、コマンドをコピー・アンド・ペーストして実行します。以下のコマンドを実行して、構成が正常に機能したことを確認できます。

```
kubectl -n <namespace> get pods
```
{:codeblock}

ここで、`default` 名前空間が使用されていない場合は、`-n <namespace>` を指定する必要があります。`<namespace>` を ICP クラスターの名前空間の値に
置き換えます。


このコマンドの結果、以下の例のような応答が返されます。`remotepeer-bd65c4769-95rmm` は、ピア・コンテナーの名前です。

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

応答からリモート・ピアのポッド名を認識できない場合は、「デプロイメント」パネルの**「ポッド」**ペインでポッドの名前を検索できます。

以下のコマンドを使用して、リモート・ピア・コンテナー内のコマンドを実行します。このとき、`<PEER_CONTAINER_NAME>` の値を上記からのポッドの名前に置き換えます。

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
ここで、`default` 名前空間が使用されていない場合は、`-n <namespace>` を指定する必要があります。`<namespace>` を ICP クラスターの名前空間の値に
置き換えます。

例えば、次のようにします。

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

これで、ピア・コンテナー CLI を使用する準備ができました。SDK またはコマンド・ラインのいずれかを使用する場合は、CLI を使用してピア TLS 証明書をダウンロードし、署名証明書をピアにアップロードする必要があります。

## ICP で実行されるリモート・ピアの再始動
{: #remote-peer-restart}

証明書がリモート・ピアに追加される場合は常に、ピア・ノードで新しい証明書が認識されるようピア・ノードを再始動する必要があります。

**kubectl** コマンドを使用して、ICP で実行されるリモート・ピアを再始動できます。このコマンドには、コマンド・パラメーターとして、リモート・ピアの**ポッド**とその**コンテナー**の名前が必要です。kubectl コマンドの使用について詳しくは、[kubectl CLI の構成](#remote-peer-kubectl-configure)を参照してください。

1. ICP コンソールで、リモート・ピア・デプロイメント用の**ポッド**名および**コンテナー**名を検索します。

  1. コンソールの左上隅の「メニュー」アイコンをクリックしてから、**「ワークロード」>「デプロイメント」**をクリックします。
  2. テーブルでリモート・ピアのリリースを見つけてクリックし、そのピアを開きます。
  3. スクロールダウンして、関連する**ポッド**名を表示します。**ポッド**名をメモします。
  4. ポッドをクリックして開きます。
  5. **「コンテナー」**タブをクリックします。リモート・ピアの**コンテナー**名をメモします。

2. コマンド・ラインから、以下のコマンドを実行してピア再始動します。このとき、`<REMOTE_PEER_POD_NAME>` および `<REMOTE_PEER_CONTAINER_NAME>` を上記からの値に置き換えます。

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. `kubectl get pods` コマンドを実行し、出力でポッドの **RESTART** 数を調べて、リモート・ピアが再始動されたことを確認できます。

さらに、[HEAD 要求](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes)を使用して、リモート・ピアの可用性を確認できます。


## Fabric SDK を使用したリモート・ピアの操作
{: #remote-peer-operate-with-sdk}

Hyperledger Fabric SDK には強力な API のセットが用意されており、これらの API により、アプリケーションはブロックチェーン・ネットワークと対話したりこれを操作したりすることができます。サポートされている言語の最新リストおよび Fabric SDK 内で使用可能な API の完全なリストについては、[Hyperledger Fabric SDK コミュニティーの資料 ![外部リンク・アイコン](../images/external_link.svg " 外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK コミュニティーの資料") を参照してください。Fabric SDK を使用して、リモート・ピアを {{site.data.keyword.blockchainfull_notm}} Platform 上のチャネルに参加させたり、チェーンコードをピアにインストールしたり、チェーンコードをチャネル上でインスタンス化したりできます。

以下の手順では、[Fabric Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Fabric Node SDK") を使用してリモート・ピアを操作するため、事前に SDK に精通していることが前提となっています。開始前に Node SDK の使用方法を学習するために、およびチェーンコードを呼び出して照会する準備ができたら、リモート・ピアを使用してアプリケーションを開発する際のガイドとして、[アプリケーションの開発チュートリアル](/docs/services/blockchain/v10_application.html)を使用できます。

### Node SDK のインストール

NPM を使用して [Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "Node SDK") をインストールできます。
```
npm install fabric-client@1.1
```
{:codeblock}

バージョン 1.1 の Node SDK を使用することをお勧めします。

### リモート・ピアを使用するための SDK の準備
{: #remote-peer-node-sdk}

SDK を使用してリモート・ピアを操作する前に、必要な証明書 (登録) を生成する必要があります。これによりアプリケーションは、{{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークおよびリモート・ピアと通信することができます。**管理者** ID を使用して、ステップに従って [SDK に登録](/docs/services/blockchain/v10_application.html#enroll-app-sdk)します。[アプリケーションの開発](/docs/services/blockchain/v10_application.html)チュートリアルでは同様に**管理者**として登録するため、サンプル・コードを変更する必要はありません。

### {{site.data.keyword.blockchainfull_notm}} Platform への signCert のアップロード
{: #remote-peer-upload-sdk}

アプリケーションにネットワークを操作する権限を付与するには、{{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークに SDK 署名証明書をアップロードする必要があります。

- 署名証明書は、SDK によって暗号情報が生成されたディレクトリー内の admin という名前のファイル内にあります。`-----BEGIN CERTIFICATE-----` で始まり `-----END CERTIFICATE-----` で終わる `certificate` フィールドの後の引用符内で囲まれた証明書をコピーします。CLI を使用してコマンド `echo -e "<CERT>" > admin.pem` を発行し、証明書を PEM 形式に変換できます。次に、ネットワーク・モニターを使用して、証明書の内容をブロックチェーン・ネットワークに貼り付けることができます。{{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークにログインします。ネットワーク・モニターの「メンバー」画面で、**「証明書」** > **「証明書の追加」**をクリックします。証明書に任意の名前を指定して、内容を**「証明書」**フィールドに貼り付けます。ピアを再始動するかどうかを尋ねられたら、**「再始動」**をクリックします。このアクションは、組織がチャネルに参加する前に実行する必要があります。

### リモート・ピアへの signCert のアップロード
{: #remote-peer-upload-signcert}

SDK の署名証明書をリモート・ピアにアップロードして、リモート・ピアを再始動する必要もあります。リモート・ピア・コンテナー内に、[{{site.data.keyword.blockchainfull_notm}} Platform にアップロード](#remote-peer-upload-sdk)したものと同じ署名証明書をインストールする必要があります。

以下のコマンドを[ピア・コンテナー内部](#remote-peer-kubectl-configure)から実行して、証明書をアップロードします。
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

テキスト `<CERT.PEM>` を、signCert (`-----BEGIN CERTIFICATE-----` で始まり `-----END CERTIFICATE-----` で終わる部分) に置き換えます。cert.pem ファイルがこのディレクトリーに既に存在していることに**注意してください**。新しい証明書に `cert2.pem` のような名前を付けて、コンテナー内に既に存在する証明書を上書きしないようにしてください。

新しい証明書を追加したため、ピアでこの証明書が選択されるようにコンテナーを再始動する必要があります。ピアを再起動するには、これらの[手順](#remote-peer-restart)に従ってください。

### SDK へのリモート・ピアの TLS 証明書の引き渡し
{: #icp-remote-peer-download-tlscert}

TLS `cacert.pem` の内容をピア・コンテナーからアプリケーションにコピーして、SDK からの通信を認証する必要があります。[ピア CLI コンテナー](#remote-peer-kubectl-configure)で、以下のコマンドを実行します。
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

`cacert.pem` ファイルのテキスト全体 (最初の `-----BEGIN CERTIFICATE-----` で始まり、最後の `-----END CERTIFICATE-----` で終わる部分) をクリップボードにコピーします。この証明書をローカル・ファイル・システムに PEM 形式で保存します。次に、単純なファイル読み取りコマンドを使用して、TLS 証明書をアプリケーションにインポートできます。
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### SDK へのリモート・ピアのエンドポイント情報の提供
{: #remote-peer-SDK-endpoints}

[リモート・ピアのエンドポイント情報](#remote-peer-retrieve-endpoint-info)を検索し、新しいピア変数を宣言するか、接続プロファイルを更新して、SDK にこの情報を提供します。以下の例では、Fabric ネットワーク上のエンドポイントとしてピアを定義し、インポートした TLS 証明書をピアに渡します。
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

ピアが DNS 要求を解決できるように、`<something>.blockchain.com` の `ssl-target-name-override` を指定する必要があります。

**注:** ピアはリモートであるため、{{site.data.keyword.blockchainfull_notm}} Platform ネットワーク上のその他の組織は、ピアのエンドポイント情報を接続プロファイル内で見つけることができません。その他の組織がトランザクションを承認のためにリモート・ピアに送信する必要がある場合は、パブリック IP と TLS 証明書をアウト・オブ・バンド操作で指定する必要があります。

### SDK を使用したチャネルへの参加
{: #remote-peer-join-channel-sdk}

ブロックチェーン・ネットワークのメンバーは、リモート・ピアをチャネルに参加させる前に、組織をネットワーク内のチャネルに追加する必要があります。

  - リモート・ピアに対して新しいチャネルを開始できます。チャネル・イニシエーターは、[チャネル作成](/docs/services/blockchain/howto/create_channel.html#creating-a-channel)時に組織を自動的に含めることができます。ネットワーク・モニターでチャネルを作成するには、{{site.data.keyword.blockchainfull_notm}} Platform 上に少なくとも 1 つのピアが存在する必要があることに注意してください。  

  - ブロックチェーン・ネットワークの別のメンバーが、[チャネル更新](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)を使用して、組織を既存のチャネルに追加することもできます。{{site.data.keyword.blockchainfull_notm}} Platform 上にピアがあるチャネルのメンバーは、プラットフォームでピアをホストしていない場合でも、ネットワーク・モニターを使用して組織をチャネルに追加できます。

    組織がチャネルに追加されたら、他のメンバーがトランザクション中にデジタル署名を検証できるように、ピアの署名証明書をチャネルに追加する必要があります。証明書とチャネルを同期するだけで良いように、リモート・ピアはインストール時にその署名証明書をアップロードします。ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。このアクションによって、チャネルのすべてのピアで証明書が同期されます。

組織がチャネルに含まれている場合は、[チャネルに参加する](/docs/services/blockchain/v10_application.html#join-channel-sdk)手順に従います。順序付けサービスの URL とチャネル名を指定する必要があります。

### SDK を使用したピアへのチェーンコードのインストール
{: #remote-peer-install-cc-sdk}

以下の手順に従って、SDK を使用してリモート・ピアに[チェーンコードをインストール](/docs/services/blockchain/v10_application.html#install-cc-sdk)します。

### SDK を使用したチャネルでのチェーンコードのインスタンス化
{: #remote-peer-instantiate-cc-sdk}

チェーンコードをインスタンス化または更新する必要があるのは、チャネルのいずれかのメンバーのみです。したがって、{{site.data.keyword.blockchainfull_notm}} Platform 上にピアがあるチャネルのどのネットワーク・メンバーも、ネットワーク・モニターを使用してチェーンコードをインスタンス化し、エンドースメント・ポリシーを指定できます。ただし、リモート・ピアを使用してチャネルでチェーンコードをインスタンス化する必要がある場合は、SDK を使用し、手順に従って[チェーンコードをインスタンス化](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk)できます。


## CLI を使用したリモート・ピアの操作
{: #remote-peer-cli-operate}

Fabric CA クライアントおよび Fabric ツール・コンテナーを使用して、コマンド・ラインからリモート・ピアを操作することもできます。これらの手順では、最初に Fabric CA クライアントを使用して必要な証明書を生成してから、Fabric ツール・コンテナーを使用して、ピアをチャネルに参加させてチェーンコードをインストールした後、チャネルでチェーンコードをインスタンス化することによりピアを操作します。

### Fabric CA クライアントを使用した登録
{: #peer-client-enroll}

最初のステップは、Fabric CA クライアントを使用した必要な証明書 (登録) の生成です。まず、Fabric CA クライアントをインストールする必要があります。[ご使用のプラットフォーム用の fabric-ca バイナリー v1.1.0](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) をローカル・マシンにダウンロードして抽出し、これを `$HOME/fabric-ca-remote/` などのフォルダーに移動します。

1.  Fabric CA クライアントを使用するために環境を準備します。コマンドでクライアント・バイナリーを直接参照できるように、移動先のディレクトリーに変更します。
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  クライアントが鍵を作成できる場所のパスを設定します。`enroll` コマンドを初めて実行する場合、`msp` フォルダーおよび `.yaml` ファイルは存在しません。以前に `enroll` コマンドを実行したことがある場合は、以下の `rm` コマンドを使用して、以前に登録を試みたときの構成情報をすべて削除することが重要です。
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  {{site.data.keyword.blockchainfull_notm}} Platform で、認証局のエンドポイント情報を取得する必要があります。ネットワーク・モニターの「概要」画面で、**「接続プロファイル」**ボタンをクリックして、ネットワーク資格情報を含む JSON ファイルを開きます。`certificateAuthorities` セクションにスクロールダウンし、以下の情報をメモします。
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  CA と通信するには、{{site.data.keyword.blockchainfull_notm}} Platform から TLS 証明書を取得する必要もあります。上記のネットワーク資格情報に、必要な証明書が含まれます。接続プロファイル・ファイルの `certificateAuthorities` セクションで、`"pem:"` の後に続く証明書 (`-----BEGIN CERTIFICATE-----` で始まり `-----END CERTIFICATE----- ` で終わる部分) をコピーします。**引用符は含めないでください**。

    端末ウィンドウから以下のコマンドを実行します。このとき、`<certificate>` をコピーした証明書に置き換えます。
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 この証明書を、将来のコマンドで参照できる場所に保管します。

5.  これで、必要な証明書を生成する準備ができました。以下のコマンドを実行します。
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    例えば、次のようにします。
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    コマンドが正常に完了したら、以下の例のような応答が表示されます。
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    このコマンドにより、必要な証明書が生成された後、`$FABRIC_CA_CLIENT_HOME` 内の `msp` という名前のフォルダーに保管されます。このフォルダーとこの中の証明書は、以下のステップで重要となります。

### 証明書のローカル・システムでの管理
{: #manage-certs}

リモート・ピアを操作するには、ローカル・マシンで証明書の一部の管理を行い、Fabric CA クライアントによって生成された一部の証明書を {{site.data.keyword.blockchainfull_notm}} Platform およびピアにアップロードする必要があります。また、Platform およびピアから TLS 証明書をダウンロードする必要もあります。作業対象の証明書および実行するタスクについて詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理](/docs/services/blockchain/certificates.html)を参照してください。

ローカル・マシンでコマンド端末を開き、Fabric-CA-Client バイナリーを移動して MSP フォルダーを保管したディレクトリーにナビゲートします。
1. `cert.pem` ファイルを `signcerts` フォルダーから新しい `admincerts` フォルダーにコピーします。  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. また、リモート・ピアの TLS 証明書を ICP 上のコンテナーからローカル・マシンにコピーする必要もあります。

  - [ピア・コンテナーにログイン](#remote-peer-kubectl-configure)して、以下のコマンドを実行します。
    ```
    cat /mnt/certs/tls/cacert.pem
    ```
    {:codeblock}

    `cacert.pem` ファイルのテキスト全体 (最初の `-----BEGIN CERTIFICATE-----` で始まり、最後の `-----END CERTIFICATE-----` で終わる部分) をクリップボードにコピーします。

  - ローカル・マシンにスイッチバックして、以下のコマンドを実行します。このとき、テキスト `<CACERT.PEM>` をクリップボード内のテキストに置き換えます。
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
    {:codeblock}

3. チェーンコードをピアにインストールする許可を CLI に付与するには、Fabric CA クライアントによって生成された signCert をピアの管理フォルダーにアップロードしてピアを再始動する必要があります。したがって、`admincert/cert.pem` 証明書を、ローカル・マシンからピア・コンテナー内の `/mnt/crypto/peer/peer/msp/admincerts/` ディレクトリーに `cert2.pem` としてコピーします。

    - ローカル・マシンで、以下のコマンドを実行します。

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - `cert.pem` ファイルのテキスト (`-----BEGIN CERTIFICATE-----` で始まり、`-----END CERTIFICATE-----` で終わる部分) をクリップボードにコピーします。

    - ピア・コンテナーにスイッチバックして以下のコマンドを実行し、cert.pem をピア証明書に追加します。このとき、テキスト `<CERT.PEM>` をクリップボード内のテキストに置き換えます。

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **注:** cert.pem ファイルは、このディレクトリーに既に存在しています。上書きしないでください。

    - 新しい証明書を追加したため、ピアでこの証明書が選択されるようにコンテナーを再始動する必要があります。ピアを再起動するには、これらの[手順](#remote-peer-restart)に従ってください。

4. リモート CLI またはアプリケーションが、チャネルのジェネシス・ブロックをフェッチしたりチェーンコードをインスタンス化したりするなどのチャネル操作を実行できるように、同じ `admincert/cert.pem` 証明書をローカル・マシンから {{site.data.keyword.blockchainfull_notm}} Platform にアップロードする必要があります。
    1. ローカル・マシンで、ファイル `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` を切り取ってクリップボードにコピーします。
    2. {{site.data.keyword.blockchainfull_notm}} Platform で、ネットワークのネットワーク・モニターを実行します。
    3. 左側のナビゲーターで**「メンバー」**をクリックし、**「証明書」**タブをクリックします。
    4. **「証明書の追加」**ボタンをクリックします。
    5. **「証明書の追加」**ウィンドウで、証明書に `fabrictools.pem` などの名前を付け、クリップボードにコピーした証明書を貼り付けて**「送信」**をクリックします。
    6. ピアを再始動する必要があるかどうかを尋ねられます。**「再始動」**をクリックします。
    7. 左側のナビゲーターで**「チャネル」**をクリックして、リモート・ピアが参加するチャネルを見つけます。
    8. **「アクション」**ヘッダーの下の 3 点リーダーをクリックし、**「証明書の同期」**をクリックします。**「証明書の同期」**ウィンドウで、**「送信」**をクリックします。

    **注**: 以下でピアがチャネルに参加する前に、チャネル証明書を同期することが重要です。

### Fabric ツール・コンテナーのセットアップ

すべての証明書を必要な場所に移動したら、Fabric ツール・コンテナーを Docker からインストールして使用する準備ができます。この場合も、MSP フォルダーを保管したディレクトリーから以下のコマンドを実行する必要があります。

1.  以下のコマンドを使用して、Fabric ツールの Docker イメージをダウンロードします。
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  以下のコマンドを実行して、Fabric ツール・コンテナーを開始します。
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    このコマンドにより、MSP ディレクトリーがツール・コンテナー内部にマウントされます。

3.  リモート・ピアのアドレスは、`blockchain.com` という接尾辞を含むドメイン名に解決される必要があります。これを行うには、DNS を使用するか、または /etc/hosts ファイルを変更します。これらの手順の目的のために、Fabric ツール・コンテナー内の `/etc/hosts` ファイルを変更します。ICP コンソールにログインして、リモート・ピアの[エンドポイント情報](#remote-peer-retrieve-endpoint-info)を検索します。ピアの `INGRESS_IP` が見つかったら、リモート・ピアのアドレスを使用して以下のコマンドを実行します。
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Fabric ツール・コンテナーを使用したチャネルへのリモート・ピアの参加
{: #icp-cli-join-peer-to-channel}

CLI コマンドを実行してリモート・ピアをチャネルに参加させる前に、組織をネットワーク内のチャネルに追加する必要があります。

  - リモート・ピアに対して新しいチャネルを開始できます。チャネル・イニシエーターは、[チャネル作成](/docs/services/blockchain/howto/create_channel.html#creating-a-channel)時に組織を自動的に含めることができます。ネットワーク・モニターでチャネルを作成するには、{{site.data.keyword.blockchainfull_notm}} Platform 上に少なくとも 1 つのピアが存在する必要があることに注意してください。  

  - ブロックチェーン・ネットワークの別のメンバーが、[チャネル更新](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)を使用して、組織を既存のチャネルに追加することもできます。{{site.data.keyword.blockchainfull_notm}} Platform 上にピアがあるチャネルのメンバーは、プラットフォームでピアをホストしていない場合でも、ネットワーク・モニターを使用して組織をチャネルに追加できます。

    組織がチャネルに追加されたら、他のメンバーがトランザクション中にデジタル署名を検証できるように、ピアの署名証明書をチャネルに追加する必要があります。証明書とチャネルを同期するだけで良いように、リモート・ピアはインストール時にその署名証明書をアップロードします。ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。このアクションによって、チャネルのすべてのピアで証明書が同期されます。

1. ネットワーク・モニターの**「概要」**パネルで使用可能な `creds.json` ファイルから構成情報を取得します。**「接続プロファイル」**、**「ダウンロード」**の順にクリックします。

  - `orderers > url` の下に配置されている **orderers** を検索して、順序付けプログラムの URL を見つけます。ネットワーク名で始まる URL をメモしておきます。URL は以下の例のようになります。
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - **organizations** を検索して、組織の名前を見つけます。この組織はリモート・ピアの登録に使用した組織と同じである必要があります。組織の名前をその関連 `mspid` とともに見つけることができます。`mspid` の値をメモしておきます。

2. [リモート・ピアのエンドポイント情報を検索](#remote-peer-retrieve-endpoint-info)します。このとき、`Ingress IP` の横にリストされている `Ports` をメモしておきます。

  ```
  Ingress IP: <IP address>
Ports: < Port List>
  ```
  {:codeblock}

  30K の範囲でポートをメモします。これらは、Fabric ツール・コンテナーからアドレス指定可能なポートであり、このポートを使用して、`PEERADDR` 環境変数を設定します。

  ```
  Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  したがって、使用する `PEERADDR` は、`/etc/hosts` ファイルに指定された値を、`7051` にバインドされているポート (この例では `30162`) とともに使用して構成されます。このため、結果として生成される `PEERADDR` の値は、`remotepeer.blockchain.com:30162` のようになります。

3. 以下のコマンドを実行して、Fabric ツール・コンテナーの環境変数を設定します。

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  フィールドを独自の情報に置き換えます。
    - `<ORDERER_URL>` を、`creds.json` ファイルからの順序付けプログラムのホスト名およびポートに置き換えます。
    - `<CHANNEL_NAME>` を、ピアが参加するチャネルの名前に置き換えます。
    - `<CC_NAME>` を、チェーンコードを参照するための任意の名前に置き換えます。
    - `<ORGANIZATION_MSP_ID>` を、`creds.json` ファイルからの組織の名前に置き換えます。
    - `<PEER_ADDR>` を、前のステップで構成した値に置き換えます。

  例えば、次のようにします。

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. チャネルのジェネシス・ブロックをフェッチして、ピア上にチャネル台帳を作成します。最初に、`cd` でルート・ディレクトリーに移動してから、ジェネシス・ブロックをフェッチするコマンドを実行します。

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
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

5. 以下のコマンドを使用して、ピアをチャネルに参加させます。

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  これが正常に機能した場合、以下の例のような情報が表示されます。

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric ツール・コンテナーを使用したリモート・ピアへのチェーンコードのインストール
{: #icp-toolcontainer-install-cc}
これで、リモート・ピアにチェーンコードをインストールおよびインスタンス化する準備ができました。これらの手順のために、`fabcar` チェーンコードを `fabric-samples` リポジトリーからインストールします。以下のコマンドを使用して、`fabric-samples` チェーンコードを `github.com` からダウンロードします。

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

以下のピア CLI コマンドを実行して、リモート・ピアに `fabcar` チェーンコードをインストールします。

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  このコマンドが正常に完了した場合、以下のように表示されます。

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric ツール・コンテナーを使用したチャネルでのチェーンコードのインスタンス化
{: #icp-toolcontainer-instantiate-cc}

チャネルでチェーンコードをインスタンス化する必要があるのは 1 つのピアのみであるため、リモート・ピアを使用してチェーンコードをインスタンス化する必要はありません。{{site.data.keyword.blockchainfull_notm}} Platform でホストされているピアでこの操作を行うことができます。ただし、リモート・ピアでチェーンコードをチャネルに対してインスタンス化する必要がある場合は、Fabric ツール・コンテナーで以下のコマンドを実行できます。

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

このコマンドが正常に完了した場合、以下のように表示されます。

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## {{site.data.keyword.cloud_notm}} Private でのリモート・ピアのログの表示
{: #remote-peer-log-icp}

ICP コンソールからリモート・ピアのログにアクセスし、Kibana インターフェースでログを確認できます。

1. ICP コンソールで、左上隅の**「メニュー」**アイコンをクリックします。
2. メニュー・リストから、**「ワークロード」** > **「Helm リリース」**をクリックします。
3. 「Helm リリース」テーブルで、Helm リリースの名前をクリックします。
4. **「ポッド」**セクションまでスクロールダウンして、表の行の末尾にある**「ログの表示」**リンクをクリックします。リモート・ピアのログが Kibana インターフェースで開きます。

## チェーンコードの更新

時間の経過とともに、リモート・ピア上で実行されているチェーンコードを変更することが必要になる場合があります。チェーンコードはピアにインストールされ、チャネルでインスタンス化されるため、チャネル上のすべてのピアでチェーンコードを更新する必要があります。

チェーンコードを更新するには、以下の手順を実行します。

1. 各リモート・ピアでチェーンコードを更新するには、クライアント・アプリケーションまたは CLI コマンドを使用して、チェーンコードをピアにインストールする際に使用した処理を再実行するだけです。必ず、当初使用したものと同じチェーンコード名を指定してください。ただし、今回は、チェーンコードの `Version` を増分します。すべてのピアで、同じチェーンコード名とバージョンを使用する必要があります。

2. チャネル内のすべてのピアに新しいチェーンコードをインストールした後、ネットワーク・モニターまたは [peer chaincode upgrade ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) コマンドを使用して、新しいチェーンコードを使用するようにチャネルを更新します。

ネットワーク・モニターの「コードのインストール」パネルを使用してチェーンコードをチャネルで更新する方法について詳しくは、これらの[手順](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode)のステップ 2 を参照してください。

## トラブルシューティング
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **問題:** ピア・コンテナーにログインする際のエラー
{: #icp-bash-error}

この問題は、以下のコマンドを実行して、kubectl ツールを使用してピア内でコマンドを実行したときに発生します。
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
失敗すると、以下の例のようなエラー・メッセージが生成されます。
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**解決策:**  

このエラーは、ビッグ・エンディアン・システム (IBM System Z など) で、2 つの Docker bash セッションが同じ Docker コンテナー内で開いている場合に発生します。2 つ目の bash セッションは、実行中のコンテナーに接続できない場合があります。この問題を解決するには、[ピア・コンテナーを再始動](#remote-peer-restart)して、ピアの再始動後に `kubectl exec` コマンドを再試行します。

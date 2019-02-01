---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# AWS でのピアの操作
{: #remote-peer-operate-aws}


***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


AWS 内で {{site.data.keyword.blockchainfull}} Platform ピアをセットアップした後に、いくつかの操作ステップを実行する必要があります。その後、ブロックチェーン・ネットワークの台帳を照会および呼び出すためのトランザクションをピアから発行できるようになります。このステップでは、チャネルへの組織の追加、チャネルへのピアの参加、ピアでのチェーンコードのインストール、チャネルでのチェーンコードのインスタンス化、およびピアへのアプリケーションの接続を行います。 [Fabric SDK](#aws-peer-operate-with-sdk) または[コマンド・ライン](#aws-peer-cli-operate)を使用して、これらの操作ステップを実行できます。推奨されるパスは Fabric SDK ですが、この手順では、SDK の操作に精通していることが前提となっています。

**注**: AWS 内の {{site.data.keyword.blockchainfull_notm}} Platform ピアは、{{site.data.keyword.blockchainfull_notm}} Platform でホストされているピアの全機能やサポートにアクセスすることはできません。結果として、ネットワーク・モニターを使用してリモート・ピアを操作することはできません。 AWS 内でピアの実行を開始する前に、必ず[考慮事項](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations)を確認してください。

## Fabric SDK を使用したピアの操作
{: #aws-peer-operate-with-sdk}

Hyperledger Fabric SDK には強力な API のセットが用意されており、これらの API により、アプリケーションはブロックチェーン・ネットワークと対話したりこれを操作したりすることができます。 サポートされている言語の最新リストおよび Hyperledger Fabric SDK 内で使用可能な API の完全なリストについては、[Hyperledger Fabric SDK コミュニティーの資料 ![外部リンク・アイコン](../images/external_link.svg " 外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK コミュニティーの資料") を参照してください。Fabric SDK を使用して、ピアを {{site.data.keyword.blockchainfull_notm}} Platform 上のチャネルに参加させたり、チェーンコードをピアにインストールしたり、チェーンコードをチャネル上でインスタンス化したりできます。

以下の手順では、[Fabric Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "jode.js 用の Hyperledger Fabric SDK") を使用してピアを操作するため、事前に SDK に精通していることが前提となっています。 [アプリケーションの開発チュートリアル](../v10_application.html)を使用して、作業を開始する前に Node SDK の使用方法を学習できます。このチュートリアルは、チェーンコードを呼び出して照会する準備ができたときに、ピアを使用してアプリケーションを開発する際のガイドとしても活用できます。

{{site.data.keyword.blockchainfull_notm}} Platform Peer on AWS のクイック・スタートでは、高可用性を実現するために 2 つのピアが作成されます。したがって、ピアごとに 1 回、操作ステップに従う必要があります。 ご使用のアプリケーションからチェーンコードを照会して呼び出す準備ができたら、SDK を両方のピアに接続することで、ご使用の[アプリケーションの高可用性を確保](/docs/services/blockchain/v10_application.html#ha-app)してください。

### Node SDK のインストール

NPM を使用して [Node SDK ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/ "jode.js 用の Hyperledger Fabric SDK") をインストールできます。
```
npm install fabric-client@1.2
```
{:codeblock}

バージョン 1.2 の Node SDK を使用することをお勧めします。

### ピアを使用するための SDK の準備
{: #remote-peer-node-sdk}

SDK を使用してピアを操作する前に、必要な証明書 (登録) を生成する必要があります。これによりアプリケーションは、{{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークおよびピアと通信することができます。 **管理者** ID を使用して、ステップに従って [SDK に登録](/docs/services/blockchain/v10_application.html#enroll-app-sdk)します。 [アプリケーションの開発](/docs/services/blockchain/v10_application.html)チュートリアルでは同様に**管理者**として登録するため、サンプル・コードを変更する必要はありません。

### IBM Blockchain Platform への署名付き証明書のアップロード
{: #remote-peer-upload-SDK}

SDK 署名証明書を {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークにアップロードすることで、自身のデジタル署名を他のメンバーが認識できるようにする必要があります。

- 署名証明書は、SDK によって暗号情報が生成されたディレクトリー内の admin という名前のファイル内にあります。 `-----BEGIN CERTIFICATE-----` で始まり `-----END CERTIFICATE-----` で終わる `certificate` フィールドの後の引用符内で囲まれた証明書をコピーします。 CLI を使用してコマンド `echo -e "<CERT>" > admin.pem` を発行し、証明書を PEM 形式に変換できます。 次に、ネットワーク・モニターを使用して、証明書の内容をブロックチェーン・ネットワークに貼り付けることができます。 {{site.data.keyword.blockchainfull_notm}} Platform 上のネットワークにログインします。 ネットワーク・モニターの「メンバー」画面で、**「証明書」** > **「証明書の追加」**をクリックします。 証明書に任意の名前を指定して、内容を**「証明書」**フィールドに貼り付けます。 ピアを再始動するかどうかを尋ねられたら、**「再始動」**をクリックします。 このアクションは、組織がチャネルに参加する前に実行する必要があります。

### ピアへの署名付き証明書のアップロード
{: #remote-peer-upload-signcert}

SDK の署名証明書をリモート・ピアにアップロードして、リモート・ピアを再始動する必要もあります。 [IBM Blockchain Platform にアップロードした](#remote-peer-upload-SDK)のと同じ署名証明書をリモート・ピア・コンテナー内にインストールする必要があります。

AWS コンソールでインスタンスを選択して (**「サービス」>「EC2」>「インスタンス」**の順にクリック)、「接続」ボタンをクリックすることで、VPC インスタンスに SSH で接続します。AWS の指示に従って ssh コマンドを実行します。

以下のコマンドをピア・コンテナー内部から実行して、証明書をアップロードします。  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- `<PEER_ENROLL_ID>` の代わりに、クイック・スタート・テンプレートで指定した登録 ID (このリモート・ピア・インスタンスに関連付けられた ID) を入力します。  
- `<CERT.PEM>` の代わりに、自身の署名付き証明書を入力します。この署名付き証明書の範囲は、`-----BEGIN CERTIFICATE-----` から `-----END CERTIFICATE-----` までです。    

  **注:** `cert.pem` ファイルが存在する場合は、このファイルを上書きせずに、新しいファイルを作成してください (`cert2.pem` など)。

新しい証明書を追加したため、ピアでこの証明書が選択されるようにするために、コンテナーを再始動する必要があります。ピアを再起動するには、こちらの[手順](#remote-peer-aws-restart)に従ってください。

### SDK へのピアの TLS 証明書の引き渡し
{: #aws-remote-peer-download-tlscert}

TLS `cacert.pem` の内容をピア・コンテナーからアプリケーションにコピーして、SDK からの通信を認証する必要があります。 ピア CLI コンテナーで以下のコマンドを実行します。 `<PEER_ENROLL_ID>` の代わりに、クイック・スタート・テンプレートで指定したリモート・ピアのスタック名を入力します (2 つの VPC インスタンスが作成されたことを思い出してください)。
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

`ca.crt` ファイルのテキスト全体 (最初の `-----BEGIN CERTIFICATE-----` で始まり、最後の `-----END CERTIFICATE-----` で終わる部分) をクリップボードにコピーします。 この証明書をローカル・ファイル・システムに PEM 形式で保存します。 次に、単純なファイル読み取りコマンドを使用して、TLS 証明書をアプリケーションにインポートできます。
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### SDK へのピアのエンドポイント情報の提供
{: #remote-peer-SDK-endpoints}

ピアのエンドポイント情報を取得するには、AWS コンソールでピア・インスタンスを見つけます。 EC2 インスタンスの `AWS EC2 ダッシュボードのパブリック DNS (IPv4)` の値をメモして、新しいピア変数を宣言するか接続プロファイルを更新することで、この値を SDK に渡します。パブリック DNS アドレスをポート `7051` に連結します。 以下の例では、ファブリック・ネットワーク上のピアを定義し、インポートした TLS 証明書をそのピアに渡します。

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**注:** ピアはリモートであるため、{{site.data.keyword.blockchainfull_notm}} Platform ネットワーク上のその他の組織は、ピアのエンドポイント情報を接続プロファイル内で見つけることができません。 その他の組織がトランザクションを承認のためにピアに送信する必要がある場合は、パブリック IP と TLS 証明書をアウト・オブ・バンド操作で指定する必要があります。

### SDK を使用したチャネルへの参加
{: #remote-peer-join-channel-sdk}

ブロックチェーン・ネットワークのメンバーは、ピアをチャネルに参加させる前に、組織をネットワーク内のチャネルに追加する必要があります。

  - ピアに対して新しいチャネルを開始できます。 チャネル・イニシエーターは、[チャネル作成](/docs/services/blockchain/howto/create_channel.html#creating-a-channel)時に組織を自動的に含めることができます。

  - ブロックチェーン・ネットワークの別のメンバーが、[チャネル更新](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)を使用して、組織を既存のチャネルに追加することもできます。

    組織がチャネルに追加されたら、他のメンバーがトランザクション中にデジタル署名を検証できるように、ピアの署名証明書をチャネルに追加する必要があります。 証明書とチャネルを同期するだけで良いように、ピアはインストール時にその署名証明書をアップロードします。 ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。 このアクションによって、チャネルのすべてのピアで証明書が同期されます。 join channel コマンドを発行する前にチャネル同期が完了できるように、数分待機することが必要になる場合があります。

組織がチャネルの一部になったら、[チャネルに参加する](/docs/services/blockchain/v10_application.html#join-channel-sdk)手順に従います。順序付けサービスの URL とチャネル名を指定する必要があります。

### SDK を使用したピアへのチェーンコードのインストール
{: #remote-peer-install-cc-sdk}

手順に従って、SDK を使用してピアに[チェーンコードをインストール](/docs/services/blockchain/v10_application.html#install-cc-sdk)します。

### SDK を使用したチャネルでのチェーンコードのインスタンス化
{: #remote-peer-instantiate-cc-sdk}

チェーンコードをインスタンス化または更新する必要があるのは、チャネルのいずれかのメンバーのみです。 したがって、{{site.data.keyword.blockchainfull_notm}} Platform 上にピアがあるチャネルのどのネットワーク・メンバーも、ネットワーク・モニターを使用してチェーンコードをインスタンス化し、エンドースメント・ポリシーを指定できます。 ただし、ピアを使用してチャネルでチェーンコードをインスタンス化する必要がある場合は、SDK を使用し、手順に従って[チェーンコードをインスタンス化](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk)できます。


## CLI を使用したピアの操作
{: #aws-peer-cli-operate}

Fabric CA クライアントおよび Fabric ツール・コンテナーを使用して、コマンド・ラインからピアを操作することもできます。 以下の手順では、まず Fabric CA クライアントを使用して必要な証明書を生成します。 次に、Fabric ツール・コンテナーを使用して、ピアをチャネルに参加させて、チェーンコードをインストールしてから、チャネル上でこのチェーンコードをインスタンス化することで、ピアを操作します。

### Fabric CA クライアントを使用した登録
{: #peer-client-enroll}

最初のステップは、Fabric CA クライアントを使用した必要な証明書 (登録) の生成です。 まず、Fabric CA クライアントをインストールする必要があります。 [ご使用のプラットフォーム用の fabric-ca バイナリー v1.2.0](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) をローカル・マシンにダウンロードして抽出し、これを `$HOME/fabric-ca-remote/` などのフォルダーに移動します。

1.  Fabric CA クライアントを使用するために環境を準備します。 コマンドでクライアント・バイナリーを直接参照できるように、移動先のディレクトリーに変更します。
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  クライアントが鍵を作成できる場所のパスを設定します。  以前に `enroll` コマンドを実行したことがない場合は、`msp` フォルダーと `.yaml` ファイルは存在しません。以前に `enroll` コマンドを実行したことがある場合は、以下の `rm` コマンドを使用して、以前に登録を試みたときの構成情報をすべて削除することが重要です。
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  {{site.data.keyword.blockchainfull_notm}} Platform で、認証局のエンドポイント情報を取得する必要があります。 ネットワーク・モニターの「概要」画面で、**「接続プロファイル」**ボタンをクリックして、ネットワーク資格情報を含む JSON ファイルを開きます。 `certificateAuthorities` セクションにスクロールダウンし、以下の情報をメモします。
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  CA と通信するには、{{site.data.keyword.blockchainfull_notm}} Platform から TLS 証明書を取得する必要もあります。 上記のネットワーク資格情報に、必要な証明書が含まれます。接続プロファイル・ファイルの `certificateAuthorities` セクションで、`"pem:"` の後に続く証明書 (`-----BEGIN CERTIFICATE-----` で始まり `-----END CERTIFICATE----- ` で終わる部分) をコピーします。 **引用符は含めないでください**。

    端末ウィンドウから以下のコマンドを実行します。このとき、`<certificate>` をコピーした証明書に置き換えます。
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 この証明書を、将来のコマンドで参照できる場所に保管します。  

5.  これで、必要な証明書を生成する準備ができました。 以下のコマンドを実行します。
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

    このコマンドにより、必要な証明書が生成された後、`$FABRIC_CA_CLIENT_HOME` 内の `msp` という名前のフォルダーに保管されます。 このフォルダーとこの中の証明書は、以下のステップで重要となります。

### 証明書のローカル・システムでの管理
{: #manage-certs}

ピアを操作するには、ローカル・マシンで証明書の一部の管理を行い、Fabric CA クライアントによって生成された一部の証明書を {{site.data.keyword.blockchainfull_notm}} Platform およびピアにアップロードする必要があります。 また、Platform およびピアから TLS 証明書をダウンロードする必要もあります。 扱うことになる証明書および実行するタスクについて詳しくは、[{{site.data.keyword.blockchainfull_notm}} Platform の証明書の管理](/docs/services/blockchain/certificates.html)を参照してください。

ローカル・マシンでコマンド端末を開き、Fabric-CA-Client バイナリーを移動して MSP フォルダーを保管したディレクトリーにナビゲートします。

1. `cert.pem` ファイルを `signcerts` フォルダーから新しい `admincerts` フォルダーにコピーします。
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. 順序付けプログラムの TLS 証明書を {{site.data.keyword.blockchainfull_notm}} Platform から MSP フォルダーにコピーします。接続プロファイルには必要な証明書が含まれています。ネットワーク・モニターの「概要」画面で、**「接続プロファイル」**ボタンをクリックして、ネットワーク資格情報を含む JSON ファイルを開きます。`「順序付けプログラム」`セクションにナビゲートします。`「pem:」`の後ろの証明書をコピーします。コピー範囲は、`-----BEGIN CERTIFICATE-----` から `-----END CERTIFICATE-----` までです。 引用符は含めないでください。

    端末ウィンドウから、以下のコマンドを実行します。
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. また、ピアの TLS 証明書を AWS 上のコンテナーからローカル・マシンにコピーする必要もあります。

    - [以下の手順に従って](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test)ピア・コンテナーにログインして、以下のコマンドを実行します。ここで、<PEER_ENROLL_ID> の代わりにクイック・スタート・テンプレートで指定したピアのスタック名を入力して、その後ろにピアの番号を入力します (2 つの VPC インスタンスが作成されたことを思い出してください)。

```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
      ```
      {:codeblock}

      `cacert.pem` ファイルのテキスト全体 (最初の `-----BEGIN CERTIFICATE-----` で始まり、最後の `-----END CERTIFICATE-----` で終わる部分) をクリップボードにコピーします。

    - ローカル・マシンにスイッチバックして、以下のコマンドを実行します。 このとき、テキスト `<CACERT.PEM>` をクリップボード内のテキストに置き換えます。
      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
      ```
      {:codeblock}

      **注:** デフォルトでは、AWS クイック・スタート・テンプレートによって 2 つのピア・インスタンスが 2 つの異なるアベイラビリティー・ゾーン内に作成されます。これらのピアのいずれかについてこれらのステップを既に完了している場合は、2 番目のインスタンスに対してこれらのステップを実行するときには、cacert.pem ファイルは既に存在しています。 そのまま作業を進めて、このファイルを 2 番目のピアの証明書に置き換えます。

4. チェーンコードをピアにインストールする許可を CLI に付与するには、Fabric CA クライアントによって生成された署名付き証明書をピアの管理フォルダーにアップロードして、ピアを再始動してください。したがって、`admincert/cert.pem` 証明書を、ローカル・マシンからピア・コンテナー内の `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` ディレクトリーに `cert2.pem` としてコピーします。

    - ローカル・マシンで、以下のコマンドを実行します。

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - `cert.pem` ファイルのテキスト (`-----BEGIN CERTIFICATE-----` で始まり、`-----END CERTIFICATE-----` で終わる部分) をクリップボードにコピーします。

    - ピア・コンテナーにスイッチバックして以下のコマンドを実行し、cert.pem をピア証明書に追加します。 このとき、テキスト `<CERT.PEM>` をクリップボード内のテキストに置き換えます。

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **注:** cert.pem ファイルは、このディレクトリーに既に存在しています。 上書きしないでください。

    - 新しい証明書を追加したため、ピアでこの証明書が選択されるようにするために、コンテナーを再始動する必要があります。[ピア・コンテナーを再始動](#remote-peer-aws-restart)するには、こちらの手順に従ってください。

5. リモートの CLI またはアプリケーションが、チャネルのジェネシス・ブロックの取得やチェーンコードのインスタンス化などのチャネル操作を実行できるようにするために、同じ `admincert/cert.pem` 証明書をローカル・マシンから {{site.data.keyword.blockchainfull_notm}} Platform にアップロードします。
    1. ローカル・マシンで、ファイル `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` を切り取ってクリップボードにコピーします。
    2. {{site.data.keyword.blockchainfull_notm}} Platform で、ネットワークのネットワーク・モニターを実行します。
    3. 左側のナビゲーターで**「メンバー」**をクリックし、**「証明書」**タブをクリックします。
    4. **「証明書の追加」**ボタンをクリックします。
    5. **「証明書の追加」**ウィンドウで、証明書に `fabrictools.pem` などの名前を付け、クリップボードにコピーした証明書を貼り付けて**「送信」**をクリックします。
    6. ピアを再始動する必要があるかどうかを尋ねられます。 **「再始動」**をクリックします。
    7. 左側のナビゲーターで**「チャネル」**をクリックして、ピアが参加するチャネルを見つけます。
    8. **「アクション」**ヘッダーの下の 3 点リーダーをクリックし、**「証明書の同期」**をクリックします。 **「証明書の同期」**ウィンドウで、**「送信」**をクリックします。

    **注**: ピアがチャネルに参加するか、チャネル上でチェーンコードをインスタンス化する前に、チャネル証明書を同期することが重要です。必要に応じて数分間待つことで、チャネル参加コマンドやチェーンコードのインスタンス化コマンドを発行する前にチャネルの同期を完了できるようにします。   

### Fabric ツール・コンテナーのセットアップ
{: #setup-fabric-cli}

すべての証明書を必要な場所に移動したら、Fabric ツール・コンテナーを Docker からインストールして使用できます。これらのコマンドは、必ず MSP フォルダーを保管したディレクトリーから実行してください。

1.  以下のコマンドを使用して、Fabric ツールの Docker イメージをダウンロードします。
    ```
    docker pull ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

2.  以下のコマンドを実行して、Fabric ツール・コンテナーを開始します。
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

    このコマンドにより、MSP ディレクトリーがツール・コンテナー内部にマウントされます。

<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### Fabric ツール CLI を使用したチャネルへのピアの参加
{: #fabric-cli-join-peer-to-channel}

CLI コマンドを実行してピアをチャネルに参加させる前に、組織をネットワーク内のチャネルに追加する必要があります。

  - ピアに対して新しいチャネルを開始できます。 チャネル・イニシエーターは、[チャネル作成](/docs/services/blockchain/howto/create_channel.html#creating-a-channel)時に組織を自動的に含めることができます。

  - ブロックチェーン・ネットワークの別のメンバーが、[チャネル更新](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)を使用して、組織を既存のチャネルに追加することもできます。

    組織がチャネルに追加されたら、他のメンバーがトランザクション中にデジタル署名を検証できるように、ピアの署名証明書をチャネルに追加する必要があります。 証明書とチャネルを同期するだけで良いように、ピアはインストール時にその署名証明書をアップロードします。 ネットワーク・モニターの「チャネル」画面で、組織が参加したチャネルを見つけ、**「アクション」**ヘッダーの下にあるドロップダウン・リストから**「証明書の同期」**を選択します。 このアクションによって、チャネルのすべてのピアで証明書が同期されます。

1. ネットワーク・モニターの「概要」パネルでアクセスできる`「接続プロファイル」`から構成情報を取得します。**「接続プロファイル」**、**「ダウンロード」**の順にクリックします。

  - `orderers > url` の下に配置されている **orderers** を検索して、順序付けプログラムの URL を見つけます。 ネットワーク名で始まる URL をメモしておきます。 URL は以下の例のようになります。

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - **organizations** を検索して、組織の名前を見つけます。 この組織はピアの登録に使用した組織と同じである必要があります。 組織の名前をその関連 `mspid` とともに見つけることができます。 `mspid` の値をメモしておきます。

2. AWS EC2 ダッシュボードのパブリック DNS (IPv4) で、AWS VPC ピア (パブリック DNS IPv4 アドレス) のエンドポイント情報を見つけます。 以下のようになります。

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  この情報を使用して環境変数 `PEERADDR` を作成します。そのためには、このアドレスをポート「7051」と連結します。その結果として、
  `PEERADDR` の値は次のようになります。

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. 以下のコマンドを実行して、Fabric ツール・コンテナー内の環境変数を設定します。

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
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. チャネルのジェネシス・ブロックをフェッチして、ピア上にチャネル台帳を作成します。 最初に、`cd` でルート・ディレクトリーに移動してから、ジェネシス・ブロックをフェッチするコマンドを実行します。

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
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

  ジェネシス・ブロックが正常に追加されると、以下の例のような出力が表示されます。
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. 次に、以下のコマンドを使用して、ピアをチャネルに参加させて、ジェネシス・ブロックを渡します。

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  このコマンドが正常に実行されると、以下のような出力が表示されます。

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric ツール・コンテナーを使用したピアへのチェーンコードのインストール
{: #aws-toolcontainer-install-cc}

これで、ピアにチェーンコードをインストールおよびインスタンス化する準備ができました。 これらの手順のために、`fabcar` チェーンコードを `fabric-samples` リポジトリーからインストールします。 以下のコマンドを使用して、`fabric-samples` チェーンコードを GitHub からダウンロードします。

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

以下のピア CLI コマンドを実行して、ピアに `fabcar` チェーンコードをインストールします。

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
{: #aws-toolcontainer-instantiate-cc}

チャネルでチェーンコードをインスタンス化する必要があるのは 1 つのピアのみであるため、ピアを使用してチェーンコードをインスタンス化する必要はありません。 {{site.data.keyword.blockchainfull_notm}} Platform でホストされているピアでこの操作を行うことができます。 ただし、ピアでチェーンコードをチャネルに対してインスタンス化する必要がある場合は、Fabric ツール・コンテナーで以下のコマンドを実行できます。

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

このコマンドが正常に完了した場合、以下のように表示されます。

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## AWS で実行されているピアを再始動します。
{: #remote-peer-aws-restart}

ピアをデプロイする環境で、ピアを始動、停止、または再始動できます。 証明書がピアに追加されるたびに、その新しい証明書が認識されるためにはピアを再始動する必要があります。 そのためには、以下の複数の方法があります。

- AWS コンソールで、ピア VPC インスタンスをリブートします。
- ピア・コンテナー内から、以下のコマンドを実行します。

 ```
 docker restart peer
 ```
 {:codeblock}  

さらに、[HEAD 要求](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes)を使用して、ピアの可用性を確認できます。

## ピアのログの表示

AWS VPC ピア・インスタンスに SSH で接続してから、以下のコマンドを実行してピア・ログを表示します。

```
docker logs peer
```
{:codeblock}

## チェーンコードの更新

時間の経過とともに、ピア上で実行されているチェーンコードを変更することが必要になる場合があります。 チェーンコードはピアにインストールされ、チャネルでインスタンス化されるため、チャネル上のすべてのピアでチェーンコードを更新する必要があります。

チェーンコードを更新するには、以下の手順を実行します。

1. AWS 内の各ピア上でチェーンコードを更新するには、クライアント・アプリケーションまたは CLI コマンドを使用して、チェーンコードをピアにインストールするために実行したプロセスを再実行します。必ず、当初使用したものと同じチェーンコード名を指定してください。 ただし今回は、チェーンコードの `Version` 番号を大きくします。

2. チャネル内のすべてのピアに新しいチェーンコードをインストールした後、ネットワーク・モニターまたは [peer chaincode upgrade ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) コマンドを使用して、新しいチェーンコードを使用するようにチャネルを更新します。

ネットワーク・モニターの「コードのインストール」パネルを使用してチャネル上のチェーンコードを更新する方法について詳しくは、こちらの[手順](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode)のステップ 2 を参照してください。

## トラブルシューティング

### **問題:** リモート・ピアがブロックチェーン・ネットワークに接続できない

スタックの作成は正常に完了しますが、Docker ログに以下のエラーが記録されます。

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

**解決策:**  
このエラーの原因として考えられるのは、クイック・スタート・テンプレートがデプロイされたときに CAUrl でポートが指定されなかったことです。
CAUrl は、`https://<network>-org1-ca.stage.blockchain.ibm.com:31011` のようになる必要があります。
CAUrl に適切な値を指定するように注意して、クイック・スタート・テンプレートを再デプロイします。

### **問題:** エラーが発生してチェーンコードをインスタンス化できない

次のエラーが発生して、チェーンコードをインスタンス化できません。
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**解決策:**   
管理者証明書がネットワーク・モニターにアップロードされた後に、証明書がチャネル上で同期されるようにしてください。 詳しくは、こちらの[手順](#manage-certs)のステップ 5 を参照してください。ピアがチャネルに参加する前に、チャネル証明書を同期することが重要であることに留意してください。

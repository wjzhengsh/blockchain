---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private での認証局の操作
{: #ca-operate}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

認証局 (CA) は、ネットワーク上の ID を提供します。CA は、複数の団体の間で信頼を確立するために使用される、信頼できる公証人のようなものと考えることができます。ネットワーク内の各エンティティーには、ルート CA がエンティティーのデジタル ID をカプセル化するために署名する証明書が与えられます。この証明書が、ネットワーク上で実行されるすべての署名操作と検証操作のトラスト・ルートです。
{:shortdesc}

マルチクラウド・ブロックチェーン・ネットワーク内の各組織は、それぞれ独自の CA をデプロイする必要があります。組織の CA は、順序付けプログラム、ピア、アプリケーションなどの、組織が操作するコンポーネントに対する要求に署名します。そのため、他のコンポーネントをデプロイする前に、CA をデプロイする必要があります。

認証局を操作するには、いくつかの前提条件ステップを実行する必要があります。

- [CLI の構成](#ca-kubectl-configure)
- [認証局 URL の取得](#ca-url)
- [CA TLS 証明書のダウンロード](#ca-tls)
- [Fabric CA クライアントのセットアップ](#fabric-ca-client)
- [CA 管理者による証明書の生成](#enroll-admin)

前提条件ステップが完了したら、CA を使用してネットワーク内の新規 ID を組織に登録し、アプリケーションが使用できる証明書を生成できます。

- [CA を使用した順序付けプログラムまたはピアのデプロイ](#deploy-orderer-peer)
<!-- [Register and enroll client applications](#register-enroll-app)-->

また、このガイドでは、CA の操作に関する重要な概念情報と、その証明書を使用してブロックチェーン・ネットワークを管理する方法についても説明します。

- [メンバーシップ・サービス・プロバイダー (MSP)](#msp)
- [TLS 証明書](#tls)

## 前提条件
{: #prerequisites}

ネットワークを設立する場合でも、ネットワークに参加する場合でも、CA を使用して組織に属する他のコンポーネントおよび ID をデプロイするには、以下の前提条件ステップを実行する必要があります。

### CLI の構成
{: #ca-kubectl-configure}

ICP で実行される CA コンテナーに接続するには、**kubectl** コマンド・ライン・ツールを使用する必要があります。

1. ICP クラスター UI にログインします。 **「コマンド・ライン・ツール」**タブにナビゲートし、**「Cloud Private CLI」**をクリックします。ダウンロード可能な以下のツールが表示されます。

   * IBM Cloud Private CLI およびプラグインのインストール
   * Kubectl CLI のインストール
   * Helm のインストール
   * Istio CLI のインストール

  CA を操作するには、最初の **3 つの**ツールを使用する必要があります。このうち、Helm はオプションです。それぞれをクリックして、使用しているマシン・タイプ向けの `curl` コマンドを実行します。次に、ツールごとに `chmod` コマンドと `sudo mv` コマンドを発行します。`chmod` コマンドは該当の CLI の権限を変更して実行可能にし、`sudo mv` コマンドはファイルを移動して名前変更します。

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

  正常に実行された場合、コマンドは以下の例のような応答を返します。ここで、`ca-6cf85f6687-hcxpl` は CA コンテナーの名前です。

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  これで、**kubectl** ツールを使用して CA URL を取得する準備ができました。

3. オプションで、**Helm** を使用する場合は、さらにいくつかのステップを実行します。Helm のインストールはオプションであり、これらの手順では使用する必要がありません。ただし、Helm リリースを管理し、ICP にデプロイする独自のアーカイブを作成すると役立ちます。

  1. ICP UI から「Helm のインストール (Install Helm)」をクリックし、`curl` コマンドを実行します。
  2. 以下のコマンドを実行して、`tar` ファイルを解凍します。  
    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}
  3. `/helm` を `darwin-amd64` に付加して、`sudo mv` コマンドを実行します。Helm に対して `chmod` コマンドを実行する必要はありません。例えば、次のようにします。  
    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  `helm help` コマンドを実行し、Helm が正常にインストールされたことを確認できます。

### 認証局 URL の取得
{: #ca-url}

証明書を生成する要求または新規 ID を登録する要求では、CA URL をターゲットにする必要があります。ご使用の CA URL は、ICP コンソール UI を使用して見つけることができます。以下のステップを実行するには、[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") である必要があります。

1. ICP コンソールにログインし、左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。**「メモ」**セクションでは、CA デプロイメントの操作に役立つ一連のコマンドを確認できます。
5. まだ実行していない場合は、CA コンテナーと対話するために使用する必要がある [kubectl CLI](#ca-kubectl-configure) の構成ステップを実行してください。
6. CLI で、**「1. Get the application URL by running these commands:」**の後にある注の最初のコマンドを実行します。このコマンドは、以下の例のような CA URL とポートを出力します。この値を、後続のコマンドで使用するアプリケーション URL として保存します。

  ```
  http://9.30.94.174:30167
  ```

**注:** ファイアウォールの内側で CA をデプロイする場合は、パススルーを開き、プラットフォーム上のネットワークが CA との TLS ハンドシェークを完了できるようにする必要があります。パススルーは、CA URL と連結されたポートに対してのみ有効にする必要があります。


### CA TLS 証明書の取得
{: #ca-tls}

リモート・クライアントから CA と通信するには、CA TLS 証明書をダウンロードし、それをコマンドと共に渡す必要があります。

1. ICP コンソールにログインします。 左上隅の**「メニュー」**アイコンをクリックします。
2. **「ワークロード (Workload)」** > **「Helm リリース」**をクリックします。
3. Helm リリースの名前を見つけ、Helm リリースの詳細パネルを開きます。
4. パネルの下部にある**「メモ」**セクションまでスクロールダウンします。**「メモ」**セクションでは、CA デプロイメントの操作に役立つ一連のコマンドを確認できます。
5. まだ実行していない場合は、CA コンテナーと対話するために使用する必要がある [kubectl CLI](#ca-kubectl-configure) の構成ステップを実行してください。
6. CLI で、**「3. Get TLS certificate:」**の後にある 3 番目の注のコマンドを実行します。このコマンドは、TLS 証明書を `tls.pem` ファイルとしてローカル・ディレクトリーに保存します。この証明書は、以降のコマンドで参照する必要があります。場所をメモしておいてください。
7. また、このコマンドは証明書を base64 形式に変換し、出力します。結果は、以下のストリングのようになります。

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  **重要:** このストリングをコピーして、`ICP CA TLS 証明書`として保存する必要があります。これは、追加コンポーネントのデプロイ時に使用します。

### Fabric CA クライアントのセットアップ
{: #fabric-ca-client}

Fabric CA クライアントを使用して CA を操作できます。ここでは、Fabric CA クライアントを使用して、組織に属する他のコンポーネントの ID をエンロールおよび登録する方法について説明します。また、Fabric SDK を使用して前提条件ステップを実行することもできます。

1. [Fabric CA クライアント ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-client "Fabric CA クライアントのダウンロード") をローカル・ファイル・システムにダウンロードする必要があります。

  Fabric CA クライアントを入手する最も簡単な方法は、すべての Fabric ツール・バイナリーを直接ダウンロードすることです。コマンド・ラインを使用してバイナリーをダウンロードするディレクトリーにナビゲートし、以下の [Curl ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") コマンドを実行して、バイナリーを取り出します。

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

  このコマンドは、`bin/` ディレクトリーにバイナリーをインストールします。

2. `PATH` パスを、Fabric ツール・バイナリーをダウンロードしたディレクトリーに設定します。

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  例えば、ホーム・ディレクトリーにバイナリーをインストールした場合、`PATH` を以下のように設定します。

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. CA 管理者の証明書を保管するフォルダーを作成します。

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. `$FABRIC_CA_CLIENT_HOME` 環境変数の値を、生成された [MSP](#msp) 証明書を CA クライアントが保管するパスに設定します。以前の試行で作成された構成素材を削除したことを確認してください。以前に `enroll` コマンドを実行したことがない場合は、`msp` フォルダーと `.yaml` ファイルは存在しません。

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. [ICP からダウンロードした](#ca-tls) TLS 証明書を、今後のコマンドで参照できるディレクトリーにコピーします。`$HOME/fabric-ca-client` ディレクトリーにいることを確認してください。

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### CA 管理者による証明書の生成
{: #enroll-ca-admin}

CA を使用してコンポーネントまたはクライアント・アプリケーションをデプロイする前に、新規ユーザーを登録する権限を持つ管理者として自分自身を認証する証明書を生成する必要があります。必要な証明書、秘密鍵、および公開証明書 (エンロール証明書または署名付き証明書とも呼ばれる) を生成するプロセスのことを**エンロール**と呼びます。

証明書を生成するには、認証局に登録されている ID を使用する必要があります。証明書を生成するための ID の名前と機密事項を指定します。**CA 管理者**の ID は、CA のデプロイ時に自動的に登録されています。ここでその管理者名とパスワードを使用して、Fabric CA クライアントで `enroll` コマンドを実行し、その他のピアまたは順序付けプログラムの ID を登録するために使用される証明書を含む MSP フォルダーを生成できます。

1. [Fabric CA クライアントをセットアップ](#fabric-ca-client)するためのステップを完了していること、および `$FABRIC_CA_CLIENT_HOME` が CA 管理者証明書を保管するディレクトリーに設定されていることを確認します。

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. 以下のコマンドを実行して証明書を生成します。

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  コマンドの `<enroll_id>` および `<enroll_password>` は、認証局のデプロイ時に Kubernetes 秘密に渡した [CA 管理者ユーザー名とパスワード](CA_deploy_icp.html#admin-secret)です。`<ca_url_with_port>` 内に [CA URL](#ca-url) を挿入します。先頭の `http://` は省略します。`<ca_name>` は、CA のデプロイ時に`「CA 名 (CA Name)」`フィールドで指定した値です。

  `<ca_tls_cert_path>` は、[CA TLS 証明書](#ca-tls)の絶対パスです。

  実際の呼び出しは以下のコマンド例のようになります。

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  `enroll` コマンドは、メンバーシップ・サービス・プロバイダー (MSP) フォルダーと呼ばれる、証明書の完全なセットを生成します。これは、Fabric CA クライアントの `$HOME` パスに設定したディレクトリー内にあります。例えば、`$HOME/fabric-ca-client/ca-admin` です。MSP および MSP フォルダーの内容について詳しくは、[メンバーシップ・サービス・プロバイダー](#msp)を参照してください。

  `enroll` コマンドが失敗した場合は、考えられる原因について[トラブルシューティングのトピック](#ca-enroll-error)を参照してください。

tree コマンドを実行して、すべての前提条件ステップを完了したことを確認できます。証明書を格納したディレクトリーにナビゲートします。tree コマンドを実行すると、以下の構造に似た結果が生成されます。

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
└── catls
    └── tls.pem    
```

## CA を使用した順序付けプログラムまたはピアのデプロイ
{: #deploy-orderer-peer}

順序付けプログラムまたはピアをデプロイする前に、コンポーネント ID と CA に関する重要な情報を含む構成 JSON ファイルを作成する必要があります。次に、[Kubernetes Secret ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/concepts/configuration/secret/) オブジェクトを使用して、構成時にこのファイルを Helm チャートに渡す必要があります。このファイルにより、順序付けプログラムまたはピアは、必要な証明書を CA から取得して、ブロックチェーン・ネットワークに参加できます。このファイルには、管理者ユーザーとしてコンポーネントを操作できるようにする管理者証明書も含まれています。

以下の手順では、編集してローカル・ファイル・システムに保存できる[テンプレート JSON 構成ファイル](#config-file-template)を示します。また、CA を使用してこのファイルを完成させる方法について順を追って説明します。

- ICP に順序付けプログラムをデプロイする場合、または ICP でホストされている共同事業体に接続するためにピアをデプロイする場合は、以下の手順に従ってください。
- スターター・プランまたはエンタープライズ・プランに接続するためにピアをデプロイする場合は、代わりに[スターター・プランまたはエンタープライズ・プランに接続するための IBM Cloud Private でのピアのデプロイ](/docs/services/blockchain/howto/peer_deploy_ibp.html)の手順に従ってください。これらのステップでは、{{site.data.keyword.blockchainfull_notm}} Platform で CA を使用して ICP にピアをデプロイする方法について順を追って説明しています。

### 構成ファイル
{: #config-file-template}

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

このファイル全体をテキスト・エディターにコピーしてください。テキスト・エディターでこのファイルを編集して、ローカル・ファイル・システムに JSON ファイルとして保存できます。

### CA 接続情報

まず、ICP で CA の接続情報を指定する必要があります。以下の情報を使用して、ファイルの `"component"` セクションの値を入力します。

- `"cahost"` および `"caport"` の値は、[CA URL](#ca-url) の URL とポートです。例えば、CA URL が http://9.30.94.174:30167 の場合、`"cahost"` の値は `9.30.94.174` になり、`"caport"` は `30167` になります。
- `"caname"` は、CA Helm チャートのデプロイ時に指定した CA の名前です。CA の名前は、[CA 管理者 ID](#enroll-admin) を使用してエンロールしたときにコマンドで参照しました。
- `"cacert"` は、CA の base64 でエンコードされた TLS 証明書です。Helm リリースの注を使用して ICP コンソールから [CA の TLS 証明書を取得](#ca-tls)し、以下のセクションに注に示されていたコマンドの base64 出力を挿入します。
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

- 以下の `"tls"` セクションのフィールドには、上記の component セクションに渡したものと同じ情報が必要です。ただし、CA のデプロイメント時に指定された CA TLS インスタンス名の値を使用する必要があります。`"cacert"` 値には同じ TLS 証明書を使用します。
  ```
  "tls": {
    "cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
      "cacert": ""
  ```
  {:codeblock}

認証局の接続情報を取得したら、Fabric CA クライアントを使用していくつかの操作手順を実行する必要があります。

1. [新規コンポーネントを CA に登録します](#register-component)。

2. また、[コンポーネント管理者を登録](#register-admin)し、MSP フォルダー内に[コンポーネント管理者の証明書を生成](#enroll-admin)する必要もあります。別のコンポーネントをデプロイするために既に管理者を登録している場合は、このステップを実行する必要はありません。

3. [新規コンポーネントを TLS CA に登録します](#tls-register-component)。

### CA へのコンポーネント ID の登録
{: #register-component}

順序付けプログラムをデプロイし、それに組織を追加して共同事業体を設立する場合、またはピアをデプロイしてチャネルに参加させる場合は、最初にコンポーネント ID を CA に登録する必要があります。登録すると、コンポーネント・デプロイメントで、ピアまたは順序付けプログラムがネットワークに参加するために必要な証明書を生成できるようになります。

1. Fabric CA クライアントを使用して、[CA 管理者で証明書を生成します](#enroll-ca-admin)。これらの管理者証明書を使用して、以下のコマンドを実行します。`$FABRIC_CA_CLIENT_HOME` が `$HOME/fabric-ca-client/ca-admin` に設定されていることを確認してください。

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. 以下のコマンドを実行して、所属団体名と組織名を確認します。
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  コマンドは次の例のようになります。
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  以下の例のような情報が表示されます。
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  `org1.department1` のような 2 番目の **affiliation** 値をメモします。 この値を、以下のコマンドで使用する必要があります。

3. 次のコマンドを実行して、順序付けプログラムまたはピアを登録します。

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  コンポーネントの名前とパスワードを作成し、それらを使用して `name` および `secret` を置換します。**重要:** この情報はメモしてください。 順序付けプログラムをデプロイする場合は `--id.type` を `orderer` に設定し、ピアをデプロイする場合はこれを `peer` に設定します。コマンドは以下の例のようになります。

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer --id.secret peerpw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  このコマンドを実行したら、構成ファイルの `"component"` セクションの下で、`name` および `secret` に `"enrollid"` および `"enrollsecret"` と入力する必要があります。

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peerpw",
  ```

  一度に登録できる ID は 1 つだけです。 問題が発生した場合は、別のユーザー名とパスワードを使用してコマンドを試行してください。 セキュリティー対策として、各 ID および付随する登録 ID と機密事項を使用してデプロイするピアは 1 つのみにします。複数のコンポーネントに対して 1 つの**管理者** ID を使用することはできますが (これが推奨されるデプロイメント方針です)、ピア ID とパスワードは再使用しないでください。

  コマンドが正常に完了すると、以下の例のような情報が表示されます。

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### 管理者の登録
{: #register-admin}

コンポーネントを登録したら、コンポーネントの操作に使用できる管理者 ID も作成する必要があります。まず、この新しい ID を CA に登録し、それを使用して MSP フォルダーを生成する必要があります。次に、管理者ユーザーの署名付き証明書を構成ファイルに追加する必要があります。デプロイメント時には、これが順序付けプログラムまたはピアの管理者証明書になります。これにより、新規チャネルの開始やピアへのチェーンコードのインストールなどのブロックチェーン・ネットワークの操作を、管理者 ID の証明書を使用して実行できるようになります。

組織に属するコンポーネントに対して、管理者 ID は 1 つのみ作成する必要があります。複数のピアまたは順序付けプログラムをデプロイする場合でも、このステップは 1 回のみ実行する必要があります。1 つのコンポーネントに生成した署名付き証明書を使用して、すべてのピアまたは順序付けプログラムをデプロイできます。

`$FABRIC_CA_CLIENT_HOME` が CA 管理者の MSP のパスに設定されていることを確認します。

```
echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
```
{:codeblock}

以下のコマンドを実行して、コンポーネント管理者 ID を CA に登録します。

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

管理者の新しいユーザー ID の `name` と `secret` を作成します。この ID は、デプロイするすべてのコンポーネントの管理者になります。必ず、先ほど登録したピアまたは順序付けプログラムの ID とは異なる値を使用してください。コマンドは、以下の例のようになります。

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**重要:** この情報はメモしてください。 この `name` と `secret` は、`enroll` コマンドで MSP フォルダーを生成するために使用します。

#### 管理者 MSP フォルダーの生成
{: #enroll-admin}

コンポーネント管理者を登録したら、以下のコマンドを使用して証明書を生成できます。

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

例えば、次のようにします。

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

このコマンドが正常に完了すると、`-M` フラグを使用して指定したディレクトリーに新しい MSP フォルダーが生成されます。このディレクトリーには、コンポーネントの操作に使用する証明書が含まれています。tree コマンドを使用して、enroll コマンドが機能したことを確認できます。証明書を格納したディレクトリーにナビゲートします。tree コマンドを実行すると、以下の構造に似た結果が生成されます。

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
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
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

この MSP ディレクトリーで、以下のコマンドを使用して、新規ユーザーの署名付き証明書ファイルを開き、それを base64 形式に変換します。

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**注:** 上記のコマンドを使用して生成されるストリングは単一行としてフォーマットされることが重要です。以下のようになります。

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

例えば、次のようにします。

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

このコマンドは、以下の例のようなストリングを出力します。

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

このストリングを、構成ファイルの component セクションの下にある `"admincerts"` フィールドにコピーします。

#### TLS CA へのコンポーネント ID の登録
{: #tls-register-component}

順序付けプログラムまたはピアを TLS CA に登録する必要もあります。これを行うには、まず TLS CA の管理者を使用してエンロールする必要があります。`$FABRIC_CA_CLIENT_HOME` を、TLS CA 管理者証明書を保管するディレクトリーに変更します。

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

以下のコマンドを実行して、TLS CA に対する管理者としてエンロールします。このコマンドは、[CA 管理者](#enroll-ca-admin)としてエンロールするために使用したものと同じですが、[CA 構成](CA_deploy_icp.html#icp-ca-configuration-parms)時に指定した CA TLS インスタンス名を使用する点のみが異なります。必ず、CA 秘密と同じ `ca-admin-name` と `ca-admin-password` を使用してください。

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

実際の呼び出しは以下の例のようになります。

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

エンロールが完了すると、コンポーネントを TLS CA に登録するために必要な証明書が得られます。次のコマンドを実行して、順序付けプログラムまたはピアを登録します。

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

このコマンドは、TLS CA 名を使用する点を除き、コンポーネント ID を CA に登録するために使用したコマンドと似ています。ピアではなく順序付けプログラムをデプロイする場合は、`--id.type` を `peer` ではなく `orderer` に設定してください。この ID には、デフォルト CA に対して使用したものとは異なるユーザー名とパスワードを指定する必要があります。実際の登録は、以下のコマンドのようになります。

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

`name` と `secret` の値を、構成ファイルの `"tls"` セクションの下にある `"enrollid"` と `"enrollsecret"` にコピーします。

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### CSR (証明書署名要求) ホスト
{: #csr-hosts}

順序付けプログラムまたはピアをデプロイするには、CSR ホスト名を指定する必要があります。このホスト名には、コンポーネントをデプロイするクラスターのプロキシー IP アドレスと、Helm チャートのデプロイ時に生成された`サービス・ホスト名`が含まれます。

#### クラスター・プロキシー IP アドレスの値の取得

CA をデプロイした同じ ICP クラスターに順序付けプログラムまたはピアをデプロイする場合は、順序付けプログラムまたはピアがデプロイされる ICP クラスターに対する[クラスター管理者 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "クラスター管理者役割とアクション") 役割を持っていることを確認してください。それから、[CA の構成](CA_deploy_icp.html#icp-ca-configuration-parms)時に使用したものと同じプロキシー IP を入力します。順序付けプログラムまたはピアを別のクラスターにデプロイする場合は、以下のステップを実行して、ICP コンソールからクラスター・プロキシー IP アドレスの値を取得できます。

1. ICP コンソールにログインします。 左側のナビゲーション・パネルで**「プラットフォーム」**、**「ノード」**の順にクリックし、クラスターで定義されているノードを表示します。
2. `proxy` 役割を持つノードをクリックし、テーブルから`「ホスト IP」`の値をコピーします。
3. 以下に示す構成ファイルの `"csr"` セクションで、`"hosts"` の値として`ホスト IP` を挿入します。

  ```
  "csr": {
    "hosts": [""]
			}
  ```
  {:codeblock}

#### サービス・ホスト名の判別

`サービス・ホスト名`は、Helm チャートのデプロイ時に生成されます。これは、Helm チャートのデプロイ時に使用する `Helm リリース名`に基づきます。

- 順序付けプログラムをデプロイする場合は、`サービス・ホスト名`はデプロイ時に指定した `helm_release_name` の末尾にストリング `-orderer` を追加したものです。例えば、クラスター IP プロキシー・アドレスが `9.42.134.44` で、`Helm リリース名`に `org1orderer` を指定した場合、以下の値をファイルの `"csr"` セクションに挿入できます。

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- ピアをデプロイする場合は、`サービス・ホスト名`はデプロイメント時に指定した `Helm リリース名`で、追加するストリングはありません。例えば、クラスター IP プロキシー・アドレスが 9.42.134.44 で、`Helm リリース名`が `org1peer1` の場合は、csr "hosts" に以下の値を挿入できます。

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

上記のすべてのステップを完了すると、更新された構成ファイルは以下の例のようになります。

```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer",
            "enrollsecret": "peerpw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
		"tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}

他のフィールドはブランクのままで構いません。このファイルを保存し、[順序付けプログラム](/docs/services/blockchain/howto/orderer_deploy_icp.html)または[ピア](/docs/services/blockchain/howto/peer_deploy_icp.html)のデプロイ時に使用する必要があります。

## メンバーシップ・サービス・プロバイダー (MSP)
{: #msp}

{{site.data.keyword.blockchainfull_notm}} Platform のコンポーネントは、メンバーシップ・サービス・プロバイダー (MSP) を介して ID を使用します。 MSP は、CA が発行する証明書を許可および役割に関連付けます。MSP について詳しくは、[Hyperledger Fabric 資料のメンバーシップのトピック ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Hyperledger Fabric 資料のメンバーシップのトピック") を参照してください。

MSP フォルダーには、Fabric コンポーネントで使用される定義済みの構造が必要です。Fabric CA クライアントは、以下の MSP フォルダーを作成することにより、この構造を確立します。

- **cacerts:** ネットワークのルート CA の証明書が含まれます。
- **intermediatecerts:** これらは、(ルート CA または CA に戻る) トラスト・チェーン内の中間 CA の証明書です。フェイルオーバーと高可用性を実現するために、中間 CA はエンタープライズ・プラン組織ごとに 2 つあります。
- **signCerts:** このフォルダーには、署名付き公開証明書 (署名付き証明書またはエンロール証明書とも呼ばれる) が含まれます。この証明書は、コマンド・ラインから MSP ディレクトリーを参照したり、SDK を使用してユーザー・コンテキスト・オブジェクトを作成したりする際に、ネットワークに対する呼び出し (チェーンコード呼び出しなど) に付加されます。 SDK またはコマンド・ラインからネットワークを操作する場合は、この証明書を IBP にアップロードします。
- **keystore:** このフォルダーには秘密鍵が含まれます。 この鍵は、コマンド・ラインから MSP ディレクトリーを参照したり、SDK を使用してユーザー・コンテキスト・オブジェクトを作成したりする際に、ネットワークに対する呼び出しに署名するために使用されますが、署名付き証明書のように呼び出しに付加されることはありません。この鍵は安全に保管することが**重要**です。漏えいした場合、ご使用の ID になりすますために使用できます。

ネットワーク・モニターや Swagger API を使用して、fabric-ca-client が参照できる MSP フォルダーを作成することもできます。

- **cacerts** および **intermediatecerts**: [Swagger API](/docs/services/blockchain/howto/swagger_apis.html) で `GET` 要求を MSP API に発行すると、これらの証明書を取得できます。
- **signCerts** および **keystore**: 「認証局」パネル上の**「証明書の生成 (Generate Certificates)」**ボタンをクリックすると、これらの証明書を生成できます。 ポップアップ・ウィンドウが開き、2 つの証明書がリストされます。 signCerts に**証明書**を、keystore に **秘密鍵**をコピーして格納してください。 これらの証明書はプラットフォーム上には保管されないので、安全な場所に保管してください。

多くの Fabric コンポーネントの MSP フォルダー内には追加情報が含まれています。 例えば、リモート・ピアを操作する場合、以下のフォルダーを参照することもできます。

- **admincerts:** このフォルダーには、この組織またはコンポーネントの管理者のリストが含まれています。 コマンド・ラインまたは SDK からリモート・ピアを操作する場合は、署名付き証明書をこのフォルダーにアップロードする必要があります。Fabric CA クライアントを使用する場合は、MSP に、管理者証明書として認識される対応する署名付き証明書を含む admincerts フォルダーも必要です。
- **tls:** その他のネットワーク・コンポーネントとの通信に使用する TLS 証明書を、このフォルダーに格納します。

MSP の構造について詳しくは、Hyperledger Fabric 資料で[メンバーシップ ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "メンバーシップ") および[メンバーシップ・サービス・プロバイダー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "メンバーシップ・サービス・プロバイダー") について参照してください。


## CA ログの表示
{: #ca-operate-view-logs}

コンポーネント・ログは、[`kubectl CLI コマンド`](#ca-kubectl-configure)を使用してコマンド・ラインから、または [Kibana ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://www.elastic.co/products/kibana "Elastic Stack への開かれた窓") (ICP クラスターに含まれています) を使用して表示できます。

- `kubectl logs` コマンドを使用して、ポッド内のコンテナー・ログを表示します。ポッド名が不明な場合は、以下のコマンドを実行してポッドのリストを表示します。

   ```
   kubectl get pods
   ```
   {:codeblock}

   次に、次のコマンドの `pod_name` を上記のコマンド出力のポッド名で置き換えて実行し、ポッド内に存在する CA コンテナーのログを取得します。

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   `kubectl logs` コマンドについて詳しくは、[Kubernetes の資料 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”) を参照してください。

- または、[ICP クラスター管理コンソール](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)を使用して、デプロイメント・イベントおよびログにアクセスすることもできます。この場合、ログは Kibana で開きます。

  **注:** Kibana でログを表示する場合、`「No results found」`という応答を受け取ることがあります。この状態は、ICP がワーカー・ノードの IP アドレスをそのホスト名として使用する場合に起こることがあります。この問題を解決するには、パネルの上部で `node.hostname.keyword` で始まるフィルターを削除すると、ログが表示されるようになります。

## トラブルシューティング
{: #ca-operate-troubleshooting}

### **問題:** `enroll` コマンド実行時のエラー
{: #ca-enroll-error}

Fabric CA クライアントの enroll コマンドを実行すると、コマンドが以下のエラーで失敗することがあります。

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**解決策:**

このエラーは、Fabric CA クライアントがエンロールしようとしているが、CA に接続できない場合に発生する可能性があります。これは、次の場合に発生します。   

- `enroll` コマンドの `-u` パラメーターに余分な `https://` が含まれている場合。
- CA 名が正しくない場合。
- ユーザー名またはパスワードが正しくない場合。

`enroll` コマンドで指定したパラメーターを確認し、これらのいずれの状況も存在しないことを確認してください。

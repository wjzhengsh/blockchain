---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# スターター・プランおよびエンタープライズ・プランへのビジネス・ネットワークのデプロイ
{: #deploying-a-business-network}

*[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)*

{{site.data.keyword.IBM}} では、実動の Hyperledger Composer を使用するネットワークをサポートしていません。これには Composer CLI、JavaScript API、REST サーバー、および Web Playground が含まれます。
{:note}

[ビジネス・ネットワーク](/docs/services/blockchain/glossary.html#glossary-business-network)は、{{site.data.keyword.blockchainfull_notm}} Platform 開発者環境と Hyperledger Composer 開発者ツール・セットを使用して開発し、スターター・プランとエンタープライズ・プランのネットワークにデプロイすることができます。
{:shortdesc}

開発者環境で、ブロックチェーンのビジネス・ネットワークを素早くモデル化してテストしたら、それらを {{site.data.keyword.blockchainfull_notm}} Platform のスターター・プランまたはエンタープライズ・プランのネットワークにデプロイできます。

## スターター・プランへのビジネス・ネットワークのデプロイ
{: #deploying-a-business-network-starter-plan}

### 始めに
{: #deploying-a-business-network-before-begin}

[スターター・プランについて](/docs/services/blockchain/starter_plan.html#starter-plan-about)を必ず読み、[スターター・プランの概説](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan)の説明に従ってスターター・プラン・ネットワークを作成しておいてください。

Node v8.9 以上、npm v5.x、および Hyperledger Composer がインストールされていることを確認します。

- ネットワークが Fabric バージョン 1.2 上にある場合は、Hyperledger Composer v0.20.x を使用します。
- ネットワークが Fabric バージョン 1.1 上にある場合は、Hyperledger Composer v0.19.x を使用します。

Fabric のバージョンは、ネットワーク・モニターで[「ネットワーク設定 (Network preferences)」ウィンドウ](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)を開くことで確認できます。

### ステップ 1: 管理シークレットの取得
{: #deploying-a-business-network-retrieve-admin-secret}

1. スターター・プランの概要画面で、**「接続プロファイル」**をクリックしてダウンロードします。 このファイルの名前を「connection-profile.json」に変更します。

2. このファイルを `.bna` ファイルと同じディレクトリーに移動します。

3. 接続プロファイル内で、「registrar」が表示されるまで下方向に移動します。 「registrar」内の「enrollId」の下に、**enrollSecret** というプロパティーがあります。 このシークレットを取得して、そのコピーを保存します。

    ![管理シークレットの取得](images/get_enroll_secret.gif "管理シークレットの取得")

### ステップ 2: 認証局カードの作成
{: #deploying-a-business-network-CA-card}

前のステップで取得したシークレットを使用して、認証局 (CA) 用のビジネス・ネットワーク・カードを作成します。 その後、CA カードをインポートし、スターター・プラン認証局からの有効な証明書用の **enrollSecret** を交換するためにそのカードを使用します。

1. ステップ 1 で記録した **enrollSecret** を使用して以下のコマンドを実行することによって、CA ビジネス・ネットワーク・カードを作成します。

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  上記のコマンドの `enrollSecret` を、接続プロファイルから取得した管理シークレットに置き換えます。

2. 次のコマンドを使用してカードをインポートします。

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. カードがインポートされると、それを使用して CA からの有効な証明書用の **enrollSecret** を交換できます。 次のコマンドを実行して、認証局の証明書を要求します。

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  上記のコマンドの `enrollSecret` を、接続プロファイルから取得した管理シークレットに置き換えます。 `composer identity request` コマンドにより、証明書 `.pem` ファイルを格納する `credentials` ディレクトリーが作成されます。

### ステップ 3: スターター・プラン・インスタンスへの証明書の追加
{: #deploying-a-business-network-add-certs-to-starter-plan}

証明書をスターター・プラン・ネットワークに追加する必要があります。 追加するには、{{site.data.keyword.blockchainfull_notm}} Platform のネットワーク・モニターを使用すると便利です。証明書を追加し、ピアを再始動してから、証明書をチャネル上で同期させる必要があります。 必要な証明書は、前のコマンドで生成された `admin-pub.pem` ファイルです。これは、`credentials` ディレクトリーにあります。

1. スターター・プランのネットワーク・モニターで、**「メンバー」**タブ、**「証明書」**、**「証明書の追加」**の順にクリックします。 `credentials` ディレクトリーに移動し、証明書ボックス内の `admin-pub.pem` ファイルの内容をコピー・アンド・ペーストします。 証明書を送信し、ピアを再始動します。 注: ピアの再始動には 1 分ほどかかります。

    ![証明書の追加](images/add_cert.gif "証明書の追加")

2. 次に、証明書をチャネル上で同期させる必要があります。 **「チャネル」**タブ、**「アクション」**ボタン、**「証明書の同期 (Sync Certificate)」**、および **「送信」**の順にクリックします。

    ![証明書の同期](images/sync_cert.gif "証明書の同期")

### ステップ 4: 管理ビジネス・ネットワーク・カードの作成
{: #deploying-a-business-network-create-admin-card}

これで、正しい証明書がピアとの間で同期されました。次に、Hyperledger Composer ランタイムをインストールしてチェーンコードを開始する権限を持つビジネス・ネットワーク・カードを作成できます。

1. 次のコマンドを使用して、チャネル管理者とピア管理者の役割を持つ管理カードを作成します。

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   このカードは、ビジネス・ネットワークをスターター・プランにデプロイするために使用されます。

2. 次のコマンドを使用して、前のステップで作成したカードをインポートします。

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### ステップ 5: ビジネス・ネットワークのインストールと開始
{: #deploying-a-business-network-install-start-network}

次に、前のステップで作成したカードを使用して、ビジネス・ネットワークをインストールして開始できます。 このガイドでは、車両製造ネットワークのサンプルをインストールし、車両製造サンプルを使用するか、ユーザー独自のビジネス・ネットワークをインストールします。ただし、コマンドに指定されているビジネス・ネットワーク名は必ず変更してください。 ビジネス・ネットワークを開始するコマンドによってカードも作成されます。 スターター・プランの場合、このカードを削除する必要があります。コマンド例ではこのカードに `delete_me.card` という名前を付けて、識別しやすくしています。

1. 次のコマンドを使用して Hyperledger Composer ランタイムをインストールします。

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   このコマンドの実行時に返されるビジネス・ネットワークのバージョン番号をメモします。 これは、次のステップで必要になります。

2. 以下のコマンドを使用して、ビジネス・ネットワークを開始します。 エラーが返された場合は、しばらく待ってから再試行してください。 直前のステップでメモしたバージョン番号を `-V` オプションの後に指定します。

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. `delete_me.card` というビジネス・ネットワーク・カードを削除します。

4. 次のコマンドを使用して、新しいビジネス・ネットワーク・カードを作成し、以前に取得した証明書を参照します。

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. 次のコマンドを使用して、ビジネス・ネットワーク・カードをインポートします。

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

これで、ビジネス・ネットワークがスターター・プラン・インスタンスにデプロイされました。

### ステップ 6: ビジネス・ネットワークを ping して、正しく稼働していることを確認する
{: #deploying-a-business-network-ping-business-network}

以下のコマンドを実行して、ビジネス・ネットワークを ping します。

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

チェーンコード・ログを表示するには、**「チャネル」**をクリックしてから、対象のチャネルを選択します。<!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> **「チェーンコード」**タブをクリックします。 チェーンコードの行を展開し、**「JSON」**または**「ログ」**ボタンをクリックします。

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

これで、ビジネス・ネットワークがスターター・プラン・インスタンスに正常にデプロイされました。

## エンタープライズ・プランへのビジネス・ネットワークのデプロイ
{: #deploying-a-business-network-to-enterprise-plan}

{{site.data.keyword.blockchainfull_notm}} Platform の開発者ツールは、**ビジネス・ネットワーク定義**の作成に役立ちます。この定義は、その後の手順でビジネス・ネットワーク・アーカイブ (`.bna`) にパッケージすることができます。 開発者環境では、開発と共有のために、`.bna` ファイルをローカルまたはクラウドの {{site.data.keyword.blockchain}} にデプロイできます。

このチュートリアルでは、ビジネス・ネットワーク・ライフサイクルの次のステップを説明します。つまり、`.bna` を {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランにデプロイしてビジネス・ネットワークをアクティブ化するステップです。

### 始めに
{: #deploying-a-business-network-enterprise-plan-before-begin}

{{site.data.keyword.blockchainfull_notm}} 開発者環境をインストールします。ビジネス・ネットワークの開発とデプロイの方法を理解しておく必要があります。 ビジネス・ネットワークの作成に関するガイダンスは、[Hyperledger Composer の資料](https://hyperledger.github.io/composer/latest/business-network/business-network-index)から参照できます。

{{site.data.keyword.blockchainfull_notm}} Platform のエンタープライズ・プラン・インスタンスに対するアクセス権限が必要であり、事前にピアを作成しておく必要があります。 {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランについて詳しくは、[エンタープライズ・プランの概説](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)を参照してください。

### ステップ 1: IBM Blockchain Platform の接続プロファイルの作成
{: #deploying-a-business-network-create-connection-profile}

1. 接続の詳細を保管するディレクトリーを作成します。以下に例を示します。

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    各接続プロファイルには、`connection.json` ファイルが含まれている必要があります。 `.composer-connection-profiles` の下に新しいディレクトリーを作成します。この例では `bmx-hlfv11` です。 これは、Hyperledger Composer と {{site.data.keyword.blockchainfull_notm}} Platform で作業するときに使用するプロファイルの名前になります。

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    これで、次のディレクトリー構造が作成されたはずです。

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

### ステップ 2: 接続プロファイルの取得
{: #deploying-a-business-network-retrieve-connection-profile}

1. {{site.data.keyword.blockchainfull_notm}} Platform ダッシュボードで、**「概要」**、**「接続プロファイル」**、**「ダウンロード」**ボタンの順に選択し、接続プロファイルを取得します。

2. 前のステップで作成したディレクトリー構造に接続プロファイルを保存します。 それに **connection.json** という名前を付けます。

### ステップ 3: チャネル情報の追加
{: #deploying-a-business-network-add-channel-information}

1. `connection.json` 内のチャネル値を、作成する予定の、ビジネス・ネットワークをデプロイするチャネルの名前と一致するように追加します。 提供されている接続プロファイル・テンプレートの例では、チャネル・エレメントは `"channels": {},` です。

### ステップ 4: ピアの準備
{: #deploying-a-business-network-prepare-peers}

ビジネス・ネットワークをデプロイするチャネルを作成する前に、このステップを実行する**必要があります**。 このステップをチャネルの作成後に実行すると、デプロイされたビジネス・ネットワークが**開始しない場合があります**。
{:note}

**certificateAuthorities** の下の接続プロファイル文書には属性 **registrar** があり、**enrollId** と **enrollSecret** の属性が次の形式で設定されています。

  ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
  ```

1. 以下のコマンドを使用して、CA のビジネス・ネットワーク・カードを作成します。

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  必ず接続プロファイルと同じディレクトリーでコマンドを実行してください。

2. 次のコマンドを使用して、ビジネス・ネットワーク・カードをインポートします。

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. カードは、インポートされた後、以下のコマンドを使用して認証局から証明書を取得するために使用できます。

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    `composer identity request` コマンドにより、関連する証明書ファイルを格納する資格情報ディレクトリーが作成されます。

4. ナビゲーション・メニューから**「メンバー」**を選択し、**「証明書」**メニュー・オプションを選択し、**「証明書の追加」**ボタンをクリックします。

5. **「名前」**フィールドに、この証明書の固有の名前を入力します。この名前には、ダッシュやハイフンを含めることはできません。

6. 先ほど作成した **admin-pub.pem** ファイルを開き、このファイルの内容を`「鍵」`フィールドにコピーし、**「送信」**ボタンを押します。

7. ユーザー・インターフェースを使用してピアを停止してから、ピアを開始します。

8. これで、証明書のリストに証明書が表示されます。

### ステップ 5: チャネルの作成
{: #deploying-a-business-network-create-your-channel}

1. 左側のパネルのナビゲーション・メニューから**「チャネル」**を選択し、**「新規チャネル」**ボタンをクリックします。

2. チャネル名とオプションの説明を入力して、**「次へ」**をクリックします。 「チャネル名」は、チャネル属性の接続プロファイルで指定する名前と一致する必要があることに注意してください。

3. 必要に応じて権限を付与し、**「次へ」**をクリックします。

4. チャネル更新を受け入れる必要があるオペレーター数に関するポリシーを選択して、要求を送信します。

5. **「通知」**セクションに、レビューが必要な新規要求が示されるはずです。 **「要求の確認」**ボタンをクリックします。

6. **「同意する」**ボタンをクリックすると、**「通知」**セクションに戻り、要求を送信できるようになったことがわかります。 **「要求の確認」**ボタンをクリックして送信ダイアログを開き、**「送信」**ボタンをクリックします。 新しいチャネルが作成されました。

7. ナビゲーション・メニューから**「チャネル」**を選択します。 チャネルのリストに新しいチャネルがあり、「ピアはまだ追加されていません (No peers added yet)」と示されています。 その横にあるアクション・メニューをクリックして、**「ピアの結合」**を選択します。 追加するピアの横のチェック・ボックスにチェック・マークを付けて、**「選択された項目を追加」**を押します。

### ステップ 6: ビジネス・ネットワークを管理する新規 ID の作成
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

要求された証明書を使用して、ビジネス・ネットワーク・カードを作成します。 このビジネス・ネットワーク・カードは、アップロードされたパブリック証明書を持つピアにチェーンコードをインストールする権限を持ち、認証局の発行者になります。

1. カードを作成するには、次のコマンドを実行します。

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    `bmx-hlfv11` は、以前に作成したプロファイルの名前です。

2. カードを作成した後、以下のコマンドを使用してそれをインポートします。

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    これで、`.bna` ファイルを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイする準備ができました。


### ステップ 7: ビジネス・ネットワークのデプロイ
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

これで、`.bna` ファイルを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイできます。

1. 前のステップで作成したカードを使用するには、まずビジネス・ネットワークをインストールし、その後開始する必要があります。 以下のコマンドを使用して、ビジネス・ネットワークをインストールします。

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. ビジネス・ネットワークをインストールした後、以下のコマンドを使用して、ビジネス・ネットワークを開始します。

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. ビジネス・ネットワークが正しくデプロイされていることを確認するため、ネットワークの ping に使用できる ID および関連するビジネス・ネットワーク・カードを作成します。

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. 次のコマンドを使用して、ビジネス・ネットワーク・カードをインポートします。

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. ネットワークを ping して、ネットワークが稼働していることを確認します。

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

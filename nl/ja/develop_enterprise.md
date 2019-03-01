---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# エンタープライズ・プランへのビジネス・ネットワークのデプロイ
{: #deploying-a-business-network}

**デモおよび概念検証のみに Hyperledger Composer を使用することをお勧めします。 IBM では、実動の Hyperledger Composer を使用するネットワークをサポートしていません。これには Composer CLI、JavaScript API、REST サーバー、および Web Playground が含まれます。**

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop の開発者ツールは、**ビジネス・ネットワーク定義**の作成に役立ちます。この定義は、その後の手順でビジネス・ネットワーク・アーカイブ (`.bna`) にパッケージすることができます。 開発者環境では、開発と共有のために、`.bna` ファイルをローカルまたはクラウドの {{site.data.keyword.blockchain}} にデプロイできます。

このチュートリアルでは、ビジネス・ネットワーク・ライフサイクルの次のステップを説明します。つまり、`.bna` を {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランにデプロイしてビジネス・ネットワークをアクティブ化するステップです。

## 始めに

{{site.data.keyword.blockchainfull_notm}} 開発者環境をインストールします。ビジネス・ネットワークの開発とデプロイの方法を理解しておく必要があります。 ビジネス・ネットワークの作成に関するガイダンスは、[Hyperledger Composer の資料](https://hyperledger.github.io/composer/latest/business-network/business-network-index)から参照できます。

{{site.data.keyword.blockchainfull_notm}} Platform のエンタープライズ・プラン・インスタンスに対するアクセス権限が必要であり、事前にピアを作成しておく必要があります。 {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランについて詳しくは、[エンタープライズ・プランの概説](./enterprise_plan.html)を参照してください。

## ステップ 1: {{site.data.keyword.blockchainfull_notm}} Platform の接続プロファイルの作成

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

## ステップ 2: 接続プロファイルの取得

1. {{site.data.keyword.blockchainfull_notm}} Platform ダッシュボードで、**「概要」**、**「接続プロファイル」**、**「ダウンロード」**ボタンの順に選択し、接続プロファイルを取得します。

2. 前のステップで作成したディレクトリー構造に接続プロファイルを保存します。 それに **connection.json** という名前を付けます。

## ステップ 3: チャネル情報の追加

1. `connection.json` 内のチャネル値を、作成する予定の、ビジネス・ネットワークをデプロイするチャネルの名前と一致するように追加します。 提供されている接続プロファイル・テンプレートの例では、チャネル・エレメントは `"channels": {},` です。

## ステップ 4: ピアの準備

**注意**: ビジネス・ネットワークをデプロイするチャネルを作成する前に、このステップを実行する**必要があります**。 このステップをチャネルの作成後に実行すると、デプロイされたビジネス・ネットワークが**開始しない場合があります**。

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

## ステップ 5: チャネルの作成

1. 左側のパネルのナビゲーション・メニューから**「チャネル」**を選択し、**「新規チャネル」**ボタンをクリックします。

2. チャネル名とオプションの説明を入力して、**「次へ」**をクリックします。 「チャネル名」は、チャネル属性の接続プロファイルで指定する名前と一致する必要があることに注意してください。

3. 必要に応じて権限を付与し、**「次へ」**をクリックします。

4. チャネル更新を受け入れる必要があるオペレーター数に関するポリシーを選択して、要求を送信します。

5. **「通知」**セクションに、レビューが必要な新規要求が示されるはずです。 **「要求の確認」**ボタンをクリックします。

6. **「同意する」**ボタンをクリックすると、**「通知」**セクションに戻り、要求を送信できるようになったことがわかります。 **「要求の確認」**ボタンをクリックして送信ダイアログを開き、**「送信」**ボタンをクリックします。 新しいチャネルが作成されました。

7. ナビゲーション・メニューから**「チャネル」**を選択します。 チャネルのリストに新しいチャネルがあり、「ピアはまだ追加されていません (No peers added yet)」と示されています。 その横にあるアクション・メニューをクリックして、**「ピアの結合」**を選択します。 追加するピアの横のチェック・ボックスにチェック・マークを付けて、**「選択された項目を追加」**を押します。

## ステップ 6: ビジネス・ネットワークを管理する新規 ID の作成

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


## ステップ 7: ビジネス・ネットワークのデプロイ

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

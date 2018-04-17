---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# エンタープライズ・プランへのビジネス・ネットワークのデプロイ

{{site.data.keyword.blockchainfull}} Platform: Develop の開発者ツールは、**ビジネス・ネットワーク定義**の作成に役立ちます。この定義は、その後の手順でビジネス・ネットワーク・アーカイブ (`.bna`) にパッケージすることができます。開発者環境では、開発と共有のために、`.bna` ファイルをローカルまたはクラウドの {{site.data.keyword.blockchain}} にデプロイできます。

このチュートリアルでは、ビジネス・ネットワーク・ライフサイクルの次のステップを説明します。つまり、`.bna` を {{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランにデプロイしてビジネス・ネットワークをアクティブ化するステップです。

## 始めに

{{site.data.keyword.blockchainfull}}: Develop 開発者環境をインストールします。ビジネス・ネットワークの開発とデプロイの方法を理解しておく必要があります。ビジネス・ネットワークの作成に関するガイダンスは、[Hyperledger Composer の資料](https://hyperledger.github.io/composer/latest/business-network/business-network-index)から参照できます。

{{site.data.keyword.blockchainfull_notm}} Platform のエンタープライズ・プラン・インスタンスに対するアクセス権限が必要です。{{site.data.keyword.blockchainfull_notm}} Platform エンタープライズ・プランについて詳しくは、[エンタープライズ・プランの概説](./enterprise_plan.html)を参照してください。

## ステップ 1: {{site.data.keyword.blockchainfull_notm}} Platform の接続プロファイルの作成

1. 接続の詳細を保管するディレクトリーを作成します。以下に例を示します。

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    各接続プロファイルには、`connection.json` ファイルが含まれている必要があります。`.composer-connection-profiles` の下に新しいディレクトリーを作成します。この例では `bmx-hlfv1` です。これは、Hyperledger Composer と {{site.data.keyword.blockchainfull_notm}} Platform で作業するときに使用するプロファイルの名前になります。

        mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1

2. これで、次のディレクトリー構造が作成されたはずです。

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    新しく作成されたディレクトリーに `connection.json` という名前のファイルを作成します。次のテンプレートを使用して `connection.json` ファイルを作成できます。

        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }

    新しく作成された `connection.json` ファイルに、{{site.data.keyword.blockchainfull_notm}} Platform ダッシュボードで提供されている属性を設定します。ダッシュボードから**「概説」**を選択し、次に**「サービス資格情報」**ボタンを選択して、チャネルのメンバーのエンドポイントと証明書情報を表示します。

## ステップ 2: 順序付けプログラム情報の追加

1. これで、「サービス資格情報」で提供される情報を使用して、テンプレートの変更を開始できます。サービス資格情報には複数の順序付けプログラムが存在する可能性がありますが、`connection.json` ファイルに必要なのは 1 つのみです。

    テンプレート内の順序付けプログラム URL 値を、次の形式で、サービス資格情報にある関連情報に置き換えます。

        “url”: “grpcs://abca.4.secure.blockchain.ibm.com:12345”

## ステップ 3: 認証局情報の追加

1. `connection.json` 内の ca 値を、**certificateAuthorities** セクションのいずれかの項目の **url** と **caName** の両方に置き換えます。

## ステップ 4: ピア情報の追加

1. 各ピアの **requestURL** と **eventURL** を設定する必要があります。**url** 属性を、サービス資格情報にある **url** 値に置き換えます。**eventURL** 属性を、サービス資格情報にある **eventUrl** に置き換えます。変更を行った後、`connection.json` の peers セクションの形式は次のようになります。

        "peers": [
          {
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"

## ステップ 5: keyValStore 情報の追加

1. 適切なディレクトリーを位置指定するように **keyValStore** 属性を設定します。**keyValStore** に使用するディレクトリーを作成します。例えば、ホーム・ディレクトリーの下に `.composer-credentials-mychannel` という新しいディレクトリーを作成します。**keyValStore** 属性が、以下の形式で、新しく作成されたディレクトリーを位置指定していることを確認します。

        "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",

## ステップ 6: チャネル情報の追加

1. `connection.json` 内のチャネル値を、作成する予定の、ビジネス・ネットワークをデプロイするチャネルの名前と一致するように置き換えます。

## ステップ 7: mspID の追加

`connection.json` ファイル内の **mspID** 値は、mspID (ユーザーの組織) に設定する必要があります。サービス資格情報には、組織とその関連 mspid 値のリストがあります。ユーザーの組織に対応した **mspid** 属性の値を使用する必要があります。

## ステップ 8: globalCert の追加
1. {{site.data.keyword.blockchainfull_notm}} Platform は、順序付けプログラムとピアに共通した TLS 証明書を使用します。順序付けプログラムとピアごとに **tlsCACerts** 属性があり、すべてが同じ証明書に設定されます。`connection.json` ファイル内のダミー値を **tlsCACerts** 値に置き換えます。次の形式にする必要があります。

        "globalCert": "-----BEGIN CERTIFICATE-----\r\.......

## ステップ 9: ピアの準備

**注意**: ビジネス・ネットワークをデプロイするチャネルを作成する前に、このステップを実行する**必要があります**。このステップをチャネル作成後に実行すると、デプロイされたビジネス・ネットワークは**開始しません**。

**certificateAuthorities** の下のサービス資格情報文書には属性 **registrar** があり、**enrollId** と **enrollSecret** の属性が次の形式で設定されています。

        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],

1. 次のコマンドを使用して証明書を要求します。

        composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12

    これにより、ホーム・ディレクトリーの下の `.identityCredentials` ディレクトリーに 3 つのファイルがダウンロードされます。対象となるファイルは、**enrollId** に基づいています。上記の例では、**admin-pub.pem** と **admin-priv.pem** という 2 つのファイルがあります。

3. ナビゲーション・メニューから**「メンバー」**を選択し、**「証明書」**メニュー・オプションを選択し、**「証明書の追加」**ボタンをクリックします。

4. **「名前」**フィールドに、この証明書の固有の名前を入力します。この名前には、ダッシュやハイフンを含めることはできません。

5. 先ほど作成した **admin-pub.pem** ファイルを開き、このファイルの内容を`「鍵」`フィールドにコピーし、**「送信」**ボタンを押します。

6. ユーザー・インターフェースを使用してピアを停止してから、ピアを開始します。

7. これで、証明書のリストに証明書が表示されます。

## ステップ 10: チャネルの作成

1. 左側のパネルのナビゲーション・メニューから**「チャネル」**を選択し、**「新規チャネル」**ボタンをクリックします。

2. チャネル名とオプションの説明を入力して、**「次へ」**をクリックします。「チャネル名」は、チャネル属性の接続プロファイルで指定する名前と一致する必要があることに注意してください。

3. 必要に応じて権限を付与し、**「次へ」**をクリックします。

4. チャネル更新を受け入れる必要があるオペレーター数に関するポリシーを選択して、要求を送信します。

5. **「通知」**セクションに、レビューが必要な新規要求が示されるはずです。**「要求の確認」**ボタンをクリックします。

6. **「同意する」**ボタンをクリックすると、**「通知」**セクションに戻り、要求を送信できるようになったことがわかります。**「要求の確認」**ボタンをクリックして送信ダイアログを開き、**「送信」**ボタンをクリックします。新しいチャネルが作成されました。

7. ナビゲーション・メニューから**「チャネル」**を選択します。チャネルのリストに新しいチャネルがあり、「ピアはまだ追加されていません (No peers added yet)」と示されています。その横にあるアクション・メニューをクリックして、**「ピアの結合」**を選択します。追加するピアの横のチェック・ボックスにチェック・マークを付けて、**「選択された項目を追加」**を押します。

## ステップ 11: ビジネス・ネットワークを管理する新規 ID のインポート

以前に要求した証明書を使用して、Composer で ID を作成します。この新しい ID は、アップロードされたパブリック証明書を持つピアにチェーンコードをインストールする権限を持ち、認証局の発行者になります。

1. 新しい ID を作成するには、次のコマンドを実行します。

        composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem

    `bmx-hlfv1` は、以前に作成したプロファイルの名前です。これで、`.bna` ファイルを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイする準備ができました。


## ステップ 12: ビジネス・ネットワークのデプロイ

これで、`.bna` ファイルを {{site.data.keyword.blockchainfull_notm}} Platform にデプロイできます。

1. 前のステップで作成した ID を使用して、次のコマンドを使用することにより、ビジネス・ネットワークをデプロイします。

        composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString

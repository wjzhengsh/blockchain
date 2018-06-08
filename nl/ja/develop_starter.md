---

copyright:
  years: 2017, 2018
lastupdated: "2018-5-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# スターター・プランでのビジネス・ネットワークのデプロイ
{: #deploying-a-business-network}

{{site.data.keyword.blockchainfull}} Platform 開発者環境と Hyperledger Composer 開発者ツール・セットを使用すると、ビジネス・ネットワークを開発し、スターター・プラン環境にそれをデプロイすることができます。

開発者環境を使用することで、{{site.data.keyword.blockchain}} ビジネス・ネットワークを迅速にモデル化してテストし、それらを {{site.data.keyword.blockchainfull_notm}} Platform のインスタンスにデプロイできます。

## 始めに

[スターター・プランについて](./starter_plan.html)と[スターター・プランの概説](./get_start_starter_plan.html)を必ずお読みください。 また、[{{site.data.keyword.blockchainfull_notm}}Platform 開発者環境](./develop_install.html)を既にインストールし、[スターター・プラン・ネットワークの管理](./get_start_starter_plan.html)の指示に従って {{site.data.keyword.blockchainfull_notm}} Platform スターター・プランのインスタンスを既に作成したことを確認してください。 v8.9 以上のノード、npm v5.x、および v0.19.x の Hyperledger Composer がインストールされていることを確認してください。


## ステップ 1: 管理シークレットの取得

1. スターター・プランの概要画面で、**「接続プロファイル」**をクリックしてダウンロードします。このファイルの名前を「connection-profile.json」に変更します。

2. このファイルを .bna ファイルと同じディレクトリーに移動します。

3. 接続プロファイル内で、「registrar」が表示されるまで下方向に移動します。「registrar」内の「enrollId」の下に、**enrollSecret** というプロパティーがあります。このシークレットを取得して、そのコピーを保存します。

![D8KBag](https://i.makeagif.com/media/4-12-2018/D8KBag.gif)


## ステップ 2: 認証局カードの作成

前のステップで取得したシークレットを使用して、認証局 (CA) 用のビジネス・ネットワーク・カードを作成します。 その後、CA カードをインポートし、スターター・プラン認証局からの有効な証明書用の **enrollSecret** を交換するためにそのカードを使用します。

1. ステップ 1 で記録した **enrollSecret** を使用して以下のコマンドを実行することによって、CA ビジネス・ネットワーク・カードを作成します。

        composer card create -f ca.card -p connection-profile.json -u admin -s ${enrollSecret}

2. 次のコマンドを使用してカードをインポートします。

        composer card import -f ca.card -c ca

3. カードがインポートされると、それを使用して CA からの有効な証明書用の **enrollSecret** を交換できます。 次のコマンドを実行して、認証局の証明書を要求します。

        composer identity request --card ca --path ./credentials -u admin -s ${enrollSecret}

`composer identity request` コマンドにより、証明書 `.pem` ファイルを格納する `credentials` ディレクトリーが作成されます。

## ステップ 3: スターター・プラン・インスタンスへの証明書の追加

証明書をスターター・プラン・インスタンスに追加する必要があります。 追加するには、{{site.data.keyword.blockchainfull_notm}} Platform UI を使用すると便利です。 証明書を追加し、ピアを再始動してから、証明書をチャネル上で同期させる必要があります。必要な証明書は、前のコマンドで生成された `admin-pub.pem` ファイルです。これは、`credentials` ディレクトリーにあります。

1. スターター・プランの UI で、**「メンバー」**タブ、**「証明書」**、**「証明書の追加」**の順にクリックします。`credentials` ディレクトリーに移動し、証明書ボックス内の `admin-pub.pem` ファイルの内容をコピー・アンド・ペーストします。証明書を送信し、ピアを再始動します。注: ピアの再始動には 1 分ほどかかります。

    ![jlEb2y](https://i.makeagif.com/media/4-12-2018/jlEb2y.gif)

2. 次に、証明書をチャネル上で同期させる必要があります。**「チャネル」**タブ、**「アクション」**ボタン、**「証明書の同期 (Sync Certificate)」**、および **「送信」**の順にクリックします。

![E-sVV5](https://i.makeagif.com/media/4-12-2018/E-sVV5.gif)

## ステップ 4: 管理ビジネス・ネットワーク・カードの作成

これで、正しい証明書がピアとの間で同期されました。次に、Hyperledger Composer ランタイムをインストールしてチェーンコードを開始する権限を持つビジネス・ネットワーク・カードを作成できます。

1. 次のコマンドを使用して、チャネル管理者とピア管理者の役割を持つ管理カードを作成します。

        composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    このカードは、ビジネス・ネットワークをスターター・プランにデプロイするために使用されます。

2. 次のコマンドを使用して、前のステップで作成したカードをインポートします。

        composer card import -f adminCard.card -c adminCard

## ステップ 5: ビジネス・ネットワークのインストールと開始

次に、前のステップで作成したカードを使用して、ビジネス・ネットワークをインストールして開始できます。 このガイドでは、車両製造ネットワークのサンプルをインストールし、車両製造サンプルを使用するか、ユーザー独自のビジネス・ネットワークをインストールします。ただし、コマンドに指定されているビジネス・ネットワーク名は必ず変更してください。ビジネス・ネットワークを開始するコマンドによってカードも作成されます。 スターター・プランの場合、このカードを削除する必要があります。コマンド例ではこのカードに `delete_me.card` という名前を付けて、識別しやすくしています。

1. 次のコマンドを使用して Hyperledger Composer ランタイムをインストールします。

        composer network install -c adminCard -a vehicle-manufacture-network.bna

    このコマンドの実行時に返されるビジネス・ネットワークのバージョン番号をメモします。これは、次のステップで必要になります。

2. 以下のコマンドを使用して、ビジネス・ネットワークを開始します。エラーが返された場合は、しばらく待ってから再試行してください。直前のステップでメモしたバージョン番号を `-V` オプションの後に指定します。

        composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. `delete_me.card` というビジネス・ネットワーク・カードを削除します。

4. 次のコマンドを使用して、新しいビジネス・ネットワーク・カードを作成し、以前に取得した証明書を参照します。

        composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. 次のコマンドを使用して、ビジネス・ネットワーク・カードをインポートします。

        composer card import -f ./admin@vehicle-manufacture-network.card

これで、ビジネス・ネットワークがスターター・プラン・インスタンスにデプロイされました。

## ステップ 6: ビジネス・ネットワークを ping して、正しく稼働していることを確認する

1. 以下のコマンドを実行して、ビジネス・ネットワークを ping します。

        composer network ping -c admin@vehicle-manufacture-network

チェーンコード・ログを表示するには、**「チャネル」**をクリックしてから、対象のチャネルを選択します。ドロップダウン矢印をクリックしてログを表示するか、「アクション」記号をクリックして詳細を表示します。

![fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif)

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

# スターター・プランでのビジネス・ネットワークのデプロイ
{: #deploying-a-business-networks-on-starter-plan}

{{site.data.keyword.blockchainfull}} Platform: Develop 開発者環境と Hyperledger Composer 開発者ツール・セットを使用すると、ビジネス・ネットワークを開発し、スターター・プラン環境にそれをデプロイすることができます。

開発者環境を使用することで、{{site.data.keyword.blockchain}} ビジネス・ネットワークを迅速にモデル化してテストし、それらを {{site.data.keyword.blockchainfull_notm}} Platform のインスタンスにデプロイできます。

## 始めに

[スターター・プランについて](./starter_plan.html)と[スターター・プランの概説](./get_start_starter_plan.html)を必ずお読みください。また、[{{site.data.keyword.blockchainfull_notm}}Platform: Develop 開発者環境](./develop_install.html)を既にインストールし、[スターター・プラン・ネットワークの管理](./get_start_starter_plan.html)の指示に従って {{site.data.keyword.blockchainfull_notm}} Platform スターター・プランのインスタンスを既に作成したことを確認してください。


## ステップ 1: 管理シークレットの取得

1. スターター・プランの概説画面で、**「接続プロファイル」**をクリックします。

2. 接続プロファイルの中に **admin secret** プロパティーがあります。このシークレットを取得して、そのコピーを保存します。

## ステップ 2: 認証局カードの作成

前のステップで取得したシークレットを使用して、認証局 (CA) 用のビジネス・ネットワーク・カードを作成します。その後、CA カードをインポートし、スターター・プラン認証局からの有効な証明書用の **admin secret** を交換するためにそのカードを使用します。

1. ステップ 1 で記録したシークレットを使用して以下のコマンドを実行することによって、CA ビジネス・ネットワーク・カードを作成します。

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. 次のコマンドを使用してカードをインポートします。

        composer card import -f ca.card -n ca

3. カードがインポートされると、それを使用して CA からの有効な証明書用の **admin secret** を交換できます。次のコマンドを実行して、認証局の証明書を要求します。

        composer identity request --card ca --path ./credentials

`composer identity request` コマンドにより、証明書 `.pem` ファイルを格納する `credentials` ディレクトリーが作成されます。

## ステップ 3: スターター・プラン・インスタンスへの証明書の追加

証明書をスターター・プラン・インスタンスに追加する必要があります。追加するには、{{site.data.keyword.blockchainfull_notm}} Platform API を使用すると便利です。証明書を追加した後、ピアを停止して再始動し、証明書を同期する必要があります。API 呼び出しの変数に必要なデータを、スターター・プラン・ネットワーク・モニターの**「接続プロファイル」**から取得できます。

1. 次の API 呼び出しを使用して証明書を追加します。

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    上記の例では、本文がブランクのままになっています。以下のコードは、API 要求の本文の例です。

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. 次の API 呼び出しを使用してピアを停止します。

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. 次の API 呼び出しを使用してピアを再始動します。

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. 次の API 呼び出しを使用して証明書を同期します。

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## ステップ 4: 管理ビジネス・ネットワーク・カードの作成

これで、正しい証明書がピアとの間で同期されました。次に、Hyperledger Composer ランタイムをインストールしてチェーンコードを開始する権限を持つビジネス・ネットワーク・カードを作成できます。

1. 次のコマンドを使用して、チャネル管理者とピア管理者の役割を持つ管理カードを作成します。

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    このカードは、ビジネス・ネットワークをスターター・プランにデプロイするために使用されます。

2. 次のコマンドを使用して、前のステップで作成したカードをインポートします。

        composer card import -f adminCard.card -n adminCard

## ステップ 5: ビジネス・ネットワークのインストールと開始

次に、前のステップで作成したカードを使用して、ビジネス・ネットワークをインストールして開始できます。このガイドでは、車両製造業ネットワーク・サンプルをインストールします。ビジネス・ネットワークを開始するコマンドによってカードも作成されます。スターター・プランの場合、このカードを削除する必要があります。コマンド例ではこのカードに `delete_me.card` という名前を付けて、識別しやすくしています。

1. 次のコマンドを使用して Hyperledger Composer ランタイムをインストールします。

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. 次のコマンドを使用して、ビジネス・ネットワークを開始します。

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. `delete_me.card` というビジネス・ネットワーク・カードを削除します。

4. 次のコマンドを使用して、新しいビジネス・ネットワーク・カードを作成し、以前に取得した証明書を参照します。

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. 次のコマンドを使用して、ビジネス・ネットワーク・カードをインポートします。

        composer card import -f ./admin@vehicle-manufacture-network.card

これで、ビジネス・ネットワークがスターター・プラン・インスタンスにデプロイされました。

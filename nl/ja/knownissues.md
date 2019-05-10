---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# 既知の問題
{: #known-issues}

このページでは、スターター・プランかエンタープライズ・プランの使用時に発生する可能性のある既知の問題について説明します。
{:shortdesc}

以下の問題が既に報告されています。
- **外部 CA の構成は現時点ではサポートされていません**。 代わりに、ネットワーク・モニターから管理者証明書を生成してアップロードできます。 詳しくは、[クライアント・サイドの証明書の生成](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel)、およびネットワーク・モニターの[「メンバー」画面の「証明書」タブ](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members)に関する説明を参照してください。
- スターター・プラン・ネットワークのネットワーク・モニターでは、「概要」画面にリストされているノードに対して**「ログの表示 (View Logs)」**をクリックすると、「{{site.data.keyword.cloud}} Logging」Kibana インターフェースが開きます。 **デフォルトでは、Kibana は直近 30 日間のアクティビティーのログを表示するように事前構成されています**。 直近 30 日間にアクティビティーがない場合は、「*No results found*」というメッセージが表示されます。 他のログを表示するには、ユーザー名の下の右上隅にあるタイマー・アイコンをクリックし、*過去 1 年間* などの広い時刻範囲を設定します。
- スターター・プラン・ネットワークのログは、[{{site.data.keyword.cloud_notm}} Log Analysis サービス ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://cloud.ibm.com/catalog/services/log-analysis) によって収集されます。 デフォルトでは、ログは Log Analysis サービスのライト・プランで収集されます。 このプランは無料であり、**1 日にログの最初の 500 MB の検索のみが可能**です。 ネットワークのログが 500 MB を超えている場合、Kibana で新規ログを表示することができません。 ネットワークで 500 MB より大きいログが生成される場合は、有料版の Log Analysis サービスにアップグレードすることができます。
- スターター・プランは実稼働環境ではないため、**アプリケーションが即座にネットワーク・リソースに到達できない場合があります**。
  - これが発生する場合は、最初のステップとして、Fabric SDK でデフォルトのタイムアウト値を大きくすることをお勧めします。 タイムアウト値の設定方法について詳しくは、[Fabric SDK でのタイムアウト値の設定](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk)を参照してください。
  - アプリケーション・レベルで要求を再試行することもできます。
- バックグラウンドでのネットワークの問題により**チェーンコード・コンテナーが停止することがあり**、ユーザーにより呼び出された後に再構築および再始動が必要になる場合があります。 これが発生する場合は、チェーンコードが応答するまで数分かかることがあります。
- スターター・プラン・ネットワークでのリソース制限 (ピアごとに 1 CPU と 4 Gi RAM) のため、**チェーンコードのインスタンス化中に `REQUEST_TIMEOUT` エラーが発生する場合があります**。 これが発生する場合は、インスタンス化ステップを再試行してください。 エラーが続く場合は、チェーンコードのインスタンス化のタイムアウトを増やすことができます。 接続プロファイルで、チェーンコードのインスタンス化のタイムアウトは 300 秒に設定されています。
  - SDK でデフォルトのタイムアウト値を使用する場合、接続プロファイルの **client** セクション (タイムアウトが 300 秒に設定されています) を以下に示すようにコピーし、SDK でこれが読み取られるようにします。 ノード SDK の場合は、接続プロファイルでのこのタイムアウト設定が `invoke`、`queries` などのすべての呼び出しに影響を与えることに注意してください。
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - SDK でチェーンコードのインスタンス化コマンドのタイムアウト設定を上書きした場合、これをデフォルト値に設定し直すか、300 秒以上の値に変更します。
    - ノード SDK を使用する場合、`sendInstantiateProposal(request, timeout)` メソッドのタイムアウト設定を変更することができます。 詳しくは、[sendInstantiateProposal(request, timeout) ![外部リンク・アイコン](images/external_link.svg "外部リンク・アイコン")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal) を参照してください。
    - Java SDK を使用する場合、`instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)` コマンドのタイムアウト設定を変更することができます。これは `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java` ファイル内にあります。

{{site.data.keyword.cloud_notm}} における {{site.data.keyword.blockchainfull_notm}} Platform ネットワークに関するサポートとヘルプについては、[サポートについて](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support)を参照してください。

## エンタープライズ・プラン・ネットワークのアップグレードのためのチェーンコードの更新
{: #known-issues-ibp-about-chaincode-migration}

エンタープライズ・プラン・ネットワークのレベルが Hyperledger Fabric V1.0 の場合、{{site.data.keyword.blockchainfull_notm}} Platform により、ネットワークを Hyperledger Fabric V1.1 にマイグレーションするためのアップグレードがスケジュールされます。 ネットワークのアップグレードでは、従属関係を含む複雑なチェーンコードを使用している場合、チェーンコード内の従属関係を更新する必要があります。 チェーンコード内の従属関係を更新しない場合、サービスの中断が発生する可能性があります。

**注意**:
- 1 つの `.go` または `.js` ファイルを含む単純なチェーンコードを使用する場合、チェーンコードを更新する必要はありません。
- 更新したチェーンコードが古いネットワークで機能しないことがあります。このため、スケジュールされたネットワークのアップグレードの後に、チェーンコードを更新する必要があります。
- チェーンコードを、スターター・プラン・ネットワークにインストールしてインスタンス化することによってテストすることができます。 スターター・プランで機能するチェーンコードはすべて、ネットワークのアップグレード後のエンタープライズ・プランでも機能します。

チェーンコードを更新するには、以下の手順を実行します。
1. 任意の Golang ベンダリング・ツールを使用してチェーンコードを更新します。 最も簡単なのは、元のファイルに従属関係を含めたときに使用したものと同じツールを使用することです。 例えば、チェーンコードで、多くの Fabric サンプルで使用される **govendor** ツールが使用される場合、vendor フォルダーの上位のディレクトリーで以下のコマンドを実行して、チェーンコードの従属関係を更新することができます。
    ```
    govendor update all +v
    ```
    {:codeblock}

    これで、`go build` を使用して、新規コードがコンパイルされるかどうか、およびチェーンコードの更新が機能するかどうかを確認することができます。

2. ネットワークのアップグレード後に、ネットワーク・モニターで[チェーンコードを更新](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc)することができます。

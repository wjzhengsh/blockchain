---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-29"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 既知の問題

以下の問題が既に報告されています。
- 外部 CA の構成は現時点ではサポートされていません。  代わりに、ネットワーク・モニターから管理者証明書を生成してアップロードできます。 詳しくは、[クライアント・サイドの証明書の生成](v10_application.html#generating-the-client-side-certificates)、およびネットワーク・モニターの[「メンバー」画面の「証明書」タブ](v10_dashboard.html#members)に関する説明を参照してください。
- Starter Plan ネットワークのネットワーク・モニターでは、「概要」画面にリストされているノードに対して**「ログの表示 (View Logs)」**をクリックすると、「{{site.data.keyword.cloud}} Logging」 kibana インターフェースが開きます。デフォルトでは、kibana は直近 15 分間のアクティビティーのログを表示するように事前構成されています。直近 15 分間にアクティビティーがない場合は、「No results found」というメッセージが表示されます。他のログを表示するには、ユーザー名の下の右上隅にあるタイマー・アイコンをクリックし、「今週」や「今月」などの広い時間範囲を設定します。
- プロキシーのフォールオーバーなどの安定性に関する問題が発生した場合は、再始動が必要になります。再始動する前に、アプリケーションの Fabric SDK のデフォルトのタイムアウト値を大きな数値で上書きしてみてください。タイムアウト値の設定方法について詳しくは、[Fabric SDK でのタイムアウト値の設定](v10_application.html#set-timeout-in-sdk)を参照してください。

{{site.data.keyword.Bluemix}} における {{site.data.keyword.blockchainfull_notm}} Platform ネットワークに関するサポートとヘルプについては、[サポートについて](ibmblockchain_support.html)を参照してください。

<!--
## Updating chaincode with Enterprise Plan migration to Hyperledger Fabric 1.1
-	Users who migrate from networks based on Hyperledger Fabric 1.0 to networks based on Fabric 1.1 will need to update the dependencies in their chaincode. If they do not, there is a risk of a service disruption.
- This does not apply to users that uploaded their chaincode without dependencies, using a .go file.
- **Update your chaincode using the following steps:**
  **1.** You can use any  golang vendoring tool to update your chaincode. It will be easiest to use the same tool that was used to include dependencies in the original file. Many early Fabric samples used the govendor tool. If your chaincode used govendor, you can update your dependencies using the following command in the directory above the vendor folder.
      govendor update all +v
  * You can use `go build` to check that the new code compiles and that the update worked.
  * You can test your chaincode by installing and instantiating it on a Starter Plan Network. All chaincode that works on Starter Plan will also work on Enterprise Plan after the upgrade.
  **2.** Once your chaincode dependencies are up to date, you can use the network monitor to [update your chaincode](install_instatiate_chaincode.html#Updating a chaincode).
-->

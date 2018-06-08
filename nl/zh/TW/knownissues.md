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


# 已知的問題

已報告下列問題：
- 尚未支援配置外部 CA。作為替代方案，您可以透過「網路監視器」來產生及上傳管理憑證。如需相關資訊，請參閱[產生用戶端憑證](v10_application.html#generating-the-client-side-certificates)，以及「網路監視器」中[「成員」畫面的「憑證」標籤](v10_dashboard.html#members)上的說明。
- 在「入門範本方案」網路的「網路監視器」中，當您在「概觀」畫面所列的節點上按一下**檢視日誌**時，即會開啟「{{site.data.keyword.cloud}} 記載」Kibana 介面。依預設，會預先配置 Kibana，以顯示最後 15 分鐘活動的日誌。如果在最後 15 分鐘沒有任何活動，則您會看到一則訊息指出「找不到任何結果」。若要檢視其他日誌，您可以按一下使用者名稱右上角的計時器圖示，並設定更廣泛的時間範圍，例如「本週」或「本月」。
- 您可能會遇到穩定性問題，例如 Proxy 失效接手，您需要重新啟動。重新啟動之前，請嘗試改寫具有較大數目之應用程式的 Fabric SDK 中的預設逾時值。如需設定逾時值的相關資訊，請參閱[設定 Fabric SDK 中的逾時值](v10_application.html#set-timeout-in-sdk)。

如需 {{site.data.keyword.Bluemix}} 上「{{site.data.keyword.blockchainfull_notm}} 平台」網路的支援及協助，請參閱[取得支援](ibmblockchain_support.html)。

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

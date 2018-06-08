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


# 已知问题

已报告以下问题：
- 不支持配置外部 CA。或者，您可以通过“网络监视器”来生成和上传管理证书。有关更多信息，请参阅[生成客户机端证书](v10_application.html#generating-the-client-side-certificates)和有关“网络监视器”中[“成员”屏幕的“证书”选项卡](v10_dashboard.html#members)的描述。
- 在入门套餐网络的“网络监视器”中，在单击“概述”屏幕上列出的节点的**查看日志**时，将打开 {{site.data.keyword.cloud}} 日志记录 Kibana 界面。缺省情况下，Kibana 预先配置为显示最近 15 分钟的活动的日志。如果过去 15 分钟内没有任何活动，那么您将看到消息“找不到结果”。要查看其他日志，可单击右上角中用户名下的计时器图标，并设置较大的时间范围，例如，“本周”或“本月”。
- 您可能会遇到稳定性问题，例如，代理发生故障转移，并且需要重新启动。在重新启动之前，尝试以更大的数字改写应用程序的 Fabric SDK 中的缺省超时值。有关设置超时值的更多信息，请参阅[在 Fabric SDK 中设置超时值](v10_application.html#set-timeout-in-sdk)。

有关 {{site.data.keyword.Bluemix}} 上 {{site.data.keyword.blockchainfull_notm}} Platform 网络的支持和帮助，请参阅[获取支持](ibmblockchain_support.html)。

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

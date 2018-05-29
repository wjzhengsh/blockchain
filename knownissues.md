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


# Known issues

The following issues are already reported:
- Configuring an external CA in not supported yet.  As an alternative, you can generate and upload admin certificates via the Network Monitor. For more information, see [Generating the client-side certificates](v10_application.html#generating-the-client-side-certificates) and the description on the ["Certificates" tab of "Member" screen](v10_dashboard.html#members) in the Network Monitor.
- In the Network Monitor of a Starter Plan network, when you click **View Logs** on the nodes that are listed on the "Overview" screen, the {{site.data.keyword.cloud}} Logging kibana interface is opened. By default, kibana is preconfigured to show logs from the last 15 minutes of activity. If there is no activity in the last 15 minutes, you will see a message that says 'No results found'. To view other logs, you can click the timer icon in the upper right corner under your user name and set a broader time range, such as 'This week' or 'This month'.
- You might encounter stability issues, such as proxies falling over, and you are required to start again. Before you restart, try to overwrite the default timeout values in Fabric SDKs of your applications with bigger numbers. For more information about setting timeout values, see [Setting timeout values in Fabric SDKs](v10_application.html#set-timeout-in-sdk).

For support and help with your {{site.data.keyword.blockchainfull_notm}} Platform network on {{site.data.keyword.Bluemix}}, see [Getting support](ibmblockchain_support.html).

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

---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Known issues

The following issues are already reported:
- **Configuring an external CA in not supported yet**. As an alternative, you can generate and upload admin certificates via the Network Monitor. For more information, see [Generating the client-side certificates](v10_application.html#generating-the-client-side-certificates) and the description on the ["Certificates" tab of "Member" screen](v10_dashboard.html#members) in the Network Monitor.  
- In the Network Monitor of a Starter Plan network, when you click **View Logs** on the nodes that are listed on the "Overview" screen, the {{site.data.keyword.cloud}} Logging Kibana interface opens. **By default, Kibana is preconfigured to show logs from the last 15 minutes of activity**. If there is no activity in the last 15 minutes, you will see a message that says *No results found*. To view other logs, you can click the timer icon in the upper right corner under your user name and set a broader time range, such as *This week* or *This month*.  
- The logs of your Starter Plan network are gathered by the [{{site.data.keyword.cloud_notm}} Log Analysis service ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/log-analysis). By default, your logs are collected by the Lite Plan of the Log Analysis service. This plan is free and **only allows you to search the first 500 MB of your logs per day**. If your network's logs exceed 500 MB, you cannot view new logs in Kibana. If your network generates more than 500 MB of logs, you can upgrade to a paid version of the Log Analysis Service.  
- Because Starter Plan is not a production environment, **applications might not be able to immediately reach a network resource**.
  - If this happens, it is recommended as a first step to increase the default timeout values in the Fabric SDK. For more information about setting timeout values, see [Setting timeout values in Fabric SDKs](v10_application.html#set-timeout-in-sdk).
  - You can also retry your request at the application level.  
- **Chaincode containers can sometimes be stopped** by a background network issue and may need to be rebuilt and restarted after being invoked by a user. If this happens it may take your chaincode a few minutes to respond.
<!--
- Because of the resource limitation in Starter Plan network, that is, 1 CPU and 2 Gi RAM for each peer, you might encounter a delay when you instantiate chaincode in the Network Monitor or **you might encounter a `REQUEST_TIMEOUT` error during chaincode instantiation with your application**. If this happens, retry the instantiation step. If the error continues, you can increase the chaincode instantiation event timeout. For example, change the timeout setting in the `sendInstantiateProposal(request, timeout)` method of Node SDK from the default value of 45 seconds to 130 seconds. -->

For support and help with your {{site.data.keyword.blockchainfull_notm}} Platform network on {{site.data.keyword.cloud_notm}}, see [Getting support](ibmblockchain_support.html).


## Updating chaincode for Enterprise Plan network upgrade
{: #chaincode-migration}

If your Enterprise Plan network is at Hyperledger Fabric V1.0 level, {{site.data.keyword.blockchainfull_notm}} Platform will schedule an upgrade for your network to migrate to Hyperledger Fabric V1.1. With the network upgrade, if you use complex chaincode with dependencies, you need to update the dependencies in your chaincode. Otherwise, you might encounter a service disruption.

**Notes**:
- If you use simple chaincode with a single `.go` or `.js` file, you don't need to update your chaincode.
- The updated chaincode might not work on your old network; therefore, you need to update your chaincode after the scheduled network upgrade.
- You can test your chaincode by installing and instantiating it in a Starter Plan network. All chaincode that works on Starter Plan will also work on Enterprise Plan after the network upgrade.

Perform the following steps to update your chaincode:
1. Use any Golang vendoring tool to update your chaincode. It is the easiest to use the same tool that was used to include dependencies in the original file. For example, if your chaincode uses the **govendor** tool that many Fabric samples use, you can run the following command in the directory above the vendor folder to update your chaincode dependencies:
    ```
    govendor update all +v
    ```
    {:codeblock}

    You can then use `go build` to check whether the new code compiles and whether the chaincode update works.

2. After your network upgrade, you can [update your chaincode](howto/install_instantiate_chaincode.html#updating-a-chaincode) in the Network Monitor.

---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Known issues
{: #known-issues}
This page describes known issues you may encounter when using Starter or Enterprise Plans.
{:shortdesc}

***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


The following issues are already reported:
- **Configuring an external CA in not supported yet**. As an alternative, you can generate and upload admin certificates via the Network Monitor. For more information, see [Generating the client-side certificates](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel) and the description on the ["Certificates" tab of "Member" screen](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) in the Network Monitor.
- In the Network Monitor of a Starter Plan network, when you click **View Logs** on the nodes that are listed on the "Overview" screen, the {{site.data.keyword.cloud}} Logging Kibana interface opens. **By default, Kibana is preconfigured to show logs from the last 30 days of activity**. If there is no activity in the last 30 days, you will see a message that says *No results found*. To view other logs, you can click the timer icon in the upper right corner under your user name and set a broader time range, such as *Year to date*
- The logs of your Starter Plan network are gathered by the [{{site.data.keyword.cloud_notm}} Log Analysis service ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/log-analysis). By default, your logs are collected by the Lite Plan of the Log Analysis service. This plan is free and **only allows you to search the first 500 MB of your logs per day**. If your network's logs exceed 500 MB, you cannot view new logs in Kibana. If your network generates more than 500 MB of logs, you can upgrade to a paid version of the Log Analysis Service.
- Because Starter Plan is not a production environment, **applications might not be able to immediately reach a network resource**.
  - If this happens, it is recommended as a first step to increase the default timeout values in the Fabric SDK. For more information about setting timeout values, see [Setting timeout values in Fabric SDKs](/docs/services/blockchain/v10_application.html#dev-app-set-timeout-in-sdk).
  - You can also retry your request at the application level.
- **Chaincode containers can sometimes be stopped** by a background network issue and may need to be rebuilt and restarted after being invoked by a user. If this happens it may take your chaincode a few minutes to respond.
- Because of the resource limitation in Starter Plan network, that is, 1 CPU and 4 Gi RAM for each peer, **you might encounter a `REQUEST_TIMEOUT` error during chaincode instantiation**. If this happens, retry the instantiation step. If the error continues, you can increase the chaincode instantiation timeout. In the connection profile, the chaincode instantiation timeout is set to 300 seconds.
  - If you use the default timeout value in SDK, copy the **client** section in connection profile as shown below, which sets the timeout to 300 seconds, and ensure that your SDK reads it. Note that for Node SDK, this timeout setting in connection profile affects all calls, such as `invoke`, `queries`, and so on.
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
  - If you overwrite the timeout setting of chaincode instantiation commands in your SDKs, either set it back to the default value or change it to 300 seconds or more.
    - If you use Node SDK, you can change the timeout setting of the `sendInstantiateProposal(request, timeout)` method. For more information, see [sendInstantiateProposal(request, timeout) ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal).
    - If you use Java SDK, you can change the timeout setting of the `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)` command, which is in the `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java` file.

For support and help with your {{site.data.keyword.blockchainfull_notm}} Platform network on {{site.data.keyword.cloud_notm}}, see [Getting support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

## Updating chaincode for Enterprise Plan network upgrade
{: #known-issues-ibp-about-chaincode-migration}

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

2. After your network upgrade, you can [update your chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) in the Network Monitor.

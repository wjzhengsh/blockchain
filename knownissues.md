---

copyright:
  years: 2017, 2019

lastupdated: "2019-06-18"

subcollection: blockchain

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:external: target="_blank" .external}

# Known issues
{: #known-issues}

This page describes known issues that you might encounter when you use Starter or Enterprise Plans.
{:shortdesc}

The following issues are already reported:
- **Configuring an external CA in not supported yet**. As an alternative, you can generate and upload admin certificates via the Network Monitor. For more information, see the description on the ["Certificates" tab of "Member" screen](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) in the Network Monitor.
- In the Network Monitor of a Starter Plan network, when you click **View Logs** on the nodes that are listed on the "Overview" screen, the {{site.data.keyword.cloud}} Logging Kibana interface opens. **By default, Kibana is preconfigured to show logs from the last 30 days of activity**. If there is no activity in the last 30 days, you will see a message that says *No results found*. To view other logs, you can click the timer icon in the upper-right corner under your user name and set a broader time range, such as *Year to date*.
- The logs of your Starter Plan network are gathered by the [{{site.data.keyword.cloud_notm}} Log Analysis service](https://cloud.ibm.com/catalog/services/log-analysis){: external}. By default, your logs are collected by the Lite Plan of the Log Analysis service. This plan is free and **allows you to search only the first 500 MB of your logs per day**. If your network's logs exceed 500 MB, you cannot view new logs in Kibana. If your network generates more than 500 MB of logs, you can upgrade to a paid version of the Log Analysis Service.
- Because Starter Plan is not a production environment, **applications might not be able to immediately reach a network resource**.
  - If this happens, it is recommended as a first step to increase the default timeout values in the Fabric SDK. For more information about setting timeout values, see [Setting timeout values in Fabric SDKs](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk).
  - You can also retry your request at the application level.
- **Chaincode containers can sometimes be stopped** by a background network issue and might need to be rebuilt and restarted after a user invokes the chaincode. If this happens it may take your chaincode a few minutes to respond.
- Because of the resource limitation in Starter Plan network, that is, 1 CPU and 4 Gi RAM for each peer, **you might encounter a `REQUEST_TIMEOUT` error during chaincode instantiation**. If this happens, retry the instantiation step. If the error continues, you can increase the chaincode instantiation timeout. In the connection profile, the chaincode instantiation timeout is set to 300 seconds.
  - If you use the default timeout value in SDK, copy the **client** section in connection profile as shown below, which sets the timeout to 300 seconds, and ensure that your SDK reads it. Note that for Node SDK, this timeout setting in connection profile affects all calls, such as `invoke` and `queries`.
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
    - If you use Node SDK, you can change the timeout setting of the `sendInstantiateProposal(request, timeout)` method. For more information, see [sendInstantiateProposal(request, timeout)](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal){: external}.
    - If you use Java SDK, you can change the timeout setting of the `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)` command, which is in the `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java` file.

For support and help with your {{site.data.keyword.blockchainfull_notm}} Platform network on {{site.data.keyword.cloud_notm}}, see [Getting support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

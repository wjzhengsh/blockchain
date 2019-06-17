---

copyright:
  years: 2019
lastupdated: "2019-06-18"

keywords: troubleshooting, debug, why, what does this mean, how can I, when I

subcollection: blockchain

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:troubleshoot: data-hd-content-type='troubleshoot'}

# Troubleshooting
{: #ibp-v2-troubleshooting}

General problems may occur when using the console to manage nodes, channels, or smart contracts. In many cases, you can recover from these problems by following a few easy steps.
{:shortdesc}

This topic describes common issues that can occur when using the {{site.data.keyword.blockchainfull_notm}} Platform console.

- [When I hover over my node, the status is `Status unavailable`, what does this mean?](#ibp-v2-troubleshooting-status-unavailable)
- [When I hover over my node, the status is `Status undetectable`, what does this mean?](#ibp-v2-troubleshooting-status-undetectable)
- [Why are my node operations failing after I create my peer or ordering service?](#ibp-console-build-network-troubleshoot-entry1)
- [Why am I getting the error `Unable to get system channel` when I open my ordering service?](#ibp-troubleshoot-ordering-service)
- [Why does my peer fail to start?](#ibp-console-build-network-troubleshoot-entry2)
- [Why did my smart contract installation, instantiation or upgrade fail?](#ibp-console-smart-contracts-troubleshoot-entry1)
- [How can I view my smart contract container logs?](#ibp-console-smart-contracts-troubleshoot-entry2)
- [My channel, smart contracts, and identities have disappeared from the console. How can I get them back?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-browser-storage)
- [Why am I getting the error `Unable to authenticate with the enroll ID and secret you provided` when I create a new organization MSP definition?](#ibp-v2-troubleshooting-create-msp)
- [Why am I getting the error `An error occurred when updating channel` when I try to add an organization to my channel?](#ibp-v2-troubleshooting-update-channel)
- [My {{site.data.keyword.cloud_notm}} Kubernetes cluster expired. What does this mean?](#ibp-v2-troubleshooting-cluster-expired)
- [Why are the transactions I submit from VS Code failing?](#ibp-v2-troubleshooting-anchor-peer)
- [When I log in to my console, why am I getting a 401 unauthorized error?](#ibp-v2-troubleshooting-console-401)
- [Why are the nodes I deployed on {{site.data.keyword.cloud_notm}} Private not processing transactions and are failing health checks?](#ibp-v2-troubleshooting-healthchecks)

## When I hover over my node, the status is `Status unavailable`, what does this mean?
{: #ibp-v2-troubleshooting-status-unavailable}
{: troubleshoot}

The node status in the tile for the CA,  peer, or ordering node is grey, meaning the status of the node is not available. Ideally, when you hover over any node, the node status should be `Running`.
{: tsSymptoms}

This problem can occur if the node is newly created and the deployment process has not completed. If the node is a CA, then it is likely the node is not running.
If the node is a peer or ordering node, this condition occurs when the health checker that runs against the peer or ordering nodes cannot contact the node.  The request for status can fail with a timeout error because the node did not respond within a specific time period, the node could be down, or network connectivity is down.
{: tsCauses}

If this is a new node, wait a few more minutes for the deployment to complete. If the node is not new,
[examine the associated node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors to determine the cause.
{: tsResolve}

## When I hover over my node, the status is `Status undetectable`, what does this mean?
{: #ibp-v2-troubleshooting-status-undetectable}
{: troubleshoot}

The node status in the tile for the peer or ordering node is yellow, meaning the status of the node cannot be detected. Ideally, when you hover over any node, the node status should be `Running`.
{: tsSymptoms}

This condition only occurs on peer and ordering nodes that were *imported* to the console and the health checker cannot run against the node. This status happens because an `operations_url` was not specified when the node was imported. An operations url is required for the node health checker to run. The node itself is likely `Running`, but because the operations url was not specified, its status cannot be determined.
{: tsCauses}

You can resolve this problem by performing the following steps:
 1. Click the node tile to open it.
 2. Click the **Settings** icon.
 3. Click **Associate identity**, view and make note of the identity that is associated with this node. Click **Cancel** to close this panel.
 4. Click **Export** to generate a `JSON` file for the node.
 5. Edit the generated `JSON` file and enter the `operations_url` for the node. The value of the `operations_url` depends on how it was configured as well as various network settings. This value needs to be provided by the network administrator who deployed the node.
 6. Click **Delete**. This step removes the imported node from the console, but does not delete the actual node.
 7. From the **Nodes** tab, click **Add Peer** or **Add ordering service** followed by **Import an existing peer** or **Import an existing Ordering service**.
 8. Click **Upload JSON** and browse to the JSON file you just edited. Click **Next**.
 9. Associate the same identity you noted in step three.
 10. Click **Add peer** or **Add ordering service**.

The health checker can now run against the node and report the status of the node.
{: tsResolve}

## Why are my node operations failing after I create my peer or ordering service?
{: #ibp-console-build-network-troubleshoot-entry1}
{: troubleshoot}

It is possible you may experience an error when managing an existing node. When that occurs, it is often useful to consult the node logs.  

For example, when you try to operate the node, the action might fail.
{: tsSymptoms}

After creating a new peer or ordering service, depending on your cluster storage configuration, it may take a few minutes for the nodes to be ready for operation.
{: tsCauses}

Check your Kubernetes dashboard and ensure the peer or node status is `Running`. Then try your action again. If you are still experiencing problems after the node is up, [check your node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors.  
{: tsResolve}

## Why am I getting the error `Unable to get system channel` when I open my ordering service?
{: #ibp-troubleshoot-ordering-service}
{: troubleshoot}

After you create an ordering service in your {{site.data.keyword.cloud_notm}} Private console, the status is `Running`. But when you open the ordering service you see the error:

```
Unable to get system channel. If you associated an identity without administrative privilege on the ordering service node,
you will not be able to view or manage ordering service details.
```

{: tsSymptoms}

This condition occurs in the blockchain console running on {{site.data.keyword.cloud_notm}} Private. On non-Chrome browsers you are required to  accept a certificate in order for the console to properly communicate with the node.
{: tsCauses}

There are multiple ways to resolve this problem:
1. In your Helm release notes, where your console browser URL is provided, there is also a note to go to a URL and accept the certificate. Browse to that URL and accept the certificate. Now open your ordering service. The error should no longer occur.
2. If you can use a Chrome browser, open your blockhain console in Chrome and open your ordering service. The error does not occur. Note that you will need export your identities from your console wallet on your non-Chrome browser and then import them into the wallet on the Chrome browser for everything to continue working.
{: tsResolve}

## Why does my peer fail to start?
{: #ibp-console-build-network-troubleshoot-entry2}
{: troubleshoot}

It is possible you may experience this error under a variety of conditions.

The peer log includes `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`
{: tsSymptoms}

- This error can occur under the following conditions:
  - When you created the peer or ordering service organization MSP definition, you specified an enroll ID and secret that corresponds to an identity of type `peer` and not `client`. It must be of type `client`.
  - When you created the peer or ordering service organization MSP definition, you specified an enroll ID and secret that does not match the enroll ID or secret of the corresponding organization admin identity.
  - When you created the peer or ordering service, you specified the enroll ID and secret of an identity that is not type 'peer'.

- Open your peer or ordering service CA node and view the registered identities listed in the **Registered Users** table.
- Delete the peer or ordering service and recreate it, being careful to specify the correct enroll ID and secret.
- Note that before you create the peer or ordering service, you need to create an organization admin id, of type 'client'. Be sure to specify that same id as the enroll ID when you create the organization MSP definition. See these instructions for [registering peer identities](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) and these instructions for [registering orderer identities](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Why did my smart contract installation, instantiation or upgrade fail?
{: #ibp-console-smart-contracts-troubleshoot-entry1}
{: troubleshoot}

It is possible you may experience an error when installing, instantiating or upgrading a smart contract.  For example, when you try to install a smart contract on a peer, it fails with the error `An error occurred when installing smart contract on peer.`
{: tsSymptoms}

You may receive this error if this version of the smart contract already exists on the peer, or if your peer is out of resources.
{: tsCauses}

- Open your Kubernetes dashboard and ensure the peer status is `Running`.  
- Open the peer node and ensure the smart contract version does not already exist on the peer and try again with the proper version.
- If you are still experiencing problems after the node is up, [check your node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors.  
{: tsResolve}

## How can I view my smart contract container logs?
{: #ibp-console-smart-contracts-troubleshoot-entry2}
{: troubleshoot}

You may need to view your smart contract, or chaincode, container logs to debug a smart contract issue.
{: tsSymptoms}

Follow these instructions to view your smart contract container logs on:
- [{{site.data.keyword.cloud_notm}}](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-container-logs).
- [{{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain?topic=blockchain-console-icp-manage#console-icp-manage-container-logs).
{: tsResolve}

## My channel, smart contracts, and identities have disappeared from the console. How can I get them back?
{: #ibp-v2-troubleshooting-browser-storage}
{: troubleshoot}

Your console wallet identities consist of a signing certificate and private key that allow you to manage your blockchain components but they are only stored in your browser local storage. You are responsible for securing and managing these identities. We recommend that you export them to your file system after you create them. Whenever you create a new node, you associate an identity from your console wallet with the node. This admin identity is what allows you to manage the node. When you switch browsers or change to a browser on a different machine, these identities are no longer in your wallet. Therefore, you are unable to manage the components.
{: tsSymptoms}

One of the new features of {{site.data.keyword.blockchainfull_notm}} Platform is that you are now responsible for securing and managing your certificates. Therefore, they are only persisted in the browser local storage to allow you to manage the component. If you are using a private browser window and then switch to another browser or non-private browser window, the identities that you created will be gone from your console wallet in the new browser session. Therefore, it is required that you export the identities from the console wallet in your private browser session to your file system. You can then import them into your non-private browser session if they are needed. Otherwise, there is no way to recover them.
{: tsCauses}

- Anytime when you create a new organization MSP definition, you generate keys for an identity that is allowed to administer the organization. Therefore, during that process you must click the **Generate** and then **Export** buttons to store the generated identity in your console wallet and then save it to your file system as a JSON file.
- To resolve this problem in your browser, you need to import those identities and associate them with the corresponding node:
  - In the browser where you are experiencing the problem, click the **Wallet** tab followed by **Add identity** to import the JSON file into your wallet.
  - Click **Upload JSON** and browse to the JSON file you exported using the **Add files** button.
  - Click **Submit**.
  - Now open the peer or ordering service node that this identity was originally associated to, and click on the **Settings** icon.
  - Click the **Associate identity** button.
  - Select the identity you just imported to your console wallet from the drop-down list.
  - Click **Associate**.
- Repeat this process for each identity that was in the wallet of the original browser.
{: tsResolve}

## Why am I getting the error `Unable to authenticate with the enroll ID and secret you provided` when I create a new organization MSP definition??
{: #ibp-v2-troubleshooting-create-msp}
{: troubleshoot}

When you attempt to create a new organization MSP definition from the Organizations tab, you experience the error `Unable to authenticate with the enroll ID and secret you provided`.
{: tsSymptoms}

This error occurs when the value that you specified for the enroll secret is not valid for the enroll ID that you selected in the `Generate organization admin certificate` section of the panel.
{: tsCauses}

Verify that you have selected the correct organization admin enroll ID from enroll ID drop down list and enter the correct value for the enroll secret.
{: tsResolve}

## Why am I getting the error `An error occurred when updating channel` when I try to add an organization to my channel?
{: #ibp-v2-troubleshooting-update-channel}
{: troubleshoot}

When you attempt to add another organization to a channel, the update fails with the message `An error occurred when updating channel`.
{: tsSymptoms}

This error occurs when the selected **Channel Updater MSP ID** on the **Update channel** panel is not an admin of the channel.
{: tsCauses}

On the **Update channel** panel, scroll down to the **Channel Updater MSP ID** and select the MSP ID that was specified when the channel was created or specify the MSP ID that is the admin of the channel.
{: tsResolve}

## My {{site.data.keyword.cloud_notm}} Kubernetes cluster expired. What does this mean?
{: #ibp-v2-troubleshooting-cluster-expired}
{: troubleshoot}

I received an e-mail that my {{site.data.keyword.IBM_notm}} Kubernetes service cluster is about to expire or its status is `Expired`. Or, you are not able to access the console after 30 days.
{: tsSymptoms}

Free Kubernetes clusters are only valid for 30 days.
{: tsCauses}

It is not possible to migrate from a free cluster to a paid cluster. After 30 days you cannot access the console and all of your nodes and certificates are deleted. See this topic on [Kubernetes cluster expiration](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration) for information on what is happening and what you can do.
{: tsResolve}

## Why are the transactions I submit from VS Code failing?
{: #ibp-v2-troubleshooting-anchor-peer}
{: troubleshoot}

Transactions submitted from VS Code fail with an error similar to:
```
Error submitting transaction: No endorsement plan available for {"chaincodes":[{"name":"hello-world"}]}
```
{: tsSymptoms}

This error occurs if you are using the Fabric Service Discovery feature but did not configure any anchor peers on your channel.
{: tsCauses}

Follow step three of the [private data topic](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) in the Deploy a smart contract tutorial to configure your anchor peers.

## When I log in to my console, why am I getting a 401 Unauthorized error?
{: #ibp-v2-troubleshooting-console-401}
{: troubleshoot}

When I try to login to my console, I am unable to access the console from my browser. If I check the browser logs, I can find the error 401 unauthorized.
{: tsSymptoms}

Your browser console session times out after **8 hours** of inactivity. If a session becomes inactive, the console will prevent the inactive user from performing any actions.
{: tsCauses}

If your session has become inactive, you can try simply refreshing your browser. If that does not work, close the browser including **all** tabs and windows. Open the URL again. You will be required to login.

As a best practice, you should have already stored your certificates and identities on your file system. If you happen to be using an incognito window, all the certificates are deleted from the browser local storage when you close the browser. After you log in again you will need to re-import your identities and certificates.
{: note}

## Why are the nodes I deployed on {{site.data.keyword.cloud_notm}} Private not processing transactions and are failing health checks?
{: #ibp-v2-troubleshooting-healthchecks}
{: troubleshoot}

My console states that my peers and ordering nodes are still running. However, my transactions are failing. When I run a liveness or readiness check on the node pods, the check states that the pods are unhealthy.
{: tsSymptoms}

If you have deployed, removed, and upgraded nodes on your cluster many times, perhaps as a result of testing, docker may fail as a result of a known issue with {{site.data.keyword.cloud_notm}} Private. For more information, see this issue in the [{{site.data.keyword.cloud_notm}} Private documentation](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.2.0/getting_started/known_issues.html#25626){: external}.
{: tsCauses}

To address this issue, remove the failed pods and deploy your nodes again. You can also restart the Docker service on the cluster.

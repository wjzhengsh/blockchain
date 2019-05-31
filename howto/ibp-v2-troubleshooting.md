---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: troubleshooting, debug, why, what does this mean, how can I, when I 

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Troubleshooting
{: #ibp-v2-troubleshooting}

General problems may occur when using the console to manage nodes, channels, or smart contracts. In many cases, you can recover from these problems by following a few easy steps.

- [When I hover over my node, the status is `Status unavailable`, what does this mean?](#ibp-v2-troubleshooting-status-unavailable)
- [When I hover over my node, the status is `Status undetectable`, what does this mean?](#ibp-v2-troubleshooting-status-undetectable)
- [Why are my node operations failing after I create my peer or orderer?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry1)
- [Why does my peer fail to start?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry2)
- [Why did my smart contract installation, instantiation or upgrade fail?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry1)
- [How can I view my smart contract container logs?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry2)
- [My channel, smart contracts, and identities have disappeared from the console. How can I get them back?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-browser-storage)
- [Why am I getting the error `An error occurred when updating channel` when I try to add an organization to my channel?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-update-channel)
- [My Kubernetes cluster expired. What does this mean?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-cluster-expired)
- [Why are the transactions I submit from VS Code failing?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-anchor-peer)

## When I hover over my node, the status is `Status unavailable`, what does this mean?
{: #ibp-v2-troubleshooting-status-unavailable}

The node status in the tile for the CA,  peer, or orderer node is grey, meaning the status of the node is not available. Ideally, when you hover over any node, the node status should be `Running`.
{: tsSymptoms}

This problem can occur if the node is newly created and the deployment process has not completed. If the node is a CA, then it is likely the node is not running.
If the node is a peer or orderer, this condition occurs when the health checker that runs against the peer or orderer nodes cannot contact the node.  The request for status can fail with a timeout error because the node did not respond within a specific time period, the node could be down, or network connectivity is down.
{: tsCauses}

If this is a new node, wait a few more minutes for the deployment to complete. If the node is not new,
[examine the associated node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors to determine the cause.
{: tsResolve}

## When I hover over my node, the status is `Status undetectable`, what does this mean?
{: #ibp-v2-troubleshooting-status-undetectable}

The node status in the tile for the peer or orderer node is yellow, meaning the status of the node cannot be detected. Ideally, when you hover over any node, the node status should be `Running`.
{: tsSymptoms}

This condition only occurs on peer and orderer nodes that were *imported* to the console and the health checker cannot run against the node. This status happens because an `operations_url` was not specified when the node was imported. An operations url is required for the node health checker to run. The node itself is likely `Running`, but because the operations url was not specified, its status cannot be determined.
{: tsCauses}

You can resolve this problem by performing the following steps:
 1. Click the node tile to open it.
 2. Click the **Settings** icon.
 3. Click **Associate identity**, view and make note of the identity that is associated with this node. Click **Cancel** to close this panel.
 4. Click **Export** to generate a `JSON` file for the node.
 5. Edit the generated `JSON` file and enter the `operations_url` for the node. The value of the `operations_url` depends on how it was configured as well as various network settings. This value needs to be provided by the network administrator who deployed the node.
 6. Click **Delete**. This step removes the imported node from the console, but does not delete the actual node.
 7. From the **Nodes** tab, click **Add Peer** or **Add orderer** followed by **Import an existing peer** or **Import an existing orderer**.
 8. Click **Upload JSON** and browse to the JSON file you just edited. Click **Next**.
 9. Associate the same identity you noted in step three.
 10. Click **Add peer** or **Add orderer**.

The health checker can now run against the node and report the status of the node.
{: tsResolve}

## Why are my node operations failing after I create my peer or orderer?
{: #ibp-console-build-network-troubleshoot-entry1}

It is possible you may experience an error when managing an existing node. When that occurs, it is often useful to consult the node logs.  

For example, when you try to operate the node, the action may fail.
{: tsSymptoms}

After creating a new peer or orderer, depending on your cluster storage configuration, it may take a few minutes for the node to be ready for operation.
{: tsCauses}

Check your Kubernetes dashboard and ensure the peer or node status is `Running`. Then try your action again. If you are still experiencing problems after the node is up, [check your node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors.  
{: tsResolve}

## Why does my peer fail to start?
{: #ibp-console-build-network-troubleshoot-entry2}

It is possible you may experience this error under a variety of conditions.

The peer log includes `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`
{: tsSymptoms}

- This error can occur under the following conditions:
  - When you created the peer or orderer organization MSP definition, you specified an enroll id and secret that corresponds to an identity of type `peer` and not `client`. It must be of type `client`.
  - When you created the peer or orderer organization MSP definition, you specified an enroll id and secret that does not match the enroll id or secret of the corresponding organization admin identity.
  - When you created the peer or orderer, you specified the enroll id and secret of an identity that is not type 'peer'.

- Open your peer or orderer CA node and view the registered identities listed in the **Registered Users** table.
- Delete the peer or orderer and recreate it, being careful to specify the correct enroll id and secret.
- Note that before you create the peer or orderer, you need to create an organization admin id, of type 'client'. Be sure to specify that same id as the enroll id when you create the organization MSP definition. See these instructions for [registering peer identities](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) and these instructions for [registering orderer identities](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Why did my smart contract installation, instantiation or upgrade fail?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

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

You may need to view your smart contract, or chaincode, container logs to debug a smart contract issue.
{: tsSymptoms}

Follow these instructions to [view your container logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs).
{: tsResolve}

## My channel, smart contracts, and identities have disappeared from the console. How can I get them back?
{: #ibp-v2-troubleshooting-browser-storage}

Your console wallet identities consist of a signing certificate and private key that allow you to manage your blockchain components but they are only stored in your browser local storage. You are responsible for securing and managing these identities. We recommend that you export them to your file system after you create them. Whenever you create a new node, you associate an identity from your console wallet with the node. This admin identity is what allows you to manage the node. When you switch browsers or change to a browser on a different machine, these identities are no longer in your wallet. Therefore, you are unable to manage the components.
{: tsSymptoms}

One of the new features of {{site.data.keyword.blockchainfull_notm}} Platform 2.0 is that you are now responsible for securing and managing your certificates. Therefore, they are only persisted in the browser local storage to allow you to manage the component. If you are using a private browser window and then switch to another browser or non-private browser window, the identities that you created will be gone from your console wallet in the new browser session. Therefore, it is required that you export the identities from the console wallet in your private browser session to your file system. You can then import them into your non-private browser session if they are needed. Otherwise, there is no way to recover them.
{: tsCauses}

- Anytime when you create a new organization MSP definition, you generate keys for an identity that is allowed to administer the organization. Therefore, during that process you must click the **Generate** and then **Export** buttons to store the generated identity in your console wallet and then save it to your file system as a JSON file.
- To resolve this problem in your browser, you need to import those identities and associate them with the corresponding node:
  - In the browser where you are experiencing the problem, click the **Wallet** tab followed by **Add identity** to import the JSON file into your wallet.
  - Click **Upload JSON** and browse to the JSON file you exported using the **Add files** button.
  - Click **Submit**.
  - Now open the peer or orderer node that this identity was originally associated to, and click on the **Settings** icon.
  - Click the **Associate identity** button.
  - Select the identity you just imported to your console wallet from the drop-down list.
  - Click **Associate**.
- Repeat this process for each identity that was in the wallet of the original browser.
{: tsResolve}

## Why am I getting the error `An error occurred when updating channel` when I try to add an organization to my channel?
{: #ibp-v2-troubleshooting-update-channel}

When you attempt to add another organization to a channel, the update fails with the message `An error occurred when updating channel`.
{: tsSymptoms}

This error occurs when the selected **Channel Updater MSP ID** on the **Update channel** panel is not an admin of the channel.
{: tsCauses}

On the **Update channel** panel, scroll down to the **Channel Updater MSP ID** and select the MSP ID that was specified when the channel was created or specify the MSP ID that is the admin of the channel.
{: tsResolve}

## My Kubernetes cluster expired. What does this mean?
{: #ibp-v2-troubleshooting-cluster-expired}

I received an e-mail that my {{site.data.keyword.IBM_notm}} Kubernetes service cluster is about to expire or its status is `Expired`. Or,  you are not able to access the console after 30 days.
{: tsSymptoms}

Free Kubernetes clusters are only valid for 30 days.
{: tsCauses}

It is not possible to migrate from a free cluster to a paid cluster. After 30 days you cannot access the console and all of your nodes and certificates are deleted. See this topic on [Kubernetes cluster expiration](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration) for information on what is happening and what you can do.
{: tsResolve}

## Why are the transactions I submit from VS Code failing?
{: #ibp-v2-troubleshooting-anchor-peer}

Transactions submitted from VS Code fail with an error similar to:
```
Error submitting transaction: No endorsement plan available for {"chaincodes":[{"name":"hello-world"}]}
```
{: tsSymptoms}

This error occurs if you are using the Fabric Service Discovery feature but did not configure any anchor peers on your channel.
{: tsCauses}

Follow step three of the [private data topic](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) in the Deploy a smart contract tutorial to configure your anchor peers.

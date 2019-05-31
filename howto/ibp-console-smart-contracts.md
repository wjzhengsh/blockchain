---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: smart contract, private data, private data collection, anchor peer

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Deploy a smart contract on the network tutorial
{: #ibp-console-smart-contracts}

A smart contract is the code, sometimes referred to as chaincode, that allows you to read and update data on the blockchain ledger. A smart contract can turn business logic into an executable program agreed to and verified by all members of a blockchain network. This tutorial is the third part in the [sample network tutorial series](#ibp-console-smart-contracts-structure) and describes how to deploy smart contracts to start transactions in the blockchain network.
{:shortdesc}

If you are using the beta trial version of {{site.data.keyword.blockchainfull_notm}} Platform, it is likely that some panels in your console will not match the current documentation, which is kept up to date with the generally available (GA) service instance. To gain the benefits of all the latest functionality, you are encouraged at this time to provision a new GA service instance by following instructions in [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
{: important}

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network. Additionally, application developers may be interested in the sections that reference how to create a smart contract.

## Sample network tutorial series
{: #ibp-console-smart-contracts-structure}

This three-part tutorial series guides you through the process of creating and interconnecting a relatively simple, multi-node Hyperledger Fabric network by using the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console to deploy a network into your Kubernetes cluster and install and instantiate a smart contract.

* [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) guides you through the process of hosting a network by creating an orderer and peer.
* [Join a network tutorial](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) guides you through the process of joining an existing network by creating a peer and joining it to a channel.
* **Deploy a smart contract on the network** This current tutorial provides information on how to write a smart contract and deploy it on your network.

You can use the steps in these tutorials to build a network with multiple organizations in one cluster for the purposes of development and testing. Use the **Build a network** tutorial if you want to form a blockchain consortium by creating an orderer node and adding organizations. Use the **Join a network** tutorial to connect a peer to the network. Following the tutorials with different consortium members allows you to create a truly **distributed** blockchain network.  

This final tutorial is meant to show how to create and package a smart contract, how to install the smart contract on a peer, and how to instantiate the smart contract on a channel.  

Smart contracts are installed on peers and then instantiated on channels. **All members that want to submit transactions or read data by using a smart contract need to install the contract on their peer.** A smart contract is defined by its name and version. Therefore, both the name and version of the installed contract need to be consistent across all peers on the channel that plan to run the smart contract.

After a smart contract is installed on the peers, a single network member instantiates the contract on the channel. The network member needs to have joined the channel in order to perform this action. Instantiation updates the ledger with the initial data used by the smart contract, and then starts smart contract containers on peers joined to the channel that have the contract installed. The peers can then use the running containers to transact.  
- **Only one network member needs to instantiate a smart contract.**
- **If a peer with a smart contract installed joins a channel where the same smart contract version has already been instantiated, the smart contract container starts automatically.**  

In this tutorial, we will go through the steps to use the {{site.data.keyword.blockchainfull_notm}} Platform console to manage the deployment of smart contracts on your network:

- [Install smart contracts on your peers](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install).
- [Instantiate them on channels](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).
- [Specify endorsement policies](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse).
- [Upgrade the smart contract policies and code](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).


## Before you begin

Before you can install a smart contract, ensure that you have the following things ready.

- You must either [build a network](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) or [join a network](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) by using your {{site.data.keyword.blockchainfull_notm}} Platform console.
- Because smart contracts are installed onto peers, ensure that you [add peers](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1) to your console.  
- Smart contracts are instantiated on a channel. Therefore, you must [create a channel](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) or [join a channel](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) by using your console.

## Step one: Write a smart contract

The {{site.data.keyword.blockchainfull_notm}} console manages the *deployment* of smart contracts rather than development. If you are interested in developing smart contracts, you can get started using tutorials provided by the Hyperledger Fabric community and tooling provided by {{site.data.keyword.IBM_notm}}.

- To learn how smart contracts can be used to conduct transactions among multiple parties, see the [Developing applications topic ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Developing applications topic") in the Hyperledger Fabric documentation.
- For a complete end-to-end tutorial about using an application to interact with smart contracts, see [Hyperledger Fabric Commercial Paper tutorial ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Hyperledger Fabric Commercial Paper tutorial").
- To learn about how to incorporate access control mechanisms into your smart contract, see [Chaincode for Developers ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "Chaincode for Developers").
- When you are ready to start building smart contracts, you can use the [{{site.data.keyword.blockchainfull_notm}} Visual Studio code extension ![External link icon](../images/external_link.svg "External link icon")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") to start building your own smart contract project. You can also use that extension to [connect directly to your network from Visual Studio code](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode).
- When you are ready to install, the smart contract must be packaged into [.cds format ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "packaging smart contracts") so that it can be installed onto the peers. For more information, see [Packaging smart contracts](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). Alternatively, you can use the [peer cli commands ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html#peer-chaincode-package "peer chaincode package") to build the package.
<!-- Update the tutorial link to release1-4 when it is published -->


## Step two: Install a smart contract
{: #ibp-console-smart-contracts-install}

Use your console to perform these steps:

1. Click the **Smart contracts** tab to install one or more smart contracts.
2. Click **Install smart contract** to upload the smart contract package file in [.cds format ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "packaging smart contracts").
You can use the {{site.data.keyword.blockchainfull_notm}} Visual Studio code extension to [create a smart contract package](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). When you install the package from the **Smart contracts** tab, you can select one or more peer nodes to install the smart contracts on.

If only one peer exists in the console, the smart contract will be installed on it. You are not prompted to select a peer to install the smart contract on. You can navigate to the nodes tab and click a peer that is managed by your console to view the list of smart contracts that have been installed on an individual peer.

You can return to the **Smart contracts** tab later to install the smart contract on additional peers, even after the smart contract was instantiated on the channel. If the version of the smart contract that you install is the same as the instantiated version, these additional peers can process transactions just like the existing peers.
{:tip}

**This console cannot be used to install Hyperledger Composer `.bna` files.**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode").  -->

## Step three: Instantiate a smart contract
{: #ibp-console-smart-contracts-instantiate}

Smart contracts are instantiated on a channel. Any console member with peers joined to a channel can instantiate a smart contract and specify the associated [endorsement policy](/docs/services/blockchain/glossary.html#glossary-endorsement-policy).

Use your console to perform these steps:

1. On the smart contracts tab, find the smart contract from the list installed on your peers and click **Instantiate** from the overflow menu on the right side of the row.
2. On the side panel that opens, select a channel to instantiate the smart contract on. You can select the channel, named `channel1`, which you created. Then, click **Next**.
3. Specify the [endorsement policy for the smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse), described in the following section. When multiple organizations are members of the channel, you have the opportunity choose how many organizations are required to endorse the smart contract transactions.
4. You also need to select the organization members to be included in the endorsement policy. If you are following along in the tutorial, that would be `org1msp` and possibly `org2msp` if you completed both the **Build a network** and **Join a network** tutorials.
5. If your smart contract includes Fabric private data collections, you need to upload the associated collection configuration JSON file, otherwise you can skip this step. See this topic for more information on using [private data](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).
6. On the last panel you are prompted to specify the smart contract function that you want to run when the smart contract starts, along with the associated arguments to pass to that function.

You can view all of the smart contracts that have been instantiated on a channel by clicking the channel icon in the left navigation, selecting a channel from the table, and then clicking the **Channel details** tab.

Be aware that if you use a free {{site.data.keyword.cloud_notm}} Kubernetes service cluster, instantiation can take significantly longer than in a paid cluster. Depending on the number of peers you have deployed in your cluster, this can take several minutes.

The combination of **installation and instantiation** is a powerful feature because it allows for a peer to use a single smart contract across many channels. Peers may want to join multiple channels that use the same smart contract, but with different sets of network members able to access the data. A peer can install the smart contract once, and then use the same smart contract container on any channel where it has been instantiated. This lightweight approach saves compute and storage space, and helps you scale your network.

## Step four: Send transactions by using your client applications
{: #ibp-console-smart-contracts-connect-to-SDK}

After a smart contract has been instantiated on a channel, you can use a client application to transact with other members of your network. Applications can invoke the business logic contained in smart contracts to create, transfer, or update assets on the blockchain ledger.

### Connect with SDK
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
The **Smart Contracts** tab contains the information that you need to connect to an instantiated smart contract from a client app. Next to each instantiated smart contract, navigate to the overflow menu. Click the **Connect with your SDK** button. This opens a side panel that provides the information that you need to connect to this smart contract: the contract name, the channel name, and your connection profile. For more information, see [Creating applications](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Specifying an endorsement policy
{: #ibp-console-smart-contracts-endorse}

Every smart contract must have an endorsement policy, which is specified during instantiation. The endorsement policy specifies the set of organizations, the peers, on a channel that can execute the smart contract and independently validate the transaction output. For example, an endorsement policy can specify that a transaction is added to the ledger only if a majority of the members on the channel endorse the transaction. The organization that instantiates the smart contract can select from among the members who have installed the smart contract to become validators, and sets the endorsement policy for all channel members. You can update your endorsement policy by following the steps for [updating your smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).

When you follow the steps to [instantiate a smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate), you can use the side panel to set a contract's endorsement policy after selecting the channel. Use this panel to specify a simple policy by selecting the peers that need to endorse the transaction from the list of peers that have installed the smart contract on the channel. You can use this method to specify an endorsement policy of all channel members, a majority of them, a single member, or a simple +1 preventing members from self signing. The default endorsement policy is set to `1 of x`, meaning only a single member is required to endorse a smart contract transaction.

Click the **Advanced** button if you want to specify a policy in JSON format. You can use this method to specify more complicated endorsement policies, such as requiring that a certain member of the channel has to validate a transaction, along with a majority of other members. You can find additional [examples of advanced endorsement policies ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Example endorsement policies") in the Hyperledger Fabric documentation. For more information about writing endorsement policies in JSON, see [Hyperledger Fabric Node SDK documentation ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "Hyperledger Fabric Node SDK documentation").

## Upgrading a smart contract
{: #ibp-console-smart-contracts-upgrade}

You can upgrade a smart contract to change its code, endorsement policy, or private data collection while maintaining its relationship to the assets on the ledger. There are a variety of reasons why you may want to upgrade an instantiated smart contract.
1. You can upgrade the smart contract to add or remove functionality from its code and iterate on the logic of your business network.
2. Whenever a new member is added to a channel, the endorsement policy of the instantiated smart contracts *must* be updated to include the new channel member. In order to work with the new channel member, the smart contract must be repackaged with a new version number and instantiated on the channel, even if the smart contract itself is unchanged. Otherwise, transaction endorsement cannot succeed.
3. When a private data collection has changed, for example an organization is added or removed you need to upgrade your smart contract. Or, use this action whenever a new private data collection is added to the collection configuration JSON file.
4. The smart contract initialization arguments have changed.

**Before you upgrade an instantiated smart contract, the new version of the smart contract must be installed on all peers in the channel that are running the previous level of the smart contract.**

### How to upgrade a smart contract
{: #ibp-console-smart-contracts-upgrade-howto}

To upgrade a smart contract, install the updated code by specifying the same smart contract name but by using and a new version number. If you have installed a newer version of a smart contract on any peer in the channel, notice that the original version now has the `Upgrade Available` button next to it in the **Instantiated smart contracts** table.

{:important}
When a new member that will run the smart contract joins the channel, it is mandatory to update the smart contract by installing a new version on all the peers and instantiating it on the channel with a modified endorsement policy that includes the new member.

- Scroll down to the **Instantiated smart contracts** table.
- In the **Instantiated smart contracts** table, locate the smart contract version and channel that you want to upgrade. It must have the **Upgrade Available** label next to it.
- Click the **overflow menu** on the right side of the smart contract row and click **Upgrade** to perform the following steps:  

 1. Select the smart contract version that you want to upgrade on the channel from the drop-down list.
 2. Update the endorsement policy by adding or removing channel members. You can also click **Advanced** to paste in a new JSON formatted string, which modifies the existing policy.
 3. If you want to associate a private data collection configuration file with the smart contract you can upload the JSON file. Or if you want to update an existing collection configuration, you can upload the JSON file.   
 If the smart contract was previously instantiated with a collection configuration file, you **must** again upload the previous version or a new version of the collection configuration file during this step.  
 {:important}
 4. (Optional) Modify the smart contract initialization argument values if the parameters have changed. If you are unsure about it, check with your smart contract developer. If they have not changed, you can leave this field blank.

After you upgrade the smart contract, you will change the version of the contract that is instantiated on the channel, and change the smart contract container for all the peers that have installed the new version. If you are using private data collections, be sure you have configured anchor peers on the channel.

### Considerations when you upgrade smart contracts
{: #ibp-console-smart-contracts-upgrade-considerations}

1. Do I need to install the new version of the smart contract on all my peers?  

  When a smart contract is invoked on a peer, it attempts to run the version that is instantiated on the channel. If a previous version is running on the peer, it results in an error. Therefore, before you upgrade a smart contract on a channel, *it is a best practice to install the latest version of the smart contract on all peers that are running the previous version.*  

2. Can a subsequent smart contract version still process data on the ledger from a prior version of the smart contract?  

  Yes. As long as the new smart contract code addresses the data in a compatible way (by adding new JSON fields and not changing the semantics or type of the existing fields), any subsequent version of the smart contract can run against the data from a prior version.

3. What happens to my peers if I forget to upgrade them to the latest version before updating the smart contract on the channel?  

  After updating the channel to use the new version of the smart contract, if there are still peers on the channel running the previous version, those peers are no longer able to endorse transactions for the smart contract. Also, you risk not having enough endorsements for transactions to be committed to the ledger, depending on how the smart contract endorsement policy is defined. However, it is possible to install the new version of the smart contract on these peers later and they will again be able to endorse transactions, effectively catching up.

4. What happens when I remove an organization from my private data collection?

   The peers in that organization will continue to store data in the private data collection until its ledger reaches the block that removes its membership from the collection. After that occurs, the peers will not receive private data in any future transactions, and _clients_ of that organization will no longer be able to query the private data via chaincode from any peer.

## Private data
{: #ibp-console-smart-contracts-private-data}

Private data is a feature of Hyperledger Fabric networks at version 1.2 or higher and is used to keep sensitive information private from other organization members **on a channel**. Data privacy is achieved through the use of [private data collections  ![External link icon")](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "What is a private data collection?"). For example, several wholesalers and a set of farmers might be joined to a single channel. If a farmer and a wholesaler want to transact privately, they can create a channel for this purpose. But they can also decide to create a private data collection on the smart contract that governs their business interactions to maintain privacy over sensitive aspects of the sale, such as the price, without having to create a secondary channel. To learn more about when to use private data within a blockchain, visit the [Private Data ![External link icon")](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "Private data") concept article in the Fabric documentation.

In order to use private data with {{site.data.keyword.blockchainfull_notm}} Platform, the following three conditions must be satisfied:  
1. **Define the private data collection.** A private data collection file can be added to your smart contract. Then, at runtime, your client application can use private data specific chaincode APIs to input and retrieve data from the collection. For more information about how to use private data collections with your smart contract, see the Fabric SDK tutorial on [Using private data ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-private-data.html "How to use Private Data") in the Fabric SDK documentation.  

2. **Install and instantiate the smart contract.** Once the smart contract private data collection has been defined, you need to install the smart contract on the peers that are members of the channel. When you instantiate the smart contract on the channel by using the console, you need to upload the collection configuration JSON file. For more information on how to [create a collection definition JSON file ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-private-data.html "How to use private data") see the Fabric SDK documentation topic.

  Instead of using the console to install and instantiate your smart contract with a collection config file, you can also use the Fabric SDK. Those instructions are also available under [How to use private data ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "how to use private data") in the Node SDK documentation.  

  **Note:** A client needs to be an admin of your peer in order to install or instantiate a smart contract using the SDK. Therefore, you need to download the certificates of the peer admin identity from your console wallet and pass the peer admin's signing certificate and private key to directly to the SDK instead of creating an application identity. For an example of how to pass a key pair to the SDK, see [Connecting to your network using low level Fabric SDK APIs](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level).  


3. **Configure anchor peers.** Because cross organizational [gossip ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip data dissemination protocol") must be enabled for private data to work, an anchor peer must exist for each organization in the collection definition. Refer to this information for [how to configure anchor peers](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-channels-anchor-peers) on your network.

Your channel is now configured to use private data.

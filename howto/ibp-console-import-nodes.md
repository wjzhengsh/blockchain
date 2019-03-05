---

copyright:
  years: 2019
lastupdated: "2019-03-05"

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

# Importing nodes
{: #ibp-console-import-nodes}

The console includes the option to import nodes that are created by using another {{site.data.keyword.blockchainfull}} Platform console.
{:shortdesc}  

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network.

## Why import a node?

Import CA, orderer, and peer nodes from other {{site.data.keyword.blockchainfull_notm}} Platform consoles when you need to operate them alongside nodes that already exist in your console. For example, you can use the import peer option when you want to join peers from organizations outside your console to channels in your console. As {{site.data.keyword.blockchainfull_notm}} Platform 2.0 services are deployed across multiple {{site.data.keyword.cloud_notm}} environments, it is likely that some service instances include only CAs and peers, whereas others host orderering services. Importing the disparate nodes into the console provides a way to view and operate these distributed components from a single user interface so they can transact together on the blockchain.

Once you import nodes in the console, a robust set of operational capability becomes available, for example:
- Add new organizations to the consortium
- Create new channels
- Join peers to channels
- Install smart contracts on peers regardless of where they were deployed
- Instantiate smart contracts on channels
- Upgrade smart contracts on channels

When importing a component, you are required to provide its connection information. When you import a component, you do not actually import the physical component itself; instead, it uses the connection information to build a representation of the component in the console so that it can be operated from the console.

 After you import a node into the console, you can also modify its connection information by using the node's **Settings** tab.

{: note}
Before you can import nodes into the console, they have to be exported from the {{site.data.keyword.blockchainfull_notm}} Platform console where they were created.  Simply export the node information from the console to a `JSON` file and import the generated file by using the `Upload JSON` button in your console import node side panel step.


## Start here: Gathering certificates or credentials

Before you can import an existing blockchain component from another {{site.data.keyword.blockchainfull_notm}} Platform 2.0 service instance, you must import the component's admin identity into your console wallet. Importing the identity into your console wallet streamlines node operation. The network operator that created the node needs to provide these credentials to you.

## Importing a CA
{: #ibp-console-import-ca}

A CA node is the blockchain component that issues certificates to all network entities (peers, orderers, clients, and so on) so that these entities can communicate, authenticate, and ultimately transact. Each organization has their own CA that acts as their root of trust. You should add your organizations whether you are joining or building a blockchain consortium. You can learn more about CA's in the [overview of blockchain components](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).  

There are several reasons why you might want to import a CA into your console. After you import the CA, you can use it to add more peers to the peer organization by clicking **Register user**. Or, you can use the CA to create additional admin identities for a peer or orderer.

To import a CA to the {{site.data.keyword.blockchainfull_notm}} Platform console and operate it, you must have already exported the CA from another {{site.data.keyword.blockchainfull_notm}} Platform. Importing a CA allows you to register new users and [enroll identities](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

### Before you begin
{: #ibp-console-import-ca-before-you-begin}

- Ensure that you have already imported the CA's admin identity JSON file into your console wallet.
- Ensure that the CA JSON file that was exported from the console where it was created is available.

### How to import a CA  

Importing a CA is performed from the **Nodes** tab. Click the **Add Certificate Authority** `+` sign in the Certificate Authorities section, followed by **Import an existing CA**. Follow the steps in the side panel that opens to specify the connection information.

- Click the **Upload JSON** button to skip the manual entry of the information and browse to the JSON file that was exported from the console where the CA was created.
- Set the admin identity for the CA by clicking **Existing identity** and selecting the CA admin identity from your console wallet.

Optionally, when you import a CA, you can also specify the credentials for the additional TLS CA if one is included.  

After you have imported the CA into the console, you can use your CA to create new identities and generate the necessary certificates to operate your components and submit transactions to the network. To learn more, see [Managing certificate authorities](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-manage-ca).

### Importing admin identities into the console wallet

Each {{site.data.keyword.blockchainfull_notm}} Platform component is deployed with its own public key, the signing cert, of a component administrator inside. When actions are performed against the component by the administrator, this public key is attached to the transaction and validated against the component configuration. The keys allow the administrator to operate their components, such as by registering new users, creating a new channel or installing smart contracts on peers. If you want to use the console to operate an orderer or peer that was created by using another console, you need to upload the associated admin identity to the console wallet. Then, you can associate the imported node with the identity the console wallet. These identities must be exported from the console where they were created and are required when the node is imported.

To import a new identity, open the **Wallet** tab and click **Add identity**. Click **Upload JSON** to browse to the JSON identity file that was exported by the network operator from where the node was created.

After you complete the **Add identity** panel and click submit, you can view the new admin identity in the wallet overview screen. Now you can refer to these identities when you import CA, peer, or orderer components.

## Importing an orderer
{: #ibp-console-import-orderer}

An orderer node is the blockchain component that collects transactions from network members, orders the transactions, and bundles them into blocks. The ordering service, which is comprised of a collection of orderers, is the common binding of blockchain consortiums, and needs to be deployed if you are founding a consortium that other organizations will join. You can learn more about orderers in the [overview of blockchain components](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

Importing an orderer into the console allows you to create new channels for peers to transact privately.

### Before you begin
{: #ibp-console-import-orderer-before-you-begin}

- Ensure that you have already imported the orderer's admin identity JSON file into your console wallet.
- Ensure that the orderer JSON file that was exported from the console where it was created is available.

### How to import an orderer
Importing an orderer is performed from the **Nodes** tab. Click the **Add Orderer** `+` sign in the Orderer section, followed by **Import an existing orderer**. Follow the steps in the side panel that opens to manually specify the connection information.
- Click the **Upload JSON** button to skip the manual entry of the information and browse to the JSON file that was exported from the console where the orderer was created.
- Set the admin identity for the orderer by clicking **Existing identity** and selecting the orderer admin identity from your console wallet.

After you have imported the orderer into the console, you can add new organization members and select the orderer when creating new channels.

## Importing a peer
{: #ibp-console-import-peer}

A peer node is the blockchain component that maintains a ledger and runs a smart contract to perform query and update operations on the ledger. Organization members own and maintain peers.  Each organization that joins a consortium should deploy at least one peer. You can learn more about peers in the [overview of blockchain components](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer).  

After you import a peer into the console, you can install smart contracts on the peer and join the peer to other channels in your blockchain.

**Note:** If you need to add more peers to the peer organization or create additional admin identities for a peer, you need to import the peer's CA and then use that CA to perform those operations.

### Before you begin
{: #ibp-console-import-peer-before-you-begin}

Before you can import a peer, you need to gather the following information and certificates:

- Ensure that you have already imported the peer's admin identity JSON file into your console wallet.
- Ensure that the peer JSON file that was exported from the console where it was created is available.

### How to import a peer

Importing a peer is performed from the **Nodes** tab. Click the **Add Peer** `+` sign in the Peers section, followed by **Import an existing peer**. Follow the steps in the side panel that opens to specify the connection information.
- Click the **Upload JSON** button to skip the manual entry of the information and browse to the JSON file that was exported from the console where the peer was created.
- Set the admin identity for the peer by clicking **Existing identity** and selecting the peer admin identity from your console wallet.

After you import the peer into the console, you can install smart contracts on the peer and join the peer to channels in your blockchain.


<!-- ## Importing an MSP -->

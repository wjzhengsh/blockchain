---

copyright:
  years: 2019
lastupdated: "2019-05-13"

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

# Join a network tutorial
{: #ibp-console-join-network}

{{site.data.keyword.blockchainfull}} Platform is a blockchain-as-a-service offering that enables you to develop, deploy, and operate blockchain applications and networks. You can learn more about blockchain components and how they work together by visiting the [Blockchain component overview](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). This tutorial is the second part in the [sample network tutorial series](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) and describes how to create nodes in the {{site.data.keyword.blockchainfull_notm}} Platform console and connect them a blockchain consortium hosted in another cluster.
{:shortdesc}

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network.  

If you have not already deployed the {{site.data.keyword.blockchainfull_notm}} Platform, see [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). You can create a new Kubernetes cluster for the blockchain components or use an existing cluster in your {{site.data.keyword.cloud_notm}} account. After you deploy the {{site.data.keyword.blockchainfull_notm}} Platform, you can launch the console to create and manage your blockchain components.

Whether you deploy to a paid or free Kubernetes cluster, use the Kubernetes dashboard to pay close attention to the resources at your disposal when you choose to deploy nodes and create channels. It is your responsibility to manage your Kubernetes cluster and deploy additional resources if necessary. While components will successfully deploy to a free cluster, the more components you add, the slower your components will run. For more information about component sizings and how the console interacts with your Kubernetes cluster, see [Allocating resources]((/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
{: note}

## Sample network tutorial series
{: #ibp-console-join-network-structure}

This three-part tutorial series guides you through the process of creating and interconnecting a relatively simple, multi-node Hyperledger Fabric network by using the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console to deploy a network into your Kubernetes cluster and install and instantiate a smart contract.

* [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) guides you through the process of hosting a network by creating an ordering service and a peer.
* **Join a network tutorial** This current tutorial guides you through the process of joining an existing network by creating a peer and joining it to a channel.
* [Deploy a smart contract on the network](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) provides information on how to write a smart contract and deploy it on your network.

You can use the steps in these tutorials to build a network with multiple organizations in one cluster for the purposes of development and testing. Use the **Build a network** tutorial if you want to form a blockchain consortium by creating an ordering service and adding organizations. Use the **Join a network** tutorial to connect a peer to the network. Following the tutorials with different consortium members allows you to create a truly **distributed** blockchain network.  


This tutorial is meant to show how to join a peer to an **existing** network. It presumes an ordering service, that hosts the all channels as well as the consortium, already exists in your cluster or on another {{site.data.keyword.blockchainfull_notm}} Platform cluster. If you don't have an existing network to join, visit the [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) tutorial to learn how to create one. The **Join a network** tutorial takes you through the steps to create the following `Org2` blockchain components, highlighted in the blue box:
![Join network structure](../images/ib2-join-network.png "Join network structure")  
*Figure 1. Join network structure*  
Perform the steps in the **Join a network** tutorial to create the following components and complete the following actions:

* **One peer organization** `Org2`  
  Create the Org2 Membership Services Provider (MSP) definition which defines the organization `Org2`.
* **One peer** `Peer Org2`   
  The ledger, `Ledger x` in the illustration above, is maintained by distributed peers. The peer is deployed with [Couch DB ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) as the state database.
* **One Certificate Authority (CA)** `Org2 CA`
  A CA is the node that issues certificates to all organization admins as well as to nodes associated with an organization. We will create one CA for the peer organization `Org2`.
* **Joining one channel**
  The tutorial describes how to join the channel that was created by the [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) tutorial.

Throughout this tutorial we supply **recommended values** for some of the fields in the console. This allows the names and identities to be easier to recognize in the tabs and drop-down lists. These values are not mandatory, but you will find them helpful, especially since you will have to remember certain values, especially the IDs and secrets of registered users, you input at a previous step. If you forget these values, you will have to created register additional users. We provide a table of the recommended values after each task and recommend that if you do not use sample values that you record the values you do use somewhere as you proceed through the tutorial.
{:tip}

## Step one: Create additional organization and your entry point to your blockchain
{: #ibp-console-join-network-create-ca-org2}

For each organization that you want to create with the console, you should deploy at least one CA. A CA is the node that issues certificates to all network participants (peers, ordering services, clients, admins, and so on). These certificates, which include a signing certificate and private key, allow network participants to communicate, authenticate, and ultimately transact. These CAs will create all of the identities and certificates that belong to your organization, in addition to defining the organization itself. You can then use those identities to deploy nodes, create admin identities, and submit transactions. For more information about your CA and the identities that you will need to create, see [Managing identities](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

In this tutorial, we will create one organization. Therefore, we will need to create **one CA**.

### Creating your peer organization CA
{: #ibp-console-join-network-create-CA-org2CA}

As part of this tutorial, your CA issues the certificates and private keys for your users and nodes. These identities are not managed by {{site.data.keyword.IBM_notm}} and the keys are not stored in the console. They are only stored in your browser local storage. Therefore, make sure to export your identities and the MSP of your organization. If you attempt to access the console from a different machine or a different browser, you will need to import these identities and organization definitions.
{:important}

Perform the following steps from your console:  

1. Navigate to the **Nodes** tab on the left and click **Add Certificate Authority**. The side panels will allow you to customize the CA that you want to create and the organization that this CA will issue keys for.
2. Click  **{{site.data.keyword.cloud_notm}}** under **Create Certificate Authority** and **Next**.
3. Use the second side panel to give your CA a **display name**. Our recommended value for this CA is `Org2 CA`.
4. On the next panel, give your CA admin credentials by specifying an **CA administrator enroll ID** of `admin` and a secret of `adminpw`. Again, these are **recommended values**.
5. If you are using a paid cluster, you have the opportunity to configure resource allocation for the node. For purposes of this tutorial, accept all the defaults and click **Next**. If you want to learn more about how to allocate resources to your node, see this topic on [Allocating resources](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). If you are using a free cluster, you will be skipped to the **Summary** page.
6. Review the Summary page, then click **Add certificate authority**.

**Task: Creating the peer organization CA**

  | **Field** | **Display name** | **Enroll ID** | **Secret** |
  | ------------------------- |-----------|-----------|-----------|
  | **Create CA** | Org2 CA  | admin | adminpw |

*Figure 2. Creating the peer organization CA*  

After you deploy the CA, you will use it when you create your organization MSP, register users, and to create your entry point to a network, the **peer**.

Advanced users may already have their own CA, and not want to create a new CA in the console. If your existing CA can issue certificates in `X.509` format, you can use your own third-party CA instead of creating a new one here. See this topic on [Using a third-party CA with your peer or ordering service](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-identities) for more information.

### Using your CA to register identities
{: #ibp-console-join-network-use-CA-org2}

Each node or application that you want to create needs certificates and private keys to participate in the blockchain network. You also need to create admin identities for these nodes and applications so that you can manage them from the console. We will go through this process twice, once for each CA that we create. And for each CA, you will create two identities:

* **An organization admin**: This identity allows you to operate nodes using the platform console.
* **A peer identity**: This is the identity of the peer itself. Whenever a peer performs an action (for example, endorsing a transaction) it will sign using its certificate.

Depending on your cluster type, deployment of the CA can take up to ten minutes. When the CA is first deployed (or when the CA is otherwise unavailable), the box in the tile for the CA will be grey box. When the CA has successfully deployed and is running, this box will be green, indicating that it is "Running" and can be used to register identities. Before proceeding with the steps below to register identities, you must wait until the CA status is "Running".
{:important}

Once the CA is running, as indicated by the green box in the tile, generate these certificates by completing the following steps:

1. Click on the `Org2 CA` and ensure the `admin` identity that you created for the CA is visible in the table. Then click the **Register User** button.
2. First we'll register the organization admin, which we can do by giving an **Enroll ID** of `org2admin` and a **secret** of `org2adminpw`. Then set the `Type` for this identity as `client` (admin identities should always be registered as `client`, while node identities should always be registered using the `peer` type). The affiliation field is for advanced users and is not a part of the tutorial, so click the box that says **Use root affiliation**. If you want to learn more about how affiliations are used by the Fabric CA, see this topic on [Registering a new identity ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). For now, select any affiliation from the list (for example, `org1`). Also, ignore the **Maximum enrollments** field. If you want to learn more about enrollments, see [Registering identities](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Click **Next**.
4. For the purpose of this tutorial, leave the **Add Attributes** field blank as well. If you want to learn more about identity attributes, see [Registering identities](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
5. After the organization admin has been registered, repeat this same process for the identity of the peer (also using the `Org2 CA`). For the peer identity, give an enroll ID of `peer2` and a secret of `peer2pw`. This is a node identity, so select `peer` as the **Type**. Click the box that says **Use root affiliation** and ignore **Maximum enrollments**. Then, on the next panel, do not assign any **Attributes**, as before.

Registering these identities with the CA is only the first step in **creating** an identity. You will not be able to use these identities until they have been **enrolled**. For the `org2admin` identity, this will happen during the creation of the MSP, which we will see in the next step. In the case of the peer, it happens during the creation of the peer.
{:note}

**Task: Register users**

  |  **Field** | **Description** | **Enroll ID** | **Secret** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Register users** |  Org2 admin | org2admin | org2adminpw |
  | | Peer identity |  peer2 | peer2pw |

*Figure 3. Using your CA to register users*  

### Creating the peer organization MSP
{: #ibp-console-join-network-create-peers-org2}

Now that we have created the peer's CA and used it to **register** our organization identities, we need to create a formal definition of the peer's organization, which is known as the Membership Services Provider (MSP) definition. Many peers can belong to an organization. **You do not need to create a new organization every time you create a peer.** Because this is the first time that we go through the tutorial, we will create the MSP ID for this organization. During the process of creating the MSP, we are going to generate certificates for the `org2admin` identity and add them to our Wallet.

1. Navigate to the **Organizations** tab in the left navigation and click **Create MSP definition**.
2. Give your MSP a display name such as `Org2 MSP` and an ID such as `org2msp`. If you want to specify your own MSP ID in this field, make sure to follow the specifications about the limitations to this name from the tool tip.
3. Under **Root Certificate Authority details**, specify the peer's CA that we created as your root CA for your organization. If this is your first time through this tutorial, you should only see one: `Org2 CA`. Select it.
4. The **Enroll ID** and **Enroll secret** fields below this will auto populate with the enroll ID and secret for the first user that you created with your CA: `admin` and `adminpw`. However, using this identity would make your organization the same identity as your CA identity, which for security reasons is not recommended. Instead, enter the enroll ID and secret that you created for your organization admin, `org2admin` and `org2adminpw`. Then, give this identity a display name: `Org2 Admin`.
5. Click the **Generate** button to enroll this identity as the admin of your organization and export the identity to the Wallet, where it will be used when creating the peer and creating channels.
6. Click **Export** to export the admin certificates to your file system. As we said above, this identity is not stored in your cluster or managed by {{site.data.keyword.IBM_notm}}. It is only stored in your browser local storage. If you change browsers, you need to import this identity into your Wallet to be able to administer the peer.
7. Click **Create MSP definition**.

**Task: Create the peer organization MSP definition**

  |  | **Display name** | **MSP ID** | **Enroll ID**  | **Secret** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Create Organization** | Org2 MSP | org2msp |||
  | **Root CA** | Org2 CA ||||
  | **Org Admin Cert** | |  | org2admin | org2adminpw |
  | **Identity** | Org2 Admin |||||

  *Figure 4. Create the peer organization MSP definition*  

After you have created the MSP, you should be able to see the peer organization admin in your **Wallet**, which can be accessed by clicking on the **Wallet** in the left navigation.

**Task: Check your Wallet**

  | **Field** |  **Display name** | **Description** |
  | ------------------------- |-----------|----------|
  | **Identity** | Org2 Admin | Org2 identity |

  *Figure 5. Check your Wallet*  

For more information about MSPs, see [managing organizations](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Exporting your organization admin identity is important because you are responsible for managing and securing these certificates. If you export the ordering service and the ordering service MSP definition, they can be imported into another console where another operator can create new channels on the ordering service or join peers to the channel.
{:important}

### Creating a peer
{: #ibp-console-join-network-peer-create}

After you have [created a CA](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA), used it to register identities, and created the [peer organization MSP](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2), you're ready to create a peer.

#### What role do peers play?
{: #ibp-console-join-network-peer-role}

It's important to remember that organizations themselves do not maintain ledgers. Peers do. Organizations also use peers to sign transaction proposals and approve channel configuration updates. Because having at least two peers on a channel makes them highly available, having at least two peers joined to a channel is considered a best practice for production level implementations. In this tutorial, we'll only show the process for creating a single peer.

From a resource allocation perspective, it is possible to join the same peers to multiple channels. The design of the peer ensures that the data from one channel cannot pass to another through the peer. However, because the peer will store a separate ledger for each channel, it is necessary to ensure that the peer has enough processing power and storage to handle the transaction and data load.

#### Deploying your peer
{: #ibp-console-join-network-deploy-peer-role}

Use your console to perform the following steps:

1. On the **Nodes** page, click **Add peer**.
2. Click {{site.data.keyword.cloud_notm}} under **Create a new peer** and **Next**.
3. Give your peer a **Display name** of `Peer Org2`.
4. On the next screen, select `Org2 CA`, as this is the CA you used to register the peer identity. Give the **Enroll ID** and **secret** for the peer identity that you created for your peer, `peer2` and `peer2pw`. Then, select `Org2 MSP` from the drop-down list and click **Next**.
5. The next side panel asks for TLS CA information. When you created the CA, a TLSCA was created alongside it. This CA is used to create certificates for the secure communication layer for nodes. Therefor, give the the **Enroll ID** and **secret** for the peer identity that you created for your peer, `peer2` and `peer2pw`. The **TLS CSR hostname** is an option available to advanced users who want specify a custom domain name that can be used to address the peer endpoint. Custom domain names are not a part of this tutorial, so leave the **TLS CSR hostname** blank for now.
6. The next side panel asks you to **Associate an identity** to make it the admin of your peer. For the purpose of this tutorial, make your organization admin, `Org2 Admin`, the admin of your peer as well. It is possible to register and enroll a different identity with the `Org2 CA` and make that identity the admin of your peer, but this tutorial uses the `Org2 Admin` identity.
7. If you are using a paid cluster, on the next panel, you have the opportunity to configure resource allocation for the node. For purposes of this tutorial, you can accept all the defaults and click **Next**. If you want to learn more about how to allocate resources to your node, see this topic on [Allocating resources](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). If you are using a free cluster, you see the **Summary** page.
8. Review the summary and click **Add peer**.

**Task: Deploying a peer**

  |  | **Display name** | **MSP ID** | **Enroll ID** | **Secret** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Create Peer** | Peer Org2 | org2msp |||
  | **CA** | Org2 CA ||||
  | **Peer Identity** | |  | peer2 | peer2pw |
  | **Administrator certificate** | org2msp ||||
  | **TLS CA** | Org2 CA ||||
  | **TLS CA ID** | || admin | adminpw |
  | **Associate identity** | Org2 Admin |||||

  *Figure 6. Deploying a peer*  

In a production scenario, it is recommended to deploy three peers to each channel. This is to allow one peer to go down (for example, during a maintenance cycle) and still maintain highly available peers. To deploy more than one peer for an organization, use the same CA you used to register your first peer identity. In this tutorial, that would be `Org2 CA`. Then, register a new peer identity using a distinct enroll ID and secret. For example, `org2secondpeer` and `org2secondpeerpw`. Then, when creating the peer, give this enroll ID and secret. As this peer is still associated with Org2, choose `Org2 CA`, `Org2 MSP`, and `Org2 Admin` from the drop down lists. You may choose to give this new peer a different admin, which can be registered and enrolled with `Org2 CA`, but this optional. This tutorial series will only show the process for creating a single peer for each peer organization.
{:tip}

## Step two: Add your organization to list of organizations that can transact
{: #ibp-console-join-network-add-org2}

As we noted earlier, a peer organization must be known to the ordering service before it can create or join a channel (this is also known as joining the "consortium", the list of organizations known to the ordering service). This is because channels are, at a technical level, **messaging paths** between peers through the ordering service. Just as a peer can be joined to multiple channels without information passing from one channel to another, so too can an ordering service have multiple channels running through it without exposing data to organizations on other channels.

Because only ordering service admins can add peer organizations to the consortium, you need to either:

* **Be** the ordering service admin. In which case you can add the peer organization you created to the consortium directly.
* **Send** MSP information to the ordering service admin, who will add your organization to their consortium.

In the next step, we will show how to add a peer organization you've created to an consortium you control. To learn how to export your organization to a console hosting an ordering service, see [Export your organization information](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote) below.

### Add the peer's organization to the ordering service in my console
{: #ibp-console-join-network-add-org2-local}

**Follow these steps only if your console already includes the ordering service and channel you want to join.** Otherwise proceed to [Export your organization information](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

Because only ordering service admins can add peer organizations to the consortium, you will need to be the ordering service admin, meaning you have the ordering service organization admin identity in your Wallet.

1. Navigate to the **Nodes** tab.
2. Scroll down to the ordering service you want to use and click on it to open it.
3. Under **Consortium Members**, click **Add organization**.
4. From the drop-down list, select `Org2 MSP`, as this is the MSP that represents `org2`.
5. Click **Add organization**.

Now add the peer organization to the channel.
1. Navigate to the **Channels** tab, click on `channel1`.
2. Click the  **Settings** icon to update the channel and add the peer organization to the channel.
3. In the **Choose from available ordering services** drop down list, ensure that `Ordering Service` is selected.
4. In the **Channel Updater MSP ID** drop down list, ensure that `org1MSP` is selected.
5. In the **Associate available identity** drop down list, ensure that `Org1Admin` is selected.
6. Scroll down to the section titled ` Add organizations to the channel` and open the `Select a channel member` drop-down list and select the peer organization MSP, `Org2 MSP`.
7. Click **Add** and then assign permissions for that organization. We recommend you make them an `Operator` so they can update the channel.
8. Click **Update channel**.

When this process is complete, it is possible for `org2` to create or join a channel hosted on the `Ordering Service`. You can proceed to [Step three: Join your peer to the channel](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Export your organization information
{: #ibp-console-join-network-add-org2-remote}

**Follow these steps only if the ordering service and channel your peer will join resides in another {{site.data.keyword.blockchainfull_notm}} Platform service instance.** Otherwise, you can skip to [Step three: Join your peer to the channel](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

You need to send your organization MSP definition to the ordering service admin and be added to the consortium by using the steps below.

When you follow these steps you need to be the admin of the **peer organization**, meaning you have the peer organization admin identity in your Wallet:

1. Navigate to the **Organizations** tab. You can see the organizations available for export are listed under **Available organizations**. Click the **download** button inside the organization tile to download the JSON configuration file that represents the MSP of the peer organization.
2. Send this file to the ordering service admin in an out of band operation.

### Import the organization definition
{: #ibp-console-join-network-import-remote-msp}

The ordering service admin needs to import this JSON file to add your organization to their console:

1. Navigate to the **Organizations** tab, click the **Import MSP definition** button, and select the JSON file that represents the peer organization MSP definition.
2. Navigate to the **Nodes** page and click on your ordering service node. On the ordering service panel, under **Consortium Members**, click **Add Organization**. On the side panel that opens, select `Org2 MSP` from the list of MSP definitions from your **Organizations** tab.
3. Navigate to the **Channels** tab, click on `channel1` and then click the  **Settings** icon to update the channel and add the peer organization to the channel.
4. In the **Choose from available ordering services** drop down list, ensure that `Ordering Service` is selected.
5. In the **Channel Updater MSP ID** drop down list, ensure that `org1MSP` is selected.
6. In the **Associate available identity** drop down list, ensure that `Org1Admin` is selected.
7. Scroll down to the section titled ` Add organizations to the channel` and open the `Select a channel member` drop-down list and select the peer organization MSP, `Org2 MSP` for the tutorial.
8. Click **Add** and then assign permissions for that organization. We recommend you make them an `Operator` so they can update the channel.
9. Click **Update channel**.

After the admin of the ordering service has joined your peer organization to the consortium and the channel, you need to import the ordering service into your console. You can then join channels that are hosted by the ordering service. Complete the following steps to **import** the ordering service:

The admin of the **ordering service organization** needs to complete these steps first:

1. Navigate to the ordering service inside the **Nodes** tab and click the **Settings** icon to the right of the ordering service name to open the side panel. Click the **Export** button under **Actions** to download a JSON configuration file.
2. Send this file to the peer organization in an out of band operation. The peer organization administrator can then use the configuration file to add the ordering service to the console.

### Import the ordering service from another cluster
{: #ibp-console-join-network-import-remote-orderer}

Follow these steps if you are the admin of the **peer organization**:

1. Navigate to the **Nodes** page and click **Add ordering service**.
2. Click the {{site.data.keyword.cloud_notm}} under **Import an existing ordering service**.
3. Then, click the **Upload JSON** button and select the JSON that represents the node itself.
4. On the subsequent step, when you are asked to associate an identity, select the peer organization identity. In this tutorial, that would be `Org2 Admin`. Click **Import ordering service**.

## Step three: Join your peer to the channel
{: #ibp-console-join-network-join-peer-org2}

We are almost done. Your peer can now be joined to an existing channel. You need to get the `channel name`, out of band, from an organization that's a member of the channel. In the **Build a network** tutorial, we created a channel named `channel1`. If you are not already there, navigate to the **Channels** tab in the left navigation.

Perform the following steps from your console:

1. Click the **Join channel** button to launch the side panels.
2. Select your ordering service named `Ordering Service` and click **Next**.
3. Enter the name of the channel you want to join,  `channel1` and click **Next**.
4. Select which peers you want to join the channel. For purposes of this tutorial, click `Peer Org2`.
5. Click **Join channel**.

If you plan to leverage the Hyperledger Fabric [Private Data ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html "Private data") or [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service Discovery") features, you must configure anchor peers in your organizations from the **Channels** tab. For more information about how to configure anchor peers for private data by using the **Channels** tab in console, see [Private data](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).

You can also create a new channel if your organization is a member of the consortium. Use the steps to [create a channel](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) as described in the [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Next steps
{: #ibp-console-join-network-next-steps}

After you have joined your peer to a channel, use the following steps to deploy a smart contract and begin submitting transactions to the blockchain:

- [Deploy a smart contract on your network](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) using the console.
- After you have installed and instantiated your smart contract, you can [submit transactions using your client application](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Use [the commercial paper sample](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) to deploy an example smart contract and submit transactions from sample application code.

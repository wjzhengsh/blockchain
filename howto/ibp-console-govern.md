---

copyright:
  years: 2019
lastupdated: "2019-05-31"

keywords: network components, IBM Cloud Kubernetes Service, allocate resources, batch timeout, channel update, reallocate resources

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

# Governing components
{: #ibp-console-govern}

After creating CAs, peers, orderers, organizations, and channels, you can use the console to update these components.
{:shortdesc}

If you are using the beta trial version of {{site.data.keyword.blockchainfull_notm}} Platform, it is likely that some panels in your console will not match the current documentation, which is kept up to date with the generally available (GA) service instance. To gain the benefits of all the latest functionality, you are encouraged at this time to provision a new GA service instance by following instructions in [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network.

## How the {{site.data.keyword.cloud_notm}} Kubernetes Service interacts with the console
{: #ibp-console-govern-iks-console-interaction}

It is the network operator's responsibility to monitor CPU, memory, and storage usage and ensure that adequate resources are available **before** attempting to create or resize a node.
{:important}

Because your instance of the {{site.data.keyword.blockchainfull_notm}} Platform console and your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster do not communicate directly about the resources that are available in your cluster, the process for deploying or resizing components by using the console must follow this pattern:

1. **Size the deployment that you want to make**. The **Resource allocation** panels for the CA, peer, and orderer in the console offer default CPU, memory, and storage allocations for each node. You may need to adjust these values according to your use case. If you are unsure, start with default allocations and adjust them as you understand your needs. Similarly, the **Resource reallocation** panel displays the existing resource allocations.

  For a sense of how much storage and compute you will need in your cluster, refer to the chart after this list, which contains the current defaults for the peer, orderer, and CA.

2. **Check whether you have enough resources in your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster**. To monitor your Kubernetes resources, we recommend using the [{{site.data.keyword.cloud_notm}} SysDig](https://www.ibm.com/cloud/sysdig){: external} tool in combination with your {{site.data.keyword.cloud_notm}} Kubernetes dashboard. If you do not have enough space in your cluster to deploy or resize resources, you need to increase the size of your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster. For more information about how to increase the size of a cluster, see [Scaling clusters](/docs/containers?topic=containers-ca#ca){: external}. If you have enough space in your cluster, you can continue with step 3.
3. **Use the console to deploy or resize your node**. If the worker node that the pod is running on is running out of resources, you can add a new larger worker node to your cluster and then delete the existing working node.

| **Component** (all containers) | CPU  | Memory (GB) | Storage (GB) |
|--------------------------------|---------------|-----------------------|------------------------|
| **Peer**                       | 1.2           | 2.4                   | 200 (includes 100GB for peer and 100GB for CouchDB)|
| **CA**                         | 0.1           | 0.2                   | 20                     |
| **Ordering node**              | 0.45          | 0.9                   | 100                    |

If you plan to deploy a five node Raft ordering service, note that your ordering node deployment will increase by a factor of five. So a total of 2.25 CPU, 4.5 GB of memory, and 500 GB of storage for the five Raft nodes. This makes the five node ordering service larger than a 2 CPU Kubernetes single worker node.
{:tip}

For cases when a user wants to minimize charges without bringing a node down completely or deleting it, it is possible to scale a node down to a minimum of 0.001 CPU (1 milliCPU). Note that the node will not be functional when using this amount of CPU.

While the figures in this topic endeavor to be precise, be aware that there are times when a node may not deploy even when it appears that you have enough space in your cluster. Make sure to reference your Kubernetes dashboard to see when components deploy and for error messages when they don't. In cases where a component doesn't deploy for a lack of resources, even if there seems to be enough space in the cluster, you will likely have to deploy additional cluster resources for the component to deploy.
{:important}

## Allocating resources
{: #ibp-console-govern-allocate-resources}

While users of a free cluster **must use default sizes** for the containers associated with their nodes, users of paid clusters can set these values during the creation of their nodes.

The **Resource allocation** panel in the console provides default values for the various fields that are involved in creating a node. These values are chosen because they represent a good way to get started. However, every use case is different. While this topic will provide guidance for ways to think about these values, it ultimately falls to the user to monitor their nodes and find sizings that work for them. Therefore, barring situations in which users are certain that they will need values different from the defaults, a practical strategy is to use these defaults and adjust these values later. For an overview of performance and scale of Hyperledger Fabric, which the {{site.data.keyword.blockchainfull_notm}} Platform is based on, see [Answering your questions on Hyperledger Fabric performance and scale](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/){: external}.

All of the containers that are associated with a node have **CPU** and **memory**, while certain containers that are associated with the peer, orderer, and CA also have **storage**. For more information about storage, see [Persistent storage considerations](/docs/services/blockchain?topic=blockchain-ibp-v2-deploy-iks#ibp-console-storage).

You are responsible for monitoring your CPU, memory and storage consumption in your cluster. If you do happen to request more resources for a blockchain node than are available, the node will not start, but existing nodes are not affected. Although CPU and memory can be changed by using the console and {{site.data.keyword.cloud_notm}} Kubernetes Service dashboard, after a node has been created, storage can be changed later only by using the {{site.data.keyword.cloud_notm}} CLI.
{:note}

Every node has a gRPC web proxy container that bootstraps the communication layer between the console and a node. This container has fixed resource values and is included on the Resource allocation panel to provide an accurate estimate of how much space is required on your Kubernetes cluster in order for the node to deploy. Because the values for this container cannot be changed, we will not discuss the gRPC web proxy in the following sections.

### Certificate Authorities (CAs)
{: #ibp-console-govern-CA}

Unlike peers and orderers, which are actively involved in the transaction process, CAs are involved only in the registration and enrollment of identities, and in the creation of an MSP. This means that they require less CPU and memory. To stress a CA, a user would need to overwhelm it with requests (likely using APIs and a script), or have issued so many certificates it runs out of storage. Under typical operations, neither of these things should happen, though as always, these values should reflect the needs of a particular use case.

The CA has only one associated container that we can adjust:

* **The CA itself**: Encapsulates the internal CA processes, such as registering and enrolling nodes and users, as well as storing a copy of every certificate it issues.

#### Sizing a CA during creation
{: #ibp-console-govern-CA-sizing-creation}

The CA has only a single container with values that you need to worry about because the values of the gRPC web proxy server cannot be changed.

| Resources | Condition to increase |
|-----------------|-----------------------|
| **CA container CPU and memory** | When you expect that your CA will be bombarded with registrations and enrollments. |
| **CA storage** | When you plan to use this CA to register a large number of users and applications. |

### Peers
{: #ibp-console-govern-peers}

The peer has three associated containers that we can adjust:

- **The peer itself**: Encapsulates the internal peer processes (such as validating transactions) and the blockchain (in other words, the transaction history) for all of the channels it belongs to. Note that the storage of the peer also includes the smart contracts installed on the peer.
- **CouchDB**: Where the state databases of the peer are stored. Recall that each channel has a distinct state database.
- **Smart contract**: Recall that during a transaction, the relevant smart contract is "invoked" (in other words, run). Note that all smart contracts that you install on the peer will run in a separate container inside your smart contract container, which is known as a Docker-in-Docker container.  

The peer also includes a container for the **Log Collector** that pipes the logs from the smart contract container to the peer container. Similar to the gRPC web proxy container, you cannot adjust the compute for this container.

#### Sizing a peer during creation
{: #ibp-console-govern-peers-sizing-creation}

As we noted in our section on [How the {{site.data.keyword.cloud_notm}} Kubernetes Service interacts with the console](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction), it is recommended to use the defaults for these peer containers and adjust them later when it becomes apparent how they are being utilized.

| Resources | Condition to increase |
|-----------------|-----------------------|
| **Peer container CPU and memory** | When you anticipate a high transaction throughput right away. |
| **Peer storage** | When you anticipate installing many smart contracts on this peer and to join it to many channels. Recall that this storage will also be used to store smart contracts from all channels the peer is joined to. Keep in mind that we estimate a "small" transaction to be in the range of 10,000 bytes (10k). As the default storage is 100G, this means as many as 10 million total transactions will fit in peer storage before it will need to be expanded (as a practical matter, the maximum number will be less than this, as transactions can vary in size and the number does not include smart contracts). While 100G might therefore seem like much more storage than is needed, keep in mind that storage is relatively inexpensive, and that the process for increasing it is more difficult (require command line) than increasing CPU or memory. |
| **CouchDB container CPU and memory** | When you anticipate a high volume of queries against a large state database. This effect can be mitigated somewhat through the use of [indexes](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html#couchdb-indexes){: external}. Nevertheless, high volumes might strain CouchDB, which can lead to query and transaction timeouts. |
| **CouchDB (ledger data) storage** | When you expect high throughput on many channels and don't plan to use indexes. However, like the peer storage, the default CouchDB storage is 100G, which is significant. |
| **Smart contract container CPU and memory** | When you expect a high throughput on a channel, especially in cases where multiple smart contracts will be invoked at once.|

### Ordering nodes
{: #ibp-console-govern-ordering-nodes}

Because orderers don't maintain the State DB and they don't host smart contracts, they require fewer containers than peers do. But they do host the blockchain (the transaction history) because the blockchain is where the channel configuration is stored, and the orderer must know the latest channel configuration to perform its role.

Similar to the CA, the orderer has only one associated container that we can adjust:

* **The orderer itself**: Encapsulates the internal orderer processes (such as validating transactions) and the blockchain for all of the channels it hosts.

#### Sizing an orderer during creation
{: #ibp-console-govern-orderer-sizing-creation}

As we noted in our section on [How the {{site.data.keyword.cloud_notm}} Kubernetes Service interacts with the console](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction), it is recommended to use the defaults for these orderer containers and adjust them later as it becomes apparent how they are being utilized.

| Resources | Condition to increase |
|-----------------|-----------------------|
| **Orderer container CPU and memory** | When you anticipate a high transaction throughput right away. |
| **Orderer storage** | When you anticipate that this orderer will be part of an ordering service on many channels. Recall that orderers keep a copy of the blockchain for every channel they're a part of. The orderer default storage is 100G, same as the container for the peer itself. |

Making the CPU and memory of your ordering nodes roughly double the size of your peers, while not mandatory, is considered a best practice. If an ordering service is overstressed, it might hit timeouts and start dropping transactions, requiring transactions to be resubmitted. This causes much greater harm to a network than a single peer struggling to keep up. In a Raft ordering service configuration, an overstressed leader node might stop sending heartbeat messages, triggering a leader election, and a temporary cessation of transaction ordering. Likewise, a follower node might miss messages and attempt to trigger a leader election where none is needed.
{:important}

## Reallocating resources
{: #ibp-console-govern-reallocate-resources}

After resizing a node, you might see a delay before it takes effect because containers are being rebuilt.
{:important}

As we said above, we recommend using the [{{site.data.keyword.cloud_notm}} SysDig](https://www.ibm.com/cloud/sysdig){: external} tool in combination with your {{site.data.keyword.cloud_notm}} Kubernetes dashboard to monitor your Kubernetes resource usage. If you determine that a worker node is running out of resources, you can add a new larger worker node to your cluster and then delete the existing working node.
{:note}

While it easier to have enough resources deployed to {{site.data.keyword.cloud_notm}} Kubernetes Service and be able to expand your pods and worker nodes as you see fit without having to increase your deployment into {{site.data.keyword.cloud_notm}} Kubernetes Service first, the bigger the deployment in {{site.data.keyword.cloud_notm}} Kubernetes Service, the more it will cost. Users need to consider their options carefully and recognize the tradeoffs that they are making regardless of the option that they choose.

You can use one of the following ways to reallocate the resources that you assign to the containers that are associated with your node.

1. **Use the {{site.data.keyword.cloud_notm}} Kubernetes Service autoscaler**. The autoscaler will scale your worker nodes up or down in response to your pod spec settings and resource requests. For more information about the {{site.data.keyword.cloud_notm}} Kubernetes Service autoscaler and how to set it up, see [Scaling clusters](/docs/containers?topic=containers-ca#ca){: external} in the {{site.data.keyword.cloud_notm}} documentation. Note that allowing the autoscaler to adjust your resources will result in charges to your {{site.data.keyword.cloud_notm}} Kubernetes Service account that will vary with your usage.
2. **Scale manually**. This involves monitoring your usage in the console and on the {{site.data.keyword.cloud_notm}} Kubernetes Service cluster. While this will involve more manual steps than using the autoscaler, it has the advantage of allowing the user to always be certain what is being charged to their {{site.data.keyword.cloud_notm}} Kubernetes Service account.

To scale manually, click the node that you want to adjust on the **Nodes** page and then click the **Usage** tab. You can see a button called **Reallocate**, which will launch a **Resource allocation** tab that is very similar to the one that you saw when you create the node. If you want to lower the amount of available resources, simply provide lower values and click **Reallocate resources** on that tab and the resulting **Summary** page.

If you want to increase the CPU and memory for a node, use the **Resource allocation** tab to increase the values. The white box at the bottom of the page will add up the new values. After clicking **Reallocate resources**, the **Summary** page will translate this value into a **VPC** amount, which is used to calculate your bill. You'll then need to navigate to your {{site.data.keyword.cloud_notm}} Kubernetes Service dashboard to make sure your cluster has sufficient resources for this reallocation. If it does, you can click **Reallocate resources**. If sufficient resources are not available, you will need to increase the size of your cluster using the {{site.data.keyword.cloud_notm}} Kubernetes Service dashboard.

The method you will use to increase storage will depend on the storage class you chose for your cluster. Refer back to the [storage options](/docs/containers?topic=containers-kube_concepts#kube_concepts){: external} documentation for information about increasing your storage. If you are about to exhaust the storage on your peer or orderer, you must deploy a new peer or orderer with a larger file system and let it sync via your other components on the same channels.

Unlike CPU and memory, which can be increased using the console (if you have resources available in your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster), you will need to use the {{site.data.keyword.cloud_notm}} CLI to increase the storage of your nodes. For a tutorial on how to do this, see [Changing the size and IOPS of your existing storage device](/docs/containers?topic=containers-file_storage#file_change_storage_configuration){: external}.

## Updating an organization MSP definition
{: #ibp-console-govern-update-msp}

Over time you may find the need to update an organization MSP definition, by adding additional organization admins for example, or changing the display name of the MSP in the console.

- Simply export the existing MSP definition from the **Organizations** tab and edit the generated JSON file to modify the display name, the existing certificates, or add new certificates. It is not recommended that you change the `msp_id` field as this could cause breaking changes to your network.
- Click your MSP definition in the **Organizations** tab to open it and then click **Add file** to upload the new JSON file.
- Click **Update MSP definition**. The changes are effective immediately.

## Updating a channel configuration
{: #ibp-console-govern-update-channel}

While creating a channel and updating a channel have the same goal, giving users the ability to ensure that the configuration of their channel is as well suited as possible to their use case, the two processes are in fact very different **as tasks** in the console. Recall from our documentation on [Creating a channel](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) that this is a process undertaken by a **single organization**. As long as an organization is a member of the consortium of the ordering service that will become the ordering service of a channel, they can create the channel in any way they want. They can give it any name, add any organizations (as long as they are a member of the consortium), assign those organizations permissions, set Access Control Lists, and so on.

The other organizations have the choice of whether to participate in this channel (for example, whether to join peers to it), but it is assumed that the collaborative process of choosing channel parameters will happen out of band, before an organization uses the console to create the channel.

Updating a channel is different. It happens **within the console**, and follows the collaborative governance procedures that are fundamental to the way the {{site.data.keyword.blockchainfull_notm}} Platform functions. This collaborative process involves sending the channel configuration update requests to organizations that have an administrative role in the channel. These organizations are also known as channel **operators**.

To update a channel, click on the channel inside the **Channels** tab. Click on the **Settings** button next to the channel name at the top of the page. A panel will appear that looks very similar to the panel you use to create a channel.

### Channel configuration parameters you can update
{: #ibp-console-govern-update-channel-available-parameters}

It's possible to change some, but not all, of the configuration parameters of a channel after the channel has been created. And there is only one parameter that is possible to update and is not available during channel creation.

You will see that the **Channel name** is greyed out and cannot be edited. This reflects the fact that a channel name cannot be changed after it has been created. Also, observe that the ordering service display name is not present, as the ordering service of a channel also cannot be changed after the channel has been created.

You can, however, change the following channel configuration parameters:

* **Organizations**. This section of the panel is how organizations are added or removed from a channel. Organizations that can be added can be seen in the drop-down list. Note that an organization must be a member of the consortium of the ordering service before it can be added to a channel. For more information about how to add an organization to the consortium, see [Add your organization to list of organizations that can transact](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-add-org).

* **Channel update policy**. The update policy of a channel specifies how many organizations (out of the total number of organizations in the channel), who must approve of an update to the channel configuration. To ensure a good balance between collaborative administration and efficient processing of channel configuration updates, consider setting this policy to a majority of admins. For example, if there are five admins in a channel, choose `3 out of 5`.

* **Block cutting parameters**. (Advanced option) Because a change to the default block cutting parameters must be signed by an admin of the ordering service organization, these fields are not present in the channel creation panel. However, because this channel configuration will be sent to all of the relevant organizations in the channel, it is possible to send a channel configuration update request with changes to the block cutting parameters. These fields determine the conditions under which the ordering service cuts a new block. For information on how these fields affect when blocks are cut, see [Block cutting parameters](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-orderer-tuning-batch-size).

* **Access control lists**. (Advanced option) To specify a finer grained control over resources, you can restrict access to a resource to an organization and a role within that organization. For example, setting access to the resource `ChaincodeExists` to `Application/Admins` would mean that only the admin of an application would be able to access the `ChaincodeExists` resource.

If you restrict access to a resource to a particular organization, be aware that only that organization will be able to access the resource. If you want other organizations to be able to access the resource, you will have to add them one by one using fields below. As a result, consider your access control decisions carefully. Restricting access to certain resources in certain ways can have a highly negative effect on how your channel functions.
{:important}

Because the console gives a single user the ability to own and control several organizations, you must specify the organization you are using when you sign a channel update in the **Channel updater organization** section. If you own more than one organization in this channel, you may choose any of the organizations you own in the channel to sign with. Depending on the **channel update policy** you've selected, you may get a notification asking you to sign the request as one or more of the other organizations you own.

If you are attempting to change any of the **Block cutting parameters** and own the ordering service organization of this channel, you will see a field for the ordering service organization. Select the MSP of the relevant ordering service organization from the drop-down list. If you are not an admin of the ordering service organization, you can still make a request to change one of the block cutting parameters, but the request will be sent, and will need to be signed, by an ordering service admin.

### Signature collection flow
{: #ibp-console-govern-update-channel-signature-collection}

For signatures to be verified, the organizations on a channel should export the MSPs (in JSON format) representing their organizations to the other organizations on the channel, as well as importing the MSPs of other organizations. To export an MSP, click the download button on your MSP (in the **Organizations** screen), then send it to other organizations out of band. When you receive a JSON of an MSP, import it using the **Organizations** screen.
{: important}

After a channel configuration update request has been made, it will be sent to the organizations in the channel that have the right to sign it. For example, if there are five operators (channel admins) in a channel, it will be sent to all five. For the channel configuration update to be approved, the number of channel operators listed in the **channel update policy** must be satisfied. If this policy says `3 out of 5`, then the channel configuration update will be sent to all five operators, and when three of them sign it, the new channel configuration update will take effect.

This process of knowing when you have an update to sign, as well as signing it, is handled through the **Notifications** button (which looks like a bell) in the top right of the console. When you see a blue dot on the **Notifications** button, it means that you either have a pending request to evaluate or are being notified of a channel update event.

When you click on the **Notifications** button, you may have one or more actions you have the ability to take. If a channel configuration update request has been made, you will have the ability to click on `Review and update channel configuration` and see the changes to the channel configuration update that are being proposed or have been made (if the new channel configuration has been approved). If you are an operator on the channel, and not enough signatures have been gathered to approve the channel configuration update request, you will have the ability to sign the update request.

You are not mandated to sign a channel configuration update, however there is no way to sign **against** a channel update. If you do not approve of a channel configuration update, you can simply close the panel and reach out to other channel operators out of band to voice your concerns. However, if enough operators in the channel approve of the update to satisfy the channel update policy, the new configuration will take effect.
{:note}

### Configuring anchor peers
{: #ibp-console-govern-channels-anchor-peers}

Because cross organizational [gossip](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html){: external} must be enabled for service discovery and private data to work, an anchor peer must exist for each organization. This anchor peer is not a special **type** of peer, but is just the peer that the organization makes known to other organizations and bootstraps cross organizational gossip. Therefore, at least one [anchor peer](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers){: external} must be defined for each organization in the collection definition.

To configure a peer to be an anchor peer, click the **Channels** tab and open the channel where the smart contract was instantiated.
 - Click the **Channel details** tab.
 - Scroll down to the Anchor peers table and click **Add anchor peer**.
 - Select at least one peer from each organization in collection definition that you want to serve as the anchor peer for the organization. For redundancy reasons, you can consider selecting more than one peer from each organization in the collection.

## Tuning your orderer
{: #ibp-console-govern-orderer-tuning}

Performance of a blockchain platform can be affected by many variables such as transaction size, block size, network size, as well as limits of the hardware. The orderer node includes a set of tuning parameters that together can be used to control orderer throughput and performance.  You can use these parameters to customize how your orderer processes transactions depending on whether you have many small frequent transactions, or fewer but large transactions that arrive less frequently. Essentially, you have the control to decide when the blocks are cut based on your transaction size, quantity, and arrival rate.

The following parameters are available in the console by clicking the orderer node in the **Nodes** tab and then clicking its **Settings** icon. Click the **Advanced** button to open the **Advanced channel configuration** for the orderer.

### Block cutting parameters
{: #ibp-console-govern-orderer-tuning-batch-size}

The following three parameters work together to control when a block is cut, based on a combination of setting the maximum number of transactions in a block as well as the block size itself.

- **Absolute max bytes**  
  Set this value to the largest block size in bytes that can be cut by the orderer.  No transaction may be larger than the value of `Absolute max bytes`. Usually, this setting can safely be two to ten times larger than your `Preferred max bytes`.    
  **Note**: The maximum size permitted is 99MB.
- **Max message count**   
  Set this value to the maximum number of transactions that can be included in a single block.
- **Preferred max bytes**  
  Set this value to the ideal block size in bytes, but it must be less than `Absolute max bytes`. A minimum transaction size,  one that contains no endorsements, is around 1KB.  If you add 1KB per required endorsement, a typical transaction size is approximately 3-4KB. Therefore, it is recommended to set the value of `Preferred max bytes` to be around `Max message count * expected averaged tx size`. At run time, whenever possible, blocks will not exceed this size. If a transaction arrives that causes the block to exceed this size, the block is cut and a new block is created for that transaction. But if a transaction arrives that exceeds this value without exceeding the `Absolute max bytes`, the transaction will be included. If a block arrives that is larger than `Preferred max bytes`, then it will only contain a single transaction, and that transaction size can be no larger than `Absolute max bytes`.

Together, these parameters can be configured to optimize throughput of your orderer.

### Batch timeout
{: #ibp-console-govern-orderer-tuning-batch-timeout}

Set the **Timeout** value to the amount of time, in seconds, to wait after the first transaction arrives before cutting the block. If you set this value too low, you risk preventing the batches from filling to your preferred size. Setting this value too high can cause the orderer to wait for blocks and overall performance to degrade. In general, we recommend that you set the value of `Batch timeout` to be at least `max message count / maximum transactions per second`.

When you modify these parameters, you do not affect the behavior of existing channels on the orderer; rather, any changes you make to the orderer configuration apply only to new channels you create on this orderer.
{:important}

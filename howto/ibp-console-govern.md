---

copyright:
  years: 2019
lastupdated: "2019-04-23"

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

# Governing components
{: #ibp-console-govern}

After creating CAs, peers, orderers, organizations, and channels, you can use the console to update these components.
{:shortdesc}

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network.  

## How the {{site.data.keyword.cloud_notm}} Kubernetes Service interacts with the console
{: #ibp-console-govern-iks-console-interaction}

It is the network operator's responsibility to monitor CPU, memory, and storage usage and ensure that adequate resources are available **before** attempting to create or resize a node.
{:important}

Because your instance of the {{site.data.keyword.blockchainfull_notm}} Platform console and your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster do not communicate directly about the resources that are available in your cluster, the process for deploying or resizing components by using the console must follow this pattern:

1. **Size the deployment that you want to make**. The **Resource allocation** panels for the CA, peer, and orderer in the console offer default CPU, memory, and storage allocations for each node. You may need to adjust these values according to your use case. If you are unsure, start with default allocations and adjust them as you understand your needs. Similarly, the **Resource reallocation** panel displays the existing resource allocations.

  For a sense of how much storage and compute you will need in your cluster, refer to the chart after this list, which contains the current defaults for the peer, orderer, and CA.

2. **Check whether you have enough resources in your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster**. To monitor your Kubernetes resources, we recommend using the [{{site.data.keyword.cloud_notm}} SysDig ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/cloud/sysdig "IBM Cloud Monitoring with Sysdig") tool in combination with your {{site.data.keyword.cloud_notm}} Kubernetes dashboard. If you do not have enough space in your cluster to deploy or resize resources, you need to increase the size of your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster. For more information about how to increase the size of a cluster, see [Scaling clusters ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-ca#ca "Scaling clusters"). If you have enough space in your cluster, you can continue with step 3.
3. **Use the console to deploy or resize your node**. If the worker node that the pod is running on is running out of resources, you can add a new larger worker node to your cluster and then delete the existing working node.

| **Component** (all containers) | CPU (in millicpus) | CPU (in CPUs) | Memory (in megabytes) | Memory (in gigabytes) | Storage (in gigabytes) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **Peer**                       | 1100               | 1.1           | 2200                  | 2.2                   | 200                    |
| **CA**                         | 300                | .3            | 600                   | .6                    | 10                     |
| **Orderer**                    | 450                | .45           | 900                   | .9                    | 100                    |

For cases when a user wants to minimize charges without bringing a node down completely or deleting it, it is possible to scale a node down to a minimum of 0.001 CPU (1 milliCPU). Note that the node will not be functional when using this amount of CPU.
{:important}

## Allocating resources
{: #ibp-console-govern-allocate-resources}

While users of a free cluster **must use default sizes** for the containers associated with their nodes, users of paid clusters can set these values during the creation of their nodes.

The **Resource allocation** panel in the console provides default values for the various fields that are involved in creating a node. These values are chosen because they represent a good way to get started. However, every use case is different. While this topic will provide guidance for ways to think about these values, it ultimately falls to the user to monitor their nodes and find sizings that work for them. Therefore, barring situations in which users are certain that they will need values different from the defaults, a practical strategy is to use these defaults and adjust these values later. For an overview of performance and scale of Hyperledger Fabric, which the {{site.data.keyword.blockchainfull_notm}} Platform is based on, see [Answering your questions on Hyperledger Fabric performance and scale ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Blog about Hyperledger Fabric performance and scale").

All of the containers that are associated with a node have **CPU** and **memory**, while certain containers that are associated with the peer, orderer, and CA also have **storage**. For more information about the different storage options that are available to you in {{site.data.keyword.cloud_notm}}, see [storage options ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-kube_concepts#kube_concepts "Kubernetes storage options"). Study the options carefully before you decide on which storage class to use.

Although CPU and memory can be changed by using the console and {{site.data.keyword.cloud_notm}} Kubernetes Service dashboard, after a node has been created, storage can be changed later only by using the {{site.data.keyword.cloud_notm}} CLI.
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

#### Sizing a peer during creation
{: #ibp-console-govern-peers-sizing-creation}

As we noted in our section on [how Kubernetes interacts with the console](#ibp-console-govern-iks-console-interaction), it is recommended to use the defaults for these peer containers and adjust them later when it becomes apparent how they are being utilized.

| Resources | Condition to increase |
|-----------------|-----------------------|
| **Peer container CPU and memory** | When you anticipate a high transaction throughput right away. |
| **Peer storage** | When you anticipate installing many smart contracts on this peer and to join it to many channels. Recall that this storage will also be used to store smart contracts from all channels the peer is joined to. Keep in mind that we estimate a "small" transaction to be in the range of 10,000 bytes (10k). As the default storage is 100G, this means as many as 10 million total transactions will fit in peer storage before it will need to be expanded (as a practical matter, the maximum number will be less than this, as transactions can vary in size and the number does not include smart contracts). While 100G might therefore seem like much more storage than is needed, keep in mind that storage is relatively inexpensive, and that the process for increasing it is more difficult (require command line) than increasing CPU or memory. |
| **CouchDB container CPU and memory** | When you anticipate a high volume of queries against a large state database. This effect can be mitigated somewhat through the use of [indexes ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html#couchdb-indexes "Hyperledger Fabric indexes documentation"). Nevertheless, high volumes might strain CouchDB, which can lead to query and transaction timeouts. |
| **CouchDB (ledger data) storage** | When you expect high throughput on many channels and don't plan to use indexes. However, like the peer storage, the default CouchDB storage is 100G, which is significant. |
| **Smart contract container CPU and memory** | When you expect a high throughput on a channel, especially in cases where multiple smart contracts will be invoked at once.|

### Ordering nodes
{: #ibp-console-govern-ordering-nodes}

Because orderers don't maintain the State DB and they don't host smart contracts, they require fewer containers than peers do. But they do host the blockchain (the transaction history) because the blockchain is where the channel configuration is stored, and the orderer must know the latest channel configuration to perform its role.

Similar to the CA, the orderer has only one associated container that we can adjust:

* **The orderer itself**: Encapsulates the internal orderer processes (such as validating transactions) and the blockchain for all of the channels it hosts.

#### Sizing an orderer during creation
{: #ibp-console-govern-peers-sizing-creation}

As we noted in our section on [how Kubernetes interacts with the console](#ibp-console-govern-iks-console-interaction), it is recommended to use the defaults for these orderer containers and adjust them later as it becomes apparent how they are being utilized.

| Resources | Condition to increase |
|-----------------|-----------------------|
| **Orderer container CPU and memory** | When you anticipate a high transaction throughput right away. |
| **Orderer storage** | When you anticipate that this orderer will be part of an ordering service on many channels. Recall that orderers keep a copy of the blockchain for every channel they're a part of. The orderer default storage is 100G, same as the container for the peer itself. |

Making the CPU and memory of your ordering nodes roughly double the size of your peers, while not mandatory, is considered a best practice. If a Solo ordering node is overstressed, it might hit timeouts and start dropping transactions, requiring transactions to be resubmitted. This causes much greater harm to a network than a single peer struggling to keep up. In a Raft ordering service configuration, an overstressed leader node might stop sending heartbeat messages, triggering a leader election, and a temporary cessation of transaction ordering. Likewise, a follower node might miss messages and attempt to trigger a leader election where none is needed.
{:important}

## Reallocating resources
{: #ibp-console-govern-reallocate-resources}

After resizing a node, you might see a delay before the it takes effect as containers are being rebuilt.
{:important}

As we said above, we recommend using the [{{site.data.keyword.cloud_notm}} SysDig ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/cloud/sysdig "{{site.data.keyword.cloud_notm}} Monitoring with Sysdig") tool in combination with your {{site.data.keyword.cloud_notm}} Kubernetes dashboard to monitor your Kubernetes resource usage. If you determine that a worker node is running out of resources, you can add a new larger worker node to your cluster and then delete the existing working node.
{:note}

While it easier to have enough resources deployed to {{site.data.keyword.cloud_notm}} Kubernetes Service and be able to expand your pods and worker nodes as you see fit without having to increase your deployment into {{site.data.keyword.cloud_notm}} Kubernetes Service first, the bigger the deployment in {{site.data.keyword.cloud_notm}} Kubernetes Service, the more it will cost. Users need to consider their options carefully and recognize the tradeoffs that they are making regardless of the option that they choose.

You can use one of the following ways to reallocate the resources that you assign to the containers that are associated with your node.

1. **Use the {{site.data.keyword.cloud_notm}} Kubernetes Service autoscaler**. The autoscaler will scale your worker nodes up or down in response to your pod spec settings and resource requests. For more information about the {{site.data.keyword.cloud_notm}} Kubernetes Service autoscaler and how to set it up, see [Scaling clusters ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-ca#ca "Scaling clusters") in the IBM {{site.data.keyword.cloud_notm}} documentation. Note that allowing the autoscaler to adjust your resources will result in charges to your {{site.data.keyword.cloud_notm}} Kubernetes Service account that will vary with your usage.
2. **Scale manually**. This involves monitoring your usage in the console and on the {{site.data.keyword.cloud_notm}} Kubernetes Service cluster. While this will involve more manual steps than using the autoscaler, it has the advantage of allowing the user to always be certain what is being charged to their {{site.data.keyword.cloud_notm}} Kubernetes Service account.

To scale manually, click the node that you want to adjust on the **Nodes** page and then click the **Usage** tab. You can see a button called **Reallocate**, which will launch a **Resource allocation** tab that is very similar to the one that you saw when you create the node. If you want to lower the amount of available resources, simply provide lower values and click **Reallocate resources** on that tab and the resulting **Summary** page.

If you want to increase the CPU and memory for a node, use the **Resource allocation** tab to increase the values. The white box at the bottom of the page will add up the new values. After clicking **Reallocate resources**, the **Summary** page will translate this value into a **VPC** amount, which is used to calculate your bill. You'll then need to navigate to your {{site.data.keyword.cloud_notm}} Kubernetes Service dashboard to make sure your cluster has sufficient resources for this reallocation. If it does, you can click **Reallocate resources**. If sufficient resources are not available, you will need to increase the size of your cluster using the {{site.data.keyword.cloud_notm}} Kubernetes Service dashboard.

The method you will use to increase storage will depend on the storage class you chose for your cluster. Refer back to the [storage options ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-kube_concepts#kube_concepts "storage options) documentation for information about increasing your storage.

Unlike CPU and memory, which can be increased using the console (if you have resources available in your {{site.data.keyword.cloud_notm}} Kubernetes Service cluster), you will need to use the {{site.data.keyword.cloud_notm}} CLI to increase the storage of your nodes. For a tutorial on how to do this, see [Changing the size and IOPS of your existing storage device ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-file_storage#file_change_storage_configuration "Chaing the size and IOPS of your existing storage device").

## Tuning your orderer
{: #ibp-console-govern-orderer-tuning}

Performance of a blockchain platform can be affected by many variables such as transaction size, block size, network size, as well as limits of the hardware. The orderer node includes a set of tuning parameters that together can be used to control orderer throughput and performance.  You can use these parameters to customize how your orderer processes transactions depending on whether you have many small frequent transactions, or fewer but large transactions that arrive less frequently. Essentially, you have the control to decide when the blocks are cut based on your transaction size, quantity, and arrival rate.

The following parameters are available in the console by clicking the orderer node in the **Nodes** tab and then clicking its **Settings** icon. Click the **Advanced** button to open the **Advanced channel configuration** for the orderer.

### Batch size
{: #ibp-console-govern-orderer-tuning-batch-size}

The following three parameters work together to control when a block is cut, based on a combination of setting the maximum number of transactions in a block as well as the block size itself.

- **Absolute max bytes**  
  Set this value to the largest block size in bytes that can be cut by the orderer.  No transaction may be larger than the value of `Absolute max bytes`. Usually, this setting can safely be two to ten times larger than your `Preferred max bytes`.    
  **Note**: The maximum size permitted is 99MB.
- **Max message count**   
  Set this value to the maximum number of transactions that can be included in a single block.
- **Preferred max bytes**  
  Set this value to the ideal block size in bytes, but it must be less than `Absolute max bytes`. A minimum transaction size,  one that contains no endorsements, is around 1KB.  If you add 1KB per required endorsement, a typical transaction size is approximately 3-4KB. Therefore, it is recommended to set the value of `Preferred max bytes` to be around `Max message count * expected averaged tx size`. At runtime, whenever possible, blocks will not exceed this size. If a transaction arrives that causes the block to exceed this size, the block is cut and a new block is created for that transaction. But if a transaction arrives that exceeds this value without exceeding the `Absolute max bytes`, the transaction will be included. If a block arrives that is larger than `Preferred max bytes`, then it will only contain a single transaction, and that transaction size can be no larger than `Absolute max bytes`.

Together, these parameters can be configured to optimize throughput of your orderer.

### Batch timeout
{: #ibp-console-govern-orderer-tuning-batch-timeout}

Set the **Timeout** value to the amount of time, in seconds, to wait after the first transaction arrives before cutting the block. If you set this value too low, you risk preventing the batches from filling to your preferred size. Setting this value too high can cause the orderer to wait for blocks and overall performance to degrade. In general, we recommend that you set the value of `Batch timeout` to be at least `max message count / maximum transactions per second`.

When you modify these parameters, you do not affect the behavior of existing channels on the orderer; rather, any changes you make to the orderer configuration apply only to new channels you create on this orderer.
{:important}

## Modifying channels
{: #ibp-console-govern-channels}

### Configuring anchor peers
{: #ibp-console-govern-channels-anchor-peers}

Because cross organizational [gossip ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip data dissemination protocol") must be enabled for service discovery and private data to work, an anchor peer must exist for each organization. This anchor peer is not a special **type** of peer, but is just the peer that the organization makes known to other organizations and does bootstraps cross organizational gossip. Therefore, at least one [anchor peer ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Anchor peers") must be defined for each organization in the collection definition.

To configure a peer to be an anchor peer, click the **Channels** tab and open the channel where the smart contract was instantiated.
 - Click the **Channel details** tab.
 - Scroll down to the Anchor peers table and click **Add anchor peer**.
 - Select at least one peer from each organization in collection definition that you want to serve as the anchor peer for the organization. For redundancy reasons, you can consider selecting more than one peer from each organization in the collection.

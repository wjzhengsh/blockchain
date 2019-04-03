---

copyright:
  years: 2019
lastupdated: "2019-04-03"

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

## Orderers
{: #ibp-console-govern-orderers}

### Tuning your orderer
{: #ibp-console-govern-orderer-tuning}

Performance of a blockchain platform can be affected by many variables such as transaction size, block size, network size, as well as limits of the hardware. The orderer node includes a set of tuning parameters that together can be used to control orderer throughput and performance.  You can use these parameters to customize how your orderer processes transactions depending on whether you have many small frequent transactions, or fewer but large transactions that arrive less frequently. Essentially, you have the control to decide when the blocks are cut based on your transaction size, quantity, and arrival rate.  

The following parameters are available in the console by clicking on the orderer node in the **Nodes** tab and then clicking on it's **Settings** icon. Click the **Advanced** button to open the **Advanced channel configuration** for the orderer.

#### Batch size

These three parameters work together to control when a block is cut, based on a combination of setting the maximum number of transactions in a block as well as the block size itself.

  - **Absolute max bytes** - Set this value to the largest block size in bytes that can be cut by the orderer.  No transaction may be larger than the value of `Absolute max bytes`. Usually, this setting can safely be two to ten times larger than your `Preferred max bytes`. **Note: The maximum size permitted is 99MB.**
  - **Max message count** - Set this value to the maximum number of transactions that can be included in a single block.  
  - **Preferred max bytes** - Set this value to the ideal block size in bytes, but it must be less than `Absolute max bytes`. A minimum transaction size,  one that contains no endorsements, is around 1KB.  If you add 1KB per required endorsement, a typical transaction size is approximately 3-4KB. Therefore, it is recommended to set the value of `Preferred max bytes` to be around `Max message count * expected averaged tx size`. At runtime, whenever possible, blocks will not exceed this size. If a transaction arrives that causes the block to exceed this size, the block is cut and a new block is created for that transaction. But if a transaction arrives that exceeds this value without exceeding the `Absolute max bytes`, the transaction will be included. If a block arrives that is larger than `Preferred max bytes`, then it will only contain a single transaction, and that transaction size can be no larger than `Absolute max bytes`.

Together, these parameters can be configured to optimize throughput of your orderer.

#### Batch timeout

  - **Timeout** - Set this value to the amount of time, in seconds, to wait after the first transaction arrives before cutting the block. If you set this value too low, you risk preventing the batches from filling to your preferred size. Setting this value too high can cause the orderer to wait for blocks and overall performance to degrade. In general, we recommend that you set the value of `Batch timeout` to be at least `max message count / maximum transactions per second`.

When you modify these parameters, you do not affect the behavior of existing channels on the orderer; rather, any changes you make to the orderer configuration apply only to new channels you create on this orderer.
{:important}

## Channels
{: #ibp-console-govern-channels}

### Configuring anchor peers
{: #ibp-console-govern-channels-anchor-peers}

Because cross organizational [gossip ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip data dissemination protocol") must be enabled for servide discovery and private data to work, an anchor peer must exist for each organization. This anchor peer is not a special **type** of peer, it is just the peer that the organization makes known to other organizations, and in so doing bootstraps cross organizational gossip. Therefore, at least one [anchor peer ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Anchor peers") must be defined for each organization in the collection definition.

To configure a peer to be an anchor peer, click the **Channels** tab and open the channel where the the smart contract was instantiated.
 - Click the **Channel details** tab.
 - Scroll down to the Anchor peers table and click **Add anchor peer**.
 - Select at least one peer from each organization in collection definition that you want to serve as the anchor peer for the organization. For redundancy reasons, you can consider selecting more than one peer from each organization in the collection.

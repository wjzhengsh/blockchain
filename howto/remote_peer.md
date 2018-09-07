---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# About remote peers
{: #remote-peer-overview}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


You can run {{site.data.keyword.blockchainfull}} Platform remote peers in your own {{site.data.keyword.cloud_notm}} Private (ICP) or Amazon Web Services (AWS) Cloud after you connect them to an existing blockchain network. Running peers outside the {{site.data.keyword.cloud_notm}} provides more flexibility to grow or join a blockchain network while taking advantage of an existing network inside {{site.data.keyword.cloud_notm}}. Remote peers leverage the Certificate Authorities (CAs) and Ordering Service on the platform, but allow you to colocate your peer with other applications outside {{site.data.keyword.cloud_notm}}.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer is currently a Beta edition for ICP and Developer edition for AWS. **This Beta or Developer edition is intended for exploration, development, and testing.** For more information and access, see [license and pricing](#remote-peer-license-pricing).

The following cloud platforms are supported:

|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| IBM Cloud Private (ICP) | 2.1.0.3 |


|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|

In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.

## Considerations
{: #remote-peer-limitations}

A remote peer does not have access to the full functionality or support of peers that are hosted on the {{site.data.keyword.blockchainfull_notm}} Platform. Before you run remote peers, ensure that you understand the following restrictions and limitations:
- Remote peers that run in other cloud environments are not visible in the Network Monitor of the blockchain network on {{site.data.keyword.cloud_notm}}.
- Remote peers cannot be addressed by using the Swagger UI in the Network Monitor UI.
- You are responsible for the management of health monitoring, security, logging, and resource usage of your remote peers.
- You can connect your remote peers only to blockchain networks that are at Hyperledger Fabric v1.1 level.
- The database type of the remote peer must match the database type of your blockchain network, either LevelDB or CouchDB.
- The CouchDB Fauxton interface is not available on the remote peer.
- [Gossip](../glossary.html#gossip) for remote peers is not currently supported.


## Prerequisites
{: #remote-peer-prereq}

To use a remote peer, you must have an organization that is a member of a Starter Plan or Enterprise Plan network on the {{site.data.keyword.blockchainfull_notm}} Platform. The remote peer leverages the API endpoints, Hyperledger Fabric CAs, and Ordering Service of the {{site.data.keyword.blockchainfull_notm}} Platform Plan network to operate. If you are not a member of any blockchain network, you need to create or join a network. For more information, see [Creating a network](../get_start.html#creating-a-network) or [Joining a network](../get_start.html#joining-a-network).


## License and pricing
{: #remote-peer-license-pricing}

To access remote peers on AWS Cloud, see [License and pricing for remote peers on AWS](remote_peer_aws.html#license-pricing-aws "License and pricing for remote peers on AWS"). To access remote peers to run on ICP, see [License and pricing for remote peers on ICP](remote_peer_icp.html#license-pricing-icp "License and pricing for remote peers on ICP"). The licenses for the Beta or Developer edition of remote peers are free for both platforms. Afterward, a generally available (GA) offering will replace the Beta or Developer edition.

Migration from Beta or Developer edition to GA is not supported. You need to download and install new remote peers to use the GA offering, when it is released. You can then join new remotes peers to the same channels of the same network as your Beta or Developer edition remote peers.<!--, and receive all the relevant data from the network-->

**Note:** To operate a remote peer, you must have an organization that belongs to a Starter Plan or Enterprise Plan network on {{site.data.keyword.blockchainfull_notm}} Platform. This implies that you or another member in the network must pay the {{site.data.keyword.blockchainfull_notm}} [membership fee](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) for your organization. For more information about paying fees, see [Paying mode](https://console.bluemix.net/docs/services/blockchain/howto/paying_mode.html).


## Deploying a remote peer
{: #deploy-remote-peer}

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).

### Amazon Web Services
{: #aws}

*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/quickstart/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in Amazon Web Services").

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*


### IBM Cloud Private
{: #icp}

{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premise environment. You can use ICP to run an {{site.data.keyword.blockchainfull_notm}} Platform remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").

For more information about deploying remote peers in ICP, see [Deploying remote peers in {{site.data.keyword.cloud_notm}} Private](remote_peer_icp.html "Deploying remote peers in {{site.data.keyword.cloud_notm}} Private").

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on ICP.

![Remote Peer deployment flow on ICP](../images/remote_peer_ICP_flow.png "Remote Peer deployment flow on ICP")  
*Figure 2. Remote Peer deployment flow on ICP*


## Operating a remote peer
{: #operate-remote-peer}

After you deploy the remote peer, you need to complete several operational steps before your peer can submit transactions to the network. The operational steps include adding your organization to a channel, joining your remote peer to the channel, installing chaincode on your remote peer, instantiating chaincode on the channel, and connecting applications to your remote peer. For more information, see [Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or [Operating remote peers in {{site.data.keyword.cloud_notm}} Private](remote_peer_operate_icp.html#remote-peer-operate).

## Data residency
{: #data-residency}

Data residency requirements are restrictions that govern where data might reside. The most common requirement on data residency is associated with laws within certain countries, which mandate all customer data that is processed and stored in an IT system must remain within a specific country’s borders. Similarly, some companies in highly regulated industries, such as government, healthcare, and financial services, require that all their data must be stored entirely behind their firewall. Additionally, some companies have a corporate policy where certain types of data, Personally Identifiable Information typically, must remain private and be entirely behind their corporate firewall. Therefore, to achieve data residency, all components of the blockchain network must be part of the same [channel](../glossary.html#channel) and reside within the border of a single country.

To address data residency requirements, it is important to understand the Hyperledger Fabric architecture that underlies {{site.data.keyword.blockchainfull_notm}} Platform. The architecture is centered around three key components: Ordering Service, Certificate Authority (CA), and Peer. A peer receives ordered state updates in the form of blocks from the ordering service and maintains the state and the ledger. Therefore, a peer and an ordering service have a direct relationship. The ledger contains the latest values for all keys and the data that the transaction logs include.

Additionally, client applications use the [Fabric SDKs](../v10_application.html#using-the-fabric-sdks) to send transactions to the peers and ordering service. These transactions include [read-write set ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "Read-Write set semantics") data, which contains the key-value pairs on the ledger.

If in-country data residency is a requirement for your business, the ordering service, peer nodes and client application should reside in the same country. When an {{site.data.keyword.blockchainfull_notm}} Platform network is created in {{site.data.keyword.cloud_notm}}, you have the choice of selecting a location for the network. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->For more information about regions and locations, see [{{site.data.keyword.blockchainfull_notm}} Platform regions and locations](../reference/ibp_regions.html). To achieve data residency in one of these countries, your remote peer should reside in the same country as the {{site.data.keyword.blockchainfull_notm}} Platform network location.

If the {{site.data.keyword.blockchainfull_notm}} Platform network contains remote peer and ordering service nodes across country borders, you can use channels to segregate data to a subset of peers on the network. Ordering nodes are always located in the data center selected to host the platform. It is not possible to have orderers across country borders. But peers can be located either in the data center or at a remote location outside the {{site.data.keyword.cloud_notm}}. Therefore, to achieve data residency, a channel can be created in which the orderer and all peers (peers local to the data center and remote peers) reside in the same country. In this way, all data that the orderer and peers share stays within the borders of a single country. Other channels can still exist if needed, where the orderer and remote peers are located across country borders and data residency is not required.

In the future, new technology in Hyperledger Fabric will improve the ability to achieve further data residency by using [Private data collections ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Private data collections") and Zero Knowledge Proof.

- A Private data collection ensures that the private data is shared peer-to-peer (via the gossip protocol) to only the authorized peers to see it, for example, peers that are within the country borders. The data is stored in a private database on the peer.  The ordering service is not involved here and does not see the private data. A hash of that data is written to the ledgers of every peer on the channel. The hash that is used for state validation, serves as evidence of the transaction, and can be used for audit purposes.

- A Zero-Knowledge Proof (ZKP) allows a “prover” to assure a “verifier” that they have knowledge of a secret without revealing the secret itself. It's a way to show that you know something that satisfies a statement without showing what you know.

For more information about these technologies, see the white paper about [Private and confidential transactions with Hyperledger Fabric ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Private and confidential transactions with Hyperledger Fabric").

## Getting support
{: #remote-peer-support}

The Beta or Developer edition of the {{site.data.keyword.blockchainfull_notm}} Remote Peer offering is meant for exploration, development, and testing. **Do not use this edition for production.** {{site.data.keyword.blockchainfull_notm}} Platform does not provide support for this edition. If you encounter any issues that are related to your remote peer, see [blockchain resources and support forums](../v10_dashboard.html#support-forums). You can also view the support resources on the **Get Help** screen of the Network Monitor.

- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").

- For issues that are related to {{site.data.keyword.cloud_notm}} Private, ICP offers [free digital support and Community Edition  support ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "ICP free digital support and Community Edition  support").


<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-13"

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

The following cloud platforms are supported:

|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| IBM Cloud Private (ICP) | 3.1.0 |


|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|

In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at **Hyperledger Fabric version 1.1 or version 1.2.1**.

## Considerations
{: #remote-peer-limitations}

A remote peer does not have access to the full functionality or support of peers that are hosted on the {{site.data.keyword.blockchainfull_notm}} Platform. Before you run remote peers, ensure that you understand the following restrictions and limitations:
- Remote peers that run in other cloud environments are not visible in the Network Monitor of the blockchain network on {{site.data.keyword.cloud_notm}}.
- Remote peers cannot be addressed by using the Swagger UI in the Network Monitor UI.
- You are responsible for the management of health monitoring, security, logging, and resource usage of your remote peers.
- You can connect your remote peers only to blockchain networks that are at Fabric level v1.1 or v1.2.1. You can find your Fabric version by opening the [Network Preferences window](../v10_dashboard.html#network-preferences) in your Network Monitor.
- The database type of the remote peer must match the database type of your blockchain network, either LevelDB or CouchDB.
- The CouchDB Fauxton interface is not available on the remote peer.
- [Gossip](../glossary.html#gossip) for remote peers is not currently supported. This implies that Fabric features that depend on gossip, such as [private data ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "private data") and [service discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "service discovery"), are also not supported.


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

You can use the [Quick Start template ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform remote peers on AWS. For more information about deploying remote peers on AWS, see [IBM Blockchain Platform for AWS deployment guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/ibm-blockchain-platform/welcome.html "IBM Blockchain Platform for AWS Quick Start Reference Deployment").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in Amazon Web Services").

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

<!-- ![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  -->

<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_AWS_flow.png" width="750" alt="Click a box to get more details on the process." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Configure or access AWS" title="Configure or access" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Create key pair" title="Create key pair" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="remote_peer_aws.html#prerequisites-aws" alt="Create or join a network" title="Create or join a network" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Join a channel" title="Join a channel" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="remote_peer_aws.html#aws-register-peer" alt="Register remote peer identity" title="Register remote peer identity" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="remote_peer_aws.html#aws-network-endpoints" alt="Retrieve remote peer configuration information" title="Retrieve remote peer configuration information" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Click link" title="Click link" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Configure remote peer instances" title="Configure remote peer instances" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-test" alt="Verify deployment" title="Verify deployment" shape="rect" coords="443.95, 258.43, 570.53, 333.48" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Use Fabric SDK" title="Use Fabric SDK" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="remote_peer_operate_aws.html#aws-peer-cli-operate" alt="Use Fabric Tools CLI" title="Use Fabric Tools CLI" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*Figure 1. Remote Peer deployment flow on AWS*

### IBM Cloud Private
{: #icp}

{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premise environment. You can use ICP to run an {{site.data.keyword.blockchainfull_notm}} Platform remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of {{site.data.keyword.cloud_notm}} Private version 3.1.0 so that you can manage peers in your own environment. For more information about ICP, see the documentation for [{{site.data.keyword.cloud_notm}} Private version 3.1.0 ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0")

For more information about deploying remote peers in ICP, see [Deploying remote peers in {{site.data.keyword.cloud_notm}} Private](remote_peer_icp.html "Deploying remote peers in {{site.data.keyword.cloud_notm}} Private").

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on ICP.
<!--
![Remote Peer deployment flow on ICP](../images/remote_peer_ICP_flow.png "Remote Peer deployment flow on ICP")  
-->

<img usemap="#home_map2" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_ICP_flow.png" width="750" alt="Click a box to get more details on the process." style="width:750px;" />
<map name="home_map2" id="home_map2">
<area href="remote_peer_icp.html#prerequisites-icp" alt="Configure or access ICP" title="Configure or access" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="remote_peer_icp.html#import-the-helm-chart-to-icp" alt="Download file from GitHub" title="Download file from GitHub" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="remote_peer_icp.html#import-the-helm-chart-to-icp" alt="Import helm chart into ICP" title="Import helm chart into ICP" shape="rect" coords="443.95, 52.53, 570.53, 127.11" />
<area href="remote_peer_icp.html#prerequisites-icp" alt="Create or join a network" title="Create or join a network" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="remote_peer_operate_icp.html#remote-peer-operate-with-sdk" alt="Join a channel" title="Join a channel" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="remote_peer_icp.html#register-peer" alt="Register remote peer identity" title="Register remote peer identity" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="remote_peer_icp.html#network-endpoints" alt="Retrieve remote peer configuration information" title="Retrieve remote peer configuration information" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="remote_peer_icp.html#step-three-configure-and-install-a-remote-peer" alt="Install Helm Chart" title="Install Helm Chart" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="remote_peer_icp.html#import-the-helm-chart-to-icp" alt="Verify deployment" title="Verify deployment" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="remote_peer_operate_icp.html#remote-peer-operate-with-sdk" alt="Use Fabric SDK" title="Use Fabric SDK" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="remote_peer_operate_icp.html#remote-peer-cli-operate" alt="Use Fabric Tools CLI" title="Use Fabric Tools CLI" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*Figure 2. Remote Peer deployment flow on ICP*


## Operating a remote peer
{: #operate-remote-peer}

After you deploy the remote peer, you need to complete several operational steps before your peer can submit transactions to the network. The operational steps include adding your organization to a channel, joining your remote peer to the channel, installing chaincode on your remote peer, instantiating chaincode on the channel, and connecting applications to your remote peer. For more information, see [Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or [Operating remote peers in {{site.data.keyword.cloud_notm}} Private](remote_peer_operate_icp.html#remote-peer-operate).

## Data residency
{: #data-residency}

Data residency requirements are restrictions that govern where data might reside. The most common requirement on data residency is associated with laws within certain countries, which mandate all customer data that is processed and stored in an IT system must remain within a specific country’s borders. Similarly, some companies in highly regulated industries, such as government, healthcare, and financial services, require that all their data must be stored entirely behind their firewall. Additionally, some companies have a corporate policy where certain types of data, Personally Identifiable Information typically, must remain private and be entirely behind their corporate firewall. Therefore, to achieve data residency, all components of the blockchain network must be part of the same [channel](../glossary.html#channel) and reside within the border of a single country.

To address data residency requirements, it is important to understand the Hyperledger Fabric architecture that underlies {{site.data.keyword.blockchainfull_notm}} Platform. The architecture is centered around three key components: Ordering Service, Certificate Authority (CA), and Peer. A peer receives ordered state updates in the form of blocks from the ordering service and maintains the state and the ledger. Therefore, a peer and an ordering service have a direct relationship. The ledger contains the latest values for all keys and the data that the transaction logs include.

Additionally, client applications use the [Fabric SDKs](../v10_application.html#using-the-fabric-sdks) to send transactions to the peers and ordering service. These transactions include [read-write set ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Read-Write set semantics") data, which contains the key-value pairs on the ledger.

If in-country data residency is a requirement for your business, the ordering service, peer nodes and client application should reside in the same country. When an {{site.data.keyword.blockchainfull_notm}} Platform network is created in {{site.data.keyword.cloud_notm}}, you have the choice of selecting a location for the network. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->For more information about regions and locations, see [{{site.data.keyword.blockchainfull_notm}} Platform regions and locations](../reference/ibp_regions.html). To achieve data residency in one of these countries, your remote peer should reside in the same country as the {{site.data.keyword.blockchainfull_notm}} Platform network location.

### A use case for data residency

Consider an {{site.data.keyword.blockchainfull_notm}} Platform network which includes the Orderer and Certificate Authority along with a consortium of four organizations. The organizations have one or more peer nodes. All four organizations are part of a single channel and all components of the network reside in the region (for example Frankfurt) where the {{site.data.keyword.blockchainfull_notm}} Platform network was deployed. And finally, the client applications which interact with the peers also reside within Germany. Data residency is maintained.  

![Data residency when all components are in the same country](../images/remote_peer_data_res_1.png "Data residency when all components are in the same country")  
*Figure 3. Data Residency when all components are in the same country*


Now let’s consider the implications when a **remote peer**  joins one of the organizations.  A remote peer can reside in the same region as the rest of the network or anywhere outside of the IBP network region:

-	If the remote peer resides in the same country as the rest of the network, data residency is maintained. All ledger data stays within Germany as in **Figure 3** above.
-	If the remote peer happens to reside in a different country (such as USA for example), data residency is no longer maintained as data on the remote peer ledger is shared outside the country's border.

To solve this problem **channels** can be used to segregate data to a subset of peers on the network. When the {{site.data.keyword.blockchainfull_notm}} Platform network contains remote peer and ordering service nodes across country borders, channels provide isolation of ledger data from organizations with peers outside the country border.  

**Note:** Ordering nodes are always located in the data center region that you selected to host the network. It is not possible to have multiple orderers across country borders. Peers, however, can be located either in the data center or at a remote location outside {{site.data.keyword.cloud_notm}}.

![Data residency when remote peers are outside the country of the IBM Blockchain Platform region ](../images/remote_peer_data_res_2.png "Data Residency remote peers reside outside the country of the IBM Blockchain Platform region")  
*Figure 4. Data Residency remote peers reside outside the country of the IBM Blockchain Platform region*

In **Figure 4**, Data residency is not required for `OrgC` and `OrgD`. In fact, `OrgD` now includes two remote peers, `OrgD-peer1` and `OrgD-peer2` which reside in the *United States*. Therefore, in order for  `OrgA`, `OrgB` and their respective client applications and peers which reside in Germany to isolate the ledger data on channel `X`, a new channel `Y` is created for `OrgC` and `OrgD`.

For a deeper understanding of the flow of data on the {{site.data.keyword.blockchainfull_notm}} Platform network, refer to the [Fabric documentation on transaction flow![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Transaction Flow").

In the future, new technology in Hyperledger Fabric will improve the ability to achieve further data residency by using [Private data collections ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Private data collections") and Zero Knowledge Proof.

- A Private data collection ensures that the private data is shared peer-to-peer (via the gossip protocol) to only the authorized peers to see it, for example, peers that are within the country borders. The data is stored in a private database on the peer.  The ordering service is not involved here and does not see the private data. A hash of that data is written to the ledgers of every peer on the channel. The hash that is used for state validation, serves as evidence of the transaction, and can be used for audit purposes. Private data is available for networks on the {{site.data.keyword.blockchainfull_notm}} Platform that are running on Fabric version 1.2.1. However, the private data feature is not available for remote peers.

- A Zero-Knowledge Proof (ZKP) allows a “prover” to assure a “verifier” that they have knowledge of a secret without revealing the secret itself. It's a way to show that you know something that satisfies a statement without showing what you know.

You can get more information about these technologies in the white paper about [Private and confidential transactions with Hyperledger Fabric ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Private and confidential transactions with Hyperledger Fabric").

## Getting support
{: #remote-peer-support}

If you encounter issues that are related to your remote peer, you can make use of free blockchain developer resources and support forums and get help from {{site.data.keyword.IBM_notm}} and the Fabric community. For more information, see [blockchain resources and support forums](../ibmblockchain_support.html#resources). You can also view the support resources on the **Get Help** screen of the Network Monitor.

- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").

- For issues that are related to {{site.data.keyword.cloud_notm}} Private, ICP offers [free digital support and Community Edition  support ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "ICP free digital support and Community Edition support").

If your problem cannot be solved by any of the above routes, {{site.data.keyword.cloud_notm}} and  {{site.data.keyword.cloud_notm}} Private clients can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal for paid versions of the Remote Peer. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.

{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the free Community Edition of the Remote Peer. The Community Edition is meant for exploration, development and testing, and should not be used for production.

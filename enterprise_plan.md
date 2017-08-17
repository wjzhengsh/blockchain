---

copyright:
  years: 2017
lastupdated: "2017-08-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan

{{site.data.keyword.blockchainfull}}
{{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan is the first available membership option for organizations who would like to found or join a blockchain business network. This plan provides the key infrastructure along with tools and support to easily deploy a highly secure, production ready blockchain network.

For members who are going to initiate the network, IBM provides a graphical user interface to guide the network initiator through key steps to set up and provision the network. This includes inviting other members and setting the governance rules. Once the network is deployed an interactive graphical user interface is available to monitor health & activity of the network; manage key network activities including new deployments, members addition/removal,  chaincode lifecycle, and channel management; and seek technical support.
Support is available 24x7 with a 99.95% service level agreement. Find more information on how to get [support ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/docs/services/blockchain/ibmblockchain_support.html#ibmblockchain_support).

The {{site.data.keyword.blockchainfull_notm}} Platform is built with key Hyperledger Fabric components including a Certificate Authority (CA) and at least 1 peer (max of 6).  IBM also provides a crash fault tolerant (CFT) Kafka ordering service for the network members. 

The Fabric CA is the certificate authority provided with the Enterprise plan. One CA is provided per member, which grants membership to the network. By using the CA the member can also provide membership (certs) to end users of the network.

It’s important to understand that for a transaction to be appended to the ledger, there are three phases involved:
1)    Transaction Simulation and Endorsement (peer)
2)    Ordering (ordering service)
3)    Validation and Commit (peer)

The Fabric peers owned by the members are the interface or gateway for applications to execute chain code, providing the business logic to execute transactions against the ledger.  All transactions must be endorsed. The other members of the network do this endorsement. After endorsement,  transactions are sent to an IBM provided ordering service (Kafka)

In addition to the core blockchain components, the Enterprise Membership option provides an infrastructure with secure data storage and communications (TLS), and high availability.  While Fabric networks share these infrastructure resources, isolation is provided for the Fabric component nodes in a network, and each node executes in a secure docker container protecting the execution environment.

The sole aspect that must be determined is the size of the peers required for the network. This decision is based on the number of channels required, plus the workload per channel, memory usage, and disk space (storage). Below you will find peer sizes available via the Enterprise level service plan and guidance choosing the proper peer.  **LINK** to doc for information on profiles and numbers.

Sign up today for your {{site.data.keyword.IBM_notm}} [{{site.data.keyword.blockchainfull_notm}} membership ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

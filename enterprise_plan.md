---

copyright:
  years: 2017, 2018
lastupdated: "2018-01-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan

{{site.data.keyword.blockchainfull}} Platform Enterprise Plan is the first available membership option for organizations who would like to create or join a blockchain business network. This plan provides the key infrastructure along with tools and support to easily deploy a highly secure, production ready blockchain network.

For members who are going to initiate the network, IBM provides a graphical user interface to guide the network initiator through key steps to set up and provision the network. This includes inviting other members and setting the governance rules. Once the network is deployed an interactive graphical user interface is available to monitor health & activity of the network; manage key network activities including new deployments, members addition/removal, chaincode lifecycle, and channel management; and seek technical support. Find more information on how to get [support](ibmblockchain_support.html).

The {{site.data.keyword.blockchainfull_notm}} Platform is built with key Hyperledger Fabric components including a Certificate Authority (CA) and at least 1 peer (max of 6).  IBM also provides a crash fault tolerant (CFT) Kafka ordering service for the network members. 

The Fabric CA is the certificate authority provided with the Enterprise plan. Two intermediate CAs are provided per member, which grant membership to the network. By using the CA the member can also provide membership (certs) to end users of the network.

It’s important to understand that for a transaction to be appended to the ledger, there are three phases involved:  
1. Transaction Simulation and Endorsement (peer)
2. Ordering (ordering service)
3. Validation and Commit (peer)

The Fabric peers owned by the members are the interface or gateway for applications to execute chain code, providing the business logic to execute transactions against the ledger.  All transactions must be endorsed. The other members of the network do this endorsement. After endorsement,  transactions are sent to an IBM provided ordering service (Kafka).

Besides the core blockchain components, the Enterprise Membership option provides an infrastructure with secure data storage and communications (TLS), and high availability.  While Fabric networks share these infrastructure resources, isolation is provided for the Fabric component nodes in a network, and each node executes in a secure docker container protecting the execution environment.

The sole aspect that must be determined is the size of the peers required for the network. This decision is based on the number of channels required, plus the workload per channel, memory usage, and disk space (storage). 

The IBM Blockchain platform should be used for more stable, production, or almost production level deployments. For testing purposes, use the IBM Container service, or local downloadable images.

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Pricing  
To use the Enterprise plan, network members must pay $1,000 per month as the membership fee, and additionally $1,000 per month for each of their peers in the network.  The monthly fees are billed daily prorated.  For example, a member (associated membership fee of $1,000) of two peers (per peer fee of $1,000 X 2 peers) needs to pay $3,000 every month.  If the month has 30 days, the member pays $100 ($3,000/30) every day.  Note that if you need high availability (HA), you must double your required peer numbers to provide the HA capabilities.

Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).  

Sign up now for your [{{site.data.keyword.blockchainfull_notm}} membership ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

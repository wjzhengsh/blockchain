---

copyright:
  years: 2017
lastupdated: "2017-07-21"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Fabric
IBM Blockchain network is built on Hyperledger Fabric V1.0 code base.  Hyperledger Fabric is a one of the blockchain projects within Hyperledger in Linux Fundation.  Hyperledger Fabric differs from other blockchain systems to provides a private and permissioned blockchain system.
{:shortdesc}

Hyperledger Fabric V1.0 offers a modular architecture to extend the scability and performance.  This topic introduces some key components in Hyperldger Fabric V1.0.  For a complete introduction on Hyperledger Fabric V1.0, see [Hyperledger Fabric documentation ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.  

## Certificate Authority  
As a platform for **permissioned** blockchain networks, Hyperledger Fabric includes a modular **Certificate Authority (CA)** component for managing the network identities of all member organizations and their users. The requirement for a permissioned identity for every user enables ACL-based control over network activity, and guarantees that every transaction is ultimately traceable to a registered user.  
* The CA (fabric-CA by default) issues a root certificate (**rootCert**) to each **member** (organization or individual) that is authorized to join the network. 
* The CA also issues an enrollment certificate (**eCert**) to each **user** that the member has authorized to join the network. 
* Each enrolled user is also granted a number of transaction certificates (**tCerts**).  Each **tCert** authorizes one network transaction. 

This certificate-based control over network membership and actions enables members to restrict access to private and confidential channels, applications, and data, by specific user identities.

For more information about the Hyperledger Fabric Certificate Authority component, see [Fabric CA User’s Guide ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/){:new_window}.

## Membership Service Provider  
Hyperledger Fabric includes a **Membership Service Provider (MSP)** component to offer an abstraction of all cryptographic mechanisms and protocols behind issuing and validating certificates, and user authentication.  The MSP is installed on each channel peer, to ensure that transaction requests that are issued to the peer originate from an authenticated and authorized user identity.

For more information on the Hyperledger Fabric Membership Services Provider component, see *[Membership Service Providers (MSP) ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/msp.html){:new_window}* in [Hyperledger Fabric documentation ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

## Transaction flow  
To ensure data consistency and integrity, Hyperledger Fabric V1.0 implements multiple checkpoints throughout the transaction flow, including invocation, endorsement, ordering, and commitment to the ledger.

**Figure 1** depicts the transaction flow on a Hyperledger Fabric V1.0 blockchain network:
![Transaction Flow](../images/v10_txflow.png "Transaction flow on a Hyperledger Fabric V1.0 network")
*Figure 1. Transaction flow on a Hyperledger Fabric V1.0 network*

On a Hyperledger Fabric V1.0 network, the flow of data for queries and transactions is intitiated by a client-side application submitting a transaction request to peers on a channel. The initial flow of data across the network is common to both queries and transactions:

1. Using the `channel.SendTransactionProposal` API available in the SDK, a client application signs and submits a transaction proposal to the appropriate endorsing peers on the specified channel.  This initial transaction proposal is a **request** for endorsement.  
2. Each peer on the channel verifies the identity and authority of the submitting client, and (if valid) runs the specified chaincode against the supplied inputs (key/vals).  Based on the transaction results and the Endorsement Policy for the invoked chaincode, each peer returns a signed YES or NO response to the application.  Each signed YES response is an **endorsement** of the transaction.  
	
	At this point in the transaction flow, the process diverges for queries and transactions.  If the proposal called a query function in the chaincode, the application returns the data to the client.  If the proposal called a function in the chaincode to update the ledger, the application continues with the following steps:  
3. The application forwards transaction (read/write set & endorsements) to the network **ordering service**.  
4. The transaction(s) is then proxied to the channel's partition topic in the Kafka cluster for ordering.  All channel peers validate each transaction in the block by applying the chaincode-specific Validation Policy and running a Concurrency Control Version Check.  
	* Any transactions that fail the validation process are marked as invalid in the block, and the block is appended to the channel's hashchain.  
	* All valid transactions update the state database accordingly with the modified key/value pairs.  
	
The **gossip data dissemination protocol** continually broadcasts ledger data across the channel to ensure synchronized ledgers amongst peers.  For more information, see *[Gossip data dissemination protocol ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/gossip.html){:new_window}* in 
[Hyperledger Fabric documentation ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

For a step-by-step introduction on transaction flow, see *[Transaction Flow ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/txflow.html){:new_window}* in [Hyperledger Fabric documentation ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.  

## Ordering Service
Hyperledger Fabric includes a Kafka-based service for ordering and broadcasting network transactions. Kafka also provides crash fault tolerance to your network, that is, if an accepted number of ordering service nodes are unavailable, the service continues to order and distribute blocks of transactions to channel peers.

Client-side applications, through an SDK API, forward endorsed transactions to the ordering service. Ordering service nodes then exploit the Kafka service, and its associated ZooKeeper server, to order the transactions in a block. The ordered block of transactions is eventually forwarded to the channel peers, for validation and commitment to the ledger.

Ordering service nodes also provide the following services:
1. Authentication of clients
2. Read-write operations of **chains** (encrypted key-value pairs that represent transaction results)
3. Filtering and validation for configuration transactions that reconfigure or create a channel.  

For more information about Hyperleder Fabric ordering service, see *[Bringing up a Kafka-based Ordering Service ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/kafka.html){:new_window}* in  [Hyperledger Fabric documentation ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

## HFC SDKs
The Hyperledger Fabric Client (HFC) SDKs enable application developers to build applications that interact with a blockchain network. The HFC SDK helps facilitate applications to manage the lifecycle of channels and chaincodes.

Hyperledger Fabric V1.0 delivers Node.js SDK and Java SDK, and provides the following functions to interact with the blockchain network:
* Register and enroll users
* Create channels
* Join peers to a channel
* Update system channel or application channel configuration
* Install chaincodes on peers
* Instantiate chaincode(s) on a channel
* Upgrade chaincode(s) on a channel
* Call chaincode functions to update the ledger
* Query the ledger for specific transactions, blocks or keys
* Monitor events on a channel (e.g. successful committment of a transaction)

For more information about HFC SDKs, see *[Hyperledger Fabric SDKs ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/fabric-sdks.html){:new_window}* in [Hyperledger Fabric documentation ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

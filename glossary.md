---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Glossary
{: #glossary}

This topic defines {{site.data.keyword.blockchainfull}} Platform-specific terms that appear in this documentation. For a deeper understanding of terms, and for a glossary of terms that relate to Hyperledger Fabric concepts, refer to the [Hyperledger Fabric glossary ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html).
{:shortdesc}

## Asset
{: #glossary-asset}
Tangible or intangible goods, services, or property that are represented as an item that is traded on the blockchain network.

## Block
{: #glossary-block}
An ordered set of transactions, which is cryptographically linked to the preceding block on a channel.

## Business network
{: #glossary-business-network}
A definition of a blockchain network, which includes the data model, transaction logic, and access control rules for your blockchain solution. Business Network Definitions are created by using [Hyperledger Composer](/docs/services/blockchain/glossary.html#glossary-composer). Business Network Definitions are packaged into deployable **.bna** (business network archive) files.

## CA
{: #glossary-CA}
An abbreviation of "Certificate Authority", this is the component that issues certificates to all the participating members. These certificates represent a member’s identity. All entities in the network (peers, orderers, clients, etc.) must have an identity to communicate, authenticate, and ultimately transact. These identities are required for any direct participation in the blockchain network.

## Chain
{: #glossary-chain}
The ledger’s chain is a transaction log structured as hash-linked blocks of transactions. Peers receive blocks of transactions from the ordering service, mark the block’s transactions as valid or invalid based on endorsement policies and concurrency violations, and append the block to the hash chain on the peer’s file system.

## Chaincode
{: #glossary-chaincode}

Also known as **smart contracts**, chaincode are the pieces of software that contain a set of functions to query or update the ledger.

## Channel
{: #glossary-channel}
Consisting of a subset of network members who want to transact privately. Channels provide data isolation and confidentiality by allowing the members of a channel to establish specific rules and a separate ledger that only channel members can access. Peers, which are the nodes that function as transaction endpoints for organizations, are joined to channels.

## Client
{: #glossary-client}
The client represents the entity that acts on behalf of a user. It must connect to a peer for communicating with the blockchain. The client might connect to any peer of its choice. Clients create and thus invoke transactions. The client submits an actual transaction invocation to the endorsers, and broadcasts transaction proposals to the ordering service.

## Connection profile
{: #glossary-connection-profile}
The Connection profile is visible in the "Overview" screen of the Network Monitor when you click the **Connection Profile** button. The information is available in JSON format and contains the API endpoint information and enrollIDs/secrets for your network resources, that is, peers, orderers, and CAs. Your application interacts with network resources through these API endpoints.

## Consensus
{: #glossary-consensus}
A collaborative process to keep the ledger transactions synchronized across the network. Consensus ensures that ledgers are updated only when the appropriate participants approve transactions, and that ledgers are updated with the same transactions in the same order. There are many different algorithmic ways of achieving consensus.

## Consortium
{: #glossary-consortium}
The group of non-orderer organizations listed on the orderer system channel. These are the only organizations that can create channels. At channel creation time, all organizations added to the channel must be part of a consortium. However, an organization that is not defined in a consortium may be added to an existing channel. While a blockchain network can have multiple consortia, most blockchain networks have a single consortium.

## CouchDB
{: #glossary-couchdb}
A document store that is used for the state database in Starter Plan networks. CouchDB is also an option for Enterprise Plan networks, along with LevelDB. CouchDB supports the use of indexes and allows you to issue rich queries against the data on your peer.

## Current state
{: #glossary-current-state}

The current state of the ledger represents the latest values for all keys that are ever included in its chain transaction log. Because current state represents all latest key values known to the channel, it is sometimes referred to as World State. Chaincode executes transaction proposals against current state data. The current state changes every time when the value of a key changes or a new key is added. The current state is critical to a transaction flow because the latest key-value pair must be known before it can be changed. Peers commit the latest values to the current state of the ledger for each valid transaction in a block. The Current state is stored in a peer's state database

## Dynamic membership
{: #glossary-dynamic-memership}
A member can be dynamically added to the network by a user with **registrar** privilege. Members are also assigned roles and attributes, which control their access and authority on the network. Neither roles nor attributes can be assigned dynamically though. Hyperledger Fabric supports the addition or removal of members, peers, and ordering service nodes, without compromising the operations of the overall network. Dynamic membership is critical when business relationships adjust and entities need to be added or removed for various reasons.

## Endorsement
{: #glossary-endorsement}
The process by which chaincode transactions are validated. Endorsement rules are implemented by specifying endorsement policies.

## Endorsement policy
{: #glossary-endorsement-policy}
Defines the peer nodes on a channel that must execute transactions that are attached to a specific chaincode application, and the required combination of responses (endorsements). A policy could require that a transaction be endorsed by a minimum number of endorsing peers, a minimum percentage of endorsing peers, or by all endorsing peers that are assigned to a specific chaincode application. Policies can be curated based on the application and the desired level of resilience against misbehavior, whether deliberate or not, by the endorsing peers. A submitted transaction must satisfy the endorsement policy before it is marked as valid by committing peers. A distinct endorsement policy to install and instantiate transactions is also required.

## Genesis Block
{: #glossary-genesis-block}
The configuration block that initializes a blockchain network or channel, and also serves as the first block on a chain.

## Gossip
{: #glossary-gossip}
Hyperledger Fabric allows peers to gather important network information from each other without having to rely on the ordering service. The [gossip data dissemination protocol ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/gossip.html) provides a secure, reliable, and scalable way for peers to exchange messages between each other. For example, if peers miss some blocks because of delays, network outages, or other reasons, they can sync up to the current ledger state by using gossip messaging to contact other peers in possession of these missing blocks.

## HSM
{: #glossary-hsm}
Hardware Security Module. Provides on-demand encryption, key management, and key storage as a managed service. HSM is a physical appliance that handles the resource-intensive tasks of cryptography processing and reduce latency to applications. For more information, see [Hardware Security Module ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/cloud/hardware-security-module)

## Hyperledger Composer
{: #glossary-composer}
**IBM does not provide support for networks using Hyperledger Composer in production, including the Composer CLI, JavaScript APIs, REST server, and Web Playground.**
[Hyperledger Composer ![External link icon](images/external_link.svg "External link icon")](https://hyperledger.github.io/composer/latest/introduction/introduction.html) is an open source development toolset. It uses a bespoke modeling language, which is combined with JavaScript transactions, and access control rules to model a blockchain business network in full. You can use Hyperledger Composer to integrate existing systems and data with your blockchain application before you deploy anything to a real blockchain.

## Hyperledger Fabric
{: #glossary-hyperledger-fabric}
[Hyperledger Fabric ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/) is a business blockchain framework that the Linux Foundation hosts to serve as a foundation for developing blockchain applications or solutions with a modular architecture. Hyperledger Fabric components such as consensus and membership services are plug-and-play.

## Install
{: #glossary-install}
The process of placing a chaincode on a peer’s file system. You must install the chaincode on every peer that will run this chaincode.

## Instantiate
{: #glossary-instantiate}
The process of starting and initializing a chaincode container on a specific channel. After chaincode is installed on the Peers and every Peer has joined the channel, the chaincode must be instantiated on the channel. Instantiation performs any necessary initialization of the chaincode, which includes setting the key value pairs that comprise a chaincode's initial world state. After instantiation, peers that have the chaincode installed can accept chaincode invocations.

## Kafka
{: #glossary-kafka}
A consensus plugin implementation for Hyperledger Fabric that results in a cluster of ordering service nodes in the blockchain network. A Kafka implementation is intended for a production network.

## Ledger
{: #glossary-ledger}
Comprised of a literal "chain of blocks" that store the immutable, sequenced record of transactions, as well as a state database to maintain current state. There is one ledger per channel, and updates to it are managed by the consensus process according to the policies of a particular channel.

## LevelDB
{: #glossary-leveldb}
A key-value store that is an option for the state database for Enterprise Plan networks, along with CouchDB. LevelDB stores the current state as key-value pairs, and does not support the use of indexes or rich queries.

## Member
{: #glossary-member}

Also known as "organizations", members in a blockchain network, similar to the members of any group, form the structure of the network. A member can be as large as a multi-national corporation or as small as an individual. Members are enrolled into the network with a certificate that grants them permissions to use the network as either a service provider (for example, issuing certificates, validating/ordering transactions) or as a consumer. The former provides foundational blockchain services that include transaction validation, transaction ordering, and certificate management services. Consumer members use the network to invoke transactions against the distributed ledger. Members can have multiple Peers.

## MSP
{: #glossary-msp}
Membership Service Provider.  A set of cryptographic mechanisms and protocols for issuing and validating certificates and identities throughout the blockchain network. Identities that are issued in the scope of a membership service provider can be evaluated within that membership service provider’s rules validation policies. The MSP is installed on each channel peer to ensure that transaction requests that are issued to the peer originate from an authenticated and authorized user identity.

## Network
{: #glossary-network}
An instance of an {{site.data.keyword.blockchainfull_notm}} Platform service on {{site.data.keyword.cloud_notm}}.

## Network Credentials
{: #glossary-network-credentials}
Visible from the "APIs" screen of the Network Monitor. Credentials include your "key" (Username) and "secret" (Password) in the Swagger UI. You need to use these network credentials to authenticate before you try out the REST APIs.

## Network Monitor
{: #glossary-network-monitor}
The GUI dashboard the {{site.data.keyword.blockchainfull_notm}} Platform provides to view and manage the blockchain network.

## Node
{: #glossary-node}
The communication entity of the blockchain. There are three types of nodes: CA, Peer, and orderer.

## Orderer
{: #glossary-orderer}
The node that collects transactions from network members, orders the transactions and bundles them into blocks. These blocks are then distributed to peers, which then verify the blocks and add them to the ledgers on each channel. Orderers contain the cryptographic identity material that is tied to each member and authenticate the identity of clients and peers to access the network. The overall function that an ordering node or collection of nodes provides is known as the **ordering service**.

## Organization
{: #glossary-organization}
See [Member](/docs/services/blockchain/glossary.html#glossary-member).

## Participant
{: #glossary-participant}
Any organization, individual, application, or device that interact with the blockchain network. Under the participant umbrella there are two distinct groupings, which are members and users.

## Peer
{: #glossary-peer}
A blockchain network resource that provides the services to execute and validate transactions, and maintain ledgers. The Peer runs chaincode and is the holder of transaction history and the current state of assets on the network’s channels, that is, the ledger. They are owned and managed by organizations and are joined to channels.

## Service credentials
{: #glossary-service-credentials}
Service credentials are in JSON format and contain the API endpoint information and enrollIDs/secrets for your network resources, that is, CAs, orderers, and peers. Your application interacts with network resources through these API endpoints.

## SDK
{: #glossary-sdk}
The Hyperledger Fabric supports two Software Development Kits (SDKs). A Node SDK and Java SDK.  The Node SDK can be installed via NPM and the Java SDK via Maven.  The SDKs have their own git repositories, that is, [Fabric Node SDK ![External link icon](images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-sdk-node) and  [Fabric Java SDK ![External link icon](images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-sdk-java), with documentation for the available APIs. The Hyperledger Fabric Client SDKs enable interaction between your client application and your blockchain network.

## SignCert
{: #glossary-sign-cert}
The certificate that any entities, whether organizations or admins, attach to their proposals or proposal responses. These signCerts are unique to an entity and are checked by the ordering service to make sure they match the signCert on file for that entity.

## Smart contracts
{: #glossary-smart-contracts}
See [Chaincode](/docs/services/blockchain/glossary.html#glossary-chaincode).

## SOLO
{: #glossary-solo}
A consensus plugin implementation for Hyperledger Fabric that results in a single ordering service node in the blockchain network. The Starter Plan network uses the SOLO implementation. A SOLO implementation is not intended for a production network. The alternative to SOLO is a Kafka cluster.

## State database
{: #glossary-state-database}
Current state data is stored in a database on the peers for efficient reads and queries from chaincode. Starter Plan networks use CouchDB as the state database. Enterprise Plan Networks can use either LevelDB or CouchDB.

## Transaction
{: #glossary-transaction}
The mechanism that participants on the blockchain network use to interact with assets. A transaction either creates new chaincode or invokes an operation in an existing chaincode.

## User
{: #glossary-user}
A user is a participant in a blockchain network that has indirect access to the ledger through a trust relationship to an existing member.

## World state
{: #glossary-world-state}
See [Current state]((/docs/services/blockchain/glossary.html#glossary-current-state).

---

copyright:
  years: 2017
lastupdated: "2017-07-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Ordering Service

Hyperledger Fabric includes a Kafka-based service for ordering and broadcasting network transactions. Kafka also provides crash fault tolerance to your network, that is, if a limited number of ordering service nodes are unavailable, the service continues to order and distribute blocks of transactions to channel peers.
{:shortdesc}

Client-side applications, through an SDK API, forward endorsed transactions to the ordering service. Ordering service nodes then exploit the Kafka service, and its associated ZooKeeper server, to order the transactions in a block. The ordered block of transactions is eventually forwarded to the channel peers, for validation and commitment to the ledger.

Ordering service nodes also provide the following services:

1. Authentication of clients
2. Read-write operations of **chains** (encrypted key-value pairs that represent transaction results)
3. Filtering and validation for configuration transactions that reconfigure or create a channel.  

For more information about Hyperleder Fabric ordering service, see the *Bringing up a Kafka-based Ordering Service* topic in the [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

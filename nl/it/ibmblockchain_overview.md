---

copyright:
  years: 2016, 2017
lastupdated: "2017-07-28"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Blockchain basics
{: #ibmblockchain_overview}

Blockchain is a distributed ledger technology (DLT) that streamlines business processes by establishing new levels of trust, accountability, and transparency for a new generation of transactional applications. The blockchain network was first introduced to the market for bitcoin exchanges, but its practical uses extend far beyond cryptocurrency transactions. In conjunction with the Linux Foundation's Hyperledger Project, {{site.data.keyword.blockchainfull}} is reimagining the most fundamental business exchanges, opening the door to a new world of digital interactions.

{{site.data.keyword.blockchain}} reduces the cost and complexity of cross-enterprise transactions through the creation of efficient, highly secure networks where virtually anything of value can be tracked and traded without reliance on a centralized point of control. In the world of finance, blockchain networks can allow securities trades to be settled in minutes rather than days. In the world of trade, these networks could facilitate supply chain management and allow the flow of goods and payments to be tracked and logged in real time. 

## Blockchain network overview

In a {{site.data.keyword.blockchain}} network, the records of network transactions are held on a shared ledger that is replicated across all or a subset of network members (ledgers exist in the scope of a channel, so if a member's peer is not subscribed to a channel, then they will not have that channel's transactions). Records of all transactions, both valid and invalid, are recorded in blocks and appended to the hash chain (i.e. blockchain) for each channel.  Valid transactions will update the world state database, whereas an invalid transaction will not. Chaincodes (also known as "smart contracts") are the pieces of software containing a set of functions allowing for reads and writes to be made against the ledger.  Client-side applications leverage an SDK to interface with a peer or peers, and ultimately call functions on a specific chaincode.  There are two key Fabric APIs that allow a chaincode to read or write - `getState` & `putState`.

**Figure 1** depicts an example permissioned blockchain network, which features a distributed, decentralized peer-to-peer architecture, and a Certificate Authority managing user roles and permissions:
![Blockchain Network](images/Architecture_network_and_application.png "Example permissioned blockchain network")
*Figure 1. A permissioned blockchain network: data flow and network access are governed by member roles*

The following descriptions correspond to the architecture and flow shown in Figure 1 (note: these do not represent a sequential process):

**A:** A Blockchain User submits a transaction to the Blockchain network. The transaction can be a deploy, invoke or query, and is issued through a client-side application leveraging an SDK, or directly through a REST API.  

**B:** Trusted business networks provide access to regulators and auditors (the SEC in a U.S. equities market, for example).  

**C:** A Blockchain Network Operator manages member permissions such as enrolling the Regulator (B) as an "auditor" and the Blockchain User (A) as a "client." An auditor could be restricted only to querying the ledger whereas a client could be authorized to deploy, invoke, and query certain types of chaincode. 

**D:** A Blockchain Developer writes chaincode and client-side applications. The Blockchain Developer can deploy chaincode directly to the network through a REST interface. To include credentials from a traditional data source in chaincode, the developer could use an out-of-band connection to access the data (G). 

**E:** A Blockchain User connects to the network through a peer node (A). Before proceeding with any transactions, the node retrieves the user's enrollment and transaction certificates from the Certificate Authority. Users must possess these digital certificates in order to transact on a permissioned network.

**F:** A user attempting to drive chaincode could be required to verify their credentials on a traditional data source (G). To confirm the user's authorization, chaincode can use an out-of-band connection to this data through a traditional processing platform.

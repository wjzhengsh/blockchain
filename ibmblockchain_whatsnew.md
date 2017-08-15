---

copyright:
  years: 2016, 2017
lastupdated: "2017-07-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# What's new
{: #whatsnew}

The {{site.data.keyword.blockchainfull}} **Enterprise Plan** is built on a production commit of the [Hyperledger Fabric V1.0 ![External link icon](images/external_link.svg "External link icon")](https://www.hyperledger.org/){:new_window} open source codebase. Hyperledger Fabric V1.0 implements a modular architecture that provides a resilient, secure, and customizable platform for building enterprise blockchain networks.  
{: shortdesc}

The **Enterprise Plan** service moves beyond a single sandbox environment to deliver a distributed blockchain network with resource groups spanning across various Bluemix organizations.

## Network architecture
{:why}

The Hyperledger Fabric architecture has evolved significantly in V1.0 to power enterprise-grade networks focused on security, scalability, confidentiality, and performance. Peers are split into two distinct runtimes - **endorsing** and **committing** - and responsibility for transaction ordering is handled by a separate component. Concerns about privacy and confidentiality are addressed through channels that provide data segregation. Ledgers exist on a per-channel basis so networks can be customized to support different combinations of bilateral, multilateral or even public transactions.

For more information about network topology and interaction, see the *Architecture Explained* topic in [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

## Channel

A channel is a private “subnet” of communication between two or more specific network members that conducts private and confidential transactions. 

Imagine you are on a network with six members. You might have a consortium-type channel where all six members transact and maintain a ledger for a common asset. These transactions and the state of the involved assets would be available to all members. However, for certain bilateral or multilateral transactions that require privacy from the network at large, channels can be created to conceal this data.  

There are also methods for channel-to-channel interaction in the case of more complex business scenarios. An application can be coded to query for the values of a key or composite key on Channel A and then use the returned values to factor into a transaction on Channel B. For more information on channels, policies, and cross-channel transactions, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/arch-deep-dive.html){:new_window}.

## Additional changes

The {{site.data.keyword.blockchain}} Enterprise Plan also includes the following changes:
* The new [Network monitor](v10_dashboard.html) allows subscribers to invite members, create channels, install and instantiate chaincodes, and manage network resources.
* The new [Sample applications](howto/sample_applications.html) enable experimentation with the latest codebase.
* The new [Transaction flow](reference/v10_fabric.html#transaction-flow) ensures data consistency and integrity by implementing multiple checkpoints throughout the transaction process.

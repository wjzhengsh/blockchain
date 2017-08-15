---

copyright:
  years: 2017
lastupdated: "2017-03-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# HSBN and Starter plans
{: #ibmblockchain_etn}


The IBM Blockchain on Bluemix Starter Developer and High Security Business Network (HSBN) plans are built on Hyperledger Fabric v0.6. Each instance runs as a four-node network, with a Certificate Authority managing identities and member permissions. The Starter Developer network runs in a shared multi-tenant environment on {{site.data.keyword.BluSoftlayer}}, whereas the HSBN runs as an isolated single tenant within IBM Secure Service Container.
{:shortdesc}

<!---The High-Security business network provides important capabilities above and beyond the two-node multi-tenant developer service on Softlayer (aimed towards application development; writing chaincode and experimenting with APIs).  The high security plan supplies your own private blockchain test environment, which has been vetted and secured by IBM.  With the following features, your dedicated and high security environment enables you to take the next step towards preparing your organization for enterprise blockchain networks:~~

~~1. A dedicated four-node blockchain network; single-tenant with no shared resources~~
~~2. An IBM-certified version of the latest Hyperledger fabric, along with mechanisms to unlock inherent identity and security features~~
~~3. Isolation and protection from system and platform administrators, root users, and unauthorized users.~~
~~4. Verified test cases for security, consensus, availability, and performance--->


## Blockchain network architecture

**Figure 2** shows the IBM Blockchain core components. Membership Services, Blockchain Services and Chaincode Services are logical structures, not a physical partitioning of components into separate processes, address spaces, or virtual machines:
![Reference Architecture](images/Architecture_core_com.png "Reference Architecture")
*Figure 2. Hyperledger fabric reference architecture*

**Membership Services**: A permissioned blockchain requires entities to register for long-term identity credentials (Enrollment Certificates), which can be distinguished according to entity type. For users, an Enrollment Certificate authorizes the Transaction Certificate Authority (TCA) to issue pseudonymous credentials; these certificates authorize transactions submitted by the user. Transaction certificates persist on the blockchain, and enable authorized auditors to associate otherwise unlinkable transactions.

Membership Services manages these user identities through the Certificate Authority peer, providing a distinction of roles by combining elements of Public Key Infrastructure (PKI) and decentralization (consensus). By contrast, non-permissioned networks do not provide member-specific authority or a distinction of roles.

**Blockchain Services**: Manages the shared ledger using a peer-to-peer protocol, which is built on HTTP/2. The data structures are highly optimized to provide the most efficient hash algorithm for maintaining replication of the shared ledger. PBFT is implemented as the consensus protocol.    

**Chaincode Services**: Provides a secured and lightweight method to sandbox chaincode execution on the validating nodes. The environment is a “locked down” and secured container, along with a set of signed base images containing secure OS and chaincode language, as well as runtime and SDK layers for Go, Java, and Node.js. Additional languages can be enabled, if required.

For more information on Hyperledger Fabric 1.0, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# About Starter Plan
{: #starter-plan-about}

***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform Starter Plan is an entry level option that enables organizations to simulate multi-organization blockchain networks, quickly develop applications, and work with sample smart contracts and business networks. It also boasts the same UI experience as other membership options, helping to eliminate any learning curve. New Starter Plan networks that are created after October 04, 2018 are built on Hyperledger Fabric V1.2.1. Older Starter Plan networks remain at Fabric V1.1.0 level.
{:shortdesc}

**Starter Plan** is an environment for those who want to get started developing blockchain networks or start learning about blockchain technology. You can use Starter Plan as a development or test environment that will allow you to start small and scale your network's membership or transaction volume before moving to a production environment.

 To deploy a Starter Plan network, sign up now for your [Starter membership ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) on {{site.data.keyword.cloud_notm}}. When you are ready to start developing your network, visit [getting started with Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).


**Notes:**
- {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan is a development and testing environment, and is not suitable for production workloads. If you need a production environment, see [About Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform is a platform service on {{site.data.keyword.cloud_notm}} and all membership offerings follow the [{{site.data.keyword.cloud_notm}} Services terms ![External link icon](images/external_link.svg "External link icon")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} Services terms") on service level agreements (SLAs). Starter Plan networks are provisioned across **multiple environments** in geographically separated data-centers.

## What Starter Plan offers
{: #starter-plan-about-what-starter-plan-offers}

- **_One-click-ready network_**
    Starter Plan provisions you a live blockchain network with a single click. Each network comes with a CA, an orderer, two organizations (with one peer per organization), and a default channel on which to transact and deploy chaincode. {{site.data.keyword.blockchainfull_notm}} Platform handles the creation and configuration of this network (you'll be able to update it after it goes live), letting you focus on development. Starter Plan networks are built on Fabric V1.2, and use CouchDB as the state database.
- **_Cost efficiency_**
    The Starter Plan membership option provides many of the same blockchain capabilities as Enterprise Plan membership options, but at a lower cost. For more information, see [Starter Plan pricing](/docs/services/blockchain/howto/pricing.html#ibp-pricing-starter-pricing)
- **_Multi-organization network simulation_**
    You can use Starter Plan to simulate building a network with multiple organizations. You do not need to actually invite other organizations to your network, but can act as other organizations yourself. This mechanism enables you to learn how a new organization can join the network, how multiple organizations work together in the network, and so on. You can switch between your organizations from the Network Monitor to view and manage the network from different organizations' view.

{{site.data.keyword.IBM_notm}} does not provide support for networks using Hyperledger Composer in production, including the Composer CLI, JavaScript APIs, REST server, and Web Playground.
{:note}

## Is Starter Plan suitable for you
{: #starter-plan-about-is-starter-suitable-for-you}

If you fit into one of the following situations, the Starter Plan is suitable for you to start your blockchain journey.
- **_Learning {{site.data.keyword.blockchainfull_notm}} Platform._**
    If you are curious about {{site.data.keyword.blockchainfull_notm}} Platform or even new to blockchain, Starter Plan offers you a great way to learn how to develop and govern a real blockchain network. You can find the components that a network consists of, manage membership, learn how to deploy and manage chaincode (also known as "smart contracts"), how to add channels (and to manage channel permissions), and track when a new block is generated, just as in a production network.
- **_Building demo solutions on a live network._**
    Starter Plan provides a powerful environment for demonstrating blockchain applications. The ready-to-use blockchain network enables quick presentations to colleagues, management, and partners through the operational and management tools that the Network Monitor provides.
- **_Moving beyond single organization development._**
    Starter Plan enables you to act as multiple organizations, which allows you to see how the {{site.data.keyword.blockchainfull_notm}} Platform manages collaborative tasks like channel creation and chaincode instantiation, as well as testing applications and invoking transactions. You can also invite others to collaborate in a Starter Plan network (as in Enterprise plans). This helps you you to build in a more realistic environment, with multiple parties endorsing chaincode and transactions.
- **_Iteratively develop and test blockchain applications._**
    Starter Plan offers you a staging area to continuously develop and test your code on a blockchain network. Moving to cloud allows developers and testers to focus on functionalities and to easily move from unit test to functional test. Starter Plan provides the same blockchain network functionalities as well as operational and management tooling as the Enterprise Plan. After you are ready to push your project to one of the Enterprise plans, you can operate the same way as in Starter Plan, but with more opportunities to grow your network.

## Starter Plan considerations
{: #starter-plan-about-considerations}

Starter Plan is an entry point to {{site.data.keyword.blockchainfull_notm}} Platform and is for developing and testing purpose.  Check the following items before you use Starter Plan.

- **{{site.data.keyword.cloud_notm}} account requirement**
    You must have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type. All membership plans that {{site.data.keyword.blockchainfull_notm}} Platform offers require an  {{site.data.keyword.cloud_notm}} paid account. To upgrade your account to a Pay-As-You-Go type, go to **Manage** > **Billing and Usage** > **Billing** in the {{site.data.keyword.cloud_notm}} console, and click **Add Credit Card**.
- **Differences from Enterprise Plan**
    - [CA](/docs/services/blockchain/glossary.html#glossary-CA) and ordering service are not fault tolerant because each organization has only one CA and a network has only one [orderer](/docs/services/blockchain/glossary.html#glossary-orderer).
    - Ordering service uses only [SOLO](/docs/services/blockchain/glossary.html#glossary-solo) [consensus](/docs/services/blockchain/glossary.html#glossary-consensus). A Starter Plan network consists of only one orderer that performs consensus for all peers.
    - [Hardware Security Module (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) is not available to safeguard and manage digital keys for strong authentication and crypto processing.
- **Starter Plan versions and upgrade**
    - New Starter Plan networks that are created after October 04, 2018 are built on Hyperledger Fabric V1.2.1. Older Starter Plan networks remain at Fabric V1.1.0 level.
    - New peers that are added to older Starter Plan networks will be built on Fabric v1.2.1. The performance of your network is not affected because of backwards compatibility.
    - You have the option of using more advanced [channel configuration ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "channel configuration") settings and [Access Control Lists ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-v1.2/access_control.html "Access Control Lists") when you create or update a channel.
    - The [Service discovery ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "service discovery") and [private data ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "private data") features of Hyperledger Fabric v1.2 are not supported on Starter Plan.
    - If you [reset](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network) an older Starter Plan network that is at Fabric V1.1.0, your new network will be at Fabric V1.2 level. If you reset your network, you need to install your chaincode or .bna files on the new network, as well as re-invite members of your old network.
- **Network resource limitation**
    Starter Plan assigns 1 CPU and 4 Gi RAM for each peer, and 20 Gi storage for each {{site.data.keyword.cloud_notm}} service instance, including peers. Chaincode containers and ledger blocks are the most resource intensive network components. Users that have many peers on their network, generate a lot of blocks, or use large chaincode files might experience the effect of resource limitations on performance. You can view your networks usage of storage in the ["Overview" screen of your Network Monitor](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-storage).
- **Maintenance and upgrade**
    Starter Plan maintenance and network updates are performed on a fixed schedule. During the maintenance period, you cannot provision new networks and might notice brief periods of network interruption.
- **Data retention**
    Starter Plan does not guarantee data retention with release upgrades.
- **Migration considerations**
    Migrating the data from a Starter Plan network to other membership plans is not currently supported. However, it is possible to migrate `.bna` files, chaincode, and applications that were tested on Starter Plan. For more information, see [Migrating from Starter Plan to Enterprise Plan](/docs/services/blockchain/howto/migrate_sp_ep.html#migrate_starter_to_enterprise).

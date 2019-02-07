---

copyright:
  years: 2018,2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Migrating from Starter Plan to Enterprise Plan
{: #migrate_starter_to_enterprise}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) offers you a test and development environment to run your PoC, demos, and try out your chaincode and applications. When you are ready to move to a production environment, you can migrate from a Starter Plan network to an [Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) network.
{:shortdesc}

Enterprise Plan networks offer the following production-ready features to support your production workload:

- Blockchain networks available in additional global locations than Starter Plan networks.
- No storage limit, which is 20GB for Starter Plan.
- Enhanced CPU and RAM management to ensure that all networks run smoothly.
- Enhanced security including the following functions:
  - Secure Service Container (SSC) ensures that the blockchain image cannot be tampered and loaded at any given point in time and the appliance code and data is protected confidential at both flight and rest.
  - [Hardware secure module (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) for key management and key storage.
  - Pervasive disk encryption.
- High Availability, Disaster Recovery, Crash Fault Tolerance, and rolling upgrades.
- Optional advanced support.

## Considerations
{: #migrate_starter_to_enterprise_considerations}

Before you migrate from your Starter Plan network to an Enterprise Plan network, you might read the following considerations.

- **Pricing:** The monthly fee for your organization to use an Enterprise Plan network includes $1000 membership fee on a per-instance basis and $1000 peer fee on a per-peer basis. For more information, see [Enterprise Plan pricing](/docs/services/blockchain
/howto/pricing.html#ibp-pricing-enterprise-plan).
- **Hyperledger Fabric version:** Enterprise Plan networks run on Hyperledger Fabric v1.1. Starter Plan networks run on Fabric v1.2.
- **Resources affected:** Chaincode (smart contracts), client applications. Again, be aware of whether your chaincode is leveraging a Fabric v1.2 component or functionality that is not compatible with v1.1 networks.
- **Time required:** It will take at least half a day to migrate a basic network from Starter Plan to Enterprise Plan.
- **Existing ledger data** cannot be moved from Starter Plan networks to Enterprise Plan networks because it is not appropriate for test data to exist in a production environment.
- **Hyperledger Composer** IBM does not provide support for networks using Hyperledger Composer in production, including the Composer CLI, JavaScript APIs, REST server, and Web Playground.

**Note:** A *basic* network includes two organizations with two peers, a single channel, and a single chaincode file. Actual time to migrate might vary depending on the size and complexity of the network components that you require in the Enterprise Plan network.

## Migration checklist
{: #migrate_starter_to_enterprise_checklist}

There are a number of tasks required for preparing to move to an Enterprise Plan network from a Starter network. You can find detailed instructions in later sections, but here is a summary:

- Create an Enterprise Plan network.
- Recreate the desired configuration of organizations and peers from your test Starter Plan network.
- Identify which chaincode, written in Go or Node, that will run in the Enterprise Plan network.
- Update client applications with new API endpoint information for the Enterprise Plan network.

### Create an Enterprise Plan network
{: #migrate_starter_to_enterprise_create_network}

You need to create an Enterprise Plan network before you migrate. For more information, see [Creating an Enterprise Plan network](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network).

### Recreate network configuration
{: #migrate_starter_to_enterprise_config_network}

You might recreate the configuration of organizations (members), channels, and peers of your Starter Plan network in your Enterprise Plan network. You can use the Network Monitor UI to recreate these network resources by inviting the appropriate organizations (note that you won't be able to **switch** organizations as you can in Starter), create channels, and create peers (again, invited organizations will have to create their own peers).

1. Login to your Enterprise Plan network on {{site.data.keyword.cloud_notm}} and enter the Network Monitor.
2. Recreate organizations (members) on the "Members" screen, recreate channels on the "Channels" screen, and recreate peers on the "Overview" screen. For more information about creating network resources, see [Using the Network Monitor](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-overview).
3. Configure channels by adding members and setting channel policies the same way as in your Starter Plan network.

**Note:** To achieve High Availability, you must create at least two peers for your organization, join them to the same channel, and code client applications properly to switch from one peer to the other when necessary.

### Migrate chaincode
{: #migrate_starter_to_enterprise_cc}

Chaincode is developed externally in your local environment and is invoked by your client applications. To install and instantiate chaincode, which was tested in your Starter Plan network, onto selected peers on your Enterprise Plan network, follow the instructions in [Installing, instantiating, and updating a chaincode](/docs/services/blockchain
/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc).

### Update client applications
{: #migrate_starter_to_enterprise_app}

You need to update existing client applications, which are tested against the Starter Plan network, with new API endpoint information for the Enterprise Plan network. For more information, see [Retrieving network credentials and connection profile](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-retrieve-credentials).

With respect to High Availability, it is the responsibility of the client applications to detect when a peer is not responding and route transactions to a different peer.

## What to do with existing Starter Plan networks
{: #migrate_starter_to_enterprise_existing_network}

You might continue to use your existing Starter Plan network as a sandbox environment to incubate new projects and test changes to existing chaincode. Additionally, you can maintain your test ledger data in your Starter Plan network, which is not migrated to the Enterprise Plan network.

Do not delete your Starter network until you complete all testing and verify all is working properly. However, once you no longer need your Starter network and the ledger data in it, you can safely delete the network. For more information, see [Deleting or leaving a network](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-delete-network).

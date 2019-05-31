---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-31"

keywords: IBM Blockchain Platform, release, new features

subcollection: blockchain

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:external: target="_blank" .external}

# What's new
{: #whats-new}

## May 31, 2019
{: #whats-new-5-31-2019}

The second-generation {{site.data.keyword.blockchainfull}} Platform, which enables you to deploy, operate, and monitor your blockchain network, becomes generally available. This release includes a new user interface console that can be used to deploy and manage blockchain components in your own {{site.data.keyword.IBM_notm}} Kubernetes Service cluster on {{site.data.keyword.cloud_notm}}.

This {{site.data.keyword.blockchainfull_notm}} Platform release includes the following key features:

**BUILD ---- Integrated developer experience**
- **Easily code** your smart contracts in Node.js, Golang, or Java, write client applications using the new {{site.data.keyword.blockchainfull_notm}} VS Code extension, leverage **SDK integration** with the console, and learn from our rich tutorials and samples.
- **Simplified DevOps** allows you to move from development to test to production in a single environment by scaling up your Kubernetes resources to add more components.
- **Up-to-date Fabric key features**. Leverage the latest features of Hyperledger Fabric v1.4.1:
  -  [Raft ordering service](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft){: external}
  - **{{site.data.keyword.cloud_notm}} service integration.** Leverage the built-in {{site.data.keyword.cloud_notm}} services, such as {{site.data.keyword.cloud_notm}} Kubernetes Service Dashboard, {{site.data.keyword.IBM_notm}} Log Analysis with LogDNA, and {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM).
  - [**Private data** collections](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) that provide increased data privacy by ensuring that ledger data is shared to only authorized peers via the gossip protocol.
  - [Service Discovery](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html){: external}, allowing you to dynamically discover and update how your application interacts with your network.
  - [Channel Access Control Lists](https://hyperledger-fabric.readthedocs.io/en/release-1.4/access_control.html){: external} that allow you additional control the governance of your channels and smart contracts.

**OPERATE --- Total control of your deployments**
- **Deploy only the components you need**. Connect a peer to multiple channels and networks, or host an ordering service that business partners can connect to.
- **Maintain complete control of your identities**. Store and manage the keys that are used to administer your nodes without storing your private keys on the {{site.data.keyword.cloud_notm}}.
- **Centralized operation**. The {{site.data.keyword.blockchainfull_notm}} Platform console allows you to deploy and manage all of your organizations and nodes in **one central console** without having to rely on {{site.data.keyword.IBM_notm}} or other vendors to manage your orderers or Certificate Authority. You can also add or remove members from a blockchain consortium, create and join channels, and install and instantiate smart contracts from your console.
- **Host or join a network**. Deploy peers hosted in your cluster to multiple channels on multiple clouds, or invite other organizations to join your consortium or channels while the organizations manage their nodes independently across infrastructures.
- **Manage access** of the users who can administer or monitor your nodes.
- **Direct access to the logs** of your nodes from the {{site.data.keyword.IBM_notm}} Kubernetes service. Use the {{site.data.keyword.cloud_notm}} Log Analysis service or a third-party service to extract and analyze your logs.
- **Interact directly with your pods** using the Kubernetes Dashboard. Exec into your pods and containers to execute commands and update certificates from the command line.
- **Dynamic signature collection** that allows better control over collaborative governance over channel configurations.

**GROW --- Scalability and flexibility**
- **Choose your compute**. You have the flexibility to decide the amount of CPU, memory, and storage you want to provision in your Kubernetes cluster. For more information, see [How the {{site.data.keyword.cloud_notm}} Kubernetes Service interacts with the console](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
- **Scale** up and down the resources in your Kubernetes cluster, paying for only what you need. For more information see [Pricing](/docs/services/blockchain/howto/pricing.html#ibp-pricing).
- **Disaster recovery and multizone high availability.** This option duplicates your Kubernetes deployment across zones, enabling high availability (HA) of your components and disaster recovery (DR).
- **Run Anywhere** (instructions coming soon). Thanks to the **unified codebase** of the {{site.data.keyword.blockchainfull_notm}} Platform console, it is possible to run your components on {{site.data.keyword.cloud_notm}}, {{site.data.keyword.cloud_notm}} Private, and third-party public clouds.

- More information about the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta is available in [About {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Instructions on how to deploy the free 2.0 beta release into an {{site.data.keyword.IBM_notm}} Kubernetes Service cluster is available in [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- New tutorials for using the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta are available in the **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** subsection under the **HOW TO** category.
  * [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) guides you through the process of hosting a network by creating an orderer and peer.
  * [Join a network tutorial](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) explains how to join an existing network by creating a peer and joining it to a channel.
  * [Deploy a smart contract on the network](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) provides information on how to write a smart contract and deploy it on your network.
- The {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta offering is based on Hyperledger Fabric v1.4.1 and supports peer-to-peer gossip, service discovery, and private data. Visit this [topic](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) to learn how to configure private data on your network.

## May 9, 2019
{: #whats-new-5-09-2019}

{{site.data.keyword.blockchainfull_notm}} releases version 1.0.0 of the {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio (VS) Code Extension. This developer toolkit contains the following key capabilities:

**A reconfigured user interface for simpler navigation**

**Prescriptive and detailed tutorials that cover how to:**
- Develop a smart contract
- Provision an instance of the {{site.data.keyword.blockchainfull_notm}} service on {{site.data.keyword.cloud_notm}}
- Deploy and transact with your {{site.data.keyword.blockchainfull_notm}} Platform service instance

**All the popular features from previous releases, including:**
- Ability to debug smart contracts
- Sample code
- Updated wallet capability

For more information, see [Developing smart contracts with Visual Studio Code extension](/docs/services/blockchain/vscode-extension.html "Developing smart contracts with Visual Studio Code extension").

## April 23, 2019
{: #whats-new-4-23-2019}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private releases v1.0.2, which uses Hyperledger Fabric v1.4.0 code base and supports deployment on {{site.data.keyword.cloud_notm}} Private v3.1.2.

If you want to upgrade your existing {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private to v1.0.2, see [Upgrading the Helm chart on {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading).

For more information about Hyperledger Fabric v1.4.0, see [Hyperledger Fabric documentation](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){: external}. For more information about {{site.data.keyword.cloud_notm}} Private, see [{{site.data.keyword.cloud_notm}} Private v3.1.2 documentation](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html){: external} .

## February 8, 2019
{: #whats-new-2-08-2019}

{{site.data.keyword.blockchainfull_notm}} Platform releases a free 2.0 beta, the next generation of {{site.data.keyword.blockchainfull_notm}} Platform solutions that enables you to deploy, operate, and monitor your blockchain network. {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta includes a new user interface console, that can be used to deploy and manage blockchain components in your own {{site.data.keyword.IBM_notm}} Kubernetes Service cluster on {{site.data.keyword.cloud_notm}}. The {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta will allow you to:

**Build your network faster and easier within a seamless experience**

*	Smooth integration between smart contract development (VS Code) and network management
*	Simplified DevOps allows you to move from development to test to production from a single console
*	Support for writing smart contracts in Javascript, Java, and Go languages

**Operate and govern networks with total control**

*	Deploy only the blockchain components you need (peer, ordering service, Certificate Authority)
*	Redesigned console lets you manage network components in one place, no matter where they are deployed
*	Maintain complete control of your identities, ledger, and smart contracts

**Grow distributed networks with ease with newly enabled multi-cloud flexibility**

*	Connect to nodes running in any environment (on-premises, public, hybrid clouds)
*	Easily connect a single peer to multiple industry networks
*	Start small, pay as you grow for what you use with no upfront investment, and upgrade easily through Kubernetes

- More information about the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta is available in [About {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Instructions on how to deploy the free 2.0 beta release into an {{site.data.keyword.IBM_notm}} Kubernetes Service cluster is available in [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- New tutorials for using the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta are available in the **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** subsection under the **HOW TO** category.
  * [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) guides you through the process of hosting a network by creating an orderer and peer.
  * [Join a network tutorial](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) explains how to  joining an existing network by creating a peer and joining it to a channel.
  * [Deploy a smart contract on the network](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) provides information on how to write a smart contract and deploy it on your network.
- The {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta offering is based on Hyperledger Fabric v1.4 and supports peer-to-peer gossip, service discovery, and private data. Visit this [topic](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) to learn how to configure private data on your network.

- The {{site.data.keyword.blockchainfull_notm}} Visual Studio Code extension is available from the Visual Studio Code Marketplace. Developers can use the extension to create, test, and deploy smart contracts to an instance of Hyperledger Fabric. The extension is compatible with Hyperledger Fabric 1.3 and greater. For information about using the VS Code extension, see [Tools for smart contracts](/docs/services/blockchain/vscode-extension.html#develop-vscode).  

The table of contents is enhanced by grouping all getting started topics together into a section called **Getting started tutorials**. Additionally, each of the {{site.data.keyword.blockchainfull_notm}} Platform offerings is described in the **About {{site.data.keyword.blockchainfull_notm}} Platform** subsections are now contained under the **LEARN** section.

**Note:** Free cloud credits are no longer available for users of Starter Plan.

## December 7, 2018
{: #whats-new-12-07-2018}

The *Operate Starter Plan Network* and *Operate Enterprise Plan Network* topics are combined and replaced by a single tutorial about [Using the Network Monitor](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## November 27, 2018
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform releases {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private. {{site.data.keyword.cloud_notm}} Private is an application platform for developing and managing containerized applications that are based on Kubernetes and it allows users to deploy Certificate Authorities (CAs), orderers, and peers on x86, LinuxONE and {{site.data.keyword.IBM_notm}} Z. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private is based on Hyperledger Fabric v1.2.1 and is deployed by using Kubernetes Helm charts. After you install the Helm chart, you can find it as a bundled service in the {{site.data.keyword.cloud_notm}} Private Catalog. Note that [this offering](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) is more suitable for experienced Fabric users.

For more information about {{site.data.keyword.cloud_notm}} Private, see [{{site.data.keyword.cloud_notm}} Private v3.1.0 documentation](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html){: external}.

The release of {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private also marks the end of the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer (Beta) program. It is still possible to deploy a peer in {{site.data.keyword.cloud_notm}} Private or in AWS and connect it to a Starter Plan or Enterprise Plan network. For more information, see [deploying a peer against Starter or Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) and [deploying a peer in AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). These peers are considered to be **distributed** peers instead of remote peers because the deployment is customer managed and as a result there is no central location to be remote from.

This release also debuts some improvements to the documentation table of contents. If you are a Starter or Enterprise user, your content can still be found in the **LEARN**, **HOW TO**, **REFERENCE**, and **HELP** sections, and the links are still the same. It might just be in a different subsection in the table of contents.

## November 13, 2018
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform releases {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS).

{{site.data.keyword.blockchainfull_notm}} Platform for AWS enables you to run peers in the AWS Cloud and connect the peers to existing blockchain networks in {{site.data.keyword.cloud_notm}}. Your peers in AWS can leverage the connection profile and other blockchain components in the existing networks, while giving you more flexibility to colocate your peers with other applications outside {{site.data.keyword.cloud_notm}}. For more information, see [About {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## October 4, 2018
{: #whats-new-10-04-2018}

{{site.data.keyword.blockchainfull_notm}} Platform upgrades Starter Plan from Hyperledger Fabric v1.1.0 to v1.2.1. For more information, see [About Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Important:** Fabric v1.2.1 is currently not compatible with the Remote Peer beta, which uses a v1.1.0 peer. Starter Plan networks, which are created before October 4, 2018 and are based on Fabric v1.1.0, are not impacted and can still be used with the Remote Peer beta. {{site.data.keyword.blockchainfull_notm}} Platform will update the Remote Peer beta to use the v1.2.1 peer, which will be compatible with new Starter networks, which are at Fabric v1.2.1 level, and Enterprise networks, which are at Fabric v1.1.0 level. Until the Remote Peer beta is updated to Fabric v1.2.1, do not attempt to deploy a v1.1.0 remote peer with a new v1.2.1 Starter network.

## September 4, 2018
{: #whats-new-9-04-2018}

{{site.data.keyword.blockchainfull_notm}} Platform releases Remote Peer offering Beta. Remote Peer offering is based on Hyperledger Fabric v1.1.0. By using the Remote Peer, you can run {{site.data.keyword.blockchainfull_notm}} Platform peer nodes in your own {{site.data.keyword.cloud_notm}} Private or Amazon Web Services (AWS) cloud environment. For more information, see [About remote peers](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).

## June 15, 2018
{: #whats-new-6-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform releases Starter Plan GA. For more information, see [About Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## May 15, 2018
{: #whats-new-5-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform upgrades Enterprise Plan from Hyperledger Fabric v1.0.0 to v1.1.0. For more information, see [About Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## March, 18, 2018
{: #whats-new-3-18-2018}

{{site.data.keyword.blockchainfull_notm}} Platform releases Starter Plan Beta. Starter Plan offers you a development and testing environment to learn and practice in an {{site.data.keyword.blockchainfull_notm}} Platform network. For more information, see [About Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## August 11, 2017
{: #whats-new-8-11-2017}

{{site.data.keyword.blockchainfull_notm}} Platform replaces {{site.data.keyword.blockchainfull_notm}} on Cloud and releases Enterprise Plan. For more information, see [About Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

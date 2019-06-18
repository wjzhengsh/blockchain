---

copyright:
years: 2018, 2019
lastupdated: "2019-06-18"

keywords: IBM Blockchain Platform, IBM Cloud Private, system requirements, Kubernetes Helm chart, behind a firewall

subcollection: blockchain

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# About {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud
{: #console-icp-about}

The {{site.data.keyword.blockchainfull}} Platform can be deployed across public and private clouds, such as the {{site.data.keyword.cloud_notm}}, your own data center, and third-party public clouds. This multicloud deployment is possible through {{site.data.keyword.cloud_notm}} Private, IBM's Kubernetes based container orchestration platform. This release provides a blockchain console user interface that you can use to deploy and manage blockchain components on an {{site.data.keyword.cloud_notm}} Private cluster. The {{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private uses the Hyperledger Fabric v1.4.1 code base and supports deployment on {{site.data.keyword.cloud_notm}} Private v3.2.0.
{:shortdesc}

## What {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private offers
{: #console-icp-about-offers}

You can use this offering to install the {{site.data.keyword.blockchainfull_notm}} Platform console on a deployment of {{site.data.keyword.cloud_notm}} Private. You can then use the console to create all of the fundamental components of a Hyperledger Fabric blockchain, a Certificate Authority, an ordering service, and peers, on your local cluster. For more information about the building blocks of Hyperledger Fabric networks, see the [blockchain component overview](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). You can also use your console to operate a distributed multicloud network by importing nodes deployed on other {{site.data.keyword.cloud_notm}} Private clusters or on {{site.data.keyword.cloud_notm}}.

This {{site.data.keyword.blockchainfull_notm}} Platform release includes the following key features:

**BUILD ---- Integrated developer experience**
- **Easily code** your smart contracts in Node.js, Golang, or Java, write client applications using the new {{site.data.keyword.blockchainfull_notm}} VS Code extension, leverage **SDK integration** with the user interface console, and learn from our rich tutorials and samples.
- **Simplified DevOps** allows you to move from development to test to production in a single environment by scaling up your Kubernetes resources to add more components.
- **Kubernetes service integration.** Leverage services such as Grafana and Prometheus for logging and Kibana for monitoring.
- **Up-to-date Fabric key features**. Leverage the latest features of Hyperledger Fabric v1.4.1:
  - [Raft ordering service](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft){: external}
  - [Private data collections](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) that provide increased data privacy by ensuring that ledger data is shared to only authorized peers via the gossip protocol.
  - [Service discovery](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html){: external}, allowing you to dynamically discover and update how your application interacts with your network.
  - [Channel access control lists](https://hyperledger-fabric.readthedocs.io/en/release-1.4/access_control.html){: external} that allow you additional control the governance of your channels and smart contracts.

**OPERATE --- Total control of your deployments**
- **Deploy only the components you need**. Connect a peer to multiple channels and networks, or host an ordering service that business partners can connect to.
- **Maintain complete control of your identities**. Store and manage the keys that are used to administer your nodes in your own secure environment.
- **Unified operation**. The {{site.data.keyword.blockchainfull_notm}} Platform console allows you to deploy and manage all of your organizations and nodes in **one console** without having to rely on {{site.data.keyword.IBM_notm}} or other vendors to manage your ordering nodes or Certificate Authority. You can also add or remove members from a blockchain consortium, create and join channels, and install and instantiate smart contracts from your console.
- **Host or join a network**. Deploy peers hosted in your cluster to multiple channels on multiple clouds, or invite other organizations to join your consortium or channels while the organizations manage their nodes independently across infrastructures.
- **Manage access** of the users who can administer or monitor your nodes.
- **Direct access to the logs** of your nodes from your Kubernetes service. Use any supported third-party service to extract and analyze your logs.
- **Interact directly with your pods** using your Kubernetes service.
- **Dynamic signature collection** that allows better control over collaborative governance over channel configurations.

**GROW --- Scalability and flexibility**
- **Choose your compute**. You have the flexibility to decide the amount of CPU, memory, and storage you want to provision in your Kubernetes cluster. For more information, see [How the console interacts with your Kubernetes cluster](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
- **Scale** up and down the resources in your Kubernetes cluster, paying for only what you need. For more information see [Pricing](/docs/services/blockchain/howto/pricing.html#ibp-pricing).
- **Disaster recovery and multizone high availability.** This ability duplicates your Kubernetes deployment across zones, enabling high availability (HA) of your components and disaster recovery (DR).
- **Run Anywhere**. Thanks to the **unified codebase** of the {{site.data.keyword.blockchainfull_notm}} Platform console, it is possible to run your components on any environment supported by {{site.data.keyword.cloud_notm}} Private.

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private is a bundled product for {{site.data.keyword.cloud_notm}} Private and is delivered as a Kubernetes Helm Chart. For more information about {{site.data.keyword.cloud_notm}} Private, see the documentation for [{{site.data.keyword.cloud_notm}} Private version 3.2.0](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.2.0/kc_welcome_containers.html){: external}.

## Is {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private suitable for you
{: #console-icp-about-suitable}

Running the {{site.data.keyword.blockchainfull_notm}} Platform outside of {{site.data.keyword.cloud_notm}} provides you with more flexibility to grow or join a blockchain network. It helps network initiators grow their networks by allowing new members to join while using the platform of their choice. It will allow organizations that are interested in joining blockchain networks to collocate their peers with their existing applications or to integrate with their systems of record.

Users of this offering will manage their own security and infrastructure. {{site.data.keyword.cloud_notm}} doesn't provide those services. Review the [Considerations and limitations](#console-icp-about-considerations) in the next section before you begin.

## Considerations and limitations
{: #console-icp-about-considerations}

Before you begin, ensure that you understand the following limitations:

- You are responsible for the management of health monitoring, security, logging, and resource usage of your blockchain components.
- The console can only be used to create and govern components based on Hyperledger Fabric v1.4.1 or higher.
- You can only deploy one console peer Kubernetes namespace. If you plan to create multiple blockchain networks, for example to create different environments for development, staging, and production, you should create a unique namespace for each environment.
- You can only import nodes that have been exported from other {{site.data.keyword.blockchainfull_notm}} Platform consoles. In order to be able to operate an imported peer or ordering node from the console, you also need to import the associated node's organization MSP definition and administrator identity into your console.
- {{site.data.keyword.blockchainfull_notm}} Platform is only supported on {{site.data.keyword.cloud_notm}} Private v3.2 Enterprise Edition.  {{site.data.keyword.cloud_notm}} Private v3.2 Community Edition is not supported.
- You cannot use {{site.data.keyword.IBM_notm}} Multicloud Manager to install the {{site.data.keyword.blockchainfull_notm}} Platform Helm Chart.

For a look at the environments supported by {{site.data.keyword.cloud_notm}} Private, see [Supported environments](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.2.0/supported_environments/environments_overview.html){: external}.
{:important}

## System prerequisites
{: #console-icp-about-prerequisites}

See [System requirements](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.2.0/supported_system_config/system_reqs.html){: external} for the list of hardware and software prerequisites for {{site.data.keyword.cloud_notm}} Private. Note however that the {{site.data.keyword.blockchainfull_notm}} Platform is not supported on Linux on Power (ppc64le) POWER8 systems.

The {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Helm chart has been validated to run on {{site.data.keyword.cloud_notm}} Private v3.2 clusters on Ubuntu Linux by using the following worker nodes and backing storage:

- **LinuxONE and {{site.data.keyword.IBM_notm}} Z**: z/VM and KVM, which are using NFS.
- **x86**: Linux 64-bit that uses GlusterFS.

## License
{: #console-icp-about-license}

See [Pricing for {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud](/docs/services/blockchain?topic=blockchain-ibp-software-pricing) for more information on licensing and pricing.

## Installing {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #console-icp-about-install}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private is delivered as a Helm chart file that can be installed in a local {{site.data.keyword.cloud_notm}} Private cluster. After you install the Helm chart, you can find {{site.data.keyword.blockchainfull_notm}} Platform as an application in the {{site.data.keyword.cloud_notm}} Private Catalog. For instructions on how to install the Helm chart and the necessary prerequisites, see [Installing {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/console-helm-install.html#helm-console-install).

If you are a new user of {{site.data.keyword.cloud_notm}} Private and would like information and tips on installing and deploying {{site.data.keyword.cloud_notm}} Private, see [Setting up {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_console_setup.html#icp-console-setup).

You can deploy the {{site.data.keyword.blockchainfull_notm}} Platform behind a firewall, without having access to the public Internet. The downloaded Helm chart package includes all of the Fabric component Docker images that {{site.data.keyword.blockchainfull_notm}} Platform uses, without pulling them from DockerHub during deployment.

## Security Considerations
{: #console-icp-about-security}

Because these components are deployed on your own infrastructure, you are responsible for managing their security. This includes important areas of security, such as key management and data encryption. Review the following topics when you consider security for your components.

### Data security
{: #console-icp-about-security-data}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} uses whole disk encryption that is based on [symmetric key encryption](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html){: external} to protect all the data created by each service instance. You must take similar steps in your own environment to protect your peer data.

The data in your state database, whether you use LevelDB or CouchDB, is not encrypted. You can use application level encryption to protect the data at rest in your state database.

<!--
### Data residency
{: #console-icp-about-security-data-residency}

Data residency requirements can mandate that the processing and storage of all blockchain ledger data remain within the border of a single country (or within some other defined boundary). For more information about how data residency can be accomplished, see [Data residency](#console-icp-about-data-residency).
-->

### Key management
{: #console-icp-about-security-key-management}

Key management is a critical aspect of security. If a private key is compromised or lost, hostile actors might be able to access your data and functionality. {{site.data.keyword.IBM_notm}} uses physical appliances that are called [Hardware Security Modules](/docs/services/blockchain/glossary.html#glossary-hsm) (HSM) to store the private keys of {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan networks.

You are responsible for managing your private keys. Although you can use the {{site.data.keyword.blockchainfull_notm}} Platform console UI to generate private keys, those keys are not stored by the console or within {{site.data.keyword.cloud_notm}} Private. It is essential to store your keys securely so that they are not compromised.

You can use Key Escrow to recover lost private keys. This needs to be performed prior to the loss of any keys. If a private key cannot be recovered, you need to get new private keys by registering a new identity with your Certificate Authority. You should also remove and replace your signCert from any channels that you joined.

### TLS
{: #console-icp-about-security-tls}

[Transport Layer Security](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm){: external} (TLS) is embedded in the trust model of Hyperledger Fabric. All {{site.data.keyword.blockchainfull_notm}} Platform components use TLS to authenticate and communicate with each other. Therefore, nodes on {{site.data.keyword.cloud_notm}} Private need to be able to complete a TLS handshake with other components and your applications. One implication of this is that you need to enable passthru, by using white listing for example, in your firewall from client apps to your nodes.

### Application security
{: #console-icp-about-security-appl}

Because all chaincode invocations are signed, Fabric manages the application security. In addition, Fabric also includes ACL-based application level checks.

## Getting support
{: #console-icp-about-support}

For more information about how to get support on {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}, as well as free blockchain developer resources and support forums that you can use to troubleshoot problems, see [Getting support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

If you have purchased an {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private license and you want to contact Customer support, see the information about [accessing the {{site.data.keyword.IBM_notm}} Support Community and opening a support ticket](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041){: external}.

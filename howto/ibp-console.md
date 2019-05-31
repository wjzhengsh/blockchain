---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: key features, build, operate, grow, architecture, multi-zone clusters

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# About {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}
{: #ibp-console-overview}

The {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} is the next generation of {{site.data.keyword.blockchainfull_notm}} Platform offerings, which gives you total control over your deployments, certificates, and private keys. It includes the new {{site.data.keyword.blockchainfull_notm}} Platform console, a user interface that can simplify and accelerate the process of deploying components into an {{site.data.keyword.cloud_notm}} Kubernetes Service managed and controlled by you. For more information about Kubernetes and {{site.data.keyword.cloud_notm}} Kubernetes Service, see [Kubernetes](/docs/services/blockchain/reference/k8s.html "Kubernetes").
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} is based on Hyperledger Fabric 1.4.1. For more information about Hyperledger Fabric 1.4.1 new features, see [What's new in 1.4 ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/whatsnew.html "What's new in Fabric 1.4"){:new_window}.

## What the new release offers
{: #ibp-console-overview-capabilities}

This latest release is tailored to experienced {{site.data.keyword.blockchainfull_notm}} and Hyperledger Fabric users and lets them host networks or create new organizations that can join other {{site.data.keyword.blockchainfull_notm}} 2.0 networks. If you are an existing Starter or Enterprise Plan customer, instead of {{site.data.keyword.IBM_notm}} managing your network, you now have total control with the ability to provision, monitor, and manage your components inside your own Kubernetes cluster.

This {{site.data.keyword.blockchainfull_notm}} Platform release includes the following key features:

**BUILD ---- Integrated developer experience**
- **Easily code** your smart contracts in Node.js, Golang, or Java, write client applications using the new {{site.data.keyword.blockchainfull_notm}} VS Code extension, leverage **SDK integration** with the console, and learn from our rich tutorials and samples.
- **Simplified DevOps** allows you to move from development to test to production in a single environment by scaling up your Kubernetes resources to add more components.
- **Up-to-date Fabric key features**. Leverage the latest features of Hyperledger Fabric v1.4.1:
  -  [Raft ordering service ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft "Raft ordering service")]
  - **{{site.data.keyword.cloud_notm}} service integration.** Leverage the built-in {{site.data.keyword.cloud_notm}} services, such as {{site.data.keyword.cloud_notm}} Kubernetes Service Dashboard, {{site.data.keyword.IBM_notm}} Log Analysis with LogDNA, and {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM).
  - [**Private data** collections](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) that provide increased data privacy by ensuring that ledger data is shared to only authorized peers via the gossip protocol.
  - [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery"), allowing you to dynamically discover and update how your application interacts with your network.
  - [Channel Access Control Lists ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/access_control.html "Access Control Lists") that allow you additional control the governance of your channels and smart contracts.

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
- **Disaster recovery and multi-region high availability.** This option duplicates your Kubernetes deployment across regions, enabling high availability (HA) of your components and disaster recovery (DR).
- **Run Anywhere** (instructions coming soon). Thanks to the **unified codebase** of the {{site.data.keyword.blockchainfull_notm}} Platform console, it is possible to run your components on {{site.data.keyword.cloud_notm}}, {{site.data.keyword.cloud_notm}} Private, and third-party public clouds.

Check out this [blog ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/blogs/blockchain/2019/02/taking-the-next-step-towards-deploying-blockchain-anywhere "Taking the next step towards deploying blockchain for business anywhere") on taking the next step towards deploying blockchain for business anywhere.

This offering is intended for experienced Fabric users who want to build and manage their own networks.

## Considerations
{: #ibp-console-overview-considerations}

Before you deploy the console, ensure that you understand the following considerations:

- Because the availability of the beta trial and the generally available (GA) release of the {{site.data.keyword.blockchainfull_notm}} Platform will overlap, it is important to make sure you know what version of the {{site.data.keyword.blockchainfull_notm}} Platform you are using. New features and fixes will not be pushed to the beta, but they will be available in the GA version of the {{site.data.keyword.blockchainfull_notm}} Platform. As a result, if you are using the beta version of {{site.data.keyword.blockchainfull_notm}} Platform, it is likely that some panels in your console will not match the current documentation, which is kept up to date with the generally available service instance. To gain the benefits of all the latest functionality, you are encouraged at to provision a new GA service instance. To learn how to do that, see [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

- All peers deployed with this release use CouchDB as their state database.
- You are responsible for the management of health monitoring, security, and logging of your Kubernetes cluster. See this [information ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Cluster management responsibilities") for details on what {{site.data.keyword.cloud_notm}} manages and what you are responsible for.
- You are also responsible for monitoring the resource usage of your Kubernetes cluster. To monitor your Kubernetes resources, we recommend using the [{{site.data.keyword.cloud_notm}} SysDig ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/cloud/sysdig "IBM Cloud Monitoring with Sysdig") tool in combination with your {{site.data.keyword.cloud_notm}} Kubernetes dashboard. If you need to increase storage capacity or performance of your cluster, see this information on how to [modify your existing volume ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "Changing the size and IOPS of your existing storage device").
- You are responsible for managing and securing your certificates and private keys. {{site.data.keyword.IBM_notm}} does not store your certificates in the Kubernetes cluster or in the console. They are only kept in the local storage of your browser. If you switch browsers, you will have to import your created identities into that browser.
- {{site.data.keyword.blockchainfull_notm}} Platform is available in select regions. Refer to this topic on [{{site.data.keyword.blockchainfull_notm}} Platform locations](/docs/services/blockchain/howto?topic=blockchain-ibp-regions-locations) for an updated list.
- Kubernetes must be version 1.11 or higher in your {{site.data.keyword.cloud_notm}} Kubernetes cluster. Use these instructions to [upgrade your new and existing clusters](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) to this version.
- You must provision storage for your Kubernetes cluster and make it the default storage class for the cluster. This storage is used by your peer, ordering service and Certificate Authority nodes. See this topic on [Kubernetes storage ![External link icon](../images/external_link.svg "External link icon")](/docs/containers?topic=containers-kube_concepts "Understanding Kubernetes storage basics") to decide what type of storage to provision.
- You can preview the {{site.data.keyword.blockchainfull_notm}} Platform at no charge for 30 days when you link your {{site.data.keyword.blockchainfull_notm}} Platform service instance to an {{site.data.keyword.cloud_notm}} Kubernetes free cluster.  Performance will be limited by throughput, storage and functionality. {{site.data.keyword.cloud_notm}} will delete your cluster after 30 days and you cannot migrate any nodes or data from a free cluster to a paid cluster. And while the {{site.data.keyword.blockchainfull_notm}} Platform beta trial is free, if you choose a paid Kubernetes cluster instead of the limited free cluster, you will incur charges for the Kubernetes service to your {{site.data.keyword.cloud_notm}} account.

## Migration
{: #ibp-console-overview-migration}

Currently, migration from any {{site.data.keyword.blockchainfull_notm}} platform offering to the {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} is not possible.

All {{site.data.keyword.blockchainfull_notm}} platform beta trial service instances cannot be migrated to the Generally Available (GA) release.

## License and pricing
{: #ibp-console-overview-license-and-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} introduces a new hourly pricing model based on virtual processor core (VPC) usage. The simplified model is based on the amount of CPU (or VPC) that your {{site.data.keyword.blockchainfull_notm}} Platform nodes consume on an hourly basis, at a flat rate of **$0.29 USD/VPC-hour**, where **1 VPC = 1 CPU**. See this topic on [Pricing](/docs/services/blockchain?topic=blockchain-ibp-console-overview) for more details.

## Getting started
{: #ibp-console-overview-deploy}

For information about how to deploy {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}, see [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

For more information about how to use the console to start deploying nodes and building consortium, see the [Building your network](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) tutorial. This tutorial guides you through the process of using the console to create a sample network with three organizations, one ordering organization, two peer organizations, and a channel with two peers joined to it. You can use this sample network to for demos or proofs of concept or adjust and expand the steps in the tutorial to create your own custom blockchain configuration.

## Architecture reference
{: #ibp-console-overview-architecture}

The following illustration shows the components of your blockchain network and how they interact.
![Sample network structure](../images/IBP_V2_Architecture.png "Archtecture reference")
*Figure 1. Architecture reference*

Notice how a single instance of the console, also known as Operational Tooling, is created for each {{site.data.keyword.blockchainfull_notm}} Platform Service Instance. When a peer, orderer or CA node is deployed by using the console, it is deployed into the **Kubernetes Cluster Service Instance**.

| **{{site.data.keyword.blockchainfull_notm}} Platform Kubernetes Cluster** | **Description** |
| ------------------------- |-----------|
| Operational Tooling | Also known as the `console`, this is your central user interface for operating all of your blockchain components. With this console you can now create CA, peer, and orderer nodes, create channels and install and instantiate smart contracts developed with the Hyperledger Fabric v1.4 VS Code extension. The console is deployed in an {{site.data.keyword.IBM_notm}}-owned cluster. There is no charge for this tooling or the Kubernetes cluster where it runs.|


| **Kubernetes Cluster Service Instance** | **Description** |
| ------------------------- |-----------|-----------|-----------|
| **Tiller** | Part of the [Helm tooling ![External link icon](../images/external_link.svg "External link icon")](https://docs.helm.sh/glossary/#tiller "Tiller"), the Tiller runs inside the Kubernetes cluster to manage the installations of your peer, CA and orderer Helm charts. |
| **Ingress** | A [Kubernetes object ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/services-networking/ingress/ "Ingress") that allows access to the cluster resources from outside the cluster. |
| **Proxy** | The {{site.data.keyword.blockchainfull_notm}} Platform proxy is responsible for routing traffic to the correct peer, CA and orderer nodes by using host header routing. |
| **Peers, CAs, Orderers** | These are the nodes that are created by deploying the underlying helm charts. Note: These nodes could also be imported from other Kubernetes Cluster Service Instances. Because the keys are never stored by {{site.data.keyword.IBM_notm}}, every peer and orderer node includes a gRPC web proxy that allows the console to communicate with each node by using the keys in the console wallet. |
| **RBAC** | Role based access control.  The {{site.data.keyword.blockchainfull_notm}} Platform configures [Kubernetes RBAC ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/reference/access-authn-authz/rbac/ "Using RBAC Authorization") in the cluster which is required to manage blockchain components in the cluster.  |

## Using Multi-zone (MZR) clusters with {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-mzr}

When you provision an  {{site.data.keyword.cloud_notm}} Kubernetes cluster, the default configuration option is to have multiple zones in a single region. Although not required, this capability allows you to deploy your blockchain components across different zones.

If you do decide to use multi-zone regions with your paid Kubernetes cluster, you must enable multi-zone storage.
{: important}

There are a variety of [Kubernetes storage options](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview) available. Coming soon, IBM Cloud will support the Portworx storage solution which is the recommended solution. Until then, if you want to include mulit-zone support in your cluster, you need to bring your own storage solution.  

To add your own storage solution, you must create a customized storage class. Read about how to
[add a storage class](https://cloud.ibm.com/docs/containers/cs_storage_basics.html#storageclasses) for your solution. The {{site.data.keyword.blockchainfull_notm}} Platform uses [dynamic volume provisioning](https://cloud.ibm.com/docs/containers/cs_storage_basics.html#dynamic_provisioning) with the `default` storage class. Therefore, to change the default storage class, run the following command:

```
kubectl patch storageclass <storageclass> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
{:codeblock}

Replace ``<storageclass>`` with the name of your storage class.

Finally, be sure to enable multi-zone regions when you create your paid cluster.

## Getting support
{: #ibp-console-overview-support}

For more information about how to get support on {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}, as well as free blockchain developer resources and support forums that you can use to troubleshoot problems, see [Getting support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

Support for the {{site.data.keyword.blockchainfull_notm}} Platform beta trial is limited during the beta period that ends August 3, 2019.

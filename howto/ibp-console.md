---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# About {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta
{: #ibp-console-overview}

***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

The {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta is the next generation of {{site.data.keyword.blockchainfull_notm}} Platform offerings, giving you total control over your deployments and certificates. This free beta includes the new {{site.data.keyword.blockchainfull_notm}} Platform console, a user interface that can simplify and accelerate the process of deploying components into a {{site.data.keyword.cloud_notm}} Kubernetes Service managed and controlled by you.
{:shortdesc}

## What the new beta offers
{: #ibp-console-overview-capabilities}

This latest release is tailored to experienced {{site.data.keyword.blockchainfull_notm}} and Hyperledger Fabric users and lets them host networks or create new organizations that can join other {{site.data.keyword.blockchainfull_notm}} 2.0 networks. If you are an existing Starter or Enterprise Plan customer, instead of {{site.data.keyword.IBM_notm}} managing your network, you now have total control with the ability to provision, monitor and manage your your components inside your own Kubernetes cluster.

The {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta release includes the following key features:

**Integrated developer experience**
- **Easily code** your smart contracts in Node.js, Golang, or Java, write client applications using the new {{site.data.keyword.blockchainfull_notm}} VSCode extension, leverage **SDK integration** with the console, and learn from our rich tutorials and samples.
- **Simplified DevOps** allows you to move from development to test to production in a single environment by scaling up your Kubernetes resources to add more components.
- **Up-to-date Fabric key features.** Leverage the latest features of Hyperledger Fabric v1.4:
  - [**Private data** collections](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) that provide increased data privacy by ensuring that ledger data is shared to only authorized peers via the gossip protocol.
  - [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery"), allowing you to dynamically discover and update how your application interacts with your network.
  - [Channel Access Control Lists ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/access_control.html "Access Control Lists") that allow you additional control the governance of your channels and smart contracts.
- **{{site.data.keyword.cloud_notm}} service integration.** Leverage the built in {{site.data.keyword.cloud_notm}} services, such as {{site.data.keyword.cloud_notm}} Kubernetes Service Dashboard, {{site.data.keyword.cloud_notm}} Logging and Metrics Service, and the {{site.data.keyword.cloud_notm}} Identity Service.

**Total control of your deployments**
- **Deploy only the components you need**. Connect a peer to multiple channels and networks, or host an ordering service that business partners can connect to.
- **Maintain complete control of your identities**. Store and manage the keys that are used to administer your nodes without storing your private keys on the {{site.data.keyword.cloud_notm}}.
- **Centralized operation** The {{site.data.keyword.blockchainfull_notm}} Platform console allows you to deploy and manage all of your organizations and nodes in **one central location** without having to rely on {{site.data.keyword.IBM_notm}} or other vendors to manage your orderers or Certificate Authority. You can also add or remove members from a blockchain consortium, create and join channels, and install and instantiate smart contracts from your console.
- **Host or join a network** Deploy peers hosted in your cluster to multiple channels on multiple clouds, or invite other organizations to join your consortium or channels while the organizations manage their nodes independently across infrastructures.
- **Manage access** of the users who can administer or monitor your nodes.
- **Direct access to the logs** of your nodes from the {{site.data.keyword.IBM_notm}} Kubernetes service. Use the {{site.data.keyword.cloud_notm}} Log Analysis service or a third party service to extract and analyze your logs.
- **Interact directly with your node pods** using the Kubernetes Dashboard. Exec into your pods and containers to execute commands and update certificates from the command line.

**Scalability and flexibility**
- **Chose your compute.** You have the flexibility to decide the amount of CPU, memory, and storage you want to provision in your Kubernetes cluster.
- **Scale** up and down the resources in your Kubernetes cluster, paying for only what you need.
- **Disaster recovery and multi-zone high availability.** This option duplicates your Kubernetes deployment across zones, enabling high availability (HA) of your components and disaster recovery (DR).

This offering is for experienced Fabric users who want to build and manage their own networks. The ability to deploy an entire network with a single click is coming soon. In the meantime, to deploy a network with one click that uses a standard configuration, see our documentation about [Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan) instead.

## Considerations
{: #ibp-console-overview-considerations}

Before you deploy the console, ensure that you understand the following considerations:

- {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta is built by using Hyperledger Fabric v1.4.
- All peers deployed using the free 2.0 beta use CouchDB as their state database.
- You have the option to use a free Kubernetes cluster for evaluation of the beta offering, however capacity and performance are limited, none of your data can be migrated, and the cluster is deleted after 30 days.
- You are responsible for the management of health monitoring, security, and logging of your Kubernetes cluster. See this [information ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Cluster management responsibilities") for details on what {{site.data.keyword.cloud_notm}} manages and what you are responsible for.
- You are also responsible for monitoring the resource usage of your Kubernetes cluster by using the Kubernetes dashboard. If you need to increase storage capacity or performance of your cluster, see this information on how to [modify your existing volume ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Changing the size and IOPS of your existing storage device").
- You are responsible for managing and securing your certificates, your public and private keys. {{site.data.keyword.IBM_notm}} does not store your certificates in the Kubernetes cluster or in the console. They are only kept in the local storage of your browser. If you switch browsers, you will have to import your created identities into that browser.
- The free 2.0 beta offering is available in only the **Dallas** region of {{site.data.keyword.cloud_notm}}. Therefore, all blockchain components will reside in the Dallas data center. They are not deployed elsewhere.
- While the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 beta offering is free, if you choose a paid Kubernetes cluster instead of the limited free cluster, you will incur charges to your {{site.data.keyword.cloud_notm}} account.

## Migration
{: #ibp-console-overview-migration}

Migration to the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta is not supported. But migrating to the generally available (GA) release will be possible under the following circumstances:
- Enterprise Plan customers will be able to migrate to {{site.data.keyword.blockchainfull_notm}} Platform 2.0 when it becomes generally available.
- If you are using a paid {{site.data.keyword.cloud_notm}} Kubernetes Service, you will be able to migrate your  {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta network to the the GA release.

Deployments to the free {{site.data.keyword.cloud_notm}} Kubernetes Service will not be migrated, nor will migrations from Starter Plan.

## License and pricing
{: #ibp-console-overview-license-and-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta is free. An instance of the console is included with each deployment of the platform into an {{site.data.keyword.cloud_notm}} Kubernetes Service cluster. You need to pay for the Kubernetes cluster if you choose to deploy the console into a standard cluster of {{site.data.keyword.cloud_notm}} Kubernetes Service.

## Getting started
{: #ibp-console-overview-deploy}

An instance of the console is deployed into an {{site.data.keyword.IBM_notm}} Kubernetes Service cluster as part of the deployment of each instance of the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta.

For more information about deploying the console and platform, see [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

For more information about how to use the console to start deploying nodes and building consortium, see the [Building your network](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) tutorial. This tutorial will guide you through the process of using the console to create a sample network with three organizations, one ordering organization, two peer organizations, and a channel with two peers joined to it. You can use the sample network to deploy demos or proofs of concept or adjust and expand the steps in the tutorial to create your own configuration.

## Architecture reference
{: #ibp-console-overview-architecture}

The following illustration shows the components of your blockchain network and how they interact.
![Sample network structure](../images/IBP_V2_Architecture.png "Archtecture reference")
*Figure 1. Architecture reference*

Notice how a single instance of the console, also known as Operational Tooling, is created for each {{site.data.keyword.blockchainfull_notm}} Platform Service Instance. When a peer, orderer or CA node is deployed by using the console, it is deployed into the **Kubernetes Cluster Service Instance**.

| **IBM Blockchain Platform Kubernetes Cluster** | **Description** |
| ------------------------- |-----------|
| Operational Tooling | Also known as the `console`, this is your central user interface for operating all of your blockchain components. With this console you can now create CA, peer, and orderer nodes, create channels and install and instantiate smart contracts developed with the Hyperledger Fabric v1.4 VSCode extension. The console is deployed in an {{site.data.keyword.IBM_notm}}-owned cluster.|


| **Kubernetes Cluster Service Instance** | **Description** |
| ------------------------- |-----------|-----------|-----------|
| **Tiller** | Part of the [Helm tooling ![External link icon](../images/external_link.svg "External link icon")](https://docs.helm.sh/glossary/#tiller "Tiller"), the Tiller runs inside the Kubernetes cluster to manage the installations of your peer, CA and orderer Helm charts. |
| **Ingress** | A [Kubernetes object ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/services-networking/ingress/ "Ingress") that allows access to the cluster resources from outside the cluster. |
| **Proxy** | The {{site.data.keyword.blockchainfull_notm}} Platform proxy is responsible for routing traffic to the correct peer, CA and orderer nodes by using host header routing. |
| **Peers, CAs, Orderers** | These are the nodes that are created by deploying the underlying helm charts. Note: These nodes could also be imported from other Kubernetes Cluster Service Instances. Because the keys are never stored by {{site.data.keyword.IBM_notm}}, every peer and orderer node includes a gRPC web proxy that allows the console to communicate with each node by using the keys in the console wallet. |
| **RBAC** | Role based access control.  The {{site.data.keyword.blockchainfull_notm}} Platform configures [Kubernetes RBAC ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/reference/access-authn-authz/rbac/ "Using RBAC Authorization") in the cluster which is required to manage blockchain components in the cluster.  |

## High Availability (HA) and Disaster Recovery (DR)
{: #ibp-console-overview-hadr}

Multi-zone storage must be enabled in your paid IBM Cloud Kubernetes cluster if you require HA and DR. There are a variety of [Kubernetes storage options](https://console.bluemix.net/docs/containers/cs_storage_planning.html#persistent_storage_overview) available. Coming soon, IBM Cloud will support the Portworx storage solution which is the recommended solution. Until then, if HA and DR are required for your implementation, you will need to bring your own storage solution.  

To add your own storage solution, you will need to create a customized storage class. Read about how to
[add a storage class](https://console.bluemix.net/docs/containers/cs_storage_basics.html#storageclasses) for your solution. IBP uses [dynamic volume provisioning](https://console.bluemix.net/docs/containers/cs_storage_basics.html#dynamic_provisioning) with the `default` storage class. Therefore,  to change the default storage class, run the following command:
```
kubectl patch storageclass <storageclass> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
{:codeblock}

Replace ``<storageclass>`` with the name of your storage class.  

Finally, be sure to enable multi-zone regions when you create your paid cluster.

## Getting support
{: #ibp-console-overview-support}

For more information about how to get support on {{site.data.keyword.blockchainfull_notm}} Platform 2.0, as well as free blockchain developer resources and support forums that you can use to troubleshoot problems, see [Getting support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

Support for the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 will be limited during the beta period.
{: important}

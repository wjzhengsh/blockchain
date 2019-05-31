---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-31"

keywords: IBM Blockchain Platform offerings, IBM Cloud Private, AWS, VS code extension, IBM Cloud

subcollection: blockchain

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
{:external: target="_blank" .external}

# Getting started with {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp}

{{site.data.keyword.blockchainfull}} Platform provides a managed and full stack blockchain-as-a-service (BaaS) offering that allows you to deploy blockchain components in environments of your choice. The environment can be {{site.data.keyword.cloud_notm}}, on-premises through {{site.data.keyword.cloud_notm}} Private, and third-party clouds, such as Amazon Web Services (AWS). In this tutorial, we'll take you through the general process to set up a basic blockchain network with {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

Before you use an {{site.data.keyword.blockchainfull_notm}} Platform offering, read the technical and support information in the [Disclaimer](/docs/services/blockchain/needtoknow.html#disclaimer) section.
{: important}


## Before you begin
{: #get-started-ibp-prereqs}

{{site.data.keyword.blockchainfull_notm}} Platform provides different offerings to help all types of users get started on their blockchain journey and move their applications into production. You need to decide your environment and the offering that you will use. Figure 1 lists the capabilities, target audiences, pricing, and cloud platforms for different offerings. For more information about each offering, click the offering in the following table.

| **Offerings** | **What's included** | **Billing policy** | **Cloud platform** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**{{site.data.keyword.blockchainfull_notm}} Platform Extension for VS Code**](/docs/services/blockchain?topic=blockchain-develop-vscode#develop-vscode) | Developers can start with the IDE that provides an explorer and commands accessible from the command palette for developing smart contracts quickly. | Free | Runs on your local machine |
| [**{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | {{site.data.keyword.blockchainfull_notm}} Platform console and APIs that can be used to deploy and manage blockchain components in your {{site.data.keyword.cloud_notm}} Kubernetes cluster | [VPC pricing $0.29 USD/VPC-hour](/docs/services/blockchain/howto/pricing-saas.html) | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | Deployable CA, Orderer, and Peer Helm charts | [VPC pricing](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) and Free Community Edition | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | AWS Quick Start Template to deploy remote peers that are outside {{site.data.keyword.cloud_notm}} | Free | AWS |

*Figure 1. {{site.data.keyword.blockchainfull_notm}} Platform offerings*


## Step one: Get an offering
{: #get-started-ibp-step1}

Ensure that you have the cloud account or PPA license to get an {{site.data.keyword.blockchainfull_notm}} Platform offering.

* **{{site.data.keyword.blockchainfull_notm}} Platform Extension for VS Code**

  This VS Code extension is available for free in the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform){: external} and can be used to develop, debug, and test smart contracts for eventual deployment into {{site.data.keyword.blockchainfull_notm}}.

* **{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}**

  This offering is available in the [{{site.data.keyword.cloud_notm}} Catalog dashboard](https://cloud.ibm.com/catalog){: external} of {{site.data.keyword.cloud_notm}}.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  This offering is delivered as a deployable Helm chart and has both paid edition and free Community edition. You can download the Enterprise Edition from [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html){: external} or download the free Community edition from [GitHub](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz){: external}.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  This offering is available in AWS and you can deploy a blockchain peer in AWS by using the [Quick Start template](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/){: external}.

## Step two: Deploy {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp-step2}

* **{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}**

  Log in to {{site.data.keyword.cloud_notm}} and create a service instance with the offering. Follow the wizard to complete initial configuration for your network. For more information, see [Getting started  {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Kubernetes Service](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Before you deploy a network, you need to [import the Helm chart to your {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). Then, you can [deploy your network components starting from a CA](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#step-three-set-up-your-cas) to build your network.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  This offering is available in AWS and you can deploy a blockchain peer in AWS by using the [Quick Start template](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/){: external}.

## Next steps
{: #get-started-ibp-next-steps}

After you deploy {{site.data.keyword.blockchainfull_notm}} Platform in the environment of your choice, you can set up the network with consortium, nodes, channels, smart contracts, and start transactions on it. For more information, see the task topics under the **HOW TO** section in the left navigation of this documentation.

## Getting support
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}} offers various support options on {{site.data.keyword.IBM_notm}}-implemented blockchain solutions. For more information about {{site.data.keyword.blockchainfull_notm}} Platform support, see [Getting support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

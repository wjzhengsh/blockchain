---

copyright:
  years: 2019
lastupdated: "2019-06-18"

keywords: FAQs, can I, upgrade, what version, peer ledger database, supported languages, why do I, regions

subcollection: blockchain

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# FAQs
{: #ibp-v2-faq}

**General**   

- [What is the value of using {{site.data.keyword.blockchainfull_notm}} Platform over native Hyperledger Fabric?](#ibp-v2-faq-v2-IBP-Overview-1-7)
- [What version of Hyperledger Fabric is being used with {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-v2-Hyperledger-Fabric-3-1)
- [What database do the peers use for their ledger?](#ibp-v2-faq-v2-IBP-Overview-1-3)
- [What languages are supported for smart contracts?](#ibp-v2-faq-v2-IBP-Overview-1-4)
- [Do you support using certificates from non-IBM Certificate Authorities (CAs)?](#ibp-v2-faq-v2-external-certs)  

**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}**  

- [Can I upgrade from V1.0 to the new {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-v2-IBP-Overview-1-5)
- [What happens when I delete my {{site.data.keyword.blockchainfull_notm}} Platform service?](#ibp-v2-faq-v2-IBP-Overview-1-8)
- [What regions are available for the blockchain service running on the {{site.data.keyword.cloud_notm}}?](#ibp-v2-faq-v2-IBP-Overview-1-9)
- [Can I use my existing {{site.data.keyword.cloud_notm}} Kubernetes Service cluster?](#ibp-v2-faq-v2-Infrastructure-4-2)
- [Do we have access to logging services and what logs are available to me?](#ibp-v2-faq-v2-Logging-and-Monitoring-11-6)  

**{{site.data.keyword.blockchainfull_notm}} Platform for Multicloud**    

- [What are the benefits of {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud?](#ibp-v2-faq-icp-benefits)
- [Which environments does {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud support?](#ibp-v2-faq-icp-environments)
- [What is the pricing model for {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud?](#ibp-v2-faq-icp-pricing)
- [What services do I need to install in order to use {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud?](#ibp-v2-faq-icp-services)
- [How can I estimate the {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud  sizing requirements for my dev, test, and production  environments?](#ibp-v2-faq-icp-sizing)
- [What happens to my blockchain components when I delete my Helm release?](#ibp-v2-faq-icp-delete)

## What is the value of using {{site.data.keyword.blockchainfull_notm}} Platform over native Hyperledger Fabric?
{: #ibp-v2-faq-v2-IBP-Overview-1-7}
{: faq}

The {{site.data.keyword.blockchainfull_notm}} Platform enables clients to easily deploy a customized blockchain network. You can use an intuitive console UI to quickly deploy your network, easily install and instantiate smart contracts, and monitor your transactions.

## What version of Hyperledger Fabric is being used with {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-Hyperledger-Fabric-3-1}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} and {{site.data.keyword.blockchainfull_notm}} Platform for Platform for Multicloud use Hyperledger Fabric v1.4.1

## What database do the peers use for their ledger?
{: #ibp-v2-faq-v2-IBP-Overview-1-3}
{: faq}

All peers that are deployed with the {{site.data.keyword.blockchainfull_notm}} Platform use CouchDB as database for the ledger.

## What languages are supported for smart contracts?
{: #ibp-v2-faq-v2-IBP-Overview-1-4}
{: faq}

The {{site.data.keyword.blockchainfull_notm}} Platform supports smart contracts that are written in Go and Node.js. The new Hyperledger Fabric programming model currently supports only Node.js, but more are coming soon. If you are interested in preserving your existing application code, or using Fabric SDKs for languages other than Node.js, you can still connect to your {{site.data.keyword.blockchainfull_notm}} Platform network by using the lower-level Fabric SDK APIs.

## Do you support using certificates from non-IBM Certificate Authorities?
{: #ibp-v2-faq-v2-external-certs}
{: faq}

Yes, you can bring your own certificates as long as they are issued by a CA that is X.509 compliant.

## Can I upgrade from V1.0 to the new {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-IBP-Overview-1-5}
{: faq}

Not for Starter Plan. You cannot upgrade from Starter Plan to the new  {{site.data.keyword.blockchainfull_notm}} Platform.
For Enterprise Plan, you will be able  to upgrade to the new  {{site.data.keyword.blockchainfull_notm}} Platform  in the future. You will receive an email to the account owner from the {{site.data.keyword.blockchainfull_notm}} Platform team to assist.

## What happens when I delete my {{site.data.keyword.blockchainfull_notm}}  Platform service?
{: #ibp-v2-faq-v2-IBP-Overview-1-8}
{: faq}

When you delete an {{site.data.keyword.blockchainfull_notm}} Platform service instance, all of the blockchain CAs, peers and ordering nodes are deleted along with their associated storage.

## What regions are available for the blockchain service running on the {{site.data.keyword.cloud_notm}}?
{: #ibp-v2-faq-v2-IBP-Overview-1-9}
{: faq}

The available regions for {{site.data.keyword.blockchainfull_notm}} Platform are listed in [{{site.data.keyword.blockchainfull_notm}} Platform locations](/docs/services/blockchain?topic=blockchain-ibp-regions-locations). Note that you must create an {{site.data.keyword.cloud_notm}} Kubernetes Service cluster in the same region as the blockchain service to recognize the cluster. Additional regions will be available soon.

## Can I use my existing {{site.data.keyword.cloud_notm}} Kubernetes Service cluster?
{: #ibp-v2-faq-v2-Infrastructure-4-2}
{: faq}

Your existing Kubernetes cluster will work with the {{site.data.keyword.blockchainfull_notm}} Platform as long as it satisfies the following conditions:
- It is running Kubernetes v1.11 or a higher stable version.
- There are enough available resources in the cluster.

## Do we have access to logging services and what logs are available to me?
{: #ibp-v2-faq-v2-Logging-and-Monitoring-11-6}
{: faq}

With {{site.data.keyword.blockchainfull_notm}} Platform, you can now directly access your peer, CA, and ordering node logs from your Kubernetes dashboard. It is recommend that you take advantage of the {{site.data.keyword.cloud_notm}} LogDNA service that allows you to easily parse the logs in real time.

## What are the benefits of {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud?
{: #ibp-v2-faq-icp-benefits}
{: faq}

See this topic on [What the  {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud offers](/docs/services/blockchain?topic=blockchain-console-icp-about#what-ibm-blockchain-platform-for-ibm-cloud-private-offers).

## Which environments does {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud support?
{: #ibp-v2-faq-icp-environments}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform for Multicloud supports all environments that {{site.data.keyword.cloud_notm}} Private v3.2 supports. See [Supported operating systems and platforms](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.2.0/supported_system_config/supported_os.html){: external} for more information.

## What is the pricing model for {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud?
{: #ibp-v2-faq-icp-pricing}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform for Multicloud [licensing](/docs/services/blockchain?topic=blockchain-ibp-software-pricing) is based on the amount of CPU (VPC) made available to the product. For details, [contact IBM](https://www.ibm.com/account/reg/us-en/signup?formid=urx-37672){: external}.

## What services do I need to install in order to use {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud?
{: #ibp-v2-faq-icp-services}
{: faq}

You only need to install  {{site.data.keyword.cloud_notm}} Private v3.2.

## How can I estimate the {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud sizing requirements for my dev, test, and production environments?
{: #ibp-v2-faq-icp-sizing}
{: faq}

Once you understand how many CA's, peers, and ordering nodes are required, you can examine the [default resource allocations table](/docs/services/blockchain?topic=blockchain-icp-console-setup#icp-console-setup-resources) to get a ballpark estimate of the CPUs (VPCs) required for your network.

## What happens to my blockchain components when I delete my Helm release?
{: #ibp-v2-faq-icp-delete}
{: faq}

When you delete a helm release from your {{site.data.keyword.cloud_notm}} Private cluster, the associated blockchain components are not deleted. To properly remove a helm release from your cluster, you should delete all of your components using the blockchain console or the blockchain APIs first. Then you can delete the helm chart.  

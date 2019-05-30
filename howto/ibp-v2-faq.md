---

copyright:
  years: 2019
lastupdated: "2019-05-31"

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

- [What database do the peers use for their ledger?](#ibp-v2-faq-v2-IBP-Overview-1-3)
- [What languages are supported for smart contracts?](#ibp-v2-faq-v2-IBP-Overview-1-4)
- [Can I upgrade from V1.0 to the new {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-v2-IBP-Overview-1-5)
- [What is the value of using {{site.data.keyword.blockchainfull_notm}} Platform over native Hyperledger Fabric?](#ibp-v2-faq-v2-IBP-Overview-1-7)
- [What happens when I delete my {{site.data.keyword.blockchainfull_notm}} Platform service?](#ibp-v2-faq-v2-IBP-Overview-1-8)
- [What regions are available for the blockchain service running on the {{site.data.keyword.cloud_notm}}?](#ibp-v2-faq-v2-IBP-Overview-1-9)
- [What version of Hyperledger Fabric is being used with {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-v2-Hyperledger-Fabric-3-1)
- [Can I use my existing {{site.data.keyword.cloud_notm}} Kubernetes Service cluster?](#ibp-v2-faq-v2-Infrastructure-4-2)
- [Do we have access to logging services and what logs are available to me?](#ibp-v2-faq-v2-Logging-and-Monitoring-11-6)


## What database do the peers use for their ledger?
{: #ibp-v2-faq-v2-IBP-Overview-1-3}
{: faq}

All peers that are deployed with the {{site.data.keyword.blockchainfull_notm}} Platform use CouchDB as database for the ledger.

## What languages are supported for smart contracts?
{: #ibp-v2-faq-v2-IBP-Overview-1-4}
{: faq}

The {{site.data.keyword.blockchainfull_notm}} Platform supports smart contracts that are written in Go, Node.js. The new Hyperledger Fabric programming model currently supports only Node.js, but more are coming soon. If you are interested in preserving your existing application code, or using Fabric SDKs for languages other than Node.js, you can still connect to your {{site.data.keyword.blockchainfull_notm}} Platform network by using lower-level Fabric SDK APIs.

## Can I upgrade from V1.0 to the new {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-IBP-Overview-1-5}
{: faq}

Not for Starter Plan. You cannot upgrade from Starter Plan to the new  {{site.data.keyword.blockchainfull_notm}} Platform.
For Enterprise Plan, you will be able  to upgrade to the new  {{site.data.keyword.blockchainfull_notm}} Platform  in the future. You will receive an email to the account owner from the {{site.data.keyword.blockchainfull_notm}} Platform team to assist.

## What is the value of using {{site.data.keyword.blockchainfull_notm}} Platform over native Hyperledger Fabric?
{: #ibp-v2-faq-v2-IBP-Overview-1-7}
{: faq}

The {{site.data.keyword.blockchainfull_notm}} Platform enables clients to easily deploy a customized blockchain network. You can use an intuitive console UI to quickly deploy your network, easily install and instantiate smart contracts, and monitor your transactions.

## What happens when I delete my {{site.data.keyword.blockchainfull_notm}}  Platform service?
{: #ibp-v2-faq-v2-IBP-Overview-1-8}
{: faq}

When you delete an {{site.data.keyword.blockchainfull_notm}} Platform service instance, all of the blockchain CAs, peers and ordering nodes are deleted along with their associated storage.

## What regions are available for the blockchain service running on the {{site.data.keyword.cloud_notm}}?
{: #ibp-v2-faq-v2-IBP-Overview-1-9}
{: faq}

The available regions for {{site.data.keyword.blockchainfull_notm}} Platform are listed in [{{site.data.keyword.blockchainfull_notm}} Platform locations](/docs/services/blockchain?topic=blockchain-ibp-regions-locations). Note that you must create an {{site.data.keyword.cloud_notm}} Kubernetes Service cluster in the same region as the blockchain service to recognize the cluster. Additional regions will be available soon.

## What version of Hyperledger Fabric is being used with {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-Hyperledger-Fabric-3-1}
{: faq}

As of Feb 2019, the beta was launched by using Hyperledger Fabric 1.4.0.
As of May 29, 2019, the GA was launched by using Hyperledger Fabric 1.4.1. Hyperledger Fabric 1.4.0 is not supported in the GA product.

## Can I use my existing {{site.data.keyword.cloud_notm}} Kubernetes Service cluster?
{: #ibp-v2-faq-v2-Infrastructure-4-2}
{: faq}

Your existing Kubernetes cluster will work with the {{site.data.keyword.blockchainfull_notm}} Platform as long as it satisfies the following conditions:
- It is running Kubernetes v1.11 or a higher stable version.
- There are enough available resources in the cluster.

## Do we have access to logging services and what logs are available to me?
{: #ibp-v2-faq-v2-Logging-and-Monitoring-11-6}
{: faq}

With {{site.data.keyword.blockchainfull_notm}} Platform, you can now directly access your peer, CA, and orderer logs from your Kubernetes dashboard. It is recommend that you take advantage of the {{site.data.keyword.cloud_notm}} LogDNA service that allows you to easily parse the logs in real time.

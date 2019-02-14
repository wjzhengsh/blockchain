---

copyright:
  years: 2019
lastupdated: "2019-02-14"

---


{:new_window: target="_blank"}
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

## What version of Hyperledger Fabric is being used with {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?
{: #ibp-v2-faq-fabric-version}
{: faq}

The {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta uses Hyperledger Fabric v1.4 and includes support for gossip, service discovery and private data.

## Do I need to pay anything for the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?
{: #ibp-v2-faq-cost}
{: faq}

The beta release is free. But you may still incur charges for your {{site.data.keyword.cloud_notm}} Kubernetes cluster, unless you selected a free cluster.  However, be aware that the free cluster is automatically deleted after 30 days per {{site.data.keyword.cloud_notm}} Kubernetes service policy and performance and capacity are limited.  If you want to persist your service beyond 30 days, you need to pay for a standard instance of the Kubernetes service. 

## Can I use my existing {{site.data.keyword.cloud_notm}} Kubernetes Service cluster?
{: #ibp-v2-faq-existing-cluster}
{: faq}

Your existing Kubernetes cluster will work with the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta as long as it satisfies the following conditions:
- It is in the {{site.data.keyword.cloud_notm}} Dallas region.
- It is running Kubernetes v1.11 or higher. See these instructions for information on how to [upgrade the Kubernetes version of your cluster](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).
- There are enough available resources in the cluster. See this topic on [minimum resource requirements](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required) for more information.

## What database do the Peers use for their ledger?
{: #ibp-v2-faq-couchDB}
{: faq}

All peers are deployed with the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta use CouchDB database. It is not possible to query the CouchDB directly though.

## What languages are supported for smart contracts?
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform supports smart contracts written in Go, Node.js, and Java. The new Hyperledger Fabric programming model currently only supports Node.js, more are coming soon. If you are interested in preserving your existing application code, or using Fabric SDKs for languages other than Node.js, you can still connect to your {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta network using lower level Fabric SDK API's.

## Can I migrate from the V2.0 beta to v2.0 GA? 
{: #ibp-v2-faq-migrate}
{: faq}

Enterprise Plan customers will be able to migrate to IBM Blockchain Platform 2.0 when it becomes generally available (GA). But you will not be able to migrate your 2.0 beta offering to the 2.0 GA offering. Also, you cannot migrate a Starter plan instance to either the 2.0 beta or to the 2.0 GA product, when it becomes available.

## Do we have access to logging services and what logs are available to me?
{: #ibp-v2-faq-logs}
{: faq}

With {{site.data.keyword.blockchainfull_notm}} Platform 2.0, you can now directly access your peer, CA and orderer logs from your Kubernetes dashboard. We recommend you take advantage of the [{{site.data.keyword.cloud_notm}} LogDNA ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis with LogDNA") service which allows you to easily parse the logs in real-time.

## What is the value of using {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta over native Hyperledger Fabric?
{: #ibp-v2-faq-native-fabric}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 Beta enables clients to easily deploy a custom blockchain network. The platform includes an intuitive console for quick deployment of a network and the ability to easily install and instantiate smart contracts. With the platform you also receive support from experts at {{site.data.keyword.IBM_notm}} to help you with developing your smart contract and building your network.

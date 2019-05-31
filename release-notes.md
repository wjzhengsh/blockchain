---

copyright:
  years: 2019
lastupdated: "2019-05-31"

keywords: release note, latest changes, Hyperledger Fabric

subcollection: blockchain

---

{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}
{:external: target="_blank" .external}

# Release notes
{: #release-notes-saas-20}

Use these release notes that are grouped by date to learn about the latest changes to {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} which is built on Hyperledger Fabric v1.4.1.
{:shortdesc}


## 9 May 2019
{: #05-09-2019}

**Introducing {{site.data.keyword.blockchainfull_notm}} Platform Console APIs**

APIs are now available to provision, edit, and delete peer, orderer, and CA nodes, making it possible to script the building of your blockchain network. Use the documentation in the [{{site.data.keyword.cloud_notm}} API documentation repository](/apidocs/blockchain#introduction){: external} to learn more about the APIs and try them out. In addition, see the topic on [Building a network with APIs](/docs/services/blockchain?topic=blockchain-ibp-v2-apis) for instructions on how to use the APIs to build out your network.  

**Channel governance**  

Channel governance updates allow policies to be reconfigured. You can also control which channel members need to sign a channel update and you can orchestrate signature collection.

## 17 April 2019
{: #04-17-2019}

**Ability to size and scale node resources**  

When you deploy a node you now have the ability to specify the amount of CPU, memory, and storage to your containers, where applicable. You can later scale their resources up or down at a later time according to usage patterns. For more information, see [Allocating resources](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources).

**Use of {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM)**  

IAM is used to control user access to the console UI as well as restricting the actions they can perform in the UI.  See this topic for information on how to [add and remove users from the console](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).

## 3 April 2019
{: #04-03-2019}

**Support for external CA**

When you add a peer or orderer node, you have the option to use certificates from an external CA, one that is not hosted by {{site.data.keyword.IBM_notm}}. See this topic on [Using certificates from an external CA with your peer or orderer](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca) for more information.

**Tuning orderer performance**

New orderer tuning parameters are available in the console to give you more control over your orderer throughput and performance. See this topic on [Tuning your orderer](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning) for instructions on how to configure the parameters.

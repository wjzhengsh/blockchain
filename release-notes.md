---

copyright:
  years: 2019
lastupdated: "2019-04-17"

---

{:new_window: target="_blank"}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}

# Release notes
{: #release-notes-saas-20}

Use these release notes that are grouped by date to learn about the latest changes to {{site.data.keyword.blockchainfull}} Platform free 2.0 beta
{:shortdesc}


## 17 April 2019

**Ability to size and scale node resources**  

When you deploy a node you now have the ability to specify the amount of CPU, memory, and storage to your containers, where applicable. You can later scale their resources up or down at a later time according to usage patterns. For more information, see [Allocating resources](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-allocate-resources).

**Use of IAM  IBM Cloud Identity and Access Management (IAM)**  

IAM is used to control user access to the console UI as well as restricting the actions they can perform in the UI.  See this topic for information on how to [add and remove users from the console](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).

## 3 April 2019
{: #04-03-2019}

**Support for external CA**

When you add a peer or orderer node, you have the option to use certificates from an external CA, one that is not hosted by {{site.data.keyword.IBM_notm}}. See this topic on [Using certificates from an external CA with your peer or orderer](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca) for more information.

**Tuning orderer performance**

New orderer tuning parameters are available in the console to give you more control over your orderer throughput and performance. See this topic on [Tuning your orderer](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning) for instructions on how to configure the parameters.

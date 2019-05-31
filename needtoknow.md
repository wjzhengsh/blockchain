---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

keywords: IBM Blockchain offerings, Linux Foundation, Hyperledger Fabric, open source, community contribution

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Disclaimer
{: #disclaimer}

**ATTENTION:** You must review the following information before you use any {{site.data.keyword.blockchainfull}} plans.

## IBM support statement
{: #disclaimer-support-statement}

{{site.data.keyword.IBM}}'s long history of leadership in innovation continues with the {{site.data.keyword.blockchainfull_notm}} Platform offerings on {{site.data.keyword.cloud_notm}}. Blockchain is a rapidly progressing technology that is projected to disrupt the financial industry, local and global supply chains, and logistical support in any number of business spaces. Through various early adoption programs, {{site.data.keyword.IBM_notm}} customers and business partners have been actively driving blockchain as an industrial solution. {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} is one such program. **As with any new technology, {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} users should be aware of the potential for rapid and fundamental change**.
{:shortdesc}

The architecture behind {{site.data.keyword.blockchainfull_notm}} is the Linux Foundation's Hyperledger Fabric project. Each open-source community contribution improves Hyperledger Fabric but can present adoption challenges. **{{site.data.keyword.IBM_notm}} cautions against defining or exchanging financial assets<!--, or any assets of value,--> directly on any Hyperledger Fabric blockchain network**.

{{site.data.keyword.IBM_notm}} does not provide support for networks using Hyperledger Composer in production, including the Composer CLI, JavaScript APIs, REST server, and Web Playground.
{:note}

## Open-source statement
{: #disclaimer-open-source-statement}

{{site.data.keyword.blockchainfull_notm}} offering plans on {{site.data.keyword.cloud_notm}} are built on top of the Linux Foundation's Hyperledger Fabric stack. Hyperledger Project members, including {{site.data.keyword.IBM_notm}}, continue to contribute to various subprojects under the Hyperledger umbrella.  All contributions are reviewed, vetted, and tested by the community.

## Chaincode support statement
{: #disclaimer-chaincode-support-statement}

The following coding practices are NOT supported on {{site.data.keyword.blockchainfull_notm}} networks:

1. Using associative arrays with iteration (the order is randomized in Go).
2. Reading a list of items from a KVS table (the order is not guaranteed).
3. Writing thread-unsafe chaincode (query and invoke might be called in parallel).
4. Substituting global memory or cache storage for ledger state variables in chaincode.
5. Accessing external services, such as databases, directly from chaincode.
6. Using libraries or global variables that could introduce non-determinism (such as using "random" or "time").

In addition, it is not recommended to write non-deterministic chaincode, which introduces risk to data consistency and integrity. Note that the Hyperledger Fabric architecture is designed to counter against non-deterministic chaincode through a series of endorsement and validation checks. However, you are still strongly encouraged to code deterministic functions that are not reliant on non-static global variables, for example, time.

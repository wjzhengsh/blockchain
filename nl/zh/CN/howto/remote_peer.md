---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 关于远程同级
{: #remote-peer-overview}


***[此页面是否有用？请告诉我们。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


在连接到现有区块链网络后，可以在 {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> 上运行 {{site.data.keyword.blockchainfull}} Platform 远程同级。在 {{site.data.keyword.cloud_notm}} 之外运行同级不但让扩展或加入区块链网络变得更加灵活，而且可利用 {{site.data.keyword.cloud_notm}} 中的现有网络。远程同级利用平台上的认证中心 (CA) 和排序服务，但是允许将同级与 {{site.data.keyword.cloud_notm}} 之外的其他应用程序放置在一起。
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} Private (ICP) 是基于 Kubernetes 的平台，用于在内部部署环境中构建私有云。您可以使用 ICP 以运行远程同级并将远程同级连接到 {{site.data.keyword.blockchainfull_notm}} Platform 上的区块链网络。{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP 利用 ICP 的存储器、安全性、日志记录和支持服务，因此您可以在自己的内部部署环境中管理远程同级。有关 ICP 的更多信息，请参阅 [{{site.data.keyword.cloud_notm}} Private 文档 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 文档")。  

**注：** {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer 当前是 Beta 产品，适合评估和试用。**此 Beta 修订版并非针对于生产用途。**有关访问和更多信息，请参阅[许可证和定价](#remote-peer-license-pricing)。

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

Beta 版本支持 {{site.data.keyword.cloud_notm}} Private (ICP) v2.1.0.3 云平台。请注意，{{site.data.keyword.blockchainfull_notm}} Platform 上的网络和 ICP 中的远程同级节点必须在相同的 **Fabric V1.1** 上运行。

## 注意事项
{: #remote-peer-limitations}

远程同级无权访问完整功能或者支持在 {{site.data.keyword.blockchainfull_notm}} Platform 上托管的同级。在运行远程同级之前，请确保了解以下限制：
- 在其他云环境中运行的远程同级在 {{site.data.keyword.cloud_notm}} 上区块链网络的“网络监视器”中不可见。
- 无法使用“网络监视器”UI 中的 Swagger UI 寻址远程同级。
- 您负责管理远程同级的运行状况监视、安全性、日志记录和资源使用情况。
- 只能将远程同级连接到 Hyperledger Fabric v1.1 级别的区块链网络。
- 远程同级的数据库类型必须匹配区块链网络的数据库类型，LevelDB 或 CouchDB。
- CouchDB Fauxton 接口在远程同级上不可用。
- 当前不支持将 [Gossip](../glossary.html#gossip) 用于远程同级。


## 先决条件
{: #remote-peer-prereq}

要使用远程同级，必须具有属于 {{site.data.keyword.blockchainfull_notm}} Platform 上的入门套餐或企业套餐网络的组织。远程同级利用 {{site.data.keyword.blockchainfull_notm}} Platform 套餐网络的 API 端点、Hyperledger Fabric CA 和排序服务来运行。如果您不是任何区块链网络的成员，那么需要创建或加入一个网络。有关更多信息，请参阅[创建网络](../get_start.html#creating-a-network)或[加入网络](../get_start.html#joining-a-network)。


## 许可证和定价
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->要访问在 ICP 上运行的远程同级，请参阅 [ICP 上远程同级的许可证和定价](remote_peer_icp.html#license-pricing-icp "ICP 上远程同级的许可证和定价")。远程同级的 Beta 版本的许可证免费。<!--[AWS] for both platforms.-->之后，普遍可用 (GA) 产品将替换 Beta 版本。

不支持从 Beta 迁移到 GA。您需要在发布时下载并安装新的远程同级以使用 GA 产品。然后，您可以将新的远程同级加入到与 Beta 远程同级相同网络的相同通道。

**注：**要运行远程同级，必须具有属于 {{site.data.keyword.blockchainfull_notm}} Platform 上入门套餐或企业套餐网络的组织。这意味着您或网络中的其他成员必须为组织支付 IBM 区块链[成员资格费用](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing)。有关支付费用的更多信息，请参阅[付费方式](paying_mode.html)。  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## 部署远程同级
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

下图描述在 ICP 上部署 {{site.data.keyword.blockchainfull_notm}} Platform 远程同级的步骤。有关如何在 ICP 上部署远程同级的更多信息，请参阅[在 {{site.data.keyword.cloud_notm}} Private 中部署远程同级](remote_peer_icp.html "在 {{site.data.keyword.cloud_notm}} Private 中部署远程同级")。

![ICP 上的远程同级部署流程](../images/remote_peer_ICP_flow.png "ICP 上的远程同级部署流程")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*图 1. ICP 上的远程同级部署流程*


## 运行远程同级
{: #operate-remote-peer}

在部署远程同级后，您需要完成多个操作步骤，然后同级才能向网络提交事务。操作步骤包括将组织添加到通道、将远程同级加入到通道、在远程同级上安装链代码、实例化通道上的链代码以及将应用程序连接到远程同级。有关更多信息，请参阅<!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[在 {{site.data.keyword.cloud_notm}} Private 中运行远程同级](remote_peer_operate_icp.html#remote-peer-operate)。

## 数据存储位置
{: #data-residency}

数据存储位置需求是管理可存储数据位置的限制。数据存储位置的最常见需求与特定国家或地区的法律相关，这强制在 IT 系统中处理和存储的所有客户数据必须保留在特定国家或地区的边界内。类似，高监管行业（例如，政府、医疗保健和金融服务）中的某些公司要求必须将所有数据完全存储在防火墙之后。此外，某些公司具有公司策略，特定类型的数据（通常是个人可标识信息）必须保持私有并且完全位于其公司防火墙之后。因此，为实现数据存储位置，区块链网络的所有组件都必须属于相同[通道](../glossary.html#channel)并且位于单个国家或地区的边界内。

为满足数据存储位置需求，重要的是了解作为 {{site.data.keyword.blockchainfull_notm}} Platform 基础的 Hyperledger Fabric 体系结构。体系结构主要围绕着三个关键组件：排序服务、认证中心 (CA) 和同级。同级以区块形式接收来自排序服务的已排序状态更新并维护状态和分类帐。因此，同级和排序服务具有直接关系。分类帐包含所有事务日志所包含的所有密钥和数据的最新值。

此外，客户机应用程序使用 [Fabric SDK](../v10_application.html#using-the-fabric-sdks) 以将事务发送给同级和排序服务。这些事务包含[读/写集语义 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "读/写集语义") 数据，其中包含有关分类帐的密钥/值对。

如果国内数据存储位置是您的业务需求，那么排序服务、同级节点和客户机应用程序应位于同一国家或地区中。在 {{site.data.keyword.cloud_notm}} 中创建 {{site.data.keyword.blockchainfull_notm}} Platform 网络时，您可以选择网络的位置。<!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->有关区域和位置的更多信息，请参阅 [{{site.data.keyword.blockchainfull_notm}} Platform 区域和位置](../reference/ibp_regions.html)。要在其中一个国家或地区中实现数据存储位置，远程同级应位于与 {{site.data.keyword.blockchainfull_notm}} Platform 网络位置相同的国家或地区中。

如果 {{site.data.keyword.blockchainfull_notm}} Platform 网络包含跨国界的远程同级和排序服务节点，那么可使用通道将数据划分到网络上的一小组同级中。排序节点总是位于选择托管网络的数据中心中。排序者不能跨国界。但是，同级可位于数据中心或 {{site.data.keyword.cloud_notm}} 之外的远程位置。因此，要实现数据存储位置，可创建排序者和所有同级（同级位于数据中心本地或者是远程同级）位于同一个国家或地区的通道。通过此方式，排序者和同级共享的所有数据仍位于单个国家或地区的边界内。如果需要，仍可存在其他通道，其中排序者和远程同级跨国界并且数据存储位置不是必需的。

将来，Hyperledger Fabric 中的新技术将使用[专用数据集合 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "专用数据集合") 和 Zero Knowledge Proof 来提高进一步实现数据存储位置的能力。

- 专用数据集合确保专用数据进行对等共享（通过 gossip 协议），只有授权的对等方（比如，国界内的同级）才能看到。数据存储在同级的专用数据库中。此处不涉及排序服务，并且排序服务也不会看到专用数据。会将散列化的此数据写入到通道上每个同级的分类帐。用于状态验证的散列充当事务证据，并且可用于审计目的。

- Zero-Knowledge Proof (ZKP) 允许“证明者”向“验证者”保证他们在不泄露自身秘密的情况下了解保密信息。这是显示您知道某些项满足条件而不显示您所知道的内容的方法。

有关这些技术的更多信息，请参阅有关[使用 Hyperledger Fabric 的专用和保密事务 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "使用 Hyperledger Fabric 的专用和保密事务") 的白皮书。

## 获取支持
{: #remote-peer-support}

{{site.data.keyword.blockchainfull_notm}} Remote Peer Beta 版本产品用于探索、开发和测试。**请勿将此版本用于生产。**{{site.data.keyword.blockchainfull_notm}} Platform 不提供此版本的支持。如果遇到与远程同级相关的任何问题，请访问[区块链资源和支持论坛](../v10_dashboard.html#support-forums)。您还可以在“网络监视器”的**获取帮助**屏幕上查看支持资源。  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

有关与 {{site.data.keyword.cloud_notm}} Private 相关的问题，ICP 提供 [免费数字支持和社区版本支持 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "ICP 免费数字支持和社区版本支持")。

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

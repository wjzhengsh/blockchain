---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 区块链组件概述

{{site.data.keyword.blockchainfull}} Platform (IBP) 的组件和结构基于
[Hyperledger Fabric ![外部链接图标](images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/)（这是一个开放式源代码许可的区块链解决方案，{{site.data.keyword.IBM_notm}} 是其主要贡献者）的底层基础架构和工具。基于 Fabric 的网络包括几个标准组件，这些组件可部署到多种配置中，以支持种类繁多的用例。

要更全面地了解 Fabric 网络以及组成该网络的组件的相互关系，请从 Fabric 社区文档查看[有关区块链网络的结构的此文档](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html)，其中显示了如何创建和健全网络。

要获取基于 Fabric 的网络中组件的高级概述，请查看以下视频：

<iframe class="embed-responsive-item" id="youtubeplayer" title="入门套餐视频" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*尽管此视频主要从入门套餐和企业套餐网络的角度介绍组件，但此信息对于 IBP on ICP (IBM Cloud Platform) 的客户管理解决方案仍然高度相关。*

在本概述中，我们将只重点关注认证中心 (CA)、排序节点、同级、智能合同以及应用程序。如 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 部署指南](ibp_for_icp_deployment_guide.html)主题所示，此顺序并非任意的，它反映了基于 Fabric 的网络中组件的部署顺序。

## 认证中心
{: #ca}

身份和许可权是基于 Fabric 的区块链网络的基石。身份采用 CA 颁发的 x.509 证书的形式，它们与信用卡的相似之处在于可*识别*某个人，可包括其相关属性。然后，这些证书将通过包含在组件或通道级别的 MSP 文件夹中，从而链接到许可权。例如，同级 MSP 将拥有名为 **admins** 的 MSP 子文件夹。其证书位于该 admin 文件夹中的任何用户都是同级的管理员，这意味着他们能够执行允许该同级的管理员执行的任何操作。每当通过其签名证书识别的用户尝试执行管理操作时，同级内的验证系统都将执行检查。该证书与“admin”文件夹中的证书匹配吗？如果匹配，那么可以执行该操作。否则，执行该操作的请求将被拒绝。

{{site.data.keyword.blockchainfull_notm}} CA 基于 [Hyperledger Fabric-CA](https://hyperledger-fabric-ca.readthedocs.io/en/latest/)，但是只要它使用基于 x.509 证书的 PKI，就可以使用另一个 CA。可以有且通常应该有多个级别的 CA。通常不会公开网络的“根 CA”，除非是将证书提供给“中间 CA”，后者会将证书直接颁发给用户和组件，或者颁发给更多层中间 CA。有关如何使用认证中心建立身份和成员资格的更多详细信息，请参阅有关[身份 ![外部链接图标](images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) 和[成员资格 ![外部链接图标](images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html) 的 Hyperledger Fabric 文档。

## 排序节点
{: #orderer}

虽然排序服务通常称为网络的“核心”，但其功能实际上相当简单：将事务排序到区块中，然后将这些事务发送回同级以写入其分类帐。在先前版本的 Fabric 中，此功能捆绑在同级内，但是从 Fabric V1.0 开始，它分离为单独的组件，
以提高同级性能并增加可能导致潜在状态派生的异常。

在物理级别，此排序功能通常需要一组统称为“排序服务”的排序节点，但在测试或 POC 环境中，可以使用单个节点（称为 SOLO 排序节点）。

## 同级
{: #peer}

在物理级别，区块链网络主要由同级节点（或者简称为同级）构成。同级是网络的基本元素，因为它们托管分类帐和智能合同（包含在“链代码”中）。更准确地说，同级托管分类帐的**实例**以及智能合同的**实例**。由于智能合同和分类帐分别用于封装网络中的共享过程和共享信息，因此同级的这些方面使其成为了解 Fabric 实际所执行操作的合适起点。

要了解有关同级的更多具体信息，请查看 Fabric 社区文档中[仅关注同级的此文档](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html)。

## 智能合同

要使企业能够相互交易，必须先在某种形式的合同中达成并定义有关规则和流程的共识。这些合同一起制定了“业务模型”，用于管理业务合作伙伴之间的所有交互。

区块链网络中存在相同的需求，其中，这些业务模型的行业术语是“智能合同”，Fabric（和 IBP）将这些合同包含在称为“链代码”的更大结构中，该结构不仅包含业务逻辑，还包括对尝试调用智能合同的用户验证身份的底层基础架构。

商业领域的合同由律师事务所进行签名和存档，而智能合同则安装在同级上，并在通道上“实例化”。

## 应用程序

基于 Fabric 的网络（如 IBP）中的客户机应用程序利用 API、SDK 之类的底层基础架构和智能合同，允许较高抽象级别的客户机交互（调用和查询）。

要了解应用程序如何与基于 Fabric 的网络进行交互，请查看[有关编写第一个应用程序的文档 ![外部链接图标](images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "编写第一个应用程序") 以及[开发应用程序部分 ![外部链接图标](images/external_link.svg "外部链接图标")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "开发应用程序")。


请注意，后一个链接引用的是与 Fabric V1.2 不兼容的 Fabric 功能（如 `Contract` 类），而入门套餐和 IBP on ICPP 发行版正是基于 Fabric V1.2。不过，本部分确实包含概念性信息和其他有用的提示，在开发将与 Fabric V1.2 组件交互的应用程序时记住这些信息非常有用。

---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 免责声明
{: #disclainer}

**注意：**在使用任何 {{site.data.keyword.blockchainfull}} 套餐之前，必须先查看以下信息。

## IBM 支持声明

IBM 一直以来都在创新方面颇具领导能力，在 {{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull_notm}} 产品套餐中亦是如此。{{site.data.keyword.blockchain}} 是一项快速发展的技术，预计将颠覆金融行业、本地和全球供应链，以及任意数量的业务空间的物流支持。IBM 客户和业务合作伙伴通过各种早期采用程序，一直积极推动区块链作为业界的解决方案。{{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull_notm}} 就是这样一种程序。**就像任何新技术一样，{{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull_notm}} 用户应该意识到可能发生的快速而基本的更改**。  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} 背后的体系结构是 Linux Foundation 的 Hyperledger 项目。每一个开放式源代码社区贡献都会改进 Hyperledger Fabric，但是会带来采用挑战。**IBM 提醒不要直接在任何 Hyperledger Fabric 区块链网络上定义或交换金融资产或任何有价值的资产**。  

## 开放式源代码声明

{{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull_notm}} 产品套餐以 Linux Foundation 的 Hyperledger Fabric 堆栈为基础构建。Hyperledger 项目成员（包括 IBM）将持续向 Hyperledger 伞下的各种子项目添加内容。所有添加项都由社区复查、审查和测试。

## 链代码支持声明

{{site.data.keyword.blockchainfull_notm}} 网络不支持以下编码实践：

1. 使用具有迭代的相关联数组（顺序在 Go 中为随机的）。
2. 从 KVS 表中读取项目列表（不保证顺序）。
3. 编写线程不安全链代码（可能会并行调用查询和调用）。
4. 在链代码中将全局内存或高速缓存存储器替换为分类帐状态变量。
5. 从链代码中直接访问外部服务，如数据库。
6. 使用可能引入非确定性的库或全局变量（如使用“random”或“time”）。  

此外，建议不要编写不确定的链代码，这会导致发生数据一致性和完整性风险。值得注意的是，Hyperledger Fabric 体系结构旨在通过一系列支持和验证检查来反对不确定的链代码。然而，对于不依赖于非静态全局变量（例如时间）的确定性函数，仍然强烈建议您进行编码。  

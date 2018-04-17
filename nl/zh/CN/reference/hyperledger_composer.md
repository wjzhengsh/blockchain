---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}

{{site.data.keyword.blockchainfull}} Platform: Develop 以开放式源代码 Hyperledger Composer 工具集为基础构建，后者是 Linux Foundation Hyperledger 项目中的项目之一。通过 Hyperledger Composer，架构设计师和开发者可以利用 REST API 来快速创建 {{site.data.keyword.blockchain}} 解决方案，用于将业务逻辑公开给 Web 或移动应用程序，以及将区块链与现有的企业记录系统相集成。
{:shortdesc}

Hyperledger Composer 由运行时、命令行界面、REST 服务器、LoopBack 连接器、Playground 用户界面、Yeoman 代码生成器以及 VSCode 和 Atom 编辑器插件组成。有关 Hyperledger Composer 的更多信息，请参阅 [Hyperledger Composer 文档 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)。


## 业务网络

通过 Hyperledger Composer，可以快速对当前的业务网络建模，其中包含现有资产以及与其相关的事务；资产是有形或无形的商品、服务或财产。作为业务网络模型的一部分，您可以定义可与资产进行交互的事务。业务网络还包括与其交互的参与者，在多个业务网络中，每个参与者与唯一身份相关联。

有关构建业务网络的更多信息，请参阅[开发业务网络](./develop.html)或 [Hyperledger Composer 文档 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)。

## 查询

查询用于返回有关区块链全局状态的数据。查询在业务网络中进行定义，并且可以包含用于简单定制的变量参数。通过使用查询，可以轻松地从 {{site.data.keyword.blockchain}} 网络中抽取数据。查询将使用 [Hyperledger Composer API ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/latest/api/api-doc-index) 进行发送。

有关在业务网络中使用查询的更多信息，请参阅[查询和过滤业务网络数据 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/business-network/query)。

## 身份和业务网络卡

在业务网络中，参与者与身份相关联。业务网络卡是身份、连接概要文件和元数据的组合；用于连接到业务网络并展示身份进行事务处理。业务网络卡简化了连接到业务网络的过程，并将身份概念扩展到业务网络之外，使其成为身份“钱包”，每个身份都与特定的业务网络和连接概要文件相关联。

有关身份和业务网络卡的更多信息，请参阅[参与者和身份 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/managing/participantsandidentities)。

## Hyperledger Composer REST 服务器

Hyperledger Composer REST 服务器会自动为业务网络生成 Swagger REST API。REST 服务器基于 LoopBack，可将业务网络模型转换为 Open API 定义。在运行时，REST 服务器会实现对资产和参与者的创建、读取、更新和删除支持，并允许提交事务进行处理或通过查询进行检索。

有关 Hyperledger Composer REST 服务器的更多信息，请参阅[生成 REST API ![外部链接图标](../images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/integrating/getting-started-rest-api)。

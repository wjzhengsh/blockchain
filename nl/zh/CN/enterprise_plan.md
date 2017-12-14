---

copyright:
  years: 2017
lastupdated: "2017-09-20"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform 企业套餐

{{site.data.keyword.blockchainfull}} Platform 企业套餐是希望找到或加入区块链业务网络的组织的第一个可用成员资格选项。此套餐提供关键基础架构以及工具和支持，以便轻松部署高度安全的生产就绪型区块链网络。

对于即将启动网络的成员，IBM 提供图形用户界面来指导网络启动器完成设置和供应网络的关键步骤。这包括邀请其他成员和设置管控规则。一旦部署了网络，就可以使用交互式图形用户界面来监视网络的运行状况和活动；管理关键网络活动，包括新部署、成员添加/除去、链代码生命周期和通道管理；寻求技术支持。查找有关如何获取[支持](ibmblockchain_support.html)的更多信息。

{{site.data.keyword.blockchainfull_notm}} Platform 以关键 Hyperledger Fabric 组件为基础构建，其中包括认证中心 (CA) 和至少 1 个同级（最多 6 个）。IBM 还为网络成员提供崩溃容错 (CFT) Kafka 订购服务。 

Fabric CA 是企业套餐随附的认证中心。每个成员提供了两个中间 CA，用于将成员资格授予网络。通过使用 CA，成员还可以为网络的最终用户提供成员资格（证书）。

重要的是，对于要附加到分类帐的事务处理而言，涉及以下三个阶段：  
1. 事务处理模拟和支持（同级）
2. 订购（订购服务）
3. 验证和提交（同级）

成员所拥有的 Fabric 同级是应用程序的接口或网关，用于执行链代码，从而提供用于对分类帐执行事务处理的业务逻辑。必须支持所有事务处理。网络的其他成员会执行此支持。支持后，事务处理将发送到 IBM 提供的订购服务 (Kafka)。

除了核心区块链组件外，“企业成员资格”选项还提供了具有安全数据存储和通信 (TLS) 以及高可用性的基础架构。虽然 Fabric 网络共享这些基础架构资源，但是为网络中的 Fabric 组件节点提供了隔离，并且每个节点都在保护执行环境的安全 Docker 容器中执行。

必须确定的唯一方面是网络所需的同级的大小。此决策基于所需的通道数，以及每个通道的工作负载、内存使用情况和磁盘空间（存储器）。 

IBM 区块链应当用在更稳定的生产级别或接近生产级别的部署中。如果要用于测试，请使用 IBM Container 服务或本地可下载映像。

Enterprise 套餐提供订购服务和 CA。成员资格费用为 $1,000，与该网络关联的每个对等成员资格费用也是 $1,000。如果要实现高可用性 (HA)，必须额外采购一个提供 HA 功能的对等成员资格。例如，一个组织（关联的成员资格费用为 $1,000）有两个对等成员资格（$1,000 X 2 个对等成员资格），还带有 HA 功能（$1,000 X 2 个 HA 对等成员资格），需要的费用是一个月 $5,000。

立即注册以获取 {{site.data.keyword.IBM_notm}} [{{site.data.keyword.blockchainfull_notm}} 成员资格 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)。

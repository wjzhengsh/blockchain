---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 关于入门套餐
{: #overview}

{{site.data.keyword.blockchainfull}} Platform 入门套餐是一个入门级选项，支持组织模拟多组织区块链网络，快速开发应用程序以及使用提供的示例。它还拥有与其他成员资格选项相同的 UI 体验，有助于消除任何学习曲线。
入门套餐网络是基于 Hyperledger Fabric V1.1 构建的。{:shortdesc}

**注**：{{site.data.keyword.blockchainfull_notm}} Platform 入门套餐是一个开发与测试环境，不适合生产工作负载。如果需要生产环境，请参阅[关于企业套餐](enterprise_plan.html)。在使用入门套餐之前，请检查[入门套餐注意事项](#considerations)。

立即注册 [{{site.data.keyword.blockchainfull_notm}} 成员资格 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) 并试用入门套餐！请注意，使用入门套餐 Beta 时，必须在 {{site.data.keyword.cloud_notm}} 中选择**美国南部**作为创建区块链网络的区域。


## 目标受众

如果您符合下列其中一种情况，那么入门套餐适合您开始区块链之旅。
- **_了解 {{site.data.keyword.blockchainfull_notm}} Platform。_**  
    如果您对 {{site.data.keyword.blockchainfull_notm}} Platform 感兴趣，甚或完全不熟悉区块链，那么通过入门套餐，可以很好地了解如何开发和管理真正的区块链网络。您可以了解组成网络的组件，了解如何部署和管理链代码（也称为“智能合同”），如何添加通道（并管理通道许可权）以及跟踪何时生成新区块，就像在生产网络中一样。
- **_希望充当多个组织来促进网络开发。_**  
    通过入门套餐，您可充当多个组织，来了解 IBM Blockchain Platform (IBP) 如何管理协作任务（如通道创建和链代码实例化），以及测试应用程序和调用事务处理。您还可以邀请其他人在入门套餐网络中进行协作（与在企业套餐中一样）。
- **_在实时网络中构建演示解决方案。_**  
    入门套餐为测试网络定义（通过集成使用 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 开发的 `.bna` 文件）和区块链应用程序提供了功能强大的环境。随时可用的区块链网络支持通过“网络监视器”提供的操作和管理工具来快速向同事、管理层和合作伙伴进行演示。
- **_以迭代方式开发和测试区块链应用程序。_**  
    入门套餐为您提供了一个编译打包区域，可用于在区块链网络上持续开发和测试代码。您可以通过迭代方式开发代码，并将其部署到持续集成和持续部署体系结构中。入门套餐提供了与企业套餐相同的区块链网络功能以及操作和管理工具。在准备好将项目推送到其中一个企业套餐后，您可以按照入门套餐中的相同方式进行操作，但有更多机会使网络增长。
- **_生产前测试项目。_**  
    入门套餐为开发者和测试者提供了一个环境，可以快速从其本地环境迁移到真正的云区块链环境。通过这种机制，开发者和测试者能专注于功能，并轻松地从单元测试移至功能测试。此外，系统、解决方案和性能测试团队也可以使用此环境，而无需执行额外工作在本地建立网络。
- **_用于培训的操作 {{site.data.keyword.blockchainfull_notm}} Platform。_**  
    {{site.data.keyword.blockchainfull_notm}} Platform 在入门套餐和企业套餐中提供了几乎相同的用户界面<!--the same user interface-->，可确保定制的培训使用您的组织将在企业套餐中使用的相同工作流程。
- **_使用 Toolchain 快速部署样本应用程序。_**  
    通过入门套餐，只需单击几下即可使用 Toolchain 来部署样本应用程序。通过提供一组借助代码来修改和向前发展的不断增长的样本，这些样本将帮助开发者进行开发。


## 入门套餐规范

入门套餐提供了管理认证中心 (CA) 的成员资格、执行事务处理支持、提供排序服务、构建专用通道、管理链代码生命周期以及与实时网络中的其他人进行协作的能力，就像在企业套餐中一样。

具体而言，入门套餐提供了一个预配置的区块链网络，您可以通过“网络监视器”进行开发、管理和操作。它还提供了部署样本应用程序的简单方法，并集成了使用 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 开发的应用程序。

- **_一次单击就绪型网络_**  
    入门套餐通过一次单击，就能为您供应具有排序服务、CA、缺省通道和两个组织（每个组织一个同级）的网络。{{site.data.keyword.blockchainfull_notm}} Platform 会处理此网络的创建和配置（在网络上线后可对其进行更新）。<!--The free trial provides you up to two organizations and two peers.-->
- **_成本效益_**  
    入门套餐成员资格选项提供了许多与企业套餐成员资格选项相同的区块链功能，但成本更低。<!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->在 Beta 阶段，可以免费使用入门套餐。
- **_多组织网络模拟_**  
    可以使用入门套餐来模拟使用多个组织构建网络。您不需要实际邀请其他组织加入网络，而是可以自己充当其他组织。通过这种机制，您可了解新组织能如何加入网络，多个组织如何在网络中一起工作，等等。可以通过“网络监视器”切换组织，以从不同组织的角度查看和管理网络。
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_Swagger API_**  
    入门套餐公开了多个可以通过 Swagger 接口试用的 REST API。有关更多信息，请参阅[使用 Swagger API](swagger_apis.html)。
- **_样本应用程序_**  
    入门套餐利用 {{site.data.keyword.cloud_notm}} 中的 Toolchain 服务，并支持通过简单几次单击来进行样本部署。部署并启动样本后，链代码和应用程序会自动针对区块链网络运行。有关样本应用程序更多信息，请参阅[部署样本应用程序](howto/prebuilt_samples.html)。
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop 应用程序集成_**  
    通过入门套餐，您可以将 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 应用程序部署到网络。有关更多信息，请参阅[导入 IBP: Develop 应用程序](link)。

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## 入门套餐注意事项
{: #considerations}

入门套餐是 {{site.data.keyword.blockchainfull_notm}} Platform 的起点，用于开发和测试目的。在使用入门套餐之前，请检查以下各项。

- **{{site.data.keyword.cloud_notm}} 帐户需求**  
    入门套餐在 Beta 期间免费。但是，必须将 {{site.data.keyword.cloud_notm}} 帐户升级至付费帐户，例如，**现买现付**类型。{{site.data.keyword.blockchainfull_notm}} Platform 提供的所有成员资格套餐均需要 {{site.data.keyword.cloud_notm}} 付费帐户。要将帐户升级为“现买现付”类型，请转至 {{site.data.keyword.cloud_notm}} 控制台中的**管理** > **计费和使用情况** > **计费**，然后单击**添加信用卡**。
- **与企业套餐的差异**
    - [CA](glossary.html#ca) 和[排序服务](glossary.html#orderer)不具有容错性，因为每个组织只有一个 CA，并且一个网络只有一个[排序者](glossary.html#orderer)。
    - 排序服务仅使用 [SOLO](glossary.html#SOLO) [共识](glossary.html#consensus)。入门套餐网络只包含一个[排序者](glossary.html#orderer)，此排序者对所有同级执行共识。
    - [硬件安全模块 (HSM)](glossary.html#hsm) 不可用于保护和管理用于高强度认证和加密处理的数字密钥。
- **网络资源限制**  
    入门套餐为每个 {{site.data.keyword.cloud_notm}} 服务实例分配具有 1 个 CPU、2 GB RAM 和 20 Gi 存储器的硬件。如果向相同网络添加更多网络资源，那么资源共享硬件分配。但是，在邀请成员加入网络时，成员会创建新的服务实例。因此，入门套餐为新成员分配另外一套具有 1 个 CPU、2 GB RAM 和 20 Gi 存储器的硬件。
- **不活动网络删除**  
    创建入门套餐 (Beta) 网络后，如果未在 15 天内访问网络或在网络中未发出任何事务，那么 {{site.data.keyword.blockchainfull_notm}} Platform 将删除此网络。
    - 如果不再需要此网络，那么无需执行任何操作，将自动删除此网络。
    - 如果仍需要使用此网络，那么可以通过在网络中发出事务来保持其处于活动状态。可以在 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/apps/){:new_window} 中找到入门套餐 (Beta) 网络。如果没有要发出的常规事务，请遵循[指示信息](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan)来试用样本应用程序。
- **维护和升级**  
入门套餐维护和网络更新会定期执行。在维护期间，不能供应新网络，并且您可能会注意到有短暂的网络中断。
- **数据保留**  
入门套餐不保证在发布升级（包括从 Beta 移至 GA）时保留数据。
- **迁移注意事项**  
目前不支持将数据从入门套餐网络迁移到其他成员资格套餐。但是，可以迁移在入门套餐上经过测试的 `.bna` 文件、链代码和应用程序。有关更多信息，请参阅[从入门套餐迁移到企业套餐](get_start_starter_plan.html#migrate)。
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

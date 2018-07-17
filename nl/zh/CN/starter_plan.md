---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 关于入门套餐
{: #overview}

{{site.data.keyword.blockchainfull}} Platform 入门套餐是一个入门级选项，支持组织模拟多组织区块链网络，快速开发应用程序以及使用样本智能合同和业务网络。它还拥有与其他成员资格选项相同的 UI 体验，有助于消除任何学习曲线。
入门套餐网络是基于 Hyperledger Fabric V1.1 构建的。
{:shortdesc}

**注**：
1. {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐是一个开发与测试环境，不适合生产工作负载。如果需要生产环境，请参阅[关于企业套餐](enterprise_plan.html)。在使用入门套餐之前，请检查[入门套餐注意事项](#considerations)。
2. 2018 年 6 月 14 日，入门套餐从 Beta 移至 GA。如果您拥有的是 Beta 网络，那么可以阅读[迁移注意事项](#beta-to-ga)。

立即注册 [{{site.data.keyword.blockchainfull_notm}} 成员资格 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)，并试用含[试用信用值](howto/pricing.html#starter-plan-pricing)的入门套餐！<!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## 入门套餐提供的内容

- **_一次单击就绪型网络_**  
    入门套餐供应的是实时区块链网络，只需单击一下就可使用。每个网络随附排序服务、CA、两个组织（每个组织一个同级）以及用于处理和部署链代码的缺省通道。{{site.data.keyword.blockchainfull_notm}} Platform 会处理此网络的创建和配置（在网络上线后您可对其进行更新），以便您可专注于开发工作。入门套餐网络是基于 Hyperledger Fabric V1.1 构建的，并将 CouchDB 用作分类帐数据库。<!--The free trial provides you up to two organizations and two peers.-->
- **_成本效益_**  
    入门套餐成员资格选项提供了许多与企业套餐成员资格选项相同的区块链功能，但成本更低。在入门套餐试用期内，可以免费供应使用基本网络资源的区块链网络。
- **_多组织网络模拟_**  
    可以使用入门套餐来模拟使用多个组织构建网络。您不需要实际邀请其他组织加入网络，而是可以自己充当其他组织。通过这种机制，您可了解新组织能如何加入网络，多个组织如何在网络中一起工作，等等。可以通过“网络监视器”切换组织，以从不同组织的角度查看和管理网络。**注**：免费试用的基本资源为两个组织和两个同级，如果添加的资源多于这些资源，可能会产生额外的成本。
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop 应用程序集成_**  
    通过入门套餐，可以部署已在 [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login) 上开发的业务网络。有关更多信息，请参阅[导入 IBP: Develop 应用程序](link)。

## 目标受众

如果您符合下列其中一种情况，那么入门套餐适合您开始区块链之旅。
- **_了解 {{site.data.keyword.blockchainfull_notm}} Platform。_**  
    如果您对 {{site.data.keyword.blockchainfull_notm}} Platform 感兴趣，甚或完全不熟悉区块链，那么通过入门套餐，可以很好地了解如何开发和管理真正的区块链网络。您可以了解组成网络的组件，管理成员资格，了解如何部署和管理链代码（也称为“智能合同”），如何添加通道（并管理通道许可权）以及跟踪何时生成新区块，就像在生产网络中一样。
- **_在实时网络中构建演示解决方案。_**  
    入门套餐为演示区块链应用程序提供了强大的环境。随时可用的区块链网络支持通过“网络监视器”提供的操作和管理工具来快速向同事、管理层和合作伙伴进行演示。
- **_突破单个组织开发。_**  
    通过入门套餐，您可充当多个组织，以了解 IBM Blockchain Platform 如何管理协作任务（如通道创建和链代码实例化），以及如何测试应用程序和调用事务处理。您还可以邀请其他人在入门套餐网络中进行协作（与在企业套餐中一样）。这有助于您在有多个参与方支持链代码和交易的更现实环境中进行构建。
- **_以迭代方式开发和测试区块链应用程序。_**  
    入门套餐为您提供了一个编译打包区域，可用于在区块链网络上持续开发和测试代码。通过移至云，开发者和测试者能专注于功能，并轻松地从单元测试移至功能测试。入门套餐提供了与企业套餐相同的区块链网络功能以及操作和管理工具。在准备好将项目推送到其中一个企业套餐后，您可以按照入门套餐中的相同方式进行操作，但有更多机会使网络增长。


## 入门套餐注意事项
{: #considerations}

入门套餐是 {{site.data.keyword.blockchainfull_notm}} Platform 的起点，用于开发和测试目的。在使用入门套餐之前，请检查以下各项。

- **{{site.data.keyword.cloud_notm}} 帐户需求**  	
    您必须具有付费 {{site.data.keyword.cloud_notm}} 帐户，例如**现买现付**类型。{{site.data.keyword.blockchainfull_notm}} Platform 提供的所有成员资格套餐均需要 {{site.data.keyword.cloud_notm}} 付费帐户。要将帐户升级为“现买现付”类型，请转至 {{site.data.keyword.cloud_notm}} 控制台中的**管理** > **计费和使用情况** > **计费**，然后单击**添加信用卡**。  
- **与企业套餐的差异**
    - [CA](glossary.html#ca) 和[排序服务](glossary.html#orderer)不具有容错性，因为每个组织只有一个 CA，并且一个网络只有一个[排序者](glossary.html#orderer)。
    - 排序服务仅使用 [SOLO](glossary.html#SOLO) [共识](glossary.html#consensus)。入门套餐网络只包含一个[排序者](glossary.html#orderer)，此排序者对所有同级执行共识。
    - [硬件安全模块 (HSM)](glossary.html#hsm) 不可用于保护和管理用于高强度认证和加密处理的数字密钥。
- **网络资源限制**  
    入门套餐为每个同级分配 1 个 CPU 和 2 Gi RAM，为每个 {{site.data.keyword.cloud_notm}} 服务实例分配 20 Gi 存储器。 
- **不活动网络删除**  
    创建入门套餐网络后，如果未在 15 天内访问网络或在网络中未发出任何事务，那么 {{site.data.keyword.blockchainfull_notm}} Platform 将删除此网络。
    - 如果不再需要此网络，那么无需执行任何操作，将自动删除此网络。
    - 如果仍需要使用此网络，那么可以通过在网络中发出事务来保持其处于活动状态。可以在 [{{site.data.keyword.cloud_notm}} 仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/apps/){:new_window} 中找到入门套餐网络。如果没有要发出的常规事务，请遵循[指示信息](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan)来试用样本应用程序。
- **维护和升级**
    入门套餐维护和网络更新会定期执行。在维护期间，不能供应新网络，并且您可能会注意到有短暂的网络中断。
- **数据保留**
    入门套餐不保证在发行版升级（包括从 Beta 移至 GA）时保留数据。
- **迁移注意事项**
    - 目前不支持将数据从入门套餐网络迁移到其他成员资格套餐。但是，可以迁移在入门套餐上经过测试的 `.bna` 文件、链代码和应用程序。有关更多信息，请参阅[从入门套餐迁移到企业套餐](get_start_starter_plan.html#migrate)。

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## 从 Beta 迁移到 GA
{: #beta-to-ga}

2018 年 6 月 14 日，入门套餐移至 GA 阶段。在 GA 中，IBM Blockchain Platform 为每个 IBM Cloud 帐户提供 500 美元的试用信用值，以通过入门套餐创建区块链网络。有关试用信用值的更多信息，请参阅相关主题。2018 年 6 月 14 日，入门套餐移至 GA 阶段。在 GA 中，IBM Blockchain Platform 为每个 IBM Cloud 帐户提供 500 美元的试用信用值，以通过入门套餐创建区块链网络。有关试用信用值的更多信息，请参阅[入门套餐定价](howto/pricing.html#starter-plan-pricing)中的*入门套餐试用*部分。确保您具有付费 IBM Cloud 帐户，例如**现买现付**类型。
. 确保您具有付费 IBM Cloud 帐户，例如**现买现付**类型。

使用入门套餐 Beta 创建的任何区块链网络都会保持**免费**，直到入门套餐 GA 发布 **30 天**后被删除。不支持将数据从入门套餐 Beta 网络迁移到 GA 网络。**Beta 网络中的数据将丢失。**但是，可以手动迁移链代码、业务网络和应用程序。
- 如果在 Beta 网络中有正在运行的链代码，请在 GA 网络中安装并实例化该链代码。有关更多信息，请参阅[安装、实例化和更新链代码](howto/install_instantiate_chaincode.html)。
- 如果在 Beta 网络上部署了业务网络，请使用 `.bna` 文件在 GA 网络上部署该业务网络。有关更多信息，请参阅[在入门套餐上部署业务网络](develop_starter.html)。
- 如果针对 Beta 网络运行了自行开发的应用程序，请更新应用程序中的 API 端点以指向 GA 网络节点。有关更多信息，请参阅[将网络 API 端点添加到应用程序](v10_application.html#adding-network-api-endpoints-to-your-application)。

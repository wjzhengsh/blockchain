---

copyright:
  years: 2017
lastupdated: "2017-12-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 管理网络
{: #getting-started-with-blockchain}

{{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull}} Platform 提供具有高度安全性、完整性、可伸缩性和性能的 {{site.data.keyword.blockchain}} 网络。您可以快速供应功能完整的 {{site.data.keyword.blockchain}} 网络，并使用“网络监视器”立即运行链代码和应用程序，而无需设计和配置专用区块链网络。
{:shortdesc}
 
本入门教程介绍了在获取 IBM 的高可用性和安全环境上托管的 {{site.data.keyword.blockchain}} 网络时需要遵循的先决条件和步骤。  

以下步骤指示启动具有多个成员组织的 {{site.data.keyword.blockchain}} 网络的基本流程：
1. **网络发起者**作为一种特殊类型的网络成员，创建 {{site.data.keyword.blockchain}} 网络并定义管控策略。然后，网络发起者可以邀请其他机构作为网络成员加入此 {{site.data.keyword.blockchain}} 网络。有关更多详细信息，请参阅[创建网络](#creating-a-network)。
2. **网络成员**接收电子邮件通知，该通知提供有关这些成员加入 {{site.data.keyword.blockchain}} 网络的指示信息。除了电子邮件通知中的指示信息外，还可以遵循[加入网络](#joining-a-network)中的步骤。
3. 在创建或加入网络之后，所有**网络成员**都可以进入“网络监视器”（GUI 仪表板），来配置和管理其网络资源。您可以设置具有一组网络成员的通道，以在仅通道成员才能访问的通道特定的分类帐上执行专用事务处理。在“网络监视器”中，您还可以将自己的同级加入通道，并在它们之上安装和实例化链代码。有关更多详细信息，请参阅[配置网络资源和环境](#configuring-network-resources-and-environment)。
4. **应用程序开发者**创建支持与 {{site.data.keyword.blockchain}} 网络进行交互的应用程序。有关更多信息，请参阅[实现应用程序与网络的交互](#enabling-applications-to-interact-with-the-network)。
5. **网络操作员**使用“网络监视器”来监视其通道上的事务处理。有关更多详细信息，请参阅[监视网络资源](#monitoring-network-resources)。

## 创建网络
开始之前，需要在 {{site.data.keyword.Bluemix_notm}} 上创建 [{{site.data.keyword.blockchain}} Platform 服务实例 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain)。您需要使用 {{site.data.keyword.Bluemix_notm}} 标识登录。如果没有标识，请单击**注册以创建**按钮。请为您的实例重命名服务和凭证名称，以便将来易于识别。选择 {{site.data.keyword.Bluemix_notm}} 区域、组织和空间，您可以在其中部署 {{site.data.keyword.blockchain}} 网络。然后从定价套餐表中选择**企业套餐**，然后单击**创建**按钮。  

您可以在 [{{site.data.keyword.Bluemix_notm}} 服务仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} 服务仪表板") 中找到 {{site.data.keyword.blockchain}} Platform 服务实例。  

如果您是网络发起者，请单击**创建网络**按钮，以启动 {{site.data.keyword.blockchain}} 网络。请遵循向导，以完成网络和资源的基本配置。  
![“创建网络”向导](images/create_network_name.png "“创建网络”向导")  

1. 在“开始使用”屏幕中，为您的网络提供一个名称，选择 {{site.data.keyword.Bluemix_notm}} 组织的位置，并添加您所在机构的名称。当您邀请其他网络成员时，他们将查找此网络名称以加入。单击**下一步**。
2. （可选）在“邀请成员”屏幕中，输入要邀请加入网络的成员的机构名称和电子邮件地址。您指定的机构名称不用是正式名称。只要机构易于识别，且在加入网络时可进行更改即可。请注意，一个网络最多可以有 15 个成员，包括您自己。此步骤是可选的，您可以稍后在“网络监视器”中邀请成员加入您的网络。单击**下一步**。在完成创建网络的所有步骤后，您邀请的成员将收到有关邀请的电子邮件通知。
3. 在“定义管控规则”屏幕中，为成员资格、通道创建和链代码建立策略。缺省情况下，所有网络成员都可以邀请其他成员加入网络、创建通道和实例化链代码。对于此 GA，您的网络使用缺省管控策略。单击**下一步**。
4. 在“复查摘要”屏幕中，验证网络配置。如果要进行修改，请单击该部分标题旁边的**编辑**，或者单击**上一步**按钮以返回到先前屏幕。完成网络配置后，单击**完成**。  
5. 在“已创建网络”屏幕中，您将收到您的网络已成功创建的通知。可以单击**添加同级**来配置网络资源，或者直接单击**输入监视器**以打开“网络监视器”。如果首先不添加同级，可以稍后在“网络监视器”中进行添加。有关同级的更多信息，请参阅[概述](v10_dashboard.html#overview)。
    
现在，您可以部署能够支持以下网络资源的 {{site.data.keyword.blockchain}} 网络：  
* 成员特定认证中心 (CA)
* 缺省管控策略
* 最多 15 个网络成员  
* 三个订货人和两个中间 CA 节点
* 每个成员最多有三个小同级  
* 崩溃容错订购服务
* 最多 150 个通道
* 最多 10 个链代码实例化


## 加入网络
与创建网络类似，您需要在 {{site.data.keyword.Bluemix_notm}} 上创建 [{{site.data.keyword.blockchain}} Platform 服务实例 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain)。您需要使用 {{site.data.keyword.Bluemix_notm}} 标识登录。如果没有标识，请单击**注册以创建**按钮。请为您的实例重命名服务和凭证名称，以便将来易于识别。选择 {{site.data.keyword.Bluemix_notm}} 区域、组织和空间，您可以在其中部署 {{site.data.keyword.blockchain}} 网络。然后从定价套餐表中选择**企业套餐**，然后单击**创建**按钮。  

您可以在 [{{site.data.keyword.Bluemix_notm}} 服务仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} 服务仪表板") 中找到 {{site.data.keyword.blockchain}} Platform 服务实例。 

如果您是受邀网络成员，请单击**暂挂邀请 ->** 按钮，从下拉列表中选择要加入的网络，然后单击**加入网络！**按钮。请遵循向导，以查看网络的基本配置并配置自己的网络组件。  
![“加入网络”向导](images/join_network_name.png "“加入网络”向导")  

1. 在“开始使用”屏幕中，添加机构的名称并验证 {{site.data.keyword.Bluemix_notm}} 组织的位置。单击**下一步**。
2. 在“复查管控规则”屏幕中，查看成员资格、通道创建和链代码的网络管控策略。单击**下一步**。
3. （可选）在“添加同级”屏幕中，选择要添加的同级的大小和数量。单击**下一步**。网络中的每个成员最多可以添加 3 个同级；此时只有“小”同级可用。此步骤是可选的，您可以稍后在“网络监视器”中添加同级。有关同级的更多信息，请参阅[概述](v10_dashboard.html#overview)。
4. 在“复查网络摘要”屏幕中，验证网络配置。如果要进行修改，请单击该部分标题旁边的**编辑**，或者单击**上一步**按钮以返回到先前屏幕。完成资源配置后，单击**完成**。系统将通知您已成功加入网络。然后，您可以单击**进入监视器**以打开“网络监视器”，或者单击**创建通道**以启动通道创建请求。您可以稍后在“网络监视器”中创建通道。有关更多信息，请参阅[通道](v10_dashboard.html#channels)。
 

## 配置网络资源和环境

1. 创建或加入 {{site.data.keyword.blockchain}} 网络后进入“网络监视器”。“网络监视器”是 GUI 仪表板，您可以在其中管理和跟踪网络状态信息。有关更多信息，请参阅[网络监视器](v10_dashboard.html)。
2. 将自己的同级添加到网络中。如果已添加足够多的同级，请跳过此步骤。同级运行链代码，它们是与应用程序交互的端点。单击“概述”屏幕中的**添加同级**，然后选择同级的数量和大小。有关更多信息，请参阅[概述](v10_dashboard.html#resources)。
3. 设置通道。同一通道中的所有成员都将供应通道特定的分类帐，该分类帐可提供数据隔离和机密性。有关如何创建通道的信息，请参阅[创建通道](howto/create_channel.html#creating-a-channel)。  
	
	如果您是已受邀加入某个通道的通道成员，那么您将收到一封电子邮件通知，其中包含指向向导的链接，该向导将允许您加入该通道。
4. 将同级加入通道。只有与该通道相关联的同级才能访问其分类帐。有关更多信息，请参阅[通道](v10_dashboard.html#channels)。
5. 安装和实例化链代码。所有通道成员都需要在将运行此链代码的每个同级上安装具有相同名称和版本的相同链代码。安装后，必须先在通道上实例化链代码，然后才能使用。有关更多信息，请参阅[安装和实例化链代码](howto/install_instantiate_chaincode.html)。  

**注**：要实现高可用性，每个机构必须至少购买 2 个同级，并且在一个通道中，每个参与机构必须至少加入 2 个同级。

## 实现应用程序与网络的交互
应用程序利用 SDK API 与 {{site.data.keyword.blockchain}} 网络组件进行交互。您需要在应用程序中添加网络组件的 API 端点信息，以便应用程序最终可以将具有事务处理请求的同级作为目标。然后，可以从“网络监视器”中添加 API 端点信息。可以在本地文件系统或 {{site.data.keyword.Bluemix_notm}} 上托管应用程序。有关更多信息，请参阅[开发应用程序](v10_application.html) 。

## 监视网络资源  
从应用程序触发事务处理后，您可以在“网络监视器”中查看事务处理状态信息。有关网络监视的更多信息，请参阅[监视网络](howto/monitor_network.html)。
  
## 离开网络 
如果您要离开网络，请从 {{site.data.keyword.Bluemix_notm}} 仪表板删除区块链服务实例。  

**注**：在离开网络之前，请确保您不是网络任何通道中的成员。否则，当您离开网络时将会获得错误。除去通道成员需要完成通道更新过程。有关通道更新过程的更多信息，请参阅[更新通道](howto/create_channel.html#updating-a-channel)。


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.0, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

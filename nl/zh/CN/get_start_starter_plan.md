---

copyright:
  years: 2018
lastupdated: "2018-05-23"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 管理入门套餐网络
{: #getting-started-with-starter-plan}

{{site.data.keyword.blockchainfull}} Platform 入门套餐提供了一个预先配置的区块链网络，只需单击一下就可使用。入门套餐<!--offers you a free trial of 30 days and -->供应一个许可的网络，缺省情况下配置有两个[组织](glossary.html#organization)、每个组织一个[同级](glossary.html#peer)以及一个[通道](glossary.html#channel)。创建网络后，可以向网络扩展和添加更多组织和同级。<!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**注**：
1. {{site.data.keyword.blockchainfull}} Platform 入门套餐是一个开发与测试环境，不适合生产工作负载。如果需要生产环境，请参阅[关于企业套餐](enterprise_plan.html)。
2. 现在，入门套餐处于 Beta 阶段，仅在 {{site.data.keyword.cloud_notm}} 中的**美国南部**区域中可用。

入门套餐支持您学习和培养使用 {{site.data.keyword.blockchainfull_notm}} Platform 的技能，运行样本应用程序，测试您自己的应用程序以及模拟多组织场景。本入门教程介绍了创建和使用入门套餐网络时需要遵循的先决条件和步骤。

如果您是 {{site.data.keyword.blockchainfull_notm}} Platform 和区块链新手，那么可以阅读[词汇表](glossary.html)来熟悉本文档中使用的术语，还可以阅读 [Hyperledger Fabric 文档 ![外部链接图标](images/external_link.svg "外部链接图标")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html) 以了解有关区块链的更多信息。


## 创建网络
创建 {{site.data.keyword.blockchainfull_notm}} Platform 服务实例后，可以立即获取使用缺省配置的入门套餐[网络](glossary.html#network)。

1. 在 {{site.data.keyword.cloud_notm}}“目录”中，找到 [Blockchain 服务 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain)。   
    **注**：您需要使用 {{site.data.keyword.cloud_notm}} 付费帐户登录。如果您没有帐户，请单击**注册以创建**按钮。在创建免费试用帐户后，通过转至 {{site.data.keyword.cloud_notm}} 控制台中的**管理** > **计费和使用情况** > **计费**，然后单击**添加信用卡**，将其升级为**现买现付**类型。
2. 确保在 {{site.data.keyword.cloud_notm}} 中选择**美国南部**作为区域。
3. 选择要在其中创建网络的 Cloud Foundry 组织和空间。
4. 从价格套餐表中选择**入门成员资格套餐**。
5. 单击**创建**按钮。请注意，如果您受邀加入网络，那么会看到一个弹出式欢迎面板。要创建网络，请选择**继续至网络**，然后单击**继续**。要加入网络，请参阅[加入网络](#joining-a-network)中的步骤 5。
  现在您已准备好使用具有缺省配置的入门套餐网络。网络运行时，会有一个排序者（称为“SOLO”排序服务）、两个组织、一个 CA 以及每个组织一个同级。此外还会创建一个缺省通道。
6. 单击**启动**按钮。

您可以在 [{{site.data.keyword.Bluemix_notm}} 服务仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} 服务仪表板") 中找到区块链服务实例。


## 邀请成员
您可以邀请其他[组织](glossary.html#organization)作为[成员](glossary.html#member)加入入门套餐网络，以便可以相互执行[事务处理](glossary.html#transaction)。此外，如果是出于学习和测试目的而使用入门套餐，那么可以通过自行向网络添加成员来模拟多组织网络。

1. 在“网络监视器”的“成员”屏幕中，单击**邀请成员**按钮。
2. 这将打开“邀请成员”窗口。
    - 如果要邀请其他组织，请选择“邀请成员”。指定要邀请的组织的名称和操作员电子邮件地址。还可以在“添加注释”字段中，输入要包含在邀请中的其他信息。单击**发送邀请**按钮。受邀组织将收到一封邀请电子邮件，然后可以遵循电子邮件中的指示信息来加入您的网络。
    - 如果要添加可以添加到通道的其他组织，请选择“添加成员”。为新组织指定名称。可以选择将同级添加到新组织，也可以日后在“网络监视器”中进行添加。单击**创建**按钮。请注意，如果为新组织添加了同级，您需要切换到此新组织才能查看同级。有关切换组织的更多信息，请参阅[切换组织](dashboard.html#switch-organizations)。


## 加入网络
如果您受网络发起者邀请，那么您将收到电子邮件通知，其中包含有关如何加入网络的指示信息。请遵循电子邮件中的指示信息进行操作，完成后您将成为网络中的其中一个成员。

您需要在 {{site.data.keyword.cloud_notm}} 中使用入门套餐成员资格选项来创建 [{{site.data.keyword.blockchain}} Platform 服务实例 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain)。

1. 使用 {{site.data.keyword.cloud_notm}} 帐户登录。如果您没有帐户，请单击**注册以创建**按钮。
2. 选择要存储 {{site.data.keyword.blockchain}} 网络的 Cloud Foundry 组织。
3. 从价格套餐表中选择**入门成员资格套餐**。
4. 单击**创建**按钮。这将打开服务实例页面并弹出欢迎面板。请注意，可以选择加入网络，也可以继续创建自己的网络。要创建网络，请参阅[创建网络](#creating-a-network)中的步骤 4。
5. 在欢迎面板上，选择**加入现有网络**，从下拉列表中选择要加入的网络，然后单击**继续**。

可以在 [{{site.data.keyword.cloud_notm}} 服务仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 服务仪表板") 中找到您的区块链服务实例。

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
-->


## 检索网络凭证和连接概要文件
在 {{site.data.keyword.cloud_notm}} 中创建入门套餐网络后，可以在服务实例页面中或在“网络监视器”中检索网络凭证和连接概要文件。

### 在服务实例页面中进行检索
一旦创建服务实例后，您将位于服务实例页面上。您还可以在 [{{site.data.keyword.cloud_notm}} 服务仪表板 ![外部链接图标](images/external_link.svg "外部链接图标")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 服务仪表板") 中单击服务来打开服务实例页面。

使用以下步骤来检索服务凭证：
1. 在服务实例页面上，单击左侧导航器中的**服务凭证**，以显示“服务凭证”屏幕。
2. 单击“服务凭证”屏幕中的**新建凭证**。
3. 在“添加新凭证”屏幕中，为凭证提供名称，然后单击**添加**。新的凭证会添加到表中。您可以单击“操作”列下的**查看凭证**以查看凭证详细信息。此凭证包含 API 密钥和私钥，可用于授权 API。
      
如果要查看网络的连接概要文件，请在创建新凭证时，输入 **{"legacy": true}** 作为内联配置参数。连接概要文件包含网络资源的 API 端点，可以在 API 和应用程序中使用。

![检索网络凭证](images/service_credentials.gif "检索网络凭证")

### 在网络监视器中进行检索
可以在“网络监视器”的“API”屏幕上找到网络凭证。有关使用 API 的更多信息，请参阅[使用 Swagger API 与网络进行交互](howto/swagger_apis.html)。

可以在“网络监视器”的“概述”屏幕上检索连接概要文件。单击“概述”屏幕上的**连接概要文件**按钮，连接概要文件会显示在新页面中。


## 部署样本应用程序
使用入门套餐时，只需单击几下就可将样本应用程序部署到网络中。通过这些样本，可以轻松了解链代码和应用程序在网络中的工作方式。

有关更多信息，请参阅[部署样本应用程序](howto/prebuilt_samples.html)。


## 开发并部署定制业务网络
入门套餐可以将 IBM Blockchain Platform: Develop 开发者环境和 Hyperledger Composer 开发者工具集相集成。可以基于您的业务需求来开发区块链网络。开发业务网络后，可以将其部署到入门套餐网络。

有关更多信息，请参阅[开发网络](develop.html)和[使用入门套餐部署业务网络](develop_starter.html)。


## 监视网络资源
如果应用程序请求事务处理，您可以在“网络监视器”中查看事务处理状态信息。有关网络监视的更多信息，请参阅[监视网络](howto/monitor_network.html)。


## 重置网络
如果要清除定制配置、正在运行的链代码或已部署的应用程序，您可以将网络重新设置为初始缺省配置。有关更多信息，请参阅[重置网络](dashboard.html#reset-network)。


## 从入门套餐迁移到企业套餐
{: #migrate}

可以将针对入门套餐网络测试的 `.bna`、链代码和应用程序部署到企业套餐网络中。

如果您有自己的业务网络归档文件 (`.bna`)，请遵循[将业务网络部署到企业套餐](./develop_enterprise.html)的指示信息。如果您没有 `.bna` 文件，请使用 `composer network download` 命令在入门套餐实例中检索该文件。有关 `composer network download` 命令的更多信息，请参阅 [Hyperledger Composer 命令行文档 ![外部链接图标](images/external_link.svg "外部链接图标")](https://hyperledger.github.io/composer/reference/commands){:new_window}。

链代码与 `.bna` 文件类似，也是在外部开发的。要将针对入门套餐网络测试的链代码部署到企业套餐中，请遵循[安装、实例化和更新链代码](howto/install_instantiate_chaincode.html#installchaincode)中的指示信息进行操作。

正如您在[部署样本应用程序](howto/prebuilt_samples.html)中所见，通过入门套餐，可以使用工具链轻松获得与网络集成的样本应用程序。此设置还支持每当派生的应用程序存储库更改时，都通过自动更新样本应用程序进行持续集成。如果要将此应用程序部署到企业套餐网络中，可以将派生的应用程序存储库复制到新的存储库中，然后遵循[手动部署样本应用程序](howto/prebuilt_samples.html#deploy_sample_applications_manually)中的指示信息进行操作。


## 删除或离开网络
如果您要删除或离开网络，可以从 {{site.data.keyword.cloud_notm}}“仪表板”中删除区块链服务实例。

**注**：在离开网络之前，请确保您不是网络任何通道中的成员。否则，当您离开网络时将会获得错误。除去通道成员需要完成通道更新过程。有关通道更新过程的更多信息，请参阅[更新通道](howto/create_channel.html#updating-a-channel)。


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

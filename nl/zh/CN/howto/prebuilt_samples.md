---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 部署样本应用程序
{: #overview}

{{site.data.keyword.blockchainfull}} Platform 提供了样本应用程序，您可进行部署和测试，以便更好地了解区块链网络和应用程序开发。
{: shortdesc}

供应入门套餐网络后，可以将样本应用程序部署到“网络监视器”中，以自动执行支持样本应用程序在网络上运行的步骤。您还可以逐步启用样本以了解应用程序部署的整个过程，这是在部署自己的应用程序时需要遵循的过程。

## 在入门套餐中部署样本应用程序

入门套餐提供了一种简单的方法，只需单击几下就可利用 {{site.data.keyword.cloud_notm}} 上的 Toolchain 服务来部署样本应用程序。样本应用程序部署并启动后，会自动在区块链网络上运行。

入门套餐提供了两个样本应用程序供您开始使用。

* **Marbles**

  Marbles 样本支持用户创建具有不同属性的虚拟弹珠，并向其他用户传输这些弹珠。有关 Marbles 的更多信息，请参阅 [Marbles 演示 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://github.com/IBM-Blockchain/marbles)。

* **Vehicle Manufacture**

  Vehicle Manufacture 样本支持用户虚拟经历车辆的整个生命周期。有关此样本的更多信息，请参阅 [Vehicle Manufacture ![外部链接图标](../images/external_link.svg "外部链接图标")](https://github.com/IBM-Blockchain/vehicle-manufacture)。 

要部署样本应用程序，请完成以下步骤：

1. 进入入门套餐网络的**网络监视器**。如果还没有“网络监视器”，请参阅[创建网络](../get_start_starter_plan.html#creating-a-network)。

2. 在“网络监视器”中打开“试用样本”屏幕。选择要部署的样本应用程序，然后单击**通过 Toolchain 部署**按钮。
<!--
    ![sampleappflow0](../images/sampleappflow0.png)
-->
3. 这将打开 Toolchain 服务配置窗口。确保所有必需的工具都已正确集成。请注意，如果您有多个组织，请确保输入正确的组织名称。组织名称应该是用于注册网络的电子邮件地址。**提示**：您必须禁用弹出窗口阻止程序，才能打开 Toolchain 服务配置页面。
<!--
    ![sampleappflow1](../images/sampleappflow1.png)
-->
  如果这是通过 Toolchain 部署的第一个应用程序，您需要授权 Toolchain 来访问 GitHub 存储库。

  ![sampleappflow2](../images/sampleappflow2.png)

  单击“授权”按钮后，您将转至 GitHub。如果您没有 GitHub 帐户，那么需要进行创建。通过输入您的帐户信息，授予 Toolchain 对您存储库的访问权。如果不想授予 Toolchain 此访问权，您可以手动部署样本应用程序。有关更多信息，请参阅[手动部署样本应用程序](#deploy_sample_applications_manually)。

5. 单击 Toolchain 页面底部的**创建**按钮。这应该会使您返回到“网络监视器”，在其中应该正在执行 Marbles 的部署。此过程需要 5 到 10 分钟。
<!--
    ![sampleappflow3](../images/sampleappflow3.png)
-->
部署完成后，即可以开始在入门套餐网络上使用 Marbles 样本。

由于此过程会创建您有权访问并控制的派生 GitHub 存储库，因此可以在派生的存储库中对 Marbles 进行更改并提交这些更改。这些提交操作将触发自动构建 Marbles 应用程序，并允许您在 {{site.data.keyword.cloud_notm}} 中对其进行演示。

## 手动部署样本应用程序
{: #deploy_sample_applications_manually}

如果要在不使用“网络监视器”的情况下部署样本应用程序，请确保在本地文件系统上安装所有必备软件。有关更多信息，请参阅[设置应用程序开发环境](../v10_application.html#setting-up-application-development-environment)。

您还需要使用入门套餐或企业套餐在 {{site.data.keyword.cloud_notm}} 上建立区块链网络，并配置通道及其同级。有关更多信息，请参阅[管理入门套餐网络](../get_start_starter_plan.html)和[管理企业套餐网络](../get_start.html)。供应网络并可以在其上部署应用程序后，请检索应用程序将访问的网络资源的 API 端点。有关更多信息，请参阅[将网络 API 端点添加到应用程序](../v10_application.html#adding-network-api-endpoints-to-your-application)。

您可以将下列其中一个样本应用程序部署到网络中：

- **Marbles**

  在 Marbles 应用程序中，多个用户可以创建具有不同属性的弹珠，并将其传输给其他人。Marbles 应用程序以 JavaScript 编写，而链代码以 Go 编写。

  您可以在 [GitHub 中的 Marbles ![外部链接图标](../images/external_link.svg "外部链接图标")](https://github.com/IBM-Blockchain/marbles) 中找到样本代码和指示信息。

  请使用 Bluemix 指示信息（而不要使用本地托管 Marbles 的指示信息），输入您网络中的相关信息。请注意，Marbles GitHub 中的截屏显示的是企业套餐 UI（因为企业套餐仅支持部署 Marbles 的手动路径），与入门套餐 UI 有点不同。不过，这两个 UI 的基本部分是相同的，您可以在相应的屏幕中找到同级名称、通道和其他服务凭证信息。

- **Fabcar**

  在 Fabcar 应用程序中，可以对分类帐中的汽车记录执行**查询**和**分类帐更新**操作。Fabcar 应用程序以 JavaScript 编写，而链代码以 Go 编写。

  您可以在 [GitHub 中的 Fabric car ![外部链接图标](../images/external_link.svg "外部链接图标")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) 中找到样本代码，在[编写第一个应用程序 ![外部链接图标](../images/external_link.svg "外部链接图标")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html) 中找到指示信息。

- **其他应用程序**

  有关如何在 {{site.data.keyword.cloud_notm}} 内部托管自己应用程序的更多信息，请参阅[托管应用程序](../v10_application.html#hosting-applications)。

## 删除样本应用程序

要删除通过 Toolchain 过程获取的样本应用程序，请浏览至样本在 UI 中的位置。由于样本应用程序是在通道上实例化的，因此可以在通道中找到样本。单击左侧导航栏上的**通道**以打开“通道”屏幕。单击在其中实例化样本的相关通道，然后单击**链代码**。这将显示在此通道上实例化的链代码。

如果单击应用程序的链代码，可以看到**删除**选项卡。但是，仅单击**删除**并不会删除样本应用程序，而只会删除链代码容器。您还需要导航至 {{site.data.keyword.cloud_notm}}“仪表板”和 Toolchain 仪表板以在其中删除样本。

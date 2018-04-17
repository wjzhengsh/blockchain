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

# 运行企业套餐网络
{: #v10_dashboard}

{{site.data.keyword.blockchainfull}} Platform 中的“网络监视器”提供区块链环境的概述，包括网络资源、成员、加入的通道、事务处理性能数据和已部署的链代码。“网络监视器”还提供了入口点来运行 Swagger API，使用 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 来开发网络以及试用样本应用程序。
{:shortdesc}

“网络监视器”分三个部分显示以下屏幕。您可以在“网络监视器”左侧导航器中导航至各个屏幕。
- **我的网络**部分包含“[概述](#overview)”、“[成员](#members)”、“[通道](#channels)”、“[通知](#notifications)”和“[API](#apis)”屏幕。
- **我的代码**部分包含“[编写代码](#write_code)”、“[安装代码](#chaincode)”和“[试用样本](#samples)”屏幕。
- “[获取帮助](#support)”屏幕显示支持信息以及 Helios 和 Hyperledger Fabric（{{site.data.keyword.blockchainfull_notm}} Platform 基于的代码库）的发行说明。


## 概述
{: #overview}

“概述”屏幕显示有关区块链资源（包括排序者、CA 和同级节点）的实时状态信息。每个资源都显示在四个不同的标头下：**类型**、**名称**、**状态**和**操作**。在创建区块链网络期间，会自动创建三个订货人节点和两个 CA 节点。CA 特定于成员，而排序节点是在整个网络中共享的公共端点。

**图 1** 显示“概述”屏幕：

![概述屏幕](images/myresources.png "网络概述")
*图 1. 网络概述*

### 节点操作
  表的**操作**标头提供了用于启动或停止资源的按钮。您还可以通过选择多个节点，然后单击**启动所选项**或**停止所选项**按钮，来启动或停止一组节点。选择一个或多个节点时，**启动所选项**或**停止所选项**按钮将显示在表的顶部。

  请注意，“停止”和“启动”操作不可用于“排序者”节点。通常，不需要在网络上停止和启动同级或 CA 节点。提供的“停止”和“启动”操作是为了满足重新启动同级的需要，例如要以全新方式启动同级时。

  您还可以通过单击**操作**标头下的下拉列表中的**查看日志**来检查组件日志。日志会公开在各种网络资源之间的调用，且在进行调试和故障诊断时非常有用。例如，通过停止同级并尝试将其设定为某个事务处理的目标来进行试验；您将看到连接错误。重新启动该同级并重试该事务处理时，您将看到连接成功。您还可以在通道继续进行事务处理时，使某个同级停止运行较长的一段时间。该同级再次启动后，您将注意到分类帐同步，因为该同级会收到在它停止运行期间提交的块。在分类帐完全同步后，您可以对其执行正常的调用和查询。

### 连接概要文件
  您可以通过单击**连接概要文件**按钮来查看有关每个资源的低级别网络信息的 JSON 文件。连接概要文件包含应用程序所需的所有配置信息。但是，由于此文件仅包含特定组件和排序者的地址，因此如果需要将更多同级设定为目标，您需要获取这些同级的端点。包含“url”的标头显示每个组件的 API 端点。需要这些端点才能以客户端应用程序的特定网络组件为目标，而其定义通常存在于应用程序随附的 JSON 模型化配置文件中。如果您要定制的应用程序需要不属于您组织的同级支持，那么您需要在带外操作中从相关操作员处检索这些同级的 IP 地址。客户端必须能够连接到它们需要从中获取响应的任何同级。


### 添加同级
{: #peers}
单击**添加同级**按钮，以将同级节点添加到网络。在入门套餐中，在网络创建时会自动添加两个同级。在企业套餐中，可以在创建或加入网络时首次添加同级节点，也可以日后在“网络监视器”中进行添加。如果需要更多同级，您可能处于不同的场景中。例如，您可能希望多个同级加入同一个通道以实现冗余。每个同级都会处理该通道的事务，并写入其各自的分类帐副本。如果其中一个同级发生故障，其他同级可以继续处理事务和应用程序请求。您可以在各同级之间对所有应用程序请求进行对称负载均衡，或者可以针对不同的功能将不同的同级设定为目标。例如，可以使用一个同级来查询分类账，而使用另一个同级来处理对分类帐更新的支持。

  在弹出的“添加同级”面板中，选择要添加的同级节点的数目。
<!--Currently only "small" peers are available for purchase, however there will eventually be "medium" and "large" to help accommodate larger workloads and higher transaction throughput.-->


## 成员
{: #members}
“成员”屏幕包含两个选项卡，用于在“成员”选项卡中显示网络成员信息，在“证书”选项卡中显示证书信息。

### 成员
{: #members_tab}
**图 2** 显示初始“成员”屏幕，其中在“成员”选项卡中显示网络成员：

![“成员”屏幕中的“成员”选项卡](images/monitor_members.png "网络成员")
*图 2. 网络成员*

除了在创建网络时邀请的成员以外，您还可以在“成员”选项卡中邀请其他成员。要邀请成员加入您的网络，请输入机构名称和操作员的电子邮件地址，然后单击**添加成员**。网络总计可以有 15 个成员（包括网络发起者）。要从网络中除去成员，请单击成员行末尾的“除去”符号。

### 证书
**图 3** 显示初始“成员”屏幕，其中在“证书”选项卡中显示成员证书：

![“成员”屏幕中的“证书”选项卡](images/monitor_certificates.png "证书")
*图 3. 证书*

操作员可以在“证书”选项卡中管理同一机构的成员的证书。单击**添加证书**以打开“添加证书”面板。向证书提供名称、将 PEM 格式的客户机端证书粘贴到“密钥”字段，然后单击**提交**。您需要在客户机端证书生效之前重新启动同级。

有关生成证书密钥的更多信息，请参阅[生成客户机端证书](v10_application.html#generating-the-client-side-certificates)。

## 通道
{: #channels}

您可以将网络划分为多个通道，其中每个通道都表示有权查看该通道上已实例化的链代码数据的一部分成员。每个网络都必须至少有一个通道，以便进行事务处理。每个通道具有唯一的分类帐，用户必须经过正确的认证才能对此分类帐执行读/写操作。如果您不在通道上，那么看不到任何数据。

**图 4** 显示初始仪表板屏幕，其中显示网络中所有通道的概述：

![通道](images/channels.png "通道")
*图 4. 通道*

创建通道将生成特定于通道的分类帐。有关更多信息，请参阅[创建通道](howto/create_channel.html)。

您还可以选择现有通道以查看有关通道、成员资格和活动链代码的更精确详细信息。有关更多信息，请参阅[监视网络](howto/monitor_network.html)。


## 通知
{: #notifications}

您可以处理暂挂请求，并在“通知”屏幕中查看已完成的请求。

**图 5** 显示“通知”屏幕：

![通知](images/notifications.png "通知")
*图 5. 通知*

在您创建通道或您受邀加入新通道时，将在“网络监视器”中出现一个通知。

这些请求分组为“全部”、“暂挂”和“已完成”子选项卡。子选项卡标题后的数字指示每个子选项卡中的请求数。
   * 您可以在“全部”子选项卡中找到所有请求。
   * 未接受或拒绝的请求或尚未查看的请求在“暂挂”子选项卡中。单击**复查请求**按钮以查看请求，包括通道策略和成员，以及投票状态。如果您是通道操作员，那么您可以**接受**或**拒绝**请求，或者单击**稍后**以在其他时间对其进行处理。如果有足够多的通道操作员接受请求，那么您可以单击**提交请求**以激活通道更新。
   * 提交的请求将显示在“已完成”子选项卡中。您可以单击**复查请求**以查看其详细信息。

如果有一个较长的请求列表，那么可以在顶部的搜索字段中搜索请求。

通过选择暂挂请求前面的框并单击**删除请求**，可以删除暂挂请求。请注意，无法删除已完成的请求。


## API
{: #apis}

为了帮助进行应用程序开发，{{site.data.keyword.blockchainfull_notm}} Platform 公开了 API，您可以在 Swagger UI 中针对自己的网络来测试这些 API。

![API](images/API_screen.png "API")
*图 6. API*

单击 **Swagger UI** 链接以打开 Swagger UI。请注意，您需要先使用自己的网络凭证（可以在此 API 页面上找到）来授权 Swagger UI，然后才能运行 API。有关更多信息，请参阅[使用 Swagger API](howto/swagger_apis.html)。


## 编写代码
{: #write-code}

企业套餐集成了 {{site.data.keyword.blockchainfull_notm}} Platform: Develop，并提供了具有业界标准工具和技术的开发环境。您可以在该环境中以联机或本地方式开发网络。开发网络后，可以将其部署回企业套餐网络。

![编写代码](images/write_code.png "编写代码")
*图 7. 编写代码*

有关使用企业套餐来开发和部署代码的更多信息，请参阅[使用企业套餐开发业务网络](develop_enterprise.html)。


## 安装代码
{: #chaincode}

链代码（也称为“智能合同”）是包含一组对分类帐进行查询和更新的函数的软件片段。链代码安装在同级上并在通道上进行实例化。

![安装代码](images/chaincode_install_overview.png "安装代码")
*图 8. 安装代码*

链代码首先安装在同级的文件系统上，然后在通道上进行实例化。有关更多信息，请参阅[安装和实例化链代码](howto/install_instantiate_chaincode.html)。


## 试用样本
{: #samples}

样本应用程序可帮助您更好地了解区块链网络和应用程序开发。请访问“网络监视器”中 Marbles 存储库的链接，以获取有关如何安装 Marbles 样本应用程序的信息。有关如何开发和部署自己样本的更多信息，请查看[部署应用程序](v10_application.html)。

**图 9** 显示“样本”屏幕：

![试用样本](images/sample_overview.png "试用样本")
*图 9. 样本*


## 获取帮助
{: #support}

“获取帮助”屏幕包含两个选项卡，在“支持”选项卡中提供支持信息，在“发行说明”选项卡中描述每个发行版的新增功能和已更改功能。

**图 10** 显示初始“支持”屏幕，其中在“支持”选项卡中显示支持信息：

![支持](images/support.png "支持")
*图 10. 区块链支持*

使用此页面上的链接和资源，可访问故障诊断和支持论坛。

* **入门**下的 [{{site.data.keyword.blockchainfull_notm}} 服务文档](index.html)是此文档的站点，提供有关如何在 {{site.data.keyword.Bluemix_notm}} 上开始使用 {{site.data.keyword.blockchainfull}} Platform 的指导信息。您可以在左侧导航器中查找相应主题，也可以使用顶部的搜索功能搜索任何项。
* **社区帮助** 下的 [IBM DeveloperWorks ![外部链接图标](images/external_link.svg "外部链接图标")](https://developer.ibm.com/blockchain/) 包含适用于开发者的资源和信息。
* **支持凭单**下的 [IBM dWAnswers ![外部链接图标](images/external_link.svg "外部链接图标")](https://developer.ibm.com/answers/smartspace/blockchain/) 充当问题和响应的平台。您可以搜索先前发布的问题的响应，也可以提交新问题。请确保在问题中包含关键字**区块链**。您还可以通过**开具 {{site.data.keyword.Bluemix_notm}} 支持凭单**选项，向 {{site.data.keyword.blockchainfull_notm}} 支持团队提交凭单。从特定 {{site.data.keyword.Bluemix_notm}} 实例共享详细信息和代码片段。
* **区块链样本应用程序**下的[样本应用程序 ![外部链接图标](images/external_link.svg "外部链接图标")](https://github.com/ibm-blockchain) 提供了指导信息和样本代码片段，以帮助开发应用程序。
* **Hyperledger Fabric** 下的 [Hyperledger Fabric ![外部链接图标](images/external_link.svg "外部链接图标")](http://hyperledger-fabric.readthedocs.io/) 和 [Hyperledger Fabric 社区 ![外部链接图标](images/external_link.svg "外部链接图标")](http://jira.hyperledger.org/secure/Dashboard.jspa) 提供有关 Hyperledger Fabric 堆栈的更多详细信息。请与 [Hyperledger 专家 ![外部链接图标](images/external_link.svg "外部链接图标")](https://chat.hyperledger.org/channel/general) 讨论有关 Hyperledger Fabric 代码的问题。

如果您无法调试问题或确定问题的答案，请在 IBM Cloud 服务门户网站中提交支持案例。有关更多信息，请参阅[获取支持](ibmblockchain_support.html)。

图 11 和 12 显示初始“获取帮助”屏幕，其中在“发行说明”选项卡中显示每个发行版的新增功能和已更改功能：

![Helios 发行说明](images/releasenotes_helios.png "Helios 发行说明")  
*图 11. Helios 发行说明*

![Fabric 发行说明](images/releasenotes_Fabric.png "Fabric 发行说明")  
*图 12. Fabric 发行说明*

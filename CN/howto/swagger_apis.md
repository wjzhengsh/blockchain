---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 使用 Swagger API 与网络进行交互


***[此页面是否有用？请告诉我们。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull_notm}} Platform 公开了使用 Swagger 生成的若干 REST API，可用于管理网络中的节点、通道、同级和成员。应用程序可以使用这些 API 来控制重要的网络资源，而无需使用“网络监视器”。

{:shortdesc}

开始之前，需要在 {{site.data.keyword.Bluemix_notm}} 上创建 [{{site.data.keyword.blockchain}} Platform 服务实例 ![外部链接图标](../images/external_link.svg "外部链接图标")](https://console.bluemix.net/catalog/services/blockchain)，然后创建或加入入门套餐<!--or Enterprise Plan -->区块链网络。


## 检索网络凭证

{: #retrieving-network-credentials}

进入区块链网络的“网络监视器”，然后在左侧导航器中打开“API”屏幕。您可以查看 REST API 的网络凭证。稍后将使用此处显示的“key”和“secret”的值对 API 进行授权，然后将“network_id”作为参数运行 API。单击**显示私钥**可显示 secret 字段的值。复制 key、secret 和 network_id 字段的值，稍后在 Swagger UI 中可以使用这些值。

**图 1** 显示“API”屏幕：
![API 屏幕](../images/API_screen_starter.png "API 屏幕")
*图 1. API*

如果使用的是入门套餐，可以在“网络监视器”中切换组织。对于入门套餐，缺省情况下配置了两个组织。要在每个组织的透视图中试用 REST API，切换组织会非常有用。要获取网络中其他组织的凭证，请单击“网络监视器”控制台右上角的用户名。在打开的菜单中，单击“组织”旁边的下拉箭头以查看所有组织。选择要切换到的组织并查看关联的网络凭证。

**图 2** 显示如何切换组织：

![切换组织](../images/switch_orgs_starter.gif "切换组织")  
*图 2. 切换组织*


## 授权 Swagger API

单击“API”屏幕上的 **Swagger UI** 链接以打开 Swagger UI。  
<!-- remove this line because the link is different depending on if you are starter or enterprise plan
You can also open the Swagger UI with the URL in the connection profiles. For example, `http://blockchain-swagger-dev.stage1.mybluemix.net`.
-->

在 Swagger UI 中，单击**授权**按钮，这将弹出授权窗口。在网络凭证中分别输入“key”和“secret”的值作为用户名和密码，然后单击**授权**，再单击**完成**。现在您已准备好运行这些 API。请注意，如果刷新浏览器，您需要使用凭证重新授权。

使用“基本授权认证”时，单击**授权**，再单击**完成**按钮后，将存储在“授权”窗口中指定的任何凭证，并且每个 REST API 调用上都会传递这些凭证。

**图 3** 显示授权 Swagger API 的过程：

![授权 API](../images/swaggerUIAuthorize.gif "授权 API")  
*图 3. 授权 API*


## 试用 API

单击要运行的 REST API，然后单击**试用**按钮。

**图 4**显示“Swagger UI”中的“试用”按钮：

![Swagger UI 中的“试用”按钮](../images/swaggerUITryItOut.png "Swagger UI 中的“试用”按钮")  
*图 4.“Swagger UI”中的“试用”按钮*

单击**试用**按钮后，可以输入必需参数来使用 API。可以在网络凭证中找到 `networkID`，并可在“网络监视器”中找到其他参数。输入参数后，单击**执行**以针对网络运行 REST API 调用。

**图 5** 显示“Swagger UI”中的参数：

![Swagger UI 中的参数](../images/swaggerUIParams.png "Swagger UI 中的参数")  
*图 5. 输入参数*  

单击**执行**后，可以看到针对网络发出的 API 调用的响应。还可以看到可直接从命令行调用该 API 的 CURL 命令。

**图 6** 显示 API 响应主体、URL 和 CURL 命令：

![Swagger UI 中的 API 响应](../images/swaggerUICurlResponse.png "Swagger UI 中的 API 响应")  
*图 6. API 响应*    

## 故障诊断技巧

### 401 未授权  
  确保您已通过提供网络凭证来授权 REST API。有关更多信息，请参阅[授权 Swagger API](#authorizing-swagger-apis)。

### 400 错误：请求错误
  某些 API 可能会在请求的“主体”中采用一个参数，用于充当过滤器来仅显示特定同级的结果。“主体”中提供了一个样本片段，如果使用该片段，那么需要对其进行编辑以指定要过滤的同级或同级列表。为了避免此错误，请编辑该片段以指定网络中的同级，或者完全除去该片段。

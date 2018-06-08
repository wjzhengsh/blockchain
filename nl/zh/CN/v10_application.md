---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-29"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 开发应用程序
{: #dev_app}
通过应用程序，您可以调用链代码，以在 {{site.data.keyword.blockchain}} 网络中查询或更新通道特定的分类帐。
{:shortdesc}

## 设置应用程序开发环境
您需要安装必备软件以开发可以与 {{site.data.keyword.Bluemix}} 上的 {{site.data.keyword.blockchain}} 网络交互的应用程序。
{:shortdesc}

*	Git（[Git 下载页面 ![外部链接图标](images/external_link.svg "外部链接图标")](https://git-scm.com/downloads){:new_window}）

	Git 是一种版本控制工具，使用该工具，您可以熟悉链代码开发和软件开发。此外，在 Windows 上随 git 一起安装的 git bash 是 Windows 命令提示符的绝佳替代方法。

*	GoLang（[GoLang 下载页面 ![外部链接图标](images/external_link.svg "外部链接图标")](https://golang.org/dl){:new_window}）

	GoLang 安装将安装一组 Go CLI 工具，这些工具对于编写链代码非常有用。例如，`go build` 命令允许在尝试将链代码部署到网络之前，先检查是否已实际编译链代码。

	遵循[安装指示信息 ![外部链接图标](images/external_link.svg "外部链接图标")](https://golang.org/doc/install){:new_window}以正确地设置环境变量。使用以下命令检查 `GOPATH`。请注意，您的 `GOPATH` 不需要匹配该示例，只需要将此变量设置为文件系统上的有效目录。

	```
	$ echo $GOPATH
C:\gopath
```

	然后，您可以通过使用 [hello world ![外部链接图标](images/external_link.svg "外部链接图标")](https://golang.org/doc/install#testing){:new_window} 示例来构建 GoLang 代码，从而验证 GoLang 安装。

* Java（[Java 下载页面 ![外部链接图标](images/external_link.svg "外部链接图标")](https://java.com/en/download/){:new_window}）。

*	Node.js（[Node.js 下载页面 ![外部链接图标](images/external_link.svg "外部链接图标")](https://nodejs.org/en/download/){:new_window}）。

  目前，支持使用 Node.js、Go 和 Java 开发链代码。

## 生成客户机端证书
我们不会深入探讨 x509 和公用密钥基础架构的细枝末节，因为这方面有大量的外部资源。只需了解 Fabric 中的通信流在每个接触点都使用签名/验证操作即可。因此，任何将调用（即分类帐查询或更新）发送到网络的客户机都需要对有效内容签名并附加正确签名的 x509 证书，以用于验证目的。专用密钥和签名证书以及 MSP 标识和认证中心 (CA) 根证书组成了称为“用户上下文”对象的内容。

此处最重要的是“注册”过程，在此过程中从相应的 CA 检索允许用于构成对象的密钥和证书。在构成用户上下文对象后，可从应用程序调用 API 以“set”或“get”此用户上下文。此时，应用程序（即客户机）将配备所有必要的工件，并且已准备好与网络通信。我们将查看用于检索密钥和证书的两种方法。

### 命令行
这是两种方法中较简单的做法。首先，按照指示信息来构建 [Fabric CA 客户机 ![外部链接图标](images/external_link.svg "外部链接图标")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html)。此步骤允许您与 CA 服务器通信，并接收格式正确的证书和密钥。

其次，根据使用的服务套餐从 IBM Cloud 下载 TLS 证书：
- [入门套餐的根 TLS 证书 ![外部链接图标](images/external_link.svg "外部链接图标")](https://blockchain-certs.mybluemix.net/us2.blockchain.ibm.com.cert)
- [企业套餐的根 TLS 证书 ![外部链接图标](images/external_link.svg "外部链接图标")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

将内容保存到文件夹，例如，``$HOME/tls``。此步骤允许在连线上加密数据流。

最后，从“网络监视器”中的**概述**屏幕打开**连接概要文件** JSON 文件，并找到相关变量：
* CA 的 URL：``certificateAuthorities`` 下的 ``url``
* 管理用户标识：``enrollId``
* 管理密码：``enrollSecret``
* CA 名称：``caName``

使用 Fabric CA 客户机，可以通过使用以下命令传入 TLS 证书路径和上述 4 个字符串，向“认证中心”发送“enroll”调用：
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

``fabric-ca-client`` 二进制文件位于 ``$GOPATH/bin`` 中，因此当您运行此命令时，您需要位于本地机器上的正确位置。实际调用可能类似于以下示例命令：
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```

在 `$HOME/.pfabric-ca-client/msp/signcerts/cert.pem` 中查找您的管理证书。然后，您可以从“网络监视器”将管理证书上传到区块链网络。有关添加证书的更多信息，请参阅“网络监视器”中[“成员”屏幕的“证书”选项卡](v10_dashboard.html#members)。

您还可以在以下目录中找到 CA 根证书和管理专用密钥：
* CA 根证书：`$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* 管理专用密钥：`$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
有几个 Fabric 存储库包含极佳的资源和脚本，可了解如何以编程方式与认证中心交互。``fabric-samples`` 存储库包含 ``balance transfer`` 示例，而 ``fabric-sdk-node`` 存储库具有一系列 CA 服务测试。如果打算在应用程序端发出注册请求，那么您需要完全了解需要在 ``fabric-ca-client`` 和 ``fabric-client`` 程序包中公开的 API。使用这些脚本和存储库作为构建应用程序的基线。

让我们快速回顾 `balance transfer` 示例中的几个关键片段：

首先，我们需要创建客户机对象，并设置键/值存储实例，在此实例中，将寄存我们的证书和密钥。我们可以使用简单的工厂方法 (``newCryptoSuite``) 执行此操作，此方法用于从 ``BaseClient`` 扩展到 ``Client`` 类。以下是快速查看代码：

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

常见做法是导出环境变量，该变量用于定义您计算机上的键/值路径，并将其传递到以上函数。现在，我们已经定义了 KVS，让我们使用来自 ``FabricCAServices`` 类的一些方法。此类是 Fabric CA 客户机的实现，因此它将允许我们与 CA 服务器通信。首先，我们需要向 CA 客户机（即 CA URL）传递一些信息：

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

接下来，我们实际发送对 CA 服务器的“enroll”调用。这将向客户机返回已包装在 x509 证书中并由目标 CA 签名的专用密钥和公用密钥：我们将之称为签名证书或注册证书。此注册证书或“eCert”是密钥部分，因为它允许网络实体验证来自客户机的事务处理和调用：

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

最终的任务是实际设置加密套件并构建用户上下文：

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

然后，您可以从“网络监视器”将管理证书上传到区块链网络。有关添加证书的更多信息，请参阅“网络监视器”中[“成员”屏幕的“证书”选项卡](v10_dashboard.html#members)。

## 开发应用程序
{: #developing-applications}

开始开发应用程序的一种简单方法是使用样本链代码和应用程序作为模板来创建自己的业务解决方案。您可以在[样本应用程序](howto/prebuilt_samples.html)中的 {{site.data.keyword.cloud_notm}} 上找到区块链网络的样本应用程序。您还可以根据自己的业务需求从头开始开发应用程序。 

您可以使用 JavaScript 或 Java 开发应用程序，并利用 Hyperledger Fabric Client SDK 中的可用 API 来启用应用程序和网络之间的交互。应用程序需要至少包含以下信息：
* 要调用的链代码的名称和版本。
* 网络资源的 API 端点信息，包括订货人、CA 和同级。
* 用于查询或更新网络中的分类帐的函数。如果需要高可用性，那么需要考虑应用程序中的节点故障转移。
<!--
### Using Fabric SDKs
{: #use-sdks}
Fabric offers Node.js SDK and Java SDK currently and will support more programming languages, such as Python, REST, or GO, in future releases. For more information about Fabric SDKs and how to use them, see the following documentation:
- [Hyperledger Fabric Node SDK documentation ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/){:new_window}
- [Hyperledger Fabric Java SDK documentation ![External link icon](images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-sdk-java){:new_window}
-->

### 在 Fabric SDK 中设置超时值
{: #set-timeout-in-sdk}

Fabric SDK 针对区块链网络中的事件在客户机应用程序中设置缺省超时值。请参阅以下有关 Fabric Java SDK 中缺省超时设置的示例。文件路径为 `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`。

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";
    
    ...
    
    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

但是，您可能需要更改自己应用程序中的缺省超时值。例如，在应用程序调用响应所需时间超过 5000 毫秒（Event Hub 连接的缺省超时值）的事务时，将收到失败错误，因为调用事件在 5000 毫秒时结束，而此时事务未完成。您可以设置系统属性以覆盖客户机应用程序的缺省值。因为在设置系统属性前初始化缺省值，所以系统属性可能不会生效。因此，需要在客户机应用程序的静态结构中设置超时系统属性。请参阅以下示例，以了解如何将 Fabric Java SDK 中 Event Hub 连接的超时值更改为 15000 毫秒。文件路径为 `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`。

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;
  
 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

## 向应用程序添加网络 API 端点
您需要将网络资源的 API 端点添加到应用程序，以便该应用程序可以与 {{site.data.keyword.Bluemix_short}} 上的 {{site.data.keyword.blockchain}} 网络进行交互。如果您在 {{site.data.keyword.Bluemix_short}} 上没有 {{site.data.keyword.blockchain}} 网络，那么可以使用入门套餐或企业套餐进行创建。有关更多信息，请参阅[管理入门套餐网络](get_start_starter_plan.html)和[管理企业套餐网络](get_start.html)。

您可以在网络的连接概要文件中找到 API 端点。连接概要文件采用 JSON 格式，并包含网络资源（包括排序者、CA 和同级节点）的 API 端点信息和注册标识/私钥。应用程序将通过这些 API 端点与同级和其他网络资源进行交互。

1. 使用下列其中一种方法从“网络监视器”中检索网络资源的 API 端点信息：
	* 要获取特定于链代码的 API 端点信息，请在运行该链代码的特定通道屏幕中找到该链代码，然后单击 **JSON** 按钮。
	![每个链代码的 API 端点](images/channel_chaincode.png "每个链代码的 API 端点")
	* 要获取有关所有网络资源的一组完整的 API 端点信息，请单击“概述”屏幕中的**连接概要文件**按钮。
	![“网络监视器”中的“连接概要文件”](images/service_credentials.png "“网络监视器”中的“连接概要文件”")

2. 找到网络资源的 API 端点信息，这类似于以下示例：
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```
	**注**：如果要以网络中的其他同级为目标，例如，您需要来自不属于贵组织的同级的支持，那么您需要获得那些同级的正确的 API 端点信息。您还需要为其他组织存储 CA 证书，以便验证返回到应用程序的响应。此信息不会在连接概要文件中公开，因此您必须联系 CLoud Foundry 组织的相应管理员，并在带外操作中获取此信息。订购服务 URL 在整个网络中很常用；对于此订购服务，您不需要任何成员特定信息。

3. 将 API 端点信息插入应用程序的配置文件，如以下示例中所示：
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## 使用 CouchDB 索引

如果网络使用 CouchDB 并且想要利用 CouchDB 索引功能（可提高 CouchDB 的性能），需要将索引与链代码打包在一起。

要了解有关 CouchDB 以及如何设置索引的更多信息，请参阅[有关 CouchDB 的 Fabric 文档 ![外部链接图标](images/external_link.svg "外部链接图标")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html)

## 托管应用程序
您可以在本地文件系统上托管应用程序，或将其推送到 {{site.data.keyword.Bluemix_notm}}。要将应用程序推送到 {{site.data.keyword.Bluemix_notm}}，请完成以下步骤：
1. 安装 [Cloud Foundry 命令行安装程序 ![外部链接图标](images/external_link.svg "外部链接图标")](https://github.com/cloudfoundry/cli/releases)。使用 `cf` 命令测试安装。
    * 如果安装成功，您应该在终端中看到一堆文本输出。
    * 如果您看到“找不到命令”，那么安装不成功，或者 CF 未添加到系统路径中。
2. 通过发出以下命令来设置 API 端点并使用 {{site.data.keyword.Bluemix_notm}} 标识和密码进行登录：
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
3. 浏览到应用程序的目录，并通过发出以下命令来推送应用程序。根据应用程序大小，这可能需要几分钟。您将在终端中看到 {{site.data.keyword.Bluemix_notm}} 日志；应用程序成功启动后，这些日志将停止。
	```
	> cf push YOUR_APP_NAME_HERE
	```
	您可以通过发出以下某个命令来检查应用程序日志：

	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## 断开应用程序与网络的连接
{: #disconnect}
完成以下步骤以除去应用程序与 {{site.data.keyword.Bluemix_short}} 上的 {{site.data.keyword.blockchain}} 网络之间的连接。
1. 从应用程序配置文件中除去 API 端点信息。有关参考信息，请参阅[将网络 API 端点添加到应用程序](#adding-network-api-endpoints-to-your-application)。
2. 删除链代码容器。
	1. 在“网络监视器”的“通道”屏幕中，找到已安装链代码的通道。
	2. 在特定通道屏幕中，找到要禁用的链代码。
	3. 单击**删除**按钮，然后单击链代码删除面板中的**提交**。此时将除去链代码容器。
	![删除链代码](images/channel_chaincode.png "删除链代码")

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

# 监视区块链网络

本教程显示如何在 {{site.data.keyword.cloud_short}} 上查看和监视 {{site.data.keyword.blockchain}} 网络的状态信息。
{:shortdesc}


## 监视同级、排序和 CA
{: #monitor-nodes}

可以针对某个网络节点发出 HTTP **HEAD** 请求，以检查该节点的状态。网络节点可以是区块链网络中的同级、排序者或 CA。**HEAD** 请求类似于 GET 请求，但仅发送不含主体的头。如果节点工作正常，您会获得 200 响应。

1. 在“网络监视器”的“概述”屏幕中，单击**连接概要文件**。然后，可以单击**原始 JSON** 以在 Web 浏览器中查看连接概要文件，或者单击**下载**以在本地保存连接概要文件。
2. 在连接概要文件中，找到要检查的网络节点的 URL 信息。例如，`fabric-orderer-20190b` 排序者的 URL 为 `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`。  
    ![排序者 URL 示例](../images/orderer_url.png "排序者 URL 示例")
3. 将 URL 中的 **grpcs** 替换为 **https**。以上示例中的 URL 会变成 `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`。
4. 使用 curl 或 Chrome Postman 应用程序等工具对该 URL 发出 **HEAD** 请求。
    - 如果获得 200 状态响应，说明网络节点工作正常。
    - 如果 **HEAD** 请求失败并返回连接错误，说明网络节点可能未在运行、节点 URL 不正确，或者防火墙阻止您访问该节点。您必须解决此错误；否则，应用程序无法连接该节点。

以下示例显示 curl 中的 **HEAD** 请求及 200 响应。请注意，您可以忽略 grpc 错误，因为 HTTP **HEAD** 请求会检查该节点是否可访问。如果可访问，那么对该节点的 grpc 请求在应用程序中也会正常工作。

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

以下示例显示 curl 中的 **HEAD** 请求及连接错误。

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

下图显示 Chrome Postman 应用程序中的 **HEAD** 请求及 200 响应。  

![Postman 中的 HEAD 请求示例](../images/orderer_head_postman.png "Postman 中的 HEAD 请求示例")


## 监视通道
{: #monitor-channnels}

进入“网络监视器”并在“通道”屏幕中查找要查看和监视的通道。在特定通道屏幕中，您可以在三个选项卡中查看此通道的数据状态信息、成员和已实例化的链代码：

* **通道概述**  
“通道概述”选项卡显示有关此通道的区块信息：
    * 一系列数据点，包括已创建的总块数、自上次事务处理以来的时间间隔、链代码实例化数和链代码调用数。
    * 列出此通道上所有块的表。展开区块，您可以查看有关该区块的详细信息。  

  ![通道概述](../images/channel_overview_detail.png "通道概述")  

* **成员**  
“成员”选项卡显示此通道上的成员的信息，包括组织操作员的电子邮件地址。![通道成员](../images/channel_members.png "通道成员")  

* **链代码**  
“链代码”选项卡列出在此通道上实例化的所有链代码，其中包括代码标识、版本和运行链代码的同级数。   

  展开链代码行，以获取有关链代码的详细信息：  
    * 您可以单击 **JSON** 以查看链代码的 JSON 文件。
    * 您可以单击**日志**以查看链代码日志。
    * 您可以单击**删除**以除去正在运行的链代码容器。**注**：删除运行中的链代码容器不会实际删除链代码。无法删除区块链网络上已实例化的链代码。

  ![通道链代码](../images/channel_chaincode.png "通道链代码")


## 监视链代码
{: #monitor-chaincode}

进入“网络监视器”并打开“安装代码”屏幕。如果您有正在运行的链代码，请从下拉列表中选择一个同级，然后可以在表中查看该同级的所有链代码以及链代码标识和版本。可以在此屏幕上为对链代码执行安装和实例化。有关更多信息，请参阅[安装、实例化和更新链代码](install_instantiate_chaincode.html)。

  ![链代码](../images/chaincode_install_overview.png "链代码")


## 监视样本应用程序
{: #monitor-apps}

在入门套餐网络中，可以在“网络监视器”的“试用样本”屏幕中查看和访问样本应用程序。部署样本应用程序后，可以单击**启动**按钮进入应用程序界面，或者单击**在 GitHub 上查看**链接以访问代码存储库。有关更多信息，请参阅[部署样本应用程序](prebuilt_samples.html)。

  ![样本应用程序](../images/sampleappflow0.png "样本应用程序")

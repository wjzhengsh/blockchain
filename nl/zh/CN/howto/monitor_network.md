---

copyright:
  years: 2017
lastupdated: "2017-07-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 监视 {{site.data.keyword.blockchain}} 网络

本教程显示如何在 {{site.data.keyword.Bluemix_short}} 上查看和监视 {{site.data.keyword.blockchain}} 网络的状态信息。
{:shortdesc}

进入“网络监视器”并在“通道”屏幕中查找要查看和监视的通道。在特定通道屏幕中，您可以在三个选项卡中查看此通道的数据状态信息、成员和已实例化的链代码：

* **通道概述**  
“通道概述”选项卡显示有关此通道的区块信息：
    * 一系列数据点，包括已创建的总块数、自上次事务处理以来的时间间隔、链代码实例化数和链代码调用数。
    * 列出此通道上所有块的表。展开区块，您可以查看有关该区块的详细信息。  

  ![通道概述](../images/channel_overview_detail.png "通道概述")  

* **成员**  
“成员”选项卡显示此通道上的成员的信息，包括组织操作员的电子邮件地址。![通道成员](../images/channel_members.png "通道成员")  
  
* **链代码**  
“链代码”选项卡将列出在此通道上实例化的所有链代码，其中包括代码标识、版本和运行链代码的同级数。   
    
  展开链代码行，以获取有关链代码的详细信息：  
    * 您可以单击 **JSON** 以查看链代码的 JSON 文件。
    * 您可以单击**日志**以查看链代码日志。
    * 您可以单击**删除**以除去正在运行的链代码容器。
  
  ![通道链代码](../images/channel_chaincode.png "通道链代码") 
  

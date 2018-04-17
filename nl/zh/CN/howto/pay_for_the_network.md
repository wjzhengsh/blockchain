---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 为网络付费

**注**：{{site.data.keyword.blockchainfull}} Platform 入门套餐 Beta 是免费的。

{{site.data.keyword.blockchainfull_notm}} Platform 按月收取成员资格费用和同级费用。根据您选择的网络套餐和使用的资源量，所付费用各不相同。以企业套餐为例，网络成员必须每月支付 1,000 美元的成员资格费用，并且每月为网络中的每个同级额外支付 1000 美元。每月费用按日分派计费。例如，如果一个成员（关联的成员资格费用为 1,000 美元）有两个同级（每个同级的费用 1,000 美元 X 2 个同级），该成员每月需要支付 3,000 美元。如果该月有 30 天，那么该成员每天支付 100 美元（3,000 美元/30）。

网络成员可以使用自己的 IBM Cloud 帐户（其中包含用于创建网络实例的空间）来支付帐单。或者，一个网络成员可以支付网络中所有成员的帐单，并为整个网络付费。


## 为自己付费
将按天向您的 {{site.data.keyword.cloud_notm}} 帐户收取成员资格费用和同级费用。


## 为整个网络付费
网络中的一个成员可以支付所有网络成员的费用。要以这种单一付款人的方式付款，付款人和其他网络成员必须完成以下步骤。

### 先决条件
每个网络成员都必须具有 {{site.data.keyword.cloud_notm}} 帐户。如果您没有此帐户，请进行[注册](https://console.bluemix.net/registration/)。

### 过程
1. 付款人在 {{site.data.keyword.cloud_notm}} 中为每个将受邀加入网络的用户创建单独的 Cloud Foundry 空间：
   1. 登录到 {{site.data.keyword.cloud_notm}}。
   2. 在菜单栏中，单击**管理** > **帐户** > **Cloud Foundry 组织**。
   3. 单击将在其中创建网络的 Cloud Foundry 组织的**查看详细信息**。如果您没有用于网络的组织，请单击**添加新的 Cloud Foundry 组织**按钮进行创建。
   4. 单击**添加 Cloud Foundry 空间**按钮，为一个网络成员创建一个空间。为空间选择区域，为其命名，然后单击**添加**。请注意，只有空间创建者才能访问创建的空间。
   5. 重复步骤 4 为所有将受邀加入网络的用户创建单独的空间。
2. 付款人邀请其他用户加入付款人的 {{site.data.keyword.cloud_notm}} 帐户，并向其授予对特定空间的访问权。每个用户只有权访问一个空间。
   1. 在菜单栏中，单击**管理** > **帐户** > **用户**。  
   2. 单击**邀请用户**，然后分配用户访问权：
      1. 指定将邀请加入 Cloud Foundry 组织的单个用户的电子邮件地址。
      2. 在 **Cloud Foundry 访问权**部分中，进行以下选择：
         - **组织**：要创建网络的 Cloud Foundry 组织。
         - **组织角色**：审计员。
         - **区域**：在其中为此用户创建空间的区域。
         - **空间**：为此用户创建的空间。
         - **空间角色**：开发者。
      3. 单击**邀请用户**。
   3. 重复相关步骤以邀请用户并为每个用户分配用户访问权。
3. 在 {{site.data.keyword.blockchainfull_notm}} Platform 仪表板中，付款人邀请用户加入区块链网络。有关邀请网络成员的更多信息，请参阅[成员](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members)。
4. 随后每个用户都会收到一封通知邮件，其中包含加入网络的邀请。用户可以完成以下步骤来加入网络。
   1. 单击通知电子邮件中的“继续”按钮，这会使您转至 {{site.data.keyword.cloud_notm}} 中的区块链服务页面。
   2. 确保使用的是正确的 {{site.data.keyword.cloud_notm}} 组织和空间。
      1. 登录到 {{site.data.keyword.cloud_notm}}，然后单击右上角的概要文件头像。
      2. 从**帐户**下拉列表中选择付款人的帐户。请注意，在其中供应区块链服务实例的帐户和组织将成为计费的对象。  
   4. 在区块链服务页面上，创建区块链服务实例：
      1. 在**服务名称**字段中，输入描述性名称供未来识别。
      2. 验证组织和空间是否为付款人邀请您加入的组织和空间。
      3. 选择要用于创建网络实例的成员资格套餐。
      4. 单击**创建**。
   5. 创建区块链服务实例后，遵循加入网络的向导。有关更多信息，请参阅[加入网络](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network)。

### 已知限制
- 由于所有成员均在付款人的 {{site.data.keyword.cloud_notm}} 帐户之内，因此付款人有权访问所有成员的区块链实例并可以对其进行模拟。因此，这种付费方式最好用于 POC 环境，或者用于付款人处理 {{site.data.keyword.blockchainfull_notm}} Platform 的所有管理工作，并且仅向成员提供一个应用程序的情况。  
- 在将所有成员添加到付款人的 {{site.data.keyword.cloud_notm}} 帐户，然后为其分配供应区块链实例并加入网络的访问权后，付款人还向成员授予了创建其他服务的访问权，而这可能会产生额外费用。  
- 付款人的 Cloud Foundry 组织中的任何成员都可以查看该组织中的所有空间。但是，他们无法编辑或修改组织，因为他们没有相应的访问权。

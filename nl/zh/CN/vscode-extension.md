---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 用于智能合同的工具
{: #overview}


***[此页面是否有用？请告诉我们。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform Visual Studio Code 扩展在 Visual Studio Code 中提供用于开发、打包和部署智能合同包的环境。该扩展还包括用于设置 Hyperledger Fabric 的预配置本地实例的命令，简化了本地智能合同开发。

**请注意：**{{site.data.keyword.blockchainfull_notm}} Platform 扩展与 {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐网络和 Hyperledger Fabric 1.3.x 兼容。

## 先决条件

安装 {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 扩展之前，请完成以下先决条件。

- **安装 Yeoman**

  Yeoman 是一种生成器工具，用于创建框架智能合同项目。使用以下命令安装 Yeoman：`npm install -g yo`

- **为 Yeoman 安装 Fabric 生成器**

  安装 Yeoman 之后，安装 Fabric 生成器以创建框架智能合同包。使用以下命令安装 Fabric 生成器：`npm install -g generator-fabric`

- **安装 Docker**

  要运行 Hyperledger Fabric 的预配置实例，请确保已安装 [Docker](https://www.docker.com/)。

- **操作系统需求**

  目前，此扩展与 Mac、Windows 和 Linux 兼容。

- **Hyperledger Fabric 版本需求**

  此扩展与 Hyperledger Fabric V1.3.0 和更高版本兼容。

- **Hyperledger Fabric 版本需求**

  此扩展与 Hyperledger Fabric V1.3.0 和更高版本兼容。


## 安装扩展

1. 浏览至 [Visual Studio Code 扩展市场页面](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform)或在 Visual Studio Code 内的“扩展”面板中搜索 **{{site.data.keyword.blockchainfull_notm}} Platform**。
2. 单击**安装**。
3. 重新启动 Visual Studio Code 以完成该扩展的安装。

## Visual Studio Code 命令选用板

此扩展向 Visual Studio Code 命令选用板添加了许多命令。本文档中详细描述的许多操作也可通过在 Visual Studio Code 命令选用板中运行相关命令来启动。

## 创建项目

要创建新项目，请完成以下步骤：

1. 打开命令选用板，运行**区块链：创建智能合同项目**命令。
2. 选择要用于创建智能合同的语言，当前选项为 JavaScript 或 TypeScript。
3. 使用项目名称创建文件夹，然后将其打开。
4. 选择打开新项目的方式。现在项目文件夹应该已打开。

## 连接到网络和断开网络连接

使用此扩展时，可在 Hyperledger Fabric 实例中的同级和通道上安装和实例化智能合同包。此扩展可以使用 Docker 初始化 Hyperledger Fabric 的预配置本地实例。

### 连接到预配置的 Hyperledger Fabric 实例

要连接到预配置的 Hyperledger Fabric 实例，请确保 Docker 在机器上运行：

1. 在 Visual Studio Code 中打开 _{{site.data.keyword.blockchainfull_notm}} Platform_ 选项卡。
2. 在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中，单击 **local_fabric**。如果 Docker 正在运行，那么应该会下载并启动本地 Hyperledger Fabric 实例。
3. 启动本地 Hyperledger Fabric 实例后，双击 **local_fabric** 以连接到该实例。现在，您应该会看到名为 `mychannel` 的通道。

#### 重新启动预配置的 Hyperledger Fabric 运行时

要重新启动 `local_fabric` 运行时，请执行以下操作：

1. 建立 `local_fabric` 连接后，右键单击该连接。
2. 选择**重新启动 Fabric 运行时**。

Hyperledger Fabric 容器将停止并重新启动。

#### 关闭 Hyperledger Fabric 运行时

要关闭 `local_fabric` 运行时，请执行以下操作：

1. 建立 `local_fabric` 连接后，右键单击该连接。
2. 选择**关闭 Fabric 运行时**。

执行 `local_fabric` 网络的关闭操作将关闭所有 Hyperledger Fabric 容器。**请注意**：这将导致分类帐和全局状态数据丢失。

#### 在预配置的 Hyperledger Fabric 运行时上启用开发方式

正常运行时，同级会创建并维护链代码容器，以用于运行实例化的智能合同。通过切换到开发方式，同级允许手动运行链代码容器。通过在命令行上或终端中手动运行链代码容器，可帮助迭代开发和调试智能合同。

要对预配置的 Hyperledger Fabric 运行时启用开发方式，请执行以下操作：

1. 建立 `local_fabric` 连接后，右键单击该连接。
2. 选择**切换开发方式**。

必须启用开发方式才能使用扩展的调试功能。

### 连接到 {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 扩展可用于连接到 {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐的实例。连接后，即可使用该扩展来开发和部署智能合同。

**注：**目前，该扩展与 {{site.data.keyword.blockchainfull_notm}} Platform 企业套餐不兼容。

1. 如果您没有 {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐的实例，请在[此处](https://console.bluemix.net/catalog/services/blockchain)进行创建。
2. 通过单击**启动**打开 IBP UI。
3. 通过单击**概述** > **连接概要文件** > **下载**，检索连接概要文件。
4. 通过单击**认证中心** > **生成证书**，并将证书和专用密钥保存到文件系统，生成所需的管理员证书。
5. 通过单击**成员** > **证书** > **添加证书**，然后选择在先前步骤中创建的证书，将证书添加到 {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐实例。
6. 在 Visual Studio Code 中，打开 {{site.data.keyword.blockchainfull_notm}} Platform 扩展视图，然后单击**添加新连接**。
7. 输入连接名称以及连接概要文件、证书和专用密钥的路径。

### 连接到您自己的 Hyperledger Fabric 实例

通过使用此扩展，可以连接到 Hyperledger Fabric 的预配置本地实例，也可以连接到您自己的 Hyperledger Fabric 实例。如果选择连接到您自己的 Fabric 实例，那么该实例必须是 Fabric 1.3.0 或更高版本。

要连接到您自己的 Hyperledger Fabric 实例，请完成以下步骤：

1. 在 Visual Studio Code 中打开 _{{site.data.keyword.blockchainfull_notm}} Platform_ 选项卡。
2. 在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中，单击**添加新连接**。
3. 输入连接的名称。此名称将显示在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中。
4. 输入 Hyperledger Fabric 连接概要文件的标准文件路径。
5. 输入与您身份相应的证书的标准文件路径。
6. 输入用于上一步中所提供证书的专用密钥的标准文件路径。
7. 您的连接应该会显示在 `local_fabric` 下的连接列表中。双击连接名称以进行连接。

如果您没有预先存在的 Hyperledger Fabric V1.3.0 或更高版本实例，那么可以使用预配置的 Hyperledger Fabric 实例进行连接，或运行以下命令来拉取并标记 V1.3.0 映像：

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### 断开网络连接

连接到网络时，可以通过单击“区块链连接”窗格右上角的“断开连接”图标来关闭连接。您还可以通过运行命令选用板中的**断开与区块链的连接**命令来断开连接。

## 编辑或删除连接

可以在“连接”窗格中编辑或删除连接。

### 编辑连接

通过编辑连接，可以更改连接概要文件的文件路径、为连接提供的名称以及用于识别证书和专用密钥的文件路径。

要编辑连接，请执行以下操作：

1. 在扩展中，右键单击左下方要编辑的连接，这将打开上下文菜单，其中包含用于添加身份、编辑连接或删除连接的选项。
2. 选择**编辑连接**。
3. 这将打开_用户设置_页面，其中突出显示了连接详细信息。
4. 进行任何更改，然后保存设置页面。

### 删除连接

可以通过以下方式删除连接：

1. 在扩展中，右键单击左下方要编辑的连接，这将打开上下文菜单，其中包含用于添加身份、编辑连接或删除连接的选项。
2. 选择**删除连接**。
3. 这将显示一个对话框，用于确认连接删除。单击**是**。

这将删除连接。

## 添加身份

添加连接时，必须提交连接概要文件、证书和专用密钥。与证书/专用密钥对关联的身份将设置为在连接到该 Hyperledger Fabric 实例时要使用的缺省身份。

要向已建立的连接添加身份，请执行以下操作：

1. 在扩展中，右键单击左下方要编辑的连接，这将打开上下文菜单，其中包含用于添加身份、编辑连接或删除连接的选项。
2. 选择**添加身份**。
3. 提供身份证书和专用密钥的文件路径。

## 探索连接

连接到 Hyperledger Fabric 实例后，可以在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中看到这些通道中的可用通道和同级的列表。请花点时间来探索 Hyperledger Fabric 实例，以熟悉结构。最先列出的是连接中的通道，通道下是属于该通道的同级的列表，以及该通道上实例化的智能合同包的列表。在列表中的每个同级下，是该同级上安装的所有智能合同包的列表。您还可以查看已安装或已实例化的智能合同包的版本。

### 导出预配置的 Hyperledger Fabric 运行时的连接详细信息

可以导出连接到 `local_fabric` 连接所需的连接详细信息。要测试旨在连接到 Hyperledger Fabric 实例或与 Hyperledger Fabric 实例进行交互的客户机应用程序，`local_fabric` 连接的连接详细信息非常有用。

要导出 `local_fabric` 连接详细信息，请执行以下操作：

1. 启动预配置的 Hyperledger Fabric 运行时。
2. 右键单击 `local_fabric` 连接，然后选择**导出连接详细信息**。

连接详细信息会保存到当前项目目录中所包含的名为 `local_fabric` 的目录中。

## 打包智能合同

智能合同准备好进行部署时，必须先将其打包。要打包智能合同，请完成以下步骤：

1. 在 Visual Studio Code 中，浏览至 _{{site.data.keyword.blockchainfull_notm}} Platform_ 面板。
2. 在_智能合同包_窗格中，单击 + 图标。如果在文件查看器中打开了一个智能合同项目，那么会对其自动打包，并且该项目会显示在_智能合同包_窗格中。如果打开了多个智能合同文件夹，那么会询问您要打包哪个文件夹。如果未打开任何智能合同文件夹，那么会收到错误消息。

## 安装智能合同包

连接到 Hyperledger Fabric 的实例后，可以在同级上安装和实例化智能合同包。

1. 在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中，浏览至要安装智能合同包的同级。
2. 右键单击要安装智能合同包的同级，然后选择**安装智能合同**。

## 实例化智能合同包

要在通道上开始运行安装的智能合同包，必须先将其实例化。

1. 连接到安装了智能合同包的 Hyperledger Fabric 实例。
2. 右键单击要在其中实例化智能合同包的通道，然后单击**实例化智能合同**。
3. 选择要实例化的智能合同包和版本。智能合同包必须安装在属于此通道的同级上。
4. 在智能合同中输入实例化函数的名称。
5. 输入实例化函数所需的任何自变量。

## 导出或删除智能合同包

打包了智能合同后，可以将其导出为 `.cds` 文件，或者如果不再需要，可以将其删除。

要删除智能合同包，请执行以下操作：

1. 在 {{site.data.keyword.blockchainfull_notm}} Platform 扩展面板中，右键单击要删除的智能合同包。
2. 选择**删除包**。

现在，将删除该包，并且该包不会显示在智能合同包列表中。

要导出智能合同包，请执行以下操作：

1. 在 {{site.data.keyword.blockchainfull_notm}} Platform 扩展面板中，右键单击要导出的智能合同包。
2. 选择**导出包**。
3. 选择用于保存智能合同包文件的目录，然后单击**导出**。

## 测试实例化的智能合同

在实例化了智能合同后，可以生成对智能合同的测试。这些测试可以生成为 JavaScript 或 TypeScript，并且可以运行或调试。

要生成智能合同测试，请执行以下操作：

1. 确保已对智能合同实例化。
2. 在**实例化的智能合同**下，右键单击要为其生成测试的智能合同。
3. 选择**生成智能合同测试**。
4. 现在，选择测试文件的语言（**JavaScript** 或 **TypeScript**）。{{site.data.keyword.blockchainfull_notm}} Platform 扩展现在将安装必需的 npm 模块并构建测试文件。

构建了测试文件后，可通过单击该文件中的**运行测试**按钮来运行测试。

## 使用预配置的 Hyperledger Fabric 运行时调试智能合同

通过调试智能合同，可以利用断点和输出来逐步运行智能合同事务，从而确保事务按预期进行。要调试智能合同，请执行以下操作：

1. 确保已连接到处于开发方式的 `local_fabric` 连接。
2. 打开智能合同项目。
3. 在 Visual Studio Code 中使用左侧导航栏打开调试视图。
4. 使用左上方的下拉列表来选择“调试智能合同”配置。
5. 通过单击**播放**按钮来打包并安装智能合同。
6. 通过单击智能合同文件中的相关行号，向智能合同添加断点。
7. 右键单击安装的智能合同，然后选择**实例化**。现在，可以右键单击以提交事务，并且将在定义的断点处暂停执行。

要在调试期间对智能合同进行更改，请在对智能合同进行更改后单击**重新启动**按钮。重新启动调试意味着您无需再次实例化合同。

**请注意**：重新启动调试会将智能合同存储在本地内存中，如果对大型智能合同进行了大量更改，那么可能需要重新实例化智能合同。

## 升级实例化的智能合同

智能合同在同级上安装并在通道上实例化后，可以将其升级以部署更新版本的智能合同。

1. 确保要升级的智能合同已实例化。
2. 将智能合同的新版本安装到同一网络上的同级。
3. 右键单击实例化的智能合同，然后选择**升级智能合同**。
4. （可选）选择在实例化新智能合同时要运行的事务。

## 提交事务

安装并实例化智能合同后，可以在 {{site.data.keyword.blockchainfull_notm}} Platform 扩展面板的“连接”窗格中提交事务。

要提交事务，请执行以下操作：

1. 确保已安装并实例化智能合同，并且已连接到网络。
2. 在“连接”窗格中，展开**实例化的智能合同**。
3. 展开包含要提交的事务的智能合同。
4. 右键单击要提交的事务，然后选择**提交事务**。
5. 输入事务需要的任何自变量，然后按 **Enter** 键。

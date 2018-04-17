---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 安装开发环境
{: #installing-a-development-environment}

按照以下指示信息来获取用于创建和测试业务网络的 {{site.data.keyword.blockchainfull}} Platform: Develop 开发工具。为了提供灵活性并支持最大数量的开发、测试和部署场景，开发工具以 npm 模块的形式提供，这些模块通过命令行进行控制。

## 开始之前

确保安装以下必备软件：

- Ubuntu Linux 14.04 / 16.04 LTS（均为 64 位）或 Mac OS 10.12
- Node V8.9 或更高版本（不支持 V9）
- npm V5.x
- git V2.9.x 或更高版本
- Python V2.7.x
- Docker Engine V17.03 或更高版本
- Docker-Compose V1.8 或更高版本
- 所选的代码编辑器，如 VSCode。

如果使用的是 VSCode，[此处](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client)提供了 Hyperledger Composer VSCode 扩展。


## 步骤 1：安装命令行工具

命令行工具包括 **composer-cli**（主 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 命令行模块）、**generator-hyperledger-composer**、**composer-rest-server** 和 **Yeoman**，这些模块用于生成业务网络、定制的 REST 服务器和 Angular 应用程序。

1. 使用以下命令来安装 **composer-cli**：

        npm install -g composer-cli@next

2. 使用以下命令来安装 **composer-rest-server**：

        npm install -g composer-rest-server@next

    使用 **composer-rest-server** 模块在机器上创建 REST 服务器，以将业务网络公开为 RESTful API。

3. 使用以下命令来安装 **generator-hyperledger-composer**：

        npm install -g generator-hyperledger-composer@next

    使用 **generator-hyperledger-composer** 生成框架业务网络结构、模型和 Angular 应用程序。

4. Yeoman 是一个用于生成应用程序的工具，它利用的是 `generator-hyperledger-composer`：

        npm install -g yo

## 步骤 2：安装本地 Playground

Hyperledger Composer Playground 是一个用户界面，可以连接到真正的 {{site.data.keyword.blockchain}}，或用作模拟环境来测试业务网络。使用以下命令来安装 Playground：

        npm install -g composer-playground@next

## 可选：设置 IDE

可以使用 Playground 来开发、编辑和测试业务网络。但是，如果要在 IDE 中开发业务网络，那么可以使用 VSCode 扩展，此扩展为 Hyperledger Composer 建模语言添加了语法突出显示功能。

1. 通过以下 URL 安装 VSCode：[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. 打开 VSCode，转至“扩展”，然后在市场中搜索并安装 Hyperledger Composer 扩展。

## 步骤 3：安装本地 Hyperledger Fabric

通过部署本地 Hyperledger Fabric 实例，可以充分测试访问控制规则和业务网络。

1. 使用以下命令来创建 `fabric-tools` 目录：

        mkdir ~/fabric-tools && cd ~/fabric-tools

在所选目录（例如 `~/fabric-tools`）中，获取包含要安装 Hyperledger Fabric 的工具的 `.tar.gz` 文件：

2. 下载包含工具的 `tar.gz` 文件，以在 Docker 环境中安装 Hyperledger Fabric。

        curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz

    另外还提供了 `.zip`。将上一个片段中的 `.tar.gz` 文件替换为 `fabric-dev-servers.zip`，并将 `tar -xvf` 命令替换为 `unzip` 命令。

3. 使用以下命令下载本地 Hyperledger Fabric V1.1 运行时：

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./downloadFabric.sh

## 步骤 4：启动 Hyperledger Fabric 实例

`fabric-tools` 目录包含一组用于控制已下载 Hyperledger Fabric 实例的脚本。您必须运行 `./createPeerAdminCard.sh` 命令来生成业务网络卡，您可以使用该卡将业务网络部署到托管 Hyperledger Fabric 实例的 Docker。

1. 运行以下命令来启动 Hyperledger Fabric V1.1 实例：

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./startFabric.sh
        ./createPeerAdminCard.sh

## 可选：使用 Playground 连接到 Hyperledger Fabric 实例

要开始开发业务网络，请使用 Hyperledger Composer Playground 或您的 IDE。

1. 要启动 Playground，请运行以下命令：

        composer-playground

    Playground 通过以下 URL 打开：http://localhost:8080/login。在上一步中创建的 **PeerAdmin@hlfv1** 卡可用于部署业务网络。


## 启动和停止 Hyperledger Fabric

在 `fabric-tools` 目录中有一组脚本用于控制 Hyperledger Fabric 实例。可以使用 `~/fabric-tools/stopFabric.sh` 和 `~/fabric-tools/startFabric.sh` 来停止和启动该运行时。

`~/fabric-tools/teardownFabric.sh` 将要求您在下次启动环境时创建新的 PeerAdmin 卡。

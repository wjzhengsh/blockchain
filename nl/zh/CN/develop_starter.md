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

# 在入门套餐上部署业务网络
{: #deploying-a-business-networks-on-starter-plan}

可以使用 {{site.data.keyword.blockchainfull}} Platform: Develop 开发者环境和 Hyperledger Composer 开发者工具集来开发业务网络，并将其部署到入门套餐环境。

通过使用开发者环境，可以快速对 {{site.data.keyword.blockchain}} 业务网络进行建模和测试，然后将其部署到 {{site.data.keyword.blockchainfull_notm}} Platform 的实例。

## 开始之前

确保您已阅读[关于入门套餐](./starter_plan.html)和[入门套餐入门](./get_start_starter_plan.html)。此外，请确保您已遵循[管理入门套餐网络](./get_start_starter_plan.html)中的指示信息，安装了 [{{site.data.keyword.blockchainfull_notm}} Platform: Develop 开发者环境](./develop_install.html)并创建了 {{site.data.keyword.blockchainfull_notm}} Platform 入门套餐实例。


## 步骤 1：检索管理私钥

1. 在入门套餐的“概述”屏幕中，单击**连接概要文件**。

2. 连接概要文件中有**管理私钥**属性。检索到该私钥并保存其副本。

## 步骤 2：创建证书授权卡

在上一步中检索到的私钥将用于为认证中心 (CA) 创建业务网络卡。这将导入 CA 卡，并且该卡用于将**管理私钥**交换为入门套餐认证中心的有效证书。

1. 使用第一步中记录的私钥，运行以下命令来创建 CA 业务网络卡：

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. 使用以下命令来导入该卡：

        composer card import -f ca.card -n ca

3. 现在，该卡已导入，可以用于将**管理私钥**交换为 CA 的有效证书。运行以下命令向认证中心请求证书：

        composer identity request --card ca --path ./credentials

`composer identity request` 命令会创建包含证书 `.pem` 文件的 `credentials` 目录。

## 步骤 3：向入门套餐实例添加证书

必须向入门套餐实例添加证书。为方便起见，可以使用 {{site.data.keyword.blockchainfull_notm}} Platform API 来添加证书。必须添加证书，随后必须停止并重新启动同级，这样才会同步证书。可以在入门套餐的“网络监视器”的**连接概要文件**中检索 API 调用变量中所需的数据。

1. 使用以下 API 调用来添加证书：

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    在上面的示例中，主体保留为空，以下代码是 API 请求主体的示例：

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. 使用以下 API 调用来停止同级：

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. 使用以下 API 调用来重新启动同级：

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. 使用以下 API 调用来同步证书：

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## 步骤 4：创建管理业务网络卡

现在，正确的证书已经与同级同步，因此可以创建有权安装 Hyperledger Composer 运行时并启动链代码的业务网络卡。

1. 使用以下命令来创建具有通道管理和同级管理角色的管理卡：

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    此卡用于将业务网络部署到入门套餐。

2. 使用以下命令导入在上一步中创建的卡：

        composer card import -f adminCard.card -n adminCard

## 步骤 5：安装并启动业务网络

接下来，可以使用上一步中创建的卡来安装并启动业务网络。对于本指南，我们将安装车辆制造网络样本。用于启动业务网络的命令也会创建一个卡。对于入门套餐，必须删除此卡；给定的示例命令将此卡命名为 `delete_me.card`，因此可以轻松认出该卡。

1. 使用以下命令来安装 Hyperledger Composer 运行时：

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. 使用以下命令来启动业务网络：

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. 删除名为 `delete_me.card` 的业务网络卡。

4. 使用以下命令来创建新的业务网络卡，并引用早先时候检索到的证书：

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. 使用以下命令来导入业务网络卡：

        composer card import -f ./admin@vehicle-manufacture-network.card

现在，业务网络已部署到入门套餐实例。

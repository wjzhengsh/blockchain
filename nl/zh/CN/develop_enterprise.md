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
{:tip: .tip}

# 在企业套餐上部署业务网络
{: #deploying-a-business-network}


***[此页面是否有用？请告诉我们。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop 的开发者工具可帮助您创建**业务网络定义**，然后可将其打包成业务网络归档 (`.bna`)。开发者环境支持您将 `.bna` 文件部署到本地或云 {{site.data.keyword.blockchain}} 以进行开发和共享。

本教程将讨论业务网络生命周期的下一步，即通过将 `.bna` 部署到 {{site.data.keyword.blockchainfull_notm}} Platform 企业套餐来激活业务网络。

## 开始之前

确保已安装 {{site.data.keyword.blockchainfull}}: Develop 开发者环境，并已熟悉开发和部署业务网络。[Hyperledger Composer 文档](https://hyperledger.github.io/composer/latest/business-network/business-network-index)中提供了有关编写业务网络的指导信息。

您需要访问 {{site.data.keyword.blockchainfull_notm}} Platform 的企业套餐实例。有关 {{site.data.keyword.blockchainfull_notm}} Platform 企业套餐的更多信息，请参阅[企业套餐概述](./enterprise_plan.html)。

## 步骤 1：为 {{site.data.keyword.blockchainfull_notm}} Platform 创建连接概要文件

1. 创建用于存储连接详细信息的目录，例如：

    ```
/Users/myUserId/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

    每个连接概要文件都应该包含 `connection.json` 文件。在 `.composer-connection-profiles` 下创建一个新目录，在本例中为 `bmx-hlfv1`。这将是您在使用 Hyperledger Composer 和 {{site.data.keyword.blockchainfull_notm}} Platform 时要使用的概要文件的名称。

    ```
mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
    cd ~/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

2. 现在，您应该具有以下目录结构：

    ```
/Users/myUserId/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

    在新创建的目录中创建名为 `connection.json` 的文件。可以将以下模板用于 `connection.json` 文件：

    ```
        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }
    ```
    {:codeblock}

    您将使用通过 {{site.data.keyword.blockchainfull_notm}} Platform 仪表板提供的属性来填充新创建的 `connection.json` 文件。在仪表板上，选择**概述**，然后选择**连接概要文件**按钮以显示通道成员的端点和证书信息。

## 步骤 2：添加排序者信息

1. 现在，我们可以开始使用“连接概要文件”提供的信息来修改模板。“连接概要文件”中可能有多个排序者，但 `connection.json` 文件只需要一个排序者。

    将模板中的排序者 URL 值替换为“连接概要文件”中的相关信息，格式如下：

    ```
"url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
    ```
    {:codeblock}

## 步骤 3：添加认证中心信息

1. 将 `connection.json` 中的 ca 值替换为 **certificateAuthorities** 部分中任一条目的 **url** 和 **caName**。

## 步骤 4：添加同级信息

1. 必须设置每个同级的 **requestURL** 和 **eventURL**。将 **url** 属性替换为在“连接概要文件”中找到的 **url** 值。将 **eventURL** 属性替换为在“连接概要文件”中找到的 **eventUrl**。进行更改后，`connection.json` 的 peers 部分应该具有以下格式：

    ```
"peers": [
          {
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
    ```
    {:codeblock}

## 步骤 5：添加 keyValStore 信息

1. 将 **keyValStore** 属性设置为指向相应的目录。创建要用于 **keyValStore** 的目录。例如，在主目录下创建名为 `.composer-credentials-mychannel` 的新目录。确保 **keyValStore** 属性指向新创建的目录，格式如下：

    ```
"keyValStore": "/Users/myUserId/.composer-credentials-mychannel",
    ```
    {:codeblock}

## 步骤 6：添加通道信息

1. 将 `connection.json` 中的 channel 值替换为计划创建业务网络并将其部署到的通道的名称。

## 步骤 7：添加 mspID

`connection.json` 文件中的 **mspID** 值应设置为您组织的 mspID。“连接概要文件”提供了组织及其关联 mspid 值的列表。应该使用组织的 **mspid** 属性值。

## 步骤 8：添加 globalCert
1. {{site.data.keyword.blockchainfull_notm}} Platform 对排序者和同级使用一个公共 TLS 证书。对于每个排序者和同级，都有 **tlsCACerts** 属性，这些属性都包含相同的证书。将 `connection.json` 文件中的哑元值替换为 **tlsCACerts** 值。该值应该采用以下格式：

    ```
"globalCert": "-----BEGIN CERTIFICATE-----\r\.......
    ```
    {:codeblock}

## 步骤 9：准备同级

**请注意**：**必须**在创建要将业务网络部署到的通道之前执行此步骤。如果在通道创建后执行此步骤，部署的业务网络**不会启动**。

在“连接概要文件”文档中，**certificateAuthorities** 下是 **registrar** 属性，包含 **enrollId** 和 **enrollSecret** 属性，格式如下：

 ```
"registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```
 {:codeblock}

1. 使用以下命令来请求证书：

    ```
composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12
    ```
    {:codeblock}

    这会将三个文件下载到主目录下的 `.identityCredentials` 目录中。相关的文件基于 **enrollId**。所以在上面的示例中会有两个文件，分别名为 **admin-pub.pem** 和 **admin-priv.pem**。

3. 从导航菜单中选择**成员**，然后选择**证书**菜单选项，并单击**添加证书**按钮。

4. 在**名称**字段中输入此证书的唯一名称，此名称不能包含破折号或连字符。

5. 打开早些时候创建的 `admin-pub.pem` 文件，将此文件的内容复制到 **Key** 字段中，然后按**提交**按钮。

6. 使用用户界面停止同级，然后启动同级。

7. 现在，该证书应该显示在证书列表中。

## 步骤 10：创建通道

1. 从左侧面板上的导航菜单中选择**通道**，然后单击**新建通道**按钮。

2. 输入“通道名称”和可选的描述，然后单击**下一步**。请注意，通道名称必须与您在连接概要文件中为 channel 属性指定的名称相匹配。

3. 根据需要授予许可权，然后单击**下一步**。

4. 选择需要接受通道更新并提交请求的操作员数量的策略。

5. 您现在应该转至**通知**部分，在其中应该有新的请求要复查。单击**复查请求**按钮。

6. 单击**接受**按钮后，您将重新转至**通知**部分，在其中可以看到现在可以提交该请求。单击**提交请求**按钮以打开提交对话框，然后单击**提交**按钮。新的通道已创建。

7. 从导航菜单中选择**通道**。新通道应该位于通道列表中，并且应该具有“尚未添加同级”。单击其旁边的“操作”菜单，并选择**加入同级**。选中要添加的同级旁边的复选框，然后按**添加所选项**。

## 步骤 11：导入新身份以管理业务网络

在 Composer 中，使用先前请求的证书创建身份。此新身份将有权将链代码安装到具有已上传公共证书的同级，并且这些同级将是认证中心的签发者。

1. 要创建新身份，请运行以下命令：

    ```
composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem
    ```
    {:codeblock}

    其中，`bmx-hlfv1` 是先前创建的概要文件的名称。现在我们已准备好将 `.bna` 文件部署到 {{site.data.keyword.blockchainfull_notm}} Platform。


## 步骤 12：部署业务网络

现在，您可以将 `.bna` 文件部署到 {{site.data.keyword.blockchainfull_notm}} Platform。

1. 使用上一步中创建的身份，通过以下命令部署业务网络：

   ```
   composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString
   ```
   {:codeblock}

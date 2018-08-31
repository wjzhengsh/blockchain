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
{:tip: .tip}

# 安裝開發環境
{: #installing-a-development-environment}

請遵循下列指示，以取得「{{site.data.keyword.blockchainfull}} 平台：開發」開發工具來建立及測試商業網路。為了賦予彈性，並啟用最大數量的開發、測試及部署實務範例，開發工具是以 npm 模組的形式提供，可從指令行進行控制。

需要一些元件的不同版本，才能與「入門範本方案」及「企業方案」搭配使用。若要搭配使用開發環境與「入門範本方案」，必須在 `0.19.x` 版安裝模組。若要搭配使用開發環境與「企業方案」，必須在 `0.16.x` 版安裝模組

## 開始之前

請確定您已安裝下列必備項目：

- Ubuntu Linux 14.04 / 16.04 LTS（兩者皆為 64 位元）或 Mac OS 10.12
- Node 8.9 版或以上版本（不支援第 9 版）
- npm 5.x 版
- git 2.9.x 版或以上版本
- Python 2.7.x 版
- 自選的程式碼編輯器，如 VSCode。

如果您使用「企業方案」，請安裝下列其他必要條件：

- Docker Engine 17.03 版或以上版本
- Docker-Compose 1.8 版或以上版本

如果您是使用 VSCode，[這裡](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client)有提供 Hyperledger Composer VSCode 延伸。

## 步驟 1：安裝指令行工具

指令行工具包括 **composer-cli**（這是主要「{{site.data.keyword.blockchainfull_notm}} 平台：開發」指令行模組）；**generator-hyperledger-composer**、**composer-rest-server** 及 **Yeoman**（這些模組可用來產生商業網路、定製的 REST 伺服器及 Angular 應用程式）。

1. 若要與「入門範本方案」搭配使用，請使用下列指令來安裝 **composer-cli**：

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

    若要與「企業方案」搭配使用，請使用下列指令來安裝 **composer-cli**：

    ```
    npm install -g composer-cli@0.16.x
    ```
    {:codeblock}

2. 若要與「入門範本方案」搭配使用，請使用下列指令來安裝 **composer-rest-server**：

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

    若要與「企業方案」搭配使用，請使用下列指令來安裝 **composer-rest-server**：

    ```
    npm install -g composer-rest-server@0.16.x
    ```
    {:codeblock}

    使用 **composer-rest-server** 模組，在您的機器上建立「REST 伺服器」，以將您的商業網路公開為 RESTful API。

3. 若要與「入門範本方案」搭配使用，請使用下列指令來安裝 **generator-hyperledger-composer**：

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    若要與「企業方案」搭配使用，請使用下列指令來安裝 **generator-hyperledger-composer**：

    ```
    npm install -g generator-hyperledger-composer@0.16.x
    ```
    {:codeblock}

    使用 **generator-hyperledger-composer** 來產生架構商業網路結構、模型及 Angular 應用程式。

4. Yeoman 是用來產生應用程式的工具，它會利用 `generator-hyperledger-composer`：

    ```
    npm install -g yo
    ```
    {:codeblock}

## 步驟 2：安裝本端遊樂場

Hyperledger Composer 遊樂場是使用者介面，可以連接至實際的 {{site.data.keyword.blockchain}}，或是用來作為測試商業網路的模擬環境。若要與「入門範本方案」搭配使用，請使用下列指令來安裝遊樂場：

```
npm install -g composer-playground@0.19.x
```
{:codeblock}


若要與「企業方案」搭配使用，請使用下列指令來安裝遊樂場：


```
npm install -g composer-playground@0.16.x
```
{:codeblock}

## 選用項目：設定您的 IDE

您可以使用遊樂場來開發、編輯及測試商業網路。然而，如果您想要在 IDE 中開發商業網路，有一個 VSCode 延伸可用來為 Hyperledger Composer 模型化語言新增語法強調顯示。

1. 從下列 URL 安裝 VSCode：[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. 開啟 VSCode，移至「延伸」，然後從市場搜尋並安裝 Hyperledger Composer 延伸。


## 僅限「企業方案」：安裝本端 Hyperledger Fabric，並使用「遊樂場」與其連接

藉由部署本端 Hyperledger Fabric 實例，您可以完整測試存取控制規則和商業網路。遵循下列步驟之前，請確定您已啟動 Docker。

1. 使用下列指令來建立 `fabric-tools` 目錄：

   ```
   mkdir ~/fabric-tools && cd ~/fabric-tools
   ```
   {:codeblock}

在您選擇的目錄中（例如 `~/fabric-tools`），取得包含 Hyperledger Fabric 安裝工具的 `.tar.gz` 檔案：

2. 下載 `tar.gz` 檔案，其中包含可在 Docker 環境中安裝 Hyperledger Fabric 的工具。

   ```
   curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
   tar -xvf fabric-dev-servers.tar.gz
   ```
   {:codeblock}

    另外，也可以使用 `.zip`。將上述 Snippet 中的 `.tar.gz` 檔案置換為 `fabric-dev-servers.zip`，並將 `tar -xvf` 指令置換為 `unzip` 指令即可。

3. 使用下列指令來下載本端 Hyperledger Fabric 第 1 版運行環境：

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./downloadFabric.sh
   ```
   {:codeblock}

   `fabric-tools` 目錄包含一組 Script，可控制所下載的 Hyperledger Fabric 實例。您必須執行 `./createPeerAdminCard.sh` 指令來產生商業網路卡，您可以用它來將商業網路部署至管理 Hyperledger Fabric 實例的 Docker。

4. 執行下列指令，以啟動 Hyperledger Fabric 第 1 版實例：

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./startFabric.sh
   ./createPeerAdminCard.sh
   ```
   {:codeblock}

   若要開始開發商業網路，請使用 Hyperledger Composer 遊樂場或您的 IDE。

5. 若要啟動遊樂場，請執行下列指令：

    ```
    composer-playground
    ```
    {:codeblock}

    遊樂場會在下列 URL 開啟：http://localhost:8080/login。在前一個步驟中建立的 **PeerAdmin@hlfv1** 卡可以用來部署商業網路。


### 啟動及停止 Hyperledger Fabric

`fabric-tools` 目錄中有一組 Script，可控制 Hyperledger Fabric 實例。您可以使用 `~/fabric-tools/stopFabric.sh` 和 `~/fabric-tools/startFabric.sh` 來停止及啟動運行環境。

在下次啟動環境時，`~/fabric-tools/teardownFabric.sh` 會需要您建立新的 PeerAdmin 卡。

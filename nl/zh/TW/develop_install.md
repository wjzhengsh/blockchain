---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 安裝商業網路開發環境
{: #installing-a-development-environment}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


請遵循下列指示，以取得 {{site.data.keyword.blockchainfull}} Platform: Develop 開發工具來建立及測試商業網路。為了賦予彈性，並啟用最大數量的開發、測試及部署實務範例，開發工具是以 npm 模組的形式提供，可從指令行進行控制。

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

## 步驟一：安裝指令行工具

指令行工具包括 **composer-cli**（這是主要的 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 指令行模組）；**generator-hyperledger-composer**、**composer-rest-server** 及 **Yeoman**（這些模組可用來產生商業網路、定製的 REST 伺服器及 Angular 應用程式）。您所安裝的版本視您「入門範本方案」或「企業方案」所執行的 Hyperledger Fabric 版本而定。您可以在「網路監視器」中開啟[網路喜好設定視窗](../v10_dashboard.html#network-preferences)來找到 Fabric 版本。

1. 若要搭配使用執行 Hyperledger Fabric 1.2.1 版的新「入門範本方案」實例，請使用下列指令來安裝 **composer-cli**：

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  若要搭配使用執行 Hyperledger Fabric 1.1 版的「企業方案」及「入門範本方案」實例，請使用下列指令來安裝 **composer-cli**：

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. 若要搭配使用執行 Hyperledger Fabric 1.2.1 版的新「入門範本方案」實例，請使用下列指令來安裝 **composer-rest-server**：

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  若要搭配使用執行 Hyperledger Fabric 1.1 版的「企業方案」及「入門範本方案」實例，請使用下列指令來安裝 **composer-rest-server**：

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 使用 **composer-rest-server** 模組，在您的機器上建立「REST 伺服器」，以將您的商業網路公開為 RESTful API。

3. 若要搭配使用執行 Hyperledger Fabric 1.2.1 版的新「入門範本方案」實例，請使用下列指令來安裝 **generator-hyperledger-composer**：

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  若要搭配使用執行 Hyperledger Fabric 1.1 版的「企業方案」及「入門範本方案」實例，請使用下列指令來安裝 **generator-hyperledger-composer**：

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    使用 **generator-hyperledger-composer** 來產生架構商業網路結構、模型及 Angular 應用程式。

4. Yeoman 是用來產生應用程式的工具，它會利用 `generator-hyperledger-composer`：

    ```
npm install -g yo
    ```
    {:codeblock}

## 步驟二：安裝本端遊樂場

Hyperledger Composer 遊樂場是使用者介面，可用來作為測試商業網路的模擬環境。請使用下列指令來安裝遊樂場：

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## 選用項目：設定您的 IDE

您可以使用遊樂場來開發、編輯及測試商業網路。然而，如果您想要在 IDE 中開發商業網路，有一個 VSCode 延伸可用來為 Hyperledger Composer 模型化語言新增語法強調顯示。

1. 從下列 URL 安裝 VSCode：[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. 開啟 VSCode，移至「延伸」，然後從市場搜尋並安裝 Hyperledger Composer 延伸。


### 啟動及停止 Hyperledger Fabric

`fabric-tools` 目錄中有一組 Script，可控制 Hyperledger Fabric 實例。您可以使用 `~/fabric-tools/stopFabric.sh` 和 `~/fabric-tools/startFabric.sh` 來停止及啟動運行環境。

在下次啟動環境時，`~/fabric-tools/teardownFabric.sh` 會需要您建立新的 PeerAdmin 卡。

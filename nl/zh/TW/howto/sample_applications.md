---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 範例應用程式

您可以部署並測試範例應用程式，以進一步瞭解 {{site.data.keyword.blockchainfull}} 網路和應用程式開發。
{:shortdesc}

## 開始之前

確定您已在本端檔案系統上安裝所有軟體必備項目。如需相關資訊，請參閱[設定應用程式開發環境](../v10_application.html#setting-up-application-development-environment)。

您在 {{site.data.keyword.Bluemix_short}} 上也需要有 {{site.data.keyword.blockchain}} 網路，並配置通道及其對等節點。如需相關資訊，請參閱[控管網路](../get_start.html)。在您的網路備妥可供使用之後，請擷取您的應用程式將要存取之網路資源的 API 端點。如需相關資訊，請參閱[將網路服務認證新增至應用程式](../v10_application.html#adding-network-service-credentials-to-your-application)。


## Marbles 應用程式

在 Marbles 應用程式中，多個使用者可以建立具有不同內容的 Marbles，並與其他 Marbles 一起轉移。Marbles 應用程式是以 JavaScript 撰寫，而鏈碼是以 Go 撰寫。

您可以在 [GitHub 中的 Marbles ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/IBM-Blockchain/marbles) 中找到範例程式碼和指示。


## Fabric Car 應用程式

在 Fabric Car 應用程式中，您可以針對分類帳中的汽車記錄執行**查詢**和**更新**。Fabric Car 應用程式是以 JavaScript 撰寫，而鏈碼是以 Go 撰寫。

您可以在 [GitHub 中的 Fabric Car ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) 中找到範例程式碼，並可在[撰寫您的第一個應用程式 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html) 中找到指示。

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

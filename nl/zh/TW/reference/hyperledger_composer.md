---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}

「{{site.data.keyword.blockchainfull}} 平台：開發」以開放程式碼 Hyperledger Composer 工具集為建置基礎，為 Linux Foundation 的 Hyperledger Project 中的專案之一。Hyperledger Composer 可讓架構師和開發人員使用對 Web 或行動應用程式公開商業邏輯的 REST API 快速建立 {{site.data.keyword.blockchain}} 解決方案，並可將區塊鏈與現有企業記錄系統整合。
{:shortdesc}

Hyperledger Composer 是由運行環境、指令行介面、REST 伺服器、LoopBack 連接器、遊樂場使用者介面、Yeoman 程式碼產生器，以及 VSCode 和 Atom 編輯器外掛程式組合而成。如需 Hyperledger Composer 的相關資訊，請參閱 [Hyperledger Composer 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)


## 商業網路

Hyperledger Composer 可讓您將現行商業網路快速模型化，其中包含您現有的資產及其相關交易；資產是有形或無形的商品、服務或財產。在您的商業網路模型中，您要定義可以與資產互動的交易。商業網路也包括與其互動的參與者，每個參與者可與跨多個商業網路的唯一身分相關聯。

如需建置商業網路的相關資訊，請參閱[開發商業網路](./develop.html)或 [Hyperledger Composer 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)。

## 查詢

查詢可用來傳回區塊鏈廣域狀態的相關資料。查詢定義在商業網路內，其中可包含簡單自訂作業的變數參數。利用查詢，可以從您的 {{site.data.keyword.blockchain}} 網路輕鬆擷取資料。查詢是藉由使用 [Hyperledger Composer API ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/latest/api/api-doc-index) 來傳送。

如需在商業網路中使用查詢的相關資訊，請參閱[查詢及過濾商業網路資料 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/business-network/query)。

## 身分和商業網路卡

在商業網路中，參與者會與身分相關聯。商業網路卡是身分、連線設定檔及 meta 資料的組合，可用來連接至商業網路，以及表明交易的身分。商業網路卡簡化了連接至商業網路的處理程序，並將商業網路之外的身分概念擴展到身分的「錢包」，每個「錢包」各與特定的商業網路和連線設定檔相關聯。

如需身分和商業網路卡的相關資訊，請參閱[參與者和身分 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/managing/participantsandidentities)。

## Hyperledger Composer REST 伺服器

Hyperledger Composer REST 伺服器會自動為商業網路產生 Swagger REST API。REST 伺服器是以 LoopBack 為基礎，並將商業網路模型轉換為 Open API 定義。在運行環境中，REST 伺服器會為資產和參與者實作「建立」、「讀取」、「更新」及「刪除」支援，並容許提交交易來進行處理或使用查詢來擷取交易。

如需 Hyperledger Composer REST 伺服器的相關資訊，請參閱[產生 REST API ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger.github.io/composer/integrating/getting-started-rest-api)。

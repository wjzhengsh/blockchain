---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 免責聲明
{: #disclainer}

**注意：**在使用任何 {{site.data.keyword.blockchainfull}} 方案之前，請務必檢閱下列資訊。

## IBM 支援聲明

IBM 在創新領域擁有歷史悠久的領導地位，現在更以 {{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull_notm}} 供應項目方案延續這項創新精神。{{site.data.keyword.blockchain}} 是一項快速發展的技術，預計將會為金融業、區域和廣域供應鏈，以及無數商業領域的物流支援帶來一陣紛亂。透過各種早期採用計劃，IBM 客戶及事業夥伴已主動將區塊鏈推進為產業解決方案。{{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} 就是一個這樣的計劃。**如同所有新技術一樣，{{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} 使用者應該注意快速及根本性變化的可能性**。  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} 背後的基礎架構是 Linux Foundation 的 Hyperledger Fabric 專案。每一個開放程式碼社群的貢獻都會改進 Hyperledger Fabric，但可能會有採用上的挑戰。**IBM 警告不要直接在任何 Hyperledger Fabric 區塊鏈網路上定義或交換金融資產或任何有價值的資產**。  

## 開放程式碼聲明

{{site.data.keyword.Bluemix_notm}} 上的 {{site.data.keyword.blockchainfull_notm}} 供應項目方案是以 Linux Foundation 的 Hyperledger Fabric 堆疊為建置基礎。Hyperledger Project 成員（包括 IBM）在 Hyperledger 的保護傘下，持續提出各種子專案。所有貢獻都會經過社群的檢閱、審查及測試。

## 鏈碼支援聲明

{{site.data.keyword.blockchainfull_notm}} 網路「不」支援下列編碼實務：

1. 搭配使用聯合陣列與反覆運算（順序在 Go 中已隨機化）。
2. 從 KVS 表格中讀取項目清單（不保證順序）。
3. 撰寫不安全執行緒鏈碼（可以並行呼叫查詢和呼叫）。
4. 以通用記憶體或快取儲存空間替換鏈碼中的分類帳狀態變數。
5. 直接從鏈碼中存取外部服務（例如，資料庫）。
6. 使用可能會引入非唯一性（例如使用 "random" 或 "time"）的程式庫或廣域變數。  

此外，不建議撰寫非唯一性鏈碼，因為這會造成資料一致性和完整性的風險。請注意，Hyperledger Fabric 架構的設計是要透過一系列背書和驗證檢查，來對抗非唯一性鏈碼，但還是非常鼓勵您編寫不依賴非靜態廣域變數（例如時間）的唯一性函數。  

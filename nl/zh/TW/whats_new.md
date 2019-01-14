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

# 新增功能
{: #whatsnew}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 2018 年 12 月 7 日

*操作入門範本方案網路* 及*操作企業方案網路* 主題已合併並取代為[使用網路監視器](v10_dashboard.html)的單一指導教學。

## 2018 年 11 月 27 日

{{site.data.keyword.blockchainfull}} Platform 會發行 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private (ICP)。ICP 是一種應用程式平台，用於開發及管理基於 Kubernetes 的容器化應用程式，其容許使用者在 x86、LinuxONE 及 IBM Z 上部署「憑證管理中心 (CA)」、排序節點及對等節點。{{site.data.keyword.blockchainfull_notm}} Platform for ICP 是以 Hyperledger Fabric 1.2.1 版為基礎，並使用 Kubernetes Helm 圖表進行部署。安裝 Helm 圖表之後，您可以在「ICP 型錄」中找到它作為組合服務。請注意，[此供應項目](ibp-for-icp-about.html)比較適合經驗豐富的 Fabric 使用者。

如需 ICP 的相關資訊，請參閱 [{{site.data.keyword.cloud_notm}} Private 3.1.0 版文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0 版文件")。

{{site.data.keyword.blockchainfull_notm}} Platform for ICP 的版本也會標示「IBM Blockchain Platform 遠端對等節點（測試版）」程式的結束。您仍然可以在 ICP 或 AWS 中部署對等節點，並將其連接至「入門範本方案」或「企業方案」網路。如需相關資訊，請參閱[針對入門範本或企業方案部署對等節點](howto/peer_deploy_ibp.html)及[在 AWS 中部署對等節點](howto/remote_peer_aws.html)。這些對等節點視為**分散式**對等節點，而非遠端對等節點，因為部署是由客戶所管理，所以沒有可成為其遠端的中央位置。

本版本還首次推出文件目錄的一些改進。如果您是「入門範本」或「企業」使用者，則仍然可以在**學習**、**如何**、**參照**及**說明**小節中找到您的內容，而且這些鏈結仍然相同。它可能只是位在目錄的不同子小節中。

## 2018 年 11 月 13 日

{{site.data.keyword.blockchainfull_notm}} Platform 會發行 {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS)。

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 可讓您在 AWS Cloud 中執行對等節點，並將對等節點連接至 {{site.data.keyword.cloud_notm}} 中的現有區塊鏈網路。AWS 中的對等節點可以利用連線設定檔及現有網路中的其他區塊鏈元件，同時可讓您更彈性地將對等節點與 {{site.data.keyword.cloud_notm}} 之外的其他應用程式並存。如需相關資訊，請參閱[關於 {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](howto/remote_peer.html)。

## 2018 年 10 月 4 日

{{site.data.keyword.blockchainfull_notm}} Platform 會將「入門範本方案」從 Hyperledger Fabric 1.1.0 版升級至 1.2.1 版。如需相關資訊，請參閱[關於入門範本方案](starter_plan.html)。

**重要事項：**Fabric 1.2.1 版目前與使用 1.1.0 版對等節點的「遠端對等節點」測試版不相容。「入門範本方案」網路（其在 2018 年 10 月 4 日之前建立，並以 Fabric 1.1.0 版為基礎）不受影響，而且仍然可以與「遠端對等節點」測試版搭配使用。{{site.data.keyword.blockchainfull_notm}} Platform 會將「遠端對等節點」測試版更新成使用 1.2.1 版對等節點（其與 Fabric 1.2.1 版層次的新「入門範本」網路及 Fabric 1.1.0 版層次的「企業」網路相容）。除非「遠端對等節點」測試版更新為 Fabric 1.2.1 版，否則請不要嘗試部署使用新 1.2.1 版「入門範本」網路的 1.1.0 版遠端對等節點。

## 2018 年 9 月 4 日

{{site.data.keyword.blockchainfull_notm}} Platform 會發行「遠端對等節點」供應項目測試版。「遠端對等節點」供應項目是以 Hyperledger Fabric 1.1.0 版為基礎。使用「遠端對等節點」，即可在自己的 IBM Cloud Private (ICP) 或 Amazon Web Services (AWS) Cloud 環境中執行 {{site.data.keyword.blockchainfull_notm}} Platform 對等節點。如需相關資訊，請參閱[關於遠端對等節點](howto/remote_peer.html)。

## 2018 年 6 月 15 日

{{site.data.keyword.blockchainfull_notm}} Platform 發行「入門範本方案 GA 版」。如需相關資訊，請參閱[關於入門範本方案](starter_plan.html)。

## 2018 年 5 月 15 日

{{site.data.keyword.blockchainfull_notm}} Platform 會將「企業方案」從 Hyperledger Fabric 1.0.0 版升級至 1.1.0 版。如需相關資訊，請參閱[關於企業方案](enterprise_plan.html)。

## 2018 年 3 月 18 日

{{site.data.keyword.blockchainfull_notm}} Platform 會發行「入門範本方案測試版」。「入門範本方案」提供開發及測試環境，以供您在 {{site.data.keyword.blockchainfull_notm}} Platform 網路中學習及練習。如需相關資訊，請參閱[關於入門範本方案](starter_plan.html)。

## 2017 年 8 月 11 日

{{site.data.keyword.blockchainfull_notm}} Platform 會取代 {{site.data.keyword.blockchainfull_notm}} on Cloud，並發行「企業方案」。如需相關資訊，請參閱[關於企業方案](enterprise_plan.html)。

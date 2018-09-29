---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於遠端對等節點
{: #remote-peer-overview}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


將「{{site.data.keyword.blockchainfull}} 平台」遠端對等節點連接至現有的區塊鏈網路之後，您可以在 {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> 上執行這些遠端對等節點。在 {{site.data.keyword.cloud_notm}} 外執行對等節點可提供更大的彈性來擴增或加入區塊鏈網路，同時又可利用 {{site.data.keyword.cloud_notm}} 內的現有網路。遠端對等節點會在平台上運用「憑證管理中心 (CA)」及「排序服務」，但容許您讓對等節點與 {{site.data.keyword.cloud_notm}} 外的其他應用程式並存。
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} Private (ICP) 是一種 Kubernetes 型平台，可在內部部署環境中建置專用雲端。您可以使用 ICP 來執行遠端對等節點，並將遠端對等節點連接至「{{site.data.keyword.blockchainfull_notm}} 平台」上的區塊鏈網路。{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP 會運用 ICP 的儲存空間、安全、記載及支援服務，以便您可以在內部部署環境中管理遠端對等節點。如需 ICP 的相關資訊，請參閱 [{{site.data.keyword.cloud_notm}} Private 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 文件")。  

**附註：**「{{site.data.keyword.blockchainfull_notm}} 平台遠端對等節點」目前是適合進行評估和實驗的測試版供應項目。**此測試版不是打算用於正式作業。** 如需存取和相關資訊，請參閱[授權與定價](#remote-peer-license-pricing)。

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

測試版支援 {{site.data.keyword.cloud_notm}} Private (ICP) 2.1.0.3 版的雲端平台。請注意，「{{site.data.keyword.blockchainfull_notm}} 平台」上的網路及 ICP 中的遠端對等節點必須在相同的 **Fabric 1.1 版**上執行。

## 考量
{: #remote-peer-limitations}

遠端對等節點無權存取完整功能，也不支援「{{site.data.keyword.blockchainfull_notm}} 平台」上管理的對等節點。在您執行遠端對等節點之前，請確定您瞭解下列限制：
- 在 {{site.data.keyword.cloud_notm}} 上，無法在區塊鏈網路的「網路監視器」中看到在其他雲端環境中執行的遠端對等節點。
- 在「網路監視器 使用者介面」中，無法使用「Swagger 使用者介面」來處理遠端對等節點。
- 您負責管理遠端對等節點的性能監視、安全、記載及資源使用。
- 您只能將遠端對等節點連接至 Hyperledger Fabric 1.1 版層次的區塊鏈網路。
- 遠端對等節點的資料庫類型必須符合區塊鏈網路的資料庫類型：LevelDB 或 CouchDB。
- CouchDB Fauxton 介面在遠端對等節點上無法使用。
- 目前不支援遠端對等節點的[聊天](../glossary.html#gossip)。


## 必要條件
{: #remote-peer-prereq}

若要使用遠端對等節點，您必須在「{{site.data.keyword.blockchainfull_notm}} 平台」上具有作為「入門範本方案」或「企業方案」網路成員的組織。遠端對等節點會運用「{{site.data.keyword.blockchainfull_notm}} 平台方案」網路的 API 端點、Hyperledger Fabric CA，以及「排序服務」來操作。如果您不是任何區塊鏈網路的成員，則需要建立或加入網路。如需相關資訊，請參閱[建立網路](../get_start.html#creating-a-network)或[加入網路](../get_start.html#joining-a-network)。


## 授權與定價
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->若要存取遠端對等節點以在 ICP 上執行，請參閱 [ICP 上遠端對等節點的授權與定價](remote_peer_icp.html#license-pricing-icp "ICP 上遠端對等節點的授權與定價")。測試版遠端對等節點的授權是免費的。<!--[AWS] for both platforms.-->之後，正式發行 (GA) 的供應項目會取代測試版。

不支援從測試版移轉至 GA。當 GA 供應項目發行時，您需要下載並安裝新的遠端對等節點，才能使用此 GA 供應項目。然後，您可以將新的遠端對等節點加入至與測試版遠端對等節點相同之網路的相同頻道。

**附註：**若要操作遠端對等節點，您必須在「{{site.data.keyword.blockchainfull_notm}} 平台」上具有屬於「入門範本方案」或「企業方案」網路的組織。這暗示，您或網路中的另一個成員必須為您的組織支付 IBM Blockchain [成員資格費用](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing)。如需支付費用的相關資訊，請參閱[付費模式](paying_mode.html)。  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## 部署遠端對等節點
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

下圖說明在 ICP 上部署「{{site.data.keyword.blockchainfull_notm}} 平台」遠端對等節點的步驟。如需如何在 ICP 上部署遠端對等節點的相關資訊，請參閱[在 {{site.data.keyword.cloud_notm}} Private 中部署遠端對等節點](remote_peer_icp.html "在 {{site.data.keyword.cloud_notm}} Private 中部署遠端對等節點")。

![ICP 上的遠端對等節點部署流程](../images/remote_peer_ICP_flow.png "ICP 上的遠端對等節點部署流程")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*圖 1. ICP 上的遠端對等節點部署流程*


## 操作遠端對等節點
{: #operate-remote-peer}

在部署遠端對等節點之後，您需要先完成數個作業步驟，然後對等節點才能將交易提交至網路。作業步驟包括將組織新增至頻道、將遠端對等節點加入至頻道、在遠端對等節點上安裝鏈碼、在頻道上實例化鏈碼，以及將應用程式連接至遠端對等節點。如需相關資訊，請參閱<!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[在 {{site.data.keyword.cloud_notm}} Private 中操作遠端對等節點](remote_peer_operate_icp.html#remote-peer-operate)。

## 資料常駐
{: #data-residency}

資料常駐需求是一些限制，可控管資料可能位於何處。資料常駐的最常見需求是與特定國家/地區內的法律相關聯，這些法律會強制要求在 IT 系統中處理及儲存的所有客戶資料，都必須保留在特定國家/地區的邊界內。同樣地，高度管制產業（如政府、醫療及金融服務）的某些公司需要其所有資料都必須完全儲存在防火牆後面。此外，某些公司具有公司原則，規定特定類型的資料（通常為「個人識別資訊」）必須保持專用，並完全受其公司防火牆保護。因此，若要實現資料常駐，區塊鏈網路的所有元件都必須是相同[頻道](../glossary.html#channel)的一部分，且位於單一國家/地區的邊界內。

為了因應資料常駐需求，瞭解作為「{{site.data.keyword.blockchainfull_notm}} 平台」基礎的 Hyperledger Fabric 架構很重要。此架構主要集中在三個重要元件：「排序服務」、「憑證管理中心 (CA)」及「對等節點」。對等節點會從排序服務中收到區塊形式的已排序狀態更新項目，並維護狀態和分類帳。因此，對等節點及排序服務具有直接關係。分類帳包含所有金鑰的最新值，以及交易日誌包括的資料。

此外，用戶端應用程式會使用 [Fabric SDK](../v10_application.html#using-the-fabric-sdks)，將交易傳送至對等節點和排序服務。這些交易包含[讀寫集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "讀寫集語意") 資料，其中包含分類帳上的金鑰值配對。

如果國內資料常駐是您公司的需求，則排序服務、對等節點和用戶端應用程式應該位於相同的國家/地區中。在 {{site.data.keyword.cloud_notm}} 中建立「{{site.data.keyword.blockchainfull_notm}}平台」網路時，您可以選擇網路的位置。<!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->如需地區及位置的相關資訊，請參閱 [{{site.data.keyword.blockchainfull_notm}} 平台地區及位置](../reference/ibp_regions.html)。若要在其中一個國家/地區實現資料常駐，您的遠端對等節點應該與「{{site.data.keyword.blockchainfull_notm}} 平台」網路位置位於相同的國家/地區。

如果「{{site.data.keyword.blockchainfull_notm}} 平台」網路包含跨越國家/地區邊界的遠端對等節點及排序服務，則您可以使用頻道，將資料隔離到網路上的對等節點子集。排序節點一律位於您已選取來管理網路的資料中心。排序節點無法跨越國家/地區邊界。但是，對等節點可以位於資料中心，或位於 {{site.data.keyword.cloud_notm}} 以外的遠端位置。因此，若要實現資料常駐，可以建立一個頻道，其中的排序節點及所有對等節點（資料中心的本端對等節點或遠端對等節點）都位於相同的國家/地區。如此一來，排序節點及對等節點共用的所有資料都會停留在單一國家/地區的邊界內。必要的話，其他頻道仍會存在，其中的排序節點與遠端對等節點所在位置跨越國家/地區邊界，而且不需要資料常駐。

未來，Hyperledger Fabric 中的新技術將使用[專用資料收集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "專用資料收集")，以及「零知識證明」，來改善實現進一步資料常駐的能力。

- 「專用」資料收集可確保專用資料是共用的對等式資料（透過聊天通訊協定），只有獲授權的對等節點才能看到它，例如，位於國家/地區邊界內的對等節點。資料儲存在對等節點上的專用資料庫中。這裡未涉及排序服務，因此看不到專用資料。該資料的雜湊會寫入至頻道上每個對等節點的分類帳。用於狀態驗證的雜湊充當交易的證明，並可用於審核目的。

- 「零知識證明 (ZKP)」容許「證明者」向「驗證者」保證他們瞭解機密資料，而不會洩露機密資料本身。此方式顯示您知道滿足聲明的內容，而不會顯示您知道的內容。

如需這些技術的相關資訊，請參閱下列白皮書：[Private and confidential transactions with Hyperledger Fabric ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Private and confidential transactions with Hyperledger Fabric")。

## 取得支援
{: #remote-peer-support}

「{{site.data.keyword.blockchainfull_notm}} 遠端對等節點」供應項目的測試版用於探索、開發及測試。**請不要將此版本用於正式作業。** 「{{site.data.keyword.blockchainfull_notm}} 平台」不提供此版本的支援。如果您遇到與遠端對等節點相關的任何問題，請參閱[區塊鏈資源及支援討論區](../v10_dashboard.html#support-forums)。您也可以在「網路監視器」的**取得協助**畫面上檢視支援資源。  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

對於與 {{site.data.keyword.cloud_notm}} Private 相關的問題，ICP 會提供 [免費的數位支援及 Community Edition 支援 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "ICP 免費的數位支援及 Community Edition 支援")。

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

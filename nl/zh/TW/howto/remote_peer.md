---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於 {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services
{: #remote-peer-overview}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**附註：**「{{site.data.keyword.blockchainfull}} Platform Remote Peer on ICP（測試版）方案」已結束。如果您仍要在 ICP 環境中執行對等節點，請改用 **{{site.data.keyword.blockchainfull_notm}} Platform for ICP** 供應項目。如需相關資訊，請參閱[關於 ICP 上的對等節點](../ibp-for-icp-about.html#ibp-icp-peer)。

在您將 {{site.data.keyword.blockchainfull_notm}} Platform 對等節點連接至 {{site.data.keyword.cloud_notm}} 中的現有區塊鏈網路之後，即可在 AWS Cloud 中執行該對等節點。在 {{site.data.keyword.cloud_notm}} 外執行對等節點可提供更大的彈性來擴增或加入區塊鏈網路，同時又可充分運用 {{site.data.keyword.cloud_notm}} 內的現有網路。AWS Cloud 中的對等節點會在平台上利用「憑證管理中心 (CA)」及排序服務，但容許您讓對等節點與 {{site.data.keyword.cloud_notm}} 之外的其他應用程式並置。
{:shortdesc}


## 考量
{: #remote-peer-limitations}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 無權存取完整功能，也不支援 {{site.data.keyword.blockchainfull_notm}} Platform 上管理的對等節點。在您執行 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 之前，請確定您瞭解下列限制：
- 在 {{site.data.keyword.cloud_notm}} 上，無法在區塊鏈網路的「網路監視器」中看到在其他雲端環境中執行的對等節點。
- 在「網路監視器」使用者介面中，無法使用 Swagger 使用者介面來處理 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 上執行的對等節點。
- 您要負責管理 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點的性能監視、安全、記載及資源使用。
- 您只能將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點連接至 Fabric 層次 1.1 或 1.2.1 版的區塊鏈網路。您可以在「網路監視器」中開啟[網路喜好設定視窗](../v10_dashboard.html#network-preferences)來找到 Fabric 版本。
- {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點的資料庫類型必須符合區塊鏈網路的資料庫類型：LevelDB 或 CouchDB。
- CouchDB Fauxton 介面在 AWS 對等節點上無法使用。
- 目前不支援 AWS 對等節點的[聊天](../glossary.html#gossip)。這暗示也不支援與聊天相依的 Fabric 特性（例如[專用資料 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "專用資料") 及[服務探索 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "服務探索")）。

## 必要條件
{: #remote-peer-prereq}

若要使用 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點，您必須在 {{site.data.keyword.blockchainfull_notm}} Platform 上具有屬於「入門範本方案」或「企業方案」網路成員的組織。{{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點會利用 {{site.data.keyword.blockchainfull_notm}} Platform 網路的 API 端點、Hyperledger Fabric CA 及排序服務來操作。如果您不是任何區塊鏈網路的成員，則需要建立或加入網路。如需相關資訊，請參閱[建立網路](../get_start.html#creating-a-network)或[加入網路](../get_start.html#joining-a-network)。

## 授權與定價
{: #remote-peer-license-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS 目前免費提供為 Community Edition；在未來，IBP for AWS 可能會變更為「自帶授權 (BYOL)」模型，這需要向 IBM 購買授權。

**附註：**若要操作 AWS 對等節點，您必須在 {{site.data.keyword.blockchainfull_notm}} Platform 上具有屬於「入門範本方案」或「企業方案」網路的組織。這暗示您或網路中的另一位成員必須為您的組織支付 {{site.data.keyword.blockchainfull_notm}} [成員資格費用](pricing.html#key-elements-of-pricing)。如需支付費用的相關資訊，請參閱[付費模式](paying_mode.html)。


## 部署 AWS 對等節點
{: #deploy-remote-peer}

請使用 AWS [快速入門範本 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/ "快速入門範本") 輕鬆地部署 {{site.data.keyword.blockchainfull_notm}} Platform for AWS。如需相關資訊，請參閱 [{{site.data.keyword.blockchainfull_notm}} Platform for AWS 快速入門部署手冊 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://s3.amazonaws.com/aws-quickstart/quickstart-ibm-fabric/doc/ibm-blockchain-platform-for-aws.pdf "IBM Blockchain Platform for AWS 快速入門參照部署")。

如需如何部署 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 的指示，請參閱[在 Amazon Web Services 中部署對等節點](remote_peer_aws.html "在 Amazon Web Services 中部署對等節點")。

下圖說明部署 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點的處理程序。

<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_AWS_flow.png" width="750" alt="按一下方框，以取得處理程序的其他詳細資料。" style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="配置或存取 AWS" title="配置或存取" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="建立金鑰組" title="建立金鑰組" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="remote_peer_aws.html#prerequisites-aws" alt="建立或加入網路" title="建立或加入網路" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="加入頻道" title="加入頻道" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="remote_peer_aws.html#aws-register-peer" alt="登錄對等節點身分" title="登錄對等節點身分" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="remote_peer_aws.html#aws-network-endpoints" alt="擷取對等節點配置資訊" title="擷取對等節點配置資訊" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="按一下鏈結" title="按一下鏈結" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="配置對等節點實例" title="配置對等節點實例" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-test" alt="驗證部署" title="驗證部署" shape="rect" coords="443.95, 258.43, 570.53, 333.48" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="使用 Fabric SDK" title="使用 Fabric SDK" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="remote_peer_operate_aws.html#aws-peer-cli-operate" alt="使用 Fabric 工具 CLI" title="使用 Fabric 工具 CLI" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*圖 1. AWS 上的 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署流程*


## 操作 AWS 對等節點
{: #operate-remote-peer}

在部署 AWS 對等節點之後，您需要先完成數個作業步驟，對等節點才能將交易提交至網路。作業步驟包括將組織新增至頻道、將對等節點加入頻道、在對等節點上安裝鏈碼、在頻道上實例化鏈碼，以及將應用程式連接至對等節點。如需相關資訊，請參閱[在 Amazon Web Services 中操作對等節點](remote_peer_operate_aws.html#remote-peer-operate-aws)。

## 資料常駐
{: #data-residency}

因為區塊鏈網路不在意所處理資料的類型，所以有時必須採取額外的步驟，以保持特定類型資料的安全。資料常駐最常見的需求是與特定國家/地區內的法律相關聯，這些法律會強制要求在 IT 系統中處理及儲存的所有資料，都必須保留在特定國家/地區的邊界內。同樣地，高度管制產業（如政府、醫療及金融服務）的某些公司需要資料必須完全儲存在其防火牆後面。因此，若要實現資料常駐，區塊鏈網路的所有元件都必須是相同[頻道](../glossary.html#channel)的一部分，且位於單一國家/地區的邊界內。

為了因應資料常駐需求，瞭解作為 {{site.data.keyword.blockchainfull_notm}} Platform 基礎的 Hyperledger Fabric 架構很重要。此架構主要集中在三個重要元件：「憑證管理中心 (CA)」、排序節點及對等節點。對等節點會從排序服務中收到區塊形式的已排序狀態更新項目，並維護狀態和分類帳。因此，對等節點及排序節點具有直接關係。分類帳包含所有金鑰的最新值，以及交易日誌包括的資料。

此外，用戶端應用程式會使用 [Fabric SDK](../v10_application.html#using-the-fabric-sdks)，將交易傳送至對等節點和排序服務。這些交易包括[讀寫集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "讀寫集語意") 資料，其中包含分類帳上的金鑰值配對。

如果國內資料常駐是您公司的需求，則排序節點、對等節點及用戶端應用程式必須位於相同的國家/地區中。在 {{site.data.keyword.cloud_notm}} 中建立「{{site.data.keyword.blockchainfull_notm}}平台」網路時，您可以選擇網路的位置。<!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->如需地區及位置的相關資訊，請參閱 [{{site.data.keyword.blockchainfull_notm}} Platform 地區及位置](../reference/ibp_regions.html)。若要在其中一個國家/地區實現資料常駐，您的對等節點應該與 {{site.data.keyword.blockchainfull_notm}} Platform 網路位置位於相同的國家/地區。

### 資料常駐的使用案例

請考量使用 {{site.data.keyword.blockchainfull_notm}} Platform 網路，其中包括「排序節點」和「憑證管理中心」以及四個組織的聯盟。組織有一個以上的對等節點。四個組織全部都屬於單一頻道，而網路的所有元件都位在 {{site.data.keyword.blockchainfull_notm}} Platform 網路部署所在的地區（例如法蘭克福）。最後，與對等節點互動的用戶端應用程式也位在德國內。將會維護資料常駐。  

![所有元件都位於相同國家/地區時的資料常駐](../images/remote_peer_data_res_1.png "所有元件都位於相同國家/地區時的資料常駐")  
*圖 3. 所有元件都位於相同國家/地區時的資料常駐*

現在，當**對等節點**加入其中一個組織時，請考量其含意。對等節點可以位於與網路其餘部分相同的地區，或位於 IBP 網路地區之外的任何位置：

-	如果對等節點位於與網路其餘部分相同的相同國家/地區，則會維護資料常駐。所有分類帳資料都會保留在德國，如同上述的**圖 3**。
-	如果對等節點碰巧位於不同的國家/地區（例如美國），則不再維護資料常駐，因為對等節點分類帳上的資料會在該國家/地區的邊界外共用。

若要解決此問題，可以使用**頻道**，將資料隔離到網路上的對等節點子集。{{site.data.keyword.blockchainfull_notm}} Platform 網路包含跨國家/地區邊界的對等節點及排序節點時，頻道會隔離分類帳資料與對等節點位在國家/地區邊界外的組織。  

**附註：**排序節點一律位於您已選取來管理網路的資料中心地區。擁有的多個排序節點無法跨越國家/地區邊界。不過，對等節點可以位於資料中心，或位於 {{site.data.keyword.cloud_notm}} 以外的遠端位置。

![對等節點位於 IBM Blockchain Platform 地區的國家/地區之外時的資料常駐](../images/remote_peer_data_res_2.png "「資料常駐」對等節點位於 IBM Blockchain Platform 地區的國家/地區之外")  
*圖 4.「資料常駐」對等節點位於 IBM Blockchain Platform 地區的國家/地區之外*

在**圖 4** 中，`OrgC` 及 `OrgD` 不需要資料常駐。事實上，`OrgD` 現在包括兩個對等節點：`OrgD-peer1` 及 `OrgD-peer2`（位於*美國*）。因此，為了讓 `OrgA`、`OrgB` 以及其位於德國的個別用戶端應用程式和對等節點與頻道 `X` 上的分類帳資料隔離，則會針對 `OrgC` 及 `OrgD` 建立新頻道 `Y`。

若要深入瞭解 {{site.data.keyword.blockchainfull_notm}} Platform 網路上的資料流程，請參閱[關於交易流程的 Fabric 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "交易流程")。

在日後，Hyperledger Fabric 中的新技術將使用[專用資料收集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "專用資料收集") 以及「零知識證明」，來改善實現進一步資料常駐的能力。

- 「專用」資料收集可確保專用資料是共用的對等式資料（透過聊天通訊協定），只有獲授權的對等節點才能看到它，例如，位於國家/地區邊界內的對等節點。資料儲存在對等節點上的專用資料庫中。這裡未涉及排序服務，因此看不到專用資料。該資料的雜湊會寫入至頻道上每個對等節點的分類帳。用於狀態驗證的雜湊作為交易的證明，並可用於審核目的。專用資料可用於 {{site.data.keyword.blockchainfull_notm}} Platform 中在 Fabric 1.2.1 版上執行的網路。不過，專用資料特性不適用於對等節點。

- 「零知識證明 (ZKP)」容許「證明者」向「驗證者」保證他們瞭解機密資料，而不會洩露機密資料本身。此方式顯示您知道滿足聲明的內容，而不會顯示您知道的內容。

在下列白皮書中，您可以取得這些技術的相關資訊：[Hyperledger Fabric 的專用及機密交易 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Hyperledger Fabric 的專用及機密交易")。

## 取得支援
{: #remote-peer-support}

IBM Blockchain Platform 不提供此供應項目的支援。如果您遇到與對等節點相關的問題，則可以利用免費的區塊鏈開發人員資源及支援討論區，以及從 {{site.data.keyword.IBM_notm}} 及 Fabric 社群取得協助。如需相關資訊，請參閱[區塊鏈資源及支援討論區](../ibmblockchain_support.html#resources)。您也可以在「網路監視器」的**取得協助**畫面上檢視支援資源。

- 若為與 AWS 相關的問題，您可以使用[社群支援討論區 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://forums.aws.amazon.com/index.jspa "AWS 社群支援討論區") 及 [AWS 超值支援 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://aws.amazon.com/premiumsupport/ "AWS 超值支援")。

{{site.data.keyword.blockchainfull_notm}} 不支援在 {{site.data.keyword.cloud_notm}} 中開啟且與 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 相關的案例。Community Edition 主要用於探索、開發及測試，因此請不要將它用於正式作業。

---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 關於 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #overview}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform 會發行 IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private (ICP)，此應用程式平台用於開發及管理基於 Kubernetes 的容器化應用程式，以容許使用者在 x86、LinuxONE 及 IBM Z 上部署「憑證管理中心 (CA)」、排序節點及對等節點。{{site.data.keyword.blockchainfull_notm}} Platform for ICP 是以 Hyperledger Fabric 1.2.1 版為基礎，並使用 Kubernetes Helm 圖表進行部署。
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 是一個組合產品，供 ICP 客戶在其本端環境中部署區塊鏈元件。匯入 Helm 圖表之後，您可以在「ICP 型錄」中找到它作為 {{site.data.keyword.blockchainfull_notm}} Platform 磚。如需 ICP 的相關資訊，請參閱 [{{site.data.keyword.cloud_notm}} Private 3.1.0 版 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0") 的文件。

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP 所提供的內容

{{site.data.keyword.blockchainfull_notm}} Platform for IBM Cloud Private (ICP) 可讓您部署 Hyperledger Fabric 區塊鏈的所有基礎元件：「憑證管理中心」、排序服務及對等節點。它可讓您根據商業需要來彈性部署不同的元件。您可以使用 {{site.data.keyword.blockchainfull_notm}} for ICP，透過部署及配置將組織一起連結至區塊鏈聯盟的排序服務，以啟動區塊鏈網路。您也可以部署對等節點，並加入使用 Fabric 型元件的其他網路，包括使用 ICP 跨雲端部署的 {{site.data.keyword.blockchainfull_notm}} Platform 網路，或 IBM Cloud 上所管理的「入門範本方案」及「企業方案」網路。如需 Hyperledger Fabric 網路構成要素的相關資訊，請參閱[區塊鏈元件概觀](blockchain_component_overview.html)。

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP 適合您嗎

在 {{site.data.keyword.cloud_notm}} 之外執行 {{site.data.keyword.blockchainfull_notm}} Platform 元件可讓您更彈性地擴展或加入區塊鏈網路。它可協助網路創辦者讓新成員加入來擴展其網路，同時使用其選擇的平台。它將容許對加入區塊鏈網路感興趣的組織將其對等節點與其現有應用程式並置，或與記錄系統整合。

**重要事項：**用於部署 {{site.data.keyword.blockchainfull_notm}} Platform for ICP 的處理程序十分複雜，並假定需要有 Fabric 的高階專業知識。如果您不熟悉 Fabric、ICP 或 {{site.data.keyword.blockchainfull_notm}} Platform，而且您的目標是設定開發環境或概念驗證，則請考量改為使用[入門範本方案](starter_plan.html)。另請注意，{{site.data.keyword.blockchainfull_notm}} Platform for ICP 中不支援每個潛在的部署配置。

本供應項目的使用者將管理自己的安全及基礎架構。{{site.data.keyword.cloud_notm}} 不提供這些服務。在您開始之前，請檢閱下節的[考量及限制](#ibp-icp-considerations)。

## 考量及限制
{: #ibp-icp-considerations}

開始使用之前，請確定您瞭解下列**考量**及**限制**：

- 您要負責性能監視、安全、記載及管理元件的資源用量。
- 在 {{site.data.keyword.cloud_notm}} 上執行之網路的「網路監視器」中，看不到其他雲端環境中執行的元件。
- Helm 圖表會部署排序節點、對等節點或 CA 的單一實例。
- 您可以將多個元件部署至 ICP 中的單一名稱空間，只要它們有不同的版本名稱即可。
- 在「網路監視器」使用者介面中，無法使用 Swagger 使用者介面來處理元件。
- 不支援相互 TLS。

**CA 考量**
- 此 Helm 圖表會部署 CA 的單一實例。因為每個組織有個別的 CA 視為最佳作法，所以可能需要部署數個 CA。例如，如果您計劃部署一個排序節點及三個對等節點，則需要至少兩個 CA（一個用於排序節點組織，另一個用於對等節點組織）。
- 雖然您可能選擇執行個別的 MySQL 資料庫，但此選項不存在於 Helm 圖表中。不過，Helm 圖表將在 CA 內部署 SQLite 資料庫來處理 CA 的資料庫必要項目，其中包括追蹤每位使用者的登記數目及任何已撤銷的憑證。

**排序節點考量**
- 排序服務與 Hyperledger Fabric 1.2 版的任何元件相容。
- 此 Helm 圖表會部署 SOLO 排序服務的單一實例（一個排序節點）。請注意，在頻道上無法部署多個 SOLO 排序節點，讓排序服務具有高可用性。這是為何認定 SOLO 排序服務用於開發（而非正式作業）環境的一個原因。不過，您可以將 SOLO 排序服務的多個實例部署到不同的網路（亦即，使用個別的聯盟）。

**對等節點考量**

- 您只能將對等節點連接至 Fabric 層次 1.1 或 1.2.1 版的區塊鏈網路。您可以在「網路監視器」中開啟[網路喜好設定視窗](../v10_dashboard.html#network-preferences)來找到 Hyperledger Fabric 版本。請遵循[指示](#starter-enterprise-network-endpoints)，以從「入門範本」或「企業」網路擷取對等節點連線資訊。
- 對等節點的資料庫類型必須符合區塊鏈網路的資料庫類型：LevelDB 或 CouchDB。
- CouchDB Fauxton 介面在對等節點上無法使用。
- 目前不支援對等節點的[聊天](../glossary.html#gossip)。這暗示也不支援與聊天相依的 Fabric 特性（例如[專用資料 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "專用資料") 及[服務探索 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "服務探索")）。

## 系統必要條件
{: #prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP 支援下列作業系統：
- Red Hat Enterprise Linux (RHEL) 7.3、7.4、7.5
- Ubuntu 18.04 LTS 及 16.04 LTS
- SUSE Linux Enterprise Server (SLES) 12 SP3

已使用下列工作者節點及後端儲存空間，驗證 {{site.data.keyword.blockchainfull_notm}} Platform for ICP Helm 圖表可在 Ubuntu Linux 的 {{site.data.keyword.cloud_notm}} Private 3.1.0 版叢集上執行：

- **LinuxONE 及 IBM Z**：使用 NFS 的 z/VM 及 KVM。
- **x86**：使用 GlusterFS 的 Linux 64 位元。

## 授權與定價
{: #ibp-icp-license-pricing}
{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 包含兩個版本：可從 Passport Advantage 下載的付費供應項目，以及可在 [GitHub ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/圖表") 中取得的免費 Community Edition。

### 授權
{: #ibp-icp-license}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private 提供在您自己的基礎架構上執行區塊鏈網路所需的元件，並部署為 ICP 應用程式。您可以存取 PPA 並[下載 Helm 圖表](howto/helm_install_icp.html)。購買授權中包含 {{site.data.keyword.blockchainfull_notm}} 的技術支援。

{{site.data.keyword.blockchainfull_notm}} Platform on IBM Cloud Private Community Edition 是適用於評估及實驗的免費供應項目，並部署為 ICP 應用程式。請不要將 Community Edition 用於正式作業。{{site.data.keyword.blockchainfull_notm}} Platform 未提供 Community Edition 的支援。您可以存取 [GitHub 套件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/圖表")，並下載 Helm 圖表。

### 定價
{: #ibp-icp-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform on ICP 的定價是根據所使用的「虛擬處理器核心 (VPC)」數目。VPC 可以是指派給虛擬伺服器的虛擬核心，或是未分割伺服器中的實體處理器核心。您必須取得可用於 {{site.data.keyword.blockchainfull_notm}} Platform 之每個 VPC 的授權。<!-- A VPC is a unit of measurement by which a program can be licensed.-->

如需如何判斷 VPC 用量的相關資訊，請參閱 IBM Knowledge Center 中這篇有關[「虛擬處理器核心 (VPC)」![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "虛擬處理器核心 (VPC)") 的文章。您可以使用 [IBM License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) 來配置及建立可用來判斷授權所需 VPC 數目的報告。


## 安裝 {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-icp-install}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private 提供為可在本端「ICP 叢集」中安裝的 Helm 圖表檔案。安裝 Helm 圖表之後，您可以在 ICP 型錄中找到 {{site.data.keyword.blockchainfull_notm}} Platform 作為應用程式。

- {{site.data.keyword.blockchainfull_notm}} Platform for ICP 是透過 Passport Advantage 提供。您需要具備必要授權才能存取 [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html)。購買時，其中包括 {{site.data.keyword.blockchainfull_notm}} Platform 的技術支援。

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition 主要用於探索、開發及測試。這個免費的 IBM Blockchain Platform for ICP 版本可透過 GitHub 存取。**附註：**{{site.data.keyword.blockchainfull_notm}} Platform 未提供 Community Edition 的支援。

如需如何安裝 Helm 圖表及必要條件的相關指示，請參閱[安裝 {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private](howto/helm_install_icp.html)。

如果您是 {{site.data.keyword.cloud_notm}} Private 的新使用者，而且需要安裝及部署 ICP 的相關資訊和提示，請參閱[設定 {{site.data.keyword.cloud_notm}} Private](ICP_setup.html)。

安裝 {{site.data.keyword.blockchainfull_notm}} Platform for ICP 之後，您需要個別部署網路的每個元件。您無法同時部署多個元件。開始之前，請造訪 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 部署手冊](ibp_for_icp_deployment_guide.html)，以瞭解創立或加入區塊鏈網路的最佳作法。然後，請檢閱下面各節中部署及操作個別元件的步驟。

### 安裝受防火牆保護的 {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-icp-firewall}

您可以部署受防火牆保護的 {{site.data.keyword.blockchainfull_notm}} Platform 元件，而不需要存取公用網際網路。下載的 Helm 圖表套件包括 {{site.data.keyword.blockchainfull_notm}} Platform 使用的所有 Fabric 元件 Docker 映像檔，而不需要在部署期間從 DockerHub 取回這些映像檔。

{{site.data.keyword.blockchainfull_notm}} Platform Community Edition Helm 圖表套件未包括必要的 Fabric 元件 Docker 映像檔。其配置為在部署期間從 DockerHub 下載這些映像檔，並且會因無法存取公用網際網路而失敗。不過，您可以採取一些額外的步驟來部署受防火牆保護的 Community Edition 元件。如需相關資訊，請參閱[安裝受防火牆保護的 Community Edition](howto/helm_install_icp.html#helm-install-prereqs-firewall)


## 關於 ICP 上的憑證管理中心
{: #ibp-icp-ca}

「憑證管理中心 (CA)」會提供網路上的身分。CA 可以視為與公證人類似，其可充當多方之間的信任錨點。系統會將封裝其數位身分的主要 CA 所簽署的憑證提供給網路中的所有實體。此憑證是信任網路上執行之所有簽署及驗證作業的根源。如需憑證管理中心及其在區塊鏈網路中所扮演角色的相關資訊，請參閱[區塊鏈元件概觀](blockchain_component_overview.html)。

CA 將驗證身分，並發出網路中您需要部署之其他元件的憑證。因此，在部署其他元件之前，您需要部署組織的 CA。

- 若要瞭解如何在安裝 Helm 圖表之後配置及部署 CA，請參閱[在 {{site.data.keyword.cloud_notm}} Private 中部署憑證管理中心](howto/CA_deploy_icp.html)。

- 若要瞭解如何使用 CA 來產生憑證，並完成部署其他元件的必要步驟，請參閱[在 {{site.data.keyword.cloud_notm}} Private 中操作憑證管理中心](howto/CA_operate.html)。

## 關於 ICP 上的排序節點
{: #ibp-icp-orderer}

排序節點可在區塊鏈網路中鑑別用戶端、排序交易及播送交易。它們是基於 Hyperledger Fabric 之區塊鏈網路的一般連結。因此，創立網路的組織需要先部署及啟動「排序服務」（執行排序的節點或節點集合），其他組織才能部署其對等節點、加入頻道，以及開始在網路上進行交易。如需排序節點及其在區塊鏈網路中所扮演角色的相關資訊，請參閱[區塊鏈元件概觀](blockchain_component_overview.html#orderer)。

如果您要創立區塊鏈網路，則需要部署一個排序節點。在部署之後，您可以邀請其他組織加入您的聯盟，然後建立它們自己的頻道。

- 若要瞭解如何在安裝 Helm 圖表之後配置及部署排序節點，請參閱[在 ICP 中部署排序節點](howto/orderer_deploy_icp.html)。

- 若要瞭解如何建置聯盟，請參閱[在 ICP 上操作排序節點](howto/orderer_operate.html)。

## 關於 ICP 上的對等節點
{: #ibp-icp-peer}

對等節點是網路的基本元素，因為它們可管理分類帳及智慧型合約。智慧型合約及分類帳分別用來封裝網路中的共用處理程序和共用資訊。如需對等節點及其在區塊鏈網路中所扮演角色的相關資訊，請參閱[區塊鏈元件概觀](blockchain_component_overview.html)。

- 當您準備好加入網路時，可以部署一個對等節點，以加入頻道、為交易背書，以及儲存頻道分類帳。如需在 ICP 上部署將連接至 ICP 上其他元件之對等節點的相關資訊，請參閱[在 ICP 上部署對等節點](howto/peer_deploy_icp.html)。如需在 ICP 上部署將連接至「入門範本方案」或「企業方案」網路之對等節點的相關資訊，請參閱[部署將連接至入門範本或企業的對等節點](howto/peer_deploy_ibp.html)。

- 設定對等節點之後，您需要先完成數個作業步驟，然後才能提交交易，並從區塊鏈網路讀取分散式分類帳。

  - 如果您要將對等節點連接至 ICP 上所部署的 {{site.data.keyword.blockchainfull_notm}} Platform，請參閱[使用多雲端網路在 {{site.data.keyword.cloud_notm}} Private 上操作對等節點](howto/peer_operate_icp.html)。
  - 如果您要將對等節點連接至 {{site.data.keyword.cloud_notm}} 上所部署的「入門範本方案」或「企業方案」網路，請參閱[使用入門範本方案或企業方案在 {{site.data.keyword.cloud_notm}} Private 上操作對等節點](howto/peer_operate_ibp.html)。

## 安全考量
{: #icp-security}

因為這些元件是在 {{site.data.keyword.cloud_notm}} 之外部署，所以您要負責管理其安全。這包括重要安全區域（例如金鑰管理及資料加密）。當您考量元件的安全時，請檢閱下列主題。

### 資料安全
{: #icp-security-data}

{{site.data.keyword.blockchainfull_notm}} Platform「入門範本方案」及「企業方案」會使用以[對稱金鑰加密 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "對稱加密法") 為基礎的整個磁碟加密，來保護網路使用的所有資料。您必須在自己的環境中採取類似步驟，才能保護您的對等節點資料。

您狀態資料庫中的資料（無論是使用 LevelDB 還是 CouchDB）都未加密。您可以使用應用程式層次加密來保護狀態資料庫中的靜態資料。

### 資料常駐
{: #data-residency}

資料常駐需求會強制要求仍在單一國家/地區的邊界內（或在某個其他定義的界限內）處理及儲存所有區塊鏈分類帳資料。如需如何實現資料常駐的相關資訊，請參閱[資料常駐](#icp-data-residency)。

### 金鑰管理
{: #icp-security-key-management}

金鑰管理是安全的重要層面。如果私密金鑰已受損或遺失，則惡意動作者或許能夠存取您的資料及功能。IBM 會使用稱為[硬體安全模組](../glossary.html#hsm) (HSM) 的實體應用裝置，來儲存 {{site.data.keyword.blockchainfull_notm}} Platform「企業方案」網路的私密金鑰。

當您在 ICP 上部署元件時，表示您要負責管理私密金鑰。雖然 {{site.data.keyword.blockchainfull_notm}} Platform 會產生私密金鑰，但這些金鑰不會儲存在 Platform 上。請務必安全地儲存您的金鑰，以免它們受損。您可以在對等節點 MSP 的 keystore 資料夾中，於元件的 `/mnt/crypto/peer/peer/msp/keystore/` 目錄下找到元件的私密金鑰。如需對等節點內憑證的相關資訊，請參閱[在 {{site.data.keyword.blockchainfull_notm}} Platform 上管理憑證](../certificates.html)指導教學的[成員資格服務提供者](../certificates.html#msp)一節。

您可以使用「金鑰委託」來回復遺失的私密金鑰。這需要在遺失任何金鑰之前先執行。如果無法回復私密金鑰，則您需要向「憑證管理中心」登錄新身分，才能取得新的私密金鑰。您也應該從任何加入的頻道中，移除和取代您的 signCert。

### TLS
{: #icp-security-tls}

[傳輸層安全 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL 或 TLS 信號交換的概觀") (TLS) 內嵌在 Hyperledger Fabric 的信任模型中。{{site.data.keyword.blockchainfull_notm}} Platform 上的所有「入門範本」及「企業」元件都會使用 TLS 來彼此鑑別及通訊。因此，Platform 上的網路元件需要能夠完成與對等節點的 TLS 信號交換。其連帶作用就是，您需要在用戶端應用程式到對等節點的防火牆中啟用透通，例如使用白名單。

<!--
You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](../v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.
-->

### 成員資格服務提供者配置
{: #icp-security-MSP}

{{site.data.keyword.blockchainfull_notm}} Platform 的元件透過「成員資格服務提供者 (MSP)」來取用身分。MSP 會讓 CA 發出的憑證與網路及頻道角色產生關聯。如需 MSP 如何使用對等節點的相關資訊，請參閱[成員資格服務提供者 (MSP)](../certificates.html#msp)。

### 應用程式安全
{: #icp-security-appl}

因為已簽署所有鏈碼呼叫，所以 Fabric 會管理應用程式安全。此外，Fabric 也會包括 ACL 型應用程式層次檢查。

## 資料常駐
{: #icp-data-residency}

因為區塊鏈網路不在意所處理資料的類型，所以有時必須採取額外的步驟，以保持特定類型資料的安全。資料常駐最常見的需求是與特定國家/地區內的法律相關聯，這些法律會強制要求在 IT 系統中處理及儲存的所有資料，都必須保留在特定國家/地區的邊界內。同樣地，高度管制產業（如政府、醫療及金融服務）的某些公司需要資料必須完全儲存在其防火牆後面。因此，若要實現資料常駐，區塊鏈網路的所有元件都必須是相同[頻道](glossary.html#channel)的一部分，且位於單一國家/地區的邊界內。

為了因應資料常駐需求，瞭解作為 {{site.data.keyword.blockchainfull_notm}} Platform 基礎的 Hyperledger Fabric 架構很重要。此架構主要集中在三個重要元件：排序服務（由排序節點構成）、「憑證管理中心 (CA)」及對等節點。對等節點會從排序服務中收到區塊形式的已排序狀態更新項目，並維護狀態和分類帳。因此，對等節點及排序服務具有直接關係。分類帳包含所有金鑰的最新值，以及交易日誌包括的資料。

此外，用戶端應用程式會使用 [Fabric SDK](v10_application.html#using-the-fabric-sdks)，將交易傳送至對等節點和排序服務。這些交易包括[讀寫集 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "讀寫集語意") 資料，其中包含分類帳上的金鑰值配對。

如果國內資料常駐是一項需求，則排序節點、對等節點及用戶端應用程式必須位於相同的國家/地區中。在 {{site.data.keyword.cloud_notm}} 中建立「{{site.data.keyword.blockchainfull_notm}}平台」網路時，您可以選擇網路的位置。<!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->如需地區及位置的相關資訊，請參閱 [{{site.data.keyword.blockchainfull_notm}} Platform 地區及位置](reference/ibp_regions.html)。

### 資料常駐的使用案例

請考量使用 {{site.data.keyword.blockchainfull_notm}} Platform 網路，其中包括排序節點和 CA 以及四個組織的聯盟。組織有一個以上的對等節點，且四個組織全部都屬於單一頻道，而網路的所有元件都位在 {{site.data.keyword.blockchainfull_notm}} Platform 網路部署所在的地區（例如法蘭克福）。最後，與對等節點互動的用戶端應用程式也位在德國內。

在此範例中，會維護資料常駐。

![所有元件都位於相同國家/地區時的資料常駐](images/remote_peer_data_res_1.png "所有元件都位於相同國家/地區時的資料常駐")
*圖 1. 所有元件都位於相同國家/地區時的資料常駐*

現在，當**分散式對等節點**加入其中一個組織時，請考量其含意。分散式對等節點可以位於與網路其餘部分相同的地區，或位於 {{site.data.keyword.blockchainfull_notm}} Platform 網路地區之外的任何位置：

-	如果對等節點位於與網路其餘部分相同的相同國家/地區，則會維護資料常駐。所有分類帳資料都會保留在德國，如同上述的**圖 1**。
-	如果對等節點碰巧位於不同的國家/地區（例如美國），則不再維護資料常駐，因為對等節點分類帳上的資料會在該國家/地區的邊界外共用。

若要解決此問題，可以使用**頻道**，將資料隔離到網路上的對等節點子集。{{site.data.keyword.blockchainfull_notm}} Platform 網路包含跨國家/地區邊界的一個分散式對等節點及多個排序節點時，頻道會隔離分類帳資料與對等節點位在國家/地區邊界外的組織。

**附註：**排序節點一律位於您已選取來管理網路的資料中心地區。擁有的多個排序節點無法跨越國家/地區邊界。不過，對等節點可以位於資料中心，或位於 {{site.data.keyword.cloud_notm}} 之外的遠端位置。

![對等節點位於 IBM Blockchain Platform 地區的國家/地區之外時的資料常駐](images/remote_peer_data_res_2.png "「資料常駐」對等節點位於 IBM Blockchain Platform 地區的國家/地區之外")
*圖 2. 「資料常駐」對等節點位於 IBM Blockchain Platform 地區的國家/地區之外*

在**圖 2** 中，`OrgC` 及 `OrgD` 不需要資料常駐。事實上，`OrgD` 現在包括兩個分散式對等節點：`OrgD-peer1` 及 `OrgD-peer2`（位於*美國*）。因此，為了讓 `OrgA`、`OrgB` 以及其位於德國的個別用戶端應用程式和對等節點與頻道 `X` 上的分類帳資料隔離，則會針對 `OrgC` 及 `OrgD` 建立新頻道 `Y`。

若要深入瞭解 {{site.data.keyword.blockchainfull_notm}} Platform 網路上的資料流程，請參閱[關於交易流程的 Fabric 文件 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "交易流程")。

在日後，Hyperledger Fabric 中的新技術將使用[專用資料收集 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "專用資料收集") 以及零知識證明，來改善實現進一步資料常駐的能力。

- 專用資料收集可確保專用資料是共用的對等式資料（透過聊天通訊協定），只有獲授權的對等節點才能看到它，例如，位於國家/地區邊界內的對等節點。資料儲存在對等節點上的專用資料庫中。這裡未涉及排序服務，因此看不到專用資料。該資料的雜湊會寫入至頻道上每個對等節點的分類帳。用於狀態驗證的雜湊作為交易的證明，並可用於審核目的。專用資料可用於 {{site.data.keyword.blockchainfull_notm}} Platform 中在 Fabric 1.2.1 版上執行的網路。不過，專用資料特性不適用於 ICP 中執行的對等節點，因為 ICP 網路目前未使用聊天。

- 零知識證明 (ZKP) 容許「證明者」向「驗證者」保證他們瞭解機密資料，而不會洩露機密資料本身。

在下列白皮書中，您可以取得這些技術的相關資訊：[Hyperledger Fabric 的專用及機密交易 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Hyperledger Fabric 的專用及機密交易")。

## 取得支援
{: #ibp-icp-support}

如需數位支援供應項目的相關資訊，請參閱 IBM Blockchain Platform 支援的[資源及支援討論區](ibmblockchain_support.html#resources)。

### {{site.data.keyword.blockchainfull_notm}} Platform for ICP

如果您已購買 IBM Blockchain Platform for ICP 授權，而且想要聯絡「客戶支援中心」，請參閱[存取 IBM Support Community 並開立支援問題單 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "{{site.data.keyword.blockchainfull_notm}} Platform for ICP 支援") 的相關資訊。

### {{site.data.keyword.blockchainfull_notm}} Platform Community Edition

Community Edition 主要用於探索、開發及測試。IBM Blockchain Platform 未提供 {{site.data.keyword.blockchainfull_notm}} Platform Community Edition 的支援。

如果您遇到與區塊鏈元件相關的問題，則可以使用免費的區塊鏈開發人員資源及支援討論區，以及從 IBM 和 Fabric 社群取得協助。如需相關資訊，請參閱[區塊鏈資源及支援討論區](ibmblockchain_support.html#resources)。

若為與 IBM Cloud Private 相關的問題，您可以利用 [ICP 所提供的免費數位支援及付費支援](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us)。

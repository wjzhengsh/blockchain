---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 區塊鏈元件概觀

{{site.data.keyword.blockchainfull}} Platform (IBP) 的元件和結構是以 [Hyperledger Fabric ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/) 的基礎架構和工具為基礎，這個開放程式碼、具有許可權之區塊鏈解決方案的主要貢獻者是 {{site.data.keyword.IBM_notm}}。基於 Fabric 的網路包括數個可部署在許多配置中的標準元件，以支援各種使用案例。

如果想要更全面檢視 Fabric 網路及其所構成元件的相互關係，請參閱 Fabric 社群文件中[關於區塊鏈網路結構的這份文件](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html)，其顯示如何啟動網路並使其完善。

如需基於 Fabric 之網路中元件的進階概觀，請觀看下面的視訊：

<iframe class="embed-responsive-item" id="youtubeplayer" title="入門範本方案視訊" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*雖然本視訊從「入門範本」和「企業」網路的觀點來看聚焦在元件上，但該資訊仍與 ICP (IBM Cloud Platform) 上 IBP 的客戶管理解決方案高度相關。*

基於此概觀的目的，我們只聚焦在憑證管理中心 (CA)、排序節點、對等節點、智慧型合約及應用程式。誠如您從 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 部署手冊](ibp_for_icp_deployment_guide.html)主題中所看到的，此順序非任意順序；它反映元件在基於 Fabric 之網路中的部署順序。

## 憑證管理中心
{: #ca}

基於 Fabric 之區塊鏈網路的基礎材料是身分和許可權。身分採用 CA 核發之 x.509 憑證的形式，類似於信用卡，因為它們會*識別* 某人，可包括有關這些人員的屬性。這些憑證可透過在元件或頻道層次併入 MSP 資料夾，而鏈結至許可權。因此，譬如對等節點 MSP 有一個稱為 **admins** 的 MSP 子資料夾。其憑證位於該管理者資料夾內的任何使用者，就是此對等節點的管理者，這表示他們可以執行該對等節點的管理者可以執行的任何動作。每當以其簽署憑證識別的使用者嘗試執行管理動作時，該對等節點內的驗證系統就會執行檢查。憑證是否符合「管理者」資料夾中的憑證？如果符合，則可以執行該動作。否則，將拒絕執行該動作的要求。

{{site.data.keyword.blockchainfull_notm}} CA 是基於 [Hyperledger Fabric-CA](https://hyperledger-fabric-ca.readthedocs.io/en/latest/)，但只要它使用基於 x.509 憑證的 PKI，就可以使用另一個 CA。CA 可以有（而且通常應該有）多個層級。網路的「主要 CA」通常不會公開，除非是要提供憑證給「中繼 CA」（它將直接發出憑證給使用者和元件，或發給更多層的中繼 CA）。如需如何使用憑證管理中心建立身分及成員資格的詳細資料，請參閱關於[身分 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) 及[成員資格 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html) 的 Hyperledger Fabric 文件

## 排序節點
{: #orderer}

雖然排序服務通常稱為網路的「核心」，但功能其實很簡單：將交易排序至區塊，然後傳回給對等節點以寫入其分類帳中。在舊版 Fabric 中，此功能已組合在對等節點內，但是從 Fabric 1.0 版開始，它已區隔成個別元件，用來增加對等節點效能，以及會導致潛在狀態分出的偏差。

在實體層次上，此排序功能通常需要一組排序節點，其統稱為「排序服務」，但在測試環境或 POC 環境中可以使用單一節點（所謂的 SOLO 排序節點）。

## 對等節點 (Peer)
{: #peer}

在實體層次上，區塊鏈網路主要是由對等節點（peer node，或簡稱為 peer）組成。對等節點是網路的基本元素，因為它們可管理分類帳及智慧型合約（其包含在「鏈碼」中）。更準確的說，對等節點可管理分類帳的**實例**，以及智慧型合約的**實例**。由於智慧型合約及分類帳分別用來將共用處理程序和共用資訊封裝在網路中，因此，對等節點的這幾方面使其成為瞭解 Fabric 網路實際功用的絕佳起點。

若要進一步瞭解對等節點，請參閱 Fabric 社群文件中[只聚焦在對等節點的這份文件](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html)。

## 智慧型合約

在企業可以彼此進行交易之前，必須對於規則和處理程序先達到共識，並將其定義在某種合約中。這些合約可結合起來構成「商業模式」，以控管事業夥伴之間的所有互動。

同樣的需求存在於區塊鏈網路中，這些商業模式的產業術語叫作「智慧型合約」，其中 Fabric（及 IBP）以所謂「鏈碼」的更大型結構來包含這些合約，其不僅包含商業邏輯，還包含基礎架構，用來驗證嘗試呼叫智慧型合約的使用者身分。

商業世界中的合約是由律師事務所簽署及存檔，智慧型合約則安裝在對等節點上，並在頻道上「實例化」。

## 應用程式

在 Fabric 型網路中的用戶端應用程式（例如 IBP）可運用基礎架構（例如 API、SDK 及智慧型合約），容許更高抽象層次的用戶端互動（呼叫及查詢）。

若要瞭解應用程式如何與基於 Fabric 的網路進行互動，請參閱關於[撰寫您的第一個應用程式 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "撰寫您的第一個應用程式") 及[開發應用程式區段 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "開發應用程式")的文件。

請注意，後面鏈結參照的 Fabric 功能（例如 `Contract` 類別）與 Fabric 1.2 版（這是 ICP 版本上的「入門範本方案」及 IBP 所依據的版本）不相容。不過，本節包含了概念性資訊及其他有用的提示，當您在開發與 Fabric 1.2 版元件互動的應用程式時，這些提示值得您牢記在心。

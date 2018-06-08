---

copyright:
  years: 2017, 2018
lastupdated: "2018-5-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 開發網路
{: #develop-the-network}

使用「{{site.data.keyword.blockchainfull}} 平台」來開發區塊鏈解決方案時，會運用 Hyperledger Composer 開放程式碼開發工具集。在您將任何項目部署至實際區塊鏈網路_之前_，Hyperledger Composer 會使用定製的模型化語言，結合 JavaScript 交易和存取控制規則來建立完整的區塊鏈商業網路模型。
{:shortdesc}

**附註**：「入門範本方案」提供範例應用程式，可讓您學習及嘗試。在開發自訂商業網路之前，您可以先在「入門範本方案」中執行範例應用程式。如需相關資訊，請參閱[關於入門範本方案](starter_plan.html)。

此指導教學完整說明 {{site.data.keyword.blockchain}} 解決方案的開發程序：從建立及模型化商業網路開始，然後將其部署至執行中的區塊鏈網路。

下列步驟概述在「{{site.data.keyword.blockchainfull_notm}} 平台」上管理之 {{site.data.keyword.blockchain}} 解決方案的基本開發程序。

1. 建立資料結構，以包含商業網路定義。商業網路是使用[模型化語言](https://hyperledger.github.io/composer/latest/reference/cto_language)，依據資產、參與者及交易來定義。

2. 定義資產類別、參與者類別、交易類別、事件類別，以及部署之後所要使用的存取控制規則，將商業網路模型化。

3. 將所定義的商業網路包裝在單一可部署檔案中。

4. 將所包裝的商業網路部署至「{{site.data.keyword.blockchainfull_notm}} 平台」。部署商業網路之後，可以產生 REST 伺服器，以容許與外部應用程式整合。

## 開始之前

確定您已安裝[「{{site.data.keyword.blockchainfull_notm}}：開發」開發工具](./develop_install.html)。

## 步驟 1：建立商業網路結構

開發 {{site.data.keyword.blockchain}} 解決方案的一個主要概念就是**商業網路定義 (BND)**。BND 可定義區塊鏈解決方案的資料模型、交易邏輯及存取控制規則。若要建立 BND，您需要在磁碟上建立適合的專案結構。

最簡單的開始方法就是使用 Yeoman 產生器來建立架構商業網路。Yeoman 產生器會建立一個包含商業網路所有元件的目錄，可依據您的特定使用案例加以修改。

1. 為您的專案建立一個新目錄，並在指令行上導覽至該目錄。

2. 從新的目錄中，使用 Yeoman 來建立架構商業網路。下列指令需要商業網路名稱、說明、作者名稱、作者電子郵件位址、授權選項及名稱空間：

        yo hyperledger-composer:businessnetwork

3. 在網路名稱部分輸入 `tutorial-network`，並且在說明、作者名稱、作者電子郵件部分輸入所需的資訊。

4. 選取 `Apache-2.0` 作為授權。

5. 選取 `org.acme.biznet` 作為名稱空間。

## 步驟 2：定義商業網路

商業網路是由資產、參與者、交易、存取控制規則，以及選用性的事件和查詢所構成。在步驟 1 中建立的架構商業網路包括一個模型檔 (`.cto`)，其中包含商業網路中所有資產、參與者及交易的類別定義。架構商業網路也包含具有基本存取控制規則的存取控制文件 (`permissions.acl`)、含有交易處理器函數的 Script 檔 (`logic.js`)，以及含有商業網路 meta 資料的套件檔 (`package.json`)。

### 將資產、參與者及交易模型化

所要更新的首份文件是模型檔 (`.cto`)。模型檔是使用 [Hyperledger Composer 模型化語言](https://hyperledger.github.io/composer/latest/reference/cto_language)撰寫而成。模型檔包含每個資產、交易、參與者及事件類別的定義。其隱含地延伸模型化語言文件中所說明的系統模型。

1. 使用您選擇的編輯器，開啟 `org.acme.biznet.cto` 模型檔。它位於您在最後一個步驟中建立之商業網路的 `models` 資料夾。

2. 以下列資訊來取代這些內容：

        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }

3. 儲存您對 `org.acme.biznet.cto` 檔案的變更。


### 新增 JavaScript 交易邏輯

在模型檔中，已定義 `Trade` 交易，其指定對資產和參與者的關係。交易處理器函數檔案包含 JavaScript 邏輯，以執行模型檔中定義的交易。

`Trade` 交易旨在單純接受所交易之 `Commodity` 資產的 ID，以及將 `Trader` 參與者的 ID 設為新的擁有者。

1. 開啟 `lib` 目錄中的 `logic.js` Script 檔。

2. 以下列資訊來取代這些內容：

        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }

3. 儲存您對 `logic.js` 的變更。

### 新增存取控制

存取控制是商業網路的主要部分。所有商業網路都必須有一個名為 `permissions.acl` 的存取控制檔案。只要在所部署的商業網路中呼叫任何處理程序，就會檢查存取控制清單，以確定進行呼叫的身分或參與者有權呼叫該作業。

針對此指導教學的目的，設定一個簡單的 ACL。請注意，此 ACL 不應部署至正式作業環境。

1. 在 `tutorial-network` 目錄中建立 `permissions.acl` 檔案。

2. 將下列存取控制規則新增至 `permissions.acl`：

        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }

3. 儲存您對 `permissions.acl` 的變更。

## 步驟 3：包裝商業網路

定義商業網路之後，必須將其包裝在可部署的商業網路保存檔 (`.bna`) 中。

1. 使用指令行，導覽至 `tutorial-network` 目錄。

2. 從 `tutorial-network` 目錄執行下列指令：

        composer archive create -t dir -n .

執行指令之後，`tutorial-network` 目錄中會建立一個商業網路保存檔 (`tutorial-network@0.0.1.bna`)。

## 步驟 4：部署商業網路及產生 REST 伺服器

使用[入門範本方案](./develop_starter.html)或[企業方案](./develop_enterprise.html)，將商業網路部署至「{{site.data.keyword.blockchainfull_notm}} 平台」。

商業網路部署至「{{site.data.keyword.blockchainfull_notm}} 平台」之後，會產生一個定製的 REST 伺服器。該 REST 伺服器會檢查商業網路的內容，並且為網路的資產、交易及參與者建置 RESTful API 呼叫。如需產生 REST 伺服器的指示，請參閱 [Hyperledger Composer 文件](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api)。

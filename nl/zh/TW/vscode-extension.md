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

# 智慧型合約的工具
{: #overview}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform Visual Studio Code 延伸提供 Visual Studio Code 內的環境，用於開發、包裝及部署智慧型合約套件。此延伸還包括一些指令，用來設定預先配置的本端 Hyperledger Fabric 實例，以簡化本端智慧型合約的開發。

**請注意：**{{site.data.keyword.blockchainfull_notm}} Platform 延伸與 {{site.data.keyword.blockchainfull_notm}} Platform「入門範本方案」網路及 Hyperledger Fabric 1.3.x 相容。

## 必要條件

安裝 {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 延伸之前，請完成下列必要條件。

- **安裝 Yeoman**

  Yeoman 是一種產生器工具，用來建立架構智慧型合約專案。請使用下列指令來安裝 Yeoman：`npm install -g yo`

- **安裝 Yeoman 的 Fabric 產生器**

  安裝 Yeoman 之後，請安裝 Fabric 產生器，以建立架構智慧型合約套件。請使用下列指令來安裝 Fabric 產生器：`npm install -g generator-fabric`

- **安裝 Docker**

  若要執行預先配置的 Hyperledger Fabric 實例，請確定您已安裝 [Docker](https://www.docker.com/)。

- **作業系統需求**

  目前，此延伸與 Mac、Windows 及 Linux 相容。

- **Hyperledger Fabric 版本需求**

  此延伸與 Hyperledger Fabric 1.3.0 版及更新版本相容。

- **Hyperledger Fabric 版本需求**

  此延伸與 Hyperledger Fabric 1.3.0 版及更新版本相容。


## 安裝延伸

1. 導覽至 [Visual Studio Code 延伸市場頁面](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform)，或在 Visual Studio Code 內的延伸畫面中搜尋 **{{site.data.keyword.blockchainfull_notm}} Platform**。
2. 按一下**安裝**。
3. 重新啟動 Visual Studio Code，以完成延伸的安裝。

## Visual Studio Code 指令選用區

此延伸會將許多指令新增至 Visual Studio Code 指令選用區。從 Visual Studio Code 指令選用區執行指令，也可以啟動本文件中詳述的許多作業。

## 建立專案

若要建立新專案，請完成下列步驟：

1. 開啟指令選用區，並執行**區塊鏈：建立智慧型合約專案**指令。
2. 選取您要用來建立智慧型合約的語言，目前的選項為 JavaScript 或 TypeScript。
3. 使用專案名稱建立資料夾，並開啟它。
4. 選取開啟新專案的方式。現在應該會開啟專案資料夾。

## 連接網路及中斷與網路的連線

當您使用此延伸時，可以在 Hyperledger Fabric 實例的對等節點與頻道上安裝及實例化智慧型合約套件。此延伸可以使用 Docker 來起始設定預先配置的本端 Hyperledger Fabric 實例。

### 連接至預先配置的 Hyperledger Fabric 實例

若要連接至預先配置的 Hyperledger Fabric 實例，請先確保 Docker 正在您的機器上執行：

1. 在 Visual Studio Code 中，開啟 _{{site.data.keyword.blockchainfull_notm}} Platform_ 標籤。
2. 在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中，按一下 **local_fabric**。如果 Docker 正在執行，應該下載及啟動本端 Hyperledger Fabric 實例。
3. 啟動本端 Hyperledger Fabric 實例之後，請按兩下 **local_fabric** 以與其連接。現在，您應該會看到一個稱為 `mychannel` 的頻道。

#### 重新啟動預先配置的 Hyperledger Fabric 運行環境

若要重新啟動 `local_fabric` 運行環境，請執行下列動作：

1. 用滑鼠右鍵按一下所建立的 `local_fabric` 連線。
2. 選取**重新啟動 Fabric 運行環境**。

將停止並重新啟動 Hyperledger Fabric 容器。

#### 關閉 Hyperledger Fabric 運行環境

若要關閉 `local_fabric` 運行環境，請執行下列動作：

1. 用滑鼠右鍵按一下所建立的 `local_fabric` 連線。
2. 選取**關閉 Fabric 運行環境**。

執行 `local_fabric` 網路的關閉動作將會關閉所有 Hyperledger Fabric 容器。**請注意**：這會導致分類帳及廣域狀態資料遺失。

#### 在預先配置的 Hyperledger Fabric 運行環境上啟用開發模式

在一般作業中，對等節點將建立並維護鏈碼容器，以執行已實例化的智慧型合約。透過切換至開發模式，對等節點容許手動執行鏈碼容器。在指令行或終端機中手動執行鏈碼容器，可協助進行智慧型合約的反覆運算式開發及除錯。

若要使用預先配置的 Hyperledger Fabric 運行環境來啟用開發模式，請執行下列動作：

1. 用滑鼠右鍵按一下所建立的 `local_fabric` 連線。
2. 選取**切換開發模式**。

必須啟用開發模式，才能使用延伸的除錯特性。

### 連接至 {{site.data.keyword.blockchainfull_notm}} Platform 入門範本方案

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 延伸可以用來連接至 {{site.data.keyword.blockchainfull_notm}} Platform「入門範本方案」實例。連接之後，此延伸可用來開發及部署智慧型合約。

**附註：**目前，此延伸與 {{site.data.keyword.blockchainfull_notm}} Platform「企業方案」不相容。

1. 如果您沒有 {{site.data.keyword.blockchainfull_notm}} Platform「入門範本方案」實例，請在[這裡](https://console.bluemix.net/catalog/services/blockchain)建立一個實例。
2. 按一下**啟動**，以開啟 IBP 使用者介面。
3. 按一下**概觀** > **連線設定檔** > **下載**，以擷取您的連線設定檔。
4. 按一下**憑證管理中心** > **產生憑證**，並將憑證及私密金鑰儲存至您的檔案系統，以產生必要的管理者憑證。
5. 按一下**成員** > **憑證** > **新增憑證**，並選取在上述步驟中建立的憑證，以將憑證新增至 {{site.data.keyword.blockchainfull_notm}} Platform「入門範本方案」實例。
6. 在 Visual Studio Code 中，開啟 {{site.data.keyword.blockchainfull_notm}} Platform 延伸視圖，然後按一下**新增連線**。
7. 輸入連線名稱，以及連線設定檔、憑證及私密金鑰的路徑。

### 連接至自己的 Hyperledger Fabric 實例

透過使用此延伸，您可以連接至預先配置的本端 Hyperledger Fabric 實例，也可以連接至自己的 Hyperledger Fabric 實例。如果您選擇連接至自己的 Fabric 實例，則它必須是 Fabric 1.3.0 或更新版本。

若要連接至自己的 Hyperledger Fabric 實例，請完成下列步驟：

1. 在 Visual Studio Code 中，開啟 _{{site.data.keyword.blockchainfull_notm}} Platform_ 標籤。
2. 在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中，按一下**新增連線**。
3. 輸入連線的名稱。此名稱將會顯示在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中。
4. 輸入 Hyperledger Fabric 連線設定檔的完整檔案路徑。
5. 為您的身分輸入憑證的完整檔案路徑。
6. 輸入私密金鑰的完整檔案路徑，以與上述步驟中所提供的憑證搭配使用。
7. 您的連線應該不會出現在 `local_fabric` 下的連線清單中。按兩下要連接的連線名稱。

如果您沒有 1.3.0 版或更新版本的現有 Hyperledger Fabric 實例，則可以使用預先配置的 Hyperledger Fabric 實例連接，或執行下列指令來取回並標記 1.3.0 版映像檔：

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### 中斷與網路的連線

連接至網路後，您可以按一下「區塊鏈連線」窗格右上方的中斷連線圖示，來關閉連線。您也可以從指令選用區中執行**中斷與區塊鏈的連線**指令，來中斷連線。

## 編輯或刪除連線

您可以在連線窗格中編輯或刪除連線。

### 編輯連線

透過編輯連線，您可以變更連線設定檔的檔案路徑、提供給連線的名稱，以及身分憑證和私密金鑰的檔案路徑。

若要編輯連線，請執行下列動作：

1. 從延伸中，用滑鼠右鍵按一下左下方要編輯的連線，這會開啟一個環境定義功能表，其中含有新增身分、編輯連線或刪除連線的選項。
2. 選取**編輯連線**。
3. 即會開啟_使用者設定_ 頁面，並強調顯示連線詳細資料。
4. 進行任何變更，並儲存設定頁面。

### 刪除連線

刪除連線的方式如下：

1. 從延伸中，用滑鼠右鍵按一下左下方要編輯的連線，這會開啟一個環境定義功能表，其中含有新增身分、編輯連線或刪除連線的選項。
2. 選取**刪除連線**。
3. 即會顯示一個對話框，以確認刪除連線。按一下**是**。

即會刪除連線。

## 新增身分

當您新增連線時，必須提交連線設定檔、憑證及私密金鑰。與憑證/私密金鑰組相關聯的身分將設為連接至該 Hyperledger Fabric 實例時所使用的預設身分。

若要將身分新增至已建立的連線，請執行下列動作：

1. 從延伸中，用滑鼠右鍵按一下左下方要編輯的連線，這會開啟一個環境定義功能表，其中含有新增身分、編輯連線或刪除連線的選項。
2. 選取**新增身分**。
3. 提供身分憑證及私密金鑰的檔案路徑。

## 探索連線

連接至 Hyperledger Fabric 實例之後，您可以在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中查看可用頻道及這些頻道中之對等節點的清單。請花一點時間探索 Hyperledger Fabric 實例，以熟悉結構。先列出的是連線內的頻道、頻道下方是屬於該頻道成員的對等節點清單，以及在該頻道上實例化的智慧型合約套件清單。清單中每個對等節點下方都是該對等節點上已安裝的所有智慧型合約套件清單。您也可以查看已安裝或實例化的智慧型合約套件版本。

### 匯出預先配置之 Hyperledger Fabric 運行環境的連線詳細資料

可以匯出連接至 `local_fabric` 連線所需的連線詳細資料。`local_fabric` 連線的連線詳細資料有助於測試想要連接至 Hyperledger Fabric 實例或與之互動的用戶端應用程式。

若要匯出 `local_fabric` 連線詳細資料，請執行下列動作：

1. 啟動預先配置的 Hyperledger Fabric 運行環境。
2. 用滑鼠右鍵按一下 `local_fabric` 連線，然後選取**匯出連線詳細資料**。

連線詳細資料會儲存至現行專案目錄中所包含且稱為 `local_fabric` 的目錄。

## 包裝智慧型合約

智慧型合約備妥可供部署時，必須先予以包裝。若要包裝智慧型合約，請完成下列步驟：

1. 在 Visual Studio Code 中，導覽至 _{{site.data.keyword.blockchainfull_notm}} Platform_ 畫面。
2. 在_智慧型合約套件_ 窗格中，按一下 + 圖示。如果您在檔案檢視器中開啟智慧型合約專案，則會自動加以包裝，並將其顯示在_智慧型合約套件_ 窗格中。如果您已開啟多個智慧型合約資料夾，則系統會詢問您要包裝哪個資料夾。如果您未開啟任何智慧型合約資料夾，則會出現錯誤訊息。

## 安裝智慧型合約套件

連接至 Hyperledger Fabric 實例之後，您可以在對等節點上安裝及實例化智慧型合約套件。

1. 在 _{{site.data.keyword.blockchainfull_notm}} Platform_ 窗格中，導覽至您要在其上安裝智慧型合約套件的對等節點。
2. 用滑鼠右鍵按一下要在其上安裝智慧型合約套件的對等節點，然後選取**安裝智慧型合約**。

## 實例化智慧型合約套件

若要開始在頻道上執行，必須先實例化已安裝的智慧型合約套件。

1. 連接至已安裝智慧型合約套件的 Hyperledger Fabric 實例。
2. 用滑鼠右鍵按一下您要在其上實例化智慧型合約套件的頻道，然後按一下**實例化智慧型合約**。
3. 選取要實例化的智慧型合約套件及版本。智慧型合約套件必須安裝在屬於此頻道成員的對等節點上。
4. 輸入智慧型合約中實例化函數的名稱。
5. 輸入實例化函數所需的任何引數。

## 匯出或刪除智慧型合約套件

智慧型合約在包裝之後，可以匯出為 `.cds` 檔案，或於不再需要時予以刪除。

若要刪除智慧型合約套件，請執行下列動作：

1. 在 {{site.data.keyword.blockchainfull_notm}} Platform 延伸畫面中，用滑鼠右鍵按一下要刪除的智慧型合約套件。
2. 選取**刪除套件**。

現在即會刪除套件，而該套件就會從智慧型合約套件清單中消失。

若要匯出智慧型合約套件，請執行下列動作：

1. 在 {{site.data.keyword.blockchainfull_notm}} Platform 延伸畫面中，用滑鼠右鍵按一下要匯出的智慧型合約套件。
2. 選取**匯出套件**。
3. 選擇目錄以儲存智慧型合約套件檔案，然後按一下**匯出**。

## 測試已實例化的智慧型合約

實例化智慧型合約之後，可以產生智慧型合約的測試。您能夠以 JavaScript 或 TypeScript 產生測試，並執行測試或對其進行除錯。

若要產生智慧型合約測試，請執行下列動作：

1. 確定已實例化智慧型合約。
2. 在**已實例化的智慧型合約**下方，用滑鼠右鍵按一下要產生測試的智慧型合約。
3. 選取**產生智慧型合約測試**。
4. 現在，請選取測試檔案的語言（**JavaScript** 或 **TypeScript**）。{{site.data.keyword.blockchainfull_notm}} Platform 延伸現在即會安裝必要的 npm 模組，並建置測試檔案。

建置測試檔案之後，按一下檔案中的**執行測試**按鈕，即可執行測試。

## 使用預先配置的 Hyperledger Fabric 運行環境對智慧型合約進行除錯

對智慧型合約進行除錯可讓您使用岔斷點及輸出來執行智慧型合約交易，以確保交易如預期運作。若要對智慧型合約進行除錯，請執行下列動作：

1. 確定您已連接至處於開發模式的 `local_fabric` 連線。
2. 開啟智慧型合約專案。
3. 使用左側導覽列，在 Visual Studio Code 中開啟除錯視圖。
4. 使用左上方的下拉清單來選取「對智慧型合約進行除錯」配置。
5. 按一下**播放**按鈕，以包裝及安裝智慧型合約。
6. 按一下智慧型合約檔案中的相關行號，將岔斷點新增至智慧型合約。
7. 用滑鼠右鍵按一下已安裝的智慧型合約，然後選取**實例化**。您現在可以按一下滑鼠右鍵來提交交易，而且會在已定義的岔斷點上暫停執行。

若要在除錯時變更智慧型合約，請在變更智慧型合約之後，按一下**重新啟動**按鈕。重新啟動除錯表示您不再需要實例化合約。

**請注意**：重新啟動除錯會將智慧型合約儲存至本端記憶體，針對大型智慧型合約的大量變更，您可能需要重新實例化智慧型合約。

## 升級已實例化的智慧型合約

在對等節點上安裝智慧型合約並在頻道上實例化智慧型合約之後，即可將它升級，以部署較新版本的智慧型合約。

1. 確定已實例化您要升級的智慧型合約。
2. 將新版本的智慧型合約安裝至相同網路上的對等節點。
3. 用滑鼠右鍵按一下已實例化的智慧型合約，然後選取**升級智慧型合約**。
4. 選擇性地選擇要執行的交易，因為已實例化新的智慧型合約。

## 提交交易

安裝並實例化智慧型合約之後，可以從 {{site.data.keyword.blockchainfull_notm}} Platform 延伸畫面中的連線窗格來提交交易。

若要提交交易，請執行下列動作：

1. 確定已安裝並實例化智慧型合約，而且您已連接至網路。
2. 在連線窗格中，展開**已實例化的智慧型合約**。
3. 展開包含要提交之交易的智慧型合約。
4. 用滑鼠右鍵按一下要提交的交易，然後選取**提交交易**。
5. 輸入交易所需的任何引數，然後按 **Enter** 鍵。

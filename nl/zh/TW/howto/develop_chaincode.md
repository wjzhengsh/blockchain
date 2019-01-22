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

# 撰寫智慧型合約

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

鏈碼（也稱為智慧型合約）是可讓您在區塊鏈分類帳上讀取及更新資料的軟體。鏈碼可以將商業邏輯轉換為可執行的程式，而該程式需由區塊鏈網路的所有成員同意及驗證。商業邏輯包括雙方之間交易的資產定義。它也包含執行交易所需的條款。將這些規則轉換為區塊鏈上的程式碼可讓企業簡化商業處理及審核，並減少大量手動處理及文書工作。

例如，假設汽車代理商、保險公司與政府管制者的網路決定使用區塊鏈來追蹤車輛所有權。鏈碼可能需要所有車輛都具有有效的登錄及車輛識別碼，才能新增至網路。售出車輛時，鏈碼可能需要將資金置入委付帳戶中，直到管制者將車輛登錄為其新擁有者為止。新的登錄完成時，就會記錄新的擁有者，並將資金自動轉帳。

下列指導教學將引導您建置鏈碼的基本層面，包括：

- [如何開始撰寫鏈碼](#write-chaincode)
- [鏈碼與資料之間的關係](#install-chaincode)
- [跨鏈碼交易](#cross-chaincode-transactions)

此指導教學也會介紹可透過鏈碼存取之網狀架構的重要層面：

- [專用資料](#private-data)
- [搭配使用索引與 couchDB](#indexes)

## 撰寫鏈碼
{: #write-chaincode}

鏈碼可以使用多種語言撰寫，而且 {{site.data.keyword.blockchainfull_notm}} Platform 支援使用 Go 及 Node.js 所撰寫的鏈碼。鏈碼容許使用者使用「Fabric 鏈碼」介面所提供的 API，來查詢及變更區塊鏈中所儲存的資料。區塊鏈上的資料會儲存至處於頻道[分類帳 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "分類帳") 廣域狀態的鍵值組中。鏈碼使用 get 指令來擷取值，並使用 put 指令來建立或更新值。您可以使用這些基本作業來建置函數，以定義網路的商業規則。您的應用程式可以呼叫這些函數，而且這些函數可開放給網路的一般使用者。若要繼續使用車輛網路範例，您可以建立一個函數，容許汽車代理商使用 put 指令將新汽車新增至分類帳（只有當他們提供有效的車輛 ID 號碼時）。

您可以造訪 Hyperledger Fabric 社群文件中的[開發人員鏈碼指導教學 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "開發人員鏈碼指導教學")，瞭解如何開始撰寫鏈碼。此指導教學將引導您建構可建立及讀取資產的簡單鏈碼，並介紹要在處理程序中使用的 API。您也可以找到所有鏈碼語言的鏈碼 API 參考手冊。在 [Fabric 範例儲存庫 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/hyperledger/fabric-samples "Fabric 範例") 的鏈碼資料夾中，還有其他範例。

## 安裝鏈碼
{: #install-chaincode}

因為鏈碼提供頻道上的交易結構，所以需要在加入頻道且想要使用鏈碼來更新或查詢頻道分類帳的所有對等節點上安裝鏈碼。然後，頻道的某個成員就可以在頻道上實例化鏈碼，並設定鏈碼的背書原則。使用「網路監視器」使用者介面、「Fabric 對等節點」指令行介面，或從使用 [Fabric SDK](../v10_application.html#operate-sdk) 的用戶端應用程式，可以執行鏈碼的安裝及實例化。若要瞭解如何使用「網路監視器」使用者介面來部署鏈碼，請參閱[安裝、實例化及更新鏈碼](install_instantiate_chaincode.html)。

## 鏈碼及資料
{: #chaincode-data}

每個頻道都只有一個分類帳，而且會依照唯一索引鍵以及將鍵值組新增至分類帳的鏈碼來分割該分類帳上的資料。成員只能使用正確的索引鍵及相關聯的鏈碼，來讀取或更新頻道分類帳上的資料。可由鏈碼存取的資料稱為該鏈碼的名稱空間，而且分類帳上的所有資料都在一個鏈碼的名稱空間內。鏈碼只有在使用可存取相關資料之鏈碼的[跨鏈碼交易](#cross-chaincode-transactions)時，才能與其名稱空間之外的資料互動。

我們可以使用 Fabric 範例儲存庫中的 fabcar 鏈碼，作為鏈碼如何與資料互動的範例。實例化智慧型合約時，即會建立名稱空間。某個鏈碼在實例化時會接受一組鍵值組引數，並使用該資料來起始設定名稱空間。Fabric 範例儲存庫中的 fabcar 鏈碼在實例化時則不會接受任何引數。對於 fabcar，您需要使用 initLedger 或 createCar 函數將資料新增至名稱空間。例如，傳遞 `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` 引數會將鍵值組 `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` 設為 fabcar 名稱空間。

只有 fabcar 鏈碼才能變更此資料。假設使用者想要使用下面的 changeCarOwner 函數，將汽車轉讓給新的擁有者：
```
func (s *SmartContract) changeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {

	if len(args) != 2 {
		return shim.Error("Incorrect number of arguments. Expecting 2")
	}

	carAsBytes, _ := APIstub.GetState(args[0])
	car := Car{}

	json.Unmarshal(carAsBytes, &car)
	car.Owner = args[1]

	carAsBytes, _ = json.Marshal(car)
	APIstub.PutState(args[0], carAsBytes)

	return shim.Success(nil)
}
```
{:codeblock}

您可以使用下列引數來呼叫鏈碼：
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

然後，鏈碼會使用 get 指令，從鏈碼名稱空間產生一個讀取集。它使用 put 指令來建立交易寫入集。鏈碼呼叫也會建立唯一的交易 ID。
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

每個已安裝鏈碼的背書對等節點都會使用讀取集及寫入集來模擬交易。如果每個對等節點的模擬都一致，則會將交易視為有效，而且可以將交易確定到分類帳。在交易之後，fabcar 名稱空間將變成 `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`。請注意，擁有者已從 Tom 變更為 Mark。

鏈碼與資料之間的 1-1 關係提供用於開發網路及應用程式的重要考量。鏈碼軟體應該透過安裝名稱相同而版本不同的鏈碼來更新，而非使用新的鏈碼。此更新程序會保留鏈碼名稱空間，並可讓您繼續使用現有的資料。您也可以使用不同的鏈碼來限制對機密資料的存取，或平行讀取及寫入資料。

## 跨鏈碼交易
{: #cross-chaincode-transactions}

您可以使用鏈碼來呼叫其他鏈碼。這可讓鏈碼查詢及寫入其名稱空間之外的資料。鏈碼可以使用在相同頻道上實例化的鏈碼同時讀取及更新資料。不過，它們只能使用不同頻道上的鏈碼來查詢資料。呼叫原始鏈碼的組織必須獲得授權，才能呼叫作為交易目標的鏈碼。

例如，您可以使用下面的 `crossChaincodeChangeCarOwner` 函數，從另一個鏈碼呼叫 fabcar。因為此函數透過變更汽車擁有者來更新資料，所以它需要呼叫在相同頻道上實例化的 fabcar 鏈碼。
```
func (s *SmartContract) crossChaincodeChangeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {
	newOwnerArgs := toChaincodeArgs("changeCarOwner", args[0], args[1])

	chaincodeName := "fabcar"
	channelName := "defaultchannel"

	APIstub.InvokeChaincode(chaincodeName, newOwnerArgs, channelName)

	return shim.Success(nil)
}
```
{:codeblock}

如果您使用此程式碼建立新合約，並呼叫 `crossChaincodeChangeCarOwner` 函數，則交易會產生新的交易 ID 及寫入集。
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

此 TXid 及寫入集將傳遞至 fabcar 鏈碼。
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

因為 `fabcar` 位於與 `newContract` 相同的頻道上，所以容許 `crossChaincodeChangeCarOwner` 函數寫入至 `fabcar` 名稱空間。新的 `fabcar` 名稱空間將是 `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`。

## 專用資料
{: #private-data}

專用資料收集是 1.2 版或更高版本的 Hyperledger Fabric 網路特性。頻道可供幾組組織用來進行交易，而不會向其他組織公開資料。不過，組織可能想要保持機密性資訊的私密性，而不向頻道的其他成員公開該資料。在此情況下，他們可以使用專用資料收集，只在需要資料的組織對等節點上儲存資訊。例如，批發商及農家可能想要使用與其他農家相同的頻道，來進行銷售證明。不過，他們可以使用專用收集來保持銷售機密層面（例如價格）的私密性。若要進一步瞭解如何在區塊鏈內使用專用資料，請造訪 Fabric 文件中的[專用資料 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "專用資料") 概念文章。

只要網路是 Fabric 1.2 版或更高版本，您就可以在 {{site.data.keyword.blockchainfull_notm}} Platform 上使用專用資料。您可以先將專用資料收集配置檔新增至鏈碼，再將它安裝到對等節點。然後，您可以使用專用資料特定鏈碼 API，從收集中輸入及擷取資料。

如需如何搭配使用專用資料收集與鏈碼的相關資訊，請參閱 Fabric 文件中的[使用專用資料 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "使用專用資料")。您無法使用「網路監視器」，利用收集配置檔來安裝鏈碼。不過，您可以使用 Fabric SDK 來安裝、實例化或更新使用專用資料的鏈碼。如需相關資訊，請參閱 Node SDK 文件中的[如何使用專用資料 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/release-1.3/tutorial-private-data.html "如何使用專用資料")。**附註**：您需要先將 SDK 的 signCert 上傳至 {{site.data.keyword.blockchainfull_notm}} Platform，才能使用 SDK 在對等節點上安裝及實例化鏈碼。如需如何使用 Fabric SDK 與網路互動及操作網路的相關資訊，請參閱[開發應用程式](../v10_application.html)及[使用 SDK 操作網路](../v10_application.html#operate-sdk)。

## 搭配使用索引與 CouchDB
{: #indexes}

如果您的分類帳資料儲存在 CouchDB 中，則強烈建議您針對 CouchDB 查詢建立索引，並在鏈碼中使用它們。這些索引可讓您的應用程式有效率地擷取資料，因為網路會在廣域狀態中新增額外的交易區塊及項目。此外，CouchDB 可讓您從鏈碼針對頻道分類帳上的資料執行豐富的資料查詢。

如需 CouchDB 及如何設定索引的相關資訊，請參閱 Hyperledger Fabric 文件中的 [CouchDB 即狀態資料庫 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB 即狀態資料庫"){:new_window}。您也可以在 [Fabric CouchDB 指導教學 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html) 中找到搭配使用索引與鏈碼的範例。如需如何從應用程式查詢資料的相關資訊，請造訪「開發應用程式」指導教學中的[使用 CouchDB 時的最佳作法](../v10_application.html#couchdb-indices)。

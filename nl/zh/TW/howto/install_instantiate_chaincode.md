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

# 安裝、實例化及更新鏈碼


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


鏈碼是一種軟體，其中封裝用來在分類帳中建立及修改資產的商業邏輯和交易指示。鏈碼可用不同語言撰寫，而且 {{site.data.keyword.blockchainfull}} Platform 支援 Go 及 Node.js 鏈碼。鏈碼會在與任何需要與其互動之對等節點相關聯的 Docker 容器中執行。如需開發鏈碼的相關資訊，請參閱[鏈碼指導教學 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode.html)。
{:shortdesc}

鏈碼會安裝在對等節點上，然後在頻道上進行實例化。**想要使用鏈碼來提交交易或讀取資料的所有成員，都需要在其對等節點上安裝鏈碼。**鏈碼是由其名稱及版本定義。在頻道上的所有對等節點中，已安裝鏈碼的名稱及版本必須一致。

在對等節點上安裝鏈碼之後，單一網路成員會在頻道上實例化鏈碼。網路成員需要加入頻道，才能執行此動作。實例化將輸入鏈碼所使用的起始資料，然後在已安裝鏈碼的情況下，於加入至頻道的對等節點上啟動鏈碼容器。然後，對等節點可以使用執行中容器來進行交易。**請注意，只有一個網路成員需要實例化鏈碼。** 如果已安裝鏈碼的對等節點加入已實例化的頻道，則鏈碼容器會自動啟動。

**安裝與實例化**的組合是一種功能強大的特性，因為它可讓對等節點跨多個頻道來使用單一鏈碼。對等節點可能想要加入多個頻道，這些頻道使用相同鏈碼，但可具有不同組的網路成員來存取資料。對等節點可以安裝鏈碼一次，然後在任何已實例化的頻道上使用相同的鏈碼容器。這種輕量型方法可節省運算及儲存空間，並協助您擴充網路。

## 安裝鏈碼
{: #installchaincode}

您必須在將執行此鏈碼的每個對等節點上安裝鏈碼。請完成下列步驟來安裝鏈碼：
1. 在「網路監視器」的「安裝程式碼」畫面中，從下拉清單中選取要安裝鏈碼的對等節點。按一下**安裝鏈碼**按鈕。
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. 在**安裝鏈碼**蹦現畫面中，輸入鏈碼的名稱和版本。**請注意**，該名稱和版本字串將會在應用程式中用來與已安裝的鏈碼互動。按一下**瀏覽**按鈕，並導覽至本端檔案系統中儲存鏈碼原始檔的任何位置。選取要安裝在對等節點上的一個以上鏈碼原始檔。然後，從**鏈碼類型**下拉清單中選取鏈碼語言。

您可以上傳單一或多個 GO 或 NODE 檔案來安裝鏈碼，也可以在 .zip 檔案內上傳鏈碼。使用 .zip 檔案將會維護具有完整目錄結構的鏈碼。如果您要包括相依關係套件，或搭配使用索引與 CouchDB，則這十分有用。如需 CouchDB 及如何設定索引的相關資訊，請參閱「開發應用程式」指導教學中的[使用 CouchDB 時的最佳作法](../v10_application.html#couchdb-indices)。您也可以在 Hyperledger Fabric 文件中找到[管理以 GO 撰寫之鏈碼的外部相依關係 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4ade.html#managing-external-dependencies-for-chaincode-written-in-go){:new_window} 相關資訊。

  ![安裝鏈碼](../images/chaincode_install.png "安裝鏈碼")

## 實例化鏈碼
將鏈碼安裝在加入頻道之每個對等節點的檔案系統上之後，必須在頻道上實例化鏈碼，這樣對等節點才能透過鏈碼容器來與分類帳互動。實例化作業會執行鏈碼的任何必要起始設定。這通常包括設定包含鏈碼起始廣域狀態的鍵值組。

您必須有頻道的**操作員**或**撰寫者**權限，才能將鏈碼實例化。不同對等節點上具有相同名稱和版本的鏈碼，需要實例化一次，即可部署鏈碼容器。請完成下列步驟來實例化鏈碼：
1. 在「網路監視器」的「安裝程式碼」畫面中，選取已安裝鏈碼的對等節點，並找出您要從鏈碼表格實例化的鏈碼。然後，按一下**動作**標頭下的**實例化**按鈕。
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. 在**實例化鏈碼**蹦現畫面中，將鍵值組設為鏈碼起始設定的引數，然後選取要在其中進行實例化作業的頻道。按**下一步**。
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

3. 指定您鏈碼的[背書原則](../glossary.html#endorsement-policy)。您可以在[下節](#specifying-chaincode-endorsement-policies)進一步瞭解如何設定背書原則。

## 指定鏈碼背書原則
{: #endorsement-policy}

您可以使用背書原則，來指定哪一組對等節點需要驗證新的交易。例如，背書原則可以指定只有在頻道上的大多數成員都對交易進行背書時，才會將交易新增至分類帳。

在頻道上實例化鏈碼時，會設定背書原則。實例化鏈碼的組織可以從已安裝鏈碼而成為驗證者的頻道成員中進行選取，並為所有頻道成員設定背書原則。背書原則的更新方式是遵循[更新鏈碼](#updating-a-chaincode)的步驟，然後在您於第二個步驟重新實例化鏈碼時指定新原則。

當您使用「網路監視器」來設定背書原則時，可以使用使用者介面來指定**簡單原則**，或使用 JSON 來指定**進階原則**。

* **使用使用者介面來指定簡單原則：**首先，按一下**新增成員**按鈕，選取可驗證交易的成員集。然後，在**背書原則**區段中，決定清單中有多少成員需要先驗證交易，再核准交易。您可以使用此方法來指定所有頻道成員、其中大多數成員、單一成員或簡單 +1 的背書原則，以防止成員自我簽署（例如，五個成員中的其中兩個）。如果您未進行任何變更，則預設原則容許頻道的任何成員為交易進行背書。

  ![簡單背書原則](../images/simple_endorsement.png "簡單背書原則")

* **使用 JSON 來指定進階原則：**使用進階原則需要重要成員或管理者的背書，或提供特定成員的背書較高的加權。

  指定進階原則的最簡單方式是從利用使用者介面畫面建置簡單原則開始。然後，按一下**進階原則**按鈕，以使用您在簡單原則中所設定的相同成員及規則來自動填寫原則的 JSON 版本。然後，您可以編輯 JSON 來撰寫更進階的版本。如需以 JSON 撰寫背書原則的相關資訊，請參閱 [Hyperledger Fabric Node SDK 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest)。<!--You can also find examples of advanced endorsement policies in the main [Hyperledger Fabric documentation![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/arch-deep-dive.html#example-endorsement-policies)-->

  ![進階背書原則](../images/advanced_endorsement.png "進階背書原則")

當新組織加入頻道並安裝鏈碼時，不會自動更新背書原則。比方說，如果原則需要五個組織中的其中兩個來為交易背書，則當新組織加入頻道時，將不會更新原則以要求六個組織中的其中兩個。相反地，不會在原則上列出新的組織，而且它們將無法為交易背書。您可以透過更新相關的鏈碼，將新組織新增至背書原則。

## 更新鏈碼

您可以更新鏈碼，以變更鏈碼的程式設計，同時維護其與分類帳上資產的關係。基於安裝與實例化作業的組合，您需要更新含此鏈碼之頻道上所有對等節點上的鏈碼。請完成下列步驟來更新鏈碼。

1. 安裝名稱與舊鏈碼相同但版本不同的鏈碼。您可以遵循與[安裝鏈碼](#installchaincode)相同的步驟。請確定您選取的頻道與原始鏈碼的頻道相同。

  ![更新鏈碼](../images/upgrade_chaincode.png "更新鏈碼")

2. 在表格中尋找新的鏈碼，然後按一下**動作**標頭下的**更新**按鈕。此動作會將您的鏈碼重新實例化，並將鏈碼容器取代為新的容器。當您按一下**更新**按鈕時，有機會更新鏈碼背書原則，如果最近已將組織新增至該頻道，則這很重要。請注意，您不需要輸入任何新的引數作為更新函數的一部分。此升級動作會在頻道上發生，而且只需要由一個組織執行即可。

  ![「更新」按鈕](../images/upgrade_button.png "「更新」按鈕")

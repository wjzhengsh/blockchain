---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 安裝、實例化及更新鏈碼

鏈碼是以 Go、Java 或 Node.js 撰寫的軟體，其中封裝用來在分類帳中建立及修改資產的商業邏輯和交易指示。鏈碼會在與任何需要與其互動之對等節點相關聯的 Docker 容器中執行。如需開發鏈碼的相關資訊，請參閱[鏈碼指導教學 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html)。
{:shortdesc}

鏈碼會安裝在對等節點的檔案系統上，然後在頻道上進行實例化。**所有頻道成員都必須在將執行此鏈碼的每個對等節點上安裝鏈碼。**若要使用相同的鏈碼，頻道成員必須在安裝鏈碼時，為鏈碼提供相同的名稱和版本。然後，實例化步驟包含起始設定鍵值組以及部署鏈碼容器。應用程式用來與鏈碼互動的任何對等節點，都必須要有安裝在對等節點檔案系統上的鏈碼，並且要有執行中的鏈碼容器。**不過，如果對等節點在多個頻道上使用相同的鏈碼，則只需要單一鏈碼容器實例**。

**安裝與實例化的組合**是一種功能強大的特性，因為它可以讓對等節點跨多個頻道來與相同的鏈碼容器互動。唯一的必要條件是要在對等節點的檔案系統上安裝實際鏈碼原始檔。因此，如果共用鏈碼的一部分將用於數個頻道，則對等節點只需要單一鏈碼容器，就可以對所有頻道分類帳執行讀寫。這種輕量型作法證明有利於運算效能和傳輸量，因為網路規模和鏈碼變得更加精密。

**附註**：如果您是以反覆運算方式來開發鏈碼，且需要更新鏈碼，則需要為鏈碼重複安裝和實例化步驟。


## 安裝鏈碼
{: #installchaincode}

您必須在將執行此鏈碼的每個對等節點上安裝鏈碼。請完成下列步驟來安裝鏈碼：
1. 在「網路監視器」的「安裝程式碼」畫面中，從下拉清單中選取要安裝鏈碼的對等節點。按一下**安裝鏈碼**按鈕。
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. 在**安裝鏈碼**蹦現畫面中，輸入鏈碼的名稱和版本。**請注意**，該名稱和版本字串將會在應用程式中用來與已安裝的鏈碼互動。按一下**瀏覽**按鈕，並導覽至本端檔案系統中儲存鏈碼原始檔的任何位置。選取要安裝在對等節點上的一個以上鏈碼原始檔。然後，從**鏈碼類型**下拉清單中選取鏈碼語言。

您可以上傳單一或多個 GO 或 NODE 檔案來安裝鏈碼，也可以在 .zip 檔案內上傳鏈碼。使用 .zip 檔案將會維護具有完整目錄結構的鏈碼。如果您要包括相依關係套件，或搭配使用索引與 CouchDB，則這十分有用。若要尋找鏈碼如何包括索引的範例，請參閱 Fabric 文件中的[從鏈碼使用 CouchDB ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html#using-couchdb-from-chaincode){:new_window}。您也可以找到[管理以 GO 撰寫之鏈碼的外部相依關係 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html#managing-external-dependencies-for-chaincode-written-in-go){:new_window} 的相關資訊

  ![安裝鏈碼](../images/chaincode_install.png "安裝鏈碼")

## 實例化鏈碼
將鏈碼安裝在加入頻道之每個對等節點的檔案系統上之後，必須在頻道上將鏈碼實例化，這樣對等節點才能透過鏈碼容器來與分類帳互動。實例化作業會執行鏈碼的任何必要起始設定。這通常需要設定包含鏈碼起始廣域狀態的鍵值組。

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

您可以使用背書原則，來指定哪一組對等節點需要驗證新的交易。例如，背書原則可以指定只有在頻道上的大多數成員都對交易進行背書時，才會將交易新增至分類帳。

在頻道上實例化鏈碼時，會設定背書原則。實例化鏈碼的組織可以從已安裝鏈碼而成為驗證者的頻道成員中進行選取，並為所有頻道成員設定背書原則。背書原則的更新方式是遵循[更新鏈碼](#updating-a-chaincode)的步驟，然後在您於第二個步驟重新實例化鏈碼時指定新原則。

當您使用「網路監視器」來設定背書原則時，可以使用使用者介面來指定**簡單原則**，或使用 JSON 來指定**進階原則**。

* **使用使用者介面來指定簡單原則：**首先，按一下**新增成員**按鈕，選取可驗證交易的成員集。然後，在**背書原則**區段中，決定清單中有多少成員需要先驗證交易，再核准交易。您可以使用此方法來指定所有頻道成員、其中大多數成員、單一成員或簡單 +1 的背書原則，以防止成員自我簽署（例如，五個成員中的其中兩個）。如果您未進行任何變更，則預設原則容許頻道的任何成員為交易進行背書。

  ![簡單背書原則](../images/simple_endorsement.png "簡單背書原則")

* **使用 JSON 來指定進階原則：**使用進階原則需要重要成員或管理者的背書，或提供特定成員的背書較高的加權。

  指定進階原則的最簡單方式是從利用使用者介面畫面建置簡單原則開始。然後，按一下**進階原則**按鈕，以使用您在簡單原則中所設定的相同成員及規則來自動填寫原則的 JSON 版本。然後，您可以編輯 JSON 來撰寫更進階的版本。如需以 JSON 撰寫背書原則的相關資訊，請參閱 [Hyperledger Fabric Node SDK 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest)。<!--You can also find examples of advanced endorsement policies in the main [Hyperledger Fabric documentation![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/arch-deep-dive.html#example-endorsement-policies)-->

  ![進階背書原則](../images/advanced_endorsement.png "進階背書原則")

## 更新鏈碼

您可以更新鏈碼，以變更鏈碼的程式設計，同時維護其與分類帳上資產的關係。基於安裝與實例化作業的組合，您需要更新含此鏈碼之頻道上所有對等節點上的鏈碼。請完成下列步驟來更新鏈碼。

1. 安裝名稱與舊鏈碼相同但版本不同的鏈碼。您可以遵循與[安裝鏈碼](install_instatiate_chaincode.html#Installing a chaincode)相同的步驟。請確定您選取的頻道與原始鏈碼的頻道相同。

  ![更新鏈碼](../images/upgrade_chaincode.png "更新鏈碼")

2. 在表格中尋找新的鏈碼，然後按一下**動作**標頭下的**更新**按鈕。此動作會將您的鏈碼重新實例化，並將鏈碼容器取代為新的容器。請注意，您不需要輸入任何新的引數作為更新函數的一部分。

  ![「更新」按鈕](../images/upgrade_button.png "「更新」按鈕")

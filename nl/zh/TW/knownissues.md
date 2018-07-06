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


# 已知的問題

已報告下列問題：
- **尚未支援配置外部 CA**。作為替代方案，您可以透過「網路監視器」來產生及上傳管理憑證。如需相關資訊，請參閱[產生用戶端憑證](v10_application.html#generating-the-client-side-certificates)，以及「網路監視器」中[「成員」畫面的「憑證」標籤](v10_dashboard.html#members)上的說明。  
- 在「入門範本方案」網路的「網路監視器」中，當您在「概觀」畫面所列的節點上按一下**檢視日誌**時，即會開啟「{{site.data.keyword.cloud}} 記載」Kibana 介面。**依預設，會預先配置 Kibana，以顯示最後 15 分鐘活動的日誌**。如果在最後 15 分鐘沒有任何活動，您會看到一則訊息指出*找不到任何結果*。若要檢視其他日誌，您可以按一下使用者名稱右上角的計時器圖示，並設定更廣泛的時間範圍，例如*本週* 或*本月*。  
- [{{site.data.keyword.cloud_notm}} Log Analysis 服務 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://console.bluemix.net/catalog/services/log-analysis) 會收集「入門範本方案」網路的日誌。依預設，Log Analysis 服務的「精簡方案」會收集您的日誌。此方案是免費的，而且**只容許您搜尋每天前 500 MB 的日誌**。如果您網路的日誌超出 500 MB，則無法在 Kibana 中檢視新的日誌。如果您的網路產生超過 500 MB 的日誌，則可以升級至「Log Analysis 服務」的付費版本。  
- 因為「入門範本方案」不是正式作業環境，所以**應用程式可能無法立即到達網路資源**。
  - 如果發生此情況，建議首要步驟是增加 Fabric SDK 中的預設逾時值。如需設定逾時值的相關資訊，請參閱[設定 Fabric SDK 中的逾時值](v10_application.html#set-timeout-in-sdk)。
  - 您也可以在應用程式層次重試要求。  
- 背景網路問題**有時可能會停止鏈碼容器**，因此在使用者呼叫之後可能需要重建及重新啟動鏈碼容器。如果發生此情況，您的鏈碼可能需要幾分鐘才能回應。
- 因為「入門範本方案」網路中的資源限制（亦即，每一個對等節點有 1 個 CPU 及 2 Gi RAM），所以**您可能會在鏈碼實例化期間遇到 `REQUEST_TIMEOUT` 錯誤**。如果發生此情況，請重試實例化步驟。如果錯誤持續發生，您可以增加鏈碼實例化逾時。在連線設定檔中，鏈碼實例化逾時設為 300 秒。
  - 如果您使用 SDK 中的預設逾時值，請如下所示複製連線設定檔中的 **client** 區段（這會將逾時設為 300 秒），並確定您的 SDK 讀取它。請注意，針對 Node SDK，連線設定檔中的這個逾時設定會影響所有呼叫（例如 `invoke`、`queries` 等等）。
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - 如果您改寫 SDK 中鏈碼實例化指令的逾時設定，請將它設回預設值，或將其變更為 300 秒以上。
    - 如果您使用 Node SDK，則可以變更 `sendInstantiateProposal(request, timeout)` 方法的逾時設定。如需相關資訊，請參閱 [sendInstantiateProposal(request, timeout) ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal)。
    - 如果您使用 Java SDK，則可以變更 `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)` 指令的逾時設定，而此指令位於 `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java` 檔案中。

如需 {{site.data.keyword.cloud_notm}} 上「{{site.data.keyword.blockchainfull_notm}} 平台」網路的支援及協助，請參閱[取得支援](ibmblockchain_support.html)。


## 更新鏈碼以升級企業方案網路
{: #chaincode-migration}

如果您的「企業方案」網路是 Hyperledger Fabric 1.0 版層次，則「{{site.data.keyword.blockchainfull_notm}} 平台」將排定升級，以將網路移轉至 Hyperledger Fabric 1.1 版。透過網路升級，如果您使用具有相依關係的複雜鏈碼，則需要更新鏈碼中的相依關係。否則，您可能會遇到服務毀壞。

**附註**：
- 如果您使用具有單一 `.go` 或 `.js` 檔案的簡單鏈碼，則不需要更新鏈碼。
- 更新的鏈碼可能無法在舊網路上運作；因此，您需要在排定的網路升級之後更新鏈碼。
- 您可以在「入門範本方案」網路中安裝並實例化鏈碼，以進行測試。在網路升級之後，所有適用於「入門範本方案」的鏈碼也會適用於「企業方案」。

請執行下列步驟來更新鏈碼：
1. 使用任何 Golang 供應商工具來更新鏈碼。使用原始檔案中用來包括相依關係的相同工具，是最簡單的方法。例如，如果您的鏈碼使用許多 Fabric 範例所使用的 **govendor** 工具，則您可以在供應商資料夾上方的目錄中執行下列指令來更新鏈碼相依關係：
    ```
    govendor update all +v
    ```
    {:codeblock}

    您接著可以使用 `go build` 來檢查是否已編譯新程式碼，以及鏈碼更新是否作用。

2. 在網路升級之後，您可以在「網路監視器」中[更新鏈碼](howto/install_instantiate_chaincode.html#updating-a-chaincode)。

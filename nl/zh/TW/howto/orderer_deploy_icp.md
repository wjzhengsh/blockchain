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

# 在 {{site.data.keyword.cloud_notm}} Private 中部署排序節點
{: #orderer-deploy}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

「排序節點」使用排序節點元件在區塊鏈網路中鑑別用戶端、排序交易及播送交易。如需排序節點及其在區塊鏈網路中所扮演角色的相關資訊，請參閱[區塊鏈元件的概觀](../blockchain_component_overview.html)。
{:shortdesc}

在部署排序服務之前，請先檢閱[注意事項及限制](../ibp-for-icp-about.html#ibp-icp-considerations)。

## 所需資源
{: #ca-resources-required}

請確定您的 ICP 系統符合最低硬體資源需求：

| 元件      | vCPU | RAM | 資料儲存空間的磁碟    |
|-----------|------|-----|-----------------------|
| 排序節點  | 2 | 512 MB | 100 GB，具有擴充能力 |


 **附註：**
 - 如果未針對虛擬機器分割伺服器，則 vCPU 是指派給虛擬機器或實體處理器核心的虛擬核心。當您決定部署在 ICP 中的虛擬處理器核心 (VPC) 時，需要考量 vCPU 需求。VPC 是決定 IBM 產品授權成本的度量單位。如需決定 VPC 之情境的相關資訊，請參閱[虛擬處理器核心 (VPC) ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)。
 - 這些最低資源層次對於測試和實驗而言便已足夠。對於具有大量交易的環境，請務必配置夠大的儲存空間數量；例如，500 GB 用於排序服務。要使用的儲存空間數量將視網路所需的交易數目及簽章數目而定。如果排序節點的儲存空間即將耗盡，則必須使用較大的檔案系統來部署新的排序節點，並讓它能透過相同頻道上的其他元件進行同步。

## 儲存空間
{: #storage}

您需要決定排序節點將使用的儲存空間。如果您使用預設值，則 Helm 圖表將為您的排序節點資料建立一個新的 8 Gi 持續性磁區要求 (PVC)（名稱為 `my-data-pvc`），並為您的狀態資料庫建立另一個 8 Gi PVC（名稱為 `stagedb-pvc`）。

如果您不想使用預設儲存空間設定，請確定在 ICP 安裝期間設定*新的* `storageClass`，否則 Kubernetes 系統管理者需要在您部署之前建立 storageClass。

您可以選擇在 amd64 或 s390x 平台上部署排序節點。不過，請注意，[動態佈建](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)只適用於 ICP 中的 amd64 節點。如果您的叢集混合了 s390x 及 amd64 工作者節點，則無法使用動態佈建。

如果您不使用動態佈建，則必須使用可用來修正 Kubernetes PVC 連結處理程序的標籤來建立及設定[持續性磁區](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。


## 部署排序節點的必要條件
{: #prerequisites-orderer-icp}

1. 在 ICP 上安裝排序節點之前，您必須先[安裝 ICP](../ICP_setup.html) 並[安裝 {{site.data.keyword.blockchainfull_notm}} Platform Helm 圖表](helm_install_icp.html)。

2. 如果您使用 Community Edition，而且想要在沒有網際網路連線功能的 ICP 叢集上執行此 Helm 圖表，則需要先在連接網際網路的機器上建立保存檔，才能在 ICP 叢集上安裝保存檔。如需相關資訊，請參閱[將精選應用程式新增至沒有網際網路連線功能的叢集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "將精選應用程式新增至沒有網際網路連線功能的叢集"){:new_window}。請注意，您可以在 Helm 圖表的 ibm-blockchain-platform-dev/ibm_cloud_pak 下找到規格檔案 `manifest.yaml`。

3. 從 ICP 主控台擷取 CA 的叢集 Proxy IP 位址值。**附註：**您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能存取 Proxy IP。請登入 ICP 叢集。在左導覽畫面中，按一下**平台**，然後按一下**節點**以檢視叢集中所定義的節點。按一下具有 `Proxy` 角色的節點，然後從表格中複製`主機 IP` 的值。**重要事項：**請儲存此值，您將在配置 Helm 圖表的 `Proxy IP` 欄位時加以使用。

4. 建立[排序節點配置檔，並將其儲存為 ICP 中的 Kubernetes 密碼](#orderer-config-file)。

## 建立排序節點配置檔
{: #orderer-config-file}

在部署排序節點之前，您需要先建立配置檔來包含關於排序節點身分和 CA 的重要資訊。然後，您必須使用 [Kubernetes 密碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/configuration/secret/) 物件，在配置期間將此檔案傳遞給 Helm 圖表。這個檔案可讓排序節點從 CA 中取得它要加入區塊鏈網路所需的憑證。它還包含管理者憑證，可讓您以管理使用者的身分來操作排序節點。在配置排序節點之前，請遵循[使用 CA 來部署排序節點或對等節點](CA_operate.html#deploy-orderer-peer)的相關指示。

您需要提供 CSR 主機名稱給配置檔。這包括`服務主機名稱`，它是根據您在部署期間指定的 `Helm 版本名稱`。`服務主機名稱`是您在結尾處新增 `-orderer` 字串指定的 `helm_release_name`。比方說，如果您指定的 `Helm 版本名稱`是 `orderer1`，您可以在檔案的 `"csr"` 區段中插入下列值：

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

儲存配置檔之後，您必須以 base64 格式進行編碼，才能將該檔案提供給 ICP 作為密碼物件。Kubernetes 密碼可讓您保護及共用資訊，而不必將資訊直接傳遞至部署。

1. 透過從終端機視窗執行下列指令，以 Base64 格式將配置檔編碼：

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **附註：**使用上述指令所產生的字串必須為單行格式，這點很重要。它看起來應如下所示：

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   而不是類似下面這樣：

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  儲存產生的輸出供下面的步驟 4 使用。

2. 登入 ICP 主控台。在左導覽畫面中，按一下**配置**，然後按一下**密碼**。按一下**建立密碼**按鈕來開啟蹦現畫面，讓您能夠產生新的密碼物件。

3. 在**一般**標籤上，完成下列欄位：
  - **名稱：**為您的密碼提供叢集內的唯一名稱。您將在部署排序節點時使用此名稱。此名稱必須全部為小寫。  
  **附註：**當您部署排序節點時，此部署會以下列名稱自動產生新密碼：`<helm_release_name>-orderer-mspsecret`。因此，當您為密碼命名時，密碼的名稱必須不同於 `<helm_release_name>-orderer-mspsecret`。否則，Helm 圖表部署將會失敗，因為它嘗試建立的密碼已存在。
  - **名稱空間：**要新增密碼的名稱空間。請選取您要在其中部署排序節點的`名稱空間`。
  - **類型：**輸入 `Opaque` 值。

4. 按一下此視窗之左導覽窗格中的**資料**標籤。在`名稱`欄位中指定 `secret.json`，然後在值欄位中，貼上配置檔的 `base64` 編碼字串。

5. 按一下**建立**以儲存密碼物件。

**附註：**當您刪除 Helm 版本時，不會從 ICP 叢集中移除排序節點配置密碼。如果您刪除排序節點，也必須一併刪除這個密碼。

## 配置
{: #orderer-configuration}

建立您的排序節點配置密碼物件之後，您可以使用下列步驟，在 ICP 中配置及安裝您的排序節點。一次只能安裝一個排序節點。

1. 登入 ICP 主控台，然後按一下右上角的**型錄**鏈結。
2. 如果您已下載 Community Edition，則請按一下左導覽畫面中的 `Blockchain`，以找到標示 `ibm-blockchain-platform-prod` 或 `ibm-blockchain-platform-dev` 的磚。按一下此磚加以開啟，您就可以看到 Readme 檔，其中包括安裝及配置 Helm 圖表的相關資訊。
3. 按一下畫面頂端的**配置**標籤，或按一下右下角的**配置**按鈕。
4. 指定[一般配置參數](#orderer-global-parameters)的值，並接受授權合約。
5. 開啟`所有參數`小三角形，並指定[廣域配置參數](#orderer-global-parameters)的值。
6. 向下捲動至**排序節點配置**區段。選取`安裝排序節點`勾選框，然後完成排序節點的[配置設定](#orderer-parameters)。
7. 按一下**安裝**。


**附註：**將排序節點部署至 s390x 時，init 容器可能會傳回下列錯誤：

`防毀：嘗試開啟 DB 時發生錯誤：沒有可用的鎖定`

這是因為排序節點使用純文字檔 DB，而「NFS 檔案系統」需要其他套件，才能讓排序服務有能力查詢檔案，用來檢查檔案是否有專用鎖定以及建立專用鎖定。若要修正此問題，您必須啟用 `rpc-statd` 套件。

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

這必須在提供 NFS 檔案系統的系統上執行。

### 配置參數
{: #icp-orderer-configuration-parms}

下表列出 {{site.data.keyword.blockchainfull_notm}} Platform 的可配置參數（**排序節點元件特有**）及其預設值。**雖然 Helm 圖表使用者介面指出不需要進一步配置，但您需要輸入特定參數來部署排序節點。**

#### 一般及廣域配置參數
{: #orderer-global-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
| `Helm 版本名稱`| Helm 版本的名稱。開頭必須為小寫字母、結尾必須為任何英數字元，而且只能包含連字號及小寫英數字元。每次您嘗試安裝元件時，都必須使用唯一的 Helm 版本名稱。**重要事項：**此值必須符合您在 [JSON 機密檔](#orderer-config-file)中針對 "hosts" 欄位產生「服務主機名稱」的值。| 無 | 是 |
| `目標名稱空間`| 選擇 Kubernetes 名稱空間來安裝 Helm 圖表。| 無 | 是 |
| **廣域配置**| 適用於 Helm 圖表中所有元件的參數 |||
| `服務帳戶名稱`| 輸入您將用來執行 Pod 的[服務帳戶 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) 名稱。| 預設值 | 否 |

#### 排序節點配置參數
{: #orderer-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
| `安裝排序節點`| 選取要安裝排序節點。| 不勾選 | 是（如果您要部署排序節點的話） |
| `排序節點工作者節點架構`| 選取您的 ICP 工作者節點架構（AMD64 或 S390X）。| 以主節點為基礎的自動偵測架構 | 是 |
| `排序節點配置`| 您可以在這個欄位中貼上您自己的 `orderer.yaml` 配置檔，來自訂排序節點的配置。若要查看 `orderer.yaml` 檔案，請參閱 [`order.yaml` 範例配置 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/orderer.yaml) **僅適用於進階使用者**。| 無 | 否 |
| `組織 MSP 密碼（必要）`| 指定包含組織 MSP 憑證及金鑰的密碼物件名稱。| 無 | 是 |
| `已啟用排序節點資料持續性` | 當容器重新啟動時，資料將可供使用。如果不勾選，則會在發生失效接手或 Pod 重新啟動時遺失所有資料。| 已勾選 | 否 |
| `排序節點使用動態佈建` | 勾選以啟用儲存空間磁區的動態佈建。| 已勾選 | 否 |
| `排序節點映像檔儲存庫` | 排序節點 Helm 圖表的位置。此欄位會自動填寫至已安裝的路徑。如果您使用 Community Edition，而且沒有網際網路存取權，請將此欄位變更為您下載 Fabric 排序節點映像檔的位置。| ibmcom/ibp-fabric-orderer | 否 |
| `排序節點 Docker 映像檔標籤`| Docker 映像檔的記錄。此欄位會自動填寫至映像檔版本。請勿變更它。| 1.2.1 | 是 |
| `排序節點共識類型`| 排序服務的共識類型。| SOLO | 是 |
| `排序節點組織名稱`| 指定您要用於排序節點組織的名稱。如果您還計劃部署對等節點，請務必使用不同於您提供給對等節點的名稱。例如，提供諸如 `ordererOrg` 的排序節點組織名稱 | 無 | 是 |
| `排序節點組織 MSP ID`| 指定您要用於排序節點組織 MSP ID 的名稱。這應該與您提供給排序節點組織的名稱相同，而且部署程序會將它設為環境變數。請記下此值，稍後您需要參照它。| 無 | 是 |
| `排序節點儲存空間類別名稱`| 指定排序節點的儲存空間類別名稱。| 無 | 取決於 ICP 叢集的配置方式。請洽詢您的叢集管理者 |
| `排序節點現有磁區要求`| 指定現有「磁區要求」的名稱，並將所有其他欄位保留為空白。| 無 | 否 |
| `排序節點選取器標籤`| PVC 的[選取器標籤 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)。| 無 | 否 |
| `排序節點選取器值`| PVC 的[選取器值 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)。| 無 | 否 |
| `排序節點儲存空間存取模式`| 指定 PVC 的儲存空間[存取模式 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)。| ReadWriteMany | 是 |
| `排序節點磁區要求大小`| 選擇要使用的磁碟大小，必須至少有 2 Gi。| 8 Gi | 是 |
| 排序節點服務類型` | 用來指定在對等節點上是否[應該公開外部埠 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)。選取 NodePort 可在外部公開埠（建議使用），而選取 ClusterIP 則不會公開埠。此版本不支援 LoadBalancer 及 ExternalName | NodePort | 是 |
| `排序節點 CPU 要求`| 指定要配置給排序節點的 CPU 數目下限。| 1 | 是 |
| `排序節點 CPU 限制`| 指定要配置給排序節點的 CPU 數目上限。| 2 | 是 |
| `排序節點記憶體要求`| 指定要配置給排序節點的記憶體數量下限。| 1Gi | 是 |
| `排序節點記憶體限制`| 指定要配置給排序節點的記憶體數量上限。| 2Gi | 是 |

### 使用 Helm 指令行來安裝 Helm 版本
{: #icp-helm-cli}

或者，您也可以使用 Helm CLI 來安裝 Helm 版本。執行 `helm install` 指令之前，請確定您[將叢集的 Helm 儲存庫新增至 Helm CLI 環境 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "將內部 Helm 儲存庫新增至 Helm CLI")。

您可以建立 `yaml` 檔案並將其傳遞至下列 `helm install` 指令，來設定安裝所需的參數。

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

其中：

- `<helm_release name>` 代表您要提供給 Helm 版本的名稱。
- `<helm_chart>` 代表匯入至型錄的 Helm 圖表名稱。
- `<helm_chart_version>` 代表匯入至型錄的 Helm 圖表版本。
- `<customvalues.yaml>` 是包含配置參數的 yaml 檔案名稱。

例如：

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values orderer-s390x-values.yaml \
--tls
```

您可以編輯所下載保存檔中包含的 `values.yaml` 來建立新的 `yaml` 檔案，其中包括所有必要的參數及其預設值。

## 驗證排序節點安裝

完成配置參數並按一下**安裝**按鈕之後，請按一下**檢視 Helm 版本**按鈕，以檢視您的部署。如果已順利完成，您應該會在「部署」表格的 `DESIRED`、`CURRENT`、`UP TO DATE` 及 `AVAILABLE` 欄位中看到值 1。您可能需要按一下「重新整理」，並等待更新表格。您也可以按一下 ICP 主控台左上角的**功能表**圖示，來尋找「部署」表格。從功能表清單中，按一下**工作負載**，然後按一下 **Helm 版本**。

## 檢視排序節點日誌
{: #orderer-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](orderer_operate.html#orderer-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。如需相關資訊，請參閱這些[存取日誌指示](orderer_operate.html#orderer-view-logs)。

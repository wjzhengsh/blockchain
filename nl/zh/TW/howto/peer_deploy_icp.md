---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 在 {{site.data.keyword.cloud_notm}} Private 中部署對等節點
{: #peer-icp}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

下列指示說明如何在 {{site.data.keyword.cloud_notm}} Private (ICP) 上部署 {{site.data.keyword.blockchainfull}} Platform 對等節點。這些指示可讓您連接至 ICP 上的 {{site.data.keyword.blockchainfull_notm}} Platform。如果您要將對等節點連接至 {{site.data.keyword.cloud_notm}} 上的「入門範本方案」或「企業方案」網路，請參閱[部署對等節點以連接至入門範本方案或企業方案](peer_deploy_ibp.html)。
{:shortdesc}

在部署對等節點之前，請先檢閱[注意事項及限制](../ibp-for-icp-about.html#ibp-icp-considerations)。

## 所需資源
{: #peer-resources-required}

請確定您的 ICP 系統符合最低硬體資源需求：

| 元件 | vCPU | RAM | 資料儲存空間的磁碟 |
|-----------|------|-----|-----------------------|
| 對等節點  | 2 | 2 GB | 50 GB（可擴充）|
| 對等節點的 CouchDB | 2 | 2 GB | 50 GB（可擴充）|

 **附註：**
 - 如果未針對虛擬機器分割伺服器，則 vCPU 是指派給虛擬機器或實體處理器核心的虛擬核心。當您決定部署在 ICP 中的虛擬處理器核心 (VPC) 時，需要考量 vCPU 需求。VPC 是決定 IBM 產品授權成本的度量單位。如需決定 VPC 之情境的相關資訊，請參閱[虛擬處理器核心 (VPC) ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)。
 - 這些最低資源層次對於測試和實驗而言便已足夠。對於具有大量交易的環境，請務必配置夠大的儲存空間數量；例如，250 GB 用於對等節點。要使用的儲存空間數量將視網路所需的交易數目及簽章數目而定。如果您即將耗盡對等節點或排序節點上的儲存空間，則必須部署具有較大檔案系統的新對等節點或排序節點，並讓它透過相同頻道上的其他元件進行同步。

## 儲存空間
{: #storage}

您需要判斷對等節點將使用的儲存空間。如果您使用預設值，則 Helm 圖表將為您的對等節點資料建立一個新的 8 Gi 持續性磁區要求 (PVC)（名稱為 `my-data-pvc`），並為您的狀態資料庫建立另一個 8 Gi PVC（名稱為 `stagedb-pvc`）。

如果您不想使用預設儲存空間設定，請確定在 ICP 安裝期間設定*新的* `storageClass`，否則 Kubernetes 系統管理者需要在您部署之前建立 storageClass。

您可以選擇在 amd64 或 s390x 平台上部署對等節點。不過，請注意，[動態佈建](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)只適用於 ICP 中的 amd64 節點。如果您的叢集混合了 s390x 及 amd64 工作者節點，則無法使用動態佈建。

如果您未使用動態佈建，則必須使用可用來修正 Kubernetes PVC 連結處理程序的標籤，建立及設定[持續性磁區 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。

## 部署對等節點的必要條件
{: #prerequisites-peer-icp}

1. 在 ICP 上安裝對等節點之前，您必須先[安裝 ICP](../ICP_setup.html) 並[安裝 {{site.data.keyword.blockchainfull_notm}} Platform Helm 圖表](helm_install_icp.html)。

2. 如果您使用 Community Edition，而且想要在沒有網際網路連線功能的 ICP 叢集上執行此 Helm 圖表，則需要先在連接網際網路的機器上建立保存檔，才能在 ICP 叢集上安裝保存檔。如需相關資訊，請參閱[將精選應用程式新增至沒有網際網路連線功能的叢集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "將精選應用程式新增至沒有網際網路連線功能的叢集"){:new_window}。請注意，您可以在 Helm 圖表的 `ibm-blockchain-platform-dev/ibm_cloud_pak` 下找到規格檔案 `manifest.yaml`。

3. 您必須先在 ICP 上[部署 CA](CA_deploy_icp.html)。您需要使用 CA 來建立[對等節點配置檔，並將其儲存為 ICP 中的 Kubernetes 密碼](#peer-config-file)。

4. 從 ICP 主控台擷取 CA 的叢集 Proxy IP 位址值。**附註：**您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能存取 Proxy IP。請登入 ICP 叢集。在左導覽畫面中，按一下**平台**，然後按一下**節點**以檢視叢集中所定義的節點。按一下具有 `Proxy` 角色的節點，然後從表格中複製`主機 IP` 的值。**重要事項：**請儲存此值，您將在配置 Helm 圖表的 `Proxy IP` 欄位時加以使用。


## 建立對等節點配置密碼
{: #peer-config-file}

若要部署對等節點，您需要建立一個配置檔，其中包含有關對等節點身分及憑證管理中心的重要資訊。然後，您必須使用 [Kubernetes 密碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/configuration/secret/) 物件，在配置期間將此檔案傳遞給 Helm 圖表。這個檔案可讓對等節點從憑證管理中心取得它要加入區塊鏈網路所需的憑證。它還包含可讓您操作對等節點的管理者憑證。請遵循指示，在對等節點配置之前[使用 CA 來部署排序節點或對等節點](CA_operate.html#deploy-orderer-peer)。

您需要提供 CSR 主機名稱給配置檔。這包括`服務主機名稱`，它與您在部署期間指定的 `Helm 版本名稱`值相同。比方說，如果您指定的 `Helm 版本名稱`是 `org1peer1`，請記得在該檔案的 `"csr"` 區段中插入下列值：
```
"csr": {
  "hosts": [
     "9.42.134.44",
    "org1peer1"
  ]
}
```
{:codeblock}

儲存配置檔之後，您必須將它編碼為 base64 格式，然後才能將它提供給 ICP 作為密碼物件。Kubernetes 密碼可讓您保護及共用資訊，而不必將資訊直接傳遞至部署。

1. 若要以 base64 格式將配置檔編碼，請從終端機視窗執行下列指令：
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <config_file> | base64 $FLAG
```
{:codeblock}

**附註：**上述指令產生的字串必須為單行格式，這點很重要。它看起來應如下所示：

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
  - **名稱：**為您的密碼提供叢集內的唯一名稱。您將在部署對等節點時使用此名稱。此名稱必須全部為小寫。  
  **附註：**當您部署對等節點時，此部署會以 `<helm_release_name>-secret` 名稱自動產生新密碼。因此，當您為密碼命名時，密碼的名稱必須不同於 `<helm_release_name>-secret`。否則，Helm 圖表部署將會失敗，因為它嘗試建立的密碼已存在。
  - **名稱空間：**要新增密碼的名稱空間。請選取您要在其中部署對等節點的`名稱空間`。
  - **類型：**輸入 `Opaque` 值。

4. 按一下此視窗之左導覽窗格中的**資料**標籤。
  - 在`名稱`欄位中指定 `secret.json`，然後在值欄位中，貼上配置檔的 `base64` 編碼字串。

5. （選用）如果您計劃使用 `CouchDB` 作為狀態資料庫，則需要在這個密碼物件中新增另外兩個值。在**資料**標籤中，按一下**新增資料**按鈕，以新增在部署容器時要用於資料庫的 CouchDB 使用者 ID 及密碼。
  1. 建立您的使用者名稱及密碼，並以 base64 格式將這些值編碼。在終端機視窗中執行下列指令，並將 `admin` 及 `adminpw` 取代為您要使用的值。
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. 在**名稱**欄位中，輸入 `couchdbuser` 值。在對應的**值**欄位中，輸入上一個步驟的 `echo -n 'couch' | base64 $FLAG` 結果。
  3. 按一下**新增資料**按鈕，以新增第二個鍵值組。
  4. 在第二個**名稱**欄位中，輸入 `couchdbpwd` 值。在對應的**值**欄位中，輸入上一個步驟的 `echo -n 'couchpw' | base64 $FLAG` 結果。

6. 按一下**建立**以儲存密碼物件。

**附註：**當您刪除 Helm 版本時，不會從 ICP 叢集中移除對等節點配置密碼。如果您刪除對等節點，也必須一併刪除這個密碼。

## 配置
{: #peer-configuration}

建立您的對等節點配置密碼物件之後，您可以使用下列步驟，在 ICP 中配置及安裝您的對等節點。您一次只能安裝一個對等節點。


1. 登入 ICP 主控台，然後按一下右上角的**型錄**鏈結。
2. 如果您已下載 Community Edition，則請按一下左導覽畫面中的 `Blockchain`，以找到標示 `ibm-blockchain-platform-prod` 或 `ibm-blockchain-platform-dev` 的磚。按一下此磚加以開啟，您就可以看到 Readme 檔，其中包括安裝及配置 Helm 圖表的相關資訊。
3. 按一下畫面頂端的**配置**標籤，或按一下右下角的**配置**按鈕。
4. 指定[一般配置參數](#peer-global-parameters)的值，並接受授權合約。
5. 開啟`所有參數`小三角形，並指定[廣域配置參數](#peer-global-parameters)的值。
6. 向下捲動至**對等節點配置**區段。選取`安裝對等節點`勾選框，然後完成對等節點的[配置設定](#peer-parameters)。
7. 按一下**安裝**。

### 配置參數
{: #icp-peer-configuration-parms}

下表列出 {{site.data.keyword.blockchainfull_notm}} Platform 的可配置參數（**對等節點元件特有**）及其預設值。如果您是第一次實驗對等節點或開始使用，請使用資料庫及儲存空間參數的預設值。請捲動至對等節點元件區段，以勾選`安裝對等節點`勾選框，並只為該元件提供相關聯的配置資訊。**雖然 Helm 圖表使用者介面指出不需要進一步配置，但您需要輸入特定參數來部署對等節點。**

#### 一般及廣域配置參數
{: #peer-global-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
| `Helm 版本名稱`| Helm 版本的名稱。開頭必須為小寫字母、結尾必須為任何英數字元，而且只能包含連字號及小寫英數字元。每次您嘗試安裝元件時，都必須使用唯一的 Helm 版本名稱。**重要事項：**此值必須符合您在 [JSON 機密檔](#peer-config-file)中針對 "hosts" 欄位用來產生「服務主機名稱」的值。| 無 | 是 |
| `目標名稱空間`| 選擇 Kubernetes 名稱空間來安裝 Helm 圖表。| 無 | 是 |
| **廣域配置**| 適用於 Helm 圖表中所有元件的參數 |||
| `服務帳戶名稱`| 輸入您將用來執行 Pod 的[服務帳戶 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) 名稱。| 預設值 | 否 |

#### 對等節點配置參數
{: #peer-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
|**叢集配置** |**叢集配置資訊** | ||
| `安裝對等節點` | 選取要安裝對等節點 | 不勾選 | 是（如果您要部署對等節點的話）|
| `對等節點工作者節點架構`| 選取您的雲端平台架構（AMD64 或 S390x）| AMD64 | 是 |
| `對等節點映像檔儲存庫`| 對等節點 Helm 圖表的位置。此欄位會自動填寫至已安裝的路徑。| ibmcom/ibp-fabric-peer | 是 |
| `對等節點 Docker 映像檔標籤`|與對等節點映像檔相關聯的標籤值。|1.2.1（自動填入正確值）。| 是 |
| `對等節點配置`| 您可以在這個欄位中貼上您自己的 `core.yaml` 配置檔，來自訂對等節點的配置。若要查看 `core.yaml` 檔案範例，請參閱 [`core.yaml` 範例配置 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **僅適用於進階使用者**。| 無 | 否 |
| `對等節點配置密碼（必要）`|您在 ICP 中所建立之[對等節點配置密碼](#peer-config-secret)的名稱。| 無 | 是 |
|`組織 MSP（必要）`| 您可以建立新的組織 MSPID 值（例如 'org1'），或指定對等節點將成為其中一部分的現有組織 MSP。如果您已部署排序節點組織，請確定任何對等節點 MSPID 都與您的排序節點 MSPID 不同。此外，請記下此值，因為稍後的 `CORE_PEER_LOCALMSPID` 及 `configtx.yaml` 都需要用到它。| 無 | 是 |
|`對等服務程式類型`| 用來指定在對等節點上是否[應該公開外部埠 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)。選取 NodePort 可在外部公開埠（建議使用），而選取 ClusterIP 則不會公開埠。此版本不支援 LoadBalancer 及 ExternalName。| NodePort | 是 |
| `狀態資料庫`| 用來儲存頻道分類帳的[狀態資料庫](../glossary.html#state-database)。對等節點需要使用與[區塊鏈網路](../v10_dashboard.html#network-preferences)相同的資料庫。| 無 | 是 |
|`CouchDB 映像檔儲存庫`| 只有在已選取 CouchDB 作為分類帳資料庫時才適用。此欄位會自動填寫至已安裝的路徑。如果您使用 Community Edition，而且沒有網際網路存取權，則它應該符合您下載 Fabric CouchDB 映像檔的目錄。| ibmcom/ibp-fabric-couchdb | 是 |
| `CouchDB Docker 映像檔標籤`| 只有在已選取 CouchDB 作為分類帳資料庫時才適用。與 CouchDB 映像檔相關聯的標籤值。| 自動填入正確值。| 是 |
| `已啟用對等節點資料持續性`| 啟用在叢集重新啟動或失敗之後持續保存資料的能力。如需相關資訊，請參閱 [Kubernetes 中的儲存空間 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/ "磁區")。*如果不勾選，則會在發生失效接手或 Pod 重新啟動時遺失所有資料。*| 已勾選 | 否 |
| `對等節點使用動態佈建`| 勾選以啟用儲存空間磁區的動態佈建。| 已勾選 | 否 |
| `對等節點持續性磁區要求`| 僅適用於新要求。輸入要建立的新「持續性磁區要求 (PVC)」名稱。| my-data-pvc | 否 |
| `對等節點儲存空間類別名稱`| 指定對等節點的儲存空間類別名稱。| 如果建立新的 PVC，則為空白；否則，請指定與現有 PVC 相關聯的儲存空間類別。| 否 |
| `對等節點現有磁區要求`| 指定現有「磁區要求」的名稱，並將所有其他欄位保留為空白。| 無 | 否 |
| `對等節點選取器標籤`| [PVC 的選取器標籤 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "標籤與選取器")。| 無 | 否 |
| `對等節點選取器值`| [PVC 的選取器值 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "標籤與選取器")。| 無 | 否 |
| `對等節點儲存空間存取模式`| 指定 PVC 的儲存空間[存取模式 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "存取模式")。| ReadWriteMany| 否 |
| `對等節點磁區要求大小`|「磁區要求」的大小，必須大於 2Gi。| 8Gi  | 是 |
| `狀態資料庫持續性磁區要求`| 僅適用於新要求。輸入要建立的新「持續性磁區要求 (PVC)」名稱。| statedb-pvc | 否 |
| `狀態資料庫儲存空間類別名稱`| 指定狀態資料庫的儲存空間類別名稱。| 無 | 否 |
| `狀態資料庫現有磁區要求`| 指定現有「磁區要求」的名稱，並將所有其他欄位保留為空白。| 無 | 否 |
| `狀態資料庫選取器標籤`| PVC 的[選取器標籤](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| 無 | 否 |
| `狀態資料庫選取器值`| PVC 的[選取器值](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| 無 | 否 |
| `狀態資料庫儲存空間存取模式`| 指定 PVC 的儲存空間[存取模式 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "存取模式")。| ReadWriteMany| 否 |
| `狀態資料庫磁區要求大小`| 選擇要使用的磁碟大小。| 8Gi | 是 |
| `CouchDB - 已啟用資料持續性`| 若為 CouchDB 容器，當容器重新啟動時，分類帳資料將可供使用。*如果不勾選，則會在發生失效接手或 Pod 重新啟動時遺失所有資料。*| 已勾選 | 否 |
| `CouchDB - 使用動態佈建`| 若為 CouchDB 容器，請使用 Kubernetes 動態儲存空間。| 已勾選 | 否 |
| `對等節點 CPU 要求` | 要配置給對等節點的 CPU 數目下限。| 1 | 是 |
| `對等節點 CPU 限制` | 要配置給對等節點的 CPU 數目上限。| 2 | 是 |
| `對等節點記憶體要求` | 要配置給對等節點的記憶體數量下限。| 1Gi | 是 |
| `對等節點記憶體限制` | 要配置給對等節點的記憶體數量上限。| 4Gi | 是 |
| `CouchDB CPU 要求` | 要配置給 CouchDB 的 CPU 數目下限。| 1 | 是 |
| `CouchDB CPU 限制` | 要配置給 CouchDB 的 CPU 數目上限。| 2 | 是 |
| `CouchDB 記憶體要求` | 要配置給 CouchDB 的記憶體數量下限。| 1Gi | 是 |
| `CouchDB 記憶體限制` | 要配置給 CouchDB 的記憶體數量上限。| 4Gi | 是 |


選取 CouchDB 作為對等節點資料庫時，會在 Pod 中建立兩個容器，一個用於對等節點，一個用於 CouchDB。「對等節點」容器包括「對等節點 PVC」的單一磁區裝載，其會將區塊及交易儲存在檔案系統上。CouchDB 容器會裝載對等節點狀態資料庫 PVC，其中包含分類帳資料。

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### 使用 Helm 指令行來安裝 Helm 版本
{: #icp-helm-cli}

或者，您也可以使用 `helm` CLI 來安裝 Helm 版本。執行 `helm install` 指令之前，請確定您[將叢集的 Helm 儲存庫新增至 Helm CLI 環境 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "將內部 Helm 儲存庫新增至 Helm CLI")。

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
--values peer-s390x-values.yaml \
--tls
```

您可以編輯所下載保存檔中包含的 `values.yaml` 來建立新的 `yaml` 檔案，其中包括所有必要的參數及其預設值。

## 驗證對等節點安裝
{: #verify-installation-icp}

完成配置參數並按一下**安裝**按鈕之後，請按一下**檢視 Helm 版本**按鈕，以檢視您的部署。如果已順利完成，您應該會在「部署」表格的 `DESIRED`、`CURRENT`、`UP TO DATE` 及 `AVAILABLE` 欄位中看到值 1。您可能需要按一下「重新整理」，並等待更新表格。您也可以按一下 ICP 主控台左上角的**功能表**圖示，來尋找「部署」表格。從功能表清單中，按一下**工作負載**，然後按一下 **Helm 版本**。

如果您往下捲動至`附註`區段，則會有您在[操作對等節點](peer_operate_icp.html)時將使用的重要資訊。

## 檢視對等節點日誌
{: #peer-deploy-view-logs}

您可以使用 [kubectl CLI 指令](peer_operate_icp.html#peer-kubectl-configure)，或透過 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "您的「彈性搜尋」視窗")，來檢視對等節點日誌。如需相關資訊，請參閱這些[存取日誌指示](peer_operate_icp.html#peer-icp-view-logs)。

## 下一步為何？

部署對等節點之後，您需要完成數個作業步驟，然後才能將交易提交至區塊鏈網路，並從區塊鏈網路讀取分散式分類帳。如需相關資訊，請參閱[使用多雲端網路操作對等節點](peer_operate_icp.html)。

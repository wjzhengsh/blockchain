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

# 在 {{site.data.keyword.cloud_notm}} Private 上部署憑證管理中心
{: #CA-deploy}

***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

匯入 {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private (ICP) Helm 圖表之後，您可以部署個別元件。「憑證管理中心 (CA)」是信任您組織的根源，可讓您為將部署的其他元件產生認證。因此，在部署其他元件之前，您需要部署 CA。多雲端區塊鏈網路中的每個組織都必須部署自己的 CA。如需 CA 及其在區塊鏈網路中所扮演角色的相關資訊，請參閱[憑證管理中心](../blockchain_component_overview.html#ca)。
{:shortdesc}

部署「憑證管理中心」之前，請檢閱[考量及限制](../ibp-for-icp-about.html#ibp-icp-considerations)。

## 所需資源
{: #ca-resources-required}

請確定您的 ICP 叢集符合最低硬體資源需求：

| 元件      | vCPU | RAM | 資料儲存空間的磁碟    |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |

**附註：**
- 如果未針對虛擬機器分割伺服器，則 vCPU 是指派給虛擬機器或實體處理器核心的虛擬核心。當您決定部署在 ICP 中的虛擬處理器核心 (VPC) 時，需要考量 vCPU 需求。VPC 是決定 IBM 產品授權成本的度量單位。如需決定 VPC 之情境的相關資訊，請參閱[虛擬處理器核心 (VPC) ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "虛擬處理器核心")。
- 資料儲存需求取決於儲存的身分及憑證數目。CA 儲存空間不像對等節點或排序節點儲存空間一樣大量，但這視使用案例而定。使用者越多，需要的儲存空間就越多。
- 這些最低資源層次對於測試和實驗而言便已足夠。若為具有大量交易的環境，請務必針對 CA 配置夠大的儲存空間數量。要使用的儲存空間數量將視網路所需的交易數目及簽章數目而定。如果您耗盡 CA 上的儲存空間，則必須部署具有較大檔案系統的新 CA，並讓它透過相同頻道上的其他 CA 進行同步。

## 儲存空間
{: #storage}

您需要判斷 CA 將使用的儲存空間。如果您使用預設值，則 Helm 圖表將為您的 CA 建立新的 2 Gi 持續性磁區要求 (PVC)，名稱為 `fabric-ca-pvc`。

如果您不想使用預設儲存空間設定，請確定在 ICP 安裝期間設定*新的* `storageClass`，否則 Kubernetes 系統管理者需要在您部署 CA 之前建立 storageClass。

您可以選擇在 AMD64 或 S390X 平台上部署 CA。不過，請注意，[動態佈建](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)只適用於 ICP 中的 AMD64 節點。如果您的叢集混合了 S390X 及 AMD64 工作者節點，則無法使用動態佈建。

如果您未使用動態佈建，則必須使用可用來修正 Kubernetes PVC 連結處理程序的標籤，建立及設定[持續性磁區 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。

## 部署 CA 的必要條件
{: #prerequisites-ca-icp}

1. 您必須先[安裝 ICP](../ICP_setup.html) 以及[安裝 {{site.data.keyword.blockchainfull_notm}} Platform Helm 圖表](helm_install_icp.html)，才能在 ICP 上安裝 CA。

2. 如果您使用 Community Edition，而且想要在沒有網際網路連線功能的 ICP 叢集上執行此 Helm 圖表，則需要先在連接網際網路的機器上建立保存檔，才能在 ICP 叢集上安裝保存檔。如需相關資訊，請參閱[將精選應用程式新增至沒有網際網路連線功能的叢集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "將精選應用程式新增至沒有網際網路連線功能的叢集"){:new_window}。請注意，您可以在 Helm 圖表的 ibm-blockchain-platform-dev/ibm_cloud_pak 下找到規格檔案 manifest.yaml。

3. 從 ICP 主控台擷取叢集 Proxy IP 位址的值。**附註：**您必須是[叢集管理者 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "叢集管理者角色及動作")，才能存取 Proxy IP。請登入 ICP 叢集。在左導覽畫面中，按一下**平台**，然後按一下**節點**以檢視叢集中所定義的節點。按一下具有 `Proxy` 角色的節點，然後從表格中複製`主機 IP` 的值。**重要事項：**請儲存此值，您將在配置 Helm 圖表的 `Proxy IP` 欄位時加以使用。

4. 建立 CA 管理使用者名稱及密碼，並將它們儲存至 ICP 的密碼物件內。您可以在[下節](#admin-secret)找到建立密碼的步驟。

## 建立 CA 管理者密碼
{: #admin-secret}

第一次啟動 CA 時，它會包含您建立來操作 CA 的管理者身分。在部署 CA 之前，您必須先建立這個管理者身分的使用者名稱及密碼。請務必記下這些值。稍後當您操作 CA 以及部署其他網路元件時，就會使用它們。建立 [Kubernetes 密碼 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/configuration/secret/ "密碼")，以儲存管理者 `username` 及 `password` 來進行 CA 部署。Kubernetes 密碼可讓您保護及共用資訊，而不必將資訊直接傳遞至部署。

1. 建立管理使用者名稱及密碼，並以 base64 格式編碼值。在終端機視窗中執行下列指令，並將 `admin` 及 `adminpw` 的值取代為您要使用的值。
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **重要事項：**請儲存上方所建立的 `admin` 及 `adminpw` 值。也請儲存這些欄位的已編碼值，這些欄位是您建立 Kubernetes 密碼物件時的必要項目。

2. 登入 ICP 主控台。在左導覽畫面中，按一下**配置**，然後按一下**密碼**。按一下**建立密碼**按鈕來開啟蹦現畫面，讓您能夠產生新的密碼物件。

3. 在**一般**標籤上，完成下列欄位：
  - **名稱：**為您的密碼提供叢集內的唯一名稱。您將使用此名稱來配置 CA。此名稱必須全部為小寫。
  - **名稱空間：**要新增密碼的名稱空間。請選取您要在其中部署 CA 的`名稱空間`。
  - **類型：**輸入 `Opaque` 值。

4. 將**註釋**標籤保留空白。

5. 在**資料**標籤中，新增使用者名稱及密碼作為鍵值組。
  1. 在第一個**名稱**欄位中，輸入 `ca-admin-name`。
  2. 在第一個**值**欄位中，輸入上一個步驟的 `echo -n admin | base64` 結果。
  3. 按一下**新增資料**按鈕，以新增第二個鍵值組。
  4. 在第二個**名稱**欄位中，輸入 `ca-admin-password`。
  5. 在第二個**值**欄位中，輸入上一個步驟的 `echo -n adminpw | base64` 結果。  
    **圖 1** 會根據您針對 `ca-admin-name` 及 `ca-admin-password` 索引鍵所指定的值，顯示 ICP 主控台密碼可能包含的內容。

    ![ICP 主控台密碼](../images/CreateCASecret.png "ICP 主控台密碼")  
    *圖 1. ICP 主控台密碼*
  6. 按一下**建立**，以形成新的「密碼」物件。

CA Helm 圖表需要您使用名稱 `ca-admin-name` 及 `ca-admin-password`，作為將密碼部署至 Helm 版本的金鑰值。

**附註：**刪除 Helm 版本時，不會從您的 ICP 叢集中移除 CA 管理者密碼。您要負責管理您 ICP 叢集中的密碼。如果計劃未來部署另一個 CA，則可以重複使用 CA 管理者密碼。否則，您要負責從 ICP 叢集中予以刪除。

## 配置
{: #ca-configuration}

建立 CA 管理者密碼之後，您可以使用下列步驟來配置及安裝 CA。一次只能安裝一個 CA。

1. 登入 ICP 主控台，然後按一下右上角的**型錄**鏈結。
2. 如果您已下載 Community Edition，則請按一下左導覽畫面中的 `Blockchain`，以找到標示 `ibm-blockchain-platform-prod` 或 `ibm-blockchain-platform-dev` 的磚。按一下此磚加以開啟，您就可以看到 Readme 檔，其中包括安裝及配置 Helm 圖表的相關資訊。
3. 按一下畫面頂端的**配置**標籤，或按一下右下角的**配置**按鈕。
4. 指定[一般配置參數](#ca-global-parameters)的值，並接受授權合約。
5. 開啟`所有參數`小三角形，並指定[廣域配置參數](#ca-global-parameters)的值。
6. 向下捲動至 **CA 配置**區段。選取`安裝 CA` 勾選框，並完成 CA 的[配置設定](#ca-parameters)。  
7. 按一下**安裝**。

### 配置參數
{: #icp-ca-configuration-parms}

下表列出 {{site.data.keyword.blockchainfull_notm}} Platform 的可配置參數（**CA 元件特有**）及其預設值。  

**雖然 Helm 圖表使用者介面指出不需要進一步配置，但您需要輸入特定參數來部署 CA。**

#### 一般及廣域配置參數
{: #ca-global-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
| `Helm 版本名稱`| Helm 版本的名稱。開頭必須為小寫字母、結尾必須為任何英數字元，而且只能包含連字號及小寫英數字元。每次您嘗試安裝元件時，都必須使用唯一的 Helm 版本名稱。| 無 | 是 |
| `目標名稱空間`| 選擇 Kubernetes 名稱空間來安裝 Helm 圖表。| 無 | 是 |
| **廣域配置**| 適用於 Helm 圖表中所有元件的參數 |||
| `服務帳戶名稱`| 輸入您將用來執行 Pod 的[服務帳戶 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "配置 Pod 的服務帳戶") 名稱。| 預設值 | 否 |

#### CA 配置參數
{: #ca-parameters}

|  參數     | 說明    | 預設值  | 必要 |
| --------------|-----------------|-------|------- |
| `安裝 CA`| 選取以安裝 CA。| 不勾選 | 是（如果您要部署 CA 的話） |
| `CA 名稱`| 指定要用於「憑證管理中心」的名稱。**重要事項：**請記下此值。稍後當您配置排序節點或對等節點時，其為必要項目。| SampleOrgCA | 是 |
| `CA 工作者節點架構`| 選取您的 ICP 工作者節點架構（ADM64 或 S390X）。| AMD64 | 是 |
| `CA 資料庫類型`| 要儲存 CA 資料的資料庫類型。僅支援 SQLite。| SQLite | 是 |
| `CA 已啟用資料持續性` | 如果已勾選，則當容器重新啟動時，資料將可供使用。否則，會在進行失效接手或 Pod 重新啟動時遺失所有資料。| 已勾選 | 否 |
| `CA 使用動態佈建` | 勾選以啟用儲存空間磁區的動態佈建。| 已勾選 | 否 |
| `CA 儲存空間類別名稱`| 指定唯一的儲存空間類別名稱。否則，會使用叢集中預設的儲存空間類別。| 無 | 取決於 ICP 叢集的配置方式。請洽詢您的叢集管理者 |
| `CA 現有磁區要求`| 指定現有「磁區要求」的名稱，並將所有其他欄位保留為空白。| 無 | 否 |
| `CA 選取器標籤`| [PVC 的選取器標籤 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "標籤與選取器")。| 無 | 否 |
| `CA 選取器值`| [PVC 的選取器值 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "標籤與選取器")。| 無 | 否 |
| `CA 儲存空間存取模式`| 指定 PVC 的儲存空間[存取模式](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "存取模式")。| ReadWriteMany | 是 |
| `CA 磁區要求大小`| 選擇要使用的磁碟大小。| 2Gi | 是 |
| `CA 映像檔儲存庫`| CA Helm 圖表的位置。| ibmcom/ibp-fabric-ca | 是 |
| `CA Docker 映像檔標籤`| 與 CA 映像檔相關聯的標籤值。此欄位會自動填寫至映像檔版本。請勿變更它。| 1.2.1 | 是 |
| `CA 服務類型` | 用來指定在對等節點上是否[應該公開外部埠 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)。選取 NodePort 可在外部公開埠（建議使用），而選取 ClusterIP 則不會公開埠。此版本不支援 LoadBalancer 及 ExternalName | NodePort | 是 |
| `CA 密碼（必要）`| 輸入您為 `ca-admin-name` 及 `ca-admin-password` 建立的 Kubernetes 密碼物件名稱。| 無 | 是 |
| `CA CPU 要求`| 指定要配置給 CA 的 CPU 數目下限。| 1 | 是 |
| `CA CPU 限制`| 指定要配置給 CA 的 CPU 數目上限。| 2 | 是 |
| `CA 記憶體要求`| 指定要配置給 CA 的記憶體數量下限。| 1Gi | 是 |
| `CA 記憶體限制`| 指定要配置給 CA 的記憶體數量上限。| 4Gi | 是 |
| `CA TLS 實例名稱`| 指定將用來登記排序節點或對等節點的 CA TLS 實例名稱。| tlsca | 是 |
| `CSR 通用名稱`| 指定在聯絡時，產生的 CA 主要憑證將呈現的「通用名稱 (CN)」。| tlsca-common | 是 |
| `Proxy IP`| 輸入 CA 部署所在[叢集的 Proxy 節點 IP ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install_proxy.html "受 HTTP Proxy 保護的 IBM Cloud Private 安裝")。| 127.0.0.1 | 是 |


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
--values ca-s390x-values.yaml \
--tls
```

您可以編輯所下載保存檔中包含的 `values.yaml`，來建立新的 `yaml` 檔案。`values.yaml` 檔案包括所有必要參數及其預設值。

## 驗證 CA 安裝
{: #verifiying-ca-installation}

完成配置參數並按一下**安裝**按鈕之後，請按一下**檢視 Helm 版本**按鈕，以檢視您的部署。如果已順利完成，您應該會在「部署」表格的 `DESIRED`、`CURRENT`、`UP TO DATE` 及 `AVAILABLE` 欄位中看到值 1。您可能需要按一下「重新整理」，並等待更新表格。您也可以按一下 ICP 主控台左上角的**功能表**圖示，來尋找「部署」表格。從功能表清單中，按一下**工作負載**，然後按一下 **Helm 版本**。

如果您向下捲動至`附註`區段，則可以找到將用來[操作 CA](CA_operate.html#ca-operate) 的重要資訊。

在 ICP 中安裝 {{site.data.keyword.blockchainfull_notm}} Platform CA 之後，會使用預設環境變數設定來建立 configmap。然後，您可以變更或新增 CA 伺服器的環境變數，以配置其行為。如需 CA 伺服器配置參數的相關資訊，請參閱 [Fabric CA 伺服器文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-server "Fabric CA 伺服器")。

配置 configmap 之後，您需要先重新啟動 CA 伺服器，變更才會生效。若要重新啟動 CA 伺服器，您可以刪除 Fabric CA 伺服器 POD。ICP 將會建立可反映變更的新 POD。

## 檢視 CA 日誌
{: #ca-deploy-view-logs}

您可以從指令行檢視元件日誌，方法是使用 [`kubectl CLI 指令`](CA_operate.html#ca-kubectl-configure)或透過您 ICP 叢集中所包含的 [Kibana ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/products/kibana "彈性搜尋件中的視窗")。如需相關資訊，請參閱這些[存取日誌指示](CA_operate.html#ca-operate-view-logs)。

## 操作 CA
{: #operate-ca}

CA 將成為信任您組織的根源。您需要使用 CA 產生其他元件的憑證。因此，在部署排序節點或對等節點之前，您必須先[設定 CA 並完成數個作業步驟](CA_operate.html)

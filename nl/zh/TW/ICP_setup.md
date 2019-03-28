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

# 設定 IBM Cloud Private (ICP)


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


在您部署 {{site.data.keyword.blockchainfull}} Platform 元件並在 {{site.data.keyword.cloud_notm}} Private (ICP) 上建置區塊鏈網路之前，需要在自己的環境中設定 ICP。
{:shortdesc}

## 必要條件
{: #icp-setup-prerequisites}
請完成下列必要條件，並準備環境來安裝 ICP。

### Docker
{{site.data.keyword.cloud_notm}} Private 需要安裝 Docker。請遵循[安裝 {{site.data.keyword.cloud_notm}} Private ![外部鏈結圖示](/images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html "安裝 {{site.data.keyword.cloud_notm}} Private") 中的相關指示來安裝 Docker。

### ICP 設定
在安裝 ICP 之前，下列提示有助於準備您的節點以進行 ICP 安裝。在 [ICP 文件 ![外部鏈結圖示](/images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "準備叢集以進行安裝") 中，可以找到其他的 ICP 必要條件。

#### 更新 `vm.max_map_count` 設定
ICP 使用「彈性搜尋」進行記載及計量。若要避免記憶體不足的異常狀況，「彈性搜尋」需要配置 `vm.max_map_count` 系統內容。安裝 ICP 之前，請參閱 [彈性搜尋配置指示 ![外部鏈結圖示](/images/external_link.svg "外部鏈結圖示")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "虛擬記憶體") 以在每個節點上配置此內容。您可以使用下列指令，永久地設定此內容：

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### 在叢集的每個節點上配置 `/etc/hosts` 檔案

- ICP 使用 [Kubernetes ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "瞭解 Kubernetes 基本概念") 來管理容器化應用程式。如果每個節點的 `/etc/hosts` 檔案中未配置主機名稱，則「Kubernetes 網域名稱伺服器 (DNS)」會失敗。請[插入叢集中每個節點的 IP 位址、主機名稱及簡稱 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "配置叢集") 至每個節點上的 `/etc/hosts` 檔案。

- [ICP 不支援 IPv6 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "不支援 IPv6")。若要避免 ICP 叢集中發生 DNS 服務問題，請停用每個節點上 `/etc/hosts` 檔案中的 IPv6 設定，方法是在行首使用 `#` 符號註銷下行：
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### 所需資源
{: #helm-icp-resources}

請確定您的 ICP 系統符合最低硬體資源需求：

| 元件 | vCPU | RAM | 資料儲存空間的磁碟 |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |
|排序節點 (Orderer)| 2 | 512 MB | 100 GB（可擴充）|
|對等節點 (Peer)| 2 | 2 GB | 50 GB（可擴充）|
| 對等節點的 CouchDB | 2 | 2 GB | 50 GB（可擴充）|

 **附註：**
 - 如果未針對虛擬機器分割伺服器，則 vCPU 是指派給虛擬機器或實體處理器核心的虛擬核心。當您決定部署在 ICP 中的虛擬處理器核心 (VPC) 時，需要考量 vCPU 需求。VPC 是決定 IBM 產品授權成本的度量單位。如需決定 VPC 之情境的相關資訊，請參閱[虛擬處理器核心 (VPC) ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)。
 - 這些最低資源層次對於測試和實驗而言便已足夠。若為具有大量交易的環境，請務必配置夠大的儲存空間數量；例如，250 GB 用於對等節點，而 500 GB 用於排序服務。要使用的儲存空間數量將視網路所需的交易數目及簽章數目而定。如果您即將耗盡對等節點或排序節點上的儲存空間，則必須部署具有較大檔案系統的新對等節點或排序節點，並讓它透過相同頻道上的其他元件進行同步。

#### 儲存空間考量
{: #helm-icp-storage-considerations}

* 您需要判斷元件將使用的儲存空間。如果您使用預設值，則「對等節點」Helm 圖表會為您的對等節點資料建立新的「持續性磁區」要求，名稱為 `my-data-pvc`。如果您選取 CouchDB 作為分類帳資料庫，則 Helm 圖表會為您的分類帳資料庫建立另一個「持續性磁區」要求，名稱為 `statedb-pvc`。
* 如果您不想使用預設儲存空間設定，請確定在 ICP 安裝期間設定*新的* storageClass，否則 Kubernetes 系統管理者需要在您部署之前建立 storageClass。
* [動態佈建 ![外部鏈結圖示](/images/external_link.svg "外部鏈結圖示")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "動態磁區佈建") 只適用於 ICP 中的 amd64 節點。因此，如果您的叢集混合了 s390x 及 amd64 工作者節點，則無法使用動態佈建。
* 如果未使用動態佈建，則必須使用可用來修正「Kubernetes 持續性磁區要求 (PVC)」連結處理程序的標籤，建立及設定[持續性磁區 ![外部鏈結圖示](/images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "持續性磁區")。
* 如果您使用「NFS 第 2 版/第 3 版持續性磁區」，則必須在 NFS 檔案系統所在的主機系統上啟用 **NFSv2/v3 檔案系統鎖定的 NFS 狀態監視器**模組（也稱為 **rpc-statd**）。此模組可讓您的 NFS 檔案系統檢查其他處理程序所保留檔案的專用鎖定。請執行下列指令以啟用此模組：
  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## 安裝 ICP
{: #icp-setup-install}

請完成下列步驟，以在環境中安裝及設定 {{site.data.keyword.cloud_notm}} Private。

1. 安裝 3.1.0 版的 [IBM Cloud Private ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) 叢集。如果您要使用 Helm 圖表進行開發、測試或實驗，則可以免費安裝 [ICP Community Edition 3.1.0 版 ![外部鏈結圖示](/images/external_link.svg "外部鏈結圖示")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE 3.1.0 版")。

2. 安裝 IBM Cloud Private CLI [3.1.0 ![外部鏈結圖示](images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html) 來安裝及操作 CA。

安裝 ICP 之後，您可以繼續[匯入 {{site.data.keyword.blockchainfull_notm}} Platform for ICP Helm 圖表](howto/helm_install_icp.html)至 ICP 叢集。

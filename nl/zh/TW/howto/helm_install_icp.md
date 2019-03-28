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

# 在 {{site.data.keyword.cloud_notm}} Private 上安裝 {{site.data.keyword.blockchainfull_notm}} Platform
{: #remote-peer-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private 提供為可在本端 ICP 叢集中安裝的 Helm 圖表檔案。安裝 Helm 圖表之後，您可以在 ICP 型錄中找到 {{site.data.keyword.blockchainfull_notm}} Platform 作為應用程式。

安裝 {{site.data.keyword.blockchainfull_notm}} Platform for ICP 之前，請檢閱[考量及限制](../ibp-for-icp-about.html#ibp-icp-considerations)。如需如何部署 Helm 圖表、定價及支援中所含區塊鏈元件的相關資訊，請參閱[關於 {{site.data.keyword.blockchainfull_notm}} Platform for ICP](../ibp-for-icp-about.html)。

{{site.data.keyword.blockchainfull_notm}} Platform for ICP 提供兩個版本：

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 是透過 Passport Advantage (PPA) 提供。您必須具備必要授權才能存取 [Passport Advantage Online ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online")。購買時，其中包括 {{site.data.keyword.blockchainfull_notm}} Platform 的技術支援。

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition 是免費的供應項目，用於探索、開發及測試。您可以透過 [GitHub ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/圖表") 來存取此免費版本。請注意，{{site.data.keyword.IBM_notm}} 未提供 Community Edition 的支援。

## 安裝 Helm 圖表的必要條件
{: #helm-install-prereqs}

安裝 Helm 圖表之前，您必須已配置 ICP 叢集。請檢閱[設定及配置 ICP 叢集](../ICP_setup.html)的指示。

## 安裝受防火牆保護的 {{site.data.keyword.blockchainfull_notm}} Platform
{: #helm-install-prereqs-firewall}

您可以在沒有網際網路連線功能的情況下，部署受防火牆保護的 {{site.data.keyword.blockchainfull_notm}} Platform 元件。PPA 套件包括 {{site.data.keyword.blockchainfull_notm}} Platform 將使用的所有 Fabric 元件 Docker 映像檔，因此您不需要在部署期間從 DockerHub 下載這些映像檔。

不過，Community Edition Helm 圖表未包括必要的 Fabric 元件 Docker 映像檔，因為此版本配置成在部署期間從 DockerHub 下載這些映像檔。如果沒有可用的網際網路連線功能，則部署會失敗。因此，您需要完成額外的步驟以先在連接網際網路的機器上建立保存檔，才能在 ICP 叢集上安裝保存檔。下列是必要的映像檔：
- [Fabric 對等節點 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic 對等節點")
- [Fabric CA ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric 排序節點 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric 排序節點")
- [Fabric Couch 資料庫 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric Couch 資料庫")
- [用來引導及配置元件（包括 MSP 資料夾）的 `Init` 映像檔 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [對等節點用來執行鏈碼容器的 Docker-in-Docker (DinD) 映像檔 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hub.docker.com/r/ibmcom/ibp-dind/)

如需如何使用這些映像檔的相關資訊，請參閱[將精選應用程式新增至沒有網際網路連線功能的叢集 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html)。請注意，您可以在 Helm 圖表的 `ibm-blockchain-platform-dev/ibm_cloud_pak` 目錄下找到規格檔案 `manifest.yaml`。

## 將 Helm 圖表匯入 ICP

1. 從 [Passport Advantage Online ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") 下載 IBM Blockchain Platform for ICP 的 Helm 圖表檔案，或從 [GitHub ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/圖表") 下載免費 Community Edition 的 Helm 圖表檔案。此 Helm 圖表套件包含 CA、排序節點及對等節點的三個子 Helm 圖表。

2. 如果還沒有登入 ICP 叢集，請登入該叢集。

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. 確定已配置 [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html)。在您配置 Docker CLI 之後，請使用下列指令來存取叢集上的映像檔登錄：
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. 使用下列指令，以尋找 ICP 中要上傳 Helm 圖表的儲存庫名稱：
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  當此指令順利完成時，您可以看到叢集中的儲存庫清單。請選擇目標儲存庫的名稱並予以儲存。您需要在下面的指令中使用它。

6. 使用指令行匯入 Helm 圖表。您執行以匯入 Helm 圖表的指令取決於 Helm 圖表是從 Passport Advantage (PPA) 還是 GitHub 下載。

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    如果已從 PPA 下載 Helm 圖表，則請遵循下列指示。在您儲存從 PPA 所下載之 Helm 圖表套件的目錄中，於 ICP CLI 中執行下列指令，將 Helm 圖表匯入您的 ICP 叢集。

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    取代下列值：
    - `<archive-name>` 取代為所下載之 `.tgz` 檔案的名稱。
    - `<cluster_CA_domain>:8500` 取代為您用來登入 ICP 叢集的網域。
    - `<repo-name>` 取代為您要上傳圖表的 Helm 儲存庫。請執行 'cloudctl catalog repos' 以列出儲存庫。

    當此指令順利完成時，您可以看到類似下列資訊的內容：
    ```
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      Processing image: ibmcom/icp-dind-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      Processing image: ibmcom/icp-dind-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-0.1.5.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
  ```

  - **從 GitHub 下載的 Community Edition**
    如果已從 GitHub 下載 Helm 圖表，則請遵循下列指示。

    在您儲存從 GitHub 所下載之 Helm 圖表套件的目錄中，於 ICP CLI 中執行下列指令，將 Helm 圖表匯入您的 ICP 叢集。若要匯入從 GitHub 下載的 Helm 圖表，請執行下列指令：
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    取代下列值：
    - `<helm_chart_from_github>` 取代為您所下載之 .tgz 檔案的名稱。
    - `<repo-name>` 取代為您要上傳圖表的 Helm 儲存庫。請執行 'cloudctl catalog repos' 以列出儲存庫。

    當此指令順利完成時，您可以看到類似下列資訊的內容：
    ```
      Loading helm chart
  Loaded helm chart

  

    Synch charts
  Synch started
  OK
  ```

按一下 ICP 主控台中的**型錄**按鈕，然後按一下左導覽畫面中的 **Blockchain**，以驗證匯入順利完成。如果順利完成，則應該會在「ICP 型錄」頁面上看到 **ibm-blockchain-platform-prod** 或 **ibm-blockchain-platform-dev** 磚。


## PodSecurityPolicy 需求

將 Helm 圖表匯入 {{site.data.keyword.cloud_notm}} Private 之後，您需要先將 [PodSecurityPolicy ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Pod 安全原則") 連結至目標名稱空間，再安裝元件。請選擇預先定義的 PodSecurityPolicy 或讓您的叢集管理者為您建立自訂的 PodSecurityPolicy：
- 預先定義的 PodSecurityPolicy 名稱：[`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- 自訂 PodSecurityPolicy 定義：
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- 自訂 PodSecurityPolicy 的自訂 ClusterRole：
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- 自訂 ClusterRole 的自訂 ClusterRoleBinding：
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## 部署個別元件

安裝 Helm 圖表之後，請按一下 ICP 型錄中的 **ibm-blockchain-platform-prod** 或 **ibm-blockchain-platform-dev** 磚予以開啟。您可以使用配置頁面來部署區塊鏈網路的任何個別元件。如需區塊鏈解決方案所需元件及其部署所需依循順序的詳細資料，請參閱 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 部署手冊](../ibp_for_icp_deployment_guide.html)。

然後，部署個別元件：

- 如果您要部署排序節點，則必須先設定排序節點的「憑證管理中心」。CA 會產生組織中的其他元件將使用的憑證。如需相關資訊，請參閱[在 ICP 中部署 {{site.data.keyword.blockchainfull_notm}} Platform 憑證管理中心](CA_deploy_icp.html)。然後，您可以部署將成為網路一般連結的排序節點。如需相關資訊，請參閱[在 ICP 中部署 {{site.data.keyword.blockchainfull_notm}} Platform 排序節點](orderer_deploy_icp.html)

- 如果您要部署對等節點，則必須先設定對等節點的「憑證管理中心」。CA 會產生對等節點將使用的憑證。如需相關資訊，請參閱[在 ICP 中部署 {{site.data.keyword.blockchainfull_notm}} Platform 憑證管理中心](CA_deploy_icp.html)。然後，當您準備好加入網路時，可以部署對等節點，以加入頻道、為交易背書，以及儲存資料。如需相關資訊，請參閱[在 ICP 中部署 {{site.data.keyword.blockchainfull_notm}} 對等節點](peer_deploy_icp.html)或[部署入門範本或企業方案網路的 {{site.data.keyword.blockchainfull_notm}} 對等節點](peer_deploy_ibp.html)（視對等節點將加入的區塊鏈網路而定）。

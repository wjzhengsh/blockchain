---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 在 {{site.data.keyword.cloud_notm}} Private 中操作遠端對等節點
{: #remote-peer-operate}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


在 {{site.data.keyword.cloud_notm}} Private (ICP) 上設定「{{site.data.keyword.blockchainfull}} 平台」遠端對等節點之後，您需要先完成數個作業步驟，然後對等節點才能發出交易，以查詢並呼叫區塊鏈網路的分類帳。這些步驟包括將組織新增至頻道、將遠端對等節點加入至頻道、在遠端對等節點上安裝鏈碼、在頻道上實例化鏈碼，以及將應用程式連接至遠端對等節點。
{:shortdesc}

您需要從「ICP 叢集」完成一些必要步驟，才能操作遠端對等節點。

**必要條件：**
- [擷取遠端對等節點端點資訊](#remote-peer-retrieve-endpoint-info)
- [使用 kubectl 登入對等節點容器](#remote-peer-kubectl-configure)
- [重新啟動遠端對等節點](#remote-peer-restart)

然後，您可以使用下列其中一種方法來操作遠端對等節點。

**作業路徑：**
- [使用 Fabric SDK](#remote-peer-operate-with-sdk)
- [使用指令行](#remote-peer-cli-operate)

雖然指示假設熟悉 SDK 的作業，但 Fabric SDK 是建議的路徑。

建議您至少部署遠端對等節點 Helm 圖表的兩個實例，以取得[高可用性](remote_peer_icp.html#high-availability)。因此，針對每一個對等節點，您需要遵循下列作業步驟一次。當您準備好從應用程式呼叫並查詢鏈碼時，請同時連接至兩個對等節點，以確保您的[應用程式高度可用](../v10_application.html#ha-app)。

**附註**：「{{site.data.keyword.blockchainfull_notm}} 平台」遠端對等節點無權存取完整功能，也不支援「{{site.data.keyword.blockchainfull_notm}} 平台」上管理的對等節點。因此，您無法使用「網路監視器」來操作遠端對等節點。在開始執行遠端對等節點之前，請確定檢閱[考量](remote_peer.html#remote-peer-limitations)。

## 必要條件

不論您計劃使用 SDK 還是指令行，您都需要在操作對等節點的課程中完成本節中的步驟。開始之前，您可以先完成前兩個步驟，即擷取對等端點及配置 kubectl。

### 擷取遠端對等節點端點資訊
{: #remote-peer-retrieve-endpoint-info}

登入 ICP 主控台。按一下左上角的**功能表**圖示。按一下**工作負載** > **部署**。然後，尋找並按一下遠端對等節點實例的名稱，以開啟部署概觀畫面。向下捲動至**公開詳細資料**區段，並尋找遠端對等節點的 `Ingress IP` 和 `Ports`。

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

請記下要連接至遠端對等節點的 Ingress IP 位址及「節點」埠。當您在遠端對等節點上安裝及實例化鏈碼時，需要使用此資訊。在下列範例中，您的對等節點位址是 `9.46.126.89:30162`、`9.46.126.89:30260` 或 `9.46.126.89:32051`。

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### 配置 kibectl CLI
{: #remote-peer-kubectl-configure}

您需要使用 **kubectl** 指令行工具，來處理在 ICP 中執行的對等節點容器內的一些必要作業。使用 {{site.data.keyword.cloud_notm}} Private CLI 工具登入叢集：

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

然後，登入「ICP 叢集」的儀表板。按一下 ICP 使用者介面右上角的使用者虛擬人像，然後按一下**配置用戶端**。在本端機器的終端機視窗中，複製、貼上及執行指令。您可以執行下列指令來驗證配置是否順利運作：

```
kubectl -n <namespace> get pods
```
{:codeblock}

其中 `-n <namespace>` 應該是在未使用 `default` 名稱空間時指定的。請將 `<namespace>` 取代為 ICP 叢集中名稱空間的值。


此指令應該會產生一個與下列範例類似的回應。`remotepeer-bd65c4769-95rmm` 是對等節點容器的名稱。

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

如果您無法辨識回應中的遠端對等節點 Pod 名稱，則可以在部署畫面的 **Pod** 窗格中尋找 Pod 名稱。

執行下列指令，並將 `<PEER_CONTAINER_NAME>` 的值取代為上述的 Pod 名稱，來執行至遠端對等節點容器。

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
其中 `-n <namespace>` 應該是在未使用 `default` 名稱空間時指定的。請將 `<namespace>` 取代為 ICP 叢集中名稱空間的值。

例如：

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

您現在可以使用對等節點容器 CLI。如果您使用的是 SDK 或指令行，則需要使用 CLI 來下載對等節點 TLS 憑證，並將簽署憑證上傳至對等節點。

## 重新啟動在 ICP 中執行的遠端對等節點
{: #remote-peer-restart}

每當將憑證新增至遠端對等節點時，需要重新啟動對等節點，它才能辨識新的憑證。

您可以使用 **kubbectl** 指令來重新啟動在 ICP 中執行的遠端對等節點。指令需要遠端對等節點 **Pod** 及其**容器**的名稱作為指令參數。如需使用 kubectl 指令的相關資訊，請參閱[配置 kibectl CLI](#remote-peer-kubectl-configure)。

1. 在 ICP 主控台中，尋找 **Pod** 名稱及**容器**名稱，以進行遠端對等節點部署。

  1. 按一下主控台左上角的功能表圖示，然後按一下**工作負載 > 部署**。
  2. 在表格中找出並按一下您的遠端對等節點版本，以開啟它。
  3. 向下捲動以檢視相關聯的 **Pod** 名稱。請記下 **Pod** 名稱。
  4. 按一下 Pod 以開啟它。
  5. 按一下**容器**標籤。請記下您的遠端對等節點**容器**名稱。

2. 從指令行中執行下列指令，來重新啟動對等節點，並將 `<REMOTE_PEER_POD_NAME>` 及 `<REMOTE_PEER_CONTAINER_NAME>` 取代為上述的值。

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. 您可以執行 `kubectl get pods` 指令，並檢查 Pod 的 **RESTART** 計數的輸出，以驗證遠端對等節點是否已重新啟動。

此外，您可以使用 [HEAD 要求](monitor_network.html#monitor-nodes)來檢查遠端對等節點的可用性。


## 使用 Fabric SDKs 來操作遠端對等節點
{: #remote-peer-operate-with-sdk}

Hyperledger Fabric SDK 提供一組功能強大的 API，可讓應用程式與區塊鏈網路互動，以及操作區塊鏈網路。您可以在 [Hyperledger Fabric SDK 社群文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 社群文件") 中，找到所支援語言的最新清單，以及 Fabric SDK 內可用 API 的完整清單。您可以使用 Fabric SDK，將遠端對等節點加入至「{{site.data.keyword.blockchainfull_notm}} 平台」上的頻道、在對等節點上安裝鏈碼，以及在頻道上實例化鏈碼。

下列指示會使用 [Fabric Node SDK ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Fabric Node SDK")，來操作遠端對等節點，並假設先前熟悉 SDK。您可以使用[開發應用程式指導教學](../v10_application.html)，來瞭解如何在開始之前使用 Node SDK，並引導您在準備好呼叫及查詢鏈碼時，使用遠端對等節點來開發應用程式。

### 安裝 Node SDK

您可以使用 NPM 來安裝 [Node SDK ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Node SDK")：
```
npm install fabric-client@1.1
```
{:codeblock}

建議您使用 1.1 版的 Node SDK。

### 準備 SDK 以使用遠端對等節點
{: #remote-peer-node-sdk}

在使用 SDK 來操作遠端對等節點之前，您需要產生必要的憑證（登記），讓您的應用程式能夠與「{{site.data.keyword.blockchainfull_notm}} 平台」及遠端對等節點上的網路通訊。使用您的**管理者**身分，遵循[使用 SDK 登記](../v10_application.html#enroll-app-sdk)的步驟。[開發應用程式](../v10_application.html)指導教學也會登記為**管理者**，因此您不需要修改範例程式碼。

### 將 signCert 上傳至 {{site.data.keyword.blockchainfull_notm}} 平台
{: #remote-peer-upload-sdk}

您需要將 SDK 簽署憑證上傳至「{{site.data.keyword.blockchainfull_notm}} 平台」上的網路，讓您的應用程式有權操作網路。

- 您可以在 SDK 產生加密資料的目錄中找到簽署憑證，其檔名為 admin。複製 `certificate` 欄位之後引號內的憑證，其以 `-----BEGIN CERTIFICATE-----` 開始，以 `-----END CERTIFICATE-----` 結束。您可以使用 CLI，藉由發出 `echo -e "<CERT>" > admin.pem` 指令，將憑證轉換成 PEM 格式。然後，您可以使用「網路監視器」將憑證的內容貼至區塊鏈網路。登入「{{site.data.keyword.blockchainfull_notm}} 平台」上的網路。在「網路監視器」的「成員」畫面上，按一下**憑證** > **新增憑證**。為您的憑證指定任何名稱，並將內容貼至**憑證**欄位。當系統詢問您是否要重新啟動對等節點時，請按一下**重新啟動**。必須在組織加入頻道之前執行此動作。

### 將 signCert 上傳至遠端對等節點
{: #remote-peer-upload-signcert}

您也需要將 SDK 的簽署憑證上傳至遠端對等節點，然後重新啟動它。您需要安裝遠端對等節點容器內您[上傳至 {{site.data.keyword.blockchainfull_notm}} 平台](#remote-peer-upload-sdk)的同一個簽署憑證。

從[對等節點容器內](#remote-peer-kubectl-configure)執行下列指令來上傳您的憑證。
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

將文字 `<CERT.PEM>` 取代為您的 signCert，其以 `-----BEGIN CERTIFICATE-----` 開始，以 `-----END CERTIFICATE-----` 結束，**請注意**，cert.pem 檔已存在於這個目錄中。將新的憑證命名為 `cert2.pem` 或類似名稱，以避免改寫已在容器內的憑證。

因為您已新增憑證，所以需要重新啟動對等節點的容器。請遵循下列[指示](#remote-peer-restart)來重新啟動對等節點。

### 將遠端對等節點的 TLS 憑證傳遞至 SDK
{: #icp-remote-peer-download-tlscert}

您需要將 TLS `cacert.pem` 的內容從對等節點容器複製到您的應用程式，以鑑別來自 SDK 的通訊。在[對等節點容器](#remote-peer-kubectl-configure)中，執行下列指令：
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

將 `cacert.pem` 檔的整個文字複製到剪貼簿，其以第一個 `-----BEGIN CERTIFICATE-----` 開始，以最後一個 `-----END CERTIFICATE-----` 結束。以 PEM 格式將此憑證儲存至本端檔案系統。然後，您可以使用簡單的讀取檔案指令，將 TLS 憑證匯入至應用程式。
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### 將遠端對等節點端點資訊提供給 SDK
{: #remote-peer-SDK-endpoints}

宣告新的對等節點變數或更新「連線設定檔」，來尋找[遠端對等節點的端點資訊](#remote-peer-retrieve-endpoint-info)，並將其提供給 SDK。下列範例將對等節點定義為光纖網路上的端點，並將您匯入的 TLS 憑證傳遞給它。
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

您需要指定 `<something>.blockchain.com` 的 `ssl-target-name-override`，對等節點才能解析 DNS 要求。

**附註：**因為對等節點是遠端，所以「{{site.data.keyword.blockchainfull_notm}} 平台」網路上的其他組織無法在其「連線設定檔」中找到對等端點的端點資訊。如果其他組織需要將背書的交易傳送至您的遠端對等節點，則您需要在頻外作業中提供「公用 IP」及 TLS 憑證。

### 使用 SDK 來加入至頻道
{: #remote-peer-join-channel-sdk}

身為區塊鏈網路的成員，您的組織需要先新增至網路中的頻道，然後才能將您的遠端對等節點加入至該頻道。

  - 您可以為遠端對等節點啟動新的頻道。作為頻道起始者，您可以在[建立頻道](create_channel.html#creating-a-channel)期間自動包含您的組織。請注意，您必須在「{{site.data.keyword.blockchainfull_notm}} 平台」至少具有一個對等節點，然後才能在「網路監視器」中建立頻道。  

  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。在「{{site.data.keyword.blockchainfull_notm}} 平台」上具有對等節點的頻道成員，可以使用「網路監視器」將您的組織新增至頻道，即使您沒有在平台上管理任何對等節點也一樣。

    將您的組織新增至頻道之後，您需要將對等節點的簽署憑證新增至頻道，以便其他成員可以在交易期間驗證您的數位簽章。在安裝期間，遠端對等節點會上傳其簽署憑證，以便您只需要將憑證同步化至頻道。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。

當您的組織是頻道的一部分時，請遵循[加入頻道](../v10_application.html#join-channel-sdk)的指示。您需要提供排序服務的 URL 及頻道名稱。

### 使用 SDK 在對等節點上安裝鏈碼
{: #remote-peer-install-cc-sdk}

使用下列指示，在遠端對等節點上使用 SDK 來[安裝鏈碼](../v10_application.html#install-cc-sdk)。

### 使用 SDK 在頻道上實例化鏈碼
{: #remote-peer-instantiate-cc-sdk}

只有一個頻道成員需要實例化或更新鏈碼。因此，在「{{site.data.keyword.blockchainfull_notm}} 平台」上具有對等節點之頻道的任何網路成員，都可以使用「網路監視器」，來實例化並指定背書原則。不過，如果您想要使用遠端對等節點來在頻道上實例化鏈碼，則可以使用 SDK，並遵循[實例化鏈碼](../v10_application.html#instantiate-cc-sdk)的指示。


## 使用 CLI 來操作遠端對等節點
{: #remote-peer-cli-operate}

您也可以使用 Fabric CA 用戶端和 Fabric 工具容器，從指令行操作遠端對等節點。在這些指示中，我們先使用「Fabric CA 用戶端」產生必要的憑證，然後使用 Fabric 工具容器來操作對等節點，方法是將其加入至頻道、安裝鏈碼，然後在頻道上實例化鏈碼。

### 使用 Fabric CA 用戶端登記
{: #peer-client-enroll}

第一個步驟是使用 Fabric CA 用戶端來產生必要的憑證（登記）。首先您需要安裝 Fabric CA 用戶端。將[適用於您平台的 fabric-ca 1.1.0 版二進位檔](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/)下載至本端機器、解壓縮它們，然後將其移至 `$HOME/fabric-ca-remote/` 這類的資料夾。

1.  準備環境以使用 Fabric CA 用戶端。切換至您已將用戶端二進位檔移至其中的目錄，以便您可以在指令中直接參照它。
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  設定用戶端可在其中建立金鑰的路徑。如果這是您第一次執行 `enroll` 指令，則 `msp` 資料夾與 `.yaml` 檔不存在。如果您之前已執行 `enroll` 指令，請務必使用下面的 `rm` 指令，從先前的登記嘗試中刪除任何配置資料：
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  您需要在「{{site.data.keyword.blockchainfull_notm}} 平台」上擷取「憑證管理中心」的端點資訊。在「網路監視器」的「概觀」畫面上，按一下**連線設定檔**按鈕，然後開啟包含網路認證的 JSON 檔案。向下捲動到 `certificateAuthorities` 區段，並記下下面的資訊。
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  您也需要從「{{site.data.keyword.blockchainfull_notm}} 平台」擷取 TLS 憑證，才能與 CA 通訊。上述網路認證包含必要的憑證。在「連線設定檔」檔案的 `certificateAuthorities` 區段中，複製位在 `"pem:"` 後面的憑證，其以 `-----BEGIN CERTIFICATE-----` 開始，以 `-----END CERTIFICATE----- ` 結束。**請不要包含引號**。

    從終端機視窗執行下列指令，將 `<certificate>` 取代為您複製的憑證。
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 將此憑證儲存在您可以在未來指令中參照它的位置。

5.  現在，您已準備好產生所需的憑證。執行下列指令：
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    例如：
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    當指令順利完成時，您可以看到類似下列範例的回應：
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    此指令會產生必要的憑證，並將它們儲存在 `$FABRIC_CA_CLIENT_HOME` 中名為 `msp` 的資料夾中。此資料夾與其內的憑證在下列步驟將很重要。

### 在本端系統上管理憑證
{: #manage-certs}

在可以操作遠端對等節點之前，我們需要在本端機器上執行一些憑證管理，並將 Fabric CA 用戶端產生的部分憑證上傳至「{{site.data.keyword.blockchainfull_notm}} 平台」及您的對等節點。我們也需要從「平台」和對等節點下載 TLS 憑證。如果您想要進一步瞭解將要使用的憑證，以及將要執行的作業，請參閱[在 {{site.data.keyword.blockchainfull_notm}} 平台上管理憑證](../certificates.html)。

在本端機器上，開啟指令終端機，並導覽至已將 Fabric-CA-Client 二進位檔移至其中，並儲存 MSP 資料夾的目錄。
1. 將 `cert.pem` 檔從 `signcerts` 資料夾複製到新的 `admincerts` 資料夾。  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. 您也需要將遠端對等節點的 TLS 憑證從 ICP 上的對等節點容器複製到本端機器。

  - [登入對等節點容器](#remote-peer-kubectl-configure)，並執行下列指令：
    ```
    cat /mnt/certs/tls/cacert.pem
    ```
    {:codeblock}

    將 `cacert.pem` 檔的整個文字複製到剪貼簿，其以第一個 `-----BEGIN CERTIFICATE-----` 開始，以最後一個 `-----END CERTIFICATE-----` 結束。

  - 切回本端機器，並執行下列指令。將文字 `<CACERT.PEM>` 取代為來自剪貼簿的文字。
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
    {:codeblock}

3. 若要提供 CLI 在對等節點上安裝鏈碼的授權，您需要將 Fabric CA 用戶端所產生的 signCert 上傳至對等節點 admin 資料夾，然後重新啟動對等節點。因此，將本端機器中的 `admincert/cert.pem` 憑證複製到對等節點容器中的 `/mnt/crypto/peer/peer/msp/admincerts/` 目錄，作為 `cert2.pem`。

    - 在本端機器上，執行下列指令：

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - 將 `cert.pem` 檔的文字複製到剪貼簿，其以 `-----BEGIN CERTIFICATE-----` 開始，以 `-----END CERTIFICATE-----` 結束。

    - 切回對等節點容器，並執行下列指令，將 cert.pem 新增至對等節點憑證。將文字 `<CERT.PEM>` 取代為來自剪貼簿的文字。

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **附註：**cert.pem 檔已存在於這個目錄中。請不要改寫它。

    - 因為您已新增憑證，所以需要重新啟動對等節點的容器。請遵循下列[指示](#remote-peer-restart)來重新啟動對等節點。

4. 您需要將相同的 `admincert/cert.pem` 憑證從本端機器上傳至「{{site.data.keyword.blockchainfull_notm}} 平台」，遠端 CLI 或應用程式才能執行頻道作業，例如提取頻道初始區塊及實例化鏈碼。
    1. 在本端機器上，找出 `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` 檔，然後將它複製到剪貼簿。
    2. 進入「{{site.data.keyword.blockchainfull_notm}} 平台」上您網路的「網路監視器」。
    3. 按一下左導覽器中的**成員**，然後按一下**憑證**標籤。
    4. 按一下**新增憑證**按鈕。
    5. 在**新增憑證**視窗中，為您的憑證提供一個名稱（例如 `fabrictools.pem`）、貼上您剛剛複製到剪貼簿的憑證，然後按一下**提交**。
    6. 系統會詢問您是否要重新啟動對等節點。按一下**重新啟動**。
    7. 按一下左導覽器中的**頻道**，找出遠端對等節點將加入的頻道。
    8. 按一下**動作**標頭下的三個點，然後按一下**同步憑證**。在**同步憑證**視窗中，按一下**提交**。

    **附註**：請務必在對等節點加入下面的頻道之前，先同步化頻道憑證。

### 設定 Fabric 工具容器

將我們的所有憑證移至必要位置之後，我們已準備好從 Docker 安裝及使用 Fabric 工具容器。我們需要再次從儲存 MSP 資料夾的目錄中執行這些指令。

1.  使用下列指令下載 Fabric 工具 Docker 映像檔：
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  執行下列指令來啟動 Fabric 工具容器。
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    此指令會在您的工具容器內裝載 MSP 目錄。

3.  遠端對等節點位址必須解析為字尾為 `blockchain.com` 的網域名稱。您可以透過 DNS 執行此動作，或修改 /etc/hosts 檔。基於這些指示的目的，我們將修改 Fabric 工具容器中的 `/etc/hosts` 檔。登入 ICP 主控台，並找出遠端對等節點的[端點資訊](#remote-peer-retrieve-endpoint-info)。一旦您具有對等節點 `INGRESS_IP`，請以遠端對等節點的位址執行下列指令。
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### 使用 Fabric 工具容器將遠端對等節點加入至頻道
{: #icp-cli-join-peer-to-channel}

在您可以執行 CLI 指令將遠端對等節點加入至頻道之前，您的組織需要新增至網路中的頻道。

  - 您可以為遠端對等節點啟動新的頻道。作為頻道起始者，您可以在[建立頻道](create_channel.html#creating-a-channel)期間自動包含您的組織。請注意，您必須在「{{site.data.keyword.blockchainfull_notm}} 平台」至少具有一個對等節點，然後才能在「網路監視器」中建立頻道。  

  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。在「{{site.data.keyword.blockchainfull_notm}} 平台」上具有對等節點的頻道成員，可以使用「網路監視器」將您的組織新增至頻道，即使您沒有在平台上管理任何對等節點也一樣。

    將您的組織新增至頻道之後，您需要將對等節點的簽署憑證新增至頻道，以便其他成員可以在交易期間驗證您的數位簽章。在安裝期間，遠端對等節點會上傳其簽署憑證，以便您只需要將憑證同步化至頻道。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。

1. 從「網路監視器」的**概觀**畫面中提供的 `creds.json` 檔擷取配置資訊。按一下**連線設定檔**，然後按一下**下載**。

  - 搜尋**排序節點**來找出排序節點 URL，其位於 `orderers > url` 之下。請記下以網路名稱開頭的 URL。URL 類似下列範例：
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - 搜尋**組織**來找出您組織的名稱。這應該是您用來登錄遠端對等節點的同一個組織。您可以同時找到組織的名稱及其相關聯的 `mspid`。請記下 `mspid` 的值。

2. [尋找遠端對等節點端點資訊](#remote-peer-retrieve-endpoint-info)，這次記下e `Ingress IP` 旁邊列出的 `Ports`。

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  請記下 30K 範圍內的埠。這些是來自 Fabric 工具容器的可定址埠，它將用來設定 `PEERADDR` 環境變數。

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  因此，我們使用的 `PEERADDR` 是以 `/etc/hosts` 檔中指定的值，以及連結至 `7051` 的埠來建構，亦即我們範例中的 `30162`。因此，`PEERADDR` 的結果值將是 `remotepeer.blockchain.com:30162`。

3. 執行下列指令，以在 Fabric 工具容器中設定環境變數。

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  將欄位取代為您自己的資訊。
    - 將 `<ORDERER_URL>` 取代為來自 `creds.json` 檔的排序的主機名稱及埠。
    - 將 `<CHANNEL_NAME>` 取代為對等節點加入的頻道名稱。
    - 將 `<CC_NAME>` 取代為參照鏈碼的任何名稱。
    - 將 `<ORGANIZATION_MSP_ID>` 取代為來自 `creds.json` 檔的組織名稱。
    - 將 `<PEER_ADDR>` 取代為您在前一個步驟中建構的值。

  例如：

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. 提取頻道的初始區塊，以在對等節點上建置頻道分類帳。首先 `cd` 至根目錄，然後執行指令以提取初始區塊。

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
  ```
  {:codeblock}

  執行下列指令，以驗證初始區塊是否已順利新增至對等節點容器：

  ```
  ls *.block
  ```
  {:codeblock}

  當看到類似下列範例的內容時，您知道已順利新增初始區塊：
  ```
  defaultchannel_0.block
  ```

5. 使用下列指令將對等節點加入至頻道：

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  當此作業順利運作時，您應該會看到類似下列範例的資訊：

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### 使用 Fabric 工具容器，在遠端對等節點上安裝鏈碼
{: #icp-toolcontainer-install-cc}
我們現在準備要在遠端對等節點上安裝及實例化鏈碼。對於這些指示，我們將從 `fabric-samples` 儲存庫安裝 `fabcar` 鏈碼。使用下列指令，從 `github.com` 下載 `fabric-samples` 鏈碼：

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

執行下列對等節點 CLI 指令，將 `fabcar` 鏈碼安裝至遠端對等節點。

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  當此指令順利完成時，您應該看到下列類似內容：

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### 使用 Fabric 工具容器在頻道上實例化鏈碼
{: #icp-toolcontainer-instantiate-cc}

因為只有一個對等節點必須在頻道上實例化鏈碼，所以您不必使用遠端對等節點來實例化鏈碼。「{{site.data.keyword.blockchainfull_notm}} 平台」上管理的對等節點可以這樣做。不過，如果您想要遠端對等節點在頻道上實例化鏈碼，則可以在「Fabric 工具」容器中執行下列指令：

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

當此指令順利完成時，您應該看到下列類似內容：

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## 檢視 {{site.data.keyword.cloud_notm}} Private 中的遠端對等節點日誌
{: #remote-peer-log-icp}

您可以從 ICP 主控台存取遠端對等節點日誌，並在 Kibana 介面中檢查日誌。

1. 在 ICP 主控台中，按一下左上角的**功能表**圖示。
2. 從功能表清單中，按一下**工作負載** > **Helm 版本**。
3. 在「Helm 版本」表格中，按一下您的 Helm 版本名稱。
4. 向下捲動到 **Pod** 區段，然後按一下表格列尾端的**檢視日誌**鏈結。遠端對等節點日誌即會在 Kibana 介面中開啟。

## 更新鏈碼

一段時間後，您可能需要修改正在遠端對等節點上執行的鏈碼。因為鏈碼已安裝在對等節點上，並在頻道上將其實例化，所以您需要在頻道的所有對等節點上更新鏈碼。

完成下列步驟來更新鏈碼：

1. 若要更新每一個遠端對等節點上的鏈碼，只需使用一個用戶端應用程式或 CLI 指令，重新執行您用來在對等節點上安裝鏈碼的處理程序。請務必指定原先使用的相同鏈碼名稱。不過，此次會遞增鏈碼 `Version`。所有對等節點都需要使用相同的鏈碼名稱和版本。

2. 在頻道的所有對等節點上安裝新的鏈碼之後，請使用「網路監視器」或 [peer chaincode upgrade ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 指令，來更新頻道以使用新的鏈碼。

請參閱這些[指示](install_instantiate_chaincode.html#updating-a-chaincode)的步驟二，以取得使用「網路監視器」的「安裝程式碼」畫面，來更新頻道上鏈碼的相關資訊。

## 疑難排解
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **問題：**登入對等節點容器時發生錯誤
{: #icp-bash-error}

當您使用 kubectl 工具，藉由執行下列指令來執行至對等節點時，此問題就會發生：
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
失敗並產生類似下列範例的錯誤訊息：
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**解決方案：**  

當在相同的 Docker 容器中開啟兩個 Docker Bash 階段作業時，若在大序排列法系統（例如 IBM System Z）上，便會發生此錯誤。第二個 Bash 階段作業可能無法連接至執行中的容器。若要解決此問題，請[重新啟動對等節點容器](#remote-peer-restart)，然後在對等節點重新啟動之後，重試 `kubbectl exec` 指令。

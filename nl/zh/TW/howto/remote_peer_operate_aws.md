---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 操作 AWS 中的對等節點
{: #remote-peer-operate-aws}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


在 AWS 中設定 {{site.data.keyword.blockchainfull}} Platform 對等節點之後，您需要先完成數個作業步驟，對等節點才能發出交易，以查詢並呼叫區塊鏈網路的分類帳。這些步驟包括將組織新增至頻道、將對等節點加入頻道、在對等節點上安裝鏈碼、在頻道上實例化鏈碼，以及將應用程式連接至對等節點。您可以使用 [Fabric SDK](#aws-peer-operate-with-sdk) 或[指令行](#aws-peer-cli-operate)來完成這些作業步驟。雖然指示假設熟悉 SDK 的作業，但 Fabric SDK 是建議的路徑。

**附註**：AWS 中的 {{site.data.keyword.blockchainfull_notm}} Platform 對等節點無權存取完整功能，也不支援 {{site.data.keyword.blockchainfull_notm}} Platform 上管理的對等節點。因此，您無法使用「網路監視器」來操作對等節點。在開始執行 AWS 中的對等節點之前，請確定您檢閱了[考量](remote_peer.html#remote-peer-limitations)。

## 使用 Fabric SDK 來操作對等節點
{: #aws-peer-operate-with-sdk}

Hyperledger Fabric SDK 提供一組功能強大的 API，可讓應用程式與區塊鏈網路互動，以及操作區塊鏈網路。您可以在 [Hyperledger Fabric SDK Community 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community 文件") 中，找到所支援語言的最新清單，以及 Hyperledger Fabric SDK 內可用 API 的完整清單。您可以使用 Fabric SDK，將對等節點加入至 {{site.data.keyword.blockchainfull_notm}} Platform 上的頻道、在對等節點上安裝鏈碼，以及在頻道上實例化鏈碼。

下列指示會使用 [Fabric Node SDK ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js") 來操作對等節點，並假設事先已熟悉 SDK。您可以使用[開發應用程式指導教學](../v10_application.html)，瞭解如何在開始之前使用 Node SDK，並引導您在準備好呼叫及查詢鏈碼之後，使用對等節點來開發應用程式。

「AWS 快速入門」上的「{{site.data.keyword.blockchainfull_notm}} Platform 對等節點」會建立兩個對等節點來取得高可用性。因此，您需要針對每個對等節點執行這些作業步驟一次。當您準備好從應用程式查詢並呼叫鏈碼之後，請讓 SDK 連接至這兩個對等節點，以確保您的[應用程式高度可用](../v10_application.html#ha-app)。

### 安裝 Node SDK

您可以使用 NPM 來安裝 [Node SDK ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js")：
```
npm install fabric-client@1.2
```
{:codeblock}

建議您使用 1.2 版的 Node SDK。

### 準備 SDK 以使用對等節點
{: #remote-peer-node-sdk}

在使用 SDK 來操作對等節點之前，您需要產生必要的憑證（登記），讓您的應用程式能夠與 {{site.data.keyword.blockchainfull_notm}} Platform 及對等節點上的網路通訊。使用您的**管理者**身分，遵循[使用 SDK 登記](../v10_application.html#enroll-app-sdk)的步驟。[開發應用程式](../v10_application.html)指導教學也會登記為**管理者**，因此您不需要修改範例程式碼。

### 將 signCert 上傳至 IBM Blockchain Platform
{: #remote-peer-upload-SDK}

您需要將 SDK 簽署憑證上傳至 {{site.data.keyword.blockchainfull_notm}} Platform 上的網路，讓其他成員可以辨識您的數位簽章。

- 您可以在 SDK 產生加密資料的目錄中找到簽署憑證，其檔名為 admin。複製 `certificate` 欄位之後引號內的憑證，其開頭為 `-----BEGIN CERTIFICATE-----`，結尾為 `-----END CERTIFICATE-----`。您可以使用 CLI，藉由發出 `echo -e "<CERT>" > admin.pem` 指令，將憑證轉換成 PEM 格式。然後，您可以使用「網路監視器」將憑證的內容貼入區塊鏈網路。登入 {{site.data.keyword.blockchainfull_notm}} Platform 上的網路。在「網路監視器」的「成員」畫面上，按一下**憑證** > **新增憑證**。為您的憑證指定任何名稱，並將內容貼至**憑證**欄位。當系統詢問您是否要重新啟動對等節點時，請按一下**重新啟動**。必須在組織加入頻道之前執行此動作。

### 將 signCert 上傳至對等節點
{: #remote-peer-upload-signcert}

您也需要將 SDK 的簽署憑證上傳至遠端對等節點，然後予以重新啟動。您需要安裝遠端對等節點容器內您[上傳至 IBM Blockchain Platform ](#remote-peer-upload-SDK) 的同一個簽署憑證。

在 AWS 主控台中選取實例（按一下**服務 > EC2 > 實例**），然後按一下「連接」按鈕，即可以 SSH 方式進入 VPC 實例。請遵循 AWS 的指示，以發出 ssh 指令。

請從對等節點容器內執行下列指令來上傳您的憑證。  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- 將 `<PEER_ENROLL_ID>` 取代為「快速入門」範本中所指定並與此遠端對等節點實例相關聯的登記 ID。  
- 將 `<CERT.PEM>` 取代為您的 signCert，其開頭為 `-----BEGIN CERTIFICATE-----`，且結尾為 `-----END CERTIFICATE-----`。    

  **附註：**如果 `cert.pem` 檔案已存在，則請不要改寫它，但請建立一個新檔案（例如，`cert2.pem`）。

因為您新增了憑證，所以需要重新啟動容器以讓對等節點挑選憑證。請遵循下列[指示](#remote-peer-aws-restart)來重新啟動對等節點。

### 將對等節點的 TLS 憑證傳遞至 SDK
{: #aws-remote-peer-download-tlscert}

您需要將 TLS `cacert.pem` 的內容從對等節點容器複製到您的應用程式，以鑑別來自 SDK 的通訊。請在「對等節點 CLI」容器中執行下列指令。將 `<PEER_ENROLL_ID>` 取代為您在「快速入門」範本中所指定遠端對等節點的堆疊名稱（請記住，系統會建立兩個 VPC 實例）。
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

將 `ca.crt` 檔案的整個文字複製到剪貼簿，其開頭為最初的 `-----BEGIN CERTIFICATE-----`，且結尾為最後的 `-----END CERTIFICATE-----`。以 PEM 格式將此憑證儲存至本端檔案系統。然後，您可以使用簡單的讀取檔案指令，將 TLS 憑證匯入至應用程式。
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### 將對等節點端點資訊提供給 SDK
{: #remote-peer-SDK-endpoints}

若要擷取對等節點的端點資訊，請在 AWS 主控台中找出您的對等節點實例。請記下 EC2 實例的 `AWS EC2 儀表板公用 DNS (IPv4)` 值，並宣告新的對等節點變數或更新連線設定檔，以將其提供給 SDK。請連結公用 DNS 位址與埠 `7051`。下列範例會在網狀架構網路上定義對等節點，並將您匯入的 TLS 憑證傳遞至其中。

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**附註：**因為對等節點是遠端節點，所以 {{site.data.keyword.blockchainfull_notm}} Platform 網路上的其他組織無法在其連線設定檔中找到對等節點的端點資訊。如果其他組織需要將要背書的交易傳送至您的對等節點，則您需要在頻外作業中提供「公用 IP」及 TLS 憑證。

### 使用 SDK 來加入頻道
{: #remote-peer-join-channel-sdk}

當您身為區塊鏈網路的成員時，您的組織必須先新增至網路中的頻道，才能將您的對等節點加入該頻道。

  - 您可以為對等節點啟動新的頻道。當您作為頻道起始者時，可以在[建立頻道](create_channel.html#creating-a-channel)期間自動併入您的組織。

  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。

    將您的組織新增至頻道之後，您需要將對等節點的簽署憑證新增至頻道，以讓其他成員可以在交易期間驗證您的數位簽章。在安裝期間，對等節點會上傳其簽署憑證，因此您只需要將憑證同步化至頻道。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。您可能需要等待數分鐘，才能在發出加入頻道指令之前完成頻道同步。

在您的組織是頻道的一部分之後，請遵循[加入頻道](../v10_application.html#join-channel-sdk)的指示。您需要提供排序服務的 URL 及頻道名稱。

### 使用 SDK 在對等節點上安裝鏈碼
{: #remote-peer-install-cc-sdk}

請遵循指示，在對等節點上使用 SDK 來[安裝鏈碼](../v10_application.html#install-cc-sdk)。

### 使用 SDK 在頻道上實例化鏈碼
{: #remote-peer-instantiate-cc-sdk}

只有一個頻道成員需要實例化或更新鏈碼。因此，在 {{site.data.keyword.blockchainfull_notm}} Platform 上具有對等節點之頻道的任何網路成員，都可以使用「網路監視器」來實例化鏈碼並指定背書原則。不過，如果您想要使用對等節點在頻道上實例化鏈碼，則可以使用 SDK，並遵循[實例化鏈碼](../v10_application.html#instantiate-cc-sdk)的指示。


## 使用 CLI 來操作對等節點
{: #aws-peer-cli-operate}

您也可以使用 Fabric CA 用戶端及 Fabric 工具容器，從指令行操作對等節點。在這些指示中，我們會先使用 Fabric CA 用戶端來產生必要的憑證。接著會使用 Fabric 工具容器來操作對等節點，方法是將其加入頻道，並安裝鏈碼，然後在頻道上實例化鏈碼。

### 使用 Fabric CA 用戶端登記
{: #peer-client-enroll}

第一個步驟是使用 Fabric CA 用戶端來產生必要的憑證（登記）。您需要先安裝 Fabric CA 用戶端。請將[您平台所適用的 fabric-ca 1.2.0 版二進位檔](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/)下載至本端機器，並解壓縮它們，然後將其移至 `$HOME/fabric-ca-remote/` 這類的資料夾中。

1.  準備環境以使用 Fabric CA 用戶端。切換至您已將用戶端二進位檔移至其中的目錄，以在指令中直接參照它。
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  設定用戶端可在其中建立金鑰的路徑。如果您之前尚未執行 `enroll` 指令，則 `msp` 資料夾與 `.yaml` 檔案不存在。如果您之前已執行 `enroll` 指令，請務必使用下面的 `rm` 指令，從先前的登記嘗試中刪除任何配置資料：
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  您需要在 {{site.data.keyword.blockchainfull_notm}} Platform 上擷取「憑證管理中心」的端點資訊。在「網路監視器」的「概觀」畫面上，按一下**連線設定檔**按鈕，然後開啟包含網路認證的 JSON 檔案。向下捲動至 `certificateAuthorities` 區段，並記下下面的資訊。
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  您也需要從 {{site.data.keyword.blockchainfull_notm}} Platform 擷取 TLS 憑證，才能與 CA 進行通訊。上述網路認證包括必要的憑證。在「連線設定檔」檔案的 `certificateAuthorities` 區段中，複製接在 `"pem:"` 後面的憑證，其開頭為 `-----BEGIN CERTIFICATE-----`，且結尾為 `-----END CERTIFICATE----- `。**請不要併入引號**。

    從終端機視窗執行下列指令，將 `<certificate>` 取代為您複製的憑證。
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 將此憑證儲存在您可以在未來指令中參照它的位置。  

5.  您現在可以產生所需的憑證。執行下列指令：
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

在可以操作對等節點之前，我們需要在本端機器上執行一些憑證管理，並將 Fabric CA 用戶端產生的部分憑證上傳至 {{site.data.keyword.blockchainfull_notm}} Platform 及您的對等節點。我們也需要從「平台」和對等節點下載 TLS 憑證。如果您要進一步瞭解將要使用的憑證，以及將要執行的作業，請造訪[在 {{site.data.keyword.blockchainfull_notm}} Platform 上管理憑證](../certificates.html)。

在本端機器上，開啟指令終端機，並導覽至已將 Fabric-CA-Client 二進位檔移至其中、並儲存 MSP 資料夾的目錄。

1. 將 `cert.pem` 檔案從 `signcerts` 資料夾複製到新的 `admincerts` 資料夾。
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. 將排序節點 TLS 憑證從 {{site.data.keyword.blockchainfull_notm}} Platform 複製到 MSP 資料夾。您的連線設定檔包含必要的憑證。在「網路監視器」的「概觀」畫面上，按一下**連線設定檔**按鈕，然後開啟包含網路認證的 JSON 檔案。導覽至 `orderers` 區段。複製接在 `"pem:"` 後面的憑證，其開頭為 `-----BEGIN CERTIFICATE-----`，且結尾為 `-----END CERTIFICATE-----`。請勿包含引號。

    從終端機視窗中，執行下列指令：
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. 您也需要將對等節點的 TLS 憑證從 AWS 上的對等節點容器複製到本端機器。

    - [遵循這些指示](remote_peer_aws.html#remote-peer-aws-test)來登入對等節點容器，然後執行下列指令，並將 <PEER_ENROLL_ID> 取代為您在「快速入門」範本中所指定對等節點的堆疊名稱，後面接著其號碼（請記住，系統會建立兩個 VPC 實例）。
      ```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
      ```
      {:codeblock}

      將 `cacert.pem` 檔案的整個文字複製到剪貼簿，其開頭為第一個 `-----BEGIN CERTIFICATE-----`，且結尾為最後一個 `-----END CERTIFICATE-----`。

    - 切回本端機器，並執行下列指令。將文字 `<CACERT.PEM>` 取代為剪貼簿中的文字。
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
      {:codeblock}

      **附註：**依預設，「AWS 快速入門」範本會在兩個不同的可用性區域中建立兩個對等節點實例。如果您已針對上述其中一個對等節點完成這些步驟，則當您針對第二個實例執行這些步驟時，cacert.pem 檔案便已存在。請繼續進行，並將它取代為第二個對等節點的憑證。

4. 若要提供 CLI 在對等節點上安裝鏈碼的授權，請將 Fabric CA 用戶端所產生的 signCert 上傳至對等節點的 admin 資料夾，然後重新啟動對等節點。因此，請將本端機器中的 `admincert/cert.pem` 憑證複製到對等節點容器中的 `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` 目錄，作為 `cert2.pem`。

    - 在本端機器上，執行下列指令：

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - 將 `cert.pem` 檔案的文字複製到剪貼簿，其開頭為 `-----BEGIN CERTIFICATE-----`，且結尾為 `-----END CERTIFICATE-----`。

    - 切回「對等節點」容器，並執行下列指令，將 cert.pem 新增至對等節點憑證。將文字 `<CERT.PEM>` 取代為剪貼簿中的文字。

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **附註：**cert.pem 檔案已存在於這個目錄中。請不要改寫它。

    - 因為我們已新增憑證，所以需要重新啟動容器以讓對等節點挑選憑證。請遵循下列指示來[重新啟動對等節點容器](#remote-peer-aws-restart)。

5. 將相同的 `admincert/cert.pem` 憑證從本端機器上傳至 {{site.data.keyword.blockchainfull_notm}} Platform，遠端 CLI 或應用程式才能執行頻道作業，例如提取頻道初始區塊及實例化鏈碼。
    1. 在本端機器上，找出 `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` 檔案，然後將它複製到剪貼簿。
    2. 進入 {{site.data.keyword.blockchainfull_notm}} Platform 上您網路的「網路監視器」。
    3. 按一下左導覽器中的**成員**，然後按一下**憑證**標籤。
    4. 按一下**新增憑證**按鈕。
    5. 在**新增憑證**視窗中，為您的憑證提供一個名稱（例如 `fabrictools.pem`）、貼上您剛剛複製到剪貼簿的憑證，然後按一下**提交**。
    6. 系統會詢問您是否要重新啟動對等節點。按一下**重新啟動**。
    7. 按一下左導覽器中的**頻道**，並找出對等節點將加入的頻道。
    8. 按一下**動作**標頭下的三個點，然後按一下**同步憑證**。在**同步憑證**視窗中，按一下**提交**。

    **附註**：請務必在對等節點加入頻道或在頻道上實例化鏈碼之前，先將頻道憑證同步化。您可能需要等待數分鐘，才能在發出加入頻道或實例化鏈碼指令之前完成頻道同步。   

### 設定 Fabric 工具容器
{: #setup-fabric-cli}

在您將我們的所有憑證都移至必要位置之後，即可從 Docker 安裝及使用 Fabric 工具容器。請確定您是從儲存 MSP 資料夾的目錄中執行這些指令。

1.  使用下列指令下載 Fabric 工具 Docker 映像檔：
    ```
    docker pull ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

2.  執行下列指令來啟動 Fabric 工具容器。
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

    此指令將在您的工具容器內裝載 MSP 目錄。

<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### 使用 Fabric 工具 CLI 將對等節點加入頻道
{: #fabric-cli-join-peer-to-channel}

在您可以執行 CLI 指令將對等節點加入頻道之前，您的組織需要新增至網路中的頻道。

  - 您可以為對等節點啟動新的頻道。當您作為頻道起始者時，可以在[建立頻道](create_channel.html#creating-a-channel)期間自動併入您的組織。

  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。

    將您的組織新增至頻道之後，您需要將對等節點的簽署憑證新增至頻道，以讓其他成員可以在交易期間驗證您的數位簽章。在安裝期間，對等節點會上傳其簽署憑證，因此您只需要將憑證同步化至頻道。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。

1. 從「網路監視器」之「概觀」畫面中提供的`連線設定檔`擷取配置資訊。按一下**連線設定檔**，然後按一下**下載**。

  - 搜尋**排序節點**來找出排序節點 URL，其位於 `orderers > url` 下。請記下以網路名稱開頭的 URL。此 URL 類似下列範例：

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - 搜尋**組織**來找出您組織的名稱。這應該是您用來登錄對等節點的同一個組織。您可以同時找到組織的名稱及其關聯的 `mspid`。請記下 `mspid` 的值。

2. 在 AWS EC2 儀表板公用 DNS (IPv4) 中，找到 AWS VPC 對等節點的端點資訊（公用 DNS IPv4 位址）。它看起來如下：

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  使用此資訊來建置環境變數 `PEERADDR`，方法是將位址與埠 '7051' 連結在一起，讓 `PEERADDR` 的值與下面類似：

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. 執行下列指令，以在 Fabric 工具容器內設定環境變數。

    ```
    export ORDERER_1=<ORDERER_URL>
    export CHANNEL=<CHANNEL_NAME>
    export CC_NAME=<CC_NAME>
    export ORGID=<ORGANIZATION_MSP_ID>
    export PEERADDR=<PEER_ADDR>
    ```
    {:codeblock}

    將欄位取代為您自己的資訊。
      - 將 `<ORDERER_URL>` 取代為 `creds.json` 檔案中排序節點的主機名稱及埠。
      - 將 `<CHANNEL_NAME>` 取代為對等節點加入的頻道名稱。
      - 將 `<CC_NAME>` 取代為參照鏈碼的任何名稱。
      - 將 `<ORGANIZATION_MSP_ID>` 取代為 `creds.json` 檔案中的組織名稱。
      - 將 `<PEER_ADDR>` 取代為您在前一個步驟中建構的值。  

  例如：

    ```
    export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
    export CHANNEL=defaultchannel
    export CC_NAME=mycc
    export ORGID=PeerOrg1
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. 提取頻道的初始區塊，以在對等節點上建置頻道分類帳。首先，`cd` 至根目錄，然後執行指令以提取初始區塊。

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
  ```
  {:codeblock}

  **附註：**您可能會在執行任何上述 CLI 指令時，看到可以放心忽略的下列警告。

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```

  執行下列指令，以驗證初始區塊已順利新增至對等節點容器：

  ```
  ls *.block
  ```
  {:codeblock}

  已順利新增初始區塊時，您應該會看到類似下列範例的內容：
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. 現在，請使用下列指令將對等節點加入頻道，並傳入初始區塊：

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  當此指令順利運作時，您應該會看到類似下列資訊的內容：

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### 使用 Fabric 工具容器在對等節點上安裝鏈碼
{: #aws-toolcontainer-install-cc}

我們現在可以在對等節點上安裝及實例化鏈碼。對於這些指示，我們將從 `fabric-samples` 儲存庫安裝 `fabcar` 鏈碼。請使用下列指令，從 GitHub 下載 `fabric-samples` 鏈碼：

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

請執行下列「對等節點 CLI」指令，將 `fabcar` 鏈碼安裝至對等節點。

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  當此指令順利完成時，您應該會看到下列類似內容：

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### 使用 Fabric 工具容器在頻道上實例化鏈碼
{: #aws-toolcontainer-instantiate-cc}

因為只有一個對等節點必須在頻道上實例化鏈碼，所以您不必使用對等節點來實例化鏈碼。{{site.data.keyword.blockchainfull_notm}} Platform 上管理的對等節點可以這樣做。不過，如果您要對等節點在頻道上實例化鏈碼，則可以在 Fabric 工具容器中執行下列指令：

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

當此指令順利完成時，您應該會看到下列類似內容：

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## 重新啟動在 AWS 中執行的對等節點
{: #remote-peer-aws-restart}

您可以在部署對等節點的環境中啟動、停止或重新啟動對等節點。每當將憑證新增至對等節點時，需要重新啟動對等節點，才能辨識新的憑證。有多種方式可以執行此動作：

- 在 AWS 主控台中，重新啟動對等節點 VPC 實例。
- 從對等節點容器內，執行下列指令：

 ```
 docker restart peer
 ```
 {:codeblock}  

此外，您還可以使用 [HEAD 要求](monitor_network.html#monitor-nodes)來檢查對等節點的可用性。

## 檢視對等節點日誌

以 SSH 方式進入 AWS VPC 對等節點實例，然後執行下列指令來檢視對等節點日誌：

```
docker logs peer
```
{:codeblock}

## 更新鏈碼

一段時間後，您可能需要修改正在對等節點上執行的鏈碼。因為鏈碼已安裝在對等節點上，並在頻道上將其實例化，所以您需要在頻道的所有對等節點上更新鏈碼。

完成下列步驟來更新鏈碼：

1. 若要更新 AWS 中每個對等節點上的鏈碼，只需使用一個用戶端應用程式或 CLI 指令，重新執行您用來在對等節點上安裝鏈碼的處理程序即可。請務必指定原先使用的相同鏈碼名稱。不過，這次會遞增鏈碼 `Version` 號碼。

2. 在頻道的所有對等節點上安裝新的鏈碼之後，請使用「網路監視器」或 [peer chaincode upgrade ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 指令，來更新頻道以使用新的鏈碼。

請參閱這些[指示](install_instantiate_chaincode.html#updating-a-chaincode)的步驟 2，以取得使用「網路監視器」的「安裝程式碼」畫面來更新頻道上鏈碼的相關資訊。

## 疑難排解

### **問題：**遠端對等節點無法連接至區塊鏈網路

堆疊建立順利完成，但 Docker 日誌包含錯誤：

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

**解決方案：**  
部署「快速入門」範本時，如果嘗試在 CAUrl 上指定埠，則可能會導致此錯誤。CAUrl 應該類似於 `https://<network>-org1-ca.stage.blockchain.ibm.com:31011`。
重新部署「快速入門」範本，並請小心指定 CAUrl 的適當值。

### **問題：**實例化鏈碼失敗，並發生錯誤

鏈碼實例化失敗，並發生下列錯誤：
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**解決方案：**   
請確定將管理者憑證上傳至「網路監視器」之後，接著會在頻道上同步化憑證。如需相關資訊，請參閱這些[指示](#manage-certs)中的步驟 5。請注意，務必在對等節點加入頻道之前，先將頻道憑證同步化。

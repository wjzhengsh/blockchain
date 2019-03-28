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

# 部署 {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #remote-peer-aws}


***[此頁面有幫助嗎？請告訴我們。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


這些指示說明如何使用「Amazon Web Services (AWS) 快速入門」範本來建立 {{site.data.keyword.blockchainfull}} Platform for AWS 對等節點，然後將它連接至 {{site.data.keyword.blockchainfull_notm}} Platform 上的網路。
{:shortdesc}

如需 AWS 的相關資訊，請參閱 [AWS 概觀文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://d1.awsstatic.com/whitepapers/aws-overview.pdf "AWS 概觀文件")。

在您部署 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點之前，請檢閱[對等節點考量](remote_peer.html#remote-peer-limitations)。

## 必要條件
{: #prerequisites-aws}

若要使用 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點，您具有的組織必須是 IBM Blockchain Platform 上所管理之區塊鏈網路的成員。您需要使用 IBM Cloud 上的「網路監視器」，才能存取網路認證及網路的 API 端點。如果您不是任何區塊鏈網路的成員，則需要建立或加入網路。如需相關資訊，請參閱[建立網路](../get_start.html#creating-a-network)或[加入網路](../get_start.html#joining-a-network)。

對等節點的預設 VPC 實例類型是 `m4.xlarge`。您應該根據 CPU、記憶體及儲存空間需求，最佳化您所選擇的實例類型。對等節點至少需要：  
-	2 個 CPU
-	2 GB RAM
-	4 GB 空間，供鏈碼使用
-	10 GB 空間，讓分類帳在擴充時具有成長的能力。

這些最低資源層次對於測試和實驗而言便已足夠。若為正式作業環境，請務必配置夠大的儲存空間數量（例如 100GB）。使用的儲存空間數量將視網路所需的交易數目及簽章數目而定。如果您耗盡對等節點上的儲存空間，則必須<!-- either expand the storage or -->部署具有較大檔案系統的新對等節點，並讓它透過相同頻道上的其他對等節點進行同步。


## 部署選項
{: #remote-peer-aws-deploy-options}

「快速入門」提供兩個部署選項：

* 將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至新的 VPC（端對端部署）。此選項會建置由 VPC、子網路、NAT 閘道、安全群組、防禦主機及其他基礎架構元件組成的新 AWS 環境，然後將對等節點部署至這個新的 VPC。

* 將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至現有 VPC。此選項會在現有 AWS 基礎架構中佈建 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點。這些選項有個別的範本，您可以在其中配置 CIDR 區塊、實例類型及「對等節點」設定，如本手冊稍後所討論。

## 步驟一：準備 AWS 帳戶
{: #remote-peer-aws-account}

1. 如果您還沒有 AWS 帳戶，請遵循畫面上的指示，在[這裡 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://aws.amazon.com "https//aws/amazon.com") 建立一個 AWS 帳戶。

2. 使用導覽列中的地區選取器，來選擇要在 AWS 中部署對等節點的「AWS 地區」。

3. 在您偏好的地區中建立金鑰組。

4. 必要的話，請要求增加 Amazon EC2 <type> 實例類型的服務限制。如果您的現有部署已使用此實例類型，而且您認為可能會超過此部署的預設限制，則可能需要這麼做。

## 步驟二：擷取遠端對等節點配置資訊
{: #aws-network-endpoints}

在配置期間，您需要將網路的 API 端點提供給對等節點。這些端點可讓對等節點在 {{site.data.keyword.blockchainfull_notm}} Platform 上尋找並連接至網路。在「網路監視器」的**概觀**畫面上，按一下**遠端對等節點配置**按鈕。

![遠端對等節點配置](../images/myresources_starter.png "遠端對等節點配置")
*圖 1. 遠端對等節點配置畫面*

即會開啟蹦現視窗，並顯示下列欄位的值。請儲存下列欄位的值，當您使用「AWS 快速入門」範本來配置對等節點時，需要這些值。

- **組織 MSP**
- **憑證管理中心 (CA) 名稱**
- **憑證管理中心 (CA) URL**
- **憑證管理中心 (CA) TLS 憑證**

您可以將每個欄位直接複製並貼入「快速入門」範本中，或按一下**下載**鏈結，將它們儲存為 JSON 檔案。

**附註：**如果您以 JSON 格式下載資訊，則需要先將 TLS 憑證轉換成 PEM 格式，再將它提供給對等節點。請發出下列指令，將您所下載之 JSON 檔案中的**憑證管理中心 (CA) TLS 憑證**轉換成 PEM 格式：
```
echo -e "<CERT>" > admin.pem
```
{:codeblock}

將 `<CERT>` 取代為**憑證管理中心 (CA) TLS 憑證**的值。於是，當系統提示輸入「快速入門」範本中的**憑證管理中心 (CA) TLS 憑證**時，會對 admin.pem 檔案進行 `cat` 處理，然後將內容複製並貼入欄位中。  

## 步驟三：登錄 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點
{: #aws-register-peer}

在 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點可以加入網路之前，您需要將新的對等節點身分新增至 {{site.data.keyword.blockchainfull_notm}} Platform 上的網路。請完成下列步驟來登錄對等節點。

**附註：**對於「高可用性」，「快速入門」範本會在兩個可用性區域中建立兩個對等節點。因此，需要兩個登記 ID 及密碼。**請重複此處理程序兩次，以產生兩個登記 ID 及密碼。**

1. 登入 {{site.data.keyword.blockchainfull_notm}} Platform 上您網路的「網路監視器」。在「網路監視器」的「憑證管理中心」畫面上，您可以檢視已向網路登錄的所有身分，例如管理者或用戶端應用程式。
  ![CA 畫面](../images/CA_screen_starter.png "CA 畫面")
  *圖 2. CA 畫面*

2. 按一下畫面上的**新增使用者**按鈕。即會開啟蹦現畫面，並可讓您在填寫下面的欄位之後，將您的對等節點登錄至網路。**請儲存 ID 及「密碼」的值，以供您在「快速入門」範本中配置對等節點時使用。**
  - **登記 ID：**配置對等節點時，您要用於對等節點的名稱（稱為 `enroll ID`）。請**儲存此值**，以供日後使用。
  - **登記密碼：**配置對等節點時，您要用於對等節點的密碼（稱為 `enroll Secret`）。請**儲存此值**，以供日後使用。
  - **類型：**為此欄位選取 `peer`。
  - **連結：**這是組織下對等節點所屬的連結，例如 `org1`。您可以指定新的連結，或使用現有的連結。
  - **登記數上限：**您可以使用此欄位，限制您可以使用此身分來登記或產生憑證的次數。如果未指定，則此值預設為不限次數的登記。

  填寫欄位之後，請按一下**提交**來登錄對等節點。登錄的對等節點接著會列在表格中，作為網路上的身分。為了安全起見，每個身分及隨附的 enrollID 和密碼只用來部署一個對等節點。請勿重複使用對等節點的 ID 及密碼。

## 步驟四：啟動快速入門
{: #remote-peer-aws-launchqs}

您要負責執行此「快速入門」參照部署時所使用的 AWS 服務成本。此「快速入門」的使用沒有額外成本。如需完整詳細資料，請參閱此「快速入門」中所需使用之每一個 AWS 服務的定價頁面。價格可能會有變動。

1. 選擇下列其中一個選項，以將 AWS CloudFormation 範本啟動至您的 AWS 帳戶。為了協助您選擇選項，請參閱本手冊中先前的部署選項。每個部署大約需要 10 分鐘才能完成。  

  * [將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至 AWS 上的新 VPC ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fwd.aws/v43nk "將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至 AWS 上的新 VPC")。  

  * [將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至 AWS 上的現有 VPC ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://fwd.aws/zrP4g "將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至 AWS 上的現有 VPC")。

  **重要事項：**     
  如果您要將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至現有 VPC，則請確定您的 VPC 在資料庫實例的不同「可用性區域」中有兩個公用子網路。這些子網路在其路由表中需要 NAT 閘道或 NAT 實例，以容許實例下載套件及軟體，而不需要向網際網路公開它們。您還需要 DHCP 選項中配置的網域名稱選項，如 Amazon VPC 文件中所述。  

  同時，請務必建立關聯於現有 VPC 的安全群組，並將埠 22 及 7051 的入埠規則新增至此安全群組。埠 22 的 TCP 連線容許對所產生實例的 SSH 存取，而埠 7051 的 TCP 連線容許對對等節點實例的外部 gRPC 存取（使用 Fabric 工具 CLI 及 Fabric SDK 操作對等節點的必要項目）。當您啟動「快速入門」時，系統會提示您提供這些 VPC 設定。

2. 檢查導覽列右上角所顯示的地區，並視需要進行變更。這是將建置對等節點網路基礎架構的位置。依預設，會在「美國東部（俄亥俄州）地區」中啟動範本。

3. 在「選取範本」頁面上，保留範本 URL 的預設值，然後選擇`下一步`。

4. 在「指定詳細資料」頁面上，視需要變更堆疊名稱。檢閱範本的參數。提供需要輸入的參數值。對於所有其他參數，請檢閱預設值，並視需要進行自訂。完成檢閱及自訂參數時，請選擇`下一步`。

在下列各表格中，參數會依種類列出，並分別針對這兩個部署選項進行說明：

  * [將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至新 VPC 的參數](#remote-peer-aws-parameters-newvpc)

  * [將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至現有 VPC 的參數](#remote-peer-aws-parameters-existvpc)。

### 將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 部署至新 VPC 的參數
{: #remote-peer-aws-parameters-newvpc}

下表列出 AWS 圖表的可配置參數及其預設值。需要所有的值。

|  參數     | 說明    | 預設值  |
| --------------|-------------|---------|
| `堆疊名稱` |堆疊名稱是一個 ID，可協助您從堆疊清單中找到特定堆疊。堆疊名稱只能包含英數字元（區分大小寫）及連字號。它的開頭必須是英文字母，且長度不能超過 128 個字元。| |
| | | |
| **網路配置** | |
| `可用性區域` | 要用於 VPC 中子網路的兩個「可用性區域」。附註：會保留邏輯順序。| |
| `容許的 SSH 存取 CIDR` | 容許的 [CIDR 區塊 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC 及子網路")，用於對「IBM Blockchain 對等節點」實例的外部 SSH 存取。它可以設為 0.0.0.0/0，以容許從任何位置存取（不建議使用）。| |
| `PeerEndpointAccessCIDR` | 容許的 [CIDR ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC 及子網路") 區塊，用於對「IBM Blockchain 對等節點」實例的外部 gRPC 存取。通常設為 0.0.0.0/0，以容許從任何位置存取（不建議使用）。| |
| | | |
| **Amazon EC2 配置** | | |
| `InstanceType` | 對等節點實例的 EC2 實例類型。| m4.xlarge |
| `KeyPairName` | AWS 地區內的現有 EC2 金鑰組名稱。您必須產生此項目。| |
| | | |
|**IBM Blockchian 配置** | |
| `IBMBlockchainVersion` | 要部署的 IBM Blockchain 版本。| 1.2.1 |
| `StateDatabase` | 用於儲存區塊鏈狀態的資料庫類型。此選項應該符合網路其餘部分所使用的「狀態資料庫」類型。| CouchDB|
| `PeerVolumeSize` | 用來儲存對等節點之持續資料（分類帳、狀態資料庫、MSP）的「EBS 磁區大小」大小 (GB)。| 100 |
| `對等節點 1 登記 ID`| 您在 IBM Blockchain Platform 使用者介面的「憑證管理中心」畫面中針對第一個對等節點輸入的「登記 ID」。|  |
| `對等節點 1 登記密碼` | 您在 IBM Blockchain Platform 使用者介面的「憑證管理中心」畫面中針對第一個對等節點輸入的「登記密碼」。| |
| `對等節點 2 登記 ID` | 您在 IBM Blockchain Platform 使用者介面的「憑證管理中心」畫面中針對第二個對等節點輸入的「登記 ID」。| |
| `對等節點 2 登記密碼` | 您在 IBM Blockchain Platform 使用者介面的「憑證管理中心」畫面中針對第二個對等節點輸入的「登記密碼」。| |
| | | |
|**IBM Blockchain 服務認證**| | |
| `組織 MSP` | 您可以在 IBM Blockchain Platform 使用者介面中找到此值。按一下「概觀」畫面上的「遠端對等節點配置」按鈕，然後在這裡複製並貼上該資訊。| |
| `憑證管理中心 (CA) 名稱` | 您可以在 IBM Blockchain Platform 使用者介面中找到此值。按一下「概觀」畫面上的「遠端對等節點配置」按鈕，然後在這裡複製並貼上該資訊。| |
| `憑證管理中心 (CA) URL` | 您可以在 IBM Blockchain Platform 使用者介面中找到此值。按一下「概觀」畫面上的「遠端對等節點配置」按鈕，然後在這裡複製並貼上該資訊（包括埠）。如果未指定，則預設埠是 443。| |
| `憑證管理中心 (CA) TLS 憑證`| 您可以在 IBM Blockchain Platform 使用者介面中找到此值。按一下「概觀」畫面上的「遠端對等節點配置」按鈕，然後在這裡複製並貼上該資訊。| |
| | | |
|**其他參數**| | |
| `QSS3BucketName` |「快速入門」資產的 S3 儲存區名稱。「快速入門」儲存區名稱可以包括數字、小寫字母、大寫字母及連字號 (-)。它不能以連字號 (-) 開頭或結尾。| `aws-quickstart` |
| `QSS3KeyPrefix` |「快速入門」資產的 S3 金鑰字首。「快速入門」金鑰字首可以包括數字、小寫字母、大寫字母、連字號 (-) 及正斜線 (/)。| `quickstart-ibm-fabric/` |

1. 在「選項」頁面上，您可以為堆疊中的資源指定標籤（鍵值組），並設定進階選項。完成之後，請選擇「下一步」。

2. 在「檢閱」頁面上，檢閱並確認範本設定。在「功能」下，選取勾選框以確認範本將建立 IAM 資源。

3. 選擇「建立」以部署堆疊。

4. 監視堆疊的狀態。所有堆疊的狀態都是 `CREATE_COMPLETE` 時，即備妥「對等節點」叢集。順利完成之後，如果選擇 couchDB，則應該具有含四個巢狀堆疊的根堆疊，或者，如果選擇 levelDB，則為含兩個巢狀堆疊的根堆疊。

5. 使用堆疊之「輸出」標籤中所顯示的資訊，來檢視已建立的資源。


### 將 {{site.data.keyword.blockchainfull_notm}} Platform 對等節點部署至現有 VPC 的參數
{: #remote-peer-aws-parameters-existvpc}

如果您要將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點部署至現有 VPC，則應該採用下列考量：

 - 確定您的 VPC 在資料庫實例的不同「可用性區域」中有兩個專用子網路。這些子網路在其路由表中需要 NAT 閘道或 NAT 實例，以容許實例下載套件及軟體，而不需要向網際網路公開它們。

 - 在 DHCP 選項中配置網域名稱選項，如 [Amazon VPC 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html "DHCP 選項集") 中所述。  

- 建立關聯於現有 VPC 的安全群組，並將埠 22 及 7051 的入埠規則新增至此安全群組。埠 22 的 TCP 連線容許對所產生實例的 SSH 存取，而埠 7051 的 TCP 連線容許對對等節點實例的外部 gRPC 存取（使用 Fabric 工具 CLI 及 Fabric SDK 操作對等節點的必要項目）。當您啟動「快速入門」時，系統會提示您提供這些 VPC 設定。

 將 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點部署至現有 VPC 時，下列參數會取代[上方](#remote-peer-aws-parameters-newvpc)對應區段中的參數：

|  參數     | 說明    | 預設值  |
| --------------|-------------|---------|
| **網路配置** | | |
| `VPCID` |	用於部署的現有 VPC ID。| |
| `AvailabilityZone1` | 要在其中部署第一個對等節點的「可用性區域」。| |
| `SubnetID1` |	用於第一個對等節點的子網路 ID。子網路應該屬於所選擇的 VPC。若要尋找屬於 VPC 的子網路，請檢查您的 AWS VPC 儀表板，然後選取「子網路」功能表。| |
| `AvailabilityZone2` | 要在其中部署第二個對等節點的「可用性區域」。| |
| `SubnetID2` |	用於第二個對等節點的子網路 ID。子網路應該屬於所選擇的 VPC。若要尋找屬於 VPC 的子網路，請檢查您的 AWS VPC 儀表板，然後選取「子網路」功能表。| |
| | | |
| **Amazon EC2 配置**| | |
| `InstanceType` 	| 對等節點實例的 EC2 實例類型。| m4.xlarge |
| `KeyPairName` |	 AWS 地區內的現有 EC2 金鑰組名稱。您必須產生此項目。| |
| `SecurityGroup` | AWS 地區內的現有 EC2 安全群組 ID。您應該容許埠 22 及 7051 的入埠 TCP 連線。|	| |

## 步驟五：測試部署
{: #remote-peer-aws-test}

AWS CloudFormation 範本已順利建立堆疊時，您的 AWS 帳戶中將會執行兩個 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點實例。實例將會根據「快速入門」範本中指定的`組織 MSP `與`對等節點登記 ID` 組合來命名。例如，`org1-remotepeer1`。  

![對等節點 AWS EC2 實例](../images/remote_peer_AWS_EC2_instances.png "對等節點 AWS EC2 實例")  
*圖 3. AWS EC2 實例上的遠端對等節點*

若要驗證對等節點是否正在執行，請執行下列動作：

  * 在 AWS 主控台中選取實例（按一下**服務 > EC2 > 實例**），然後按一下**連接**按鈕，以 SSH 方式進入新建立的 VPC。請遵循 AWS 的指示，以發出 `ssh` 指令。  
  * 從指令行中執行 `docker ps` 來檢視執行中容器。每個虛擬機器都包括一個對等節點容器及一個 ecs-agent。如果您已選取 CouchDB 作為您的分類帳資料庫，則也會有一個 CouchDB 容器。

  ```
  CONTAINER ID        IMAGE                                STATUS              PORTS                          NAMES
  fb3c49fe52fe        amazon/amazon-ecs-agent:latest       Created                                            ecs-agent
  667780cf3cd3        ibmblockchain/fabric-peer:1.2.1      Up                  0.0.0.0:7051->7051/tcp         peer
  2aa143c81027        ibmblockchain/fabric-couchdb:0.4.6   Up                  4369/tcp, 5984/tcp, 9100/tcp   couchdb
  ```

  * 您可以執行 `docker exec -it peer sh`，以在對等節點容器內建立 Shell 階段作業。

此外，若要驗證對等節點連線適用於 {{site.data.keyword.blockchainfull_notm}} Platform 網路，您可以從對等節點容器內執行對等節點 CLI 指令。請執行 `peer channel fetch` CLI 指令，從頻道提取初始區塊：

1. 從「網路監視器」之「概觀」畫面中提供的`連線設定檔`擷取配置資訊。按一下**連線設定檔**，然後按一下**下載**。

   - 搜尋**排序節點**來找出排序節點 URL，其位於 `orderers > url` 下。請記下以網路名稱開頭的 URL。此 URL 類似下列範例：

   ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

   - 搜尋**組織**來找出您組織的名稱。這應該是您用來登錄對等節點的相同組織。您可以同時找到組織的名稱及其關聯的 `mspid`。此值也可以在「網路監視器」的「概觀」畫面中使用。按一下**遠端對等節點配置**按鈕。該值會顯示在`組織 MSP` 下方。請記下 `mspid` 的值。

   - 如果您尚未這麼做，請執行 `docker exec -it peer sh`，以在對等節點容器內建立 Shell 階段作業。

   ```
   docker exec -it peer sh
   ```
   {:codeblock}

   - 將排序節點 TLS 憑證從連線設定檔複製到對等節點。導覽至 **orderers** 區段。複製接在 "pem:" 後面的憑證，其開頭為 -----BEGIN CERTIFICATE-----，且結尾為 -----END CERTIFICATE-----。請勿包含引號。從指令行執行下列指令，並將 `<orderer cert>` 取代為您已從 creds.json 檔案複製的內容。

   ```
   echo -e "<orderer cert>" > /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem
   ```
   {:codeblock}

   將 `<PEER_ENROLL_ID>` 取代為「快速入門」範本中所指定並與此對等節點實例相關聯的登記 ID。

2. 您的組織必須先新增至網路中的頻道，才能提取初始區塊。

  - 您可以為對等節點啟動新的頻道。當您作為頻道起始者時，可以在[建立頻道](create_channel.html#creating-a-channel)期間自動併入您的組織。

  - 區塊鏈網路的另一個成員也可以使用[頻道更新](create_channel.html#updating-a-channel)，將您的組織新增至現有的頻道。

  - 將您的組織新增至頻道之後，您需要將對等節點的簽署憑證新增至頻道。在安裝期間，對等節點會上傳其簽署憑證，因此您只需要將憑證同步化至頻道。在「網路監視器」的「頻道」畫面上，找出您組織加入的頻道，然後從**動作**標頭下的下拉清單中選取**同步憑證**。此動作會同步處理頻道上所有對等節點中的憑證。

3. 執行下列指令，以在對等節點容器中設定環境變數。

   ```
   export ORDERER_1=<ORDERER_URL>
   export CHANNEL=<CHANNEL_NAME>
   export ORGID=<ORGANIZATION_MSP_ID>
   export PEERADDR=<PEER_ADDR>
   ```
   {:codeblock}

   將欄位取代為您自己的資訊。
     - 將 `<ORDERER_URL>` 取代為 `creds.json` 檔案中排序節點的主機名稱及埠。
     - 將 `<CHANNEL_NAME>` 取代為對等節點將加入的頻道名稱。
     - 將 `<ORGANIZATION_MSP_ID>` 取代為 `creds.json` 檔案中的組織名稱。
     - 將 `<PEER_ADDR>` 取代為 `localhost:7051`

   例如：

   ```
   export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
   export CHANNEL=defaultchannel
   export ORGID=PeerOrg1
   export PEERADDR=localhost:7051
   ```

4. 執行下列對等節點 CLI 指令，以提取頻道的初始區塊。

   **重要事項：**在下列指令中，將每個出現的 `<PEER_ENROLL_ID>` 都取代為與「快速入門」範本中所指定之這個對等節點實例相關聯的登記 ID。執行 `ls /etc/hyperledger/` 指令，即可找到此值。將會列出兩個資料夾：`fabric`，而第二個資料夾是您的 `<PEER_ENROLL_ID>`。

   ```
   CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/<PEER_ENROLL_ID>/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem --tls
   ```
   {:codeblock}


   **附註：**執行下列任何 CLI 指令時，可能會看到可以放心忽略的下列警告。

   ```
   [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
   /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem
   ```

   執行下列指令，以驗證指令順利運作，並驗證初始區塊已新增至對等節點容器：

   ```
  ls *.block
  ```
   {:codeblock}

   看到類似下列範例的內容時，您知道已順利新增初始區塊：

   ```
  defaultchannel_0.block
  ```

   恭喜！您的 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 對等節點已順利連接至您的 {{site.data.keyword.blockchainfull_notm}} Platform 網路。

## 常見問題
{: #remote-peer-aws-faq}

* **問**：當我啟動「快速入門」時發現 CREATE_FAILED 錯誤。
* **答**：如果 AWS CloudFormation 無法建立堆疊，則建議您重新啟動範本，並將「失敗時回復」設為`否`（此設定在 AWS CloudFormation 主控台之「選項」頁面的「進階」下方）。使用此設定時，將會保留堆疊的狀態，而且實例將會保持執行狀態，因此您可以對問題進行疑難排解（請查看 `%ProgramFiles%\Amazon\EC2ConfigService` 及 `C:\cfn\log` 中的日誌檔）。

  - 當您將「失敗時回復」設為`否`時，將繼續產生此堆疊的 AWS 費用。請務必在完成疑難排解時刪除堆疊。如需相關資訊，請參閱 AWS 網站上的 [AWS CloudFormation 疑難排解 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html "AWS CloudFormation 疑難排解")。

* **問**：我在部署 AWS Cloudformation 範本時發現大小限制錯誤。
* **答**：建議您從我們提供的位置或從另一個 S3 儲存區啟動「快速入門」範本。如果您從電腦上的本端副本或非 S3 位置部署範本，則在建立堆疊時可能會遇到範本大小限制問題。如需 AWS CloudFormation 限制的相關資訊，請參閱 [AWS 文件 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html "AWS CloudFormation 限制")。

## 下一步為何？
{: #whats-next-aws}

在 AWS 中設定對等節點之後，您可以先完成數個作業步驟，再將交易提交至區塊鏈網路，並從區塊鏈網路讀取分散式分類帳。如需相關資訊，請參閱[在 AWS 中操作對等節點](remote_peer_operate_aws.html#remote-peer-operate-aws)。

## 高可用性 (HA)
{: #aws-high-availability}

依預設，對於 HA 支援，「快速入門」範本會在兩個不同的可用性區域中部署兩個對等節點實例。若要利用此 HA 支援，您也需要配置[用於高可用性的用戶端應用程式](../v10_application.html#ha-app)。

## 安全考量
{: #remote-peer-aws-security}

AWS Cloud 提供一個可擴充且高度可靠的平台，協助客戶快速且安全地部署應用程式及資料。當您在 AWS 基礎架構上建置系統時，您與 AWS 之間會分攤安全責任。此分攤模型可以減輕您的作業負荷，因為 AWS 可操作、管理及控制元件（從主機作業系統及虛擬化層級向下到服務操作所在設備的實體安全）。因此，您需負責及管理訪客作業系統（包括更新項目及安全修補程式）、其他相關聯的應用程式，以及 AWS 所提供安全群組防火牆的配置。如需 AWS 上安全的相關資訊，請造訪 [AWS Cloud 安全 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://aws.amazon.com/security/ "AWS Cloud 安全")。

### AWS Identity and Access Management (IAM)
{: #remote-peer-aws-iam}

此解決方案會利用具備最低特許存取權的 IAM 角色。不需要或不建議將 SSH 金鑰、秘密金鑰或存取金鑰儲存至佈建的實例。

### OS 安全
{: #remote-peer-aws-ossecurity}

只有使用在部署處理程序期間所指定的 SSH 金鑰，才能存取叢集節點上的 root 使用者。AWS 不會儲存這些 SSH 金鑰，因此，如果您遺失 SSH 金鑰，則可能會失去這些實例的存取權。作業系統修補程式是您的責任，應該要定期執行。

### 安全群組
{: #remote-peer-aws-securitygroups}

安全群組可充當防火牆，用來控制一個以上實例的資料流量。啟動實例時，您可以將一個以上的安全群組與實例相關聯。請將規則新增至每個安全群組，以容許往返其相關聯實例的資料流量。您可以隨時修改安全群組的規則。新的規則會自動套用至與安全群組相關聯的所有實例。在此解決方案中建立及指派給個別實例的安全群組，於容許存取對等節點所需的各種功能時盡可能加以限制。建議您檢閱安全群組，以在叢集啟動並執行之後視需要進一步限制存取權。

### 對等節點安全
{: #aws-security}

對等節點是在 {{site.data.keyword.blockchainfull_notm}} Platform 之外部署；因此，您必須負責管理對等節點的安全。這包括「企業方案」網路所提供的重要安全區域（例如金鑰管理及資料加密）。當您考量對等節點的安全時，請檢閱下列主題。

#### 資料安全
{: #aws-security-data}

{{site.data.keyword.blockchainfull_notm}} Platform 的「企業方案」會使用以[對稱金鑰加密 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "對稱加密法") 為基礎的整個磁碟加密，來保護網路使用的所有資料。您必須在自己的環境中採取類似步驟，才能保護您的對等節點資料。

您狀態資料庫中的資料（無論是使用 levelDB 或 couchDB）都未加密。您可以使用應用程式層次加密來保護狀態資料庫中的靜態資料。

#### 資料常駐
{: #aws-security-data-residency}

資料常駐會強制要求仍在單一國家/地區的邊界內處理及儲存所有區塊鏈分類帳資料。
如需如何達成此目的的詳細資料，請參閱此[主題](remote_peer.html#data-residency)。

#### 金鑰管理
{: #aws-security-key-management}

金鑰管理是對等節點安全的重要層面。如果私密金鑰已受損或遺失，則惡意動作者或許能夠存取您對等節點的資料及功能。{{site.data.keyword.blockchainfull_notm}} Platform 的「企業方案」會使用[硬體安全模組](../glossary.html#hsm) (HSM)，來儲存網路的私密金鑰。HSM 是一種可阻止其他人存取您的私密金鑰的實體應用裝置。

當您在 AWS 上部署對等節點時，表示您要負責管理私密金鑰。雖然 {{site.data.keyword.blockchainfull_notm}} Platform 會產生您的私密金鑰，但這些金鑰不會儲存在「平台」上。務必確保將您的金鑰儲存在安全的位置，以免它們受損。您可以在對等節點 MSP 的 keystore 資料夾中，於對等節點容器的 `/etc/hyperledger/<PEER_ENROLL_ID>/msp/keystore/` 目錄下找到對等節點的私密金鑰。如需對等節點內憑證的相關資訊，請造訪[在 {{site.data.keyword.blockchainfull_notm}} Platform 上管理憑證](../certificates.html)主題的[成員資格服務提供者](../certificates.html#msp)一節。

您可以使用「金鑰委託」來回復遺失的私密金鑰。這需要在遺失任何金鑰之前先執行。如果無法回復私密金鑰，則您需要從「憑證管理中心」取得新的 signCert，才能取得新的私密金鑰。您也應該從任何加入的頻道中，移除及取代您的管理者憑證。

<!---
In IBP when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

#### TLS
{: #aws-security-tls}

[傳輸層安全 ![外部鏈結圖示](../images/external_link.svg "外部鏈結圖示")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL 或 TLS 信號交換的概觀") (TLS) 內嵌在 Hyperledger Fabric 的信任模型中。{{site.data.keyword.blockchainfull_notm}} Platform 上的所有元件都會使用 TLS 來彼此鑑別及通訊。因此，{{site.data.keyword.blockchainfull_notm}} Platform 上的網路元件需要能夠完成與對等節點的 TLS 信號交換。其連帶作用就是，您需要在用戶端應用程式到對等節點的防火牆中啟用透通，例如使用白名單。


#### 成員資格服務提供者配置
{: #aws-security-MSP}

IBM Blockchain Platform 的元件透過「成員資格服務提供者 (MSP)」來取用身分。MSP 會讓 CA 發出的憑證與網路及頻道角色產生關聯。如需 MSP 如何與對等節點搭配運作的相關資訊，請參閱此[主題](../certificates.html#msp)。

#### 應用程式安全
{: #aws-security-appl}

因為已簽署所有鏈碼呼叫，所以 Fabric 會管理應用程式安全。此外，Fabric 也會包括 ACL 型應用程式層次檢查。

## 授權與定價
{: #license-pricing-aws}

您必須接受 {{site.data.keyword.blockchainfull_notm}} Platform for AWS 的 Community Edition 授權版本，才能使用「快速入門」所啟用的部署解決方案。使用 {{site.data.keyword.blockchainfull_notm}} Platform for AWS（包括透過「快速入門」供應項目所提供的所有套件，以及從這些套件所衍生的套件）不適用於正式作業。IBM 可能隨時決定取消存取程式碼及使用此程式碼的授權。
{{site.data.keyword.blockchainfull_notm}} Platform for AWS 軟體授權合約包含授權條款的其他詳細資料。當您啟動「快速入門」時，系統會要求您閱讀並同意合約條款。

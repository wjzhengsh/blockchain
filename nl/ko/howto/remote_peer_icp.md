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

# {{site.data.keyword.cloud_notm}} Private에 원격 피어 배치
{: #remote-peer-icp}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


다음 지시사항에서는 ICP({{site.data.keyword.cloud_notm}} Private)에서 {{site.data.keyword.blockchainfull}} Platform 원격 피어를 설치하고 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 연결하는 방법을 설명합니다.{:shortdesc}

{{site.data.keyword.cloud_notm}} Private는 온프레미스 컨테이너화 애플리케이션을 개발하고 관리하는 데 사용하는 애플리케이션 플랫폼입니다. {{site.data.keyword.cloud_notm}} Private의 {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers는 고객이 로컬 환경에 피어를 배치하도록 ICP와 함께 번들된 {{site.data.keyword.blockchainfull_notm}} 제품입니다. 원격 피어의 Helm 차트를 설치하고 나면 ICP 카탈로그에서 해당 차트를 애플리케이션으로 찾을 수 있습니다. ICP의 원격 피어에서 피어 관리를 위해 ICP의 스토리지, 보안, 로깅 및 지원 서비스를 활용합니다. ICP에 관한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 문서")를 참조하십시오.

ICP에 원격 피어를 설치하기 전에 [원격 피어 고려사항](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations)을 검토하십시오.

## 전제조건
{: #prerequisites-icp}

ICP에 원격 피어를 설치하기 전에 {{site.data.keyword.cloud_notm}} Private, 로컬 시스템 및 {{site.data.keyword.blockchainfull_notm}} Platform에서 다음 단계를 완료해야 합니다.

1. 로컬 시스템

  {{site.data.keyword.cloud_notm}} Private CLI [2.1.0.3 설치 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "{{site.data.keyword.cloud_notm}} Private CLI")를 설치해야 합니다.  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} Private

  [{{site.data.keyword.cloud_notm}} Private ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v2.1.0.3 문서") 클러스터 버전 2.1.0.3이 필요합니다. 개발, 테스트 또는 실험을 위해 원격 피어를 사용하는 경우 [{{site.data.keyword.cloud_notm}} Private Community Edition ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "{{site.data.keyword.cloud_notm}}e Private-CE 설치")을 무료로 설치할 수 있습니다.

  **참고**:
    - 피어에서 사용할 스토리지를 결정해야 합니다. 기본 설정을 사용하는 경우 Helm 차트에서 피어 데이터의 새로운 8 Gi Persistent Volume 청구를 `my-data-pvc`라는 이름으로 작성합니다. CouchDB를 원장 데이터베이스로 선택하면 Helm 차트에서 원장 데이터베이스용으로 또 하나의 8 Gi Persistent Volume 청구를 `statedb-pvc`라는 이름으로 작성합니다. 
    - 기본 스토리지 설정을 사용하지 않으려면 ICP 설치 중에 *새* storageClass가 설정되었는지 확인하거나, Kubernetes 시스템 관리자 배치하기 전에 storageClass를 작성해야 합니다.
    - [동적 프로비저닝 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "동적 볼륨 프로비저닝")은 ICP의 amd64 노드에만 사용할 수 있습니다. 따라서 클러스터에 s390x 와 amd64 작업자 노드가 혼합되어 있는 경우 동적 프로비저닝을 사용할 수 없습니다.
    - 동적 프로비저닝을 사용하지 않는 경우 [지속적 볼륨 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "지속적 볼륨")을 작성하여 Kubernetes PVC(Persistent Volume Claim) 바인드 프로세스를 세분화하는 데 사용할 수 있는 레이블로 설정해야 합니다.

  ICP 하드웨어 요구 사항 외에도 원격 피어에는 최소 다음이 필요합니다.
    - 2x vCPU
    - 2GB RAM
    - 체인코드용 4GB 공간
    - 원장이 확장됨에 따라 증가할 수 있는 원장용 10GB 공간

3. {{site.data.keyword.blockchainfull_notm}} Platform  

  원격 피어를 사용하려면 {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 Blockchain 네트워크의 멤버인 조직이 있어야 합니다. 네트워크의 API 엔드포인트와 네트워크 인증 정보에 액세스하려면 {{site.data.keyword.cloud_notm}}에서 네트워크 모니터를 사용해야 합니다. Blockchain 네트워크의 구성원이 아니면 네트워크를 작성하거나 가입해야 합니다. 자세한 정보는 [네트워크 작성](/docs/services/blockchain/get_start.html#creating-a-network) 또는 [네트워크에 가입](/docs/services/blockchain/get_start.html#joining-a-network)을 참조하십시오.

## Helm 차트를 ICP에 가져오기

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer 패키지는 [Github ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer")에서 사용할 수 있습니다. 명령행을 사용하여 Helm 차트를 ICP 콘솔에 가져옵니다. 로컬 시스템에서 다음 단계를 완료하여 Helm 차트를 가져오십시오.

1. [Github ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "IBM Blockchain Remote Peer Helm Chart").<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->에서 `.tgz` Helm 차트를 다운로드하십시오.

2. 아직 로그인하지 않은 경우 ICP CLI에서 ICP 클러스터에 로그인한 다음 Docker 사설 이미지 레지스트리에 로그인하십시오.  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  이 코드에서 `<cluster_CA_domain>`은 인증 기관(CA) 도메인입니다. CA 도메인을 지정하지 않으면
기본값은 `mycluster.icp`입니다.

3. 다운로드한 Helm 차트를 저장한 디렉토리의 ICP CLI에서 다음 명령을 실행하여 Helm 차트를 ICP 클러스터에 가져오십시오. `<helm_chart_from_github>`를 GitHub에서 다운로드한 `.tgz` 파일의 이름으로 바꾸십시오.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  이 명령이 완료되면 다음 정보와 비슷한 내용이 표시됩니다.

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  이제 {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Helm 차트 타일이 ICP 카탈로그 페이지에 표시되어야 합니다. ICP 콘솔에서 **카탈로그** 단추를 클릭하여 가져오기에 성공했는지 확인하십시오.

### 원격 피어 배치

다음 단계에서는 ICP에서 원격 피어를 배치하는 데 필요한 프로세스에 관해 설명합니다. ICP에서 원격 피어를 구성하기 전에 {{site.data.keyword.blockchainfull_notm}} Platform에 관한 엔드포인트 정보를 검색하고 원격 피어의 사용자 ID를 작성하십시오. 나중 단계에서 이 정보를 사용해야 합니다.

## 1단계: 원격 피어 구성 정보 검색
{: #network-endpoints}

구성 중에 원격 피어에 네트워크의 API 엔드포인트를 제공해야 합니다. 해당 엔드포인트를 사용하면 원격 피어가 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크를 찾아 연결할 수 있습니다. 네트워크 모니터의 **개요** 화면에서 **원격 피어 구성** 단추를 클릭하십시오.

![원격 피어 구성](../images/myresources_starter.png "원격 피어 구성")
*그림 1. 원격 피어 구성*

팝업 창이 열리고 다음 필드의 값이 표시됩니다. 원격 피어를 구성할 때 해당 값을 저장하십시오.

  - **네트워크 ID**
  - **조직 MSP**
  - **인증 기관(CA) 이름**
  - **인증 기관(CA) URL**
  - **인증 기관(CA) TLS 인증서**

필드를 개별적으로 복사하여 붙여넣거나 **다운로드** 링크를 클릭하여 JSON 파일로 저장할 수 있습니다. JSON에서 정보를 다운로드하는 경우 TLS 인증서를 원격 피어에 제공하기 전에 PEM 형식으로 변환해야 한다는 점을 **참고**하십시오. `echo -e "<CERT>" > catls.pem` 명령을 실행하여 인증서를 PEM 형식으로 변환하는 데 CLI를 사용할 수 있습니다.

## 2단계: 원격 피어 등록
{: #register-peer}

{{site.data.keyword.blockchainfull_notm}} Platform에서 조직에 새 피어 ID를 추가해야 원격 피어에서 네트워크에 가입할 수 있습니다. 원격 피어를 등록하려면 다음 단계를 완료하십시오.

1. {{site.data.keyword.blockchainfull_notm}} Platform에 로그인하여 네트워크 모니터에 액세스하십시오. "인증 기관" 화면에서 관리자나 클라이언트 애플리케이션과 같이 조직에 등록한 모든 ID를 볼 수 있습니다.
  ![CA 화면](../images/CA_screen_starter.png "CA 화면")
  *그림 2. CA 화면*

2. 화면 맨 위에서 **사용자 추가** 단추를 클릭하십시오. 팝업 화면이 열리고 다음 필드를 채우면 원격 피어를 등록할 수 있습니다. ID와 시크릿의 값을 저장한 다음 피어를 구성할 때 사용해야 합니다.
  - **ID:** 피어를 구성할 때 `enroll ID`로 참조되는 피어의 사용 이름입니다. 원격 피어를 배치할 때 나중에 사용하도록 **이 값을 저장**하십시오.
  - **시크릿:** 피어를 구성할 때 `enroll Secret`으로 참조되는 피어의 비밀번호입니다. 원격 피어를 배치할 때 나중에 사용하도록 **이 값을 저장**하십시오.
  - **유형:** 이 필드의 `peer`를 선택하십시오.
  - **소속:** 원격 피어가 속할 `org1`과 같은 조직의 소속입니다. 드롭 다운 목록에서 기존 소속을 선택하거나 새 소속을 입력하십시오.
  - **최대 등록 수:** 이 ID를 사용하여 인증서를 등록하거나 생성할 수 있는 횟수를 제한하는 데 이 필드를 사용할 수 있습니다. 값을 지정하지 않으면 무제한 등록으로 기본값이 지정됩니다.

  필드를 완료한 후 **제출**을 클릭하여 원격 피어를 등록하십시오. 등록된 피어는 조직의 식별자로 테이블에 나열됩니다.

## 3단계: 원격 피어 구성 및 설치

네트워크 모니터에 피어 ID를 등록하고 나면 아래 ICP 콘솔 지시사항을 사용하여 ICP에 원격 피어를 구성하고 설치할 수 있습니다.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. ICP 콘솔에 로그인하고 오른쪽 상단에서 **카탈로그** 링크를 클릭하십시오.
2. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer의 Helm 차트를 찾은 후 클릭하여 여십시오. Helm 차트 설치 및 구성에 대한 정보가 포함된 readme 파일이 표시됩니다.
3. **구성**을 클릭하십시오. 구성 페이지에서 Helm 차트의 구성 설정을 완료한 후 라이센스 계약을 읽고 동의함을 확인합니다.
4. **설치**를 클릭하십시오.

### 구성 매개변수
{: #icp-configuration-parms}

원격 피어가 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 연결하는 데 필요한 정보를 제공해야 합니다.
다음 테이블에는 {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer 차트의 구성 가능한 매개변수와 해당 기본값이 나열됩니다.


|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
|**구성** | **원격 피어의 릴리스 이름과 대상 네임스페이스 구성** |  ||
| `Release name`| Helm 릴리스의 이름입니다. | 없음 | 예 |
| `Target namespace`| Kubernetes 네임스페이스입니다. | 기본값 | 예 |
| | | | |
|**클러스터 구성** |** 클러스터 구성 정보 ** | ||
| `Worker node architecture`| 클라우드 플랫폼 아키텍처(amd64 또는 S390x)를 선택합니다. | 마스터 노드를 기반으로 자동 발견된 아키텍처 | 예 |
| `Image`| Helm 차트의 경로입니다. | 설치된 경로로 자동 입력됩니다. 이 값을 변경하지 마십시오. | 예 |
| | | | |
|**Blockchain 네트워크** | **원격 피어에 필요한 네트워크 구성 정보**| | |
| `Network ID`| {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor에 있는 네트워크 ID 값입니다. "개요" 화면에서 `원격 피어 구성` 단추를 클릭한 다음 해당 정보를 복사하여 여기에 붙여넣으십시오. | 없음 | 예 |
| `Organization MSP`| {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor에 있는 조직 MSP ID 값입니다. "개요" 화면에서 `원격 피어 구성` 단추를 클릭한 다음 해당 정보를 복사하여 여기에 붙여넣으십시오. | 없음 | 예 |
| `Certificate Authority (CA) Name`| {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor에 있는 CA 이름 값입니다. "개요" 화면에서 `원격 피어 구성` 단추를 클릭한 다음 해당 정보를 복사하여 여기에 붙여넣으십시오. |없음| 예 |
| `Certificate Authority (CA) URL`| {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor에 있는 CA URL 값입니다. "개요" 화면에서 `원격 피어 구성` 단추를 클릭한 다음 해당 정보를 복사하여 여기에 붙여넣으십시오. | 없음 | 예 |
| `Certificate Authority (CA) TLS Certificate` | {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor의 CA TLS 인증서 문자열입니다. "개요" 화면에서 `원격 피어 구성` 단추를 클릭한 다음 해당 정보를 복사하여 여기에 붙여넣으십시오. | 없음 | 예 |
| | | | |
|**원격 피어 ID** | **원격 피어를 등록하는 데 사용하는 등록 ID와 시크릿**| | |
| `Peer enroll ID`| {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor의 "인증 기관" 화면에 입력한 등록 ID입니다. | 없음 | 예 |
| `Peer enroll secret`| {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor의 "인증 기관" 화면에 입력한 등록 시크릿입니다. | 없음 | 예 |
| | | | |
|**원격 피어 데이터베이스** | **원장 데이터베이스 유형**| | |
| `Ledger database`| 채널 원장을 저장하는 데 사용한 [상태 데이터베이스](/docs/services/blockchain/glossary.html#state-database)입니다. 원격 피어에서는 [Blockchain 네트워크](/docs/services/blockchain/v10_dashboard.html#network-preferences)와 동일한 데이터베이스를 사용해야 합니다. | 없음 | 예 |
| `CouchDB image`| CouchDB가 원장 데이터베이스로 선택된 경우에만 적용됩니다. | 설치된 경로로 자동 입력됩니다. 이 값을 변경하지 마십시오. | 예 |
|**데이터 지속성** | 클러스터가 다시 시작하거나 실패한 후 데이터를 지속하는 기능을 사용합니다. 자세한 정보는 [Kubernetes의 스토리지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/ "볼륨")을 참조하십시오. | | |
| `Data persistence enabled`| 컨테이너를 다시 시작하면 상태 데이터를 사용할 수 있습니다. *선택하지 않으면 장애 복구 또는 팟(Pod) 다시 시작 시 모든 데이터가 유실됩니다.* | 선택됨 | 아니오 |
| `Use dynamic provisioning`| Kubernetes 동적 스토리지를 사용합니다. | 선택됨 | 아니오 |
| | | | |
|**지속적 볼륨 구성**| **피어에서 사용할 지속적 볼륨 청구** |  |  |
| `Persistent volume claim`| 작성될 새 지속적 볼륨 청구(PVC)의 이름을 입력하십시오. | my-data-pvc | 아니오 |
| `Storage class name`| 스토리지 클래스 이름을 선택합니다. | 새 PVC를 작성하는 경우 공백입니다. 그렇지 않으면 기존 PVC와 연관된 스토리지 클래스를 지정하십시오. | 아니오 |
| `Existing volume claim`| 기존 볼륨 청구의 이름을 지정하고 다른 모든 필드는 공백으로 두십니다. | 새 청구 이름 | 아니오 |
| `Storage access mode`| PVC의 스토리지 [액세스 모드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "액세스 모드")를 지정하십시오. | ReadOnlyMany| 아니오 |
| `Selector label`| PVC의 [선택기 레이블 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 기본값 | 아니오 |
| `Selector value`|PVC의 [선택기 값 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 기본값 | 아니오 |
| `Volume claim size`| 볼륨 청구의 크기는 2Gi보다 커야 합니다. | 8Gi  | 예 |
| | | | |
|**상태 데이터베이스 데이터 지속성** | **컨테이너가 다시 시작하거나 실패한 후 데이터를 지속합니다. 자세한 정보는 [Kubernetes의 스토리지![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/ "볼륨")을 참조하십시오.** | | |
| `Data persistence enabled`| 컨테이너를 다시 시작하면 원장 데이터를 사용할 수 있습니다. *선택하지 않으면 장애 복구 또는 팟(Pod) 다시 시작 시 모든 데이터가 유실됩니다.*| 선택됨 | 아니오 |
| `Use dynamic provisioning`| Kubernetes 동적 스토리지를 사용합니다. | 선택됨 | 아니오 |
| | | | |
|**상태 데이터베이스 지속적 볼륨 구성** |**상태 데이터베이스에 사용할 지속적 볼륨 청구입니다. 자세한 정보는 [Kubernetes의 스토리지![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/ "볼륨")을 참조하십시오.** |||
| `Persistent volume claim`| 작성될 새 CouchDB 지속적 볼륨 청구의 이름을 입력하거나 기본값을 사용하십시오. | statedb-pvc | 아니오 |
| `State database storage class name`|스토리지 클래스 이름을 선택합니다.| 없음 | 아니오 |
| `State database existing volume claim`| 기존 볼륨 청구를 사용하려면 pvc 이름을 입력하고 다른 모든 필드는 공백으로 두십시오. | 없음 | 아니오 |
| `State database storage access mode`| PVC의 스토리지 [액세스 모드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "액세스 모드")를 지정하십시오. | ReadOnlyMany| 아니오 |
| `Selector label`|PVC의 [선택기 레이블 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 기본값 | 아니오 |
| `Selector value`|PVC의 [선택기 값 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 기본값 | 아니오 |
| `State database volume claim size`| 지속적 볼륨 청구의 크기입니다. | 8Gi | 아니오 |
| | | | |
| **피어 리소스** | **피어 컨테이너의 최소 및 최대 CPU와 메모리** | | |
| `Peer CPU request` | 피어에 할당할 최소 CPU 수입니다. |2 | 예 |
| `Peer CPU limit` | 피어에 할당할 최대 CPU 수입니다. |2 | 예 |
| `Peer Memory request` | 피어에 할당할 최소 메모리 크기입니다. | 4Gi | 예 |
| `Peer Memory limit` | 피어에 할당할 최대 메모리 크기입니다. | 4Gi | 예 |
| | | | |
|**CouchDB 리소스** | **CouchDB 컨테이너에 할당된 최소 및 최대 CPU와 메모리입니다. 이 섹션은 CouchDB가 상태 데이터베이스로 선택된 경우에만 적용됩니다.**| | | |
| `CouchDB CPU request` | CouchDB에 할당할 최소 CPU 수입니다. |2 | 예 |
| `CouchDB CPU limit` | CouchDB에 할당할 최대 CPU 수입니다. |2 | 예 |
| `CouchDB Memory request` | CouchDB에 할당할 최소 메모리 크기입니다. | 4Gi | 예 |
| `CouchDB Memory limit` | CouchDB에 할당할 최대 메모리 크기입니다. | 4Gi | 예 |

CouchDB를 원격 피어 데이터베이스로 선택하면 팟(Pod)에 두 개의 컨테이너가 작성됩니다. 하나는 피어용이고 다른 하나는 CouchDB용입니다.
피어 컨테이너에는 파일 시스템에 블록과 트랜잭션을 저장하는 피어 PVC에 대한 단일 볼륨 마운트가 포함됩니다. CouchDB 컨테이너는 원장 데이터를 포함하는 피어 상태 데이터베이스 PVC를 마운트합니다.

LevelDB를 원격 피어 데이터베이스로 선택하면 피어와 LevelDB 프로세스를 모두 실행하기 위해 하나의
컨테이너가 팟에 생성됩니다. 이 컨테이너에는 두 개의 볼륨 마운트가 있습니다. 하나는 피어 PVC용이고 두 번째 볼륨 마운트는 원장 데이터를 포함하는 피어 상태 데이터베이스 PVC용입니다.

| 원격 피어 데이터베이스 선택  | 컨테이너 #1의 컨텐츠 | 컨테이너 #2의 컨텐츠 |
| --------------|-----------------|---------------|
|CouchDB | 피어 PVC를 마운트하는 원격 피어| 상태 데이터베이스 PVC를 마운트하는 CouchDB |
|LevelDB | 피어 PVC와 상태 데이터베이스 PVC를 마운트하는 원격 피어 및 LevelDB| 해당사항 없음 |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](/docs/services/blockchain/howto/remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  이 명령에서는 피어 TLS 인증서인 `peer-cert.pem` 파일을 생성합니다.

--->

## 4단계: 원격 피어 설치 확인
{: #verify-installation-icp}

원격 피어를 설치한 후 피어 로그를 보고 피어 설치에 성공했는지 확인할 수 있습니다.

1. **설치**를 클릭한 다음 팝업 창에서 **Helm 릴리스 보기** 단추를 클릭하십시오. 아래로 스크롤하여 **팟(Pod)** 섹션으로 이동하십시오. 팟 상태가 **실행 중**으로 표시되면 **배치** 섹션에서 원격 피어 인스턴스를 찾을 수 있습니다.

2. ICP 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하십시오. 메뉴 표시줄에서 **워크로드** > **배치**를 클릭하십시오. 배치 테이블에서 사용자가 작성하는 원격 피어 인스턴스를 클릭하면 배치 개요 화면이 열립니다. 원격 피어 로그를 보려면 **로그** 탭을 클릭하십시오. 검색 필드에 `Started` 문자열을 입력할 수 있습니다.

  - 원격 피어가 정상적으로 시작되면 다음 예와 비슷한 로그를 볼 수 있습니다.
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - 화면에 로그가 표시되지 않으면 원격 피어가 성공적으로 시작되지 않은 것입니다. 추가 로그를 보고 문제점을 파악하려면 왼쪽 상단에서 **메뉴** 아이콘을 클릭한 다음 **워크로드** > **Helm 릴리스**를 클릭하십시오. Helm 릴리스를 클릭하여 여십시오. 연관된 **팟(Pod)** 옆의 **로그 보기** 링크를 클릭하여 Kibana 인터페이스에서 추가 차트 로그를 보십시오.

3. 원격 피어에서 {{site.data.keyword.blockchainfull}} Platform 네트워크에 연결할 수 있는지 확인하려면
원격 피어 컨테이너 내부에서 피어 CLI 명령을 실행할 수 있습니다. `peer channel fetch` CLI 명령을 실행하여 채널의 최초 블록을 페치하십시오.

1. ICP 클러스터에 아직 연결하지 않은 경우 다음 [지시사항](/docs/services/blockchain/howto/remote_peer_operate_icp.html#remote-peer-kubectl-configure)에 따라 연결하고 피어 컨테이너에서 cli를 사용하십시오.

2. 네트워크의 채널에 조직을 추가해야 최초 블록을 페치할 수 있습니다.

     - 원격 피어의 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 하나 이상 있어야 네트워크 모니터에서 채널을 작성할 수 있습니다.  

     - Blockchain 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다. 플랫폼에서 피어를 호스팅하지 않는 경우에도 {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 있는 채널 구성원은 네트워크 모니터를 사용하여 채널에 조직을 추가할 수 있습니다.

     원격 피어에서 설치 중에 서명 인증서를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다.

3. 네트워크 모니터의 "개요" 화면에서 사용할 수 있는 `creds.json` 파일에서 구성 정보를 검색하십시오. **연결 프로파일**을 클릭한 다음 **다운로드**를 클릭하십시오.

     - `orderers > url`에 있는 **순서 지정자**를 검색하여 순서 지정자 URL을 찾으십시오. 네트워크 이름으로 시작하는 URL을 기록해 두십시오. URL은 다음 예와 비슷합니다.

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

   - **조직**을 검색하여 조직의 이름을 찾으십시오. 이 조직은 원격 피어를 등록하는 데 사용하는 조직과 같아야 합니다. 연관된 `mspid`와 함께 조직의 이름을 찾을 수 있습니다. `mspid`의 값을 기록해 두십시오.

4. 다음 명령을 실행하여 피어 컨테이너에서 환경 변수를 설정하십시오.

       ```
       export ORDERER_1=<ORDERER_URL>
       export CHANNEL=<CHANNEL_NAME>
       export CC_NAME=<CC_NAME>
       export ORGID=<ORGANIZATION_MSP_ID>
       export PEERADDR=<PEER_ADDR>
       ```
       {:codeblock}

       고유 정보로 필드를 바꾸십시오.
         - `<ORDERER_URL>`을 `creds.json` 파일에 있는 순서 지정자의 호스트 이름과 포트로 바꾸십시오.
         - `<CHANNEL_NAME>`을 피어가 가입하는 채널의 이름으로 바꾸십시오.
         - `<CC_NAME>`을 체인코드를 나타내는 이름으로 바꾸십시오.
         - `<ORGANIZATION_MSP_ID>`를 `creds.json` 파일의 조직 이름으로 바꾸십시오.
         - `<PEER_ADDR>`을 `localhost:7051`로 바꾸십시오.

       예를 들어 다음과 같습니다.

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. 다음 피어 CLI 명령을 실행하여 채널의 최초 블록을 페치하십시오.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **참고:** 해당 CLI 명령을 실행할 때 무시해도 되는 다음과 같은 경고가 표시될 수 있습니다.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     다음 명령을 실행하여 명령이 제대로 작동했으며 최초 블록이 피어 컨테이너에 추가되었는지 확인하십시오.

     ```
     ls *.block
     ```
     {:codeblock}

     다음 예와 비슷한 내용이 표시되면 최초 블록이 제대로 추가된 것입니다.

     ```
     defaultchannel_0.block
     ```

## 다음 작업
{: #whats-next-icp}

ICP에 원격 피어를 설정하고 나면, Blockchain 네트워크에 트랜잭션을 제출하고 분산된 원장을 읽을 수 있도록 여러 운영 단계를 완료할 수 있습니다. 자세한 정보는 [원격 피어 운영](/docs/services/blockchain/howto/remote_peer_operate_icp.html)을 참조하십시오.

## 고가용성
{: #high-availability}

고가용성 우수 사례로서 조직당 최소 두 개의 피어를 배치하도록 적극 권장합니다.

다음 단계를 완료하여 ICP에 고가용성 원격 피어를 배치하십시오.

1. [ICP 클러스터의 고가용성 구성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "고가용성 {{site.data.keyword.cloud_notm}} Private 클러스터") 방법을 검토하십시오.

2. 클러스터에 피어를 여러 번 설치하여 여러 다른 작업자 노드에 피어를 배치하십시오. Helm 차트에는 네트워크 ID와 OrgID를 기반으로 하는 비선호도 정책이 포함되어 있습니다. 
ICP 클러스터에서 피어가 동일한 조직의 동일한 네트워크에 연결하는지 발견하고 여러 다른 작업자 노드에서 해당 피어를 배열하려고 합니다.  
  **참고:** 정책에서는 피어를 다른 작업자 노드에 두는 것을 "선호"하지만 필수는 아닙니다. 예를 들어 하나의 작업자 노드만 있으면 모든 피어가 이 노드에 배치됩니다.

두 개 이상의 피어를 배치하는 경우 [애플리케이션의 고가용성](/docs/services/blockchain/v10_application.html#ha-app)도 구성해야 합니다.

## 보안 고려사항
{: #icp-security}

원격 피어는 {{site.data.keyword.blockchainfull}} Platform 외부에 배치되므로 원격 피어의 보안을 관리해야 합니다. 여기에는 엔터프라이즈 플랜 네트워크에서 제공하는 중요한 보안 영역(예: 키 관리 및 데이터 암호화)이 포함됩니다. 원격 피어에 대한 보안을 고려할 때 다음 주제를 검토하십시오.

### ID와 액세스 제어
{: #icp-security-node}

{{site.data.keyword.cloud_notm}} 외에도 원격 피어와 상호작용할 사용자의 ID 관리, 인증 및 액세스 제어를 설정해야 합니다. [ICP 사용자 관리 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "보안")를 사용하여 해당 기능을 구성할 수 있습니다.

### 데이터 보안
{: #icp-security-data}

{{site.data.keyword.blockchainfull}} Platform 엔터프라이즈 플랜에서는 [대칭 키 암호화 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "대칭 키 암호화")를 기반으로 하는 전체 디스크 암호화를 사용하여 네트워크에서 사용하는 모든 데이터를 보호합니다. 원격 피어 데이터를 보호하려면 사용자의 환경에서 비슷한 단계를 수행해야 합니다.

### 데이터 상주
{: #icp-data-residency}

데이터 상주를 사용하면 모든 Blockchain 원장 데이터를 한 국가 내에서 처리하고 저장해야 합니다. 데이터 상주를 수행하는 방법에 관한 자세한 정보는 [데이터 상주](/docs/services/blockchain/howto/remote_peer.html#data-residency)를 참조하십시오.

### 키 관리
{: #icp-security-key-management}

키 관리는 원격 피어 보안의 중요한 요소입니다. 개인 키가 손상되거나 유실되면 적대적인 액터가 원격 피어의 데이터와 기능에 액세스할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에서는 HSM([Hardware Security Modules](/docs/services/blockchain/glossary.html#hsm))을 사용하여 네트워크의 개인 키를 저장합니다. HSM은 다른 당사자가 개인 키에 액세스하지 못하게 하는 물리적 어플라이언스입니다.

ICP에서 원격 피어를 배치하면 사용자가 개인 키를 관리해야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 개인 키를 생성해도 해당 키는 Platform에 저장되지 않습니다. 키가 손상되지 않도록 안전한 위치에 저장하는 것이 중요합니다. 피어 컨테이너의 `/mnt/crypto/peer/peer/msp/keystore/` 디렉토리에 있는 피어 MSP의 키 저장소 폴더에서 원격 피어의 개인 키를 찾을 수 있습니다. 원격 피어의 인증서에 관한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform에서 인증서 관리](/docs/services/blockchain/certificates.html) 튜토리얼의 [Membership Services Provider](/docs/services/blockchain/certificates.html#msp) 섹션을 참조하십시오.

Key Escrow를 사용하여 유실된 개인 키를 복구할 수 있습니다. 이 작업은 키 유실 전에 수행해야 합니다. 개인 키를 복구할 수 없으면 새 ID를 인증 기관에 등록하여 새 개인 키를 가져와야 합니다. 가입한 모든 채널에서 signCert를 제거하고 바꿔야 합니다.

<!---
IBP에서 개인 키를 작성할 때 두 개의 서로 다른 엔티티에 보관되는 두 세트의 개별 키 자료가 생성됩니다. 그런 다음 이 두 세트의 키 자료를 결합하여 개인 키를 작성합니다.
--->

### TLS
{: #icp-security-tls}

[전송 계층 보안 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL 또는 TLS 핸드쉐이크 개요")(TLS)은 Hyperledger Fabric의 신뢰 모델에 임베드됩니다. {{site.data.keyword.blockchainfull_notm}} Platform의 모든 컴포넌트에서는 TLS를 사용하여 서로 인증하고 통신합니다. 따라서 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크 컴포넌트에서 원격 피어를 통해 TLS 핸드쉐이크를 완료할 수 있어야 합니다. 따라서 화이트리스트 등을 사용하여 웹 방화벽을 통과하여 클라이언트 앱에서 피어로 이동할 수 있어야 합니다.

단방향(서버만) 인증이 아니라 양방향(서버-클라이언트) 인증이 필요한 상호 TLS를 사용하여 애플리케이션과 엔터프라이즈 플랜 네트워크 사이의 통신을 보호할 수 있습니다. 네트워크 모니터를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform에서 피어에 대해 [상호 TLS를 사용](/docs/services/blockchain/v10_dashboard.html#mutual-tls)하게 설정할 수 있습니다. 원격 피어에서 상호 TLS를 사용하려면 Hyperledger Fabric 문서의 [피어 노드에 상호 TLS 사용 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "전송 계층 보안으로 통신 보안") 지시사항을 따르십시오. 애플리케이션에 상호 TLS를 사용하는 것이 좋습니다.

### Membership Services Provider 구성
{: #icp-security-MSP}

{{site.data.keyword.blockchainfull_notm}} Platform의 컴포넌트에서는 MSP(Membership Services Provider)를 통해 ID를 이용합니다. MSP는 CA에서 발행하는 인증서를 네트워크 및 채널 역할과 연관시킵니다. MSP가 원격 피어와 작동하는 방식에 관한 자세한 정보는[MSP(Membership Services Provider)](/docs/services/blockchain/certificates.html#msp)를 참조하십시오.

### 애플리케이션 보안
{: #icp-security-appl}

모든 체인코드 호출에 서명되었으므로 Fabric에서 애플리케이션 보안을 관리합니다. Fabric에는 ACL 기반 애플리케이션 레벨 검사도 포함됩니다.

## 라이센스 및 가격
{: #license-pricing-icp}

{{site.data.keyword.cloud_notm}} Private의 {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer는 평가와 실험에 적합한 무료 베타 에디션이며 ICP 애플리케이션으로 배치됩니다. [Github 패키지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and-->에 액세스하고 [Helm 차트 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Helm 차트")를 다운로드할 수 있습니다.

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

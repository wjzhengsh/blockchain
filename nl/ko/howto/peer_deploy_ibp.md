---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: TLS CA, IBM Blockchain Platform, peer, deploy peers, CouchDB container use Kubernetes, IBM Cloud

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private에 피어 배치 및 스타터 플랜 또는 엔터프라이즈 플랜에 연결
{: #ibp-peer-deploy}

다음 지시사항은 {{site.data.keyword.blockchainfull}} Platform 피어를 {{site.data.keyword.cloud_notm}} Private에 배치하고
피어를 {{site.data.keyword.cloud_notm}} 또는 로컬 {{site.data.keyword.cloud_notm}} Private의 스타터 플랜 또는 엔터프라이즈 플랜에 연결하는 방법에 대해 설명합니다.
{:shortdesc}

피어를 배치하기 전에 [고려사항 및 제한사항](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)을 검토하십시오.

스타터 플랜 또는 엔터프라이즈 플랜 네트워크에서는 Hyperledger Fabric v1.1 또는 v1.2.1이 실행되어야 합니다. 네트워크 모니터에서 [네트워크 환경 설정 창](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)을 열어서 Hyperledger Fabric 버전을 찾을 수 있습니다.

## 필수 리소스
{: #ibp-peer-deploy-resources-required}

사용자의 {{site.data.keyword.cloud_notm}} Private 시스템이 최소 하드웨어 리소스 요구사항을 충족하는지 확인하십시오.

| 컴포넌트 | vCPU | RAM | 데이터 스토리지용 디스크 |
|-----------|------|-----|-----------------------|
| 피어 |2 |2GB | 50GB(확장 기능 포함) |
| 피어를 위한 CouchDB<br>(CouchDB를 사용하는 경우에만 적용 가능) |2|2GB | 50GB(확장 기능 포함) |

 **참고:**
 - vCPU는 서버가 가상 머신에 대해 파티션되지 않은 경우 가상 머신 또는 실제 프로세서 코어에 지정되는 가상 코어입니다. {{site.data.keyword.cloud_notm}} Private에서 배치를 위해 가상 프로세서 코어(VPC)를 결정할 때 vCPU 요구사항을 고려해야 합니다. VPC는 IBM 제품의 라이센싱 비용을 판별하는 측정 단위입니다. VPC를 결정하기 위한 시나리오에 대한 자세한 정보는 [가상 프로세서 코어(VPC) ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)를 참조하십시오.
 - 이 최소 리소스 레벨은 테스트 및 실험을 수행하는 데 충분합니다. 트랜잭션의 대형 볼륨이 포함된 환경의 경우 스토리지의 충분한 양을 할당하는 것이 중요합니다(예: 피어를 위해 250GB). 사용할 스토리지의 양은 네트워크에서 필요한 트랜잭션의 수와 서명의 수에 따라 달라집니다. 피어에서 스토리지를 다 써버리는 경우 대형 파일 시스템에서 새 피어를 배치해야 하고 동일한 채널의 기타 컴포넌트를 통해 동기화할 수 있도록 해야 합니다.

## 스토리지
{: #ibp-peer-deploy-storage}

피어에서 사용할 스토리지를 결정해야 합니다. 기본 설정을 사용하는 경우 Helm 차트에서 피어 데이터의 새로운 8 Gi 지속적 볼륨 청구(PVC)를 `my-data-pvc`라는 이름으로 작성하고, 상태 데이터베이스의 다른 8 Gi PVC를 `statedb-pvc`라는 이름으로 작성합니다.

기본 스토리지 설정을 사용하지 않으려면 {{site.data.keyword.cloud_notm}} Private 설치 중에 *새* `storageClass`가 설정되었는지 확인하거나,
Kubernetes 시스템 관리자 배치하기 전에 storageClass를 작성해야 합니다.

amd64 또는 s390x 플랫폼에 피어를 배치하도록 선택할 수 있습니다. 단, [동적 프로비저닝 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "동적 프로비저닝")은 {{site.data.keyword.cloud_notm}} Private의 amd64 노드에 대해서만 사용 가능합니다. 클러스터에 s390x와 amd64 작업자 노드가 혼합되어 있는 경우 동적 프로비저닝을 사용할 수 없습니다.

동적 프로비저닝을 사용하지 않는 경우 [지속적 볼륨 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "지속적 볼륨")을 작성하여 Kubernetes PVC 바인드 프로세스를 세분화하는 데 사용할 수 있는 레이블로 설정해야 합니다.

## 피어를 배치하기 위한 전제조건
{: #ibp-peer-deploy-prerequisites}

1. 피어를 {{site.data.keyword.cloud_notm}} Private에 설치하기 전에 [{{site.data.keyword.cloud_notm}} Private을 설치](/docs/services/blockchain/ICP_setup.html#icp-setup)하고 [{{site.data.keyword.blockchainfull_notm}} Platform Helm 차트를 설치](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)해야 합니다.

2. Community Edition을 사용하고 인터넷 연결 없이 {{site.data.keyword.cloud_notm}} Private 클러스터에 이 Helm 차트를 실행하려면 아카이브를 {{site.data.keyword.cloud_notm}} Private 클러스터에 설치하기 전에 인터넷에 연결된 시스템에서 아카이브를 작성해야 합니다. 자세한 정보는 [인터넷 연결 없이 클러스터에 주요 애플리케이션 추가 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "인터넷 연결 없이 클러스터에 주요 애플리케이션 추가"){:new_window}를 참조하십시오. Helm 차트의 ibm-blockchain-platform-dev/ibm_cloud_pak 아래에서 스펙 파일인 manifest.yaml을 찾을 수 있습니다.

3. {{site.data.keyword.cloud_notm}}에서 스타터 플랜 또는 엔터프라이즈 플랜 네트워크의 멤버인 조직이 있어야 합니다. 피어에서는 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 순서 지정 서비스, Hyperledger Fabric CA 및 API 엔드포인트를 활용하여 운영합니다. 블록체인 네트워크의 구성원이 아니면 네트워크를 작성하거나 가입해야 합니다. 자세한 정보는 [네트워크 작성](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) 또는 [네트워크에 가입](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw)을 참조하십시오.

4. 먼저 {{site.data.keyword.cloud_notm}} Private에 [CA를 배치](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)해야 합니다. TLS CA로 이 CA를 사용합니다. 피어를 배치하기 전에 {{site.data.keyword.cloud_notm}} Private에서 CA를 운영하기 위한 [전제조건 단계](/docs/services/blockchain/howto/CA_operate.html#ca-operate-prerequisites)를 따라야 합니다. 이 단계 이상으로 진행할 필요가 없습니다.

5. {{site.data.keyword.cloud_notm}} Private 콘솔에서 TLS CA의 클러스터 프록시 IP 주소 값을 검색하십시오. **참고:** 프록시 IP에 액세스하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다. {{site.data.keyword.cloud_notm}} Private 클러스터에 로그인하십시오. 왼쪽 탐색 패널에서 **플랫폼**, **노드**를 차례로 클릭하여 클러스터에 정의되어 있는 노드를 표시하십시오. 역할이 `proxy`인 노드를 클릭한 후 테이블에서 `Host IP`의 값을 복사하십시오. **중요:** 이 값을 저장하십시오. 이 값은 Helm 차트의 `Proxy IP` 필드를 구성할 때 사용하게 됩니다.

6. 피어 구성 파일을 작성하고 {{site.data.keyword.cloud_notm}} Private에서 Kubernetes 시크릿으로 이를 저장하십시오. [다음 섹션](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file)에서 이 파일을 작성하기 위한 단계를 찾을 수 있습니다.

## 구성 파일 빌드
{: #ibp-peer-deploy-config-file}

피어를 배치하기 전에 {{site.data.keyword.cloud_notm}}의 피어 ID 및 인증 기관에 대한 중요한 정보가 포함된 구성 JSON 파일을 작성해야 합니다. 그런 다음 [Kubernetes 시크릿 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/configuration/secret/ "시크릿") 오브젝트를 사용하여 구성 중에 이 파일을 Helm 차트에 전달해야 합니다. 이 파일을 통해 피어는 스타터 플랜 또는 엔터프라이즈 플랜에 가입하기 위해 {{site.data.keyword.cloud_notm}}의 인증 기관에서 필요한 인증서를 가져올 수 있습니다. 이 파일에는 사용자가 관리자로 피어를 운영할 수 있는 관리자 인증서도 포함됩니다.

편집하고 로컬 파일 시스템에 저장할 수 있도록 이 지시사항에 템플리트 JSON을 제공합니다. 그런 다음 구성 파일을 완료하기 위해 CA를 사용하는 방법에 대해 설명합니다.

### 구성 파일

구성 파일의 템플리트는 아래에서 찾을 수 있습니다.
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

이 전체 파일을 편집할 수 있는 텍스트 편집기에 복사하고 이를 로컬 파일 시스템에 JSON 파일로 저장하십시오. 이 파일의 상위 두 가지 섹션인 `"enrollment"` 및 `"tls"`만 채워야 합니다.

구성 파일을 완료하려면 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에서 여러 단계를 완료해야 합니다.

1. [스타터 플랜 또는 엔터프라이즈 플랜 CA의 엔드포인트 정보를 검색](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-ibp-ca-endpoint)하십시오.
2. [CA로 피어를 검색](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-peer)하십시오.
3. 피어를 운영하는 데 사용할 [피어 관리자를 등록](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)하십시오. 다른 피어를 배치하도록 관리자를 이미 등록한 경우 이 단계를 완료할 필요는 없습니다.

또한 {{site.data.keyword.cloud_notm}} Private에 배치된 Fabric CA 클라이언트 및 TLS CA를 사용하여 여러 단계를 완료해야 합니다.

1. Fabric CA 클라이언트를 사용하여 [피어 관리자 MSP 폴더를 생성](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin)하십시오.
2. [TLS CA의 엔드포인트 정보를 검색](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-ca-endpoint)하십시오.
3. Fabric CA 클라이언트를 사용하여 [TLS CA로 피어를 등록](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-register-peer)하십시오.


### 스타터 플랜 또는 엔터프라이즈 플랜 CA 정보
{: #ibp-peer-deploy-ibp-ca-endpoint}

먼저 {{site.data.keyword.cloud_notm}}의 CA에 대한 연결 정보를 구성 파일에 제공해야 합니다. 스타터 또는 엔터프라이즈 플랜의 네트워크 모니터 UI에 로그인하십시오. 네트워크 모니터의 **개요** 화면에서 **원격 피어 구성** 단추를 클릭하십시오. 그러면 CA에 대한 필수 정보가 포함된 팝업이 열립니다.

![원격 피어 구성](../images/myresources_starter.png "원격 피어 구성")
*그림 1. 원격 피어 구성 패널*

팝업 창에는 다음 필드가 포함됩니다.

  - **조직 MSP**
  - **인증 기관(CA) 이름**
  - **인증 기관(CA) URL**
  - **인증 기관(CA) TLS 인증서**

파일의 `"components"` 섹션 아래에서 위의 다음 값을 입력하십시오.
- `"caname"`은 **인증 기관(CA) 이름**의 값입니다
- `"cahost"`는 CA URL의 호스트 이름입니다. 예를 들어, CA URL이 `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`인 경우 `"cahost"`의 값은 `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com`이 됩니다.
- `"caport"`는 `"cahost"`의 포트입니다. 예를 들어, CA URL이 `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`인 경우 `"caport"`는 `31011`이 됩니다.
- `"cacert"`는 **인증 기관(CA) TLS 인증서** 필드의 값입니다. 파일에서 인증서를 삽입하기 전에 다음 명령을 실행하고 `<paste in Certificate Authority (CA) TLS Certificate>` 문자열을 네트워크 모니터에서 복사한 값으로 대체하여 base64로 인코딩해야 합니다.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
  ```
  {:codeblock}

  **참고:** 위의 명령으로 생성된 문자열은 하나의 행으로 형식화되어야 합니다. 다음과 유사하게 표시되어야 합니다.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```

  그러나 다음과 같이 표시되어서는 안됩니다.
  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
  ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
  Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
  VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
  WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

파일의 `"catls"` 아래에서 결과 문자열을 `"cacert"` 필드로 붙여넣으십시오. 업그레이드 후 `"cacert"` 필드는 다음 예제와 유사하게 표시됩니다.

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### 피어 등록
{: #ibp-peer-deploy-register-peer}

피어를 채널에 가입하고 체인코드를 설치 및 인스턴스화하려면 먼저 {{site.data.keyword.cloud_notm}}에서 CA로 피어를 등록해야 합니다. 그런 다음 피어 배치는 피어가 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 참여하는 데 필요한 인증서를 생성할 수 있습니다. `enroll ID` 및 `enroll secret`을 사용하여 피어를 등록하기 위한 다음 단계를 완료하십시오. 그런 다음 이 두 값을 구성 파일에 붙여넣습니다.

1. {{site.data.keyword.blockchainfull_notm}} Platform에 로그인하여 네트워크 모니터에 액세스하십시오. "인증 기관" 화면에서 관리자나 클라이언트 애플리케이션과 같이 조직에 등록한 모든 ID를 볼 수 있습니다.
  ![CA 화면](../images/CA_screen_starter.png "CA 화면")
  *그림 2. CA 화면*

2. 화면에서 **사용자 추가** 단추를 클릭하십시오. 팝업 화면이 열리고 다음 필드를 채우면 피어를 등록할 수 있습니다. ID와 시크릿의 값을 저장한 다음 피어를 구성할 때 사용해야 합니다.
  - **등록 ID:** 피어를 구성할 때 `enroll ID`로 참조되는 피어의 사용자 이름입니다. 구성 파일에 **이 값을 저장**하십시오.
  - **시크릿 등록:** 피어를 구성할 때 `enroll Secret`으로 참조되는 피어의 비밀번호입니다. 구성 파일에 **이 값을 저장**하십시오.
  - **유형:** 이 필드의 `Peer`를 선택하십시오.
  - **소속:** 피어가 속할 `org1`과 같은 조직의 소속입니다. 드롭 다운 목록에서 기존 소속을 선택하거나 새 소속을 입력하십시오.
  - **최대 등록 수:** 이 ID를 사용하여 인증서를 등록하거나 생성할 수 있는 횟수를 제한하는 데 이 필드를 사용할 수 있습니다. 값을 지정하지 않으면 무제한 등록으로 기본값이 지정됩니다.

  필드를 완료한 후 **제출**을 클릭하여 피어를 등록하십시오. 등록된 피어는 조직의 식별자로 테이블에 나열됩니다. 보안 조치로 각 ID 및 수반하는 enrollID 및 secret을 사용하여 하나의 피어만 배치하십시오. 피어 ID 및 비밀번호를 재사용하지 마십시오.

3. 구성 파일의 `"components"` 섹션 아래에서 다음 값을 위부터 입력하십시오.
  - `"enrollid"`은 이전 단계의 `enroll ID` 값입니다.
  - `"enrollsecret"`은 이전 단계의 `enroll secret` 값입니다.


### 관리자 작성
{: #ibp-peer-deploy-register-admin}

피어 ID를 등록한 후 피어를 운영하는 데 사용할 관리자 ID도 작성해야 합니다. 먼저 CA로 이 새 ID를 등록하고 MSP 폴더를 생성하는 데 이를 사용해야 합니다. 그런 다음 배치 중에 피어의 관리자 인증서가 작성될 관리자를 구성 파일에 추가합니다. 이를 통해 새 채널 시작 또는 피어에 체인코드 설치와 같은 작업을 통해 블록체인 네트워크를 운영하는 데 관리자 ID의 인증서를 사용할 수 있습니다.

조직에 속하는 컴포넌트에 대한 하나의 관리자 ID를 작성해야 합니다. 다중 피어를 배치하는 경우 이 단계를 한 번만 완료해야 합니다. 배치할 하나의 피어와 조직에 속하는 기타 피어를 위해 생성한 signCert를 사용할 수 있습니다.

1. {{site.data.keyword.blockchainfull_notm}} Platform에 로그인하여 네트워크 모니터에 액세스하십시오. "인증 기관" 화면에서 관리자나 클라이언트 애플리케이션과 같이 조직에 등록한 모든 ID를 볼 수 있습니다.
  ![CA 화면](../images/CA_screen_starter.png "CA 화면")
  *그림 2. CA 화면*

2. 화면에서 **사용자 추가** 단추를 클릭하십시오. 팝업 화면이 열리고 다음 필드를 채우면 피어를 등록할 수 있습니다. ID와 시크릿의 값을 저장한 다음 피어를 구성할 때 사용해야 합니다.
  - **등록 ID:** 피어 관리자를 구성할 때 `enroll ID`로 참조되는 피어의 사용자 이름입니다. 관리자 MSP 폴더를 생성하는 경우를 위해 **이 값을 저장**하십시오.
  - **시크릿 등록:** 피어 관리자를 구성할 때 `enroll Secret`으로 참조되는 피어의 비밀번호입니다. 관리자 MSP 폴더를 생성하는 경우를 위해 **이 값을 저장**하십시오.
  - **유형:** 이 필드의 `Peer`를 선택하십시오.
  - **소속:** 피어가 속할 `org1`과 같은 조직의 소속입니다. 드롭 다운 목록에서 기존 소속을 선택하거나 새 소속을 입력하십시오.
  - **최대 등록 수:** 이 ID를 사용하여 인증서를 등록하거나 생성할 수 있는 횟수를 제한하는 데 이 필드를 사용할 수 있습니다. 값을 지정하지 않으면 무제한 등록으로 기본값이 지정됩니다.

  이 필드를 입력한 후 **제출**을 클릭하여 관리자를 작성하십시오. 그런 다음 작성된 관리자는 조직의 ID로 테이블에 나열됩니다.

### 피어 관리자 MSP 폴더 생성
{: #ibp-peer-deploy-enroll-admin}

관리자 ID를 등록한 후 피어 관리자 MSP 폴더 및 signCert를 생성해야 합니다. 그러므로 스타터 플랜 또는 엔터프라이즈 플랜 CA에 대한 등록 명령을 실행해야 합니다.

1. 아직 수행하지 않은 경우 [Fabric CA 클라이언트](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)를 다운로드하십시오.
2. 암호화 자료를 저장할 디렉토리로 이동하고 피어 관리자의 MSP 폴더를 저장할 폴더를 작성하십시오.

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. 클라이언트가 `$FABRIC_CA_CLIENT_HOME`으로 인증서를 작성할 수 있는 경로를 설정하십시오. 이전 시도에서 작성된 구성 자료를 제거했는지 확인하십시오. `enroll` 명령을 처음 실행하는 경우 `msp` 폴더와 `.yaml` 파일이 없습니다.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. 네트워크 모니터의 "개요" 화면에서 **연결 프로파일** JSON 파일을 열고 다음 변수를 찾으십시오.
  - CA의 URL: `certificateAuthorities` 아래 `url`
  - CA 이름: `caName`

5. 사용하는 서비스 플랜, 위치 및 클러스터에 따라 {{site.data.keyword.cloud_notm}}에서 TLS 인증서를 다운로드하십시오. 인증 기관 URL(예: `us01.blockchain.ibm.com:31011` 또는 `us02.blockchain.ibm.com:31011`)의 도메인 이름을 기반으로 클러스터를 찾을 수 있습니다.

  - 스타터 플랜의 TLS 인증서
    - 미국: [us01.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
    [us03.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
    [us05.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
    [us07.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - 영국: [uk01.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert"); [uk03.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - 시드니: [aus01.blockchain.ibm.com.cert ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert");
  - [엔터프라이즈 플랜의 TLS 인증서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

  이후 명령에서 이 인증서를 참조할 수 있는 디렉토리에 컨텐츠를 저장하십시오.

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. 사용자의 피어 관리자 ID로 인증서를 생성하려면 다음 명령을 실행하십시오.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  위의 `<enroll_id>` 및 `<enroll_password>`는 [네트워크 모니터를 사용하여 등록](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)된 피어 관리자의 **ID** 및 **시크릿**입니다. `<ca_name>` 및 `<ca_url_with_port>`는 연결 프로파일의 `caName` 및 `url` 값입니다. CA URL의 시작 부분에 `http://`를 포함하지 않아야 합니다.

  실제 호출은 다음 예제 명령과 비슷하게 보일 수 있습니다.

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  이 명령이 완료된 후 `$FABRIC_CA_CLIENT_HOME`으로 지정한 디렉토리에 새 MSP 폴더가 생성됩니다. 이 디렉토리에는 피어를 운영하는 데 사용할 인증서가 포함됩니다.

7. 방금 전에 작성한 MSP 폴더에서 새 사용자의 signCert 파일을 열고 이를 base64 형식으로 변환하십시오. MSP 디렉토리의 `signcerts` 폴더에 signCert가 있습니다. 예제 단계를 사용하는 경우 다음 명령을 사용할 수 있습니다.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **참고:** 위의 명령으로 생성된 문자열은 하나의 행으로 형식화되어야 합니다. 다음과 유사하게 표시되어야 합니다.

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   다음과 같이 표시되어서는 안됩니다.

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  이 명령으로 다음 예제와 유사한 문자열이 출력됩니다.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  구성 파일의 컴포넌트 섹션 아래에서 이 문자열을 `"admincerts"` 필드에 입력하십시오.

### TLS CA 정보
{: #ibp-peer-deploy-tls-ca-endpoint}

구성 파일의 `"tls"` 필드에는 {{site.data.keyword.cloud_notm}} Private에 배치한 CA의 정보가 필요합니다. 피어의 보안을 강화하도록 개별 TLS CA로 이 CA를 사용합니다. 관련 정보를 생성하려면 다음 정보를 사용하십시오.

- `"cahost"` 및 `"caport"` 값은 [CA URL](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url)의 URL 및 포트입니다. 예를 들어, CA URL이 `http://9.30.94.174:30167`인 경우 `cahost`의 값은 `9.30.94.174`이 되고 `caport`의 값은 `30167`이 됩니다.
- `"caname"`은 {{site.data.keyword.cloud_notm}} Private에 배치한 CA의 TLS CA 이름입니다. TLS CA 이름은 CA 구성 중에 `CA TLS instance name` 필드에 제공한 값입니다.
- `"cacert"`는 CA의 base64로 인코딩된 TLS 인증서입니다. 전제조건으로 [CA의 TLS 인증서](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)를 검색할 때 명령 출력의 값으로 다음 섹션을 업데이트하십시오.

  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  업그레이드 후 `"cacert"` 필드는 다음 예제와 유사하게 표시됩니다.

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### TLS CA로 피어 등록
{: #ibp-peer-deploy-tls-register-peer}

Fabric CA 클라이언트를 사용하여 {{site.data.keyword.cloud_notm}} Private에서 TLS CA로 피어를 등록해야 합니다.

1. 현재 `$HOME/fabric-ca-client/catls` 폴더에 TLS 인증서 파일 `tls.pem`이 있어야 합니다. 그렇지 않으면 [{{site.data.keyword.cloud_notm}} Private에서 다운로드](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)한 TLS 인증서를 다음 명령에서 참조할 수 있는 디렉토리에 복사할 수 있습니다. `$HOME/fabric-ca-client` 디렉토리에 있는지 확인하십시오.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. TLS CA의 관리자를 사용하여 등록해야 합니다. `$FABRIC_CA_CLIENT_HOME`을 TLS CA 관리자 인증서가 저장될 디렉토리로 변경하십시오.

  ```
  cd $HOME/fabric-ca-client
  mkdir tlsca-admin
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. TLS CA 관리자로 인증서를 생성하려면 다음 명령을 실행하십시오.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  명령의 `<enroll_id>` 및 `<enroll_password>`는 인증 기관을 배치했을 때 Kubernetes 시크릿에 전달한 [CA 관리자 및 비밀번호](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret)입니다. `<ca_url_with_port>` 내부에 [CA URL](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url)을 삽입하십시오. 시작 부분에 `http://`를 포함하지 않아야 합니다. `<tls_ca_name>`은 [CA 구성](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms) 중에 지정된 값입니다.

  `<ca_tls_cert_file>`은 전체 경로가 포함된 [CA TLS 인증서](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) 파일입니다.

  실제 호출은 다음 예제와 비슷하게 보일 수 있습니다.

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  등록한 후 TLS CA로 피어를 등록하는 데 필요한 인증서가 제공됩니다.

4. 다음 명령을 실행하여 소속과 조직 이름을 찾으십시오.

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  명령은 다음 예제와 같을 수 있습니다.

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  다음 예제와 비슷한 정보가 표시되어야 합니다.

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  조직의 **소속(affiliation)** 값(예: 위의 예제에서 `org1` 조직의 `org1.department1`)을 기록해 두십시오. 아래 명령에서 이 값을 사용해야 합니다.

5. 다음 명령을 실행하여 피어를 등록하십시오.

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  `--caname` 필드에 TLS CA 이름을 사용해야 합니다.  피어의 이름과 비밀번호를 작성하고 이를 사용하여 `name`과 `secret`을 바꾸십시오. **중요:** 이 정보를 기록하십시오. 구성 파일의 `"tls"` 섹션에서 `"enrollid"` 및 `"enrollsecret"`으로 `name` 및 `secret`을 입력해야 합니다.

  예제 명령은 다음과 비슷하게 보일 수 있습니다.

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  이 명령을 실행한 후 위의 값을 사용하여 파일을 업데이트할 수 있습니다.

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

  ID는 한 번만 등록할 수 있습니다. 문제점이 발생하면 새 사용자 이름과 비밀번호를 사용하여 명령을 시도하십시오. 보안 조치로 각 ID 및 수반하는 enrollID 및 secret을 사용하여 하나의 피어만 배치하십시오. 피어 ID 및 비밀번호를 재사용하지 마십시오.

  명령이 완료되면 다음 예제와 비슷한 정보가 표시되어야 합니다.

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

트리 명령을 사용하여 구성 파일 준비를 완료했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. 트리 명령은 다음 구조와 유사한 결과를 생성해야 합니다.

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CSR(Certificate Signing Request) 호스트
{: #ibp-peer-deploy-csr-hosts}

피어를 배치하려면 CSR 호스트 이름을 제공해야 합니다. CSR 호스트 이름에는 Helm 차트 호스트 이름이 될 `service host name`을 비롯하여 컴포넌트를 배치하는 클러스터의 프록시 IP 주소가 포함됩니다.

#### 클러스터 프록시 IP 주소의 값 찾기
{: #ibp-peer-deploy-cluster-proxy-ip}

TLS CA를 배치한 동일한 {{site.data.keyword.cloud_notm}} Private 클러스터에 피어를 배치할 경우 동일한 [TLS CA에 대해 구성](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms)할 때 사용한 동일한 프록시 IP를 입력하십시오. 다른 클러스터에 컴포넌트를 배치할 경우 {{site.data.keyword.cloud_notm}} Private 콘솔에서 클러스터 프록시 IP 주소의 값을 검색할 수 있습니다. 사용자는 피어가 배치될 {{site.data.keyword.cloud_notm}} Private 클러스터의 클러스터 관리자 역할이 필요합니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하십시오. 왼쪽 탐색 패널에서 **플랫폼**, **노드**를 차례로 클릭하여 클러스터에 정의되어 있는 노드를 표시하십시오.
2. 역할이 `proxy`인 노드를 클릭한 후 테이블에서 `Host IP`의 값을 복사하십시오.
3. 아래 구성 파일의 `"csr"` 섹션에 있는 `"hosts"`의 값으로 `Host IP`를 삽입하십시오.

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### 서비스 호스트 이름 결정
{: #ibp-peer-deploy-determine-svc-host-name}

`service host name`은 배치 중에 지정하는 `helm release name`이 됩니다. 클러스터 IP 프록시 주소가 "9.42.134.44"이고 `helm release name`이 `org1peer1`인 경우 파일의 `"csr"` 섹션을 삽입합니다.

```
  "csr": {
  "hosts": [
     "9.42.134.44",
     "org1peer1"
  ]
}
```
{:codeblock}

### 구성 파일 완료
{: #ibp-peer-deploy-complete-config}

모든 단계를 완료한 후 업데이트된 구성 파일은 다음 예제와 비슷하게 보입니다.

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer1",
			"enrollsecret": "peer1pw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
           "org1peer1"
        ]
			}
		}
  }
}
```
{:codeblock}

이 파일의 채우기를 완료한 후 JSON 형식으로 이 파일을 저장하고 Kubernetes 시크릿으로 피어 배치에 전달해야 합니다. [다음 섹션](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp)에 있는 단계를 사용하여 시크릿을 작성하십시오.

## 구성 시크릿 작성
{: #ibp-peer-deploy-config-file-ibp}

[Kubernetes 시크릿 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/configuration/secret/ "시크릿")을 통해 정보를 배치에 직접 전달하지 않고 정보를 보호하고 공유할 수 있습니다. 구성 파일을 저장했으면 이를 `base64`으로 인코딩해야 합니다.

1. base64 형식에서 구성 파일을 인코딩하려면 터미널 창에서 다음 명령을 실행하십시오.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **참고:** 위의 명령으로 생성된 문자열은 하나의 행으로 형식화되어야 합니다. 다음과 유사하게 표시되어야 합니다.

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   다음과 같이 표시되어서는 안됩니다.

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  아래 4단계의 결과 출력을 저장하십시오.

2. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하십시오. 왼쪽 탐색 패널에서 **구성**, **시크릿**을 차례로 클릭하십시오. **시크릿 작성** 단추를 클릭하여 새 시크릿 오브젝트를 작성할 수 있는 팝업 패널을 여십시오.

3. **일반** 탭에서 다음 필드를 완료하십시오.
  - **이름:** 클러스터 내에서 시크릿에 고유한 이름을 지정하십시오. 피어를 배치할 때 이 이름을 사용합니다. 이름은 모두 소문자여야 합니다.  
  **참고:** 피어를 배치할 때 새 시크릿은 새 시크릿은 이름이 `<helm release name you intend to use>-secret`인 배치로 자동 생성됩니다. 그러므로 시크릿의 이름을 지정하는 경우 시크릿의 이름은 `<helm release name you intend to use>-secret`과 달라야 합니다. 그렇지 않으면 작성을 시도하는 시크릿이 이미 존재하므로 Helm 차트 배치에 실패합니다.
  - **네임스페이스:** 시크릿을 추가할 네임스페이스입니다. 피어를 배치할 `namespace`를 선택하십시오.
  - **유형:** `Opaque` 값을 입력하십시오.

4. 이 창의 왼쪽 탐색 분할창에서 **데이터** 탭을 클릭하십시오. `Name` 필드에서 `secret.json`을 지정하고 해당 값 필드에 구성 파일의 `base64`로 인코딩된 문자열을 붙여넣으십시오.

5. (선택사항) 상태 데이터베이스로 `CouchDB`를 사용할 계획인 경우 두 개의 추가 값을 이 시크릿 오브젝트에 추가해야 합니다. **데이터** 탭에서 **데이터 추가** 단추를 클릭하여 컨테이너가 배치될 때 데이터베이스에 사용할 CouchDB 사용자 ID 및 비밀번호를 추가하십시오.
  1. 사용자 이름 및 비밀번호를 작성하고 base64 형식으로 이 값을 인코딩하십시오. 터미널 창에서 다음 명령을 실행하고 `admin` 및 `adminpw`를 사용할 값으로 대체하십시오.
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

   2. **이름** 필드에 `couchdbusr` 값을 입력하십시오. 해당 **값** 필드에 위의 1단계로 생성된 `echo -n 'couch' | base64 $FLAG`의 결과를 입력하십시오.
   3. **데이터 추가** 단추를 클릭하여 두 번째 키 값 쌍을 추가하십시오.
   4. 두 번째 **이름** 필드에 `couchdbpwd` 값을 입력하십시오. 해당 **값** 필드에 위의 1단계로 생성된 `echo -n 'couchpw' | base64 $FLAG`의 결과를 입력하십시오.

6. **작성**을 클릭하여 시크릿 오브젝트를 저장하십시오.

**참고:** Helm 릴리스를 삭제할 때 피어 구성 시크릿은 {{site.data.keyword.cloud_notm}} Private 클러스터에서 제거되지 않습니다. 피어를 삭제하는 경우 이 시크릿도 삭제해야 합니다.

## 구성
{: #ibp-peer-deploy-peer-configuration}

피어 구성 시크릿 오브젝트를 작성한 후 다음 단계를 통해 {{site.data.keyword.cloud_notm}} Private에서 피어를 구성하고 설치할 수 있습니다. 한 번에 하나의 피어만 설치할 수 있습니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하고 오른쪽 상단에서 **카탈로그** 링크를 클릭하십시오.
2. 왼쪽 탐색 패널에서 `Blockchain`을 클릭하여 `ibm-blockchain-platform-prod` 또는 `ibm-blockchain-platform-dev`로 레이블된 타일을 찾으십시오(커뮤니티 에디션을 다운로드한 경우). 타일을 클릭하여 이를 열면 Helm 차트 설치 및 구성에 대한 정보가 포함된 Readme 파일이 표시됩니다.
3. 패널의 상단에 있는 **구성** 탭을 클릭하거나 오른쪽 하단 구석에 있는 **구성** 단추를 클릭하십시오.
4. [일반 구성 매개변수](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-configuration-parms)의 값을 지정하고 라이센스 계약에 동의하십시오.
5. `All parameters` 트위스티를 열고 [글로벌 구성 매개변수](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-global-parameters)의 값을 지정하십시오.
6. 아래로 스크롤하여 **피어 구성** 섹션으로 이동하십시오. `Install Peer` 선택란을 선택하고 피어의 [구성 설정](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-parameters)을 완료하십시오.
7. **설치**를 클릭하십시오.

### 구성 매개변수
{: #ibp-peer-deploy-peer-configuration-parms}

다음 표는 {{site.data.keyword.blockchainfull_notm}} Platform의 구성 가능한 매개변수, **피어 컴포넌트에 특정한 값** 및 기본값을 나열합니다. 피어를 실험하거나 처음 시작하는 경우 데이터베이스 및 스토리지 매개변수의 기본값을 사용하십시오. 피어 컴포넌트 섹션으로 스크롤하여 `Install Peer` 선택란을 선택하고 해당 컴포넌트에 대한 연관된 구성 정보만 제공하십시오. **Helm 차트 UI에는 추가 구성이 필요하지 않다고 표시되어 있으나 피어를 배치하려면 특정 매개변수를 입력해야 합니다.**

#### 일반 및 글로벌 구성 매개변수
{: #ibp-peer-deploy-global-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
|**일반 매개변수**| Helm 차트를 구성하는 매개변수입니다. | | |
| `Helm release name`| Helm 릴리스의 이름입니다. 소문자로 시작하고 영숫자 문자로 끝나야 하며 하이픈과 소문자의 영숫자 문자만 포함해야 합니다. 컴포넌트를 설치하려고 할 때마다 고유한 Helm 릴리스 이름을 사용해야 합니다. **중요:** 이 값은 [JSON 시크릿 파일](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-csr-hosts)의 "호스트" 필드에 대한 '서비스 호스트 이름'을 생성하는 데 사용한 값과 일치해야 합니다. | 없음 | 예  |
| `Target namespace`| Helm 차트를 설치할 Kubernetes 네임스페이스를 선택합니다. | 없음 | 예 |
| `Target namespace policies`| 선택한 네임스페이스의 팟(Pod) 보안 정책을 표시하며, 여기에는 **`ibm-privileged-psp`** 정책을 포함해야 합니다. 그렇지 않으면 네임스페이스에 [PodSecurityPolicy를 바인드](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp)하십시오. | 없음 | 아니오 |
|**글로벌 구성**| Helm 차트의 모든 컴포넌트에 적용하는 매개변수입니다.|||
| `Service account name`| 팟(Pod)을 실행하는 데 사용할 [서비스 계정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "팟(Pod)의 서비스 계정 구성")의 이름을 입력합니다. | 기본값 | 아니오 |

#### 피어 구성 매개변수
{: #ibp-peer-deploy-peer-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
|**클러스터 구성** |** 클러스터 구성 정보 ** | ||
| `Install Peer` | 피어를 설치하도록 선택합니다.|선택 취소 | 예(피어를 설치할 경우) |
| `Peer worker node architecture`| 클라우드 플랫폼 아키텍처(AMD64 또는 S390x)를 선택합니다.| AMD64 | 예 |
| `Peer image repository`| 피어 Helm 차트의 위치입니다. 이 필드는 설치된 경로로 자동으로 채워집니다. 커뮤니티 에디션을 사용 중이고 인터넷 액세스 권한이 없는 경우 Fabric 피어 이미지를 다운로드한 디렉토리와 일치해야 합니다. | ibmcom/ibp-fabric-peer | 예 |
| `Peer Docker image tag`| 피어 이미지와 연관된 태그의 값입니다. |1.4.0. 값을 정정하도록 자동으로 채워집니다.| 예|
| `Peer configuration`| 이 필드에서 고유한 `core.yaml` 구성 파일을 붙여넣어 피어의 구성을 사용자 정의할 수 있습니다. 샘플 `core.yaml` 파일을 보려면 [`core.yaml` 샘플 구성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/core.yaml "hyperledger/fabric/core.yaml")을 참조하십시오(**고급 사용자 전용**). |없음| 아니오|
| `Peer configuration secret(필수)`| {{site.data.keyword.cloud_notm}} Private에 작성한 [피어 구성 시크릿](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp)의 이름입니다. | 없음 | 예 |
|`Organization MSP(필수)`| 이 값은 개요 화면의 "원격 피어 구성"을 클릭하여 네트워크 모니터(스타터 플랜 및 엔터프라이즈 플랜 UI)에서 찾을 수 있습니다.  |없음| 예|
|`Peer service type`| 피어에서 [외부 포트 노출![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types "서비스 공개 - 서비스 유형") 여부를 지정하는 데 사용됩니다. 포트를 외부적으로 노출하려면(권장됨) NodePort를 선택하고 포트를 노출하지 않으려면 ClusterIP를 선택하십시오. 이 릴리스에서 LoadBalancer 및 ExternalName은 지원되지 않습니다. | NodePort | 예|
| `State database`|채널 원장을 저장하는 데 사용한 [상태 데이터베이스](/docs/services/blockchain/glossary.html#glossary-state-database)입니다. 피어에서는 [블록체인 네트워크](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)와 동일한 데이터베이스를 사용해야 합니다. |LevelDB | 예 |
|`CouchDB image repository`| CouchDB가 원장 데이터베이스로 선택된 경우에만 적용됩니다. 이 필드는 설치된 경로로 자동으로 채워집니다. 커뮤니티 에디션을 사용 중이고 인터넷 액세스 권한이 없는 경우 Fabric CouchDB 이미지를 다운로드한 디렉토리와 일치해야 합니다.| ibmcom/ibp-fabric-couchdb | 예 |
| `CouchDB Docker image tag`| CouchDB가 원장 데이터베이스로 선택된 경우에만 적용됩니다. CouchDB 이미지와 연관된 태그의 값입니다. |값을 정정하도록 자동으로 채워집니다.| 예 |
| `Peer Data persistence enabled`| 클러스터가 다시 시작하거나 실패한 후 데이터를 지속하는 기능을 사용합니다. 자세한 정보는 [Kubernetes의 스토리지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/ "볼륨")을 참조하십시오.  *선택하지 않으면 장애 복구 또는 팟(Pod) 다시 시작 시 모든 데이터가 유실됩니다.* | 선택됨 | 아니오 |
| `Peer use dynamic provisioning`| 스토리지 볼륨에 대한 동적 프로비저닝을 사용으로 설정할 경우 선택합니다. | 선택됨 | 아니오 |
| `Peer persistent volume claim`| 새 청구에만 해당됩니다.  작성될 새 지속적 볼륨 청구(PVC)의 이름을 입력합니다. | my-data-pvc | 아니오 |
| `Peer storage class name`| 피어의 스토리지 클래스 이름을 지정합니다. | 새 PVC를 작성하는 경우 공백입니다. 그렇지 않으면 기존 PVC와 연관된 스토리지 클래스를 지정하십시오. | 아니오 |
| `Peer existing volume claim`| 기존 볼륨 청구의 이름을 지정하고 다른 모든 필드는 공백으로 두십니다. | 없음 | 아니오 |
| `Peer selector label`| PVC의 [선택기 레이블 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다.| 없음 | 아니오 |
| `Peer selector value`|PVC의 [선택기 값 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 없음 | 아니오 |
| `Peer storage access mode`| PVC의 스토리지 [액세스 모드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "액세스 모드")를 지정하십시오.  | ReadWriteMany| 아니오 |
| `Peer volume claim size`| 볼륨 청구의 크기는 2Gi보다 커야 합니다. | 8Gi  | 예 |
| `State database persistent volume claim`| 새 청구에만 해당됩니다.  작성될 새 지속적 볼륨 청구(PVC)의 이름을 입력합니다. | statedb-pvc | 아니오 |
| `State database storage class name`| 상태 데이터베이스의 스토리지 클래스 이름을 지정합니다. | 없음 | 아니오 |
| `State database existing volume claim`| 기존 볼륨 청구의 이름을 지정하고 다른 모든 필드는 공백으로 두십니다. | 없음 | 아니오 |
| `State database selector label`| PVC를 위한 [선택기 레이블](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)| 없음 | 아니오 |
| `State database selector value`| PVC를 위한 [선택기 값](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) | 없음 | 아니오 |
| `State database storage access mode`| PVC의 스토리지 [액세스 모드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "액세스 모드")를 지정하십시오. | ReadWriteMany| 아니오 |
| `State database volume claim size`| 사용할 디스크 크기를 선택합니다. | 8Gi | 예 |
| `CouchDB - Data persistence enabled`| CouchDB 컨테이너의 경우 컨테이너 다시 시작 시 원장 데이터를 사용할 수 있습니다. *선택하지 않으면 장애 복구 또는 팟(Pod) 다시 시작 시 모든 데이터가 유실됩니다.*| 선택됨 | 아니오 |
| `CouchDB - Use dynamic provisioning`| CouchDB 컨테이너의 경우 Kubernetes 동적 스토리지를 사용합니다.| 선택됨 | 아니오 |
| `Docker-in-Docker CPU request`| 체인코드가 실행되는 컨테이너에 할당할 최소 CPU 수를 지정합니다. |1 | 예 |
| `Docker-in-Docker CPU limit`| 체인코드가 실행되는 컨테이너에 할당할 최대 CPU 수를 지정합니다. |2 | 예 |
| `Docker-in-Docker memory request`| 체인코드가 실행되는 컨테이너에 할당할 최소 메모리 양을 지정합니다. | 1Gi | 예 |
| `Docker-in-Docker  memory limit`| 체인코드가 실행되는 컨테이너에 할당할 최대 메모리 양을 지정합니다. | 4Gi | 예 |
| `gRPC web proxy CPU request`| gRPC 웹 프록시에 할당할 최소 CPU(millicpus) 수를 지정합니다. | 100m | 예 |
| `gRPC web proxy CPU limit`| gRPC 웹 프록시에 할당할 최대 CPU(millicpus) 수를 지정합니다. | 200m | 예 |
| `gRPC web proxy memory request`| gRPC 웹 프록시에 할당할 최소 메모리 크기를 지정합니다. | 100Mi | 예 |
| `gRPC web proxy memory limit`| gRPC 웹 프록시에 할당할 최대 메모리 크기를 지정합니다. | 200Mi | 예 |
| `Peer CPU request` | 피어에 할당할 최소 CPU 수입니다. |1 | 예 |
| `Peer CPU limit` | 피어에 할당할 최대 CPU 수입니다.|2 | 예 |
| `Peer Memory request` | 피어에 할당할 최소 메모리 크기입니다. | 1Gi | 예 |
| `Peer Memory limit` | 피어에 할당할 최대 메모리 크기입니다. | 4Gi | 예 |
| `CouchDB CPU request` | CouchDB에 할당할 최소 CPU 수입니다.|1 | 예 |
| `CouchDB CPU limit` | CouchDB에 할당할 최대 CPU 수입니다. |2 | 예 |
| `CouchDB Memory request` | CouchDB에 할당할 최소 메모리 크기입니다.| 1Gi | 예 |
| `CouchDB Memory limit` | CouchDB에 할당할 최대 메모리 크기입니다. | 4Gi | 예 |


CouchDB를 피어 데이터베이스로 선택하면 팟(Pod)에 두 개의 컨테이너가 작성됩니다. 하나는 피어용이고 다른 하나는 CouchDB용입니다. 피어 컨테이너에는 파일 시스템에 블록과 트랜잭션을 저장하는 피어 PVC에 대한 단일 볼륨 마운트가 포함됩니다. CouchDB 컨테이너는 원장 데이터를 포함하는 피어 상태 데이터베이스 PVC를 마운트합니다.

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

### Helm 명령행을 사용하여 Helm 릴리스 설치
{: #ibp-peer-deploy-helm-cli}

또는 Helm CLI를 사용하여 Helm 릴리스를 설치할 수 있습니다. `helm install` 명령을 실행하기 전에 [Helm CLI 환경에 클러스터의 Helm 저장소를 추가![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Helm CLI에 내부 Helm 저장소 추가")했는지 확인하십시오.

`yaml` 파일을 작성하고 이 파일을 `helm install` 명령에 전달하여 설치에 필요한 매개변수를 설정할 수 있습니다.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

여기서,
- `<helm_release name>`은 Helm 릴리스에 지정할 이름을 표시합니다.
- `<helm_chart>`는 카탈로그에 가져온 Helm 차트의 이름을 표시합니다.
- `<helm_chart_version>`은 카탈로그에 가져온 Helm 차트의 버전을 표시합니다.
- `<customvalues.yaml>`은 구성 매개변수가 포함된 yaml 파일의 이름입니다.

예를 들어, 다음과 같습니다.

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```
다운로드된 아카이브 파일에 포함된 `values.yaml`을 편집하여 새 `yaml` 파일을 작성할 수 있으며, 여기에는 기본 설정이 사용된 모든 필수 매개변수가 포함됩니다.

## 피어 설치 확인
{: #ibp-peer-deploy-verify-installation-ibp}

구성 매개변수를 완료하고 **설치** 단추를 클릭한 후 **Helm 릴리스 보기** 단추를 클릭하여 배치를 보십시오. 성공한 경우 배치 테이블의 `DESIRED`, `CURRENT`, `UP TO DATE` 및 `AVAILABLE` 필드에 값 1이 표시됩니다. 새로 고치기를 클릭하고 테이블이 업데이트될 때까지 기다려야 할 수 있습니다. 또한 {{site.data.keyword.cloud_notm}} Private 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하여 배치 테이블도 찾을 수 있습니다. 메뉴 표시줄에서 **워크로드**, **Helm 릴리스**를 차례로 클릭하십시오.

`Notes` 섹션까지 아래로 스크롤한 경우 [피어 운영](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate) 시 사용할 중요 정보가 있습니다.

## 피어 로그 보기
{: #ibp-peer-deploy-view-logs}

컴포넌트 로그는 [`kubectl CLI 명령`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)을 사용하거나 {{site.data.keyword.cloud_notm}} Private 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다. 자세한 정보는 [로그에 액세스하기 위한 지시사항](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-view-logs)을 참조하십시오.

## 다음에 수행할 작업
{: #ibp-peer-deploy-whats-next}

피어를 배치하고 나면, 블록체인 네트워크에 트랜잭션을 제출하고 분산된 원장을 읽을 수 있도록 여러 작업 단계를 완료해야 합니다. 자세한 정보는 [스타터 플랜 또는 엔터프라이즈 플랜으로 피어 운영](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate)을 참조하십시오.


## 문제점 해결
{: #ibp-peer-deploy-troubleshooting}

### **문제점:** `enroll` 명령을 실행할 때 CA URL에 오류가 발생합니다.
{: #ibp-peer-deploy-ca-enroll-error}

등록 url, `-u` 매개변수값에 특수 문자가 포함된 경우, Fabric CA 클라이언트 등록 명령이 실패할 수 있습니다. 예를 들어, 등록 ID 및 비밀번호가 `admin:C25A06287!0`인 다음 명령의 경우,

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

명령이 실패하고 다음 오류가 생성됩니다.

```
!pw@9.12.19.115: event not found
```

### **솔루션:**
{: #ibp-peer-deploy-ca-enroll-error-solution}

특수 문자를 인코딩하거나 작은따옴표로 url을 묶어야 합니다. 예를 들어, `!`가 `%21`이 되거나 다음과 유사하게 표시됩니다.

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private에 피어 배치
{: #icp-peer-deploy}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

다음 지시사항은 {{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull}} Platform 피어를 배치하는 방법에 대해 설명합니다. 이 지시사항을 통해 {{site.data.keyword.cloud_notm}} Private의 {{site.data.keyword.blockchainfull_notm}} Platform에 연결할 수 있습니다. 피어를 {{site.data.keyword.cloud_notm}}의 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 연결할 경우 [스타터 플랜 또는 엔터프라이즈 플랜에 연결하도록 피어 배치](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)를 참조하십시오.
{:shortdesc}

피어를 배치하기 전에 [고려사항 및 제한사항](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)을 검토하십시오.


## 필수 리소스
{: #icp-peer-deploy-resources-required}

사용자의 {{site.data.keyword.cloud_notm}} Private 시스템이 최소 하드웨어 리소스 요구사항을 충족하는지 확인하십시오.

| 컴포넌트 | vCPU | RAM | 데이터 스토리지용 디스크 |
|-----------|------|-----|-----------------------|
| 피어 |2 |2GB | 50GB(확장 기능 포함) |
| 피어를 위한 CouchDB |2|2GB | 50GB(확장 기능 포함) |

 **참고:**
 - vCPU는 서버가 가상 머신에 대해 파티션되지 않은 경우 가상 머신 또는 실제 프로세서 코어에 지정되는 가상 코어입니다. {{site.data.keyword.cloud_notm}} Private에서 배치를 위해 가상 프로세서 코어(VPC)를 결정할 때 vCPU 요구사항을 고려해야 합니다. VPC는 IBM 제품의 라이센싱 비용을 판별하는 측정 단위입니다. VPC를 결정하는 시나리오에 대한 자세한 정보는 [가상 프로세서 코어(VPC) ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "IBM Licence Metric Tool 9.2")를 참조하십시오.
 - 이 최소 리소스 레벨은 테스트 및 실험을 수행하는 데 충분합니다. 트랜잭션의 대형 볼륨이 포함된 환경의 경우 스토리지의 충분한 양을 할당하는 것이 중요합니다(예: 피어를 위해 250GB). 사용할 스토리지의 양은 네트워크에서 필요한 트랜잭션의 수와 서명의 수에 따라 달라집니다. 피어 또는 순서 지정자에서 스토리지를 다 써버리는 경우 대형 파일 시스템에서 새 피어 또는 순서 지정자를 배치해야 하고 동일한 채널의 기타 컴포넌트를 통해 동기화할 수 있도록 해야 합니다.

## 스토리지
{: #icp-peer-deploy-storage}

피어에서 사용할 스토리지를 결정해야 합니다. 기본 설정을 사용하는 경우 Helm 차트에서 피어 데이터의 새로운 8 Gi 지속적 볼륨 청구(PVC)를 `my-data-pvc`라는 이름으로 작성하고, 상태 데이터베이스의 다른 8 Gi PVC를 `statedb-pvc`라는 이름으로 작성합니다.

기본 스토리지 설정을 사용하지 않으려면 {{site.data.keyword.cloud_notm}} Private 설치 중에 *새* `storageClass`가 설정되었는지 확인하거나 배치하기 전에 Kubernetes 시스템 관리자가 storageClass를 작성해야 합니다.

amd64 또는 s390x 플랫폼에 피어를 배치하도록 선택할 수 있습니다. 단, [동적 프로비저닝 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "동적 볼륨 프로비저닝")은 {{site.data.keyword.cloud_notm}} Private의 amd64 노드에 대해서만 사용 가능합니다. 클러스터에 s390x와 amd64 작업자 노드가 혼합되어 있는 경우 동적 프로비저닝을 사용할 수 없습니다.

동적 프로비저닝을 사용하지 않는 경우 [지속적 볼륨 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "지속적 볼륨")을 작성하여 Kubernetes PVC 바인드 프로세스를 세분화하는 데 사용할 수 있는 레이블로 설정해야 합니다.

## 피어를 배치하기 위한 전제조건
{: #icp-peer-deploy-prerequisites}

1. 피어를 {{site.data.keyword.cloud_notm}} Private에 설치하기 전에 [{{site.data.keyword.cloud_notm}} Private을 설치](/docs/services/blockchain/ICP_setup.html#icp-setup)하고 [{{site.data.keyword.blockchainfull_notm}} Platform Helm 차트를 설치](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)해야 합니다.

2. Community Edition을 사용하고 인터넷 연결 없이 {{site.data.keyword.cloud_notm}} Private 클러스터에 이 Helm 차트를 실행하려면 아카이브를 {{site.data.keyword.cloud_notm}} Private 클러스터에 설치하기 전에 인터넷에 연결된 시스템에서 아카이브를 작성해야 합니다. 자세한 정보는 [인터넷 연결 없이 클러스터에 주요 애플리케이션 추가 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "인터넷 연결 없이 클러스터에 주요 애플리케이션 추가"){:new_window}를 참조하십시오. Helm 차트의 `ibm-blockchain-platform-dev/ibm_cloud_pak` 아래에서 스펙 파일 `manifest.yaml`을 찾을 수 있습니다.

3. 먼저 {{site.data.keyword.cloud_notm}} Private에 [CA를 배치](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)해야 합니다. CA를 사용하여 [피어 구성 파일을 작성하고 {{site.data.keyword.cloud_notm}} Private에서 Kubernetes 시크릿으로 이를 저장](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-config-file)해야 합니다.

4. {{site.data.keyword.cloud_notm}} Private 콘솔에서 CA의 클러스터 프록시 IP 주소 값을 검색하십시오. **참고:** 프록시 IP에 액세스하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다. {{site.data.keyword.cloud_notm}} Private 클러스터에 로그인하십시오. 왼쪽 탐색 패널에서 **플랫폼**, **노드**를 차례로 클릭하여 클러스터에 정의되어 있는 노드를 표시하십시오. 역할이 `proxy`인 노드를 클릭한 후 테이블에서 `Host IP`의 값을 복사하십시오. **중요:** 이 값을 저장하십시오. 이 값은 Helm 차트의 `Proxy IP` 필드를 구성할 때 사용하게 됩니다.


## 피어 구성 시크릿 작성
{: #icp-peer-deploy-config-file}

피어를 배치하려면 피어 ID 및 인증 기관에 대한 중요한 정보가 포함된 구성 파일을 작성해야 합니다. 그런 다음 [Kubernetes 시크릿 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/configuration/secret/) 오브젝트를 사용하여 구성 중에 이 파일을 Helm 차트에 전달해야 합니다. 이 파일을 통해 피어는 블록체인 네트워크에 가입하기 위해 인증 기관에서 필요한 인증서를 가져올 수 있습니다. 이 파일에는 사용자가 피어를 운영할 수 있는 관리자 인증서도 포함됩니다. 피어 구성 전에 [CA를 사용하여 순서 지정자 또는 피어 배치](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)에 대한 지시사항을 따르십시오.

CSR 호스트 이름을 구성 파일에 제공해야 합니다. 여기에는 배치 중에 지정하는 `helm release name`과 동일한 값이 되는 `service host name`이 포함됩니다. 예를 들어, `org1peer1`의 `helm release name`을 지정하는 경우 파일의 `"csr"` 섹션에 다음 값을 삽입해야 합니다.
```
"csr": {
  "hosts": [
     "9.42.134.44",
    "org1peer1"
  ]
}
```
{:codeblock}

구성 파일을 저장한 후 시크릿 오브젝트로 {{site.data.keyword.cloud_notm}} Private에 구성 파일을 제공하려면 base64 형식으로 이를 인코딩해야 합니다. Kubernetes 시크릿을 사용하면 정보를 배치에 직접 전달하지 않고도 정보를 보호하고 공유할 수 있습니다.

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

그러나 다음과 같이 표시되어서는 안됩니다.

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
  **참고:** 피어를 배치할 때 새 시크릿은 새 시크릿은 이름이 `<helm_release_name>-secret`인 배치로 자동 생성됩니다. 그러므로 시크릿의 이름을 지정하는 경우 시크릿의 이름은 `<helm_release_name>-secret`과 달라야 합니다. 그렇지 않으면 작성을 시도하는 시크릿이 이미 존재하므로 Helm 차트 배치에 실패합니다.
  - **네임스페이스:** 시크릿을 추가할 네임스페이스입니다. 피어를 배치할 `namespace`를 선택하십시오.
  - **유형:** `Opaque` 값을 입력하십시오.

4. 이 창의 왼쪽 탐색 분할창에서 **데이터** 탭을 클릭하십시오.
  - `Name` 필드에서 `secret.json`을 지정하고 해당 값 필드에 구성 파일의 `base64`로 인코딩된 문자열을 붙여넣으십시오.

5. (선택사항) 상태 데이터베이스로 `CouchDB`를 사용할 계획인 경우 두 개의 추가 값을 이 시크릿 오브젝트에 추가해야 합니다. **데이터** 탭에서 **데이터 추가** 단추를 클릭하여 컨테이너가 배치될 때 데이터베이스에 사용할 CouchDB 사용자 ID 및 비밀번호를 추가하십시오.
  1. 사용자 이름 및 비밀번호를 작성하고 base64 형식으로 이 값을 인코딩하십시오. 터미널 창에서 다음 명령을 실행하고 `admin` 및 `adminpw`를 사용할 값으로 대체하십시오.
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. **이름** 필드에 `couchdbuser` 값을 입력하십시오. 해당 **값** 필드에 위의 1단계로 생성된 `echo -n 'couch' | base64 $FLAG`의 결과를 입력하십시오.
  3. **데이터 추가** 단추를 클릭하여 두 번째 키 값 쌍을 추가하십시오.
  4. 두 번째 **이름** 필드에 `couchdbpwd` 값을 입력하십시오. 해당 **값** 필드에 위의 1단계로 생성된 `echo -n 'couchpw' | base64 $FLAG`의 결과를 입력하십시오.

6. **작성**을 클릭하여 시크릿 오브젝트를 저장하십시오.

**참고:** Helm 릴리스를 삭제할 때 피어 구성 시크릿은 {{site.data.keyword.cloud_notm}} Private 클러스터에서 제거되지 않습니다. 피어를 삭제하는 경우 이 시크릿도 삭제해야 합니다.

## 구성
{: #peer-configuration}

피어 구성 시크릿 오브젝트를 작성한 후 다음 단계를 통해 {{site.data.keyword.cloud_notm}} Private에서 피어를 구성하고 설치할 수 있습니다. 한 번에 하나의 피어만 설치할 수 있습니다.


1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하고 오른쪽 상단에서 **카탈로그** 링크를 클릭하십시오.
2. 왼쪽 탐색 패널에서 `Blockchain`을 클릭하여 `ibm-blockchain-platform-prod` 또는 `ibm-blockchain-platform-dev`로 레이블된 타일을 찾으십시오(커뮤니티 에디션을 다운로드한 경우). 타일을 클릭하여 이를 열면 Helm 차트 설치 및 구성에 대한 정보가 포함된 Readme 파일이 표시됩니다.
3. 패널의 상단에 있는 **구성** 탭을 클릭하거나 오른쪽 하단 구석에 있는 **구성** 단추를 클릭하십시오.
4. [일반 구성 매개변수](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-configuration-parms)의 값을 지정하고 라이센스 계약에 동의하십시오.
5. `All parameters` 트위스티를 열고 [글로벌 구성 매개변수](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-global-parameters)의 값을 지정하십시오.
6. 아래로 스크롤하여 **피어 구성** 섹션으로 이동하십시오. `Install Peer` 선택란을 선택하고 피어의 [구성 설정](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-parameters)을 완료하십시오.
7. **설치**를 클릭하십시오.

### 구성 매개변수
{: #icp-peer-deploy-configuration-parms}

다음 표는 {{site.data.keyword.blockchainfull_notm}} Platform의 구성 가능한 매개변수, **피어 컴포넌트에 특정한 값** 및 기본값을 나열합니다. 피어를 실험하거나 처음 시작하는 경우 데이터베이스 및 스토리지 매개변수의 기본값을 사용하십시오. 피어 컴포넌트 섹션으로 스크롤하여 `Install Peer` 선택란을 선택하고 해당 컴포넌트에 대한 연관된 구성 정보만 제공하십시오. **Helm 차트 UI에는 추가 구성이 필요하지 않다고 표시되어 있으나 피어를 배치하려면 특정 매개변수를 입력해야 합니다.**

#### 일반 및 글로벌 구성 매개변수
{: #icp-peer-deploy-global-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
| `Helm release name`| Helm 릴리스의 이름입니다. 소문자로 시작하고 영숫자 문자로 끝나야 하며 하이픈과 소문자의 영숫자 문자만 포함해야 합니다. 컴포넌트를 설치하려고 할 때마다 고유한 Helm 릴리스 이름을 사용해야 합니다. **중요:** 이 값은 [JSON 시크릿 파일](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-config-file)의 "호스트" 필드에 대한 '서비스 호스트 이름'을 생성하는 데 사용한 값과 일치해야 합니다. | 없음 | 예 |
| `Target namespace`| Helm 차트를 설치할 Kubernetes 네임스페이스를 선택합니다. | 없음 | 예 |
|**글로벌 구성**| Helm 차트의 모든 컴포넌트에 적용하는 매개변수입니다.|||
| `Service account name`| 팟(Pod)을 실행하는 데 사용할 [서비스 계정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)의 이름을 입력합니다. | 기본값 | 아니오 |

#### 피어 구성 매개변수
{: #icp-peer-deploy-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
|**클러스터 구성** |** 클러스터 구성 정보 ** | ||
| `Install Peer` | 피어를 설치하도록 선택합니다.|선택 취소 | 예(피어를 배치할 경우) |
| `Peer worker node architecture`| 클라우드 플랫폼 아키텍처(AMD64 또는 S390x)를 선택합니다.| AMD64 | 예 |
| `Peer image repository`| 피어 Helm 차트의 위치입니다. 이 필드는 설치된 경로로 자동으로 채워집니다. | ibmcom/ibp-fabric-peer | 예 |
| `Peer Docker image tag`| 피어 이미지와 연관된 태그의 값입니다. |1.2.1. 값을 정정하도록 자동으로 채워집니다.| 예|
| `Peer configuration`| 이 필드에서 고유한 `core.yaml` 구성 파일을 붙여넣어 피어의 구성을 사용자 정의할 수 있습니다. 샘플 `core.yaml` 파일을 보려면 [`core.yaml` 샘플 구성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml)을 참조하십시오(**고급 사용자 전용**). | 없음 | 아니오 |
| `Peer configuration secret(필수)`| {{site.data.keyword.cloud_notm}} Private에 작성한 [피어 구성 시크릿](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-config-file)의 이름입니다. | 없음 | 예 |
|`Organization MSP(필수)`| 새 조직 MSPID 값(예: 'org1')을 작성하거나 피어가 일부가 되는 기존 조직 MSP를 지정할 수 있습니다. 순서 지정자 조직을 배치한 경우 피어 MSPID가 순서 지정자 MSPID와 다른지 확인하십시오. 또한 나중에 `CORE_PEER_LOCALMSPID` 및 `configtx.yaml`에 필요하므로 이 값을 기록해 두십시오. | 없음 | 예 |
|`Peer service type`| 피어에서 [외부 포트 노출![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) 여부를 지정하는 데 사용됩니다. 포트를 외부적으로 노출하려면(권장됨) NodePort를 선택하고 포트를 노출하지 않으려면 ClusterIP를 선택하십시오. 이 릴리스에서 LoadBalancer 및 ExternalName은 지원되지 않습니다. | NodePort | 예 |
| `State database`|채널 원장을 저장하는 데 사용한 [상태 데이터베이스](/docs/services/blockchain/glossary.html#glossary-state-database)입니다. | 없음 | 예 |
|`CouchDB image repository`| CouchDB가 원장 데이터베이스로 선택된 경우에만 적용됩니다. 이 필드는 설치된 경로로 자동으로 채워집니다. 커뮤니티 에디션을 사용 중이고 인터넷 액세스 권한이 없는 경우 Fabric CouchDB 이미지를 다운로드한 디렉토리와 일치해야 합니다.| ibmcom/ibp-fabric-couchdb | 예 |
| `CouchDB Docker image tag`| CouchDB가 원장 데이터베이스로 선택된 경우에만 적용됩니다. CouchDB 이미지와 연관된 태그의 값입니다. | 값을 정정하도록 자동으로 채워집니다. | 예 |
| `Peer Data persistence enabled`| 클러스터가 다시 시작하거나 실패한 후 데이터를 지속하는 기능을 사용합니다. 자세한 정보는 [Kubernetes의 스토리지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/ "볼륨")을 참조하십시오. *선택하지 않으면 장애 복구 또는 팟(Pod) 다시 시작 시 모든 데이터가 유실됩니다.* | 선택됨 | 아니오 |
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
| `Peer CPU request` | 피어에 할당할 최소 CPU 수입니다. |1 | 예 |
| `Peer CPU limit` | 피어에 할당할 최대 CPU 수입니다.|2 | 예 |
| `Peer Memory request` | 피어에 할당할 최소 메모리 크기입니다. | 1Gi | 예 |
| `Peer Memory limit` | 피어에 할당할 최대 메모리 크기입니다. | 4Gi | 예 |
| `CouchDB CPU request` | CouchDB에 할당할 최소 CPU 수입니다.|1 | 예 |
| `CouchDB CPU limit` | CouchDB에 할당할 최대 CPU 수입니다. |2 | 예 |
| `CouchDB Memory request` | CouchDB에 할당할 최소 메모리 크기입니다.| 1Gi | 예 |
| `CouchDB Memory limit` | CouchDB에 할당할 최대 메모리 크기입니다. | 4Gi | 예 |


CouchDB를 피어 데이터베이스로 선택하면 팟(Pod)에 두 개의 컨테이너가 작성됩니다. 하나는 피어용이고 다른 하나는 CouchDB용입니다.
피어 컨테이너에는 파일 시스템에 블록과 트랜잭션을 저장하는 피어 PVC에 대한 단일 볼륨 마운트가 포함됩니다. CouchDB 컨테이너는 원장 데이터를 포함하는 피어 상태 데이터베이스 PVC를 마운트합니다.

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
{: #icp-peer-deploy-helm-cli}

또는 Helm CLI를 사용하여 `helm` 릴리스를 설치할 수 있습니다. `helm install` 명령을 실행하기 전에 [Helm CLI 환경에 클러스터의 Helm 저장소를 추가![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Helm CLI에 내부 Helm 저장소 추가")했는지 확인하십시오.

`yaml` 파일을 작성하고 이 파일을 `helm install` 명령에 전달하여 설치에 필요한 매개변수를 설정할 수 있습니다.
```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

여기서,
- `<helm_release name>`은 helm 릴리스에 지정할 이름을 표시합니다.
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
{: #icp-peer-deploy-verify-installation-icp}

구성 매개변수를 완료하고 **설치** 단추를 클릭한 후 **Helm 릴리스 보기** 단추를 클릭하여 배치를 보십시오. 성공한 경우 배치 테이블의 `DESIRED`, `CURRENT`, `UP TO DATE` 및 `AVAILABLE` 필드에 값 1이 표시됩니다. 새로 고치기를 클릭하고 테이블이 업데이트될 때까지 기다려야 할 수 있습니다. 또한 {{site.data.keyword.cloud_notm}} Private 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하여 배치 테이블도 찾을 수 있습니다. 메뉴 표시줄에서 **워크로드**, **Helm 릴리스**를 차례로 클릭하십시오.

`Notes` 섹션까지 아래로 스크롤한 경우 [피어 운영](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate) 시 사용할 중요 정보가 있습니다.

## 피어 로그 보기
{: #icp-peer-deploy-view-logs}

피어 로그는 [kubectl CLI 명령](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure)을 사용하거나 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다. 자세한 정보는 [로그에 액세스하기 위한 지시사항](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-view-logs)을 참조하십시오.

## 다음 단계
{: #icp-peer-deploy-next-steps}

피어를 배치하고 나면, 블록체인 네트워크에 트랜잭션을 제출하고 분산된 원장을 읽을 수 있도록 여러 작업 단계를 완료해야 합니다. 자세한 정보는 [다중 클라우드 네트워크에서 피어 운영](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)을 참조하십시오.

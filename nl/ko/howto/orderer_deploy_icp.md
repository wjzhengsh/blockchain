---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private에 순서 지정자 배치
{: #icp-orderer-deploy}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

순서 지정자는 블록체인 네트워크의 클라이언트, 순서 지정 트랜잭션 및 브로드캐스트 트랜잭션을 순서 지정자 컴포넌트로 인증합니다. 순서 지정자와 블록체인 네트워크에서 수행하는 역할에 대한 자세한 정보는 [blockchain 컴포넌트 개요](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview)를 참조하십시오.
{:shortdesc}

순서 지정 서비스를 배치하기 전에 [고려사항 및 제한사항](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)을 검토하십시오.

## 필수 리소스
{: #icp-orderer-deploy-resources-required}

사용자의 {{site.data.keyword.cloud_notm}} Private 시스템이 최소 하드웨어 리소스 요구사항을 충족하는지 확인하십시오.

| 컴포넌트 | vCPU | RAM |데이터 스토리지용 디스크 |
|-----------|------|-----|-----------------------|
| 순서 지정자 |2 |512MB |100GB(확장 기능 포함) |


 **참고:**
 - vCPU는 서버가 가상 머신에 대해 파티션되지 않은 경우 가상 머신 또는 실제 프로세서 코어에 지정되는 가상 코어입니다. {{site.data.keyword.cloud_notm}} Private에서 배치를 위해 가상 프로세서 코어(VPC)를 결정할 때 vCPU 요구사항을 고려해야 합니다. VPC는 IBM 제품의 라이센싱 비용을 판별하는 측정 단위입니다. VPC를 결정하는 시나리오에 대한 자세한 정보는 [가상 프로세서 코어(VPC) ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)를 참조하십시오.
 - 이 최소 리소스 레벨은 테스트 및 실험을 수행하는 데 충분합니다. 대량의 트랜잭션을 처리하는 환경에서는 충분히 큰 크기의 스토리지를 할당하는 것이 중요합니다. 예를 들어 순서 지정 서비스의 경우 500GB입니다. 사용할 스토리지의 양은 네트워크에서 필요한 트랜잭션의 수와 서명의 수에 따라 달라집니다. 스토리지를 순서 지정자에 모두 소모하려는 경우 더 큰 파일 시스템으로 새 순서 지정자를 배치하고 동일한 채널의 다른 컴포넌트를 통해 동기화하도록 해야 합니다.

## 스토리지
{: #icp-orderer-deploy-storage}

순서 지정자에서 사용할 스토리지를 결정해야 합니다. 기본 설정을 사용하는 경우 Helm 차트에서 순서 지정자 데이터의 새로운 8Gi PVC(Persistent Volume Claim)를 `orderer-data`라는 이름으로 작성합니다.

기본 스토리지 설정을 사용하지 않으려면 {{site.data.keyword.cloud_notm}} Private 설치 중에 *새* `storageClass`가 설정되었는지 확인하거나 배치하기 전에 Kubernetes 시스템 관리자가 storageClass를 작성해야 합니다.

amd64 또는 s390x 플랫폼에 순서 지정자를 배치하도록 선택할 수 있습니다. 단, [동적 프로비저닝 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "동적 볼륨 프로비저닝")은 {{site.data.keyword.cloud_notm}} Private의 AMD64 노드에서만 사용할 수 있습니다. 클러스터에 s390x와 amd64 작업자 노드가 혼합되어 있는 경우 동적 프로비저닝을 사용할 수 없습니다.

동적 프로비저닝을 사용하지 않는 경우 [지속적 볼륨 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "지속적 볼륨")을 작성하여 Kubernetes PVC 바인드 프로세스를 세분화하는 데 사용할 수 있는 레이블로 설정해야 합니다.


## 순서 지정자 배치에 필요한 전제조건
{: #icp-orderer-deploy-prerequisites}

1. 순서 지정자를 {{site.data.keyword.cloud_notm}} Private에 설치하기 전에 [{{site.data.keyword.cloud_notm}} Private을 설치](/docs/services/blockchain/ICP_setup.html#icp-setup)하고 [{{site.data.keyword.blockchainfull_notm}} Platform Helm 차트를 설치](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)해야 합니다.

2. Community Edition을 사용하고 인터넷 연결 없이 {{site.data.keyword.cloud_notm}} Private 클러스터에 이 Helm 차트를 실행하려면 아카이브를 {{site.data.keyword.cloud_notm}} Private 클러스터에 설치하기 전에 인터넷에 연결된 시스템에서 아카이브를 작성해야 합니다. 자세한 정보는 [인터넷 연결 없이 클러스터에 주요 애플리케이션 추가 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "인터넷 연결 없이 클러스터에 주요 애플리케이션 추가"){:new_window}를 참조하십시오. Helm 차트의 ibm-blockchain-platform-dev/ibm_cloud_pak 아래에 스펙 파일 `manifest.yaml`이 있습니다.

3. {{site.data.keyword.cloud_notm}} Private 콘솔에서 CA의 클러스터 프록시 IP 주소 값을 검색하십시오. **참고:** 프록시 IP에 액세스하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다. {{site.data.keyword.cloud_notm}} Private 클러스터에 로그인하십시오. 왼쪽 탐색 패널에서 **플랫폼**, **노드**를 차례로 클릭하여 클러스터에 정의되어 있는 노드를 표시하십시오. 역할이 `proxy`인 노드를 클릭한 후 테이블에서 `Host IP`의 값을 복사하십시오. **중요:** 이 값을 저장하여 Helm 차트의 `Proxy IP` 필드를 구성할 때 사용합니다.

4. [순서 지정자 구성 파일을 작성하여 {{site.data.keyword.cloud_notm}} Private에 Kubernetes 시크릿으로 저장](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file)하십시오.

## 순서 지정자 구성 파일 작성
{: #icp-orderer-deploy-config-file}

순서 지정자를 배치하기 전에 순서 지정자 ID와 CA에 대한 중요한 정보를 포함하는 구성 파일을 작성해야 합니다. 그런 다음 [Kubernetes 시크릿 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/configuration/secret/) 오브젝트를 사용하여 구성 중에 이 파일을 Helm 차트에 전달해야 합니다. 이 파일을 사용하면 순서 지정자가 블록체인 네트워크에 가입하기 위해 CA로부터 필요한 인증서를 획득할 수 있습니다. 관리자로서 순서 지정자를 운영할 수 있는 관리자 인증서도 포함되어 있습니다. 순서 지정자를 구성하기 전에 [CA를 사용하여 순서 지정자 또는 피어 배치](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)에 대한 지시사항를 따르십시오.

CSR 호스트 이름을 구성 파일에 제공해야 합니다. 배치 중에 지정한 `helm release name`을 기준으로 할 `service host name`이 포함됩니다. `service host name`은 끝에 추가된 문자열 `-orderer`로 지정하는 `helm_release_name`입니다. 예를 들어, `orderer1`의 `helm release name`을 지정하려면 다음 값을 파일의 `"csr"` 섹션에 삽입할 수 있습니다.

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

구성 파일을 저장한 후 시크릿 오브젝트로서 파일을 {{site.data.keyword.cloud_notm}} Private에 제공하기 전에 base64 형식으로 인코딩해야 합니다. Kubernetes 시크릿을 사용하면 정보를 배치에 직접 전달하지 않고도 정보를 보호하고 공유할 수 있습니다.

1. 터미널 창에서 다음 명령을 실행하여 base64 형식으로 구성 파일을 인코딩하십시오.

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
  - **이름:** 클러스터 내에서 시크릿에 고유한 이름을 지정하십시오. 순서 지정자를 배치할 때 이 이름을 사용합니다. 이름은 모든 소문자여야 합니다.  
  **참고:** 순서 지정자를 배치할 때 새 시크릿이 `<helm_release_name>-orderer-mspsecret`라는 이름으로 배치에 의해 자동으로 생성됩니다. 그러므로 시크릿의 이름을 지정할 때 시크릿의 이름이 `<helm_release_name>-orderer-mspsecret`와 다른지 확인하십시오. 그렇지 않으면 작성을 시도하는 시크릿이 이미 존재하므로 Helm 차트 배치에 실패합니다.
  - **네임스페이스:** 시크릿을 추가할 네임스페이스입니다. 순서 지정자를 배치하려는 `namespace`를 선택하십시오.
  - **유형:** `Opaque` 값을 입력하십시오.

4. 이 창의 왼쪽 탐색 분할창에서 **데이터** 탭을 클릭하십시오. `Name` 필드에서 `secret.json`을 지정하고 해당 값 필드에 구성 파일의 `base64`로 인코딩된 문자열을 붙여넣으십시오.

5. **작성**을 클릭하여 시크릿 오브젝트를 저장하십시오.

**참고:** Helm 릴리스를 삭제할 때 순서 지정자 구성 시크릿은 {{site.data.keyword.cloud_notm}} Private 클러스터에서 제거되지 않습니다. 순서 지정자를 삭제하면 이 시크릿도 삭제해야 합니다.

## 구성
{: #icp-orderer-deploy-configuration}


순서 지정자 구성 시크릿 오브젝트를 작성한 후 다음 단계를 통해 {{site.data.keyword.cloud_notm}} Private에서 순서 지정자를 구성하고 설치할 수 있습니다. 한 번에 하나의 순서 지정자만 설치할 수 있습니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하고 오른쪽 상단에서 **카탈로그** 링크를 클릭하십시오.
2. Community Edition을 다운로드한 경우, 왼쪽 탐색 패널에서 `Blockchain`을 클릭하여 `ibm-blockchain-platform-prod` 또는 `ibm-blockchain-platform-dev`라는 타일을 찾으십시오. 타일을 클릭하여 이를 열면 Helm 차트 설치 및 구성에 대한 정보가 포함된 Readme 파일이 표시됩니다.
3. 패널의 상단에 있는 **구성** 탭을 클릭하거나 오른쪽 하단 구석에 있는 **구성** 단추를 클릭하십시오.
4. [일반 구성 매개변수](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-configuration-parms)의 값을 지정하고 라이센스 계약에 동의하십시오.
5. `All parameters` 트위스티를 열고 [글로벌 구성 매개변수](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-global-parameters)의 값을 지정하십시오.
6. 아래로 스크롤하여 **순서 지정자 구성** 섹션으로 이동하십시오. `Install Orderer` 선택란을 선택하고 순서 지정자에 대한 [구성 설정](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-parameters)을 완료하십시오.
7. **설치**를 클릭하십시오.


**참고:** 순서 지정자를 s390x에 배치할 때 init 컨테이너에서 다음 오류 메시지를 리턴할 수 있습니다.

`panic: Error while trying to open DB: no locks available`

이는 순서 지정자가 일반 파일 DB를 사용하고 순서 지정 서비스에서 배타적 잠금이 있는지 여부를 확인하고 배타적 잠금을 작성하기 위해 파일을 조회할 수 있는 기능을 허용하는 추가 패키지가 NFS 파일 시스템에 필요하기 때문입니다. 이 문제를 수정하려면 `rpc-statd` 패키지를 사용으로 설정해야 합니다.

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

NFS 파일 시스템이 제공되는 시스템에서 수행해야 합니다.

### 구성 매개변수
{: #icp-orderer-configuration-parms}

다음 표에는 {{site.data.keyword.blockchainfull_notm}} Platform, **순서 지정자 컴포넌트 관련 항목** 및 해당 기본값의 구성 가능한 매개변수가 나열됩니다. **Helm 차트 UI에 더 이상의 구성이 필요 없다고 되어 있지만 순서 지정자를 배치하려면 특정 매개변수를 입력해야 합니다.**

#### 일반 및 글로벌 구성 매개변수
{: #icp-orderer-deploy-global-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
| `Helm release name`| Helm 릴리스의 이름입니다. 소문자로 시작하고 영숫자 문자로 끝나야 하며 하이픈과 소문자의 영숫자 문자만 포함해야 합니다. 컴포넌트를 설치하려고 할 때마다 고유한 Helm 릴리스 이름을 사용해야 합니다. **중요:** 이 값은 [JSON 시크릿 파일](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file)의 "호스트" 필드에 대한 '서비스 호스트 이름'을 생성하는 데 사용한 값과 일치해야 합니다. | 없음 | 예  |
| `Target namespace`| Helm 차트를 설치할 Kubernetes 네임스페이스를 선택합니다. | 없음 | 예 |
|**글로벌 구성**| Helm 차트의 모든 컴포넌트에 적용하는 매개변수입니다. |||
| `Service account name`| 팟(Pod)을 실행하는 데 사용할 [서비스 계정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)의 이름을 입력합니다. | 기본값 | 아니오 |

#### 순서 지정자 구성 매개변수
{: #icp-orderer-deploy-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
| `Install Orderer`| 순서 지정자를 설치하려면 선택합니다. | 선택 취소 | 예, 순서 지정자를 배치하려는 경우 |
| `Orderer worker node architecture`| {{site.data.keyword.cloud_notm}} Private 작업자 노드 아키텍처(AMD64 또는 S390X)를 선택합니다. | 마스터 노드를 기반으로 자동 발견된 아키텍처 | 예 |
| `Orderer configuration`| 이 필드에 사용자 고유의 `orderer.yaml` 구성 파일을 붙여넣어 순서 지정자의 구성을 사용자 정의할 수 있습니다. 샘플 `orderer.yaml` 파일을 보려면 [`orderer.yaml` 샘플 구성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/orderer.yaml) **고급 사용자 전용**을 참조하십시오. | 없음 | 아니오 |
| `Organization MSP secret (Required)`| 조직 MSP 인증서와 키를 포함하는 시크릿 오브젝트의 이름을 지정합니다. | 없음 | 예 |
| `Orderer data persistence enabled` | 컨테이너를 다시 시작하면 데이터를 사용할 수 있습니다. 선택하지 않으면 장애 복구 또는 팟(Pod) 재시작 시 모든 데이터가 유실됩니다. | 선택됨 | 아니오 |
| `Orderer use dynamic provisioning` | 스토리지 볼륨에 대한 동적 프로비저닝을 사용으로 설정할 경우 선택합니다. | 선택됨 | 아니오 |
| `Orderer image repository` | 순서 지정자 Helm 차트의 위치입니다. 이 필드는 설치된 경로로 자동으로 채워집니다. Community Edition을 사용하고 있고 인터넷에 액세스하지 않는 경우 이 필드를 Fabric 순서 지정자 이미지를 다운로드한 위치로 변경합니다. | ibmcom/ibp-fabric-orderer | 아니오 |
| `Orderer Docker image tag`| Docker 이미지의 레코드입니다. 이 필드는 이미지 버전으로 자동 완성됩니다. 변경하지 마십시오.| 1.2.1 | 예 |
| `Orderer consensus type`| 순서 지정 서비스의 합의 유형입니다. |SOLO | 예 |
| `Orderer organization name`| 순서 지정자 조직에 사용할 이름을 지정합니다. 피어도 배치할 계획이라면 피어에 지정할 이름과 다른 이름을 사용하십시오. 예를 들면, 순서 지정자 조직에 `ordererOrg`와 같은 이름을 지정하십시오. | 없음 | 예 |
| `Orderer Org MSP ID`| 순서 지정자 조직의 MSP ID에 사용할 이름을 지정합니다. 순서 지정자 조직에 제공한 이름과 같아야 하며 배치 프로세스에 의해 환경 변수로 설정됩니다. 이 값을 기록해 두고 이후에 참조해야 합니다. | 없음 | 예 |
| `Orderer storage class name`| 순서 지정자의 스토리지 클래스 이름을 지정합니다. | 없음 | {{site.data.keyword.cloud_notm}} Private 클러스터가 구성되는 방법에 따라 다릅니다. 클러스터 관리자에게 확인 |
| `Orderer existing volume claim`| 기존 볼륨 청구의 이름을 지정하고 다른 모든 필드는 공백으로 두십니다. | 없음 | 아니오 |
| `Orderer selector label`| PVC의 [선택기 레이블 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)입니다. | 없음 | 아니오 |
| `Orderer selector value`| PVC의 [선택기 값 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)입니다. | 없음 | 아니오 |
| `Orderer storage access mode`| PVC의 스토리지 [액세스 모드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)를 지정합니다. | ReadWriteMany | 예 |
| `Orderer volume claim size`| 사용할 디스크의 크기를 선택합니다(2Gi 이상이어야 함). | 8 Gi | 예 |
| 순서 지정자 서비스 유형` | 피어에서 [외부 포트 노출![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) 여부를 지정하는 데 사용됩니다. 포트를 외부적으로 노출하려면(권장됨) NodePort를 선택하고 포트를 노출하지 않으려면 ClusterIP를 선택하십시오. LoadBalancer와 ExternalName은 이 릴리스에서 지원되지 않습니다 | NodePort | 예 |
| `Orderer CPU request`| 순서 지정자에 할당할 최소 CPU 수를 지정합니다. |1 | 예 |
| `Orderer CPU limit`| 순서 지정자에 할당할 최대 CPU 수를 지정합니다. |2 | 예 |
| `Orderer memory request`| 순서 지정자에 할당할 최소 메모리 크기를 지정합니다. | 1Gi | 예 |
| `Orderer memory limit`| 순서 지정자에 할당할 최대 메모리 크기를 지정합니다. | 2Gi | 예 |

### Helm 명령행을 사용하여 Helm 릴리스 설치
{: #icp-orderer-deploy-helm-cli}

또는 Helm CLI를 사용하여 Helm 릴리스를 설치할 수 있습니다. `helm install` 명령을 실행하기 전에 [Helm CLI 환경에 클러스터의 Helm 저장소를 추가![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Helm CLI에 내부 Helm 저장소 추가")했는지 확인하십시오.

`yaml` 파일을 작성하고 이 파일을 다음 `helm install` 명령에 전달하여 설치에 필요한 매개변수를 설정할 수 있습니다.

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
--values orderer-s390x-values.yaml \
--tls
```

다운로드된 아카이브 파일에 포함된 `values.yaml`을 편집하여 새 `yaml` 파일을 작성할 수 있으며, 여기에는 기본 설정이 사용된 모든 필수 매개변수가 포함됩니다.

## 순서 지정자 설치 확인
{: #icp-orderer-deploy-verify-install}

구성 매개변수를 완료하고 **설치** 단추를 클릭한 후 **Helm 릴리스 보기** 단추를 클릭하여 배치를 보십시오. 성공한 경우 배치 테이블의 `DESIRED`, `CURRENT`, `UP TO DATE` 및 `AVAILABLE` 필드에 값 1이 표시됩니다. 새로 고치기를 클릭하고 테이블이 업데이트될 때까지 기다려야 할 수 있습니다. 또한 {{site.data.keyword.cloud_notm}} Private 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하여 배치 테이블도 찾을 수 있습니다. 메뉴 표시줄에서 **워크로드**, **Helm 릴리스**를 차례로 클릭하십시오.

## 순서 지정자 로그 보기
{: #icp-orderer-deploy-view-logs}

컴포넌트 로그는 [`kubectl CLI 명령`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)을 사용하거나 {{site.data.keyword.cloud_notm}} Private 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다. 자세한 정보는 [로그에 액세스하기 위한 지시사항](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-view-logs)을 참조하십시오.

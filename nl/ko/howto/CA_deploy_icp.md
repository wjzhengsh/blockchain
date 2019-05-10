---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

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

# {{site.data.keyword.cloud_notm}} Private에 인증 기관 배치
{: #ca-deploy}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private Helm 차트를 가져오면 개별 컴포넌트를 배치할 수 있습니다. 인증 기관(CA)은 조직의 신뢰 루트이며 배치할 다른 컴포넌트에 대한 인증서를 생성할 수 있도록 합니다. 결과적으로 다른 컴포넌트를 배치하기 전에 CA를 배치해야 합니다. 여러 클라우드 블록체인 네트워크의 각 조직에서는 자체 CA를 배치해야 합니다.  CA와 블록체인 네트워크에서 수행하는 역할에 대한 자세한 정보는 [인증 기관](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca)을 참조하십시오.
{:shortdesc}

인증 기관을 배치하기 전에 [고려사항 및 제한사항](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)을 검토하십시오.

## 필수 리소스
{: #ca-deploy-resources-required}

사용자의 {{site.data.keyword.cloud_notm}} Private 클러스터가 최소 하드웨어 리소스 요구사항을 충족하는지 확인하십시오.

| 컴포넌트 | vCPU | RAM | 데이터 스토리지용 디스크 |
|-----------|------|-----|-----------------------|
|    CA |   1 |192MB |         1GB |

**참고:**
- vCPU는 서버가 가상 머신에 대해 파티션되지 않은 경우 가상 머신 또는 실제 프로세서 코어에 지정되는 가상 코어입니다. {{site.data.keyword.cloud_notm}} Private에서 배치를 위해 가상 프로세서 코어(VPC)를 결정할 때 vCPU 요구사항을 고려해야 합니다. VPC는 IBM 제품의 라이센싱 비용을 판별하는 측정 단위입니다. VPC를 결정하는 시나리오에 대한 자세한 정보는 [가상 프로세서 코어(VPC) ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "가상 프로세서 코어")의 내용을 참조하십시오.
- 데이터 스토리지 요구사항은 저장되는 ID 및 인증서 수에 따라 다릅니다. CA 스토리지는 피어 또는 순서 지정자 스토리지만큼 무겁지 않지만 유스 케이스에 따라 다릅니다. 사용자가 많을수록 더 많은 스토리지가 필요합니다.
- 이 최소 리소스 레벨은 테스트 및 실험을 수행하는 데 충분합니다. 대량의 트랜잭션이 사용되는 환경의 경우 CA에 충분히 큰 크기의 스토리지를 할당하는 것이 중요합니다. 사용할 스토리지의 양은 네트워크에서 필요한 트랜잭션의 수와 서명의 수에 따라 달라집니다. CA의 스토리지를 모두 소모하면 더 큰 파일 시스템으로 새 CA를 배치하고 동일한 채널의 다른 CA를 통해 동기화하도록 해야 합니다.

## 스토리지
{: #ca-deploy-storage}

CA에서 사용할 스토리지를 결정해야 합니다. 기본 설정을 사용하는 경우 Helm 차트에서 CA의 새로운 2Gi PVC(Persistent Volume Claim)를 `fabric-ca-pvc`라는 이름으로 작성합니다.

기본 스토리지 설정을 사용하지 않으려면 {{site.data.keyword.cloud_notm}} Private 설치 중에 *새* `storageClass`가 설정되었는지 확인하거나, CA를 배치하기 전에 Kubernetes 시스템 관리자가 storageClass를 작성해야 합니다.

AMD64 또는 S390X 플랫폼에 CA를 배치하도록 선택할 수 있습니다. 단, [동적 프로비저닝![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)은 {{site.data.keyword.cloud_notm}} Private의 AMD64 노드에서만 사용할 수 있습니다. 클러스터에 S390X와 AMD64 작업자 노드가 혼합되어 있는 경우 동적 프로비저닝을 사용할 수 없습니다.

동적 프로비저닝을 사용하지 않는 경우 [지속적 볼륨 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)을 작성하여 Kubernetes PVC 바인드 프로세스를 세분화하는 데 사용할 수 있는 레이블로 설정해야 합니다.

## CA 배치에 필요한 전제조건
{: #ca-deploy-prerequisites}

1. CA를 {{site.data.keyword.cloud_notm}} Private에 설치하기 전에 [{{site.data.keyword.cloud_notm}} Private을 설치](/docs/services/blockchain/ICP_setup.html#icp-setup)하고 [{{site.data.keyword.blockchainfull_notm}} Platform Helm 차트를 설치](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)해야 합니다.

2. Community Edition을 사용하고 인터넷 연결 없이 {{site.data.keyword.cloud_notm}} Private 클러스터에 이 Helm 차트를 실행하려면 아카이브를 {{site.data.keyword.cloud_notm}} Private 클러스터에 설치하기 전에 인터넷에 연결된 시스템에서 아카이브를 작성해야 합니다. 자세한 정보는 [인터넷 연결 없이 클러스터에 주요 애플리케이션 추가 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "인터넷 연결 없이 클러스터에 주요 애플리케이션 추가"){:new_window}를 참조하십시오. Helm 차트의 ibm-blockchain-platform-dev/ibm_cloud_pak 아래에서 스펙 파일인 manifest.yaml을 찾을 수 있습니다.

3. 클러스터 프록시 IP 주소의 값을 {{site.data.keyword.cloud_notm}} Private 콘솔에서 검색하십시오. **참고:** 프록시 IP에 액세스하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다. {{site.data.keyword.cloud_notm}} Private 클러스터에 로그인하십시오. 왼쪽 탐색 패널에서 **플랫폼**, **노드**를 차례로 클릭하여 클러스터에 정의되어 있는 노드를 표시하십시오. 역할이 `proxy`인 노드를 클릭한 후 테이블에서 `Host IP`의 값을 복사하십시오.

  이 값을 저장하십시오. 이 값은 Helm 차트의 `Proxy IP` 필드를 구성할 때 사용하게 됩니다.
  {:important}

4. CA 관리자 이름 및 비밀번호를 작성하고 {{site.data.keyword.cloud_notm}} Private의 시크릿 오브젝트에 저장하십시오. 시크릿을 작성하는 단계가 [다음 섹션](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret)에 있습니다.

## CA 관리자 시크릿 작성
{: #ca-deploy-admin-secret}

CA가 처음 시작될 때 CA를 운영하기 위해 작성한 관리자 ID가 포함되어 있습니다. CA를 배치하기 전에 이 관리자 ID의 사용자 이름과 비밀번호를 작성해야 합니다. 이 값을 기록하는 것이 중요합니다. 나중에 CA를 운영하고 추가 네트워크 컴포넌트를 배치할 때 사용됩니다. [Kubernetes 시크릿 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/configuration/secret/ "시크릿")을 작성하여 CA 배치에 필요한 관리자 `username` 및 `password`를 저장하십시오. Kubernetes 시크릿을 사용하면 정보를 배치에 직접 전달하지 않고도 정보를 보호하고 공유할 수 있습니다.

1. 관리자 사용자 이름 및 비밀번호를 작성하고 base64 형식으로 값을 인코딩하십시오. 터미널에서 다음 명령을 실행하고 사용하려는 값으로 `admin` 및 `adminpw`의 값을 대체하십시오.
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **중요:** 위에 작성된 `admin` 및 `adminpw`의 값을 저장하십시오. Kubernetes 시크릿 오브젝트를 작성할 때 필요한 이러한 필드의 인코딩된 값도 저장하십시오.

2. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하십시오. 왼쪽 탐색 패널에서 **구성**, **시크릿**을 차례로 클릭하십시오. **시크릿 작성** 단추를 클릭하여 새 시크릿 오브젝트를 작성할 수 있는 팝업 패널을 여십시오.

3. **일반** 탭에서 다음 필드를 완료하십시오.
  - **이름:** 클러스터 내에서 시크릿에 고유한 이름을 지정하십시오. CA를 구성하는 데 이 이름을 사용합니다. 이름은 모든 소문자여야 합니다.
  - **네임스페이스:** 시크릿을 추가할 네임스페이스입니다. CA를 배치하려는 `namespace`를 선택하십시오.
  - **유형:** `Opaque` 값을 입력하십시오.

4. **어노테이션** 탭을 비워 두십시오.

5. **데이터** 탭에서 사용자 이름 및 비밀번호를 키 값 쌍으로 추가하십시오.
  1. 첫 번째 **이름** 필드에 `ca-admin-name`을 입력하십시오.
  2. 첫 번째 **값** 필드에 위 1단계로부터 `echo -n admin | base64`의 결과를 입력하십시오.
  3. **데이터 추가** 단추를 클릭하여 두 번째 키 값 쌍을 추가하십시오.
  4. 두 번째 **이름** 필드에 `ca-admin-password`를 입력하십시오.
  5. 두 번째 **값** 필드에 위 1단계로부터 `echo -n adminpw | base64`의 결과를 입력하십시오.  
    **그림 1**에는 `ca-admin-name` 및 `ca-admin-password` 키에 지정된 값에 따라 {{site.data.keyword.cloud_notm}} Private 콘솔 시크릿에 포함될 수 있는 항목이 표시됩니다.

    ![{{site.data.keyword.cloud_notm}} Private 콘솔 시크릿](../images/CreateCASecret.png "{{site.data.keyword.cloud_notm}} Private 콘솔 시크릿")  
    *그림 1. {{site.data.keyword.cloud_notm}} Private 콘솔 시크릿*
  6. **작성**을 클릭하여 새 시크릿 오브젝트를 작성하십시오.

CA Helm 차트에서는 `ca-admin-name` 및 `ca-admin-password` 이름을 해당 키 값으로 사용하여 Helm 릴리스에 시크릿을 배치해야 합니다.

**참고:** Helm 릴리스를 삭제할 때 CA 관리자 시크릿은 {{site.data.keyword.cloud_notm}} Private 클러스터에서 제거되지 않습니다. 사용자는 {{site.data.keyword.cloud_notm}} Private 클러스터에서 시크릿을 관리해야 합니다. 이후에 다른 CA를 배치하려는 경우 CA 관리자 시크릿을 재사용할 수 있습니다. 그렇지 않으면, 사용자가 {{site.data.keyword.cloud_notm}} Private 클러스터에서 이를 삭제해야 합니다.

## 구성
{: #ca-deploy-configuration}

CA 관리자 시크릿을 작성하면 CA를 구성하고 설치하기 위한 다음 단계를 사용할 수 있습니다. 한 번에 하나의 CA만 설치할 수 있습니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하고 오른쪽 상단에서 **카탈로그** 링크를 클릭하십시오.
2. Community Edition을 다운로드한 경우, 왼쪽 탐색 패널에서 `Blockchain`을 클릭하여 `ibm-blockchain-platform-prod` 또는 `ibm-blockchain-platform-dev`라는 타일을 찾으십시오. 타일을 클릭하여 이를 열면 Helm 차트 설치 및 구성에 대한 정보가 포함된 Readme 파일이 표시됩니다.
3. 패널의 상단에 있는 **구성** 탭을 클릭하거나 오른쪽 하단 구석에 있는 **구성** 단추를 클릭하십시오.
4. [일반 구성 매개변수](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters)의 값을 지정하고 라이센스 계약에 동의하십시오.
5. `All parameters` 트위스티를 열고 [글로벌 구성 매개변수](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters)의 값을 지정하십시오.
6. 아래로 스크롤하여 **CA 구성** 섹션으로 이동하십시오. `Install CA` 선택란을 선택하고 CA에 대한 [구성 설정](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-parameters)을 완료하십시오.  
7. **설치**를 클릭하십시오.

### 구성 매개변수
{: #ca-deploy-configuration-parms}

다음 표에는 {{site.data.keyword.blockchainfull_notm}} Platform, **CA 컴포넌트 관련 항목** 및 해당 기본값의 구성 가능한 매개변수가 나열됩니다.  

**Helm 차트 UI에 더 이상의 구성이 필요 없다고 되어 있지만 CA를 배치하려면 특정 매개변수를 입력해야 합니다.**

#### 일반 및 글로벌 구성 매개변수
{: #ca-deploy-global-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
|**일반 매개변수**| Helm 차트를 구성하는 매개변수입니다.| | |
| `Helm release name`| Helm 릴리스의 이름입니다. 소문자로 시작하고 영숫자 문자로 끝나야 하며 하이픈과 소문자의 영숫자 문자만 포함해야 합니다. 컴포넌트를 설치하려고 할 때마다 고유한 Helm 릴리스 이름을 사용해야 합니다. | 없음 | 예 |
| `Target namespace`| Helm 차트를 설치할 Kubernetes 네임스페이스를 선택합니다. | 없음 | 예 |
| `Target namespace policies`| 선택한 네임스페이스의 팟(Pod) 보안 정책을 표시하며, 여기에는 **`ibm-privileged-psp`** 정책을 포함해야 합니다. 그렇지 않으면 네임스페이스에 [PodSecurityPolicy를 바인드](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp)하십시오.| 없음 | 아니오 |
|**글로벌 구성**| Helm 차트의 모든 컴포넌트에 적용하는 매개변수입니다.| | |
| `Service account name`|팟(Pod)을 실행하는 데 사용할 [서비스 계정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "팟(Pod)의 서비스 계정 구성")의 이름을 입력합니다. | 기본값 | 아니오 |

#### CA 구성 매개변수
{: #ca-deploy-parameters}

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
| `Install CA`| CA를 설치하려면 선택합니다. | 선택 취소 | 예, CA를 배치하려는 경우 |
| `CA name`| 인증 기관에 사용할 이름을 지정합니다. **중요:** 이 값을 기록하십시오. 나중에 순서 지정자 또는 피어를 구성할 때 필요합니다. | SampleOrgCA | 예 |
| `CA worker node architecture`| {{site.data.keyword.cloud_notm}} Private 작업자 노드 아키텍처(ADM64 또는 S390X)를 선택합니다. | AMD64 | 예|
| `CA database type`| CA 데이터를 저장할 데이터베이스 유형입니다. SQLite만 지원됩니다. | SQLite | 예 |
| `CA data persistence enabled` | 선택된 경우 컨테이너를 다시 시작하면 데이터를 사용할 수 있습니다. 그렇지 않으면 장애 복구 또는 팟(Pod) 재시작 시 모든 데이터가 유실됩니다. | 선택됨 | 아니오 |
| `CA use dynamic provisioning` | 스토리지 볼륨에 대한 동적 프로비저닝을 사용으로 설정할 경우 선택합니다. | 선택됨 | 아니오 |
| `CA storage class name`| 고유한 스토리지 클래스 이름을 지정합니다. 그렇지 않으면, 클러스터의 기본 스토리지 클래스가 사용됩니다. | 없음 | {{site.data.keyword.cloud_notm}} Private 클러스터가 구성되는 방법에 따라 다릅니다. 클러스터 관리자에게 확인 |
| `CA existing volume claim`| 기존 볼륨 청구의 이름을 지정하고 다른 모든 필드는 공백으로 두십니다. | 없음 | 아니오 |
| `CA selector label`|PVC의 [선택기 레이블 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 없음 | 아니오 |
| `CA selector value`|PVC의 [선택기 값 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 없음 | 아니오 |
| `CA storage access mode`| PVC의 스토리지 [액세스 모드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "액세스 모드")를 지정하십시오. | ReadWriteMany | 예 |
| `CA volume claim size`| 사용할 디스크 크기를 선택합니다. | 2Gi | 예 |
| `CA image repository`| CA Helm 차트의 위치입니다. | ibmcom/ibp-fabric-ca | 예 |
| `CA Docker image tag`| CA 이미지와 연관된 태그의 값입니다. 이 필드는 이미지 버전으로 자동 완성됩니다. | 1.4.0 | 예 |
| `CA Init Docker image repository`| CA Init Docker 이미지의 위치입니다. 이 필드는 이미지 위치로 자동 완성됩니다. | ibmcom/ibp-init | 예 |
| `CA Init Docker image tag`| CA Init Docker 이미지와 연관된 태그의 값입니다. 이 필드는 이미지 버전으로 자동 완성됩니다. | 1.4.0 | 예 |
| `CA service type` | 피어에서 [외부 포트 노출![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) 여부를 지정하는 데 사용됩니다. 포트를 외부적으로 노출하려면(권장됨) NodePort를 선택하고 포트를 노출하지 않으려면 ClusterIP를 선택하십시오. LoadBalancer와 ExternalName은 이 릴리스에서 지원되지 않습니다 | NodePort | 예 |
| `CA secret(Required)`|  `ca-admin-name` 및 `ca-admin-password`에 대해 작성한 Kubernetes 시크릿 오브젝트의 이름을 입력합니다. | 없음 | 예 |
| `CA CPU request`| CA에 할당할 최소 CPU 수를 지정합니다. |   1 | 예 |
| `CA CPU limit`| CA에 할당할 최대 CPU 수를 지정합니다. |2 | 예 |
| `CA memory request`| CA에 할당할 최소 메모리 크기를 지정합니다. | 1Gi | 예 |
| `CA memory limit`| CA에 할당할 최대 메모리 크기를 지정합니다. | 4Gi | 예 |
| `CA TLS instance name`| 순서 지정자 또는 피어를 등록하는 데 사용될 CA TLS 인스턴스의 이름을 지정합니다. | tlsca | 예 |
| `CSR common name`| 접속될 때 생성된 CA 루트 인증서에서 표시할 CN(Common Name)을 지정합니다. | tlsca-common | 예 |
| `Proxy IP`|CA가 배치된 [클러스터의 프록시 노드 IP ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/install_proxy.html "HTTP 프록시 뒤에 IBM Cloud Private 설치")를 입력합니다. | 127.0.0.1 | 예 |


### Helm 명령행을 사용하여 Helm 릴리스 설치
{: #ca-deploy-helm-cli}

또는 Helm CLI를 사용하여 Helm 릴리스를 설치할 수 있습니다. `helm install` 명령을 실행하기 전에 [Helm CLI 환경에 클러스터의 Helm 저장소를 추가![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Helm CLI에 내부 Helm 저장소 추가")했는지 확인하십시오.

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

예를 들면 다음과 같습니다.

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values ca-s390x-values.yaml \
--tls
```

다운로드된 아카이브 파일에 포함된 `values.yaml`을 편집하여 새 `yaml` 파일을 작성할 수 있습니다. `values.yaml` 파일에는 기본 설정이 포함된 모든 필수 매개변수가 포함되어 있습니다.

## CA 설치 확인
{: #verifiying-ca-installation}

구성 매개변수를 완료하고 **설치** 단추를 클릭한 후 **Helm 릴리스 보기** 단추를 클릭하여 배치를 보십시오. 성공한 경우 배치 테이블의 `DESIRED`, `CURRENT`, `UP TO DATE` 및 `AVAILABLE` 필드에 값 1이 표시됩니다. 새로 고치기를 클릭하고 테이블이 업데이트될 때까지 기다려야 할 수 있습니다. 또한 {{site.data.keyword.cloud_notm}} Private 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하여 배치 테이블도 찾을 수 있습니다. 메뉴 표시줄에서 **워크로드**, **Helm 릴리스**를 차례로 클릭하십시오.

아래로 스크롤하여 `참고` 섹션으로 이동하면 [CA 운영](/docs/services/blockchain/howto/CA_operate.html#ca-operate)에 사용할 중요한 정보를 찾을 수 있습니다.

{{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull_notm}} Platform CA를 설치하면 configmap이 기본 환경 변수 설정으로 작성됩니다. 그런 다음 CA 서버의 환경 변수를 변경하거나 추가하여 작동을 구성할 수 있습니다. CA 서버 구성 매개변수에 대한 자세한 정보는 [Fabric CA 서버 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-server "Fabric CA 서버")를 참조하십시오.

configmap을 구성한 후 변경사항이 적용되기 전에 CA 서버를 다시 시작해야 합니다. CA 서버를 다시 시작하기 위해 Fabric CA 서버 팟(Pod)을 삭제할 수 있습니다. {{site.data.keyword.cloud_notm}} Private은 변경사항을 반영하는 새 팟(Pod)을 작성합니다.

## CA 로그 보기
{: #ca-deploy-view-logs}

컴포넌트 로그는 [`kubectl CLI 명령`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure)을 사용하거나 {{site.data.keyword.cloud_notm}} Private 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다. 자세한 정보는 [로그에 액세스하기 위한 지시사항](/docs/services/blockchain/howto/CA_operate.html#ca-operate-view-logs)을 참조하십시오.

## CA 운영
{: #ca-deploy-operate}

CA는 조직의 신뢰 루트가 됩니다. CA를 사용하여 다른 컴포넌트에 대한 인증서를 생성해야 합니다.  결과적으로 순서 지정자 또는 피어를 배치하기 전에 [CA를 설정하고 여러 작업 단계를 완료해야 합니다](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

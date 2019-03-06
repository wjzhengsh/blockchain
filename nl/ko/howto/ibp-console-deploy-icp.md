---

copyright:
  years: 2019
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

# {{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 배치
{: #ibp-console-deploy-icp}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

다음 지시사항은 {{site.data.keyword.cloud_notm}} Private을 통해 사용자 자신의 인프라에 {{site.data.keyword.blockchainfull}} Platform 콘솔을
배치하는 방법에 대해 설명합니다. 다른 블록체인 컴포넌트와 동일한 환경에 콘솔을 배치할 필요는 없습니다.
{:shortdesc}

Helm 차트를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 {{site.data.keyword.cloud_notm}} Private에
설치할 것입니다. 각 배치는 콘솔의 단일 인스턴스를 {{site.data.keyword.cloud_notm}} Private 네임스페이스에 설치합니다.

## 고려사항
{: #ibp-console-deploy-icp-considerations}

콘솔을 배치하기 전에 다음 고려사항을 이해해야 합니다.

- 블록체인 컴포넌트가 이미 {{site.data.keyword.cloud_notm}} Private 또는 AWS(Amazon Web Services)에
배치되어 있어야 합니다. 아직 배치되지 않은 경우,
[About {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private] 또는
[IBM Blockchain Platform for AWS]에서 자세한 정보를 참조하십시오.
- 사용자가 콘솔의 상태 모니터링, 보안, 로깅 및 리소스 사용량 관리 작업을 수행해야 합니다.
- Hyperledger Fabric v1.2.1 레벨의 블록체인 컴포넌트에만 콘솔을 연결할 수 있습니다.

## 필수 리소스
{: #ibp-console-deploy-icp-resources-required}

사용자의 {{site.data.keyword.cloud_notm}} Private 시스템이 최소 하드웨어 리소스 요구사항을 충족하는지 확인하십시오.

| 컴포넌트 | vCPU | RAM | 데이터 스토리지용 디스크 |
|-----------|------|-----|-----------------------|
| IBP 콘솔 |   1 |192MB | 1GB |
| IBP 콘솔용 CouchDB |   1 | 1GB | 1GB |

 **참고:**
 - vCPU는 서버가 가상 머신에 대해 파티션되지 않은 경우 가상 머신 또는 실제 프로세서 코어에 지정되는 가상 코어입니다. {{site.data.keyword.cloud_notm}} Private에서 배치를 위해 가상 프로세서 코어(VPC)를 결정할 때 vCPU 요구사항을 고려해야 합니다. VPC는 IBM 제품의 라이센싱 비용을 판별하는 측정 단위입니다. VPC를 결정하기 위한 시나리오에 대한 자세한 정보는 [가상 프로세서 코어(VPC) ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html)를 참조하십시오.
 - 이 최소 리소스 레벨은 테스트 및 실험을 수행하는 데 충분합니다. 트랜잭션의 대형 볼륨이
포함된 환경의 경우 스토리지의 충분한 양을 할당하는 것이 중요합니다(예: {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 위해 250GB). 사용할 스토리지의 양은 네트워크에서 필요한 트랜잭션의 수와 서명의 수에 따라 달라집니다. 피어 또는 순서 지정자에서 스토리지를 다 써버리는 경우 대형 파일 시스템에서 새 콘솔을
배치해야 하고 이에 따라 네트워크 컴포넌트를 다시 가져와야 합니다.

## 스토리지
{: #ibp-console-deploy-icp-resources-required-storage}

콘솔에서 사용할 스토리지를 결정해야 합니다. 기본 설정을 사용하는 경우
Helm 차트에서 콘솔 데이터의 새로운 8 Gi 지속적 볼륨 청구(PVC)를
`console-pvc`라는 이름으로 작성하고, CouchDB의 다른 8 Gi PVC를 `couchdb-pvc`라는 이름으로 작성합니다. 

기본 스토리지 설정을 사용하지 않으려면 {{site.data.keyword.cloud_notm}} Private 설치 중에 *새* `storageClass`가 설정되었는지 확인하거나,
Kubernetes 시스템 관리자 배치하기 전에 storageClass를 작성해야 합니다.

AMD64 또는 S390X 플랫폼에 콘솔을 배치하도록 선택할 수 있습니다. 그러나
[동적 프로비저닝](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)은
{{site.data.keyword.cloud_notm}} Private에서 AMD64 노드에만 사용 가능하다는 점에 유의하십시오. 클러스터에 S390X와 AMD64 작업자 노드가 혼합되어 있는 경우 동적 프로비저닝을 사용할 수 없습니다.

동적 프로비저닝을 사용하지 않는 경우 [지속적 볼륨 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)>을 작성하여 Kubernetes PVC 바인드 프로세스를 세분화하는 데 사용할 수 있는 레이블로 설정해야 합니다.

## 전제조건
{: #ibp-console-icp-prereq}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을
{{site.data.keyword.cloud_notm}} Private에 배치하기 전에 [{{site.data.keyword.cloud_notm}} Private 설정](/docs/services/blockchain/ICP_setup.html#icp-setup)의
튜토리얼에서 단계를 완료해야 합니다.

또한 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 배치하기 전에
다음 종속 항목을 설치 또는 구성해야 합니다.

- [IBM App ID 서비스에 등록](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Docker 설치](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### App ID 서비스에 등록
{: #ibp-console-icp-prereq-app-id}

[App ID ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/docs/services/appid/index.html#gettingstarted "App ID")는
{{site.data.keyword.cloud_notm}}에서 제공되는 사용자 관리 서비스입니다. {{site.data.keyword.blockchainfull_notm}} Platform
콘솔은 App ID 서비스를 사용하여 누가 대시보드를 사용하고 블록체인 네트워크를 작동할 수 있는지 관리합니다. 이를
통해 UI의 사용자가 IBM ID의 인증 정보 없이 자체 조직 또는 Google 또는 Facebook과 같은 서드파티에서
발행되는 인증 정보를 사용할 수 있습니다. 콘솔을 배치하는 사용자만이 서비스에 등록할 {{site.data.keyword.IBM_notm}} ID가 필요합니다.

콘솔을 배치하기 전에 App ID Cloud Directory에 일회성 등록을 수행하기 위해 IBM ID를 얻어야 합니다. 그런 다음, APP ID 서비스 인증 정보를 페치하여 콘솔에 전달해야 합니다. 다음
단계에 따라 서비스에 등록하고 서비스 인증 정보를 가져오십시오.

1. 아직 [{{site.data.keyword.IBM_notm}} ID ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid)가 없으면 작성해야 합니다.

2. {{site.data.keyword.IBM_notm}} ID를 사용하여 [{{site.data.keyword.cloud_notm}} ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID")에 로그인하거나 가입하십시오. 그런 다음, 카탈로그 내의 [App ID ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID") 서비스로
이동하십시오. 필요한 서비스에 따라 `Lite` 플랜 또는 `Graduated tier`를 선택하십시오. 그런 다음, **작성**
단추를 클릭하여 "시작" 화면을 실행하십시오.

3. "시작" 화면이 표시되면 왼쪽 탐색 패널의 **관리** 링크를
클릭하여 선호하는 ID 제공업체를 보거나 업데이트하십시오. Google 등의 서드파티 제공업체 또는 사용자 조직이 ID 서비스를 소유합니다.

4. 표에서 **설정** 탭을 클릭하여 URL 경로 재지정을 구성하십시오. 이 URL은 {{site.data.keyword.blockchainfull_notm}}
Platform 콘솔을 전개하고 액세스할 수 있는 URL이 됩니다. 로컬 호스트를 사용하려면 `http://localhost/auth/cb`를 지정하고 `+` 부호를 클릭하십시오.

5. 콘솔을 처음 구성할 때 로그인할 수 있도록 서비스에 이메일 주소를 추가하십시오. 왼쪽 탐색 패널에서 **사용자**
링크를 클릭하고 **사용자 추가** 단추를 클릭하십시오. 이메일 정보를 입력하고 **저장**을 클릭하십시오.

6. 왼쪽 탐색 패널에서 **서비스 인증 정보** 링크를 클릭하십시오. 서비스를 처음 사용하는 경우, **새 인증 정보**를 클릭하여
인증 정보를 생성해야 합니다. **인증 정보 보기**를 클릭하여 서비스에 액세스하는 데 필요한 인증 정보를 복사하십시오. 새 인증 정보를
작성하는 데 필요한 세부사항이 표시되면 UI에 특정 이름을 지정할 경우, `reader` 옵션 및 서비스 ID를
선택하십시오. App ID 인증 정보가 아래와 같이 JSON 값과 유사하게 표시됩니다.
  ![서비스 인증 정보](../images/service_credentials.gif "서비스 인증 정보")

7. 복사 아이콘을 클릭하고 인증 정보를 저장하십시오. 콘솔을 구성할 때 이를 사용합니다.

### Docker 설치
{: #ibp-console-icp-prereq-docker}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 전개할 환경에 [Docker ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.docker.com/get-started) 버전 17.06.2-ce
이상을 설치하십시오. 터미널 창에서 다음 명령을 사용하여 설치한 Docker의 버전을 확인할 수 있습니다.

```
docker --version
```
{:codeblock}

**참고: ** Mac 또는 Windows용 Docker 또는 Docker Toolbox를 설치하면 Docker Compose도 설치됩니다. Docker가 이미
설치되어 있는 경우에는 Docker Compose 버전 1.14.0 이상이 설치되어 있는지 확인해야 합니다. 그렇지 않으면 최신
버전의 Docker를 설치하는 것이 좋습니다. 터미널 창에서 다음 명령을 사용하여 설치한 Docker Compose의 버전을 확인할 수 있습니다.

```
docker-compose --version
```
{:codeblock}


## 1단계: {{site.data.keyword.IBM_notm}} Passport Advantage에서 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 패키지 다운로드
{: #ibp-console-icp-download-icp}

{{site.data.keyword.IBM_notm}} 판매자에게 문의하여
[Passport Advantage ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/software/passportadvantage/pao_customer.html "고객용 Passport Advantage 온라인")에서
{{site.data.keyword.blockchainfull_notm}} Platform 콘솔 패키지를 다운로드하십시오. 아는 IBM 판매자가 없습니까? <link>링크를 방문하여 등록하고 정보를 얻을 수 있습니다.

## 2단계: {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 Helm 차트를 {{site.data.keyword.cloud_notm}} Private으로 가져오기 
{: #ibp-console-icp-import-helmchart}

[{{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull_notm}} Platform 설치](/docs/services/blockchain/howto/helm_install_icp.html#helm-install)를 참조하십시오.

## 3단계: {{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 배치
{: #ibp-console-deploy-helmchart-icp}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔 Helm 차트를 가져오면
다음 단계를 사용하여 콘솔을 구성하고 설치할 수 있습니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인하고 오른쪽 상단에서 **카탈로그** 링크를 클릭하십시오.
2. 왼쪽 탐색 패널에서 `Blockchain`을 클릭하여 `ibm-blockchain-platform-ui`로 레이블된 타일을 찾으십시오. 타일을
클릭하여 이를 열면 Helm 차트 설치 및 구성에 대한 정보가 포함된 Readme 파일이 표시됩니다.
3. 패널의 상단에 있는 **구성** 탭을 클릭하거나 오른쪽 하단 구석에 있는 **구성** 단추를 클릭하십시오.
4. [구성 설정](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters)을 완료하십시오.
5. **설치**를 클릭하십시오.

### {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 매개변수
{: #ibp-console-icp-parameters}

다음 표에는 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔의 구성 가능한 매개변수 및 해당 기본값이 나열되어 있습니다.

|매개변수     |설명    | 기본값  | 필수 |
| --------------|-----------------|-------|------- |
| `Helm release name`| Helm 릴리스의 이름입니다. 소문자로 시작하고 영숫자 문자로 끝나야 하며 하이픈과 소문자의 영숫자 문자만 포함해야 합니다. 콘솔을 설치할 때마다 고유한 Helm 릴리스 이름을 사용해야 합니다. | 없음 | 예 |
| `Target namespace`| Helm 차트를 설치할 Kubernetes 네임스페이스를 선택합니다. | 없음 | 예 |
| `Image repository`| {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 Helm 차트의 위치입니다. | registry.ng.bluemix.net/op-tools/op-tools | 예 |
| `Image tag`| {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 이미지와 연관된 태그의 값입니다. 이 필드는 이미지 버전으로 자동 완성됩니다. 변경하지 마십시오. | v0.0.34 | 예 |
| `Service type` | 피어에서 [외부 포트 노출![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) 여부를 지정하는 데 사용됩니다. 포트를 외부적으로 노출하려면(권장됨) NodePort를 선택하고 포트를 노출하지 않으려면 ClusterIP를 선택하십시오. 이 릴리스에서 LoadBalancer 및 ExternalName은 지원되지 않습니다. | NodePort | 예 |
| `Console ingress` | {{site.data.keyword.blockchainfull_notm}} Platform 콘솔에 액세스하는 데 사용할 호스트 이름을 지정합니다. | 없음 | 아니오 |
| `Configtxlator ingress` | configtxlator 서비스에 액세스하는 데 사용할 호스트 이름을 지정합니다. | 없음 | 아니오 |
| `Persistence enabled` | 선택된 경우 컨테이너를 다시 시작하면 데이터를 사용할 수 있습니다. 그렇지 않으면 장애 복구 또는 팟(Pod) 재시작 시 모든 데이터가 유실됩니다. | 선택됨 | 아니오 |
| `Use dynamic provisioning` | 스토리지 볼륨에 대한 동적 프로비저닝을 사용으로 설정할 경우 선택합니다. | 선택됨 | 아니오 |
| `Storage class name`| 고유한 스토리지 클래스 이름을 지정합니다. 그렇지 않으면, 클러스터의 기본 스토리지 클래스가 사용됩니다. | 없음 | {{site.data.keyword.cloud_notm}} Private 클러스터가 구성되는 방법에 따라 다릅니다. 클러스터 관리자에게 문의하십시오.|
| `Existing volume claim`| 기존 볼륨 청구의 이름을 지정하고 다른 모든 필드는 공백으로 두십니다. | 없음 | 아니오 |
| `Selector label`|PVC의 [선택기 레이블 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 없음 | 아니오 |
| `Selector value`|PVC의 [선택기 값 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "레이블 및 선택기")입니다. | 없음 | 아니오 |
| `Storage access mode`| PVC의 스토리지 [액세스 모드 ](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "액세스 모드")를 지정합니다. | ReadWriteMany | 예 |
| `Volume name`| PVC의 이름을 지정합니다. | 없음 | 예 |
| `CPU request` | 콘솔에 할당할 최소 CPU 수입니다. | 250m | 예 |
| `CPU limit` | 콘솔에 할당할 최대 CPU 수입니다. | 500m | 예 |
| `Memory request` | 콘솔에 할당할 최소 메모리 크기입니다. | 256Mi | 예 |
| `Memory limit` | 콘솔에 할당할 최대 메모리 크기입니다. | 1Gi | 예 |

## 4단계: 프록시 IP 구성
{: #ibp-console-icp-config-proxy-ip}

설치가 완료된 후에 **Helm 릴리스 보기** 단추를 클릭하여 콘솔의 Helm 릴리스 페이지를 여십시오. 또한
{{site.data.keyword.cloud_notm}} Private에서
**메뉴** > **워크로드** > **Helm 릴리스**를
클릭하고 Helm 릴리스를 클릭할 수 있습니다.

콘솔의 Helm 릴리스 페이지에서 {{site.data.keyword.cloud_notm}} Private 클러스터의 프록시 IP(예: `9.12.19.115`)를 찾으십시오.

콘솔에 액세스할 클라이언트 시스템에서 `/etc/hosts`에 항목을 추가하여
optools 및 configtxlator 서비스에 대해 이전 단계에서 입력한 호스트 이름을 프록시 IP에 맵핑하십시오. 예를 들면 다음과 같습니다.
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## 5단계: {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 설정
{: #ibp-console-icp-setup}

*이 절은 gif 애니메이션의 도움을 받을 수 있습니다.*

1. {{site.data.keyword.cloud_notm}}의 왼쪽 탐색에서 **ID 제공업체** > **관리**로
이동하여 **인증 설정** 탭을 선택하십시오.
2. **웹 경로 재지정 URL 추가** 필드에서 App ID를 사용하여 콘솔에 액세스하는 데 사용할 URL을 지정하십시오.
3. 웹 브라우저에서 {{site.data.keyword.cloud_notm}} Private에서 구성한 호스트 이름을 사용하여 콘솔에 액세스하십시오. 첫 번째 로그인의 경우 콘솔 설정 패널이 표시됩니다.
  1. **인증** 탭에서 App ID를 입력하십시오.
  2. **구성** 탭에서 [1단계: App ID 서비스에 등록](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)에서 복사한 App ID 서비스 인증 정보를 붙여넣으십시오.
  3. **사용자 추가** 탭에서 콘솔에 대해 권한 부여된 `Admin` 및 `General` 사용자의 이메일 주소 목록을
지정하십시오. 이메일 도메인은 {{site.data.keyword.cloud_notm}}에서 App ID 서비스가
등록될 때 선택된 기업, Google 또는 Facebook 등의 ID 제공업체 세트로 제한됩니다.
    - `Admin` 역할은 콘솔 권한 목록에 대해 새 사용자를 추가하거나 기존 사용자를 제거하기 위해서 필수입니다. **팁:** 콘솔을
관리하는 사용자의 경우, 목록에 자신의 이메일 주소를 포함시키십시오.
    - `General` 역할은 사용자가 콘솔 내의 컴포넌트에 대한 액세스**만 볼 수 있도록** 허용합니다.

    마지막으로 권한이 없는 사용자, 즉, 위 목록에 없는 사용자가 콘솔에 액세스하려고 시도할 때 연락처 이메일 주소로 표시되는
하나의 `Admin contact email`을 지정해야 합니다. 이 이메일 주소가 콘솔 내의 관리자일 필요는 없습니다. 이 정보가
나중에 콘솔의 **구성원** 탭에서 작성되거나 수정될 수도 있습니다.
  4. **로그인 진행**을 클릭하십시오. 이제 위 목록에서 지정된 모든 사용자가 이메일 주소를 사용하여
{{site.data.keyword.blockchainfull_notm}} Platform 콘솔에 로그인할 수 있습니다.

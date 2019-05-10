---

copyright:
  years: 2019
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


# 콘솔 관리
{: #ibp-console-manage-console}

콘솔 동작을 관리하기 위해 수행할 수 있는 다양한 조치가 있습니다. 이 주제에서는 콘솔의 일상적인 사용에 도움이 되는 블록체인 노드 조작 외부의 조치에 대해 설명합니다.
{:shortdesc}

**대상 독자:** 이 주제는 블록체인 네트워크를 작성, 모니터링 및 관리할 책임이 있는 네트워크 운영자를 위해 설계되었습니다.

## 콘솔에서 사용자 추가 및 제거
{: #ibp-console-manage-console-add-remove}

콘솔 권한 부여 프로세스는 베타 프로그램 중에 변경됩니다. 원래의 {{site.data.keyword.IBM_notm}} ID 인증을 사용한 기존 블록체인 서비스가 있는 경우 더 이상 **사용자** 탭에 나열된 사용자를 볼 수 없습니다. 아래 설명된 IAM 프로세스를 사용하여 사용자에게 콘솔에 대한 액세스 권한을 부여해야 합니다.
{:important}

콘솔에 액세스하는 모든 사용자에게는 {{site.data.keyword.cloud_notm}} Identity and Access Management(IAM) 사용자 역할이 정의된 액세스 정책이 지정되어야 합니다. 정책은 사용자가 콘솔 내에서 수행할 수 있는 조치를 결정합니다. {{site.data.keyword.blockchainfull}} Platform 콘솔은 콘솔 관리자로서 {{site.data.keyword.cloud_notm}} 소유자의 이메일 주소를 사용하여 프로비저닝됩니다. 기본적으로 이 {{site.data.keyword.cloud_notm}} 사용자에게는 IAM의 Blockchain Platform 2.0 서비스에 대한 `Manager` 역할이 부여됩니다. 그런 다음 콘솔 관리자는 IAM UI를 사용하여 다른 사용자에게 콘솔에 대한 액세스 권한을 부여할 수 있습니다. IAM에 대한 자세한 정보는 [IAM 개념 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘") ](/docs/iam?topic=iam-iamoverview#iamoverview "IAM 개념")을 참조하십시오.  

[IAM을 사용하여 사용자 초대![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/iam?topic=iam-iamuserinv#iamuserinv "IAM을 사용하여 사용자 초대")를 수행하는 경우 다음 단계를 완료하여 역할을 구성하고 콘솔에 액세스해야 합니다. 
 1. 메뉴 표시줄에서 **관리** > **액세스(IAM)**를 클릭한 후 **사용자**를 선택하십시오.
 2. **사용자 초대**를 클릭하십시오.
 3. 사용자의 이메일 주소를 입력하십시오. 
 4. `Services` 드롭 다운 목록에서 `Blockchain Platform 2.0`을 선택하십시오.
 5. 아래로 스크롤하여 **역할 선택**으로 이동하십시오. 
 6. `Assign service access roles` 아래에서 `Manager`, `Writer` 및 `Reader`의 사용자 역할을 선택하십시오. 
 7. **사용자 초대**를 클릭하십시오.

| 역할 | 기능 |
|--------|----------|
| 관리자 | 관리자는 작성자 역할 이상의 권한이 있습니다. 독자 및 작성자도 수행할 수 있는 모든 작업을 수행할 수 있습니다. 관리자의 역할은 다음과 같습니다. <ul><li>새 컴포넌트를 추가합니다.</li><li>프로비저닝된 컴포넌트를 삭제합니다.</li><li>콘솔 로깅 레벨을 변경합니다.</li></ul> |
| 작성자 | 작성자는 독자 역할 이상의 권한이 있습니다. 작성자의 역할은 다음과 같습니다. <ul><li>컴포넌트를 가져옵니다.</li><li>가져온 컴포넌트를 제거합니다.</li><li>CA에 사용자를 등록합니다.</li></ul>  |
| 독자 | 독자는 읽기 전용 조치를 수행할 수 있습니다. 독자의 역할은 다음과 같습니다. <ul><li>콘솔 UI를 봅니다. </li><li>콘솔 로그를 봅니다.</li><li>컴포넌트를 내보냅니다.</li></ul> | |

 권한은 누적됩니다. `Manager` 역할을 선택하는 경우 사용자는 모든 `Writer` 및 `Reader`의 조치도 수행할 수 있으며 추가로 이 역할을 확인할 필요가 없습니다. 이와 마찬가지로, `Writer` 역할이 있는 사용자는 `Reader` 역할의 모든 조치를 수행할 수 있습니다. 콘솔 액세스의 경우 `Service Access Roles` 아래의 역할만 선택하고 `Platform Access Roles` 아래에서는 아무 역할도 선택할 필요가 없습니다. 

![Kubernetes 버전 업데이트](../images/AddICPUser.gif)

새 사용자를 콘솔에 추가한 후 사용자는 기타 사용자가 배치하는 모든 노드, 채널 또는 체인코드를 볼 수 없을 수도 있습니다. 이 콘솔 관련 작업을 수행하려면 각 사용자는 연관된 ID를 고유한 콘솔 지갑으로 가져와야 합니다. 자세한 정보는 [콘솔 지갑에 ID 저장](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)을 참조하십시오.
{:important}

사용자의 역할을 수정해야 하는 경우 다음을 수행하십시오.
 1. 메뉴 표시줄에서 **관리** > **액세스(IAM)**를 클릭한 후 **사용자**를 선택하십시오.
 2. 수정할 사용자 옆에 있는 조치 메뉴를 클릭하고 **액세스 권한 지정**을 선택하십시오. 
 3. 타일 **리소스에 대한 액세스 권한 지정**을 선택하십시오. 
 4. `Services` 드롭 다운 목록에서 `Blockchain Platform 2.0`을 선택하십시오.
 5. 아래로 스크롤하여 **역할 선택**으로 이동하십시오. 
 6. `Assign service access roles` 아래에서 `Manager`, `Writer` 및 `Reader`의 사용자 역할을 선택하십시오. 
 7. **지정**을 클릭하십시오.

콘솔에 대한 사용자의 액세스 권한을 제거하려면 [IAM에서 사용자 제거 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/iam?topic=iam-remove#remove "사용자 제거")를 참조하십시오.

### IAM에서 개별 사용자 또는 그룹 사용자에 대한 액세스 역할 지정
{: #ibp-console-manage-console-users-groups}

{{site.data.keyword.cloud_notm}} IAM 정책을 설정하면 개별 사용자 또는 그룹 사용자에게 역할을 지정할 수 있습니다. 

<dl>
<dt>개별 사용자</dt>
<dd>나머지 팀원보다 더 많거나 더 적은 권한이 필요한 특정 사용자가 있을 수 있습니다. 각 사용자가 해당 태스크를 완료하는 데 필요한 권한을 갖을 수 있도록 개별적으로 권한을 사용자 정의할 수 있습니다. 둘 이상의 {{site.data.keyword.Bluemix_notm}} IAM 역할을 각 사용자에게 지정할 수 있습니다. </dd>
<dt>액세스 그룹의 여러 사용자</dt>
<dd>사용자 그룹을 작성한 후 권한을 해당 그룹에 지정할 수 있습니다. 예를 들어, 모든 팀 리더를 그룹화하고 관리자 액세스 권한을 그룹에 지정할 수 있습니다. 그런 다음 모든 개발자를 그룹화하고 쓰기 액세스 권한만 해당 그룹에 지정할 수 있습니다. 둘 이상의 {{site.data.keyword.Bluemix_notm}} IAM 역할을 각 액세스 그룹에 지정할 수 있습니다. 권한을 그룹에 지정하면 그룹에서 추가되거나 제거된 모든 사용자는 영향을 받습니다. 사용자를 그룹에 추가하는 경우 추가 액세스 권한도 갖게 됩니다. 사용자가 제거되면 액세스 권한도 취소됩니다. </dd>
</dl>

IAM에서 추가하는 사용자는 콘솔에 로그인할 수 있는 사용자의 이메일 주소입니다. 조직
탭의 **사용 가능한 조직** 또는 콘솔 지갑에 의해 저장된 ID와는 관계가 없습니다.
{:note}

## 관리자 이메일 주소 업데이트

콘솔이 다중 개인 또는 조직에 의해 사용되는 경우, 네트워크에 액세스하기 위한 기능적 이메일 주소를 작성하는 것을 권장합니다. 기능적 이메일을 사용하면 원래 관리자가 조직을 떠나거나 해당 이메일 주소가 일시중단된 경우에도 콘솔에 액세스할 수 있습니다. 관리자 연락처로 하나의 이메일 주소만 사용할 수 있습니다. 

콘솔이 배치될 때 구성된 콘솔 관리자의 이메일 주소를 업데이트하려면 콘솔 관리자로 로그인해야 합니다. **사용자** 탭으로 이동하여 **{{site.data.keyword.IBM_notm}} ID** 타일에서 **구성**을 클릭하십시오. 열리는 패널에서 콘솔 관리자의 새 이메일 주소를 지정하십시오.

## 로그 보기
{: #ibp-console-manage-logs}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하는 경우 문제를 디버깅하기 위해 로그를 확인해야 할 수도 있습니다.

### 콘솔 로그 보기
{: #ibp-console-manage-console-logs}

콘솔을 사용하거나 노드를 작동할 때 발생하는 문제점을 디버그해야 하는 경우 쉽게 콘솔 로그에 액세스할 수 있습니다. 로깅 레벨을 설정하여 콘솔에서 수집하는 로그의 양을 늘리거나 줄일 수도 있습니다. 콘솔 로그는 {{site.data.keyword.cloud_notm}} Kubernetes Service가 수집하는 [노드 로그](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)와 별도로 수집됩니다.

콘솔 브라우저에서 **설정** 탭으로 이동하여 로깅 설정을 변경하십시오. 콘솔 로그는 두 개의 독립된 소스에서 수집됩니다.

  * **클라이언트 로깅:** 해당 로그는 사용자 브라우저에서 콘솔로 명령이 전송될 때 수집됩니다.
  * **서버 로깅:** 해당 로그는 콘솔이 콘솔 배치에서 노드로 명령을 전송할 때 수집됩니다. 이러한 로그에는 Hyperledger Fabric 로깅 출력이 포함되어 있습니다.

각 로그 유형 아래의 드롭 다운 목록을 사용하여 수집되는 로그의 양을 설정하십시오. 예를 들어,
**오류** 및 **경고**는
가장 적은 양의 로그를 수집하는 반면 **디버그** 및 **모두**는 가장 많은 양의 로그를 수집합니다.

콘솔 관리자로 로그인한 경우에만 콘솔 로그를 볼 수 있습니다. **설정** 탭에서
로그를 보려면 브라우저 URL에서 `settings`를 `api/v1/logs`로 대체하십시오. 예를 들어,
콘솔 URL이 `localhost:3001/settings`인 경우, `localhost:3001/api/v1/logs`로 이동하면
로그를 볼 수 있습니다. 클라이언트 및 서버 로그는 별도의 파일에 수집됩니다. 최신 로그는 페이지 맨 위에 있습니다.

### 노드 로그 보기
{: #ibp-console-manage-console-node-logs}

피어, 순서 지정자 및 인증 기관의 로그는 {{site.data.keyword.IBM_notm}} Kubernetes Service에 의해 수집됩니다. 아래
단계에 따라 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 네트워크를 배치한
클러스터에서 노드의 로그를 볼 수 있습니다.

1. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/resources)를 열고 {{site.data.keyword.IBM_notm}} Kubernetes Service 클러스터의 개요 화면으로 이동하십시오.
2. 위의 클러스터 개요 화면에서 **Kubernetes 대시보드**를 클릭하십시오.
3. Kubernetes 대시보드 내에서 왼쪽 탐색 네임스페이스 드롭 다운 목록을
사용하여 네임스페이스를 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스로
변경하십시오. 서비스 인스턴스 이름은 문자 및 숫자로 구성된 긴 문자열입니다. {{site.data.keyword.blockchainfull_notm}} Platform 콘솔의
URL의 시작 부분에서 서비스 인스턴스 이름을 찾을 수 있습니다.
4. 왼쪽 탐색에서 **팟(Pod)**을 클릭하여 배치한 노드 팟(Pod)의 목록을 보십시오.
5. 팟(Pod)을 클릭하십시오. 그런 다음 맨 위 메뉴에서 **로그 보기**를 클릭하여 노드의 로그를 여십시오. 로그 위에 있는 **로그 위치** 뒤의 드롭 다운 메뉴를 사용하여 팟(Pod) 내의 다양한 컨테이너의 로그를 볼 수 있습니다. 예를 들어, 피어 및 상태 데이터베이스(예를 들어, CouchDB)가
다른 컨테이너에서 실행되어 다른 로그를 생성합니다.

기본적으로 노드의 로그는 클러스터 내에서 로컬로 수집됩니다. 또한 {{site.data.keyword.cloud_notm}} Log Analysis 서비스 또는 서드파티 서비스를 사용하여 네트워크로부터 로그를 수집, 저장 및 분석할 수 있습니다. 자세한 정보는 [{{site.data.keyword.IBM_notm}} Kubernetes Service에 대한 로깅 및 모니터링 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "{{site.data.keyword.IBM_notm}} Kubernetes Service에 대한 로깅 및 모니터링")을 참조하십시오. 로그를 실시간으로 손쉽게 구문 분석해주는 [{{site.data.keyword.cloud_notm}} LogDNA ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-kube#kube "IBM Log Analysis with LogDNA를 사용하여 Kubernetes 클러스터 로그 관리") 서비스를 사용하는 것이 좋습니다.

### 스마트 계약 컨테이너 로그 보기
{: #ibp-console-manage-console-container-logs}

스마트 계약 관련 문제가 발생할 경우 문제를 디버깅하기 위해 스마트 계약 또는 체인코드, 컨테이너 로그를 확인할 수 있습니다.

- Kubernetes 대시보드를 열고 스마트 계약이 실행 중인 피어 팟(Pod)을 클릭하십시오.
- 대시보드에서 `exec` 링크를 클릭하여 팟(Pod)으로 실행하십시오. 이 링크는 기본적으로 피어 컨테이너를 가리킵니다.
- 드롭 다운 목록에서 `dind` 컨테이너를 선택하여 해당 컨테이너로 전환하십시오.
- `docker ps -a` 명령을 실행하여 체인코드 컨테이너 목록을 확인하십시오.
- <chaincode-container-ID>를 체인코드 컨테이너의 ID로 바꿔 `docker logs <chaincode-container-ID>`를 실행하십시오. 

## Kubernetes 클러스터 만기
{: #ibp-console-manage-console-cluster-expiration}

무료 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터를 사용 중인 경우에는 30일 후에 만료됩니다. 만료되면 콘솔에 더 이상 액세스할 수 없습니다. 모든 연관된 노드 및 인증서도 삭제됩니다. 한 번에 하나의 무료 클러스터만 가질 수 있습니다. 그러므로 다른 블록체인 서비스 인스턴스를 작성하기 전에 만료된 클러스터 및 연관된 서비스 인스턴스가 {{site.data.keyword.cloud_notm}}에서 삭제되었는지 확인해야 합니다. 만료된 클러스터 및 연관된 서비스 인스턴스가 이미 삭제되었는지 확인하거나 이 리소스를 수동으로 삭제하려면 다음 단계를 수행하십시오. 

1. {{site.data.keyword.cloud_notm}} 대시보드에서 햄버거 메뉴를 클릭하고 **리소스 목록**을 여십시오.
2. 아래로 스크롤하여 **서비스** 트위스티로 이동하고 이를 펼쳐 서비스 인스턴스를 보십시오.
3. 인스턴스가 나열되면 서비스 인스턴스의 조치 메뉴를 클릭한 후 **삭제**를 클릭하여 이 서비스 인스턴스를 삭제하십시오. 
4. 아래로 스크롤하여 **Kubernetes 클러스터** 트위스티로 이동하고 이를 펼쳐 무료 클러스터를 보십시오.
5. 무료 클러스터가 나열되면 클러스터의 조치 메뉴를 클릭한 후 **삭제**를 클릭하여 무료 클러스터를 삭제하십시오. 

이 조치가 완료되면 [원래의 단계](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)를 수행하여 IBM Cloud 카탈로그 페이지에서 새 Kubernetes 클러스터 및 블록체인 서비스 인스턴스를 작성할 수 있습니다. 

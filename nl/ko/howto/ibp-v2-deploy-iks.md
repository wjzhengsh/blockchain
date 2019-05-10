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

# {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타로 시작하기
{: #ibp-v2-deploy-iks}

{{site.data.keyword.blockchainfull}} Platform 2.0은
{{site.data.keyword.blockchainfull_notm}} Platform 콘솔, 즉, 블록체인 컴포넌트를 배치하고 관리하는 작업을
단순화하고 가속화하는 GUI를 포함하는 베타 릴리스입니다. 이 튜토리얼에서는 {{site.data.keyword.blockchainfull_notm}} Platform 2.0을 사용하여 시작하는 방법 및 콘솔을 사용하여 {{site.data.keyword.cloud_notm}}의 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터에 블록체인 컴포넌트를 배치하고 관리하는 방법에 대해 설명합니다. Kubernetes 및 {{site.data.keyword.cloud_notm}} Kubernetes Service에 대한 자세한 정보는 [Kubernetes](/docs/services/blockchain/reference/k8s.html "Kubernetes")를 참조하십시오.
{:shortdesc}

**대상 독자:** 이 주제는 {{site.data.keyword.cloud_notm}}에서 Kubernetes 클러스터를 설정하고 {{site.data.keyword.blockchainfull_notm}} Platform을 배치할 책임이 있는 시스템 관리자를 위해 설계되었습니다.

{{site.data.keyword.blockchainfull_notm}} Platform을 {{site.data.keyword.cloud_notm}} Kubernetes 클러스터에 링크한 후에 콘솔을 실행하여 블록체인 컴포넌트를 작성하고 관리할 수 있습니다. Kubernetes Service 클러스터를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 2.0을 배치하면 다음과 같은 중요한 이점이 있습니다.

- **제어:** 하나의 중심 콘솔에서 블록체인 컴포넌트 및 인증서를 제어 및 관리할 수 있습니다. 비즈니스에 필요한 컴포넌트만 배치하고 필요에 따라 추가하십시오.
- **유연한 Kubernetes 기반 배치:** Kubernetes 클러스터에 대한 컴퓨팅(CPU, 메모리, 스토리지) 옵션을 활용하고
기본 제공되는 HA 및 DR 옵션을 사용할 수 있습니다.


## 고려사항
{: #ibp-v2-deploy-iks-considerations}

콘솔을 배치하기 전에 다음 고려사항을 이해해야 합니다.

- {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타는 Hyperledger Fabric v1.4와 함께 빌드됩니다.
- 무료 2.0 베타를 사용하여 배치되는 모든 피어는 상태 데이터베이스로 CouchDB를 사용합니다.
- 베타 오퍼링을 평가하기 위해 무료 Kubernetes 클러스터를 사용할 수 있으나 용량 및 성능은 제한적이며 데이터를 마이그레이션할 수 없으며 30일 후에 클러스터가 삭제됩니다.
- 사용자가 Kubernetes 클러스터의 상태 모니터링, 보안 및 로깅 관리를 책임집니다. {{site.data.keyword.cloud_notm}} 관리 및 책임 대상에 대한 자세한 내용은 이 [정보 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "클러스터 관리 책임")를 참조하십시오.
- Kubernetes 대시보드를 사용하여 Kubernetes 클러스터의 자원 사용량을 모니터링할 책임도 있습니다. 스토리지 용량 또는 클러스터의 성능을 개선해야 하는 경우, [기존 볼륨 수정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "기존 스토리지 디바이스의 크기 및 IOPS 변경")방법에 대한 정보를 참조하십시오.
- 인증서, 공개 및 개인 키에 대한 관리 및 보안 책임이 있습니다. IBM은 사용자의 인증서를 Kubernetes 클러스터에 저장하지 않습니다.
- 무료 2.0 베타 오퍼링은 {{site.data.keyword.cloud_notm}} Kubernetes 서비스의 **댈러스** 지역에서만 사용 가능합니다. 이 지역에는 댈러스, 산호세, 휴스턴, 브라질의 데이터 센터가 있습니다. 따라서 모든 블록체인 컴포넌트는 이 4곳의 데이터 센터에 상주할 수 있습니다. 다른 곳에 배치되지 않습니다.
- 베타 오퍼링은 무료이지만, 유료 클러스터를 선택하면 Kubernetes 클러스터에 대한 비용을 지불해야 합니다.
- Kubernetes가 사용자의 {{site.data.keyword.cloud_notm}} Kubernetes 클러스터 내에서 1.11 버전 이상이어야 합니다. 이러한 지시사항에 따라 [신규 및 기존 클러스터를 해당 버전으로 업그레이드](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)하십시오.

## 동영상 튜토리얼
{: #ibp-v2-deploy-video}

{{site.data.keyword.blockchainfull}} Platform 무료 2.0 베타에 대한 자세한 정보를 얻고 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타 배치를 시작하는 방법을 알아보려면 다음 동영상을 시청하십시오.

<iframe class="embed-responsive-item" id="youtubeplayer" title="IBM Blockchain Platform 무료 2.0 베타 - 배치 튜토리얼" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/gPnkVQiHRqk" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

## 시작하기 전에
{: #ibp-v2-deploy-iks-prereq}

시작하기 전에:

- [{{site.data.keyword.cloud_notm}} 유료 계정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/blockchain "카탈로그")이 있는지 확인하십시오. 계정이 없는 경우 다음을 수행하십시오.
   1. **가입** 단추를 클릭하십시오.
   2. 무료 평가판 계정을 작성한 후 {{site.data.keyword.cloud_notm}} 콘솔에서 **관리** > **청구 및 사용량** > **청구**로 이동하고 **신용카드 추가**를 클릭하여 **종량과금제** 유형으로 업그레이드하십시오.
   3. Kubernetes 클러스터에서 사용자의 역할이 관리자(Administrator) 및 관리자(Manager)인지 확인하십시오. 이 역할은 블록체인 서비스 인스턴스에 연결됩니다. 자세한 정보는 [Kubernetes 액세스 역할을 지정하는 방법](#ibp-v2-deploy-iks-k8x-access-roles)의 단계를 참조하십시오. 

광범위한 조직 범위의 솔루션의 컨텍스트에서 서비스 인스턴스를 사용하려는 경우,
참여하는 조직이 기능적 이메일 주소를 사용하여 해당 네트워크를 작성하는 것이 좋습니다. 이 경우, 네트워크에 대한 액세스는 단일 개인의 가용성에 의존하지 않습니다.
{:tip}  

- 기존 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터를 사용하려는 경우,
Kubernetes 버전을 확인하고 필요에 따라 1.11 이상으로 업그레이드하십시오. 클러스터가 실행 중인
Kubernetes 버전을 확인하고 버전을 업그레이드하는 방법에 대한 자세한 정보는
[클러스터의 Kubernetes 버전 업데이트](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)를 참조하십시오.

### 브라우저
{: #ibp-v2-deploy-iks-browsers}
다음 목록은 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔에 대한 최소한의 필수 브라우저 소프트웨어를 지정합니다.

- Chrome: 운영 체제용 최신 버전
- Firefox: 운영 체제용 최신 일반(ESR 외) 버전
- Safari: Mac용 최신 버전
- Edge: v44.17763.1.0 이상

### 필수 리소스
{: #ibp-v2-deploy-iks-resources-required}

#### 무료 클러스터
{: #ibp-v2-deploy-iks-resources-required-free}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터에
배치하려면 Kubernetes 클러스터가 최소한의 하드웨어 리소스 요구사항을 충족하는지 확인하십시오.

|Kubernetes 클러스터 유형 | 유스 케이스 | CPU | RAM | 작업자 노드 |
|-----------|------|-----|-----------------------|
| 표준(권장됨) | MVP에 적합 | 4(공유됨) | 16GB(공유됨)|다중|
|무료 | 평가에 적합 |2 | 4GB |   1 |  

이러한 리소스는 테스트 및 실험을 수행하는 데 충분합니다. 두 개의 피어, 두 개의 CA 및 순서 지정자를 작성하는 [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은 대략 1.1CPU를 사용하고 테스트를 위해 일부 추가 헤드룸이 허용됩니다(예를 들어, 각각 개별적인 원장을 제공하도록 여러 채널을 작성하여). 무료 Kubernetes 클러스터를 사용하는 경우, 클러스터가 30일 간의 평가 이후에 삭제되며 연관된 모든 자산이 제거됩니다. 또한 무료 클러스터에서는 성능이 저하되며 속도가 느립니다.
{:note}

#### 유료 클러스터
{: #ibp-v2-deploy-iks-resources-required-paid}

{{site.data.keyword.blockchainfull_notm}} Platform의 프로덕션 레벨 배치는 {{site.data.keyword.cloud_notm}} Kubernetes Service의 유료 클러스터에 배치됩니다. 이 클러스터의 크기 및 구성은 특정 유스 케이스의 요구사항에 따라 달라집니다. 대형 배치는 반드시 대형 클러스터에 배치되어야 합니다. 클러스터가 예상된 배치보다 얼마나 더 큰지는 사용자에게 달려있습니다. 노드의 크기를 조정하기 **전에** Kubernetes 클러스터에 추가 리소스를 배치하지 않고도 피어 및 순서 지정자는 추가 채널을 결합하고 더 많은 처리량을 처리할 수 있으므로 최소 몇 개의 헤드룸을 보유하고 있는 것이 좋습니다. 이 값을 조정하는 방법에 대한 자세한 정보는 [리소스 재할당](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-reallocate-resources)을 참조하십시오.

확장 가능한 충분한 크기로 초기 배치를 작성하는 것은 [{{site.data.keyword.cloud_notm}} Kubernetes Service Autoscaler ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers?topic=containers-ca#ca "{{site.data.keyword.cloud_notm}} Kubernetes Service Autoscaler")를 사용하지 않도록 선택하며 추가 노드 및 팟(Pod)을 배치하는 작업의 일부에 대한 책임을 질 수 있는 사용자에게 특히 중요합니다. 

{{site.data.keyword.cloud_notm}} Kubernetes Service에 충분한 리소스를 배치하는 것이 더 쉽고 먼저 Kubernetes 클러스터 배치를 늘리지 않고도 원하는 대로 팟(Pod) 및 작업자 노드를 확장할 수 있지만, Kubernetes 클러스터의 배치가 늘어나면 비용도 증가합니다. 사용자는 이 옵션을 신중하게 고려하고 사용자가 선택하는 옵션에 관계 없이 절충안을 알고 있어야 합니다. 

클러스터에 필요한 스토리지 및 컴퓨팅의 양은 차트를 참조하십시오. 여기에는 피어, 순서 지정자 및 CA에 대한 현재 기본값이 포함되어 있습니다: 

| **컴포넌트**(모든 컨테이너) | CPU(millicpus) | CPU(CPU) | 메모리(MB) | 메모리(GB) | 스토리지(GB) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **피어**                       | 1100               | 1.1           | 2200                  | 2.2                   | 200                    |
| **CA**                         | 300                | .3            | 600                   | .6                    | 10                     |
| **순서 지정자**                    | 450                | .45           | 900                   | .9                    | 100                    |



## 1단계: {{site.data.keyword.cloud_notm}}에서 서비스 인스턴스 작성
{: #ibp-v2-deploy-iks-create-service-instance}

다음 단계에 따라 {{site.data.keyword.cloud_notm}}에서 {{site.data.keyword.blockchainfull_notm}} Platform 2.0의 서비스 인스턴스를 작성하십시오.

1. {{site.data.keyword.cloud_notm}} 카탈로그에서 [블록체인 서비스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/blockchain)를 찾거나 {{site.data.keyword.cloud_notm}} 카탈로그 페이지에서 `Blockchain`을 검색하십시오.
2. 필요한 경우, 나중에 쉽게 알아볼 수 있도록 사용자의 인스턴스에 대한 **서비스 이름**을 바꾸십시오.
3. 베타의 경우, **댈러스**가 유일하게 사용 가능한 지역이며 이는 변경할 수 없습니다.
4. 리소스 그룹 및 태그 필드를 변경되지 않은 상태로 둘 수 있습니다.
5. **작성**을 클릭하여 서비스 인스턴스를 프로비저닝하십시오.

## 2단계: {{site.data.keyword.blockchainfull_notm}} Platform 2.0 배치
{: #ibp-v2-deploy-iks-steps}

서비스 인스턴스를 작성한 직후에 지침에 따라 {{site.data.keyword.blockchainfull_notm}} Platform 2.0을 전개할 수 있습니다.

1. **시작 및 필수 소프트웨어** 단계. **댈러스** 지역에 기존 {{site.data.keyword.IBM_notm}} Kubernetes Service 클러스터가 있어서 이 클러스터를 블록체인 서비스에 사용하려면 해당 선택란을 선택하십시오. **기존 클러스터를 사용하는 경우 다음 단계를 건너뛰어도 됩니다. 단, Kubernetes 버전이 v1.11 이상이어야 합니다**. **계속**을 클릭하십시오.
2. **클러스터 작성** 단계. 1단계에서 선택란을 선택하여 기존 Kubernetes 클러스터를 사용하는 경우 이 단계를 건너뜁니다. 그렇지 않으면
**새 클러스터 작성**을
클릭하여 {{site.data.keyword.cloud_notm}} Kubernetes 대시보드를 시작하여 클러스터를 작성하십시오. 자세한 정보는 [{{site.data.keyword.cloud_notm}} Kubernetes Service 시작하기 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers/container_index.html)를 참조하십시오. 이 프로세스가 완료될 때까지 추가 시간이 필요할 수 있습니다.
  - 베타 릴리스의 경우, 선택한 클러스터 유형에 상관없이 Kubernetes 클러스터 위치로 **댈러스**를 선택해야 합니다.
  - **표준 클러스터(권장됨)** 선택: 고가용성을 위한 다중 노드를 포함하는 장기 옵션이 필요한 경우입니다. **Kubernetes 버전 v1.11 이상을 선택해야 합니다.** 유료 클러스터를 배치하려면 [표준 클러스터 작성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers?topic=containers-clusters#clusters_ui_standard "표준 클러스터 작성")을 참조하십시오. 고가용성 또는 재해 복구를 원하는 경우 사용 중인 스토리지 클래스를 결정해야 합니다. 클러스터의 `default` 스토리지 클래스는 동적 프로비저닝에서 사용합니다. 그러므로 고객은 모든 스토리지 클래스를 기본값으로 설정할 수 있습니다. 자세한 정보는 [파일 스토리지 구성에 대한 의사결정 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers?topic=containers-file_storage#file_predefined_storageclass "파일 스토리지 구성에 대한 의사결정")을 참조하십시오.
  - **무료 클러스터** 선택: 30일 미만 동안 클러스터를 사용할 계획인 경우에 해당됩니다. 무료 클러스터에서
유료 클러스터로 마이그레이션할 수 없음에 **유의**하십시오. 무료 유형의 클러스터는
제한된 스토리지 및 트랜잭션 처리량을 제공합니다. Kubernetes 클러스터가 만료될 때 수행할 작업에 대한 지시사항은 [Kubernetes 클러스터 만기](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration)를 참조하십시오.
  - {{site.data.keyword.cloud_notm}}에 대한 무료 및 유료 Kubernetes 클러스터의 차이에 대한 자세한 정보는 [무료 및 표준 클러스터 비교 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "무료 및 표준 클러스터 비교")를 참조하십시오.  

   {{site.data.keyword.blockchainfull_notm}} Platform 2.0 배치 프로세스를 완료할 수 있도록 클러스터를 작성한 후에 브라우저에서 이 탭으로 돌아와야 합니다.  
   {:important}  

  클러스터가 배치될 때까지 대기해야 합니다. 그런 다음 **클러스터가 있음** 단추를 클릭하십시오.
3. 클러스터에서 실행 중인 Kubernetes 버전이 1.11 이상이어야 합니다. 다음 [단계](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)에
따라 클러스터 버전을 확인하고 필요한 경우 업그레이드하십시오. 그런 다음 다시 돌아와 지시사항을 진행하십시오.
4. **클러스터에 배치** 단계. 드롭 다운 목록에서
{{site.data.keyword.blockchainfull_notm}} Platform 2.0을 배치할
Kubernetes 클러스터를 선택하고 **클러스터에 배치**를 클릭하십시오.  

  드롭 다운 목록에 Kubernetes 클러스터가 보이지 않는 경우 다음 조건이 원인일 수 있습니다.
  - 클러스터 작성 프로세스를 완료하는 데 최대 60분이 소요될 수 있습니다. 클러스터를 작성한 경우, 클러스터 상태가 **정상**이 될 때까지 추가 시간이 필요할 수 있습니다.
  - **댈러스** 지역 외부의 클러스터는 보이지 않으며 사용할 수 없습니다.
  - Firefox의 ESR 버전을 사용하고 있지 않은지 확인하십시오. 사용 중인 경우, Chrome과 같은 다른 브라우저로 전환한 후 다시 시도하십시오.

5. **콘솔 실행** 단계. {{site.data.keyword.blockchainfull_notm}} Platform 2.0이
배치된 후 **{{site.data.keyword.blockchainfull_notm}} Platform 실행**을
클릭하여 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 여십시오. 콘솔이 프로비저닝되는 동안 단추가 활성화되는 데 몇 분 정도 걸릴 수 있습니다.

## (선택사항) 콘솔에 추가 사용자 추가
{: #ibp-v2-deploy-iks-add-users}

기본적으로 콘솔은 {{site.data.keyword.cloud_notm}} ID 서비스 제공자로 [{{site.data.keyword.cloud_notm}} Identity and Access Management(IAM) ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/iam?topic=iam-iamoverview#iamoverview "IBM Cloud Identity and Access Management")를 사용합니다. {{site.data.keyword.blockchainfull_notm}} Platform 콘솔은
콘솔의 관리자인 {{site.data.keyword.IBM_notm}} 소유자의 이메일 주소를 구성하여
프로비저닝됩니다. 이 사용자는 관리자로서 다른 사용자에게 이메일 주소를 통해 콘솔에 대한 액세스 권한을 부여할 수 있는 권한이 부여됩니다.  [콘솔에서 사용자 추가 및 제거](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)에 대한 방법에 대한 자세한 정보는 이 지시사항을 참조하십시오. 

## 다음 단계
{: #ibp-v2-deploy-iks-next-steps}

이제 콘솔을 사용할 준비가 되었으므로 [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)로 진행할 수 있습니다.

## 클러스터의 Kubernetes 버전 업데이트
{: #ibp-v2-deploy-iks-updating-kubernetes}

기존 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터를 사용하는 경우,
Kubernetes 버전이 1.11 이상인지 확인하십시오.

{{site.data.keyword.cloud_notm}}에 대한 [Kubernetes 클러스터 페이지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/kubernetes/clusters)에서
클러스터의 Kubernetes 버전을 확인할 수 있습니다. 표에 모든 클러스터가 나열됩니다.

Kubernetes 버전이 1.11 이상이 아닌 경우, 다음 단계를 완료하여 클러스터의 Kubernetes 버전을 업데이트해야 합니다.

1. 행의 끝에 있는 오버플로우 메뉴 아이콘을 클릭하고 **버전 업데이트**를 선택하십시오. 이 프로세스를 완료하는 데 약 한 시간이
소요됩니다. 버전이 업데이트되면 **Kubernetes 버전** 열에서 클러스터의 업데이트된 버전을 볼 수 있습니다.  
2. Kubernetes 버전 드롭 다운 목록에서 Kubernetes 버전 1.11 이상을 선택하고 **업데이트**를 클릭하십시오.
3. 클러스터를 클릭하고 **작업자 노드** 탭으로 이동하십시오. 업데이트할 작업 노드 앞에 있는 선택란을 선택하고
팝업 메뉴 막대에서 **Kubernetes 업데이트**를 클릭하십시오. 클러스터에 여러 개의 작업 노드가 있는 경우 모든 노드를 업데이트해야 합니다.

  작업자 노드를 업데이트하면 앱 및 서비스에 중단 시간이 발생할 수 있습니다. 작업자 노드가 다시 이미지화되고
[팟 외부에 저장 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview)되지 않은 경우 데이터가 삭제됩니다.
  {:important}

![Kubernetes 버전 업데이트](../images/update_k8s_version.gif)

{{site.data.keyword.IBM_notm}} Kubernetes Service 클러스터 및 작업 노드에
대한 Kubernetes 버전 업데이트에 대한 자세한 정보는 [클러스터, 작업자 노드, 및 추가 기능 업데이트 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers?topic=containers-update#update)를 참조하십시오.  

[{{site.data.keyword.blockchainfull_notm}} Platform 2.0 배치를 재개](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps)하려면
업데이트가 완료될 때까지 대기해야 합니다.

## Kubernetes 액세스 역할을 지정하는 방법
{: #ibp-v2-deploy-iks-k8x-access-roles}

Kubernetes에서 블록체인 서비스 인스턴스를 Kubernetes 클러스터에 연결하는 사용자의 역할은 관리자(Administrator) 및 관리자(Manager)여야 합니다.
이 액세스를 구성하려면 다음 단계를 완료해야 합니다. 
   1. {{site.data.keyword.cloud_notm}} 대시보드에서 **관리** 드롭 다운 목록을 클릭한 후 **액세스(IAM)**를 클릭하십시오.
   2. 왼쪽 탐색 메뉴에서 **사용자**를 클릭하고 서비스 인스턴스를 Kubernetes 클러스터에 연결하는 사용자의 ID를 클릭하십시오. 
   3. **액세스 정책**을 클릭한 후 **액세스 권한 지정**을 클릭하십시오.
   4. 타일 **리소스에 대한 액세스 권한 지정**을 클릭하십시오.
   5. **서비스** 드롭 다운 목록에서 **Kubernetes Service**를 선택하십시오.
   6. 이 사용자에게 적합한 **관리자(Administrator)** 및 **관리자(Manager)** 역할을 확인하십시오. 
   7. **지정**을 클릭하십시오.

![Kubernetes 버전 업데이트](../images/k8sAccess.gif)

Kubernetes 액세스 제어에 대한 자세한 정보는 [사용자에게 적합한 올바른 액세스 정책 및 역할을 선택하는 방법](/docs/containers?topic=containers-users#access_roles)을 참조하십시오.

## 서비스 인스턴스 삭제
{: #ibp-v2-deploy-iks-delete-service-instance}

서비스 인스턴스가 더 이상 필요하지 않을 경우 Kubernetes 클러스터에서 해당 인스턴스를 삭제하여 리소스를 해제할 수 있습니다. IBM Cloud 사용자 인터페이스를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타 서비스 인스턴스를 삭제할 수 있습니다.

1. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/ "대시보드")로 이동하십시오.
2. **리소스 요약** 타일을 찾아 **서비스**를 클릭하십시오.
3. **서비스** 트위스티 아래에서 삭제하려는 서비스 인스턴스를 찾고 조치 메뉴에서 **삭제**를 클릭하십시오.

서비스 인스턴스 삭제에 실패한다면 Kubernetes 클러스터에 액세스할 수 없기 때문일 수 있습니다. 이 경우 서비스 인스턴스가 삭제되도록 하려면 [지원 티켓](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases)을 여십시오.

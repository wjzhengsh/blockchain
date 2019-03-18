---

copyright:
  years: 2019
lastupdated: "2019-03-05"

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

이 {{site.data.keyword.blockchainfull}} Platform 콘솔은
콘솔의 관리자인 {{site.data.keyword.IBM_notm}} 소유자의 이메일 주소를 사용하여
프로비저닝됩니다. 그러면 관리자가 다른 사용자가 콘솔에 액세스할 수 있도록 허용할 수 있습니다.

왼쪽 탐색에서 **사용자**를 클릭하여 새 사용자에게 이메일 주소를 통해 콘솔에 액세스할 권한을
부여하십시오. **새 사용자 추가**를 클릭하여
사용자 유형(`Admin` 또는 `General`)과 함께 권한 부여할 이메일 주소 목록을
지정하십시오. `Admin` 유형의 사용자는 새 사용자를 추가하거나 기존 사용자를 제거하고
콘솔 로그를 볼 수 있습니다. `General` 사용자는 콘솔에 로그인할 수 있지만 사용자를 추가하거나 제거하거나 로그를 볼 수는 없습니다.


이 패널에 나열된 사용자는 콘솔에 로그인할 수 있는 사용자의 이메일 주소입니다. 조직
탭의 **사용 가능한 조직** 또는 콘솔 지갑에 의해 저장된 ID와는 관계가 없습니다.
{:note}

## 관리자 이메일 주소 업데이트

콘솔이 다중 개인 또는 조직에 의해 사용되는 경우,
네트워크에 액세스하기 위한 기능적 이메일 주소를 작성하는 것을 권장합니다. 기능적 이메일을 사용하면 원래 관리자가 조직을 떠나거나
해당 이메일 주소가 일시중단된 경우에 콘솔에 액세스할 수 있습니다. 기능적
이메일을 작성할 수 없는 경우 개인에 대한 종속성을 피하기 위해 여러 사용자에게 `Admin` 액세스를 제공할 수 있습니다.

콘솔이 배치될 때 구성된 콘솔 관리자의 이메일 주소를 업데이트하려면 콘솔 관리자로 로그인해야 합니다. **사용자** 탭으로
이동하여 **IBM ID** 타일에서 **구성**을 클릭하십시오. 열리는 패널에서 콘솔 관리자의 새 이메일 주소를 지정하십시오.

## 로그 보기
{: #ibp-console-manage-logs}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하는 동안 문제를 디버깅하기 위해 로그를 확인해야 할 수도 있습니다. 

### 콘솔 로그 보기
{: #ibp-console-manage-console-logs}

콘솔을 사용하거나 노드를 작동할 때 발생하는 문제점을 디버그해야 하는 경우 쉽게 콘솔 로그에 액세스할 수 있습니다. 로깅 레벨을 설정하여 콘솔에서 수집하는 로그의 양을 늘리거나 줄일 수도
있습니다. 콘솔의 로그는 {{site.data.keyword.IBM_notm}} Kubernetes 서비스가 수집하는 [노드의 로그](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)와
별도로 수집됩니다.

콘솔 브라우저에서 **설정** 탭으로 이동하여 로깅 설정을 변경하십시오. 콘솔 로그는 두 개의 독립된 소스에서 수집됩니다.

  * **클라이언트 로깅:** 해당 로그는 사용자 브라우저에서 콘솔로 명령이 전송될 때 수집됩니다.
  * **서버 로깅:** 해당 로그는 콘솔이 콘솔 배치에서 노드로 명령을 전송할 때 수집됩니다. 이러한 로그에는 Hyperledger Fabric 로깅 출력이 포함되어 있습니다.

각 로그 유형 아래의 드롭 다운 목록을 사용하여 수집되는 로그 양을 설정하십시오. 예를 들어,
**오류** 및 **경고**는
가장 적은 양의 로그를 수집하는 반면 **디버그** 및 **모두**는 가장 많은 양의 로그를 수집합니다.

콘솔 관리자로 로그인한 경우에만 콘솔 로그를 볼 수 있습니다. **설정** 탭에서
로그를 보려면 브라우저 URL에서 `settings`를 `api/v1/logs`로 대체하십시오. 예를 들어,
콘솔 URL이 `localhost:3001/settings`인 경우, `localhost:3001/api/v1/logs`로 이동하면
로그를 볼 수 있습니다. 클라이언트 및 서버 로그는 별도의 파일에 수집됩니다. 최신 로그는 페이지 맨 위에 있습니다.

### 노드 로그 보기
{: #ibp-console-manage-console-node-logs}

피어, 순서 지정자 및 인증 기관의 로그는 {{site.data.keyword.IBM_notm}} Kubernetes 서비스에 의해 수집됩니다. 아래
단계에 따라 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 네트워크를 배치한
클러스터에서 노드의 로그를 볼 수 있습니다.

1. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/resources)를
열고 {{site.data.keyword.IBM_notm}} Kubernetes 서비스 클러스터의 개요 화면으로 이동하십시오.
2. 위의 클러스터 개요 화면에서 **Kubernetes 대시보드**를 클릭하십시오. 
3. Kubernetes 대시보드 내에서 왼쪽 탐색 네임스페이스 드롭 다운 목록을
사용하여 네임스페이스를 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스로
변경하십시오. 서비스 인스턴스 이름은 문자 및 숫자로 구성된 긴 문자열입니다. {{site.data.keyword.blockchainfull_notm}} Platform 콘솔의
URL의 시작 부분에서 서비스 인스턴스 이름을 찾을 수 있습니다.
4. 왼쪽 탐색에서 **팟(Pod)**을 클릭하여 배치한 노드 팟(Pod)의 목록을 보십시오.
5. 팟(Pod)을 클릭하십시오. 그런 다음 맨 위 메뉴에서 **로그 보기**를 클릭하여 노드의 로그를 여십시오. 로그 위에 있는
**로그 위치** 뒤의 드롭 다운 메뉴를 사용하여 팟(Pod) 내의 다양한 컨테이너의
로그를 볼 수 있습니다. 예를 들어, 피어 및 상태 데이터베이스(예를 들어, CouchDB)가
다른 컨테이너에서 실행되어 다른 로그를 생성합니다.

기본적으로 노드의 로그는 클러스터 내에서 로컬로 수집됩니다. 또한 {{site.data.keyword.cloud_notm}} Log Analysis 서비스
또는 서드파티 서비스를 사용하여 네트워크로부터 로그를 수집, 저장 및 분석할 수 있습니다. 자세한 정보는 [{{site.data.keyword.IBM_notm}} Kubernetes 서비스에 대한 로깅 및 모니터링 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "{{site.data.keyword.IBM_notm}} Kubernetes 서비스에 대한 로깅 및 모니터링")을 참조하십시오. 로그를 실시간으로 손쉽게 구문 분석해주는 [{{site.data.keyword.cloud_notm}} LogDNA ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis with LogDNA") 서비스를 이용하는 것이 좋습니다. 

### 스마트 계약 컨테이너 로그 보기
{: #ibp-console-manage-console-container-logs}

스마트 계약 관련 문제가 발생할 경우 문제를 디버깅하기 위해 스마트 계약 또는 체인코드, 컨테이너 로그를 확인할 수 있습니다. 

- Kubernetes 대시보드를 열고 스마트 계약이 실행 중인 피어 팟(Pod)을 클릭하십시오. 
- 대시보드에서 `exec` 링크를 클릭하여 팟(Pod)으로 실행하십시오. 이 링크는 기본적으로 피어 컨테이너를 가리킵니다. 
- 드롭 다운 목록에서 `dind` 컨테이너를 선택하여 해당 컨테이너로 전환하십시오. 
- `docker ps -a` 명령을 실행하여 체인코드 컨테이너 목록을 확인하십시오. 
- `docker logs <chaincode-container-ID>`를 실행하십시오. 이때 <chaincode-container-ID>는 체인코드 컨테이너의 ID로 바꾸십시오. 


## 리소스 사용량 모니터링

{{site.data.keyword.cloud_notm}} Platform 노드가 배치될 때
기본 CPU, 메모리 및 스토리지 설정으로 사전 구성되며 이러한 값은 수정할 수 없습니다. {{site.data.keyword.IBM_notm}} Kubernetes 서비스 대시보드를
사용하여 리소스 사용량을 모니터링할 수 있습니다. {{site.data.keyword.cloud_notm}} Platform 콘솔에서
새 노드를 작성하려고 시도하면 Kubernetes 클러스터를 펼쳐야 하는 오류가 발생하며 Kubernetes 클러스터에
더 많은 스토리지를 추가할 수 있습니다. 기존 볼륨의 스토리지 용량 또는 성능을 개선하는 방법에 대해서는 이 [정보 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "기존 스토리지 디바이스의 크기 및 IOPS  변경")를 참조하십시오.

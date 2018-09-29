---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 엔터프라이즈 플랜 정보


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform 엔터프라이즈 플랜은 실제 비즈니스용 블록체인 네트워크를 작성하거나 가입할 조직을 위한 프로덕션 준비가 된 오퍼링입니다. 이 플랜은 보안성이 높고 프로덕션 준비가 된 네트워크를 쉽게 시작하기 위한 도구 및 지원과 함께 핵심 인프라를 제공합니다. 엔터프라이즈 플랜은 2018년 5월 15일에 Hyperledger Fabric V1.0에서 V1.1로 업그레이드되었습니다. 2018년 5월 15일 이후에 작성된 모든 네트워크는 Fabric V1.1 레벨입니다. 그러나 이전에 작성된 네트워크는 Fabric V1.0 레벨로 유지됩니다.
{:shortdesc}

**참고:**
- {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에서는 프로덕션 환경을 제공합니다. 개발 및 테스트 환경이 필요한 경우 [스타터 플랜 정보](starter_plan.html)를 참조하십시오.
- {{site.data.keyword.blockchainfull_notm}} Platform은 {{site.data.keyword.cloud_notm}}의 플랫폼 서비스이며 모든 멤버십 오퍼링은 서비스 레벨 계약(SLA)의 [{{site.data.keyword.cloud_notm}} 서비스 이용 약관 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "{{site.data.keyword.cloud_notm}} 서비스 이용 약관")을 따릅니다. 엔터프라이즈 플랜 네트워크는 지리적으로 구분된 데이터 센터의 **여러 환경** 간에 프로비저닝됩니다.

네트워크를 시작하려는 구성원을 위해 IBM에서는 네트워크를 설정하고 프로비저닝하기 위한 주요 단계를 통해 네트워크 개시자를 안내하는 그래픽 사용자 인터페이스를 제공합니다. 여기에는 다른 구성원 초대 및 통제 규칙 설정이 포함됩니다. 자세한 정보는 [엔터프라이즈 플랜 네트워크 통제](get_start.html)를 참조하십시오. 네트워크가 배치된 후, 대화식 그래픽 사용자 인터페이스인 네트워크 모니터는 네트워크의 상태 및 활동을 모니터하고 새 배치, 구성원 추가 또는 제거, 체인코드 라이프사이클 및 채널 관리를 포함하는 주요 네트워크 활동을 관리하며 기술 지원을 찾을 수 있습니다. 네트워크 모니터에 대한 자세한 정보는 [엔터프라이즈 플랜 네트워크 운영](v10_dashboard.html)을 참조하십시오.

[{{site.data.keyword.blockchainfull_notm}} 멤버십 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)에 지금 가입하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform은 인증 기관(CA) 및 1개 이상의 피어(최대 6개)를 포함하는 주요 Hyperledger Fabric 컴포넌트와 함께 빌드됩니다.  또한 엔터프라이즈 플랜은 네트워크 구성원에게 충돌 결함 허용(CFT) Kafka 순서 지정 서비스를 제공합니다.

Fabric CA는 엔터프라이즈 플랜이 제공되는 인증 기관입니다. 구성원당 두 개의 중간 CA가 제공되며, 이는 네트워크에 멤버십 권한을 부여합니다. 또한 구성원은 CA를 사용하여 네트워크의 사용자에게 멤버십(인증서)을 제공할 수 있습니다.

트랜잭션을 원장에 추가하려면 다음 세 가지 단계가 필요하다는 것을 이해해야 합니다.  
1. 트랜잭션 시뮬레이션 및 보증(피어)
2. 순서 지정(순서 지정 서비스)
3. 유효성 검증 및 커미트(피어)

구성원이 소유한 Fabric 피어는 애플리케이션이 체인코드를 실행하기 위한 게이트웨이 또는 인터페이스이며, 원장에 대한 트랜잭션을 실행하기 위한 비즈니스 로직을 제공합니다.  모든 트랜잭션은 보증을 받아야 합니다. 네트워크의 다른 구성원이 이 보증을 수행합니다. 보증 후에 트랜잭션이 IBM 제공 순서 지정 서비스(Kafka)에 전송됩니다.

코드 블록체인 컴포넌트 외에, 엔터프라이즈 멤버십 옵션은 보안 데이터 스토리지와 통신(TLS) 및 고가용성의 인프라를 제공합니다.  Fabric 네트워크가 이러한 인프라 리소스를 공유하는 동안, 네트워크의 Fabric 컴포넌트 노드에 격리가 제공되며, 각 노드가 실행 환경을 보호하는 보안 Docker 컨테이너에서 실행됩니다.

결정해야 하는 유일한 요소는 네트워크에 필요한 피어의 크기입니다. 이 결정은 필요한 채널의 수와 함께 채널당 워크로드, 메모리 사용량 및 디스크 공간(스토리지)을 기반으로 합니다.

안정된 프로덕션 또는 거의 프로덕션 레벨인 배치를 위해 엔터프라이즈 플랜을 사용해야 합니다. 테스트를 위해서는 [스타터 플랜](starter_plan.html), [IBM Container 서비스에서 개발](https://ibm-blockchain.github.io/) 또는 [로컬로 Docker 이미지 설치](http://hyperledger-fabric.readthedocs.io/en/release-1.1/build_network.html)를 사용하십시오.

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## 가격  
네트워크 구성원은 엔터프라이즈 플랜을 사용하기 위해 멤버십 요금으로 매월 1,000달러와 네트워크의 각 피어에 대해 매월 1,000달러를 지불해야 합니다.  월별 요금은 매일 일할 계산되어 청구됩니다.  예를 들어, 두 피어(피어당 요금 1,000달러 X 2피어)의 구성원(연관된 멤버십 요금 1,000달러)은 매월 3,000달러를 지불해야 합니다.  한 달이 30일인 경우 구성원은 매일 100달러(3,000달러/30)를 지불하는 것입니다.  고가용성(HA)이 필요한 경우 필수 피어 수를 두 배로 늘려 HA 기능을 제공해야 합니다.

네트워크 구성원은 네트워크 인스턴스를 작성하기 위한 영역이 포함된 고유 {{site.data.keyword.cloud_notm}} 계정으로 요금을 지불할 수 있습니다. 또는 하나의 네트워크 구성원이 네트워크에 있는 모든 구성원의 요금을 지불할 수 있습니다. 블록체인 네트워크 비용을 지불하는 방법에 대한 자세한 정보는 [네트워크 비용 지불](howto/paying_mode.html)을 참조하십시오.

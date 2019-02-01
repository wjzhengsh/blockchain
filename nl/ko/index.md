---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**주의:** {{site.data.keyword.blockchainfull}} Platform 오퍼링을 사용하기 전에 [면책사항](/docs/services/blockchain/needtoknow.html) 절에서 기술 및 지원 정보를 읽으십시오.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform(IBP)은 다중 조직 블록체인 네트워크의 전체 라이프사이클을 다루는 유일한 통합 비즈니스 준비 플랫폼입니다. 가장 요구가 많은 유스 케이스와 규제 산업에 대해서도 성능과 보안으로 "비즈니스를 위해 빌드"된 글로벌 블록체인 네트워크의 작성을 각 단계의 협업을 통해 가속화하도록 설계되었습니다. {{site.data.keyword.cloud_notm}}의 {{site.data.keyword.IBM_notm}} 관리 네트워크 오퍼링은 블록체인의 신규 고객에 적합합니다. Hyperledger Fabric 사용 경험이 있는 고객은 {{site.data.keyword.cloud_notm}} Private(ICP) 또는 AWS(Amazon Web Services)를 통해 네트워크 컴포넌트 또는 네트워크를 고유한 인프라에 배치할 수 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform은 모든 유형의 사용자가 블록체인 과정을 시작하고 애플리케이션을 프로덕션으로 이동하는 데 도움이 되는 여러 오퍼링을 제공합니다.

|       |[**스타터 플랜**](#starter-enterprise)      |[ **엔터프라이즈 플랜**](#starter-enterprise)  | [**IBP for ICP**](#ibp-for-icp) | [**IBP for AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **포함된 내용** | **기본 서비스 레벨, 개발 및 테스트 환경** |**고급 서비스 레벨 및 엔터프라이즈 프로덕션 준비 환경** |**배치 가능한 CA, 순서 지정자 및 피어 Helm 차트** | **AWS 빠른 시작 피어**  |
| **청구 정책** | **[클라우드 크레딧 사용 가능](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing) 월별 구독** |**월별 구독** |  **[VPC 가격](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-pricing) 및 무료 커뮤니티 에디션** | **무료** |
| **클라우드 플랫폼**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud Private**| **AWS**|

**주의:** **스타터 플랜**을 프로덕션 용도로 사용하지 마십시오. 이는 개발 및 테스트 환경이며 프로덕션 워크로드에 적합하지 않습니다.

## {{site.data.keyword.blockchainfull_notm}} 플랫폼 기능

{{site.data.keyword.blockchainfull_notm}} 오퍼링은 사용자의 비즈니스 요구사항을 충족하기 위한 엔터프라이즈 레벨의 보안, 데이터 무결성, 확장성 및 성능을 얻기 위해 모듈식 아키텍처를 활용하는 [Hyperledger Fabric](/docs/services/blockchain/reference/v10_fabric.html) 코드 베이스에서 빌드되었습니다.

{{site.data.keyword.blockchainfull_notm}} Platform은 인증된 구성원이 자산을 쉽게 정의할 수 있으며 자산 수정 및 교환을 위한 비즈니스 솔루션을 작성할 수 있는 매우 안전하고 권한 부여된 블록체인 네트워크를 제공합니다.  {{site.data.keyword.blockchainfull_notm}} Platform 오퍼링을 사용하면 **컨소시엄을 활성 블록체인 네트워크로 빠르게 구성**할 수 있는 오케스트레이션 프레임워크를 활용할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform은 여러 기관이 함께 결합해서 통제된 네트워크를 민주적으로 작성하는 과정을 단순화하도록 설계된 익숙한 도구를 제공합니다. 네트워크의 작성, 통제 및 운영 태스크는 기본 제공 대시보드 모니터 및 프로비저닝된 유틸리티를 통해 직관적이고 투명하게 됩니다. 네트워크 작성 및 통제 구현의 번거로운 프로세스에 선행하여, 컨소시엄 구성원은 그 대신 **스마트 계약의 배치 및 자산과 정보의 전송에 초점을 맞출 수 있습니다**.

네트워크의 필수 리소스(피어, 순서 지정자, 인증 기관)에 대한 **고가용성**은 단일 실패 지점에서 발생할 수 있는 부정적인 영향을 제거합니다. 기본 제공 대시보드 모니터를 통해 이러한 리소스를 쉽게 관리할 수 있으며 자산 및 스마트 계약을 시각화하는 강력한 메커니즘을 제공합니다. *스타터 또는 엔터프라이즈 오퍼링에만 지원*됩니다.

Hyperledger Fabric 아키텍처의 **모듈성** 및 네트워크 역할의 뚜렷한 분리는 광범위한 유스 케이스에 대한 적응성 및 확장성을 가능하게 하는 인프라를 제공합니다.*모든 {{site.data.keyword.blockchainfull_notm}} Platform 오퍼링에 지원되고 사용 가능*합니다.

트랜잭션의 라이프사이클 전체에서 이루어지는 확인 및 조정을 통해 일관성 있고 충분히 조사된 결과를 얻을 수 있으며, 가십 프로토콜의 구현을 통해 원장이 지속적으로 동기화됩니다. ID 및 액세스 제어는 네트워크 전체에서 지속적으로 발생하는 **서명/확인** 오퍼레이션을 통해 쉽게 적용되며 *모든 {{site.data.keyword.blockchainfull_notm}} Platform 오퍼링에 지원되고 사용 가능*합니다.

**통제 도구**는 구성원이 자신의 네트워크에 대한 중요 비즈니스 역할을 관리할 수 있도록 허용하기 위해 제공됩니다. 예를 들어, 새 구성원이 가입하기 위해 이를 동의해야 하는 네트워크의 구성원 수를 정의하는 정책을 구현할 수 있습니다. 또는 수정사항을 적용하기 위해 모든 참가자의 보증이 필요한 자산이 있을 수 있습니다. 모든 유형의 비즈니스 네트워크에 대해 통제의 규칙이 근본적으로 필요하며, 종종 매우 자세하게 그에 대해 설명합니다. 통제 도구(예: 정책 편집기)는 이 프로세스를 크게 단순화합니다. *스타터 또는 엔터프라이즈 오퍼링에만 사용 가능*합니다.

엔터프라이즈 플랜은 네트워크 리소스에 대한 외부 액세스(루트 액세스 포함) 없이 **보안 수준이 높고 격리된** 환경에서 실행됩니다. 데이터는 진행 중에 암호화되어 저장되며, 하드웨어 보안 지원 모듈은 디지털 키가 산업 규정에 따라 보호되도록 허용합니다. 하드웨어 가상화는 격리된 환경에서 각 노드를 실행하는 데 사용되며, 따라서 네트워크의 다른 노드를 오작동 가능성이 있거나 악성 체인코드가 있는 피어로부터 보호합니다. 고급 암호화 구현 덕분에 해싱, 서명/확인 오퍼레이션 및 노드 간 통신이 가속화됩니다. *엔터프라이즈에만 사용 가능*합니다.

모든 Hyperledger Fabric 기능에 대한 세부사항은
[Hyperledger Fabric 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/ "Hyperledger Fabric 문서")를 참조하십시오.

**그림 1**은 네 구성원(각각 두 개의 피어를 소유), 암호 ID 자료 분배를 담당하는 인증 기관 및 정책과 네트워크 참가자를 정의하는 순서 지정 서비스로 구성되는 배치된 블록체인 네트워크의 예를 설명합니다. 파란색 채널에는 네트워크 구성원 넷이 모두 포함되어 있고 노란색 채널은 세 구성원(은행 B, C, D)으로만 제한됩니다. 또한, 은행 A는 네트워크 개시자의 역할을 하며 은행 D는 노란색 채널의 컨텍스트에서 구성원으로서만 존재한다는 것을 알 수 있습니다. 마지막으로, 올바르게 서명된 x509 인증서를 소유하고 있는 애플리케이션 또는 사용자는 네트워크에서 피어에 호출을 전송할 수 있습니다. 

![블록체인 네트워크](images/blockchain_network_2-01.png "블록체인 네트워크의 예")

*그림 1. 데이터를 격리하기 위해 채널을 활용하는 구성원 넷이 포함된 블록체인 네트워크의 예*

## 네트워크에 실행되도록 애플리케이션 개발

400개 이상의 클라이언트가 관련되어 연마된 우수 사례를 활용하여 블록체인 개발을 탐색하고 가속화하십시오.

* 블록체인 애플리케이션 개발 시간을 현저하게 줄이는 기술로 비즈니스 전체에서 긴밀한 협력이 가능하도록 합니다(그렇지 않으면 개발에 6주가 소요될 애플리케이션을 2일 미만에 작성할 수 있음).
* {{site.data.keyword.blockchainfull_notm}} 개발자 도구를 활용하여 프로그래머의 기존 작업목록 내에서 블록체인 스킬을 신속하게 빌드합니다.
* 개방되고 현대적인 도구 세트가 있는 선호하는 환경에서 학습하고 개발하기 위한 유연성을 개발자에게 제공합니다.

비즈니스 소유자는 {{site.data.keyword.blockchainfull_notm}} Garage에서 함께 하는 IBM의 많은 산업 및 블록체인 전문가의 도움으로 유스 케이스를 개발하여 {{site.data.keyword.blockchainfull_notm}} Platform을 최대한 이용할 수 있습니다.

개발자는 대화식 플레이그라운드에서 비즈니스 네트워크를 개발하고 반복하고 테스트하여 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크 환경에서 비즈니스 요구사항을 빠르고 쉽게 조정하고 블록체인 애플리케이션 개발을 가속화할 수 있습니다. 이러한 도구는 선호하는 환경에서 비즈니스 규칙을 비즈니스 네트워크 코드로 바꾸도록 설계되었습니다.

* **온라인 탐색**
  [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](/docs/services/blockchain/develop.html)<!--, which is powered by an open source development tool-->을 활용하여 키 블록체인 개념을 학습하고 네트워크 정의를 작성하며 재사용 가능한 산업 모델 및 스마트 계약 라이브러리를 이용하십시오.

    비즈니스 네트워크를 개발한 후 {{site.data.keyword.blockchainfull_notm}} Platform에서 실행되는 라이브 네트워크에 배치할 수 있습니다. 자세한 정보는 [스타터 플랜에서 비즈니스 네트워크 배치](/docs/services/blockchain/develop_starter.html) 및 [엔터프라이즈 플랜에서 비즈니스 네트워크 배치](/docs/services/blockchain/develop_enterprise.html)를 참조하십시오.

* **로컬로 설치**
  Hyperledger Fabric을 활용하여 랩탑에서 직접 개발하고 테스트하십시오. 자세한 정보는 [첫 네트워크 빌드](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html)를 참조하십시오.

* **클라우드 환경에서 협업**
  스타터 플랜 및 엔터프라이즈 플랜 옵션이 있는 사용 준비가 된 라이브 네트워크에서 코드를 개발하고 다른 사용자와 공유하십시오. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html) 및 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html)를 참조하십시오.

## {{site.data.keyword.IBM_notm}} 지원

{{site.data.keyword.IBM_notm}}은 {{site.data.keyword.IBM_notm}} 구현 블록체인 솔루션에서 여러 지원 옵션을 제공합니다. {{site.data.keyword.blockchainfull_notm}} 지원에 대한 자세한 정보는 [지원 받기](/docs/services/blockchain/ibmblockchain_support.html)를 참조하십시오.


## 스타터 플랜 또는 엔터프라이즈 플랜
{: #starter-enterprise}

스타터 및 엔터프라이즈 플랜을 사용하여 프로덕션 준비 보안 서비스로 분산된 블록체인 네트워크를 배치하고 운영하십시오. **스타터 플랜**은 블록체인 네트워크 개발을 학습하거나 시작하기 위한 환경이고 **엔터프라이즈 플랜**은 상위 레벨의 보안 및 지원을 제공하는 프로덕션 환경입니다. 작게 시작해서 다음 기능을 활용하여 멤버십 및 트랜잭션 볼륨 증가에 따라 네트워크를 탄력적으로 확장하십시오.

* 많은 하드웨어, 펌웨어 및 소프트웨어 보안 기능이 있는 초고도의 보안 환경.
* 확장성, 복원성 및 가용성을 위해 강화된 아키텍처.
* 성능을 위해 최적화되어 있으며 세계에서 가장 빠른 Linux 컴퓨팅에서 실행.

{{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크 운영하기에는 관리 태스크를 단순화하기 위한 도구 및 기능이 포함됩니다.

* 네트워크에서 리소스 관리 및 모니터링용 대시보드.
* 전체 코드 스택의 완벽한 업그레이드.
* 포털에 통합된 24/7 기술 지원.
* 권한 부여 없는 액세스, 악성코드 및 변조 저항, 100% 암호화 및 규제 산업의 민감한 데이터가 있는 네트워크에 대한 더 많은 기능으로 강화된 보안 스택.

스타터 플랜 및 엔터프라이즈 플랜은 {{site.data.keyword.cloud_notm}}의 플랫폼 서비스이며 두 플랜은 서비스 레벨 계약(SLA)의 [{{site.data.keyword.cloud_notm}} 서비스 이용 약관![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} 서비스 이용 약관")을 준수합니다. 스타터 및 엔터프라이즈 플랜 네트워크는 모두 지리적으로 구분된 데이터 센터의 **여러 환경** 간에 프로비저닝됩니다.

스타터 플랜 및 엔터프라이즈 플랜에 대한 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html) 및 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html)를 참조하십시오. 시작할 준비가 된 경우 지금 {{site.data.keyword.cloud_notm}}의 [IBP 스타터 또는 엔터프라이즈 멤버십 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)에 가입하십시오! 

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-for-icp}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP는 ICP를 통해 고유한 인프라에서 블록체인 네트워크를 실행해야 하는 컴포넌트를 제공합니다. 컴포넌트에는 Kubernetes Helm 차트를 사용하여 배치하고, 관리하고, 설정하는 Hyperledger Fabric, Certificate Authority (CA), 순서 지정자 및 피어가 포함됩니다. **이 오퍼링은 고급 Hyperledger Fabric 경험이 있는 사용자를 대상으로 합니다.**

IBP for ICP를 통해 블록체인 네트워크를 프라이빗 클라우드에 배치하여 데이터 중복성 요구사항, 시장 규제 및 인프라 환경 설정을 다룰 수 있습니다. 온프레미스의 컨테이너형 애플리케이션을 개발하고 관리하기 위한 Kubernetes 기반 애플리케이션 플랫폼인 {{site.data.keyword.cloud_notm}} Private를 통해 고유한 인프라에서 블록체인 네트워크의 필수 요소의 개발을 단순화합니다. 

 * 클라이언트는 고유한 인프라에서 IBP 네트워크를 관리할 수 있습니다. 무료 커뮤니티 에디션을 통해 고객은 고유한 격리된 보안 환경에서 실행할 수 있으나 지원은 제공되지 않습니다. 
 * 클라이언트는 오퍼레이션에 맞는 Helm 차트 및 세부사항 문서를 사용하여 Kubernetes에서 Fabric을 구성할 수 있습니다. 
 * 커뮤니티 에디션을 사용하지 않는 한 고급 기술 지원으로 클라이언트에게 권한을 부여할 수 있습니다. 

 IBP for ICP에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 정보](/docs/services/blockchain/ibp-for-icp-about.html)를 참조하십시오.

## {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #ibp-for-aws}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 사용하여 피어는 {{site.data.keyword.cloud_notm}}에서 기존 스타터 또는 엔터프라이즈 플랜 네트워크의 연결 프로파일, Hyperledger Fabric 인증 기관(CA) 및 순서 지정 서비스를 활용할 수 있습니다. 빠른 시작을 통해 AWS CloudFormation 템플리트를 사용하여 피어를 배치할 수 있습니다. 이 템플리트는 스타터 또는 엔터프라이즈 플랜 네트워크에 연결된 AWS 호스트 IBP 피어를 빠르게 구성하고, 배치하고, 실행하려는 IT 인프라 의사결정자 및 시스템 관리자를 대상으로 합니다. 템플리트를 사용하여 AWS에서 새 가상 프라이빗 클라우드(VPC)를 빌드하거나 피어를 기존 VPC에 배치할 수 있습니다. 

빠른 시작으로 다음 구성이 완료됩니다. 
 * 두 개의 가용성 구역을 포함하는 고가용성 아키텍처
 * AWS 우수 사례에 따라 공인 서브넷으로 구성된 VPC. 이는 AWS에서 공유한 가상 네트워크를 제공합니다. 
 * 인터넷에 액세스를 허용하기 위한 인터넷 게이트웨이
 * 공인 서브넷에서 두 개의 가용성 구역(각 서브넷당 하나)에 걸쳐 있는 두 개의 피어
 * 각 공인 서브넷에서 임베디드 LevelDB 데이터베이스 또는 보조 CouchDB 컨테이너가 포함된 피어 컨테이너

IBP for AWS에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services 정보](/docs/services/blockchain/howto/remote_peer.html)를 참조하십시오.

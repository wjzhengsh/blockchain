---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 스타터 플랜 정보
{: #overview}

{{site.data.keyword.blockchainfull}} Platform 스타터 플랜은 조직이 다중 조직 블록체인 네트워크를 시뮬레이션하고 신속하게 애플리케이션을 개발하며 샘플 스마트 계약 및 비즈니스 네트워크에 대한 작업을 수행할 수 있도록 하는 엔트리 레벨 옵션입니다. 또한 다른 멤버십 옵션과 동일한 UI 경험을 제공하여 학습 과정을 줄여줍니다. 스타터 플랜 네트워크는 Hyperledger Fabric V1.1을 기반으로 빌드됩니다.
{:shortdesc}

**참고**:
1. {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜은 개발 및 테스트 환경이며 프로덕션 워크로드에는 적합하지 않습니다. 프로덕션 환경이 필요한 경우 [엔터프라이즈 플랜 정보](enterprise_plan.html)를 참조하십시오. 스타터 플랜을 사용하기 전에 [스타터 플랜 고려사항](#considerations)을 확인할 수 있습니다.
2. 스타터 플랜은 2018년 6월 14일에 베타에서 GA로 변경됩니다. 베타 네트워크를 사용하는 경우 [마이그레이션 고려사항](#beta-to-ga)을 읽을 수 있습니다.

[{{site.data.keyword.blockchainfull_notm}} 멤버십 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)에 가입하고 [평가판 크레딧](howto/pricing.html#starter-plan-pricing)으로 스타터 플랜을 사용해보십시오. <!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## 스타터 플랜이 제공하는 내용

- **_원클릭 준비 네트워크_**  
    스타터 플랜은 한 번의 클릭으로 라이브 블록체인 네트워크를 프로비저닝합니다. 각 네트워크에는 순서 지정 서비스, CA, 두 개의 조직(조직마다 하나의 피어 포함) 및 체인코드를 트랜잭션하고 배치할 기본 채널이 포함됩니다. {{site.data.keyword.blockchainfull_notm}} Platform은 이 네트워크의 작성 및 구성을 처리하며(가동 준비가 되면 업데이트할 수 있음) 개발에 초점을 맞추도록 합니다. 스타터 플랜 네트워크는 Hyperledger Fabric V1.1에서 빌드되며 CouchDB를 원장 데이터베이스로 사용합니다. <!--The free trial provides you up to two organizations and two peers.-->
- **_비용 효율성_**  
    스타터 플랜 멤버십 옵션은 엔터프라이즈 플랜 멤버십 옵션과 동일한 여러 블록체인 기능을 적은 비용으로 제공합니다. 스타터 플랜 평가판 기간 중에 블록체인 네트워크에 기본 네트워크 리소스를 무료로 프로비저닝할 수 있습니다.
- **_다중 조직 네트워크 시뮬레이션_**  
    스타터 플랜을 사용하여 다중 조직의 네트워크 빌드를 시뮬레이션할 수 있습니다. 실제로 네트워크에 다른 조직을 초대할 필요는 없지만 다른 조직의 역할을 할 수는 있습니다. 이 메커니즘을 통해 새 조직이 네트워크에 가입하는 방법, 여러 조직이 네트워크에서 함께 작업하는 방법 등을 학습할 수 있습니다. 네트워크 모니터에서 조직 간에 전환하여 여러 조직의 보기를 통해 네트워크를 보고 관리할 수 있습니다.
    **참고**: 두 개의 조직과 두 개의 피어의 무료 평가판 기본 리소스보다 많은 리소스를 추가하는 경우 추가 비용이 발생할 수 있습니다.
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop 애플리케이션 통합_**  
    스타터 플랜을 사용하면 [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login)에서 개발한 비즈니스 네트워크를 배치할 수 있습니다. 자세한 정보는 [IBP: Develop 애플리케이션 가져오기](link)를 참조하십시오.

## 대상

다음 상황 중 하나인 경우 스타터 플랜이 블록체인 과정을 시작하는 데 적합합니다.
- **_{{site.data.keyword.blockchainfull_notm}} Platform에 대해 학습합니다._**  
    {{site.data.keyword.blockchainfull_notm}} Platform에 관심이 있는 경우 또는 심지어 블록체인을 처음 사용하는 경우에도 스타터 플랜은 실제 블록체인 네트워크를 개발하고 통제하는 방법을 학습할 수 있는 좋은 방법을 제공합니다. 프로덕션 네트워크의 경우와 같이 네트워크를 구성하는 컴포넌트를 찾고 멤버십을 관리하고 체인코드("스마트 계약"이라고도 함) 배치 및 관리 방법과 채널을 추가하는 방법(및 채널 권한을 관리하는 방법)을 학습하며, 새 블록이 생성되었을 때 추적할 수 있습니다.
- **_라이브 네트워크에서 데모 솔루션을 빌드합니다._**  
    스타터 플랜은 블록체인 애플리케이션 시연을 위한 강력한 환경을 제공합니다. 사용 준비가 된 블록체인 네트워크를 사용하면 네트워크 모니터가 제공하는 운영 및 관리 도구를 통해 동료, 관리 및 파트너에게 빠른 프리젠테이션을 수행할 수 있습니다.
- **_단일 조직 개발 이상을 수행합니다._**  
    스타터 플랜에서는 다중 조직의 역할을 할 수 있으며, 이를 통해 IBM Blockchain Platform이 애플리케이션 테스트 및 트랜잭션 호출뿐만 아니라 채널 작성과 체인코드 설치와 같은 협업 태스크를 관리하는 방법을 확인할 수 있습니다. 또한 스타터 플랜 네트워크에서 협업할 다른 사용자를 초대할 수도 있습니다(엔터프라이즈 플랜의 경우와 같이). 이를 통해 체인코드와 트랜잭션을 보증하는 다중 파티가 있는 보다 실제적인 환경을 빌드할 수 있습니다.
- **_블록체인 애플리케이션을 반복적으로 개발하고 테스트합니다._**  
    스타터 플랜은 블록체인 네트워크에서 코드를 지속적으로 개발하고 테스트할 수 있는 스테이징 영역을 제공합니다. 클라우드로 이동하면 개발자 및 테스터가 기능에 초점을 맞춰 단위 테스트에서 기능 테스트로 쉽게 이동할 수 있습니다. 스타터 플랜은 운영 및 관리 도구 뿐만 아니라 엔터프라이즈 플랜과 동일한 블록체인 네트워크 기능을 제공합니다. 사용자 프로젝트를 엔터프라이즈 플랜 중 하나로 사용할 준비가 되면 스타터 플랜과 동일한 방식으로 운영되지만 네트워크를 확장할 수 있는 가능성이 높아집니다.


## 스타터 플랜 고려사항
{: #considerations}

스타터 플랜은 {{site.data.keyword.blockchainfull_notm}} Platform의 시작점이며 개발 및 테스트용으로 사용됩니다.  스타터 플랜을 사용하기 전에 다음 항목을 확인하십시오.

- **{{site.data.keyword.cloud_notm}} 계정 요구사항**  	
    유료 {{site.data.keyword.cloud_notm}} 계정(예: **종량과금제** 유형)이 있어야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 제공하는 모든 멤버십 플랜에는 {{site.data.keyword.cloud_notm}} 유료 계정이 필요합니다. 계정을 종량과금제 유형으로 업그레이드하려면 {{site.data.keyword.cloud_notm}} 콘솔에서 **관리** > **청구 및 사용량** > **청구**로 이동하고 **신용카드 추가**를 클릭하십시오.  
- **엔터프라이즈 플랜과의 차이점**
    - 각 조직에 하나의 CA만 있고 네트워크에는 하나의 [순서 지정자](glossary.html#orderer)만 있으므로 [CA](glossary.html#ca) 및 [순서 지정 서비스](glossary.html#orderer)에서 결함 허용이 되지 않습니다.
    - 순서 지정 서비스는 [SOLO](glossary.html#SOLO) [합의](glossary.html#consensus)만 사용합니다. 스타터 플랜 네트워크는 모든 피어에 대한 합의를 수행하는 하나의 [순서 지정자](glossary.html#orderer)로만 구성됩니다.
    - [하드웨어 보안 모듈(HSM)](glossary.html#hsm)은 강력한 인증 및 암호 처리를 위한 디지털 키를 보호하고 관리할 수 없습니다.
- **네트워크 리소스 제한사항**  
    스타터 플랜은 각 피어에 1개의 CPU 및 2Gi RAM을 지정하고 각 {{site.data.keyword.cloud_notm}} 서비스 인스턴스에 20Gi 스토리지를 지정합니다. 
- **비활성 네트워크 삭제**  
    스타터 플랜 네트워크를 작성한 후 15일 동안 네트워크에 방문하지 않거나 이에 대한 트랜잭션을 발행하지 않으면 {{site.data.keyword.blockchainfull_notm}} Platform이 네트워크를 삭제합니다.
    - 네트워크가 더 이상 필요하지 않은 경우 어떠한 조치를 취할 필요가 없으며 네트워크가 자동으로 삭제됩니다.
    - 네트워크를 계속 사용해야 하는 경우 해당 네트워크에 대한 트랜잭션을 발행하여 활성 상태로 유지할 수 있습니다. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps/){:new_window}에서 스타터 플랜 네트워크를 찾을 수 있습니다. 발행할 일반 트랜잭션이 없으면 [지시사항](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan)에 따라 샘플 애플리케이션을 사용해 보십시오.
- **유지보수 및 업그레이드**  
    스타터 플랜 유지보수 및 네트워크 업데이트는 고정된 스케줄에 따라 수행됩니다. 유지보수 기간 중에는 새 네트워크를 프로비저닝할 수 없고 짧은 시간 동안 네트워크 인터럽션이 발생할 수 있습니다.
- **데이터 보존**  
    스타터 플랜은 베타에서 GA로 변경을 포함해 릴리스 업그레이드를 통한 데이터 보존을 보장하지 않습니다. 
- **마이그레이션 고려사항**
    - 스타터 플랜 네트워크에서 다른 멤버십 플랜으로의 데이터 마이그레이션은 현재 지원되지 않습니다. 하지만 스타터 플랜에서 테스트한 `.bna` 파일, 체인코드 및 애플리케이션은 마이그레이션할 수 있습니다. 자세한 정보는 [스타터 플랜에서 엔터프라이즈 플랜으로 마이그레이션](get_start_starter_plan.html#migrate)을 참조하십시오.

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## 베타에서 GA로 마이그레이션
{: #beta-to-ga}

스타터 플랜은 2018년 6월 14일에 GA 스테이지로 이동합니다. GA에서 IBM Blockchain Platform은 스타터 플랜으로 블록체인 네트워크를 작성할 수 있도록 각 IBM Cloud 계정에 $500의 평가판 크레딧을 제공합니다. 평가판 크레딧에 대한 자세한 정보는 다음을 참조하십시오. 스타터 플랜은 2018년 6월 14일에 GA 스테이지로 이동합니다. GA에서 IBM Blockchain Platform은 스타터 플랜으로 블록체인 네트워크를 작성할 수 있도록 각 IBM Cloud 계정에 $500의 평가판 크레딧을 제공합니다. 평가판 크레딧에 대한 자세한 정보는 [스타터 플랜 가격 책정](howto/pricing.html#starter-plan-pricing)의 *스타터 플랜 평가판* 절을 참조하십시오. 유료 IBM Cloud 계정(예: **종량과금제** 유형)이 있어야 합니다. 유료 IBM Cloud 계정(예: **종량과금제** 유형)이 있어야 합니다

스타터 플랜 GA가 된 지 **30일** 후에 삭제될 때까지 스타터 플랜 베타를 사용하여 작성된 블록체인 네트워크는 **무료**입니다. 스타터 플랜 베타 네트워크에서 GA 네트워크로의 데이터 마이그레이션은 지원되지 않습니다. **베타 네트워크의 데이터는 유실됩니다.**  하지만 체인코드, 비즈니스 네트워크 및 애플리케이션을 수동으로 마이그레이션할 수 있습니다.
- 베타 네트워크에서 체인코드를 실행하는 경우 GA 네트워크에서 체인코드를 설치하고 인스턴스화하십시오. 자세한 정보는 [체인코드 설치, 인스턴스화 및 업데이트](howto/install_instantiate_chaincode.html)를 참조하십시오.
- 베타 네트워크에 비즈니스 네트워크를 배치한 경우 GA 네트워크에 `.bna` 파일로 비즈니스 네트워크를 배치하십시오. 자세한 정보는 [스타터 플랜에 비즈니스 네트워크 배치](develop_starter.html)를 참조하십시오.
- 베타 네트워크에서 자체 개발 애플리케이션을 실행한 경우 GA 네트워크 노드를 가리키도록 애플리케이션의 API 엔드포인트를 업데이트하십시오. 자세한 정보는 [애플리케이션에 네트워크 API 엔드포인트 추가](v10_application.html#adding-network-api-endpoints-to-your-application)를 참조하십시오.

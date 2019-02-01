---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 스타터 플랜 시작하기
{: #getting-started-with-starter-plan}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform 스타터 플랜은 한 번의 클릭으로 사전 구성된 블록체인 네트워크를 제공합니다. 스타터 플랜은 기본적으로 두 개의 [조직](/docs/services/blockchain/glossary.html#organization), 각각의 조직당 하나의 [피어](/docs/services/blockchain/glossary.html#peer) 및 하나의 [채널](/docs/services/blockchain/glossary.html#channel)이 구성되어 있는 권한 부여된 네트워크를 프로비저닝합니다. 네트워크가 작성된 후에는 조직 및 피어를 스케일링하고 네트워크에 추가할 수 있습니다. 이러한 네트워크는 {{site.data.keyword.blockchainfull_notm}} Platform을 처음 사용하는 초보 사용자용입니다. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

스타터 플랜을 사용하면 {{site.data.keyword.blockchainfull_notm}} Platform에 대한 기술을 학습하고 개발하며 샘플 애플리케이션을 실행하고 고유 애플리케이션을 테스트하고 다중 조직 시나리오를 시뮬레이션할 수 있습니다. 이 시작하기 튜토리얼은 스타터 플랜을 사용하여 블록체인 네트워크에서 개발 및 트랜잭션을 시작하는 방법을 안내합니다.

{{site.data.keyword.blockchainfull_notm}} Platform 및 블록체인을 처음 사용하는 경우 오픈 소스인 [Hyperledger Fabric](/docs/services/blockchain/reference/v10_fabric.html)에 빌드된 네트워크의 [기본 컴포넌트 개요](/docs/services/blockchain/blockchain_component_overview.html)를 검토하여 블록체인에 대한 자세한 정보를 확인할 수 있습니다. 또한 [Hyperledger Fabric 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/blockchain.html "블록체인 소개")를 검토할 수 있습니다.

**참고**: {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜은 개발 및 테스트 환경이며 프로덕션 워크로드에는 적합하지 않습니다. 프로덕션 환경이 필요한 경우 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html)를 참조하십시오.

## 개요

**컨소시엄 빌드**

블록체인을 사용하여 빌드하는 첫 번째 단계는 블록체인을 사용하여 트랜잭션을 수행할 조직의 컨소시엄을 구성한 후 네트워크에 초대하는 것입니다. 이 기술을 실험하는 중인 스타터 플랜 사용자는 새 조직 자체를 작성하여 다중 조직 컨소시엄을 시뮬레이션할 수 있습니다.

- [스타터 플랜 네트워크 작성](#creating-a-network)
- [조직을 네트워크에 초대](#inviting-members)
- [스타터 플랜 네트워크에 가입](#joining-a-network)

네트워크에 가입한 조직의 컨소시엄 내에서 채널을 작성하여 개인적으로 해당 조직 세트와 관련된 트랜잭션을 수행할 수 있습니다.

- [채널 작성](#create-channels)

**네트워크 및 애플리케이션 개발**

컨소시엄을 구성한 후에는 네트워크의 비즈니스 로직이 포함되고 블록체인 원장의 데이터와 상호작용할 수 있도록 해주는 체인코드(스마트 계약이라고도 함)를 작성해야 합니다. 그런 다음 해당 스마트 계약과 함께 Fabric SDK를 사용하여 클라이언트 측 애플리케이션에서 네트워크로 트랜잭션을 제출해야 합니다.

- [체인코드 개발](#develop-chaincode)
- [Fabric SDK를 사용하여 애플리케이션 개발](#develop-apps)

Hyperledger Fabric을 위한 오픈 소스 도구 세트인 Hyperledger Composer를 사용하여 비즈니스 로직을 개발하고, 네트워크에 로직을 배치하고, 애플리케이션에서 해당 로직에 연결할 수 있습니다.

- [Hyperledger Composer를 사용하여 사용자 정의 비즈니스 네트워크 개발 및 배치](#develop-composer)

{{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Composer를 사용하여 빌드된 샘플 애플리케이션 및 몇 번의 클릭만으로 스타터 플랜에 배치할 수 있는 체인코드로 빌드된 샘플을 제공합니다.

- [샘플 애플리케이션 배치](#sample-applications)

**네트워크 작동 및 관리**

{{site.data.keyword.blockchainfull_notm}} Platform은 블록체인의 멤버십, 라이프사이클 및 상태를 관리하기 위해 사용할 수 있는 도구 및 API를 제공합니다. 이러한 기능 중 대부분은 네트워크 모니터 사용자 인터페이스를 통해 액세스할 수 있습니다.

- [네트워크 리소스 모니터링](#monitoring-resources)
- [네트워크 인증 정보 및 연결 프로파일 검색](#retrieving-network-credentials)
- [Swagger API를 사용하여 네트워크 관리](#swagger)
- [네트워크 재설정](#resetting-network)
- [스타터 플랜에서 엔터프라이즈 플랜으로 마이그레이션](#migrate)
- [네트워크 삭제 또는 나가기](#delete-network)


## 네트워크 작성
{: #creating-a-network}

{{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스를 작성한 후 즉시 기본 구성의 스타터 플랜 [네트워크](/docs/services/blockchain/glossary.html#network)를 가져올 수 있습니다.

1. {{site.data.keyword.cloud_notm}} 카탈로그에서 [Blockchain 서비스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)를 찾으십시오.
    **참고**: {{site.data.keyword.cloud_notm}} 유료 계정으로 로그인해야 합니다. 계정이 없는 경우, **회원가입하기** 단추를 클릭하십시오. 무료 평가판 계정을 작성한 후 {{site.data.keyword.cloud_notm}} 콘솔에서 **관리** > **청구 및 사용량** > **청구**로 이동하고 **신용카드 추가**를 클릭하여 **종량과금제** 유형으로 업그레이드하십시오.
2. 네트워크를 작성할 {{site.data.keyword.cloud_notm}}의 지역을 선택하십시오.
3. 네트워크를 작성할 Cloud Foundry 조직 및 영역을 선택하십시오.
4. 가격 플랜 테이블에서 **스타터 멤버십 플랜**을 선택하십시오.
5. **작성** 단추를 클릭하십시오. 네트워크에 가입하도록 초대되면 팝업 환영 패널을 볼 수 있습니다. 네트워크를 작성하려면 **네트워크 진행**을 선택하고 **계속**을 클릭하십시오. 네트워크에 가입하려면 [네트워크 가입](#joining-a-network)의 5단계를 참조하십시오.
  이제 기본 구성의 스타터 플랜 네트워크를 사용할 준비가 되었습니다. 네트워크는 하나의 순서 지정자("SOLO" 순서 지정 서비스로 알려짐), 두 개의 조직, 하나의 CA 및 각 조직당 하나의 피어와 함께 실행됩니다. 기본 채널도 작성됩니다.
6. **실행** 단추를 클릭하십시오.

[{{site.data.keyword.Bluemix_notm}} 서비스 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} 서비스 대시보드")에서 블록체인 서비스 인스턴스를 찾을 수 있습니다.


## 구성원 초대
{: #inviting-members}

스타터 플랜 네트워크에 [구성원](/docs/services/blockchain/glossary.html#member)으로 가입하도록 다른 [조직](/docs/services/blockchain/glossary.html#organization)을 초대하여 서로 [트랜잭션](/docs/services/blockchain/glossary.html#transaction)을 수행할 수 있게 합니다. 또한 스타터 플랜을 학습 및 테스트용으로 사용하려는 경우 네트워크에 스스로 구성원을 추가하여 다중 조직 네트워크를 시뮬레이션할 수 있습니다.

1. 네트워크 모니터의 "구성원" 화면에서 **구성원 초대** 단추를 클릭하십시오.
2. "구성원 초대" 창이 열립니다.
    - 다른 조직을 초대하려는 경우 "구성원 초대"를 선택하십시오.  초대할 조직의 이름 및 운영자 이메일 주소를 지정하십시오.  또한 "참고 추가" 필드에 초대에 포함할 추가 정보를 입력할 수 있습니다.  **초대 전송** 단추를 클릭하십시오.  초대된 조직은 초대 이메일을 수신한 다음 이메일의 지시사항에 따라 네트워크에 가입할 수 있습니다.
    - 채널에 추가할 수 있는 조직을 추가하려면 "구성원 추가"를 선택하십시오.  새 조직의 이름을 지정하십시오. 선택적으로 새 조직에 피어를 추가하거나 네트워크 모니터에서 나중에 수행할 수 있습니다.  **작성** 단추를 클릭하십시오. 새 조직의 피어를 추가하는 경우 피어를 보려면 이 새 조직으로 전환해야 합니다.<!-- 조직 전환에 대한 자세한 정보는 [조직 전환](/docs/services/blockchain/v10_dashboard.html#switch-organizations)을 참조하십시오.-->


## 네트워크 가입
{: #joining-a-network}

네트워크 개시자에게 초대된 경우 네트워크 가입 방법에 대한 지시사항이 포함된 이메일 알림을 수신합니다. 이메일의 지시사항을 따르십시오. 그러면 네트워크의 구성원 중 하나가 됩니다.

{{site.data.keyword.cloud_notm}}의 스타터 플랜 멤버십 옵션을 사용하여 [{{site.data.keyword.blockchain}} Platform 서비스 인스턴스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)를 작성해야 합니다.

1. {{site.data.keyword.cloud_notm}} 계정으로 로그인하십시오. 계정이 없는 경우, **회원가입하기** 단추를 클릭하십시오.
2. {{site.data.keyword.blockchain}} 네트워크를 저장할 Cloud Foundry 조직을 선택하십시오.
3. 가격 플랜 테이블에서 **스타터 멤버십 플랜**을 선택하십시오.
4. **작성** 단추를 클릭하십시오. 서비스 인스턴스 페이지는 팝업 환영 패널에서 열립니다. 네트워크에 가입하거나 계속 고유 네트워크를 작성하도록 선택할 수 있습니다. 네트워크를 작성하려면 [네트워크 작성](#creating-a-network)의 4단계를 참조하십시오.
5. 환영 패널에서 **기존 네트워크 가입**을 선택하고 드롭 다운 목록에서 가입할 네트워크를 선택한 다음 **계속**을 클릭하십시오.

[{{site.data.keyword.cloud_notm}} 서비스 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 서비스 대시보드")에서 블록체인 서비스 인스턴스를 찾을 수 있습니다.


## 채널 작성
{: #create-channels}

채널은 다른 조직에 데이터를 노출하지 않고 트랜잭션을 수행하기 위해 조직 세트에서 사용됩니다. 스타터 플랜 네트워크의 구성원 및 채널을 업데이트할 수 있는 정책을 선택하여 [채널](/docs/services/blockchain/glossary.html#channel)을 작성할 수 있습니다.

자세한 정보는 [채널 작성](/docs/services/blockchain/howto/create_channel.html#creating-a-channel)을 참조하십시오. 다른 조직에서 사용자를 채널에 초대하는 경우 초대를 승인하고 사용자의 피어를 해당 채널에 가입하는 방법도 표시됩니다.

## 체인코드 개발
{: #develop-chaincode}

[체인코드](/docs/services/blockchain/glossary.html#chaincode)(스마트 계약으로 참조되기도 함)는 블록체인 원장에서 데이터를 읽고 업데이트할 수 있도록 해주는 소프트웨어입니다. 체인코드는 비즈니스 로직을 블록체인 네트워크의 모든 구성원이 승인하고 확인한 실행 가능 프로그램으로 전환할 수 있습니다.

체인코드 작성을 시작하는 방법 및 체인코드를 통해 액세스할 수 있는 Fabric 기능에 대한 자세한 정보는 [체인코드 개발](/docs/services/blockchain/howto/develop_chaincode.html) 튜토리얼을 참조하십시오.

## 체인코드 설치 및 인스턴스화
{: #install-instantiate-chaincode}
채널에 가입하고 비즈니스 로직을 개발한 후에는 네트워크의 피어에 체인코드를 설치해야 합니다. 네트워크 모니터를 사용하여 체인코드를 조직의 피어에 설치 및 인스턴스화하고 지속적인 개발을 위해 체인코드를 업데이트할 수 있습니다.

사전 빌드된 샘플에 대한 자세한 정보는 [체인코드 설치, 인스턴스화 및 업데이트](/docs/services/blockchain/howto/install_instantiate_chaincode.html)를 참조하십시오.


## Fabric SDK를 사용하여 애플리케이션 개발
{: #develop-apps}

{{site.data.keyword.blockchainfull_notm}} Platform은 Fabric SDK를 사용하여 연결할 수 있는 API 엔드포인트를 제공합니다. 이 SDK를 사용하여 애플리케이션에서 체인코드를 호출하고 블록체인 네트워크에 트랜잭션을 제출할 수 있습니다.

자세한 정보는 [애플리케이션 개발](/docs/services/blockchain/v10_application.html) 튜토리얼을 참조하십시오.


## Hyperledger Composer를 사용하여 사용자 정의 비즈니스 네트워크 개발 및 배치
{: #develop-composer}

스타터 플랜은 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 개발자 환경 및 Hyperledger Composer 개발자 도구 세트를 통합합니다. 비즈니스 요구에 따라 블록체인 네트워크를 개발할 수 있습니다.  비즈니스에 맞는 네트워크를 개발하면 비즈니스 네트워크를 스타터 플랜 네트워크에 배치할 수 있습니다.

자세한 정보는 [네트워크 개발](/docs/services/blockchain/develop.html) 및 [스타터 플랜에서 비즈니스 네트워크 배치](/docs/services/blockchain/develop_starter.html)를 참조하십시오.


## 샘플 애플리케이션 배치
{: #sample-applications}

스타터 플랜을 사용하면 몇 번의 클릭만으로 네트워크에 샘플 애플리케이션을 배치할 수 있습니다. 이러한 샘플을 사용하여 체인코드 및 Hyperledger Composer 비즈니스 네트워크에 대한 자세한 정보를 확인하고 해당 샘플을 자체 개발을 위한 시작점으로 사용할 수 있습니다.

자세한 정보는 [샘플 애플리케이션 배치](/docs/services/blockchain/howto/prebuilt_samples.html)를 참조하십시오.


## 네트워크 리소스 모니터링
{: #monitoring-resources}

애플리케이션이 트랜잭션을 요청하면 네트워크 모니터에서 트랜잭션 상태 정보를 볼 수 있습니다. 네트워크 모니터링에 대한 자세한 정보는 [네트워크 모니터링](/docs/services/blockchain/howto/monitor_network.html)을 참조하십시오.


## 네트워크 인증 정보 및 연결 프로파일 검색
{: #retrieving-network-credentials}

{{site.data.keyword.cloud_notm}}에서 스타터 플랜 네트워크를 작성한 후 서비스 인스턴스 페이지 또는 네트워크 모니터에서 네트워크 인증 정보 및 연결 프로파일을 검색할 수 있습니다.

### 서비스 인스턴스 페이지에서 검색
서비스 인스턴스를 작성하면 바로 서비스 인스턴스 페이지가 나타납니다. 또한 [{{site.data.keyword.cloud_notm}} 서비스 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 서비스 대시보드")에서 서비스를 클릭하여 서비스 인스턴스 페이지를 열 수 있습니다.

다음 단계에 따라 서비스 인증 정보를 검색하십시오.
1. 서비스 인스턴스 페이지의 왼쪽 네비게이터에서 **서비스 인증 정보**를 클릭하여 "서비스 인증 정보" 화면을 표시하십시오.
2. "서비스 인증 정보" 화면에서 **새 인증 정보**를 클릭하십시오.
3. "새 인증 정보 추가" 화면에서 인증 정보에 이름을 지정하고 "인라인 구성 매개변수 추가" 필드에 **{"type": "service_instance_token"}**을 입력하십시오. **추가**를 클릭하십시오. 새 인증 정보가 테이블에 추가됩니다. "조치" 열 아래의 **인증 정보 보기**를 클릭하여 인증 정보 세부사항을 볼 수 있습니다. 이 인증 정보에는 API에 권한을 부여하는 데 사용할 수 있는 API 키 및 시크릿이 포함됩니다.  

![네트워크 인증 정보 검색](images/service_credentials.gif "네트워크 인증 정보 검색")

### 네트워크 모니터에서 검색
네트워크 모니터의 "API" 화면에서 네트워크 인증 정보를 찾을 수 있습니다. API 사용에 대한 자세한 정보는 [Swagger API를 사용하여 네트워크와 상호작용](/docs/services/blockchain/howto/swagger_apis.html)을 참조하십시오.

네트워크 모니터의 "개요" 화면에서 연결 프로파일을 검색할 수 있습니다. "개요" 화면의 **연결 프로파일** 단추를 클릭하면 연결 프로파일이 새 페이지에 표시됩니다.

## Swagger API를 사용하여 네트워크 관리
{: #swagger}

{{site.data.keyword.blockchainfull_notm}} Platform은 네트워크의 노드, 채널, 피어 및 구성원을 관리하는 데 사용할 수 있는 Swagger의 여러 REST API를 노출합니다. 애플리케이션은 이러한 API를 사용하여 네트워크 모니터를 사용하지 않고 중요한 네트워크 리소스를 제어할 수 있습니다.

자세한 정보는 [Swagger API를 사용하여 네트워크와 상호작용](/docs/services/blockchain/howto/swagger_apis.html)을 참조하십시오.


## 네트워크 재설정
{: #resetting-network}

사용자 정의된 구성, 실행 중인 체인코드 또는 배치된 애플리케이션을 정리하려는 경우 네트워크를 초기 기본 구성으로 다시 설정할 수 있습니다. 자세한 정보는 [네트워크 재설정](/docs/services/blockchain/v10_dashboard.html#reset-network)을 참조하십시오.


## 스타터 플랜에서 엔터프라이즈 플랜으로 마이그레이션
{: #migrate}

스타터 플랜 네트워크에 대해 테스트한 `.bna`, 체인코드 및 애플리케이션을 엔터프라이즈 플랜 네트워크에 배치할 수 있습니다.

비즈니스 네트워크 아카이브 파일(`.bna`)이 있는 경우 [엔터프라이즈 플랜에 비즈니스 네트워크 배치](/docs/services/blockchain/develop_enterprise.html)에 대한 지시사항을 따르십시오. `.bna` 파일이 없으면 `composer network download` 명령을 사용하여 스타터 플랜 인스턴스에서 검색하십시오. `composer network download` 명령에 대한 자세한 정보는 [Hyperledger Composer 명령행 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}를 참조하십시오.

`.bna` 파일과 유사한 체인코드가 외부에서 개발되었습니다. 스타터 플랜 네트워크에 대해 테스트하는 체인코드를 엔터프라이즈에 배치하려면 [체인코드 설치, 인스턴스화 및 업데이트](/docs/services/blockchain/howto/install_instantiate_chaincode.html#installchaincode)의 지시사항을 따르십시오.

<!--
As you can see in [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](/docs/services/blockchain/howto/prebuilt_samples.html#deploy_sample_applications_manually).
-->

스타터 플랜 네트워크에 샘플 애플리케이션을 배치하고 이 애플리케이션을 엔터프라이즈 플랜 네트워크에 배치하려는 경우, 새 저장소에 분기(fork)된 애플리케이션 저장소를 복사한 다음 샘플 애플리케이션의 지시사항을 따라 엔터프라이즈 플랜 네트워크에 배치합니다.


## 네트워크 삭제 또는 나가기
{: #delete-network}

네트워크를 삭제하거나 나가려는 경우, {{site.data.keyword.cloud_notm}} 대시보드에서 블록체인 서비스 인스턴스를 삭제할 수 있습니다.

**참고**: 네트워크에서 나가기 전에 네트워크에 있는 채널의 구성원이 아닌지 확인하십시오. 구성원이면 네트워크를 나갈 때 오류가 발생합니다. 채널 업데이트 프로세스를 완료하려면 채널 구성원을 제거해야 합니다. 채널 업데이트 프로세스에 대한 자세한 정보는 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)를 참조하십시오.


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](/docs/services/blockchain/index.html).
* For more information about Hyperledger Fabric V1.2, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/){:new_window}.
-->

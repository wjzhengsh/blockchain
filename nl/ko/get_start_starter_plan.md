---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 스타터 플랜 네트워크 통제
{: #getting-started-with-starter-plan}

{{site.data.keyword.blockchainfull}} Platform 스타터 플랜은 한 번의 클릭으로 사전 구성된 블록체인 네트워크를 제공합니다. 기본적으로 권한 부여된 네트워크에 두 개의 [조직](glossary.html#organization), 각 조직당 하나의 [피어](glossary.html#peer) 및 하나의 [채널](glossary.html#channel)의 구성을 <!--offers you a free trial of 30 days and -->프로비저닝합니다. 네트워크를 작성한 후 네트워크에 조직 및 피어를 스케일링하고 추가할 수 있습니다. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**참고**:
1. {{site.data.keyword.blockchainfull}} Platform 스타터 플랜은 개발 및 테스트 환경입니다. 프로덕션 환경이 필요한 경우 [엔터프라이즈 플랜 정보](enterprise_plan.html)를 참조하십시오.
2. 스타터 플랜은 베타 단계에 있으며 {{site.data.keyword.cloud_notm}}의 **미국** 지역에서만 사용 가능합니다.

스타터 플랜을 사용하면 {{site.data.keyword.blockchainfull_notm}} Platform에 대한 기술을 학습하고 개발하며 샘플 애플리케이션을 실행하고 고유 애플리케이션을 테스트하고 다중 조직 시나리오를 시뮬레이션할 수 있습니다. 이 시작하기 튜토리얼에서는 스타터 플랜 네트워크를 작성하고 사용하기 위해 따라야 하는 전제조건 및 단계를 소개합니다.

{{site.data.keyword.blockchainfull_notm}} Platform 및 블록체인을 처음 사용하는 경우, [용어집](glossary.html)을 통해 이 문서의 용어를 익히고 [Hyperledger Fabric 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html)를 통해 블록체인에 대해 자세히 알아볼 수 있습니다.


## 네트워크 작성
{{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스를 작성한 후 즉시 기본 구성의 스타터 플랜 [네트워크](glossary.html#network)를 가져올 수 있습니다.

1. {{site.data.keyword.cloud_notm}} 카탈로그에서 [블록체인 서비스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)를 찾으십시오. {{site.data.keyword.cloud_notm}} 계정으로 로그인해야 합니다. 계정이 없는 경우, **작성을 위해 등록** 단추를 클릭하십시오. {{site.data.keyword.cloud_notm}}에서 **미국**을 지역으로 선택했는지 확인하십시오.
2. Cloud Foundry 조직 및 영역을 선택하십시오. 여기서 네트워크를 작성합니다.
3. 가격 책정 플랜 테이블에서 **스타터 멤버십 플랜**을 선택하십시오.
4. **작성** 단추를 클릭하십시오. 네트워크에 가입하도록 초대되면 팝업 환영 패널을 볼 수 있습니다. 네트워크를 작성하려면 **네트워크 진행**을 선택하고 **계속**을 클릭하십시오. 네트워크에 가입하려면 [네트워크 가입](#joining-a-network)의 5단계를 참조하십시오.
  이제 기본 구성의 스타터 플랜 네트워크를 사용할 준비가 되었습니다. 네트워크는 하나의 순서 지정자("SOLO" 순서 지정 서비스로 알려짐), 두 개의 조직, 하나의 CA 및 각 조직당 하나의 피어와 함께 실행됩니다. 기본 채널도 작성됩니다.
5. **실행** 단추를 클릭하십시오.

[{{site.data.keyword.Bluemix_notm}} 서비스 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} 서비스 대시보드")에서 블록체인 서비스 인스턴스를 찾을 수 있습니다.


## 구성원 초대
스타터 플랜 네트워크에 [구성원](glossary.html#member)으로 가입하도록 다른 [조직](glossary.html#organization)을 초대하여 서로 [트랜잭션](glossary.html#transaction)을 수행할 수 있게 합니다. 또한 스타터 플랜을 학습 및 테스트용으로 사용하려는 경우 네트워크에 스스로 구성원을 추가하여 다중 조직 네트워크를 시뮬레이션할 수 있습니다.

1. 네트워크 모니터의 "구성원" 화면에서 **구성원 초대** 단추를 클릭하십시오.
2. "구성원 초대" 창이 열립니다.
    - 다른 조직을 초대하려는 경우 "구성원 초대"를 선택하십시오. 초대할 조직의 이름 및 운영자 이메일 주소를 지정하십시오. 또한 "참고 추가" 필드에 초대에 포함할 추가 정보를 입력할 수 있습니다. **초대 전송** 단추를 클릭하십시오. 초대된 조직은 초대 이메일을 수신한 다음 이메일의 지시사항에 따라 네트워크에 가입할 수 있습니다.
    - 채널에 추가할 수 있는 조직을 추가하려면 "구성원 추가"를 선택하십시오. 새 조직의 이름을 지정하십시오. 선택적으로 새 조직에 피어를 추가하거나 네트워크 모니터에서 나중에 수행할 수 있습니다. **작성** 단추를 클릭하십시오. 새 조직의 피어를 추가하는 경우 피어를 보려면 이 새 조직으로 전환해야 합니다. 조직 전환에 대한 자세한 정보는 [조직 전환](dashboard.html#switch-organizations)을 참조하십시오.


## 네트워크 가입
네트워크 개시자에게 초대된 경우 네트워크 가입 방법에 대한 지시사항이 포함된 이메일 알림을 수신합니다. 이메일의 지시사항을 따르십시오. 그러면 네트워크의 구성원 중 하나가 됩니다.

{{site.data.keyword.cloud_notm}}의 스타터 플랜 멤버십 옵션을 사용하여 [{{site.data.keyword.blockchain}} Platform 서비스 인스턴스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)를 작성해야 합니다.

1. {{site.data.keyword.cloud_notm}} 계정으로 로그인하십시오. 계정이 없는 경우, **작성을 위해 등록** 단추를 클릭하십시오.
2. {{site.data.keyword.blockchain}} 네트워크를 저장할 Cloud Foundry 조직을 선택하십시오.
3. 가격 책정 플랜 테이블에서 **스타터 멤버십 플랜**을 선택하십시오.
4. **작성** 단추를 클릭하십시오. 서비스 인스턴스 페이지는 팝업 환영 패널에서 열립니다. 네트워크에 가입하거나 계속 고유 네트워크를 작성하도록 선택할 수 있습니다. 네트워크를 작성하려면 [네트워크 작성](#creating-a-network)의 4단계를 참조하십시오.
5. 환영 패널에서 **기존 네트워크 가입**을 선택하고 드롭 다운 목록에서 가입할 네트워크를 선택한 다음 **계속**을 클릭하십시오.

[{{site.data.keyword.cloud_notm}} 서비스 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 서비스 대시보드")에서 블록체인 서비스 인스턴스를 찾을 수 있습니다.

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing and instantiating a chaincode](howto/install_instantiate_chaincode.html).
-->


## 네트워크 신임 정보 및 연결 프로파일 검색
{{site.data.keyword.cloud_notm}}에서 스타터 플랜 네트워크를 작성한 후 서비스 인스턴스 페이지 또는 네트워크 모니터에서 네트워크 신임 정보 및 연결 프로파일을 검색할 수 있습니다.

### 서비스 인스턴스 페이지에서 검색
서비스 인스턴스를 작성하면 바로 서비스 인스턴스 페이지가 나타납니다. 또한 [{{site.data.keyword.cloud_notm}} 서비스 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} 서비스 대시보드")에서 서비스를 클릭하여 서비스 인스턴스 페이지를 열 수 있습니다.

다음 단계에 따라 서비스 신임 정보를 검색하십시오.
1. 서비스 인스턴스 페이지의 왼쪽 네비게이터에서 **서비스 신임 정보**를 클릭하여 "서비스 신임 정보" 화면을 표시하십시오.
2. "서비스 신임 정보" 화면에서 **새 신임 정보**를 클릭하십시오.
3. "신임 정보 새로 추가" 화면에서 신임 정보에 이름을 지정하고 **추가**를 클릭하십시오. 새 신임 정보가 테이블에 추가됩니다. "조치" 열 아래의 **신임 정보 보기**를 클릭하여 신임 정보 세부사항을 볼 수 있습니다. 이 신임 정보에는 API에 권한을 부여하는 데 사용할 수 있는 API 키 및 시크릿이 포함됩니다. 네트워크의 연결 프로파일을 보려면 신임 정보를 새로 작성할 때 **{"legacy": true}**를 인라인 구성 매개변수로 입력하십시오. 연결 프로파일에는 네트워크 리소스에 대한 API 엔드포인트가 포함되며, 이 API 엔드포인트를 API 및 애플리케이션에서 사용할 수 있습니다.

### 네트워크 모니터에서 검색
네트워크 모니터의 "API" 화면에서 네트워크 신임 정보를 찾을 수 있습니다. API 사용에 대한 자세한 정보는 [Swagger에서 API 시험 사용](apis.html)을 참조하십시오.

네트워크 모니터의 "개요" 화면에서 연결 프로파일을 검색할 수 있습니다. "개요" 화면의 **연결 프로파일** 단추를 클릭하면 연결 프로파일이 새 페이지에 표시됩니다.


## 샘플 애플리케이션 배치
스타터 플랜을 사용하면 몇 번의 클릭만으로 네트워크에 샘플 애플리케이션을 배치할 수 있습니다. 샘플을 사용하면 체인코드 및 애플리케이션이 네트워크에서 작업하는 방식을 쉽게 학습할 수 있습니다.

자세한 정보는 [샘플 애플리케이션 배치](howto/prebuilt_samples.html)를 참조하십시오.


## 사용자 정의 비즈니스 네트워크 개발 및 전개
스타터 플랜은 IBM Blockchain Platform: Develop 개발자 환경 및 Hyperledger Composer 개발자 도구 세트를 통합합니다. 비즈니스 요구에 따라 블록체인 네트워크를 개발할 수 있습니다. 비즈니스에 맞는 네트워크를 개발하면 비즈니스 네트워크를 스타터 플랜 네트워크에 배치할 수 있습니다.

자세한 정보는 [네트워크 개발](develop.html) 및 [스타터 플랜에서 비즈니스 네트워크 배치](develop_starter.html)를 참조하십시오.


## 네트워크 리소스 모니터링
애플리케이션이 트랜잭션을 요청하면 네트워크 모니터에서 트랜잭션 상태 정보를 볼 수 있습니다. 네트워크 모니터링에 대한 자세한 정보는 [네트워크 모니터링](howto/monitor_network.html)을 참조하십시오.


## 네트워크 재설정
사용자 정의된 구성, 실행 중인 체인코드 또는 배치된 애플리케이션을 정리하려는 경우 네트워크를 초기 기본 구성으로 다시 설정할 수 있습니다. 자세한 정보는 [네트워크 재설정](dashboard.html#reset-network)을 참조하십시오.


## 스타터 플랜에서 엔터프라이즈 플랜으로 마이그레이션
{: #migrate}

스타터 플랜 네트워크에 대해 테스트한 `.bna`, 체인코드 및 애플리케이션을 엔터프라이즈 플랜 네트워크에 배치할 수 있습니다.

비즈니스 네트워크 아카이브 파일(`.bna`)이 있는 경우 [엔터프라이즈 플랜에 비즈니스 네트워크 배치](./develop_enterprise.html)에 대한 지시사항을 따르십시오. `.bna` 파일이 없으면 `composer network download` 명령을 사용하여 스타터 플랜 인스턴스에서 검색하십시오. `composer network download` 명령에 대한 자세한 정보는 [Hyperledger Composer 명령행 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/reference/commands){:new_window}를 참조하십시오.

`.bna` 파일과 유사한 체인코드가 외부에서 개발되었습니다. 스타터 플랜 네트워크에 대해 테스트한 체인코드를 엔터프라이즈에 배치하려면 [체인코드 설치 및 인스턴스화](howto/install_instantiate_chaincode.html#installchaincode)의 지시사항을 따르십시오.

[샘플 애플리케이션 배치](howto/prebuilt_samples.html)에서 볼 수 있는 것처럼 스타터 플랜에서는 도구 체인을 사용하여 샘플 애플리케이션을 네트워크에 쉽게 통합할 수 있습니다. 또한 이 설정에서는 복제(fork)된 애플리케이션 저장소를 변경할 때마다 샘플 애플리케이션을 자동으로 업데이트하여 지속적 통합을 허용합니다. 이 애플리케이션을 엔터프라이즈 플랜 네트워크에 배치하려는 경우, 새 저장소에 복제(fork)된 애플리케이션 저장소를 복사한 다음 [샘플 애플리케이션 수동 배치](howto/prebuilt_samples.html#deploy_sample_applications_manually)의 지시사항을 따를 수 있습니다.


## 네트워크 삭제 또는 나가기
네트워크를 삭제하거나 나가려는 경우, {{site.data.keyword.cloud_notm}} 대시보드에서 블록체인 서비스 인스턴스를 삭제할 수 있습니다.

**참고**: 네트워크를 나가기 전에 네트워크 내의 채널의 구성원이 아닌지 확인하십시오. 구성원이면 네트워크를 나갈 때 오류가 발생합니다. 채널 업데이트 프로세스를 완료하려면 채널 구성원을 제거해야 합니다. 채널 업데이트 프로세스에 대한 자세한 정보는 [채널 업데이트](howto/create_channel.html#updating-a-channel)를 참조하십시오.


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 엔터프라이즈 플랜 네트워크 운영
{: #v10_dashboard}

{{site.data.keyword.blockchainfull}} Platform은 네트워크 모니터가 네트워크 리소스, 구성원, 가입된 채널, 트랜잭션 성능 데이터 및 배치된 체인코드를 포함하는 블록체인 환경의 개요를 제공하도록 합니다. 또한 네트워크 모니터는 Swagger API를 실행하고 {{site.data.keyword.blockchainfull_notm}} Platform: Dvelop을 사용하여 네트워크를 개발하며 샘플 애플리케이션을 시도하기 위한 시작점을 제공합니다.
{:shortdesc}

네트워크 모니터에서 [엔터프라이즈 플랜 네트워크의 이름을 변경](#ep-network-name)할 수 있습니다.

네트워크 모니터는 다음 화면을 세 개의 섹션으로 표시합니다. 네트워크 모니터의 왼쪽 네비게이터에서 각 화면으로 이동할 수 있습니다.
- **내 네트워크** 섹션에는 "[개요](#overview)", "[구성원](#members)", "[채널](#channels)", "[알림](#notifications)" 및 "[API](#apis)" 화면이 포함됩니다.
- **내 코드** 섹션에는 "[코드 개발](#write_code)", "[코드 설치](#chaincode)" 및 "[샘플 시험 사용](#samples)" 화면이 포함됩니다.
- "[도움 받기](#support)" 화면에는 Helios 및 Hyperledger Fabric({{site.data.keyword.blockchainfull_notm}} Platform이 기반으로 하는 코드 베이스)의 릴리스 정보 뿐만 아니라 지원 정보가 표시됩니다.

네트워크 모니터의 오른쪽 상단에 있는 드롭 다운 메뉴에서 [네트워크 환경 설정을 확인 및 구성](#network-preferences)할 수 있습니다.

이 튜토리얼에서는 위의 각 화면 및 기능을 설명합니다.

## 네트워크 이름 업데이트
{: #ep-network-name}

엔터프라이즈 플랜 네트워크를 작성할 때 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 이름을 지정합니다. 그러나 언제든지 네트워크 모니터에서 이 네트워크 이름을 업데이트할 수 있습니다.

네트워크 모니터에서 왼쪽 네비게이터의 맨 위에 있는 네트워크 이름을 클릭하면 필드가 편집 가능하게 됩니다. 사용할 네트워크 이름을 입력하고 **Enter** 키를 누르십시오. 몇 초 후에 네트워크 이름이 업데이트됩니다.

**그림 1**은 스타터 플랜 네트워크 이름을 지정된 이름에서 "Starter Plan Network"로 업데이트하는 단계를 보여줍니다.

![네트워크 이름 업데이트](images/update_network_name_ep.gif "네트워크 이름 업데이트")
*그림 1. 네트워크 이름 업데이트*


## 개요
{: #overview}

"개요" 화면에는 순서 지정자, CA 및 피어 노드를 포함한 블록체인 리소스의 실시간 상태 정보가 표시됩니다. 각 리소스는 네 개의 개별 헤더인 **유형**, **이름**, **상태** 및 **조치** 아래에 표시됩니다. 사용자의 블록체인 네트워크 작성 중에 세 개의 순서 지정자 노드 및 두 개의 CA 노드가 자동으로 작성됩니다. CA는 구성원에 따라 다른 반면, 순서 지정 노드는 네트워크 전체에서 공유되는 공통 엔드포인트입니다.

**그림 2**는 "개요" 화면을 보여줍니다.

![개요 화면](images/myresources.png "네트워크 개요")
*그림 2. 네트워크 개요*

### 노드 조치
테이블의 **조치** 헤더에 리소스를 시작하거나 중지하는 단추가 제공됩니다. 다중 노드를 선택한 후에 **선택사항 시작** 또는 **선택사항 중지** 단추를 클릭하여 노드 그룹을 시작하거나 중지할 수도 있습니다. 하나 이상의 노드를 선택하면 **선택사항 시작** 또는 **선택사항 중지** 단추가 테이블의 맨 위에 테이블시됩니다.

중지 및 시작 조치는 순서 지정자 노드에서 사용할 수 없습니다. 일반적으로 네트워크에서 피어 또는 CA 노드를 중지하고 시작하지 않아도 됩니다. 예를 들어, 클린 상태가 되도록 피어를 다시 시작해야 하는 경우 중지 및 시작 조치가 제공됩니다.

**조치** 헤더 아래의 드롭 다운 목록에서 **로그 보기**를 클릭하여 컴포넌트 로그를 확인할 수도 있습니다. 로그는 다양한 네트워크 리소스 간 호출을 노출하며 디버깅 및 문제점 해결에 유용합니다. 예를 들어, 피어를 중지하고 트랜잭션으로 이를 대상으로 지정하여 실험하면 연결 오류가 표시됩니다. 피어를 다시 시작해서 트랜잭션을 다시 시도하면 연결에 성공합니다. 또한 채널에서 계속 트랜잭션을 수행하므로 연장된 기간 동안 피어를 작동 중지 상태로 남겨둘 수 있습니다. 피어를 다시 가져오면, 피어가 중지되었을 때 커미트된 블록을 수신하므로 원장의 동기화를 알게 됩니다. 원장이 완전히 동기화되면 이 원장에 대해 일반 호출 및 조회를 수행할 수 있습니다.

### 연결 프로파일
{: #enterprise-connection-profile}
**연결 프로파일** 단추를 클릭하여 각 리소스의 하위 레벨 네트워크 정보에 대한 JSON 파일을 볼 수 있습니다. 연결 프로파일에는 애플리케이션에 필요한 모든 구성 정보가 포함됩니다. 하지만 이 파일에는 특정 컴포넌트 및 순서 지정자의 주소만 있으므로, 추가 피어를 대상으로 지정해야 하는 경우 해당 엔드포인트를 확보해야 합니다. "url"이 포함된 헤더에는 각 컴포넌트의 API 엔드포인트가 표시됩니다. 클라이언트 측 애플리케이션에서 특정 네트워크 컴포넌트를 대상으로 지정하기 위해 이러한 엔드포인트가 필요하며 그 정의는 일반적으로 앱을 동반하는 JSON 모델 구성 파일에 존재하게 됩니다. 조직의 일부가 아닌 피어에서 보증을 필요로 하는 애플리케이션을 사용자 정의하는 경우, 대역 외 오퍼레이션의 관련 운영자에서 해당 피어의 IP 주소를 검색해야 합니다. 클라이언트는 응답이 필요한 피어로 연결할 수 있어야 합니다.

### 피어 추가
{: #peers}
**피어 추가** 단추를 클릭하여 네트워크에 피어 노드를 추가하십시오. 스타터 플랜에서 네트워크 작성 시 두 개의 피어가 자동으로 추가됩니다. 엔터프라이즈 플랜에서는 네트워크를 작성하거나 가입할 때 피어 노드를 처음 추가하거나 나중에 네트워크 모니터에서 추가할 수 있습니다. 추가 피어가 필요한 경우 여러 시나리오가 있습니다.  예를 들어, 다중 피어가 중복성을 위해 동일한 채널에 가입하게 할 수 있습니다. 각 피어는 채널의 트랜잭션을 처리하고 원장의 각 사본에 기록합니다. 피어 중 하나가 실패하면 다른 피어가 트랜잭션 및 애플리케이션 요청 처리를 계속할 수 있습니다.  피어에서 모든 애플리케이션 요청을 균형 있게 로드 밸런싱하거나 여러 기능에 대해 서로 다른 피어를 대상으로 지정할 수 있습니다. 예를 들어, 하나의 피어를 사용하여 원장을 조회하고 다른 피어를 사용하여 원장 업데이트에 대한 보증을 처리할 수 있습니다.

  팝업 "피어 추가" 패널에서 추가할 피어 노드 수를 선택하십시오. <!--Currently only "small" peers are available for purchase, however there will eventually be "medium" and "large" to help accommodate larger workloads and higher transaction throughput.-->

## 구성원
{: #members}
"구성원" 화면에는 "구성원" 탭에서 네트워크 구성원 정보, "인증서" 탭에서 인증서 정보를 표시하는 두 개의 탭이 포함되어 있습니다.

### 구성원
{: #members_tab}
**그림 3**은 "구성원" 탭에 네트워크 구성원을 표시하는 초기 "구성원" 화면을 보여줍니다.

![구성원 화면의 구성원 탭](images/monitor_members.png "네트워크 구성원")
*그림 3. 네트워크 구성원*

네트워크를 작성할 때 초대하는 구성원을 제외한 "구성원" 탭의 다른 구성원을 초대할 수 있습니다. 구성원을 사용자의 네트워크에 초대하려면 기관 이름 및 운영자의 이메일 주소를 입력하고 **구성원 추가**를 클릭하십시오. 네트워크에는 총 15개의 구성원이 있을 수 있습니다(네트워크 개시자 포함). 사용자의 네트워크에서 구성원을 제거하려면 구성원 행의 끝에 있는 "제거" 기호를 클릭하십시오.

### 인증서
**그림 4**는 "인증서" 탭에 구성원 인증서를 표시하는 초기 "구성원" 화면을 보여줍니다.

![구성원 화면의 인증서 탭](images/monitor_certificates.png "인증서")
*그림 4. 인증서*

운영자는 "인증서" 탭에서 같은 기관의 구성원에 대한 인증서를 관리할 수 있습니다. **인증서 추가**를 클릭하여 "인증서 추가" 패널을 여십시오. 인증서에 이름을 지정하고, PEM 형식의 클라이언트 측 인증서를 "키" 필드에 붙여넣고 **제출**을 클릭하십시오. 클라이언트 측 인증서가 적용되려면 먼저 피어를 다시 시작해야 합니다.

인증서 키 생성에 대한 자세한 정보는 [클라이언트 측 인증서 생성](v10_application.html#generating-the-client-side-certificates)을 참조하십시오.

## 채널
{: #channels}

각 채널이 해당 채널에서 인스턴스화된 체인코드에 대한 데이터를 볼 수 있는 구성원의 서브세트를 표시하는 채널로 사용자의 네트워크를 격리할 수 있습니다. 모든 네트워크에는 적어도 한 개의 채널이 있어야 트랜잭션이 발생할 수 있습니다. 각 채널에는 고유 원장이 있으며 이 원장에 대해 읽기/쓰기 오퍼레이션을 수행하려면 사용자가 올바르게 인증되어야 합니다. 사용자가 채널에 없는 경우에는 데이터를 볼 수 없습니다.

**그림 5**는 네트워크에서 모든 채널의 개요를 표시하는 초기 대시보드 화면을 보여줍니다.

![채널](images/channels.png "채널")
*그림 5. 채널*

채널을 작성하면 채널 특정 원장이 생성됩니다. 자세한 정보는 [채널 작성](howto/create_channel.html)을 참조하십시오.

또한 기존 채널을 선택하여 채널, 멤버십 및 활성 체인코드에 대한 더 정확한 세부사항을 볼 수 있습니다. 자세한 정보는 [네트워크 모니터링](howto/monitor_network.html)을 참조하십시오.

## 알림
{: #notifications}

보류 중인 요청을 처리하고 "알림" 화면에서 완료된 요청을 볼 수 있습니다.

**그림 6**은 "알림" 화면을 보여줍니다.

![알림](images/notifications.png "알림")
*그림 6. 알림*

채널을 작성하거나 새 채널에 초대되면 네트워크 모니터에 알림이 표시됩니다.

요청은 "모두", "보류 중" 및 "완료됨" 하위 탭으로 그룹화됩니다. 하위 탭 헤더 다음의 수는 각 탭의 요청 수를 나타냅니다.
   * "모두" 하위 탭에서 모든 요청을 찾을 수 있습니다.
   * 승인 또는 거부하지 않았거나 보지 않은 요청은 "보류 중" 하위 탭에 있습니다. **요청 검토** 단추를 클릭하여 채널 정책, 구성원 및 투표 상태가 포함된 요청을 보십시오. 채널 운영자이면 요청을 **승인** 또는 **거부**하거나 **나중에**를 클릭하여 다른 시간에 처리할 수 있습니다. 요청이 충분한 채널 운영자들에 의해 승인되면 **요청 제출**을 클릭하여 채널 업데이트를 활성화할 수 있습니다.
   * 제출된 요청이 "완료됨" 하위 탭에 표시됩니다.  **요청 검토**를 클릭하여 그 세부사항을 볼 수 있습니다.

요청의 목록이 긴 경우, 맨 위의 검색 필드에서 요청을 검색할 수 있습니다.

보류 중인 요청은 요청의 앞에 있는 상자를 선택하고 **요청 삭제**를 클릭하여 삭제할 수 있습니다. 완료된 요청은 삭제할 수 없음에 유의하십시오.

## API
{: #apis}

{{site.data.keyword.blockchainfull_notm}} Platform은 네트워크의 노드, 채널, 피어 및 멤버를 관리하는 데 사용할 수 있는 Swagger의 여러 REST API를 노출합니다. 애플리케이션은 이러한 API를 사용하여 네트워크 모니터를 사용하지 않고 중요한 네트워크 리소스를 제어할 수 있습니다.

**그림 7**은 "API" 화면을 보여줍니다.

![API](images/API_screen.png "API")
*그림 7. API*

**Swagger UI** 링크를 클릭하여 Swagger UI를 여십시오. API를 실행하려면 네트워크 신임 정보(이 API 페이지에 있음)를 사용하여 Swagger UI에 권한을 부여해야 합니다. 자세한 정보는 [Swagger API를 사용하여 네트워크와 상호작용](howto/swagger_apis.html)을 참조하십시오.

## 코드 개발
{: #write-code}

엔터프라이즈 플랜은 {{site.data.keyword.blockchainfull_notm}} Platform: Develop을 통합하고 산업 표준 도구와 기술을 개발 환경에 제공합니다. 사용자는 온라인 또는 로컬로 환경에서 네트워크를 개발할 수 있습니다. 네트워크를 개발한 후 엔터프라이즈 플랜 네트워크에 다시 배치할 수 있습니다.

**그림 8**은 "코드 개발" 화면을 보여줍니다.

![코드 개발](images/write_code.png "코드 개발")
*그림 8. 코드 개발*

엔터프라이즈 플랜에서 코드를 개발하고 배치하는 데 대한 자세한 정보는 [엔터프라이즈 플랜에서 비즈니스 네트워크 개발](develop_enterprise.html)을 참조하십시오.

## 코드 설치
{: #chaincode}

"스마트 계약"이라고도 하는 체인코드는 원장을 조회하고 업데이트하는 기능 세트가 포함된 소프트웨어의 부분입니다. 이 체인코드는 피어에 설치되고 채널에서 인스턴스화됩니다.

**그림 9**는 "코드 설치" 화면을 보여줍니다.

![코드 설치](images/chaincode_install_overview.png "코드 설치")
*그림 9. 코드 설치*

체인코드는 먼저 피어의 파일 시스템에 설치되고 그 다음에 채널에서 인스턴스화됩니다. 자세한 정보는 [체인코드 설치, 인스턴스화 및 업데이트](howto/install_instantiate_chaincode.html)를 참조하십시오.

## 샘플 시험 사용
{: #samples}

샘플 애플리케이션을 사용하면 블록체인 네트워크 및 애플리케이션 개발에 대해 이해하는 데 도움이 됩니다. **GitHub에서 보기** 링크를 따라 샘플을 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform에 배치하는 방법을 학습하십시오. 샘플 개발 및 배치 방법에 대한 자세한 정보는 [샘플 애플리케이션 배치](howto/prebuilt_samples.html)를 참조하십시오.

**그림 10**은 "샘플 시험 사용" 화면을 보여줍니다.

![샘플 시험 사용](images/sample_overview_ep.png "샘플 시험 사용")
*그림 10. 샘플*

## 도움 받기
{: #support}

"도움 받기" 화면에는 "지원" 탭에서 지원 정보를 제공하고 "릴리스 정보" 탭에서 각 릴리스의 새 기능 및 변경된 기능에 대해 설명하는 두 개의 탭이 포함되어 있습니다.

**그림 11**은 "지원" 탭에 지원 정보를 표시하는 초기 "지원" 화면을 보여줍니다.

![지원](images/support.png "지원")
*그림 11. Blockchain 지원*

이 페이지의 링크 및 리소스를 사용하여 문제점 해결 및 지원 포럼에 액세스할 수 있습니다.

* **시작하기** 아래의 이 문서 사이트인 [{{site.data.keyword.blockchainfull_notm}} 서비스 문서](index.html)에서는 {{site.data.keyword.Bluemix_notm}}에서 {{site.data.keyword.blockchainfull}} Platform 시작 방법에 대한 안내를 제공합니다. 왼쪽 네비게이터에서 해당 주제를 찾거나 맨 위에 있는 검색 기능으로 용어를 검색할 수 있습니다.
* **커뮤니티 도움말** 아래의 [IBM DeveloperWorks ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://developer.ibm.com/blockchain/)에는 개발자를 위한 리소스와 정보가 포함되어 있습니다.
* **지원 티켓** 아래의 [IBM dWAnswers ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://developer.ibm.com/answers/smartspace/blockchain/)는 질문과 응답을 위한 플랫폼 역할을 합니다. 이전 질문에서 응답을 검색하거나 새 질문을 제출할 수 있습니다. 질문에 키워드 **blockchain**을 포함하십시오.
  또한 **{{site.data.keyword.Bluemix_notm}} 지원 티켓 열기** 옵션을 사용하여 {{site.data.keyword.blockchainfull_notm}} 지원 팀에 티켓을 제출할 수 있습니다.  특정 {{site.data.keyword.Bluemix_notm}} 인스턴스의 세부사항 및 코드 스니펫을 공유하십시오.
* **Blockchain 샘플 애플리케이션** 아래의 [샘플 애플리케이션 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/ibm-blockchain)은 애플리케이션 개발을 지원하는 안내 및 샘플 코드 스니펫을 제공합니다.
* **Hyperledger Fabric** 아래의 [Hyperledger Fabric ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/) 및 [Hyperledger Fabric 커뮤니티 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://jira.hyperledger.org/secure/Dashboard.jspa)는 Hyperledger Fabric 스택에 대한 추가 세부사항을 제공합니다.
    Hyperledger Fabric 코드에 대한 질문이 있으면 [Hyperledger 전문가 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://chat.hyperledger.org/channel/general)와 대화하십시오.

문제를 디버깅하거나 질문에 대한 답변을 확인할 수 없는 경우 IBM Cloud 서비스 포털에 지원 케이스를 제출하십시오. 자세한 정보는 [지원 받기](ibmblockchain_support.html)를 참조하십시오.

그림 12 및 그림 13은 "릴리스 정보" 탭에 각 릴리스의 새 기능과 변경된 기능을 표시하는 초기 "도움 받기" 화면을 보여줍니다.

![릴리스 정보 Helios](images/releasenotes_helios.png "릴리스 정보 Helios")
*그림 12. Helios의 릴리스 정보*

![릴리스 정보 Fabric](images/releasenotes_Fabric.png "릴리스 정보 Fabric")
*그림 13. Fabric의 릴리스 정보*


## 네트워크 환경 설정
{: #network-preferences}

오른쪽 상단 모서리를 클릭하고 드롭 다운 메뉴가 열리면 **네트워크 환경 설정**을 클릭하십시오. 네트워크 환경 설정 창이 열립니다. 네트워크 환경 설정 창에는 네트워크 이름, Fabric 버전, {{site.data.keyword.cloud_notm}}의 네트워크 위치 및 원장 데이터베이스 유형과 같은 네트워크의 기본 정보가 표시됩니다.

2018년 5월 15일 이후에 작성된 엔터프라이즈 플랜 네트워크는 Hyperledger Fabric v1.1에서 실행됩니다. 업그레이드 후 네트워크를 작성하는 경우 네트워크 환경 설정 창에서 네트워크에 대한 웹 비활성 제한시간 및 상호 TLS를 관리할 수도 있습니다. 네트워크 개시자만 이러한 설정을 변경할 수 있습니다.

<!--

Enterprise Plan networks that are created after May 15th, 2018 will run on Hyperledger Fabric v1.1. If you create networks after the upgrade, you can also manage web inactivity timeout, mutual TLS, and switch your ledger to CouchDB for your network in the Network preferences window. These settings can be changed by the network initiator only.

-->

### 웹 비활성 제한시간
{: #web-inactivity-timeout}

**참고**: **네트워크 개시자**만 웹 비활성 제한시간 설정을 변경할 수 있습니다. 이는 네트워크 레벨 설정이며 모든 네트워크 구성원에 영향을 미칩니다.

기본적으로 웹 비활성 제한시간은 **해제**로 설정됩니다. 웹 비활성 제한시간을 **설정**으로 변환하면 네트워크가 10분 동안 비활성 상태로 있은 후에 자동으로 로그아웃됩니다. 웹 비활성 타이머가 10분에 도달하면 웹 비활성 제한시간 기능이 비활성 웹 세션을 종료하여 네트워크 구성원 계정의 보안을 보장합니다. 링크를 클릭하거나 네트워크 모니터를 새로 고치면 웹 비활성 타이머가 재설정됩니다. 10분에 도달하기 전에 브라우저 창 또는 탭을 닫아도 웹 세션이 종료됩니다.

### 상호 TLS
{: #mutual-tls}

상호 TLS는 애플리케이션과 네트워크 간 통신을 보안합니다.

**참고**: **네트워크 개시자**만 상호 TLS를 사용 또는 사용 안함으로 설정할 수 있습니다. 이는 네트워크 레벨 설정이며 모든 네트워크 구성원에 영향을 미칩니다.

기본적으로 상호 TLS 단추는 **해제**로 설정됩니다. 상호 TLS를 사용으로 설정하는 경우 이 기능을 지원하도록 애플리케이션을 업데이트해야 합니다. 그렇지 않으면 애플리케이션이 사용자 네트워크와 통신할 수 없습니다.

Fabric 1.1 엔터프라이즈 플랜 네트워크의 경우 각 조직에는 자체 상호 TLS 인증 기관(CA)이 있습니다. **연결 프로파일** 단추를 클릭하여 네트워크 모니터의 **개요** 화면에서 액세스할 수 있는 [연결 프로파일](##enterprise-connection-profile)에서 상호 TLS CA에 연결하는 데 필요한 정보를 볼 수 있습니다.  연결 프로파일에는 CA에 연결하고 네트워크 연결에 필요한 인증서를 가져오는 데 필요한 정보가 포함되어 있습니다.

연결 프로파일에서 `certificateAuthorities` 섹션을 찾으십시오. 이 섹션에는 상호 TLS를 사용하여 사용자 네트워크와 통신하기 위해 인증서를 등록접수하고 가져오는 데 필요한 다음 속성이 있습니다.

- `url`: 상호 TLS 인증서를 제공할 수 있는 CA에 연결하기 위한 URL
- `enrollId`: 인증서를 가져오는 데 사용할 등록접수 ID
- `enrollSecret`: 인증서를 가져오는 데 사용할 등록접수 시크릿
- `x-tlsCAName`: 애플리케이션이 상호 TLS를 사용하여 통신할 수 있도록 하는 인증서를 가져오는 데 사용할 CA 이름

상호 TLS를 지원하도록 애플리케이션을 업데이트하는 데 대한 자세한 정보는 [상호 TLS를 구성하는 방법 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html)을 참조하십시오.

<!--

### CouchDB ledger type
{: #couchdb}

**Note**: Only the **network initiator** can switch the ledger database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.

Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your ledger database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.

To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.

If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up index, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/couchdb_as_state_database.html). For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](howto/install_instantiate_chaincode.html#updating-a-chaincode).

-->

**그림 14**는 "네트워크 환경 설정" 창을 보여줍니다.

![네트워크 환경 설정](images/network_preferences_ep_tmp.png "네트워크 환경 설정")
*그림 14. 네트워크 환경 설정*

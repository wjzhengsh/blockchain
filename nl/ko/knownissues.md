---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-04"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 알려진 문제


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


다음 문제가 이미 보고되었습니다.
- **외부 CA 구성은 아직 지원되지 않습니다**. 그 대안으로 네트워크 모니터를 통해 관리자 인증서를 생성해서 업로드할 수 있습니다. 자세한 정보는 [클라이언트 측 인증서 생성](/docs/services/blockchain/v10_application.html#enroll-app) 및 네트워크 모니터에 있는 ["구성원" 화면의 "인증서" 탭](/docs/services/blockchain/v10_dashboard.html#members)에 있는 설명을 참조하십시오.
- 스타터 플랜 네트워크의 네트워크 모니터에서 "개요" 화면에 나열된 노드에 대해 **로그 보기**를 클릭하면 {{site.data.keyword.cloud}} 로깅 Kibana 인터페이스가 열립니다. **기본적으로 Kibana는 최근 30일 동안의 활동에 대한 로그를 표시하도록 사전 구성됩니다**. 최근 30일 동안 활동이 없으면 *결과를 찾을 수 없음*이라는 메시지가 표시됩니다. 다른 로그를 보려면 오른쪽 상단에 있는 사용자 이름 아래의 타이머 아이콘을 클릭하고 보다 넓은 시간 범위(예: *올해 초부터 지금까지*)를 설정할 수 있습니다.
- 스타터 플랜 네트워크의 로그는 [{{site.data.keyword.cloud_notm}} 로그 분석 서비스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/log-analysis)에서 수집됩니다. 기본적으로 로그는 로그 분석 서비스의 Lite 플랜에서 수집됩니다. 이 플랜은 무료이며 **매일 처음 500MB의 로그만 검색할 수 있도록 허용합니다**. 네트워크의 로그가 500MB를 초과하면 Kibana에서 새 로그를 볼 수 없습니다. 네트워크가 500MB를 넘는 로그를 생성하는 경우 로그 분석 서비스의 유료 버전으로 업그레이드할 수 있습니다.
- 스타터 플랜은 프로덕션 환경이 아니므로 **애플리케이션이 바로 네트워크 리소스에 도달할 수 없을 수도 있습니다**.
  - 이러한 상황이 발생하면 첫 번째 단계로 Fabric SDK에서 기본 제한시간 값을 늘리는 것이 권장됩니다. 제한시간 값 설정에 대한 자세한 정보는 [Fabric SDK에서 제한시간 값 설정](/docs/services/blockchain/v10_application.html#set-timeout-in-sdk)을 참조하십시오.
  - 또한 애플리케이션 레벨에서 요청을 재시도할 수 있습니다.
- 백그라운드 네트워크 문제로 인해 **체인코드 컨테이너가 때때로 중지될 수 있으며** 사용자에 의해 호출된 후 다시 빌드되고 다시 시작되어야 할 수 있습니다. 이러한 상황이 발생하면 체인코드 응답에 수 분이 걸릴 수 있습니다.
- 스타터 플랜 네트워크의 리소스 제한사항(즉, 피어당 1개의 CPU 및 4Gi RAM)으로 인해 **체인코드 설치 중에 `REQUEST_TIMEOUT` 오류가 발생할 수 있습니다**. 이러한 상황이 발생하면 설치 단계를 재시도하십시오. 오류가 계속되면 체인코드 설치 제한시간을 늘릴 수 있습니다. 연결 프로파일에서 체인코드 설치 제한시간은 300초로 설정됩니다.
  - SDK에서 기본 제한시간 값을 사용하는 경우 아래에 표시된 대로(제한시간을 300초로 설정) 연결 프로파일에서 **클라이언트** 섹션을 복사하고 SDK가 이를 읽도록 하십시오. 노드 SDK의 경우 연결 프로파일의 이 제한시간 설정은 모든 호출(예: `invoke`, `queries` 등)에 적용됩니다.
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - SDK에서 체인코드 설치 명령의 제한시간 설정을 겹쳐쓰는 경우 이를 다시 기본값으로 설정하거나 300초 이상으로 변경하십시오.
    - 노드 SDK를 사용하는 경우 `sendInstantiateProposal(request, timeout)` 메소드의 제한시간 설정을 변경할 수 있습니다. 자세한 정보는 [sendInstantiateProposal(request, timeout) ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal)을 참조하십시오.
    - Java SDK를 사용하는 경우 `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java` 파일에 있는 `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)` 명령의 제한시간 설정을 변경할 수 있습니다.

{{site.data.keyword.cloud_notm}}의 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 대한 지원 및 도움은 [지원 받기](/docs/services/blockchain/ibmblockchain_support.html)를 참조하십시오.


## 엔터프라이즈 플랜 네트워크 업그레이드를 위해 체인코드 업데이트
{: #chaincode-migration}

엔터프라이즈 플랜 네트워크가 Hyperledger Fabric V1.0 레벨에 있는 경우 {{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Fabric V1.1로 마이그레이션하는 네트워크의 업그레이드를 스케줄합니다. 네트워크 업그레이드에서 종속 항목을 포함한 복합 체인코드를 사용하는 경우 체인코드에서 종속 항목을 업데이트해야 합니다. 그렇지 않으면 서비스 중단이 발생할 수 있습니다.

**참고**:
- 단일 `.go` 또는 `.js` 파일에 단순 체인코드를 사용하는 경우 체인코드를 업데이트하지 않아도 됩니다.
- 업데이트된 체인코드가 이전 네트워크에서 작동하지 않을 수 있습니다. 따라서 스케줄된 네트워크 업그레이드 후에 체인코드를 업데이트해야 합니다.
- 스타터 플랜 네트워크에서 체인코드를 설치하고 인스턴스화하여 이 체인코드를 테스트할 수 있습니다. 스타터 플랜에서 작동되는 모든 체인코드는 네트워크 업그레이드 후에 엔터프라이즈 플랜에서도 작동됩니다.

다음 단계를 수행하여 체인코드를 업데이트하십시오.
1. Golang 벤더링 도구를 사용하여 체인코드를 업데이트하십시오. 원래 파일에 종속 항목을 포함하는 데 사용된 동일한 도구를 사용하는 것이 가장 쉽습니다. 예를 들어, 체인코드가 다수의 Fabric 샘플이 사용하는 **govendor** 도구를 사용하는 경우 벤더 폴더 위에 있는 디렉토리에서 다음 명령을 실행하여 체인코드 종속 항목을 업데이트할 수 있습니다.
    ```
    govendor update all +v
    ```
    {:codeblock}

    그런 다음 `go build`를 사용하여 새 코드가 컴파일하는지 여부와 체인코드 업데이트가 작동하는지 여부를 확인할 수 있습니다.

2. 네트워크 업그레이드 후에 네트워크 모니터에서 [체인코드를 업데이트](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode)할 수 있습니다.

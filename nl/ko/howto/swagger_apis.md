---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Swagger에서 API 시험 사용

{{site.data.keyword.blockchainfull}} Platform은 애플리케이션 개발이 용이하도록 다수의 REST API를 노출합니다. Swagger UI를 사용하여 블록체인 네트워크에 대해 테스트할 수 있습니다.
{:shortdesc}

시작하기 전에 {{site.data.keyword.Bluemix_notm}}에서 [{{site.data.keyword.blockchain}} Platform 서비스 인스턴스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)를 작성하고 스타터 플랜 <!--or Enterprise Plan -->블록체인 네트워크를 작성하거나 가입해야 합니다.


## 네트워크 신임 정보 검색

블록체인 네트워크의 네트워크 모니터에 들어가 왼쪽 네비게이터에서 "API" 화면을 여십시오. REST API에 대한 네트워크 신임 정보를 볼 수 있습니다. 나중에 여기에 표시된 "키" 및 "시크릿"의 값을 사용하여 API에 권한을 부여하고 "network_id"의 API를 매개변수로 실행하십시오. **시크릿 표시**를 클릭하여 시크릿 필드의 값을 표시하십시오. 키, 시크릿 및 network_id 필드의 값을 복사하십시오. 이러한 값은 나중에 Swagger UI에서 사용할 수 있습니다.

<!-- Removing this code snippet so people don't try to use these values
```
},
   "x-api": {
       "url": "https://ibmblockchain.bluemix.net",
       "key": "PeerOrg1",
       "network_id": "e1f5b3341b1d483bbaf829f601144023",
       "secret": "71a329aabde9ff20de0aa4bfafd72a4466d78c87f637e7ff92c2534b5ce81cc0"
   }
```
-->

**그림 1**은 "API" 화면을 보여줍니다.
![개요 화면](../images/restAPI.png)
*그림 1. API*

스타터 플랜을 사용하는 경우 네트워크 모니터에서 조직 간에 전환할 수 있습니다. 스타터 플랜에서는 기본적으로 두 개의 조직이 구성됩니다. 조직 간 전환은 각 조직의 퍼스펙티브에서 REST API를 시험 사용할 때 유용합니다. 네트워크에 있는 다른 조직의 신임 정보를 가져오려면 네트워크 모니터 콘솔의 오른쪽 상단 구석에 있는 사용자 이름을 클릭하십시오. 열려 있는 메뉴에서 모든 조직을 보려면 조직 옆에 드롭 다운 화살표를 클릭하십시오. 전환하여 연관된 네트워크 신임 정보를 보려는 조직을 선택하십시오.

**그림 2**는 조직 간 전환 방법을 보여줍니다.
![조직 간 전환](../images/restAPIOrganization.png)
*그림 2. 조직 간 전환*


## Swagger API 권한 부여

"API" 화면에서 **Swagger UI** 링크를 클릭하여 Swagger UI를 여십시오.  
<!-- remove this line because the link is different depending on if you are starter or enterprise plan
You can also open the Swagger UI with the URL in the connection profiles. For example, `http://blockchain-swagger-dev.stage1.mybluemix.net`.
-->

Swagger UI에서 **권한 부여** 단추를 클릭하면 권한 부여 창이 팝업됩니다. 네트워크 신임 정보에 "키" 및 "시크릿"의 값을 사용자 이름 및 비밀번호로 입력하고 **권한 부여** 및 **완료**를 차례로 클릭하십시오. 이제 API를 실행할 준비가 되었습니다. 브라우저를 새로 고치면 신임 정보를 사용하여 다시 권한 부여해야 합니다.

기본 권한 인증을 사용하는 경우, **권한 부여** 및 **완료** 단추를 차례로 클릭하면 권한 부여 창에서 지정한 신임 정보가 저장되고 각 REST API 호출에 전달됩니다.

**그림 3**은 "권한 부여" 팝업 창을 보여줍니다.
![권한 부여 팝업 창](../images/swaggerUIAuthorize.png)
*그림 3. 권한 부여 팝업 창*


## API 시험 사용

실행할 REST API를 클릭하고 **시험 사용** 단추를 클릭하십시오. 필수 매개변수를 입력하고 **실행**을 클릭하십시오. REST API 호출은 네트워크에 대해 실행됩니다.

**그림 4**는 "Swagger UI"를 보여줍니다.
![Swagger UI](../images/swaggerUITryItOut.png)
*그림 4. Swagger UI*


## 문제점 해결 팁

### 401 권한 없음  
  네트워크 신임 정보를 제공하여 REST API에 권한을 부여했는지 확인하십시오. 자세한 정보는 [Swagger API 권한 부여](#authorizing-swagger-apis)를 참조하십시오.

### 400 오류: 잘못된 요청
  일부 API는 특정 피어에 대한 결과만 표시하도록 필터의 역할을 하는 인수를 요청의 본문에 사용할 수 있습니다. 샘플 스니펫은 본문에 제공되며, 사용되는 경우 필터링할 피어 또는 피어의 목록을 지정하도록 편집되어야 합니다. 이 오류를 방지하려면 스니펫을 편집하여 네트워크에 피어를 지정하거나 스니펫을 완전히 제거하십시오.

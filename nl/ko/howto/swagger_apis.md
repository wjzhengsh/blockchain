---

copyright:
  years: 2018,2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Swagger API를 사용하여 네트워크와 상호작용
{: #ibp-swagger}

{{site.data.keyword.blockchainfull_notm}} Platform은 네트워크의 노드, 채널, 피어 및 구성원을 관리하는 데 사용할 수 있는 Swagger의 여러 REST API를 노출합니다. 애플리케이션은 이러한 API를 사용하여 네트워크 모니터를 사용하지 않고 중요한 네트워크 리소스를 제어할 수 있습니다.

{:shortdesc}

시작하기 전에 {{site.data.keyword.cloud_notm}}에서 [{{site.data.keyword.blockchain}} Platform 서비스 인스턴스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod)를 작성하고 스타터 플랜 <!--or Enterprise Plan -->블록체인 네트워크를 작성하거나 참여해야 합니다.


## 네트워크 인증 정보 검색
{: #ibp-swagger-retrieving-network-credentials}

블록체인 네트워크의 네트워크 모니터에 들어가 왼쪽 네비게이터에서 "API" 화면을 여십시오. REST API에 대한 네트워크 인증 정보를 볼 수 있습니다. 나중에 여기에 표시된 "키" 및 "시크릿"의 값을 사용하여 API에 권한을 부여하고 "network_id"의 API를 매개변수로 실행하십시오. **시크릿 표시**를 클릭하여 시크릿 필드의 값을 표시하십시오. 키, 시크릿 및 network_id 필드의 값을 복사하십시오. 이러한 값은 나중에 Swagger UI에서 사용할 수 있습니다.

**그림 1**은 "API" 화면을 보여줍니다.
![API 화면](../images/API_screen_starter.png "API 화면")
*그림 1. API*

스타터 플랜을 사용하는 경우 네트워크 모니터에서 조직 간에 전환할 수 있습니다. 스타터 플랜에서는 기본적으로 두 개의 조직이 구성됩니다. 조직 간 전환은 각 조직의 관점에서 REST API를 시험 사용할 때 유용합니다. 네트워크에 있는 다른 조직의 인증 정보를 가져오려면 네트워크 모니터 콘솔의 오른쪽 상단 구석에 있는 사용자 이름을 클릭하십시오. 열려 있는 메뉴에서 모든 조직을 보려면 조직 옆에 드롭 다운 화살표를 클릭하십시오. 전환하여 연관된 네트워크 인증 정보를 보려는 조직을 선택하십시오.

**그림 2**는 조직 간 전환 방법을 보여줍니다.

![조직 간 전환](../images/switch_orgs_starter.gif "조직 간 전환")  
*그림 2. 조직 간 전환*


## Swagger API 권한 부여
{: #ibp-swagger-authorizing-swagger}

"API" 화면에서 **Swagger UI** 링크를 클릭하여 Swagger UI를 여십시오.  

Swagger UI에서 **권한 부여** 단추를 클릭하면 권한 부여 창이 팝업됩니다. 네트워크 인증 정보에 "키" 및 "시크릿"의 값을 사용자 이름 및 비밀번호로 입력하고 **권한 부여** 및 **완료**를 차례로 클릭하십시오. 이제 API를 실행할 준비가 되었습니다. 브라우저를 새로 고치면 인증 정보를 사용하여 다시 권한 부여해야 합니다.

기본 권한 인증을 사용하는 경우, **권한 부여** 및 **완료** 단추를 차례로 클릭하면 권한 부여 창에서 지정한 인증 정보가 저장되고 각 REST API 호출에 전달됩니다.

**그림 3**은 Swagger API에 권한을 부여하는 프로세스를 보여줍니다.

![API에 권한 부여](../images/swaggerUIAuthorize.gif "API에 권한 부여")  
*그림 3. API에 권한 부여*


## API 시험 사용
{: #ibp-swagger-try-out}

실행할 REST API를 클릭하고 **시험 사용** 단추를 클릭하십시오.

**그림 4**는 "Swagger UI"의 "시험 사용" 단추를 보여줍니다.

![Swagger UI의 시험 사용 단추](../images/swaggerUITryItOut.png "Swagger UI의 시험 사용 단추")  
*그림 4. "Swagger UI"의 "시험 사용" 단추*

**시험 사용** 단추를 클릭한 후 필수 매개변수를 입력하여 API를 사용할 수 있습니다. 네트워크 인증 정보에서 `networkID`를 찾고 네트워크 모니터에서 다른 매개변수를 찾을 수 있습니다. 매개변수를 입력한 후 **실행**을 클릭하여 네트워크에 대해 REST API 호출을 실행하십시오.

**그림 5**는 "Swagger UI"의 매개변수를 보여줍니다.

![Swagger UI의 매개변수](../images/swaggerUIParams.png "Swagger UI의 매개변수")  
*그림 5. 엔터프라이즈 매개변수*  

**실행**을 클릭하면 네트워크에 대해 API 호출의 응답을 볼 수 있습니다. 또한 명령행에서 직접 API를 호출할 수 있는 CURL 명령을 볼 수 있습니다.

**그림 6**은 API 응답 본문, URL 및 CURL 명령을 보여줍니다.

![Swagger UI의 API 응답](../images/swaggerUICurlResponse.png "Swagger UI의 API 응답")  
*그림 6. API 응답*    

## API 액세스 사용 안함
{: #ibp-swagger-turn-off}

기본적으로 IBM Cloud에서 감사자 역할을 제외한 모든 사용자는 Swagger API 패널에 표시되는 **네트워크 인증 정보**를 보고 사용할 수 있으며, 이에 따라 API를 사용하여 네트워크를 관리할 수 있습니다. 그러나 UI에서 Swagger API 네트워크 인증 정보를 노출하지 않으려면 기존 키 및 시크릿 값을 복사하고 보안 설정하며 Swagger API에 사용할 수 없는 새 인증서를 생성할 수 있습니다. 다음 단계를 완료하여 액세스를 제어할 수 있도록 resetCredentials라는 플래그가 제공됩니다. 

1. [서비스 인증 정보 대시보드](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token)에 설명된 대로 새 네트워크 인증 정보를 생성하려면 다음 단계를 따르십시오.
2. 그러나 **인라인 구성 매개변수 추가** 상자에 다음 값을 붙여넣으십시오. 
   ```
   {
     "resetCredentials": true
   }
   ```
   {:codeblock}
3. **추가**를 클릭하십시오.

이제 사용자가 UI의 Swagger API 패널에 액세스하면 UI의 **네트워크 인증 정보**에는 네트워크 관리에 사용할 수 없는 일반 키 및 시크릿 값이 포함됩니다. 해당 인증 정보를 사용하여 제출된 API 요청은 처리되지 않습니다.   

나중에 UI에서 올바른 네트워크 인증 정보를 노출하려는 경우 새 인증 정보를 생성하려면 위의 단계만 반복하면 되지만 **인라인 구성 매개변수 추가** 상자의 경우에는 상자를 비어 있는 상태로 둘 수 있습니다. 매개변수를 지정할 필요가 없습니다.

이제 원래의 유효한 인증 정보가 UI의 **새 인증 정보**에 표시되며 Swagger API를 인증하는 데 사용될 수 있습니다. 

## 문제점 해결 팁
{: #ibp-swagger-troubleshooting}

### 401 권한 없음  
{: #ibp-swagger-401}

  네트워크 인증 정보를 제공하여 REST API에 권한을 부여했는지 확인하십시오. 자세한 정보는 [Swagger API 권한 부여](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-authorizing-swagger)를 참조하십시오.

### 400 오류: 잘못된 요청
{: #ibp-swagger-400}

  일부 API는 특정 피어에 대한 결과만 표시하도록 필터의 역할을 하는 인수를 요청의 본문에 사용할 수 있습니다. 샘플 스니펫이 본문에 제공되며, 사용되는 경우 필터링할 피어 또는 피어의 목록을 지정하도록 편집되어야 합니다. 이 오류를 방지하려면 스니펫을 편집하여 네트워크에 피어를 지정하거나 스니펫을 완전히 제거하십시오.

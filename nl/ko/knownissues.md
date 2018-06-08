---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-29"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# 알려진 문제

다음 문제가 이미 보고되었습니다.
- 외부 CA 구성이 아직 지원되지 않습니다.  그 대안으로 네트워크 모니터를 통해 관리자 인증서를 생성해서 업로드할 수 있습니다. 자세한 정보는 [클라이언트 측 인증서 생성](v10_application.html#generating-the-client-side-certificates) 및 네트워크 모니터에 있는 ["구성원" 화면의 "인증서" 탭](v10_dashboard.html#members)에 있는 설명을 참조하십시오.
- 스타터 플랜 네트워크의 네트워크 모니터에서 "개요" 화면에 나열된 노드에 대한 **로그 보기**를 클릭하면 {{site.data.keyword.cloud}} 로깅 Kibana 인터페이스가 열립니다. 기본적으로 Kibana는 최근 15분 동안의 활동에 대한 로그를 표시하도록 사전 구성됩니다. 최근 15분 동안 활동이 없는 경우 '결과를 찾을 수 없음'이라는 메시지가 표시됩니다. 오른쪽 상단 모서리에 있는 사용자 이름 아래의 타이머 아이콘을 클릭하고 보다 넓은 시간 범위(예: '이번 주' 또는 '이번 달')를 설정하여 다른 로그를 볼 수 있습니다.
- 프록시 중단과 같은 안정성 문제가 발생할 수 있으며 다시 시작해야 합니다. 다시 시작하기 전에 애플리케이션의 Fabric SDK에서 기본 제한시간 값을 더 큰 수로 겹쳐쓰십시오. 제한시간 값 설정에 대한 자세한 정보는 [Fabric SDK에서 제한시간 값 설정](v10_application.html#set-timeout-in-sdk)을 참조하십시오.

{{site.data.keyword.Bluemix}}의 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 대한 지원 및 도움은 [지원 받기](ibmblockchain_support.html)를 참조하십시오.

<!--
## Updating chaincode with Enterprise Plan migration to Hyperledger Fabric 1.1
-	Users who migrate from networks based on Hyperledger Fabric 1.0 to networks based on Fabric 1.1 will need to update the dependencies in their chaincode. If they do not, there is a risk of a service disruption.
- This does not apply to users that uploaded their chaincode without dependencies, using a .go file.
- **Update your chaincode using the following steps:**
  **1.** You can use any  golang vendoring tool to update your chaincode. It will be easiest to use the same tool that was used to include dependencies in the original file. Many early Fabric samples used the govendor tool. If your chaincode used govendor, you can update your dependencies using the following command in the directory above the vendor folder.
      govendor update all +v
  * You can use `go build` to check that the new code compiles and that the update worked.
  * You can test your chaincode by installing and instantiating it on a Starter Plan Network. All chaincode that works on Starter Plan will also work on Enterprise Plan after the upgrade.
  **2.** Once your chaincode dependencies are up to date, you can use the network monitor to [update your chaincode](install_instatiate_chaincode.html#Updating a chaincode).
-->

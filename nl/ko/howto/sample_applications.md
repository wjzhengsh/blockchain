---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 샘플 애플리케이션

{{site.data.keyword.blockchainfull}} 네트워크 및 애플리케이션 개발에 대해 더 잘 이해할 수 있도록 샘플 애플리케이션을 배치하고 테스트할 수 있습니다.
{:shortdesc}

## 시작하기 전에

사용자의 로컬 파일 시스템에 모든 소프트웨어 전제조건을 설치했는지 확인하십시오. 자세한 정보는 [애플리케이션 개발 환경 설정](../v10_application.html#setting-up-application-development-environment)을 참조하십시오.

또한, {{site.data.keyword.blockchain}} 네트워크가 {{site.data.keyword.Bluemix_short}}에 채널 및 해당 피어와 함께 구성되어 있어야 합니다. 자세한 정보는 [네트워크 통제](../get_start.html)를 참조하십시오. 네트워크가 사용할 준비가 되었으면 사용자의 애플리케이션이 액세스할 네트워크 리소스의 API 엔드포인트를 검색하십시오.  자세한 정보는 [애플리케이션에 네트워크 서비스 신임 정보 추가](../v10_application.html#adding-network-service-credentials-to-your-application)를 참조하십시오.


## Marbles 애플리케이션

Marbles 애플리케이션에서는 여러 사용자가 각기 다른 특성으로 marbles를 작성하고 다른 사용자와 함께 전송할 수 있습니다. Marbles 애플리케이션은 Javascript로 작성되고 체인코드는 Go로 작성됩니다.

[GitHub의 Marbles ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM-Blockchain/marbles)에서 샘플 코드 및 지시사항을 찾을 수 있습니다.


## Fabric car 애플리케이션

Fabric car 애플리케이션에서는 원장의 차량 레코드에 대한 **조회** 및 **업데이트**를 수행할 수 있습니다. Fabric car 애플리케이션은 Javascript로 작성되고 체인코드는 Go로 작성됩니다.

[GitHub의 Fabric car ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar)에서 샘플 코드를 찾고, [Writing Your First Application ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html)에서 지시사항을 찾을 수 있습니다.

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 새로운 기능
{: #whatsnew}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 2018년 12월 7일

*스타터 플랜 네트워크 운영* 및 *엔터프라이즈 플랜 네트워크 운영* 주제는 [네트워크 모니터 사용](/docs/services/blockchain/v10_dashboard.html)에 대한 하나의 튜토리얼을 통해 결합되고 대체됩니다. 

## 2018년 11월 27일

{{site.data.keyword.blockchainfull}} Platform은 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private(ICP)을 릴리스합니다. ICP는 Kubernetes를 기반으로 하는 컨테이너화된 애플리케이션을 개발하고 관리하기 위한 애플리케이션 플랫폼이며 이를 통해 사용자는 x86, LinuxONE 및 IBM Z에 인증 기관(CA), 순서 지정자 및 피어를 배치할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform for ICP는 Hyperledger Fabric v1.2.1을 기반으로 하고 Kubernetes Helm 차트를 사용하여 배치됩니다. Helm 차트를 설치하고 나면 ICP 카탈로그에서 번들화된 서비스로 이를 찾을 수 있습니다. [이 오퍼링](/docs/services/blockchain/ibp-for-icp-about.html)은 경험이 많은 Fabric 사용자에게 좀 더 적합합니다. 

ICP에 대한 정보는 [{{site.data.keyword.cloud_notm}} Private v3.1.0 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0 문서")를 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform for ICP의 릴리스도 IBM Blockchain Platform Remote Peer(베타) 프로그램의 종료 지점을 표시합니다. 계속해서 ICP 또는 AWS에 피어를 배치하고 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 이를 연결할 수 있습니다. 자세한 정보는 [스타터 또는 엔터프라이즈 플랜에 대한 피어 배치](/docs/services/blockchain/howto/peer_deploy_ibp.html) 및 [AWS에 피어 배치](/docs/services/blockchain/howto/remote_peer_aws.html)를 참조하십시오. 이 피어는 배치가 고객 관리이고 이에 따라 원격인 중심 위치가 없으므로 원격 피어 대신 **분배된** 피어인 것으로 간주됩니다. 

이 릴리스에서는 문서 목차에 대한 일부 개선사항도 공개합니다. 스타터 또는 엔터프라이즈 사용자인 경우 컨텐츠는 계속해서 **LEARN**, **HOW TO**, **REFERENCE** 및 **HELP** 섹션에서 찾을 수 있으며 링크는 계속해서 동일합니다. 목차의 다른 하위 섹션에 있을 수 있습니다. 

## 2018년 11월 13일

{{site.data.keyword.blockchainfull_notm}} Platform은 {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services(AWS)를 릴리스합니다. 

{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 통해 AWS Cloud에서 피어를 실행하고 피어를 {{site.data.keyword.cloud_notm}}의 기존 블록체인 네트워크에 연결할 수 있습니다. AWS의 피어는 {{site.data.keyword.cloud_notm}} 외부의 기타 애플리케이션과 피어를 같은 위치에 배치할 수 있는 더 많은 유연성을 제공하면서 기존 네트워크에서 연결 프로파일과 기타 블록체인 컴포넌트를 활용할 수 있습니다. 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services 정보](/docs/services/blockchain/howto/remote_peer.html)를 참조하십시오.

## 2018년 10월 4일

{{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Fabric v1.1.0에서 v1.2.1로 스타터 플랜을 업그레이드합니다. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html)를 참조하십시오.

**중요:** Fabric v1.2.1은 현재 v1.1.0 피어를 사용하는 원격 피어 베타와 호환 가능하지 않습니다. 2018년 10월 4일에 작성되었으며 Fabric v1.1.0을 기반으로 하는 스타터 플랜 네트워크는 영향을 받지 않고 계속해서 원격 피어 베타로 사용될 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform은 Fabric v1.2.1 레벨에 있는 새 스타터 네트워크와 Fabric v1.1.0 레벨에 있는 엔터프라이즈 네트워크와 호환 가능한 v1.2.1 피어를 사용하도록 원격 피어 베타를 업데이트합니다. 원격 피어 베타가 Fabric v1.2.1로 업데이트될 때까지 새 v1.2.1 스타터 네트워크로 a v1.1.0 원격 피어를 배치하지 않도록 하십시오. 

## 2018년 9월 4일

{{site.data.keyword.blockchainfull_notm}} Platform은 원격 피어 오퍼링 베타를 릴리스합니다. 원격 피어 오퍼링은 Hyperledger Fabric v1.1.0을 기반으로 합니다. 원격 피어를 사용하여 고유한 IBM Cloud Private(ICP) 또는 Amazon Web Services(AWS) 클라우드 환경에 {{site.data.keyword.blockchainfull_notm}} Platform 피어 노드를 실행할 수 있습니다. 자세한 정보는 [원격 피어 정보](/docs/services/blockchain/howto/remote_peer.html)를 참조하십시오.

## 2018년 6월 15일

{{site.data.keyword.blockchainfull_notm}} Platform은 스타터 플랜 GA를 릴리스합니다. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html)를 참조하십시오.

## 2018년 5월 15일

{{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Fabric v1.0.0에서 v1.1.0로 엔터프라이즈 플랜을 업그레이드합니다. 자세한 정보는 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html)를 참조하십시오.

## 2018년 3월 18일

{{site.data.keyword.blockchainfull_notm}} Platform은 스타터 플랜 베타를 릴리스합니다. 스타터 플랜은 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에서 학습하고 연습하기 위한 개발 및 테스트 환경을 제공합니다. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html)를 참조하십시오.

## 2017년 8월 11일

{{site.data.keyword.blockchainfull_notm}} Platform은
{{site.data.keyword.blockchainfull_notm}} on Cloud를 대체하고 엔터프라이즈 플랜을 릴리스합니다. 자세한 정보는 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html)를 참조하십시오.

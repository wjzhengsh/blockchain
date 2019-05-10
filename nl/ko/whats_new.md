---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# 새로운 기능
{: #whats-new}

## 2019년 4월 23일
{: #whats-new-4-17-2019}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2가 릴리스되었습니다. 이 릴리스는 Hyperledger Fabric v1.4.0 코드 베이스를 사용하고 {{site.data.keyword.cloud_notm}} Private v3.1.2에 대한 배치를 지원합니다.

기존 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private을 v1.0.2로 업그레이드하려면 [{{site.data.keyword.cloud_notm}} Private의 Helm 차트 업그레이드](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading)를 참조하십시오.

Hyperledger Fabric v1.4.0에 대한 자세한 정보는 [Hyperledger Fabric 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/ "Hyperledger Fabric v1.4 문서")를 참조하십시오. {{site.data.keyword.cloud_notm}} Private에 대한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private v3.1.2 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.2 문서")를 참조하십시오. 

## 2019년 2월 8일
{: #whats-new-2-08-2019}

{{site.data.keyword.blockchainfull}} Platform에서는 블록체인 네트워크를 배치, 작동 및 모니터링할 수 있도록 해주는 차세대 {{site.data.keyword.blockchainfull_notm}} Platform 솔루션인 무료 2.0 베타를 릴리스합니다. {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타에는 {{site.data.keyword.cloud_notm}}의 {{site.data.keyword.IBM_notm}} Kubernetes Service 클러스터에서 블록체인 컴포넌트를 배치하고 관리하는 데 사용될 수 있는 새 사용자 인터페이스 콘솔이 포함됩니다. {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타는 다음과 같은 기능을 제공합니다.

**원활한 경험을 통해 네트워크를 더 빠르고 쉽게 구축**

*	스마트 계약 개발(VS 코드) 및 네트워크 관리 간의 원활한 통합
*	단순화된 DevOps를 사용하면 개발에서 테스트까지 단일 콘솔에서 프로덕션으로 이동할 수 있음
*	Javascript, Java 및 Go 언어로 스마트 계약 작성 지원

**완전한 제어 기능으로 네트워크 작동 및 관리**

*	필요한 블록체인 컴포넌트(피어, 순서 지정 서비스, 인증 기관)만 배치
*	재구성된 콘솔을 사용하여 배치된 위치에 상관없이 한 장소에서 네트워크 컴포넌트를 관리할 수 있음
*	ID, 원장 및 스마트 계약의 완전 제어 유지

**새로 도입된 다중 클라우드 분산 유연성을 사용하는 편리한 분산 네트워크 증가**

*	온프레미스, 공용, 하이브리드 클라우드 등의 모든 환경에서 실행 중인 노드에 연결
*	단일 피어를 다중 업계 네트워크에 쉽게 연결
*	소규모로 시작하여 선불 투자 없이 사용량 증가에 따라 지불하고 Kubernetes를 통해 쉽게 업그레이드 가능

- {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform 2.0 정보](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview)에서 제공합니다.
- 무료 2.0 베타 릴리스를 {{site.data.keyword.IBM_notm}} Kubernetes Service 클러스터에 배치하는 방법에 대한 지시사항은 [{{site.data.keyword.blockchainfull_notm}} Platform 2.0 시작하기](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)에서 제공합니다.
- {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타 사용에 대한 새 튜토리얼은 **HOW TO** 카테고리 아래의 **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** 하위 섹션에서 사용 가능합니다.
  * [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은 순서 지정자 또는 피어를 작성하여 네트워크를 호스팅하는 프로세스를 안내합니다.
  * [네트워크 참여 튜토리얼](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)은 피어를 작성하고 해당 피어를 채널에 가입시켜 기존 네트워크에 참여하는 방법을 설명합니다.
  * [네트워크에 스마트 계약 배치](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)는 스마트 계약을 작성하여 이를 네트워크에 배치하는 방법을 제공합니다.
- {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타 오퍼링은 Hyperledger Fabric v1.4을 기반으로 하며 피어 투 피어 gossip, 서비스 발견 및 개인용 데이터를 지원합니다. 네트워크에서 개인용 데이터를 구성하는 방법을 학습하려면 이 [주제](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)를 방문하십시오.

- {{site.data.keyword.blockchainfull_notm}} Visual Studio Code 확장은 Visual Studio Code 마켓플레이스에서 사용 가능합니다. 개발자는 확장을 사용하여 Hyperledger Fabric의 인스턴스에 스마트 계약을 작성, 테스트 및 배치할 수 있습니다. 확장은 Hyperledger Fabric 1.3 이상과 호환 가능합니다. VS Code 확장에 대한 정보는 [스마트 계약을 위한 도구](/docs/services/blockchain/vscode-extension.html#develop-vscode)를 참조하십시오.  

모든 시작하기 주제를 **시작하기 튜토리얼**이라는 섹션으로 함께 분류하여 목차가 강화되었습니다. 또한 각 {{site.data.keyword.blockchainfull_notm}} Platform 오퍼링에 대해 **{{site.data.keyword.blockchainfull_notm}} Platform 정보** 하위 섹션에서 설명하고 **학습** 섹션 아래에 배치했습니다.

**참고:** 스타터 플랜의 사용자는 더 이상 무료 클라우드 크레딧을 사용할 수 없습니다.

## 2018년 12월 7일
{: #whats-new-12-7-2018}

*스타터 플랜 네트워크 운영* 및 *엔터프라이즈 플랜 네트워크 운영* 주제는 [네트워크 모니터 사용](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard)에 대한 하나의 튜토리얼을 통해 결합되고 대체됩니다.

## 2018년 11월 27일
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private을 릴리스합니다. {{site.data.keyword.cloud_notm}} Private은 Kubernetes를 기반으로 하는 컨테이너화된 애플리케이션을 개발하고 관리하기 위한 애플리케이션 플랫폼이며 이를 통해 사용자는 x86, LinuxONE 및 IBM Z에 인증 기관(CA), 순서 지정자 및 피어를 배치할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private은 Hyperledger Fabric v1.2.1을 기반으로 하며 Kubernetes Helm 차트를 사용하여 배치됩니다. Helm 차트를 설치하고 나면 {{site.data.keyword.cloud_notm}} Private 카탈로그에서 번들화된 서비스로 이를 찾을 수 있습니다. [이 오퍼링](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about)은 경험이 많은 Fabric 사용자에게 좀 더 적합합니다.

{{site.data.keyword.cloud_notm}} Private에 대한 정보는 [{{site.data.keyword.cloud_notm}} Private v3.1.0 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0 문서")를 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private의 릴리스도 IBM Blockchain Platform Remote Peer(베타) 프로그램의 종료 지점을 표시합니다. 계속해서 {{site.data.keyword.cloud_notm}} Private 또는 AWS에 피어를 배치하고 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 이를 연결할 수 있습니다. 자세한 정보는 [스타터 또는 엔터프라이즈 플랜에 대한 피어 배치](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) 및 [AWS에 피어 배치](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws)를 참조하십시오. 이 피어는 배치가 고객 관리이고 이에 따라 원격인 중심 위치가 없으므로 원격 피어 대신 **분배된** 피어인 것으로 간주됩니다.

이 릴리스에서는 문서 목차에 대한 일부 개선사항도 공개합니다. 스타터 또는 엔터프라이즈 사용자인 경우 컨텐츠는 계속해서 **LEARN**, **HOW TO**, **REFERENCE** 및 **HELP** 섹션에서 찾을 수 있으며 링크는 계속해서 동일합니다. 목차의 다른 하위 섹션에 있을 수 있습니다.

## 2018년 11월 13일
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services(AWS)를 릴리스합니다.

{{site.data.keyword.blockchainfull_notm}} Platform for AWS를 통해 AWS Cloud에서 피어를 실행하고 피어를 {{site.data.keyword.cloud_notm}}의 기존 블록체인 네트워크에 연결할 수 있습니다. AWS의 피어는 {{site.data.keyword.cloud_notm}} 외부의 기타 애플리케이션과 피어를 같은 위치에 배치할 수 있는 더 많은 유연성을 제공하면서 기존 네트워크에서 연결 프로파일과 기타 블록체인 컴포넌트를 활용할 수 있습니다. 자세한 정보는
[{{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services 정보](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws)를 참조하십시오.
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 2018년 10월 4일
{: #whats-new-10-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Fabric v1.1.0에서 v1.2.1로 스타터 플랜을 업그레이드합니다. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html#starter-plan-about)를 참조하십시오.

**중요:** Fabric v1.2.1은 현재 v1.1.0 피어를 사용하는 원격 피어 베타와 호환 가능하지 않습니다. 2018년 10월 4일에 작성되었으며 Fabric v1.1.0을 기반으로 하는 스타터 플랜 네트워크는 영향을 받지 않고 계속해서 원격 피어 베타로 사용될 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform은 Fabric v1.2.1 레벨에 있는 새 스타터 네트워크와 Fabric v1.1.0 레벨에 있는 엔터프라이즈 네트워크와 호환 가능한 v1.2.1 피어를 사용하도록 원격 피어 베타를 업데이트합니다. 원격 피어 베타가 Fabric v1.2.1로 업데이트될 때까지 새 v1.2.1 스타터 네트워크로 a v1.1.0 원격 피어를 배치하지 않도록 하십시오.

## 2018년 9월 4일
{: #whats-new-9-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 원격 피어 오퍼링 베타를 릴리스합니다. 원격 피어 오퍼링은 Hyperledger Fabric v1.1.0을 기반으로 합니다. 원격 피어를 사용하여 고유한 {{site.data.keyword.cloud_notm}} Private 또는 Amazon Web Services(AWS) 클라우드 환경에 {{site.data.keyword.blockchainfull_notm}} Platform 피어 노드를 실행할 수 있습니다. 자세한 정보는 [원격 피어 정보](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws)를 참조하십시오.

## 2018년 6월 15일
{: #whats-new-6-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 스타터 플랜 GA를 릴리스합니다. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html#starter-plan-about)를 참조하십시오.

## 2018년 5월 15일
{: #whats-new-5-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Fabric v1.0.0에서 v1.1.0로 엔터프라이즈 플랜을 업그레이드합니다. 자세한 정보는 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)를 참조하십시오.

## 2018년 3월 18일
{: #whats-new-3-18-2018}

{{site.data.keyword.blockchainfull_notm}} Platform은 스타터 플랜 베타를 릴리스합니다. 스타터 플랜은 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에서 학습하고 연습하기 위한 개발 및 테스트 환경을 제공합니다. 자세한 정보는 [스타터 플랜 정보](/docs/services/blockchain/starter_plan.html#starter-plan-about)를 참조하십시오.

## 2017년 8월 11일
{: #whats-new-8-11-2017}

{{site.data.keyword.blockchainfull_notm}} Platform은
{{site.data.keyword.blockchainfull_notm}} on Cloud를 대체하고 엔터프라이즈 플랜을 릴리스합니다. 자세한 정보는 [엔터프라이즈 플랜 정보](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)를 참조하십시오.

---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

keywords: blockchain components, ca, certificate authorities, peer, ordering service, orderer, channel, smart contract, applications

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# 블록체인 컴포넌트 개요
{: #blockchain-component-overview}

{{site.data.keyword.blockchainfull}} Platform의 컴포넌트 및 구조는 {{site.data.keyword.IBM_notm}}이 주도적으로 제공하는 오픈 소스 권한의 블록체인 솔루션인 [Hyperledger Fabric ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/)의 기본 인프라 및 도구를 기반으로 합니다. Fabric 기반의 네트워크에는 다양한 유스 케이스를 지원하기 위해 많은 구성에 배치될 수 있는 여러 표준 컴포넌트가 포함됩니다.

Fabric 네트워크의 더욱 포괄적인 보기와 이를 포함하는 컴포넌트의 연관성에 대해 알아보려면 네트워크 시작과 발전 방법을 보여주는 Fabric 커뮤니티 문서의 [블록체인 네트워크의 구조에 대한 문서](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html)를 참조하십시오.

Fabric을 기반으로 하는 네트워크의 컴포넌트에 대한 상위 레벨 개요는 아래 동영상을 확인하십시오.

<iframe class="embed-responsive-item" id="youtubeplayer" title="스타터 플랜 동영상" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*이 동영상은 스타터 및 엔터프라이즈 네트워크의 관점에서 컴포넌트에 중점을 두고 있으나, 이 정보는 계속해서 {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private의 고객 관리 솔루션과 높은 관련성을 유지합니다.*

이 개요의 목적으로 인증 기관(CA), 순서 지정자, 피어, 스마트 계약 및 애플리케이션에 중점을 둡니다. [{{site.data.keyword.blockchainfull_notm}} Platform 2.0 네트워크 빌드 안내서](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) 및 [{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 배치 안내서](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp) 주제에서 확인할 수 있듯이 이 순서는 임의적이지 않으며, Fabric 기반 네트워크의 컴포넌트가 배치되는 순서를 반영합니다.

## 인증 기관
{: #blockchain-component-overview-ca}

Fabric 기반 블록체인 네트워크의 기본 요소는 ID 및 권한입니다. ID는 CA가 발행하는 x.509 인증서의 양식을 취하고 사용자를 *식별*하는 점에서 신용 카드와 유사하며, 이를 통해 ID에 대한 속성을 포함할 수 있습니다. 그런 다음 이 인증서는 컴포넌트 또는 채널 레벨에서 MSP 폴더에 포함하여 권한에 연결됩니다. 예를 들어, 피어 MSP에는 **admins**라고 하는 MSP 서브폴더가 있습니다. 인증서가 해당 관리자 폴더 내부에 있는 사용자가 피어의 관리자입니다. 이는 해당 피어의 관리자에게 허용된 조치를 수행할 수 있는 기능이 있음을 의미합니다. 피어 내부의 유효성 검증 시스템은 인증서를 서명하여 식별되는 사용자가 관리 조치를 수행하려고 할 때마다 검사를 수행합니다. 인증서가 "admin" 폴더 내에 있는 인증서와 일치합니까? 일치하는 경우 조치를 수행할 수 있습니다. 일치하지 않는 경우 조치를 수행하기 위한 요청이 거부됩니다.

{{site.data.keyword.blockchainfull_notm}} Platform CA는 [Hyperledger Fabric-CA ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.2/ "Hyperledger Fabric CA")를 기반으로 하나 x.509 인증서를 기반으로 하는 PKI를 사용하는 한 다른 CA를 사용하는 것도 가능합니다. 다중 레벨의 CA일 수 있으며 일반적으로 다중 레벨의 CA여야 합니다. 일반적으로 네트워크의 "루트 CA"는 "중간 CA"에 인증서를 제공하는 경우를 제외하고는 노출되지 않으며, 인증서가 사용자 및 컴포넌트에 직접 발행되거나 더 많은 계층의 중간 CA에 발행됩니다. 인증 기관이 ID 및 멤버십을 설정하기 위해 사용되는 방법에 대한 자세한 사항은 [ID 관련 Hyperledger Fabric 문서![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/identity/identity.html) 및 [멤버십 관련 Hyperledger Fabric 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html)를 참조하십시오.

## 순서 지정자
{: #blockchain-component-overview-orderer}

순서 지정 서비스가 보통 네트워크의 "핵심"이라고 하지만 실제로 기능은 상당히 단순합니다. 여기에는 피어에 의해 유효성 검증된 트랜잭션을 블록으로 순서를 지정하고 원장에 작성되도록 피어로 다시 보내기 위한 기능이 포함됩니다. Fabric의 초기 버전에서는 이 기능이 피어 내부에 번들화되었으나 Fabric v1.0부터는 잠재적인 상태 분기(fork)가 발생될 수 있는 일탈을 방지하고 피어 성능을 높일 수 있도록 개별 컴포넌트로 분리되었습니다.

실제 레벨에서 일반적으로 이 순서 지정 기능에는 통칭하여 "순서 지정 서비스"라고 하는 순서 지정자 세트가 필요하지만 테스트 또는 POC 환경에서 단일 노드(SOLO 순서 지정자라고 함)를 사용할 수 있습니다.

## 피어
{: #blockchain-component-overview-peer}

실제 레벨에서 블록체인 네트워크는 주로 피어 노드로(또는 피어 노드로만) 구성됩니다. 피어는 원장 및 스마트 계약([" 체인코드" ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "체인코드 네임스페이스")에 포함됨)을 호스팅하므로 네트워크의 기본 요소입니다. 좀 더 정확하게 피어는 원장의 **인스턴스**를 호스팅하고 스마트 계약의 **인스턴스**를 호스팅합니다. 스마트 계약 및 원장이 네트워크에서 공유된 프로세스와 공유된 정보를 각각 캡슐화하는 데 사용되므로 이러한 피어의 측면은 Fabric 네트워크에서 실제로 수행하는 항목을 이해하는 데 적합한 시작점이 되도록 합니다.

특히 피어에 대해 자세히 알아보려면 Fabric 커뮤니티 문서에서 [this document focusing just on peers![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html)를 참조하십시오.

## 채널
{: #blockchain-component-overview-channels}

채널은 네트워크 구성원 간의 계층과 무관한 통신을 제공하는 메커니즘입니다. 구성원 서브세트 간에 여러 채널이 작성될 수 있으므로 [개인정보 보호를 구현하는 많은 메커니즘 중 하나를 지원 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Hyperledger Fabric을 사용하는 개인 및 기밀 트랜잭션")합니다. 블록체인 네트워크의 데이터는 채널 원장에 저장됩니다. 채널 원장은 채널에 가입한 조직의 피어에서 호스팅됩니다. 채널 및 채널 사용 방법에 대한 자세한 정보는 [Hyperledger Fabric 문서![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channels.html)를 참조하십시오.

## 스마트 계약(Smart contracts)
{: #blockchain-component-overview-smart-contracts}

비즈니스가 서로 거래하기 전에 일부 계약에서 규칙 및 프로세스에 대한 공통된 이해에 도달되고 이를 정의해야 합니다. 이들을 결합하여 이 계약에서는 비즈니스 파트너 간의 모든 상호작용을 통제하는 "비즈니스 모델"을 설계합니다.

동일한 필요성이 블록체인 네트워크에도 존재합니다. 이러한 비즈니스 모델에 대한 업계 용어는 "스마트 계약"이고, Fabric 및 {{site.data.keyword.blockchainfull_notm}} Platform에는 "체인코드"라고 하는 더 큰 구조로 이러한 계약이 포함되며, 비즈니스 로직이 아닌 스마트 계약 호출을 시도하는 사용자의 ID를 유효성 검증하는 기본 인프라가 포함됩니다.

비즈니스에서 계약은 법률 사무소에서 서명되고 작성됩니다. 스마트 계약은 피어에 설치되고 채널에서 "인스턴스화"됩니다.

## 애플리케이션
{: #blockchain-component-overview-applications}

{{site.data.keyword.blockchainfull_notm}} Platform과 같은 Fabric 기반 네트워크의 클라이언트 애플리케이션은 기본 인프라(예: API, SDK 및 스마트 계약)를 활용하여 더 높은 추상화 레벨에서 클라이언트 상호작용(호출 및 조회)을 허용합니다.

애플리케이션이 Fabric 기반 네트워크와 상호작용하는 방법을 보려면 [첫 번째 애플리케이션 작성과 관련된 문서![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "첫 번째 애플리케이션 작성")를 참조하십시오.  또한 Fabric v1.4의 경우, {{site.data.keyword.blockchainfull_notm}} Platform 2.0에서 사용 가능한 영역이 지속적으로 개선되고 있습니다. 자세한 정보는 [애플리케이션 개발![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "애플리케이션 개발")에서 Hyperledger Fabric 주제를 참조하십시오.  계약 클래스와 같은 이러한 직접적인 기능은 Fabric v1.2(Starter Plan 및 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private의 현재 버전)와 호환되지 않지만, 여기에는 개념적 정보와 애플리케이션 디자인의 요소로 사용할 가치가 있고 미래를 대비할 수 있는 기타 유용한 팁들이 포함되어 있습니다.

애플리케이션이 Fabric 기반 네트워크와 상호작용하는 방법을 보려면 [애플리케이션 작성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app)을 참조하십시오.

## 네트워크의 예
{: #blockchain-component-overview-example-network}

**그림 1**은 네 구성원(각각 두 개의 피어를 소유), 암호 ID 자료 분배를 담당하는 인증 기관 및 정책과 네트워크 참가자를 정의하는 순서 지정 서비스로 구성되는 배치된 블록체인 네트워크의 예를 설명합니다. 파란색 채널에는 네트워크 구성원 넷이 모두 포함되어 있고 노란색 채널은 세 구성원(은행 B, C, D)으로만 제한됩니다. 또한, 은행 A는 네트워크 개시자의 역할을 하며 은행 D는 노란색 채널의 컨텍스트에서 구성원으로서만 존재한다는 것을 알 수 있습니다. 마지막으로, 올바르게 서명된 x509 인증서를 소유하고 있는 애플리케이션 또는 사용자는 네트워크에서 피어에 호출을 전송할 수 있습니다.

![블록체인 네트워크](images/blockchain_network_2-01.png "블록체인 네트워크의 예")

*그림 1. 데이터를 격리하기 위해 채널을 활용하는 구성원 넷이 포함된 블록체인 네트워크의 예*

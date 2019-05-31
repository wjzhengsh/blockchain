---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: smart contract, private data, private data collection, anchor peer

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

# 네트워크에 스마트 계약 배치 튜토리얼
{: #ibp-console-smart-contracts}

스마트 계약(체인코드라고도 함)은 블록체인 원장에서 데이터를 읽고 업데이트할 수 있도록 하는 코드입니다. 스마트 계약은 비즈니스 로직을 블록체인 네트워크의 모든 구성원이 승인하고 검증한 실행 가능 프로그램으로 변환할 수 있습니다. 이 튜토리얼은 [샘플 네트워크 튜토리얼 시리즈](#ibp-console-smart-contracts-structure)의 세 번째 부분이며 스마트 계약을 배치하여 블록체인 네트워크에서 트랜잭션을 시작하는 방법에 대해 설명합니다.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform 베타 평가판을 사용 중인 경우 콘솔의 일부 패널이 GA(Generally Available) 서비스 인스턴스에 따라 최신 상태로 유지되는 현재 문서와 일치하지 않을 수 있습니다. 모든 최신 기능의 이점을 얻으려면 지금 [{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} 시작하기](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)의 지시사항에 따라 새 GA 서비스 인스턴스를 프로비저닝하는 것이 좋습니다.
{: important}

**대상 독자:** 이 주제는 블록체인 네트워크를 작성, 모니터링 및 관리할 책임이 있는 네트워크 운영자를 위해 설계되었습니다. 또한 애플리케이션 개발자의 경우 스마트 계약 작성 방법을 참조하는 절에 관심이 있을 수 있습니다.

## 샘플 네트워크 튜토리얼 시리즈
{: #ibp-console-smart-contracts-structure}

세 개의 파트로 구성된 이 튜토리얼 시리즈는 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 콘솔에서 상대적으로 단순한 다중 노드 Hyperledger Fabric 네트워크를 작성한 후 상호 연결하여 네트워크를 Kubernetes 클러스터에 배치하고 스마트 계약을 설치하고 인스턴스화하는 프로세스를 안내합니다.

* [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은 순서 지정자 또는 피어를 작성하여 네트워크를 호스팅하는 프로세스를 안내합니다.
* [네트워크 가입 튜토리얼](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)은 피어를 작성하고 해당 피어를 채널에 가입시켜 기존 네트워크에 가입하는 프로세스를 안내합니다.
* **네트워크에 스마트 계약 배치**: 이 현재 튜토리얼은 스마트 계약을 작성하여 이를 네트워크에 배치하는 방법을 제공합니다.

이러한 튜토리얼의 단계를 사용하여 개발 및 테스트 목적의 한 클러스터에 다중 조직이 있는 네트워크를 빌드할 수 있습니다. 순서 지정자 노드를 작성하고 조직을 추가하여 블록체인 컨소시엄을 구성하려는 경우 **네트워크 빌드** 튜토리얼을 참조하십시오. 피어를 네트워크에 연결하려면 **네트워크 가입** 튜토리얼을
사용하십시오. 다른 컨소시엄 구성원과 함께 튜토리얼에 따라 **분산** 블록체인 네트워크를 작성할 수 있습니다.  

이 최종 튜토리얼은 스마트 계약을 작성하고 패키지하는 방법, 피어에 스마트 계약을 설치하는 방법, 채널에서 스마트 계약을 인스턴스화하는 방법을 보여줍니다.  

스마트 계약은 피어에 설치된 다음 채널에서 인스턴스화됩니다. **스마트 계약을 사용하여 트랜잭션을 제출하거나 데이터를 읽으려는 모든 구성원은 피어에 계약을 설치해야 합니다.** 스마트 계약은 해당 이름과 버전으로 정의됩니다. 따라서 설치된 계약의 이름과 버전은 스마트 계약을 실행할 예정인 채널의 전체 피어에서 일관되어야 합니다.

스마트 계약을 피어에 설치하고 나면 단일 네트워크 구성원이 채널에서 계약을 인스턴스화합니다. 이 조치를 수행하려면 네트워크 구성원이 채널에 가입해야 합니다. 인스턴스화를 수행하면 스마트 계약에서 사용한 초기 데이터로 원장을 업데이트한 다음 계약이 설치된 채널에 가입한 피어에서 스마트 계약 컨테이너를 시작합니다. 그런 다음 피어에서는 실행 컨테이너를 사용하여 트랜잭션을 수행할 수 있습니다.  
- **한 네트워크 구성원만 스마트 계약을 인스턴스화하면 됩니다.**
- **스마트 계약이 설치된 피어가 동일한 스마트 계약 버전이 이미 인스턴스화된 채널에 가입하면 스마트 계약 컨테이너가 자동으로 시작됩니다.**  

이 튜토리얼에서는 단계에 따라 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 네트워크에서 스마트 계약의 배치를 관리합니다.

- [피어에 스마트 계약을 설치](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install)하십시오.
- [채널에서 인스턴스화](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate)하십시오.
- [보증 정책을 지정](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse)하십시오.
- [스마트 계약 정책 및 코드를 업그레이드](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade)하십시오.


## 시작하기 전에

스마트 계약을 설치하기 전에 다음 사항이 준비되었는지 확인하십시오.

- {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 [네트워크를 빌드](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)하거나 [네트워크에 가입](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)해야 합니다.
- 스마트 계약이 피어에 설치되므로 콘솔에 [피어를 추가](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1)했는지 확인하십시오.  
- 스마트 계약은 채널에서 인스턴스화됩니다. 따라서 콘솔을 사용하여 [채널을 작성](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel)하거나 [채널에 가입](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2)해야 합니다.

## 1단계: 스마트 계약 작성

{{site.data.keyword.blockchainfull_notm}} 콘솔은 스마트 계약의 개발이 아니라 *배치*를 관리합니다. 스마트 계약의 개발에 관심이 있는 경우, Hyperledger Fabric 커뮤니티에 의해 제공되는 튜토리얼 및 {{site.data.keyword.IBM_notm}}에 의해 제공되는 도구를 사용하여 시작할 수 있습니다.

- 다중 당사자 간에 트랜잭션을 수행하기 위해 스마트 계약을 사용하는 방법을 배우려면 Hyperledger Fabric 문서에서 [애플리케이션 개발 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "애플리케이션 개발 주제")를 참조하십시오.
- 애플리케이션을 사용하여 스마트 계약과 상호작용하는 데 필요한 전체 엔드-투-엔드 튜토리얼을 보려면 [Hyperledger Fabric 상업 어음 튜토리얼![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Hyperledger Fabric 상업 어음 튜토리얼")을 참조하십시오.
- 스마트 계약에 액세스 제어 메커니즘을 통합하는 방법에 대해 배우려면 [개발자용 체인코드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "개발자용 체인코드")를 참조하십시오.
- 스마트 계약 빌드를 시작할 준비가 되면 [{{site.data.keyword.blockchainfull_notm}} Visual Studio 코드 확장 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio 마켓플레이스")을 사용하여 자체 스마트 계약 프로젝트 빌드를 시작하십시오. 또한 해당 확장을 사용하여 [Visual Studio에서 사용자의 네트워크로 직접 연결](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode)할 수 있습니다.
- 설치할 준비가 되면 스마트 계약을 피어에 설치할 수 있도록 [.cds 형식 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "스마트 계약 패키지")으로 패키지해야 합니다. 자세한 정보는 [스마트 계약 패키지](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract)를 참조하십시오. 또는 [피어 cli 명령![외부 링크아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html#peer-chaincode-package "피어 체인코드 패키지")을 사용하여 패키지를 빌드할 수 있습니다.
<!-- Update the tutorial link to release1-4 when it is published -->


## 2단계: 스마트 계약 설치
{: #ibp-console-smart-contracts-install}

콘솔을 사용하여 다음 단계를 수행하십시오.

1. **스마트 계약** 탭을 클릭하여 하나 이상의 스마트 계약을 설치하십시오.
2. **스마트 계약 설치**를 클릭하여 스마트 계약 패키지 파일을 [.cds 형식 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "스마트 계약 패키징")으로 업로드하십시오.
{{site.data.keyword.blockchainfull_notm}} Visual Studio 코드 확장을 사용하여 [스마트 계약 패키지를 작성](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract)할 수 있습니다. **스마트 계약** 탭에서 패키지를 설치할 때 스마트 계약을 설치할 피어 노드를 하나 이상 선택할 수 있습니다.

콘솔에 하나의 피어만 있는 경우 스마트 계약이 해당 피어에 설치됩니다. 스마트 계약을 설치할 피어를 선택하도록 프롬프트되지 않습니다. 노드 탭으로 이동하여 콘솔에 의해 관리되는 피어를 클릭하여 개별 피어에 설치되는 스마트 계약 목록을 볼 수 있습니다.

스마트 계약이 채널에서 인스턴스화된 이후라도 **스마트 계약** 탭으로 돌아가서 추가 피어에 스마트 계약을 설치할 수 있습니다. 설치하는 스마트 계약 버전이 인스턴스화된 버전과 동일한 경우, 이러한 추가 피어는 기존 피어와 마찬가지로 트랜잭션을 처리할 수 있습니다.
{:tip}

**이 콘솔을 사용하여 Hyperledger Composer `.bna` 파일을 설치할 수 없습니다.**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode" "peer chaincode").  -->

## 3단계: 스마트 계약 인스턴스화
{: #ibp-console-smart-contracts-instantiate}

스마트 계약은 채널에서 인스턴스화됩니다. 채널에 가입한 피어가 있는 임의의 콘솔 구성원은 스마트 계약을 인스턴스화하고 연관된 [보증 정책](/docs/services/blockchain/glossary.html#glossary-endorsement-policy)을 지정할 수 있습니다.

콘솔을 사용하여 다음 단계를 수행하십시오.

1. 스마트 계약 탭에 표시되는 피어에 설치된 목록에서 스마트 계약을 찾고 행의 오른쪽에 있는 오버플로우 메뉴에서 **인스턴스화**를 클릭하십시오.
2. 열리는 사이드 패널에서 스마트 계약을 인스턴스화할 채널을 선택하십시오. 사용자가 작성한 `channel1` 채널을 선택할 수 있습니다. 그런 후 **다음**을 클릭하십시오.
3. 다음 절에 설명된 [스마트 계약에 대한 보증 정책](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse)을 지정하십시오. 다중 조직이 채널의 멤버인 경우 스마트 계약 트랜잭션을 보증하는 데 필요한 조직의 수를 선택할 수 있습니다.
4. 또한 보증 정책에 포함시킬 조직 구성원을 선택해야 합니다. 튜토리얼에 따른 경우, `org1msp`일 것이며 **네트워크 빌드** 및 **네트워크에 가입** 튜토리얼을 둘 다 완료한 경우, `org2msp`일 것입니다.
5. 스마트 계약에 Fabric 개인용 데이터 콜렉션 정의가 포함된 경우, 연관된 콜렉션 구성 JSON 파일을 업로드해야 합니다. 그렇지 않으면 이 단계를 건너뛸 수 있습니다. [개인용 데이터](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) 사용에 대한 자세한 정보는 이 주제를 참조하십시오.
6. 마지막 패널에서 스마트 계약을 시작할 때 실행할 스마트 계약 함수와 해당 함수에 전달할 연관 인수를 함께 지정하도록 프롬프트됩니다.

왼쪽 탐색에서 채널 아이콘을 클릭하고 표에서 채널을 선택한 다음 **채널 세부사항** 탭을 클릭하여 채널에서 인스턴스화된 모든 스마트 계약을 볼 수 있습니다.

무료 {{site.data.keyword.cloud_notm}} Kubernetes 서비스 클러스터를 사용하는 경우, 유료 클러스터에 비해 인스턴스화에 상당히 긴 시간이 소요될 수 있습니다. 클러스터에 배치한 피어의 수에 따라 몇 분이 소요될 수 있습니다.

**설치 및 인스턴스화** 조합을 사용하면 피어가 여러 채널에서 단일 스마트 계약을 사용할 수 있으므로 강력한 기능이 됩니다. 피어에서 동일한 스마트 계약을 사용하지만 데이터에 액세스할 수 있는 여러 다른 네트워크 구성원 세트가 있는 여러 채널에 가입하려 할 수 있습니다. 피어에서 스마트 계약을 한 번 설치한 다음, 인스턴스화된 임의 채널에서 동일한 스마트 계약 컨테이너를 사용할 수 있습니다. 이 경량 접근법을 사용하면 계산 및 스토리지 공간을 절약하고 네트워크를 확장하는 데 도움이 됩니다.

## 4단계: 클라이언트 애플리케이션을 사용하여 트랜잭션 전송
{: #ibp-console-smart-contracts-connect-to-SDK}

스마트 계약이 채널에서 인스턴스화된 후, 클라이언트 애플리케이션을 사용하여 네트워크의 다른 구성원과 거래할 수 있습니다. 애플리케이션은 블록체인 원장의 자산을 작성, 전송 또는 업데이트하기 위해 스마트 계약에 포함된 비즈니스 로직을 호출할 수 있습니다.

### SDK로 연결
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
**스마트 계약** 탭에는 클라이언트 앱에서 인스턴스화된 스마트 계약에 연결하는 데 필요한 정보가 포함되어 있습니다. 인스턴스화된 각 스마트 계약 옆의 오버플로우 메뉴로 이동하십시오. **SDK로 연결** 단추를 클릭하십시오. 그러면 스마트 계약에 연결하는 데 필요한 정보(계약 이름, 채널 이름 및 연결 프로파일)를 제공하는 사이드 패널이 열립니다. 자세한 정보는 [애플리케이션 작성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app)을 참조하십시오.

## 보증 정책 지정
{: #ibp-console-smart-contracts-endorse}

모든 스마트 계약에는 보증 정책이 있어야 합니다. 이는 인스턴스화 중에 지정됩니다. 보증 정책은 스마트 계약을 실행할 수 있고 독립적으로 트랜잭션 출력의 유효성을 검증할 수 있는 채널에서 조직 및 피어 세트를 지정합니다. 예를 들어, 보증 정책은 채널의 대다수의 구성원이 트랜잭션을 보증하는 경우에만 트랜잭션이 원장에 추가되도록 지정할 수 있습니다. 스마트 계약을 인스턴스화하는 조직은 스마트 계약을 설치한 구성원 중에서 유효성 검증자가 될 구성원을 선택할 수 있으며, 모든 채널 구성원에 대한 보증 정책을 설정합니다. [스마트 계약 업데이트](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade)의 단계에 따라 보증 정책을 업데이트할 수 있습니다.

[스마트 계약 인스턴스화](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate) 단계를 따르는 경우, 채널을 선택한 후에 사이드 패널을 사용하여 계약의 보증 정책을 설정할 수 있습니다. 이 패널을 사용하면 채널에서 스마트 계약을 설치한 피어의 목록에서 트랜잭션을 보증해야 하는 피어를 선택하여 단순 정책을 지정할 수 있습니다. 이 방법을 사용하여 모든 채널 구성원, 이 구성원 중 대다수, 단일 구성원 또는 구성원이 자체 서명하지 못하도록 단순 +1 중 하나의 보증 정책을 지정할 수 있습니다. 기본 보증 정책은 `1 of x`로 설정되며 이는 단일 구성원만이 스마트 계약 트랜잭션을 보증하는 데 필요함을 의미합니다.

JSON 형식으로 정책을 지정하려면 **고급** 단추를 클릭하십시오. 이 방법을 사용하면 채널의 특정 구성원이 기타 대부분의 구성원과 함께 트랜잭션을 유효성 검증하도록 요청하는 것과 같이 보다 복잡한 보증을 지정할 수 있습니다. Hyperledger Fabric 문서에서 추가 [고급 보증 정책 예제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "보증 정책 예제")를 찾을 수 있습니다. JSON으로 보증 정책을 작성하는 방법에 대한 자세한 정보는 [Hyperledger Fabric 노드 SDK 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "Hyperledger Fabric 노드 SDK 문서")를 참조하십시오.

## 스마트 계약 업그레이드
{: #ibp-console-smart-contracts-upgrade}

원장의 자산과의 관계를 유지하면서 코드, 보증 정책 또는 개인용 데이터 콜렉션을 변경하도록 스마트 계약을 업그레이드할 수 있습니다. 인스턴스화된 스마트 계약을 업그레이드하려는 여러 가지 이유가 있을 수 있습니다.
1. 스마트 계약을 업그레이드하여 코드에서 기능을 추가 또는 제거하고 비즈니스 네트워크 로직에서 반복할 수 있습니다.
2. 새 구성원이 채널에 추가될 때마다, 인스턴스화된 스마트 계약의 보증 정책이 *반드시* 채널 구성원을 포함하도록 업데이트되어야 합니다. 새 채널 구성원에 대해 작업하려면 스마트 계약 자체가 변경되지 않은 경우에도 스마트 계약이 새 버전 번호로 다시 패키지되고 채널에서 인스턴스화되어야 합니다. 그렇지 않으면 트랜잭션 보증이 성공할 수 없습니다.
3. 개인용 데이터 콜렉션이 변경된 경우(예: 조직이 추가되거나 제거됨) 스마트 계약을 업그레이드해야 합니다. 또는 새 개인용 데이터 콜렉션이 콜렉션 구성 JSON 파일에 추가될 때마다 이 조치를 사용하십시오.
4. 스마트 계약 초기화 인수가 변경되었습니다.

**인스턴스화된 스마트 계약을 업그레이드하기 전에 스마트 계약의 이전 레벨을 실행 중인 채널의 모든 피어에 새 버전의 스마트 계약을 설치해야 합니다.**

### 스마트 계약을 업그레이드하는 방법
{: #ibp-console-smart-contracts-upgrade-howto}

스마트 계약을 업그레이드하려면 스마트 계약 이름은 동일하게 지정하고 새 버전 번호를 사용하여 업데이트된 코드를 설치하십시오. 채널 내의 임의의 피어에 스마트 계약의 새 버전을 설치한 경우, **인스턴스화된 스마트 계약** 표의 원래 버전 옆에 `Upgrade Available` 단추가 표시됩니다.

{:important}
스마트 계약을 실행할 새 구성원이 채널에 가입하면 모든 피어에 새 버전을 설치하고 새 구성원을 포함하는 수정된 보증 정책이 있는 채널에서 인스턴스화하여 스마트 계약을 업데이트해야 합니다.

- **인스턴스화된 스마트 계약** 표로 스크롤하십시오.
- **인스턴스화된 스마트 계약** 표에서 업그레이드할 스마트 계약 버전 및 채널을 찾으십시오. 옆에 **업그레이드 사용 가능** 레이블이 있어야 합니다.
- 스마트 계약 행의 오른쪽에서 **오버플로우 메뉴**를 클릭하고 **업그레이드**를 클릭하여 다음 단계를 수행하십시오.  

 1. 드롭 다운 목록에서 채널에서 업그레이드할 스마트 계약 버전을 선택하십시오.
 2. 채널 구성원을 추가하거나 제거하여 보증 정책을 업데이트하십시오. 또한 **고급**을 클릭하여 기존 정책을 수정하는 새 JSON 형식의 문자열을 붙여넣을 수 있습니다.
 3. 개인용 데이터 콜렉션 구성 파일을 스마트 계약과 연관시키려는 경우 JSON 파일을 업로드할 수 있습니다. 또는 기존 콜렉션 구성을 업데이트하려는 경우 JSON 파일을 업로드할 수 있습니다.   
 스마트 계약이 이전에 콜렉션 구성 파일로 인스턴스화된 경우 이 단계 중에 이전 버전 또는 새 버전의 콜렉션 구성 파일을 **반드시** 다시 업로드해야 합니다.  
 {:important}
 4. (선택사항) 매개변수가 변경된 경우 스마트 계약 초기화 인수 값을 수정하십시오. 확실하지 않은 경우, 스마트 계약 개발자와 함께 확인하십시오. 변경되지 않은 경우 이 필드를 공백으로 둘 수 있습니다.

스마트 계약을 업그레이드한 후, 채널에서 인스턴스화된 계약의 버전을 변경하고 새 버전을 설치한 모든 피어에 대해 스마트 계약 컨테이너를 변경하십시오. 개인용 데이터 콜렉션을 사용 중인 경우 채널에 앵커 피어를 구성했는지 확인하십시오.

### 스마트 계약 업그레이드 시 고려사항
{: #ibp-console-smart-contracts-upgrade-considerations}

1. 스마트 계약의 새 버전을 모든 피어에 설치해야 합니까?  

  스마트 계약이 피어에서 호출되면 채널에서 인스턴스화된 버전을 실행하려고 시도합니다. 피어에서 이전 버전이 실행 중인 경우 오류가 발생합니다. 따라서 스마트 계약을 채널에서 업그레이드하기 전에, *이전 버전을 실행 중인 모든 피어에 스마트 계약의 최신 버전을 설치하는 것이 가장 좋습니다.*  

2. 스마트 계약의 후속 버전이 이전 버전의 스마트 계약의 원장에서 계속 데이터를 처리합니까?  

  예. 새 스마트 계약 코드가 호환 가능한 방법으로 데이터를 처리하는 한(새 JSON 필드를 추가하고 기존 필드의 시맨틱 또는 유형을 변경하지 않음), 스마트 계약의 후속 버전은 이전 버전의 데이터에 대해 실행될 수 있습니다.

3. 채널에서 스마트 계약을 업데이트하기 전에 최신 버전으로 업그레이드하는 것을 잊은 경우 피어에 어떠한 일이 발생합니까?  

  스마트 계약의 새 버전을 사용하도록 채널을 업데이트한 후, 이전 버전을 실행하는 채널에 여전히 피어가 있는 경우 해당 피어는 더 이상 스마트 계약에 대한 트랜잭션을 보증할 수 없습니다. 또한 스마트 계약 보증 정책이 정의되는 방법에 따라 원장에 커미트할 트랜잭션에 대해 보증이 충분하지 않을 위험이 있습니다. 그러나 이러한 피어에 새 버전의 스마트 계약을 나중에 설치할 수 있으며 다시 트랜잭션을 보증하여 효과적으로 캐치업할 수 있습니다.

4. 내 개인용 데이터 콜렉션에서 조직을 제거하면 어떻게 됩니까?

   원장이 콜렉션에서 멤버십을 제거하는 블록에 도달할 때까지 해당 조직의 피어가 개인용 데이터 콜렉션에 데이터를 계속 저장합니다. 이러한 상황이 발생한 후에는 피어가 향후 트랜잭션에서 개인용 데이터를 수신하지 않으며 해당 조직의 _클라이언트_는 더 이상 피어에서 체인코드를 통해 개인용 데이터를 조회할 수 없습니다.

## 개인용 데이터
{: #ibp-console-smart-contracts-private-data}

개인용 데이터는 버전 1.2 이상의 Hyperledger Fabric 네트워크 기능이며 **채널의** 기타 조직 구성원으로부터 민감한 정보를 비공개로 유지하는 데 사용됩니다. [개인용 데이터 콜렉션 ![외부 링크 아이콘")](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "개인용 데이터 콜렉션의 개념")을 사용하여 데이터 개인정보를 보호할 수 있습니다. 예를 들어, 여러 도매업자 및 농부 집단이 단일 채널에 가입할 수 있습니다. 만약 농부와 도매업자가 개인적으로 거래하기를 원한하는 경우, 해당 목적을 위한 채널을 만들 수 있습니다. 그러나 비즈니스 상호작용을 제어하는 스마트 계약에 대한 개인용 데이터 콜렉션을 작성하여 보조 채널을 작성할 필요 없이 가격과 같은 민감한 판매 영역을 비공개로 유지할 수 있습니다. 블록체인 내의 개인용 데이터를 사용하는 시기에 대한 자세한 정보는 Fabric 문서에서 [개인용 데이터 ![외부 링크 아이콘")](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "개인용 데이터") 개념에 대한 기사를 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform에 개인용 데이터를 사용하려면 다음 세 가지 조건이 충족되어야 합니다.  
1. **개인용 데이터 콜렉션을 정의하십시오.** 개인용 데이터 콜렉션 파일이 스마트 계약에 추가될 수 있습니다. 그런 다음, 런타임 시 클라이언트 애플리케이션이 개인용 데이터별 체인코드 API를 사용하여 콜렉션에서 데이터를 입력하고 검색할 수 있습니다. 스마트 계약과 함께 개인용 데이터 콜렉션을 사용하는 방법에 대한 자세한 정보는 Fabric 문서의 [개인용 데이터 사용 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-private-data.html "개인용 데이터 사용 방법")에서 Fabric SDK 튜토리얼을 참조하십시오.  

2. **스마트 계약을 설치하고 인스턴스화하십시오.** 스마트 계약 개인용 데이터 콜렉션이 정의된 후에는 채널의 구성원인 피어에 스마트 계약을 설치해야 합니다. 콘솔을 사용하여 채널에서 스마트 계약을 인스턴스화할 때 콜렉션 구성 JSON 파일을 업로드해야 합니다. [콜렉션 정의 JSON 파일 작성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-private-data.html "개인용 데이터 사용 방법") 방법에 대한 자세한 정보는 Fabric SDK 문서 주제를 참조하십시오.

  콜렉션을 사용하여 콜렉션 구성 파일로 스마트 계약을 설치하고 인스턴스화하는 대신 Fabric SDK를 사용할 수도 있습니다. Node SDK 문서의 [개인용 데이터 사용 방법 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "개인용 데이터 사용 방법")에서도 해당 지시사항을 볼 수 있습니다.  

  **참고:** SDK를 사용하여 스마트 계약을 설치하거나 인스턴스화하려면 클라이언트가 피어의 관리자여야 합니다. 따라서 사용자는 애플리케이션 ID를 작성하는 대신 콘솔 지갑에서 피어 관리자 ID의 인증서를 다운로드하여 피어 관리자의 서명 인증서 및 개인 키를 직접 SDK에 전달해야 합니다. 키 쌍을 SDK에 전달하는 방법에 대한 예를 보려면 [하위 레벨 Fabric SDK API를 사용하여 네트워크에 연결](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level)을 참조하십시오.  


3. **앵커 피어를 구성하십시오.** 개인용 데이터가 작동하려면 교차 조직의 [gossip ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "gossip 데이터 분배 프로토콜")을 사용할 수 있어야 하므로 콜렉션 정의 내의 각 조직에 대해 앵커 피어가 존재해야 합니다. 네트워크에서 [앵커 피어를 구성하는 방법](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-channels-anchor-peers)은 이 정보를 참조하십시오.

이제 채널이 개인용 데이터를 사용하도록 구성되었습니다.

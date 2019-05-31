---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: getting started tutorials, create a CA, enroll, register, create an MSP, wallet, create a peer, create ordering service, Raft, join a network, system channel

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

# 네트워크 가입 튜토리얼
{: #ibp-console-join-network}

{{site.data.keyword.blockchainfull}} Platform은
블록체인 애플리케이션 및 네트워크를 개발, 배치 및 운영할 수 있게 해주는 블록체인 서비스 제공
오퍼링입니다. [블록체인 컴포넌트 개요](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview)를
방문하여 블록체인 컴포넌트 및 함께 작동하는 방법에 대해 배울 수 있습니다. 이 튜토리얼은
[샘플 네트워크 튜토리얼 시리즈](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial)의
두 번째 부분이며 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔에서 노드를 작성하여 다른 클러스터에서 호스팅되는 블록체인 컨소시엄에
연결하는 방법에 대해 설명합니다.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform 베타 평가판을 사용 중인 경우 콘솔의 일부 패널이 GA(Generally Available) 서비스 인스턴스에 따라 최신 상태로 유지되는 현재 문서와 일치하지 않을 수 있습니다. 모든 최신 기능의 이점을 얻으려면 지금 [{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} 시작하기](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)의 지시사항에 따라 새 GA 서비스 인스턴스를 프로비저닝하는 것이 좋습니다. 

**대상 독자:** 이 주제는 블록체인 네트워크를 작성, 모니터링 및 관리할 책임이 있는 네트워크 운영자를 위해 설계되었습니다.  

아직 {{site.data.keyword.blockchainfull_notm}} Platform을 배치하지 않은 경우 [{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} 시작하기](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)를 참조하십시오. 블록체인 컴포넌트에 대한 새 Kubernetes 클러스터를 작성하거나 {{site.data.keyword.cloud_notm}} 계정 내의 기존 클러스터를 사용할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform을 배치한 후 콘솔을 실행하여 블록체인 컴포넌트를 작성하고 관리할 수 있습니다.

유료 또는 무료 Kubernetes 클러스터에 배치했는지에 상관없이, Kubernetes 대시보드를 사용하여 노드를 배치하고 채널을 작성할 때 원하는 대로 사용 가능한 리소스에 세심한 주의를 기울이십시오. Kubernetes 클러스터를 관리하고 필요에 따라 추가 리소스를 배치하는 것은 사용자의 책임입니다. 컴포넌트가 무료 클러스터에 성공적으로 배치되었으나 사용자가 더 많은 컴포넌트를 추가하면 컴포넌트 실행이 느려질 수 있습니다. 컴포넌트 크기 조정 및 콘솔이 Kubernetes 클러스터와 상호작용하는 방법에 대한 자세한 정보는 [리소스 할당](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction)을 참조하십시오.
{: note}

## 샘플 네트워크 튜토리얼 시리즈
{: #ibp-console-join-network-structure}

세 개의 파트로 구성된 이 튜토리얼 시리즈는 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 콘솔에서 상대적으로 단순한 다중 노드 Hyperledger Fabric 네트워크를 작성한 후 상호 연결하여 네트워크를 Kubernetes 클러스터에 배치하고 스마트 계약을 설치하고 인스턴스화하는 프로세스를 안내합니다.

* [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은 순서 지정 서비스 또는 피어를 작성하여 네트워크를 호스팅하는 프로세스를 안내합니다.
* **네트워크 가입 튜토리얼**: 이 현재 튜토리얼은 피어를 작성하고 해당 피어를 채널에 가입시켜 기존 네트워크에 가입하는 프로세스를 안내합니다.
* [네트워크에 스마트 계약 배치](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)는 스마트 계약을 작성하여 이를 네트워크에 배치하는 방법을 제공합니다.

이러한 튜토리얼의 단계를 사용하여 개발 및 테스트 목적의 한 클러스터에 다중 조직이 있는 네트워크를 빌드할 수 있습니다. 순서 지정 서비스를 작성하고 조직을 추가하여 블록체인 컨소시엄을 구성하려는 경우 **네트워크 빌드** 튜토리얼을 사용하십시오. 피어를 네트워크에 연결하려면 **네트워크 가입** 튜토리얼을
사용하십시오. 다른 컨소시엄 구성원과 함께 튜토리얼에 따라 **분산** 블록체인 네트워크를 작성할 수 있습니다.  


이 튜토리얼은 피어를 **기존** 네트워크에 가입시키는 방법을 보여줍니다. 모든 채널 및 컨소시엄을 호스팅하는 순서 지정 서비스가 이미 사용자의 클러스터 또는 다른 {{site.data.keyword.blockchainfull_notm}} Platform 클러스터에 존재하는 것으로 가정합니다. 가입할 기존 네트워크가 없는 경우 [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)을 방문하여 작성 방법을 알아보십시오. **네트워크에 가입** 튜토리얼을 통해
파란색 상자로 강조표시된 다음 `Org2` 블록체인 컴포넌트를 작성하는 단계를 배울 수 있습니다.
![네트워크 가입 구조](../images/ib2-join-network.png "네트워크 가입 구조")  
*그림 1. 네트워크 가입 구조*  
**네트워크 가입** 튜토리얼의 단계를 수행하여 다음 컴포넌트를 작성하고 다음 조치를 완료할 수 있습니다.

* **하나의 피어 조직** `Org2`  
조직 `Org2`를 정의하는 Org2 MSP(Membership Services Provider) 정의를 작성합니다.
* **하나의 피어** `Peer Org2`   
  위 그림에서 `원장 x` 원장은 분산 피어에서 유지보수됩니다. 피어는 [Couch DB ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html)를 상태 데이터베이스로 사용하여 배치됩니다.
* **하나의 인증 기관(CA)** `Org2 CA`
  CA는 모든 조직 관리자 및 조직과 연관된 노드에 인증서를 발행하는 노드입니다. 여기서는 피어 조직 `Org2`에 대해 하나의 CA를 작성합니다.
* **하나의 채널에 가입**
  이 튜토리얼에서는 [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)에서 작성된 채널에 가입하는 방법에 대해 설명합니다.

이 튜토리얼 전체에 걸쳐 콘솔 내의 일부 필드에 대해 **권장되는 값**을 제공합니다. 이로 인해
탭 및 드롭 다운 목록에서 이름 및 ID를 쉽게 인식할 수 있습니다. 특히 이전 단계에서 입력한 등록된 사용자의 ID 및 시크릿과 같은 특정 값을 기억해야 하므로 이러한 값은 필수가 아니지만 도움이 될 것입니다. 이러한 값을 잊어버린 경우 추가 사용자를 작성하고 등록해야 합니다. 각 태스크 후에 권장되는 값의 표를 제공하며 샘플 값을 사용하지 않는 경우 튜토리얼을 진행하면서 임의의 위치에서 사용하는 값을 기록하도록 권장합니다.
{:tip}

## 1단계: 추가 조직 및 블록체인의 시작점 작성
{: #ibp-console-join-network-create-ca-org2}

콘솔을 사용하여 작성할 각 조직에 대해 하나 이상의 CA를 배치해야 합니다. CA는 모든 네트워크 참가자(피어, 순서 지정 서비스, 클라이언트, 관리자 등)에게 인증서를 발행하는 노드입니다. 서명 인증서 및 개인 키를 포함하는 이러한 인증서는 네트워크 참가자가 통신하고 인증하며 궁극적으로 트랜잭션을 수행할 수 있도록 허용합니다. 이러한
CA는 조직 자체를 정의하는 것 외에도 조직에 속한 모든 ID 및 인증서를 작성합니다. 그러면 이러한 ID를 사용하여 노드를 배치하고 관리자 ID를 작성하며 트랜잭션을 제출할 수 있습니다. 작성해야 하는 CA 및 ID에 대한 자세한 정보는
[ID 관리](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities)를 참조하십시오.

이 튜토리얼에서는 하나의 조직을 작성합니다. 따라서 **하나의 CA**를 작성해야 합니다.

### 피어 조직 CA 작성
{: #ibp-console-join-network-create-CA-org2CA}

이 튜토리얼의 일부로, CA가 사용자 및 노드에 대한 인증서 및 개인 키를 발행합니다. 이러한 ID는 {{site.data.keyword.IBM_notm}}에서 관리되지 않으며 키는 콘솔에 저장되지 않습니다. 브라우저의 로컬 스토리지에만 저장됩니다. 따라서 사용자의 ID와 조직 MSP를 내보내야 합니다. 다른 시스템 또는 다른 브라우저에서 콘솔에 액세스하려고 시도하는 경우 이러한 ID 및 조직 정의를 가져와야 합니다.
{:important}

콘솔에서 다음 단계 수행:  

1. 왼쪽의 **노드** 탭으로 이동하여 **인증 기관 추가**를 클릭하십시오. 사이드 패널을 사용하여 작성할 CA를 사용자 정의할 수 있으며 이 CA가 키를 발행할 조직을 사용자 정의할 수 있습니다.
2. **인증 기관 작성** 아래의 **{{site.data.keyword.cloud_notm}}**를 클릭하고 **다음**을 클릭하십시오.
3. 두 번째 사이드 패널을 사용하여 CA의 **표시 이름**을 제공하십시오. 이 CA에 대한 권장 값은 `Org2 CA`입니다.
4. 다음 패널에서 **CA 관리자 등록 ID**를 `admin`으로 지정하고 시크릿을 `adminpw`로 지정하여 CA 관리자 인증 정보를 제공하십시오. 다시 말하지만 이는 **권장되는 값**입니다.
5. 유료 클러스터를 사용 중인 하는 경우 노드에 대한 리소스 할당을 구성할 수 있는 기회가 제공됩니다. 이 튜토리얼의 목적에 따라 모든 기본값을 허용하고 **다음**을 클릭하십시오. 리소스를 노드에 할당하는 방법을 알아보려면 [리소스 할당](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources)에 대한 주제를 참조하십시오. 무료 클러스터를 사용 중인 경우 **요약** 페이지로 건너뜁니다.
6. 요약 페이지를 검토한 다음 **인증 기관 추가**를 클릭하십시오.

**태스크: 피어 조직 CA 작성**

  | **필드** | **표시 이름** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|
  | **CA 작성** | Org2 CA  | admin | adminpw |

*그림 2. 피어 조직 CA 작성*  

CA를 배치한 후에 조직 MSP를 작성하고 사용자를 등록하고 네트워크, **peer**에
시작점을 작성할 때 이를 사용할 것입니다.

고급 사용자에게 자체 CA가 이미 제공되었을 수 있으며 콘솔에 새 CA를 작성하지 않으려고 할 수 있습니다. 기존 CA에서 `X.509` 형식으로 된 인증서를 발행할 수 있는 경우 여기서 새 CA를 작성하는 대신 자체 서드파티 CA를 사용할 수 있습니다. 자세한 정보는 [피어 또는 순서 지정 서비스에 서드파티 CA 사용](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-identities)에 대한 주제를 참조하십시오.

### ID를 등록하기 위해 CA 사용
{: #ibp-console-join-network-use-CA-org2}

작성할 각 노드 또는 애플리케이션에는 블록체인 네트워크에 참여할 인증서 및 개인 키가 필요합니다. 또한 이러한 노드 및 애플리케이션에 대한 관리자 ID를 작성해야만 콘솔에서 관리할 수 있습니다. 작성한 각 CA에 대해 한 번씩, 총 두 번 이 프로세스를 수행할 것입니다. 각 CA에 두 개의 ID를 ID를 작성할 것입니다.

* **조직 관리자**: 이 ID를 사용하면 플랫폼 콘솔을 사용하여 노드를 작동할 수 있습니다.
* **피어 ID**: 피어 자체의 ID입니다. 피어가 조치(예: 트랜잭션 보증)를 수행할 때마다 해당 인증서를 사용하여 서명합니다.

클러스터 유형에 따라 CA 배치에 최대 10분이 소요될 수 있습니다. CA가 처음 배치되거나 CA가 사용 불가능한 경우 CA에 대한 타일의 상자는 회색 상자가 됩니다. CA가 성공적으로 배치되고 실행 중이면 이 상자가 녹색이 되며, 이는 CA가 "실행 중"이며 ID를 등록하는 데 사용될 수 있음을 나타냅니다. ID를 등록하기 위해 아래의 단계로 진행하기 전에 CA 상태가 "실행 중"이 될 때까지 기다려야 합니다.
{:important}

타일의 녹색 상자로 표시된 대로 CA가 실행 중이면 다음 단계를 완료하여 이러한 인증서를 생성하십시오.

1. `Org2 CA`를 클릭하고 CA에 대해 작성한 `admin` ID가 테이블에 표시되는지 확인하십시오. 그런 다음 **사용자 등록** 단추를 클릭하십시오.
2. 먼저 조직 관리자를 등록합니다. 이는 **등록 ID**를 `org2admin`으로 지정하고 **시크릿**을 `org2adminpw`로 지정하여 수행할 수 있습니다. 그런 다음 이 ID에 대한 `유형`을 `client`로 설정하십시오(관리자 ID는 항상 `client`로 등록되어야 하는 반면 노드 ID는 항상 `peer` 유형을 사용하여 등록되어야 함). 소속 필드는 고급 사용자용이며 이 튜토리얼의 일부가 아니므로 **루트 소속 사용**을 나타내는 상자를 클릭하십시오. 소속이 Fabric CA에서 사용되는 방법에 대해 자세히 알아보려면 [새 ID 등록 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity)에 대한 주제를 참조하십시오. 이제 목록에서 임의의 소속(예: `org1`)을 선택하십시오. 또한 **최대 등록 수** 필드를 무시하십시오. 등록에 대해 자세히 알아보려면 [ID 등록](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)을 참조하십시오. **다음**을 클릭하십시오.
4. 이 튜토리얼을 위해서는 **속성 추가**를 사용할 필요가 없습니다. ID 속성에 대해 자세히 알아보려면 [ID 등록](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)을 참조하십시오.
5. 조직 관리자가 등록된 후 피어의 ID에 대해 이 동일한 프로세스를 반복하십시오(`Org2 CA` 사용). 피어 ID의 경우 등록 ID `peer2` 및 시크릿 `peer2pw`를 제공하십시오. 이는 노드 ID이므로 `peer`를 **유형**으로 선택하십시오. **루트 소속 사용**을 의미하는 상자를 클릭하고 **최대 등록 수**를 무시하십시오. 그런 다음, 다음 패널에서 이전과 같이 **속성**을 지정하지 마십시오.

CA를 사용하여 이러한 ID를 등록(register)하는 것은 ID **작성**의 첫 번째 단계일 뿐입니다. **등록(enroll)**될 때까지 이러한 ID를 사용할 수 없습니다. `org2admin` ID의 경우 이는 다음 단계에 표시될 MSP 작성 중에 발생합니다. 피어의 경우 피어 작성 중에 발생합니다.
{:note}

**태스크: 사용자 등록**

  |  **필드** | **설명** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **사용자 등록** |  Org2 admin | org2admin | org2adminpw |
  | | 피어 ID |  peer2 | peer2pw |

*그림 3. CA를 사용하여 사용자 등록*  

### 피어 조직 MSP 작성
{: #ibp-console-join-network-create-peers-org2}

피어의 CA를 작성하여 이를 사용하여 조직 ID를 **등록**했습니다. 이제
피어 조직의 정규 정의, 즉, MSP(Membership Services Provider)를 작성해야 합니다. 많은 피어가 조직에 속할 수 있습니다. **피어를
작성할 때마다 새 조직을 작성할 필요는 없습니다.** 튜토리얼을 수행함에 있어서 이번이 처음이므로
이 조직의 MSP ID를 작성할 것입니다. MSP를 작성하는 과정에서 `org2admin` ID에 대한 인증서를 생성하여 지갑에 추가합니다.

1. 왼쪽 탐색의 **조직** 탭으로 이동하여 **MSP 정의 작성**을 클릭하십시오.
2. MSP에 표시 이름 `Org2 MSP` 및 MSP ID `org2msp`를 제공하십시오. 이 필드에
사용자 고유의 MSP ID를 지정하려면 도구 팁에서 이 이름에 대한 제한사항 스펙을 확인하십시오.
3. **루트 인증 기관 세부사항** 아래에 이전 단계에서 ID를 등록하는 데 사용한 CA를 지정하십시오. 이 튜토리얼을 처음 사용하는 경우에는 `Org2 CA`만 표시되어야 합니다.
4. 아래의 **등록 ID** 및 **등록 시크릿** 필드가 사용자가 CA를 사용하여 작성한 첫 번째 사용자의 등록 ID 및 시크릿(`admin` 및 `adminpw`)으로 자동으로 채워집니다. 그러나 이 ID를 사용하면 사용자의 조직이 CA ID와 동일한 ID가 될 수 있으며, 이는 보안을 이유로 권장되지 않습니다. 대신 드롭 다운 목록에서 조직 관리자에 대해 작성한 등록 ID `org2admin`을 선택하고 이와 연관된 시크릿 `org2adminpw`를 입력하십시오. 그런 다음 이 ID에 표시 이름 `Org2 Admin`을 제공하십시오.
5. **생성** 단추를 클릭하여 이 ID를 조직의 관리자로 등록하고 지갑으로 내보내십시오. 이는 피어 및 채널을 작성할 때 사용됩니다.
6. **내보내기**를 클릭하여 관리자 인증서를 파일 시스템으로 내보내십시오. 위에서 설명한 대로
이 ID는 클러스터에 저장되거나 {{site.data.keyword.IBM_notm}}에 의해 관리되지 않습니다. 로컬 브라우저 스토리지에만 저장됩니다. 브라우저를 변경하는 경우에는 이 ID를 지갑으로 가져와야 피어를 관리할 수 있습니다.
7. **MSP 정의 작성**을 클릭하십시오.

**태스크: 피어 조직 MSP 정의 작성**

  |  | **표시 이름** | **MSP ID** | **등록 ID**  | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **조직 작성** | Org2 MSP | org2msp |||
  | **루트 CA** | Org2 CA ||||
  | **조직 관리자 인증서** | |  | org2admin | org2adminpw |
  | **ID** | Org2 Admin |||||

  *그림 4. 피어 조직 MSP 정의 작성*  

MSP를 작성한 후 왼쪽 탐색에서 **지갑**을 클릭하여 액세스할 수 있는 **지갑**에서 피어 조직 관리자를 볼 수 있어야 합니다.

**태스크: 지갑 확인**

  | **필드** | **표시 이름** | **설명** |
  | ------------------------- |-----------|----------|
  | **ID** | Org2 Admin | Org2 ID |

  *그림 5. 지갑 확인*  

MSP에 대한 자세한 정보는 [조직 관리](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations)를 참조하십시오.

사용자가 인증서 관리 및 보안의 책임을 지므로 조직 관리자 ID를 내보내는 것이 중요합니다. 순서 지정 서비스 및 순서 지정 서비스 MSP 정의를 내보내는 경우 다른 운영자가 순서 지정 서비스에 대한 새 채널을 작성하거나 피어를 채널에 가입시킬 수 있는 다른 콘솔로 가져올 수 있습니다.
{:important}

### 피어 작성
{: #ibp-console-join-network-peer-create}

[CA를 작성](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA)하고
이를 사용하여 ID를 등록하고
[피어 조직 MSP](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2)를
작성했으면 피어를 작성할 준비가 된 것입니다.

#### 피어의 역할
{: #ibp-console-join-network-peer-role}

조직 자체가 원장을 유지보수하지 않는다는 사실을 기억하는 것이 중요합니다. 피어가 수행합니다. 또한 조직은
피어를 사용하여 트랜잭션 제안에 서명하고 채널 구성 업데이트를 승인합니다. 채널에
두 개 이상의 피어가 있으면 고가용성이 구현되며 두 개 이상의 피어가 한 채널에
가입하는 것이 프로덕션 레벨의 구현에 대한 우수 사례로 간주됩니다. 이 튜토리얼에서는 단일 피어 작성 프로세스만 표시합니다.

리소스 할당 관점에서는 동일한 피어를 여러 채널에 가입시킬 수 있습니다. 피어 디자인은
한 채널의 데이터가 피어를 통해 다른 채널로 전달되지 않도록 합니다. 그러나 피어가
각 채널에 대해 별도의 원장을 저장하므로 피어가 트랜잭션 및 데이터 로드를 처리하기에 충분한 처리 능력과 스토리지를 보유해야 합니다.

#### 피어 배치
{: #ibp-console-join-network-deploy-peer-role}

콘솔을 사용하여 다음 단계를 수행하십시오.

1. **노드** 페이지에서 **피어 추가**를 클릭하십시오.
2. **새 피어 작성** 아래의 {{site.data.keyword.cloud_notm}}를 클릭하고 **다음**을 클릭하십시오.
3. 피어에 **표시 이름**인 `Peer Org1`를 제공하십시오.
4. 다음 화면에서 `Org2 CA`를 선택하십시오. 이는 피어 ID를 등록하는 데 사용한 CA입니다. 드롭 다운 목록에서 피어에 대해 작성한 피어 ID의 **등록 ID**로 `peer2`를 선택하고 이와 연관된 **시크릿**으로 `peer2pw`를 입력하십시오. 그런 다음, 드롭 다운 목록에서 `Org2 MSP`를 선택하고 **다음**을 클릭하십시오.
5. 다음 사이드 패널에서는 TLS CA 정보를 요청합니다. CA를 작성할 때 TLSCA가 함께 작성되었습니다. 이 CA는 노드에 대한 보안 통신 계층의 인증서를 작성하는 데 사용됩니다. 그런 다음, 드롭 다운 목록에서 피어에 대해 작성한 피어 ID의 **등록 ID**로 `peer2`를 선택하고 이와 연관된 **시크릿**으로 `peer2pw`를 입력하십시오. **TLS CSR 호스트 이름**은 피어 엔드포인트를 처리하는 데 사용될 수 있는 사용자 정의 도메인 이름을 지정하려는 고급 사용자가 사용할 수 있는 옵션입니다. 사용자 정의 도메인 이름은 이 튜토리얼의 일부가 아니므로 지금은 **TLS CSR 호스트 이름**을 공백으로 두십시오.
6. 다음 사이드 패널에서 피어의 관리자가 될 수 있도록 **ID 연관**을 요청합니다. 이 튜토리얼의 목적에 따라 조직 관리자 `Org2 Admin`을 피어의 관리자로도 설정하십시오. `Org2 CA`와 다른 ID를 등록하고 해당 ID를 피어의 관리자로 설정할 수 있지만 이 튜토리얼에서는 `Org2 Admin` ID를 사용합니다.
7. 유료 클러스터를 사용 중인 하는 경우 다음 패널에서 노드에 대한 리소스 할당을 구성할 수 있는 기회가 제공됩니다. 이 튜토리얼의 목적에 따라 모든 기본값을 허용하고 **다음**을 클릭하십시오. 리소스를 노드에 할당하는 방법을 알아보려면 [리소스 할당](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources)에 대한 주제를 참조하십시오. 무료 클러스터를 사용 중인 경우 **요약** 페이지가 표시되어 있습니다.
8. 요약 내용을 검토하고 **피어 추가**를 클릭하십시오.

**태스크: 피어 배치**

  |  | **표시 이름** | **MSP ID** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **피어 작성** | Peer Org2 | org2msp |||
  | **CA** | Org2 CA ||||
  | **피어 ID** | |  | peer2 | peer2pw |
  | **관리자 인증서** | org2msp ||||
  | **TLS CA** | Org2 CA ||||
  | **TLS CA ID** | || admin | adminpw |
  | **ID 연관** | Org2 Admin |||||

  *그림 6. 피어 배치*  

프로덕션 시나리오에서는 각 채널에 세 개의 피어를 배치하는 것이 좋습니다. 이는 하나의 피어가 작동 중지되도록 허용하고(예: 유지보수 주기 동안) 고가용성 피어를 계속 유지하기 위한 것입니다. 조직에 대한 둘 이상의 피어를 배치하려면 첫 번째 피어 ID를 등록하는 데 사용한 것과 동일한 CA를 사용하십시오. 이 튜토리얼에서는 `Org2 CA`입니다. 그런 다음 별개의 등록 ID 및 시크릿을 사용하여 새 피어 ID를 등록하십시오(예: `org2secondpeer` 및 `org2secondpeerpw`). 그런 다음, 피어를 작성할 때 이 등록 ID 및 시크릿을 제공하십시오. 이 피어는 Org2과 여전히 연관되어 있으므로 드롭 다운 목록에서 `Org2 CA`, `Org2 MSP` 및 `Org2 Admin`을 선택하십시오. 이 새 피어에 `Org2 CA`로 등록할 수 있는 다른 관리자를 지정하도록 선택할 수 있지만 이는 선택사항입니다. 이 튜토리얼 시리즈에서는 각 피어 조직에 대한 단일 피어를 작성하는 프로세스만 표시합니다.
{:tip}

## 2단계: 거래할 수 있는 조직 목록에 조직 추가
{: #ibp-console-join-network-add-org2}

앞에서 설명한 바와 같이 피어 조직이 채널을 작성하거나 가입하려면 순서 지정 서비스에 피어 조직으로 알려져 있어야 합니다(순서 지정 서비스에 알려진 조직의 목록인 "컨소시엄"에 가입하는 것이라고도 함). 그 이유는 채널이 기술 레벨에서 순서 지정 서비스를 통한 피어 간의 **메시징 경로**이기 때문입니다. 피어가 한 채널에서 다른 채널로 전달되는 정보 없이 여러 채널에 가입할 수 있으므로 순서 지정 서비스도 데이터를 다른 채널의 조직에 노출하지 않고 채널을 통해 실행되는 여러 채널을 보유할 수 있습니다.

순서 지정 서비스 관리자만 피어 조직을 컨소시엄에 추가할 수 있으므로 다음 중 하나여야 합니다.

* 순서 지정 서비스 관리자**여야** 합니다. 이 경우 작성한 피어 조직을 컨소시엄에 직접 추가할 수 있습니다.
* 조직을 해당 컨소시엄에 추가할 순서 지정 서비스 관리자에게 MSP 정보를 **전송**해야 합니다.

다음 단계에서는 사용자가 제어하는 컨소시엄에 사용자가 작성한 피어 조직을 추가하는 방법을 보여줍니다. 순서 지정 서비스를 호스팅하는 콘솔로 조직을 내보내는 방법을 알아보려면 아래의 [조직 정보 내보내기](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote)를 참조하십시오.

### 피어의 조직을 내 콘솔 내의 순서 지정 서비스에 추가
{: #ibp-console-join-network-add-org2-local}

**가입하려는 순서 지정 서비스 및 채널이 콘솔에 이미 포함되어 있는 경우에만 다음 단계에 따르십시오.** 그렇지 않으면
[조직 정보를 내보내십시오](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

순서 지정 서비스 관리자만이 피어 조직을 컨소시엄에 추가할 수 있으므로 사용자가 순서 지정자 관리자여야 합니다. 즉, 사용자가 지갑 내에 순서 지정 서비스 조직 관리자 ID를 보유하고 있어야 합니다.

1. **노드** 탭으로 이동하십시오.
2. 사용할 순서 지정 서비스까지 아래로 스크롤한 후 이를 클릭하여 여십시오.
3. **컨소시엄 구성원** 아래에서 **조직 추가**를 클릭하십시오.
4. 드롭 다운 목록에서 `org2`를 나타내는 MSP인 `Org2 MSP`를 선택하십시오.
5. **조직 추가**를 클릭하십시오.

피어 조직을 채널에 추가하십시오.
1. **채널** 탭으로 이동하여 `channel1`을 클릭하십시오.
2. **설정** 아이콘을 클릭하여 채널을 업데이트하고 피어 조직을 채널에 추가하십시오.
3. **사용 가능한 순서 지정 서비스에서 선택** 드롭 다운 목록에서 `Ordering Service`가 선택되었는지 확인하십시오.
4. **채널 업데이트 수행자 MSP ID** 드롭 다운 목록에서 `org1MSP`가 선택되었는지 확인하십시오.
5. **사용 가능한 ID 연관** 드롭 다운 목록에서 `Org1Admin`이 선택되었는지 확인하십시오.
6. `Add organizations to the channel`이라는 제목의 섹션까지 아래로 스크롤하여 `Select a channel member` 드롭 다운 목록을 열고 피어 조직 MSP인 `Org2 MSP`를 선택하십시오.
7. **추가**를 클릭한 다음 해당 조직에 대한 권한을 지정하십시오. `Operator`가 채널을 업데이트할 수 있으므로 해당 권한을 권장합니다.
8. **채널 업데이트**를 클릭하십시오.

이 프로세스가 완료되면 `org2`가 `Ordering Service`에서 호스팅되는 채널을 작성하거나 가입할 수 있습니다. [3단계: 채널에 피어 가입](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2)으로 진행할 수 있습니다.

### 조직 정보 내보내기
{: #ibp-console-join-network-add-org2-remote}

**순서 지정 서비스 및 피어가 가입할 채널이 다른 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스에 상주하는 경우에만 이러한 단계를 수행하십시오.** 그렇지 않으면
[3단계:
채널에 피어 결합](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2)으로 건너뛸 수 있습니다.

조직 MSP 정의를 순서 지정 서비스 관리자에게 전송하고 아래 단계를 사용하여 컨소시엄에 추가되어야 합니다.

이러한 단계를 따르는 경우 사용자가 **피어 조직**의 관리자여야 합니다. 즉, 지갑 내에 피어 조직 관리자 ID를 보유하고 있어야 합니다.

1. **조직** 탭으로 이동하십시오. 내보내기에 사용 가능한 조직은 **사용 가능한 조직** 아래에서
볼 수 있습니다. 조직 타일 내의 **다운로드** 단추를 클릭하여
피어 조직의 MSP를 나타내는 JSON 구성 파일을 다운로드하십시오.
2. 대역 외 오퍼레이션의 순서 지정 서비스 관리자에게 이 파일을 전송하십시오.

### 조직 정의 가져오기
{: #ibp-console-join-network-import-remote-msp}

조직을 콘솔에 추가하려면 순서 지정 서비스 관리자가 이 JSON 파일을 가져와야 합니다.

1. **조직** 탭으로 이동하여 **MSP 정의 가져오기** 단추를 클릭하고
피어 조직 MSP 정의를 표시하는 JSON 파일을 선택하십시오.
2. **노드** 페이지로 이동하여 순서 지정 서비스 노드를 클릭하십시오. 순서 지정 서비스 패널의 **컨소시엄 구성원** 아래에서 **조직 추가**를 클릭하십시오. 사이드 패널이 열리면 **조직** 탭의
MSP 정의 목록에서 `Org2 MSP`를 선택하십시오.
3. **채널** 탭으로 이동하여 `channel1`을 클릭한 다음 **설정** 아이콘을 클릭하여 채널을 업데이트하고 피어 조직을 채널에 추가하십시오.
4. **사용 가능한 순서 지정 서비스에서 선택** 드롭 다운 목록에서 `Ordering Service`가 선택되었는지 확인하십시오.
5. **채널 업데이트 수행자 MSP ID** 드롭 다운 목록에서 `org1MSP`가 선택되었는지 확인하십시오.
6. **사용 가능한 ID 연관** 드롭 다운 목록에서 `Org1Admin`이 선택되었는지 확인하십시오.
7. `Add organizations to the channel`이라는 제목의 섹션까지 아래로 스크롤하여 `Select a channel member` 드롭 다운 목록을
열고 튜토리얼로 피어 조직 MSP인 `Org2 MSP`를 선택하십시오.
8. **추가**를 클릭한 다음 해당 조직에 대한 권한을 지정하십시오. `Operator`가 채널을 업데이트할 수 있으므로 해당 권한을 권장합니다.
9. **채널 업데이트**를 클릭하십시오.

순서 지정 서비스의 관리자가 피어 조직을 컨소시엄 및 채널에 가입시킨 후 사용자가 순서 지정 서비스를 콘솔로 가져와야 합니다. 그러면 순서 지정 서비스가 호스팅하는 채널에 가입할 수 있습니다. 순서 지정 서비스를 **가져오려면** 다음 단계를 완료하십시오.

**순서 지정 서비스 조직**의 관리자가 먼저 다음 단계를 완료해야 합니다.

1. **노드** 탭 내부의 순서 지정 서비스 노드로 이동하고 순서 지정 서비스 이름의 오른쪽에 있는 **설정** 아이콘을 클릭하여 사이드 패널을 여십시오. **조치** 아래의 **내보내기** 단추를 클릭하여 JSON 구성 파일을 다운로드하십시오.
2. 대역 외 오퍼레이션의 피어 조직에 이 파일을 전송하십시오. 그러면 피어 조직 관리자가 구성 파일을 사용하여 콘솔에 순서 지정 서비스를 추가할 수 있습니다.

### 다른 클러스터에서 순서 지정 서비스 가져오기
{: #ibp-console-join-network-import-remote-orderer}

**피어 조직**의 관리자인 경우 다음 단계를 수행하십시오.

1. **노드** 페이지로 이동하여 **순서 지정 서비스 추가**를 클릭하십시오.
2. **기존 순서 지정 서비스 가져오기** 아래에서 {{site.data.keyword.cloud_notm}}를 클릭하십시오.
3. 그런 다음 **JSON 업로드** 단추를 클릭하고 노드 자체를 나타내는 JSON을 선택하십시오.
4. 후속 단계에서 ID를 연관시키도록 요청된 경우 피어 조직 ID를 선택하십시오. 이 튜토리얼에서는 `Org2 Admin`입니다. **순서 지정 서비스 가져오기**를 클릭하십시오.

## 3단계: 채널에 피어 가입
{: #ibp-console-join-network-join-peer-org2}

이제 거의 완료되었습니다. 피어가 기존 채널에 가입할 수 있습니다. 채널의 구성원인 조직으로부터 대역 외의 `channel name`을 가져와야 합니다. **네트워크 빌드** 튜토리얼에서 `channel1`이라는 채널을 작성했습니다. 아직 해당 위치가 아닌
경우, 왼쪽 탐색에서 **채널** 탭으로 이동하십시오.

콘솔에서 다음 단계 수행:

1. **채널 가입** 단추를 클릭하여 사이드 패널을 실행하십시오.
2. `Ordering Service`라는 이름의 순서 지정 서비스를 선택하고 **다음**을 클릭하십시오.
3. 가입할 채널 이름인 `channel1`을 입력하고 **다음**을 클릭하십시오.
4. 채널에 가입시킬 피어를 선택하십시오. 이 튜토리얼의 목적에 따라 `Peer Org2`를 클릭하십시오.
5. **채널 가입**을 클릭하십시오.

Hyperledger Fabric [개인용 데이터 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html "개인용 데이터") 또는
[서비스 발견 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "서비스 발견") 기능을
사용하려면 **채널** 탭에서 조직 내의 앵커 피어를 구성해야 합니다. 콘솔의 **채널** 탭을 사용하여 개인용 데이터에 대한 앵커 피어를 구성하는 방법에 대한 자세한 정보는 [개인용 데이터](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)를 참조하십시오.

조직이 컨소시엄의 구성원이면 새 채널을 작성할 수도 있습니다. [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)에 설명된 대로 단계에 따라 [채널을 작성](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel)하십시오.

## 다음 단계
{: #ibp-console-join-network-next-steps}

채널에 피어를 가입시킨 후, 다음 단계에 따라
스마트 계약을 배치하고 블록체인에 트랜잭션 제출을 시작하십시오.

- 콘솔을 시작하여 [네트워크에 스마트 계약을 배치](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)하십시오.
- 스마트 계약을 설치하고 인스턴스화한 후에
[클라이언트 애플리케이션을 사용하여 트랜잭션을 제출](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK)하십시오.
- [상업 어음 샘플](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)을
사용하여 예제 스마트 계약을 배치하고 샘플 애플리케이션 코드에서 트랜잭션을 제출하십시오.

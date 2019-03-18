---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 용어집
{: #glossary}

이 주제에서는 이 문서에 나오는 {{site.data.keyword.blockchainfull}} 플랫폼 특정 용어를 정의합니다. 용어와 Hyperledger Fabric 개념과 관련된 용어의 용어집에 대해 더 자세히 알아보려면 [Hyperledger Fabric 용어집 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html)을 참조하십시오.
{:shortdesc}

## 구성원(Member)
{: #glossary-member}

"조직"이라고도 하는 블록체인 네트워크의 구성원은 임의 그룹의 구성원과 유사하며 네트워크 구조를 형성합니다. 구성원은 크게는 다국적 기업, 작게는 개인일 수 있습니다. 구성원은 서비스 제공자(예: 인증서 발행, 트랜잭션 유효성 검증/순서 지정) 또는 이용자로서 네트워크를 사용하는 권한을 구성원에게 부여하는 인증서를 사용하여 네트워크에 등록접수됩니다. 서비스 제공자는 트랜잭션 유효성 검증, 트랜잭션 순서 지정 및 인증서 관리 서비스가 포함된 기본 블록체인 서비스를 제공합니다. 이용자 구성원은 네트워크를 사용하여 분산 원장에 대해 트랜잭션을 호출합니다. 구성원은 여러 피어를 가질 수 있습니다.

## 네트워크 모니터(Network Monitor)
{: #glossary-network-monitor}
{{site.data.keyword.blockchainfull_notm}} Platform이 블록체인 네트워크를 보고 관리하기 위해 제공하는 GUI 대시보드입니다.

## 네트워크 인증 정보(Network Credentials)
{: #glossary-network-credentials}
네트워크 모니터의 "API" 화면에 표시됩니다. 인증 정보에는 Swagger UI의 "키"(사용자 이름) 및 "시크릿"(비밀번호)이 포함됩니다. REST API를 시험 사용하기 전에 이러한 네트워크 인증 정보를 사용하여 인증해야 합니다.

## 네트워크(Network)
{: #glossary-network}
{{site.data.keyword.cloud_notm}}에 있는 {{site.data.keyword.blockchainfull_notm}} Platform 서비스의 인스턴스입니다.

## 노드(Node)
{: #glossary-node}
블록체인의 통신 엔티티입니다. 세 가지 유형의 노드, 즉 CA, 피어 및 순서 지정자가 있습니다.

## 동적 멤버십(Dynamic membership)
{: #glossary-dynamic-memership}
**등록자** 권한을 가진 사용자가 네트워크에 구성원을 동적으로 추가할 수 있습니다. 또한 구성원에게는 네트워크의 액세스 및 권한을 제어하는 역할 및 속성이 지정됩니다. 하지만 역할이나 속성 모두 동적으로 지정할 수 없습니다. Hyperledger Fabric은 전체 네트워크 운영에 부정적인 영향을 주지 않고 구성원, 피어 및 순서 지정 서비스 노드 추가 또는 제거를 지원합니다. 동적 멤버십은 여러 이유로 비즈니스 관계가 조정되고 엔티티를 추가하거나 제거해야 하는 경우에 중요합니다.

## 보증 정책(Endorsement policy)
{: #glossary-endorsement-policy}
특정 체인코드 애플리케이션에 연결된 트랜잭션을 실행해야 하는 채널의 피어 노드 및 필수 응답 조합(보증)을 정의합니다. 정책에서는 최소 수의 보증 피어, 최소 백분율의 보증 피어 또는 특정 체인코드 애플리케이션에 지정된 모든 보증 피어로 트랜잭션을 보증해야 합니다. 정책은 의도적인지 여부에 관계없이 잘못된 동작에 대한 보증 피어의 원하는 레벨의 복원력 및 애플리케이션을 기반으로 체계화할 수 있습니다. 제출된 트랜잭션은 피어를 커미트하여 올바른 것으로 표시되기 전에 보증 정책을 충족해야 합니다. 트랜잭션을 설치하고 인스턴스화하기 위한 개별 보증 정책도 필요합니다.

## 보증(Endorsement)
{: #glossary-endorsement}
체인코드 트랜잭션을 유효성 검증하는 프로세스입니다. 보증 정책을 지정하여 보증 규칙을 구현합니다.

## 블록(Block)
{: #glossary-block}
순서 지정된 트랜잭션 세트이며, 체인의 이전 블록에 암호를 사용하여 링크되어 있습니다.

## 비즈니스 네트워크(Business network)
{: #glossary-business-network}
블록체인 네트워크의 정의이며, 블록체인 솔루션을 위한 데이터 모델, 트랜잭션 로직 및 액세스 제어 규칙이 포함됩니다. [Hyperledger Composer](/docs/services/blockchain/glossary.html#glossary-composer)를 사용하여 비즈니스 네트워크 정의를 작성합니다. 비즈니스 네트워크 정의는 배치 가능 **.bna**(비즈니스 네트워크 아카이브) 파일로 패키지됩니다.

## 사용자(User)
{: #glossary-user}
사용자는 기존 구성원에 대한 신뢰 관계를 통해 원장에 간접적으로 액세스할 수 있는 블록체인 네트워크의 참가자입니다.

## 상태 데이터베이스(State database)
{: #glossary-state-database}
현재 상태 데이터는 체인코드에서 효율적인 읽기 및 조회를 위해 피어의 데이터베이스에 저장됩니다. 스타터 플랜 네트워크는 CouchDB를 상태 데이터베이스로 사용합니다. 엔터프라이즈 플랜 네트워크는 LevelDB 또는 CouchDB를 사용할 수 있습니다.

## 서비스 인증 정보(Service credentials)
{: #glossary-service-credentials}
서비스 인증 정보는 JSON 형식이며 CA, 순서 지정자 및 피어 등의 네트워크 리소스에 대한 API 엔드포인트 정보 및 enrollID/시크릿을 포함합니다. 애플리케이션은 이러한 API 엔드포인트를 통해 네트워크 리소스와 상호작용합니다.

## 설치(Install)
{: #glossary-install}
피어 파일 시스템에 체인코드를 배치하는 프로세스입니다. 이 체인코드를 실행할 모든 피어에 체인코드를 설치해야 합니다.

## 세계 상태(World state)
{: #glossary-world-state}
[현재 상태(Current state)]((/docs/services/blockchain/glossary.html#glossary-current-state)를 참조하십시오.

## 순서 지정자(Orderer)
{: #glossary-orderer}
네트워크 구성원의 트랜잭션을 수집하고 트랜잭션의 순서를 지정하고 블록으로 번들화하는 노드입니다. 그런 다음 이러한 블록이 피어에 분배되고 피어는 블록을 확인한 다음 각 채널의 원장에 추가합니다. 순서 지정자는 각 구성원에 연결된 암호 ID 자료를 포함하고 클라이언트 및 피어의 ID를 인증하여 네트워크에 액세스합니다. 노드의 순서 지정 노드 또는 콜렉션에서 제공하는 전체 기능을 **순서 지정 서비스**라고 합니다.

## 스마트 계약(Smart contracts)
{: #glossary-smart-contracts}
[체인코드(Chaincode)](/docs/services/blockchain/glossary.html#glossary-chaincode)를 참조하십시오.

## 연결 프로파일(Connection profile)
{: #glossary-connection-profile}
**연결 프로파일** 단추를 클릭하면 네트워크 모니터의 "개요" 화면에 연결 프로파일이 표시됩니다. 정보는 JSON 형식으로 사용 가능하며 피어, 순서 지정자 및 CA 등의 네트워크 리소스에 대한 API 엔드포인트 정보 및 enrollID/시크릿을 포함합니다. 애플리케이션은 이러한 API 엔드포인트를 통해 네트워크 리소스와 상호작용합니다.

## 원장(Ledger)
{: #glossary-ledger}
변경할 수 없는 순차 트랜잭션 레코드 및 현재 상태를 유지하는 상태 데이터베이스를 저장하는, 문자 그대로 "블록의 체인"으로 구성됩니다. 채널당 하나의 원장이 있으며 이에 대한 업데이트는 특정 채널의 정책에 따라 합의 프로세스를 통해 관리됩니다.

## 인스턴스화(Instantiate)
{: #glossary-instantiate}
특정 채널에서 체인코드 컨테이너를 시작하고 초기화하는 프로세스입니다. 체인코드를 피어가 설치하고 모든 피어가 채널에 가입한 후 채널에서 체인코드를 인스턴스화해야 합니다. 인스턴스화는 체인코드의 필수 초기화를 수행하며, 여기에는 체인코드의 초기 세계 상태를 구성하는 키 값 쌍 설정이 포함됩니다. 인스턴스화 후에 체인코드가 설치된 피어는 체인코드 호출을 허용할 수 있습니다.

## 자산(Asset)
{: #glossary-asset}
블록체인 네트워크에서 거래되는 항목으로 표시되는 유무형의 상품, 서비스 또는 부동산입니다.

## 조직(Organization)
{: #glossary-organization}
[구성원(Member)](/docs/services/blockchain/glossary.html#glossary-member)을 참조하십시오.

## 참가자(Participant)
{: #glossary-participant}
블록체인 네트워크와 상호작용하는 조직, 개인, 애플리케이션 또는 디바이스입니다. 참가자 개념 아래에는 두 개의 개별 그룹(구성원과 사용자)이 있습니다.

## 채널(Channel)
{: #glossary-channel}
개인적으로 거래하려는 네트워크 구성원의 서브세트로 구성됩니다. 채널은 채널 구성원이 채널 구성원만 액세스할 수 있는 특정 규칙 및 개별 원장을 설정할 수 있게 하여 데이터 격리 및 기밀성을 제공합니다. 조직의 트랜잭션 엔드포인트로 기능하는 노드인 피어가 채널에 가입됩니다.

## 체인(Chain)
{: #glossary-chain}
원장의 체인은 트랜잭션의 해시 링크 블록으로 구조화된 트랜잭션 로그입니다. 피어는 순서 지정 서비스에서 트랜잭션의 블록을 수신하고, 보증 정책 및 동시성 위반에 따라 블록의 트랜잭션을 올바르거나 올바르지 않은 것으로 표시하며, 피어 파일 시스템의 해시 체인에 블록을 추가합니다.

## 체인코드(Chaincode)
{: #glossary-chaincode}

**스마트 계약**이라고도 하는 체인코드는 원장을 조회하거나 업데이트하는 기능 세트가 포함된 소프트웨어의 부분입니다.

## 최초 블록(Genesis Block)
{: #glossary-genesis-block}
블록체인 네트워크 또는 채널을 초기화하고 블록에서 첫 번째 블록의 역할을 하는 구성 블록입니다.

## 컨소시엄(Consortium)
{: #glossary-consortium}
순서 지정자 시스템 채널에 나열된 순서 지정자가 아닌 조직의 그룹입니다. 채널을 작성할 수 있는 유일한 조직입니다. 채널 작성 시, 채널에 추가된 모든 조직은 컨소시엄의 일부여야 합니다. 그러나 컨소시엄에 정의되지 않는 조직이 기존 채널에 추가될 수 있습니다. 블록체인 네트워크에는 여러 개의 컨소시엄이 있을 수 있지만 대부분의 블록체인 네트워크에는 단일 컨소시엄이 있습니다.

## 클라이언트(Client)
{: #glossary-client}
클라이언트는 사용자를 대행하는 엔티티를 나타냅니다. 블록체인과 통신하기 위해 피어에 연결되어야 합니다. 클라이언트는 선택한 피어에 연결할 수 있습니다. 클라이언트는 트랜잭션을 작성하고 호출합니다. 클라이언트는 양도인에게 실제 트랜잭션 호출을 제출하고 순서 지정 서비스로 트랜잭션 제안을 브로드캐스트합니다.

## 트랜잭션(Transaction)
{: #glossary-transaction}
블록체인 네트워크의 참가자가 자산과 상호작용하는 데 사용하는 메커니즘입니다. 트랜잭션은 새 체인코드를 작성하거나 기존 체인코드에서 오퍼레이션을 호출합니다.

## 피어(Peer)
{: #glossary-peer}
트랜잭션을 실행하고 유효성 검증하며 원장을 유지보수하는 서비스를 제공하는 블록체인 네트워크 리소스입니다. 피어는 체인코드를 실행하며 트랜잭션 히스토리 및 네트워크 채널에 있는 자산의 현재 상태(즉, 원장)의 소유자입니다. 이러한 피어는 조직에서 소유하고 관리하며 채널에 가입됩니다.

## 합의(Consensus)
{: #glossary-consensus}
원장 트랜잭션이 네트워크에서 동기화 상태를 유지하게 하는 협업 프로세스입니다. 합의를 통해 원장은 적절한 참가자가 트랜잭션을 승인하는 경우에만 업데이트되고 동일한 순서로 동일한 트랜잭션을 사용하여 업데이트됩니다. 합의에 도달하는 알고리즘 방식은 여러 가지가 있습니다.

## 현재 상태(Current state)
{: #glossary-current-state}

원장의 현재 상태는 해당 체인 트랜잭션 로그에 포함된 모든 키에 대한 최신 값을 나타냅니다. 현재 상태는 채널에 알려진 모든 최신 키 값을 나타내기 때문에 세계 상태라고도 합니다. 체인코드는 현재 상태 데이터에 대해 트랜잭션 제안을 실행합니다. 현재 상태는 키 값이 변경되거나 새 키가 추가될 때마다 변경됩니다. 최신 키-값 쌍은 변경되기 전에 알려져야 하므로 현재 상태는 트랜잭션 플로우에 매우 중요합니다. 피어는 블록의 각 유효한 트랜잭션에 대한 원장의 현재 상태에 최신 값을 커미트합니다. 현재 상태는 피어의 상태 데이터베이스에 저장됩니다.

## CA
{: #glossary-CA}
"인증 기관(Certificate Authority)"의 약어로, 참가하는 모든 구성원에게 인증서를 발행하는 컴포넌트입니다. 이러한 인증서는 구성원의 ID를 나타냅니다. 네트워크의 모든 엔티티(피어, 순서 지정자, 클라이언트 등)는 통신하고 인증하고 결과적으로 거래하기 위한 ID를 가져야 합니다. 이러한 ID는 블록체인 네트워크에 직접 참가하는 데 필요합니다.

## CouchDB
{: #glossary-couchdb}
스타터 플랜 네트워크의 상태 데이터베이스에 사용되는 문서 저장소입니다. 또한 CouchDB는 LevelDB와 함께 엔터프라이즈 플랜 네트워크에 대한 옵션입니다. CouchDB는 인덱스 사용을 지원하고 사용자가 피어의 데이터에 대해 다양한 조회를 실행할 수 있게 합니다.


## Gossip
{: #glossary-gossip}
Hyperledger Fabric을 사용하면 피어가 순서 지정 서비스에 의존하지 않고 서로 중요한 네트워크 정보를 수집할 수 있습니다. [gossip 데이터 전파 프로코톨 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/gossip.html)에서는 피어에서 서로 메시지를 교환하는 안전하고 신뢰할 수 있으며 확장 가능한 방법을 제공합니다. 예를 들어, 지연, 네트워크 가동 중단 또는 다른 이유로 인해 피어에서 일부 블록이 누락된 경우 gossip 메시징을 사용하여 누락된 블록을 보유하고 있는 다른 피어와 연락하여 현재 원장 상태와 동기화할 수 있습니다.

## HSM
{: #glossary-hsm}
하드웨어 보안 모듈(Hardware Security Module)입니다. On-Demand 암호화, 키 관리 및 키 스토리지를 관리 서비스로 제공합니다. HSM은 암호화 처리의 리소스 집약적 태스크를 처리하는 물리적 어플라이언스이며 애플리케이션에 대한 대기 시간을 줄입니다. 자세한 정보는 [하드웨어 보안 모듈 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/cloud/hardware-security-module)을 참조하십시오.

## Hyperledger Composer
{: #glossary-composer}
**IBM은 Composer CLI, JavaScript API, REST 서버 및 웹 플레이그라운드를 포함하여 프로덕션에서 Hyperledger Composer를 사용하는 네트워크를 지원하지 않습니다.**
[Hyperledger Composer ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)는 오픈 소스 개발 도구 세트입니다. 이는 맞춤형 모델링 언어를 사용하며, 이 언어는 JavaScript 트랜잭션 및 액세스 제어 규칙과 결합되어 전체 블록체인 비즈니스 네트워크를 모델링합니다. 실제 블록체인에 무언가를 배치하기 전에 Hyperledger Composer를 사용하여 기존 시스템 및 데이터를 블록체인 애플리케이션과 통합할 수 있습니다.

## Hyperledger Fabric
{: #glossary-hyperledger-fabric}
[Hyperledger Fabric ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/)은 모듈식 아키텍처로 블록체인 애플리케이션 또는 솔루션을 개발하기 위한 기초적인 역할을 하도록 Linux Foundation이 호스팅하는 비즈니스 블록체인 프레임워크입니다. 합의 및 멤버십 서비스와 같은 Hyperledger Fabric 컴포넌트는 플러그 앤 플레이입니다.

## Kafka
{: #glossary-kafka}
Hyperledger Fabric에 대한 합의 플러그인 구현이며 블록체인 네트워크에 순서 지정 서비스 노드의 클러스터로 나타납니다. Kafka 구현은 프로덕션 네트워크를 대상으로 합니다.

## LevelDB
{: #glossary-leveldb}
CouchDB와 함께 엔터프라이즈 플랜 네트워크의 상태 데이터베이스에 대한 옵션인 키-값 저장소입니다. LevelDB는 현재 상태를 키-값 쌍으로 저장하며, 인덱스 또는 다양한 조회 사용을 지원하지 않습니다.

## MSP
{: #glossary-msp}
멤버십 서비스 제공자(Membership Service Provider)입니다. 블록체인 네트워크 전체에서 인증서 및 ID를 발행하고 유효성 검증하기 위한 암호 메커니즘 및 프로토콜 세트입니다. 멤버십 서비스 제공자 범위에 발행된 ID는 멤버십 서비스 제공자의 규칙 유효성 검증 정책 내에서 평가할 수 있습니다. MSP는 각 채널 피어에 설치되어 피어에 발행된 트랜잭션 요청이 인증되고 권한이 부여된 사용자 ID에서 시작하는지 확인합니다.

## SDK
{: #glossary-sdk}
Hyperledger Fabric은 두 개의 SDK(Software Development Kit)를 지원합니다 (노드 SDK 및 Java SDK).  노드 SDK는 NPM을 통해 설치 가능하고 Java SDK는 Maven을 통해 설치 가능합니다.  SDK에는 사용 가능한 API의 문서와 함께 고유 git 저장소, 즉 [Fabric 노드 SDK ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric-sdk-node) 및 [Fabric Java SDK ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric-sdk-java)가 있습니다. Hyperledger Fabric Client SDK는 클라이언트 애플리케이션과 블록체인 네트워크 간 상호작용을 사용 가능하게 합니다.

## SignCert
{: #glossary-sign-cert}
조직 또는 관리자가 제안 또는 제안 응답에 접속하는 모든 엔티티의 인증서입니다. 이러한 signCert는 엔티티에 고유하고 순서 지정 서비스에서 해당 엔티티의 파일에 있는 signCert와 일치하는지 확인합니다.


## SOLO
{: #glossary-solo}
Hyperledger Fabric에 대한 합의 플러그인 구현이며 블록체인 네트워크에 단일 순서 지정 서비스 노드로 나타납니다. 스타터 플랜 네트워크는 SOLO 구현을 사용합니다. SOLO 구현은 프로덕션 네트워크에는 사용되지 않습니다. SOLO의 대안은 Kafka 클러스터입니다.





---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# 네트워크 튜토리얼 빌드
{: #ibp-console-build-network}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform은
블록체인 애플리케이션 및 네트워크를 개발, 배치 및 운영할 수 있게 해주는 블록체인 서비스 제공
오퍼링입니다. [블록체인 컴포넌트 개요](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview)를
방문하여 블록체인 컴포넌트 및 함께 작동하는 방법에 대해 배울 수 있습니다. 이 튜토리얼은 [샘플 네트워크 튜토리얼 시리즈](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial)의 첫 번째 부분이며 {{site.data.keyword.cloud_notm}}에 배치된 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 단일 {{site.data.keyword.cloud_notm}} Kubernetes 서비스에 완전한 기능의 네트워크를 빌드하는 방법에 대해 설명합니다.
{:shortdesc}

아직 {{site.data.keyword.cloud_notm}} Kubernetes 서비스를 사용하여 Kubernetes 클러스터에 콘솔을 배치하지 않은 경우, [{{site.data.keyword.blockchainfull_notm}} Platform 2.0으로 시작하기](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)를 참조하십시오. 콘솔 배치를 위해 새 Kubernetes 클러스터를 작성하거나 {{site.data.keyword.cloud_notm}} 계정 내의 기존 클러스터를 사용할 수 있습니다. {{site.data.keyword.blockchainfull}} Platform을 Kubernetes 클러스터에 배치한 후에 콘솔을 실행하여 블록체인 컴포넌트를 작성하고 관리할 수 있습니다.

유료 또는 무료 Kubernetes 클러스터에 배치했는지에 상관없이, Kubernetes 대시보드를 사용하여 노드를 배치하고 채널을 작성할 때 원하는 대로 사용 가능한 리소스에 세심한 주의를 기울이십시오. Kubernetes 클러스터를 관리하고 필요에 따라 추가 리소스를 배치하는 것은 사용자의 책임입니다. 컴포넌트가 무료 클러스터에 성공적으로 배치되었으나 사용자가 더 많은 컴포넌트를 추가하면 컴포넌트 실행이 느려질 수 있습니다.
{: note}

## 샘플 네트워크 튜토리얼 시리즈
{: #ibp-console-build-network-sample-tutorial}

이 세 부분으로 구성된 튜토리얼은 콘솔을 사용하고 Kubernetes 배치를 활용하여 상대적으로 단순한 다중 노드 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 네트워크를 작성하고 상호연결하는 과정을 소개합니다.

* [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은 순서 지정자 또는 피어를 작성하여 네트워크를 호스팅하는 프로세스를 안내합니다.
* [네트워크 가입 튜토리얼](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network)은 피어를 작성하고 해당 피어를 채널에 가입하여 기존 네트워크에 가입하는 프로세스를 안내합니다.
* [네트워크에 스마트 계약 배치 튜토리얼](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)은 스마트 계약을 작성하여 이를 네트워크에 배치하는 방법을 제공합니다.

이러한 튜토리얼의 단계를 사용하여 개발 및 테스트 목적의 한 클러스터에 다중 조직이 있는 네트워크를 빌드할 수 있습니다. 블록체인 컨소시엄을 찾고 다른 구성원이 **네트워크에 가입** 튜토리얼을 사용하여 다른 클러스터에서 연결하도록 하려면 **네트워크 빌드** 튜토리얼을 사용하십시오. 다른 컨소시엄 구성원과 함께 튜토리얼에 따라 **분산** 블록체인 네트워크를 작성할 수 있습니다.

### 이 네트워크의 구조
{: #ibp-console-build-network-structure}

**네트워크 빌드** 및 **네트워크 가입** 튜토리얼을
모두 완료했으면 네트워크가 아래 그림 중 하나와 유사하게 표시됩니다.
![샘플 기본 네트워크 구조](../images/ibp-v2-build-network.png "샘플 기본 네트워크 구조")  
*그림 1. 샘플 기본 네트워크 구조*  
이 구성은 애플리케이션 및 스마트 계약을 테스트하는 데 충분합니다. 네트워크는 다음 컴포넌트를 포함합니다.

* **두 개의 피어 조직**: `조직1` 및 `조직2`(`Org1` 및 `Org2`)  
  튜토리얼 시리즈는 두 개의 피어 조직 및 두 개의 연관된 피어를 작성하는 방법에 대해 설명합니다. 블록체인
네트워크의 조직이 서로 거래해야 하는 두 개의 서로 다른 은행과 같다고
생각해 보십시오. 조직 `Org1` 및 `Org2`를
정의하는 Org1 및 Org2 MSP(Membership Services Provider) 정의를 작성합니다.
* **하나의 순서 지정자 조직**: `순서 지정자 조직`(`Orderer Org`)  
  분산 원장을 빌드하고 있으므로 피어 및 순서 지정자가 별도 조직의 일부여야 합니다. 따라서 순서 지정자에
대해 별도의 조직이 작성됩니다. 순서 지정자 노드는 원장에 기록되어 블록체인이 되기 위해 피어로 전송될
트랜잭션 블록의 순서를 지정합니다. 조직 `Orderer Org`을 정의하는 순서 지정자 MSP(Membership Services Provider) 정의를 작성합니다.
* **세 개의 인증 기관(CA)**: `조직1 CA`, `조직2 CA`, `순서 지정자 CA`(`Org1 CA, Org2 CA, Orderer CA`)   
  CA는 모든 조직 구성원에게 인증서를 발행하는 노드입니다. 조직당 하나의 CA를
배치하는 것이 우수 사례이므로 각 조직당 하나, 순서 지정자 조직당 하나, 즉, 총 세 개의 CA를 배치할 것입니다. 또한
CA를 사용하여 각 조직에 대한 모든 노드, ID 및 조직 정의를 작성할 것입니다.
* **하나의 순서 지정자:** `순서 지정자`(`Orderer`)  
  현재, SOLO 순서 지정 서비스만이 콘솔을 사용하여 배치될 수 있습니다. 클러스터에 공간이 있으면
이러한 노드를 둘 이상 배치할 수 있지만 각 순서 지정자가 자체 "컨소시엄", 즉,
작성될 수 있고 채널에 가입할 수 있는 피어 조직 목록을 갖게 됩니다. 다중 순서 지정자를
동일한 채널에 연결할 수 없습니다. 이 순서 지정자의 관리자로서
컨소시엄을 작성하기 위해 사용한 피어 조직을 추가할 수 있으며
이로 인해 사용자의 피어 조직이 채널을 작성할 수 있습니다. 조직이 다른
클러스터에 배치된 채널을 작성하려면(대부분의 프로덕션 네트워크가 구성되는 방법),
다른 콘솔에 배치된 피어 조직을 사용자의 콘솔로 가져와야 합니다. 그러면
피어 조직이 순서 지정 서비스가 호스팅하는 채널에 가입할 수 있습니다.
* **두 개의 피어:** `피어 조직1` 및 `피어 조직2`(`Peer Org1` 및 `Peer Org2`)  
  위 그림에서 블록체인 원장 `원장 x`는 분산 피어에 의해 유지보수됩니다. 이러한 피어는
[Couch DB ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html)가 데이터베이스로서 함께 배치됩니다.
* **하나의 채널**: `채널1`(`channel1`)  
  채널은 데이터 개인정보 보호를 제공합니다. 조직 세트가 채널의 구성원이 아닌
조직에 데이터를 노출하지 않고 거래할 수 있도록 해줍니다. 각 채널은
해당 채널에 가입한 피어 간에 공유되는 자체 블록체인 원장을 가집니다. 튜토리얼은
두 조직 모두가 가입한 하나의 채널을 작성하고 조직이 거래에 사용할 수 있는
채널에서 스마트 계약을 인스턴스화합니다.

이 구성은 필수가 아닙니다. {{site.data.keyword.blockchainfull_notm}} Platform 2.0은 고도로 사용자 정의할 수 있습니다. Kubernetes 클러스터에서
사용할 수 있는 리소스가 있으면 많은 피어 조직이 있는 콘솔에서 설립한 컨소시엄을 사용할 수 있습니다. 또한
다중 순서 지정 서비스에 연결된 피어 조직을 작성할 수 있습니다. 이 튜토리얼에서는
자체 네트워크를 빌드하는 데 필요한 단계를 제공하며 {{site.data.keyword.blockchainfull_notm}} Platform 및 콘솔에
대한 세부사항을 제공하는 주제에 대한 참조를 제공합니다.


이 **네트워크 빌드** 튜토리얼에서는
위 네트워크, 즉, 순서 지정자, 단일 피어 조직 및 단일 채널의 피어를 호스팅하는 데 사용되는 단순 네트워크의
일부만 빌드합니다. 다음은 빌드할 위 네트워크의 일부를 표시합니다. </br>
![단순 네트워크 구조](../images/ibp2-simple-network.png "단순 네트워크 구조")  
*그림 2. 단순 네트워크 구조*  
이 구성은 스마트 계약을 신속하게 시작하고 테스트하는 데 유용하지만 사용자가 완전한 분산 원장을 작성하여
거래할 기타 조직을 추가하기 전에는 그다지 의미가 없습니다. 따라서 후속
[네트워크 가입](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) 튜토리얼에서
추가 피어 조직 및 피어를 작성하고 새 조직을 채널에 추가하는 방법에 대해 설명합니다.  

이 튜토리얼 전체에 걸쳐 콘솔 내의 일부 필드에 대해 **권장되는 값**을 제공합니다. 이로 인해
탭 및 드롭 다운 목록에서 이름 및 ID를 쉽게 인식할 수 있습니다. 이러한 값은 필수가 아니나 찾는 데 도움이 됩니다. 각 태스크 후에 권장되는 값의 표를 제공합니다.
{:tip}

## 1단계: 조직 및 블록체인의 시작점 작성
{: #ibp-console-build-network-create-peer-org1}

콘솔을 사용하여 작성할 각 조직에 대해 하나 이상의 CA를 배치해야 합니다. CA는 모든 조직 구성원(피어, 순서 지정자, 클라이언트 등)에게 인증서를 발행하는
노드입니다. 공개 및 개인 키 쌍을 포함하는 이러한 인증서는 네트워크 참가자가 통신, 인증 및 궁극적으로 트랜잭션을 수행할 수 있도록 허용합니다. 이러한
CA는 조직 자체를 정의하는 것 외에도 조직에 속한 모든 ID 및 인증서를 작성합니다. 그러면 이러한 ID를 사용하여 노드를 배치하고 네트워크를 운영하며
블록체인에 트랜잭션을 제출할 수 있습니다. 작성해야 하는 CA 및 ID에 대한 자세한 정보는
[ID 관리](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities)를 참조하십시오.

이 튜토리얼에서는 두 개의 조직을 작성합니다. 하나는 피어를 소유하고 다른 하나는 순서 지정자를 소유하게 됩니다. 각 조직이 인증서를 발행하려면 CA가 필요하므로 **두 개의 CA**를 작성해야
합니다. 이 튜토리얼의 목적을 위해 **한 번에 하나의 CA만 작성할 것입니다**.

### 피어 조직 CA 작성
{: #ibp-console-build-network-create-CA-org1CA}

이 튜토리얼의 일부로, CA가 사용자 및 노드에 대한 공개 및 개인 키를 발행합니다. 이러한 ID는
{{site.data.keyword.IBM_notm}}에 의해 관리되지 않으며 키는 사용자의 Kubernetes 클러스터 또는 콘솔에 저장되지
않습니다. 브라우저의 로컬 스토리지에만 저장됩니다. 따라서 사용자의 ID와 조직 MSP를 내보내야 합니다. 다른 시스템
또는 다른 브라우저에서 콘솔에 액세스하려고 시도하는 경우 이러한 ID 및 조직 정의를 가져와야 합니다.  
{:important}

콘솔에서 다음 단계 수행:

1. 왼쪽의 **노드** 탭으로 이동하여 **인증 기관 추가**를 클릭하십시오. 4단계
패널을 사용하여 작성할 CA를 사용자 정의할 수 있으며 이 CA가 키를 발행할 조직을 사용자 정의할 수 있습니다.
2. **인증 기관 작성** 아래의 **{{site.data.keyword.cloud_notm}}**를 클릭하고 **다음**을 클릭하십시오.
3. 두 번째 사이드 패널을 사용하여 CA의 **표시 이름**을 제공하십시오. 이 CA에 대한 권장 값은 `Org1 CA`입니다.
4. 다음 패널에서 `admin`의 **관리자 ID**를 지정하고 원하는 시크릿을 제공하여 CA 관리자 인증 정보를
제공하십시오. 이 튜토리얼을 참조하는 데 도움이 되도록 `adminpw`를 사용하도록 권장합니다.
5. **제출**을 클릭하십시오.

**태스크: 피어 조직 CA 작성**

  | **필드** | **표시 이름** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|
  | **CA 작성** | Org1 CA  | admin | adminpw |

*그림 3. 피어 조직 CA 작성*

CA를 배치한 후에 조직 MSP를 작성하고 사용자를 등록하고 네트워크, **peer**에
시작점을 작성할 때 이를 사용할 것입니다.

### ID를 등록하기 위해 CA 사용
{: #ibp-console-build-network-use-CA-org1}

작성할 각 노드 또는 애플리케이션에는 블록체인 네트워크에 참여할 공개 및 개인 키가 필요합니다. 또한 이러한 노드 및 애플리케이션에 대한 관리 키를 작성해야만 콘솔에서 관리 키를 관리할 수 있습니다. 작성한 각 CA에 대해 한 번씩, 총 두 번 이 프로세스를 수행할 것입니다. 각 CA에 두 개의 ID를 ID를 작성할 것입니다.

* **조직 관리자**. 이 ID를 사용하면 플랫폼 콘솔을 사용하여 노드를 작동할 수 있습니다.
* **피어 ID**. 이 ID를 사용하면 피어를 배치할 수 있습니다.

이러한 인증서를 생성하려면 다음 단계를 완료해야 합니다.

1. 콘솔에서 **노드** 탭을 사용하여 사용자가 작성한 `Org1 CA`로 이동하십시오.
2. 사용자의 CA를 선택한 후에 피어 자체에 대한 ID 외에 관리자를 첫 번째 조직인 `org1`에 등록해야 합니다. 이미
이 페이지에 ID가 표시되어야 하며 해당 ID는 사용자가 CA에 대해 작성한 관리자입니다. 새 사용자를 등록하려면
**사용자 등록** 단추를 클릭하십시오.
3. 조직 관리자의 경우, 등록 ID인 `org1admin`을 제공하십시오. 임의의 시크릿을 사용할 수 있으나 이해를 돕기 위해 `org1adminpw`를 사용하도록 권장합니다.**다음**을 클릭하십시오.
4. 다음 단계에서는 이 ID의 유형을 `client`로 설정하고
드롭 다운 목록에서 소속된 조직을 선택하십시오. 소식 필드는 고급 사용자용이며 이 튜토리얼에서는 사용하지 않습니다. 목록의 항목은 Fabric CA의 기본 소속입니다. Fabric CA가
소속을 사용하는 방법에 대한 자세한 정보는 [새 ID 등록 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity)에 대한
주제를 참조하십시오. 이제 목록에서 `org1` 등의 임의의 소속을 선택하고 **다음**을 클릭하십시오.
5. **최대 등록 수** 및 **속성 추가** 필드는 공백으로 둘 수 있습니다. 두 필드는
이 튜토리얼에서는 사용하지 않습니다. 단, [ID 등록](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)에서
이 주제에 대해 자세히 볼 수 있습니다.
6. 조직 관리자가 등록된 후 피어의 ID에 대해 동일한 `Org1 CA`를 사용하고 등록 ID `peer1`및 원하는 임의의 시크릿을 제공하여 이 동일한 프로세스를 반복하십시오. 이전과 같이 `peer1pw` 시크릿을
권장합니다. 이는 노드 ID이므로 다음 단계에서 **유형**으로 `peer`를 선택하십시오. 임의의 **소속**을 선택하십시오. 그런 다음,
**최대 등록 수** 및 **소속**을 무시하십시오.

**태스크: 사용자 등록**

  |  **필드** | **설명** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **사용자 등록** |  Org1 admin | org1admin | org1adminpw |
  | | 피어 ID |  peer1 | peer1pw |

*그림 4. CA를 사용하여 사용자 등록*

### 피어 조직 MSP 정의 작성
{: #ibp-console-build-network-create-peers-org1}

피어의 CA를 작성하여 이를 사용하여 조직 ID를 **등록**했습니다. 이제
피어 조직의 정규 정의, 즉, MSP(Membership Services Provider)를 작성해야 합니다. 많은 피어가 조직에 속할 수 있습니다. **피어를
작성할 때마다 새 조직을 작성할 필요는 없습니다.** 튜토리얼을 수행함에 있어서 이번이 처음이므로
이 조직의 MSP ID를 작성할 것입니다. MSP를 작성하는 과정에서 `org1admin` ID에 대한
인증서를 작성하여 콘솔 지갑에 추가할 것입니다.

1. 왼쪽 탐색의 **조직** 탭으로 이동하여 **MSP 정의 작성**을 클릭하십시오.
2. MSP에 표시 이름인 `Org1 MSP` 및 MSP ID인 `org1msp`를 제공하십시오. 이 필드에
사용자 고유의 MSP ID를 지정하려면 도구 팁에서 이 이름에 대한 제한사항 스펙을 확인하십시오.
3. **루트 인증 기관 세부사항** 아래에서 사용자의 조직에 대한 루트 CA로 작성한 피어 CA를 지정하십시오. 이 튜토리얼을 처음 사용하는 경우에는 `Org1 CA`만 표시되어야 합니다.
4. 이 아래의 **등록 ID** 및 **등록 시크릿** 필드가
사용자가 CA를 사용하여 작성한 첫 번째 사용자의 등록 ID 및 시크릿으로 자동으로 채워집니다. 이러한 값을 사용하지 마십시오. 대신,
조직 관리자에 대한 등록 ID 및 시크릿, 즉, `org1admin` 및 `org1adminpw`를 제공하십시오. 그런 다음,
이 ID에 대해 표시 이름인 `Org1 Admin`를 제공하십시오.
5. **생성** 단추를
클릭하여 이 ID를 조직의 관리자로 등록하고 ID를 지갑으로 내보내십시오.
사용자가 피어 및 채널을 작성할 때 사용됩니다.
6. **내보내기**를 클릭하여 관리자 인증서를 파일 시스템으로 내보내십시오. 위에서 설명한 대로
이 ID는 클러스터에 저장되거나 {{site.data.keyword.IBM_notm}}에 의해 관리되지 않습니다. 사용자의 브라우저에만 저장됩니다.
7. **MSP 정의 작성**을 클릭하십시오.

**태스크: 피어 조직 MSP 작성**

  |  | **표시 이름** | **MSP ID** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **조직 작성** | Org1 MSP | org1msp |||
  | **루트 CA** | Org1 CA ||||
  | **조직 관리자 인증서** | |  | org1admin | org1adminpw |
  | **ID** | Org1 Admin |||||

  *그림 5. 피어 조직 MSP 정의 작성*

MSP를 작성한 후에는 콘솔 지갑에서 피어 조직 관리자를 볼 수 있어야 합니다.

**태스크: 콘솔 지갑 확인**

  | **필드** | **표시 이름** | **설명** |
  | ------------------------- |-----------|----------|
  | **ID** | Org1 Admin | Org1 관리자 ID|

  *그림 6. 콘솔 지갑 확인*

MSP에 대한 자세한 정보는 [조직 관리](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations)를 참조하십시오.

사용자가 인증서 관리 및 보안의 책임을 지므로 조직 관리자 ID를 내보내는 것이 중요합니다.
{:important}

<!--
You are free to repeat this process as many times as times as you want. To create a network that is similar to [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about), for example, you will create two organizations and one peer per organization, and this tutorial will take you through those steps. This process involves repeating the steps above, to create the CA and the relevant identities, and below, to create another peer. Remember that the best practice is to have **one CA for each organization**. This CA can, however, be used to register and enroll multiple organization admins as well as multiple peer identities **for that organization**. Make sure to keep track of the resources you are using in your Kubernetes cluster, especially if using the free cluster.
{: note}
-->


### 피어 작성
{: #ibp-console-build-network-peer-create}

[CA를 작성](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-CA-org1CA)하고
이를 사용하여 ID를 등록하고
[피어 조직 MSP](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peers-org1)를
작성했으면 피어를 작성할 준비가 된 것입니다.

#### 피어의 역할
{: #ibp-console-build-network-peer-role}

조직 자체가 원장을 유지보수하지 않는다는 사실을 기억하는 것이 중요합니다. 피어가 수행합니다. 또한 조직은
피어를 사용하여 트랜잭션 제안에 서명하고 채널 구성 업데이트를 승인합니다. 채널에
두 개 이상의 피어가 있으면 고가용성이 구현되며 두 개 이상의 피어가 한 채널에
가입하는 것이 프로덕션 레벨의 구현에 대한 우수 사례로 간주됩니다. 이 튜토리얼에서는 단일 피어 작성 프로세스만 표시합니다.

리소스 할당 관점에서는 동일한 피어를 여러 채널에 가입시킬 수 있습니다. 피어 디자인은
한 채널의 데이터가 피어를 통해 다른 채널로 전달되지 않도록 합니다. 그러나 피어가
각 채널에 대해 별도의 원장을 저장하므로 피어가 트랜잭션 및 데이터 로드를 처리하기에 충분한 처리 능력과 스토리지를 보유해야 합니다.

#### 피어 배치
{: #ibp-console-build-network-deploy-peer-role}

콘솔을 사용하여 다음 단계를 수행하십시오.

1. **노드** 페이지에서 **피어 추가**를 클릭하십시오.
2. **새 피어 작성** 아래의 {{site.data.keyword.cloud_notm}}를 클릭하고 **다음**을 클릭하십시오.
3. 피어에 **표시 이름**인 `Peer Org1`를 제공하십시오.
4. 다음 화면에서 CA로 `Org1 CA`를 선택하십시오. 그런 다음, 피어에 대해
작성한 피어 ID의 등록 ID 및 시크릿으로 `peer1` 및 `peer1pw`를 제공하십시오. 그런 다음, 드롭 다운 목록에서
MSP로 `Org1 MSP`를 선택하고 **다음**을 클릭하십시오.
5. 다음 사이드 패널에서는 TLS CA 정보를 요청합니다. 사용자의 CA와 함께 배치된 TLS CA에 대해 별도의 관리자를 작성할 수는 있으나 필수는 아닙니다.
  - **TLS Enroll ID**로 `admin`, 시크릿으로 `adminpw`를 제공하십시오.
이는 CA를 작성할 때 제공한 등록 ID 및 등록 시크릿과 동일한 값입니다.
  - **TLS CSR 호스트 이름**은 고급 사용자가
피어 엔드포인트를 처리하기 위해 사용할 수 있는 사용자 정의 도메인 이름을 지정하기 위한 것입니다. 지금은 **TLS CSR 호스트 이름**을
공백으로 두십시오. 이 튜토리얼에서는 사용하지 않습니다.
6. 마지막 사이드 패널에서는 **ID 연관**을 요청하여 이를 피어의 관리자로 만듭니다. 피어 관리자 ID인 `Org1 Admin`을 선택하십시오.
7. 요약을 검토하고 **제출**을 클릭하십시오.

**태스크: 피어 배치**

  |  | **표시 이름** | **MSP ID** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **피어 작성** | Peer Org1 | org1msp |||
  | **CA** | Org1 CA ||||
  | **피어 ID** | |  | peer1 | peer1pw |
  | **관리자 인증서** | org1msp ||||
  | **TLS CA** | Org1 CA ||||
  | **TLS CA ID** | || admin | adminpw |
  | **ID 연관** | Org1 Admin |||||

  *그림 7. 피어 배치*

## 2단계: 트랜잭션의 순서를 지정하는 노드 작성
{: #ibp-console-build-network-create-orderer}

이더리움이나 비트코인 등의 기타 분산 블록체인과 달리 트랜잭션 순서를 지정하고 피어로 전송하는 중앙 권한이 없습니다. {{site.data.keyword.blockchainfull_notm}} Platform의
기반이 되는 블록체인인 Hyperledger Fabric은 다르게 작동합니다. 즉, **순서 지정자**라는 노드 기능을 사용합니다.

순서 지정자는 몇 가지 핵심 기능을 수행하므로 네트워크에서 핵심 컴포넌트입니다.

- 순서 지정자는 문자 그대로 원장에 작성되기 위해 피어에 전송되는 트랜잭션 블록의 **순서를 지정**합니다. 이 프로세스를
"순서 지정"이라고 하며 순서 지정자가 "순서 지정 서비스"라고도 불리는 이유입니다.
- 순서 지정자는 채널을 작성하도록 허용되는 피어 조직 목록인 **컨소시엄**이
상주하는 장소인 **순서 지정자 시스템 채널**을 유지보수합니다. 컨소시엄은
본질적으로 멀티 테넌시 수단이며 디자인에 의한 단일 순서 지정 서비스는 다중 컨소시엄을 호스팅할 수 있습니다.
- 순서 지정자는 컨소시엄 또는 채널 관리자에 의해 결정된 **정책을 적용**합니다. 이러한 정책은
채널에 대한 읽기 또는 쓰기 권한을 가지는 사용자부터 채널을 작성 또는 수정할 수 있는 사용자까지 모든 것을 결정합니다. 예를 들어,
네트워크 참가자가 채널 또는 컨소시엄 정책을 수정하도록 요청하는 경우, 순서 지정 서비스는 요청을 처리하여 참가자가 해당 구성 업데이트에
대한 적절한 관리 권한을 보유하고 있는지 확인하고 기존 구성에 대해 유효성 검증을 수행하고 새 구성을 생성하고 피어에 릴레이합니다.

피어와 마찬가지로 순서 지정자를 작성하기 전에 CA를 작성하여 순서 지정자 조직의 ID 및 MSP를 제공해야 합니다.

### 콘솔에서 순서 지정하기
{: #ibp-console-build-network-ordering-console}

다양한 순서 지정 서비스가 존재하지만 현재 유일하게 지원되는 순서 지정 서비스는 단일 순서 지정자 노드를 특징으로 하는 SOLO입니다. 단일 노드를
가진다는 것은 단일 실패 지점이 있다는 의미이므로 프로덕션 네트워크에서는 SOLO를 권장하지 않습니다.

콘솔을 사용하여 원하는 수만큼의 순서 지정자를 작성할 수 있습니다. 단, 이러한 각 순서 지정자는
구별되는 순서 지정자 시스템 채널이 있어야 하며 고유의 컨소시엄이 있어야 합니다. 하나의 SOLO 순서 지정자만이
채널에 연결될 수 있습니다.

이 튜토리얼에서는 하나의 순서 지정자만 작성합니다.

### 순서 지정자 조직 CA 작성
{: #ibp-console-build-network-create-orderer-ca}

순서 지정자에 대한 CA를 작성하는 프로세스는 피어의 경우와 동일합니다.
1. **노드** 탭으로 이동하여 **인증 기관 추가**를 클릭하십시오.
2. **인증 기관 작성** 아래의 **{{site.data.keyword.cloud_notm}}**를 클릭하고 **다음**을 클릭하십시오.
3. 이 CA에 고유한 표시 이름인 `Orderer CA`를 제공하십시오.
4. 기타 CA에 제공한 **등록 ID**인 `admin`을 재사용할 수 있으며 원하는 시크릿을
지정할 수 있지만 `adminpw`를 권장합니다.

### CA를 사용하여 순서 지정자 및 순서 지정자 관리자 ID 등록
{: #ibp-console-build-network-use-CA-orderer}

피어에 대해 작업한 것처럼 순서 지정자 CA에 두 개의 ID를 등록해야 합니다. 사용자의 CA를 선택한 후에 순서 지정자 자체에 대한 ID 외에 관리자를 순서 지정자 조직에 등록해야 합니다. 이전과
마찬가지로 `Orderer CA` 탭에 ID가 표시되어야 하며 해당 ID는 사용자가 CA에 대해 작성한 관리자입니다. 

1. 콘솔에서 **노드** 탭을 클릭하고 사용자가 작성한 `Orderer CA`를 클릭하십시오.
2. 방금 작성한 관리자 ID가 테이블에 표시되면  **사용자 등록** 단추를 클릭하여 새 사용자를 등록하십시오.
3. 조직 관리자의 경우, 등록 ID인 `ordereradmin`을 제공하십시오. `ordereradminpw` 시크릿을 권장합니다.
4. 다음 단계에서는 이 ID의 유형을 `client`로 설정하고
드롭 다운 목록에서 소속된 조직을 선택하십시오. 소식 필드는 고급 사용자용이며 이 튜토리얼에서는 사용하지 않습니다. 목록의 항목은 Fabric CA의 기본 소속입니다. Fabric CA가
소속을 사용하는 방법에 대한 자세한 정보는 [새 ID 등록 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity)에 대한
주제를 참조하십시오. 이제 목록에서 임의의 소속을 선택하고 **다음**을 클릭하십시오.
5. **최대 등록 수** 및 **속성 추가** 필드는 공백으로 둘 수 있습니다. 두 필드는
이 튜토리얼에서는 사용하지 않습니다. 단, 콘솔의 [ID 등록](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)에서
이 주제에 대해 자세히 볼 수 있습니다.
6. 조직 관리자가 등록된 후 순서 지정자의 ID에 대해 동일한 `Orderer CA`를 사용하고 등록 ID `orderer1`을 제공하여 이 동일한 프로세스를 반복하십시오. 원하는 시크릿을 입력할 수 있으나 튜토리얼을 수행하는 데 도움이 되는 `orderer1pw`를 권장합니다. 이는 노드 ID이므로 다음 단계에서 **유형**으로 `peer`를 선택하십시오. 그런 다음,
이전과 마찬가지로 **최대 등록 수** 및 **소속**을 무시하십시오.

**태스크: CA 작성 및 사용자 등록**

  | **필드** | **설명** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **CA 작성** | 순서 지정자 CA | admin | adminpw |
  | **사용자 등록** | 순서 지정자 관리자 | ordereradmin | ordereradminpw |
  |  | 순서 지정자 ID |  orderer1 | orderer1pw |

  *그림 8. CA 작성 및 사용자 등록*

### 순서 지정자 조직 MSP 정의 작성
{: #ibp-console-build-network-create-orderer-org-msp}

순서 지정자 조직 MSP 정의를 작성하고 조직에 대한 관리자 ID를 지정하십시오. 순서 지정자 관리자 및 순서 지정자 사용자를 등록한 다음
MSP ID를 작성하고 조직의 관리자로 등록한 `ordereradmin` 사용자를 등록해야 합니다.

1. 왼쪽 탐색의 **조직** 탭으로 이동하여 **MSP 정의 작성**을 클릭하십시오.
2. MSP 정의에 `Orderer MSP`와 같은 표시 이름을 제공하고 `orderermsp`와 같은 ID를 제공하고
도구 팁에서 이 이름에 대한 제한사항 스펙을 준수하십시오.
3. **루트 인증 기관 세부사항** 아래에서 작성한 `Orderer CA`를 선택하십시오.
4. 이 아래의 **등록 ID** 및 **등록 시크릿**이
사용자가 CA를 사용하여 작성한 첫 번째 사용자의 등록 ID 및 시크릿으로 자동으로 채워집니다. 이러한 값을 사용하지 마십시오. 대신,
조직 관리자에 대한 등록 ID 및 시크릿, 즉, `ordereradmin`과
원하는 시크릿을 사용하십시오. 단, 튜토리얼을 수행하는 데 도움이 되는 `ordereradminpw`를 권장합니다. 그런 다음, 이 ID에 대해 `Orderer Admin`과 같은 표시 이름을 제공하십시오.
5. **생성** 단추를
클릭하여 이 ID를 조직의 관리자로 등록하고 ID를 콘솔 지갑에 추가하십시오.
순서 지정자를 작성할 때 사용됩니다.
6. **내보내기**를 클릭하여 순서 지정자 조직 관리자 ID를 파일 시스템으로 내보내십시오. 위에서 설명한 대로
이 ID는 클러스터에 저장되거나 {{site.data.keyword.IBM_notm}}에 의해 관리되지 않습니다. 사용자의 브라우저의 로컬 스토리지에만 저장됩니다.
7. **MSP 정의 작성**을 클릭하십시오.

**태스크: 순서 지정자 조직 MSP 정의 작성**

  |  | **표시 이름** | **MSP ID** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **조직 작성** | Orderer MSP | orderermsp |||
  | **루트 CA** | Orderer CA ||||
  | **조직 관리자 인증서** | |  | ordereradmin | ordereradminpw |
  | **ID** | Orderer Admin |||||

  *그림 9. 순서 지정자 조직 MSP 정의 작성*

MSP를 작성한 후에는 콘솔 지갑에서 순서 지정자 조직 관리자를 볼 수 있어야 합니다.

**태스크: 콘솔 지갑 확인**

  | **필드** | **표시 이름** | **설명** |
  | ------------------------- |-----------|----------|
  | **ID** | Org1 Admin | Org1 관리자 ID|
  | **ID** | Orderer Admin | 순서 지정자 관리자 ID |

  *그림 10. 순서 지정자 조직 MSP 정의 작성*

사용자가 인증서 관리 및 보안의 책임을 지므로 조직 관리자 ID를 내보내는 것이 중요합니다. 순서 지정자 노드 및 순서 지정자 MSP 정의를 내보내는 경우,
다른 운영자가 순서 지정자에 대한 새 채널을 작성하거나 피어를 채널에 가입시킬 수 있는 다른 콘솔로 가져올 수 있습니다.
{:tip}

### 순서 지정자 작성
{: #ibp-console-build-network-create-orderer}

콘솔에서 다음 단계 수행:

1. **노드** 페이지에서 **순서 지정자 추가**를 클릭하십시오.
2. **새 순서 지정자 작성** 아래의 {{site.data.keyword.cloud_notm}} 단추를 클릭하고 **다음**을 클릭하십시오.
3. 사용자의 순서 지정자에 `Orderer`라는 **표시 이름**을 제공하십시오.
4. 다음 단계에서 CA로 `Orderer CA`를 선택하십시오. 그런 다음, 순서 지정자에 대해
작성한 순서 지정자 ID의 등록 ID 및 시크릿으로 `orderer1` 및 `orderer1pw`를 제공하십시오. 그런 다음, 드롭 다운 목록에서
MSP로 `Orderer MSP`를 선택하고 **다음**을 클릭하십시오.
5. 다음 사이드 패널에서는 TLS CA 정보를 요청합니다. 사용자의 CA와 함께 배치된 TLS CA에 대해 별도의 사용자를 작성할 수는 있으나 필수는 아닙니다.
   - **TLS Enroll ID**로 `admin`, 시크릿으로 `adminpw`를 제공하십시오.
이러한 값은 CA를 작성할 때 제공한 등록 ID 및 등록 시크릿입니다. 
   - **TLS CSR 호스트 이름**은 고급 사용자가
순서 지정자 엔드포인트를 처리하기 위해 사용할 수 있는 사용자 정의 도메인 이름을 지정하기 위한 것입니다. 지금은 **TLS CSR 호스트 이름**을
공백으로 두십시오. 이 튜토리얼에서는 사용하지 않습니다.
6. **ID 연관** 단계를 통해 순서 지정자에 대한 관리자를 선택할 수 있습니다. 앞에서와 같이 `Orderer Admin`을 선택하고 **다음**을 클릭하십시오.
7. 요약을 검토하고 **제출**을 클릭하십시오.

**태스크: 순서 지정자 작성**

  |  | **표시 이름** | **MSP ID** | **등록 ID** | **시크릿** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **순서 지정자 작성** | Orderer | orderermsp |||
  | **CA** | Orderer CA ||||
  | **순서 지정자 ID** | |  | ordereradmin | ordereradminpw |
  | **관리자 인증서** | Orderer MSP ||||
  | **TLS CA** | Orderer CA ||||
  | **TLS CA ID** | || admin | adminpw |
  | **ID 연관** | Orderer Admin |||||

  *그림 11. 순서 지정자 작성*

순서 지정자가 작성된 후에는 **노드** 패널에서 이를 볼 수 있습니다.

## 3단계: 거래할 수 있는 조직 목록에 조직 추가
{: #ibp-console-build-network-add-org}

앞에서 설명한 바와 같이 피어 조직이 채널에 가입하려면 그 전에 순서 지정자의 컨소시엄의 구성원이어야 합니다. 그 이유는
채널이 기술 레벨에서 순서 지정자를 통한 피어 간의 **메시징 경로**이기
때문입니다. 피어가 한 채널에서 다른 채널로 전달되는 정보 없이 여러 채널에 가입할 수 있으므로
순서 지정자가 데이터를 다른 채널의 조직에 노출하지 않고 채널을 통해 실행되는 여러 채널을 가질 수 있습니다.

순서 지정자 관리자만이 피어 조직을 컨소시엄에 추가할 수 있으므로
사용자가 순서 지정자 관리자 **이거나** MSP 정보를 순서 지정자 관리자에게 **전송**해야 합니다.
<!-- More on the latter at the LINK. -->
사용자가 순서 지정자 관리자이므로 이 프로세스는 상대적으로 간단합니다.
1. **노드** 탭으로 이동하십시오.
2. 작성한 순서 지정자까지 아래로 스크롤한 다음 이를 클릭하여 여십시오.
3. **컨소시엄 구성원** 아래에서 **조직 추가**를 클릭하십시오.
4. 드롭 다운 목록에서 피어의 `org1`을 나타내는 MSP인 `Org1 MSP`를 선택하십시오.
5. **제출**을 클릭하십시오.

이 프로세스가 완료되면 `org1`이 `Orderer`에서 호스팅되는 채널을 작성하거나 가입할 수 있습니다.

이 튜토리얼에서는 피어 조직 및 순서 지정자 조직 둘 다 동일한 콘솔에서 작성되므로 쉽게 `Org1 MSP`에
액세스할 수 있습니다. 피어 조직이 다른 콘솔 또는 클러스터에서
작성되어 컨소시엄에 가입하려는 경우, 사용자에게 대역 내외 오퍼레이션의 MSP 정의를 전송해야
합니다. 또한 채널에 가입하거나 작성하려면 순서 지정자 노드를 콘솔로 내보내야 합니다. 이 프로세스는
[조직 정보 내보내기](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote)
아래의 네트워크 가입 튜토리얼에서 설명합니다.

## 4단계: 채널 작성
{: #ibp-console-build-network-create-channel}

네트워크의 구성원은 일반적으로 서로 거래하고자 하는 연관된 네트워크 엔티티이나
구성원의 서브세트가 서로를 모른 상태로 거래하고자 하는 경우에는 인스턴스일 수 있습니다. 해당 트랜잭션이
발생하는 **채널**을 작성하여 이러한 방법이 가능합니다. 채널은
구성원, 피어, 순서 지정 서비스, 원장, 정책 및 스마트 계약을 포함하는 블록체인 네트워크의
구조를 복제합니다. 그러나 채널의 존재를 아는 경우에도 멤버십을 네트워크 멤버십의 특정 서브세트로 제한함으로써
필요한 경우, 네트워크 구성원이 정책을 유지보수하면서 네트워크의 전체 구조를 활용할 수 있습니다.

위에서 언급한 바와 같이, `org1`의 피어를 채널에 가입시키려면
먼저 `org1`이 컨소시엄에 추가되어야 합니다. 조직이
채널 작성 시에 컨소시엄의 구성원이 아닌 경우, 채널을 작성하고 관련 채널의
페이지에서 **설정** 단추를 클릭하고 **채널 업데이트** 과정을
수행하여 나중에 조직을 추가할 수 있습니다.

채널 및 채널 사용 방법에 대한 자세한 정보는 [Hyperledger Fabric 문서![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channels.html)를 참조하십시오.

<!--
Note that even though the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 uses Hyperledger Fabric v1.4 binaries, because the [gossip protocol ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html) is not being used with the console, Fabric functionalities that leverage gossip, such as [Private Data ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html)] and [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html)], are not available.
-->

### 채널 작성: `channel1`
{: #ibp-console-build-network-create-channel1}

콘솔이 피어를 사용하여 피어가 속한 채널에 대한 정보를 수집하므로 **조직이 피어를 채널에 가입시키지 않은 한 콘솔을 사용하여 채널을 보거나 채널과 상호작용할 수 없습니다**.

콘솔을 사용하여 작성한 노드 범위에서만 운영할 것이므로 채널을 작성하기 전에 사용자의 CA, 순서 지정자 노드 및 피어를 작성할 때까지
대기해야 합니다. 또한 피어 조직을 컨소시엄에 추가해야 합니다.

준비가 되면 왼쪽 탐색에서 **채널** 탭으로 이동하십시오. 여기는 채널 작성 및 관리가 수행되는 곳입니다.

이 탭으로 처음 이동하는 경우, **채널 작성** 및 **채널 가입** 단추 외에는
비어 있습니다. 그 이유는 아직 채널을 작성하지 않았으며 피어를 가입시키지 않았기 때문입니다.

#### 채널 작성
{: #ibp-console-build-network-channels-create}

콘솔에서 다음 단계 수행:

1. **채널 작성**을 클릭하십시오. 사이드 패널이 열립니다.
2. 채널에 `channel1`이라는 **이름**을 제공하십시오. 이 값은 콘솔에
저장되지 않으므로 기록해 두십시오. 이 채널에 가입시키고자 하는 임의의 사용자와 이 값을 공유해야 합니다.
3. 순서 지정자 드롭 다운 목록에서 사용자가 작성한 **순서 지정자**인 `Orderer`를 선택하십시오.
4. 드롭 다운 목록에서 채널 작성자의 조직을 식별하는 **MSP**를 선택하십시오. 이는 `Org1 MSP(org1msp)`여야 합니다.
5. 채널 작성자의 ID를 지정하십시오. 앞의 MSP 단계에서 채널을 작성한 조직을 참고하는 반면
이 단계에서는 **사용자의** 관리자 ID인
`Org1 Admin`을 참고합니다.
6. 채널에 가입시킬 조직 및 부여할 권한을 선택하십시오. 채널 작성자로 `Org1 MSP(org1msp)`를
입력했으나 여기에서도 선택해야 합니다. **추가**를 클릭한 다음 조직에 권한 레벨을 부여하십시오. 실제
시나리오에서는 채널 작성자로서 조직 가입 채널의 필요와 부합하는 권한을 신중하게 선택해야 합니다. 단일 구성원이 있는
채널을 작성하는 중이며 모든 채널이 한 명 이상의 운영자를
가지므로 조직을 **Operator**로 설정하십시오.

준비가 완료되면 **작성**을 클릭하십시오. 채널 탭으로 다시 이동해야 합니다.

**태스크: 채널 작성**

  |  **필드** | **이름** |
  | ------------------------- |-----------|
  | **채널 이름** | channel1 |
  | **순서 지정자** | Orderer |
  | **채널 작성자 MSP** | Org1 MSP |
  | **연관 가능 ID** | Org1 Admin|
  | **채널 구성원** | Org1 MSP|

*그림 12. 채널 작성*

채널을 작성하고 피어를 채널에 가입시키는 것은 두 단추 뒤의 흐름에 의해 처리되는
별도의 두 단계이므로 피어를 채널에 가입시키기 전에는 이 화면에서 작성된 채널을 볼 수 없습니다.
{:note}

다음 단계는 피어를 이 채널에 가입시키는 것입니다.

## 5단계: 채널에 피어 가입
{: #ibp-console-build-network-join-peer}

이제 거의 완료되었습니다. 피어를 채널에 가입시키는 것은 네트워크에 대한 기본 인프라를 설정하는 과정의 마지막 단계입니다. 아직 해당 위치가 아닌
경우, 왼쪽 탐색에서 **채널** 탭으로 이동하십시오. 

콘솔에서 다음 단계 수행:

1. **채널 가입**을 클릭하여 사이드 패널을 실행하십시오.
2. `Orderer`를 선택하고 **다음**을 클릭하십시오.
3. 방금 작성한 채널 이름인 `channel1`을 입력하고 **다음**을 클릭하십시오.
4. 채널에 가입시킬 피어를 선택하십시오. 이 튜토리얼의 목적에 따라 `Peer Org1`을 클릭하십시오.
5. **제출**을 클릭하십시오.

## 다음 단계
{: #ibp-console-build-network-next-steps}

피어를 작성하고 채널에 가입시킨 후에는 개발 및 테스트 용도로 사용할
완전한 기능의 기본 블록체인 네트워크를 가질 수 있습니다. 다음 단계에 따라
스마트 계약을 배치하고 블록체인에 트랜잭션 제출을 시작하십시오.

- 콘솔을 시작하여 [네트워크에 스마트 계약을 배치](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)하십시오.
- 스마트 계약을 설치하고 인스턴스화한 후에
[클라이언트 애플리케이션을 사용하여 트랜잭션을 제출](docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK)하십시오.
- [상업 어음 샘플](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)을
사용하여 예제 스마트 계약을 배치하고 샘플 애플리케이션 코드를 사용하여 트랜잭션을 제출하십시오.

또한 [네트워크 가입 튜토리얼](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-structure)을
사용하는 방법으로 다른 피어 조직을 작성할 수 있습니다. 채널에 두 번째
조직을 추가하여 단일 채널 원장을 공유하는 두 개의 피어가 있는 분산 네트워크를 시뮬레이션할 수 있습니다.

## 문제점 해결
{: #ibp-console-build-network-troubleshooting}

**문제점:** 노드를 작동하려고 할 때 오류가 발생합니다.  

**솔루션:** 콘솔에서 노드를 작동하는 중에 오류가 수신되면 오류에 관한
[노드 로그를 확인](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs)하십시오.  


**문제점:** Kubernetes 대시보드가 피어 시작에 실패했음을 표시하고
피어 로그에 `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`가 포함되어 있습니다.

**솔루션:** 피어의 조직 MSP 정의를 작성할 때 사용자가 지정한
등록 ID 및 시크릿이 `peer`가 아닌 `client` 유형의 ID에 해당되는지 확인하십시오.

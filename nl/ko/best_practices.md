---

copyright:
  years: 2019
lastupdated: "2019-03-22"

keywords: best practices, develop applications, connectivity, availability, mutual TLS, CouchDB

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# 애플리케이션 개발을 위한 우수 사례
{: #best-practices-app}

이 안내서는 애플리케이션 개발의 기본사항을 이미 학습했고 솔루션을 스케일링할 준비가 된 사용자를 대상으로 합니다. 다음 우수 사례에 따라 네트워크의 성능을 최대화하고 애플리케이션 작동 중단을 방지하십시오.
{:shortdesc}

## 애플리케이션 연결성 및 가용성
{: #best-practices-app-connectivity-availability}

Hyperledger Fabric [트랜잭션 플로우 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "트랜잭션 플로우"){:new_window}는 고유한 역할을 수행하는 클라이언트 애플리케이션과 함께 여러 컴포넌트를 포함합니다. SDK에서는 인증을 위해 피어에 트랜잭션 제안을 제출합니다. 그런 다음 순서 지정 서비스에 보낼 인증된 제안을 수집한 후 채널 원장에 추가할 피어에 트랜잭션 블록을 보냅니다. 프로덕션 애플리케이션 개발자는 효율성과 가용성을 위해 SDK와 네트워크 사이의 상호작용을 관리하도록 준비해야 합니다.

### 트랜잭션 관리
{: #best-practices-app-managing-transactions}

애플리케이션 클라이언트에서는 해당 트랜잭션 제안의 유효성이 검증되고 제안이 성공적으로 완료되었는지 확인해야 합니다. 네트워크 가동 중단 또는 컴포넌트 실패와 같은 여러 이유 때문에 제안이 지연되거나 유실될 수 있습니다. 컴포넌트 실패를 처리하려면 [고가용성](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app)을 위해 애플리케이션을 코딩해야 합니다. 또한 네트워크에서 응답하기 전에 제안의 제한시간이 초과되지 않도록 애플리케이션에서 [제한시간 값을 증가](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk)시킬 수 있습니다.

체인코드가 실행 중이지 않으면 이 체인코드에 보낸 첫 번째 트랜잭션 제안에서 체인코드를 시작합니다. 체인코드를 시작하는 동안 기타 모든 제안은 체인코드가 현재 시작 중임을 나타내는 오류로 인해 거부됩니다. 이는 트랜잭션 무효화와 다릅니다. 체인코드를 시작하는 동안 제안이 거부되면 애플리케이션 클라이언트에서 체인코드가 시작된 후 거부된 제안을 다시 보내야 합니다. 애플리케이션 클라이언트에서는 트랜잭션 제안을 유실하지 않도록 메시지 큐를 사용합니다.

채널 기반 이벤트 서비스를 사용하여 트랜잭션을 모니터링하고 메시지 큐를 빌드할 수 있습니다. [channelEventHub ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} 클래스에서 트랜잭션, 블록 및 체인코드 이벤트를 기반으로 리스너를 등록할 수 있습니다. 채널 eventhub의 채널 기반 리스너는 여러 채널로 스케일링하여 서로 다른 채널의 트래픽을 구분할 수 있습니다.

이전 EventHub 클래스 대신 channelEventHub를 사용하는 것이 좋습니다. EventHub는 단일 스레드이며 모든 채널의 이벤트가 포함되어 느려지거나 채널 사이에서 리스너가 정지될 수 있습니다. 또한 eventHub 클래스는 이벤트가 전달되도록 보장하지 않으며 특정 위치(예: 블록 번호)에서 유실된 이벤트를 추적하기 위해 이벤트를 검색하는 방법도 제공하지 않습니다.

**참고:** 피어 EventHub 클래스는 Fabric SDK의 향후 릴리스에서 더 이상 사용되지 않습니다. 피어 EventHub 클래스를 사용하는 기존 애플리케이션이 있는 경우에는 대신 채널 EventHub 클래스를 사용하도록 애플리케이션을 업데이트하십시오. 자세한 정보는 노드 SDK 문서의 [채널 기반 이벤트 서비스를 사용하는 방법 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "채널 기반 이벤트 서비스를 사용하는 방법"){:new_window}을 참조하십시오.

### 네트워크 연결 열기 및 닫기
{: #best-practices-app-connections}

트랜잭션 제안을 제출하기 전에 SDK를 사용하여 피어 및 순서 지정자를 작성하는 경우 애플리케이션과 네트워크 컴포넌트 간의 gRPC 연결을 열게 됩니다. 예를 들어 다음 명령은 `org1-peer1`에 대한 연결을 엽니다. 이 연결은 애플리케이션이 실행되는 동안 계속 활성 상태로 유지됩니다.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

애플리케이션과 네트워크 간의 연결을 관리하는 경우 다음과 같은 권장사항을 고려할 수 있습니다.

- 네트워크와 상호작용할 때 트랜잭션을 제출하기 위해 새 연결을 열지 않고 피어 및 순서 지정자 오브젝트를 재사용합니다. 피어 및 순서 지정자를 재사용하는 경우 리소스를 절약하여 더 좋은 성능으로 이어질 수 있습니다.  
- 네트워크 컴포넌트에 대한 지속적 연결을 유지하려면 [gRPC keepalives ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "gRPC Keepalives")를 사용하십시오. Keepalives는 gRPC 연결을 활성 상태로 유지하고 "사용되지 않은" 연결이 닫히지 않도록 해줍니다. 피어 연결에 대한 다음 예제에서는 [연결 옵션 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "연결") 오브젝트에 gRPC 옵션을 추가합니다. gRPC 옵션은 {{site.data.keyword.blockchainfull_notm}} Platform에서 권장하는 값으로 설정되어 있습니다.  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  또한 네트워크 연결 파일의 `"peers"` 섹션에서 권장 설정을 사용하는 이러한 변수를 찾을 수 있습니다. [SDK가 포함된 연결 프로파일](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile)을 사용하여 네트워크 엔드포인트에 연결하는 경우 자동으로 권장 옵션을 애플리케이션으로 가져옵니다.

- 더 이상 연결이 필요 없는 경우 리소스를 사용 가능한 상태로 변경하고 성능 저하를 방지하기 위해 `peer.close()` 및 `orderer.close()` 명령을 사용하십시오. 자세한 정보는 노드 SDK 문서의 [peer close ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "peer close") 및 [orderer close![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "orderer close") 클래스를 참조하십시오. 연결 프로파일을 사용하여 채널 오브젝트에 피어 및 순서 지정자를 추가한 경우 `channel.close()` 명령을 사용하여 해당 채널에 지정된 모든 연결을 닫을 수 있습니다.

### 고가용성 애플리케이션
{: #best-practices-app-ha-app}

고가용성 우수 사례로서 장애 복구를 위해 조직당 최소 두 개의 피어를 배치하도록 적극 권장합니다. 또한 고가용성을 위해 애플리케이션을 조정해야 합니다. 두 피어 모두에 체인코드를 설치하고 채널에 추가하십시오. 그런 다음 네트워크를 설정하고 피어 대상 목록을 빌드할 때 두 피어 엔드포인트 모두에 트랜잭션 제안을 제출하도록 준비하십시오. 엔터프라이즈 플랜 네트워크에는 장애 복구를 위해 복수의 순서 지정자가 있으며, 이를 통해 하나의 순서 지정자를 사용할 수 없는 경우 클라이언트 애플리케이션에서 다른 순서 지정자로 인증된 트랜잭션을 전송할 수 있습니다. 수동으로 네트워크 엔드포인트를 추가하지 않고 [연결 프로파일](/docs/services/blockchain/v10_application.html#dev-app-connection-profile)을 사용하는 경우 프로파일이 최신 상태이며 추가 피어 및 순서 지정자가 프로파일의 `channels` 섹션에 있는 관련 채널에 추가되어 있는지 확인하십시오. 그런 다음 SDK에서 연결 프로파일을 사용하여 채널에 가입된 컴포넌트를 추가합니다.

## 상호 TLS 사용
{: #best-practices-app-mutual-tls}

Fabric V1.1 레벨에 있는 엔터프라이즈 플랜 네트워크를 실행하는 경우 애플리케이션에 [상호 TLS를 사용](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)하는 옵션이 있습니다. 상호 TLS를 사용으로 설정하는 경우 이 기능을 지원하도록 애플리케이션을 업데이트해야 합니다. 그렇지 않으면 애플리케이션에서 네트워크와 통신할 수 없습니다.

연결 프로파일에서 `certificateAuthorities` 섹션을 찾으십시오. 이 섹션에는 상호 TLS를 사용하여 사용자 네트워크와 통신하기 위해 인증서를 등록접수하고 가져오는 데 필요한 다음 속성이 있습니다.

- `url`: 상호 TLS 인증서를 제공할 수 있는 CA에 연결하기 위한 URL
- `enrollId`: 인증서를 가져오는 데 사용할 등록접수 ID
- `enrollSecret`: 인증서를 가져오는 데 사용할 등록접수 시크릿
- `x-tlsCAName`: 애플리케이션이 상호 TLS를 사용하여 통신할 수 있도록 하는 인증서를 가져오는 데 사용할 CA 이름

상호 TLS를 지원하도록 애플리케이션을 업데이트하는 데 관한 자세한 정보는 [상호 TLS 구성 방법 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "상호 tls"){:new_window}을 참조하십시오.


## (선택사항) Fabric SDK에서 제한시간 값 설정
{: #best-practices-app-set-timeout-in-sdk}

Fabric SDK는 블록체인 네트워크의 이벤트에 대한 클라이언트 애플리케이션의 기본 제한시간 값을 설정합니다. Fabric Java SDK의 기본 제한시간 설정에 대한 다음 예제를 참조하십시오. 파일 경로는 `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`입니다.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

그러나 사용자 고유의 애플리케이션에서 기본 제한시간 값을 변경해야 할 수 있습니다. 예를 들어, 애플리케이션이 이벤트 허브 연결의 기본 제한시간 값인 5000밀리초보다 응답하는 데 더 오랜 시간이 필요한 트랜잭션을 호출하는 경우 트랜잭션이 완료되기 전에 호출 이벤트가 5000밀리초에 종료되므로 실패 오류가 발생합니다. 클라이언트 애플리케이션의 기본값을 겹쳐쓰도록 시스템 특성을 설정할 수 있습니다. 시스템 특성을 설정하기 전에 기본값이 초기화되므로 시스템 특성이 적용되지 않을 수 있습니다. 따라서 클라이언트 애플리케이션에서 제한시간에 대한 시스템 특성을 정적 구조체에 설정해야 합니다. Fabric Java SDK에서 이벤트 허브 연결의 제한시간 값을 15000밀리초로 변경하는 데 대한 다음 예제를 참조하십시오. 파일 경로는 `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`입니다.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Node SDK를 사용하는 경우 호출된 메소드에 제한시간 값을 직접 지정할 수 있습니다. 아래 행을 예로 사용하여 [체인코드 인스턴스화 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal")의 제한시간 값을 5분으로 늘립니다.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## CouchDB 사용 시 우수 사례
{: #best-practices-app-couchdb-indices}

CouchDB를 상태 데이터베이스로 사용하는 경우, 채널의 상태 데이터에 대해 체인코드에서 JSON 데이터 조회를 시작할 수 있습니다. JSON 조회를 위한 색인을 작성한 후 체인코드에서 해당 색인을 사용할 것을 적극 권장합니다. 색인을 사용하는 경우 네트워크에서 트랜잭션 및 항목의 추가 블록을 세계 상태로 추가할 때 애플리케이션에서 효율적으로 데이터를 검색할 수 있습니다.

CouchDB에 관한 자세한 정보와 인덱스 작성 방법은 Hyperledger Fabric 문서에서 [상태 데이터베이스로서의 CouchDB![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html "상태 데이터베이스로서의 CouchDB"){:new_window}를 참조하십시오. [Fabric CouchDB 튜토리얼 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_tutorial.html)에서 체인코드의 색인을 사용하는 예제도 찾을 수 있습니다.

전체 CouchDB 데이터베이스 스캔을 유발하는 조회를 위해 체인코드를 사용하지 마십시오. 전체 데이터베이스 스캔을 실행하면 긴 응답 시간이 필요하게 되어 네트워크의 성능이 저하됩니다. 다음 단계 중 일부를 수행하여 대형 조회를 방지할 수 있습니다.
- 체인코드의 색인을 설정하십시오.
- 색인이 사용되려면 색인의 모든 필드가 조회의 선택기 또는 정렬 섹션에도 있어야 합니다.
- 조회가 복잡할수록 성능이 더 저하되고 색인을 사용할 가능성이 줄어듭니다.
- 전체 테이블 스캔 또는 전체 색인 스캔을 유발하는 연산자(예: `$or`, `$in` 및 `$regex`)를 사용하지 않도록 해야 합니다.

[Fabric CouchDB 튜토리얼] ![외부 링크 아이콘](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes)에서 조회가 색인을 사용하는 방법과 최상의 성능을 제공하는 조회의 유형을 보여주는 예제를 찾을 수 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform의 피어에는 queryLimit가 설정되어
있으며 상태 데이터베이스에서 10,000개의 항목만 리턴합니다. 조회가 queryLimit에 도달하면
다중 조회를 사용하여 나머지 결과를 얻을 수 있습니다. 범위 조회에서 더 많은 결과가 필요한 경우 이전 조회에서 마지막 키가 리턴된 상태로 후속 조회를
시작하십시오. JSON 조회에서 더 많은 결과가 필요한 경우, 데이터의 변수 중 하나를 사용하여 조회를 정렬한 후에 다음 조회에 대한 '초과' 필터에 이전 조회의 마지막 값을 사용하십시오.

집계 또는 보고 용도로 전체 데이터베이스를 조회하지 마십시오. 대시보드를 빌드하거나 애플리케이션의 일부로 많은 양의
데이터를 수집하려는 경우 블록체인 네트워크의 데이터를 복제하는 오프 체인 데이터베이스를 조회할 수 있습니다. 이를 통해 네트워크의 성능이 저하되거나 트랜잭션을 방해하지 않고 블록체인의 데이터를 파악할 수 있습니다.

Fabric SDK에서 제공하는 채널 기반의 이벤트 서비스 클라이언트를 사용하여 오프 체인 데이터 저장소를 빌드할 수 있습니다. 예를 들어 블록 리스너를 사용하여 최근에 채널 원장에 추가된 트랜잭션을 가져올 수 있습니다. 그런 다음 올바른 트랜잭션에서 트랜잭션 읽기 및 쓰기 세트를 사용하여 별도의 데이터베이스에 저장되어 있는 세계 상태의 사본을
업데이트할 수 있습니다. 자세한 정보는 노드 SDK 문서의 [채널 기반 이벤트 서비스를 사용하는 방법 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "채널 기반 이벤트 서비스를 사용하는 방법"){:new_window}을 참조하십시오.

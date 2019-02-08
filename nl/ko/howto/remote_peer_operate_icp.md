---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private에서 원격 피어 운영
{: #remote-peer-operate}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


ICP({{site.data.keyword.cloud_notm}} Private)에 {{site.data.keyword.blockchainfull}} Platform 원격 피어를 설정한 다음 여러 오퍼레이션 단계를 완료해야 피어가 Blockchain 네트워크의 원장을 조회하고 호출하기 위한 트랜잭션을 발행할 수 있습니다. 이 단계에는 채널에 조직을 추가하고 원격 피어에 채널이 가입하며 원격 피어에 체인코드를 설치하고 채널에서 체인코드를 인스턴스화하며 애플리케이션을 원격 피어에 연결하는 단계가 포함됩니다.{:shortdesc}

원격 피어를 작동시키려면 ICP 클러스터에서 몇 가지 전제조건 단계를 완료해야 합니다.

**전제조건:**
- [원격 피어 엔드포인트 정보 검색](#remote-peer-retrieve-endpoint-info)
- [kubectl을 사용하여 피어 컨테이너에 로그인](#remote-peer-kubectl-configure)
- [원격 피어 다시 시작](#remote-peer-restart)

다음 방법 중 하나를 사용하여 원격 피어를 작동시킬 수 있습니다.

**오퍼레이션 경로:**
- [Fabric SDK 사용](#remote-peer-operate-with-sdk)
- [명령행 사용](#remote-peer-cli-operate)

지시사항에서는 SDK 오퍼레이션에 익숙하다고 가정하지만 Fabric SDK 경로를 사용하는 것이 좋습니다.

[고가용성](remote_peer_icp.html#high-availability)을 위해 원격 피어 helm 차트 인스턴스를 2개 이상 배치하는 것이 좋습니다. 따라서 피어마다 해당 오퍼레이션 단계를 한 번씩 수행해야 합니다. 애플리케이션에서 체인코드를 호출하고 조회할 준비가 되면 [애플리케이션의 고가용성](/docs/services/blockchain/v10_application.html#ha-app)을 위해 두 피어에 모두 연결하십시오.

**참고**: {{site.data.keyword.blockchainfull_notm}} Platform 원격 피어에서는 {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어의 전체 기능 또는 지원에 액세스할 권한이 없습니다. 따라서 네트워크 모니터를 사용하여 원격 피어를 작동시킬 수 없습니다. 원격 피어 실행을 시작하기 전에 [고려사항](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations)을 검토하십시오.

## 전제조건

SDK를 사용할지 아니면 명령행을 사용할 계획인지에 상관없이 피어 오퍼레이션 과정에서 이 섹션의 단계를 완료해야 합니다. 시작하기 전에 처음 두 단계를 완료하여 피어 엔드포인트를 검색하고 kubectl을 구성할 수 있습니다.

### 원격 피어 엔드포인트 정보 검색
{: #remote-peer-retrieve-endpoint-info}

ICP 콘솔에 로그인하십시오. 왼쪽 상단에 있는 **메뉴** 아이콘을 클릭하십시오. **워크로드** > **배치**를 클릭하십시오. 그런 다음 원격 피어 인스턴스의 이름을 찾아 클릭하여 배치 개요 화면을 여십시오. 아래로 스크롤하여 **세부사항 표시** 섹션으로 이동한 후 원격 피어의 `Ingress IP`와 `Ports`를 찾으십시오.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

원격 피어에 연결하기 위해 Ingress IP 주소와 Node 포트를 기록해 두십시오. 원격 피어에 체인코드를 설치하고 인스턴스화할 때 이 정보를 사용해야 합니다. 다음 예에서 피어 주소는 `9.46.126.89:30162`, `9.46.126.89:30260` 또는 `9.46.126.89:32051`입니다.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### kubectl CLI 구성
{: #remote-peer-kubectl-configure}

ICP에서 실행되는 피어 컨테이너에서 몇 가지 필수 오퍼레이션을 수행하려면 **kubectl** 명령행 도구를 사용해야 합니다. 다음과 같이 {{site.data.keyword.cloud_notm}} Private CLI 도구를 사용하여 클러스터에 로그인하십시오.

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

그런 다음 ICP 클러스터의 대시보드에 로그인하십시오. ICP UI의 오른쪽 상단에서 사용자 아바타를 클릭한 다음 **클라이언트 구성**을 클릭하십시오. 로컬 시스템의 터미널 창에서 명령을 복사하여 붙여넣고 실행하십시오. 다음 명령을 실행하여 구성이 제대로 작동했는지 확인할 수 있습니다.

```
kubectl -n <namespace> get pods
```
{:codeblock}

여기서 `-n <namespace>`은 `default` 네임스페이스를 사용하지 않을 때 지정해야 합니다. 
ICP 클러스터에 있는 네임스페이스 값으로 `<namespace>`를
바꾸십시오.


이 명령을 실행하면 다음 예제와 비슷한 응답이 생성되어야 합니다. `remotepeer-bd65c4769-95rmm`은 피어 컨테이너의 이름입니다.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

응답에서 원격 피어 팟(Pod) 이름을 인식하지 못하는 경우 배치 패널의 **팟** 분할창에서 팟의 이름을 찾을 수 있습니다.

다음 명령을 실행하고 `<PEER_CONTAINER_NAME>`의 값을 위의 팟 이름으로 바꾸어 원격 피어 컨테이너를 실행하십시오.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
여기서 `-n <namespace>`은 `default` 네임스페이스를 사용하지 않을 때 지정해야 합니다. 
ICP 클러스터에 있는 네임스페이스 값으로 `<namespace>`를
바꾸십시오.

예를 들어, 다음과 같습니다.

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

이제 피어 컨테이너 CLI를 사용할 준비가 되었습니다. SDK 또는 명령행을 사용하는 경우 CLI를 사용하여 피어 TLS 인증서를 다운로드하고 서명 인증서를 피어에 업로드해야 합니다.

## ICP에서 실행되는 원격 피어 다시 시작
{: #remote-peer-restart}

인증서를 원격 피어에 추가할 때마다 새 인증서를 인식하도록 피어 노드를 다시 시작해야 합니다.

**kubectl** 명령을 사용하여 ICP에서 실행되는 원격 피어를 다시 시작할 수 있습니다. 이 명령에서는 원격 피어 **팟(pod)**의 이름과 해당 **컨테이너**를 명령 매개변수로 사용해야 합니다. kubectl 명령 사용에 관한 자세한 정보는 [kubectl CLI 구성](#remote-peer-kubectl-configure)을 참조하십시오.

1. ICP 콘솔에서 원격 피어 배치의 **팟(pod)** 이름과 **컨테이너** 이름을 찾으십시오.

  1. 콘솔의 왼쪽 상단에 있는 메뉴 아이콘을 클릭한 다음 **워크로드 > 배치**를 클릭하십시오.
  2. 테이블의 원격 피어 릴리스를 찾아서 클릭하여 여십시오.
  3. 연관된 **팟(pod)** 이름이 표시될 때까지 아래로 스크롤하십시오. **팟** 이름을 기록해 두십시오.
  4. 팟을 클릭하여 여십시오.
  5. **컨테이너** 탭을 클릭하십시오. 원격 피어 **컨테이너** 이름을 기록해 두십시오.

2. 명령행에서 다음 명령을 실행하여 피어를 다시 시작하고 `<REMOTE_PEER_POD_NAME>` 및 `<REMOTE_PEER_CONTAINER_NAME>`을 위의 값으로 바꾸십시오.

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. `kubectl get pods` 명령을 실행하고 출력에서 팟의 **RESTART** 수를 검사하여 원격 피어가 다시 시작되었는지 확인할 수 있습니다.

또한 [HEAD 요청](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes)을 사용하여 원격 피어의 가용성을 확인할 수 있습니다.


## Fabric SDK를 사용하여 원격 피어 운영
{: #remote-peer-operate-with-sdk}

Hyperledger Fabric SDK에서는 애플리케이션에서 Blockchain 네트워크와 상호작용하고 운영하는 데 사용할 수 있는 강력한 API 세트를 제공합니다. [Hyperledger Fabric SDK 커뮤니티 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 커뮤니티 문서")에서 Fabric SDK 내에서 지원되는 최신 언어 목록과 사용 가능한 전체 API 목록을 찾을 수 있습니다. Fabric SDK를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform의 채널에 원격 피어가 가입하고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화할 수 있습니다.

다음 지시사항에서는 [Fabric Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Fabric Node SDK")를 사용하여 원격 피어를 운영하고 이미 SDK에 익숙하다고 가정합니다. [애플리케이션 개발 튜토리얼](/docs/services/blockchain/v10_application.html)은 시작하기 전에 Node SDK를 사용하는 방법을 알아보고 체인코드를 호출하여 조회할 준비가 되었을 때 원격 피어로 애플리케이션을 개발하기 위한 가이드로 사용할 수 있습니다.

### Node SDK 설치

NPM을 사용하여 [Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Node SDK")를 설치할 수 있습니다.
```
npm install fabric-client@1.1
```
{:codeblock}

Node SDK 버전 1.1을 사용하는 것이 좋습니다.

### 원격 피어와 작업하기 위해 SDK 준비
{: #remote-peer-node-sdk}

SDK를 사용하여 원격 피어를 운영하기 전에 애플리케이션이 {{site.data.keyword.blockchainfull_notm}} Platform과 원격 피어에서 네트워크와 통신하는 데 필요한 인증서(등록)를 생성해야 합니다. **admin** ID를 사용하여 [SDK에 등록](/docs/services/blockchain/v10_application.html#enroll-app-sdk)하는 단계를 수행하십시오. [애플리케이션 개발](/docs/services/blockchain/v10_application.html) 튜토리얼에서도 **admin**으로 등록하므로 샘플 코드를 수정하지 않아도 됩니다.

### {{site.data.keyword.blockchainfull_notm}} Platform에 signCert 업로드
{: #remote-peer-upload-sdk}

애플리케이션에서 네트워크를 운영할 수 있는 권한이 있도록 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 SDK 서명 인증서를 업로드해야 합니다.

- SDK에서 암호화 자료를 생성한 디렉토리의 admin이라는 파일에서 서명 인증서를 찾을 수 있습니다. `-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE-----`로 끝나는 `certificate` 필드 다음에 있는 물음표 내부에 인증서를 복사하십시오. `echo -e "<CERT>" > admin.pem` 명령을 실행하여 인증서를 PEM 형식으로 변환하는 데 CLI를 사용할 수 있습니다. 그런 다음 네트워크 모니터를 사용하여 인증서 컨텐츠를 Blockchain 네트워크에 붙여넣을 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 로그인하십시오. 네트워크 모니터의 "멤버" 화면에서 **인증서** > **인증서 추가**를 클릭하십시오. 인증서의 이름을 지정하고 **인증서** 필드에 컨텐츠를 붙여넣으십시오. 피어를 다시 시작할지 물으면 **다시 시작**을 클릭하십시오. 조직이 채널에 가입하기 전에 이 조치를 수행해야 합니다.

### 원격 피어에 signCert 업로드
{: #remote-peer-upload-signcert}

또한 SDK의 서명 인증서를 원격 피어에 업로드한 후 다시 시작해야 합니다. 원격 피어 컨테이너에서 [uploaded to the {{site.data.keyword.blockchainfull_notm}} Platform에 업로드](#remote-peer-upload-sdk)한 서명 인증서를 설치해야 합니다.

[피어 컨테이너 내부](#remote-peer-kubectl-configure)에서 다음 명령을 실행하여 인증서를 업로드하십시오.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

`-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE-----`로 끝나는 signCert로 `<CERT.PEM>` 텍스트를 바꾸십시오. cert.pem 파일은 이 디렉토리에 이미 있다는 점을 **참고**하십시오. 새 인증서를 `cert2.pem` 또는 이와 유사한 이름으로 지정하여 컨테이너에 이미 있는 인증서를 겹쳐쓰지 않게 하십시오.

새 인증서를 추가했으므로 피어에서 인증서를 선택하도록 컨테이너를 다시 시작해야 합니다. 다음 [지시사항](#remote-peer-restart)을 따라 피어를 다시 시작하십시오.

### 원격 피어의 TLS 인증서를 SDK에 전달
{: #icp-remote-peer-download-tlscert}

SDK의 통신을 인증하려면 TLS `cacert.pem`의 컨텐츠를 피어 컨테이너에서 애플리케이션으로 복사해야 합니다. [피어 CLI 컨테이너](#remote-peer-kubectl-configure)에서 다음 명령을 실행하십시오.
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

`cacert.pem` 파일의 전체 텍스트를 클립보드로 복사하십시오. 해당 텍스트는 `-----BEGIN CERTIFICATE-----`로 시작하고 맨 마지막 `-----END CERTIFICATE-----`로 끝납니다. 이 인증서를 PEM 형식으로 로컬 파일 시스템에 저장하십시오. 그런 다음 간단한 읽기 파일 명령을 사용하여 TLS 인증서를 애플리케이션에 가져올 수 있습니다.
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### SDK에 원격 피어 엔드포인트 정보 제공
{: #remote-peer-SDK-endpoints}

[원격 피어의 엔드포인트 정보](#remote-peer-retrieve-endpoint-info)를 찾아 새 피어 변수를 선언하거나 연결 프로파일을 업데이트하여 SDK에 제공하십시오. 다음 예에서는 패브릭 네트워크에서 피어를 엔드포인트로 정의하고 가져온 TLS 인증서를 전달합니다.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

피어에서 DNS 요청을 해결할 수 있도록 `<something>.blockchain.com`의 `ssl-target-name-override`를 지정해야 합니다.

**참고:** 피어가 원격이므로 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 기타 조직은 연결 프로파일에서 피어의 엔드포인트 정보를 찾을 수 없습니다. 다른 조직에서 인증할 트랜잭션을 원격 피어 트랜잭션에 보내면 대역 내외 오퍼레이션에서 공용 IP와 TLS 인증서를 제공해야 합니다.

### SDK를 사용하여 채널에 가입
{: #remote-peer-join-channel-sdk}

Blockchain 네트워크의 구성원으로서 조직이 네트워크의 채널에 추가되어야 원격 피어를 채널에 가입할 수 있습니다.

  - 원격 피어의 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 하나 이상 있어야 네트워크 모니터에서 채널을 작성할 수 있습니다.  

  - Blockchain 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다. 플랫폼에서 피어를 호스팅하지 않는 경우에도 {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 있는 채널 구성원은 네트워크 모니터를 사용하여 채널에 조직을 추가할 수 있습니다.

    채널에 조직을 추가하고 나면 다른 구성원이 트랜잭션 중에 디지털 서명을 확인할 수 있도록 피어의 서명 인증서를 채널에 추가해야 합니다. 원격 피어에서 설치 중에 서명 인증서를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다.

조직이 채널의 일부이면 [채널 가입](/docs/services/blockchain/v10_application.html#join-channel-sdk)의 지시사항을 따르십시오. 주문 서비스와 채널 이름의 URL을 제공해야 합니다.

### SDK를 사용하여 피어에 체인코드 설치
{: #remote-peer-install-cc-sdk}

SDK를 사용하여 원격 피어에 [체인코드를 설치](/docs/services/blockchain/v10_application.html#install-cc-sdk)하려면 다음 지시사항을 사용하십시오.

### SDK를 사용하여 채널에서 체인코드 인스턴스화
{: #remote-peer-instantiate-cc-sdk}

채널의 한 멤버만 체인코드를 인스턴스화하거나 업데이트하면 됩니다. 따라서 {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 있는 채널의 네트워크 구성원이 네트워크 모니터를 사용하여 체인코드를 인스턴스화하고 인증 정책을 지정할 수 있습니다. 그러나 원격 피어를 사용하여 채널에서 체인코드를 인스턴스화하려면 SDK를 사용하고 지시사항에 따라 [체인코드를 인스턴스화](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk)할 수 있습니다.


## CLI를 사용하여 원격 피어 운영
{: #remote-peer-cli-operate}

Fabric CA Client 및 Fabric 도구 컨테이너를 사용하여 명령행에서 원격 피어를 운영할 수도 있습니다. 이 지시사항에서는 먼저 Fabric CA Client를 사용하여 필요한 인증서를 생성한 후 Fabric 도구 컨테이너를 사용하여 채널에 가입하고 체인코드를 설치한 다음 채널에서 체인코드를 인스턴스화하여 피어를 운영합니다.

### Fabric CA Client를 사용하여 등록
{: #peer-client-enroll}

첫 번째 단계에서는 Fabric CA Client를 사용하여 필요한 인증서(등록)를 생성합니다. 먼저 Fabric CA Client를 설치해야 합니다. [플랫폼에 맞는 fabric-ca 2진 파일 v1.1.0](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/)을 로컬 시스템에 다운로드하고 압축을 푼 다음 `$HOME/fabric-ca-remote/` 등의 폴더로 이동하십시오.

1.  Fabric CA Client를 사용하도록 환경을 준비하십시오. 명령에서 직접 참조할 수 있도록 클라이언트 2진 파일을 이동한 디렉토리로 변경하십시오.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  클라이언트에서 키를 작성할 수 있는 경로를 설정하십시오. `enroll` 명령을 처음으로 실행하는 경우에는 `msp` 폴더와 `.yaml` 파일이 없습니다. 이전에 `enroll` 명령을 실행한 경우 아래 `rm` 명령을 사용하여 이전 등록에서 사용한 구성 자료를 모두 삭제하는 것이 중요합니다.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  {{site.data.keyword.blockchainfull_notm}} Platform에서 인증 기관의 엔드포인트 정보를 검색해야 합니다. 네트워크 모니터의 "개요" 화면에서 **연결 프로파일** 단추를 클릭하고 네트워크 인증 정보가 포함된 JSON 파일을 여십시오. 아래로 스크롤하여 `certificateAuthorities` 섹션으로 이동한 후 아래 정보를 기록해 두십시오.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  CA와 통신하려면 {{site.data.keyword.blockchainfull_notm}} Platform에서 TLS 인증서도 검색해야 합니다. 위의 네트워크 인증 정보에는 필요한 인증서가 포함되어 있습니다. 연결 프로파일 파일의 `certificateAuthorities` 섹션에서 `"pem:"` 다음에 오는 인증서를 복사하십시오. 이 인증서는 `-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE----- `로 종료됩니다. **따옴표는 포함하지 마십시오.**.

    `<certificate>`를 복사한 인증서로 바꾸어 터미널 창에서 다음 명령을 실행하십시오.
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 이후 명령에서 이 인증서를 참조할 수 있는 위치에 이 인증서를 저장하십시오.

5.  이제 필수 인증서를 생성할 준비가 되었습니다. 다음 명령을 실행하십시오.
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    예를 들어, 다음과 같습니다.
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    명령이 완료되면 다음 예제와 비슷한 응답이 표시됩니다.
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    이 명령에서는 필수 인증서를 생성하고 `$FABRIC_CA_CLIENT_HOME`의 `msp`라는 폴더에 저장합니다. 이 폴더와 포함된 인증서는 다음 단계에서 중요합니다.

### 로컬 시스템에서 인증서 관리
{: #manage-certs}

로컬 시스템에서 인증서를 관리하고 Fabric CA Client에서 생성한 인증서를 {{site.data.keyword.blockchainfull_notm}} Platform과 피어에 업로드해야 원격 피어를 운영할 수 있습니다. Platform과 피어에서 TLS 인증서도 다운로드해야 합니다. 작업할 인증서와 수행할 작업에 관한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform에서 인증서 관리](/docs/services/blockchain/certificates.html)를 참조하십시오.

로컬 시스템에서 명령 터미널을 열고 Fabric-CA-Client 2진 파일을 이동하고 MSP 폴더를 저장한 디렉토리로 이동합니다.
1. `cert.pem` 파일을 `signcerts` 폴더에서 새 `admincerts` 폴더로 복사하십시오.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. 또한 원격 피어의 TLS 인증서를 ICP의 피어 컨테이너에서 로컬 시스템으로 복사해야 합니다.

  - [피어 컨테이너에 로그인](#remote-peer-kubectl-configure)한 후 다음 명령을 실행하십시오.
    ```
    cat /mnt/certs/tls/cacert.pem
    ```
    {:codeblock}

    `cacert.pem` 파일의 전체 텍스트를 클립보드로 복사하십시오. 해당 텍스트는 `-----BEGIN CERTIFICATE-----`로 시작하고 맨 마지막 `-----END CERTIFICATE-----`로 끝납니다. 

  - 로컬 시스템으로 다시 전환하고 다음 명령을 실행하십시오. `<CACERT.PEM>` 텍스트를 클립보드의 텍스트로 바꾸십시오.
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
    {:codeblock}

3. 피어에서 체인코드를 설치할 수 있는 권한을 CLI에 부여하려면 Fabric CA Client에서 생성한 signCert를 피어 관리 폴더로 업로드하고 피어를 다시 시작해야 합니다. 따라서 `admincert/cert.pem` 인증서를 `cert2.pem`으로 로컬 시스템에서 피어 컨테이너에 있는 `/mnt/crypto/peer/peer/msp/admincerts/` 디렉토리로 복사하십시오.

    - 로컬 시스템에서 다음 명령을 실행하십시오.

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - `cert.pem` 파일에서 `-----BEGIN CERTIFICATE-----`로 시작하여 `-----END CERTIFICATE-----`로 끝나는 텍스트를 클립보드에 복사하십시오.

    - 피어 컨테이너로 다시 전환한 후 다음 명령을 실행하여 cert.pem을 피어 인증서에 추가하십시오. `<CERT.PEM>` 텍스트를 클립보드의 텍스트로 바꾸십시오.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **참고:** cert.pem 파일이 이미 이 디렉토리에 있습니다. 해당 파일을 겹쳐쓰지 마십시오.

    - 새 인증서를 추가했으므로 피어에서 인증서를 선택하도록 컨테이너를 다시 시작해야 합니다. 다음 [지시사항](#remote-peer-restart)을 따라 피어를 다시 시작하십시오.

4. 원격 CLI나 애플리케이션에서 채널 최초 블록을 페칭하고 체인코드를 인스턴스화하는 등의 채널 오퍼레이션을 수행하려면 동일한 `admincert/cert.pem` 인증서를 로컬 시스템에서 {{site.data.keyword.blockchainfull_notm}} Platform으로 업로드해야 합니다.
    1. 로컬 시스템에서 `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` 파일을 카탈로그화한 다음 클립보드에 복사하십시오.
    2. {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크의 네트워크 모니터를 입력하십시오.
    3. 왼쪽 네비게이터에서 **멤버**를 클릭하고 **인증서** 탭을 클릭하십시오.
    4. **인증서 추가** 단추를 클릭하십시오.
    5. **인증서 추가** 창에서 `fabrictools.pem`과 같이 인증서의 이름을 지정하고 클립보드에 방금 복사한 인증서를 붙여넣은 다음 **제출**을 클릭하십시오.
    6. 피어를 다시 시작하려는지 묻습니다. **다시 시작**을 클릭하십시오.
    7. 왼쪽 네비게이터에서 **채널**을 클릭하고 해당 원격 피어가 가입할 채널을 찾으십시오.
    8. **조치** 헤더 아래 세 개의 점을 클릭하고 **인증서 동기화**를 클릭하십시오. **인증서 동기화** 창에서 **제출**을 클릭하십시오.

    **참고**: 피어가 아래 채널에 가입하기 전에 채널 인증서를 동기화하는 것이 중요합니다.

### Fabric 도구 컨테이너 설정

모든 인증서를 필요한 위치로 이동하고 나면 Fabric 도구 컨테이너를 설치하고 Docker에서 사용할 준비가 됩니다. MSP 폴더를 저장한 디렉토리에서 다시 한 번 해당 명령을 실행해야 합니다.

1.  다음 명령을 사용하여 Fabric 도구 Docker 이미지를 다운로드하십시오.
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  다음 명령을 실행하여 Fabric 도구 컨테이너를 시작하십시오.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    이 명령은 실행하면 도구 컨테이너에서 MSP 디렉토리를 마운트합니다.

3.  원격 피어 주소는 접미부가 `blockchain.com`인 도메인 이름으로 분석되어야 합니다. DNS를 통하여 이 작업을 수행하거나 /etc/hosts 파일을 수정할 수 있습니다. 이 지시사항을 위해 Fabric 도구 컨테이너에서 `/etc/hosts` 파일을 수정합니다. ICP 콘솔에 로그인한 다음 원격 피어 [엔드포인트 정보](#remote-peer-retrieve-endpoint-info)를 찾으십시오. 피어 `INGRESS_IP`가 있으면 원격 피어 주소를 사용하여 다음 명령을 실행하십시오.
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Fabric 도구 컨테이너를 사용하여 원격 피어를 채널에 가입
{: #icp-cli-join-peer-to-channel}

네트워크의 채널에 조직을 추가해야 원격 피어를 채널에 가입시키는 CLI 명령을 실행할 수 있습니다.

  - 원격 피어의 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 하나 이상 있어야 네트워크 모니터에서 채널을 작성할 수 있습니다.  

  - Blockchain 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다. 플랫폼에서 피어를 호스팅하지 않는 경우에도 {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 있는 채널 구성원은 네트워크 모니터를 사용하여 채널에 조직을 추가할 수 있습니다.

    채널에 조직을 추가하고 나면 다른 구성원이 트랜잭션 중에 디지털 서명을 확인할 수 있도록 피어의 서명 인증서를 채널에 추가해야 합니다. 원격 피어에서 설치 중에 서명 인증서를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다.

1. 네트워크 모니터의 **개요** 패널에서 사용할 수 있는 `creds.json` 파일에서 구성 정보를 검색하십시오. **연결 프로파일**을 클릭한 다음 **다운로드**를 클릭하십시오.

  - `orderers > url`에 있는 **순서 지정자**를 검색하여 순서 지정자 URL을 찾으십시오. 네트워크 이름으로 시작하는 URL을 기록해 두십시오. 이 URL은 다음 예와 비슷합니다.
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - **조직**을 검색하여 조직의 이름을 찾으십시오. 이 조직은 원격 피어를 등록하는 데 사용하는 조직과 같아야 합니다. 연관된 `mspid`와 함께 조직의 이름을 찾을 수 있습니다. `mspid`의 값을 기록해 두십시오.

2. [원격 피어 엔드포인트 정보 찾기](#remote-peer-retrieve-endpoint-info)를 수행하십시오. 이때 `Ingress IP` 옆에 나열된 `Ports`를 기록해 두십시오.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  30K 범위에서 포트를 기록해 두십시오. 해당 포트는 패브릭 도구 컨테이너에서 주소를 지정할 수 있는 포트로서, `PEERADDR` 환경 변수를 설정하는 데 사용할 수 있습니다.

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  따라서 사용할 `PEERADDR`은 `/etc/hosts` 파일에 지정한 값과 `7051`에 바인드된 포트(이 예에서는 `30162`)를 함께 사용하여 구성됩니다. 결과적으로 `PEERADDR`의 값은
 `remotepeer.blockchain.com:30162`이 됩니다.

3. 다음 명령을 실행하여 Fabric 도구 컨테이너에서 환경 변수를 설정하십시오.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  고유 정보로 필드를 바꾸십시오.
    - `<ORDERER_URL>`을 `creds.json` 파일의 순서 지정자 호스트 이름과 포트로 바꾸십시오.
    - `<CHANNEL_NAME>`을 피어가 가입하는 채널의 이름으로 바꾸십시오.
    - `<CC_NAME>`을 체인코드를 참조하는 이름으로 바꾸십시오.
    - `<ORGANIZATION_MSP_ID>`를 `creds.json` 파일의 조직 이름으로 바꾸십시오.
    - `<PEER_ADDR>`을 이전 단계에서 구성한 값으로 바꾸십시오.

  예를 들어, 다음과 같습니다.

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. 채널의 최초 블록을 페치하여 피어에서 채널 원장을 빌드하십시오. 먼저 루트 디렉토리로 `cd`한 후 명령을 실행하여 최초 블록을 페치하십시오.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
  ```
  {:codeblock}

  다음 명령을 실행하여 최초 블록이 피어 컨테이너에 제대로 추가되었는지 확인하십시오.

  ```
  ls *.block
  ```
  {:codeblock}

  다음 예와 비슷한 내용이 표시되면 최초 블록이 제대로 추가된 것입니다.
  ```
  defaultchannel_0.block
  ```

5. 다음 명령을 사용하여 채널에 피어를 가입시키십시오.

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  이 작업이 성공적으로 작동하면 다음 예와 유사한 정보가 표시되어야 합니다.

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric 도구 컨테이너를 사용하여 원격 피어에 체인코드 설치
{: #icp-toolcontainer-install-cc}
이제 원격 피어에 체인코드를 설치하고 인스턴스화할 준비가 되었습니다. 이 지시사항에서 사용하기 위해 `fabric-samples` 저장소에서 `fabcar`를 설치합니다. 다음 명령을 사용하여 `github.com`에서 `fabric-samples` 체인코드를 다운로드하십시오.

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

다음 피어 CLI 명령을 실행하여 `fabcar` 체인코드를 원격 피어에 설치하십시오.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric 도구 컨테이너를 사용하여 채널에서 체인코드 인스턴스화
{: #icp-toolcontainer-instantiate-cc}

한 피어만 채널에서 체인코드를 인스턴스화하면 되므로 원격 피어를 사용하여 체인코드를 인스턴스화하지 않아도 됩니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어에서 해당 작업을 수행할 수 있습니다. 그러나 원격 피어가 채널에서 체인코드를 인스턴스화하게 하려면 Fabric Tools 컨테이너에서 다음 명령을 실행할 수 있습니다.

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## {{site.data.keyword.cloud_notm}} Private에서 원격 피어 로그 보기
{: #remote-peer-log-icp}

ICP 콘솔에서 원격 피어 로그에 액세스하고 Kibana 인터페이스에서 로그를 확인할 수 있습니다.

1. ICP 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하십시오.
2. 메뉴 표시줄에서 **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스 테이블에서 helm 릴리스의 이름을 클릭하십시오.
4. 아래로 스크롤하여 **팟(Pod)** 섹션으로 이동하고 테이블 행의 끝에서 **로그 보기** 링크를 클릭하십시오. 원격 피어 로그가 Kibana 인터페이스에서 열립니다.

## 체인코드 업데이트

시간 경과에 따라 원격 피어에서 실행 중인 체인코드를 수정해야 할 가능성이 있습니다. 체인코드는 피어에 설치되어 채널에서 인스턴스화되므로 채널의 모든 피어에서 체인코드를 업데이트해야 합니다.

체인코드를 업데이트하려면 다음 단계를 완료하십시오.

1. 각 원격 피어에서 체인코드를 업데이트하려면 클라이언트 애플리케이션이나 CLI 명령을 사용하여 피어에 체인코드를 설치하는 데 사용한 프로세스를 다시 실행하기만 하면 됩니다. 원래 사용된 이름과 동일한 체인코드 이름을 지정하십시오. 그러나 이번에는 체인코드 `Version`을 늘르십시오. 모든 피어는 같은 이름과 버전을 사용해야 합니다.

2. 채널의 모든 피어에 새 체인코드를 설치한 다음 네트워크 모니터 또는 [피어 체인코드 업그레이드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 명령을 사용하여 새 체인코드를 사용하도록 채널을 업데이트하십시오.

네트워크 모니터의 "코드 설치" 패널을 사용하여 채널에서 체인코드를 업데이트하는 데 관한 자세한 정보는 해당 [지시사항](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) 중 2단계를 참조하십시오.

## 문제점 해결
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **문제점:** 피어 컨테이너에 로그인할 때 오류 발생
{: #icp-bash-error}

이 문제점은 다음 명령을 실행하여 피어를 실행하는 데 kubectl 도구를 사용할 때 발생합니다.
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
실패하고 다음 예와 같은 오류 메시지를 생성합니다.
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**솔루션:**  

이 오류는 IBM System Z와 같은 Big Endian 시스템에서 두 개의 Docker Bash 세션이 동일한 Docker 컨테이너에서 열리면 발생합니다. 두 번째 Bash 세션에서 실행 중인 컨테이너에 연결하지 못할 수 있습니다. 이 문제를 해결하려면 [피어 컨테이너를 다시 시작](#remote-peer-restart)하고 피어를 다시 시작한 후 `kubectl exec` 명령을 재시도하십시오.

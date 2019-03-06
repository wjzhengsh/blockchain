---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# AWS에서 피어 운영
{: #remote-peer-aws-operate}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


AWS에 {{site.data.keyword.blockchainfull}} Platform 피어를 설정한 다음 여러 작업 단계를 완료해야 피어가 블록체인 네트워크의 원장을 조회하고 호출하기 위한 트랜잭션을 발행할 수 있습니다. 이 단계에는 채널에 조직을 추가하고 피어가 채널에 가입하며 피어에 체인코드를 설치하고 채널에서 체인코드를 인스턴스화하며 애플리케이션을 피어에 연결하는 단계가 포함됩니다. [Fabric SDK](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-with-sdk) 또는 [명령행](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-cli-operate)을 사용하여 이러한 작업 단계를 완료할 수 있습니다. 지시사항에서는 SDK 오퍼레이션에 익숙하다고 가정하지만 Fabric SDK 경로를 사용하는 것이 좋습니다.

**참고**: AWS의 {{site.data.keyword.blockchainfull_notm}} Platform 피어에서는 {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어의 전체 기능 또는 지원에 액세스할 권한이 없습니다. 따라서 네트워크 모니터를 사용하여 피어를 작동시킬 수 없습니다. AWS에서 피어 실행을 시작하기 전에 [고려사항](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-limitations)을 검토하십시오.

## Fabric SDK를 사용하여 피어 운영
{: #remote-peer-aws-operate-with-sdk}

Hyperledger Fabric SDK에서는 애플리케이션에서 블록체인 네트워크와 상호작용하고 운영하는 데 사용할 수 있는 강력한 API 세트를 제공합니다. [Hyperledger Fabric SDK 커뮤니티 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 커뮤니티 문서")에서 Hyperledger Fabric SDK 내에서 지원되는 최신 언어 목록과 사용 가능한 전체 API 목록을 찾을 수 있습니다. Fabric SDK를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform의 채널에 피어를 가입시키고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화할 수 있습니다.

다음 지시사항에서는 [Fabric Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js")를 사용하여 피어를 운영하고 이미 SDK에 익숙하다고 가정합니다. [애플리케이션 개발 튜토리얼](/docs/services/blockchain/v10_application.html#dev-app)은 시작하기 전에 Node SDK를 사용하는 방법을 알아보고 체인코드를 호출하여 조회할 준비가 되었을 때 피어로 애플리케이션을 개발하기 위한 안내서로 사용할 수 있습니다.

AWS 빠른 시작의 {{site.data.keyword.blockchainfull_notm}} Platform 피어는 고가용성을 위해 두 개의 피어를 작성합니다. 따라서 피어마다 해당 오퍼레이션 단계를 한 번씩 수행해야 합니다. 애플리케이션에서 체인코드를 조회하고 호출할 준비가 되면 [애플리케이션의 고가용성](/docs/services/blockchain/v10_application.html#dev-app-ha-app)을 위해 SDK를 두 피어 모두에 연결하십시오.

### Node SDK 설치
{: #remote-peer-aws-operate-install-sdk}

NPM을 사용하여 [Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js")를 설치할 수 있습니다.
```
npm install fabric-client@1.2
```
{:codeblock}

Node SDK 버전 1.2를 사용하는 것이 좋습니다.

### 피어와 작업하기 위해 SDK 준비
{: #remote-peer-aws-operate-sdk}

SDK를 사용하여 피어를 운영하기 전에 애플리케이션이 {{site.data.keyword.blockchainfull_notm}} Platform과 피어에서 네트워크와 통신하는 데 필요한 인증서(등록)를 생성해야 합니다. **admin** ID를 사용하여 [SDK에 등록](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk)하는 단계를 수행하십시오. [애플리케이션 개발](/docs/services/blockchain/v10_application.html#dev-app) 튜토리얼에서도 **admin**으로 등록하므로 샘플 코드를 수정하지 않아도 됩니다.

### IBM Blockchain Platform에 signcert 업로드
{: #remote-peer-aws-operate-upload-SDK}

다른 멤버가 디지털 서명을 인식할 수 있도록 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 SDK 서명 인증서를 업로드해야 합니다.

- SDK에서 암호화 자료를 생성한 디렉토리의 admin이라는 파일에서 서명 인증서를 찾을 수 있습니다. `-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE-----`로 끝나는 `certificate` 필드 다음에 있는 물음표 내부에 인증서를 복사하십시오. `echo -e "<CERT>" > admin.pem` 명령을 실행하여 인증서를 PEM 형식으로 변환하는 데 CLI를 사용할 수 있습니다. 그런 다음 네트워크 모니터를 사용하여 인증서 컨텐츠를 블록체인 네트워크에 붙여넣을 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 로그인하십시오. 네트워크 모니터의 "멤버" 화면에서 **인증서** > **인증서 추가**를 클릭하십시오. 인증서의 이름을 지정하고 **인증서** 필드에 컨텐츠를 붙여넣으십시오. 피어를 다시 시작할지 물으면 **다시 시작**을 클릭하십시오. 조직이 채널에 가입하기 전에 이 조치를 수행해야 합니다.

### 피어에 signcert 업로드
{: #remote-peer-aws-operate-upload-signcert}

또한 SDK의 서명 인증서를 원격 피어에 업로드한 후 다시 시작해야 합니다. 원격 피어 컨테이너에서 [IBM Blockchain Platform에 업로드](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-upload-SDK)한 서명 인증서를 설치해야 합니다.

AWS 콘솔에서 인스턴스를 선택(**서비스 > EC2 > 인스턴스** 클릭)한 다음 연결 단추를 클릭하여 VPC 인스턴스로 SSH를 실행하십시오. ssh 명령을 실행하려면 AWS의 지시사항을 준수하십시오.

피어 컨테이너 내부에서 다음 명령을 실행하여 인증서를 업로드하십시오.  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- `<PEER_ENROLL_ID>`를 빠른 시작 템플리트에 지정되고 이 원격 피어 인스턴스와 연관된 등록 ID로 바꾸십시오.  
- `-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE-----`로 끝나는 signCert로 `<CERT.PEM>`을 바꾸십시오.    

  **참고:** 파일 `cert.pem`이 있으면 이를 겹쳐쓰지 말고 새 파일(예: `cert2.pem`)을 작성하십시오.

새 인증서를 추가했으므로 피어에서 인증서를 선택하도록 컨테이너를 다시 시작해야 합니다. 다음 [지시사항](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-restart)을 따라 피어를 다시 시작하십시오.

### 피어의 TLS 인증서를 SDK에 전달
{: #remote-peer-aws-operate-download-tlscert}

SDK의 통신을 인증하려면 TLS `cacert.pem`의 컨텐츠를 피어 컨테이너에서 애플리케이션으로 복사해야 합니다. 피어 컨테이너에서 다음 명령을 실행하십시오. `<PEER_ENROLL_ID>`를 빠른 시작 템플리트에 지정된 원격 피어의 스택 이름으로 바꾸십시오. (두 개의 VPC 인스턴스가 작성됨을 상기하십시오.)
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

`ca.crt` 파일의 전체 텍스트를 클립보드로 복사하십시오. 해당 텍스트는 `-----BEGIN CERTIFICATE-----`로 시작하고 맨 마지막 `-----END CERTIFICATE-----`로 끝납니다. 이 인증서를 PEM 형식으로 로컬 파일 시스템에 저장하십시오. 그런 다음 간단한 읽기 파일 명령을 사용하여 TLS 인증서를 애플리케이션에 가져올 수 있습니다.
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### SDK에 피어 엔드포인트 정보 제공
{: #remote-peer-aws-operate-SDK-endpoints}

피어의 엔드포인트 정보를 검색하려면 AWS 콘솔에서 피어 인스턴스를 찾으십시오. EC2 인스턴스의 `AWS EC2 dashboard Public DNS (IPv4)`의 값을 기록하고 새 피어 변수를 선언하거나 연결 프로파일을 업데이트하여 SDK에 제공하십시오. 포트 `7051`에 공용 DNS 주소를 연결하십시오. 다음 예에서는 패브릭 네트워크에서 피어를 정의하고 가져온 TLS 인증서를 전달합니다.

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**참고:** 피어가 원격이므로 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 기타 조직은 연결 프로파일에서 피어의 엔드포인트 정보를 찾을 수 없습니다. 다른 조직에서 인증할 트랜잭션을 피어에 보내면 대역 내외 오퍼레이션에서 공용 IP와 TLS 인증서를 제공해야 합니다.

### SDK를 사용하여 채널에 가입
{: #remote-peer-aws-operate-join-channel-sdk}

블록체인 네트워크의 구성원으로서 조직이 네트워크의 채널에 추가되어야 피어를 채널에 가입할 수 있습니다.

  - 피어의 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다.

  - 블록체인 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다.

    채널에 조직을 추가하고 나면 다른 구성원이 트랜잭션 중에 디지털 서명을 확인할 수 있도록 피어의 서명 인증서를 채널에 추가해야 합니다. 피어에서 설치 중에 서명 인증서를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다. 채널 가입 명령을 실행하기 전에 채널 동기화가 완료되도록 몇 분 동안 기다려야 합니다.

조직이 채널의 일부이면 [채널 가입](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk)의 지시사항을 따르십시오. 순서 지정 서비스의 URL과 채널 이름을 제공해야 합니다.

### SDK를 사용하여 피어에 체인코드 설치
{: #remote-peer-aws-operate-install-cc-sdk}

SDK를 사용하여 피어에 [체인코드를 설치](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk)하려면 다음 지시사항을 준수하십시오.

### SDK를 사용하여 채널에서 체인코드 인스턴스화
{: #remote-peer-aws-operate-instantiate-cc-sdk}

채널의 한 멤버만 체인코드를 인스턴스화하거나 업데이트하면 됩니다. 따라서 {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 있는 채널의 네트워크 구성원이 네트워크 모니터를 사용하여 체인코드를 인스턴스화하고 인증 정책을 지정할 수 있습니다. 그러나 피어를 사용하여 채널에서 체인코드를 인스턴스화하려면 SDK를 사용하고 지시사항에 따라 [체인코드를 인스턴스화](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk)할 수 있습니다.


## CLI를 사용하여 피어 운영
{: #remote-peer-aws-operate-cli-operate}

Fabric CA Client 및 Fabric 도구 컨테이너를 사용하여 명령행에서 피어를 운영할 수도 있습니다. 이 지시사항에서는 먼저 Fabric CA client를 사용하여 필요한 인증서를 생성합니다. 그런 다음 Fabric 도구 컨테이너를 사용하여 채널에 가입하고 체인코드를 설치한 다음 채널에서 체인코드를 인스턴스화하여 피어를 운영합니다.

### Fabric CA Client를 사용하여 등록
{: #remote-peer-aws-operate-client-enroll}

첫 번째 단계에서는 Fabric CA Client를 사용하여 필요한 인증서(등록)를 생성합니다. 먼저 Fabric CA Client를 설치해야 합니다. [플랫폼에 대한 fabric-ca 바이너리 v1.2.1 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/ "저장소 색인")을
로컬 시스템에 다운로드하여 압축을 푼 다음 `$HOME/fabric-ca-remote/` 등의 폴더로 이동하십시오.

1.  Fabric CA Client를 사용하도록 환경을 준비하십시오. 명령에서 직접 참조할 수 있도록 클라이언트 바이너리 파일을 이동한 디렉토리로 변경하십시오.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  클라이언트에서 키를 작성할 수 있는 경로를 설정하십시오.  `enroll` 명령을 처음 실행하는 경우 `msp` 폴더와 `.yaml` 파일이 없습니다. 이전에 `enroll` 명령을 실행한 경우 아래 `rm` 명령을 사용하여 이전 등록에서 사용한 구성 자료를 모두 삭제하는 것이 중요합니다.
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

    **팁:** 등록 url의 값, `-u` 매개변수값이 특수 문자를 포함하는 경우, 특수 문자를 인코딩하거나 작은따옴표로 url을 묶어야 합니다. 예를 들어, `!`가 `%21`이 되거나 다음과 유사하게 표시됩니다.

    ```
    ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
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
{: #remote-peer-aws-operate-manage-certs}

로컬 시스템에서 인증서를 관리하고 Fabric CA Client에서 생성한 인증서를 {{site.data.keyword.blockchainfull_notm}} Platform과 피어에 업로드해야 피어를 운영할 수 있습니다. Platform과 피어에서 TLS 인증서도 다운로드해야 합니다. 작업할 인증서와 수행할 작업에 관한
자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform에서 인증서 관리](/docs/services/blockchain/certificates.html#managing-certificates)를 참조하십시오.

로컬 시스템에서 명령 터미널을 열고 Fabric-CA-Client 바이너리 파일을 이동하고 MSP 폴더를 저장한 디렉토리로 이동합니다.

1. `cert.pem` 파일을 `signcerts` 폴더에서 새 `admincerts` 폴더로 복사하십시오.
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. {{site.data.keyword.blockchainfull_notm}} Platform의 순서 지정자 TLS 인증서를 MSP 폴더에 복사하십시오. 연결 프로파일에 필요한 인증서가 포함되어 있습니다. 네트워크 모니터의 "개요" 화면에서 **연결 프로파일** 단추를 클릭하고 네트워크 인증 정보가 포함된 JSON 파일을 여십시오.`orderers` 섹션으로 이동하십시오. `-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE----- `로 종료되는 `"pem:"` 다음에 오는 인증서를 복사하십시오. 따옴표를 포함하지 마십시오.

    터미널 창에서 다음 명령을 실행하십시오.
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. 또한 피어의 TLS 인증서를 AWS의 피어 컨테이너에서 로컬 시스템으로 복사해야 합니다.

    - [이 지시사항을 준수](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test)하여 피어 컨테이너에 로그인하고 해당 번호가 뒤에 오는 빠른 시작 템플리트에 지정한 피어의 스택 이름으로 <PEER_ENROLL_ID>를 대체하는 다음 명령을 실행하십시오. (두 개의 VPC 인스턴스가 작성됨을 상기하십시오.)
      ```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
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

      **참고:** 기본적으로 AWS 빠른 시작 템플리트는 다른 두 개의 가용성 구역에서 두 개의 피어 인스턴스를 작성합니다. 이러한 피어 중 하나에 대해 이미 이 단계를 완료한 경우 두 번째 인스턴스에 대해 이 단계를 실행하면 cacert.pem 파일이 이미 존재하게 됩니다. 계속 진행하여 이를 두 번째 피어의 인증서로 바꾸십시오.

4. 피어에서 체인코드를 설치할 수 있는 권한을 CLI에 부여하려면 Fabric CA Client에서 생성한 signCert를 피어의 관리 폴더로 업로드하고 피어를 다시 시작하십시오. 따라서 `admincert/cert.pem` 인증서를 `cert2.pem`으로 로컬 시스템에서 피어 컨테이너에 있는 `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` 디렉토리로 복사하십시오.

    - 로컬 시스템에서 다음 명령을 실행하십시오.

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - `cert.pem` 파일에서 `-----BEGIN CERTIFICATE-----`로 시작하여 `-----END CERTIFICATE-----`로 끝나는 텍스트를 클립보드에 복사하십시오.

    - 피어 컨테이너로 다시 전환한 후 다음 명령을 실행하여 cert.pem을 피어 인증서에 추가하십시오. `<CERT.PEM>` 텍스트를 클립보드의 텍스트로 바꾸십시오.

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **참고:** cert.pem 파일이 이미 이 디렉토리에 있습니다. 해당 파일을 겹쳐쓰지 마십시오.

    - 새 인증서를 추가했으므로 피어에서 인증서를 선택하도록 컨테이너를 다시 시작해야 합니다. 다음 지시사항을 따라 [피어 컨테이너를 다시 시작](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-restart)하십시오.

5. 원격 CLI나 애플리케이션에서 채널 최초 블록을 페칭하고 체인코드를 인스턴스화하는 등의 채널 오퍼레이션을 수행하려면 동일한 `admincert/cert.pem` 인증서를 로컬 시스템에서 {{site.data.keyword.blockchainfull_notm}} Platform으로 업로드하십시오.
    1. 로컬 시스템에서 `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` 파일을 카탈로그화한 다음 클립보드에 복사하십시오.
    2. {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크의 네트워크 모니터를 입력하십시오.
    3. 왼쪽 네비게이터에서 **멤버**를 클릭하고 **인증서** 탭을 클릭하십시오.
    4. **인증서 추가** 단추를 클릭하십시오.
    5. **인증서 추가** 창에서 `fabrictools.pem`과 같은 인증서의 이름을 지정하고 클립보드에 방금 복사한 인증서를 붙여넣은 다음 **제출**을 클릭하십시오.
    6. 피어를 다시 시작하도록 요청하는 경우, **다시 시작**을 클릭하십시오.
    7. 왼쪽 네비게이터에서 **채널**을 클릭하고 해당 피어가 가입할 채널을 찾으십시오.
    8. **조치** 헤더 아래 세 개의 점을 클릭하고 **인증서 동기화**를 클릭하십시오. **인증서 동기화** 창에서 **제출**을 클릭하십시오.

    **참고**: 피어가 채널에 가입하거나 채널에서 체인코드를 인스턴스화하기 전에 채널 인증서를 동기화하는 것이 중요합니다. 채널 가입 또는 체인코드 인스턴스화 명령을 실행하기 전에 채널 동기화가 완료되도록 몇 분 동안 기다려야 합니다.   

### Fabric 도구 컨테이너 설정
{: #remote-peer-aws-operate-fabric-cli}

모든 인증서를 필요한 위치로 옮기면 Docker의 Fabric 도구 컨테이너를 설치하고 사용할 수 있습니다. 이러한 명령은 시스템에서 로컬로 실행됩니다. 사용자의 MSP 폴더를 저장한 디렉토리에서 이러한 명령을 실행했는지 확인하십시오. 해당 단계를 완료하려면 [Git ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "시작하기 - Git 설치")가
로컬 시스템에 설치되어 있어야 합니다.   

다음 명령을 사용하여 Fabric 도구 Docker 이미지를 다운로드하십시오.

```
docker pull ibmblockchain/fabric-tools:1.2.1
```
{:codeblock}

#### 컨테이너에 대한 리소스 수집    

나중에 체인코드를 설치하고 인스턴스화할 때 사용할 `fabcar` 샘플을 포함하도록 로컬 시스템에 일부 폴더를 설정해야 합니다. 다음 명령을 실행하여 샘플 체인코드에 대한 폴더 구조를 설정한 다음 Git를 사용하여 다운로드하십시오.

```
mkdir toolsrc
cd toolsrc
mkdir mycc
mkdir vendor
cd vendor
git clone https://github.com/hyperledger/fabric.git
```
{:codeblock}

**참고:** 자체 체인코드 파일이 있는 경우, 해당 파일도 피어 CLI 명령에 사용되도록 `mycc` 폴더에
배치할 수 있습니다.  

다음 명령을 실행하여 Fabric 도구 컨테이너를 시작하십시오. 두 번째 명령은 MSP 및 Fabric 샘플 폴더를 도구 컨테이너에 마운트합니다.사용자의 MSP 폴더를 저장한 디렉토리에서 이러한 명령을 실행했는지 확인하십시오.

```
docker network create blockchain.com
docker run -ti --network blockchain.com -v ${PWD}:/mnt -v path/to/toolsrc:/src ibmblockchain/fabric-tools:1.2.1
```
{:codeblock}


<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### Fabric 도구 CLI를 사용하여 피어를 채널에 가입
{: #remote-peer-aws-operate-cli-join-channel}

네트워크의 채널에 조직을 추가해야 피어를 채널에 가입시키는 CLI 명령을 실행할 수 있습니다.

  - 피어의 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다.

  - 블록체인 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다.

    채널에 조직을 추가하고 나면 다른 구성원이 트랜잭션 중에 디지털 서명을 확인할 수 있도록 피어의 서명 인증서를 채널에 추가해야 합니다. 피어에서 설치 중에 서명 인증서를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다.

1. 네트워크 모니터의 개요 패널에서 사용 가능한 `Conection Profile` 파일에서 구성 정보를 검색하십시오. **연결 프로파일**을 클릭한 다음 **다운로드**를 클릭하십시오.

  - `orderers > url`에 있는 **순서 지정자**를 검색하여 순서 지정자 URL을 찾으십시오. 네트워크 이름으로 시작하는 URL을 기록해 두십시오. 이 URL은 다음 예와 비슷합니다.

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - **조직**을 검색하여 조직의 이름을 찾으십시오. 이 조직은 피어를 등록하는 데 사용하는 조직과 같아야 합니다. 연관된 `mspid`와 함께 조직의 이름을 찾을 수 있습니다. `mspid`의 값을 기록해 두십시오.

2. AWS EC2 대시보드 공용 DNS(IPv4)에서 사용자의 AWS VPC 피어(공용 DNS IPV4 주소)의 엔드포인트 정보를 찾으십시오. 다음과 같이 표시됩니다.

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  이 정보를 사용하여 주소를 '7051' 포트와 연결하여 환경 변수 `PEERADDR`을 빌드하십시오.
  `PEERADDR` 값은 다음과 유사하게 됩니다.

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. 다음 명령을 실행하여 Fabric 도구 컨테이너 내부의 환경 변수를 설정하십시오.

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

  예를 들면 다음과 같습니다.

    ```
    export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
    export CHANNEL=defaultchannel
    export CC_NAME=mycc
    export ORGID=PeerOrg1
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. 채널의 최초 블록을 페치하여 피어에서 채널 원장을 빌드하십시오. 먼저 루트 디렉토리로 `cd`한 후 명령을 실행하여 최초 블록을 페치하십시오.

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
  ```
  {:codeblock}

  **참고:** 해당 CLI 명령을 실행할 때 무시해도 되는 다음과 같은 경고가 표시될 수 있습니다.

  ```
[msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  다음 명령을 실행하여 최초 블록이 피어 컨테이너에 제대로 추가되었는지 확인하십시오.

  ```
  ls *.block
  ```
  {:codeblock}

  최초 블록이 정상적으로 추가되면 다음 예와 비슷한 내용이 표시되어야 합니다.
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. 이제 다음 명령을 사용하여 최초 블록에 전달하며 채널에 피어를 가입시키십시오.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  이 작업이 정상적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric 도구 컨테이너를 사용하여 피어에 체인코드 설치
{: #aws-toolcontainer-install-cc}

이제 피어에 체인코드를 설치하고 인스턴스화할 준비가 되었습니다. 이러한 지시사항을 수행하기 위해
[Fabric 도구 컨테이너를 설정](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-cli-operate)할 때
로컬 시스템에 이미 다운로드했어야 하는 Hyperledger `fabric-samples` 저장소에서
`fabcar` 체인코드를 설치할 것입니다.  

다음 피어 CLI 명령을 실행하여 `fabcar` 체인코드를 피어에 설치하십시오.

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
{: #remote-peer-aws-operate-oolcontainer-instantiate-cc}

한 피어만 채널에서 체인코드를 인스턴스화하면 되므로 피어를 사용하여 체인코드를 인스턴스화하지 않아도 됩니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어에서 해당 작업을 수행할 수 있습니다. 그러나 피어가 채널에서 체인코드를 인스턴스화하게 하려면 Fabric tools 컨테이너에서 다음 명령을 실행할 수 있습니다.

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## AWS에서 실행 중인 피어 재시작
{: #remote-peer-aws-operate-restart}

피어를 배치한 환경에서 피어를 시작, 중지 또는 재시작할 수 있습니다. 인증서를 피어에 추가할 때마다 새 인증서가 인식되도록 피어를 다시 시작해야 합니다. 이를 수행하는 방법은 여러 가지가 있습니다.

- AWS 콘솔에서 피어 VPC 인스턴스를 다시 부팅하십시오.
- 피어 컨테이너에서 다음 명령을 실행하십시오.

 ```
 docker restart peer
 ```
 {:codeblock}  

또한 [HEAD 요청](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-nodes)을 사용하여 피어의 가용성을 확인할 수 있습니다.

## 피어 로그 보기

AWS VPC 피어 인스턴스로 SSH를 실행한 후 다음 명령을 실행하여 피어 로그를 보십시오.

```
docker logs peer
```
{:codeblock}

## 체인코드 업데이트
{: #remote-peer-aws-operate-update-cc}

시간 경과에 따라 피어에서 실행 중인 체인코드를 수정해야 할 가능성이 있습니다. 체인코드는 피어에 설치되어 채널에서 인스턴스화되므로 채널의 모든 피어에서 체인코드를 업데이트해야 합니다.

체인코드를 업데이트하려면 다음 단계를 완료하십시오.

1. AWS의 각 피어에서 체인코드를 업데이트하려면 클라이언트 애플리케이션이나 CLI 명령을 사용하여 피어에 체인코드를 설치하는 데 사용한 프로세스를 다시 실행하기만 하면 됩니다. 원래 사용된 이름과 동일한 체인코드 이름을 지정하십시오. 그러나 이번에는 체인코드 `Version` 번호를 늘립니다.

2. 채널의 모든 피어에 새 체인코드를 설치한 다음 네트워크 모니터 또는 [피어 체인코드 업그레이드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 명령을 사용하여 새 체인코드를 사용하도록 채널을 업데이트하십시오.

네트워크 모니터의 코드 설치 패널을 사용하여 채널에서 체인코드를 업데이트하는 데 관한 자세한 정보는 해당 [지시사항](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) 중 2단계를 참조하십시오.

## 문제점 해결
{: #remote-peer-aws-operate-troubleshooting}

### **문제점:** 원격 피어에서 블록체인 네트워크에 연결할 수 없음
{: #remote-peer-aws-operate-problem-1}

스택 작성은 정상적으로 완료되었으나 Docker 로그에 다음과 같은 오류가 포함되어 있습니다.

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

**솔루션:**  
이 오류는 빠른 시작 템플리트를 배치할 때 CAUrl에 포트를 지정하지 않아 발생합니다.
CAUrl은 `https://<network>-org1-ca.stage.blockchain.ibm.com:31011`과 유사해야 합니다.
CAUrl에 올바른 값을 지정하도록 주의하면서 빠른 시작 템플리트를 다시 배치하십시오.

### **문제점:** 체인코드 인스턴스화가 실패하고 오류가 발생함
{: #remote-peer-aws-operate-problem-2}

체인코드 인스턴스화가 실패했으며 오류 메시지는 다음과 같습니다.
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**솔루션:**   
관리자 인증서가 네트워크 모니터에 업로드된 후 해당 인증서가 채널에서 동기화되는지 확인하십시오. 자세한 정보는 이 [지시사항](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-manage-certs)의 5단계를 참조하십시오. 피어가 채널에 가입하기 전에 채널 인증서를 동기화하는 것이 중요하다는 점에 유의하십시오.

---

copyright:
  years: 2017, 2019
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
# 스타터 플랜 또는 엔터프라이즈 플랜으로 {{site.data.keyword.cloud_notm}} Private에서 피어 운영
{: #ibp-peer-operate}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.cloud_notm}} Private 피어에 {{site.data.keyword.blockchainfull}} Platform을 설정한 후 여러 작업 단계를 완료해야 피어에서 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 트랜잭션을 제출할 수 있습니다. 이 단계에는 채널에 조직을 추가하고 피어가 채널에 가입하며 피어에 체인코드를 설치하고 채널에서 체인코드를 인스턴스화하며 애플리케이션을 피어에 연결하는 단계가 포함됩니다.
{:shortdesc}

피어를 작동시키려면 {{site.data.keyword.cloud_notm}} Private 클러스터에서 몇 가지 전제조건 단계를 완료해야 합니다.

**전제조건:**
- [CLI 구성](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)
- [피어 엔드포인트 정보 검색](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)
- [피어 TLS 인증서 다운로드](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)

다음 방법 중 하나를 사용하여 피어를 운영할 수 있습니다.

**오퍼레이션 경로:**
- [Fabric SDK 사용](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-operate-with-sdk)
- [명령행 사용](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-cli-operate)

지시사항에서는 SDK 오퍼레이션에 익숙하다고 가정하지만 Fabric SDK 경로를 사용하는 것이 좋습니다. 명령행을 사용하려는 경우 Fabric 피어 클라이언트를 사용할 수 있습니다.

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

**참고**: {{site.data.keyword.blockchainfull_notm}} Platform 피어에서는 {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어의 전체 기능 또는 지원에 액세스할 권한이 없습니다. 따라서 네트워크 모니터를 사용하여 {{site.data.keyword.cloud_notm}} Private의 피어를 작동시킬 수 없습니다. 피어 실행을 시작하기 전에 [고려사항 및 제한사항](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)을 검토하십시오.

## 전제조건
{: #ibp-peer-operate-prerequisites}

SDK를 사용할지 아니면 명령행을 사용할 계획인지에 상관없이 피어 오퍼레이션 과정에서 이 섹션의 단계를 완료해야 합니다. 시작하기 전에 이 단계를 모두 완료할 수 있습니다.

### CLI 구성
{: #ibp-peer-operate-kubectl-configure}

{{site.data.keyword.cloud_notm}} Private에서 실행되는 피어 컨테이너에 연결하려면 **kubectl** 명령행 도구를 사용해야 합니다.

1. {{site.data.keyword.cloud_notm}} Private 클러스터 UI에 로그인하십시오. **명령행 도구** 탭으로 이동하여 **Cloud Private CLI**를 클릭하십시오. 다운로드할 수 있는 다음 도구가 표시됩니다.

   * IBM Cloud Private CLI 및 플러그인 설치
   * Kubectl CLI 설치
   * Helm 설치
   * Istio CLI 설치

  피어를 운영하려면 먼저 **세 가지** 도구를 사용해야 하며 Helm은 선택사항입니다. 각각을 클릭하고 사용 중인 머신 유형에 맞는 `curl` 명령을 실행하십시오. 그런 다음 각 도구마다 `chmod` 및 `sudo mv` 명령을 실행하십시오. `chmod` 명령으로 해당 CLI의 권한을 변경하여 실행할 수 있도록 하고 `sudo mv` 명령으로 파일을 이동하고 이름을 변경합니다.

  첫 번째 도구인 **cloudctl**의 명령은 다음 예제와 같을 수 있습니다.

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  두 번째 도구인 **kubectl**의 명령은 다음 예제와 같을 수 있습니다.

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. **kubectl**을 구성한 후 다음 명령을 실행하십시오.

  ```
  kubectl get pods
  ```
  {:codeblock}

  성공하는 경우 명령은 다음 예제와 유사한 응답을 리턴합니다. 여기서, `peer-6cf85f6687-hcxpl`은 피어 컨테이너의 이름입니다.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  이제 피어 엔드포인트 정보를 얻기 위해 **kubectl** 도구를 사용할 준비가 되었습니다.

3. 선택적으로 **Helm**을 사용하려는 경우 몇 가지 추가 단계를 완료해야 합니다. Helm 설치는 선택사항이며 이 지시사항에서 이를 사용할 필요는 없습니다. 그러나 Helm 릴리스를 관리하고 고유한 아카이브를 작성하여 {{site.data.keyword.cloud_notm}} Private에 배치하는 것이 유용할 수 있습니다.

  1. "Helm 설치"를 클릭하고 {{site.data.keyword.cloud_notm}} Private UI에서 `curl` 명령을 실행하십시오.
  2. 다음 명령을 실행하여 `tar` 파일을 압축 해제하십시오.

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. `/helm`을 `darwin-amd64`에 추가하여 `sudo mv` 명령을 실행하십시오. Helm에 대해 `chmod` 명령을 실행할 필요는 없습니다. 예를 들면 다음과 같습니다.

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  `helm help` 명령을 실행하여 Helm이 정상적으로 설치되었는지 확인할 수 있습니다.

### 피어 엔드포인트 정보 검색
{: #ibp-peer-operate-endpoint}

채널에 가입하거나 스마트 계약을 설치하려면 SDK 또는 Fabric CA 클라이언트에서 피어 엔드포인트를 대상으로 설정해야 합니다. {{site.data.keyword.cloud_notm}} Private 콘솔 UI를 사용하여 피어의 엔드포인트를 찾을 수 있습니다. 다음 단계를 완료하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인한 후 왼쪽 상단 구석에 있는 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스의 이름을 찾아서 Helm 릴리스 세부사항 패널을 여십시오.
4. 패널의 맨 아래에 있는 **참고** 섹션으로 스크롤하십시오. **참고** 섹션에서 피어 배치를 운영하는 데 도움이 되는 명령 세트를 볼 수 있습니다.
5. 아직 수행하지 않은 경우 [kubectl CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)를 구성하려면 다음 단계를 따르십시오. kubectl를 사용하여 피어 컨테이너와 상호작용합니다.
6. CLI에서 **1. 이 명령을 실행하여 애플리케이션 URL 가져오기:** 다음에 표시되는 참고 정보의 첫 번째 명령을 실행하십시오. 이 명령으로 피어 URL 및 포트가 출력되고 다음 예제와 유사합니다. 이후 명령을 위해 이 URL을 저장하십시오.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**참고:** 방화벽 뒤에 피어를 배치하는 경우 플랫폼의 네트워크에서 피어로 TIS 핸드쉐이크를 완료할 수 있으려면 패스스루를 열어야 합니다. 피어의 포트 7051에 바인드된 노드 포트에 대한 패스스루만 사용으로 설정해야 합니다. 자세한 정보는 [피어 엔드포인트 정보 찾기](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)를 참조하십시오.

### 피어 TLS 인증서 다운로드
{: #ibp-peer-operate-tls-cert}

피어 TLS 인증서를 다운로드하고 이를 명령에 전달하여 원격 클라이언트에서 피어와 통신할 수 있습니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인한 후 왼쪽 상단 구석에 있는 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스의 이름을 찾아서 Helm 릴리스 세부사항 패널을 여십시오.
4. 패널의 맨 아래에 있는 **참고** 섹션으로 스크롤하십시오. **참고** 섹션에서 피어 배치를 운영하는 데 도움이 되는 명령 세트를 볼 수 있습니다.
5. 아직 수행하지 않은 경우 [kubectl CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)를 구성하려면 다음 단계를 따르십시오. kubectl를 사용하여 피어 컨테이너와 상호작용합니다.
6. CLI에서 **3. 피어 TLS 루트 인증서 파일 가져오기** 다음에 표시되는 참고 정보의 첫 번째 명령을 실행하십시오. 이 명령으로 로컬 머신에서 `cacert.pem` 파일로 TLS 인증서가 저장됩니다.
7. 이후 명령에서 인증서를 참조할 수 있는 위치로 인증서를 이동하고 `peertls.pem`으로 이름을 변경하십시오.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Fabric SDK를 사용하여 피어 운영
{: #ibp-peer-operate-operate-with-sdk}

Hyperledger Fabric SDK에서는 애플리케이션에서 블록체인 네트워크와 상호작용하고 운영하는 데 사용할 수 있는 강력한 API 세트를 제공합니다. [Hyperledger Fabric SDK 커뮤니티 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 커뮤니티 문서")에서 Fabric SDK 내에서 지원되는 최신 언어 목록과 사용 가능한 전체 API 목록을 찾을 수 있습니다. Fabric SDK를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform의 채널에 피어를 가입시키고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화할 수 있습니다.

다음 지시사항에서는 [Fabric Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Fabric Node SDK")를 사용하여 피어를 운영하고 이미 SDK에 익숙하다고 가정합니다. [애플리케이션 개발 튜토리얼](/docs/services/blockchain/v10_application.html#dev-app)은 시작하기 전에 Node SDK를 사용하는 방법을 알아보고 체인코드를 호출하여 조회할 준비가 되었을 때 피어로 애플리케이션을 개발하기 위한 가이드로 사용할 수 있습니다.

### Node SDK 설치
{: #ibp-peer-operate-install-sdk}

NPM을 사용하여 [Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Node SDK")를 설치할 수 있습니다.

```
npm install fabric-client@1.2
```
{:codeblock}

Node SDK 버전 1.2를 사용하는 것이 좋습니다.

### 피어와 작업하기 위해 SDK 준비
{: #ibp-peer-operate-prepare-node-sdk}

피어는 내부에 피어 관리자의 signCert와 함께 배치됩니다. 이를 통해 피어 관리자의 인증서와 MSP 폴더를 사용하여 피어를 운영할 수 있습니다.

[피어 관리자를 등록](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin)할 때 작성한 인증서를 찾으십시오. 예제 명령을 사용한 경우 `$HOME/fabric-ca-client/peer-admin`에서 피어 관리자 MSL 폴더를 찾을 수 있습니다.
  - MSP 폴더에서 signCert(공용 키)과 개인 키를 사용하여 SDK로 피어 관리자 컨텍스트를 빌드할 수 있습니다. 다음 위치에서 해당 키를 찾을 수 있습니다.
    - `$HOME/fabric-ca-client/peer-admin/msp/signcerts`의 **signcerts** 폴더에서 signCert를 찾을 수 있습니다.
    - 개인 키는 **키 저장소:** 폴더(`$HOME/fabric-ca-client/peer-admin/msp/keystore`)에 있습니다.
    공용 및 개인 키만을 사용하여 사용자 컨텍스트를 작성하고 SDK를 작동하는 방법의 예를 [애플리케이션 개발 튜토리얼의 이 섹션](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel)에서 찾을 수 있습니다.

또한 SDK를 사용하여 스타터 플랜 또는 엔터프라이즈 플랜의 CA 엔드포인트 정보와 [피어 관리자 사용자 이름 및 비밀번호](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin)를 사용하여 피어 관리자 signCert 및 개인 키를 생성할 수 있습니다.

### 스타터 플랜 또는 엔터프라이즈 플랜에 피어 관리자 signCert 업로드
{: #ibp-peer-operate-upload-sdk}

애플리케이션에서 네트워크를 운영할 권한이 있도록 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크에 피어 관리자 signCert를 업로드해야 합니다.

- SDK에서 암호화 자료를 생성한 디렉토리의 피어 관리자의 이름을 딴 파일에서 signCert를 찾을 수 있습니다. `-----BEGIN CERTIFICATE-----`로 시작하고 `-----END CERTIFICATE-----`로 끝나는 `certificate` 필드 다음에 있는 물음표 내부에 인증서를 복사하십시오. `echo -e "<CERT>" > admin.pem` 명령을 실행하여 인증서를 PEM 형식으로 변환하는 데 CLI를 사용할 수 있습니다. 그런 다음 네트워크 모니터를 사용하여 인증서 컨텐츠를 블록체인 네트워크에 붙여넣을 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크에 로그인하십시오. 네트워크 모니터의 "멤버" 화면에서 **인증서** > **인증서 추가**를 클릭하십시오. 인증서의 이름을 지정하고 **인증서** 필드에 컨텐츠를 붙여넣으십시오. 피어를 다시 시작할지 물으면 **다시 시작**을 클릭하십시오. 조직이 채널에 가입하기 전에 이 조치를 수행해야 합니다.

### 피어의 TLS 인증서를 SDK에 전달
{: #ibp-peer-operate-download-tlscert}

SDK와의 통신을 인증하려면 피어의 TLS 인증서를 참조해야 합니다. [피어 컨테이너에서 다운로드한](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) TLS 인증서를 찾고 애플리케이션으로 참조될 수 있는 위치에 TLS 인증서를 저장하십시오. 예제 명령을 사용한 경우 `$HOME/fabric-ca-client/peer-tls/peertls.pem`에서 피어 TLS 인증서를 찾을 수 있습니다. 그런 다음 간단한 읽기 파일 명령을 사용하여 TLS 인증서를 애플리케이션에 가져올 수 있습니다.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### SDK에 피어 엔드포인트 정보 제공
{: #ibp-peer-operate-SDK-endpoints}

[피어의 엔드포인트 정보](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)를 찾아 새 피어 변수를 선언하거나 연결 프로파일을 업데이트하여 SDK에 제공하십시오. 다음 예에서는 패브릭 네트워크에서 피어를 엔드포인트로 정의하고 가져온 TLS 인증서를 전달합니다.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**참고:** 피어가 {{site.data.keyword.cloud_notm}} 외부에 있으므로 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 기타 조직은 연결 프로파일에서 피어의 엔드포인트 정보를 찾을 수 없습니다. 다른 조직에서 인증할 트랜잭션을 피어에 보내면 대역 내외 오퍼레이션에서 피어 url을 제공해야 합니다.

### SDK를 사용하여 채널에 가입
{: #ibp-peer-operate-peer-join-channel-sdk}

피어로 채널에 가입할 수 있으려면 그 전에 조직이 채널의 멤버가 되어야 합니다.

  - 피어의 새 채널을 시작할 수 있습니다. 채널 개시자인 경우 [채널 작성](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다.

  - 블록체인 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다.

    채널에 조직을 추가하고 나면 다른 구성원이 트랜잭션 중에 디지털 서명을 확인할 수 있도록 피어의 signCert를 채널에 추가해야 합니다. 피어에서 설치 중에 signCert를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다. `join channel` 명령을 실행하기 전에 채널 동기화가 완료되도록 몇 분 동안 기다려야 합니다.

    **참고:** {{site.data.keyword.cloud_notm}} Private에 추가하고 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 연결한 피어가 네트워크 모니터로 추가된 피어와 같은 조직의 일부이면 채널에 추가되는 새 블록, 인스턴스화되는 체인코드 및 네트워크 모니터의 "채널" 화면에서의 다른 채널 업데이트를 볼 수 있습니다. 네트워크 모니터는 피어에서 "채널" 화면에 대한 정보를 수집하고 {{site.data.keyword.cloud_notm}} 외부의 피어에 대한 가시성이 없기 때문입니다. 모든 피어가 개인용 데이터 기능을 사용하지 않는 경우 네트워크 모니터의 정보는 조직의 한 피어에 대해 다른 피어와 마찬가지로 동일하게 됩니다.

조직이 채널의 멤버가 되면 다음 지시사항을 따라 SDK를 사용하여 [피어를 채널에 가입](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk)시키십시오. 순서 지정 서비스의 URL과 채널 이름을 제공해야 합니다.

### SDK를 사용하여 피어에 체인코드 설치
{: #ibp-peer-operate-install-cc-sdk}

SDK를 사용하여 피어에 [체인코드를 설치](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk)하려면 다음 지시사항을 사용하십시오.

### SDK를 사용하여 채널에서 체인코드 인스턴스화
{: #ibp-peer-operate-instantiate-cc-sdk}

채널의 한 멤버만 체인코드를 인스턴스화하거나 업데이트하면 됩니다. 따라서 {{site.data.keyword.blockchainfull_notm}} Platform에 피어가 있는 채널의 네트워크 구성원이 네트워크 모니터를 사용하여 체인코드를 인스턴스화하고 인증 정책을 지정할 수 있습니다. 그러나 피어를 사용하여 채널에서 체인코드를 인스턴스화하려면 SDK를 사용하고 지시사항에 따라 [체인코드를 인스턴스화](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk)할 수 있습니다.

## CLI를 사용하여 피어 운영
{: #ibp-peer-operate-cli-operate}

Fabric `peer` client를 사용하여 명령행에서 피어를 운영할 수도 있습니다.

피어가 내부에서 피어 관리자의 signCert로 배치되며 해당 ID가 피어를 운영할 수 있습니다. 다음 지시사항은 채널에 피어가 가입하고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화하기 위해 [피어를 배치](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin) 할 때 생성된 피어 관리자 MSP 폴더를 사용합니다.

### Fabric 피어 클라이언트 다운로드
{: #ibp-peer-operate-download-fabric-client}

피어 클라이언트를 확보하는 가장 쉬운 방법은 Hyperledger에서 모든 Fabric 도구 바이너리를 다운로드하는 것입니다. 명령행을 사용하여 바이너리를 다운로드할 디렉토리로 이동하고 아래 명령을 실행하여 페치하십시오. [Curl ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl")을 설치하지 않은 경우 먼저 설치해야 합니다.

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

명령으로 `bin/` 디렉토리에서 바이너리가 설치됩니다. 도구에 사용자의 경로를 설정하십시오. 여기서, 위의 Fabric 도구가 다운로드됩니다.
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

예를 들어, 홈 디렉토리에 바이너리를 설치한 경우 다음과 같이 `PATH`를 설정할 수 있습니다..

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

바이너리를 다운로드하면 core.yaml, orderer.yaml 및 configtx.yaml 파일이 포함된 `config` 폴더가 작성됩니다. 이 `config` 디렉토리에 Fabric 구성 경로를 설정하십시오.

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

예를 들면 다음과 같습니다.
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}


### 로컬 시스템에서 인증서 관리
{: #manage-certs}

피어 관리자 MSP 폴더가 생성되는 디렉토리를 전환하십시오. 이 문서에 있는 예제 단계를 따른 경우 아래와 유사한 디렉토리에서 MSP 폴더를 찾을 수 있습니다.

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

피어를 운영하기 전에 로컬 머신에서 인증서에 대한 일부 관리를 수행해야 합니다. 예를 들면, 스타터 플랜 또는 엔터프라이즈 플랜에 피어 관리자 signCert를 업로드해야 하며 {{site.data.keyword.cloud_notm}}에서 피어와 네트워크의 TLS 인증서에 액세스할 수 있는지 확인해야 합니다. 사용할 인증서와 수행할 태스크에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform에서 인증서 관리](/docs/services/blockchain/certificates.html#managing-certificates)를 참조하십시오.

1. `admincerts`라는 새 폴더로 피어 관리자의 signCert를 이동하십시오.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. 원격 CLI나 애플리케이션에서 채널 최초 블록을 페칭하고 체인코드를 인스턴스화하는 등의 채널 오퍼레이션을 수행하려면 이 signCert를 {{site.data.keyword.cloud_notm}}의 네트워크로 업로드하십시오.
    1. 로컬 시스템에서 `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` 파일을 열어 클립보드에 복사하십시오.
    2. {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크의 네트워크 모니터를 입력하십시오.
    3. 왼쪽 네비게이터에서 **멤버**를 클릭하고 **인증서** 탭을 클릭하십시오.
    4. **인증서 추가** 단추를 클릭하십시오.
    5. **인증서 추가** 창에서 peer-admin과 같은 인증서의 이름을 지정하고 클립보드에 방금 복사한 인증서를 붙여넣은 다음 **제출**을 클릭하십시오.
    6. 피어를 다시 시작하려는지 묻습니다. **다시 시작**을 클릭하십시오.
    7. 왼쪽 네비게이터에서 **채널**을 클릭하고 해당 피어가 가입할 채널을 찾으십시오.
    8. **조치** 헤더 아래 세 개의 점을 클릭하고 **인증서 동기화**를 클릭하십시오. **인증서 동기화** 창에서 **제출**을 클릭하십시오.

    **참고**: 피어가 채널에 가입하거나 채널에서 체인코드를 인스턴스화하기 전에 채널 인증서를 동기화하는 것이 중요합니다. 채널 가입 또는 체인코드 인스턴스화 명령을 실행하기 전에 채널 동기화가 완료되도록 몇 분 동안 기다려야 합니다.

3. [피어 TLS 인증서를 다운로드](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)했고 명령행에서 참조할 수 있는지 확인하십시오. 예제 명령을 사용한 경우 `$HOME/fabric-ca-client/peer-tls/peertls.pem` 파일에서 이 TLS 인증서를 찾을 수 있습니다.

4. 또한 [피어 관리자를 등록](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin)할 때 스타터 플랜 또는 엔터프라이즈 플랜 CA와 통신하는 데 사용한 TLS 인증서를 참조해야 합니다. 이 문서의 예제 명령을 수행했다면 `$HOME/fabric-ca-client/tls-ibp/tls.pem` 파일에 TLS 인증서가 있습니다.

트리 명령을 실행하여 이 단계를 완료했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. 트리 명령은 다음 구조와 유사한 결과를 생성해야 합니다.
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CLI 환경 변수 설정
{: #ibp-peer-operate-environment-variables}

모든 인증서를 필요한 위치로 옮긴 후 사용할 명령에 일부 환경 변수를 설정해야 합니다. 그런 다음 Fabric 피어 클라이언트를 사용하여 피어를 운영할 준비가 되었습니다.

1. 네트워크 모니터의 **개요** 화면에서 사용 가능한 **연결 프로파일** 파일에서 스타터 플랜 또는 엔터프라이즈 플랜의 구성 정보를 검색하십시오. **연결 프로파일**을 클릭한 다음 **다운로드**를 클릭하십시오.

  - `orderers > url`에 있는 **순서 지정자**를 검색하여 순서 지정자 URL을 찾으십시오. 네트워크 이름으로 시작하는 URL을 기록해 두십시오. 이 URL은 다음 예와 비슷합니다.

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - **조직**을 검색하여 조직의 이름을 찾으십시오. 이 조직은 피어를 등록하는 데 사용하는 조직과 같아야 합니다. 연관된 `mspid`와 함께 조직의 이름을 찾을 수 있습니다. `mspid`의 값을 기록해 두십시오.

2. [피어의 엔드포인트 정보 찾기](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint). `PEERADDR` 환경 변수를 설정하려면 피어 엔드포인트를 사용해야 합니다. 시작 부분에 `http://`를 제외해야 합니다.

3. 다음 명령을 실행하여 환경 변수를 설정하십시오.

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  고유 정보로 필드를 바꾸십시오.
    - `<full_path_to_config_folder>`를 [피어 클라이언트를 다운로드](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-download-fabric-client)할 때 작성한 구성 폴더로 바꾸십시오.
    - `<CHANNEL_NAME>`을 피어가 가입하는 채널의 이름으로 바꾸십시오.
    - `<CC_NAME>`을 체인코드를 참조하는 이름으로 바꾸십시오.
    - `<PEERADDR>`을 현재 사용하고 있는 피어의 이전 단계에서의 피어 엔드포인트로 바꾸십시오.
    - `<ORDERER_URL>`을 연결 프로파일의 순서 지정자 호스트 이름과 포트로 바꾸십시오.
    - `<PATH_TO_ADMIN_MSP>`를 피어 관리자의 MSP 폴더에 대한 경로로 바꾸십시오.
    - `<PATH_TO_PEER_TLS_CERT>`를 다운로드한 피어 TLS 인증서에 대한 경로로 바꾸십시오.
    - `<MSPID_OF_PEER_BEING_USED>`를 최초 블록을 페치하거나 채널에 가입하거나 체인코드를 설치하기 위해 사용하는 피어의 조직 MSPID로 바꾸십시오.

  예를 들면 다음과 같습니다.

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  TLS를 사용으로 설정하려는 경우에는 다음 명령을 실행하여 수행할 수 있습니다.

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### CLI를 사용하여 피어를 채널에 가입
{: #ibp-peer-operate-cli-join-channel}

다음 중 하나의 방법으로 네트워크의 채널에 조직을 추가해야 피어를 채널에 가입시키는 CLI 명령을 실행할 수 있습니다.

  - 피어의 새 채널을 시작할 수 있습니다. 채널 개시자로서 [채널 작성](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) 중에 조직을 자동으로 포함시킬 수 있습니다.
  - 블록체인 네트워크의 다른 멤버도 [채널 업데이트](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel)를 사용하여 기존 채널에 조직을 추가할 수 있습니다.

    채널에 조직을 추가하고 나면 다른 구성원이 트랜잭션 중에 디지털 서명을 확인할 수 있도록 피어의 signCert를 채널에 추가해야 합니다. 피어에서 설치 중에 signCert를 업로드하므로 사용자는 채널에 인증서를 동기화하기만 하면 됩니다. 네트워크 모니터의 "화면" 채널에서 조직이 가입하는 채널을 찾고 **조치** 헤더의 드롭 다운 목록에서 **인증서 동기화**를 선택하십시오. 이 조치는 채널에 있는 피어 전체에서 인증서를 동기화합니다.

    **참고:** {{site.data.keyword.cloud_notm}} Private 피어에서 네트워크 모니터에 추가된 다른 피어와 같은 조직의 일부인 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 연결하는 경우, 네트워크 모니터의 "채널" 화면에서 채널에 추가된 새 블록, 인스턴스화된 체인코드 및 기타 채널 업데이트만 볼 수 있습니다. 네트워크 모니터는 피어에서 "채널" 화면에 대한 정보를 수집하고 {{site.data.keyword.cloud_notm}} 외부의 피어에 대한 가시성이 없기 때문입니다. 모든 피어가 개인용 데이터 기능을 사용하지 않는 경우 네트워크 모니터의 정보는 조직의 한 피어에 대해 다른 피어와 마찬가지로 동일합니다.

1. [CLI의 환경 변수](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-environment-variables)를 설정했는지 확인하십시오.

2. 채널의 최초 블록을 페치하여 피어에서 채널 원장을 빌드하십시오. `$HOME/fabric-ca-client/tls-ibp/tls.pem`은 스타터 플랜 또는 엔터프라이즈 플랜 TLS 인증서의 경로를 표시한다는 점을 유의하십시오. 사용자의 경로가 다른 경우 올바른 경로를 설정했는지 확인하십시오.

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
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

  다음 예와 비슷한 내용이 표시되면 최초 블록이 제대로 추가된 것입니다.

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. 채널의 최초 블록을 페치한 후 다음 명령을 사용하여 피어를 채널에 가입시킬 수 있습니다.

  ```
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  이 작업이 성공적으로 작동하면 다음 예와 유사한 정보가 표시되어야 합니다.

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### CLI를 사용하여 피어에 체인코드 설치
{: #ibp-peer-operate-toolcontainer-install-cc}

이제 피어에 체인코드를 설치하고 인스턴스화할 준비가 되었습니다. 이 지시사항에서 사용하기 위해 `fabric-samples` 저장소에서 `fabcar`를 설치합니다. 다음 명령을 사용하여 Github에서 `fabric-samples` 체인코드를 다운로드한 전후에 [GOPATH를 구성![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "GOPAT")했는지 확인하십시오.

```
cd $GOPATH/src
git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

다음 피어 CLI 명령을 실행하여 `fabcar` 체인코드를 피어에 설치하십시오.

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**참고:** 이 체인코드가 스타터 또는 엔터프라이즈 플랜에서 실행 중인 다른 피어에 이미 설치되어 인스턴스화된 경우 피어에 체인코드를 설치하기 전에 수행해야 하는 추가 단계가 있습니다. 이 체인코드 설치 방법과 연관된 오류를 방지하는 방법에 대한 자세한 정보는 [문제점 해결 주제](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-troubleshooting)의 내용을 참조하십시오.

### CLI를 사용하여 채널에서 체인코드 인스턴스화
{: #ibp-peer-operate-toolcontainer-instantiate-cc}

한 피어만 채널에서 체인코드를 인스턴스화하면 되므로 피어를 사용하여 체인코드를 인스턴스화하지 않아도 됩니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어에서 해당 작업을 수행할 수 있습니다. 그러나 피어가 채널에서 체인코드를 인스턴스화하게 하려면 다음 명령을 실행하여 이를 수행할 수 있습니다. 스타터 플랜 또는 엔터프라이즈 플랜에서 CA의 TLS 인증서 경로에 `CORE_PEER_TLS_ROOTCERT_FILE`의 값을 설정했는지 확인하십시오.

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

체인코드가 인스턴스화된 후 체인코드 조회를 사용하고 명령을 호출하여 채널 원장의 데이터를 읽고 쓸 수 있습니다. 자세한 정보는 Hyperledger Fabric 문서에서 [피어 체인코드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) 명령을 참조하십시오. 프록시 IP와 외부 순서 지정자 포트를 사용하여 순서 지정자 엔드포인트를 호출 명령에 전달해야 합니다. 피어 엔드포인트를 조회 명령에 전달하기만 하면 됩니다.

## 체인코드 업데이트
{: #ibp-peer-operate-update-chaincode}

시간 경과에 따라 피어에서 실행 중인 체인코드를 수정해야 할 가능성이 있습니다. 체인코드는 피어에 설치되어 채널에서 인스턴스화되므로 채널의 모든 피어에서 체인코드를 업데이트해야 합니다.

체인코드를 업데이트하려면 다음 단계를 완료하십시오.

1. 각 피어에서 체인코드를 업데이트하려면 클라이언트 애플리케이션이나 CLI 명령을 사용하여 피어에 체인코드를 설치하는 데 사용한 프로세스를 다시 실행하기만 하면 됩니다. 원래 사용된 이름과 동일한 체인코드 이름을 지정하십시오. 그러나 이번에는 체인코드 `Version`을 늘르십시오. 모든 피어는 같은 체인코드 이름과 버전을 사용해야 합니다.

2. 채널의 모든 피어에 새 체인코드를 설치한 다음 네트워크 모니터 또는
[피어 체인코드 업그레이드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 명령을 사용하여 새 체인코드를 사용하도록 채널을 업데이트하십시오.

네트워크 모니터의 "코드 설치" 패널을 사용하여 채널에서 체인코드를 업데이트하는 데 관한 자세한 정보는 해당 [지시사항](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) 중 2단계를 참조하십시오.

## 피어 로그 보기
{: #ibp-peer-operate-view-logs}

컴포넌트 로그는 [`kubectl` CLI 명령](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)을 사용하거나 {{site.data.keyword.cloud_notm}} Private 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다.

- `kubectl logs` 명령을 실행하여 팟(Pod) 내부의 컨테이너 로그를 보십시오. 팟(Pod) 이름이 확실하지 않은 경우 다음 명령을 실행하여 팟(Pod)의 목록을 보십시오.

  ```
  kubectl get pods
  ```
  {:codeblock}

  그런 다음 `<pod_name>`을 위에 있는 명령 출력의 팟(Pod)에 대한 이름으로 대체하여 팟(Pod) 내부에 상주하는 피어 컨테이너의 로그를 검색하려면 다음 명령을 실행하십시오.

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  `kubectl logs` 명령에 대한 자세한 정보는 [Kubernetes 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)를 참조하십시오.

- 또는 Kibana에서 로그를 여는 [{{site.data.keyword.cloud_notm}}Private 클러스터 관리 콘솔](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)을 사용하여 로그에 액세스할 수 있습니다.

   **참고:** Kibana에서 로그인이 표시되면 `No results found`의 응답을 수신할 수 있습니다. 이 상태는 {{site.data.keyword.blockchainfull_notm}} Private이 호스트 이름으로 작업자 노드 IP 주소를 사용하는 경우 발생할 수 있습니다. 이 문제점을 해결하려면 패널 상단에서 `node.hostname.keyword`로 시작하는 필터를 제거하십시오. 그런 다음 로그가 표시됩니다.

## 문제점 해결
{: #ibp-peer-operate-troubleshooting}

### **문제점:** 호출 명령이 피어에서 실패했고 `chaincode fingerprint mismatch` 오류가 발생함
{: #ibp-peer-operate
-install-error}

{{site.data.keyword.cloud_notm}} Private에서 실행 중인 피어에 `peer chaincode invoke` 요청을 실행하는 경우 `chaincode fingerprint mismatch` 오류를 수신할 수 있습니다.

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

이 오류는 다음 영역 중 하나에서 체인코드를 실행하는 피어 간에 불일치가 있는 경우 발생할 수 있습니다.

- 체인코드 이름
- 체인코드 버전
- 체인코드 경로
- 체인코드 바이너리

네트워크 모니터 UI가 스타터 또는 엔터프라이즈 계획에서 실행 중인 피어에 체인코드를 설치하고 인스턴스화하는 데 사용된 후 {{site.data.keyword.cloud_notm}} Private에서 실행 중인 피어에 체인코드를 설치하면 이 오류가 발생할 수 있습니다. 피어의 결과 체인코드 경로가 다르기 때문에 `호출` 요청에서 오류가 발생합니다.

**솔루션:**
{{site.data.keyword.cloud_notm}}(예: 스타터 또는 엔터프라이즈 플랜) 및 {{site.data.keyword.cloud_notm}} Private 둘 다에서 피어에 체인코드를 실행하려면 네트워크 모니터 UI를 사용하여 체인코드를 설치하지 마십시오. 대신 [`peer chaincode package`![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) 명령으로 체인코드를 패키지한 다음 [`peer chaincode install`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-toolcontainer-install-cc) 명령을 실행하여 모든 피어에 패키지를 설치하십시오.

체인코드가 채널에 이미 설치되고 인스턴스화된 경우 {{site.data.keyword.cloud_notm}} Private 피어에 체인코드를 설치하기 전에 해당 문제를 방지하려면 다음 단계를 완료해야 합니다.

1. [`peer chaincode package`![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) 명령을 사용하여 체인코드를 패키지하십시오.
2. `peer chaincode install` 명령을 실행하여 {{site.data.keyword.cloud_notm}} Private에서 실행되는 피어에 체인코드 패키지를 설치하십시오.
3. 플랫폼 특정 바이너리가 있는 경우 [`peer chaincode upgrade`![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) 명령을 실행하여 체인코드 패키지를 사용하는 스타터 또는 엔터프라이즈 플랜 피어에서 실행 중인 체인코드를 업그레이드할 수 있습니다.
4. 네트워크 모니터 UI 또는 CLI를 사용하여 채널에 새로 설치된 체인코드를 인스턴스화하십시오.

체인코드를 업그레이드할 수 있는 프로세스는 Hyperledger Fabric 문서의 [`Chaincode for Operators`![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html)에서 찾을 수 있습니다.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 다중 클라우드 네트워크에서 {{site.data.keyword.cloud_notm}} Private 운영
{: #peer-operate_icp}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

ICP({{site.data.keyword.cloud_notm}} Private)에 {{site.data.keyword.blockchainfull}} Platform을 설정한 다음 여러 오퍼레이션 단계를 완료해야 피어가 블록체인 네트워크의 원장을 조회하고 호출하기 위한 트랜잭션을 발행할 수 있습니다. 이 단계에는 채널에 조직을 추가하고 피어에 채널이 가입하며 피어에 체인코드를 설치하고 채널에서 체인코드를 인스턴스화하며 애플리케이션을 피어에 연결하는 단계가 포함됩니다. 네트워크를 스터터 플랜 또는 엔터프라이즈 플랜 네트워크에 연결하려면 [스타터 플랜 또는 엔터프라이즈 플랜 네트워크로 {{site.data.keyword.cloud_notm}} Private에서 피어 운영](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-operate_icp)을 참조하십시오.
{:shortdesc}

피어를 운영하려면 ICP 클러스터에서 몇 가지 전제조건 단계를 완료해야 합니다.

**전제조건:**
- [CLI 구성](#peer-kubectl-configure)
- [피어 엔드포인트 정보 검색](#peer-endpoint)
- [피어 TLS 인증서 다운로드](#peer-tls)

다음 방법 중 하나를 사용하여 피어를 운영할 수 있습니다.

**오퍼레이션 경로:**
- [Fabric SDK 사용](#peer-operate-with-sdk)
- [명령행 사용](#peer-cli-operate)

지시사항에서는 SDK 오퍼레이션에 익숙하다고 가정하지만 Fabric SDK 경로를 사용하는 것이 좋습니다. 명령행을 사용할 경우 Fabric 피어 바이너리를 사용할 수 있습니다. 

조직이 아직 컨소시엄 또는 채널의 멤버가 아닌 경우 이 단계를 사용하여 [채널을 작성](#create-channel)할 수 있습니다. 지시사항은 [조직 정의 준비](#organization-definition) 방법을 제공합니다. 이 정의는 순서 지정자 시스템 채널에 추가하여 컨소시엄의 멤버가 될 수 있도록 하는 데 사용됩니다. 그런 다음 [채널 트랜잭션을 작성](#peer-icp-channeltx)하여 새 채널을 형성할 수 있습니다. 
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](/docs/services/blockchain/howto/peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](/docs/services/blockchain/v10_application.html#ha-app).
-->

## 선행 조건

SDK를 사용할지 아니면 명령행을 사용할 계획인지에 상관없이 피어 오퍼레이션 과정에서 이 섹션의 단계를 완료해야 합니다. 시작하기 전에 이 단계를 모두 완료할 수 있습니다. 

### CLI 구성
{: #peer-kubectl-configure}

**kubectl** 명령행 도구를 사용하여 ICP에서 실행되는 피어 컨테이너에 연결해야 합니다. 

1. ICP 클러스터 UI에 로그인하십시오. **명령행 도구** 탭으로 이동하여 **Cloud Private CLI**를 클릭하십시오. 다운로드할 수 있는 다음 도구가 표시됩니다. 

   * IBM Cloud Private CLI 및 플러그인 설치
   * Kubectl CLI 설치
   * Helm 설치
   * Istio CLI 설치

  피어를 운영하려면 먼저 **세 가지** 도구를 사용해야 하며 Helm은 선택사항입니다. 각각을 클릭하고 사용 중인 머신 유형에 맞는 `curl` 명령을 실행하십시오. 그런 다음 각 도구마다 `chmod` 및 `sudo mv` 명령을 실행하십시오. `chmod` 명령으로 문제가 되는 CLI의 권한을 변경하여 실행할 수 있도록 하고 `sudo mv` 명령으로 파일이 이동되고 이름이 변경됩니다. 

  첫 번째 도구인 **cloudctl**의 명령은 다음 예제와 비슷하게 보일 수 있습니다. 

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  두 번째 도구인 **kubectl**의 명령은 다음 예제와 비슷하게 보일 수 있습니다. 

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

3. 선택적으로 **Helm**을 사용할 경우 몇 가지 추가 단계를 완료해야 합니다. Helm 설치는 선택사항이며 이 지시사항에서 이를 사용할 필요가 없습니다. 그러나 Helm 릴리스를 관리하고 고유한 아카이브를 작성하여 ICP에 배치하는 것이 유용할 수 있습니다. 

  1. "Helm 설치"를 클릭하고 ICP UI에서 `curl` 명령을 실행하십시오. 
  2. 다음 명령을 실행하여 `tar` 파일을 압축 해제하십시오. 

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. `/helm`을 `darwin-amd64`에 추가하여 `sudo mv` 명령을 실행하십시오. Helm용 `chmod` 명령을 실행할 필요가 없습니다. 예를 들어, 다음과 같습니다.

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  `helm help` 명령을 실행하여 Helm이 설치되었는지 확인할 수 있습니다. 

### 피어 엔드포인트 정보 검색
{: #peer-endpoint}

채널에 가입하거나 스마트 계약을 설치하려면 SDK 또는 Fabric CA 클라이언트에서 피어 엔드포인트를 대상으로 설정해야 합니다. ICP 콘솔 UI를 사용하여 피어의 엔드포인트를 찾을 수 있습니다. 다음 단계를 완료하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다. 

1. ICP 콘솔에 로그인하고 왼쪽 상단에서 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오. 
3. Helm 릴리스의 이름을 찾고 Helm 릴리스 세부사항 패널을 여십시오.
4. 아래로 스크롤하여 패널의 맨 아래에 있는 **참고** 섹션으로 이동하십시오. **참고** 섹션에서 피어 배치를 운영하는 데 도움이 되는 명령 세트를 볼 수 있습니다. 
5. 아직 수행하지 않은 경우 [kubeclt CLI](#peer-kubectl-configure)를 구성하려면 다음 단계를 따르십시오. kubectl를 사용하여 피어 컨테이너와 상호작용합니다. 
6. CLI에서 **1. 이 명령을 실행하여 애플리케이션 URL 가져오기:** 다음에 표시되는 참고 정보의 첫 번째 명령을 실행하십시오. 이 명령으로 피어 URL 및 포트가 출력되고 다음 예제와 유사합니다. 이후 명령을 위해 이 URL을 저장하십시오. 

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**참고:** 방화벽 뒤에 피어를 배치하는 경우 플랫폼의 네트워크에서 피어로 TIS 핸드쉐이크를 완료할 수 있으려면 패스스루를 열어야 합니다. 피어의 포트 7051에 바인드된 노드 포트에 대한 패스스루만 사용으로 설정해야 합니다. 자세한 정보는 [피어 엔드포인트 정보 찾기](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-endpoint)를 참조하십시오.

### 피어 TLS 인증서 다운로드
{: #peer-tls}

피어 TLS 인증서를 다운로드하고 이를 명령에 전달하여 원격 클라이언트에서 피어와 통신할 수 있습니다. 

1. ICP 콘솔에 로그인하고 왼쪽 상단에서 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오. 
3. Helm 릴리스의 이름을 찾고 릴리스 Helm 세부사항 패널을 여십시오.
4. 아래로 스크롤하여 패널의 맨 아래에 있는 **참고** 섹션으로 이동하십시오. **참고** 섹션에서 피어 배치를 운영하는 데 도움이 되는 명령 세트를 볼 수 있습니다. 
5. 아직 수행하지 않은 경우 [kubeclt CLI](#peer-kubectl-configure)를 구성하려면 다음 단계를 따르십시오. kubectl를 사용하여 피어 컨테이너와 상호작용합니다. 
6. CLI에서 **3. 피어 TLS 루트 인증서 파일 가져오기** 다음에 표시되는 참고 정보의 첫 번째 명령을 실행하십시오. 이 명령으로 로컬 머신에서 `cacert.pem` 파일로 TLS 인증서가 저장됩니다. 
7. 이후 명령에서 인증서를 참조할 수 있는 위치로 인증서를 이동하고 `peertls.pem`으로 이름을 변경하십시오. 

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Fabric SDK를 사용하여 피어 운영
{: #peer-operate-with-sdk}

Hyperledger Fabric SDK에서는 애플리케이션에서 블록체인 네트워크와 상호작용하고 운영하는 데 사용할 수 있는 강력한 API 세트를 제공합니다. [Hyperledger Fabric SDK 커뮤니티 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 커뮤니티 문서")에서 Fabric SDK 내에서 지원되는 최신 언어 목록과 사용 가능한 전체 API 목록을 찾을 수 있습니다. Fabric SDK를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform의 채널에 피어가 가입하고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화할 수 있습니다.

다음 지시사항에서는 [Fabric Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Fabric Node SDK")를 사용하여 피어를 운영하고 이미 SDK에 익숙하다고 가정합니다. [애플리케이션 개발 튜토리얼](/docs/services/blockchain/v10_application.html)은 시작하기 전에 Node SDK를 사용하는 방법을 알아보고 체인코드를 호출하여 조회할 준비가 되었을 때 피어로 애플리케이션을 개발하기 위한 가이드로 사용할 수 있습니다.

### Node SDK 설치

NPM을 사용하여 [Node SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Node SDK")를 설치할 수 있습니다.

```
npm install fabric-client@1.2
```
{:codeblock}

Node SDK 버전 1.2를 사용하는 것이 좋습니다.

### 피어와 작업하기 위해 SDK 준비
{: #peer-node-sdk}

피어가 내부에서 피어 관리자의 signCert로 배치됩니다. 이를 통해 사용자는 피어가 운영되도록 피어 관리자의 인증서 및 MSP 폴더를 사용할 수 있습니다. 

[피어 관리자를 등록](/docs/services/blockchain/howto/CA_operate.html#enroll-admin)할 때 작성한 인증서를 찾으십시오. 예제 명령을 사용한 경우 `$HOME/fabric-ca-client/peer-admin`에서 피어 관리자 MSL 폴더를 찾을 수 있습니다.
  - MSP 폴더의 signCert(공개 키) 및 개인 키를 사용하여 SDK로 피어 관리자 컨텍스트를 빌드할 수 있습니다. 다음 위치에서 해당 키를 찾을 수 있습니다. 
    - `$HOME/fabric-ca-client/peer-admin/msp/signcerts`의 **signcerts** 폴더에서 signCert를 찾을 수 있습니다.
    - `$HOME/fabric-ca-client/peer-admin/msp/keystore`의 **keystore:** 폴더에서 개인 키를 찾을 수 있습니다.
    공개 키 및 개인 키만 사용하여 사용 컨텍스트를 형성하고 SDK를 운영하는 방법의 예는 [네트워크 모니터를 사용하여 인증서 생성](/docs/services/blockchain/v10_application.html#enroll-panel)을 참조하십시오.

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](/docs/services/blockchain/howto/CA_operate.html#register-admin). -->

### 피어의 TLS 인증서를 SDK에 전달
{: #icp-peer-download-tlscert}

SDK에서 통신을 인증하려면 피어의 TLS 인증서를 참조해야 합니다. [피어 컨테이너에서 다운로드한](#peer-tls) TLS 인증서를 찾고 애플리케이션으로 참조될 수 있는 위치에 TLS 인증서를 저장하십시오. 예제 명령을 사용한 경우 `$HOME/fabric-ca-client/peer-tls/peertls.pem`에서 피어 TLS 인증서를 찾을 수 있습니다. 그런 다음 간단한 읽기 파일 명령을 사용하여 TLS 인증서를 애플리케이션에 가져올 수 있습니다.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### SDK에 피어 엔드포인트 정보 제공
{: #peer-SDK-endpoints}

[피어의 엔드포인트 정보](#peer-endpoint)를 찾아 새 피어 변수를 선언하거나 SDK에 제공하십시오. 다음 예에서는 패브릭 네트워크에서 피어를 엔드포인트로 정의하고 가져온 TLS 인증서를 전달합니다.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

트랜잭션을 보증해야 하는 다른 조직에 속하는 다중 피어가 포함된 대형 네트워크가 있는 경우 [공통 연결 프로파일을 빌드![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-network-config.html "공통 연결 프로파일")하기 위해 컨소시엄과 작업하려고 할 수 있습니다.  

### 순서 지정자의 TLS 인증서를 SDK에 전달
{: #icp-orderer-download-tlscert}

채널에 가입하고 트랜잭션을 제출하려면 컨소시엄의 순서 지정자에 대한 TLS 인증서도 필요합니다. 순서 지정자의 관리자인 경우 지시사항에 따라 [순서 지정자 TLS 인증서를 다운로드](/docs/services/blockchain/howto/orderer_operate.html#orderer-tls)하십시오. 예제 명령을 사용한 경우 `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`에서 피어 TLS 인증서를 찾을 수 있습니다. 다른 조직에서 순서 지정자를 관리하는 경우 사용자에게 대역 내외 오퍼레이션에서 TLS 인증서를 제공해야 합니다. 그런 다음 TLS 인증서를 애플리케이션에 가져올 수 있습니다. 

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### SDK에 순서 지정자 정보 제공
{: #orderer-SDK-endpoints}

SDK를 사용하려면 컨소시엄에서 순서 지정자의 엔드포인트 정보도 필요합니다. 순서 지정자의 관리자인 경우 이 지시사항을 사용하여 [순서 지정자 엔드포인트 정보를 검색](/docs/services/blockchain/howto/orderer_operate.html#orderer-endpoint)할 수 있습니다. 다른 조직에서 순서 지정자를 관리하는 경우 사용자에게 대역 내외 오퍼레이션에서 순서 지정자 URL을 제공해야 합니다. 다음 예제는 엔드포인트로서 순서 지정자를 정의하고 이를 순서 지정자 TLS 인증서에 전달합니다. 

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### SDK를 사용하여 채널에 가입
{: #peer-join-channel-sdk}

조직은 피어로 채널에 가입하기 전에 채널의 멤버가 되어야 합니다. 채널의 멤버가 아니면 지시사항에 따라 [새 채널을 작성](#create-channel)할 수 있습니다. 

채널의 멤버가 된 후 지시사항에 따라 SDK를 사용하여 [피어를 채널에 가입](/docs/services/blockchain/v10_application.html#join-channel-sdk)하십시오. 

### SDK를 사용하여 피어에 체인코드 설치
{: #peer-install-cc-sdk}

SDK를 사용하여 피어에 [체인코드를 설치](/docs/services/blockchain/v10_application.html#install-cc-sdk)하려면 다음 지시사항을 사용하십시오.

### SDK를 사용하여 채널에서 체인코드 인스턴스화
{: #peer-instantiate-cc-sdk}

채널의 한 멤버만 체인코드를 인스턴스화하거나 업데이트하면 됩니다. 따라서 피어에 있는 체인코드를 설치한 채널의 모든 멤버가 체인코드를 인스턴스화하고 인증 정책을 지정할 수 있습니다. 그러나 피어를 사용하여 채널에서 체인코드를 인스턴스화하려면 SDK를 사용하고 지시사항에 따라 [체인코드를 인스턴스화](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk)할 수 있습니다.

## CLI를 사용하여 피어 운영
{: #peer-cli-operate}

Fabric Peer 바이너리를 사용하여 명령행에서 원격 피어를 운영할 수도 있습니다. 

피어가 내부에서 피어 관리자의 signCert로 배치되며 해당 ID가 피어를 운영할 수 있습니다. 다음 지시사항은 채널에 피어가 가입하고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화하기 위해 [피어를 배치](/docs/services/blockchain/howto/CA_operate.html#register-admin) 할 때 생성된 피어 관리자 MSP 폴더를 사용합니다. 

### Fabric 피어 클라이언트 다운로드
{: #peer-client}

원격 클라이언트에서 피어와 상호작용하려면 [Fabric 피어 클라이언트 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "Fabric 피어 명령")를 다운로드해야 합니다.

피어 클라이언트를 가져올 수 있는 가장 쉬운 방법은 Hyperledger Project에서 Fabric 도구 바이너리를 다운로드하는 것입니다. 명령행을 사용하여 바이너리를 다운로드할 디렉토리를 작성하고 다음 명령을 실행하여 바이너리를 패치하십시오. [Curl ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl")을 먼저 설치해야 합니다. 

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

명령으로 `bin/` 디렉토리에서 바이너리가 설치됩니다. 도구에 사용자의 경로를 설정하십시오. 여기서, 위의 Fabric 도구가 다운로드됩니다. 
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

예를 들어, 다음으로 `PATH`를 설정하는 홈 디렉토리에 바이너리를 설치한 경우입니다. 

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

바이너리를 다운로드하면 core.yaml, orderer.yaml 및 configtx.yaml 파일을 포함한 구성 폴더가 작성됩니다. 이 구성 디렉토리에 Fabric 구성 경로를 설정하십시오.

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

예를 들어, 다음과 같습니다.
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

피어를 운영하기 전에 로컬 머신에서 인증서에 대한 일부 관리를 수행해야 합니다. 피어에서 TLS 인증서에 액세스할 수 있는지도 확인해야 합니다. 사용할 인증서에 대한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private에서 인증 기관 얻기](/docs/services/blockchain/howto/CA_operate.html)의 [멤버십 서비스 제공자](/docs/services/blockchain/howto/CA_operate.html#msp)를 참조하십시오.

1. `admincerts`라는 새 폴더로 피어 관리자의 signCert를 이동하십시오. 

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
    mkdir admincerts
    cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. [피어 TLS 인증서를 다운로드](#peer-tls)하고 명령행에서 이를 참조할 수 있는지 확인하십시오. 이 문서에 있는 예제 명령을 따른 경우 `$HOME/fabric-ca-client/peer-tls/peertls.pem` 파일에서 이 TLS 인증서를 찾을 수 있습니다. 

3. 순서 지정자의 TLS 인증서도 참조해야 합니다. 순서 지정자의 관리자인 경우 지시사항에 따라 [순서 지정자 TLS 인증서를 다운로드](/docs/services/blockchain/howto/orderer_operate.html#orderer-tls)하십시오. 다른 조직에서 순서 지정자를 관리하는 경우 사용자에게 대역 내외 오퍼레이션에서 TLS 인증서를 제공해야 합니다. 이후 명령에서 이 인증서를 참조할 수 있는 위치에 이 인증서를 저장하십시오.

트리 명령을 실행하여 이 단계를 완료했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. 트리 명령은 다음 구조와 유사한 결과를 생성해야 합니다.
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CLI 환경 변수 설정
{: #environment-variables}

모든 인증서를 필요한 위치로 이동하고 나면 피어 CLI 명령으로 사용된 일부 환경 변수를 설정해야 합니다. 그러면 피어를 운영하기 위해 Fabric 피어 클라이언트를 사용할 준비가 됩니다. 

1. [CLI의 환경 변수](#environment-variables)를 설정했는지 확인하십시오. 

2. [Fabric 도구 바이너리를 다운로드](#peer-client)할 때 작성된 이 구성 디렉토리에 Fabric 구성 경로를 설정하십시오. 

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    이 변수를 설정하면 디렉토리에 있는 피어 클라이언트를 사용하여 명령을 실행할 수 있습니다. 

3. 순서 지정자의 엔드포인트 정보가 필요합니다. 순서 지정자의 관리자인 경우 이 지시사항을 사용하여 [순서 지정자 엔드포인트 정보를 검색](/docs/services/blockchain/howto/orderer_operate.html#orderer-endpoint)할 수 있습니다. 다른 조직에서 순서 지정자를 관리하는 경우 사용자에게 대역 내외 오퍼레이션에서 순서 지정자 URL을 제공해야 합니다. 

4. [피어 엔드포인트 정보를 검색](#peer-endpoint)하십시오. 이 URL을 사용하여 `PEERADDR` 환경 변수를 설정합니다. 시작 부분에서 `http://`를 포함하지 않아야 합니다. 

5. 다음 명령을 실행하여 환경 변수를 설정하십시오.

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  고유 정보로 필드를 바꾸십시오.
    - `<CHANNEL_NAME>`을 피어가 가입하는 채널의 이름으로 바꾸십시오.
    - `<CC_NAME>`을 체인코드를 참조하는 이름으로 바꾸십시오.
    - `<PEERADDR>`을 피어 엔드포인트 호스트 이름 및 포트로 바꾸십시오(이전 단계에서).
    - `<ORDERER_URL>`을 컨소시엄 순서 지정자의 호스트 이름 및 포트로 바꾸십시오.
    - `<PATH_TO_ADMIN_MSP>`를 피어 관리자의 MSP 폴더에 대한 경로로 바꾸십시오. 
    - `<MSPID_OF_PEER_BEING_USED>`를 최초 블록을 페치하거나 채널에 가입하거나 체인코드를 설치하기 위해 사용 중인 피어의 조직 MSPID로 바꾸십시오. 이 값은 Helm 차트 배치 중에 제공됩니다. 

  예를 들어, 다음과 같습니다.

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  TLS를 사용으로 설정하려는 경우에는 다음 명령을 실행하여 수행할 수 있습니다. 

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### CLI를 사용하여 피어를 채널에 가입
{: #icp-cli-join-peer-to-channel}

조직은 피어로 채널에 가입하기 전에 채널의 멤버가 되어야 합니다. 채널의 멤버가 아니면 지시사항에 따라 [새 채널을 작성](#create-channel)할 수 있습니다. 

1. [CLI의 환경 변수](#environment-variables)를 설정했는지 확인하십시오. 

2. 채널의 최초 블록을 페치하여 피어에서 채널 원장을 빌드하십시오. `CORE_PEER_TLS_ROOTCERT_FILE`을 순서 지정자 TLS 인증서의 경로로 설정하고 다음 명령을 실행하십시오. 

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  성공하면 다음과 같은 결과가 표시됩니다. 
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **참고:** 해당 CLI 명령을 실행할 때 무시해도 되는 다음과 같은 경고가 표시될 수 있습니다.

  ```
[msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
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

3. 채널의 최초 블록을 페치했으면 피어를 채널에 가입할 수 있습니다. 다음 명령을 사용하여 `CORE_PEER_TLS_ROOTCERT_FILE`을 피어 TLS 인증서의 경로로 설정하고 채널에 가입하십시오. 

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
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
{: #icp-toolcontainer-install-cc}

이제 피어에 체인코드를 설치하고 인스턴스화할 준비가 되었습니다. 이 지시사항에서 사용하기 위해 `fabric-samples` 저장소에서 `fabcar`를 설치합니다. 다음 명령을 사용하여 Github에서 `fabric-samples` 체인코드를 다운로드한 전후에 [GOPATH를 구성![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "GOPAT")했는지 확인하십시오. 

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

다음 피어 CLI 명령을 실행하여 `fabcar` 체인코드를 피어에 설치하십시오. `CORE_PEER_TLS_ROOTCERT_FILE`을 피어 TLS 인증서의 경로로 설정하십시오. 

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

<!--
**Note:** If this chaincode has already been installed and instantiated on another peer running in IBP Starter or Enterprise Plan, there are additional steps that must be performed before installing the chaincode on the peer. For more instructions about how to avoid errors that are associated with how this chaincode is installed, see the [troubleshooting topic](#icp-cc-install-error).
-->

### CLI를 사용하여 채널에서 체인코드 인스턴스화
{: #icp-toolcontainer-instantiate-cc}

한 피어만 채널에서 체인코드를 인스턴스화하면 되므로 피어를 사용하여 체인코드를 인스턴스화하지 않아도 됩니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어에서 해당 작업을 수행할 수 있습니다. 그러나 피어가 채널에서 체인코드를 인스턴스화하게 하려면 다음 명령을 실행하여 이를 수행할 수 있습니다. `CORE_PEER_TLS_ROOTCERT_FILE`의 값을 피어 TLS 인증서의 경로로 설정하십시오. `--cafile`의 값을 순서 지정자 TLS 인증서로 설정하십시오. 

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

이 명령이 성공적으로 완료되면 다음과 비슷한 내용이 표시됩니다.

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

체인코드가 인스턴스화된 후 체인코드 조회를 사용하고 명령을 호출하여 채널 원장의 데이터를 읽고 쓸 수 있습니다. 자세한 정보는 Hyperledger Fabric 문서에서 [피어 체인코드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) 명령을 참조하십시오. 프록시 IP 및 외부 순서 지정자 포트를 사용하여 순서 지정자 엔드포인트를 호출 명령에 전달해야 합니다. 피어 엔드포인트를 조회 명령에 전달하기만 하면 됩니다. 

## {{site.data.keyword.cloud_notm}} Private에서 피어 로그 보기
{: #peer-log-icp}

ICP 콘솔에서 피어 로그에 액세스하고 Kibana 인터페이스에서 로그를 확인할 수 있습니다.

1. ICP 콘솔의 왼쪽 상단에서 **메뉴** 아이콘을 클릭하십시오.
2. 메뉴 표시줄에서 **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스 테이블에서 helm 릴리스의 이름을 클릭하십시오.
4. 아래로 스크롤하여 **팟(Pod)** 섹션으로 이동하고 테이블 행의 끝에서 **로그 보기** 링크를 클릭하십시오. 피어 로그가 Kibana 인터페이스에서 열립니다.

## 체인코드 업데이트

시간 경과에 따라 피어에서 실행 중인 체인코드를 수정해야 할 가능성이 있습니다. 체인코드는 피어에 설치되어 채널에서 인스턴스화되므로 채널의 모든 피어에서 체인코드를 업데이트해야 합니다.

체인코드를 업데이트하려면 다음 단계를 완료하십시오.

1. 각 피어에서 체인코드를 업데이트하려면 클라이언트 애플리케이션이나 CLI 명령을 사용하여 피어에 체인코드를 설치하는 데 사용한 프로세스를 다시 실행하기만 하면 됩니다. 원래 사용된 이름과 동일한 체인코드 이름을 지정하십시오. 그러나 이번에는 체인코드 `Version`을 늘르십시오. 모든 피어는 같은 이름과 버전을 사용해야 합니다.

2. 채널의 모든 피어에 새 체인코드를 설치한 다음
[피어 체인코드 업그레이드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) 명령을 사용하여 새 체인코드를 사용하도록 채널을 업데이트하십시오.

## ICP에서 실행되는 피어 다시 시작
{: #peer-restart}

인증서를 피어에 추가할 때마다 새 인증서를 인식하도록 피어 노드를 다시 시작해야 합니다.

**kubectl** 명령을 사용하여 ICP에서 실행되는 피어를 다시 시작할 수 있습니다. 이 명령에서는 피어 **팟(pod)**의 이름과 해당 **컨테이너**를 명령 매개변수로 사용해야 합니다. kubectl 명령 사용에 관한 자세한 정보는 [kubectl CLI 구성](#peer-kubectl-configure)을 참조하십시오.

1. ICP 콘솔에서 피어 배치의 **팟(pod)** 이름과 **컨테이너** 이름을 찾으십시오.

  1. 콘솔의 왼쪽 상단에 있는 메뉴 아이콘을 클릭한 다음 **워크로드 > 배치**를 클릭하십시오.
  2. 테이블의 피어 릴리스를 찾아서 클릭하여 여십시오.
  3. 연관된 **팟(pod)** 이름이 표시될 때까지 아래로 스크롤하십시오. **팟** 이름을 기록해 두십시오.
  4. 팟을 클릭하여 여십시오.
  5. **컨테이너** 탭을 클릭하십시오. 피어 **컨테이너** 이름을 기록해 두십시오.

2. 명령행에서 다음 명령을 실행하여 피어를 다시 시작하고 `<PEER_POD_NAME>` 및 `<PEER_CONTAINER_NAME>`을 위의 값으로 바꾸십시오.

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. `kubectl get pods` 명령을 실행하고 출력에서 팟의 **RESTART** 수를 검사하여 피어가 다시 시작되었는지 확인할 수 있습니다.

## 채널 작성
{: #create-channel}

조직이 아직 컨소시엄 또는 채널의 멤버가 아닌 경우 다음 단계를 사용하여 채널을 작성할 수 있습니다. 이 단계를 사용하여 기존 채널을 업데이트할 수도 있습니다. 지시사항은 [조직 정의 준비](#organization-definition) 방법을 제공합니다. 이 정의는 순서 지정자 시스템 채널에 추가하여 [컨소시엄의 멤버](orderer_operate.html#add-organizations-to-consortium)가 될 수 있도록 하는 데 사용됩니다. 그런 다음 [채널 트랜잭션을 작성](#peer-icp-channeltx)하여 새 채널을 형성할 수 있습니다. 

이미 컨소시엄에 가입한 경우 채널 트랜잭션을 준비하는 단계만 완료해야 합니다. 컨소시엄의 다른 멤버도 멤버로서 조직에 새 채널을 형성할 수 있습니다. 업데이트를 컨소시엄에 전송해야 하는 경우 조직 정의를 업데이트할 수 있습니다(예: 새 앵커 피어 정의, 새 관리자 인증서 추가). 

### configtxgen 도구 다운로드
{: #peer-icp-configtxgen}

조직이 컨소시엄 또는 채널에 가입해야 하는 경우 [configtxgen ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen") 도구를 다운로드해야 합니다. 

configtxgen을 가져올 수 있는 가장 쉬운 방법은 Hyperledger Project에서 모든 Fabric 도구 바이너리를 다운로드하는 것입니다. 명령행을 사용하여 바이너리를 다운로드할 디렉토리로 이동하고 다음 명령을 실행하여 바이너리를 패치하십시오. [Curl ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl")을 먼저 설치해야 합니다. 

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

명령으로 `bin/` 디렉토리에서 configtxgen이 설치됩니다. Fabric 도구 바이너리를 다운로드한 디렉토리에 `PATH` 경로를 설정하십시오. 

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

예를 들어, 다음으로 `PATH`를 설정하는 홈 디렉토리에 바이너리를 설치한 경우입니다. 

```
export PATH=$PATH:$HOME/bin
```

## 조직 정의 준비
{: #organization-definition}

컴포넌트를 배치했으면 조직이 조직 정의 파일을 준비하여 컨소시엄에 가입할 수 있습니다. 정의에는 컨소시엄 통제에 참여해야 하는 모든 정보가 포함됩니다(예: 새 채널 작성 또는 가입, 채널에 피어 추가 또는 체인코드 인스턴스화). 정의에는 조직 이름, MSPID 및 MSP 인증서가 포함됩니다. 모든 조직은 이 단계를 완료해야 합니다. 

### Crypto 자료 준비

정의의 조직을 준비하기 전에 [피어의 관리자](/docs/services/blockchain/howto/CA_operate.html#register-admin)를 등록해야 합니다. 피어 관리자 MSP 폴더를 작성한 디렉토리로 이동하십시오. 예제 단계는 `$HOME/fabric-ca-client/peer-admin/msp`의 이 폴더에 작성되었습니다. `configtxgen` 도구로 MSP를 사용하기 전에 일부 추가 단계를 수행해야 합니다. 

1. CA에서 TLS 인증서를 복사하여 `tlscacerts`라는 새 폴더에 배치하십시오.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  그런 다음 인증서를 작성한 `tlscacerts` 디렉토리에 복사해야 합니다. 

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  명령은 다음과 같이 표시됩니다. 

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. signCert를 `signcerts` 폴더에서 `admincerts`라는 새 폴더로 이동하십시오.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

`configtxgen` 도구는 configtx.yaml 파일을 이동하여 정의를 작성합니다. 아래에서 이 파일의 샘플 버전을 찾을 수 있습니다. 

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

이 파일에는 컨소시엄 내에 조직을 정의하는 정보가 포함됩니다. 이 파일의 좀 더 복잡한 버전은 [다운로드한 Fabric 피어 클라이언트](/docs/services/blockchain/howto/peer_operate_icp.html#peer-client)의 `/config` 폴더에서도 사용 가능합니다. 해당 파일을 편집하거나 위의 샘플로 대체하도록 선택할 수 있습니다. 아래의 `FABRIC_CFG_PATH` 값을 설정하려면 이 `/config` 폴더의 위치를 기록해 두십시오. 이 파일의 `Organizations` 섹션을 편집하고 다음 값을 설정하십시오. 

- `Name:`은 조직에 사용할 이름으로 어떠한 이름도 될 수 있습니다. 

- MSPID의 `ID:` 값은 조직의 고유한 ID가 됩니다. 피어 Helm 차트를 배치할 때 이 조직 MSP 값이 지정되었습니다. `<peer pod name>`을 피어의 팟(Pod)으로 대체하여 다음 명령 실행을 통해 피어 컨테이너 내부를 볼 수 있습니다. 

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  출력은 다음과 유사하게 표시됩니다. 
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  그러므로 MSP `ID`는 org1입니다.

- `MSPDir:`은 관리자 MSP에 대한 관리자 경로입니다(예: `/Users/chandra/fabric-ca-client/peer-admin/msp`).

- `AnchorPeers:` 필드는 조직이 가십으로 내부 조직 통신을 위해 리드 피어가 되도록 선택하는 피어입니다. 이는 개인 데이터 또는 서비스 검색과 같은 기능에 사용됩니다. 그러나 필드가 계속해서 필요한 경우에도 개인 데이터 및 서비스 검색은 피어 Helm 차트에 현재 지원되지 않습니다. `Host` 및 `Port` 값은 참고 섹션 `#1. Get the application URL by running these commands` 아래의 ICP 피어에서 사용 가능합니다. 출력은 다음과 유사합니다. 

```
http://9.30.250.70:30481
```

여기서, `Host:` = `9.30.250.70`이고 `Port:` = `30481`입니다.

그런 다음 조직 구성을 출력하려면 다음 명령을 사용하십시오. 

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

호출은 다음 예제 명령과 비슷하게 보일 수 있습니다.

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

명령이 성공하면 `configtxgen`은 JSON 형식으로 조직 정의를 출력합니다. 컨소시엄에 가입하려면 대역 내외 오퍼레이션에서 이 파일을 순서 지정자 조직에 전송해야 합니다. 그런 다음 순서 지정자 조직은 정의를 시스템 채널에 추가하여 [컨소시엄을 형성하거나 기존 컨소시엄에 추가](/docs/services/blockchain/howto/orderer_operate.html#consortium)될 수 있습니다. 

## 채널 트랜잭션 작성
{: #peer-icp-channeltx}

새 채널을 작성하기 전에 조직은 [조직 정의](#organization-definition)를 준비하고 컨소시엄의 멤버가 되어야 합니다. [컨소시엄을 형성하거나 기존 컨소시엄에 추가](/docs/services/blockchain/howto/orderer_operate.html#consortium)되어야 하는 경우 이 지시사항을 따르십시오. 조직이 이미 시스템 채널에 추가된 경우 컨소시엄의 멤버도 새 채널에 쉽게 추가될 수 있습니다. 시스템 채널의 멤버가 아닌 조직은 [채널 업데이트 요청 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html) 사용을 통해 채널에 조직 정의를 추가하여 수동으로 채널에 가입할 수 있습니다. 이 단계를 사용하여 기존 채널을 업데이트할 수도 있습니다. 

### 새 채널 형성
{: #peer-icp-create-channel}

새 채널을 형성하려면 조직은 [configtxgen 도구 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen")를 사용하여 채널 작성 트랜잭션 제안을 작성해야 합니다. 이 도구는 새 채널 멤버를 정의하는 `configtx.yaml` 파일을 이용합니다. 샘플 `configtx.yaml` 파일은 아래에 제공됩니다. 이 파일의 좀 더 복잡한 버전은 [다운로드한 Fabric 피어 클라이언트](/docs/services/blockchain/howto/peer_operate_icp.html#peer-client)의 `/config` 폴더에서도 사용 가능합니다. 해당 파일을 편집하거나 샘플로 대체하도록 선택할 수 있습니다. 아래의 `FABRIC_CFG_PATH` 값을 설정하려면 이 `/config` 폴더의 위치를 기록해 두십시오. 
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

새 채널을 작성하기 위해 관련되는 세 가지 섹션은 **조직**, **기능** 및 **프로파일**입니다. 

- **조직:** 이 섹션은 컨소시엄의 모든 멤버를 정의합니다. 각 조직에는 앵커(예: `&Org1`)가 있습니다. 이 앵커 아래에서 조직의 이름, MSPID, MSP 폴더에 대한 디렉토리 및 교차 피어 가십을 위한 조직의 앵커 피어를 찾을 수 있습니다. 다음 단계를 사용하여 각 컨소시엄 멤버마다 조직 프로파일을 채울 수 있습니다. 
  1. MSPID를 사용하여 조직의 `Name` 및 `ID`를 지정하십시오. 채널을 작성하는 조직은 피어 Helm 차트가 배치될 때 지정된 MSPID를 알고 있어야 합니다. 
  2. `MSPDir`에서 [조직 정의](#organization-definition)를 작성하는 데 사용한 MSP 폴더에 대한 완전한 경로를 지정하십시오. 암호화 자료가 채널 정의 트랜잭션에 사용되지 않는다는 점에 유의하십시오. *configtxgen* 도구는 MSP의 실제 컨텐츠를 무시합니다. 해당 위치에서 폴더 하위 구조가 올바른 MSP 폴더가 예상됩니다. 
  3. `AnchorPeers` 매개변수는 각 조직이 가십으로 내부 조직 통신에 사용하는 리드 피어를 식별하는 데 사용됩니다. 이 조직에 맞는 [앵커 피어 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html)의 역할을 수행할 피어의 호스트 이름 및 포트를 지정하십시오. 이 값은 개인 데이터 또는 서비스 검색과 같은 기능을 사용하는 데 중요합니다. 그러나 개인 데이터 및 서비스 검색은 피어 Helm 차트에 현재 지원되지 않습니다. 

- **기능:** 이 섹션은 `configtx.yaml`에 있어야 하지만 변경이 필요하지 않습니다. 

- **프로파일:** 섹션. 이 섹션에는 새 채널을 작성하는 데 필요한 정보가 포함됩니다. 프로파일에는 다음 정보가 포함됩니다. 
  1. 새 채널의 이름
  2. 시스템 채널 내에 정의되고 채널을 작성하는 데 사용될 컨소시엄의 이름
  3. 채널에 추가될 조직의 목록. 나열된 조직 이름은 나열된 조직 MSPID 및 연관된 앵커 피어를 찾기 위해 `Organizations` 섹션에 나열되는 앵커 피어를 사용합니다. 

  *configtxgen* 도구는 채널 작성 제안을 작성하기 위해 이 `Profiles` 섹션을 사용합니다. 

구성 파일을 업데이트한 후 다음 명령을 사용하여 채널 업데이트 트랜잭션 제안을 생성하십시오.

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

실제 명령은 다음 예제와 유사합니다. 
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


성공하는 경우 아래에 다음 출력과 유사한 결과가 표시됩니다. 명령으로 `FABRIC_CFG_PATH` 디렉토리에 채널 업데이트 트랜잭션 제안이 작성됩니다. 

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

보증 정책에 컨소시엄에 있는 기타 멤버의 추가 서명이 필요하지 않은 경우 새 채널을 형성하기 위해 제안을 순서 지정자에 직접 제출할 수 있습니다. 채널을 작성하려면 채널 업데이트 트랜잭션을 생성했던 동일한 위치에서 다음 명령을 실행하십시오. 

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

여기서, `<ORG_MSPID>`는 조직의 MSPID이고 `<PATH_TO_ADMIN_MSP>`는 피어 관리자 MSP 폴더에 대한 경로입니다. 명령 내부에서 `<orderer_url>`은 채널을 작성할 순서 지정자의 URL이고, `<orderer_tls_cert>`는 순서 지정자 TLS 인증서의 경로입니다. 실제 명령은 아래의 예제와 비슷하게 보일 수 있습니다. 
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## 제안 보증 및 요청 제출

시스템 채널 보증 정책을 충족하도록 적합한 조직에서 새 채널 작성 제안을 서명하는 경우 채널 업데이트 트랜잭션을 순서 지정자에 제출하여 새 채널을 형성할 수 있습니다. 

Node Fabric SDK API를 사용하여 단일 트랜잭션에서 서명 및 제출을 완료할 수 있습니다. 자세한 정보는 Node SDK 문서의 [How to create a Hyperledger Fabric Channel ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) 튜토리얼을 방문하십시오.

## 피어 로그 보기
{: #peer-icp-view-logs}

컴포넌트 로그는 [`kubectl CLI 명령`](#peer-kubectl-configure)을 사용하거나 ICP 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다. 

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

- 또는 [ICP 클러스터 관리 콘솔](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)을 사용하여 로그에 액세스할 수 있으며 Kibana에서 로그인이 열립니다. 

  **참고:** Kibana에서 로그인이 표시되면 `No results found`의 응답을 수신할 수 있습니다. 이 상태는 ICP가 호스트 이름으로 작업자 노드 IP 주소를 사용하는 경우 발생할 수 있습니다. 이 문제점을 해결하려면 패널 상단에서 `node.hostname.keyword`로 시작하는 필터를 제거하십시오. 그런 다음 로그가 표시됩니다. 


## 문제점 해결
{: #peer-icp-troubleshooting}

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
{:codeblock}
2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the selected version is compatible with ICP 2.1.0.3. `v2.7.2 ` is recommended.

3.  After the helm installation completes, [login](#peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```
 {:codeblock}
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
 {:codeblock}

5. Check helm version:

  ```
  helm version --tls
  ```
  {:codeblock}

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
{:codeblock}

실패하고 다음 예와 같은 오류 메시지를 생성합니다 

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**솔루션:**

이 오류는 IBM System Z와 같은 Big Endian 시스템에서 두 개의 Docker Bash 세션이 동일한 Docker 컨테이너에서 열리면 발생합니다. 두 번째 Bash 세션에서 실행 중인 컨테이너에 연결하지 못할 수 있습니다. 이 문제를 해결하려면 [피어 컨테이너를 다시 시작](#peer-restart)하고 피어를 다시 시작한 후 `kubectl exec` 명령을 재시도하십시오.

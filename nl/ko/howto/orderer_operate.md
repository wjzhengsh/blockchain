---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private에서 순서 지정자 작동
{: #icp-orderer-operate}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull}} Platform 순서 지정자를 설치하면 환경 변수에 대한 기본 설정이 포함된 configmap이 작성됩니다. 그런 다음 해당 동작을 구성하기 위해 순서 지정자에 대한 환경 변수를 변경하거나 추가할 수 있습니다.

일반적으로 순서 지정자 관리자는 순서 지정자를 부트스트랩하고 유지보수하는 역할을 담당하지만 하나의 순서 지정자만 존재하는 SOLO 배치에서는 이 역할이 필요하지 않습니다. SOLO 배치의 경우 순서 지정자 관리자는 순서 지정자 시스템 채널에 새 조직을 추가하는 역할을 담당합니다.

## 전제조건
{: #icp-orderer-operate-prerequisites}

순서 지정자를 작동시키려면 {{site.data.keyword.cloud_notm}} Private 클러스터에서 몇 가지 전제조건 단계를 완료해야 합니다.

**전제조건:**
- [CLI 구성](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)
- [순서 지정자 엔드포인트 정보 검색](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
- [순서 지정자 TLS 인증서 다운로드](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)

이제 이 주제의 지시사항을 사용하여 순서 지정자를 작동시킬 수 있습니다. `peer` CLI 바이너리를 가져오기 위해 필요한 명령행을 통해 순서 지정자를 작동시킵니다. `peer` CLI 바이너리는 `configtxlator` 등의 다른 바이너리와 함께 다운로드됩니다. 따라서 이 주제에 있는 명령 중 다수가 "peer"라는 단어로 시작됩니다. 이러한 명령은 실제로 피어를 사용하는 것이 아니라 단지 바이너리의 이름일 뿐입니다.

### CLI 구성
{: #icp-orderer-operate-kubectl-configure}

{{site.data.keyword.cloud_notm}} Private에서 실행되는 순서 지정자 컨테이너에 연결하려면 **kubectl** 명령행 도구를 사용해야 합니다.

1. {{site.data.keyword.cloud_notm}} Private 클러스터 UI에 로그인하십시오. **명령행 도구** 탭으로 이동하여 **Cloud Private CLI**를 클릭하십시오. 다운로드할 수 있는 다음 도구가 표시됩니다.

   * IBM Cloud Private CLI 및 플러그인 설치
   * Kubectl CLI 설치
   * Helm 설치
   * Istio CLI 설치

  순서 지정자를 작동시키려면 첫 **세 가지** 도구를 사용해야 하며, 이 중에 Helm은 선택사항입니다. 각각을 클릭하고 사용 중인 머신 유형에 맞는 `curl` 명령을 실행하십시오. 그런 다음 각 도구마다 `chmod` 및 `sudo mv` 명령을 실행하십시오. `chmod` 명령으로 해당 CLI의 권한을 변경하여 실행할 수 있도록 하고 `sudo mv` 명령으로 파일을 이동하고 이름을 변경합니다.

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

  정상적으로 완료되는 경우 이 명령은 다음 예제와 유사한 응답을 리턴합니다. 여기서 `orderer-6cf85f6687-hcxpl`은 순서 지정자 컨테이너의 이름입니다.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  이제 **kubectl** 도구를 사용하여 순서 지정자 엔드포인트 정보를 가져올 준비가 되었습니다.

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

### 순서 지정자 엔드포인트 정보 검색
{: #icp-orderer-operate-orderer-endpoint}

순서 지정자 시스템 채널을 업데이트하려면 순서 지정자 엔드포인트를 대상으로 지정해야 합니다. 다음 단계를 완료하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인한 후 왼쪽 상단 구석에 있는 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스의 이름을 찾아서 Helm 릴리스 세부사항 패널을 여십시오.
4. 패널의 맨 아래에 있는 **참고** 섹션으로 스크롤하십시오. **참고** 섹션에는 순서 지정자 배치를 작동시킬 수 있도록 해주는 명령 세트가 포함되어 있습니다.
5. 아직 구성하지 않은 경우 지시사항에 따라 [kubectl CLI](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)를 구성하십시오. 순서 지정자 컨테이너와 상호작용하려면 해당 CLI를 사용해야 합니다.
6. CLI에서 **1. 다음 명령을 실행하여 애플리케이션 URL을 가져오십시오.** 다음의 참고에 있는 첫 번째 명령을 실행하십시오. 이 명령은 다음 예제와 유사한 순서 지정자 URL 및 포트를 인쇄합니다. 나중에 프록시 주소 및 외부 노드 포트를 설정하는 명령에서 사용하기 위해 이 URL을 저장하십시오.

  ```
  http://9.30.94.174:30159
  ```

이 예제에서 프록시 IP 주소는 `9.30.94.174`이며, 7050에 해당하는 외부 노드 포트는 `30159`입니다.  

**참고:** 순서 지정자를 방화벽 뒤에 배치하는 경우 해당 플랫폼의 네트워크에서 이 태스크를 완료할 수 있도록 패스스루를 열어야 합니다.

### 순서 지정자 TLS 인증서 다운로드
{: #icp-orderer-operate-tls-cert}

원격 클라이언트에서 순서 지정자와 통신하려면 순서 지정자 TLS 인증서를 다운로드하여 해당 명령에 전달해야 합니다. 다음 단계를 완료하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다.

1. {{site.data.keyword.cloud_notm}} Private 콘솔에 로그인한 후 왼쪽 상단 구석에 있는 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스의 이름을 찾아서 Helm 릴리스 세부사항 패널을 여십시오.
4. 패널의 맨 아래에 있는 **참고** 섹션으로 스크롤하십시오. **참고** 섹션에는 순서 지정자 배치를 작동시킬 수 있도록 해주는 명령 세트가 포함되어 있습니다.
5. 아직 구성하지 않은 경우 지시사항에 따라 [kubectl CLI](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)를 구성하십시오. 순서 지정자 컨테이너와 상호작용하려면 해당 CLI를 사용해야 합니다.
6. CLI에서 **3. 순서 지정자 TLS 루트 인증서 파일 가져오기** 다음의 참고에 있는 세 번째 명령을 실행하십시오.  이 명령은 TLS 인증서를 로컬 시스템에 `cert.pem` 파일로 저장합니다.

  **참고:** 명령을 실행하기 전에 아래의 명령에 표시된 것처럼 `app=orderer`를 제거할 수 있습니다.

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. 생성된 인증서를 나중에 명령에서 참조할 수 있는 위치로 이동한 후 `orderertls.pem`으로 이름을 바꾸십시오.

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### 로컬 시스템에서 인증서 관리
{: #manage-certs}

순서 지정자 관리자 MSP 폴더가 생성되는 디렉토리로 전환하십시오. 이 문서의 예제 단계를 수행한 방법 또는 배치하는 컴포넌트의 수에 따라 `$HOME/fabric-ca-client/orderer-admin/msp` 또는 `$HOME/fabric-ca-client/peer-admin/msp`에서 MSP 폴더를 찾을 수 있습니다.

순서 지정자를 작동시키려면 먼저 로컬 시스템에서 몇 가지 인증서 관리 작업을 수행해야 합니다. 또한 순서 지정자에서 TLS 인증서에 액세스할 수 있는지 확인해야 합니다. 사용할 인증서에 대한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private에서 인증 기관 작동](/docs/services/blockchain/howto/CA_operate.html#ca-operate)의 [MSP(Membership Service Provider)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)를 참조하십시오.

1. 순서 지정자 관리자의 signCert를 `admincerts`로 이름 지정된 새 폴더로 이동하십시오.

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. [순서 지정자 TLS 인증서 다운로드](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)를 수행할 수 있으며 명령행에서 해당 인증서를 참조할 수 있는지 확인하십시오. 이 문서의 예제 명령을 수행한 경우 `$HOME/fabric-ca-client/orderer-tls/orderertls.pem` 파일에서 이 TLS 인증서를 찾을 수 있습니다.

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
├── orderer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
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

## 순서 지정자 시스템 채널에 조직 추가
{: #icp-orderer-operate-add-organizations-to-consortium}

**참고:** 피어를 배치한 후 스타터 또는 엔터프라이즈 플랜 네트워크에 연결하려는 경우 이 단계를 건너뛸 수 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Helm 차트를 사용하여 순서 지정자를 배치하는 경우 자동으로 시스템 채널의 유일한 관리자인 순서 지정자 조직으로 최초 블록이 작성됩니다. 이는 순서 지정자 조직 관리자에게만 콘소시엄에 조직을 추가할 수 있는 권한이 있음을 의미합니다. 순서 지정자 시스템 채널에 새 조직이 추가된 후에는 이 내용을 통제하는 정책(순서 지정자 시스템 채널의 경우 `mod_policy`)을 변경해서는 안됩니다.

순서 지정자 시스템 채널에 조직을 추가하는 경우 해당 조직이 기본적으로 채널을 작성할 수 있는 구성원의 목록인 "콘소시엄"에 포함됩니다. 또한 컨소시엄의 구성원이 되면 시스템 채널에 나열된 인증서 및 정보를 사용하여 쉽게 채널에 추가할 수 있게 됩니다.

순서 지정자 시스템 채널을 업데이트하는 작업은 "구성 업데이트 트랜잭션"을 통해 수행되며, 해당 조직과 관련된 MSP 정보가 순서 지정자 시스템 채널의 채널 구성에 추가됩니다.

순서 지정자 시스템 채널에 조직을 추가하는 작업은 기본적으로 조직을 추가하기 위해 채널의 구성을 업데이트하는 작업과 동일한 플로우입니다. 하지만 업데이트할 채널이 애플리케이션 채널이 아니고 관련 관리자가 피어 조직이 아닌 순서 지정자 관리자이기 때문에 몇 가지 항목을 변경해야 합니다.

시스템 채널에 먼저 가입하지 않고도 채널에 조직을 추가할 수 있습니다. 자세한 정보는 Hyperledger Fabric 문서에서 [채널에 조직 추가 튜토리얼 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html "채널에 조직 추가")을 참조하십시오.

다음 목록은 컨소시엄의 다양한 조직 세트에서 수행되는 일반 단계 및 태스크를 보여줍니다.

1. 콘소시엄에 가입하는 각각의 조직에서 [조직 정의를 준비](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)해야 합니다.
2. 순서 지정자 조직 관리자는 순서 지정자 시스템 채널에 조직을 추가하여 [컨소시엄을 구성](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium)합니다.
3. 콘소시엄의 조직은 채널 구성 트랜잭션을 준비하여 [새 채널을 작성](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx)할 수 있습니다.

## Fabric 도구 가져오기
{: #icp-orderer-operate-get-fabric-tools}

시스템 채널을 업데이트하려면 다음과 같은 Hyperledger Fabric 도구를 다운로드해야 합니다.
- [peer ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html) - 최초 블록을 페치하고 시스템 채널을 업데이트할 수 있도록 해줍니다.
- [configtxlator ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html) - protobuf 형식의 채널 구성을 더 쉽게 읽고 업데이트할 수 있는 JSON 형식으로 변환합니다.

1. 도구를 저장할 위치를 결정한 후 다음 명령을 실행하십시오.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. 도구에 사용자의 경로를 설정하십시오. 여기서, 위의 Fabric 도구가 다운로드됩니다.

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  예를 들어, 홈 디렉토리에 바이너리를 설치한 경우 다음과 같이 `PATH`를 설정할 수 있습니다..

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. 바이너리를 다운로드하면 core.yaml, orderer.yaml 및 configtx.yaml 파일을 포함한 구성 폴더가 작성됩니다. 이 구성 디렉토리에 Fabric 구성 경로를 설정하십시오.

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  예를 들면 다음과 같습니다.
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## 조직 정의 작성
{: #icp-orderer-operate-org-definition}

조직의 **정의**에는 조직 이름(MSP ID) 및 관련 인증서가 포함되어 있습니다. 시스템 채널 및 애플리케이션 채널은 채널 작성, 업데이트 및 트랜잭션 인증을 제어하는 정책에 조직을 포함시키기 위해 이 정의를 사용합니다. 컨소시엄에 가입하려는 각각의 조직에서 이 단계를 완료해야 합니다. 자세한 정보는 [조직 정의 준비](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)를 참조하십시오.

## 컨소시엄 구성
{: #icp-orderer-operate-consortium}

컨소시엄을 구성하기 위한 상위 레벨 플로우를 기억하십시오.

1. 순서 지정자 시스템 채널은 시스템 채널의 구성원인 순서 지정자 조직으로만 구성되어 있습니다. 해당 조직은 추가적인 서명 없이 업데이트할 수 있습니다.
2. 순서 지정자 조직 관리자는 컨소시엄에 가입하려는 구성원으로부터 조직 정의를 수신합니다. 순서 지정자 조직은 조직 정의를 사용하여 순서 지정자 시스템 채널의 구성을 업데이트합니다.

### 조직 정의 가져오기

순서 지정자는 컨소시엄에 가입하려는 구성원으로부터 [조직 정의](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)를 수신해야 합니다. 이 작업은 해당 MSP ID 및 압호화 자료가 포함된 JSON 파일을 전송하는 다른 구성원과의 대역 내외 오퍼레이션에서 완료해야 합니다. 참고로 아래의 명령에서는 `org-definitions`로 이름 지정된 폴더를 작성하여 관련된 모든 파일을 해당 디렉토리에 배치한 것으로 가정합니다.

### 시스템 채널의 최초 블록 페치

1. 배치 중에 설정되는 순서 지정자 조직 MSP 이름, 순서 지정자 MSP의 경로 및 순서 지정자의 TLSCA 인증서에 대한 경로를 사용하여 다음과 같이 환경 변수를 설정하십시오.

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  고유 정보로 필드를 바꾸십시오.

    - `<CORE_PEER_LOCALMSPID>`를 순서 지정자 조직의 MSP ID로 대체하십시오. `<orderer pod name>`을 순서 지정자 팟(Pod)의 값으로 대체하고 다음 명령을 실행하면 순서 지정자 컨테이너 내에도 표시됩니다.

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      출력은 다음과 유사하게 표시됩니다.

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      따라서 `CORE_PEER_LOCALMSPID`는 ordererOrg입니다.

    - `<CORE_PEER_MSPCONFIGPATH>`를 순서 지정자 조직의 관리자 MSP 폴더에 대한 경로로 대체하십시오.
    - `<CORE_PEER_TLS_ROOTCERT_FILE>`을 TLS CA 인증서의 경로로 대체하십시오.
    - `<PROXY_IP>`를 [순서 지정자 엔드포인트 정보](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)의 프록시 IP 주소로 대체하십시오.
    - `<EXTERNAL_NODE_PORT>`를 [순서 지정자 엔드포인트 정보](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)의 외부 노드 포트로 대체하십시오.

  환경 변수는 다음과 유사합니다.

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  언제든지 다음 명령을 실행하여 이러한 환경 변수를 확인할 수 있습니다.

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. 채널 이름을 환경 변수로 설정하십시오. 순서 지정자 시스템 채널의 이름은 항상 `test-system-channel-name`입니다.

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. 시스템 채널의 최초 블록을 페치하십시오. 먼저 구성 업데이트 프로세스 중에 생성되는 구성 파일을 저장하기 위해 `org-definitions` 내에 `configupdate`라는 디렉토리를 작성하십시오. 이 디렉토리에 원하는 임의의 이름을 지정할 수 있지만 다음 샘플 명령에서는 `configupdate`를 사용합니다.

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  `<orderer_TLS_root_cert_file>`을 이 [단계](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)에서 작성한 `orderertls.pem` 파일에 대한 경로로 대체하십시오. 예를 들어 해당 명령은 다음 예제와 유사할 수 있습니다.

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  정상적으로 완료되는 경우 이 명령에서 다음과 같은 출력을 생성합니다.

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  이후에는 파일 시스템에서 `genesis.pb` 블록도 찾을 수 있습니다.

### 시스템 채널 업데이트 작성
{: #icp-orderer-operate-system-channel-update}

다운로드된 [Fabric 도구](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-get-fabric-tools)(`configtxtlator`)는 protobuf 형식의 채널 구성을 JSON 형식으로 변환하거나 반대로 변환합니다.

다음 단계는 [블록을 JSON 형식으로 변환 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down "구성을 JSON으로 변환하여 크기 줄이기")하는 작업과 관련된 채널 업데이트 튜토리얼의 일반 플로우를 수행합니다. 애플리케이션 채널이 아닌 순서 지정자 시스템 채널을 업데이트하고 있다는 사실을 반영하려면 튜토리얼에 있는 명령을 일부 변경해야 합니다. 해당 튜토리얼을 방문하여 이 프로세스에 대한 자세한 정보를 확인할 수 있습니다. 이 절에서는 단순히 사용자를 위한 명령만 제공합니다.

1. 조직 정의 JSON 파일을 [조직을 작성한](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) 폴더에서 `configupdate` 폴더로 복사하십시오. 아래의 예제에서 조직 정의 JSON 파일은 `org1definition.json`입니다.

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. 최초 블록을 JSON 형식으로 변환하십시오. 다음 명령을 실행하십시오.

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. 조직의 암호화 자료를 컨소시엄 구성에 추가하려면 다음 명령을 실행하십시오. <NEWORGMSP>를 [사용자가 작성한 조직](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition)에 대한 조직 MSP ID로 대체하십시오.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  이 명령은 다음 예제와 같을 수 있습니다.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. 컨소시엄에 가입해야 하는 각각의 조직에 대해 이 명령을 반복하십시오. `./orgdefinition.json` 파일을 새 조직 JSON 파일로 변경해야 합니다.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. `modified_config.json` 파일을 JSON 형식의 구성 업데이트로 변환하려면 다음 명령을 실행하십시오.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. 이 명령을 완료한 후 `config_update`에 엔벨로프를 배치하십시오.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. 구성 업데이트를 다시 protobuf 형식으로 변환하려면 다음 명령을 실행하십시오.

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### 시스템 채널로 업데이트 전송

다음 단계를 완료하기 전에 `FABRIC_CFG_PATH`, `$PROXY` 및 `ORDERER_PORT`가 올바르게 설정되어 있는지 확인하십시오. 다음 명령을 실행하십시오.

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
```
{:codeblock}

환경 변수는 다음 예제와 유사합니다.

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

채널 구성 업데이트(`config_update_in_envelope.json`)를 작성한 후에는 순서 지정자 조직에서 다음 명령을 사용하여 채널 업데이트를 제출할 수 있습니다.

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

이 명령은 동시에 업데이트 요청에 서명하고 순서 지정자에게 전송합니다. 순서 지정자 조직이 시스템 채널의 유일한 관리자이기 때문에 이 업데이트가 유효한 요청이 되기 위해서는 하나의 서명만 있으면 됩니다. 시스템 채널 업데이트가 정상적으로 완료되면 다음 출력이 표시됩니다.

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

하나의 순서 지정자 시스템 채널 구성 업데이트에 복수의 조직 정의를 포함시킬 수 있지만 한 번에 하나의 조직과 관련된 채널을 업데이트한 후 업데이트가 정상적으로 완료되었는지 확인하는 것이 좋습니다.

## 순서 지정자 로그 보기
{: #icp-orderer-operate-orderer-view-logs}

컴포넌트 로그는 [`kubectl CLI 명령`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)을 사용하거나 {{site.data.keyword.cloud_notm}} Private 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 표시될 수 있습니다. 

- `kubectl logs` 명령을 실행하여 팟(Pod) 내부의 컨테이너 로그를 보십시오. 팟(Pod) 이름이 확실하지 않은 경우 다음 명령을 실행하여 팟(Pod)의 목록을 보십시오.

  ```
  kubectl get pods
  ```
  {:codeblock}

  그런 다음 `<pod_name>`을 상기 명령 출력의 팟(Pod) 이름으로 대체하고 다음 명령을 실행하여 팟(Pod) 내에 상주하는 순서 지정자 컨테이너에 대한 로그를 검색하십시오.

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  `kubectl logs` 명령에 대한 자세한 정보는 [Kubernetes 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)를 참조하십시오.

- 또는 Kibana에서 로그를 여는 [{{site.data.keyword.cloud_notm}} Private 클러스터 관리 콘솔 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)을 사용하여 로그에 액세스할 수 있습니다.

  **참고:** Kibana에서 로그인이 표시되면 `No results found`의 응답을 수신할 수 있습니다. 이 상태는 {{site.data.keyword.blockchainfull_notm}} Private이 호스트 이름으로 작업자 노드 IP 주소를 사용하는 경우 발생할 수 있습니다. 이 문제점을 해결하려면 패널 상단에서 `node.hostname.keyword`로 시작하는 필터를 제거하십시오. 그런 다음 로그가 표시됩니다.

## 문제점 해결
{: #icp-orderer-operate-troubleshooting}

### **문제점:** `peer channel update` 명령이 오류로 인해 실패합니다.
{: #icp-orderer-operate-peer-channel-update-error}

`peer channel update` 명령을 실행할 때 다음과 같은 오류가 수신될 수 있습니다.

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

이 오류는 피어에서 순서 지정자와 동일한 조직 MSP ID를 사용하는 경우에 발생할 수 있습니다.  

**솔루션:**  

이 문제점을 해결하려면 순서 지정자를 배치할 때 사용된 것과 다른 조직 MSP ID를 지정하여 피어를 재배치해야 합니다.

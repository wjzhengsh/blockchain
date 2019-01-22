---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# {{site.data.keyword.cloud_notm}} Private에서 인증 기관 작동
{: #ca-operate}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

인증 기관(CA)은 네트워크 상의 ID를 제공합니다. CA는 복수의 당사자 간에 신뢰를 설정하기 위해 사용되는 공개적으로 신뢰할 수 있는 공증인과 유사한 것으로 간주할 수 있습니다. 네트워크에 있는 각각의 엔티티에는 엔티티의 디지털 ID를 캡슐화하기 위해 루트 CA에서 서명하는 인증서가 지정됩니다. 이 인증서는 네트워크에서 수행되는 모든 서명 및 확인 오퍼레이션을 위한 신뢰 루트입니다.
{:shortdesc}

다중 클라우드 블록체인 네트워크에 있는 각각의 조직에서 자체 CA를 배치해야 합니다. 조직의 CA는 조직에서 운영하는 컴포넌트(예: 순서 지정 서비스, 피어 또는 애플리케이션)에 대한 요청에 서명합니다. 따라서 다른 컴포넌트를 배치하기 전에 CA를 배치해야 합니다.

인증 기관을 작동시키려면 몇 가지 전제조건 단계를 수행해야 합니다.

- [CLI 구성](#ca-kubectl-configure)
- [인증 기관 URL 검색](#ca-url)
- [CA TLS 인증서 다운로드](#ca-tls)
- [Fabric CA 클라이언트 설정](#fabric-ca-client)
- [CA 관리자로 인증서 생성](#enroll-admin)

전제조건 단계를 완료한 후에는 CA를 사용하여 네트워크의 새 ID를 조직에 등록하고 애플리케이션에서 사용할 인증서를 생성할 수 있습니다.

- [CA를 사용하여 순서 지정자 또는 피어 배치](#deploy-orderer-peer)
<!-- [Register and enroll client applications](#register-enroll-app)-->

이 안내서에서는 CA를 작동시키는 방법 및 해당 인증서를 사용하여 블록체인 네트워크를 관리하는 방법에 대한 몇 가지 중요한 개념 정보도 제공합니다.

- [MSP(Membership Service Provider)](#msp)
- [TLS 인증서](#tls)

## 선행 조건
{: #prerequisites}

네트워크를 작성하는지 또는 가입하는지 여부에 관계 없이 CA를 사용하여 조직에 속한 기타 컴포넌트 및 ID를 배치하려면 다음과 같은 전제조건 단계를 완료해야 합니다.

### CLI 구성
{: #ca-kubectl-configure}

ICP에서 실행되는 CA 컨테이너에 연결하려면 **kubectl** 명령행 도구를 사용해야 합니다.

1. ICP 클러스터 UI에 로그인하십시오. **명령행 도구** 탭으로 이동하여 **Cloud Private CLI**를 클릭하십시오. 다음과 같이 다운로드할 수 있는 도구가 표시됩니다.

   * IBM Cloud Private CLI 및 플러그인 설치
   * Kubectl CLI 설치
   * Helm 설치
   * Istio CLI 설치

  CA를 작동시키려면 첫 **세 가지** 도구를 사용해야 하며, 이 중에 Helm은 선택사항입니다. 각각의 도구를 클릭한 후 사용 중인 시스템 유형에 해당하는 `curl` 명령을 실행하십시오. 그런 다음 각각의 도구에 대해 `chmod` 및 `sudo mv` 명령을 실행하십시오. `chmod` 명령은 실행할 수 있도록 해당 CLI의 권한을 변경하며, `sudo mv` 명령은 해당 파일을 이동한 후 이름을 바꿉니다.

  첫 번째 도구인 **cloudctl**에 대한 명령은 다음 예제와 유사합니다.

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  두 번째 도구인 **kubectl**에 대한 명령은 다음 예제와 유사합니다.

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

  정상적으로 완료되는 경우 이 명령은 다음 예제와 유사한 응답을 리턴합니다. 여기서 `ca-6cf85f6687-hcxpl`은 CA 컨테이너의 이름입니다.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  이제 **kubectl** 도구를 사용하여 CA URL을 가져올 준비가 되었습니다.

3. 선택적으로 **Helm**를 사용하려면 몇 가지 추가 단계를 완료해야 합니다. Helm은 선택적으로 설치되며, 이 지시사항에서 반드시 사용할 필요는 없습니다. 하지만 Helm 릴리스를 관리하고 자체 아카이브를 작성하여 ICP에 배치하기 위해 유용할 수 있습니다.

  1. "Helm 설치"를 클릭한 후 ICP UI에서 `curl` 명령을 실행하십시오.
  2. 다음 명령을 실행하여 `tar` 파일을 압축 해제하십시오.  
    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}
  3. `darwin-amd64`에 `/helm`을 추가하여 `sudo mv` 명령을 실행하십시오. Helm에 대한 `chmod` 명령을 반드시 실행할 필요는 없습니다. 예를 들면 다음과 같습니다.  
    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  `helm help` 명령을 실행하여 Helm이 정상적으로 설치되었는지 확인할 수 있습니다.

### 인증 기관 URL 검색
{: #ca-url}

인증서를 생성하거나 새 ID로 등록하는 요청의 경우 CA URL을 대상으로 지정해야 합니다. ICP 콘솔 UI를 사용하여 CA URL을 찾을 수 있습니다. 다음 단계를 완료하려면 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치")여야 합니다.

1. ICP 콘솔에 로그인한 후 왼쪽 상단 구석에 있는 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스의 이름을 찾아서 Helm 릴리스 세부사항 패널을 여십시오.
4. 패널의 맨 아래에 있는 **참고** 섹션으로 스크롤하십시오. **참고** 섹션에서 CA 배치를 작동시킬 수 있도록 해주는 명령 세트를 찾을 수 있습니다.
5. 아직 구성하지 않은 경우 CA 컨테이너와 상호작용하기 위해 사용해야 하는 [kubectl CLI](#ca-kubectl-configure)를 구성하는 단계를 수행하십시오.
6. CLI에서 **1. 다음 명령을 실행하여 애플리케이션 URL을 가져오십시오.** 다음의 참고에 있는 첫 번째 명령을 실행하십시오. 이 명령은 다음 예제와 유사한 CA URL 및 포트를 인쇄합니다. 후속 명령에서 사용하기 위해 이 값을 애플리케이션 URL로 저장하십시오.

  ```
  http://9.30.94.174:30167
  ```

**참고:** CA를 방화벽 뒤에 배치하는 경우 해당 플랫폼의 네트워크에서 CA와 TLS 핸드쉐이크를 완료할 수 있도록 패스스루를 열어야 합니다. CA URL과 연결되어 있는 포트에 대해서만 패스스루를 사용으로 설정해야 합니다.


### CA TLS 인증서 검색
{: #ca-tls}

원격 클라이언트에서 CA와 통신하려면 CA TLS 인증서를 다운로드하여 해당 명령과 함께 전달해야 합니다.

1. ICP 콘솔에 로그인하십시오. 왼쪽 상단에 있는 **메뉴** 아이콘을 클릭하십시오.
2. **워크로드** > **Helm 릴리스**를 클릭하십시오.
3. Helm 릴리스의 이름을 찾아서 Helm 릴리스 세부사항 패널을 여십시오.
4. 패널의 맨 아래에 있는 **참고** 섹션으로 스크롤하십시오. **참고** 섹션에서 CA 배치를 작동시킬 수 있도록 해주는 명령 세트를 찾을 수 있습니다.
5. 아직 구성하지 않은 경우 CA 컨테이너와 상호작용하기 위해 사용해야 하는 [kubectl CLI](#ca-kubectl-configure)를 구성하는 단계를 수행하십시오.
6. CLI에서 **3. TLS 인증서를 가져오십시오.** 다음에 있는 세 번째 참고의 명령을 실행하십시오. 이 명령은 TLS 인증서를 로컬 디렉토리에 `tls.pem` 파일로 저장합니다. 나중에 명령에서 이 인증서를 참조해야 합니다. 해당 위치를 기록해 두십시오.
7. 또한 이 명령은 인증서를 base64 형식으로 변환하고 인쇄합니다. 결과는 다음 문자열과 유사합니다.

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  **중요:** 이 문자열을 복사하여 `ICP CA TLS 인증서`로 저장해야 합니다. 추가 컴포넌트를 배치할 때 이 문자열을 사용합니다.

### Fabric CA 클라이언트 설정
{: #fabric-ca-client}

Fabric CA 클라이언트를 사용하여 CA를 작동시킬 수 있습니다. 다음 지시사항에서는 Fabric CA 클라이언트를 사용하여 조직에 속한 다른 컴포넌트의 ID를 등록하는 방법에 대해 설명합니다. Fabric SDK를 사용하여 전제조건 단계를 완료할 수도 있습니다.

1. [Fabric CA 클라이언트 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-client "Fabric CA 클라이언트 다운로드")를 로컬 파일 시스템에 다운로드해야 합니다.

  Fabric CA 클라이언트를 얻는 가장 간단한 방법은 모든 Fabric 도구 2진을 직접 다운로드하는 것입니다. 명령행을 사용하여 2진을 다운로드하려는 디렉토리로 이동한 후 다음 [Curl ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") 명령을 실행하여 페치하십시오.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

  이 명령은 `bin/` 디렉토리에 2진을 설치합니다.

2. `PATH` 경로를 Fabric 도구 2진을 다운로드한 디렉토리로 설정하십시오.

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  예를 들어 홈 디렉토리에 2진을 설치한 경우 다음과 같이 `PATH`를 설정할 수 있습니다.

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. CA 관리자의 인증서를 저장할 폴더를 작성하십시오.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. `$FABRIC_CA_CLIENT_HOME` 환경 변수의 값을 CA 클라이언트에서 생성된 [MSP](#msp) 인증서를 저장할 경로로 설정하십시오. 이전 시도에서 작성된 구성 자료를 제거했는지 확인하십시오. 이전에 `enroll` 명령을 실행하지 않은 경우 `msp` 폴더 및 `.yaml` 파일이 존재하지 않습니다.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. [ICP에서 다운로드](#ca-tls)한 TLS 인증서를 나중에 명령에서 참조할 수 있는 디렉토리에 복사하십시오. `$HOME/fabric-ca-client` 디렉토리에 있는지 확인하십시오.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### CA 관리자로 인증서 생성
{: #enroll-ca-admin}

CA를 사용하여 컴포넌트 또는 클라이언트 애플리케이션을 배치하려면 먼저 사용자를 새 사용자를 등록할 권한이 있는 관리자로 인증하는 인증서를 생성해야 합니다. 필요한 인증서, 개인 키 및 공용 인증서(등록 인증서 또는 signCert라고도 함)를 생성하는 프로세스를 **등록**이라고 합니다.

인증 기관을 사용하여 등록된 ID를 사용해야만 인증서를 생성할 수 있습니다. 인증서를 생성하기 위해 ID의 이름 및 시크릿을 제공하십시오. **CA 관리자** ID는 CA를 배치할 때 자동으로 등록되었습니다. 이제 이후에 다른 피어 또는 순서 지정자 ID를 등록하기 위해 사용되는 인증서가 포함된 MSP 폴더를 생성하기 위해 해당 관리자 이름 및 비밀번호를 사용하여 Fabric CA 클라이언트와 함께 `enroll` 명령을 실행할 수 있습니다.

1. [Fabric CA 클라이언트를 설정](#fabric-ca-client)하는 단계를 완료했으며 `$FABRIC_CA_CLIENT_HOME`이 CA 관리자 인증서를 저장할 디렉토리로 설정되어 있는지 확인하십시오.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. 다음 명령을 실행하여 인증서를 생성하십시오.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  명령에 있는 `<enroll_id>` 및 `<enroll_password>`는 인증 기관을 배치할 때 Kubernetes 시크릿에 전달한 [CA 관리자 이름 및 비밀번호](CA_deploy_icp.html#admin-secret)입니다. `<ca_url_with_port>` 내에 [CA URL](#ca-url)을 삽입하십시오. 시작 부분에 있는 `http://`를 제거하십시오. `<ca_name>`은 CA를 배치할 때 `CA Name` 필드에 제공한 값입니다.

  `<ca_tls_cert_path>`는 [CA TLS 인증서](#ca-tls)에 대한 전체 경로입니다.

  실제 호출은 다음 예제 명령과 비슷하게 보일 수 있습니다.

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  `enroll` 명령은 Fabric CA 클라이언트에 대한 `$HOME` 경로로 설정한 디렉토리 내에 있는 MSP(Membership Service Provider) 폴더로 알려진 전체 인증서 세트를 생성합니다. 예를 들면 `$HOME/fabric-ca-client/ca-admin`입니다. MSP에 대한 자세한 정보 및 MSP 폴더에 포함되는 항목은 [MSP(Membership Service Provider)](#msp)를 참조하십시오.

  `enroll` 명령이 실패하는 경우 가능한 원인은 [문제점 해결 주제](#ca-enroll-error)를 참조하십시오.

tree 명령을 실행하여 모든 전제조건 단계를 완료했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. tree 명령에서 다음 구조와 유사한 결과를 생성해야 합니다.

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
└── catls
    └── tls.pem
```

## CA를 사용하여 순서 지정자 또는 피어 배치
{: #deploy-orderer-peer}

순서 지정자 또는 피어를 배치하려면 먼저 컴포넌트 ID 및 CA에 대한 중요한 정보가 포함된 구성 JSON 파일을 작성해야 합니다. 그런 다음 구성 중에 [Kubernetes 시크릿 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/configuration/secret/) 오브젝트를 사용하여 이 파일을 Helm 차트에 전달해야 합니다. 이 파일을 통해 순서 지정자 또는 피어가 블록체인 네트워크에 가입하기 위해 CA에서 받아야 하는 인증서를 가져올 수 있습니다. 이 파일에는 관리자로 컴포넌트를 작동시킬 수 있도록 해주는 관리자 인증서도 포함되어 있습니다.

다음 지시사항은 편집하여 로컬 파일 시스템에 저장할 수 있는 [템플리트 JSON 구성 파일](#config-file-template)을 제공하며 이 파일을 완성하기 위해 CA를 사용하는 방법에 대해 설명합니다.

- ICP에 순서 지정자를 배치하거나 ICP에서 호스팅되는 컨소시엄에 연결할 피어를 배치하는 경우 아래의 지시사항을 수행하십시오.
- 스타터 플랜 또는 엔터프라이즈 플랜에 연결할 피어를 배치하려는 경우 [IBM Cloud Private에 스타터 플랜 또는 엔터프라이즈 플랜에 연결할 피어 배치](peer_deploy_ibp.html)의 지시사항을 대신 수행하십시오. 이 단계는 {{site.data.keyword.blockchainfull_notm}} Platform에서 CA를 사용하여 ICP에 피어를 배치하는 방법에 대해 설명합니다.

### 구성 파일
{: #config-file-template}

다음은 구성 파일을 위한 템플리트입니다.
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

이 전체 파일을 편집하여 로컬 파일 시스템에 JSON 파일로 저장할 수 있는 텍스트 편집기로 복사하십시오.

### CA 연결 정보

먼저 ICP에 있는 CA의 연결 정보를 제공해야 합니다. 다음 정보를 사용하여 파일의 `"component"` 섹션에 있는 값을 완성하십시오.

- `"cahost"` 및 `"caport"` 값은 [CA URL](#ca-url)의 URL 및 포트입니다. 예를 들어 CA URL이 http://9.30.94.174:30167인 경우 `"cahost"`의 값은 `9.30.94.174`이고 `"caport"`의 값은 `30167`입니다.
- `"caname"`은 CA Helm 차트를 배치할 때 지정된 CA의 이름입니다. [CA 관리자 ID](#enroll-admin)를 사용하여 등록할 때 해당 명령에서 CA 이름을 참조했습니다.
- `"cacert"`는 CA의 base64 인코딩 TLS 인증서입니다. Helm 릴리스의 참고를 사용하여 ICP 콘솔에서 [CA의 TLS 인증서를 검색](#ca-tls)한 후 다음 섹션에 note 명령의 base64 출력을 삽입하십시오.
  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  업데이트 후에는 `"cacert"` 필드가 다음 예제와 유사합니다.

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

- 다음 `"tls"` 섹션의 필드는 CA 배치 중에 지정된 CA TLS 인스턴스 이름의 값을 사용해야 하는 경우를 제외하고 상기 컴포넌트 섹션으로 전달된 것과 동일한 정보를 사용해야 합니다. `"cacert"` 값에 대해 동일한 TLS 인증서를 사용하십시오.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

인증 기관 연결 정보를 검색한 후에는 Fabric CA 클라이언트를 사용하여 몇 가지 추가적인 작동 단계를 완료해야 합니다.

1. [CA를 사용하여 새 컴포넌트를 등록](#register-component)하십시오.

2. 또한 [컴포넌트 관리자를 등록](#register-admin)한 후 MSP 폴더 내에서 [컴포넌트 관리자의 인증서를 생성](#enroll-admin)해야 합니다. 다른 컴포넌트를 배치하기 위해 이미 관리자를 등록한 경우 이 단계를 완료할 필요가 없습니다.

3. [TLS CA를 사용하여 새 컴포넌트를 등록](#tls-register-component)하십시오.

### CA를 사용하여 컴포넌트 ID 등록
{: #register-component}

순서 지정 서비스를 배치한 후 해당 서비스에 조직을 추가하여 컨소시엄을 작성하거나 피어를 배치한 후 해당 피어를 채널에 배치하려는 경우 먼저 CA를 사용하여 컴포넌트 ID를 등록해야 합니다. 이 경우 컴포넌트 배치 시 네트워크에 참여할 피어 또는 순서 지정자에게 필요한 인증서가 생성됩니다.

1. Fabric CA 클라이언트를 사용하여 [CA 관리자로 인증서를 생성](#enroll-ca-admin)하십시오. 이 관리자 인증서를 사용하여 다음 명령을 실행하십시오. `$FABRIC_CA_CLIENT_HOME`이 `$HOME/fabric-ca-client/ca-admin`으로 설정되어 있는지 확인하십시오.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. 다음 명령을 실행하여 소속 및 조직 이름을 찾으십시오.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  이 명령은 다음 예제와 유사합니다.
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  다음 예제와 비슷한 정보가 표시되어야 합니다.
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  두 번째 **affiliation** 값(예: `org1.department1`)을 기록해 두십시오. 아래 명령에서 이 값을 사용해야 합니다.

3. 다음 명령을 실행하여 순서 지정자 또는 피어를 등록하십시오.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  컴포넌트의 이름 및 비밀번호를 작성한 후 해당 이름 및 비밀번호를 사용하여 `name` 및 `secret`을 대체하십시오. **중요:** 이 정보를 기록해 두십시오. `--id.type`은 순서 지정자를 배치하는 경우 `orderer`로 설정하고 피어를 배치하는 경우 `peer`로 설정하십시오. 이 명령은 다음 예제와 유사합니다.

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer --id.secret peerpw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  이 명령을 실행한 후에는 구성 파일의 `"component"` 섹션에 있는 `name` 및 `secret`을 `"enrollid"` 및 `"enrollsecret"`으로 입력해야 합니다.

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peerpw",
  ```

  ID는 한 번만 등록할 수 있습니다. 문제점이 발생하면 새 사용자 이름과 비밀번호를 사용하여 명령을 시도하십시오. 보안 조치로서 enrollID 및 시크릿과 함께 각각의 ID를 사용하여 하나의 피어만 배치하십시오. 여러 컴포넌트에 대해 하나의 **관리자** ID를 사용할 수 있지만(권장하는 배치 전략임) 피어 ID 및 비밀번호를 재사용하지 마십시오.

  명령이 완료되면 다음 예제와 비슷한 정보가 표시되어야 합니다.

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### 관리자 등록
{: #register-admin}

컴포넌트를 등록한 후에는 컴포넌트를 작동시키기 위해 사용할 수 있는 관리자 ID도 작성해야 합니다. 먼저 CA를 사용하여 이 새 ID를 등록한 후 해당 ID를 사용하여 MSP 폴더를 생성하십시오. 그런 다음 구성 파일에 배치 중에 순서 지정자 또는 피어의 관리자 인증서로 사용되는 관리자 signCert를 추가하십시오. 이 경우 관리자 ID의 인증서를 사용하여 블록체인 네트워크를 작동시킬 수 있습니다(예: 새 채널 시작 또는 피어에 체인코드 설치).

조직에 속한 컴포넌트에 대해 하나의 관리자 ID만 작성할 수 있습니다. 복수의 피어 또는 순서 지정자를 배치하는 경우 이 단계를 한 번만 완료하면 됩니다. 하나의 컴포넌트에 대해 생성된 signCert를 사용하여 모든 피어 또는 순서 지정자를 배치할 수 있습니다.

`$FABRIC_CA_CLIENT_HOME`이 CA 관리자의 MSP에 대한 경로로 설정되어 있는지 확인하십시오.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

다음 명령을 실행하여 CA를 통해 컴포넌트 관리자 ID를 등록하십시오.

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

관리자에 대한 새 사용자 ID의 `name` 및 `secret`을 작성하십시오. 이 ID는 배치하는 모든 컴포넌트에 대한 관리자가 됩니다. 방금 등록한 피어 또는 순서 지정자 ID와 다른 값을 사용해야 합니다. 이 명령은 다음 예제와 유사합니다.

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**중요:** 이 정보를 기록해 두십시오. `enroll` 명령을 사용하여 MSP 폴더를 생성하기 위해 이 `name` 및 `secret`을 사용합니다.

#### 관리자 MSP 폴더 생성
{: #enroll-admin}

컴포넌트 관리자를 등록한 후에는 다음 명령을 사용하여 인증서를 생성할 수 있습니다.

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

예를 들어, 다음과 같습니다.

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

이 명령이 정상적으로 완료되면 `-M` 플래그를 사용하여 지정한 디렉토리에 새 MSP 폴더가 생성됩니다. 이 디렉토리에는 컴포넌트를 작동시키기 위해 사용할 인증서가 포함되어 있습니다. tree 명령을 사용하여 enroll 명령이 올바르게 작동했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. tree 명령에서 다음 구조와 유사한 결과를 생성해야 합니다.

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
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
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

이 MSP 디렉토리에서 새 사용자의 signCert 파일을 열고 다음 명령을 사용하여 base64 형식으로 변환하십시오.

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**참고:** 상기 명령을 사용하여 생성된 문자열은 단일 행으로 형식화되어야 합니다. 다음과 유사하게 표시되어야 합니다.

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
 ```
다음 문자열처럼 표시되어서는 안됩니다.

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
 ```

예를 들어, 다음과 같습니다.

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

이 명령은 다음 예제와 유사한 문자열을 인쇄합니다.

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

이 문자열을 구성 파일의 컴포넌트 섹션에 있는 `"admincerts"` 필드에 복사하십시오.

#### TLS CA를 사용하여 컴포넌트 ID 등록
{: #tls-register-component}

TLS CA를 사용하여 순서 지정자 또는 피어를 등록하는 작업도 수행해야 합니다. 이 작업을 수행하려면 먼저 TLS CA의 관리자를 사용하여 등록해야 합니다. `$FABRIC_CA_CLIENT_HOME`을 TLS CA 관리자 인증서를 저장할 디렉토리로 변경하십시오.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

다음 명령을 실행하여 TLS CA에 대한 관리자로 등록하십시오. 이 명령은 [CA 관리자](#enroll-ca-admin)로 등록하기 위해 사용한 명령과 동일하지만 [CA 구성](CA_deploy_icp.html#icp-ca-configuration-parms) 중에 지정한 CA TLS 인스턴스 이름만 사용합니다. CA 시크릿에서 동일한 `ca-admin-name` 및 `ca-admin-password`를 사용하는지 확인하십시오.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

실제 호출은 다음 예제와 유사합니다.

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

등록한 후에는 TLS CA를 사용하여 컴포넌트를 등록하기 위해 필요한 인증서를 보유하게 됩니다. 다음 명령을 실행하여 순서 지정자 또는 피어를 등록하십시오.

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

이 명령은 TLS CA 이름을 사용하는 점을 제외하면 CA를 사용하여 컴포넌트 ID를 등록하기 위해 사용한 명령과 유사합니다. 피어가 아닌 순서 지정자를 배치하는 경우 `--id.type`을 `peer`가 아닌 `orderer`로 설정하십시오. 이 ID에는 기본 CA에 대해 사용한 것과 다른 사용자 이름 및 비밀번호를 지정해야 합니다. 실제 등록은 다음 명령과 유사합니다.

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

`name` 및 `secret`의 값을 구성 파일의 `"tls"` 섹션에 있는 `"enrollid"` 및 `"enrollsecret"`에 복사하십시오.

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### 인증서 서명 요청(CSR) 호스트
{: #csr-hosts}

순서 지정자 또는 피어를 배치하려면 CSR 호스트 이름을 제공해야 합니다. 이 호스트 이름에는 컴포넌트를 배치할 클러스터의 프록시 IP 주소 및 Helm 차트를 배치할 때 생성된 `서비스 호스트 이름`이 포함되어 있습니다.

#### 클러스터 프록시 IP 주소 값 찾기

순서 지정자 또는 피어를 CA를 배치한 것과 동일한 ICP 클러스터에 배치하려는 경우 순서 지정자 또는 피어를 배치할 ICP 클러스터에 [클러스터 관리자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "클러스터 관리자 역할 및 조치") 역할이 있는지 확인하십시오. 그런 다음 [CA를 구성](CA_deploy_icp.html#icp-ca-configuration-parms)할 때 사용한 것과 동일한 프록시 IP를 입력하십시오. 순서 지정자 또는 피어를 다른 클러스터에 배치하려는 경우 다음 단계를 완료하여 ICP 콘솔에서 클러스터 프록시 IP 주소의 값을 검색할 수 있습니다.

1. ICP 콘솔에 로그인하십시오. 왼쪽 탐색 패널에서 **플랫폼**을 클릭한 후 **노드**를 클릭하여 클러스터에 정의되어 있는 노드를 표시하십시오.
2. 역할이 `프록시`인 노드를 클릭한 후 테이블에서 `호스트 IP`의 값을 복사하십시오.
3. 다음 구성 파일의 `"csr"` 섹션에서 `"hosts"`의 값으로 `호스트 IP`를 삽입하십시오.

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### 서비스 호스트 이름 판별

`서비스 호스트 이름`은 Helm 차트를 배치할 때 생성됩니다. 이 이름은 Helm 차트를 배치할 때 사용하는 `Helm 릴리스 이름`을 기반으로 합니다.

- 순서 지정자를 배치하는 경우 `서비스 호스트 이름`은 배치 중에 끝 부분에 `-orderer` 문자열을 추가하여 지정한 `helm_release_name`입니다. 예를 들어 클러스터 IP 프록시 주소가 `9.42.134.44`이고 `Helm 릴리스 이름`을 `org1orderer`로 지정하는 경우 파일의 `"csr"` 섹션에 다음과 같이 값을 삽입할 수 있습니다.

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- 피어를 배치하는 경우 `서비스 로스트 이름`은 배치 중에 추가 문자열 없이 지정한 `Helm 릴리스 이름`입니다. 예를 들어 클러스터 IP 프록시 주소가 9.42.134.44"이고 `Helm 릴리스 이름`이 `org1peer1`인 경우 csr "hosts"에 다음과 같이 값을 삽입할 수 있습니다.

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
      "org1peer1"
    ]
  }
  ```
  {:codeblock}

### 구성 파일 완성

상기 단계를 모두 완료한 후 업데이트된 구성 파일은 다음 예제와 유사합니다.

```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer",
            "enrollsecret": "peerpw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}

다른 필드는 공백으로 둘 수 있습니다. 이 파일을 저장하는 경우 [순서 지정자](orderer_deploy_icp.html) 또는 [피어](peer_deploy_icp.html)를 배치할 때 해당 파일을 사용해야 합니다.

## MSP(Membership Service Provider)
{: #msp}

{{site.data.keyword.blockchainfull_notm}} Platform의 컴포넌트에서는 MSP(Membership Services Provider)를 통해 ID를 이용합니다. MSP는 CA에서 발행하는 인증서를 권한 및 역할과 연관시킵니다. MSP에 대한 자세한 정보는 [Hyperledger Fabric 문서의 멤버십 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Hyperledger Fabric 문서의 멤버십 주제")를 참조하십시오.

MSP 폴더에는 Fabric 컴포넌트에서 사용할 구조가 정의되어 있어야 합니다. Fabric CA 클라이언트는 다음과 같은 MSP 폴더를 작성하여 이 구조를 설정합니다.

- **cacerts:** 네트워크의 루트 CA 인증서가 포함되어 있습니다.
- **intermediatecerts:** 신뢰 체인에 있는 모든 중간 CA의 인증서입니다(루트 CA 또는 CA로 되돌아감). 각각의 엔터프라이즈 플랜 조직에는 장애 복구 및 고가용성을 위해 두 개의 중간 CA가 존재합니다.
- **signCerts:** 이 폴더에는 signCert 또는 등록 인증서라고도 하는 공용 서명 인증서가 포함되어 있습니다. 이 인증서는 명령행에서 MSP 디렉토리를 참조하거나 SDK에 사용자 컨텍스트 오브젝트를 빌드할 때 네트워크 호출에 첨부됩니다(예: 체인코드 호출). SDK 또는 명령행에서 네트워크를 운영하려는 경우 이 인증서를 IBP에 업로드할 수 있습니다.
- **keystore:** 이 폴더에는 개인 키가 포함되어 있습니다. 이 키는 명령행에서 MSP 디렉토리를 참조하거나 SDK를 사용하여 사용자 컨텍스트 오브젝트를 빌드하는 경우 네트워크에 대한 호출에 서명하기 위해 사용되지만 signCert와 같은 방법으로 호출에 첨부되지는 않습니다. 이 키를 안전하게 유지하는 것이 **매우 중요**합니다. 손상되는 경우 이 키를 사용하여 ID를 위장할 수 있습니다.

네트워크 모니터 및 Swagger API를 사용하여 fabric-ca-client에서 참조할 MSP 폴더를 빌드할 수도 있습니다.

- **cacerts** 및 **intermediatecerts**: MSP API에 대한 `Get` 요청을 실행하여 [Swagger API](swagger_apis.html)를 통해 이러한 인증서를 페치할 수 있습니다.
- **signCerts** 및 **keystore**: "인증 기관" 패널에서 **인증서 생성** 단추를 클릭하여 이러한 인증서를 생성할 수 있습니다. 두 개의 인증서가 나열된 팝업 창이 열립니다. **인증서** 및 **개인 키**를 복사하여 각각 signCert 및 키 저장소에 저장하십시오. 해당 인증서는 플랫폼에 저장되지 않으므로 안전한 위치에 보관하십시오.

많은 Fabric 컴포넌트에는 MSP 폴더에 추가 정보가 포함되어 있습니다. 예를 들어 원격 피어를 운영하는 경우 다음 폴더가 표시될 수 있습니다.

- **admincerts:** 이 폴더에는 이 조직이나 컴포넌트의 관리자 목록이 들어 있습니다. 명령행 또는 SDK에서 원격 피어를 작동시키는 경우 이 폴더에 signCert를 업로드해야 합니다. Fabric CA 클라이언트를 사용하는 경우 관리자 인증서로 인식되는 해당 signCert가 포함된 MSP에 admincerts 폴더도 있어야 합니다.
- **tls:** 다른 네트워크 컴포넌트와 통신하는 데 사용하는 TLS 인증서를 저장하는 폴더입니다.

MSP의 구조에 관한 자세한 정보는 Hyperledger Fabric 문서에서 [멤버십 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "멤버십") 및 [Membership Service Providers ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers")를 참조하십시오.


## CA 로그 보기
{: #ca-operate-view-logs}

컴포넌트 로그는 명령행에서 [`kubectl CLI 명령`](#ca-kubectl-configure)을 사용하여 확인하거나 ICP 클러스터에 포함된 [Kibana ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.elastic.co/products/kibana "Elastic Search에 대한 창")를 통해 확인할 수 있습니다.

- 팟(Pod) 내에 있는 컨테이너 로그를 확인하려면 `kubectl logs` 명령을 사용하십시오. 팟(Pod) 이름이 불확실한 경우 다음 명령을 실행하여 팟(Pod) 목록을 확인하십시오.

   ```
   kubectl get pods
   ```
   {:codeblock}

   그런 다음 `pod_name`을 상기 명령 출력의 팟(Pod) 이름으로 대체하고 다음 명령을 실행하여 팟(Pod) 내에 상주하는 CA 컨테이너에 대한 로그를 검색하십시오.

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   `kubectl logs` 명령에 대한 자세한 정보는 [Kubernetes 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)를 참조하십시오.

- 또는 Kibana에서 로그를 여는 [ICP 클러스터 관리 콘솔](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html)을 사용하여 배치 이벤트 및 로그에 액세스할 수 있습니다.

  **참고:** Kibana에서 로그를 확인하는 경우 `No results found`라는 응답이 수신될 수 있습니다. ICP에서 해당 호스트 이름으로 작업자 노드 IP 주소를 사용하는 경우 이 상태가 발생할 수 있습니다. 이 문제점을 해결하려면 패널의 맨 위에서 `node.hostname.keyword`로 시작되는 필터를 제거하면 로그가 표시됩니다.

## 문제점 해결
{: #ca-operate-troubleshooting}

### **문제점:** `enroll` 명령을 실행할 때 오류가 발생합니다.
{: #ca-enroll-error}

Fabric CA 클라이언트 enroll 명령을 실행할 때 다음 오류로 인해 명령이 실패할 수 있습니다.

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**솔루션:**

이 오류는 Fabric CA 클라이언트에서 등록하려고 시도하지만 CA에 연결할 수 없는 경우에 발생할 수 있습니다. 다음과 같은 상황에서 이 문제가 발생할 수 있습니다.   

- `enroll` 명령의 `-u` 매개변수에 추가적인 `https://`가 포함되어 있습니다.
- CA 이름이 올바르지 않습니다.
- 사용자 이름 또는 비밀번호가 올바르지 않습니다.

`enroll` 명령에 지정된 매개변수를 검토하여 이러한 조건이 존재하지 않는지 확인하십시오.

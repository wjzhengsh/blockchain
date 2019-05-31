---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: APIs, build a network, authentication, service credentials, API key, API endpoint, IAM access token, Fabric CA client, import a network, generate certificates

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:curl: #curl .ph data-hd-programlang='curl'}

# API를 사용하여 네트워크 빌드
{: #ibp-v2-apis}

{{site.data.keyword.blockchainfull}} Platform은 블록체인 컴포넌트를 작성하고, 가져오고, 편집하고, 삭제하며 로깅, 알림 및 콘솔 설정을 관리하기 위한 RESTful API를 노출합니다. API 및 해당 SDK를 사용하여 블록체인 네트워크와 상호작용하는 애플리케이션을 개발할 수 있습니다.
{: shortdesc}

이 튜토리얼에서는{{site.data.keyword.blockchainfull_notm}} Platform API를 사용하여 블록체인 네트워크를 빌드하는 일반 플로우를 소개합니다. 각 API에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform API 참조 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform API 참조 문서"){: new_window}를 참조하십시오.

이러한 API는 {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} v0.1.77 이상과 호환 가능합니다. 버전을 확인하려면 `https://[your_console_url]/version.txt`를 찾아보십시오. 여기서 *`[your_console_url]`*은 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔의 URL입니다(예: https://1ee1869ffa6496d6bc1ce4b-optools.bp01.blockchain.cloud.ibm.com/version.txt).
{:note}

## 시작하기 전에
{: #ibp-v2-apis-prereq}

API 호출을 발행하여 네트워크와 상호작용할 수 있도록 {{site.data.keyword.cloud_notm}}에 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스가 있어야 합니다. 아직 서비스 인스턴스가 없는 경우 [시작하기 지시사항](/docs/services/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks)에 따라 작성하십시오.

## 인증
{: #ibp-v2-apis-authentication}

API를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform으로 작성한 블록체인 네트워크에 액세스하려면 애플리케이션이 {{site.data.keyword.cloud_notm}}에 인증하고 서비스 인스턴스에 연결할 수 있어야 합니다.

### 서비스 인증 정보 검색
{: #ibp-v2-apis-retrieve-service-credentials}

{{site.data.keyword.cloud_notm}}의 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스에 액세스할 수 있으려면 기본 인증 정보가 필요합니다. 

1. [{{site.data.keyword.cloud_notm}} 리소스 목록 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/resources)에서 사용자가 작성한 블록체인 서비스 인스턴스를 여십시오.
2. 왼쪽 네비게이터에서 **서비스 인증 정보**를 클릭하십시오.
3. **서비스 인증 정보** 페이지에서 "새 인증 정보" 단추를 클릭하여 새 인증 정보를 작성하십시오.
  1. 인증 정보 이름(예: *UseAPIs*)을 제공하십시오.
  2. "인라인 구성 매개변수 추가" 필드를 공백으로 둘 수 있습니다.
  3. **추가** 단추를 클릭하십시오.
4. 새 인증 정보가 작성된 후 이 인증 정보의 **조치** 헤더 아래에서 **인증 정보 보기**를 클릭하십시오. 인증 정보의 컨텐츠는 다음 예제와 유사합니다.

  ```
  {
    "api_endpoint": "https://1088ac8563e44f5a92539d946733ad7e-optools.so01.blockchain.test.cloud.ibm.com"
    "apikey": "nvASKts6B6lMZGZUNTGVP7MLK2BujMnxz0plSPYaqc3R",
    "configtxlator": "https://1088ac8563e44f5a92539d946733ad7e-configtxlator.so01.blockchain.test.cloud.ibm.com",
    "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:blockchain:us-south:a/9d46037caee397faa45c55e087d26baa:1088ac85-63e4-4f5a-9253-9d946733ad7e::",
    "iam_apikey_name": "auto-generated-apikey-c1981f5c-cff0-464f-9a78-ed2f52e24d1a",
    "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
    "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/9d46037caee397faa45c55e087d26baa::serviceid:ServiceId-774190c5-9c37-4f68-8572-8d6e2aabbc7e",
  }
  ```

서비스 인증 정보에서 {{site.data.keyword.iamshort}}(IAM) 액세스 토큰을 검색하는 데 필요한 **API 키**(`apikey`) 및 **API 엔드포인트**(`api_endpoint`)를 찾을 수 있습니다.

### 액세스 토큰 검색
{: #ibp-v2-apis-retrieve-token}

IAM에서 액세스 토큰을 검색하여 {{site.data.keyword.blockchainfull_notm}} 플랫폼에 인증할 수 있습니다. 액세스 토큰을 사용하면 개인 사용자 인증 정보를 공유할 필요 없이 {{site.data.keyword.cloud_notm}} 내부 또는 외부에서 서비스 또는 애플리케이션 대신 {{site.data.keyword.blockchainfull_notm}} Platform API에 대해 작업할 수 있습니다.  

{{site.data.keyword.iamshort}} API를 호출하여 액세스 토큰을 검색하십시오.

```cURL
curl -X POST \
  "https://iam.cloud.ibm.com/identity/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  -d "grant_type=urn%3Aibm%3Aparams%3Aoauth%3Agrant-type%3Aapikey&apikey=<API_KEY>" \
```
{: codeblock}

이 요청에서 `<API_KEY>`를 서비스 인증 정보의 `apikey` 값으로 바꾸십시오. 다음의 잘려진 예는 토큰 출력을 보여줍니다.

```
{
"access_token": "eyJraWQiOiIyM...",
"refresh_token": "...",
"expires_in":3600,
"expiration":1555558683,
"scope":"ibm openid"}
```
{: screen}

API를 사용하여 블록체인 네트워크에 대해 프로그래밍 방식으로 작업하려면 `Bearer` 토큰 유형이 앞에 붙은 전체 `access_token` 값을 사용하십시오.

액세스 토큰은 1시간 동안 유효하지만 필요에 따라 재생성할 수 있습니다. 서비스에 대한 액세스를 유지보수하려면 {{site.data.keyword.iamshort}} API를 호출하여 정기적으로 API 키에 대한 액세스 토큰을 새로 고치십시오.   
{: tip}

## API 요청 구성
{: #ibp-v2-apis-form-api-request}

서비스에 대한 API 호출을 작성할 때 처음에 {{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스를 프로비저닝한 방법에 대한 API 요청을 구조화하십시오.

요청을 빌드하려면 다음과 같은 형식으로 API 엔드포인트를 적절한 인증 정보와 쌍으로 묶으십시오.

```cURL
curl -X <API method> \
    <API_endpoint>/<API> \
    -H 'authorization: Bearer <access_token>' \
    -H 'Content-Type: application/json' \
    -d '<request body>' \
```
{: codeblock}

[{{site.data.keyword.blockchainfull_notm}} Platform API 참조 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform API 참조 문서")에서 각 API에 대한 예제 curl 명령이 제공됩니다.

또한 API 참조 문서에서 **시험 사용** 기능을 사용하여 API 호출을 애플리케이션에 추가하기 전에 테스트할 수 있습니다. **시험 사용** 기능을 사용하려면 {{site.data.keyword.cloud_notm}}에 로그인되어 있어야 합니다. 드롭 다운 목록에서 임의의 서비스 인스턴스를 선택할 수 있습니다. 모든 API 요청이 API 엔드포인트에 지정된 네트워크에 전송됩니다.

## 제한사항
{: #ibp-v2-apis-limitations}

{{site.data.keyword.cloud_notm}} 네트워크의 다른 {{site.data.keyword.blockchainfull_notm}} Platform에서 기존 CA, 피어 및 순서 지정자 노드만 가져올 수 있습니다.

## API를 사용하여 네트워크 빌드
{: #ibp-v2-apis-build-with-apis}

API를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform의 인스턴스에 블록체인 컴포넌트를 작성할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} API를 사용하여 블록체인 네트워크를 빌드하려면 다음 단계를 사용하십시오.

1. [`POST /ak/api/v1/kubernetes/components/ca`](/apidocs/blockchain?code=try#create-a-ca)를 호출하여 인증 기관(CA)을 작성하십시오.

  나중에 필요하므로 입력 및 응답을 기억하십시오.
  {: tip}

  CA가 시작될 때까지 대기해야 합니다. 환경에 따라 몇 분이 걸릴 수 있습니다. `GET <ca_url>/cainfo` API를 호출하여 CA 상태를 확인할 수 있습니다. 반복된 오류가 발생하면 다음 단계로 진행할 수 있음을 의미하는 `200` 상태 코드가 표시됩니다. 이 API 호출은 1분 후에 제한시간 초과됩니다.

2. CA를 사용하여 컴포넌트 및 관리자 ID를 등록하고 필요한 인증서를 생성하십시오. Fabric CA 클라이언트를 사용하여 다음 단계를 완료할 수 있습니다.

  - [CA 클라이언트를 설정](#ibp-v2-apis-config-fabric-ca-client)하십시오.
  - [CA 관리자로 인증서를 생성](#ibp-v2-apis-enroll-ca-admin)하십시오.
  - [CA를 사용하여 새 컴포넌트를 등록](#ibp-v2-apis-config-register-component)하십시오.
  - 또한 [조직 관리자를 등록](#ibp-v2-apis-config-register-admin)한 후 MSP 폴더 내부에서 [관리자의 인증서를 생성](#ibp-v2-apis-config-enroll-admin)해야 합니다. 관리자 ID를 이미 등록한 경우 이 단계를 완료할 필요가 없습니다.
  - [TLS CA를 사용하여 새 컴포넌트를 등록](#ibp-v2-apis-config-register-component-tls)하십시오.

  {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 이러한 단계를 완료할 수도 있습니다. 자세한 정보는 [ID 작성 및 관리](/docs/services/blockchain/howto/ibp-console-identities.html)를 참조하십시오.

3. [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?#import-a-membership-service-provide-msp)를 호출하여 [조직에 대한 MSP 정의를 작성](#ibp-v2-apis-msp)하십시오.

4. 순서 지정자 또는 피어를 작성하는 데 필요한 [구성 파일을 빌드](#ibp-v2-apis-config)하십시오. 작성할 각 순서 지정자 또는 피어마다 고유한 구성 파일을 빌드해야 합니다.

5. [`POST /ak/api/v1/kubernetes/components/orderer`](/apidocs/blockchain?code=try#create-an-orderer)를 호출하여 순서 지정자를 작성하십시오.

6. [`POST /ak/api/v1/kubernetes/components/peer`](/apidocs/blockchain?code=try#create-a-peer)를 호출하여 피어를 작성하십시오.

7. 콘솔을 사용하여 블록체인 컴포넌트를 작동시키려면 관리자 ID를 콘솔 지갑으로 가져와야 합니다. 지갑 탭을 사용하여 노드 관리자의 인증서 및 개인 키를 콘솔로 가져오고 ID를 작성하십시오. 그런 다음 콘솔을 사용하여 이 ID를 사용자가 작성한 컴포넌트와 연관시켜야 합니다. 자세한 정보는 [관리자 ID를 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔로 가져오기](#ibp-v2-apis-admin-console)를 참조하십시오.

8. 네트워크를 배치한 후 Fabric SDK, 피어 CLI 또는 콘솔 UI를 사용하여 채널을 작성하고 스마트 계약을 설치하거나 인스턴스화할 수 있습니다.

컴포넌트를 작성할 수 있으려면 API 인증에 사용되는 서비스 인증 정보에 IAM의 `Manager` 역할이 있어야 합니다. 자세한 정보는 [사용자 역할](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)에 대한 주제의 표를 참조하십시오.
{: note}

## API를 사용하여 네트워크 가져오기
{: #ibp-v2-apis-import-with-apis}

API를 사용하여 API 또는 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 통해 작성된 {{site.data.keyword.blockchainfull_notm}} 컴포넌트를 {{site.data.keyword.blockchainfull_notm}} Platform 2.0의 다른 서비스 인스턴스로 가져올 수도 있습니다.

1. [`POST /ak/api/v1/components/ca`](/apidocs/blockchain?code=try#import-a-ca)를 호출하여 CA를 가져오십시오.

  나중에 필요하므로 입력 및 응답을 기억하십시오.
  {: tip}

  CA가 시작될 때까지 대기해야 합니다. 환경에 따라 몇 분이 걸릴 수 있습니다.  [`GET /components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components)를 호출하여 CA 상태를 확인할 수 있습니다. 다음 단계로 이동하기 위해 `200` 상태 코드를 얻기 전에 반복된 오류가 발생합니다. 이 API 호출은 1분 이내에 제한시간 초과됩니다.

2. [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp)를 호출하여 조직 MSP 정의를 가져오십시오.

3. [`POST /ak/api/v1/components/orderer`](/apidocs/blockchain?code=try#import-a-orderer)를 호출하여 순서 지정자를 가져오십시오.

4. [`POST /ak/api/v1/components/peer`](/apidocs/blockchain?code=try#import-a-peer)를 호출하여 피어를 가져오십시오.

5. {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 블록체인 컴포넌트를 작동시키려면 컴포넌트 관리자 ID를 콘솔 지갑으로 가져와야 합니다. 자세한 정보는 [관리자 ID를 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔로 가져오기](#ibp-v2-apis-admin-console)를 참조하십시오.

6. 네트워크를 배치한 후 Fabric SDK, 피어 CLI 또는 콘솔 UI를 사용하여 채널을 작성하고 스마트 계약을 설치하거나 인스턴스화할 수 있습니다.

컴포넌트를 가져올 수 있으려면 API 인증에 사용되는 서비스 인증 정보에 IAM의 `Writer` 역할이 있어야 합니다. 자세한 정보는 [사용자 역할](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove)에 대한 주제의 표를 참조하십시오.
{: note}

## Fabric CA 클라이언트를 사용하여 CA 작동
{: #ibp-v2-apis-config-fabric-ca-client}

Fabric CA 클라이언트를 사용하여 CA를 작동시킬 수 있습니다. 다음 Fabric CA 클라이언트 명령을 사용하여 컴포넌트 및 관리자 ID를 등록하고 필요한 인증서를 생성하십시오.

### Fabric CA 클라이언트 설정
{: #ibp-v2-apis-setup-fabric-ca-client}

1. 로컬 파일 시스템에 [Fabric CA 클라이언트를 다운로드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Fabric CA 클라이언트 다운로드") 하십시오.

  Fabric CA 클라이언트를 얻는 가장 간단한 방법은 모든 Fabric 도구 바이너리를 직접 다운로드하는 것입니다. 명령행을 사용하여 바이너리를 다운로드하려는 디렉토리로 이동한 후 다음 [Curl ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl") 명령을 실행하여 페치하십시오.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
  ```
  {:codeblock}

  이 명령은 `bin/` 디렉토리에 바이너리를 설치합니다.

2. `PATH` 경로를 Fabric 도구 바이너리를 다운로드한 디렉토리로 설정하십시오.

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  예를 들어, 홈 디렉토리에 바이너리를 설치한 경우 다음과 같이 `PATH`를 설정할 수 있습니다..

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. CA 관리자의 인증서를 저장할 폴더를 작성하십시오.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. `$FABRIC_CA_CLIENT_HOME` 환경 변수의 값을 CA 클라이언트에서 생성된 [MSP](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) 인증서를 저장할 경로로 설정하십시오. 이전 시도에서 작성된 구성 자료를 제거했는지 확인하십시오. `enroll` 명령을 처음 실행하는 경우 `msp` 폴더와 `.yaml` 파일이 없습니다.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Fabric CA 클라이언트에서 사용될 CA의 TLS 인증서를 검색하십시오. {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용 중인 경우 CA를 열고 **설정**을 클릭한 후 **TLS 인증서** 필드에서 base64 형식의 인증서를 찾으십시오. API를 사용 중인 경우 [`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components)를 호출하고 `"PEM"` 필드에서 CA TLS 인증서를 찾을 수 있습니다. `Fabric CA 작성` API를 사용하여 CA를 작성한 경우 응답 본문에서 TLS 인증서를 찾을 수도 있습니다.

  CA와 통신하는 데 사용하려면 base64 형식의 인증서를 PEM 형식으로 변환해야 합니다. 아래 명령에 TLS 인증서의 base64 인코딩 문자열을 삽입하십시오. `$HOME/fabric-ca-client` 디렉토리에 있는지 확인하십시오.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > tls.pem
  ```
  {:codeblock}

### CA 관리자로 인증서 생성
{: #ibp-v2-apis-enroll-ca-admin}

**CA 관리자** ID는 CA를 작성할 때 자동으로 등록되었습니다. 이제 해당 관리자 이름 및 비밀번호를 사용하여 Fabric CA 클라이언트에 `enroll` 명령을 실행하여 이후 다른 피어 또는 순서 지정자 ID를 등록하는 데 사용될 수 있는 인증서가 포함된 MSP 폴더를 생성할 수 있습니다.

1. [Fabric CA 클라이언트를 설정](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)하는 단계를 완료했으며 `$FABRIC_CA_CLIENT_HOME`이 CA 관리자 인증서를 저장할 디렉토리로 설정되어 있는지 확인하십시오.

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

  이 명령의 `<enroll_id>` 및 `<enroll_password>`는 CA를 작성할 때 지정한 `enroll_id` 및 `enroll_secret`입니다. {{site.data.keyword.blockchainfull_notm}} Platform 콘솔의 **인증 기관 엔드포인트 URL** 또는 API 호출에서 `<ca_url_with_port>`에 대한 값으로 리턴된 `"ca_url"`을 사용하십시오. 시작 부분에 `http://`를 포함하지 않아야 합니다. `<ca_name>`은 콘솔의 **CA 이름** 또는 API에서 리턴된 `"ca_name"`입니다.

  `<ca_tls_cert_path>`는 CA TLS 인증서의 전체 경로입니다.

  실제 호출은 다음 예제 명령과 비슷하게 보일 수 있습니다.

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```  
  {:codeblock}

**팁:** `-u` 매개변수값인 등록 URL 값에 특수 문자가 포함된 경우 특수 문자를 인코딩하거나 URL을 작은따옴표로 묶어야 합니다. 예를 들어, `!`가 `%21`이 되거나 다음과 유사하게 표시됩니다.

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname ca
  ```
  {:codeblock}

  `enroll` 명령은 Fabric CA 클라이언트에 대한 `$HOME` 경로로 설정한 디렉토리 내에 있는 MSP(Membership Service Provider) 폴더로 알려진 전체 인증서 세트를 생성합니다. 예를 들면 `$HOME/fabric-ca-client/ca-admin`입니다. MSP에 대한 자세한 정보 및 MSP 폴더에 포함되는 항목은 [MSP(Membership Service Provider)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)를 참조하십시오.

  `enroll` 명령이 실패하는 경우 가능한 원인은 [문제점 해결 주제](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting)를 참조하십시오.

  tree 명령을 실행하여 모든 전제조건 단계를 완료했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. 트리 명령은 다음 구조와 유사한 결과를 생성해야 합니다.

  ```
cd $HOME/fabric-ca-client
tree
.
  ├── ca-admin
  │   ├── fabric-ca-client-config.yaml
  │   └── msp
  │       ├── cacerts
  │       │   └── 9-30-250-70-30395-SampleOrgCA.pem
  │       ├── IssuerPublicKey
  │       ├── IssuerRevocationPublicKey
  │       ├── keystore
  │       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
  │       ├── signcerts
  │       │   └── cert.pem
  │       └── user
  └── catls
      └── tls.pem    
  ```

### CA를 사용하여 컴포넌트 ID 등록
{: #ibp-v2-apis-config-register-component}

먼저 CA로 컴포넌트 ID를 등록해야 합니다. 컴포넌트는 이 ID를 사용하여 배치 시 필요한 인증서를 생성합니다.

1. Fabric CA 클라이언트를 사용하여 [CA 관리자로 인증서를 생성](#ibp-v2-apis-enroll-ca-admin)하십시오. 이 관리자 인증서를 사용하여 다음 명령을 실행하십시오. `$FABRIC_CA_CLIENT_HOME`이 `$HOME/fabric-ca-client/ca-admin`으로 설정되어 있는지 확인하십시오.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. 다음 명령을 실행하여 소속과 조직 이름을 찾으십시오.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  명령은 다음 예제와 같을 수 있습니다.
  ```
  fabric-ca-client affiliation list --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
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

  컴포넌트의 이름 및 비밀번호를 작성한 후 해당 이름 및 비밀번호를 사용하여 `name` 및 `secret`을 대체하십시오.  이 정보를 기록하십시오. `--id.type`은 순서 지정자를 배치하는 경우 `orderer`로 설정하고 피어를 배치하는 경우 `peer`로 설정하십시오. 이 명령은 다음 예제와 유사합니다.

  ```
  fabric-ca-client register --caname ca --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  구성 파일을 작성할 때를 대비하여 위의 명령의 `"enrollid"` 및 `"enrollsecret"`을 저장해야 합니다.
{: important}

  ID는 한 번만 등록할 수 있습니다. 문제점이 발생하면 새 사용자 이름과 비밀번호를 사용하여 명령을 시도하십시오. 보안 조치로 각 ID 및 수반하는 enrollID 및 secret을 사용하여 하나의 피어만 배치하십시오. 여러 컴포넌트에 대해 하나의 **관리자** ID를 사용할 수 있지만(권장하는 배치 전략임) 피어 ID 및 비밀번호를 재사용하지 마십시오.

  명령이 완료되면 다음 예제와 비슷한 정보가 표시되어야 합니다.

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

### 조직 관리자 등록
{: #ibp-v2-apis-config-register-admin}

네트워크를 조작하는 데 사용할 수 있는 관리자 ID도 작성해야 합니다. 이 ID를 사용하여 특정 컴포넌트를 조작합니다(예: 피어에 스마트 계약 설치). 이 ID를 조직의 관리자로 사용하고 이 ID를 사용하여 채널을 작성하고 편집할 수도 있습니다.  

CA를 사용하여 이 새 ID를 등록하고 이 ID를 사용하여 MSP 폴더를 생성해야 합니다. 해당 signCert를 조직 MSP에 추가하여 이 ID를 조직 관리자로 설정할 수 있습니다. 또한 배치 중에 순서 지정자 또는 피어의 관리자 인증서가 될 수 있도록 signCert를 구성 파일에 추가해야 합니다. 조직에 대한 하나의 관리자 ID만 작성해야 합니다. 결과적으로 이러한 단계를 한 번만 완료해야 합니다. 사용자가 생성한 signCert를 사용하여 많은 피어 또는 순서 지정자를 배치할 수 있습니다.

`$FABRIC_CA_CLIENT_HOME`이 CA 관리자의 MSP에 대한 경로로 설정되어 있는지 확인하십시오.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

다음 명령을 실행하여 CA를 통해 컴포넌트 관리자 ID를 등록하십시오.

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type client --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

관리자에 대한 새 사용자 ID의 `name` 및 `secret`을 작성하십시오. 방금 등록한 피어 또는 순서 지정자 ID와 다른 값을 사용해야 합니다. 이 명령은 다음 예제와 유사합니다:

```
fabric-ca-client register --caname ca --id.name peeradmin --id.affiliation org1.department1 --id.type client --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

이 정보를 기록하십시오. `enroll` 명령을 사용하여 MSP 폴더를 생성하기 위해 이 `name` 및 `secret`을 사용합니다.
{: important}

### 관리자 MSP(Membership Service Provider) 폴더 생성
{: #ibp-v2-apis-config-enroll-admin}

컴포넌트 관리자를 등록한 후 다음 명령을 실행하여 인증서를 생성할 수 있습니다.

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

예를 들어, 다음과 같습니다.

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname ca -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```
{:codeblock}

이 명령이 정상적으로 완료되면 `-M` 플래그를 사용하여 지정한 디렉토리에 새 MSP 폴더가 생성됩니다. 이 디렉토리에는 컴포넌트를 작동하는 데 사용해야 하는 인증서가 포함되어 있습니다. tree 명령을 사용하여 enroll 명령이 올바르게 작동했는지 확인할 수 있습니다. 인증서를 저장한 디렉토리로 이동하십시오. 트리 명령은 다음 구조와 유사한 결과를 생성해야 합니다.


```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
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
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

조직 MSP 정의 및 구성 파일을 작성할 때 이 폴더로 돌아가야 합니다.
{: important}

### TLS CA를 사용하여 컴포넌트 ID 등록
{: #ibp-v2-apis-config-register-component-tls}

CA를 작성할 때 TLS CA가 기본 CA와 함께 배치되었습니다. 또한 TLS CA를 사용하여 순서 지정자 또는 피어를 등록해야 합니다. 이를 수행하려면 먼저 TLS CA의 관리자를 사용하여 등록해야 합니다. `$FABRIC_CA_CLIENT_HOME`을 TLS CA 관리자 인증서가 저장될 디렉토리로 변경하십시오.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

다음 명령을 실행하여 TLS CA에 대한 관리자로 등록하십시오. TLS CA 관리자의 등록 ID 및 비밀번호는 기본 CA와 동일합니다. 따라서 아래 명령은 [CA 관리자](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin)로 등록하는 데 사용한 것과 동일하며 여기에 TLS CA 이름만 사용합니다. TLS CA 이름은 콘솔의 CA **설정** 패널에 있는 **TLS CA 이름** 값 또는 `CA 작성` API에서 리턴된 `"tlsca_name"` 값입니다. 

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

실제 호출은 다음 예제와 비슷하게 보일 수 있습니다.

```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

등록한 후에는 TLS CA를 사용하여 컴포넌트를 등록하기 위해 필요한 인증서를 보유하게 됩니다. 다음 명령을 실행하여 순서 지정자 또는 피어를 등록하십시오.

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

이 명령은 TLS CA 이름을 사용해야 한다는 점을 제외하면 CA를 사용하여 컴포넌트 ID를 등록하는 데 사용한 명령과 유사합니다. 피어가 아닌 순서 지정자를 배치하는 경우 `--id.type`을 `peer`가 아닌 `orderer`로 설정하십시오. 이 ID에는 기본 CA에 대해 사용한 것과 다른 사용자 이름 및 비밀번호를 제공해야 합니다. 실제 등록은 다음 명령과 유사합니다.

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

구성 파일을 작성할 때를 대비하여 위의 명령의 `"enrollid"` 및 `"enrollsecret"`을 저장해야 합니다.
{: important}

## 조직 MSP 정의 작성
{: #ibp-v2-apis-msp}

API를 통해 [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp)를 호출하여 조직 MSP 정의를 작성할 수 있습니다. 이 MSP에는 블록체인 컨소시엄에서 조직을 정의하는 인증서와 네트워크를 조작하는 데 사용될 수 있는 관리자 인증서가 포함됩니다. 위의 단계를 따른 경우 조직 MSP를 작성하는 데 필요한 인증서를 이미 생성했습니다. 다음 단계를 사용하여 API 호출의 요청 본문을 완료하십시오.

1. `msp`를 요청 `type`으로 입력하십시오.

2. 조직의 MSP ID를 선택하십시오. MSP ID는 컨소시엄 내에서 조직의 정규 이름입니다. 조직 MSP을 작성하는 데 사용되는 MSP ID는 피어를 배치하는 데 사용하는 것과 동일해야 합니다.

3. MSP에 대한 표시 이름을 선택하십시오.

4. Fabric CA 클라이언트를 사용하여 등록한 조직 관리자의 signCert를 base64 형식으로 제공해야 합니다.  

  [조직 관리자를 사용하여 인증서를 생성](#ibp-v2-apis-config-enroll-admin)할 때 작성된 MSP 디렉토리로 이동하십시오.
  ```
cd $HOME/fabric-ca-client/peer-admin/msp
  ```
이 MSP 디렉토리에서 새 사용자의 signCert 파일을 열고 다음 명령을 사용하여 base64 형식으로 변환하십시오.

  ```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **참고:** 위의 명령으로 생성된 문자열은 하나의 행으로 형식화되어야 합니다. 다음과 유사하게 표시되어야 합니다.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```
   다음과 같이 표시되어서는 안됩니다.

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

  이 문자열을 API 요청의 `admins` 필드에 제공하십시오.

5. CA의 루트 인증서도 제공해야 합니다. 이 인증서는 [CA 관리자를 사용하여 인증서를 생성](#ibp-v2-apis-enroll-ca-admin)할 때 작성되었습니다.

  CA 관리자 MSP 디렉토리로 이동하십시오.
  ```
  cd $HOME/fabric-ca-client/ca-admin/msp
  ```
  이 디렉토리에서 cacerts 폴더를 열고 다음 명령을 사용하여 내부의 인증서를 base64 형식으로 변환하십시오.
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-ca-admin>/msp/cacerts/<ca_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  이렇게 하면 루트 인증서가 base64로 인코딩된 문자열로 인쇄됩니다.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGekNDQWIyZ0F3SUJBZ0lVQmZnZzcvVnIrL25OVEFNQlQ4UUtHL00wQU8wd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU1TURVd016RXpNamt3TUZvWERUTTBNRFF5T1RFek1qa3dNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVXMUtvN2lWeVE2VWkwdDVqbU5KaWVuSUwKR3pNM1BDWHlhL2VSQ0NWMmFQb0dTZ1lrVUg2UWN5RjAzbFlMZFU4Y0drNTQ0alViVC9KT1lYeVgzTWc4bHFORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZDK2lJR0NSb2Zvb3FsVkZoU3dOMmk2MXNJaVBNQW9HQ0NxR1NNNDlCQU1DQTBnQU1FVUNJUURTYW9RL1E0QzkKbFl1VGNhVXVHb3d6YmhUZHBuN2F3S2lHN1Nvd2lSQXVld0lnUWlyM3RNR3IvYWo2aU5lRXJFN2NyOVowQ0gvTwp3QnNQcWd4RVR3MjVqZUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  이 문자열을 API 요청의 `root_certs` 필드에 제공하십시오.

6. TLS CA의 루트 인증서도 제공해야 합니다. TLS 루트 인증서를 사용하면 피어가 채널의 Gossip에 참여할 수 있습니다.

  [TLS CA 관리자를 등록](#ibp-v2-apis-config-register-component-tls)할 때 생성된 MSP 디렉토리로 이동하십시오.
  ```
  cd $HOME/fabric-ca-client/tlsca-admin/msp
  ```
  이 디렉토리에서 cacerts 폴더를 열고 다음 명령을 사용하여 내부의 인증서를 base64 형식으로 변환하십시오.
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-tlsca-admin>/msp/cacerts/<tls_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  이렇게 하면 TLS CA 루트 인증서가 base64로 인코딩된 문자열로 인쇄됩니다.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVWUVQWnprNXV2b3dobEtacG5JMXplODdIQUlnd0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWURWUVFERXdWMGJITmpZVEFlCkZ3MHhPVEExTURNeE16STVNREJhRncwek5EQTBNamt4TXpJNU1EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdzVPYjNKMGFDQkRZWEp2YkdsdVlURVVNQklHQTFVRUNoTUxTSGx3WlhKc1pXUm5aWEl4RHpBTgpCZ05WQkFzVEJrWmhZbkpwWXpFT01Bd0dBMVVFQXhNRmRHeHpZMkV3V1RBVEJnY3Foa2pPUFFJQkJnZ3Foa2pPClBRTUJCd05DQUFRdSs2UnZWd2w5T2dDVlAraEVxbjVxdExRVG9LWkw4a1lic0pOeU1JbERoc3hlNWx6cW1zQkoKbTk2eUR2TVV6OSsxL2pzb1M4M1JqMVAwc3M2TnJNb3FvMXd3V2pBT0JnTlZIUThCQWY4RUJBTUNBUVl3RWdZRApWUjBUQVFIL0JBZ3dCZ0VCL3dJQkFUQWRCZ05WSFE0RUZnUVVnUEc4anJEK1BxVjdoelc3WDlsbTFrMS91WjR3CkZRWURWUjBSQkE0d0RJY0VxVGJESW9jRUNwbnBkVEFLQmdncWhrak9QUVFEQWdOSUFEQkZBaUVBenk3cHJZaVMKQmlDVWdYeWRkY09WMm9mZmtqaEI0N091QXFjQWNqZS9SWkVDSUdKZFgzZ1ErTDRIN3duY1RoZkwrenU1ejV1UApGUWhXTmlNS3hQWEYrZnYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  이 문자열을 API 요청의 `tls_root_certs` 필드에 제공하십시오.

## 구성 파일 작성
{: #ibp-v2-apis-config}

API를 사용하여 피어 또는 순서 지정자를 작성하려면 구성 파일을 완료해야 합니다. 이 파일은 API 호출의 요청 본문에 있는 `config` 오브젝트로 API에 제공됩니다. CA를 {{site.data.keyword.cloud_notm}} Platform 서비스 인스턴스에 배치하고 파일을 완료하기 전에 필수 ID를 등록하는 단계를 수행해야 합니다.

구성 파일의 템플리트는 아래에서 찾을 수 있습니다.
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

이 전체 파일을 편집할 수 있는 텍스트 편집기에 복사하고 이를 로컬 파일 시스템에 JSON 파일로 저장하십시오. 아래 단계를 사용하여 이 구성 파일을 완료하고 이를 사용하여 순서 지정자 또는 피어를 배치하십시오.

### CA 연결 정보 검색
{: #ibp-v2-apis-config-connx-info}

먼저 {{site.data.keyword.cloud_notm}} Platform에 있는 CA의 연결 정보를 제공해야 합니다. 콘솔 UI 또는 API를 사용하여 CA에 대한 필요한 정보를 가져올 수 있습니다.

**{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용 중인 경우:**
콘솔에서 CA를 열고 **설정**을 클릭한 후 **내보내기** 단추를 클릭하여 CA 정보를 JSON 파일로 내보내십시오. 이 파일의 값을 사용하여 구성 파일을 완료할 수 있습니다.

**API를 사용 중인 경우:**
[`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components)를 호출하여 CA의 연결 정보를 가져올 수 있습니다. `Fabric CA 작성` API를 사용하여 CA를 작성한 경우 응답 본문에서 필요한 정보를 찾을 수도 있습니다.

- `"cahost"` 및 `"caport"` 값은 응답 본문 또는 내보낸 JSON 파일의 `ca_url` 필드에 표시됩니다. 예를 들어, `ca_url`이 https://9.30.94.174:30167인 경우 `"cahost"`는 `9.30.94.174`이고 `"caport"`는 `30167`입니다.
- `"caname"`은 CA를 배치할 때 지정된 CA의 이름입니다. 이는 응답 본문 또는 내보낸 JSON 파일의 `ca_name` 필드 값입니다.
- `"cacert"`는 CA의 base64로 인코딩된 TLS 인증서입니다. 이는 응답 본문 또는 내보낸 JSON 파일의 `pem` 필드 값입니다.

- 다음 `"tls"` 섹션의 필드는 CA 배치 중에 지정된 CA TLS 인스턴스 이름의 값을 사용해야 하는 경우를 제외하고 상기 컴포넌트 섹션으로 전달된 것과 동일한 정보를 사용해야 합니다. `caname`을 응답 본문 또는 내보낸 JSON 파일의 `tlsca_name` 값으로 바꾸십시오. `"cacert"` 값에 대해 동일한 TLS 인증서를 사용하십시오.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

### 컴포넌트 등록 ID 및 시크릿 제공

1. [기본 CA를 사용하여 컴포넌트를 등록](#ibp-v2-apis-config-register-component)하는 데 사용한 `name` 및 `secret`을 구성 파일의 `"component"` 섹션 아래에 `"enrollid"` 및 `"enrollsecret"`으로 붙여넣으십시오.

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```
2. [TLS CA를 사용하여 컴포넌트를 등록](#ibp-v2-apis-config-register-component-tls)하는 데 사용한 `name` 및 `secret`을 구성 파일의 `"tls"` 섹션 아래에 `"enrollid"` 및 `"enrollsecret"`으로 붙여넣으십시오.

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

### 조직 관리자의 signCert 제공

[조직 관리자를 사용하여 인증서를 생성](#ibp-v2-apis-config-enroll-admin)할 때 작성된 MSP 디렉토리로 이동하십시오.
```
cd $HOME/fabric-ca-client/peer-admin/msp
```
이 MSP 디렉토리에서 새 사용자의 signCert 파일을 열고 다음 명령을 사용하여 base64 형식으로 변환하십시오.

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**참고:** 위의 명령으로 생성된 문자열은 하나의 행으로 형식화되어야 합니다. 다음과 유사하게 표시되어야 합니다.

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```
다음과 같이 표시되어서는 안됩니다.

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

### CSR(Certificate Signing Request) 호스트

컴포넌트 파일의 `"csr"` 섹션을 사용하여 컴포넌트에 사용자 정의 도메인을 제공하는 옵션이 있습니다.

```
  "csr": {
  "hosts": [""]
  }
```
{:codeblock}

이 섹션은 고급 사용자가 피어 엔드포인트를 처리하는 데 사용될 수 있는 사용자 정의 도메인 이름을 지정하기 위해 제공됩니다. 대부분의 사용자는 이 섹션을 공백으로 둘 수 있습니다.

### 구성 파일 완료
{: #ibp-v2-apis-config-file}

상기 단계를 모두 완료한 후 업데이트된 구성 파일은 다음 예제와 유사합니다.


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "ca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer1",
            "enrollsecret": "peer1pw",
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
                ]
            }
        }
    }
}
```
{:codeblock}

다른 필드는 공백으로 둘 수 있습니다. 이 파일을 완료한 후 이 파일을 `config` 필드로 `순서 지정자 작성` 또는 `피어 작성` API의 요청 분문에 전달할 수 있습니다.

### 관리자 ID를 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔로 가져오기
{: #ibp-v2-apis-admin-console}

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 블록체인 컴포넌트를 작동시키려면 관리자 ID를 콘솔 지갑으로 가져와야 합니다. 콘솔에서 지갑 패널을 여십시오. 개요 화면에서 **ID 추가** 단추를 클릭하십시오. 이 단추를 클릭하면 ID의 서명 인증서 및 개인 키를 콘솔에 직접 추가할 수 있는 사이드 패널이 열립니다.
- **이름:** 참조용으로만 사용되는 ID의 표시 이름을 입력하십시오.
- **인증서:** 관리자의 서명 인증서를 업로드하십시오. 위의 지시사항을 따른 경우 `$HOME/fabric-ca-client/peer-admin/msp/signcerts/` 폴더에서 이 키를 찾을 수 있습니다.
- **개인 키:** 관리자 개인 키를 업로드하십시오. 위의 지시사항을 따른 경우 `$HOME/fabric-ca-client/peer-admin/msp/keystore/` 폴더에서 이 키를 찾을 수 있습니다.

관리자 ID를 가져온 후 이 ID를 사용자가 작성한 컴포넌트와 연관시킬 수 있습니다. 그런 다음 콘솔을 사용하여 네트워크를 작동할 수 있습니다.

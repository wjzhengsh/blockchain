---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 애플리케이션 개발
{: #dev_app}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform에서는 블록체인 네트워크에 애플리케이션을 연결하는 데 사용할 수 있는 API를 제공합니다. 연결 프로파일에서 네트워크 API 엔드포인트를 사용하여 체인코드를 호출하고 피어에서 채널별 원장을 업데이트하거나 조회할 수 있습니다. [Swagger UI](howto/swagger_apis.html)의 API도 사용하여 네트워크의 노드, 채널 및 구성원을 관리할 수 있습니다.
{:shortdesc}

이 튜토리얼을 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform API에 액세스하고 해당 API를 사용하여 애플리케이션을 네트워크에 등록하는 방법을 알아볼 수 있습니다. 네트워크와 상호작용하고 애플리케이션에서 트랜잭션을 발행하는 방법에 대해서도 알아봅니다. 이 튜토리얼은 Hyperledger Fabric 문서에 있는 [첫 번째 애플리케이션 쓰기 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) 튜토리얼을 기반으로 합니다. **첫 번째 애플리케이션 쓰기** 튜토리얼과 동일한 여러 파일과 명령을 사용하지만 해당 파일과 명령을 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform에서 네트워크와 상호작용합니다. 이 튜토리얼에서는 Hyperledger Fabric Node SDK를 사용하여 애플리케이션 개발의 각 단계를 설명합니다. SDK를 사용하는 대신 Fabric CA Client를 사용하여 사용자를 등록하는 방법도 알아봅니다.

이 학습서 외에도 고유 비즈니스 솔루션을 작성할 때 해당 {{site.data.keyword.blockchainfull}} Platform에서 템플리트로 제공하는 샘플 애플리케이션과 체인코드를 사용할 수 있습니다. 자세한 정보는 [샘플 애플리케이션 배치](howto/prebuilt_samples.html)를 참조하십시오.

## 전제조건
{{site.data.keyword.blockchainfull_notm}} Platform에서 **첫 번째 애플리케이션 쓰기** 튜토리얼을 사용할 수 있으려면 다음 전제조건이 필요합니다.

- {{site.data.keyword.cloud_notm}}에 Blockchain 네트워크가 없으면 스타터 또는 엔터프라이즈 멤버십 플랜으로 해당 네트워크를 작성해야 합니다. 자세한 정보는 [스타터 플랜 네트워크 작성](get_start_starter_plan.html#creating-a-network) 또는 [엔터프라이즈 플랜 네트워크 작성](get_start.html#creating-a-network)을 참조하십시오.

  네트워크의 네트워크 모니터를 입력한 다음 "개요" 화면에서 조직의 피어를 하나 이상 추가하십시오. 그런 다음 네트워크에 하나 이상의 채널을 작성하십시오. 자세한 정보는 [채널 작성](howto/create_channel.html#creating-a-channel)을 참조하십시오. 스타터 플랜 네트워크를 사용하는 경우 체인코드를 배치하는 데 사용할 수 있는 `defaultchannel`이라는 이름의 채널이 네트워크에 이미 있다는 점을 **참고**하십시오.

- Hyperledger Fabric 샘플을 다운로드하고 Node SDK를 사용하는 데 필요한 도구를 설치하십시오.
  * [Curl ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") 또는 [Git ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- `fabric-samples` 디렉토리를 다운로드하여 Hyperledger Fabric 샘플을 설치하십시오. Hyperledger Fabric 문서의 [시작하기 안내서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html)를 따를 수 있습니다.

  로컬 시스템에 `fabric-samples` 디렉토리를 다운로드한 다음 `fabric-samples/fabcar`로 이동하십시오. 클린 디렉토리에서 샘플 애플리케이션을 시도하고 테스트할 수 있도록 이 디렉토리의 사본을 작성하여 이름을 바꾸십시오.

  `fabcar` 디렉토리에서 `npm install` 명령을 실행하여 Fabric SDK를 사용하는 데 필요한 패키지를 설치하십시오. 여기에는 `fabric-client`와 `fabric-ca-client`가 포함되어 있습니다.

- [네트워크 모니터](howto/install_instantiate_chaincode.html#installchaincode)를 사용하여 채널에서 fabcar 체인코드를 설치하고 인스턴스화하십시오. `fabric-samples` 폴더의 `fabric-samples > chaincode > fabcar > go`에서 fabcar 체인코드를 찾을 수 있습니다.

- 네트워크 모니터의 "개요" 화면에서 네트워크의 연결 프로파일을 검색하십시오. 연결 프로파일을 `fabcar` 디렉토리에 저장하고 `creds.json`으로 이름을 바꾸십시오.

## Fabric SDK 사용
{: #using-the-fabric-sdks}

Hyperledger Fabric SDK에서는 애플리케이션에서 Blockchain 네트워크와 상호작용하는 데 사용할 수 있는 강력한 API 세트를 제공합니다. [Hyperledger Fabric SDK 커뮤니티 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK 커뮤니티 문서"){:new_window}에서 지원되는 최신 언어 목록을 찾을 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 Node SDK나 Java SDK를 사용하는 것이 좋습니다. SDK의 개별 저장소에서 SDK가 제공하는 API에 관해 자세히 알아볼 수 있습니다.

이 튜토리얼에서는 [Node SDK ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Node SDK"){:new_window}를 사용하여 애플리케이션을 등록한 다음, 체인코드를 호출하고 조회하여 트랜잭션을 발행하는 데 애플리케이션을 사용할 수 있습니다. 이 튜토리얼에서는 애플리케이션에서 Blockchain 네트워크에 연결할 수 있도록 SDK에 제공해야 하는 정보에 관해 설명합니다. 사용할 수 있는 몇 가지 API와 SDK가 Blockchain 네트워크와 상호 작용하고 해당 네트워크에 트랜잭션을 제출하는 방법에 관해서도 설명합니다.

## 애플리케이션에 네트워크 API 엔드포인트 추가
{: #api-endpoints}

{{site.data.keyword.cloud_notm}}의 Blockchain 네트워크에서 순서 지정자, CA 및 피어 노드를 비롯한 특정 네트워크 리소스의 API 엔드포인트를 애플리케이션에 제공해야 합니다. 애플리케이션에서는 해당 API 엔드포인트를 통해 네트워크와 상호 작용할 수 있습니다. 네트워크의 연결 프로파일에서 API 엔드포인트를 찾을 수 있습니다. 연결 프로파일은 JSON 형식으로 되어 있으며 네트워크 리소스의 enrollID/시크릿과 API 엔드포인트 정보를 포함합니다.

1. 다음 방법 중 하나로 네트워크 모니터에서 네트워크 리소스의 API 엔드포인트 정보를 검색하십시오.
  * "개요" 화면에서 **연결 프로파일**을 클릭하십시오. 연결 프로파일에는 모든 네트워크 리소스의 전체 API 엔드포인트 정보 세트가 포함되어 있습니다.
    ![네트워크 모니터의 연결 프로파일](images/service_credentials.png "네트워크 모니터의 연결 프로파일")

  * 네트워크에서 체인코드를 실행하는 경우 체인코드에 특정한 API 엔드포인트 정보를 얻을 수 있습니다. "채널" 화면에서 체인코드가 실행 중인 채널 행을 클릭하여 특정 채널 화면을 여십시오. Then, locate the chaincode and click the **JSON** button.
    ![체인코드별 API 엔드포인트](images/channel_chaincode.png "체인코드별 API 엔드포인트")

2. 네트워크 리소스의 API 엔드포인트 정보를 찾으십시오. 다음 예에서 `peer1-org1` 행과 비슷합니다.
  ```
"peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **참고**: 애플리케이션에서 조직 외부의 네트워크 리소스를 대상으로 지정할 수 있습니다. 예를 들어 체인코드 [인증 정책](howto/install_instantiate_chaincode.html#specifying-chaincode-endorsement-policies)에 채널에 있는 다른 조직의 인증이 필요한 경우 피어의 엔드포인트 정보와 수반하는 TLS 인증서를 얻어야 합니다. 연결 프로파일의 피어 섹션에서 이 정보를 찾을 수 있습니다. 그러나 특정 채널에 가입한 피어에 관해서는 다른 조직의 관리자에게 문의해야 합니다.

3. 다음 예제에 표시된 대로 API 엔드포인트 정보를 애플리케이션의 구성 파일에 플러그로 지정하십시오.
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  네트워크 리소스의 가용성을 확인하기 위해 해당 엔드포인트에 [HEAD 요청](howto/monitor_network.html#monitor-nodes)도 보낼 수 있습니다.

  Fabric SDK를 사용하는 경우 연결 프로파일을 사용하여 네트워크에도 연결할 수 있습니다. 이 튜토리얼에서는 SDK에 네트워크의 엔드포인트 정보를 수동으로 제공합니다. 그러나 나중 섹션에서 [SDK와 연결 프로파일을 사용](#using-your-connection-profile-with-the-sdk)하는 데 관한 지침과 튜토리얼을 찾을 수 있습니다.

## 애플리케이션 등록
{: #enroll-app}

{{site.data.keyword.blockchainfull_notm}} Platform에서 애플리케이션을 네트워크에 연결하기 전에 네트워크에 애플리케이션의 인증서를 제공해야 합니다. x509와 공개 키 인프라에 관한 [외부 리소스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window}가 많이 있으므로 해당 사항에 관한 세부사항은 조사하지 않습니다. 간단히 말해서 Fabric의 통신 플로우는 모든 터치포인트에서 서명/확인 오퍼레이션을 사용합니다. 따라서 원장 조회 또는 업데이트와 같은 호출을 네트워크에 보내는 애플리케이션에서는 개인 키로 페이로드에 서명한 다음 검증을 위해 적절하게 서명된 x509 인증서를 첨부해야 합니다. **등록**은 해당 인증 기관에서 필요한 키와 인증서를 생성하는 프로세스입니다. 등록하고 나면 애플리케이션은 네트워크와 통신할 준비가 됩니다.

이 섹션에서는 **첫 번째 애플리케이션 쓰기** 튜토리얼의 일부로 Fabric SDK를 사용하여 키와 인증서를 검색하는 방법을 설명합니다. 명령행에서 Fabric CA Client를 사용하여 애플리케이션을 등록하는 방법에 관해서도 알아볼 수 있습니다.

<!-- this last sentance when the certificates article goes live --->

### Fabric SDK를 사용하여 등록
{: #enroll-app-sdk}

`fabric-samples` 폴더의 `fabcar` 디렉토리에서 문서 편집기로 `enrollAdmin.js` 파일을 여십시오.

1. 파일에서 먼저 Fabric Client의 인스턴스를 작성합니다.
  ```
  var fabric_client = new Fabric_Client();
  ```

2. 그런 다음 파일에서 인증서를 관리할 키-값 저장소(KVS)를 작성합니다. SDK에서는 [KeyValueStore ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} 클래스를 사용하여 키-값 저장소를 작성하고 [CryptoSuite ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} 클래스를 사용하여 암호 계산을 수행합니다. 아래에서 관련 코드 블록을 볼 수 있습니다.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. KVS를 정의한 다음 [Fabric Client ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Client.html "Fabric Client") 클래스와 Fabric-CA-Client API <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")--->의 몇 가지 메소드를 사용하여 CA 서버와 통신할 수 있습니다. 인증 기관의 이름과 URL을 SDK에 제공해야 합니다. 네트워크 모니터의 **개요** 화면에서 **연결 프로파일** JSON 파일을 열고 `certificateAuthorites` 섹션에서 다음 변수를 찾으십시오.
  * CA의 URL: ``certificateAuthorities`` 아래 ``url``
  * 관리자 ID: ``enrollId``
  * 관리 비밀번호: ``enrollSecret``
  * CA 이름: `caName`

  `enrollAdmin.js` 파일에서 이 정보를 포함하는 관련 행을 다음 방식으로 **편집**하십시오.
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  예를 들어, 다음과 같습니다.
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```
  {:codeblock}

  그러면 Fabric Client에서 애플리케이션이 이미 등록되었는지 확인합니다. 연결 프로파일에서 `enrollID`가 있는 아래 행을 **편집**하십시오.
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. CA 서버에 "enroll" 호출을 보내야 합니다. `admin`이 이미 네트워크에 등록되었습니다. 등록 호출에서는 x509 인증서로 랩핑되고 대상 CA에서 서명한 개인 키와 공개 키를 검색합니다. 이 랩핑되고 서명된 인증서는 signCert라고 합니다. signCert를 사용하면 네트워크 구성원이 클라이언트에서 시작되는 호출을 확인할 수 있습니다. 인증 정보 파일의 조직 이름, 비밀번호 및 MSP ID를 제공해야 합니다. 네트워크 인증 정보의 `certificateAuthorities` 섹션에서 `enrollID`, `enrollSecret` 및 `x-mspid`를 찾으십시오. 해당 값이 있는 아래 코드 블록을 **편집**하고 파일의 관련 섹션을 바꾸십시오.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. `enrollAdmin.js` 파일을 저장하십시오.

`fabcar` 디렉토리에서 다음 명령을 실행하여 관리자를 등록하십시오.
```
node enrollAdmin.js
```
{:codeblock}

등록 명령을 통해 signCert를 생성한 다음 `hfc-key-store`라는 폴더에 내보냅니다. 이 튜토리얼에 포함된 나중 파일이 이 폴더에서 인증서를 찾습니다. `hfc-key-store` 폴더에서 관리 인증서를 찾을 수 있으면 등록 명령이 작동합니다.

[SDK를 사용하여 네트워크를 운영](#operate-sdk)하려면 관리 signCert를 {{site.data.keyword.blockchainfull}} Platform에 업로드해야 합니다. `hfc-key-store` 폴더에서 관리 signCert를 찾을 수 있습니다. `admin` 파일을 열고 `certificate` 필드 다음에 따옴표로 묶인 인증서를 복사하십시오. 도구 또는 문서 편집기를 사용하여 인증서를 PEM 형식으로 변환하십시오. 그런 다음 네트워크 모니터에서 사용자의 블록체인 네트워크에 관리자 인증서를 업로드할 수 있습니다. 인증서 추가에 대한 자세한 정보는 네트워크 모니터에서 ["구성원" 화면의 "인증서" 탭](v10_dashboard.html#members)을 참조하십시오. SDK만 사용하여 체인코드를 호출하거나 조회하는 경우에는 필요하지 않습니다.

<!-- you can eliminate this section when the certificates article goes live --->

### Fabric CA Client를 사용하여 등록
{: #enroll-app-caclient}

1. 로컬 시스템에 [fabric-ca 2진 파일](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/)을 다운로드하여 압축을 푸십시오. 실행 가능 `fabric-ca` 클라이언트를 다운로드한 디렉토리에서 명령을 실행하는 데 사용할 수 있는 디렉토리로 이동하십시오.

2. 사용하는 서비스 플랜, 위치 및 클러스터에 따라 {{site.data.keyword.cloud_notm}}에서 TLS 인증서를 다운로드하십시오.
  - 스타터 플랜의 루트 TLS 인증서  
    - 미국: [us01.blockchain.ibm.com.cert ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"), [us02.blockchain.ibm.com.cert ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - 영국: [uk01.blockchain.ibm.com.cert ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"), [uk02.blockchain.ibm.com.cert ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - 시드니: [aus01.blockchain.ibm.com.cert ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"), [aus02.blockchain.ibm.com.cert ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [엔터프라이즈 플랜에 대한 루트 TLS 인증서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  컨텐츠를 폴더(예: ``$HOME/tls``)에 저장하십시오. 이 단계는 데이터 플로우가 연결에서 암호화되도록 허용합니다.

3. 네트워크 모니터의 **개요** 화면에서 **연결 프로파일** JSON 파일을 열고 다음 변수를 찾으십시오.
  * CA의 URL: ``certificateAuthorities`` 아래 ``url``
  * 관리자 ID: ``enrollId``
  * 관리 비밀번호: ``enrollSecret``
  * CA 이름: ``caName``

4. 다음 명령으로 TLS 인증 경로와 위의 네 가지 문자열을 전달하여 `enroll` 호출을 인증 기관에 보내는 데 Fabric CA Client를 사용할 수 있습니다.
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Fabric CA Client가 이동될 디렉토리에서 이 명령을 실행해야 합니다. 실제 호출은 다음 예제 명령과 비슷하게 보일 수 있습니다.
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`에서 관리자 인증서를 찾으십시오. 그런 다음 네트워크 모니터에서 사용자의 블록체인 네트워크에 관리자 인증서를 업로드할 수 있습니다. 인증서 추가에 대한 자세한 정보는 네트워크 모니터에서 ["구성원" 화면의 "인증서" 탭](v10_dashboard.html#members)을 참조하십시오.

  또한, CA 루트 인증서 및 관리자 개인 키를 다음 디렉토리에서 찾을 수 있습니다.
  * CA 루트 인증서: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * 관리자 개인 키: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`


## 애플리케이션 등록
{: #register-app}

클라이언트 측 인증서를 생성한 다음 네트워크 인증 기관에 애플리케이션을 등록해야 합니다. 등록하면 네트워크에서 인식할 수 있는 컴포넌트 목록에 애플리케이션을 추가합니다. `admin`을 사용하여 요청에 서명하지 않고 개별 ID로 애플리케이션을 등록하는 것이 우수 사례입니다.

### SDK를 사용하여 등록
{: #register-app-sdk}

`admin` signCert를 사용하여 `user1`을 애플리케이션을 등록하는 데 `registerUser.js` 파일을 사용할 수 있습니다. 문서 편집기에서 `registerUser.js`를 여십시오.

1. Fabric CA Client에 새 인스턴스의 CA URL과 이름을 제공하십시오.

2. [Fabric Client ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Client.html)에서 `getUserContext` 메소드에 enrollID를 제공하여 `admin`이 등록되었으며 이 요청을 발행할 수 있는지 확인하십시오. 아래 샘플을 기반으로 파일에서 관련 코드 블록을 **편집**하십시오.
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. **Fabric CA Client**를 사용하여 CA에 사용자를 등록한 다음 **Fabric Client**를 사용하여 새 signCert를 작성하십시오. MSP ID와 조직의 소속을 사용하여 다음 블록을 **편집**하십시오. 네트워크 인증서의 인증 기관 섹션에서 `x-affiliations`를 찾을 수 있으며 나열된 소속을 사용할 수 있습니다. 작성할 사용자 이름을 추가하십시오. 기본적으로 fabcar 샘플에서는 `user1`을 사용합니다.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. `registerUser.js` 파일을 저장하십시오.

`node registerUser.js` 명령을 실행하여 `user1`을 등록하십시오. `hfc-key-store` 폴더에서 `user1` 인증서를 찾을 수 있으면 명령이 작동하는 것입니다. ID는 한 번만 등록할 수 있습니다. 문제점이 발생하면 새 사용자 이름으로 `registerUser.js`를 실행해 보십시오.

<!-- you can eliminate this section when the certificates article goes live --->

### Fabric CA Client를 사용하여 등록
{: #register-app-caclient}

1. 다음 명령을 실행하여 Blockchain 네트워크에서 소속과 조직 이름을 찾으십시오.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  다음 예제와 비슷한 정보가 표시되어야 합니다.
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  두 번째 **소속(affiliation)** 값(예: `ibp.PeerOrg1`)을 기록해 두십시오. 아래 명령에서 이 값을 사용해야 합니다.

2. 다음 명령을 실행하여 피어를 등록하십시오.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  피어의 이름과 비밀번호를 지정하고 `name`과 `password`를 사용자의 이름과 비밀번호로 바꾸십시오. 이 정보를 기록하십시오. 이 정보는 피어를 구성할 때 필요합니다. 예를 들어, 다음과 같습니다.
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  ID는 한 번만 등록할 수 있습니다. 문제점이 발생하면 새 사용자 이름과 비밀번호를 사용하여 명령을 시도하십시오.

  명령이 완료되면 다음 예제와 비슷한 정보가 표시되어야 합니다.
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

### 네트워크 모니터를 사용하여 등록

또는 네트워크 모니터 **인증 기관** 탭을 사용하여 클라이언트 애플리케이션을 등록할 수 있습니다. 자세한 지시사항은 이 [정보](v10_dashboard.html#ca)를 참조하십시오.

## 체인코드를 호출하고 조회하여 트랜잭션 실행
{: #invoke-query}

애플리케이션에서 트랜잭션을 제출하려면 전체 Blockchain 네트워크와 상호작용해야 합니다.

1. 애플리케이션에서 채널의 피어가 승인할 트랜잭션 제안을 전송합니다.
2. 승인 피어에서 승인된 트랜잭션을 애플리케이션에 보냅니다.
3. 애플리케이션에서 승인된 트랜잭션을 순서 지정 서비스에 전송하여 트랜잭션을 원장에 추가합니다.

전체 트랜잭션 플로우에 관한 자세한 정보는 Hyperledger Fabric 문서에서 [트랜잭션 플로우 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "트랜잭션 플로우"){:new_window}을 참조하십시오. 이 튜토리얼을 시작한 다음 SDK에서 네트워크와 상호작용하는 방법을 관리하는 팁을 보려면 [애플리케이션 연결 및 가용성](#app-connectivity-availability) 섹션을 방문하십시오.

다음 샘플에서는 Node SDK에서 네트워크 토폴로지를 설정하고 트랜잭션 제안을 정의한 다음 네트워크에 트랜잭션을 제출하는 방법을 설명합니다. `invoke.js` 파일을 사용하여 `fabcar` 체인코드에서 함수를 호출할 수 있습니다. 해당 함수를 사용하면 Blockchain 원장에서 자산을 작성하고 전송할 수 있습니다. 이 튜토리얼에서는 `initLedger` 함수를 사용하여 채널에 새 데이터를 추가한 다음 `query.js` 파일을 사용하여 데이터를 조회합니다.

### 체인코드 호출
{: #invoke}

문서 편집기에서 `invoke.js` 파일을 여십시오.

1. 파일 맨 위에 `var creds = require('./creds.json')`을 추가하십시오. 이 코드 행을 사용하면 `invoke.js` 파일이 `creds.json` 인증 정보 파일에서 정보를 읽을 수 있습니다.

2. 네트워크 리소스의 API 엔드포인트를 사용하여 패브릭 네트워크를 설정하려면 [Fabric Client ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} 클래스를 사용하십시오. 이 단계에서는 클라이언트가 제안을 제출할 채널과 피어 및 SDK에서 인증된 트랜잭션을 보낼 순서 지정 서비스를 정의합니다. 아래 관련 코드 블록을 **편집**하십시오. `creds.peers["org1-peer1"].url` 행에서는 인증 정보 파일에서 피어 url을 가져옵니다.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  피어 URL을 `fabric_client.newPeer` 메소드에 추가할 때 아래 코드 스니펫을 사용하여 연결 프로파일에서 관련 TLS 인증서도 가져옵니다. 순서 지정 서비스 URL을 추가할 때도 동일한 작업을 수행합니다. 이 TLS 인증을 사용하여 네트워크와의 통신을 인증해야 합니다.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  인증 정책에 따라 채널에 있는 다른 조직이 트랜잭션을 인증해야 하는 경우 네트워크를 설정할 때 `newPeer()` 및 `channel.addPeer()` 메소드를 사용하여 해당 조직 피어를 추가해야 합니다. 조직에서는 특정 채널에 가입한 피어 목록을 보내야 합니다. 엔드포인트 정보 및 TLS 인증은 연결 프로파일에서 사용할 수 있습니다. SDK에서는 채널에 추가된 모든 피어에 트랜잭션을 보냅니다.

  [애플리케이션을 고가용성으로 작성](#ha-app)하는 단계로 채널에 가입한 조직에 속한 피어도 추가할 수 있습니다. 그러면 피어 중 하나가 중단되는 경우 SDK에 장애 복구를 제공합니다.

3. 패브릭 네트워크를 설정하고 등록 단계에서 애플리케이션 ID와 signCert를 가져오고 나면 사용자가 네트워크에 제출할 제안을 `invoke.js` 파일에서 정의합니다. `fabcar` 체인코드의 `initLedger` 함수를 사용하여 원장에 초기 데이터를 추가할 수 있습니다. `fabcar` 체인코드에서 찾을 수 있는 기타 함수를 호출하는 코드 블록도 편집할 수 있습니다.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  요청을 정의한 다음 [트랜잭션 제안 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal")을 채널의 피어에 보낼 수 있습니다. 피어에서 제안을 리턴하고 나면 [트랜잭션 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction")을 순서 지정 서비스에 보낼 수 있습니다.

4. 이벤트 서비스를 추가하여 트랜잭션 플로우의 효율성을 높일 수 있습니다. 다음 절을 **편집**하십시오.
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  샘플에서는 피어 기반 이벤트 서비스를 사용하지만 사용자는 채널 기반 리스너를 사용해야 합니다. [트랜잭션 관리](#managing-transactions) 섹션과 [Node SDK 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "채널 기반 이벤트 서비스"){:new_window}에서 자세히 알아볼 수 있습니다.

5. 기본적으로 `invoke.js`에서 `user1`로 트랜잭션을 제출합니다. 다른 이름을 등록한 경우 `invoke.js` 파일을 편집할 수 있습니다.

파일 편집을 완료하고 나면 `node invoke.js` 명령을 실행하여 트랜잭션을 네트워크에 제출하십시오. 호출에 성공했음을 확인하려면 다음이 표시되어야 합니다.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

이 메시지는 애플리케이션에서 체인코드를 호출하여 원장에 데이터를 추가했음을 나타냅니다.

### 체인코드 조회
{: #query}

이제 `query.js`를 사용하여 원장을 읽을 수 있습니다. 문서 편집기에서 `query.js` 파일을 여십시오.

1. 파일 맨 위에 `var creds = require('./creds.json')`을 추가하십시오. 
2. 채널 이름 및 피어의 엔드포인트 정보로 파일을 업데이트하십시오. 이 오퍼레이션에서는 피어에 저장된 데이터만 읽으므로 순서 지정 서비스의 엔드포인트 정보를 추가하지 않아도 됩니다. `query.js`에서는 `user1`로 제안을 전송한다고도 가정합니다.
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

파일 편집을 완료한 후 `node query.js` 명령을 실행하면 다음 예와 비슷한 원장의 자동차 목록이 표시되어야 합니다.
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

fabcar 애플리케이션과 해당 애플리케이션에서 사용하는 함수에 관한 자세한 정보를 보려면 Hyperledger Fabric 문서에 있는 [첫 번째 애플리케이션 쓰기![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "첫 번째 애플리케이션 쓰기"){:new_window}의 전체 튜토리얼을 방문할 수 있습니다.

## SDK와 연결 프로파일 사용
{: #using-your-connection-profile-with-the-sdk}

네트워크의 엔드포인트 정보를 수동으로 가져오지 않고 네트워크 모니터의 **개요** 화면에서 **연결 프로파일**을 사용하여 네트워크에 SDK를 연결할 수 있습니다. 그러면 등록을 위해 인증 기관에 연결하는 프로세스가 간소화됩니다. 또한 트랜잭션을 제출하기 전에 패브릭 네트워크를 정의할 필요가 없습니다. SDK를 통해 연결 프로파일에서 직접 관련 채널에 있는 피어와 순서 지정자를 찾습니다. [Node SDK 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-network-config.html "연결 프로파일 튜토리얼"){:new_window}에서 연결 프로파일 사용 방법에 관한 자세한 정보를 찾을 수 있습니다.

`invoke.js` 파일을 예로 사용하여 수동 엔드포인트가 아니라 연결 프로파일을 사용하는 경우 효율성을 확인할 수 있습니다. `loadFromConfig` 클래스를 사용하여 패브릭 클라이언트의 새 인스턴스를 설정할 수 있습니다. `var fabric_client = new Fabric_Client();`를 다음 코드로 바꾸십시오.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

피어와 순서 지정자를 작성한 다음 채널에 추가하여 패브릭 네트워크를 설정하지 않고 다음 행을 사용하여 새 채널을 작성할 수 있습니다.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

그런 다음 SDK에서 연결 프로파일을 사용하여 채널에 정의된 피어와 순서 지정 서비스를 추가합니다. 그러면 애플리케이션을 더 효율적으로 쓸 수 있고, 네트워크 구성원이 새 채널에 가입, 종료 및 시작할 때 애플리케이션을 더 쉽게 업데이트할 수 있습니다. 추가 관련 단계에 관해 알아보려면 Node SDK 문서 [연결 프로파일 튜토리얼 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-network-config.html "연결 프로파일 튜토리얼"){:new_window}를 참조하십시오. 수동 엔드포인트 연결이 아니라 연결 프로파일을 사용하는 이 [버전의 fabcar 튜토리얼 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window}을 사용할 수 있습니다.

연결 프로파일을 편집하여 인증을 위해 조직 외부에 있는 피어에 트랜잭션을 보낼 수 있습니다. {{site.data.keyword.blockchainfull}} Platform 네트워크의 다른 조직에 있는 피어의 TLS 인증서와 엔드포인트 정보가 이미 연결 프로파일에 포함되어 있습니다. 프로파일의 `channels` 섹션에서 관련 채널에 피어 이름을 추가하여 채널에 피어를 추가하십시오.

## 애플리케이션 연결 및 가용성
{: #app-connectivity-availability}

Hyperledger Fabric [트랜잭션 플로우 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "트랜잭션 플로우"){:new_window}는 고유한 역할을 수행하는 클라이언트 애플리케이션과 함께 여러 컴포넌트를 포함합니다. SDK에서는 인증을 위해 피어에 트랜잭션 제안을 제출합니다. 그런 다음 순서 지정 서비스에 보낼 인증된 제안을 수집한 후 채널 원장에 추가할 피어에 트랜잭션 블록을 보냅니다. 프로덕션 애플리케이션 개발자는 효율성과 가용성을 위해 SDK와 네트워크 사이의 상호작용을 관리하도록 준비해야 합니다.

### 트랜잭션 관리
{: #managing-transactions}

애플리케이션 클라이언트에서는 해당 트랜잭션 제안의 유효성이 검증되고 제안이 성공적으로 완료되었는지 확인해야 합니다. 네트워크 가동 중단 또는 컴포넌트 실패와 같은 여러 이유 때문에 제안이 지연되거나 유실될 수 있습니다. 컴포넌트 실패를 처리하려면 애플리케이션의 [고가용성](#ha-app)을 준비해야 합니다. 또한 네트워크에서 응답하기 전에 제안의 제한시간이 초과되지 않도록 애플리케이션에서 [제한시간 값을 증가](#set-timeout-in-sdk)시킬 수 있습니다.

체인코드가 실행 중이지 않으면 이 체인코드에 보낸 첫 번째 트랜잭션 제안에서 체인코드를 시작합니다. 체인코드를 시작하는 동안 기타 모든 제안은 체인코드가 현재 시작 중임을 나타내는 오류로 인해 거부됩니다. 이는 트랜잭션 무효화와 다릅니다. 체인코드를 시작하는 동안 제안이 거부되면 애플리케이션 클라이언트에서 체인코드가 시작된 후 다시 거부된 제안을 보내야 합니다. 애플리케이션 클라이언트에서는 트랜잭션 제안을 유실하지 않도록 메시지 큐를 사용합니다.

채널 기반 이벤트 서비스를 사용하여 트랜잭션을 모니터하고 메시지 큐를 빌드할 수 있습니다. 채널 기반 리스너는 여러 채널로 확장할 수 있고 여러 다른 채널의 트래픽을 구분할 수 있으므로 피어 기반 서비스 대신 사용해야 합니다. [channelEventHub ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} 클래스에서 트랜잭션, 블록 및 체인코드 이벤트를 기반으로 리스너를 등록할 수 있습니다. Node SDK 문서를 사용하여 [채널 기반 이벤트 서비스 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "채널 기반 이벤트 서비스"){:new_window}을 설정하는 방법을 알아볼 수 있습니다.

### 네트워크 연결 종료

트랜잭션 제안을 제출하기 전에 SDK에 피어와 순서 지정자를 추가할 때 네트워크 컴포넌트에 대한 grpc 스트리밍 연결을 엽니다. 예를 들어
```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
명령을 실행하면 `org1-peer1`에 대한 연결이 열립니다. 지속적으로 실행 중인 애플리케이션이 있는 경우 해당 애플리케이션이 필요하지 않게 되면 `peer.close()` 및 `orderer.close()` 명령으로 연결을 종료하여 리소스를 사용 가능하게 하고 성능 저하를 방지할 수 있습니다. Node SDK 문서의 [피어](https://fabric-sdk-node.github.io/Peer.html#close__anchor) 및 [순서 지정자](https://fabric-sdk-node.github.io/Orderer.html#close__anchor) 클래스에서 자세한 정보를 찾을 수 있습니다.

### 고가용성 애플리케이션
{: #ha-app}

고가용성 우수 사례로서 장애 복구를 위해 조직당 최소 두 개의 피어를 배치하도록 적극 권장합니다. 고가용성을 위해서도 애플리케이션을 조정해야 합니다. 두 피어 모두에 체인코드를 설치하고 채널에 추가하십시오. 그런 다음 네트워크를 설정하고 피어 대상 목록을 빌드할 때 두 피어 엔드포인트 모두에 [트랜잭션 제안을 제출](#invoke)하도록 준비하십시오. 엔터프라이즈 플랜 네트워크가 있는 경우 네트워크에 또 다른 순서 지정자도 추가할 수 있습니다. 네트워크 엔드포인트를 수동으로 추가하지 않고 [연결 프로파일](#using-your-connection-profile-with-the-sdk)을 사용하는 경우 프로파일이 최신 상태이며 추가 피어와 순서 지정자가 프로파일의 `channels` 섹션에서 관련 채널을 추가했는지 확인하십시오. 그러면 SDK에서 연결 프로파일을 사용하여 채널에 가입한 컴포넌트를 추가합니다.

## 상호 TLS 사용
{: #mutual-tls}

Fabric V1.1 레벨에 있는 엔터프라이즈 플랜 네트워크를 실행하는 경우 애플리케이션에 [상호 TLS를 사용](v10_dashboard.html#network-preferences)하는 옵션이 있습니다. 상호 TLS를 사용으로 설정하는 경우 이 기능을 지원하도록 애플리케이션을 업데이트해야 합니다. 그렇지 않으면 애플리케이션에서 네트워크와 통신할 수 없습니다.

연결 프로파일에서 `certificateAuthorities` 섹션을 찾으십시오. 이 섹션에는 상호 TLS를 사용하여 사용자 네트워크와 통신하기 위해 인증서를 등록접수하고 가져오는 데 필요한 다음 속성이 있습니다.

- `url`: 상호 TLS 인증서를 제공할 수 있는 CA에 연결하기 위한 URL
- `enrollId`: 인증서를 가져오는 데 사용할 등록접수 ID
- `enrollSecret`: 인증서를 가져오는 데 사용할 등록접수 시크릿
- `x-tlsCAName`: 애플리케이션이 상호 TLS를 사용하여 통신할 수 있도록 하는 인증서를 가져오는 데 사용할 CA 이름

상호 TLS를 지원하도록 애플리케이션을 업데이트하는 데 관한 자세한 정보는 [상호 TLS 구성 방법 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "상호 tls"){:new_window}을 참조하십시오.

## (선택사항) SDK를 사용하여 네트워크 운영
{: #operate-sdk}

SDK도 사용하여 Blockchain 네트워크를 운영할 수 있습니다. 이 튜토리얼에서는 SDK를 사용하여 피어를 채널에 가입하고 피어에 체인코드를 설치하며 채널에서 체인코드를 인스턴스화하는 방법을 설명합니다. 모든 피어가 {{site.data.keyword.blockchainfull}} Platform에서 실행 중인 경우 [Swagger UI](howto/swagger_apis.html)에서 네트워크 모니터나 API를 사용하여 해당 오퍼레이션을 수행할 수 있으므로 이 단계는 선택사항입니다.

해당 단계를 완료하려면 관리 signCert를 {{site.data.keyword.blockchainfull}} Platform에 업로드해야 합니다. [등록 섹션](#enroll-app-sdk)의 끝에서 signCert를 업로드하는 방법에 관한 지시사항을 찾을 수 있습니다.

### 채널 가입
{: #join-channel-sdk}

네트워크 모니터 또는 API를 사용하여 채널을 작성한 후 SDK를 사용하여 피어를 채널에 가입시킬 수 있습니다.

1. 순서 지정 서비스에서 채널의 [최초 블록 페치 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "최초 블록 페치"){:new_window}를 수행합니다.
2. 최초 블록을 [채널 가입 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} 메소드에 가입하여 피어를 채널에 가입시킵니다.

`fabcar` 샘플을 사용하여 채널을 가입하려면 `invoke.js` 파일을 시작점으로 사용하십시오. 애플리케이션이 아니라 관리자로 이 요청을 보내야 하므로 `getUserContext` 메소드에서 `user1`을 `admin`으로 바꾸십시오. `var request = {`에서 체인코드 호출 요청을 정의한 위치에서 시작하여, [node SDK 문서 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "상호 tls"){:new_window}에서 아래 코드 스니펫을 기반으로 트랜잭션 플로우를 채널 가입 요청으로 바꾸십시오.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```
### 체인코드 설치
{: #install-cc-sdk}

[패브릭 클라이언트 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Client.html "패브릭 클라이언트"){:new_window} 클래스의 [체인코드 설치 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} 메소드를 사용하여 피어에 체인코드를 설치할 수 있습니다.

`fabcar` 샘플을 사용하여 피어에 `fabcar` 체인코드를 설치하려면 `query.js` 파일을 기준선으로 사용하여 편집하십시오. 애플리케이션이 아니라 관리자로 이 요청을 보내야 하므로 `getUserContext` 메소드에서 `user1`을 `admin`으로 바꾸십시오. 아래 예제를 사용하여 트랜잭션 제안 오브젝트를 체인코드 설치 요청으로 바꾸십시오.
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

현재 `return channel.queryByChaincode(request);` 파일에 있는 행 대신 이 오브젝트를 `return fabric_client.installChaincode(request);`에 보내십시오.

### 체인코드 인스턴스화
{: #instantiate-cc-sdk}

체인코드를 인스턴스화하려면 [제안 인스턴스화 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window}를 피어에 보낸 다음 [트랜잭션 요청 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window}을 순서 지정 서비스에 보내야 합니다.

`fabcar` 샘플을 사용하여 체인코드를 인스턴스화하려면 `invoke.js` 파일을 시작점으로 사용하십시오. 애플리케이션이 아니라 관리자로 이 요청을 보내야 하므로 `getUserContext` 메소드에서 `user1`을 `admin`으로 바꾸십시오. 아래 예제를 사용하여 트랜잭션 제안 오브젝트를 체인코드 설치 요청으로 바꾸십시오.
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };

```
{:codeblock}

현재 파일에 있는 `return channel.sendTransactionProposal(request);` 대신 `return channel.sendInstantiateProposal(request);`에 이 요청을 보내십시오. 채널에 인스턴스화 요청을 보낸 다음 인증된 제안을 트랜잭션으로 순서 지정 서비스에 보내야 합니다. 여기에서는 트랜잭션을 보낼 때와 동일한 메소드를 사용하므로, 나머지 파일은 변경하지 않은 상태로 유지할 수 있습니다. 인스턴스화 제안에서 [시간제한 초과를 증가](#set-timeout-in-sdk)시킬 수 있습니다. 그렇지 않으면 플랫폼에서 체인코드 컨테이너를 시작할 수 있기 전에 제한시간이 초과될 수 있습니다.

## (선택사항) Fabric SDK에서 제한시간 값 설정
{: #set-timeout-in-sdk}

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

## (선택사항) CouchDB 색인 사용
{: #couchdb-indices}

네트워크에서 CouchDB를 사용하고 CouchDB 색인 작성 기능을 사용하여 CouchDB의 성능을 향상시키려는 경우 체인코드로 인덱스를 패키징해야 합니다.

CouchDB에 관한 자세한 정보와 색인 작성 방법은 Hyperledger Fabric 문서에서 [상태 데이터베이스로서의 CouchDB![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "상태 데이터베이스로서의 CouchDB"){:new_window}를 참조하십시오. 이 [Fabric 튜토리얼 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window}에서 체인코드와 색인을 사용하는 예도 찾을 수 있습니다.

<!--# Next steps-->

## 애플리케이션 호스팅
{: #host-app}

로컬 파일 시스템에 애플리케이션을 호스팅하거나 {{site.data.keyword.Bluemix_notm}}에 푸시할 수 있습니다. 애플리케이션을 {{site.data.keyword.Bluemix_notm}}에 푸시하려면 다음 단계를 완료하십시오.
1. [Cloud Foundry 명령행 설치 프로그램 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/cloudfoundry/cli/releases)을 설치하십시오.  `cf` 명령으로 설치를 테스트하십시오.
    * 설치가 완료되면 사용자의 터미널에 다수의 텍스트 출력이 표시되어야 합니다.
    * "명령을 찾을 수 없음"이 표시되면 설치에 성공하지 못했거나 CF가 사용자의 시스템 경로에 추가되지 않은 것입니다.
2. API 엔드포인트를 설정하고 다음 명령을 실행하여 {{site.data.keyword.Bluemix_notm}} ID 및 비밀번호로 로그인하십시오.
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. 애플리케이션의 디렉토리를 찾아서 다음 명령을 실행하여 애플리케이션을 푸시하십시오. 이는 사용자의 애플리케이션 크기에 따라 몇 분이 소요될 수 있습니다. 터미널의 {{site.data.keyword.Bluemix_notm}}에서 로그를 볼 수 있습니다. 애플리케이션이 제대로 실행되면 로그가 중지됩니다.
```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	다음 명령 중 하나를 실행하여 애플리케이션 로그를 확인할 수 있습니다.
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`

## 네트워크에서 애플리케이션 연결 끊기
{: #disconnect-app}

다음 단계를 완료하여 {{site.data.keyword.cloud_notm}}에서 애플리케이션과 Blockchain 네트워크 사이의 연결을 제거하십시오.
1. 애플리케이션 구성 파일에서 API 엔드포인트 정보를 제거하십시오. 참조사항은 [애플리케이션에 네트워크 API 엔드포인트 추가](#api-endpoints)를 참조하십시오.
2. 체인코드 컨테이너를 삭제하십시오.
  1. 네트워크 모니터의 "채널" 화면에서 체인코드가 설치된 채널을 찾으십시오.
  2. 특정 채널 화면에서 사용 안함으로 설정하려는 체인 코드를 찾으십시오.
  3. **삭제** 단추를 클릭하고 체인코드 삭제 패널에서 **제출**을 클릭하십시오. 체인코드 컨테이너가 제거됩니다.
	![체인코드 삭제](images/channel_chaincode.png "체인코드 삭제")

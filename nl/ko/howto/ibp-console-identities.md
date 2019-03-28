---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:important: .important}
{:note: .note}
{:pre: .pre}

# ID 작성 및 관리
{: #ibp-console-identities}

{{site.data.keyword.blockchainfull_notm}} Platform의 노드는 Hyperledger Fabric을 기반으로 하며 권한이 있는
블록체인 네트워크를 빌드합니다. 즉, 블록체인 컨소시엄의 모든 참가자가 공개 키 인프라에 의해 지속적으로 확인되는
ID를 가져야 합니다. 이러한 ID는 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔에서 조직의 인증 기관(CA)을 통해 작성할 수 있습니다. 해당 ID는 네트워크 작동에 사용될 수 있으므로 콘솔 지갑에 저장해야 합니다. 

**대상 독자:** 이 주제는 블록체인 네트워크를 작성, 모니터링 및 관리할 책임이 있는 네트워크 운영자를 위해 설계되었습니다. 

## 인증 기관 관리
{: #ibp-console-identities-manage-ca}

CA는 자체 CA를 유지보수하는 컨소시엄 내의 각 조직과 함께 여러 당사자 간에 신뢰 앵커 역할을 하며 공개적으로
신뢰할 수 있는 공증인과 유사합니다. CA는 조직에 속하는 ID를 작성하고 각 ID에 공개 및 개인 키를 발행합니다. 이러한
키로 인해 모든 노드 및 애플리케이션이 조치에 서명하고 확인할 수 있습니다. CA가 ID를
설정하기 위해 사용되는 방법에 대한 자세한 정보는 Hyperledger Fabric 문서에서 [ID 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "ID")를
참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 CA를 작성하는 경우,
엔드포인트 URL이 동일한 두 개의 CA, 즉, 루트 CA와 TLS CA가 작성됩니다. 이러한 두 CA를 콘솔의 **노드** 페이지의 동일한 표시 이름 아래에서
볼 수 있습니다. 루트 CA는 노드 및 애플리케이션에 키를 제공합니다. TLS CA는 네트워크 내에서 통신을 보호하는 데 사용되는 인증서를 제공합니다. 네트워크 상의
TLS에 대한 자세한 정보는 [TLS CA 사용](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca)을 참조하십시오.

노드를 작성할 때 루트 CA를 사용하여 네트워크에 대해 배치, 작동 및 상호작용을 수행하는 데 필요한 다음 ID를 작성해야 합니다.
- **CA 관리자:** CA에 기본 CA 관리자 ID가 포함됩니다. 이 ID에는 사용자 이름 및 비밀번호와 관련된 등록 ID와 시크릿이 있으며 이는 CA를 배치하는 동안 지정됩니다. 이 관리 사용자 이름 및 비밀번호를 사용하여 CA를
작동하고 새 ID를 작성할 수 있습니다. 콘솔을 사용하여 CA를 작성한 경우, 별도의 TLS CA 관리자를 작성하기로 결정하지 않은 한
루트 CA의 CA 관리자가 TLS CA의 관리자이기도 합니다.
- **피어 또는 순서 지정자:** 조직에 속한 각 피어 및 순서 지정자에 대해 ID를 등록해야 합니다. 그런 다음,
노드가 배치 동안 해당 ID를 사용하여 네트워크에 참여하는 데 필요한 키를 생성합니다. 보안을 위해 배치하는 각 노드에 대해 고유한 등록 ID 및 비밀번호를 작성하십시오.
- **피어 또는 순서 지정자 관리자:** {{site.data.keyword.blockchainfull_notm}} Platform 노드는
노드 내의 컴포넌트 관리자 ID의 공개 키를 사용하여 배치됩니다. 이러한 인증서는
관리자가 원격 클라이언트에서 또는 콘솔을 사용하여 컴포넌트를 작동할 수 있도록 허용합니다.
- **조직 관리자:** 순서 지정 서비스가 호스팅하는
컨소시엄에 가입하는 경우, 조직의 관리자가 될 ID의 공개 키를 제공합니다. 이러한 ID를 사용하여 채널을 작성하거나 편집할 수 있습니다.
- **애플리케이션:** 애플리케이션이 트랜잭션을 제출하기 전에 네트워크에 의해 유효성 검증을 받도록 서명해야 합니다. 클라이언트
애플리케이션에서 트랜잭션에 서명하는 데 사용할 수 있는 ID를 작성해야 합니다.

콘솔을 사용하여 [등록 프로세스](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register)를 통해
해당 ID를 작성할 수 있습니다. 관리자 ID를 등록한 후에
각 ID에 공개 개인 키를 발행하고 공개 키를 조직 MSP 정의에 제공한 다음 ID를 콘솔 지갑에 추가해야 합니다. [조직 MSP를 작성](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)할
때 하나의 관리자 ID에 대해 해당 단계를 완료할 수 있습니다. 별도의 ID를 조직 관리자 또는 노드 관리자로 사용하거나 하나의 ID를 사용하여
두 태스크를 모두 수행할 수 있습니다. [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은
하나의 ID를 사용하여 튜토리얼에서 작성된 각 조직에 대한 관리자가 됩니다.

## CA ID 설정
{: #ibp-console-identities-ca-identity}

ID에 대해 작업하기 전에 CA 작성 동안 작성된 관리자 ID를 사용하거나 새 ID를 설정하여 CA 관리자의 ID를
설정해야 합니다. **노드** 탭에서 CA를 여십시오. 현재 활성 상태인 ID의 등록 ID는 패널의 맨 위에 있는 CA 이름 옆에
표시됩니다. 해당 관리자 ID를 사용하여,
조직 관리자 및 노드 ID가 될 ID를 등록하는 **등록** 단추 또는
ID를 생성하여 지갑으로 내보내는 **등록**
단추를 통해 기타 ID를 작성할 수 있습니다.

다른 ID로 전환하여 CA 관리자로 사용하려면 **설정** 아이콘을 클릭한 후 슬라이더에서 **ID 설정**을 클릭하십시오. **기존 ID** 탭을 사용하여 지갑에 존재하는 ID를 지정할 수 있습니다. 또는 **새 ID** 탭에서 새 관리자에 대한 인증서를 붙여넣거나 인증서가 포함된 JSON 파일을 업로드할 수 있습니다. 

모든 ID가 새 사용자를 등록할 수 있는 것은 아닙니다. 추가 CA 관리자 설정을 포함한
자세한 정보는 [새 CA 관리자 작성](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin)을 참조하십시오.
{: note}

## ID 등록
{: #ibp-console-identities-register}

ID를 작성하는 첫 번째 단계를 **등록**이라고 합니다. 등록 중에 공개 및 개인 키를 생성하여
해당 ID를 **등록**하기 위해 노드 또는 조직 관리자에 의해 사용될 수 있는 등록 ID 및
시크릿이 작성됩니다.

CA에 새로운 ID를 등록할 때 다음 정보를 입력해야 합니다.

- **등록 ID** 및 **등록 시크릿**: 이 ID에는 사용자 이름 및 비밀번호와 관련된 ID와 시크릿이 있으며
이는 CA를 배치하는 동안 지정됩니다. 이 관리자 ID 및 시크릿을 사용하면 콘솔 또는 Fabric CA 클라이언트를 사용하여 이 ID로 인증서를 작성할 수 있습니다.
- **유형**: CA가 배치될 때 관리자가 CA에 의해 발행되는 ID의 유형을 지정합니다. ID 유형의
일반적인 예는 피어, 순서 지정자 및 클라이언트(애플리케이션) 등입니다. 사용 가능한 유형 목록에서 이 사용자의 ID 유형을 선택하십시오.
- **소속**: 이 사용자가 속할 조직 내 파트입니다. 예를 들어,
애플리케이션 또는 피어를 작동시키는 부서 또는 단위일 수 있습니다. 특정 CA 관리자가 조직에 자체 부서 내의 사용자만 등록할 수 있도록 제한할 수 있습니다.
- **최대 등록 수:** 필요에 따라 이 ID를 사용하여 인증서를 등록하거나 생성할 수 있는 횟수를 입력할 수 있습니다. 제한된 등록 수를
지정하면 노드 및 애플리케이션의 보안을 강화하는 데 도움이 됩니다. 무제한 등록으로 기본값이 지정됩니다.
- **속성**: 필요에 따라, 사용자에 대해 임의의
[속성 기반 액세스 제어 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "속성 기반 액세스 제어") 속성을
사용할 수 있습니다. 예를 들어, 이 절을 사용하여 새 ID를 등록할 수 있는 권한으로
[다른 CA 관리자를 작성](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)할 수 있습니다. Fabric CA
사용자 안내서의 [새 ID 등록 ![외부  링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "새 ID 등록")
절에서 사용 가능한 Fabric CA 속성의 전체 목록을 볼 수 있습니다.

CA 개요 패널에서 **사용자 등록** 단추를 클릭하여 새 사용자를 작성하십시오. 이 태스크를
수행하기 전에 새 사용자를 등록하는 기능이 있는 ID를 사용하여 [ID를 설정](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)했는지
확인하십시오. 일반적으로 `admin` 사용자입니다. 단추가 회색이면 사용자가 ID를 설정하지 않았거나 ID가 새 ID를 작성할 수 없음을 의미합니다.  

**사용자 등록**을 클릭하면 일련의 사이드 패널이 열립니다.
1. 첫 번째 사이드 패널에 새 ID의 **등록 ID** 및 **등록 시크릿**을 입력하십시오. 이 값은 콘솔에 저장되지 않으므로 **해당 값을 저장**하십시오.
2. 두 번째 사이드 패널에서 ID **유형**을 선택하십시오. 드롭 다운 목록에 이 CA가 지원하는 유형의 목록이 포함되어 있습니다. 그런 다음 새 ID가
속할 **소속**을 선택하십시오. 드롭 다운 목록에서 기존 소속을 선택하거나 새 소속을 입력할 수 있습니다.
3. 세 번째 사이드 패널에서 이 ID에 허용되는 **최대 등록 수**를 입력하십시오. 값을 지정하지 않으면 무제한 등록으로 기본값이 지정됩니다.
4. 마지막 패널에서 작성 중인 ID의 **속성**을 추가하십시오.

**등록**을 클릭한 후에는 새 ID가 CA에 의해 작성된 **인증된 사용자** 목록에
추가될 수 있습니다. ID는 **등록 ID** 기준으로 나열되고
**유형** 및 **소속**이 함께 나열됩니다. 표의 ID를 클릭하면
등록 동안 작성된 **최대 등록** 수 및 **속성**을 볼 수 있는 사이드 패널이 열립니다.

### 새 CA 관리자 작성
{: #ibp-console-identities-ca-admin}

기본적으로 배치 동안 작성된 CA 관리자만이 새 ID를 등록할 수 있습니다. 또한
등록 프로세스의 **속성** 패널을 사용하여 새 사용자를 등록할 수 있는 기능을 통해 기타 ID를 작성할 수 있습니다.

사이드 패널 4에서 **속성 추가** 단추를 클릭하십시오. `hf.Registrar.Roles`의 **속성 이름**을
제공하십시오. **속성 값**으로 `*`을 입력하십시오. 또한 이 패널을 사용하여
클라이언트, 피어 등의 특정 유형 또는 특정 소속 내의 의 ID 유형만 등록할 수 있는 ID를 작성할 수 있습니다. ID 및 ID가 발행한 모든 인증서를
취소할 수 있는 기능이 있는 ID도 작성할 수 있습니다. Fabric CA
사용자 안내서의 [새 ID 등록 ![외부  링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "새 ID 등록")
절에서 속성의 전체 목록을 볼 수 있습니다.

## ID 등록
{: #ibp-console-identities-enroll}

CA에 등록된 각 ID에 대해 공용 인증서와 개인 키를 생성할 수 있습니다. 추가 관리자 ID를 CA에 등록한 경우 관리자 ID에 대한 키를 생성한 후 [조직 MSP을 작성](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)할 때 이 키를 추가로 포함시키십시오. 

ID를 등록하기 전에 [ID를 설정](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)해야 CA가 작동될 수 있습니다. 일반적으로 CA를 작성할 때 지정된 관리자 ID로 설정하십시오. CA가 해당 ID로 설정되었는지 확인하려면 CA 세부사항 페이지를 조사하고 CA 이름 옆에 있는 현재 활성 상태인 ID의 등록 ID를 확인하십시오. ID가 관리자 ID로 설정되었는지 확인한 후에는 사용자의 오버플로우 메뉴에서 **ID 등록**을 클릭하여 CA에 등록된 사용자에 대해 인증서와 키를 생성하십시오. 

- 사용자의 `등록 시크릿`을 입력하십시오. 
- 다음 단계에서는 생성된 키가 표시됩니다. 
  - 공개 키는 **인증서** 필드에 표시됩니다. 이 인증서를 등록 인증서, 서명 인증서 또는 signCert라고도 합니다. signCert는 VSCode 확장으로 클라이언트 애플리케이션을 작성할 때 사용될 수 있도록 로컬 시스템의 파일로 내보내야 합니다. 
  - **개인 키** 필드에서 해당되는 개인 키를 찾을 수 있습니다. 마찬가지로, 개인 키도 VSCode 확장으로 작성된 클라이언트 애플리케이션에서 사용될 수 있도록 로컬 시스템으로 내보내야 합니다. 
  - **ID 등록**을 클릭하여 작성된 인증서 및 개인 키는 한 번만 생성되고 콘솔이나 브라우저에 저장되지 않습니다. **ID 등록** 단추를 클릭하면
CA 관리자에 대해 설정한 최대 등록 수에 대해 계수됩니다. 이 등록 과정의 일부로 ID를 로컬 파일 시스템에 다운로드하거나 콘솔 지갑에 추가하여 키 쌍을 저장해야 합니다. 이 공개 및 개인 키 쌍의 새 이름을
검색할 수 있도록 **이름** 필드에 입력하십시오.
- **중요:** **ID 내보내기**를 클릭하여 인증서와 키를 로컬 파일 시스템에 JSON 형식의 단일 파일로 다운로드하십시오. 이러한 키를 보호하고 관리할 책임은 사용자 본인에게 있습니다. 
- **지갑에 ID 추가**를 클릭하여 이러한 인증서를 콘솔 지갑에 추가하십시오. 그런 다음
[지갑 탭](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)의
새 타일에서 이 ID의 이름 및 키를 찾을 수 있습니다.

또한 Fabric CA 클라이언트 또는 Fabric SDK를 사용하여 콘솔에서 작성한 ID를 등록할 수 있습니다. 콘솔은 이러한 단계를 완료하는 데 필요한 모든 정보를
제공합니다. 등록 중에 지정한 **등록 ID**와 **등록 시크릿**을 저장했는지 확인하십시오. 

## TLS CA 사용
{: #ibp-console-identities-tlsca}

네트워크 내의 통신은 TLS 인증서에 의해 보안됩니다. TLS는 사용자 노드와 애플리케이션 사이에서 통신을 암호화합니다. TLS를
사용하면 공격자가 노드 간의 통신을 방해하거나 애플리케이션에서 제출된 트랜잭션을 읽는 것을 방지할 수 있습니다. TLS에
사용되는 키와 인증서는 트랜잭션을 서명하고 유효성을 검증하는 데 사용되는 인증서와는 다르며 별도의 인증 기관에서 발행합니다.

{{site.data.keyword.blockchainfull_notm}} Platform 콘솔에서 작성된 각 CA는 루트 CA 및 TLS CA를 포함합니다. 이러한
두 CA를 콘솔의 노드 탭의 동일한 표시 이름 아래에서
볼 수 있습니다. CA 개요 패널을 클릭한 다음 **TLS 인증 기관**을 클릭하여 TLS CA를 작동시키십시오. TLS CA에는 루트 CA와
[동일한 등록](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) 및
[등록 프로세스](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll)가
있습니다. 두 개의 CA 모두 동일한 CA 관리자 ID 및 시크릿으로 배치됩니다.

사용자가 배치하는 각 피어 또는 순서 지정자 노드가 공개 TLS 인증서를 생성해야 합니다. 콘솔을 사용하여 노드를 작성할 때
TLS CA에 등록된 ID의 등록 ID 및 시크릿을 제공하도록 요청됩니다. [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)은
편의 상 CA 관리자 ID를 사용합니다. 그러나 각 노드에 대해 TLS CA에 고유한 ID를 등록하는 것이 가장 좋은 방법입니다. 노드는 배치 중에 이 ID를
사용하여 TLS 인증서를 생성합니다. 이 인증서는 순서 지정자 또는 피어와 통신해야 애플리케이션에 필요합니다. 노드 개요 패널로 이동하고
설정을 클릭하여 노드의 TLS 인증서를 찾을 수 있습니다. 또한
[스마트 계약 탭에서](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK) 다운로드할 수 있는
연결 프로파일에서 피어 및 순서 지정자의 TLS 인증서를 찾을 수 있습니다.

콘솔을 사용하여 피어 또는 순서 지정자를 작성할 때 TLS CA를 사용하여 각 노드의 추가 도메인 이름을 지정할 수 있습니다. 순서 지정자 또는
피어를 배치할 때 **TLS CSR 호스트 이름** 필드에 새 도메인 이름을 입력하십시오. 이 호스트 이름은 노드에 발행된 TLS 인증서의
공통 이름 목록에 추가됩니다.


## 인증서 만기
{: #ibp-console-identities-expiration}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0 CA를 사용하여 생성된 인증서는 일 년 후에 만기됩니다. 만기
기간은 Fabric SDK, Fabric CA 클라이언트 또는 콘솔을 사용하여 생성된 인증서와 동일합니다. 인증서가
만기되면 애플리케이션이 더 이상 네트워크와 상호작용할 수 없으며 새 인증서를 생성하려면 다시 등록해야 합니다. 사용자의 등록 제한이 적용되는 경우
새 사용자를 등록한 다음 등록할 수 있습니다.

명령행을 사용하여 인증서 만기 날짜를 확인할 수 있습니다. 먼저 로컬 시스템에서 다음 명령을 실행하여
base64 형식의 인증서를 PEM 형식으로 변환해야 합니다.

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

다음 명령을 실행하여 PEM 인코딩 인증서를 사용자가 읽을 수 있는 양식으로 표시하십시오.
```
  openssl x509 -in <certificate file> -text
```
{:codeblock}

인증서는 아래 예와 유사하게 표시됩니다.

```
Certificate:
    Data:
        Version: 3 (0x2)
    Serial Number:
        20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18
Signature Algorithm: ecdsa-with-SHA256
    Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA
    Validity
        Not Before: Nov 28 19:18:00 2018 GMT
        Not After : Nov 28 19:23:00 2019 GMT
    Subject: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin
    ...
    ...
```

**Validity** 섹션의 `Not After:` 뒤에서 만기 날짜를 찾을 수 있습니다. 이 예에서는 인증서가 2019년 11월 28일에 만기됩니다.

## 콘솔 지갑에 ID 저장
{: #ibp-console-identities-wallet}

지갑은 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔이
네트워크의 노드를 작동시키는 데 사용하는 ID 및 키를 저장합니다. 콘솔을 사용하여 채널 및 스마트 계약에
대한 작업을 수행하려면 피어, 순서 지정자 및 조직 관리자를 이 지갑에 추가해야 합니다. 또한 지갑을 사용하여
애플리케이션에서 사용할 ID를 편리하게 저장할 수 있습니다. 언제라도 지갑을 사용하여 내보낼 수 있습니다. 왼쪽 탐색을 사용하여 지갑 개요 패널을 찾아보십시오. 개요
화면을 사용하여 이 지갑에서 ID를 추가, 업데이트 및 내보낼 수 있습니다.

지갑은 콘솔의 컴포넌트이며 별도의 서비스가 아닙니다. 지갑은
로컬 파일 시스템 대신 콘솔에 액세스하는 데 사용하는 브라우저의 로컬 스토리지에 키를 저장합니다. 다른 브라우저에서 콘솔에 액세스하거나
개인용 찾아보기 세션을 시작하는 경우, 지갑에 저장된 ID에 액세스할 수 없습니다. **콘솔에서 관리자 ID를 내보내어 안전한 장소에
저장하도록 적극 권장합니다**.
{:important}

### ID 추가
{: #ibp-console-identities-add}

[조직 MSP를 작성](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)할
때 지갑에 관리자 ID를 추가할 수 있습니다. 콘솔에 의해 관리되는 CA 또한
[등록 프로세스](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) 동안
지갑에 ID를 추가할 수 있습니다.

개요 화면에서 **ID 추가** 단추를 사용하여 전자 지갑에 직접
ID를 가져올 수 있습니다. 이 단추를 클릭하면 ID의 공개 및 개인 키 쌍을 추가할 수 있는 사이드 패널이 열립니다.
- **이름:** 참조용으로만 사용되는 ID 이름을 입력하십시오.
- **인증서:** CA를 사용하여 생성된 ID의 공개 키에 붙여넣으십시오.
- **개인 키:** CA를 사용하여 생성된 ID의 개인 키에 붙여넣으십시오.


아래 형식으로 JSON 파일을 업로드하여 ID를 추가할 수도 있습니다. 또한 **JSON 업로드** 단추를
사용하여 다중 ID를 한 번에 업로드할 수 있습니다.

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Fabric CA 클라이언트 또는 패브릭 SDK를 사용하여 ID를 등록한 경우에는 키를 PEM 형식에서 base64 형식으로 변환해야 합니다. 로컬 시스템에서 다음 명령을 실행하여 인증서를 base64 형식으로 변환할 수 있습니다.
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

### ID 보기 및 업데이트
{: #ibp-console-identities-update-identities}

**지갑** 탭에서 지갑에서 ID를 보거나 업데이트하거나 제거할 타일을 클릭하십시오. 인증서가 만기되어 CA에서 새 키를 발행해야 하는 경우에는
사용자의 ID를 업데이트해야 할 수도 있습니다. 또한 이 탭을 사용하여 콘솔 및 로컬 시스템에서 키를 삭제할 수 있습니다.

ID를 클릭하면 base64 형식으로 공개 및 개인 키를 표시하는 사이드 패널이 열립니다. **내보내기**를
클릭하여 해당 인증서를 로컬 파일 시스템에 다운로드하십시오. 지갑의 ID 이름을 변경하거나 새 키 세트를 패널에
붙여넣으려면 ** 업데이트**를 클릭하십시오. 이 ID를 더 이상 사용할 필요가 없으며 키를 삭제하고자 하는 경우 **제거**를 클릭하십시오.

## ID 연관
{: #ibp-console-identities-associate-admin}

조직의 공개 키 및 노드 관리자를
[조직 MSP 정의](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)에 제공해야
합니다. 콘솔에 의해 작성된 노드 또는 채널은 MSP 정의의 인증서를 사용하여 누가 유효한 관리자인지 확인합니다. 결과적으로
MSP 정의에 관리자 인증서를 추가하는 데 사용한 것과 동일한 공개 개인 키 쌍이 콘솔 지갑에 저장됩니다.

콘솔을 사용하여 순서 지정자 또는 피어를 작성하는 경우 **ID 연관** 패널이 표시됩니다. 조직 MSP 정의 내에 인증서도
있는 지갑에서 ID를 선택하십시오. 또한 채널을 작성하거나 편집할 때도 **ID 연관** 섹션에서
관리자 ID를 선택해야 합니다. 이렇게 하면 콘솔에서 피어, 순서 지정자 및 순서 지정 서비스 컨소시엄과 통신할 때 사용할 ID를 알 수 있습니다.

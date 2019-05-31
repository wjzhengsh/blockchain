---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: organizations, MSPs, create an MSP, MSP JSON file, consortium, system channel

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 조직 관리
{: #ibp-console-organizations}

{{site.data.keyword.blockchainfull}} Platform 콘솔을 사용하여 MSP(Membership Services Provider)로
알려진 정규 조직 정의를 작성할 수 있습니다. 조직의 MSP 정의를 통해 블록체인 컨소시엄의 다른 구성원이 노드 및 애플리케이션의 ID를
확인할 수 있습니다. 또한 MSP 정의에는 조직의 관리자 인증서도 포함되어 있습니다.

콘솔을 사용하여 어떤 조직이 네트워크의 구성원인지 관리할 수도 있습니다. 순서 지정 서비스의
관리자는 조직 탭을 사용하여 블록체인 [컨소시엄](/docs/services/blockchain/glossary.html#glossary-consortium)에
구성원을 추가할 수 있습니다. 그런 다음 컨소시엄의 구성원이 콘솔을 사용하여 새 채널 또는 기존 채널에 구성원을 추가할 수 있습니다.

**대상 독자:** 이 주제는 블록체인 네트워크를 작성, 모니터링 및 관리할 책임이 있는 네트워크 운영자를 위해 설계되었습니다.

## MSP 이해
{: #console-organizations-about-msp}

{{site.data.keyword.blockchainfull_notm}} Platform은 Hyperledger Fabric을 기반으로 하며
권한이 부여된 블록체인 네트워크를 빌드합니다. 참가자가 네트워크를 제출하고 원장 상의 자산과 상호작용하려면
먼저 네트워크에 알려져야 합니다. Fabric은 컨소시엄이라고 하는 초대된 조직 그룹을 통해 ID를 인식합니다. 컨소시엄 내의
조직은 구성원에게 올바른 인증 정보를 제공하고 구성원으로 하여금 네트워크의 참가자가 되도록 할 수 있습니다. 그러면
참가자가 블록체인 노드를 작동하여 클라이언트 애플리케이션에서 트랜잭션을 제출합니다.

컨소시엄의 각 조직은 루트 CA로 알려진 자체 인증 기관을 운영해야 합니다. 이 인증 기관(또는 중간 CA)은 조직에 속하는 모든 ID를 작성하고 각 ID에 설명 인증서 및 개인 키를 발행합니다. 이러한 키는 인증 기관에 의해 서명되고
조직의 구성원이 조치에 서명 및 확인하는 데 사용합니다. 컨소시엄에 가입하면
다른 조직에서 사용자의 CA 서명을 인식하고 피어 및 애플리케이션이 올바른 참가자임을 확인할 수 있습니다. Hyperledger Fabric의
멤버십에 대한 자세한 정보는 Fabric 문서에서 [멤버십 개념 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "멤버십")를
참조하십시오.

조직이 컨소시엄에 가입하기 전에 **MSP(Membership Services Provider)**로
알려진 조직 정의를 작성해야 합니다. MSP에는 다음 정보가 포함됩니다.
- **루트 인증 기관**에 의해 서명된 인증서. 이 인증서는 노드, 채널 및 애플리케이션의 ID를 확인하는 데 사용됩니다.
- **TLS CA**에 의해 서명된 인증서. 루트 TLS 인증서는 사용자의 피어가 교차 조직 gossip에 참가할 수 있도록
허용하며 이는 Hyperledger Fabric의 [**개인용 데이터** 콜렉션](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) 및 [서비스 발견 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "서비스 발견") 기능을 사용하기 위해 필수입니다.
- **MSP ID**. MSP ID는 컨소시엄 내에서 조직의 정규 이름입니다. 노드 및 애플리케이션에 대한 MSP ID를 기억해야 합니다.
- **Peer admin** 및 **Org Admin** ID의 **Admin 인증서**. 이러한 인증서는
순서 지정 서비스로 전달되며 조직 내의 어떤 ID가 채널 작성 또는 편집이 가능한지 확인하는 데 사용됩니다. 콘솔을 사용하여 순서 지정자 또는 피어를 작성하는 경우
MSP 내의 관리자 인증서가 새 노드 내에 배치됩니다. 그런 다음 이러한 인증서를 사용하여 콘솔 또는 클라이언트 애플리케이션에서 피어 또는 순서 지정자를 작동시킬 수 있습니다.

## 콘솔에서 MSP 관리

**조직** 탭으로 이동하십시오. 이 탭을 사용하면 콘솔에 존재하는 인증 기관을 사용하여 [MSP 정의를 작성](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp)할 수 있습니다. 또한 이 탭을 사용하여 다른 조직에서 작성한 [MSP를 가져올](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) 수 있습니다.

**사용 가능한 조직** 아래에서 작성하거나 가져온 모든 MSP를 볼 수 있습니다. 콘솔 내의
중요한 기능에 대해 조직 탭에서 MSP 정의를 사용할 수 있습니다.
- 피어 또는 순서 지정자 노드를 작성하는 경우 조직의 MSP가 새 노드에 관리자 인증서를 제공하는 데 사용됩니다.
- 순서 지정 노드의 관리자인 경우, MSP를 사용하여 [컨소시엄에 새 조직을 추가](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium)할 수 있습니다.
- 컨소시엄의 구성원인 경우, 다른 컨소시엄 구성원의 MSP를 콘솔로 가져온 다음 구성원을 신규 또는 기존 채널에 추가할 수 있습니다.

## 조직에 대한 MSP 작성
{: #console-organizations-create-msp}

조직에 대한 MSP 정의를 생성하려면 **조직** 탭을 사용하십시오. **MSP 작성**을
클릭하면 모든 필수 정보(루트 CA, MSP ID, 조직 관리자 등)를 입력할 수 있는 사이드 패널이 열립니다.

- 패널의 **MSP 세부사항** 섹션을 사용하여 조직 MSP ID를 선택하십시오. 이 ID는
네트워크의 다른 구성원이 사용자의 조직을 언급하기 위해 사용할 정규 이름입니다. **MSP ID를 저장하십시오.**

- **루트 인증 기관 세부사항** 섹션을 사용하여 조직의 루트 CA를 선택하십시오. 루트 CA는
조직에 속하는 모든 노드 및 애플리케이션 ID를 작성하기 위해 사용하거나 사용할 CA입니다. 중간 CA를 사용하는 경우에는 해당 CA를 작성하는 데 사용한 CA입니다. 콘솔을 사용하여 관리되는 CA 목록에서
루트 CA를 선택하십시오. 콘솔을 사용하여 CA를 작성한 경우, 루트 CA를 선택하면 루트 TLS 인증서도 작성됩니다.

- 또한 **루트 인증 기관 세부사항** 섹션을 사용하여 조직 관리자 인증서 중 하나를 생성할 수 있습니다. 조직
MSP 정의를 작성하기 전에 루트 CA에 조직 및 노드 관리자를 등록해야 합니다. 그런 다음 이러한 ID를 사용하여 네트워크를 작동시키려면 다음 단계를 완료해야 합니다.

  1. 각 관리자 ID에 대해 서명 인증서 및 개인 키를 작성하십시오.
  2. MSP 정의 내 각 관리자 ID의 서명 인증서를 제공하십시오.
  3. 콘솔 지갑에 ID를 추가하십시오. 콘솔에 의해 작성된 노드 또는 채널은 MSP의
인증서를 사용하여 누가 유효한 관리자인지 확인합니다. 결과적으로 MSP에 관리자 인증서를 추가하는 데 사용한 것과 동일한 서명 인증서 및 개인 키 쌍이 콘솔 지갑에 저장되어야 합니다.

  **MSP 정의 작성** 패널을 사용하여 관리자 ID 중 하나에 대해 해당 조치를 완료할 수 있습니다. 루트 CA를
선택한 다음 **조직 관리자 인증서 생성** 섹션의 다음 단계를 완료하십시오.
  1. 루트 CA에 등록된 관리자 ID의 등록 ID와 등록 시크릿을 입력하십시오. 등록 ID와 등록 시크릿을 입력한 다음 콘솔 지갑에 ID를 표시할 이름을 선택하십시오.
  2. **생성**을 클릭하십시오. 그러면 인증서 및 개인 키가 생성되고 자동으로 키가 콘솔 지갑에 추가됩니다. 그러면
사용자가 이 패널에서 선택한 이름을 사용하여 지갑에서 관리자 ID를 찾을 수 있습니다. 이러한 키는 브라우저 로컬 스토리지에만
저장되므로 브라우저를 변경하면 콘솔 지갑에서 찾을 수 없습니다. 원래 브라우저에서 ID를 내보내고 새 브라우저의 콘솔 지갑으로 가져와야 합니다.
  3. 그런 다음 **내보내기**를 클릭하여 키 쌍을 파일 시스템으로 다운로드하여 보안을 설정하십시오.

- 사이드 패널의 **관리자(선택사항)** 섹션에는 관리자의 서명 인증서 키가 포함되어 있습니다. **조직 관리자 인증서 생성** 섹션을 사용하여 생성된 인증서는 **관리자 인증서** 필드의 첫 번째 행에서 찾을 수 있습니다. 다중 관리자 ID를 사용하여 네트워크를
운영하려면 추가 노드 또는 조직 관리자의 인증서를 **관리자 인증서** 필드로 붙여넣으십시오.

관리자 인증서가 MSP 정의를 사용하여 노드 및 채널로 전달되므로 각 노드 및 조직 관리자 인증서가
MSP에 저장되는지 확인해야 합니다. 콘솔을 사용하여 순서 지정자, 피어 또는 채널을 작성하는 경우,
MSP 정의에 제공된 관리자 인증서를 사용하여 콘솔 지갑에 내보낸 ID 중 하나를 **연관**시켜야 합니다. **ID 연관** 섹션 또는
패널이 표시되면 MSP 정의를 작성할 때 생성하여 지갑에 저장한 ID를 선택하십시오.

루트 CA, MSP ID를 선택하고 관리자 인증서를 작성한 다음 **MSP 정의 작성**을 클릭하여 MSP 정의를
작성하십시오. 이제 조직 탭에 조직으로 나열됩니다. 노드를 배치하고 블록체인 컨소시엄에 가입할 때 MSP 정의를 사용할 것입니다.

## 수동으로 MSP JSON 파일 빌드
{: #console-organizations-build-msp}

**이 옵션은 인증서가 블록체인 ID 관리에 사용되는 방법에 익숙한 고급 사용자를 대상으로 합니다.**

{{site.data.keyword.IBM_notm}}에서 호스팅되지 않는 **외부 CA**에서 피어 또는 순서 지정 서비스의 인증서를 사용하려는 경우 피어 또는 순서 지정 서비스 조직 MSP 정의를 표시하는 MSP 정의 JSON 파일을 빌드해야 합니다. 

모든 인증서는 base64 형식으로 인코딩되어야 합니다.
{:important}

로컬 시스템에서 다음 명령을 실행하여 `PEM` 형식으로 된 인증서 파일 `<cert.pem>`의 컨텐츠를 base64 문자열로 변환할 수 있습니다.

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}


다음 형식을 사용하여 JSON 파일을 작성하십시오.

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name**: 콘솔에서 이 MSP 정의를 식별하는 데 사용할 이름을 지정합니다.
- **organization_id**: 콘솔에서 이 MSP를 내부적으로 표시하는 데 사용할 ID를 지정합니다.
- **root_certs**: (선택사항) `base64` 형식으로 된 외부 CA에서 하나 이상의 루트 인증서가 포함된 배열을 붙여넣습니다. CA 루트 인증서 또는 중간 CA 인증서를 제공해야 하며, 둘 다 제공할 수도 있습니다.
- **intermediate_certs**: (선택사항) `base64` 형식으로 된 외부 중간 CA에서 하나 이상의 인증서가 포함된 배열을 붙여넣습니다. CA 루트 인증서 또는 중간 CA 인증서를 제공해야 하며, 둘 다 제공할 수도 있습니다.
- **admins**: `base64` 형식으로 된 조직 관리자의 서명 인증서에 붙여넣습니다. 
- **tls_root_certs**: (선택사항) `base64` 형식으로 된 외부 TLS CA에서 하나 이상의 루트 인증서가 포함된 배열을 붙여넣습니다. 외부 TLS CA 루트 인증서 또는 외부 중간 TLS CA 인증서를 제공해야 하며, 둘 다 제공할 수도 있습니다.
- **tls_intermediate_certs**: (선택사항) `base64` 형식으로 된 중간 TLS CA에서 하나 이상의 인증서가 포함된 배열을 붙여넣습니다. 외부 TLS CA 루트 인증서 또는 외부 중간 TLS CA 인증서를 제공해야 하며, 둘 다 제공할 수도 있습니다.  

다음 추가 필드가 MSP 정의에도 사용 가능하지만 필수는 아닙니다.
- **organizational_unit_identifiers**: 이 MSP의 유효한 멤버가 X.509 인증서에 포함되어야 하는 OU(Organizational Units)의 목록입니다. 이는 다중 조직이 동일한 신뢰 루트 및 중간 CA를 활용할 때 사용되는 선택적 구성 매개변수이고, 다중 조직은 멤버를 위해 OU 필드를 예약했습니다. 조직은 종종 다중 조직 단위로 구분되고 각 조직 단위에는 일련의 책임이 있습니다. 예를 들어, ORG1 조직에는 개별적인 비즈니스 부문을 반영하도록 ORG1-MANUFACTURING 및 ORG1-DISTRIBUTION OU가 모두 포함될 수 있습니다. CA에서 X.509 인증서를 발행하면 인증서의 OU 필드는 ID가 속하는 비즈니스 부문을 지정합니다. 자세한 정보는 [ID 분류 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "ID 분류")의 Fabric 문서에서 이 주제를 참조하십시오.   
- **fabric_node_OUs**: ID 분류를 사용으로 설정하는 패브릭 특정 OU입니다. `NodeOUs`에는 OU를 기반으로 클라이언트, 피어 및 순서 지정자를 구분하는 방법에 대한 정보가 포함되어 있습니다. 사용을 true로 설정하여 검사가 실행되면 MSP는 ID 유형이 `client`, `peer` 또는 `orderer`인 경우 ID가 유효하다고 간주합니다. ID는 이러한 특수 OU 중 하나여야 합니다. Fabric Service Discovery 문서에서 [MSP에서 `fabric_node_OU`를 지정하는 방법 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query "구성 조회")에 대한 예는 이 주제를 참조하십시오. 
- **revocation_list**: 더 이상 유효하지 않은 인증서 목록입니다. X.509 기반 ID의 경우 이 ID는 SKI(Subject Key Identifier) 및 AKI(Authority Access Identifier)로 알려진 문자열의 쌍이며, 인증서가 취소되지 않았는지 확인하기 위해 X.509 인증서를 사용할 때마다 선택됩니다. [인증서 취소 목록 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "인증서 또는 ID 취소")에 대한 자세한 정보는 Fabric 문서의 이 주제를 참조하십시오.

예를 들어, JSON 파일은 다음과 유사합니다.

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

이 정의를 MSP 정의 `JSON` 파일로 저장하십시오.  

피어 또는 순서 지정 서비스 노드의 조직을 정의하고 외부 CA의 인증서를 사용하는 MSP 정의를 구성했습니다. 이제 [피어 또는 순서 지정자로 외부 CA의 인증서를 사용하는 방법](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca)에 설명된 지시사항으로 돌아갈 수 있습니다.

## MSP 가져오기
{: #console-organizations-import-msp}

순서 지정자 관리자만이 새 조직을 컨소시엄에 추가할 수 있습니다. 순서 지정자 관리자인 경우,
컨소시엄에 초대된 모든 조직의 MSP 정의를 수집하여 MSP를 콘솔로 가져와야 합니다. 그런 다음 MSP를
순서 지정자 노드를 사용하여 순서 지정 서비스에 추가할 수 있습니다.

관리자가 MSP 정의를 작성한 후에 조직 탭을 사용하여
MSP를 JSON 형식으로 로컬 파일 시스템에 다운로드할 수 있습니다. 그런 다음 대역 외 오퍼레이션에서 MSP JSON 파일을 사용자에게 전송할 수 있습니다. **조직** 탭으로
이동하여 **MSP 정의 가져오기**를 사용하여 MSP 파일을 콘솔로 가져오십시오. 일단 MSP 정의가
**사용 가능한 조직** 섹션에 표시되면 순서 지정자 노드로 이동하여
[컨소시엄에 조직을 추가](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium)할 수 있습니다.


## 컨소시엄에 조직 추가
{: #console-organizations-add-consortium}

조직의 컨소시엄은 순서 지정 서비스에 의해 호스팅됩니다.

순서 지정 서비스의 관리자인 경우, 콘솔을 사용하여 컨소시엄에 조직을 추가할 수 있습니다. **노드** 탭으로 이동하여 순서 지정 노드를 클릭하십시오. 순서 지정 노드 패널의 **컨소시엄 구성원**
아래에서 **조직 추가**를 클릭하십시오. 그러면
[조직 탭으로 가져온](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp)
사용 가능한 MSP 정의 목록에서 선택할 수 있는 사이드 패널이 열립니다. 또한 **JSON 업로드** 옵션을
사용하여 직접 다른 조직에 의해 작성된 MSP 정의 파일을 가져올 수 있습니다.

## 채널 작성 및 편집
{: #console-organizations-create-channel}

조직이 컨소시엄에 추가되면 해당 조직은 순서 지정 서비스를 사용하여 새 채널을 작성하거나 기존 채널에 추가될 수 있습니다. 피어를 채널에 가입시키고 스마트 계약을 인스턴스화하며 트랜잭션을 제출하는 것과 같이 채널에 참가하기 위해 필요한 정보가 MSP 정의를 사용하여 제공됩니다.

조직이 컨소시엄에 추가된 후에 다음 단계에 따라 채널을 작성할 수 있습니다.

1. 컨소시엄을 호스팅하는 순서 지정 노드를 콘솔로 가져오십시오. 사용자가 순서 지정 노드의 관리자일 필요는 없습니다. 그러나 순서 지정자 노드 이름과 엔드포인트 정보를 콘솔에 제공해야 합니다.
2. **조직** 탭에서 새 채널에 추가할 조직의 MSP를 콘솔로 가져오십시오. 조직이 채널에 추가되려면 먼저 컨소시엄에 추가되어야 함에 **유의하십시오**.
3. **채널**로 이동하여 **채널 작성**을 클릭하십시오. 그러면 채널 이름, 멤버십 및 채널 정책을 지정할 수 있는 사이드 패널이 열립니다. 컨소시엄에 추가된 임의의 조직을 새 채널에 추가할 수 있습니다.

이러한 단계에 대한 자세한 정보는 **네트워크 빌드** 튜토리얼에서 [채널 작성](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1)을 참조하십시오.

### 채널 정의의 MSP 업데이트
{: #console-organizations-update-channel}

시간 경과에 따라 이미 채널과 연관된 MSP 정의의 인증서를 업데이트해야 할 수 있습니다. 이러한 상황이 발생하는 경우 다음 단계에 따라 채널에 있는 조직의 MSP 정의를 업데이트하십시오.  

1. 콘솔에서 **채널** 탭으로 이동하십시오.
2. 업데이트할 조직 MSP를 포함하는 채널을 클릭하여 여십시오.
3. **채널 세부사항** 탭을 클릭하십시오.
4. 업데이트할 연관된 채널 구성원의 타일을 클릭하십시오.
5. 업데이트된 MSP 정의를 콘솔로 아직 가져오지 않은 경우 여기서 파일을 업로드할 수 있습니다. **참고:** 이 조치는 조직 탭에서 연관된 MSP 정의를 업데이트하지 않습니다. 콘솔의 조직 탭에서 MSP 정의를 이미 업데이트한 경우 드롭 다운 목록에서 선택할 수 있습니다.

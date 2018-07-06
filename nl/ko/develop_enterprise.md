---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 엔터프라이즈 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-network}

{{site.data.keyword.blockchainfull}} Platform: Develop의 개발자 도구를 사용하면 비즈니스 네트워크 아카이브(`.bna`)에 패키지할 수 있는 **비즈니스 네트워크 정의**를 작성할 수 있습니다. 개발자 환경에서는 `.bna` 파일을 개발 및 공유를 위한 로컬 또는 클라우드 {{site.data.keyword.blockchain}}에 배치할 수 있습니다.

이 튜토리얼에서는 비즈니스 네트워크 라이프사이클의 다음 단계, 즉 `.bna`를 {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에 배치하여 비즈니스 네트워크를 활성화하는 단계를 다룹니다.

## 시작하기 전에

{{site.data.keyword.blockchainfull}} Platform: Develop 개발자 환경을 설치했고 비즈니스 네트워크 개발 및 배치에 익숙한지 확인하십시오. 비즈니스 네트워크 작성에 대한 안내는 [Hyperledger Composer 문서](https://hyperledger.github.io/composer/latest/business-network/business-network-index)에 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform의 엔터프라이즈 플랜 인스턴스에 대한 액세스 권한이 있어야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에 대한 자세한 정보는 [엔터프라이즈 플랜 개요](./enterprise_plan.html)를 참조하십시오.

## 1단계: {{site.data.keyword.blockchainfull_notm}} Platform에 대한 연결 프로파일 작성

1. 연결 세부사항을 저장할 디렉토리를 작성하십시오. 예를 들어, 다음과 같습니다.

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

    각 연결 프로파일에 `connection.json` 파일이 있어야 합니다. 이 인스턴스 `bmx-hlfv1`에서 `.composer-connection-profiles` 아래에 새 디렉토리를 작성하십시오. 이는 Hyperledger Composer 및 {{site.data.keyword.blockchainfull_notm}} Platform에 대한 작업을 수행할 때 사용할 프로파일의 이름이 됩니다.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

2. 이제 다음 디렉토리 구조를 사용합니다.

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

    새로 작성된 디렉토리에 파일을 작성하고 이름을 `connection.json`으로 지정하십시오. `connection.json` 파일에 다음 템플리트를 사용할 수 있습니다.

    ```
        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }
    ```
    {:codeblock}

    새로 작성된 `connection.json` 파일에 {{site.data.keyword.blockchainfull_notm}} Platform 대시보드를 통해 제공된 속성을 입력합니다. 대시보드에서 **개요**를 선택한 다음 **연결 프로파일** 단추를 클릭하여 채널 구성원에 대한 엔드포인트 및 인증서 정보를 표시하십시오.

## 2단계: 순서 지정자 정보 추가

1. 이제 연결 프로파일이 제공하는 정보로 템플리트를 수정할 수 있습니다. 연결 프로파일에는 여러 순서 지정자가 있을 수 있지만 `connection.json` 파일에는 하나만 필요합니다.

    템플리트의 순서 지정자 URL 값을 연결 프로파일의 관련 정보(다음 형식 사용)로 바꾸십시오.

    ```
    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
    ```
    {:codeblock}

## 3단계: 인증 기관 정보 추가

1. **certificateAuthorities** 섹션의 항목 중 하나에서 `connection.json`의 ca 값을 **url** 및 **caName**으로 바꾸십시오.

## 4단계: 피어 정보 추가

1. 각 피어에 대해 **requestURL** 및 **eventURL**을 설정해야 합니다. **url** 속성을 연결 프로파일에 있는 **url** 값으로 바꾸십시오. **eventURL** 속성을 연결 프로파일에 있는 **eventUrl**로 바꾸십시오. 변경 후 `connection.json`의 피어 섹션 형식은 다음과 같습니다.

    ```
        "peers": [{
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
    ```
    {:codeblock}

## 5단계: keyValStore 정보 추가

1. 적절한 디렉토리를 가리키도록 **keyValStore** 속성을 설정하십시오. **keyValStore**에 사용할 디렉토리를 작성하십시오. 예를 들어, `.composer-credentials-mychannel`라는 홈 디렉토리 아래의 새 디렉토리입니다. **keyValStore** 속성이 다음 형식으로 새로 작성된 디렉토리를 가리키는지 확인하십시오.

    ```
    "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",
    ```
    {:codeblock}

## 6단계: 채널 정보 추가

1. 비즈니스 네트워크를 작성하고 배치할 채널의 이름과 일치하도록 `connection.json`의 채널 값을 바꾸십시오.

## 7단계: mspID 추가

`connection.json` 파일의 **mspID** 값을 조직의 mspID로 설정해야 합니다. 연결 프로파일은 연관된 mspid 값과 함께 조직 목록을 제공합니다. 조직의 **mspid** 속성 값을 사용해야 합니다.

## 8단계: globalCert 추가
1. {{site.data.keyword.blockchainfull_notm}} Platform은 순서 지정자 및 피어에 공통 TLS 인증서를 사용합니다. 각 순서 지정자 및 피어에 대해 모두 동일한 인증서를 포함하는 **tlsCACerts** 속성이 있습니다. `connection.json` 파일의 더미 값을 **tlsCACerts** 값으로 바꾸십시오. 다음 형식을 사용해야 합니다.

    ```
    "globalCert": "-----BEGIN CERTIFICATE-----\r\.......
    ```
    {:codeblock}

## 9단계: 피어 준비

**주의**: 비즈니스 네트워크를 배치할 채널을 작성하기 전에 이 단계를 수행**해야 합니다**. 채널 작성 후 이 단계를 수행하면 배치된 비즈니스 네트워크가 **시작되지 않습니다**.

**certificateAuthorities** 아래 연결 프로파일 문서에 **enrollId** 및 **enrollSecret**의 속성이 포함된 다음 형식의 **registrar** 속성이 있습니다.

 ```
"registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```
 {:codeblock}

1. 다음 명령을 사용하여 인증서를 요청하십시오.

    ```
    composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12
    ```
    {:codeblock}

    이렇게 하면 홈 디렉토리 아래 `.identityCredentials` 디렉토리에 세 개의 파일이 다운로드됩니다. 원하는 파일은 **enrollId**를 기준으로 합니다. 따라서 위의 예제에 **admin-pub.pem** 및 **admin-priv.pem**이라는 두 개의 파일이 있습니다.

3. 탐색 메뉴에서 **구성원**을 선택한 다음 **인증서** 메뉴 옵션을 선택하고 **인증서 추가** 단추를 클릭하십시오.

4. **이름** 필드에 이 인증서의 고유 이름을 입력하십시오. 이 이름에는 대시 또는 하이픈이 포함되면 안됩니다.

5. 이전에 작성된 `admin-pub.pem` 파일을 열고 이 파일의 컨텐츠를 **키** 필드에 복사한 다음 **제출** 단추를 누르십시오.

6. 사용자 인터페이스를 사용하여 피어를 중지한 다음 시작하십시오.

7. 이제 인증서가 인증서 목록에 표시됩니다.

## 10단계: 채널 작성

1. 왼쪽 패널의 탐색 메뉴에서 **채널**을 선택하고 **새 채널** 단추를 클릭하십시오.

2. 채널 이름 및 선택적 설명을 입력하고 **다음**을 클릭하십시오. 채널 이름이 채널 속성에 대한 연결 프로파일에서 지정한 이름과 일치해야 합니다.

3. 필요에 따라 권한을 제공하고 **다음**을 클릭하십시오.

4. 채널 업데이트를 허용해야 하는 운영자 수에 대한 정책을 선택하고 요청을 제출하십시오.

5. 이제 검토할 새 요청이 있는 **알림** 섹션으로 이동합니다. **요청 검토** 단추를 클릭하십시오.

6. **허용** 단추를 클릭하면 **알림** 섹션으로 이동하며 이 섹션에 이제 요청을 제출할 수 있다는 내용이 표시됩니다. **요청 제출** 단추를 클릭하여 제출 대화 상자를 연 다음 **제출** 단추를 클릭하십시오. 새 채널이 작성됩니다.

7. 탐색 메뉴에서 **채널**을 선택하십시오. 채널 목록의 새 채널에 “아직 추가된 피어가 없어야” 합니다. 채널 옆의 조치 메뉴를 클릭하고 **피어 가입**을 선택하십시오. 추가할 피어 옆에 있는 선택란을 선택하고 **선택사항 추가**를 누르십시오.

## 11단계: 비즈니스 네트워크를 관리할 새 ID 가져오기

이전에 요청된 인증서를 사용하여 작성기에서 ID를 작성하십시오. 이 새 ID는 업로드된 공용 인증서가 있는 피어에 체인코드를 설치하는 권한을 가지며 인증 기관의 발행자가 됩니다.

1. 새 ID를 작성하려면 다음 명령을 실행하십시오.

    ```
    composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem
    ```
    {:codeblock}

    여기서 `bmx-hlfv1`은 이전에 작성한 프로파일의 이름입니다. 이제 `.bna` 파일을 {{site.data.keyword.blockchainfull_notm}} Platform에 배치할 준비가 되었습니다.


## 12단계: 비즈니스 네트워크 배치

이제 `.bna` 파일을 {{site.data.keyword.blockchainfull_notm}} Platform에 배치할 수 있습니다.

1. 이전 단계에서 작성된 ID로 다음 명령을 사용하여 비즈니스 네트워크를 배치하십시오.

   ```
composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString
   ```
   {:codeblock}

---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 스타터 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-networks-on-starter-plan}

{{site.data.keyword.blockchainfull}} Platform: Develop 개발자 환경 및 Hyperledger Composer 개발자 도구 세트를 사용하여 비즈니스 네트워크를 개발하고 스타터 플랜 환경에 배치할 수 있습니다.

개발자 환경을 사용하여 {{site.data.keyword.blockchain}} 비즈니스 네트워크를 신속하게 모델링하고 테스트하며 {{site.data.keyword.blockchainfull_notm}} Platform의 인스턴스에 배치할 수 있습니다.

## 시작하기 전에

[스타터 플랜 정보](./starter_plan.html) 및 [스타터 플랜 시작하기](./get_start_starter_plan.html)를 읽었는지 확인하십시오. 또한 [스타터 플랜 네트워크 통제](./get_start_starter_plan.html)의 지시사항에 따라 [{{site.data.keyword.blockchainfull_notm}} Platform: Develop 개발자 환경](./develop_install.html)을 설치하고 {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜의 인스턴스를 작성했는지 확인하십시오.


## 1단계: 관리자 시크릿 검색

1. 스타터 플랜 개요 화면에서 **연결 프로파일**을 클릭하십시오.

2. 연결 프로파일 내에 **관리자 시크릿** 특성이 있습니다. 시크릿을 검색하고 사본을 저장하십시오.

## 2단계: 인증 기관 카드 작성

이전 단계에서 검색한 시크릿을 사용하여 인증 기관(CA)의 비즈니스 네트워크 카드를 작성합니다. 그런 다음 CA 카드를 가져와 이 카드를 사용하여 **관리자 시크릿**을 스타터 플랜 인증 기관의 올바른 인증서로 교환합니다.

1. 1단계에서 언급한 시크릿으로 다음 명령을 실행하여 CA 비즈니스 네트워크 카드를 작성하십시오.

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. 다음 명령을 사용하여 카드를 가져오십시오.

        composer card import -f ca.card -n ca

3. 카드를 가져왔으므로 이 카드를 사용하여 **관리자 시크릿**을 CA의 올바른 인증서로 교환할 수 있습니다. 다음 명령을 실행하여 인증 기관의 인증서를 요청하십시오.

        composer identity request --card ca --path ./credentials

`composer identity request` 명령은 인증서 `.pem` 파일이 포함된 `credentials` 디렉토리를 작성합니다.

## 3단계: 스타터 플랜 인스턴스에 인증서 추가

인증서를 스타터 플랜 인스턴스에 추가해야 합니다. 편의를 위해 {{site.data.keyword.blockchainfull_notm}} Platform API를 사용하여 인증서를 추가할 수 있습니다. 인증서를 추가한 다음 피어를 중지하고 다시 시작해야 인증서가 동기화됩니다. API 호출의 변수에 필요한 데이터는 스타터 플랜 네트워크 모니터의 **연결 프로파일**에서 검색할 수 있습니다.

1. 다음 API 호출을 사용하여 인증서를 추가하십시오.

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    위의 예제에서 본문은 공백으로 남아 있으며 다음 코드는 API 요청의 본문 예제입니다.

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. 다음 API 호출을 사용하여 피어를 중지하십시오.

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. 다음 API 호출을 사용하여 피어를 다시 시작하십시오.

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. 다음 API 호출을 사용하여 인증서를 동기화하십시오.

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## 4단계: 관리 비즈니스 네트워크 카드 작성

올바른 인증서가 피어와 동기화되었으므로 Hyperledger Composer 런타임을 설치하고 체인코드를 시작하는 권한이 있는 비즈니스 네트워크 카드를 작성할 수 있습니다.

1. 다음 명령을 사용하여 채널 관리자 및 피어 관리자 역할로 관리자 카드를 작성하십시오.

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    이 카드는 스타터 플랜에 비즈니스 네트워크를 배치하는 데 사용됩니다.

2. 다음 명령을 사용하여 이전 단계에서 작성된 카드를 가져오십시오.

        composer card import -f adminCard.card -n adminCard

## 5단계: 비즈니스 네트워크 설치 및 시작

그런 다음 이전 단계에서 작성된 카드를 사용하여 비즈니스 네트워크를 설치하고 시작할 수 있습니다. 이 안내서에서는 차량 제조 네트워크 샘플을 설치합니다. 비즈니스 네트워크를 시작하기 위한 명령은 카드도 작성합니다. 스타터 플랜의 경우 이 카드를 삭제해야 하며 지정된 예제 명령은 이 카드의 이름을 `delete_me.card`로 지정하므로 쉽게 구분할 수 있습니다.

1. 다음 명령을 사용하여 Hyperledger Composer 런타임을 설치하십시오.

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. 다음 명령을 사용하여 비즈니스 네트워크를 시작하십시오.

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. `delete_me.card`라는 비즈니스 네트워크 카드를 삭제하십시오.

4. 다음 명령을 사용하여 새 비즈니스 네트워크 카드를 작성하고 이전에 검색한 인증서를 참조하십시오.

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. 다음 명령을 사용하여 비즈니스 네트워크 카드를 가져오십시오.

        composer card import -f ./admin@vehicle-manufacture-network.card

비즈니스 네트워크가 이제 스타터 플랜 인스턴스에 배치됩니다.

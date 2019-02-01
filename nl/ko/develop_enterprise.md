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
{:tip: .tip}

# 엔터프라이즈 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-network}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop의 개발자 도구를 사용하면 비즈니스 네트워크 아카이브(`.bna`)에 패키지할 수 있는 **비즈니스 네트워크 정의**를 작성할 수 있습니다. 개발자 환경에서는 `.bna` 파일을 개발 및 공유를 위한 로컬 또는 클라우드 {{site.data.keyword.blockchain}}에 배치할 수 있습니다.

이 튜토리얼에서는 비즈니스 네트워크 라이프사이클의 다음 단계, 즉 `.bna`를 {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에 배치하여 비즈니스 네트워크를 활성화하는 단계를 다룹니다.

## 시작하기 전에

{{site.data.keyword.blockchainfull_notm}} 개발자 환경을 설치했고 비즈니스 네트워크 개발 및 배치에 익숙한지 확인하십시오. 비즈니스 네트워크 작성에 대한 안내는 [Hyperledger Composer 문서](https://hyperledger.github.io/composer/latest/business-network/business-network-index)에 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform의 엔터프라이즈 플랜 인스턴스에 대한 액세스 권한이 있어야 하며 사전에 피어를 작성해야 합니다.{{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에 대한 자세한 정보는 [엔터프라이즈 플랜 개요](/docs/services/blockchain/enterprise_plan.html)를 참조하십시오.

## 1단계: {{site.data.keyword.blockchainfull_notm}} Platform에 대한 연결 프로파일 작성

1. 연결 세부사항을 저장할 디렉토리를 작성하십시오. 예를 들어, 다음과 같습니다.

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    각 연결 프로파일에 `connection.json` 파일이 있어야 합니다. 이 인스턴스 `bmx-hlfv11`에서 `.composer-connection-profiles` 아래에 새 디렉토리를 작성하십시오. 이는 Hyperledger Composer 및 {{site.data.keyword.blockchainfull_notm}} Platform에 대한 작업을 수행할 때 사용할 프로파일의 이름이 됩니다.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    이제 다음 디렉토리 구조를 사용합니다.

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

## 2단계: 연결 프로파일 검색

1. {{site.data.keyword.blockchainfull_notm}} Platform 대시보드에서 **개요**, **연결 프로파일** 및 **다운로드** 단추를 선택하여 연결 프로파일을 검색하십시오.

2. 이전 단계에서 작성된 디렉토리 구조에 연결 프로파일을 저장하십시오. 연결 프로파일의 이름을 **connection.json**으로 지정하십시오.

## 3단계: 채널 정보 추가

1. 비즈니스 네트워크를 작성하고 배치할 채널의 이름과 일치하도록 `connection.json`에 채널 값을 추가하십시오. 제공된 연결 프로파일 템플리트 예제에서 채널 요소는 다음과 같습니다. `"channels": {},`.

## 4단계: 피어 준비

**주의**: 비즈니스 네트워크를 배치할 채널을 작성하기 전에 이 단계를 수행**해야 합니다**. 채널 작성 후 이 단계를 수행하면 배치된 비즈니스 네트워크가 **시작되지 않을 수 있습니다**.

**certificateAuthorities** 아래 연결 프로파일 문서에 **enrollId** 및 **enrollSecret**의 속성이 포함된 다음 형식의 **registrar** 속성이 있습니다.

 ```
"registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```

1. 다음 명령을 사용하여 CA의 비즈니스 네트워크 카드를 작성하십시오.

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  연결 프로파일과 같은 디렉토리에서 명령을 실행하고 있는지 확인하십시오.

2. 다음 명령을 사용하여 비즈니스 네트워크 카드를 가져오십시오.

    ```
composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. 카드를 가져온 후 다음 명령을 사용하여 인증 기관에서 인증서를 획득하는 데 사용할 수 있습니다.

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    `composer identity request` 명령은 관련 인증서 파일이 포함된 인증 정보 디렉토리를 작성합니다.

4. 탐색 메뉴에서 **구성원**을 선택한 다음 **인증서** 메뉴 옵션을 선택하고 **인증서 추가** 단추를 클릭하십시오.

5. **이름** 필드에 이 인증서의 고유 이름을 입력하십시오. 이 이름에는 대시 또는 하이픈이 포함되면 안됩니다.

6. 이전에 작성된 `admin-pub.pem` 파일을 열고 이 파일의 컨텐츠를 **키** 필드에 복사한 다음 **제출** 단추를 누르십시오.

7. 사용자 인터페이스를 사용하여 피어를 중지한 다음 시작하십시오.

8. 이제 인증서가 인증서 목록에 표시됩니다.

## 5단계: 채널 작성

1. 왼쪽 패널의 탐색 메뉴에서 **채널**을 선택하고 **새 채널** 단추를 클릭하십시오.

2. 채널 이름 및 선택적 설명을 입력하고 **다음**을 클릭하십시오. 채널 이름이 채널 속성에 대한 연결 프로파일에서 지정한 이름과 일치해야 합니다.

3. 필요에 따라 권한을 제공하고 **다음**을 클릭하십시오.

4. 채널 업데이트를 허용해야 하는 운영자 수에 대한 정책을 선택하고 요청을 제출하십시오.

5. 이제 검토할 새 요청이 있는 **알림** 섹션으로 이동합니다. **요청 검토** 단추를 클릭하십시오.

6. **허용** 단추를 클릭하면 **알림** 섹션으로 이동하며 이 섹션에 이제 요청을 제출할 수 있다는 내용이 표시됩니다. **요청 제출** 단추를 클릭하여 제출 대화 상자를 연 다음 **제출** 단추를 클릭하십시오. 새 채널이 작성됩니다.

7. 탐색 메뉴에서 **채널**을 선택하십시오. 채널 목록의 새 채널에 “아직 추가된 피어가 없어야” 합니다. 채널 옆의 조치 메뉴를 클릭하고 **피어 가입**을 선택하십시오. 추가할 피어 옆에 있는 선택란을 선택하고 **선택사항 추가**를 누르십시오.

## 6단계: 비즈니스 네트워크를 관리할 새 ID 작성

요청된 인증서를 사용하여 비즈니스 네트워크 카드를 작성하십시오. 이 비즈니스 네트워크 카드는 업로드된 공용 인증서가 있는 피어에 체인코드를 설치하는 권한을 가지며 인증 기관의 발행자가 됩니다.

1. 새 카드를 작성하려면 다음 명령을 실행하십시오.

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    여기서 `bmx-hlfv11`은 이전에 작성한 프로파일의 이름입니다.

2. 카드를 작성한 후 다음 명령을 사용하여 가져오십시오.

    ```
composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    이제 `.bna` 파일을 {{site.data.keyword.blockchainfull_notm}} Platform에 배치할 준비가 되었습니다.


## 7단계: 비즈니스 네트워크 배치

이제 `.bna` 파일을 {{site.data.keyword.blockchainfull_notm}} Platform에 배치할 수 있습니다.

1. 이전 단계에서 작성된 카드를 사용하려면 먼저 비즈니스 네트워크를 설치한 다음 시작해야 합니다. 다음 명령을 사용하여 비즈니스 네트워크를 설치하십시오.

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. 비즈니스 네트워크를 설치한 후 다음 명령을 사용하여 비즈니스 네트워크를 시작하십시오.

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. 비즈니스 네트워크가 올바르게 배치되었는지 확인하려면 네트워크에 Ping을 실행할 때 사용할 수 있는 ID와 연관된 비즈니스 네트워크 카드를 작성하십시오.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. 다음 명령을 사용하여 비즈니스 네트워크 카드를 가져오십시오.

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. 네트워크에 ping을 실행하여 네트워크가 실행 중인지 확인하십시오.

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

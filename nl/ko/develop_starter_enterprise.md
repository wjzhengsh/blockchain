---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: business network, Starter Plan, Enterprise Plan, developer environment, certificate authority card, admin business network card, BNA, business network archive

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# 스타터 및 엔터프라이즈 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-network}

{{site.data.keyword.IBM}}은 Composer CLI, JavaScript API, REST 서버 및 웹 플레이그라운드를 포함하여 프로덕션에서 Hyperledger Composer를 사용하는 네트워크를 지원하지 않습니다.
{:note}

{{site.data.keyword.blockchainfull_notm}} Platform 개발자 환경 및 Hyperledger Composer 개발자 도구 세트를 사용하여 [비즈니스 네트워크](/docs/services/blockchain/glossary.html#glossary-business-network)를 개발하고 스타터 및 엔터프라이즈 플랜 네트워크에 배치할 수 있습니다.
{:shortdesc}

개발자 환경을 사용하여 블록체인 비즈니스 네트워크를 신속하게 모델링하고 테스트하며 {{site.data.keyword.blockchainfull_notm}} Platform의 스타터 또는 엔터프라이즈 플랜 네트워크에 배치할 수 있습니다.

## 스타터 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-network-starter-plan}

### 시작하기 전에
{: #deploying-a-business-network-before-begin}

[스타터 플랜 정보](/docs/services/blockchain/starter_plan.html#starter-plan-about)를 읽고 [스타터 플랜 시작하기](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan)의 지시사항에 따라 스타터 플랜 네트워크를 작성했는지 확인하십시오.

Node v8.9 이상, npm v5.x 및 Hyperledger Composer를 설치했는지 확인하십시오.

- 네트워크에 Fabric 버전 1.2가 있는 경우 Hyperledger Composer v0.20.x를 사용하십시오.
- 네트워크에 Fabric 버전 1.1이 있는 경우 Hyperledger Composer v0.19.x를 사용하십시오.

네트워크 모니터에서 [네트워크 환경 설정 창](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences)을 열어서 Fabric 버전을 찾을 수 있습니다.

### 1단계: 관리자 시크릿 검색
{: #deploying-a-business-network-retrieve-admin-secret}

1. 스타터 플랜 개요 화면에서 **연결 프로파일**을 클릭한 후 다운로드하십시오. 이 파일의 이름을 'connection-profile.json'으로 바꾸십시오.

2. `.bna` 파일과 동일한 디렉토리에 위치하도록 이 파일을 이동하십시오.

3. 연결 프로파일 내부에서 'registrar'가 표시될 때까지 아래로 이동하십시오. 'registrar' 내부의 'enrollId' 아래에 **enrollSecret** 특성이 있습니다. 시크릿을 검색하고 사본을 저장하십시오.

    ![관리 시크릿 검색](images/get_enroll_secret.gif "관리 시크릿 검색")

### 2단계: 인증 기관 카드 작성
{: #deploying-a-business-network-CA-card}

이전 단계에서 검색한 시크릿을 사용하여 인증 기관(CA)의 비즈니스 네트워크 카드를 작성합니다. 그런 다음 CA 카드를 가져와 이 카드를 사용하여 **enrollSecret**을 스타터 플랜 인증 기관의 올바른 인증서로 교환합니다.

1. 1단계에서 언급한 **enrollSecret**으로 다음 명령을 실행하여 CA 비즈니스 네트워크 카드를 작성하십시오.

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  이전 명령의 `enrollSecret`을 연결 프로파일에서 검색된 관리 시크릿으로 대체하십시오.

2. 다음 명령을 사용하여 카드를 가져오십시오.

   ```
composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. 카드를 가져왔으므로 이 카드를 사용하여 **enrollSecret**을 CA의 올바른 인증서로 교환할 수 있습니다. 다음 명령을 실행하여 인증 기관의 인증서를 요청하십시오.

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  이전 명령의 `enrollSecret`을 연결 프로파일에서 검색된 관리 시크릿으로 대체하십시오. `composer identity request` 명령은 인증서 `.pem` 파일이 포함된 `credentials` 디렉토리를 작성합니다.

### 3단계: 스타터 플랜 인스턴스에 인증서 추가
{: #deploying-a-business-network-add-certs-to-starter-plan}

인증서를 스타터 플랜 네트워크에 추가해야 합니다. 편의상, {{site.data.keyword.blockchainfull_notm}} Platform 네트워크 모니터를 사용하여 인증서를 추가할 수 있습니다. 인증서를 추가하고 피어를 다시 시작한 다음 채널에서 인증서를 동기화해야 합니다. 필요한 인증서는 이전 명령에서 생성된 `credentials` 디렉토리의 `admin-pub.pem` 파일입니다.

1. 스타터 플랜 네트워크 모니터에서 **구성원** 탭을 클릭한 후 **인증서** 및 **인증서 추가**를 차례로 클릭하십시오. `credentials` 디렉토리로 이동한 후 `admin-pub.pem` 파일의 컨텐츠를 복사하여 인증서 상자에 붙여넣으십시오. 인증서를 제출하고 피어를 다시 시작하십시오. 참고: 피어를 다시 시작하는 데 몇 분이 걸립니다.

    ![인증서 추가](images/add_cert.gif "인증서 추가")

2. 그런 다음 인증서가 채널에서 동기화되어야 합니다. **채널** 탭을 클릭한 후 **조치** 단추, **인증서 동기화** 및 **제출**을 차례로 클릭하십시오.

    ![인증서 동기화](images/sync_cert.gif "인증서 동기화")

### 4단계: 관리 비즈니스 네트워크 카드 작성
{: #deploying-a-business-network-create-admin-card}

올바른 인증서가 피어와 동기화되었으므로 Hyperledger Composer 런타임을 설치하고 체인코드를 시작하는 권한이 있는 비즈니스 네트워크 카드를 작성할 수 있습니다.

1. 다음 명령을 사용하여 채널 관리자 및 피어 관리자 역할로 관리자 카드를 작성하십시오.

   ```
composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   이 카드는 스타터 플랜에 비즈니스 네트워크를 배치하는 데 사용됩니다.

2. 다음 명령을 사용하여 이전 단계에서 작성된 카드를 가져오십시오.

   ```
composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### 5단계: 비즈니스 네트워크 설치 및 시작
{: #deploying-a-business-network-install-start-network}

그런 다음 이전 단계에서 작성된 카드를 사용하여 비즈니스 네트워크를 설치하고 시작할 수 있습니다. 이 안내서에는 차량 제조 네트워크 샘플을 설치하고 차량 제조 샘플을 사용하거나 고유한 비즈니스 네트워크를 설치하지만 명령에 지정된 비즈니스 네트워크 이름을 변경해야 합니다. 비즈니스 네트워크를 시작하기 위한 명령은 카드도 작성합니다. 스타터 플랜의 경우 이 카드를 삭제해야 하며 지정된 예제 명령은 이 카드의 이름을 `delete_me.card`로 지정하므로 쉽게 구분할 수 있습니다.

1. 다음 명령을 사용하여 Hyperledger Composer 런타임을 설치하십시오.

   ```
composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   이 명령을 실행할 때 리턴되는 비즈니스 네트워크 버전 번호를 기록하십시오. 다음 단계에서 필요합니다.

2. 아래 명령을 사용하여 비즈니스 네트워크를 시작하십시오. 오류가 발생하는 경우 몇 분 기다린 후 다시 시도하십시오. `-V` 옵션 다음에 지난 단계에서 얻은 버전 번호를 사용하십시오.

    ```
composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. `delete_me.card`라는 비즈니스 네트워크 카드를 삭제하십시오.

4. 다음 명령을 사용하여 새 비즈니스 네트워크 카드를 작성하고 이전에 검색한 인증서를 참조하십시오.

   ```
composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. 다음 명령을 사용하여 비즈니스 네트워크 카드를 가져오십시오.

    ```
composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

비즈니스 네트워크가 이제 스타터 플랜 인스턴스에 배치됩니다.

### 6단계: 비즈니스 네트워크에 ping을 실행하여 올바르게 실행 중인지 확인
{: #deploying-a-business-network-ping-business-network}

다음 명령을 실행하여 비즈니스 네트워크에 ping을 실행하십시오.

   ```
composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

체인코드 로그를 보려면 **채널**을 클릭한 다음 채널을 선택하십시오. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> **체인코드** 탭을 클릭하십시오. 체인코드 행을 펼친 다음 **JSON** 또는 **로그** 단추를 클릭하십시오.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

비즈니스 네트워크가 스타터 플랜 인스턴스에 배치되었습니다.

## 엔터프라이즈 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-network-to-enterprise-plan}

{{site.data.keyword.blockchainfull_notm}} Platform의 개발자 도구를 사용하면 비즈니스 네트워크 아카이브(`.bna`)에 패키지할 수 있는 **비즈니스 네트워크 정의**를 작성할 수 있습니다. 개발자 환경에서는 `.bna` 파일을 개발 및 공유를 위한 로컬 또는 클라우드 {{site.data.keyword.blockchain}}에 배치할 수 있습니다.

이 튜토리얼에서는 비즈니스 네트워크 라이프사이클의 다음 단계, 즉 `.bna`를 {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에 배치하여 비즈니스 네트워크를 활성화하는 단계를 다룹니다.

### 시작하기 전에
{: #deploying-a-business-network-enterprise-plan-before-begin}

{{site.data.keyword.blockchainfull_notm}} 개발자 환경을 설치했고 비즈니스 네트워크 개발 및 배치에 익숙한지 확인하십시오. 비즈니스 네트워크 작성에 대한 안내는 [Hyperledger Composer 문서](https://hyperledger.github.io/composer/latest/business-network/business-network-index)에 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform의 엔터프라이즈 플랜 인스턴스에 대한 액세스 권한이 있어야 하며 사전에 피어를 작성해야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜에 대한 자세한 정보는 [엔터프라이즈 플랜 개요](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)를 참조하십시오.

### 1단계: {{site.data.keyword.blockchainfull_notm}} Platform에 대한 연결 프로파일 작성
{: #deploying-a-business-network-create-connection-profile}

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

### 2단계: 연결 프로파일 검색
{: #deploying-a-business-network-retrieve-connection-profile}

1. {{site.data.keyword.blockchainfull_notm}} Platform 대시보드에서 **개요**, **연결 프로파일** 및 **다운로드** 단추를 선택하여 연결 프로파일을 검색하십시오.

2. 이전 단계에서 작성된 디렉토리 구조에 연결 프로파일을 저장하십시오. 연결 프로파일의 이름을 **connection.json**으로 지정하십시오.

### 3단계: 채널 정보 추가
{: #deploying-a-business-network-add-channel-information}

1. 비즈니스 네트워크를 작성하고 배치할 채널의 이름과 일치하도록 `connection.json`에 채널 값을 추가하십시오. 제공된 연결 프로파일 템플리트 예제에서 채널 요소는 다음과 같습니다. `"channels": {},`.

### 4단계: 피어 준비
{: #deploying-a-business-network-prepare-peers}

비즈니스 네트워크를 배치할 채널을 작성하기 전에 이 단계를 수행**해야 합니다**. 채널 작성 후에 이 단계를 수행하면 배치된 비즈니스 네트워크가 **시작되지 않을 수 있습니다**.
{:note}

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

### 5단계: 채널 작성
{: #deploying-a-business-network-create-your-channel}

1. 왼쪽 패널의 탐색 메뉴에서 **채널**을 선택하고 **새 채널** 단추를 클릭하십시오.

2. 채널 이름 및 선택적 설명을 입력하고 **다음**을 클릭하십시오. 채널 이름이 채널 속성에 대한 연결 프로파일에서 지정한 이름과 일치해야 합니다.

3. 필요에 따라 권한을 제공하고 **다음**을 클릭하십시오.

4. 채널 업데이트를 허용해야 하는 운영자 수에 대한 정책을 선택하고 요청을 제출하십시오.

5. 이제 검토할 새 요청이 있는 **알림** 섹션으로 이동합니다. **요청 검토** 단추를 클릭하십시오.

6. **허용** 단추를 클릭하면 **알림** 섹션으로 이동하며 이 섹션에 이제 요청을 제출할 수 있다는 내용이 표시됩니다. **요청 제출** 단추를 클릭하여 제출 대화 상자를 연 다음 **제출** 단추를 클릭하십시오. 새 채널이 작성됩니다.

7. 탐색 메뉴에서 **채널**을 선택하십시오. 채널 목록의 새 채널에 “아직 추가된 피어가 없어야” 합니다. 채널 옆의 조치 메뉴를 클릭하고 **피어 가입**을 선택하십시오. 추가할 피어 옆에 있는 선택란을 선택하고 **선택사항 추가**를 누르십시오.

### 6단계: 비즈니스 네트워크를 관리할 새 ID 작성
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

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


### 7단계: 비즈니스 네트워크 배치
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

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

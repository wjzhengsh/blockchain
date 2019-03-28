---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 스타터 플랜에 비즈니스 네트워크 배치
{: #deploying-a-business-network}

**IBM은 Hyperledger Composer를 데모 및 개념 증명(POC)용으로만 사용하도록 권장합니다. IBM은 Composer CLI, JavaScript API, REST 서버 및 웹 플레이그라운드를 포함하여 프로덕션에서 Hyperledger Composer를 사용하는 네트워크를 지원하지 않습니다.**

*[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)*


{{site.data.keyword.blockchainfull}} Platform 개발자 환경 및 Hyperledger Composer 개발자 도구 세트를 사용하여 비즈니스 네트워크를 개발하고 스타터 플랜 환경에 배치할 수 있습니다.
{:shortdesc}

개발자 환경을 사용하여 {{site.data.keyword.blockchain}} 비즈니스 네트워크를 신속하게 모델링하고 테스트하며 {{site.data.keyword.blockchainfull_notm}} Platform의 인스턴스에 배치할 수 있습니다.

## 시작하기 전에

[스타터 플랜 정보](./starter_plan.html) 및 [스타터 플랜 시작하기](./get_start_starter_plan.html)를 읽었는지 확인하십시오. 또한 [스타터 플랜 네트워크 통제](./get_start_starter_plan.html)의 지시사항에 따라 [{{site.data.keyword.blockchainfull_notm}} Platform 개발자 환경](./develop_install.html)을 설치하고 {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜의 인스턴스를 작성했는지 확인하십시오.

Node v8.9 이상, npm v5.x 및 Hyperledger Composer가 있는지 확인하십시오.

- 네트워크에 Fabric 버전 1.2가 있는 경우 Hyperledger Composer v0.20.x를 사용하십시오.
- 네트워크에 Fabric 버전 1.1이 있는 경우 Hyperledger Composer v0.19.x를 사용하십시오.  

네트워크 모니터에서 [네트워크 환경 설정 창](../v10_dashboard.html#network-preferences)을 열어서 Fabric 버전을 찾을 수 있습니다.


## 1단계: 관리자 시크릿 검색

1. 스타터 플랜 개요 화면에서 **연결 프로파일**을 클릭한 후 다운로드하십시오. 이 파일의 이름을 'connection-profile.json'으로 바꾸십시오.

2. `.bna` 파일과 동일한 디렉토리에 위치하도록 이 파일을 이동하십시오.

3. 연결 프로파일 내부에서 'registrar'가 표시될 때까지 아래로 이동하십시오. 'registrar' 내부의 'enrollId' 아래에 **enrollSecret** 특성이 있습니다. 시크릿을 검색하고 사본을 저장하십시오.

    ![관리 시크릿 검색](images/get_enroll_secret.gif "관리 시크릿 검색")


## 2단계: 인증 기관 카드 작성

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

## 3단계: 스타터 플랜 인스턴스에 인증서 추가

인증서를 스타터 플랜 인스턴스에 추가해야 합니다. 편의를 위해 {{site.data.keyword.blockchainfull_notm}} Platform UI를 사용하여 인증서를 추가할 수 있습니다. 인증서를 추가하고 피어를 다시 시작한 다음 채널에서 인증서를 동기화해야 합니다. 필요한 인증서는 이전 명령에서 생성된 `credentials` 디렉토리의 `admin-pub.pem` 파일입니다.

1. 스타터 플랜 UI에서 **구성원** 탭을 클릭한 후 **인증서** 및 **인증서 추가**를 차례로 클릭하십시오. `credentials` 디렉토리로 이동한 후 `admin-pub.pem` 파일의 컨텐츠를 복사하여 인증서 상자에 붙여넣으십시오. 인증서를 제출하고 피어를 다시 시작하십시오. 참고: 피어를 다시 시작하는 데 몇 분이 걸립니다.

    ![인증서 추가](images/add_cert.gif "인증서 추가")

2. 그런 다음 인증서가 채널에서 동기화되어야 합니다. **채널** 탭을 클릭한 후 **조치** 단추, **인증서 동기화** 및 **제출**을 차례로 클릭하십시오.

    ![인증서 동기화](images/sync_cert.gif "인증서 동기화")

## 4단계: 관리 비즈니스 네트워크 카드 작성

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

## 5단계: 비즈니스 네트워크 설치 및 시작

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

## 6단계: 비즈니스 네트워크에 ping을 실행하여 올바르게 실행 중인지 확인

다음 명령을 실행하여 비즈니스 네트워크에 ping을 실행하십시오.

   ```
composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

체인코드 로그를 보려면 **채널**을 클릭한 다음 채널을 선택하십시오. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> **체인코드** 탭을 클릭하십시오. 체인코드 행을 펼친 다음 **JSON** 또는 **로그** 단추를 클릭하십시오.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

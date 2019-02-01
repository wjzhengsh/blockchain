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

# 비즈니스 네트워크 개발 환경 설치
{: #installing-a-development-environment}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


다음 지시사항에 따라 비즈니스 네트워크 작성 및 테스트를 위한 {{site.data.keyword.blockchainfull}} Platform: Develop 개발 도구를 확보하십시오. 유연성을 제공하고 최대 수의 개발, 테스트 및 배치 시나리오를 사용할 수 있도록 개발 도구가 명령행에서 제어되는 npm 모듈로서 제공됩니다.

## 시작하기 전에

다음 필수 소프트웨어를 설치했는지 확인하십시오.

- Ubuntu Linux 14.04/16.04 LTS(둘 다 64비트) 또는 Mac OS 10.12
- Node v8.9 이상(v9는 지원되지 않음)
- npm v5.x
- git v2.9.x 이상
- Python v2.7.x
- VSCode와 같은 원하는 코드 편집기

엔터프라이즈 플랜을 사용하는 경우 다음과 같은 추가 필수 소프트웨어를 설치하십시오.

- Docker Engine v17.03 이상
- Docker-Compose v1.8 이상

VSCode를 사용하는 경우 [여기](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client)에 사용 가능한 Hyperledger Composer VSCode 확장이 있습니다.

## 1단계: 명령행 도구 설치

명령행 도구에는 **composer-cli**라는 기본 {{site.data.keyword.blockchainfull_notm}} Platform: Develop 명령행 모듈과 **generator-hyperledger-composer**, **composer-rest-server** 및 **Yeoman**이 포함되며, 이러한 모듈은 비즈니스 네트워크, 맞춤형 REST 서버 및 Angular 애플리케이션을 생성하는 데 사용됩니다. 설치 버전은 스타터 또는 엔터프라이즈 플랜이 실행 중인 Hyperledger Fabric의 버전에 따라 달라집니다. 네트워크 모니터에서 [네트워크 환경 설정 창](/docs/services/blockchain/v10_dashboard.html#network-preferences)을 열어 Fabric 버전을 찾을 수 있습니다. 

1. Hyperledger Fabric v1.2.1을 실행 중인 스타터 플랜의 새 인스턴스에 사용하려면 다음 명령을 사용하여 **composer-cli**를 설치하십시오. 

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Hyperledger Fabric v1.1을 실행 중인 엔터프라이즈 플랜 및 스타터 플랜 인스턴스에 사용하려면 다음 명령을 사용하여 **composer-cli**를 설치하십시오. 

    ```
npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Hyperledger Fabric v1.2.1을 실행 중인 스타터 플랜의 새 인스턴스에 사용하려면 다음 명령을 사용하여 **composer-rest-server**를 설치하십시오. 

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Hyperledger Fabric v1.1을 실행 중인 엔터프라이즈 플랜 및 스타터 플랜 인스턴스에 사용하려면 다음 명령을 사용하여 **composer-rest-server**를 설치하십시오. 

    ```
npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 RESTful API로 비즈니스 네트워크를 노출시키려면 **composer-rest-server** 모듈을 사용하여 시스템에서 REST 서버를 작성하십시오.

3. Hyperledger Fabric v1.2.1을 실행 중인 스타터 플랜의 새 인스턴스에 사용하려면 다음 명령을 사용하여 **generator-hyperledger-composer**를 설치하십시오. 

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Hyperledger Fabric v1.1을 실행 중인 엔터프라이즈 플랜 및 스타터 플랜 인스턴스에 사용하려면 다음 명령을 사용하여 **generator-hyperledger-composer**를 설치하십시오. 

    ```
npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    스켈레톤 비즈니스 네트워크 구조, 모델 및 Angular 애플리케이션을 생성하려면 **generator-hyperledger-composer**를 사용하십시오.

4. Yeoman은 애플리케이션 생성을 위한 도구이며 `generator-hyperledger-composer`를 이용합니다.

    ```
npm install -g yo
    ```
    {:codeblock}

## 2단계: 로컬 Playground 설치

Hyperledger Composer Playground는 비즈니스 네트워크 테스트를 위해 시뮬레이션된 환경으로 사용할 수 있는 사용자 인터페이스입니다. 다음 명령을 사용하여 플레이그라운드를 설치하십시오.

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## 선택사항: IDE 설정

Playground를 사용하여 비즈니스 네트워크를 개발하고 편집하고 테스트할 수 있습니다. 하지만 IDE에서 비즈니스 네트워크를 개발하려는 경우 VSCode 확장을 사용할 수 있으며, 이 확장은 Hyperledger Composer 모델링 언어에 대한 구문 강조표시를 추가합니다.

1. URL [https://code.visualstudio.com/download](https://code.visualstudio.com/download)에서 VSCode를 설치하십시오.

2. VSCode를 열고 Extensions로 이동한 다음 마켓플레이스에서 Hyperledger Composer 확장을 검색하고 설치하십시오.


### Hyperledger Fabric 시작 및 중지

`fabric-tools` 디렉토리에 Hyperledger Fabric 인스턴스를 제어하는 스크립트 세트가 있습니다. `~/fabric-tools/stopFabric.sh` 및 `~/fabric-tools/startFabric.sh`를 사용하여 런타임을 중지하고 시작할 수 있습니다.

`~/fabric-tools/teardownFabric.sh`에서는 환경이 다음에 시작될 때 새 PeerAdmin 카드를 작성해야 합니다.

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

# 비즈니스 네트워크 개발
{: #develop-the-network}

**IBM은 Hyperledger Composer를 데모 및 개념 증명(POC)용으로만 사용하도록 권장합니다. IBM은 Composer CLI, JavaScript API, REST 서버 및 웹 플레이그라운드를 포함하여 프로덕션에서 Hyperledger Composer를 사용하는 네트워크를 지원하지 않습니다.**

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform을 사용하여 블록체인 솔루션을 개발할 때 Hyperledger Composer 오픈 소스 개발 도구 세트를 활용합니다. Hyperledger Composer는 맞춤형 모델링 언어를 사용하며, 이 언어는 사용자가 실제 블록체인 네트워크에 무언가를 배치하기 _전에_ JavaScript 트랜잭션 및 액세스 제어 규칙에 결합되어 블록체인 비즈니스 네트워크 전체를 모델링합니다.
{:shortdesc}

**참고:** 스타터 플랜은 사용자가 학습하고 시험 사용해 볼 수 있는 샘플 애플리케이션을 제공합니다. 사용자 정의 비즈니스 네트워크를 개발하기 전에 스타터 플랜에서 샘플 애플리케이션을 실행할 수 있습니다. 자세한 정보는 [스타터 플랜 정보](starter_plan.html)를 참조하십시오.

이 튜토리얼에서는 비즈니스 네트워크 작성 및 모델링으로 시작하는 {{site.data.keyword.blockchain}} 솔루션의 개발을 거친 다음 실행 중인 블록체인 네트워크에 배치합니다.

다음 단계에서는 {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 {{site.data.keyword.blockchain}} 솔루션의 기본적인 개발에 대해 요약합니다.

1. 비즈니스 네트워크 정의를 포함하도록 데이터 구조를 작성하십시오. 비즈니스 네트워크는 [모델링 언어](https://hyperledger.github.io/composer/latest/reference/cto_language)를 사용하여 자산, 참가자 및 트랜잭션의 측면에서 정의됩니다.

2. 배치 후에 사용될 자산 클래스, 참가자 클래스, 트랜잭션 클래스, 이벤트 클래스 및 액세스 제어 규칙을 정의하여 비즈니스 네트워크를 모델링하십시오.

3. 정의된 비즈니스 네트워크를 단일 배치 가능 파일로 패키지하십시오.

4. 패키지된 비즈니스 네트워크를 {{site.data.keyword.blockchainfull_notm}} Platform에 배치하십시오. 비즈니스 네트워크를 배치한 후 외부 애플리케이션과의 통합이 가능하도록 REST 서버를 생성할 수 있습니다.

## 시작하기 전에

[{{site.data.keyword.blockchainfull_notm}}: Develop 개발 도구](./develop_install.html)를 설치했는지 확인하십시오.

## 1단계: 비즈니스 네트워크 구조 작성

{{site.data.keyword.blockchain}} 솔루션 개발의 핵심 개념은 **비즈니스 네트워크 정의(BND)**입니다. BND는 블록체인 솔루션을 위한 데이터 모델, 트랜잭션 로직 및 액세스 제어 규칙을 정의합니다. BND를 작성하려면 디스크에서 적절한 프로젝트 구조를 작성해야 합니다.

시작하는 가장 쉬운 방법은 Yeoman 생성기를 사용하여 스켈레톤 비즈니스 네트워크를 작성하는 것입니다. Yeoman 생성기는 비즈니스 네트워크의 모든 컴포넌트가 포함된 디렉토리를 작성하며, 이 디렉토리는 특정 유스 케이스에 맞게 수정할 수 있습니다.

1. 프로젝트에 대한 새 디렉토리를 작성하고 명령행에서 해당 디렉토리로 이동하십시오.

2. 새 디렉토리에서 Yeoman을 사용하여 스켈레톤 비즈니스 네트워크를 작성하십시오. 다음 명령에는 비즈니스 네트워크 이름, 설명, 작성자 이름, 작성자 이메일 주소, 라이센스 선택사항 및 네임스페이스가 필요합니다.
    ```
yo hyperledger-composer:businessnetwork
    ```
    {:codeblock}

3. 네트워크 이름에 대해 `tutorial-network`를 입력하고 설명, 작성자 이름 및 작성자 이메일에 대해 원하는 정보를 입력하십시오.

4. `Apache-2.0`을 라이센스로 선택하십시오.

5. `org.acme.biznet`을 네임스페이스로 선택하십시오.

## 2단계: 비즈니스 네트워크 정의

비즈니스 네트워크는 자산, 참가자, 트랜잭션, 액세스 제어 규칙, 선택적으로 이벤트와 조회로 구성됩니다. 1단계에서 작성된 스켈레톤 비즈니스 네트워크에는 비즈니스 네트워크의 모든 자산, 참가자 및 트랜잭션에 대한 클래스 정의가 포함된 모델 파일(`.cto`)이 있습니다. 스켈레톤 비즈니스 네트워크에는 기본 액세스 제어 규칙이 포함된 액세스 제어 문서(`permissions.acl`), 트랜잭션 프로세서 기능이 포함된 스크립트 파일(`logic.js`) 및 비즈니스 네트워크 메타데이터가 포함된 패키지 파일(`package.json`)도 있습니다.

### 자산, 참가자 및 트랜잭션 모델링

업데이트할 첫 번째 문서는 모델 파일(`.cto`)입니다. 모델 파일은 [Hyperledger Composer 모델링 언어](https://hyperledger.github.io/composer/latest/reference/cto_language)를 사용하여 작성됩니다. 모델 파일에는 자산, 트랜잭션, 참가자 및 이벤트의 각 클래스에 대한 정의가 포함됩니다. 이 파일은 모델링 언어 문서에 설명된 시스템 모델을 내재적으로 확장합니다.

1. 선택한 편집기에서 `org.acme.biznet.cto` 모델 파일을 여십시오. 이 모델 파일은 이전 단계에서 작성한 비즈니스 네트워크의 `models` 폴더에 있습니다.

2. 컨텐츠를 다음 정보로 바꾸십시오.

    ```
        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }
    ```
    {:codeblock}

3. `org.acme.biznet.cto` 파일에 변경사항을 저장하십시오.


### JavaScript 트랜잭션 로직 추가

모델 파일에서 `Trade` 트랜잭션이 정의되었으며, 이 트랜잭션은 자산 및 참가자에 대한 관계를 지정합니다. 트랜잭션 프로세서 기능 파일에는 모델 파일에 정의된 트랜잭션을 실행하기 위한 Javascript 로직이 포함됩니다.

`Trade` 트랜잭션은 거래되는 `Commodity` 자산의 ID 및 새 소유자로 설정할 `Trader` 참가자의 ID를 허용하는 데 사용됩니다.

1. `lib` 디렉토리에서 `logic.js` 스크립트 파일을 여십시오.

2. 컨텐츠를 다음 정보로 바꾸십시오.

    ```
        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }
    ```
    {:codeblock}

3. `logic.js`에 변경사항을 저장하십시오.

### 액세스 제어 추가

액세스 제어는 비즈니스 네트워크의 중요한 부분입니다. 모든 비즈니스 네트워크에는 `permissions.acl`이라는 액세스 제어 파일이 있어야 합니다. 배치된 비즈니스 네트워크에서 프로세스가 호출될 때마다 액세스 제어 목록을 검사하여 호출 ID 또는 참가자가 오퍼레이션을 호출할 수 있는지 확인합니다.

이 튜토리얼을 위해 단순 ACL을 설정하십시오. 이는 프로덕션 환경에 배치되면 안됩니다.

1. `tutorial-network` 디렉토리에서 `permissions.acl` 파일을 작성하십시오.

2. 다음 액세스 제어 규칙을 `permissions.acl`에 추가하십시오.

    ```
        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }
    ```
    {:codeblock}

3. `permissions.acl`에 변경사항을 저장하십시오.

## 3단계: 비즈니스 네트워크 패키징

비즈니스 네트워크를 정의한 후 배치 가능한 비즈니스 네트워크 아카이브 파일(`.bna`)로 패키지해야 합니다.

1. 명령행을 사용하여 `tutorial-network` 디렉토리로 이동하십시오.

2. `tutorial-network` 디렉토리에서 다음 명령을 실행하십시오.

    ```
composer archive create -t dir -n .
    ```
    {:codeblock}

명령을 실행한 후 비즈니스 네트워크 아카이브 파일(`tutorial-network@0.0.1.bna`)이 `tutorial-network` 디렉토리에 작성됩니다.

## 4단계: 비즈니스 네트워크 배치 및 REST 서버 생성

[스타터 플랜](./develop_starter.html) 또는 [엔터프라이즈 플랜](./develop_enterprise.html)을 사용하여 비즈니스 네트워크를 {{site.data.keyword.blockchainfull_notm}} Platform에 배치하십시오.

비즈니스 네트워크를 {{site.data.keyword.blockchainfull_notm}} Platform에 배치한 후 맞춤형 REST 서버를 생성할 수 있습니다. REST 서버는 비즈니스 네트워크의 컨텐츠를 조사하고 네트워크의 자산, 트랜잭션 및 참가자에 대한 RESTful API 호출을 빌드합니다. REST 서버 생성을 위한 지시사항은 [Hyperledger Composer 문서](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api)에 포함되어 있습니다.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}

{{site.data.keyword.blockchainfull}} Platform: Develop은 Linux Foundation의 Hyperledger 프로젝트 내 프로젝트 중 하나인 오픈 소스 Hyperledger Composer 도구 세트에 빌드됩니다. Hyperledger Composer를 사용하면 설계자 및 개발자가 레코드의 기존 엔터프라이즈 시스템과 블록체인을 통합할 뿐만 아니라 웹 또는 모바일 애플리케이션에 비즈니스 로직을 노출하는 REST API와 함께 {{site.data.keyword.blockchain}} 솔루션을 빠르게 작성할 수 있습니다.
{:shortdesc}

Hyperledger Composer는 런타임, 명령 인터페이스, REST 서버, 루프백 커넥터, Playground 사용자 인터페이스, Yeoman 코드 생성기 및 VSCode와 Atom 편집기 플러그인으로 구성됩니다. Hyperledger Composer에 대한 자세한 정보는 [Hyperledger Composer 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)를 참조하십시오.


## 비즈니스 네트워크

Hyperledger Composer를 사용하면 현재 비즈니스 네트워크를 빠르게 모델링할 수 있으며 여기에는 기존 자산 및 관련된 트랜잭션이 포함됩니다. 자산은 유무형의 상품, 서비스 또는 부동산입니다. 비즈니스 네트워크 모델의 일부로 자산과 상호작용할 수 있는 트랜잭션을 정의합니다. 또한 비즈니스 네트워크에는 이와 상호작용하는 참가자가 포함되며 각 참가자는 다중 비즈니스 네트워크에서 고유 ID와 연관될 수 있습니다.

비즈니스 네트워크 빌드에 대한 자세한 정보는 [비즈니스 네트워크 개발](./develop.html) 또는 [Hyperledger Composer 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)를 참조하십시오.

## 조회

조회는 블록체인 세계 상태에 대한 데이터를 리턴하는 데 사용됩니다. 조회는 비즈니스 네트워크 내에 정의되며 단순 사용자 정의를 위한 변수 매개변수를 포함할 수 있습니다. 조회를 사용하여 {{site.data.keyword.blockchain}} 네트워크에서 데이터를 쉽게 추출할 수 있습니다. [Hyperledger Composer API ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/latest/api/api-doc-index)를 사용하여 조회를 전송합니다.

비즈니스 네트워크에서 조회 사용에 대한 자세한 정보는 [비즈니스 네트워크 데이터 조회 및 필터링 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/business-network/query)을 참조하십시오.

## ID 및 비즈니스 네트워크 카드

비즈니스 네트워크 내에서 참가자는 ID와 연관됩니다. 비즈니스 네트워크 카드는 ID, 연결 프로파일 및 메타데이터의 조합이며 비즈니스 네트워크에 연결하고 트랜잭션을 위한 ID를 보여주는 데 사용됩니다. 비즈니스 네트워크 카드는 비즈니스 네트워크에 연결하는 프로세스를 단순화하고 비즈니스 네트워크 외부의 ID 개념을 ID의 '지갑'으로 확장하며, 각각 특정 비즈니스 네트워크 및 연결 프로파일과 연관되어 있습니다.

ID 및 비즈니스 네트워크 카드에 대한 자세한 정보는 [참가자 및 ID ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/managing/participantsandidentities)를 참조하십시오.

## Hyperledger Composer REST 서버

Hyperledger Composer REST 서버는 비즈니스 네트워크의 Swagger REST API를 자동으로 생성합니다. REST 서버는 루프백을 기반으로 하며 비즈니스 네트워크 모델을 Open API 정의로 변환합니다. 런타임 시 REST 서버는 자산 및 참가자를 위한 작성, 읽기, 업데이트 및 삭제 지원을 구현하며 트랜잭션이 처리를 위해 제출되거나 조회를 통해 검색되도록 합니다.

Hyperledger Composer REST 서버에 대한 자세한 정보는 [REST API 생성 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger.github.io/composer/integrating/getting-started-rest-api)을 참조하십시오.

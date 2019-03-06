---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform 시작하기
{: #get-started-ibp}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform은 사용자가
직접 선택한 환경에서 블록체인 컴포넌트를 배치할 수 있는 관리되는 전체 스택의
BaaS(blockchain-as-a-service) 오퍼링을 제공합니다. 환경은
{{site.data.keyword.cloud_notm}}, {{site.data.keyword.cloud_notm}} Private을 통한 온프레미스,
AWS(Amazon Web Services) 등의 서드파티일 수 있습니다. 이 튜토리얼에서는
{{site.data.keyword.blockchainfull_notm}} Platform을 사용하여
기본 블록체인 네트워크를 설정하는 일반적인 프로세스에 대해 설명합니다.
{:shortdesc}

**중요:** {{site.data.keyword.blockchainfull_notm}} Platform 오퍼링을
사용하기 전에 [면책사항](/docs/services/blockchain/needtoknow.html#disclaimer) 절에서 기술 및 지원 정보를 읽으십시오.


## 시작하기 전에
{: #get-started-ibp-prereqs}

{{site.data.keyword.blockchainfull_notm}} Platform은 모든 유형의 사용자가 블록체인 과정을 시작하고 애플리케이션을 프로덕션으로 이동하는 데 도움이 되는 여러 오퍼링을 제공합니다. 사용할 환경 및 오퍼링을 결정해야 합니다. 그림 1에는 다양한 오퍼링에 대한 기능, 대상 독자, 가격 및 클라우드 플랫폼이 나열되어 있습니다. 각 오퍼링에 대한 자세한 정보를 보려면 다음 표에서 오퍼링을 클릭하십시오.

| **오퍼링** | **포함된 내용** | **청구 정책** | **Cloud 플랫폼** |
| ------------------------- |-----------|-----------|-----------|-----------|
|[**스타터 플랜**](/docs/services/blockchain/starter_plan.html#starter-plan-about) | 기본 서비스 레벨, 개발 및 테스트 환경이 있는 {{site.data.keyword.IBM_notm}}에서 관리되는 네트워크 |월별 구독 | {{site.data.keyword.cloud_notm}} |
|[**엔터프라이즈 플랜**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) | 고급 서비스 레벨 및 엔터프라이즈 프로덕션 준비 환경이 있는 {{site.data.keyword.IBM_notm}}에서 관리되는 네트워크 |월별 구독 | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | {{site.data.keyword.cloud_notm}} Kubernetes에서 블록체인 컴포넌트를 배치하고 관리할 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 | 베타의 경우, 무료 | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | 배치 가능한 CA, 순서 지정자 및 피어 Helm 차트 | [VPC 가격](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) 및 무료 커뮤니티 에디션 | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | {{site.data.keyword.cloud_notm}} 외부의 원격 피어를 배치하기 위한 AWS 빠른 시작 템플릿 | 무료 | AWS |

*그림 1. {{site.data.keyword.blockchainfull_notm}} Platform 오퍼링*

스타터 플랜을 프로덕션 용도로 사용하지 마십시오. 이는 개발 및 테스트 환경이며 프로덕션 워크로드에 적합하지 않습니다.
{: important}

## 1단계: 오퍼링 얻기
{: #get-started-ibp-step1}

{{site.data.keyword.blockchainfull_notm}} Platform 오퍼링을 얻으려면 클라우드 계정 또는 PPA 라이센스가 필요합니다.

* **스타터 플랜**, **엔터프라이즈 플랜** 및 **{{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타**

  이러한 오퍼링은 {{site.data.keyword.cloud_notm}}에서 사용 가능하며 {{site.data.keyword.cloud_notm}}의 [카탈로그 대시보드 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog "카탈로그")에서 찾을 수 있습니다.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  이 오퍼링은 배치 가능한 Helm 차트로 전달되며 유료 에디션 및 무료 커뮤니티 에디션이 둘 다 있습니다. [Passport Advantage 온라인 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/software/passportadvantage/pao_customer.html)에서 엔터프라이즈 에디션을 다운로드하거나 [GitHub ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz)에서 무료 커뮤니티 에디션을 다운로드할 수 있습니다.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  이 오퍼링은 AWS에서 사용 가능하며 [빠른 시작 템플리트 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/)를 사용하여 AWS에서 블록체인 피어를 배치할 수 있습니다.

## 2단계: {{site.data.keyword.blockchainfull_notm}} Platform 배치
{: #get-started-ibp-step2}

* **스타터 플랜**, **엔터프라이즈 플랜** 및 **{{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타**

  {{site.data.keyword.cloud_notm}}에 로그인하여 오퍼링을 사용하여 서비스 인스턴스를 작성하십시오. 스타터 플랜을
사용하는 경우, [서비스 인스턴스를 작성](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan)한
직후에 기본 구성으로 블록체인 네트워크를 가져올 수 있습니다. 엔터프라이즈 플랜 또는 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타를
사용하는 경우, 마법사를 따라 네트워크의 초기 구성을 완료해야 합니다. 자세한 정보는
[엔터프라이즈 플랜 네트워크 작성](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) 또는 [{{site.data.keyword.cloud_notm}} Kubernetes 서비스에 {{site.data.keyword.blockchainfull_notm}} Platform 배치](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks)를 참조하십시오.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  네트워크를 배치하기 전에 [Helm 차트를 {{site.data.keyword.cloud_notm}} Private으로 가져와야](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) 합니다. 그런 다음 [{{site.data.keyword.blockchainfull_notm}} Platform을 구성하고 설치](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-deploy-icp)할 수 있습니다.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  이 오퍼링은 AWS에서 사용 가능하며 [빠른 시작 템플리트 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/)를 사용하여 AWS에서 블록체인 피어를 배치할 수 있습니다.

## 다음 단계
{: #get-started-ibp-next-steps}

선택한 환경에 {{site.data.keyword.blockchainfull_notm}} Platform을 배치한 후에
컨소시엄, 노드, 채널, 스마트 계약을 사용하여 네트워크를 설정하고 해당 네트워크에서 트랜잭션을 시작할 수 있습니다. 자세한 정보는
이 문서의 왼쪽 탐색에 있는 **HOW TO** 절 아래의 태스크 주제를 참조하십시오.

## 지원 받기
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}}은 {{site.data.keyword.IBM_notm}}에서 구현된
블록체인 솔루션에서 여러 지원 옵션을 제공합니다. {{site.data.keyword.blockchainfull_notm}} Platform
지원에 대한 자세한 정보는 [지원 받기](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support)를 참조하십시오.

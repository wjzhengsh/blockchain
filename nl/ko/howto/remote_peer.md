---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 원격 피어 정보
{: #remote-peer-overview}


***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


기존 Blockchain 네트워크에 연결한 후 ICP({{site.data.keyword.cloud_notm}} Private)에서 <!--[AWS]or on AWS Cloud --> {{site.data.keyword.blockchainfull}} Platform 원격 피어를 실행할 수 있습니다. {{site.data.keyword.cloud_notm}} 외부에서 피어를 실행하면 {{site.data.keyword.cloud_notm}} 내부의 기존 네트워크를 활용하면서 Blockchain 네트워크에 가입하고 확장할 수 있는 유연성을 제공합니다. 원격 피어는 플랫폼에서 인증 기관(CA)과 순서 지정 서비스를 이용하지만, {{site.data.keyword.cloud_notm}} 외부의 기타 애플리케이션과 피어를 같은 위치에 배치할 수 있습니다.{:shortdesc}

<!--[AWS]Move ICP description here.-->ICP({{site.data.keyword.cloud_notm}} Private)는 온프레미스 환경에서 프라이빗 클라우드를 빌드하는 데 사용하는 Kubernetes 기반 플랫폼입니다. ICP를 사용하여 원격 피어를 실행하고 원격 피어를 {{site.data.keyword.blockchainfull_notm}} Platform의 Blockchain 네트워크에 연결할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP에서는 ICP의 스토리지, 보안, 로깅 및 지원 서비스를 활용하므로, 온프레미스에서 원격 피어를 관리할 수 있습니다. ICP에 관한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private 문서 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 문서")를 참조하십시오.  

**참고:** {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer는 현재 평가와 실험에 적합한 베타 오퍼링입니다. **이 베타 에디션은 프로덕션용이 아닙니다.** 액세스 및 자세한 정보는 [라이센스 및 가격](#remote-peer-license-pricing)을 참조하십시오.

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

베타 에디션에서는 {{site.data.keyword.cloud_notm}} Private(ICP), v2.1.0.3의 클라우드 플랫폼을 지원합니다. {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크와 ICP의 원격 피어 노드는 동일한 **Fabric 버전 1.1**에서 실행되어야 합니다.

## 고려사항
{: #remote-peer-limitations}

원격 피어에서는 {{site.data.keyword.blockchainfull_notm}} Platform에서 호스팅되는 피어의 전체 기능 또는 지원에 액세스할 권한이 없습니다. 원격 피어를 실행하기 전에 다음 제한사항을 이해해야 합니다.
- 다른 클라우드 환경에서 실행되는 원격 피어는 {{site.data.keyword.cloud_notm}}에 있는 Blockchain 네트워크의 네트워크 모니터에 표시되지 않습니다.
- 원격 피어는 네트워크 모니터 UI의 Swagger UI를 사용하여 처리할 수 없습니다.
- 사용자가 원격 피어의 상태 모니터링, 보안, 로깅 및 리소스 사용량을 관리해야 합니다.
- Hyperledger Fabric v1.1 레벨에 있는 Blockchain 네트워크에만 원격 피어를 연결할 수 있습니다.
- 원격 피어의 데이터베이스 유형은 Blockchain 네트워크의 데이터베이스 유형(LevelDB 또는 CouchDB)과 일치해야 합니다.
- CouchDB Fauxton 인터페이스는 원격 피어에서 사용할 수 없습니다.
- 원격 피어용 [Gossip](../glossary.html#gossip)은 현재 지원되지 않습니다.


## 전제조건
{: #remote-peer-prereq}

원격 피어를 사용하려면 {{site.data.keyword.blockchainfull_notm}} Platform의 스타터 플랜 또는 엔터프라이즈 플랜 네트워크 멤버인 조직이 있어야 합니다. 원격 피어에서는 {{site.data.keyword.blockchainfull_notm}} Platform Plan 네트워크의 순서 지정 서비스, Hyperledger Fabric CA 및 API 엔드포인트를 활용하여 운영합니다. Blockchain 네트워크의 구성원이 아니면 네트워크를 작성하거나 가입해야 합니다. 자세한 정보는 [네트워크 작성](../get_start.html#creating-a-network) 또는 [네트워크에 가입](../get_start.html#joining-a-network)을 참조하십시오.


## 라이센스 및 가격
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->ICP에서 실행할 원격 피어에 액세스하려면 [ICP의 원격 피어 라이센스 및 가격](remote_peer_icp.html#license-pricing-icp "ICP의 원격 피어 라이센스 및 가격")을 참조하십시오. 원격 피어의 베타 에디션 라이센스는 무료입니다.<!--[AWS] for both platforms.--> 그 후에는 GA(Generally Available) 오퍼링으로 베타 에디션을 대체합니다.

베타에서 GA로 마이그레이션하는 기능은 지원되지 않습니다. GA 오퍼링이 릴리스된 후 사용하려면 새 원격 피어를 다운로드하여 설치해야 합니다. 그런 다음 새 원격 피어를 베타 원격 피어와 동일한 네트워크의 동일한 채널에 가입할 수 있습니다.

**참고:** 원격 피어를 운영하려면 {{site.data.keyword.blockchainfull_notm}} Platform에서 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 속한 조직이 있어야 합니다. 즉, 사용자나 네트워크의 다른 구성원이 조직을 위해 IBM Blockchain [멤버십 요금](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing)을 지급해야 합니다. 요금 지불에 관한 자세한 정보는 [지급 모드](paying_mode.html)를 참조하십시오.  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## 원격 피어 배치
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

다음 다이어그램에서는 ICP에 {{site.data.keyword.blockchainfull_notm}} Platform 원격 피어를 배치하는 단계를 설명합니다. ICP에서 원격 피어를 배치하는 방법에 관한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private에 원격 피어 배치](remote_peer_icp.html "{{site.data.keyword.cloud_notm}} Private에 원격 피어 배치")를 참조하십시오.

![ICP에 원격 피어 배치 플로우](../images/remote_peer_ICP_flow.png "ICP에 원격 피어 배치 플로우")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*그림 1. ICP에 원격 피어 배치 플로우*


## 원격 피어 운영
{: #operate-remote-peer}

원격 피어를 배치한 다음 여러 운영 단계를 완료해야 피어에서 네트워크에 트랜잭션을 제출할 수 있습니다. 이 운영 단계에는 채널에 조직을 추가하고 원격 피어에 채널이 가입하며 원격 피어에 체인코드를 설치하고 채널에서 체인코드를 인스턴스화하며 애플리케이션을 원격 피어에 연결하는 단계가 포함됩니다. 자세한 정보는 <!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[{{site.data.keyword.cloud_notm}} Private에서 원격 피어 운영](remote_peer_operate_icp.html#remote-peer-operate)을 참조하십시오.

## 데이터 상주
{: #data-residency}

데이터 상주 요구사항은 데이터를 둘 수 있는 위치를 관리하는 제한사항입니다. 데이터 상주의 가장 일반적인 요구사항은 특정 국가의 법률과 관련되어 있습니다. 해당 법률에 따르면 IT 시스템에서 처리하고 저장하는 모든 고객 데이터는 특정 국가 내에 유지해야 합니다. 마찬가지로 정부, 보건, 금융 서비스와 같이 고도로 규제된 일부 업계에서는 모든 데이터를 완전히 방화벽 뒤에 보관해야 합니다. 또한 일부 회사에는 특정 유형 데이터(일반적으로 개인 식별 가능 정보)의 기밀을 보장하고 완전히 회사 방화벽 뒤에 보관하도록 하는 기업 정책이 있습니다. 따라서 데이터 상주를 수행하려면 Blockchain 네트워크의 모든 컴포넌트가 동일한 [채널](../glossary.html#channel)의 일부여야 하며 한 국가 내에 있어야 합니다.

데이터 상주 요구사항을 처리하려면 {{site.data.keyword.blockchainfull_notm}} Platform의 기초가 되는 Hyperledger Fabric 아키텍처를 이해하는 것이 중요합니다. 아키텍처는 순서 지정 서비스, 인증 기관(CA) 및 피어의 세 가지 핵심 요소를 중심으로 구성됩니다. 피어에서 순서 지정 서비스에서 순서 지정된 상태 업데이트를 블록의 양식으로 받고 해당 상태와 원장을 유지보수합니다. 따라서 피어와 순서 지정 서비스는 직접적인 관계가 있습니다. 원장에는 트랜잭션 로그에 포함된 모든 키와 데이터의 최신 값이 들어 있습니다.

또한 클라이언트 애플리케이션에서는 [Fabric SDK](../v10_application.html#using-the-fabric-sdks)를 사용하여 트랜잭션을 피어와 순서 지정 서비스에 보냅니다. 이러한 트랜잭션에는 원장에 키-값 쌍을 포함하는 [읽기-쓰기 시맨틱 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "읽기-쓰기 세트 시맨틱") 데이터가 포함됩니다.

비즈니스에서 국가 내 데이터 상주가 요구사항이면 순서 지정 서비스, 피어 노드 및 클라이언트 애플리케이션도 같은 국가에 있어야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform 네트워크가 {{site.data.keyword.cloud_notm}}에 작성되면 네트워크의 위치를 선택할 수 있습니다. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->지역과 위치에 관한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform 지역 및 위치](../reference/ibp_regions.html)를 참조하십시오. 해당 국가 중 하나에서 데이터 상주를 수행하려면 원격 피어가 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크 위치와 동일한 국가에 있어야 합니다.

{{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 국가 간 원격 피어와 순서 지정 서비스 노드가 있으면 채널을 사용하여 네트워크의 피어 세브세트로 데이터를 분리할 수 있습니다. 순서 지정 노드는 항상 네트워크를 호스트하기 위해 선택한 데이터 센터에 있습니다. 순서 지정자가 국경을 넘어 존재할 수 없습니다. 그러나 피어는 {{site.data.keyword.cloud_notm}} 외부의 원격 위치나 데이터 센터에 있을 수 있습니다. 따라서 데이터 상주를 수행하려면 순서 지정자와 모든 피어(데이터 센터에 로컬인 피어 또는 원격 피어)가 같은 국가에 있는 채널을 작성할 수 있습니다. 이 방법으로 순서 지정자와 피어가 공유하는 모든 데이터가 한 국가 내에서 유지됩니다. 순서 지정자와 원격 피어가 여러 국가에 걸쳐 있으며 데이터 상주가 필요하지 않은 경우, 필요하면 다른 채널도 존재할 수 있습니다.

향후 Hyperledger Fabric의 새 기술에서는 [개인 데이터 콜렉션![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "개인 데이터 콜렉션") 및 Zero Knowledge Proof를 사용하여 추가 데이터 상주를 수행하는 기능을 향상시킬 것입니다.

- 개인 데이터 콜렉션을 사용하면 인증된 피어(예: 한 국가에 있는 피어)만 볼 수 있도록 개인 데이터를 피어 투 피어로 공유할 수 있습니다(gossip 프로토콜 사용). 데이터는 피어의 개인 데이터베이스에 저장됩니다. 순서 지정 서비스는 여기에 관련되지 않으며 개인 데이터를 볼 수 없습니다. 해당 데이터의 해시가 채널에 있는 모든 피어의 원장에 기록됩니다. 상태 유효성 검증에 사용된 해시는 트랜잭션의 증거로 사용되며 감사 용도로 사용할 수 있습니다.

- ZKP(Zero-Knowledge Proof)를 사용하면 “승인자”가 시크릿 자체를 노출하지 않고 시크릿을 알고 있음을 “확인자”에게 보증할 수 있습니다. 이 방식을 사용하면 알고 있는 내용을 표시하지 않고도 규정이 충족됨을 알고 있다고 표시할 수 있습니다.

이 기술에 관한 자세한 정보는 [Hyperledger Fabric을 사용한 개인 및 기밀 트랜잭션 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Hyperledger Fabric을 사용한 개인 및 기밀 트랜잭션")에 관한 백서를 참조하십시오.

## 지원 받기
{: #remote-peer-support}

{{site.data.keyword.blockchainfull_notm}} 원격 피어 오퍼링의 베타 에디션은 탐색, 개발 및 테스트용입니다. **이 에디션을 프로덕션에 사용하지 마십시오.** {{site.data.keyword.blockchainfull_notm}} Platform에서는 이 에디션을 지원하지 않습니다. 원격 피어와 관련된 문제가 발생하면 [Blockchain 리소스 및 지원 포럼](../v10_dashboard.html#support-forums)을 참조하십시오. 네트워크 모니터의 **도움말** 화면에서 지원 리소스도 볼 수 있습니다.  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

{{site.data.keyword.cloud_notm}} Private과 관련된 문제를 위해 ICP에서는 [무료 디지털 지원 및 커뮤니티 에디션 지원 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "ICP 무료 디지털 지원 및 커뮤니티 에디션 지원")을 제공합니다.

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

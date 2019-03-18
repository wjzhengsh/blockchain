---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# FAQ
{: #ibp-v2-faq}

## {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타에서 사용되는 Hyperledger Fabric의 버전은 무엇입니까? 
{: #ibp-v2-faq-fabric-version}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타는 Hyperledger Fabric v1.4를 사용하며 gossip, 서비스 발견 및 개인용 데이터에 대한 지원을 제공합니다. 

## {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타에 대한 비용을 지불해야 합니까? 
{: #ibp-v2-faq-cost}
{: faq}

베타 릴리스는 무료입니다. 그러나 무료 클러스터를 선택하지 않을 경우 {{site.data.keyword.cloud_notm}} Kubernetes 클러스터에 대한 비용이 발생할 수 있습니다. 하지만 무료 클러스터는 {{site.data.keyword.cloud_notm}} Kubernetes 서비스 정책에 따라 30일 후에 자동으로 삭제되며 성능과 용량이 제한적입니다. 서비스를 30일 이상 계속 이용하려면 Kubernetes 서비스의 표준 인스턴스에 대한 비용을 지불해야 합니다. 

## 기존 {{site.data.keyword.cloud_notm}} Kubernetes 서비스 클러스터를 사용할 수 있습니까? 
{: #ibp-v2-faq-existing-cluster}
{: faq}

기존 Kubernetes 클러스터가 다음 조건을 충족할 경우 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타에서 작동합니다. 
- {{site.data.keyword.cloud_notm}} 댈러스 지역에 있습니다. 
- Kubernetes v1.11 이상을 실행 중입니다. [클러스터의 Kubernetes 버전을 업그레이드](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes)하는 방법은 다음 지시사항을 참조하십시오. 
- 클러스터에 사용 가능한 리소스가 충분히 있습니다. 자세한 정보는 [최소 리소스 요구사항](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required)에 대한 주제를 참조하십시오. 

## 피어 원장에 사용되는 데이터베이스는 무엇입니까? 
{: #ibp-v2-faq-couchDB}
{: faq}

모든 피어는 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타를 사용하여 배치되며 CouchDB 데이터베이스를 사용합니다. 그러나 CouchDB를 직접 조회할 수는 없습니다. 

## 스마트 계약에 지원되는 언어는 무엇입니까? 
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform은 Go, Node.js 및 Java로 작성된 스마트 계약을 지원합니다. 새 Hyperledger Fabric 프로그래밍 모델은 현재 Node.js만 지원하지만, 조만간 더 많은 언어를 지원할 계획입니다. 기존 애플리케이션 코드를 보존하거나 Node.js 외의 언어에 대해 Fabric SDK를 사용하려는 경우 계속 하위 레벨 Fabric SDK API를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타 네트워크에 연결할 수 있습니다. 

## V2.0 베타에서 v2.0 GA로 마이그레이션할 수 있습니까? 
{: #ibp-v2-faq-migrate}
{: faq}

엔터프라이즈 플랜 고객은 IBM Blockchain Platform 2.0이 일반적으로 사용 가능(GA)하게 되면 해당 버전으로 마이그레이션할 수 있습니다. 그러나 2.0 베타 오퍼링을 2.0 GA 오퍼링으로 마이그레이션할 수는 없습니다. 또한 스타터 플랜 인스턴스를 2.0 베타 또는 2.0 GA 제품(사용 가능해진 경우)으로 마이그레이션할 수도 없습니다. 

## 로깅 서비스에 액세스할 수 있으며 사용 가능한 로그는 무엇입니까? 
{: #ibp-v2-faq-logs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 2.0을 사용할 경우 이제 Kubernetes 대시보드에서 직접 피어, CA 및 순서 지정자에 액세스할 수 있습니다. 로그를 실시간으로 손쉽게 구문 분석해주는 [{{site.data.keyword.cloud_notm}} LogDNA ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis with LogDNA") 서비스를 이용하는 것이 좋습니다. 

## 기본 Hyperledger Fabric을 기반으로 {{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타를 사용할 때 얻는 가치는 무엇입니까? 
{: #ibp-v2-faq-native-fabric}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타를 사용할 경우 고객이 사용자 정의 블록체인 네트워크를 쉽게 배치할 수 있습니다. 이 플랫폼은 네트워크를 빠르게 배치할 수 있는 직관적인 콘솔과 스마트 계약을 쉽게 설치 및 인스턴화하는 기능을 제공합니다. 이 플랫폼을 사용할 경우, 스마트 계약 개발 및 네트워크 빌드와 관련하여 {{site.data.keyword.IBM_notm}} 전문가의 지원을 받을 수도 있습니다. 

## 성능을 최대화하고 {{site.data.keyword.blockchainfull_notm}} Platform 솔루션을 스케일링할 수 있는 방법은 무엇입니까? 
{: #ibp-v2-faq-perf-scale}
{: faq}

솔루션의 성능에 영향을 주는 요인 및 블록체인 스케일링 방법에 대한 정보는 [블로그 시리즈![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Hyperledger Fabric 성능 및 스케일에 대한 질문에 답하기")를 확인하십시오. 

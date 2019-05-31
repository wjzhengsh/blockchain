---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: FAQs, can I, upgrade, what version, peer ledger database, supported languages, why do I, regions

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

- [피어 원장에 사용되는 데이터베이스는 무엇입니까?](#ibp-v2-faq-v2-IBP-Overview-1-3)
- [스마트 계약에 지원되는 언어는 무엇입니까? ](#ibp-v2-faq-v2-IBP-Overview-1-4)
- [V1.0에서 새 {{site.data.keyword.blockchainfull_notm}} Platform으로 업그레이드할 수 있습니까?](#ibp-v2-faq-v2-IBP-Overview-1-5)
- [기본 Hyperledger Fabric을 기반으로 {{site.data.keyword.blockchainfull_notm}} Platform을 사용할 때 얻는 가치는 무엇입니까?](#ibp-v2-faq-v2-IBP-Overview-1-7)
- [내 {{site.data.keyword.blockchainfull_notm}} Platform 서비스를 삭제하면 어떻게 됩니까? ](#ibp-v2-faq-v2-IBP-Overview-1-8)
- [{{site.data.keyword.cloud_notm}}에서 실행 중인 블록체인 서비스에 사용 가능한 지역은 무엇입니까?](#ibp-v2-faq-v2-IBP-Overview-1-9)
- [{{site.data.keyword.blockchainfull_notm}} Platform에서 사용되는 Hyperledger Fabric의 버전은 무엇입니까? ](#ibp-v2-faq-v2-Hyperledger-Fabric-3-1)
- [기존 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터를 사용할 수 있습니까?](#ibp-v2-faq-v2-Infrastructure-4-2)
- [로깅 서비스에 액세스할 수 있으며 사용 가능한 로그는 무엇입니까? ](#ibp-v2-faq-v2-Logging-and-Monitoring-11-6)


## 피어 원장에 사용되는 데이터베이스는 무엇입니까?
{: #ibp-v2-faq-v2-IBP-Overview-1-3}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform과 함께 배치된 모든 피어는 CouchDB를 원장 데이터베이스로 사용합니다.

## 스마트 계약에 지원되는 언어는 무엇입니까?
{: #ibp-v2-faq-v2-IBP-Overview-1-4}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform은 Go, Node.js로 작성된 스마트 계약을 지원합니다. 새 Hyperledger Fabric 프로그래밍 모델은 현재 Node.js만 지원하지만 곧 더 많은 언어를 지원할 계획입니다. 기존 애플리케이션 코드를 보존하거나 Node.js 이외의 언어로 Fabric SDK를 사용하려는 경우 하위 레벨 Fabric SDK API를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 계속 연결할 수 있습니다. 

## V1.0에서 새 {{site.data.keyword.blockchainfull_notm}} Platform으로 업그레이드할 수 있습니까?
{: #ibp-v2-faq-v2-IBP-Overview-1-5}
{: faq}

스타터 플랜의 경우 가능하지 않습니다. 스타터 플랜에서 새 {{site.data.keyword.blockchainfull_notm}} Platform으로 업그레이드할 수 없습니다.
엔터프라이즈 플랜의 경우 향후 새 {{site.data.keyword.blockchainfull_notm}} Platform으로 업그레이드할 수 있습니다. 도움을 주기 위해 {{site.data.keyword.blockchainfull_notm}} Platform 팀에서 계정 소유자에게 전송하는 이메일을 수신합니다.

## 기본 Hyperledger Fabric을 기반으로 {{site.data.keyword.blockchainfull_notm}} Platform을 사용할 때 얻는 가치는 무엇입니까?
{: #ibp-v2-faq-v2-IBP-Overview-1-7}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform을 사용하면 고객이 사용자 정의된 블록체인 네트워크를 쉽게 배치할 수 있습니다. 직관적인 콘솔 UI를 사용하여 네트워크를 빠르게 배치하고, 스마트 계약을 쉽게 설치하고 인스턴스화하며, 트랜잭션을 모니터할 수 있습니다.

## 내 {{site.data.keyword.blockchainfull_notm}} Platform 서비스를 삭제하면 어떻게 됩니까?
{: #ibp-v2-faq-v2-IBP-Overview-1-8}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform 서비스 인스턴스를 삭제하면 콘솔만 삭제됩니다. 콘솔을 사용하여 작성된 모든 컴포넌트 및 이와 연관된 스토리지는 삭제되지 않습니다. Kubernetes  클러스터와 가져온 컴포넌트도 삭제되지 않습니다. Kubernetes 대시보드 또는 CLI 명령을 사용하여 직접 삭제할 때까지 Kubernetes 노드 및 스토리지에 대한 비용이 계속 청구됩니다.

## {{site.data.keyword.cloud_notm}}에서 실행 중인 블록체인 서비스에 사용 가능한 지역은 무엇입니까?
{: #ibp-v2-faq-v2-IBP-Overview-1-9}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform에 사용 가능한 지역은 [{{site.data.keyword.blockchainfull_notm}} Platform 지역](/docs/services/blockchain?topic=blockchain-ibp-regions-locations)에 나열되어 있습니다. 클러스터를 인식하려면 블록체인 서비스와 동일한 지역에 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터를 작성해야 합니다. 추가 지역이 곧 사용 가능하게 됩니다.

## {{site.data.keyword.blockchainfull_notm}} Platform에서 사용되는 Hyperledger Fabric의 버전은 무엇입니까?
{: #ibp-v2-faq-v2-Hyperledger-Fabric-3-1}
{: faq}

2019년 2월 기준으로, 베타가 Hyperledger Fabric 1.4.0을 사용하여 출시되었습니다.
2019년 5월 29일 기준으로, GA가 Hyperledger Fabric 1.4.1을 사용하여 출시되었습니다. Hyperledger Fabric 1.4.0은 GA 제품에서는 지원되지 않습니다.

## 기존 {{site.data.keyword.cloud_notm}} Kubernetes Service 클러스터를 사용할 수 있습니까?
{: #ibp-v2-faq-v2-Infrastructure-4-2}
{: faq}

기존 Kubernetes 클러스터가 다음 조건을 충족할 경우 {{site.data.keyword.blockchainfull_notm}} Platform에서 작동합니다.
- Kubernetes v1.11 이상의 안정적인 버전을 실행 중입니다. 
- 클러스터에 사용 가능한 리소스가 충분히 있습니다.

## 로깅 서비스에 액세스할 수 있으며 사용 가능한 로그는 무엇입니까?
{: #ibp-v2-faq-v2-Logging-and-Monitoring-11-6}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform을 사용할 경우 이제 Kubernetes 대시보드에서 직접 피어, CA 및 순서 지정자 로그에 액세스할 수 있습니다. 로그를 실시간으로 쉽게 구문 분석할 수 있도록 하는 {{site.data.keyword.cloud_notm}} LogDNA 서비스를 활용하는 것이 좋습니다.

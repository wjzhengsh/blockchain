---

copyright:
  years: 2019
lastupdated: "2019-04-17"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Kubernetes
{: #k8s-overview}

{{site.data.keyword.blockchainfull_notm}} Platform 무료 2.0 베타 콘솔은 {{site.data.keyword.cloud_notm}} Kubernetes Service를 통해 Kubernetes 클러스터에 배치됩니다. Kubernetes는 컨테이너화된 애플리케이션의 배치 자동화, 스케일링 및 관리를 위한 오픈 소스 시스템입니다.
{:shortdesc}

Kubernetes는 컨테이너 중심 관리 환경을 제공합니다. 사용자 워크로드를 대신하여 컴퓨팅, 네트워킹 및 스토리지 인프라를 조정합니다. Kubernetes는 IaaS(Infrastructure as a Service)의 유연성과 함께 PaaS(Platform as a Service)의 뛰어난 단순성을 제공하며 인프라 제공자 간의 이식성을 지원합니다.

다음 다이어그램은 Kubernetes의 아키텍처에 대해 설명합니다. 노드, 컨테이너 및 팟(Pod)에 대한 자세한 설명은 아래의 [주요 Kubernetes 오브젝트](#k8s-overview-key-obj) 섹션을 참조하십시오.

![Kubernetes 아키텍처 다이어그램](../images/k8s-archi-diagram.svg "{{site.data.keyword.cloud_notm}} Kubernetes Service 아키텍처")
*그림 1. Kubernetes 아키텍처 다이어그램*


## {{site.data.keyword.cloud_notm}} Kubernetes Service
{: #k8s-overview-iks}

{{site.data.keyword.cloud_notm}} Kubernetes Service는 Docker 컨테이너, Kubernetes 기술, 직관적인 사용자 경험, 기본 제공 보안 및 격리를 결합하여 컴퓨팅 호스트의 클러스터에서 컨테이너화된 앱의 배치, 오퍼레이션, 스케일링 및 모니터링을 자동화하는 강력한 도구를 제공합니다.

{{site.data.keyword.cloud_notm}} Kubernetes Service에 대한 자세한 정보는 {{site.data.keyword.cloud_notm}} Kubernetes Service 문서에서 다음 주제를 참조하십시오.
- [{{site.data.keyword.cloud_notm}} Kubernetes Service 기술 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers/cs_tech.html#ibm-cloud-kubernetes-service-technology "{{site.data.keyword.cloud_notm}} Kubernetes Service 기술 문서")
- [{{site.data.keyword.cloud_notm}} Kubernetes Service를 선택해야 하는 이유 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers?topic=containers-cs_ov#cs_ov "{{site.data.keyword.cloud_notm}} Kubernetes Service를 선택해야 하는 이유에 대한 문서")
- [Kubernetes 전략 정의 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](/docs/containers?topic=containers-strategy#strategy "Kubernetes 전략 정의")


## 주요 Kubernetes 오브젝트
{: #k8s-overview-key-obj}

- **클러스터**

  Kubernetes에서 관리되는 컨테이너화된 애플리케이션을 실행하는 일련의 시스템(노드라고 함)입니다. 클러스터에는 여러 작업자 노드와 하나 이상의 마스터 노드가 있습니다.

- **노드(Node)**

  노드는 Kubernetes의 작업자 시스템입니다. 노드는 클러스터에 따라 VM 또는 실제 시스템일 수 있습니다. 각 노드는 팟(Pod)을 실행하는 데 필요한 서비스를 포함하며 마스터 컴포넌트에서 관리됩니다. 노드의 서비스에는 컨테이너 런타임 `kubelet` 및 `kube-proxy`가 포함되어 있습니다. 자세한 정보는 Kubernetes 문서의 [Kubernetes 노드 섹션 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/architecture/nodes/ "Kubernetes 노드 섹션")을 참조하십시오.

- **컨테이너**

  소프트웨어와 모든 해당 종속 항목을 포함하는 경량의 이식 가능한 실행 가능 이미지입니다. 컨테이너는 애플리케이션을 기본 호스트 인프라에서 분리하여 여러 클라우드 또는 OS 환경에서 보다 쉽게 배치하고 스케일링할 수 있도록 합니다.

- **팟(Pod)**

  가장 작고 단순한 Kubernetes 오브젝트입니다. 팟(Pod)은 클러스터에서 실행 중인 컨테이너 세트를 나타냅니다. 일반적으로 팟(Pod)은 단일 기본 컨테이너를 실행하도록 설정됩니다. 또한 로깅과 같은 보충 기능을 추가하는 선택적 사이드카 컨테이너를 실행할 수도 있습니다. 일반적으로 팟(Pod)은 배치를 통해 관리됩니다. 자세한 정보는 Kubernetes 문서의 [Kubernetes 팟(Pod) 섹션 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/workloads/pods/pod/)을 참조하십시오.

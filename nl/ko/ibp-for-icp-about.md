---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private에 대한 정보
{: #overview}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform에서 사용자가 x86, LinuxONE 및 IBM Z에 인증 기관(CA), 순서 지정자 및 피어를 배치할 수 있도록 해주는 Kubernetes를 기반으로 한 컨테이너화된 애플리케이션을 개발 및 관리하기 위한 애플리케이션 플랫폼인 IBM Blockchain Platform for ICP({{site.data.keyword.cloud_notm}} Private)가 릴리스되었습니다. {{site.data.keyword.blockchainfull_notm}} Platform for ICP는 Hyperledger Fabric v1.2.1을 기반으로 하며 Kubernetes Helm 차트를 사용하여 배치됩니다.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private은 ICP 고객이 해당 로컬 환경에 블록체인 컴포넌트를 배치하기 위해 번들화된 제품입니다. Helm 차트를 가져온 후에는 해당 제품을 ICP 카탈로그에서 {{site.data.keyword.blockchainfull_notm}} Platform 타일로 찾을 수 있습니다. ICP에 대한 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private 버전 3.1.0 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0") 문서를 참조하십시오.

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP에서 제공하는 기능

{{site.data.keyword.blockchainfull_notm}} Platform for ICP(IBM Cloud Private)를 통해 Hyperledger Fabric 블록체인의 모든 기본 컴포넌트(인증 기관, 순서 지정 서비스 및 피어)를 배치할 수 있습니다. 비즈니스 요구사항에 따라 다양한 컴포넌트를 배치할 수 있도록 유연성을 제공해 줍니다. {{site.data.keyword.blockchainfull_notm}} for ICP를 사용하여 블록체인 컨소시엄에서 조직을 함께 바인드하는 순서 지정 서비스를 배치 및 구성함으로써 블록체인 네트워크를 시작할 수 있습니다. 또한 피어를 배치하고 Fabric을 기반으로 한 컴포넌트를 사용하는 다른 네트워크(예: ICP 또는 IBM Cloud에서 호스팅되는 스타터 플랜 및 엔터프라이즈 플랜 네트워크를 사용하여 클라우드 전체에 배치된 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크)에 가입할 수 있습니다. Hyperledger Fabric 네트워크의 구성 요소에 대한 자세한 정보는 [블록체인 컴포넌트 개요](blockchain_component_overview.html)를 참조하십시오.

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP의 적합성

{{site.data.keyword.blockchainfull_notm}} Platform 컴포넌트를 {{site.data.keyword.cloud_notm}} 외부에서 실행하는 경우 블록체인 네트워크에 가입하거나 확장할 수 있는 유연성이 제공됩니다. 이를 통해 네트워크 작성자가 원하는 플랫폼을 사용하는 동안 새 구성원이 가입할 수 있도록 허용하여 네트워크를 확장할 수 있습니다. 이 경우 블록체인 네트워크에 가입하려는 조직에서 해당 피어를 기존 애플리케이션과 함께 배치하거나 SOR(System of Record)에 통합할 수 있습니다.

**중요:** {{site.data.keyword.blockchainfull_notm}} Platform for ICP를 배치하는 프로세스는 매우 복잡하며 Fabric에 대한 고도의 전문성을 보유한 것으로 가정합니다. Fabric, ICP 또는 {{site.data.keyword.blockchainfull_notm}} Platform에 익숙하지 않으며 개발 환경 또는 개념 증명을 설정하는 것이 목표인 경우 [스타터 플랜](starter_plan.html)을 대신 사용하십시오. {{site.data.keyword.blockchainfull_notm}} Platform for ICP에서는 지원되는 잠재적인 배치 구성 중 일부가 지원되지 않을 수도 있습니다.

이 오퍼링의 사용자는 자체 보안 및 인프라를 관리하게 됩니다. {{site.data.keyword.cloud_notm}}에서는 해당 서비스를 제공하지 않습니다. 시작하기 전에 다음 절에 있는 [고려사항 및 제한사항](#ibp-icp-considerations)을 검토하십시오.

## 고려사항 및 제한사항
{: #ibp-icp-considerations}

시작하기 전에 다음과 같은 **고려사항** 및 **제한사항**을 숙지해야 합니다.

- 사용자가 컴포넌트의 상태 모니터링, 보안, 로깅 및 리소스 사용량 관리 작업을 수행해야 합니다.
- 다른 클라우드 환경에서 실행되는 컴포넌트는 {{site.data.keyword.cloud_notm}}에서 실행되는 네트워크에 대한 네트워크 모니터에 표시되지 않습니다.
- Helm 차트는 순서 지정자, 피어 또는 CA의 단일 인스턴스를 배치합니다.
- 릴리스 이름이 다를 경우 ICP에 있는 단일 네임스페이스에 복수의 컴포넌트를 배치할 수 있습니다.
- 네트워크 모니터 UI의 Swagger UI를 사용하여 컴포넌트에 주소를 지정할 수는 없습니다.
- 상호 TLS는 지원되지 않습니다.

**CA 고려사항**
- 이 Helm 차트는 CA의 단일 인스턴스를 배치합니다. 각각의 조직에 대한 별도의 CA를 보유하는 것이 우수 사례로 간주되기 때문에 여러 개의 CA를 배치해야 할 수도 있습니다. 예를 들어 하나의 순서 지정자 및 세 개의 피어를 배치하려는 경우 둘 이상의 CA(하나는 순서 지정자 조직을 위한 것이고, 다른 하나는 피어 조직을 위한 것임)가 필요합니다.
- 별도의 MySQL 데이터베이스를 실행하도록 선택할 수 있지만 Helm 차트에는 이 옵션이 존재하지 않습니다. Helm 차트는 사용자별 등록 수 및 취소된 인증서의 수에 대한 추적을 포함하여 CA의 데이터베이스 필요성을 처리하기 위해 CA 내에 SQLite 데이터베이스를 배치합니다.

**순서 지정자 고려사항**
- 순서 지정자 서비스는 v1.2 Hyperledger Fabric의 모든 컴포넌트와 호환 가능합니다.
- 이 Helm 차트는 SOLO 순서 지정 서비스의 단일 인스턴스(하나의 순서 지정자)를 배치합니다. 순서 지정 서비스에 고가용성을 적용하기 위해 하나의 채널에 둘 이상의 SOLO 순서 지정자를 배치할 수는 없습니다. 이것이 SOLO 순서 지정 서비스를 프로덕션이 아닌 개발 환경용으로 고려하는 이유 중 한 가지입니다. 하지만 SOLO 순서 지정 서비스에 대한 복수의 인스턴스를 서로 다른 네트워크(즉, 별도의 컨소시엄)에 배치할 수 있습니다.

**피어 고려사항**

- Fabric 레벨 v1.1 또는 v1.2.1에 있는 블록체인 네트워크에만 피어를 연결할 수 있습니다. 네트워크 모니터에서 [네트워크 환경 설정 창](../v10_dashboard.html#network-preferences)을 열어서 Hyperledger Fabric 버전을 찾을 수 있습니다. 스타터 또는 엔터프라이즈 네트워크에서 피어 연결 정보를 검색하기 위한 [지시사항](#starter-enterprise-network-endpoints)을 수행하십시오.
- 피어의 데이터베이스 유형이 블록체인 네트워크의 데이터베이스 유형(LevelDB 또는 CouchDB)과 일치해야 합니다.
- 피어에서는 CouchDB Fauxton 인터페이스를 사용할 수 없습니다.
- 피어용 [가십](../glossary.html#gossip)은 현재 지원되지 않습니다. 이 내용은 가십에 의존하는 Fabric 기능(예: [개인용 데이터 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "개인용 데이터") 및 [서비스 검색 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "서비스 검색"))도 지원되지 않음을 의미합니다.

## 시스템 전제조건
{: #prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP에서는 다음과 같은 운영 체제를 지원합니다.
- RHEL(Red Hat Enterprise Linux) 7.3, 7.4, 7.5
- Ubuntu 18.04 LTS 및 16.04 LTS
- SLES(SUSE Linux Enterprise Server) 12 SP3

{{site.data.keyword.blockchainfull_notm}} Platform for ICP Helm 차트는 다음과 같은 작업자 노드 및 백업 스토리지를 사용하여 Ubuntu Linux 기반 {{site.data.keyword.cloud_notm}} Private v3.1.0 클러스터에서 실행되도록 유효성 검증되었습니다.

- **LinuxONE 및 IBM Z**: z/VM 및 KVM(NFS 사용)
- **x86**: Linux 64비트(GlusterFS 사용)

## 라이센스 및 가격
{: #ibp-icp-license-pricing}
{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private은 Passport Advantage에서 다운로드할 수 있는 유료 오퍼링 및 [GitHub ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts")에서 사용할 수 있는 무료 Community Edition으로 구성되어 있습니다.

### 라이센스
{: #ibp-icp-license}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private은 자체 인프라에서 블록체인 네트워크를 실행하기 위해 필요한 컴포넌트를 제공하며 ICP 애플리케이션으로 배치됩니다. PPA에 액세스하여 [Helm 차트를 다운로드](howto/helm_install_icp.html)할 수 있습니다. {{site.data.keyword.blockchainfull_notm}}의 기술 지원도 구매에 포함됩니다.

{{site.data.keyword.blockchainfull_notm}} Platform on IBM Cloud Private Community Edition은 평가 및 실험에 적합한 무료 오퍼링이며 ICP 애플리케이션으로 배치됩니다. 프로덕션의 경우 Community Edition을 사용하지 마십시오. {{site.data.keyword.blockchainfull_notm}} Platform에서는 Community Edition에 대한 지원을 제공하지 않습니다. [GitHub 패키지 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts")에 액세스하여 Helm 차트를 다운로드할 수 있습니다.

### 가격
{: #ibp-icp-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform on ICP의 가격은 사용되는 가상 프로세서 코어(VPC)의 수에 따라 달라집니다. VPC는 가상 서버에 지정된 가상 코어 또는 파티션되지 않은 서버의 실제 프로세서 코어가 될 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 사용할 수 있는 각각의 VPC에 대한 라이센스를 받아야 합니다. <!-- A VPC is a unit of measurement by which a program can be licensed.-->

VPC의 사용량을 판별하는 방법에 대한 자세한 정보는 IBM Knowledge Center의 ["가상 프로세서 코어(VPC)" ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "가상 프로세서 코어(VPC)") 기사를 참조하십시오. [IBM License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html)을 사용하여 라이센스가 필요한 VPC의 수를 판별하기 위해 사용할 수 있는 보고서를 구성 및 작성할 수 있습니다.


## {{site.data.keyword.blockchainfull_notm}} Platform for ICP 설치
{: #ibp-icp-install}

{{site.data.keyword.cloud_notm}} Private 기반 {{site.data.keyword.blockchainfull_notm}} Platform은 로컬 ICP 클러스터에 설치할 수 있는 Helm 차트 파일로 제공됩니다. Helm 차트를 설치한 후에는 ICP 카탈로그에서 애플리케이션으로 {{site.data.keyword.blockchainfull_notm}} Platform을 찾을 수 있습니다.

- {{site.data.keyword.blockchainfull_notm}} Platform for ICP는 Passport Advantage를 통해 제공됩니다. [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html)에 액세스하려면 필요한 라이센스가 있어야 합니다. 구매 시 {{site.data.keyword.blockchainfull_notm}} Platform에 대한 기술 지원도 포함됩니다.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition은 탐색, 개발 및 테스트용입니다. 이 무료 버전의 IBM Blockchain Platform for ICP는 GitHub를 통해 액세스할 수 있습니다. **참고:** {{site.data.keyword.blockchainfull_notm}} Platform에서는 Community Edition에 대한 지원을 제공하지 않습니다.

Helm 차트 및 필요한 전제조건을 설치하는 방법에 대한 지시사항은 [{{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull_notm}} Platform 설치](howto/helm_install_icp.html)를 참조하십시오.

{{site.data.keyword.cloud_notm}} Private의 신규 사용자이며 ICP를 설치하고 배치하는 방법에 대한 정보 및 팁이 필요한 경우 [{{site.data.keyword.cloud_notm}} Private 설정](ICP_setup.html)을 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform for ICP를 설치한 후에는 각각의 네트워크 컴포넌트를 개별적으로 배치해야 합니다. 동시에 복수의 컴포넌트를 배치할 수는 없습니다. 시작하기 전에 블록체인 네트워크를 작성하거나 가입하기 위한 우수 사례에 대해 학습하려면 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 배치 안내서](ibp_for_icp_deployment_guide.html)를 방문하십시오. 그런 다음 아래의 절에서 개별 컴포넌트를 배치하고 작동시키는 단계를 검토하십시오.

### 방화벽 뒤에 {{site.data.keyword.blockchainfull_notm}} Platform for ICP 설치
{: #ibp-icp-firewall}

공용 인터넷에 액세스하지 않고 {{site.data.keyword.blockchainfull_notm}} Platform 컴포넌트를 방화벽 뒤에 배치할 수 있습니다. 다운로드된 Helm 차트 패키지에는 배치 중에 DockerHub에서 가져오지 않고 {{site.data.keyword.blockchainfull_notm}} Platform에서 사용하는 모든 Fabric 컴포넌트 Docker 이미지가 포함되어 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform Community Edition Helm 차트 패키지에는 필요한 Fabric 컴포넌트 Docker 이미지가 포함되어 있지 않습니다. 배치 중에 DockerHub에서 해당 이미지를 다운로드하도록 구성되어 있으며 공용 인터넷에 액세스할 수 없는 경우 실패합니다. 하지만 몇 가지 추가 단계를 수행하여 Community Edition 컴포넌트를 방화벽 뒤에 배치할 수 있습니다. 자세한 정보는 [방화벽 뒤에 Community Edition 설치](howto/helm_install_icp.html#helm-install-prereqs-firewall)를 참조하십시오.


## ICP의 인증 기관에 대한 정보
{: #ibp-icp-ca}

인증 기관(CA)에서 네트워크의 ID를 제공합니다. CA는 복수의 당사자 사이에서 신뢰의 앵커로 동작하는 공증인과 유사한 것으로 간주할 수 있습니다. 디지털 ID를 캡슐화하는 루트 CA에서 서명한 인증서를 네트워크의 모든 엔티티에 제공합니다. 이 인증서는 네트워크에서 수행되는 확인 오퍼레이션과 모든 서명의 신뢰 루트입니다. 인증 기관 및 블록체인 네트워크에서 인증 기관이 수행하는 역할에 대한 자세한 정보는 [블록체인 컴포넌트 개요](blockchain_component_overview.html)를 참조하십시오.

CA는 ID를 유효성 검증하고 네트워크에 배치해야 하는 다른 컴포넌트에 대한 인증서를 발행합니다. 따라서 다른 컴포넌트를 배치하기 전에 조직을 위한 CA를 배치해야 합니다.

- Helm 차트를 설치한 후 CA를 구성 및 배치하는 방법에 대한 정보는 [{{site.data.keyword.cloud_notm}} Private에 인증 기관 배치](howto/CA_deploy_icp.html)를 참조하십시오.

- CA를 사용하여 인증서를 생성하고 추가 컴포넌트를 배치하기 위한 전제조건 단계를 완료하는 방법에 대한 정보는 [{{site.data.keyword.cloud_notm}} Private에서 인증 기관 작동](howto/CA_operate.html)을 참조하십시오.

## ICP의 순서 지정자에 대한 정보
{: #ibp-icp-orderer}

순서 지정자는 블록체인 네트워크에서 클라이언트를 인증하고, 트랜잭션의 순서를 지정하고, 트랜잭션을 브로드캐스트합니다. 순서 지정자는 Hyperledger Fabric을 기반으로 하는 블록체인 네트워크의 공통 바인딩입니다. 따라서 네트워크를 작성하는 조직의 경우 다른 조직이 네트워크에서 해당 피어를 배치하고, 채널에 가입하고, 트랜잭션을 시작하기 전에 "순서 지정 서비스"(순서 지정 작업을 수행하는 노드 또는 노드의 콜렉션)를 배치하고 시작해야 합니다. 순서 지정자 및 블록체인 네트워크에서 순서 지정자가 수행하는 역할에 대한 자세한 정보는 [블록체인 컴포넌트 개요](blockchain_component_overview.html#orderer)를 참조하십시오.

블록체인 네트워크를 작성하는 경우 순서 지정자를 배치해야 합니다. 순서 지정자가 배치되면 다른 조직을 컨소시엄에 초대할 수 있으며, 이후에는 해당 조직에서 자체 채널을 작성할 수 있게 됩니다.

- Helm 차트를 설치한 후 순서 지정자를 구성 및 배치하는 방법에 대한 정보는 [ICP에 순서 지정자 배치](howto/orderer_deploy_icp.html)를 참조하십시오.

- 컨소시엄을 빌드하는 방법에 대한 정보는 [ICP에서 순서 지정자 작동](howto/orderer_operate.html)을 참조하십시오.

## ICP의 피어에 대한 정보
{: #ibp-icp-peer}

피어는 원장 및 스마트 계약을 호스팅하므로 기본적인 네트워크 요소입니다. 스마트 계약 및 원장은 네트워크에서 공유 프로세스 및 공유 정보를 각각 캡슐화하기 위해 사용됩니다. 피어 및 블록체인 네트워크에서 피어가 수행하는 역할에 대한 자세한 정보는 [블록체인 컴포넌트 개요](blockchain_component_overview.html)를 참조하십시오.

- 네트워크에 가입할 준비가 되면 채널에 가입하고, 트랜잭션을 인증하고, 채널 원장을 저장할 피어를 배치할 수 있습니다. ICP에 ICP의 다른 컴포넌트에 연결할 피어를 배치하는 방법에 대한 정보는 [ICP에 피어 배치](howto/peer_deploy_icp.html)를 참조하십시오. ICP에 스타터 또는 엔터프라이즈 플랜 네트워크에 연결할 피어를 배치하는 방법에 대한 정보는 [스타터 또는 엔터프라이즈에 연결할 피어 배치](howto/peer_deploy_ibp.html)를 참조하십시오.

- 피어를 설정한 후에는 트랜잭션을 제출하고 블록체인 네트워크에서 분산 원장을 읽어들이기 전에 몇 가지 작동 단계를 완료해야 합니다.

  - 피어를 ICP에 배치된 {{site.data.keyword.blockchainfull_notm}} Platform에 연결하는 경우 [다중 클라우드 네트워크가 포함된 {{site.data.keyword.cloud_notm}} Private에서 피어 작동](howto/peer_operate_icp.html)을 참조하십시오.
  - 피어를 {{site.data.keyword.cloud_notm}}에 배치된 스타터 플랜 또는 엔터프라이즈 플랜 네트워크에 연결하는 경우 [스타터 플랜 또는 엔터프라이즈 플랜이 포함된 {{site.data.keyword.cloud_notm}} Private에서 피어 작동](howto/peer_operate_ibp.html)을 참조하십시오.

## 보안 고려사항
{: #icp-security}

이러한 컴포넌트는 {{site.data.keyword.cloud_notm}} 외부에 배치되기 때문에 사용자가 해당 보안을 관리해야 합니다. 여기에는 키 관리 및 데이터 암호화 등의 중요한 보안 영역이 포함되어 있습니다. 컴포넌트에 대한 보안을 고려할 때 다음 주제를 검토하십시오.

### 데이터 보안
{: #icp-security-data}

{{site.data.keyword.blockchainfull_notm}} Platform 스타터 및 엔터프라이즈 플랜은 [대칭 키 암호화 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "대칭 암호화")를 기반으로 하는 전체 디스크 암호화를 사용하여 네트워크에서 사용되는 모든 데이터를 보호합니다. 피어 데이터를 보호하려면 자체 환경에서 유사한 단계를 수행해야 합니다.

LevelDB를 사용하는지 또는 CouchDB를 사용하는지 여부에 관계 없이 상태 데이터베이스의 데이터는 암호화되지 않습니다. 애플리케이션 레벨 암호화를 사용하여 상태 데이터베이스에 있는 저장 데이터를 보호할 수 있습니다.

### 데이터 상주
{: #data-residency}

데이터 상주 요구사항으로 인해 모든 블록체인 원장 데이터의 처리 및 저장이 단일 국가의 경계 내에서(또는 정의되어 있는 일부 다른 경계 내에서) 수행되어야 할 수도 있습니다. 데이터 상주를 수행하는 방법에 관한 자세한 정보는 [데이터 상주](#icp-data-residency)를 참조하십시오.

### 키 관리
{: #icp-security-key-management}

키 관리는 보안 측면에서 매우 중요한 요소입니다. 개인 키가 손상되거나 유실되는 경우 적대적인 액터가 데이터 및 기능에 액세스할 수도 있습니다. IBM은 HSM([Hardware Security Modules](../glossary.html#hsm))이라는 물리적 어플라이언스를 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜 네트워크의 개인 키를 저장합니다.

ICP에 컴포넌트를 배치하는 경우 사용자가 개인 키를 관리해야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform에서 개인 키를 생성하지만 해당 키는 Platform에 저장되지 않습니다. 키가 손상되지 않도록 안전하게 저장하는 것이 중요합니다. 컴포넌트의 개인 키는 피어 MSP의 키 저장소 폴더(컴포넌트 내에 있는 `/mnt/crypto/peer/peer/msp/keystore/` 디렉토리)에 있습니다. 피어 내에 있는 인증서에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform에서 인증서 관리](../certificates.html) 튜토리얼의 [MSP(Membership Services Provider)](../certificates.html#msp) 절을 참조하십시오.

Key Escrow를 사용하여 유실된 개인 키를 복구할 수 있습니다. 이 작업은 키 유실 전에 수행해야 합니다. 개인 키를 복구할 수 없으면 새 ID를 인증 기관에 등록하여 새 개인 키를 가져와야 합니다. 가입한 모든 채널에서 signCert를 제거하고 바꿔야 합니다.

### TLS
{: #icp-security-tls}

[전송 계층 보안 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "SSL 또는 TLS 핸드쉐이크 개요")(TLS)은 Hyperledger Fabric의 신뢰 모델에 임베드됩니다. {{site.data.keyword.blockchainfull_notm}} Platform의 모든 스타터 및 엔터프라이즈 컴포넌트는 TLS를 사용하여 서로 인증하고 통신합니다. 따라서 Platform의 네트워크 컴포넌트는 피어와의 TLS 핸드쉐이크를 완료할 수 있어야 합니다. 이로 인한 영향 중 하나는 예를 들면 클라이언트 앱에서 피어로의 방화벽에서 화이트리스트를 사용하여 패스스루를 사용으로 설정해야 한다는 것입니다.

<!--
You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](../v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.
-->

### Membership Services Provider 구성
{: #icp-security-MSP}

{{site.data.keyword.blockchainfull_notm}} Platform의 컴포넌트에서는 MSP(Membership Services Provider)를 통해 ID를 이용합니다. MSP는 CA에서 발행하는 인증서를 네트워크 및 채널 역할과 연관시킵니다. 피어에서 MSP가 작동하는 방법에 대한 자세한 정보는 [MSP(Membership Service Provider)](../certificates.html#msp)를 참조하십시오.

### 애플리케이션 보안
{: #icp-security-appl}

모든 체인코드 호출에 서명되었으므로 Fabric에서 애플리케이션 보안을 관리합니다. Fabric에는 ACL 기반 애플리케이션 레벨 검사도 포함됩니다.

## 데이터 상주
{: #icp-data-residency}

블록체인 네트워크는 처리되는 데이터의 유형을 감지하지 못하기 때문에 특정 유형의 데이터의 보안을 유지하기 위해 추가 단계를 수행해야 하는 경우도 있습니다. 데이터 상주에 대한 가장 일반적인 요구사항은 IT 시스템에서 처리 및 저장되는 모든 데이터가 특정 국가의 경계 내에서 유지되도록 강제하는 특정 국가 내의 법률과 연관되어 있습니다. 마찬가지로 정부, 의료 및 금융 서비스와 같이 고도로 규제되는 업계에 속한 일부 회사의 경우 데이터를 완전히 방화벽 뒤에 저장해야 합니다. 따라서 데이터 상주를 수행하려면 블록체인 네트워크의 모든 컴포넌트가 동일한 [채널](glossary.html#channel)의 일부여야 하며 한 국가 내에 있어야 합니다.

데이터 상주 요구사항을 처리하려면 {{site.data.keyword.blockchainfull_notm}} Platform의 기초가 되는 Hyperledger Fabric 아키텍처를 이해하는 것이 중요합니다. 이 아키텍처는 순서 지정 서비스(순서 지정자로 구성됨), 인증 기관(CA) 및 피어라는 세 가지 핵심 컴포넌트를 중심으로 집중되어 있습니다. 피어에서 순서 지정 서비스에서 순서 지정된 상태 업데이트를 블록의 양식으로 받고 해당 상태와 원장을 유지보수합니다. 따라서 피어와 순서 지정 서비스는 직접적인 관계가 있습니다. 원장에는 트랜잭션 로그에 포함된 모든 키와 데이터의 최신 값이 들어 있습니다.

또한 클라이언트 애플리케이션에서는 [Fabric SDK](v10_application.html#using-the-fabric-sdks)를 사용하여 트랜잭션을 피어와 순서 지정 서비스에 보냅니다. 이러한 트랜잭션에는 원장에 키-값 쌍을 포함하는 [읽기-쓰기 시맨틱 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "읽기-쓰기 세트 시맨틱") 데이터가 포함됩니다.

국가 내 데이터 상주가 요구사항인 경우 순서 지정자, 피어 및 클라이언트 애플리케이션이 동일한 국가 내에 상주해야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform 네트워크가 {{site.data.keyword.cloud_notm}}에 작성되면 네트워크의 위치를 선택할 수 있습니다. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->지역과 위치에 관한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform 지역 및 위치](reference/ibp_regions.html)를 참조하십시오.

### 데이터 상주에 대한 유스 케이스

네 개 조직의 컨소시엄과 함께 순서 지정자 및 CA가 포함되어 있는 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크를 고려해 보십시오. 조직에는 하나 이상의 피어 노드가 포함되어 있고, 네 개의 조직이 모두 단일 채널에 속해 있으며, 네트워크의 모든 컴포넌트가 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크가 배치된 지역(예: 프랑크프루트)에 상주합니다. 마지막으로 피어와 상호작용하는 클라이언트 애플리케이션도 독일 내에 상주합니다.

이 예제에서는 데이터 상주가 유지됩니다.

![모든 컴포넌트가 동일한 국가 내에 있는 경우 데이터 상주](images/remote_peer_data_res_1.png "모든 컴포넌트가 동일한 국가 내에 있는 경우 데이터 상주")
*그림 1. 모든 컴포넌트가 동일한 국가 내에 있는 경우 데이터 상주*

이제 **분산 피어**가 해당 조직 중 하나에 가입하는 경우 그 영향에 대해 고려해 보십시오. 분산 피어는 나머지 네트워크와 동일한 지역에 상주할 수도 있고 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크 지역 외부의 임의 위치에 상주할 수도 있습니다.

-	피어가 나머지 네트워크와 동일한 국가에 상주하는 경우 데이터 상주가 유지됩니다. 모든 원장 데이터는 상기 **그림 1**과 같이 독일 내에서 유지됩니다.
-	피어가 다른 국가(예: 미국)에 상주하는 경우에는 피어 원장의 데이터가 해당 국가의 경계 외부에서 공유되기 때문에 데이터 상주가 더 이상 유지되지 않습니다.

이 문제점을 해결하기 위해 **채널**을 사용하여 데이터를 네트워크의 피어 서브세트로 분리할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 국가 경계에 걸친 분산 피어 및 순서 지정자가 포함되어 있는 경우 채널을 통해 원장 데이터를 국가 경계 외부에 있는 피어를 사용하는 조직으로부터 격리할 수 있습니다.

**참고:** 순서 지정자는 항상 네트워크를 호스팅하도록 선택한 데이터 센터 지역 내에 있습니다. 국가 경계에 걸쳐 복수의 순서 지정자를 보유할 수는 없습니다. 하지만 피어는 데이터 센터 내에 있거나 {{site.data.keyword.cloud_notm}} 외부의 원격 위치에 있을 수 있습니다.

![피어가 IBM Blockchain Platform의 국가 외부 지역에 상주하는 경우 데이터 상주](images/remote_peer_data_res_2.png "피어가 IBM Blockchain Platform의 국가 외부 지역에 상주하는 경우 데이터 상주")
*그림 2. 피어가 IBM Blockchain Platform의 국가 외부 지역에 상주하는 경우 데이터 상주*

**그림 2**에서 `OrgC` 및 `OrgD`의 경우 데이터 상주가 필요하지 않습니다. 실제로 현재 `OrgD`에는 *미국*에 상주하는 두 개의 분산 피어(`OrgD-peer1` 및 `OrgD-peer2`)가 포함되어 있습니다. 따라서 독일에 상주하는 `OrgA`, `OrgB`와 각각의 해당 클라이언트 애플리케이션 및 피어에서 원장 데이터를 채널 `X`에 격리하기 위해 `OrgC` 및 `OrgD`를 위한 새 채널 `Y`가 작성됩니다.

{{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 데이터 플로우에 대한 자세한 정보는 [트랜잭션 플로우에 대한 Fabric 문서![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "트랜잭션 플로우")를 참조하십시오.

향후 Hyperledger Fabric의 새로운 기술을 통해 [개인용 데이터 콜렉션 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "개인용 데이터 콜렉션") 및 제로 지식 증명을 사용하여 추가적인 데이터 상주를 달성하도록 기능을 개선할 예정입니다.

- 개인용 데이터 콜렉션을 사용하면 인증된 피어(예: 국가 경계 내에 있는 피어)에만 표시되도록 개인용 데이터를 피어 투 피어로 공유할 수 있습니다(가십 프로토콜을 통해). 데이터는 피어의 개인 데이터베이스에 저장됩니다. 순서 지정 서비스는 여기에 관련되지 않으며 개인 데이터를 볼 수 없습니다. 해당 데이터의 해시가 채널에 있는 모든 피어의 원장에 기록됩니다. 상태 유효성 검증에 사용되는 해시는 트랜잭션의 증거로 사용되며 감사 용도로 사용할 수 있습니다. 개인용 데이터는 Fabric 버전 1.2.1에서 실행 중인 {{site.data.keyword.blockchainfull_notm}} Platform의 네트워크에서 사용할 수 있습니다. 하지만 ICP에서 실행 중인 피어의 경우 현재는 ICP 네트워크에서 가십을 사용하지 않기 때문에 개인용 데이터 기능을 사용할 수 없습니다.

- 제로 지식 증명(ZKP)을 사용하는 경우 “증명자”가 시크릿 자체를 노출하지 않고도 “확인자”에게 시크릿을 알고 있음을 보증할 수 있습니다.

이러한 기술에 대한 자세한 정보는 [Hyperledger Fabric을 사용한 개인용 및 기밀 트랜잭션 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Hyperledger Fabric을 사용한 개인용 및 기밀 트랜잭션")에 대한 백서에서 확인할 수 있습니다.

## 지원 받기
{: #ibp-icp-support}

디지털 지원 오퍼링에 대한 정보는 IBM Blockchain Platform 지원 [리소스 및 지원 포럼](ibmblockchain_support.html#resources)을 참조하십시오.

### {{site.data.keyword.blockchainfull_notm}} Platform for ICP

IBM Blockchain Platform for ICP 라이센스를 구매한 상태에서 고객 지원 센터에 문의하려는 경우 [IBM 지원 커뮤니티에 액세스하여 지원 티켓 열기![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "{{site.data.keyword.blockchainfull_notm}} Platform for ICP 지원 센터")에 대한 정보를 참조하십시오.

### {{site.data.keyword.blockchainfull_notm}} Platform Community Edition

Community Edition은 탐색, 개발 및 테스트용입니다. IBM Blockchain Platform에서는 {{site.data.keyword.blockchainfull_notm}} Platform Community Edition에 대한 지원을 제공하지 않습니다.

블록체인 컴포넌트와 관련된 문제가 발생하는 경우 무료 블록체인 개발자 리소스 및 지원 포럼을 사용하여 IBM 및 Fabric 커뮤니티의 지원을 받을 수 있습니다. 자세한 정보는 [블록체인 리소스 및 지원 포럼](ibmblockchain_support.html#resources)을 참조하십시오.

IBM Cloud Private과 관련된 문제의 경우 [무료 디지털 지원 및 ICP에서 제공하는 유료 지원](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us)을 둘 다 이용할 수 있습니다.

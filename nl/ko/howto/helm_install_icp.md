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

# {{site.data.keyword.cloud_notm}} Private에 {{site.data.keyword.blockchainfull_notm}} Platform 설치
{: #remote-peer-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private은 로컬 ICP 클러스터에 설치될 수 있는 Helm 차트 파일로 제공됩니다. Helm 차트를 설치하고 나면 ICP 카탈로그에서 {{site.data.keyword.blockchainfull_notm}} 플랫폼을 애플리케이션으로 찾을 수 있습니다. 

{{site.data.keyword.blockchainfull_notm}} Platform for ICP를 설치하기 전에 [고려사항 및 제한사항](../ibp-for-icp-about.html#ibp-icp-considerations)을 검토하십시오. Helm 차트, 가격 및 지원에 포함된 블록체인 컴포넌트의 배치에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 정보](../ibp-for-icp-about.html)를 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform for ICP에서는 다음 두 가지 에디션을 제공합니다. 

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private은 PPA(Passport Advantage)를 통해 제공됩니다. [Passport Advantage Online ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online")에 액세스하는 데 필요한 라이센스를 보유해야 합니다. 구매 시 {{site.data.keyword.blockchainfull_notm}} Platform을 위한 기술 지원이 포함됩니다. 

- {{site.data.keyword.blockchainfull_notm}} Platform 커뮤니티 에디션은 탐색, 개발 및 테스트를 위한 무료 오퍼링입니다. 이 무료 버전은 [GitHub ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/차트")를 통해 액세스될 수 있습니다. {{site.data.keyword.IBM_notm}}은 커뮤니티 에디션을 위한 지원을 제공하지 않습니다. 

## Helm 차트를 설치하기 위한 전제조건
{: #helm-install-prereqs}

Helm 차트를 설치하기 전에 ICP 클러스터를 구성해야 합니다. [ICP 클러스터 설정 및 구성](../ICP_setup.html)에 대한 지시사항을 검토하십시오.

## 방화벽 뒤에 {{site.data.keyword.blockchainfull_notm}} Platform 설치
{: #helm-install-prereqs-firewall}

인터넷 연결 없이 방화벽 뒤에 {{site.data.keyword.blockchainfull_notm}} Platform 컴포넌트를 배치할 수 있습니다. PPA 패키지에는 {{site.data.keyword.blockchainfull_notm}} Platform이 사용하는 모든 Fabric 컴포넌트 Docker 이미지가 포함되며, 이에 따라 배치 중에 DockerHub에서 해당 이미지를 다운로드할 필요가 없습니다. 

그러나 이 에디션이 배치 중에 DockerHub에서 해당 이미지를 다운로드하도록 구성되어 있으므로 커뮤니티 에디션 Helm 차트에는 필요한 Fabric 컴포넌트 Docker 이미지가 포함되지 않습니다. 인터넷 연결이 불가능하면 배치에 실패합니다. 그러므로 ICP 클러스터에 아카이브를 설치하기 전에 인터넷이 연결된 머신에 아카이브를 작성하기 위한 추가 단계를 완료해야 합니다. 다음 이미지가 필요합니다. 
- [Fabric 피어 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic 피어")
- [Fabric CA ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric 순서 지정자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric 순서 지정자")
- [Fabric 카우치 데이터베이스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric 카우치 데이터베이스")
- [`Init` MSP 폴더를 포함하여 컴포넌트를 부트스트랩하고 구성하는 데 사용되는 이미지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [체인코드 컨테이너를 실행하기 위해 피어에 사용되는 DinD(Docker-in-Docker) 이미지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-dind/)

이 이미지 사용에 대한 자세한 정보는 [인터넷 연결 없이 클러스터에 주요 애플리케이션 추가 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html)를 참조하십시오. Helm 차트의 `ibm-blockchain-platform-dev/ibm_cloud_pak` 디렉토리 아래에서 스펙 파일 `manifest.yaml`을 찾을 수 있습니다. 

## Helm 차트를 ICP에 가져오기

1. [Passport Advantage Online ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online")에서 IBM Blockchain Platform for ICP의 Helm 차트 파일을 다운로드하거나 [GitHub ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/차트")에서 무료 커뮤니티 에디션의 Helm 차트 파일을 다운로드하십시오. 이 Helm 차트 패키지에는 CA, 순서 지정자 및 피어를 위한 세 가지 하위 Helm이 포함됩니다. 

2. 아직 로그인하지 않은 경우 ICP 클러스터에 로그인하십시오. 

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html)가 구성되어 있는지 확인하십시오. Docker CLI를 구성한 후 다음 명령을 사용하여 클러스터의 이미지 레지스트리에 액세스하십시오. 
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. 다음 명령을 사용하여 ICP에서 저장소의 이름을 찾아 Helm 차트를 업로드하십시오. 
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  이 명령이 완료되면 클러스터에서 저장소의 목록을 볼 수 있습니다. 대상 저장소의 이름을 선택하고 이를 저장하십시오. 아래 명령에서 이 이름을 사용해야 합니다.

6. 명령행을 사용하여 Helm 차트를 가져오십시오.
  Helm 차트를 가져오기 위해 실행하는 명령은 Helm 차트가 PPA(Passport Advantage)에서 다운로드되는지 아니면 GitHub에서 다운로드되는지에 따라 달라집니다. 

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    PPA에서 Helm 차트를 다운로드한 경우 이 지시사항을 따르십시오.
    PPA에서 다운로드한 Helm 차트를 저장한 디렉토리에서 Helm 차트를 ICP 클러스터에 가져오려면 ICP CLI에서 다음 명령을 실행하십시오. 

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    다음 값을 대체하십시오. 
    - `<archive-name>`(다운로드한 `.tgz` 파일의 이름 포함)
    - `<cluster_CA_domain>:8500`(ICP 클러스터에 로그인하는 데 사용하는 도메인 포함)
    - `<repo-name>`(차트를 업로드할 Helm 저장소 포함). 저장소를 나열하려면 'cloudctl catalog repos'를 실행하십시오.

    이 명령이 완료되면 다음 정보와 비슷한 내용이 표시됩니다.
    ```
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      Processing image: ibmcom/icp-dind-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      Processing image: ibmcom/icp-dind-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-0.1.5.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```

  - **GitHub에서 다운로드한 커뮤니티 에디션**
    GitHub에서 Helm 차트를 다운로드한 경우 이 지시사항을 따르십시오.

    GitHub에서 다운로드한 Helm 차트를 저장한 디렉토리에서 Helm 차트를 ICP 클러스터에 가져오려면 ICP CLI에서 다음 명령을 실행하십시오. GitHub에서 다운로드한 Helm 차트를 가져오려면 다음 명령을 실행하십시오.
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    다음 값을 대체하십시오. 
    - `<helm_chart_from_github>`(다운로드한 .tgz 파일의 이름 포함)
    - `<repo-name>`(차트를 업로드할 Helm 저장소 포함). 저장소를 나열하려면 'cloudctl catalog repos'를 실행하십시오.

    이 명령이 완료되면 다음 정보와 비슷한 내용이 표시됩니다.
    ```
      Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

ICP 콘솔에서 **카탈로그** 단추를 클릭한 후 왼쪽 탐색 분할창에서 **블록체인**을 클릭하여 가져오기에 성공했는지 확인하십시오. 성공한 경우 **ibm-blockchain-platform-prod** 또는 **ibm-blockchain-platform-dev** 타일이 ICP 카탈로그 페이지에 표시되어야 합니다. 


## PodSecurityPolicy 요구사항

Helm 차트를 {{site.data.keyword.cloud_notm}} Private에 가져온 후 컴포넌트를 설치하기 전에 [PodSecurityPolicy ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "팟(Pod) 보안 정책")를 대상 네임스페이스에 바인드해야 합니다. 사전 정의된 PodSecurityPolicy를 선택하거나 클러스터 관리자가 사용자를 위해 사용자 정의 PodSecurityPolicy를 작성하도록 하십시오. 
- 사전 정의된 PodSecurityPolicy 이름: [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- 사용자 정의 PodSecurityPolicy 정의:
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- 사용자 정의 PodSecurityPolicy를 위한 사용자 정의 ClusterRole:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- 사용자 정의 ClusterRole을 위한 사용자 정의 ClusterRoleBinding:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## 개별 컴포넌트 배치

Helm 차트를 설치한 후 ICP 카탈로그에서 **ibm-blockchain-platform-prod** 또는 **ibm-blockchain-platform-dev** 타일을 클릭하여 이를 여십시오. 구성 페이지를 사용하여 블록체인 네트워크의 개별 컴포넌트를 배치할 수 있습니다. 블록체인 솔루션에 필요한 컴포넌트 및 컴포넌트가 배치되어야 하는 순서에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for ICP 배치 안내서](../ibp_for_icp_deployment_guide.html)를 참조하십시오.

그런 다음 개별 컴포넌트를 배치하십시오. 

- 순서 지정자를 배치하는 경우 먼저 순서 지정자의 인증 기관을 설정해야 합니다. CA가 조직의 기타 컴포넌트에서 사용되는 인증서를 생성합니다. 자세한 정보는 [ICP에서 {{site.data.keyword.blockchainfull_notm}} Platform 인증 기관 배치](CA_deploy_icp.html)를 참조하십시오. 그런 다음 네트워크의 공통 바인딩이 될 순서 지정자를 배치할 수 있습니다. 자세한 정보는 [ICP에서 {{site.data.keyword.blockchainfull_notm}} Platform 순서 지정자 배치](orderer_deploy_icp.html)를 참조하십시오. 

- 피어를 배치하는 경우 먼저 피어의 인증 기관을 설정해야 합니다. CA가 조직의 피어에서 사용되는 인증서를 생성합니다. 자세한 정보는 [ICP에서 {{site.data.keyword.blockchainfull_notm}} Platform 인증 기관 배치](CA_deploy_icp.html)를 참조하십시오. 그런 다음 네트워크에 가입할 준비가 되면 채널에 가입하고, 트랜잭션을 보증하고, 데이터를 저장하는 피어를 배치할 수 있습니다. 자세한 정보는 피어가 가입하는 블록체인 네트워크에 따라 [ICP에서 {{site.data.keyword.blockchainfull_notm}} 피어 배치](peer_deploy_icp.html) 또는 [스타터 또는 엔터프라이즈 플랜 네트워크를 위한 {{site.data.keyword.blockchainfull_notm}} 피어 배치](peer_deploy_ibp.html)를 참조하십시오. 

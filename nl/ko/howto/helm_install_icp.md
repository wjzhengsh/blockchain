---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: IBM Blockchain Platform, Helm chart file, local ICP cluster, IBM Cloud Private, upgrade Helm chart

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

# {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 설치
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private은 로컬 {{site.data.keyword.cloud_notm}} Private 클러스터에 설치될 수 있는 Helm 차트 파일로 제공됩니다. Helm 차트를 설치한 후에는 {{site.data.keyword.cloud_notm}} Private 카탈로그에서 애플리케이션으로 {{site.data.keyword.blockchainfull_notm}} Platform을 찾을 수 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private을 설치하기 전에 [고려사항 및 제한사항](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations)을 검토하십시오. Helm 차트, 가격 및 지원에 포함된 블록체인 컴포넌트의 배치에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 정보](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about)를 참조하십시오.

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private에서는 다음 두 가지 에디션을 제공합니다.

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private은 PPA(Passport Advantage)를 통해 제공됩니다. [Passport Advantage Online ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online")에 액세스하는 데 필요한 라이센스를 보유해야 합니다. 구매 시 {{site.data.keyword.blockchainfull_notm}} Platform을 위한 기술 지원이 포함됩니다.

- {{site.data.keyword.blockchainfull_notm}} Platform 커뮤니티 에디션은 탐색, 개발 및 테스트를 위한 무료 오퍼링입니다. 이 무료 버전은 [GitHub ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/차트")를 통해 액세스될 수 있습니다. {{site.data.keyword.IBM_notm}}은 커뮤니티 에디션을 위한 지원을 제공하지 않습니다.

## Helm 차트를 설치하기 위한 전제조건
{: #helm-install-prereqs}

Helm 차트를 설치하기 전에 {{site.data.keyword.cloud_notm}} Private 클러스터를 구성하고 팟(Pod) 보안 정책에 바인드되는 새 대상 네임스페이스를 작성해야 합니다. [{{site.data.keyword.cloud_notm}} Private 클러스터 설정 및 구성](/docs/services/blockchain/ICP_setup.html#icp-setup)에 대한 지시사항을 검토하십시오.

## 방화벽 뒤에 {{site.data.keyword.blockchainfull_notm}} Platform 설치
{: #helm-install-prereqs-firewall}

인터넷 연결 없이 방화벽 뒤에 {{site.data.keyword.blockchainfull_notm}} Platform 컴포넌트를 배치할 수 있습니다. PPA 패키지에는 {{site.data.keyword.blockchainfull_notm}} Platform이 사용하는 모든 Fabric 컴포넌트 Docker 이미지가 포함되며, 이에 따라 배치 중에 DockerHub에서 해당 이미지를 다운로드할 필요가 없습니다.

그러나 이 에디션이 배치 중에 DockerHub에서 해당 이미지를 다운로드하도록 구성되어 있으므로 커뮤니티 에디션 Helm 차트에는 필요한 Fabric 컴포넌트 Docker 이미지가 포함되지 않습니다. 인터넷 연결이 불가능하면 배치에 실패합니다. 그러므로 {{site.data.keyword.cloud_notm}} Private 클러스터에 아카이브를 설치하기 전에 인터넷에 연결된 시스템에 아카이브를 작성하기 위한 추가 단계를 완료해야 합니다. 다음 이미지가 필요합니다.
- [Fabric 피어 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic 피어")
- [Fabric CA ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric 순서 지정자 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric 순서 지정자")
- [Fabric 카우치 데이터베이스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric 카우치 데이터베이스")
- [`Init` MSP 폴더를 포함하여 컴포넌트를 부트스트랩하고 구성하는 데 사용되는 이미지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [체인코드 컨테이너를 실행하기 위해 피어에 사용되는 DinD(Docker-in-Docker) 이미지 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hub.docker.com/r/ibmcom/ibp-dind/)

이 이미지 사용에 대한 자세한 정보는 [인터넷 연결 없이 클러스터에 주요 애플리케이션 추가 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html)를 참조하십시오. Helm 차트의 `ibm-blockchain-platform-dev/ibm_cloud_pak` 디렉토리 아래에서 스펙 파일 `manifest.yaml`을 찾을 수 있습니다.

## Helm 차트를 {{site.data.keyword.cloud_notm}} Private에 가져오기
{: #helm-install-importing}

1. [Passport Advantage Online ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online")에서 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private의 Helm 차트 파일을 다운로드하거나 [GitHub ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/차트")에서 무료 커뮤니티 에디션의 Helm 차트 파일을 다운로드하십시오. 이 Helm 차트 패키지에는 CA, 순서 지정자 및 피어를 위한 세 가지 하위 Helm이 포함됩니다.

2. 아직 로그인하지 않은 경우 {{site.data.keyword.cloud_notm}} Private 클러스터에 로그인하십시오.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html)가 구성되어 있는지 확인하십시오. Docker CLI를 구성한 후 다음 명령을 사용하여 클러스터의 이미지 레지스트리에 액세스하십시오.
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. 다음 명령을 사용하여 {{site.data.keyword.cloud_notm}} Private에서 저장소의 이름을 찾아 Helm 차트를 업로드하십시오.
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  이 명령이 완료되면 클러스터에서 저장소의 목록을 볼 수 있습니다. 대상 저장소의 이름을 선택하고 이를 저장하십시오. 아래 명령에서 이 이름을 사용해야 합니다.

5. 명령행을 사용하여 Helm 차트를 가져오십시오.
  Helm 차트를 가져오기 위해 실행하는 명령은 Helm 차트가 PPA(Passport Advantage)에서 다운로드되는지 아니면 GitHub에서 다운로드되는지에 따라 달라집니다.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    PPA에서 Helm 차트를 다운로드한 경우 이 지시사항을 따르십시오.
    PPA에서 다운로드한 Helm 차트를 저장한 디렉토리에서 Helm 차트를 {{site.data.keyword.cloud_notm}} Private 클러스터에 가져오려면 {{site.data.keyword.cloud_notm}} Private CLI에서 다음 명령을 실행하십시오.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    다음 값을 대체하십시오.
    - `<archive-name>`(다운로드한 `.tgz` 파일의 이름 포함)
    - `<cluster_CA_domain>:8500`({{site.data.keyword.cloud_notm}} Private 클러스터에 로그인하는 데 사용하는 도메인 포함)
    - `<repo-name>`(차트를 업로드할 Helm 저장소 포함). 저장소를 나열하려면 'cloudctl catalog repos'를 실행하십시오.

    이 명령이 완료되면 다음 정보와 비슷한 내용이 표시됩니다.

    ```  
Expanding archive
    OK

    Importing docker images
      Processing image: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
      Processing image: ibmcom/ibp-couchdb-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
      Processing image: ibmcom/ibp-couchdb-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
      Processing image: ibmcom/ibp-dind-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
      Processing image: ibmcom/ibp-dind-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-1.0.2.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```  
    </details>

  - **GitHub에서 다운로드한 커뮤니티 에디션**
    GitHub에서 Helm 차트를 다운로드한 경우 이 지시사항을 따르십시오.

    GitHub에서 다운로드한 Helm 차트를 저장한 디렉토리에서 Helm 차트를 {{site.data.keyword.cloud_notm}} Private 클러스터에 가져오려면 {{site.data.keyword.cloud_notm}} Private CLI에서 다음 명령을 실행하십시오. GitHub에서 다운로드한 Helm 차트를 가져오려면 다음 명령을 실행하십시오.
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

{{site.data.keyword.cloud_notm}} Private 콘솔에서 **카탈로그** 단추를 클릭한 후 왼쪽 탐색 분할창에서 **블록체인**을 클릭하여 가져오기에 성공했는지 확인하십시오. 성공한 경우 **ibm-blockchain-platform-prod** 또는 **ibm-blockchain-platform-dev** 타일이 {{site.data.keyword.cloud_notm}} Private 카탈로그 페이지에 표시되어야 합니다.

## 개별 컴포넌트 배치
{: #helm-install-deploying-components}

Helm 차트를 설치한 후 {{site.data.keyword.cloud_notm}} Private 카탈로그에서 **ibm-blockchain-platform-prod** 또는 **ibm-blockchain-platform-dev** 타일을 클릭하여 이를 여십시오. 구성 페이지를 사용하여 블록체인 네트워크의 개별 컴포넌트를 배치할 수 있습니다. 블록체인 솔루션에 필요한 컴포넌트 및 컴포넌트가 배치되어야 하는 순서에 대한 자세한 정보는 [{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private 시작하기](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp)를 참조하십시오.

그런 다음 개별 컴포넌트를 배치하십시오.

- 순서 지정자를 배치하는 경우 먼저 순서 지정자의 인증 기관을 설정해야 합니다. CA가 조직의 기타 컴포넌트에서 사용되는 인증서를 생성합니다. 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private에서 {{site.data.keyword.blockchainfull_notm}} Platform 인증 기관 배치](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)를 참조하십시오. 그런 다음 네트워크의 공통 바인딩이 될 순서 지정자를 배치할 수 있습니다. 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private에서 {{site.data.keyword.blockchainfull_notm}} Platform 순서 지정자 배치](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)를 참조하십시오.

- 피어를 배치하는 경우 먼저 피어의 인증 기관을 설정해야 합니다. CA가 조직의 피어에서 사용되는 인증서를 생성합니다. 자세한 정보는 [{{site.data.keyword.cloud_notm}} Private에서 {{site.data.keyword.blockchainfull_notm}} Platform 인증 기관 배치](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy)를 참조하십시오. 그런 다음 네트워크에 가입할 준비가 되면 채널에 가입하고, 트랜잭션을 보증하고, 데이터를 저장하는 피어를 배치할 수 있습니다. 자세한 정보는 피어가 가입하는 블록체인 네트워크에 따라 [{{site.data.keyword.cloud_notm}} Private에서 {{site.data.keyword.blockchainfull_notm}} 피어 배치](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) 또는 [스타터 또는 엔터프라이즈 플랜 네트워크를 위한 {{site.data.keyword.blockchainfull_notm}} 피어 배치](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)를 참조하십시오.

## {{site.data.keyword.cloud_notm}} Private의 Helm 차트 업그레이드
{: #helm-install-upgrading}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.1을 사용하는 경우 {{site.data.keyword.cloud_notm}} Private 문서의 [번들화된 제품 업그레이드 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "번들화된 제품 업그레이드")에 있는 지시사항을 따라 Helm 차트를 {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2로 업그레이드할 수 있습니다. 지시사항에 있는 `helm upgrade` 명령 외에도 [{{site.data.keyword.cloud_notm}} Private 콘솔을 사용하여 Helm 릴리스를 업그레이드](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui)할 수도 있습니다.

Helm 차트를 업그레이드하려면 {{site.data.keyword.cloud_notm}} Private에서 사용자의 역할은 **클러스터 관리자** 또는 **팀 관리자**여야 합니다.
{:note}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2에서 v1.0.1으로의 롤백은 지원되지 않습니다.

### {{site.data.keyword.cloud_notm}} Private 콘솔에서 Helm 릴리스 업그레이드
{: #helm-install-upgrading-ui}

{{site.data.keyword.cloud_notm}} Private 콘솔에서 컴포넌트의 Helm 릴리스를 업그레이드하는 경우 `Reuse values`를 확인하고 어떠한 매개변수도 변경하지 않아야 합니다.
{:important}  

{{site.data.keyword.cloud_notm}} Private 콘솔에서 Helm 릴리스를 업그레이드하려면 다음 단계를 완료하십시오.
1. 메뉴 표시줄에서 **워크로드** > **Helm 릴리스**를 클릭하십시오.
2. 업그레이드할 Helm 릴리스를 선택하십시오.
3. **Details and Upgrades** 섹션의 `Available Version` 아래의 버전 번호를 확인하십시오.
4. 주요 변경사항을 보려면 버전 번호 아래의 **ReadMe**를 클릭하여 릴리스 정보를 검토하십시오.
5. **업그레이드**를 클릭한 후 드롭 다운 목록에서 올바른 저장소 및 버전을 선택하십시오.
6. `Reuse values`가 선택되었는지 확인하십시오.
7. **업그레이드**를 클릭하십시오.

콘솔의 왼쪽 상단 모서리에 있는 릴리스 이름 옆에 릴리스 상태에 대한 확인 메시지 및 업데이트가 표시될 때까지 기다리십시오.

### 피어 업그레이드를 위한 추가 단계 완료
{: #helm-install-upgrading-peer}

피어를 업그레이드한 후 업그레이드 프로세스를 완료하는 데 수행해야 할 일부 추가 단계가 있습니다. 피어 Helm 릴리스 페이지에서 **팟(Pod)** 섹션 아래에서 두 개의 피어 팟(Pod)을 볼 수 있습니다.
- 상태가 `CrashLoopBackOff`인 새 피어 팟(Pod)
- 상태가 `Running`인 원래의 피어 팟(Pod)

연관된 피어 이름을 기록해 두십시오. 아래 단계에서 해당 이름을 사용해야 합니다.
{:tip}

피어 업그레이드 프로세스를 완료하려면 CLI에서 다음 단계를 완료하십시오.
1. {{site.data.keyword.cloud_notm}} Private CLI에서 클러스터에 로그인하고 `kubectl get replicaset` 명령을 실행하십시오.
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. 원래 피어에 해당하는 피어 복제 세트를 찾으십시오. 피어 복제 세트의 이름은 {{site.data.keyword.cloud_notm}} Private 콘솔의 **팟(Pod)** 섹션에 있는 피어 이름의 시작 부분과 일치해야 합니다.
3. 피어 복제 세트를 삭제하십시오.
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. 새 피어 팟(Pod)을 삭제하십시오. 이는 UI에서 상태가 `CrashLoopBackOff`인 피어 팟(Pod)입니다.
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

새 피어 팟(Pod)이 `Running`의 상태로 작성되었음이 표시되면 피어가 업그레이드된 것입니다.

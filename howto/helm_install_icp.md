---

copyright:
  years: 2017, 2019
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
{:pre: .pre}

# Installing {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private is delivered as a Helm chart file that can be installed on a local {{site.data.keyword.cloud_notm}} Private cluster. After you install the Helm chart, you can find {{site.data.keyword.blockchainfull_notm}} Platform as an application in the {{site.data.keyword.cloud_notm}} Private Catalog.

Before you install {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, review the [Considerations and limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations). For more information about how to deploy the blockchain components that are included in the Helm chart, pricing, and support, see [About {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private delivers two editions:

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private is delivered through Passport Advantage (PPA). You need to have the required license to access [Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Upon your purchase, technical support for {{site.data.keyword.blockchainfull_notm}} Platform is included.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition is a free offering for exploration, development, and testing. This free version can be accessed through [GitHub ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts"). Note that {{site.data.keyword.IBM_notm}} does not provide support for the Community Edition.

## Prerequisites for installing the Helm chart
{: #helm-install-prereqs}

Before you install the Helm chart, you must have configured an {{site.data.keyword.cloud_notm}} Private cluster. Review the instructions for [setting up and configuring an {{site.data.keyword.cloud_notm}} Private cluster](/docs/services/blockchain/ICP_setup.html#icp-setup).

## Installing {{site.data.keyword.blockchainfull_notm}} Platform behind a firewall
{: #helm-install-prereqs-firewall}

You can deploy {{site.data.keyword.blockchainfull_notm}} Platform components behind a firewall without Internet connectivity. The PPA package includes all Fabric component Docker images that {{site.data.keyword.blockchainfull_notm}} Platform will use and you don't need to download them from DockerHub during deployment.

However, the Community Edition Helm chart does not include the necessary Fabric component Docker images because this edition is configured to download those images from DockerHub during deployment. The deployment will fail if no Internet connectivity is available. Therefore, you need to complete extra steps to create archives on an Internet-connected machine before you can install the archives on your the {{site.data.keyword.cloud_notm}} Private cluster. The following images are required:
- [Fabric peer ![External link icon](../images/external_link.svg "External link icon")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic peer")
- [Fabric CA ![External link icon](../images/external_link.svg "External link icon")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric orderer ![External link icon](../images/external_link.svg "External link icon")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric Couch database ![External link icon](../images/external_link.svg "External link icon")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init` image used to bootstrap and configure components, including the MSP folders ![External link icon](../images/external_link.svg "External link icon")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Docker-in-Docker (DinD) image used by peers to run chaincode containers ![External link icon](../images/external_link.svg "External link icon")](https://hub.docker.com/r/ibmcom/ibp-dind/)

For more information about how to use these images, see [Adding featured applications to clusters without Internet connectivity ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html). Note that you can find the specification file `manifest.yaml` under the `ibm-blockchain-platform-dev/ibm_cloud_pak` directory in the Helm chart.

## Importing the Helm chart to {{site.data.keyword.cloud_notm}} Private

1. Download the Helm chart file of IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private from [Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") or for the free Community edition from [GitHub ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts").  This Helm chart package contains three sub Helm charts for the CA, orderer, and peer.

2. If you haven't already, log in to your {{site.data.keyword.cloud_notm}} Private cluster.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. Ensure that the [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html) is configured. After you configure the Docker CLI, access the image registry on your cluster by using the following command:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. Find the name of the repository in {{site.data.keyword.cloud_notm}} Private to upload your Helm chart by using the following command:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  When this command completes successfully, you can see a list of repo's in your cluster. Choose the name of the target repo and save it. You need to use it in the command below.

6. Import Helm chart by using the command line.
  The command you run to import the Helm chart depends on whether the Helm chart was downloaded from Passport Advantage (PPA) or from GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Follow these instructions if you downloaded the Helm chart from PPA.
    From the directory where you stored the downloaded Helm chart package from PPA, run the following command in the {{site.data.keyword.cloud_notm}} Private CLI to import the Helm chart into your {{site.data.keyword.cloud_notm}} Private cluster.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Replace the following values:
    - `<archive-name>` with the name of the downloaded `.tgz` file.
    - `<cluster_CA_domain>:8500` with domain that you use to log in to your {{site.data.keyword.cloud_notm}} Private cluster.
    - `<repo-name>` with the helm repository where you want to upload the chart. Run 'cloudctl catalog repos' to list your repositories.

    When this command completes successfully, you can see something that is similar to the following information:

    <details aria-label="Details"><summary>Helm install output</summary>
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

  - **Community edition downloaded from GitHub**
    Follow these instructions if you downloaded the Helm chart from GitHub.

    From the directory where you stored the downloaded Helm chart package from GitHub, run the following command in the {{site.data.keyword.cloud_notm}} Private CLI to import the Helm chart into your {{site.data.keyword.cloud_notm}} Private cluster. To import the Helm chart downloaded from GitHub, run the following command:
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Replace the following values:
    - `<helm_chart_from_github>` with the name of the .tgz file you downloaded.
    - `<repo-name>` with the helm repository where you want to upload the chart. Run 'cloudctl catalog repos' to list your repositories.

    When this command completes successfully, you can see something that is similar to the following information:
    ```
    Loading helm chart
    Loaded helm chart

    Synch charts
    Synch started
    OK
    ```

Click the **Catalog** button in the {{site.data.keyword.cloud_notm}} Private console, and then click **Blockchain** in the left navigation panel to verify the import was successful. If yes, the **ibm-blockchain-platform-prod** or **ibm-blockchain-platform-dev** tile should be visible on the {{site.data.keyword.cloud_notm}} Private Catalog page.


## PodSecurityPolicy Requirements

After importing the Helm chart to {{site.data.keyword.cloud_notm}} Private, you need to bind a [PodSecurityPolicy ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Pod Security Policies") to the target namespace prior to installing the components.  Choose either a predefined PodSecurityPolicy or have your cluster administrator create a custom PodSecurityPolicy for you:
- Predefined PodSecurityPolicy name: [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- Custom PodSecurityPolicy definition:
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
- Custom ClusterRole for the custom PodSecurityPolicy:
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

- Custom ClusterRoleBinding for the custom ClusterRole:
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

## Deploying individual components

After you install the Helm chart, click the **ibm-blockchain-platform-prod** or **ibm-blockchain-platform-dev** tile in your {{site.data.keyword.cloud_notm}} Private catalog to open it. You can use the configuration page to deploy any of the individual components of your blockchain network. For more details on the components that are required for your blockchain solution and the order in which they must be deployed, see [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp).

Then deploy the individual components:

- If you are deploying an orderer, you first need to setup a Certificate Authority for the orderer. The CA will generate certificates that will be used by other components in your organization. For more information, see [Deploying an {{site.data.keyword.blockchainfull_notm}} Platform Certificate Authority in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Then you can deploy the orderer that will be the common binding of the network. For more information, see [Deploying an {{site.data.keyword.blockchainfull_notm}} Platform orderer in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)

- If you are deploying a peer, you first need to setup a Certificate Authority for the peer. The CA will generate certificates that will be used by the peer. For more information, see [Deploying an {{site.data.keyword.blockchainfull_notm}} Platform Certificate Authority in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Then, when you are ready to join a network, you can deploy the peers that will join channels, endorse transactions, and store your data. For more information, see [Deploying an {{site.data.keyword.blockchainfull_notm}} Peer in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) or [Deploying an {{site.data.keyword.blockchainfull_notm}} Peer for a Starter or Enterprise Plan network](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy), depending on which blockchain network the peer will join.

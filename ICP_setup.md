---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: IBM Cloud Private, data storage CA, cluster ICP, configuration

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

# Setting up {{site.data.keyword.cloud_notm}} Private
{: #icp-setup}

Before you deploy {{site.data.keyword.blockchainfull}} Platform components and build blockchain network on {{site.data.keyword.cloud_notm}} Private, you need to set up {{site.data.keyword.cloud_notm}} Private in your own environment.
{:shortdesc}

## Prerequisites
{: #icp-setup-prerequisites}

Complete the following prerequisites and prepare your environment to install {{site.data.keyword.cloud_notm}} Private.

### Docker
{{site.data.keyword.cloud_notm}} Private requires Docker to be installed. Follow the related instructions in [Installing {{site.data.keyword.cloud_notm}} Private ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/install.html "Installing {{site.data.keyword.cloud_notm}} Private") to install Docker.

### {{site.data.keyword.cloud_notm}} Private settings
Before you install {{site.data.keyword.cloud_notm}} Private, the following tips are useful to prepare your nodes for {{site.data.keyword.cloud_notm}} Private installation. Additional {{site.data.keyword.cloud_notm}} Private prerequisites can be found in the [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/prep.html "Preparing your cluster for installation").

#### Update `vm.max_map_count` setting
{{site.data.keyword.cloud_notm}} Private uses Elastic Search for logging and metering. To avoid out-of-memory exceptions, Elastic Search requires the `vm.max_map_count` system property to be configured. Before you install {{site.data.keyword.cloud_notm}} Private, see the [Elastic Search configuration instructions ![External link icon](images/external_link.svg "External link icon")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Virtual memory") to configure this property on each node. You can use the following commands to set this property permanently:

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Configure the `/etc/hosts` file on each node in your cluster

- {{site.data.keyword.cloud_notm}} Private uses [Kubernetes ![External link icon](images/external_link.svg "External link icon")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Learn Kubernetes Basics") to manage containerized applications. The Kubernetes Domain Name Server (DNS) fails if host names are not configured in the `/etc/hosts` file on each node. [Insert the IP address, hostname, and shortname of each node in your cluster ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/prep_cluster.html "Configuring your cluster") into the `/etc/hosts` file on each node.

- [IPv6 is not supported by {{site.data.keyword.cloud_notm}} Private ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/getting_started/known_issues.html#ipv6 "IPv6 is not supported"). To avoid problems with the DNS service in an {{site.data.keyword.cloud_notm}} Private cluster, disable the IPv6 settings in the `/etc/hosts` file on each node by commenting out the following line with a `#` sign at the beginning of the line:
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Resources required
{: #icp-setup-resources}

Ensure that your {{site.data.keyword.cloud_notm}} Private system meets the minimum hardware resource requirements for each Fabric runtime component:

| Component | vCPU | RAM | Disk for data storage |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |
| Orderer | 2 | 512 MB | 100 GB with the ability to expand |
| Peer | 2 | 2 GB | 50 GB with the ability to expand |
| CouchDB for Peer<br>(Applicable only if you use CouchDB) | 2| 2 GB | 50 GB with the ability to expand |

 **Notes:**
 - A vCPU is a virtual core that is assigned to a virtual machine or a physical processor core if the server is not partitioned for virtual machines. You need to consider vCPU requirements when you decide the virtual processor core (VPC) for your deployment in {{site.data.keyword.cloud_notm}} Private. VPC is a unit of measurement to determine the licensing cost of {{site.data.keyword.IBM_notm}} products. For more information about scenarios to decide VPC, see [Virtual processor core (VPC) ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - These minimum resource levels are sufficient for testing and experimentation. For an environment with a large volume of transactions, it is important to allocate a sufficiently large amount of storage; 250 GB for your peer and 500 GB for your ordering service as an example. The amount of storage to use will depend on the number of transactions and the number of signatures that are required from your network. If you are about to exhaust the storage on your peer or orderer, you must deploy a new peer or orderer with a larger file system and let it sync via your other components on the same channels.

#### Storage considerations
{: #icp-setup-storage-considerations}

* You need to determine the storage that your components will use. If you use the default settings, the Peer Helm chart creates a new Persistent Volume claim with the name of `my-data-pvc` for your peer data. If you select CouchDB as your ledger database, the Helm chart creates another Persistent Volume claim with the name of `statedb-pvc` for your ledger database.
* If you do not want to use the default storage settings, ensure that a *new* storageClass is set up during the {{site.data.keyword.cloud_notm}} Private installation or the Kubernetes system administrator needs to create a storageClass before you deploy.
* [Dynamic provisioning ![External link icon](images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamic Volume Provisioning") is available only for amd64 nodes in {{site.data.keyword.cloud_notm}} Private. Therefore, if your cluster includes a mix of s390x and amd64 worker nodes, dynamic provisioning cannot be used.
* If dynamic provisioning is not used, [Persistent Volumes ![External link icon](images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistent Volumes") must be created and set up with labels that can be used to refine the Kubernetes Persistent Volume Claim (PVC) bind process.
* If you use NFS v2/v3 Persistent Volumes, you must enable the **NFS status monitor for NFSv2/v3 Filesystem Locks** module, which is also known as **rpc-statd**, on the host system where the NFS file system exists. This module allows your NFS file system to check for exclusive locks on files that other processes hold. Run the following commands to enable this module:

  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## Installing {{site.data.keyword.cloud_notm}} Private
{: #icp-setup-install}

Complete the following steps to install and set up {{site.data.keyword.cloud_notm}} Private in your environment.

1. Install an [{{site.data.keyword.cloud_notm}} Private ![External link icon](images/external_link.svg "External link icon") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/kc_welcome_containers.html) cluster at version 3.1.2. If you want to use the Helm chart for development, test, or experimentation, you can install the [{{site.data.keyword.cloud_notm}} Private Community Edition version 3.1.2 ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE version 3.1.2") for free.

2. Install the {{site.data.keyword.cloud_notm}} Private CLI [3.1.2 ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/manage_cluster/install_cli.html) to install and operate the CA.

3. Setup the pod security policy for the target namespace. Instructions are provided in the [next section](#icp-setup-psp).

After you install {{site.data.keyword.cloud_notm}} Private and bind a pod security policy to a target namespace, you can continue to [import the {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Helm chart](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) into your {{site.data.keyword.cloud_notm}} Private cluster.

## PodSecurityPolicy Requirements
{: #icp-setup-psp}

Before you can deploy any components using the Helm chart, you must create a new target namespace and bind a [PodSecurityPolicy ![External link icon](images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Pod Security Policies") to it.  Choose either a predefined PodSecurityPolicy or have your cluster administrator create a custom PodSecurityPolicy for you:
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

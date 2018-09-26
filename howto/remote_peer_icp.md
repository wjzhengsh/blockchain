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

# Deploying remote peers in {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-icp}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


The following instructions describe how to install an {{site.data.keyword.blockchainfull}} Platform remote peer on {{site.data.keyword.cloud_notm}} Private (ICP) and connect it to a network on {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

{{site.data.keyword.cloud_notm}} Private is an application platform for developing and managing on-premises containerized applications. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on {{site.data.keyword.cloud_notm}} Private is an {{site.data.keyword.blockchainfull_notm}} product that is bundled with ICP for customers to deploy peers in their local environment. After you install the Helm Chart of the remote peer, you can find it as an application in the ICP catalog. The remote peer for ICP leverages the storage, security, logging, and support services of ICP for peer management. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").

Before you install remote peers on ICP, review the [remote peer considerations](remote_peer.html#remote-peer-limitations).

## Prerequisites
{: #prerequisites-icp}

Before you install remote peers on ICP, you must complete the following steps on {{site.data.keyword.cloud_notm}} Private, your local system, and {{site.data.keyword.blockchainfull_notm}} Platform.

1. Your local system

  You need to install the {{site.data.keyword.cloud_notm}} Private CLI [2.1.0.3 ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "Installing the {{site.data.keyword.cloud_notm}} Private CLI").  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} Private

  You need an [{{site.data.keyword.cloud_notm}} Private ![External link icon](../images/external_link.svg "External link icon")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v2.1.0.3 documentation") cluster version 2.1.0.3. If you are using the remote peer for development, test, or experimentation, you can install the [{{site.data.keyword.cloud_notm}} Private Community Edition ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "Installing {{site.data.keyword.cloud_notm}}e Private-CE") for free.

  **Notes**:
    - You need to determine the storage that your peer will use. If you use the default settings, the Helm Chart creates a new 8 Gi Persistent Volume claim with the name of `my-data-pvc` for your peer data. If you select CouchDB as your ledger database, the Helm Chart creates another 8 Gi Persistent Volume claim with the name of `statedb-pvc` for your ledger database.
    - If you do not want to use the default storage settings, ensure that a *new* storageClass is set up during the ICP installation or the Kubernetes system administrator needs to create a storageClass before you deploy.
    - [Dynamic provisioning ![External link icon](../images/external_link.svg "External link icon")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamic Volume Provisioning") is only available for amd64 nodes in ICP. Therefore, if your cluster includes a mix of s390x and amd64 worker nodes, dynamic provisioning cannot be used.
    - If you are not using dynamic provisioning, [Persistent Volumes ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistent Volumes") must be created and setup with labels that can be used to refine the kubernetes Persistent Volume Claim (PVC) bind process.

  In addition to your ICP hardware requirements, the remote peer requires at least:
    - 2x vCPU
    - 2 GB RAM
    - 4 GB space for chaincode
    - 10 GB space for the ledger with ability to grow as the ledger expands

3. {{site.data.keyword.blockchainfull_notm}} Platform  

  To use a remote peer, you must have an organization that is a member of a blockchain network that is hosted on {{site.data.keyword.blockchainfull_notm}} Platform. You need to use the Network Monitor on {{site.data.keyword.cloud_notm}} to access network credentials and API endpoints of your network. If you are not a member of any blockchain network, you need to create or join a network. For more information, see [Creating a network](../get_start.html#creating-a-network) or [Joining a network](../get_start.html#joining-a-network).

## Import the Helm Chart to ICP

The {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer package is available in [Github ![External link icon](../images/external_link.svg "External link icon")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer"). The Helm Chart is imported into the ICP console by using the command line. From your local machine, complete the following steps to import the Helm Chart:

1. Download the `.tgz` Helm Chart file from [Github ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "IBM Blockchain Remote Peer Helm Chart").<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. If you are not already logged in, log in to your ICP cluster from the ICP CLI and then log in to the Docker private image registry.  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  In the code, `<cluster_CA_domain>` is the certificate authority (CA) domain. If you did not specify a CA domain, the default
value is `mycluster.icp`.

3. From the directory where you stored the downloaded Helm Chart, run the following command in the ICP CLI to import the Helm Chart into your ICP cluster. Replace `<helm_chart_from_github>` with the name of the `.tgz` file you downloaded from GitHub.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  When this command completes successfully, you can see something similar to the following information:

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  The {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Helm Chart tile should now be visible on the ICP Catalog page. Click the **Catalog** button in the ICP console to verify the import was successful.

### Deploy the remote peer

The following steps describe the process that is required to deploy the remote peer in ICP. Before you configure the remote peer in ICP, retrieve some endpoint information about your {{site.data.keyword.blockchainfull_notm}} Platform and create a user identity for the remote peer. You need to use this information in later steps.

## Step One: Retrieve your remote peer configuration information
{: #network-endpoints}

You need to provide the API endpoints of your network to your remote peer during configuration. These endpoints allow a remote peer to find and connect to the network on {{site.data.keyword.blockchainfull_notm}} Platform. On the **Overview** screen of your Network Monitor, click the **Remote Peer Configuration** button.

![Remote Peer Configuration](../images/myresources_starter.png "Remote Peer Configuration")
*Figure 1. Remote Peer Configuration*

A pop-up window opens and displays the values of the following fields. Save these values for when you configure the remote peer.

  - **Network ID**
  - **Organization MSP**
  - **Certificate Authority (CA) Name**
  - **Certificate Authority (CA) URL**
  - **Certificate Authority (CA) TLS Certificate**

You can copy and paste the fields individually, or save them as a JSON file by clicking the **Download** link. **Note** that if you download the information in JSON, you need to convert the TLS certificate into PEM format before you provide it to the remote peer. You can use the CLI to convert the certificate into PEM format by issuing the `echo -e "<CERT>" > catls.pem` command.

## Step Two: Register a remote peer
{: #register-peer}

You need to add a new peer identity to your organization on the {{site.data.keyword.blockchainfull_notm}} Platform before the remote peer can join the network. Complete the following steps to enroll and register a remote peer.

1. Log in to your {{site.data.keyword.blockchainfull_notm}} Platform and access your Network Monitor. On the "Certificate Authority" screen, you can view all the identities that have been registered with your organization, such as your admin or client applications.
  ![CA screen](../images/CA_screen_starter.png "CA screen")
  *Figure 2. CA screen*

2. Click the **Add User** button at the top of the screen. A pop-up screen opens and allows you to register your remote peer after you fill out the following fields. Save the values of the ID and Secret and you need to use them when you configure your peer.
  - **ID:** The user name of your peer, which is referred to as your `enroll ID` when you configure your peer. **Save this value** for future usage when you deploy the remote peer.
  - **Secret:** The password of your peer, which is referred to as your `enroll Secret` when you configure your peer. **Save this Value** for future usage when you deploy the remote peer.
  - **Type:** Select `peer` for this field.
  - **Affiliation:** This is the affiliation under your organization, `org1` for example, that your remote peer will belong to. Select an existing affiliation from the drop-down list or type in a new one.
  - **Maximum Enrollments:** You can use this field to limit the number of times you can enroll or generate certificates by using this identity. If not specified, the value defaults to unlimited enrollments.

  After you complete the fields, click **Submit** to register the remote peer. The registered peer is then listed in the table as an identity in your organization.

## Step Three: Configure and install a remote peer

After you register the peer identity in the Network Monitor, you can configure and install your remote peer in ICP by using the ICP console instructions below.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. Log in to the ICP console and click the **Catalog** link in the upper right corner.
2. Locate the Helm Chart for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer and click it to open it. You can see a readme file that includes information about installing and configuring the Helm Chart.
3. Click **Configure**. On the Configuration page, complete the configuration settings for the Helm Chart and confirm that you read and agree to the license agreement.
4. Click **Install**.

### Configuration parameters
{: #icp-configuration-parms}

You need to provide the information that your remote peer needs to connect to the network on {{site.data.keyword.blockchainfull_notm}} Platform.
The following table lists the configurable parameters of the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer chart and their default values.


|  Parameter     | Description    | Default  | Required |
| --------------|-----------------|-------|------- |
|**Configuration** | **Configure Release name and Target namespace for your remote peer** |  ||
| `Release name`| The name of your helm release. | none | yes |
| `Target namespace`| The Kubernetes namespace. | default | yes |
| | | | |
|**Cluster configuration** |**Cluster configuration information** | ||
| `Worker node architecture`| Select your cloud platform architecture (amd64 or S390x).| Autodetected architecture based on your master node | yes |
| `Image`| Path to the Helm Chart. | Autofilled to the installed path, do not change this value | yes |
| | | | |
|**Blockchain network** | **The network configuration information that is required for the remote peer**| | |
| `Network ID`| Value of the network id found in your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. Click the `Remote Peer Configuration` button on the "Overview" screen, then copy and paste that information here.| none | yes |
| `Organization MSP`| Value of the organization MSP id found in your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. Click the `Remote Peer Configuration` button on the "Overview" screen, then copy and paste that information here.|none|yes|
| `Certificate Authority (CA) Name`| Value of the CA Name found in your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. Click the `Remote Peer Configuration` button on the "Overview" screen and  copy and paste that information here.|none|yes|
| `Certificate Authority (CA) URL`| Value of the CA URL found in your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. Click the `Remote Peer Configuration` button on the "Overview" screen and  copy and paste that information here. | none | yes |
| `Certificate Authority (CA) TLS Certificate` | CA TLS certificate string in your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. Click the `Remote Peer Configuration` button on the "Overview" screen and  copy and paste that information here. | none | yes |
| | | | |
|**Remote peer identity** | **The enroll id and secret that are used to register your remote peer**| | |
| `Peer enroll ID`| This in the Enroll ID that you entered on the "Certificate Authority" screen of your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. | none | yes |
| `Peer enroll secret`| This in the Enroll Secret that you entered on the "Certificate Authority" screen of your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor.| none | yes |
| | | | |
|**Remote peer database** | **Ledger database type**| | |
| `Ledger database`| The [state database](../glossary.html#state-database) used to store your channel ledger. The remote peer needs to use the same database as your [blockchain network](../v10_dashboard.html#network-preferences). | none | yes |
| `CouchDB image`| Only applies if CouchDB was selected as the ledger database. | Autofilled to the installed path, do not change this value. | yes |
|**Data persistence** | Enable the ability to persist data after cluster restarts or fails. See [storage in Kubernetes ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/ "Volumes") for more information. | | |
| `Data persistence enabled`| State data will be available when the container restarts. *If unchecked, all data will be lost in the event of a failover or pod restart.* | checked | no |
| `Use dynamic provisioning`| Use Kubernetes dynamic storage. | checked | no |
| | | | |
|**Persistent volume configuration** | **Persistent Volume Claim to be used by your peer** |  |  |
| `Persistent volume claim`| Enter a name for your new Persistent Volume Claim (PVC) which will be created. | my-data-pvc | no |
| `Storage class name`| Chose storage class name. | Blank if creating a new PVC; otherwise, specify the storage class that is associated with the existing PVC. | no |
| `Existing volume claim`| Specify the name of an existing Volume Claim and leave all other fields blank. | new claim name | no |
| `Storage access mode`| Specify the storage [access mode ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") for the PVC.  | ReadOnlyMany| no |
| `Selector label`| [Selector label ![External link icon](../images/external_link.svg "External link icon") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") for your PVC.| default | no |
| `Selector value`| [Selector value ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") for your PVC. | default | no |
| `Volume claim size`| Size of the Volume Claim, must be larger than 2Gi. | 8Gi  | yes |
| | | | |
|**State database data persistence** | **Persist data after your container is restarted or fails. See [storage in Kubernetes ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/ "Volumes") for more information** | | |
| `Data persistence enabled`| Ledger data will be available when the container restarts. *If unchecked, all data will be lost in the event of a failover or pod restart.*| checked | no |
| `Use dynamic provisioning`| Use Kubernetes dynamic storage.| checked | no |
| | | | |
|**State database persistent volume configuration** |**Persistent Volume Claim to be used for the state database. See [storage in Kubernetes ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/ "Volumes") for more information.** |||
| `Persistent volume claim`| Enter a name or use the default for your new CouchDB Persistent Volume Claim which will be created. | statedb-pvc | no |
| `State database storage class name`|Choose storage class name.| none | no |
| `State database existing volume claim`| To use an existing volume claim, enter the pvc name and leave all other fields blank. | none | no |
| `State database storage access mode`| Specify the storage [access mode ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") for the PVC. | ReadOnlyMany| no |
| `Selector label`| [Selector label ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") of your PVC.| default | no |
| `Selector value`| [Selector value ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") of your PVC.| default | no |
| `State database volume claim size`| Size of the Persistent Volume Claim. | 8Gi | no |
| | | | |
| **Peer resources** | **Min and Max CPU and Memory for the peer container** | | |
| `Peer CPU request` | Minimum number of CPUs to allocate to the peer. | 2 | yes |
| `Peer CPU limit` | Maximum number of CPUs to allocate to the peer.| 2 | yes |
| `Peer Memory request` | Minimum amount of memory to allocate to the peer. | 4Gi | yes |
| `Peer Memory limit` | Maximum amount of memory to allocate to the peer. | 4Gi | yes |
| | | | |
|**CouchDB Resources** | **Min and Max CPU and Memory that are allocated to CouchDB container. This section applies only if CouchDB was selected as the state database**| | | |
| `CouchDB CPU request` | Minimum number of CPUs to allocate to CouchDB.| 2 | yes |
| `CouchDB CPU limit` | Maximum number of CPUs to allocate to CouchDB. | 2 | yes |
| `CouchDB Memory request` | Minimum amount of memory to allocate to CouchDB.| 4Gi | yes |
| `CouchDB Memory limit` | Maximum amount of memory to allocate to CouchDB. | 4Gi | yes |

When CouchDB is selected as the remote peer database, two containers are created in the pod, one for the peer and one for CouchDB.
The Peer container includes a single volume mount to the Peer PVC that stores the blocks and transactions on the file system. The CouchDB container mounts the peer state database PVC that contains the ledger data.

When LevelDB is selected as the Remote peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.

| Remote Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Remote Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Remote Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  This command generates the `peer-cert.pem` file, which is your Peer TLS certificate.

--->

## Step Four: Verify the remote peer installation
{: #verify-installation-icp}

After you install the remote peer, you can view the peer log to check whether your peer installation is successful.

1. After you click **Install**, click the **View Helm Release** button in the pop-up window. Scroll down to the **Pods** section. When the Pods status shows **Running**, you can find your remote peer instance under the **Deployments** section.

2. In the ICP console, click the **Menu** icon in the upper left corner. From the menu list, click **Workloads** > **Deployments**. In the deployment table, click the remote peer instance that you create and the deployment overview screen opens. Click the **Logs** tab to view your remote peer logs. You can enter the string `Started` in the search field.

  - If your remote peer is started successfully, you can see logs that are similar to the following example:
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - If you see no logs in the screen, your remote peer did not start successfully. To view additional logs to figure out the problem, click the **Menu** icon in the upper left corner and then click **Workloads** > **Helm Releases**. Click your helm release to open it. Click the **View Logs** link next to the associated **Pod** to view additional chart logs in the Kibana interface.

3. To verify that the remote peer can connect to your {{site.data.keyword.blockchainfull}} Platform network, you can
run a peer CLI command from inside the remote peer container. Run the `peer channel fetch` CLI command to fetch the genesis block from a channel:

   1. If not already connected to your ICP cluster, follow these [instructions](remote_peer_operate_icp.html#remote-peer-kubectl-configure) to connect to it and use the cli from inside the peer container.

   2. Your organization needs to be added to a channel in the network before you can fetch the genesis block.

     - You can start a new channel for the remote peer. As the channel initiator, you can automatically include your organization during [channel creation](create_channel.html#creating-a-channel). Note that you need to have at least one peer on {{site.data.keyword.blockchainfull_notm}} Platform before you can create a channel in the Network Monitor.  

     - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](create_channel.html#updating-a-channel). A member of the channel with peers on {{site.data.keyword.blockchainfull_notm}} Platform can use the Network Monitor to add your organization to the channel even if you do not host any peers on the platform.

      The remote peer uploads its signing cert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joined and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

   3. Retrieve configuration information from your `creds.json` file available on the "Overview" screen of your Network Monitor. Click **Connection Profile** and then **Download**.

     - Find the orderer URL by searching for **orderers**, which is located under `orderers > url`. Make a note of the URL that begins with the network name. The URL is similar to the following example:

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - Find the name of your organization by searching for **organizations**. This should be the same organization as you use to register your remote peer. You can find your organization's name together with its associated `mspid`. Make a note of the value of the `mspid`.

    4. Run the following commands to set the environment variables in the peer container.

       ```
       export ORDERER_1=<ORDERER_URL>
       export CHANNEL=<CHANNEL_NAME>
       export CC_NAME=<CC_NAME>
       export ORGID=<ORGANIZATION_MSP_ID>
       export PEERADDR=<PEER_ADDR>
       ```
       {:codeblock}

       Replace the fields with your own information.
         - Replace `<ORDERER_URL>` with the hostname and port of the orderer from the `creds.json` file.
         - Replace `<CHANNEL_NAME>` with the name of the channel that the peer joins.
         - Replace `<CC_NAME>` with any name to refer to your chaincode.
         - Replace `<ORGANIZATION_MSP_ID>` with the name of the organization from the `creds.json` file.
         - Replace `<PEER_ADDR>` with `localhost:7051`

       For example:

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. Run the following peer CLI command to fetch the genesis block of the channel.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **Note:** It is possible that when you run any of these CLI commands, you might experience the following warning that can be safely ignored.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     Verify that the command worked successfully and that genesis block is added to your peer container by running the following command:

     ```
     ls *.block
     ```
     {:codeblock}

     You know that the genesis block is added successfully when you see something that is similar to the following example:

     ```
     defaultchannel_0.block
     ```

## What's next
{: #whats-next-icp}

After you set up the remote peer in ICP, you can complete several operational steps before you can submit transactions to and read the distributed ledger from the blockchain network. For more information, see [Operate a remote peer](remote_peer_operate_icp.html).

## High Availability
{: #high-availability}

As a high availability best practice, it is strongly recommended that you deploy a minimum of two peers per organization.

Complete the following steps to deploy highly available remote peers on ICP:

1. Review how to [configure your ICP Cluster for High Availability ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "High availability {{site.data.keyword.cloud_notm}} Private clusters").

2. Install the peer multiple times on your cluster to deploy peers to different worker nodes. The Helm Chart contains an anti-affinity policy that is based on your network ID and OrgID. The ICP cluster detects whether your peers are connecting to the same network from the same org, and attempts arrange those peers on different worker nodes.  
  **Note:** Although the policy "prefers" to put peers on different worker nodes, it doesn't mandate it. For example, if you have only one worker node, all your peers are deployed on this same node.

If you deploy at least two peers, you also need to configure your [applications for high availability](../v10_application.html#ha-app).

## Security considerations
{: #icp-security}

The remote peers are deployed outside of {{site.data.keyword.blockchainfull}} Platform; therefore, you are responsible for managing the security of the remote peer. This includes important areas of security that Enterprise Plan networks provide, such as key management and data encryption. Review the following topics when you consider security for your remote peers.

### Identity and access control
{: #icp-security-node}

Outside of {{site.data.keyword.cloud_notm}}, you need to set up the identity management, authentication, and access control for users who interact with the remote peers. You can use [ICP user management ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "Security") to configure these capabilities.

### Data security
{: #icp-security-data}

{{site.data.keyword.blockchainfull}} Platform Enterprise Plan uses whole disk encryption that is based on [symmetric key encryption ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Symmetric cryptography") to protect all the data that the networks use. You must take similar steps in your own environment to protect your remote peer data.

### Data residency
{: #icp-data-residency}

Data residency mandates that the processing and storage of all blockchain ledger data remain within the border of a single country.
For more information on how data residency can be accomplished, see [Data residency](remote_peer.html#data-residency).

### Key management
{: #icp-security-key-management}

Key management is a critical aspect of remote peer security. If a private key is compromised or lost, hostile actors might be able to access your remote peer's data and functionality. {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan uses [Hardware Security Modules](../glossary.html#hsm) (HSM) to store the private keys of your network. The HSM is a physical appliance that prevents other parties from accessing your private key.

When you deploy a remote peer on ICP, you are responsible for managing your private keys. Although {{site.data.keyword.blockchainfull_notm}} Platform generates your private keys, those keys are not stored on the Platform. It is essential to ensure that you store your keys in a secure location so that they are not compromised. You can find the private key of your remote peer in the keystore folder of your peer MSP, at the `/mnt/crypto/peer/peer/msp/keystore/` directory within your peer container. For more information about the certificates inside your remote peer, see the [Membership Services Provider](../certificates.html#msp) section of the [Managing certificates on {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html) tutorial.

You can use Key Escrow to recover lost private keys. This needs to be performed prior to the loss of any keys. If a private key cannot be recovered, you need to get new private keys by registering a new identity with your Certificate Authority. You should also remove and replace your signCert from any channels that you joined.

<!---
In IBP when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

### TLS
{: #icp-security-tls}

[Transport Layer Security ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "An overview of the SSL or TLS handshake") (TLS) is embedded in the trust model of Hyperledger Fabric. All components on {{site.data.keyword.blockchainfull_notm}} Platform use TLS to authenticate and communicate with each other. Therefore, the network components on {{site.data.keyword.blockchainfull_notm}} Platform need to be able to complete a TLS handshake with your remote peers. One implication of this is that you need to enable passthru, by using white listing for example, in your web firewall from client apps to your peer.

You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](../v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your remote peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.

### Membership Service Provider configuration
{: #icp-security-MSP}

Components of {{site.data.keyword.blockchainfull_notm}} Platform consume identities via Membership Service Providers (MSPs). MSPs associate the certificates that the CAs issue with network and channel roles. For more information about how MSPs work with the remote peer, see [Membership Service Providers (MSPs)](../certificates.html#msp).

### Application security
{: #icp-security-appl}

Because all chaincode invocations are signed, Fabric manages the application security. In addition, Fabric also includes ACL-based application level checks.

## License and pricing
{: #license-pricing-icp}

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer on {{site.data.keyword.cloud_notm}} Private is a free Beta edition that is suitable for evaluation and experimentation and is deployed as an ICP application. You can access the [Github package ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and-->and download the [Helm Chart ![External link icon](../images/external_link.svg "External link icon")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Helm Chart").

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

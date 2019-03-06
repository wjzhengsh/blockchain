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
{:pre: .pre}

# Deploying {{site.data.keyword.blockchainfull_notm}} Platform console on {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-icp}

These instructions describe how to deploy an {{site.data.keyword.blockchainfull}} Platform console in your own infrastructure through {{site.data.keyword.cloud_notm}} Private. You do not have to deploy the console in the same environment as your other blockchain components.
{:shortdesc}

You will use a Helm chart to deploy {{site.data.keyword.blockchainfull_notm}} Platform console on your {{site.data.keyword.cloud_notm}} Private. Each of your deployments installs a single instance of the console on your {{site.data.keyword.cloud_notm}} Private name space.

## Considerations
{: #ibp-console-deploy-icp-considerations}

Before you deploy the console, ensure that you understand the following considerations:

- The blockchain components must have already been deployed in {{site.data.keyword.cloud_notm}} Private or Amazon Web Services (AWS). If you haven't already, see [About {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private] or About [IBM Blockchain Platform for AWS] for more information.
- You are responsible for the management of health monitoring, security, logging, and resource usage of your console.
- You can connect your console only to blockchain components that are at Hyperledger Fabric v1.2.1 level.

## Resources required
{: #ibp-console-deploy-icp-resources-required}

Ensure that your {{site.data.keyword.cloud_notm}} Private system meets the minimum hardware resource requirements:

| Component | vCPU | RAM | Disk for data storage |
|-----------|------|-----|-----------------------|
| IBP console | 1 | 192 MB | 1 GB |
| CouchDB for IBP console | 1 | 1 GB | 1 GB |

 **Notes:**
 - A vCPU is a virtual core that is assigned to a virtual machine or a physical processor core if the server is not partitioned for virtual machines. You need to consider vCPU requirements when you decide the virtual processor core (VPC) for your deployment in {{site.data.keyword.cloud_notm}} Private. VPC is a unit of measurement to determine the licensing cost of IBM products. For more information about scenarios to decide VPC, see [Virtual processor core (VPC) ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - These minimum resource levels are sufficient for testing and experimentation. For an environment with a large volume of transactions, it is important to allocate a sufficiently large amount of storage; 250 GB for your {{site.data.keyword.blockchainfull_notm}} Platform console as an example. The amount of storage to use will depend on the number of transactions and the number of signatures that are required from your network. If you are about to exhaust the storage on your peer or orderer, you must deploy a new console with a larger file system and importing your network components again accordingly.

## Storage
{: #ibp-console-deploy-icp-resources-required-storage}

You need to determine the storage that your console will use. If you use the default settings, the Helm chart will create a new 8 Gi Persistent Volume Claim (PVC) with the name of `console-pvc` for your console data, and another 8 Gi PVC with the name of `couchdb-pvc` for your CouchDB.

If you do not want to use the default storage settings, ensure that a *new* `storageClass` is set up during the {{site.data.keyword.cloud_notm}} Private installation or the Kubernetes system administrator needs to create a storageClass before you deploy.

You can choose to deploy the console on either the AMD64 or S390X platforms. However, be aware that [Dynamic provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) is only available for AMD64 nodes in {{site.data.keyword.cloud_notm}} Private. If your cluster includes a mix of S390X and AMD64 worker nodes, dynamic provisioning cannot be used.

If you do not use dynamic provisioning, [Persistent Volumes ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) must be created and set up with labels that can be used to refine the Kubernetes PVC bind process.

## Prerequisites
{: #ibp-console-icp-prereq}

Before you deploy the {{site.data.keyword.blockchainfull_notm}} Platform console on {{site.data.keyword.cloud_notm}} Private, ensure you complete the steps in the tutorials of [Setting up {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

You also need to install or configure the following dependencies before you deploy the {{site.data.keyword.blockchainfull_notm}} Platform console.

- [Register with the IBM App ID Service](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Install Docker](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### Registering with App ID service
{: #ibp-console-icp-prereq-app-id}

[App ID ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/docs/services/appid/index.html#gettingstarted "App ID") is a user management service provided by {{site.data.keyword.cloud_notm}}. The {{site.data.keyword.blockchainfull_notm}} Platform console uses the App ID service to manage who will be able to use the dashboard and operate your blockchain network. This allows users of the UI to use credentials issued by your own organization or by third parties such as Google or Facebook, without them needing to have IBM ID's. Only the user who deploys the console will need an {{site.data.keyword.IBM_notm}} ID to register with the service.

Before you deploy the console, you need to get an IBM ID to perform an one-time registration with the App ID Cloud Directory. Then, you need to fetch the APP ID service credentials to pass to the console. Use the following steps to register with the service and get your service credentials.

1. If you do not already have one, you need to create an [{{site.data.keyword.IBM_notm}} ID ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid).

2. Use your {{site.data.keyword.IBM_notm}} ID to log in or sign up for [{{site.data.keyword.cloud_notm}} ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/catalog/services/app-id  "IBM Cloud App ID"). Then, go to the [App ID ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/catalog/services/app-id  "IBM Cloud App ID") service in the Catalog. Select the `Lite` plan or the `Graduated tier` depending on your service needs. Then, click the **Create** button to launch the "Welcome" screen.

3. When you are in the "Welcome" screen, Click the **Manage** link on the left navigation panel to view or update the preferred identity providers. These can be third party providers, such as Google, or your organizations own identity service.

4. Click the **Settings** tab on the table to configure a redirect URL. This will be the URL where you will deploy and access the {{site.data.keyword.blockchainfull_notm}} Platform console. To use localhost, specify `http://localhost/auth/cb` and click the `+` sign.

5. Add your email address to service so that you can log in when you first configure the console. Click **Users** link in the left navigation panel and click the **Add User** button. Enter your email information and click **Save**.

6. Click the **Service Credentials** link on the left navigation panel. If this is the first time that you use the service, you need to generate credentials by clicking **New credential**. Click **View Credentials** to copy the credentials that are required to access the service.  When the details for creating new credentials appear, select the `reader` option, and a Service ID if you wish to assign the UI a specific name. The App ID credentials look similar to the JSON values as below:
  ![Service credentials](../images/service_credentials.gif "Service credentials")

7. Click the Copy icon and save your credentials. You will use them when you configure the console.

### Installing Docker
{: #ibp-console-icp-prereq-docker}

Install [Docker ![External link icon](../images/external_link.svg "External link icon")](https://www.docker.com/get-started) version 17.06.2-ce or higher in the environment where you will deploy the {{site.data.keyword.blockchainfull_notm}} Platform console. You can check the version of Docker that you installed with the following command from a terminal window:

```
docker --version
```
{:codeblock}

**Note:** Installing Docker for Mac, or Windows, or Docker Toolbox will also install Docker Compose. If you already had Docker installed, you should check that you have Docker Compose version 1.14.0 or greater installed. If not, we recommend that you install a more recent version of Docker. You can check the version of Docker Compose that you installed with the following command from a terminal window:

```
docker-compose --version
```
{:codeblock}


## Step one: Download {{site.data.keyword.blockchainfull_notm}} Platform console package from {{site.data.keyword.IBM_notm}} Passport Advantage
{: #ibp-console-icp-download-icp}

Contact your {{site.data.keyword.IBM_notm}} seller to download the {{site.data.keyword.blockchainfull_notm}} Platform console package from [Passport Advantage ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online for customers"). Don't have an IBM seller? Visit this <link> to register and get one ...

## Step two: Import {{site.data.keyword.blockchainfull_notm}} Platform console Helm chart to {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-icp-import-helmchart}

For reference, see [Installing {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

## Step three: Deploy the {{site.data.keyword.blockchainfull_notm}} Platform console on {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-helmchart-icp}

After you import the {{site.data.keyword.blockchainfull_notm}} Platform console Helm chart, you can use the following steps to configure and install your console.

1. Log in to the {{site.data.keyword.cloud_notm}} Private console and click the **Catalog** link in the upper right corner.
2. Click `Blockchain` in the left navigation panel to locate the tile labeled `ibm-blockchain-platform-ui`. Click the tile to open it and you can see a Readme file that includes information about configuring and installing the Helm chart.
3. Click the **Configuration** tab on the top of the panel or click the **Configure** button in the lower right corner.
4. Complete the [configuration settings](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters).
5. Click **Install**.

### {{site.data.keyword.blockchainfull_notm}} Platform console parameters
{: #ibp-console-icp-parameters}

The following table lists the configurable parameters of the {{site.data.keyword.blockchainfull_notm}} Platform console and their default values.

|  Parameter     | Description    | Default  | Required |
| --------------|-----------------|-------|------- |
| `Helm release name`| The name of your Helm release. Must begin with a lowercase letter and end with any alphanumeric character, must only contain hyphens and lowercase alphanumeric characters. You must use a unique Helm release name each time when you attempt to install an console. | none | yes |
| `Target namespace`| Choose the Kubernetes namespace to install the Helm chart. | none | yes |
| `Image repository`| Location of the {{site.data.keyword.blockchainfull_notm}} Platform console Helm chart. | registry.ng.bluemix.net/op-tools/op-tools | yes |
| `Image tag`| Value of the tag that is associated with the {{site.data.keyword.blockchainfull_notm}} Platform console image. This field is autofilled to the image version. Do not change it. | v0.0.34 | yes |
| `Service type` | Used to specify whether [external ports should be exposed ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) on the peer. Select NodePort to expose the ports externally (recommended), and ClusterIP to not expose the ports. LoadBalancer and ExternalName are not supported in this release. | NodePort | yes |
| `Console ingress` | Specify the hostname that you want to use to access the {{site.data.keyword.blockchainfull_notm}} Platform console. | none | no |
| `Configtxlator ingress` | Specify the hostname that you want to use to access the configtxlator service. | none | no |
| `Persistence enabled` | If checked, data will be available when the container restarts. Otherwise, all data will be lost in the event of a failover or pod restart. | checked | no |
| `Use dynamic provisioning` | Check to enable dynamic provisioning for storage volumes. | checked | no |
| `Storage class name`| Specify a unique storage class name. Otherwise, the default storage class in the cluster is used. | none | Depends on how the {{site.data.keyword.cloud_notm}} Private cluster is configured. Check with your cluster administrator. |
| `Existing volume claim`| Specify the name of an existing Volume Claim and leave all other fields blank. | none | no |
| `Selector label`| [Selector label ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") for your PVC. | none | no |
| `Selector value`| [Selector value ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") for your PVC. | none | no |
| `Storage access mode`| Specify the storage [access mode](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") for the PVC. | ReadWriteMany | yes |
| `Volume name`| Specify the name of the PVC. | none | yes |
| `CPU request` | Minimum number of CPUs to allocate to the console. | 250m | yes |
| `CPU limit` | Maximum number of CPUs to allocate to the console.| 500m | yes |
| `Memory request` | Minimum amount of memory to allocate to the console. | 256 Mi | yes |
| `Memory limit` | Maximum amount of memory to allocate to the console. | 1Gi | yes |

## Step four: Configure Proxy IP
{: #ibp-console-icp-config-proxy-ip}

After the installation completes, click the **View Helm release** button to open the Helm release page of your console. You can also click **Menu** > **Workloads** > **Helm Releases** in {{site.data.keyword.cloud_notm}} Private and click your Helm release.

On the Helm release page of your console, find the Proxy IP of the {{site.data.keyword.cloud_notm}} Private cluster, for example, `9.12.19.115`.

In the client machine where you want to access the console, add entries in `/etc/hosts` to map the hostnames that you input in previous step for optools and configtxlator services to the Proxy IP. For example:
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## Step five: Set up {{site.data.keyword.blockchainfull_notm}} Platform console
{: #ibp-console-icp-setup}

*This section would benefit from a gif animation.*

1. On {{site.data.keyword.cloud_notm}}, go to **Identity Providers** > **Manage** from left navigation and select the **Authentication Settings** tab.
2. In the **Add web redirect URLs** field, specify the URL that you want to use to access the console with your App ID.
3. In your web browser, access the console with the hostname that you configured in {{site.data.keyword.cloud_notm}} Private. For the first login, you will see the console setup panels.
  1. On the **Authentication** tab, enter your App ID.
  2. On the **Configuration** tab, paste the App ID service credentials that you copied in [Step One: Register with App ID service](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id).
  3. On the **Add Users** tab, specify a list of email addresses of `Admin` and `General` users that are authorized to the console. The email domains are restricted to the set of identity providers, such as your corporate, Google, or Facebook, which are selected when the App ID service was registered in {{site.data.keyword.cloud_notm}}.
    - The `Admin` role is required to be able to add new users or remove existing users from the console authorization list.   **Tip:** If you will be the person administering the console, remember to include your own email address in the list.
    - The `General` role allows users **view only** access to the components in the console.

    Lastly, you need to specify a single `Admin contact email` that is displayed as a contact email address when an unauthorized user, one that is not in either of the lists above, tries to access the console. This email address does not have to be an admin user in the console. Note that this information can also be created or modified later in the **Members** tab in the console.
  4. Click **Proceed to Login**. All users that are specified in the lists above can now log in to the {{site.data.keyword.blockchainfull_notm}} Platform console by using their email addresses.

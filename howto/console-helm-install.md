---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-18"

keywords: IBM Cloud Private, IBM Blockchain Platform, install, Helm chart, PodSecurityPolicy

subcollection: blockchain

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Installing the {{site.data.keyword.blockchainfull_notm}} Platform Helm chart
{: #console-helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private is delivered as a Helm chart that can be installed on a local {{site.data.keyword.cloud_notm}} Private cluster. After you install the Helm chart, you can find {{site.data.keyword.blockchainfull_notm}} Platform as an application in the {{site.data.keyword.cloud_notm}} Private Catalog.

The Helm chart must be purchased through [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html){: external}. Upon your purchase, technical support for {{site.data.keyword.blockchainfull_notm}} Platform is included.

Before you install {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, review the [Considerations and limitations](/docs/services/blockchain/console-icp-about.html#console-icp-about-considerations). For more information about pricing, support, and security and data residency considerations, see [About {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/console-icp-about.html#console-icp-about).

## Prerequisites for installing the Helm chart
{: #console-helm-install-prereqs}

Before you install the Helm chart, you must have configured an {{site.data.keyword.cloud_notm}} Private cluster and created a new target namespace that is bound to a pod security policy. Review the instructions for [setting up and configuring an {{site.data.keyword.cloud_notm}} Private cluster](/docs/services/blockchain/ICP_console_setup.html#icp-console-setup). You can only deploy one console per namespace. If you plan to create multiple blockchain networks, for example to create different environments for development, staging, and production, you should create a unique namespace for each environment.

### PodSecurityPolicy Requirements
{: #console-helm-install-prereqs-pod-security-requirements}

The {{site.data.keyword.blockchainfull_notm}} Platform Helm chart requires specific security and access policies be bound to the target namespace prior to installation. Use the following steps to configure the policies prior to configuration of the Helm Chart:

1. Choose either a predefined PodSecurityPolicy for your namespace, or have your cluster administrator create a custom PodSecurityPolicy for you:
  - You can use the predefined PodSecurityPolicy of [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
  - You can also create using YAML below a Custom PodSecurityPolicy definition:

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

2. Create a ClusterRole for the PodSecurityPolicy.
  - If you created a custom security policy, you can create a ClusterRole using the YAML file below:

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

  - If you are using a predefined PodSecurityPolicy, you only need to create a ClusterRole using the second apiGroups section:

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      annotations:
      name: ibm-blockchain-platform-clusterrole
      rules:
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

3. Create a custom ClusterRoleBinding. If you decide to change the ServiceAccount name in the file below, you need to provide the name to the `Service account name` field in the **All Parameters** Section of the configuration page when deploying the Helm chart.

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

You can complete the following steps to use YAML files to bind security and access policies to your namespace:

1. Save the YAML file to your local system.

2. Log in to your {{site.data.keyword.cloud_notm}} Private cluster and select the target namespace of your deployment.

  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

3. Use the following command to apply the policy to the target namespace:

  ```
  kubectl apply -f <filename>.yaml
  ```
  {:codeblock}

## Importing the Helm chart to {{site.data.keyword.cloud_notm}} Private
{: #console-helm-install-importing}

1. Download the Helm chart of {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private from [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html){: external}.

2. If you haven't already, log in to your {{site.data.keyword.cloud_notm}} Private cluster.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. Ensure that the [Docker CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.2.0/manage_images/configuring_docker_cli.html) is configured. After you configure the Docker CLI, access the image registry on your cluster by using the following command:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. Find the name of the repository in {{site.data.keyword.cloud_notm}} Private to upload your Helm chart by using the following command:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  When this command completes successfully, you can see a list of repo's in your cluster. Choose the name of the target repo and save it. You need to use it in the command below.

5. Import Helm chart by using the command line. From the directory where you stored the downloaded Helm chart package from PPA, run the following command in the {{site.data.keyword.cloud_notm}} Private CLI to import the Helm chart into your {{site.data.keyword.cloud_notm}} Private cluster.

  ```
  cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
  ```
  {:codeblock}

  Replace the following values:
  - `<archive-name>` with the name of the downloaded `.tgz` file.
  - `<cluster_CA_domain>:8500` with domain that you use to log in to your {{site.data.keyword.cloud_notm}} Private cluster.
  - `<repo-name>` with the helm repository where you want to upload the chart. Run 'cloudctl catalog repos' to list your repositories.

  When this command completes successfully, you can see something that is similar to the following information:

  ```  
  Uploading Helm chart(s)
   Processing chart: charts/ibm-blockchain-platform-prod-1.1.0.tgz
   Updating chart values.yaml
   Uploading chart
  Loaded Helm chart
  OK

  Synch charts
  OK

  Archive finished processing
  ```  
  </details>

Click the **Catalog** button in the {{site.data.keyword.cloud_notm}} Private console, and then click **Blockchain** in the left navigation panel. If the import was successful, the **ibm-blockchain-platform-prod** tile will be visible on the {{site.data.keyword.cloud_notm}} Private Catalog page.

## Next steps
{: #console-helm-install-next-steps}

After you install the Helm chart, you can use the **ibm-blockchain-platform-prod** tile in your {{site.data.keyword.cloud_notm}} Private Catalog to deploy the {{site.data.keyword.blockchainfull_notm}} Platform console. You need to create a new target namespace for the deployment and ensure that your cluster has sufficient resources for your {{site.data.keyword.blockchainfull_notm}} Platform components before you complete the configuration page. For more information, see [Deploying the {{site.data.keyword.blockchainfull_notm}} console on {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/console-deploy-icp.html#console-deploy-icp).

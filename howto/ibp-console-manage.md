---

copyright:
  years: 2019
lastupdated: "2019-04-23"

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


# Administering your console
{: #ibp-console-manage-console}

There are various actions that you can take to manage your console behavior. This topic describes actions outside of the blockchain node operations that aid you in your day-to-day usage of the console.
{:shortdesc}

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network.

## Add and remove users from the console
{: #ibp-console-manage-console-add-remove}

The console authorization process is changing during the beta program. If you have an existing blockchain service instance that used the original {{site.data.keyword.IBM_notm}} ID authentication, you are no longer able to see your users listed in the **Users** tab. You must grant them access to the console by using the IAM process described below.
{:important}

Every user that accesses the console must be assigned an access policy with an {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM) user role defined. The policy determines what actions the user can perform within the console. The {{site.data.keyword.blockchainfull}} Platform console is provisioned with the email address of the {{site.data.keyword.cloud_notm}} owner as the console administrator.  By default, this {{site.data.keyword.cloud_notm}} user is given the `Manager` role for the Blockchain Platform 2.0 service in IAM. The console administrator can then grant other users access to the console by using the IAM UI. For more information about IAM，see [What is IAM ![External link icon](../images/external_link.svg "External link icon") ](/docs/iam?topic=iam-iamoverview#iamoverview "What is IAM?").  

When you [use IAM to invite users ![External link icon](../images/external_link.svg "External link icon")](/docs/iam?topic=iam-iamuserinv#iamuserinv "Use IAM to invite users"), you need to complete the following steps to configure their roles and access to the console:
 1. From the menu bar, click  **Manage** > **Access (IAM)** and then select **Users**.
 2. Click **Invite User**.
 3. Type in the email address of the user or users.
 4. From the `Services` drop-down list, select `Blockchain Platform 2.0`.
 5. Scroll down to `Select roles`.
 6. Under `Assign service access roles`, choose a role for the user, which can be `Manager`, `Writer`, and `Reader`.
 7. Click **Invite users**.

| Role | Capabilities |
|--------|----------|
| Manager | As a Manager, you have permissions beyond the Writer role. You can do everything a Reader and Writer can do as well as: <ul><li>Add new components.</li><li>Delete provisioned components.</li><li>Change console logging levels.</li></ul> |
| Writer | As a Writer, you have permissions beyond the Reader role, including: <ul><li>Import components.</li><li>Remove imported components.</li><li>Register users on a CA.</li></ul>  |
| Reader | As a reader, you can perform read-only actions including: <ul><li>View console UI.</li><li>View console log.</li><li>Export components.</li></ul> | |

 Permissions are cumulative. If you select a `Manager` role, the user will also be able to perform all `Writer` and `Reader` actions, you are not required to additionally check those roles.   Likewise, a user with the `Writer` role will be able to perform all of the actions in the `Reader` role.  For console access, you only need to select a role under the `Service Access Roles`, you do not need to select anything under the `Platform Access Roles`.

![Update Kubernetes version](../images/AddICPUser.gif)

After you add new users to the console, the users might not be able to view all the nodes, channels, or chaincode, which other users deploy. To work with these components, each user needs to import the associated identities into their own console wallet. For more information, see [Storing identities in your console wallet](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet).
{:important}

If you need to modify a user's role:
 1. From the menu bar, click  **Manage** > **Access (IAM)** and then select **Users**.
 2. Click the Actions menu next to the user you want to modify and select **Assign access**.
 3. Select the tile **Assign access to resources**.
 4. From the `Services` drop-down list, select `Blockchain Platform 2.0`.
 5. Scroll down to `Select roles`.
 6. Under `Assign service access roles`, choose a role for the user, which can be `Manager`, `Writer`, and `Reader`.
 7. Click **Assign**.

When you need to remove a user's access to the console, follow instructions in the [IAM Removing users topic ![External link icon](../images/external_link.svg "External link icon")](/docs/iam?topic=iam-remove#remove "Removing users").

### Assign access roles to individual or groups of users in IAM
{: #ibp-console-manage-console-users-groups}

When you set {{site.data.keyword.cloud_notm}} IAM policies, you can assign roles to an individual user or to a group of users.

<dl>
<dt>Individual users</dt>
<dd>You might have a specific user that needs more or less permissions than the rest of your team. You can customize permissions on an individual basis so that each person has the permissions that they need to complete their tasks. You can assign more than one {{site.data.keyword.Bluemix_notm}} IAM role to each user.</dd>
<dt>Multiple users in an access group</dt>
<dd>You can create a group of users and then assign permissions to that group. For example, you can group all team leaders and assign administrator access to the group. Then, you can group all developers and assign only write access to that group. You can assign more than one {{site.data.keyword.Bluemix_notm}} IAM role to each access group. When you assign permissions to a group, any user that is added or removed from that group is affected. If you add a user to the group, then they also have the additional access. If they are removed, their access is revoked.</dd>
</dl>

The users that you add in IAM are simply email addresses of users who can log in to the console. They have no relationship to the **Available Organizations** in the Organizations tab or the identities stored by the console wallet.
{:note}

## Update the administrator email address

If the console is used by multiple people or organizations, it is recommended that you create a functional email address to access your network. The use of a functional email allows you to continue to access the console even when the original administrator leaves your organization or has their email address suspended. Only a single email address can be used as the administrator contact.

To update the email address of the console administrator that was configured when the console was deployed, you must be logged in as the console administrator. Navigate to the **Users** tab and click **Configure** on the **{{site.data.keyword.IBM_notm}} ID** tile. In the panel that opens, specify a new email address for the console administrator.

## Viewing your logs
{: #ibp-console-manage-logs}

When you use the {{site.data.keyword.blockchainfull_notm}} Platform console, you might need to view logs to debug an issue.

### Viewing your console logs
{: #ibp-console-manage-console-logs}

You can easily access the console logs if you need to debug problems that you encounter when you use the console or operate your nodes. You can also sett the logging level to increase or decrease the amount of logs that the console collects. The console logs are collected separately from the [node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), which are collected by the {{site.data.keyword.cloud_notm}} Kubernetes Service.

Navigate to the **Settings** tab in the console browser to change the logging settings. The console logs are collected from two separate sources:

  * **Client logging:** These logs are collected when commands are sent from your browser to the console.
  * **Server logging:** These logs are collected when the console sends commands to your nodes and from the console deployment. These logs include the Hyperledger Fabric logging output.

Set the amount of collected logs by using the drop-down list under each log type. For example, **Error** and **Warning** collect the least amount of logs, while **Debug** and **All** collect the most.

You can view only the console logs if you are logged in as a console administrator. To view the logs from the **Settings** tab, replace the word `settings` in the browser URL with `api/v1/logs`. For example, if your console url is `localhost:3001/settings`, you can view your logs by navigating to `localhost:3001/api/v1/logs`. Client and server logs are collected in separate files. The most recent logs are at the top of the page.

### Viewing your node logs
{: #ibp-console-manage-console-node-logs}

The logs of your peers, orderers, and Certificate Authorities are collected by the {{site.data.keyword.IBM_notm}} Kubernetes Service. Use the steps below to view the logs of your nodes from the cluster where you deployed your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network.

1. Open the [{{site.data.keyword.cloud_notm}} dashboard ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/resources) and navigate to the overview screen of your {{site.data.keyword.IBM_notm}} Kubernetes Service cluster.
2. Above the cluster overview screen, click **Kubernetes Dashboard**.
3. Inside the Kubernetes dashboard, use the left navigation Namespace drop-down list to change the namespace to the {{site.data.keyword.blockchainfull_notm}} Platform service instance. The service instance name will be a long string of letters and numbers. You can find your service instance name at the beginning of the URL of your {{site.data.keyword.blockchainfull_notm}} Platform console.
4. On the left navigation, click **Pods** to view the list of node pods that you have deployed.
5. Click on a pod. Then click **View logs** on the top menu to open the logs of your node. Above the logs, you can use the drop-down menu after **Logs from** to view the logs from the different containers within the pod. For example, your peer and the state database (CouchDB for example) run in different containers and generate different logs.

By default, the logs of your nodes are collected locally within your cluster. You can also use the {{site.data.keyword.cloud_notm}} Log Analysis service or a third-party service to collect, store, and analyze the logs from your network. For more information, see [Logging and monitoring for the {{site.data.keyword.IBM_notm}} Kubernetes Service ![External link icon](../images/external_link.svg "External link icon")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Logging and Monitoring for the {{site.data.keyword.IBM_notm}} Kubernetes Service"). It is recommended that you take advantage of the [{{site.data.keyword.cloud_notm}} LogDNA ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-kube#kube "Managing Kubernetes cluster logs with IBM Log Analysis with LogDNA") service that allows you to easily parse the logs in real time.

### Viewing your smart contract container logs
{: #ibp-console-manage-console-container-logs}

If you encounter issues with your smart contract, you can view the smart contract, or chaincode, container logs to debug an issue:

- Open your Kubernetes dashboard and click the peer pod where the smart contract is running.
- Click the `exec` link from your dashboard to exec into the pod. By default it points to peer container.
- Switch to the `dind` container by selecting it from the drop-down list.
- Run the command `docker ps -a` to see the list of chaincode containers.
- Run `docker logs <chaincode-container-ID>` replacing <chaincode-container-ID> with the ID of your chaincode container.

## Kubernetes cluster expiration
{: #ibp-console-manage-console-cluster-expiration}

If you are using a free {{site.data.keyword.cloud_notm}} Kubernetes Service cluster, it will expire after 30 days. When this happens, you can no longer access your console. All of the associated nodes and certificates are deleted as well. You can have only one free cluster at a time. Therefore, before you can create another blockchain service instance, you need to ensure that the expired cluster and its associated service instance have been deleted from {{site.data.keyword.cloud_notm}}. To confirm if they have already been deleted or to manually delete these resources, follow these steps:

1. In your {{site.data.keyword.cloud_notm}} dashboard, click the hamburger menu and open the **Resource List**.
2. Scroll to the **Services** twistie and expand it to view your service instance.
3. If your instance is listed, click the action menu for the service instance, then **Delete**, to delete this service instance.
4. Scroll to the **Kubernetes Clusters** twistie and expand it to view your free cluster.
5. If your free cluster is listed, click the action menu for the cluster and then click **Delete** to delete the free cluster.

When these actions are complete, you can follow the [original steps](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks) to create a new Kubernetes cluster and blockchain service instance from the IBM Cloud catalog page.

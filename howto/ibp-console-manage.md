---

copyright:
  years: 2019
lastupdated: "2019-02-08"

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

***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

There are various actions you can take to manage your console behavior. This topic describes actions outside of the blockchain node operations that aid you in your day-to-day usage of the console.
{:shortdesc}

## Add and remove users from the console

This {{site.data.keyword.blockchainfull}} Platform console is provisioned with the email address of the {{site.data.keyword.IBM_notm}} owner as an administrator of the console. The administrator can then grant other users to access the console.

Click **Users** in the left navigation to grant access to new users to the console via their email address. Click **Add new users** and specify a list of email addresses you want to authorize along with their user type: `Admin` or `General`. Users with the `Admin` type can add new users or delete existing users and view console logs. `General` users can log in to the console but cannot add or remove users or view the logs.


The users listed on this panel are simply email addresses of users who can log in to the console. They have no relationship to the **Available Organizations** in the Organizations tab or the identities stored by the console wallet.
{:note}

## Update the administrator e-mail address

If the console is used by multiple people or organizations, it is recommended that you create functional email address to access your network. Using a functional email allows you to access the console if the original admin leaves your organization or has their email address suspended. If it is not possible to create a functional email, you can provide `Admin` access to multiple users to avoid a dependency on a single individual.

In order to update the email address of the console administrator that was configured when the console was deployed you must be logged in as a console administrator. Navigate to the **Users** tab and click **Configure** on the **IBM ID** tile. In the panel which opens specify a new email address for the console administrator.


## Viewing your console logs

You can easily access the console logs if you need to debug problems that you encounter when you use the console or operate your nodes. You can also increase or decrease the amount of logs collected by the console by setting the logging level. The logs of the console are collected separately from the [logs of your nodes](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), which are collected by the {{site.data.keyword.IBM_notm}} Kubernetes service.

Navigate to the **Settings** tab in the console browser to change the logging settings. The console logs are collected from two separate sources:

  * **Client logging:** These logs are collected when commands are sent from your browser to the console.
  * **Server logging:** These logs are collected when the console sends commands to your nodes and from the console deployment. These logs include the Hyperledger Fabric logging output.

Set the amount of logs collected by using the drop-down list under each log type. For example, **Error** and **Warning** collect the least amount of logs, while **Debug** and **All** collect the most.

You can only view the console logs if you are logged in as a console administrator. To view the logs from the **Settings** tab, replace the word `settings` in the browser URL with `api/v1/logs`. For example, if your console url is `localhost:3001/settings`, you can view your logs by navigating to `localhost:3001/api/v1/logs`. Client and server logs are collected in separate files. The most recent logs are at the top of the page.


## Viewing your node logs
{: #ibp-console-manage-console-node-logs}

The logs of your your peers, orderers and Certificate Authorities are collected by the {{site.data.keyword.IBM_notm}} Kubernetes service. Use the steps below to view the logs of your nodes from the cluster where you deployed your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network.

1. Open the [{{site.data.keyword.cloud_notm}} dashboard ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/apps/) and navigate to the overview screen of your {{site.data.keyword.IBM_notm}} Kubernetes service cluster.
2. Above the cluster overview screen, click **Kubernetes Dashboard**.
3. Inside the Kubernetes dashboard, use the left navigation Namespace drop-down list to change the namespace to the {{site.data.keyword.blockchainfull_notm}} Platform service instance. The service instance name will be a long string of letters and numbers. You can find your service instance name at the beginning of the URL of your {{site.data.keyword.blockchainfull_notm}} Platform console.
4. On the left navigation, click **Pods** to view the list of node pods that you have deployed.
5. Click a pod. Then click **View logs** on the top menu to open the logs of your node. Above the logs, you can use the dropdown menu after **Logs from** to view the logs from the different containers within the pod. For example, your peer and the state database (CouchDB for example) run in different containers and generate different logs.

By default, the logs of your nodes are collected locally within your cluster. You can also use the {{site.data.keyword.cloud_notm}} Log Analysis service or a third party service to collect, store and analyze the logs from your network. For information, see [Logging and monitoring for the {{site.data.keyword.IBM_notm}} Kubernetes service ![External link icon](../images/external_link.svg "External link icon")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Logging and Monitoring for the {{site.data.keyword.IBM_notm}} Kubernetes service").


## Monitoring resource usage

When the {{site.data.keyword.cloud_notm}} Platform nodes are deployed, they are pre-configured with default CPU, memory, and storage settings and these values cannot be modified. You can monitor your resource usage by using the {{site.data.keyword.IBM_notm}} Kubernetes Service dashboard. If you attempt to create a new node in the {{site.data.keyword.cloud_notm}} Platform console and get an error that you need to expand your Kubernetes cluster, it is possible to add more storage to the Kubernetes cluster. See this [information ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Changing the size and IOPS of your existing storage device") on how to increase storage capacity or performance of your existing volume.

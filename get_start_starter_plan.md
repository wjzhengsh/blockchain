---

copyright:
  years: 2018
lastupdated: "2018-05-23"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Govern Starter Plan network
{: #getting-started-with-starter-plan}

{{site.data.keyword.blockchainfull}} Platform Starter Plan offers you a pre-configured blockchain network with a single click. It <!--offers you a free trial of 30 days and -->provisions a permissioned network with the configuration of two [organizations](glossary.html#organization), one [peer](glossary.html#peer) per each organization, and one [channel](glossary.html#channel) by default. After the network is created, you can scale and add more organizations and peers to your network. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**Notes**:
1. {{site.data.keyword.blockchainfull}} Platform Starter Plan is a development and testing environment, and is not suitable for production workloads. If you need a production environment, see [About Enterprise Plan](enterprise_plan.html).
2. Starter Plan is at beta stage now and is available in only the **US South** region in {{site.data.keyword.cloud_notm}}.

Starter Plan enables you to learn and develop skills with {{site.data.keyword.blockchainfull_notm}} Platform, run sample applications, test your own applications, and simulate a multi-organization scenario.  This getting started tutorial introduces the prerequisites and steps you need to follow to create and use a Starter Plan network.

If you are new to {{site.data.keyword.blockchainfull_notm}} Platform and blockchain, you can read the [Glossary](glossary.html) to get familiar with the terms in this documentation and the [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html) to learn more about blockchain.


## Creating a network
You can get a Starter Plan [network](glossary.html#network) with the default configuration immediately after you create an {{site.data.keyword.blockchainfull_notm}} Platform service instance.

1. Locate the [Blockchain service ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain) in {{site.data.keyword.cloud_notm}} Catalog.   
    **Note**: You need to log in with your {{site.data.keyword.cloud_notm}} paid account. If you do not have an account, click the **Sign up to Create** button. After you create a free trial account, upgrade it to a **Pay-As-You-Go** type by going to **Manage** > **Billing and Usage** > **Billing** in the {{site.data.keyword.cloud_notm}} console, and clicking **Add Credit Card**.
2. Ensure that you choose **US South** as the region in {{site.data.keyword.cloud_notm}}.
3. Select your Cloud Foundry organization and space, where you will create your network.
4. Choose **Starter Membership Plan** from the pricing plans table.
5. Click the **Create** button. Note that if you are invited to join a network, you can see a pop-up welcome panel. To create a network, choose **Proceed to your network** and click **Continue**. To join a network, see step 5 in [Joining a network](#joining-a-network).
  Now you're ready to use your Starter Plan network with the default configuration. The network is running with one orderer (known as a "SOLO" ordering service), two organizations, one CA, and one peer per each organization. A default channel is also created.
6. Click the **Launch** button.

You can find your blockchain service instance in your [{{site.data.keyword.Bluemix_notm}} service dashboard ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} service dashboard").


## Inviting members
You can invite other [organizations](glossary.html#organization) to join your Starter Plan network as [members](glossary.html#member) so that you can perform [transactions](glossary.html#transaction) with each other. In addition, if you want to use Starter Plan for learning and testing purposes, you can simulate a multi-organization network by adding members for yourself to the network.

1. On the "Members" screen of your Network Monitor, click the **Invite Members** button.
2. The "Invite member" window opens.
    - If you want to invite another organization, choose "Invite a member".  Specify the name and operator email address of the organization that you want to invite.  You can also enter additional information that you want to include in your invitation in the "Add a note" field.  Click the **Send invite** button.  The invited organization will receive an invitation email and can then follow the instructions in the email to join your network.
    - If you want to add additional organizations that can be added to a channel, choose "Add a member".  Specify a name for your new organization. You can optionally add peers to your new organization or do it later in the Network Monitor.  Click the **Create** button. Note that if you add peers for your new organization, you need to switch to this new organization to see your peers. For more information about switching organizations, see [Switch organizations](dashboard.html#switch-organizations).


## Joining a network
If you are invited by a network initiator, you will receive an email notification with instructions on how to join the network. Follow the instructions in the email, and you will become one of the members in the network.

You need to create a [{{site.data.keyword.blockchain}} Platform service instance ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain) with Starter Plan membership option in {{site.data.keyword.cloud_notm}}.

1. Log in with your {{site.data.keyword.cloud_notm}} account. If you do not have an account, click the **Sign up to Create** button.
2. Select the Cloud Foundry organization where you want to store your {{site.data.keyword.blockchain}} network.
3. Select **Starter Membership Plan** from the pricing plans table.
4. Click the **Create** button. The service instance page opens with a pop-up welcome panel. Note that you can choose to join a network or continue to create your own one. To create a network, see step 4 in [Creating a network](#creating-a-network).
5. On the welcome panel, choose **Join existing network**, select the network to join from the drop-down list, and click **Continue**.

You can find your blockchain service instance in the [{{site.data.keyword.cloud_notm}} service dashboard ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} service dashboard").

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
-->


## Retrieving network credentials and connection profile
After you create a Starter Plan network in {{site.data.keyword.cloud_notm}}, you can retrieve the network credentials and connection profile from the service instance page or in the Network Monitor.

### Retrieving from the service instance page
You are on the service instance page right after you create a service instance. You can also click your service in the [{{site.data.keyword.cloud_notm}} service dashboard ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} service dashboard") to open your service instance page.

Retrieve your service credentials with the following steps:
1. On the service instance page, click **Service credentials** in the left navigator to show the "Service credentials" screen.
2. Click **New credential** in the "Service credentials" screen.
3. In the "Add new credential" screen, Give the credential a name and click **Add**. The new credential is added in the table. You can click **View credentials** under the "ACTIONS" column to view the credential details. This credential contains the API key and secret, which you can use to authorize APIs.  
    If you want to see the connection profile of your network, enter **{"legacy": true}** as inline configuration parameter when you create new credentials. The connection profile contains API endpoints for your network resources, which you can use in your APIs and applications.

![Retrieving network credentials](images/service_credentials.gif "Retrieving network credentials")

### Retrieving in the Network Monitor
You can find the network credentials on the "APIs" screen in your Network Monitor. For more information about using APIs, see [Interacting with the network using Swagger APIs](howto/swagger_apis.html).

You can retrieve the connection profile on the "Overview" screen in your Network Monitor. Click the **Connection Profile** button on the "Overview" screen and the connection profile shows in a new page.


## Deploying sample applications
Starter Plan enables you to deploy sample applications to your network with only a few clicks. With the samples, you can easily learn how chaincode and applications work in a network.

For more information, see [Deploying sample applications](howto/prebuilt_samples.html).


## Developing and deploying customized business networks
Starter Plan integrates IBM Blockchain Platform: Develop developer environment and Hyperledger Composer developer toolset. You can develop your blockchain network based on your business needs.  After you develop a network for your business, you can deploy your business network to Starter Plan network.

For more information, see [Develop the network](develop.html) and [Deploying business networks with Starter Plan](develop_starter.html).


## Monitoring network resources
If your application requests a transaction, you can view transaction status information in the Network Monitor. For more information about network monitoring, see [Monitoring a network](howto/monitor_network.html).


## Resetting a network
If you want to clean your customized configurations, running chaincode, or deployed applications, you can set your network back to the initial default configuration.  For more information, see [Reset network](dashboard.html#reset-network).


## Migrating from Starter Plan to Enterprise Plan
{: #migrate}

You can deploy a `.bna`, chaincode, and applications that you test against a Starter Plan network into an Enterprise plan network.

If you have your business network archive file (`.bna`), follow the instructions for [deploying a business network to Enterprise Plan](./develop_enterprise.html). If you do not have your `.bna` file, use the `composer network download` command to retrieve it from the Starter Plan instance. For more information about the `composer network download` command, see [Hyperledger Composer command line documentation ![External link icon](images/external_link.svg "External link icon")](https://hyperledger.github.io/composer/reference/commands){:new_window}.

Chaincode, which is similar to `.bna` files, is developed externally. To deploy a chaincode that you test against a Starter Plan network into Enterprise, follow the instructions in [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html#installchaincode).

As you can see in [Deploying sample applications](howto/prebuilt_samples.html), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](howto/prebuilt_samples.html#deploy_sample_applications_manually).


## Deleting or leaving a network
If you want to delete or leave a network, you can delete the blockchain service instance from your {{site.data.keyword.cloud_notm}} dashboard.

**Note**: Before you leave a network, ensure that you are not a member in any channels of the network. Otherwise, you will get errors when you leave the network. A channel member removal needs to complete the channel update process. For more information about the channel update process, see [Updating a channel](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

---

copyright:
  years: 2017
lastupdated: "2017-12-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Govern the network
{: #getting-started-with-blockchain}

The {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}} provides a {{site.data.keyword.blockchain}} network with high security, integrity, scalability, and performance. You can quickly provision a fully functional {{site.data.keyword.blockchain}} network and use the Network Monitor to immediately run chaincode and applications without having to design and configure a private blockchain network. 
{:shortdesc}
 
This getting started tutorial introduces the prequisites and steps you'll need to follow to get a {{site.data.keyword.blockchain}} network hosted on IBM's highly available and secure environment.  

The following steps indicates the basic flow to launch a {{site.data.keyword.blockchain}} network with multiple member organizations:
1. A **network initiator**, as one special type of network member, creates the {{site.data.keyword.blockchain}} network and defines governance policies. The network initiator can then invite other institutions to join this {{site.data.keyword.blockchain}} network as network members.  For more details, see [Creating a network](#creating-a-network).
2. **Network members** receive an email notification that provides instructions for them to join a {{site.data.keyword.blockchain}} network. Besides the instructions in the email notification, you can also follow the steps in [Joining a network](#joining-a-network).
3. All **network members**, after creating or joining a network, can enter the Network Monitor, which is a GUI dashboard, to configure and manage their network resources. You can set up channels with a group of network members to execute private transactions on a channel-specific ledger, which only channel members can access. In the Network Monitor, you can also join your own peers to the channel and install and instantiate chaincode on them. For more details, see [Configuring network resources and environment](#configuring-network-resources-and-environment).
4. **Application developers** create applications that enable interaction with the {{site.data.keyword.blockchain}} network. For more information, see [Enabling applications to interact with the network](#enabling-applications-to-interact-with-the-network).
5. **Network operators** monitor transactions on their channels using the Network Monitor. For more details, see [Monitoring network resources](#monitoring-network-resources).

## Creating a network
Before you begin, you need to create a [{{site.data.keyword.blockchain}} Platform service instance ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain) on {{site.data.keyword.Bluemix_notm}}. You'll need to log in with your {{site.data.keyword.Bluemix_notm}} ID. If you do not have an ID, click the **Sign up to Create** button.  Rename the service and credential names for your instance so you can recognize it easily in the future. Select the {{site.data.keyword.Bluemix_notm}} region, organization, and space, where you can deploy your {{site.data.keyword.blockchain}} network. Then select **Enterprise Plan** from the pricing plans table and click the **Create** button.  

You can find your {{site.data.keyword.blockchain}} Platform service instance in your [{{site.data.keyword.Bluemix_notm}} service dashboard ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} service dashboard").  

If you are a network initiator, click the **Create Network** button to initiate a {{site.data.keyword.blockchain}} network.  Follow the wizard to complete the basic configuration of your network and resources.  
![Create Network wizard](images/create_network_name.png "Create Network wizard")  

1. In the "Let's Get Started" screen, give a name to your network, choose the location of your {{site.data.keyword.Bluemix_notm}} organization, and add the name of your institution. When you invite other network members, they will look for this network name to join. Click **Next**.
2. (Optional) In the "Invite Members" screen, enter the institution name and the email address of the member you want to invite to your network. The institution name you designate is not an official title. It simply allows the institution to be easily recognized and can be changed when they join the network. Note that a network can have up to 15 members including yourself. This step is optional, and you can invite members to your network later in the Network Monitor.  Click **Next**.
	Members that you invite will receive an email notification about your invitation after you complete all of the steps to create the network.
3. In the "Define Governance Rules" screen, establish the policies for membership, channel creation, and chaincode. By default, all network members can invite other members to join the network, create channels, and instantiate chaincode. For this GA, your network uses the default governance policies.  Click **Next**.
4. In the "Review Summary" screen, verify your network configuration. If you want to make modifications, click **Edit** beside the section header or click the **Previous** button to go back to previous screens. When you complete the network configuration, click **Done**.  
5. In the "Network Created" screen, you will be notified that your network has been successfully created. You can click **Add Peers** to configure your network resources or click **Enter Monitor** directly to open the Network Monitor. If you don't add peers first, they can be added later in the Network Monitor.  For more information about peers, see [Overview](v10_dashboard.html#overview).
    
Now you're ready to deploy a {{site.data.keyword.blockchain}} network that can support the following network resources:  
* A member-specific certificate authority (CA)
* Default governance policies
* Up to 15 network members  
* Three orderers and two intermediate CA nodes
* Up to three small peers for each member  
* A crash fault tolerant ordering service
* Up to 150 channels
* Up to 10 instantiations of chaincodes


## Joining a network
Similar to creating a network, you need to create a [{{site.data.keyword.blockchain}} Platform service instance ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain) on {{site.data.keyword.Bluemix_notm}}. You'll need to log in with your {{site.data.keyword.Bluemix_notm}} ID. If you do not have an ID, click the **Sign up to Create** button.  Rename the service and credential names for your instance so you can recognize it easily in the future. Select the {{site.data.keyword.Bluemix_notm}} region, organization, and space, where you can deploy your {{site.data.keyword.blockchain}} network. Then select **Enterprise Plan** from the pricing plans table and click the **Create** button.  

You'll be able to find your {{site.data.keyword.blockchain}} Platform service instance in the [{{site.data.keyword.Bluemix_notm}} service dashboard ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} service dashboard"). 

If you are an invited network member, click the **Pending Invite ->** button, select the network you want to join from the drop down list, and click the **Join Network!** button. Follow the wizard to view the network basic configuration of your network and configure your own network components.  
![Join Network wizard](images/join_network_name.png "Join Network wizard")  

1. In the "Let's Get Started" screen, add the name of your institution and verify the location of your {{site.data.keyword.Bluemix_notm}} organization. Click **Next**.
2. In the "Review Governance Rules" screen, view the network's governance policies of membership, channel creation, and chaincode. Click **Next**.
3. (Optional) In the "Add Peers" screen, choose the size and quantity of peers that you want to add. Click **Next**. Each member in a network can add up to 3 peers; at this point in time only "small" peers are available. This step is optional, and you can add your peers later in the Network Monitor. For more information about peers, see [Overview](v10_dashboard.html#overview).
4. In the "Review Network Summary" screen, verify the network configuration. If you want to make modifications, click **Edit** besides the section header or click the **Previous** button to go back to previous screens. When you complete the resource configuration, Click **Done**. You will be notified that you have joined the network successfully. You can then click **Enter Monitor** to open the Network Monitor or click **Create a Channel** to initiate a channel creation request. You can create channels later in the Network Monitor. For more information, see [Channels](v10_dashboard.html#channels).
 

## Configuring network resources and environment

1. Enter your Network Monitor after you create or join a {{site.data.keyword.blockchain}} network. The Network Monitor is a GUI dashboard where you can manage and keep track of network status information. For more information, see [Network monitor](v10_dashboard.html).
2. Add your own peers to the network. If you have already added enough peers, skip this step. Peers run chaincode and they are the endpoint to interact with your applications. Click **Add Peers** in the "Overview" screen, and select the quantity and size of your peers. For more information, see [Overview](v10_dashboard.html#resources).
3. Set up a channel. All members in the same channel are provisioned a channel-specific ledger, which delivers data isolation and confidentiality. For information on how to create a channel, see [Creating a channel](howto/create_channel.html#creating-a-channel).  
	
	If you are a channel member who has been invited to join a channel, you will receive an email notification with a link to the wizard that will allow you to join the channel. 
4. Join peers to the channel.  Only peers that are associated with the channel can access its ledger. For more information, see [Channels](v10_dashboard.html#channels).
5. Install and instantiate chaincode. All channel members need to install the same chaincode with the same name and version on every peer that will run the chaincode. After being installed, chaincode must be instantiated on the channel before it can be used. For more information, see [Installing and instantiating a chaincode](howto/install_instantiate_chaincode.html).  

**Note**: To achieve high availability, each institution must purchase at least 2 peers, and within a channel, each participanting institution must join at least 2 peers.

## Enabling applications to interact with the network
Applications leverage the SDK APIs to interact with your {{site.data.keyword.blockchain}} network components. You need to add the API endpoint information of your network components in your application so that the application can ultimately target your peers with transaction requests. You can then add the API endpoint information from the Network Monitor. Applications can be hosted on your local file system or on {{site.data.keyword.Bluemix_notm}}. For more information, see [Developing applications](v10_application.html).

## Monitoring network resources  
After a transaction has been triggered from your application, you can view transaction status information in the Network Monitor. For more information about network monitoring, see [Monitoring a network](howto/monitor_network.html).
  
## Leaving a network 
If you want to leave a network, delete the blockchain service instance from your {{site.data.keyword.Bluemix_notm}} dashboard.  

**Note**: Before you leave a network, ensure that you are not a member in any channels of the network. Otherwise, you will get errors when you leave the network. A channel member removal needs to complete the channel update process. For more information about the channel update process, see [Updating a channel](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.0, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

---

copyright:
  years: 2017
lastupdated: "2017-08-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

**ATTENTION:** Before using a {{site.data.keyword.blockchainfull}} offering, read the technical and support information in the [Disclaimer](needtoknow.html) section.  
{:shortdesc}

The {{site.data.keyword.blockchainfull_notm}} Platform is the only integrated business ready platform that addresses the full life cycle (**develop**, **govern**, and **operate**) of a multi-organization blockchain network. It is designed to accelerate, through collaboration in each phase, the creation of "built for business" global blockchain networks with the performance and security for even the most demanding use cases and regulated industries. {{site.data.keyword.blockchainfull_notm}} offerings are built on the Hyperledger Fabric V1.0 code base leveraging a modular architecture to achieve enterprise levels of security, data integrity, scalability, and performance to meet your business needs.  

Let's take a look at the primary activites and roles in an {{site.data.keyword.blockchainfull_notm}} network, and see how they fit into a network's lifecycle.  

**Note**: The table below is not meant to represent a linear progression. Tasks such as development and network management will occur repeatedly as applications and membership evolve.

|  Function     | Activities       | Roles  |
| ------------------------- |--------------------------|-----|
| [Develop ![External link icon](images/external_link.svg "External link icon")](https://developer.ibm.com/blockchain/sandbox/) | Develop Applications, Develop Chaincode | Application Developers |
| [Govern](get_start.html)| Invite Members, Generate Credentials, Propose Governance Models, New members join, Disseminate certs & endpoint info | Network Operators, Network Members |
| [Operate](v10_dashboard.html)| Monitor health & activity, Manage new deployments, Manage members (addition/removal), Manage chaincode lifecycle,  Manage channels, Support | Network Operators, Network Participants |
  
Now let's take a closer look at these roles and activities.

## **Develop** the network
Explore and accelerate blockchain development by leveraging best practices honed across 400+ client engagements to:
* Ensure close alignment across businesses with technology that significantly reduces blockchain application development time (applications that would otherwise take six weeks to develop can be created in less than two days).
* Quickly build blockchain skills within your existing roster of programmers by leveraging {{site.data.keyword.blockchainfull_notm}} developer tools.
* Give developers the flexibility to learn and develop in their preferred environment with an open and modern toolset.  
  
As a business owner, you can develop your use case with help from IBM's deep bench of industry and blockchain experts who come together in the {{site.data.keyword.blockchainfull_notm}} Garage to harness the full power of the IBM Blockchain Platform. 

As a developer, you can quickly and easily align business requirements and accelerate blockchain application development for free with a cloud sandbox and interactive playground that turns any programmer into a blockchain developer. These tools are designed to turn business rules into code in your preferred environment:
* **Explore online**  
  Leverage [Hyperledger Composer ![External link icon](images/external_link.svg "External link icon")](https://hyperledger.github.io/composer/introduction/introduction.html), which is an open source development tool to learn key blockchain concepts, to create network definitions, and to leverage reusable industry models and smart contract libraries. 
* **Install locally**  
  Leverage IBM certified images of Hyperledger Fabric and Composer, which is an open source framework for building a business network, to develop and test directly on your laptop.
* **Collaborate in a cloud environment**  
  Free and fee options to develop and share your code with others.

## **Govern** the network
There are two options for creating the backend environment of your network. First, you can use the published Hyperledger Docker images, giving you the option of implementing the Composer library to build your applications and interact with your network. Or, you can write chaincode natively and develop the server side code to drive transactions. Running locally is the perfect opportunity to tinker with network configurations, explore potential use cases, and start building out proofs of concept. As your PoC begins to take shape, you'll be able to broaden the implementation by hosting your network in the cloud.

With a cloud deployment you're provided with a collection of easy-to-use recipes and scripts to facilitate the deployment of a Hyperledger Fabric network running on Kubernetes. Use this phase to examine the behavior and stability of your PoC in a hosted environment. The [{{site.data.keyword.blockchainfull_notm}} Container Service ![External link icon](images/external_link.svg "External link icon")](https://ibm-blockchain.github.io/) can be best thought of as a testing mechanism for the functionality and resiliency of your application and as a natural precursor to the Enterprise Plan.

After you have a network, {{site.data.keyword.blockchainfull_notm}} has been designed to create a network management experience that offers members some control, while no single member is in complete control. The {{site.data.keyword.blockchainfull_notm}} Platform has the first set of integrated tools to allow teams to enforce change management of the network via customizable policies. 

The following list shows key features of this governance model:

* Democratic management tools allowing for collective management.
* Policy Editor to define flexible, democratic policies to govern changes to the network.
* Pre-built tools and policies enable faster onboarding, customization, and activation.
* A multi-party workflow tool with integrated notifications, a member activities panel, and secure signature collection.

## **Operate** the network
Deploy and operate decentralized networks with a production ready secure service. Start small and scale your network elastically as your membership and transaction volumes increase by taking advantage of the following features:

* An ultra high security environment with many hardware, firmware, and software security features.
* Hardened architecture for scalability, resiliency, and availability.
* Optimized for performance and running on the world's fastest Linux compute.
  
Operating your network on the {{site.data.keyword.blockchainfull_notm}} Platform includes tools and capabilities to simplify administrative tasks:

* Dashboards for monitoring and managing the resources on the network. 
* Lifecycle management for seamless upgrades of the full code stack.
* 24/7 technical support integrated into the portal.
* Hardened security stack with no privileged access, malware and tamper resistance, 100% encryption, and many more features for networks with sensitive data in regulated industries.

## Roles

Form your consortium and launch a network using this fully managed blockchain-as-a-service offering.  Invite institutions to join your network and propose governance policies.  Before taking a deeper dive into the prominent fetures of the {{site.data.keyword.blockchainfull_notm}} Platform, let's take a quick look at the key personas and entities in more detail. Please refer back to the table at the top of the page to see the responsibilities for each.

A **Participant** is anyone that interacts or is involved with the blockchain network. This could be the front end developer who creates applications that front the network or the institution who runs key components, including peers, Certificate Authorities (CA), and ordering service. There are two major roles in a blockchain network, maintainers and participants. Below you find out more information on these roles and people required to complete the activities of these roles.

1. **Maintainers**  
  Typically aligned with an institution who owns the central components of the blockchain infrastructure, including peer nodes, CA, and so on. They share in the governance and operational duties of the network. All maintainers have a stake in the network, and by extension, have a vote in any operations that configure and customize their stance in the network. There are two types of maintainers, initiators and operators.

   * **Initiator** – While the initiator is designated as a “member” like any follow-on institution, the operator for this institution will have the key distinction of actually starting the network. This is a one time process per network, which involves inviting fellow members to the network and proposing network governance implementations that the invitees might subsequently agree to or reject. This process can be accomplished through the {{site.data.keyword.blockchainfull_notm}} service with an easy-to-use GUI. With this service, {{site.data.keyword.IBM_notm}} provides the framework to quickly bootstrap a network and enable a governance policy, in which all original members can serve as operators for their institutions. This means that all operators have the necessary permissions to generate certificates and to issue proposals for policies, addition or removal of network members, channel creation, and chaincode deployment. The network initiator might or might not be either a network member or an operator.
   
   * **Operator** – Operators have the responsibility of maintaining the network by monitoring the stability and behavior of the network, and managing their institution’s peers. These tasks include operations such as issuing network proposals (e.g. channel configuration change, removing member, etc.), responding to proposals from fellow members, and managing chaincode lifecycle (install, instantiate, upgrade, remove). {{site.data.keyword.IBM_notm}} provides a Network Monitor to easily complete network activites, such as adding members, setting policies, monitoring and managing chaincode, and so on.  

2. **Participants**  
  These are the individuals or entities that will leverage the network resources to drive transactions and ultimately "use" the network. Most of this use will come from interaction with the apps provided by the application developer.

    * **Application Developer** - This role is strictly an individual who creates an application for a blockchain network. Application developers will typically work out-of-band with solution architects in order to structure apps that satisfy the needs of the entire consortium or a subset of the consortium. They will not have concern regarding the network.

    * **End User** - Primary role is the interaction with applications created by the application developers. It is quite possible that an end user will have zero knowledge of the network infrastructure and implementation. Their only concern, in all likelihood, is the usability and consumption of a front-end UI driving calls to peers on the network. 

## The {{site.data.keyword.blockchainfull_notm}} Platform

The {{site.data.keyword.blockchainfull_notm}} Platform provides a highly secure and permissioned blockchain network upon which authenticated members can easily define assets and create the business solutions for modifying and exchanging them.  No longer is it a tedious and complicated process to bootstrap a production-grade blockchain network. With the The {{site.data.keyword.blockchainfull_notm}} Platform you can take advantage of an orchestration framework that allows you to **organize your consortium into an active blockchain network in record time**. We provide consortium friendly tooling designed to make it simple for multiple institutions to join together and create a democratically governed network. The creation, governance and management tasks of the network become intuitive and transparent through a built-in dashboard monitor and provisioned utilities. By foregoing the otherwise cumbersome process of creating a network and implementing governance, consortium members can instead **focus on the deployment of smart contracts and transfer of assets and information**.      
{:shortdesc}

**High availability** for the integral components of the network (peers, ordering service, Certificate Authority, chaincode) eliminates the crippling effects that can arise from single points of failure. A built-in dashboard monitor allows for easy management of these components and provides a powerful mechanism to visualize assets and smart contracts.

The **modularity** of the Hyperledger Fabric V1.0 architecture and the distinct separation of network roles provides an infrastructure enabling scalability and adaptability to a wide variety of use cases. 

The checks and balances that occur throughout the lifecycle of a transaction ensure consistent and fully vetted results; and ledgers constantly stay synchronized through an implementation of the well-known gossip protocol. Identity and access control are easily enforced through **sign/verify** operations that occur perpetually throughout the network.  

**Governance tooling** is provided to allow members to administer and manage the critical business rules for their network. For example, you may wish to implement a policy defining how many members of a network must agree in order for a new member to join. Or perhaps there is an asset that requires endorsement from every participant in order for a modification to take place. Rules of governance are fundamental for any type of business network and are often extremely elaborate. Governance tooling (e.g. policy editors) greatly simplifies this process.

The service runs in a **highly-secure and isolated** environment with no external access (including root access) to network components. Data is encrypted in flight and at rest, and hardware security support modules allow digital keys to be protected in accordance with industry regulations. Hardware virtualisation is used to run each node in an isolated environment, thereby protecting other nodes in the network from peers with potentially misbehaving or malicious chaincode. Hashing, sign/verify operations, and component-to-component communications are accelerated thanks to advanced cryptography implementations.

Before continuing, let's take a look at a simple configuration within the The {{site.data.keyword.blockchainfull_notm}} Platform.  **Figure 1** depicts an example of a deployed blockchain network consisting of four members (each owning two peers), a Certificate Authority responsible for distributing cryptographic identity material, and an ordering service defining policies and network participants. The blue channel contains all four network members, whereas the yellow channel is restricted to only three members: Banks B, C and D. We also see that Bank A played the role of network initiator and that Bank D exists only as a member in the context of the yellow channel. Lastly, an end user or application in possession of a properly signed x509 certificate is able to send calls to peers on the network. As mentioned earlier, it's quite conceivable that an end user won't even know about the existence of the blockchain network.

![Blockchain Network](images/blockchain_network_2-01.png "Example blockchain network")

*Figure 2. An example blockchain network consisting of four members leveraging channels to isolate data*

## {{site.data.keyword.IBM_notm}} Membership Offerings

[Membership options](enterprise_plan.md) currently available.

Below is a summary of current and future options:

|       | Entry      | [Enterprise](enterprise_plan.md)  | Enterprise Plus | Self Managed
| ------------------------- |--------------------------|-----|-----|------|
| **What's Included** | Basic service levels | **Advanced service levels, enterprise production ready** | Dedicated compute for performance and isolation | Software stack to install on your own infrastructure + connect to a network hosted on this cloud service |
| **Billing Policy** | Hourly charge | **Monthly subscription** | Monthly subscription | Monthly subscription |
| **Availability** | Coming soon | **Available now** | Coming soon | Planned for 2018 |

Sign up today for your {{site.data.keyword.IBM_notm}} [{{site.data.keyword.blockchainfull_notm}} membership ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

## {{site.data.keyword.IBM_notm}} Support 

{{site.data.keyword.IBM_notm}} offers support on {{site.data.keyword.IBM_notm}} implemented {{site.data.keyword.blockchain}} solutions. Access the {{site.data.keyword.blockchainfull_notm}} Support details through the [{{site.data.keyword.blockchainfull_notm}} DockerHub ![External link icon](images/external_link.svg "External link icon")](https://hub.docker.com/u/ibmblockchain/) and explore the support engagements available.

For complete details on all Hyperledger Fabric v1.0 features and functionality,
see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/).

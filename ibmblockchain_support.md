---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-30"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Getting support
{: #ibmblockchain_support}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


There are several mechanisms available to obtain support and troubleshoot problems that are associated with your blockchain instance on {{site.data.keyword.Bluemix}}.
{:shortdesc}


## Resources and support forums
{: #resources}

Navigate to the "Support" screen in your Network Monitor as shown in Figure 1. This page contains links to resources that you can use to troubleshoot problems and get help from {{site.data.keyword.IBM_notm}} and the Fabric community. Refer to the list below for descriptions of the resources on the "support" screen and guidance on how they can help.

![Support screen](images/support.png "Support screen")
*Figure 1. Support screen*


**Getting Started**
* [{{site.data.keyword.blockchainfull_notm}} Service docs](index.html) under **Getting started**, which is this doc site, provides guidance on how to start with {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}}. You can find corresponding topics from the left navigator or search any term with the search function on the top.

**Support**
* [IBM dWAnswers ![External link icon](images/external_link.svg "External link icon")](https://developer.ibm.com/answers/smartspace/blockchain/) under **Support ticket** is a community forum for users of the {{site.data.keyword.blockchainfull}} Platform and Hyperledger Fabric that is monitored by IBM experts. You can search for answers to previously posed questions or submit a new question. Be sure to include the keyword **blockchain** in your question.

  You can also submit a ticket to {{site.data.keyword.blockchainfull_notm}} support team with the **Open an {{site.data.keyword.Bluemix_notm}} support ticket** option.  Share details and code snippets from your specific {{site.data.keyword.Bluemix_notm}} instance.

**Sample Applications**
* [Sample applications ![External link icon](images/external_link.svg "External link icon")](https://github.com/ibm-blockchain) under **Blockchain sample applications** provides guidance and sample code
snippets to assist in the development of applications.
* [Blockchain on IBM Developer ![External link icon](images/external_link.svg "External link icon")](https://developer.ibm.com/technologies/blockchain/) contains code patterns and information for developers.

**Hyperledger Fabric**
* [Hyperledger Fabric ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/) and [Hyperledger Fabric community ![External link icon](images/external_link.svg "External link icon")](http://jira.hyperledger.org/secure/Dashboard.jspa) provide more details about the Hyperledger Fabric stack. Talk to a [Hyperledger Expert ![External link icon](images/external_link.svg "External link icon")](https://chat.hyperledger.org/channel/general) with questions about the Hyperledger Fabric code.

**Hyperledger Composer**
* [{{site.data.keyword.blockchainfull_notm}} Platform: Develop ![External link icon](images/external_link.svg "External link icon")](https://ibm-blockchain.github.io/develop/)
provides more details about the Hyperledger Composer, that is, {{site.data.keyword.blockchainfull_notm}} Platform: Develop. You can also find answers or raise questions in [Hyperledger Composer community ![External link icon](images/external_link.svg "External link icon")](https://chat.hyperledger.org/channel/general) and [StackOverflow ![External link icon](images/external_link.svg "External link icon")](https://stackoverflow.com/questions/tagged/hyperledger-composer).


## Submitting support cases
{: #support-cases}

If your problem cannot be solved by any of the above routes, you can submit a support case in the [{{site.data.keyword.cloud_notm}} Service Portal ![External link icon](images/external_link.svg "External link icon")](https://ibm.biz/ibmcloudsupport) on all issues that are related to the underlying {{site.data.keyword.blockchainfull_notm}} Platform. If you need help with Hyperledger Fabric chaincode, Hyperledger Composer, or your applications, please make use of the community resources above or engage [{{site.data.keyword.blockchainfull_notm}} Services ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/blockchain/services). IBM recommends using Hyperledger Composer solely for demos and proof-of-concepts. IBM does not provide support for networks using Hyperledger Composer in production, including the Composer CLI, JavaScript APIs, REST server, and Web Playground.

The response to your support case depends on your [{{site.data.keyword.cloud_notm}} support plan](https://console.bluemix.net/docs/get-support/index.html#support-plans).

- If you have purchased **Premium Support** or **Advanced Support**,, you can specify a severity level on your ticket from Sev-1 to Sev-4. Sev-1 is the highest level of severity. Support cases with higher severity levels indicates more urgency and will be responded to more quickly by the support team. For more information, see [{{site.data.keyword.cloud_notm}} support plan offerings](https://console.bluemix.net/docs/get-support/index.html#support-plans).

- If you do not purchase support, your {{site.data.keyword.cloud_notm}} Pay-As-Your-Go or Subscription account comes with a free **Basic Support** plan. In this case, your support case is automatically be registered as a Sev-4 case.

Follow these steps to submit a support case.

1. Log in to [{{site.data.keyword.cloud_notm}} Service Portal ![External link icon](images/external_link.svg "External link icon")](https://ibm.biz/ibmcloudsupport) with your {{site.data.keyword.IBM_notm}} ID.
2. Click the **Get help** avatar in the middle or the **Create a Case** button on the upper right.
3. Fill the **Create Case** form with your information at least for the following fields.  
    - Choose **Technical Support** as your case type.
    - In the **Account** field, select the account that you use to create the blockchain service instance.
    - In the **Service region or environment** field, select the region where you create your service instance.
    - Select **Blockchain** for the **Service Impacted** field.
    - Select **Services** for the **Category** field.
    - In the **Provide the Organization** and **Provide the Space** fields, specify your Cloud Foundry organization and space, in which you create the blockchain service instance.  You can find this information from the {{site.data.keyword.cloud_notm}} console.
    - In the **Provide the Resource** field, enter your blockchain network ID. You can find the network ID by clicking the **Connection Profile** button at the upper right of the **Overview** screen in your Network Monitor.
    - In the **Case Summary** field, enter a short summary of your problem.
    - In the **Case Description** field, describe your problem as detailed as possible.  For example, the data center where your network was built and the sequence of steps that led to the problem.  Note that the more details you provide, the more chances that your problem can be resolved quickly.
4. Click the **Submit** button.  You will receive an email notification in a few minutes for this case.


You can find your previously submitted cases by clicking **My Cases** on the upper right in the {{site.data.keyword.cloud_notm}} Service Portal.  Click and open a case to check its status or provide additional information.

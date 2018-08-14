---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Paying mode


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform charges monthly with membership fees and peer fees. Network members can pay their bill with their IBM Cloud accounts that contain the space to create the network instance. Alternatively, one network member can cover the bill for all members in the network and pay for the entire network.
{:shortdesc}

Depending on the network plan that you choose and the amount of resources that you use, your payments are different. For more information about pricing, see [Pricing](pricing.html).

## Prerequisites
Every network member must have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** account. If you don't have an account, [register](https://console.bluemix.net/registration/) one and upgrade it to a paid account. To upgrade your account to a Pay-As-You-Go type, go to **Manage** > **Billing and Usage** > **Billing** in the IBM Cloud console, and click **Add Credit Card**.


## Paying your own fees
After you create any blockchain networks with {{site.data.keyword.blockchainfull_notm}} Platform, your {{site.data.keyword.cloud_notm}} accounts will be charged daily for your membership fee and peer fees.


## Paying for the entire network
One member in the network can cover the fees for all network members.  To pay in this solo-payer mode, payer and other network members must complete the following steps.

1. The payer creates separate Cloud Foundry spaces in {{site.data.keyword.cloud_notm}} for each user who will be invited to the network:
   1. Log in to {{site.data.keyword.cloud_notm}}.
   2. From the menu bar, click **Manage** > **Account** > **Cloud Foundry Orgs**.
   3. Click **View Details** of the Cloud Foundry Org that you will create the network.  If you don't have an Org for the network, click the **Add a new Cloud Foundry Org** button to create one.
   4. Click the **Add a Cloud Foundry Space** button to create a space for one network member.  Select the region for the space, give it a name, and click **Add**.  Note that only the space creator can access the created space.
   5. Repeat step 4 to create separate spaces for all user who will be invited to the network.
2. The payer invites other users to the payer's {{site.data.keyword.cloud_notm}} account and give them access to the specific space.  Each user has access to only one space.
   1. From the menu bar, click **Manage** > **Account** > **Users**.  
   2. Click **Invite Users** and assign user access:
      1. Specify the email address of a single user that you will invite to your Cloud Foundry Org.
      2. In the **Cloud Foundry access** section, make the following selections:
         - **Organization**: The Cloud Foundry Org to create the network.
         - **Organization roles**: Auditor.
         - **Region**: The region where you created the space for this user.
         - **Space**: The space that you created for this user.
         - **Space roles**: Developer.
      3. Click **Invite Users**.
   3. Repeat steps to invite users and assign user access for each user.
3. In the {{site.data.keyword.blockchainfull_notm}} Platform dashboard, the payer invites the users to the blockchain network. For more information about inviting network members, see [Members](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Each user then receives a notification email that contains an invitation to join the network.  The user can complete the following steps to join the network.
   1. Click the "Proceed" button in the notification email, which takes you to the blockchain service page in {{site.data.keyword.cloud_notm}}.
   2. Ensure that you use correct {{site.data.keyword.cloud_notm}} organization and space.
      1. Log in {{site.data.keyword.cloud_notm}} and click the profile avatar on the upper right.
      2. Choose the payer's account from the **Account** drop-down list.  Note that the account and organization where you provision the blockchain service instance will be the one that gets charged.  
   4. On blockchain service page, create your blockchain service instance:
      1. In the **Service Name** field, type in a descriptive name for your future recognition.
      2. Verify that the organization and space are the ones that the payer invited you to.
      3. Select the membership plan with which to create your network instance.
      4. Click **Create**.
   5. After you create the blockchain service instance, follow the wizard to join the network.  For more information, see [Joining a network](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Known limitations
- Because all members are inside of the payers {{site.data.keyword.cloud_notm}} account, the payer has access to all members' blockchain instances and can impersonate them.  Therefore, this paying mode is best used for POC environments, or in cases where the payer is handling all management of the {{site.data.keyword.blockchainfull_notm}} Platform and the members are being provided with only an application.  
- After adding all members to the payer's {{site.data.keyword.cloud_notm}} account and assigning them access to provision blockchain instances and join the network, the payer is also giving the members access to create other services, which could cause additional charges.  
- Any member in the payer's Cloud Foundry Org can see all the spaces in the organization.  However, they cannot edit or modify the organizations because they don't have accesses.

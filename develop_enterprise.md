---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Deploying a business network on Enterprise Plan

{{site.data.keyword.blockchainfull}} Platform: Develop's developer tools help you create a **Business Network Definition** which can then be packaged up into a business network archive (`.bna`). The developer environment allows you to deploy `.bna` files to a local or cloud {{site.data.keyword.blockchain}} for development and sharing.

This guide deals with the next step of a business network lifecycle: activating your business network by deploying the `.bna` to {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

## Before you begin

Before you begin, ensure that you have installed the {{site.data.keyword.blockchainfull}}: Develop developer environment and are comfortable with developing and deploying business networks. Guidance on writing business networks is available in the [Hyperledger Composer documentation](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

You will need access to an Enterprise Plan instance of {{site.data.keyword.blockchainfull_notm}} Platform. For information on the {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan, see the [Enterprise Plan overview](./enterprise_plan.html).

## Step One: Create a connection profile for {{site.data.keyword.blockchainfull_notm}} Platform

1. Create a directory to store your connection details, for example:

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Each connection profile should contain a `connection.json` file. Create a new directory under the `.composer-connection-profiles`, in this instance `bmx-hlfv1`. This will be the name of the profile that you will use when working with Hyperledger Composer and {{site.data.keyword.blockchainfull_notm}} Platform.

        mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1

2. You should now have the following directory structure:

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Create a file in the newly created directory name it `connection.json`. You can use the following template for your `connection.json` file:

        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }

    You will populate the newly created `connection.json` file with attributes that are provided via your {{site.data.keyword.blockchainfull_notm}} Platform dashboard. From your dashboard on, select **Overview**, then  **Service Credentials** button to display the endpoint and certificate information for the members of the channel.

## Step Two: Adding orderer information

1. Now we can start to modify the template with the information provided by the Service Credentials. While there may be multiple orderers in the Service Credentials only one is required for a `connection.json` file.

    Replace the orderer url values in the template with the relevant information from your service credentials in the following format:

        “url”: “grpcs://abca.4.secure.blockchain.ibm.com:12345”

## Step Three: Adding certificate authority information

1. Replace the ca value in the `connection.json` with both the **url** and the **caName** from either of the entries in the **certificateAuthorities** section.

## Step Four: Adding peer information

1. The **requestURL** and **eventURL** for each peer must be set. Replace the **url** attribute with the **url** value found in your service credentials. Replace the **eventURL** attribute with the **eventUrl** found in your Service Credentials. After making the changes, the peers section of `connection.json` should have the following format:

        "peers": [
          {
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"

## Step Five: Adding keyValStore information

1. Next we need to set the **keyValStore** attribute to point to the appropriate directory. Create a directory to use for your **keyValStore**. For example, a new directory under your home directory called `.composer-credentials-mychannel`. Make sure the **keyValStore** attribute points to your newly created directory in the following format:

        "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",

## Step Six: Adding channel information

1. Replace the channel value in the `connection.json` to match the name of the channel which you plan to create and deploy your business network to.

## Step Seven: Adding an mspID

The **mspID** value in your `connection.json` file should be set to the mspID or your organization. The service credentials provide a list of the organizations with their associated mspid values. You should use the value from the **mspid** attribute for your organization.

## Step Eight: Adding the globalCert
1. {{site.data.keyword.blockchainfull_notm}} Platform uses a common TLS certificate for the orderers and peers. For each orderer and peer there is a **tlsCACerts** attribute which all contain the same certificate. Replace the dummy value in the `connection.json` file with the **tlsCACerts** value. It should take the following format:

        "globalCert": "-----BEGIN CERTIFICATE-----\r\.......

## Step Nine: Preparing your peers

**Please Note**: This step **must** be performed before creating the channel to deploy a business network to. If this step is performed after channel creation, a deployed business network **will not start**.

In the service credentials document under **certificateAuthorities** is an attribute **registrar** containing attributes for **enrollId** and **enrollSecret** in the following format:

        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],

1. Request the certificates by using the following command:

        composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12

    This will download 3 files into the `.identityCredentials` directory under your home directory. The files of interest are based on the **enrollId**. So in the above example there will be 2 files called **admin-pub.pem** and **admin-priv.pem**

3. Select **Members** from the navigation menu, then select the **Certificates** menu option and click on the **Add Certificate** button.

4. Enter a unique name for this certificate in the **Name** field, this name cannot include dashes or hyphens.

5. Open the `admin-pub.pem` file created earlier and copy the contents of this file into the **Key** field and press the **Submit** button.

6. Use the user interface stop the peers, then start the peers.

7. The certificate should now appear in the list of certificates.

## Step Ten: Creating your channel

1. Select **Channels** from the navigation menu on the left panel and click the **New Channel** button.

2. Enter a Channel Name (Ensure it matches the name you have specified in your connection profile for the channel attribute) and optional description and click **Next**.

3. Give permissions as required and click **Next**.

4. Select the policy of the number of operators that need to accept channel updates and submit the request.

5. You should now be taken to the **Notifications** section where there should be a new request to review. Click on the **Review Request** button.

6. Click the **Accept** button to accept and you will be taken back to the **Notifications** section where you can see the request can now be submitted. Click the **Submit Request** button to bring up the submission dialog and then click the **Submit** button. The new channel has been created.

7. Select **Channels** from the navigation menu. The new channel in the list of channels and should have “No peers added yet”. Click on the actions menu next to it and select **Join Peers**. Check the check boxes next to the Peers you want to add and press **Add Selected**.

## Step Eleven: Importing a new identity to administer your business network

Next we are going to create an identity in Composer using the certificates requested previously. This new identity will have the authority to install chaincode onto the peers that have your uploaded public certificate and will be an issuer for the certificate authorities.

1. To create the new identity, run the following command:

        composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem

    Where `bmx-hlfv1` is the name of the profile that you previously created. Now we are ready to deploy your `.bna` file to the {{site.data.keyword.blockchainfull_notm}} Platform.


## Step Twelve: Deploying the business network

Now you can deploy your `.bna` file to the {{site.data.keyword.blockchainfull_notm}} Platform.

1. Using the identity created in the previous step, deploy the business network using the following command:

        composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString

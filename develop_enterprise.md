---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-28"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Deploying a business network on Enterprise Plan
{: #deploying-a-business-network}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop's developer tools help you create a **Business Network Definition** which can then be packaged up into a business network archive (`.bna`). The developer environment allows you to deploy `.bna` files to a local or cloud {{site.data.keyword.blockchain}} for development and sharing.

This tutorial deals with the next step of a business network lifecycle, that is, to activate your business network by deploying the `.bna` to {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

## Before you begin

Ensure that you have installed the {{site.data.keyword.blockchainfull}} developer environment and are comfortable with developing and deploying business networks. Guidance on writing business networks is available in the [Hyperledger Composer documentation](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

You need access to an Enterprise Plan instance of {{site.data.keyword.blockchainfull_notm}} Platform and create your peers in the Enterprise Plan network. For more information about {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan, see [Enterprise Plan overview](./enterprise_plan.html).

## Step One: Create a connection profile for {{site.data.keyword.blockchainfull_notm}} Platform

Create a directory to store your connection details, for example:

  ```
  /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
  ```
  {:codeblock}

  Each connection profile should contain a `connection.json` file. Create a new directory under the `.composer-connection-profiles`, in this instance `bmx-hlfv11`. This will be the name of the profile that you will use when working with Hyperledger Composer and {{site.data.keyword.blockchainfull_notm}} Platform.

  ```
  mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
  cd ~/.composer-connection-profiles/bmx-hlfv11
  ```
  {:codeblock}

  You should now have the following directory structure:

  ```
  /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
  ```
  {:codeblock}

## Step Two: Retrieve your connection profile

1. In your {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor, select **Overview** from the left navigation menu. 

2. In the "Overview" screen, cick the **Connection Profile** button and then the **Download** button to retrieve your connection profile.

3. Save the connection profile in the directory structure that you create in Step One. You can name it as **connection.json**.

## Step Three: Adding channel information

Add the channel value in the `connection.json` to match the name of the channel, which you will create in the Enterprise Plan network and deploy your business network to. In the provided example template of connection profile, the channels element is `"channels": {},`.

## Step Four: Preparing your peers

**Note**: This step **must** be performed before you create the channel to deploy a business network to. If this step is performed after channel creation, a deployed business network **may not start**.

In the Connection Profile document under **certificateAuthorities**, an attribute **registrar** contains attributes for **enrollId** and **enrollSecret** in the following format:

 ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```
 {:codeblock}

1. Create a business network card for the certificate authority by using the following command:

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Ensure that you run the command in the same directory as your connection profile.

2. Import the business network card using the following command:

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. After the card has been imported, it can be used to acquire the certificates from the certificate authority using the following command:

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    The `composer identity request` command creates creates a credentials directory that contains the relevant certificate files.

4. In your Network Monitor, select **Members** from the left navigation menu. In the "Members" screen, select the **Certificates** tab and then click the **Add Certificate** button.

5. Enter a unique name for this certificate in the **Name** field. This name cannot include dashes or hyphens.

6. Open the `admin-pub.pem` file that is created earlier and copy the contents of this file into the **Key** field. Press the **Submit** button.

7. In the "Overview" screen of your Network Monitor, restart the peers.

8. The certificate should then appear in the list of certificates.

## Step Five: Creating your channel

1. Select **Channels** from the navigation menu on the left panel and click the **New Channel** button.

2. Enter a Channel Name and optional description and click **Next**. Note that the Channel Name must match the name that you specify in your connection profile for the channel attribute.

3. Give permissions as required and click **Next**.

4. Select the policy of the number of operators that need to accept channel updates and submit the request.

5. You should now be taken to the **Notifications** section where there should be a new request to review. Click the **Review Request** button.

6. Click the **Accept** button and you will be taken back to the **Notifications** section where you can see that the request can now be submitted. Click the **Submit Request** button to open the submission dialog and then click the **Submit** button. The new channel has been created.

7. Select **Channels** from the navigation menu. The new channel in the list of channels and should have “No peers added yet”. Click the actions menu next to it and select **Join Peers**. Check the check boxes next to the Peers you want to add and press **Add Selected**.

## Step Six: Creating a new identity to administer your business network

Create business network card by using the requested certificates. This business network card has the authority to install chaincode onto the peers that your uploaded public certificate and will be an issuer for the certificate authorities.

1. To create the card, run the following command:

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    Where `bmx-hlfv11` is the name of the profile that you previously created.

2. After creating the card, import it by using the following command:

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    Now we are ready to deploy your `.bna` file to the {{site.data.keyword.blockchainfull_notm}} Platform network.


## Step Seven: Deploying the business network

Now you can deploy your `.bna` file to the {{site.data.keyword.blockchainfull_notm}} Platform.

1. Using the card that is created in the previous step, we must first install, and then start the business network. Install the business network using the following command:

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. After installing the business network, start the business network using the following command:

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

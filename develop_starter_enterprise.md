---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Deploying business networks on Starter and Enterprise Plan
{: #deploying-a-business-network}

*[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)*

{{site.data.keyword.IBM}} does not provide support for networks using Hyperledger Composer in production, including the Composer CLI, JavaScript APIs, REST server, and Web Playground.
{:note}

[Business networks](/docs/services/blockchain/glossary.html#glossary-business-network) can be developed and deployed to Starter and Enterprise Plan networks by using the {{site.data.keyword.blockchainfull_notm}} Platform developer environment and Hyperledger Composer developer toolset.
{:shortdesc}

By using the developer environment, you can quickly model and test blockchain business networks and deploy them to a Starter or Enterprise Plan network of the {{site.data.keyword.blockchainfull_notm}} Platform.

## Deploying a business network to Starter Plan
{: #deploying-a-business-network-starter-plan}

### Before you begin
{: #deploying-a-business-network-before-begin}

Ensure that you read the [About Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) and have created a Starter Plan network by following the instructions in [Getting started with Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).

Ensure you have installed Node v8.9 or higher, npm v5.x, and Hyperledger Composer:

- If your network is on Fabric version 1.2, use Hyperledger Composer v0.20.x.
- If your network is on Fabric version 1.1, use Hyperledger Composer v0.19.x.

You can find your Fabric version by opening the [Network Preferences window](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) in your Network Monitor.

### Step one: Retrieve admin secret
{: #deploying-a-business-network-retrieve-admin-secret}

1. From the Starter Plan overview screen, click **Connection Profile** and then download. Rename this file to 'connection-profile.json'.

2. Move this file to be in the same directory as your `.bna` file.

3. Inside the connection profile, go all the way down until you see 'registrar'. Inside 'registrar', under 'enrollId' there is an **enrollSecret** property. Retrieve the secret and save a copy of it.

    ![Retrieve admin secret](images/get_enroll_secret.gif "Retrieve admin secret")

### Step two: Create a certificate authority card
{: #deploying-a-business-network-CA-card}

The secret retrieved in the preceding step will be used to create a business network card for the certificate authority (CA). The CA card will then be imported and the card will be used to exchange the **enrollSecret** for valid certificates from the Starter Plan certificate authority.

1. Using the **enrollSecret** noted from step one, run the following command to create the CA business network card:

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  Replace `enrollSecret` in the preceding command with the admin secret retrieved from the connection profile.

2. Import the card using the following command:

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. Now that the card is imported, it can be used to exchange the **enrollSecret** for valid certificates from the CA. Run the following command to request certificates from the certificate authority:

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  Replace `enrollSecret` in the preceding command with the admin secret retrieved from the connection profile. The `composer identity request` command creates a `credentials` directory that contains certificate `.pem` files.

### Step three: Add the certificates to the Starter Plan instance
{: #deploying-a-business-network-add-certs-to-starter-plan}

The certificates must be added to the Starter Plan network. For convenience, you can add them by using the {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. The certificates must be added, then the peers must be restarted, and then the certificates must be synced on the channel. The certificate needed is the `admin-pub.pem` file that was generated from the previous command, which is in the `credentials` directory.

1. In the Starter Plan Network Monitor, click on the **Members** tab, then **Certificates**, then **Add Certificate**. Go to your `credentials` directory, and copy and paste the contents of the `admin-pub.pem` file in the certificate box. Submit the certificate and restart the peers. Note: restarting the peers takes a minute.

    ![Add certificates](images/add_cert.gif "Add certificates")

2. Next, the certificates must be synced on the channel. Click the **Channels** tab, then the **Actions** button, then **Sync Certificate** and **Submit**.

    ![Sync certificates](images/sync_cert.gif "Sync certificates")

### Step four: Create an admin business network card
{: #deploying-a-business-network-create-admin-card}

Now that the correct certificates have been synced with the peers, business network cards can be created which have the permissions to install the Hyperledger Composer runtime and start chaincode.

1. Create an admin card with the channel admin and peer admin roles by using the following command:

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   This card will be used to deploy a business network to Starter Plan.

2. Import the card created in the previous step using the following command:

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### Step five: Install and start the business network
{: #deploying-a-business-network-install-start-network}

Next, the card created in the previous step can be used to install and start a business network. For this guide, we'll install the vehicle manufacturing network sample, either use the vehicle manufacturing sample or install your own business network, but make sure to change the business network names specified in the commands. The command to start a business network will also create a card. For Starter Plan, this card must be deleted, the example command given names this card `delete_me.card` so it can be easily distinguished.

1. Install the Hyperledger Composer runtime with the following command:

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   Note the business network version number which is returned when you run this command. It will be required in the next step.

2. Start the business network with the command below. If you get an error, wait a minute and try again. Use the version number from the last step after the `-V` option.

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Delete the business network card called `delete_me.card`.

4. Create a new business network card and reference the certificates that are retrieved earlier with the following command:

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. Import the business network card with the following command:

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

The business network is now deployed to the Starter Plan instance.

### Step six: Ping the business network to ensure it is running correctly
{: #deploying-a-business-network-ping-business-network}

Run the following command to ping the business network:

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

To view the chaincode logs, click **Channels**, and then select your channel. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> Click the **Chaincode** tab. Expend the chaincode row and then click the **JSON** or **Logs** button.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

The business network has now been successfully deployed to the Starter Plan instance.

## Deploying a business network to Enterprise Plan
{: #deploying-a-business-network-to-enterprise-plan}

{{site.data.keyword.blockchainfull_notm}} Platform's developer tools help you create a **Business Network Definition** that can then be packaged up into a business network archive (`.bna`). The developer environment allows you to deploy `.bna` files to a local or cloud {{site.data.keyword.blockchain}} for development and sharing.

This tutorial deals with the next step of a business network lifecycle, that is, to activate your business network by deploying the `.bna` to {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

### Before you begin
{: #deploying-a-business-network-enterprise-plan-before-begin}

Ensure that you have installed the {{site.data.keyword.blockchainfull_notm}} developer environment and are comfortable with developing and deploying business networks. Guidance on writing business networks is available in the [Hyperledger Composer documentation](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

You need access to an Enterprise Plan instance of {{site.data.keyword.blockchainfull_notm}} Platform, and to have created your peers beforehand. For more information about {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan, see the [Enterprise Plan overview](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

### Step one: Create a connection profile for IBM Blockchain Platform
{: #deploying-a-business-network-create-connection-profile}

1. Create a directory to store your connection details, for example:

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

### Step two: Retrieve your connection profile
{: #deploying-a-business-network-retrieve-connection-profile}

1. From your {{site.data.keyword.blockchainfull_notm}} Platform dashboard, select **Overview**, then **Connection Profile** and the **Download** button to retrieve your connection profile.

2. Save the connection profile in the directory structure created in the preceding step. Name it **connection.json**.

### Step three: Add channel information
{: #deploying-a-business-network-add-channel-information}

1. Add the channel value in the `connection.json` to match the name of the channel that you plan to create and deploy your business network to. In the example connection profile template provided, the channels element is as follows: `"channels": {},`.

### Step Four: Prepare your peers
{: #deploying-a-business-network-prepare-peers}

This step **MUST** be performed before you create the channel to deploy a business network to. If this step is performed after channel creation, a deployed business network **might not start**.
{:note}

In the Connection Profile document under **certificateAuthorities** is an attribute **registrar** that contains attributes for **enrollId** and **enrollSecret** in the following format:

  ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
  ```

1. Create a business network card for the CA by using the following command:

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Ensure that you're running the command in the same directory as your connection profile.

2. Import the business network card by using the following command:

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

4. Select **Members** from the navigation menu, then select the **Certificates** menu option and click the **Add Certificate** button.

5. Enter a unique name for this certificate in the **Name** field, this name cannot include dashes or hyphens.

6. Open the `admin-pub.pem` file created earlier and copy the contents of this file into the **Key** field and press the **Submit** button.

7. Use the user interface to stop the peers, then start the peers.

8. The certificate should now appear in the list of certificates.

### Step five: Create your channel
{: #deploying-a-business-network-create-your-channel}

1. Select **Channels** from the navigation menu on the left panel and click the **New Channel** button.

2. Enter a Channel Name and optional description and click **Next**. Note that the Channel Name must match the name that you specify in your connection profile for the channel attribute.

3. Give permissions as required and click **Next**.

4. Select the policy of the number of operators that need to accept channel updates and submit the request.

5. You should now be taken to the **Notifications** section where there should be a new request to review. Click the **Review Request** button.

6. Click the **Accept** button and you will be taken back to the **Notifications** section where you can see that the request can now be submitted. Click the **Submit Request** button to open the submission dialog and then click the **Submit** button. The new channel has been created.

7. Select **Channels** from the navigation menu. The new channel in the list of channels and should have “No peers added yet”. Click the actions menu next to it and select **Join Peers**. Check the check boxes next to the Peers you want to add and press **Add Selected**.

### Step six: Create a new identity to administer your business network
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

Create business network card by using the requested certificates. This business network card will have the authority to install chaincode onto the peers that have your uploaded public certificate and will be an issuer for the certificate authorities.

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

    Now we are ready to deploy your `.bna` file to the {{site.data.keyword.blockchainfull_notm}} Platform.


### Step seven: Deploy the business network
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

Now you can deploy your `.bna` file to the {{site.data.keyword.blockchainfull_notm}} Platform.

1. To use the card that is created in the previous step, you need to first install and then start the business network. Install the business network by using the following command:

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. After you install the business network, start the business network by using the following command:

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. To check that the business network has been deployed correctly, create an identity and associated business network card that can be used to ping the network.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. Import the business network card by using the following command:

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. Ping the network to check the network is running:

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

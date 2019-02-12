---

copyright:
  years: 2019
lastupdated: "2019-02-12"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Creating applications
{: #ibp-console-app}

***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

After installing smart contracts and deploying your nodes, you can use client applications to transact with other members of your network. Applications can invoke the business logic contained in smart contracts to create, transfer, or update assets on the blockchain ledger. Use this tutorial to learn how to interact with networks you manage from {{site.data.keyword.blockchainfull}} platform console from client applications.
{:shortdesc}

You can learn more about how applications and smart contracts work together by visiting the [Developing applications topic ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "developing applications") in the Hyperledger Fabric documentation. The topic explores a hypothetical use case where banks and corporations trade commercial paper and describes how the transactions are encoded in smart contracts. The [commercial paper tutorial ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") extends this topic by allowing you to deploy the smart contracts on fabric network and use sample application code to create and transfer the commercial paper between network members.

**Application samples and tutorials**

|  Sample  |  Description    |  Difficulty    | Language | Location |
| -----------------|---------|---------|---------|
| [FabCar ![External link icon](../images/external_link.svg "External link icon")](https://developer.ibm.com/patterns/write-a-smart-contract-for-the-fabcarcommercial-paper-or-iks-cluster-with-saas-v2-beta-network-think/ "Blockchain")| Use an IKS Cluster to deploy a simple Fabric network smart contract onto the Blockchain Platform. | Intermediate | Node.js | DeveloperWorks documentation|
| [Commercial Paper ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Commercial Paper")| Use the commercial paper smart contract to trade on a local instance of Hyperledger Fabric. | Intermediate | Node.js | Hyperledger Fabric documentation|
| **Coming Soon:** Advanced Commercial Paper  | Advanced sample with best practices of ownership and privacy by using private data collections and state based endorsement. | Advanced | Node.js | Hyperledger Fabric documentation|
| [Run a commercial paper smart contract with the {{site.data.keyword.blockchainfull_notm}} VSCode extension ![External link icon](../images/external_link.svg "External link icon")](https://developer.ibm.com/tutorials/run-commercial-paper-smart-contract-with-ibm-blockchain-vscode-extension/ "Run a commercial paper smart contract with VSCode")|  Learn to use the VScode extension to install and invoke the commercial paper smart contract on a local instance of Hyperledger Fabric. | Intermediate | Node.js | IBM Developer|
| [Run the commercial paper sample on the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)| Learn to deploy and invoke the commercial paper contract on an {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network. | Intermediate |  Node.js | {{site.data.keyword.blockchainfull_notm}} Platform documentation|  

Developing an application might require coordination between two distinct users of your network, The network operator and the application developer:
- **The network operator** is the administrator who uses the {{site.data.keyword.blockchainfull_notm}} Platform console to deploy the nodes of your organization and installs the smart contracts on your network.
- **The application developer** builds the client application will be consumed by end users. The developer uses the [Hyperledger Fabric SDK's ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK's"){:new_window} to invoke transactions written in the smart contracts.

If you are the **network operator**, you will need to complete the following steps before the application developer can interact with your network:
1. Use your organization CA to [register an application identity](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities).
2. [Download the connection profile](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) from the smart contracts panel.
3. Send the application developer the following objects and information:
  - The enroll ID and secret of the application identity.
  - The connection profile.
  - The smart contract name.
  - The name of the channel the smart contract was instantiated on.  

If you are the **application developer**, use the information provided by the network operator to complete following steps:
1. Generate a public and private key pair using the enroll ID and secret of the application identity, along with CA endpoint information inside your connection profile.
2. Use the connection profile, channel name, smart contract name, and application keys to invoke the smart contract.  

The application developer can use two programming models to interact with the network:

**High Level Fabric SDK API's**

Starting with Fabric v1.4, users can take advantage of a simplified application and smart contract programming model. The new model reduces the number of steps and amount of code required to submit a transaction. This model is only supported for applications written in **Node.js**. If you want to take advantage of the new model, you can use this tutorial to complete the following actions on an {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network:

- [Generate certificates for your application](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll) using the SDK.
- [Invoke a smart contract from the SDK](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke).
- Use the [IBM Blockchain VScode extension](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) to write, deploy, and iteratively develop and test your smart contracts.
- Learn about application development by deploying the [commercial paper tutorial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) to the nodes managed from your console. This tutorial will provide more background on how to use Fabric Wallets and Gateways.


**Low Level Fabric SDK API's**

If you want to continue to use your existing smart contract and application code, or use the other Fabric SDK languages provided by the Hyperledger community, you can use the [low level Fabric SDK API's](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level) to connect to your network.

## Register an application identity
{: #ibp-console-app-identities}

Applications need to sign the transactions they submit to {{site.data.keyword.blockchainfull_notm}} nodes, and attach a public key that is used by nodes to verify that the transactions are being sent by the proper party. This ensures that transactions are submitted by the organizations that have permission to participate.

The network operator needs to use the organization's CA to register an application identity, which can then be used by the application developer to generate a public and private key. The operator can provide the enroll ID and secret of the identity, along the CA endpoint information, to be used by the SDK to generate certificates. By enrolling on the client side, the application developer ensures that no other party has access to the private key of the application. For additional security, the network operator can set an enrollment limit of one during registration. After the application developer enrolls, the enroll ID and secret cannot be used to generate another private key.

If you are less worried about security, the network operator can enroll an application identity using the [CA tab](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll). The operator can then download the identity or export it to the console wallet. In order to use the certificates from the SDK, the keys need to decoded from base64 into PEM format. You can decode the certs by running the following command on your local machine:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

## Download your connection profile
{: #ibp-console-app-profile}

Applications are able to submit transactions only to the smart contracts that have been instantiated on channels. As a result, the information you need to connect to interact with a smart contract can be found in the list of instantiated smart contracts in your console. This means you must have already installed and instantiated your smart contract.

The Hyperledger Fabric [Transaction Flow ![External link icon](../images/external_link.svg "External link icon")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaction Flow"){:new_window} spans multiple components, with the client applications collecting endorsements from peers and sending endorsed transactions to the ordering service. The connection profile provides your application with the endpoints of the peers and ordering nodes it needs to submit a transaction. It also contains information about your organization, such your Certificate Authorities and your MSP ID. The Fabric SDKs can read the connection profile directly, without you having to write code that manages the transaction and endorsement flow.

If you configured anchor peers when creating a channel, you can take advantage of the [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery") feature of Hyperledger Fabric. Service discovery allows your application to learn which peers on the channel outside your organization need to endorse a transaction. Without service discovery, you will need to get the endpoint information of these peers out of band from other organizations and add them to your connection profile. For more information about how to configure anchor peers, see step three of the [private data topic](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) in the Deploy a smart contract tutorial.

Navigate to the smart contracts tab in your platform console. Next to each instantiated smart contract, navigate to the overflow menu. Click on the button named **Connect with SDK**. This will open a side panel that will allow you to build and download your connection profile. First, you need to select the CA of your organization that you used to register your application identity. You will also need to select your organization MSP definition. You will then be able to download the connection profile that you can use to generate certificates and invoke the smart contract.

## Enroll using the SDK
{: #ibp-console-app-enroll}

Once the network operator provides the enroll ID and secret of the application identity and the network connection profile, an application developer can use the Fabric SDKs or the Fabric CA client to generate client side certificates. You can use the following steps to enroll an application identity using the [Fabric SDK for Node.js ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/ "Fabric SDK for Node.js").

1. Save the connection profile to your local machine and rename it `connection.json`.
2. Save the following code block as `enrollUser.js` in the same directory as your connection profile:

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Create a new CA client for interacting with the CA.
      const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
      const ca = new FabricCAServices(caURL);

      // Create a new file system based wallet for managing identities.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Check to see if we've already enrolled the admin user.
      const userExists = await wallet.exists('user1');
      if (userExists) {
        console.log('An identity for "user1" already exists in the wallet');
        return;
      }

      // Enroll the admin user, and import the new identity into the wallet.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. Edit `enrollUser.js` to replace the following values:
  - Replace  ``<CA_Name>`` with the name of your organizations CA. You can find your CA name in the "organizations" section of your connection profile under "Certificate Authorities". Do not use the "caName" in the "Certificate Authorities" section.
  - Replace ``<app_enroll_id>`` with the application enroll ID provided by your network operator.
  - Replace ``<app_enroll_secret>`` with the application enroll secret provided by your network operator.
  - Replace ``<msp_id>`` with the MSP ID of your organization. You can find your MSP ID under the "organizations" section of your connection profile.
4. Navigate to `enrollUser.js` using a terminal and run `node enrollUser.js`. If the command runs successfully, you should see the following output:

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
  The SDK will create and store your certificates inside the `wallet/user1/` directory created by the command. This directory is the File System wallet used submit future transactions.

The wallets used by the Fabric SDKs are different from the wallet in the {{site.data.keyword.blockchainfull_notm}} Platform console. The identities stored in your console wallet cannot be directly used by the SDK.
{: note}

## Invoke a smart contract using the SDK
{: #ibp-console-app-invoke}

After you have generated the application public and private key and stored them in a wallet, you are ready to submit a transaction. You need to know the name of the smart contract and the name of the channel it was instantiated on. You can use the steps below to invoke a smart contract using the [Fabric SDK for Node.js ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/ "Fabric SDK for Node.js").


1. Save the file below on your local machine as `invoke.js`. Save the file in the same directory as `enrollUser.js`

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Parse the connection profile. This would be the path to the file downloaded
        // from the IBM Blockchain Platform operational console.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Configure a wallet. This wallet must already be primed with an identity that
        // the application can use to interact with the peer node.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Create a new gateway, and connect to the gateway peer node(s). The identity
        // specified must already exist in the specified wallet.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {enabled: true, asLocalhost:false }});

        // Get the network channel that the smart contract is deployed to.
        const network = await gateway.getNetwork('<channel_name>');

        // Get the smart contract from the network channel.
        const contract = network.getContract('<smart_contract_name>');

        // Submit the 'createCar' transaction to the smart contract, and wait for it
        // to be committed to the ledger.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. Edit `invoke.js` to replace the following values:
  - Replace  ``<channel_name>`` with the name of the channel the smart contract was instantiated on. You can find your CA name under the "Certificate Authorities" section of your connection profile.
  - Replace ``<smart_contract_name>`` with the name of the installed smart contract. You can get this value from your network operator.
  - Edit the contents of `submitTransaction` to invoke a function inside your smart contract. The `invoke.js` file is written to invoke the [fabcar smart contract ![External link icon](../images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar). If you want to run the file below to submit a transaction, install fabcar and instantiate the smart contract on one of your channels.

3. Navigate to `invoke.js` using a terminal and run `node invoke.js`. If the command runs successfully, you should see the following output:

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  If you navigate to you channel using the console, you will be able to see another block added by the transaction.


## Connecting with the IBM Blockchain VScode extension
{: #ibp-console-app-vscode}

The {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code extension provides an environment within Visual Studio Code for developing, packaging, and deploying smart contract packages. Be sure you have your connection profile and the set of key files generated using your CA. You can then use the VScode extension to connect to a network you are managing using your console.

Follow the instructions to download the [VScode extension ![External link icon](../images/external_link.svg "External link icon")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode extension") on the visual studio code marketplace. Once the extension is installed, open VScode you an access the extension by clicking **View** > **Command Palette**. Enter the command *IBM Blockchain Platform: Create Smart Contract Project* to create a new smart contract project.

Once you have created the new project, you can connect directly to your network from Visual Studio code using the information in your instantiated smart contract from your console. Use the  **Instantiated smart contracts** table on the Smart contracts tab to download your [connection profile](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) to your local file system. Then [create an application identity](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) and use your CA to create and download a certificate (public key) and private key. Follow the instructions above to convert the private key and certificate into PEM format. Once you have downloaded your connection profile and keys, use the following steps to connect to your network.

1. Open the _{{site.data.keyword.blockchainfull_notm}} Platform_ tab in Visual Studio Code.
2. In the _{{site.data.keyword.blockchainfull_notm}} Platform_ pane, click **Add new connection**.
3. Enter a name for the connection. This name will be displayed in the _{{site.data.keyword.blockchainfull_notm}} Platform_ pane.
4. Enter the fully qualified file path of your connection profile.
5. Enter the fully qualified file path of your certificate (public key) in PEM format.
6. Enter the fully qualified file path of the private key in PEM format.
7. Your connection should now appear in the connections list underneath `local_fabric`. Double-click the connection name to connect.

Once you are connected from VScode, you will be able to see the list of peers from your organization and channels they have joined in the blockchain connections pane. You will be able to see the list of installed smart contracts under each peers. To install a new smart contract from your project on your network, first right-click a peer and select **Install smart contract**. You can then instantiate that smart contract by right-clicking on a channel and select **Install smart contract**. You can use learn more about using the {{site.data.keyword.blockchainfull_notm}} Platform extension by visiting the documentation in the [Visual Studio code marketplace ![External link icon](../images/external_link.svg "External link icon")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode extension").


## Run the Commercial Paper sample
{: #ibp-console-app-commercial-paper}

The [commercial paper tutorial ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") in the Hyperledger Fabric documentation takes developers through a use case in which multiple parties buy, sell and redeem commercial paper. It extends the [Developing applications topic ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "developing applications") by providing sample  smart contract and application code that allows you to create and trade assets on a local instance of Fabric.

You can also deploy the commercial paper tutorial sample code onto an {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network. This will allow you to quickly get started interacting with your network and use the sample to download the necessary dependencies. The sample code also includes examples of how you can import certificates into a [wallet ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") and use your connection profile to build a [Fabric gateway ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway"). The tutorial can be used by two different organizations to complete different transactions with a single asset. If you used the [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) to deploy two peer organizations connected to a channel, you can interact with the tutorial using both organizations.

Follow the steps below to deploy the sample on your network. You can review the tutorial in the Fabric Documentation [commercial paper tutorial ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") for more details about the smart contracts and the application structure.

### Prerequisites

Before you can deploy the commercial paper sample, you will need to install required tools on your local machine:
  * [Git ![External link icon](../images/external_link.svg "External link icon")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

You will also need a use text editor to edit and save files in the sample. You can use many of the high quality editors that are available for free, such as [Atom ![External link icon](../images/external_link.svg "External link icon")](https://atom.io/ "atom"), [Sublime text ![External link icon](../images/external_link.svg "External link icon")](http://www.sublimetext.com/ "Git"), or [Brackets ![External link icon](../images/external_link.svg "External link icon")](http://brackets.io/ "Brackets").

### Step one: Download the sample

You download the commercial paper sample by cloning the [Fabric Samples repository ![External link icon](../images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric-samples "Fabric Samples repository"):

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

Once you have downloaded the Fabric Samples, run the following commands to ensure that you are using the version of the samples compatible with Fabric v1.4.

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

You can find the sample in the `commercial paper` folder of `fabric-samples`.

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

The tutorial contains two sample organizations, **magnetocorp** and **digibank**. Each organization has its own sample application code, stored in two separate folders under the `organization` directory.

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

In the course of the tutorial, you will first use the magnetocorp application code to issue a commercial paper. You can then use the digibank application code to buy and redeem the paper. Both directories contain the same smart contract.

Navigate to the application code inside the magnetocorp directory.

```
cd magnetocorp/application
```
{:codeblock}

Once you are inside the directory, you can download the application dependencies by running the following command:

```
npm install
```
{:codeblock}

### Step two: Install and instantiate smart contract

You can find the commercial paper smart contract inside the `contract` folder of the `digibank` and `magnetocorp` directory. You need to install this smart contract on all the peers of the organizations using the tutorial. You will then need to instantiate the commercial paper contract on a channel. The smart contract needs to be packaged in [.cds format ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "packaging smart contracts") to be installed using the console.

You can use the [IBM Blockchain VScode extension](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) to package the smart contract. After installing the extension, use Visual Studio Code to open the `contracts` folder in your workspace. Open the _{{site.data.keyword.blockchainfull_notm}} Platform_ tab. In the _{{site.data.keyword.blockchainfull_notm}} Platform_ pane, navigate to the smart contract packages section and click **Package a Smart Contract Project**. The VScode extension will use the files in the `contracts` folder to create a new package named `papernet-js@.0.0.1.cds`. Right click this package to export it to your local file system. You can then use your console to [install the smart contracts on your peers](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install) and then [instantiate the smart contract on a channel](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).

### Step three: Generate certificates for your wallet

Applications need to sign the requests they send to fabric components. If the components do not recognize the organizations submitting the transactions, the transactions will be rejected and return with an error. The commercial paper sample creates a file system wallet that will store your certificates and sign your transactions. For more information about how applications use wallets, see the [wallet ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") topic in the Fabric Documentation. The wallets used by the Fabric SDKs are different from the wallet in the {{site.data.keyword.blockchainfull_notm}} Platform console. The identities stored in your console wallet cannot be directly used by the SDK.

The original sample uses the `addToWallet.js` file to create a file system wallet using certificates from the fabric samples folder. We are going to create a new file that uses the SDK to generate a client side certificates and store them directly inside a new wallet.

Choose the CA of the organization you want to use to operate the tutorial as magnetocorp. For example, you can use Org1 if you have completed the the Build a network tutorial. Use the CA to [create an application identity](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities). **Save** the enroll ID and secret.

Use your console to [download your connection profile](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Save the connection profile to your local file system and rename it `connection.json`. Then use following command to move the connection profile to a directory where it will be referenced by future commands.

  ```
  mv $HOME/<path_to_creds>/connection.json /gateway/connection.json
  ```
  {:codeblock}

Navigate to the ``/magnetocorp/application`` directory and save the following code block as `enrollUser.js`.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Create a new CA client for interacting with the CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Create a new file system based wallet for managing identities.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Check to see if we've already enrolled the admin user.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Enroll the admin user, and import the new identity into the wallet.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

We should take moment to study how this file works before we edit it. First, `enrollUser.js` imports the `FileSystemWallet` and `X509WalletMixin` classes from the `fabric-network` library.

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

The file then uses the `FileSystemWallet` class to build a wallet on your local filesystem. You can edit the line below to store the wallet in a different location.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

After creating the wallet, the code snippet uses the enroll ID and secret to enroll using your organization CA. It then creates an identity for the public private key pair and imports them into the wallet. Notice how the file passes your organization MSP ID into the wallet as well.

```
// Enroll the admin user, and import the new identity into the wallet.
const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

**Edit** `enrollUser.js` to replace the following values:
- Replace  `'<CA_Name>'` with the name of your organizations CA. You can find your CA name in the "organizations" section of your connection profile under "Certificate Authorities". Do not use the "caName" in the "Certificate Authorities" section.
- Replace `'<app_enroll_id>` with the application enroll ID provided by your network operator.
- Replace `'<app_enroll_secret>'` with the application enroll secret provided by your network operator.
- Replace `'<msp_id>'` with the MSP ID of your organization. You can find this MSP ID under the "organizations" section of your connection profile.

Save `enrollUser.js` and close it. In the `magnetocorp` directory, run the following command:
```
node enrollUser.js
```
{:codeblock}

When the command completes successfully, you should see the following output:

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

You can find the wallet that was created in the `identity` folder of the `magnetocorp` directory.

### Step four: Using the connection profile to build a fabric gateway

The Hyperledger Fabric [Transaction Flow ![External link icon](../images/external_link.svg "External link icon")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaction Flow"){:new_window} spans multiple components, with the client applications playing a unique role. Your application needs to connect to the peers that need to endorse the transaction and the ordering service that will order the transaction and add it into a block. You can provide the endpoints of these nodes to your application by using your connection profile to construct a Fabric Gateway. The Gateway then conducts the low level interactions with your fabric network. To learn more, visit the [Fabric gateway ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway") topic in the Fabric Documentation.

You have already downloaded your connection profile and used it to connect to your organization's Certificate Authority. Now we will use the connection profile to build a gateway.

Open the file `issue.js` in a text editor. Before you edit the file, notice that it imports the `FileSystemWallet` and `Gateway` classes from fabric-network library.

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

The `Gateway` class is used to construct a gateway that you will used to submit your transaction.

```
const gateway = new Gateway()
```
{:codeblock}

The `FileSystemWallet` class is used to load the wallet you created in the previous step. **Edit** the line below if you changed the location of the wallet on your filesystem.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

After importing your wallet, the following code passes your connection profile and the wallet to new gateway. The lines that print logs have been removed for brevity.

```
const userName = 'User1@org1.example.com';

// Load connection profile; will be used to locate a gateway
let connectionProfile = yaml.safeLoad(fs.readFileSync('../gateway/networkConnection.yaml', 'utf8'));

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: false, asLocalhost: true }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

**Edit** the code above to import the connection profile that you downloaded from the console. Update the username that you selected for your `identityLabel` in `enrollUser.js`. In addition, you also need to update the discovery options to take advantage of service discovery on your network. Set `discovery: { enabled: true, asLocalhost: false }`.  **Note** that the console connection profile is in JSON format rather than a YAML file. You will need to change the section importing your connection profile. After your edits, the code above might look like the following:

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const ccp = JSON.parse(ccpJSON);

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

This code snippet will use the gateway to open GRPC connections to the peer and orderer nodes, and interact with your network.

### Step five: Invoking the smart contract

After configuring the gateway to connect to the network managed by your console, we will edit the portion of the `issue.js` file that connects to the commercial paper smart contract. You will need to provide the gateway the contract name and the channel on which you instantiated the smart contract.

**Edit** the line below, replacing `mychannel` with your channel name.

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

Following a line of code that prints a message to your console, you can find a line that provides the gateway the smart contract name. **Edit** the line below to change the name `papercontract` to the name of the contract you installed.

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

The gateway now has all the information it needs to submit a transaction. The following line invokes the `issue` the issue function in the commercial paper contract, with the arguments defining the new commercial paper asset.

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

After submitting the transaction using the gateway, the `issue.js` file disconnects the gateway connection.

```
gateway.disconnect();
```
{:codeblock}

This command closes the GRPC connections opened by your gateway. Closing connections will save network resources and improve performance.

After completing the edits from this step and **Step four**, save `issue.js` and close it. Submit the transaction that creates the new commercial paper using the following command:

```
node issue.js
```
{:codeblock}

If the transaction is successful, you should be able to see the following output in your terminal:

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### Step six: Operate the sample as digibank

After creating the commercial paper operating as magnetocorp, you can buy and redeem the commercial paper by operating the tutorial as digibank. You can use the digibank application code using the same organization as magnetocorp, or use the CA, peers and connection profile of a different organization. If you competed the [Build a network tutorial](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), this is a good opportunity operate the tutorial as Org2.

Navigate to the `digibank/application` directory. You can follow the directions provided in **Step three** to create to generate the certificates and wallet that will sign the transaction as digibank. You can then use the `buy.js` file to purchase the commercial paper from magnetocorp, and then use `redeem.js` to redeem the paper. You can follow **Step four** and **Step five** to edit those files so that they point to the correct connection profile, channel and smart contract.

## Connecting to your network using low level Fabric SDK API's
{: #ibp-console-app-low-level}

If you are interested in preserving your existing application code, or using Fabric SDKs for languages other than Node.js, you can still connect to your network using lower level Fabric SDK API's. Use the console to [download your connection profile](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). You can then import the endpoints of the peers and ordering nodes of your channel directly from the connection profile, or use the node endpoint information to manually add peer and orderer objects. You will also need to use your CA to [create an application identity](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities), and then use the CA endpoint information enroll on the client side, or generate certificates using your console.

The [Fabric Node SDK ![External link icon](../images/external_link.svg "External link icon ")](https://fabric-sdk-node.github.io "Fabric Node SDK") documentation provides a tutorial on how to [connect to your network using a connection profile ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-network-config.html "connection profile tutorial"){:new_window}. The tutorial uses the CA endpoint information in your connection profile to generate keys using the SDK. You can also use your console to generate a public and private key and convert the keys into PEM format. You can then set a user context by passing your keys directly to the SDK's [Fabric Client class ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html "Fabric Client class") using the code below:

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

If you are using low level SDK API's to connect to your network, there are additional steps that you can take to manage the performance and availability of your application. For more information, see [Best practices for application connectivity and availability](/docs/services/blockchain/v10_application.html#dev-app-connectivity-availability).

## Using indexes with CouchDB
{: #console-app-couchdb}

If you use CouchDB as your state database, you can perform JSON data queries from your smart contracts against the channel's state data. It is strongly recommended that you create indexes for your JSON queries and use them in your smart contracts. Indexes allow your applications to retrieve data efficiently when your network adds additional blocks of transactions and entries in the world state. To learn how to use indexes with your smart contracts and your applications, see [Best practices when using CouchDB](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices).

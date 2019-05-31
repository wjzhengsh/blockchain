---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: vs code extension, Visual Studio Code extension, smart contract, development tools

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Developing smart contracts with Visual Studio Code extension
{: #develop-vscode}


The {{site.data.keyword.blockchainfull}} Platform Visual Studio (VS) Code extension provides an environment within Visual Studio Code for developing, packaging, and testing smart contracts. You can use the extension to create your smart contract project and get started developing your business logic. You can then use VS Code to test your smart contract on your local machine by using a preconfigured instance of Hyperledger Fabric before you deploy the smart contract to the {{site.data.keyword.blockchainfull_notm}} Platform. This tutorial describes how to use the VS Code extension.


![Typical smart contract development workflow](images/SmartContractflow.png "Typical smart contract development workflow")
*Figure 1. Typical smart contract development workflow with {{site.data.keyword.blockchainfull_notm}} Platform VS Code extension*  

<!--
<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="images/SmartContractflow.png" width="750" alt="Click a box to get more details on the process." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Create a smart contract project" title="Create a Smart contract project" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Develop contract code in VS Code" title="Create key pair" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract" alt="Package the smart contract" title="Package the smart contract" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-deploy" alt="Deploy locally to debug" title="Deploy locally to debug" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-exporting-deleting-smart-contract-package" alt="Export the package" title="Export the package" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-connecting-ibp" alt="Deploy to IBM Cloud" title="Deploy to IBM Cloud" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
-->

The {{site.data.keyword.blockchainfull_notm}} Platform extension works seamlessly with any instance of the {{site.data.keyword.blockchainfull_notm}} Platform that uses Hyperledger Fabric versions 1.4 and later.
{: note}

## Step one: Install {{site.data.keyword.blockchainfull_notm}} Platform VS Code extension for free
{: #develop-vscode-install}

Before you install the {{site.data.keyword.blockchainfull_notm}} Platform VS Code extension, you must complete the prerequisites.

### Prerequisites
{: #develop-vscode-prerequisites}

- **Install Visual Studio Code**  
  Install the [Visual Studio](https://code.visualstudio.com/) code editor.  
- **Install Yeoman**  
  Yeoman is a generator tool that you can use to create skeleton smart contract projects. Install Yeoman by using the following command: `npm install -g yo`.  
- **Install Docker**  
  To run the preconfigured instance of Hyperledger Fabric, ensure you have installed [Docker ![External link icon](images/external_link.svg "External link icon")](https://www.docker.com/).  
- **Operating system requirements**  
  Currently, this extension is compatible with Mac, Windows, and Linux.  
- **Hyperledger Fabric version requirements**  
  This extension is compatible with Hyperledger Fabric versions 1.4.0 and later.  

### Install the extension
{: #develop-vscode-installing-the-extension}

1. Navigate to the [Visual Studio Code extension marketplace page ![External link icon](images/external_link.svg "External link icon")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) or search for **{{site.data.keyword.blockchainfull_notm}} Platform** in the extensions panel within Visual Studio Code.
2. Click **Install**.
3. Restart Visual Studio Code to complete installation of the extension.

After the installation, you can use the {{site.data.keyword.blockchainfull_notm}} icon on the left side of VS Code to open the {{site.data.keyword.blockchainfull_notm}} Platform panel.

The extension also adds new commands to the Visual Studio Code command palette. You can use the command palette to complete many of the operations that are explained in details in this guide.

## Step two: Create a smart contract project
{: #develop-vscode-creating-a-project}

You can use the extension to create a new smart contract project in Visual Studio Code. The extension creates a basic smart contract that manages an example asset in the language of your choice. You can use the structure of example as a starting point for developing your own business logic. The extension provides all the dependencies that are required to deploy your smart contract to an instance of Hyperledger Fabric.

1. Open the **{{site.data.keyword.blockchainfull_notm}}** tab. Click the overflow menu in the smart contract packages pane and click **Create Smart Contract Project**.
2. Select the language that you want to create a smart contract in. The current options are JavaScript, TypeScript, Go, and Java. **Note:** The {{site.data.keyword.blockchainfull_notm}} Platform does not support Java chaincode.
3. Select an asset to be managed by the example contract. For example, ***bond***.
4. Create a folder with the name of your project and open it.
5. Select how to open your new project. The project folder should now open.

When the project opens, you can find the new smart contract in the explorer window in the left pane. The structure of the project depends on the language that you selected. However, each smart contract contains the same elements:
- The source code of the smart contract. The extension builds a basic smart contract by using the `fabric-contract-api` with a series of functions that manage your example asset. For example, if you selected ***bond***, you will find the functions of `createBond`, `updateBond`, `readBond`, `bondExists`, and `deleteBond`.
- A test file.
- The accompanying smart contract dependencies.

## Step three: Package a smart contract
{: #packaging-a-smart-contract}

You need to package a smart contract in `.cds` format before you can install it on your {{site.data.keyword.blockchainfull_notm}} Platform network or the preconfigured Hyperledger Fabric network. Complete the following steps to package your smart contract:

1. In VS Code, navigate to the **{{site.data.keyword.blockchainfull_notm}} Platform** panel. Ensure that you have a smart contract project open in the file viewer.
2. In the **Smart Contract Packages** pane, click **+**.
  - If you have one smart contract project, it will be packaged automatically and be displayed in the **Smart Contract Packages** pane.
  - If you have multiple smart contract folders open, you will be asked which one to package.
  - If you have no smart contract folders open, you'll get an error message.

### Exporting, importing, and deleting a smart contract package
{: #develop-vscode-exporting-deleting-smart-contract-package}

After you package a smart contract project, you can export it from VS Code:

1. In the {{site.data.keyword.blockchainfull_notm}} Platform extension panel, right-click the smart contract package and select **Export Package**.
2. Choose the directory to save your smart contract package file and click **Export**.

You can also import an existing smart contract package into the {{site.data.keyword.blockchainfull_notm}} Platform pane:

1. In the **Smart Contract Packages** pane. Click the overflow menu and select **Import Package**.
2. Browse to the smart contract package that you want to import, and click **Import**.

You can also click **Delete Package** to remove the smart contract package from the list of packages.

## Step four: Deploy a smart contract to a preconfigured Hyperledger Fabric network
{: #develop-vscode-deploy}

You can use the VS Code to deploy your smart contract to a preconfigured Hyperledger Fabric network that the extension creates on your local machine. This allows you to install, instantiate, and test your smart contract before you deploy it to a live network.

### Deploying the preconfigured Hyperledger Fabric network
{: #develop-vscode-connecting-and-disconnecting}

Use the following steps to deploy the preconfigured network:

1. Ensure that Docker is running on your machine.
2. Open the **{{site.data.keyword.blockchainfull_notm}} Platform** tab in VS Code.
3. In the **Local Fabric Ops** pane, click **local fabric runtime**. If Docker is running, the local Hyperledger Fabric instance should be downloaded and started.
4. Double-click **local_fabric** in the **Fabric Gateways** pane to connect to the local network. By default, the connection uses the admin identity in the Fabric wallets pane. You can create a new identity by right-clicking the certificate authority node in the **Local Fabric Ops** pane. This new identity can then be added to a wallet and be associated with **local_fabric** connection.

The VS Code extension creates a basic Fabric network that includes one orderer, one peer, and one certificate authority. The peer is joined to a channel named `mychannel`. You can find the list of nodes, organizations, and channels that belong to the network in the **Local Fabric Ops** pane. Above these nodes, you can find the list of smart contracts that have been installed and instantiated.

### Stopping, restarting, and removing the preconfigured network
{: #develop-vscode-stop-Fabric-runtime}

You can stop or restart the preconfigured network after it has been created:

1. In the **Local Fabric Ops** pane, click the overflow menu.
2. Select **Restart Fabric Runtime** or **Stop Fabric Runtime** to stop or restart the container.

The connection details are saved to a directory called **local_fabric** that is contained in your current project directory. You can also select **Teardown Fabric Runtime** to completely remove the local Fabric network. **Note:** This removal will result in the loss of the ledger and world state data.

### Deploying your smart contract to the preconfigured network
{: #develop-vscode-deploy-smart-contract}

You can deploy any packages in the **Smart Contract Packages** pane to a running preconfigured network.

First, you need to install the smart contract on a peer:

1. In the **Local Fabric Ops** pane, click **Install Smart Contract**.
2. Select the peer that you want to install the smart contract on.
3. Select the smart contract package that you want to install, and click **Install**.

Next, you can instantiate the smart contract on a channel:

1. In the **Local Fabric Ops** pane, click **Instantiate Smart Contract**.
2. Select the installed smart contract to instantiate.
3. (Optional) Enter the name of the instantiate function in your smart contract. If you used the default smart contract template, no instantiate function is used.
4. (Optional) Enter any arguments that your instantiate function requires.
5. (Optional) Browse to your collection configuration file if your smart contract uses private data.
6. Click **Instantiate**.

If you make any changes to your smart contract code and repackage it, you can upgrade the instantiated smart contract to deploy a newer version to the network:

1. Ensure that the smart contract that you want to upgrade has been instantiated.
2. Install the new version of the smart contract to a peer on the same network.
3. Right-click the instantiated smart contract, and select **Upgrade Smart Contract**.
4. (Optional) Run a transaction after the new smart contract is instantiated.

### Interacting with your smart contract
{: #develop-vscode-submitting-transactions}

After a smart contract is installed and instantiated, you can submit transactions to the functions inside your smart contract by using the **Fabric Gateways** pane:

1. Ensure that your smart contract has installed and instantiated, and that you are connected to the network.
2. In the connections pane, expand the **Instantiated Smart Contracts**.
3. Expand the smart contract that you want to interact with. You will find the list of transactions that are listed beneath your smart contract.
4. Right-click the transaction to submit, and select **Submit Transaction**. For example, if you created and packaged the example bonds smart contract, click **createBond**.
5. Enter any arguments that the transaction requires, and press **Enter**. For example, enter `["bond01","100"]` to create your first bond.

### Connecting your applications to the preconfigured network
{: #develop-vscode-exploring-connection-details}

You can test your client applications by connecting them to the preconfigured network and submitting transactions to your smart contract.

First, you need to export your connection profile:

1. Start the network and expand **Nodes** in the **Local Fabric Ops** pane.
2. Right-click the peer and select **Export Connection Profile**.

You can then use the Fabric SDKs and the connection profile to enroll your admin identity by using the username `admin` and the password `adminpw`. You can then use this identity to invoke your smart contract or register and enroll additional users.

## Step five: Debug smart contracts by using development mode
{: #develop-vscode-development-mode}

You can run the preconfigured network in **Development Mode** to iteratively develop and debug your smart contracts locally, without having to repackage and upgrade the smart contract after every change. Debugging a smart contract allows you to run through the smart contract transactions with breakpoints and output, ensuring the transactions work as intended.

Use the following steps to enable development mode on the preconfigured network:

1. After the network has started, from the **Local Fabric Ops** pane expand the **Nodes** section.
2. Right-click the peer and select **Toggle Development Mode**.

In normal operation, a peer creates and maintains a chaincode container to run instantiated smart contracts. By switching to development mode, the peer allows the chaincode container to be run manually, which allows you to deploy updates to your smart contract directly to your running network by using the VS Code **Debug** view.

1. Ensure that you are connected to the **local_fabric** connection that it is in development mode.
2. Open your smart contract project.
3. Open the **Debug** view in VS Code from the left-hand navigation bar.
4. Select **Debug Smart Contract configuration** from the drop-down list in the upper left.
5. Package and install the smart contract by clicking the **play** button.
6. Add breakpoints to the smart contract by clicking the relevant line numbers in your smart contract files.
7. On the debug toolbar, click the **Blockchain** button to instantiate the smart contract.
8. On the debug toolbar, click the **Blockchain** button to submit or evaluate transactions.

To modify your smart contract while debugging, click the **restart** button after you make changes to your smart contract. Restarting debugging means you don't need to instantiate the contract again.

## Step six: Test an instantiated smart contract
{: #develop-vscode-testing-instantiated-smart-contract}

You can generate tests for smart contracts that are instantiated on the networks that you connect to. The tests can be generated as either **JavaScript** or **TypeScript**, and run or debugged.

1. Ensure that the smart contract has been instantiated.
2. Under **Instantiated Smart Contracts**, right-click the smart contract to generate tests for.
3. Select **Generate Smart Contract Tests**.
4. Select the language for the test file, either **JavaScript** or **TypeScript**. The {{site.data.keyword.blockchainfull_notm}} Platform extension will install required npm modules and build the test file.

After the test file is built, the tests can be run by clicking the **Run Tests** button in the file.


## Step seven: Connect to your {{site.data.keyword.blockchainfull_notm}} Platform network
{: #develop-vscode-connecting-ibp}

You can also use the extension to connect to the {{site.data.keyword.blockchainfull_notm}} Platform and invoke any smart contracts that are installed and instantiated by using the {{site.data.keyword.blockchainfull_notm}} Platform console UI.

Open the {{site.data.keyword.blockchainfull_notm}} Platform console that is associated with your instance of the {{site.data.keyword.blockchainfull_notm}} Platform. Navigate to the **Smart Contracts** tab. Use the **Instantiated Smart Contracts** table on the smart contracts tab to download your [connection profile](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) to your local file system. Then, [create an application identity](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) by using your CA and save the enrollID and secret. Follow the steps below to connect to the {{site.data.keyword.blockchainfull_notm}} Platform from VS Code.

1. Open the _{{site.data.keyword.blockchainfull_notm}} Platform_ tab.
2. In the **Fabric Gateways** pane, click **+**.
3. Enter a name for the connection.
4. Enter the fully qualified file path of your connection profile. Your connection should now appear in the connections list underneath **local_fabric**.
5. In the **Fabric Wallets** pane, click **+**.
6. Choose **Create a new wallet and add an identity** from the options. Provide a name for your wallet and your identity.
7. Enter the MSP ID of your organization.
8. Select the **Select a gateway and provide an enrollment ID and secret** option and choose the gateway that you created above.
9. Enter the enrollID and secret of the application identity that you created with the console. A new identity will be created in the **Fabric Wallets** pane.
10. You can now connect to your instance of your {{site.data.keyword.blockchainfull_notm}} Platform network. Double-click the connection name and select the name of the wallet that you just created. You can also associate the wallet that you created with the gateway by right-clicking the gateway and selecting **Associate A Wallet**. This allows the connection to use the same wallet each time when it connects.

After you connect to the {{site.data.keyword.blockchainfull_notm}} Platform from VS Code, you can see the list of channels that your organization peers have joined under the gateway. Under each channel, you can see the list of smart contracts that are instantiated on each channel and the functions within each smart contract. You can submit transactions to your network by right-clicking a function and selecting **Submit Transaction** and passing the required arguments. You can also generate a test file for the smart contracts that are instantiated on your channels.

### Adding wallets and users
{: #develop-vscode-add-a-wallet}

Use the steps below to create a new wallet by using a certificate and private key:

1. In the **Fabric Wallets** pane, click **+**.
2. Choose to **Create a new wallet and add an identity** from the options. Provide a name for your wallet and your identity.
3. Enter the MSP ID of your organization.
4. Choose to add a certificate and private key.
5. If you use a certificate and private key, browse to the certificate and private key.

You can also add new users to the wallets that have already been created:

1. In the **Fabric Wallets** pane, right-click a wallet and select **Add Identity**.
2. Provide a name for the identity and an MSPID.
3. Choose either to use a certificate and private key or an enrollment id and secret.
4. If you use a certificate and private key, browse to the certificate and private key.
5. If you use an enrollment ID and secret, choose the gateway to enroll with and enter the enrollment ID and secret.

### Editing, disconnecting, and deleting connections
{: #develop-vscode-editing-connection}

From the extension, right-click a connection in the lower left to open a contextual menu with the options to add an identity, edit the connection, or delete the connection.

To edit a connection, complete the following steps:
1. Select **Edit connection** option. The **User Settings** page opens with the connection details highlighted.
2. Make any changes that you need and save the settings page.

When you are ready to disconnect from the network, click the **Disconnect** icon in the upper right of the **Fabric Gateways** pane.

To delete a connection, right-click the connection and select **Delete Gateway**.

---

copyright:
  years: 2017, 2018
lastupdates: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Tools for developing smart contracts
{: #overview}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


The {{site.data.keyword.blockchainfull}} Platform Visual Studio Code extension provides an environment within Visual Studio Code for developing, packaging, and deploying smart contract packages. The extension also includes commands to set up a preconfigured local instance of Hyperledger Fabric for simplified local smart contract development.

You can use this extension to connect to {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan networks.

## Prerequisites

Before installing the {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code extension, complete the following prerequisites.

- **Install Yeoman**

  Yeoman is a generator tool used to create skeleton smart contract projects. Install Yeoman using the following command: `npm install -g yo`

- **Install the Fabric generator for Yeoman**

  After installing Yeoman, install the Fabric generator for creating skeleton smart contract packages. Install the Fabric generator using the following command: `npm install -g generator-fabric`

- **Install Docker**

  To run the preconfigured instance of Hyperledger Fabric, ensure you have installed [Docker](https://www.docker.com/).

- **Operating system requirements**

  Currently, this extension is compatible with Mac, Windows, and Linux.

- **Hyperledger Fabric version requirements**

  This extension is compatible with Hyperledger Fabric versions 1.3.0 and later.

- **Hyperledger Fabric version requirements**

  This extension is compatible with Hyperledger Fabric versions 1.3.0 and later.


## Installing the extension

1. Navigate to the [Visual Studio Code extension marketplace page](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) or search for **{{site.data.keyword.blockchainfull_notm}} Platform** in the extensions panel within Visual Studio Code.

2. Click **Install**.

3. Restart Visual Studio Code to complete installation of the extension.

## Visual Studio Code command palette

This extensions adds many commands to the Visual Studio Code command palette. Many of the operations detailed in this document can also be started by running the command from the Visual Studio Code command palette.

## Creating a project

To create a new project, complete the following steps:

1. Open the command palette, run the **Blockchain: Create Smart Contract Project** command.
2. Select the language you wish to create a smart contract in, currently the options are JavaScript or TypeScript.
3. Create a folder with the name of your project, and open it.
4. Select how to open your new project. The project folder should now open.

## Adding a connection

When you use this extension, you install and instantiate smart contract packages on peers and channels in your Hyperledger Fabric instance. This extension can initialise a preconfigured local instance of Hyperledger Fabric using Docker.

### Connecting to the preconfigured Hyperledger Fabric instance

To connect to the preconfigured Hyperledger Fabric instance, first ensure Docker is running on your machine:

1. Open the _Blockchain Explorer_ tab in Visual Studio Code.
2. In the _Blockchain Explorer_ pane, click **local_fabric**. If Docker is running, the local Hyperledger Fabric instance should be downloaded and started.
3. After the local Hyperledger Fabric instance has been started, double click **local_fabric** to connect to it. You should now see a channel called `mychannel`.

### Connecting to {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan

The {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code extension can be used to connect to an instance of the {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan. Once connected, the extension can be used to develop and deploy smart contracts.

**Note:** Currently the extension is not compatible with {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

1. If you don't have an instance of {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan, create one [here](https://console.bluemix.net/catalog/services/blockchain).
2. Open the IBP UI by clicking **Launch**.
3. Retrieve your connection profile by clicking **Overview** > **Connection Profile** > **Download**.
4. Generate the required admin certificates by clicking **Certificate Authority** > **Generate Certificate** and saving the certificate and private key to your file system.
5. Add the certificates to your {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan instance by clicking **Members** > **Certificates** > **Add Certificate**, and selecting the certificate created in the preceding step.
6. In Visual Studio Code, open the {{site.data.keyword.blockchainfull_notm}} Platform extension view and click **Add new connection**.
7. Enter the connection name, and the path to the connection profile, certificate, and private key.

### Connecting to your own Hyperledger Fabric instance

By using this extension, you can connect to a preconfigured local instance of Hyperledger Fabric, or you can connect to your own Hyperledger Fabric instance. If you choose to connect to your own Fabric instance, it must be Fabric 1.3.0 or later.

To connect to your own Hyperledger Fabric instance, complete the following steps:

1. Open the _Blockchain Explorer_ tab in Visual Studio Code.
2. In the _Blockchain Explorer_ pane, click **Add new connection**.
3. Enter a name for the connection. This name will be displayed in the _Blockchain Explorer_ pane.
4. Enter the fully qualified filepath of your Hyperledger Fabric connection profile.
5. Enter the fully qualified filepath of the certificate for your identity.
6. Enter the fully qualified filepath of the private key for use with the certificate supplied in the preceding step.
7. Your connection should not appear in the connections list underneath `local_fabric`. Double-click the connection name to connect.

If you don't have a preexisting Hyperledger Fabric instance of version 1.3.0 or later, you can connect using the preconfigured Hyperledger Fabric instance, or run the following commands to pull and tag the version 1.3.0 images:

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

## Adding identities

When you add a connection, you must submit a connection profile, certificate, and private key. The identity that is associated with the certificate/private key pair will be set as the default identity to use when connecting to that Hyperledger Fabric instance.

To add an identity to a connection that is already established:

1. Click the cog in the lower left of Visual Studio Code.
2. Select **Settings**. In the _User Settings_ pane, the details of your connections and the identities associated with them will be listed in the following format:

    ```
    {
        "connectionProfilePath": "filepath/to/connectionprofile1.json",
        "name": "newconnection1",
        "identities": [
            {
                "certificatePath": "filepath/to/certificate.pem",
                "privateKeyPath": "filepath/to/key.pem"
            }
        ]
    }
    ```

3. Additional identities can be added as objects within the array by using the following format:

    ```
    "identities": [
        {
            "certificatePath": "filepath/to/certificate.pem",
            "privateKeyPath": "filepath/to/key.pem"
        },
        {
            "certificatePath": "second/filepath/to/certificate.pem",
            "privateKeyPath": "second/filepath/to/key.pem"
        }
    ]
    ```
    {:codeblock}

4. Save the _User Settings_. When connecting to an instance of Hyperledger Fabric with multiple identities in your local user settings, a dropdown will be available to select the identity to use to connect.

New identities can also be added by right-clicking a connection, and entering the paths to the certificate and private key.

## Exploring connections

After connecting to a Hyperledger Fabric instance, you can see a list of the available channels and peers in those channels in the _Blockchain Explorer_ pane. Spend a minute exploring your Hyperledger Fabric instance to get familiar with the structure. Listed first are the channels within a connection, under a channel is a list of the peers that are members of that channel, and a list of smart contract packages instantiated on that channel. Under each peer in the list is a list of all smart contract packages installed on that peer. You can also see the versions of smart contract packages that are installed or instantiated.

## Packaging a smart contract

When a smart contract is ready to be deployed, it must first be packaged. To package a smart contract, complete the following steps:

1. In Visual Studio Code, navigate to the _Blockchain Explorer_ panel.
2. In the _Smart Contract Packages_ pane, click the + icon. If you have a smart contract project open in the file viewer, it will be automatically packaged, and will be shown in the _Smart Contract Packages_ pane. If you have multiple smart contract folders open, you will be asked which one to package. If you have no smart contract folders open, you'll get an error message.

## Installing smart contract packages

After connecting to an instance of Hyperledger Fabric, you can install and instantiate smart contract packages on the peers.

1. In the _Blockchain Explorer_ pane, navigate to the peer you wish to install the smart contract package on.
2. Right-click the peer to install the smart contract package on, and select **Install Smart Contract**.

## Instantiating smart contract packages

To begin running on a channel, an installed smart contract package must first be instantiated.

1. Connect to the Hyperledger Fabric instance where the smart contact package is installed.
2. Right-click on the channel you wish to instantiate the smart contract package on and click **Instantiate Smart Contract**.
3. Select the smart contract package and version to be instantiated. The smart contract package must be installed on a peer that is a member of this channel.
4. Enter the name of the instantiate function in your smart contract.
5. Enter any arguments required by your instantiate function.

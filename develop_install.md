---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Installing a development environment
{: #installing-a-development-environment}

Follow these instructions to obtain the {{site.data.keyword.blockchainfull}} Platform: Develop development tools for creating and testing business networks. To provide flexibility and enable the maximum number of dev, test and deployment scenarios, the development tools are delivered as npm modules, controlled from the command line.

## Before you begin

Ensure that you install the following prerequisites:

- Ubuntu Linux 14.04 / 16.04 LTS (both 64-bit), or Mac OS 10.12
- Node v8.9 or higher (v9 is not supported)
- npm v5.x
- git v2.9.x or higher
- Python v2.7.x
- Docker Engine v17.03 or higher
- Docker-Compose v1.8 of higher
- A code editor of your choice, like VSCode.

If you are using VSCode, there is a Hyperledger Composer VSCode extension available [here](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).


## Step One: Install the command line tools

The command line tools include **composer-cli**, the main {{site.data.keyword.blockchainfull_notm}} Platform: Develop command line module; **generator-hyperledger-composer**, **composer-rest-server**, and **Yeoman**, these modules are used to generate business networks, bespoke REST servers, and Angular applications.

1. Install **composer-cli** by using the following command:

        npm install -g composer-cli@next

2. Install **composer-rest-server** by using the following command:

        npm install -g composer-rest-server@next

    Use the **composer-rest-server** module to create a REST Server on your machine to expose your business networks as RESTful APIs.

3. Install **generator-hyperledger-composer** by using the following command:

        npm install -g generator-hyperledger-composer@next

    Use **generator-hyperledger-composer** to generate skeleton business network structures, models, and an Angular application.

4. Yeoman is a tool for generating applications, which utilises `generator-hyperledger-composer`:

        npm install -g yo

## Step Two: Install a local Playground

The Hyperledger Composer playground is a user interface which can be connected to a real {{site.data.keyword.blockchain}} or used as a simulated environment for testing a business network. Install the Playground using the following command:

        npm install -g composer-playground@next

## Optional: Set up your IDE

Playground can be used to develop, edit, and test business networks, however, if you wish to develop a business network in an IDE, there is a VSCode extension available which adds syntax highlighting for the Hyperledger Composer modelling language.

1. Install VSCode from this URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Open VSCode, go to Extensions, then search for and install the Hyperledger Composer extension from the marketplace.

## Step Three: Install a local Hyperledger Fabric

By deploying a local Hyperledger Fabric instance, you can fully test access control rules and business networks.

1. Create the `fabric-tools` directory using the following command:

        mkdir ~/fabric-tools && cd ~/fabric-tools

In a directory of your choice (we will assume `~/fabric-tools`), get the `.tar.gz` file that contains the tools to install Hyperledger Fabric:

2. Download the `tar.gz` file containing tools for installing Hyperledger Fabric in a Docker environment.

        curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz

    A `.zip` is also available. Replace the `.tar.gz` file with `fabric-dev-servers.zip` and the `tar -xvf` command with a `unzip` command in the preceding snippet.

3. Use the following commands to download a local Hyperledger Fabric v1.1 runtime:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./downloadFabric.sh

## Step Four: Starting the Hyperledger Fabric instance

The `fabric-tools` directory contains a set of scripts that control the downloaded Hyperledger Fabric instance. The `./createPeerAdminCard.sh` command must be run to generate a business network card which can be used to deploy the business network to the Docker hosted Hyperledger Fabric instance.

1. Run the following commands to start the Hyperledger Fabric v1.1 instance:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./startFabric.sh
        ./createPeerAdminCard.sh

## Optional: Connect to Hyperledger Fabric instance using the Playground

To begin developing a business network, use the Hyperledger Composer Playground or your IDE.

1. To start the Playground, run the following command:

        composer-playground

    The Playground will open at the following URL: http://localhost:8080/login. The **PeerAdmin@hlfv1** card created in the previous step can be used to deploy a business network.


## Starting and stopping Hyperledger Fabric

There are a set of scripts in the `fabric-tools` directory which control the Hyperledger Fabric instance. You can stop and start the runtime using `~/fabric-tools/stopFabric.sh`, and `~/fabric-tools/startFabric.sh`.

The `~/fabric-tools/teardownFabric.sh` will require you to create a new PeerAdmin card when the environment is next started.

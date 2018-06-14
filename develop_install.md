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
{:tip: .tip}

# Installing a development environment
{: #installing-a-development-environment}

Follow these instructions to obtain the {{site.data.keyword.blockchainfull}} Platform: Develop development tools for creating and testing business networks. To provide flexibility and to enable the maximum number of dev, test, and deployment scenarios, the development tools are delivered as npm modules, which are controlled from the command line.

Different versions of some components are required for use with Starter Plan and Enterprise Plan. To use the development environment with Starter Plan, the modules must be installed at version `0.19.x`. To use the development environment with Enterprise Plan, the modules must be installed at version `0.16.x`

## Before you begin

Ensure that you install the following prerequisites:

- Ubuntu Linux 14.04 / 16.04 LTS (both 64-bit), or Mac OS 10.12
- Node v8.9 or higher (v9 is not supported)
- npm v5.x
- git v2.9.x or higher
- Python v2.7.x
- A code editor of your choice, like VSCode.

If you are using Enterprise Plan, install the following additional prerequisites:

- Docker Engine v17.03 or higher
- Docker-Compose v1.8 of higher

If you are using VSCode, there is a Hyperledger Composer VSCode extension available [here](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).

## Step One: Install the command line tools

The command line tools include **composer-cli**, the main {{site.data.keyword.blockchainfull_notm}} Platform: Develop command line module; **generator-hyperledger-composer**, **composer-rest-server**, and **Yeoman**, these modules are used to generate business networks, bespoke REST servers, and Angular applications.

1. For use with Starter Plan, install **composer-cli** by using the following command:

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

    For use with Enterprise Plan, install **composer-cli** by using the following command:

    ```
    npm install -g composer-cli@0.16.x
    ```
    {:codeblock}

2. For use with Starter Plan, install **composer-rest-server** by using the following command:

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

    For use with Enterprise Plan, install **composer-rest-server** by using the following command:

    ```
    npm install -g composer-rest-server@0.16.x
    ```
    {:codeblock}

    Use the **composer-rest-server** module to create a REST Server on your machine to expose your business networks as RESTful APIs.

3. For use with Starter Plan, install **generator-hyperledger-composer** by using the following command:

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    For use with Enterprise Plan, install **generator-hyperledger-composer** by using the following command:

    ```
    npm install -g generator-hyperledger-composer@0.16.x
    ```
    {:codeblock}

    Use **generator-hyperledger-composer** to generate skeleton business network structures, models, and an Angular application.

4. Yeoman is a tool for generating applications, which utilizes `generator-hyperledger-composer`:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Step Two: Install a local Playground

The Hyperledger Composer playground is a user interface that can be connected to a real {{site.data.keyword.blockchain}} or used as a simulated environment for testing a business network. For use with Starter Plan, install the playground by using the following command:

```
npm install -g composer-playground@0.19.x
```
{:codeblock}


For use with Enterprise Plan, install the playground by using the following command:


```
npm install -g composer-playground@0.16.x
```
{:codeblock}

## Optional: Set up your IDE

You can use the playground to develop, edit, and test business networks. However, if you want to develop a business network in an IDE, there is a VSCode extension available, which adds syntax highlighting for the Hyperledger Composer modelling language.

1. Install VSCode from this URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Open VSCode, go to Extensions, then search for and install the Hyperledger Composer extension from the marketplace.


## Enterprise Plan only: Install a local Hyperledger Fabric and connect to it with the Playground

By deploying a local Hyperledger Fabric instance, you can fully test access control rules and business networks. Ensure that you have started Docker before following these steps.

1. Create the `fabric-tools` directory by using the following command:

   ```
   mkdir ~/fabric-tools && cd ~/fabric-tools
   ```
   {:codeblock}

In a directory of your choice, for example, `~/fabric-tools`, get the `.tar.gz` file that contains the tools to install Hyperledger Fabric:

2. Download the `tar.gz` file that contains tools for installing Hyperledger Fabric in a Docker environment.

   ```
   curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
   tar -xvf fabric-dev-servers.tar.gz
   ```
   {:codeblock}

    A `.zip` is also available. Replace the `.tar.gz` file with `fabric-dev-servers.zip` and the `tar -xvf` command with an `unzip` command in the preceding snippet.

3. Use the following commands to download a local Hyperledger Fabric v1 runtime:

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./downloadFabric.sh
   ```
   {:codeblock}

   The `fabric-tools` directory contains a set of scripts that control the downloaded Hyperledger Fabric instance. You must run the `./createPeerAdminCard.sh` command to generate a business network card, which you can use to deploy the business network to the Docker that hosts Hyperledger Fabric instance.

4. Run the following commands to start the Hyperledger Fabric v1 instance:

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./startFabric.sh
   ./createPeerAdminCard.sh
   ```
   {:codeblock}

   To begin developing a business network, use the Hyperledger Composer playground or your IDE.

5. To start the playground, run the following command:

    ```
    composer-playground
    ```
    {:codeblock}

    The playground opens at the following URL: http://localhost:8080/login. The **PeerAdmin@hlfv1** card created in the previous step can be used to deploy a business network.


### Starting and stopping Hyperledger Fabric

There are a set of scripts in the `fabric-tools` directory, which control the Hyperledger Fabric instance. You can stop and start the runtime by using `~/fabric-tools/stopFabric.sh` and `~/fabric-tools/startFabric.sh`.

The `~/fabric-tools/teardownFabric.sh` will require you to create a new PeerAdmin card when the environment is next started.

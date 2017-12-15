---

copyright:
  years: 2017
lastupdated: "2017-12-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Develop the network
{: #develop-the-network}

Developing {{site.data.keyword.blockchain}} solutions using the {{site.data.keyword.blockchainfull}} Platform leverages the Hyperledger Composer open source development toolset. Hyperledger Composer uses a bespoke modelling language, which is combined with JavaScript transactions, and access control rules to model a {{site.data.keyword.blockchain}} business network in full _before_ deploying anything to a real {{site.data.keyword.blockchain}}.
{:shortdesc}

This tutorial goes through the development of a {{site.data.keyword.blockchain}} solution that begins with creating and modelling a business network, and then deploys it to a running blockchain network.

The following steps outline the basic development of a {{site.data.keyword.blockchain}} solution that is hosted on  {{site.data.keyword.blockchainfull_notm}} Platform:

1. Create the data structure to contain the business network definition. The business network is defined in terms of assets, participants, and transactions by using the [Modelling Language](https://pages.github.ibm.com/Blockchain-WW-Labs/IBM-Composer/reference/cto_language.html).

2. Model the business network by defining the asset classes, participant classes, transaction classes, event classes, and access control rules that will be used after deployment.

3. Package the defined business network into a single deployable file.

4. Deploy the packaged business network to the {{site.data.keyword.blockchainfull_notm}} Platform. Once the business network is deployed, a REST server can be generated to allow integration with external applications.

## Before you begin

Before you begin, you need to run the following commands to install Yeoman (an open source generator tool), the Hyperledger Composer Playground, the Hyperledger Composer CLI, and the REST server generator:
- `npm install -g yo`  
- `npm install -g composer-cli`  
- `npm install -g generator-hyperledger-composer`  
- `npm install -g composer-rest-server`  
- `npm install -g composer-playground`  

## Step One: Creating the business network structure

A key concept for developing {{site.data.keyword.blockchain}} solutions is the **business network definition (BND)**. The BND defines the data model, transaction logic and access control rules for your blockchain solution. To create a BND,  you need to create a suitable project structure on disk.

The easiest way to get started is to use the Yeoman generator to create a skeleton business network. The Yeoman generator creates a directory that contains all of the components of a business network, which can be modified for your specific use-case.

1. Use Yeoman to create a skeleton business network. The following command requires a business network name, description, author name, author email address, license selection, and namespace:

        yo hyperledger-composer:businessnetwork

2. Enter `tutorial-network` for the network name, and desired information for description, author name, and author email.

3. Select `Apache-2.0` as the license.

4. Select `org.acme.biznet` as the namespace.

## Step Two: Defining the business network

A business network is made up of assets, participants, transactions, access control rules, and optionally events and queries. The skeleton business network that is created in step one includes a model file (`.cto`), which contains the class definitions for all assets, participants, and transactions in the business network. The skeleton business network also contains an access control document (`permissions.acl`) with basic access control rules, a script file (`logic.js`) that contains transaction processor functions, and a package file (`package.json`) that contains business network metadata.

### Modelling assets, participants, and transactions

The first document to update is the model file (`.cto`). This file is written using the [Hyperledger Composer Modelling Language](https://pages.github.ibm.com/Blockchain-WW-Labs/IBM-Composer/reference/cto_language.html). The model file contains the definitions of each class of asset, transaction, participant, and event. It implicitly extends the system model that is described in the modelling language documentation.

1. Open the `org.acme.biznet.cto` model file.

2. Replace the contents with the following:

        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }

3. Save your changes to `org.acme.biznet.cto`.

### Adding JavaScript transaction logic

In the model file, a `Trade` transaction was defined, which specifies a relationship to an asset and a participant. The transaction processor function file contains the JavaScript logic to execute the transactions that are defined in the model file.

The `Trade` transaction is intended to simply accept the identifier of the `Commodity` asset that is being traded, and the identifier of the `Trader` participant to set as the new owner.

1. Open the `logic.js` script file in the `lib` directory.

2. Replace the contents with the following:

        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }

3. Save your changes to `logic.js`.

### Adding access control

Access control is a key part of a business network. All business network must have an access control file that is called `permissions.acl`. Whenever any process is invoked in a deployed business network, the access control list is checked to ensure that the invoking identity or participant has can invoke the operation.

For the purposes of this tutorial, set up a simple ACL. Note that this should not be deployed to a production environment.

1. Create a `permissions.acl` file in the `tutorial-network` directory.

2. Add the following access control rules to `permissions.acl`:

        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }

3. Save your changes to `permissions.acl`.

## Step Three: Packaging the business network

After the business network is defined, it must be packaged into a deployable business network archive file (`.bna`).

1. Using the command line, navigate to the `tutorial-network` directory.

2. From the `tutorial-network` directory, run the following command:

        composer archive create -t dir -n .

After running the command, a business network archive file (`tutorial-network@0.0.1.bna`) is created in the `tutorial-network` directory.

## Step Four: Deploying the business network and generating a REST server

Deploy the business network to the {{site.data.keyword.blockchainfull_notm}} Platform using the setup, installation, and interacting instructions at https://ibm-blockchain.github.io/setup/.

After the business network has been deployed to the {{site.data.keyword.blockchainfull_notm}} Platform Developer Sandbox, a bespoke REST server can be generated. The REST server examines the contents of the business network and builds rest calls for the assets, transactions and participants of the network. The instructions for generating a REST server are included in the [Interacting with your blockchain](https://ibm-blockchain.github.io/interacting/) instructions.

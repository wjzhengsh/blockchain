---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-18"

keywords: Fabric SDKs, client application, enroll, register, chaincode

subcollection: blockchain

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
{:external: target="_blank" .external}

# Developing applications with the Fabric SDKs
{: #dev-app}

{{site.data.keyword.blockchainfull}} Platform provides APIs that you can use to connect applications to your blockchain network. You can use the network API endpoints in the connection profile to invoke your chaincode and update or query the channel-specific ledger on your peers. You can also use APIs in [the Swagger UI](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) to manage nodes, channels, and members of your network.
{:shortdesc}

You can use this tutorial to learn how to access the {{site.data.keyword.blockchainfull_notm}} Platform APIs and use them to enroll and register your application with the network. You will also learn how to interact with your network and issue transactions from your application. The tutorial is based on the [Writing Your First Application](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html){: external} tutorial in the Hyperledger Fabric documentation. You will use many of the same files and commands the **Writing Your First Application** tutorial, but will use them to interact with a network on {{site.data.keyword.blockchainfull_notm}} Platform. This tutorial describes each step of application development by using the Hyperledger Fabric Node SDK. You will also learn how to enroll and register users using the Fabric CA client as an alternative to using the SDK.

In addition to this tutorial, you can use sample applications and chaincode that {{site.data.keyword.blockchainfull_notm}} Platform provides as templates when you create your own business solutions. For more information, see [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications).

When you are ready to scale your application, visit the [Best practices for application development](/docs/services/blockchain/best_practices.html#best-practices-app).

## Prerequisites
{: #dev-app-prerequisites}

You need the following prerequisites before you can use the **Writing Your First Application** tutorial on {{site.data.keyword.blockchainfull_notm}} Platform.

- If you don't have a blockchain network on {{site.data.keyword.cloud_notm}}, you need to create one with either a Starter or Enterprise Membership Plan. For more information, see [Creating a Starter Plan network](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network) or [Creating an Enterprise Plan network](/docs/services/blockchain/get_start.html).

  After you enter the Network Monitor of your network, add at least one peer for your organization on the "Overview" screen. Then, create at least one channel in your network. For more information, see [Creating a channel](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel). **Note** that if you use a Starter Plan network, your network already has a channel with the name of `defaultchannel` that you can use to deploy chaincode.

- Install required tools to download Hyperledger Fabric samples and to use Node SDK.
  * [Curl](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl){: external} or [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git){: external}
  * [Node.js](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#node-js-runtime-and-npm){: external}

- Install Hyperledger Fabric samples by downloading the `fabric-samples` directory. You can follow the [getting started guide](https://hyperledger-fabric.readthedocs.io/en/release-1.2/install.html){: external} in the Hyperledger Fabric documentation.

- Navigate to the `fabric-samples` directory on your local machine.
  * Use the `git checkout` command to use the branch that matches your networks Hyperledger Fabric version. You can find your Fabric version by opening the [Network Preferences window](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) in your Network Monitor.
    - If your network is on Fabric version 1.2, you can use the main branch.
    - If your network is on Fabric version 1.1, run `git checkout v1.1.0`.
    - If your network is on Fabric version 1.0, run `git checkout v1.0.6`.

  * Navigate to `fabric-samples/fabcar`. You can make a copy of this directory and rename it so that you can try and test the sample application in a clean directory.

  * In the `fabcar` directory, run the `npm install` command to install the necessary packages to use the Fabric SDK, which include `fabric-client` and `fabric-ca-client`.

- Install and instantiate the fabcar chaincode on your channel by using the [Network Monitor](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc). You can find the fabcar chaincode in the `fabric-samples` folder under `fabric-samples > chaincode > fabcar > go`.

- Retrieve the Connection Profile of your network on the "Overview" screen of the Network Monitor. Save the Connection Profile into your `fabcar` directory and rename it to `creds.json`.

## Using the Fabric SDKs
{: #dev-app-fabric-sdks}

The Hyperledger Fabric SDKs provide a powerful set of APIs that enable applications to interact with blockchain networks. You can find the latest list of supported languages in the [Hyperledger Fabric SDK Community documentation](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks){: external}. It is recommended to use the Node SDK or the Java SDK with {{site.data.keyword.blockchainfull_notm}} Platform. You can learn more about the APIs that the SDKs provide in the SDKs' individual repositories.

This tutorial uses the [Node SDK](https://fabric-sdk-node.github.io/){: external} to register and enroll your application, and then use the application to issue transactions by invoking and querying chaincode. This tutorial describes the information that you need to provide to the SDK so that your application can connect to your blockchain network. It also introduces some of the APIs that you can use, and how the SDK interacts with and submits transactions to your the blockchain network.

## Adding network API endpoints to your application
{: #dev-app-api-endpoints}

You need to provide your application with the API endpoints of specific network resources, including orderer, CA, and peer nodes, in the blockchain network on {{site.data.keyword.cloud_notm}}. Your application can interact with the network through these API endpoints. You can find the API endpoints in the Connection Profile of your network. The Connection Profile is in JSON format and contains the API endpoint information and enrollIDs/secrets for your network resources.

1. Retrieve the API endpoint information of your network resources from your Network Monitor with one of the following methods:
  * On the "Overview" screen, click **Connection Profile**. The Connection Profile contains a complete set of API endpoint information of all your network resources.
    ![Connection Profile in Network Monitor](images/service_credentials.png "Connection Profile in Network Monitor")

  * If you have running chaincode in the network, you can get the API endpoint information that is specific to the chaincode. On the "Channels" screen, click the channel row on which the chaincode is running to open the specific channel screen. Then, locate the chaincode and click the **JSON** button.
    ![API endpoints per chaincode](images/channel_chaincode.png "API endpoints per chaincode")

2. Locate the API endpoint information of your network resources, which is similar to the URL of `peer1-org1` row in the following example:
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```

You might want to target network resources outside of your organization with your application. For example, if a chaincode [endorsement policy](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-endorsement-policy) requires endorsements from other organizations on the channel, then you need to send the transaction to enough of those organization's peers to comply with the policy. [Service Discovery](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html){: external} in Hyperledger Fabric is not supported for Starter Plan or Enterprise Plan. You will need to obtain the endpoint information of peers and the accompanying TLS certificates of other organizations using the "peers" section of your Connection Profile. You can contact the admins of the other organizations about which peers they have joined to particular channels.{:note}

3. Plug the API endpoint information into a configuration file of your application as shown in the following example:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```

  You can also send [HEAD requests](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-nodes) to these endpoints to check the availability of your network resources.

  If you are using the Fabric SDKs, you can also connect to your network using your Connection Profile. This tutorial provides the endpoint information of your network to the SDK manually. However you can find a tutorial and guidance on [using your connection profile with the SDK](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) in a later section.

## Enrolling your application
{: #dev-app-enroll}

Before you connect an application to your network on {{site.data.keyword.blockchainfull_notm}} Platform, you need to prove the authenticity of your application to the network. We won't delve into the details of x509 certificates and public key infrastructure, though you can learn more by visiting the [Managing certificates on {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates) tutorial. Simply speaking, the communication flows in Fabric use sign/verify operations at every touchpoint. Therefore, any application that sends calls, such as ledger queries or updates, to the network needs to sign payloads with their private key and attach a properly signed x509 certificate for verification purposes. **Enrollment** is the process of generating the necessary keys and certs from the appropriate Certificate Authority. After the enrollment, your application is ready to communicate with the network.

This section explains how to retrieve the keys and certs with the Fabric Node SDK by using the sample code that is part of the **Writing Your First Application** tutorial. You can only generate certificates using an identity that has been registered with your Certificate Authority. The tutorial below first enrolls using an admin identity that has already been registered with your CA. It then uses those certificates to register a new client identity. The tutorial enrolls again using the new identity, and uses those certificates to submit transactions to the network. <!---You can find an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

You can also use the "Certificate Authority" screen of the Network Monitor to generate certificates, and use those certificates to interact with the network. To learn how, visit [Generating certificates using the Network Monitor](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel). You can also learn how to use the [Fabric CA client](/docs/services/blockchain/certificates.html#managing-certificates-enroll-register-caclient) from the command line to generate certificates and register users in the [Managing certificates](/docs/services/blockchain/certificates.html#managing-certificates) tutorial.

### Enrollment using the Fabric SDK
{: #dev-app-enroll-sdk}

From the `fabcar` directory in your `fabric-samples` folder, open the `enrollAdmin.js` file in a text editor.

1. The file first creates an instance of the Fabric client.
  ```
  var fabric_client = new Fabric_Client();
  ```
  {:codeblock}

2. The file then creates a key-value store (KVS) to manage your certificates. The SDK uses the [KeyValueStore](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html){: external} class to create the key-value store, and the [CryptoSuite](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html){: external} class to perform the crypto computations. You can see the relevant code block below.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. After the definition of the KVS, you can use a few methods from the [Fabric Client](https://fabric-sdk-node.github.io/Client.html){: external} class and Fabric-CA-Client API <!---[FabricCAServices](https://fabric-sdk-node.github.io/FabricCAServices.html){: external} ---> to communicate with the CA Server. You need to provide the SDK the name and URL of your Certificate Authority. Open the **Connection Profile** JSON file from your **Overview** screen in the Network Monitor, and find the following variables under the `certificateAuthorites` section:
  * URL for CA: ``url`` under `certificateAuthorities`
  * Admin user ID: ``enrollId``
  * Admin password: ``enrollSecret``
  * CA name: `caName`

  **Edit** the relevant lines in the `enrollAdmin.js` file with this information in the following way:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  For example:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```

  The Fabric client then checks whether your application is already enrolled. **Edit** the line below with the `enrollID` from your connection profile:
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. You need to send the "enroll" call to the CA Server. Your `admin` is already registered with your network. The enroll call retrieves a private key and a public key wrapped in an x509 cert and signed by the targeted CA. This wrapped and signed cert is called the signCert. The signCert allows network members to verify calls that originate from the client. You need to provide your org name, password, and MSP ID from your credentials file. In the `certificateAuthorities` section of your network credentials, find your `enrollID`, `enrollSecret`, and `x-mspid`. **Edit** the code block below with those values and replace the relevant section of your file.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. Save the `enrollAdmin.js` file.

In the `fabcar` directory, enroll the admin by issuing the following command:
```
node enrollAdmin.js
```
{:codeblock}

The enrollment command generates the signCert and exports it into a folder named `hfc-key-store`. The future files in this tutorial will look for your certificates in this folder. If you can find the admin certs in the `hfc-key-store` folder, the enroll command works.

If you want to [operate your network using the SDK](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk), you need to upload your admin signCert to the {{site.data.keyword.blockchainfull_notm}} Platform. You can find your admin signCert in the `hfc-key-store` folder. Open the `admin` file, and copy the certificate inside the quotation marks after the `certificate` field. Use to a tool or text editor to convert the certificate into PEM format. You can then upload the admin certificate to your blockchain network from the Network Monitor. For more information about adding certificates, see [the "Certificates" tab of "Member" screen](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) in the Network Monitor. This is not necessary if you are only using the SDK to invoke or query chaincode.

## Registering your application
{: #dev-app-register}

After generating the client-side certificates, you need to register your application with the network Certificate Authority. Registration adds your application to the list of components that the network can recognize. A best practice is to register your application as a separate identity rather than use the `admin` to sign requests.

### Registering using the SDK
{: #dev-app-register-sdk}

You can use the `registerUser.js` file to register and enroll the application as `user1` by using your `admin` signCert. Open `registerUser.js` in a text editor.

1. Provide the CA URL and name to a new instance of the Fabric CA client.

2. Provide the enrollID to the `getUserContext` method from the [Fabric Client class](https://fabric-sdk-node.github.io/Client.html){: external} to check whether your `admin` is enrolled and allowed to issue this request. **Edit** relevant code block in your file based on the sample below:
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. Use the **Fabric CA client** to register and enroll the user with the CA, and then use the **Fabric client** to create the new signCert. **Edit** the following block with your MSP ID and your organization affiliation. You can find your `x-affiliations` in the certificate authorities section of your network credentials and use any affiliation listed. Add the name of the user that you want to create. The fabcar sample uses `user1` by default.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. Save the `registerUser.js` file.

Run the `node registerUser.js` command to register and enroll `user1`. If you can find the `user1` certificates in the `hfc-key-store` folder, the command works. You can only register an identity once. If you experience a problem, try running `registerUser.js` with a new user name.

### Registering using the Network Monitor

Alternatively, you can register and enroll your client application using the Network Monitor **Certificate Authority** tab. Refer to this [information](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-ca) for more instructions.

## Issuing transactions by invoking and querying chaincode
{: #dev-app-invoke-query}

Your application needs to interact with the full blockchain network to submit a transaction.

1. The application sends a transaction proposal to be endorsed by peers on the channel.
2. Endorsing peers return endorsed transaction to the application.
3. The application sends the endorsed transaction to the ordering service to add the transaction to the ledger.

For more information about the complete transaction flow, see [Transaction Flow](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html){: external} in the Hyperledger Fabric documentation. After you getting started with this tutorial, visit the [application connectivity and availability](/docs/services/blockchain/best_practices.html#best-practices-app-connectivity-availability) section for tips on managing how your SDK interacts with the network.

The following samples demonstrate how the Node SDK sets up the network topology, defines the transaction proposal, and then submits the transaction to the network. You can use the `invoke.js` file to invoke functions within the `fabcar` chaincode. These functions allow you to create and transfer assets on the blockchain ledger. This tutorial uses the `initLedger` function to add new data to your channel, and then uses the `query.js` file to query the data.

### Invoking chaincode
{: #dev-app-invoke}

Open the `invoke.js` file in a text editor.

1. Add `var creds = require('./creds.json')` to the top of the file. This code line allows the `invoke.js` file to read information from the `creds.json` credentials file.

2. Use the [Fabric Client](https://fabric-sdk-node.github.io/Client.html){: external} class to set up the fabric network by using the API endpoints of your network resources. This step defines the channel and peers that your client will submit the proposal to, and the ordering service that the SDK will then send the endorsed transaction to. **Edit** the relevant code block below. The line `creds.peers["org1-peer1"].url` imports the peer url from your credentials file.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  The new peer and orderer variables open gRPC connections to your blockchain network. For more information about managing these connections, see [Opening and closing network connections](/docs/services/blockchain/best_practices.html#best-practices-app-connections).

  When you add the peer URL to the `fabric_client.newPeer` method, you also import the relevant TLS certificates from your connection profile by using the code snippet below. You did the same when you added the ordering service URL. You need to use these TLS certs to authenticate communication with your network.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  If the endorsement policy requires that transactions be endorsed by other organizations in the channel, you need to add those organizations peers using the `newPeer()` and `channel.addPeer()` methods when you set up the network. The organizations send need to send you the list of the peers they have joined to a particular channel. The endpoint information and TLS certs will be available in the connection profile. The SDK will send the transaction to all the peers added to the channel.

  You can also add additional peers belonging to your organization that are joined to the channel as a step to [making your application highly available](/docs/services/blockchain/best_practices.html#best-practices-ha-app). This will provide the SDK with a failover in the event one of your peers is disrupted.

3. After setting up the fabric network, and importing the application identity and signCert from the registration step, the `invoke.js` file defines the proposal that you will submit to the network. You can use the `initLedger` function in the `fabcar` chaincode to add some initial data to your ledger. You can also edit the code block to invoke other functions that you can find in the `fabcar` chaincode.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'defaultchannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  After defining the request, you can send a [transaction proposal](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal){: external} to the peers on the channel. After the proposal is returned from the peers, you can [send the transaction](https://fabric-sdk-node.github.io/Channel.html#sendTransaction){: external} to the ordering service.

4. You can add an event service to make the transaction flow more efficient. **Edit** the following section:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Although the sample uses a peer-based event service, you should use a channel-based listener. You can learn more in the [managing transactions](/docs/services/blockchain/best_practices.html#best-practices-managing-transactions) section and the [Node SDK documentation](https://fabric-sdk-node.github.io/tutorial-channel-events.html){: external}.

5. By default, `invoke.js` submits the transaction as `user1`. You can edit the `invoke.js` file if you registered a different name.

After you finish editing the file, issue the `node invoke.js` command to submit the transaction to the network. You should see the following as a confirmation that the invocation was successful.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

This indicates that your application successfully invoked your chaincode and added data to the ledger.

### Querying chaincode
{: #dev-app-query}

Now you can use `query.js` to read the ledger. Open the `query.js` file in a text editor.

1. Add `var creds = require('./creds.json')` to the top of the file.
2. Update the file with the channel name and the endpoint information of the peer. Because this operation reads only data that is stored on the peer, you do not need to add the endpoint information of the ordering service. `query.js` also assumes that you send the proposal as `user1`.
  ```
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

After you finish editing the file, issue the `node query.js` command and you should see the list of cars on your ledger, which is similar to the following example.
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

For more information about the fabcar application and the functions it uses, you can visit the full tutorial of [Writing Your First Application](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html){: external} in the Hyperledger Fabric documentation.

## Using your Connection Profile with the SDK
{: #dev-app-connection-profile}

Instead of importing the endpoint information of your network manually, you can have the SDK connect to your network by using the **Connection Profile** from the **Overview** screen of your Network Monitor. This streamlines the process of connecting to your certificate Authority for enrollment and registration. It also eliminates the need to define your fabric network before you submit a transaction. The SDK will find the peers and orderers on the relevant channel directly from the Connection Profile. You can find more information on how to use a Connection Profile in the [Node SDK documentation](https://fabric-sdk-node.github.io/tutorial-network-config.html){: external}.

We can use the `invoke.js` file as an example to see the efficiency of using a Connection Profile rather than manual endpoints. You can establish a new instance of the fabric client using the `loadFromConfig` class. Replace `var fabric_client = new Fabric_Client();` with the following code.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

Instead of setting up the fabric network by creating peers and orderers, and then adding them to a channel, you can use the following line to create a new channel.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

The SDK then adds the peers and ordering service that are defined on the channel using the Connection Profile. This makes writing applications more efficient, and makes it easier to update your applications as network members join, leave, and start new channels. Please refer to the [connection profile tutorial](https://fabric-sdk-node.github.io/tutorial-network-config.html){: external} in the Node SDK Documentation to learn about the additional steps involved. You can use of this [version of the fabcar tutorial](https://developer.ibm.com/tutorials/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/){: external} that uses a connection profile rather than manual endpoint connections.

[Service Discovery](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html){: external} for an endorsement policy in Hyperledger Fabric is not supported for Starter Plan or Enterprise Plan. However, you can send transactions to peers that are outside of your organization for endorsement by editing your Connection Profile. The Connection Profile already contains the endpoint information and TLS certificates of peers from other organizations on your {{site.data.keyword.blockchainfull_notm}} Platform network. Add the name of the peer to the relevant channel in the "channels" section of the profile to add the peer to the channel. You will need to contact the admins of the other organizations about which peers they have joined to particular channels.

## Generating certificates by using the Network Monitor
{: #dev-app-enroll-panel}

You can use the Network Monitor to generate certificates by using the admin identity, and then pass those certificates directly to the SDK. This means that you can get started interacting with the network quickly, without having to generate certificates using the SDK.

Navigate to the "Certificate Authority" panel in your Network Monitor. Click the **Generate Certificate** button next to your admin identity to get a new signCert and private key from your CA. The **Certificate** field contains the signCert, just above the **Private Key**. You can click the copy icon at the end of each field to copy the value. Save these certificates place where you can provide them to your application. **Note** that {{site.data.keyword.blockchainfull_notm}} Platform doesn't store these certificates. You need to safely save and store them.

The signCert and private key are sufficient to form a user context that can sign requests within the Node SDK. Use the [createUser](https://fabric-sdk-node.github.io/Client.html#createUser__anchor){: external} method of the Client class to create the user context object. Within the `creatUser` method, pass the identity name and mspid to the [user](https://fabric-sdk-node.github.io/global.html#UserOpts){: external} object, along with the paths to the private key and signCert to the [cryptoContent](https://fabric-sdk-node.github.io/global.html#CryptoContent){: external} object.

We can use the "Certificate Authority" panel and the `createUser` class as part of the developing applications tutorial as an example. If you have already gone through the tutorial, then you have installed and instantiated the `fabcar` chaincode, and added some data to your ledger. We can use the certificates to query the ledger as the `admin` user. Follow the instructions to generate certificates using the Network Monitor above if you have not already done so.

Save your private key as privateKey.pem and your signCert as certificate.pem in the same directory as `query.js`. Open `query.js` in a text editor. Add the following line to the top of the file:
```
var fs = require('fs');
```
Replace the following line that imports the user context from persistence,
```
return fabric_client.getUserContext('user1', true);
```
with the following code that creates a new user context by using our signCert and private key.
```
return fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
The snippet above reads your certificates directly to the `cryptoContent` class as PEM files. The username will be `admin`, since the certificates were generated using the `admin` identity. You can find your mspid in the `certificateAuthorites` section of your connection profile. Save the file and issue the command `node query.js`. If successful, the query will return the same results as before.

## (Optional) Operating your network using the SDK
{: #dev-app-operate-sdk}

You can also use the SDK to operate your blockchain network. This tutorial explains how you can use the SDK to join your peers to channels, install chaincode on your peers, and instantiate chaincode on channels. These steps are optional because you can also perform these operations using the Network Monitor or the APIs in the [Swagger UI](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) if all of your peers are being run on the {{site.data.keyword.blockchainfull_notm}} Platform.

You need to upload your admin signCert to the {{site.data.keyword.blockchainfull_notm}} Platform in order to complete these steps. You can find instructions on how to upload your signCert at the end of the [enrollment section](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk)

### Joining a channel
{: #dev-app-join-channel-sdk}

After your organization creates or joins a channel by using the Network Monitor or the APIs, you can use the SDK to join your peer to the channel.

1. [Fetch the genesis block](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock){: external} of the channel from the ordering service.
2. Pass the genesis block to the [join channel](https://fabric-sdk-node.github.io/Channel.html#joinChannel){: external} method to join your peer to the channel.

To use the `fabcar` sample to join a channel, use the `invoke.js` file as your starting point. You need to send this request as the admin rather than the application, so replace `user1` with `admin` in the `getUserContext` method. Starting where you define your chaincode invoke request at `var request = {`, replace the transaction flow with a channel join request based on the code snippet below from the [node SDK documentation](https://fabric-sdk-node.github.io/tutorial-channel-create.html){: external}.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId : tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```

Your signCert need to be added to the channel before you can fetch the genesis block. If you generated certificates after your organization joined the channel, you need to upload your signCert to the platform and then click the **Sync Certificates** button in the "Channels" screen. You might need to wait a few minutes for channel sync to complete before issuing the join channel command. For more information, see [Uploading signing certificates to {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs) in the [Managing certificates](/docs/services/blockchain/certificates.html#managing-certificates) tutorial.

### Installing a chaincode
{: #dev-app-install-cc-sdk}

You can use the [install chaincode](https://fabric-sdk-node.github.io/Client.html#installChaincode){: external} method from the [Fabric Client](https://fabric-sdk-node.github.io/Client.html){: external} class to install chaincode on your peer.

To use the `fabcar` sample to install the `fabcar` chaincode on your peer, use the `query.js` file as a baseline and edit it. You need to send this request as the admin rather than the application, so replace `user1` with `admin` in the `getUserContext` method. Replace the transaction proposal object with an install chaincode request using the example below:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'defaultchannel'
	 };
```
{:codeblock}

Send this object to `return fabric_client.installChaincode(request);`, instead of the `return channel.queryByChaincode(request);` line currently in the file.

### Instantiating a chaincode
{: #dev-app-instantiate-cc-sdk}

To instantiate the chaincode, you need to send an [instantiate proposal](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal){: external} to the peer, and then send a [transaction request](https://fabric-sdk-node.github.io/Channel.html#sendTransaction){: external} to the ordering service.

To use the `fabcar` sample to instantiate your chaincode, use the `invoke.js` file as your starting point. You need to send this request as the admin rather than the application, so replace `user1` with `admin` in the `getUserContext` method. Replace the transaction proposal object with an install chaincode request using the example below:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'defaultchannel',
    txId : tx_id
};
```
{:codeblock}

Send this request to `return channel.sendInstantiateProposal(request);` instead of the `return channel.sendTransactionProposal(request);` currently in the file. After sending the instantiate request to the channel, you then need to send the endorsed proposal as a transaction to the ordering service. This uses the same methods as sending a transaction, so you can keep the rest of the file unchanged. You might want to [increase the timeout value](/docs/services/blockchain/best_practices.html#best-practices-set-timeout-in-sdk) in the instantiate proposal. Otherwise the request may timeout before the platform can start the chaincode container.

Your signCert needs to be added to the channel before you can instantiate chaincode. If you generated certificates after you joined the channel, you need to upload your signCert to the platform and then click the **Sync Certificates** button in the "Channels" screen. You may need to wait a few minutes for channel sync to complete before issuing the instantiate chaincode command. To learn more, see [Uploading signing certificates to {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs) in the [Managing certificates](/docs/services/blockchain/certificates.html#managing-certificates) tutorial.

## Disconnecting your application from the network
{: #dev-app-disconnect-app}

Complete the following steps to remove the connection between your application and the blockchain network on {{site.data.keyword.cloud_notm}}.
1. Remove the API endpoint information from your application configuration file. For reference, see [Adding network API endpoints to your application](/docs/services/blockchain/v10_application.html#dev-app-api-endpoints).
2. Delete your chaincode container.
  1. In the "Channel" screen of the Network Monitor, locate the channel where your chaincode is installed.
  2. In the specific channel screen, locate the chaincode you want to disable.
  3. Click the **Delete** button, and click **Submit** in the chaincode deletion panel. Your chaincode container will be removed.
    ![Delete a chaincode](images/channel_chaincode.png "Delete a chaincode")

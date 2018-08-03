---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-03"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}

{:pre: .pre}

# Developing applications
{: #dev_app}

{{site.data.keyword.blockchainfull}} Platform provides APIs that you can use to connect applications to your blockchain network. You can use the network API endpoints in the connection profile to invoke your chaincode and update or query the channel-specific ledger on your peers. You can also use APIs in [the Swagger UI](howto/swagger_apis.html) to manage nodes, channels, and members of your network.
{:shortdesc}

You can use this tutorial to learn how to access the {{site.data.keyword.blockchainfull_notm}} Platform APIs and use them to enroll and register your application with the network. You will also learn how to interact with your network and issue transactions from your application. The tutorial is based on the [Writing Your First Application ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) tutorial in the Hyperledger Fabric documentation. You will use many of the same files and commands the **Writing Your First Application** tutorial, but will use them to interact with a network on {{site.data.keyword.blockchainfull_notm}} Platform. This tutorial describes each step of application development by using the Hyperledger Fabric Node SDK. You will also learn how to enroll and register users using the Fabric CA Client as an alternative to using the SDK.

In addition to this tutorial, you can use sample applications and chaincode that {{site.data.keyword.blockchainfull}} Platform provides as templates when you create your own business solutions. For more information, see [Deploying sample applications](howto/prebuilt_samples.html).

## Prerequisites
You need the following prerequisites before you can use the **Writing Your First Application** tutorial on {{site.data.keyword.blockchainfull_notm}} Platform.

- If you don't have a blockchain network on {{site.data.keyword.cloud_notm}}, you need to create one with either a Starter or Enterprise Membership Plan. For more information, see [Creating a Starter Plan network](get_start_starter_plan.html#creating-a-network) or [Creating an Enterprise Plan network](get_start.html#creating-a-network).

  After you enter the Network Monitor of your network, add at least one peer for your organization on the "Overview" screen. Then, create at least one channel in your network. For more information, see [Creating a channel](howto/create_channel.html#creating-a-channel). **Note** that if you use a Starter Plan network, your network already has a channel with the name of `defaultchannel` that you can use to deploy chaincode.

- Install required tools to download Hyperledger Fabric samples and to use Node SDK.
  * [Curl ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") or [Git ![External link icon](images/external_link.svg "External link icon")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- Install Hyperledger Fabric samples by downloading the `fabric-samples` directory. You can follow the [getting started guide ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html) in the Hyperledger Fabric documentation.

  After you download the `fabric-samples` directory in your local machine, navigate to `fabric-samples/fabcar`. You can make a copy of this directory and rename it so that you can try and test the sample application in a clean directory.

  In the `fabcar` directory, run the `npm install` command to install the necessary packages to use the Fabric SDK, which include `fabric-client` and `fabric-ca-client`.

- Install and Instantiate the fabcar chaincode on your channel using the [Network Monitor](howto/install_instatiate_chaincode.html#installing-a-chaincode). You can find the fabcar chaincode in the `fabric-samples` folder under `fabric-samples > chaincode > fabcar > go`.

- Retrieve the Connection Profile of your network on the "Overview" screen of the Network Monitor. Save the Connection Profile into your `fabcar` directory and rename it to `creds.json`.

## Using the Fabric SDKs
{: #using-the-fabric-sdks}

The Hyperledger Fabric SDKs provide a powerful set of APIs that enable applications to interact with blockchain networks. You can find the latest list of supported languages in the [Hyperledger Fabric SDK Community documentation ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community documentation"){:new_window}. It is recommended to use the Node SDK or the Java SDK with {{site.data.keyword.blockchainfull_notm}} Platform. You can learn more about the APIs that the SDKs provide in the SDKs' individual repositories.

This tutorial uses the [Node SDK ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/ "Node SDK"){:new_window} to register and enroll your application, and then use the application to issue transactions by invoking and querying chaincode. This tutorial describes the information that you need to provide to the SDK so that your application can connect to your blockchain network. It also introduces some of the APIs that you can use, and how the SDK interacts with and submits transactions to your the blockchain network.

## Adding network API endpoints to your application
{: #api-endpoints}

You need to provide your application with the API endpoints of specific network resources, including orderer, CA, and peer nodes, in the blockchain network on {{site.data.keyword.cloud_notm}}. Your application can interact with the network through these API endpoints. You can find the API endpoints in the Connection Profile of your network. The Connection Profile is in JSON format and contains the API endpoint information and enrollIDs/secrets for your network resources.

1. Retrieve the API endpoint information of your network resources from your Network Monitor with one of the following methods:
  * On the "Overview" screen, click **Connection Profile**. The Connection Profile contains a complete set of API endpoint information of all your network resources.
    ![Connection Profile in Network Monitor](images/service_credentials.png "Connection Profile in Network Monitor")

  * If you have running chaincode in the network, you can get the API endpoint information that is specific to the chaincode. On the "Channels" screen, click the the channel row on which the chaincode is running to open the specific channel screen. Then, locate the chaincode and click the **JSON** button.
    ![API endpoints per chaincode](images/channel_chaincode.png "API endpoints per chaincode")

2. Locate the API endpoint information of your network resources, which is similar to the URL of `peer1-org1` row in the following example:
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **Note**: You might want to target network resources outside of your organization with your application. For example, if a chaincode [endorsement policy](howto/install_instatiate_chaincode.html#specifying-chaincode-endorsement-policies) requires endorsements from other organizations on the channel, then you need to obtain the endpoint information of their peers and the accompanying TLS certificates. You can find this information in the peers section of Connection Profile. However, you need to contact the admin of the other orgs about which peers they have joined to particular channels.

3. Plug the API endpoint information into a configuration file of your application as shown in the following example:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  You can also send [HEAD requests](howto/monitor_network.html#monitor-nodes) to these endpoints to check the availability of your network resources.

  If you are using the Fabric SDKs, you can also connect to your network using your Connection Profile. This tutorial provides the endpoint information of your network to the SDK manually. However you can find a tutorial and guidance on [using your connection profile with the SDK](#using-your-connection-profile-with-the-sdk) in a later section.

## Enrolling your application
{: #enroll-app}

Before you connect an application to your network on {{site.data.keyword.blockchainfull_notm}} Platform, you need to prove the authenticity of your application to the network. We won't delve into the details of x509 and public key infrastructure because you can find plenty of [external resources ![External link icon](images/external_link.svg "External link icon")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window} for that. Simply speaking, the communication flows in Fabric use sign/verify operations at every touchpoint. Therefore, any application that sends calls, such as ledger queries or updates, to the network needs to sign payloads with their private key and attach a properly signed x509 certificate for verification purposes. **Enrollment** is the process of generating the necessary keys and certs from the appropriate Certificate Authority. After the enrollment, your application is ready to communicate with the network.

This section explains how to retrieve the keys and certs by using the Fabric SDK as part of the **Writing Your First Application** tutorial. You can also learn how to use the Fabric CA Client from the command line to enroll your application.

### Enrollment using the Fabric SDK
{: #enroll-app-sdk}

From the `fabcar` directory in your `fabric-samples` folder, open the `enrollAdmin.js` file in a text editor.

1. The file firstly creates an instance of the Fabric Client.
  ```
  var fabric_client = new Fabric_Client();
  ```

2. The file then creates a key-value store (KVS) to manage your certificates. The SDK uses the [KeyValueStore ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window}  class to create the key-value store, and the [CryptoSuite ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} class to perform the crypto computations. You can see the relevant code block below.
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

3. After the definition of the KVS, you can use a few methods from the [Fabric Client ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html "Fabric Client") class and Fabric-CA-Client API <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> to communicate with the CA Server. You will need to provide the SDK the name and URL of your Certificate Authority. Open the **Connection Profile** JSON file from your **Overview** screen in the Network Monitor, and find the following variables under the `certificateAuthorites` section:
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
  {:codeblock}

  The Fabric Client then checks whether your application is already enrolled. **Edit** the line below with the `enrollID` from your connections profile:
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

If you want to [operate your network using the SDK](#operate-sdk), you need to upload your admin signCert to the {{site.data.keyword.blockchainfull}} Platform. You can find your admin signCert in the `hfc-key-store` folder. Open the `admin` file, and copy the certificate inside the quotation marks after the `certificate` field. Use to a tool or text editor to convert the certificate into PEM format. You can then upload the admin certificate to your blockchain network from the Network Monitor. For more information about adding certificates, see [the "Certificates" tab of "Member" screen](v10_dashboard.html#members) in the Network Monitor. This is not necessary if you are only using the SDK to invoke or query chaincode.

### Enrollment using the Fabric CA Client
{: #enroll-app-caclient}

1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them. Move the executable `fabric-ca` client from the directory where it was downloaded into a directory where you can use it to run commands.

2. Download the TLS certs from {{site.data.keyword.cloud_notm}} depending on the service plan, location, and cluster that you use.
  - Root TLS Cert for Starter Plan  
    - US: [us01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Root TLS Cert for Enterprise Plan ![External link icon](images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Save the contents to a folder, for example ``$HOME/tls``. This step allows the data flowing to be encrypted on the wire.

3. Open the **Connection Profile** JSON file from your **Overview** screen in the Network Monitor, and find the following variables:
  * URL for CA: ``url`` under `certificateAuthorities`
  * Admin user ID: ``enrollId``
  * Admin password: ``enrollSecret``
  * CA Name: ``caName``

4. You can use the Fabric CA client to send an `enroll` call to the Certificate Authority by passing in the TLS certs path and the four strings above with the following command:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  You need to run this command in the directory where you move the Fabric CA Client to. A real call might look similar to the following example command:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. Find your admin certificate in `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. You can then upload the admin certificate to your blockchain network from the Network Monitor. For more information about adding certificates, see [the "Certificates" tab of "Member" screen](v10_dashboard.html#members) in the Network Monitor.

  You can also find CA root certificate and admin private key in the following directories:
  * CA root certificate: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * The admin private key: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

## Registering your application
{: #register-app}

After generating the client side certificates, you need to register your application with the network Certificate Authority. Registration adds your application to the list of components that the network can recognize. A best practice is to register your application as a separate identity rather than use the `admin` to sign requests.

### Registering using the SDK
{: #register-app-sdk}

You can use the `registerUser.js` file to register and enroll the application as `user1` by using your `admin` signCert. Open `registerUser.js` in a text editor.

1. Provide the CA URL and name to a new instance of the Fabric CA Client.

2. Provide the enrollID to the `getUserContext` method from the [Fabric Client ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html) to check whether your `admin` is enrolled and allowed to issue this request. **Edit** relevant code block in your file based on the sample below:
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

3. Use the **Fabric CA Client** to register and enroll the user with the CA, and then use the **Fabric Client** to create the new signCert. **Edit** the following block with your MSP ID and your organization affiliation. You can find your `x-affiliations` in the certificate authorities section of your network credentials and use any affiliation listed. Add the name of the user that you want to create. The fabcar sample uses `user1` by default.
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

### Registering using the Fabric CA Client
{: #register-app-caclient}

1. Issue the following command to find your affiliation and your organization name in your blockchain network.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  You should see information that is similar to the following example:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  Make a note of the second **affiliation** value, for example, `ibp.PeerOrg1`. You need to use this value in the command below.

2. Run the following command to register the peer.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  Specify a name and password for your peer and replace `name` and `password` with your peer name and password. Make a note of this information. You need to need it when you configure your peer. For example:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  You can only register an identity once. If you experience a problem, try a command with a new user name and password.

  When the command completes successfully, you should see information that is similar to the following example:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

## Issuing transactions by invoking and querying chaincode
{: #invoke-query}

Your application needs to interact with the full blockchain network to submit a transaction.

1. The application sends a transaction proposal to be endorsed by peers on the channel.
2. Endorsement peers return endorsed transaction to the application.
3. The application sends the endorsed transaction to the ordering service to add the transaction to the ledger.

For more information about the complete transaction flow, see [Transaction Flow ![External link icon](images/external_link.svg "External link icon")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Transaction Flow"){:new_window} in the Hyperledger Fabric documentation.

The following samples demonstrates how the Node SDK sets up the network topology, defines the transaction proposal, and then submits the transaction to the network. You can use the `invoke.js` file to invoke functions within the `fabcar` chaincode. These functions allow you to create and transfer assets on the blockchain ledger. This tutorial uses the `initLedger` function to add new data to your channel, and then uses the `query.js` file to query the data.

### Invoking chaincode
{: #invoke}

Open the `invoke.js` file in a text editor.

1. Add `var creds = require('./creds.json')` to the top of the file. This code line allows the `invoke.js` file to read information from the `creds.json` credentials file.

2. Use the [Fabric Client ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} class to set up the fabric network by using the API endpoints of your network resources. This step defines the channel and peers that your client will submit the proposal to, and the ordering service that the SDK will then send the endorsed transaction to. **Edit** the relevant code block below. The line `creds.peers["org1-peer1"].url` imports the peer url from your credentials file.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  When you add the peer URL to the `fabric_client.newPeer` method, you also import the relevant TLS certificates from your connection profile by using the code snippet below. You did the same when you added the ordering service URL. You need to use these TLS certs to authenticate communication with your network.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  If the endorsement policy requires that transactions be endorsed by other organizations in the channel, then you will need to add those organizations peers using the `newPeer()` and `channel.addPeer()` methods when you set up the network. The organizations send need to send you the list of the peers they have joined to a particular channel. The endpoint information and TLS certs will be available in the connection profile. The SDK will send the transaction to all the peers added to the channel.

  You can also add additional peers belonging to your organization that are joined to the channel as a step to [making your application highly available](#ha-app). This will provide the SDK with a failover in the event one of your peers is disrupted.

3. After setting up the fabric network, and importing the application identity and signCert from the registration step, the `invoke.js` file defines the proposal that you will submit to the network. You can use the `initLedger` function in the `fabcar` chaincode to add some initial data to your ledger. You can also edit the code block to invoke other functions that you can find in the `fabcar` chaincode.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  After defining the request, you can send a [transaction proposal ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") to the peers on the channel. After the proposal is returned from the peers, you can [send the transaction ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") to the ordering service.

4. You can add a listener service on the peer in your application by using an eventhub to make the transaction flow more efficient. **Edit** the following section:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  The sample uses a peer based event service, but we recommend using a channel based listener to distinguish between traffic from different channels on your peer. You can learn how to set up a [channel based event service ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "channel based event service"){:new_window} using the Node SDK Documentation.

5. By default, `invoke.js` submits the transaction as `user1`. You can edit the `invoke.js` file if you registered a different name.

After you finish editing the file, issue the `node invoke.js` command to submit the transaction to the network. You should see the following as a confirmation that the invocation was successful.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

This indicates that your application successfully invoked your chaincode and added data to the ledger.

### Querying chaincode
{: #query}

Now you can use `query.js` to read the ledger. Open the `query.js` file in a text editor.

1. Add `var creds = require('./creds.json')` to the top of the file.
2. Update the file with the channel name and the endpoint information of the peer. Because this operation reads only data that is stored on the peer, you do not need to add the endpoint information of the ordering service. `query.js` also assumes that you send the proposal as `user1`.
  ```
  var channel = fabric_client.newChannel('mychannel');
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

For more information about the fabcar application and the functions it uses, you can visit the full tutorial of [Writing Your First Application ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "writing your first application"){:new_window} in the Hyperledger Fabric documentation.

## Using your Connection Profile with the SDK
{: #using-your-connection-profile-with-the-sdk}

Instead of importing the endpoint information of your network manually, you can have the SDK connect to your network by using the **Connection Profile** from the **Overview** screen of your Network Monitor. This streamlines the process of connecting to your certificate Authority for enrollment and registration. It also eliminates the need to define your fabric network before you submit a transaction. The SDK will find the peers and orderers on the relevant channel directly from the Connection Profile. You can find more information on how to use a Connection Profile in the [Node SDK documentation ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-network-config.html "connection profile tutorial"){:new_window}.

Lets use the `invoke.js` file as an example to see the efficiency of using a Connection Profile rather than manual endpoints. You can establish a new instance of the the fabric client using the `loadFromConfig` class. Replace `var fabric_client = new Fabric_Client();` with the following code.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

Instead of setting up the fabric network by creating peers and orderers, and then adding them to a channel, you can use the following line to create a new channel.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

The SDK then adds the peers and ordering service that are defined on the channel using the Connection Profile. This makes writing applications more efficient, and makes it easier to update your applications as network members join, leave, and start new channels. Please refer to the [connection profile tutorial ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-network-config.html "connection profile tutorial"){:new_window} in the Node SDK Documentation to learn about the additional steps involved. You can use of this [version of the fabcar tutorial ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} that uses a connection profile rather than manual endpoint connections.

You can send transactions to peers that are outside of your organization for endorsement by editing your Connection Profile. The Connection Profile already contains the endpoint information and TLS certificates of peers from other organizations on your {{site.data.keyword.blockchainfull}} Platform network. Add the name of the peer to the relevant channel in the `channels` section of the profile to add the peer to the channel.

## Highly available applications
{: #ha-app}

As a high availability best practice, it is strongly recommended to deploy a minimum of two peers per organization for failover. To ensure high availability for your applications, install chaincode on both peers and add them to your channels. Then add both peer endpoints to the SDK using the newPeer method of the [Fabric Client ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} class when setting up your blockchain network before [submitting transactions](#invoke-query). If you have an Enterprise Plan network, you can also add another orderer to the channel using the newOrderer method. If you are using your [Connection Profile](#using-your-connection-profile-with-the-sdk) instead of adding network endpoints manually, ensure that your profile is up to date and that the additional peers and orderers have been added relevant channel in the `channels` section of the profile. The SDK will then add the components joined on the channel using the Connection Profile.

## Enabling mutual TLS
{: #mutual-tls}

If you are running Enterprise Plan networks that is at Fabric V1.1 level, you have the option of [enabling mutual TLS](v10_dashboard.html#network-preferences) for your applications. If you enable mutual TLS, you need to update your applications to support this function. Otherwise, your applications cannot communicate with your network.

In the Connection Profile, locate the `certificateAuthorities` section where you can find the following attributes that are necessary to enroll and get the certificates to communicate with your network by using mutual TLS.

- `url`: URL for connecting to the CA that can give out mutual TLS certificates
- `enrollId`: Enroll ID to use for getting a certificate
- `enrollSecret`: Enroll secret to use for getting a certificate
- `x-tlsCAName`: CA name to use for getting certificate that allows the application to communicate with Mutual TLS.

For more information about updating your applications to support mutual TLS, see [How to configure mutual TLS ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "mutual tls"){:new_window}.

## (Optional) Operating your network using the SDK
{: #operate-sdk}

You can also use the SDK to operate your blockchain network. This tutorial explains how you can use the SDK to join your peers to channels, install chaincode on your peers, and instantiate chaincode on channels. These steps are optional because you can also perform these operations using the Network Monitor or the APIs in the [Swagger UI](howto/swagger_apis.html) if all of your peers are being run on the {{site.data.keyword.blockchainfull}} Platform.

You need to upload your admin signCert to the {{site.data.keyword.blockchainfull}} Platform in order to complete these steps. You can find instructions on how to upload your signCert at the end of the [enrollment section](#enroll-app-sdk)

### Joining a channel
{: #join-channel-sdk}

After you create a channel using the Network Monitor or the API's, you can use the SDK to join your peer to the channel.

1. [Fetch the genesis block ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "fetch genesis block"){:new_window} of the channel from the ordering service.
2. Pass the genesis block to the [join channel ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window}  method to join your peer to the channel.

To use the `fabcar` sample to join a channel, use the `invoke.js` file as your starting point. You need to send this request as the admin rather than the application, so replace `user1` with `admin` in the `getUserContext` method. Starting where you define your chaincode invoke request at `var request = {`, replace the transaction flow with a channel join request based on the code snippet below from the [node SDK documentation ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "mutual tls"){:new_window}.
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
      txId :     tx_id
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
### Installing a chaincode
{: #install-cc-sdk}

You can use the [install chaincode ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} method from the [Fabric Client ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} class to install chaincode on your peer.

To use the `fabcar` sample to install the `fabcar` chaincode on your peer, use the `query.js` file as a baseline and edit it. You need to send this request as the admin rather than the application, so replace `user1` with `admin` in the `getUserContext` method. Replace the transaction proposal object with an install chaincode request using the example below:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

Send this object to `return fabric_client.installChaincode(request);`, instead of the `return channel.queryByChaincode(request);` line currently in the file.

### Instantiating a chaincode
{: #instantiate-cc-sdk}

To instantiate the chaincode, you need to send an [instantiate proposal ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} to the peer, and then send a [transaction request ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} to the ordering service.

To use the `fabcar` sample to instantiate your chaincode, use the `invoke.js` file as your starting point. You need to send this request as the admin rather than the application, so replace `user1` with `admin` in the `getUserContext` method. Replace the transaction proposal object with an install chaincode request using the example below:
```
var request = {
		targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
		channelNames: 'mychannel'
		};

```
{:codeblock}

Send this request to `return channel.sendInstantiateProposal(request);` instead of the `return channel.sendTransactionProposal(request);` currently in the file. After sending the instantiate request to the channel, you then need to send the endorsed proposal as a transaction to the ordering service. This uses the same methods as sending a transaction, so you can keep the rest of the file unchanged. You may want [increase the timeout value](#set-timeout-in-sdk) in the instantiate proposal. Otherwise the request may timeout before the platform can start the chaincode container.

## (Optional) Setting timeout values in Fabric SDKs
{: #set-timeout-in-sdk}

Fabric SDKs set default timeout values in client applications for events in the blockchain network. See the following example about default timeout settings in Fabric Java SDK. The file path is `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

However, you might need to change the default timeout values in your own application. For example, when your application invokes a transaction that needs more than 5000 ms, which is the default timeout value for event hub connection, to response, you might get a failing error because the invoke event ends at 5000 ms before the transaction completes. You can set the system property to overwrite the default values from your client application. Because the default values are initialized before you set the system property, the system property might not take effect. Therefore, you need to set the system property for timeout in a static construct in your client application. See the following example on changing timeout value for event hub connection to 15000 ms in Fabric Java SDK. The file path is `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

If you are using the Node SDK, you can specify the timeout values directly in the method called. As an example, you would use the line below to increase the timeout value for [instantiating a chaincode ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") to 5 minutes.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## (Optional) Using CouchDB indices
{: #couchdb-indices}

If your network uses CouchDB and you want to take advantage of the CouchDB indexing feature to improve the performance of CouchDB, you need to package the indexes with the chaincode.

For more information about CouchDB and how to set up indexes, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window}. in the Hyperledger Fabric documentation. You can also find an example that uses an index with chaincode in this [Fabric tutorial ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window}.

<!--# Next steps-->

## Hosting applications
{: #host-app}

You can host your application on your local file system or push it to {{site.data.keyword.Bluemix_notm}}. To push your application to {{site.data.keyword.Bluemix_notm}}, complete the following steps:
1. Install the [Cloud Foundry Command Line Installer ![External link icon](images/external_link.svg "External link icon")](https://github.com/cloudfoundry/cli/releases).  Test your installation with the `cf` command.
    * If your installation is successful, you should see a bunch of text output in your terminal.
    * If you see "command not found", your installation was either not successful or CF is not added to your system path.
2. Set up API endpoint and log in with your {{site.data.keyword.Bluemix_notm}} ID and password by issuing the following commands:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. Browse to the directory of your application, and push your application by issuing the following command. This can take several minutes depending on your application size. You can see logs from {{site.data.keyword.Bluemix_notm}} in your terminal. The logs cease when the application is successfully launched.
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	You can check your application logs by issuing one of the following commands:
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`

## Disconnecting your application from the network
{: #disconnect-app}

Complete the following steps to remove the connection between your application and the blockchain network on {{site.data.keyword.cloud_notm}}.
1. Remove the API endpoint information from your application configuration file. For reference, see [Adding network API endpoints to your application](#api-endpoints).
2. Delete your chaincode container.
  1. In the "Channel" screen of the Network Monitor, locate the channel where your chaincode is installed.
  2. In the specific channel screen, locate the chaincode you want to disable.
  3. Click the **Delete** button, and click **Submit** in the chaincode deletion panel. Your chaincode container will be removed.
    ![Delete a chaincode](images/channel_chaincode.png "Delete a chaincode")

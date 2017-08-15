---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Developing applications

With applications, you can invoke the chaincode to query or update a channel-specific ledger in your {{site.data.keyword.blockchain}} network. 
{:shortdesc}

## Setting up application development environment
You need to install software prerequisites to develop applications that can interact with the {{site.data.keyword.blockchain}} network on {{site.data.keyword.Bluemix}}.
{:shortdesc}

*	Git ([Git download page ![External link icon](../images/external_link.svg "External link icon")](https://git-scm.com/downloads){:new_window})
	
	Git is a version control tool to familiarize yourself with, both for chaincode development and software development in general.  Also, git bash, which is installed with git on Windows, is an excellent alternative to the the Windows command prompt.
 
	Use the following command to verify your Git installation.  You should see an output similar to the below:  
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([GoLang download page ![External link icon](../images/external_link.svg "External link icon")](https://golang.org/dl){:new_window})

	The GoLang installation installs a set of Go CLI tools, which are very useful to write chaincode.  For example, the `go build` command allows you to check that your chaincode actually compiles before you attempt to deploy it to a network.  At time of writing, this chaincode is known to build successfully with version `1.7.5`.

	Use the following command to verify your GoLang version.  You should see an output similar to the below:
	```
	$ go version
	go version go1.7.5 windows/amd64
	```
	{:screen}
	
	Follow the [Installation instructions ![External link icon](../images/external_link.svg "External link icon")](https://golang.org/doc/install){:new_window} to properly set the environment variables.  Check your `GOPATH` using the below command.  Note that your `GOPATH` does not need to match the example, it only matters that you have this variable set to a valid directory on your filesystem.   
	```
	$ echo $GOPATH
	C:\gopath
	```
	{:screen}

	You can then verify your GoLang installation by building GoLang code with the [hello world ![External link icon](../images/external_link.svg "External link icon")](https://golang.org/doc/install#testing){:new_window} example.

*	Node.js ([Node.js download page ![External link icon](../images/external_link.svg "External link icon")](https://nodejs.org/en/download/){:new_window}).  Choose a version between 6.9.5 < 7.  Node versions greater than 7 will cause errors when downloading the SDK modules.  

	Use the following commands to verify your Node.js installation.  You should see an output similar to the below:  
	```
	$ node -v
	v6.10.1
	
	$ npm -v
	3.10.10
	```
	{:screen}

## Generating the client-side certificates
We won't delve into the minutiae of x509 and public key infrastructure, there are plenty of external resources for that.  Suffice it to say that communication flows in Fabric implement sign/verify operations for every touchpoint.  As such, any client sending calls (i.e. transactions) to the network will need to sign payloads (private key) and attach a properly signed x509 certificate for verification purposes (signed cert).  The private key and signed certificate, along with with an MSP identifier and the Certificate Authority (CA) root certificate make up what is referred to as the "user context" object.  Again no need for extraneous details.  We will simply communicate with the appropriate Certificate Authority and retrieve the keys and certs that allow for the object to be formed - this process is referred to as enrollment.  After you form the user context object, it's as easy as calling an API from your application to "set" or "get" this user context.  At this point, the application (i.e. client) is equipped with all the necessary artifacts and is ready to communicate with the network.  We'll look at two approaches for retrieving the keys and certs.

### Command Line
This is the simpler of the two approaches.  First, follow the instructions to build the [Fabric CA client ![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html).  This step allows you to communicate with a CA Server and receive back properly formatted certificates and keys.  

Second, download the TLS certs from [your Bluemix ![External link icon](../images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) and save the contents to a folder, for example ``$HOME/tls``.  This step allows the data flowing to be encrypted on the wire.

Finally, open the **Service Credentials** JSON file from your **Overview** screen in the Network Monitor, and ascertain the following information:
* URL for CA: ``url`` under `certificateAuthorities`
* Admin user ID: ``enrollId``
* Admin password: ``enrollSecret``
* CA Name: ``caName``

Using the Fabric CA client, we can send an "enroll" call to our Certificate Authority by passing pass in the TLS certs path and the four strings above with the following command:  
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

The ``fabric-ca-client`` binary is placed in ``$GOPATH/bin``, so you will need to be in the proper location on your local machine when you run this command.  A real call might look similar to the following example command:  
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```

Your certificates should then be placed in the following directory structure:
* Client cert: ``$HOME/.fabric-ca-client/msp/signcerts/cert.pem``
* CA root certificate: ``$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem``
* The admin private key: ``$HOME/.fabric-ca-client/msp/keystore/<>_sk file``

### SDK
There are two Hyperledger repositories that contain excellent resources and scripts for understanding how to programatically interact with a Certificate Authortity.  The ``fabric-samples`` repo contains the "balance transfer" example and the ``fabric-sdk-node`` repo has a series of CA Services tests.  If you intend to issue your enrollment requests on the application side, then you will need to fully understand the APIs that need to be exposed within the ``fabric-ca-client`` and ``fabric-client`` packages.  Use these scripts and repos as a baseline for structuring your app.

Let's take a quick look at a few key snippets from the "balance transfer" example:

First we need to create our client object and set a key/value store instance where our certs and keys will be parked.  We can do this with a simple factory method - ``newCryptoSuite`` - that extends to the ``Client`` class from ``BaseClient``.  Here's a quick look at the code:

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

The common practice would be to export an environment variable defining the key/value path on your machine and pass it to the above function.  Now that we've defined our KVS, let's use the a few methods from the ``FabricCAServices`` class.  This class is an implementation of the Fabric CA client, and as such it will allow us to communicate with the CA Server.  First we need to pass some information to our CA client, namely the CA URL:

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

Next, we will actually send the "enroll" call to the CA Server.  This will return to the client a private key and a public key that has been wrapped in an x509 cert and signed by the targeted CA - we call this the signcert or enrollment certificate.  This enrollment cert or "eCert" is the key piece, because it allows network entities to verify transactions and calls originating from the client:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

And the final task is actually setting the crypto suite and building the user context:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

## Developing applications
You can develop your application in Javascript or Java, and leverage the available APIs in the Hyperledger Fabric Client SDKs to enable interaction between your application and your network.  An application needs to include at least the following information:
* Name and version of the chaincode to invoke.
* API endpoint information of your network resources, including orderers, CAs, and peers.
* Functions to query or update the ledger in the network.  If you want high availability, you need to consider node failover in your application.

You can find sample applications for the **Enterprise Plan** on {{site.data.keyword.Bluemix_short}} in [Sample applications](howto/sample_applications.html).  Use these sample chaincodes and applications as a template for creating your own business solution.

## Adding network service credentials to your application
You need to add service credentials of your network components to your application so that it can interact with your {{site.data.keyword.blockchain}} network on {{site.data.keyword.Bluemix_short}}.  If you don't have a {{site.data.keyword.blockchain}} network on {{site.data.keyword.Bluemix_short}}, see [Getting started with IBM Blockchain Platform](get_start.html). 

The service credentials are in JSON format and contain the API endpoint information and enrollIDs/secrets for your network components, including orderer, CA, and peer nodes.  Your application will interact with peers and other network components through these API endpoints.  

1. Retrieve the API endpoint information of your network components from your Network Monitor with one of the following methods:
	* To get the API endpoint information that is specific to a chaincode, in the specific channel screen, on which the chaincode is running, locate the chaincode and click the **JSON** button.
	![Service credentials per chaincode](images/channel_chaincode_detail.png "Service credentials per chaincode")
	* To get a complete set of API endpoint information about all your network components, click the **Service Credentials** button in the "Resources" screen.
	![Service credentials in network monitor](images/service_credentials.png "Service credentials network monitor")
	 
2. Locate the credentials and API endpoint information of your network resources.  See the following example:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**Note**: If you want to target additional peers in the network, for example, you require endorsement from a peer that does not belong to your organization, then you need to obtain the correct API endpoint information of those peers.  You also need to store the CA cert for the other organization to verify responses that return to your application. This information is not exposed in your service credentials, therefore you must contact the appropriate admin for the Bluemix Org and acquire this information in an out-of-band operation. The ordering service URL is common across the network; you don't need any member-specific information for ordering service.  

3. Plug the API endpoint information into a configuration file of your application as shown in the following example:  
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## Hosting applications
You can host your application on your local file system or push it to {{site.data.keyword.Bluemix_notm}}.  To push your application to {{site.data.keyword.Bluemix_notm}}, complete the following steps:
1. Install the [Cloud Foundry Command Line Installer ![External link icon](../images/external_link.svg "External link icon")](https://github.com/cloudfoundry/cli/releases).  Test your installation with the `cf` command.	
    * If your installation is successful, you should see a bunch of text output in your terminal. 
    * If you see "command not found", your installation was either not successful or CF is not added to your system path.	
2. Set up API endpoint and log in with your {{site.data.keyword.Bluemix_notm}} ID and password by issuing the following commands:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```  
3. Browse to the directory of your application, and push your application by issuing the following command.  This can take several minutes depending on your application size.  You will see logs from {{site.data.keyword.Bluemix_notm}} in your terminal; they will cease once the application has successfully launched.  
	```
	> cf push YOUR_APP_NAME_HERE
	```  
	You can check your application logs by issuing one of the following commands:
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## Disconnecting your application from the network
Complete the following steps to remove the connection between your application and the {{site.data.keyword.blockchain}} network on {{site.data.keyword.Bluemix_short}}.
1. Remove the API endpoint information from your application configuration file. For reference, see [Adding network service credentials to your application](#adding-network-service-credentials-to-your-application).
2. Delete your chaincode container.
	1. In the "Channel" screen of the Network Monitor, locate the channel where your chaincode is installed.
	2. In the specific channel screen, locate the chaincode you want to disable.
	3. Click the **Delete** button, and click **Submit** in the chaincode deletion panel. Your chaincode container will be removed.  
	![Delete a chaincode](images/channel_chaincode_detail.png "Delete a chaincode")

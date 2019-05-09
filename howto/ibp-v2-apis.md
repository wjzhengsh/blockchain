---

copyright:
  years: 2019
lastupdated: "2019-05-05"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:curl: #curl .ph data-hd-programlang='curl'}

# Building a network with APIs
{: #ibp-v2-apis}

The {{site.data.keyword.blockchainfull}} Platform 2.0 exposes RESTful APIs for you to create, import, edit, and delete your blockchain components, as well as to manage logging, notifications, and console settings. You can use the APIs, and the corresponding SDKs, to develop applications that interact with your blockchain network.
{: shortdesc}

This tutorial introduces the generic flow to build a blockchain network with {{site.data.keyword.blockchainfull_notm}} Platform APIs. For more information about each API, see [{{site.data.keyword.blockchainfull_notm}} Platform API reference doc ![External link icon](../images/external_link.svg "External link icon")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform API reference doc"){: new_window}.

These APIs are compatible with the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta v0.1.77 or higher only. To check the version, browse to `https://[your_console_url]/version.txt`, where *`[your_console_url]`* is the URL of your {{site.data.keyword.blockchainfull_notm}} Platform console. For example: https://1ee1869ffa6496d6bc1ce4b-optools.bp01.blockchain.cloud.ibm.com/version.txt
{:note}

## Before you begin
{: #ibp-v2-apis-prereq}

You must have an {{site.data.keyword.blockchainfull_notm}} Platform service instance in {{site.data.keyword.cloud_notm}} so that you can issue API calls to interact with the network. If you do not have a service instance yet, follow the [Getting started instructions](/docs/services/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks) to create one.

## Authentication
{: #ibp-v2-apis-authentication}

In order to use the APIs to access your blockchain network that you create with {{site.data.keyword.blockchainfull_notm}} Platform, your application needs to be able to authenticate with {{site.data.keyword.cloud_notm}} and connect to your service instance.

### Retrieving service credentials
{: #ibp-v2-apis-retrieve-service-credentials}

You need a basic authentication credential to ensure that you have access to your {{site.data.keyword.blockchainfull_notm}} Platform service instance on {{site.data.keyword.cloud_notm}}.

1. In your [{{site.data.keyword.cloud_notm}} resource list ![External link icon](../images/external_link.svg "External link icon")](https://cloud.ibm.com/resources), open the blockchain service instance that you created.
2. Click **Service credentials** from the left navigator.
3. Click the "New Credential" button on the **Service credentials** page to create a new credential.
  1. Give the credential a name, for example, *UseAPIs*.
  2. You can leave the "Add inline configuration parameter" field blank.
  3. Click the **Add** button.
4. After the new credential is created, click **View credentials** under the **ACTIONS** header of this credential. The contents of the credential looks similar to the following example:

  ```
  {
    "api_endpoint": "https://1088ac8563e44f5a92539d946733ad7e-optools.so01.blockchain.test.cloud.ibm.com"
    "apikey": "nvASKts6B6lMZGZUNTGVP7MLK2BujMnxz0plSPYaqc3R",
    "configtxlator": "https://1088ac8563e44f5a92539d946733ad7e-configtxlator.so01.blockchain.test.cloud.ibm.com",
    "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:blockchain:us-south:a/9d46037caee397faa45c55e087d26baa:1088ac85-63e4-4f5a-9253-9d946733ad7e::",
    "iam_apikey_name": "auto-generated-apikey-c1981f5c-cff0-464f-9a78-ed2f52e24d1a",
    "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
    "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/9d46037caee397faa45c55e087d26baa::serviceid:ServiceId-774190c5-9c37-4f68-8572-8d6e2aabbc7e",
  }
  ```

In the service credential, you can find the **API key** (`apikey`) and **API endpoint** (`api_endpoint`) that you need to retrieve an {{site.data.keyword.iamshort}} (IAM) access token.

### Retrieving an access token
{: #ibp-v2-apis-retrieve-token}

You can authenticate with {{site.data.keyword.blockchainfull_notm}} Platform by retrieving an access token from IAM. With an access token, you can work with the {{site.data.keyword.blockchainfull_notm}} Platform API on behalf of your service or application on or outside {{site.data.keyword.cloud_notm}}, without needing to share your personal user credentials.  

Call the {{site.data.keyword.iamshort}} API to retrieve your access token.

```cURL
curl -X POST \
  "https://iam.bluemix.net/identity/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  -d "grant_type=urn%3Aibm%3Aparams%3Aoauth%3Agrant-type%3Aapikey&apikey=<API_KEY>" \
```
{: codeblock}

In the request, replace `<API_KEY>` with the `apikey` value from your service credentials. The following truncated example shows the token output:

```
{
"access_token": "eyJraWQiOiIyM...",
"refresh_token": "...",
"expires_in":3600,
"expiration":1555558683,
"scope":"ibm openid"}
```
{: screen}

Use the full `access_token` value, prefixed by the `Bearer` token type, to programmatically work with your blockchain network by using the APIs.

Access tokens are valid for one hour, but you can regenerate them as needed. To maintain access to the service, refresh the access token for your API key on a regular basis by calling the {{site.data.keyword.iamshort}} API.   
{: tip}

## Forming your API request
{: #ibp-v2-apis-form-api-request}

When you make an API call to the service, structure your API request according to how you initially provisioned your {{site.data.keyword.blockchainfull_notm}} Platform service instance.

To build your request, pair an API endpoint with the appropriate authentication credentials in the following format:

```cURL
curl -X <API method> \
    <API_endpoint>/<API> \
    -H 'authorization: Bearer <access_token>' \
    -H 'Content-Type: application/json' \
    -d '<request body>' \
```
{: codeblock}

Example curl commands are provided for each API in the [{{site.data.keyword.blockchainfull_notm}} Platform API reference doc ![External link icon](../images/external_link.svg "External link icon")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform API reference doc").

Also, you can use the **Try it out** function in the API Reference doc to test your API calls before you add them to your applications.

## Limitations
{: #ibp-v2-apis-limitations}

You can import only existing CA, peer, and orderer nodes from other {{site.data.keyword.blockchainfull_notm}} Platform free v2.0 beta networks.

## Building a network by using APIs
{: #ibp-v2-apis-build-with-apis}

You can use APIs to create blockchain components in your instance of the {{site.data.keyword.blockchainfull_notm}} Platform. Use the following steps to build a blockchain network by using the {{site.data.keyword.blockchainfull_notm}} APIs.

1. Create a Certificate Authority (CA) by calling [`POST /ak/api/v1/kubernetes/components/ca`](/apidocs/blockchain?code=try#create-a-ca).

  Remember your input and the response, you will need them later.
  {: tip}

  You need to wait for the CA to start. It might take several minutes depending on environment. You can call `GET <ca_url>/cainfo` API to check your CA status. You will get repeated errors, then a `200` status code, which means you can proceed to the next step. Note that this API call times out after one minute.

2. Use your CA to register your component and administrator identities and generate the necessary certificates. You can use the Fabric CA client to complete the following steps:

  - [Set up the Fabric CA client](#ibp-v2-apis-config-fabric-ca-client).
  - [Generate certificates with your CA admin](#ibp-v2-apis-enroll-ca-admin).
  - [Register the new component with your CA](#ibp-v2-apis-config-register-component).
  - You also need to [register an organization administrator](#ibp-v2-apis-config-register-admin) and then [generate certificates for the admin](#ibp-v2-apis-config-enroll-admin) inside an MSP folder. You do not have to complete this step if you have already registered your admin identity.
  - [Register the new component with your TLS CA](#ibp-v2-apis-config-register-component-tls).

  You can also complete these steps by using your {{site.data.keyword.blockchainfull_notm}} Platform console. For more information, see [Creating and managing identities](/docs/services/blockchain/reference?topic=blockchain-ibp-console-identities.html#ibp-console-identities).

3. [Create an MSP definition for your organization](#ibp-v2-apis-msp) by calling [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?#import-a-membership-service-provide-msp).

4. [Build the configuration file](#ibp-v2-apis-config) that is required to create an orderer or peer. You must build a unique configuration file for each orderer or peer that you want to create.

5. Create an orderer by calling [`POST /ak/api/v1/kubernetes/components/orderer`](/apidocs/blockchain?code=try#create-an-orderer).

6. Create a peer by calling [`POST /ak/api/v1/kubernetes/components/peer`](/apidocs/blockchain?code=try#create-a-peer).

7. If you want to use the console to operate your blockchain components, you must import your administrator identity into your console wallet. Use the wallet tab to import the certificate and private key of your node admin into the console and create an identity. You then need to use the console to associate this identity with the components you created. For more information, see [Importing an admin identity into the {{site.data.keyword.blockchainfull_notm}} Platform console](#ibp-v2-apis-admin-console).

8. After you deploy your network, you can use the Fabric SDKs, the Peer CLI, or the console UI to create channels and install or instantiate smart contracts.

The service credential that is used for API authentication must have the `Manager` role in IAM to be able to create components. See the table in this topic on [user roles](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove) for more information.
{: note}

## Import a network by using APIs
{: #ibp-v2-apis-import-with-apis}

You can also use the APIs to import {{site.data.keyword.blockchainfull_notm}} components created by using the APIs or the {{site.data.keyword.blockchainfull_notm}} Platform console into another service instance of the {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Import a CA by calling [`POST /ak/api/v1/components/ca`](/apidocs/blockchain?code=try#import-a-ca).

  Remember your input and the response, you will need them later.
  {: tip}

  You need to wait for the CA to start. It might take several minutes depending on environment. You can call [`GET /components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) to check the CA status. You will get repeated errors before you get a `200` status code to go to next step. Note that this API call times out in one minute.

2. Import an organization MSP definition by calling [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp).

3. Import an orderer by calling [`POST /ak/api/v1/components/orderer`](/apidocs/blockchain?code=try#import-a-orderer).

4. Import a peer by calling [`POST /ak/api/v1/components/peer`](/apidocs/blockchain?code=try#import-a-peer).

5. If you plan to use the {{site.data.keyword.blockchainfull_notm}} Platform console to operate your blockchain components, you must import your component administrator identities into your console wallet. For more information, see [Importing an admin identity into the {{site.data.keyword.blockchainfull_notm}} Platform console](#ibp-v2-apis-admin-console).

6. After you deploy your network, you can use the Fabric SDKs, the Peer CLI, or the console UI to create channels and install or instantiate smart contracts.

The service credential that is used for API authentication must have the `Writer` role in IAM to be able to import components. See the table in this topic on [user roles](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove) for more information.
{: note}

## Operating your CA using the Fabric CA client
{: #ibp-v2-apis-config-fabric-ca-client}

You can use the Fabric CA client to operate your CAs. Run the following Fabric CA client commands to register your component and administrator identities and generate the necessary certificates.

### Set up the Fabric CA client
{: #ibp-v2-apis-setup-fabric-ca-client}

1. Download the [Fabric CA client ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Download Fabric CA client") to your local file system.

  The easiest way to get the Fabric CA client is to download all of the Fabric tool binaries directly. Navigate to a directory where you would like to download the binaries with your command line, and fetch them by running the following [Curl ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl") command.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
  ```
  {:codeblock}

  This command installs the binaries in a `bin/` directory.

2. Set the `PATH` path to the directory where you downloaded the Fabric tool binaries:

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  For example, if you installed the binaries in your home directory you would set your `PATH` as:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Create the folder where you will store the certificates of your CA admin.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. Set the value of the `$FABRIC_CA_CLIENT_HOME` environment variable to be the path where the CA client will store the generated [MSP](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) certificates. Ensure that you remove the configuration material that might be created by earlier attempts. If you didn't run the `enroll` command before, the `msp` folder and the `.yaml` file do not exist.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Retrieve the TLS certificate of your CA to be used by the Fabric CA client. If you are using the {{site.data.keyword.blockchainfull_notm}} Platform console, open the CA and click **Settings**, and look for the certificate in base64 format in the **TLS Certificate** field. If your are using the APIs, you can call [`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) and find the CA TLS certificate in the `"PEM"` field. If you created the CA by using the `Create a Fabric CA` API, you can also find the TLS certificate in the response body.

  You need to convert the certificate from base64 into PEM format to use it to communicate with your CA. Insert the base64 encoded string of the TLS certificate into command below. Ensure that you are in your `$HOME/fabric-ca-client` directory.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > tls.pem
  ```
  {:codeblock}

### Generate certificates with your CA admin
{: #ibp-v2-apis-enroll-ca-admin}

A **CA admin** identity was automatically registered for you when you created your CA. You can now use that admin name and password to issue an `enroll` command with the Fabric CA client to generate an MSP folder with certificates that can then be used to register other peer or orderer identities.

1. Ensure that you complete the steps to [set up the Fabric CA client](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) and that `$FABRIC_CA_CLIENT_HOME` is set to the directory where you want to store your CA admin certs.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. Run the following command to generate certificates:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles   <ca_tls_cert_path>
  ```
  {:codeblock}

  The `<enroll_id>`and `<enroll_password>` in the command are the `enroll_id` and `enroll_secret` you specified when you created the CA. Use the **Certificate Authority Endpoint URL** from your {{site.data.keyword.blockchainfull_notm}} Platform console or the `"ca_url"` returned by your API call as the value for `<ca_url_with_port>`. Leave off the `http://` at the beginning. The `<ca_name>` is the  **CA Name** from your console, or the `"ca_name"` returned by the APIs.

  The `<ca_tls_cert_path>` is the full path your CA TLS cert.

  A real call might look similar to the following example command:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```  
  {:codeblock}

**Tip:** If the value of the enrollment url, which is the `-u` parameter value, contains a special character, you need to either encode the special character or surround the url with the single quotes. For example, `!` becomes `%21`, or the command looks like:

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname ca
  ```
  {:codeblock}

  The `enroll` command generates a complete set of certificates, which is known as a Membership Service Provider (MSP) folder, that is located inside the directory where you set to `$HOME` path for your Fabric CA client. For example, `$HOME/fabric-ca-client/ca-admin`. For more information about MSPs and what the MSP folder contains, see [Membership Service Providers](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp).

  If the `enroll` command fails, see the [Troubleshooting topic](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting) for possible causes.

  You can run a tree command to verify that you have completed all of the prerequisite steps. Navigate to the directory where you stored your certificates. A tree command should generate a result similar to the following structure:

  ```
  cd $HOME/fabric-ca-client
  tree
  .
  ├── ca-admin
  │   ├── fabric-ca-client-config.yaml
  │   └── msp
  │       ├── cacerts
  │       │   └── 9-30-250-70-30395-SampleOrgCA.pem
  │       ├── IssuerPublicKey
  │       ├── IssuerRevocationPublicKey
  │       ├── keystore
  │       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
  │       ├── signcerts
  │       │   └── cert.pem
  │       └── user
  └── catls
      └── tls.pem    
  ```

### Registering the component identity with the CA
{: #ibp-v2-apis-config-register-component}

First, you need to register a component identity with your CA. Your component will use this identity to generate the certificates it needs when it is deployed.

1. [Generate certificates with your CA admin](#ibp-v2-apis-enroll-ca-admin) by using the Fabric CA client. Use these admin certificates to issue the following commands. Ensure that `$FABRIC_CA_CLIENT_HOME` is set to `$HOME/fabric-ca-client/ca-admin`.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. Issue the following command to find your affiliation and your organization name.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Your command might look like the following example:
  ```
  fabric-ca-client affiliation list --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  You should see information that is similar to the following example:
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  Make a note of the second **affiliation** value, for example, `org1.department1`. You need to use this value in the command below.

3. Run the following command to register the orderer or peer.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Create a name and password for the component and then use them to replace `name` and `secret`.  Make a note of this information. Set the `--id.type` to `orderer` if you are deploying an orderer, or set it to `peer` if you are deploying a peer. The command might look similar to the following example:

  ```
  fabric-ca-client register --caname ca --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  You need to save the `"enrollid"` and `"enrollsecret"` from the command above for when you create your configuration file.
  {: important}

  You can register an identity only once. If you experience a problem, try a command with a new user name and password. As a security measure, use each identity, and the accompanying enrollID and secret, to deploy only one peer. While you can use one **admin** identity for several components (this is our recommended deployment strategy), do not reuse peer IDs and passwords.

  When the command completes successfully, you should see information that is similar to the following example:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

### Registering your organization administrator
{: #ibp-v2-apis-config-register-admin}

You also need to create an admin identity that you can use to operate your network. You will use this identity to operate specific components, such as by installing a smart contract on your peer. You can also use this identity as an administrator of your organization and use it to create and edit channels.  

You need to register this new identity with your CA, and use it to generate an MSP folder. You can make this identity an organization administrator by adding its signCert to your organization MSP. You will also need to add the signCert to your configuration file so that it can be made the admin cert of the orderer or peer during deployment. You need to create only one admin identity for your organization. As a result, you need to complete these steps only once. You can use the signCert that you generated to deploy many peers or orderers.

Ensure that your `$FABRIC_CA_CLIENT_HOME` is set to the path to the MSP of your CA Admin.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Register the component admin identity with the CA by running the following command:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type client --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Create a new user identity `name` and `secret` for the admin. Make sure to use different values than for the peer or orderer identity you just registered. The command looks similar to the following example:

```
fabric-ca-client register --caname ca --id.name peeradmin --id.affiliation org1.department1 --id.type client --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Make a note of this information. You will use this `name` and `secret` to generate the MSP folder by using the `enroll` command.
{: important}

### Generating the admin Membership Service Provider (MSP) folder
{: #ibp-v2-apis-config-enroll-admin}

After your register the component admin, you can generate certificates by running the following command:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

For example:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname ca -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```
{:codeblock}

After this command completes successfully, it generates a new MSP folder in the directory that you specified by using the `-M` flag. This directory contains the certificates that you need to use to operate your components. You can verify that the enroll command worked by using a tree command. Navigate to the directory where you stored your certificates. A tree command should generate a result similar to the following structure:


```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

You will need return to this folder when you create your organization MSP definition and configuration files.
{: important}

### Registering the component identity with the TLS CA
{: #ibp-v2-apis-config-register-component-tls}

When you created your CA, a TLS CA was deployed along with your default CA. You also need to register the orderer or peer with your TLS CA. To do this, you will first need to enroll by using the admin of the TLS CA. Change `$FABRIC_CA_CLIENT_HOME` to a directory where you want to store your TLS CA admin certificates.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Run the command below to enroll as your admin against the TLS CA. The enroll ID and password of your TLS CA admin are the same as your default CA. As a result, the command below is the same as you used to enroll as your [CA admin](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin) only with the name of your TLS CA. Your TLS CA name is the **TLS CA Name** value from the CA **settings** panel in your console, or the value of the `"tlsca_name"` returned by the `Create a CA` API.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

A real call might look similar to the following example:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

After you have enrolled, you have the necessary certificates to register your component with the TLS CA. Run the following command to register the orderer or peer:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

This command is similar to the one that you used to register the component identity with the CA, except that you need to use the TLS CA name. If you are deploying an orderer instead of a peer, set `--id.type` to `orderer` instead of `peer`. You must provide this identity a different user name and password than the one that you used against your default CA. A real register might look similar to the command below:

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

You need to save the `"enrollid"` and `"enrollsecret"` from the command above for when you create your configuration file.
{: important}

## Creating an organization MSP definition
{: #ibp-v2-apis-msp}

You can use the APIs to create an organization MSP definition by calling [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp). This MSP contains certificates that define your organization in a blockchain consortium, as well as the admin certificates that you can use to operate your network. If you followed the step above, you have already generated the certificates needed to create an organization MSP. Use the following steps to complete the request body of the API call.

1. Input `msp` as the request `type`.

2. Select an MSP ID for your organization. The MSP ID is the formal name of your organization within the consortium. The MSP ID used to create the organization MSP needs to be the same that you use to deploy your peers.

3. Select a display name for your MSP.

4. You need to provide the signCert, in base64 format, of your organization administrator that you registered and enrolled by using the Fabric CA client.  

  Navigate to the MSP directory that was created when you [generated certificates using your organization administrator](#ibp-v2-apis-config-enroll-admin).
  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  ```
  In this MSP directory, open the signCert file of the new user and convert it to base64 format by using the following commands:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **Note:** It is important that the string generated by using the command above is formatted as a single line. It should look similar to:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```
  not like this:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
  ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
  Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
  VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
  WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

  For example:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  This command prints out a string that is similar to the following example:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  Provide this string to the `admins` field of the API request.

5. You also need to provide the root certificate of your CA. This certificate was created when you [generated certificates using your CA admin](#ibp-v2-apis-enroll-ca-admin).

  Navigate to the CA admin MSP directory.
  ```
  cd $HOME/fabric-ca-client/ca-admin/msp
  ```
  In this directory, open the cacerts folder and convert the certificate inside into base64 format by using the following commands:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-ca-admin>/msp/cacerts/<ca_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  This will print out the root cert as a base64 encoded sting.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGekNDQWIyZ0F3SUJBZ0lVQmZnZzcvVnIrL25OVEFNQlQ4UUtHL00wQU8wd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU1TURVd016RXpNamt3TUZvWERUTTBNRFF5T1RFek1qa3dNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVXMUtvN2lWeVE2VWkwdDVqbU5KaWVuSUwKR3pNM1BDWHlhL2VSQ0NWMmFQb0dTZ1lrVUg2UWN5RjAzbFlMZFU4Y0drNTQ0alViVC9KT1lYeVgzTWc4bHFORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZDK2lJR0NSb2Zvb3FsVkZoU3dOMmk2MXNJaVBNQW9HQ0NxR1NNNDlCQU1DQTBnQU1FVUNJUURTYW9RL1E0QzkKbFl1VGNhVXVHb3d6YmhUZHBuN2F3S2lHN1Nvd2lSQXVld0lnUWlyM3RNR3IvYWo2aU5lRXJFN2NyOVowQ0gvTwp3QnNQcWd4RVR3MjVqZUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  Provide this string to the `root_certs` field of the API request.

6. You should also provide the root certificate of your TLS CA. The TLS root certificate allows your peers to participate in gossip on a channel.

  Navigate to the MSP directory that was generated when you [enrolled your TLS CA admin](#ibp-v2-apis-config-register-component-tls).
  ```
  cd $HOME/fabric-ca-client/tlsca-admin/msp
  ```
  In this directory, open the cacerts folder and convert the certificate inside into base64 format by using the following commands:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-tlsca-admin>/msp/cacerts/<tls_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  This will print out the TLS CA root cert as a base64 encoded sting.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVWUVQWnprNXV2b3dobEtacG5JMXplODdIQUlnd0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWURWUVFERXdWMGJITmpZVEFlCkZ3MHhPVEExTURNeE16STVNREJhRncwek5EQTBNamt4TXpJNU1EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdzVPYjNKMGFDQkRZWEp2YkdsdVlURVVNQklHQTFVRUNoTUxTSGx3WlhKc1pXUm5aWEl4RHpBTgpCZ05WQkFzVEJrWmhZbkpwWXpFT01Bd0dBMVVFQXhNRmRHeHpZMkV3V1RBVEJnY3Foa2pPUFFJQkJnZ3Foa2pPClBRTUJCd05DQUFRdSs2UnZWd2w5T2dDVlAraEVxbjVxdExRVG9LWkw4a1lic0pOeU1JbERoc3hlNWx6cW1zQkoKbTk2eUR2TVV6OSsxL2pzb1M4M1JqMVAwc3M2TnJNb3FvMXd3V2pBT0JnTlZIUThCQWY4RUJBTUNBUVl3RWdZRApWUjBUQVFIL0JBZ3dCZ0VCL3dJQkFUQWRCZ05WSFE0RUZnUVVnUEc4anJEK1BxVjdoelc3WDlsbTFrMS91WjR3CkZRWURWUjBSQkE0d0RJY0VxVGJESW9jRUNwbnBkVEFLQmdncWhrak9QUVFEQWdOSUFEQkZBaUVBenk3cHJZaVMKQmlDVWdYeWRkY09WMm9mZmtqaEI0N091QXFjQWNqZS9SWkVDSUdKZFgzZ1ErTDRIN3duY1RoZkwrenU1ejV1UApGUWhXTmlNS3hQWEYrZnYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  Provide this string to the `tls_root_certs` field of the API request.

## Creating a configuration file
{: #ibp-v2-apis-config}

You need to complete a configuration file in order to create a peer or orderer by using the APIs. This file is provided to the API as the `config` object in the request body of the API call. You need to deploy a CA to your {{site.data.keyword.cloud_notm}} Platform service instance and follow the steps to register and enroll the required identities before completing the file.

The template for the configuration file can be found below:
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

Copy this entire file into a text editor where you can edit it and save it to your local file system as a JSON file. Use the steps below to complete this configuration file and use it to deploy an orderer or peer.

### Retrieve the CA connection information
{: #ibp-v2-apis-config-connx-info}

First, we need to provide the connection information of your CA on {{site.data.keyword.cloud_notm}} Platform. You can use the console UI or the APIs to get the necessary information about your CA.

**If you are using the {{site.data.keyword.blockchainfull_notm}} Platform console:**
Open the CA in your console and click **Settings**, then the **Export** button to export the CA information to a JSON file. You can use the values from this file to complete your configuration file.

**If your are using the APIs:**
You can call [`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) to get the connection information of your CA. If you created the CA using the `Create a Fabric CA` API, you can also find the necessary information in the response body.

- The `"cahost"` and `"caport"` values are visible in the `ca_url` field in the response body or CA JSON file that you exported.  For example, if your `ca_url` is https://9.30.94.174:30167, the value of the `"cahost"` would be `9.30.94.174` and the `"caport"` would be `30167`.
- The `"caname"` is the name of the CA that was specified when you deployed the CA. This is the value of the `ca_name` field in the response body or the exported JSON file.
- The `"cacert"` is the base64-encoded TLS certificate of your CA. This is the value of the `pem` field in the response body or the exported JSON file.

- The fields in the `"tls"` section below require the same information as you passed to the component sections above, except you need to use the value of the CA TLS instance name that is specified during CA deployment. Replace `caname` with the value of `tlsca_name` in the response body or the exported JSON file. Use the same TLS cert for the `"cacert"` value.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

### Provide your component enroll ID and secret

1. Paste the `name` and `secret` you used to [register your component with your default CA](#ibp-v2-apis-config-register-component) as the `"enrollid"` and `"enrollsecret"` in the configuration file, under the `"component"` section:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```
2. Paste the `name` and `secret` you used to [register your component with your tls CA](#ibp-v2-apis-config-register-component-tls) as the `"enrollid"` and `"enrollsecret"` in the configuration file, under the `"tls"` section:

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

### Provide the signCert of your organization administrator

Navigate to the MSP directory that was created when your [generated certificates using your organization administrator](#ibp-v2-apis-config-enroll-admin).
```
cd $HOME/fabric-ca-client/peer-admin/msp
```
In this MSP directory, open the signCert file of the new user and convert it to base64 format by using the following commands:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**Note:** It is important that the string generated by using the command above is formatted as a single line. It should look similar to:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```
not like this:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
```

For example:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

This command prints out a string that is similar to the following example:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

Copy this string to the `"admincerts"` field under the component section in the configuration file.

### CSR (Certificate Signing Request) hosts

You have the option of providing a custom domain to your component using the `"csr"` section of the components file.

```
"csr": {
  "hosts": [""]
  }
```
{:codeblock}

This section is provided for advanced users to specify a custom host name that can be used to address the peer endpoint. Most users can leave this section blank.

### Completing the configuration file

After you completed all the steps above, your updated configuration file might look similar to the following example:


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "ca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer1",
            "enrollsecret": "peer1pw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                ]
            }
        }
    }
}
```
{:codeblock}

You can leave the other fields blank. After completing this file, you can pass this file as the `config` field to the request body of the `Create an orderer` or `Create a peer` API.

### Importing an admin identity into the {{site.data.keyword.blockchainfull_notm}} Platform console
{: #ibp-v2-apis-admin-console}

If you want to use the {{site.data.keyword.blockchainfull_notm}} Platform console to operate your blockchain components, you must import your administrator identity into your console wallet. Open the wallet panel in your console. Click the **Add identity** button on the overview screen. Clicking this button will open up a side panel that allows you to add an identity's  signing certificate and private key directly to the console.
- **Name:** Enter a display name for the identity that is used for your reference only.
- **Certificate:** Upload your admin's signing certificate. If you followed the instructions above, you can find this key in the `$HOME/fabric-ca-client/peer-admin/msp/signcerts/` folder.
- **Private Key:** Upload your admins private key. If you followed the instructions above, you can find this key in the `$HOME/fabric-ca-client/peer-admin/msp/keystore/` folder.

After you import your admin identity, you can associate this identity with the components that you have created. You can then use the console to operate your network.

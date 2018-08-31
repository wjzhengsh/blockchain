---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Managing certificates on {{site.data.keyword.blockchainfull_notm}} Platform
{: #certificates}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform is based on Hyperledger Fabric and builds permissioned blockchain networks. Participants are known as members of the network, and their activities and access to network resources are verified continuously. A participant's identity is provided in the form of a trusted x509 digital certificate. The verification is provided by an underlying public key infrastructure and sign/verify operations that secure transactions and management within the network.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform manages most of the certificate operations without users needing to handle their certificates. However, there are times when you will have to manage the certificates that allow you to communicate with the network, such as developing applications or expanding your network with a remote peer. The following is a short guide to your Certificate Authority, and the underlying certificate infrastructure. This tutorial can help you understand the certificates you will be working with and the tasks you need to perform.

## Certificate authorities
{: #network-ca}

Certificate authorities (CAs) provide identity on the network. A CA can be considered as a publicly trusted notary that acts as an anchor of trust among multiple parties. All entities in the network are given a certificate signed by a root CA that encapsulates their digital identity. This certificate is the root of trust for all the sign and verify operations that are performed on the network. For more details about how certificate authorities are used to establish identity, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html).

Each member in the network possesses their own CA, which is issued a certificate from the root CA of the network. This tie to the root CA establishes the anchor of trust for your organization. Your org CA signs requests for all the entities that your organization owns, such as your admin, peers, or applications. If you want to add a remote peer or new application to your network, you need to register the new identity with your Certificate Authority (registration). Then, the CA can provide the new entity with the certificates it will need to interact with the network (enrollment).

### Registration using the Network Monitor
{: #ca-panel}

You can use the {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor to view the identities that are registered with your CA and to add new ones. Navigate to the "Certificate Authority" panel of the Network Monitor. This panel displays all the identities that have been registered with your CA, including your organization admin, peers, and client applications. To register a new identity in your organization, click the **Add User** button at the top of the panel. A pop-up window opens and displays the following fields that are necessary to register a new identity.
  - **ID:** This will the name of your new identity, sometimes referred to as your `enroll ID`. **Save this Value** for when you configure a remote peer or enroll a new application.
  - **Secret:** This will be the password to your identity, sometimes referred to as your `enroll Secret`  **Save this Value.** for when you configure a remote peer or enroll a new application.  
  - **Type:** Select the type of identity the you want to register, either peer or client application.
  - **Affiliation:** This will be the affiliation within your organization, such as `org1` for example, that the identity will belong to.
  - **Maximum Enrollments:** You can use this field to limit the number of times your can enroll or generate certificates using this identity. If you leave the field blank, the value defaults to an unlimited number of enrollments.

You can use this panel to register a new peer identity if you are deploying a [remote peer](howto/remote_peer.html). Alternatively, you can you can register a client if you are developing an application that can submit transactions to your network. Visit the [developing applications tutorial](v10_application.html) to learn more about using the with Fabric SDKs with the platform.

### Generating client side certificates (enrollment)
{: #enrollment}
Before you connect a third party client to {{site.data.keyword.blockchainfull_notm}} Platform, you need will need to be authenticated. The process of generating the necessary certs, your private key and your public certificate (also known as your enrollment cert or signCert) is called enrollment. These certificates will be needed anytime your client communicates with the network. Any client that submits calls to the network will need to sign payloads using a private key and attach a properly signed x509 certificate.

Visit the [developing applications tutorial](v10_application.html) to learn how to [enroll using Fabric Node SDK](v10_application.html). Enrolling with the SDK generates a signCert, a public key that was used to create the signCert, and your private key.

You can also generate certificates from the [command line](#enroll-register-caclient) using the Fabric CA Client. The Fabric CA Client returns more complete set of certificates inside a Membership Service Provider (MSP) folder. This folder contains the root certificate signed by the CA, intermediate certificates tied to your affiliation, a private key and your signCert. To learn more about MSP's and what this folder contains, see the [Membership Service Providers](#msp) section below.

### Uploading signing certificates to {{site.data.keyword.blockchainfull_notm}} Platform
{: #upload-certs}

An application only requires a valid signCert to submit transactions to the network. However, if a client wants to operate the network, by installing chaincode on peers or joining peers to channels, for example, the client needs to be recognized as an administrator. Each component recognizes a set of signCerts certs that are owned by an admin. If you need to operate your network from a client, you will need to upload your signCert and have it added to the list of admin certs. You can do this on the platform by uploading your signCert in the **Certificates** tab of the ["Overview" panel](v10_dashboard.html#members) of your Network Monitor. Sync this certificate to your peers by hitting the restart button that comes up after uploading. Afterwards, your client can be recognized as an admin of the network.

<!----
When you add an organization to a channel, this will upload the organization signCert to the channel MSP. Synching the Sign Certs does something.
--->

## Using TLS Certificates
{: #tls}

[Transport Layer Security ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) is embedded in the trust model of Hyperledger Fabric. All components on {{site.data.keyword.blockchainfull_notm}} Platform use TLS to authenticate and communicate with each other. Therefore, you need to attach a TLS certificate issued by the platform to your calls in order to validate and encrypt your communication. The other certificates discussed in this tutorial protect your ability to transact with and manage the network. TLS certs are used to secure your calls to the network.

TLS certificates are issued publicly by the platform and are the same for all your network components. You can download the TLS certificates with the following links, depending on your membership plan and cloud location. You can also find the TLS certificates in your [credentials profile](v10_dashboard.html#enterprise-connection-profile "credentials profile"). This certificate can reside anywhere, as long as you can reference it from your application or command line.

- Root TLS Cert for Starter Plan  
  - US: [us01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - UK: [uk01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sydney: [aus01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
- [Root TLS Cert for Enterprise Plan ![External link icon](images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

All {{site.data.keyword.blockchainfull_notm}} Platform networks use server side TLS, in which the network needs to authenticate your clients. Enterprise Plan networks can also enable mutual TLS, in which the client and the server authenticate each other, to further secure your applications. Client-side TLS certificates (for Mutual TLS) are issued by the client CA and are unique to your network. If you use an Enterprise Plan network, it is recommended that you enable mutual TLS. For more information about mutual TLS, see these [instructions](v10_dashboard.html#mutual-tls "mutual TLS instructions").

## Membership Service Providers (MSPs)
{: #msp}

Components of {{site.data.keyword.blockchainfull_notm}} Platform consume identities via Membership Service Providers (MSPs). MSPs associate the certificates that the CAs issue with network and channel roles. For more information about MSPs, see [Membership concept topic in Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Membership concept topic in Hyperledger Fabric documentation").

The MSP folders within Fabric have a defined structure. When you enroll using the Fabric CA Client, the client stores the certificates in an MSP folder on your local file system with the following subfolders:

- **cacerts:** This folder contains the root certificate of the root CA of your network.
- **intermediatecerts:** These are certificates of the intermediate CAs of your network. These intermediate CAs are linked to the root CA and form a chain of trust. Each Enterprise Plan organization has two intermediate CA's for failover and high availability.
- **signcerts:** This folder contains your public signing certificate, which is also called your signCert or enrollment certificate. This certificate is attached to your calls to the network (a chaincode invoke, for example) when you reference your MSP directory from the command line or build a user context object with the SDKs. You can upload this certificate to the platform if you want to operate a network from the SDK or command line.
- **keystore:** This folder contains your private key. This key is used to sign your calls to the network when you reference your MSP directory from the command line or build a user context object with the SDKs. Keep this key safe to protect your network and your data.

You can also build an MSP folder that can be referenced by the Fabric CA Client using the Network Monitor and the Swagger APIs.

- **cacerts** and **intermediatecerts**: You can fetch these certificates with the [Swagger APIs](howto/swagger_apis.html) by issuing a Get request to the MSP API.
- **signcerts** and **keystore**: You can generate these certificates by clicking the **Generate Certificates** button on the "Certificate Authority" panel. A pop-up window opens with two certificates listed. Copy and store the **Certificate** in signcerts and the **Private key** in keystore. Keep these certificates in a safe place because they are not stored on the platform.

Many Fabric components contain additional information inside their MSP folder. For example, if you operate a remote peer, you might see the following folders:

- **admincerts:** This folder contains the list of administrators for this organization or component. You will need to upload your sign cert to this folder if you are operating a remote peer from the command line or the SDKs.
- **tls:** This is a folder where you store TLS certs that are used for communicating with other network components.

For more information about the structure of MSPs, see [Membership ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Membership") and [Membership Service Providers ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") in Hyperledger Fabric documentation.

## Enrollment and Registration using the Fabric CA Client.
{: #enroll-register-caclient}

You can also use the Fabric CA Client to generate certificates and register a new identity with the Certificate Authority. The instructions below generate certificates using your admin identity, then use those certificates to register a new client. Enrolling using the Fabric CA Client will generate the certificates that are described in the [Membership Services Providers section](#msp).

### Enrolling by using the Fabric CA Client
{: #enroll-app-caclient}

1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them, extract them, and move them to a folder on such as `$HOME/fabric-ca-platform/`. Change to the directory where you moved your client binaries so that you can reference it directly in your commands.
    ```
    cd $HOME/fabric-ca-platform/
    ```

2.  Set the path where the client can create your keys. Ensure that you remove the configuration material that past attempt create. If you don't run the `enroll` command before, the `msp` folder and the `.yaml` file do not exist.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```

3. Download the TLS certs from {{site.data.keyword.cloud_notm}} depending on the service plan, location, and cluster that you use.
  - Root TLS Cert for Starter Plan  
    - US: [us01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Root TLS Cert for Enterprise Plan ![External link icon](images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Save the contents to a folder, for example ``$HOME/tls``. This step allows the data flowing to be encrypted on the wire.

4. Open the **Connection Profile** JSON file from your "Overview" panel in the Network Monitor, and find the following variables:
  * URL for CA: ``url`` under `certificateAuthorities`
  * Admin user ID: ``enrollId``
  * Admin password: ``enrollSecret``
  * CA Name: ``caName``

5. You can use the Fabric CA client to send an `enroll` call to the Certificate Authority by passing in the TLS certs path and the four strings above with the following command:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  You need to run this command in the directory where you move the Fabric CA Client to. A real call might look similar to the following example command:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

6. Find your admin certificate in `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. You can then upload the admin certificate to your blockchain network from the Network Monitor. For more information about adding certificates, see [the "Certificates" tab of "Member" panel](v10_dashboard.html#members) in the Network Monitor.

  You can also find CA root certificate and admin private key in the following directories:
  * CA root certificate: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * The admin private key: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

To see an example of where you would enroll by using the Fabric CA Client and use the generated certificates to operate a network component in the instructions to [operate a remote peer](howto/remote_peer_operate_icp.html#remote-peer-cli-operate).

### Registering by using the Fabric CA Client
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
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

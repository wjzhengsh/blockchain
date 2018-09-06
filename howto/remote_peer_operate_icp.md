---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Operating remote peers in {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-operate}


***[Is this page helpful? Tell us.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


After you set up {{site.data.keyword.blockchainfull}} Platform remote peers on {{site.data.keyword.cloud_notm}} Private (ICP), you need to complete several operational steps before your peer can issue transactions to query and invoke the ledger of the blockchain network. The steps include adding your organization to a channel, joining your remote peer to the channel, installing chaincode on your remote peer, instantiating chaincode on the channel, and connecting applications to your remote peer.
{:shortdesc}

You need to complete a few prerequisite steps from your ICP Cluster to operate your remote peer.

**Prerequisites:**
- [Retrieving your remote peer endpoint information](#remote-peer-retrieve-endpoint-info)
- [Logging in to your peer container by using kubectl](#remote-peer-kubectl-configure)
- [Restarting your remote peer](#remote-peer-restart)

You can then use one of the following methods to operate your remote peer.

**Operations Paths:**
- [Using the Fabric SDKs](#remote-peer-operate-with-sdk)
- [Using the command line](#remote-peer-cli-operate)

The Fabric SDKs are the recommended path, although the instructions assume familiarity with the operation of the SDK.

It is recommended that you deploy at least two instances of the remote peer helm chart for [high availability](remote_peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).

**Note**: An {{site.data.keyword.blockchainfull_notm}} Platform remote peer does not have access to the full functionality or support of peers that are hosted on the {{site.data.keyword.blockchainfull_notm}} Platform. As a result, you cannot use the Network Monitor to operate a remote peer. Before you start running remote peers, ensure that you review the [considerations](remote_peer.html#remote-peer-limitations).

## Prerequisites

Whether you plan on using the SDKs or the command line, you need to complete the steps in this section in the course of operating your peer. You can complete the first two steps, retreiving your peer endpoint and configuring kubectl, before you get started.

### Retrieving your remote peer endpoint information
{: #remote-peer-retrieve-endpoint-info}

Log in to your ICP console. Click the **Menu** icon in the upper left corner. Click **Workload** > **Deployments**. Then find and click on the name of your remote peer instance to open the deployment overview screen. Scroll down to the **Expose details** section and find the `Ingress IP` and `Ports` of your remote peer.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

Make a note of the Ingress IP address and Node ports to connect to your remote peer. You need to use this information when you install and instantiate chaincode on the remote peer. In the following example, your peer address is either `9.46.126.89:30162`, `9.46.126.89:30260`, or `9.46.126.89:32051`.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### Configuring kubectl CLI
{: #remote-peer-kubectl-configure}

You need to use the **kubectl** command line tool to conduct some necessary operations inside the peer container that runs in ICP. Log in to your cluster by using the {{site.data.keyword.cloud_notm}} Private CLI tool:

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

Then log in to the dashboard of your ICP Cluster. Click your user avatar in the upper right corner of the ICP UI and then click **Configure client**. Copy, paste, and run the commands in your terminal window on your local machine. You can verify that the configuration worked successfully by running the following command:

```
kubectl -n <namespace> get pods
```
{:codeblock}

where `-n <namespace>` should be specified when the `default` namespace is not being used. Replace `<namespace>` with the value of your
namespace in your ICP cluster.


The command should result in a response, which is similar to the following example. `remotepeer-bd65c4769-95rmm` is the name of the peer container.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

If you do not recognize your remote peers pod name from the response, you can find the name of your pod in the **Pods** pane of your deployment panel.

Exec into the remote peer container by running the following command, replacing the value of `<PEER_CONTAINER_NAME>` with the name of the Pod from above.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
where `-n <namespace>` should be specified when the `default` namespace is not being used. Replace `<namespace>` with the value of your
namespace in your ICP cluster.

For example:

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

You are now ready to use the peer container CLI. You need to use the CLI to download the peers TLS certificate and upload your sign cert to the peer if you are using either the SDKs or the command line.

## Restarting a remote peer that runs in ICP
{: #remote-peer-restart}

Whenever a certificate is added to the remote peer, the peer node needs to be restarted in order for it to recognize the new certificate.

You can use the **kubectl** commands to restart the remote peer that runs in ICP. The command requires the name of the remote peer **pod** and its **container** as command parameters. For more information about using the kubectl commands, see [configuring kubectl CLI](#remote-peer-kubectl-configure).

1. In the ICP console, find the **pod** name and **container** name for your remote peer deployment.

  1. Click the menu icon in the upper left corner of the console, then click  **Workloads > Deployments**.
  2. Locate and click your remote peer release in the table to open it.
  3. Scroll down to view the associated **pod** name. Make a note of the **pod** name.
  4. Click the pod to open it.
  5. Click the **Containers** tab. Make a note of your remote peer **container** name.

2. From your command line, run the following command to restart your peer, replacing `<REMOTE_PEER_POD_NAME>` and `<REMOTE_PEER_CONTAINER_NAME>` with the values from above.

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. You can verify that the remote peer was restarted by running the `kubectl get pods` command and examining the output for the **RESTART** count for your pod.

Additionally, you can use the [HEAD request](monitor_network.html#monitor-nodes) to check the availability of your remote peer.


## Using Fabric SDKs to operate the remote peer
{: #remote-peer-operate-with-sdk}

The Hyperledger Fabric SDKs provide a powerful set of APIs that enable applications to interact with and operate blockchain networks. You can find the latest list of supported languages and the complete list of available APIs within the Fabric SDKs in the [Hyperledger Fabric SDK community documentation ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community documentation"). You can use the Fabric SDKs to join your remote peer to a channel on the {{site.data.keyword.blockchainfull_notm}} Platform, install a chaincode on your peer, and instantiate the chaincode on a channel.

The following instructions use the [Fabric Node SDK ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/ "Fabric Node SDK") to operate the remote peer and assume prior familiarity with the SDK. You can use the [developing applications tutorial](../v10_application.html) to learn how to use the Node SDK before you get started, and as a guide to developing applications with your remote peer when you are ready to invoke and query chaincode.

### Installing the Node SDK

You can use NPM to install the [Node SDK ![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/ "Node SDK"):
```
npm install fabric-client@1.1
```
{:codeblock}

It is recommended that you use version 1.1 of the Node SDK.

### Preparing the SDK to work with the remote peer
{: #remote-peer-node-sdk}

Before you use the SDK to operate the remote peer, you need to generate the necessary certificates (enrollment) that will allow your application to communicate with your network on {{site.data.keyword.blockchainfull_notm}} Platform and your remote peer. Follow the steps to [enroll with the SDK](../v10_application.html#enroll-app-sdk) using your **admin** identity. The [Developing Applications](../v10_application.html) tutorial also enrolls as the **admin**, so you don't need to modify the sample code.

### Uploading a signCert to the {{site.data.keyword.blockchainfull_notm}} Platform
{: #remote-peer-upload-sdk}

You need to upload your SDK signing cert to the network on {{site.data.keyword.blockchainfull_notm}} Platform so that your application has permission to operate the network.

- You can find your signing cert in the directory your SDK generated your crypto material, in the file named admin. Copy the certificate inside the quotation marks after the `certificate` field, starting with  `-----BEGIN CERTIFICATE-----` and ending with `-----END CERTIFICATE-----`. You can use the CLI to convert the certificate into PEM format by issuing the command `echo -e "<CERT>" > admin.pem`. You can then paste the contents of the certificate into your blockchain network by using the Network Monitor. Log in to the network on {{site.data.keyword.blockchainfull_notm}} Platform. On the "Members" screen of the Network Monitor, click **Certificates** > **Add Certificate**. Specify any name for your certificate and paste the contents in to the **Certificate** field. Click **Restart** when your are asked if you want to restart your peers. This action must be performed before your organization joins the channel.

### Uploading a signCert to the remote peer
{: #remote-peer-upload-signcert}

You also need to upload the SDK's signing cert to the remote peer and restart it. You need to install the same sign cert you [uploaded to the {{site.data.keyword.blockchainfull_notm}} Platform](#remote-peer-upload-sdk) inside the remote peer container.

Run the following commands from [inside your peer container](#remote-peer-kubectl-configure) to upload your cert.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

Replace the text `<CERT.PEM>` with your signCert, starting with `-----BEGIN CERTIFICATE-----` and ending with `-----END CERTIFICATE-----`. **Note** that a cert.pem file already exists in this directory. Name the new cert `cert2.pem` or similar to avoid overwriting the cert already inside the container.

Because you added a new certificate, you need to restart the container for the peer pick up the cert. Follow these [instructions](#remote-peer-restart) to restart the peer.

### Passing your remote peer's TLS cert to the SDK
{: #icp-remote-peer-download-tlscert}

You need to copy the contents of the TLS `cacert.pem` from the peer container to your application to authenticate communication from your SDK. In the [peer CLI container](#remote-peer-kubectl-configure), run the following command:
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

Copy the entire text of the `cacert.pem` file to your clipboard, which starts with the first `-----BEGIN CERTIFICATE-----` and ends with the very last `-----END CERTIFICATE-----`. Save this certificate to your local file system in PEM format. You can then import the TLS cert into your application by using a simple read file command.
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### Providing the remote peer endpoint information to the SDK
{: #remote-peer-SDK-endpoints}

Find the [endpoint information of your remote peer](#remote-peer-retrieve-endpoint-info) and provide it to the SDK by declaring a new peer variable or by updating your Connection Profile. The following example defines the peer as an endpoint on your fabric network and passes it the TLS cert that you imported.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.

**Note:** Because the peer is remote, other organizations on the {{site.data.keyword.blockchainfull_notm}} Platform network will not be able to find your peer's endpoint information in their Connection Profile. If other organizations need to send your remote peer a transaction to be endorsed, you need to provide the Public IP and TLS certs in an out of band operation.

### Using the SDK to join to a channel
{: #remote-peer-join-channel-sdk}

As a member of the blockchain network, your organization needs to be added to a channel in the network before you can join your remote peer to the channel.

  - You can start a new channel for the remote peer. As the channel initiator, you can automatically include your organization during [channel creation](create_channel.html#creating-a-channel). Note that you need to have at least one peer on {{site.data.keyword.blockchainfull_notm}} Platform before you can create a channel in the Network Monitor.  

  - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](create_channel.html#updating-a-channel). A member of the channel with peers on {{site.data.keyword.blockchainfull_notm}} Platform can use the Network Monitor to add your organization to the channel even if you do not host any peers on the platform.

    After your organization is added to a channel, you need to add your peer's signing certificate to the channel so that other members can verify your digital signature during transactions. The remote peer uploads its signing cert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joins and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

When your organization is part of the channel, follow the instructions of [joining a channel](../v10_application.html#join-channel-sdk). You need to provide the URL of the ordering service and the channel name.

### Using the SDK to install chaincode on the peer
{: #remote-peer-install-cc-sdk}

Use the following instructions to use the SDK to [install a chaincode](../v10_application.html#install-cc-sdk) on your remote peer.

### Using the SDK to instantiate chaincode on the channel
{: #remote-peer-instantiate-cc-sdk}

Only one member of the channel needs to instantiate or update chaincode. Therefore, any network member of the channel with peers on {{site.data.keyword.blockchainfull_notm}} Platform can use the Network Monitor to instantiate chaincode and specify endorsement policies. However, if you want to use the remote peer to instantiate chaincode on a channel, you can use the SDK and follow the instructions to [instantiate a chaincode](../v10_application.html#instantiate-cc-sdk).


## Using the CLI to operate the remote peer
{: #remote-peer-cli-operate}

You can also operate your remote peer from the command line by using the Fabric CA client and Fabric tools container. In these instructions, we first generate required certificates by using the Fabric CA Client and then use the Fabric tools container to operate the peer by joining it to a channel, installing a chaincode, and then instantiating the chaincode on a channel.

### Enrollment by using Fabric CA client
{: #peer-client-enroll}

The first step is to generate the required certificates (enrollment) by using the Fabric CA client. You need to install Fabric CA client first. Download the [fabric-ca binaries v1.1.0 for your platform](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine, extract them, and move them to a folder such as `$HOME/fabric-ca-remote/`.

1.  Prepare your environment to use the Fabric CA client. Change to the directory where you moved your client binaries so that you can reference it directly in your commands.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Set the path where the client can create your keys. If this is the first time to run the `enroll` command, the `msp` folder and the `.yaml` file do not exist. If you have run the `enroll` command before, it is important that you delete any configuration material from previous enroll attempts by using the `rm` commands below:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  You need retrieve the endpoint information of your Certificate Authority on the {{site.data.keyword.blockchainfull_notm}} Platform. On the "Overview" screen of your Network Monitor, click the **Connection Profile** button and open the JSON file that contains the network credentials. Scroll down to the `certificateAuthorities` section and make a note of the information below.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  You also need to retrieve a TLS certificate from the {{site.data.keyword.blockchainfull_notm}} Platform to communicate with the CA. The network credentials above include the necessary cert. In the `certificateAuthorities` section of the Connection Profile file, copy the certificate that follows `"pem:"`, which begins with `-----BEGIN CERTIFICATE-----` and ends with `-----END CERTIFICATE----- `. **Do not include the quotation marks**.

    Run the following command From a terminal window, replacing `<certificate>` with your copied certificate.
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 Store this cert in a place where you can reference it in future commands.

5.  You are now ready to generate the required certificates. Running the following command:
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    For example:
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    When the command completes successfully, you can see a response that is similar to the following example:
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    The command will generate the required certs and store them then in a folder named `msp` in `$FABRIC_CA_CLIENT_HOME`. This folder and the certificates inside will be important in the following steps.

### Managing the certificates on your local system
{: #manage-certs}

Before we can operate the remote peer, we need to do some management of the certificates on our local machine, and upload some of the certs the Fabric CA client generated to {{site.data.keyword.blockchainfull_notm}} Platform and your peer. We also need to download TLS certificates from the Platform and the peer. If you want to learn more about the certificates you will be working with and the tasks you will be performing, see [Managing certificates on {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

On your local machine, open a command terminal and navigate to the directory where you moved the Fabric-CA-Client binaries and stored the MSP folder.
1. Copy the `cert.pem` file from the `signcerts` folder to a new `admincerts` folder.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. You will also need to copy your remote peer's TLS certificate from the peer container on ICP to your local machine.

  - [log in to your peer container](#remote-peer-kubectl-configure) and run the following command:
    ```
    cat /mnt/certs/tls/cacert.pem
    ```
    {:codeblock}

    Copy the entire text of the `cacert.pem` file to your clipboard, starting with the first `-----BEGIN CERTIFICATE-----` and ending with the very last `-----END CERTIFICATE-----`.

  - Switch back to your local machine and run the following commands. Replace the text `<CACERT.PEM>` with the text from the clipboard.
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
    {:codeblock}

3. To give your CLI the authorization to install chaincode on the peer, you need to upload the signCert generated by the Fabric CA client to the peers admin folder and restart the peer. Therefore, copy the `admincert/cert.pem` certificate from your local machine into the `/mnt/crypto/peer/peer/msp/admincerts/` directory in the peer container as `cert2.pem`.

    - On your local machine, run the following commands:

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copy the text of the `cert.pem` file to your clipboard starting with `-----BEGIN CERTIFICATE-----` and ending with the `-----END CERTIFICATE-----`.

    - Switch back to the peer container and run the following commands to add the cert.pem to the peer certs. Replace the text `<CERT.PEM>` with text from the clipboard.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Note:** A cert.pem file already exists in this directory. Do not overwrite it.

    - Because you added a new certificate, you need to restart the container for the peer pick up the cert. Follow these [instructions](#remote-peer-restart) to restart the peer.

4. You need to upload the same `admincert/cert.pem` certificate from your local machine to the {{site.data.keyword.blockchainfull_notm}} Platform in order for a remote CLI or application to perform channel operations such as fetching the channel genesis block and instantiating chaincode.
    1. On your local machine, cat the file `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` and then copy it to the clipboard.
    2. Enter the Network Monitor of your network on {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Click **Members** in the left navigator and click the **Certificates** tab.
    4. Click the **Add Certificate** button.
    5. In the **Add certificate** window, give your certificate a name, such as `fabrictools.pem`, paste in the certificate you just copied to the clipboard and click **Submit**.
    6. You will be asked whether you want to restart the peers. Click **Restart**.
    7. Click **Channels** in the left navigator and locate the channel that the remote peer will join.
    8. Click the three dots under the **Actions** header and click **Sync Certificate**. In the **Sync certificate** window, click **Submit**.

    **Note**: It is important to sync the channel certificate before the peer joins the channel below.

### Setting up the Fabric tools container

After we have moved all of our certificates to the necessary location, we are ready to install and use the Fabric tools container from docker. Once again, we need to run these commands from the directory where you stored your MSP folder.

1.  Download the Fabric tools docker image with the following command:
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  Run the following commands to start the Fabric tools container.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    This command mounts the MSP directory inside your tools container.

3.  The remote peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. Login to your ICP console and find your remote peers [endpoint information](#remote-peer-retrieve-endpoint-info). Once you have the your peers `INGRESS_IP`, run the following command with the address of your remote peer.
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Using the Fabric tools container to join the remote peer to the channel
{: #icp-cli-join-peer-to-channel}

Before you can run the CLI commands to join the remote peer to a channel, your organization needs to be added to a channel in the network.

  - You can start a new channel for the remote peer. As the channel initiator, you can automatically include your organization during [channel creation](create_channel.html#creating-a-channel). Note that you need to have at least one peer on {{site.data.keyword.blockchainfull_notm}} Platform before you can create a channel in the Network Monitor.  

  - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](create_channel.html#updating-a-channel). A member of the channel with peers on {{site.data.keyword.blockchainfull_notm}} Platform can use the Network Monitor to add your organization to the channel even if you do not host any peers on the platform.

    After your organization is added to a channel, you need to add your peer's signing certificate to the channel so that other members can verify your digital signature during transactions. The remote peer uploads its signing cert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joins and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

1. Retrieve configuration information from your `creds.json` file available in the **Overview** panel of the Network Monitor. Click **Connection Profile** and then **Download**.

  - Find the orderer URL by searching for **orderers**, which is located under `orderers > url`. Make a note of the URL that begins with the network name. The URL is similar to the following example:
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Find the name of your organization by searching for **organizations**. This should be the same organization as you use to register your remote peer. You can find your organization's name together with its associated `mspid`. Make a note of the value of the `mspid`.

2. [Find the remote peers endpoint information](#remote-peer-retrieve-endpoint-info), this time making note of the `Ports` listed beside the `Ingress IP`.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  Make a note of the ports in the 30K range. These are your addressable ports from the Fabric tools container, which we will use to set the `PEERADDR` environment variable.

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  Therefore, the `PEERADDR` that we use is constructed with the value that we specified in the `/etc/hosts` file together with the port bound to `7051`, that is `30162` in our example. So the resulting value of `PEERADDR`
  would be `remotepeer.blockchain.com:30162`.

3. Run the following commands to set the environment variables in the Fabric tools container.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Replace the fields with your own information.
    - Replace `<ORDERER_URL>` with the hostname and port of the orderer from the `creds.json` file.
    - Replace `<CHANNEL_NAME>` with the name of the channel that the peer joins.
    - Replace `<CC_NAME>` with any name to refer to your chaincode.
    - Replace `<ORGANIZATION_MSP_ID>` with the name of the organization from the `creds.json` file.
    - Replace `<PEER_ADDR>` with the value that you constructs in the previous step.

  For example:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. Fetch the genesis block of the channel to build the channel ledger on your peer. First `cd` to your root directory, and then run the command to fetch the genesis block.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
  ```
  {:codeblock}

  Run the following command to verify the genesis block is added to your peer container successfully:

  ```
  ls *.block
  ```
  {:codeblock}

  You know the genesis block is added successfully when you see something that is similar to the following example:
  ```
  defaultchannel_0.block
  ```

5. Join the peer to the channel by using the following command:

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  When this works successfully, you should see information that is similar to the following example:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Using the Fabric tools container to install chaincode on the remote peer
{: #icp-toolcontainer-install-cc}
We are now ready to install and instantiate chaincode on the remote peer. For these instructions, we'll install the `fabcar` chaincode from the `fabric-samples` repository. Download the `fabric-samples` chaincode from `github.com` by using the following commands:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

Run the following Peer CLI command to install the `fabcar` chaincode onto the remote peer.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  When this command completes successfully, you should see something similar to:

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Using the Fabric tools container to instantiate chaincode on a channel
{: #icp-toolcontainer-instantiate-cc}

Because only one peer has to instantiate chaincode on a channel, you do not have use your remote peer to instantiate chaincode. Peers that are hosted on {{site.data.keyword.blockchainfull_notm}} Platform can do that. However, if you would like the remote peer to instantiate the chaincode onto the channel, you can run the following command in the Fabric Tools container::

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

When this command completes successfully, you should see something similar to:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Viewing remote peer logs in {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-log-icp}

You can access the remote peer logs from ICP console and check the logs in the Kibana interface.

1. In the ICP console, click the **Menu** icon in the upper left corner.
2. From the menu list, click **Workloads** > **Helm Releases**.
3. In the Helm Releases table, click the name of your helm release.
4. Scroll down to the **Pod** section and click the **View Logs** link at the end of the table row. The remote peer logs are opened in the Kibana interface.

## Updating chaincode

Over time it is likely you need to modify chaincode that is running on the remote peer. Because chaincode is installed on the peers and instantiated on the channel you need to update the chaincode on all of the peers on the channel.

Complete the following steps to update your chaincode:

1. To update the chaincode on each remote peer, simply rerun the process you used to install the chaincode on the peers, by using either a client application or a CLI command. Be sure to specify the same chaincode name as was originally used. However, this time increment the chaincode `Version`. All peers need to use the same chaincede name and version.

2. After installing the new chaincode on all the peers in the channel, use the Network Monitor or the
[peer chaincode upgrade ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) command to update the channel to use the new chaincode.

See step two of these [instructions](install_instantiate_chaincode.html#updating-a-chaincode) for more information about using the "Install Code" panel of Network Monitor to update the chaincode on the channel.

## Troubleshooting
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problem:** Error when logging in to peer container
{: #icp-bash-error}

This problem occurs when you use the kubectl tool to exec into your peer by running the following command:
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
fails and produces an error message similar to the following example:
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Solution:**  

This error occurs when on big endian systems, such as IBM System Z, when two docker bash sessions are opened in the same docker container. The second bash session might be unable to connect to the running container. To resolve this issue, [restart the peer container](#remote-peer-restart) and retry the `kubectl exec` command after the peer restarts.

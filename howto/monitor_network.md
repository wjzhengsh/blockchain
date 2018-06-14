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

# Monitoring a blockchain network

This tutorial shows how to view and monitor the status information of your {{site.data.keyword.blockchain}} network on {{site.data.keyword.cloud_short}}.
{:shortdesc}


## Monitoring peers, orders, and CAs
{: #monitor-nodes}

You can issue an HTTP **HEAD** request against one of your network nodes to check the node status. A network node can be a peer, an orderer, or a CA in your blockchain network. A **HEAD** request is similar to a GET request and sends just the headers without bodies. You can get a 200 response if the node works normally.

1. In the "Overview" screen of the Network Monitor, click **Connection Profile**. Then, you can click **Raw JSON** to view the connection profile in your web browser or click **Download** to save the connection profile locally.
2. In the connection profile, find the url information of the network node that you want to check. For example, the url of the `fabric-orderer-20190b` orderer is `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.  
    ![Orderer url example](../images/orderer_url.png "Orderer url example")
3. Replace **grpcs** with **https** in the url. In the example above, the url becomes `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Issue the **HEAD** request against the url with a tool such as curl or Chrome Postman app.
    - If you get a 200 status response, your network node works normally.
    - If the **HEAD** request fails with a connection error, your network node might not be running, the node url is wrong, or a firewall blocks your access to the node.  You must resolve this error; otherwise, your applications cannot connect the node.

The following example shows a **HEAD** request with a 200 response in curl. Note that you can ignore the grpc error because the HTTP **HEAD** request checks whether the node is accessible. If yes, the grpc request to the node also works in your application.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

The followng example shows a **HEAD** request with a connection error in curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

The following figure shows a **HEAD** request with a 200 response in Chrome Postman app.  

![HEAD request Postman example](../images/orderer_head_postman.png "HEAD request Postman example")


## Monitoring channels
{: #monitor-channnels}

Enter Network Monitor and locate the channel that you want to view and monitor in the "Channel" screen.  In the specific channel screen, you can view the data status information, members, and instantiated chaincode of this channel in three tabs:

* **Channel Overview**  
  The "Channel Overview" tab shows the block information on this channel:
    * A series of data points, which include the total number of blocks that are created, the time interval since the last transaction, the number of chaincode instantiations, and the number of chaincode invocations.
    * A table that lists all blocks on this channel.  Expand a block and you can view the detailed information about the block.  

  ![Channel overview](../images/channel_overview_detail.png "Channel overview")  

* **Members**  
  The "Members" tab shows the information of the members on this channel, including the email addresses for the organizational operators.
  ![Channel members](../images/channel_members.png "Channel members")  

* **Chaincode**  
  The "Chaincode" tab lists all the chaincode that are instantiated on this channel with chaincode ID, version, and number of peers that are running the chaincode.   

  Expand a chaincode row to get detailed information about the chaincode:  
    * You can click **JSON** to view the JSON file of the chaincode.
    * You can click **Logs** to view logs of the chaincode.
    * You can click **Delete** to remove the running chaincode container.
    **Note**: Deleting the running chaincode container does not actually delete the chaincode. An instantiated chaincode on blockchain network cannot be deleted.

  ![Channel chaincode](../images/channel_chaincode.png "Channel chaincode")


## Monitoring chaincode
{: #monitor-chaincode}

Enter Network Monitor and open the "Install Code" screen. If you have running chaincode, choose a peer from the drop-down list and you can see all chaincode for this peer in the table with chaincode IDs and versions.  You can perform installation and instantiation for your chaincode on this screen.  For more information, see [Installing, instantiating, and updating a chaincode](install_instantiate_chaincode.html).

  ![Chaincode](../images/chaincode_install_overview.png "Chaincode")


## Monitoring sample applications
{: #monitor-apps}

In a Starter Plan network, you can view and access sample applications in the "Try Samples" screen of the Network Monitor.  After you deploy a sample application, you can click the **Launch** button to enter your application interface, or the **View on GitHub** link to visit the code repository.  For more information, see [Deploying sample applications](prebuilt_samples.html).

  ![Sample applications](../images/sampleappflow0.png "Sample applications")

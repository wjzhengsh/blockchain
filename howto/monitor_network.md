---

copyright:
  years: 2017
lastupdated: "2017-12-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitoring a {{site.data.keyword.blockchain}} network

This tutorial shows how to view and monitor the status information of your {{site.data.keyword.blockchain}} network on {{site.data.keyword.Bluemix_short}}.
{:shortdesc}

Enter Network Monitor and locate the channel that you want to view and monitor in the "Channel" screen.  In the specific channel screen, you can view the data status information, members, and instantiated chaincode of this channel in three tabs:

* **Channel Overview**  
  The "Channel Overview" tab shows the block information on this channel:
    * A series of data points including the total number of blocks that are created, the time interval since the last transaction, the number of chaincode instantiations, and the number of chaincode invocations.
    * A table listing all blocks on this channel.  Expand a block and you can view the detailed information about the block.  

  ![Channel overview](../images/channel_overview_detail.png "Channel overview")  

* **Members**  
  The "Members" tab shows the information of the members on this channel, including the email addresses for the organizational operators.
  ![Channel members](../images/channel_members.png "Channel members")  
  
* **Chaincode**  
  The "Chaincode" tab lists all the chaincodes that are instantiated on this channel with chaincode ID, version, and number of peers that are running the chaincode.   
    
  Expand a chaincode row to get detailed information about the chaincode:  
    * You can click **JSON** to view the JSON file of the chaincode.
    * You can click **Logs** to view logs of the chaincode.
    * You can click **Delete** to remove the running chaincode container. 
    **Note**: Deleting the running chaincode container does not actually delete the chaincode. An instantiated chaincode on blockchain network cannot be deleted.
  
  ![Channel chaincode](../images/channel_chaincode.png "Channel chaincode") 
  

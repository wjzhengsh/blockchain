---

copyright:
  years: 2019
lastupdated: "2019-03-22"

keywords: best practices, develop applications, connectivity, availability, mutual TLS, CouchDB

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Best practices for application development
{: #best-practices-app}

This guide is for users who have already learned the basics of application development and are ready to scale their solution. Follow these best practices to maximize the performance of your network, and avoid application downtime.
{:shortdesc}

## Application connectivity and availability
{: #best-practices-app-connectivity-availability}

The Hyperledger Fabric [Transaction Flow ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaction Flow"){:new_window} spans multiple components, with the client applications playing a unique role. The SDK submits transaction proposals to the peers for endorsement. It then collects the endorsed proposals to be sent to the ordering service, which then sends blocks of transactions to the peers to be added to channel ledgers. Developers of production applications should be prepared to manage their interactions between the SDK and their networks for efficiency and availability.

### Managing transactions
{: #best-practices-app-managing-transactions}

Application clients must ensure that their transaction proposals are validated and that the proposals complete successfully. A proposal can be delayed or lost for multiple reasons, such as a network outage or a component failure. You should code your application for [high availability](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) to handle component failure. You can also [increase the timeout values](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk) in your application to prevent proposals from timing out before the network can respond.

If a chaincode is not running, the first transaction proposal that is sent to this chaincode starts the chaincode. While the chaincode is starting, all other proposals are rejected with an error that indicates that the chaincode is currently starting. This is different from transaction invalidation. If any proposal is rejected while the chaincode is starting, application clients need to resend the rejected proposals after the chaincode starts. Application clients can use a message queue to avoid losing transaction proposals.

You can use a channel-based event service to monitor transactions and build message queues. The [channelEventHub ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} class can register listeners based on transaction, block, and chaincode events. Channel-based listeners from the channel eventhub can scale to multiple channels and distinguish between traffic on different channels.

It is recommended that you use the channelEventHub rather than the old EventHub class. EventHub is single threaded and contains events from all channels that could slow down or even hang listeners across channels. The eventHub class also provides no guarantee that an event will be delivered, and provides no way of retrieving events from a certain point, such as a block number, to track events that were missed.

**Note:** The peer EventHub class will be deprecated in a future release of the Fabric SDK. If you have existing applications that use the peer EventHub class, update your applications to use the channel EventHub class instead. For more information, see [How to use the channel-based event service ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} in the Node SDK Documentation.

### Opening and closing network connections
{: #best-practices-app-connections}

When you create peer and orderer objects with the SDK before submitting transaction proposals, you are opening a gRPC connection between your application and the network component. For example, the following command opens a connection to `org1-peer1`. This connection continues to be active while your application is running.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

When you manage the connections between your application and your network, you might consider the following recommendations.

- Reuse peer and orderer objects when you interact with your network, instead of opening new connections to submit transactions. Reusing peer and orderer objects can save resources and lead to better performance.  
- To maintain a persistent connection to your network components, use [gRPC keepalives ![External link icon](images/external_link.svg "External link icon")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "gRPC Keepalives"). Keepalives keep the gRPC connection active and prevent an "unused" connection from being closed. The following example of peer connection adds gRPC options to the [Connection Options ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "Connection") object. The gRPC options are set to values that {{site.data.keyword.blockchainfull_notm}} Platform recommends.  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  You can also find these variables with the recommended settings in the `"peers"` section of your network connection profile. The recommended options will be imported into your application automatically if you use the [connection profile with the SDK](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile) to connect to your network endpoints.

- When a connection is no longer needed, use the `peer.close()` and `orderer.close()` commands to free up resources and prevent performance degradation. For more information, see the [peer close ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "peer close") and [orderer close![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "orderer close") classes in the Node SDK documentation. If you used a connection profile to add peers and orderers to a channel object, you can close all connections that are assigned to that channel by using a `channel.close()` command.

### Highly available applications
{: #best-practices-app-ha-app}

As a high availability best practice, it is strongly recommended that you deploy a minimum of two peers per organization for failover. You need to adapt your applications for high availability as well. Install chaincode on both peers and add them to your channels. Then be prepared to submit transaction proposals to both peer endpoints when setting up your network and building your peer target list. Enterprise Plan networks have multiple orderers for failover, which allows your client application to send endorsed transactions to a different orderer if one orderer is not available. If you use your [connection profile](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) instead to add network endpoints manually, ensure that your profile is up to date and that the additional peers and orderers have been added to the relevant channel in the `channels` section of the profile. The SDK can then add the components that are joined on the channel by using the connection profile.

## Enabling mutual TLS
{: #best-practices-app-mutual-tls}

If you are running Enterprise Plan networks that is at Fabric V1.1 level, you have the option of [enabling mutual TLS](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) for your applications. If you enable mutual TLS, you need to update your applications to support this function. Otherwise, your applications cannot communicate with your network.

In the Connection Profile, locate the `certificateAuthorities` section where you can find the following attributes that are necessary to enroll and get the certificates to communicate with your network by using mutual TLS.

- `url`: URL for connecting to the CA that can give out mutual TLS certificates
- `enrollId`: Enroll ID to use for getting a certificate
- `enrollSecret`: Enroll secret to use for getting a certificate
- `x-tlsCAName`: CA name to use for getting certificate that allows the application to communicate with Mutual TLS.

For more information about updating your applications to support mutual TLS, see [How to configure mutual TLS ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "mutual tls"){:new_window}.


## (Optional) Setting timeout values in Fabric SDKs
{: #best-practices-app-set-timeout-in-sdk}

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

However, you might need to change the default timeout values in your own application. For example, when your application invokes a transaction that needs more than 5000 ms, which is the default timeout value for event hub connection to respond, you might get a failing error because the invoke event ends at 5000 ms before the transaction completes. You can set the system property to overwrite the default values from your client application. Because the default values are initialized before you set the system property, the system property might not take effect. Therefore, you need to set the system property for timeout in a static construct in your client application. See the following example on changing timeout value for event hub connection to 15000 ms in Fabric Java SDK. The file path is `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

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

## Best practices when using CouchDB
{: #best-practices-app-couchdb-indices}

If you use CouchDB as your state database, you can perform JSON data queries from your chaincode against the channel's state data. It is strongly recommended that you create indexes for your JSON queries and use them in your chaincode. Indexes allow your applications to retrieve data efficiently when your network adds additional blocks of transactions and entries in the world state.

For more information about CouchDB and how to set up indexes, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} in the Hyperledger Fabric documentation. You can also find an example that uses an index with chaincode in the [Fabric CouchDB tutorial ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_tutorial.html).

Avoid using chaincode for queries that will result in a scan of the entire CouchDB database. Full length database scans will result in long response times and will degrade the performance of your network. You can take some of the following steps to avoid and manage large queries:
- Set up indexes with your chaincode.
- All fields in the index must also be in the selector or sort sections of your query for the index to be used.
- More complex queries will have a lower performance and will be less likely to use an index.
- You should try to avoid operators that will result in a full table scan or a full index scan, such as `$or`, `$in` and `$regex`.

You can find examples that demonstrate how queries use indexes and what type of queries will have the best performance in the [Fabric CouchDB tutorial ![External link icon](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes).

Peers on the {{site.data.keyword.blockchainfull_notm}} Platform have a set queryLimit, and will only return 10,000 entries from the state database. If your query hits the queryLimit, you can use multiple queries to get the remaining results. If you need more results from a range query, start subsequent queries with the last key returned by the previous query. If you need more results from JSON queries, sort your query using one of the variables in your data, then use the last value from the previous query in a 'greater than' filter for the next query.

Do not query the entire database for the purpose of aggregation or reporting. If you want to build a dashboard or collect large amounts of data as part of your application, you can query an off chain database that replicates the data from your blockchain network. This will allow you to understand the data on the blockchain without degrading the performance of your network or disrupting transactions.

You can use the channel-based event services client provided by the Fabric SDKs to build an off chain data store. For example, you can use a block listener to get the latest transactions being added to a channel ledger. The transaction read and write sets from the valid transactions can then be used to update a copy of the world state that has been stored in a separate database. For more information, see [How to use the channel-based event service ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} in the Node SDK documentation.

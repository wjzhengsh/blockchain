---

copyright:
  years: 2019
lastupdated: "2019-05-31"

keywords: logging levels, metrics, health check, peer, orderer

subcollection: blockchain

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Operating nodes with operations service
{: #operations_service}

The {{site.data.keyword.blockchainfull}} Platform is based on Hyperledger Fabric v1.4.1  The platform supports the Operations Service feature that offers a RESTful “operations” API for operators to perform node health checks, pull operational metrics from the peer and orderer nodes, and manage logging levels. The peer and the orderer host an HTTP server that offers the RESTful “operations” API.  For more information about Operations Service, see [The Operations Service](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html){: external}.
{:shortdesc}


## Considerations and limitations
{: #operations_service_consideration_limitation}

- All peer and orderer nodes are configured with `clientAuthRequired: false` so that the health checker can be accessed. Because `clientAuthRequired` is set to `false` and also mutual TLS is enabled, when you access the REST server, you need to pass the TLS identities to be able to authenticate. This setting ensures that only users with the appropriate keys can see the corresponding logs.
- Only metrics pulling model that is based on [Prometheus](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#prometheus){: external} is supported for now.

## Before you begin
{: #operations_service_before_you_begin}

You need to gather the following information from your peer and orderer.

- **`peer-endpoint`** or **`orderer-endpoint`**

  You can find the operations endpoint URL of the peer or orderer in the peer or orderer JSON file that you export from the console.

    1. Click the peer or orderer in the **Node** tab.
    2. On the peer or orderer page, click the **Settings** icon besides the peer or orderer name.
    3. In the side panel, click **Export** to save the peer or orderer JSON file.
    4. Find the operations endpoint URL that is the value of the `operations_url` parameter in the JSON file. This value is referred to as the `peer-endpoint` or `orderer-endpoint` in the commands later in this topic. For example:

      ```
      {
      "short_name": "Peer1Org1_0",
      "name": "Peer1 Org1",
      "url": "https://169.46.208.93:32739",
      "type": "fabric-peer",
      "msp_id": "org1msp",
      "operations_url": "https://169.46.208.93:32101"
      }
      ```

      ```
      {
      "short_name": "Orderer_0",
      "name": "Orderer",
      "url": "https://169.46.208.93:31612",
      "type": "fabric-orderer",
      "msp_id": "orderermsp",
      "operations_url": "https://169.46.208.93:30115"
      }
      ```

- **`client-tls-cert`** and **`client-tls-key`**

  You can find the certificate and private key of the TLS CA in the console.

  1. Click the peer or orderer's CA node in the **Node** tab.
  2. On the CA page, click **TLS Certificate Authority**.
  3. In the TLS CA table, locate the `admin` user and click the vertical dots under **Actions**. Then, click **Enroll identity**.
  4. In the side panel, click **Next** and you can see the certificate and private key of the TLS CA.
  5. Give the identity a display name and click **Export identity**  and then **Add identity to wallet** to save the certificate and private key of the TLS CA into a JSON file.
  6. Open the exported JSON file.
  7. Find the private key that is the value of the `private_key` parameter in the JSON file. This is your `client-tls-key` for use in the commands below.
  8. Find the TLS CA certificate that is the value of the `cert` parameter in the JSON file. This is your `client-tls-cert` for use in the commands below.

- **`peer tls-ca cert`** or **`orderer tls-ca cert`**

  You can find the TLS CA certificate of the peer or orderer in the console.

  1. Click the peer or orderer's node in the **Node** tab.
  2. Click the **Settings** icon beside the node name.
  3. Copy the node's TLS CA certificate string. This is your `peer tls-ca cert` or `orderer tls-ca cert` that can be used in the commands below.

**Note:** For all the certificates and keys, you must convert the certificate strings, which are in `base64` format, to individual `.pem` files by using the following commands:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > <key_name>.pem
  ```
  {:codeblock}
{:important}


## Checking node health
{: #operations_service_health_check}

Run the `curl -k <peer-endpoint>/healthz` or `curl -k <orderer-endpoint>/healthz` command to check the health of the peer or orderer node. For example:

```
curl -k https://169.46.208.93:3210/healthz
```
{:codeblock}

For more information about health checks, see [Health checks](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#health-checks){: external}.


## Viewing the metrics
{: #operations_service_view_metrics}

Run the following command to view the peer metrics. To view the orderer metrics you would run the same command but replace the `<peer-endpoint>` with the `<orderer-endpoint>`.

```
curl -k <peer-endpoint>/metrics --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

For example:

```
curl -k https://169.55.231.152:30766/metrics --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```
{:codeblock}


For more information about metrics, see [Metrics](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#metrics){: external}.


## Viewing logging levels
{: #operations_service_log_level_view}

Run the following command to view the logging level. You will see the log level on your terminal after the command completes.

```
curl -k <peer-endpoint>/logspec --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

For example:
```
curl -k https://169.46.208.93:3210/logspec --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

<!--
```
curl https://169.46.208.93:3210/logspec --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

You can see the result that is similar to the following example:

```
{"spec":"info"}
```

## Setting logging levels
{: #operations_service_log_level_set}

To change the existing logging level setting, run the following command, which uses the `PUT` method with JSON body that consists of a single attribute named `spec`. Replace `<log-level>` with your expected logging levels. For more information about the logging levels that you can set, see [Logging specification](https://hyperledger-fabric.readthedocs.io/en/release-1.4/logging-control.html#logging-specification){: external} in Hyperledger Fabric documentation.

```
curl -X PUT  <peer-endpoint>/logspec -d '{"spec":"<log-level>"}' --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

For example:
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

After you set a new logging level, you can use the [view logging level command](#operations_service_log_level_view) to check your settings.

<!--
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

For more information about log level configuration, see [Log level management](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#log-level-management){: external} in Hyperledger Fabric documentation.

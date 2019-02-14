---

copyright:
  years: 2019
lastupdated: "2019-02-14"

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Troubleshooting
{: #ibp-v2-troubleshooting}

General problems may occur when using the console to manage nodes, channels, or smart contracts. In many cases, you can recover from these problems by following a few easy steps.

## Why are my node operations failing after I create my peer or orderer?
{: #ibp-console-build-network-troubleshoot-entry1}

It is possible you may experience an error when managing an existing node. When that occurs, it is often useful to consult the node logs.  

For example, when you try to operate the node, the action may fail.
{: tsSymptoms}

After creating a new peer or orderer, depending on your cluster storage configuration, it may take a few minutes for the node to be ready for operation.
{: tsCauses}

Check your Kubernetes dashboard and ensure the peer or node status is `Running`. Then try your action again. If you are still experiencing problems after the node is up, [check your node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors.  
{: tsResolve}

## Why does my peer fails to start?
{: #ibp-console-build-network-troubleshoot-entry2}

It is possible you may experience this error under a variety of conditions.

The peer log includes `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`
{: tsSymptoms}

This error can occur if:
- When you created the peer or orderer organization MSP definition, you specified an enroll id and secret that corresponds to an identity of type `peer` and not `client`. It must be of type `client`.
- When you created the peer or orderer organization MSP definition, you specified an enroll id and secret that does not match the enroll id or secret of the corresponding organization admin identity.
- When you created the peer or orderer, you specified the enroll id and secret of an identity that is not type 'peer'.
{: tsCauses}

- Open your peer or orderer CA node and view the registered identities listed in the **Registered Users** table.
- Delete the peer or orderer and recreate it, being careful to specify the correct enroll id and secret.
- Note that before you create the peer or orderer, you need to create an organization admin id, of type 'client'. Be sure to specify that same id as the enroll id when you create the organization MSP definition. See these instructions for [registering peer identities](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) and these instructions for [registering orderer identities](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Why did my smart contract installation, instantiation or upgrade fail?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

It is possible you may experience an error when installing, instantiating or upgrading a smart contract.  For example, when you try to install a smart contract on a peer, it fails with the error `An error occurred when installing smart contract on peer.`
{: tsSymptoms}

You may receive this error if this version of the smart contract already exists on the peer, or if your peer is out of resources.
{: tsCauses}

- Open your Kubernetes dashboard and ensure the peer status is `Running`.  
- Open the peer node and ensure the smart contract version does not already exist on the peer and try again with the proper version.
- If you are still experiencing problems after the node is up, [check your node logs](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) for errors.  
{: tsResolve}

## How can I view my smart contract container logs?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

You may need to view your smart contract, or chaincode container, logs to debug a smart contract issue.
{: tsSymptoms}

- Open your Kubernetes dashboard and open your peer pod where the smart contract is running.
- Click the `exec` link from your dashboard to exec into the pod. By default it points to peer container.
- Switch to the `dind` container by selecting it from the dropdown list.
- Run the command `docker ps -a` to see the list of chaincode containers.
- Run `docker logs <chaincode-container-ID>` replacing <chaincode-container-ID> with the ID of your chaincode container.
{: tsResolve}

## My channel, smart contracts, and identities have disappeared from the console. How can I get them back?
{: #ibp-v2-troubleshooting-browser-storage}

Your console wallet identities consist of a public and private key pair that allow you to manage your blockchain components but they are only stored in your browser local storage. You are responsible for securing and managing these identities. We recommend that you export them to your file system after you create them. Whenever you create a new node, you associate an identity from your console wallet with the node. This admin identity is what allows you to manage the node. When you switch browsers or change to a browser on a different machine, these identities are no longer in your wallet. Therefore, you are unable to manage the components.
{: tsSymptoms}

One of the new features of {{site.data.keyword.blockchainfull_notm}} Platform 2.0 is that you are now responsible for securing and managing your certificates. Therefore, they are only persisted in the browser local storage to allow you manage the component.
{: tsCauses}

- Any time you create a new organization MSP definition, you generate keys for an identity that is allowed to administer the organization. Therefore, during that process you must click the **Generate** and then **Export** buttons to store the generated identity in your console wallet and then save it to your file system as a JSON file.
- To resolve this problem in your browser, you need to import those identities and associate them with the corresponding node:
  - In the browser where you are experiencing the problem, click the **Wallet** tab followed by **Add identity** to import the JSON file into your wallet.
  - Click **Upload JSON** and browse to the JSON file you exported using the **Add files** button.
  - Click **Submit**.
  - Now open the peer or orderer node that this identity was originally associated to, and click on the **Settings** icon.
  - Click the **Associate identity** button.
  - Select the identity you just imported to your console wallet from the drop down list.
  - Click **Associate**.
- Repeat this process for each identity that was in the wallet of the original browser.
{: tsResolve}

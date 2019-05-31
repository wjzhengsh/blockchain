---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: organizations, MSPs, create an MSP, MSP JSON file, consortium, system channel

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Managing organizations
{: #ibp-console-organizations}

You can use the {{site.data.keyword.blockchainfull}} Platform console to create a formal organization definition known as a Membership Services Provider (MSP). Your organization's MSP definition allows other members of the blockchain consortium to verify the identity of your nodes and applications. Your MSP definition also contains your organization's admin certificates.

You can also use the console to manage which organizations are members of your network. The administrator of the ordering service can use the organizations tab to add members to the blockchain [consortium](/docs/services/blockchain/glossary.html#glossary-consortium). Members of the consortium can then use the console to add members to new or existing channels.

**Target audience:** This topic is designed for network operators who are responsible for creating, monitoring, and managing the blockchain network.

## Understanding MSPs
{: #console-organizations-about-msp}

The {{site.data.keyword.blockchainfull_notm}} Platform is based on Hyperledger Fabric and builds permissioned blockchain networks. Participants need to be known to the network before they can submit transactions and interact with the assets on the ledger. Fabric recognizes identity through a group of invited organizations, known as the consortium. Organizations in the consortium are able to issue valid credentials to their members and let them become participants in the network. The participants can then operate blockchain nodes and submit transactions from client applications.

Each organization in the consortium needs to operate its own Certificate Authority, known as the root CA. This Certificate Authority (or its intermediate CAs) creates all the identities belonging to your organization and issues each identity a signing certificate and private key. These keys are signed by the CA and used by the members of your organization to sign and verify their actions. Joining the consortium allows other organizations to recognize your CAs signature, and verify that your peers and applications are valid participants. For more information about membership in Hyperledger Fabric, see the [Membership concept topic ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Membership") in the Fabric documentation.

Before your organization can join a consortium, it needs to create an organization definition known as a **Membership Services Provider (MSP)**. The MSP contains the following information:
- A certificate signed by your **root Certificate Authority**. This certificate is used to verify the identity of your nodes, channels, and applications.
- A certificate signed by your **TLS CA**. A root TLS certificate allows your peers to participate in cross organization gossip, which is necessary to take advantage of the [**Private data** collections](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) and [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery") features of Hyperledger Fabric.
- The **MSP ID**. The MSP ID is the formal name of your organization within the consortium. You need to remember the MSP ID for your nodes and applications.
- **Admin certificates** from your **Peer admin** and **Org Admin** identities. These certificates are passed to the Ordering Service and are used to verify which identities in your organization are allowed to create or edit channels. When you use your console to create an orderer or peer, the admin certificates inside the MSP are deployed within the new node. These certificates can then be used to operate the peers or orderers from your console or a client application.

## Managing MSPs in the console

Navigate to the **Organizations** tab. You can use this tab to [create an MSP definition](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp) by using a Certificate Authority that exists in your console. You can also use this tab to [import an MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) that was created by another organization.

You can view all of the MSPs that you have created or imported under **Available Organizations**. You can use the MSP definitions in the organizations tab for important functions within your console:
- If you are creating peer or orderer nodes, the MSP of your organization is used to provide admin certificates to the new node.
- If you are the admin of an ordering node, you can use the MSPs to [add new organizations to the consortium](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).
- If you are a member of the consortium, you can import the MSPs of other consortium members into your console and then add the members to new or existing channels.

## Creating an MSP for your organization
{: #console-organizations-create-msp}

Use the **Organizations** tab to generate an MSP definition for your organization. When you click **Create MSP**, a side panel opens that allows you to enter all the necessary information: your Root CA, the MSP ID, and your organization administrators.

- Use the **MSP details** section of the panel to select your organizations MSP ID. This ID is the formal name that other members of the network will use to refer to your organization. **Save your MSP ID.**

- Use the **Root Certificate Authority details** section to select your organization's root CA. This is the CA that you used (or will use) to create all of the node and application identities that belong to your organization. If you use intermediate CAs, this is the CA that you used to create those CAs. Select your root CA from the list of CAs managed by using your console. If you created a CA using the console, selecting a root CA will will also create a root TLS Certificate.

- You can also use the **Root Certificate Authority details** section to generate one of your organizations admin certificates. Before creating your organization MSP definition, you need to register your org and node admins with your root CA. You then need to complete the following steps in order to use these identities to operate your network:

  1. Create a signing certificate and private key for each admin identity.
  2. Provide the signing certificate of each admin identity in the MSP definition.
  3. Add the identity to your console wallet. The nodes or channels created by the console uses the certificates from the MSP to check who is a valid administrator. As a result, the same signing certificate and private key pair that you used to add an admin cert to the MSP needs to be stored inside your console wallet.

  You can use the **Create MSP definition** panel to complete these actions for one of your admin identities. After you select your root CA, complete the following steps in the **Generate organization admin certificate** section:
  1. Enter the Enroll ID and Enroll secret of an admin identity registered with your root CA. After you enter the Enroll ID and Enroll secret, choose a name to display the identity in your console wallet.
  2. Click **Generate**. This will generate a certificate and private key and automatically add the keys to your console wallet. You can then find your admin identity in your wallet by using the name you selected on this panel. These keys are only stored in your browser local storage, therefore if you change browsers, they will not be in your console wallet. You will need to export the identity from your original browser and import them into the console wallet of your new browser.
  3. Then click **Export** to download the key pair to your file system and secure them.

- The **Administrators (Optional)** section of the side panel contains the signing certificates keys of your admins. The certificate you generated by using the **Generate organization admin certificate** section can be found in the first row of the **admin certificate** field. If you want to use multiple admin identities to operate your network, you can paste the certificates of additional node or organization admins into **admin certificate** fields.

Because your admin certs are passed to your nodes and channels by using the MSP definition, you need ensure that each of your node and org admin certificates are stored in the MSP. When you use the console to create an orderer, peer, or channel, you need to **Associate** one of the identities you exported in your console wallet with the admin certificates that were provided to the MSP definition. When you encounter an **Associate identity** section or panel, select an identity you generated and saved to the wallet when creating the MSP definition.

After you have selected your root CA, MSP ID, and created your admin certs, click **Create MSP definition** to create the MSP definition. It is now listed as an organization in the Organizations tab. You will use the MSP definition when you deploy your nodes and join a blockchain consortium.

## Manually building an MSP JSON file
{: #console-organizations-build-msp}

**This option is for advanced users only who are familiar with how certificates are used in blockchain identity management.**

If you prefer to use certificates for your peer or ordering service from an **external CA**, one that is not hosted by {{site.data.keyword.IBM_notm}}, you need to build an MSP definition JSON file that represents the peer or ordering service organization MSP definition.

Note that all certificates must be encoded in base64 format.
{:important}

You can convert the contents of your certificate file, `<cert.pem>` from `PEM` format into a base64 string by running the following command on your local machine:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}


Create a JSON file by using the following format:

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name**: Specify any name to be used to identify this MSP definition in the console.
- **organization_id**: Specify an id that is used to represented this MSP internally in the console.
- **root_certs**: (Optional) Paste in an array that contains one or more root certificates from the external CA in `base64` format. You must provide either a  CA root certificate or an intermediate CA certificate, you may also provide both.
- **intermediate_certs**: (Optional) Paste in an array that contains one ore more certificates from the external intermediate CA in `base64` format. You must provide either a  CA root certificate or an intermediate CA certificate, you may also provide both.
- **admins**: Paste in the signing certificate of the organization admin in `base64` format.
- **tls_root_certs**: (Optional) Paste in an array that contains one or more root certificates from the TLS CA in `base64` format. You must provide either an external TLS CA root certificate or an external intermediate TLS CA certificate, you may also provide both.
- **tls_intermediate_certs**: (Optional) Paste in an array that contains one or more certificates from the intermediate TLS CA in `base64` format. You must provide either an external TLS CA root certificate or an external intermediate TLS CA certificate, you may also provide both.  

The following additional fields are also available in your MSP definition but are not required:
- **organizational_unit_identifiers**: A list of Organizational Units (OU) that valid members of this MSP should include in their X.509 certificate. This is an optional configuration parameter that is used when multiple organizations leverage the same root of trust and intermediate CAs, and they have reserved an OU field for their members. An organization is often divided up into multiple organizational units (OUs), each of which has a certain set of responsibilities. For example, the ORG1 organization might have both ORG1-MANUFACTURING and ORG1-DISTRIBUTION OUs to reflect these separate lines of business. When a CA issues X.509 certificates, the OU field in the certificate specifies the line of business to which the identity belongs. See this topic in the Fabric documentation on [Identity Classification ![External link icon](../images/external_link.svg "External link icon") ](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "Identity Classification") for more information.  
- **fabric_node_OUs**: Fabric-specific OUs that enable identity classification. `NodeOUs` contain information on how to distinguish clients, peers and orderers based on their OU. If the check is enforced, by setting Enabled to true, the MSP will consider an identity valid if it is an identity of a `client`, a `peer` or an `orderer`. An identity should have only one of these special OUs. See this topic for an example of [how to specify the `fabric_node_OU` in an MSP ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query "Configuration query") in the Fabric Service Discovery documentation.
- **revocation_list**: A list of certificates that are no longer valid. For X.509-based identities, these identifiers are pairs of strings known as Subject Key Identifier (SKI) and Authority Access Identifier (AKI), and are checked whenever the X.509 certificate is being used to make sure the certificate has not been revoked. See this topic  in the Fabric documentation for more information about [Certificate Revocation Lists ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "Revoking a certificate or identity").

For example, your JSON file would look similar to:

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

Save this definition as your MSP definition `JSON` file.  

You have constructed an MSP definition, which defines the organization for your peer or ordering service nodes, and uses certificates from an external CA. You can now return to the instructions that describe [How to use certificates from an external CA with your peer or orderer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

## Importing an MSP
{: #console-organizations-import-msp}

Only the orderer admin can add new organizations to the consortium. If you are the orderer admin, you will need to collect the MSP definitions of all the organizations who have been invited to the consortium and import the MSPs into the console. You can then add the MSPs to the ordering service, by using the orderer node.

After an administrator creates an MSP definition, they can use the Organizations tab to download the MSP in JSON format to their local filesystem. They can then send you the MSP JSON file in an out of band operation. Navigate to the **Organizations** tab and use **Import MSP Definition** to import the MSP file into your console. Once an MSP definition is visible in the **Available organizations** section, you can then navigate to your orderer node to [add the organization to the consortium](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Adding an organization to a consortium
{: #console-organizations-add-consortium}

The consortium of organizations is hosted by the ordering service.

If you are the administrator of the ordering service, you can use the console to add an organization to the consortium. Navigate to the **Nodes** tab and click on the ordering node. On the ordering node panel, under **Consortium members**, click **Add organization**. This will open a side panel that will allow you to select from the list of available MSP definitions that you have [imported into your organizations tab](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). You can also use the **Upload JSON** option to import the MSP definition file created by another org directly.

## Creating and editing a channel
{: #console-organizations-create-channel}

After an organization is added to the consortium, the organization can use the ordering service to create a new channel or can be added to an existing channel. The information that allows you to participate in a channel, such as joining your peers to the channel, instantiating smart contracts, and submitting transactions, is provided by using the MSP definitions.

After your organization is added to a consortium, you can create a channel by using the following steps:

1. Import the ordering node that hosts the consortium into your console. You do not need to be an administrator of the ordering node; but you need to provide the orderer node name and endpoint information in your console.
2. Import the MSPs of organizations that you want to add to the new channel into your console in the **Organizations** tab. **Note** that organizations need to be added to the consortium before they can be added to a channel.
3. Navigate to the **Channels** tab and click **Create channel**. This will open a side panel that allows you to specify the channel name, membership, and channel policies. You can add any organizations that have been added to the consortium to the new channel.

For more information about these steps, see [creating a channel](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) in the **Build a network** tutorial.

### Updating an MSP in a channel definition
{: #console-organizations-update-channel}

Over time you may need to update the certificates in an MSP definition that is already associated with a channel. When that situation occurs follow these steps to update an organization's MSP definition in the channel:  

1. Navigate to the **Channels** tab in your console.
2. Click on the channel that contains the organization MSP that you want to update and open it.
3. Click on the **Channel details** tab.
4. Click on the associated channel member's tile that you want to update.
5. If you have not already imported the updated MSP definition into the console, you can upload the file here. **Note:** This action will not update the associated MSP definition in the Organizations tab. If you have already updated the MSP definition in the Organizations tab of the console, you can select it from the drop-down list.

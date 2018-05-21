---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Creating or joining a network with Swagger APIs

{{site.data.keyword.blockchainfull}} Platform exposes a number of REST APIs that you can use to create or join a blockchain network on {{site.data.keyword.cloud_notm}}. You can try these APIs by using the [Swagger UI](swagger_apis.html) that is associated with your network.
{:shortdesc}


## Retrieving basic auth credential for API
{: #retrieve-id-token}

Before you begin, you need to create an [{{site.data.keyword.blockchainfull_notm}} Platform service instance ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain) with Starter Plan or Enterprise Plan in {{site.data.keyword.cloud_notm}}.

To use Swagger APIs to create or join a network, you need a basic authentication credential to ensure that you have access to the service instance in {{site.data.keyword.cloud_notm}}.

1. In your [{{site.data.keyword.cloud_notm}} dashboard ![External link icon](../images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/apps/), open the service instance that you created.
2. Click **Service credentials** from the left navigator.
3. Click the "New Credential" button on the **Service credentials** page to create a new credential. Give the credential a name, for example, *CreateJoin*, and click the **Add** button. You don't need to enter anything in the "Add inline configuration parameter" field.
    ![Retrieve service credentials](../images/service_credentials.gif "Retreive service credentials")
4. After the new credential is created, click **View credentials** under the **ACTIONS** header of this credential. The contents of the credential looks similar to the following example:

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    The `service_instance_id` serves as the basic auth user id and the `service_instance_token` serves as the basic auth password. Use these values as the basic authentication credential when you call the **Create network** or **Join network** APIs.

    If you create a blockchain network before you follow the preceding steps to create a service credential, the contents of the credential also includes the network information and looks similar to the following example:

    ```
    {
      "PeerOrg1": {
        "url": "https://ibmblockchain_xyz.ng.bluemix.net",
        "network_id": "92d511f7e587413c8a9848fdae595ef2",
        "key": "PeerOrg1",
        "secret": "T8eUA65l-qtznUHL10KzQ7IK-3BVWWfHu5-hpCiDdXCRQyNfeyIm1p5NT7g17l6U"
      }
    }
    ```
    {:codeblock}

    **Note**: For the **Invite** API, the `key` serves as the basic auth user id and the `secret` serves as the basic auth password.


## Checking available network locations
{: #check-location}

You can use APIs to create blockchain networks in only available network locations. Before you create a network, use the following API to check the available network locations.

```
/network-locations/available
```
{:codeblock}


## Creating a network

**Note**: If you use Starter Plan, you don't need to create a network with APIs because a default network is provided when you create the blockchain service instance in {{site.data.keyword.cloud_notm}}.

If you use Enterprise Plan, you need to complete two steps to create a network with APIs.

1. Create a blockchain service instance on {{site.data.keyword.cloud_notm}} with Enterprise Plan<!-- or Enterprise Plus Plan-->.  Retrieve your service instance ID and token as the basic auth username and password. For more information, see [Retrieving basic auth credential for API](#retrieve-id-token).

2. Call the **Create network** API by using these service credentials.

```
/networks
```
{:codeblock}

**Parameters**:
- `location_id`: The ID of an available network location. For more information, see [Checking available network locations](check-location).
- `company_name`: Your identifier as a member in the network.
- `email`: Your email address to receive notifications.
- `peers`: : Number of peers that you want to create for this member. Valid values are 0 - 6. You can also create peers for your member later in the Network Monitor UI.
- `ledger_type`: The type of ledger of this network. The valid values are levelDB and couchDB. **levelDB** is the default value.


## Inviting new members to a network

After you create a blockchain network, you can invite other members to join your network. You need to specify the ID of the network that you want to invite new members to join. The basic auth credential that is required to invite a member is different than the one that is used in the **Create network** API. <!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". --> You can get the credential to invite a member with the **Retrieve network credentials** API on the [Swagger UI ](swagger_apis##retrieving-network-credentials) or [retrieve basic auth information for API](#retrieve-id-token) from your service instance in {{site.data.keyword.cloud_notm}}.

```
/networks/{networkID}/invite
```
{:codeblock}

**Parameters**:
- `email`: The email address of the member to invite to the network.
- `company_name`: The identifier you define for the member to join the network. Invited members can change their identifiers when they join the network.

The invited member will receive an email invitation with instructions on how to join your network.


## Joining a network

If you are invited to join a blockchain network, you will receive an email invitation.  You can follow the instructions in the email or use the following API to join the network.

**Note**: Before you join a network, you need to create an {{site.data.keyword.blockchainfull_notm}} Platform service instance and retrieve your service instance ID and token as the basic auth username and password. For more information, see [Retrieving basic auth information for API](#retrieve-id-token). You need to specify the ID of the network that you want to join.

```
/networks/{networkID}/join
```
{:codeblock}

**Parameters**:
- `company_name`: Your identifier as a member in the network. This will replace the name the inviter assigned.
- `email`: Your email address to receive notifications.  This should match the email address in the invite notification.
- `peers`: Number of peers that you want to create for this member. Valid values are 0 to 6. You can also create peers for your member later in the Network Monitor.


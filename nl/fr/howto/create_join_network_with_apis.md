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

# Création ou association à un réseau à l'aide d'API Swagger APIs

La plateforme {{site.data.keyword.blockchainfull}} expose un certain nombre d'API REST que vous pouvez utiliser pour créer ou rejoindre un réseau de blockchain sur {{site.data.keyword.cloud_notm}}. Vous pouvez essayer ces API à l'aide de l"[Interface utilisateur swagger](swagger_apis.html) qui est associée à votre réseau.
{:shortdesc}


## Extraction de données d'identification de base pour l'API
{: #retrieve-id-token}

Avant de commencer, vous devez créer une instance de service de plateforme [{{site.data.keyword.blockchainfull_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) avec un plan Starter ou un plan Enterprise dans {{site.data.keyword.cloud_notm}}.

Pour utiliser des API Swagger afin de créer ou de rejoindre un réseau, vous avez besoin de données d'identification de base afin de garantir que vous avez accès à l'instance de service dans {{site.data.keyword.cloud_notm}}.

1. Dans votre tableau de bord [{{site.data.keyword.cloud_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/apps/), ouvrez l'instance de service que vous avez créée.
2. Cliquez sur **Données d'identification du service** dans le navigateur de gauche. 
3. Cliquez sur le bouton "Nouvelles données d'identification" dans la page **Données d'identification du service** pour créer de nouvelles données d'identification. Attribuez un nom à ces données d'identification, par exemple *CreateJoin*, et cliquez sur le bouton **Ajouter**. Il n'est pas nécessaire de remplir la zone "Ajouter des paramètres de configuration en ligne".
    ![Extraction des données d'identification du service](../images/service_credentials.gif "Extraction des données d'identification du service")
4. Une fois les nouvelles données d'identification créées, cliquez sur **Afficher les données d'identification** sous l'en-tête **ACTIONS** de ces données d'identification. Le contenu des données d'identification est semblable à l'exemple suivant :

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    `service_instance_id` fait office d'ID utilisateur d'authentification de base et `service_instance_token` fait office de mot de passe d'authentification de base. Utilisez ces valeurs comme données d'identification de base lorsque vous appelez les API **Créer un réseau** ou **Rejoindre le réseau**.

    Si vous créez un réseau de blockchain avant de suivre les précédentes étapes pour créer des données d'identification du service de service, le contenu des données d'identification inclut également les informations réseau et ressemble à l'exemple suivant :

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

    **Remarque **: Pour l'API **Invite**, la `clé` fait office d'ID utilisateur d'authentification de base et `secret` fait office de mot de passe d'authentification de base.


## Vérification des emplacements réseau disponibles
{: #check-location}

Vous pouvez utiliser les API pour créer des réseaux de blockchain dans les emplacements disponibles uniquement. Avant de créer un réseau, utilisez l'API suivante pour vérifier les emplacements réseau disponibles.

```
/network-locations/available
```
{:codeblock}


## Création d'un réseau

**Remarque **: Si vous utilisez le plan Starter, vous n'êtes pas tenu de créer un réseau avec des API car un réseau par défaut est fourni lorsque vous créez l'instance de service de blockchain dans {{site.data.keyword.cloud_notm}}.

Si vous utilisez un plan Enterprise, vous devez suivre deux étapes pour créer un réseau avec des API.

1. Créez une instance de service de blockchain sur {{site.data.keyword.cloud_notm}} avec le plan Enterprise<!-- or Enterprise Plus Plan-->.  Procédez à l'extraction de votre ID et jeton d'instance de service en tant que nom d'utilisateur et mot de passe d'authentification de base. Pour plus d'informations, voir [Extraction de données d'identification de base pour l'API](#retrieve-id-token).

2. Appelez l'API **Créer un réseau** à l'aide de ces données d'identification du service de service.

```
/networks
```
{:codeblock}

**Paramètres** :
- `location_id`: ID d'un emplacement réseau disponible. Pour plus d'informations, voir [Vérification des emplacements réseau disponibles](check-location).
- `company_name` : votre identificateur en tant que membre sur le réseau.
- `email` : votre adresse e-mail pour la réception des notifications.
- `peers` : nombre d'homologues que vous voulez créer pour ce membre. Les valeurs valides vont de 0 à 6. Vous pouvez également créer des homologues pour votre membre ultérieurement dans l'interface utilisateur du moniteur réseau.
- `ledger_type` : type de registre de ce réseau. Les valeurs valides sont levelDB et couchDB. **levelDB** est la valeur par défaut.


## Invitation de nouveaux membres sur un réseau

Après avoir créé un réseau de blockchain, vous pouvez inviter d'autres membres à rejoindre votre réseau. Vous devez spécifier l'ID du réseau que vous voulez inviter des membres à rejoindre. Les données d'identification d'authentification de base qui sont nécessaires pour inviter un membre sont différentes de celles qui sont utilisées dans l'API **Créer un réseau**. <!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". --> Vous pouvez obtenir les données d'identification pour inviter un membre à l'aide de l'API d'**extraction des données d'identification réseau** dans l'[interface utilisateur Swagger](swagger_apis##retrieving-network-credentials) ou [extraire les informations d'authentification de base pour l'API](#retrieve-id-token) à partir de votre instance de service dans {{site.data.keyword.cloud_notm}}.

```
/networks/{networkID}/invite
```
{:codeblock}

**Paramètres** :
- `email` : adresse e-mail du membre à inviter à rejoindre le réseau.
- `company_name` : identificateur que vous définissez pour le membre qui doit rejoindre le réseau. Les membres invités peuvent modifier leurs identificateurs lorsqu'ils rejoignent le réseau.

Le membre invité recevra une invitation par e-mail avec les instructions pour rejoindre votre réseau.


## Rejoindre un réseau

Si vous êtes invité à rejoindre un réseau, vous recevrez un e-mail d'invitation. Vous pouvez suivre les instructions fournies dans l'e-mail ou utiliser l'API pour rejoindre le réseau.

**Remarque **: Avant de rejoindre un réseau, vous devez créer une instance de service de plateforme {{site.data.keyword.blockchainfull_notm}} et procéder à l'extraction de votre ID et jeton d'instance de service en tant que nom d'utilisateur et mot de passe d'authentification de base. Pour plus d'informations, voir [Extraction des informations d'authentification de base pour l'API](#retrieve-id-token). Vous devez spécifier l'ID du réseau que vous voulez rejoindre. 

```
/networks/{networkID}/join
```
{:codeblock}

**Paramètres** :
- `company_name` : votre identificateur en tant que membre sur le réseau. Cela remplacera le nom de la personne affecté par la personne qui invite.
- `email` : votre adresse e-mail pour la réception des notifications. Elle doit correspondre à l'adresse e-mail dans la notification d'invitation.
- `peers` : nombre d'homologues que vous voulez créer pour ce membre. Les valeurs valides vont de 0 à 6. Vous pouvez également créer des homologues pour votre membre ultérieurement dans le Moniteur réseau.


---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Création ou association à un réseau à l'aide d'API Swagger
{: #swagger-network}

{{site.data.keyword.blockchainfull}} Platform expose un certain nombre d'API REST que vous pouvez utiliser pour créer ou rejoindre un réseau de blockchain sur {{site.data.keyword.cloud_notm}}. Vous pouvez essayer ces API à l'aide de l'[Interface utilisateur swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) qui est associée à votre réseau.
{:shortdesc}


## Extraction de données d'identification de base pour l'API
{: #swagger-network-retrieve-id-token}

Avant de commencer, vous devez créer une instance de service [{{site.data.keyword.blockchainfull_notm}} Platform ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) avec un plan Starter ou un plan Enterprise dans {{site.data.keyword.cloud_notm}}.

Pour utiliser des API Swagger afin de créer ou de rejoindre un réseau, vous avez besoin de données d'identification de base afin de garantir que vous avez accès à l'instance de service dans {{site.data.keyword.cloud_notm}}.

1. Dans votre tableau de bord [{{site.data.keyword.cloud_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/resources), ouvrez l'instance de service que vous avez créée.
2. Cliquez sur **Données d'identification du service** dans le navigateur de gauche.
3. Cliquez sur le bouton "Nouvelles données d'identification" dans la page **Données d'identification du service** pour créer de nouvelles données d'identification.
    1. Attribuez un nom à ces données d'identification, par exemple *CreateJoin*.
    2. Entrez **{"type": "service_instance_token"}** dans la zone "Ajouter des paramètres de configuration en ligne".
    3. Cliquez sur le bouton **Ajouter**.
    ![Extraction des données d'identification du service](../images/service_credentials.gif "Extraction des données d'identification du service")
4. Une fois les nouvelles données d'identification créées, cliquez sur **Afficher les données d'identification** sous l'en-tête **ACTIONS** de ces données d'identification. Le contenu des données d'identification est semblable à l'exemple suivant :

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```

    `service_instance_id` fait office d'ID utilisateur d'authentification de base et `service_instance_token` fait office de mot de passe d'authentification de base. Utilisez ces valeurs comme données d'identification de base lorsque vous appelez les API **Créer un réseau** ou **Rejoindre le réseau**.

    Si vous créez un réseau de blockchain avant de suivre les précédentes étapes pour créer des données d'identification du service de service, le contenu des données d'identification inclut également les informations réseau et ressemble à l'exemple suivant :

    ```
    {
      "org1": {
        "url": "https://ibp-sp.us-south.ibm-blockchain-5-prod.cloud.ibm.com",
        "network_id": "n5edf19260c998940c5933daca2be76974a",
        "key": "org1",
        "secret": "A8YeuUuNvKVbN5cVGOlwprpaaHCVxYiP0uEN0fviQB2W9_ldJLtMSOSHqkUpkqysMA"
        }
    }    
    ```

    **Remarque **: Pour l'API **Invite**, la `clé` fait office d'ID utilisateur d'authentification de base et `secret` fait office de mot de passe d'authentification de base.


## Vérification des emplacements réseau disponibles
{: #swagger-network-check-location}

Vous pouvez utiliser les API pour créer des réseaux de blockchain dans les emplacements disponibles uniquement. Avant de créer un réseau, utilisez l'API suivante pour obtenir la liste actuelle des emplacements réseau disponibles. L'exécution de cette API ne requiert pas de données d'identification.

```
https://ibmblockchain-v2.ng.bluemix.net/api/v1/network-locations/available
```
{:codeblock}

La liste des emplacements réseau retournée sera similaire à l'exemple suivant :

```
{
  "DAL": {
    "location_id": "DAL",
    "description": "Dallas",
    "status": "available"
    "swagger_url": "https://ibmblockchain-v2-dal.4.secure.blockchain.ibm.com/api-docs"
  },
  "FFT": {
    "location_id": "FFT",
    "description": "Frankfurt",
    "status": "available"
    "swagger_url": "https://ibmblockchain-v2-fft.2.secure.blockchain.ibm.com/api-docs"
  },
  "TOR": {
    "location_id": "TOR",
    "description": "Toronto",
    "status": "available"
    "swagger_url": "https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api-docs"
  }
}
```

Si vous comptez créer un réseau, sélectionnez dans la liste renvoyée par l'API l'emplacement où vous désirez le créer. Prenez note des valeurs ``location_id`` et ``swagger_url`` associées à cet emplacement.

Si vous comptez rejoindre un réseau, prenez note de la valeur ``swagger_url`` associée à l'élément ``location_id`` spécifiée dans votre courrier électronique d'invitation.

L'élément ``swagger_url`` représente le noeud final d'API à utiliser lorsque vous créez ou rejoignez un réseau à l'aide des API ci-dessous.


## Création d'un réseau

**Remarque **: Si vous utilisez le plan Starter, vous n'êtes pas tenu de créer un réseau avec des API car un réseau par défaut est fourni lorsque vous créez l'instance de service de blockchain dans {{site.data.keyword.cloud_notm}}.

Si vous utilisez un plan Enterprise, vous devez suivre deux étapes pour créer un réseau avec des API.

1. Créez une instance de service de blockchain sur {{site.data.keyword.cloud_notm}} avec le plan Enterprise<!-- or Enterprise Plus Plan-->.  Procédez à l'extraction de votre ID et jeton d'instance de service en tant que nom d'utilisateur et mot de passe d'authentification de base. Pour plus d'informations, voir [Extraction de données d'identification de base pour l'API](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token).

2. Appelez l'API **Create network** à l''aide de ces données d'identification du service. Lancez à nouveau cette API vis à vis de l'élément ``swagger_url`` extrait lors de l'étape [Vérification des emplacements réseau disponibles](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location). Accédez au ``lien swagger_url`` afin d'utiliser l'interface utilisateur Swagger pour lancer l'API Create Network, ou soumettez la commande par voie de programmation à l'aide de l'adresse URL sans ``/api-docs``. Par exemple,

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api/v1/networks
    ```
    {:codeblock}

**Paramètres** :
- `location_id`: ID d'un emplacement réseau disponible. Spécifiez la valeur de `location_id` que vous avez notée lors de la [Vérification des emplacements réseau disponibles](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location).
- `company_name` : votre identificateur en tant que membre sur le réseau.
- `email` : votre adresse e-mail pour la réception des notifications.
- `peers` : nombre d'homologues que vous voulez créer pour ce membre. Les valeurs valides vont de 0 à 6. Vous pouvez également créer des homologues pour votre membre ultérieurement dans l'interface utilisateur du moniteur réseau.
- `ledger_type` : type de registre de ce réseau. Les valeurs valides sont levelDB et couchDB. **levelDB** est la valeur par défaut.


## Invitation de nouveaux membres sur un réseau

Après avoir créé un réseau de blockchain, vous pouvez inviter d'autres membres à rejoindre votre réseau. Vous devez spécifier l'ID du réseau que vous voulez inviter des membres à rejoindre. Les données d'identification d'authentification de base qui sont nécessaires pour inviter un membre sont différentes de celles qui sont utilisées dans l'API **Créer un réseau**. <!--In order to get the basic auth information, you need to follow the same steps in "Retrieving basic auth information for API". --> Vous pouvez obtenir les données d'identification pour inviter un membre à l'aide de l'API d'**extraction des données d'identification réseau** dans l'[interface utilisateur Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-retrieving-network-credentials) ou [extraire les informations d'authentification de base pour l'API](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token) à partir de votre instance de service dans {{site.data.keyword.cloud_notm}}.

```
/networks/{networkID}/invite
```
{:codeblock}

**Paramètres** :
- `email` : adresse e-mail du membre à inviter à rejoindre le réseau.
- `company_name` : identificateur que vous définissez pour le membre qui doit rejoindre le réseau. Les membres invités peuvent modifier leurs identificateurs lorsqu'ils rejoignent le réseau.

Le membre invité recevra une invitation par e-mail avec les instructions pour rejoindre votre réseau.


## Rejoindre un réseau

Si vous êtes invité à rejoindre un réseau Blockchain, vous recevrez un e-mail d'invitation contenant les éléments `location_id` et `network id`.

1. Avant de joindre le réseau, vous devez créer une instance du service {{site.data.keyword.blockchainfull_notm}} Platform et extraire l'ID de votre instance de service avec votre nom d'utilisateur et mot de passe d'authentification de base. Pour plus d'informations, voir [Extraction des informations d'authentification de base pour l'API](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token).

2. [Vérifiez les emplacements réseau disponibles](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location) pour recevoir dans votre e-mail d'invitation la valeur de `swagger_url` pour l'élément `location_id`. Elle devrait être similaire à ceci :

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api-docs
    ```
    {:codeblock}

3. Accédez à votre ``swagger_url`` pour utiliser l'interface utilisateur Swagger pour lancer l'API Join ou soumettez par voie de programmation la requête Join à l'aide de ``swagger_url``. Remplacez ``/api-docs`` par ``/api/v1/networks/[network_id]]/join``
et renseignez `network_id` avec la valeur indiquée dans votre e-mail d'invitation. L'URL résultante devrait être similaire à ceci :

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api/v1/networks/56102acee0e4487889ef09db681bada0/join
    ```
    {:codeblock}

    **Remarque **: Pour l'API **Join**, utilisez l'ID d'instance de service et le mot de passe extraits à l'étape 1 comme nom d'utilisateur et mot de passe de l'authentification de base.

**Paramètres** :
- `company_name` : votre identificateur en tant que membre sur le réseau. Cela remplacera le nom de la personne affecté par la personne qui invite.
- `email` : votre adresse e-mail pour la réception des notifications.  Elle doit correspondre à l'adresse e-mail dans la notification d'invitation.
- `peers` : nombre d'homologues que vous voulez créer pour ce membre. Les valeurs valides vont de 0 à 6. Vous pouvez également créer des homologues pour votre membre ultérieurement dans le Moniteur réseau.

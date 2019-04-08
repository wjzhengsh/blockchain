---

copyright:
  years: 2019
lastupdated: "2019-03-20"

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

# Meilleures pratiques pour le développement d'application
{: #best-practices-app}

Ce guide s'adresse aux utilisateurs qui connaissent déjà les bases du développement d'application et qui sont prêts à faire évoluer leur solution. Ces meilleures pratiques permettent d'optimiser les performances de votre réseau et d'éviter les interruptions des applications.
{:shortdesc}

## Connectivité et disponibilité des applications
{: #best-practices-app-connectivity-availability}

Hyperledger Fabric [Flux de transactions![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flux de transactions"){:new_window} s'étend sur plusieurs composants, les applications client jouant un rôle unique. Le logiciel SDK soumet des propositions de transaction aux homologues pour validation. Il collecte ensuite les propositions validées pour leur envoi au service de tri, lequel envoie ensuite les blocs de transactions aux homologues à ajouter aux registres de canal. Les développeurs d'applications de production doivent être préparés à la gestion de leurs interactions entre le logiciel SDK et leurs réseaux à des fins d'efficacité et de disponibilité.

### Gestion de transactions
{: #best-practices-app-managing-transactions}

Les clients d'application doivent s'assurer que leurs propositions de transaction sont validées et que les propositions sont effectuées avec succès. Une proposition peut être retardée ou perdue pour de multiples raisons, comme une indisponibilité du réseau ou une défaillance de composant. Vous devez coder votre application pour la [haute disponibilité](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) afin de gérer les défaillances de composant. Vous pouvez aussi [accroître les valeurs de délai](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk) dans votre application afin d'éviter un dépassement du délai des propositions avant que le réseau puisse répondre.

Si un code blockchain n'est pas en cours d'exécution, la première proposition de transaction qui est envoyée à ce code blockchain démarre le code blockchain. Alors que le code blockchain est en cours de démarrage, toutes les autres propositions sont rejetées avec une erreur qui indique que le code blockchain est actuellement en cours de démarrage. Cela est différent de l'invalidation de transaction. Si une proposition est rejetés alors que le code blockchain est en cours de démarrage, les clients d'application doivent envoyer de nouveau les propositions rejetées une fois le code blockchain démarré. Les clients d'application peuvent utiliser une file d'attente de messages pour éviter la perte des propositions de transaction.

Vous pouvez utiliser un service d'événement basé sur un canal pour surveiller les transactions et générer des file d'attente de messages. La classe [channelEventHub ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} peut enregistrer des programmes d'écoute basés sur des événements de transaction, de bloc et de séquence d'opérations. Les programmes d'écoute basés sur un canal du canal eventhub peuvent s'adapter à plusieurs canaux et distinguer le trafic sur différents canaux.

Il est recommandé d'utiliser channelEventHub plutôt que l'ancienne classe EventHub. EventHub est doté d'une seule unité d'exécution et il contient les événements de tous les canaux qui peuvent ralentir et même bloquer les programmes d'écoute sur les canaux. La classe eventHub ne fournit également aucune garantie qu'un événement va être distribué, et elle n'offre aucun moyen d'extraire des événements depuis un certain point, comme un numéro de bloc, pour le suivi des événements qui ont été manqués.

**Remarque :** La classe EventHub de l'homologue sera dépréciée dans une future édition du logiciel SDK Fabric. Si vous avez des applications qui utilisent la classe EventHub de l'homologue, mettez à jour vos applications afin d'utiliser le canal EventHub. Pour plus d'informations, voir [How to use the channel-based event service ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} dans la documentation du logiciel SDK Node.

### Ouverture et fermeture des connexions réseau
{: #best-practices-app-connections}

Lorsque vous créez des objets homologue et service de tri avec le logiciel SDK avant de soumettre des propositions de transaction, vous ouvrez une connexion gRPC entre votre application et le composant réseau. Par exemple, la commande suivante ouvre une connexion à `org1-peer1`. Cette connexion demeure active pendant que votre application est en cours d'exécution.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

Lorsque vous gérez les connexions entre votre application et votre réseau, vous pouvez tenir compte des recommandations suivantes.

- Réutilisez les objets homologue et service de tri lorsque vous interagissez avec votre réseau, au lieu d'ouvrir de nouvelles connexions pour soumettre des transactions. La réutilisation d'objets homologue et service de tri permet d'économiser des ressources et d'obtenir de meilleures performances.  
- Pour maintenir une connexion permanente entre les composants de votre réseau, utilisez des [signaux de présence gRPC![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "Signaux de présence gRPC"). les signaux de présence maintiennent la connexion gRPC active et empêchent la fermeture d'une connexion "inutilisée". L'exemple suivant d'une connexion d'homologue ajoute des options gRPC à l'objet the [Options de connexion![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "Connexion"). Les options gRPC sont définies sur des valeurs recommandées par {{site.data.keyword.blockchainfull_notm}} Platform.  
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

  Vous pouvez également trouver ces variables avec les valeurs recommandées dans la section `"peers"` du profil de connexion de votre réseau. Les options recommandées seront importées dans votre application automatiquement si vous utilisez le [profil de connexion avec le logiciel SDK](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile) pour vous connecter aux noeuds finaux de votre réseau.

- Lorsqu'une connexion n'est plus utilisée, utilisez les commandes `peer.close()` et `orderer.close()` pour libérer des ressources et éviter une dégradation des performances. Pour plus d'informations, voir les classes [peer close ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "peer close") and [orderer close![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "orderer close") dans la documentation du logiciel SDK Node. Si vous avez utilisé un profil de connexion pour ajouter des homologues et des services de tri à un objet de canal, vous pouvez fermer toutes les connexions qui sont affectées à ce canal à l'aide de la commande `channel.close()`.

### Applications hautement disponibles
{: #best-practices-app-ha-app}

Comme pratique optimale pour haute disponibilité, nous vous recommandons fortement de déployer au moins deux homologues par organisation pour ma reprise en ligne. Vous devez également adapter vos applications pour la haute disponibilité. Installez du code blockchain sur les deux homologues chaincode et ajoutez-les à vos canaux. Ensuite, soyez préparé à la soumission de propositions de transaction sur les deux noeuds finaux homologue lors de la configuration de votre réseau et la génération de votre liste cible d'homologues. Les réseaux de plan Enterprise comportent plusieurs services de tri pour le basculement, ce qui permet à votre application client d'envoyer des transactions validées à un service de tri différent si un service de tri n'est pas disponible. Si vous utilisez votre [profil de connexion](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) au lieu d'ajouter des noeuds finaux de réseau manuellement, assurez-vous que votre profil est à jour et qu'un canal pertinent a été ajouté aux homologues et services de tri supplémentaires dans la section `channels` du profil. Le logiciel SDK ajoute ensuite les composants rejoints sur le canal à l'aide du profil de connexion.

## Activation de TLS mutuel
{: #best-practices-app-mutual-tls}

Si vous exécutez des réseaux de plan Enterprise au niveau Fabric V1.1, vous avez la possibilité d'[activer le TLS mutuel](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) pour vos applications. Si vous activez TLS mutuel, vous devez mettre à jour vos applications afin de prendre en charge cette fonction. Sinon, vos applications ne peuvent pas communiquer avec votre réseau.

Dans le profil de connexion, localisez la section `certificateAuthorities` où se trouvent les attributs suivants qui sont nécessaires à l'inscription et à l'obtention des certificats pour communiquer avec votre réseau à l'aide de TLS mutuel.

- `url` : URL pour la connexion à l'autorité de certification qui peut fournir les certificats TLS mutuel
- `enrollId` : ID d'inscription à utiliser pour l'obtention d'un certificat
- `enrollSecret` : valeur confidentielle d'inscription à utiliser pour l'obtention d'un certificat
- `x-tlsCAName` : nom d'autorité de certification à utiliser pour l'obtention d'un certificat qui permet à l'application de communiquer avec TLS mutuel.

Pour plus d'informations sur la mise à jour de vos applications pour la prise en charge de TLS mutuel, voir [Comment configurer le TLS mutuel ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "TLS mutuel"){:new_window}.


## (Facultatif) Définition de valeurs de délai dans les logiciels SDK Fabric
{: #best-practices-app-set-timeout-in-sdk}

Les logiciels SDK Fabric définissent des valeurs de délai par défaut dans les applications client pour les événements du réseau de blockchain. Consultez l'exemple suivant concernant les paramètres de délai par défaut dans le kit SDK Fabric Java. Le chemin du fichier est `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

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

Cependant, vous devrez peut-être modifier les valeurs de délai par défaut dans votre propre application. Par exemple, si votre application appelle une transaction qui a besoin de plus de 5000 millisecondes, qui correspond à la valeur de délai par défaut pour la connexion au concentrateur d'événements, en réponse, vous obtenez une erreur car l'événement d'appel s'achève à 5000 ms avant la fin de la transaction. Vous pouvez définir la propriété système afin de remplacer les valeurs par défaut de votre application client. Etant donné que les valeurs par défaut sont initialisées avant que vous ne définissiez la propriété système, celle-ci pourrait ne pas être prise en compte. Par conséquent, vous devez définir la propriété système du délai dans une construction statique de votre application client. Consultez l'exemple suivant sur la définition de la valeur de délai pour la connexion au concentrateur d'événements sur 15000 ms dans le kit SDK Java. Le chemin du fichier est `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Si vous utilisez le logiciel SDK Node, vous pouvez définir les valeurs de délai d'expiration directement dans la méthode appelée. Par exemple, vous pourriez utiliser la ligne ci-dessous pour augmenter la valeur du délai pour [instancier un code blockchain![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") sur 5 minutes.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## Meilleures pratiques lors de l'utilisation de CouchDB
{: #best-practices-app-couchdb-indices}

Si vous utilisez CouchDB comme base de données d'état, vous pouvez exécuter des requêtes de données JSON depuis votre code blockchain sur les données d'état du canal. Nous vous recommandons fortement de créer des index pour vos requêtes JSON et de les utiliser dans votre code blockchain. Les index permettent à vos applications d'extraire les données de manière aussi efficace que votre réseau ajoute des blocs de transaction et des entrées supplémentaires dans le World State.

Pour plus d'informations sur CouchDB et la façon de définir des index, voir [CouchDB as the State Database ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} dans la documentation Hyperledger Fabric. Vous trouverez également un exemple utilisant un index avec un code blockchain dans le[tutoriel Fabric CouchDB![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html).

Evitez d'utiliser du code blockchain pour les requêtes qui aboutissent à une analyse de la totalité de la base de données CouchDB. Une base de données de longueur intégrale analyse les résultats avec de longs temps de réponse et dégrade les performances du réseau. Vous pouvez effectuer certaines des étapes suivantes afin d'éviter et de gérer des requêtes longues :
- Définissez des index avec votre code blockchain.
- Toutes les zones de l'index doivent également figurer dans les sections de sélecteur ou de tri de votre requête pour que l'index puisse être utilisé.
- Les requêtes plus complexes présenteront des performances inférieures et seront moins susceptibles d'utiliser un index.
- Vous devez essayer d'éviter des opérateurs qui entraîneront une analyse complète de tableau, ou une analyse complète d'index, par exemple `$or`, `$in` et `$regex`.

Des exemples illustrant comment les requêtes utilisent les index et le type de requêtes présentant les meilleures performances sont fournis dans le dans [tutoriel Fabric CouchDB![Icône de lien externe](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes).

Les homologues sur {{site.data.keyword.blockchainfull_notm}} Platform ont une valeur queryLimit définie, et ils ne renvoient que 10 000 entrées de la base de données d'état. Si votre requête atteint la valeur de queryLimit, vous pouvez utiliser plusieurs requêtes pour obtenir les résultats restants. Si vous avez besoin de résultats supplémentaires d'une requête de plage, lancez les requêtes suivantes avec la dernière clé renvoyée par la requête précédente. Si vous avez besoin de résultats supplémentaires de requêtes JSON, triez votre requête à l'aide de l'une des variables de vos données, puis utilisez la dernière valeur de la requête précédente dans un filtre 'greater than' pour la requête suivante.

N'interrogez pas l'intégralité de la base de données à des fins d'agrégation ou de génération de rapports. Si vous souhaitez générer un tableau de bord ou collecter de grandes quantités de données dans le cadre de votre application, vous pouvez interroger une base hors chaîne qui réplique les données depuis votre réseau de blockchain. Cela vous permettra de comprendre les données sur la blockchain sans dégradation des performances de votre réseau ni interruption des transactions.

Vous pouvez utiliser le client de services d'événement basé sur un canal fourni par les logiciels SDK de Fabric pour générer un magasin de données hors chaîne. Par exemple, vous pouvez utiliser un programme d'écoute pour bloquer les dernières transactions ajoutées à un registre de canal. Les ensembles de lecture et d'écriture des transaction valides peuvent ensuite être utilisés pour mettre à jour une copie du world state qui a été stocké dans une base de données distincte. Pour plus d'informations, voir [How to use the channel-based event service ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} dans la documentation du logiciel SDK Node.

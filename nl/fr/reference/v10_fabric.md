---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Fabric
{: #hyperledger-fabric}

Le réseau {{site.data.keyword.blockchainfull}} repose sur la pile Hyperledger Fabric V1.0, projet de blockchain au sein du projet Hyperledger de Linux Foundation. Il s'agit d'un réseau "privé" dans lequel l'ensemble des utilisateurs et des composants ont des identités connues. Une logique de signature/vérification est mise en oeuvre à chaque point de contact de communication, et les transactions sont accordées par le biais d'une série de contrôles d'adhésion et de validation. En ce sens, il diffère grandement des implémentations de blockchain traditionnelles qui encouragent l'anonymat et sont forcées de s'appuyer sur des crypto-monnaies et de lourdes contraintes de calcul pour valider les transactions.
{:shortdesc}

Hyperledger Fabric propose une architecture modulaire qui permet d'étendre l'évolutivité et les performances. Cette rubrique présente certains des principaux composants dans Hyperledger Fabric. Pour une présentation complète de Hyperledger Fabric, consultez la [documentation Hyperledger Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Homologues
{: #hyperledger-fabric-peer}

A un niveau physique, un réseau de blockchain se compose essentiellement de noeuds homologues (ou, simplement, d'homologues). Les homologues sont un élément fondamental du réseau car ils hébergent des registres et des contrats intelligents (qui sont contenus dans un ["code blockchain" ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "Espace de nom de code blockchain")). Plus précisément, l'homologue héberge des **instances** du registre et des **instances** de contrats intelligents . Comme les contrats intelligents et les registres sont utilisés pour encapsuler les processus partagés et les informations partagées au sein d'un réseau, respectivement, ces aspects d'un homologue constituent un bon point de départ pour comprendre ce que fait réellement un réseau Fabric.

Pour en savoir plus sur les homologues en particulier, consultez [ce document consacré uniquement aux homologues![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/peers/peers.html) dans la documentation de la communauté Fabric.

## Autorité de certification
{: #hyperledger-fabric-certificate-authority}

En tant que plateforme pour les réseaux de blockchain **privés**, Hyperledger Fabric inclut un composant **Autorité de certification (CA)** modulaire pour la gestion des identités réseau de l'ensemble des organisations membres et leurs utilisateurs. L'exigence d'une identité privée pour chaque utilisateur permet un contrôle ACL sur l'activité réseau et garantit que chaque transaction est finalement traçable jusqu'à un utilisateur inscrit.
* L'autorité de certification émet un certificat racine (**rootCert**) pour chaque **membre** (organisation ou personne) autorisé à rejoindre le réseau.
* L'autorité de certification émet un certificat d'enregistrement (**eCert**) pour chaque composant de membre, des applications côté serveur et parfois des utilisateurs.
* Une allocation de certificats de transaction (**tCerts**) est également accordée à chaque utilisateur inscrit. Chaque **tCert** autorise une transaction réseau.

Ce contrôle basé sur des certificats au niveau de l'appartenance réseau et des actions permet aux membres de restreindre l'accès des canaux privés et confidentiels, des applications et des données, à des identités utilisateurs spécifiques.

Pour plus d'informations sur le composant Autorité de certification d'Hyperledger Fabric, consultez le document [Fabric CA User’s Guide ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/){:new_window}.

## Membership Service Provider (Fournisseur de services aux membres)
{: #hyperledger-fabric-membership-service-provider}

Hyperledger Fabric inclut un composant **Membership Service Provider (MSP)** qui offre une abstraction de tous les mécanismes cryptographiques et protocoles sous-jacents à l'émission et la validation de certificats, et à l'authentification utilisateur. Le fournisseur MSP est installé sur chaque homologue de canal afin de garantir que les demandes de transaction qui sont émises pour l'homologue proviennent d'une identité utilisateur authentifiée et autorisée.

Pour plus d'informations sur le composant Hyperledger Fabric Membership Services Provider, voir [Membership ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html){:new_window} dans la [documentation Hyperledger Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Service de tri
{: #hyperledger-fabric-ordering-service}

Dans d'autres blockchains distribuées, comme Ethereum et Bitcoin, il n'y a pas d'autorité centrale qui trie les transactions et les envoie aux homologues. Hyperledger Fabric, blockchain sur laquelle {{site.data.keyword.blockchainfull_notm}} Platform est basé, fonctionne différemment. Elle dispose d'un noeud appelé **service de tri**.

Les services de tri sont des composants essentiels dans un réseau car ils effectuent quelques fonctions essentielles :

- Ils **trient** littéralement les blocs de transactions qui sont envoyés aux homologues afin qu'ils puissent être écrits dans leurs registres, et ce processus est appelé "tri". Si ces transactions étaient regroupées et triées au niveau des homologues eux-mêmes, cela augmenterait le risque qu'un homologue écrive une transaction dans son registre et pas un autre, ce qui créerait une bifurcation d'état.
- Ils gèrent le **canal du système de tri**, où résident le **consortium** et la liste des organisations autorisées à créer des canaux.
- Ils effectuent des vérifications de validation d'identité importantes. Par exemple, si une organisation essaie de créer un canal lorsqu'elle n'est pas membre du consortium du service de tri, la demande est rejetée. Les services de tri sont également validés en fonction de comportements dans les canaux de transaction, par exemple le droit de modification d'une configuration de canal.

Hyperledger Fabric prend actuellement en charge les implémentations de service de tri SOLO (un noeud de service de tri) et Kafka. Pour plus d'informations sur le service de tri Hyperledger Fabric, consultez la rubrique [Bringing up a Kafka-based Ordering Service ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/kafka.html){:new_window} dans la [documentation Hyperledger Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Logiciels SDK Fabric
{: #hyperledger-fabric-fabric-sdks}

Les logiciels SDK Hyperledger Fabric permettent aux développeurs d'applications de générer des applications qui interagissent avec un réseau de blockchain. Ces logiciels SDK aident les applications à gérer le cycle de vie des canaux et du code blockchain.

Hyperledger Fabric offre à la fois un logiciel SDK Node.js et Java, ainsi que les fonctions suivantes pour l'interaction avec le réseau de blockchain :

* Enregistrer et inscrire des utilisateurs
* Créer des canaux
* Joindre des homologues à un canal
* Mettre à jour la configuration du canal système ou du canal application
* Installer de code blockchain sur les homologues
* Instancier de code blockchain sur un canal
* Mettre à jour de code blockchain sur un canal
* Appeler des fonctions de code blockchain pour la mise à jour du registre
* Interroger le registre au sujet de transactions, blocs ou clés spécifiques
* Surveiller les événements sur un canal (validation d'une transaction, par exemple)

Pour plus d'informations sur les logiciels SDK Fabric, consultez la rubrique [Hyperledger Fabric SDKs![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/fabric-sdks.html){:new_window} dans la [documentation Hyperledger Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Flux de transactions
{: #hyperledger-fabric-transaction-flow}

Pour garantir la cohérence et l'intégrité des données, Hyperledger Fabric met en oeuvre plusieurs points de contrôle via le flux de transactions, notamment l'authentification de client, l'adhésion, le tri et l'engagement dans le registre.

La **Figure 1** illustre le flux de transactions sur un réseau de blockchain Hyperledger Fabric :
![Flux de transactions](../images/v10_txflow.png "Flux de transactions sur un réseau de blockchain Hyperledger Fabric")
*Figure 1. Flux de transactions sur un réseau de blockchain Hyperledger Fabric*

Sur un réseau Hyperledger Fabric V1.0, le flux de données pour les requêtes et les transactions est initié par une application côté client qui soumet une demande de transaction à un homologue sur un canal. Le flux de données initial au sein du réseau est commun aux requêtes et aux transactions:

1. Grâce aux API disponibles dans le logiciel SDK, une application client signe et soumet une proposition de transaction aux homologues de validation appropriés sur le canal spécifié. Cette proposition de transaction initiale est une **demande** pour la validation.
2. Chaque homologue du canal vérifie l'identité et l'autorité du client qui soumet, et (si elle est valide) exécute le code blockchain sur les entrées fournies. En fonction des résultats de transaction et de la règle de validation pour le code blockchain appelé, chaque homologue retourne une réponse OUI ou NON signée à l'application. Chaque réponse signée OUI est une **validation** de la transaction.

	A ce stade du flux de transactions, le processus diverge pour les requêtes et les transactions. Si la proposition a appelé une fonction de requête dans le code blockchain, l'application retourne les données au client. Si la proposition a appelé une fonction dans le code blockchain pour mettre à jour le registre, l'application continue avec les étapes suivantes :
3. L'application réachemine la transaction, laquelle inclut le jeu de lecture/écriture et les validations, vers le **service de tri**.
4. La transaction est ensuite relayée jusqu'au service de tri. Tous les homologues du canal valident chaque transaction dans le bloc en appliquant la règle de validation spécifique au code blockchain et en exécutant une vérification de version de contrôle des accès concurrents.
	* Les transactions qui échouent au processus de validation sont marquées comme étant non valides dans le bloc, et ce dernier est ajouté à la chaîne de hachage du canal.
	* Les transactions valides mettent à jour la base de données d'état en fonction des paires clé/valeur modifiées.

Le **protocole de dissémination des données gossip** diffuse en continu les données du registre sur le canal afin de garantir des registres synchronisés entre les homologues. Pour plus d'informations, consultez la rubrique [Gossip data dissemination protocol![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html){:new_window} dans la [documentation Hyperledger Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

Pour une présentation étape par étape du flux de transactions, voir [Transaction Flow![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html){:new_window} dans la [documentation Hyperledger Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

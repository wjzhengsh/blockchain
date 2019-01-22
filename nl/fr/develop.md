---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Développement de réseaux d'entreprise
{: #develop-the-network}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Le développement de solutions de blockchain à l'aide de {{site.data.keyword.blockchainfull}} Platform permet d'optimiser le jeu d'outils de développement en open source d'Hyperledger Composer. Hyperledger Composer utilise un langage de modélisation spécifique, qui est associé à des transactions JavaScript, ainsi qu'à des règles de contrôle d'accès pour modéliser un réseau d'entreprise de modèle en totalité _avant_ tout déploiement dans un réseau de blockchain réel.
{:shortdesc}

**Remarque :** Le plan Starter fournit des applications que vous pouvez étudier et tester. Vous pouvez ainsi exécuter des modèles d'application dans le plan Starter avant de développer votre réseau d'entreprise personnalisé. Pour plus de détails, voir [A propos du plan Starter](starter_plan.html).

Ce tutoriel décrit toutes les étapes du développement d'une solution {{site.data.keyword.blockchain}} qui vont de la création et de la modélisation d'un réseau d'entreprise à son déploiement dans un réseau de blockchain opérationnel.

Les étapes suivantes décrivent le développement de base d'une solution {{site.data.keyword.blockchain}} qui est hébergée sur {{site.data.keyword.blockchainfull_notm}} Platform :

1. Créez la structure de données qui va accueillir la définition de réseau d'entreprise. Le réseau d'entreprise se définit en termes de ressources, de participants et de transactions à l'aide du [langage de modélisation](https://hyperledger.github.io/composer/latest/reference/cto_language).

2. Modélisez le réseau d'entreprise en définissant les classes de ressource, les classes de participant, les classes de transaction, les classes d'événement et les règles de contrôle d'accès qui seront utilisées après le déploiement.

3. Packagez le réseau d'entreprise défini dans un fichier déployable unique.

4. Déployez le réseau d'entreprise packagé sur {{site.data.keyword.blockchainfull_notm}} Platform. Une fois le réseau d'entreprise déployé, un serveur REST peut être généré pour autoriser l'intégration à des applications externes.

## Avant de commencer

Vous devez vérifier que vous avez installé les outils de développement d'[{{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop_install.html).

## Etape 1 : Créer la structure du réseau d'entreprise

La **définition de réseau d'entreprise (BND)** est un concept essentiel en termes de développement de solutions {{site.data.keyword.blockchain}}. La définition de réseau d'entreprise définit le modèle de données, la logique de transaction et les règles de contrôle d'accès de votre solution de blockchain. Pour créer une définition de réseau d'entreprise,  vous devez créer une structure de projet appropriée sur le disque.

Le moyen le plus simple pour commencer consiste à utiliser le générateur Yeoman pour créer un squelette de réseau d'entreprise. Le générateur Yeoman crée un répertoire qui contient tous les composants d'un réseau d'entreprise, lesquels peuvent être modifiés pour votre cas d'utilisation particulier.

1. Créez un nouveau répertoire pour votre projet et accédez à la ligne de commande.

2. Depuis votre nouveau répertoire, utilisez le générateur Yeoman pour créer un squelette de réseau d'entreprise. La commande suivante requiert un nom de réseau d'entreprise, une description, un nom d'auteur, une adresse e-mail d'auteur, un choix de licence et un espace de nom :
    ```
    yo hyperledger-composer:businessnetwork
    ```
    {:codeblock}

3. Entrez `tutorial-network` comme nom de réseau, ainsi que les informations de votre choix pour la description, le nom d'auteur et l'adresse e-mail d'auteur.

4. Sélectionnez la licence `Apache-2.0`.

5. Sélectionnez l'espace de nom `org.acme.biznet`.

## Etape 2 : Définir le réseau d'entreprise

Un réseau d'entreprise se compose de ressources, de participants, de transactions, de règles de contrôle d'accès et parfois d'événements et de requêtes. Le squelette de réseau d'entreprise qui est créé à l'étape 1 inclut un fichier de modèle (`.cto`), qui contient les définitions de classe de l'ensemble des ressources, participants et transactions dans le réseau d'entreprise. Le squelette de réseau d'entreprise comprend également un document de contrôle d'accès (`permissions.acl`) avec des règles de contrôle d'accès de base, un fichier script (`logic.js`) contenant les fonctions de processeur de transaction ainsi qu'un fichier de pack (`package.json`) contenant les métadonnées du réseau d'entreprise.

### Modélisation des ressources, des participants et des transactions

Le premier document à mettre à jour est le fichier de modèle (`.cto`). Le fichier modèle est écrit à l'aide du [langage de modélisation Hyperledger Composer](https://hyperledger.github.io/composer/latest/reference/cto_language). Le fichier de modèle contient les définitions de chaque classe de ressource, transaction, participant et événement. Il étend implicitement le modèle de système qui est décrit dans la documentation du langage de modélisation.

1. Ouvrez le fichier de modèle `org.acme.biznet.cto` dans un éditeur de votre choix. Il se trouve dans le dossier `models` du réseau d'entreprise que vous avez créé à la dernière étape.

2. Remplacez le contenu par les informations suivantes :

    ```
        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }
    ```
    {:codeblock}

3. Sauvegardez les modifications que vous avez apportées au fichier `org.acme.biznet.cto`.


### Ajout d'une logique de transaction JavaScript

Dans le fichier de modèle, une transaction `Trade` a été définie, laquelle indique une relation avec une ressource et un participant. La fonction processeur de transaction contient la logique JavaScript qui permet d'exécuter les transactions définies dans le fichier de modèle.

La transaction `Trade` est simplement destinée à l'acceptation de l'identificateur de la ressource `Commodity` qui est échangée, et l'identificateur du participant `Trader` à définir comme nouveau propriétaire.

1. Ouvrez le fichier script `logic.js` dans le répertoire `lib`.

2. Remplacez le contenu par les informations suivantes :

    ```
        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }
    ```
    {:codeblock}

3. Sauvegardez les modifications que vous avez apportées au fichier `logic.js`.

### Ajout d'un contrôle d'accès

Le contrôle d'accès est un élément clé d'un réseau d'entreprise. Tous les réseaux d'entreprise doivent disposer d'un fichier de contrôle d'accès qui est appelé `permissions.acl`. Chaque fois qu'un processus est appelé dans un réseau d'entreprise déployé, la liste de contrôle d'accès est consultée afin de vérifier que l'identité ou le participant qui appelle peut appeler l'opération.

Pour les besoins de ce tutoriel, configurez une liste de contrôle d'accès (ACL) simple. Notez qu'elle ne doit pas être déployée dans un environnement de production.

1. Créez un fichier `permissions.acl` dans le répertoire `tutorial-network`.

2. Ajoutez les règles de contrôle d'accès suivantes pour `permissions.acl`:

    ```
        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }
    ```
    {:codeblock}

3. Sauvegardez les modifications que vous avez apportées au fichier `permissions.acl`.

## Etape 3 : Packager le réseau d'entreprise

Une fois le réseau d'entreprise défini, il doit être packagé dans un fichier archive de réseau d'entreprise déployable (`.bna`).

1. Depuis la ligne de commande, accédez au répertoire `tutorial-network`.

2. Depuis le répertoire `tutorial-network`, exécutez la commande suivante :

    ```
    composer archive create -t dir -n .
    ```
    {:codeblock}

Une fois la commande exécutée, un fichier archive de réseau d'entreprise (`tutorial-network@0.0.1.bna`) est créé dans le répertoire `tutorial-network`.

## Etape 4 : Déployer le réseau d'entreprise et générer un serveur REST

Déployez le réseau d'entreprise sur {{site.data.keyword.blockchainfull_notm}} Platform à l'aide du [plan Starter](./develop_starter.html) ou du [plan Enterprise](./develop_enterprise.html).

Une fois le réseau d'entreprise déployé sur {{site.data.keyword.blockchainfull_notm}} Platform, un serveur REST spécifique peut être généré. Ce serveur REST examine le contenu du réseau d'entreprise et génère des appels d'API RESTful pour les ressources, les transactions et les participants du réseau. Les instructions relatives à la génération d'un serveur REST figurent dans la [documentation Hyperledger Composer](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).

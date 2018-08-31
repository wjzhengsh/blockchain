---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}

La plateforme {{site.data.keyword.blockchainfull}} : Develop repose sur le jeu d'outils Hyperledger Composer en open source, projet au sein du projet Hyperledger de Linux Foundation. Hyperledger Composer permet aux architectes et aux développeurs de rapidement créer des solutions {{site.data.keyword.blockchain}} à l'aide d'API REST qui exposent la logique métier à des applications Web ou mobiles, et intègre également du code blockchain à des systèmes d'entreprise d'enregistrement existants.
{:shortdesc}

Hyperledger Composer se compose d'un environnement d'exécution, d'une interface de ligne de commande, d'un serveur REST, d'un connecteur LoopBack, d'une interface utilisateur Playground, d'un générateur de code Yeoman et des plug-ins d'éditeur VSCode et Atom. Pour plus d'informations sur Hyperledger Composer, consultez la [documentation Hyperledger Composer![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)


## Réseaux d'entreprise

Hyperledger Composer vous permet de rapidement modéliser votre réseau d'entreprise existant, lequel contient vos ressources existantes et les transactions qui leur sont associées ; les ressources sont des biens matériels ou immatériels, services ou propriétés. Dans le cadre de votre modèle de réseau d'entreprise, vous définissez les transactions qui peuvent interagir avec les ressources. Les réseaux d'entreprise incluent également les participants qui interagissent avec eux, chacun d'eux pouvant être associé à une identité unique, au sein de plusieurs réseaux d'entreprise.

Pour plus d'informations sur la génération de réseaux d'entreprise, voir [Développement de réseaux d'entreprise](./develop.html) ou consultez la [documentation Hyperledger Composer ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/latest/introduction/introduction.html).

## Requêtes

Les requêtes sont utilisées pour renvoyer des données relatives à la blockchain "world state". Les requêtes sont définies au sein d'un réseau d'entreprise, et elles peuvent inclure des paramètres de variable pour une personnalisation simple. Grâce aux requêtes, les données peuvent être facilement extraites de votre réseau {{site.data.keyword.blockchain}}. Les requêtes sont envoyées à l'aide de l'[API Hyperledger Composer![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/latest/api/api-doc-index).

Pour plus d'informations sur l'utilisation de requêtes dans votre réseau d'entreprise, voir [Querying and filtering business network data ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/business-network/query).

## Identités et cartes de réseau d'entreprise

Au sein d'un réseau d'entreprise, les participants sont associés à une identité. Les cartes de réseau d'entreprise sont une combinaison d'une identité, d'un profil de connexion, et de métadonnées. Elles sont utilisés pour la connexion à un réseau d'entreprise et comme preuve d'identité pour les transactions. Les cartes de réseau d'entreprise simplifient le processus de connexion à un réseau d'entreprise, et elles étendent le concept d'une identité en dehors du réseau d'entreprise à un 'portefeuille' d'identités, lequel est associé à un réseau d'entreprise et un profil de connexion spécifiques.

Pour plus d'informations sur les identités et les cartes de réseau d'entreprise, voir [Participants and identities ![Icône de lien externen](../images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/managing/participantsandidentities).

## Serveur REST de Hyperledger Composer

Le serveur REST de Hyperledger Composer génère automatiquement une API REST Swagger pour un réseau d'entreprise. Le serveur REST repose sur LoopBack et il convertit le modèle de réseau d'entreprise en une définition Open API. Lors de l'exécution, le serveur REST implémente un support Create, Read, Update et Delete pour les ressources et les participants et il autorise la soumission de transactions pour leur traitement ou leur extraction à l'aide de requêtes.

Pour plus d'informations sur le serveur REST de Hyperledger Composer, voir [Generating a REST API ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/integrating/getting-started-rest-api).

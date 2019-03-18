---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Installation d'un environnement de développement de réseau d'entreprise
{: #installing-a-development-environment}

**IBM recommande d'utiliser Hyperledger Composer uniquement pour les démonstrations et les validations de concept. IBM n'assure pas de support pour les réseaux utilisant Hyperledger Composer en production, y compris l'interface CLI Composer, les API JavaScript, le serveur REST et l'aire de jeu Web.**

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Suivez ces instructions pour obtenir les outils de développement d'{{site.data.keyword.blockchainfull}} Platform: Develop pour créer et tester des réseaux d'entreprise. Pour une plus grande souplesse et un nombre maximum de scénarios de développement, test et déploiement, les outils de développement sont fournis sous la forme de modules npm, qui peuvent être contrôlés depuis la ligne de commande.

## Avant de commencer

Assurez-vous d'installer les éléments prérequis suivants :

- Ubuntu Linux 14.04 / 16.04 LTS (64 bits) ou Mac OS 10.12
- Node version 8.9 ou suivante (la version 9 n'est pas prise en charge)
- npm version 5.x
- git version 2.9.x ou suivante
- Python version 2.7.x
- Editeur de code de votre choix, par exemple VSCode.

Si vous utilisez le plan Enterprise, installez les autres éléments prérequis suivants :

- Docker Engine version 17.03 ou suivante
- Docker-Compose version 1.8 ou suivante

Si vous utilisez VSCode, une extension VSCode Hyperledger Composer est disponible [ici](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).

## Etape 1 : Installer les outils de ligne de commande

Les outils de ligne de commande incluent **composer-cli**, module de ligne de commande principal d'{{site.data.keyword.blockchainfull_notm}} Platform: Develop. Les modules **generator-hyperledger-composer**, **composer-rest-server** et **Yeoman** sont utilisés pour générer des réseaux d'entreprise, des serveur REST spécifiques et des applications Angular. La version que vous installez dépend de la version d'Hyperledger Fabric exécutée par votre plan Starter ou Enterprise. Pour connaître votre version Fabric, ouvriez la [fenêtre Préférences réseau](../v10_dashboard.html#network-preferences) dans votre Moniteur réseau.

1. Pour une utilisation avec de nouvelles instances de plan Starter exécutant Hyperledger Fabric version 1.2.1, installez **composer-cli** à l'aide de la commande suivante :

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Pour une utilisation avec des instances de plan Enterprise et Starter exécutant Hyperledger Fabric version 1.1, installez **composer-cli** à l'aide de la commande suivante :

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Pour une utilisation avec de nouvelles instances de plan Starter exécutant Hyperledger Fabric version 1.2.1, installez **composer-rest-server** à l'aide de la commande suivante :

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Pour une utilisation avec des instances de plan Enterprise et Starter exécutant Hyperledger Fabric version 1.1, installez **composer-rest-server** à l'aide de la commande suivante :

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 Utilisez le module **composer-rest-server** pour créer un serveur REST sur votre machine et exposer votre réseau d'entreprise sous la forme d'API RESTful.

3. Pour une utilisation avec de nouvelles instances de plan Starter exécutant Hyperledger Fabric version 1.2.1, installez **generator-hyperledger-composer** à l'aide de la commande suivante :

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Pour une utilisation avec des instances de plan Enterprise et Starter exécutant Hyperledger Fabric version 1.1, installez **generator-hyperledger-composer** à l'aide de la commande suivante :

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Utilisez **generator-hyperledger-composer** pour générer des structures de réseau d'entreprise, des modèles et une application Angular.

4. Yeoman est un outil qui permet de générer des applications et il utilise `generator-hyperledger-composer`:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Etape 2 : Installer une interface Playground

L'interface Playground d'Hyperledger Composer est une interface utilisateur qui peut être utilisée en tant qu'environnement simulé pour tester un réseau d'entreprise. Installez l'interface Playground à l'aide de la commande suivante :

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## Facultatif : Configurez votre environnement de développement intégré

Vous pouvez utiliser l'interface Playground pour développer, éditer et tester des réseaux d'entreprise. Toutefois, si vous voulez développer un réseau d'entreprise dans un environnement de développement intégré, une extension VSCode est disponible, elle permet d'ajouter une mise en évidence de syntaxe pour le langage de modélisation Hyperledger Composer.

1. Installez VSCode depuis l'URL suivante : [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Ouvrez VSCode, accédez à Extensions, puis recherchez et installez l'extension Hyperledger Composer depuis la place du marché.


### Démarrage et arrêt d'Hyperledger Fabric

Un ensemble de scripts dans le répertoire `fabric-tools` permet de contrôler l'instance Hyperledger Fabric. Vous pouvez ainsi arrêter et redémarrer l'environnement d'exécution à l'aide des scripts `~/fabric-tools/stopFabric.sh` et `~/fabric-tools/startFabric.sh`.

Le script `~/fabric-tools/teardownFabric.sh` implique la création d'une nouvelle carte PeerAdmin au démarrage suivant de l'environnement.

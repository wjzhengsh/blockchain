---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Installation d'un environnement de développement
{: #installing-a-development-environment}

Suivez ces instructions pour obtenir les outils de développement de la plateforme {{site.data.keyword.blockchainfull}} : Develop pour créer et tester des réseaux d'entreprise. Pour une plus grande souplesse et un nombre maximum de scénarios de développement, test et déploiement, les outils de développement sont fournis sous la forme de modules npm, qui peuvent être contrôlés depuis la ligne de commande.

## Avant de commencer

Assurez-vous d'installer les éléments prérequis suivants :

- Ubuntu Linux 14.04 / 16.04 LTS (64 bits) ou Mac OS 10.12
- Node version 8.9 ou suivante (la version 9 n'est pas prise en charge)
- npm version 5.x
- git version 2.9.x ou suivante
- Python version 2.7.x
- Docker Engine version 17.03 ou suivante
- Docker-Compose version 1.8 ou suivante
- Editeur de code de votre choix, par exemple VSCode.

Si vous utilisez VSCode, une extension VSCode Hyperledger Composer est disponible [ici](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).


## Etape 1 : Installer les outils de ligne de commande

Les outils de ligne de commande incluent **composer-cli**, module de ligne de commande principal de la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop. Les modules **generator-hyperledger-composer**, **composer-rest-server** et **Yeoman** sont utilisés pour générer des réseaux d'entreprise, des serveur REST spécifiques et des applications Angular.

1. Installez **composer-cli** à l'aide de la commande suivante :

        npm install -g composer-cli@next

2. Installez **composer-rest-server** à l'aide de la commande suivante :

        npm install -g composer-rest-server@next

    Utilisez le module **composer-rest-server** pour créer un serveur REST sur votre machine et exposer votre réseau d'entreprise sous la forme d'API RESTful.

3. Installez **generator-hyperledger-composer** à l'aide de la commande suivante :

        npm install -g generator-hyperledger-composer@next

    Utilisez **generator-hyperledger-composer** pour générer des structures de réseau d'entreprise, des modèles et une application Angular.

4. Yeoman est un outil qui permet de générer des applications et il utilise `generator-hyperledger-composer`:

        npm install -g yo

## Etape 2 : Installer une interface Playground

L'interface Playground d'Hyperledger Composer est une interface utilisateur qui peut être connectée à une {{site.data.keyword.blockchain}} réelle ou utilisée en tant qu'environnement simulé pour tester un réseau d'entreprise. Installez l'interface Playground à l'aide de la commande suivante :

        npm install -g composer-playground@next

## Facultatif : Configurez votre environnement de développement intégré

Vous pouvez utiliser l'interface Playground pour développer, éditer et tester des réseaux d'entreprise. Toutefois, si vous voulez développer un réseau d'entreprise dans un environnement de développement intégré, une extension VSCode est disponible, elle permet d'ajouter une mise en évidence de syntaxe pour le langage de modélisation Hyperledger Composer.

1. Installez VSCode depuis l'URL suivante : [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Ouvrez VSCode, accédez à Extensions, puis recherchez et installez l'extension Hyperledger Composer depuis la place du marché.

## Etape 3 : Installer une instance locale d'Hyperledger Fabric

Le déploiement d'une instance locale d'Hyperledger Fabric vous permet de tester intégralement des règles de contrôle d'accès et des réseaux d'entreprise.

1. Créez le répertoire `fabric-tools` à l'aide de la commande suivante :

        mkdir ~/fabric-tools && cd ~/fabric-tools

Dans un répertoire de votre choix, par exemple `~/fabric-tools`, recevez le fichier `.tar.gz` qui contient les outils permettant d'installer Hyperledger Fabric :

2. Téléchargez le fichier `tar.gz` qui contient les outils pour l'installation d'Hyperledger Fabric dans un environnement Docker.

        curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz

    Un fichier `.zip` est également disponible. Remplacez le fichier `.tar.gz` par le fichier `fabric-dev-servers.zip` et la commande `tar -xvf` par une commande `unzip` dans le fragment précédent.

3. Utilisez les commandes suivantes pour télécharger un environnement d'exécution Hyperledger Fabric version 1.1 local :

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./downloadFabric.sh

## Etape 4 : Démarrer l'instance Hyperledger Fabric

Le répertoire `fabric-tools` contient un ensemble de scripts qui contrôlent l'instance Hyperledger Fabric téléchargée. Vous devez exécuter la commande `./createPeerAdminCard.sh` pour générer une carte de réseau d'entreprise, laquelle peut ensuite être utilisée pour déployer le réseau d'entreprise dans le Docker qui héberge l'instance Hyperledger Fabric.

1. Exécutez les commandes suivantes pour démarrer l'instance Hyperledger Fabric version 1.1 :

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./startFabric.sh
        ./createPeerAdminCard.sh

## Facultatif : Connectez-vous à l'instance Hyperledger Fabric à l'aide de l'interface Playground

Pour commencer à développer un réseau d'entreprise, utilisez l'interface Hyperledger Composer ou votre environnement de développement intégré.

1. Pour démarrer l'interface Playground, exécutez la commande suivante :

        composer-playground

    L'interface Playground s'ouvre à l'URL suivante : http://localhost:8080/login. La carte **PeerAdmin@hlfv1** créée à l'étape précédente peut être utilisée pour déployer un réseau d'entreprise. 


## Démarrage et arrêt d'Hyperledger Fabric

Un ensemble de scripts dans le répertoire `fabric-tools` permet de contrôler l'instance Hyperledger Fabric. Vous pouvez ainsi arrêter et redémarrer l'environnement d'exécution à l'aide des scripts `~/fabric-tools/stopFabric.sh` et `~/fabric-tools/startFabric.sh`.

Le script `~/fabric-tools/teardownFabric.sh` implique la création d'une nouvelle carte PeerAdmin au démarrage suivant de l'environnement.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Installation d'un environnement de développement
{: #installing-a-development-environment}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Suivez ces instructions pour obtenir les outils de développement d'{{site.data.keyword.blockchainfull}} Platform: Develop pour créer et tester des réseaux d'entreprise. Pour une plus grande souplesse et un nombre maximum de scénarios de développement, test et déploiement, les outils de développement sont fournis sous la forme de modules npm, qui peuvent être contrôlés depuis la ligne de commande.

Différentes de certains composants sont nécessaires pour une utilisation avec le plan Starter et le plan Enterprise. Pour utiliser l'environnement de développement avec le plan Starter, les modules doivent être installés en version `0.19.x 19.`. Pour utiliser l'environnement de développement avec le plan Enterprise, les modules doivent être installés en version `0.16.x`.

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

Les outils de ligne de commande incluent **composer-cli**, module de ligne de commande principal d'{{site.data.keyword.blockchainfull_notm}} Platform: Develop. Les modules **generator-hyperledger-composer**, **composer-rest-server** et **Yeoman** sont utilisés pour générer des réseaux d'entreprise, des serveur REST spécifiques et des applications Angular.

1. Pour une utilisation avec le plan Starter, installez **composer-cli** à l'aide de la commande suivante :

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

    Pour une utilisation avec le plan Enterprise, installez **composer-cli** à l'aide de la commande suivante :

    ```
    npm install -g composer-cli@0.16.x
    ```
    {:codeblock}

2. Pour une utilisation avec le plan Starter, installez **composer-rest-server** à l'aide de la commande suivante :

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

    Pour une utilisation avec le plan Enterprise, installez **composer-rest-server** à l'aide de la commande suivante :

    ```
    npm install -g composer-rest-server@0.16.x
    ```
    {:codeblock}

    Utilisez le module **composer-rest-server** pour créer un serveur REST sur votre machine et exposer votre réseau d'entreprise sous la forme d'API RESTful.

3. Pour une utilisation avec le plan Starter, installez **generator-hyperledger-composer** à l'aide de la commande suivante :

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Pour une utilisation avec le plan Enterprise, installez **generator-hyperledger-composer** à l'aide de la commande suivante :

    ```
    npm install -g generator-hyperledger-composer@0.16.x
    ```
    {:codeblock}

    Utilisez **generator-hyperledger-composer** pour générer des structures de réseau d'entreprise, des modèles et une application Angular.

4. Yeoman est un outil qui permet de générer des applications et il utilise `generator-hyperledger-composer`:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Etape 2 : Installer une interface Playground

L'interface Playground d'Hyperledger Composer est une interface utilisateur qui peut être connectée à un {{site.data.keyword.blockchain}} réel ou utilisé en tant qu'environnement simulé pour tester un réseau d'entreprise. Pour une utilisation avec le plan Starter, installez l'interface Playground à l'aide de la commande suivante :

```
npm install -g composer-playground@0.19.x
```
{:codeblock}


Pour une utilisation avec le plan Enterprise, installez l'aire de jeu à l'aide de la commande suivante :


```
npm install -g composer-playground@0.16.x
```
{:codeblock}

## Facultatif : Configurez votre environnement de développement intégré

Vous pouvez utiliser l'interface Playground pour développer, éditer et tester des réseaux d'entreprise. Toutefois, si vous voulez développer un réseau d'entreprise dans un environnement de développement intégré, une extension VSCode est disponible, elle permet d'ajouter une mise en évidence de syntaxe pour le langage de modélisation Hyperledger Composer.

1. Installez VSCode depuis l'URL suivante : [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Ouvrez VSCode, accédez à Extensions, puis recherchez et installez l'extension Hyperledger Composer depuis la place du marché.


## Plan Enterprise uniquement : Installez une version locale de Hyperledger Fabric et connectez-vous à celle-ci avec l'interface Playground

Le déploiement d'une instance locale d'Hyperledger Fabric vous permet de tester intégralement des règles de contrôle d'accès et des réseaux d'entreprise. Vérifiez que vous avez démarré Docker avant de suivre cette procédure.

1. Créez le répertoire `fabric-tools` à l'aide de la commande suivante :

   ```
   mkdir ~/fabric-tools && cd ~/fabric-tools
   ```
   {:codeblock}

Dans un répertoire de votre choix, par exemple `~/fabric-tools`, recevez le fichier `.tar.gz` qui contient les outils permettant d'installer Hyperledger Fabric :

2. Téléchargez le fichier `tar.gz` qui contient les outils pour l'installation d'Hyperledger Fabric dans un environnement Docker.

   ```
   curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz
   ```
   {:codeblock}

    Un fichier `.zip` est également disponible. Remplacez le fichier `.tar.gz` par le fichier `fabric-dev-servers.zip` et la commande `tar -xvf` par une commande `unzip` dans le fragment précédent.

3. Utilisez les commandes suivantes pour télécharger un environnement d'exécution Hyperledger Fabric v1 local :

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./downloadFabric.sh
   ```
   {:codeblock}

   Le répertoire `fabric-tools` contient un ensemble de scripts qui contrôlent l'instance Hyperledger Fabric téléchargée. Vous devez exécuter la commande `./createPeerAdminCard.sh` pour générer une carte de réseau d'entreprise, laquelle peut ensuite être utilisée pour déployer le réseau d'entreprise dans le Docker qui héberge l'instance Hyperledger Fabric.

4. Exécutez les commandes suivantes pour lancer l'instance Hyperledger Fabric v1 :

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./startFabric.sh
   ./createPeerAdminCard.sh
   ```
   {:codeblock}

   Pour commencer à développer un réseau d'entreprise, utilisez l'interface Hyperledger Composer ou votre environnement de développement intégré.

5. Pour démarrer l'interface Playground, exécutez la commande suivante :

    ```
    composer-playground
    ```
    {:codeblock}

    L'interface Playground s'ouvre à l'URL suivante : http://localhost:8080/login. La carte **PeerAdmin@hlfv1** créée à l'étape précédente peut être utilisée pour déployer un réseau d'entreprise.


### Démarrage et arrêt d'Hyperledger Fabric

Un ensemble de scripts dans le répertoire `fabric-tools` permet de contrôler l'instance Hyperledger Fabric. Vous pouvez ainsi arrêter et redémarrer l'environnement d'exécution à l'aide des scripts `~/fabric-tools/stopFabric.sh` et `~/fabric-tools/startFabric.sh`.

Le script `~/fabric-tools/teardownFabric.sh` implique la création d'une nouvelle carte PeerAdmin au démarrage suivant de l'environnement.

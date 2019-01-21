---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Outils pour smart contracts
{: #overview}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


L'extension Visual Studio Code de {{site.data.keyword.blockchainfull}} Platform fournit un environnement au sein de Visual Studio Code pour le développement, le packaging et le déploiement de packages smart contract. Cette extension inclut également des commandes pour définir une instance locale préconfigurée d'Hyperledger Fabric pour le développement de smart contract local.

**Remarque :** L'extension {{site.data.keyword.blockchainfull_notm}} Platform est compatible avec les réseaux de plan Starter {{site.data.keyword.blockchainfull_notm}} Platform, et avec Hyperledger Fabric 1.3.x.

## Prérequis

Avant d'installer l'extension Visual Studio Code de {{site.data.keyword.blockchainfull_notm}} Platform, effectuez les étapes prérequises suivantes.

- **Installez Yeoman**

  Yeoman est un outil générateur utilisé pour créer le squelette de projets smart contract. Installez Yeoman à l'aide de la commande suivante : `npm install -g yo`

- **Installez le générateur Fabric pour Yeoman**

  Après avoir installé Yeoman, installez le générateur Fabric pour la création du squelette de packages smart contract. Installez le générateur Fabric à l'aide de la commande suivante : `npm install -g generator-fabric`

- **Installez Docker**

  Pour exécuter l'instance préconfigurée d'Hyperledger Fabric, vérifiez que vous avez installé [Docker](https://www.docker.com/).

- **Système d'exploitation requis**

  Actuellement, cette extension est compatible avec Mac, Windows et Linux.

- **Versions d'Hyperledger Fabric requises**

  Cette extension est compatible avec les versions 1.3.0 et suivantes d'Hyperledger Fabric.

- **Versions d'Hyperledger Fabric requises**

  Cette extension est compatible avec les versions 1.3.0 et suivantes d'Hyperledger Fabric.


## Installation de l'extension

1. Accédez à la [page de place du marché de l'extension de code Visual Studio](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) ou recherchez **{{site.data.keyword.blockchainfull_notm}} Platform** dans l'écran des extensions au sein de Visual Studio Code.
2. Cliquez sur **Installer**.
3. Redémarrez Visual Studio Code pour terminer l'installation de l'extension.

## Palette de commandes de Visual Studio Code

Cette extension ajoute de nombreuses commandes à la palette de commandes Visual Studio Code. Nombre d'opérations détaillées dans ce document peuvent également être démarrées en exécutant la commande à partir de la palette de commandes Visual Studio Code.

## Création d'un projet

Pour créer un projet, procédez comme suit : 

1. Ouvrez la palette de commandes, exécutez la commande **Blockchain: Create Smart Contract Project**.
2. Sélectionnez la langue dans laquelle vous voulez créer un smart contract, actuellement les options possibles sont le JavaScript ou le TypeScript.
3. Créez un dossier portant le nom de votre projet, et ouvrez-le.
4. Sélectionnez comment ouvrir votre nouveau projet. Le dossier de projet doit s'ouvrir.

## Connexion et déconnexion d'un réseau

Lorsque vous utilisez cette extension, vous installez et instanciez les packages de smart contract sur des homologues et des canaux dans votre instance Hyperledger Fabric. Cette extension peut initialiser une instance locale préconfigurés d'Hyperledger Fabric avec Docker.

### Connexion à l'instance Hyperledger Fabric préconfigurée

Pour vous connecter à l'instance Hyperledger Fabric préconfigurée, vérifiez d'abord que Docker est en cours d'exécution sur votre machine :

1. Ouvrez l'onglet _{{site.data.keyword.blockchainfull_notm}} Platform_ dans Visual Studio Code.
2. Dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_, cliquez sur **local_fabric**. Si Docker est en cours d'exécution, l'instance Hyperledger Fabric locale doit être téléchargée et démarrée. 
3. Une fois l'instance Hyperledger Fabric locale démarrée, double-cliquez sur **local_fabric** pour vous y connecter. Vous devriez maintenant voir un canal nommé `mychannel`.

#### Redémarrage de l'exécution Hyperledger Fabric préconfigurée

Pour redémarrer l'exécution `local_fabric` :

1. Cliquez avec le bouton droit sur la connexion `local_fabric` lorsqu'elle est établie.
2. Sélectionnez **Redémarrer l'exécution Fabric**.

Les conteneurs Hyperledger Fabric seront arrêtés et redémarrés.

#### Désassemblage de l'exécution Hyperledger Fabric 

Pour désassembler l'exécution `local_fabric` :

1. Cliquez avec le bouton droit sur la connexion `local_fabric` lorsqu'elle est établie.
2. Sélectionnez **Désassembler l'exécution Fabric**.

L'exécution d'un désassemblage du réseau `local_fabric` fermera tous les conteneurs Hyperledger Fabric. **Remarque **: Cela entraînera la perte du registre et des données "world state".

#### Activation du mode développement dans l'exécution Hyperledger Fabric préconfigurée

En mode de fonctionnement normal, un homologue doit créer et gérer un conteneur de code blockchain pour exécuter des smart contracts instanciés. En passant au mode développement, l'homologue permet au conteneur de code blockchain de s'exécuter manuellement. L'exécution du conteneur de code blockchain manuellement, sur la ligne de commande ou sur un terminal, vous aide dans le développement et le débogage itératifs des smart contracts.

Pour activer le mode développement avec l'exécution Hyperledger Fabric préconfigurée : 

1. Cliquez avec le bouton droit sur la connexion `local_fabric` lorsqu'elle est établie.
2. Sélectionnez **Basculer en mode développement**.

Le mode développement doit être activé pour permettre l'utilisation de la fonction de débogage de l'extension.

### Connexion à {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan

L'extension Visual Studio Code de {{site.data.keyword.blockchainfull_notm}} Platform peut être utilisée pour la connexion à une instance de {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan. Une fois la connexion effectuée, l'extension peut être utilisée pour développer et déployer des smart contracts.

**Remarque :** Actuellement, l'extension n'est pas compatible avec {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

1. Si vous ne disposez pas d'une instance de {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan, créez-en une [ici](https://console.bluemix.net/catalog/services/blockchain).
2. Ouvrez l'interface utilisateur IBP en cliquant sur **Lancer**.
3. Procédez à l'extraction de votre profil de connexion en cliquant sur **Présentation** > **Profil de connexion** > **Télécharger**.
4. Générez les certificats d'admin requis en cliquant sur **Autorité de certification** > **Génération de certificat** et en sauvegardant le certificat et la clé privée sur votre système de fichiers.
5. Ajoutez les certificats à votre instance {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan en cliquant sur **Membres** > **Certificats** > **Ajouter un certificat**, et en sélectionnant le certificat créé à l'étape précédente.
6. Dans Visual Studio Code, ouvrez la vue de l'extension {{site.data.keyword.blockchainfull_notm}} Platform et cliquez sur **Ajouter nouvelle connexion**.
7. Entrez le nom de la connexion, le chemin d'accès au profil de connexion, le certificat et la clé privée.

### Connexion à votre propre instance d'Hyperledger Fabric

En utilisant cette extension, vous pouvez vous connecter à une instance locale préconfigurée d'Hyperledger Fabric, ou vous pouvez vous connecter à votre propre instance Hyperledger Fabric. Si vous choisissez de vous connecter à votre propre instance de Fabric, il doit s'agir des versions 1.3.0 ou suivantes de Fabric.

Pour vous connecter à votre propre instance d'Hyperledger Fabric, procédez comme suit :

1. Ouvrez l'onglet _{{site.data.keyword.blockchainfull_notm}} Platform_ dans Visual Studio Code.
2. Dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_, cliquez sur **Ajouter nouvelle connexion**. 
3. Entrez un nom pour la connexion. Ce nom sera affiché dans le panneau  _{{site.data.keyword.blockchainfull_notm}} Platform_.
4. Entrez le chemin complet de votre profil de connexion Hyperledger Fabric.
5. Entrez le chemin complet du certificat pour votre identité.
6. Entrez le nom complet du chemin d'accès de la clé privée à utiliser avec le certificat fourni dans l'étape précédente.
7. Votre connexion ne doit pas apparaître dans la liste des connexions sous `local_fabric`. Double-cliquez sur le nom de la connexion pour vous connecter.

Si vous ne disposez pas d'une instance préexistante d'Hyperledger Fabric en version 1.3.0 ou suivante, vous pouvez vous connecter à l'aide de l'instance Hyperledger Fabric préconfigurée, ou exécuter les commandes suivantes pour extraire et marquer les images de la version 1.3.0 :

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### Déconnexion d'un réseau

Une fois connecté à un réseau, vous pouvez fermer la connexion en cliquant sur l'icône de déconnexion dans l'angle supérieur droit du panneau Connexions blockchain. Vous pouvez également vous déconnecter en exécutant la commande **Disconnect from a blockchain** depuis la palette de commandes.

## Edition ou suppression d'une connexion

Les connexions peuvent être éditées ou supprimées dans le volet des connexions.

### Edition d'une connexion

En éditant une connexion, vous pouvez modifier le chemin du profil de connexion, le nom donné à la connexion, ainsi que les chemins d'accès aux certificats d'identité et aux clés privées.

Pour éditer une connexion :

1. À partir de l'extension, cliquez avec le bouton droit sur la connexion à éditer dans l'angle inférieur gauche. Un menu contextuel s'ouvre avec les options pour ajouter une identité, éditer la connexion ou supprimer la connexion.
2. Sélectionnez **Editer une connexion**.
3. La page _Paramètres utilisateur_ s'affiche, avec les détails de connexion mis en évidence.
4. Apportez des modifications, puis sauvegardez la page des paramètres.

### Suppression d'une connexion

Les connexions peuvent être supprimées comme suit :

1. À partir de l'extension, cliquez avec le bouton droit sur la connexion à éditer dans l'angle inférieur gauche. Un menu contextuel s'ouvre avec les options pour ajouter une identité, éditer la connexion ou supprimer la connexion.
2. Sélectionnez **Supprimer une connexion**.
3. Une boîte de dialogue s'affiche pour confirmer la suppression de la connexion. Cliquez sur
**Oui**.

La connexion sera supprimée.

## Ajout d'identités

Lorsque vous ajoutez une connexion, vous devez soumettre un profil de connexion, un certificat et une clé privée. L'identité qui est associée à la paire de clés certificat/clé privée sera définie comme l'identité par défaut à utiliser lors de la connexion à l'instance Hyperledger Fabric.

Pour ajouter une identité à une connexion déjà établie :

1. À partir de l'extension, cliquez avec le bouton droit sur la connexion à éditer dans l'angle inférieur gauche. Un menu contextuel s'ouvre avec les options pour ajouter une identité, éditer la connexion ou supprimer la connexion.
2. Sélectionnez **Ajouter une identité**.
3. Fournissez les chemins d'accès aux certificats d'identité et à la clé privée.

## Exploration des connexions

Une fois connecté à une instance Hyperledger Fabric, vous pouvez voir une liste des canaux et des homologues disponibles dans ces canaux dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_. Prenez une  minute pour explorer votre instance Hyperledger Fabric afin de vous familiariser avec la structure. Les canaux sont répertoriés en premier au sein d'une connexion. Sous chaque canal, figure une liste des homologues qui sont membres de ce canal, ainsi qu'une liste des packages de smart contract instanciés sur ce canal. Sous chaque homologue de la liste figure est une liste de tous les packages de smart contract installés sur cet homologue. Vous pouvez également voir les versions de smart contract qui sont installés ou instanciés.

### Exportation des détails de connexion pour l'environnement d'exécution Hyperledger Fabric préconfiguré

Les détails de connexion requis pour la connexion à `local_fabric` peuvent être exportés. Les détails de la connexion `local_fabric` sont utiles pour tester les applications client destinées à la connexion ou à une interaction avec votre instance Hyperledger Fabric.

Pour exporter les détails de connexion `local_fabric` :

1. Démarrez l'environnement d'exécution Hyperledger Fabric préconfiguré.
2. Cliquez avec le bouton droit sur la connexion `local_fabric`, puis sélectionnez **Exporter les détails de connexion**.

Les détails de connexion sont sauvegardés dans un répertoire nommé `local_fabric` qui se trouve dans votre répertoire de projet en cours.

## Création d'un package de smart contract

Lorsqu'un smart contract est prêt à être déployé, il doit d'abord être packagé. Pour créer un package, procédez comme suit : 

1. Dans Visual Studio Code, accédez au panneau _{{site.data.keyword.blockchainfull_notm}} Platform_.
2. Dans le panneau _Packages smart contract _, cliquez sur l'icône +. Si un projet de smart contract est ouvert dans l'afficheur de fichiers, il sera automatiquement packagé, et il s'affichera dans le panneau _Packages smart contract_. Si vous avez plusieurs dossiers smart contract ouverts, vous serez invité à préciser un package. Si vous n'avez pas de dossiers de smart contract ouverts, vous recevrez un message d'erreur.

## Installation de packages de smart contract

Une fois connecté à une instance d'Hyperledger Fabric, vous pouvez installer et instancier des package de smart contract sur les homologues.

1. Dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_, accédez à l'homologue sur lequel vous voulez installer le package de smart contract.
2. Cliquez avec le bouton droit sur l'homologue sur lequel installer le package de smart contract, et sélectionnez  **Installer le smart contract**.

## Instanciation de packages de smart contract

Pour commencer l'exécution sur un canal, il est nécessaire d'instancier d'abord un package de smart contract installé. 

1. Connectez-vous à l'instance Hyperledger Fabric où est installé le package de smart contract.
2. Cliquez avec le bouton droit sur le canal sur lequel vous souhaitez instancier le package de smart contract et cliquez sur **Instancier le smart contract**.
3. Sélectionnez le package et la vezsion de smart contract à instancier. Le package de smart contract doit être installé sur un homologue qui est membre de ce canal.
4. Entrez le nom de la fonction d'instanciation dans votre smart contract.
5. Entrez les arguments requis par votre fonction d'instanciation.

## Exportation ou suppression d'un package de smart contract

Une fois qu'un package de smart contract a été créé, il peut être exporté en tant que fichier `.cds` ou supprimé s'il n'est plus nécessaire.

Pour supprimer un package de smart contract :

1. Dans le panneau d'extension {{site.data.keyword.blockchainfull_notm}} Platform, cliquez avec le bouton droit de la souris sur le package de smart contract à supprimer.
2. Sélectionnez **Supprimer le package**.

Le package va être supprimé et il disparaît de la liste des packages de smart contract.

Pour exporter un package de smart contract :

1. Dans le panneau d'extension {{site.data.keyword.blockchainfull_notm}} Platform, cliquez avec le bouton droit de la souris sur le package de smart contract à exporter.
2. Sélectionnez **Exporter le package**.
3. Choisissez le répertoire pour la sauvegarde de votre fichier de package de smart contract et cliquez sur **Exportater**.

## Tests et instanciation d'un package de smart contract

Les tests d'un smart contract peuvent être générés une fois le smart contract instancié. Ils peuvent être générés en JavaScript ou en TypeScript, et exécutés ou débogués.

Pour générer des tests de smart contract :

1. Assurez-vous que le smart contract a été instancié.
2. Sous **Smart contracts instanciés**, cliquez avec le bouton droit de la souris sur le smart contract pour lequel générer des tests.
3. Sélectionnez **Générer des tests de smart contract**.
4. Sélectionnez maintenant le langage du fichier de test, **JavaScript** ou **TypeScript**. L'extension {{site.data.keyword.blockchainfull_notm}} Platform va maintenant installer les modules npm requis et générer le fichier de test.

Une fois le fichier de test généré, les tests peuvent être exécutés en cliquant sur le bouton **Exécuter les tests** dans le fichier.

## Débogage d'un smart contract à l'aide de l'environnement d'exécution Hyperledger Fabric préconfiguré

Le débogage d'un smart contract vous permet d'exécuter des transactions de smart contract avec des points d'arrêt et une sortie, garantissant ainsi un bon fonctionnement des transactions. Pour déboguer votre smart contract :

1. Vérifiez que vous êtes connecté à la connexion `local_fabric` qui est en mode développement.
2. Ouvrez votre projet de smart contract.
3. Ouvrez la vue de débogage dans Visual Studio Code à l'aide de la barre de navigation de gauche.
4. Sélectionnez la configuration de débogage de smart contract dans la liste déroulante dans l'angle supérieur gauche.
5. Préparez et installez le smart contract en cliquant sur le bouton **play**.
6. Ajoutez des points d'arrêt au smart contract en cliquant sur les numéros de ligne appropriés dans fichiers de smart contract.
7. Cliquez avec le bouton droit de la souris sur le smart contract et sélectionnez **Instancier**. Vous pouvez maintenant cliquer avec le bouton droit pour soumettre des transactions et l'exécution sera interrompue sur les points d'arrêt définis.

Pour apporter des modifications à votre smart contract lors du débogage, cliquez sur le bouton **Redémarrer** une fois les modifications effectuées. Redémarrer le débogage signifie que vous n'avez pas besoin d'instancier à nouveau le smart contract.

**Remarque **: Le redémarrage du débogage stocke le smart contract en mémoire locale, pour un grand nombre de modifications apportées à des smart contracts de grande taille, et vous devrez peut-être réinstancier le smart contract.

## Mise à niveau d'un smart contract instancié

Une fois qu'un smart contract a été installé sur un homologue et instancié sur un canal, il peut être mis à niveau pour déployer une version plus récente du smart contract.

1. Vérifiez que le smart contract à mettre à jour est instancié.
2. Installez la nouvelle version du smart contract sur un homologue du même réseau.
3. Cliquez avec le bouton droit de la souris sur le smart contract instancié, et sélectionnez **Mettre à niveau le smart contrat**.
4. Vous pouvez aussi choisir une transaction à exécuter car le nouveau smart contract est instancié.

## Soumission de transactions

Dès lors qu'un smart contract est installé et instancié, les transactions peuvent être soumises depuis le panneau de connexions dans le panneau d'extension {{site.data.keyword.blockchainfull_notm}} Platform.

Pour soumettre une transaction :

1. Vérifiez que votre smart contract est installé et instancié, et que vous êtes connecté au réseau.
2. Dans le volet des connexions, développez le **smart contracts instanciés**.
3. Développez le smart contract qui contient la transaction à soumettre.
4. Cliquez avec le bouton droit de la souris sur la transaction à soumettre, puis sélectionnez **Soumettre la transaction**.
5. Entrez les arguments obligatoires pour la transaction, puis appuyez sur **Entrée**.

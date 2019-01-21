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

# Exploitation des homologues dans {{site.data.keyword.cloud_notm}} Private avec un réseau multi-cloud
{: #peer-operate_icp}

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Après que vous avez configuré une plateforme {{site.data.keyword.blockchainfull}} Platform sur un homologue {{site.data.keyword.cloud_notm}} Private (ICP), vous devez effectuer des étapes supplémentaires pour que votre homologue puisse émettre des transactions afin d'interroger et d'appeler le registre du réseau de blockchain. Ces étapes incluent l'ajout de votre organisation à un canal, l'association de votre homologue au canal, l'installation de code blockchain sur votre homologue, l'instanciation de code blockchain sur le canal, ainsi que la connexion d'applications à votre homologue.
Si vous voulez connecter votre homologue à un réseau de plan Starter ou Enterprise, voir [Exploitation des homologues sur {{site.data.keyword.cloud_notm}} Private avec un réseau de plan Starter ou Enterprise](peer_operate_ibp.html#peer-operate_icp)
{:shortdesc}

Vous devez effectuer quelques étapes préalables depuis votre cluster ICP pour exploiter votre homologue.

**Etapes préalables :**
- [Configurer vos interfaces de ligne de commande](#peer-kubectl-configure)
- [Extraire des informations de noeud final de votre homologue](#peer-endpoint)
- [Télécharger votre certificat TLS homologue](#peer-tls)

Vous pouvez ensuite utiliser l'une des méthodes suivantes pour exploiter votre homologue.

**Opérations :**
- [Utilisation de logiciels SDK Fabric](#peer-operate-with-sdk)
- [Utilisation de la ligne de commande](#peer-cli-operate)

Les logiciels SDK Fabric sont recommandés. Cependant, les instructions supposent que vous avez une bonne connaissance du fonctionnement du logiciel SDK. Si vous préférez utiliser la ligne de commande, vous pouvez utiliser des binaires homologues Fabric.

Si votre organisation n'est pas encore membre d'un consortium ou d'un canal, vous pouvez utiliser ces étapes pour [créer un canal](#create-channel). Les instructions vous guident tout au long de la [préparation d'une définition d'organisation](#organization-definition). Cette définition sera utilisée pour faire de vous un membre du consortium par l'ajout à un canal système de service de tri. Par la suite, vous pourrez former un nouveau canal par la [création d'une transaction de canal](#peer-icp-channeltx)
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

## Prérequis

Quel que soit votre choix, utiliser les logiciels SDK ou la ligne de commande, vous devez effectuer les étapes de cette section lors de l'exploitation de votre homologue. Vous pouvez effectuer toutes ces étapes avant de commencer.

### Configuration des interfaces de ligne de commande
{: #peer-kubectl-configure}

Vous devez utiliser l'outil de ligne de commande **kubectl** pour vous connecter au conteneur homologue qui s'exécute dans ICP. 

1. Connectez-vous à l'interface utilisateur de votre cluster ICP. Accédez à l'onglet **Outils de ligne de commande** et cliquez sur **Interface de ligne de commande Cloud Private**. Vous verrez les outils suivants que vous pouvez télécharger.

   * Installer l'interface CLI et les plug-ins IBM Cloud Private
   * Installer l'interface CLI Kubectl
   * Installer Helm
   * Installer l'interface CLI Istio

  Pour exploiter un homologue, vous devez utiliser les **trois** premiers outils, Helm étant facultatif. Cliquez sur chacun d'eux et exécutez les commandes `curl` pour le type de machine que vous utilisez. Ensuite, exécutez les commandes `chmod` et `sudo mv` pour chaque outil. La commande `chmod` modifie les droits de l'interface de ligne de commande en question afin de la rendre exécutable, et la commande `sudo mv` déplace le fichier et le renomme.

  Les commandes du premier outil **cloudctl** peuvent ressembler à l'exemple suivant :

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  Les commandes du deuxième outil **kubectl** peuvent ressembler à l'exemple suivant :

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Après avoir configuré **kubectl**, exécutez la commande suivante :

  ```
  kubectl get pods
  ```
  {:codeblock}

  Si la commande aboutit, elle renvoie une réponse qui est similaire à l'exemple suivant, où `peer-6cf85f6687-hcxpl` est le nom du conteneur homologue.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Vous êtes maintenant prêt à utiliser l'outil **kubectl** pour obtenir les informations de noeud final de l'homologue.

3. Si vous le souhaitez, vous pouvez utiliser **Helm** pour effectuer quelques étapes supplémentaires. Notez que Helm est un outil dont l'installation est facultative et il n'est pas nécessaire de l'utiliser dans ces instructions. Il peut toutefois être utile pour gérer vos éditions Helm et créer vos propres archives à déployer dans ICP.

  1. Cliquez sur "Installer Helm" et exécutez la commande `curl` depuis l'interface utilisateur ICP.
  2. Décompressez le fichier `tar` en exécutant la commande suivante :

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. Exécutez la commande `sudo mv` en ajoutant `/helm` à  `darwin-amd64`. Notez qu'il n'est pas nécessaire d'exécuter la commande `chmod` pour Helm. Par exemple :

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Vous pouvez exécuter la commande `helm help` pour confirmer que Helm est bien installé.

### Extraction des informations de noeud final de votre homologue
{: #peer-endpoint}

Vous devez cibler le noeud final de votre homologue à partir du logiciel SDK ou du client d'autorité de certification Fabric pour rejoindre le canal ou installer des smart contracts. Pour trouver le noeud final de votre homologue, vous pouvez utiliser l'interface utilisateur de votre console ICP. Vous devrez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Rôles et actions de l'administrateur de cluster") pour effectuer les étapes suivantes :

1. Connectez-vous à la console ICP et cliquez sur l'icône **Menu ** dans l'angle supérieur gauche.
2. Cliquez sur **Charge de travail** > **Editions Helm**. 
3. Recherchez le nom de votre édition Helm et ouvrez l'écran des détails de l'édition Helm.
4. Faites défiler jusqu'à la section **Remarques** au bas de la fenêtre. Dans la section  **Remarques**, vous pouvez voir un ensemble de commandes pour vous aider à effectuer le déploiement de votre homologue.
5. Suivez les étapes permettant de configurer l'[interface de ligne de commande kubeclt](#peer-kubectl-configure) si ce n'est déjà fait. Vous utiliserez kubectl pour interagir avec votre conteneur homologue.
6. Dans votre interface de ligne de commande, exécutez la première commande de la note, qui suit **1. Get the application URL by running these commands:** Cette commande produira l'URL et le port de l'homologue, comme dans l'exemple suivant. Notez cette URL  pour de futures commandes.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Remarque :** Si vous déployez votre homologue derrière un pare-feu, vous devez ouvrir un passe-système afin de permettre au réseau sur la plateforme d'effectuer un établissement de liaison TLS avec votre homologue. Vous devez uniquement activer un passe-système pour le port du noeud lié au port 7051 de vos homologues. Pour plus d'informations, voir la section relative à la [recherche de vos informations de noeud final](peer_operate_ibp.html#peer-endpoint).

### Télécharger votre certificat TLS homologue
{: #peer-tls}

Vous devez télécharger votre certificat TLS homologue et le transmettre à vos commandes afin de pouvoir communiquer avec votre homologue depuis un client distant.

1. Connectez-vous à la console ICP et cliquez sur l'icône **Menu ** dans l'angle supérieur gauche.
2. Cliquez sur **Charge de travail** > **Editions Helm**. 
3. Recherchez le nom de votre édition Helm et ouvrez l'écran des détails de l'édition Helm.
4. Faites défiler jusqu'à la section **Remarques** au bas de la fenêtre. Dans la section  **Remarques**, vous pouvez voir un ensemble de commandes pour vous aider à effectuer le déploiement de votre homologue.
5. Suivez les étapes permettant de configurer l'[interface de ligne de commande kubeclt](#peer-kubectl-configure) si ce n'est déjà fait. Vous utiliserez kubectl pour interagir avec votre conteneur homologue.
6. Dans votre interface de ligne de commande, exécutez la première commande de la note, qui suit **3. Get peer TLS root cert file**. Cette commande permet de sauvegarder votre certificat TLS sous le nom de fichier `cacert.pem` sur votre machine locale.
7. Déplacez le certificat vers un emplacement où vous pouvez le référencer dans de futures commandes, puis renommez-le `peertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Utilisation de SDK Fabric pour l'exploitation de votre homologue
{: #peer-operate-with-sdk}

Les logiciels SDK Hyperledger Fabric fournissent un puissant jeu d'API qui permettent aux applications d'interagir et d'exploiter les réseaux de blockchain. Vous pouvez obtenir la liste la plus récente des langages pris en charge et la liste complète des API disponibles au sein des logiciels SDK Fabric dans la [documentation Hyperledger Fabric SDK community![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentation Hyperledger Fabric SDK Community"). Vous pouvez utiliser les logiciels SDK Fabric pour associer votre homologue à un canal sur {{site.data.keyword.blockchainfull_notm}} Platform, installer un code blockchain sur votre homologue, et instancier le code blockchain sur un canal.

Les instructions suivantes utilisent le [Logiciel SDK Fabric Node![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Fabric Node") pour exploiter l'homologue et supposent une connaissance préalable du logiciel SDK. Vous pouvez utiliser le [tutoriel de développement d'applications](../v10_application.html) pour en savoir plus sur l'utilisation du logiciel SDK Node avant de commencer, et comme guide pour le développement d'applications avec votre homologue lorsque vous êtes prêt à appeler et à interroger le code blockchain.

### Installation du logiciel SDK Node

Vous pouvez utiliser NPM pour installer le [Logiciel SDK Node ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Node") :

```
npm install fabric-client@1.2
```
{:codeblock}

Il est recommandé d'utiliser la version 1.2 du logiciel SDK Node.

### Préparation du logiciel SDK pour l'utilisation de l'homologue
{: #peer-node-sdk}

Votre homologue a été déployé avec le certificat signCert de votre admin homologue interne. Cela vous permettra d'utiliser des certificats d'admin homologues et un dossier MSP pour exploiter l'homologue.

Recherchez les certificats que vous avez créés lorsque vous avez [enregistré votre admin homologue](CA_operate.html#enroll-admin). Si vous avez utilisé les commandes en  exemple, vous pouvez trouver votre dossier MSP admin homologue dans `$HOME/fabric-ca-client/peer-admin`.
  - Vous pouvez générer le contexte utilisateur admin homologue avec le logiciel SDK qui utilise le certificat signCert (clé publique) et la clé privée dans le dossier MSP. Vous pouvez trouver ces clés dans les emplacements suivants :
    - Le certificat signCert peut se trouver dans le dossier **signcerts** : `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - La clé privée peut se  trouver dans le dossier **keystore:** : `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    Pour obtenir un exemple de formation d'un contexte d'utilisation et d'exploitation du logiciel SDK à l'aide seulement de la clé publique et la clé privée, voir [Génération de certificats à l'aide du Moniteur réseau](../v10_application.html#enroll-panel).

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](CA_operate.html#register-admin). -->

### Transmission du certificat TLS de votre homologue au logiciel SDK
{: #icp-peer-download-tlscert}

Vous devez faire référence au certificat TLS de votre homologue pour authentifier les communications de votre logiciel SDK. Recherchez le certificat TLS que vous avez [téléchargé depuis votre conteneur homologue](#peer-tls) et enregistrez-le à un emplacement où il peut être référencé par votre application. Si vous avez utilisé les commandes en exemple, vous pouvez trouver le certificat TLS de votre homologue dans `$accueil/matrice ca client/poste de tls/peertls.pem`. Vous pouvez ensuite importer le certificat TLS dans votre application à l'aide d'une simple commande de lecture de fichier.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Indication des informations de noeud final de l'homologue au logiciel SDK
{: #peer-SDK-endpoints}

Recherchez les [informations de noeud final de votre homologue ](#peer-endpoint) et indiquez-les au logiciel SDK en déclarant une nouvelle variable d'homologue. L'exemple suivant définit l'homologue en tant que noeud final sur votre réseau Fabric et le transmet au certificat TLS que vous avez importé.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

Si vous disposez d'un réseau de grande taille, avec plusieurs homologues appartenant à différentes organisations qui doivent valider vos transactions, vous devrez peut-être collaborer avec votre consortium pour [générer un profil de connexion commun![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-network-config.html "profil de connexion commun")

### Transmission du certificat TLS de votre service de tri au logiciel SDK
{: #icp-orderer-download-tlscert}

Vous avez également besoin du certificat TLS du service de tri de votre consortium pour rejoindre des canaux et soumettre des transactions. Si vous êtes l'administrateur du service de tri, suivez les instructions relatives au [téléchargement du certificat TLS du service de tri](orderer_operate.html#orderer-tls).  Si vous avez utilisé les commandes en exemple, vous pouvez trouver le certificat TLS de votre homologue dans `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`. Si le service de tri est régi par une autre organisation, elle doit vous fournir le certificat TLS dans une opération hors bande. Vous pouvez ensuite importer le certificat TLS dans votre application.

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### Indication des informations de service de tri au logiciel SDK
{: #orderer-SDK-endpoints}

Pour utiliser le logiciel SDK, vous avez également besoin des informations de noeud final des services de tri dans votre consortium. Si vous êtes l'administrateur du service de tri, vous pouvez utiliser les instructions relatives à l'[extraction des informations de noeud final du service de tri](orderer_operate.html#orderer-endpoint).  Si le service de tri est régi par une autre organisation, elle doit vous fournir l'URL du service de tri dans une opération hors bande. L'exemple suivant définit un service de tri comme noeud final et le transmet au certificat TLS du service de tri.

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### Utilisation du logiciel SDK pour rejoindre un canal
{: #peer-join-channel-sdk}

Votre organisation doit être membre d'un canal pour que vous puissiez rejoindre le canal avec votre homologue. Si vous n'êtes pas membre d'un canal, vous pouvez suivre les instructions relatives à la [création d'un canal](#create-channel).

Dès que votre organisation est devenue membre d'un canal, suivez les instructions permettant de [joindre vos homologues à un canal](../v10_application.html#join-channel-sdk) à l'aide du logiciel SDK.

### Utilisation du logiciel SDK pour installer le code blockchain sur l'homologue
{: #peer-install-cc-sdk}

Utilisez les instructions suivantes pour utiliser le logiciel SDK pour [installer un code blockchain](../v10_application.html#install-cc-sdk) sur votre homologue.

### Utilisation du logiciel SDK pour instancier le code blockchain sur l'homologue
{: #peer-instantiate-cc-sdk}

Un seul membre de ce canal doit instancier ou mettre à jour le code blockchain. Par conséquent, tout membre du canal qui a  installé le code blockchain sur ses homologues peut instancier et spécifier les règles de validation. Toutefois, si vous souhaitez utiliser l'homologue pour instancier du code blockchain sur un canal, vous pouvez utiliser le logiciel SDK et suivre les instructions d'[instanciation d'un code blockchain](../v10_application.html#instantiate-cc-sdk).

## Utilisation de l'interface CLI pour exploiter l'homologue
{: #peer-cli-operate}

Vous pouvez également exploiter votre homologue depuis la ligne de commande à l'aide de binaires d'homologue Fabric. 

Votre homologue a été déployé avec le certificat signCert de votre admin homologue interne, ce qui permet à cette identité d'exploiter l'homologue. Les instructions ci-après utilisent le dossier MSP admin homologue qui a été généré lorsque vous avez [déployé votre homologue](CA_operate.html#register-admin) pour joindre l'homologue à un canal, installer un code blockchain sur l'homologue, puis instancier le code blockchain sur un canal.

### Téléchargement du client homologue Fabric
{: #peer-client}

Pour interagir avec votre homologue depuis un client distant, vous devez télécharger le [client homologue Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "commande homologue Fabric").

Le moyen le plus simple d'obtenir le client homologue est de télécharger les binaires de l'outil Fabric du projet Hyperledger. Créez un répertoire dans lequel vous souhaitez télécharger les binaires à partir de votre ligne de commande, puis procédez à leur extraction en exécutant la commande ci-dessous. Vous devez d'abord installer [Curl ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl").

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

La commande installera les binaires dans un répertoire `bin/`. Définissez le chemin d'accès à vos outils, où les outils Fabric ci-dessus sont téléchargés :
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

Par exemple, si vous avez installé les binaires dans votre répertoire de base, vous définiriez votre `PATH` comme suit :

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

Le téléchargement des binaires va créer un dossier config contenant un fichier core.yaml, orderer.yaml et configtx.yaml. Définissez le chemin de configuration Fabric sur ce chemin de configuration :

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

Par exemple :
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}

### Gestion des certificats sur votre système local
{: #manage-certs}

Accédez au répertoire où le dossier MSP admin homologue est généré. Si vous avez suivi les étapes en exemple décrites dans cette documentation, vous pouvez rechercher le dossier MSP dans un répertoire similaire comme ci-dessous :

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Avant d'exploiter l'homologue, vous devez procéder à quelques opérations de gestion des certificats sur votre machine locale. Vous devez également vous assurer que vous pouvez accéder aux certificats TLS à partir de l'homologue. Pour plus d'informations sur les certificats à utiliser, voir [Fournisseur de services aux membres (MSP)](CA_operate.html#msp) dans [Exploitation d'une autorité de certification sur {{site.data.keyword.cloud_notm}} Private](CA_operate.html).

1. Déplacez le certificat signCert de l'admin de votre homologue vers un nouveau dossier nommé `admincerts` :

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
    mkdir admincerts
    cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. Vérifiez que vous avez [téléchargé le certificat TLS de votre homologue](#peer-tls) et que vous pouvez le référencer à partir de la ligne de commande. Si vous avez suivi les exemples de commandes figurant dans cette documentation, vous pouvez trouver ce certificat dans le fichier `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

3. Vous devez également référencer le certificat TLS de votre service de tri. Si vous êtes l'administrateur du service de tri, suivez les instructions relatives au [téléchargement du certificat TLS du service de tri](orderer_operate.html#orderer-tls).  Si le service de tri est régi par une autre organisation, elle doit vous fournir le certificat TLS dans une opération hors bande. Conservez ce certificat dans un emplacement où vous pouvez y faire référence dans les commandes futures.

Vous pouvez exécuter une commande tree pour vérifier que vous avez effectué ces étapes. Accédez au répertoire dans lequel vous avez stocké vos certificats. Une commande tree doit générer un résultat similaire à la structure suivante :
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Définition des variables d'environnement de l'interface de ligne de commande
{: #environment-variables}

Après avoir déplacé tous nos certificats vers l'emplacement nécessaire, nous devons définir des variables d'environnement qui sont utilisées par les commandes de l'interface CLI de notre homologue. Nous serons ensuite prêts à utiliser la client homologue Fabric pour exploiter notre homologue.

1. Assurez-vous que vous avez défini les [variables d'environnement dans votre interface de ligne de commande](#environment-variables)

2. Définissez le chemin de configuration Fabric sur le chemin de configuration qui a été créé lorsque vous avez [téléchargé les binaires de l'outil Fabric](#peer-client):

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    La définition de cette variable vous permettra d'exécuter les commandes à l'aide du client homologue dans un répertoire.

3. Vous avez besoin des informations de noeud final de votre service de tri. Si vous êtes l'administrateur du service de tri, vous pouvez utiliser les instructions relatives à l'[extraction des informations de noeud final du service de tri](orderer_operate.html#orderer-endpoint).  Si le service de tri est régi par une autre organisation, elle doit vous fournir l'URL du service de tri dans une opération hors bande. 

4. [Procédez à l'extraction des informations de noeud final](#peer-endpoint). Nous allons utiliser cette  URL pour définir la variable d'environnement `PEERADDR`. Vous devez retirer `http://` au début. 

5. Exécutez les commandes suivantes pour définir les variables d'environnement :

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Remplacez les zones par vos informations.
    - Remplacez `<CHANNEL_NAME>` par le nom du canal que rejoint l'homologue.
    - Remplacez `<CC_NAME>` par un nom pour faire référence à votre code blockchain.
    - Remplacez `<PEERADDR>` par le nom d'hôte et le port du noeud final d'homologue de l'étape précédente.
    - Remplacez `<ORDERER_URL>` par le nom d'hôte et le port du service de tri de votre consortium.
    - Remplacez `<PATH_TO_ADMIN_MSP>` par le chemin d'accès au dossier MSP de l'admin de votre homologue.
    - Remplacez `<MSPID_OF_PEER_BEING_USED>` par le MSPID d'organisation de l'homologue que vous utilisez pour extraire un bloc d'origine, rejoindre un canal ou installer un code blockchain. Cette valeur a été fournie lors du déploiement de la charte Helm.

  Par exemple :

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  Vous pouvez aussi si vous le souhaitez activer TLS, en exécutant la commande suivante :

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Utilisation de l'interface CLI pour joindre l'homologue au canal
{: #icp-cli-join-peer-to-channel}

Votre organisation doit être membre d'un canal pour que vous puissiez rejoindre le canal avec votre homologue. Si vous n'êtes pas membre d'un canal, vous pouvez suivre les instructions relatives à la [création d'un canal](#create-channel).

1. Assurez-vous que vous avez défini les [variables d'environnement dans votre interface de ligne de commande](#environment-variables).

2. Extrayez le block d'origine du canal pour générer le registre de canal sur votre homologue. Définissez `CORE_PEER_TLS_ROOTCERT_FILE` sur le chemin du certificat TLS du service de tri et exécutez la commande suivante :

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Si cette opération aboutit, vous verrez un résultat similaire à ceci :
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **Remarque :** Il est possible que lors de l'exécution de l'une de ces commandes de l'interface CLI, vous obteniez l'avertissement suivant qui peut être ignoré.


  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```
  {:codeblock}

  Exécutez la commande suivante pour vérifier que le bloc d'origine est ajouté à votre conteneur homologue avec succès :

  ```
  ls *.block
  ```
  {:codeblock}

  Vous savez que le bloc d'origine est ajouté lorsque vous voyez quelque chose de semblable à l'exemple suivant :

  ```
  defaultchannel_0.block
  ```

3. Une fois que vous avez extrait le bloc d'origine du canal, vous pouvez joindre l'homologue au canal.  Définissez  `CORE_PEER_TLS_ROOTCERT_FILE` sur le chemin du certificat TLS de votre homologue et joignez le canal à l'aide de la commande suivante :

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  Si l'opération aboutit, vous devez voir des informations similaires à l'exemple suivant :

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilisation de l'interface de ligne de commande pour installer le code blockchain sur l'homologue
{: #icp-toolcontainer-install-cc}

Nous sommes maintenant prêts à installer et à instancier le code blockchain sur l'homologue. Dans le cadre des présentes instructions, nous installons le code blockchain `fabcar` du référentiel `fabric-samples`. Assurez-vous de [d'avoir configuré votre GOPATH ![Icône de lien externe](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Définition de votre GOPATH") au préalable, puis téléchargez code blockchain `fabric-samples` depuis github à l'aide des commandes suivantes :

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Exécutez la commande d'interface CLI d'homologue suivante afin d'installer le code blockchain `fabcar` sur l'homologue. Définissez `CORE_PEER_TLS_ROOTCERT_FILE` sur le chemin d'accès au certificat TLS de votre homologue.

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Lorsque cette commande est exécutée correctement, vous devriez voir un quelque chose de similaire à ceci :

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

<!--
**Note:** If this chaincode has already been installed and instantiated on another peer running in IBP Starter or Enterprise Plan, there are additional steps that must be performed before installing the chaincode on the peer. For more instructions about how to avoid errors that are associated with how this chaincode is installed, see the [troubleshooting topic](#icp-cc-install-error).
-->

### Utilisation de l'interface CLI pour instancier un code blockchain sur un canal
{: #icp-toolcontainer-instantiate-cc}

Etant donné qu'un seul homologue doit instancier du code blockchain sur un canal, vous n'avez pas à utiliser votre homologue pour instancier du code blockchain. Les homologues qui sont hébergés sur {{site.data.keyword.blockchainfull_notm}} Platform peuvent faire cela. Toutefois, si vous préférez que l'homologue instancie du code blockchain sur le canal, vous pouvez exécuter la commande suivante ci-après pour cela. Définissez la valeur de `CORE_PEER_TLS_ROOTCERT_FILE` sur le chemin d'accès au certificat TLS de votre homologue. Définissez la valeur `--cafile` sur le certificat TLS du service de tri.

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

Lorsque cette commande est exécutée correctement, vous devriez voir un quelque chose de similaire à ceci :

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

Une fois le code blockchain instancié, vous pouvez utiliser une requête de code blockchain et appeler des commandes pour lire et écrire des données dans le registre de canal. Pour plus d'informations, voir les [code blockchain homologue![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) dans la documentation Hyperledger Fabric. Vous devrez transmettre le noeud fin du service de tri à vos commandes d'appel en utilisant l'IP proxy et le porte de service de tri externe. Il vous suffit de transmettre le noeud final homologue à une commande de requête.

## Consultation des journaux d'homologue dans {{site.data.keyword.cloud_notm}} Private
{: #peer-log-icp}

Vous pouvez accéder aux journaux de l'homologue depuis la console ICP et consulter les journaux dans l'interface Kibana.

1. Sur la console ICP, cliquez sur l'icône **Menu** dans l'angle supérieur gauche.
2. Dans la liste de menus, cliquez sur **Charges de travail** > **Editions Helm**.
3. Dans le tableau Editions Helm, cliquez sur le nom de votre édition Helm.
4. Faites défiler jusqu'à la section **Pod** et cliquez sur le lien **Afficher les journaux** en fin de ligne de tableau. Les journaux de l'homologue s'ouvrent dans l'interface Kibana.

## Mise à jour du code blockchain

Il est probable qu'au fil du temps vous devrez modifier le code blockchain qui s'exécute sur l'homologue. Etant donne que le code blockchain est installé sur les homologues et instancié sur le canal, vous devez mettre à jour le code blockchain sur tous les homologues du canal.

Procédez comme suit pour mettre à jour votre code blockchain :

1. Pour mettre à jour le code blockchain sur chaque homologue, relancez simplement le processus utilisé pour installer le code blockchain sur les homologues, au moyen d'une application client ou d'une commande d'interface de ligne de commande. Veillez à indiquer le même nom de code blockchain que celui utilisé initialement. Toutefois, cette fois incrémentez la `version` de code blockchain. Tous les homologues doivent utiliser le même nom et la même version de code blockchain.

2. Une fois le nouveau code blockchain installé sur tous les homologues du canal, utilisez la commande [peer chaincode upgrade ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) pour mettre à jour le canal qui va utiliser le nouveau code blockchain.

## Redémarrage d'un homologue qui s'exécute dans ICP
{: #peer-restart}

Chaque fois qu'un certificat est ajouté à l'homologue, le noeud homologue doit être redémarré afin qu'il puisse reconnaître le nouveau certificat.

Vous pouvez utiliser les commandes **kubectl** pour redémarrer l'homologue qui s'exécute dans ICP. La commande requiert le nom de **pod** de l'homologue et son **conteneur** en tant que paramètres de commande. Pour plus d'informations sur l'utilisation des commandes kubectl, voir [Configuration de l'interface de ligne de commande kubectl](#peer-kubectl-configure).

1. Sur la console ICP, recherchez le nom de **pod** et la nom de **conteneur** de déploiement de votre homologue.

  1. Cliquez sur l'icône de menu dans l'angle supérieur gauche de la console, puis cliquez sur **Charges de travail > Déploiements**.
  2. Recherchez la version de votre homologue et cliquez dessus dans le tableau afin de l'ouvrir.
  3. Faites défiler vers le bas pour afficher le nom de **pod** associé. Notez le nom de **pod**.
  4. Cliquez sur le pod afin de l'ouvrir.
  5. Cliquez sur l'onglet **Conteneurs**. Notez le nom de **conteneur** de votre homologue.

2. Depuis votre ligne de commande, exécutez la commande suivante pour redémarrer votre homologue, en remplaçant `<PEER_POD_NAME>` et `<PEER_CONTAINER_NAME>` par les valeurs ci-dessus.

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. Vous pouvez vérifier que l'homologue a été redémarré en exécutant la commande `kubectl get pods` et en examinant le résultat du nombre de **RESTART** pour votre pod.

## Création d'un canal
{: #create-channel}

Si votre organisation n'est pas encore membre d'un consortium ou d'un canal, vous pouvez utiliser les étapes ci-après pour créer un canal. Vous pouvez également utiliser ces étapes pour mettre à jour un canal existant. Les instructions vous guident tout au long de la [préparation d'une définition d'organisation](#organization-definition). Cette définition sera utilisée pour faire de vous un [membre du consortium](orderer_operate.html#add-organizations-to-consortium) par l'ajout à un canal système de service de tri. Par la suite, vous pourrez former un nouveau canal par la [création d'une transaction de canal](#peer-icp-channeltx)

Si vous avez déjà rejoint le consortium, vous devez effectuer uniquement l'étape de préparation de la transaction de canal. Un autre membre du consortium peut également former un nouveau canal avec votre organisation en tant que membre. Vous pouvez mettre à jour la définition d'organisation si vous souhaitez envoyer des mises à jour au consortium, comme la définition de nouveaux homologues d'ancrage, l'ajout de nouveaux certificats d'administration, ou la mise à jour votre matériel cryptographique.

### Téléchargement de l'outil configtxgen
{: #peer-icp-configtxgen}

Si votre organisation doit rejoindre un consortium ou un canal, vous devez télécharger l'outil [configtxgen ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen"). 

Le moyen le plus simple d'obtenir l'outil configtxgen est de télécharger tous les binaires de l'outil Fabric du projet Hyperledger. Accédez à un répertoire dans lequel vous souhaitez télécharger les binaires à partir de votre ligne de commande, puis procédez à leur extraction en exécutant la commande ci-dessous. Vous devez d'abord installer [Curl ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl").

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

La commande installe l'outil configtxgen dans un répertoire `bin/`. Définissez le chemin `PATH` sur le répertoire dans lequel vous avez téléchargé les binaires de l'outil Fabric :

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

Par exemple, si vous avez installé les binaires dans votre répertoire de base, vous définiriez votre `PATH` comme suit :

```
export PATH=$PATH:$HOME/bin
```

## Préparation d'une définition d'organisation
{: #organization-definition}

Une fois que vous avez déployé vos composants, votre organisation peut rejoindre un consortium en préparant un fichier de définition de l'organisation. Cette définition contient toutes les informations dont vous avez besoin pour participer à la gouvernance du consortium, comme créer ou joindre de nouveaux canaux, ajouter vos homologues à des canaux, ou instancier du code blockchain. La définition contient le nom de votre organisation, les certificats MSPID et MSP. Toutes les organisations doivent effectuer cette étape.

### Préparer votre matériel cryptographique

Avant de préparer une définition d'organisation, vous devez enregistrer et inscrire  l'[administrateur de vos homologues](CA_operate.html#register-admin). Accédez au répertoire dans lequel vous avez créé le dossier MSP de votre admin homologue. Les étapes en exemple ont créé ce dossier dans  `$HOME/fabric-ca-client/peer-admin/msp`. Vous devez effectuer certaines étapes supplémentaires dans ce dossier pour que ce dossier puisse être utilisé par l'outil `configtxgen`.

1. Copiez votre certificat TLS depuis votre autorité de certification et placez-le dans un nouveau dossier nommé `tlscacerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  Ensuite, vous devrez copier le certificat dans le répertoire `tlscacerts` que vous avez créé :

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  Votre commande peut ressembler à l'exemple suivant :

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. Déplacez votre certificat signCert du dossier `signcerts` dans un nouveau dossier nommé `admincerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

L'outil `configtxgen` crée la définition en consommant un fichier configtx.yaml. Vous pouvez trouver un exemple de version de ce fichier ci-dessous :

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

Ce fichier contient des informations qui définissent votre organisation au sein du consortium. Une version plus complexe de ce fichier est également disponible dans le dossier `/config` du [client d'homologue Fabric que vous avez téléchargé](peer_operate_icp.html#peer-client). Vous pouvez choisir de modifier ce fichier, ou de le remplacer par l'exemple ci-dessus. Notez l'emplacement de ce dossier `/config` afin de définir la valeur de `FABRIC_CFG_PATH` ci-dessous. Editez la section `Organizations` de ce fichier et définissez les valeurs suivantes :

- `Nom :` peut être le nom que vous souhaitez utiliser pour votre organisation.

- `ID :` valeur de votre MSPID, qui devient l'identificateur unique de votre organisation. La valeur MSP de cette organisation a été spécifiée lors du déploiement de la charte Helm de votre homologue. Elle est également visible dans le conteneur d'homologue en exécutant les commandes suivantes, dans lesquelles `<peer pod name>` est remplacé par la valeur du pod de votre homologue :

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  La sortie devrait être similaire à ceci :
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  Par conséquent, l'`ID` MSP est org1.

- `MSPDir :` chemin d'accès complet à votre MSP admin, par exemple : `/Users/chandra/fabric-ca-client/peer-admin/msp`

- `AnchorPeers :` zone qui affiche l'homologue sélectionné par votre organisation comme homologue principal pour une communication entre organisations. Elle est utilisé pour des fonctions telles que les données privées ou la reconnaissance de service ; toutefois, les données privées et la reconnaissance de service ne sont pas actuellement pris en charge par la charte Helm de l'homologue, même si cette zone est toujours obligatoire. Les valeurs  `Host` et `Port` sont disponibles dans la page relative à l'édition Helm de l'homologue ICP sous la section Remarques `1. Pour obtenir l'URL de l'application, exécutez les commandes ci-après`. La sortie devrait être similaire à ceci :

```
http://9.30.250.70:30481
```

où `Host :` = `9.30.250.70` et `Port:` = `30481`

Utilisez ensuite la commande suivante pour imprimer la définition d'organisation :

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

Votre appel peut ressembler à ce qui suit dans l'exemple de commande :

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

Si cette commande aboutit, l'outil `configtxgen` imprime la définition d'organisation au format JSON. Vous devez envoyer ce fichier à l'organisation du service de tri dans une opération hors bande pour rejoindre le consortium. L'organisation du service de tri peut ensuite [former un consortium ou être ajoutée à un consortium existant](orderer_operate.html#consortium) par l'ajout de la  définition au canal système, ce qui vous permet de créer de nouveaux canaux et d'être ajouté aux canaux par d'autres membres du consortium.

## Création de la transaction de canal
{: #peer-icp-channeltx}

Pour que vous puissiez créer un nouveau canal, votre organisation doit avoir préparé une [définition d'organisation](#organization-definition) et être devenue membre du consortium. Suivez les instructions ci-après si vous devez [former un consortium ou être ajouté à l'un d'eux](orderer_operate.html#consortium). Les membres du consortium peuvent également être facilement ajoutés à de nouveaux canaux, si leur organisation a déjà été ajoutée au canal  système. Les organisations qui ne sont pas membres du canal système peuvent uniquement rejoindre un canal manuellement, par l'ajout de leur définition d'organisation au canal à l'aide d'une [demande de mise à jour de canal![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html). Vous pouvez également utiliser ces étapes pour mettre à jour un canal existant. 

### Formation d'un nouveau canal
{: #peer-icp-create-channel}

Pour former un nouveau canal, une organisation doit créer une proposition de transaction de création de canal à l'aide de l'outil [outil configtxgen![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen"). Cet outil utile un fichier a `configtx.yaml` qui définit les membres du nouveau canal. Un exemple de fichier `configtx.yaml` est fourni ci-après. Une version plus complexe de ce fichier est également disponible dans le dossier `/config` du [client d'homologue Fabric que vous avez téléchargé](peer_operate_icp.html#peer-client). Vous pouvez choisir de modifier ce fichier, ou de le remplacer par l'exemple. Notez l'emplacement de ce dossier `/config` afin de définir la valeur de `FABRIC_CFG_PATH` ci-dessous. 
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

Les trois sections qui sont pertinentes pour la création d'un nouveau canal sont :creating a new channel are **Organizations**, **Capabilities** et **Profiles**:

- **Organizations :** Cette section définit tous les membres du consortium. Chaque organisation a une ancre, par exemple `&Org1`. Sous cette ancre, vous pouvez trouver le nom de l'organisation, le MSPID, le répertoire d'accès à son dossier MSP ainsi que les homologues d'ancrage de leurs organisations pour les communications entre homologues. Vous pouvez compléter le profil de l'organisation pour chaque membre du consortium avec les étapes suivantes :
  1. Indiquez le `Nom` et l'`ID` de l'organisation en utilisant son MSPID. L'organisation qui crée le canal doit connaître le MSPID qui a été spécifié lors du déploiement de la charte Helm de l'homologue.
  2. Dans `MSPDir`, indiquez le chemin d'accès complet au dossier MSP que vous avez utilisé pour créer votre [définition d'organisation](#organization-definition). Notez qu'aucun de vos matériels cryptographiques n'est utilisé dans la transaction de création de canal. L'outil *configtxgen* ignorera le contenu réel du MSP. Cependant, il attend un dossier MSP à cet emplacement, avec la sous-structure de dossier correcte.
  3. Le paramètre `AnchorPeers` est utilisé pour identifier le principal homologue utilisé par chaque organisation pour la communication entre organisations. Indiquez le nom d'hôte et le port de l'homologue qui fera office d'[homologue d'ancrage![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html) pour cette organisation. Cette valeur est importante pour l'utilisation de fonctions telles que les données privées ou la reconnaissance de service ; toutefois, les données privées et la reconnaissance de service ne sont pas actuellement pris en charge par la charte Helm de l'homologue. 

- **Capabilities :** Cette section doit figurer dans le fichier `configtx.yaml` mais aucune modification n'est nécessaire.

- Section  **Profiles :**. Cette section contient les informations nécessaires à la création d'un nouveau canal. Le profil contient les informations suivantes :
  1. Nom du nouveau canal. 
  2. Nom du consortium qui est défini au sein du canal système et sera utilisé pour créer le canal.
  3. Liste des organisations qui seront ajoutées au canal. Les noms des organisations répertoriées utiliseront les homologues d'ancrage qui sont figurent dans la section `Organizations` pour la recherche du MSPID de ces organisations et des homologues d'ancrage associés.

  L'outil *configtxgen* utilise cette section `Profiles` pour créer la proposition de création de canal.

Une fois le fichier de configuration mis à jour, générez la proposition de transaction de mise à jour de canal à l'aide de la commande suivante :

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

Une commande réelle peut être ressembler à l'exemple suivant :
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


Si la commande aboutit, vous obtiendrez un résultat similaire à la sortie ci-dessous. La commande écrira la proposition de  transaction de mise à jour de canal dans votre répertoire `FABRIC_CFG_PATH`.

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

Si votre règle d'adhésion ne requiert pas d'autres signatures des autres membres du consortium, vous pouvez soumettre la proposition directement au service de tri pour former le nouveau canal. Exécutez la commande suivante à partir du même emplacement où vous avez généré la transaction de mise à jour du canal pour créer ce canal.

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

Où `<ORG_MSPID>` est le MSPID de votre organisation, et `<PATH_TO_ADMIN_MSP>` est le chemin d'accès au dossier MSP de votre admin homologue. Dans la commande, `<orderer_url>` est l'URL du service de tri qui va créer le canal, et `<orderer_tls_cert>` est le chemin d'accès au certificat TLS des services de tri. Une  commande réelle peut ressembler à l'exemple suivant :

```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## Validation de la proposition et soumission de la demande

Lorsque la proposition de création de nouveau canal est signée par suffisamment d'organisations pour respecter la règle d'adhésion du canal système, vous pouvez soumettre la transaction de mise à jour de canal au service de tri pour former le nouveau canal

Vous pouvez utiliser les API de logiciel SDK Node Fabric pour terminer la signature et la soumission en une seule transaction. Pour plus d'informations, consultez le  [Comment créer un canal Hyperledger Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) dans la documentation du logiciel SDK Node.

## Affichage des journaux de l'homologue
{: #peer-icp-view-logs}

Les journaux de composant peuvent être affichés à partir de la ligne de commande à l'aide de [`commandes de l'interface CLI kubectl`](#peer-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Votre fenêtre dans Elastic Search"), qui est inclus dans votre cluster ICP. 

- Utilisez la commande `kubectl logs` pour afficher les journaux de conteneur dans le  pod. Si vous avez des doutes quant à votre nom de pod, exécutez la commande suivante pour afficher la liste des pods.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Ensuite, exécutez la commande suivante pour extraire les journaux pour le conteneur d'homologue qui réside dans le pod en  replaçant  `<pod_name>` par le nom de votre pod de la sortie de commande plus haut :

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  Pour plus d'informations sur la commande `kubectl logs`, consultez la [documentation Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Vous pouvez aussi accéder aux journaux en utilisant la   [console de gestion de cluster ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), qui ouvre les journaux dans Kibana.

  **Remarque :** Lorsque vous affichez vos journaux dans Kibana, vous pouvez recevoir la réponse  `No results found`. Cette condition peut se produire si ICP utilise l'adresse IP de votre noeud worker comme nom d'hôte. Pour résoudre ce problème, supprimez le filtre qui commence par `node.hostname.keyword` en haut de l'écran et les journaux deviennent visibles.


## Traitement des incidents
{: #peer-icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```
{:codeblock}
2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the selected version is compatible with ICP 2.1.0.3. `v2.7.2 ` is recommended.

3.  After the helm installation completes, [login](#peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```
 {:codeblock}
 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```
 {:codeblock}

5. Check helm version:

  ```
  helm version --tls
  ```
  {:codeblock}

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problème :** Erreur lors de la connexion au conteneur homologue
{: #icp-bash-error}

Ce problème se produit lorsque vous utilisez l'outil kubectl pour exécution sur votre homologue par l'exécution de la commande suivante :

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}

L'opération échoue et produit un message d'erreur similaire à l'exemple suivant :

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Solution :**

This error occurs when on big endian systems, such as IBM System Z, when two Docker bash sessions are opened in the same Docker container. La deuxième session ne peut peut-être pas se connecter au conteneur en cours d'exécution. Pour résoudre ce problème, [redémarrez le conteneur homologue](#peer-restart) et relancez la commande `kubectl exec` une fois l'homologue redémarré.

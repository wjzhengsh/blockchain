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

# Exploitation des homologues sur {{site.data.keyword.cloud_notm}} Private avec un réseau de plan Starter ou Enterprise
{: #peer-operate_icp}

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Après que vous avez configuré un homologue {{site.data.keyword.blockchainfull}} Platform sur {{site.data.keyword.cloud_notm}} Private (ICP), vous devez effectuer plusieurs étapes supplémentaires pour que votre homologue puisse soumettre des transactions sur un réseau de plan Starter ou Enterprise. Ces étapes incluent l'ajout de votre organisation à un canal, l'association de votre homologue au canal, l'installation de code blockchain sur votre homologue, l'instanciation de code blockchain sur le canal, ainsi que la connexion d'applications à votre homologue.
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

Les logiciels SDK Fabric sont recommandés. Cependant, les instructions supposent que vous avez une bonne connaissance du fonctionnement du logiciel SDK. Si vous préférez utiliser la ligne de commande, vous pouvez utiliser le client homologue Fabric.

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](/docs/services/blockchain/howto/peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](/docs/services/blockchain/v10_application.html#ha-app).
-->

**Remarque **: L'homologue {{site.data.keyword.blockchainfull_notm}} Platform n'a pas accès à toutes les fonctionnalités ou à la prise en charge des homologues qui sont hébergés sur {{site.data.keyword.blockchainfull_notm}} Platform. Par conséquent, vous ne pouvez pas utiliser le moniteur réseau pour exploiter un homologue sur ICP. Avant de commencer à lancer des homologues, assurez-vous d'avoir passé en revue les [considérations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

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

**Remarque :** Si vous déployez votre homologue derrière un pare-feu, vous devez ouvrir un passe-système afin de permettre au réseau sur la plateforme d'effectuer un établissement de liaison TLS avec votre homologue. Vous devez uniquement activer un passe-système pour le port du noeud lié au port 7051 de vos homologues. Pour plus d'informations, voir la section relative à la [recherche de vos informations de noeud final](#peer-endpoint).

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

Les instructions suivantes utilisent le [Logiciel SDK Fabric Node![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Fabric Node") pour exploiter l'homologue et supposent une connaissance préalable du logiciel SDK. Vous pouvez utiliser le [tutoriel de développement d'applications](/docs/services/blockchain/v10_application.html) pour en savoir plus sur l'utilisation du logiciel SDK Node avant de commencer, et comme guide pour le développement d'applications avec votre homologue lorsque vous êtes prêt à appeler et à interroger le code blockchain.

### Installation du logiciel SDK Node

Vous pouvez utiliser NPM pour installer le [Logiciel SDK Node ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Node") :

```
npm install fabric-client@1.2
```
{:codeblock}

Il est recommandé d'utiliser la version 1.2 du logiciel SDK Node.

### Préparation du logiciel SDK pour l'utilisation de l'homologue
{: #remote-peer-node-sdk}

Votre homologue est déployé avec le certificat signCert de votre admin homologue interne. Cela vous permet d'utiliser les certificats et le dossier MSP de l'admin homologue pour exploiter l'homologue.

Recherchez les certificats que vous avez créés lorsque vous avez [enregistré votre admin homologue](/docs/services/blockchain/howto/peer_deploy_ibp.html#enroll-admin). Si vous avez utilisé les commandes en  exemple, vous pouvez trouver votre dossier MSP admin homologue dans `$HOME/fabric-ca-client/peer-admin`.
  - Vous pouvez générer le contexte utilisateur admin homologue avec le logiciel SDK qui utilise le certificat signCert (clé publique) et la clé privée dans le dossier MSP. Vous pouvez trouver ces clés dans les emplacements suivants :
    - Le certificat signCert peut se trouver dans le dossier **signcerts** : `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - La clé privée peut se  trouver dans le dossier **keystore:** : `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    Pour obtenir un exemple de formation d'un contexte d'utilisation et d'exploitation du logiciel SDK à l'aide seulement de la clé publique et la clé privée, voir [cette section du tutoriel relatif au développement d'applications](/docs/services/blockchain/v10_application.html#enroll-panel).

Vous pouvez également utiliser le logiciel SDK pour générer le certificat signCert admin homologue et la clé privée en utilisant les informations de noeud de l'autorité de certification dans Starter Plan ou Enterprise Plan et votre [nom d'utilisateur et mot de passe](/docs/services/blockchain/howto/peer_deploy_ibp.html#register-admin).

### Téléchargement du certificat signCert admin homologue vers un plan Starter ou Enterprise
{: #remote-peer-upload-sdk}

Vous devez envoyer par téléchargement le certificat signCert administrateur homologue au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform afin que votre application ait le droit d'exploiter le réseau.

- Le certificat signCert se trouve dans le répertoire où votre logiciel SDK a généré votre matériel cryptographique, dans le fichier nommé après votre homologue admin. Copiez le certificat figurant entre guillemets après le champ `certificate`, commençant par `-----BEGIN CERTIFICATE-----` et se terminant par `-----END CERTIFICATE-----`. Vous pouvez utiliser l'interface CLI pour convertir le certificat au format PEM à l'aide de la commande `echo -e "<CERT>" > admin.pem`. Vous pouvez ensuite coller le contenu du certificat dans votre réseau de blockchain à l'aide du moniteur de réseau. Connectez-vous au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform. A l'écran "Membres" du Moniteur réseau, cliquez sur **Certificats** > **Ajouter un certificat**. Indiquez un nom pour votre certificat et collez le contenu dans le champ **Certificat**. Cliquez sur **Redémarrer** lorsque vous êtes invité à indiquer si vous voulez redémarrer vos homologues. Cette opération doit être effectuée avant que votre organisation ne rejoigne le canal.

### Transmission du certificat TLS de votre homologue au logiciel SDK
{: #icp-peer-download-tlscert}

Vous devez référencer le certificat TLS de vos homologues pour authentifier les communications avec votre logiciel SDK. Recherchez le certificat TLS que vous avez [téléchargé depuis votre conteneur homologue](#peer-tls) et enregistrez-le à un emplacement où il peut être référencé par votre application. Si vous avez utilisé les commandes en exemple, vous pouvez trouver le certificat TLS de votre homologue dans `$accueil/matrice ca client/poste de tls/peertls.pem`. Vous pouvez ensuite importer le certificat TLS dans votre application à l'aide d'une simple commande de lecture de fichier.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Indication des informations de noeud final de l'homologue au logiciel SDK
{: #peer-SDK-endpoints}

Recherchez les [informations de noeud final de votre homologue ](#peer-endpoint) et indiquez-les au logiciel SDK en déclarant une nouvelle variable d'homologue ou en mettant à jour votre profil de connexion. L'exemple suivant définit l'homologue en tant que noeud final sur votre réseau Fabric et le transmet au certificat TLS que vous avez importé.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**Remarque :** Etant donné que l'homologue est en dehors de {{site.data.keyword.cloud_notm}}, les autres organisations sur le réseau {{site.data.keyword.blockchainfull_notm}} Platform ne pourront pas trouver les informations de noeud final de votre homologue dans leur profil de connexion. Si d'autres organisations doivent envoyer à votre homologue une transaction à approuver, vous devez leur fournir l'URL de l'homologue dans une opération  hors bande.

### Utilisation du logiciel SDK pour rejoindre un canal
{: #peer-join-channel-sdk}

Votre organisation doit être membre d'un canal pour que vous puissiez rejoindre le canal avec votre homologue. 

  - Vous pouvez démarrer un nouveau canal pour l'homologue. En tant qu'initiateur de canal, vous pouvez inclure automatiquement votre organisation durant la [création de canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Un autre membre du réseau blockchain peut également ajouter votre organisation à un canal existant en utilisant une [mise à jour de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Une fois que votre organisation est ajoutée à un canal, vous devez ajouter le certificat signCert de votre homologue au canal de sorte que les autres membres puissent vérifier votre signature numérique au cours des transactions. L'homologue envoie par téléchargement son certificat signCert lors de l'installation, ce qui signifie que vous devez uniquement synchroniser le certificat pour le canal. Dans l'écran "Canaux" de votre Moniteur réseau, localisez le canal rejoint par votre organisation et sélectionnez **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Action**. Cette action synchronise les certificats entre tous les homologues sur le canal. Vous devrez peut-être attendre quelques minutes le temps que le canal se synchronise avant l'exécution des commandes `join channel`.

    **Remarque :** Vous ne pourrez voir que les nouveaux blocs qui sont ajoutés au canal, le code blockchain instancié et les autres mises à jour de canal à l'écran "Canaux" de votre Moniteur réseau si l'homologue que vous ajoutez à ICP et à un réseau Starter Plan ou Enterprise Plan fait parte de la même organisation en tant qu'homologue qui est ajouté avec le Moniteur réseau. Ceci est dû au fait que le Moniteur réseau collecte des informations à l'écran "Canaux" de votre  homologue, et il n'a pas de visibilité sur les homologues à l'extérieur de {{site.data.keyword.cloud_notm}}. Si aucun de vos homologues n'utilise la fonction Données privées, les informations du Moniteur réseau seront les mêmes pour un homologue dans une organisation comme dans une autre.

Dès que votre organisation est devenue membre d'un canal, suivez les instructions permettant de [joindre votre homologue à un canal](/docs/services/blockchain/v10_application.html#join-channel-sdk) à l'aide du logiciel SDK. Vous devez indiquer l'URL du service de commande et le nom du canal.

### Utilisation du logiciel SDK pour installer le code blockchain sur l'homologue
{: #peer-install-cc-sdk}

Utilisez les instructions suivantes pour utiliser le logiciel SDK pour [installer un code blockchain](/docs/services/blockchain/v10_application.html#install-cc-sdk) sur votre homologue.

### Utilisation du logiciel SDK pour instancier le code blockchain sur l'homologue
{: #peer-instantiate-cc-sdk}

Un seul membre de ce canal doit instancier ou mettre à jour le code blockchain. Par conséquent, tout membre réseau du canal avec des homologues sur {{site.data.keyword.blockchainfull_notm}} Platform peut utiliser le Moniteur réseau pour instancier du code blockchain et spécifier les règles de validation. Toutefois, si vous souhaitez utiliser l'homologue pour instancier du code blockchain sur un canal, vous pouvez utiliser le logiciel SDK et suivre les instructions d'[instanciation d'un code blockchain](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).

## Utilisation de l'interface CLI pour exploiter l'homologue
{: #peer-cli-operate}

Vous pouvez également exploiter votre homologue depuis la ligne de commande à l'aide du client `homologue` Fabric.

Votre homologue a été déployé avec le certificat signCert de votre admin homologue interne, ce qui permet à cette identité d'exploiter l'homologue. Les instructions ci-après utilisent le dossier MSP admin homologue qui a été généré lorsque vous avez [déployé votre homologue](/docs/services/blockchain/howto/peer_deploy_ibp.html#register-admin) pour joindre l'homologue à un canal, installer un code blockchain sur l'homologue, puis instancier le code blockchain sur un canal.

### Téléchargement du client homologue Fabric
{: #peer-client}

Le moyen le plus simple d'obtenir le client homologue est de télécharger tous les binaires de l'outil Fabric du projet Hyperledger. Accédez au répertoire dans lequel vous souhaitez télécharger les binaires à partir de votre ligne de commande, puis procédez à leur extraction en exécutant la commande ci-dessous. Si vous n'avez pas encore installé [Curl ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl"), vous devez d'abord effectuer cette opération en premier.

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

Le téléchargement des binaires va créer un dossier `config` contenant un fichier core.yaml, orderer.yaml et configtx.yaml. Définissez le chemin de configuration Fabric sur ce répertoire `config` :

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

Avant d'exploiter l'homologue, vous devez procéder à quelques opérations de gestion des certificats sur votre machine locale. Par exemple, vous devez télécharger le certificat signCert admin vers Starter Plan ou Enterprise Plan et vous assurer que vous pouvez accéder aux certificats TLS à partir de l'homologue et votre réseau sur {{site.data.keyword.cloud_notm}}. Pour plus d'informations sur les certificats à utiliser et les tâches à exécuter, voir  [Gestion des  certificats sur {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

1. Déplacez le certificat signCert de l'admin de votre homologue vers un nouveau dossier nommé `admincerts` :

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Téléchargez ce certificat signCert vers le réseau sur {{site.data.keyword.cloud_notm}} de sorte qu'une interface CLI ou une application distante puisse effectuer des opérations de canal, comme l'extraction du bloc d'origine de canal ou l'instanciation de code blockchain.
    1. Sur votre machine locale, ouvrez le fichier `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` et copiez-le dans le presse-papiers.
    2. Accédez au Moniteur réseau de votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Cliquez sur **Membres** dans le navigateur de gauche et cliquez sur l'onglet **Certificats**.
    4. Cliquez sur le bouton **Ajouter un certificat**.
    5. Dans la fenêtre **Ajouter un certificat**, donnez un nom à votre certificat, par exemple "peer-admin", collez le certificat que vous venez de copier dans le presse-papiers et cliquez sur **Soumettre**.
    6. Il vous sera demandé si vous souhaitez redémarrer les homologues. Cliquer sur **Redémarrer**.
    7. Cliquez sur **Canaux** dans le navigateur de gauche et recherchez le canal que l'homologue va  rejoindre.
    8. Cliquez sur les trois points sous l'en-tête **Actions** et cliquez sur **Synchroniser le certificat**. Dans la fenêtre **Synchroniser le certificat**, cliquez sur **Soumettre**.

    **Remarque **: Il est important de synchroniser le certificat de canal avant que l'homologue ne rejoigne le canal ou instancie le code blockchain sur le canal. Vous devrez peut-être attendre quelques minutes le temps que le canal se synchronise avant l'exécution des commandes join channel ou l'instanciation de commandes de code blockchain. 

3. Vérifiez que vous avez [téléchargé le certificat TLS de votre homologue](#peer-tls) et que vous pouvez le référencer à partir de la ligne de commande. Si vous avez suivi les commandes en exemple, vous pouvez trouver ce certificat TLS dans le fichier `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

4. Vous devez également référencer le certificat TLS que vous avez utilisé pour communiquer avec l'autorité de certification de votre Starter Plan ou Enterprise Plan lors de  l'[inscription de votre admin homologue](/docs/services/blockchain/howto/peer_deploy_ibp.html#enroll-admin). Si vous avez suivi les exemples de commandes figurant dans cette documentation, vous pouvez trouver le certificat dans le fichier  `$HOME/fabric-ca-client/tls-ibp/tls.pem`.

Vous pouvez exécuter une commande tree pour vérifier que vous avez effectué ces étapes. Accédez au répertoire dans lequel vous avez stocké vos certificats. Une commande tree doit générer un résultat similaire à la structure suivante :
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Définition des variables d'environnement de l'interface de ligne de commande
{: #environment-variables}

Après avoir déplacé tous vos certificats vers l'emplacement nécessaire, vous devez définir des variables d'environnement qui seront utilisées par les commandes. Vous serez ensuite prêts à utiliser la client homologue Fabric pour exploiter votre homologue.

1. Extrayez les informations de configuration de Starter Plan ou Enterprise Plan depuis le **profil de connexion** qui est disponible dans l'écran **Présentation** du Moniteur réseau. Cliquez sur **Profil de connexion**, puis sur **Télécharger**.

  - Localisez l'URL du service de tri en recherchant **orderers**, qui se trouve sous `orderers > url`. Notez l'URL qui commence par le nom réseau. L'URL est similaire à l'exemple suivant :

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localisez le nom de votre organisation en recherchant **organizations**. Il doit s'agir de la même organisation que celle que vous utilisez pour enregistrer votre homologue. Vous pouvez trouver le nom de votre organisation ainsi que le `mspid` associé. Notez la valeur de `mspid`.

2. [Recherchez les informations de noeud final de votre homologue](#peer-endpoint). Vous devez utiliser ce noeud final d'homologue pour définir la variable d'environnement `PEERADDR`. Assurez-vous d'exclure `http://` au début.

3. Exécutez les commandes suivantes pour définir les variables d'environnement.

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Remplacez les zones par vos informations.
    - Remplacez `<full_path_to_config_folder>` par le dossier config qui a été créé lorsque vous avez [téléchargé le client homologue](#peer-client)
    - Remplacez `<CHANNEL_NAME>` par le nom du canal que rejoint l'homologue.
    - Remplacez `<CC_NAME>` par un nom pour faire référence à votre code blockchain.
    - Remplacez `<PEERADDR>` par le noeud final d'homologue de l'étape précédente pour l'homologue que vous utilisez actuellement.
    - Remplacez `<ORDERER_URL>` par le nom d'hôte et le port du service de tri de votre profil de connexion.
    - Remplacez `<PATH_TO_ADMIN_MSP>` par le chemin d'accès au dossier MSP de l'admin de votre homologue.
    - Remplacez `<PATH_TO_PEER_TLS_CERT>` par le chemin d'accès au certificat TLS homologue que vous avez téléchargé.
    - Remplacez `<MSPID_OF_PEER_BEING_USED>` par le MSPID d'organisation de l'homologue que vous utilisez pour extraire un bloc d'origine, rejoindre un canal ou installer un code blockchain. 

  Par exemple :

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
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

Avant d'exécuter les commandes d'interface CLI pour joindre l'homologue à un canal, il est nécessaire d'ajouter votre organisation à un canal du réseau de l'une des manières ci-après.

  - Vous pouvez démarrer un nouveau canal pour l'homologue. En tant qu'initiateur de canal, vous pouvez inclure automatiquement votre organisation durant la [création de canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).
  - Un autre membre du réseau blockchain peut également ajouter votre organisation à un canal existant en utilisant une [mise à jour de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Une fois que votre organisation est ajoutée à un canal, vous devez ajouter le certificat signCert de votre homologue au canal de sorte que les autres membres puissent vérifier votre signature numérique au cours des transactions. L'homologue envoie par téléchargement son certificat signCert lors de l'installation, de sorte que vous devez uniquement synchroniser le certificat pour le canal. Dans l'écran "Canaux" de votre Moniteur réseau, localisez le canal rejoint par votre organisation et sélectionnez **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Action**. Cette action synchronise les certificats entre tous les homologues sur le canal.

    **Remarque :** Si votre homologue ICP qui se connecte à un réseau Starter Plan ou Enterprise Plan network fait partie de la même organisation qu'un autre homologue qui est ajouté avec le Moniteur réseau, vous pouvez afficher uniquement les nouveaux blocs qui sont ajoutés au canal, au code blockchain qui est instancié, et d'autres mises à jour de canal de l'écran "Canaux" de l'écran de votre Moniteur réseau. Ceci est dû au fait que le Moniteur réseau collecte des informations à l'écran "Canaux" de votre  homologue, et il n'a pas de visibilité sur les homologues à l'extérieur de {{site.data.keyword.cloud_notm}}. Si aucun de vos homologues n'utilise la fonction Données privées, les informations du Moniteur réseau seront les mêmes pour un homologue d'une organisation que pour un autre homologue.

1. Assurez-vous que vous avez défini les [variables d'environnement dans votre interface de ligne de commande](#environment-variables).

2. Extrayez le block d'origine du canal pour générer le registre de canal sur votre homologue. Notez que `$HOME/fabric-ca-client/tls-ibp/tls.pem` représente le chemin d'accès au certificat TLS de votre Starter Plan ou Enterprise Plan. Si votre chemin est différent, assurez-vous de définir le chemin correct.

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
  ```
  {:codeblock}

  **Remarque :** Il est possible que lors de l'exécution de l'une de ces commandes de l'interface CLI, vous obteniez l'avertissement suivant qui peut être ignoré.


  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  Exécutez la commande suivante pour vérifier que le bloc d'origine est ajouté à votre conteneur homologue avec succès :

  ```
  ls *.block
  ```
  {:codeblock}

  Vous savez que le bloc d'origine est ajouté lorsque vous voyez quelque chose de semblable à l'exemple suivant :

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. Une fois que vous avez extrait le bloc d'origine du canal, vous pouvez joindre l'homologue au canal à l'aide de la commande suivante.  

  ```
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

Exécutez la commande CLI homologue suivante pour installer le code blockchain `fabcar` sur l'homologue :

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

Lorsque cette commande est exécutée correctement, vous devriez voir un quelque chose de similaire à ceci :

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**Remarque :** Si ce code blockchain a déjà été installé et instancié sur un autre homologue s'exécutant dans IBP Starter ou Enterprise Plan, des étapes supplémentaires doivent être effectuées avant l'installation du code blockchain sur l'homologue. Consultez [rubrique relative au traitement des incidents](#peer-ibp-troubleshooting) pour plis de détails sur la manière d'éviter les erreurs associées au mode d'installation de ce code blockchain.

### Utilisation de l'interface CLI pour instancier un code blockchain sur un canal
{: #icp-toolcontainer-instantiate-cc}

Etant donné qu'un seul homologue doit instancier du code blockchain sur un canal, vous n'avez pas à utiliser votre homologue pour instancier du code blockchain. Les homologues qui sont hébergés sur {{site.data.keyword.blockchainfull_notm}} Platform peuvent faire cela. Toutefois, si vous préférez que l'homologue instancie du code blockchain sur le canal, vous pouvez exécuter la commande suivante ci-après pour cela. Assurez-vous de définir la valeur de `CORE_PEER_TLS_ROOTCERT_FILE` sur le chemin d'accès au certificat TLS de votre autorité de certification dans Starter Plan ou Enterprise Plan. 

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

Lorsque cette commande est exécutée correctement, vous devriez voir un quelque chose de similaire à ceci :

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

Une fois le code blockchain instancié, vous pouvez utiliser une requête de code blockchain et appeler des commandes pour lire et écrire des données dans le registre de canal. Pour plus d'informations, voir les [code blockchain homologue![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) dans la documentation Hyperledger Fabric. Vous devrez transmettre le noeud fin du service de tri à vos commandes d'appel en utilisant l'IP proxy et le porte de service de tri externe. Il vous suffit de transmettre le noeud final homologue à une commande de requête.

## Mise à jour du code blockchain

Il est probable qu'au fil du temps vous devrez modifier le code blockchain qui s'exécute sur l'homologue. Etant donne que le code blockchain est installé sur les homologues et instancié sur le canal, vous devez mettre à jour le code blockchain sur tous les homologues du canal.

Procédez comme suit pour mettre à jour votre code blockchain :

1. Pour mettre à jour le code blockchain sur chaque homologue, relancez simplement le processus utilisé pour installer le code blockchain sur les homologues, au moyen d'une application client ou d'une commande d'interface de ligne de commande. Veillez à indiquer le même nom de code blockchain que celui utilisé initialement. Toutefois, cette fois incrémentez la `version` de code blockchain. Tous les homologues doivent utiliser le même nom et la même version de code blockchain.

2. Une fois le nouveau code blockchain installé sur tous les homologues du canal, utilisez le Moniteur réseau ou la commande
[peer chaincode upgrade ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) pour mettre à jour le canal qui va utiliser le nouveau code blockchain.

Consultez l'étape 2 des présentes [instructions](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) pour plus d'informations sur l'utilisation du panneau "Installer le code" du Moniteur réseau pour la mise à jour du code blockchain sur le canal.

## Affichage des journaux de l'homologue
{: #peer-ibp-view-logs}

Les journaux de composant peuvent être affichés à partir de la ligne de commande à l'aide de [`commandes de l'interface CLI kubectl`](#ca-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Votre fenêtre dans Elastic Search"), qui est inclus dans votre cluster ICP. 

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
{: #peer-ibp-troubleshooting}

### **Problème :** La commande Invoke échoue sur un homologue avec une erreur `chaincode fingerprint mismatch`
{: #icp-cc-install-error}

Il est possible de recevoir une erreur `chaincode fingerprint mismatch` lors de l'exécution d'une demande `peer chaincode invoke` sur un homologue qui s'exécute dans {{site.data.keyword.cloud_notm}} Private :

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

Cette erreur peut être due à la présence d'une incohérence entre homologues qui s'exécutent dans le code blockchain de l'une des zones suivantes :

- Nom du code blockchain
- Version de code blockchain
- Chemin d'accès au code blockchain
- Binaire du code blockchain

Cette erreur peut se produire si l'utilisation du Moniteur réseau a été utilisée pour installer et instancier du code blockchain sur un homologue s'exécutant dans un plan Starter Plan Enterprise Plan, et que vous installez ultérieurement le code blockchain sur un homologue s'exécutant sur {{site.data.keyword.cloud_notm}} Private. L'erreur se produit dans la demande `invoke` car les chemins d'accès au code blockchain résultant sur les homologues sont différents.

**Solution :**
Si vous voulez exécuter du code blockchain sur des homologues à la fois dans {{site.data.keyword.cloud_notm}}, comme Starter ou Enterprise Plan, et {{site.data.keyword.cloud_notm}} Private, n'utilisez pas l'interface utilisateur du Moniteur réseau pour installer le code blockchain. Créez plutôt un package du code blockchain avec la commande [`peer chaincode package`![Icône de lien externe](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package), puis installez le package sur tous les homologues en exécutant la commande [`peer chaincode install`](#icp-toolcontainer-install-cc).

Si le code blockchain est déjà installé et instancié sur un canal avant que vous n'essayiez d'installer le code blockchain sur un homologue {{site.data.keyword.cloud_notm}} Private, vous devez effectuer les étapes suivantes pour éviter ce problème :

1. Créez plutôt un package du code blockchain avec la commande [`peer chaincode package`![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package).
2. Installez le package de code blockchain sur l'homologue qui s'exécute sur {{site.data.keyword.cloud_notm}} Private en exécutant la commande `peer chaincode installer`.
3. Si vous avez des binaires spécifiques à la plateforme, vous pouvez exécuter la commande [`peer chaincode upgrade`![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) pour mettre à niveau le code blockchain s'exécutant sur l'homologue Starter Plan ou Enterprise Plan, qui utilise le package de code blockchain.
4. Instanciez le code blockchain nouvellement installé sur le canal en utilisant l'interface utilisateur du Moniteur réseau ou l'interface CLI.

Le processus de mise à niveau du code blockchain est également accessible dans [`Chaincode for Operators`![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) dans la documentation Hyperledger Fabric.

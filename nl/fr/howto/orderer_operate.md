---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Utilisation d'un service de tri sur {{site.data.keyword.cloud_notm}} Private
{: #orderer-operate}

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Une fois que vous avez installé un service de tri {{site.data.keyword.blockchainfull}} Platform dans ICP,  une configmap est créée et elle contient les paramètres par défaut des variables d'environnement. Vous pouvez ensuite changer ou ajouter des variables d'environnement pour le service de tri afin de configurer son comportement.

En règle générale, les administrateurs du service de tri sont responsables de l'amorçage et de la gestion des services de tri, mais cela n'est pas nécessaire dans un déploiement SOLO où il existe un seul service de tri. Dans un déploiement SOLO, l'admin du service de tri est responsable de l'ajout de nouvelles organisations au canal système du service de tri.

## Prérequis

Vous devez effectuer quelques étapes préalables depuis votre cluster ICP pour exploiter votre service de tri.

**Etapes préalables :**
- [Configurer vos interfaces de ligne de commande](#orderer-kubectl-configure)
- [Extraire des informations de noeud final de votre service de tri](#orderer-endpoint)
- [Télécharger le certificat TLS de votre service de tri](#orderer-tls)

Vous pouvez ensuite utiliser les instructions de cette rubrique pour utiliser votre service de tri. Notez que vous allez utiliser votre service de tri via la ligne de commande, ce qui implique d'obtenir le binaire CLI `peer`. Le binaire CLI `peer` est téléchargé avec les autres binaires, par exemple `configtxlator`. Par conséquent, le nom de la plupart des commandes de cette rubrique commence par "peer". Il n'utilise pas réellement l'homologue, il s'agit juste du nom du binaire.

### Configuration des interfaces de ligne de commande
{: #orderer-kubectl-configure}

Vous devez utiliser l'outil de ligne de commande **kubectl** pour vous connecter au conteneur de service de tri qui s'exécute dans ICP.

1. Connectez-vous à l'interface utilisateur de votre cluster ICP. Accédez à l'onglet **Outils de ligne de commande** et cliquez sur **Interface de ligne de commande Cloud Private**. Vous verrez les outils suivants que vous pouvez télécharger.

   * Installer l'interface CLI et les plug-ins IBM Cloud Private
   * Installer l'interface CLI Kubectl
   * Installer Helm
   * Installer l'interface CLI Istio

  Pour utiliser des services de tri, vous devez utiliser les **trois** premiers outils, Helm étant facultatif. Cliquez sur chacun d'eux et exécutez les commandes `curl` pour le type de machine que vous utilisez. Ensuite, exécutez les commandes `chmod` et `sudo mv` pour chaque outil. La commande `chmod` modifie les droits de l'interface de ligne de commande en question afin de la rendre exécutable, et la commande `sudo mv` déplace le fichier et le renomme.

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

  Si l'opération aboutit, la commande doit renvoyer une réponse qui est similaire à l'exemple suivant, où `orderer-6cf85f6687-hcxpl` est le nom du conteneur de service de tri. 

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Vous êtes maintenant prêt à utiliser l'outil **kubectl** pour obtenir les informations de noeud final du service de tri.

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

### Extraction des informations de noeud final du service de tri
{: #orderer-endpoint}

Vous devez cibler le noeud final de votre service de tri afin d'apporter des mises à jour au canal système du service de tri. Vous devrez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Rôles et actions de l'administrateur de cluster") pour effectuer les étapes suivantes :

1. Connectez-vous à la console ICP et cliquez sur l'icône **Menu ** dans l'angle supérieur gauche.
2. Cliquez sur **Charge de travail** > **Editions Helm**. 
3. Recherchez le nom de votre édition Helm et ouvrez l'écran des détails de l'édition Helm.
4. Faites défiler jusqu'à la section **Remarques** au bas de la fenêtre. Dans la section  La section **Remarques** inclut un ensemble de commandes pour vous aider à effectuer le déploiement de votre service de tri.
5. Si vous ne l'avez pas déjà, suivez les instructions pour configurer  l'[interface CLI kubeclt](#orderer-kubectl-configure). Vous devez l'utiliser pour interagir avec le conteneur de votre service de tri.
6. Dans votre interface de ligne de commande, exécutez la première commande de la note, qui suit **1. Get the application URL by running these commands:** Cette commande produira l'URL et le port du service de tri, comme dans l'exemple suivant. Sauvegardez cette URL et vous devez l'utiliser dans d'autres commandes futures pour définir l'adresse et le port de noeud externe de votre proxy.

  ```
  http://9.30.94.174:30159
  ```

Dans cet exemple, l'adresse IP proxy est `9.30.94.174` et le port de noeud externe qui correspond à 7050 est `30159`.  

**Remarque :** Si vous déployez votre service de tri derrière un pare-feu, vous devez ouvrir un passe-système afin de permettre au réseau sur la plateforme d'effectuer cette tâche. 

### Téléchargement du certificat TLS de votre service de tri
{: #orderer-tls}

Vous devez télécharger le certificat TLS de votre service de tri et le transmettre à vos commandes afin de pouvoir communiquer avec votre service de tri homologue depuis un client distant.Vous devrez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Rôles et actions de l'administrateur de cluster") pour effectuer les étapes suivantes :

1. Connectez-vous à la console ICP et cliquez sur l'icône **Menu ** dans l'angle supérieur gauche.
2. Cliquez sur **Charge de travail** > **Editions Helm**. 
3. Recherchez le nom de votre édition Helm et ouvrez l'écran des détails de l'édition Helm.
4. Faites défiler jusqu'à la section **Remarques** au bas de la fenêtre. Dans la section  La section **Remarques** inclut un ensemble de commandes pour vous aider à effectuer le déploiement de votre service de tri.
5. Si vous ne l'avez pas déjà, suivez les instructions pour configurer  l'[interface CLI kubeclt](#ca-kubectl-configure). Vous devez l'utiliser pour interagir avec le conteneur de votre service de tri.
6. Dans votre interface de ligne de commande, exécutez la troisième commande de la note, qui suit **3. Get orderer TLS root cert file**. Cette commande permet de sauvegarder votre certificat TLS sous le nom de fichier `cert.pem` sur votre machine locale.

  **Remarque :** avant d'exécuter les commandes, vous pouvez supprimer `app=orderer` comme indiqué dans la commande ci-dessous :

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. Déplacez le certificat généré vers un emplacement où vous pouvez le référencer dans de futures commandes, puis renommez-le `orderertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### Gestion des certificats sur votre système local
{: #manage-certs}

Accédez au répertoire où le dossier MSP admin du service de tri est généré. Selon que vous avez suivi les étapes en exemple décrites dans cette documentation, ou le nombre de composants que vous déployez, vous pouvez rechercher le dossier MSP dans `$HOME/fabric-ca-client/orderer-admin/msp` ou `$HOME/fabric-ca-client/peer-admin/msp`

Avant d'utiliser le service de tri, vous devez procéder à quelques opérations de gestion des certificats sur votre machine locale. Vous devez également vous assurer que vous pouvez accéder aux certificats TLS à partir du service de tri. Pour plus d'informations sur les certificats à utiliser, voir [Fournisseur de services aux membres (MSP)](CA_operate.html#msp) dans [Exploitation d'une autorité de certification sur {{site.data.keyword.cloud_notm}} Private](CA_operate.html).

1. Déplacez le certificat signCert de l'admin de votre service de tri vers un nouveau dossier nommé `admincerts` :

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Vérifiez que vous avez [téléchargé le certificat TLS de votre service de tri](#orderer-tls) et que vous pouvez le référencer à partir de la ligne de commande. Si vous avez suivi les exemples de commandes figurant dans cette documentation, vous pouvez trouver ce certificat dans le fichier `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`.

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
├── orderer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
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

## Ajout d'organisations au canal système du service de tri
{: #add-organizations-to-consortium}

**Remarque :** Si vous prévoyez de déployer un homologue et de le connecter à un réseau de plan Starter ou Enterprise, vous pouvez omettre cette étape.

Lorsque vous déployez un service de tri à l'aide de d'une charte Helm {{site.data.keyword.blockchainfull_notm}} Platform for ICP, le bloc d'origine est automatiquement créé avec l'organisation du service de tri en tant que seul administrateur du canal système. Cela signifie que l'admin de l'organisation du service de tri est le seul avec la capacité d'ajouter des organisations au consortium. Les règles qui régissent cela, appelées `mod_policy` pour le canal système du service de tri, ne doivent pas être modifiées après que les nouvelles organisations sont ajoutées au canal système du service de tri.

L'ajout d'organisations au canal système du service de tri fait de celles-ci une partie "du consortium", c'est-à-dire la liste des membres qui peuvent, par défaut, créer des canaux. En tant que membre du consortium, il est également plus facile d'être ajouté à un canal à l'aide de certificats et d'informations qui sont répertoriées dans le canal système.

La mise à jour du canal système du service de tri est effectuée via la "configuration des transactions de mise à jour", dans laquelle les informations MSP connexes des organisations est ajoutée à la configuration de canal du canal système du service de tri.

L'ajout d'organisations au canal système du service de tri est essentiellement identique au flux de mise à jour de la configuration de canal pour l'ajout d'une organisation. Toutefois, vous devez apporter quelques changements car le canal à mettre à jour n'est pas un canal d'application et l'admin approprié est l'admin du service de tri au lieu d'une organisation d'homologue.

Notez que vous pouvez ajouter une organisation à un canal sans d'abord rejoindre le canal système. Pour plus d'informations, voir le [tutoriel d'ajout d'une  organisation à un canal](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html) dans la documentation Hyperledger Fabric.

La liste suivante présente les étapes générales et les tâches qui seront effectuées par différents ensembles d'organisations de votre consortium.

1. Chaque organisation qui souhaite rejoindre le consortium doit [préparer une définition d'organisation](peer_operate_icp.html#organization-definition).
2. L'admin de l'organisation du service de tri [forme le consortium](#consortium) par l'ajout d'organisations au canal système du service de tri.
3. Toute organisation du consortium peut [créer un nouveau canal](peer_operate_icp.html#peer-icp-channeltx) par la préparation d'une transaction de configuration de canal.

## Obtention des outils Fabric
{: #get-fabric-tools}

Vous devez télécharger les outils Hyperledger Fabric ci-après pour mettre à jour le canal système.
- [peer](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html), qui vous permettra d'extraire le bloc d'origine et de mettre à jour le canal système.
- [configtxlator](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html), qui convertit le format protobuf d'une configuration de canal au format JSON qui est plus facile à lire et à mettre à jour. 

1. Indiquez où vous souhaitez stocker les outils et exécutez cette commande :

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. Définissez le chemin d'accès à vos outils, où les outils Fabric ci-dessus sont téléchargés :

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  Par exemple, si vous avez installé les binaires dans votre répertoire de base, vous définiriez votre `PATH` comme suit :

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Le téléchargement des binaires va créer un dossier config contenant un fichier core.yaml, orderer.yaml et configtx.yaml. Définissez le chemin de configuration Fabric sur ce chemin de configuration :

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  Par exemple :
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## Création d'une définition d'organisation
{: #org-definition}

La **définition** d'une organisation contient le nom de l'organisation (ID MSP) et les certificats pertinents. Le canal système et les canaux d'application vont utiliser cette définition pour inclure votre organisation dans les règles qui contrôlent la création de canal, les mises à jour et la validation de transaction. Chaque organisation qui souhaite rejoindre le consortium doit effectuer cette étape. Pour en savoir plus, voir la section relative à la [préparation d'une définition d'organisation](peer_operate_icp.html#organization-definition).

## Formation du consortium
{: #consortium}

Rappelez le flux de haut niveau pour la formation d'un consortium :

1. Le canal système du service de tri est formé par uniquement l'organisation du service de tri en tant que membre du canal système. Cette organisation peut effectuer des mises à jour sans que des signatures supplémentaires soient nécessaires. 
2. L'admin de l'organisation du service de tri reçoit des définitions d'organisation de membres qui souhaitent rejoindre le consortium. L'organisation du service de tri utilise les définitions d'organisation pour mettre à jour la configuration du canal système du service de tri.

### Obtention des définitions d'organisation

Le service de tri doit recevoir les [définitionsd'organisation](peer_operate_icp.html#organization-definition) de membres qui souhaitent rejoindre le consortium. Cela doit être effectué dans une opération hors bande avec d'autres membres qui vous envoient des fichiers JSON qui incluent leur ID MSP et le matériel cryptographique. Pour la référence dans les commandes ci-dessous, on suppose que vous avez créé un dossier nommé `org-definitions` et placé tous les fichiers pertinents dans ce répertoire.

### Extraction du bloc d'origine du canal système

1. Définissez les variables d'environnement ci-après en utilisant le nom MSP de l'organisation de votre service de tri qui est établi durant le déploiement, le chemin d'accès au MSP de votre service de tri, ainsi que le chemin d'accès au certificat TLSCA de votre service de tri.

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  Remplacez les zones par vos informations.

    - Remplacez `<CORE_PEER_LOCALMSPID>`par l'ID MSP de l'organisation de votre service de tri. Elle est également visible dans le conteneur de service de tri en exécutant les commandes suivantes, dans lesquelles `<orderer pod name>` par la valeur du pod de votre service de tri :

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      La sortie devrait être similaire à ceci :

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      Par conséquent, le `CORE_PEER_LOCALMSPID` est ordererOrg.

    - Remplacez `<CORE_PEER_MSPCONFIGPATH>` parle chemin d'accès au dossier MSP admin de l'organisation du service de tri.
    - Remplacez `<CORE_PEER_TLS_ROOTCERT_FILE>` par le chemin d'accès au certificat de l'autorité de certification TLS.
    - Remplacez `<PROXY_IP>` par l'adresse IP proxy des [informations de noeud final du service de tri](#orderer-endpoint)
    - Remplacez `<EXTERNAL_NODE_PORT>` par le port de noeud externe des [informations de noeud final du service de tri](#orderer-endpoint)

  Vos variables d'environnement peuvent ressembler à l'exemple suivant :

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  Vous pouvez vérifier ces variables d'environnement à n'importe quel moment en exécutant les commandes suivantes :

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. Définissez le nom de canal comme variable d'environnement. Le nom du canal système du service de tri sera toujours `test-system-channel-name`.

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. Procédez à l'extraction du bloc d'origine du canal système. Tout d'abord, créez un répertoire appelé `configupdate` dans `org-definitions` pour stocker les fichiers de configuration générés lors du processus de mise à jour de la configuration. Vous pouvez appeler ce répertoire comme vous voulez, mais les exemples de commandes suivants utilisent `configupdate.`

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  Remplacez `<orderer_TLS_root_cert_file>` par le chemin d'accès au fichier `orderertls.pem` que vous avez créé dans cette [étape](#orderer-tls). Par exemple, la commande peut ressembler à l'exemple suivant :

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Si la commande aboutit, cette commande génère le résultat suivant.

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  Vous pouvez également trouver ensuite le bloc `genesis.pb` dans votre système de fichiers.

### Création de la mise à jour du canal système
{: #system-channel-update}

L'[outil Fabric](#get-fabric-tools) `configtxtlator` téléchargé convertit le format protobuf d'une configuration de canal au format JSON, et inversement.

Ces étapes suivent le flux général du tutoriel de mise à jour de canal sur la[conversion du bloc au format JSON]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down). Vous devez apporter certaines modifications aux commandes dans ce tutoriel pour refléter le fait que vous mettez à jour le canal système du service de tri et non un canal d'application. Vous pouvez consulter le tutoriel pour plus de détails sur ce processus. Cette section fournit simplement les commandes.

1. Copiez le fichier JSON de la définition d'organisation du dossier où vous avez [créé votre organisation](peer_operate_icp.html#organization-definition) vers le dossier `configupdate`. Dans l'exemple de commande ci-dessous, le fichier JSON de la définition d'organisation est `org1definition.json` :

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. Convertissez le bloc d'origine au format JSON. Exécutez les commandes suivantes :

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. Exécutez la commande suivante pour ajouter le matériel cryptographique d'une organisation à la configuration du consortium. Remplacez <NEWORGMSP> par l'ID MSO de l'organisation pour l'[organisation que vous avez créée](peer_operate_icp.html#organization-definition).

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  La commande peut ressembler à l'exemple suivant :

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. Répétez cette commande pour chaque organisation qui doit rejoindre le consortium. Assurez-vous de définir le fichier `./orgdefinition.json` comme étant le nouveau fichier JSON de l'organisation.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. Exécutez les commandes suivantes pour convertir le fichier `modified_config.json` pour la mise à jour de configuration au format JSON.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. Une fois ces commandes exécutées, placez une enveloppe sur `config_update`.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. Exécutez la commande suivante pour reconvertir la mise à jour de configuration au format protobuf :

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### Envoi de la mise à jour au canal système

Avant d'effectuer ces étapes, assurez que vos valeurs `FABRIC_CFG_PATH`, `$PROXY` et `ORDERER_PORT` sont correctement définies. Exécutez
la commande suivante :

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](#orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](#orderer-endpoint)>
```
{:codeblock}

Vos variables d'environnement peuvent ressembler à l'exemple suivant :

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

Une fois la mise à jour de configuration de canal créée, `config_update_in_envelope.json`, l'organisation du service de tri peut soumettre la mise à jour de canal à l'aide de la commande suivante :

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

Cette commande signe simultanément la demande de mise à jour et l'envoie au service de tri. Etant donné que l'organisation du service de tri est le seul administrateur du canal système, une seule signature est nécessaire pour que cette mise à jour soit une demande valide. Si la mise à du canal système aboutit, vous pouvez voir la sortie suivante :

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

Il est possible d'inclure plusieurs définitions d'organisation dans la mise à jour de configuration d'un seul canal système de service de tri, mais il est recommandé de mettre à jour le canal avec une organisation à la fois et de vérifier que la mise à jour a abouti.

## Affichage des journaux du service de tri
{: #orderer-view-logs}

Les journaux de composant peuvent être affichés à partir de la ligne de commande à l'aide de [`commandes de l'interface CLI kubectl`](#ca-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Votre fenêtre dans Elastic Search"), qui est inclus dans votre cluster ICP. 

- Utilisez la commande `kubectl logs` pour afficher les journaux de conteneur dans le  pod. Si vous avez des doutes quant à votre nom de pod, exécutez la commande suivante pour afficher la liste des pods.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Ensuite, exécutez la commande suivante pour extraire les journaux pour le conteneur de service de tri qui réside dans le pod en  replaçant `<pod_name>` par le nom de votre pod de la sortie de commande plus haut :

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  Pour plus d'informations sur la commande `kubectl logs`, consultez la [documentation Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Vous pouvez aussi accéder aux journaux en utilisant la   [console de gestion de cluster ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), qui ouvre les journaux dans Kibana.

  **Remarque :** Lorsque vous affichez vos journaux dans Kibana, vous pouvez recevoir la réponse  `No results found`. Cette condition peut se produire si ICP utilise l'adresse IP de votre noeud worker comme nom d'hôte. Pour résoudre ce problème, supprimez le filtre qui commence par `node.hostname.keyword` en haut de l'écran et les journaux deviennent visibles.

## Traitement des incidents
{: #orderer-troubleshooting}

### **Problème : la commande ** `peer channel update` échoue avec une erreur.
{: #orderer-peer-channel-update-error}

Il est possible de recevoir l'erreur suivante lorsque vous exécutez une commande `peer channel update` :

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

Cette erreur peut se produire si l'homologue utilise le même ID MSP organisation que le service de tri.  

**Solution :**  

Pour résoudre ce problème, vous devez redéployer votre homologue en spécifiant un ID MSP organisation différent de celui qui a été utilisé lors du déploiement du service de tri.

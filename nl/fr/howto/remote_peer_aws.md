---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Déploiement d'{{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #remote-peer-aws}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Ces instructions expliquent comment utiliser un modèle de démarrage rapide Amazon Web Services (AWS) pour créer un homologue {{site.data.keyword.blockchainfull}} Platform for AWS et le connecter ensuite à un réseau sur une plateforme {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

Pour plus d'informations sur AWS, consultez le [Document de présentation d'AWS![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://d1.awsstatic.com/whitepapers/aws-overview.pdf "Document de présentation d'AWS").

Avant de déployer des homologues {{site.data.keyword.blockchainfull_notm}} Platform for AWS, passez en revue les [considérations relatives aux homologues](remote_peer.html#remote-peer-limitations).

## Prérequis
{: #prerequisites-aws}

Pour utiliser un homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS, vous devez appartenir à une organisation qui est membre d'un réseau de blockchain hébergé sur IBM Blockchain Platform. Vous devez utiliser le Moniteur réseau sur IBM Cloud pour accéder aux données d'identification du réseau et aux noeuds finaux d'API de votre réseau. Si vous n'êtes membre d'aucun un réseau de blockchain, vous devez créer ou rejoindre un réseau. Pour plus d'informations, voir [Création d'un réseau de plan](../get_start.html#creating-a-network) ou [Rejoindre un réseau](../get_start.html#joining-a-network).

La type d'instance VPC par défaut pour l'homologue est `m4.xlarge`. Vous devez optimiser le type d'instance que vous choisissez en fonction de vos besoins en termes d'UC, de mémoire et de stockage. L'homologue nécessite au moins :  
-	2 UC
-	2 Go de RAM
-	4 Go d'espace pour le code blockchain
-	10 Go d'espace pour le registre avec possibilité d'extension.

Ces niveaux de ressources minimum sont suffisants pour les tests et l'expérimentation. Pour un environnement de production, il est important d'allouer une quantité de mémoire suffisamment importante, 100 Go par exemple. La quantité de mémoire utilisée dépend du nombre de transactions et du nombre de signatures qui sont nécessaires à partir de votre réseau. Si vous épuisez l'espace de stockage de votre homologue, vous devez <!-- either expand the storage or -->déployer un nouvel homologue avec un système de fichiers plus important et le laisser se synchroniser par l'intermédiaire de vos autres composants sur les mêmes canaux.


## Options de déploiement
{: #remote-peer-aws-deploy-options}

Le modèle de démarrage rapide fournit deux options de déploiement :

* Déployer {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un nouveau VPC (déploiement de bout en bout). Cette option permet de générer un nouvel environnement composé du VPC, de sous-réseaux, de passerelles NAT, de groupes de sécurité, d'hôtes bastion et d'autres composants d'infrastructure, puis elle déploie l'homologue dans ce nouveau VPC.

* Déployer la plateforme {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un VPC existant. Cette permet de mettre à disposition l'homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans votre infrastructure AWS existante. Il existe des modèles distincts pour ces options dans lesquels vous pouvez configurer des blocs de CIDR, des types d'instance et des paramètres d'homologue, comme décrit plus loin dans ce guide. 

## Etape 1 : Préparer votre compte AWS
{: #remote-peer-aws-account}

1. Si vous n'avez pas encore de compte AWS, créez-en un [ici ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://aws.amazon.com "https//aws/amazon.com") en suivant les instructions à l'écran.

2. Utilisez le sélecteur de région dans la barre de navigation pour choisir la région AWS dans laquelle vous souhaitez déployer l'homologue dans AWS.

3. Créez une paire de clés dans votre région préférée.

4. Si nécessaire, demandez une augmentation de la limite pour de service pour le type d'instance Amazon EC2 <type>. Vous devrez peut-être effectuer cette opération si vous disposez déjà d'un déploiement existant déjà qui utilise ce type d'instance, et que vous envisagez de dépasser la limite par défaut de ce déploiement.

## Etape 2 : Extraire les informations de configuration de l'homologue distant
{: #aws-network-endpoints}

Vous devez fournir les noeuds finaux d'API de votre réseau à votre homologue au cours de la configuration. Ces noeuds finaux permettent à un homologue de rechercher et de se connecter au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform. A l'écran **Présentation** de votre Moniteur réseau, cliquez sur le bouton **Configuration de l'homologue distant**.

![Configuration de l'homologue distant](../images/myresources_starter.png "Configuration de l'homologue distant")
*Figure 1. Panneau Configuration de l'homologue distant*

Une fenêtre en incrustation s'ouvre et affiche les valeurs des zones suivantes. Notez les valeurs des zones suivantes, elles sont nécessaires lorsque de la configuration de l'homologue à l'aide du modèle de démarrage rapide AWS.

- **MSP de l'organisation**
- **Nom de l'autorité de certification (CA)**
- **URL de l'autorité de certification (CA)**
- **Certificat TLS de l'autorité de certification (CA)**

Vous pouvez copier et coller chaque zone directement dans le modèle de démarrage rapide, ou les sauvegarder en tant que fichier JSON en cliquant sur le lien **Télécharger**. 

**Remarque :** Si vous téléchargez les information au format JSON, vous devez convertir le certificat TLS au format PEM avant de le fournir à l'homologue. Convertissez le **Certificat TLS de l'autorité de certification** dans le fichier JSON que vous avez téléchargé au format PEM en exécutant la commande :
```
echo -e "<CERT>" > admin.pem
```
{:codeblock}

Remplacez `<CERT>` par la caleur du **Certificat TLS de l'autorité de certification**. Ensuite, lorsque vous êtes invité à entrer le **Certificat TLS de l'autorité de certification** dans le modèle de démarrage rapide, `cat` le fichier admin.pem file puis copiez et collez le contenu dans la zone.  

## Etape 3 : Enregistrer un homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #aws-register-peer}

Vous devez ajouter une nouvelle identité d'homologue à votre organisation sur {{site.data.keyword.blockchainfull_notm}} Platform avant que l'homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS ne rejoigne le réseau. Procédez comme suit pour enregistrer un homologue.

**Remarque :** Pour la haute disponibilité, le modèle de démarrage rapide crée deux noeuds homologues entre deux zones de disponibilité. Par conséquent, deux ID et secrets d'inscription sont requis. **Répétez ce processus deux fois pour générer deux ID et secrets d'inscription.**

1. Connectez-vous au Moniteur réseau de votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform. A l'écran "Autorité de certification" de votre Moniteur réseau, vous pouvez voir toutes les identités qui sont enregistrées auprès du réseau, comme votre admin ou vos applications client.
  ![CA screen](../images/CA_screen_starter.png "CA screen")
  *Figure 2. Ecran de l'autorité de certification*

2. Cliquez sur le bouton **Ajouter un utilisateur** à l'écran. Un écran contextuel s'ouvre et vous permet d'enregistrer votre homologue sur le réseau après que vous avez rempli les zones suivantes. **Notez la valeur de l'ID et du Secret en vue d'un usage ultérieur lors de la configuration de votre homologue dans le modèle de démarrage rapide.**
  - **ID d'inscription :** Nom à utiliser pour votre homologue, référencé comme votre `ID d'inscription` lors de la configuration de votre homologue. **Notez cette value** en vue d'un usage ultérieur.
  - **Secret d'inscription :** Mot de passe à utiliser pour votre homologue, référencé comme votre `Secret d'inscription` lors de la configuration de votre homologue. **Notez cette valeur** en vue d'un usage ultérieur.
  - **Type :** Sélectionnez `homologue` pour cette zone.
  - **Affiliation :** Il s'agit de l'affiliation de votre organisation, `org1` par exemple, à laquelle appartient votre homologue. Vous pouvez spécifier une nouvelle affiliation ou en utiliser une existante.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si cette zone n'est pas renseignée, la valeur par défaut est un nombre illimité d'inscriptions.

  Après avoir renseigné les zones, cliquez sur **Soumettre** pour enregistrer l'homologue. L'homologue enregistré est ensuite répertorié dans le tableau en tant qu'identité sur le réseau. Par mesure de sécurité, utilisez chaque identité, ainsi que l'ID d'inscription et la valeur confidentielle qui l'accompagnent, pour déployer un seul homologue. Ne réutilisez par les ID et les mots de passe de l'homologue.

## Etape 4 : Lancer le démarrage rapide
{: #remote-peer-aws-launchqs}

Vous êtes responsable du coût des services AWS que vous utilisez lorsque vous exécutez ce déploiement de référence de démarrage rapide. Il n'y a pas de frais supplémentaires pour l'utilisation de ce démarrage rapide. Pour plus de détails, consultez les pages relatives à la tarification de chaque service AWS que vous devez utiliser dans ce démarrage rapide. Les prix sont susceptibles de changement. 

1. Choisissez l'une des options suivantes pour lancer le modèle AWS CloudFormation dans votre compte AWS. Pour obtenir de l'aide sur le choix d'une option, consultez les options de déploiement plus haut dans ce guide. La durée d'exécution de chaque déploiement est d'environ 10 minutes.  

  * [Déployer {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un nouveau VPC sur AWS ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fwd.aws/v43nk "Déployer {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un nouveau VPC sur AWS").  

  * [Déployer {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un VPC existant sur AWS ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fwd.aws/zrP4g "Déployer {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un  VPC existant sur AWS").

  **Important :**     
Si vous déployez la plateforme {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un VPC existant, assurez-vous que votre VPC comporte deux sous-réseaux publics dans différentes zones de disponibilité pour les instances de base de données. Ces sous-réseaux requièrent des passerelles NAT ou des instances NAT dans leurs tables de routage, pour autoriser les instances à télécharger des packages et des logiciels sans les exposer sur Internet. Vous aurez également besoin de l'option de nom de domaine configurée dans les options DHCP comme expliqué dans la documentation VPC Amazon.  

  Assurez-vous également de créer un groupe de sécurité lié à votre VPC existant et ajoutez des règles entrantes sur les ports 22 et 7051 à ce groupe de sécurité. Les connexions TCP sur le port 22 autorisent un accès SSH à l'instance générée alors que les connexions TCP sur le port 7051 autorisent un accès gRPC externe à l'instance homologue (nécessaire pour exploiter l'homologue à l'aide de l'interface CLI des outils Fabric et de logiciels SDK). Vous serez invité à entrer ces paramètres VPC lors du lancement du démarrage rapide.

2. Vérifiez la région qui s'affiche dans l'angle supérieur droit de la barre de navigation et modifiez-la si nécessaire. Il s'agit de l'endroit où sera créée l'infrastructure réseau de l'homologue. Le modèle est lancé dans la région Est des Etats-Unis (Ohio) par défaut.

3. Sur la page Sélectionner un modèle, conservez le paramètre par défaut pour l'URL du modèle, puis choisissez `Suivant`.

4. Sur la page d'indication des détails, modifiez le nom de la pile si nécessaire. Vérifiez les paramètres du modèle. Fournissez des valeurs pour les paramètres qui nécessitent des entrées. Pour tous les autres paramètres, vérifiez les valeurs par défaut et personnalisez-les selon vos besoins. Lorsque vous avez terminé la vérification et la personnalisation des paramètres, choisissez `Suivant`.

Dans les tableaux suivants, les paramètres sont répertoriés par catégorie et décrits séparément pour les deux options de déploiement :

  * [Paramètres pour le déploiement de  {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un nouveau VPC](#remote-peer-aws-parameters-newvpc)

  * [Paramètres pour le déploiement de {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un VPC existant](#remote-peer-aws-parameters-existvpc).

### Paramètres pour le déploiement de {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un nouveau VPC
{: #remote-peer-aws-parameters-newvpc}

Le tableau suivant répertorie les paramètres configurables du graphique AWS et leurs valeurs par défaut. Toutes les valeurs sont obligatoires.


|  Paramètre    | Description | Val. déf |
| --------------|-------------|---------|
| `Stack name` |Le nom de la pile est un identificateur qui vous aide à trouver une pile particulière à partir d'une liste de piles. Un nom de pile peut contenir uniquement des caractères alphanumériques (sensible à la casse) et des traits d'union. Il doit commencer par un caractère alphabétique et ne peut pas comporter plus de 128 caractères.| |
| | | |
| **Configuration réseau** | |
| `Availability Zones` |Les deux zones de disponibilité à utiliser pour les sous-réseaux dans le VPC. Remarque : l'ordre logique est conservé. | |
| `Allowed SSH access CIDR` | Bloc [CIDR![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC et sous-réseaux") autorisé pour un accès SSH externe aux instance d'homologue IBM Blockchain. Il peut être défini sur 0.0.0.0/0 pour autoriser l'accès depuis n'importe où (non recommandé). | |
| `PeerEndpointAccessCIDR` | Bloc [CIDR autorisé![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC et sous-réseaux") pour un accès gRPC externe aux instances d'homologue IBM Blockchain. Généralement défini sur 0.0.0.0/0 pour autoriser l'accès depuis n'importe où (non recommandé). | |
| | | |
| **Configuration Amazon EC2** | | |
| `InstanceType` | Type d'instance EC2 pour les instances homologues. | m4.xlarge |
| `KeyPairName` | Nom d'une paire de clés EC2 existante au sein de la région AWS. Vous devez la générer. | |
| | | |
|** Configuration IBM Blockchian ** | |
| `IBMBlockchainVersion` | Version IBM Blockchain à déployer. | 1.2.1 |
| `StateDatabase` | Type de base de données à utiliser pour stocker l'état blockchain. Cette sélection doit correspondre au type de base de données d'état utilisé par le reste du réseau. | CouchDB|
| `PeerVolumeSize` | Taille du volume EBS utilisé pour stocker les données persistantes (registre, base de données d'état, MSP) pour l'homologue, en Go. | 100 |
| `Peer 1 enroll ID`|  ID d'inscription que vous avez entré dans votre écran relatif à l'autorité de certification de l'interface utilisateur d'IBM Blockchain Platform pour votre premier homologue. |  |
| `Peer 1 enroll secret` |  Secret d'inscription que vous avez entré dans votre écran relatif à l'autorité de certification de l'interface utilisateur d'IBM Blockchain Platform pour votre premier homologue. | |
| `Peer 2 enroll ID` |  ID d'inscription que vous avez entré dans votre écran relatif à l'autorité de certification de l'interface utilisateur d'IBM Blockchain Platform pour votre second homologue. | |
| `Peer 2 enroll secret` |  Secret d'inscription que vous avez entré dans votre écran relatif à l'autorité de certification de l'interface utilisateur d'IBM Blockchain Platform pour votre second homologue. | |
| | | |
|**Données d'identification du service du service IBM Blockchain**| | |
| `Organization MSP` | Cette valeur peut se trouver dans l'interface utilisateur d'IBM Blockchain Platform. Cliquez sur le bouton Configuration de l'homologue distant de l'écran Présentation, puis copiez et collez ces informations ici.| |
| `Certificate Authority (CA) Name` | Cette valeur peut se trouver dans l'interface utilisateur d'IBM Blockchain Platform. Cliquez sur le bouton Configuration de l'homologue distant de l'écran Présentation, puis copiez et collez ces informations ici.| |
| `Certificate Authority (CA) URL` | Cette valeur peut se trouver dans l'interface utilisateur d'IBM Blockchain Platform. Cliquez sur le bouton Configuration de l'homologue distant de l'écran Présentation, puis copiez et collez ces informations ici, port compris. Si rien n'est indiqué, le port par défaut est 443. | |
| `Certificate Authority (CA)  TLS Certificate`| Cette valeur peut se trouver dans l'interface utilisateur d'IBM Blockchain Platform. Cliquez sur le bouton Configuration de l'homologue distant de l'écran Présentation, puis copiez et collez ces informations ici.| |
| | | |
|**Autres paramètres**| | |
| `QSS3BucketName` | Nom du compartiment S3 pour les ressources de démarrage rapide. Ce nom e compartiment peut inclure des nombres, des lettres minuscules, des lettres majuscules et des traits d'union (-). Il ne peut pas commencer ou se terminer par un trait d'union (-). | `aws-quickstart` |
| `QSS3KeyPrefix` | Préfixe de clé S3 pour les ressources de démarrage rapide. Ce préfixe de clé peut inclure des nombres, des lettres minuscules, des lettres majuscules, des traits d'union (-) et une barre oblique (/). | `quickstart-ibm-fabric/` |

1. Dans la page Options, vous pouvez spécifier des balises (paires clé-valeur) pour les ressources de votre pile et définir des options avancées. Lorsque vous avez terminé, cliquez sur Suivant.

2. Sur la page Vérifier, passez en revue et confirmez les paramètres du modèle. Sous Fonctions, cochez la case pour confirmer que le modèle créera des ressources IAM.

3. Choisissez Créer pour déployer la pile.

4. Surveillez l'état de la pile. Lorsque l'état de toutes les piles est `CREATE_COMPLETE`, le cluster homologue est prêt. Une fois l'opération terminée, vous devez avoir une pile racine avec quatre piles imbriquées si couchDB a été choisi ou deux piles imbriquées si levelDB a été choisi.

5. Utilisez les informations qui affichées sous l'onglet Sorties de la pile pour afficher les ressources qui ont été créées.


### Paramètres pour le déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} Platform dans un VPC existant
{: #remote-peer-aws-parameters-existvpc}

Si vous déployez l'homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un VPC existant, il est nécessaire de tenir compte des remarques suivantes :

 - Assurez-vous que votre VPC comporte deux sous-réseaux privés dans différentes zones de disponibilité pour les instances de base de données. Ces sous-réseaux requièrent des passerelles NAT ou des instances NAT dans leurs tables de routage, pour autoriser les instances à télécharger des packages et des logiciels sans les exposer sur Internet. 

 - Configurez l'option de nom de domaine dans les options DHCP comme décrit dans la [documentation Amazon VPC ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html "Ensembles d'options DHCP").  

- Créez un groupe de sécurité lié à votre VPC existant et ajoutez des règles entrantes sur les ports 22 et 7051 à ce groupe de sécurité. Les connexions TCP sur le port 22 autorisent un accès SSH à l'instance générée alors que les connexions TCP sur le port 7051 autorisent un accès gRPC externe à l'instance homologue (nécessaire pour exploiter l'homologue à l'aide de l'interface CLI des outils Fabric et de logiciels SDK). Vous serez invité à entrer ces paramètres VPC lors du lancement du démarrage rapide.

 Lors du déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS dans un VPC existant, les paramètres suivants remplacent les paramètres dans les sections correspondantes [ci-dessus](#remote-peer-aws-parameters-newvpc):

|  Paramètre    | Description | Val. déf |
| --------------|-------------|---------|
| **Configuration réseau** | | |
| `VPCID` |	ID de votre VPC existant pour le déploiement.| |
| `AvailabilityZone1` | Zone de disponibilité dans laquelle déployer le premier noeud homologue. | |
| `SubnetID1` |	ID du sous-réseau à utiliser pour le premier noeud homologue. Le sous-réseau doit appartenir au VPC choisi. Pour trouver les sous-réseaux appartenant au VPC, consultez votre tableau de bord VPC AWS et  sélectionnez le menu relatif aux "sous-réseaux".| |
| `AvailabilityZone2` | Zone de disponibilité dans laquelle déployer le second noeud homologue. | |
| `SubnetID2` |	ID du sous-réseau à utiliser pour le second noeud homologue. Le sous-réseau doit appartenir au VPC choisi. Pour trouver les sous-réseaux appartenant au VPC, consultez votre tableau de bord VPC AWS et  sélectionnez le menu relatif aux "sous-réseaux".| |
| | | |
| **Configuration Amazon EC2**| | |
| `InstanceType` 	| Type d'instance EC2 pour les instances homologues. | m4.xlarge |
| `KeyPairName` |	 Nom d'une paire de clés EC2 existante au sein de la région AWS. Vous devez la générer. | |
| `SecurityGroup` | Nom d'un groupe de sécurité EC2 existant au sein de la région AWS. Vous devez autoriser les connexions TCP entrantes sur les port 22 et 7051. |	| |

## Etape 5 : Tester le déploiement
{: #remote-peer-aws-test}

Lorsque le modèle AWS CloudFormation a créé la pile, deux instances homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS s'exécutent dans votre compte AWS. Ces instances seront nommées à partir d'une combinaison du `MSP de l'organisation` et de l'`ID d'inscription de l'homologue` qui est spécifié dans le modèle de démarrage rapide. Par exemple, `org1-remotepeer1`.  

![Instances AWS EC2 de l'homologue](../images/remote_peer_AWS_EC2_instances.png "Instances AWS EC2 de l'homologue")  
*Figure 3. Homologue distant sur des instances AWS EC2*

Pour vérifier que l'homologue s'exécute :

  * Lancez SSH dans vote VPC nouvellement créé en sélectionnant l'instance dans la console AWS (cliquez sur **Services > EC2 > Instances**), puis cliquez sur le bouton **Connecter**. Suivez les instructions d'AWS pour émettre la commande `ssh`.  
  * À partir de la ligne de commande, exécutez `docker ps` pour afficher les conteneurs en cours d'exécution. Chaque machine virtuelle comporte un conteneur homologue et un ecs-agent. Si vous avez sélectionné CouchDB comme base de données de registre, il y aura également un conteneur CouchDB.

  ```
  CONTAINER ID        IMAGE                                STATUS              PORTS                          NAMES
  fb3c49fe52fe        amazon/amazon-ecs-agent:latest       Created                                            ecs-agent
  667780cf3cd3        ibmblockchain/fabric-peer:1.2.1      Up                  0.0.0.0:7051->7051/tcp         peer
  2aa143c81027        ibmblockchain/fabric-couchdb:0.4.6   Up                  4369/tcp, 5984/tcp, 9100/tcp   couchdb
  ```

  * Vous pouvez créer une session shell au sein du conteneur homologue en exécutant `docker exec -it peer sh`.

Pour vérifier que la connexion à votre réseau {{site.data.keyword.blockchainfull_notm}} Platform fonctionne, vous pouvez exécuter une commande de l'interface CLI de l'homologue depuis le conteneur homologue. Exécutez la commande de l'interface CLI `peer channel fetch` pour extraire le bloc d'origine du canal :

1. Procédez à l'extraction des informations de configuration de votre `profil de connexion` disponible dans l'écran Présentation de votre Moniteur réseau. Cliquez sur **Profil de connexion**, puis sur **Télécharger**.

   - Localisez l'URL du service de tri en recherchant **orderers**, qui se trouve sous `orderers > url`. Notez l'URL qui commence par le nom réseau. L'URL est similaire à l'exemple suivant :

   ```
   ash-zbc07b.4.secure.blockchain.ibm.com:21239
   ```

   - Localisez le nom de votre organisation en recherchant **organizations**. Il doit s'agir de la même organisation que celle que vous utilisez pour enregistrez votre homologue. Vous pouvez trouver le nom de votre organisation ainsi que le `mspid` associé. Cette valeur est également disponible dans l'écran Présentation du Moniteur réseau. Cliquez sur le bouton **Configuration de l'homologue distant**. La valeur s'affiche sous `MSP de l'organisation`. Notez la valeur de `mspid`.

   - Si ce n'est déjà fait, créez une session shell au sein du conteneur homologue en exécutant `docker exec -it peer sh`.

   ```
   docker exec -it peer sh
   ```
   {:codeblock}

   - Copiez le certificat TLS des services de tri du profil de connexion vers l'homologue. Accédez à la section **orderers**. Copiez le certificate qui suit "pem:", commençant par -----BEGIN CERTIFICATE----- et se terminant par -----END CERTIFICATE-----. N'incluez pas les apostrophes. Exécutez la commande suivante depuis la ligne de commande, en remplaçant `<orderer cert>` par le contenu que vous avez copié depuis le fichier creds.json.

   ```
   echo -e "<orderer cert>" > /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem
   ```
   {:codeblock}

   Remplacez `<PEER_ENROLL_ID>` par l'ID d'inscription qui est spécifié dans le modèle de démarrage rapide et est associé à cette instance homologue.

2. Votre organisation doit être ajoutée à un canal du réseau pour que vous puissiez extraire le bloc d'origine. 

  - Vous pouvez démarrer un nouveau canal pour l'homologue. En tant qu'initiateur de canal, vous pouvez inclure automatiquement votre organisation durant la [création de canal](create_channel.html#creating-a-channel).

  - Un autre membre du réseau blockchain peut également ajouter votre organisation à un canal existant en utilisant une [mise à jour de canal](create_channel.html#updating-a-channel).

  - Une fois que votre organisation est ajoutée à un canal, vous devez ajouter le certificat signataire de votre homologue au canal. L'homologue envoie par téléchargement son certificat signataire lors de l'installation, de sorte que vous devez uniquement synchroniser le certificat pour le canal. Dans l'écran "Canaux" de votre Moniteur réseau, localisez le canal rejoint par votre organisation et sélectionnez **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Action**. Cette action synchronise les certificats entre tous les homologues sur le canal.

3. Exécutez les commandes suivantes pour définir les variables d'environnement dans le conteneur de l'homologue. 

   ```
   export ORDERER_1=<ORDERER_URL>
   export CHANNEL=<CHANNEL_NAME>
   export ORGID=<ORGANIZATION_MSP_ID>
   export PEERADDR=<PEER_ADDR>
   ```
   {:codeblock}

   Remplacez les zones par vos informations.
     - Remplacez `<ORDERER_URL>` par le nom d'hôte et le port du service de tri du fichier `creds.json`.
     - Remplacez `<CHANNEL_NAME>` par le nom du canal que va rejoindre l'homologue.
     - Remplacez `<ORGANIZATION_MSP_ID>` par le nom de l'organisation du fichier `creds.json`.
     - Remplacez `<PEER_ADDR>` par `localhost:7051`

   Par exemple :

   ```
   export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
   export CHANNEL=defaultchannel
   export ORGID=PeerOrg1
   export PEERADDR=localhost:7051
   ```

4. Exécutez la commande d'interface CLI d'homologue suivante pour extraire le bloc d'origine du canal.

   **IMPORTANT :** Dans la commande suivante, remplacez chaque occurrence de `<PEER_ENROLL_ID>` par l'ID d'inscription associé à cette instance homologue qui a été spécifiée dans le modèle de démarrage rapide. Cette valeur peut être trouvée en exécutant la commande `ls /etc/hyperledger/`. Deux dossiers doivent être répertoriés :`fabric`, et le second est votre `<PEER_ENROLL_ID>`.

   ```
   CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/<PEER_ENROLL_ID>/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem --tls
   ```
   {:codeblock}


   **Remarque :** Il est possible qu'un avertissement s'affiche lors de l'exécution de l'une de ces commandes de l'interface CLI ; vous pouvez l'ignorer.

   ```
   [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
   /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem
   ```

   Vérifiez que la commande s'est correctement exécutée et que le bloc d'origine est ajouté à votre conteneur homologue avec succès en exécutant la commande suivante : 

   ```
   ls *.block
   ```
   {:codeblock}

   Vous savez que le bloc d'origine est ajouté lorsque vous voyez quelque chose de semblable à l'exemple suivant :

   ```
   defaultchannel_0.block
   ```

   Félicitations. Votre homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS est connecté à votre réseau {{site.data.keyword.blockchainfull_notm}} Platform.

## Foire aux questions
{: #remote-peer-aws-faq}

* **Q**. Une erreur CREATE_FAILED s'est affichée lors du lancement du démarrage rapide.
* **R**. Si AWS CloudFormation ne parvient pas à créer la pile, nous vous conseillons de relancer le modèle avec Rollback on failure défini sur `No`. (Ce paramètre figure sous Advanced dans la console AWS CloudFormation, page Options.) Avec ce paramètre, l'état de la pile est maintenu et l'instance continue de s'exécuter, de manière à vous permettre de résoudre le problème. (Consultez les fichiers journaux dans `%ProgramFiles%\Amazon\EC2ConfigService` et `C:\cfn\log`.)

  - Lorsque vous définissez Rollback on failure sur `No`, des frais AWS continuent à vous être imputés pour cette pile. Veillez à supprimer la pile lorsque vous avez terminé l'identification et résolution des problèmes. Pour plus d'informations, voir  [Troubleshooting AWS CloudFormation ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html "Troubleshooting AWS CloudFormation") sur le site Web d'AWS.

* **Q**. Une erreur de limitation de taille s'est affichée lors du déploiement de modèles AWS Cloudformation.
* **R**. Nous vous recommandons de lancer les modèles de démarrage rapide depuis l'emplacement fourni ou depuis un autre compartiment S3. Si vous déployez les modèles depuis une copie locale sur votre ordinateur ou à partir d'un emplacement non S3, vous risquez de rencontrer des limitations de taille de modèle lors de la création de modèle de la pile. Pour plus d'informations sur les limites AWS CloudFormation, voir la [documentation AWS![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html "Limites AWS CloudFormation").

## Etapes suivantes
{: #whats-next-aws}

Après que vous avez configuré l'homologue dans AWS, vous pouvez effectuer quelques étapes supplémentaires avant de soumettre les transactions et lire le registre partagé du réseau de blockchain. Pour plus d'informations, voir  [Exploitation d'un homologue dans AWS](remote_peer_operate_aws.html#remote-peer-operate-aws).

## Haute disponibilité (HA)
{: #aws-high-availability}

Par défaut, pour la prise en charge de la haute disponibilité, le modèle de démarrage rapide déploie deux instances d'homologue, dans deux zones de disponibilité différentes. Pour optimiser cette prise en charge de la haute disponibilité, vous devez également configurer vos [applications client pour la haute disponibilité](../v10_application.html#ha-app).

## Remarques sur la sécurité
{: #remote-peer-aws-security}

AWS Cloud fournit une plateforme évolutive, hautement fiable qui aide les clients à déployer des applications et des données de manière rapide et sécurisée. Lorsque vous générez des systèmes sur l'infrastructures AWS, les responsabilités en matière de sécurité sont partagés entre vous et AWS. Ce modèle partagé peut réduire votre charge opérationnelle car AWS exploite, gère et contrôle les composants du système d'exploitation hôte et de la couche de virtualisation à la sécurité physique des installations dans lesquelles les services fonctionnent. Vous assumez la responsabilité et la gestion du système d'exploitation invité (y compris les mises à jour et les correctifs de sécurité), des autres applications associées, ainsi que la configuration du pare-feu du groupe de sécurité fourni par AWS. Pour plus d'informations sur la sécurité dans AWS, consultez  [AWS Cloud Security ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://aws.amazon.com/security/ "AWS Cloud Security").

### AWS Identity and Access Management (IAM)
{: #remote-peer-aws-iam}

Cette solution optimise un rôle IAM doté de moins de privilèges. Il n'est pas nécessaire ni recommandé de stocker les clés SSH, les clés secrètes, ou les clés d'accès sur les instances mises à disposition.

### Sécurité du système d'exploitation
{: #remote-peer-aws-ossecurity}

L'utilisateur racine sur les noeuds de cluster est accessible uniquement grâce à l'utilisation de la clé SSH spécifiée durant le processus de déploiement. AWS ne stocke pas ces clés SSH, de sorte que si vous perdez votre clé SSH, vous pouvez perdre l'accès à ces instances. Les correctifs de système d'exploitation sont de votre responsabilité et ils doivent être effectuées sur une base périodique.

### Groupes de sécurité 
{: #remote-peer-aws-securitygroups}

Un groupe de sécurité agit comme un pare-feu qui contrôle le trafic pour une ou plusieurs instances. Lorsque vous lancez une instance, vous pouvez associer un ou plusieurs groupes de sécurité à cette instance. Vous pouvez ajouter des règles à chaque groupe de sécurité afin d'autoriser le trafic vers ou depuis ses instances associées. Vous pouvez modifier les règles d'un groupe de sécurité à tout moment. Les nouvelles règles sont automatiquement appliquées à toutes les instances qui sont associées au groupe de sécurité. Les groupes de sécurité créés et affectés à des instances individuelles dans le cadre de cette solution sont limités autant que possible tout en autorisant l'accès aux différentes fonctions requises par l'homologue. Nous vous recommandons de passer en revue les groupes de sécurité afin de restreindre davantage l'accès autant que nécessaire une fois le cluster opérationnel.

### Sécurité des homologues
{: #aws-security}

Les homologues sont déployés à l'extérieur d'{{site.data.keyword.blockchainfull_notm}} Platform ; par conséquent, vous êtes responsable de la gestion de la sécurité de l'homologue. Cela inclut des zones de sécurité importants fournies par les réseaux de plan Enterprise, comme la gestion des clés et le chiffrement de données. Passez en revue les rubriques suivantes concernant la sécurité de vos homologues.

#### Sécurité des données 
{: #aws-security-data}

Le plan Entreprise de {{site.data.keyword.blockchainfull_notm}} Platform utilise le chiffrement de disque intégral qui repose sur la [cryptographie symétrique ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Cryptographie symétrique") pour protéger l'ensemble des données utilisées par le réseau. Vous devez suivre des étapes similaires dans votre environnement pour protéger les données de votre homologue.

Les données de votre base de données d'état, que vous utilisiez levelDB ou couchDB, ne sont pas chiffrées. Vous pouvez utiliser le chiffrement au niveau de l'application afin de protéger les données au repos dans votre base de données d'état.

#### Hébergement de données
{: #aws-security-data-residency}

L'hébergement de données indique que le traitement et le stockage de l'ensemble des données de registre de blockchain demeurent à l'intérieur des frontières d'un pays unique.
Pour plus de détails sur ce qui peut être réalisé, consultez cette [rubrique](remote_peer.html#data-residency).

#### Gestion des clés 
{: #aws-security-key-management}

La gestion des clés est un aspect essentiel de la sécurité de l'homologue. Si une clé privée est compromise ou égarée, des acteurs hostiles pourraient accéder aux données et aux fonctionnalités de votre homologue. Le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform utilise des [modules de sécurité matérielle](../glossary.html#hsm) (HSM) pour stocker les clés privées de votre réseau. HSM est un dispositif physique qui empêche des tiers d'accéder à votre clé privée.

Lorsque vous déployez un homologue distant sur AWS, vous êtes responsable de la gestion de vos clés privées. Bien qu'{{site.data.keyword.blockchainfull_notm}} Platform génère vos clés privées, ces clés ne sont pas stockées sur cette plateforme. Il est essentiel que vous stockiez vos clés dans un emplacement sécurisé afin qu'elles ne soient pas compromises. Vous pouvez trouver la clé privée de votre homologue dans le dossier du magasin de clés de votre MSP, dans le répertoire  `/etc/hyperledger/<PEER_ENROLL_ID>/msp/keystore/` au sein de votre conteneur d'homologue. Pour plus d'informations sur les certificats au sein de votre homologue distant, voir la section relative [Membership Services Provider](../certificates.html#msp) de la rubrique [Gestion des certificats sur {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

Vous pouvez utiliser Key Escrow pour récupérer des clés privées perdues. Cette opération doit être effectuée avant la perte de clés. Si une clé privée ne peut pas être récupérée, vous devez obtenir de nouvelles clés privées en obtenant un nouveau certificat SignCert auprès de votre autorité de certification. Vous devez également retirer et remplacer votre certificat admin des canaux que vous avez rejoints.

<!---
In IBP when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

#### TLS
{: #aws-security-tls}

[Transport Layer Security ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Une présentation de l'établissement de liaison SSL ou TLS") (TLS) est intégrée au modèle de confiance d'Hyperledger Fabric. Tous les composants sur {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS pour s'authentifier et communiquer entre eux. Par conséquent, les composants réseau sur {{site.data.keyword.blockchainfull_notm}} Platform doivent pouvoir effectuer un établissement de liaison TLS avec vos homologues. Ceci implique entre autres que vous devez activer le passe-système, en utilisant des listes blanches par exemple, dans le pare-feu entre vos applications client et votre homologue.


#### Configuration de Membership Service Provider (Fournisseur de services aux membres)
{: #aws-security-MSP}

Les composants d'IBM Blockchain Platform consomment des identités via des Fournisseur de services aux membres (MSP). Les MSP associent les certificats émis par les autorités de certification à des rôles de réseau et de canal. Pour plus d'informations sur la façon dont les MSP fonctionnent avec les homologues distants, consultez cette [rubrique](../certificates.html#msp).

#### Sécurité des applications
{: #aws-security-appl}

Tous les appels de code blockchain étant signés, Fabric gère la sécurité des applications. De plus, Fabric inclut également des vérifications au niveau de l'application basées sur ACL.

## Licence et tarification
{: #license-pricing-aws}

Vous devez accepter la version de licence de l'édition Community de {{site.data.keyword.blockchainfull_notm}} Platform for AWS pour pouvoir utiliser la solution de déploiement activée par le démarrage rapide. L'utilisation de  {{site.data.keyword.blockchainfull_notm}} Platform for AWS  (y compris tous les packages fournis via l'offre de démarrage rapide, et les packages dérivés de ces derniers) n'est pas destinée à des fins de production. Ibm peut décider d'annuler l'autorisation d'accès au code, et l'utilisation de ce code, à tout moment. Le contrat de licence de logiciel {{site.data.keyword.blockchainfull_notm}} Platform for AWS comporte plus de détails sur les termes de la licence. Lorsque vous lancez le démarrage rapide, vous êtes invité à lire et accepter les termes du contrat.

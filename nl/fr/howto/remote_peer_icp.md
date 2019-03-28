---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Déploiement d'homologues distants dans {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-icp}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Les instructions suivantes décrivent comment installer l'homologue distant {{site.data.keyword.blockchainfull}} Platform sur {{site.data.keyword.cloud_notm}} Private (ICP) et le connecter à un réseau sur {{site.data.keyword.blockchainfull_notm}} Platform.{:shortdesc}

{{site.data.keyword.cloud_notm}} Private est une plateforme applicative pour le développement et la gestion d'applications conteneurisées sur site. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers sur {{site.data.keyword.cloud_notm}} est un produit {{site.data.keyword.blockchainfull_notm}} qui est intégré avec ICP pour les clients qui déploient des homologues dans leur environnement local. Une fois que vous avez installé la charte Helm de l'homologue distant, vous pouvez la trouver en tant qu'application dans le catalogue ICP. L'homologue distant pour ICP optimise les services de stockage, de sécurité, de journalisation et d'assistance d'ICP pour la gestion des homologues. Pour plus d'informations sur ICP, voir la documentation de [{{site.data.keyword.cloud_notm}} Private documentation ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private").

Avant d'installer des homologues distants sur ICP, passez en revue les [considérations relatives aux homologues distants](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Prérequis
{: #prerequisites-icp}

Avant d'installer des homologues distants sur ICP vous devez suivre les étapes ci-après sur {{site.data.keyword.cloud_notm}} Private, votre système local et {{site.data.keyword.blockchainfull_notm}} Platform.

1. Votre système local

  Vous devez installer l'interface CLI de {{site.data.keyword.cloud_notm}} Private [2.1.0.3 ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "Installation de l'interface CLI de {{site.data.keyword.cloud_notm}} Private").  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} Private

  Vous avez besoin d'un cluster [{{site.data.keyword.cloud_notm}} Private ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v2.1.0.3 - Documentation") version 2.1.0.3. Si vous utilisez l'homologue distant pour le développement, le test ou l'expérimentation, vous pouvez installer [{{site.data.keyword.cloud_notm}} Private Community Edition ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "Installing {{site.data.keyword.cloud_notm}}e Private-CE") sans frais.

  **Remarques **:
    - Vous devez déterminer l'espace de stockage que votre homologue va utiliser. Si vous utilisez les paramètres par défaut, la charte Helm crée un nouveau volume persistant de 8 Gi nommé `my-data-pvc` pour les données de votre homologue. Si vous sélectionnez CouchDB comme base de données de registre, la charte Helm crée un autre volume persistant de 8 Gi nommé `statedb-pvc` pour les données de votre registre. 
    - Si vous ne voulez pas utiliser les paramètres de stockage par défaut, vérifiez qu'une *nouvelle* storageClass est définie lors de l'installation ICP ou l'administrateur système Kubernetes doit créer une storageClass avant le déploiement.
    - [Dynamic provisioning ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamic Volume Provisioning") n'est disponible que pour les noeud amd64 dans ICP. Par conséquent, si votre cluster combine à la fois des noeuds worker s390x et amd64, la mise à disposition dynamique ne peut pas être utilisée.
    - Si vous n'utilisez pas la mise à disposition dynamique, des [Volumes permanents ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes permanents") doivent être créés et configurés avec des libellés qui peuvent être utilisés pour affiner le processus de liaison PVC (Persistent Volume Claim) kubernetes. 

  En plus de la configuration matérielle ICP, l'homologue distant requiert au moins :
    - 2 vCPU
    - 2 Go de RAM
    - 4 Go d'espace pour le code blockchain
    - 10 Go d'espace pour le registre avec possibilité d'extension

3. {{site.data.keyword.blockchainfull_notm}} Platform  

  Pour utiliser un homologue distant, vous devez disposer d'une organisation qui est membre d'un réseau de blockchain hébergé sur {{site.data.keyword.blockchainfull_notm}} Platform. Vous devez utiliser le Moniteur réseau sur {{site.data.keyword.cloud_notm}} pour accéder aux données d'identification du réseau et aux noeuds finaux d'API de votre réseau. Si vous n'êtes membre d'aucun un réseau de blockchain, vous devez créer ou rejoindre un réseau. Pour plus d'informations, voir [Création d'un réseau de plan](/docs/services/blockchain/get_start.html#creating-a-network) ou [Rejoindre un réseau](/docs/services/blockchain/get_start.html#joining-a-network).

## Importation de la charte Helm dans ICP 

Le module d'homologue distant {{site.data.keyword.blockchainfull_notm}} Platform est disponible dans [Github ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer"). La charte Helm est importée dans la console ICP à partir de la ligne de commande. Depuis votre machine locale, procédez comme suit pour importer la charte Helm :

1. Téléchargez le fichier `.tgz` de la charte Helm depuis [Github ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "IBM Blockchain Remote Peer Helm Chart").<!--[Passport Advantage Online ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. Si vous n'êtes pas déjà connecté, connectez-vous à votre cluster ICP depuis l'interface CLI ICP, puis connectez-vous au registre d'image privée Docker.   
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  Dans le code, `<cluster_CA_domain>` correspond au domaine de l'autorité de certification. Si vous n'avez pas spécifié de domaine CA, la valeur par défaut est
`mycluster.icp`.

3. Depuis le répertoire où vous avez stocké le graphique Help téléchargé, exécutez la commande suivante dans l'interface CLI ICP CLI afin d'importer la charte Helm dans votre cluster ICP. Remplacez `<helm_chart_from_github>` par le nom du fichier `.tgz` que vous avez téléchargé depuis GitHub.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  Lorsque cette commande est exécutée correctement, vous pouvez voir un quelque chose de similaire à ceci :

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  La vignette de la charte Helm d'homologue distant {{site.data.keyword.blockchainfull_notm}} Platform doit maintenant être visible sur la page de catalogue ICP. Cliquez sur le bouton **Catalogue** sur la console ICP afin de vérifier que l'importation a abouti.

### Déploiement de l'homologue distant

Les étapes suivantes illustrent le processus à suivre pour déployer l'homologue distant dans ICP. Avant de configurer l'homologue distant dans ICP, extrayez certaines informations de noeud final concernant votre plate-forme {{site.data.keyword.blockchainfull_notm}} et créez une identité utilisateur pour l'homologue distant. Vous devrez utiliser ces informations lors des étapes ultérieures.

## Etape 1 : Extraire les informations de configuration de l'homologue distant
{: #network-endpoints}

Vous devez fournir les noeuds finaux d'API de votre réseau à votre homologue distant au cours de la configuration. Ces noeuds finaux permettent à un homologue distant de rechercher et de se connecter au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform. A l'écran **Présentation** de votre Moniteur réseau, cliquez sur le bouton **Configuration de l'homologue distant**.

![Configuration de l'homologue distant](../images/myresources_starter.png "Configuration de l'homologue distant")
*Figure 1. Configuration de l'homologue distant*

Une fenêtre en incrustation s'ouvre et affiche les valeurs des zones suivantes. Sauvegardez ces valeurs lorsque vous configurez l'homologue distant.

  - **ID réseau**
  - **MSP de l'organisation**
  - **Nom de l'autorité de certification (CA)**
  - **URL de l'autorité de certification (CA)**
  - **Certificat TLS de l'autorité de certification (CA)**

Vous pouvez copier et coller les zones individuellement, ou les sauvegarder en tant que fichier JSON en cliquant sur le lien **Télécharger**. **Remarque **: Si vous téléchargez les information au format JSON, vous devez convertir le certificat TLS au format PEM avant de le fournir à l'homologue distant. Vous pouvez utiliser l'interface CLI pour convertir le certificat au format PEM en exécutant la commande `echo -e "<CERT>" > catls.pem`.

## Etape 2 : Enregistrer un homologue distant
{: #register-peer}

Vous devez ajouter une nouvelle identité d'homologue à votre organisation sur {{site.data.keyword.blockchainfull_notm}} Platform avant que l'homologue distant ne rejoigne le réseau. Procédez comme suit pour inscrire et enregistrer un homologue distant.

1. Connectez-vous à {{site.data.keyword.blockchainfull_notm}} Platform et accédez au Moniteur réseau. A l'écran "Autorité de certification", vous pouvez voir toutes les identités qui sont enregistrées auprès de votre organisation, comme votre admin ou vos applications client.
  ![CA screen](../images/CA_screen_starter.png "CA screen")
  *Figure 2. Ecran de l'autorité de certification*

2. Cliquez sur le bouton **Ajouter un utilisateur** en haut de l'écran. Un écran contextuel s'ouvre et vous permet d'enregistrer votre homologue distant après que vous avez remplis les zones suivantes. Sauvegardez les valeurs ID et Valeur confidentielle car vous en aurez besoin lors de la configuration de votre homologue.
  - **ID :** Nom de votre homologue, référencé comme votre `ID d'inscription` lors de la configuration de votre homologue. **Sauvegardez cette valeur** en vue d'une utilisation future lors du déploiement de l'homologue distant.
  - **Valeur confidentielle :** Mot de passe de votre homologue, référencé comme votre `Valeur confidentielle d'inscription` lors de la configuration de votre homologue. **Sauvegardez cette valeur** en vue d'une utilisation future lors du déploiement de l'homologue distant.
  - **Type :** Sélectionnez `homologue` pour cette zone.
  - **Affiliation :** Il s'agit de l'affiliation de votre organisation, `org1` par exemple, à laquelle appartiendra votre homologue distant. Sélectionnez une affiliation existante dans la liste déroulante ou entrez-en une nouvelle.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si cette zone n'est pas renseignée, la valeur par défaut est un nombre illimité d'inscriptions.

  Après avoir renseigné les zones, cliquez sur **Soumettre** pour enregistrer l'homologue distant. L'homologue enregistré est ensuite répertorié dans le tableau en tant qu'identité dans votre organisation.

## Etape 3 : Configurer et installer un homologue distant

Une fois que vous avez enregistré l'identité d'homologue dans le Moniteur réseau, vous pouvez configurer et installer votre homologue distant dans ICP en utilisant les instructions de console ICP ci-dessous.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. Connectez-vous à la console ICP et cliquez sur le lien **Catalogue** dans l'angle supérieur droit.
2. Recherchez la charte Helm de l'homologue distant {{site.data.keyword.blockchainfull_notm}} Platform et cliquez dessus afin de l'ouvrir. Vous pouvez voir un fichier readme qui contient des informations sur l'installation et la configuration de la charte Helm.
3. Cliquez sur **Configurer**. Sur la page Configuration, complétez les paramètres de configuration de la charte Helm et confirmer que vous avez lu et accepté le contrat de licence.
4. Cliquez sur **Installer**.

### Paramètres de configuration ;
{: #icp-configuration-parms}

Vous devez fournir les informations dont a besoin votre homologue distant pour se connecter au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform.
Le tableau suivant répertorie les paramètres configurables du graphique d'homologue distant {{site.data.keyword.blockchainfull_notm}} Platform et leurs valeurs par défaut.


|  Paramètre     | Description    | Val. déf | Requis |
| --------------|-----------------|-------|------- |
|**Configuration** | **Configuration du nom de l'édition et de l'espace de nom cible pour votre homologue distant** |  ||
| `Release name`| Nom de votre édition Helm. | none | yes |
| `Target namespace`| Espace de nom Kubernetes. | default | yes |
| | | | |
|**Cluster configuration** |**Informations de configuration de cluster** | ||
| `Worker node architecture`| Sélection de l'architecture de votre plateforme cloud (amd64 ou S390x).| Architecture détectée automatiquement en fonction de votre noeud principal| yes |
| `Image`| Chemin d'accès à la charte Helm. | Renseigné automatiquement par le chemin installé, ne modifiez pas cette valeur | yes |
| | | | |
|**Blockchain network** | **Informations de configuration réseau qui sont nécessaires pour l'homologue distant**| | |
| `Network ID`| Valeur de l'ID réseau trouvée sur le Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. Cliquez sur le bouton `Configuration de l'homologue distant` de l'écran "Présentation", puis copiez et collez ces informations ici.| none | yes |
| `Organization MSP`| Valeur de l'ID MSP trouvé sur le Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. Cliquez sur le bouton `Configuration de l'homologue distant` de l'écran "Présentation", puis copiez et collez ces informations ici.|none|yes|
| `Certificate Authority (CA) Name`| Valeur du nom de l'autorité de certification trouvé sur le Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. Cliquez sur le bouton `Configuration de l'homologue distant` de l'écran "Présentation", puis copiez et collez ces informations ici.|none|yes|
| `Certificate Authority (CA) URL`| Valeur de l'URL de l'autorité de certification trouvée sur le Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. Cliquez sur le bouton `Configuration de l'homologue distant` de l'écran "Présentation", puis copiez et collez ces informations ici.| none | yes |
| `Certificate Authority (CA) TLS Certificate` | Chaîne de certificats TLS de l'autorité de certification sur le Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. Cliquez sur le bouton `Configuration de l'homologue distant` de l'écran "Présentation", puis copiez et collez ces informations ici.| none | yes |
| | | | |
|**Remote peer identity** | **ID d'inscription et valeur confidentielle utilisés pour enregistrer votre homologue distant**| | |
| `Peer enroll ID`| ID d'inscription que vous avez entré dans l'écran "Autorité de certification" du Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. | none | yes |
| `Peer enroll secret`| Valeur confidentielle d'inscription que vous avez entrée dans l'écran "Autorité de certification" du Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. | none | yes |
| | | | |
|**Remote peer database** | **Type de base de données du registre**| | |
| `Ledger database`| [Base de données d'état](/docs/services/blockchain/glossary.html#state-database) utilisée pour le stockage de votre registre de canal. L'homologue distant doit utiliser la même base de données que votre [réseau de blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | none | yes |
| `CouchDB image`| S'applique uniquement si CouchDB a été sélectionné comme base de données de registre. | Renseigné automatiquement par le chemin installé, ne modifiez pas cette valeur. | yes |
|**Data persistence** | Activité de la capacité de persistance des données après un redémarrage ou une défaillance du cluster. Voir la section relative au [stockage dans Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/ "Volumes") pour plus de détails. | | |
| `Data persistence enabled`| Des données d'état seront disponibles au redémarrage du conteneur. *Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod.* | checked | no |
| `Use dynamic provisioning`| Utilisation de la mémoire dynamique Kubernetes. | checked | no |
| | | | |
|**Persistent volume configuration** | **Réservation de volume persistant que doit utiliser votre homologue** |  |  |
| `Persistent volume claim`| Saisie d'un nom pour votre Réservation de volume persistant (PVC) qui va être créée. | my-data-pvc | no |
| `Storage class name`| Choix du nom de la classe de stockage. | Vide si création d'une nouvelle Réservation de volume persistant ; sinon, indiquez la classe de stockage qui est associée à la Réservation de volume persistant existante. | no |
| `Existing volume claim`| Indication du nom d'une Réservation de volume persistant existante et toutes les autres zones sont laissées vides. | new claim name | no |
| `Storage access mode`| Indication du [mode d'accès![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour la Réservation de volume persistant.  | ReadOnlyMany| no |
| `Selector label`| [Libellé de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs ") pour votre Réservation de volume persistant.| default | no |
| `Selector value`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs") pour votre Réservation de volume persistant. | default | no |
| `Volume claim size`| Taille de la Réservation de volume doit être supérieure à 2 Gi. | 8Gi  | yes |
| | | | |
|**State database data persistence** | **Données de persistance après redémarrage ou défaillance de votre conteneur. Voir la section relative au [stockage dans Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/ "Volumes") pour plus de détails** | | |
| `Data persistence enabled`| Des données de registre seront disponibles au redémarrage du conteneur. *Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod.* | checked | no |
| `Use dynamic provisioning`| Utilisation de la mémoire dynamique Kubernetes. | checked | no |
| | | | |
|**State database persistent volume configuration** |**Réservation de volume persistant qui doit être utilisée pour la base de données d'état. Voir la section relative au [stockage dans Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/ "Volumes") pour plus de détails.** |||
| `Persistent volume claim`| Saisie d'un nom ou utilisation du nom par défaut pour votre nouvelle Réservation de volume persistant CouchDB qui va être créée. | statedb-pvc | no |
| `State database storage class name`| Choix du nom de la classe de stockage. | none | no |
| `State database existing volume claim`| Pour utiliser une Réservation de volume persistant existante, entrez son non et laissez toutes les autres zones vides. | none | no |
| `State database storage access mode`| Indication du [mode d'accès![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour la Réservation de volume persistant.  | ReadOnlyMany| no |
| `Selector label`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs") de votre Réservation de volume persistant. | default | no |
| `Selector value`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs") de votre Réservation de volume persistant.| default | no |
| `State database volume claim size`| Taille de la Réservation de volume persistant. | 8Gi | no |
| | | | |
| **Peer resources** | **UC et mémoire min et max pour le conteneur homologue** | | |
| `Peer CPU request` | Nombre minimum d'UC à allouer à l'homologue. | 2 | yes |
| `Peer CPU limit` | Nombre maximum d'UC à allouer à l'homologue. | 2 | yes |
| `Peer Memory request` | Quantité minimum de mémoire à allouer à l'homologue. | 4Gi | yes |
| `Peer Memory limit` | Quantité maximum de mémoire à allouer à l'homologue. | 4Gi | yes |
| | | | |
|**CouchDB Resources** | **UC et mémoire min et max allouées au conteneur CouchDB. Cette section s'applique uniquement si CouchDB a été sélectionné comme base de données d'état **| | | |
| `CouchDB CPU request` | Nombre minimum d'UC à allouer à CouchDB.| 2 | yes |
| `CouchDB CPU limit` | Nombre maximum d'UC à allouer à CouchDB. | 2 | yes |
| `CouchDB Memory request` | Quantité minimum de mémoire à allouer à CouchDB. | 4Gi | yes |
| `CouchDB Memory limit` | Quantité maximum de mémoire à allouer à CouchDB. | 4Gi | yes |

Lorsque CouchDB est sélectionnée comme base de données de l'homologue distant, deux conteneurs sont créés dans le pod, l'un pour l'homologue et l'autre pour CouchDB.
Le conteneur homologue inclut un seul montage de volume pour la Réservation de volume persistant de l'homologue qui stocke les blocs et les transactions sur le système de fichiers. Le conteneur CouchDB monte la Réservation de volume persistant de la base de données d'état de l'homologue qui contient les données de registre.

Lorsque LevelDB est sélectionnée comme base de données de l'homologue distant, un seul conteneur est créé dans le pod pour l'exécution de l'homologue et de LevelDB.
Ce conteneur comporte deux montages de volume, l'un pour la Réservation de volume persistant de l'homologue et le second montage de volume est pour la Réservation de volume persistant de la base de donnée d'état de l'homologue qui contient les données de registre .

| Sélect. BDD homologue distant   | Contenu du conteneur 1    | Contenu du conteneur 2    |
| --------------|-----------------|---------------|
| CouchDB | Homologue distant qui monte la Réservation de volume persistant de l'homologue | CouchDB qui monte la Réservation de volume persistant de la base de données d'état |
| LevelDB | Homologue distant et LevelDB qui montent la Réservation de volume persistant de l'homologue et la Réservation de volume persistant de la base de données d'état | n/a |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](/docs/services/blockchain/howto/remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  Cette commande génère le fichier `peer-cert.pem`, qui est le certificat TLS de votre homologue. 

--->

## Etape 4 : Vérifier l'installation de l'homologue distant
{: #verify-installation-icp}

Après avoir installé l'homologue distant, vous pouvez consulter le journal de l'homologue afin de vérifier si l'installation de votre homologue a abouti.

1. Après avoir cliqué sur **Installer**, cliquez sur le bouton **Afficher l'édition Helm** dans la fenêtre en incrustation. Faites défiler vers le bas jusqu'à la section **Pods**. Lorsque l'état de Pods indique **En cours d'exécution**, vous pouvez trouver l'instance de votre homologue distant sous la section **Déploiements**.

2. Sur la console ICP, cliquez sur l'icône **Menu** dans l'angle supérieur gauche. Dans la barre de menus, cliquez sur **Charges de travail** > **Déploiements**. Dans le tableau de déploiement, cliquez sur l'instance d'homologue distant que vous créez et l'écran de présentation du déploiement s'affiche. Cliquez sur l'onglet **Journaux** pour consulter les journaux de votre homologue distant. Vous pouvez entrer ka chaîne `Démarré` dans la zone de recherche.

  - Si votre homologue distant a bien démarré, vous pouvez voir des journaux semblables à l'exemple suivant :
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - Si vous ne voyez aucun journal à l'écran, votre homologue distant n'a pas démarré. Pour voir d'autres journaux et identifier le problème, cliquez sur l'icône **Menu** dans l'angle supérieur gauche et cliquez sur **Charges de travail** > **Editions Helm**. Cliquez sur votre édition Helm afin de l'ouvrir. Cliquez sur le lien **Afficher les journaux** en regard du **Pod** associé afin voir des journaux de graphique supplémentaires dans l'interface Kibana.

3. Pour vérifier que l'homologue distant peut se connecter au réseau d'{{site.data.keyword.blockchainfull}} Platform, vous pouvez
exécuter une commande de l'interface CLI au sein du conteneur de l'homologue distant. Exécutez la commande d'interface CLI `peer channel fetch` pour extraire le bloc d'origine d'un canal :

   1. Si vous n'êtes pas déjà connecté à votre cluster ICP, suivez ces [instructions](/docs/services/blockchain/howto/remote_peer_operate_icp.html#remote-peer-kubectl-configure) pour vous y connecter et utilisez l'interface CLI au sein du conteneur de l'homologue.

   2. Votre organisation doit être ajoutée à un canal du réseau pour que vous puissiez extraire le bloc d'origine.

     - Vous pouvez démarrer un nouveau canal pour l'homologue distant. En tant qu'initiateur de canal, vous pouvez inclure automatiquement votre organisation durant la [création de canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel). Notez que vous devez disposer d'au moins un homologue sur {{site.data.keyword.blockchainfull_notm}} Platform pour pouvoir créer un canal dans le Moniteur réseau.  

     - Un autre membre du réseau blockchain peut également ajouter votre organisation à un canal existant en utilisant une [mise à jour de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). Un membre du canal avec des homologues sur {{site.data.keyword.blockchainfull_notm}} Platform peut utiliser le Moniteur réseau pour ajouter votre organisation au canal même si vous n'hébergez aucun homologue sur la plateforme.
      L'homologue distant envoie par téléchargement son certificat signataire lors de l'installation, de sorte que vous devez uniquement synchroniser le certificat pour le canal. Dans l'écran "Canaux" de votre Moniteur réseau, localisez le canal rejoint par votre organisation et sélectionnez **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Action**. Cette action synchronise les certificats entre tous les homologues sur le canal.

   3. Extrayez les informations de configuration de votre fichier `creds.json` disponible à l'écran "Présentation" de votre Moniteur réseau. Cliquez sur **Profil de connexion**, puis sur **Télécharger**.

     - Localisez l'URL du programme de tri en recherchant **orderers**, qui se trouve sous `orderers > url`. Notez l'URL qui commence par le nom réseau. L'URL est similaire à l'exemple suivant :

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - Localisez le nom de votre organisation en recherchant **organizations**. Il doit s'agir de la même organisation que celle que vous utilisez pour enregistrez votre homologue distant. Vous pouvez trouver le nom de votre organisation ainsi que le `mspid` associé. Notez la valeur de `mspid`.

    4. Exécutez les commandes suivantes pour définir les variables d'environnement dans le conteneur de l'homologue : 

       ```
       export ORDERER_1=<ORDERER_URL>
       export CHANNEL=<CHANNEL_NAME>
       export CC_NAME=<CC_NAME>
       export ORGID=<ORGANIZATION_MSP_ID>
       export PEERADDR=<PEER_ADDR>
       ```
       {:codeblock}

       Remplacez les zones par vos informations.
         - Remplacez `<ORDERER_URL>` par le nom d'hôte et le port du programme de tri du fichier `creds.json`.
         - Remplacez `<CHANNEL_NAME>` par le nom du canal que rejoint l'homologue.
         - Remplacez `<CC_NAME>` par un nom pour faire référence à votre code blockchain. 
         - Remplacez `<ORGANIZATION_MSP_ID>` par le nom de l'organisation du fichier `creds.json`.
         - Remplacez `<PEER_ADDR>` par `localhost:7051`

       Par exemple :

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. Exécutez la commande d'interface CLI d'homologue suivante pour extraire le bloc d'origine du canal.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **Remarque :** Il est possible que lors de l'exécution de l'une de ces commandes de l'interface CLI, vous obteniez l'avertissement suivant qui peut être ignoré.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
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

## Etapes suivantes
{: #whats-next-icp}

Après que vous avez configuré l'homologue distant dans ICP, vous pouvez effectuer quelques étapes supplémentaires avant de soumettre les transactions et lire le registre partagé du réseau de blockchain. Pour plus d'informations, voir [Exploitation d'un homologue distant](/docs/services/blockchain/howto/remote_peer_operate_icp.html).

## Haute disponibilité
{: #high-availability}

Comme pratique optimale pour haute disponibilité, nous vous recommandons fortement de déployer au moins deux homologues par organisation.

Pour déployer des homologues distants à haute disponibilité dans ICP, procédez comme suit :

1. Passez en revue la section relative à la [configuration de votre cluster ICP pour la haute disponibilité![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "High availability {{site.data.keyword.cloud_notm}} Private clusters").

2. Installez l'homologue plusieurs fois sur votre cluster afin de déployer des homologues sur différents noeuds worker. La charte Helm contient une règle anti-affinité qui repose sur votre ID réseau et votre OrgID. Le cluster ICP détecte si vos homologues se connectent au même réseau de la même organisation, et il essaie d'organiser ces homologues sur différents noeuds worker.  
  **Note:** Although the policy "prefers" to put peers on different worker nodes, it doesn't mandate it. Par exemple, si vous avez un seul noeud worker, tous vos homologues sont déployés sur ce même noeud.

Si vous déployez au moins deux homologues, vous devez également configurer vos [applications pour la haute disponibilité](/docs/services/blockchain/v10_application.html#ha-app).

## Remarques concernant la sécurité
{: #icp-security}

Les homologues distants sont déployés à l'extérieur d'{{site.data.keyword.blockchainfull}} Platform ; par conséquent, vous êtes responsable de la gestion de la sécurité de l'homologue distant. Cela inclut des zones de sécurité importants fournies par les réseaux de plan Enterprise, comme la gestion des clés et le chiffrement de données. Passez en revue les rubriques suivantes concernant la sécurité de vos homologues distants.

### Identité et contrôle d'accès
{: #icp-security-node}

A l'extérieur de {{site.data.keyword.cloud_notm}}, vous devez configurer ka gestion des identités, l'authentification et le contrôle d'accès pour les utilisateurs qui interagissent avec les homologues distants. Vous pouvez utiliser la [gestion des utilisateurs ICP![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "Security") pour configurer ces fonctionnalités

### Sécurité des données
{: #icp-security-data}

Le plan Enterprise d'{{site.data.keyword.blockchainfull}} Platform utilise le chiffrement de disque intégral qui repose sur la [cryptographie symétrique ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Cryptographie symétrique") pour protéger l'ensemble des données utilisées par le réseau. Vous devez suivre des étapes similaires dans votre environnement pour protéger les données de votre homologue distant.

### Hébergement des données
{: #icp-data-residency}

L'hébergement de données indique que le traitement et le stockage de l'ensemble des données de registre de blockchain demeurent à l'intérieur des frontières d'un pays unique.
Pour plus de détails sur la mise en oeuvre de l'hébergement de données, voir [Hébergement de données](/docs/services/blockchain/howto/remote_peer.html#data-residency).

### Gestion des clés
{: #icp-security-key-management}

La gestion des clés est un aspect essentiel de la sécurité de l'homologue distant. Si une clé privée est compromise ou égarée, des acteurs hostiles pourraient accéder aux données et aux fonctionnalités de votre homologue distant. Le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform utilise des [modules de sécurité matérielle](/docs/services/blockchain/glossary.html#hsm) (HSM) pour stocker les clés privées de votre réseau. HSM est un dispositif physique qui empêche des tiers d'accéder à votre clé privée.

Lorsque vous déployez un homologue distant sur ICP, vous êtes responsable de la gestion de vos clés privées. Bien qu'{{site.data.keyword.blockchainfull_notm}} Platform génère vos clés privées, ces clés ne sont pas stockées sur cette plateforme. Il est essentiel que vous stockiez vos clés dans un emplacement sécurisé afin qu'elles ne soient pas compromises. Vous pouvez trouver la clé privée de votre homologue distant dans le dossier du magasin de clés de votre MSP, dans le répertoire `/mnt/crypto/peer/peer/msp/keystore/` au sein de votre conteneur d'homologue. Pour plus d'informations sur les certificats au sein de votre homologue distant, voir la section relative au [fournisseur de services d'appartenance](/docs/services/blockchain/certificates.html#msp) du tutoriel [Gestion des certificats sur {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

Vous pouvez utiliser Key Escrow pour récupérer des clés privées perdues. Cette opération doit être effectuée avant la perte de clés. Si une clé privée ne peut pas être récupérée, vous devez obtenir de nouvelles clés privées en enregistrant une nouvelle identité avec votre autorité de certification. Vous devez également retirer et remplacer votre signCert des canaux que vous avez rejoints.

<!---
Dans IBP, lorsqu'une clé privée est créée, deux ensembles de matériel de clé indépendant sont générés, sous la responsabilité de deux entités différentes. Ces deux ensembles sont ensuite combinés pour créer la clé privée.
--->

### TLS
{: #icp-security-tls}

[Transport Layer Security ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Une présentation de l'établissement de liaison SSL ou TLS") (TLS) est intégrée au modèle de confiance d'Hyperledger Fabric. Tous les composants sur {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS pour s'authentifier et communiquer entre eux. Par conséquent, les composants réseau sur {{site.data.keyword.blockchainfull_notm}} Platform doivent pouvoir effectuer un établissement de liaison TLS avec vos homologues distants. Ceci implique entre autres que vous devez activer le passe-système, en utilisant des listes blanches par exemple, dans le pare-feu Web entre vos applications client et votre homologue.

Vous pouvez utiliser TLS mutuel, qui requiert une authentification bilatérale (serveur-client) et non unilatérale (serveur uniquement), pour sécuriser la communication entre votre application et les réseaux du plan Enterprise. Vous pouvez utiliser le Moniteur réseau [pour activer TLS mutuel](/docs/services/blockchain/v10_dashboard.html#mutual-tls) pour les homologues sur {{site.data.keyword.blockchainfull_notm}} Platform. Pour activer TLS mutuel sur votre homologue distant, suivez les instructions relatives à la [l'activation de TLS mutuel pour les noeuds homologues![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Sécurisation des communications avec TLS") dans la documentation Hyperledger Fabric. Il est vivement recommandé d'activer TLS mutuel pour vos applications.

### Configuration de Membership Service Provider (Fournisseur de services aux membres) 
{: #icp-security-MSP}

Les composants d'{{site.data.keyword.blockchainfull_notm}} Platform consomment des identités via des Fournisseur de services aux membres (MSP). Les MSP associent les certificats émis par les autorités de certification à des rôles de réseau et de canal. Pour plus d'informations sur la façon dont les MSP fonctionnent avec les homologues distants, voir [Membership Service Providers (MSP)](/docs/services/blockchain/certificates.html#msp).

### Sécurité des applications
{: #icp-security-appl}

Tous les appels de code blockchain étant signés, Fabric gère la sécurité des applications. De plus, Fabric inclut également des vérifications au niveau de l'application basées sur ACL.

## Licence et tarification
{: #license-pricing-icp}

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer sur {{site.data.keyword.cloud_notm}} Private est une édition bêta gratuite qui convient pour une évaluation et une expérimentation et elle est déployée en tant qu'application ICP. Vous pouvez accéder au [module Github ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and-->et télécharger la [charte Helm ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "Charte Helm {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer").

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

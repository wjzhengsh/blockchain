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

# Déploiement d'homologues dans {{site.data.keyword.cloud_notm}} Private
{: #peer-icp}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Les instructions suivantes décrivent comment déployer un homologue {{site.data.keyword.blockchainfull}} Platform peer on {{site.data.keyword.cloud_notm}} Private (ICP). Ces instructions vous permettent de vous connecter à une plateforme {{site.data.keyword.blockchainfull_notm}} sur ICP. Si vous voulez connecter un homologue à un réseau Starter Plan ou Enterprise Plan sur {{site.data.keyword.cloud_notm}}, voir [Déploiement d'homologues pour la connexion à un réseau Starter Plan ou Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html).
{:shortdesc}

Avant de déployer un homologue, passez en revue la section [Considérations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

## Ressources obligatoires
{: #peer-resources-required}

Vérifiez que votre système ICP respecte les exigences de ressources matérielle minimum :

| Composant                 | vCPU | RAM   | Disque pour le stockage de données   |
|-----------|------|-----|-----------------------|
| Homologue                 | 2    | 2 Go  | 50 Go avec possibilité d'extension   |
| CouchDB for Peer          | 2    | 2 Go  | 50 Go avec possibilité d'extension   |

 **Remarques :**
 - Une unité vCPU est un coeur virtuel qui est affecté à une machine virtuelle ou à un coeur de processeur physique si le serveur n'est pas partitionné pour les machines virtuelles. Vous devez tenir compte des exigences vCPU lorsque vous décidez d'utiliser le coeur de processeur virtuel (VPC) pour votre déploiement dans ICP. VPC est une unité de mesure pour déterminer les coûts de licences des produits IBM. Pour plus d'informations sur les scénarios VPC, voir[Virtual processor core (VPC) ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Ces niveaux de ressources minimum sont suffisants pour les tests et l'expérimentation. Pour un environnement avec un gros volume de transactions, il est important d'allouer une quantité de mémoire suffisamment important ; 250 Go pour votre homologue par  exemple. La quantité de stockage à utiliser dépend du nombre de transactions et du nombre de signatures qui sont nécessaires depuis votre réseau. Si vous êtes sur le point d'épuiser la mémoire de votre homologue ou service de tri, vous devez déployer un nouvel homologue ou service de tri avec un système de fichiers plus important et le laisser se synchroniser par l'intermédiaire de vos autres composants sur les mêmes canaux.

## Stockage
{: #storage}

Vous devez déterminer l'espace de stockage que votre homologue va utiliser. Si vous utilisez les paramètres par défaut, la charte Helm crée un nouveau volume persistant de 8 Gi nommé `my-data-pvc` pour les données de votre homologue, et un autre volume persistant de 8 Gi nommé `statedb-pvc` pour votre base de données d'état. 

Si vous ne voulez pas utiliser les paramètres de stockage par défaut, vérifiez qu'une *nouvelle* `storageClass` est définie lors de l'installation ICP ou l'administrateur système Kubernetes doit créer une storageClass avant le déploiement.

Vous pouvez choisir de déployer les homologues sur les plateformes amd64 ou s390x. Toutefois, vous devez savoir que la [mise à disposition dynamique](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) est uniquement disponible pour les noeuds amd64 dans ICP. Si votre cluster combine à la fois des noeuds worker s390x et amd64, la mise à disposition dynamique ne peut pas être utilisée.

Si vous n'utilisez pas la mise à disposition dynamique, des[Volumes permanents ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) doivent être créés et configurés avec des libellés qui peuvent être utilisés pour affiner le processus de définition des accès Kubernetes PVC.

## Prérequis pour le déploiement d'un homologue
{: #prerequisites-peer-icp}

1. Avant d'installer un homologue sur ICP, vous devez [installer ICP](/docs/services/blockchain/ICP_setup.html) et [installer la Charte Helm de {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html).

2. Si vous utilisez Community Edition et souhaitez exécuter cette charte Helm sur un cluster ICP sans connectivité Internet, vous devez créer des archives sur une machine connectée à Internet avant d'installer les archives sur votre cluster ICP. Pour plus d'informations, voir [Adding featured applications to clusters without Internet connectivity ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Remarque : Vous pouvez trouver le fichier de spécification `manifest.yaml` sous `ibm-blockchain-platform-dev/ibm_cloud_pak` dans la charte Helm.

3. Vous devez d'abord [déployer une autorité de certification](/docs/services/blockchain/howto/CA_deploy_icp.html) sur ICP. Vous devez utiliser l'autorité de certification pour créer un [fichier de configuration homologue et le stocker en tant que secret Kubernetes dans ICP](#peer-config-file).

4. Procédez à l'extraction de la valeur de l'adresse IP proxy du cluster de votre autorité de certification depuis la console ICP. **Remarque :** Vous devrez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Cluster administrator roles and actions") pour accéder à votre IP de proxy. Connectez-vous au cluster ICP. Dans le panneau de navigation gauche, cliquez sur **Plateforme** puis sur **Noeuds** pour afficher les noeuds qui sont définis dans le cluster. Cliquez sur le noeud avec le rôle `proxy`, puis copiez la valeur de l'`IP hôte` de la table. **Important :** Conservez cette valeur car vous allez l'utiliser lors de la configuration de la zone `Adresse IP du proxy` de la charte Helm.


## Création du secret de configuration de l'homologue
{: #peer-config-file}

Pour déployer un homologue, vous devez créer un fichier de configuration qui contient d'importantes informations concernant l'identité de l'homologue et votre autorité de certification. Vous devez ensuite transmettre ce fichier à la charte Helm  pendant la configuration à l'aide d'un objet [Valeur confidentielle de Kubernetes![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/configuration/secret/). Ce fichier permettra à votre homologue d'obtenir les certificats dont il a besoin auprès de l'autorité de certification pour rejoindre un réseau de blockchain. Il contient également un certificat admin qui vous permettra d'exploiter votre homologue. Suivez les instructions relatives à l'[utilisation de l'autorité de certification pour déployer un service de tri ou un homologue](/docs/services/blockchain/howto/CA_operate.html#deploy-orderer-peer) avant la configuration de l'homologue.

Vous devez fournir les noms d'hôte CSR dans le fichier de configuration. Cela inclut le `nom d'hôte de service` qui sera la même valeur que le `nom d'édition Helm` que vous indiquez durant le déploiement. Par exemple, si vous indiquez le `nom d'édition Helm` `org1peer1`, pensez à insérer la valeur suivante dans la section `"csr"` du fichier :
```
"csr": {
  "hosts": [
     "9.42.134.44",
    "org1peer1"
  ]
}
```
{:codeblock}

Une fois le fichier de configuration sauvegardé, vous devez l'encoder dans le format base64 avant de le fournir à ICP en tant qu'objet secret. Un  secret Kubernetes vous permet de protéger et de partager des informations sans avoir à les transmettre directement au déploiement.

1. Pour encoder le fichier de configuration au format base64, exécutez les commandes suivantes dans une fenêtre de terminal :
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <config_file> | base64 $FLAG
```
{:codeblock}

**Remarque :** Il est important que la chaîne générée par la commande ci-dessus soit mise en forme sur une seule ligne. Elle doit ressembler à ceci :

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```

Mais pas à ceci :

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
```

Sauvegardez la sortie obtenue à l'étape 4 ci-dessous.

2. Connectez-vous à la console ICP. Dans le panneau de navigation gauche, cliquez sur **Configuration** puis sur **Secrets**. Cliquez sur le bouton **Créer Secret** pour ouvrir un panneau en incrustation qui vous permet de générer un nouvel objet secret.

3. Sous l'onglet **Général**, remplissez les zones suivantes :
  - **Nom :** Donnez un nom unique à votre secret au sein de votre cluster. Vous utiliserez ce nom lors du  déploiement de votre homologue. Il doit être entièrement en minuscules.  
  **Remarque :** Lorsque vous déployez un homologue, un nouveau secret est automatiquement généré par le déploiement sous le nom `<helm_release_name>-secret`. Par conséquent, lorsque vous nommez votre secret, assurez-vous que son nom soit  différent de `<helm_release_name>-secret` . Sinon, le déploiement de la charte Help échouera car le secret qu'il essaie de créer existe déjà.
  - **Espace de nom :** espace de nom pour l'ajout de votre secret. Sélectionnez l'`espace de nom` dans lequel vous voulez déployer votre homologue.
  - **Type :** entrez la valeur `Opaque`.

4. Cliquez sur l'onglet **Données** dans le panneau de navigation gauche de cette fenêtre. 
  - Dans les zones `Nom`, indiquez `secret.json` et dans la zone de valeur collez la chaîne encodée `base64` de votre fichier de configuration.

5. (Facultatif) Si vous prévoyez d'utiliser `CouchDB` comme base de données d'état, vous devez ajouter deux valeurs supplémentaires à cet objet secret. Sous l'onglet **Données**, cliquez sur le bouton **Ajouter des données** pour ajouter l'ID utilisateur et le mot de passe CouchDB à utiliser pour la base de données lors du déploiement du conteneur.
  1. Créez vos nom d'utilisateur et mot de passe et encodez les valeurs au format base64. Exécutez les commandes suivantes dans une fenêtre de terminal et remplacez `admin` et `adminpw` par les valeurs que vous souhaitez utiliser.
```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. Dans la zone **Nom**, entrez la valeur `couchdbuser`. Dans la zone **Value** correspondante, entrez le résultat de `echo -n 'couch' | base64 $FLAG` dans l'étape 1 plus haut.
  3. Cliquez sur le bouton **Ajouter des données** pour ajouter une seconde paire clé-valeur.
  4. Dans la seconde zone **Nom**, entrez la valeur `couchdbpwd`. Dans la zone **Value** correspondante, entrez le résultat de `echo -n 'couchpw' | base64 $FLAG` dans l'étape 1 plus haut.

6. Cliquez sur **Créer** pour sauvegarder votre objet secret.

**Remarque :** Le secret de configuration de l'homologue ne sera pas retiré de votre cluster ICP lors de la suppression de votre édition Helm. Si vous supprimez votre homologue, vous devez également supprimer ce secret.

## Configuration
{: #peer-configuration}

Après avoir créé votre objet secret de configuration de l'homologue, vous pouvez configurer et installer votre homologue dans ICP en suivant les étapes ci-après. Vous pouvez installer un seul homologue à la fois.


1. Connectez-vous à la console ICP et cliquez sur le lien **Catalogue** dans l'angle supérieur droit.
2. Cliquez sur `Blockchain` dans le panneau de navigation gauche afin de localiser la vignette libellée `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` si vous avez téléchargé l'édition Community. Cliquez sur la vignette afin de l'ouvrir et consulter un fichier Readme qui contient des informations sur l'installation et la configuration de la charte Helm.
3. Cliquez sur l'onglet **Configuration** dans la partie supérieure du panneau ou cliquez sur le bouton **Configurer** dans l'angle inférieur droit.
4. Indiquez les valeurs relatives aux [paramètres de configuration généraux](#peer-global-parameters) et acceptez le contrat de licence.
5. Ouvrez le bouton de développement `Tous les paramètres` et indiquez la valeur des [paramètres de configuration généraux](#peer-global-parameters).
6. Faites défiler vers le bas jusqu'à la section **Configuration de l'homologue**. Sélectionnez la case à cocher relative à l'`installation de l'homologue` et remplissez les  [paramètres de configuration](#peer-parameters) de l'homologue.
7. Cliquez sur **Installer**.

### Paramètres de configuration ;
{: #icp-peer-configuration-parms}

Le tableau suivant répertorie les paramètres configurables de {{site.data.keyword.blockchainfull_notm}} Platform, **spécifiques au composant de l'homologue**, ainsi que leurs valeurs par défaut. Si vous découvrez l'homologue ou si vous démarrez, utilisez les valeurs par défaut des paramètres de base de données et de stockage. Faites défiler jusqu'à la section relative au composant de l'homologue pour sélectionner la case relative à l'`installation de l'homologue` et fournissez les informations de configuration pour ce composant uniquement. **Même si l'interface utilisateur de la charte Helm indique qu'aucune configuration supplémentaire n'est nécessaire, vous devez entrer certains paramètres pour déployer un homologue.**

#### Paramètres de configuration généraux et globaux
{: #peer-global-parameters}

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
| `Helm release name`| Nom de votre édition Helm. Doit commencer par une lettre minuscule et se terminer par une caractère alphanumérique, doit contenir uniquement des traits d'union et des caractère alphanumérique minuscules. Vous devez utiliser un nom d'édition Helm unique chaque fois que vous essayez d'installer un composant. **Important :** Cette valeur doit correspondre à la valeur que vous avez utilisée pour générer le 'nom d'hôte de service' pour la zone "hosts" dans votre [fichier de secret JSON.](#peer-config-file) | none | yes |
| `Target namespace`| Choisissez l'espace de nom Kubernetes pour installer la charte Helm. | none | yes |
|**Global configuration**| Paramètres qui s'appliquent à tous les composants de la charte Helm|||
| `Service account name`| Entrez le nom du compte de service  [![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que vous allez utiliser pour exécuter le pod. | default | non |

#### Paramètres de configuration de l'homologue
{: #peer-parameters}

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
|**Cluster configuration** |**Informations de configuration de cluster** | ||
| `Install Peer` | Sélectionner pour installer un homologue|non sélectionné | Oui, si vous souhaitez déployer un homologue |
| `Peer worker node architecture`| Sélectionnez votre architecture de plateforme cloud (AMD64 ou S390x).| AMD64 | oui  |
| `Peer image repository`| Emplacement de la charte Helm de votre homologue. Cette zone est remplie automatiquement par le chemin installé. | ibmcom/ibp-fabric-peer | oui  |
| `Peer Docker image tag`| Valeur de la balise associée à l'image de l'homologue. |1.2.1, renseigné automatiquement avec la valeur correcte.|oui|
| `Peer configuration`|Vous pouvez personnaliser la configuration de l'homologue en collant votre propre fichier de configuration `core.yaml` dans cette zone. Pour voir un exemple de fichier `core.yaml`, voir l'exemple de config [`core.yaml` ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **Pour les utilisateurs avancés uniquement**. | aucune | non |
| `Peer configuration secret (Required)`| Nom du [secret de configuration d'homologue](#peer-config-secret) que vous avez créé dans ICP.  | aucune | oui  |
|`Organization MSP (Required)`| Vous pouvez créer une nouvelle valeur MSPID d'organisation, telle que 'org1' ou spécifier un MSP d'organisation existante dont l'homologue fera partie. Si vous avez déployé l'organisation d'un service de tri, vérifiez que les MSPID d'homologue sont différents du MSPID de votre service de tri. Notez également cette valeur, car vous en aurez besoin ultérieurement pour `CORE_PEER_LOCALMSPID` et `configtx.yaml`. | aucune | oui  |
|`Peer service type`| Utilisé pour indiquer si des[ports externes doivent être exposés ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sur l'homologue. Sélectionnez NodePort pour exposer les ports en externe (recommandé), et ClusterIP pour ne pas exposer les ports. LoadBalancer et ExternalName ne sont pas pris en charge dans cette édition. | NodePort | oui  |
| `State database`| [Base de données d'état](/docs/services/blockchain/glossary.html#state-database) utilisée pour le stockage de votre registre de canal. L'homologue doit utiliser la même base de données que votre [réseau de blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | aucune | oui  |
|`CouchDB image repository`| S'applique uniquement si CouchDB a été sélectionné comme base de données de registre. Cette zone est remplie automatiquement par le chemin installé. Si vous utilisez Community Edition et ne disposez pas d'un accès Internet, il doit s'agir du répertoire où vous avez téléchargé l'image CouchDB Fabric. | ibmcom/ibp-fabric-couchdb | yes |
| `CouchDB Docker image tag`| S'applique uniquement si CouchDB a été sélectionné comme base de données de registre. Valeur de la balise associée à l'image CouchDBde. | Renseigné automatiquement par la valeur correcte.| yes |
| `Peer Data persistence enabled`| Activité de la capacité de persistance des données après un redémarrage ou une défaillance du cluster. Voir la section relative au [stockage dans Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/ "Volumes") pour plus de détails. *Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod.* | checked | no |
| `Peer use dynamic provisioning`| Sélectionnez pour activer la mise à disposition dynamique pour les volumes de stockage. | sélectionné | non |
| `Peer persistent volume claim`| Pour une nouvelle réservation uniquement. Entrez un nom pour votre Réservation de volume persistant (PVC) à créer. | my-data-pvc | no |
| `Peer storage class name`| Indiquez une classe de stockage pour l'homologue. | Vide si création d'une nouvelle Réservation de volume persistant ; sinon, indiquez la classe de stockage qui est associée à la Réservation de volume persistant existante. | no |
| `Peer existing volume claim`| Indication du nom d'une Réservation de volume persistant existante et toutes les autres zones sont laissées vides. | none | no |
| `Peer selector label`| [Libellé de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs ") pour votre Réservation de volume persistant.| none | no |
| `Peer selector value`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs") pour votre Réservation de volume persistant. | none | no |
| `Peer storage access mode`| Indication du [mode d'accès![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour la Réservation de volume persistant.  | ReadWriteMany| no |
| `Peer volume claim size`| Taille de la Réservation de volume doit être supérieure à 2 Gi. | 8Gi  | yes |
| `State database persistent volume claim`| Pour une nouvelle réservation uniquement. Entrez un nom pour votre Réservation de volume persistant (PVC) à créer. | statedb-pvc | no |
| `State database storage class name`| Indiquez une classe de stockage pour la base de données d'état. | none | no |
| `State database existing volume claim`| Indication du nom d'une Réservation de volume persistant existante et toutes les autres zones sont laissées vides. | none | no |
| `State database selector label`| [Libellé du sélecteur](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) pour votre PVC| none | no |
| `State database selector value`| [Valeur du sélecteur](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) pour votre PVC| none | no |
| `State database storage access mode`| Indication du [mode d'accès![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour la Réservation de volume persistant. | ReadWriteMany| no |
| `State database volume claim size`| Choisissez la taille de disque à utiliser. | 8Gi | oui |
| `CouchDB - Data persistence enabled`| Pour le conteneur CouchDB, les données de registre seront disponibles au redémarrage du conteneur. *Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod.*| sélectionné | non |
| `CouchDB - Use dynamic provisioning`| Pour le conteneur CouchDB, utilisation de la mémoire dynamique Kubernetes. | sélectionné | non |
| `Peer CPU request` | Nombre minimum d'UC à allouer à l'homologue. | 1    | oui |
| `Peer CPU limit` | Nombre maximum d'UC à allouer à l'homologue.| 2 | oui |
| `Peer Memory request` | Quantité minimum de mémoire à allouer à l'homologue. | 1 Gi | oui |
| `Peer Memory limit` | Quantité maximum de mémoire à allouer à l'homologue. | 4 Gi | oui |
| `CouchDB CPU request` | Nombre minimum d'UC à allouer à CouchDB.| 1 | oui |
| `CouchDB CPU limit` | Nombre maximum d'UC à allouer à CouchDB. | 2 | oui |
| `CouchDB Memory request` | Quantité minimum de mémoire à allouer à CouchDB.| 1 Gi | oui |
| `CouchDB Memory limit` | Quantité maximum de mémoire à allouer à CouchDB. | 4 Gi | oui |


Lorsque CouchDB est sélectionnée comme base de données de l'homologue, deux conteneurs sont créés dans le pod, l'un pour l'homologue et l'autre pour CouchDB.
Le conteneur homologue inclut un seul montage de volume pour la Réservation de volume persistant de l'homologue qui stocke les blocs et les transactions sur le système de fichiers. Le conteneur CouchDB monte la Réservation de volume persistant de la base de données d'état de l'homologue qui contient les données de registre.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Utilisation de la ligne de commande Helm pour installer l'édition Helm
{: #icp-helm-cli}

Vous pouvez aussi utiliser l'interface de ligne de commande `helm` pour installer l'édition Helm. Avant d'exécuter la commande `helm install`, assurez-vous  [d'ajouter le référentiel Helm de votre cluster à l'environnement de l'interface de ligne de commande Helm![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

Vous pouvez définir les paramètres requis pour l'installation en créant un fichier `yaml` et en le transmettant à la commande `helm install` suivante.
```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

Où :
- `<helm_release name>` représente le nom que vous voulez affecter à votre édition Helm.
- `<helm_chart>` représente le nom de la charte Helm importée dans le catalogue.
- `<helm_chart_version>` représente la version de la charte Helm importée dans le catalogue.
- `<customvalues.yaml>` est le nom du fichier yaml qui contient les paramètres de configuration.

Par exemple :
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```

Vous pouvez créer un nouveau fichier `yaml` en éditant `values.yaml` inclus dans le fichier archive téléchargé, lequel inclut tous les paramètres nécessaires avec leurs valeurs par défaut.

## Vérification de l'installation de l'homologue
{: #verify-installation-icp}

Une fois que vous avez entré les paramètres de configuration et cliqué sur le bouton **Installer**, cliquez sur le bouton **Afficher l'édition Helm** pour afficher votre déploiement. Si l'opération aboutit, vous devez voir la valeur 1 dans les zones  `DESIRED`, `CURRENT`, `UP TO DATE` et `AVAILABLE` dans le tableau Déploiement. Vous devrez peut-être cliquer sur Actualiser et attendre que le tableau soit mis à jour. Vous pouvez aussi afficher le tableau Déploiement en cliquant sur l'icône **Menu** dans l'angle supérieur gauche sur la console ICP. Dans la liste de menus, cliquez sur **Charges de travail**, puis sur **Editions Helm**.

Si vous faites défiler jusqu'à la section `Remarques`, vous verrez des informations importantes relatives à l'[exploitation de votre homologue](/docs/services/blockchain/howto/peer_operate_icp.html).

## Affichage des journaux de l'homologue
{: #peer-deploy-view-logs}

Les journaux d'homologue peuvent être affichés à l'aide de [commandes de l'interface CLI kubectl](/docs/services/blockchain/howto/peer_operate_icp.html#peer-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"). Pour plus d'informations, consultez les [instructions relatives à l'accès aux journaux](/docs/services/blockchain/howto/peer_operate_icp.html#peer-icp-view-logs).

## Etapes suivantes

Après que vous avez déployé l'homologue, vous devez effectuer quelques étapes supplémentaires avant de soumettre des transactions et lire le registre partagé depuis le réseau de blockchain. Pour plus d'informations, voir  [Exploitation d'homologues dans un réseau multi-cloud](/docs/services/blockchain/howto/peer_operate_icp.html).

---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Déploiement d'un service de tri dans {{site.data.keyword.cloud_notm}} Private
{: #icp-orderer-deploy}

Les services de tri authentifient les clients, trient les transactions et diffusent les transactions dans un réseau de blockchain avec le composant de service de tri. Pour plus d'informations sur les services de tri et leur rôle dans un réseau blockchain, voir la [présentation sur les composants de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).
{:shortdesc}

Avant de déployer un service de tri, consultez les [considérations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Ressources obligatoires
{: #icp-orderer-deploy-resources-required}

Vérifiez que votre système {{site.data.keyword.cloud_notm}} Private respecte les exigences de ressources matérielle minimum :

| Composant | vCPU | RAM | Disque pour le stockage de données |
|-----------|------|-----|-----------------------|
| Service de tri | 2 | 512 Mo | 100 Go avec possibilité d'extension |


 **Remarques :**
 - Une unité vCPU est un coeur virtuel qui est affecté à une machine virtuelle ou à un coeur de processeur physique si le serveur n'est pas partitionné pour les machines virtuelles. Vous devez tenir compte des exigences vCPU lorsque vous décidez d'utiliser le coeur de processeur virtuel (VPC) pour votre déploiement dans {{site.data.keyword.cloud_notm}} Private. VPC est une unité de mesure pour déterminer les coûts de licences des produits IBM. Pour plus d'informations sur les scénarios VPC, voir [Virtual processor core (VPC) ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Ces niveaux de ressources minimum sont suffisants pour les tests et l'expérimentation. Pour un environnement avec un gros volume de transactions, il est important d'allouer une quantité de mémoire suffisamment important ; 500 Go pour votre service de tri par exemple. La quantité de stockage à utiliser dépend du nombre de transactions et du nombre de signatures qui sont nécessaires depuis votre réseau. Si vous êtes sur le point d'épuiser la mémoire de votre service de tri, vous devez déployer un nouveau service de tri avec un système de fichiers plus important et le laisser se synchroniser par l'intermédiaire de vos autres composants sur les mêmes canaux.

## Stockage
{: #icp-orderer-deploy-storage}

Vous devez déterminer l'espace de stockage que votre service de tri va utiliser. Si vous utilisez les paramètres par défaut, la charte Helm crée un nouveau volume persistant de 8 Gi nommé `orderer-data` pour les données de votre service de tri.

Si vous ne voulez pas utiliser les paramètres de stockage par défaut, vérifiez qu'une *nouvelle* `storageClass` est définie lors de l'installation de {{site.data.keyword.cloud_notm}} Private ou l'administrateur système Kubernetes doit créer une storageClass avant le déploiement.

Vous pouvez choisir de déployer le service de tri sur les plateformes amd64 ou s390x. Toutefois, vous devez savoir que la [Mise à disposition dynamique![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Mise à disposition de volume dynamique") est uniquement disponible pour les noeuds AMD64 uniquement dans {{site.data.keyword.cloud_notm}} Private. Si votre cluster combine à la fois des noeuds worker s390x et amd64, la mise à disposition dynamique ne peut pas être utilisée.

Si vous n'utilisez pas la mise à disposition dynamique, des [Volumes permanents![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes permanents") doivent être créés et configurés avec des libellés qui peuvent être utilisés pour affiner le processus de définition des accès Kubernetes PVC.


## Prérequis pour le déploiement d'un service de tri
{: #icp-orderer-deploy-prerequisites}

1. Avant d'installer un service de tri sur {{site.data.keyword.cloud_notm}} Private, vous devez [installer {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) et [installer la Charte Helm de {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Si vous utilisez Community Edition et souhaitez exécuter cette charte Helm sur un cluster {{site.data.keyword.cloud_notm}} Private sans connectivité Internet, vous devez créer des archives sur une machine connectée à Internet avant d'installer les archives sur votre cluster {{site.data.keyword.cloud_notm}} Private. Pour plus d'informations, voir [Adding featured applications to clusters without Internet connectivity ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Remarque : Vous pouvez trouver le fichier de spécification `manifest.yaml` sous ibm-blockchain-platform-dev/ibm_cloud_pak dans la charte Helm.

3. Procédez à l'extraction de la valeur de l'adresse IP proxy du cluster de votre autorité de certification depuis la console {{site.data.keyword.cloud_notm}} Private. **Remarque :** Vous devrez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Cluster administrator roles and actions") pour accéder à votre IP de proxy. Connectez-vous au cluster {{site.data.keyword.cloud_notm}} Private. Dans le panneau de navigation gauche, cliquez sur **Plateforme** puis sur **Noeuds** pour afficher les noeuds qui sont définis dans le cluster. Cliquez sur le noeud avec le rôle `proxy`, puis copiez la valeur de l'`IP hôte` de la table. **Important :** Conservez cette valeur car vous allez l'utiliser lors de la configuration de la zone `Adresse IP du proxy` de la charte Helm.

4. Créez un [fichier de configuration de service de tri et stockez-le en tant que secret Kubernetes dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file).

## Création du fichier de configuration d'un service de tri
{: #icp-orderer-deploy-config-file}

Avant de déployer un service de tri, vous devez créer un fichier de configuration contenant d'importantes informations relatives à l'identité du service de tri et à votre autorité de certification. Vous devez ensuite transmettre ce fichier à la charte Helm pendant la configuration à l'aide d'un objet [Valeur confidentielle de Kubernetes![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/configuration/secret/). Ce fichier permettra au service de tri d'obtenir les certificats dont il a besoin auprès de l'autorité de certification pour rejoindre un réseau de blockchain. Il contient également un certificat admin qui vous permet d'utiliser le service de tri en tant qu'utilisateur admin. Suivez les instructions relatives à l'[utilisation de l'autorité de certification pour déployer un service de tri ou un homologue](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer) avant de configurer le service de tri.

Vous devez fournir les noms d'hôte CSR dans le fichier de configuration. Les noms d'hôte CSR incluent l'adresse IP proxy du cluster où vous déployez le composant ainsi que le nom d'hôte de service qui sera le nom d’hôte de votre charte Helm. Le `nom de l'hôte de service` repose sur le `nom de l'édition Helm` que vous indiquez lors du déploiement. Le `nom d'hôte de service` est la valeur `helm_release_name` que vous spécifiez avec la chaîne `-orderer` ajoutée à la fin. Par exemple, si vous indiquez le `nom d'édition Helm` `orderer1`, vous pouvez insérer la valeur suivante dans la section `"csr"` du fichier :

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

Une fois le fichier de configuration sauvegardé, vous devez l'encoder dans le format base64 avant de fournir le fichier à {{site.data.keyword.cloud_notm}} Private en tant qu'objet secret. Un secret Kubernetes vous permet de protéger et de partager des informations sans avoir à les transmettre directement au déploiement.

1. Encodez le fichier de configuration au format base64 en exécutant les commandes suivantes dans une fenêtre de terminal :

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **Remarque :** Il est important que la chaîne générée par l'utilisation de la commande ci-dessus soit mise en forme sur une seule ligne. Elle doit ressembler à ceci :

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   et non à ceci :

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Sauvegardez la sortie obtenue à l'étape 4 ci-dessous.

2. Connectez-vous à la console {{site.data.keyword.cloud_notm}} Private. Dans le panneau de navigation gauche, cliquez sur **Configuration** puis sur **Secrets**. Cliquez sur le bouton **Créer Secret** pour ouvrir un panneau en incrustation qui vous permet de générer un nouvel objet secret.

3. Sous l'onglet **Général**, remplissez les zones suivantes :
  - **Nom :** Donnez un nom unique à votre secret au sein de votre cluster. Vous utiliserez ce nom lors du déploiement de votre service de tri. Il doit être entièrement en minuscules.  
  **Remarque :** Lorsque vous déployez un service de tri, un nouveau secret est automatiquement généré par le déploiement sous le nom `<helm_release_name>-orderer-mspsecret`. Par conséquent, lorsque vous nommez votre secret, assurez-vous que son nom soit différent de `<helm_release_name>-orderer-mspsecret`. Sinon, le déploiement de la charte Help échouera car le secret qu'il essaie de créer existe déjà.
  - **Espace de nom :** espace de nom pour l'ajout de votre secret. Sélectionnez l'`espace de nom` dans lequel vous voulez déployer votre service de tri.
  - **Type :** entrez la valeur `Opaque`.

4. Cliquez sur l'onglet **Données** dans le panneau de navigation gauche de cette fenêtre. Dans les zones `Nom`, indiquez `secret.json` et dans la zone de valeur collez la chaîne encodée `base64` de votre fichier de configuration.

5. Cliquez sur **Créer** pour sauvegarder votre objet secret.

**Remarque :** Le secret de configuration du service de tri ne sera pas retiré de votre cluster {{site.data.keyword.cloud_notm}} Private lors de la suppression de votre édition Helm. Si vous supprimez votre service de tri, vous devez également supprimer ce secret.

## Configuration
{: #icp-orderer-deploy-configuration}


Après avoir créé votre objet secret de configuration du service de tri, vous pouvez configurer et installer votre service de tri dans {{site.data.keyword.cloud_notm}} Private en suivant les étapes ci-après. Vous pouvez installer uniquement un service de tri à la fois.

1. Connectez-vous à la console {{site.data.keyword.cloud_notm}} Private et cliquez sur le lien **Catalogue** dans l'angle supérieur droit.
2. Cliquez sur `Blockchain` dans le panneau de navigation gauche afin de localiser la vignette libellée `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` si vous avez téléchargé l'édition Community. Cliquez sur la vignette afin de l'ouvrir et consulter un fichier Readme qui contient des informations sur l'installation et la configuration de la charte Helm.
3. Cliquez sur l'onglet **Configuration** dans la partie supérieure du panneau ou cliquez sur le bouton **Configurer** dans l'angle inférieur droit.
4. Indiquez les valeurs relatives aux [paramètres de configuration généraux](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-configuration-parms) et acceptez le contrat de licence.
5. Ouvrez le bouton de développement `Tous les paramètres` et indiquez la valeur des [paramètres de configuration généraux](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-global-parameters).
6. Faites défiler vers le bas jusqu'à la section **Orderer configuration**. Sélectionnez la case à cocher relative à l'`installation du service de tri` et remplissez les  [paramètres de configuration](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-parameters) du service de tri.
7. Cliquez sur **Installer**.


**Remarque :** Lors du déploiement du service de tri sur s390x, le conteneur init peut renvoyer l'erreur suivante :

`panic: Error while trying to open DB: no locks available`

Cela est dû au fait que le service de tri utilise un BD de fichier à plat alors que le système de fichiers NFS nécessite un package supplémentaire pour permettre au service de tri d'interroger des fichiers afin de vérifier qu'ils comportent des verrous exclusifs et pour la création de verrous exclusifs. Pour corriger ce problème, vous devez activer le package `rpc-statd`.

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

Cette opération doit être effectuée sur le système servi par le système de fichiers NFS.

### Paramètres de configuration
{: #icp-orderer-configuration-parms}

Le tableau suivant répertorie les paramètres configurables de {{site.data.keyword.blockchainfull_notm}} Platform, **spécifiques au composant du service de tri **, ainsi que leurs valeurs par défaut. **Même si l'interface utilisateur de la charte Helm indique qu'aucune configuration supplémentaire n'est nécessaire, vous devez entrer certains paramètres pour déployer un service de tri.**

#### Paramètres de configuration généraux et globaux
{: #icp-orderer-deploy-global-parameters}

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
| **Paramètres généraux**| Paramètres qui configurent la charte Helm | | |
| `Helm release name`| Nom de votre édition Helm. Doit commencer par une lettre minuscule et se terminer par une caractère alphanumérique, doit contenir uniquement des traits d'union et des caractère alphanumérique minuscules. Vous devez utiliser un nom d'édition Helm unique chaque fois que vous essayez d'installer un composant. **Important :** Cette valeur doit correspondre à la valeur que vous avez utilisée pour générer le 'nom d'hôte de service' pour la zone "hosts" dans votre [fichier de secret JSON.](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file) | aucune | oui  |
| `Target namespace`| Choisissez l'espace de nom Kubernetes pour installer la charte Helm. | aucune | oui |
| `Target namespace policies`| Affiche les règles de sécurité de pod de l'espace de nom choisi, lequel doit inclure une règle **`ibm-privileged-psp`**. Sinon, une règle [bind a PodSecurityPolicy](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp) est lié à votre espace de nom. | aucune | non |
|**Global configuration**| Paramètres qui s'appliquent à tous les composants de la charte Helm|||
| `Service account name`| Entrez le nom du compte de service [![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que vous allez utiliser pour exécuter le pod. | default | non |

#### Paramètres de configuration du service de tri
{: #icp-orderer-deploy-parameters}

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
| `Install Orderer`| Sélectionner pour installer un service de tri. | non sélectionné | oui, si vous souhaitez déployer un service de tri |
| `Orderer worker node architecture`| Sélectionnez l'architecture de votre noeud worker {{site.data.keyword.cloud_notm}} Private (AMD64 ou S390X). | Architecture détectée automatiquement en fonction de votre noeud principal | oui |
| `Orderer configuration`| Vous pouvez personnaliser la configuration du service de tri en collant votre propre fichier de configuration `orderer.yaml` dans cette zone. Pour voir un exemple de fichier `orderer.yaml`, voir l'exemple de config [`orderer.yaml` ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/orderer.yaml) **Pour les utilisateurs avancés uniquement**. | aucune | non |
| `Organization MSP secret (Required)`| Indiquez le nom de l'objet secret qui contient les certificats et les clés MSP de l'organisation. | aucune | oui |
| `Orderer data persistence enabled` | Des données seront disponibles au redémarrage du conteneur. Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod. | sélectionné | non |
| `Orderer use dynamic provisioning` | Sélectionnez pour activer la mise à disposition dynamique pour les volumes de stockage. | sélectionné | non |
| `Orderer image repository` | Emplacement de la charte Helm du service de tri. Cette zone est remplie automatiquement par le chemin installé. Si vous utilisez l'édition Community et ne disposez pas d'un accès Internet, définissez cette zone sur l'emplacement où vous avez téléchargé l'image de service de tri Fabric. | ibmcom/ibp-fabric-orderer | non |
| `Orderer Docker image tag`| Enregistrement de l'image Docker. Cette zone est remplie automatiquement par la version de l'image. Ne la modifiez pas.| 1.4.0 | oui |
| `Orderer consensus type`| Type de consensus du service de tri. | SOLO | oui |
| `Orderer organization name`| Indiquez le nom que vous souhaitez utiliser pour l'organisation du service de tri. Si vous souhaitez également déployer des homologues, vérifiez que vous utilisez un nom différent de celui que vous allez donner à vos homologues. Par exemple, donnez à l'organisation de votre service de tri un nom comme `ordererOrg` | aucune | oui |
| `Orderer Org MSP ID`| Indiquez le nom que vous souhaitez utiliser pour l'ID MSP de l'organisation du service de tri. Ce doit être le même nom que celui que vous avez donné à l'organisation de votre service de tri, et il sera défini comme variable d'environnement par le processus de déploiement. Notez cette valeur car vous devrez y faire référence ultérieurement. | aucune | oui |
| `Orderer storage class name`| Indiquez une classe de stockage pour le service de tri. | aucune | Dépend de la façon dont le cluster {{site.data.keyword.cloud_notm}} Private est configuré. Vérifiez auprès de votre administrateur de cluster |
| `Orderer existing volume claim`| Indiquez le nom d'une Réservation de volume persistant existante et laissez toutes les autres zones vides. | aucune | non |
| `Orderer selector label`| [Libellé de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) pour votre Réservation de volume persistant. | aucune | non |
| `Orderer selector value`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) pour votre Réservation de volume persistant. | aucune | non |
| `Orderer storage access mode`| Indiquez le mode d'accès au stockage [![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) pour le PVC. | ReadWriteMany | oui |
| `Orderer volume claim size`| Choisissez la taille de disque à utiliser, qui doit être d'au moins 2 Gi. | 8 Gi | oui |
| `Orderer service type` | Utilisé pour indiquer si des [ports externes doivent être exposés ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sur l'homologue. Sélectionnez NodePort pour exposer les ports en externe (recommandé), et ClusterIP pour ne pas exposer les ports. LoadBalancer et ExternalName ne sont pas pris en charge dans cette édition. | NodePort | oui |
| `Orderer CPU request`| Indiquez le nombre maximum d'UC à allouer au service de tri. | 1 | oui |
| `Orderer CPU limit`| Indiquez le nombre minimum d'UC à allouer au service de tri. | 2 | oui |
| `Orderer memory request`| Indiquez la quantité minimum de mémoire à allouer au service de tri. | 1 Gi | oui |
| `Orderer memory limit`| Indiquez la quantité minimum de mémoire à allouer au service de tri. | 2 Gi | oui |
| `gRPC web proxy CPU request`| Indiquez le nombre minimum d'UC en millicpus (m) à allouer au proxy Web gRPC. | 100 m | oui |
| `gRPC web proxy CPU limit`| Indiquez le nombre maximum d'UC en millicpus (m) à allouer au proxy Web gRPC. | 200 m | oui |
| `gRPC web proxy memory request`| Indiquez la quantité minimum de mémoire à allouer au proxy Web gRPC. | 100 Mi | oui |
| `gRPC web proxy memory limit`| Indiquez la quantité maximum de mémoire à allouer au proxy Web gRPC. | 200 Mi | oui |


### Utilisation de la ligne de commande Helm pour installer l'édition Helm
{: #icp-orderer-deploy-helm-cli}

Vous pouvez aussi utiliser l'interface de ligne de commande Helm pour installer l'édition Helm. Avant d'exécuter la commande `helm install`, assurez-vous [d'ajouter le référentiel Helm de votre cluster à l'environnement de l'interface de ligne de commande Helm![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

Vous pouvez définir les paramètres requis pour l'installation en créant un fichier `yaml` et en le transmettant à la commande `helm install` suivante.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

où :

- `<helm_release name>` représente le nom que vous voulez affecter à votre édition Helm.
- `<helm_chart>` représente le nom de la charte Helm importée dans le catalogue.
- `<helm_chart_version>` représente la version de la charte Helm importée dans le catalogue.
- `<customvalues.yaml>` est le nom du fichier yaml qui contient les paramètres de configuration.

Par exemple :

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values orderer-s390x-values.yaml \
--tls
```

Vous pouvez créer un nouveau fichier `yaml` en éditant `values.yaml` inclus dans le fichier archive téléchargé, lequel inclut tous les paramètres nécessaires avec leurs valeurs par défaut.

## Vérification de l'installation du service de tri
{: #icp-orderer-deploy-verify-install}

Une fois que vous avez entré les paramètres de configuration et cliqué sur le bouton **Installer**, cliquez sur le bouton **Afficher l'édition Helm** pour afficher votre déploiement. Si l'opération aboutit, vous devez voir la valeur 1 dans les zones `DESIRED`, `CURRENT`, `UP TO DATE` et `AVAILABLE` dans le tableau Déploiement. Vous devrez peut-être cliquer sur Actualiser et attendre que le tableau soit mis à jour. Vous pouvez aussi afficher le tableau Déploiement en cliquant sur l'icône **Menu** dans l'angle supérieur gauche sur la console {{site.data.keyword.cloud_notm}} Private. Dans la liste de menus, cliquez sur **Charges de travail**, puis sur **Editions Helm**.

## Affichage des journaux du service de tri
{: #icp-orderer-deploy-view-logs}

Les journaux de composant peuvent être affichés à partir de la ligne de commande à l'aide de [`commandes de l'interface CLI kubectl`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Votre fenêtre dans Elastic Search"), qui est inclus dans votre cluster {{site.data.keyword.cloud_notm}} Private. Pour plus d'informations, consultez les [instructions relatives à l'accès aux journaux](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-view-logs).

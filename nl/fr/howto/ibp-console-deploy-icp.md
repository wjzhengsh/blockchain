---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Déploiement d'une console {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-icp}

Les instructions suivantes décrivent comment déployer une console {{site.data.keyword.blockchainfull}} Platform dans votre propre infrastructure à l'aide de {{site.data.keyword.cloud_notm}} Private. Vous n'êtes pas tenu de déployer la console dans le même environnement que vos autres composants de blockchain.
{:shortdesc}

Vous allez utiliser une charte Helm pour déployer la console {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}} Private. Chacun de vos déploiements installe une instance unique de la console sur votre espace de nom {{site.data.keyword.cloud_notm}} Private.

## Remarques
{: #ibp-console-deploy-icp-considerations}

Avant de déployer la console, tenez compte des remarques suivantes :

- Les composants de blockchain doivent déjà avoir été déployés dans {{site.data.keyword.cloud_notm}} Private ou Amazon Web Services (AWS). Si ce n'est déjà fait, consultez [A propos de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private] ou A propos de [IBM Blockchain Platform for AWS] pour plus d'informations.
- Vous êtes chargé de gérer la surveillance, de la sécurité, de la journalisation et l'utilisation des ressources de votre console.
- Vous pouvez connecter votre console uniquement aux composants de blockchain qui sont au niveau de version 1.2.1 d'Hyperledger Fabric.

## Ressources obligatoires
{: #ibp-console-deploy-icp-resources-required}

Vérifiez que votre système {{site.data.keyword.cloud_notm}} Private respecte les exigences de ressources matérielle minimum :

| Composant | vCPU | RAM | Disque pour le stockage de données |
|-----------|------|-----|-----------------------|
| Console IBP | 1 | 192 Mo | 1 Go |
| Console CouchDB for IBP | 1 | 1 Go | 1 Go |

 **Remarques :**
 - Une unité vCPU est un coeur virtuel qui est affecté à une machine virtuelle ou à un coeur de processeur physique si le serveur n'est pas partitionné pour les machines virtuelles. Vous devez tenir compte des exigences vCPU lorsque vous décidez d'utiliser le coeur de processeur virtuel (VPC) pour votre déploiement dans {{site.data.keyword.cloud_notm}} Private. VPC est une unité de mesure pour déterminer les coûts de licences des produits IBM. Pour plus d'informations sur les scénarios VPC, voir[Virtual processor core (VPC) ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Ces niveaux de ressources minimum sont suffisants pour les tests et l'expérimentation. Pour un environnement avec un gros volume de transactions, il est important d'allouer une quantité de mémoire suffisamment important ; 250 Go pour votre console {{site.data.keyword.blockchainfull_notm}} Platform par exemple. La quantité de stockage à utiliser dépend du nombre de transactions et du nombre de signatures qui sont nécessaires depuis votre réseau. Si vous êtes sur le point d'épuiser l'espace de stockage de votre homologue ou service de tri, vous devez déployer une nouvelle console avec un système de fichiers plus important et importer de nouveau vos composants réseau comme il convient.

## Stockage
{: #ibp-console-deploy-icp-resources-required-storage}

Vous devez déterminer l'espace de stockage que votre console va utiliser. Si vous utilisez les paramètres par défaut, la charte Helm crée un nouveau volume persistant de 8 Gi nommé `console-pvc` pour les données de votre homologue, et un autre volume persistant de 8 Gi nommé `couchdb-pvc` pour votre base CouchDB.

Si vous ne voulez pas utiliser les paramètres de stockage par défaut, vérifiez qu'une *nouvelle* `storageClass` est définie lors de l'installation {{site.data.keyword.cloud_notm}} Private ou l'administrateur système Kubernetes doit créer une storageClass avant le déploiement.

Vous pouvez choisir de déployer la console sur les plateformes AMD64 ou S390X. Toutefois, vous devez savoir que la [mise à disposition dynamique](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) est uniquement disponible pour les noeuds AMD64 dans {{site.data.keyword.cloud_notm}} Private. Si votre cluster combine à la fois des noeuds worker S390X et AMD64, la mise à disposition dynamique ne peut pas être utilisée.

Si vous n'utilisez pas la mise à disposition dynamique, des[Volumes permanents ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) doivent être créés et configurés avec des libellés qui peuvent être utilisés pour affiner le processus de définition des accès Kubernetes PVC.

## Prérequis
{: #ibp-console-icp-prereq}

Avant de déployer la console {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}} Private, assurez-vous de terminer les étapes des tutoriels [Configuration de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

Vous devez également installer ou configurer les dépendances suivantes avant de déployer la console {{site.data.keyword.blockchainfull_notm}} Platform.

- [Enregistrez-vous auprès du service IBM App ID Service](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Installez Docker](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### Enregistrement auprès du service App ID
{: #ibp-console-icp-prereq-app-id}

[App ID ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/services/appid/index.html#gettingstarted "App ID") est un service de gestion des utilisateurs fourni par {{site.data.keyword.cloud_notm}}. La console {{site.data.keyword.blockchainfull_notm}} Platform utilise le service App ID pour gérer les utilisateurs qui pourront utiliser le tableau de bord et exploiter votre réseau de blockchain. Les utilisateurs de l'interface utilisateur pourront ainsi utiliser les données d'identification émises par votre propre organisation ou par des tiers comme Google ou Facebook, sans qu'il soit nécessaire que ceux-ci aient un ID IBM. Seul l'utilisateur qui déploie la console aura besoin d'un ID {{site.data.keyword.IBM_notm}} pour s'enregistrer auprès du service.

Avant de déployer la console, vous devez obtenir un ID IBM pour effectuer un enregistrement unique auprès de App ID Cloud Directory. Ensuite, vous devez extraire les données d'identification de APP ID à transmettre à la console. Utilisez les étapes suivantes pour un enregistrement auprès du service et obtenir vos informations d'identification du service.

1. Si vous n'en avez pas déjà un, vous devez créer un [ID {{site.data.keyword.IBM_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid).

2. Utilisez votre ID {{site.data.keyword.IBM_notm}} pour vous connecter ou vous inscrire à [{{site.data.keyword.cloud_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/app-id "IBM Cloud App ID"). Ensuite, accédez au service [App ID ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/app-id "IBM Cloud App ID") dans le catalogue. Sélectionnez le plan `Lite` ou `Graduated tier` en fonction de vos besoins de service. Ensuite, cliquez sur le bouton **Create** pour afficher l'écran "Welcome".

3. A partir de l'écran "Welcome", cliquez sur le lien **Manage** dans le panneau de navigation de gauche afin d'afficher ou de mettre à jour les fournisseurs d'identité préférés. Il peut s'agit de fournisseurs tiers, comme Google, ou du service d'identité propres à vos organisations.

4. Cliquez sur l'onglet **Settings** dans le tableau pour configurer une URL de redirection. Il s'agira de l'URL où vous allez déployer et accéder à la console {{site.data.keyword.blockchainfull_notm}} Platform. Pour utiliser localhost, indiquez `http://localhost/auth/cb` et cliquez sur le signe `+`.

5. Ajoutez votre adresse e-mail au service afin de pouvoir vous connecter lors de la configuration initiale de la console. Cliquez sur le lien **Users** dans le panneau de navigation de gauche et cliquez sur le bouton **Add User**. Entrez votre e-mail et cliquez sur **Save**.

6. Cliquez sur le lien **Service Credentials** dans le panneau de navigation de gauche. S'il s'agit de la première fois que vous utilisez le service, vous devez générer des données d'identification en cliquant sur **New credential**. Cliquez sur **View Credentials** pour copier les données d'identification nécessaires pour accéder au service.  Lorsque les informations relatives à la création des nouvelles données d'identification s'affichent, sélectionnez l'option `reader`, ainsi qu'un ID de service si vous voulez un nom spécifique à l'interface utilisateur. Les données d'identification d'App ID ressemblent aux valeurs JSON ci-dessous :
  ![Données d'identification du service](../images/service_credentials.gif "Données d'identification du service")

7. Cliquez sur l'icône de copie et sauvegardez vos données d'identification. Vous les utiliserez lors de la configuration de la console.

### Installation de Docker
{: #ibp-console-icp-prereq-docker}

Installez [Docker ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.docker.com/get-started) version 17.06.2-ce ou suivante dans l'environnement où vous allez déployer la console {{site.data.keyword.blockchainfull_notm}} Platform. Vous pouvez vérifier la version de Docker que vous avez installée à l'aide de la commande suivante depuis une fenêtre de terminal :

```
docker --version
```
{:codeblock}

**Remarque :** L'installation de Docker pour Mac, ou Windows, ou de Docker Toolbox installe également Docker Compose. Si Docker est déjà installé, vous devez vérifier que vous disposez de Docker Compose version 1.14.0 ou suivant. Si ce n'est pas le cas, nous vous recommandons d'installer un version plus récente de Docker. Vous pouvez vérifier la version de Docker Compose que vous avez installée à l'aide de la commande suivante depuis une fenêtre de terminal :

```
docker-compose --version
```
{:codeblock}


## Etape 1 : Télécharger le package de console {{site.data.keyword.blockchainfull_notm}} Platform depuis {{site.data.keyword.IBM_notm}} Passport Advantage
{: #ibp-console-icp-download-icp}

Contactez votre vendeur {{site.data.keyword.IBM_notm}} afin de télécharger le package de console {{site.data.keyword.blockchainfull_notm}} Platform depuis [Passport Advantage ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online pour les clients"). Vous ne connaissez pas de vendeur IBM ? Consultez ce lien <link> pour vous enregistrer et en trouver un ...

## Etape 2 : Importer la charte Helm de la console {{site.data.keyword.blockchainfull_notm}} Platform dans {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-icp-import-helmchart}

A titre de référence, consultez [Installation d'{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

## Etape 3 : Déployer la console {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-helmchart-icp}

Une fois que vous avez importé la charte Helm de la console {{site.data.keyword.blockchainfull_notm}} Platform, vous pouvez utilisez les étapes suivantes pour configurer et installer votre console.

1. Connectez-vous à la console {{site.data.keyword.cloud_notm}} Private et cliquez sur le lien **Catalogue** dans l'angle supérieur droit.
2. Cliquez sur `Blockchain` dans le panneau de navigation gauche afin de localiser la vignette libellée `ibm-blockchain-platform-ui`. Cliquez sur la vignette afin de l'ouvrir et d'afficher un fichier Readme comportant des informations sur la configuration et l'installation de la charte Helm.
3. Cliquez sur l'onglet **Configuration** dans la partie supérieure du panneau ou cliquez sur le bouton **Configurer** dans l'angle inférieur droit.
4. Définissez les [paramètres de configuration](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters).
5. Cliquez sur **Installer**.

### Paramètres de la console {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-icp-parameters}

Le tableau suivant répertorie les paramètres configurables de la console {{site.data.keyword.blockchainfull_notm}} Platform et leurs valeurs par défaut.

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
| `Helm release name`| Nom de votre édition Helm. Doit commencer par une lettre minuscule et se terminer par une caractère alphanumérique, doit contenir uniquement des traits d'union et des caractère alphanumérique minuscules. Vous devez utiliser un nom d'édition Helm unique chaque fois que vous essayez d'installer une console. | aucune | oui |
| `Target namespace`| Choisissez l'espace de nom Kubernetes pour installer la charte Helm. | aucune | oui |
| `Image repository`| Emplacement de la charte Helm de la console {{site.data.keyword.blockchainfull_notm}} Platform. | registry.ng.bluemix.net/op-tools/op-tools | oui |
| `Image tag`| Valeur de la balise associée à l'image de la console {{site.data.keyword.blockchainfull_notm}} Platform. Cette zone est remplie automatiquement par la version de l'image. Ne la modifiez pas. | v0.0.34 | oui |
| `Service type` | Utilisé pour indiquer si des [ports externes doivent être exposés ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sur l'homologue. Sélectionnez NodePort pour exposer les ports en externe (recommandé), et ClusterIP pour ne pas exposer les ports. LoadBalancer et ExternalName ne sont pas pris en charge dans cette édition. | NodePort | oui |
| `Console ingress` | Indiquez le nom d'hôte que vous voulez utiliser pour accéder à la console {{site.data.keyword.blockchainfull_notm}} Platform. | aucune | non |
| `Configtxlator ingress` | Indiquez le nom d'hôte que vous voulez utiliser pour accéder au service configtxlator. | aucune | non |
| `Persistence enabled` | Si sélectionné, les données seront disponibles au redémarrage du conteneur. Sinon, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod. | sélectionné | non |
| `Use dynamic provisioning` | Sélectionnez pour activer la mise à disposition dynamique pour les volumes de stockage. | sélectionné | non |
| `Storage class name`| Indiquez un nom de classe de stockage unique. Sinon, la classe de stockage par défaut dans le cluster est utilisée. | aucune | Dépend de la façon dont le cluster {{site.data.keyword.cloud_notm}} Private est configuré. Vérifiez auprès de votre administrateur de cluster. |
| `Existing volume claim`| Indiquez le nom d'une Réservation de volume persistant existante et laissez toutes les autres zones vides. | aucune | non |
| `Selector label`| [Libellé de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs ") pour votre Réservation de volume persistant. | aucune | non |
| `Selector value`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs") pour votre Réservation de volume persistant. | aucune | non |
| `Storage access mode`| Indiquez le [mode d'accès](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour le PVC. | ReadWriteMany | oui |
| `Volume name`| Indiquez le nom du PVC. | aucune | oui |
| `CPU request` | Nombre minimum d'UC à allouer à la console. | 250m | oui |
| `CPU limit` | Nombre maximum d'UC à allouer à la console.| 500m | oui |
| `Memory request` | Quantité minimum de mémoire à allouer à la console. | 256 Mi | oui |
| `Memory limit` | Quantité maximum de mémoire à allouer à la console. | 1 Gi | oui |

## Etape 4 : Configurer l'ID du proxy
{: #ibp-console-icp-config-proxy-ip}

Une fois l'installation terminée, cliquez sur le bouton **Afficher l'édition Helm** pour afficher la page d'édition Helm de votre console. Vous pouvez également cliquez sur **Menu** > **Charges de travail** > **Editions Helm** dans {{site.data.keyword.cloud_notm}} Private et cliquer sur votre édition Helm.

Sur la page d'édition Helm de votre console, rechercher l'IP du proxy du cluster {{site.data.keyword.cloud_notm}} Private, par exemple, `9.12.19.115`.

Sur la machine client où vous voulez accéder à la console, ajoutez des entrées dans `/etc/hosts` afin de mapper les noms d'hôte que vous avez entrés à l'étape précédente pour les services optools et configtxlator sur l'IP du proxy. Par exemple :
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## Etape 5 : Configuration de la console {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-icp-setup}

*Cette section pourrait bénéficier d'une animation gif.*

1. Dans {{site.data.keyword.cloud_notm}}, accédez à **Fournisseurs d'identité** > **Gérer** dans la navigation de gauche et sélectionnez l'onglet **Paramètres d'authentification**.
2. Dans la zone **Ajouter des URL de redirection Web**, indiquez l'URL que vous voulez utiliser pour accéder à la console avec votre App ID.
3. Dans votre navigateur Web, accédez à la console avec le nom d'hôte que vous avez configuré dans {{site.data.keyword.cloud_notm}} Private. Pour la première connexion, vous verrez les panneaux de configuration de la console.
  1. Sous l'onglet **Authentification**, entrez votre App ID.
  2. Sous l'onglet **Configuration**, collez les données d'identification du service App ID que vous avez copiées à l'[Etape 1 : Enregistrement auprès du service App ID](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id).
  3. Sous l'onglet **Ajouter des utilisateurs**, indiquez une liste des adresses e-mail des utilisateurs `Admin` et `Général` qui sont autorisés sur la console. Les domaines de messagerie sont limités à l'ensemble des fournisseurs d'identité, par exemple l'entrepris, Google ou Facebook, qui sont sélectionnés lors de l'enregistrement du service App ID dans {{site.data.keyword.cloud_notm}}.
    - Le rôle `Admin` est obligatoire pour pouvoir ajouter de utilisateurs ou retirer des utilisateurs existants de la liste d'autorisation de la console.   **Astuce :** Si vous êtes la personne qui administre la console, pensez à inclure votre propre adresse dans la liste.
    - Le rôle `Général` accorde aux utilisateurs un accès **Affichage uniquement** au composants de la console.

    Enfin, vous devez indiquer un seul `E-mail de contact d'administrateur` qui s'affiche en tant qu'adresse e-mail de contact lorsqu'un utilisateur non autorisé, qui ne figure dans aucune des listes précédentes, essaie d'accéder à la console. Cette adresse e-mail ne doit pas nécessairement être un utilisateur admin sur la console. Notez que ces informations peuvent également être créées ou modifiées ultérieurement sous l'onglet **Membres** de la console.
  4. Cliquez sur **Aller à Connexion**. Tous les utilisateurs qui sont spécifiés dans les listes précédentes peuvent à présent se connecter à la console à l'aide de la console {{site.data.keyword.blockchainfull_notm}} Platform avec leurs adresses e-mail.

---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# Installation d'{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private est fourni en tant que fichier de charte Helm qui peut être installé dans un cluster {{site.data.keyword.cloud_notm}} Private local. Une fois que vous avez installé la charte Helm, vous pouvez trouver {{site.data.keyword.blockchainfull_notm}} Platform en tant qu'application dans le catalogue {{site.data.keyword.cloud_notm}} Private.

Avant d'installer {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, passez en revue la section [Considérations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations). Pour plus d'informations sur le déploiement des composants de blockchain qui sont inclus dans la charte Helm, la tarification et le support, voir [A propos de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est disponibles dans deux éditions :

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est fourni via Passport Advantage (PPA). Vous devez posséder la licence requise pour accéder à [Passport Advantage Online ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Dans le cadre de cet achat, le support technique de {{site.data.keyword.blockchainfull_notm}} Platform est inclus.

- L'édition Community d'{{site.data.keyword.blockchainfull_notm}} Platform est une offre gratuite destinée à l'exploration, au développement et aux tests. Cette version gratuite est accessible via [GitHub ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/chartes"). Notez qu'{{site.data.keyword.IBM_notm}} n'assure pas de support pour l'édition Community.

## Prérequis pour l'installation de la charte Helm
{: #helm-install-prereqs}

Avant d'installer la charte Helm, vous devez avoir configuré un cluster {{site.data.keyword.cloud_notm}} Private et créé un nouvel espace de nom cible qui est lié à une politique de sécurité de pod. Passez en revue les instructions relatives à la [définition et à la configuration d'un cluster {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

## Installation de {{site.data.keyword.blockchainfull_notm}} Platform derrière un pare-feu
{: #helm-install-prereqs-firewall}

Vous pouvez déployer des composants {{site.data.keyword.blockchainfull_notm}} Platform derrière un pare-feu sans connectivité Internet. Le package PPA inclut l'ensemble des images Docker du composant Fabric utilisées par {{site.data.keyword.blockchainfull_notm}} Platform, et vous n'avez pas besoin de les télécharger depuis Docker durant le déploiement.

Toutefois, la charte Helm de l'édition Community n'inclut pas les images Docker nécessaires du composant Fabric car cette édition est configurée pour télécharger ces images depuis DockerHub durant le déploiement. Le déploiement échouera si aucune connexion Internet n'est disponible. Par conséquent, vous devez effectuer des étapes supplémentaires pour créer des archives sur une machine connectée à Internet pour pouvoir installer ces archives sur le cluster {{site.data.keyword.cloud_notm}} Private. Les images suivantes sont requises :
- [Fabric peer ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic peer")
- [Fabric CA ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric orderer ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric Couch database ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init` image used to bootstrap and configure components, including the MSP folders ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Docker-in-Docker (DinD) image used by peers to run chaincode containers ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Pour plus d'informations sur l'utilisation de ces images, voir [Adding featured applications to clusters without Internet connectivity ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html). Remarque : Vous pouvez trouver le fichier de spécification `manifest.yaml` sous le répertoire `ibm-blockchain-platform-dev/ibm_cloud_pak` dans la charte Helm.

## Importation de la charte Helm dans {{site.data.keyword.cloud_notm}} Private
{: #helm-install-importing}

1. Téléchargez le fichier de charte Helm d'IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private depuis [Passport Advantage Online ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") ou, pour l'édition gratuite Community, depuis [GitHub ![Icône de lien](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts").  Ce package de charte Helm contient trois chartes Helm secondaires pour l'autorité de certification, le service de tri et l'homologue.

2. Si ce n'est déjà fait, connectez-vous à votre cluster {{site.data.keyword.cloud_notm}} Private.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. Vérifiez que l'[interface CLI Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html) est configurée. Une fois l'interface CLI Docker configurée, accédez au registre d'images sur votre cluster à l'aide de la commande suivante :
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. Recherchez le nom du référentiel dans {{site.data.keyword.cloud_notm}} Private pour télécharger la charte Helm à l'aide de la commande suivante :
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Lorsque cette commande est exécutée correctement, vous pouvez voir une liste des référentiels dans votre cluster. Sélectionnez le nom du référentiel cible et sauvegardez-le. Vous devez l'utiliser dans la commande ci-dessous.

5. Importez la charte Helm à l'aide de la ligne de commande.
  La commande que vous exécutez pour importer la charte Helm dépend de la charte Helm qui a été téléchargée depuis Passport Advantage (PPA) ou depuis GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Suivez ces instructions si vous avez téléchargé la charte Helm depuis PPA.
    Depuis le répertoire où vous avez stocké le package de la charte Helm téléchargée depuis PPA, exécutez la commande suivante dans l'interface CLI {{site.data.keyword.cloud_notm}} Private afin d'importer la charte Helm dans votre cluster {{site.data.keyword.cloud_notm}} Private.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Remplacez les valeurs suivantes :
    - `<archive-name>` par le nom du fichier `.tgz` téléchargé.
    - `<cluster_CA_domain>:8500` par le domaine que vous utilisez pour vous connecter à votre cluster {{site.data.keyword.cloud_notm}} Private.
    - `<repo-name>` par le référentiel Helm ou vous voulez télécharger la charte. Exécutez 'cloudctl catalog repos' pour afficher la liste de vos référentiels.

    Lorsque cette commande est exécutée correctement, vous pouvez voir quelque chose similaire aux informations suivantes :

    ```  
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
      Processing image: ibmcom/ibp-couchdb-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
      Processing image: ibmcom/ibp-couchdb-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
      Processing image: ibmcom/ibp-dind-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
      Processing image: ibmcom/ibp-dind-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-1.0.2.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```  
    </details>

  - **Edition Community téléchargée depuis GitHub**
    Suivez ces instructions si vous avez téléchargé la charte Helm depuis GitHub.

    Depuis le répertoire où vous avez stocké le package de la charte Helm téléchargée depuis GitHub, exécutez la commande suivante dans l'interface CLI {{site.data.keyword.cloud_notm}} Private afin d'importer la charte Helm dans votre cluster {{site.data.keyword.cloud_notm}} Private. Pour importer la charte Helm téléchargée depuis GitHub, exécutez la commande suivante :
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Remplacez les valeurs suivantes :
    - `<helm_chart_from_github>` par le nom du fichier .tgz que vous avez téléchargé.
    - `<repo-name>` par le référentiel Helm ou vous voulez télécharger la charte. Exécutez 'cloudctl catalog repos' pour afficher la liste de vos référentiels.

    Lorsque cette commande est exécutée correctement, vous pouvez voir quelque chose similaire aux informations suivantes :
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

Cliquez sur le bouton **Catalogue** sur la console {{site.data.keyword.cloud_notm}} Private, puis cliquez sur **Blockchain** dans le panneau de navigation gauche afin de vérifier que l'importation a abouti. Si tel est le cas, la vignette **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** doit être visible sur la page du catalogue {{site.data.keyword.cloud_notm}} Private.

## Déploiement de composants individuels
{: #helm-install-deploying-components}

Une fois la charte Helm installée, cliquez sur la vignette **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** dans votre catalogue {{site.data.keyword.cloud_notm}} Private afin de l'ouvrir. Vous pouvez utiliser la page de configuration pour déployer des composants individuels de votre réseau de blockchain. Pour plus de détails sur les composants requis pour votre solution de blockchain et l'ordre dans lequel ils doivent être déployés, consultez le guide [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp).

Déployez ensuite les composants individuels :

- Si vous déployez un service de tri, vous devez d'abord définir une autorité de certification pour ce service de tri. L'autorité de certification générera des certificats qui seront utilisés par d'autres composants de votre organisation. Pour plus d'informations, voir [Déploiement d'une autorité de certification {{site.data.keyword.blockchainfull_notm}} Platform dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Vous pouvez ensuite déployer le service de tri qui sera la liaison commune du réseau. Pour plus d'informations, voir [Déploiement d'un service de tri {{site.data.keyword.blockchainfull_notm}} Platform dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)

- Si vous déployez un homologue, vous devez d'abord définir une autorité de certification pour cet homologue. L'autorité de certification générera des certificats qui seront utilisés par l'homologue. Pour plus d'informations, voir [Déploiement d'une autorité de certification {{site.data.keyword.blockchainfull_notm}} Platform dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Ensuite, lorsque vous êtes prêt à rejoindre un réseau, vous pouvez déployer les homologues qui rejoindront des canaux, valideront des transactions, et stockeront vos données. Pour plus d'informations, voir [Déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) ou [Déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} pour un réseau de plan Starter ou Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy), en fonction du réseau de blockchain que l'homologue va rejoindre.

## Mise à niveau de la charte Helm sur {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading}

Si vous utilisez {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.1, vous pouvez mettre à niveau votre charte Helm vers {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 en suivant les instructions de la section [Upgrading bundled products ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "Upgrading bundled products") dans la documentation {{site.data.keyword.cloud_notm}} Private. Outre la commande `helm upgrade` des instructions, vous pouvez également [utiliser la console {{site.data.keyword.cloud_notm}} Private pour mettre à niveau l'édition Helm](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui).

Vous devez avoir le rôle **administrateur de cluster** ou **administrateur d'équipe** sur {{site.data.keyword.cloud_notm}} Private pour mettre à niveau une charte Helm.
{:note}

L'annulation de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private version 1.0.2 vers la version 1.0.1 n'est pas prise en charge.

### Mise à niveau de l'édition Helm à partir de la console {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading-ui}

Lorsque vous mettez à niveau l'édition Helm d'un composant à partir de la console {{site.data.keyword.cloud_notm}} Private, vous devez sélectionnez `Réutiliser les valeurs` et ne modifier aucun paramètre.
{:important}  

Procédez comme suit pour mettre à niveau l'édition Helm à partir de la console {{site.data.keyword.cloud_notm}} Private :
1. Depuis la barre de menu, cliquez sur **Charges de travail** > **Editions Helm**.
2. Sélectionnez l'édition Help vers laquelle vous voulez effectuer la mise à niveau.
3. Vérifiez le numéro de version sous `Version disponible` dans la section **Détails et mises à niveau**.
4. Cliquez sur **ReadMe** sous le numéro de version afin de consulter les notes sur l'édition relatives aux modifications importantes.
5. Cliquez sur **Upgrade**, puis sélectionnez le référentiel et la version corrects dans la liste déroulante. 
6. Assurez-vous que l'option `Réutiliser les valeurs` est sélectionnée.
7. Cliquez sur **Mettre à niveau**.

Patientez le temps que s'affiche le message de confirmation et que l'état de la version se mette à jour en regard du nom de version don dans l'angle supérieur gauche de la console.

### Etapes supplémentaires pour la mise à niveau d'un homologue
{: #helm-install-upgrading-peer}

Après la mise à niveau d'un homologue, des étapes supplémentaires sont nécessaires pour terminer le processus de mise à niveau. Sur la page de version Helm de l'homologue, vous pouvez voir deux noeuds homologue sous la section **Pod** :
- Le nouveau pod homologue à l'état `CrashLoopBackOff`
- Le pod homologue d'origine à l'état `Running`

Notez les noms d'homologue associés, vous devrez les utiliser dans les étapes ci-dessous.
{:tip}

Procédez comme suit depuis l'interface CLI pour exécuter le processus de mise à niveau de l'homologue :
1. Connectez-vous à votre cluster depuis l'interface CLI de {{site.data.keyword.cloud_notm}} Private et exécutez la commande `kubectl get replicaset`.
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. Localisez le jeu de répliques de l'homologue qui correspond à l'homologue d'origine. Le nom du jeu de répliques de l'homologue doit correspondance à la portion initiale du nom d'homologue dans la section **Pod** sur la console {{site.data.keyword.cloud_notm}} Private.
3. Supprimez le jeu de répliques de l'homologue.
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. Supprimez le nouveau pod homologue. Il s'agit du pod homologue à l'état `CrashLoopBackOff`in the UI.
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

Votre homologue est bien mis à niveau lorsque vous constatez qu'un nouveau pod homologue est créé avec l'état `Running`.

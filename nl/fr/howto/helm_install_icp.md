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

# Installation d'{{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private est fourni en tant que fichier de charte Helm qui peut être installé dans un cluster ICP local. Une fois que vous avez installé la charte Helm,vous pouvez trouver {{site.data.keyword.blockchainfull_notm}} Platform en tant qu'application dans le catalogue ICP.

Avant d'installer {{site.data.keyword.blockchainfull_notm}} Platform for ICP, passez en revue la section  [Considérations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations). Pour plus d'informations sur le déploiement des composants de blockchain qui sont inclus dans la charte Helm, la tarification et le support, voir [A propos de {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp-for-icp-about.html).

{{site.data.keyword.blockchainfull_notm}} Platform for ICP est disponibles dans deux éditions :

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est fourni via Passport Advantage (PPA). Vous devez posséder la licence requise pour accéder à [Passport Advantage Online ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Dans le cadre de cet achat, le support technique de {{site.data.keyword.blockchainfull_notm}} Platform est inclus.

- L'édition Community d'{{site.data.keyword.blockchainfull_notm}} Platform est une offre gratuite destinée à l'exploration, au développement et aux tests. Cette version gratuite est accessible via [GitHub ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/chartes"). Notez  qu'{{site.data.keyword.IBM_notm}} n'assure pas de support pour l'édition Community. 

## Prérequis pour l'installation de la charte Helm
{: #helm-install-prereqs}

Avant d'installer la charte Helm, vous devez avoir configuré un cluster ICP. Passez en revue les instructions relatives à la [définition et à la configuration d'un cluster ICP](/docs/services/blockchain/ICP_setup.html).

## Installation de {{site.data.keyword.blockchainfull_notm}} Platform derrière un pare-feu
{: #helm-install-prereqs-firewall}

Vous pouvez déployer des composants {{site.data.keyword.blockchainfull_notm}} Platform derrière un pare-feu sans connectivité Internet. Le package PPA inclut l'ensemble des images Docker du composant Fabric utilisées par {{site.data.keyword.blockchainfull_notm}} Platform, et vous n'avez pas besoin de les télécharger depuis Docker  durant le déploiement.

Toutefois, la charte Helm de l'édition Community n'inclut pas les images Docker nécessaires du composant Fabric car cette édition est configurée pour télécharger ces images depuis DockerHub durant le déploiement. Le déploiement échouera si aucune connexion Internet n'est disponible. Par conséquent, vous devez effectuer des étapes supplémentaires pour créer des archives sur une machine connectée à Internet pour pouvoir installer ces archives sur cluster ICP. Les images suivantes sont requises :
- [Fabric peer ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Farbic peer")
- [Fabric CA ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric orderer ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric Couch database ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init` image used to bootstrap and configure components, including the MSP folders ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Docker-in-Docker (DinD) image used by peers to run chaincode containers ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Pour plus d'informations sur l'utilisation de ces images, voir [Adding featured applications to clusters without Internet connectivity ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html). Remarque : Vous pouvez trouver le fichier de spécification `manifest.yaml` sous le répertoire `ibm-blockchain-platform-dev/ibm_cloud_pak` dans la charte Helm.

## Importation de la charte Helm dans ICP

1. Téléchargez le fichier de charte Helm d'IBM Blockchain Platform for ICP depuis [Passport Advantage Online ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") ou, pour l'édition gratuite Community, depuis [GitHub ![Icône de lien](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts").  Ce package de charte Helm contient trois chartes Helm secondaires pour l'autorité de certification, le service de tri et l'homologue.

2. Si ce n'est déjà fait, connectez-vous à votre cluster ICP.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. Vérifiez que l'[interface CLI Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html) est configurée. Une fois l'interface CLI Docker configurée, accédez au registre d'images sur votre cluster à l'aide de la commande suivante :
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. Recherchez le nom du référentiel dans ICP pour télécharger la charte Helm à l'aide de la commande suivante :
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Lorsque cette commande est exécutée correctement, vous pouvez voir une listes des référentiels dans votre cluster.
Sélectionnez le nom du référentiel cible et sauvegardez-le. Vous devez l'utiliser dans la commande ci-dessous.

6. Importez la charte Helm à l'aide de la ligne de commande.
  La commande que vous exécuter pour importer la charte Helm dépend de la charte Helm qui a été téléchargée depuis Passport Advantage (PPA) ou depuis GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Suivez ces instructions si vous avez téléchargé la charte Helm depuis PPA. Depuis le répertoire où vous avez stocké le package de la charte Helm téléchargée depuis PPA, exécutez la commande suivante dans l'interface CLI ICP afin d'importer la charte Helm dans votre cluster ICP. 

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Remplacez les valeurs suivantes :
    - `<archive-name>` par le nom du fichier `.tgz` téléchargé.
    - `<cluster_CA_domain>:8500` par le domaine que vous utilisez pour vous connecter à votre cluster ICP.
    - `<repo-name>` par le référentiel Helm ou vous voulez télécharger la charte. Exécutez 'cloudctl catalog repos' pour afficher la liste de vos référentiels.

    Lorsque cette commande est exécutée correctement, vous pouvez voir quelque chose similaire aux informations suivantes :
    ```
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      Processing image: ibmcom/icp-dind-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      Processing image: ibmcom/icp-dind-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-0.1.5.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```

  - **Edition Community téléchargée depuis GitHub**
    Suivez ces instructions si vous avez téléchargé la charte Helm depuis GitHub.

    Depuis le répertoire où vous avez stocké le package de la charte Helm téléchargée depuis GitHub, exécutez la commande suivante dans l'interface CLI ICP afin d'importer la charte Helm dans votre cluster ICP. Pour importer la charte Helm téléchargée depuis GitHub, exécutez la commande suivante :
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Remplacez les valeurs suivantes :
    - `<helm_chart_from_github>` par le nom du fichier .tgz que vous avez téléchargé.
    - `<repo-name>` par le référentiel Helm où vous voulez télécharger la charte. Exécutez 'cloudctl catalog repos' pour afficher la liste de vos référentiels.

    Lorsque cette commande est exécutée correctement, vous pouvez voir quelque chose similaire aux informations suivantes :
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

Cliquez sur le bouton **Catalogue** sur la console ICP, puis cliquez sur **Blockchain** dans le panneau de navigation gauche afin de vérifier que l'importation a abouti. Si tel est le cas, la vignette **ibm-blockchain-platform-prod** ou  **ibm-blockchain-platform-dev** doit être visible sur la page du catalogue ICP.


## Exigences PodSecurityPolicy

Après l'importation de la charte Helm dans {{site.data.keyword.cloud_notm}} Private, vous devez lier une règle [PodSecurityPolicy ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Pod Security Policies") à l'espace de nom cible avant l'installation des composants. Choisissez un règle PodSecurityPolicy prédéfinie ou demandez à votre administrateur de cluster de créer une règle PodSecurityPolicy personnalisée pour vous :
- Nom de la règle PodSecurityPolicy prédéfinie : [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- Définition de règle PodSecurityPolicy personnalisée :
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- Rôle ClusterRole personnalisé pour la règle PodSecurityPolicy personnalisée :
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- Liaison ClusterRoleBinding personnalisée pour le rôle ClusterRole personnalisé :
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## Déploiement de composants individuels

Une fois la charte Helm installée, cliquez sur la vignette **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** dans votre catalogue ICP afin de l'ouvrir. Vous pouvez utiliser la page de configuration pour déployer des composants individuels de votre réseau de blockchain. Pour plus de détails sur les composants requis pour votre solution de blockchain et l'ordre dans lequel ils doivent être déployés, consultez le [Guide de déploiement d'{{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp_for_icp_deployment_guide.html).

Déployez ensuite les composants individuels :

- Si vous déployez un service de tri, vous devez d'abord définir une autorité de certification pour ce service de tri. L'autorité de certification générera des certificats qui seront utilisés par d'autres composants de votre organisation. Pour plus d'informations, voir [Déploiement d'une autorité de certification {{site.data.keyword.blockchainfull_notm}} Platform dans ICP](/docs/services/blockchain/howto/CA_deploy_icp.html). Vous pouvez ensuite déployer le service de tri qui sera la liaison commune du réseau. Pour plus d'informations, voir [Déploiement d'un service de tri {{site.data.keyword.blockchainfull_notm}} Platform dans ICP](/docs/services/blockchain/howto/orderer_deploy_icp.html)

- Si vous déployez un homologue, vous devez d'abord définir une autorité de certification pour cet homologue. L'autorité de certification générera des certificats qui seront utilisés par l'homologue. Pour plus d'informations, voir [Déploiement d'une autorité de certification {{site.data.keyword.blockchainfull_notm}} Platform dans ICP](/docs/services/blockchain/howto/CA_deploy_icp.html). Ensuite, lorsque vous êtes prêt à rejoindre un réseau, vous pouvez déployer les homologues qui rejoindront des canaux, valideront des transactions, et stockeront vos données. Pour plus d'informations, voir [Déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} dans ICP](/docs/services/blockchain/howto/peer_deploy_icp.html) ou [Déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} pour un réseau de plan Starter ou Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html), en fonction du réseau de blockchain que l'homologue va rejoindre.

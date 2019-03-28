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

# Initiation à la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-v2-deploy-iks}

{{site.data.keyword.blockchainfull}} Platform 2.0 est une version bêta gratuite qui inclut la console {{site.data.keyword.blockchainfull_notm}} Platform, interface graphique qui peut simplifier et accélérer vos opérations de déploiement et de gestion des composants de blockchain. Ce tutoriel explique comment s'initier à {{site.data.keyword.blockchainfull_notm}} Platform 2.0 et comment utiliser la console pour déployer et gérer des composants de blockchain dans votre cluster {{site.data.keyword.IBM_notm}} Kubernetes Service sur {{site.data.keyword.cloud_notm}}.
{:shortdesc}

**Public cible :** Cette rubrique s'adresse aux administrateurs système qui sont responsables de la configuration d'un cluster Kubernetes dans {{site.data.keyword.IBM_notm}} et du déploiement de {{site.data.keyword.blockchainfull_notm}} Platform.

 Après avoir déployé {{site.data.keyword.blockchainfull}} Platform dans votre cluster Kubernetes, vous pouvez lancer la console afin de créer et de gérer vos composants de blockchain. En utilisant un cluster {{site.data.keyword.IBM_notm}} Kubernetes Service pour déployer {{site.data.keyword.blockchainfull_notm}} Platform 2.0, vous bénéficiez d'importants avantages :

- **Contrôle :** Vous contrôlez et gérez vos composants et certificats de blockchain depuis une console centrale. Vous déployez uniquement les composants nécessaires à votre activité et pouvez en ajouter davantage au fur et à mesure de votre croissance.
- **Déploiement flexible basé sur Kubernetes :** Vous pouvez bénéficier des options de calcul (UC, mémoire, stockage) pour votre cluster Kubernetes et optimiser les options HA et DR intégrées.


## Remarques
{: #ibp-v2-deploy-iks-considerations}

Avant de déployer la console, tenez compte des remarques suivantes :

- La version 2.0 bêta gratuite de {{site.data.keyword.blockchainfull_notm}} Platform est générée avec Hyperledger Fabric version 1.4.
- Tous les homologues déployés avec la version 2.0 bêta gratuite utilisent CouchDB comme base de données d'état.
- Vous avez la possibilité d'utiliser un cluster Kubernetes gratuit à des fins d'évaluation de l'offre bêta. Toutefois, la capacité et les performances sont limitées, aucune de vos données ne peut être migrée et le cluster est supprimé au bout de 30 jours.
- Vous êtes chargé de gérer la surveillance de l'intégrité, de la sécurité et de la journalisation de votre cluster Kubernetes. Consultez ces [informations ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Responsabilités de gestion de cluster") pour plus de détails sur les éléments gérés par {{site.data.keyword.cloud_notm}} et sur ceux dont vous êtes responsable.
- Vous êtes également responsable de la surveillance de l'utilisation des ressources de votre cluster Kubernetes à l'aide du tableau de bord Kubernetes. Si vous devez accroître la capacité de stockage ou les performances de votre cluster, consultez les informations relatives à la [modification de votre volume existant![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "Modification de la taille et des IOPS de votre périphérique de stockage existant").
- Vous êtes responsable de la gestion et de la sécurisation de vos certificats, et de vos clés publiques et privées. IBM ne stocke pas vos certificats dans le cluster Kubernetes.
- L'offre de la version 2.0 bêta gratuite est disponible uniquement dans la région de **Dallas** du service {{site.data.keyword.cloud_notm}} Kubernetes. Cette région regroupe les centres de données de Dallas, Sam Jose, Houston et du Brésil. Par conséquent, tous les composants de blockchain peuvent résider dans l'un des ces quatre centres de données. Ils ne sont pas déployés ailleurs.
- Même si l'offre bêta est gratuite, vous devez quand même payer pour votre cluster Kubernetes, si vous choisissez un cluster payant.
- Kubernetes doit être en version 1.11 ou suivante dans votre cluster {{site.data.keyword.cloud_notm}} Kubernetes. Utilisez ces instructions pour [mettre à niveau vos clusters nouveaux et existants](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) vers cette version.

## Tutoriel vidéo
{: #ibp-v2-deploy-video}

Regardez la vidéo suivante pour vous familiariser avec la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull}} Platform et vous initier à son déploiement.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vidéo IBM Blockchain Platform version 2.0 bêta - Tutoriel de déploiement" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/gPnkVQiHRqk" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

## Avant de commencer
{: #ibp-v2-deploy-iks-prereq}

Avant de commencer :

- Assurez-vous de disposer d'un  [compte {{site.data.keyword.cloud_notm}} payant ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.cloud.ibm.com/catalog/services/blockchain "Catalogue"). Si vous n'avez pas de compte  :
1. Cliquez sur le bouton **S'inscrire**.
2. Après que vous avez créé un compte d'essai gratuit, effectuez une mise à niveau vers un type **Paiement à la carte** en accédant à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, et en cliquant sur **Ajouter carte de crédit**.

Lorsque vous prévoyez d'utiliser l'instance de service dans le contexte d'une solution de niveau organisation plus large, il est recommandé que les organisations participantes utilisent une adresse e-mail fonctionnelle pour créer leur réseau. Dans ce cas, l'accès au réseau ne dépend pas de la disponibilité d'une seule personne.
{:tip}  

- Si vous prévoyez d'utiliser un cluster {{site.data.keyword.cloud_notm}} Kubernetes Service existant, vérifiez la version de Kubernetes et effectuez une mise à niveau vers la version 1.11 ou supérieure, si nécessaire. Pour plus d'informations sur la manière de déterminer la version de Kubernetes dans laquelle s'exécute votre cluster et comment effectuer une mise à niveau de la version, voir [Mise à jour de la version Kubernetes de votre cluster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).

### Navigateurs
{: #ibp-v2-deploy-iks-browsers}
La liste suivante indique la version de navigateur minimum requise pour la console {{site.data.keyword.blockchainfull_notm}} Platform :

- Chrome : version la plus récente pour votre système d'exploitation
- Firefox : versions standard les plus récentes (non ESR) pour votre système d'exploitation
- Safari : version la plus récente pour Mac

### Ressources obligatoires
{: #ibp-v2-deploy-iks-resources-required}

Pour déployer la console {{site.data.keyword.blockchainfull_notm}} Platform dans un cluster {{site.data.keyword.cloud_notm}} Kubernetes Service, vous devez vous assurer que votre cluster Kubernetes respecte les exigences de ressources minimum requises :

|Type de cluster Kubernetes | Cas d'utilisation | UC | RAM | Noeuds worker |
|-----------|------|-----|-----------------------|
|Standard (recommandé) | Utilisable pour MVP | 4 (partagé) | 16 Go (partagé)|multiple|
|Gratuit | Utilisable pour évaluation | 2 | 4 Go | 1 |  

Ces ressources sont suffisantes pour les tests et l'expérimentation. Si vous utilisez un cluster Kubernetes gratuit, sachez que le cluster sera supprimé au terme de la période d'essai de 30 jours et toutes les ressources associées sont retirées. Les performances sont réduites de manière significative dans un cluster gratuit.
{:note}

## Etape 1 : Créer une instance de service dans {{site.data.keyword.cloud_notm}} Platform
{: #ibp-v2-deploy-iks-create-service-instance}

Procédez comme suit pour créer instance de service d'{{site.data.keyword.blockchainfull_notm}} Platform 2.0 on {{site.data.keyword.cloud_notm}}.

1. Localisez le [service de blockchain ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.cloud.ibm.com/catalog/services/blockchain) dans le catalogue {{site.data.keyword.cloud_notm}}, ou recherchez `Blockchain` dans votre page de catalogue {{site.data.keyword.cloud_notm}}.
2. Vous pouvez si vous le souhaitez renommer le **Nom de service** de votre instance afin qu'elle soit facile à reconnaître dans le futur.
3. Pour la version bêta, **Dallas** est la seule région disponible et elle ne peut pas être modifiée.
4. Vous pouvez laisser les zones de groupes de ressources et de balises vides.
5. Cliquez sur **Créer** pour mettre à disposition l'instance de service.

## Étape 2 : Déployer {{site.data.keyword.blockchainfull_notm}} Platform 2.0
{: #ibp-v2-deploy-iks-steps}

Vous pouvez suivre les instructions de déploiement d'{{site.data.keyword.blockchainfull_notm}} Platform 2.0 juste après avoir créé l'instance de service.

1. Etape **Bienvenue & Prérequis**. Si vous disposez déjà d'un cluster  {{site.data.keyword.IBM_notm}} Kubernetes Service dans la région de **Dallas** et souhaitez l'utiliser pour votre service de blockchain, sélectionnez la case à cocher. **Si vous utilisez un cluster existant,vous pouvez ignorer  l'étape suivante, en vous assurant au préalable que Kubernetes est en version 1.11 ou suivante**. Cliquez sur **Continuer**.
2. Etape **Créer un cluster**. Si vous sélectionnez la case à cocher à l'étape 1 pour utiliser un cluster Kubernetes existant, cette étape peut être omise. Sinon, cliquez sur **Créer un nouveau cluster**, ce qui lance le tableau de bord {{site.data.keyword.cloud_notm}} Kubernetes pour créer un cluster. Pour plus d'informations, voir [Initiation à {{site.data.keyword.cloud_notm}} Kubernetes Service ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/containers/container_index.html). Prévoyez un peu de temps supplémentaire pour l'exécution de ce processus.
  - Quel que soit le type de cluster que vous choisissez, vous devez sélectionner l'emplacement du cluster Kubernetes **Dallas** pour la version bêta.
  - Choisissez **Cluster standard (recommandé) :** Si vous avez besoin d'une option à plus long terme qui inclut plusieurs noeuds pour la haute disponibilité et qui puisse être migrée depuis l'offre bêta vers l'offre en disponibilité générale lorsque celle-ci sera disponible. **Assurez-vous de choisir la version 1.11 ou suivante de Kubernetes.**
  - Choisissez **Cluster gratuit :** Si vous prévoyez d'utiliser le cluster pendant une période inférieure à 30 jours. **Remarque ** : Il n'est pas possible de migrer depuis un cluster gratuit vers un cluster payant. Le type gratuit du cluster offre un espace de stockage et un débit de transactions limités.
  - Pour plus d'informations sur les différences entre les clusters Kubernetes gratuits et payants sur {{site.data.keyword.cloud_notm}}, voir [Comparaison des clusters gratuits et standard ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "Comparaison des clusters gratuits et standard").  

   Vous devez revenir à cet onglet dans votre navigateur après avoir créé le cluster afin de terminer le processus de déploiement de {{site.data.keyword.blockchainfull_notm}} Platform 2.0.  
   {:important}  

  Vous devez patienter le temps que votre cluster soit correctement déployé. Ensuite, cliquez sur le bouton **Je dispose d'un cluster**.
3. La version Kubernetes qui s'exécute dans votre cluster doit être au niveau de version 1.11 ou supérieur. Suivez ces [étapes](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) pour vérifier la version de votre cluster et effectuez une mise à niveau si nécessaire. Ensuite, revenez et poursuivez avec ces instructions.
4. Etape **Déployer sur le cluster**. Sélectionnez dans la liste déroulante le cluster Kubernetes sur lequel vous voulez déployer {{site.data.keyword.blockchainfull_notm}} Platform 2.0 et cliquez sur **Déployer sur un cluster**.  

  Si votre cluster Kubernetes n'est pas visible dans la liste déroulante, les raisons possibles sont les suivantes :
  - Le processus de création du cluster peut durer environ 60 minutes. Si vous avez créé un cluster, patientez un peu plus jusqu'à ce qu'il affiche l'état **Normal**.
  - Les clusters situés à l'extérieur de la région **Dallas** ne sont pas visibles et ils ne peuvent pas être utilisés.
  - Vérifiez que vous n'utilisez pas la version ESR de Firefox. Si tel est le cas, utilisez un autre navigateur tel que Chrome et réessayez.

5. Etape **Lancer la console**. Une fois que {{site.data.keyword.blockchainfull_notm}} Platform 2.0 a été déployé avec succès, cliquez sur **Lancer {{site.data.keyword.blockchainfull_notm}} Platform** pour ouvrir la console {{site.data.keyword.blockchainfull_notm}} Platform. Quelques minutes peuvent être nécessaires pour que le bouton soit activé le temps que la console soit mise à disposition.

## (Facultatif) Ajout d'utilisateurs supplémentaires à la console
{: #ibp-v2-deploy-iks-add-users}

Par défaut, la console utilise l'ID [{{site.data.keyword.IBM_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/iam/index.html#iamoverview "IBM Cloud Identity and Access Management") comme fournisseur de service d'identité. Votre console {{site.data.keyword.blockchainfull_notm}} Platform est mise à disposition par la configuration de l'adresse e-mail du propriétaire {{site.data.keyword.IBM_notm}} en tant qu'administrateur de la console. En tant qu'administrateur, l'utilisateur est autorisé à accorder à d'autres utilisateurs l'accès à la console à l'aide de leurs adresses e-mail.  

### Procédure d'ajout d'un nouvel utilisateur
{: #ibp-v2-deploy-iks-add-users-howto}

Cliquez sur l'icône **Utilisateurs** dans le panneau de navigation de gauche pour afficher la liste des utilisateurs qui peuvent se connecter à la console. Si vous êtes connecté en tant qu'administrateur de console, vous pouvez cliquer sur **Ajouter de nouveaux utilisateurs** pour autoriser de nouveaux utilisateurs sur la console et leur accorder un accès **Admin** ou **General**. Les utilisateurs ayant le rôle **Admin** peuvent ajouter ou supprimer des utilisateurs.

## Etapes suivantes
{: #ibp-v2-deploy-iks-next-steps}

A présent que votre console est prête pour utilisation, vous pouvez passer au [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Mise à jour de la version Kubernetes de votre cluster
{: #ibp-v2-deploy-iks-updating-kubernetes}

Si vous utilisez un cluster {{site.data.keyword.cloud_notm}} Kubernetes Service existant, assurez-vous que Kubernetes est au niveau de version 1.11 ou supérieur.

Vous pouvez vérifier la version Kubernetes de votre cluster dans la [page des clusters Kubernetes ![Icône de lien externe](../images/external_link.svg "External link icon")](https://console.cloud.ibm.com/containers-kubernetes/clusters) dans {{site.data.keyword.cloud_notm}}, qui répertorie tous vos clusters dans un tableau.

Si Kubernetes n'est pas en version 1.11 ou supérieur, vous devez procéder comme suit pour mettre à jour la version Kubernetes de votre cluster.

1. Cliquez sur l'icône de menu déroulant dynamique en fin de ligne et sélectionnez **Mettre à jour la version**. Ce processus dure environ une heure. Si la mise à jour de version aboutit, vous pouvez voir la version mise à jour de votre cluster dans la colonne **Version de Kubernetes**.  
2. Sélectionnez la version 1.11 ou une version suivante de Kubernetes dans la liste déroulante et cliquez sur **Mettre à jour**.
3. Cliquez sur votre cluster et accédez à l'onglet **Noeuds worker**. Sélectionnez la case à cocher en regard du noeud worker que vous voulez mettre à jour et cliquez sur **Mettre à jour Kubernetes** dans la barre de menu contextuel. Si votre cluster contient plusieurs noeuds worker, ils doivent tous être mis à jour.

  Les mises à jour des noeuds worker peuvent provoquer l'indisponibilité de vos services et applications. La machine de votre noeud worker est réimagée et les données sont supprimées si elles ne sont pas [stockées en dehors du pod ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview).
  {:important}

![Mise à jour de la version Kubernetes](../images/update_k8s_version.gif)

Pour plus d'informations sur la mise à jour de la version Kubernetes pour un cluster {{site.data.keyword.IBM_notm}} Kubernetes Service et les noeuds worker, voir [Mise à jour des clusters, des noeuds worker et des modules complémentaires![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/containers/cs_cluster_update.html#update).  

Vous devez attendre que la mise à jour soit terminée avant de [reprendre le déploiement d'{{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps).

## Suppression d'une instance de service
{: #ibp-v2-deploy-iks-delete-service-instance}

Lorsque vous n'avez plus besoin de votre instance de service, il est possible de la supprimer de votre cluster Kubernetes afin de libérer des ressources. Vous pouvez utiliser l'interface utilisateur d'IBM Cloud pour supprimer une instance de service  d'{{site.data.keyword.blockchainfull_notm}} Platform version 2.0 bêta gratuite.

1. Accédez à votre [tableau de bord {{site.data.keyword.cloud_notm}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/ "Dashboard").
2. Recherchez **Resources summary** et cliquez sur **Services**.
3. Sous le bouton de développement **Services**, recherchez l'insance de service à supprimer et cliquez sur **Delete** dans le menu Actions.

Si la suppression de l'instance de service échoue, le cluster Kubernetes n'est peut-être pas accessible. Si cela se produit, créez un [ticket de demande de service](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases) afin de demander la suppression de l'instance de service.

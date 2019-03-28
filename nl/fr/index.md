---

copyright:
  years: 2017, 2019
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

# Initiation à {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp}

{{site.data.keyword.blockchainfull}} Platform fournit une offre de blockchain en tant que service (BaaS) de pile gérée et complète qui vous permet de déployer des composants de blockchain dans les environnements de votre choix. L'environnement peut être {{site.data.keyword.cloud_notm}}, sur site via {{site.data.keyword.cloud_notm}} Private, et des clouds tiers, comme Amazon Web Services (AWS). Dans ce tutoriel, nous vous guiderons tout au long du processus général de configuration d'un réseau de blockchain de base avec {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

**Important :** Avant d'utiliser une offre d'{{site.data.keyword.blockchainfull_notm}} Platform, lisez les informations relatives aux données techniques et à l'aide dans la section [Clause de protection](/docs/services/blockchain/needtoknow.html#disclaimer).


## Avant de commencer
{: #get-started-ibp-prereqs}

{{site.data.keyword.blockchainfull_notm}} Platform propose différentes offres pour aider tous les types d'utilisateurs à démarrer leurs parcours sur la blockchain et transférer leurs applications en production. Vous devez décider de votre environnement et de l'offre que vous allez utiliser. La Figure 1 présente les fonctions, le public cible, la tarification et les plateformes de cloud pour les différentes offres. Pour plus d'informations sur chaque offre, cliquez sur son nom dans le tableau suivant.

| **Offres** | **Eléments inclus** | **Règle facturation** | **Plateforme cloud** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**Plan Starter**](/docs/services/blockchain/starter_plan.html#starter-plan-about) | Réseau géré par {{site.data.keyword.IBM_notm}} avec niveaux de service de base, environnement de développement et de tests | Abonnement mensuel | {{site.data.keyword.cloud_notm}} |
| [**Plan Enterprise**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) | Réseau géré par {{site.data.keyword.IBM_notm}} avec services avancés et environnement prêt pour la production d'entreprise | Abonnement mensuel | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform version 2.0 gratuite bêta**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | Console {{site.data.keyword.blockchainfull_notm}} Platform pour le déploiement et la gestion de composants de blockchain dans votre cluster Kubernetes {{site.data.keyword.cloud_notm}} | Gratuit pour version bêta | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | Autorité de certification déployable, Service de tri et chartes Helm d'homologue | [Tarification VPC](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) et Free Community Edition | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | Modèle de démarrage rapide AWS pour le déploiement d'homologues distants situés à l'extérieur d'{{site.data.keyword.cloud_notm}} | Gratuit | AWS |

*Figure 1. Offres {{site.data.keyword.blockchainfull_notm}} Platform*

N'utilisez pas le plan Starter ou la version 2.0 bêta gratuite en production. Il s'agit d'un environnement de développement et de test qui ne convient pas aux charges de travail en production.
{: important}

## Etape 1 : Obtenir une offre
{: #get-started-ibp-step1}

Vérifiez que vous disposez de la licence de compte cloud ou PPA pour obtenir une offre {{site.data.keyword.blockchainfull_notm}} Platform.

* **Plan Starter**, **Plan Enterprise** et **{{site.data.keyword.blockchainfull_notm}} Platform version 2.0 gratuite bêta**

  Ces offres sont disponibles dans {{site.data.keyword.cloud_notm}} et vous pouvez les trouver dans le [tableau de bord du Catalogue ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog "Catalogue") d'{{site.data.keyword.cloud_notm}}.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Cette offre est fournie en tant que charte Helm déployable et elle comporte à la fois une édition payante et une édition Community gratuite. Vous pouvez télécharger la version Enterprise Edition depuis [Passport Advantage Online ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/software/passportadvantage/pao_customer.html) ou télécharger l'édition Community gratuite depuis [GitHub ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz).

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Cette offre est disponible dans AWS et vous pouvez déployer un homologue de blockchain dans AWS à l'aide d'un [modèle de démarrage rapide![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Etape 2 : Déployer {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp-step2}

* **Plan Starter**, **Plan Enterprise** et **{{site.data.keyword.blockchainfull_notm}} Platform version 2.0 gratuite bêta**

  Connectez-vous à {{site.data.keyword.cloud_notm}} et créez une instance de service avec l'offre. Si vous utilisez un plan Starter, vous pouvez obtenir un réseau de blockchain avec une configuration par défaut juste après avoir [créé l'instance de service](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan). Si vous utilisez un plan Enterprise ou {{site.data.keyword.blockchainfull_notm}} Platform version 2.0 gratuite bêta, vous devez suivre les instructions de l'assistant pour terminer la configuration initiale de votre réseau. Pour plus d'informations, voir [Création d'un réseau de plan Enterprise](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) ou [Déploiement de {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Kubernetes Service](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Avant de déployer un réseau, vous devez [importer la charte Helm dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). Ensuite, vous pouvez [configurer et installer {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-deploy-icp).

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Cette offre est disponible dans AWS et vous pouvez déployer un homologue de blockchain dans AWS à l'aide d'un [modèle de démarrage rapide![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Etapes suivantes
{: #get-started-ibp-next-steps}

Une fois {{site.data.keyword.blockchainfull_notm}} Platform déployé dans l'environnement de votre choix, vous pouvez configurer le réseau avec un consortium, des noeuds, des canaux, des contrats intelligents et y démarrer des transactions. Pour plus d'informations, consultez les rubriques sous la section **Procédures détaillées** dans la zone de navigation gauche de la présente documentation.

## Support
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}} offre différente options de support sur des solutions de blockchain implémentées par {{site.data.keyword.IBM_notm}}. Pour plus d'informations sur le support {{site.data.keyword.blockchainfull_notm}} Platform, voir [Support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

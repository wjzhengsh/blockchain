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

# {{site.data.keyword.blockchainfull_notm}} Platform

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**ATTENTION :** Avant d'utiliser une offre d'{{site.data.keyword.blockchainfull}} Platform, lisez les informations relatives aux données techniques et à l'aide dans la section [Clause de protection](needtoknow.html).
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform (IBP) est la seule plateforme d'entreprise intégrée capable de gérer le cycle de vie complet d'un réseau de blockchain à plusieurs organisations. Elle est conçue pour accélérer, grâce à une collaboration à chaque stade, la création de réseaux de blockchain globaux "conçus pour l'entreprise" à la fois performants et sécurisés même dans les environnements les plus exigeants et les secteurs réglementés. Les offres réseau gérées par {{site.data.keyword.IBM_notm}} sur {{site.data.keyword.cloud_notm}} sont adaptées aux clients qui découvrent la blockchain. Les clients qui ont déjà utilisé Hyperledger Fabric peuvent déployer des composants réseau ou un réseau sur leur propre infrastructure via {{site.data.keyword.cloud_notm}} Private (ICP) ou Amazon Web Services (AWS).

{{site.data.keyword.blockchainfull_notm}} Platform propose différentes offres pour aider tous les types d'utilisateurs à démarrer leurs parcours sur la blockchain et transférer leurs applications en production.

|       | [**Plan Starter**](#starter-enterprise)      | [ **Plan Enterprise**](#starter-enterprise)  | [**IBP for ICP**](#ibp-for-icp) | [**IBP for AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **Eléments inclus** | **Niveaux de service de base, environnement de développement et de tests** | **Services avancés et environnement prêt pour la production d'entreprise** |**Autorité de certification déployable, Service de tri et chartes Helm d'homologue** | **Homologue de démarrage rapide AWS**  |
| **Règle facturation** | **Abonnement mensuel avec [crédits de cloud disponibles](howto/pricing.html#starter-plan-pricing)** | **Abonnement mensuel** |  **[Tarification VPC](ibp-for-icp-about.html#ibp-icp-pricing) et édition Community gratuite** | **Gratuit** |
| **Plateforme cloud**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud Private**| **AWS**|

**Attention :** N'utilisez pas le **plan Starter** en production. Il s'agit d'un environnement de développement et de test qui ne convient pas aux charges de travail en production.

## Fonctionnalités de {{site.data.keyword.blockchainfull_notm}} Platform 

Les offres de solution {{site.data.keyword.blockchainfull_notm}} reposent sur une base de code [Hyperledger Fabric](reference/v10_fabric.html) qui optimise une architecture modulaire pour parvenir aux niveaux de sécurité, d'intégrité des données, d'évolutivité et de performance nécessaires pour répondre aux besoins de votre entreprise.

{{site.data.keyword.blockchainfull_notm}} Platform fournit un réseau de blockchain hautement sécurisé et privé sur lequel des membres authentifiés peuvent aisément définir des actifs et créer les solutions métier permettant de les modifier et de les échanger.  Grâce aux offres {{site.data.keyword.blockchainfull_notm}} Platform, vous pouvez tirer parti d'une infrastructure d'orchestration qui vous permet d'**organiser votre consortium en un réseau de blockchain actif**. {{site.data.keyword.blockchainfull_notm}} Platform met ainsi à disposition des outils conviviaux pour encourager plusieurs institutions à s'associer et créer un réseau régi de manière démocratique. Les tâches de création, de gouvernance et de gestion du réseau deviennent intuitives et transparentes grâce au moniteur de tableau de bord intégré et aux utilitaires mis à disposition. En évitant le processus laborieux de création d'un réseau et d'implémentation d'une gouvernance, les membres du consortium peuvent ainsi **se concentrer sur le déploiement de smart contracts et le transfert d'actifs et d'informations**.

La **haute disponibilité** pour les ressources intégrales du réseau (homologues, services de tri, autorités de certification) élimine les effets désastreux qui pourraient naître de points de défaillance uniques. Un moniteur de tableau de bord intégré permet de gérer facilement ces ressources et constitue un puissant mécanisme pour la visualisation des actifs et des smart contracts. *Uniquement pris en charge dans les offres Starter et Enterprise*.

La **modularité** de l'architecture Hyperledger Fabric et la nette séparation des rôles réseau fournit une infrastructure qui permet l'évolutivité et l'adaptabilité à un large éventail de scénarios d'utilisation.*Disponible et pris en charge dans toutes les offres {{site.data.keyword.blockchainfull_notm}} Platform*.

Les freins et contrepoids qui opèrent tout au long du cycle de vie d'une transaction garantissent des résultats homogènes et approfondis, les registres demeurent constamment synchronisés grâce à la mise en oeuvre du protocole gossip. Le contrôle d'identité et d'accès sont aisément appliqués via les opérations de **signature/vérification** qui se produisent perpétuellement via le réseau, qui sont *disponibles et prises en charge dans toutes les offres {{site.data.keyword.blockchainfull_notm}} Platform*.

Des **outils de gouvernance** sont fournis pour permettre aux membres d'administrer et de gérer les règles métier essentielles au réseau. Vous pouvez, par exemple, implémenter des règles qui définissent le nombre de membres d'un réseau qui doivent accepter l'adhésion d'un nouveau membre. Ou peut-être un actif nécessite-t-il une validation par chaque participant pour qu'une modification puisse être effectuée. Les règles de gouvernance sont une absolue nécessité pour tous les types de réseau métier et elles peuvent souvent être extrêmement élaborées. Les outils de gouvernance (éditeurs de règle, par exemple) simplifient énormément ce processus.*Disponible uniquement dans les offres Starter et Enterprise*.

Les réseaux Enterprise Plan s'exécutent dans un environnement **hautement sécurisé et isolé** sans accès externe (accès racine compris) à des ressources réseau. Les données sont chiffrées durant leur transfert et leur stockage, et le support disponible pour les modules de sécurité matériels permet de protéger les clés numériques conformément à la réglementation du secteur. La virtualisation matérielle permet d'exécuter chaque noeud dans un environnement isolé, ce qui protège ainsi les autres noeuds du réseau des homologues susceptibles de contenir du code malintentionné ou malveillant. Le hachage, les opérations de signature/vérification, ainsi que les communications entre noeuds sont accélérés par l'implémentation de systèmes de chiffrement évolués. *Uniquement disponible avec Enterprise*.

Pour une présentation complète des fonctions et fonctionnalités de Hyperledger Fabric, voir la
[documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/ "Documentation Hyperledger Fabric").

La **Figure 1** illustre un exemple de réseau de blockchain déployé composé de quatre membres (chacun propriétaire de deux homologues), des autorités de certification responsables de la distribution du matériel cryptographique de vérification d'identité et d'un service de tri définissant les règles et les participants du réseau. Le canal bleu comporte l'ensemble des quatre membres du réseau, tandis que le canal jaune est limité à trois membres seulement : Les banques B, C et D. On peut voir également que la banque A a joué le rôle d'initiateur réseau et que la banque D existe seulement en tant que membre dans le contexte du canal jaune. Enfin, un utilisateur final ou une application en possession d'un certificat x509 correctement signé est en mesure d'effectuer des appels vers les homologues du réseau. 

![Réseau de blockchain](images/blockchain_network_2-01.png "Exemple de réseau de blockchain")

*Figure 1. Exemple de réseau de blockchain composé de quatre membres utilisant des canaux pour isoler des données*

## Développement d'applications pour une exécution sur le réseau

Vous pouvez explorer et accélérer le développement blockchain en optimisant les meilleures pratiques affinées dans plus de 400 engagements clients pour :

* Garantir une concordance étroite entre les entreprises dotées d'une technologie qui réduit considérablement le temps de développement des applications blockchain (des applications dont le développement était autrefois de six semaines peuvent désormais être crées en moins de deux jours).
* Promouvoir des compétences en matière de blockchain au sein de votre équipe de programmateurs en optimisant les outils de développeur {{site.data.keyword.blockchainfull_notm}}.
* Offrir aux développeurs la souplesse nécessaire pour leur permettre un apprentissage et un développement dans leur environnement préféré avec un jeu d'outils ouvert et moderne.

En tant que propriétaire de l'activité, vous pouvez développer votre scénario d'utilisation avec l'aide d'une équipe chevronnée d'experts IBM du secteur et en matière de blockchain, qui se sont engagés sur la voie {{site.data.keyword.blockchainfull_notm}} pour exploiter toutes les capacités d'{{site.data.keyword.blockchainfull_notm}} Platform.

En tant que développeur, vous pouvez mettre rapidement et facilement en concordance les besoins métier et accélérer le développement gratuit d'applications dans un environnement réseau {{site.data.keyword.blockchainfull_notm}} Platform à l'aide de l'aire de jeu interactive permettant de développer, d'itérer et de tester les réseaux d'entreprise. Les outils suivants sont conçus pour vous permettre de transformer des règles métier en code de réseau d'entreprise dans votre environnement préféré :

* **Explore online**
  Optimisez [{{site.data.keyword.blockchainfull_notm}} Platform : Develop](./develop.html)<!--, which is powered by an open source development tool--> pour découvrir les principaux concepts de blockchain, créer des définitions réseau, et optimiser des modèles d'industrie réutilisables et des bibliothèques de smart contract.

  Une fois que vous avez développé votre réseau d'entreprise, vous pouvez le développer dans un réseau opérationnel qui s'exécute sur {{site.data.keyword.blockchainfull_notm}} Platform. Pour plus d'informations, voir [Déploiement de réseaux d'entreprise avec le plan Starter](./develop_starter.html) et [Déploiement de réseaux d'entreprise avec le plan Enterprise](./develop_enterprise.html).

* **Install locally**
  Optimisez Hyperledger Fabric pour développer et tester directement sur votre ordinateur portable. Pour plus d'informations, voir [Création de votre premier réseau](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html).

* **Collaborate in a cloud environment**
  Utilisez des réseaux opérationnels prêts à l'emploi avec les options Plan Starter et plan Enterprise pour le développement et le partage de votre code avec d'autres utilisateurs. Pour plus d'informations, voir [A propos du plan Starter](starter_plan.html) et [A propos du plan Enterprise](enterprise_plan.html).

## Support {{site.data.keyword.IBM_notm}} 

{{site.data.keyword.IBM_notm}} offre différente options de support sur des solutions de blockchain implémentées par {{site.data.keyword.IBM_notm}}. Pour plus d'informations sur le support {{site.data.keyword.blockchainfull_notm}}, voir [Support](ibmblockchain_support.html).


## Plans Starter Plan et Enterprise
{: #starter-enterprise}

Les plans Starter et Enterprise vous permettent de déployer et d'exploiter des réseaux de blockchain décentralisés à l'aide d'un service sécurisé prêt pour la production. Le **plan Starter**  constitue un environnement d'apprentissage ou pour commencer à développer des réseaux de blockchain. Le **plan Enterprise**  constitue un environnement de production offrant de hauts niveaux de sécurité et de support. Commencez avec un petit réseau que vous ferez évoluer de manière élastique au fur et à mesure de l'ajout de membres et de l'accroissement du volume des transactions en utilisant les fonctions suivantes :

* Environnement hautement sécurisé doté de nombreuses fonctions de sécurité matérielles, logicielles et de microprogramme.
* Architecture renforcée pour l'évolutivité, la résilience et la disponibilité.
* Réseau optimisé pour les performances et s'exécutant dans un environnement de calcul Linux le plus rapide au monde.

L'exploitation de votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform inclut des outils et des fonctions qui simplifient les tâches d'administration :

* Tableaux de bord pour la surveillance et la gestion des ressources sur le réseau.
* Mises à niveau transparentes de la pile de code complète.
* Support technique 24h sur 24 et 7 jours sur 7 intégré au portail.
* Pile de sécurité renforcée sans aucun accès privilégié, résistance aux logiciels malveillants et aux tentatives de sabotage, chiffrement à 100 % et bien d'autres fonctions pour les réseaux comportant des données sensibles dans les secteurs réglementés.

Le plan Starter et le plan Enterprise sont des services de plateforme sur {{site.data.keyword.cloud_notm}} et ces deux plans respectent les [dispositions des services {{site.data.keyword.cloud_notm}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "{{site.data.keyword.cloud_notm}} Dispositions des services") relatives aux accords sur les niveaux de licence. Notez que les réseaux de plan Starter et  Enterprise sont mis à disposition dans **plusieurs environnements** dans des centres de données géographiquement distincts.

Pour plus d'informations sur les plans Starter et Enterprise, voir [A propos du plan Starter](starter_plan.html) et [A propos du plan Enterprise](enterprise_plan.html). Su vous êtes prêt à démarrer, inscrivez-vous pour l'[appartenance IBP Starter ou Enterprise![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) sur {{site.data.keyword.cloud_notm}}!

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-for-icp}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP offre les composants nécessaires à l'exécution d'un réseau de blockchain sur votre propre infrastructure via ICP. Ces composants incluent Hyperledger Fabric, une autorité de certification, un service de tri, ainsi qu'un homologue, que vous déployez,  gérer et configurez à l'aide de chartes Helm Kubernetes. **Cette offre est destinée aux clients dotés d'une bonne connaissance d'Hyperledger Fabric.**

IBP for ICP permet le déploiement de réseaux de blockchain sur un cloud privé pour répondre à des exigences relatives à l'hébergement de données, aux réglementations du marché et aux préférences d'infrastructure. Il simplifie le déploiement des principaux éléments d'un réseau de blockchain au sein de votre propre infrastructure via {{site.data.keyword.cloud_notm}} Private, une plateforme applicative basée sur Kubernetes pour le développement et la gestion d'applications sur site et conteneurisées.

 * Il permet aux clients de gérer des réseaux IBP au sein de leur propre infrastructure. Une édition Community gratuite permet aux clients de lancer des exécutions dans leurs propres environnements isolés et sécurisés, mais aucun support n'est assuré.
 * Il permet aux clients de configurer Fabric sur Kubernetes à l'aide de chartes Helm et d'une documentation détaillée pour les opérations.
 * Il propose aux clients un support technique avancé, sauf si vous utilisez l'édition Community gratuite.

 Pour plus d'informations sur IBP for ICP, voir [A propos de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](ibp-for-icp-about.html).

## {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #ibp-for-aws}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS permet aux homologues d'optimiser le profil de connexion, les autorités de certification Hyperledger Fabric, ainsi que le service de tri d'un réseau de plan Starter ou Enterprise existant sur {{site.data.keyword.cloud_notm}} pour le traitement des transactions via un modèle de démarrage rapide AWS. Le démarrage rapide vous permet de déployer des homologues à partir de modèles AWS CloudFormation. Ces modèles sont destinés aux décideurs d'infrastructure informatiques et aux administrateurs système qui souhaitent rapidement configurer, déployer et exécuter des homologues IBM hébergés par AWS qui sont connectés à un réseau de plan Starter ou Enterprise. Vous pouvez utiliser ce type de modèle pour créer un cloud privé virtuel (VPC) dans AWS, ou pour déployer l'homologue dans un VPC existant.

Le démarrage rapide permet d'effectuer les configurations suivantes :
 * Une architecture à haute disponibilité qui s'étend sur deux zones de disponibilité.
 * Un VPC configuré avec des sous-réseaux publics conformes aux meilleures pratiques AWS. Vous disposez ainsi de votre propre réseau virtuel sur AWS.
 * Une passerelle Internet pour autoriser l'accès à Internet.
 * Dans les sous-réseaux publics, deux homologues dans deux zones de disponibilité (un homologue dans chaque sous-réseau).
 * Dans chaque sous-réseau public, un conteneur homologue avec une base de données LevelDB intégrée ou un conteneur CouchDB secondaire.

Pour plus d'informations sur IBP for AWS, voir [About {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](howto/remote_peer.html).

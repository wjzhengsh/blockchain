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
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# A propos du plan Starter
{: #starter-plan-about}

Le plan Starter de {{site.data.keyword.blockchainfull}} Platform est une option d'entrée de gamme qui permet aux organisations de simuler des réseaux blockchain composés de plusieurs organisations, de développer rapidement des applications et d'utiliser des exemples de contrats et de réseaux d'entreprise intelligents. Il bénéficie également de la même interface utilisateur que les autres options d'appartenance, ce qui élimine le besoin de mettre en place des formations. Les nouveaux réseaux de plan Starter qui sont créés après le 4 octobre 2018 sont basés sur Hyperledger Fabric version 1.2.1. Les réseaux de plan Starter plus anciens demeurent au niveau de version Fabric 1.1.0.
{:shortdesc}

Le **Plan Starter** est un environnement pour ceux qui souhaitent démarrer avec le développement de réseaux de blockchain ou commencer à apprendre la technologie de blockchain. Vous pouvez utiliser un plan Starter comme environnement de développement ou de test qui vous permettra de démarrer petit et d'étendre le volume d'appartenance ou de transactions de votre réseau avant de passer à un environnement de production.

 Pour déployer un réseau de plan Starter, inscrivez-vous maintenant en tant que [membre Starter![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) sur {{site.data.keyword.cloud_notm}}. Lorsque vous êtes prêt(e) à commencer à développer votre réseau, consultez la section relative à la [mise en route d'un plan Starter](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).


**Remarques :**
- Le plan Starter d'{{site.data.keyword.blockchainfull_notm}} Platform est un environnement de tests et de développement, et il ne convient pas pour des charges de travail de production. Si vous avez besoin d'un environnement de production, consultez la section [A propos du plan Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform est un service de plateforme sur {{site.data.keyword.cloud_notm}} et toutes les offres d'appartenance suivent les [Dispositions des services {{site.data.keyword.cloud_notm}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Dispositions des services {{site.data.keyword.cloud_notm}}") relatives aux accords sur les niveaux de licence (SLA). Les réseaux du plan Starter sont mis à disposition dans **plusieurs environnements** dans des centres de données géographiquement distincts.

## Que propose le plan Starter ?
{: #starter-plan-about-what-starter-plan-offers}

- **_Réseau prêt en un seul clic_**
    Le plan Starter met à votre disposition un réseau de blockchain opérationnel d'un simple clic. Chaque réseau est livré avec une autorité de certification,  un service de tri, deux organisations (avec un homologue par organisation), et un canal par défaut sur lequel effectuer les transactions et déployer le code blockchain. {{site.data.keyword.blockchainfull_notm}} Platform gère la création et la configuration de ce réseau (vous pourrez le mettre à jour une fois qu'il sera opérationnel), en vous laissant vous consacrer au développement. Les réseaux de plan Starter reposent sur Fabric V1.2 et ils utilisent CouchDB comme base de données d'état.
- **_Rentabilité économique_**
    L'option d'appartenance du plan Starter propose la plupart des nombreuses fonctions de blockchain du plan Enterprise, mais à un coût moins élevé. Pour plus d'informations, voir [Tarification du plan Starter](/docs/services/blockchain/howto/pricing.html#ibp-pricing-starter-pricing).
- **_Simulation d'un réseau composé de plusieurs organisations_**
    Vous pouvez utiliser le plan Starter pour simuler la génération d'un réseau comprenant plusieurs organisations. Il n'est pas nécessaire d'inviter réellement d'autres organisations à rejoindre votre réseau, et vous pouvez occuper vous-même le rôle d'autres organisations. Vous découvrez ainsi comment une nouvelle organisation peut rejoindre un réseau, comment plusieurs organisations fonctionnent au sein du réseau, et ainsi de suite. Vous pouvez basculer entre vos organisations depuis le Moniteur réseau afin de voir et de gérer le réseau du point de vue des différentes organisations.

{{site.data.keyword.IBM_notm}} n'assure pas de support pour les réseaux utilisant Hyperledger Composer en production, y compris l'interface CLI Composer, les API JavaScript, le serveur REST et l'aire de jeu Web.
{:note}

## Le plan Starter vous convient-il ?
{: #starter-plan-about-is-starter-suitable-for-you}

Si vous êtes dans l'une des situations suivantes, vous pouvez commencer votre parcours de blockchain avec le plan Starter.
- **_Apprentissage de {{site.data.keyword.blockchainfull_notm}} Platform._**
    Si vous êtes intéressé par {{site.data.keyword.blockchainfull_notm}} Platform ou encore si vous découvrez le concept de blockchain, le plan Starter est une excellente façon d'en apprendre davantage sur le développement et la gouvernance d'un véritable réseau de blockchain. Vous découvrirez les composants dont est constitué un réseau, vous apprendrez à déployer et à gérer du code blockchain (également appelé "contrat intelligent"), à ajouter des canaux (et à gérer des droits de canal), mais également à suivre la génération d'un nouveau bloc, tout comme dans un réseau de production.
- **_Génération de solutions de démonstration dans un réseau opérationnel._**
    Le plan Starter fournit un environnement puissant pour démonstration d'applications blockchain. Le réseau de blockchain prêt à l'emploi permet de réaliser des présentations rapides pour les collègues, la direction et les partenaires grâce à des outils de gestion et d'exploitation fournis par le Moniteur réseau.
- **_Au-delà de la barre du développement d'une seule organisation._**
    Le plan Starter vous permet de simuler plusieurs organisations, ce qui vous permet de voir comment {{site.data.keyword.blockchainfull_notm}} Platform gère les tâches de collaboration, notamment la création de canal et l'instanciation de code blockchain, mais également de tester des applications et d'appeler des transactions. Vous pouvez aussi inviter d'autres personnes à collaborer au sein d'un réseau de plan Starter (comme dans les plans Enterprise). Ceci facilite la création d'un environnement plus réaliste, avec plusieurs parties endossant le code blockchain et les transactions.
- **_Développement et tests répétés d'applications de blockchain._**
    Le plan Starter propose une zone de transfert pour le développement et le test continus de votre code sur un réseau de blockchain. Le passage au cloud permet aux développeurs et aux testeurs de se concentrer sur les fonctionnalités et de passer sans difficulté de test unitaire à test fonctionnel. Le plan Starter fournit les mêmes fonctionnalités de réseau de blockchain et outils de gestion et d'exploitation que le plan Enterprise. Dès que vous êtes prêt à intégrer votre projet à l'un des plans Enterprise, vous pouvez l'exploiter de la même manière que dans le plan Starter, mais avec davantage de possibilités de faire croître votre réseau.

## Remarques relatives au plan Starter
{: #starter-plan-about-considerations}

Le plan Starter constitue un point d'entrée d'{{site.data.keyword.blockchainfull_notm}} Platform, à des fins de développement et de tests.  Vérifiez les éléments suivants avant d'utiliser un plan Starter.

- **Exigences liées au compte {{site.data.keyword.cloud_notm}} **
    Vous devez disposer d'un compte {{site.data.keyword.cloud_notm}} payant, par exemple, du type **Paiement à la carte**. Tous les plans d'appartenance offerts par {{site.data.keyword.blockchainfull_notm}} Platform nécessitent un compte payant {{site.data.keyword.cloud_notm}}. Pour mettre à niveau votre compte vers un compte de type Paiement à la carte, accédez à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, puis cliquez sur **Ajouter une carte de crédit**.
- **Différences par rapport au plan Enterprise**
    - L'[autorité de certification](/docs/services/blockchain/glossary.html#glossary-CA) et le service de tri ne sont pas tolérants aux pannes car chaque organisation ne dispose que d'une seule autorité de certification et un réseau n'a qu'un seul [service de tri](/docs/services/blockchain/glossary.html#glossary-orderer).
    - Le service de tri utilise uniquement [SOLO](/docs/services/blockchain/glossary.html#glossary-solo) et le [consensus](/docs/services/blockchain/glossary.html#glossary-consensus). Un réseau de plan Starter ne comporte qu'un seul service de tri qui effectue le consensus pour tous les homologues.
    - Aucun [Module de sécurité matérielle (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) n'est disponible pour la sauvegarde et la gestion des clés numériques pour une authentification forte et le traitement du chiffrement.
- **Versions et mise à niveau du plan Starter**
    - Les nouveaux réseaux de plan Starter qui sont créés après le 4 octobre 2018 sont basés sur Hyperledger Fabric version 1.2.1. Les réseaux de plan Starter plus anciens demeurent au niveau de version Fabric 1.1.0.
    - Les nouveaux homologues qui sont ajoutés à des réseaux de plan Starter plus anciens seront basés sur Fabric v1.2.1. Les performances de votre réseau ne sont pas affectées en raison de la compatibilité amont.
    - Vous avez la possibilité d'utiliser des paramètres de [configuration de canal ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "Configuration de canal") plus avancés et des [listes de contrôle d'accès![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-v1.2/access_control.html "Listes de contrôle d'accès") lors de la création ou de la mise à jour d'un canal.
    - La [Reconnaissance de service ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "reconnaissance de service") et la fonction de [données privées ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "données privées") d'Hyperledger Fabric version 1.2 ne sont pas prises en charge dans le plan Starter.
    - Si vous [réinitialisez](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network) un ancien réseau de plan Starter qui est en version 1.1.0 de Fabric, votre nouveau réseau sera u niveau 1.2 de Fabric. Si vous réinitialisez votre réseau, vous devez installer votre code blockchain ou les fichiers .bna sur le nouveau réseau, mais également re-inviter des membres de votre ancien réseau.
- **Limitation des ressources réseau**
    Le plan Starter alloue 1 UC et 4 Go de RAM pour chaque homologue, avec 20 Go de stockage pour chaque instance du service {{site.data.keyword.cloud_notm}}, homologue compris. Les conteneurs de code blockchain et les blocs de registre sont les composants réseau les plus gourmands en ressources. Les utilisateurs qui ont de nombreux homologues sur leur réseau, génèrent un grand nombre de blocs, ou utilisent des fichiers de code blockchain de grande taille, chaincode, peuvent rencontrer des problèmes de performance liés à des limitations de ressources. Vous pouvez afficher l'utilisation du stockage de vos réseau à l'écran ["Présentation" de votre Moniteur réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-storage).
- **Maintenance et mise à niveau**
    La maintenance du plan Starter et les mises à jour du réseau sont effectués d'après un calendrier fixe. Pendant la période de maintenance, vous ne pouvez pas mettre à disposition de nouveaux réseaux et vous pouvez constater de courtes périodes d'interruption réseau.
- **Conservation des données**
    Le plan Starter ne garantit pas la conservation des données après des mises à jour de version.
- **Considérations relatives à la migration**
    La migration des données d'un réseau de plan Starter vers d'autres plans d'appartenance n'est actuellement pas prise en charge. Il est toutefois possible de migrer des fichiers `.bna`, du code blockchain, ainsi que des applications qui ont été testées dans un plan Starter. Pour plus d'informations, voir [Migration d'un plan Starter vers un plan Enterprise](/docs/services/blockchain/howto/migrate_sp_ep.html#migrate_starter_to_enterprise).

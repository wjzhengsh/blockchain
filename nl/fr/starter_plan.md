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

# A propos du plan Starter
{: #overview}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Le plan Starter de {{site.data.keyword.blockchainfull}} Platform est une option d'entrée de gamme qui permet aux organisations de simuler des réseaux blockchain composés de plusieurs organisations, de développer rapidement des applications et d'utiliser des exemples de contrats et de réseaux d'entreprise intelligents. Il bénéficie également de la même interface utilisateur que les autres options d'appartenance, ce qui élimine le besoin de mettre en place des formations. Les nouveaux réseaux de plan Starter qui sont créés après le 4 octobre 2018 sont basés sur Hyperledger Fabric version 1.2.1. Les réseaux de plan Starter plus anciens demeurent au niveau de version Fabric 1.1.0.
{:shortdesc}

**Remarques :**
- Le plan Starter d'{{site.data.keyword.blockchainfull_notm}} Platform est un environnement de tests et de développement, et il ne convient pas pour des charges de travail de production. Si vous avez besoin d'un environnement de production, consultez la section [A propos du plan Enterprise](enterprise_plan.html). Vous pouvez consulter la section [Remarques relatives au plan Starter](#considerations) avant d'utiliser un plan Starter.
- {{site.data.keyword.blockchainfull_notm}} Platform est un service de plateforme sur {{site.data.keyword.cloud_notm}} et toutes les offres d'appartenance suivent les [Dispositions des services {{site.data.keyword.cloud_notm}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Dispositions des services {{site.data.keyword.cloud_notm}}") relatives aux accords sur les niveaux de licence (SLA). Les réseaux du plan Starter sont mis à disposition dans **plusieurs environnements** dans des centres de données géographiquement distincts.

## Que propose le plan Starter ?

- **_Réseau prêt en un seul clic_**
    Le plan Starter met à votre disposition un réseau de blockchain opérationnel d'un simple clic. Chaque réseau est livré avec une autorité de certification,  un service de tri, deux organisations (avec un homologue par organisation), et un canal par défaut sur lequel effectuer les transactions et déployer le code blockchain. {{site.data.keyword.blockchainfull_notm}} Platform gère la création et la configuration de ce réseau (vous pourrez le mettre à jour une fois qu'il sera opérationnel), en vous laissant vous consacrer au développement. Les réseaux du plan Starter sont basés sur Fabric version 1.2, et ils utilisent CouchDB comme base de données d'état. <!--The free trial provides you up to two organizations and two peers.-->
- **_Rentabilité économique_**
    L'option d'appartenance du plan Starter propose la plupart des nombreuses fonctions de blockchain du plan Enterprise, mais à un coût moins élevé. {{site.data.keyword.blockchainfull_notm}} Platform offre 500 $ de crédits de cloud aux nouveaux utilisateurs d'un plan Starter. Ces crédits vous permettent de mettre à disposition un réseau blockchain avec des ressources réseau de base sans encourir de frais pendant un mois. Voir [Remarques relatives au plan Starter](#starter-plan-considerations) pour plus de détails.
- **_Simulation d'un réseau composé de plusieurs organisations_**
    Vous pouvez utiliser le plan Starter pour simuler la génération d'un réseau comprenant plusieurs organisations. Il n'est pas nécessaire d'inviter réellement d'autres organisations à rejoindre votre réseau, et vous pouvez occuper vous-même le rôle d'autres organisations. Vous découvrez ainsi comment une nouvelle organisation peut rejoindre un réseau, comment plusieurs organisations fonctionnent au sein du réseau, et ainsi de suite. Vous pouvez basculer entre vos organisations depuis le Moniteur réseau afin de voir et de gérer le réseau du point de vue des différentes organisations.
    **Remarque **: Vous pouvez encourir des frais supplémentaires si vous ajoutez des ressources en excédent des ressources de base de la période d'essai gratuit (deux organisations et deux homologues).
<!-- - **_Easy to deploy sample applications_**
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **Intégration de l'application _{{site.data.keyword.blockchainfull_notm}} Platform: Develop_**
    Le plan Starter vous permet de déployer des réseaux d'entreprise que vous avez développés sur [{{site.data.keyword.blockchainfull_notm}} Platform : Develop](https://blockchaindevelop.mybluemix.net/login). Pour plus d'informations, voir [Déploiement d'un réseau d'entreprise sur un plan Starter](develop_starter.html).

## Le plan Starter vous convient-il ?

Si vous êtes dans l'une des situations suivantes, vous pouvez commencer votre parcours de blockchain avec le plan Starter.
- **_Apprentissage de {{site.data.keyword.blockchainfull_notm}} Platform._**
    Si vous êtes intéressé par {{site.data.keyword.blockchainfull_notm}} Platform ou encore si vous découvrez le concept de blockchain, le plan Starter est une excellente façon d'en apprendre davantage sur le développement et la gouvernance d'un véritable réseau de blockchain. Vous découvrirez les composants dont est constitué un réseau, vous apprendrez à déployer et à gérer du code blockchain (également appelé "smart contract"), à ajouter des canaux (et à gérer des droits de canal), mais également à suivre la génération d'un nouveau bloc, tout comme dans un réseau de production.
- **_Génération de solutions de démonstration dans un réseau opérationnel._**
    Le plan Starter fournit un environnement puissant pour démonstration d'applications blockchain. Le réseau de blockchain prêt à l'emploi permet de réaliser des présentations rapides pour les collègues, la direction et les partenaires grâce à des outils de gestion et d'exploitation fournis par le Moniteur réseau.
- **_Au-delà de la barre du développement d'une seule organisation._**
    Le plan Starter vous permet de simuler plusieurs organisations, ce qui vous permet de voir comment {{site.data.keyword.blockchainfull_notm}} Platform gère les tâches de collaboration, notamment la création de canal et l'instanciation de code blockchain, mais également de tester des applications et d'appeler des transactions. Vous pouvez aussi inviter d'autres personnes à collaborer au sein d'un réseau de plan Starter (comme dans les plans Enterprise). Ceci facilite la création d'un environnement plus réaliste, avec plusieurs parties endossant le code blockchain et les transactions.
- **_Développement et tests répétés d'applications de blockchain._**
    Le plan Starter propose une zone de transfert pour le développement et le test continus de votre code sur un réseau de blockchain. Le passage au cloud permet aux développeurs et aux testeurs de se concentrer sur les fonctionnalités et de passer sans difficulté de test unitaire à test fonctionnel. Le plan Starter fournit les mêmes fonctionnalités de réseau de blockchain et outils de gestion et d'exploitation que le plan Enterprise. Dès que vous êtes prêt à intégrer votre projet à l'un des plans Enterprise, vous pouvez l'exploiter de la même manière que dans le plan Starter, mais avec davantage de possibilités de faire croître votre réseau.


## Remarques relatives au plan Starter
{: #considerations}

Le plan Starter constitue un point d'entrée d'{{site.data.keyword.blockchainfull_notm}} Platform, à des fins de développement et de tests.  Vérifiez les éléments suivants avant d'utiliser un plan Starter.

- **Exigences liées au compte {{site.data.keyword.cloud_notm}} **
    Vous devez disposer d'un compte {{site.data.keyword.cloud_notm}} payant, par exemple, du type **Paiement à la carte**. Tous les plans d'appartenance offerts par {{site.data.keyword.blockchainfull_notm}} Platform nécessitent un compte payant {{site.data.keyword.cloud_notm}}. Pour mettre à niveau votre compte vers un compte de type Paiement à la carte, accédez à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, puis cliquez sur **Ajouter une carte de crédit**.
- **Crédits de cloud {{site.data.keyword.blockchainfull_notm}} Platform**
    {{site.data.keyword.blockchainfull_notm}} Platform offre 500 $ de crédits de cloud aux nouveaux utilisateurs d'un plan Starter. Ces crédits permettent aux utilisateurs de démarrer en couvrant les coûts d'une configuration réseau par défaut pendant un mois.
    - Les nouveaux utilisateurs doivent souscrire des crédits de cloud. Ils ne sont pas appliqués automatiquement. Veuillez vous [inscrire![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "inscrire") pour réclamer les crédits si vous ne les avez pas déjà. Patientez 24 heures afin de vous assurer que les crédits sont dans votre [compte ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "compte") avant d'accéder à votre plan Starter. Sinon, vous risquez d'être facturé avant application des crédits.
    - Les crédits de cloud s'appliquent à tous les services de {{site.data.keyword.cloud_notm}}. Ils peuvent être consommés par des produits autres qu'{{site.data.keyword.blockchainfull_notm}} Platform que vous utilisez sur {{site.data.keyword.cloud_notm}}.
    - L'offre fournit des crédits à vie. Vos crédits durent aussi longtemps que vous gérez votre compte {{site.data.keyword.cloud_notm}}. Vous n'avez dont pas à les utiliser au cours du premier mois.
    Pour en savoir plus sur l'utilisation des crédits restants, consultez le [guide de tarification](howto/pricing.html#starter-plan-pricing).
- **Différences par rapport au plan Enterprise**
    - L'[autorité de certification (CA)](glossary.html#ca) et le [service de tri](glossary.html#orderer) ne sont pas tolérants aux pannes car chaque organisation ne dispose que d'une seule autorité de certification et un réseau n'a qu'un seul [service de tri](glossary.html#orderer).
    - Le service de tri utilise uniquement le [consensus](glossary.html#consensus) [SOLO](glossary.html#solo). Un réseau de plan Starter ne comporte qu'un seul [service de tri](glossary.html#orderer) qui effectue le consensus pour tous les homologues.
    - Aucun [Module de sécurité matérielle (HSM)](glossary.html#hsm) n'est disponible pour la sauvegarde et la gestion des clés numériques pour une authentification forte et le traitement du chiffrement.
- **Versions et mise à niveau du plan Starter**
    - Les nouveaux réseaux de plan Starter qui sont créés après le 4 octobre 2018 sont basés sur Hyperledger Fabric version 1.2.1. Les réseaux de plan Starter plus anciens demeurent au niveau de version Fabric 1.1.0.

    - Les nouveaux homologues qui sont ajoutés à des réseaux de plan Starter plus anciens seront basés sur Fabric v1.2.1. Les performances de votre réseau ne sont pas affectées en raison de la compatibilité amont.
    - Vous pouvez utiliser la fonction [Données privées![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "Données privées") de  Hyperledger Fabric version 1.2 dans les nouveaux réseaux de plan Starter. Pour plus d'informations sur la manière d'utiliser vos données privées avec le code blockchain, voir [Données privées](howto/develop_chaincode.html#private-data).
    - Vous avez la possibilité d'utiliser des paramètres de [configuration de canal ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "Configuration de canal") plus avancés et des [listes de contrôle d'accès![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/access_control.html "Listes de contrôle d'accès") lors de la création ou de la mise à jour d'un canal.
    - La fonction de [Reconnaissance de service![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "econnaissance de service") d'Hyperledger Fabric version 1.2 n'est pas prise en charge dans {{site.data.keyword.blockchainfull_notm}}.
    - Si vous [réinitialisez](v10_dashboard.html#reset-network) un ancien réseau de plan Starter qui est en version 1.1.0 de Fabric, votre nouveau réseau sera u niveau 1.2 de Fabric. Si vous réinitialisez votre réseau, vous devez installer votre code blockchain ou les fichiers .bna sur le nouveau réseau, mais également re-inviter des membres de votre ancien réseau.
- **Limitation des ressources réseau**
    Le plan Starter alloue 1 UC et 4 Go de RAM pour chaque homologue, avec 20 Go de stockage pour chaque instance du service {{site.data.keyword.cloud_notm}}, homologue compris. Les conteneurs de code blockchain et les blocs de registre sont les composants réseau les plus gourmands en ressources. Les utilisateurs qui ont de nombreux homologues sur leur réseau, génèrent un grand nombre de blocs, ou utilisent des fichiers de code blockchain de grande taille, chaincode, peuvent rencontrer des problèmes de performance liés à des limitations de ressources.Vous pouvez afficher l'utilisation du stockage de vos réseau à l'écran ["Présentation" de votre Moniteur réseau](v10_dashboard.html#storage).
- **Maintenance et mise à niveau**
    La maintenance du plan Starter et les mises à jour du réseau sont effectués d'après un calendrier fixe. Pendant la période de maintenance, vous ne pouvez pas mettre à disposition de nouveaux réseaux et vous pouvez constater de courtes périodes d'interruption réseau.
- **Conservation des données**
    Le plan Starter ne garantit pas la conservation des données après des mises à jour de version.
- **Considérations relatives à la migration**
    La migration des données d'un réseau de plan Starter vers d'autres plans d'appartenance n'est actuellement pas prise en charge. Il est toutefois possible de migrer des fichiers `.bna`, du code blockchain, ainsi que des applications qui ont été testées dans un plan Starter. Pour plus d'informations, voir [Migration d'un plan Starter vers un plan Enterprise](howto/migrate_sp_ep.html).


<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](v10_application.html#adding-network-api-endpoints-to-your-application).
-->

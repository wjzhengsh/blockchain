---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# A propos du plan Starter
{: #overview}

Le plan Starter de {{site.data.keyword.blockchainfull}} Platform est une option d'entrée de gamme qui permet aux organisations de simuler des réseaux blockchain composés de plusieurs organisations, de développer rapidement des applications et d'utiliser des exemples de contrats et de réseaux d'entreprise intelligents. Il bénéficie également de la même interface utilisateur que les autres options d'appartenance, ce qui élimine le besoin de mettre en place des formations. Les réseaux du plan Starter reposent sur Hyperledger Fabric V1.1.
{:shortdesc}

**Remarques **:
1. Le plan Starter de la plateforme {{site.data.keyword.blockchainfull_notm}} est un environnement de tests et de développement, et il ne convient pas pour des charges de travail de production. Si vous avez besoin d'un environnement de production, consultez la section [A propos du plan Enterprise](enterprise_plan.html). Vous pouvez consulter la section [Remarques relatives au plan Starter](#considerations) avant d'utiliser un plan Starter.
2. Le plan Starter passe de la version bêta à la version GA (disponibilité générale) le 14 juin 2018. Si vous utilisez des réseaux bêta, vous voudrez éventuellement consulter la rubrique [considérations de migration](#beta-to-ga).

Inscrivez-vous maintenant pour appartenance à [{{site.data.keyword.blockchainfull_notm}} ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) et essayez le plan Starter en bénéficiant d'un [crédit d'essai](howto/pricing.html#starter-plan-pricing) ! <!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## Que propose le plan Stater ?

- **_Un réseau prêt en un clic_**  
    Le plan Starter met à votre disposition un réseau blockchain opérationnel d'un simple clic. Chaque réseau est livré avec un service de tri, des autorités de certification, deux organisations (avec un homologue par organisation), et un canal par défaut sur lequel effectuer les transactions et déployer le code blockchain. {{site.data.keyword.blockchainfull_notm}} Platform gère la création et la configuration de ce réseau (vous pourrez le mettre à jour une fois qu'il sera opérationnel), en vous laissant vous consacrer au développement. Les réseaux du plan Starter reposent sur Hyperledger Fabric V1.1 et utilisent CouchDB comme base de données de registre. <!--The free trial provides you up to two organizations and two peers.-->
- **_Rentabilité_**  
    L'option d'appartenance du plan Starter propose la plupart des nombreuses fonctions de blockchain du plan Enterprise, mais à un coût moins élevé. Lors de la période d'essai du plan Starter, vous pouvez mettre à disposition un réseau blockchain avec des ressources réseau de base sans encourir de frais.
- **_Simulation d'un réseau avec plusieurs organisations_**  
    Vous pouvez utiliser le plan Starter pour simuler la génération d'un réseau comprenant plusieurs organisations. Il n'est pas nécessaire d'inviter réellement d'autres organisations à rejoindre votre réseau, et vous pouvez occuper vous-même le rôle d'autres organisations. Vous découvrez ainsi comment une nouvelle organisation peut rejoindre un réseau, comment plusieurs organisations fonctionnent au sein du réseau, et ainsi de suite. Vous pouvez basculer entre vos organisations depuis le Moniteur réseau afin de voir et de gérer le réseau du point de vue des différentes organisations.
    **Remarque **: Vous pouvez encourir des frais supplémentaires si vous ajoutez des ressources en excédent des ressources de base de la période d'essai gratuit (deux organisations et deux homologues).
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_Intégration d'applications de la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop_**  
    Le plan Starter vous permet de déployer des réseaux d'entreprise que vous avez développés sur [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login). Pour plus d'informations, voir [Importing IBP: Develop applications](link).

## Public cible

Si vous êtes dans l'une des situations suivantes, vous pouvez commencer votre parcours de blockchain avec le plan Starter.
- **_Apprentissage de {{site.data.keyword.blockchainfull_notm}} Platform._**  
    Si vous êtes intéressé par la plateforme {{site.data.keyword.blockchainfull_notm}} ou encore si vous découvrez le concept de blockchain, le plan Starter est une excellente façon d'en apprendre davantage sur le développement et la gouvernance d'un véritable réseau de blockchain. Vous découvrirez les composants dont est constitué un réseau, vous apprendrez à déployer et à gérer du code blockchain (également appelé "contrat intelligent"), à ajouter des canaux (et à gérer des droits de canal), mais également à suivre la génération d'un nouveau bloc, tout comme dans un réseau de production.
- **_Génération de solutions de démonstration dans un réseau opérationnel. _**  
    Le plan Starter fournit un environnement puissant pour démonstration d'applications blockchain. Le réseau de blockchain prêt à l'emploi permet de réaliser des présentations rapides pour les collègues, la direction et les partenaires grâce à des outils de gestion et d'exploitation fournis par le Moniteur réseau.
- **_Au-delà de la barre du développement d'une seule organisation._**  
    Le plan Starter vous permet de simuler plusieurs organisations, ce qui vous permet de voir comment IBM Blockchain Platform gère les tâches de collaboration, notamment la création de canal et l'instanciation de code blockchain, mais également de tester des applications et d'appeler des transactions. Vous pouvez aussi inviter d'autres personnes à collaborer au sein d'un réseau de plan Starter (comme dans les plans Enterprise). Ceci facilite la création d'un environnement plus réaliste, avec plusieurs parties endossant le code blockchain et les transactions.
- **_Développement et tests répétés d'applications de blockchain._**  
    Le plan Starter propose une zone de transfert pour le développement et le test continus de votre code sur un réseau de blockchain. Le passage au cloud permet aux développeurs et aux testeurs de se concentrer sur les fonctionnalités et de passer sans difficulté de test unitaire à test fonctionnel. Le plan Starter fournit les mêmes fonctionnalités de réseau de blockchain et outils de gestion et d'exploitation que le plan Enterprise. Dès que vous êtes prêt à intégrer votre projet à l'un des plans Enterprise, vous pouvez l'exploiter de la même manière que dans le plan Starter, mais avec davantage de possibilités de faire croître votre réseau.


## Remarques relatives au plan Starter
{: #considerations}

Le plan Starter constitue un point d'entrée de la plateforme {{site.data.keyword.blockchainfull_notm}}, à des fins de développement et de tests.  Vérifiez les éléments suivants avant d'utiliser un plan Starter.

- Exigences liées au compte **{{site.data.keyword.cloud_notm}}**  	
    Vous devez disposer d'un compte {{site.data.keyword.cloud_notm}} payant, par exemple, du type **Paiement à la carte**. Tous les plans d'appartenance offerts par la plateforme {{site.data.keyword.blockchainfull_notm}} nécessitent un compte payant {{site.data.keyword.cloud_notm}}. Pour mettre à niveau votre compte vers un compte de type Paiement à la carte, accédez à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, puis cliquez sur **Ajouter une carte de crédit**.  
- **Différences par rapport au plan Enterprise**
    - L'[autorité de certification (CA)](glossary.html#ca) et le [service de tri](glossary.html#orderer) ne sont pas tolérants aux pannes car chaque organisation ne dispose que d'une seule autorité de certification et un réseau n'a qu'un seul [programme de tri](glossary.html#orderer).
    - Le service de tri utilise uniquement le [consensus](glossary.html#consensus) [SOLO](glossary.html#SOLO). Un réseau de plan Starter ne comporte qu'un seul [programme de tri](glossary.html#orderer) qui effectue le consensus pour tous les homologues.
    - Aucun [Module de sécurité matérielle (HSM)](glossary.html#hsm) n'est disponible pour la sauvegarde et la gestion des clés numériques pour une authentification forte et le traitement du chiffrement.
- **Limitation des ressources réseau**  
    Le plan Starter alloue 1 UC et 2 Go de RAM pour chaque homologue, avec 20 Go de stockage pour chaque instance du service {{site.data.keyword.cloud_notm}}. 
- **Suppression de réseau inactif**  
    Après que vous avez créé un réseau de plan Starter, si vous n'accédez pas à ce réseau ni n'émettez aucune transaction sur celui-ci pendant 15 jours, {{site.data.keyword.blockchainfull_notm}} Platform supprime le réseau.
    - Si vous n'avez plus besoin du réseau, vous n'avez rien à faire et votre réseau sera supprimé automatiquement.
    - Si vous avez encore besoin d'utiliser le réseau, vous pouvez le conserver actif en exécutant des transactions sur ce dernier. Vous pouvez trouver votre réseau de plan Starter sur votre tableau de bord [{{site.data.keyword.cloud_notm}}![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/dashboard/apps/){:new_window}. Si vous n'avez pas de transactions régulières à émettre, suivez les [instructions](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) pour essayer des modèles d'application.
- **Maintenance et mise à niveau**
    La maintenance du plan Starter et les mises à jour du réseau sont effectués d'après un calendrier fixe. Pendant la période de maintenance, vous ne pouvez pas mettre à disposition de nouveaux réseaux et vous pouvez constater de courtes périodes d'interruption réseau.
- **Conservation des données**
    Le plan Starter ne garantit pas la conservation des données après des mises à jour de la version, notamment le passage de la version bêta à la version GA.
- **Remarques relatives à la migration**
    - La migration des données d'un réseau de plan Starter vers d'autres plans d'appartenance n'est actuellement pas prise en charge. Il est toutefois possible de migrer des fichiers `.bna`, du code blockchain, ainsi que des applications qui ont été testées dans un plan Starter. Pour plus d'informations, voir [Migration d'un plan Starter vers un plan Enterprise](get_start_starter_plan.html#migrate).

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## Migration de la version bêta à la version GA
{: #beta-to-ga}

Le plan Starter passe à la phase GA (disponibilité générale) le 14 juin 2018. A compter de l'édition GA, IBM Blockchain Platform offre $500 de crédit d'évaluation pour chaque compte IBM Cloud afin de créer des réseaux blockchain sous le plan Starter. Pour plus d'informations sur le crédit pour période d'essai, sachez que le plan Starter passe à la phase GA le 14 juin 2018. A compter de l'édition GA, IBM Blockchain Platform offre $500 de crédit d'évaluation pour chaque compte IBM Cloud afin de créer des réseaux blockchain sous le plan Starter. Pour plus d'informations sur le crédit pour période d'essai, reportez-vous à la section *Compte d'essai du plan Starter* dans la rubrique [Tarification du plan Starter](howto/pricing.html#starter-plan-pricing). Assurez-vous de disposer d'un compte IBM Cloud payant, par exemple de type **Paiement à la carte**.
. Assurez-vous de disposer d'un compte IBM Cloud payant, par exemple de type **Paiement à la carte**.


Tous les réseaux blockchain créés sous la version bêta du plan Starter restent **gratuits** jusqu'à ce qu'ils soient supprimés **30 jours** après la sortie de la version GA du plan Starter. La migration de données des réseaux bêta du plan Starter vers les réseaux GA n'est pas prise en charge. **Vos données issues des réseaux bêta seront perdues.**  Toutefois, vous pouvez migre manuellement votre code blockchain, vos réseaux d'entreprise et vos applications.
- Si vous utilisez un code blockchain dans des réseaux bêta, installez et instanciez le code blockchain dans les réseaux GA. Pour plus d'informations, voir [Installation, instanciation et mise à jour d'un code blockchain](howto/install_instantiate_chaincode.html).
- Si vous avez installé in réseau d'entreprise sur des réseaux bêta, déployez le réseau d'entreprise via le fichier `.bna` sur les réseaux GA. Pour plus d'informations, voir [Déploiement d'un réseau d'entreprise sur un plan Starter](develop_starter.html).
- Si vous exécutiez sur des réseaux bêta des applications que vous aviez développées, mettez à jour les noeuds finaux d'API dans vos applications afin qu'elles pointent vers des noeuds du réseau GA. Pour plus d'informations, voir [Ajout de noeuds finaux d'API à votre application](v10_application.html#adding-network-api-endpoints-to-your-application).

---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# A propos du plan Starter
{: #overview}

Le plan Starter de la plateforme {{site.data.keyword.blockchainfull}} est une option d'entrée de gamme qui permet aux organisations de simuler des réseaux de blockchain composés de plusieurs organisations, de développer rapidement des applications et d'utiliser les exemples fournis. Il bénéficie également de la même interface utilisateur que les autres options d'appartenance, ce qui élimine le besoin de mettre en place des formations. Les réseaux du plan Starter reposent sur Hyperledger Fabric V1.1.{:shortdesc}

**Remarque **: le plan Starter de la plateforme {{site.data.keyword.blockchainfull_notm}} est un environnement de tests et de développement, et il ne convient pas pour des charges de travail de production. Si vous avez besoin d'un environnement de production, consultez la section [A propos du plan Enterprise](enterprise_plan.html). Vous pouvez consulter la section [Remarques relatives au plan Starter](#considerations) avant d'utiliser un plan Starter.

Inscrivez-vous pour devenir [membre {{site.data.keyword.blockchainfull_notm}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) et testez dès maintenant le plan Starter ! Notez que vous devez choisir la région **Sud des Etats-Unis** dans {{site.data.keyword.cloud_notm}} pour créer des réseaux de blockchain avec la version bêta du plan Starter.


## Public cible

Si vous êtes dans l'une des situations suivantes, vous pouvez commencer votre parcours de blockchain avec le plan Starter.
- **_Apprentissage de la plateforme {{site.data.keyword.blockchainfull_notm}}._**  
    Si vous êtes intéressé par la plateforme {{site.data.keyword.blockchainfull_notm}} ou encore si vous découvrez le concept de blockchain, le plan Starter est une excellente façon d'en apprendre davantage sur le développement et la gouvernance d'un véritable réseau de blockchain. Vous découvrirez les composants dont est constitué un réseau, vous apprendrez à déployer et à gérer du code blockchain (également appelé "contrat intelligent"), à ajouter des canaux (et à gérer des droits de canal), mais également à suivre la génération d'un nouveau bloc, tout comme dans un réseau de production.
- **_Simulation de plusieurs organisations pour simplifier le développement d'un réseau._**  
    Le plan Starter vous permet de simuler plusieurs organisations, ce qui vous permet de voir comment le plateforme IBM Blockchain gère les tâches de collaboration, notamment la création de canal et l'instanciation de code blockchain, mais également de tester des applications et d'appeler des transactions. Vous pouvez aussi inviter d'autres personnes à collaborer au sein d'un réseau de plan Starter (comme dans les plans Enterprise).
- **_Génération de solutions de démonstration dans un réseau opérationnel._**  
    Le plan Starter fournit un environnement puissant pour tester des définitions réseau (en intégrant un fichier `.bna` développé à l'aide de la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop) et des applications de blockchain. Le réseau de blockchain prêt à l'emploi permet de réaliser des présentations rapides pour les collègues, la direction et les partenaires grâce à des outils de gestion et d'exploitation fournis par le Moniteur réseau.
- **_Développement et tests répétés d'applications de blockchain._**  
    Le plan Starter propose une zone de transfert pour le développement et le test continus de votre code sur un réseau de blockchain. Vous pouvez développer votre code et le déployer de manière répétée au sein de votre architecture d'intégration et déploiement continus. Le plan Starter fournit les mêmes fonctionnalités de réseau de blockchain et outils de gestion et d'exploitation que le plan Enterprise. Dès que vous êtes prêt à intégrer votre projet à l'un des plans Enterprise, vous pouvez l'exploiter de la même manière que dans le plan Starter, mais avec davantage de possibilités de faire croître votre réseau.
- **_Test de projets avant leur mise en production._**  
    Starter Plan fournit un environnement qui permet aux développeurs et aux testeurs de passer rapidement de leur environnement local à un environnement de blockchain sur le cloud réel.  Développeurs et testeurs peuvent ainsi se concentrer sur les fonctionnalités et passer rapidement d'un test unitaire à un test fonctionnel. Les équipes de test système, solution et performances peuvent aussi trouver pratique d'utiliser cet environnement pour configurer un réseau en local.
- **_Plateforme {{site.data.keyword.blockchainfull_notm}} de formation._**  
    La plateforme {{site.data.keyword.blockchainfull_notm}} fournit virtuellement la même interface utilisateur<!--the same user interface--> dans les plans Starter et Enterprise. Vous êtes ainsi assuré que votre formation personnalisée utilise les mêmes flux de travail que votre organisation dans un plan Enterprise.
- **_Déploiement rapide de modèles d'application à l'aide de Toolchain._**  
    Le plan Starter permet de déployer en quelques clics des modèles d'application à l'aide de Toolchain. Ces modèles constituent une aide pour les développeurs en fournissant un nombre toujours grandissant d'exemples contenant du code qu'il est possible de modifier et de faire évoluer.


## Spécialisations du plan Starter

Avec le plan Starter, il est possible de gérer l'appartenance avec une autorité de certification, de valider des transactions, de fournir des services de tri, de générer des canaux privés, de gérer des cycles de vie de code blockchain, mais également de collaborer avec d'autres personnes dans un réseau opérationnel, tout comme dans un plan Enterprise.

Plus précisément, le plan Starter offre un réseau de blockchain pré-configuré que vous pouvez développer, gouverner et exploiter via le Moniteur réseau. Il fournit également des méthodes simples pour le déploiement de modèles d'application et intègre les applications que vous développez avec la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop.

- **_Un réseau prêt en un clic_**  
    Le plan Starter met à votre disposition un réseau doté d'un service de tri, d'autorités de certification (AC), d'un canal par défaut et de deux organisations (avec un homologue par organisation), le tout accessible d'un simple clic. La plateforme {{site.data.keyword.blockchainfull_notm}} gère la création et la configuration de réseau (vous pouvez mettre ce réseau à jour une fois qu'il est opérationnel).  <!--The free trial provides you up to two organizations and two peers.-->
- **_Rentabilité_**  
    L'option d'appartenance du plan Starter propose la plupart des nombreuses fonctions de blockchain du plan Enterprise, mais à un coût moins élevé.  <!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->Dans la version bêta, vous pouvez utiliser le plan Starter sans frais.
- **_Simulation d'un réseau avec plusieurs organisations_**  
    Vous pouvez utiliser le plan Starter pour simuler la génération d'un réseau comprenant plusieurs organisations. Il n'est pas nécessaire d'inviter réellement d'autres organisations à rejoindre votre réseau, et vous pouvez occuper vous-même le rôle d'autres organisations. Vous découvrez ainsi comment une nouvelle organisation peut rejoindre un réseau, comment plusieurs organisations fonctionnent au sein du réseau, et ainsi de suite. Vous pouvez basculer entre vos organisations depuis le Moniteur réseau afin de voir et de gérer le réseau du point de vue des différentes organisations.
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_API Swagger_**  
    Le plan Starter expose plusieurs API REST que vous pouvez tester depuis une interface Swagger. Pour plus de détails, voir [Test des API avec Swagger](swagger_apis.html).
- **_Modèles d'application_**  
    Le plan Starter optimise le service Toolchain dans {{site.data.keyword.cloud_notm}} et permet de déployer un modèle en quelques clics.  Une fois le modèle déployé et lancé, le code blockchain et les applications s'exécutent automatiquement pour votre réseau de blockchain. Pour plus d'informations sur les modèles d'application, voir [Déploiement de modèles d'application](howto/prebuilt_samples.html).
- **_Intégration d'applications de la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop_**  
    Avec le plan Starter, vous pouvez développer des applications de la plateforme {{site.data.keyword.blockchainfull_notm}} : au sein de votre réseau.  Pour plus d'informations, voir [Importing IBP: Develop applications](link).

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## Remarques relatives au plan Starter
{: #considerations}

Le plan Starter constitue un point d'entrée de la plateforme {{site.data.keyword.blockchainfull_notm}}, à des fins de développement et de tests. Vérifiez les éléments suivants avant d'utiliser un plan Starter.

- Exigences liées au compte **{{site.data.keyword.cloud_notm}}**  
    Le plan Starter est gratuit dans sa version bêta. Toutefois, vous devez mettre à niveau votre compte {{site.data.keyword.cloud_notm}} vers un compte payant, par exemple de type **Paiement à la carte**. Tous les plans d'appartenance offerts par la plateforme {{site.data.keyword.blockchainfull_notm}} nécessitent un compte payant {{site.data.keyword.cloud_notm}}. Pour mettre à niveau votre compte vers un compte de type Paiement à la carte, accédez à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, puis cliquez sur **Ajouter une carte de crédit**.
- **Différences par rapport au plan Enterprise**
    - L'[autorité de certification (CA)](glossary.html#ca) et le [service de tri](glossary.html#orderer) ne sont pas tolérants aux pannes car chaque organisation ne dispose que d'une seule autorité de certification et un réseau n'a qu'un seul [programme de tri](glossary.html#orderer).
    - Le service de tri utilise uniquement le [consensus](glossary.html#consensus) [SOLO](glossary.html#SOLO). Un réseau de plan Starter ne comporte qu'un seul [programme de tri](glossary.html#orderer) qui effectue le consensus pour tous les homologues.
    - Aucun [Module de sécurité matérielle (HSM)](glossary.html#hsm) n'est disponible pour la sauvegarde et la gestion des clés numériques pour une authentification forte et le traitement du chiffrement.
- **Limitation des ressources réseau**  
    Le plan Starter attribue 1 UC, 2 Go de RAM et 20 Gi de matériel de stockage de manière constante à chaque instance de service {{site.data.keyword.cloud_notm}}. Si vous ajoutez plus de ressources réseau au même réseau, ces ressources partagent les affectations de matériel. Toutefois, lorsque vous invitez un membre à rejoindre le réseau, ce membre crée une nouvelle instance de service. Par conséquent, le plan Starter affecte encore 1 UC, 2 Go de RAM et 20 Gi de matériel de stockage au nouveau membre.
- **Suppression de réseau inactif**  
    Après que vous avez créé un réseau de plan Starter (bêta), si vous n'accédez pas à ce réseau ni n'émettez aucune transaction sur celui-ci pendant 15 jours, la plateforme {{site.data.keyword.blockchainfull_notm}} supprime le réseau.
    - Si vous n'avez plus besoin du réseau, vous n'avez rien à faire et votre réseau sera supprimé automatiquement.
    - Si vous avez encore besoin d'utiliser le réseau, vous pouvez le conserver actif en exécutant des transactions sur ce dernier. Vous pouvez trouver votre réseau de plan Starter (bêta) sur votre tableau de bord [{{site.data.keyword.cloud_notm}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/apps/){:new_window}. Si vous n'avez pas de transactions régulières à émettre, suivez les [instructions](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) pour essayer des modèles d'application.
- **Maintenance et mise à niveau**  
    La maintenance et les mises à jour réseau du plan Starter sont effectuées selon un calendrier prédéfini. Pendant la période de maintenance, vous ne pouvez pas mettre à disposition de nouveaux réseaux et vous pouvez constater de courtes périodes d'interruption réseau.
- **Conservation des données**  
    Le plan Starter ne garantit pas la conservation de données avec les mises à niveau de version, y compris lors du passage de la version bêta à la version de disponibilité générale.
- **Remarques relatives à la migration**  
La migration des données d'un réseau de plan Starter vers d'autres plans d'appartenance n'est actuellement pas prise en charge. Il est toutefois possible de migrer des fichiers `.bna`, du code blockchain, ainsi que des applications qui ont été testées dans un plan Starter. Pour plus d'informations, voir [Migration d'un plan Starter vers un plan Enterprise](get_start_starter_plan.html#migrate).
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

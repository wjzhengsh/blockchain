---

copyright:
  years: 2018, 2019
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

# Initiation à {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #get-started-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private permet aux utilisateurs de déployer des autorités de certification, des services de tri, ainsi que des homologues sur x86, LinuxONE et IBM Z à l'aide de chartes Helm Kubernetes dans un environnement local, tel que {{site.data.keyword.cloud_notm}} Private, et de les connecter à des composants qui sont hébergés dans plusieurs environnements de cloud. {{site.data.keyword.cloud_notm}} Private est une plateforme applicative pour le développement et la gestion d'applications sur site et conteneurisées. Il s'agit d'un environnement intégré pour la gestion de conteneurs qui inclut l'orchestrateur de conteneurs Kubernetes, un registre d'images privé, une console de gestion, ainsi que des infrastructures préfabriquées de surveillance.
{:shortdesc}

Les réseaux de blockchain qui reposent sur [Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/) peuvent être déployés dans presque n'importe quels schémas de configurations illimités pour la prise en charge de nombreux cas d'utilisation. Malgré une telle souplesse, il existe in certain nombre de meilleures pratique, en particulier autour de la **séquence** appropriée pour la configuration et le déploiement des composants réseau.

Ce Guide de déploiement présente la séquence appropriée pour la configuration d'un réseau {{site.data.keyword.blockchainfull}} Platform opérationnel sur {{site.data.keyword.cloud_notm}} Private, en plus des meilleures pratiques et des éléments à prendre en compte lors d'un déploiement. Toutefois, les règles de base s'appliquent toujours si vous prévoyez de définir uniquement une autorité de certification, un service de tri ou un homologue opérationnels. Notez que le service de tri SOLO, qui déploie uniquement un seul noeud de service de tri, n'est pas destiné aux environnements de production. Tout réseau ou canal qui exécute un service SOLO ne peut pas être considéré comme "environnement de production". Toutefois, il est possible de déployer l'homologue et l'autorité de certification dans un environnement de production, en particulier lorsqu'ils sont hautement disponibles.

Le processus de déploiement de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est complexe et il suppose un haut degré d'expertise dans Fabric. Si vous débutez avec Fabric, {{site.data.keyword.blockchainfull_notm}} Platform ou {{site.data.keyword.cloud_notm}} Private, et que vous avez pour objectif de définir un environnement de développement ou une validation de concept, utilisez plutôt un [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about). Notez également que certaines configurations de déploiement potentielles ne sont pas prises en charge. En raison de cette difficulté, on part du principe qu'un expert Fabric est présent tout au long du processus de déploiement et de connexion des composants avant de transférer les tâches administratives à d'autres parties. Le présent guide s'adresse à ces experts, tout comme la documentation disponible pour les composants de la charte Helm en général.
{:important}

## Etape 1 : Préparer votre configuration de réseau

La structure d'un réseau de blockchain doit être dictée par le cas d'utilisation. Ces décisions métier fondamental varient selon les différentes situations, mais prenons le cas de quelques-unes.

* Configuration d'un **environnement de développement**

  Un [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) est conçu pour un tel environnement, mais si vous voulez gérer vos propres composants dans un environnement de développement, vous aurez besoin d'une configuration de base qui se compose d'un service de tri et d'un homologue (avec une autorité de certification pour chaque organisation). Pour chaque organisation, vous pouvez décider d'avoir uniquement un seul service de tri et un seul homologue, comme dans la configuration du [réseau fabcar ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/understand_fabcar_network.html). De la même manière, vous pouvez décider qu'un seul utilisateur admin est nécessaire pour tous ces composants, au lieu de créer des utilisateurs distincts pour chaque composant.

* Déploiement de **composants de production** pour un nouveau réseau ou dans un réseau existant

  les composants de production, et les réseaux de production, présentent des besoins différents des environnements de développement ou des validations de concept. D'un côté, la haute disponibilité devient une priorité. Un service de tri SOLO, qui déploie uniquement un seul service de tri (et est par conséquent un point de défaillance unique) n'est par définition non destiné à la production.

Ce Guide de déploiement ne présente pas chaque itération et configuration réseau possible, mais il fournit des conseils et des règles communes à prendre en compte.
{:note}

## Etape 2 : Configurer un cluster Kubernetes sur {{site.data.keyword.cloud_notm}} Private

Une fois que vous avez décidé de la structure du réseau, configurez un cluster Kubernetes sur {{site.data.keyword.cloud_notm}} Private pour l'adapter à vos cas d'utilisation. Pour plus d'informations, voir [Configuration de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

Vous pouvez également utiliser {{site.data.keyword.IBM_notm}} Secure Service Container comme hôte de {{site.data.keyword.cloud_notm}} Private afin d'optimiser les avantages de sécurité de Secure Service Container pour la protection des données critiques des menaces internes et externes. Pour plus d'informations, voir [Utilisation de {{site.data.keyword.IBM_notm}} Secure Service Container pour {{site.data.keyword.cloud_notm}} Private](/docs/services/howto/ibp-ssc-for-icp.html "Utilisation de {{site.data.keyword.IBM_notm}} Secure Service Container pour {{site.data.keyword.cloud_notm}} Private").
{:note}

## Etape 3 : Configurer vos autorités de certification

Dans les réseaux de blockchain basés sur Fabric, le premier composant qui doit être déployé est une autorité de certification. La raison à cela est que la configuration d'un composant doit comporter au moins une identité utilisateur qui est autorisé à exploiter le composant avant que celui-ci ne soit déployé.

Il peut parfois être difficile de comprendre le plan conceptuel. Un appareil électronique tel qu'un ordinateur portable, par exemple, est souvent accessible à tout le monde lorsqu'il est tout neuf. La première personne qui allume l'appareil est considérée comme "propriétaire" et est donc autorisée à s'enregistrer et à créer des mots de passe que chaque utilisateur doit connaître. Cependant, ce n'est pas ainsi que sont configurés les composants dans les réseaux de blockchain basés sur Fabric. Les homologues et les services de tri doivent comporter des informations d'administration intégrées lorsqu'ils sont déployés pour la première fois.

**Il est recommandé de déployer une autorité de certification par organisation.** Par exemple, si vous déployez trois homologues qui sont associés à une organisation et un service de tri qui est associé à une autre organisation, vous devez déployer deux autorités de certification. Une autorité de certification est pour l'organisation de l'homologue et l'autre est pour l'organisation du service de tri.

Pour éviter un régression infinie des autorités de certification (où chaque autorité de certification doit être liée à une autre autorité de certification, et ainsi de suite pour toujours), les autorités de certification ont un nom d'utilisateur et un mot de passe par défaut qui leur sont associés. Vous indiquez ce nom d'utilisateur et ce mot de passe lorsque l'autorité de certification est déployée. Cette autorité de certification est l'"autorité de certification racine", la racine de confiance de votre composant. Dans les environnements de production, il est recommandé de déployer au moins une autre autorité de certification qui obtient son certificat de cette autorité de certification racine et d'utiliser cette autorité de certification, laquelle est connue en tant que "autorité de certification intermédiaire", pour l'émission de certificats pour les utilisateurs et les composants. Toutefois, dans cette édition, l'utilisation d'autorités de certification intermédiaires n'est pas prise en charge.

Chaque organisation doit disposer d'une autorité de certification pour l'inscription et une autorité de certification TLS.  Lorsque vous déployez une autorité de certification sur {{site.data.keyword.cloud_notm}} Private, une autorité de certification TLS est également déployée dans le même conteneur par défaut. Cette autorité de certification TLS génère et gère vos certificats TLS. L'autorité de certification sur un réseau de plan Starter Plan ou Enterprise ne contient pas une autorité de certification TLS, mais elle contient une autorité de certification qui est utilisée pour l'inscription. Par conséquent, si vous prévoyez de connecter votre homologue à un réseau de plan Starter ou Enterprise, avant de déployer votre homologue, vous devez également [déployer une autorité de certification dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy), laquelle fera office d'autorité de certification TLS pour cet homologue. Consultez également les [prérequis pour la connexion d'un homologue {{site.data.keyword.cloud_notm}} Private à {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-prerequisites). Notez que l'autorité de certification TLS est utilisée uniquement pour l'émission de certificats et elle peut être arrêtée lorsque l'activité est terminée.

Pour plus d'informations sur TLS, voir [Securing Communication With Transport Layer Security (TLS) ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/enable_tls.html "Securing Communication With Transport Layer Security (TLS)") dans la documentation Hyperledger Fabric.

### Préparation des MSP pour le service de tri et les homologues

En raison de l'extrême élaboration du processus {{site.data.keyword.blockchainfull_notm}} Platform pour {{site.data.keyword.cloud_notm}} Private, il est recommandé d'utiliser une seule identité utilisateur admin comme admin pour tous les noeuds de composant réseau pendant la configuration initiale. Cela devrait réduire le nombre d'erreurs de déploiement et de connexion car vous avez la garantie qu'un seul utilisateur peut définir la configuration et les connexions entre les différents composants pour s'assurer de leur bon fonctionnement. Toutefois, il est extrêmement important que chaque composant possède des certificats différents. Il arrive parfois qu'on oublie facilement de les distinguer. L'entité qui signe une proposition de transaction n'est pas l'administrateur de l'homologue mais l'**homologue lui-même**. Par conséquent, l'homologue doit être enregistré et avoir un certificat qu'il joint à chaque action, ainsi qu'une clé privée qu'il peut utiliser pour générer certains types de signatures. Pour plus d'informations sur les identités et les droits dans un réseau de blockchain basé sur Fabric, voir [Identity ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "Identity") et [Membership ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Membership") dans la documentation Fabric.

Le client d'autorité de certification Fabric, qui doit être installé en suivant les instructions de la section [Utilisation d'une autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client), est utilisé pour enregistrer et inscrire des identités. Cette identité correspond à l'administrateur du réseau que vous allez déployer et elle vous permet de modifier les configurations et de connecter les composants entre eux. Si vous voulez transférer l'administration de ces composants à d'autres utilisateurs plus tard, vous pouvez enregistrer et inscrire de nouveaux administrateurs pour ces composants et vous retirer en tant qu'administrateur si nécessaire.

Une fois le service de tri ou l'homologue déployé, un conteneur `init` qui est associé au service de tri ou à l'homologue va utiliser un objet secret Kubernetes afin de créer le MSP pour le composant. Pour savoir comment créer un objet secret, voir la section relative à l'[utilisation d'une autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate). Comme nous l'avons mentionné précédemment, gardez à l'esprit que vous devez configurer une autorité de certification et répéter ce flux pour chaque organisation.

## Etape 4 : Déployer des services de tri et des homologues

Une fois qu'un secret Kubernetes est créé, vous êtes prêt à déployer un composant. Si votre projet est d'établir des canaux, il est recommandé de déployer le service de tri avant les homologues. Assurez-vous d'utiliser des noms d'organisation différents pour tous vos composants.

- **[Déployez le service de tri](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)**. Notez qu'à l'heure actuelle, seul le service de tri SOLO est pris en charge. Vous disposez d'options dans le processus de déploiement relatives au calcul.

- **[Déployez l'homologue pour qu'il soit connecté à un service de tri {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy)**. Un certain nombre d'options pour le déploiement de vos homologues, y compris le type de base de données, peut être spécifié dans la charte Helm. Assurez-vous que l'ID MSP de l'organisation de l'homologue est différent de celui de l'organisation du service de tri.

- **[Déployez l'homologue pour qu'il soit connecté à un réseau {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)**. Le processus de déploiement d'un homologue et sa connexion à un réseau [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) ou [Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise_plan-about) dans {{site.data.keyword.cloud_notm}} est différent car il utilise des profils de connexion et l'interface utilisateur du Moniteur réseau. Notez que l'organisation à laquelle l'homologue appartient doit déjà être jointe à un canal dans le réseau. Encore une fois, assurez-vous que l'ID MSP de l'organisation de l'homologue est différent de celui de l'organisation du service de tri.

## Etapes suivantes

Une fois tous vos noeuds déployés, vous pouvez commencer à les exploiter et à soumettre des transactions. Consultez les liens suivants pour plus d'informations :

- [Utilisation d'une autorité de certification sur {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate)
- [Utilisation d'un service de tri sur {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate)
- [Exploitation des homologues dans {{site.data.keyword.cloud_notm}} Private avec un réseau multi-cloud](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)
- [Exploitation des homologues sur {{site.data.keyword.cloud_notm}} Private avec un réseau de plan Starter ou Enterprise](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)

## Développement de votre réseau

Si vous avez pour objectif de définir un environnement de développement ou une validation de concept, vous devrez ajouter des organisations homologues ai canal système du service de tri qui est créé durant le déploiement du service de tri. Il s'agit d'un processus à plusieurs étapes qui utilise chaque composant et est documenté dans les rubriques correspondantes.

1. Avant d'ajouter une organisation, vous devez la créer et définir son ID MSP. Voir [Utilisation d'une autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer).

2. Une fois l'organisation créée, elle peut être ajoutée au canal système du service de tri. Pour plus d'informations, voir [Utilisation d'un service de tri](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-add-organizations-to-consortium).

3. Lorsqu'une organisation est répertoriée dans le canal système du système de tri, elle est membre du "consortium" et capable de créer un canal d'application, type de canal sur lequel sont effectuées les transactions. Pour plus d'informations sur la création d'un canal, voir [Création d'un canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel).

## Références
{: #get-started-icp-ref}

- [Tutoriel : Exploiter un homologue distribué sur {{site.data.keyword.blockchainfull_notm}} Platform](https://developer.ibm.com/tutorials/operate-distributed-peer-on-ibm-blockchain-platform/)
- [Page d'accueil d'{{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/cloud/private "Page d'accueil d'{{site.data.keyword.cloud_notm}} Private")
- Documentation d'[{{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/kc_welcome_containers.html)

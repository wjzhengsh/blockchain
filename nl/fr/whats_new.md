---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Nouveautés
{: #whats-new}

## 8 Février 2019
{: #whats-new-2-08-2019}

{{site.data.keyword.blockchainfull}} Platform présente une version 2.0 bêta gratuite, génération suivante des solutions {{site.data.keyword.blockchainfull_notm}} Platform qui vous permet de déployer, exploiter et surveiller votre réseau de blockchain. {{site.data.keyword.blockchainfull_notm}} Platform version 2.0 bêta gratuite comporte une nouvelle console d'interface utilisateur, laquelle peut être utilisée pour déployer et gérer des composants de blockchain dans votre propre cluster {{site.data.keyword.IBM_notm}} Kubernetes Service on {{site.data.keyword.cloud_notm}}. {{site.data.keyword.blockchainfull_notm}} Platform version 2.0 bêta gratuite vous permet de :

**Générer votre réseau plus vite et plus facilement en toute transparence**

*	Intégration sans problème entre le développement de contrat intelligent (par opposition au code) et la gestion de réseau
*	Simplified DevOps vous permet de passer du développement au test en production depuis une simple console.
*	Possibilité d'écrire des contrats intelligents en Javascript, Java et en Go

**Exploiter et gouverner des réseaux avec un contrôle total**

*	Déployez uniquement les composants de blockchain dont vous avez besoin (homologue, service de tri, autorité de certification)
*	La nouvelle console vous permet de gérer les composants réseau à un seul emplacement, peu importe où ils sont déployés
*	Conservez le contrôle intégral de vos identités, registres et contrats intelligents

**Etendre les réseaux distribués en toute simplicité grâce à la nouvelle souplesse multi-cloud**

*	Connectez-vous à des noeuds de n'importe quel environnement (sur site, public, clouds hybrides)
*	Connectez facilement un seul homologue à plusieurs réseaux industriels
*	Démarrez petit, payez pour ce que vous consommez et développez-vous sans investissement initial, et mettez-vous facilement à niveau avec Kubernetes

- Des informations supplémentaires concernant la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform sont disponibles dans la section [A propos de {{site.data.keyword.blockchainfull_notm}} Platform version 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Les instructions relatives au déploiement de la version 2.0 bêta gratuite dans un cluster {{site.data.keyword.IBM_notm}} Kubernetes Service sont disponibles dans la section [Initiation à {{site.data.keyword.blockchainfull_notm}} Platform version 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- De nouveaux tutoriels relatifs à l'utilisation de {{site.data.keyword.blockchainfull_notm}} Platform version 2.0 bêta gratuite sont disponibles dans la sous-section **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** sous la catégorie **Procédures détaillées**.
  * Le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) vous guide tout au long du processus d'hébergement d'un réseau par la création d'un service de tri et d'un homologue.
  * Le [tutoriel Rejoindre un réseau](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) vous explique comment rejoindre un réseau existant en créant un homologue et en le joignant à un canal.
  * [Déployer un contrat intelligent sur le réseau ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts): Ce tutoriel fournit des informations sur l'écriture d'un contrat intelligent et son déploiement sur votre réseau.
- L'offre {{site.data.keyword.blockchainfull_notm}} Platform version 2.0 bêta gratuite repose sur Hyperledger Fabric version 1.4 et elle prend en charge le protocole gossip entre homologues, la reconnaissance de service et les données privées. Pour en savoir plus sur la configuration des données privées sur votre réseau, voir la rubrique [](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).

- L'extension {{site.data.keyword.blockchainfull_notm}} Visual Studio Code est disponible à partir de Visual Studio Code Marketplace. Les développeurs peuvent utiliser cette extension pour créer, tester et déployer des contrats intelligents dans une instance d'Hyperledger Fabric. L'extension est compatible avec Hyperledger Fabric versions 1.3 et suivantes. Pour plus d'informations sur l'utilisation de l'extension VS Code, voir [Outils pour contrats intelligents](/docs/services/blockchain/vscode-extension.html#develop-vscode).  

Des améliorations ont été apportées à la table des matières dans laquelle toutes les rubriques d'initiation sont désormais regroupées dans une section appelée **Tutoriels d'initiation**. De plus, chacune des offres {{site.data.keyword.blockchainfull_notm}} Platform est décrite dans les sous-sections **A propos de {{site.data.keyword.blockchainfull_notm}} Platform** et figurent désormais sous la section **LEARN**.

**Remarque :** Les crédits de cloud gratuits ne sont plus disponibles pour les utilisateurs de plan Starter.

## 7 Décembre 2018
{: #whats-new-12-7-2018}

Les rubriques *Exploitation d'un réseau de plan Starter* et *Exploitation d'un réseau de plan Enterprise* sont combinées et remplacées par un tutoriel unique [Utilisation du Moniteur réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## 27 novembre 2018
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform présente {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private. {{site.data.keyword.cloud_notm}} Private est une plateforme d'application pour le développement et la gestion d'applications conteneurisées basées sur Kubernetes, qui permet aux utilisateurs de déployer des autorités de certification, des services de tri, ainsi que des homologues sur x86, LinuxONE et IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private repose sur Hyperledger Fabric v1.2.1 et est déployé à l'aide de chartes Helm Kubernetes.. Une fois que vous avez installé la charte Helm, vous pouvez la trouver sous forme d'un service intégré dans le catalogue {{site.data.keyword.cloud_notm}} Private. Notez que [cette offre](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) convient davantage aux utilisateurs Fabric expérimentés.

Pour plus d'informations sur {{site.data.keyword.cloud_notm}} Private, voir la [documentation {{site.data.keyword.cloud_notm}} Private v3.1.0![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0 documentation").

L'édition de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private marque également la fin du programme IBM Blockchain Platform Remote Peer (bêta). Il est toujours possible de déployer un homologue dans {{site.data.keyword.cloud_notm}} Private ou dans AWS et de le connecter à un réseau de plan Starter ou Enterprise. Pour plus d'informations, voir les sections relatives au [déploiement d'un homologue pour un réseau de plan Starter ou Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) et au [déploiement d'un homologue dans AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). Ces homologues sont considérés comme étant des homologues **distribués** au lieu d'homologues distants car le déploiement est géré par le client et par conséquent il n'y a aucun emplacement central de gestion à distance.

Cette édition introduit également des améliorations apportées à la table des matières de la documentation. Si vous êtes un utilisateur Starter ou Enterprise, votre contenu est toujours accessible dans les sections **LEARN**, **Procédures détaillées**, **Référence** et **Aide**, et les liens sont toujours identiques. Ils peuvent simplement se trouver dans des sous-sections différentes de la table des matières.

## 13 novembre 2018
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform présente {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS).

{{site.data.keyword.blockchainfull_notm}} Platform for AWS vous permet d'exécuter des homologues dans le cloud AWS et de connecter les homologues à des réseaux de blockchain existants dans {{site.data.keyword.cloud_notm}}. Vos homologues dans AWS peuvent optimiser le profil de connexion et d'autres composants de blockchain des réseaux existants, tout en vous offrant davantage de souplesse pour colocaliser vos homologues avec d'autres applications situées à l'extérieur de {{site.data.keyword.cloud_notm}}. Pour plus d'informations, voir  [A propos de {{site.data.keyword.blockchainfull_notm}} Platform pour Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 4 octobre 2018
{: #whats-new-10-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform met à jour le plan Starter depuis la version 1.1.0 d'Hyperledger Fabric vers la version 1.2.1. Pour plus de détails, voir [A propos du plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Important :** La version 1.2.1 de Fabric n'est actuellement pas compatible avec la version bêta de l'homologue distant, qui utilise un homologue en version 1.1.0. Les réseaux de plan Starter, qui sont créés avant le 4 octobre 2018 et sont basés sur la version 1.1.0 de Fabric, ne sont pas concernés et ils peuvent toujours être utilisés avec la version bêta de l'homologue distant. {{site.data.keyword.blockchainfull_notm}} Platform mettra à jour la version bêta de l'homologue distant pour qu'elle puisse utiliser l'homologue en version 1.2.1, qui sera compatible avec les nouveaux réseaux de plan Starter, qui sont au niveau de version 1.2.1 de Fabric, et avec les réseaux de plan Enterprise, qui sont qui sont au niveau de version 1.1.0 de Fabric. Tant que la version bêta de l'homologue distant n'est pas mise à jour vers la version 1.2.1 de Fabric, n'essayez pas de déployer un homologue distant en version 1.1.0 avec un nouveau réseau de plan Starter en version 1.2.1.

## 4 septembre 2018
{: #whats-new-9-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform présente la version bêta de l'offre d'homologue distant. Cette offre est basée sur la version 1.1.0 d'Hyperledger Fabric. En utilisant l'homologue distant, vous pouvez exécuter des noeuds homologues {{site.data.keyword.blockchainfull_notm}} Platform dans votre propre environnement de cloud {{site.data.keyword.cloud_notm}} Private ou Amazon Web Services (AWS). Pour plus d'informations, voir [A propos des homologues distants](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).

## 15 juin 2018
{: #whats-new-6-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform annonce la disponibilité générale du plan Starter. Pour plus de détails, voir [A propos du plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 15 mai 2018
{: #whats-new-5-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform met à niveau le plan Enterprise de la version 1.0.0 d'Hyperledger Fabric vers la version 1.1.0. Pour plus d'informations, voir [A propos du plan Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## 18 mars 2018
{: #whats-new-3-18-2018}

{{site.data.keyword.blockchainfull_notm}} Platform présente la version bêta du plan Starter. Le plan Starter vous offre un environnement de développement et de tests pour découvrir et apprendre à utiliser un réseau {{site.data.keyword.blockchainfull_notm}} Platform. Pour plus de détails, voir [A propos du plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 11 août 2017
{: #whats-new-8-11-2017}

{{site.data.keyword.blockchainfull_notm}} Platform remplace
{{site.data.keyword.blockchainfull_notm}} on Cloud et présente le plan Enterprise. Pour plus d'informations, voir [A propos du plan Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Nouveautés
{: #whatsnew}

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 7 Décembre 2018

Les rubriques *Exploitation d'un réseau de plan Starter* et *Exploitation d'un réseau de plan Enterprise* sont combinées et remplacées par un tutoriel unique [Utilisation du Moniteur réseau](v10_dashboard.html).

## 27 novembre 2018

{{site.data.keyword.blockchainfull}} Platform présente {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private (ICP). ICP est une plateforme d'application pour le développement et la gestion d'applications conteneurisées basées sur Kubernetes, qui permet aux utilisateurs de déployer des autorités de certification, des services de tri, ainsi que des homologues sur x86, LinuxONE et IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for ICP repose sur Hyperledger Fabric v1.2.1 et est déployé à l'aide de chartes Helm Kubernetes. Une fois que vous avez installé la charte Helm, vous pouvez la trouver sous forme d'un service intégré dans le catalogue ICP. Notez que [cette offre](ibp-for-icp-about.html) convient davantage aux utilisateurs Fabric expérimentés.

Pour plus d'informations sur ICP, voir la [documentation {{site.data.keyword.cloud_notm}} Private v3.1.0![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0 documentation").

L'édition de {{site.data.keyword.blockchainfull_notm}} Platform for ICP marque également la fin du programme IBM Blockchain Platform Remote Peer (bêta). Il est toujours possible de déployer un homologue dans ICP ou dans AWS et de le connecter à un réseau de plan Starter ou Enterprise. Pour plus d'informations, voir les sections relatives au [déploiement d'un homologue pour un réseau de plan Starter ou Enterprise](howto/peer_deploy_ibp.html) et au [déploiement d'un homologue dans AWS](howto/remote_peer_aws.html). Ces homologues sont considérés comme étant des homologues **distribués** au lieu d'homologues distants car le déploiement est géré par le client et par conséquent il n'y a aucun emplacement central de gestion à distance.

Cette édition introduit également des améliorations apportées à la table des matières de la documentation. Si vous êtes un utilisateur Starter ou Enterprise, votre contenu est toujours accessible dans les sections **LEARN**, **Procédures détaillées**, **Référence** et **Aide**, et les liens sont toujours identiques. Ils peuvent simplement se trouver dans des sous-sections différentes de la table des matières.

## 13 novembre 2018

{{site.data.keyword.blockchainfull_notm}} Platform présente {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS).

{{site.data.keyword.blockchainfull_notm}} Platform for AWS vous permet d'exécuter des homologues dans le cloud AWS et de connecter les homologues à des réseaux de blockchain existants dans {{site.data.keyword.cloud_notm}}. Vos homologues dans AWS peuvent optimiser le profil de connexion et d'autres composants de blockchain des réseaux existants, tout en vous offrant davantage de souplesse pour colocaliser vos homologues avec d'autres applications situées à l'extérieur de {{site.data.keyword.cloud_notm}}. Pour plus d'informations, voir  [A propos de {{site.data.keyword.blockchainfull_notm}} Platform pour Amazon Web Services](howto/remote_peer.html).

## 4 octobre 2018

{{site.data.keyword.blockchainfull_notm}} Platform met à jour le plan Starter depuis la version 1.1.0 d'Hyperledger Fabric vers la version 1.2.1. Pour plus de détails, voir [A propos du plan Starter](starter_plan.html).

**Important :** La version 1.2.1 de Fabric n'est actuellement pas compatible avec la version bêta de l'homologue distant, qui utilise un homologue en version 1.1.0. Les réseaux de plan Starter, qui sont créés avant le 4 octobre 2018 et sont basés sur la version 1.1.0 de Fabric, ne sont pas concernés et ils peuvent toujours être utilisés avec la version bêta de l'homologue distant.  {{site.data.keyword.blockchainfull_notm}} Platform mettra à jour la version bêta de l'homologue distant pour qu'elle puisse utiliser l'homologue en version 1.2.1, qui sera compatible avec les nouveaux réseaux de plan Starter, qui sont au niveau de version 1.2.1 de Fabric, et avec les réseaux de plan Enterprise, qui sont qui sont au niveau de version 1.1.0 de Fabric. Tant que la version bêta de l'homologue distant n'est pas mise à jour vers la version 1.2.1 de Fabric, n'essayez pas de déployer un homologue distant en version 1.1.0 avec un nouveau réseau de plan Starter en version 1.2.1.

## 4 septembre 2018

{{site.data.keyword.blockchainfull_notm}} Platform présente la version bêta de l'offre d'homologue distant. Cette offre est basée sur la version 1.1.0 d'Hyperledger Fabric. En utilisant l'homologue distant, vous pouvez exécuter des noeuds homologues {{site.data.keyword.blockchainfull_notm}} Platform dans votre propre environnement de cloud IBM Cloud Private (ICP) ou Amazon Web Services (AWS). Pour plus d'informations, voir [A propos des homologues distants](howto/remote_peer.html).

## 15 juin 2018

{{site.data.keyword.blockchainfull_notm}} Platform annonce la disponibilité générale du plan Starter. Pour plus de détails, voir [A propos du plan Starter](starter_plan.html).

## 15 mai 2018

{{site.data.keyword.blockchainfull_notm}} Platform met à niveau le plan Enterprise de la version 1.0.0 d'Hyperledger Fabric vers la version 1.1.0. Pour plus d'informations, voir [A propos du plan Enterprise](enterprise_plan.html).

## 18 mars 2018

{{site.data.keyword.blockchainfull_notm}} Platform présente la version bêta du plan Starter. Le plan Starter vous offre un environnement de développement et de tests pour découvrir et apprendre à utiliser un réseau {{site.data.keyword.blockchainfull_notm}} Platform. Pour plus de détails, voir [A propos du plan Starter](starter_plan.html).

## 11 août 2017

{{site.data.keyword.blockchainfull_notm}} Platform remplace
{{site.data.keyword.blockchainfull_notm}} on Cloud et présente le plan Enterprise. Pour plus d'informations, voir [A propos du plan Enterprise](enterprise_plan.html).

---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# A propos des homologues distants
{: #remote-peer-overview}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Vous pouvez exécuter des homologues distants {{site.data.keyword.blockchainfull}} Platform sur {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> après les avoir connectés à un réseau de blockchain existant. L'exécution des homologues en dehors d'{{site.data.keyword.cloud_notm}} offre davantage de souplesse pour agrandir ou rejoindre un réseau de blockchain tout en bénéficiant d'un réseau existant dans {{site.data.keyword.cloud_notm}}. Les homologues distants optimisent les autorités de certification (CA) et le service de tri sur la plateforme, mais ils vous permettent de colocaliser votre homologue avec d'autres applications extérieures à {{site.data.keyword.cloud_notm}}.
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} Private (ICP) est une plateforme basée sur Kubernetes pour la génération d'un cloud privé dans un environnement sur site. Vous pouvez utiliser ICP pour exécuter un homologue distant et connecter ce dernier à un réseau de blockchain sur {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP optimise le stockage, la sécurité, la consignation et les services de support d'ICP afin que vous puissiez gérer vos homologues distants dans votre environnement sur site. Pour plus d'informations sur ICP, voir la [documentation {{site.data.keyword.cloud_notm}} Private ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "Documentation {{site.data.keyword.cloud_notm}} Private").  

**Remarque : ** {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer est actuellement une offre en version bêta qui convient pour une évaluation et un expérimentation. **Cette version bêta n'est pas destinée pour un usage en production.** Pour plus de détails sur l'accès et pour plus d'informations, voir [licence et tarification](#remote-peer-license-pricing).

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

L'édition bêta prend en charge la plateforme cloud d'{{site.data.keyword.cloud_notm}} Private (ICP), version 2.1.0.3. Notez que le réseau sur {{site.data.keyword.blockchainfull_notm}} Platform et les noeuds de l'homologue distant dans ICP doivent s'exécuter sans la même **version de Fabric 1.1**.

## Remarques
{: #remote-peer-limitations}

Un homologue distant n'a pas accès à toutes les fonctionnalités ou à la prise en charge des homologues qui sont hébergés sur {{site.data.keyword.blockchainfull_notm}} Platform. Avant d'exécuter des homologues distants, vérifiez que vous comprenez les restrictions et limitations suivantes :
- Les homologues distants qui s'exécutent dans d'autres environnements de cloud ne sont pas visibles dans le Moniteur de réseau du réseau de blockchain sur {{site.data.keyword.cloud_notm}}.
- Les homologues distants ne peuvent pas être adressés à l'aide de l'interface utilisateur swagger dans l'interface utilisateur du Moniteur réseau. 
- Vous êtes responsable de la gestion de surveillance de l'intégrité, de la sécurité, de la journalisation et de l'utilisation des ressources de vos homologues distants.
- Vous pouvez connecter vos homologues distants uniquement aux réseaux de blockchain au niveau Hyperledger Fabric v1.1.
- Le type de base de données de l'homologue distant doit correspondre au type de base de données de votre réseau blockchain, LevelDB ou CouchDB.
- L'interface CouchDB Fauxton n'est pas disponible sur l'homologue distant.
- [Gossip](../glossary.html#gossip) pour les homologues distants n'est pas pour l'instant pris en charge.


## Prérequis
{: #remote-peer-prereq}

Pour utiliser un homologue distant, vous devez disposer d'une organisation qui est membre d'un réseau de plan Starter ou Enterprise sur {{site.data.keyword.blockchainfull_notm}} Platform. L'homologue distant optimise les noeuds finaux d'API, les autorités de certification Hyperledger Fabric et le service de tri du réseau de plan {{site.data.keyword.blockchainfull_notm}} Platform à exploiter. Si vous n'êtes membre d'aucun un réseau de blockchain, vous devez créer ou rejoindre un réseau. Pour plus d'informations, voir [Création d'un réseau de plan](../get_start.html#creating-a-network) ou [Rejoindre un réseau](../get_start.html#joining-a-network).


## Licence et tarification
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->Pour accéder aux homologues distants pour l'exécution sur ICP, voir la section relative à la [licence et la tarification pour les homologues distants sur ICP](remote_peer_icp.html#license-pricing-icp "Licence et tarification pour les homologues distants sur ICP"). Les licences pour l'édition bêta des homologues distants sont gratuites.<!--[AWS] for both platforms.--> Par la suite, une offre de disponibilité générale (GA) remplace l'édition bêta.

La migration depuis une édition bêta n'est pas prise en charge. Vous devez télécharger et installer les nouveaux homologues distants pour une utiliser de l'offre GA lorsqu'elle est disponible. Vous pouvez ensuite joindre de nouveaux homologues distants aux mêmes canaux du même réseau que vos homologues distants bêta.

**Remarque :** Pour exploiter un homologue distant, vous devez disposer d'une organisation qui appartient au réseau d'un plan Starter ou Enterprise sur {{site.data.keyword.blockchainfull_notm}} Platform. Cela implique que vous ou un autre membre du réseau doivent payer les [frais d'appartenance](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) IBM Blockchain pour votre organisation. Pour plus d'informations sur le paiement de ces frais, voir [Mode de paiement](paying_mode.html).  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## Déploiement d'un homologue distant
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

Le diagramme suivant décrit les étapes permettant de déployer un homologue distant {{site.data.keyword.blockchainfull_notm}} Platform sur ICP. Pour plus d'informations sur la façon de déployer des homologues distants sur ICP, voir la section relative au [déploiement d'homologues distants dans {{site.data.keyword.cloud_notm}} Private](remote_peer_icp.html "Déploiement d'homologues distants dans {{site.data.keyword.cloud_notm}} Private").

![Flux de déploiement d'homologue distant dans ICP](../images/remote_peer_ICP_flow.png "Flux de déploiement d'homologue distant dans ICP")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*Figure 1. Flux de déploiement d'homologue distant dans ICP*


## Exploitation d'un homologue distant
{: #operate-remote-peer}

Après que vous avez déployé l'homologue distant, vous devez effectuer quelques tâches supplémentaires pour que votre homologue distant puisse soumettre des transactions au réseau. Ces étapes incluent l'ajout de votre organisation à un canal, l'association de votre homologue distant au canal, l'installation de code blockchain sur votre homologue distant, l'instanciation de code blockchain sur le canal, ainsi que la connexion d'applications à votre homologue distant.
Pour plus d'informations, voir <!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[Exploitation d'homologues distants dans {{site.data.keyword.cloud_notm}} Private](remote_peer_operate_icp.html#remote-peer-operate).

## Hébergement de données
{: #data-residency}

Les exigences en termes d'hébergement de données sont des restrictions qui gouvernent l'emplacement où les données peuvent résider. L'exigence la plus courante sur l'hébergement de données est associée aux réglementations de certains pays, qui demandent que toutes les données client qui sont traitées et stockées sur un système informatique doivent demeurer à l'intérieur des frontières d'un pays donné. De même, certaines entreprises dans des secteurs extrêmement régulés, comme le gouvernement, la santé et les services financiers, exigent que l'ensemble de leurs données soient entièrement stockées derrière leur pare-feu. En outre, certaines entreprises appliquent des règles d'entreprise où certains types de données, généralement des informations identifiant la personne, doivent demeurer privées et derrière leur pare-feu d'entreprise. Par conséquent, pour appliquer l'hébergement de données, tous les composants du réseau de blockchain doivent faire partie du même [canal](../glossary.html#channel) et se trouver à l'intérieur des frontières d'un seul pays.

Pour respecter les exigences d'hébergement de données, il est important de comprendre l'architecture Hyperledger Fabric qui est sous-jacente à {{site.data.keyword.blockchainfull_notm}} Platform. L'architecture est centrée autour de trois composants clé : service de tri, autorité de certification et homologue. Un homologue reçoit les mises à jour de l'état de tri sous la forme de blocs du service de tri et gère l'état et la registre. Par conséquent, un homologue et un service de tri ont une relation directe. Le registre contient les valeurs les plus récentes de toutes les clés et les données incluses dans les journaux de transaction.

En outre, les applications client utilisent les [logiciels SDK Fabric](../v10_application.html#using-the-fabric-sdks) pour envoyer des transactions aux homologues et au service de tri. Ces transactions incluent les données [read-write set ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "Read-Write set semantics"), qui contiennent les paires clé-valeur sur le registre.

Si l'hébergement de données dans le pays est une exigence de votre entreprise, le service de tri, les noeuds homologues et les applications client doivent résider dans le même pays. Lorsqu'un réseau {{site.data.keyword.blockchainfull_notm}} Platform est créé dans {{site.data.keyword.cloud_notm}}, vous avez la possibilité de choisir un emplacement pour le réseau. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Pour plus d'informations sur les régions et les emplacements, voir [Régions et emplacements d'{{site.data.keyword.blockchainfull_notm}} Platform](../reference/ibp_regions.html). Pour appliquer l'hébergement de données dans l'un de ces pays, votre homologue distant doit résider dans le même pays que l'emplacement du réseau {{site.data.keyword.blockchainfull_notm}} Platform.

Si le réseau {{site.data.keyword.blockchainfull_notm}} Platform contient un homologue distant et des noeuds de service de tri aux frontières de pays, vous pouvez utiliser des canaux pour séparer les données dans un sous-ensemble d'homologues sur le réseau. Les noeuds de tri sont toujours situés dans le centre de données que vous avez sélectionné pour héberger le réseau. Il n'est pas possible d'avoir des programmes de tri entre les frontières. Les homologues peuvent cependant se situer dans le centre de données ou sur un site distant à l'extérieur de {{site.data.keyword.cloud_notm}}. Par conséquent, pour appliquer l'hébergement de données, un canal peut être créé dans lequel le programme de tri et tous les homologues, qu'il s'agisse d'homologues locaux dans le centre de données ou d'homologues distants, résident dans le même pays. De cette manière, toutes les données du programme de tri et les homologues partagent des séjours au sein des frontières d'un seul pays. D'autres canaux peuvent encore exister si nécessaire, lorsque le programme de tri et les homologues distants sont situés sur les frontières de pays et que l'hébergement de données n'est pas obligatoire.

A l'avenir, la nouvelle technologie dans Hyperledger Fabric améliorera la capacité à réaliser d'autres hébergements de données l'étranger en utilisant des [collectes de données privées ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Collectes de données privées") et zéro connaissance.

- Une collecte de données privées garantit que les données privées sont partagés homologue par homologue (via le protocole gossip) sur des homologues autorisés uniquement pour voir si, par exemple, des homologues se trouvent entre les frontières d'un pays. Les données sont stockées dans une base de données privée sur l'homologue. Le service de tri n'est pas impliqué ici et il ne voit pas les données privées. Un hachage de ces données est écrit dans les registres de chaque homologue sur le canal. Le hachage qui est utilisé pour la validation d'état fait office de preuve de la transaction, et il peut être utilisé à des fins d'audit.

- Une preuve ZKP (Zero-Knowledge Proof) permet à un “prouveur” de garantir à un “vérificateur” qu'ils ont la connaissance d'une valeur confidentielle sans révéler la valeur confidentielle elle-même. Il s'agit d'un moyen de montrer que vous savez quelque chose qui répond à une instruction sans afficher ce que vous savez.

Pour plus d'informations sur ces technologies, consultez le livre blanc sur les [transactions privées et confidentielles avec Hyperledger Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transactions privées et confidentielles avec Hyperledger Fabric").

## Support
{: #remote-peer-support}

L'édition bêta de l'offre {{site.data.keyword.blockchainfull_notm}} Remote Peer est destinée à l'exploration, au développement et aux tests. **N'utilisez pas cette édition pour la production.** {{site.data.keyword.blockchainfull_notm}} Platform n'assure pas de support pour cette édition. Si vous rencontrez des problèmes liés à votre homologue distant, voir les [ressources de blockchain et les forums de support](../v10_dashboard.html#support-forums). Vous pouvez également afficher les ressources de support à l'écran **Obtenir de l'aide** du Moniteur réseau.  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

Pour les problèmes liés à {{site.data.keyword.cloud_notm}} Private, aux offres ICP [support numérique gratuit ICP et support Community Edition ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "Support numérique gratuit ICP et support Community Edition").

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

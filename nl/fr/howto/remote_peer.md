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

# A propos d'{{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services
{: #remote-peer-overview}

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**Remarque :** Le programme (bêta) Homologue distant {{site.data.keyword.blockchainfull}} Platform sur ICP est terminé. Si vous souhaitez néanmoins exécuter des homologues dans votre environnement ICP, utilisez plutôt l'offre **{{site.data.keyword.blockchainfull_notm}} Platform for ICP**. Pour plus d'informations, voir [A propos des homologues dans ICP](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-peer).

Vous pouvez exécuter l'homologue {{site.data.keyword.blockchainfull_notm}} Platform dans AWS Cloud une fois que vous l'avez connecté à un réseau de blockchain existant dans {{site.data.keyword.cloud_notm}}. L'exécution des homologues en dehors d'{{site.data.keyword.cloud_notm}} offre davantage de souplesse pour agrandir ou rejoindre un réseau de blockchain tout en bénéficiant d'un réseau existant dans {{site.data.keyword.cloud_notm}}. Les homologues optimisent les autorités de certification (CA) et le service de tri sur la plateforme, mais ils vous permettent de colocaliser votre homologue avec d'autres applications extérieures à {{site.data.keyword.cloud_notm}}.
{:shortdesc}


## Remarques
{: #remote-peer-limitations}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS n'a pas accès à toutes les fonctionnalités ou à la prise en charge des homologues qui sont hébergés sur  {{site.data.keyword.blockchainfull_notm}} Platform. Avant d'exécuter {{site.data.keyword.blockchainfull_notm}} Platform for AWS, vérifiez que vous comprenez les restrictions et limitations suivantes : 
- Les homologues qui s'exécutent dans d'autres environnements de cloud ne sont pas visibles dans le Moniteur de réseau du réseau de blockchain sur {{site.data.keyword.cloud_notm}}.
- Les homologues qui s'exécutent sur {{site.data.keyword.blockchainfull_notm}} Platform for AWS ne peuvent pas être adressés à l'aide de l'interface utilisateur swagger dans l'interface utilisateur du Moniteur réseau. 
- Vous êtes responsable de la gestion de surveillance de l'intégrité, de la sécurité, de la journalisation et de l'utilisation des ressources de vos noeuds homologues {{site.data.keyword.blockchainfull_notm}} Platform for AWS.
- Vous pouvez connecter vos homologues {{site.data.keyword.blockchainfull_notm}} Platform for AWS uniquement aux réseaux de blockchain au niveau Fabric v1.1 ou v1.2.1. Pour connaître votre version Fabric, ouvriez la [fenêtre Préférences réseau](/docs/services/blockchain/v10_dashboard.html#network-preferences) dans votre Moniteur réseau.
- Le type de base de données de l'homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS doit correspondre au type de base de données de votre réseau blockchain, LevelDB ou CouchDB.
- L'interface CouchDB Fauxton n'est pas disponible sur l'homologue AWS.
- [Gossip](/docs/services/blockchain/glossary.html#gossip) pour les homologues AWS n'est pas pour l'instant pris en charge. Cela suppose que les fonctions Fabric qui dépendent de gossip, comme les [données privées ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "données privées") et la [reconnaissance de service![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "reconnaissance de service"), ne sont également pas pris en charge.

## Prérequis
{: #remote-peer-prereq}

Pour utiliser un homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS, vous devez disposer d'une organisation qui est membre d'un réseau de plan Starter ou Enterprise sur {{site.data.keyword.blockchainfull_notm}} Platform. L'homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS optimise les noeuds finaux d'API, les autorités de certification Hyperledger Fabric et le service de tri du réseau {{site.data.keyword.blockchainfull_notm}} Platform à exploiter. Si vous n'êtes membre d'aucun un réseau de blockchain, vous devez créer ou rejoindre un réseau. Pour plus d'informations, voir [Création d'un réseau de plan](/docs/services/blockchain/get_start.html#creating-a-network) ou [Rejoindre un réseau](/docs/services/blockchain/get_start.html#joining-a-network).

## Licence et tarification
{: #remote-peer-license-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS est actuellement proposé dans l'édition Community, gratuitement ; Ultérieurement, IBP for AWS pourrait devenir un modèle Bring-Your-Own-License (BYOL), ce qui impliquera l'achat d'une licence auprès d'IBM.

**Remarque :** Pour exploiter un homologue AWS, vous devez disposer d'une organisation qui appartient au réseau d'un plan Starter ou Enterprise sur {{site.data.keyword.blockchainfull_notm}} Platform. Cela implique que vous ou un autre membre du réseau doivent payer les {{site.data.keyword.blockchainfull_notm}} [frais d'appartenance](/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) pour votre organisation. Pour plus d'informations sur le paiement de ces frais, voir [Mode de paiement](/docs/services/blockchain/howto/paying_mode.html).


## Déploiement d'un homologue AWS
{: #deploy-remote-peer}

Utilisez le [modèle de démarrage rapide AWS ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/ "Quick Start Template") pour déployer facilement {{site.data.keyword.blockchainfull_notm}} Platform for AWS. Pour plus d'informations, voir [{{site.data.keyword.blockchainfull_notm}} Platform for AWS Quick Start Deployment Guide ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://s3.amazonaws.com/aws-quickstart/quickstart-ibm-fabric/doc/ibm-blockchain-platform-for-aws.pdf "IBM Blockchain Platform for AWS Quick Start Reference Deployment").

Pour plus de détails sur le déploiement de {{site.data.keyword.blockchainfull_notm}} Platform for AWS, voir [Déploiement d'homologues dans Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html "Déploiement d'homologues dans Amazon Web Services").

Le diagramme suivant décrit le processus de déploiement d'un homologue {{site.data.keyword.blockchainfull_notm}} Platform for AWS.

<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_AWS_flow.png" width="750" alt="Cliquez sur une zone pour afficher davantage de détails sur le processus." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Configurer ou accéder à AWS" title="Configurer ou accéder" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Créer une paire de clés" title="Créer une paire de clés" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="remote_peer_aws.html#prerequisites-aws" alt="Créer ou rejoindre un réseau" title="Créer ou rejoindre un réseau" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Rejoindre un canal" title="Rejoindre un canal" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="remote_peer_aws.html#aws-register-peer" alt="Enregistrer une identité homologue" title="Enregistrer une identité homologue" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="remote_peer_aws.html#aws-network-endpoints" alt="Extraire les informations de configuration homologue" title="Extraire les informations de configuration homologue" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Cliquer sur le lien" title="Cliquer sur le lien" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Configurer des instances homologues" title="Configurer des instances homologues" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-test" alt="Vérifier le déploiement" title="Vérifier le déploiement" shape="rect" coords="443.95, 258.43, 570.53, 333.48" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Utiliser le logiciel SDK Fabric" title="Utiliser le logiciel SDK Fabric" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="remote_peer_operate_aws.html#aws-peer-cli-operate" alt="Utiliser l'interface CLI des outils Fabric" title="Utiliser l'interface CLI des outils Fabric" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*Figure 1. Flux de déploiement de {{site.data.keyword.blockchainfull_notm}} Platform for AWS sur AWS*


## Exploitation d'un homologue AWS
{: #operate-remote-peer}

Après que vous avez déployé l'homologue, vous devez effectuer quelques tâches supplémentaires pour que votre homologue puisse soumettre des transactions au réseau. Ces étapes opérationnelles incluent l'ajout de votre organisation à un canal, l'association de votre homologue au canal, l'installation de code blockchain sur votre homologue, l'instanciation de code blockchain sur le canal, ainsi que la connexion d'applications à votre homologue.
Pour plus d'informations, voir [Utilisation des homologues dans Amazon Web Service](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-operate-aws).

## Hébergement de données
{: #data-residency}

Etant donné que les réseaux de blockchain dépendent du type de données qui sont traitées, des étapes supplémentaires sont parfois nécessaires pour assurer la sécurité de certains types de données. L'exigence la plus courante sur l'hébergement de données est associée aux réglementations de certains pays, qui demandent que toutes les données qui sont traitées et stockées sur un système informatique doivent demeurer à l'intérieur des frontières d'un pays donné. De même, certaines entreprises dans des secteurs extrêmement régulés, comme le gouvernement, la santé et les services financiers, exigent que les données soient entièrement stockées derrière leur pare-feu. Par conséquent, pour appliquer l'hébergement de données, tous les composants du réseau de blockchain doivent faire partie du même [canal](/docs/services/blockchain/glossary.html#channel) et se trouver à l'intérieur des frontières d'un seul pays.

Pour respecter les exigences d'hébergement de données, il est important de comprendre l'architecture Hyperledger Fabric qui est sous-jacente à {{site.data.keyword.blockchainfull_notm}} Platform. L'architecture est centrée autour de trois composants clé : autorité de certification, service de tri et homologue. Un homologue reçoit les mises à jour de l'état de tri sous la forme de blocs du service de tri et gère l'état et la registre. Par conséquent, un homologue et un service de tri ont une relation directe. Le registre contient les valeurs les plus récentes de toutes les clés et les données incluses dans les journaux de transaction.

En outre, les applications client utilisent les [logiciels SDK Fabric](/docs/services/blockchain/v10_application.html#using-the-fabric-sdks) pour envoyer des transactions aux homologues et au service de tri. Ces transactions incluent les données [read-write set ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Read-Write set semantics"), qui contiennent les paires clé-valeur sur le registre.

Si l'hébergement de données dans le pays est une exigence pour votre entreprise, le service de tri, les homologues et les applications client doivent résider dans le même pays. Lorsqu'un réseau {{site.data.keyword.blockchainfull_notm}} Platform est créé dans {{site.data.keyword.cloud_notm}}, vous avez la possibilité de choisir un emplacement pour le réseau. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Pour plus d'informations sur les régions et les emplacements, voir [Régions et emplacements d'{{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/reference/ibp_regions.html). Pour appliquer l'hébergement de données dans l'un de ces pays, votre homologue doit résider dans le même pays que l'emplacement du réseau {{site.data.keyword.blockchainfull_notm}} Platform.

### Cas d'utilisation de l'hébergement des données dans un pays

Envisagez d'utiliser un réseau {{site.data.keyword.blockchainfull_notm}} Platform qui inclut le service de tri et  et l'autorité de certification ainsi qu'un consortium de quatre organisations. Ces organisations comportent un ou plusieurs noeuds homologues. Les quatre organisations font partie d'un canal unique et tous les composants de réseau résident dans la région (par exemple, Francfort) où le réseau {{site.data.keyword.blockchainfull_notm}} Platform a été déployé. Enfin, les applications client qui interagissent avec les homologues résident également en Allemagne. L'hébergement de données est géré.  

![Hébergement de données lorsque tous les composants  résident dans le même pays](../images/remote_peer_data_res_1.png "Hébergement de données lorsque tous les composants résident dans le même pays")
  
*Figure 3. Hébergement de données lorsque tous les composants résident dans le même pays*

Nous allons maintenant étudier les conséquences lorsqu'un **homologue** rejoint l'une des organisations. Un homologue peut résider dans la même région que le reste du réseau ou en dehors de la région du réseau IBP :

-	Si l'homologue réside dans le même pays que le reste du réseau, l'hébergement de données est géré. Toutes les données du registre demeurent en Allemagne comme illustré dans la **Figure 3** ci-dessus.
-	Si l'homologue réside dans un pays différent (par exemple, les Etats-Unis), l'hébergement de données n'est plus géré car les données sur le registre homologue sont partagées à l'extérieur des frontières.

Pour résoudre ce problème, des **canaux** peuvent être utilisés pour séparer les données dans un sous-ensemble d'homologues sur le réseau. Lorsque le réseau {{site.data.keyword.blockchainfull_notm}} Platform comporte un des homologues et des services de tri entres les frontières de pays, les canaux assurent l'isolement des données de registre des organisations avec des homologues à l'extérieur des frontières.  

**Remarque :** Les noeuds de service de tri sont toujours situés dans la région du centre de données que vous avez sélectionné pour héberger le réseau. Il n'est pas possible d'avoir plusieurs services de tri entre les frontières. Toutefois, les homologues peuvent se situer dans le centre de données ou sur un site distant à l'extérieur de {{site.data.keyword.cloud_notm}}. 

![Les homologues d'hébergement de données résident à l'extérieur du pays de la région d'IBM Blockchain Platform](../images/remote_peer_data_res_2.png "Les homologues d'hébergement de données résident à l'extérieur du pays de la région d'IBM Blockchain Platform")  
*Figure 4. Les homologues d'hébergement de données résident à l'extérieur du pays de la région d'IBM Blockchain Platform*

Dans la **Figure 4**, l'hébergement de données n'est pas obligatoire pour `OrgC` et `OrgD`. En fait, `OrgD` inclut désormais deux homologues, `OrgD-peer1` et `OrgD-peer2`, qui résident aux *Etats-Unis*. Par conséquent, pour que `OrgA`, `OrgB` et leurs applications client et homologues respectifs qui résident en Allemagne puissent isoler les données de registre sur le canal `X`, un nouveau canal `Y` est créé pour `OrgC` et `OrgD`.

Pour une meilleure compréhension du flux de données sur le réseau de {{site.data.keyword.blockchainfull_notm}} Platform, voir la[documentation Fabric sur le flux de transactions![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flux de transactions").

A l'avenir, la nouvelle technologie dans Hyperledger Fabric améliorera la capacité à réaliser d'autres hébergements de données l'étranger en utilisant des [collectes de données privées ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Collectes de données privées") et zéro connaissance.

- Une collecte de données privées garantit que les données privées sont partagés homologue par homologue (via le protocole gossip) sur des homologues autorisés uniquement pour voir si, par exemple, des homologues se trouvent entre les frontières d'un pays. Les données sont stockées dans une base de données privée sur l'homologue.  Le service de tri n'est pas impliqué ici et il ne voit pas les données privées. Un hachage de ces données est écrit dans les registres de chaque homologue sur le canal. Le hachage qui est utilisé pour la validation d'état fait office de preuve de la transaction, et il peut être utilisé à des fins d'audit. Des données privées sont disponibles pour les réseaux sur  {{site.data.keyword.blockchainfull_notm}} Platform qui s'exécutent dans Fabric version 1.2.1. Toutefois, la fonction Données privées n'est pas disponible pour les homologues.

- Une preuve ZKP (Zero-Knowledge Proof) permet à un “prouveur” de garantir à un “vérificateur” qu'ils ont la connaissance d'une valeur confidentielle sans révéler la valeur confidentielle elle-même. Il s'agit d'un moyen de montrer que vous savez quelque chose qui répond à une instruction sans afficher ce que vous savez.

Vous trouverez davantage d'informations sur ces technologies dans le livre blanc sur les [transactions privées et confidentielles avec Hyperledger Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transactions privées et confidentielles avec Hyperledger Fabric").

## Support
{: #remote-peer-support}

IBM Blockchain Platform n'assure pas de support pour cette offre. Si vous rencontrez des problèmes liés à votre homologue, vous pouvez utiliser des ressources développeur de blockchain gratuites, des forums de support et obtenir de l'aide auprès d'{{site.data.keyword.IBM_notm}} et de la communauté Fabric. Pour plus d'informations, voir les [ressources et forums de support blockchain](/docs/services/blockchain/ibmblockchain_support.html#resources).Vous pouvez également afficher les ressources de support à l'écran **Obtenir de l'aide** du Moniteur réseau.

- Pour les problèmes liés à AWS, vous pouvez utiliser à la fois les [forums de support de la communauté ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://forums.aws.amazon.com/index.jspa "forums de support de la communauté AWS") et le [support premium AWS![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://aws.amazon.com/premiumsupport/ "support premium AWS").

{{site.data.keyword.blockchainfull_notm}} ne prend pas en charge les cas d'utilisation qui sont ouverts dans {{site.data.keyword.cloud_notm}} et son liés à {{site.data.keyword.blockchainfull_notm}} Platform for AWS. L'édition Community est destinée à l'exploration, au développement et aux tests et elle ne doit pas être utilisée en production. 

---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Présentation du composant Blockchain

Les composants et la structure de {{site.data.keyword.blockchainfull}} Platform (IBP) reposent sur l'infrastructure et les outils sous-jacents d'[Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/), solution de blockchain autorisée en open source largement soutenue par {{site.data.keyword.IBM_notm}}. Les réseaux reposant sur Fabric comportent plusieurs composants standard qui peuvent être déployés dans un certain nombre de configurations pour la prise en charge d'une large gamme de cas d'utilisation.

Pour obtenir une vue plus globale des réseaux Fabric, et de l'interrelation entre leurs composants, consultez [ce document sur la structure d'un réseau de blockchain](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) dans la documentation de communauté Fabric, qui indique comment démarrer et faire évoluer un réseau.

Pour un aperçu de haut niveau des composants d'un réseau basé sur Fabric, regardez la vidéo ci-dessous :

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vidéos du plan Starter" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Bien que cette vidéo présente les composants du point de vue des réseaux Starter et Enterprise, les informations demeurent très pertinentes pour la solution gérée par le client IBP sur ICP (IBM Cloud Platform).*

Pour les besoins de cette présentation, nos nous concentrerons seulement sur les autorités de certification, les services de tri, les homologues, les smart contracts et les applications. Comme vous pouvez le voir dans le document [{{site.data.keyword.blockchainfull_notm}} Platform for ICP deployment guide](/docs/services/blockchain/ibp_for_icp_deployment_guide.html), cette séquence n'est pas arbitraire ; elle reflète la séquence dans laquelle vont être déployés les composants d'un réseau basé sur Fabric.

## Autorités de certification
{: #ca}

Les identités et les autorisations constituent la base nécessaire à un réseau de blockchain basé sur Fabric. Les identités prennent la forme de certificats x.509 émis par une autorité de certification et elles sont semblables à une carte de crédit dans la mesure où elles *identifient* quelqu'un, à l'aide par exemple des attributs de ce dernier. Ces certificats sont ensuite liés à des autorisations par leur inclusion dans des dossiers MSP au niveau composant ou canal. Ainsi, un MSP homologue comportera un sous-dossier MSP appelé **admins**. Tout utilisateur dont le certificat est dans ce dossier admin, est donc admin de l'homologue, ce qui signifie qu'il a la possibilité d'effectuer toute action autorisée par l'admin de cet homologue. Un système de validation au sein de l'homologue effectue une vérification chaque fois qu'un utilisateur, identifié par son certificat signataire, essaie d'effectuer une opération d'administration. La question se pose alors de savoir si le certificat correspond à celui du dossier "admin". Si c'est le cas, l'opération peut être effectuée. Dans le cas contraire, la demande d'exécution de l'opération est rejetée.

Les autorités de certification {{site.data.keyword.blockchainfull_notm}} reposent sur [Hyperledger Fabric-CA](https://hyperledger-fabric-ca.readthedocs.io/en/latest/), bien qu'il soit possible d'utiliser une autre autorité de certification dès lors qu'elle utilise une infrastructure à clés publiques basée sur des certificats X.509. Il peut y avoir, et ce doit être généralement le cas, plusieurs niveaux d'autorités de certification. L'autorité de "certification racine" d'un réseau n'est généralement pas exposée sauf pour fournir des certificats à des "autorités de certification intermédiaires", qui émettront des certificats pour des utilisateurs et des composants directement, ou pour plusieurs couches d'autorités de certification intermédiaires. Pour plus de détails sur la façon dont les autorités de certification sont utilisées pour établir une identité et une appartenance, voir la [documentation Hyperledger Fabric sur l'identité ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) et sur l'[appartenance ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html)

## Services de tri
{: #orderer}

Alors que le service de tri est souvent considéré comme le "coeur" d'un réseau, sa fonction est en fait assez simple : classer les transactions par blocs et les renvoyer aux homologues afin qu'elles soient écrites dans leurs registres. Dans les versions précédentes de Fabric, cette fonctionnalité était liée au sein de l'homologue, mais depuis la version 1.0 de Fabric, elle est séparée dans un composant distinct pour accroître les performances et les anomalies d'homologue qui pourraient entraîner des bifurcations d'état.

A un niveau physique, cette fonction de tri requiert généralement un ensemble de services de tri, regroupées sous l'appellation "service de tri", bien que dans les environnements de test ou de validation de concept, il soit possible d'utiliser un seul noeud (appelé service de tri SOLO).

## Homologue
{: #peer}

A un niveau physique, un réseau de blockchain se compose essentiellement de noeuds homologues (ou, simplement, d'homologues). Les homologues sont un élément fondamental du réseau car ils hébergent des registres et des smart contracts (qui sont contenus dans des "codes blockchain"). Plus précisément, l'homologue héberge des **instances** du registre et des **instances** de smart contracts. Comme les smart contracts et les registres sont utilisés pour encapsuler les processus partagés et les informations partagées au sein d'un réseau, respectivement, ces aspects d'un homologue constituent un bon point de départ pour comprendre ce que fait réellement un réseau Fabric.

Pour en savoir plus sur les homologues en particulier, consultez [ce document consacré uniquement aux homologues](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html) dans la documentation Fabric Community.

## Smart contracts

Pour que les entreprises puissent effectuer des transactions entre elles, il est nécessaire de parvenir à une compréhension commune de règles et processus définis dans une sorte de contrat. Considérés dans leur ensemble, ces contrats établissent le "modèle de gestion" qui régit l'ensemble des interactions entre les partenaires commerciaux.

Les réseaux de blockchain présentent la même exigence, avec le terme "smart contracts" qui est utilisé pour ces modèles de gestion, Fabric (et IBP) contiennent ces contrats dans une plus grande structure appelée "code blockchain", laquelle inclut non seulement la logique applicative mais également l'infrastructure sous-jacente qui valide les identités des utilisateurs qui essaient d'appeler le smart contract.

Alors que dans le monde de l'entreprise, les contrats sont signés et présentés à des cabinets juridiques, les smart contracts sont installés sur des homologues et "instanciés" sur un canal.

## Applications

Les applications client d'un réseau basé sur Fabric comme IBP, optimisent l'infrastructure sous-jacente comme les API, les logiciels et les smart contracts pour permettre des interactions client (appels et requêtes) à un niveau d'abstraction plus élevé.

Pour voir comment les applications interagissent avec un réseau basé sur Fabric, consultez la documentation[relative à l'écriture de votre première application ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "Writing Your First Application") et la section relative au développement d'applications [![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "Developing Applications").

Notez que le dernier lien fait référence aux fonctionnalités Fabric, comme la classe `Contract`, qui ne sont pas compatibles avec la version 1.2 de Fabric, sur laquelle reposent le Starter Plan et la version IBP on ICP. Néanmoins, la section contient des informations conceptuelles et d'autres conseils utiles qu'il convient de garder à l'esprit lors du développement d'applications qui feront office d'interface avec des composants de la version 1.2 de Fabric.

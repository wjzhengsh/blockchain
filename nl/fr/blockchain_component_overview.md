---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

keywords: blockchain components, ca, certificate authorities, peer, ordering service, orderer, channel, smart contract, applications

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

# Présentation du composant Blockchain
{: #blockchain-component-overview}

Les composants et la structure de {{site.data.keyword.blockchainfull}} Platform reposent sur l'infrastructure et les outils sous-jacents d'[Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/), solution de blockchain autorisée en open source largement soutenue par {{site.data.keyword.IBM_notm}}. Les réseaux reposant sur Fabric comportent plusieurs composants standard qui peuvent être déployés dans un certain nombre de configurations pour la prise en charge d'une large gamme de cas d'utilisation.

Pour obtenir une vue plus globale des réseaux Fabric, et de l'interrelation entre leurs composants, consultez [ce document sur la structure d'un réseau de blockchain](https://hyperledger-fabric.readthedocs.io/en/release-1.4/network/network.html) dans la documentation de communauté Fabric, qui indique comment démarrer et faire évoluer un réseau.

Pour un aperçu de haut niveau des composants d'un réseau basé sur Fabric, regardez la vidéo ci-dessous :

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vidéos du plan Starter" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Bien que cette vidéo présente les composants du point de vue des réseaux Starter et Enterprise, les informations demeurent très pertinentes pour la solution gérée par le client {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private.*

Pour les besoins de cette présentation, nous nous concentrerons seulement sur les autorités de certification, les services de tri, les homologues, les contrats intelligents et les applications. Comme vous pouvez le voir dans les rubriques [{{site.data.keyword.blockchainfull_notm}} Platform 2.0 guide to building a network](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) et [{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private deployment guide](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp), cette séquence n'est pas arbitraire ; elle reflète la séquence dans laquelle vont être déployés les composants d'un réseau basé sur Fabric.

## Autorités de certification
{: #blockchain-component-overview-ca}

Les identités et les autorisations constituent la base nécessaire à un réseau de blockchain basé sur Fabric. Les identités prennent la forme de certificats x.509 émis par une autorité de certification et elles sont semblables à une carte de crédit dans la mesure où elles *identifient* quelqu'un, à l'aide par exemple des attributs de ce dernier. Ces certificats sont ensuite liés à des autorisations par leur inclusion dans des dossiers MSP au niveau composant ou canal. Ainsi, un MSP homologue comportera un sous-dossier MSP appelé **admins**. Tout utilisateur dont le certificat est dans ce dossier admin, est donc admin de l'homologue, ce qui signifie qu'il a la possibilité d'effectuer toute action autorisée par l'admin de cet homologue. Un système de validation au sein de l'homologue effectue une vérification chaque fois qu'un utilisateur, identifié par son certificat signataire, essaie d'effectuer une opération d'administration. La question se pose alors de savoir si le certificat correspond à celui du dossier "admin". Si c'est le cas, l'opération peut être effectuée. Dans le cas contraire, la demande d'exécution de l'opération est rejetée.

Les autorités de certification d'{{site.data.keyword.blockchainfull_notm}} Platform reposent sur [Hyperledger Fabric-CA ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/ "Hyperledger Fabric CA"), bien qu'il soit possible d'utiliser une autre autorité de certification dès lors qu'elle utilise une infrastructure à clés publiques basée sur des certificats X.509. Il peut y avoir, et ce doit être généralement le cas, plusieurs niveaux d'autorités de certification. L'autorité de "certification racine" d'un réseau n'est généralement pas exposée sauf pour fournir des certificats à des "autorités de certification intermédiaires", qui émettront des certificats pour des utilisateurs et des composants directement, ou pour plusieurs couches d'autorités de certification intermédiaires. Pour plus de détails sur la façon dont les autorités de certification sont utilisées pour établir une identité et une appartenance, voir la [documentation Hyperledger Fabric sur l'identité ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html) et sur l'[appartenance ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html)

## Services de tri
{: #blockchain-component-overview-orderer}

Alors que le service de tri est souvent considéré comme le "coeur" d'un réseau, sa fonction est en fait assez simple : classer par blocs des transactions qui ont été validées par des homologues et les renvoyer aux homologues afin qu'elles soient écrites dans leurs registres. Dans les versions précédentes de Fabric, cette fonctionnalité était liée au sein de l'homologue, mais depuis la version 1.0 de Fabric, elle est séparée dans un composant distinct pour accroître les performances et éviter les anomalies d'homologue qui pourraient entraîner des bifurcations d'état.

A un niveau physique, cette fonction de tri requiert généralement un ensemble de services de tri, regroupés sous l'appellation "service de tri", bien que dans les environnements de test ou de validation de concept, il soit possible d'utiliser un seul noeud (appelé service de tri SOLO).

## Homologues
{: #blockchain-component-overview-peer}

A un niveau physique, un réseau de blockchain se compose essentiellement de noeuds homologues (ou, simplement, d'homologues). Les homologues sont un élément fondamental du réseau car ils hébergent des registres et des contrats intelligents (qui sont contenus dans un ["code blockchain" ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "Espace de nom de code blockchain") ). Plus précisément, l'homologue héberge des **instances** du registre et des **instances** de contrats intelligents. Comme les contrats intelligents et les registres sont utilisés pour encapsuler les processus partagés et les informations partagées au sein d'un réseau, respectivement, ces aspects d'un homologue constituent un bon point de départ pour comprendre ce que fait réellement un réseau Fabric.

Pour en savoir plus sur les homologues en particulier, consultez [ce document consacré uniquement aux homologues![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/peers/peers.html) dans la documentation de la communauté Fabric.

## Canaux
{: #blockchain-component-overview-channels}

Un canal est un mécanisme qui offre une couche de communication ouverte entre les membres du réseau. Plusieurs canaux peuvent être créés entre des sous-ensembles de membres, ce qui permet la prise en charge de l'un des [nombreux mécanismes de mise en oeuvre de la confidentialité![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Transactions privées et confidentielles avec Hyperledger Fabric"). Les données sur le réseau de blockchain sont stockées dans les registres de canal. Ces registres sont hébergés sur les homologues ds organisations qui ont rejoint le canal. Pour plus d'informations sur les canaux et leur utilisation, consultez la [documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channels.html)

## Smart contracts
{: #blockchain-component-overview-smart-contracts}

Pour que les entreprises puissent effectuer des transactions entre elles, il est nécessaire de parvenir à une compréhension commune de règles et processus définis dans une sorte de contrat. Considérés dans leur ensemble, ces contrats établissent le "modèle de gestion" qui régit l'ensemble des interactions entre les partenaires commerciaux.

Les réseaux de blockchain présentent la même exigence, avec le terme "contrats intelligents" qui est utilisé pour ces modèles de gestion, Fabric et {{site.data.keyword.blockchainfull_notm}} Platform contiennent ces contrats dans une plus grande structure appelée "code blockchain", laquelle inclut non seulement la logique applicative mais également l'infrastructure sous-jacente qui valide les identités des utilisateurs qui essaient d'appeler le contrat intelligent.

Alors que dans le monde de l'entreprise, les contrats sont signés et présentés à des cabinets juridiques, les contrats intelligents sont installés sur des homologues et "instanciés" sur un canal.

## Applications
{: #blockchain-component-overview-applications}

Les applications client d'un réseau basé sur Fabric comme {{site.data.keyword.blockchainfull_notm}} Platform, optimisent l'infrastructure sous-jacente comme les API, les logiciels et les contrats intelligents pour permettre des interactions client (appels et requêtes) à un niveau d'abstraction plus élevé.

Pour voir comment les applications interagissent avec un réseau basé sur Fabric, consultez la documentation [relative à l'écriture de votre première application ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/write_first_app.html "Writing Your First Application").  De même, vous devez savoir que ce domaine est en passe de faire l'objet d'importante améliorations dans Fabric version 1.4, disponibles dans {{site.data.keyword.blockchainfull_notm}} Platform 2.0. Pour plus d'informations, consultez la rubrique Hyperledger Fabric relative au [Développement d'applications![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Développement d'applications").  Même si ces fonctions directes, comme la classe Contract, ne sont pas compatibles avec la version 1.2 de Fabric, version actuelle du plan Starter, elles contiennent des informations conceptuelles et d'autres conseils utiles qu'il convient d'intégrer dans la conception de votre application et vous préparent pour le futur.

Pour voir comment une application interagit avec un réseau basé sur Fabric, consultez la documentation relative à la [création d'applications](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Exemple de réseau
{: #blockchain-component-overview-example-network}

La **Figure 1** illustre un exemple de réseau de blockchain déployé, qui se compose de quatre membres (chacun propriétaire de deux homologues), des autorités de certification responsables de la distribution du matériel cryptographique de vérification d'identité et d'un service de tri définissant les règles et les participants du réseau. Le canal bleu comporte l'ensemble des quatre membres du réseau, tandis que le canal jaune est limité à trois membres seulement : Les banques B, C et D. On peut voir également que la banque A a joué le rôle d'initiateur réseau et que la banque D existe seulement en tant que membre dans le contexte du canal jaune. Enfin, un utilisateur final ou une application en possession d'un certificat x509 correctement signé est en mesure d'effectuer des appels vers les homologues du réseau.

![Réseau de blockchain](images/blockchain_network_2-01.png "Exemple de réseau de blockchain")

*Figure 1. Exemple de réseau de blockchain composé de quatre membres utilisant des canaux pour isoler des données*

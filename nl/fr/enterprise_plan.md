---

copyright:
  years: 2017
lastupdated: "2017-09-20"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Plan d'entreprise de la plateforme {{site.data.keyword.blockchainfull_notm}}

Le plan d'entreprise de la plateforme {{site.data.keyword.blockchainfull}} est la première option de membre proposée pour les organisations qui souhaitent trouver ou rejoindre un réseau d'entreprise de type Blockchain. Ce plan fournit l'infrastructure de base ainsi que les outils et le support nécessaires à un déploiement en toute simplicité d'un réseau de type Blockchain, prêt pour la production et hautement sécurisé.

IBM met à disposition des membres qui vont démarrer le réseau une interface graphique qui guide l'initiateur du réseau au cours des étapes de configuration et de mise à disposition du réseau. Cette interface comporte des fonctions permettant d'inviter d'autres membres et de définir des règles de gouvernance. Une fois le réseau déployé, une interface graphique utilisateur interactive est disponible pour surveiller la santé et l'activité du réseau, gérer les principales activités en réseau, comme les nouveaux déploiements, l'ajout/le retrait de membres, le cycle de vie du code blockchain, ainsi que la gestion des canaux et la recherche d'un support technique. Pour plus de détails sur la procédure d'obtention d'un support, cliquez [ici](ibmblockchain_support.html).

La plateforme {{site.data.keyword.blockchainfull_notm}} est créée à l'aide de composants Hyperledger Fabric importants, dont  une Autorité de certification et au moins 1 homologue (6 max).  IBM fournit également un service de commande Kafka (CFT) tolérant aux pannes pour les membres réseau. 

Fabric CA est l'autorité de certification fournie avec le plan d'entreprise. Deux autorités de certification intermédiaire sont fournies par membre, qui permettent d'accorder l'appartenance au réseau. Grâce à l'autorité de certification, le membre peut également accorder l'appartenance (certificats) à des utilisateurs finaux du réseau.

Il est important de comprendre que l'ajout d'une transaction au registre s'effectue en plusieurs étapes :  
1. Simulation de transaction et adhésion (homologue)
2. Tri (service de tri)
3. Validation et confirmation (homologue)

Les homologues Fabric dont les membres sont propriétaires constituent l'interface ou la passerelle pour les applications qui exécutent du code blockchain ; ils fournissent la logique métier nécessaire à l'exécution des transactions dans le registre.  Toutes les transactions doivent être validées. Les autres membres du réseau effectuent cette adhésion. Ensuite, les transactions sont envoyées à un service de tri (Kafka) fourni par IBM.

Outre les principaux composants blockchain, l'option Plan d'appartenance d'entreprise
fournit une infrastructure avec un stockage de données et des communications sécurisées (TLS) ainsi qu'une haute disponibilité.  Alors que les réseaux Fabric partagent ces ressources d'infrastructure, l'isolement est fourni pour les noeuds de composant Fabric au sein d'un réseau, et chaque noeud s'exécute dans un  conteneur docker sécurisé qui protège l'environnement d'exécution.

Le seul élément qui doit être défini est la taille des homologues nécessaires au réseau. Ce choix dépend du nombre de canaux requis, ainsi que de la charge de travail par canal, de l'utilisation de la mémoire et de l'espace disque (stockage). Vous trouverez ci-dessous les tailles d'homologue disponibles via le plan de service de niveau entreprise et une aide pour vous aider à choisir l'homologue approprié.

La plateforme IBM Blockchain doit être utilisée pour des déploiements de production, ou ayant quasiment atteint le niveau production, plus stables. A des fins de test, utilisez le service IBM Container ou des images téléchargeables locales.

Le plan Enterprise fournit le service de commande et une autorité de certification. Les frais d'appartenance sont de 1000 dollars auxquels s'ajoutent des frais de 1000 dollars par homologue associés au réseau. Si vous souhaitez disposer de la haute disponibilité (HA), vous devez acquérir un homologue supplémentaire fournissant les fonctions HA. Par exemple, une organisation (avec des frais d'appartenance de 1000 dollars) ayant deux homologues (1000 dollars X 2 homologues) et disposant de la HA (1000 dollars X 2 homologues HA) devra s'acquitter chaque mois de 5000 dollars.

Inscrivez-vous dès maintenant en tant que membre [{{site.data.keyword.blockchainfull_notm}} ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

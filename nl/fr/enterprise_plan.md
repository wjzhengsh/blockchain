---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# A propos du plan Enterprise


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Le plan Enterprise d'{{site.data.keyword.blockchainfull}} Platform est une offre prête pour la production pour les organisations qui souhaitent trouver ou rejoindre un réseau d'entreprise de type Blockchain pour de vraies activités. Ce plan fournit l'infrastructure de base ainsi que les outils et le support nécessaires à un démarrage d'un réseau hautement sécurisé et prêt pour la production. Le plan Enterprise a été mis à niveau de Hyperledger Fabric V1.0 vers la version V1.1 le 15 mai 2018. Tous les réseaux créés après le 15 mai 208 sont au niveau Fabric V1.1. Toutefois, les réseaux qui ont été créés précédemment resteront au niveau Fabric V1.0.
{:shortdesc}

**Remarques :**
- Le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform fournit un environnement de production. Si vous avez besoin d'un environnement de tests et de développement, voir [A propos du plan Starter](starter_plan.html).
- {{site.data.keyword.blockchainfull_notm}} Platform est un service de plateforme sur {{site.data.keyword.cloud_notm}} et toutes les offres d'appartenance suivent les [Dispositions des services {{site.data.keyword.cloud_notm}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Dispositions des services {{site.data.keyword.cloud_notm}}") relatives aux accords sur les niveaux de licence (SLA). Les réseaux du plan Enterprise sont mis à disposition dans **plusieurs environnements** dans des centres de données géographiquement distincts.

IBM met à disposition des membres qui vont démarrer le réseau une interface graphique qui guide l'initiateur du réseau au cours des étapes de configuration et de mise à disposition du réseau. Cette interface comporte des fonctions permettant d'inviter d'autres membres et de définir des règles de gouvernance. Pour plus d'informations, voir [Gouvernance du réseau de plan Enterprise](get_start.html). Une fois le réseau déployé, une interface graphique interactive, le Moniteur réseau, est disponible pour surveiller la santé et l'activité du réseau, gérer les principales activités en réseau, comme les nouveaux déploiements, l'ajout ou le retrait de membres, le cycle de vie du code blockchain, ainsi que la gestion des canaux et la recherche d'un support technique. Pour plus d'informations, voir [Utilisation du Moniteur réseau](v10_dashboard.html).

Inscrivez-vous dès maintenant en tant que [{{site.data.keyword.blockchainfull_notm}} membre ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

{{site.data.keyword.blockchainfull_notm}} Platform est créé à l'aide de composants Hyperledger Fabric importants, dont une Autorité de certification et au moins 1 homologue (6 max).  Le plan Enterprise fournit également un service de tri Kafka (CFT) tolérant aux pannes pour les membres réseau.

Fabric CA est l'autorité de certification fournie avec le plan d'entreprise. Deux autorités de certification intermédiaire sont fournies par membre, qui permettent d'accorder l'appartenance au réseau. Grâce à l'autorité de certification, le membre peut également accorder l'appartenance (certificats) à des utilisateurs du réseau.

Il est important de comprendre que l'ajout d'une transaction au registre s'effectue en plusieurs étapes :  
1. Simulation de transaction et validation (homologue)
2. Tri (service de tri)
3. Validation et confirmation (homologue)

Les homologues Fabric dont les membres sont propriétaires constituent l'interface ou la passerelle pour les applications qui exécutent du code blockchain ; ils fournissent la logique métier nécessaire à l'exécution des transactions dans le registre.  Toutes les transactions doivent être validées. Les autres membres du réseau effectuent cette validation. Ensuite, les transactions sont envoyées à un service de tri (Kafka) fourni par IBM.

Outre les principaux composants blockchain, l'option Plan d'appartenance d'entreprise
fournit une infrastructure avec un stockage de données et des communications sécurisées (TLS) ainsi qu'une haute disponibilité.  Alors que les réseaux Fabric partagent ces ressources d'infrastructure, l'isolement est fourni pour les noeuds de composant Fabric au sein d'un réseau, et chaque noeud s'exécute dans un conteneur Docker sécurisé qui protège l'environnement d'exécution.

Le seul élément qui doit être défini est la taille des homologues nécessaires au réseau. Ce choix dépend du nombre de canaux requis, ainsi que de la charge de travail par canal, de l'utilisation de la mémoire et de l'espace disque (stockage).

Vous devez utiliser le plan Enterprise pour des déploiements de production, ou ayant quasiment atteint le niveau production, plus stables. A des fins de test, utilisez le [plan Starter](starter_plan.html), [développez dans IBM Container Service](https://ibm-blockchain.github.io/) ou [installez des images Docker en local](http://hyperledger-fabric.readthedocs.io/en/release-1.1/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Tarification  
Pour utiliser le plan Enterprise, les membres réseau doivent régler 1 000 dollars par mois de frais d'appartenance et 1 000 euros mensuels supplémentaires pour chacun des homologues du réseau.  Les frais mensuels sont facturés par jour au prorata.  Par exemple, une organisation (avec des frais d'appartenance de 1 000 dollars) ayant deux homologues (frais par homologue de 1 000 dollars X 2 homologues) doit payer 3 000 dollars par mois.  Si le mois comporte 30 jours, le membre paie 100 dollars (3 000/30) par jour.  Notez que si vous souhaitez disposer de la haute disponibilité (HA), vous devez doubler le nombre d'homologues nécessaires pour fournir les fonctions HA.

Les membres réseau peuvent régler leur facture avec leurs comptes {{site.data.keyword.cloud_notm}} qui contiennent l'espace nécessaire à la création de l'instance de réseau. Un membre réseau peut aussi couvrir les factures de tous les membres du réseau. Pour plus d'informations sur la facturation des réseaux de blockchain, voir [Facturation du réseau](howto/paying_mode.html).

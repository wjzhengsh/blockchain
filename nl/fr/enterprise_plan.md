---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# A propos du plan Enterprise
{: #enterprise-plan-about}

Le plan Enterprise d'{{site.data.keyword.blockchainfull}} Platform est une offre prête pour la production pour les organisations qui souhaitent trouver ou rejoindre un réseau d'entreprise de type Blockchain pour de vraies activités. Ce plan fournit l'infrastructure de base ainsi que les outils et le support nécessaires à un démarrage d'un réseau hautement sécurisé et prêt pour la production. Le plan Enterprise a été mis à niveau de Hyperledger Fabric V1.0 vers la version V1.1 le 15 mai 2018. Tous les réseaux créés après le 15 mai 208 sont au niveau Fabric V1.1. Toutefois, les réseaux qui ont été créés précédemment resteront au niveau Fabric V1.0.
{:shortdesc}

Le **plan Enterprise** constitue un environnement de production offrant de hauts niveaux de sécurité et de support. Le déploiement de votre réseau dans le plan Enterprise vous permet de tirer parti des fonctionnalités suivantes :

* Environnement hautement sécurisé doté de nombreuses fonctions de sécurité matérielles, logicielles et de microprogramme.
* Architecture renforcée pour l'évolutivité, la résilience et la disponibilité.
* Réseau optimisé pour les performances et s'exécutant dans un environnement de calcul Linux le plus rapide au monde.

L'exploitation de votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform inclut des outils et des fonctions qui simplifient les tâches d'administration :

* Tableaux de bord pour la surveillance et la gestion des ressources sur le réseau.
* Mises à niveau transparentes de la pile de code complète.
* Support technique 24h sur 24 et 7 jours sur 7 intégré au portail.
* Pile de sécurité renforcée sans aucun accès privilégié, résistance aux logiciels malveillants et aux tentatives de sabotage, chiffrement à 100 % et bien d'autres fonctions pour les réseaux comportant des données sensibles dans les secteurs réglementés.
* Réseaux d'entreprise sauvegardés hors site une fois toutes les 24 heures. En cas de sinistre, ces réseaux peuvent être restaurés sur le même site ou sur un site secondaire.

Le plan Enterprise est un service de plateforme sur {{site.data.keyword.cloud_notm}} et il suit les [Dispositions des services {{site.data.keyword.cloud_notm}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Dispositions des services {{site.data.keyword.cloud_notm}}") relatives aux accords sur les niveaux de licence (SLA). Notez que les réseaux du plan Enterprise sont mis à disposition dans **plusieurs environnements** dans des centres de données géographiquement distincts.

**Remarques :**
- Le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform fournit un environnement de production. Si vous avez besoin d'un environnement de tests et de développement, voir [A propos du plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform est un service de plateforme sur {{site.data.keyword.cloud_notm}} et toutes les offres d'appartenance suivent les [Dispositions des services {{site.data.keyword.cloud_notm}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Dispositions des services {{site.data.keyword.cloud_notm}}") relatives aux accords sur les niveaux de licence (SLA). Les réseaux du plan Enterprise sont mis à disposition dans **plusieurs environnements** dans des centres de données géographiquement distincts.

IBM met à disposition des membres qui vont démarrer le réseau une interface graphique qui guide l'initiateur du réseau au cours des étapes de configuration et de mise à disposition du réseau. Cette interface comporte des fonctions permettant d'inviter d'autres membres et de définir des règles de gouvernance. Pour plus d'informations, voir [Gouvernance du réseau de plan Enterprise](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan). Une fois le réseau déployé, une interface graphique interactive, le Moniteur réseau, est disponible pour surveiller la santé et l'activité du réseau, gérer les principales activités en réseau, comme les nouveaux déploiements, l'ajout ou le retrait de membres, le cycle de vie du code blockchain, ainsi que la gestion des canaux et la recherche d'un support technique. Pour plus d'informations, voir [Utilisation du Moniteur réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

Inscrivez-vous dès maintenant en tant que [{{site.data.keyword.blockchainfull_notm}} membre ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod).

{{site.data.keyword.blockchainfull_notm}} Platform est créé à l'aide de composants Hyperledger Fabric importants, dont une Autorité de certification et au moins 1 homologue (6 max).  Le plan Enterprise fournit également un service de tri Kafka (CFT) tolérant aux pannes pour les membres réseau.

Fabric CA est l'autorité de certification fournie avec le plan Enterprise. Deux autorités de certification intermédiaire sont fournies par membre, qui permettent d'accorder l'appartenance au réseau. Grâce à l'autorité de certification, le membre peut également accorder les certificats d'appartenance à des utilisateurs du réseau.

Il est important de comprendre que l'ajout d'une transaction au registre s'effectue en plusieurs étapes :
1. Simulation de transaction et validation (homologue)
2. Tri (service de tri)
3. Validation et confirmation (homologue)

Les homologues Fabric dont les membres sont propriétaires constituent l'interface ou la passerelle pour les applications qui exécutent du code blockchain ; ils fournissent la logique métier nécessaire à l'exécution des transactions dans le registre. Toutes les transactions doivent être validées. Les autres membres du réseau effectuent cette validation. Ensuite, les transactions sont envoyées à un service de tri fourni par IBM.

Outre les principaux composants blockchain, l'option Plan d'appartenance d'entreprise
fournit une infrastructure avec un stockage de données et des communications sécurisées (TLS) ainsi qu'une haute disponibilité.  Alors que les réseaux Fabric partagent ces ressources d'infrastructure, l'isolement est fourni pour les noeuds de composant Fabric au sein d'un réseau, et chaque noeud s'exécute dans un conteneur Docker sécurisé qui protège l'environnement d'exécution.

Le seul élément qui doit être défini est la taille des homologues nécessaires au réseau. Ce choix dépend du nombre de canaux requis, ainsi que de la charge de travail par canal, de l'utilisation de la mémoire et de l'espace disque (stockage).

Vous devez utiliser le plan Enterprise pour des déploiements de production, ou ayant quasiment atteint le niveau production, plus stables. A des fins de test, utilisez le [plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about) ou [installez des images Docker en local](http://hyperledger-fabric.readthedocs.io/en/release-1.1/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Tarification
{: #enterprise-plan-about-pricing}

Pour utiliser le plan Enterprise, les membres réseau doivent régler 1 000 dollars par mois de frais d'appartenance et 1 000 euros mensuels supplémentaires pour chacun des homologues du réseau.  Les frais mensuels sont facturés par jour au prorata.  Par exemple, une organisation (avec des frais d'appartenance de 1 000 dollars) ayant deux homologues (frais par homologue de 1 000 dollars X 2 homologues) doit payer 3 000 dollars par mois.  Si le mois comporte 30 jours, le membre paie 100 dollars (3 000/30) par jour.  Notez que si vous souhaitez disposer de la haute disponibilité (HA), vous devez doubler le nombre d'homologues nécessaires pour fournir les fonctions HA.

Les membres réseau peuvent régler leur facture avec leurs comptes {{site.data.keyword.cloud_notm}} qui contiennent l'espace nécessaire à la création de l'instance de réseau. Un membre réseau peut aussi couvrir les factures de tous les membres du réseau. Pour plus d'informations sur la facturation des réseaux de blockchain, voir [Facturation du réseau](/docs/services/blockchain/howto/paying_mode.html#paying-mode).

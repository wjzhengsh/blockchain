---

copyright:
  years: 2017, 2018
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Tarification
{: #ibp-pricing}


Ce guide vous aidera à comprendre la tarification des plans d'appartenance à {{site.data.keyword.blockchainfull}} Platform et à estimer vos coûts de développement et d'expansion de votre réseau de blockchain.  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform facture des frais d'appartenance et d'homologue mensuels aux organisations qui construisent des réseaux de blockchain. Les redevances varient selon le plan d'appartenance sélectionné et les ressources réseau utilisées par votre réseau. Le tableau suivant offre une vue d'ensemble de la tarification d'{{site.data.keyword.blockchainfull_notm}} Platform.

| Eléments de la tarification | Redevances mensuelles pour le plan Starter | Redevances mensuelles pour le plan Enterprise |
|-----|-----|-----|
| Frais d'appartenance | 250$ | 1000$ |
| Frais d'homologue | 125$ | 1000$ |

*Figure 1. Vue d'ensemble de la tarification d'{{site.data.keyword.blockchainfull_notm}} Platform*

Les frais mensuels sont facturés par jour au prorata. Par exemple, une organisation (avec des frais d'appartenance de 1 000 dollars) ayant deux homologues (frais par homologue de 1 000 dollars X 2 homologues) doit payer 3 000 dollars par mois. Si le mois comporte 30 jours, le membre paie 100 dollars (3 000/30) par jour. Pour plus d'informations sur le mode de règlement pour vos réseaux, voir [Mode de paiement](/docs/services/blockchain/howto/paying_mode.html#paying-mode).


## Composants de base du réseau
{: #ibp-pricing-components}

Pour comprendre la tarification, nous devons commencer par une présentation des composants de base d'un réseau. {{site.data.keyword.blockchainfull_notm}} Platform permet la création d'un réseau de blockchain basé sur Hyperledger Fabric. A un haut niveau, un réseau de blockchain Fabric est constitué des composants de base suivants :

-	**Organisations** – Entité quelconque ayant besoin de conserver une copie du registre blockchain et de valider des transactions. Une même entreprise peut avoir plusieurs organisations blockchain.
-	**Homologues** – Noeud associé à une organisation et qui contient le registre de blockchain et valide des transactions. Les homologues sont associés à une organisation blockchain spécifique.
-	**Service de tri** – Composé d'un seul service de tri (SOLO) ou de plusieurs. Le service de tri séquence les transactions, crée des blocs, et envoie les blocs aux homologues pour validation.
-	**Autorité de certification** – Emet des certificats numériques à des fins d'identification à n'importe quel composant interactif.

{{site.data.keyword.blockchainfull_notm}} Platform offre deux plans d'appartenance, le plan **Starter** et le plan **Enterprise**, que vous pouvez sélectionner dans {{site.data.keyword.cloud_notm}}. Les deux plans vous permettent de créer des organisations et vous allouent une autorité de certification. Les plans divergent quant aux homologues, autorités de certification et service de tri.

Le plan Enterprise vous offre des autorités de certification et homologues à haute disponibilité avec un service de tri à tolérance de pannes. Le plan Starter ne propose pas d'options de haute disponibilité et utilise un service de tri SOLO de base. Pour cette raison, le plan Starter est destiné à constituer le point d'entrée dans {{site.data.keyword.blockchainfull_notm}} Platform pour des environnements de développement, de test et de validation de concept. Le plan Enterprise constitue l'option appropriée pour les réseaux prêts pour les environnements pilotes et de production.

## Eléments clés de la tarification
{: #ibp-pricing-key-elements}

Le plan Starter tout comme le plan Enterprise comporte deux éléments de tarification :

- **Frais d'appartenance** – Couvrent la création d'organisation, l'accès au service de tri et à l'autorité de certification, et sont facturés **par instance**. Inclus dans cet élément de tarification, {{site.data.keyword.blockchainfull_notm}} Platform gère le service de tri et l'autorité de certification pour le compte de votre réseau. Cette redevance est obligatoire pour avoir accès à un réseau bâti sur {{site.data.keyword.blockchainfull_notm}} Platform.

  -	Le plan Starter permet un nombre *illimité* d'organisations par souscription et la possibilité de basculer entre les organisations dans le Moniteur réseau. Comme le plan Starter est conçu pour les environnements de développement, de test et de validation de concept, vous pouvez simuler des environnements multi-organisations. **Notez** que le stockage réseau total est plafonné à 20 Go, composants, code blockchain et données de registre compris. Vos organisations simulées se partagent le stockage de 20 Go dans le réseau de blockchain.

  -	Le plan Enterprise permet une seule organisation par souscription. Comme le plan Enterprise est conçu pour les environnements pilotes et de production, vous êtes lié à votre organisation spécifique.

-	**Frais d'homologues** – Couvrent les homologues d'une organisation et sont facturés sur la base du nombre **d'homologues**. Cette redevance est due uniquement si vous désirez avoir un homologue pour conserver une copie du registre et valider des transactions.

### Exemple de frais d'appartenance
{: #ibp-pricing-example}

La figure 2 illustre un exemple d'instances réseau, ce qui peut vous aider à comprendre les frais d'appartenance. Dans cette figure, le compte {{site.data.keyword.cloud_notm}} concerné met à disposition trois instances réseau : une instance du plan Enterprise nommée *Blockchain-11* et deux instances du plan Starter nommées *Blockchain-cz* et *Blockchain-da*. Chaque instance nécessite son propre service de tri et autorité de certification. En l'occurrence, ce compte {{site.data.keyword.cloud_notm}} spécifique doit s'acquitter de trois frais d'appartenance, un pour chaque instance réseau.

![Instances réseau Blockchain](../images/ibp_instance_example.png "Instances réseau Blockchain")  
*Figure 2. Instances réseau Blockchain*


## Tarification du plan Starter
{: #ibp-pricing-starter-pricing}

Les réseaux sous le plan Starter sont mis à disposition avec une configuration par défaut de deux organisations et un homologue par organisation, pour un coût total de 500 dollars par mois. Avec la configuration par défaut, vous n'avez pas un besoin immédiat de manipuler l'environnement pour votre mise en route. Vous pouvez ajouter d'autres organisations sans encourir de frais et ajouter d'autres homologues à vos organisations pour un coût de 125 dollars par mois et par homologue. Votre facture est représentée dans la figure 3 si vous créez ou rejoignez un réseau du plan Starter avec la configuration par défaut.

| Composants de la tarification | Coût mensuel |
|-----|----------------|
| Frais d'appartenance | 250$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | 125$ |
| Facture de fin de mois | 500$ |

*Figure 3. Redevance pour réseau d'un plan Starter par défaut*

### Exemple de tarification de plan Starter
{: #ibp-pricing-starter-plan-pricing-example}

#### Homologues supplémentaires
{: #ibp-pricing-additional-peers}

Le plan Starter ne limite pas le nombre d'homologues que vous pouvez ajouter à votre réseau. Si vous ajoutez, par exemple, deux homologues au réseau du plan Starter par défaut, un pour chacune de vos organisations, vous alourdissez votre facture de 250 dollars par mois. Votre facture est reflétée dans la figure 4 si vous ajoutez deux homologues supplémentaires au début du premier mois où vous créez ou rejoignez un réseau du plan Starter.

| Composants de la tarification | Coût mensuel |
|-----|----------------|
| Frais d'appartenance au réseau 1 | 250$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | 125$ |
| Total | 750 $ US |
| Facture à la fin du premier mois | 750 $ US |

*Figure 4. Redevance pour réseau d'un plan Starter par défaut avec deux homologues supplémentaires*

#### Réseaux supplémentaires
{: #ibp-pricing-additional-networks}
Le plan Starter ne limite pas le nombre d'instances réseau que vous pouvez mettre à disposition. Si vous mettez à disposition une seconde instance réseau du plan Starter, vous devrez vous acquitter une nouvelle fois des frais d'appartenance et des frais d'homologue associés au second réseau. Vous augmentez donc votre facture de 500 dollars par mois pour utiliser la configuration réseau par défaut. Vous pouvez vous retrouver dans la situation où vous avez besoin de réseaux Starter supplémentaires, isolés les uns des autres, pour vos environnements de développement, de test et de validation de concept. Par exemple, vous pourriez vouloir fournir de tels réseaux à vos ingénieurs qualité pour réaliser des tests fonctionnels significatifs à l'écart de votre environnement de développement.

Votre facture est reflétée dans la figure 5 si vous ajoutez un réseau supplémentaire au début du premier mois où vous créez ou rejoignez un réseau du plan Starter.

| Composants de la tarification | Coût mensuel |
|-----|----------------|
| Frais d'appartenance au réseau 1 | 250$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | 125$ |
| Frais d'appartenance au réseau 2 | 250$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | 125$ |
| Facture à la fin du premier mois | 1000$  |

*Figure 5. Redevance pour deux réseaux du plan Starter par défaut*

#### Suppression d'homologues
{: #ibp-pricing-removing-peers}

Vous pouvez aussi retirer un homologue de la configuration réseau du plan Starter par défaut. Dans ce cas, votre facture sera réduite de 125 dollars par mois. Ce scénario peut vous concerner lorsque vous collaborez avec un autre membre dans votre environnement du plan Starter, où chacun de vous n'a besoin que d'un seul homologue. Votre facture est reflétée dans la figure 6 si vous retirez un homologue de votre réseau de plan Starter.

| Composants de la tarification | Coût mensuel |
| ----- | ---------------- |
| Frais d'appartenance au réseau 1 | 250$ |
| Frais d'homologue | 125$ |
| Frais d'homologue | Non applicable |
| Facture à la fin du premier mois |  375$ |

*Figure 6. Redevance pour réseau de plan Starter après retrait d'un homologue*


## Tarification du plan Enterprise
{: #ibp-pricing-enterprise-plan}

{{site.data.keyword.blockchainfull_notm}} Platform ne fournit pas de configuration par défaut pour un réseau du plan Enterprise. Vous pouvez choisir la configuration avec laquelle vous désirez débuter. Lorsque vous êtes prêt à utiliser le plan Enterprise, vous deviez avoir une bonne compréhension de la configuration réseau idoine. Comme pratique optimale pour haute disponibilité; nous vous recommandons fortement de disposer d'au moins deux homologues par organisation pour garantir que votre organisation ne rencontre pas de panne réseau.

Si vous opérez dans un réseau de plan Enterprise avec l'autre membre réseau et que chacun de vous ajoute deux homologues à votre organisation, chacune de vos factures est reflétée dans la figure 7.

| Composants de la tarification | Coût mensuel |
|-----|----------------|
| Frais d'appartenance | 1000$ |
| Frais d'homologue | 1000$ |
| Frais d'homologue | 1000$ |
| Facture de fin de mois | 3000$ |

*Figure 7. Redevance pour réseau de plan Enterprise de base*

### Exemple de tarification de plan Enterprise
{: #ibp-pricing-enterprise-plan-pricing}


#### Homologues supplémentaires
{: #ibp-pricing-enterprise-additional-peers}

Poursuivons avec l'exemple ci-dessus, en supposant que vous ajoutez deux homologues à votre organisation. Votre facture augmente de 2000 dollars par mois. Votre facture est reflétée dans la figure 8 :

| Composants de la tarification | Coût mensuel |
|-----|----------------|
| Frais d'appartenance | 1000$ |
| Frais d'homologue | 1000$ |
| Frais d'homologue | 1000$ |
| Frais d'homologue | 1000$ |
| Frais d'homologue | 1000$ |
| Facture à la fin du premier mois | 5000$ |

*Figure 8. Redevance pour un réseau de plan Enterprise avec quatre homologues*

Si l'autre membre conserve la même configuration réseau, sa facture, reflétée dans la figure 7, se présente comme avant.

#### Réseaux supplémentaires
{: #ibp-pricing-enterprise-additional-networks}

Le plan Enterprise ne limite pas le nombre d'instances réseau que vous pouvez mettre à disposition ou rejoindre. Si vous créez un second réseau du plan Enterprise avec la même configuration réseau de base (à savoir, une organisation unique avec deux homologues), votre facture est reflétée dans la figure 11. Ce scénario peut vous concerner si vous avez créé plusieurs réseaux, rejoint plusieurs réseaux, ou une combinaison des deux possibilités.

| Composants de la tarification | Coût mensuel |
|-----|----------------|
| Frais d'appartenance au réseau 1 | 1000$ |
| Frais d'homologue | 1000$ |
| Frais d'homologue | 1000$|
| Frais d'appartenance au réseau 2 | 1000$ |
| Frais d'homologue | 1000$ |
| Frais d'homologue | 1000$|
| Total | 6000$ |

*Figure 9. Redevance pour deux réseaux de plan Enterprise, chacun avec la configuration réseau de base*

Si l'autre membre utilise uniquement un réseau de plan Enterprise et conserve la même configuration réseau, sa facture, reflétée dans la figure 7, se présente comme avant.

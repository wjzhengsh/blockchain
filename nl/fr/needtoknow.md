---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Clause de protection
{: #disclainer}

**ATTENTION :** Vous devez prendre connaissance des informations suivantes avant d'utiliser des plans {{site.data.keyword.blockchainfull}}.

## Déclaration du support IBM

IBM est depuis longtemps chef de file en matière d'innovation, et cela se poursuit avec les offres du plan {{site.data.keyword.blockchainfull_notm}} sur {{site.data.keyword.Bluemix_notm}}. {{site.data.keyword.blockchain}} est une technologie qui connaît un essor rapide et qui devrait engendrer des perturbations dans le secteur financier, les chaînes d'approvisionnement locales et globales, ainsi que le soutien logistique dans un certain nombre d'espaces professionnels. Dans le cadre de programmes d'adoption précoces, les clients et partenaires commerciaux IBM étudient la blockchain en tant que solution industrielle. {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} est l'un de ces programmes. **Comme pour toute nouvelle technologie, les utilisateurs d'{{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} doivent se préparer à des changements rapides et essentiels**.  
{:shortdesc}

L'architecture d'{{site.data.keyword.blockchainfull_notm}} repose sur le projet Hyperledger Fabric de Linux Foundation. Chaque contribution de communauté en open source améliore Hyperledger Fabric mais peut représenter un défi en termes d'adoption. **IBM met en garde contre toute définition ou échange d'actifs financiers, ou tout actif de valeur, directement sur un réseau de blockchain Hyperledger Fabric**.  

## Déclaration Open source

Les plans de l'offre {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} reposent sur la pile Hyperledger Fabric de Linux Foundation. Les membres du projet Hyperledger, y compris IBM, continuent à contribuer aux différents sous-projets sous l'égide de Hyperledger.  Toutes les contributions sont passées en revue, certifiées et testées par la communauté.

## Déclaration du support du code blockchain

Les pratiques de codage suivantes NE sont PAS prises en charge sur les réseaux {{site.data.keyword.blockchainfull_notm}} :

1. Utilisation de tableaux associatifs avec itération (l'ordre est aléatoire en Go).
2. Lecture d'une liste d'éléments d'une table KVS (l'ordre n'est pas garanti).
3. Ecriture de code blockchain dangereux (query et invoke peuvent être appelés en parallèle).
4. Substitution de mémoire globale ou de mémoire cache pour les variables d'état de registre dans le code blockchain.
5. Accès à des services externes, comme les bases de données, directement depuis le code blockchain.
6. Utilisation de bibliothèques ou de variables globales qui
pourraient introduire du non déterminisme (utilisation de "random" ou
"time", par exemple).  

En outre, il n'est pas recommandé d'écrire du code blockchain non déterministe, car cela présente un risque pour la cohérence et l'intégrité des données.  Il est à noter que l'architecture Hyperledger Fabric est conçue pour contrer le code blockchain non déterministe via une série de vérifications d'adhésion et de validation. Cependant, vous êtes quand même fortement encouragé à coder des fonctions déterministes qui ne reposent pas sur des variables globales non statiques (par exemple, le temps).  

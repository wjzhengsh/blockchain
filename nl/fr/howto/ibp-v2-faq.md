---

copyright:
  years: 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# Foire aux questions
{: #ibp-v2-faq}

- [Quelle version d'Hyperledger Fabric est utilisée avec la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform ?](#ibp-v2-faq-fabric-version)
- [Dois-je payer pour la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform ?](#ibp-v2-faq-cost)
- [Puis-je utiliser mon cluster {{site.data.keyword.cloud_notm}} Kubernetes Service existant ?](#ibp-v2-faq-existing-cluster)
- [Quelle base de données les homologues utilisent-ils pour leur registre ?](#ibp-v2-faq-couchDB)
- [Quels langages sont pris en charge pour les contrats intelligents ?](#ibp-v2-faq-cc-langs)
- [Puis-je migrer depuis la version 2.0 bêta vers la version 2.0 en disponibilité générale ? ](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-faq-migrate)
- [Avons-nous accès aux services de consignation et quels journaux me sont accessibles ?](#ibp-v2-faq-logs)
- [Quel avantage présente l'utilisation de la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform par rapport à la version native d'Hyperledger Fabric ?](#ibp-v2-faq-native-fabric)
- [Comment puis-je optimiser les performances et mettre à l'échelle ma solution {{site.data.keyword.blockchainfull_notm}} Platform ?](#ibp-v2-faq-perf-scale)

## Quelle version d'Hyperledger Fabric est utilisée avec la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform ?
{: #ibp-v2-faq-fabric-version}
{: faq}

La version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform utilise la version 1.4 d'Hyperledger Fabric et elle inclut une prise en charge du protocole gossip, de la reconnaissance de service et des données privées.

## Dois-je payer pour la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform ?
{: #ibp-v2-faq-cost}
{: faq}

La version bêta est gratuite. Cependant, des frais peuvent vous être facturés pour votre cluster {{site.data.keyword.cloud_notm}} Kubernetes, sauf si vous avez sélectionné un cluster gratuit.  Sachez toutefois que le cluster gratuit est supprimé automatiquement au bout de 30 jours conformément à la règle de service {{site.data.keyword.cloud_notm}} Kubernetes et les performances ainsi que la capacité sont limitées.  Si vous souhaitez conserver votre service au-delà de 30 jours, vous devez payer pour une instance standard du service Kubernetes. 

## Puis-je utiliser mon cluster {{site.data.keyword.cloud_notm}} Kubernetes Service existant ?
{: #ibp-v2-faq-existing-cluster}
{: faq}

Votre cluster Kubernetes existant est compatible avec la version bêta gratuite 2.0 d'{{site.data.keyword.blockchainfull_notm}} Platform dès lors qu'il respecte les conditions suivantes :
- Il se situe dans la région de Dallas d'{{site.data.keyword.cloud_notm}}.
- Il exécute la version 1.11 ou une version suivante de Kubernetes. Consultez les instructions relatives à la [mise à niveau de la version Kubernetes de votre cluster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).
- Il y a suffisamment de ressources disponibles dans le cluster. Voir la rubrique relative aux [ressources minimum requises](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required) pour plus d'informations.

## Quelle base de données les homologues utilisent-ils pour leur registre ?
{: #ibp-v2-faq-couchDB}
{: faq}

Tous les homologues déployés avec la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform utilisent la base de données CouchDB. Il n'est cependant pas possible d'interroger directement la base de données CouchDB.

## Quels langages sont pris en charge pour les contrats intelligents ?
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform prend en charge les contrats intelligents écrits en Go, Node.js et Java. Le nouveau modèle de programmation d'Hyperledger Fabric ne prend actuellement en charge que Node.js. D'autres langages seront prochainement pris en charge. Si vous souhaitez conserver votre code de l'application existant, ou encore utiliser des logiciels SDK Fabric pour des langages autres que Node.js, vous pouvez encore vous connecter au réseau de votre version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform à l'aide d'API de logiciel SDK Fabric de niveau inférieur.

## Puis-je migrer depuis la version 2.0 bêta vers la version 2.0 en disponibilité générale ? 
{: #ibp-v2-faq-migrate}
{: faq}

Vous ne pourrez pas migrer votre offre 2.0 bêta vers l'offre 2.0 en disponibilité générale.

## Avons-nous accès aux services de consignation et quels journaux me sont accessibles ?
{: #ibp-v2-faq-logs}
{: faq}

A compter de la version 2.0 d'{{site.data.keyword.blockchainfull_notm}} Platform, vous pouvez accéder directement aux journaux de l'homologue, de l'autorité de certification et du service de tri depuis votre tableau de bord Kubernetes. Nous vous recommandons d'utiliser le service [{{site.data.keyword.cloud_notm}} LogDNA ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-kube#kube "Managing Kubernetes cluster logs with IBM Log Analysis with LogDNA") qui vous permet de facilement analyser les journaux en temps réel.

## Quel avantage présente l'utilisation de la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform par rapport à la version native d'Hyperledger Fabric ?
{: #ibp-v2-faq-native-fabric}
{: faq}

Avec la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform, les clients peuvent déployer facilement un réseau de blockchain personnalisé. La plateforme inclut une console intuitive pour le déploiement rapide d'un réseau et elle permet d'installer et d'instancier facilement des contrats intelligents. Cette plateforme vous permet également de bénéficier de l'assistance d'experts {{site.data.keyword.IBM_notm}} pour le développement de vos contrats intelligents et pour la génération de votre réseau.

## Comment puis-je optimiser les performances et mettre à l'échelle ma solution {{site.data.keyword.blockchainfull_notm}} Platform ?
{: #ibp-v2-faq-perf-scale}
{: faq}

Consultez la série de blogues [ ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Answering your questions on Hyperledger Fabric performance and scale") pour plus de détails sur les facteurs susceptibles d'affecter les performances de votre solution et sur la mise à l'échelle de votre blockchain.

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

# Domande frequenti (FAQ)
{: #ibp-v2-faq}

- [Quale versione di Hyperledger Fabric viene utilizzata con {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?](#ibp-v2-faq-fabric-version)
- [Devo pagare qualcosa per {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?](#ibp-v2-faq-cost)
- [Posso utilizzare il mio cluster {{site.data.keyword.cloud_notm}} Kubernetes Service esistente?](#ibp-v2-faq-existing-cluster)
- [Quale database utilizza i peer per il proprio libro mastro?](#ibp-v2-faq-couchDB)
- [Quali linguaggi sono supportati per gli smart contract?](#ibp-v2-faq-cc-langs)
- [Posso eseguire la migrazione da V2.0 beta a v2.0 GA? ](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-faq-migrate)
- [Abbiamo accesso ai servizi di registrazione e quali log sono disponibili per me?](#ibp-v2-faq-logs)
- [Qual è il valore dell'utilizzo di {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sull'Hyperledger Fabric nativo?](#ibp-v2-faq-native-fabric)
- [Come posso massimizzare le prestazioni e ridimensionare la mia soluzione {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-perf-scale)

## Quale versione di Hyperledger Fabric viene utilizzata con {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?
{: #ibp-v2-faq-fabric-version}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta utilizza Hyperledger Fabric v1.4 e include il supporto per gossip, rilevamento dei servizi e dati privati.

## Devo pagare qualcosa per {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?
{: #ibp-v2-faq-cost}
{: faq}

La release beta è gratuita. Ma potrebbero ancora esserci degli addebiti per il tuo cluster {{site.data.keyword.cloud_notm}} Kubernetes, a meno che non hai selezionato un cluster gratuito.  Tuttavia, tieni presente che il cluster gratuito viene automaticamente eliminato dopo 30 giorni per le politiche del servizio {{site.data.keyword.cloud_notm}} Kubernetes e le prestazioni e la capacità sono limitate.  Se vuoi mantenere il tuo servizio oltre i 30 giorni, devi pagare per un'istanza standard del servizio Kubernetes.

## Posso utilizzare il mio cluster {{site.data.keyword.cloud_notm}} Kubernetes Service esistente?
{: #ibp-v2-faq-existing-cluster}
{: faq}

Il tuo cluster Kubernetes esistente funzionerà con {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta finché soddisfa le seguenti condizioni:
- È nella regione {{site.data.keyword.cloud_notm}} Dallas.
- Sta eseguendo Kubernetes v1.11 o successiva. Vedi queste istruzioni per delle informazioni su come [eseguire l'upgrade della versione Kubernetes del tuo cluster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).
- Ci sono risorse sufficienti nel cluster. Per ulteriori informazioni, vedi questo argomento sui [requisiti della risorsa minimi](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required).

## Quale database utilizza i peer per il proprio libro mastro?
{: #ibp-v2-faq-couchDB}
{: faq}

Tutti i peer distribuiti con {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta utilizzano il database CouchDB. Non è possibile eseguire la query direttamente di CouchDB.

## Quali linguaggi sono supportati per gli smart contract?
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform supporta smart contract scritti in Go, Node.js e Java. Il nuovo modello di programmazione di Hyperledger Fabric al momento supporta solo Node.js, ulteriori linguaggi saranno disponibili in futuro. Se sei interessato nel preservare il tuo codice dell'applicazione esistente o nell'utilizzare gli SDK Fabric per linguaggi diversi da Node.js, puoi ancora connetterti alla tua rete {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta utilizzando API SDK Fabric di livello inferiore.

## Posso eseguire la migrazione da V2.0 beta a v2.0 GA? 
{: #ibp-v2-faq-migrate}
{: faq}

Non potrai migrare la tua offerta 2.0 beta all'offerta 2.0 GA.

## Abbiamo accesso ai servizi di registrazione e quali log sono disponibili per me?
{: #ibp-v2-faq-logs}
{: faq}

Con {{site.data.keyword.blockchainfull_notm}} Platform 2.0, puoi ora accedere direttamente ai tuoi log peer, CA e ordinante dal tuo dashboard Kubernetes. Ti consigliamo di avvalerti del servizio [{{site.data.keyword.cloud_notm}} LogDNA ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-kube#kube "Managing Kubernetes cluster logs with IBM Log Analysis with LogDNA") che ti consente di analizzare facilmente i log in tempo reale.

## Qual è il valore dell'utilizzo di {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sull'Hyperledger Fabric nativo?
{: #ibp-v2-faq-native-fabric}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta consente ai client di distribuire facilmente una rete blockchain personalizzata. La piattaforma include una console intuitiva per la distribuzione veloce di una rete e la capacità di installare e istanziare facilmente gli smart contract. Con la piattaforma puoi anche ricevere supporto dagli esperti in {{site.data.keyword.IBM_notm}} per avere aiuto con lo sviluppo del tuo smart contract e per la creazione della tua rete.

## Come posso massimizzare le prestazioni e ridimensionare la mia soluzione {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-perf-scale}
{: faq}

Consulta questa serie di blog [ ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Answering your questions on Hyperledger Fabric performance and scale") per i fattori che influenzano le prestazioni della tua soluzione e per delle informazioni su come può essere ridimensionato il blockchain.

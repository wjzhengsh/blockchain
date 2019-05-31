---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: FAQs, can I, upgrade, what version, peer ledger database, supported languages, why do I, regions

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

- [Quale database utilizza i peer per il proprio libro mastro?](#ibp-v2-faq-v2-IBP-Overview-1-3)
- [Quali linguaggi sono supportati per gli smart contract?](#ibp-v2-faq-v2-IBP-Overview-1-4)
- [Posso eseguire l'upgrade dalla V1.0 al nuovo {{site.data.keyword.blockchainfull_notm}} Platform? ](#ibp-v2-faq-v2-IBP-Overview-1-5)
- [Qual è il valore dell'utilizzo di {{site.data.keyword.blockchainfull_notm}} Platform sull'Hyperledger Fabric nativo?](#ibp-v2-faq-v2-IBP-Overview-1-7)
- [Cosa succede quando elimino il mio servizio {{site.data.keyword.blockchainfull_notm}} Platform? ](#ibp-v2-faq-v2-IBP-Overview-1-8)
- [Quali regioni sono disponibili per il servizio blockchain in esecuzione su {{site.data.keyword.cloud_notm}}? ](#ibp-v2-faq-v2-IBP-Overview-1-9)
- [Quale versione di Hyperledger Fabric viene utilizzata con {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-v2-Hyperledger-Fabric-3-1)
- [Posso utilizzare il mio cluster {{site.data.keyword.cloud_notm}} Kubernetes Service esistente?](#ibp-v2-faq-v2-Infrastructure-4-2)
- [Abbiamo accesso ai servizi di registrazione e quali log sono disponibili per me?](#ibp-v2-faq-v2-Logging-and-Monitoring-11-6)


## Quale database utilizza i peer per il proprio libro mastro?
{: #ibp-v2-faq-v2-IBP-Overview-1-3}
{: faq}

Tutti i peer che vengono distribuiti con {{site.data.keyword.blockchainfull_notm}} Platform utilizzano CouchDB come database per il libro mastro.

## Quali linguaggi sono supportati per gli smart contract?
{: #ibp-v2-faq-v2-IBP-Overview-1-4}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform supporta smart contract scritti in Go, Node.js. Il nuovo modello di programmazione di Hyperledger Fabric al momento supporta solo Node.js, ma ulteriori linguaggi saranno disponibili in futuro. Se sei interessato a preservare il tuo codice applicazione esistente o a utilizzare gli SDK Fabric per linguaggi diversi da Node.js, puoi ancora connetterti alla tua rete {{site.data.keyword.blockchainfull_notm}} Platform utilizzando API SDK Fabric di livello inferiore. 

## Posso eseguire l'upgrade dalla V1.0 al nuovo {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-IBP-Overview-1-5}
{: faq}

Non per il piano Starter. Non puoi eseguire l'upgrade dal piano Starter al nuovo {{site.data.keyword.blockchainfull_notm}} Platform.
Per il piano Enterprise, potrai eseguire l'upgrade al nuovo {{site.data.keyword.blockchainfull_notm}} Platform in futuro. Il proprietario dell'account riceverà un'email dal team di {{site.data.keyword.blockchainfull_notm}} Platform per assistenza.

## Qual è il valore dell'utilizzo di {{site.data.keyword.blockchainfull_notm}} Platform sull'Hyperledger Fabric nativo?
{: #ibp-v2-faq-v2-IBP-Overview-1-7}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform consente ai client di distribuire facilmente una rete blockchain personalizzata. Puoi utilizzare un'IU della console intuitiva per distribuire velocemente la tua rete, installare e istanziare facilmente gli smart contract e monitorare le tue transazioni.

## Cosa succede quando elimino il mio servizio {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-IBP-Overview-1-8}
{: faq}

Quando elimini un'istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform, viene eliminata solo la console. Tutti i componenti che sono stati creati con la console e la loro archiviazione associata non vengono eliminati. Anche il cluster Kubernetes e tutti i componenti importati non vengono eliminati. Continuerai a ricevere degli addebiti per la tua archiviazione e i tuoi nodi kuberenetes finché non li elimini tu stesso utilizzando il dashboard kubernetes o i comandi CLI.

## Quali regioni sono disponibili per il servizio blockchain in esecuzione su {{site.data.keyword.cloud_notm}}? 
{: #ibp-v2-faq-v2-IBP-Overview-1-9}
{: faq}

Le regioni disponibili per {{site.data.keyword.blockchainfull_notm}} Platform sono elencate nelle [Ubicazioni {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain?topic=blockchain-ibp-regions-locations). Tieni presente che devi creare un cluster {{site.data.keyword.cloud_notm}} Kubernetes Service nella stessa regione in modo che il servizio blockchain riconosca il cluster. Ulteriori regioni saranno disponibili a breve.

## Quale versione di Hyperledger Fabric viene utilizzata con {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-Hyperledger-Fabric-3-1}
{: faq}

A partire da febbraio 2019, la beta è stata avviata utilizzando Hyperledger Fabric 1.4.0.
A partire dal 29 maggio 2019, la GA è stata avviata utilizzando Hyperledger Fabric 1.4.1. Hyperledger Fabric 1.4.0 non è supportato nel prodotto GA.

## Posso utilizzare il mio cluster {{site.data.keyword.cloud_notm}} Kubernetes Service esistente?
{: #ibp-v2-faq-v2-Infrastructure-4-2}
{: faq}

Il tuo cluster Kubernetes esistente funzionerà con {{site.data.keyword.blockchainfull_notm}} Platform finché soddisfa le seguenti condizioni:
- Sta eseguendo Kubernetes v1.11 o una versione stabile successiva. 
- Ci sono risorse sufficienti nel cluster.

## Abbiamo accesso ai servizi di registrazione e quali log sono disponibili per me?
{: #ibp-v2-faq-v2-Logging-and-Monitoring-11-6}
{: faq}

Con {{site.data.keyword.blockchainfull_notm}} Platform, puoi ora accedere direttamente ai tuoi log peer, CA e ordinante dal tuo dashboard Kubernetes. Ti consigliamo di avvalerti del servizio {{site.data.keyword.cloud_notm}} LogDNA che ti consente di analizzare facilmente i log in tempo reale.

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


# Dichiarazione di non responsabilità
{: #disclainer}

**ATTENZIONE:** devi riesaminare le seguenti informazioni prima di utilizzare qualsiasi piano {{site.data.keyword.blockchainfull}}.

## Istruzione di supporto IBM

La lunga tradizione di IBM di leadership nell'innovazione continua con i piani di offerta {{site.data.keyword.blockchainfull_notm}} su {{site.data.keyword.Bluemix_notm}}. {{site.data.keyword.blockchain}} è una tecnologia in rapido sviluppo destinata a cambiare profondamente il settore finanziario, le catene di approvvigionamento locali e globali e il supporto logistico in qualsiasi numero di spazi di business. Attraverso vari programmi di adozione iniziali, i clienti e business partner IBM hanno portato attivamente la blockchain a diventare una soluzione industriale. {{site.data.keyword.blockchainfull_notm}} su {{site.data.keyword.Bluemix_notm}} è uno di questi programmi. **Come con qualsiasi nuova tecnologia, gli utenti di {{site.data.keyword.blockchainfull_notm}} su {{site.data.keyword.Bluemix_notm}} devono essere consapevoli della possibilità di cambiamenti rapidi e importanti**.  
{:shortdesc}

L'architettura che sta dietro {{site.data.keyword.blockchainfull_notm}} è il progetto Hyperledger della Linux Foundation. Ogni contributo della community open source apporta miglioramenti a Hyperledger Fabric ma può presentare delle difficoltà di adozione. **IBM mette in guardia dalla definizione o dallo scambio di asset finanziari, o di qualsiasi asset di valore, direttamente sulle reti blockchain Hyperledger Fabric**.  

## Istruzione open source

I piani di offerta {{site.data.keyword.blockchainfull_notm}} su {{site.data.keyword.Bluemix_notm}} sono sviluppati sullo stack Hyperledger Fabric della Linux Foundation. I membri del progetto Hyperledger, compresa IBM, continuano a contribuire ai vari progetti secondari che ricadono nell'ambito di Hyperledger.  Tutti i contributi sono revisionati, controllati e testati dalla community.

## Istruzione di supporto del chaincode

Le seguenti prassi di codifica NON sono supportate sulle reti {{site.data.keyword.blockchainfull_notm}}:

1. Utilizzo di array associativi con iterazione (l'ordine è casuale in Go).
2. Lettura di un elenco di voci da una tabella KVS (l'ordine non è garantito).
3. Scrittura del chaincode thread-unsafe (query e richiamo potrebbero essere chiamati in parallelo).
4. Sostituzione della memoria globale o della memoria cache per le variabili di stato del libro mastro nel chaincode.
5. Accesso ai servizi esterni, ad esempio database, direttamente dal chaincode.
6. Utilizzo di librerie o variabili globali che potrebbero introdurre il non determinismo (ad esempio, l'uso di "random" o "time").  

Si sconsiglia inoltre di scrivere un chaincode non deterministico, che introduce un rischio per la congruenza e l'integrità dei dati.  Nota: l'architettura Hyperledger Fabric è progettata per contrastare un chaincode non deterministico tramite una serie di controlli di approvazione e di convalida. Tuttavia, sei comunque vivamente invitato a codificare delle funzioni deterministiche che non dipendano da variabili globali non statiche, ad esempio, il tempo.  

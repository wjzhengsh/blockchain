---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Dichiarazione di non responsabilità
{: #disclaimer}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


**ATTENZIONE:** devi riesaminare le seguenti informazioni prima di utilizzare qualsiasi piano {{site.data.keyword.blockchainfull}}.

## Istruzione di supporto IBM
{: #disclaimer-support-statement}

La lunga tradizione di {{site.data.keyword.IBM}} di leadership nel campo dell'innovazione continua con le offerte {{site.data.keyword.blockchainfull_notm}} Platform su {{site.data.keyword.Bluemix_notm}}. Blockchain è una tecnologia in rapido sviluppo destinata a cambiare profondamente il settore finanziario, le catene di approvvigionamento locali e globali e il supporto logistico in qualsiasi numero di spazi di business. Attraverso vari programmi di adozione precoce, i clienti e business partner {{site.data.keyword.IBM_notm}} hanno portato attivamente la blockchain a diventare una soluzione industriale. {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} è uno di questi programmi. **Come con qualsiasi nuova tecnologia, gli utenti di {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} devono essere consapevoli della possibilità di cambiamenti rapidi e importanti**.
{:shortdesc}

L'architettura che sta dietro {{site.data.keyword.blockchainfull_notm}} è il progetto Hyperledger della Linux Foundation. Ogni contributo della community open source apporta miglioramenti a Hyperledger Fabric ma può presentare delle difficoltà di adozione. **{{site.data.keyword.IBM_notm}} mette in guardia contro la definizione o lo scambio di risorse finanziarie<!--, or any assets of value,--> direttamente sulle reti blockchain di Hyperledger Fabric**.

{{site.data.keyword.IBM_notm}} non fornisce supporto per le reti che utilizzano Hyperledger Composer in produzione, compresi la CLI Composer, le API JavaScript, il server REST e Web Playground.
{:note}

## Istruzione open source
{: #disclaimer-open-source-statement}

I piani di offerta {{site.data.keyword.blockchainfull_notm}} su {{site.data.keyword.Bluemix_notm}} sono sviluppati sullo stack Hyperledger Fabric della Linux Foundation. I membri del progetto Hyperledger, compresa {{site.data.keyword.IBM_notm}}, continuano a contribuire ai vari progetti secondari che ricadono nell'ambito di Hyperledger.  Tutti i contributi sono revisionati, controllati e testati dalla community.

## Istruzione di supporto del chaincode
{: #disclaimer-chaincode-support-statement}

Le seguenti prassi di codifica NON sono supportate sulle reti {{site.data.keyword.blockchainfull_notm}}:

1. Utilizzo di array associativi con iterazione (l'ordine è casuale in Go).
2. Lettura di un elenco di voci da una tabella KVS (l'ordine non è garantito).
3. Scrittura del chaincode thread-unsafe (query e richiamo potrebbero essere chiamati in parallelo).
4. Sostituzione della memoria globale o della memoria cache per le variabili di stato del libro mastro nel chaincode.
5. Accesso ai servizi esterni, ad esempio database, direttamente dal chaincode.
6. Utilizzo di librerie o variabili globali che potrebbero introdurre il non determinismo (ad esempio, l'uso di "random" o "time").

Si sconsiglia inoltre di scrivere un chaincode non deterministico, che introduce un rischio per la congruenza e l'integrità dei dati. Nota: l'architettura Hyperledger Fabric è progettata per contrastare un chaincode non deterministico tramite una serie di controlli di approvazione e di convalida. Tuttavia, sei comunque vivamente invitato a codificare delle funzioni deterministiche che non dipendano da variabili globali non statiche, ad esempio, il tempo.

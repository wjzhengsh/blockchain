---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitoraggio di una rete {{site.data.keyword.blockchain}}

Questa esercitazione ti mostra come visualizzare e monitorare le informazioni sullo stato della tua rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}}.
{:shortdesc}


## Monitoraggio dei canali

Accedi a Network Monitor e individua il canale che vuoi visualizzare e monitorare nella schermata "Channel".  Nella schermata per lo specifico canale, puoi visualizzare le informazioni sullo stato dei dati, i membri e il chaincode istanziato di questo canale in tre schede:

* **Channel Overview**  
  La scheda "Channel Overview" mostra le informazioni sui blocchi su questo canale:
    * Viene creata una serie di punti di dati che includono il numero totale di blocchi creati, l'intervallo di tempo dall'ultima transazione, il numero di istanziazioni del chaincode e il numero di richiami del chaincode.
    * Una tabella che elenca tutti i blocchi su questo canale. Espandi un blocco e potrai vedere le informazioni dettagliate ad esso relativo.  

  ![Panoramica del canale](../images/channel_overview_detail.png "Panoramica del canale")  

* **Members**  
  La scheda "Members" mostra le informazioni dei membri di questo canale, compresi gli indirizzi email per gli operatori organizzativi.
  ![Membri del canale](../images/channel_members.png "Membri del canale")  

* **Chaincode**  
  La scheda "Chaincode" elenca tutti i chaincode istanziati su questo canale con ID chaincode, versione e numero di peer che stanno eseguendo il chaincode.   

  Espandi una riga di chaincode per ottenere informazioni dettagliate sul chaincode:  
    * Puoi fare clic su **JSON** per visualizzare il file JSON del chaincode.
    * Puoi fare clic su **Logs** per visualizzare i log del chaincode.
    * Puoi fare clic su **Delete** per rimuovere il contenitore chaincode in esecuzione.
    **Nota**: al momento l'eliminazione del contenitore chaincode in esecuzione non elimina il chaincode. Un chaincode istanziato sulla rete blockchain non può essere eliminato.

  ![Chaincode del canale](../images/channel_chaincode.png "Chaincode del canale")


## Monitoraggio dei chaincode

Accedi al Network Monitor e apri la schermata "Install Code" (schermata "Chaincode" nella rete Enterprise Plan). Se hai dei chaincode in esecuzione, scegli un peer dall'elenco a discesa; potrai vedere tutti i chaincode per questo peer nella tabella con versioni e ID del chaincode. Puoi eseguire l'installazione e l'istanziazione per il tuo chaincode in questa schermata. Per ulteriori informazioni, vedi [Installazione e istanziazione di un chaincode](install_instantiate_chaincode.html).

  ![Chaincode](../images/chaincode_install_overview.png "Chaincode")


## Monitoraggio di applicazioni di esempio

In una rete Starter Plan, puoi visualizzare e accedere alle applicazioni di esempio nella schermata "Try Samples" del Network Monitor.  Dopo che hai distribuito un'applicazione di esempio, puoi fare clic sul pulsante **Launch** per accedere alla tua interfaccia applicativa oppure sul link **View on GitHub** per visualizzare il repository del codice. Per ulteriori informazioni, vedi [Distribuzione di applicazioni di esempio](prebuilt_samples.html).

  ![Applicazioni di esempio](../images/sampleappflow0.png "Applicazioni di esempio")

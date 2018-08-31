---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitoraggio di una rete blockchain

Questa esercitazione ti mostra come visualizzare e monitorare le informazioni sullo stato della tua rete {{site.data.keyword.blockchain}} su {{site.data.keyword.cloud_short}}.
{:shortdesc}


## Monitoraggio di peer, ordinanti e CA
{: #monitor-nodes}

Puoi immettere una richiesta HTTP **HEAD** su uno dei nodi di rete per controllare lo stato del nodo. Un nodo di rete può essere un peer, un ordinante oppure una CA nella tua rete blockchain. Una richiesta **HEAD** è simile a una richiesta GET e invia solo le intestazioni senza includere i corpi. Puoi ottenere una risposta 200 se il nodo funziona normalmente.

1. Nella schermata "Panoramica" del monitoraggio della rete, fai clic su **Profilo connessione**. Puoi quindi fare clic su **JSON non elaborato** per visualizzare il profilo di connessione nel tuo browser web oppure fare clic su **Scarica** per salvare il profilo di connessione in locale.
2. Nel profilo di connessione, trova le informazioni sull'url del nodo di rete che vuoi controllare. Ad esempio, l'url dell'ordinante `fabric-orderer-20190b` è `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.  
    ![Esempio di url dell'ordinante](../images/orderer_url.png "Esempio di URL dell'ordinante")
3. Sostituisci **grpcs** con **https** nell'url. Nell'esempio precedente, l'url diventa `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Immetti la richiesta **HEAD** sull'url con uno strumento quale curl oppure l'applicazione Chrome Postman.
    - Se ottieni una risposta di stato 200, il tuo nodo della rete funziona normalmente.
    - Se la richiesta **HEAD** non riesce con un errore di connessione, il tuo nodo di rete potrebbe non essere in esecuzione, l'url del nodo è errato oppure un firewall blocca il tuo accesso al nodo. Devi risolvere questo errore, altrimenti le tue applicazioni non possono connettersi al nodo.

Il seguente esempio mostra una richiesta **HEAD** con una risposta 200 in curl. Nota che puoi ignorare l'errore grpc perché la richiesta HTTP **HEAD** controlla se il nodo è accessibile. In caso affermativo, la richiesta grpc al nodo funziona anche nella tua applicazione.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

Il seguente esempio mostra una richiesta **HEAD** con un errore di connessione in curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

La seguente figura mostra una richiesta **HEAD** con una risposta 200 nell'applicazione Chrome Postman.  

![Esempio Postman di richiesta HEAD](../images/orderer_head_postman.png "Esempio Postman di richiesta HEAD")


## Monitoraggio dei canali
{: #monitor-channnels}

Accedi a monitoraggio della rete e individua il canale che vuoi visualizzare e monitorare nella schermata "Canale".  Nella schermata per lo specifico canale, puoi visualizzare le informazioni sullo stato dei dati, i membri e il chaincode istanziato di questo canale in tre schede:

* **Panoramica canale**  
  La scheda "Panoramica canale" mostra le informazioni sui blocchi su questo canale:
    * Viene creata una serie di punti di dati che includono il numero totale di blocchi creati, l'intervallo di tempo dall'ultima transazione, il numero di istanziazioni del chaincode e il numero di richiami del chaincode.
    * Una tabella che elenca tutti i blocchi su questo canale.  Espandi un blocco e potrai vedere le informazioni dettagliate ad esso relativo.  

  ![Panoramica del canale](../images/channel_overview_detail.png "Panoramica del canale")  

* **Membri**  
  La scheda "Membri" mostra le informazioni dei membri di questo canale, compresi gli indirizzi email per gli operatori organizzativi.
  ![Membri del canale](../images/channel_members.png "Membri del canale")  

* **Chaincode**  
  La scheda "Chaincode" elenca tutto il chaincode istanziato su questo canale con ID chaincode, versione e numero di peer che stanno eseguendo il chaincode.   

  Espandi una riga di chaincode per ottenere informazioni dettagliate sul chaincode:  
    * Puoi fare clic su **JSON** per visualizzare il file JSON del chaincode.
    * Puoi fare clic su **Log** per visualizzare i log del chaincode.
    * Puoi fare clic su **Elimina** per rimuovere il contenitore chaincode in esecuzione.
    **Nota**: al momento l'eliminazione del contenitore chaincode in esecuzione non elimina il chaincode. Un chaincode istanziato sulla rete blockchain non può essere eliminato.

  ![Chaincode del canale](../images/channel_chaincode.png "Chaincode del canale")


## Monitoraggio del chaincode
{: #monitor-chaincode}

Accedi al monitoraggio della rete e apri la schermata "Installa il codice". Se hai del chaincode in esecuzione, scegli un peer dall'elenco a discesa; potrai vedere tutto il chaincode per questo peer nella tabella con versioni e ID del chaincode.  Puoi eseguire l'installazione e l'istanziazione per il tuo chaincode in questa schermata.  Per ulteriori informazioni, vedi [Installazione, istanziazione e aggiornamento di un chaincode](install_instantiate_chaincode.html).

  ![Chaincode](../images/chaincode_install_overview.png "Chaincode")


## Monitoraggio di applicazioni di esempio
{: #monitor-apps}

In una rete piano Starter, puoi visualizzare e accedere alle applicazioni di esempio nella schermata "Prova gli esempi" del monitoraggio della rete.  Dopo che hai distribuito un'applicazione di esempio, puoi fare clic sul pulsante **Avvia** per accedere alla tua interfaccia applicativa oppure sul link **Visualizza in GitHub** per visualizzare il repository del codice.  Per ulteriori informazioni, vedi [Distribuzione di applicazioni di esempio](prebuilt_samples.html).

  ![Applicazioni di esempio](../images/sampleappflow0.png "Applicazioni di esempio")

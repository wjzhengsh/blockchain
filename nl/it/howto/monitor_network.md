---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitoraggio di una rete blockchain


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Questa esercitazione ti mostra come visualizzare e monitorare le informazioni sullo stato della tua rete {{site.data.keyword.blockchain}} su {{site.data.keyword.cloud_notm}}.
{:shortdesc}


## Monitoraggio di peer, ordinanti e CA
{: #monitor-nodes}

Puoi immettere una richiesta HTTP **HEAD** su uno dei nodi di rete per controllare lo stato del nodo. Un nodo di rete può essere un peer, un ordinante oppure una CA nella tua rete blockchain. Una richiesta **HEAD** è simile a una richiesta GET e invia solo le intestazioni senza includere i corpi. Puoi ottenere una risposta 200 se il nodo funziona normalmente.

1. Nella schermata "Panoramica" del Monitoraggio della rete, fai clic su **Profilo connessione**. Puoi quindi fare clic su **JSON non elaborato** per visualizzare il profilo di connessione nel tuo browser web oppure fare clic su **Scarica** per salvare il profilo di connessione in locale.
2. Nel profilo di connessione, trova le informazioni sull'url del nodo di rete che vuoi controllare. Ad esempio, l'url dell'ordinante `fabric-orderer-20190b` è `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.  
    ![Esempio di url dell'ordinante](../images/orderer_url.png "Esempio di URL dell'ordinante")
3. Sostituisci **grpcs** con **https** nell'url. Nell'esempio precedente, l'url diventa `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Immetti la richiesta **HEAD** sull'url con uno strumento quale curl oppure l'applicazione Chrome Postman.
    - Se ottieni una risposta di stato 200, il tuo nodo della rete funziona normalmente.
    - Se la richiesta **HEAD** non riesce con un errore di connessione, il tuo nodo di rete potrebbe non essere in esecuzione, l'url del nodo è errato oppure un firewall blocca il tuo accesso al nodo.  Devi risolvere questo errore, altrimenti le tue applicazioni non possono connettersi al nodo.

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

## Utilizzo dei log di rete
La schermata "Panoramica" del Monitoraggio della rete visualizza lo stato del servizio ordini, dell'Autorità di certificazione e dei peer. Fai clic su **Visualizza log** dall'elenco a discesa sotto l'intestazione **Azioni** per visualizzare i log di uno specifico componente di rete. Se utilizzi le reti piano Enterprise, puoi visualizzare i log dei componenti in un formato file di testo. Se utilizzi le reti piano Starter, i log dei componenti vengono raccolti dal [{{site.data.keyword.cloud_notm}} servizio Log Analysis ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/log-analysis) e puoi visualizzare i log in [Kibana](#viewing-logs-in-kibana-in-starter-plan).

Ogni componente genera log da diverse attività. Questo è dovuto al fatto che ogni componente svolge diversi ruoli all'interno dell'[architettura di rete ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) e dei [flussi di transazioni ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html) di Hyperledger Fabric.

- **Log del servizio ordini**  
  Il servizio ordini è il componente di associazione comune della rete blockchain. Tutte le proposte di transazione approvate dai peer, gli aggiornamenti di canale o gli aggiornamenti di adesione alla rete vengono inviati al servizio ordini per la verifica. Pertanto, il servizio ordini contiene i log relativi a quando è stata avviata la rete. Contiene anche i log per una transazione che è stata rifiutata perché non è stata adeguatamente approvata dalle organizzazioni corrette. Puoi inoltre trovare i log relativi a quando i canali vengono creati o aggiornati o quando un aggiornamento del canale non riesce.

- **Log dell'Autorità di certificazione**  
  L'Autorità di certificazione gestisce l'identità dei partecipanti all'interno della rete. Nei log dell'Autorità di certificazione, puoi trovare i log da quando i partecipanti generano le chiavi pubbliche e private per comunicare con la rete (iscrizione) o quando nuovi membri, peer o applicazioni si registrano con l'Autorità di certificazione. Puoi anche utilizzare i log CA per eseguire il debug in caso di problemi con la verifica del certificato.

- **Log del peer**  
  I log del peer contengono i risultati dell'installazione, dell'istanziazione e del richiamo del chaincode. Puoi cercare un nome e una versione di chaincode per trovare i log di un determinato chaincode. Puoi anche visualizzare i log di uno specifico chaincode dalla [sezione chaincode del monitoraggio del canale](#monitor-channel-cc). I messaggi, generati dalle tue proposte di transazione, o eventuali problemi di timeout con le richieste di proposta, possono essere trovati nei log del peer. Puoi anche trovare i risultati delle richieste di unione del canale.

Hyperledger Fabric fornisce diversi [livelli di registrazione ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/logging-control.html "controllo registrazione") in base alla severità del messaggio. Il livello di registrazione predefinito su {{site.data.keyword.blockchainfull_notm}} Platform è `INFO`. Per visualizzare ulteriori log, apri un [ticket di supporto](../ibmblockchain_support.html#submitting-support-cases) per impostare il livello di registrazione sul `DEBUG` più dettagliato. Tieni presente che i log di livello `DEBUG` visualizzano una grande quantità di messaggi gossip che potresti dover filtrare. Ricerca `warning` o `error` nei tuoi messaggi per rilevare problemi dai componenti di Hyperledger Fabric. Per rilevare se il contenitore del componente ha esito negativo o viene terminato, cerca i messaggi `panic` o `killed` inviati da {{site.data.keyword.cloud_notm}}.

## Visualizzazione dei log in Kibana nel piano Starter
I log della tua rete piano Starter vengono raccolti dal [servizio Log Analysis di {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/log-analysis "servizio Log Analysis"). Per impostazione predefinita, i tuoi log sono raccolti dal piano Lite del servizio Log Analysis. Questo piano è gratuito e **memorizza i tuoi log per 3 giorni** prima di eliminarli. Ti consente inoltre di **cercare solo i primi 500 MB di log al giorno**. Se i tuoi log di rete superano i 500 MB, non puoi visualizzare i nuovi log in Kibana. Se la tua rete genera più di 500 MB di log o se vuoi conservare i log per più di 3 giorni, puoi eseguire l'upgrade a una versione a pagamento del servizio Log Analysis.

Nella schermata "Panoramica" del Monitoraggio della rete, fai clic su **Visualizza log** dall'elenco a discesa sotto l'intestazione **Azioni** per aprire i log di ogni componente di rete nell'interfaccia Kibana. Quando Kibana si apre, visualizza i log filtrati in base a una barra di ricerca nella parte superiore. Ad esempio, quando fai clic per visualizzare i log del tuo peer, la ricerca viene filtrata in base all'ID di rete e all'ID peer: `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939" && NODE_NAME_str:"org1-peer1"`. Puoi anche immettere un campo aggiuntivo nella barra di ricerca se desideri visualizzare log più specifici. Ad esempio, puoi aggiungere `&& "marbles"` per visualizzare i log dal chaincode `"marbles"`. L'eliminazione del termine del componente specifico e la ricerca solo con l'ID di rete, ad esempio `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939"`, visualizza i log provenienti da tutti i componenti di rete.

Puoi utilizzare il pulsante dell'intervallo di tempo nell'angolo superiore destro per modificare il periodo di tempo in cui i log vengono visualizzati. Puoi anche utilizzare la scheda sul lato sinistro della schermata per aggiungere e rimuovere i campi dalla ricerca. Il campo più importante da visualizzare è il campo del messaggio. Potrebbe essere utile effettuare una ricerca con un messaggio senza la data/ora per trovare tutte le istanze del log di tale messaggio. Fai clic sul pulsante **Salva** per salvare la ricerca corrente e ritornare a una specifica vista. Per ulteriori informazioni sulla visualizzazione dei dati in Kibana, consulta la [Guida utente Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/guide/en/kibana/6.2/index.html "Guida utente Kibana"). Puoi anche [scaricare i log](https://console.bluemix.net/docs/services/CloudLogAnalysis/how-to/manage-logs/downloading_logs_cloud.html#downloading_logs) nel tuo file system locale utilizzando la CLI di Log Analysis.

**Nota:** per impostazione predefinita, Kibana è preconfigurato per mostrare i log da 30 giorni di attività. Se non c'è stata attività negli ultimi 30 giorni, vedrai un messaggio che indica *Nessun risultato trovato*. Per visualizzare altri log, puoi fare clic sull'icona del timer nell'angolo superiore destro sotto il tuo nome utente e impostare un intervallo di tempo più ampio, ad esempio *Da inizio anno*.  

## Monitoraggio dei canali
{: #monitor-channnels}

Accedi a Monitoraggio della rete e individua il canale che vuoi visualizzare e monitorare nella schermata "Canale".  Nella schermata per lo specifico canale, puoi visualizzare le informazioni sullo stato dei dati, i membri e il chaincode istanziato di questo canale in tre schede:

### Panoramica canale
{: #monitor-channel-overview}

La scheda "Panoramica canale" mostra le informazioni sui blocchi su questo canale:
  * Viene creata una serie di punti di dati che includono il numero totale di blocchi creati, l'intervallo di tempo dall'ultima transazione, il numero di istanziazioni del chaincode e il numero di richiami del chaincode.
  * Una tabella che elenca tutti i blocchi su questo canale.  Espandi un blocco e potrai vedere le informazioni dettagliate ad esso relativo.  

  ![Panoramica del canale](../images/channel_overview_detail.png "Panoramica del canale")  

### Membri  
{: #monitor-channel-members}

La scheda "Membri" mostra le informazioni dei membri di questo canale, compresi gli indirizzi email per gli operatori organizzativi.

  ![Membri del canale](../images/channel_members.png "Membri del canale")  

### Chaincode
{: #monitor-channel-cc}

La scheda "Chaincode" elenca tutto il chaincode istanziato su questo canale con ID chaincode, versione e numero di peer che stanno eseguendo il chaincode.   

Espandi una riga di chaincode per ottenere informazioni dettagliate sul chaincode:  
  * Puoi fare clic su **JSON** per visualizzare il file JSON del chaincode.
  * Puoi fare clic su **Log** per visualizzare i log del chaincode. Questa vista mostra i log del peer da cui hai installato il chaincode e filtrati con il nome e la versione del chaincode.

    Si consiglia di aggiungere messaggi di errore o di esito positivo univoci dopo ogni funzione chaincode per consentire il monitoraggio e il debug del chaincode. Se hai un chaincode complesso che utilizza molti file diversi, puoi aggiungere una parola chiave univoca nei tuoi log del chaincode che può aiutarti a individuare i messaggi dalle diverse fasi della transazione.
   * Puoi fare clic su **Elimina** per rimuovere il contenitore chaincode in esecuzione. Nota che l'eliminazione del contenitore chaincode in esecuzione non elimina effettivamente il chaincode. Un chaincode istanziato sulla rete blockchain non può essere eliminato.

  ![Chaincode del canale](../images/channel_chaincode.png "Chaincode del canale")


## Monitoraggio del chaincode
{: #monitor-chaincode}

Accedi al Monitoraggio della rete e apri la schermata "Installa il codice". Se hai il chaincode in esecuzione, puoi visualizzare il chaincode con gli ID e le versioni nella tabella. Scegli un peer dall'elenco a discesa e potrai vedere tutto il chaincode per questo peer nella tabella. Puoi visualizzare i log del chaincode nella [scheda "Chaincode"](#monitor-channel-cc) della schermata "Canale" specifica.

  ![Chaincode](../images/installed_cc.png "Chaincode")

<!----
## Monitoring sample applications
{: #monitor-apps}

In a Starter Plan network, you can view and access sample applications in the "Try Samples" screen of the Network Monitor.  After you deploy a sample application, you can click the **Launch** button to enter your application interface, or the **View on GitHub** link to visit the code repository.  For more information, see [Deploying sample applications](prebuilt_samples.html).

  ![Sample applications](../images/sampleappflow0.png "Sample applications")
--->

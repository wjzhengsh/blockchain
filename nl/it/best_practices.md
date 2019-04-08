---

copyright:
  years: 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Procedure consigliate per lo sviluppo dell'applicazione
{: #best-practices-app}

Questa guida è pensata per gli utenti che hanno già imparato le basi dello sviluppo dell'applicazione e sono pronti per ridimensionare la propria soluzione. Segui queste procedure consigliate per massimizzare le prestazioni della tua rete ed evitare il tempo di inattività dell'applicazione.
{:shortdesc}

## Connettività e disponibilità delle applicazioni
{: #best-practices-app-connectivity-availability}

Il [Flusso di transazioni ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flusso di transazioni"){:new_window} di Hyperledger Fabric si estende su più componenti, dove le applicazioni client svolgono un ruolo unico. L'SDK invia le proposte di transazione ai peer per ottenere l'approvazione. Dopo raccoglie le proposte approvate da inviare al servizio ordini, che quindi invia blocchi di transazioni ai peer da aggiungere ai libri mastro del canale. Gli sviluppatori di applicazioni di produzione devono essere preparati a gestire le interazioni tra l'SDK e le loro reti per l'efficienza e la disponibilità.

### Gestione delle transazioni
{: #best-practices-app-managing-transactions}

I client dell'applicazione devono garantire che le loro proposte di transazione siano convalidate e vengano completate correttamente. Una proposta può essere ritardata o persa per diversi motivi, ad esempio un'interruzione della rete o un guasto di un componente. Devi codificare la tua applicazione per l'[alta disponibilità](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) per gestire i guasti dei componenti. Puoi anche [aumentare i valori di timeout](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk) nella tua applicazione per impedire che le proposte scadano prima che la rete possa rispondere.

Se un chaincode non è in esecuzione, la prima proposta di transazione inviata a questo chaincode avvia il chaincode. Mentre il chaincode viene avviato, tutte le altre proposte vengono rifiutate con un errore che indica che il chaincode è attualmente in fase di avvio. Questo è diverso dall'invalidità della transazione. Se una proposta viene rifiutata mentre il chaincode è in fase di avvio, i client dell'applicazione devono inviare nuovamente le proposte rifiutate dopo l'avvio del chaincode. I client dell'applicazione possono utilizzare una coda messaggi per evitare di perdere le proposte di transazione.

Puoi utilizzare un servizio eventi basato sul canale per monitorare le transazioni e creare le code messaggi. La classe [channelEventHub ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} può registrare listener in base a eventi di transazione, blocco e chaincode. I listener basati sul canale dall'eventhub di canale possono essere ridimensionati a più canali e possono distinguere il traffico su diversi canali.

Ti consigliamo di utilizzare channelEventHub invece della vecchia classe EventHub. EventHub è a thread singolo e contiene eventi da tutti i canali che potrebbero rallentare, o anche bloccare, i listener nei canali. La classe eventHub non fornisce inoltre alcuna garanzia che un evento verrà recapitato e non fornisce alcun modo per richiamare eventi da un punto specifico, ad esempio un numero di blocco, per tracciare eventi che erano stati mancati.

**Nota:** la classe EventHub peer verrà dichiarata obsoleta in una release futura dell'SDK Fabric. Se hai delle applicazioni esistenti che utilizzano la classe EventHub peer, aggiorna le tue applicazioni per utilizzare invece la classe EventHub di canale. Per ulteriori informazioni, vedi [How to use the channel-based event service ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} nella documentazione dell'SDK Node.

### Apertura e chiusura di connessioni di rete
{: #best-practices-app-connections}

Quando crei oggetti peer e orderer (ordinante) con l'SDK prima di inoltrare le proposte di transazione, stai aprendo una connessione gRPC tra la tua applicazione e il componente di rete. Ad esempio, il seguente comando apre una connessione a `org1-peer1`. Questa connessione continua a essere attiva mentre la tua applicazione è in esecuzione.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

Quando gestisci le connessioni tra la tua applicazione e la tua rete, potresti prendere in considerazione i seguenti suggerimenti.

- Riutilizza gli oggetti peer e orderer (ordinante) quando interagisci con la tua rete invece di aprire nuove connessioni per inoltrare le transazioni. Il riutilizzo di oggetti peer e orderer (ordinante) consente di risparmiare risorse e produrre migliori prestazioni.  
- Per mantenere una connessione persistente ai tuoi componenti di rete, utilizza i [keepalive gRPC ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "gRPC Keepalive"). I keepalive mantengono attiva la connessione gRPC e impediscono la chiusura di una connessione "inutilizzata". Il seguente esempio di connessione peer aggiunge opzioni gRPC all'oggetto delle [ConnectionOpts![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "Connessione"). Le opzioni gRPC sono impostate sui valori suggeriti da {{site.data.keyword.blockchainfull_notm}} Platform.  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  Puoi anche trovare queste variabili con le impostazioni suggerite nella sezione `"peers"` del tuo profilo di connessione di rete. Le opzioni suggerite verranno importate nella tua applicazione automaticamente se utilizzi il [profilo di connessione con l'SDK](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile) per stabilire una connessione ai tuoi endpoint di rete.

- Quando una connessione non è più necessaria, usa i comandi `peer.close()` e `orderer.close()` per liberare risorse e impedire una riduzione delle prestazioni. Per ulteriori informazioni, vedi le classi [close di peer ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "close di peer") e [close di orderer![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "close di orderer") nella documentazione dell'SDK Node. Se hai bisogno di un profilo di connessione per aggiungere peer e ordinanti a un oggetto canale, puoi chiudere tutte le connessioni assegnate a tale canale utilizzando il comando `channel.close()`.

### Applicazioni altamente disponibili
{: #best-practices-app-ha-app}

Come procedura ottimale per l'alta disponibilità, si consiglia vivamente di distribuire almeno due peer per ogni organizzazione per il failover. Devi anche adattare le tue applicazioni per l'alta disponibilità. Installa il chaincode su entrambi i peer e aggiungili ai tuoi canali. Quindi, preparati a inviare le proposte di transazione a entrambi gli endpoint del peer durante l'impostazione della rete e la creazione dell'elenco di destinazione del peer. Le reti piano Enterprise hanno più ordinanti per failover, il che consente alla tua applicazione client di inviare transazioni approvate a un ordinante differente nel caso in cui un ordinante non sia disponibile. Se utilizzi il tuo [profilo di connessione](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) invece di aggiungere gli endpoint di rete manualmente, assicurati che il tuo profilo sia aggiornato e che i peer e gli ordinanti supplementari siano stati aggiunti al canale pertinente nella sezione `channels` del profilo. L'SDK può quindi aggiungere i componenti uniti al canale utilizzando il profilo di connessione.

## Abilitazione di TLS reciproco
{: #best-practices-app-mutual-tls}

Se esegui le reti piano Enterprise a livello di Fabric V1.1, hai la possibilità di [abilitare il TLS reciproco](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) per le tue applicazioni. Se abiliti il TLS reciproco, devi aggiornare le tue applicazioni per supportare questa funzione. In caso contrario, le tue applicazioni non possono comunicare con la tua rete.

Nel profilo di connessione, individua la sezione `certificateAuthorities` dove puoi trovare i seguenti attributi che sono necessari per registrare e ottenere i certificati per comunicare con la tua rete utilizzando il TLS reciproco.

- `url`: l'URL per il collegamento alla CA che può fornire i certificati del TLS reciproco
- `enrollId`: l'ID di iscrizione da utilizzare per ottenere un certificato
- `enrollSecret`: il segreto di iscrizione da utilizzare per ottenere un certificato
- `x-tlsCAName`: il nome CA da utilizzare per ottenere il certificato che consente all'applicazione di comunicare con il TLS reciproco.

Per ulteriori informazioni sull'aggiornamento delle tue applicazioni in modo che supportino il TLS reciproco, vedi [How to configure mutual TLS ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "tls reciproco"){:new_window}.


## (Facoltativo) Impostazione dei valori di timeout negli SDK Fabric
{: #best-practices-app-set-timeout-in-sdk}

Gli SDK Fabric impostano i valori di timeout predefiniti nelle applicazioni client per gli eventi nella rete di blockchain. Consulta il seguente esempio sulle impostazioni di timeout predefinite nell'SDK Java Fabric. Il percorso file è `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Valori predefiniti
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

Tuttavia, potresti dover modificare i valori di timeout predefiniti nella tua applicazione. Ad esempio, quando la tua applicazione richiama una transazione che ha bisogno di più di 5000 ms, che è il valore di timeout predefinito per la risposta della connessione hub dell'evento, potresti ricevere un errore perché l'evento di richiamo termina a 5000 ms prima del completamento della transazione. Puoi impostare la proprietà di sistema per sovrascrivere i valori predefiniti dalla tua applicazione client. Poiché i valori predefiniti sono inizializzati prima che imposti la proprietà di sistema, potrebbe non avere effetto. Pertanto, devi impostare la proprietà di sistema per il timeout in un costrutto statico nella tua applicazione client. Consulta il seguente esempio sulla modifica del valore di timeout per la connessione hub dell'evento a 15000 ms nella SDK Fabric Java. Il percorso file è `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Se stai utilizzando l'SDK Node, puoi specificare i valori di timeout direttamente nel metodo richiamato. Come esempio, puoi utilizzare la riga riportata di seguito per aumentare il valore di timeout per [istanziare un chaincode ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") a 5 minuti.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## Prassi ottimali quando si utilizza CouchDB
{: #best-practices-app-couchdb-indices}

Se utilizzi CouchDB come tuo database dello stato, puoi eseguire le query di dati JSON dal tuo chaincode sui dati di stato del canale. Ti consigliamo vivamente di creare indici per le tue query JSON e di utilizzarli nel tuo chaincode. Gli indici consentono alle tue applicazioni di richiamare in modo efficiente i dati quando la tua rete aggiunge blocchi aggiuntivi di transazioni e voci nello stato globale.

Per ulteriori informazioni su CouchDB e su come configurare gli indici, vedi [CouchDB as the State Database ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} nella documentazione di Hyperledger Fabric. Puoi anche trovare un esempio che utilizza un indice con il chaincode nell'[esercitazione di CouchDB di Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html).

Evita di usare il chaincode per le query che produrranno una scansione dell'intero database CouchDB. Delle scansioni del database complete comporteranno dei tempi di risposta lunghi e ridurranno le prestazioni della tua rete. Per evitare e gestire delle query di grandi dimensioni, puoi eseguire alcuni dei seguenti passi:
- Configura gli indici con il tuo chaincode.
- Anche tutti i campi nell'indice devono essere nelle sezioni selettore o ordinamento della tua query per l'indice che deve essere utilizzato.
- Le query più complesse avranno delle prestazioni inferiori e sarà meno probabile che vengano utilizzate in un indice.
- Dovresti provare ad evitare gli operatori che comporteranno una scansione dell'intera tabella o dell'intero indice, come ad esempio `$or`, `$in` e `$regex`.

Puoi trovare degli esempi che illustrano come le query utilizzando gli indici e quale tipo di query avrà le prestazioni migliori nell'[Esercitazione Fabric CouchDB ![Icona link esterno](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes).

I peer su {{site.data.keyword.blockchainfull_notm}} Platform hanno un queryLimit impostato e restituiranno solo 10.000 voci dal database dello stato. Se la tua query raggiunge il queryLimit, puoi utilizzare più query per ottenere i rimanenti risultati. Se hai bisogno di più risultati da una query di intervallo, inizia le query successive con l'ultima chiave restituita dalla query precedente. Se hai bisogno di più risultati dalle query JSON, ordina la tua query utilizzando una delle variabili nei tuoi dati e utilizza quindi l'ultimo valore dalla query precedente in un filtro 'greater than' per la query successiva.

Non eseguire una query dell'intero database per fini di aggregazione o di creazione di report. Se vuoi creare un dashboard o raccogliere grandi quantità di dati come parte della tua applicazione, puoi eseguire una query di un database fuori dalla catena che replica i dati dalla tua rete blockchain. Ciò ti consentirà di comprendere i dati nella blockchain senza ridurre le prestazioni della tua rete o interrompere le transazioni.

Puoi utilizzare il client dei servizi eventi basato sul canale fornito dagli SDK Fabric per creare un archivio dati fuori dalla catena. Ad esempio, puoi utilizzare un listener di blocco per ottenere le transazioni più recenti aggiunte a un libro mastro del canale. Gli insiemi di lettura e scrittura di transazioni dalle transazioni valide possono quindi essere utilizzati per aggiornare una copia dello stato globale che è stato archiviato in un database separato. Per ulteriori informazioni, vedi [How to use the channel-based event service ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} nella documentazione dell'SDK Node.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Sviluppo di applicazioni
{: #dev_app}
Con le applicazioni, puoi richiamare il chaincode per interrogare mediante query o aggiornare un libro mastro specifico per il canale nella tua rete blockchain.
{:shortdesc}

## Configurazione dell'ambiente di sviluppo di applicazioni
Devi installare i prerequisiti software per sviluppare delle applicazioni che possano interagire con la rete blockchain su {{site.data.keyword.cloud}}.
{:shortdesc}

*	Git ([Pagina di download di Git ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://git-scm.com/downloads){:new_window})  
	Git è uno strumento di controllo delle versioni con cui prendere dimestichezza, sia per lo sviluppo di chaincode che per lo sviluppo di software in generale. Inoltre, git bash, che viene installato con git su Windows, è un'eccellente alternativa al prompt dei comandi Windows.

*	GoLang ([Pagina di download di GoLang ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://golang.org/dl){:new_window})  
	L'installazione di GoLang installa una serie di strumenti di CLI Go, che sono molto utili per scrivere chaincode. Ad esempio, il comando `go build` ti consente di controllare che il tuo chaincode venga effettivamente compilato prima di provare a distribuirlo a una rete.

	Attieniti alle [istruzioni d'installazione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://golang.org/doc/install){:new_window} per impostare correttamente le variabili di ambiente. Controlla il tuo `GOPATH` utilizzando il comando indicato di seguito. Nota: il tuo `GOPATH` non deve necessariamente corrispondere all'esempio; la cosa che conta è che tu abbia questa variabile impostata su una directory valida sul tuo file system.

	```
	$ echo $GOPATH
C:\gopath
	```
	{:codeblock}

	Puoi quindi verificare la tua installazione GoLang creando il codice GoLang con l'esempio [hello world ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://golang.org/doc/install#testing){:new_window}.

* Java ([pagina di download di Java ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://java.com/en/download/){:new_window}).
*	Node.js ([pagina di download di Node.js ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://nodejs.org/en/download/){:new_window}).

Lo sviluppo del chaincode è al momento supportato in Node.js, Go e Java.

## Generazione dei certificati lato client
Non scenderemo nei dettagli dell'infrastruttura di chiavi pubbliche e x509; ci sono tante risorse esterne per questo. Basti dire che i flussi di comunicazioni in Fabric utilizzano operazioni di firma/verifica a ogni touchpoint. Di conseguenza, qualsiasi client che invia chiamate (ossia, aggiornamenti o query del libro mastro) alla rete dovrà firmare i payload e collegare un certificato x509 firmato correttamente per scopi di verifica. La chiave privata e il certificato firmato, insieme a un identificativo MSP e al certificato root dell'Autorità di certificazione (CA, Certificate Authority) formano l'oggetto indicato come "contesto utente".

La cosa importante qui è il processo di "registrazione", in cui le chiavi e i certificati che consentono di formare l'oggetto, vengono richiamati dal CA appropriato. Dopo che hai formato l'oggetto di contesto utente, puoi richiamare una API dalla tua applicazione per impostare (set) od ottenere (get) questo contesto utente. A questo punto, l'applicazione (ossia il client) è dotata di tutte le risorse necessarie ed è pronta a comunicare con la rete. Analizzeremo due approcci per richiamare le chiavi e i certificati: la riga di comando ed SDK.

### Riga di comando
L'utilizzo della riga di comando è il più semplice dei due approcci.

1. Attieniti alle istruzioni per creare il [client CA Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Questo passo ti consente di comunicare con un server CA e ricevere dei certificati e delle chiavi con un formato corretto.

2. Scarica i certificati TLS da {{site.data.keyword.cloud_notm}} a seconda del piano di servizi, dell'ubicazione e del cluster che utilizzi.
  - Certificato TLS root per piano Starter  
    - Stati Uniti: [us01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Regno Unito: [uk01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Certificato TL root per piano Enterprise ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Salva il contenuto in una cartella, ad esempio ``$HOME/tls``. Questo passo consente di crittografare il flusso di dati in transito.

3. Apri il file JSON **Profilo connessione** dalla tua schermata **Panoramica** nel monitoraggio della rete e trova le variabili pertinenti:
  * URL per CA: `url` in `certificateAuthorities`
  * ID utente amministratore: ``enrollId``
  * Password amministratore: ``enrollSecret``
  * Nome CA: ``caName``

4. Utilizzando il client CA Fabric, possiamo inviare una chiamata di registrazione (enroll) alla nostra Autorità di certificazione passando il percorso certificati TLS e le quattro stringhe di cui sopra con il seguente comando:
  ```
  $GOPATH/bin/fabric-ca-client enroll -u https://<id_registrazione>:<password_registrazione>@<url_CA_con_porta> --caname <nome_CA> --tls.certfiles <percorso_certificati_tls>
  ```
  {:codeblock}

  Il file binario ``fabric-ca-client`` viene inserito in ``$GOPATH/bin``, quindi dovrai trovarti nell'ubicazione corretta sul tuo computer locale quando esegui questo comando.  Una vera chiamata potrebbe essere simile al seguente comando di esempio:
  ```
  ./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
  ```
  {:codeblock}

5. Trova il tuo certificato amministratore in `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Puoi quindi caricare il certificato amministratore nella tua rete blockchain dal monitoraggio della rete. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificates" della schermata "Member"](v10_dashboard.html#members) nel monitoraggio della rete.

  Puoi anche trovare il certificato root CA e la chiave privata amministratore nelle seguenti directory:
  * Certificato root CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * Chiave privata amministratore: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Ci sono alcuni repository Fabric che contengono risorse e script eccellenti per capire come interagire programmaticamente con un'Autorità di certificazione. Il repository ``fabric-samples`` contiene l'esempio ``balance transfer`` e il repository ``fabric-sdk-node`` ha una serie di test di servizi CA. Se intendi emettere le tue richieste di registrazione sul lato applicazione, devi capire pienamente le API che devono essere presentate nei pacchetti ``fabric-ca-client`` e ``fabric-client``. Usa questi script e repository come una base di riferimento per strutturare la tua applicazione.

Diamo una rapida occhiata ad alcuni frammenti chiave per l'esempio `balance transfer`:

Dobbiamo innanzitutto creare il nostro oggetto client e impostare un'istanza di archivio chiave-valore dove verranno messi i nostri certificati e le nostre chiavi. Possiamo eseguire questa operazione con un semplice metodo factory, ``newCryptoSuite``, che esegue l'estensione alla classe ``Client`` da ``BaseClient``. Ecco una rapida occhiata al codice:

```
# <PERCORSO_CHIAVE_PUBBLICA_PRIVATA> indica il percorso sul tuo computer locale dove desideri archiviare la tua chiave e il tuo certificato
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PERCORSO_CHIAVE_PUBBLICA_PRIVATA>)}));
client.setCryptoSuite(cryptoSuite);
```
{:codeblock}

La prassi comune consisterebbe nell'esportare una variabile di ambiente che definisce il percorso di chiave/valore sul tuo computer e passarla alla funzione sopra indicata. Ora che abbiamo definito il nostro KVS, utilizziamo qualcuno dei metodi dalla classe ``FabricCAServices``. Questa classe è un'implementazione del client CA Fabric e, in quanto tale, ci consentirà di comunicare con il server CA. Dobbiamo prima passare alcune informazioni al nostro client CA, vale a dire l'URL CA:

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```
{:codeblock}

Invieremo quindi effettivamente la chiamata di registrazione (enroll) al server CA. Questo restituirà al client una chiave privata e una chiave pubblica che è stata inclusa in un certificato x509 e firmata dalla CA di destinazione; chiamiamo questo "signcert" o certificato di registrazione. Questo certificato di registrazione, o "eCert", è il pezzo chiave poiché consente alle entità di rete di verificare le transazioni e le chiamate che hanno origine dal client:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```
{:codeblock}

E l'attività finale consiste nell'impostare effettivamente la crypto suite e nel creare il contesto utente:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```
{:codeblock}

Puoi quindi caricare il certificato amministratore nella tua rete blockchain dal monitoraggio della rete. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificati" della schermata "Membro"](v10_dashboard.html#members) nel monitoraggio della rete.

## Sviluppo di applicazioni
{: #developing-applications}

Un modo facile per iniziare a sviluppare le applicazioni è di utilizzare il chaincode e le applicazioni di esempio come un template per creare la tua soluzione di business. Puoi trovare delle applicazioni di esempio per le reti blockchain su {{site.data.keyword.cloud_notm}} in [Applicazioni di esempio](howto/prebuilt_samples.html). Puoi anche iniziare a sviluppare le applicazioni da zero in base ai tuoi bisogni di business.

Puoi sviluppare la tua applicazione in JavaScript o Java e avvalerti delle API disponibili negli SDK Hyperledger Fabric Client per abilitare l'interazione tra la tua applicazione e la tua rete.  Un'applicazione deve includere almeno le seguenti informazioni:
* Nome e versione del chaincode da richiamare.
* Le informazioni sull'endpoint API delle tue risorse di rete, compresi ordinanti, CA e peer.
* Funzioni per interrogare mediante query o aggiornare il libro mastro nella rete.  Se vuoi un'elevata disponibilità, devi prendere in considerazione il failover di nodo nella tua applicazione.

### Utilizzo di SDK Fabric
{: #use-sdks}

Fabric attualmente offre l'SDK Node.js e l'SDK Java e supporterà più linguaggi di programmazione, come Python, REST o GO, nelle future release. Per ulteriori informazioni sugli SDK Fabric e su come utilizzarli, consulta la seguente documentazione:

- [Documentazione dell'SDK Node Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/){:new_window}
- [Documentazione dell'SDK Java Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-sdk-java){:new_window}

### Configurazione dei valori di timeout nelle SDK Fabric
{: #set-timeout-in-sdk}

Le SDK Fabric impostano i valori di timeout predefiniti nelle applicazioni client per gli eventi nella rete di blockchain. Consulta il seguente esempio sulle impostazioni di timeout predefinite nella SDK Fabric Java. Il percorso file è `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

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

Tuttavia, potresti dover modificare i valori di timeout predefiniti nella tua applicazione. Ad esempio, quando la tua applicazione richiama una transazione che ha bisogno di più di 5000 ms, che è il valore di timeout predefinito per la connessione hub dell'evento, per riposta, riceverai un errore perché l'evento di richiamo termina a 5000 ms prima del completamento della transazione. Puoi impostare la proprietà di sistema per sovrascrivere i valori predefiniti dalla tua applicazione client. Poiché i valori predefiniti sono inizializzati prima che imposti la proprietà di sistema, potrebbe non avere effetto. Pertanto, devi impostare la proprietà di sistema per il timeout in un costrutto statico nella tua applicazione client. Consulta il seguente esempio sulla modifica del valore di timeout per la connessione hub dell'evento a 15000 ms nella SDK Fabric Java. Il percorso file è `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}


## Aggiunta di endpoint API di rete alla tua applicazione
Devi aggiungere gli endpoint API delle tue risorse di rete alla tua applicazione in modo che possa interagire con la tua rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}}.  Se non hai una rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}}, puoi crearne una con piano Starter o piano Enterprise. Per ulteriori informazioni, vedi [Governance della rete piano Starter](get_start_starter_plan.html) e [Governance della rete piano Enterprise](get_start.html).

Puoi trovare gli endpoint API nel profilo di connessione della tua rete. Il profilo di connessione è in formato JSON e contiene le informazioni sugli endpoint API e gli ID registrazione(enrollID)/i segreti per le tue risorse di rete, compresi ordinante, CA e nodi peer. La tua applicazione interagirà con i peer e altre risorse di rete tramite questi endpoint API.

1. Richiama le informazioni sull'endpoint API delle tue risorse di rete dal tuo monitoraggio della rete con uno dei seguenti metodi:
	* Per ottenere le informazioni sull'endpoint API specifiche per un chaincode, nella schermata dello specifico canale, su cui è in esecuzione il chaincode, individua il chaincode e fai clic sul pulsante **JSON**.
	![Endpoint API per chaincode](images/channel_chaincode.png "Endpoint API per chaincode")
	* Per ottenere un set completo di informazioni sugli endpoint API relative a tutte le tue risorse di rete, fai clic sul pulsante **Profilo connessione** nella schermata "Panoramica".
	![Profilo di connessione nel monitoraggio della rete](images/service_credentials.png "Profilo di connessione nel monitoraggio della rete")

2. Individua le informazioni sugli endpoint API delle tue risorse di rete, che sono simili al seguente esempio:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```
	{:codeblock}
	**Nota**: se vuoi avere come obiettivo ulteriori peer nella rete, ad esempio ti serve l'approvazione da un peer che non appartiene alla tua organizzazioni, devi ottenere le informazioni sull'endpoint API corrette da tali peer.  Devi anche archiviare il certificato CA per l'altra organizzazione per verificare le risposte che vengono restituite alla tua applicazione. Queste informazioni non vengono presentate nel tuo profilo di connessione; devi, pertanto, contattare l'amministratore appropriato per l'organizzazione Cloud Foundry e acquisire queste informazioni in un'operazione fuori banda. L'URL del servizio ordini è comune nella rete, pertanto non avrai bisogno di alcuna informazione specifica per il membro per il servizio ordini.

3. Inserisci le informazioni sull'endpoint API in un file di configurazione della tua applicazione come mostrato nel seguente esempio:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```
	{:codeblock}

## Utilizzo degli indici CouchDB

Se la tua rete utilizza CouchDB e vuoi approfittare della funzione di indicizzazione di CouchDB (che migliora le prestazioni di CouchDB), gli indici devono essere compressi con il chaincode.

Per ulteriori informazioni su CouchDB e su come configurare gli indici, consulta [Fabric documentation on CouchDB ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html)

## Host di applicazioni
Puoi ospitare la tua applicazione sul tuo file system locale oppure eseguirne il push a {{site.data.keyword.Bluemix_notm}}. Per eseguire il push della tua applicazione a{{site.data.keyword.Bluemix_notm}}, completa la seguente procedura:
1. Installa il [programma di installazione della riga di comando Cloud Foundry ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/cloudfoundry/cli/releases).  Verifica la tua installazione con il comando `cf`.
    * Se la tua installazione viene eseguita correttamente, dovresti vedere un bel po' di output di testo nel tuo terminale.
    * Se vedi "command not found", la tua installazione non è riuscita o CF non è stato aggiunto al tuo percorso di sistema.
2. Configura l'endpoint API ed esegui l'accesso con i tuoi ID e password {{site.data.keyword.Bluemix_notm}} immettendo i seguenti comandi:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. Accedi alla directory della tua applicazione ed esegui il push della tua applicazione immettendo il seguente comando. Questa operazione può richiedere diversi minuti, a seconda della dimensione della tua applicazione. Vedrai i log da {{site.data.keyword.Bluemix_notm}} nel tuo terminale; cesseranno dopo che l'applicazione sarà stata avviata correttamente.
	```
	> cf push IL_NOME_DELLA_TUA_APPLICAZIONE_QUI
	```
	{:codeblock}
	Puoi controllare i log della tua applicazione immettendo uno dei seguenti comandi:
	* `> cf logs IL_NOME_DELLA_TUA_APPLICAZIONE_QUI`
	* `> cf logs IL_NOME_DELLA_TUA_APPLICAZIONE_QUI --recent`


## Disconnessione della tua applicazione dalla rete
{: #disconnect}
Completa la seguente procedura per rimuovere la connessione tra la tua applicazione e la rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}}.
1. Rimuovi le informazioni sull'endpoint API dal file di configurazione della tua applicazione. Per informazioni di riferimento, vedi [Aggiunta di endpoint API di rete alla tua applicazione](#adding-network-api-endpoints-to-your-application).
2. Elimina il tuo contenitore chaincode.
	1. Nella schermata "Canale" del monitoraggio della rete, individua il canale dove è installato il tuo chaincode.
	2. Nella schermata dello specifico canale, individua il chaincode che vuoi disabilitare.
	3. Fai clic sul pulsante **Elimina** e fai clic su **Invia** nel pannello di eliminazione del chaincode. Il tuo contenitore chaincode verrà rimosso.
	![Elimina un chaincode](images/channel_chaincode.png "Elimina un chaincode")

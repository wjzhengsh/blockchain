---

copyright:
  years: 2017
lastupdated: "2017-11-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Sviluppo di applicazioni

Con le applicazioni, puoi richiamare il chaincode per interrogare mediante query o aggiornare un libro mastro specifico per il canale nella tua rete {{site.data.keyword.blockchain}}. 
{:shortdesc}

## Configurazione dell'ambiente di sviluppo di applicazioni
Devi installare i prerequisiti software per sviluppare delle applicazioni che possano interagire con la rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix}}.
{:shortdesc}

*	Git ([Pagina di download di Git ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://git-scm.com/downloads){:new_window})
	
	Git è uno strumento di controllo delle versioni con cui prendere dimestichezza, sia per lo sviluppo di chaincode che per lo sviluppo di software in generale.  Inoltre, git bash, che viene installato con git su Windows, è un'eccellente alternativa al prompt dei comandi Windows.
 
	Utilizza il seguente comando per verificare la tua installazione di Git.  Dovresti vedere un output simile al seguente:  
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([Pagina di download di GoLang ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://golang.org/dl){:new_window})

	L'installazione di GoLang installa una serie di strumenti di CLI Go, che sono molto utili per scrivere chaincode.  Ad esempio, il comando `go build` ti consente di controllare che il tuo chaincode venga effettivamente compilato prima di provare a distribuirlo a una rete.  Alla data della presente redazione, il build di questo chaincode risulta corretto con la versione `1.8.3`.

	Utilizza il seguente comando per verificare la tua versione di GoLang.  Dovresti vedere un output simile al seguente:
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}
	
	Attieniti alle [istruzioni d'installazione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://golang.org/doc/install){:new_window} per impostare correttamente le variabili di ambiente.  Controlla il tuo `GOPATH` utilizzando il comando indicato di seguito.  Nota: il tuo `GOPATH` non deve necessariamente corrispondere all'esempio; la cosa che conta è che tu abbia questa variabile impostata su una directory valida sul tuo filesystem.   
	```
	$ echo $GOPATH
C:\gopath
	```
	{:screen}

	Puoi quindi verificare la tua installazione GoLang creando il codice GoLang con l'esempio [hello world ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://golang.org/doc/install#testing){:new_window}.

*	Node.js ([pagina di download di Node.js ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://nodejs.org/en/download/){:new_window}).  Scegli una versione compresa tra 6.9.5 e 7.  Le versioni di nodo superiori alla 7 causeranno degli errori quando si scaricano i moduli SDK.  

	Utilizza i seguenti comandi per verificare la tua installazione di Node.js.  Dovresti vedere un output simile al seguente:  
	```
	$ node -v
	v6.10.1
	
	$ npm -v
	3.10.10
	```
	{:screen}

## Generazione dei certificati lato client
Non scenderemo nei dettagli dell'infrastruttura di chiavi pubbliche e x509, ci sono tante risorse esterne per questo.  Basti dire che i flussi di comunicazioni in Fabric implementano operazioni di firma/verifica per ogni touchpoint.  In quanto tali, qualsiasi client che invia delle chiamate (ossia delle transazioni) alla rete dovrà firmare i payload (chiave privata) e collegare un certificato x509 firmato correttamente per scopi di verifica (certificato firmato).  La chiave privata e il certificato firmato, insieme a un identificativo MSP e al certificato root dell'Autorità di certificazione (CA, Certificate Authority) formano l'oggetto indicato come "contesto utente".  Ancora una volta, non sono necessari dettagli estranei.  Comunicheremo semplicemente con l'Autorità di certificazione appropriata e richiameremo le chiavi e i certificati che consentono di formare l'oggetto; questo processo viene indicato come registrazione.  Dopo che hai formato l'oggetto di contesto utente, impostare (set) od ottenere (get) questo contesto utente è facile quanto richiamare un'API dalla tua applicazione.  A questo punto, l'applicazione (ossia il client) è dotata di tutte le risorse necessarie ed è pronta a comunicare con la rete.  Analizzeremo due approcci per richiamare le chiavi e i certificati.

### Riga di comando
Questo è il più semplice dei due approcci.  Per prima cosa, attieniti alle istruzioni per creare il [client CA Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html).  Questo passo ti consente di comunicare con un server CA e ricevere in risposta dei certificati e delle chiavi con un formato corretto.  

Scarica quindi i certificati TLS dal [tuo {{site.data.keyword.Bluemix_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) e salva il contenuto in una cartella, ad esempio ``$HOME/tls``.  Questo passo consente di crittografare il flusso di dati in transito.

Per finire, apri il file JSON **Service Credentials** dalla tua schermata **Overview** nel Network Monitor e verifica le seguenti informazioni:
* URL per CA: `url` in `certificateAuthorities`
* ID utente amministratore: ``enrollId``
* Password amministratore: ``enrollSecret``
* Nome CA: ``caName``

Utilizzando il client CA Fabric, possiamo inviare una chiamata di registrazione (enroll) alla nostra Autorità di certificazione passando il percorso certificati TLS e le quattro stringhe di cui sopra con il seguente comando:    
```
$GOPATH/bin/fabric-ca-client enroll -u https://<id_registrazione>:<password_registrazione>@<url_CA_con_porta> --caname <nome_CA> --tls.certfiles <percorso_certificati_tls>
```

Il file binario ``fabric-ca-client`` viene inserito in ``$GOPATH/bin``, quindi dovrai trovarti nell'ubicazione corretta sul tuo computer locale quando esegui questo comando.  Una vera chiamata potrebbe essere simile al seguente comando di esempio:  
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```
  
Trova il tuo certificato amministratore in `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Puoi quindi caricare il certificato amministratore nella tua rete blockchain dal Network Monitor. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificates" della schermata "Members"](v10_dashboard.html#members) nel Network Monitor.

Puoi anche trovare il certificato root CA e la chiave privata amministratore nelle seguenti directory:
* Certificato root CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* Chiave privata amministratore: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Ci sono due repository Hyperledger che contengono risorse e script eccellenti per capire come interagire programmaticamente con un'Autorità di certificazione.  Il repository ``fabric-samples`` contiene l'esempio "balance transfer" e il repository ``fabric-sdk-node`` ha una serie di test di servizi CA.  Se intendi emettere le tue richieste di registrazione sul lato applicazione, dovrai capire pienamente le API che devono essere esposte nei pacchetti ``fabric-ca-client`` e ``fabric-client``.  Usa questi script e repository come una base di riferimento per strutturare la tua applicazione.

Diamo una rapida occhiata ad alcuni frammenti chiave per l'esempio "balance transfer":

Dobbiamo innanzitutto creare il nostro oggetto client e impostare un'istanza di archivio chiave/valore dove verranno messi i nostri certificati e le nostre chiavi.  Possiamo eseguire questa operazione con un semplice metodo factory, ``newCryptoSuite``, che esegue l'estensione alla classe ``Client`` da ``BaseClient``.  Ecco una rapida occhiata al codice:

```
# <PERCORSO_CHIAVE_PUBBLICA_PRIVATA> indica il percorso sul tuo computer locale dove desideri archiviare la tua chiave e il tuo certificato
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PERCORSO_CHIAVE_PUBBLICA_PRIVATA>)}));
client.setCryptoSuite(cryptoSuite);
```

La prassi comune consisterebbe nell'esportare una variabile di ambiente definendo il percorso di chiave/valore sul tuo computer e passarla alle funzioni sopra indicate.  Ora che abbiamo definito il nostro KVS, utilizziamo qualcuno dei metodi dalla classe ``FabricCAServices``. Questa classe è un'implementazione del client CA Fabric e, in quanto tale, ci consentirà di comunicare con il server CA.  Dobbiamo prima passare alcune informazioni al nostro client CA, vale a dire l'URL CA:

```
# il caURL può essere definito manualmente oppure impostando una variabile di ambiente
# la variabile copService è definita all'inizio del programma
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

Invieremo quindi effettivamente la chiamata di registrazione (enroll) al server CA.  Questo restituirà al client una chiave privata e una chiave pubblica che è stata inclusa in un certificato x509 e firmata dalla CA di destinazione; chiamiamo questo il "signcert" o il certificato di registrazione.  Questo certificato di registrazione, o "eCert", è il pezzo chiave poiché consente alle entità di rete di verificare le transazioni e le chiamate che hanno origine dal client:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

E l'attività finale consiste nell'impostare effettivamente la crypto suite e nel creare il contesto utente:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

Puoi quindi caricare il certificato amministratore nella tua rete blockchain dal Network Monitor. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificates" della schermata "Members"](v10_dashboard.html#members) nel Network Monitor.

## Sviluppo di applicazioni
Puoi sviluppare la tua applicazione in JavaScript o Java e avvalerti delle API disponibili negli SDK Hyperledger Fabric Client per abilitare l'interazione tra la tua applicazione e la tua rete.  Un'applicazione deve includere almeno le seguenti informazioni:
* Nome e versione del chaincode da richiamare.
* Le informazioni sull'endpoint API delle tue risorse di rete, compresi ordinanti, CA e peer.
* Funzioni per interrogare mediante query o aggiornare il libro mastro nella rete.  Se vuoi un'elevata disponibilità, devi prendere in considerazione il failover di nodo nella tua applicazione.

Puoi trovare delle applicazioni di esempio per il **piano Enterprise** su {{site.data.keyword.Bluemix_short}} in [Applicazioni di esempio](howto/sample_applications.html).  Utilizza questi chaincode e queste applicazioni di esempio come un template per creare la tua soluzione di business.

## Aggiunta delle credenziali del servizio di rete alla tua applicazione
Devi aggiungere le credenziali del servizio dei tuoi componenti di rete alla tua applicazione in modo che possa interagire con la tua rete {{site.data.keyword.blockchain}} su{{site.data.keyword.Bluemix_short}}.  Se non hai una rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}}, vedi [Introduzione a IBM Blockchain Platform](get_start.html). 

Le credenziali del servizio sono in formato JSON e contengono le informazioni sull'endpoint API e gli ID registrazione(enrollID)/segreti per i tuoi componenti di rete, compresi ordinante, CA e nodi peer.  La tua applicazione interagirà con i peer e altri componenti di rete tramite questi endpoint API.  

1. Richiama le informazioni sull'endpoint API dei tuoi componenti di rete dal tuo Network Monitor con uno dei seguenti metodi:
	* Per ottenere le informazioni sull'endpoint API specifiche per un chaincode, nella schermata dello specifico canale, individua il chaincode e fai clic sul pulsante **JSON**.
	![Credenziali del servizio per chaincode](images/channel_chaincode_detail.png "Credenziali del servizio per chaincode")
	* Per ottenere un set completo di informazioni sull'endpoint API relative a tutti i tuoi componenti di rete, fai clic sul pulsante **Service Credentials** nella schermata "Resources".
	![Credenziali del servizio nel Network Monitor](images/service_credentials.png "Credenziali del servizio nel Network Monitor")
	 
2. Individua le credenziali e le informazioni sull'endpoint API delle tue risorse di rete.  Vedi il seguente esempio:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**Nota**: se vuoi avere come obiettivo ulteriori peer nella rete, ad esempio ti serve l'approvazione da un peer che non appartiene alla tua organizzazioni, devi ottenere le informazioni sull'endpoint API corrette da tali peer.  Devi anche archiviare il certificato CA per l'altra organizzazione per verificare le risposte che vengono restituite alla tua applicazione. Queste informazioni non vengono esposte nelle tue credenziali del servizio e dovrai pertanto contattare l'amministratore dell'organizzazione CLoud Foundry appropriato e acquisire questi dati in un'operazione fuori banda. L'URL del servizio ordini è comune nella rete, pertanto non avrai bisogno di alcuna informazione specifica per il membro per il servizio ordini.  

3. Inserisci le informazioni sull'endpoint API in un file di configurazione della tua applicazione come mostrato nel seguente esempio:  
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## Applicazioni host
Puoi ospitare la tua applicazione sul tuo file system locale oppure eseguirne il push a {{site.data.keyword.Bluemix_notm}}.  Per eseguire il push della tua applicazione a{{site.data.keyword.Bluemix_notm}}, completa la seguente procedura:
1. Installa il [programma di installazione della riga di comando Cloud Foundry ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/cloudfoundry/cli/releases).  Verifica la tua installazione con il comando `cf`.	
    * Se la tua installazione viene eseguita correttamente, dovresti vedere un bel po' di output di testo nel tuo terminale. 
    * Se vedi "command not found", la tua installazione non è riuscita o CF non è stato aggiunto al tuo percorso di sistema.	
2. Configura l'endpoint API ed esegui l'accesso con i tuoi ID e password {{site.data.keyword.Bluemix_notm}} immettendo i seguenti comandi:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```  
3. Accedi alla directory della tua applicazione ed esegui il push della tua applicazione immettendo il seguente comando.  Questa operazione può richiedere diversi minuti, a seconda della dimensione della tua applicazione.  Vreai i log da {{site.data.keyword.Bluemix_notm}} nel tuo terminale; cesseranno dopo che l'applicazione sarà stata avviata correttamente.  
	```
	> cf push IL_NOME_DELLA_TUA_APPLICAZIONE_QUI
	```  
	Puoi controllare i log della tua applicazione immettendo uno dei seguenti comandi:
	* `> cf logs IL_NOME_DELLA_TUA_APPLICAZIONE_QUI`
	* `> cf logs IL_NOME_DELLA_TUA_APPLICAZIONE_QUI --recent`


## Disconnessione della tua applicazione dalla rete
Completa la seguente procedura per rimuovere la connessione tra la tua applicazione e la rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}}.
1. Rimuovi le informazioni sull'endpoint API dal file di configurazione della tua applicazione. Per informazioni di riferimento, vedi [Aggiunta delle credenziali del servizio di rete alla tua applicazione](#adding-network-service-credentials-to-your-application).
2. Elimina il tuo contenitore chaincode.
	1. Nella schermata "Channel" del Network Monitor, individua il canale dove è installato il tuo chaincode.
	2. Nella schermata dello specifico canale, individua il chaincode che vuoi disabilitare.
	3. Fai clic sul pulsante **Delete** e fai clic su **Submit** nel pannello di eliminazione del chaincode. Il tuo contenitore chaincode sarà rimosso.  
	![Elimina un chaincode](images/channel_chaincode_detail.png "Elimina un chaincode")

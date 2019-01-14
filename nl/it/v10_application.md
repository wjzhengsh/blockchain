---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Sviluppo di applicazioni con gli SDK Fabric
{: #dev_app}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform fornisce le API che puoi utilizzare per connettere applicazioni alla tua rete blockchain. Puoi utilizzare gli endpoint API di rete nel profilo di connessione per richiamare il tuo chaincode e aggiornare o interrogare tramite query il libro mastro specifico del canale sui tuoi peer. Puoi anche utilizzare le API nella [IU Swagger](howto/swagger_apis.html) per gestire i nodi, i canali e i membri della tua rete.
{:shortdesc}

Puoi utilizzare questa esercitazione per apprendere in che modo accedere alle API di {{site.data.keyword.blockchainfull_notm}} Platform e utilizzarle per iscrivere e registrare l'applicazione con la rete. Imparerai anche come interagire con la tua rete ed emettere transazioni dalla tua applicazione. L'esercitazione è basata sull'esercitazione [Writing Your First Application ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "writing your first application"){:new_window} nella documentazione di Hyperledger Fabric. Utilizzerai molti degli stessi file e comandi dell'esercitazione **Writing Your First Application**, ma li userai per interagire con una rete in {{site.data.keyword.blockchainfull_notm}} Platform. Questa esercitazione descrive ogni fase dello sviluppo di applicazioni utilizzando l'SDK Node di Fabric Hyperledger. Imparerai inoltre come iscrivere e registrare gli utenti utilizzando il client CA Fabric come alternativa all'utilizzo dell'SDK.

Oltre a questa esercitazione, puoi utilizzare le applicazioni e il chaincode di esempio che {{site.data.keyword.blockchainfull_notm}} Platform fornisce come template quando crei le tue soluzioni di business. Per ulteriori informazioni, vedi [Distribuzione di applicazioni di esempio](howto/prebuilt_samples.html).

## Prerequisiti
Prima di utilizzare l'esercitazione **Writing Your First Application** su {{site.data.keyword.blockchainfull_notm}} Platform, devi avere i seguenti prerequisiti.

- Se non hai una rete blockchain su {{site.data.keyword.cloud_notm}}, devi crearne una con un piano di adesione Starter o Enterprise. Per ulteriori informazioni, vedi [Creazione di una rete piano Starter](get_start_starter_plan.html#creating-a-network) o [Creazione di una rete piano Enterprise](get_start.html#creating-a-network).

  Dopo aver eseguito l'accesso al Monitoraggio della rete della tua rete, aggiungi almeno un peer per la tua organizzazione nella schermata "Panoramica". Quindi, crea almeno un canale nella tua rete. Per ulteriori informazioni, vedi [Creazione di un canale](howto/create_channel.html#creating-a-channel). **Nota** che se utilizzi una rete piano Starter, la tua rete dispone già di un canale con il nome di `defaultchannel` che puoi usare per distribuire il chaincode.

- Installa gli strumenti richiesti per scaricare gli esempi di Fabric Hyperledger e per utilizzare l'SDK Node.
  * [Curl ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") o [Git ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

- Installa gli esempi di Hyperledger Fabric scaricando la directory `fabric-samples`. Puoi attenerti a quanto indicato nella [guida Getting Started ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/install.html "guida Getting Started"){:new_window} nella documentazione di Hyperledger Fabric.

- Vai alla directory `fabric-samples` sulla tua macchina locale.
  * Usa il comando `git checkout` per usare il ramo che corrisponde alla versione di Hyperledger Fabric delle tue reti. Puoi trovare la tua versione Fabric aprendo la [finestra Preferenze della rete](../v10_dashboard.html#network-preferences) nel tuo Monitoraggio della rete.
    - Se la tua rete si trova su Fabric versione 1.2, puoi utilizzare il ramo principale.
    - Se la tua rete si trova su Fabric versione 1.1, esegui `git checkout v1.1.0`.
    - Se la tua rete si trova su Fabric versione 1.0, esegui `git checkout v1.0.6`.

  * Passa a `fabric-samples/fabcar`. Puoi creare una copia di questa directory e rinominarla in modo che tu possa provare e testare l'applicazione di esempio in una directory pulita.

  * Nella directory `fabcar`, esegui il comando `npm install` per installare i pacchetti necessari per l'utilizzo dell'SDK Fabric, tra cui `fabric-client` e `fabric-ca-client`.

- Installa e istanzia il chaincode fabcar sul tuo canale utilizzando il [Monitoraggio della rete](howto/install_instantiate_chaincode.html#installchaincode). Puoi trovare il chaincode fabcar nella cartella `fabric-samples` sotto `fabric-samples > chaincode > fabcar > go`.

- Richiama il profilo di connessione della tua rete nella schermata "Panoramica" del Monitoraggio della rete. Salva il profilo di connessione nella directory `fabcar` e rinominalo in `creds.json`.

## Utilizzo degli SDK Fabric
{: #using-the-fabric-sdks}

Gli SDK Hyperledger Fabric forniscono una potente serie di API che consentono alle applicazioni di interagire con le reti blockchain. Puoi trovare l'elenco più aggiornato dei linguaggi supportati nella [Documentazione della community Hyperledger Fabric SDK ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Documentazione della community Hyperledger Fabric SDK"){:new_window}. Si consiglia di utilizzare l'SDK Node o l'SDK Java con {{site.data.keyword.blockchainfull_notm}} Platform. Puoi ottenere ulteriori informazioni sulle API fornite dagli SDK nei singoli repository dell'SDK.

Questa esercitazione utilizza l'[SDK Node ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node"){:new_window} per registrare e iscrivere la tua applicazione e quindi utilizza l'applicazione per emettere le transazioni richiamando e interrogando il chaincode. Questa esercitazione descrive le informazioni che devi fornire all'SDK in modo che la tua applicazione possa connettersi alla rete blockchain. Introduce inoltre alcune delle API che puoi utilizzare e il modo in cui l'SDK interagisce con le transazioni e le invia alla tua rete blockchain.

## Aggiunta di endpoint API di rete alla tua applicazione
{: #api-endpoints}

Devi fornire alla tua applicazione gli endpoint API di specifiche risorse di rete, tra cui ordinante, CA e nodi peer, nella rete blockchain su {{site.data.keyword.cloud_notm}}. La tua applicazione può interagire con la rete tramite questi endpoint API. Puoi trovare gli endpoint API nel Profilo di connessione della tua rete. Il profilo di connessione è in formato JSON e contiene le informazioni sull'endpoint API e gli ID di registrazione (enrollID) e i segreti per le tue risorse di rete.

1. Richiama le informazioni sull'endpoint API delle tue risorse di rete dal tuo Monitoraggio della rete con uno dei seguenti metodi:
  * Nella schermata "Panoramica", fai clic su **Profilo connessione**. Il profilo di connessione contiene un insieme completo di informazioni sull'endpoint API di tutte le tue risorse di rete.
    ![Profilo di connessione nel monitoraggio della rete](images/service_credentials.png "Profilo di connessione nel monitoraggio della rete")

  * Se hai il chaincode in esecuzione nella rete, puoi ottenere le informazioni sull'endpoint API specifiche per il chaincode. Nella schermata "Canali", fai clic sulla riga del canale su cui è in esecuzione il chaincode per aprire la schermata del canale specifico. Quindi, individua il chaincode e fai clic sul pulsante **JSON**.
    ![Endpoint API per chaincode](images/channel_chaincode.png "Endpoint API per chaincode")

2. Individua le informazioni sull'endpoint API delle tue risorse di rete, che sono simili all'URL della riga `peer1-org1` nel seguente esempio:
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```

  **Nota**: ti potresti voler occupare delle risorse di rete al di fuori della tua organizzazione con la tua applicazione. Ad esempio, se una [politica di approvazione](howto/install_instantiate_chaincode.html#endorsement-policy) del chaincode richiede approvazioni da altre organizzazioni sul canale, dovrai ottenere le informazioni sull'endpoint dei loro peer e dei certificati TLS associati. Puoi trovare queste informazioni nella sezione peer del profilo di connessione. Tuttavia, devi contattare l'amministratore delle altre organizzazioni in merito ai peer che hanno aggiunto a determinati canali.

3. Inserisci le informazioni sull'endpoint API in un file di configurazione della tua applicazione come mostrato nel seguente esempio:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```

  Puoi anche inviare [richieste HEAD](howto/monitor_network.html#monitor-nodes) a questi endpoint per verificare la disponibilità delle tue risorse di rete.

  Se utilizzi gli SDK Fabric, puoi anche connetterti alla rete utilizzando il profilo di connessione. Questa esercitazione fornisce manualmente le informazioni sull'endpoint della tua rete all'SDK. Tuttavia, puoi trovare un'esercitazione e una guida sull'[utilizzo del tuo profilo di connessione con l'SDK](#using-your-connection-profile-with-the-sdk) in una sezione successiva.

## Iscrizione della tua applicazione
{: #enroll-app}

Prima di connettere un'applicazione alla tua rete su {{site.data.keyword.blockchainfull_notm}} Platform, devi provare l'autenticità della tua applicazione alla rete. Non approfondiremo i dettagli dei certificati x509 e dell'infrastruttura di chiavi pubbliche, anche se puoi scoprire di più visitando l'esercitazione [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](certificates.html). In poche parole, i flussi di comunicazione in Fabric utilizzano le operazioni di firma/verifica su ogni touchpoint. Pertanto, qualsiasi applicazione che invia chiamate, come query o aggiornamenti del libro mastro, alla rete deve firmare i payload con la chiave privata e allegare un certificato x509 correttamente firmato a scopo di verifica. L'**iscrizione** è il processo di generazione delle chiavi e dei certificati necessari dall'Autorità di certificazione appropriata. Dopo l'iscrizione, la tua applicazione è pronta per comunicare con la rete.

Questa sezione spiega come richiamare le chiavi e i certificati con l'SDK Node di Fabric utilizzando il codice di esempio che fa parte dell'esercitazione **Writing Your First Application**. Puoi generare i certificati solo utilizzando un'identità che è stata registrata presso la tua CA (Certificate Authority, Autorità di certificazione). L'esercitazione di seguito esegue prima l'iscrizione utilizzando un'identità di amministratore che è già stata registrata presso la tua CA. Utilizza quindi questi certificati per registrare una nuova identità client. L'esercitazione esegue nuovamente l'iscrizione utilizzando la nuova identità e utilizza questi certificati per inoltrare le transazioni alla rete.<!---You can find an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

Puoi anche utilizzare la schermata "CA (Certificate Authority)" del Monitoraggio della rete per generare i certificati e utilizzare tali certificati per interagire con la rete. Per scoprire come, visita [Generazione di certificati utilizzando il Monitoraggio della rete](#enroll-panel). Puoi anche imparare come utilizzare il [client CA Fabric](certificates.html#enroll-register-caclient) dalla riga di comando per generare i certificati e registrare gli utenti nell'esercitazione [Gestione certificati](certificates.html).

### Iscrizione mediante l'SDK Fabric
{: #enroll-app-sdk}

Dalla directory `fabcar` nella tua cartella `fabric-samples`, apri il file `enrollAdmin.js` in un editor di testo.

1. Il file crea innanzitutto un'istanza del client Fabric.
  ```
  var fabric_client = new Fabric_Client();
  ```
  {:codeblock}

2. Il file crea quindi un archivio chiave-valore (KVS) per gestire i tuoi certificati. L'SDK utilizza la classe [KeyValueStore ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} per creare l'archivio chiave-valore e la classe [CryptoSuite ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} per eseguire i calcoli di crittografia. Puoi vedere il relativo blocco di codice qui sotto.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. Dopo la definizione del KVS, puoi utilizzare alcuni metodi dalla classe del [client Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html "Client Fabric"){:new_window} e l'API del client CA Fabric <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> per comunicare con il server CA. Devi fornire all'SDK il nome e l'URL della tua Certificate Authority (Autorità di certificazione). Apri il file JSON del **Profilo connessione** dalla schermata **Panoramica** nel Monitoraggio della rete e trova le seguenti variabili sotto la sezione `certificateAuthorites`:
  * URL per CA: `url` in `certificateAuthorities`
  * ID utente amministratore: ``enrollId``
  * Password amministratore: ``enrollSecret``
  * Nome CA: `caName`

  **Modifica** le righe pertinenti nel file `enrollAdmin.js` con queste informazioni nel seguente modo:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  Ad esempio:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```

  Il client Fabric verifica quindi se la tua applicazione è già iscritta. **Modifica** la riga qui sotto con l'`enrollID` ottenuto dal tuo profilo di connessione:
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. Devi inviare la chiamata di iscrizione (enroll) al server CA. Il tuo `admin` è già registrato con la rete. La chiamata di iscrizione richiama una chiave privata e una chiave pubblica racchiuse in un certificato x509 e firmate dalla CA interessata. Questo certificato racchiuso e firmato è chiamato signCert. Il signCert consente ai membri della rete di verificare le chiamate che provengono dal client. Devi fornire il nome dell'organizzazione, la password e l'ID MSP dal tuo file di credenziali. Nella sezione `certificateAuthorities` delle tue credenziali di rete, trova i tuoi `enrollID`, `enrollSecret` e `x-mspid`. **Modifica** il blocco di codice qui sotto con questi valori e sostituisci la sezione pertinente del tuo file.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. Salva il file `enrollAdmin.js`.

Nella directory `fabcar`, iscrivi l'amministratore immettendo il seguente comando:
```
node enrollAdmin.js
```
{:codeblock}

Il comando di iscrizione genera il signCert e lo esporta in una cartella denominata `hfc-key-store`. I file futuri in questa esercitazione cercheranno i tuoi certificati in questa cartella. Se riesci trovare i certificati di gestione nella cartella `hfc-key-store`, il comando di iscrizione funziona.

Se vuoi [gestire la tua rete utilizzando l'SDK](#operate-sdk), devi caricare il signCert amministratore in {{site.data.keyword.blockchainfull_notm}} Platform. Puoi trovare il signCert amministratore nella cartella `hfc-key-store`. Apri il file `admin` e copia il certificato all'interno delle virgolette dopo il campo `certificate`. Utilizza uno strumento o un editor di testo per convertire il certificato in formato PEM. Puoi quindi caricare il certificato di gestione nella tua rete blockchain dal Monitoraggio della rete. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificati" della schermata "Membro"](v10_dashboard.html#members) nel Monitoraggio della rete. Questa operazione non è necessaria se stai utilizzando l'SDK solo per richiamare o interrogare il chaincode.

## Registrazione della tua applicazione
{: #register-app}

Dopo aver generato i certificati lato client. devi registrare la tua applicazione con l'Autorità di certificazione della rete. La registrazione aggiunge la tua applicazione all'elenco dei componenti che la rete può riconoscere. Una procedura ottimale è registrare la tua applicazione come identità separata piuttosto che utilizzare `admin` per firmare le richieste.

### Registrazione utilizzando l'SDK
{: #register-app-sdk}

Puoi utilizzare il file `registerUser.js` per registrare e iscrivere l'applicazione come `user1` utilizzando il tuo signCert `admin`. Apri `registerUser.js` in un editor di testo.

1. Fornisci l'URL e il nome CA a una nuova istanza del client CA Fabric.

2. Fornisci l'enrollID al metodo `getUserContext` dalla [classe client Fabric![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html "classe client Fabric"){:new_window} per controllare se il tuo `admin` è iscritto e autorizzato a emettere questa richiesta. **Modifica** il blocco di codice pertinente nel tuo file in base all'esempio qui sotto:
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. Utilizza il **client CA Fabric** per registrare e iscrivere l'utente presso la CA, quindi utilizza il **client Fabric** per creare il nuovo signCert. **Modifica** il seguente blocco con il tuo ID MSP e la tua affiliazione dell'organizzazione. Puoi trovare le tue `x-affiliations` nella sezione delle autorità di certificazione delle tue credenziali di rete e utilizzare qualsiasi affiliazione elencata. Aggiungi il nome dell'utente che vuoi creare. Per impostazione predefinita, l'esempio fabcar utilizza `user1`.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. Salva il file `registerUser.js`.

Esegui il comando `node registerUser.js` per registrare e iscrivere `user1`. Se riesci a trovare i certificati `user1` nella cartella `hfc-key-store`, il comando funziona. Puoi registrare un'entità solo una volta. Se riscontri un problema, prova ad eseguire `registerUser.js` con un nuovo nome utente.

### Registrazione utilizzando il Monitoraggio della rete

In alternativa, puoi registrare e iscrivere la tua applicazione client utilizzando la scheda **CA (Certificate Authority)** del Monitoraggio della rete. Per ulteriori istruzioni, fai riferimento a queste [informazioni](v10_dashboard.html#ca).

## Emissione di transazioni richiamando e interrogando il chaincode
{: #invoke-query}

La tua applicazione deve interagire con l'intera rete blockchain per inviare una transazione.

1. L'applicazione invia una proposta di transazione che deve essere approvata dai peer sul canale.
2. I peer di approvazione restituiscono la transazione approvata all'applicazione.
3. L'applicazione invia la transazione approvata al servizio ordini per aggiungere la transazione al libro mastro.

Per ulteriori informazioni sul flusso completo della transazione, vedi [Flusso di transazioni ![Icona link esterno](images/external_link.svg "Icona link esterno")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flusso di transazioni"){:new_window} nella documentazione di Hyperledger Fabric. Dopo aver iniziato con questa esercitazione, consulta la sezione [Connettività e disponibilità delle applicazioni](#app-connectivity-availability) per suggerimenti sulla gestione di come il tuo SDK interagisce con la rete.

I seguenti esempi dimostrano come l'SDK Node imposta la topologia di rete, definisce la proposta di transazione e quindi invia la transazione alla rete. Puoi utilizzare il file `invoke.js` per richiamare le funzioni all'interno del chaincode `fabcar`. Queste funzioni ti consentono di creare e trasferire gli asset sul libro mastro blockchain. Questa esercitazione utilizza la funzione `initLedger` per aggiungere nuovi dati al tuo canale e quindi utilizza il file `query.js` per eseguire la query dei dati.

### Richiamo del chaincode
{: #invoke}

Apri il file `invoke.js` in un editor di testo.

1. Aggiungi `var creds = require('./creds.json')` nella parte superiore del file. Questa riga di codice consente al file `invoke.js` di leggere le informazioni dal file di credenziali `creds.json`.

2. Utilizza la classe [Client Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html "Client Fabric"){:new_window} per impostare la rete fabric utilizzando gli endpoint API delle tue risorse di rete. Questo passo definisce il canale e i peer a cui il tuo client invierà la proposta e il servizio ordini a cui l'SDK invierà quindi la transazione approvata. **Modifica** il blocco di codice pertinente riportato di seguito. La riga `creds.peers["org1-peer1"].url` importa l'URL peer dal tuo file di credenziali.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  Le nuove variabili peer e orderer (ordinante) aprono connessioni GRPC alla tua rete blockchain. Per ulteriori informazioni sulla gestione di queste connessioni, consulta [Apertura e chiusura di connessioni di rete](#connections).

  Quando aggiungi l'URL peer al metodo `fabric_client.newPeer`, importi anche i certificati TLS pertinenti dal tuo profilo di connessione utilizzando il frammento di codice riportato di seguito. Hai fatto lo stesso quando hai aggiunto l'URL del servizio ordini. Devi utilizzare questi certificati TLS per autenticare la comunicazione con la tua rete.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  Se la politica di approvazione richiede che le transazioni siano approvate da altre organizzazioni nel canale, devi aggiungere tali peer di organizzazioni utilizzando i metodi `newPeer()` e `channel.addPeer()` quando configuri la rete. Le organizzazioni devono inviarti l'elenco dei peer che hanno unito a un particolare canale. Le informazioni sull'endpoint e i certificati TLS saranno disponibili nel profilo di connessione. L'SDK invierà la transazione a tutti i peer aggiunti al canale.

  Puoi anche aggiungere ulteriori peer appartenenti alla tua organizzazione che sono uniti al canale come passo per [rendere la tua applicazione altamente disponibile](#ha-app). Ciò fornirà all'SDK un failover nel caso in cui uno dei tuoi peer venga interrotto.

3. Dopo aver impostato la rete fabric e importato l'identità dell'applicazione e il signCert dal passo di registrazione, il file `invoke.js` definisce la proposta che invierai alla rete. Puoi utilizzare la funzione `initLedger` nel chaincode `fabcar` per aggiungere alcuni dati iniziali al libro mastro. Puoi anche modificare il blocco di codice per richiamare altre funzioni che puoi trovare nel chaincode `fabcar`.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  Una volta aver definito la richiesta, puoi inviare una [proposta di transazione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") ai peer sul canale. Dopo che la proposta viene restituita dai peer, puoi [inviare la transazione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") al servizio ordini.

4. Puoi aggiungere un servizio eventi per rendere più efficiente il flusso delle transazioni. **Modifica ** la seguente sezione:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Sebbene l'esempio utilizzi un servizio eventi basato sul peer, devi utilizzare un listener basato sul canale. Puoi saperne di più nella sezione relativa alla [gestione delle transazioni](#managing-transactions) e nella [documentazione di SDK Node ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "servizio eventi basato sul canale"){:new_window}.

5. Per impostazione predefinita, `invoke.js` invia la transazione come `user1`. Puoi modificare il file `invoke.js` se hai registrato un nome diverso.

Dopo aver completato la modifica del file, immetti il comando `node invoke.js` per inviare la transazione alla rete. Dovresti visualizzare quanto riportato di seguito come conferma che il richiamo è stato eseguito correttamente.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

Ciò indica che l'applicazione ha richiamato correttamente il tuo chaincode e che ha aggiunto i dati al libro mastro.

### Esecuzione della query del chaincode
{: #query}

Adesso puoi utilizzare `query.js` per leggere il libro mastro. Apri il file `query.js` in un editor di testo.

1. Aggiungi `var creds = require('./creds.json')` nella parte superiore del file.
2. Aggiorna il file con il nome del canale e le informazioni sull'endpoint del peer. Poiché questa operazione legge solo i dati memorizzati sul peer, non dovrai aggiungere le informazioni sull'endpoint del servizio ordini. `query.js` presume anche che invii la proposta come `user1`.
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

Dopo aver completato la modifica del file, immetti il comando `node query.js` e dovresti vedere l'elenco di automobili sul tuo libro mastro, che è simile al seguente esempio.
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

Per ulteriori informazioni sull'applicazione fabcar e sulle funzioni che utilizza, puoi consultare l'esercitazione completa di [Writing Your First Application ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "writing your first application"){:new_window} nella documentazione di Hyperledger Fabric.

## Utilizzo del tuo profilo di connessione con l'SDK
{: #using-your-connection-profile-with-the-sdk}

Invece di importare manualmente le informazioni sull'endpoint della tua rete, puoi fare in modo che l'SDK si connetta alla rete utilizzando il **Profilo connessione** dalla schermata **Panoramica** del tuo Monitoraggio della rete. Ciò semplifica il processo di connessione alla tua Certificate Authority (Autorità di certificazione) per l'iscrizione e la registrazione. Inoltre, elimina la necessità di definire la tua rete fabric prima di inviare una transazione. L'SDK troverà i peer e gli ordinanti sul canale pertinente direttamente dal profilo di connessione. Puoi trovare ulteriori informazioni su come utilizzare il profilo di connessione nella [documentazione di SDK Node ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-network-config.html "esercitazione sul profilo di connessione"){:new_window}.

Possiamo utilizzare il file `invoke.js` come esempio per vedere l'efficienza dell'utilizzo di un profilo di connessione invece degli endpoint manuali. Puoi stabilire una nuova istanza del client fabric utilizzando la classe `loadFromConfig`. Sostituisci `var fabric_client = new Fabric_Client();` con il seguente codice.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

Invece di configurare la rete fabric creando peer e ordinanti e aggiungendoli quindi a un canale, puoi utilizzare la seguente riga per creare un nuovo canale.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

L'SDK aggiunge quindi i peer e il servizio ordini definiti sul canale utilizzando il profilo di connessione. Ciò rende più efficienti le applicazioni di scrittura e semplifica l'aggiornamento delle applicazioni man mano che i membri della rete si uniscono, escono e avviano nuovi canali. Fai riferimento all'[esercitazione sul profilo di connessione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-network-config.html "esercitazione sul profilo di connessione"){:new_window} nella documentazione di SDK Node per conoscere i passi aggiuntivi coinvolti. Puoi utilizzare questa [versione dell'esercitazione di fabcar ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} che usa un profilo di connessione anziché connessioni endpoint manuali.

Puoi inviare transazioni da approvare ai peer al di fuori della tua organizzazione modificando il tuo profilo di connessione. Il profilo di connessione contiene già le informazioni sull'endpoint e i certificati TLS di peer delle altre organizzazioni sulla tua rete {{site.data.keyword.blockchainfull_notm}} Platform. Aggiungi il nome del peer al canale pertinente nella sezione `channels` del profilo per aggiungere il peer al canale.

## Generazione dei certificati utilizzando il Monitoraggio della rete
{: #enroll-panel}

Puoi utilizzare il Monitoraggio della rete per generare i certificati utilizzando l'identità di amministratore e passare quindi tali certificati direttamente all'SDK. Ciò significa che puoi iniziare a interagire con la rete rapidamente, senza dover generare i certificati utilizzando l'SDK.

Vai al pannello "CA (Certificate Authority)" nel tuo Monitoraggio della rete. Fai clic sul pulsante **Genera certificato** accanto alla tua identità di amministratore per ottenere un nuovo signCert e una nuova chiave privata dalla tua CA. Il campo **Certificato** contiene il signCert, appena sopra **Chiave privata**. Puoi fare clic sull'icona di copia alla fine di ogni campo per copiare il valore. Salva questi certificati in un'ubicazione dove puoi fornirli alla tua applicazione. **Nota** che {{site.data.keyword.blockchainfull_notm}} Platform non memorizza questi certificati. Devi salvarli e memorizzarli in modo sicuro.

Il signCert e la chiave privata sono sufficienti per formare un contesto utente che può firmare le richieste all'interno dell'SDK Node. Usa il metodo [createUser ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html#createUser__anchor "crea utente"){:new_window} della classe Client per creare l'oggetto di contesto utente. All'interno del metodo `creatUser`, passa nome identità e mspid all'oggetto [user ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/global.html#UserOpts "user"){:new_window}, insieme ai percorsi alla chiave privata e al signCert all'oggetto [CryptoContent ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/global.html#CryptoContent "CryptoContent"){:new_window}.

Possiamo utilizzare il pannello "CA (Certificate Authority)" e la classe `createUser` come parte dell'esercitazione di sviluppo di applicazioni come un esempio. Se già hai svolto l'esercitazione, hai installato e istanziato il chaincode `fabcar` e aggiunto dei dati al tuo libro mastro. Puoi utilizzare i certificati per eseguire query del libro mastro come utente `admin`. Attieniti alle istruzioni per generare i certificati usando il Monitoraggio della rete indicate in precedenza, se non lo hai già fatto.

Salva la tua chiave privata come privateKey.pem e il tuo signCert come certificate.pem nella stessa directory di `query.js`. Apri `query.js` in un editor di testo. Aggiungi la seguente riga all'inizio del file:
```
var fs = require('fs');
```
Sostituisci la seguente riga che importa il contesto utente dalla persistenza.
```
return fabric_client.getUserContext('user1', true);
```
con il seguente codice che crea un nuovo contesto utente utilizzando il nostro signCert e la nostra chiave privata.
```
return fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
Il frammento di codice qui in alto legge i tuoi certificati direttamente alla classe `cryptoContent` come file PEM. Il nome utente sarà `admin`, poiché i certificati sono stati generati utilizzando l'identità `admin`. Puoi trovare il tuo mspid nella sezione `certificateAuthorites` del tuo profilo di connessione. Salva il file e immetti il comando `node query.js`. Se ha esito positivo, la query restituirà gli stessi risultati di prima.

## Prassi ottimali per connettività e disponibilità delle applicazioni
{: #app-connectivity-availability}

Il [Flusso di transazioni ![Icona link esterno](images/external_link.svg "Icona link esterno")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flusso di transazioni"){:new_window} di Hyperledger Fabric si estende su più componenti, dove le applicazioni client svolgono un ruolo unico. L'SDK invia le proposte di transazione ai peer per ottenere l'approvazione. Dopo raccoglie le proposte approvate da inviare al servizio ordini, che quindi invia blocchi di transazioni ai peer da aggiungere ai libri mastro del canale. Gli sviluppatori di applicazioni di produzione devono essere preparati a gestire le interazioni tra l'SDK e le loro reti per l'efficienza e la disponibilità.

### Gestione delle transazioni
{: #managing-transactions}

I client dell'applicazione devono garantire che le loro proposte di transazione siano convalidate e vengano completate correttamente. Una proposta può essere ritardata o persa per diversi motivi, ad esempio un'interruzione della rete o un guasto di un componente. Devi preparare la tua applicazione per l'[alta disponibilità](#ha-app) per gestire i guasti dei componenti. Puoi anche [aumentare i valori di timeout](#set-timeout-in-sdk) nella tua applicazione per impedire che le proposte scadano prima che la rete possa rispondere.

Se un chaincode non è in esecuzione, la prima proposta di transazione inviata a questo chaincode avvierà il chaincode. Mentre il chaincode viene avviato, tutte le altre proposte vengono rifiutate con un errore che indica che il chaincode è attualmente in fase di avvio. Questo è diverso dall'invalidità della transazione. Se una proposta viene rifiutata mentre il chaincode è in fase di avvio, i client dell'applicazione devono inviare nuovamente le proposte rifiutate dopo l'avvio del chaincode. I client dell'applicazione possono utilizzare una coda messaggi per evitare di perdere le proposte di transazione.

Puoi utilizzare un servizio eventi basato sul canale per monitorare le transazioni e creare le code messaggi. La classe [channelEventHub ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} può registrare listener in base a eventi di transazione, blocco e chaincode. I listener basati sul canale dall'eventhub di canale possono essere ridimensionati a più canali e possono distinguere il traffico su diversi canali.

Ti consigliamo di utilizzare channelEventHub invece della vecchia classe eventHub. EventHub è a thread singolo e contiene eventi da tutti i canali che potrebbero rallentare, o anche bloccare, i listener nei canali. La classe eventHub non fornisce inoltre alcuna garanzia che un evento verrà recapitato e non fornisce alcun modo per richiamare eventi da un punto specifico, ad esempio un numero di blocco, per tracciare eventi che erano stati mancati.

**Nota:** l'eventhub peer verrà dichiarato obsoleto in una release futura dell'SDK Fabric. Se hai delle applicazioni esistenti che utilizzano l'eventhub peer, aggiorna le tue applicazioni per utilizzare l'eventhub di canale. Per ulteriori informazioni, vedi [How to use the channel-based event service ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} nella documentazione dell'SDK Node.

### Apertura e chiusura di connessioni di rete
{: #connections}

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

  Puoi anche trovare queste variabili con le impostazioni suggerite nella sezione `"peers"` del tuo profilo di connessione di rete. Le opzioni suggerite verranno importate nella tua applicazione automaticamente se utilizzi il [profilo di connessione con l'SDK](#using-your-connection-profile-with-the-sdk) per stabilire una connessione ai tuoi endpoint di rete.

- Se una connessione non è più necessaria, usa i comandi `peer.close()` e `orderer.close()` per liberare risorse e impedire una riduzione delle prestazioni. Per ulteriori informazioni, vedi le classi [close di peer ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "close di peer") e [close di orderer![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "close di orderer") nella documentazione dell'SDK Node. Se hai bisogno di un profilo di connessione per aggiungere peer e ordinanti a un oggetto canale, puoi chiudere tutte le connessioni assegnate a tale canale utilizzando il comando `channel.close()`.

### Applicazioni altamente disponibili
{: #ha-app}

Come procedura ottimale per l'alta disponibilità, si consiglia vivamente di distribuire almeno due peer per ogni organizzazione per il failover. Devi anche adattare le tue applicazioni per l'alta disponibilità. Installa il chaincode su entrambi i peer e aggiungili ai tuoi canali. Quindi, preparati a [inviare le proposte di transazione](#invoke) a entrambi gli endpoint del peer durante l'impostazione della rete e la creazione dell'elenco di destinazione del peer. Le reti piano Enterprise hanno più ordinanti per failover, il che consente alla tua applicazione client di inviare transazioni approvate a un ordinante differente nel caso in cui un ordinante non sia disponibile. Se utilizzi il tuo [profilo di connessione](#using-your-connection-profile-with-the-sdk) invece di aggiungere gli endpoint di rete manualmente, assicurati che il tuo profilo sia aggiornato e che i peer e gli ordinanti supplementari siano stati aggiunti al canale pertinente nella sezione `channels` del profilo. L'SDK può quindi aggiungere i componenti uniti al canale utilizzando il profilo di connessione.

## Abilitazione di TLS reciproco
{: #mutual-tls}

Se esegui le reti piano Enterprise a livello di Fabric V1.1, hai la possibilità di [abilitare il TLS reciproco](v10_dashboard.html#network-preferences) per le tue applicazioni. Se abiliti il TLS reciproco, devi aggiornare le tue applicazioni per supportare questa funzione. In caso contrario, le tue applicazioni non possono comunicare con la tua rete.

Nel profilo di connessione, individua la sezione `certificateAuthorities` dove puoi trovare i seguenti attributi che sono necessari per registrare e ottenere i certificati per comunicare con la tua rete utilizzando il TLS reciproco.

- `url`: l'URL per il collegamento alla CA che può fornire i certificati del TLS reciproco
- `enrollId`: l'ID di registrazione da utilizzare per ottenere un certificato
- `enrollSecret`: il segreto di registrazione da utilizzare per ottenere un certificato
- `x-tlsCAName`: il nome CA da utilizzare per ottenere il certificato che consente all'applicazione di comunicare con il TLS reciproco.

Per ulteriori informazioni sull'aggiornamento delle tue applicazioni in modo che supportino il TLS reciproco, vedi [How to configure mutual TLS ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "tls reciproco"){:new_window}.

## (Facoltativo) Gestione della tua rete utilizzando l'SDK
{: #operate-sdk}

Puoi anche utilizzare l'SDK per gestire la tua rete blockchain. Questa esercitazione spiega come puoi utilizzare l'SDK per unire i tuoi peer ai canali, installare il chaincode sui peer e istanziare il chaincode sui canali. Questi passi sono facoltativi perché puoi eseguire queste operazioni utilizzando anche il Monitoraggio della rete o le API nell'[IU Swagger](howto/swagger_apis.html) se tutti i tuoi peer sono in esecuzione su {{site.data.keyword.blockchainfull_notm}} Platform.

Per completare questi passi, devi caricare il tuo signCert amministratore in {{site.data.keyword.blockchainfull_notm}} Platform. Puoi trovare le istruzioni su come caricare il tuo signCert alla fine della [sezione di iscrizione](#enroll-app-sdk)

### Unione a un canale
{: #join-channel-sdk}

Dopo che la tua organizzazione ha creato o si è unita a un canale utilizzando il Monitoraggio della rete oppure le API, puoi utilizzare l'SDK per unire il tuo peer al canale.

1. [Recupera il blocco genesi ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "recupera il blocco genesi"){:new_window} del canale dal servizio ordini.
2. Passa il blocco genesi al metodo [unione al canale![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} per unire il tuo peer al canale.

Per utilizzare l'esempio `fabcar` per unire un canale, usa il file `invoke.js` come punto di partenza. Devi inviare questa richiesta come amministratore anziché come applicazione, quindi sostituisci `user1` con `admin` nel metodo `getUserContext`. A partire dal punto in cui definisci la richiesta di richiamo del chaincode in `var request = {`, sostituisci il flusso della transazione con una richiesta di unione del canale basata sul frammento di codice sottostante fornito nella [documentazione di SDK Node ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "mutual tls"){:new_window}.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good
    } else {
      // not good
    }
  });
  ```

Il tuo signCert deve essere aggiunto al canale prima che tu possa recuperare il blocco genesi. Se hai generato dei certificati dopo che la tua organizzazione si è unita al canale, devi caricare il tuo signCert sulla piattaforma e fare quindi clic sul pulsante **Sincronizza certificati** nella schermata "Canali". Potresti dover attendere qualche minuto perché la sincronizzazione dei canali venga completata prima di immettere il comando di unione al canale. Per ulteriori informazioni, vedi [Caricamento dei certificati di firma in {{site.data.keyword.blockchainfull_notm}} Platform](certificates.html#upload-certs) nell'esercitazione [Gestione dei certificati](certificates.html).

### Installazione di un chaincode
{: #install-cc-sdk}

Puoi utilizzare il metodo [install chaincode ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} dalla classe [Client Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html "Client Fabric"){:new_window} per installare il chaincode sul tuo peer.

Per utilizzare l'esempio `fabcar` per installare il chaincode `fabcar` sul tuo peer, utilizza il file `query.js` come linea di base e modificalo. Devi inviare questa richiesta come amministratore anziché come applicazione, quindi sostituisci `user1` con `admin` nel metodo `getUserContext`. Sostituisci l'oggetto della proposta di transazione con una richiesta di installazione del chaincode utilizzando l'esempio qui sotto:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

Invia questo oggetto a `return fabric_client.installChaincode(request);`, anziché alla riga `return channel.queryByChaincode(request);` attualmente presente nel file.

### Istanziazione di un chaincode
{: #instantiate-cc-sdk}

Per istanziare il chaincode, devi inviare una [proposta di istanziazione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} al peer e quindi inviare una [richiesta di transazione ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} al servizio ordini.

Per utilizzare l'esempio `fabcar` per istanziare il tuo chaincode, usa il file `invoke.js` come punto di partenza. Devi inviare questa richiesta come amministratore anziché come applicazione, quindi sostituisci `user1` con `admin` nel metodo `getUserContext`. Sostituisci l'oggetto della proposta di transazione con una richiesta di installazione del chaincode utilizzando l'esempio qui sotto:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel',
    txId : tx_id
};
```
{:codeblock}

Invia questa richiesta a `return channel.sendInstantiateProposal(request);` anziché alla riga `return channel.sendTransactionProposal(request);` attualmente presente nel file. Dopo aver inviato la richiesta di istanziazione al canale, devi inviare la proposta approvata sotto forma di transazione al servizio ordini. Per questa operazione vengono utilizzati gli stessi metodi usati per inviare una transazione, per cui puoi mantenere invariato il resto del file. Potresti voler [aumentare il valore di timeout](#set-timeout-in-sdk) nella proposta di istanziazione. In caso contrario, la richiesta potrebbe scadere prima che la piattaforma possa avviare il contenitore chaincode.

Il tuo signCert deve essere aggiunto al canale prima che tu possa istanziare il chaincode. Se hai generato dei certificati dopo che ti sei unito al canale, devi caricare il tuo signCert sulla piattaforma e fare quindi clic sul pulsante **Sincronizza certificati** nella schermata "Canali". Potresti dover attendere qualche minuto perché la sincronizzazione dei canali venga completata prima di immettere il comando di istanziazione del chaincode. Per saperne di più, vedi [Caricamento dei certificati di firma in {{site.data.keyword.blockchainfull_notm}} Platform](certificates.html#upload-certs) nell'esercitazione [Gestione dei certificati](certificates.html).

## (Facoltativo) Impostazione dei valori di timeout negli SDK Fabric
{: #set-timeout-in-sdk}

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

Tuttavia, potresti dover modificare i valori di timeout predefiniti nella tua applicazione. Ad esempio, quando la tua applicazione richiama una transazione che ha bisogno di più di 5000 ms, che è il valore di timeout predefinito per la connessione hub dell'evento, per riposta, potresti ricevere un errore perché l'evento di richiamo termina a 5000 ms prima del completamento della transazione. Puoi impostare la proprietà di sistema per sovrascrivere i valori predefiniti dalla tua applicazione client. Poiché i valori predefiniti sono inizializzati prima che imposti la proprietà di sistema, potrebbe non avere effetto. Pertanto, devi impostare la proprietà di sistema per il timeout in un costrutto statico nella tua applicazione client. Consulta il seguente esempio sulla modifica del valore di timeout per la connessione hub dell'evento a 15000 ms nella SDK Fabric Java. Il percorso file è `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

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
{: #couchdb-indices}

Se i tuoi dati di libro mastro sono archiviati in CouchDB, ti suggeriamo vivamente di creare degli indici per le tue query CouchDB e di usarli nel tuo chaincode. Gli indici consentono alle tue applicazioni di richiamare in modo efficiente i dati mentre la tua rete aggiunge blocchi aggiuntivi di transazioni e voci nello stato globale. Inoltre, CouchDB ti consente di eseguire query di dati avanzate dal tuo chaincode sui dati in un libro mastro del canale.

Per ulteriori informazioni su CouchDB e su come configurare gli indici, vedi [CouchDB as the State Database ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} nella documentazione di Hyperledger Fabric. Puoi anche trovare un esempio che utilizza un indice con il chaincode nell'[esercitazione di CouchDB di Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html).

Evita di usare il chaincode per le query che produrranno una scansione dell'intero database CouchDB. Delle scansioni del database complete comporteranno dei tempi di risposta lunghi e ridurranno le prestazioni della tua rete. Per evitare delle query lunghe, puoi eseguire alcuni dei seguenti passi:
- Configura gli indici con il tuo chaincode.
- Se stai immettendo delle query JSON avanzate, evita gli operatori che comporteranno una scansione dell'intero database, come ad esempio `$or`, `$in` e `$regex`.
- Devi utilizzare un queryLimit per evitare la restituzione di un insieme di dati di grandi dimensioni, che provocherà una condizione di timeout della tua query. Puoi quindi usare più query per eseguire una ricerca in tutto il database con il limite implementato. Se stai utilizzando una query di intervallo, inizia la query successiva con l'ultima chiave restituita dalla query precedente. Se stai usando query JSON avanzate, ordina la tua query utilizzando una delle variabili nei tuoi dati. Usa quindi i risultati per filtrare la tua successiva query utilizzando la stessa variabile.
- Non eseguire una query dell'intero database per fini di aggregazione o di creazione di report. Se vuoi creare un dashboard o raccogliere dati come parte della tua applicazione, puoi eseguire una query di un database fuori dalla catena che replica i dati dalla tua rete blockchain. Ciò ti consentirà di comprendere i dati nella blockchain senza ridurre le prestazioni della tua rete o interrompere le transazioni.

  Puoi utilizzare il Channel Event Hub fornito dall'SDK Fabric per creare un archivio dati fuori dalla catena. Ad esempio, puoi utilizzare un listener di blocco per ottenere le transazioni più recenti aggiunte a un libro mastro del canale. Gli insiemi di lettura e scrittura di transazioni possono quindi essere utilizzati per aggiornare una copia dello stato globale che è stato archiviato in un database separato. Per ulteriori informazioni, vedi [How to use the channel-based event service ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "How to use the channel-based event service"){:new_window} nella documentazione dell'SDK Node.

## Host di applicazioni
{: #host-app}

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
3. Accedi alla directory della tua applicazione ed esegui il push della tua applicazione immettendo il seguente comando. Questa operazione può richiedere diversi minuti, a seconda della dimensione della tua applicazione. Puoi visualizzare i log da {{site.data.keyword.Bluemix_notm}} nel tuo terminale. I log cessano quando l'applicazione viene avviata correttamente.
	```
	> cf push IL_NOME_DELLA_TUA_APPLICAZIONE_QUI
	```
	{:codeblock}
	Puoi controllare i log della tua applicazione immettendo uno dei seguenti comandi:
	* `> cf logs IL_NOME_DELLA_TUA_APPLICAZIONE_QUI`
	* `> cf logs IL_NOME_DELLA_TUA_APPLICAZIONE_QUI --recent`

## Disconnessione della tua applicazione dalla rete
{: #disconnect-app}

Completa la seguente procedura per rimuovere la connessione tra la tua applicazione e la rete blockchain su {{site.data.keyword.cloud_notm}}.
1. Rimuovi le informazioni sull'endpoint API dal file di configurazione della tua applicazione. Per informazioni di riferimento, vedi [Aggiunta di endpoint API di rete alla tua applicazione](#api-endpoints).
2. Elimina il tuo contenitore chaincode.
  1. Nella schermata "Canale" del Monitoraggio della rete, individua il canale dove è installato il tuo chaincode.
  2. Nella schermata dello specifico canale, individua il chaincode che vuoi disabilitare.
  3. Fai clic sul pulsante **Elimina** e fai clic su **Invia** nel pannello di eliminazione del chaincode. Il tuo contenitore chaincode verrà rimosso.
	![Elimina un chaincode](images/channel_chaincode.png "Elimina un chaincode")

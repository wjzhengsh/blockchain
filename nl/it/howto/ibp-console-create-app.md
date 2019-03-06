---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Creazione di applicazioni
{: #ibp-console-app}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Dopo aver installato gli smart contract e distribuito i tuoi nodi, puoi utilizzare le applicazioni client per effettuare transazioni con gli altri membri della tua rete. Le applicazioni possono richiamare la logica di business contenuta negli smart contract per creare, trasferire o aggiornare gli asset nel libro mastro blockchain. Utilizza questa esercitazione per imparare come interagire con le reti che gestisci dalla console della piattaforma {{site.data.keyword.blockchainfull}} dalle applicazioni client.
{:shortdesc}

Puoi scoprire di più su come applicazioni e smart contract lavorano insieme consultando [l'argomento sullo sviluppo di applicazioni ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "developing applications") nella documentazione di Hyperledger Fabric. L'argomento esplora un caso di utilizzo ipotetico in cui banche e società si scambiano la commercial paper e descrive come vengono codificate le transazioni negli smart contract. L'[esercitazione sulla commercial paper ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") estende questo argomento consentendoti di distribuire gli smart contract sulla rete fabric e di utilizzare il codice dell'applicazione di esempio per creare e trasferire la commercial paper tra i membri della rete.

Lo sviluppo di un'applicazione potrebbe richiedere il coordinamento tra due utenti distinti della tua rete, l'operatore di rete e lo sviluppatore di applicazioni:
- **L'operatore di rete** è l'amministratore che utilizza la console {{site.data.keyword.blockchainfull_notm}} Platform per distribuire i nodi della tua organizzazione e installa gli smart contract sulla tua rete.
- **Lo sviluppatore di applicazioni** crea l'applicazione client che verrà utilizzata dagli utenti finali. Lo sviluppatore utilizza gli [SDK Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK's"){:new_window} per richiamare le transazioni scritte negli smart contract.

Se sei l'**operatore di rete**, dovrai completare i seguenti passi prima che lo sviluppatore di applicazioni possa interagire con la tua rete.
1. Utilizza la CA della tua organizzazione per [registrare un'identità dell'applicazione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities).
2. [Scarica il profilo di connessione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) dal pannello degli smart contract.
3. Invia allo sviluppatore di applicazioni i seguenti oggetti e informazioni:
  - L'ID e il segreto di registrazione dell'identità dell'applicazione.
  - Il profilo di connessione.
  - Il nome dello smart contract.
  - Il nome del canale in cui è stato istanziato lo smart contract.  

Se sei lo **sviluppatore di applicazioni**, utilizza le informazioni fornite dall'operatore di rete per completare i seguenti passi:
1. Genera una coppia di chiavi pubblica e privata utilizzando l'ID e il segreto di registrazione dell'identità dell'applicazione, insieme alle informazioni sull'endpoint CA all'interno del tuo profilo di connessione.
2. Utilizza il profilo di connessione, il nome del canale, il nome dello smart contract e le chiavi dell'applicazione per richiamare lo smart contract.  

Lo sviluppatore di applicazioni può utilizzare due modelli di programmazione per interagire con la rete:

**API SDK Fabric di alto livello**

A partire da Fabric v1.4, gli utenti possono usufruire di un modello di programmazione di applicazioni e smart contract semplificato. Il nuovo modello riduce il numero di passi e la quantità di codice necessari per inviare una transazione. Questo modello è supportato solo per le applicazioni scritte in **Node.js**. Se vuoi usufruire del nuovo modello, puoi utilizzare questa esercitazione per completare le seguenti azioni su una rete {{site.data.keyword.blockchainfull_notm}} Platform 2.0:

- [Genera certificati per la tua applicazione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll) utilizzando l'SDK.
- [Richiama uno smart contract dall'SDK](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke).
- Utilizza l'[estensione IBM Blockchain VScode](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) per scrivere, distribuire e sviluppare e testare iterativamente i tuoi smart contract.
- Acquisisci informazioni sullo sviluppo di applicazioni distribuendo l'[esercitazione sulla commercial paper](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) ai nodi gestiti dalla tua console. Questa esercitazione fornirà un ulteriore background su come utilizzare i portafogli e i gateway Fabric.


**API SDK Fabric di basso livello**

Se vuoi continuare a utilizzare il tuo codice di smart contract e applicazioni esistente o vuoi utilizzare gli altri linguaggi SDK Fabric forniti dalla community Hyperledger, puoi usare le [API SDK Fabric di basso livello](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level) per connetterti alla tua rete.

## Registra un'identità dell'applicazione
{: #ibp-console-app-identities}

Le applicazioni devono firmare le transazioni che inviano ai nodi {{site.data.keyword.blockchainfull_notm}} e allegare una chiave pubblica utilizzata dai nodi per verificare che le transazioni vengano inviate dalla parte corretta. Ciò garantisce che le transazioni vengano inviate dalle organizzazioni autorizzate a partecipare. 

L'operatore di rete deve utilizzare la CA dell'organizzazione per registrare un'identità dell'applicazione, che può quindi essere utilizzata dallo sviluppatore di applicazioni per generare una chiave pubblica e privata. L'operatore può fornire l'ID e il segreto di registrazione dell'identità, insieme alle informazioni sull'endpoint CA, che verranno utilizzati dall'SDK per generare i certificati. Effettuando l'iscrizione sul lato client, lo sviluppatore di applicazioni garantisce che nessun'altra parte abbia accesso alla chiave privata dell'applicazione. Per ulteriore sicurezza, l'operatore di rete può impostare un limite di iscrizione di uno durante la registrazione. Dopo che lo sviluppatore di applicazioni esegue l'iscrizione, non è possibile utilizzare l'ID e il segreto di registrazione per generare un'altra chiave privata.

Se non sei molto preoccupato per la sicurezza, l'operatore di rete può iscrivere un'identità dell'applicazione utilizzando la [scheda CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll). L'operatore può quindi scaricare l'identità o esportarla nel portafoglio della console. Per utilizzare i certificati dall'SDK, le chiavi devono essere decodificate dal formato base64 in formato PEM. Puoi decodificare i certificati immettendo il seguente comando sulla tua macchina locale:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

## Scarica il tuo profilo di connessione
{: #ibp-console-app-profile}

Le applicazioni sono in grado di inviare transazioni solo agli smart contract che sono stati istanziati sui canali. Di conseguenza, le informazioni che devi connettere per interagire con uno smart contract possono essere trovate nell'elenco degli smart contract istanziati nella tua console. Ciò significa che devi aver già installato e istanziato il tuo smart contract.

Il [flusso della transazione![Icona link esterno](../images/external_link.svg "Icona link esterno")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaction Flow"){:new_window} di Hyperledger Fabric si estende su più componenti, dove le applicazioni client raccolgono le approvazioni dai peer e inviano le transazioni approvate al servizio ordini. Il profilo di connessione fornisce alla tua applicazione gli endpoint dei peer e i nodi di ordine di cui ha bisogno per inviare una transazione. Contiene inoltre informazioni sulla tua organizzazione, come le tue autorità di certificazione (CA) e il tuo ID MSP. Gli SDK Fabric possono leggere direttamente il profilo di connessione, senza dover scrivere il codice che gestisce il flusso di transazione e di approvazione.

Se hai configurato i peer di ancoraggio durante la creazione di un canale, puoi usufruire della funzione di [rilevamento servizi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery") di Hyperledger Fabric. Il rilevamento dei servizi consente alla tua applicazione di conoscere quali peer sul canale esterno alla tua organizzazione devono approvare una transazione. Senza il rilevamento dei servizi, dovrai ottenere le informazioni sugli endpoint di questi peer fuori banda da altre organizzazioni e aggiungerle al tuo profilo di connessione. Per ulteriori informazioni su come configurare i peer di ancoraggio, vedi il passo tre dell'[argomento sui dati privati](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) nell'esercitazione sulla distribuzione di uno smart contract.

Vai alla scheda degli smart contract nella tua console della piattaforma. Accanto a ciascuno smart contract istanziato, vai al menu di overflow. Fai clic sul pulsante denominato **Connettiti con SDK**. Verrà aperto un pannello laterale che ti consentirà di creare e scaricare il tuo profilo di connessione. Innanzitutto, devi selezionare la CA della tua organizzazione che hai utilizzato per registrare l'identità della tua applicazione. Dovrai anche selezionare la definizione MSP della tua organizzazione. Sarai quindi in grado di scaricare il profilo di connessione che puoi utilizzare per richiamare lo smart contract.

## Esegui l'iscrizione utilizzando l'SDK
{: #ibp-console-app-enroll}

Una volta che l'operatore di rete fornisce l'ID e il segreto di registrazione dell'identità dell'applicazione e il profilo di connessione della rete, uno sviluppatore di applicazioni può utilizzare gli SDK Fabric o il client CA Fabric per generare i certificati lato client. Puoi utilizzare la seguente procedura per iscrivere un'identità dell'applicazione usando l'[SDK Fabric per Node.js ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "Fabric SDK for Node.js").

1. Salva il profilo di connessione sulla tua macchina locale e rinominalo in `connection.json`.
2. Salva il seguente blocco di codice come `enrollUser.js` nella stessa directory del tuo profilo di connessione:

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Create a new CA client for interacting with the CA.
      const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
      const ca = new FabricCAServices(caURL);

      // Create a new file system based wallet for managing identities.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Check to see if we've already enrolled the admin user.
      const userExists = await wallet.exists('user1');
      if (userExists) {
        console.log('An identity for "user1" already exists in the wallet');
        return;
      }

      // Enroll the admin user, and import the new identity into the wallet.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. Modifica `enrollUser.js` per sostituire i seguenti valori:
  - Sostituisci ``<CA_Name>`` con il nome della CA della tua organizzazione. Puoi trovare il nome della CA nella sezione "organizations" del tuo profilo di connessione sotto "Certificate Authorities". Non utilizzare il "caName" presente nella sezione "Certificate Authorities".
  - Sostituisci ``<app_enroll_id>`` con l'ID di registrazione dell'applicazione fornito dal tuo operatore di rete.
  - Sostituisci ``<app_enroll_secret>`` con il segreto di registrazione dell'applicazione fornito dal tuo operatore di rete.
  - Sostituisci ``<msp_id>`` con l'ID MSP della tua organizzazione. Puoi trovare il tuo ID MSP nella sezione "organizations" del tuo profilo di connessione.
4. Passa a `enrollUser.js` utilizzando un terminale ed esegui `node enrollUser.js`. Se il comando viene eseguito correttamente, dovresti vedere il seguente output:

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
  L'SDK creerà e memorizzerà i tuoi certificati all'interno della directory `wallet/user1/` creata dal comando. Questa directory è il portafoglio del file system utilizzato per inviare le transazioni future.


## Richiama uno smart contract utilizzando l'SDK
{: #ibp-console-app-invoke}

Dopo aver generato la chiave pubblica e privata dell'applicazione e averle memorizzate in un portafoglio, sei pronto per inviare una transazione. Devi conoscere il nome dello smart contract e il nome del canale in cui è stato istanziato. Puoi utilizzare la seguente procedura per richiamare uno smart contract usando l'[SDK Fabric per Node.js ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "Fabric SDK for Node.js").


1. Salva il file qui sotto sulla tua macchina locale come `invoke.js`. Salva il file nella stessa directory di `enrollUser.js`

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Parse the connection profile. This would be the path to the file downloaded
        // from the IBM Blockchain Platform operational console.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Configure a wallet. This wallet must already be primed with an identity that
        // the application can use to interact with the peer node.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Create a new gateway, and connect to the gateway peer node(s). The identity
        // specified must already exist in the specified wallet.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {discover: true, asLocalhost:false }});

        // Get the network channel that the smart contract is deployed to.
        const network = await gateway.getNetwork('<channel_name>');

        // Get the smart contract from the network channel.
        const contract = network.getContract('<smart_contract_name>');

        // Submit the 'createCar' transaction to the smart contract, and wait for it
        // to be committed to the ledger.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. Modifica `invoke.js` per sostituire i seguenti valori:
  - Sostituisci ``<channel_name>`` con il nome del canale in cui è stato istanziato lo smart contract. Puoi trovare il nome della tua CA nella sezione "Certificate Authorities" del tuo profilo di connessione.
  - Sostituisci ``<smart_contract_name>`` con il nome dello smart contract installato. Puoi ottenere questo valore dal tuo operatore di rete. 
  - Modifica il contenuto di `submitTransaction` per richiamare una funzione all'interno del tuo smart contract. Il file `invoke.js` viene scritto per richiamare lo [smart contract fabcar ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar). Se vuoi eseguire il file sottostante per inviare una transazione, installa fabcar e istanzia lo smart contract su uno dei tuoi canali.

3. Passa a `invoke.js` utilizzando un terminale ed esegui `node invoke.js`. Se il comando viene eseguito correttamente, dovresti vedere il seguente output:

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  Se passi al tuo canale utilizzando la console, sarai in grado di vedere un altro blocco aggiunto dalla transazione.


## Connessione con l'estensione IBM Blockchain VScode
{: #ibp-console-app-vscode}

L'estensione {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code fornisce un ambiente in Visual Studio Code per sviluppare, assemblare e distribuire pacchetti di smart contract. Assicurati di avere il profilo di connessione e la serie di file di chiave generati utilizzando la tua CA. Puoi quindi utilizzare l'estensione VScode per connetterti a una rete che gestisci utilizzando la tua console.

Segui le istruzioni per scaricare l'[estensione VScode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode extension") nel marketplace di Visual Studio Code. Una volta installata l'estensione, apri VScode e accedi all'estensione facendo clic su **View** > **Command Palette**. Immetti il comando *IBM Blockchain Platform: Create Smart Contract Project* per creare un nuovo progetto smart contract.

Dopo aver creato il nuovo progetto, puoi connetterti direttamente alla tua rete da Visual Studio Code utilizzando le informazioni contenute nel tuo smart contract istanziato dalla console. Utilizza la tabella **Smart contract istanziati** nella scheda Smart contract per scaricare il tuo [profilo di connessione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) nel file system locale. Quindi, [crea un'identità dell'applicazione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) e usa la tua CA per creare e scaricare un certificato (chiave pubblica) e una chiave privata. Segui le istruzioni precedenti per convertire la chiave privata e il certificato in formato PEM. Una volta scaricato il profilo di connessione e le chiavi, utilizza i seguenti passi per connetterti alla tua rete.

1. Apri la scheda _{{site.data.keyword.blockchainfull_notm}} Platform_ in Visual Studio Code.
2. Nel riquadro _{{site.data.keyword.blockchainfull_notm}} Platform_, fai clic su **Add new connection**.
3. Immetti un nome per la connessione. Questo nome verrà visualizzato nel riquadro _{{site.data.keyword.blockchainfull_notm}} Platform_.
4. Immetti il percorso file completo del tuo profilo di connessione.
5. Immetti il percorso file completo del tuo certificato (chiave pubblica) in formato PEM.
6. Immetti il percorso file completo della chiave privata in formato PEM.
7. La tua connessione dovrebbe ora comparire nell'elenco di connessioni sotto `local_fabric`. Fai doppio clic sul nome connessione per stabilire la connessione.

Una volta che sei connesso da VScode, sarai in grado di vedere l'elenco dei peer dalla tua organizzazione e dei canali che hanno unito nel pannello delle connessioni blockchain. Potrai visualizzare l'elenco degli smart contract installati sotto ciascun peer. Per installare un nuovo smart contract dal tuo progetto sulla tua rete, innanzitutto fai clic con il tasto destro del mouse su un peer e seleziona **Installa smart contract**. Puoi quindi istanziare tale smart contract facendo clic con il tasto destro del mouse su un canale e selezionando **Installa smart contract**. Per ulteriori informazioni sull'utilizzo dell'estensione di {{site.data.keyword.blockchainfull_notm}} Platform, consulta la documentazione nel [marketplace di Visual Studio Code ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode extension").


## Utilizzo dell'esempio di Commercial Paper
{: #ibp-console-app-commercial-paper}

L'[esercitazione sulla commercial paper ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") nella documentazione di Hyperledger Fabric guida gli sviluppatori in un caso di utilizzo in cui più parti comprano, vendono e riscattano la commercial paper. Estende l'[argomento sullo sviluppo di applicazioni ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "developing applications") fornendo un codice di smart contract e applicazioni di esempio che ti consente di creare e scambiare asset su un'istanza locale di Fabric.

Puoi anche distribuire il codice di esempio dell'esercitazione sulla commercial paper su una rete {{site.data.keyword.blockchainfull_notm}} Platform 2.0. Ciò ti consentirà di iniziare rapidamente a interagire con la tua rete e utilizzare l'esempio per scaricare le dipendenze necessarie. Il codice di esempio include anche degli esempi di come importare i certificati in un [portafoglio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") e utilizzare il profilo di connessione per creare un [gateway Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway"). L'esercitazione può essere utilizzata da due organizzazioni differenti per completare transazioni diverse con un singolo asset. Se hai utilizzato l'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) per distribuire due organizzazioni peer connesse a un canale, puoi interagire con l'esercitazione utilizzando entrambe le organizzazioni.

Segui i passi riportati di seguito per distribuire l'esempio sulla tua rete. Per ulteriori dettagli sugli smart contract e sulla struttura dell'applicazione, puoi esaminare l'[esercitazione sulla commercial paper ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "commercial paper tutorial") nella documentazione di Fabric.

### Prerequisiti

Prima di poter distribuire l'esempio di commercial paper, dovrai installare gli strumenti necessari sulla tua macchina locale:
  * [Git ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

Dovrai anche utilizzare un editor di testo per modificare e salvare i file nell'esempio. Puoi usare molti degli editor di alta qualità che sono disponibili gratuitamente, come ad esempio [Atom ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://atom.io/ "atom"), [Sublime Text ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://www.sublimetext.com/ "Git") o [Brackets ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://brackets.io/ "Brackets").

### Passo uno: scarica l'esempio

Scarica l'esempio di commercial paper clonando il [repository di esempi Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-samples "Fabric Samples repository"):

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

Dopo aver scaricato gli esempi di Fabric, immetti i seguenti comandi per assicurarti di utilizzare la versione degli esempi compatibile con Fabric v1.4.

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

Puoi trovare l'esempio nella cartella `commercial paper` di `fabric-samples`.

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

L'esercitazione contiene due organizzazioni di esempio, **magnetocorp** e **digibank**. Ogni organizzazione ha il suo proprio codice dell'applicazione di esempio, memorizzato in due cartelle separate nella directory `organization`.

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

Nel corso dell'esercitazione, per prima cosa utilizzerai il codice dell'applicazione magnetocorp per emettere una commercial paper. Potrai quindi utilizzare il codice dell'applicazione digibank per acquistare e riscattare tale commercial paper. Entrambe le directory contengono lo stesso smart contract.

Vai al codice dell'applicazione all'interno della directory magnetocorp.

```
cd magnetocorp/application
```
{:codeblock}

Una volta dentro la directory, puoi scaricare le dipendenze dell'applicazione immettendo il seguente comando:

```
npm install
```
{:codeblock}

### Passo due: installa e istanzia lo smart contract

Puoi trovare lo smart contract della commercial paper nella cartella `contract` della directory `digibank` e `magnetocorp`. Devi installare questo smart contract su tutti i peer delle organizzazioni utilizzando l'esercitazione. Dovrai quindi istanziare il contratto della commercial paper su un canale. Lo smart contract deve essere impacchettato in [formato .cds ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "packaging smart contracts") per essere installato utilizzando la console.

Puoi utilizzare l'[estensione IBM Blockchain VScode](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) per impacchettare lo smart contract. Dopo aver installato l'estensione, utilizza Visual Studio Code per aprire la cartella `contracts` nel tuo spazio di lavoro. Apri la scheda _{{site.data.keyword.blockchainfull_notm}} Platform_. Nel riquadro _{{site.data.keyword.blockchainfull_notm}} Platform_, vai alla sezione dei pacchetti di smart contract e fai clic su **Package a Smart Contract Project**. L'estensione VScode utilizzerà i file contenuti nella cartella `contracts` per creare un nuovo pacchetto denominato `papernet-js@.0.0.1.cds`. Fai clic con il tasto destro del mouse su questo pacchetto per esportarlo nel tuo file system locale. Puoi quindi usare la tua console per [installare gli smart contract sui tuoi peer](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install) e in seguito [istanziare lo smart contract su un canale](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).

### Passo tre: genera i certificati per il tuo portafoglio

Le applicazioni devono firmare le richieste che inviano ai componenti Fabric. Se i componenti non riconoscono le organizzazioni che inviano le transazioni, le transazioni verranno rifiutate e restituiranno un errore. L'esempio di commercial paper crea un portafoglio del file system che memorizzerà i tuoi certificati e firmerà le tue transazioni. Per ulteriori informazioni su come le applicazioni utilizzano i portafogli, consulta l'argomento sui [portafogli ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") nella documentazione di Fabric.

L'esempio originale utilizza il file `addToWallet.js` per creare un portafoglio del file system utilizzando i certificati dalla cartella degli esempi di Fabric. Creeremo un nuovo file che utilizza l'SDK per generare certificati lato client e memorizzarli direttamente all'interno di un nuovo portafoglio.


Scegli la CA dell'organizzazione che vuoi adoperare per utilizzare l'esercitazione come magnetocorp. Ad esempio, puoi usare Org1 se hai completato l'esercitazione **Crea una rete**. Utilizza la CA per [creare un'identità dell'applicazione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities). **Salva** l'ID e il segreto di registrazione.

Utilizza la tua console per [scaricare il profilo di connessione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Salva il profilo di connessione sul tuo file system locale e rinominalo in `connection.json`. Utilizza quindi il seguente comando per spostare il profilo di connessione in una directory a cui verrà fatto riferimento da comandi futuri.

  ```
  mv $HOME/<path_to_creds>/connection.json /gateway/connection.json
  ```
  {:codeblock}

Passa alla directory `/magnetocorp/application` e salva il seguente blocco di codice come `enrollUser.js`.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Create a new CA client for interacting with the CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Create a new file system based wallet for managing identities.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Check to see if we've already enrolled the admin user.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Enroll the admin user, and import the new identity into the wallet.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

Prendiamoci un momento per verificare come funziona questo file prima di modificarlo. Innanzitutto, `enrollUser.js` importa le classi `FileSystemWallet` e `X509WalletMixin` dalla libreria `fabric-network`.

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

Il file utilizza quindi la classe `FileSystemWallet` per creare un portafoglio sul tuo file system locale. Puoi modificare la seguente riga per memorizzare il portafoglio in un'ubicazione diversa.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

Dopo aver creato il portafoglio, il frammento di codice utilizza l'ID e il segreto di registrazione per eseguire l'iscrizione utilizzando la CA della tua organizzazione. Quindi crea un'identità per la coppia di chiavi pubblica e privata e le importa nel portafoglio. Nota come il file passa anche l'ID MSP della tua organizzazione nel portafoglio.

```
// Enroll the admin user, and import the new identity into the wallet.
const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

**Modifica** `enrollUser.js` per sostituire i seguenti valori:
- Sostituisci `'<CA_Name>'` con il nome della CA della tua organizzazione. Puoi trovare il nome della CA nella sezione "organizations" del tuo profilo di connessione sotto "Certificate Authorities". Non utilizzare il "caName" presente nella sezione "Certificate Authorities".
- Sostituisci `'<app_enroll_id>` con l'ID di registrazione dell'applicazione fornito dal tuo operatore di rete.
- Sostituisci `'<app_enroll_secret>'` con il segreto di registrazione dell'applicazione fornito dal tuo operatore di rete.
- Sostituisci `'<msp_id>'` con l'ID MSP della tua organizzazione. Puoi trovare questo ID MSP nella sezione "organizations" del tuo profilo di connessione.

Salva `enrollUser.js` e chiudilo. Nella directory `magnetocorp`, esegui questo comando:
```
node enrollUser.js
```
{:codeblock}

Se il comando termina correttamente, dovresti vedere il seguente output:

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

Puoi trovare il portafoglio che è stato creato nella cartella `identity` della directory `magnetocorp`.

### Passo quattro: utilizzo del profilo di connessione per creare un gateway Fabric

Il [Flusso di transazioni ![Icona link esterno](../images/external_link.svg "Icona link esterno")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Flusso di transazioni"){:new_window} di Hyperledger Fabric si estende su più componenti, dove le applicazioni client svolgono un ruolo unico. La tua applicazione deve connettersi ai peer che devono approvare la transazione e al servizio ordini che ordinerà la transazione e la aggiungerà in un blocco. Puoi fornire gli endpoint di questi nodi alla tua applicazione utilizzando il tuo profilo di connessione per costruire un gateway Fabric. Il gateway conduce quindi le interazioni di basso livello con la tua rete Fabric. Per ulteriori informazioni, consulta l'argomento sul [gateway Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway") nella documentazione di Fabric.

Hai già scaricato il tuo profilo di connessione e lo hai utilizzato per connetterti all'Autorità di certificazione (CA) della tua organizzazione. Ora useremo il profilo di connessione per creare un gateway.

Apri il file `issue.js` in un editor di testo. Prima di modificare il file, nota che questo importa le classi `FileSystemWallet` e `Gateway` dalla libreria della rete Fabric.

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

La classe `Gateway` è utilizzata per costruire un gateway che userai per inviare la tua transazione.

```
const gateway = new Gateway()
```
{:codeblock}

La classe `FileSystemWallet` è utilizzata per caricare il portafoglio che hai creato nel passo precedente. **Modifica** la seguente riga se hai cambiato l'ubicazione del portafoglio sul tuo file system.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

Dopo aver importato il tuo portafoglio, il seguente codice passa il tuo profilo di connessione e il portafoglio al nuovo gateway. Le righe che stampano i log sono state rimosse per la brevità.

```
const userName = 'User1@org1.example.com';

// Load connection profile; will be used to locate a gateway
let connectionProfile = yaml.safeLoad(fs.readFileSync('../gateway/networkConnection.yaml', 'utf8'));

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: false, asLocalhost: true }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

**Modifica** il codice precedente per importare il profilo di connessione che hai scaricato dalla console. Aggiorna il nome utente selezionato per `identityLabel` in `enrollUser.js`. Inoltre, devi aggiornare anche le opzioni di rilevamento per usufruire del rilevamento dei servizi sulla tua rete. Imposta `discovery: { enabled: true, asLocalhost: false }`.  **Nota** che il profilo di connessione della console è in formato JSON anziché in un file YAML. Dovrai modificare la sezione che importa il tuo profilo di connessione. Dopo le tue modifiche, il codice precedente potrebbe essere simile al seguente esempio:

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const ccp = JSON.parse(ccpJSON);

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

Questo frammento di codice utilizzerà il gateway per aprire connessioni GRPC ai nodi peer e ordinante e interagire con la tua rete.

### Passo cinque: richiamo dello smart contract

Dopo aver configurato il gateway per la connessione alla rete gestita dalla tua console, modificheremo la parte del file `issue.js` che si connette allo smart contract della commercial paper. Dovrai fornire al gateway il nome del contratto e il canale su cui hai istanziato lo smart contract.

**Modifica** la seguente riga, sostituendo `mychannel` con il tuo nome di canale.

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

Seguendo una riga di codice che stampa un messaggio sulla tua console, puoi trovare una riga che fornisce al gateway il nome dello smart contract. **Modifica** la seguente riga per cambiare il nome `papercontract` con il nome del contratto che hai installato.

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

Il gateway ora ha tutte le informazioni che gli servono per inviare una transazione. La seguente riga richiama la funzione di emissione `issue` nel contratto della commercial paper, con gli argomenti che definiscono il nuovo asset di commercial paper.

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

Dopo aver inviato la transazione utilizzando il gateway, il file `issue.js` disconnette la connessione del gateway.

```
gateway.disconnect();
```
{:codeblock}

Questo comando chiude le connessioni GRPC aperte dal tuo gateway. La chiusura delle connessioni salverà le risorse di rete e migliorerà le prestazioni.

Dopo aver completato le modifiche in questo passo e nel **Passo quattro**, salva il file `issue.js` e chiudilo. Invia la transazione che crea la nuova commercial paper utilizzando il seguente comando:

```
node issue.js
```
{:codeblock}

Se la transazione ha esito positivo, dovresti essere in grado di vedere il seguente output nel tuo terminale:

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### Passo sei: utilizza l'esempio come digibank

Dopo aver creato la commercial paper utilizzando magnetocorp, puoi acquistare e riscattare la commercial paper utilizzando l'esercitazione come digibank. Puoi utilizzare il codice dell'applicazione digibank utilizzando la stessa organizzazione di magnetocorp o utilizzare la CA, i peer e il profilo di connessione di un'organizzazione diversa. Se hai completato l'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), questa è una buona opportunità per utilizzare l'esercitazione come Org2.

Vai alla directory `digibank/application`. Puoi seguire le istruzioni fornite nel **Passo tre** per generare i certificati e il portafoglio che firmeranno la transazione come digibank. Puoi quindi utilizzare il file `buy.js` per acquistare la commercial paper da magnetocorp e utilizzare `redeem.js` per riscattarla. Puoi seguire il **Passo quattro** e il **Passo cinque** per modificare questi file in modo che puntino al profilo di connessione, canale e smart contract appropriati.

## Connessione alla tua rete utilizzando le API SDK Fabric di basso livello
{: #ibp-console-app-low-level}

Se sei interessato a preservare il tuo codice applicazione esistente o a utilizzare gli SDK Fabric per linguaggi diversi da Node.js, puoi ancora connetterti alla tua rete utilizzando API SDK Fabric di livello inferiore. Utilizza la console per [scaricare il tuo profilo di connessione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Puoi quindi importare gli endpoint dei peer e dei nodi di ordine del tuo canale direttamente dal profilo di connessione o utilizzare le informazioni sull'endpoint dei nodi per aggiungere manualmente gli oggetti peer e ordinante. Dovrai anche utilizzare la tua CA per [creare un'identità dell'applicazione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) e quindi utilizzare le informazioni sull'endpoint CA per eseguire l'iscrizione sul lato client o generare i certificati utilizzando la console.

La documentazione di [SDK Node Fabric Node ![Icona link esterno](../images/external_link.svg "Icona link esterno ")](https://fabric-sdk-node.github.io "Fabric Node SDK") fornisce un'esercitazione su come [connetterti alla tua rete utilizzando un profilo di connessione ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-network-config.html "connection profile tutorial"){:new_window}. L'esercitazione utilizza le informazioni sull'endpoint CA nel tuo profilo di connessione per generare chiavi utilizzando l'SDK. Puoi anche utilizzare la tua console per generare una chiave pubblica e privata e convertire le chiavi in formato PEM. Puoi quindi impostare un contesto utente passando le tue chiavi direttamente alla [classe client Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Client.html "Fabric Client class") utilizzando il seguente codice:

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

Se utilizzi le API SDK di basso livello per connetterti alla tua rete, puoi completare ulteriori passi per gestire le prestazioni e la disponibilità della tua applicazione. Per ulteriori informazioni, vedi [Prassi ottimali per connettività e disponibilità delle applicazioni](/docs/services/blockchain/v10_application.html#dev-app-connectivity-availability).

## Utilizzo degli indici con CouchDB
{: #console-app-couchdb}

Se utilizzi CouchDB come tuo database dello stato, puoi eseguire query di dati JSON dai tuoi smart contract rispetto ai dati di stato del canale. Si consiglia vivamente di creare degli indici per le tue query JSON e di usarli nei tuoi smart contract. Gli indici consentono alle tue applicazioni di richiamare i dati in modo efficiente quando la tua rete aggiunge ulteriori blocchi di transazioni e voci nello stato globale. Per informazioni su come utilizzare gli indici con gli smart contract e le applicazioni, vedi [Prassi ottimali quando si utilizza CouchDB](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices).

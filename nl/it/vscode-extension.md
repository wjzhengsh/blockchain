---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: vs code extension, Visual Studio Code extension, smart contract, development tools

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

# Sviluppo di smart contract con l'estensione Visual Studio Code
{: #develop-vscode}


L'estensione {{site.data.keyword.blockchainfull}} Platform Visual Studio (VS) Code fornisce un ambiente in Visual Studio Code per sviluppare, impacchettare e verificare gli smart contract. Puoi utilizzare l'estensione per creare il tuo progetto di smart contract e per iniziare a sviluppare la tua logica di business. Puoi quindi utilizzare VS Code per verificare il tuo smart contract sulla tua macchina locale utilizzando un'istanza preconfigurata di Hyperledger Fabric prima di distribuire lo smart contract a {{site.data.keyword.blockchainfull_notm}} Platform. Questa esercitazione illustra come utilizzare l'estensione VS Code.


![Normale flusso di lavoro di sviluppo dello smart contract](images/SmartContractflow.png "Normale flusso di lavoro di sviluppo dello smart contract")
*Figura 1. Normale flusso di lavoro di sviluppo dello smart contract con l'estensione {{site.data.keyword.blockchainfull_notm}} Platform VS Code*  

<!--
<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="images/SmartContractflow.png" width="750" alt="Click a box to get more details on the process." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Create a smart contract project" title="Create a Smart contract project" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Develop contract code in VS Code" title="Create key pair" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract" alt="Package the smart contract" title="Package the smart contract" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-deploy" alt="Deploy locally to debug" title="Deploy locally to debug" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-exporting-deleting-smart-contract-package" alt="Export the package" title="Export the package" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-connecting-ibp" alt="Deploy to IBM Cloud" title="Deploy to IBM Cloud" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
-->

L'estensione {{site.data.keyword.blockchainfull_notm}} Platform funziona facilmente con tutte le istanze di {{site.data.keyword.blockchainfull_notm}} Platform che utilizzano Hyperledger Fabric versioni 1.4 e successive.
{: note}

## Passo uno: installa l'estensione {{site.data.keyword.blockchainfull_notm}} Platform VS Code gratuitamente
{: #develop-vscode-install}

Prima di installare l'estensione {{site.data.keyword.blockchainfull_notm}} Platform VS Code, devi completare i prerequisiti.

### Prerequisiti
{: #develop-vscode-prerequisites}

- **Installa Visual Studio Code**  
  Installa l'editor di codice [Visual Studio](https://code.visualstudio.com/).  
- **Installa Yeoman**  
  Yeoman è uno strumento generatore che puoi utilizzare per creare progetti smart contract di base. Installa Yeoman utilizzando il seguente comando: `npm install -g yo`.  
- **Installa Docker**  
  Per eseguire l'istanza preconfigurata di Hyperledger Fabric, assicurati di avere installato [Docker ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.docker.com/).  
- **Requisiti relativi al sistema operativo**  
  Attualmente, questa estensione è compatibile con Mac, Windows e Linux.  
- **Requisiti relativi alla versione di Hyperledger Fabric**  
Questa estensione è compatibile con Hyperledger Fabric versioni 1.4.0 e successive.  

### Installa l'estensione
{: #develop-vscode-installing-the-extension}

1. Vai alla [pagina Marketplace relativa all'estensione per Visual Studio Code ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) oppure cerca **{{site.data.keyword.blockchainfull_notm}} Platform** nel pannello delle estensioni all'interno di Visual Studio Code.
2. Fai clic su **Installa**.
3. Riavvia Visual Studio Code per completare l'installazione dell'estensione.

Dopo l'installazione, puoi utilizzare l'icona {{site.data.keyword.blockchainfull_notm}} sul lato sinistro di VS Code per aprire il pannello {{site.data.keyword.blockchainfull_notm}} Platform.

L'estensione aggiunge anche dei nuovi comandi al riquadro comandi di Visual Studio Code. Puoi utilizzare il riquadro comandi per completare molte delle operazioni illustrate nel dettaglio in questa guida.

## Passo due: crea un progetto di smart contract
{: #develop-vscode-creating-a-project}

Puoi utilizzare l'estensione per creare un nuovo progetto di smart contract in Visual Studio Code. L'estensione crea uno smart contract di base che gestisce un asset di esempio nel linguaggio di tua scelta. Puoi utilizzare la struttura dell'esempio come punto di partenza per lo sviluppo della tua logica di business. L'estensione fornisce tutte le dipendenze necessarie per distribuire il tuo smart contract a un 'istanza di Hyperledger Fabric.

1. Apri la scheda **{{site.data.keyword.blockchainfull_notm}}**. Fai clic sul menu di overflow nel pannello dei pacchetti di smart contract e fai clic su **Create Smart Contract Project**.
2. Seleziona il linguaggio con cui vuoi creare uno smart contract. Le opzioni correnti sono JavaScript, TypeScript, Go e Java. **Nota:** {{site.data.keyword.blockchainfull_notm}} Platform non supporta il chaincode Java.
3. Seleziona un asset che deve essere gestito dal contratto di esempio. Ad esempio, ***bond***.
4. Crea una cartella con il nome del tuo progetto e aprila.
5. Seleziona come aprire il tuo nuovo progetto. La cartella del progetto dovrebbe ora aprirsi.

Quando si apre il progetto, puoi trovare il nuovo smart contract nella finestra di esplorazione nel pannello di sinistra. La struttura del progetto dipende dal linguaggio che hai selezionato. Tuttavia, ogni smart contract contiene gli stessi elementi:
- Il codice di origine dello smart contract. L'estensione crea uno smart contract di base utilizzando `fabric-contract-api` con una serie di funzioni che gestiscono il tuo asset di esempio. Ad esempio, se hai selezionato ***bond***, troverai le funzioni `createBond`, `updateBond`, `readBond`, `bondExists` e `deleteBond`.
- Un file di test.
- Le dipendenze dello smart contract di accompagnamento.

## Passo tre: impacchetta uno smart contract
{: #packaging-a-smart-contract}

Devi impacchettare uno smart contract nel formato `.cds` prima di poterlo installare sulla tua rete {{site.data.keyword.blockchainfull_notm}} Platform o sulla rete Hyperledger Fabric preconfigurata. Completa la seguente procedura per impacchettare il tuo smart contract:

1. In VS Code, vai al pannello **{{site.data.keyword.blockchainfull_notm}} Platform**. Assicurati di avere un progetto di smart contract aperto nel visualizzatore dei file. 
2. Nel riquadro **Smart Contract Packages**, fai clic sull'icona **+**. 
  - Se hai un progetto di smart contract, sarà impacchettato automaticamente e visualizzato nel pannello **Smart Contract Packages**.
  - Se hai più cartelle di smart contract aperte, ti verrà richiesto di indicare quella da assemblare.
  - Se non hai alcuna cartella di smart contract aperta, otterrai un messaggio di errore.

### Esportazione, importazione ed eliminazione di un pacchetto di smart contract
{: #develop-vscode-exporting-deleting-smart-contract-package}

Dopo aver impacchettato un progetto di smart contract, puoi esportarlo da VS Code:

1. Nel pannello dell'estensione {{site.data.keyword.blockchainfull_notm}} Platform, fai clic con il tasto destro sul pacchetto di smart contract e seleziona **Export Package**.
2. Scegli la directory per salvare il tuo file di smart contract e fai clic su **Export**.

Puoi anche importare un pacchetto di smart contract esistente nel pannello {{site.data.keyword.blockchainfull_notm}} Platform:

1. Nel pannello **Smart Contract Packages**. Fai clic sul menu di overflow e seleziona **Import Package**.
2. Seleziona il pacchetto di smart contract che vuoi importare e fai clic su **Import**.

Puoi anche fare clic su **Delete Package** per rimuovere il pacchetto di smart contract dall'elenco di pacchetti.

## Passo quattro: distribuisci uno smart contract a una rete Hyperledger Fabric preconfigurata
{: #develop-vscode-deploy}

Puoi utilizzare VS Code per distribuire il tuo smart contract a una rete Hyperledger Fabric preconfigurata che viene creata dall'estensione sulla tua macchina locale. Questo ti consente di installare, istanziare e verificare il tuo smart contract prima di distribuirlo a una rete attiva.

### Distribuzione alla rete Hyperledger Fabric preconfigurata
{: #develop-vscode-connecting-and-disconnecting}

Utilizza la seguente procedura per la distribuzione alla rete preconfigurata:

1. Assicurati che Docker sia in esecuzione sulla tua macchina.
2. Apri la scheda **{{site.data.keyword.blockchainfull_notm}} Platform** in VS Code.
3. Nel pannello **Local Fabric Ops**, fai clic su **local fabric runtime**. Se Docker è in esecuzione, l'istanza Hyperledger Fabric locale dovrebbe essere scaricata e avviata.
4. Fai doppio clic su **local_fabric** nel pannello **Fabric Gateways** per la connessione alla rete locale. Per impostazione predefinita, la connessione utilizza l'identità amministratore nel pannello dei portafogli Fabric. Puoi creare una nuova identità facendo clic con il tasto destro sul nodo dell'autorità di certificazione nel pannello **Local Fabric Ops**. Questa nuova identità può quindi essere aggiunta a un portafoglio e associata alla connessione **local_fabric**.

L'estensione VS Code crea una rete Fabric di base che include un ordinante, un peer e un'autorità di certificazione. Il peer viene unito a un canale denominato `mychannel`. Puoi trovare l'elenco di nodi, organizzazioni e canali che appartengono alla rete nel pannello **Local Fabric Ops**. Oltre questi nodi, puoi trovare l'elenco di smart contract che sono stati installati e istanziati.

### Arresto, riavvio e rimozione della rete preconfigurata
{: #develop-vscode-stop-Fabric-runtime}

Puoi arrestare o riavviare la rete preconfigurata dopo che è stata creata:

1. Nel pannello **Local Fabric Ops**, fai clic sul menu di overflow.
2. Seleziona **Restart Fabric Runtime** o **Stop Fabric Runtime** per arrestare o riavviare il contenitore.

I dettagli della connessione vengono salvati in una directory denominata **local_fabric** contenuta nella tua directory del progetto corrente. Puoi anche selezionare **Teardown Fabric Runtime** per rimuovere completamente la rete Fabric locale. **Nota:** tale rimozione comporterà la perdita dei dati del libro mastro e di stato globale.

### Distribuzione del tuo smart contract alla rete preconfigurata
{: #develop-vscode-deploy-smart-contract}

Puoi distribuire tutti i pacchetti nel pannello **Smart Contract Packages** a una rete preconfigurata in esecuzione.

Per prima cosa, devi installare lo smart contract su un peer:

1. Nel pannello **Local Fabric Ops**, fai clic su **Install Smart Contract**. 
2. Seleziona il peer su cui vuoi installare lo smart contract.
3. Seleziona il pacchetto di smart contract che vuoi installare e fai clic su **Install**.

Successivamente, puoi istanziare lo smart contract su un canale:

1. Nel pannello **Local Fabric Ops**, fai clic su **Instantiate Smart Contract**. 
2. Seleziona lo smart contract installato da istanziare.
3. (Facoltativo) Immetti il nome della funzione di istanziazione nel tuo smart contract. Se hai utilizzato il template di smart contract predefinito, non viene utilizzata alcuna funzione di istanziazione.
4. (Facoltativo) Immetti tutti gli argomenti richiesti dalla funzione di istanziazione.
5. (Facoltativo) Seleziona il tuo file di configurazione della raccolta se il tuo smart contract utilizza dei dati privati.
6. Fai clic su **Instantiate**.

Se apporti delle modifiche al tuo codice di smart contract e lo impacchetti nuovamente, puoi eseguire l'upgrade dello smart contract istanziato a una versione più recente sulla rete:

1. Assicurati che lo smart contract di cui desideri eseguire l'upgrade sia stato istanziato.
2. Installa la nuova versione dello smart contract su un peer sulla stessa rete.
3. Fai clic con il pulsante destro del mouse sullo smart contract istanziato e seleziona **Upgrade Smart Contract**.
4. (Facoltativo) Esegui una transazione dopo aver istanziato il nuovo smart contract.

### Interazione con il tuo smart contract
{: #develop-vscode-submitting-transactions}

Dopo aver installato e istanziato uno smart contract, puoi inviare delle transazioni alle funzioni all'interno dello smart contract utilizzando il pannello **Fabric Gateways**:

1. Assicurati che il tuo smart contract sia installato e istanziato e che tu sia connesso alla rete.
2. Nel riquadro delle connessioni, espandi **Instantiated Smart Contracts**.
3. Espandi lo smart contract con cui vuoi interagire. Troverai l'elenco delle transazioni sotto il tuo smart contract.
4. Fai clic con il pulsante destro del mouse sulla transazione da inoltrare e seleziona **Submit Transaction**. Ad esempio, se hai creato e impacchettato lo smart contract bonds di esempio, fai clic su **createBond**.
5. Immetti tutti gli argomenti richiesti dalla transazione e premi **Invio**. Ad esempio, immetti `["bond01","100"]` per creare il tuo primo bond.

### Connessione delle tue applicazioni alla rete preconfigurata
{: #develop-vscode-exploring-connection-details}

Puoi verificare le tue applicazioni client connettendole alla rete preconfigurata e inviando delle transazioni al tuo smart contract.

Per prima cosa, devi esportare il tuo profilo di connessione:

1. Avvia la rete ed espandi **Nodes** nel pannello **Local Fabric Ops**.
2. Fai clic con il pulsante destro del mouse e seleziona **Export Connection Profile**.

Puoi anche utilizzare gli SDK Fabric e il profilo di connessione per iscrivere la tua identità amministratore utilizzando il nome utente `admin` e la password `adminpw`. Puoi quindi utilizzare questa identità per richiamare il tuo smart contract o registrare ed iscrivere ulteriori utenti.

## Passo cinque: esegui il debug degli smart contract utilizzando la modalità di sviluppo
{: #develop-vscode-development-mode}

Puoi eseguire la rete preconfigurata nella modalità di sviluppo (**Development Mode**) per sviluppare ed eseguire il debug in modo iterattivo del tuo smart contract in locale, senza dover impacchettare nuovamente e aggiornare lo smart contract dopo ogni modifica. L'esecuzione del debug di uno smart contract ti consente di esaminarne le transazioni con punti di interruzione e output, garantendo che le transazioni funzionino come previsto.

Utilizza la seguente procedura per abilitare la modalità di sviluppo sulla rete preconfigurata:

1. Dopo aver avviato la rete, dal pannello **Local Fabric Ops** espandi la sezione **Nodes**.
2. Fai clic con il pulsante destro del mouse e seleziona **Toggle Development Mode**.

In modalità operativa normale, un peer crea e gestisce un contenitore chaincode per eseguire gli smart contract istanziati. Passando alla modalità di sviluppo, il peer consente al contenitore chaincode di essere eseguito manualmente, il che ti consente di distribuire gli aggiornamenti al tuo smart contract direttamente alla tua rete in esecuzione utilizzando la vista **Debug** di VS Code. 

1. Assicurati di essere connesso alla connessione **local_fabric** che è in modalità di sviluppo.
2. Apri il tuo progetto di smart contract.
3. Apri la vista **Debug** in VS Code dalla barra di navigazione di sinistra.
4. Seleziona **Debug Smart Contract configuration** dall'elenco a discesa in alto a sinistra.
5. Impacchetta e installa lo smart contract facendo clic sul pulsante **play**.
6. Aggiungi dei punti di interruzione allo smart contract facendo clic sui numeri riga pertinenti nei file di smart contract.
7. Nella barra degli strumenti di debug, fai clic sul pulsante **Blockchain** per istanziare lo smart contract.
8. Nella barra degli strumenti di debug, fai clic sul pulsante **Blockchain** per inviare o valutare le transazioni.

Per modificare il tuo smart contract durante l'esecuzione del debug, fai clic sul pulsante **restart** dopo aver apportato le modifiche al tuo smart contract. Riavviare il debug significa che non hai bisogno di istanziare nuovamente il contratto.

## Passo sei: verifica uno smart contract istanziato
{: #develop-vscode-testing-instantiated-smart-contract}

Puoi generare dei test per gli smart contract che sono stati istanziati sulle reti a cui sei connesso. I test possono essere generati come **JavaScript** o **TypeScript** e possono essere eseguiti o sottoposti a debug.

1. Assicurati che lo smart contract sia stato istanziato.
2. In **Instantiated Smart Contracts**, fai clic con il pulsante destro del mouse sullo smart contract per cui generare i test.
3. Seleziona **Generate Smart Contract Tests**.
4. Seleziona il linguaggio per il file di test, **JavaScript** o **TypeScript**. L'estensione {{site.data.keyword.blockchainfull_notm}} Platform installerà i moduli npm richiesti e creerà il file di test.

Dopo che il file di test è stato creato, i test possono essere eseguiti facendo clic sul pulsante **Run Tests** nel file.


## Passo sette: connessione alla tua rete {{site.data.keyword.blockchainfull_notm}} Platform
{: #develop-vscode-connecting-ibp}

Puoi utilizzare l'estensione anche per la connessione a {{site.data.keyword.blockchainfull_notm}} Platform e richiamare tutti gli smart contract installati e istanziati utilizzando l'IU della console {{site.data.keyword.blockchainfull_notm}} Platform.

Apri la console {{site.data.keyword.blockchainfull_notm}} Platform associata alla tua istanza di {{site.data.keyword.blockchainfull_notm}} Platform. Vai alla scheda **Smart contract**. Utilizza la tabella **Smart contract istanziati** nella scheda smart contract per scaricare il tuo [profilo di connessione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) nel file system locale. Quindi, [crea un'identità dell'applicazione](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) utilizzando la tua CA e salva il segreto e l'ID di iscrizione. Segui questa procedura per la connessione a {{site.data.keyword.blockchainfull_notm}} Platform da VS Code.

1. Apri la scheda _{{site.data.keyword.blockchainfull_notm}} Platform_.
2. Nel pannello **Fabric Gateways**, fai clic su **+**.
3. Immetti un nome per la connessione.
4. Immetti il percorso file completo del tuo profilo di connessione. La tua connessione dovrebbe ora comparire nell'elenco di connessioni sotto **local_fabric**.
5. Nel pannello **Fabric Wallets**, fai clic su **+**.
6. Scegli **Create a new wallet and add an identity** dalle opzioni. Fornisci un nome per il tuo portafoglio e la tua identità.
7. Immetti l'ID MSP della tua organizzazione.
8. Seleziona l'opzione **Select a gateway and provide an enrollment ID and secret** e scegli il gateway che hai creato in precedenza.
9. Immetti il segreto e l'ID di iscrizione dell'identità dell'applicazione che hai creato con la console. Sarà creata una nuova identità nel pannello **Fabric Wallets**.
10. Puoi ora eseguire la connessione alla tua istanza della tua rete {{site.data.keyword.blockchainfull_notm}} Platform. Fai doppio clic sul nome della connessione e seleziona il nome del portafoglio che hai appena creato. Puoi anche associare il portafoglio che hai creato al gateway facendo clic con il tasto destro sul gateway e selezionando **Associate A Wallet**. Questo permette alla connessione di utilizzare ogni volta lo stesso portafoglio quando si connette.

Dopo esserti connesso a {{site.data.keyword.blockchainfull_notm}} Platform da VS Code, puoi visualizzare l'elenco di canali a cui si sono uniti i peer della tua organizzazione nel gateway. In ogni canale, puoi visualizzare l'elenco di smart contract istanziati su ogni canale e le funzioni all'interno di ogni smart contract. Puoi inviare le transazioni alla tua rete facendo clic con il tasto destro su una funzione e selezionando **Submit Transaction** e passando gli argomenti richiesti. Puoi anche generare un file di test per gli smart contract istanziati sui tuoi canali.

### Aggiunta di portafogli e utenti
{: #develop-vscode-add-a-wallet}

Utilizza la seguente procedura per creare un nuovo portafoglio utilizzando un certificato e una chiave privata:

1. Nel pannello **Fabric Wallets**, fai clic su **+**.
2. Scegli **Create a new wallet and add an identity** dalle opzioni. Fornisci un nome per il tuo portafoglio e la tua identità.
3. Immetti l'ID MSP della tua organizzazione.
4. Scegli di aggiungere un certificato e una chiave privata.
5. Se utilizzi un certificato e una chiave privata, selezionali.

Puoi anche aggiungere dei nuovi utenti ai portafogli che sono stati già creati:

1. Nel pannello **Fabric Wallets**, fai clic con il tasto destro su un portafoglio e seleziona **Add Identity**.
2. Fornisci un nome per l'identità e un ID MSP.
3. Scegli di utilizzare un certificato e una chiave privata o un segreto e un ID di iscrizione.
4. Se utilizzi un certificato e una chiave privata, selezionali.
5. Se utilizzi un segreto e un ID di iscrizione, scegli il gateway con cui eseguire l'iscrizione e immetti il segreto e l'ID di iscrizione.

### Modifica, disconnessione ed eliminazione delle connessioni
{: #develop-vscode-editing-connection}

Dall'estensione, fai clic con il tasto destro su una connessione nell'angolo in basso a sinistra per aprire un menu contestuale con le opzioni per aggiungere un'identità, modificare o eliminare la connessione.

Per modificare una connessione, completa la seguente procedura:
1. Seleziona l'opzione **Edit connection**. Viene aperta la pagina **User Settings**, con i dettagli della connessione evidenziati.
2. Apporta tutte le modifiche di cui hai bisogno e salva la pagina delle impostazioni.

Quando sei pronto per disconnetterti dalla rete, fai clic sull'icona **Disconnect** nell'angolo in alto a destra del pannello **Fabric Gateways**.

Per eliminare una connessione, fai clic con il tasto destro sulla connessione e seleziona **Delete Gateway**.

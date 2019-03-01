---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Strumenti per smart contract
{: #develop-vscode}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


L'estensione {{site.data.keyword.blockchainfull}} Platform Visual Studio Code fornisce un ambiente in Visual Studio Code per sviluppare, assemblare e distribuire pacchetti di smart contract. L'estensione include anche dei comandi per impostare un'istanza locale preconfigurata di Hyperledger Fabric per uno sviluppo di smart contract locale semplificato.

**Nota:** l'estensione {{site.data.keyword.blockchainfull_notm}} Platform è compatibile con le reti piano Starter di {{site.data.keyword.blockchainfull_notm}} Platform e con Hyperledger Fabric versioni 1.3.x e successive.

## Prerequisiti
{: #develop-vscode-prerequisites}

Prima di installare l'estensione {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code, completa i seguenti prerequisiti.

- **Installa Yeoman**

  Yeoman è uno strumento generatore utilizzato per creare progetti smart contract di base. Installa Yeoman utilizzando il seguente comando: `npm install -g yo`

- **Installa il generatore Fabric per Yeoman**

  Dopo aver installato Yeoman, installa il generatore Fabric per creare pacchetti di smart contract di base. Installa il generatore Fabric utilizzando il seguente comando: `npm install -g generator-fabric`

- **Installa Docker**

  Per eseguire l'istanza preconfigurata di Hyperledger Fabric, assicurati di avere installato [Docker ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.docker.com/).

- **Requisiti relativi al sistema operativo**

  Attualmente, questa estensione è compatibile con Mac, Windows e Linux.

- **Requisiti relativi alla versione di Hyperledger Fabric**

  Questa estensione è compatibile con Hyperledger Fabric versioni 1.3.0 e successive.

- **Requisiti relativi alla versione di Hyperledger Fabric**

  Questa estensione è compatibile con Hyperledger Fabric versioni 1.3.0 e successive.

## Installazione dell'estensione
{: #develop-vscode-installing-the-extension}

1. Vai alla [pagina Marketplace relativa all'estensione per Visual Studio Code ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) oppure cerca **{{site.data.keyword.blockchainfull_notm}} Platform** nel pannello delle estensioni all'interno di Visual Studio Code.
2. Fai clic su **Installa**.
3. Riavvia Visual Studio Code per completare l'installazione dell'estensione.

## Riquadro comandi di Visual Studio Code
{: #develop-vscode-command-palette}

Questa estensione aggiunge molti comandi al riquadro comandi di Visual Studio Code. Molte delle operazioni descritte in questo documento possono essere avviate anche eseguendo il comando dal riquadro comandi di Visual Studio Code.

## Creazione di un progetto
{: #develop-vscode-creating-a-project}

Per creare un nuovo progetto, completa la seguente procedura:

1. Apri il riquadro comandi ed esegui il comando **Blockchain: Create Smart Contract Project**.
2. Seleziona il linguaggio in cui desideri creare uno smart contract; attualmente le opzioni sono JavaScript o TypeScript.
3. Crea una cartella con il nome del tuo progetto e aprila.
4. Seleziona come aprire il tuo nuovo progetto. La cartella del progetto dovrebbe ora aprirsi.

## Connessione a, e disconnessione da, una rete
{: #develop-vscode-connecting-and-disconnecting}

Quando utilizzi questa estensione, installi e istanzi pacchetti di smart contract su peer e canali nella tua istanza Hyperledger Fabric. Questa estensione può inizializzare un'istanza locale preconfigurata di Hyperledger Fabric utilizzando Docker.

### Connessione all'istanza Hyperledger Fabric preconfigurata
{: #develop-vscode-connecting-to-preconfigured-Fabric-instance}

Per stabilire una connessione all'istanza Hyperledger Fabric preconfigurata, assicurati prima che Docker sia in esecuzione sulla tua macchina:

1. Apri la scheda **{{site.data.keyword.blockchainfull_notm}} Platform** in Visual Studio Code.
2. Nel riquadro **{{site.data.keyword.blockchainfull_notm}} Platform**, fai clic su **local_fabric**. Se Docker è in esecuzione, l'istanza Hyperledger Fabric locale dovrebbe essere scaricata e avviata.
3. Dopo che l'istanza Hyperledger Fabric locale è stata avviata, fai doppio clic su **local_fabric** per stabilire una connessione ad essa. Dovresti ora vedere un canale denominato `mychannel`.

#### Riavvio del runtime Hyperledger Fabric preconfigurato
{: #develop-vscode-restarting-Fabric-runtime}

Per riavviare il runtime `local_fabric`:

1. Fai clic con il pulsante destro del mouse sulla connessione `local_fabric` dopo che è stata stabilita.
2. Seleziona **Restart Fabric Runtime**.

I contenitori Fabric Hyperledger verranno arrestati e riavviati.

#### Esegui il teardown del runtime Hyperledger Fabric
{: #develop-vscode-teardown-Fabric}

Per eseguire il teardown del runtime `local_fabric`;

1. Fai clic con il pulsante destro del mouse sulla connessione `local_fabric` dopo che è stata stabilita.
2. Seleziona **Teardown Fabric Runtime**.

L'esecuzione di un teardown della rete `local_fabric` chiuderà tutti i contenitori Hyperledger Fabric. **Nota**: tale operazione produrrà la perdita dei dati del libro mastro e di stato globale.

#### Abilitazione della modalità di sviluppo sul runtime Hyperledger Fabric preconfigurato
{: #develop-vscode-enabling-development-mode-Fabric}

In modalità operativa normale, un peer creerà e gestirà un contenitore chaincode per eseguire gli smart contract istanziati. Passando alla modalità di sviluppo, il peer consente al contenitore chaincode di essere eseguito manualmente. L'esecuzione manuale del contenitore chaincode, sulla riga di comando oppure in un terminale, assiste nello sviluppo iterativo e nell'esecuzione di debug di smart contract.

Per abilitare la modalità di sviluppo con il runtime Hyperledger Fabric preconfigurato:

1. Fai clic con il pulsante destro del mouse sulla connessione `local_fabric` dopo che è stata stabilita.
2. Seleziona **Toggle Development Mode**.

Per utilizzare la funzione di debug dell'estensione, la modalità di sviluppo deve essere abilitata.

### Connessione al piano Starter di {{site.data.keyword.blockchainfull_notm}} Platform
{: #develop-vscode-connecting-to-Starter-Plan}

L'estensione {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code può essere utilizzata per stabilire una connessione a un'istanza del piano Starter di {{site.data.keyword.blockchainfull_notm}} Platform. Una volta stabilita la connessione, l'estensione può essere utilizzata per sviluppare e distribuire smart contract.

**Nota:** attualmente, l'estensione non è compatibile con il piano Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform.

1. Se non hai un'istanza del piano Starter di {{site.data.keyword.blockchainfull_notm}} Platform, [creane una ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain).
2. Apri l'IU {{site.data.keyword.blockchainfull_notm}} Platform facendo clic su **Avvia**.
3. Richiama il tuo profilo di connessione facendo clic su **Panoramica** > **Profilo di connessione** > **Scarica**.
4. Genera i certificati di amministrazione richiesti facendo clic su **CA (Certificate Authority)** > **Genera certificato** e salvando il certificato e la chiave privata nel tuo file system.
5. Aggiungi i certificati alla tua istanza del piano Starter di {{site.data.keyword.blockchainfull_notm}} Platform facendo clic su **Membri** > **Certificati** > **Aggiungi certificato** e selezionando il certificato creato nel passo precedente.
6. In Visual Studio Code, apri la vista dell'estensione {{site.data.keyword.blockchainfull_notm}} Platform e fai clic su **Add new connection**.
7. Immetti il nome della connessione, il percorso al profilo della connessione e seleziona un [portafoglio ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") di file system esistente oppure crea un nuovo portafoglio utilizzando un certificato e una chiave privata.

### Connessione alla tua istanza Hyperledger Fabric
{: #develop-vscode-connecting-to-own-Fabric-instance}

Utilizzando questa estensione, puoi stabilire una connessione a un'istanza locale preconfigurata di Hyperledger Fabric oppure puoi stabilire una connessione alla tua istanza Hyperledger Fabric. Se scegli di stabilire una connessione alla tua istanza Fabric, deve essere Fabric 1.3.0 o successiva.

Per stabilire una connessione alla tua istanza Hyperledger Fabric, completa la seguente procedura:

1. Apri la scheda **{{site.data.keyword.blockchainfull_notm}} Platform** in Visual Studio Code.
2. Nel riquadro **{{site.data.keyword.blockchainfull_notm}} Platform**, fai clic su **Add new connection**.
3. Immetti un nome per la connessione. Questo nome verrà visualizzato nel riquadro **{{site.data.keyword.blockchainfull_notm}} Platform**.
4. Immetti il percorso file completo del tuo profilo di connessione Hyperledger Fabric.
5. Seleziona un [portafoglio ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") di file system esistente oppure crea un nuovo portafoglio di file system immettendo i percorsi file del certificato e della chiave privata per la tua identità fabric.
6. La tua connessione dovrebbe comparire nell'elenco di connessioni sotto `local_fabric`. Fai doppio clic sul nome connessione per stabilire la connessione.

Se non hai un'istanza Hyperledger Fabric preesistente della versione 1.3.0 o successiva, puoi stabilire la connessione utilizzando l'istanza Hyperledger Fabric preconfigurata oppure eseguire i seguenti comandi per eseguire il pull e contrassegnare con tag le immagini versione 1.3.0:

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### Disconnessione da una rete
{: #develop-vscode-disconnecting}

Quando sei connesso a una rete, puoi chiudere la connessione facendo clic sull'icona di disconnessione nell'angolo superiore destro del riquadro Blockchain Connections. Puoi anche eseguire la disconnessione eseguendo il comando **Disconnect from a blockchain** dal riquadro comandi.

## Modifica o eliminazione di una connessione
{: #develop-vscode-editing-or-deleting-connection}

Le connessioni possono essere modificate o eliminate nel riquadro delle connessioni.

### Modifica di una connessione
{: #develop-vscode-editing-connection}

Modificando una connessione, puoi modificare il percorso file per il profilo di connessione, il nome dato alla connessione e i percorsi file ai certificati d'identità e alle chiavi private.

Per modificare una connessione:

1. Dall'estensione, fai clic con il pulsante destro del mouse sulla connessione da modificare nell'angolo inferiore sinistro; questo apre un menu contestuale con le opzioni per aggiungere un'identità, modificare la connessione o eliminare la connessione.
2. Seleziona **Edit connection**.
3. Verrà aperta la pagina **User Settings**, con i dettagli della connessione evidenziati.
4. Apporta eventuali modifiche e salva la pagina delle impostazioni.

### Eliminazione di una connessione
{: #develop-vscode-deleting-connection}

Le connessioni possono essere eliminate nei seguenti modi:

1. Dall'estensione, fai clic con il pulsante destro del mouse sulla connessione da modificare nell'angolo inferiore sinistro; questo apre un menu contestuale con le opzioni per aggiungere un'identità, modificare la connessione o eliminare la connessione.
2. Seleziona **Delete connection**.
3. Viene visualizzata una finestra di dialogo per confermare l'eliminazione della connessione. Fai clic su **Yes**.

La connessione verrà eliminata.

## Aggiunta di identità
{: #develop-vscode-adding-identities}

Quando aggiungi una connessione, devi selezionare un portafoglio esistente contenente l'identità da utilizzare oppure creare un nuovo portafoglio utilizzando il certificato e la chiave privata per un'identità. Per ulteriori informazioni sui portafogli, vedi l'[argomento relativo ai portafogli ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") nella documentazione di Fabric.

Per aggiungere un'identità ad una connessione già stabilita:

1. Dall'estensione, fai clic con il pulsante destro del mouse sulla connessione da modificare nell'angolo inferiore sinistro; questo apre un menu contestuale con le opzioni per aggiungere un'identità, modificare la connessione o eliminare la connessione.
2. Seleziona **Add an identity**.
3. Seleziona un portafoglio esistente contenente l'identità da aggiungere oppure crea un nuovo portafoglio fornendo i percorsi file dei certificati e della chiave privata.

## Esplorazione delle connessioni
{: #develop-vscode-exploring-connections}

Dopo aver stabilito una connessione a un'istanza Hyperledger Fabric, puoi vedere un elenco dei canali e dei peer disponibili nel riquadro **{{site.data.keyword.blockchainfull_notm}} Platform**. Dedica un minuto all'esplorazione della tua istanza Hyperledger Fabric per acquisire dimestichezza con la struttura. Sono elencati per primi i canali all'interno di una connessione; sotto un canale è presente un elenco dei peer membri di tale canale e un elenco dei pacchetti di smart contract istanziati su tale canale. Sotto ciascun peer nell'elenco è presente un elenco di tutti i pacchetti di smart contract installati su tale peer. Puoi anche vedere le versioni dei pacchetti di smart contract installati o istanziati.

### Esportazione dei dettagli della connessione per il runtime Hyperledger Fabric preconfigurato
{: #develop-vscode-exploring-connection-details}

I dettagli della connessione richiesti per stabilire una connessione alla connessione `local_fabric` possono essere esportati. I dettagli della connessione per la connessione `local_fabric` sono utili per eseguire test delle applicazioni client che è previsto si connettano alla tua istanza Hyperledger Fabric o che interagiscano con essa.

Per esportare i dettagli della connessione `local_fabric`:

1. Avvia il runtime Hyperledger Fabric preconfigurato.
2. Fai clic con il pulsante destro del mouse sulla connessione `local_fabric` e seleziona **Export Connection Details**.

I dettagli della connessione vengono salvati in una directory denominata `local_fabric` contenuta nella tua directory del progetto corrente.

## Assemblaggio di uno smart contract
{: #packaging-a-smart-contract}

Quando uno smart contract è pronto per essere distribuito, è necessario prima assemblarlo. Per assemblare uno smart contract, completa la seguente procedura:

1. In Visual Studio Code, vai al pannello **{{site.data.keyword.blockchainfull_notm}} Platform**.
2. Nel riquadro **Smart Contract Packages**, fai clic sull'icona +. Se hai un progetto di smart contract aperto nel visualizzatore dei file, verrà automaticamente assemblato e verrà visualizzato nel riquadro **Smart Contract Packages**. Se hai più cartelle di smart contract aperte, ti verrà richiesto di indicare quella da assemblare. Se non hai alcuna cartella di smart contract aperta, otterrai un messaggio di errore.

## Installazione di pacchetti di smart contract
{: #develop-vscode-installing-smart-contract-packages}

Dopo aver stabilito una connessione a un'istanza di Hyperledger Fabric, puoi installare e istanziare pacchetti di smart contract sui peer.

1. Nel riquadro {{site.data.keyword.blockchainfull_notm}} Platform, vai al peer su cui vuoi installare il pacchetto di smart contract.
2. Fai clic con il pulsante destro del mouse sul peer su cui installare il pacchetto di smart contract e seleziona **Install Smart Contract**.

## Istanziazione dei pacchetti di smart contract
{: #develop-vscode-instantiating-smart-contract-packages}

per iniziare l'esecuzione su un canale, un pacchetto di smart contract installato deve prima essere istanziato.

1. Stabilisci una connessione all'istanza Hyperledger Fabric dove è installato il pacchetto di smart contract.
2. Fai clic con il pulsante destro del mouse sul canale su cui desideri istanziare il pacchetto di smart contract e fai clic su **Instantiate Smart Contract**.
3. Seleziona il pacchetto di smart contract e la versione da istanziare. Il pacchetto di smart contract deve essere installato su un peer che è membro di questo canale.
4. Immetti il nome della funzione di istanziazione nel tuo smart contract.
5. Immetti gli eventuali argomenti richiesti dalla tua funzione di istanziazione.

## Esportazione o eliminazione di un pacchetto di smart contract
{: #develop-vscode-exporting-deleting-smart-contract-package}

Dopo essere stato assemblato, uno smart contract può essere esportato come un file `.cds` o eliminato se non è più necessario.

Per eliminare un pacchetto di smart contract:

1. Nel pannello dell'estensione {{site.data.keyword.blockchainfull_notm}} Platform, fai clic con il pulsante destro del mouse sul pacchetto di smart contract da eliminare.
2. Seleziona **Delete Package**.

Il pacchetto verrà ora eliminato e sparirà dall'elenco di pacchetti di smart contract.

Per esportare un pacchetto di smart contract:

1. Nel pannello dell'estensione {{site.data.keyword.blockchainfull_notm}} Platform, fai clic con il pulsante destro del mouse sul pacchetto di smart contract da esportare.
2. Seleziona **Export Package**.
3. Scegli la directory per salvare il tuo file di smart contract e fai clic su **Export**.

## Test di uno smart contract istanziato
{: #develop-vscode-testing-instantiated-smart-contract}

I test per uno smart contract possono essere generati dopo che lo smart contract è stato istanziato. I test possono essere generati come JavaScript o TypeScript e possono essere eseguiti o sottoposti a debug.

Per generare i test di smart contract:

1. Assicurati che lo smart contract sia stato istanziato.
2. In **Instantiated Smart Contracts**, fai clic con il pulsante destro del mouse sullo smart contract per cui generare i test.
3. Seleziona **Generate Smart Contract Tests**.
4. Ora seleziona il linguaggio per il file di test, **JavaScript** o **TypeScript**. l'estensione {{site.data.keyword.blockchainfull_notm}} Platform installerà ora i moduli npm richiesti e creerà il file di test.

Dopo che il file di test è stato creato, i test possono essere eseguiti facendo clic sul pulsante **Run Tests** nel file.

## Esecuzione del debug di uno smart contract utilizzando il runtime Hyperledger Fabric preconfigurato
{: #develop-vscode-debugging}

La possibilità di eseguire il debug di uno smart contract localmente aiuta uno sviluppatore di smart contract ad eseguire iterazioni sulle funzioni del loro smart contract e di correggere i bug prima di provare a richiamare le funzioni dopo l'istanziazione. L'esecuzione del debug di uno smart contract ti consente di esaminarne le transazioni con punti di interruzione e output, garantendo che le transazioni funzionino come previsto. Per eseguire il debug del tuo smart contract:

1. Assicurati di essere connesso alla connessione `local_fabric` che è in modalità di sviluppo.
2. Apri il tuo progetto di smart contract.
3. Apri la vista di debug in Visual Studio Code utilizzando la barra di navigazione a sinistra.
4. Seleziona la configurazione Debug Smart Contract utilizzando il menu a discesa nell'angolo superiore sinistro.
5. Assembla e installa lo smart contract facendo clic sul pulsante **play**.
6. Aggiungi dei punti di interruzione allo smart contract facendo clic sui numeri riga pertinenti nei file di smart contract.
7. Fai clic con il pulsante destro del mouse sullo smart contract installato e seleziona **Instantiate**. Puoi ora fare clic con il pulsante destro del mouse per inoltrare le transazioni; l'esecuzione verrà messa in pausa sui punti di interruzione definiti.

Per apportare modifiche al tuo smart contract durante l'esecuzione del debug, fai clic sul pulsante **restart** dopo aver apportato le modifiche al tuo smart contract. Riavviare il debug significa che non hai bisogno di istanziare nuovamente il contratto.

**Nota**: il riavvio del debug archivia lo smart contract nella memoria locale; per un ampio numero di modifiche a smart contract di grandi dimensioni, potresti dover reistanziare lo smart contract.

## Upgrade di uno smart contract istanziato
{: #develop-vscode-upgrading-instantiated-smart-contract}

Dopo che uno smart contract è stato installato su un peer e istanziato su un canale, è possibile eseguirne l'upgrade per distribuire una sua versione più recente.

1. Assicurati che lo smart contract di cui desideri eseguire l'upgrade sia istanziato.
2. Installa la nuova versione dello smart contract su un peer sulla stessa rete.
3. Fai clic con il pulsante destro del mouse sullo smart contract istanziato e seleziona **Upgrade Smart Contract**.
4. Facoltativamente, scegli una transazione da eseguire dopo che il nuovo smart contract è stato istanziato.

## Inoltro di transazioni
{: #develop-vscode-submitting-transactions}

Dopo che uno smart contract è stato installato e istanziato, le transazioni possono essere inoltrate dal riquadro delle connessioni nel pannello dell'estensione {{site.data.keyword.blockchainfull_notm}} Platform.

Per inoltrare una transazione:

1. Assicurati che il tuo smart contract sia installato e istanziato e che tu sia connesso alla rete.
2. Nel riquadro delle connessioni, espandi **Instantiated Smart Contracts**.
3. Espandi lo smart contract che contiene la transazione da inoltrare.
4. Fai clic con il pulsante destro del mouse sulla transazione da inoltrare e seleziona **Submit Transaction**.
5. Immetti gli eventuali argomenti richiesti dalla transazione e premi **Invio**.

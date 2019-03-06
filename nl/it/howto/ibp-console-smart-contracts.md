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

# Esercitazione di distribuzione di uno smart contract sulla rete
{: #ibp-console-smart-contracts}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Uno smart contract consiste in codice, a volte indicato come chaincode, che ti consente di leggere e aggiornare i dati nel libro mastro della blockchain. Uno smart contract può trasformare la logica di business in un programma eseguibile accettato e verificato da tutti i membri di una rete blockchain. Questa esercitazione è la terza parte nella [serie di esercitazioni di rete di esempio](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) e descrive come distribuire gli smart contract per avviare le transazioni nella rete blockchain. Le due esercitazioni precedenti descrivono come creare componenti nella console {{site.data.keyword.blockchainfull_notm}} Platform e connetterli ai componenti creati in altri cluster per creare una rete blockchain veramente **distribuita**.
{:shortdesc}

Gli smart contract sono installati sui peer e quindi istanziati sui canali. **Tutti i membri che desiderano inoltrare transazioni o leggere dati utilizzando uno smart contract devono installare il contratto sul loro peer.** Uno smart contract è definito dal suo nome e dalla sua versione. Pertanto, sia il nome che la versione del contratto installato devono essere congruenti tra tutti i peer sul canale che intendono eseguire lo smart contract.

Dopo che uno smart contract è stato installato sui peer, un singolo membro della rete istanzia il contratto sul canale. Per eseguire questa azione, è necessario che il membro della rete abbia aderito al canale. L'istanziazione aggiorna il libro mastro con i dati iniziali utilizzati dallo smart contract e avvia quindi i contenitori smart contract sui peer uniti al canale che hanno il contratto installato. I peer possono quindi utilizzare i contenitori in esecuzione per effettuare transazioni.  
- **Solo un singolo membro della rete deve istanziare uno smart contract.**
- **Se un peer con uno smart contract installato si unisce a un canale in cui è già stata istanziata la stessa versione di smart contract, il contenitore smart contract viene avviato automaticamente.**  

In questa esercitazione, vedremo la procedura per utilizzare la console {{site.data.keyword.blockchainfull_notm}} Platform per gestire la distribuzione di smart contract sulla tua rete.

- [Installa gli smart contract sui tuoi peer](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install).
- [Istanziali sui canali](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).
- [Specifica le politiche di approvazione](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse).
- [Esegui l'upgrade del codice e delle politiche di smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).


**Prima di cominciare**

Prima di poter installare uno smart contract, assicurati di avere pronto quanto segue.

- Devi [creare una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) o [unirti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) utilizzando la tua console {{site.data.keyword.blockchainfull_notm}} Platform.
- Poiché gli smart contract sono installati sui peer, assicurati di [aggiungere i peer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1) alla tua console.  
- Gli smart contract sono istanziati su un canale. Devi pertanto [creare un canale](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) o [unirti a un canale](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) utilizzando la tua console.

## Passo uno: Scrivi uno smart contract

La console {{site.data.keyword.blockchainfull_notm}} gestisce la *distribuzione* degli smart contract piuttosto che lo sviluppo. Se sei interessato a sviluppare degli smart contract, puoi iniziare utilizzando le esercitazioni fornite dalla community di Hyperledger Fabric e la strumentazione fornita da {{site.data.keyword.IBM_notm}}.

- Per ulteriori informazioni su come è possibile utilizzare gli smart contract per condurre delle transazioni tra più parti, vedi l'[argomento relativo allo sviluppo di applicazioni ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Argomento relativo allo sviluppo di applicazioni") nella documentazione di Hyperledger Fabric.
- Per un'esercitazione end-to-end completa relativa all'utilizzo di un'applicazione per interagire con gli smart contract, vedi l'[esercitazione Commercial paper di Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "esercitazione Commercial paper di Hyperledger Fabric").
- Per ulteriori informazioni su come incorporare i meccanismi di controllo dell'accesso nel tuo smart contract, vedi il documento relativo al [chaincode per gli sviluppatori ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "chaincode per gli sviluppatori").
- Quando sei pronto a iniziare a creare degli smart contract, puoi utilizzare l'[estensione {{site.data.keyword.blockchainfull_notm}} Visual Studio Code ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") per iniziare a creare il tuo progetto di smart contract. Puoi anche utilizzare tale estensione per [connetterti direttamente alla tua rete da Visual Studio Code](docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode).
- Quando sei pronto ad eseguire l'installazione, lo smart contract deve essere impacchettato in [formato .cds ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "impacchettamento di smart contract") in modo che possa essere installato sui peer. Per ulteriori informazioni, vedi [Impacchettamento di smart contract](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract).
<!-- Update the tutorial link to release1-4 when it is published -->


## Passo 2: Installa uno smart contract
{: #ibp-console-smart-contracts-install}

Utilizza la tua console per eseguire i seguenti passi:

1. Fai clic sulla scheda **Smart contract** per installare uno o più smart contract.
2. Fai clic su **Installa smart contract** per caricare il file di pacchetto di smart contract in [formato .cds ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "impacchettamento di smart contract").
Puoi utilizzare l'estensione {{site.data.keyword.blockchainfull_notm}} Visual Studio Code per [creare un pacchetto di smart contract](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). Quando installi il pacchetto dalla scheda **Smart contract**, puoi selezionare uno o più nodi peer su cui installare gli smart contract.

Se nella console esiste solo un singolo peer, lo smart contract verrà installato su di esso. Non ti viene richiesto di selezionare un peer su cui installare lo smart contract. Puoi andare alla scheda dei nodi e fare clic su un peer gestito dalla tua console per visualizzare l'elenco degli smart contract che sono stati installati su un singolo peer.

Puoi ritornare alla scheda **Smart contract** successivamente per installare lo smart contract su ulteriori peer, anche dopo che lo smart contract è stato istanziato sul canale. Se la versione dello smart contract che installi è uguale alla versione istanziata, questi peer aggiuntivi possono elaborare le transazioni proprio come i peer esistenti.
{:tip}

**Questa console non può essere utilizzata per installare i file `.bna` di Hyperledger Composer.**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode" "peer chaincode").  -->

## Passo tre: Istanzia uno smart contract
{: #ibp-console-smart-contracts-instantiate}

Gli smart contract sono istanziati su un canale. Qualsiasi membro della console con i peer uniti a un canale può istanziare uno smart contract e specificare la [politica di approvazione](/docs/services/blockchain/glossary.html#glossary-endorsement-policy) associata.

Utilizza la tua console per eseguire i seguenti passi:

1. Nella scheda degli smart contract, trova lo smart contract dall'elenco di quelli installati sui tuoi peer e fai clic su **Istanzia** dal menu di overflow sul lato destro della riga.
2. Nel pannello laterale che viene aperto, seleziona un canale, `channel1`, su cui istanziare lo smart contract e seleziona l'ordinante dove risiede il canale, `Ordinante`, se ti stai attenendo all'esercitazione. Fai clic su **Avanti**.
3. Specifica la [politica di approvazione per lo smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse) descritta nella seguente sezione.
4. Devi anche selezionare i membri dell'organizzazione da includere nella politica. Se ti stai attenendo all'esercitazione, si tratterà di `org1msp` e forse di `org2msp` se hai completato entrambe le esercitazioni **Crea una rete** e **Unisciti a una rete**.
5. Nell'ultimo pannello ti viene chiesto di specificare la funzione smart contract che desideri eseguire quando viene avviato lo smart contract, insieme agli argomenti associati da passare a tale funzione.

Puoi visualizzare tutti gli smart contract che sono stati istanziati su un canale facendo clic sull'icona di canale nella navigazione di sinistra, selezionando un canale dalla tabella e facendo quindi clic sulla scheda **Dettagli del canale**.

Tieni presente che, se utilizzi un cluster del servizio {{site.data.keyword.cloud_notm}} Kubernetes gratuito, l'istanziazione può richiedere molto più tempo che in un cluster a pagamento. A seconda del numero di peer che hai distribuito nel tuo cluster, questa operazione può richiedere diversi minuti.

Se il tuo smart contract include una definizione di raccolta di dati privati, lo smart contract non può essere istanziato dalla console {{site.data.keyword.blockchainfull_notm}}. Vedi questi [suggerimenti](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) relativi alla modalità di istanziazione di uno smart contract che include dei dati privati.
{:note}

La combinazione di **installazione e istanziazione** è una potente funzione perché consente a un peer di utilizzare un singolo smart contract su molti canali. I peer possono volersi unire a più canali che utilizzano lo stesso smart contract ma con insiemi di membri della rete differenti in grado di accedere ai dati. Un peer può installare lo smart contract una sola volta e utilizzare quindi lo stesso contenitore smart contract su qualsiasi canale dove è stato istanziato. Questo approccio leggero consente di risparmiare spazio di calcolo e di archiviazione e ti aiuta a ridimensionare la tua rete.

## Passo quattro: Invia le transazioni utilizzando le tue applicazioni client
{: #ibp-console-smart-contracts-connect-to-SDK}

Dopo che uno smart contract è stato istanziato su un canale, puoi utilizzare un'applicazione client per eseguire transazioni con altri membri della tua rete. Le applicazioni possono richiamare la logica di business contenuta negli smart contract per creare, trasferire o aggiornare gli asset nel libro mastro blockchain.

### Connetti a SDK
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
La scheda **Smart contract** contiene le informazioni di cui hai bisogno per connetterti a uno smart contract istanziato da un'applicazione client. Accanto a ogni smart contract istanziato, vai al menu di overflow. Fai clic sul pulsante **Connetti con il tuo SDK**. Viene aperto un pannello laterale che fornisce le informazioni di cui hai bisogno per connetterti a questo smart contract: il nome del contratto, il nome del canale e il tuo profilo di connessione. Per ulteriori informazioni, vedi [Creazione di applicazioni](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Specifica di una politica di approvazione
{: #ibp-console-smart-contracts-endorse}

Ogni smart contract deve avere una politica di approvazione, che viene specificata durante l'istanziazione. La politica di approvazione specifica l'insieme di organizzazioni, i peer, su un canale che possono eseguire lo smart contract e convalidano indipendentemente l'output delle transazioni. Ad esempio, una politica di approvazione può specificare che una transazione viene aggiunta al libro mastro solo se la maggioranza dei membri del canale la approva. L'organizzazione che istanzia lo smart contract può selezionare tra i membri del canale che hanno installato lo smart contract perché diventino validatori e imposta la politica di approvazione per tutti i membri del canale. Puoi aggiornare la tua politica di approvazione attenendoti alla procedura per l'[aggiornamento del tuo smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).

Quando ti attieni alla procedura per [istanziare uno smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate), puoi utilizzare il pannello laterale per impostare la politica di approvazione di un contratto dopo aver selezionato il canale. Utilizza questo pannello per specificare una semplice politica selezionando i peer che devono approvare la transazione dall'elenco di peer che hanno installato lo smart contract sul canale. Puoi utilizzare questo metodo per specificare una politica di approvazione di tutti i membri del canale, una loro maggioranza, un singolo membro oppure un semplice +1 per evitare che i membri firmino per se stessi. La politica di approvazione predefinita viene impostata su `1 of x`, il che significa che solo un singolo membro deve approvare una transazione di smart contract.

Fai clic sul pulsante **Avanzate** se desideri specificare una politica in formato JSON. Puoi utilizzare questo metodo per specificare delle politiche di approvazione più complicate, come ad esempio la richiesta che uno specifico membro del canale debba convalidare una transazione, insieme a una maggioranza degli altri membri. Puoi trovare ulteriori [esempi di politiche di approvazione avanzate ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Politiche di approvazione di esempio") nella documentazione di Hyperledger Fabric. Per ulteriori informazioni sulla scrittura di politiche di approvazione in JSON, vedi la [documentazione dell'SDK Node di Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "documentazione dell'SDK Node di Hyperledger Fabric").

## Upgrade di uno smart contract
{: #ibp-console-smart-contracts-upgrade}

Puoi eseguire l'upgrade di uno smart contract per modificarne il codice o la politica di approvazione conservandone al tempo stesso la sua relazione con gli asset nel libro mastro. Potresti voler eseguire l'upgrade di uno smart contract istanziato per diversi motivi.
1. Puoi eseguire l'upgrade dello smart contract per aggiungere o rimuovere funzionalità dal suo codice e ad eseguire iterazioni sulla logica della tua rete di business.
2. Ogni volta che un nuovo membro viene aggiunto a un canale, la politica di approvazione degli smart contract istanziati *deve* essere aggiornata per includere il nuovo membro del canale. Per lavorare con il nuovo membro del canale, lo smart contract deve essere reimpacchettato con un nuovo numero di versione e istanziato sul canale, anche se lo smart contract stesso è invariato. In caso contrario, l'approvazione della transazione non può avere esito positivo.
3. Gli argomenti di inizializzazione dello smart contract sono stati modificati.

**Prima di eseguire l'upgrade di uno smart contract istanziato, la nuova versione dello smart contract deve essere installata su tutti i peer nel canale che stanno eseguendo il livello precedente dello smart contract.**

### Come eseguire l'upgrade di uno smart contract
{: #ibp-console-smart-contracts-upgrade-howto}

Per eseguire l'upgrade di uno smart contract, installa il codice aggiornato specificando lo stesso nome di smart contract ma utilizzando un nuovo numero di versione. Se hai installato una versione più recente di uno smart contract su qualsiasi peer nel canale, nota che, accanto alla versione originale, è ora presente il pulsante `Upgrade disponibile` nella tabella **Smart contract istanziati**.

{:important}
Quando un nuovo membro che eseguirà lo smart contract si unisce al canale, è obbligatorio aggiornare lo smart contract installando una nuova versione su tutti i peer e istanziandolo sul canale con una politica di approvazione modificata che include il nuovo membro.

- Scorri verso il basso alla tabella **Smart contract istanziati**.
- Nella tabella **Smart contract istanziati**, individua la versione dello smart contract e il canale di cui desideri eseguire l'upgrade. Deve avere accanto l'etichetta **Upgrade disponibile**.
- Fai clic sul **menu di overflow** sul lato destro della riga dello smart contract e fai clic su **Esegui upgrade** per eseguire la procedura di seguito indicata:  

 1. Seleziona la versione dello smart contract di cui desideri eseguire l'upgrade sul canale dall'elenco a discesa.
 2. Aggiorna la politica di approvazione aggiungendo o rimuovendo membri del canale. Puoi anche fare clic su **Avanzate** per incollare una nuova stringa con formattazione JSON, che modifica la politica esistente.
 3. (Facoltativo) Modifica i valori di argomento di inizializzazione dello smart contract se i parametri hanno subito variazioni. Se non ne sei sicuro, controlla con il tuo sviluppatore dello smart contract. Se non hanno subito variazioni, puoi lasciare questo campo vuoto.

Dopo che hai eseguito l'upgrade dello smart contract, modificherai la versione del contratto istanziato sul canale e modificherai il contenitore smart contract per tutti i peer che hanno installato la nuova versione.

### Considerazioni quando esegui l'upgrade di smart contract
{: #ibp-console-smart-contracts-upgrade-considerations}

1. Devo installare la nuova versione dello smart contract su tutti i miei peer?  

  Quando viene richiamato su un peer, uno smart contract prova ad eseguire la versione che è istanziata sul canale. Se sul server è in esecuzione una versione precedente, viene generato un errore. Pertanto, prima di eseguire l'upgrade di uno smart contract su un canale, *è da ritenersi prassi ottimale l'installazione della versione più recente dello smart contract su tutti i peer che stanno eseguendo la versione precedente.*  

2. Una versione dello smart contract successiva può comunque elaborare i dati sul libro mastro da una versione antecedente dello smart contract?  

  Sì. A condizione che il nuovo codice dello smart contract si occupi dei dati in un modo compatibile (aggiungendo nuovi campi JSON e senza modificare la semantica o il tipo dei campi esistenti), qualsiasi versione successiva dello smart contract può essere eseguita sui dati da una versione precedente.

3. Cosa succede ai miei peer se mi dimentico di eseguirne l'upgrade alla versione più recente prima di aggiornare lo smart contract sul canale?  

  Dopo l'aggiornamento del canale per utilizzare la nuova versione dello smart contract, se ci sono ancora dei peer sul canale che eseguono la versione precedente, tali peer non possono più approvare transazioni per lo smart contract. Rischi inoltre di non disporre di un numero sufficiente di approvazioni perché venga eseguito il commit delle transazioni sul libro mastro, a seconda di come è definita la politica di approvazione dello smart contract. Tuttavia, è possibile installare la nuova versione dello smart contract su questi peer in un secondo momento e potranno nuovamente approvare le transazioni, effettivamente rimettendosi al passo.

## Dati privati
{: #ibp-console-smart-contracts-private-data}

I dati privati sono una funzione delle reti Hyperledger Fabric alla versione 1.2 o superiore e sono utilizzati per mantenere private le informazioni sensibili rispetto agli altri membri dell'organizzazione **in un canale**. La riservatezza dei dati viene ottenuta mediante l'uso di [raccolte di dati privati ![Icona link esterno")](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "What is a private data collection?"). Ad esempio, diversi grossisti e un gruppo di agricoltori potrebbero essere uniti a un singolo canale. Se un agricoltore e un grossista vogliono eseguire transazioni in privato, possono creare un canale a questo scopo. Possono però anche decidere di creare una raccolta di dati privati sullo smart contract che regola le loro interazioni commerciali per mantenere la riservatezza sugli aspetti sensibili della vendita, come ad esempio il prezzo, senza dover creare un canale secondario. Per ulteriori informazioni su quando utilizzare i dati privati all'interno di una blockchain, visita l'articolo relativo al concetto di [dati privati![Icona link esterno")](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "dati privati") nella documentazione di Fabric.

Per utilizzare i dati privati con {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta, devono essere soddisfatte le tre condizioni di seguito indicate.  
1. **Definisci la raccolta di dati privati.** Un file di raccolta di dati privati può essere aggiunto al tuo smart contract. Quindi, al runtime, la tua applicazione client può utilizzare le API di chaincode specifiche per i dati privati per immettere e richiamare dati dalla raccolta. Per ulteriori informazioni su come utilizzare le raccolte di dati privati con il tuo smart contract, vedi l'esercitazione relativa all'[utilizzo di dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "utilizzo di dati privati") nella documentazione di Fabric.  

2. **Installa e istanzia lo smart contract.** Dopo che la raccolta di dati privati dello smart contract è stata definita, devi installare lo smart contract sui peer che sono membri del canale. Quando istanzi lo smart contract sul canale, devi specificare la configurazione della raccolta. La console {{site.data.keyword.blockchainfull_notm}} attualmente non fornisce un modo per specificare una definizione della raccolta **durante** l'istanziazione dello smart contract. Tuttavia, puoi utilizzare l'SDK Fabric per installare, istanziare e aggiornare uno smart contract che utilizza i dati privati. Per ulteriori informazioni, consulta [How to use private data ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "how to use private data") nella documentazione SDK Node.  

 **Nota:** un client deve essere un amministratore del tuo peer per installare o istanziare uno smart contract. Pertanto, devi scaricare i certificati dell'identità amministratore del peer dal tuo portafoglio della console e passare la chiave privata e quella pubblica dell'amministratore del peer direttamente all'SDK invece di creare un'identità dell'applicazione. Per un esempio di come passare una coppia di chiavi all'SDK, vedi [Connessione alla tua rete utilizzando API SDK Fabric di basso livello](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level).  

3. **Configura i peer di ancoraggio.** Poiché il [gossip ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip data dissemination protocol") tra le organizzazioni deve essere abilitato perché i dati privati funzionino, deve esistere un peer di ancoraggio per ciascuna organizzazione nella definizione della raccolta. Questo peer di ancoraggio non è un **tipo** di peer speciale; è solo il peer che l'organizzazione rende noto alle altre organizzazioni, eseguendo così il bootstrap del gossip tra le organizzazioni. Pertanto, deve essere definito almeno un [peer di ancoraggio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Anchor peers") per ciascuna organizzazione nella definizione della raccolta.
 - Per configurare un peer in modo che sia un peer di ancoraggio, fai clic sulla scheda **Canali** e apri il canale dove era stato istanziato lo smart contract.  
 - Fai clic sulla scheda **Dettagli del canale**.
 - Scorri verso il basso fino alla tabella Peer di ancoraggio e fai clic su **Aggiungi peer di ancoraggio**.
 - Seleziona almeno un peer da ciascuna organizzazione nella definizione della raccolta che desideri funga da peer di ancoraggio per l'organizzazione. Per motivi di ridondanza, puoi considerare la selezione di più di un peer da ciascuna organizzazione nella raccolta.

Il tuo canale è ora configurato per utilizzare i dati privati.

## Risoluzione dei problemi
{: #console-operate-troubleshooting}

**Problema:** l'installazione, l'istanziazione o l'upgrade di uno smart contract non riesce con un errore nella console.  
**Soluzione:** se una di queste azioni su uno smart contract non riesce, [controlla i tuoi log del nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) per rilevare la presenza di eventuali errori.

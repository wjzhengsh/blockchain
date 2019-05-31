---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: smart contract, private data, private data collection, anchor peer

subcollection: blockchain

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

Uno smart contract consiste in codice, a volte indicato come chaincode, che ti consente di leggere e aggiornare i dati nel libro mastro della blockchain. Uno smart contract può trasformare la logica di business in un programma eseguibile accettato e verificato da tutti i membri di una rete blockchain. Questa esercitazione è la terza parte nella [serie di esercitazioni di rete di esempio](#ibp-console-smart-contracts-structure) e descrive come distribuire gli smart contract per avviare le transazioni nella rete blockchain.
{:shortdesc}

Se stai utilizzando la versione di prova beta di {{site.data.keyword.blockchainfull_notm}} Platform, è probabile che alcuni pannelli nella tua console non corrisponderanno alla documentazione corrente, che viene mantenuta aggiornata con l'istanza del servizio generalmente disponibile (GA). Per avvalerti dei vantaggi di tutte le ultime funzionalità, ti incoraggiamo in questo momento ad eseguire il provisioning a una nuova istanza del servizio GA seguendo le istruzioni in [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
{: important}

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain. Inoltre, gli sviluppatori dell'applicazione potrebbero essere interessati alle sezioni che fanno riferimento a come creare uno smart contract.

## Serie di esercitazioni sulla rete di esempio
{: #ibp-console-smart-contracts-structure}

Questa serie di esercitazioni in tre parti ti guida attraverso il processo di creazione e interconnessione di una rete Hyperledger Fabric a più nodi relativamente semplice utilizzando la console {{site.data.keyword.blockchainfull_notm}} Platform 2.0 per distribuire una rete nel tuo cluster Kubernetes e installare e istanziare uno smart contract.

* L'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) ti guida attraverso il processo di hosting di una rete creando un ordinante e un peer.
* L'[Esercitazione: unisciti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) ti guida attraverso il processo di unione a una rete esistente creando un peer e unendolo a un canale.
* **Esercitazione: distribuisci uno smart contract sulla rete** questa esercitazione fornisce informazioni su come scrivere uno smart contract e distribuirlo sulla tua rete.

Puoi utilizzare i passi descritti in queste esercitazioni per creare una rete con più organizzazioni in un cluster per scopi di sviluppo e di test. Utilizza l'esercitazione **Crea una rete** se vuoi formare un consorzio blockchain creando un nodo ordinante e aggiungendo delle organizzazioni. Utilizza l'esercitazione **Unisciti a una rete** per connettere un peer alla rete. Seguendo le esercitazioni con diversi membri del consorzio puoi creare una rete blockchain realmente **distribuita**.  

Questa esercitazione finale ha lo scopo di mostrarti come creare e impacchettare uno smart contract, come installarlo su un peer e come istanziarlo su un canale.  

Gli smart contract sono installati sui peer e quindi istanziati sui canali. **Tutti i membri che desiderano inoltrare transazioni o leggere dati utilizzando uno smart contract devono installare il contratto sul loro peer.** Uno smart contract è definito dal suo nome e dalla sua versione. Pertanto, sia il nome che la versione del contratto installato devono essere congruenti tra tutti i peer sul canale che intendono eseguire lo smart contract.

Dopo che uno smart contract è stato installato sui peer, un singolo membro della rete istanzia il contratto sul canale. Per eseguire questa azione, è necessario che il membro della rete abbia aderito al canale. L'istanziazione aggiorna il libro mastro con i dati iniziali utilizzati dallo smart contract e avvia quindi i contenitori smart contract sui peer uniti al canale che hanno il contratto installato. I peer possono quindi utilizzare i contenitori in esecuzione per effettuare transazioni.  
- **Solo un singolo membro della rete deve istanziare uno smart contract.**
- **Se un peer con uno smart contract installato si unisce a un canale in cui è già stata istanziata la stessa versione di smart contract, il contenitore smart contract viene avviato automaticamente.**  

In questa esercitazione, vedremo la procedura per utilizzare la console {{site.data.keyword.blockchainfull_notm}} Platform per gestire la distribuzione di smart contract sulla tua rete.

- [Installa gli smart contract sui tuoi peer](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install).
- [Istanziali sui canali](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).
- [Specifica le politiche di approvazione](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse).
- [Esegui l'upgrade del codice e delle politiche di smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).


## Prima di cominciare

Prima di poter installare uno smart contract, assicurati di avere pronto quanto segue.

- Devi [creare una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) o [unirti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) utilizzando la tua console {{site.data.keyword.blockchainfull_notm}} Platform.
- Poiché gli smart contract sono installati sui peer, assicurati di [aggiungere i peer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1) alla tua console.  
- Gli smart contract sono istanziati su un canale. Devi pertanto [creare un canale](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) o [unirti a un canale](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) utilizzando la tua console.

## Passo uno: Scrivi uno smart contract

La console {{site.data.keyword.blockchainfull_notm}} gestisce la *distribuzione* degli smart contract piuttosto che lo sviluppo. Se sei interessato a sviluppare degli smart contract, puoi iniziare utilizzando le esercitazioni fornite dalla community di Hyperledger Fabric e la strumentazione fornita da {{site.data.keyword.IBM_notm}}.

- Per ulteriori informazioni su come è possibile utilizzare gli smart contract per condurre delle transazioni tra più parti, vedi l'[argomento relativo allo sviluppo di applicazioni ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Argomento relativo allo sviluppo di applicazioni") nella documentazione di Hyperledger Fabric.
- Per un'esercitazione end-to-end completa relativa all'utilizzo di un'applicazione per interagire con gli smart contract, vedi l'[esercitazione Commercial paper di Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "esercitazione Commercial paper di Hyperledger Fabric").
- Per ulteriori informazioni su come incorporare i meccanismi di controllo dell'accesso nel tuo smart contract, vedi il documento relativo al [chaincode per gli sviluppatori ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "chaincode per gli sviluppatori").
- Quando sei pronto a iniziare a creare degli smart contract, puoi utilizzare l'[estensione {{site.data.keyword.blockchainfull_notm}} Visual Studio Code ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") per iniziare a creare il tuo progetto di smart contract. Puoi anche utilizzare tale estensione per [connetterti direttamente alla tua rete da Visual Studio Code](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode).
- Quando sei pronto ad eseguire l'installazione, lo smart contract deve essere impacchettato in [formato .cds ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "impacchettamento di smart contract") in modo che possa essere installato sui peer. Per ulteriori informazioni, vedi [Impacchettamento di smart contract](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). In alternativa, puoi utilizzare i [comandi peer cli ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html#peer-chaincode-package "peer chaincode package") per creare il pacchetto.
<!-- Update the tutorial link to release1-4 when it is published -->


## Passo 2: Installa uno smart contract
{: #ibp-console-smart-contracts-install}

Utilizza la tua console per eseguire i seguenti passi:

1. Fai clic sulla scheda **Smart contract** per installare uno o più smart contract.
2. Fai clic su **Installa smart contract** per caricare il file di pacchetto di smart contract in [formato .cds ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "impacchettamento di smart contract").
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
2. Nel pannello laterale che viene aperto, seleziona un canale su cui istanziare lo smart contract. Puoi selezionare il canale, denominato `channel1`, che hai creato. Poi, fai clic su **Avanti**.
3. Specifica la [politica di approvazione per lo smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse) descritta nella seguente sezione. Quando più organizzazioni sono membri del canale, hai l'opportunità di scegliere quante organizzazioni sono necessarie per approvare le transazioni di smart contract.
4. Devi anche selezionare i membri dell'organizzazione da includere nella politica di approvazione. Se ti stai attenendo all'esercitazione, si tratterà di `org1msp` e forse di `org2msp` se hai completato entrambe le esercitazioni **Crea una rete** e **Unisciti a una rete**.
5. Se il tuo smart contract include delle raccolte di dati privati Fabric, devi caricare il file JSON di configurazione della raccolta associata, altrimenti puoi ignorare questo passo. Vedi questo argomento per ulteriori informazioni sull'utilizzo dei [dati privati](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).
6. Nell'ultimo pannello ti viene chiesto di specificare la funzione smart contract che desideri eseguire quando viene avviato lo smart contract, insieme agli argomenti associati da passare a tale funzione.

Puoi visualizzare tutti gli smart contract che sono stati istanziati su un canale facendo clic sull'icona di canale nella navigazione di sinistra, selezionando un canale dalla tabella e facendo quindi clic sulla scheda **Dettagli del canale**.

Tieni presente che, se utilizzi un cluster {{site.data.keyword.cloud_notm}} Kubernetes Service gratuito, l'istanziazione può richiedere molto più tempo che in un cluster a pagamento. A seconda del numero di peer che hai distribuito nel tuo cluster, questa operazione può richiedere diversi minuti.

La combinazione di **installazione e istanziazione** è una potente funzione perché consente a un peer di utilizzare un singolo smart contract su molti canali. I peer possono volersi unire a più canali che utilizzano lo stesso smart contract ma con insiemi di membri della rete differenti in grado di accedere ai dati. Un peer può installare lo smart contract una sola volta e utilizzare quindi lo stesso contenitore smart contract su qualsiasi canale dove è stato istanziato. Questo approccio leggero consente di risparmiare spazio di calcolo e di archiviazione e ti aiuta a ridimensionare la tua rete.

## Passo quattro: Invia le transazioni utilizzando le tue applicazioni client
{: #ibp-console-smart-contracts-connect-to-SDK}

Dopo che uno smart contract è stato istanziato su un canale, puoi utilizzare un'applicazione client per eseguire transazioni con altri membri della tua rete. Le applicazioni possono richiamare la logica di business contenuta negli smart contract per creare, trasferire o aggiornare gli asset nel libro mastro blockchain.

### Connetti a SDK
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
La scheda **Smart contract** contiene le informazioni di cui hai bisogno per connetterti a uno smart contract istanziato da un'applicazione client. Accanto a ciascuno smart contract istanziato, vai al menu di overflow. Fai clic sul pulsante **Connetti con il tuo SDK**. Viene aperto un pannello laterale che fornisce le informazioni di cui hai bisogno per connetterti a questo smart contract: il nome del contratto, il nome del canale e il tuo profilo di connessione. Per ulteriori informazioni, vedi [Creazione di applicazioni](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Specifica di una politica di approvazione
{: #ibp-console-smart-contracts-endorse}

Ogni smart contract deve avere una politica di approvazione, che viene specificata durante l'istanziazione. La politica di approvazione specifica l'insieme di organizzazioni, i peer, su un canale che possono eseguire lo smart contract e convalidano indipendentemente l'output delle transazioni. Ad esempio, una politica di approvazione può specificare che una transazione viene aggiunta al libro mastro solo se la maggioranza dei membri del canale la approva. L'organizzazione che istanzia lo smart contract può selezionare tra i membri del canale che hanno installato lo smart contract perché diventino validatori e imposta la politica di approvazione per tutti i membri del canale. Puoi aggiornare la tua politica di approvazione attenendoti alla procedura per l'[aggiornamento del tuo smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).

Quando ti attieni alla procedura per [istanziare uno smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate), puoi utilizzare il pannello laterale per impostare la politica di approvazione di un contratto dopo aver selezionato il canale. Utilizza questo pannello per specificare una semplice politica selezionando i peer che devono approvare la transazione dall'elenco di peer che hanno installato lo smart contract sul canale. Puoi utilizzare questo metodo per specificare una politica di approvazione di tutti i membri del canale, una loro maggioranza, un singolo membro oppure un semplice +1 per evitare che i membri firmino per sé stessi. La politica di approvazione predefinita viene impostata su `1 of x`, il che significa che solo un singolo membro deve approvare una transazione di smart contract.

Fai clic sul pulsante **Avanzate** se desideri specificare una politica in formato JSON. Puoi utilizzare questo metodo per specificare delle politiche di approvazione più complicate, come ad esempio la richiesta che uno specifico membro del canale debba convalidare una transazione, insieme a una maggioranza degli altri membri. Puoi trovare ulteriori [esempi di politiche di approvazione avanzate ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Politiche di approvazione di esempio") nella documentazione di Hyperledger Fabric. Per ulteriori informazioni sulla scrittura di politiche di approvazione in JSON, vedi la [documentazione dell'SDK Node di Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "documentazione dell'SDK Node di Hyperledger Fabric").

## Upgrade di uno smart contract
{: #ibp-console-smart-contracts-upgrade}

Puoi eseguire l'upgrade di uno smart contract per modificarne il codice, la politica di approvazione o la raccolta di dati privati conservandone al tempo stesso la sua relazione con gli asset nel libro mastro. Potresti voler eseguire l'upgrade di uno smart contract istanziato per diversi motivi.
1. Puoi eseguire l'upgrade dello smart contract per aggiungere o rimuovere funzionalità dal suo codice e ad eseguire iterazioni sulla logica della tua rete di business.
2. Ogni volta che un nuovo membro viene aggiunto a un canale, la politica di approvazione degli smart contract istanziati *deve* essere aggiornata per includere il nuovo membro del canale. Per lavorare con il nuovo membro del canale, lo smart contract deve essere reimpacchettato con un nuovo numero di versione e istanziato sul canale, anche se lo smart contract stesso è invariato. In caso contrario, l'approvazione della transazione non può avere esito positivo.
3. Quando viene modificata una raccolta di dati privati, ad esempio viene aggiunta o rimossa un'organizzazione, devi eseguire l'upgrade del tuo smart contract. Oppure, utilizzare questa azione se viene aggiunta una nuova raccolta di dati privati al file JSON di configurazione della raccolta.
4. Gli argomenti di inizializzazione dello smart contract sono stati modificati.

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
 3. Se vuoi associare il file di configurazione della raccolta di dati privati allo smart contract, puoi caricare il file JSON. Oppure se vuoi aggiornare una configurazione della raccolta esistente, puoi caricare il file JSON.   
 Se lo smart contract era stato precedentemente istanziato con un file di configurazione della raccolta, **devi** caricare nuovamente la precedente versione o una nuova versione del file di configurazione della raccolta durante questo passo.  
 {:important}
 4. (Facoltativo) Modifica i valori di argomento di inizializzazione dello smart contract se i parametri hanno subito variazioni. Se non ne sei sicuro, controlla con il tuo sviluppatore dello smart contract. Se non hanno subito variazioni, puoi lasciare questo campo vuoto.

Dopo che hai eseguito l'upgrade dello smart contract, modificherai la versione del contratto istanziato sul canale e modificherai il contenitore smart contract per tutti i peer che hanno installato la nuova versione. Se stai utilizzando le raccolte di dati privati, assicurati di aver configurato dei peer di ancoraggio sul canale.

### Considerazioni quando esegui l'upgrade di smart contract
{: #ibp-console-smart-contracts-upgrade-considerations}

1. Devo installare la nuova versione dello smart contract su tutti i miei peer?  

  Quando viene richiamato su un peer, uno smart contract prova ad eseguire la versione che è istanziata sul canale. Se sul server è in esecuzione una versione precedente, viene generato un errore. Pertanto, prima di eseguire l'upgrade di uno smart contract su un canale, *è da ritenersi prassi ottimale l'installazione della versione più recente dello smart contract su tutti i peer che stanno eseguendo la versione precedente.*  

2. Una versione dello smart contract successiva può comunque elaborare i dati sul libro mastro da una versione antecedente dello smart contract?  

  Sì. A condizione che il nuovo codice dello smart contract si occupi dei dati in un modo compatibile (aggiungendo nuovi campi JSON e senza modificare la semantica o il tipo dei campi esistenti), qualsiasi versione successiva dello smart contract può essere eseguita sui dati da una versione precedente.

3. Cosa succede ai miei peer se mi dimentico di eseguirne l'upgrade alla versione più recente prima di aggiornare lo smart contract sul canale?  

  Dopo l'aggiornamento del canale per utilizzare la nuova versione dello smart contract, se ci sono ancora dei peer sul canale che eseguono la versione precedente, tali peer non possono più approvare transazioni per lo smart contract. Rischi inoltre di non disporre di un numero sufficiente di approvazioni perché venga eseguito il commit delle transazioni sul libro mastro, a seconda di come è definita la politica di approvazione dello smart contract. Tuttavia, è possibile installare la nuova versione dello smart contract su questi peer in un secondo momento e potranno nuovamente approvare le transazioni, effettivamente rimettendosi al passo.

4. Cosa succede quando rimuovo un'organizzazione dalla mia raccolta di dati privati?

   I peer in tale organizzazione continueranno ad archiviare i dati nella raccolta di dati privati finché il relativo libro mastro raggiunge il blocco che rimuove la sua appartenenza dalla raccolta. Dopo che questo si verifica, i peer non riceveranno i dati privati in alcuna transazione futura e i _client_ di tale organizzazione non potranno più eseguire query dei dati privati tramite il chaincode da tutti i peer.

## Dati privati
{: #ibp-console-smart-contracts-private-data}

I dati privati sono una funzione delle reti Hyperledger Fabric alla versione 1.2 o superiore e sono utilizzati per mantenere private le informazioni sensibili rispetto agli altri membri dell'organizzazione **in un canale**. La riservatezza dei dati viene ottenuta mediante l'uso di [raccolte di dati privati ![Icona link esterno")](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "What is a private data collection?"). Ad esempio, diversi grossisti e un gruppo di agricoltori potrebbero essere uniti a un singolo canale. Se un agricoltore e un grossista vogliono eseguire transazioni in privato, possono creare un canale a questo scopo. Possono però anche decidere di creare una raccolta di dati privati sullo smart contract che regola le loro interazioni commerciali per mantenere la riservatezza sugli aspetti sensibili della vendita, come ad esempio il prezzo, senza dover creare un canale secondario. Per ulteriori informazioni su quando utilizzare i dati privati all'interno di una blockchain, visita l'articolo relativo al concetto di [dati privati![Icona link esterno")](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "dati privati") nella documentazione di Fabric.

Per utilizzare i dati privati con {{site.data.keyword.blockchainfull_notm}} Platform, devono essere soddisfatte le tre condizioni di seguito indicate.  
1. **Definisci la raccolta di dati privati.** Un file di raccolta di dati privati può essere aggiunto al tuo smart contract. Quindi, al runtime, la tua applicazione client può utilizzare le API di chaincode specifiche per i dati privati per immettere e richiamare dati dalla raccolta. Per ulteriori informazioni su come utilizzare le raccolte di dati privati con il tuo smart contract, vedi l'esercitazione dell'SDK Fabric su [Utilizzo dei dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-private-data.html "How to use Private Data") nella documentazione dell'SDK Fabric.  

2. **Installa e istanzia lo smart contract.** Dopo che la raccolta di dati privati dello smart contract è stata definita, devi installare lo smart contract sui peer che sono membri del canale. Quando istanzi lo smart contract sul canale utilizzando la console, devi caricare il file JSON di configurazione della raccolta. Per ulteriori informazioni su come [creare un file JSON di definizione della raccolta ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-private-data.html "How to use private data") vedi l'argomento della documentazione dell'SDK Fabric.

  Invece di utilizzare la console per installare e istanziare il tuo smart contract con un file di configurazione della raccolta, puoi anche utilizzare l'SDK Fabric. Queste istruzioni sono disponibili anche in [How to use private data ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "how to use private data") nella documentazione dell'SDK Node.  

  **Nota:** un client deve essere un amministratore del tuo peer per installare o istanziare uno smart contract utilizzando l'SDK. Pertanto, devi scaricare i certificati dell'identità amministratore del peer dal tuo portafoglio della console e passare la chiave privata e il certificato di firma dell'amministratore del peer direttamente all'SDK invece di creare un'identità dell'applicazione. Per un esempio di come passare una coppia di chiavi all'SDK, vedi [Connessione alla tua rete utilizzando API SDK Fabric di basso livello](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level).  


3. **Configura i peer di ancoraggio.** Poiché il [gossip ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip data dissemination protocol") tra le organizzazioni deve essere abilitato perché i dati privati funzionino, deve esistere un peer di ancoraggio per ciascuna organizzazione nella definizione della raccolta. Fai riferimento a queste informazioni su [come configurare i peer di ancoraggio](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-channels-anchor-peers) sulla tua rete.

Il tuo canale è ora configurato per utilizzare i dati privati.

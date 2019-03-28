---

copyright:
  years: 2019
lastupdated: "2019-03-05"

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

# Importazione di nodi
{: #ibp-console-import-nodes}

La console include l'opzione per importare i nodi creati utilizzando un'altra console {{site.data.keyword.blockchainfull}} Platform.
{:shortdesc}  

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain.

## Perché importare un nodo?

Importa i nodi CA, ordinante e peer da altre console {{site.data.keyword.blockchainfull_notm}} Platform quando devi gestirli insieme a nodi già presenti nella tua console. Ad esempio, puoi utilizzare l'opzione per importare i peer se vuoi unire i peer di organizzazioni esterne alla console ai canali nella tua console. Poiché i servizi {{site.data.keyword.blockchainfull_notm}} Platform 2.0 vengono distribuiti in più ambienti {{site.data.keyword.cloud_notm}}, è probabile che alcune istanze del servizio includano solo CA e peer, mentre altre ospitano i servizi di ordini. L'importazione dei vari nodi nella console offre un modo per visualizzare e gestire questi componenti distribuiti da un'unica interfaccia utente in modo che possano effettuare transazioni insieme sulla blockchain.

Una volta importati i nodi nella console, diventa disponibile una solida serie di funzionalità operative che ti consentono ad esempio di:
- Aggiungere nuove organizzazioni al consorzio
- Creare nuovi canali
- Unire i peer ai canali
- Installare smart contract sui peer indipendentemente da dove sono stati distribuiti
- Istanziare gli smart contract sui canali
- Aggiornare gli smart contract sui canali

Durante l'importazione di un componente, devi fornire le relative informazioni di connessione. Quando importi un componente, non importi effettivamente il componente fisico stesso; vengono utilizzate invece le informazioni di connessione per creare una rappresentazione del componente nella console in modo che possa essere gestito dalla console.

 Dopo aver importato un nodo nella console, puoi anche modificare le sue informazioni di connessione utilizzando la scheda **Impostazioni** del nodo.

{: note}
Prima di poter importare i nodi nella console, devono essere esportati dalla console {{site.data.keyword.blockchainfull_notm}} Platform in cui sono stati creati.  Esporta semplicemente le informazioni sul nodo dalla console in un file `JSON` e importa il file generato utilizzando il pulsante `Carica JSON` nel passo del pannello laterale per l'importazione del nodo nella console.


## Inizia da qui: raccolta di certificati o credenziali

Prima di poter importare un componente blockchain esistente da un'altra istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform 2.0, devi importare l'identità di amministratore del componente nel tuo portafoglio della console. L'importazione dell'identità nel portafoglio della console semplifica l'operazione del nodo. L'operatore di rete che ha creato il nodo deve fornirti queste credenziali.

## Importazione di una CA
{: #ibp-console-import-ca}

Un nodo CA è il componente blockchain che emette i certificati per tutte le identità della rete (peer, ordinanti, client e così via) in modo che queste entità possano comunicare, autenticarsi e infine effettuare transazioni. Ogni organizzazione ha la propria CA che funge da radice di attendibilità. Devi aggiungere le tue organizzazioni se ti unisci o crei un consorzio blockchain. Puoi scoprire di più sulle CA nella [panoramica dei componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).  

Esistono diversi motivi per cui potresti voler importare una CA nella tua console. Dopo aver importato la CA, puoi utilizzarla per aggiungere altri peer all'organizzazione peer facendo clic su **Registra utente**. Oppure, puoi utilizzare la CA per creare ulteriori identità di amministratore per un peer o un ordinante.

Per importare una CA nella console {{site.data.keyword.blockchainfull_notm}} Platform e gestirla, devi aver già esportato la CA da un'altra {{site.data.keyword.blockchainfull_notm}} Platform. L'importazione di una CA ti consente di registrare nuovi utenti e [iscrivere le identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

### Prima di cominciare
{: #ibp-console-import-ca-before-you-begin}

- Assicurati di aver già importato il file JSON dell'identità di amministratore della CA nel tuo portafoglio della console.
- Assicurati che il file JSON della CA esportato dalla console in cui è stato creato sia disponibile.

### Come importare una CA  

L'importazione di una CA viene eseguita dalla scheda **Nodi**. Fai clic su **Aggiungi autorità di certificazione** e sul segno `+` nella sezione Autorità di certificazione (CA), seguito da **Importa un'autorità di certificazione esistente**. Segui i passi nel pannello laterale che si apre per specificare le informazioni di connessione.

- Fai clic sul pulsante **Carica JSON** per saltare l'immissione manuale delle informazioni e cercare il file JSON che è stato esportato dalla console in cui è stata creata la CA.
- Imposta l'identità di amministratore per la CA facendo clic su **Identità esistente** e selezionando l'identità di amministratore della CA dal portafoglio della console.

Facoltativamente, quando importi una CA, puoi anche specificare le credenziali per la CA TLS aggiuntiva se ne è inclusa una.  

Dopo aver importato la CA nella console, puoi utilizzare la CA per creare nuove identità e generare i certificati necessari per gestire i componenti e inviare transazioni alla rete. Per ulteriori informazioni, vedi [Gestione delle autorità di certificazione (CA)](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-manage-ca).

### Importazione delle identità di amministratore nel portafoglio della console

Ogni componente di {{site.data.keyword.blockchainfull_notm}} Platform viene distribuito con la relativa chiave pubblica, il certificato di firma, di un amministratore del componente al suo interno. Quando l'amministratore effettua azioni sul componente, questa chiave pubblica viene allegata alla transazione e convalidata rispetto alla configurazione del componente. Le chiavi consentono all'amministratore di svolgere attività di gestione sui propri componenti, come la registrazione di nuovi utenti, la creazione di un nuovo canale o l'installazione di smart contract sui peer. Se vuoi utilizzare la console per gestire un ordinante o un peer creato utilizzando un'altra console, devi caricare l'identità di amministratore associata nel portafoglio della console. Quindi, puoi associare il nodo importato con l'identità nel portafoglio della console. Queste identità devono essere esportate dalla console in cui sono state create e sono necessarie quando il nodo viene importato.

Per importare una nuova identità, apri la scheda **Portafoglio** e fai clic su **Aggiungi identità**. Fai clic su **Carica JSON** per cercare il file di identità JSON esportato dall'operatore di rete da dove era stato creato il nodo.

Dopo aver completato il pannello **Aggiungi identità** e fatto clic su Invia, puoi visualizzare la nuova identità di amministratore nella schermata di panoramica del portafoglio. Puoi ora fare riferimento a queste identità quando importi i componenti CA, peer od ordinante.

## Importazione di un ordinante
{: #ibp-console-import-orderer}

Un nodo ordinante è il componente blockchain che raccoglie le transazioni dai membri della rete, ordina le transazioni e le raggruppa in blocchi. Il servizio ordini, che comprende una raccolta di ordinanti, è l'associazione comune dei consorzi blockchain e deve essere distribuito se stai costituendo un consorzio a cui si uniranno altre organizzazioni. Puoi scoprire di più sugli ordinanti nella [panoramica dei componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

L'importazione di un ordinante nella console ti consente di creare nuovi canali in modo che i peer possano effettuare transazioni private.

### Prima di cominciare
{: #ibp-console-import-orderer-before-you-begin}

- Assicurati di aver già importato il file JSON dell'identità di amministratore dell'ordinante nel tuo portafoglio della console.
- Assicurati che il file JSON dell'ordinante esportato dalla console in cui è stato creato sia disponibile.

### Come importare un ordinante
L'importazione di un ordinante viene eseguita dalla scheda **Nodi**. Fai clic su **Aggiungi ordinante** e sul segno `+` nella sezione degli ordinanti, seguito da **Importa un ordinante esistente**. Segui i passi nel pannello laterale che si apre per specificare manualmente le informazioni di connessione.
- Fai clic sul pulsante **Carica JSON** per saltare l'immissione manuale delle informazioni e cercare il file JSON che è stato esportato dalla console in cui è stato creato l'ordinante.
- Imposta l'identità di amministratore per l'ordinante facendo clic su **Identità esistente** e selezionando l'identità di amministratore dell'ordinante dal portafoglio della console.

Dopo aver importato l'ordinante nella console, puoi aggiungere nuovi membri dell'organizzazione e selezionare l'ordinante quando crei nuovi canali.

## Importazione di un peer
{: #ibp-console-import-peer}

Un nodo peer è il componente blockchain che gestisce un libro mastro e che esegue uno smart contract per effettuare operazioni di query e aggiornamento sul libro mastro. I membri dell'organizzazione possiedono e gestiscono i peer.  Ogni organizzazione che si unisce a un consorzio deve distribuire almeno un peer. Puoi scoprire di più sui peer nella [panoramica dei componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer).  

Dopo aver importato un peer nella console, puoi installare gli smart contract sul peer e unire il peer ad altri canali nella tua blockchain.

**Nota:** se hai bisogno di aggiungere altri peer all'organizzazione peer o di creare ulteriori identità di amministratore per un peer, devi importare la CA del peer e quindi utilizzare tale CA per effettuare queste operazioni.

### Prima di cominciare
{: #ibp-console-import-peer-before-you-begin}

Prima di poter importare un peer, devi raccogliere le seguenti informazioni e certificati:

- Assicurati di aver già importato il file JSON dell'identità di amministratore del peer nel tuo portafoglio della console.
- Assicurati che il file JSON del peer esportato dalla console in cui è stato creato sia disponibile.

### Come importare un peer

L'importazione di un peer viene eseguita dalla scheda **Nodi**. Fai clic su **Aggiungi peer** e sul segno `+` nella sezione Peer, seguito da **Importa un peer esistente**. Segui i passi nel pannello laterale che si apre per specificare le informazioni di connessione.
- Fai clic sul pulsante **Carica JSON** per saltare l'immissione manuale delle informazioni e cercare il file JSON che è stato esportato dalla console in cui è stato creato il peer.
- Imposta l'identità di amministratore per il peer facendo clic su **Identità esistente** e selezionando l'identità di amministratore del peer dal portafoglio della console.

Dopo aver importato il peer nella console, puoi installare gli smart contract sul peer e unire il peer ai canali nella tua blockchain.


<!-- ## Importing an MSP -->

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

# Esercitazione: unisciti a una rete
{: #ibp-console-join-network}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform è un'offerta BaaS (blockchain-as-a-service) che ti consente di sviluppare, distribuire e gestire applicazioni e reti blockchain. Puoi scoprire di più sui componenti blockchain e su come lavorano insieme visitando la [Panoramica dei componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Questa esercitazione è la seconda parte della [serie di esercitazioni sulla rete di esempio](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) e descrive come creare nodi nella console {{site.data.keyword.blockchainfull_notm}} Platform e connetterli a un consorzio blockchain ospitato in un altro cluster.
{:shortdesc}

Se non hai già distribuito la console in un cluster Kubernetes utilizzando {{site.data.keyword.cloud_notm}} Kubernetes Service, vedi [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Puoi creare un nuovo cluster Kubernetes per la distribuzione della console o utilizzarne uno esistente nel tuo account {{site.data.keyword.cloud_notm}}.  Dopo aver distribuito {{site.data.keyword.blockchainfull_notm}} Platform al tuo cluster Kubernetes, puoi avviare la console per creare e gestire i tuoi componenti blockchain.

Sia che tu esegua la distribuzione su un cluster Kubernetes a pagamento o gratuito, utilizza il dashboard Kubernetes per prestare particolare attenzione alle risorse a tua disposizione quando scegli di distribuire nodi e creare canali. È tua responsabilità gestire il tuo cluster Kubernetes e distribuire risorse aggiuntive, laddove necessario. Sebbene i componenti verranno distribuiti correttamente su un cluster gratuito, più componenti aggiungi, più lenta sarà la loro esecuzione.
{: note}

## Serie di esercitazioni sulla rete di esempio
{: #ibp-console-join-network-structure}

Questa serie di esercitazioni in tre parti ti guida attraverso il processo di creazione e interconnessione di una rete {{site.data.keyword.blockchainfull_notm}} Platform 2.0 relativamente semplice e a più nodi utilizzando la console e sfruttando la distribuzione di Kubernetes.

* L'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) ti guida attraverso il processo di hosting di una rete creando un ordinante e un peer.
* L'[Esercitazione: unisciti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) ti guida attraverso il processo di unione a una rete esistente creando un peer e unendolo a un canale.
* L'[Esercitazione: distribuisci uno smart contract sulla rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) fornisce informazioni su come scrivere uno smart contract e distribuirlo sulla tua rete.

Puoi utilizzare i passi descritti in queste esercitazioni per creare una rete con più organizzazioni in un cluster per scopi di sviluppo e di test. Utilizza l'esercitazione **Crea una rete** se vuoi fondare un consorzio blockchain. Utilizza l'esercitazione **Unisciti a una rete** per connettere un peer alla rete. Seguendo le esercitazioni con diversi membri del consorzio puoi creare una rete blockchain realmente **distribuita**.  

Questa esercitazione ha lo scopo di mostrare come unire un peer a una rete **esistente**. Presuppone che un ordinante, che ospita la rete, esista già nel tuo cluster o su un altro cluster di {{site.data.keyword.blockchainfull_notm}} Platform. Se non hai una rete esistente da unire, consulta l'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) per imparare a crearne una. L'esercitazione **Unisciti a una rete** ti guida attraverso i passi per creare i seguenti componenti blockchain di `Org2`, evidenziati nella casella blu:
![Unisciti alla struttura della rete](../images/ib2-join-network.png "Unisciti alla struttura della rete")  
*Figura 1. Unisciti alla struttura della rete*  
Esegui i passi dell'esercitazione **Unisciti a una rete** per creare i seguenti componenti e completare le seguenti azioni:

* **Una organizzazione peer** `Org2`  
  Crea la definizione MSP (Membership Services Provider) di Org2 che definisce l'organizzazione `Org2`.
* **Un peer** `Peer Org2`   
  Il libro mastro blockchain, `Ledger x` nell'illustrazione precedente, è gestito dai peer distribuiti. Il peer viene distribuito con [Couch DB ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) come database.
* **Una CA (Certificate Authority)** `Org2 CA`
  Una CA è il nodo che rilascia i certificati a tutti i membri dell'organizzazione. Creiamo una CA per l'organizzazione peer `Org2`.
* **Unione a un canale**
  L'esercitazione descrive come unirsi al canale creato dall'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

Nel corso di tutta questa esercitazione, forniamo **valori consigliati** per alcuni dei campi nella console. Ciò consente di riconoscere più facilmente i nomi e le identità nelle schede e negli elenchi a discesa. Questi valori non sono obbligatori, ma li troverai utili. Forniamo una tabella dei valori consigliati dopo ogni attività.
{:tip}

## Passo uno: crea un'organizzazione aggiuntiva e il tuo punto di ingresso alla blockchain
{: #ibp-console-join-network-create-ca-org2}

Per ogni organizzazione che vuoi creare mediante la console, devi distribuire almeno una CA. Una CA è il nodo che rilascia i certificati a tutti i partecipanti alla rete (peer, ordinanti, client e così via). Questi certificati, che includono una coppia di chiavi pubblica e privata, consentono ai partecipanti alla rete di comunicare, autenticarsi e infine effettuare transazioni. Queste CA creeranno tutte le identità e i certificati che appartengono alla tua organizzazione, oltre a definire l'organizzazione stessa. Puoi quindi usare tali identità per distribuire nodi, utilizzare la tua rete e inviare transazioni alla blockchain. Per ulteriori informazioni sulla tua CA e sulle identità che dovrai creare, vedi [Gestione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

In questa esercitazione, creeremo un'organizzazione e **una CA**.

### Creazione della CA della tua organizzazione peer
{: #ibp-console-join-network-create-CA-org2CA}

Come parte di questa esercitazione, la CA emette le chiavi pubbliche e private per i tuoi utenti e nodi. Queste identità non sono gestite da {{site.data.keyword.IBM_notm}} e le chiavi non vengono memorizzate nel tuo cluster Kubernetes o nella console. Vengono memorizzate solo nell'archiviazione locale del tuo browser. Pertanto, assicurati di esportare le identità e l'MSP dell'organizzazione. Se tenti di accedere alla console da una macchina diversa o da un browser diverso, devi importare tali identità e definizioni dell'organizzazione.  
{:important}

Completa la seguente procedura dalla tua console:  

1. Vai alla scheda **Nodi** sulla sinistra e fai clic su **Aggiungi autorità di certificazione**. I pannelli laterali a quattro fasi ti consentiranno di personalizzare la CA che vuoi creare e l'organizzazione per cui questa CA emetterà le chiavi.
2. Fai clic su **{{site.data.keyword.cloud_notm}}** in **Crea autorità di certificazione**.
3. Utilizza il secondo pannello laterale per fornire alla tua CA un **nome di visualizzazione**. Il nostro valore consigliato per questa CA è `Org2 CA`.
4. Nel pannello successivo, fornisci le tue credenziali di amministratore CA specificando come **ID amministratore** il valore `admin` e fornendo qualsiasi segreto che preferisci, ma ti consigliamo di utilizzare `adminpw` ai fini di questa esercitazione.
5. Fai clic su **Invia**.

**Attività: creazione della CA dell'organizzazione peer**

  | **Campo** | **Nome di visualizzazione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|
  | **Crea CA** | Org2 CA  | admin | adminpw |

*Figura 2. Creazione della CA dell'organizzazione peer*  
Dopo aver distribuito la CA, la utilizzerai per creare l'MSP dell'organizzazione, registrare gli utenti e creare il tuo punto di ingresso a una rete, ossia il **peer**.

### Utilizzo della tua CA per registrare le identità
{: #ibp-console-join-network-use-CA-org2}

Ogni nodo o applicazione che vuoi creare necessita di chiavi pubbliche e private per partecipare alla rete blockchain. Devi inoltre creare delle chiavi di amministrazione per questi nodi e applicazioni in modo che tu possa gestirli dalla console. Userai la CA per creare due identità:

* **Un amministratore dell'organizzazione**: questa identità ti consente di utilizzare i nodi servendoti della console della piattaforma.
* **Un'identità peer**: questa identità ti consentirà di distribuire un peer.

Per generare questi certificati, completa la seguente procedura:

1. Nella console, utilizza la scheda **Nodi** per passare alla `Org2 CA` che hai creato.
2. Dopo aver selezionato la tua CA, dovrai registrare un amministratore per questa organizzazione, `org2`, oltre a un'identità per il peer stesso. Dovresti già vedere un'identità su questa pagina; è l'amministratore che hai creato per la CA. Per registrare i nostri nuovi utenti, fai clic sul pulsante **Registra utente**.
3. Per l'amministratore dell'organizzazione, fornisci l'ID di registrazione `org2admin`. Puoi usare qualsiasi segreto, ma ti suggeriamo `org2adminpw` per aiutarti a proseguire. Fai clic su **Avanti**.
4. Nel passo successivo, imposta il tipo per questa identità su `client` e seleziona una delle organizzazioni affiliate dall'elenco a discesa. Il campo di affiliazione è per utenti esperti e non è utilizzato nell'esercitazione. Gli elementi nell'elenco sono le affiliazioni predefinite dalla CA Fabric. Se vuoi saperne di più su come vengono utilizzate le affiliazioni dalla CA Fabric, consulta questo argomento su [Registering a new identity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Per ora, seleziona una qualsiasi affiliazione dall'elenco, ad esempio `org2` e fai clic su **Avanti**.
5. Sentiti libero di lasciare vuoti i campi **Numero massimo di iscrizioni** e **Aggiungi attributi**. Non vengono utilizzati in questa esercitazione, ma puoi trovare una descrizione del loro utilizzo nell'argomento [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
6. Dopo che l'amministratore dell'organizzazione è stato registrato, ripeti il processo dal passo due al passo cinque per l'identità del peer, utilizzando la stessa `Org2 CA`, fornendo l'ID di registrazione `peer2`. Come prima, ti consigliamo di utilizzare il segreto `peer2pw` per aiutarti a proseguire. Questa è un'identità del nodo, quindi seleziona `peer` per il **Tipo** nel passo successivo. Quindi, ignora i campi **Numero massimo di iscrizioni** e **Attributi** come hai fatto prima.

**Attività: registra gli utenti**

  |  **Campo** | **Descrizione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registra utenti** |  Org2 admin | org2admin | org2adminpw |
  | | Identità peer |  peer2 | peer2pw |

*Figura 3. Utilizzo della tua CA per registrare gli utenti*  

### Creazione dell'MSP dell'organizzazione peer
{: #ibp-console-join-network-create-peers-org2}

Ora che abbiamo creato la CA del peer e l'abbiamo utilizzata per **registrare** le identità della nostra organizzazione, dobbiamo creare una definizione formale dell'organizzazione del peer, che è nota come definizione MSP (Membership Services Provider). Diversi peer possono appartenere a un'organizzazione. **Non hai bisogno di creare una nuova organizzazione ogni volta che crei un peer.** Poiché questa è la prima volta che eseguiamo l'esercitazione, creeremo l'ID MSP per questa organizzazione. Durante il processo di creazione dell'MSP, andremo a generare i certificati per l'identità `org2admin` e li aggiungeremo al nostro portafoglio della console.

1. Vai alla scheda **Organizzazioni** nel pannello di navigazione a sinistra e fai clic su **Crea definizione MSP**.
2. Fornisci alla tua MSP un nome di visualizzazione come `Org2 MSP` e un ID come `org2msp`. Se in questo campo vuoi specificare il tuo proprio ID MSP, assicurati di seguire le specifiche sulle limitazioni per questo nome indicate nel suggerimento.
3. In **Dettagli autorità di certificazione (CA) root**, specifica la CA del peer che abbiamo creato come CA root per la tua organizzazione. Se è la prima volta che esegui questa esercitazione, dovresti vederne solo una: `Org2 CA`. Selezionala.
4. I campi **ID di registrazione** e **Segreto di registrazione** qui sotto verranno compilati automaticamente con l'ID e il segreto di registrazione per il primo utente che hai creato con la tua CA. Non utilizzare questi valori. Fornisci invece l'ID e il segreto di registrazione per l'amministratore della tua organizzazione, `org2admin` e `org2adminpw`. Quindi, fornisci a questa identità un nome di visualizzazione, ad esempio `Org2 Admin`.
5. Fai clic sul pulsante **Genera** per registrare questa identità come amministratore della tua organizzazione ed esportare l'identità nel portafoglio, dove verrà utilizzata durante la creazione del peer e dei canali.
6. Fai clic su **Esporta** per esportare i certificati dell'amministratore nel tuo file system. Come detto sopra, questa identità non viene memorizzata nel tuo cluster o gestita da {{site.data.keyword.IBM_notm}}. Viene memorizzata solo nell'archiviazione locale del tuo browser.
7. Fai clic su **Crea definizione MSP**.

**Attività: crea la definizione MSP dell'organizzazione peer**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea organizzazione** | Org2 MSP | org2msp |||
  | **CA root** | Org2 CA ||||
  | **Certificato amministratore dell'organizzazione** | |  | org2admin | org2adminpw |
  | **Identità** | Org2 Admin |||||

  *Figura 4. Crea la definizione MSP dell'organizzazione peer*  

Dopo aver creato l'MSP, dovresti essere in grado di vedere l'amministratore dell'organizzazione peer nel tuo portafoglio della console.

**Attività: controlla il portafoglio della console**

  | **Campo** | **Nome di visualizzazione** | **Descrizione** |
  | ------------------------- |-----------|----------|
  | **Identità** | Org2 Admin | Identità Org2 |

  *Figura 5. Controlla il portafoglio della console*  

Per ulteriori informazioni sulle MSP, vedi [Gestione delle organizzazioni](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Esportare e salvare l'MSP è importante perché consente di aggiungere la tua organizzazione a un consorzio ospitato in un'altra console.
{:important}

### Creazione di un peer
{: #ibp-console-join-network-peer-create}

Dopo aver [creato una CA](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA), averla utilizza per registrare le identità e aver creato la [MSP dell'organizzazione peer](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2), sei pronto per creare un peer.

#### Che ruolo svolgono i peer?
{: #ibp-console-join-network-peer-role}

È importante ricordare che le organizzazioni stesse non gestiscono i libri mastro. Sono i peer a farlo. Le organizzazioni utilizzano anche i peer per firmare le proposte di transazione e approvare gli aggiornamenti della configurazione del canale. Poiché avere almeno due peer su un canale li rende altamente disponibili, avere almeno due peer uniti a un canale è considerato una prassi ottimale per le implementazioni a livello di produzione. In questa esercitazione, mostreremo solo il processo per la creazione di un singolo peer.

Da una prospettiva di allocazione delle risorse, è possibile unire gli stessi peer a più canali. La progettazione del peer garantisce che i dati di un canale non possano passare a un altro canale attraverso il peer. Tuttavia, poiché il peer memorizzerà un libro mastro separato per ogni canale, è necessario assicurarsi che il peer disponga di potenza di elaborazione e archiviazione sufficienti per gestire il caricamento di dati e transazioni.

#### Distribuzione del tuo peer
{: #ibp-console-join-network-deploy-peer-role}

Utilizza la tua console per completare la seguente procedura:

1. Nella pagina **Nodi**, fai clic su **Aggiungi peer**.
2. Fai clic su {{site.data.keyword.cloud_notm}} in **Crea un nuovo peer** e quindi su **Avanti**.
3. Fornisci al tuo peer un **nome di visualizzazione** come `Peer Org2`.
4. Nella schermata successiva, seleziona `Org2 CA` come tua CA. Quindi, immetti l'ID e il segreto di registrazione per l'identità peer che hai creato per il tuo peer, `peer2` e `peer2pw`. Seleziona quindi la tua MSP, `Org2 MSP`, dall'elenco a discesa e fai clic su **Avanti**.
5. Il pannello laterale successivo richiede informazioni sulla CA TLS. Sebbene sia possibile creare amministratori separati per la CA TLS che viene distribuita con la tua CA, non è necessario farlo.
   - Fornisci l'**ID di registrazione TLS**, `admin`, e il segreto `adminpw`, gli stessi valori sono l'ID registrazione e il segreto di registrazione che hai fornito durante la creazione della CA.
   - L'opzione **Nome host CSR TLS** è riservata ad utenti esperti ed è utilizzata per specificare un nome di dominio personalizzato che può essere utilizzato per indirizzare l'endpoint del peer. Per adesso, lascia vuoto il campo **Nome host CSR TLS** in quanto non è utilizzato in questa esercitazione.
6. L'ultimo pannello laterale ti chiederà di **Associare un'identità** e renderla l'amministratore del tuo peer. Seleziona l'identità amministratore del tuo peer `Org2 Admin`.
7. Riesamina il riepilogo e fai clic su **Invia**.

**Attività: distribuzione di un peer**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea peer** | Peer Org2 | org2msp |||
  | **CA** | Org2 CA ||||
  | **Identità peer** | |  | peer2 | peer2pw |
  | **Certificato dell'amministratore** | org2msp ||||
  | **CA TLS** | Org2 CA ||||
  | **ID CA TLS** | || admin | adminpw |
  | **Associa identità** | Org2 Admin |||||

  *Figura 6. Distribuzione di un peer*  

## Passo due: aggiungi la tua organizzazione all'elenco di organizzazioni che possono effettuare transazioni
{: #ibp-console-join-network-add-org2}

Come notato in precedenza, un'organizzazione peer deve essere un membro del consorzio di un ordinante prima di poter creare o unirsi a un canale. Questo perché, a un livello tecnico, i canali sono dei **percorsi di messaggistica** tra i peer attraverso l'ordinante. Proprio come un peer può essere unito a più canali senza che le informazioni passino da un canale a un altro, così un ordinante può disporre di più canali in esecuzione al suo interno senza esporre i dati alle organizzazioni su altri canali.

Poiché solo gli amministratori dell'ordinante possono aggiungere organizzazioni peer al consorzio, dovrai **essere** l'amministratore dell'ordinante o dovrai **inviare** le informazioni MSP all'amministratore dell'ordinante. In questa esercitazione, puoi **aggiungere l'organizzazione del peer all'ordinante nella tua console**, se ospiti il tuo ordinante, oppure devi **esportare le informazioni sulla tua organizzazione** e fornirle all'amministratore dell'ordinante, che ha creato la rete in cui risiede l'ordinante. L'amministratore dell'ordinante può quindi importare la tua organizzazione e aggiungerti al consorzio.

### Aggiungi l'organizzazione del peer all'ordinante nella console
{: #ibp-console-join-network-add-org2-local}

**Segui questi passi solo se la tua console include già l'ordinante e il canale a cui vuoi unirti.** Altrimenti, vai a [Esporta le informazioni sulla tua organizzazione](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).  

Poiché solo gli amministratori dell'ordinante possono aggiungere organizzazioni peer al consorzio, dovrai essere l'amministratore dell'ordinante, il che significa che disponi dell'identità di amministratore dell'organizzazione ordinante nel tuo portafoglio della console.  

1. Vai alla scheda **Nodi**.
2. Scorri verso il basso fino all'ordinante che vuoi utilizzare e fai clic su di esso per aprirlo.
3. In **Membri del consorzio**, fai clic su **Aggiungi organizzazione**.
4. Dall'elenco a discesa, seleziona `Org2 MSP`, in quanto questa è l'MSP che rappresenta l'organizzazione del peer `org2`.
5. Fai clic su **Invia**.

Adesso aggiungi l'organizzazione peer al canale.
1. Vai alla scheda **Canali** e fai clic su `channel1`.
2. Fai clic sull'icona **Impostazioni** per aggiornare il canale e aggiungere l'organizzazione peer al canale.
2. Scorri fino alla sezione `Aggiungi organizzazioni al canale` e quindi apri l'elenco a discesa `Seleziona un membro del canale` e seleziona l'MSP dell'organizzazione peer, `Org2 MSP` per l'esercitazione.
5. Fai clic su **Aggiungi** e quindi assegna le autorizzazioni per tale organizzazione. Ti consigliamo di renderla un `Operator` in modo che possa aggiornare il canale.
6. Fai clic su **Aggiorna canale**.

Una volta completato questo processo, `org2` può creare o unirsi a un canale ospitato sul tuo `Orderer`. Puoi procedere al [Passo tre: unisci il tuo peer al canale](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Esporta le informazioni sulla tua organizzazione
{: #ibp-console-join-network-add-org2-remote}

**Segui questi passi solo se l'ordinante e il canale a cui si unirà il tuo peer risiedono in un'altra istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform.** Altrimenti, puoi passare al [Passo tre: unisci il tuo peer al canale](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

Devi inviare la definizione MSP della tua organizzazione all'amministratore dell'ordinante e aggiungerla al consorzio utilizzando la seguente procedura.  

Quando esegui questi passi, devi essere l'amministratore dell'**organizzazione peer**, il che significa che disponi dell'identità di amministratore dell'organizzazione peer nel tuo portafoglio della console:  
1. Vai alla scheda **Organizzazioni**. Puoi vedere che le organizzazioni disponibili per l'esportazione sono elencate in **Organizzazioni disponibili**. Fai clic sul pulsante di **download** all'interno del tile dell'organizzazione per scaricare il file di configurazione JSON che rappresenta l'MSP dell'organizzazione peer.
2. Invia questo file all'amministratore dell'ordinante in un'operazione fuori banda.

### Importa la definizione dell'organizzazione
{: #ibp-console-join-network-import-remote-msp}
L'amministratore dell'ordinante deve importare questo file JSON per aggiungere la tua organizzazione alla sua console:
1. Vai alla scheda **Organizzazioni**, fai clic sul pulsante **Importa definizione MSP** e seleziona il file JSON che rappresenta la definizione MSP dell'organizzazione peer.  
2. Vai alla pagina **Nodi** e fai clic sul tuo nodo ordinante. Nel pannello del nodo di ordinazione, sotto **Membri del consorzio**, fai clic su **Aggiungi organizzazione**. Nel pannello laterale che si apre, seleziona `Org2 MSP` dall'elenco di definizioni MSP nella scheda **Organizzazioni**.
3. Vai alla scheda **Canali**, fai clic su `channel1` e quindi sull'icona **Impostazioni** per aggiornare il canale e aggiungere l'organizzazione peer al canale.
4. Scorri fino alla sezione `Aggiungi organizzazioni al canale` e quindi apri l'elenco a discesa `Seleziona un membro del canale` e seleziona l'MSP dell'organizzazione peer, `Org2 MSP` per l'esercitazione.
5. Fai clic su **Aggiungi** e quindi assegna le autorizzazioni per tale organizzazione. Ti consigliamo di renderla un `Operator` in modo che possa aggiornare il canale.
6. Fai clic su **Aggiorna canale**.

Dopo che l'amministratore dell'ordinante ha unito la tua organizzazione peer al consorzio e al canale, devi importare il nodo di ordinazione nella tua console. Puoi quindi unirti ai canali ospitati dal servizio ordini. Completa la seguente procedura per **importare** l'ordinante:

Come amministratore dell'**organizzazione ordinante** devi prima completare questi passi:
1. Vai al nodo ordinante all'interno della scheda **Nodi** e fai clic sull'icona **Impostazioni** a destra del nome del nodo per aprire il pannello laterale. Fai clic sul pulsante **Esporta** sotto **Azioni** per scaricare un file di configurazione JSON.
2. Invia questo file all'organizzazione peer in un'operazione fuori banda. L'amministratore dell'organizzazione peer può quindi utilizzare il file di configurazione per aggiungere l'ordinante alla console.

### Importa l'ordinante da un altro cluster
{: #ibp-console-join-network-import-remote-orderer}

Puoi seguire questi passi se sei l'amministratore dell'**organizzazione peer**:
1. Vai alla pagina **Nodi** e fai clic su **Aggiungi ordinante**.
2. Fai clic su {{site.data.keyword.cloud_notm}} in **Importa un ordinante esistente**.
3. Quindi, fai clic sul pulsante **Carica JSON** e seleziona il JSON che rappresenta il nodo stesso.
4. Nel passo successivo, quando ti viene richiesto di associare un'identità, seleziona l'identità dell'organizzazione peer. Per questa esercitazione, seleziona `Org2 Admin` e fai clic su **Invia**.


## Passo tre: unisci il tuo peer al canale
{: #ibp-console-join-network-join-peer-org2}

Abbiamo quasi finito. Il tuo peer può ora essere unito a un canale esistente. Devi ottenere il `channel name`, fuori banda, dall'operatore di rete che ha creato il canale. Nell'esercitazione **Crea una rete**, abbiamo creato un canale denominato `channel1`. Se non ci sei già, vai alla scheda **Canali** nel pannello di navigazione a sinistra.

Completa la seguente procedura dalla tua console:

1. Fai clic sul pulsante **Unisci a canale** per avviare i pannelli laterali.
2. Seleziona il tuo ordinante denominato 'Orderer' e fai clic su **Avanti**.
3. Immetti il nome del canale a cui vuoi unirti, `channel1`, e fai clic su **Avanti**.
4. Seleziona i peer che vuoi unire al canale. Ai fini di questa esercitazione, fai clic su `Peer Org2`.
5. Fai clic su **Invia**.

Se intendi utilizzare le funzioni [Dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/private-data/private-data.html "Private data") o [Rilevamento di servizi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-overview.html "Service Discovery") di Hyperledger Fabric, devi configurare i peer di ancoraggio nella tua organizzazione dalla scheda **Canali**. Vedi questo argomento su [come configurare i peer di ancoraggio per i dati privati](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) utilizzando la scheda **Canali** della console.

Una volta che la tua organizzazione è un membro del consorzio, puoi anche creare un nuovo canale. Utilizza la procedura per [creare un canale](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) nell'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Passi successivi
{: #ibp-console-join-network-next-steps}

Dopo aver unito il tuo peer a un canale, utilizza le seguenti procedure per distribuire uno smart contract e iniziare a inviare transazioni alla blockchain:

- [Distribuisci uno smart contract sulla tua rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) utilizzando la console.
- Dopo aver installato e istanziato il tuo smart contract, puoi [inviare le transazioni utilizzando la tua applicazione client](docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilizza [l'esempio di commercial paper](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) per distribuire uno smart contract di esempio e inviare le transazioni dal codice dell'applicazione di esempio.

## Risoluzione dei problemi
{: #ibp-console-join-network-troubleshooting}

**Problema:** si verificano errori quando provo a utilizzare un nodo.    

**Soluzione:** se ricevi errori mentre utilizzi un nodo nella console, [controlla i log del tuo nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) per verificare gli errori.  

**Problema:** il dashboard Kubernetes mostra che il peer non si avvia e i log del peer includono l'errore `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`

**Soluzione:** assicurati che, quando hai creato la definizione MSP dell'organizzazione del peer, l'ID e il segreto di registrazione che hai specificato corrispondano a un'identità di tipo `client` e non `peer`.

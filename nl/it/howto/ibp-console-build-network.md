---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: getting started tutorials, create a CA, enroll, register, create an MSP, wallet, create a peer, create ordering service, Raft

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

# Esercitazione: crea una rete
{: #ibp-console-build-network}

{{site.data.keyword.blockchainfull}} Platform è un'offerta BaaS (blockchain-as-a-service) che ti consente di sviluppare, distribuire e gestire le applicazioni e le reti blockchain. Puoi scoprire di più sui componenti blockchain e su come lavorano insieme visitando la [Panoramica dei componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Questa esercitazione è la prima parte della [serie di esercitazioni sulla rete di esempio](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) e descrive come utilizzare la console {{site.data.keyword.blockchainfull_notm}} Platform per creare una rete completamente funzionale su un singolo servizio {{site.data.keyword.cloud_notm}} Kubernetes.
{:shortdesc}

Se stai utilizzando la versione di prova beta di {{site.data.keyword.blockchainfull_notm}} Platform, è probabile che alcuni pannelli nella tua console non corrisponderanno alla documentazione corrente, che viene mantenuta aggiornata con l'istanza del servizio generalmente disponibile (GA). Per avvalerti dei vantaggi di tutte le ultime funzionalità, ti incoraggiamo in questo momento ad eseguire il provisioning a una nuova istanza del servizio GA seguendo le istruzioni in [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
{: important}

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain.

Se non hai già utilizzato la console {{site.data.keyword.blockchainfull_notm}} Platform per distribuire i componenti in un cluster Kubernetes utilizzando {{site.data.keyword.cloud_notm}} Kubernetes Service, vedi [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Tieni presente che la console stessa non risiede nel tuo cluster. È uno strumento che puoi utilizzare per distribuire i componenti al tuo cluster. 


Sia che tu esegua la distribuzione dei componenti su un cluster Kubernetes a pagamento o gratuito, utilizza il dashboard Kubernetes per prestare particolare attenzione alle risorse a tua disposizione quando scegli di distribuire nodi e creare canali. È tua responsabilità gestire il tuo cluster Kubernetes e distribuire risorse aggiuntive, laddove necessario. Sebbene i componenti verranno distribuiti correttamente su un cluster gratuito, più componenti aggiungi, più lenta sarà la loro esecuzione. Per ulteriori informazioni sul dimensionamento dei componenti e su come la console interagisce con il tuo cluster Kubernetes, vedi [Allocazione di risorse](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
{: note}

## Serie di esercitazioni sulla rete di esempio
{: #ibp-console-build-network-sample-tutorial}

Questa serie di esercitazioni in tre parti ti guida attraverso il processo di creazione e interconnessione di una rete Hyperledger Fabric a più nodi relativamente semplice utilizzando la console {{site.data.keyword.blockchainfull_notm}} Platform per distribuire una rete nel tuo cluster Kubernetes e installare e istanziare uno smart contract. Tieni presente che mentre questa esercitazione mostrerà come funziona questo processo con un cluster Kubernetes a pagamento, lo stesso flusso di base si applica ai cluster gratuiti, anche se con alcune limitazioni (ad esempio, non puoi dimensionare o ridimensionare i nodi in un cluster gratuito).

* **Esercitazione: crea una rete** questa esercitazione ti guida attraverso il processo di hosting di una rete creando due organizzazioni, una per il tuo peer e l'altra per il tuo servizio di ordinazione e un canale. Utilizza questa esercitazione se vuoi formare un consorzio blockchain creando un servizio di ordinazione e aggiungendo delle organizzazioni.
* L'[Esercitazione: unisciti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) ti guida attraverso il processo di unione a una rete esistente creando un peer e unendolo a un canale esistente. Utilizza questa esercitazione se non vuoi ospitare una rete creando un servizio di ordinazione oppure se vuoi avere informazioni sul processo di unione di altre reti.
* [Distribuire uno smart contract sulla rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) mostra come scrivere uno smart contract e distribuirlo su una rete.


### La struttura di questa rete
{: #ibp-console-build-network-structure}

Se completi tutti i passi nelle esercitazioni **Crea una rete** e **Unisciti a una rete**, la tua rete sarà simile a quella della seguente illustrazione:
![Struttura della rete di base di esempio](../images/ibp-v2-build-network.png "Struttura della rete di base di esempio")
*Figura 1. Struttura della rete di base di esempio*  

Questa configurazione è sufficiente per verificare le applicazioni e gli smart contract e come una guida per la creazione dei componenti e l'unione alle reti di produzione che si adattano al tuo caso di utilizzo. La rete contiene i seguenti componenti:

* **Due organizzazioni peer**: `Org1` e `Org2`  
  Le serie di esercitazioni descrivono come creare due organizzazioni peer e due peer associati. Pensa che le organizzazioni su una rete blockchain siano come due banche diverse che devono effettuare transazioni tra loro. Creeremo le definizioni di `Org1` e `Org2`.
* **Un'organizzazione del servizio di ordinazione**: `Ordering Service`  
  Poiché stiamo creando un libro mastro distribuito, i peer e i servizi di ordinazione devono far parte di organizzazioni separate. Pertanto, viene creata un'organizzazione separata per il servizio di ordinazione.  Tra le altre cose, un servizio di ordinazione ordina i blocchi di transazioni che vengono inviati ai peer per essere scritti nei loro libri mastro e diventare la blockchain. Creeremo la definizione dell'organizzazione `Ordering Service`.
* **Tre autorità di certificazione (CA)**: `Org1 CA, Org2 CA, Ordering Service CA`   
  Una CA è il nodo che rilascia i certificati ad entrambi gli utenti e i nodi associati a un'organizzazione. Poiché è una prassi ottimale distribuire una CA per organizzazione, distribuiremo tre CA in totale: una per ogni organizzazione peer e una per l'organizzazione del servizio di ordinazione. Queste CA creeranno anche la definizione di ogni organizzazione, che viene incapsulata da un MSP (Membership Service Provider). Una CA TLS viene distribuita insieme alla CA dell'organizzazione. Questa CA fornisce i certificati utilizzati per la comunicazione tra i nodi. Mentre è una procedura consigliata stabilire un amministratore separato per la CA TLS, per lo scopo di questa esercitazione non mostreremo tale processo. Per ulteriori informazioni, vedi [Utilizzo della tua CA TLS](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca).
* **Un servizio di ordinazione:** `Ordering Service`  
  Mentre le distribuzioni eseguite su un cluster a pagamento hanno l'opzione di distribuire un servizio di ordinazione di un nodo o un servizio di ordinazione di cinque nodi con tolleranza agli errori di arresto anomalo, i cluster gratuiti hanno solo l'opzione di eseguire un singolo nodo. Il servizio di ordinazione di cinque nodi utilizza un'implementazione del protocollo Raft (per ulteriori informazioni su Raft, vedi [The Ordering Service ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft)) ed è l'opzione di distribuzione che verrà utilizzata da questa esercitazione. Al momento, è supportata solo un'organizzazione del servizio di ordinazione per servizio di ordinazione, indipendentemente dal numero di nodi di ordinazione associati a tale organizzazione. Questo servizio di ordinazione aggiungerà le organizzazioni peer al proprio "consorzio", che è l'elenco delle organizzazioni peer che possono creare e unirsi ai canali. Se vuoi creare un canale con organizzazioni distribuite in cluster diversi, che è il modo in cui verranno strutturate la maggior parte delle reti di produzione, l'amministratore del servizio di ordinazione dovrà importare nella propria console un'organizzazione peer che è stata distribuita in un'altra console. Ciò consente all'organizzazione peer di unirsi al canale ospitato su tale servizio di ordinazione.
* **Due peer:** `Peer Org1` e `Peer Org2`  
  Il libro mastro, `Ledger x` nell'illustrazione precedente, è gestito dai peer distribuiti. Questi peer vengono distribuiti utilizzando [Couch DB ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) come il database dello stato in un contenitore separato associato al peer. Questo database ospita il valore corrente di tutti gli "stati" (come rappresentati dalle coppie chiave-valore). Ad esempio, affermando che `Org1` (un valore) è il proprietario attuale di un asset bancario (la chiave). La blockchain, l'elenco delle transazioni, viene archiviata localmente sul peer.
* **Un canale**: `channel1`  
  I canali consentono a insiemi di organizzazioni di effettuare transazioni senza esporre i propri dati a organizzazioni che non sono membri del canale. Ogni canale ha il proprio libro mastro, gestito collettivamente dai peer uniti a quel canale. L'esercitazione crea un canale unito da entrambe le organizzazioni e mostra come istanziare uno smart contract sul canale che le organizzazioni possono utilizzare per effettuare transazioni.

Questa configurazione non è obbligatoria. {{site.data.keyword.blockchainfull_notm}} Platform è altamente personalizzabile. Se nel tuo cluster Kubernetes sono disponibili risorse, puoi utilizzare la console per distribuire i componenti in un array di configurazioni infinito. In questa esercitazione vengono descritti i passi necessari per creare la tua propria rete, con riferimenti ad argomenti che forniscono informazioni più approfondite su {{site.data.keyword.blockchainfull_notm}} Platform e sulla console.

In questa esercitazione **Crea una rete**, creiamo solo una parte della rete illustrata in precedenza, una rete semplice che può essere utilizzata per ospitare un servizio di ordinazione e una singola organizzazione peer e un peer su un singolo canale. La seguente illustrazione mostra la parte della rete precedente che creeremo:
![Struttura della rete semplice](../images/ibp2-simple-network.png "Struttura della rete semplice")  
*Figura 2. Struttura della rete semplice*  

Questa configurazione è utile per iniziare a utilizzare e testare rapidamente uno smart contract, ma non è molto significativa finché non aggiungi altre organizzazioni con cui effettuare transazioni, creando una rete realmente distribuita.  Pertanto, nella successiva esercitazione [Unisciti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network), ti mostriamo come creare ulteriori organizzazioni peer e peer e come aggiungere una nuova organizzazione al canale.

In tutta questa esercitazione forniamo **valori consigliati** per i campi nella console. Ciò consente di riconoscere più facilmente i nomi e le identità nelle schede e negli elenchi a discesa. Questi valori non sono obbligatori, ma li troverai utili, specialmente perché dovrai ricordare alcuni valori come gli ID e i segreti degli utenti registrati che ha immesso nei passi precedenti. Poiché questi valori non vengono archiviati nella console, se li dimentichi, dovrai registrare ulteriori utenti e riavviare il processo. Forniamo una tabella dei valori consigliati dopo ogni attività e ti consigliamo, nel caso non abbia utilizzato i valori consigliati, di registrare i tuoi valori man mano che procedi nell'esercitazione.
{:tip}

## Passo uno: crea la tua organizzazione e il tuo punto di ingresso alla blockchain
{: #ibp-console-build-network-create-peer-org1}

Per ogni organizzazione che vuoi creare con la console, devi distribuire almeno una CA. Una CA è il nodo che rilascia i certificati a tutti i partecipanti alla rete (peer, servizi di ordinazione, client, amministratori e così via). Questi certificati, che includono un certificato di firma e una chiave privata, consentono ai partecipanti alla rete di comunicare, autenticarsi e infine effettuare transazioni. Queste CA creeranno tutte le identità e i certificati che appartengono alla tua organizzazione, oltre a definire l'organizzazione stessa. Puoi quindi usare tali identità per distribuire nodi, creare identità amministratore e inviare transazioni. Per ulteriori informazioni sulla tua CA e sulle identità che dovrai creare, vedi [Gestione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

In questa esercitazione creiamo due organizzazioni, una che possiederà un peer e un'altra che possiederà un servizio di ordinazione. Ogni organizzazione ha bisogno di una CA per emettere i propri certificati, quindi dobbiamo creare **due CA**. Ai fini di questa esercitazione, **creeremo solo una CA alla volta**.

Guarda il seguente video [ ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://ibm.biz/BlockchainPlatformSeries2 "Deploy a peer on the {{site.data.keyword.blockchainfull_notm}} Platform") per informazioni sul processo di creazione del peer e dell'organizzazione del peer.


### Creazione della CA della tua organizzazione peer
{: #ibp-console-build-network-create-CA-org1CA}

Come parte di questa esercitazione, la CA emette i certificati e le chiavi private per i tuoi utenti e nodi. Queste identità non sono gestite da {{site.data.keyword.IBM_notm}} e le chiavi non vengono memorizzate nella console. Vengono memorizzate solo nell'archiviazione locale del tuo browser. Pertanto, assicurati di esportare le identità e l'MSP della tua organizzazione. Se tenti di accedere alla console da una macchina diversa o da un browser diverso, dovrai importare tali identità e definizioni dell'organizzazione.
{:important}

Per creare la CA che emetterà i certificati per la tua prima organizzazione, attieniti alla seguente procedura nella tua console:

1. Vai alla scheda **Nodi** sulla sinistra e fai clic su **Aggiungi autorità di certificazione**. I pannelli laterali ti consentiranno di personalizzare la CA che vuoi creare e l'organizzazione per cui questa CA emetterà le chiavi.
2. Fai clic su **{{site.data.keyword.cloud_notm}}** in **Crea autorità di certificazione** e quindi su **Avanti**.
3. Utilizza il secondo pannello laterale per fornire alla tua CA un **nome di visualizzazione**. Il nostro valore consigliato per questa CA è `Org1 CA`.
4. Nel pannello successivo, fornisci le tue credenziali di amministratore CA specificando un **ID di iscrizione amministratore CA** di `admin` e un segreto di `adminpw`. Nuovamente, questi sono i **valori consigliati**.
5. Se stai utilizzando un cluster a pagamento, hai l'opportunità di configurare l'allocazione di risorse per il nodo. Ai fini di questa esercitazione, accetta tutti i valori predefiniti e fai clic su **Avanti**. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Se stai utilizzando un cluster gratuito, vedrai la pagina **Riepilogo**.
6. Esamina la pagina Riepilogo e fai quindi clic su **Aggiungi autorità di certificazione**.

**Attività: creazione della CA dell'organizzazione peer**

  | **Campo** | **Nome di visualizzazione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|
  | **Crea CA** | Org1 CA  | admin | adminpw |

  *Figura 3. Creazione della CA dell'organizzazione peer*

Dopo aver distribuito la CA, la utilizzerai per creare l'MSP dell'organizzazione, registrare gli utenti e creare il tuo punto di ingresso a una rete, ossia il **peer**.

Gli utenti avanzati possono già avere una loro CA e non vogliono creare una nuova CA nella console. Se la tua CA esistente può emettere certificati in formato `X.509`, puoi utilizzare la tua CA esterna invece di crearne una nuova qui. Per ulteriori informazioni, vedi questo argomento sull'[utilizzo di certificati da una CA esterna con il tuo peer o servizio di ordinazione](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

### Utilizzo della tua CA per registrare le identità
{: #ibp-console-build-network-use-CA-org1}

Ogni nodo o applicazione che vuoi creare necessita di un certificato e di una chiave privata per partecipare alla rete blockchain. Devi inoltre creare delle identità di amministrazione per questi nodi e applicazioni in modo che tu possa gestirli dalla console. Ripeteremo questo processo due volte, una volta per ogni CA che creiamo. Inoltre, per ogni CA, creerai due identità:

* **Un amministratore dell'organizzazione**: questa identità ti consente di utilizzare i nodi servendoti della console della piattaforma.
* **Un'identità peer**: questa è l'identità del peer stesso. Ogni volta che un peer esegue un'azione (ad esempio, l'approvazione di una transazione) la firmerà utilizzando il proprio certificato.

A seconda del tuo tipo di cluster, la distribuzione della CA può richiedere fino a dieci minuti. Quando la CA viene distribuita la prima volta (oppure quando la CA non è disponibile), la casella nel tile per la CA sarà grigia. Quando la CA è stata distribuita correttamente ed è in esecuzione, questa casella sarà verde, indicando che è "In esecuzione" e può essere utilizzata per registrare le identità. Prima di procedere con i passi indicati di seguito per registrare le identità, devi attendere che lo stato della CA sia "Running".
{:important}

Dopo che la CA è in esecuzione, come indicato dalla casella verde nel tile, genera questi certificati completando la seguente procedura:

1. Fai clic su `Org1 CA` e assicurati che l'identità `admin` che hai creato per la CA sia visibile nella tabella. Successivamente, fai clic sul pulsante **Registra utente**.
2. Per prima cosa registreremo l'amministratore dell'organizzazione, cosa che possiamo fare fornendo un **ID di iscrizione** di `org1admin` e un **segreto** di `org1adminpw`. Quindi imposta il tipo (`Type`) per questa identità su `client` (le identità amministratore dovrebbero sempre essere registrate come `client`, mentre le identità del nodo utilizzando il tipo `peer`). Puoi ignorare il campo **Numero massimo di iscrizioni**. Se vuoi saperne di più sulle iscrizioni, vedi [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Fai clic su **Avanti**.
3. Ai fini di questa esercitazione, non è necessario utilizzare **Aggiungi attributo**. Se vuoi saperne di più sugli attributi, vedi [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). 
4. Dopo che l'amministratore dell'organizzazione è stato registrato, ripeti lo stesso processo per l'identità del peer (utilizzando anche `Org1 CA`). Per l'identità del peer, fornisci un ID di iscrizione di `peer1` e un segreto di `peer1pw`. Questa è un'identità del nodo, quindi seleziona `peer` per il **Tipo**. Puoi ignorare il campo **Numero massimo di iscrizioni** e, al successivo pannello, non assegnare alcun **Attributo**, come hai fatto prima.

La registrazione di queste identità con la CA è solo il primo passo nella **creazione** di un'identità. Non potrai utilizzare queste identità finché non sono state **iscritte**. Per l'identità `org1admin`, ciò avverrà durante la creazione dell'MSP, che vedremo nel prossimo passo. Nel caso del peer, avviene durante la creazione del peer.
{:note}

**Attività: registra gli utenti**

  |  **Campo** | **Descrizione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registra utenti** |  Org1 Admin | org1admin | org1adminpw |
  | | Identità peer |  peer1 | peer1pw |

  *Figura 4. Utilizzo della tua CA per registrare gli utenti*

### Creazione della definizione MSP dell'organizzazione peer
{: #ibp-console-build-network-create-peers-org1}

Ora che abbiamo creato la CA del peer e l'abbiamo utilizzata per **registrare** le identità per l'amministratore `Org1` e per il peer tramite l'associazione a `Org1`, dobbiamo creare una definizione formale dell'organizzazione del peer, che è nota come un MSP. Tieni presente che molti peer possono appartenere a un'organizzazione. **Non hai bisogno di creare una nuova organizzazione ogni volta che crei un peer**. Poiché questa è la prima volta che eseguiamo l'esercitazione, creeremo l'ID MSP per questa organizzazione. Durante il processo di creazione dell'MSP, genereremo i certificati per l'identità `org1admin` e li aggiungeremo al nostro portafoglio.

1. Vai alla scheda **Organizzazioni** nel pannello di navigazione a sinistra e fai clic su **Crea definizione MSP**.
2. Fornisci all'MSP il nome di visualizzazione `Org1 MSP` e l'ID MSP `org1msp`. Se in questo campo vuoi specificare il tuo proprio ID MSP, assicurati di seguire le specifiche sulle limitazioni per questo nome indicate nel suggerimento.
3. In **Dettagli autorità di certificazione (CA) root**, specifica la CA che hai utilizzato per registrare le identità nel passo precedente. Se è la prima volta che esegui questa esercitazione, dovresti vederne solo una: `Org1 CA`.
4. I campi **ID di registrazione** e **Segreto di registrazione** qui sotto verranno compilati automaticamente con l'ID e il segreto di iscrizione per il primo utente che hai creato con la tua CA: `admin` e `adminpw`. Tuttavia, l'utilizzo di questa identità comporterà che la tua organizzazione avrà la stessa identità della CA, il che per motivi di sicurezza non è consigliato. Invece, seleziona l'ID di iscrizione che hai creato per il tuo amministratore dell'organizzazione dall'elenco a discesa, `org1admin` e immetti il relativo segreto associato, `org1adminpw`. Quindi, fornisci a questa identità un nome di visualizzazione, `Org1 Admin`.
5. Fai clic sul pulsante **Genera** per registrare questa identità come amministratore della tua organizzazione ed esportare l'identità nel portafoglio, dove verrà utilizzata durante la creazione del peer e dei canali.
6. Fai clic su **Esporta** per esportare i certificati dell'amministratore nel tuo file system. Come detto sopra, questa identità non viene memorizzata nella tua console o gestita da {{site.data.keyword.IBM_notm}}. Viene memorizzata solo nell'archiviazione del browser locale. Se cambi browser, dovrai importare questa identità nel tuo portafoglio per poter gestire il peer.
7. Fai clic su **Crea definizione MSP**.

**Attività: crea l'MSP dell'organizzazione peer**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione**  | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea organizzazione** | Org1 MSP | org1msp |||
  | **CA root** | Org1 CA ||||
  | **Certificato amministratore dell'organizzazione** | |  | org1admin | org1adminpw |
  | **Identità** | Org1 Admin |||||

  *Figura 5. Crea la definizione MSP dell'organizzazione peer*

Dopo aver creato l'MSP, dovresti essere in grado di vedere l'amministratore dell'organizzazione peer nel tuo **portafoglio**, a cui è possibile accedere facendo clic su **Portafoglio** nel riquadro di navigazione di sinistra.

**Attività: controlla il tuo portafoglio**

  | **Campo** |  **Nome di visualizzazione** | **Descrizione** |
  | ------------------------- |-----------|----------|
  | **Identità** | Org1 Admin | Identità amministratore org1 |

  *Figura 6. Controlla il tuo portafoglio*

Per ulteriori informazioni sugli MSP, vedi [Gestione delle organizzazioni](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Esportare l'identità di amministratore dell'organizzazione è importante perché sei responsabile della gestione e della protezione di questi certificati.
{:important}

### Creazione di un peer
{: #ibp-console-build-network-peer-create}

Dopo aver [creato una CA](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-CA-org1CA), averla utilizza per registrare le identità e aver creato la [MSP dell'organizzazione peer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peers-org1), sei pronto per creare un peer.

#### Che ruolo svolgono i peer?
{: #ibp-console-build-network-peer-role}

È importante ricordare che le organizzazioni stesse non gestiscono i libri mastro. Sono i peer a farlo. Le organizzazioni utilizzano anche i peer per firmare le proposte di transazione e approvare gli aggiornamenti della configurazione del canale. Poiché avere almeno due peer su un canale li rende altamente disponibili, avere almeno due peer uniti a un canale è considerato una prassi ottimale per le implementazioni a livello di produzione. In questa esercitazione, mostreremo solo il processo per la creazione di un singolo peer.

Da una prospettiva di allocazione delle risorse, è possibile unire gli stessi peer a più canali. La progettazione del peer garantisce che i dati di un canale non possano passare a un altro canale attraverso il peer. Tuttavia, poiché il peer memorizzerà un libro mastro separato per ogni canale, è necessario assicurarsi che il peer disponga di potenza di elaborazione e archiviazione sufficienti per gestire il caricamento di dati e transazioni.

#### Distribuzione del tuo peer
{: #ibp-console-build-network-deploy-peer-role}

Utilizza la tua console per completare la seguente procedura:

1. Nella pagina **Nodi**, fai clic su **Aggiungi peer**.
2. Fai clic su {{site.data.keyword.cloud_notm}} in **Crea un nuovo peer** e quindi su **Avanti**.
3. Fornisci al tuo peer un **Nome di visualizzazione** come `Peer Org1`.
4. Nella schermata successiva, seleziona `Org1 CA`, ovvero la CA che hai utilizzato per registrare l'identità del peer. Seleziona l'**ID di iscrizione** per l'identità del peer che hai creato per il tuo peer dall'elenco a discesa, `peer1` e immetti il relativo **segreto** associato, `peer1pw`. Seleziona quindi `Org1 MSP` dall'elenco a discesa e fai clic su **Avanti**.
5. Il pannello laterale successivo richiede informazioni sulla CA TLS. Quando hai creato la CA, è stato creato un TLSCA insieme ad essa. Questa CA viene utilizzata per creare i certificati per il livello di comunicazione sicura con i nodi. Pertanto, seleziona l'**ID di iscrizione** per l'identità del peer che hai creato per il tuo peer dall'elenco a discesa, `peer1` e immetti il **segreto** associato, `peer1pw`. L'opzione **Nome host CSR TLS** è un'opzione disponibile per gli utenti esperti che vogliono specificare un nome di dominio personalizzato che può essere utilizzato per indirizzare l'endpoint del peer. I nomi di dominio personalizzati non fanno parte di questa esercitazione, per cui lascia per ora vuoto il campo **Nome host CSR TLS**.
6. Il successivo pannello laterale ti chiede di **Associare un'identità** per renderla l'amministratore del tuo peer. Ai fini di questa esercitazione, fai in modo che il tuo amministratore dell'organizzazione, `Org1 Admin`, sia anche l'amministratore del tuo peer. È possibile registrare e iscrivere un'identità diversa con `Org1 CA` e rendere tale identità l'amministratore del tuo peer, ma questa esercitazione utilizza l'identità `Org1 Admin`.
7. Se stai utilizzando un cluster a pagamento, nel pannello successivo hai l'opportunità di configurare l'allocazione di risorse per il nodo. Ai fini di questa esercitazione, puoi accettare tutti i valori predefiniti e fare clic su **Avanti**. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Se stai utilizzando un cluster gratuito, vedi la pagina **Riepilogo**.
8. Riesamina il riepilogo e fai clic su **Aggiungi peer**.

**Attività: distribuzione di un peer**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea peer** | Peer Org1 | org1msp |||
  | **CA** | Org1 CA ||||
  | **Identità peer** | |  | peer1 | peer1pw |
  | **Certificato dell'amministratore** | org1msp ||||
  | **CA TLS** | Org1 CA ||||
  | **ID CA TLS** | || admin | adminpw |
  | **Associa identità** | Org1 Admin |||||

  *Figura 7. Distribuzione di un peer*

In uno scenario di produzione, si consiglia di distribuire tre peer per ogni canale. Questo per consentire a un peer di essere inattivo (ad esempio, durante un ciclo di manutenzione) e mantenere ancora i peer altamente disponibili. Per distribuire più di un peer per un'organizzazione, utilizza la stessa CA che hai utilizzato per registrare la tua prima identità del peer. In questa esercitazione, è `Org1 CA`. Successivamente, registra una nuova identità del peer utilizzando un segreto e un ID di iscrizione distinti. Ad esempio, `org1secondpeer` e `org1secondpeerpw`. Successivamente, quando crei il peer, fornisci questi segreto e ID di iscrizione. Poiché il peer è ancora associato a Org1, scegli `Org1 CA`, `Org1 MSP` e `Org1 Admin` dagli elenchi a discesa. Puoi scegliere di fornire a questo nuovo peer un amministratore differente, che può essere registrato ed iscritto con `Org1 CA`, ma questa opzione è facoltativa. Queste serie di esercitazioni mostreranno soltanto il processo di creazione di un singolo peer per ogni organizzazione del peer.
{:tip}

## Passo due: crea il nodo che ordina le transazioni
{: #ibp-console-build-network-create-orderer}

In altre blockchain distribuite, come Ethereum e Bitcoin, non esiste un'autorità centrale che ordini le transazioni e le invii ai peer. Hyperledger Fabric, la blockchain su cui si basa {{site.data.keyword.blockchainfull_notm}} Platform, funziona in modo diverso. Offre un nodo o un cluster di nodi, denominato **servizio di ordinazione**.

Il servizio di ordinazione è un componente chiave in una rete perché esegue alcune funzioni essenziali:

- **Ordinano** letteralmente i blocchi di transazioni che vengono inviati ai peer per essere scritti nei loro libri mastro. Questo processo è chiamato "ordinazione".
- Gestiscono il **canale del sistema di ordinazione**, il luogo in cui risiede il **consorzio**, ossia l'elenco delle organizzazioni peer autorizzate a creare canali. Un consorzio è essenzialmente un veicolo multi-tenancy e un singolo servizio ordini, per progettazione, può ospitare più consorzi.
- **Applicano le politiche** stabilite dal consorzio o dagli amministratori del canale. Queste politiche dettano tutto, da chi può leggere o scrivere su un canale a chi può creare o modificare un canale. Ad esempio, quando un partecipante alla rete chiede di modificare una politica del canale o del consorzio, il servizio ordini elabora la richiesta per verificare se il partecipante dispone dei diritti amministrativi appropriati per tale aggiornamento di configurazione, la convalida rispetto alla configurazione esistente, genera una nuova configurazione e la trasmette ai peer.

Per ulteriori informazioni sui servizi di ordinazione e il loro ruolo nelle reti basate su Hyperledger Fabric, vedi [The Ordering Service ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html).

In un cluster a pagamento, hai l'opzione di creare un servizio di ordinazione con un nodo (sufficiente per gli scopi di test) e un servizio di ordinazione con tolleranza agli errori anomali che utilizza cinque nodi collegati a una sola organizzazione. In un cluster gratuito, potrai soltanto creare un servizio di ordinazione con un nodo. In questa esercitazione, mostreremo il servizio di ordinazione con cinque nodi.

Tuttavia, proprio come con il peer, prima di poter creare un servizio di ordinazione, dobbiamo creare una CA per fornire le identità e l'MSP della nostra organizzazione del servizio di ordinazione.

Guarda il seguente video [ ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://ibm.biz/BlockchainPlatformSeries3 "Deploy an ordering service on the {{site.data.keyword.blockchainfull_notm}} Platform") per informazioni sul processo di creazione del servizio di ordinazione e dell'organizzazione del servizio di ordinazione.

### Ordine nella console
{: #ibp-console-build-network-ordering-console}

In questa release, i servizi di ordinazione distribuiti, in cui più organizzazioni contribuiscono ai nodi di un servizio di ordinazione, non sono supportati. Ogni nodo di ordinazione nel servizio di ordinazione sarà amministrato da una sola organizzazione.

Il servizio di ordinazione al livello di produzione disponibile è un servizio di ordinazione con tolleranza di errori anomali (CFT) basato su un'implementazione del protocollo Raft in `etcd`. Raft segue un modello “leader e follower”, dove un nodo leader viene eletto (per canale) e le sue decisioni vengono replicate dai follower. I servizi di ordinazione Raft dovrebbero essere più facili da configurare e gestire rispetto ai servizi di ordinazione basati su Kafka e la loro progettazione consente a diverse organizzazioni di contribuire ai nodi per un servizio di ordinazione distribuito. Per ulteriori informazioni su Raft, vedi [The Ordering Service ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft).

Al momento, l'unica configurazione con tolleranza di errori anomali (CFT) dei nodi di ordinazione disponibile è con **cinque** nodi. Mentre è tecnicamente possibile creare un servizio di ordinazione con tolleranza di errori anomali come appena tre nodi (i servizi di ordinazione con due nodi sono fortemente sconsigliati), disporre di soli tre nodi crea potenzialmente dei problemi quando i nodi vengono disattivati, ad esempio durante l'upgrade.

Di conseguenza, i cluster a pagamento potranno scegliere solo tra uno o cinque nodi. Le reti di produzione dovrebbero scegliere l'opzione di cinque nodi, perché un servizio di ordinazione con un nodo, per definizione, non dispone della tolleranza di errori anomali.

In questa esercitazione, creeremo un servizio di ordinazione con cinque nodi.

### Creazione della tua CA dell'organizzazione del servizio di ordinazione
{: #ibp-console-build-network-create-orderer-ca}

Il processo di creazione di una CA per un servizio di ordinazione è identico a quello utilizzato per un peer.

1. Vai alla scheda **Nodi** e fai clic su **Aggiungi autorità di certificazione**.
2. Fai clic su **{{site.data.keyword.cloud_notm}}** in **Crea una nuova autorità di certificazione** e quindi su **Avanti**
3. Fornisci a questa CA un nome di visualizzazione univoco, `Ordering Service CA`.
4. Sei libero di riutilizzare l'**ID di iscrizione amministratore CA** di `admin` e un segreto di `adminpw`. Poiché si tratta di una CA diversa, questa identità è distinta dall'identità dell'amministratore della CA creata per `Org1 CA`, anche se l'ID e il segreto sono identici.
5. Se stai utilizzando un cluster a pagamento, nel pannello successivo hai l'opportunità di configurare l'allocazione di risorse per la CA. Ai fini di questa esercitazione, accetta tutti i valori predefiniti e fai clic su **Avanti**. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources).  Se stai utilizzando un cluster gratuito, vedrai la pagina **Riepilogo**.
6. Esamina la pagina Riepilogo e fai quindi clic su **Aggiungi autorità di certificazione**.

Come per il peer, gli utenti avanzati possono già avere una loro CA e non vogliono creare una nuova CA utilizzando la console. Se la tua CA esistente può emettere certificati in formato `X.509`, puoi utilizzare la tua CA esterna invece di crearne una nuova qui. Per ulteriori informazioni, vedi questo argomento sull'[utilizzo di certificati da una CA esterna con il tuo peer o servizio di ordinazione](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

### Utilizzo della tua CA per registrare il nodo del servizio di ordinazione e le identità amministratore del servizio di ordinazione
{: #ibp-console-build-network-use-CA-orderer}

Come abbiamo fatto con il peer, dobbiamo registrare due identità con la nostra CA del servizio di ordinazione.  Dopo aver selezionato la tua CA, dovrai registrare un amministratore per la nostra organizzazione del servizio di ordinazione e un'identità per il servizio di ordinazione stesso. Come prima, dovresti vedere un'identità sulla scheda `Ordering Service CA`; è l'amministratore che hai creato per la CA.

A seconda del tuo tipo di cluster, la distribuzione della CA può richiedere fino a dieci minuti. Quando la CA viene distribuita la prima volta (oppure quando la CA non è disponibile), la casella nel tile per la CA sarà grigia. Quando la CA è stata distribuita correttamente ed è in esecuzione, questa casella sarà verde, indicando che è "In esecuzione" e può essere utilizzata per registrare le identità. Prima di procedere con i passi indicati di seguito per registrare le identità, devi attendere che lo stato della CA sia "Running".
{:important}

Dopo che la CA è in esecuzione, come indicato dalla casella verde nel tile per `Ordering Service CA`, genera questi certificati completando la seguente procedura:

1. Fai clic su `Ordering Service CA` nella scheda **Nodi** e assicurati che l'identità `admin` che hai creato per la CA sia visibile nella tabella. Successivamente, fai clic sul pulsante **Registra utente**.
2. Per prima cosa registreremo l'amministratore dell'organizzazione, cosa che possiamo fare fornendo un **ID di iscrizione** di `OSadmin` e un **segreto** di `OSadminpw`. Quindi imposta il tipo (`Type`) per questa identità su `client` (le identità amministratore dovrebbero sempre essere registrate come `client`, mentre le identità del nodo utilizzando il tipo `peer`). Puoi ignorare il campo **Numero massimo di iscrizioni**. Se vuoi saperne di più sulle iscrizioni, vedi [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Fai clic su **Avanti**.
3. Ai fini di questa esercitazione, non è necessario utilizzare **Aggiungi attributo**. Se vuoi saperne di più sugli attributi, vedi [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). 
4. 5. Dopo che l'amministratore dell'organizzazione è stato registrato, ripeti lo stesso processo per l'identità del servizio di ordinazione (utilizzando anche `Ordering Service CA`). Per le identità del nodo del servizio di ordinazione, fornisci un ID di iscrizione di `OS1` e un segreto di `OS1pw`. Questa è un'identità del nodo, quindi seleziona `peer` per il **Tipo**. Puoi ignorare il campo **Numero massimo di iscrizioni** e, al successivo pannello, non assegnare alcun **Attributo**, come hai fatto prima.

**Attività: crea una CA e registra gli utenti**

  | **Campo** | **Descrizione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea CA** | CA del servizio di ordinazione | admin | adminpw |
  | **Registra utenti** | Amministratore del servizio di ordinazione | OSadmin | OSadminpw |
  |  | Identità del nodo del servizio di ordinazione |  OS1 | OS1pw |

*Figura 8. Crea una CA e registra gli utenti*

Ai fini di questa esercitazione, stiamo creando solo un'identità del nodo. Questa identità sarà utilizzata da tutti i cinque nodi che distribuiremo per creare il servizio di ordinazione. Mentre potresti non volerlo fare in un servizio di ordinazione con più organizzazioni, qui è accettabile dato che tutti i nodi di ordinazione sono gestiti dalla stessa organizzazione.

### Creazione della definizione MSP dell'organizzazione del servizio di ordinazione
{: #ibp-console-build-network-create-orderer-org-msp}

Crea la definizione MSP dell'organizzazione del servizio di ordinazione e specifica l'identità di amministratore per l'organizzazione. Dopo aver registrato l'amministratore e gli utenti del servizio di ordinazione, dobbiamo creare l'ID MSP e iscrivere l'utente `OSadmin` che abbiamo registrato come amministratore della nostra organizzazione.

1. Vai alla scheda **Organizzazioni** nel pannello di navigazione a sinistra e fai clic su **Crea definizione MSP**.
2. Fornisci alla definizione MSP il nome di visualizzazione `Ordering Service MSP` e l'ID MSP `osmsp`. Se scegli un ID MSP differente, assicurati di seguire le specifiche sulle limitazioni per questo nome indicate nel suggerimento.
3. In **Dettagli autorità di certificazione (CA) root**, seleziona la `Ordering Service CA` che abbiamo creato.
4. I campi **ID di registrazione** e **Segreto di registrazione** qui sotto verranno compilati automaticamente con l'ID e il segreto di iscrizione per il primo utente che hai creato con la tua CA: `admin` e `adminpw`. Tuttavia, l'utilizzo di questa identità comporterà che la tua organizzazione avrà la stessa identità della CA, il che per motivi di sicurezza non è consigliato. Invece, seleziona l'ID di iscrizione che hai creato per il tuo amministratore dell'organizzazione dall'elenco a discesa, `OSadmin` e immetti il relativo segreto associato, `OSadminpw`. Quindi, fornisci a questa identità un nome di visualizzazione, `Ordering Service Admin`.
5. Fai clic sul pulsante **Genera** per registrare questa identità come amministratore della tua organizzazione ed esportare l'identità nel portafoglio.
6. Fai clic su **Esporta** per esportare i certificati dell'amministratore nel tuo file system. Come detto sopra, questa identità non viene memorizzata nella tua console o gestita da {{site.data.keyword.IBM_notm}}. Viene memorizzata solo nel tuo browser. Se cambi browser, dovrai importare questa identità per poter gestire il servizio di ordinazione.
7. Fai clic su **Crea definizione MSP**.

**Attività: crea la definizione MSP dell'organizzazione del servizio di ordinazione**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione**  | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea organizzazione** | MSP del servizio di ordinazione | osmsp |||
  | **CA root** | CA del servizio di ordinazione ||||
  | **Certificato amministratore dell'organizzazione** | |  | OSadmin | OSadminpw |
  | **Identità** | Amministratore del servizio di ordinazione |||||

  *Figura 9. Crea la definizione MSP dell'organizzazione del servizio di ordinazione*

Dopo aver creato l'MSP, dovresti essere in grado di vedere l'amministratore dell'organizzazione del servizio di ordinazione nel tuo **portafoglio**, a cui è possibile accedere facendo clic su **Portafoglio** nel riquadro di navigazione di sinistra.

**Attività: controlla il tuo portafoglio**

  | **Campo** |  **Nome di visualizzazione** | **Descrizione** |
  | ------------------------- |-----------|----------|
  | **Identità** | Org1 Admin | Identità amministratore org1 |
  | **Identità** | Amministratore del servizio di ordinazione | Identità dell'amministratore del servizio di ordinazione |

  *Figura 10. Crea la definizione MSP dell'organizzazione del servizio di ordinazione*

Per ulteriori informazioni sugli MSP, vedi [Gestione delle organizzazioni](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Esportare l'identità di amministratore dell'organizzazione è importante perché sei responsabile della gestione e della protezione di questi certificati. Se esporti il servizio di ordinazione e la definizione MSP del servizio di ordinazione, puoi importarli in un'altra console in cui un altro operatore può creare nuovi canali sul servizio di ordinazione o unire i peer al canale.
{:important}

### Creazione di un servizio di ordinazione
{: #ibp-console-build-network-create-an-orderer}

Completa la seguente procedura dalla tua console:

1. Nella pagina **Nodi**, fai clic su **Aggiungi servizio di ordinazione**.
2. Fai clic sul pulsante {{site.data.keyword.cloud_notm}} in **Crea un nuovo servizio di ordinazione IBM Cloud** e quindi su **Avanti**.
3. Fornisci al tuo servizio di ordinazione un **Nome di visualizzazione** di `Ordering Service` e, se sei in un cluster a pagamento, scegli se vuoi che il tuo servizio di ordinazione abbia un nodo (sufficiente per il test) o cinque nodi (buono per la produzione). Scegli **cinque nodi**. Non scegliere di utilizzare una CA esterna. Questa è un'opzione avanzata.
4. Nel pannello successivo, seleziona `Ordering Service CA` come tua CA. Seleziona quindi l'**ID di iscrizione** per l'identità del nodo che hai creato per il tuo servizio di ordinazione dall'elenco a discesa, `OS1` e immetti il **segreto** associato, `OS1pw`. Seleziona quindi la tua MSP, `Ordering Service MSP` dall'elenco a discesa. Ai fini di questa esercitazione, non scegliere di utilizzare una CA esterna per il tuo servizio di ordinazione, se desideri ulteriori informazioni, vedi [Utilizzo di certificati da una CA esterna](#ibp-console-build-network-third-party-ca). Fai clic su **Avanti**.
5. Il pannello laterale successivo richiede informazioni sulla CA TLS. Quando hai creato la CA, è stata creata una CA TLS insieme ad essa. Questa CA viene utilizzata per creare i certificati per il livello di comunicazione sicura con i nodi. Pertanto, seleziona l'**ID di iscrizione** per l'identità del servizio di ordinazione che hai creato dall'elenco a discesa, `OS1` e immetti il **segreto** associato, `OS1pw`. L'opzione **Nome host CSR TLS** è un'opzione disponibile per gli utenti esperti che vogliono specificare un nome di dominio personalizzato che può essere utilizzato per indirizzare l'endpoint del servizio di ordinazione. I nomi di dominio personalizzati non fanno parte di questa esercitazione, per cui lascia per ora vuoto il campo **Nome host CSR TLS**.
6. Il passo **Associa identità** ti consente di scegliere un amministratore per il tuo servizio di ordinazione. Seleziona `Ordering Service Admin` come prima e fai clic su **Avanti**.
7. Se stai utilizzando un cluster a pagamento, nel pannello successivo hai l'opportunità di configurare l'allocazione di risorse per il nodo. Ai fini di questa esercitazione, puoi accettare tutti i valori predefiniti e fare clic su **Avanti**. Le selezioni che effettui qui vengono applicate a tutti e cinque i nodi di ordinazione. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources).
7. Riesamina la pagina Riepilogo e fai clic su **Aggiungi servizio di ordinazione**.

**Attività: crea un servizio di ordinazione**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea un servizio di ordinazione** | Servizio di ordinazione | osmsp |||
  | **CA** | CA del servizio di ordinazione ||||
  | **Identità del servizio di ordinazione** | |  | OS1 | OS1pw |
  | **Certificato dell'amministratore** | MSP del servizio di ordinazione ||||
  | **CA TLS** | CA del servizio di ordinazione ||||
  | **ID CA TLS** | || admin | adminpw |
  | **Associa identità** | Amministratore del servizio di ordinazione |||||

  *Figura 11. Crea un servizio di ordinazione*

Una volta che il servizio di ordinazione è stato creato, puoi vederlo nel pannello **Nodi**.

## Passo tre: aggiungi la tua organizzazione all'elenco di organizzazioni che possono effettuare transazioni
{: #ibp-console-build-network-add-org}

Come notato in precedenza, un'organizzazione peer deve essere nota al servizio di ordinazione prima di poter creare o unirsi a un canale (processo noto anche come unione al "consorzio", l'elenco di organizzazioni note al servizio di ordinazione). Questo perché, a un livello tecnico, i canali sono dei **percorsi di messaggistica** tra i peer attraverso il servizio di ordinazione. Proprio come un peer può essere unito a più canali senza che le informazioni passino da un canale a un altro, così un servizio di ordinazione può disporre di più canali in esecuzione al suo interno senza esporre i dati alle organizzazioni su altri canali.

Poiché solo gli amministratori del servizio di ordinazione possono aggiungere organizzazioni peer al consorzio, dovrai **essere** l'amministratore del servizio di ordinazione o dovrai **inviare** le informazioni MSP all'amministratore del servizio di ordinazione. 

Guarda il seguente video [ ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://ibm.biz/BlockchainPlatformSeries4 "Create and join a channel on the {{site.data.keyword.blockchainfull_notm}} Platform") per informazioni sul processo per aggiungere l'organizzazione al consorzio, creare il canale e unire il tuo peer al canale.

Poiché hai creato l'amministratore del servizio di ordinazione utilizzando la console, questo processo è relativamente semplice:
1. Vai alla scheda **Nodi**.
2. Scorri verso il basso fino al servizio di ordinazione che hai creato e fai clic su di esso per aprirlo.
3. In **Membri del consorzio**, fai clic su **Aggiungi organizzazione**.
4. Dall'elenco a discesa, seleziona `Org1 MSP`, in quanto questa è l'MSP che rappresenta l'organizzazione del peer: `org1`.
5. Fai clic su **Aggiungi organizzazione**.

Una volta completato questo processo, `org1` può creare o unirsi a un canale ospitato sul tuo `Ordering Service`.

In questa esercitazione, possiamo accedere facilmente a `Org1 MSP` perché sia l'organizzazione peer che l'organizzazione del servizio di ordinazione sono state create nella stessa console. In uno scenario di produzione, verrebbero create delle definizioni MSP di un'altra organizzazione da operatori di rete diversi nel proprio cluster utilizzando la propria console {{site.data.keyword.blockchainfull_notm}}. In questi casi, quando l'organizzazione vuole unirsi al tuo consorzio, la definizione MSP dell'organizzazione dovrà essere inviata alla tua console in un'operazione fuori banda. Inoltre, dovrai esportare il tuo servizio di ordinazione e inviarlo loro in modo che lo possano importare nella propria console e unire un peer a un canale (o creare un nuovo canale). Questo processo viene descritto nell'Esercitazione: unisciti a una rete in [Esportazione delle informazioni della tua organizzazione](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

## Passo quattro: crea un canale
{: #ibp-console-build-network-create-channel}

Per informazioni su come aggiornare un canale, vedi [Aggiornamento di una configurazione del canale](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-update-channel).

Sebbene i membri di una rete siano in genere entità di business correlate che desiderano effettuare transazioni tra loro, potrebbero esserci casi in cui sottoinsiemi di membri desiderano effettuare transazioni senza la conoscenza degli altri. Ciò è possibile creando un **canale** sul quale si svolgeranno queste transazioni. I canali replicano la struttura di una rete blockchain in quanto contengono membri, peer, un servizio ordini, un libro mastro, politiche e smart contract. Tuttavia, limitando l'adesione, e persino la conoscenza del canale, a particolari sottoinsiemi dell'adesione alla rete, i canali garantiscono che i membri della rete possano sfruttare la struttura complessiva della rete mantenendo al contempo la privacy, laddove necessario.

Come notato in precedenza, per unire un peer da `org1` a un canale, è necessario prima aggiungere `org1` al consorzio. Se l'organizzazione non è un membro del consorzio al momento della creazione del canale, è possibile creare il canale e aggiungere l'organizzazione in un secondo momento facendo clic sul pulsante **Impostazioni** nella pagina del canale pertinente e passando attraverso il flusso di **aggiornamento del canale**.

Per ulteriori informazioni sui canali e su come utilizzarli, consulta la [documentazione di Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channels.html).

Guarda il precedente video 3 per informazioni sul processo per creare il canale e unire il tuo peer ad esso.


<!--
Note that even though the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 uses Hyperledger Fabric v1.4 binaries, because the [gossip protocol ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html) is not being used with the console, Fabric functionalities that leverage gossip, such as [Private Data ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html)] and [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html)], are not available.
-->

### Creazione di un canale: `channel1`
{: #ibp-console-build-network-create-channel1}

Poiché la console utilizza i peer per raccogliere informazioni sui canali a cui appartengono i peer, **a meno che un'organizzazione non abbia unito un peer a un canale, non può interagire con il canale**.

Quando hai creato le CA, le identità, gli MSP, il servizio di ordinazione e il peer, vai alla scheda **Canali** nel pannello di navigazione a sinistra. Qui è dove vengono effettuate la creazione e la gestione dei canali.

Quando accedi a questa scheda per la prima volta, sarà vuota ad eccezione dei pulsanti **Crea canale** e **Unisci a canale**. Questo perché non hai ancora creato un canale e unito un peer ad esso.

#### Creazione del canale
{: #ibp-console-build-network-channels-create}

Completa la seguente procedura dalla tua console:

1. Fai clic su **Crea canale**. Si aprirà un pannello laterale.
2. Fornisci un **nome** per il canale, ad esempio `channel1`. Prendi nota di questo valore in quanto dovrai condividerlo con chiunque voglia unirsi a questo canale.
3. Seleziona `Ordering Service` dall'elenco a discesa.
4. Scegli le **Organizzazioni** che faranno parte di questo canale. Poiché abbiamo creato solo un'organizzazione, sarà `Org1 MSP (org1msp)`. Rendi questa organizzazione un **Operatore**. Nota: non utilizzare `Ordering Service MSP` qui.
5. Scegli una **Politica di aggiornamento del canale** per il canale. Questa è la politica che determinerà quante organizzazioni dovranno approvare gli aggiornamenti alla configurazione del canale. Poiché questa esercitazione implica la creazione in una sola organizzazione, questa politica deve essere `1 out of 1`. Quando aggiungi le organizzazioni al canale, dovresti modificare questa politica per rispecchiare i bisogni del tuo caso di utilizzo. Uno standard ragionevole è di utilizzare una maggioranza di organizzazioni. Ad esempio, `3 of 5`.
6. Specifica tutte le limitazioni del **Controllo dell'accesso** che vuoi inserire. Nota: questa è un'**opzione avanzata**. Se imposti l'accesso a una risorsa a una particolare organizzazione, sarà limitato l'accesso a tale risorsa per tutte le organizzazioni. Ad esempio, se l'accesso predefinito a una particolare risorsa è per i lettori (`Readers`) di tutte le organizzazioni e tale accesso viene modificato per l'amministratore (`Admin`) di `Org1`, **solo** l'amministratore di Org1 avrà accesso a tale risorsa. Poiché l'accesso ad alcune risorse è fondamentale per il perfetto funzionamento di un canale, è altamente consigliato di prendere attentamente le decisioni sul controllo dell'accesso. Se decidi di limitare l'accesso a una risorsa, assicurati che l'accesso a tale risorsa venga aggiunto, se necessario, a tutte le organizzazioni.
7. Seleziona l'**Organizzazione creatore canale**. Poiché la console consente la gestione di più organizzazioni da parte di un singolo utente, è necessario specificare quale organizzazione sta creando il canale. Poiché questa esercitazione è limitata alla creazione di una sola organizzazione, scegli `Org1 MSP` dall'elenco a discesa. Altrimenti, scegli `Org1 Admin` come l'identità che crea il canale.

Quando sei pronto, fai clic su **Invia proposta**. Ritornerai alla scheda Canali e potrai vedere un tile in sospeso del canale che hai appena creato.

**Attività: crea un canale**

  |  **Campo** | **Nome** |
  | ------------------------- |-----------|
  | **Nome canale** | channel1 |
  | **Servizio di ordinazione** | Servizio di ordinazione |
  | **Organizzazioni** | Org1 MSP |
  | **Politica di aggiornamento canale** | 1 su 1 |
  | **Elenco di controllo dell'accesso** | Nessuno|
  | **Organizzazione creatore canale** | Org1 MSP |
  | **Identità portafoglio** | Org1 Admin|

*Figura 12. Crea un canale*

Il prossimo passo è quello di unire un peer a questo canale.

## Passo cinque: unisci il tuo peer al canale
{: #ibp-console-build-network-join-peer}

Abbiamo quasi finito. L'unione del peer al canale è l'ultimo passo per la configurazione dell'infrastruttura di base per la tua rete. Se non ci sei già, vai alla scheda **Canali** nel pannello di navigazione a sinistra.

Completa la seguente procedura dalla tua console:

1. Fai clic sul tile in sospeso `channel1` per avviare i pannelli laterali.
2. Seleziona i peer che vuoi unire al canale. Ai fini di questa esercitazione, fai clic su `Peer Org1`.
3. Fai clic su **Unisci a canale**.

## Passi successivi
{: #ibp-console-build-network-next-steps}

Dopo aver creato e unito il tuo peer a un canale, hai una rete blockchain di base completamente funzionale che puoi utilizzare per lo sviluppo e il test. Utilizza le seguenti procedure per distribuire uno smart contract e iniziare a inviare transazioni alla blockchain:

- [Distribuisci uno smart contract sulla tua rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) utilizzando la console.
- Dopo aver installato e istanziato il tuo smart contract, puoi [inviare le transazioni utilizzando la tua applicazione client](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilizza [l'esempio di commercial paper](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) per distribuire uno smart contract di esempio e inviare le transazioni utilizzando il codice dell'applicazione di esempio.

Puoi anche creare un'altra organizzazione peer utilizzando l'[Esercitazione: unisciti a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-structure). Puoi aggiungere la seconda organizzazione al tuo canale per simulare una rete distribuita, con due peer che condividono un singolo libro mastro del canale.

## Utilizzo di certificati da una CA esterna con il tuo peer o servizio di ordinazione
{: #ibp-console-build-network-third-party-ca}

Invece di utilizzare una CA (Certificate Authority) {{site.data.keyword.blockchainfull_notm}} Platform come CA del tuo peer o servizio di ordinazione, puoi utilizzare i certificati da una CA esterna, una che non è ospitata da {{site.data.keyword.IBM_notm}}, a condizione che emetta certificati in formato [X.509 ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html#digital-certificates "Certificati digitali").

### Prima di cominciare
{: #ibp-console-build-network-third-party-ca-prereq}

1. Devi raccogliere le seguenti informazioni sui certificati e salvarle in singoli file che possono essere caricati nella console.   
**Nota:** i certificati all'interno dei file possono essere in formato `PEM` o in formato con codifica base64 (`base64 encoded`).
 * **Certificato d'identità del peer o del servizio di ordinazione** Questo è il certificato di firma dalla tua CA esterna che verrà utilizzato dal tuo peer o servizio di ordinazione.
 * **Chiave privata d'identità del peer o del servizio di ordinazione**  Questa è la chiave privata corrispondente al certificato firmato dalla tua CA di terze parti che verrà utilizzata da questo peer o servizio di ordinazione.
 * **Definizione MSP dell'organizzazione del peer o del servizio di ordinazione** Devi generare manualmente questo file utilizzando le istruzioni fornite in [Creazione manuale di un file JSON MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-build-msp).
 * **Certificato CA TLS** Questo è il certificato di firma pubblico creato dalla tua CA TLS esterna che verrà utilizzato da questo peer o servizio di ordinazione.
  * **Chiave privata CA TLS** Questa è la chiave privata corrispondente al certificato firmato dalla tua CA TLS che verrà utilizzata da questo peer o servizio di ordinazione per proteggere le comunicazioni con altri membri sulla rete.
 * **Certificato root CA TLS** (Facoltativo) Questo è il certificato della tua CA TLS esterna. Devi fornire un certificato root CA TLS o un certificato CA TLS intermedio; puoi anche fornire entrambi.
 * **Certificato TLS intermedio**: (Facoltativo) Questo è il certificato TLS se il tuo certificato TLS viene emesso da una CA TLS intermedia. Carica il certificato CA TLS intermedio. Devi fornire un certificato root CA TLS o un certificato CA TLS intermedio; puoi anche fornire entrambi.
 * **Certificato d'identità amministratore peer o servizio di ordinazione** Questo è il certificato di firma dalla tua CA esterna che verrà utilizzato dall'identità amministratore su questo peer o servizio di ordinazione. Questo certificato è noto anche come tua chiave d'identità amministratore peer o servizio di ordinazione.
 * **Chiave privata d'identità amministratore peer o servizio di ordinazione**  Questa è la chiave privata corrispondente al certificato firmato dalla tua CA esterna che verrà utilizzato dall'identità amministratore di questo peer o servizio di ordinazione.

2. Importa il file di definizione MSP dell'organizzazione del peer o del servizio di ordinazione generato nella console facendo clic sulla scheda **Organizzazioni** seguito da **Importa definizione dell'MSP**.

Ora hai la scelta di creare un peer o un nodo del servizio di ordinazione con un solo nodo, oppure, se disponi di un cluster a pagamento, un servizio di ordinazione con cinque nodi.

### Opzione 1: crea un nuovo peer o servizio di ordinazione con un solo nodo utilizzando i certificati da una CA esterna
{: #ibp-console-build-network-create-peer-orderer-third-party-ca-}

Puoi passare all'**Opzione 2** se vuoi creare un nuovo servizio di ordinazione con cinque nodi. Le seguenti istruzioni sono solo per la creazione di un peer o servizio di ordinazione con un solo nodo utilizzando i certificati dalla tua CA esterna.
{:note}

Nota che hai raccolto tutti i certificati necessari, sei pronto a creare un peer o un servizio di ordinazione che utilizza tali certificati. Attieniti alle istruzioni per creare il peer o il servizio di ordinazione con un solo nodo con i certificati da una CA esterna.

1. Nella scheda **Nodi**, fai clic su **Aggiungi peer**  o **Aggiungi servizio di ordinazione**.
2. Dopo aver immesso un nome di visualizzazione per il nodo, seleziona l'opzione per utilizzare una CA esterna.
3. Scorri i pannelli e carica i file corrispondenti alle informazioni sul certificato che hai raccolto.
4. Assicurati di selezionare la definizione MSP dell'organizzazione del peer o del servizio di ordinazione che hai importato nella console dall'elenco a discesa.
5. Nell'ultimo passo, quando ti viene chiesto di associare un'identità al tuo peer o servizio di ordinazione, devi fare clic su **Nuova identità**.
6. Specifica qualsiasi valore come **Nome di visualizzazione** per questa identità. Il nome di visualizzazione sarà visibile nel portafoglio dopo che hai creato il nodo.
7. Nel campo **Certificati**, carica il file che contiene il **Certificato d'identità amministratore del peer o del servizio di ordinazione**.
8. Nel campo **Chiave privata**, carica il file che contiene la **Chiave privata d'identità amministratore del peer o del servizio di ordinazione**.
9. Esamina le informazioni nella pagina Riepilogo e fai clic su **Aggiungi peer** o **Aggiungi servizio di ordinazione**.

### Opzione 2: crea un servizio di ordinazione con cinque nodi utilizzando i certificati da una CA esterna
{: #ibp-console-build-network-create-five-node}

Quando disponi di un cluster Kubernetes a pagamento, hai l'opzione aggiuntiva di distribuire un servizio di ordinazione con cinque nodi che utilizza il protocollo di consenso Raft.  Prima di distribuire un servizio di ordinazione con cinque nodi, devi creare un file JSON che contiene tutti i certificati per i cinque nodi.

#### Crea il file JSON di certificati
{: #ibp-console-build-network-create-certs-file}

Il file JSON di certificati richiesto contiene un array di cinque voci `msp`, in cui ogni elemento dell'array contiene i certificati per uno dei nodi di ordinazione. In circostanze normali, ogni nodo userebbe la stessa serie di certificati. Ma, hai l'opzione di specificare certificati differenti per ogni nodo. I certificati nella sezione `component` rappresentano i certificati per il nodo stesso, mentre la sezione `tls` include i certificati emessi dalla CA TLS.  

- **keystore**: la chiave privata per questo nodo
- **signcerts**: la chiave pubblica (nota anche come certificato di firma o di iscrizione) assegnata dalla CA per questo nodo.
- **cacerts**: il certificato della CA root.
- **admincerts**: il certificato degli utenti amministratori del nodo. Questo potrebbe anche essere l'amministratore dell'organizzazione.
- **intermediatecerts**: se la tua rete include CA a più livelli, incollale nel certificato della CA intermedia. Se non hai utilizzato un certificato intermedio, puoi lasciare questo campo vuoto.

Utilizzando i certificati raccolti in precedenza, incolla il certificato corrispondente nei seguenti campi, in formato base64.

Puoi convertire il contenuto del tuo file certificato, `<cert.pem>`, dal formato `PEM` a una stringa in formato base64 eseguendo questo comando sulla tua macchina locale:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}

```
[
    {
        "msp": {
            "component": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "admincerts": [],
                "intermediatecerts": []
            },
        "tls": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "intermediatecerts": []
            }
        }
    },
    {
        "msp": {
            "component": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "admincerts": [],
                "intermediatecerts": []
            },
        "tls": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "intermediatecerts": []
            }
        }
    },
    {
        "msp": {
            "component": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "admincerts": [],
                "intermediatecerts": []
            },
        "tls": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "intermediatecerts": []
            }
        }
    },
    {
        "msp": {
            "component": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "admincerts": [],
                "intermediatecerts": []
            },
        "tls": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "intermediatecerts": []
            }
        }
    },
    {
        "msp": {
            "component": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "admincerts": [],
                "intermediatecerts": []
            },
        "tls": {
                "keystore": [],
                "signcerts": [],
                "cacerts": [],
                "intermediatecerts": []
            }
        }
    }
]
```
{:codeblock}

Salva questa definizione come un file `JSON`.

#### Crea il servizio di ordinazione e utilizza i certificati dalla CA esterna per ogni nodo di ordinazione.
{: #ibp-console-build-network-create-five-node-os}

Ora che hai creato un file JSON con tutti i certificati per i nodi di ordinazione, sei pronto a creare il servizio di ordinazione.

1. Nella scheda **Nodi**, fai clic su **Aggiungi servizio di ordinazione**.
2. Immetti un solo **Nome di visualizzazione** per i cinque nodi di ordinazione. Il nome di visualizzazione che fornisci sarà il prefisso di ogni nome del nodo di ordinazione e gli sarà accodato un numero.
3. In **Numero di nodi di ordinazione**, seleziona **Cinque nodi di ordinazione**.
4. Seleziona **Voglio utilizzare i certificati da un'autorità di certificazione esterna** e fai clic su **Avanti**.
5. Fai clic su **Aggiungi file** per caricare il file JSON che contiene tutti i certificati.
6. Seleziona la definizione **MSP organizzazione** che hai importato.  
7. Poiché stai utilizzando un cluster a pagamento, nel pannello successivo hai l'opportunità di configurare l'allocazione di risorse per i nodi. Le selezioni che effettui qui vengono applicate a tutti e cinque i nodi di ordinazione. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources).
8. Riesamina il riepilogo e fai clic su **Aggiungi servizio di ordinazione**.

### Operazioni successive
{: #ibp-console-build-network-third-party-ca-next}

Hai raccolto tutti i tuoi certificati del peer o del servizio di ordinazione dalla tua CA di terze parti, creato la loro definizione MSP dell'organizzazione corrispondente e creato un peer o un servizio di ordinazione. Se ti stai attenendo all'ordine indicato nelle esercitazioni, puoi andare al passo successivo.
- Se hai creato il nodo peer, il passo successivo consiste nel [creare il nodo che ordina le transazioni](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-orderer).
- Se hai creato il nodo per unirti a una rete esistente, il passo successivo consiste nell'[aggiungere la tua organizzazione all'elenco di organizzazioni che possono effettuare transazioni](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2).
- Se hai creato un servizio di ordinazione, il passo successivo consiste nel [creare un canale](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel).

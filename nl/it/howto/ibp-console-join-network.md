---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: getting started tutorials, create a CA, enroll, register, create an MSP, wallet, create a peer, create ordering service, Raft, join a network, system channel

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

# Esercitazione: unisciti a una rete
{: #ibp-console-join-network}

{{site.data.keyword.blockchainfull}} Platform è un'offerta BaaS (blockchain-as-a-service) che ti consente di sviluppare, distribuire e gestire le applicazioni e le reti blockchain. Puoi scoprire di più sui componenti blockchain e su come lavorano insieme visitando la [Panoramica dei componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Questa esercitazione è la seconda parte della [serie di esercitazioni sulla rete di esempio](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) e descrive come creare nodi nella console {{site.data.keyword.blockchainfull_notm}} Platform e connetterli a un consorzio blockchain ospitato in un altro cluster.
{:shortdesc}

Se stai utilizzando la versione di prova beta di {{site.data.keyword.blockchainfull_notm}} Platform, è probabile che alcuni pannelli nella tua console non corrisponderanno alla documentazione corrente, che viene mantenuta aggiornata con l'istanza del servizio generalmente disponibile (GA). Per avvalerti dei vantaggi di tutte le ultime funzionalità, ti incoraggiamo in questo momento ad eseguire il provisioning a una nuova istanza del servizio GA seguendo le istruzioni in [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain.  

Se non hai già distribuito {{site.data.keyword.blockchainfull_notm}} Platform, vedi [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Puoi creare un nuovo cluster Kubernetes per i componenti blockchain o utilizzarne uno esistente nel tuo account {{site.data.keyword.cloud_notm}}. Dopo aver distribuito {{site.data.keyword.blockchainfull_notm}} Platform, puoi avviare la console per creare e gestire i tuoi componenti blockchain.

Sia che tu esegua la distribuzione su un cluster Kubernetes a pagamento o gratuito, utilizza il dashboard Kubernetes per prestare particolare attenzione alle risorse a tua disposizione quando scegli di distribuire nodi e creare canali. È tua responsabilità gestire il tuo cluster Kubernetes e distribuire risorse aggiuntive, laddove necessario. Sebbene i componenti verranno distribuiti correttamente su un cluster gratuito, più componenti aggiungi, più lenta sarà la loro esecuzione. Per ulteriori informazioni sul dimensionamento dei componenti e su come la console interagisce con il tuo cluster Kubernetes, vedi [Allocazione di risorse](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
{: note}

## Serie di esercitazioni sulla rete di esempio
{: #ibp-console-join-network-structure}

Questa serie di esercitazioni in tre parti ti guida attraverso il processo di creazione e interconnessione di una rete Hyperledger Fabric a più nodi relativamente semplice utilizzando la console {{site.data.keyword.blockchainfull_notm}} Platform 2.0 per distribuire una rete nel tuo cluster Kubernetes e installare e istanziare uno smart contract.

* L'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) ti guida attraverso il processo di hosting di una rete creando un servizio di ordinazione e un peer.
* **Esercitazione: unisciti a una rete** questa esercitazione ti guida attraverso il processo di unione a una rete esistente creando un peer e unendolo a un canale.
* [Distribuire uno smart contract sulla rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) fornisce informazioni su come scrivere uno smart contract e distribuirlo sulla tua rete.

Puoi utilizzare i passi descritti in queste esercitazioni per creare una rete con più organizzazioni in un cluster per scopi di sviluppo e di test. Utilizza l'esercitazione **Crea una rete** se vuoi formare un consorzio blockchain creando un servizio di ordinazione e aggiungendo delle organizzazioni. Utilizza l'esercitazione **Unisciti a una rete** per connettere un peer alla rete. Seguendo le esercitazioni con diversi membri del consorzio puoi creare una rete blockchain realmente **distribuita**.  


Questa esercitazione ha lo scopo di mostrare come unire un peer a una rete **esistente**. Presuppone che un servizio di ordinazione, che ospita tutti i canali nonché il consorzio, esista già nel tuo cluster o su un altro cluster di {{site.data.keyword.blockchainfull_notm}} Platform. Se non hai una rete esistente da unire, consulta l'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) per imparare a crearne una. L'esercitazione **Unisciti a una rete** ti guida attraverso i passi per creare i seguenti componenti blockchain di `Org2`, evidenziati nella casella blu:
![Unisciti alla struttura della rete](../images/ib2-join-network.png "Unisciti alla struttura della rete")  
*Figura 1. Unisciti alla struttura della rete*  
Esegui i passi dell'esercitazione **Unisciti a una rete** per creare i seguenti componenti e completare le seguenti azioni:

* **Una organizzazione peer** `Org2`  
  Crea la definizione MSP (Membership Services Provider) di Org2 che definisce l'organizzazione `Org2`.
* **Un peer** `Peer Org2`   
  Il libro mastro, `Ledger x` nell'illustrazione precedente, è gestito dai peer distribuiti. Il peer viene distribuito con [Couch DB ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) come database dello stato.
* **Una CA (Certificate Authority)** `Org2 CA`
  Una CA è il nodo che rilascia i certificati a tutti gli amministratori dell'organizzazione così come ai nodi associati a un'organizzazione. Creeremo una CA per l'organizzazione peer `Org2`.
* **Unione a un canale**
  L'esercitazione descrive come unirsi al canale creato dall'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

In tutta questa esercitazione forniamo **valori consigliati** per alcuni dei campi nella console. Ciò consente di riconoscere più facilmente i nomi e le identità nelle schede e negli elenchi a discesa. Questi valori non sono obbligatori, ma li troverai utili, specialmente perché dovrai ricordare alcuni valori come gli ID e i segreti degli utenti registrati che hai immesso in un passo precedente. Se dimentichi questi valori, dovrai creare degli ulteriori utenti registrati. Forniamo una tabella dei valori consigliati dopo ogni attività e ti consigliamo, nel caso non abbia utilizzato i valori di esempio, di registrare i tuoi valori man mano che procedi nell'esercitazione.
{:tip}

## Passo uno: crea un'organizzazione aggiuntiva e il tuo punto di ingresso alla blockchain
{: #ibp-console-join-network-create-ca-org2}

Per ogni organizzazione che vuoi creare con la console, devi distribuire almeno una CA. Una CA è il nodo che rilascia i certificati a tutti i partecipanti alla rete (peer, servizi di ordinazione, client, amministratori e così via). Questi certificati, che includono un certificato di firma e una chiave privata, consentono ai partecipanti alla rete di comunicare, autenticarsi e infine effettuare transazioni. Queste CA creeranno tutte le identità e i certificati che appartengono alla tua organizzazione, oltre a definire l'organizzazione stessa. Puoi quindi usare tali identità per distribuire nodi, creare identità amministratore e inviare transazioni. Per ulteriori informazioni sulla tua CA e sulle identità che dovrai creare, vedi [Gestione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

In questa esercitazione, creeremo solo un singolo ordinante. Pertanto, sarà necessario creare **una CA**.

### Creazione della CA della tua organizzazione peer
{: #ibp-console-join-network-create-CA-org2CA}

Come parte di questa esercitazione, la CA emette i certificati e le chiavi private per i tuoi utenti e nodi. Queste identità non sono gestite da {{site.data.keyword.IBM_notm}} e le chiavi non vengono memorizzate nella console. Vengono memorizzate solo nell'archiviazione locale del tuo browser. Pertanto, assicurati di esportare le identità e l'MSP della tua organizzazione. Se tenti di accedere alla console da una macchina diversa o da un browser diverso, dovrai importare tali identità e definizioni dell'organizzazione.
{:important}

Completa la seguente procedura dalla tua console:  

1. Vai alla scheda **Nodi** sulla sinistra e fai clic su **Aggiungi autorità di certificazione**. I pannelli laterali ti consentiranno di personalizzare la CA che vuoi creare e l'organizzazione per cui questa CA emetterà le chiavi.
2. Fai clic su **{{site.data.keyword.cloud_notm}}** in **Crea autorità di certificazione** e quindi su **Avanti**.
3. Utilizza il secondo pannello laterale per fornire alla tua CA un **nome di visualizzazione**. Il nostro valore consigliato per questa CA è `Org2 CA`.
4. Nel pannello successivo, fornisci le tue credenziali di amministratore CA specificando un **ID di iscrizione amministratore CA** di `admin` e un segreto di `adminpw`. Nuovamente, questi sono i **valori consigliati**.
5. Se stai utilizzando un cluster a pagamento, hai l'opportunità di configurare l'allocazione di risorse per il nodo. Ai fini di questa esercitazione, accetta tutti i valori predefiniti e fai clic su **Avanti**. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Se stai utilizzando un cluster gratuito, sarai portato alla pagina **Riepilogo**.
6. Esamina la pagina Riepilogo e fai quindi clic su **Aggiungi autorità di certificazione**.

**Attività: creazione della CA dell'organizzazione peer**

  | **Campo** | **Nome di visualizzazione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|
  | **Crea CA** | Org2 CA  | admin | adminpw |

*Figura 2. Creazione della CA dell'organizzazione peer*  

Dopo aver distribuito la CA, la utilizzerai per creare l'MSP dell'organizzazione, registrare gli utenti e creare il tuo punto di ingresso a una rete, ossia il **peer**.

Gli utenti avanzati possono già avere una loro CA e non vogliono creare una nuova CA nella console. Se la tua CA esistente può emettere certificati in formato `X.509`, puoi utilizzare la tua CA di terze parti invece di crearne una nuova qui. Per ulteriori informazioni, vedi questo argomento sull'[utilizzo di una CA di terze parti con il tuo peer o servizio di ordinazione](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-identities).

### Utilizzo della tua CA per registrare le identità
{: #ibp-console-join-network-use-CA-org2}

Ogni nodo o applicazione che vuoi creare necessita di certificati e chiavi private per partecipare alla rete blockchain. Devi inoltre creare delle identità di amministrazione per questi nodi e applicazioni in modo che tu possa gestirli dalla console. Ripeteremo questo processo due volte, una volta per ogni CA che creiamo. Inoltre, per ogni CA, creerai due identità:

* **Un amministratore dell'organizzazione**: questa identità ti consente di utilizzare i nodi servendoti della console della piattaforma.
* **Un'identità peer**: questa è l'identità del peer stesso. Ogni volta che un peer esegue un'azione (ad esempio, l'approvazione di una transazione) la firmerà utilizzando il proprio certificato.

A seconda del tuo tipo di cluster, la distribuzione della CA può richiedere fino a dieci minuti. Quando la CA viene distribuita la prima volta (oppure quando la CA non è disponibile), la casella nel tile per la CA sarà grigia. Quando la CA è stata distribuita correttamente ed è in esecuzione, questa casella sarà verde, indicando che è "In esecuzione" e può essere utilizzata per registrare le identità. Prima di procedere con i passi indicati di seguito per registrare le identità, devi attendere che lo stato della CA sia "Running".
{:important}

Dopo che la CA è in esecuzione, come indicato dalla casella verde nel tile, genera questi certificati completando la seguente procedura:

1. Fai clic su `Org2 CA` e assicurati che l'identità `admin` che hai creato per la CA sia visibile nella tabella. Successivamente, fai clic sul pulsante **Registra utente**.
2. Per prima cosa registreremo l'amministratore dell'organizzazione, cosa che possiamo fare fornendo un **ID di iscrizione** di `org2admin` e un **segreto** di `org2adminpw`. Quindi imposta il tipo (`Type`) per questa identità su `client` (le identità amministratore dovrebbero sempre essere registrate come `client`, mentre le identità del nodo utilizzando il tipo `peer`). Il campo di affiliazione è per utenti esperti e non fa parte dell'esercitazione, per cui fai clic sulla casella che indica **Utilizza affiliazione root**. Se vuoi saperne di più su come vengono utilizzate le affiliazioni dalla CA Fabric, consulta questo argomento su [Registering a new identity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Per ora, seleziona una qualsiasi affiliazione dall'elenco (ad esempio, `org1`). Inoltre, ignora il campo **Numero massimo di iscrizioni**. Se vuoi saperne di più sulle iscrizioni, vedi [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Fai clic su **Avanti**.
4. Ai fini di questa esercitazione, non è necessario utilizzare **Aggiungi attributo**. Se vuoi saperne di più sugli attributi, vedi [Registrazione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
5. Dopo che l'amministratore dell'organizzazione è stato registrato, ripeti lo stesso processo per l'identità del peer (utilizzando anche `Org2 CA`). Per l'identità del peer, fornisci un ID di iscrizione di `peer2` e un segreto di `peer2pw`. Questa è un'identità del nodo, quindi seleziona `peer` per il **Tipo**. Fai clic sulla casella che indica **Utilizza affiliazione root** e ignora **Numero massimo di iscrizioni**. Quindi, nel successivo pannello, non assegnare alcun **Attributo**, come hai fatto prima.

La registrazione di queste identità con la CA è solo il primo passo nella **creazione** di un'identità. Non potrai utilizzare queste identità finché non sono state **iscritte**. Per l'identità `org2admin`, ciò avverrà durante la creazione dell'MSP, che vedremo nel prossimo passo. Nel caso del peer, avviene durante la creazione del peer.
{:note}

**Attività: registra gli utenti**

  |  **Campo** | **Descrizione** | **ID di registrazione** | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registra utenti** |  Org2 admin | org2admin | org2adminpw |
  | | Identità peer |  peer2 | peer2pw |

*Figura 3. Utilizzo della tua CA per registrare gli utenti*  

### Creazione dell'MSP dell'organizzazione peer
{: #ibp-console-join-network-create-peers-org2}

Ora che abbiamo creato la CA del peer e l'abbiamo utilizzata per **registrare** le identità della nostra organizzazione, dobbiamo creare una definizione formale dell'organizzazione del peer, che è nota come definizione MSP (Membership Services Provider). Diversi peer possono appartenere a un'organizzazione. **Non hai bisogno di creare una nuova organizzazione ogni volta che crei un peer.** Poiché questa è la prima volta che eseguiamo l'esercitazione, creeremo l'ID MSP per questa organizzazione. Durante il processo di creazione dell'MSP, andremo a generare i certificati per l'identità `org2admin` e li aggiungeremo al nostro portafoglio.

1. Vai alla scheda **Organizzazioni** nel pannello di navigazione a sinistra e fai clic su **Crea definizione MSP**.
2. Fornisci all'MSP il nome di visualizzazione `Org2 MSP` e l'ID MSP `org2msp`. Se in questo campo vuoi specificare il tuo proprio ID MSP, assicurati di seguire le specifiche sulle limitazioni per questo nome indicate nel suggerimento.
3. In **Dettagli autorità di certificazione (CA) root**, specifica la CA che hai utilizzato per registrare le identità nel passo precedente. Se è la prima volta che esegui questa esercitazione, dovresti vederne solo una: `Org2 CA`.
4. I campi **ID di registrazione** e **Segreto di registrazione** qui sotto verranno compilati automaticamente con l'ID e il segreto di iscrizione per il primo utente che hai creato con la tua CA: `admin` e `adminpw`. Tuttavia, l'utilizzo di questa identità comporterà che la tua organizzazione avrà la stessa identità della CA, il che per motivi di sicurezza non è consigliato. Invece, seleziona l'ID di iscrizione che hai creato per il tuo amministratore dell'organizzazione dall'elenco a discesa, `org2admin` e immetti il relativo segreto associato, `org2adminpw`. Quindi, fornisci a questa identità un nome di visualizzazione, `Org2 Admin`.
5. Fai clic sul pulsante **Genera** per registrare questa identità come amministratore della tua organizzazione ed esportare l'identità nel portafoglio, dove verrà utilizzata durante la creazione del peer e dei canali.
6. Fai clic su **Esporta** per esportare i certificati dell'amministratore nel tuo file system. Come detto sopra, questa identità non viene memorizzata nel tuo cluster o gestita da {{site.data.keyword.IBM_notm}}. Viene memorizzata solo nell'archiviazione del browser locale. Se cambi browser, dovrai importare questa identità nel tuo portafoglio per poter gestire il peer.
7. Fai clic su **Crea definizione MSP**.

**Attività: crea la definizione MSP dell'organizzazione peer**

  |  | **Nome di visualizzazione** | **ID MSP** | **ID di registrazione**  | **Segreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crea organizzazione** | Org2 MSP | org2msp |||
  | **CA root** | Org2 CA ||||
  | **Certificato amministratore dell'organizzazione** | |  | org2admin | org2adminpw |
  | **Identità** | Org2 Admin |||||

  *Figura 4. Crea la definizione MSP dell'organizzazione peer*  

Dopo aver creato l'MSP, dovresti essere in grado di vedere l'amministratore dell'organizzazione peer nel tuo **portafoglio**, a cui è possibile accedere facendo clic su **Portafoglio** nel riquadro di navigazione di sinistra.

**Attività: controlla il tuo portafoglio**

  | **Campo** |  **Nome di visualizzazione** | **Descrizione** |
  | ------------------------- |-----------|----------|
  | **Identità** | Org2 Admin | Identità Org2 |

  *Figura 5. Controlla il tuo portafoglio*  

Per ulteriori informazioni sugli MSP, vedi [Gestione delle organizzazioni](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Esportare l'identità di amministratore dell'organizzazione è importante perché sei responsabile della gestione e della protezione di questi certificati. Se esporti il servizio di ordinazione e la definizione MSP del servizio di ordinazione, puoi importarli in un'altra console in cui un altro operatore può creare nuovi canali sul servizio di ordinazione o unire i peer al canale.
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
3. Fornisci al tuo peer un **Nome di visualizzazione** come `Peer Org1`.
4. Nella schermata successiva, seleziona `Org2 CA`, ovvero la CA che hai utilizzato per registrare l'identità del peer. Seleziona l'**ID di iscrizione** per l'identità del peer che hai creato per il tuo peer dall'elenco a discesa, `peer2` e immetti il relativo **segreto** associato, `peer2pw`. Seleziona quindi `Org2 MSP` dall'elenco a discesa e fai clic su **Avanti**.
5. Il pannello laterale successivo richiede informazioni sulla CA TLS. Quando hai creato la CA, è stato creato un TLSCA insieme ad essa. Questa CA viene utilizzata per creare i certificati per il livello di comunicazione sicura con i nodi. Pertanto, seleziona l'**ID di iscrizione** per l'identità del peer che hai creato per il tuo peer dall'elenco a discesa, `peer2` e immetti il **segreto** associato, `peer2pw`. L'opzione **Nome host CSR TLS** è un'opzione disponibile per gli utenti esperti che vogliono specificare un nome di dominio personalizzato che può essere utilizzato per indirizzare l'endpoint del peer. I nomi di dominio personalizzati non fanno parte di questa esercitazione, per cui lascia per ora vuoto il campo **Nome host CSR TLS**.
6. Il successivo pannello laterale ti chiede di **Associare un'identità** per renderla l'amministratore del tuo peer. Ai fini di questa esercitazione, fai in modo che il tuo amministratore dell'organizzazione, `Org2 Admin`, sia anche l'amministratore del tuo peer. È possibile registrare e iscrivere un'identità diversa con `Org2 CA` e rendere tale identità l'amministratore del tuo peer, ma questa esercitazione utilizza l'identità `Org2 Admin`.
7. Se stai utilizzando un cluster a pagamento, nel pannello successivo hai l'opportunità di configurare l'allocazione di risorse per il nodo. Ai fini di questa esercitazione, puoi accettare tutti i valori predefiniti e fare clic su **Avanti**. Se vuoi saperne di più su come allocare risorse al tuo nodo, vedi questo argomento sull'[allocazione delle risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Se stai utilizzando un cluster gratuito, vedi la pagina **Riepilogo**.
8. Riesamina il riepilogo e fai clic su **Aggiungi peer**.

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

In uno scenario di produzione, si consiglia di distribuire tre peer per ogni canale. Questo per consentire a un peer di essere inattivo (ad esempio, durante un ciclo di manutenzione) e mantenere ancora i peer altamente disponibili. Per distribuire più di un peer per un'organizzazione, utilizza la stessa CA che hai utilizzato per registrare la tua prima identità del peer. In questa esercitazione, è `Org2 CA`. Successivamente, registra una nuova identità del peer utilizzando un segreto e un ID di iscrizione distinti. Ad esempio, `org2secondpeer` e `org2secondpeerpw`. Successivamente, quando crei il peer, fornisci questi segreto e ID di iscrizione. Poiché il peer è ancora associato a Org2, scegli `Org2 CA`, `Org2 MSP` e `Org2 Admin` dagli elenchi a discesa. Puoi scegliere di fornire a questo nuovo peer un amministratore differente, che può essere registrato ed iscritto con `Org2 CA`, ma questa opzione è facoltativa. Queste serie di esercitazioni mostreranno soltanto il processo di creazione di un singolo peer per ogni organizzazione del peer.
{:tip}

## Passo due: aggiungi la tua organizzazione all'elenco di organizzazioni che possono effettuare transazioni
{: #ibp-console-join-network-add-org2}

Come notato in precedenza, un'organizzazione peer deve essere nota al servizio di ordinazione prima di poter creare o unirsi a un canale (processo noto anche come unione al "consorzio", l'elenco di organizzazioni note al servizio di ordinazione). Questo perché, a un livello tecnico, i canali sono dei **percorsi di messaggistica** tra i peer attraverso il servizio di ordinazione. Proprio come un peer può essere unito a più canali senza che le informazioni passino da un canale a un altro, così un servizio di ordinazione può disporre di più canali in esecuzione al suo interno senza esporre i dati alle organizzazioni su altri canali.

Poiché solo gli amministratori del servizio di ordinazione possono aggiungere organizzazioni peer al consorzio, dovrai: 

* **Essere** l'amministratore del servizio di ordinazione. Nel qual caso puoi aggiungere direttamente l'organizzazione peer che hai creato nel consorzio.
* **Inviare** le informazioni MSP all'amministratore del servizio di ordinazione, che aggiungerà la tua organizzazione al proprio consorzio.

Nel passo successivo, mostreremo come aggiungere un'organizzazione peer che hai creato a un consorzio che controlli. Per informazioni su come esportare la tua organizzazione in una console che ospita un servizio di ordinazione, vedi [Esporta le informazioni sulla tua organizzazione](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

### Aggiungi l'organizzazione del peer al servizio di ordinazione nella mia console
{: #ibp-console-join-network-add-org2-local}

**Segui questi passi solo se la tua console include già il servizio di ordinazione e il canale a cui vuoi unirti.** Altrimenti, vai a [Esporta le informazioni sulla tua organizzazione](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

Poiché solo gli amministratori del servizio di ordinazione possono aggiungere organizzazioni peer al consorzio, dovrai essere l'amministratore del servizio di ordinazione, il che significa che disponi dell'identità di amministratore dell'organizzazione del servizio di ordinazione nel tuo portafoglio.

1. Vai alla scheda **Nodi**.
2. Scorri verso il basso fino al servizio di ordinazione che vuoi utilizzare e fai clic su di esso per aprirlo.
3. In **Membri del consorzio**, fai clic su **Aggiungi organizzazione**.
4. Dall'elenco a discesa, seleziona `Org2 MSP`, in quanto questo è l'MSP che rappresenta `org2`.
5. Fai clic su **Aggiungi organizzazione**.

Adesso aggiungi l'organizzazione peer al canale.
1. Vai alla scheda **Canali** e fai clic su `channel1`.
2. Fai clic sull'icona **Impostazioni** per aggiornare il canale e aggiungere l'organizzazione peer al canale.
3. Nell'elenco a discesa **Scegli tra i servizi di ordinazione disponibili**, assicurati che sia selezionato `Ordering Service`.
4. Nell'elenco a discesa **ID MSP del programma di aggiornamento del canale**, assicurati che sia selezionato `org1MSP`.
5. Nell'elenco a discesa **Associa identità disponibile**, assicurati che sia selezionato `Org1Admin`.
6. Scorri fino alla sezione `Aggiungi organizzazioni al canale` e apri l'elenco a discesa `Seleziona un membro del canale` e seleziona l'MSP dell'organizzazione peer, `Org2 MSP`.
7. Fai clic su **Aggiungi** e quindi assegna le autorizzazioni per tale organizzazione. Ti consigliamo di renderla un `Operator` in modo che possa aggiornare il canale.
8. Fai clic su **Aggiorna canale**.

Una volta completato questo processo, `org2` può creare o unirsi a un canale ospitato sull'`Ordering Service`. Puoi procedere al [Passo tre: unisci il tuo peer al canale](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Esporta le informazioni sulla tua organizzazione
{: #ibp-console-join-network-add-org2-remote}

**Segui questi passi solo se il servizio di ordinazione e il canale a cui si unirà il tuo peer risiedono in un'altra istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform.** Altrimenti, puoi passare al [Passo tre: unisci il tuo peer al canale](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

Devi inviare la definizione MSP della tua organizzazione all'amministratore del servizio di ordinazione e aggiungerla al consorzio utilizzando la seguente procedura.

Quando esegui questi passi, devi essere l'amministratore dell'**organizzazione peer**, il che significa che disponi dell'identità di amministratore dell'organizzazione peer nel tuo portafoglio:

1. Vai alla scheda **Organizzazioni**. Puoi vedere che le organizzazioni disponibili per l'esportazione sono elencate in **Organizzazioni disponibili**. Fai clic sul pulsante di **download** all'interno del tile dell'organizzazione per scaricare il file di configurazione JSON che rappresenta l'MSP dell'organizzazione peer.
2. Invia questo file all'amministratore del servizio di ordinazione in un'operazione fuori banda.

### Importa la definizione dell'organizzazione
{: #ibp-console-join-network-import-remote-msp}

L'amministratore del servizio di ordinazione deve importare questo file JSON per aggiungere la tua organizzazione alla sua console:

1. Vai alla scheda **Organizzazioni**, fai clic sul pulsante **Importa definizione MSP** e seleziona il file JSON che rappresenta la definizione MSP dell'organizzazione peer.
2. Vai alla pagina **Nodi** e fai clic sul tuo nodo del servizio di ordinazione. Nel pannello del servizio di ordinazione, sotto **Membri del consorzio**, fai clic su **Aggiungi organizzazione**. Nel pannello laterale che si apre, seleziona `Org2 MSP` dall'elenco di definizioni MSP nella scheda **Organizzazioni**.
3. Vai alla scheda **Canali**, fai clic su `channel1` e quindi sull'icona **Impostazioni** per aggiornare il canale e aggiungere l'organizzazione peer al canale.
4. Nell'elenco a discesa **Scegli tra i servizi di ordinazione disponibili**, assicurati che sia selezionato `Ordering Service`.
5. Nell'elenco a discesa **ID MSP del programma di aggiornamento del canale**, assicurati che sia selezionato `org1MSP`.
6. Nell'elenco a discesa **Associa identità disponibile**, assicurati che sia selezionato `Org1Admin`.
7. Scorri fino alla sezione `Aggiungi organizzazioni al canale` e apri l'elenco a discesa `Seleziona un membro del canale` e seleziona l'MSP dell'organizzazione peer, `Org2 MSP` per l'esercitazione.
8. Fai clic su **Aggiungi** e quindi assegna le autorizzazioni per tale organizzazione. Ti consigliamo di renderla un `Operator` in modo che possa aggiornare il canale.
9. Fai clic su **Aggiorna canale**.

Dopo che l'amministratore del servizio di ordinazione ha unito la tua organizzazione peer al consorzio e al canale, devi importare il servizio di ordinazione nella tua console. Puoi quindi unirti ai canali ospitati dal servizio ordini. Completa la seguente procedura per **importare** il servizio di ordinazione:

Come amministratore dell'**organizzazione del servizio di ordinazione** devi prima completare questi passi:

1. Vai al servizio di ordinazione all'interno della scheda **Nodi** e fai clic sull'icona **Impostazioni** a destra del nome del servizio di ordinazione per aprire il pannello laterale. Fai clic sul pulsante **Esporta** sotto **Azioni** per scaricare un file di configurazione JSON.
2. Invia questo file all'organizzazione peer in un'operazione fuori banda. L'amministratore dell'organizzazione peer può quindi utilizzare il file di configurazione per aggiungere il servizio di ordinazione alla console.

### Importa il servizio di ordinazione da un altro cluster
{: #ibp-console-join-network-import-remote-orderer}

Segui questi passi se sei l'amministratore dell'**organizzazione peer**:

1. Vai alla pagina **Nodi** e fai clic su **Aggiungi servizio di ordinazione**.
2. Fai clic su {{site.data.keyword.cloud_notm}} in **Importa un servizio di ordinazione esistente**.
3. Quindi, fai clic sul pulsante **Carica JSON** e seleziona il JSON che rappresenta il nodo stesso.
4. Nel passo successivo, quando ti viene richiesto di associare un'identità, seleziona l'identità dell'organizzazione peer. In questa esercitazione, è `Org2 Admin`. Fai clic su **Importa servizio di ordinazione**.

## Passo tre: unisci il tuo peer al canale
{: #ibp-console-join-network-join-peer-org2}

Abbiamo quasi finito. Il tuo peer può ora essere unito a un canale esistente. Devi ottenere il `channel name`, fuori banda, da un'organizzazione che è un membro del canale. Nell'esercitazione **Crea una rete**, abbiamo creato un canale denominato `channel1`. Se non ci sei già, vai alla scheda **Canali** nel pannello di navigazione a sinistra.

Completa la seguente procedura dalla tua console:

1. Fai clic sul pulsante **Unisci a canale** per avviare i pannelli laterali.
2. Seleziona il tuo servizio di ordinazione denominato `Ordering Service` e fai clic su **Avanti**.
3. Immetti il nome del canale a cui vuoi unirti, `channel1` e fai clic su **Avanti**.
4. Seleziona i peer che vuoi unire al canale. Ai fini di questa esercitazione, fai clic su `Peer Org2`.
5. Fai clic su **Unisci a canale**.

Se intendi utilizzare le funzioni [Dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html "Private data") o [Rilevamento di servizi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service Discovery") di Hyperledger Fabric, devi configurare i peer di ancoraggio nella tua organizzazione dalla scheda **Canali**. Per ulteriori informazioni su come configurare i peer di ancoraggio per i dati privati utilizzando la scheda **Canali** nella console, vedi [Dai privati](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).

Se la tua organizzazione è un membro del consorzio, puoi anche creare un nuovo canale. Utilizza la procedura per [creare un canale](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) come descritto nell'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Passi successivi
{: #ibp-console-join-network-next-steps}

Dopo aver unito il tuo peer a un canale, utilizza le seguenti procedure per distribuire uno smart contract e iniziare a inviare transazioni alla blockchain:

- [Distribuisci uno smart contract sulla tua rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) utilizzando la console.
- Dopo aver installato e istanziato il tuo smart contract, puoi [inviare le transazioni utilizzando la tua applicazione client](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilizza [l'esempio di commercial paper](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) per distribuire uno smart contract di esempio e inviare le transazioni dal codice dell'applicazione di esempio.

---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Problemi noti
{: #known-issues}

Questa pagina illustra i problemi noti che puoi riscontrare durante l'utilizzo dei piani Starter o Enterprise.
{:shortdesc}

I seguenti problemi sono già stati segnalati:
- **La configurazione di una CA esterna non è ancora supportata**. In alternativa, puoi generare e caricare i certificati di amministrazione tramite il Monitoraggio della rete. Per ulteriori informazioni, vedi [Generazione dei certificati lato client](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel) e la descrizione nella [scheda "Certificati" della schermata "Membro"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) nel Monitoraggio della rete.
- Nel Monitoraggio della rete di una rete piano Starter, quando fai clic su **Visualizza log** nei nodi elencati nella schermata "Panoramica", viene aperta l'interfaccia Kibana di registrazione nei log {{site.data.keyword.cloud}}. **Per impostazione predefinita, Kibana è preconfigurato per mostrare i log degli ultimi 30 giorni di attività**. Se non c'è stata attività negli ultimi 30 giorni, vedrai un messaggio che indica *Nessun risultato trovato*. Per visualizzare altri log, puoi fare clic sull'icona del timer nell'angolo superiore destro sotto il tuo nome utente e impostare un intervallo di tempo più ampio, ad esempio *Da inizio anno*
- I log della tua rete piano Starter vengono raccolti dal servizio Log Analysis di[{{site.data.keyword.cloud_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/log-analysis). Per impostazione predefinita, i tuoi log sono raccolti dal piano Lite del servizio Log Analysis. Questo piano è gratuito e **consente solo di cercare nei primi 500 MB dei tuoi log al giorno**. Se i log della tua rete superano i 500 MB, non puoi visualizzare i nuovi log in Kibana. Se la tua rete genera più di 500 MB di log, puoi eseguire l'upgrade a una versione a pagamento del servizio Log Analysis.
- Poiché il piano Starter non è un ambiente di produzione, **le applicazioni potrebbero non essere in grado di raggiungere immediatamente una risorsa di rete**.
  - Se ciò si verifica, si consiglia come primo passo di aumentare i valori di timeout predefiniti nell'SDK Fabric. Per ulteriori informazioni sull'impostazione dei valori di timeout, vedi [Impostazione dei valori di timeout negli SDK Fabric](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk).
  - Puoi anche riprovare la tua richiesta a livello delle applicazioni.
- **I contenitori chaincode possono a volte essere arrestati** da un problema di rete di background e potrebbe essere necessario crearli nuovamente e riavviarli dopo essere stati richiamati da un utente. Se ciò si verifica, potrebbe volerci qualche minuto perché il tuo chaincode risponda.
- A causa della limitazione delle risorse nella rete piano Starter, ossia 1 CPU e 4 Gi di RAM per ogni peer, **potresti riscontrare un errore `REQUEST_TIMEOUT` durante l'istanziazione del chaincode**. Se ciò si verifica, riprova il passo di istanziazione. Se l'errore persiste, puoi aumentare il timeout di istanziazione del chaincode. Nel profilo di connessione, il timeout di istanziazione del chaincode è impostato su 300 secondi.
  - Se utilizzi il valore di timeout predefinito in SDK, copia la sezione **client** nel profilo di connessione come mostrato di seguito, che imposta il timeout su 300 secondi, e assicurati che il tuo SDK la legga. Nota che, per l'SDK Node, questa impostazione di timeout nel profilo di connessione influenza tutte le chiamate, come `invoke`, `queries` e così via.
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - Se sovrascrivi l'impostazione di timeout dei comandi di istanziazione del chaincode nei tuoi SDK, impostala nuovamente sul valore predefinito oppure modificala in modo che sia pari a 300 secondi o più.
    - Se usi l'SDK Node, puoi modificare l'impostazione di timeout del metodo `sendInstantiateProposal(request, timeout)`. Per ulteriori informazioni, consulta [sendInstantiateProposal(request, timeout) ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal).
    - Se utilizzi SDK Java, puoi modificare l'impostazione di timeout del comando `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)`, che si trova nel file `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java`.

Per supporto e assistenza con la tua rete {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}}, consulta [Richiesta di assistenza tecnica](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

## Aggiornamento del chaincode per l'upgrade della rete piano Enterprise
{: #known-issues-ibp-about-chaincode-migration}

Se la tua rete piano Enterprise è al livello Hyperledger Fabric V1.0, {{site.data.keyword.blockchainfull_notm}} Platform pianificherà un upgrade per la tua rete per eseguire la migrazione a Hyperledger Fabric V1.1. Con l'upgrade di rete, se utilizzi il chaincode complesso con delle dipendenze, devi aggiornare le dipendenze nel tuo chaincode. In caso contrario, potresti riscontrare un interruzione del servizio.

**Note**:
- Se utilizzi il chaincode semplice con un singolo file `.go` o `.js`, non hai bisogno di aggiornare il tuo chaincode.
- Il chaincode aggiornato potrebbe non funzionare sulla tua vecchia rete; devi pertanto aggiornare il tuo chaincode dopo l'upgrade della rete pianificato.
- Puoi testare il tuo chaincode installandolo e istanziandolo in una rete piano Starter. Tutti i chaincode che funzionano sul piano Starter funzionano anche sul piano Enterprise dopo l'upgrade delle rete.

Attieniti alla seguente procedura per aggiornare il tuo chaincode:
1. Utilizza qualsiasi strumento Golang vendoring per aggiornare il tuo chaincode. È più facile utilizzare lo stesso strumento che era stato utilizzato per includere le dipendenze nel file originale. Ad esempio, se il tuo chaincode utilizza lo strumento **govendor** che è utilizzato da molti esempi Fabric, puoi eseguire il seguente comando nella directory che contiene la cartella vendor per aggiornare le dipendenze del tuo chaincode:
    ```
    govendor update all +v
    ```
    {:codeblock}

    Puoi quindi utilizzare `go build` per controllare se il nuovo codice è conforme e se l'aggiornamento del chaincode funziona.

2. Dopo il tuo upgrade della rete, puoi [aggiornare il tuo chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) nel monitoraggio della rete.

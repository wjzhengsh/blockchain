---

copyright:
  years: 2018,2019
lastupdated: "2019-04-17"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Migrazione dal piano Starter al piano Enterprise
{: #migrate_starter_to_enterprise}

Il [piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about) di {{site.data.keyword.blockchainfull}} Platform ti offre un ambiente di test e sviluppo per eseguire PoC, demo e provare il tuo chaincode e le tue applicazioni. Quando sei pronto a passare a un ambiente di produzione, puoi migrare da una rete piano Starter a una rete [piano Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
{:shortdesc}

Le reti piano Enterprise offrono le seguenti funzioni pronte per la produzione a supporto del tuo carico di lavoro di produzione:

- Reti blockchain disponibili in ubicazioni globali aggiuntive rispetto alle reti piano Starter.
- Nessun limite di archiviazione, che è di 20 GB per il piano Starter.
- Gestione avanzata della CPU e della RAM per garantire che tutte le reti funzionino senza problemi.
- Sicurezza avanzata con le seguenti funzioni:
  - SSC (Secure Service Container) garantisce che l'immagine blockchain non possa essere manomessa e caricata in un dato momento e che il codice e i dati dei dispositivi siano protetti in modo confidenziale sia in fase di elaborazione che di inattività.
  - [Hardware secure module (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) per la gestione e l'archiviazione delle chiavi.
  - Crittografia pervasiva del disco.
- Alta disponibilità, ripristino di emergenza, tolleranza di errori anomali e aggiornamenti progressivi.
- Supporto avanzato facoltativo.

## Considerazioni
{: #migrate_starter_to_enterprise_considerations}

Prima di migrare dalla tua rete piano Starter a una rete piano Enterprise, esamina le seguenti considerazioni.

- **Prezzi:** per la tua organizzazione, la tariffa mensile per l'utilizzo di una rete piano Enterprise include un costo di adesione di $1000 in base alle singole istanze e un costo peer di $1000 in base ai singoli peer. Per ulteriori informazioni, vedi
[Prezzi del piano Enterprise](/docs/services/blockchain/howto/pricing.html#ibp-pricing-enterprise-plan).
- **Versione di Hyperledger Fabric:** le reti piano Enterprise vengono eseguite su Hyperledger Fabric v1.1. Le reti piano Starter sono eseguite su Fabric v1.2.
- **Risorse interessate:** chaincode (smart contract), applicazioni client. Ancora una volta, fai attenzione se il tuo chaincode sta utilizzando un componente o una funzionalità Fabric v1.2 che non è compatibile con le reti v1.1.
- **Tempo richiesto:** la migrazione di una rete di base dal piano Starter al piano Enterprise richiederà almeno mezza giornata.
- I **Dati del libro mastro esistenti** non possono essere spostati dalle reti piano Starter alle reti piano Enterprise perché non è appropriato che i dati di test esistano in un ambiente di produzione.
- **Hyperledger Composer** IBM non fornisce supporto per le reti che utilizzano Hyperledger Composer in produzione, compresi la CLI Composer, le API JavaScript, il server REST e Web Playground.

**Nota:** una rete *di base* include due organizzazioni con due peer, un singolo canale e un singolo file chaincode. Il tempo effettivo di migrazione può variare a seconda delle dimensioni e della complessità dei componenti di rete che richiedi nella rete piano Enterprise.

## Elenco di controllo della migrazione
{: #migrate_starter_to_enterprise_checklist}

Ci sono una serie di attività necessarie per prepararsi a passare a una rete piano Enterprise da una rete Starter. Puoi trovare istruzioni dettagliate nelle sezioni successive, ma ecco un riepilogo:

- Crea una rete piano Enterprise.
- Ricrea la configurazione desiderata di organizzazioni e peer dalla tua rete piano Starter di test.
- Identifica quale chaincode, scritto in Go o Node, verrà eseguito nella rete piano Enterprise.
- Aggiorna le applicazioni client con le nuove informazioni sull'endpoint API per la rete piano Enterprise.

### Crea una rete piano Enterprise
{: #migrate_starter_to_enterprise_create_network}

Prima di eseguire la migrazione, devi creare una rete piano Enterprise. Per ulteriori informazioni, vedi [Creazione di una rete piano Enterprise](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network).

### Ricrea la configurazione di rete
{: #migrate_starter_to_enterprise_config_network}

Puoi ricreare la configurazione di organizzazioni (membri), canali e peer della tua rete piano Starter nella tua rete piano Enterprise. Puoi utilizzare l'interfaccia utente di Monitoraggio della rete per ricreare queste risorse di rete invitando le organizzazioni appropriate (nota che non potrai **passare** tra le organizzazioni come in Starter), creare i canali e creare i peer (di nuovo, le organizzazioni invitate dovranno creare i propri peer).

1. Accedi alla tua rete piano Enterprise su {{site.data.keyword.cloud_notm}} e accedi al Monitoraggio della rete.
2. Ricrea le organizzazioni (membri) sulla schermata "Membri", ricrea i canali sulla schermata "Canali" e ricrea i peer sulla schermata "Panoramica". Per ulteriori informazioni sulla creazione delle risorse di rete, vedi [Utilizzo del Monitoraggio della rete](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-overview).
3. Configura i canali aggiungendo membri e impostando le politiche di canale allo stesso modo della tua rete piano Starter.

**Nota:** per ottenere l'alta disponibilità, devi creare almeno due peer per la tua organizzazione, unirli allo stesso canale e codificare correttamente le applicazioni client per passare da un peer all'altro laddove necessario.

### Migra il chaincode
{: #migrate_starter_to_enterprise_cc}

Il chaincode viene sviluppato esternamente nel tuo ambiente locale e viene richiamato dalle tue applicazioni client. Per installare e istanziare il chaincode, che è stato testato nella tua rete piano Starter, su peer selezionati nella tua rete piano Enterprise, attieniti alle istruzioni in [Installazione, istanziazione e aggiornamento di un chaincode](/docs/services/blockchain
/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc).

### Aggiorna le applicazioni client
{: #migrate_starter_to_enterprise_app}

Devi aggiornare le applicazioni client esistenti, testate nella rete piano Starter, con le nuove informazioni sull'endpoint API per la rete piano Enterprise. Per ulteriori informazioni, vedi [Richiamo delle credenziali di rete e profilo di connessione](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-retrieve-credentials).

Per quanto riguarda l'alta disponibilità, è responsabilità delle applicazioni client rilevare quando un peer non risponde e instradare le transazioni a un altro peer.

## Cosa fare con le reti piano Starter esistenti
{: #migrate_starter_to_enterprise_existing_network}

Potresti continuare a utilizzare la tua rete piano Starter esistente come ambiente sandbox per incubare nuovi progetti e testare le modifiche al chaincode esistente. Inoltre, puoi mantenere i dati del libro mastro di test nella tua rete piano Starter, che non vengono migrati alla rete piano Enterprise.

Non eliminare la tua rete Starter prima di aver completato tutti i test e verificato che tutto funzioni correttamente. Tuttavia, una volta che non hai più bisogno della tua rete Starter e dei dati del libro mastro al suo interno, puoi tranquillamente eliminare la rete. Per ulteriori informazioni, vedi [Eliminazione, o uscita da, una rete](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-delete-network).

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Migrazione dal piano Starter al piano Enterprise
{: #migrate_starter_to_enterprise}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Il [piano Starter](../starter_plan.html) di {{site.data.keyword.blockchainfull}} Platform ti offre un ambiente di test e sviluppo per eseguire PoC, demo e provare il tuo chaincode e le tue applicazioni. Quando sei pronto a passare a un ambiente di produzione, puoi migrare da una rete piano Starter a una rete [piano Enterprise](../enterprise_plan.html).
{:shortdesc}

Le reti piano Enterprise offrono le seguenti funzioni pronte per la produzione a supporto del tuo carico di lavoro di produzione:

- Reti blockchain disponibili in ubicazioni globali aggiuntive rispetto alle reti piano Starter.
- Nessun limite di archiviazione, che è di 20 GB per il piano Starter.
- Gestione avanzata della CPU e della RAM per garantire che tutte le reti funzionino senza problemi.
- Sicurezza avanzata con le seguenti funzioni:
  - SSC (Secure Service Container) garantisce che l'immagine blockchain non possa essere manomessa e caricata in un dato momento e che il codice e i dati dei dispositivi siano protetti in modo confidenziale sia in fase di elaborazione che di inattività.
  - [Hardware secure module (HSM)](../glossary.html#hsm) per la gestione e l'archiviazione delle chiavi.
  - Crittografia pervasiva del disco.
- Alta disponibilità, ripristino di emergenza, tolleranza di errori anomali e aggiornamenti progressivi.
- Supporto avanzato facoltativo.

## Considerazioni
{: #migrate_starter_to_enterprise_considerations}

Prima di migrare dalla tua rete piano Starter a una rete piano Enterprise, esamina le seguenti considerazioni.

- **Prezzi:** per la tua organizzazione, la tariffa mensile per l'utilizzo di una rete piano Enterprise include un costo di adesione di $1000 in base alle singole istanze e un costo peer di $1000 in base ai singoli peer. Per ulteriori informazioni, vedi
[Prezzi del piano Enterprise](pricing.html#enterprise-plan-pricing).
- **Versione di Hyperledger Fabric:** le reti piano Enterprise vengono eseguite su Hyperledger Fabric v1.1. Le reti piano Starter sono eseguite su Fabric v1.2. Questo ad esempio significa che un componente come [Private Data](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html), --- un chaincode sviluppato per utilizzare Private Data --- che funziona con una rete del piano Starter, non funzionerà nel piano Enterprise.
- **Risorse interessate:** chaincode (smart contract), definizioni di rete di business, applicazioni client. Ancora una volta, fai attenzione se il tuo chaincode sta utilizzando un componente o una funzionalità Fabric v1.2 che non è compatibile con le reti v1.1.
- **Tempo richiesto:** la migrazione di una rete di base dal piano Starter al piano Enterprise richiederà almeno mezza giornata.
- I **Dati del libro mastro esistenti** non possono essere spostati dalle reti piano Starter alle reti piano Enterprise perché non è appropriato che i dati di test esistano in un ambiente di produzione.

**Nota:** una rete *di base* include due organizzazioni con due peer, un singolo canale e un singolo chaincode o file di archivio di rete di business (`.bna`). Il tempo effettivo di migrazione può variare a seconda delle dimensioni e della complessità dei componenti di rete che richiedi nella rete piano Enterprise.

## Elenco di controllo della migrazione
{: #migrate_starter_to_enterprise_checklist}

Ci sono una serie di attività necessarie per prepararsi a passare a una rete piano Enterprise da una rete Starter. Puoi trovare istruzioni dettagliate nelle sezioni successive, ma ecco un riepilogo:

- Crea una rete piano Enterprise.
- Se hai sviluppato un `.bna` utilizzando Hyperledger Composer, individua e migra il file `.bna`.
- Ricrea la configurazione desiderata di organizzazioni e peer dalla tua rete piano Starter di test.
- Identifica quale chaincode, scritto in Go o Node, verrà eseguito nella rete piano Enterprise.
- Aggiorna le applicazioni client con le nuove informazioni sull'endpoint API per la rete piano Enterprise.

### Crea una rete piano Enterprise
{: #migrate_starter_to_enterprise_create_network}

Prima di eseguire la migrazione, devi creare una rete piano Enterprise. Per ulteriori informazioni, vedi [Creazione di una rete piano Enterprise](../get_start.html#creating-a-network).

### Migra un file `.bna`
{: #migrate_starter_to_enterprise_bna}

**Nota:** puoi ignorare questo passo se non utilizzi un file `.bna` nella tua rete piano Starter.

Se hai utilizzato Hyperleger Composer per definire una rete di business e hai distribuito un file `.bna` nella tua rete piano Starter, puoi distribuire lo stesso file `.bna` nella tua rete piano Enterprise.

- Se hai un tuo file di archivio di rete di business (`.bna`), segui le istruzioni in [Distribuzione di una rete di business su piano Enterprise](../develop_enterprise.html).
- Se non hai un tuo file `.bna`, usa il comando `composer network download` per richiamarlo dall'istanza del piano Starter. Per ulteriori informazioni sul comando `composer network download`, vedi la [documentazione della riga di comando Hyperledger Composer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}. Quindi, puoi eseguire le istruzioni riportate in [Distribuzione di una rete di business su piano Enterprise](../develop_enterprise.html).

### Ricrea la configurazione di rete
{: #migrate_starter_to_enterprise_config_network}

Puoi ricreare la configurazione di organizzazioni (membri), canali e peer della tua rete piano Starter nella tua rete piano Enterprise. Puoi utilizzare l'interfaccia utente di Monitoraggio della rete per ricreare queste risorse di rete invitando le organizzazioni appropriate (nota che non potrai **passare** tra le organizzazioni come in Starter), creare i canali e creare i peer (di nuovo, le organizzazioni invitate dovranno creare i propri peer).

1. Accedi alla tua rete piano Enterprise su {{site.data.keyword.cloud_notm}} e accedi al Monitoraggio della rete.
2. Ricrea le organizzazioni (membri) sulla schermata "Membri", ricrea i canali sulla schermata "Canali" e ricrea i peer sulla schermata "Panoramica". Per ulteriori informazioni sulla creazione delle risorse di rete, vedi [Utilizzo del Monitoraggio della rete](../v10_dashboard.html#overview).
3. Configura i canali aggiungendo membri e impostando le politiche di canale allo stesso modo della tua rete piano Starter.

**Nota:** per ottenere l'alta disponibilità, devi creare almeno due peer per la tua organizzazione, unirli allo stesso canale e codificare correttamente le applicazioni client per passare da un peer all'altro laddove necessario.

### Migra il chaincode
{: #migrate_starter_to_enterprise_cc}

Il chaincode viene sviluppato esternamente nel tuo ambiente locale e viene richiamato dalle tue applicazioni client. Per installare e istanziare il chaincode, che è stato testato nella tua rete piano Starter, sui peer selezionati nella tua rete piano Enterprise, segui le istruzioni riportate in [Installazione, istanziazione e aggiornamento di un chaincode](./install_instantiate_chaincode.html#installchaincode).

### Aggiorna le applicazioni client
{: #migrate_starter_to_enterprise_app}

Devi aggiornare le applicazioni client esistenti, testate nella rete piano Starter, con le nuove informazioni sull'endpoint API per la rete piano Enterprise. Per ulteriori informazioni, vedi [Richiamo delle credenziali di rete e profilo di connessione](../get_start.html#retrieving-network-credentials-and-connection-profile).

Per quanto riguarda l'alta disponibilità, è responsabilità delle applicazioni client rilevare quando un peer non risponde e instradare le transazioni a un altro peer.

## Cosa fare con le reti piano Starter esistenti
{: #migrate_starter_to_enterprise_existing_network}

Potresti continuare a utilizzare la tua rete piano Starter esistente come ambiente sandbox per incubare nuovi progetti e testare le modifiche al chaincode esistente. Inoltre, puoi mantenere i dati del libro mastro di test nella tua rete piano Starter, che non vengono migrati alla rete piano Enterprise.

Non eliminare la tua rete Starter prima di aver completato tutti i test e verificato che tutto funzioni correttamente. Tuttavia, una volta che non hai più bisogno della tua rete Starter e dei dati del libro mastro al suo interno, puoi tranquillamente eliminare la rete. Per ulteriori informazioni, vedi [Eliminazione, o uscita da, una rete](../get_start_starter_plan.html#deleting-or-leaving-a-network).

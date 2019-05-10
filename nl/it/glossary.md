---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-17"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Glossario
{: #glossary}

Questo argomento definisce i termini specifici di {{site.data.keyword.blockchainfull}} Platform che compaiono in questa documentazione. Per una comprensione più approfondita dei termini e per un glossario dei termini correlati ai concetti di Hyperledger Fabric, fai riferimento a [Hyperledger Fabric glossary ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/glossary.html).
{:shortdesc}

## Asset
{: #glossary-asset}
Beni, servizi o proprietà tangibili o intangibili rappresentati come un elemento che viene negoziato sulla rete blockchain.

## Blocco
{: #glossary-block}
Un set ordinato di transazioni, che è crittograficamente collegato al precedente blocco su un canale.

## CA
{: #glossary-CA}
Un'abbreviazione di "Certificate Authority - Autorità di certificazione", si tratta del componente che emette i certificati a tutti i membri partecipanti. Questi certificati rappresentano l'identità di un membro. Tutte le entità nella rete (peer, ordinanti, client ecc.) devono avere un'identità per comunicare, autenticare e, alla fine, interagire con il libro mastro. Queste identità sono richieste per qualsiasi partecipazione diretta nella rete blockchain.

## Catena
{: #glossary-chain}
La catena del libro mastro è un log di transazioni strutturato come blocchi di transazioni collegati da hash. I peer ricevono i blocchi di transazioni dal servizio ordini, contrassegnano le transazioni del blocco come valide o non valide, in base alle politiche di approvazione e alle violazioni della concorrenza, e accodano il blocco alla catena hash sul file system del peer.

## Chaincode
{: #glossary-chaincode}

Noto anche come **smart contract**, il chaincode è un elemento software che contiene una serie di funzioni per eseguire query o aggiornamenti del libro mastro.

## Canale
{: #glossary-channel}
Consiste in un sottoinsieme di membri della rete che vogliono eseguire transazioni in privato. I canali forniscono l'isolamento e la confidenzialità dei dati consentendo ai membri di un canale di stabilire delle specifiche regole e un libro mastro separato a cui possono accedere solo i membri del canale. I peer, che sono i nodi che fungono da endpoint delle transazioni per le organizzazioni, vengono uniti ai canali.

## Client
{: #glossary-client}
Il client rappresenta l'entità che agisce per conto di un utente. Deve connettersi a un peer per comunicare con la blockchain. Il client può connettersi a qualsiasi peer di sua scelta. I clienti creano e quindi richiamano le transazioni. Il client inoltra un effettivo richiamo della transazione agli approvatori e trasmette le proposte di transazione al servizio ordini.

## Profilo di connessione
{: #glossary-connection-profile}
Il profilo di connessione è visibile nella schermata "Panoramica" del Monitoraggio della rete quando si fa clic sul pulsante **Profilo connessione**. Le informazioni sono disponibili in formato JSON e contengono le informazioni sugli endpoint API e gli ID registrazione(enrollID)/i segreti per le tue risorse di rete, ossia peer, ordinanti e CA. La tua applicazione interagisce con le risorse di rete tramite questi endpoint API.

## Consenso
{: #glossary-consensus}
Un processo collaborativo per mantenere le transazioni del libro mastro sincronizzate nella rete. Il consenso garantisce che i libri mastro vengano aggiornati solo quando gli appropriati partecipanti approvano le transazioni e che i libri mastro vengano aggiornati con le stesse transazioni nello stesso ordine. Ci sono molti modi algoritmici differenti per raggiungere il consenso.

## Consorzio
{: #glossary-consortium}
Il gruppo di organizzazioni non ordinanti elencate sul canale del sistema ordinante. Queste sono le sole organizzazioni che possono creare il canale. Nel momento della creazione del canale, tutte le organizzazioni aggiunte al canale devono far parte di un consorzio. Tuttavia, un'organizzazione che non viene definita in un consorzio, può essere aggiunta a un canale esistente. Sebbene una rete blockchain può avere più consorzi, la maggior parte delle reti blockchain hanno un solo consorzio.

## CouchDB
{: #glossary-couchdb}
Un archivio documenti utilizzato per il database dello stato nelle reti piano Starter. CouchDB è anche un'opzione per le reti piano Enterprise, insieme a LevelDB. CouchDB supporta l'utilizzo di indici e consente di emettere query avanzate sui dati sul tuo peer.

## Stato corrente
{: #glossary-current-state}

Lo stato corrente del libro mastro rappresenta gli ultimi valori di tutte le chiavi che sono mai state incluse nel suo log di transazione a catena. Poiché lo stato corrente rappresenta tutte le ultime chiavi conosciute del canale, gli viene a volte fatto riferimento come a stato globale. Il chaincode esegue le proposte di transazione nei dati dello stato corrente. Lo stato corrente viene modificato ogni volta che cambia il valore di una chiave o viene aggiunta una nuova chiave. Lo stato corrente è fondamentale per un flusso di transazione perché l'ultima coppia chiave-valore deve essere nota prima di poter essere modificata. I peer eseguono il commit degli ultimi valori allo stato corrente del libro mastro di ogni transazione valida in un blocco. Lo stato corrente viene archiviato in un database dello stato del peer.

## Adesione dinamica
{: #glossary-dynamic-memership}
Un membro può essere aggiunto dinamicamente alla rete da un utente con il privilegio di **registrar** (conservatore del registro). Ai membri vengono anche assegnati ruoli e attributi che controllano il loro accesso e la loro autorizzazione sulla rete. Né i ruoli né gli attributi possono essere però assegnati dinamicamente. Hyperledger Fabric supporta l'aggiunta o la rimozione di membri, peer e nodi di servizio ordini, senza compromettere il funzionamento della rete complessiva. L'adesione dinamica è critica quando le relazioni di business vengono regolate ed è necessario, per vari motivi, aggiungere o rimuovere entità.

## Approvazione
{: #glossary-endorsement}
Il processo con cui le transazioni chaincode sono convalidate. Le regole di approvazione vengono implementate specificando le politiche di approvazione.

## Politica di approvazione
{: #glossary-endorsement-policy}
Definisce i nodi peer su un canale che devono eseguire le transazioni che sono collegate a una specifica applicazione chaincode e la combinazione richiesta di risposte (approvazioni). Una politica potrebbe richiedere che una transazione sia approvata da un numero minimo di peer di approvazione, una percentuale minima di peer di approvazione o da tutti i peer di approvazione assegnati a una specifica applicazione chaincode. Le politiche possono essere curate in base all'applicazione e al livello desiderato di resilienza contro comportamenti non corretti, deliberati o meno, dei peer di approvazione. Una transazione inoltrata deve soddisfare la politica di approvazione prima di essere contrassegnata come valida dai peer di commit. È anche richiesta una politica di approvazione distinta per installare e istanziare le transazioni.

## Blocco di genesi
{: #glossary-genesis-block}
Il blocco di configurazione che inizializza un canale o una rete blockchain e che funge anche da primo blocco in una catena.

## Gossip
{: #glossary-gossip}
Hyperledger Fabric consente ai peer di raccogliere, reciprocamente, importanti informazioni sulla rete senza dover fare affidamento sul servizio ordini. Il [protocollo di diffusione dei dati gossip ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html) fornisce ai peer un modo sicuro, affidabile e scalabile per scambiare messaggi tra loro. Ad esempio, se i peer perdono alcuni blocchi a causa di ritardi, interruzioni di rete o altri motivi, possono sincronizzarsi con lo stato attuale del libro mastro utilizzando la messaggistica gossip per contattare altri peer in possesso di questi blocchi mancanti.

## HSM
{: #glossary-hsm}
Hardware Security Module. Fornisce gestione delle chiavi, archiviazione delle chiavi e crittografia on-demand come un servizio gestito. HSM è un dispositivo fisico che gestisce le attività a elevato utilizzo di risorse dell'elaborazione di crittografia e riduce la latenza per le applicazioni. Per ulteriori informazioni, vedi [Hardware Security Module ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/cloud/hardware-security-module)

## Hyperledger Fabric
{: #glossary-hyperledger-fabric}
[Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/) è un framework blockchain di business ospitato da Linux Foundation che funge da base per lo sviluppo di applicazioni o soluzioni blockchain con un'architettura modulare. I componenti di Hyperledger Fabric quali i servizi di consenso e adesione sono plug-and-play.

## Installare
{: #glossary-install}
Il processo di inserire un chaincode nel file system di un peer. Devi installare il chaincode su ogni peer che eseguirà questo chaincode.

## Istanziare
{: #glossary-instantiate}
Il processo di avviare e istanziare un contenitore chaincode su uno specifico canale. Dopo che il chaincode è stato installato sui peer e ogni peer ha aderito al canale, il chaincode deve essere istanziato sul canale. L'istanziazione esegue tutta l'inizializzazione necessario del chaincode, che include l'impostazione di coppie chiave-valore che formano lo stato globale iniziale di un chaincode. Dopo l'istanziazione i peer che hanno il chaincode installato possono accettare richiami del chaincode.

## Kafka
{: #glossary-kafka}
L'implementazione di un plugin di consenso per Hyperledger Fabric che dà come risultato un cluster di nodi del servizio ordini nella rete blockchain. Un'implementazione Kafka è concepita per una rete di produzione.

## Libro mastro
{: #glossary-ledger}
Formato da una vera e propria "catena di blocchi" che memorizzano i record di transazioni immutabili e in sequenza, e un database dello stato per conservare lo stato corrente. C'è un libro mastro per canale e gli aggiornamenti ad esso vengono gestiti dal processo di consenso in base alle politiche di uno specifico canale.

## LevelDB
{: #glossary-leveldb}
Un archivio chiave-valore che è un'opzione per il database dello stato per le reti piano Enterprise, insieme a CouchDB. LevelDB archivia lo stato corrente come coppie chiave-valore e non supporta l'uso di indici o query avanzate.

## Membro
{: #glossary-member}

Noti anche come "organizzazioni", i membri in una rete blockchain, analogamente ai membri di qualsiasi gruppo, formano la struttura della rete. Un membro può essere grande quanto una società multinazionale o piccolo come una singola persona. I membri sono registrati nella rete con un certificato che concede loro le autorizzazioni a utilizzare la rete come un fornitore di servizi (ad esempio l'emissione di certificati e la convalida/ordinazione di transazioni) oppure come un consumatore. Nel primo caso fornisce servizi blockchain di base che includono la convalida di transazioni, gli ordini di transazioni e i servizi di gestione dei certificati. I membri consumatore utilizzano la rete per richiamare le transazioni sul libro mastro distribuito. I membri possono avere più peer.

## MSP
{: #glossary-msp}
Membership Service Provider.  Una serie di meccanismi e protocolli crittografici per emettere e convalidare certificati e identità in tutta la rete blockchain. Le identità emesse nell'ambito di un fornitore di servizi di adesione possono essere valutate nell'ambito elle politiche di convalida delle regole del fornitore di servizi di adesione. L'MSP è installato su ciascun peer del canale per garantire che le richieste di transazione emesse al peer abbiano origine da un'identità utente autenticata e autorizzata.

## Rete
{: #glossary-network}
Un'istanza di un servizio {{site.data.keyword.blockchainfull_notm}} Platform su {{site.data.keyword.cloud_notm}}.

## Credenziali di rete
{: #glossary-network-credentials}
Visibile dalla schermata "API" del Monitoraggio della rete. Le credenziali includono la tua "chiave" (Nome utente) e il "segreto" (password) nell'IU Swagger. Devi utilizzare queste credenziali di rete per eseguire l'autenticazione prima di provare le API REST.

## Monitoraggio della rete
{: #glossary-network-monitor}
Il dashboard GUI fornito da {{site.data.keyword.blockchainfull_notm}} Platform per visualizzare e gestire la rete blockchain.

## Nodo
{: #glossary-node}
L'entità di comunicazione della blockchain. Ci sono tre tipi di nodi: CA, peer e ordinante.

## Ordinante
{: #glossary-orderer}
Il nodo che raccoglie le transazioni dai membri della rete, ordina le transazioni e le include in blocchi. Questi blocchi vengono poi distribuiti ai peer, che verificano quindi i blocchi e li aggiungono ai libri mastro su ogni canale. Gli ordinanti contengono il materiale di identità crittografica associato a ciascun membro e autenticano l'identità dei client e dei peer per l'accesso alla rete. La funzione generale fornita da un nodo ordinante o da una raccolta di nodi, è nota come **servizio ordini**.

## Organizzazione
{: #glossary-organization}
Vedi [Membro](/docs/services/blockchain/glossary.html#glossary-member).

## Partecipante
{: #glossary-participant}
Qualsiasi organizzazione, individuo, applicazione o dispositivo che interagisce con la rete blockchain. Nell'ambito del termine 'partecipante' rientrano due raggruppamenti distinti, ossia membri e utenti.

## Peer
{: #glossary-peer}
Una risorsa di rete blockchain che fornisce i servizi per eseguire e convalidare le transazioni e mantenere i libri mastro. Il peer esegue il chaincode ed è il detentore della cronologia delle transazioni e dello stato corrente degli asset sui canali della rete, ossia il libro mastro. Appartengono e sono gestiti dalle organizzazioni e vengono uniti ai canali.

## Credenziali del servizio
{: #glossary-service-credentials}
Le credenziali del servizio sono in formato JSON e contengono le informazioni sull'endpoint API e gli ID iscrizione(enrollID)/segreti per le tue risorse di rete, ossia le CA, gli ordinanti e i peer. La tua applicazione interagisce con le risorse di rete tramite questi endpoint API.

## SDK
{: #glossary-sdk}
Hyperledger Fabric supporta due SDK (Software Development Kit). Un SDK Node e un SDK Java.  L'SDK Node può essere installato tramite NPM e l'SDK Java tramite Maven.  Gli SDK hanno i propri repository git, ossia [SDK Node Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-sdk-node) e [SDK Java Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-sdk-java), con la documentazione per le API disponibili. Gli SDK Hyperledger Fabric Client abilitano l'interazione tra la tua applicazione client e la tua rete blockchain.

## SignCert
{: #glossary-sign-cert}
Il certificato che ogni entità, che sia un'organizzazione o un amministratore, allega alla propria proposta o alle risposte alla proposta. Questi signCert sono univoci per un'entità e vengono controllati dal servizio ordini per garantire che corrispondano al signCert sul file di tale entità.

## Smart contract
{: #glossary-smart-contracts}
Vedi [Chaincode](/docs/services/blockchain/glossary.html#glossary-chaincode).

## SOLO
{: #glossary-solo}
L'implementazione di un plugin di consenso per Hyperledger Fabric che dà come risultato un singolo nodo di servizio ordini nella rete blockchain. La rete piano Starter utilizza l'implementazione SOLO. Un'implementazione SOLO non è concepita per una rete di produzione. L'alternativa a SOLO è un cluster Kafka.

## Database dello stato
{: #glossary-state-database}
I dati dello stato corrente vengono archiviati in un database sui peer per letture e query efficienti dal chaincode. Le reti piano Starter utilizzano CouchDB come database dello stato. Le reti piano Enterprise possono utilizzare LevelDB o CouchDB.

## Transazione
{: #glossary-transaction}
Il meccanismo che i partecipanti nella rete blockchain utilizzano per interagire con gli asset. Una transazione crea un nuovo chaincode o richiama un'operazione in un chaincode esistente.

## Utente
{: #glossary-user}
Un utente è un partecipante in una rete blockchain che ha accesso indiretto al libro mastro tramite una relazione di attendibilità a un membro esistente.

## Stato globale
{: #glossary-world-state}
Consulta [Stato corrente](/docs/services/blockchain/glossary.html#glossary-current-state).

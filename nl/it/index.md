---

copyright:
  years: 2017
lastupdated: "2017-08-24"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

**ATTENZIONE:** prima di utilizzare un'offerta {{site.data.keyword.blockchainfull}}, leggi le informazioni tecniche e di supporto nella sezione [Dichiarazione di non responsabilità](needtoknow.html).   
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform è la sola piattaforma pronta per il business integrata che si occupa dell'intero ciclo di vita (**sviluppo**, **governance** e **funzionamento**) di una rete blockchain con più organizzazioni. È progettata per accelerare, mediante collaborazione in ciascuna fase, la creazione delle reti blockchain globali "sviluppate per il business" con le prestazioni e la sicurezza anche per i casi d'uso e i settori regolamentati più esigenti. Le offerte {{site.data.keyword.blockchainfull_notm}} vengono sviluppate sulla base di codice Hyperledger Fabric V1.0, che si avvale di un'architettura modulare per ottenere livelli di categoria enterprise di sicurezza, integrità dei dati, scalabilità e prestazioni per soddisfare le tue esigenze di business.  

Diamo un'occhiata alle attività e ai ruoli principali in una rete {{site.data.keyword.blockchainfull_notm}} e vediamo che posto occupano nel ciclo di vita di una rete.

**Nota**: la tabella di seguito non intende rappresentare una progressione lineare. Le attività quali lo sviluppo e la gestione di rete si verificheranno ripetutamente man mano che le applicazioni e le adesioni si evolvono.

|  Funzione     | Attività       | Ruoli  |
| ------------------------- |--------------------------|-----|
| [Sviluppo ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://developer.ibm.com/blockchain/sandbox/) | Sviluppo di applicazioni, sviluppo di chaincode | Sviluppatori di applicazioni |
| [Governance](get_start.html)| Invito di membri, generazione di credenziali, proposizione di modelli di governance, adesione di nuovi membri, diffusione di certificati e informazioni sugli endpoint | Operatori di rete, membri della rete |
| [Funzionamento](v10_dashboard.html)| Monitoraggio di stato e attività, gestione delle nuove distribuzioni, gestione dei membri (aggiunta/rimozione), gestione del ciclo di vita del chaincode, gestione dei canali, supporto | Operatori di rete, partecipanti alla rete |
  
Ora diamo un'occhiata da vicino a questi ruoli e attività...

## **Sviluppo** della rete
Esplora e accelera lo sviluppo di blockchain avvalendoti delle prassi ottimali affinate in più di 400 impegni con i client per:
* garantire uno stretto allineamento tra le attività di business e la tecnologia che riduce il tempo di sviluppo dell'applicazione blockchain (le applicazioni il cui sviluppo richiederebbe sei settimane possono essere create in meno di due giorni).
* Sviluppa rapidamente competenze di blockchain nella tua lista esistente di programmatori avvalendoti degli strumenti per sviluppatori {{site.data.keyword.blockchainfull_notm}}.
* Dai agli sviluppatori la flessibilità di apprendimento e sviluppo nel loro ambiente preferito con un set di strumenti aperto e moderno.  
  
In quanto proprietario del business, puoi sviluppare il tuo caso d'uso con l'aiuto dell'ampia scelta di esperti del settore e del blockchain di IBM, che si riuniscono nel {{site.data.keyword.blockchainfull_notm}} Garage per sfruttare la piena potenza di IBM Blockchain Platform. 

In quanto sviluppatore, puoi allineare rapidamente e facilmente i requisiti di business e accelerare lo sviluppo di applicazioni blockchain gratuitamente con un sandbox cloud e un'area di esercitazione interattiva che trasforma qualsiasi programmatore in uno sviluppatore blockchain. Questi strumenti sono progettati per trasformare le regole di business in codice nel tuo ambiente preferito:
* **Esplora online**  
  Avvaliti di [Hyperledger Composer ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/introduction/introduction.html), che è uno strumento di sviluppo open source per apprendere i concetti chiave del blockchain, creare definizioni di rete e avvalersi di modelli del settore riutilizzabili e librerie di smart contract. 
* **Installa in locale**  
  Avvaliti delle immagini certificate da IBM di Hyperledger Fabric e Composer, che è un framework open source per creare una rete di business da sviluppare e testare direttamente sul tuo laptop.
* **Collabora in un ambiente cloud**  
  Opzioni gratuite e a pagamento per sviluppare e condividere il tuo codice con altri.
  
Dopo che hai sviluppato la tua rete di business, puoi distribuirla a una rete dal vivo in esecuzione su IBM Blockchain Platform con questa [procedura ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://ibm-blockchain.github.io/platform-deployment/).

## **Governance** della rete
Sono disponibili due opzioni per creare l'ambiente di backend della tua rete. Innanzitutto, puoi utilizzare le immagini Hyperledger Docker pubblicate, che ti danno l'opzione di implementare la libreria Composer per sviluppare le tue applicazioni e interagire con la tua rete. In alternativa, puoi scrivere il chaincode in modo nativo e sviluppare il codice lato server per gestire le transazioni. L'esecuzione in locale è l'occasione perfetta per armeggiare con le configurazioni di rete, esplorare i potenziali casi d'uso e iniziare a mettere a punto delle installazioni di prova. Man mano che la tua installazione di prova inizia a prendere forma, potrai ampliare l'implementazione ospitando la tua rete nel cloud.

Con una distribuzione cloud, ti viene fornita una raccolta di procedure e script di semplice utilizzo per facilitare lo sviluppo di una rete Hyperledger Fabric in esecuzione su Kubernetes. Utilizza questa fase per esaminare il comportamento e la stabilità della tua installazione di prova in un ambiente host. Il modo migliore per vedere il servizio contenitore [{{site.data.keyword.blockchainfull_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://ibm-blockchain.github.io/) è come un meccanismo per l'esecuzione di test per la funzionalità e la resilienza della tua applicazione e come un precursore naturale al piano Enterprise.

Una volta che disponi di una rete, {{site.data.keyword.blockchainfull_notm}} è stato progettato per creare un'esperienza di gestione della rete che offre ai membri un certo controllo senza però nessun singolo membro che ha un controllo completo. {{site.data.keyword.blockchainfull_notm}} Platform offre il primo set di strumenti integrati per consentire ai team di implementare la gestione delle modifiche della rete mediante politiche personalizzabili. 

Il seguente elenco mostra le funzioni chiave di questo modello di governance:

* Strumenti di gestione democratica per consentire la gestione collettiva.
* Editor delle politiche per definire delle politiche flessibili e democratiche per la governance delle modifiche alla rete.
* Strumenti e politiche precostruiti abilitano un'accoglienza, una personalizzazione e un'attivazione più rapide.
* Uno strumento di flusso di lavoro con molteplici parti con notifiche integrate, un pannello di attività dei membri e una raccolta di firme sicure.

## **Operare** la rete
Distribuisci e opera reti decentralizzate con un servizio sicuro pronto per la produzione. Inizia in piccolo e ingrandisci la tua rete in modo elastico man mano che le adesioni e i volumi delle transazioni aumentano avvalendoti delle seguenti funzioni:

* Un ambiente ad altissima sicurezza con molte funzioni di sicurezza hardware, firmware e software.
* Un'architettura consolidata con scalabilità, resilienza e disponibilità.
* Ottimizzato per le prestazioni e in esecuzione sul calcolo Linux più veloce del mondo.
  
Operare la tua rete su {{site.data.keyword.blockchainfull_notm}} Platform include strumenti e funzionalità per semplificare le attività amministrative:

* Dashboard per il monitoraggio e la gestione delle risorse sulla rete. 
* Gestione del ciclo di vita per dei perfetti upgrade di tutto lo stack di codice.
* Supporto tecnico 24/7 integrato nel portale.
* Stack di sicurezza consolidato senza accesso privilegiato, resistenza a malware e manomissioni, crittografia al 100% e molte altre funzioni per le reti con dati sensibili nei settori regolamentati.

## Partecipazione a una rete blockchain 

Usiamo il termine **partecipante** come una più ampia classificazione per qualsiasi organizzazione, individuo, applicazione o dispositivo che interagisce con la rete blockchain.  Nell'ambito del termine "partecipante" rientrano due raggruppamenti distinti: **membri** e **utenti**.   
 
Detto semplicemente, un membro possiede un certificato digitale valido, che consente al membro di emettere e/o convalidare transazioni in una rete blockchain.  Un utente non ha un certificato ma può comunque interagire con la rete blockchain tramite uno dei membri della rete esistenti.   Puoi pensare al certificato di un membro come alla sua "tessera d'iscrizione" a una palestra, ad esempio.    Un utente può non avere tale tessera d'iscrizione ma può comunque entrare in palestra come "ospite" di un membro esistente.  Diamo un'occhiata più da vicino a questi ruoli.

### Membri 

{{site.data.keyword.blockchainfull_notm}} Platform è potenziato da Hyperledger Fabric, che è una tecnologia "blockchain con autorizzazioni".  Pertanto, tutti i membri sono registrati nella rete con un certificato che concede loro le autorizzazioni a utilizzare la rete come un **fornitore** (ossia emettere certificati, convalidare/ordinare transazioni) o **consumatore** di servizi (ossia emettere transazioni).   

- **Fornitori - crediamo nell'importanza dei membri** - una rete blockchain trae la sua forza dai suoi membri.   Per rendere operativa una rete blockchain, è necessario che ci sia un gruppo minimo di membri *che fornisce* i servizi blockchain fondamentali, inclusi i servizi di convalida delle transazioni, di esecuzione di ordini delle transazioni e di gestione dei certificati.  Eseguendo questi servizi, questi membri diventano i mantenitori dell'integrità del libro mastro condiviso all'epicentro della rete blockchain.  Quindi, ti chiederai, quanti membri ti servono per rendere operativa un blockchain? La risposta è: dipende dal requisito di affidabilità della rete.   Alcune reti tollerano un modello di attendibilità più centralizzato che richiede un minor numero di membri che fungono da fornitori.  Altre reti richiedono un insieme di membri più vario (ossia entità legalmente separate) e mantengono un modello di affidabilità più decentralizzato.  Un esempio di un modello di attendibilità più centralizzato è una rete di visibilità della catena di approvvigionamento i cui i membri fornitori sono un venditore al dettaglio globale, una società di spedizione globale e IBM.  In questo caso, questi tre membri fungono da "base di attendibilità" per la rete, fornendo i servizi fondamentali della rete blockchain.   Questi membri possono emettere certificati per importatori, esportatori, agenti doganali e venditori al dettaglio in modo che possano partecipare (emettere transazioni) nella rete. Questa rete può decentralizzare l'attendibilità abilitando più membri a partecipare alla fornitura dei servizi fondamentali, garantendo così che tutti i membri abbiano il controllo ma che nessun singolo membro abbia un controllo esclusivo.    Una tipica rete presenterà circa 10 membri che forniscono i servizi blockchain fondamentali.

- **Consumatori - l'attendibilità è stata stabilita, ora è possibile procedere ai richiami** – dopo che è stata stabilita una base di attendibilità, la rete è pronta a crescere.  È una cosa abbastanza comune che la maggior parte dei membri nella tua rete stia semplicemente utilizzando la rete per richiamare delle transazioni sul libro mastro distribuito.  Questi membri sono semplicemente dei *consumatori* e non stanno partecipando a fornire i servizi fondamentali alla rete.  Una tipica rete presenterà tra i 10 e i 100 membri con l'autorizzazione ad emettere le transazioni in una rete blockchain specificata.


#### Utenti tipo membro
 
È a volte utile pensare ai membri ricorrendo a un utente tipo (persona) che delinea il ruolo del membro nella rete di business.  Ne sono qui di seguito riportati alcuni che vediamo usare di frequente.
 
- **Iniziatore** – un membro che è selezionato da altri membri per eseguire il bootstrap della rete blockchain.    IBM Blockchain Platform richiede che un singolo membro acceda a IBM Blockchain Platform ed esegua le attività per iniziare la rete.  Includono la denominazione della rete, l'invito del gruppo iniziale di membri e l'impostazione della serie predefinita di politiche operative della rete.  Questo è un ruolo temporaneo.  Dopo che è stato eseguito il bootstrap della rete, l'iniziatore non conserva privilegi speciali e riprende semplicemente il ruolo di membro.  

- **Mantenitore** – un membro che sta eseguendo una o più peer e CA di rete. Questi membri mantengono l'integrità del libro mastro distribuito partecipando al processo di consenso, che è il modo in cui le transazioni sono convalidate su una rete blockchain.   Il mantenitore, mediante la proprietà di una CA, può anche emettere certificati ai partecipanti e concedere loro l'accesso alla rete. 
 
- **Operatore** – un membro che sta eseguendo dei servizi per conto di altri membri della rete, compresi il servizio ordini di transazioni, le autorità di certificazione (CA, Certificate Authority), i gateway di transazioni e altri servizi di rete fondamentali.  Per impostazione predefinita, IBM è l'operatore di rete delle reti distribuite sulla IBM Blockchain Platform.
 
- **Revisore** – un membro a cui sono state concesse le autorizzazioni dalla rete per eseguire funzioni di revisione della rete.  Degli esempi delle funzioni di revisione includono la fatturazione, l'analisi o il tracciamento della conformità.  Il ruolo di revisore di norma si traduce in una più ampia visione che il membro ha delle transazioni nel libro mastro e/o in una più ampia registrazione ai canali delle transazioni.

### Utenti

Mentre possono esserci centinaia di membri in una rete blockchain, gli utenti possono essere migliaia.   Un utente è un partecipante in una rete blockchain che ha accesso indiretto al libro mastro tramite una "relazione di attendibilità" a un membro esistente.  È ad esempio una cosa comune che alcune applicazioni mobili utilizzino un loro schema di autenticazione e autorizzazione degli utenti (OAuth, OpenID) e associno queste credenziali a uno o più membri dotati di credenziali in una rete blockchain.    Per eseguire questa funzione di associazione viene di norma creato un servizio gateway o proxy, associando così il mondo esterno a quello blockchain.

## Servizi di rete fondamentali 

Per rendere operativo un blockchain, i membri formano una base di attendibilità eseguendo uno o più servizi di rete fondamentali: 

- **Servizio ordini** – ordini e sincronizzazione di transazioni  
  Essenzialmente, il servizio ordini è la definizione della rete.  Contiene le informazioni sull'identità per ciascun membro, le informazioni sui canali e una serie di politiche che dettano quali sono i membri a cui è consentito eseguire delle specifiche attività (ad es. invitare altri membri, creare canali ecc). Ogni operazione di transazione e configurazione transiterà per il servizio ordini quindi si tratta di una parte di importanza essenziale nello schema globale delle cose.  Data l'importanza fondamentale del servizio ordini, è facile vedere le insidie che riserverebbe un'organizzazione autoritaria dove magari un solo membro controllasse tutto.  Per evitare ciò, il servizio ordini è gestito in comune dai membri della rete e le misure sulle implementazioni di governance vengono adottate in modo congiunto.  In altre parole, le decisioni sono prese collettivamente, NON unilateralmente. Tutti i membri hanno una quota nella rete e, per estensione, hanno un voto in qualsiasi operazione che configura e personalizza il posto da loro occupato nella rete.  Queste nozioni di "democrazia" e decisioni prese congiuntamente sono gli elementi fondamentali inerenti per una rete affidabile e centralizzata.  IBM funge da "operatore" del servizio ordini per qualsiasi rete distribuita su IBM Blockchain Platform.
 
- **Autorità di certificazione (CA, Certificate Authority)** – emette i certificati per i partecipanti  
  Per dirla semplicemente, la CA fornisce l'adesione.  Tutte le entità nella rete (peer, ordinatori, client ecc.) devono avere un'identità per comunicare, autenticare e, alla fine, interagire con il libro mastro. Queste "identità" esistono sotto forma di certificati x509 (ossia certificati di registrazione), che sono richiesti per qualsiasi partecipazione diretta nella rete blockchain.  Ci sono anche modi di partecipazione indiretta, ma ci arriveremo dopo.  Si può pensare alla CA come a un timbro che dà alle identità autenticazione e credibilità.  Ciascun membro possiede una sua CA, mediante la quale può firmare i certificati non solo per i componenti completamente di sua proprietà (peer) ma anche per applicazioni e client terzi.  Puoi paragonare la CA di un membro a una penna speciale o a un timbro notarile.  Un certificato firmato da questa CA è un prerequisito per l'accesso alla rete.
 
- **Peer** – convalida/approvazione di transazioni  
  Il peer esiste per eseguire due funzioni principali: eseguire/convalidare transazioni e mantenere i libri mastro.  Il peer esegue degli smart contract ed è il detentore della cronologia delle transazioni e dello stato corrente delle risorse sui canali della rete.  In fin dei conti, tutto si riduce ad accedere al peer (direttamente o indirettamente) ed eseguire letture e scritture sul libro mastro. Quando un membro fornisce a un utente finale l'accesso alla rete, sta in effetti fornendo l'accesso alla funzionalità del peer.

Quando un membro aderisce a una rete tramite {{site.data.keyword.blockchainfull_notm}} Platform, l'emissione standard è un peer e una CA.  Per una rete di produzione, i membri vorranno eseguire più istanze di questi servizi per garantire la disponibilità. Per impostazione predefinita, IBM esegue il servizio ordini per le reti create da {{site.data.keyword.blockchainfull_notm}} Platform.  

## {{site.data.keyword.blockchainfull_notm}} Platform

{{site.data.keyword.blockchainfull_notm}} Platform fornisce una rete blockchain altamente sicura e con autorizzazioni su cui i membri autenticati possono facilmente definire le risorse e creare soluzioni di business per la modifica e lo scambio di tali risorse.  Non è più un processo tedioso e complicato eseguire il bootstrap di una rete blockchain di livello produzione. Con {{site.data.keyword.blockchainfull_notm}} Platform puoi avvalerti di un framework di orchestrazione che ti consente di **organizzare il tuo consorzio in una rete blockchain attiva in tempo record**. Forniamo degli strumenti adatti per i consorzi progettati per consentire a più istituzioni di unirsi e di creare una rete amministrata democraticamente in modo semplice. Le attività di creazione, governance e gestione della rete diventano intuitive e trasparenti mediante un monitor dashboard integrato e i programmi di utilità forniti. Tralasciando l'altrimenti ingombrante processo di creazione di una rete e di implementazione della governance, i membri del consorzio possono invece **concentrarsi sulla distribuzione di smart contract e sul trasferimento di risorse e informazioni**.      
{:shortdesc}

L'**Elevata disponibilità** per i componenti integrali della rete (peer, servizio ordini, Autorità di certificazione, chaincode) elimina gli effetti paralizzanti che possono derivare dai singoli punti di errore. Un monitor dashboard integrato consente una facile gestione di questi componenti e fornisce un potente meccanismo per visualizzare risorse e contratti smart.

La **modularità** dell'architettura di Hyperledger Fabric V1.0 e la chiara separazione dei ruoli di rete fornisce un'infrastruttura che abilita la scalabilità e l'adattabilità a un'ampia gamma di casi d'uso. 

I controlli e bilanciamenti che si verificano durante tutto il ciclo di vita di una transazione garantiscono risultati uniformi e totalmente controllati; inoltre i libri mastro vengono mantenuti costantemente sincronizzati attraverso un'implementazione del noto protocollo gossip. L'identità e il controllo degli accessi vengono applicati facilmente attraverso le operazioni di **firma/verifica** che avvengono continuamente in tutta la rete.  

Vengono forniti degli **strumenti di governance** per consentire ai membri di amministrare e gestire le regole di business fondamentali per la propria rete. Ad esempio, potresti voler implementare una politica che definisca quanti membri di una rete debbano accordarsi per far aderire un nuovo membro. Oppure, è richiesta l'approvazione di ogni partecipante affinché una risorsa possa essere modificata. Le regole di governance sono fondamentali per qualsiasi tipo di rete di business e sono spesso estremamente elaborate. Gli strumenti di governance (ad esempio, gli editor delle politiche) semplificano notevolmente questo processo.

Il servizio viene eseguito in un ambiente **altamente sicuro e isolato** senza accesso esterno (incluso l'accesso root) ai componenti di rete. I dati vengono crittografati in fase di elaborazione e mentre sono inattivi e i moduli di supporto della sicurezza hardware consentono di proteggere le chiavi digitali nel rispetto delle norme del settore. La virtualizzazione hardware viene utilizzata per eseguire ciascun nodo in un ambiente isolato, proteggendo così gli altri nodi nella rete dai peer con un chaincode che ha potenzialmente un comportamento scorretto o malintenzionato. Hashing, operazioni di firma/verifica e comunicazioni da componente a componente sono accelerati grazie alle implementazioni di crittografia avanzate.

Prima di continuare, diamo un'occhiata a una semplice configurazione in {{site.data.keyword.blockchainfull_notm}} Platform.  La **Figura 1** mostra un esempio di rete blockchain distribuita composta da quattro membri (ognuno proprietario di due peer), un'Autorità di certificazione responsabile della distribuzione del materiale di identità crittografico e un servizio ordini che definisce le politiche e i partecipanti della rete. Il canale blu contiene tutti i e quattro i membri della rete, laddove il canale giallo è limitato a soli tre membri, le banche B, C e D. Vediamo anche che la banca A ha ricoperto il ruolo di iniziatore della rete e che la banca D esiste solo come un membro nel contesto del canale giallo. Infine, un utente finale o un'applicazione in possesso di un certificato x509 correttamente firmato può inviare chiamate ai peer sulla rete. Come menzionato in precedenza, è assolutamente plausibile che un utente finale non sappia nemmeno dell'esistenza della rete blockchain.

![Rete blockchain](images/blockchain_network_2-01.png "Rete blockchain di esempio")

*Figura 2. Una rete blockchain di esempio composta da quattro membri che utilizzano i canali per isolare i dati*

## Offerte di adesione {{site.data.keyword.IBM_notm}}

La seguente tabella riepiloga le opzioni di adesione correnti e future.  Il [piano Enterprise](enterprise_plan.html) è l'opzione di adesione attualmente disponibile.

|       | Entry      | [Enterprise](enterprise_plan.html)  | Enterprise Plus | Self Managed
| ------------------------- |--------------------------|-----|-----|------|
| **Cos'è incluso** | Livelli di servizio di base | **Livelli di servizio avanzati, pronto per la produzione enterprise** | Calcolo dedicato per le prestazioni e l'isolamento | Stack software per installare sulla tua infrastruttura e stabilire una connessione a una rete ospitata su questo servizio cloud. |
| **Politica di fatturazione** | Addebito orario | **Sottoscrizione mensile** | Sottoscrizione mensile | Sottoscrizione mensile |
| **Disponibilità** | Prossimamente | **Disponibile ora** | Prossimamente | Pianificato per il 2018 |

Registrati ora per [{{site.data.keyword.blockchainfull_notm}} membership ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)!

## {{site.data.keyword.IBM_notm}} Support 

{{site.data.keyword.IBM_notm}} offre un supporto sulle soluzioni {{site.data.keyword.blockchain}} implementate da {{site.data.keyword.IBM_notm}}. Per ulteriori informazioni sul supporto {{site.data.keyword.blockchainfull_notm}}, vedi [Richiesta di assistenza tecnica](ibmblockchain_support.html).

Per dei dettagli completi su tutte le funzioni e la funzionalità di Hyperledger Fabric v1.0, consulta [la documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/latest/).

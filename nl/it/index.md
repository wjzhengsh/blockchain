---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**ATTENZIONE:** prima di utilizzare un'offerta {{site.data.keyword.blockchainfull}} Platform, leggi le informazioni tecniche e di supporto nella sezione [Dichiarazione di non responsabilità](needtoknow.html).
{:shortdesc}

IBP ({{site.data.keyword.blockchainfull_notm}} Platform) è la sola piattaforma pronta per il business integrata che si occupa dell'intero ciclo di vita di una rete blockchain con più organizzazioni. È progettata per accelerare, mediante collaborazione in ciascuna fase, la creazione delle reti blockchain globali "sviluppate per il business" con le prestazioni e la sicurezza anche per i casi d'uso e i settori regolamentati più esigenti. Le offerte di rete gestite da {{site.data.keyword.IBM_notm}} su {{site.data.keyword.cloud_notm}} sono adatte per i clienti senza precedente esperienza con la blockchain. I clienti che hanno dimestichezza con l'utilizzo di Hyperledger Fabric possono distribuire componenti di rete o una rete sulla loro infrastruttura tramite ICP ({{site.data.keyword.cloud_notm}} Private) o AWS (Amazon Web Services).

{{site.data.keyword.blockchainfull_notm}} Platform fornisce diverse offerte per aiutare tutti i tipi di utenti ad iniziare il loro percorso blockchain e a passare le loro applicazioni in produzione.

|       | [**piano Starter**](#starter-enterprise)      | [ **piano Enterprise**](#starter-enterprise)  | [**IBP for ICP**](#ibp-for-icp) | [**IBP for AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **Cos'è incluso** | **Livelli di servizio di base, ambiente di sviluppo e test** | **Livelli di servizio avanzati e ambiente pronto per la produzione enterprise** |**Grafici Helm CA, Ordinante e Peer distribuibili** | **Peer Quick Start AWS**  |
| **Politica di fatturazione** | **Sottoscrizione mensile con [crediti cloud disponibili](howto/pricing.html#starter-plan-pricing)** | **Sottoscrizione mensile** |  **[Prezzi VPC](ibp-for-icp-about.html#ibp-icp-pricing) e Community Edition gratuita** | **Gratuita** |
| **Piattaforma Cloud**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud Private**| **AWS**|

**Attenzione:** non utilizzare il **piano Starter** per uso di produzione. È un ambiente di sviluppo e test e non è adatto per i carichi di lavoro di produzione.

## Funzionalità di {{site.data.keyword.blockchainfull_notm}} Platform

Le offerte {{site.data.keyword.blockchainfull_notm}} vengono sviluppate sulla base di codice [Hyperledger Fabric](reference/v10_fabric.html) che si avvale di un'architettura modulare per ottenere livelli di categoria aziendale di sicurezza, integrità dei dati, scalabilità e prestazioni per soddisfare le tue esigenze di business.

{{site.data.keyword.blockchainfull_notm}} Platform fornisce una rete blockchain altamente sicura e con autorizzazioni su cui i membri autenticati possono facilmente definire gli asset e creare soluzioni di business per la modifica e lo scambio di tali asset.  Con le offerte {{site.data.keyword.blockchainfull_notm}} Platform, puoi avvalerti di un framework di orchestrazione che ti consente di **organizzare rapidamente il tuo consorzio in una rete blockchain attiva**. {{site.data.keyword.blockchainfull_notm}} Platform fornisce una strumentazione facile da usare che è progettata per consentire a più istituzioni di unirsi e di creare una rete gestita democraticamente in modo semplice. Le attività di creazione, governance e gestione della rete diventano intuitive e trasparenti mediante un monitor dashboard integrato e i programmi di utilità forniti. Tralasciando l'altrimenti ingombrante processo di creazione di una rete e di implementazione della governance, i membri del consorzio possono invece **concentrarsi sulla distribuzione di smart contract e sul trasferimento di asset e informazioni**.

L'**Elevata disponibilità** per le risorse integrali della rete (peer, ordinanti, CA (Certificate Authority, Autorità di certificazione) elimina gli effetti paralizzanti che possono derivare dai singoli punti di errore. Un monitor dashboard integrato consente una facile gestione di queste risorse e fornisce un potente meccanismo per visualizzare asset e smart contract. *Supportata solo sulle offerte Starter ed Enterprise*.

La **modularità** dell'architettura di Hyperledger Fabric e la chiara separazione dei ruoli di rete forniscono un'infrastruttura che abilita la scalabilità e l'adattabilità a un'ampia gamma di casi d'uso. *Disponibile e supportata su tutte le offerte {{site.data.keyword.blockchainfull_notm}} Platform*.

I controlli e bilanciamenti che si verificano durante tutto il ciclo di vita di una transazione garantiscono risultati uniformi e totalmente controllati; inoltre, i libri mastro vengono mantenuti costantemente sincronizzati attraverso un'implementazione del protocollo gossip. Identità e controllo dell'accesso sono facilmente implementati attraverso le operazioni **sign/verify** che avvengono continuamente in tutta la rete, che sono *disponibili e supportate su tutte le offerte {{site.data.keyword.blockchainfull_notm}} Platform*.

Vengono forniti degli **strumenti di governance** per consentire ai membri di amministrare e gestire le regole di business fondamentali per la propria rete. Ad esempio, potresti voler implementare una politica che definisca quanti membri di una rete devono accordarsi per far aderire un nuovo membro. Oppure, è richiesta l'approvazione di ogni partecipante affinché venga eseguita una modifica. Le regole di governance sono fondamentali per qualsiasi tipo di rete di business e sono spesso estremamente elaborate. La strumentazione di governance (ad esempio, gli editor delle politiche) semplifica notevolmente questo processo. *Disponibile solo nelle offerte Starter ed Enterprise*.

Le reti piano Enterprise vengono eseguite in un ambiente **altamente sicuro e isolato** senza alcun accesso esterno (compreso l'accesso root) alle risorse di rete. I dati vengono crittografati in fase di elaborazione e mentre sono inattivi e i moduli di supporto della sicurezza hardware consentono di proteggere le chiavi digitali nel rispetto delle norme del settore. La virtualizzazione hardware viene utilizzata per eseguire ciascun nodo in un ambiente isolato, proteggendo di conseguenza gli altri nodi nella rete dai peer con un chaincode che ha potenzialmente un comportamento scorretto o malintenzionato. L'esecuzione dell'hash, le operazioni di firma/verifica e la comunicazione tra i nodi vengono accelerate grazie alle implementazioni di crittografia avanzate. *Disponibile solo con Enterprise*.

Per ulteriori informazioni su tutte le funzioni e la funzionalità di Hyperledger Fabric, vedi la [documentazione di Hyperledger Fabric![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/ "documentazione di Hyperledger Fabric").

La **Figura 1** mostra un esempio di rete blockchain distribuita composta da quattro membri (ognuno proprietario di due peer), CA (Certificate Authority, Autorità di certificazione) responsabili della distribuzione del materiale di identità crittografico e un servizio ordini che definisce le politiche e i partecipanti della rete. Il canale blu contiene tutti i e quattro i membri della rete e il canale giallo è limitato a soli tre membri, le banche B, C e D. Puoi anche vedere che la banca A ricopre il ruolo di iniziatore della rete e che la banca D esiste solo come un membro nel contesto del canale giallo. Infine, un utente o un'applicazione in possesso di un certificato x509 correttamente firmato possono inviare chiamate ai peer sulla rete.

![Rete blockchain](images/blockchain_network_2-01.png "Rete blockchain di esempio")

*Figura 1. Una rete blockchain di esempio con quattro membri che si avvalgono dei canali per isolare i dati*

## Sviluppa applicazioni per l'esecuzione sulla rete

Esplora e accelera lo sviluppo di blockchain avvalendoti delle prassi ottimali affinate in più di 400 impegni con i clienti:

* Garantisci uno stretto allineamento tra le attività di business e la tecnologia, che riduce notevolmente il tempo di sviluppo dell'applicazione blockchain (le applicazioni il cui sviluppo richiederebbe sei settimane possono essere create in meno di due giorni).
* Sviluppa rapidamente competenze di blockchain nella tua lista esistente di programmatori avvalendoti degli strumenti per sviluppatori {{site.data.keyword.blockchainfull_notm}}.
* Dai agli sviluppatori la flessibilità di apprendimento e sviluppo nel loro ambiente preferito con un set di strumenti aperto e moderno.

In quanto proprietario del business, puoi sviluppare il tuo caso d'uso con l'aiuto dell'ampia scelta di esperti del settore e della blockchain di IBM, che si riuniscono nel {{site.data.keyword.blockchainfull_notm}} Garage per sfruttare la piena potenza di {{site.data.keyword.blockchainfull_notm}} Platform.

In quanto sviluppatore, puoi allineare rapidamente e facilmente i requisiti di business e accelerare lo sviluppo di applicazioni blockchain nell'ambiente di rete {{site.data.keyword.blockchainfull_notm}} Platform utilizzando un playground interattivo per sviluppare, iterare e testare le reti di business. Questi strumenti sono progettati per trasformare le regole di business in codice di rete di business nel tuo ambiente preferito:

* **Esplora online**
  Avvaliti di [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop.html)<!--, which is powered by an open source development tool--> per apprendere i concetti chiave della blockchain, creare definizioni di rete e avvalerti di modelli del settore riutilizzabili e librerie di smart contract.

  Dopo aver sviluppato la tua rete di business, puoi distribuirla a una rete attiva in esecuzione su {{site.data.keyword.blockchainfull_notm}} Platform. Per ulteriori informazioni, vedi [Distribuzione di reti di business con piano Starter](./develop_starter.html) e [Distribuzione di reti di business con piano Enterprise](./develop_enterprise.html).

* **Installa in locale**
  Avvaliti di Hyperledger Fabric per eseguire attività di sviluppo e test direttamente sul tuo laptop. Per ulteriori informazioni, vedi il documento relativo alla [creazione della tua prima rete](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html).

* **Collabora in un ambiente cloud**
  Utilizza reti attive pronte per l'uso con le opzioni piano Starter ed piano Enterprise per sviluppare e condividere il tuo codice con altri. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](starter_plan.html) e [Informazioni sul piano Enterprise](enterprise_plan.html).

## Supporto di {{site.data.keyword.IBM_notm}}

{{site.data.keyword.IBM_notm}} offre diverse opzioni di supporto su soluzioni blockchain implementate da {{site.data.keyword.IBM_notm}}, Per ulteriori informazioni sul supporto {{site.data.keyword.blockchainfull_notm}}, vedi [Richiesta di assistenza tecnica](ibmblockchain_support.html).


## Piano Starter e piano Enterprise
{: #starter-enterprise}

I piani Starter ed Enterprise ti consentono di sviluppare e gestire reti blockchain decentralizzate con un servizio sicuro pronto per la produzione. Il **piano Starter** è un ambiente per imparare o iniziare a sviluppare reti blockchain e il **piano Enterprise** è un ambiente di produzione che offre elevati livelli di sicurezza e supporto. Inizia in piccolo e ingrandisci la tua rete in modo elastico man mano che le adesioni e i volumi delle transazioni aumentano avvalendoti delle seguenti funzioni:

* Un ambiente ad altissima sicurezza con molte funzioni di sicurezza hardware, firmware e software.
* Un'architettura consolidata con scalabilità, resilienza e disponibilità.
* Ottimizzato per le prestazioni e in esecuzione sul calcolo Linux più veloce del mondo.

La gestione della tua rete su {{site.data.keyword.blockchainfull_notm}} Platform include strumenti e funzionalità per semplificare le attività amministrative:

* Dashboard per il monitoraggio e la gestione delle risorse sulla rete.
* Upgrade senza problemi di tutto lo stack di codice.
* Supporto tecnico 24/7 integrato nel portale.
* Stack di sicurezza consolidato senza accesso privilegiato, resistenza a malware e manomissioni, crittografia al 100% e molte altre funzioni per le reti con dati sensibili nei settori regolamentati.

Il piano Starter e il piano Enterprise sono servizi di piattaforma su {{site.data.keyword.cloud_notm}} ed entrambi i piani si attengono ai termini dei servizi [termini dei servizi {{site.data.keyword.cloud_notm}}![Icona link esterno](images/external_link.svg "Icona link esterno")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}}") negli SLA (service level agreement). Nota che il provisioning di entrambe le reti piano Starter e piano Enterprise viene eseguito in **più ambienti** in data center geograficamente separati.

Per ulteriori informazioni sul piano Starter e sul piano Enterprise, vedi [Informazioni sul piano Starter](starter_plan.html) e [Informazioni sul piano Enterprise](enterprise_plan.html).Se sei pronto a iniziare, registrati ora per la tua [adesione IBP Starter o Enterprise ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) su {{site.data.keyword.cloud_notm}}!

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-for-icp}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP fornisce i componenti necessari per eseguire una rete blockchain sulla tua infrastruttura tramite ICP. I componenti includono Hyperledger Fabric, una CA (Certificate Authority, Autorità di certificazione), un ordinante e un peer, che distribuisci, gestisci e configuri utilizzando i grafici Helm Kubernetes. **Questa offerta è progettata per i clienti con un'esperienza avanzata di Hyperledger Fabric.**

IBP for ICP consente alle reti blockchain di essere distribuite su un cloud privato per soddisfare i requisiti di residenza dei dati, le regolamentazioni del mercato e la preferenza dell'infrastruttura. Semplifica la distribuzione di elementi essenziali di una rete blockchain nella tua infrastruttura tramite {{site.data.keyword.cloud_notm}} Private, un'applicazione basata su Kubernetes per lo sviluppo e la gestione in loco di applicazioni inserite nei contenitori.

 * Consente ai clienti di gestire le reti IBP con la loro infrastruttura. Una Community Edition gratuita consente ai clienti un'esecuzione nei loro ambienti isolati e sicuri, ma non verrà fornito alcun supporto.
 * Abilita i clienti a configurare Fabric su Kubernetes utilizzando grafici Helm e una documentazione dettagliata per le operazioni.
 * Dà diritto ai clienti a un supporto tecnico avanzato, tranne nel caso in cui si stia utilizzando la Community Edition.

 Per ulteriori informazioni su IBP for ICP, vedi [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](ibp-for-icp-about.html).

## {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #ibp-for-aws}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS abilita i peer ad avvalersi del profilo di connessione, delle CA (Certificate Authority) Hyperledger Fabric e del servizio ordini di una rete piano Starter o Enterprise esistente su {{site.data.keyword.cloud_notm}} per elaborare transazioni mediante un template Quick Start AWS. Quick Start ti consente di distribuire i peer utilizzando i template CloudFormation AWS. Questo template è per gli amministratori di sistema e i responsabili delle decisioni relative all'infrastruttura IT che desiderano configurare, distribuire ed eseguire rapidamente dei peer IBP ospitati su AWS che sono connessi a una rete piano Starter o Enterprise. Puoi utilizzare il template per creare un nuovo VPC (virtual private cloud) in AWS o distribuire il peer in un VPC esistente.

Quick Start completa le seguenti configurazioni:
 * Un'architettura altamente disponibile che si estende a due zone di disponibilità.
 * Un VPC configurato con sottoreti pubbliche secondo le prassi ottimali di AWS. Questo ti fornisce una tua rete virtuale su AWS.
 * Un gateway internet per consentire l'accesso a Internet.
 * Nelle sottoreti pubbliche, due peer in due zone di disponibilità (un peer in ogni sottorete).
 * In ogni sottorete pubblica, un contenitore peer con un database LevelDB integrato o un contenitore CouchDB secondario.

Per ulteriori informazioni su IBP for AWS, vedi [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](howto/remote_peer.html).

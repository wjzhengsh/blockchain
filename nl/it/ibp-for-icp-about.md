---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform per {{site.data.keyword.cloud_notm}} Private
{: #overview}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform rilascia IBM Blockchain Platform for ICP ({{site.data.keyword.cloud_notm}} Private), una piattaforma dell'applicazione per sviluppare e gestire applicazioni inserite nel contenitore basate su Kubernetes che consente agli utenti di distribuire CA (Certificate Authority, Autorità di certificazione), ordinanti e peer su x86, LinuxONE e IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for ICP è basato su Hyperledger Fabric v1.2.1 ed è distribuito utilizzando grafici Helm Kubernetes.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private è un prodotto in bundle per i clienti ICP per distribuire componenti blockchain nel loro ambiente locale. Dopo che hai importato il grafico Helm, puoi trovarlo come un tile {{site.data.keyword.blockchainfull_notm}} Platform nel catalogo ICP. Per ulteriori informazioni su ICP, vedi la documentazione per [{{site.data.keyword.cloud_notm}} Private versione 3.1.0 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0").

## Cosa offre {{site.data.keyword.blockchainfull_notm}} Platform for ICP

{{site.data.keyword.blockchainfull_notm}} Platform for ICP (IBM Cloud Private) ti consente di distribuire tutti i componenti fondamentali di una blockchain Hyperledger Fabric: una CA (Certificate Authority, Autorità di certificazione), un servizio ordini e dei peer. Ti offre la flessibilità di distribuire componenti diversi a seconda delle tue esigenze di business. Puoi utilizzare {{site.data.keyword.blockchainfull_notm}} for ICP per avviare una rete blockchain distribuendo e configurando un servizio ordini che associa tra loro le organizzazioni in un consorzio blockchain. Puoi anche distribuire i peer e unirti ad altre reti che utilizzano componenti basati su Fabric, comprese le reti {{site.data.keyword.blockchainfull_notm}} Platform distribuite nei cloud utilizzando ICP o le reti di piano Starter ed Enterprise ospitate su IBM Cloud. Per ulteriori informazioni sui blocchi di creazione di reti Hyperledger Fabric, vedi la [panoramica del componente blockchain](blockchain_component_overview.html).

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP è adatto per te

L'esecuzione di componenti {{site.data.keyword.blockchainfull_notm}} Platform fuori da {{site.data.keyword.cloud_notm}} ti fornisce maggiore flessibilità di crescere o di unirti a una rete blockchain. Aiuta i fondatori della rete a far crescere le loro reti consentendo a nuovi membri di unirsi pur utilizzando la piattaforma di loro scelta. Consentirà alle organizzazioni interessate a unirsi a reti blockchain di collocare i loro peer con le loro applicazioni esistenti o a eseguire l'integrazione con i tuoi system of record.

**Importante:** il processo per distribuire {{site.data.keyword.blockchainfull_notm}} Platform for ICP è complicato e presuppone un elevato grado di competenza in Fabric. Se non hai esperienza con Fabric, ICP o {{site.data.keyword.blockchainfull_notm}} Platform, e il tuo obiettivo è configurare un ambiente di sviluppo o un modello di verifica (PoC, Poof of Concept), considera invece l'utilizzo del [piano Starter](starter_plan.html). Nota anche che non tutte le configurazioni di distribuzione potenziali sono supportate in {{site.data.keyword.blockchainfull_notm}} Platform for ICP.

Gli utenti di questa offerta gestiranno la loro sicurezza e la loro infrastruttura. {{site.data.keyword.cloud_notm}} non fornisce tali servizi. Prima di iniziare, esamina [Considerazioni e limitazioni](#ibp-icp-considerations) nella prossima sezione.

## Considerazioni e limitazioni
{: #ibp-icp-considerations}

Prima di iniziare, assicurati di comprende le seguenti **considerazioni** e **limitazioni**:

- Sei responsabile del monitoraggio dell'integrità, della sicurezza, della registrazione e della gestione dell'uso delle risorse dei tuoi componenti.
- I componenti che vengono eseguiti in altri ambienti cloud non sono visibili nel Monitoraggio della rete delle reti in esecuzione su {{site.data.keyword.cloud_notm}}.
- I grafici Helm distribuiscono una singola istanza di un ordinante, peer o CA.
- Puoi distribuire più componenti a un singolo spazio dei nomi in ICP a condizione che abbiano dei nomi di release differenti.
- Non ci si può occupare dei componenti utilizzando l'interfaccia utente Swagger nell'interfaccia utente di Monitoraggio della rete.
- Il TLS reciproco non è supportato.

**Considerazioni sulle CA**
- Questo grafico Helm distribuisce una singola istanza della CA. Poiché è considerata prassi ottimale avere una CA separata per ciascuna organizzazione, potrebbe essere necessario distribuire diverse CA. Ad esempio, se intendi distribuire un ordinante e tre peer, ti occorreranno almeno due CA (una per l'organizzazione ordinante e l'altra per l'organizzazione peer).
- Anche se potresti scegliere di eseguire un database MySQL separato, questa opzione non è presente nel grafico Helm. Il grafico Helm, tuttavia, distribuirà un database SQLite all'interno della CA per gestire le necessità di database della CA, che includono la traccia del numero di iscrizioni per utente e gli eventuali certificati revocati.

**Considerazioni sugli ordinanti**
- Il servizio ordini è compatibile con qualsiasi componente alla v1.2 di Hyperledger Fabric.
- Questo grafico Helm distribuisce una singola istanza del servizio ordini SOLO (un ordinante). Nota che non è possibile distribuire più di un ordinante SOLO su un canale per rendere il servizio ordini altamente disponibile. Questo è un motivo per cui si ritiene che i servizi di ordinazione SOLO siano per gli ambienti di sviluppo, invece che per gli ambienti di produzione. Tuttavia, puoi distribuire più istanze del servizio ordini SOLO a reti differenti (ossia, con un consorzio separato).

**Considerazioni sui peer**

- Puoi connettere i tuoi peer solo alle reti blockchain che sono al livello Fabric v1.1 o v1.2.1. Puoi trovare la tua versione Hyperledger Fabric aprendo la [finestra Preferenze della rete](../v10_dashboard.html#network-preferences) nel tuo Monitoraggio della rete. Attieniti alle [istruzioni](#starter-enterprise-network-endpoints) per richiamare le informazioni sulla connessione peer da reti Starter o Enterprise.
- Il tipo di database del peer deve corrispondere al tipo di database della tua rete blockchain, LevelDB o CouchDB.
- L'interfaccia CouchDB Fauxton non è disponibile sul peer.
- [Gossip](../glossary.html#gossip) per i peer non è attualmente supportato. Ciò implica che anche le funzioni Fabric che dipendono da gossip, come i [dati privati ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "dati privati") e il [rilevamento di servizi ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "rilevamento di servizi"), non sono supportate.

## Prerequisiti del sistema
{: #prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP supporta i seguenti sistemi operativi:
- RHEL (Red Hat Enterprise Linux) 7.3, 7.4, 7.5
- Ubuntu 18.04 LTS e 16.04 LTS
- SLES (SUSE Linux Enterprise Server) 12 SP3

Il grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform for ICP è stato convalidato per l'esecuzione sui cluster {{site.data.keyword.cloud_notm}} Private v3.1.0 su Ubuntu Linux utilizzando i seguenti nodi di lavoro e la seguente archiviazione di supporto:

- **LinuxONE e IBM Z**: z/VM e KVM, che stanno utilizzando NFS.
- **x86**: Linux a 64 bit che utilizza GlusterFS.

## Licenza e determinazione dei prezzi
{: #ibp-icp-license-pricing}
{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private consiste in due edizioni: un'offerta a pagamento scaricabile da Passport Advantage e una Community Edition gratuita disponibile in [GitHub ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts").

### Licenza
{: #ibp-icp-license}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private fornisce i componenti necessari per eseguire una rete blockchain su una tua infrastruttura ed è distribuito come un'applicazione ICP. Puoi accedere a PPA e [scaricare il grafico Helm ](howto/helm_install_icp.html). Il supporto tecnico da {{site.data.keyword.blockchainfull_notm}} è incluso nell'acquisto.

{{site.data.keyword.blockchainfull_notm}} Platform on IBM Cloud Private Community Edition è un'offerta gratuita adatta per la valutazione e la sperimentazione ed è distribuito come un'applicazione ICP. Non utilizzare la Community Edition per la produzione. {{site.data.keyword.blockchainfull_notm}} Platform non fornisce supporto per la Community Edition. Puoi accedere al [pacchetto GitHub ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts") e scaricare il grafico Helm.

### Prezzi
{: #ibp-icp-pricing}

Il prezzo di {{site.data.keyword.blockchainfull_notm}} Platform on ICP è basato sul numero di VPC (Virtual Processor Core) utilizzato. Un VPC può essere un core virtuale assegnato un server virtuale o un core di processore fisico in un server non partizionato. Devi ottenere una titolarità di licenza per ogni VPC reso disponibile per {{site.data.keyword.blockchainfull_notm}} Platform. <!-- A VPC is a unit of measurement by which a program can be licensed.-->

Per ulteriori informazioni su come determinare il tuo utilizzo di VPC, vedi questo articolo su ["Virtual processor core (VPC)" ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "VPC (Virtual Processor Core)") in IBM Knowledge Center. Puoi utilizzare l'[IBM License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) per configurare e creare un report che puoi utilizzare per determinare il numero di VPC per cui hai bisogno di una licenza.


## Installazione di {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-icp-install}

{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private viene fornito come un file di grafico Helm che può essere installato in un cluster ICP locale. Dopo che hai installato il grafico Helm, puoi trovare {{site.data.keyword.blockchainfull_notm}} Platform come un'applicazione nel catalogo ICP.

- {{site.data.keyword.blockchainfull_notm}} Platform for ICP viene fornito tramite Passport Advantage. Devi disporre della licenza necessaria per accedere a [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html). Quando esegui l'acquisto, il supporto tecnico per {{site.data.keyword.blockchainfull_notm}} Platform è incluso.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition è progettato per l'esplorazione, lo sviluppo e l'esecuzione di test. È possibile accedere a questa versione gratuita di IBM Blockchain Platform for ICP tramite GitHub. **Nota:** {{site.data.keyword.blockchainfull_notm}} Platform non fornisce supporto per la Community Edition.

Per istruzioni su come installare il grafico Helm e i prerequisiti necessari, vedi [Installazione di {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private](howto/helm_install_icp.html).

Se non hai dimestichezza di {{site.data.keyword.cloud_notm}} Private e vorresti informazioni e suggerimenti sull'installazione e la distribuzione di ICP, vedi [Configurazione di {{site.data.keyword.cloud_notm}} Private](ICP_setup.html).

Dopo che hai installato {{site.data.keyword.blockchainfull_notm}} Platform for ICP, devi distribuire ciascun componente della tua rete singolarmente. Non puoi distribuire più componenti contemporaneamente. Visita la [guida alla distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for ICP](ibp_for_icp_deployment_guide.html) per imparare le prassi ottimali per fondare o unirsi a una rete blockchain prima di iniziare. Esamina quindi i passi per distribuire e gestire i singoli componenti nelle sezioni di seguito.

### Installazione di {{site.data.keyword.blockchainfull_notm}} Platform for ICP dietro un firewall
{: #ibp-icp-firewall}

Puoi distribuire i componenti di {{site.data.keyword.blockchainfull_notm}} Platform dietro un firewall senza avere accesso all'internet pubblico. Il pacchetto del grafico Helm scaricato include tutte le immagini Docker del componente Fabric che {{site.data.keyword.blockchainfull_notm}} Platform utilizza, senza estrarle da DockerHub durante la distribuzione.

Il pacchetto del grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform Community Edition non include le immagini Docker del componente Fabric necessarie. È configurato per scaricare queste immagini da DockerHub durante la distribuzione e non riuscirà ad eseguire tale operazione se non dispone dell'accesso all'internet pubblico. Puoi tuttavia eseguire qualche passo aggiuntivo per distribuire i componenti della Community Edition dietro un firewall. Per ulteriori informazioni, vedi [Installazione della Community Edition dietro un firewall](howto/helm_install_icp.html#helm-install-prereqs-firewall)


## Informazioni sulle CA (Certificate Authority, Autorità di certificazione) su ICP
{: #ibp-icp-ca}

Le Autorità di certificazione (CA, Certificate Authority) forniscono identità sulla rete. Una CA può essere considerata una sorta di notaio che funge da punto di riferimento di attendibilità tra più parti. A tutte le entità della rete viene assegnato un certificato firmato da una CA root che incapsula la loro identità digitale. Questo certificato è la radice di attendibilità per tutte le operazioni di firma e di verifica eseguite sulla rete. Per ulteriori informazioni sulle CA e sul ruolo che svolgono in una rete blockchain, vedi la [panoramica del componente blockchain](blockchain_component_overview.html).

La CA convaliderà l'identità ed emetterà i certificati per gli altri componenti nella tua rete che devi distribuire. Di conseguenza, devi distribuire una CA per la tua organizzazione prima di distribuire altri componenti.

- Per informazioni su come configurare e distribuire una CA dopo l'installazione del grafico Helm, vedi [Distribuzione di una CA (Certificate Authority, Autorità di certificazione) in {{site.data.keyword.cloud_notm}} Private](howto/CA_deploy_icp.html).

- Per informazioni su come utilizzare la tua CA per generare certificati e completate i passi prerequisiti per distribuire componenti aggiuntivi, vedi [Gestione di una CA (Certificate Authority, Autorità di certificazione) in {{site.data.keyword.cloud_notm}} Private](howto/CA_operate.html).

## Informazioni sugli ordinanti su ICP
{: #ibp-icp-orderer}

Gli ordinanti autenticano i client, ordinano le transazioni e trasmettono mediante broadcast le transazioni in una rete blockchain. Sono l'associazione comune delle reti blockchain basate su Hyperledger Fabric. Di conseguenza, l'organizzazione che fonda una rete deve distribuire e avviare un "servizio ordini" (il nodo o la raccolta di nodi che eseguono l'ordinazione) prima che altre organizzazioni possano distribuire i loro peer, unirsi a canali e iniziare transazioni sulla rete. Per ulteriori informazioni sugli ordinanti e sul ruolo che svolgono in una rete blockchain, vedi la [panoramica del componente blockchain](blockchain_component_overview.html#orderer).

Se stai fondando una rete blockchain, devi distribuire un ordinante. Dopo che è stato distribuito, puoi invitare altre organizzazioni al tuo consorzio, che possono quindi creare dei propri canali.

- Per informazioni su come configurare e distribuire un ordinante dopo che hai installato il grafico Helm, vedi [Distribuzione di un ordinante in ICP](howto/orderer_deploy_icp.html).

- Per informazioni su come creare un consorzio, vedi [Gestione di un ordinante su ICP](howto/orderer_operate.html).

## Informazioni sui peer su ICP
{: #ibp-icp-peer}

I peer sono un elemento fondamentale della rete perché ospitano i libri mastro e gli smart contract. Gli smart contract e i libri mastro sono utilizzati, rispettivamente, per incapsulare i processi condivisi e le informazioni condivise in una rete. Per ulteriori informazioni sui peer e sul ruolo che svolgono in una rete blockchain, vedi la [panoramica del componente blockchain](blockchain_component_overview.html).

- Quando sei pronto a unirti a una rete, puoi distribuire un peer che si unirà ai canali, approverà le transazioni e archivierà i libri mastro del canale. Per informazioni sulla distribuzione di un peer su ICP che si connetterà ad altri componenti su ICP, vedi [Distribuzione di un peer su ICP](howto/peer_deploy_icp.html). Per informazioni sulla distribuzione di un peer su ICP che si connetterà a una rete piano Starter o Enterprise, vedi il documento relativo alla [distribuzione di un peer che si connetterà a Starter o Enterprise](howto/peer_deploy_ibp.html).

- Dopo aver configurato il peer, devi completare diversi passi operativi prima di poter inoltrare transazioni e leggere il libro mastro distribuito dalla rete blockchain.

  - Se stai connettendo il tuo peer a un {{site.data.keyword.blockchainfull_notm}} Platform distribuito su ICP, vedi [Gestione dei peer in {{site.data.keyword.cloud_notm}} Private con una rete multi-cloud](howto/peer_operate_icp.html).
  - Se stai connettendo il tuo peer a una rete piano Starter o Enterprise distribuita su {{site.data.keyword.cloud_notm}}, vedi [Gestione dei peer su {{site.data.keyword.cloud_notm}} Private con piano Starter o Enterprise](howto/peer_operate_ibp.html).

## Considerazioni sulla sicurezza
{: #icp-security}

Poiché questi componenti sono distribuiti al di fuori di {{site.data.keyword.cloud_notm}}, sei tu responsabile della gestione della loro sicurezza. Ciò include importanti aree di sicurezza, come la gestione delle chiavi e la crittografia dei dati. Esamina i seguenti argomenti quando prendi in considerazione la sicurezza per i tuoi componenti.

### Sicurezza dei dati
{: #icp-security-data}

I piani Starter ed Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform utilizzano la crittografia dell'intero disco, che è basata sulla[crittografia a chiave simmetrica![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Crittografia simmetrica") per proteggere tutti i dati utilizzati dalle reti. Devi adottare misure simili nel tuo ambiente per proteggere i dati del tuo peer.

I dati nel tuo database dello stato, indipendentemente dal fatto che tu usi LevelDB o CouchDB, non sono crittografati. Puoi utilizzare la crittografia a livello delle applicazioni per proteggere i dati inattivi nel tuo database dello stato.

### Residenza dei dati
{: #data-residency}

I requisiti di residenza dei dati possono imporre che l'elaborazione e l'archiviazione di tutti i dati del libro mastro blockchain rimangano entro i confini di un singolo paese (o entro quale altro limite definito). Per ulteriori informazioni su come completare la residenza dei dati, vedi [Residenza dei dati](#icp-data-residency).

### Gestione delle chiavi
{: #icp-security-key-management}

La gestione delle chiavi è un aspetto critico della sicurezza. Se una chiave privata è compromessa o persa, attori ostili potrebbero essere in grado di accedere ai tuoi dati e alle tue funzionalità. IBM utilizza dispositivi fisici denominati HSM ([Hardware Security Module](../glossary.html#hsm)) per archiviare le chiavi private delle reti piano Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform.

Quando distribuisci un componente su ICP, sei responsabile della gestione delle tue chiavi private. Sebbene {{site.data.keyword.blockchainfull_notm}} Platform generi le chiavi private, tali chiavi non vengono archiviate su Platform. È essenziale archiviare le chiavi in modo protetto in modo che non vengano compromesse. Puoi trovare la chiave privata del tuo componente nella cartella keystore dell'MSP del peer, nella directory `/mnt/crypto/peer/peer/msp/keystore/` all'interno del tuo componente. Per ulteriori informazioni sui certificati all'interno del tuo peer, vedi la sezione [Membership Services Provider](../certificates.html#msp) dell'esercitazione [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

Puoi utilizzare Key Escrow per recuperare le chiavi private perse. Questo deve essere eseguito prima della perdita di qualsiasi chiave. Se non è possibile recuperare una chiave privata, devi ottenere nuove chiavi private registrando una nuova identità con la tua Certificate Authority (Autorità di certificazione). Devi inoltre rimuovere e sostituire il tuo signCert da tutti i canali a cui ti sei unito.

### TLS
{: #icp-security-tls}

[Transport Layer Security ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Una panoramica dell'handshake SSL o TLS") (TLS) è integrato nel modello di attendibilità di Hyperledger Fabric. Tutti i componenti Starter ed Enterprise in {{site.data.keyword.blockchainfull_notm}} Platform utilizzano TLS per autenticarsi e comunicare tra loro. Pertanto, i componenti di rete su Platform devono essere in grado di completare un handshake TLS con i tuoi peer. Ciò implica, tra le altre cose, che devi abilitare il pass-through, utilizzando ad esempio l'elenco elementi consentiti (whitelist), nel tuo firewall dalle applicazioni client al tuo peer.

<!--
You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](../v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.
-->

### Configurazione di Membership Service Provider
{: #icp-security-MSP}

I componenti di {{site.data.keyword.blockchainfull_notm}} Platform utilizzano le identità tramite gli MSP (Membership Service Provider). Gli MSP associano i certificati emessi dalle CA ai ruoli di rete e di canale. Per ulteriori informazioni sulla modalità di funzionamento degli MSP con il peer, vedi [Membership Service Provider (MSP)](../certificates.html#msp).

### Sicurezza delle applicazioni
{: #icp-security-appl}

Poiché tutti i richiami del chaincode sono firmati, Fabric gestisce la sicurezza delle applicazioni. Inoltre, Fabric include anche controlli a livello di applicazione basati su ACL.

## Residenza dei dati
{: #icp-data-residency}

Poiché le reti blockchain non rilevano il tipo di dati che viene elaborato, è a volte necessario eseguire delle operazioni supplementari per mantenere al sicuro certi tipi di dati. Il requisito più comune sulla residenza dei dati è associato alle leggi di alcuni paesi, che impongono che tutti i dati elaborati e memorizzati in un sistema IT debbano rimanere all'interno dei confini di un determinato paese. Allo stesso modo, alcune aziende in settori altamente regolamentati, come il governo, la sanità e i servizi finanziari, richiedono che i dati siano memorizzati interamente dietro il proprio firewall. Pertanto, per ottenere la residenza dei dati, tutti i componenti della rete blockchain devono far parte dello stesso [canale](glossary.html#channel) e risiedere all'interno del confine di un singolo paese.

Per soddisfare i requisiti di residenza dei dati, è importante comprendere l'architettura di Hyperledger Fabric che sta alla base di {{site.data.keyword.blockchainfull_notm}} Platform. L'architettura è incentrata su tre componenti chiave: un servizio ordini (costituito da ordinanti), CA (Certificate Authority, Autorità di certificazione) e peer. Un peer riceve gli aggiornamenti di stato ordinati sotto forma di blocchi dal servizio ordini e mantiene lo stato e il libro mastro. Pertanto, un peer e un servizio ordini hanno una relazione diretta. Il libro mastro contiene i valori più recenti per tutte le chiavi e i dati inclusi nei log delle transazioni.

Inoltre, le applicazioni client utilizzano gli [SDK Fabric](v10_application.html#using-the-fabric-sdks) per inviare transazioni ai peer e al servizio ordini. Queste transazioni includono dati della [serie di lettura-scrittura![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Semantica della serie di lettura-scrittura"), che contengono le coppie chiave-valore sul libro mastro.

Se la residenza dei dati all'interno del paese è un requisito, l'ordinante, i peer e le applicazioni client devono risiedere nello stesso paese. Quando viene creata una rete {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}}, hai la possibilità di selezionare un'ubicazione per la rete. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Per ulteriori informazioni sulle regioni e sulle ubicazioni, vedi [Regioni e ubicazioni {{site.data.keyword.blockchainfull_notm}} Platform](reference/ibp_regions.html). 

### Un caso d'uso per la residenza dei dati

Prendi in considerazione una rete {{site.data.keyword.blockchainfull_notm}} Platform che include l'ordinante e la CA insieme a un consorzio di quattro organizzazioni. Le organizzazioni hanno uno o più nodi peer e tutte e quattro le organizzazioni fanno parte di un singolo canale e tutti i componenti della rete risiedono nella regione (ad esempio Francoforte) dove è stata distribuita la rete {{site.data.keyword.blockchainfull_notm}} Platform. Infine, le applicazioni client che interagiscono con i peer risiedono anch'esse in Germania. 

In questo esempio, la residenza dei dati viene mantenuta.

![Residenza dei dati quando tutti i componenti si trovano nello stesso paese](images/remote_peer_data_res_1.png "Residenza dei dati quando tutti i componenti si trovano nello stesso paese")
*Figura 1. Residenza dei dati quando tutti i componenti si trovano nello stesso paese*

Prendiamo ora in considerazione le implicazioni quando un **peer distribuito** si unisce a una delle organizzazioni. Un peer distribuito può risiedere nella stessa regione del resto della rete o dovunque al di fuori della regione della rete {{site.data.keyword.blockchainfull_notm}} Platform:

-	Se il peer risiede nello stesso paese del resto della rete, la residenza dei dati viene mantenuta. Tutti i dati di libro mastro restano all'interno della Germania come nella **Figura 1** in alto.
-	Se il peer risiede in un altro paese (ad esempio negli Stati Uniti), la residenza dei dati non viene più mantenuta poiché i dati nel libro mastro del peer sono condivisi fuori dai confini nazionali.

Per risolvere questo problema, è possibile utilizzare i **canali** per segregare i dati a un sottoinsieme di peer sulla rete. Quando la rete {{site.data.keyword.blockchainfull_notm}} Platform contiene un peer distribuito e ordinanti che oltrepassano i confini nazionali, i canali forniscono l'isolamento dei dati di libro mastro dalle organizzazioni con dei peer fuori dai confini nazionali.

**Nota:** gli ordinanti si trovano sempre nella regione del data center che hai selezionato per ospitare la rete. Non è possibile avere più ordinanti oltre i confini nazionali. Tuttavia, i peer possono trovarsi nel data center o in un'ubicazione remota al di fuori di {{site.data.keyword.cloud_notm}}. 

![Residenza dei dati quando i peer si trovano fuori dal paese della regione di IBM Blockchain Platform](images/remote_peer_data_res_2.png "Residenza dei dati quando i peer si trovano fuori dal paese della regione di IBM Blockchain Platform")
*Figura 2. Residenza dei dati quando i peer si trovano fuori dal paese della regione di IBM Blockchain Platform*

Nella **Figura 2**, la residenza dei dati non è richiesta per `OrgC` e `OrgD`. In effetti, `OrgD` ora include due peer distribuiti, `OrgD-peer1` e `OrgD-peer2`, che si trovano negli *Stati Uniti*. Pertanto, affinché `OrgA`, `OrgB` e le loro rispettive applicazioni client e i loro rispettivi peer che risiedono in Germania isolino i dati di libro mastro sul canale `X`, viene creato un nuovo canale `Y` per `OrgC` e `OrgD`.

Per una comprensione più approfondita del flusso di dati sulla rete {{site.data.keyword.blockchainfull_notm}} Platform, vedi la [documentazione di Fabric sul flusso di transazioni![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flusso di transazioni").

In futuro, la nuova tecnologia in Hyperledger Fabric migliorerà la capacità di ottenere un'ulteriore residenza dei dati utilizzando le [Raccolte di dati privati ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Raccolte di dati privati") e la ZKP (zero knowledge proof, dimostrazione a conoscenza zero).

- Una raccolta di dati privati garantisce che i dati privati siano condivisi peer-to-peer (tramite il protocollo gossip) solo con i peer autorizzati a vederli, ad esempio, i peer che si trovano all'interno dei confini nazionali. I dati vengono memorizzati in un database privato sul peer. Qui il servizio ordini non è coinvolto e non vede i dati privati. Un hash di questi dati viene scritto nei libri mastro di ogni peer sul canale. L'hash utilizzato per la convalida dello stato funge da prova della transazione e può essere utilizzato per scopi di controllo. I dati privati sono disponibili per le reti in {{site.data.keyword.blockchainfull_notm}} Platform in esecuzione su Fabric versione 1.2.1. Tuttavia, la funzione dei dati privati non è disponibile per i peer in esecuzione in ICP poiché le reti ICP attualmente non utilizzano gossip.

- Una ZKP (zero knowledge proof, dimostrazione a conoscenza zero) consente a un “prover” (dimostratore) di assicurare a un “verifier” (verificatore) di essere a conoscenza di un segreto senza rivelare il segreto stesso. 

Puoi ottenere maggiori informazioni su queste tecnologie nel white paper sulle [Transazioni private e confidenziali con Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transazioni private e confidenziali con Hyperledger Fabric").

## Richiesta di assistenza tecnica
{: #ibp-icp-support}

Per informazioni sulle offerte di supporto digitale, fai riferimento a [risorse e forum di supporto](ibmblockchain_support.html#resources) del supporto di IBM Blockchain Platform.

### {{site.data.keyword.blockchainfull_notm}} Platform for ICP

Se hai acquistato una licenza di IBM Blockchain Platform for ICP e vuoi contattare il supporto clienti, vedi le informazioni correlate ad [accesso alla community di IBM Support e apertura di un ticket di supporto![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "{{site.data.keyword.blockchainfull_notm}} Platform for ICP - supporto").

### {{site.data.keyword.blockchainfull_notm}} Platform Community Edition

La Community Edition è progettata per l'esplorazione, lo sviluppo e l'esecuzione di test. IBM Blockchain Platform non fornisce il supporto per {{site.data.keyword.blockchainfull_notm}} Platform Community Edition.

Se riscontri dei problemi correlati ai tuoi componenti blockchain, puoi utilizzare le risorse per sviluppatori di blockchain e i forum di supporto gratuiti e richiedere assistenza da IBM e dalla community di Fabric. Per ulteriori informazioni, vedi [Risorse blockchain e forum di supporto](ibmblockchain_support.html#resources). 

Per problemi correlati a IBM Cloud Private, puoi avvalerti [sia del supporto digitale gratuito che del supporto a pagamento offerto da ICP](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us).

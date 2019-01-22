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

# Guida alla distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for ICP

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Le reti blockchain che sono basate su [Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.2/) possono essere distribuite in una gamma quasi illimitata di configurazioni per supportare altrettanti casi d'uso. Nonostante questa malleabilità, esiste un certo numero di prassi ottimali, soprattutto per quanto riguarda la corretta **sequenza** per configurare e distribuire i componenti di rete.

La guida alla distribuzione mostra la corretta sequenza per configurare una rete {{site.data.keyword.blockchainfull}} Platform funzionante su ICP ({{site.data.keyword.cloud_notm}} Private), oltre alle prassi ottimali e ai fattori da prendere in considerazione quando si eseguono distribuzioni. Tuttavia, le regole di base continuano a essere valide se intendi configurare solo una CA, un ordinante o un peer funzionanti. Nota: il servizio ordini SOLO, che distribuisce solo un singolo nodo ordinante, non è progettato per gli ambienti di produzione. Qualsiasi rete o canale che esegue SOLO non può essere considerato un ambiente di "produzione". Tuttavia, è possibile distribuire il peer e la CA in un ambiente di produzione, soprattutto quando sono altamente disponibili.

**IMPORTANTE**: il processo per distribuire IBP su ICP è difficile e presuppone un elevato grado di competenza in Fabric. Se non hai esperienza con Fabric, IBP o ICP e il tuo obiettivo è configurare un ambiente di sviluppo o un modello di verifica (PoC. Proof of Concept), dovresti prendere invece in considerazione il [piano Starter](starter_plan.html). Nota anche che non tutte le configurazioni di distribuzione potenziali sono supportate. A causa della difficoltà, il presupposto è che un esperto di Fabric esaminerà il processo di distribuzione e di connessione tra di loro dei componenti prima di passare i compiti amministrativi ad altre parti. Tali esperti sono il pubblico a cui si rivolgono questa guida e la documentazione che abbiamo per i componenti del grafico Helm in generale.

## Decisioni relative alla tua configurazione di rete

La struttura di una rete blockchain deve essere dettata dal caso d'uso. Queste decisioni di business fondamentali varieranno in situazioni diverse, ma prendiamone in considerazione qualcuna.

* Configurazione di un **ambiente di sviluppo**

  Il [piano Starter](starter_plan.html) è stato creato per tale scopo ma, se vuoi gestire i tuoi componenti in un ambiente di sviluppo, ti servirà una configurazione di base che consiste in un ordinante e un peer (con una CA per ciascuna organizzazione). Per ciascuna organizzazione, potresti decidere di avere solo un singolo ordinante e un singolo peer, simile alla configurazione della [rete fabcar](https://hyperledger-fabric.readthedocs.io/en/release-1.2/understand_fabcar_network.html). Allo stesso modo, potresti decidere che hai bisogno solo di un singolo utente amministratore per tutti questi componenti, piuttosto che creare degli utenti separati per ciascun componente.

* Distribuzione dei **componenti di produzione** per una nuova rete oppure a una rete esistente

  I componenti di produzione, e le reti di produzione, hanno esigenze diverse rispetto agli ambienti di sviluppo o ai modelli di verifica (PoC, Proof of Concept). In primo luogo, l'alta disponibilità diventa una priorità. Un servizio ordini SOLO, che include solo un singolo ordinante (ed è pertanto un singolo punto di errore) è, per definizione, non destinato alla produzione.

**Nota:** questa guida alla distribuzione non coprirà ogni iterazione e potenziale configurazione di rete; fornisce invece delle linee guida e regole comuni da prendere in considerazione.

## Configurazione di un cluster Kubernetes ICP

Dopo che hai deciso la struttura di rete, configura un cluster Kubernetes su ICP per soddisfare i tuoi casi di uso. Per ulteriori informazioni, vedi [Configurazione di {{site.data.keyword.cloud_notm}} Private](ICP_setup.html).

## Configurazione delle tue CA

Nelle reti blockchain basate su Fabric, il primo componente che deve essere distribuito è una CA. Ciò è dovuto al fatto che la configurazione di un componente deve includere almeno una identità utente autorizzata a gestirlo prima che il componente venga distribuito.

A volte questo può essere difficile da capire concettualmente. A un dispositivo elettronico tipo un laptop, ad esempio, può spesso accedere chiunque. Si presume che la prima persona che lo accende sia il "proprietario" e gli è pertanto consentito registrare e stabilire delle password che tutti gli utenti successivi devono conoscere. Tuttavia, non è questo il modo in cui sono configurati i componenti nelle reti blockchain basate su Fabric. Nei peer e negli ordinanti devono essere integrate delle informazioni amministrative, quando ne viene eseguita la distribuzione iniziale.

**Si consiglia di distribuire una singola CA per ogni organizzazione.** Ad esempio, se distribuisci tre peer associati ad un'organizzazione e un ordinante associato ad un'altra organizzazione, hai bisogno di distribuire due CA. Una CA è per l'organizzazione peer e l'altra è per l'organizzazione ordinante.

Per evitare una regressione infinita di CA (dove ogni CA deve essere collegata ad un'altra CA, in una progressione infinita), alle CA è associata una coppia predefinita di nome utente e password. Specifichi questo nome utente e questa password quando la CA viene distribuita. Questa CA è la "CA radice", la radice di attendibilità per il tuo componente. Nelle reti di produzione, si consiglia di distribuire almeno un'altra CA che ottiene il suo certificato da questa CA radice e di utilizzare tale CA, nota come "CA intermedia", per emettere certificati a utenti e componenti. Tuttavia, in questa release, l'uso di CA intermedie non è supportato.

Ogni organizzazione deve avere una CA per l'iscrizione e una CA TLS.  Quando distribuisci una CA su ICP, per impostazione predefinita viene distribuita anche una CA TLS nello stesso contenitore. Questa CA TLS genera e gestisce i tuoi certificati TLS. La CA su una rete piano Starter o Enterprise non contiene una CA TLS ma contiene una CA che viene utilizzata per l'iscrizione. Pertanto, se intendi connettere il tuo peer a una rete piano Starter o Enterprise, prima di distribuire il tuo peer devi anche [distribuire una CA in ICP](howto/CA_deploy_icp.html) che fungerà da CA TLS per tale peer. Vedi anche i [prerequisiti per la connessione di un peer ICP a IBP](howto/peer_deploy_ibp.html#prerequisites-peer-ibp). Nota: la CA TLS è utilizzata solo per emettere certificati e può essere arrestata quando tale attività è completata.

Per ulteriori informazioni su TLS, vedi [Securing Communication With Transport Layer Security (TLS) ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/enable_tls.html "Securing Communication With Transport Layer Security (TLS)") nella documentazione di Hyperledger Fabric

### Preparazione degli MSP per l'ordinante e i peer

Poiché il processo IBP for ICP è così elaborato, si consiglia di utilizzare una singola identità di utente di gestione come amministratore per tutti i nodi componente di rete durante l'impostazione iniziale. Questo dovrebbe ridurre gli errori di distribuzione e di connessione assicurando che un singolo utente possa impostare la configurazione e le connessioni tra i vari componenti per garantire che stiano funzionando correttamente. Tuttavia, è estremamente importante che ogni componente abbia dei certificati diversi. A volte è facile confondersi in merito a questi certificati. L'entità che firma una proposta di transazione non è l'amministratore del peer ma il **peer stesso**. Pertanto, il peer deve essere iscritto e disporre di un certificato che allega a qualsiasi cosa faccia, nonché di una chiave privata che può usare per generare determinati tipi di firme. Per ulteriori informazioni sulle identità e sulle autorizzazioni in una rete blockchain basata su Fabric, vedi [Identity ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/identity/identity.html "Identity") e [Membership ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/membership/membership.html "Membership") nella documentazione di Fabric.

Il client CA Fabric, che dovrebbe essere installato attenendosi alle istruzioni contenute nella [documentazione relativa alla gestione di una CA](howto/CA_operate.html#fabric-ca-client), viene utilizzato per registrare e iscrivere le identità. Questa identità è l'amministratore dei componenti di rete che distribuirai e ti consente di modificare le configurazioni e di connettere i componenti tra loro. Se vuoi trasferire l'amministrazione di questi componenti ad altri utenti in un secondo momento, puoi registrare e iscrivere nuovi amministratori per tali componenti e rimuovere te stesso come amministratore, se necessario.

Quando l'ordinante o il peer vengono distribuiti, un contenitore `init` associato agli ordinanti o al peer utilizzerà un oggetto segreto Kubernetes per creare l'MSP per il componente. Per informazioni su come creare un oggetto segreto, vedi [Gestione di una CA](howto/CA_operate.html). Come abbiamo detto in precedenza, ricordati che devi configurare una CA e ripetere questo flusso per ciascuna organizzazione.

## Distribuzione di ordinanti e peer

Dopo che un segreto Kubernetes è stato creato, sei pronto a distribuire un componente. Se intendi stabilire dei canali, ti consigliamo di distribuire l'ordinante prima di qualsiasi peer. Assicurati di utilizzare nomi organizzazione differenti per tutti i tuoi componenti.

- **[Distribuisci l'ordinante](howto/orderer_deploy_icp.html)**. Nota che, attualmente, è supportato esclusivamente il servizio ordini SOLO. Non hai opzioni nel processo di distribuzione correlate al calcolo.

- **[Distribuisci il peer da connettere a un ordinante ICP](howto/peer_deploy_icp.html)**. Nel grafico Helm, puoi specificare diverse opzioni per la distribuzione del tuo peer, compreso il tipo di database. Assicurati che l'ID di MSP dell'organizzazione del peer sia diverso dall'ID di MSP dell'organizzazione dell'ordinante.

- **[Distribuisci il peer da connettere a una rete IBP](howto/peer_deploy_ibp.html)**. Il processo per distribuire un peer e connetterlo a una rete [piano Starter](starter_plan.html) o [piano Enterprise](enterprise_plan.html) in {{site.data.keyword.cloud_notm}} è diverso perché utilizza i profili di connessione e l'IU Monitoraggio della rete. Nota: l'organizzazione a cui appartiene il peer deve già essere unita a un canale nella rete. Ancora una volta, assicurati che l'ID di MSP dell'organizzazione del peer sia diverso dall'ID di MSP dell'organizzazione dell'ordinante.

## Crescita della tua rete

Se il tuo obiettivo è configurare un ambiente di sviluppo o un modello di verifica (PoC, Proof of Concept), dovrai aggiungere le organizzazioni peer al canale del sistema ordinante creato durante la distribuzione dell'ordinante.Questo è un processo articolato in più passi che utilizza ogni componente ed è documentato negli argomenti operativi pertinenti.

1. Prima di poter aggiungere un'organizzazione, devi crearla e impostare il relativo ID di MSP. Vedi [Gestione di una CA](howto/CA_operate.html#deploy-orderer-peer).

2. Dopo essere stata creata, l'organizzazione può essere aggiunta al canale del sistema ordinante. Per ulteriori informazioni, vedi [Gestione di un ordinante](howto/orderer_operate.html#add-organizations-to-consortium).

3. Quando è elencata nel canale del sistema ordinante, un'organizzazione è un membro del "consorzio" ed è abilitata a creare un canale dell'applicazione, i tipi di canali su cui si verificano le transazioni. Per ulteriori informazioni su come creare un canale, vedi [Gestione di un peer](howto/peer_operate_icp.html#peer-icp-channeltx).

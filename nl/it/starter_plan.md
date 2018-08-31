---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informazioni su piano Starter
{: #overview}

{{site.data.keyword.blockchainfull}} Platform piano Starter è un'opzione entry-level che consente alle organizzazioni di simulare delle reti blockchain di più organizzazioni, sviluppare rapidamente delle applicazioni e lavorare con reti di business e smart contract di esempio. Offre anche la stessa esperienza di IU delle altre opzioni di adesione, aiutando ad eliminare eventuali curve di apprendimento. Le reti piano Starter sono create con Hyperledger Fabric V1.1.
{:shortdesc}

**Note**:
1. {{site.data.keyword.blockchainfull_notm}} Platform piano Starter è un ambiente di sviluppo e test  non è adatto per i carichi di lavoro di produzione. Se ti serve un ambiente di produzione, vedi [Informazioni su piano Enterprise](enterprise_plan.html). Potresti controllare [Considerazioni su piano Starter](#considerations) prima di utilizzare piano Starter.
2. Il piano Starter passa da Beta a GA il 14 giugno 2018. Se hai delle reti Beta, puoi leggere le [considerazioni sulla migrazione](#beta-to-ga).

Registrati per la tua adesione a [{{site.data.keyword.blockchainfull_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) e prova il piano Starter con i [crediti di prova](howto/pricing.html#starter-plan-pricing) ora! <!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## Cosa offre il piano Starter

- **_Rete pronta in un clic_**  
    Il piano Starter ti offre una rete blockchain attiva con un singolo clic. Ogni rete è dotata di un servizio ordini, CA, due organizzazioni (con un peer per ogni organizzazione) e un canale predefinito su cui eseguire transazioni e distribuire il chaincode. {{site.data.keyword.blockchainfull_notm}} Platform gestisce la creazione e la configurazione di questa rete (potrai aggiornarla dopo che sarà attiva), consentendoti di concentrarti sullo sviluppo. Le reti piano Starter vengono create su Hyperledger Fabric V1.1 e utilizzano CouchDB come database libro mastro. <!--The free trial provides you up to two organizations and two peers.-->
- **_Efficienza in termini di costi_**  
    L'opzione di adesione piano Starter fornisce molte delle stesse funzionalità blockchain delle opzioni di adesione piano Enterprise, ma a un costo più basso. Durante un periodo di prova del piano Starter, puoi eseguire il provisioning di una rete blockchain con le risorse di rete di base gratuitamente.
- **_Simulazione di una rete con più organizzazioni_**  
    Puoi usare piano Starter per simulare la creazione di una rete con più organizzazioni. Non devi effettivamente invitare altre organizzazioni alla tua rete, ma puoi agire tu stesso come altre organizzazioni. Questo meccanismo ti consente di apprendere in che modo una nuova organizzazione può aderire alla rete e come più organizzazioni lavorano insieme nella rete e così via. Puoi passare da un'organizzazione all'altra dal monitoraggio della rete per visualizzare e gestire la rete dalla prospettiva di organizzazioni differenti.
    **Nota**: se aggiungi ulteriori risorse, potresti incorrere in costi extra rispetto alle risorse di base di prova gratuite che consistono in due organizzazioni e due peer.
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- Integrazione delle applicazioni **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    Il piano Starter ti consente di distribuire reti di business che sono state sviluppate su [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login). Per ulteriori informazioni, vedi [Importazione di applicazioni IBP: Develop](link).

## Gruppi di destinatari

Se ti riconosci in una di queste situazioni, piano Starter è adatto per te per iniziare la tua esperienza con blockchain.
- **_Vuoi saperne di più su {{site.data.keyword.blockchainfull_notm}} Platform._**  
    Se sei incuriosito da {{site.data.keyword.blockchainfull_notm}} Platform o anche se non conosci ancora la blockchain, piano Starter ti offre un ottimo modo per imparare come sviluppare e gestire una vera rete blockchain. Puoi trovare i componenti in cui si articola una rete, gestire l'adesione, imparare come distribuire e gestire il chaincode (noto anche come "smart contract"), come aggiungere canali (e come gestire le autorizzazioni ad essi) e tenere traccia di quando viene generato un nuovo blocco, proprio come in una rete di produzione.
- **_Creazione di soluzioni demo su una rete attiva._**  
    Il piano Starter fornisce un potente ambiente per dimostrare le applicazioni blockchain. La rete blockchain pronta per l'uso consente delle rapide presentazioni ai colleghi, ai dirigenti e ai partner mediante gli strumenti operativi e di gestione forniti dal monitoraggio della rete.
- **_Andare oltre lo sviluppo di una singola organizzazione._**  
    Il piano Starter ti consente di agire come più organizzazioni, il che ti consente di appurare il modo in cui IBM Blockchain Platform gestisce attività collaborative quali la creazione di canali e l'istanziazione di chaincode, nonché l'esecuzione di test di applicazioni e il richiamo di transazioni. Puoi anche invitare altre persone a collaborare a una rete piano Starter (come negli piano Enterprise). Questo ti aiuta a creare in un ambiente più realistico, con più parti che approvano il chaincode e le transazioni.
- **_Sviluppo e test iterativi di applicazioni blockchain._**  
    Piano Starter ti offre un'area di gestione temporanea per sviluppare e testare continuativamente il tuo codice su una rete blockchain. Il passaggio al cloud consente agli sviluppatori e ai tester di concentrarsi sulle funzionalità e di passare facilmente da test d'unità a test funzionali. Piano Starter fornisce le stesse funzionalità di rete blockchain così come gli strumenti operativi e di gestione di piano Enterprise. Quando sei pronto ad eseguire il push del tuo progetto a uno degli piano Enterprise, puoi operare nello stesso modo che in piano Starter, ma con più opportunità di far crescere la tua rete.


## Considerazioni su piano Starter
{: #considerations}

Piano Starter è un punto d'ingresso a {{site.data.keyword.blockchainfull_notm}} Platform ed è pensato per scopi di sviluppo e test.  Controlla i seguenti elementi prima di utilizzare piano Starter.

- **Requisiti dell'account {{site.data.keyword.cloud_notm}} **  	
    Devi disporre di un account {{site.data.keyword.cloud_notm}} a pagamento, ad esempio uno di tipo **Pagamento a consumo**. Tutti i piani di adesione che {{site.data.keyword.blockchainfull_notm}} Platform offre richiedono un account a pagamento {{site.data.keyword.cloud_notm}}. Per eseguire l'upgrade del tuo account a un tipo Pagamento a consumo, vai a **Gestisci** > **Fatturazione e utilizzo** > **Fatturazione** nella console {{site.data.keyword.cloud_notm}} e fai clic su **Aggiungi carta di credito**.  
- **Differenze rispetto al piano Enterprise**
    - [Autorità di certificazione (CA, Cerificate Authority)](glossary.html#ca) e [servizio di ordinazione](glossary.html#orderer) non sono tolleranti ai guasti perché ciascuna organizzazione ha una sola CA e una rete ha un solo [ordinante](glossary.html#orderer).
    - Il servizio ordini usa esclusivamente il [consenso](glossary.html#consensus) [SOLO](glossary.html#SOLO). Una rete piano Starter consiste in un solo [ordinante](glossary.html#orderer) che esegue il consenso per tutti i peer.
    - [HSM (Hardware Security Module)](glossary.html#hsm) non è disponibile per salvaguardare e gestire le chiavi digitali per un'elaborazione della crittografia e un'autenticazione avanzate.
- **Limitazione della risorsa di rete**  
    Il piano Starter assegna 1 CPU e 2 Gi di RAM per ogni peer oppure 20 Gi di archiviazione per ogni istanza del servizio {{site.data.keyword.cloud_notm}}. 
- **Eliminazione rete non attiva**  
    Dopo che hai creato una rete piano Starter, se non visiti la rete o immetti una transazione su di essa per 15 giorni, {{site.data.keyword.blockchainfull_notm}} Platform eliminerà la rete.
    - Se non hai più bisogno della rete, non devi fare nulla e sarà eliminata automaticamente.
    - Se hai ancora bisogno della rete, puoi mantenerla attiva emettendo transazioni su essa. Puoi trovare la tua rete piano Starter nel tuo dashboard [{{site.data.keyword.cloud_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps/){:new_window}. Se non hai transazioni regolari da emettere, segui le [istruzioni](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) per provare le applicazioni di esempio.
- **Manutenzione e upgrade**
    Gli aggiornamenti di manutenzione e di rete di piano Starter sono eseguiti in base a una pianificazione fissa. Durante il periodo di manutenzione, non puoi eseguire il provisioning di nuove reti e potresti notare dei brevi periodi di interruzione della rete.
- **Conservazione dei dati**
    Il piano Starter non garantisce la conservazione dei dati con gli upgrade delle release, compreso il passaggio da Beta a GA.
- **Considerazioni sulla migrazione **
    - La migrazione di dati da una rete piano Starter ad altri piani di adesione non è attualmente supportata. È tuttavia possibile migrare file `.bna`, chaincode e applicazioni che erano stati testati su piano Starter. Per ulteriori informazioni, vedi [Migrazione da piano Starter a piano Enterprise](get_start_starter_plan.html#migrate).

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## Migrazione da Beta a GA
{: #beta-to-ga}

Il piano Starter passa alla fase GA il 14 giugno 2018. In GA, IBM Blockchain Platform offre $500 di crediti di prova per ciascun account IBM Cloud per creare reti blockchain con l piano Starter. Per ulteriori informazioni sui crediti di prova, il piano Starter passa alla fase GA il 14 giugno 2018. In GA, IBM Blockchain Platform offre $500 di crediti di prova per ciascun account IBM Cloud per creare reti blockchain con l piano Starter. Per ulteriori informazioni sui crediti di prova, consulta la sezione *Prova del piano Starter* in [Prezzi del piano Starter](howto/pricing.html#starter-plan-pricing). Assicurati di disporre di un account IBM Cloud a pagamento, ad esempio uno di tipo **Pagamento a consumo**.
. Assicurati di disporre di un account IBM Cloud a pagamento, ad esempio uno di tipo **Pagamento a consumo**.

Le reti blockchain create con il piano Starter Beta rimangono **gratuite** finché non vengono eliminate **30 giorni** dopo la GA del piano Starter. La migrazione dei dati non è supportata dalle reti piano Starter Beta alle reti GA. **I tuoi dati nelle reti Beta andranno perduti.**  Puoi tuttavia migrare il tuo chaincode, le tue reti di business e le tue applicazioni manualmente.
- Se hai un chaincode in esecuzione nelle reti Beta, installa e istanzia il chaincode nelle reti GA. Per ulteriori informazioni, vedi [Installazione, istanziazione e aggiornamento di un chaincode](howto/install_instantiate_chaincode.html).
- Se hai distribuito una rete di business su reti Beta, distribuisci la rete di business con il file `.bna` su reti GA. Per ulteriori informazioni, consulta [Distribuzione di una rete di business su piano Starter](develop_starter.html).
- Se hai eseguito delle applicazioni a sviluppo autonomo sulle reti Beta, aggiorna gli endpoint API nelle tue applicazioni per puntare ai nodi di rete GA. Per ulteriori informazioni, vedi [Aggiunta di endpoint API di rete alla tua applicazione](v10_application.html#adding-network-api-endpoints-to-your-application).

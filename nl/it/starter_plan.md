---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informazioni su piano Starter
{: #overview}

{{site.data.keyword.blockchainfull}} Platform piano Starter è un'opzione entry-level che consente alle organizzazioni di simulare delle reti blockchain di più organizzazioni, di sviluppare rapidamente delle applicazioni e di gestire gli esempi forniti. Offre anche la stessa esperienza di IU delle altre opzioni di adesione, aiutando ad eliminare eventuali curve di apprendimento. Le reti piano Starter sono create con Hyperledger Fabric V1.1.
{:shortdesc}

**Nota**: {{site.data.keyword.blockchainfull_notm}} Platform piano Starter è un ambiente di sviluppo e test  non è adatto per i carichi di lavoro di produzione. Se ti serve un ambiente di produzione, vedi [Informazioni su piano Enterprise](enterprise_plan.html). Potresti controllare [Considerazioni su piano Starter](#considerations) prima di utilizzare piano Starter.

Registrati per la tua adesione [{{site.data.keyword.blockchainfull_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) e prova piano Starter ora! Nota: devi scegliere **Stati Uniti Sud** come regione in {{site.data.keyword.cloud_notm}} per creare delle reti blockchain con piano Starter Beta.


## Gruppi di destinatari

Se ti riconosci in una di queste situazioni, piano Starter è adatto per te per iniziare la tua esperienza con blockchain.
- **_Ulteriori informazioni su {{site.data.keyword.blockchainfull_notm}} Platform._**  
    Se sei incuriosito da {{site.data.keyword.blockchainfull_notm}} Platform o anche se non conosci ancora la blockchain, piano Starter ti offre un ottimo modo per imparare come sviluppare e gestire una vera rete blockchain. Puoi trovare i componenti in cui si articola una rete, imparare come distribuire e gestire la chaincode (nota anche come "smart contract"), come aggiungere canali (e come gestire le autorizzazioni ad essi) e tenere traccia di quando viene generato un nuovo blocco, proprio come in una rete di produzione.
- **_Vuoi agire come più organizzazioni per facilitare lo sviluppo di rete._**  
    piano Starter ti consente di agire come più organizzazioni, il che ti consente di appurare il modo in cui IBP (IBM Blockchain Platform) gestisce attività collaborative quali la creazione di canali e l'istanziazione di chaincode, nonché l'esecuzione di test di applicazioni e il richiamo di transazioni. Puoi anche invitare altre persone a collaborare a una rete piano Starter (come negli piano Enterprise).
- **_Creazione di soluzioni demo in una rete attiva._**  
    Piano Starter fornisce un potente ambiente per l'esecuzione di test di definizioni di rete (integrando un file `.bna` sviluppato utilizzando {{site.data.keyword.blockchainfull_notm}} Platform: Develop) e applicazioni blockchain. La rete blockchain pronta per l'uso consente delle rapide presentazioni ai colleghi, ai dirigenti e ai partner mediante gli strumenti operativi e di gestione forniti dal monitoraggio della rete.
- **_Sviluppo e test iterativi di applicazioni blockchain._**  
    Piano Starter ti offre un'area di gestione temporanea per sviluppare e testare continuativamente il tuo codice su una rete blockchain. Puoi sviluppare in modo iterativo il tuo codice ed eseguire la distribuzione nella tua integrazione continua e nell'architettura di distribuzione continua. Piano Starter fornisce le stesse funzionalità di rete blockchain così come gli strumenti operativi e di gestione di piano Enterprise. Quando sei pronto ad eseguire il push del tuo progetto a uno degli piano Enterprise, puoi operare nello stesso modo che in piano Starter, ma con più opportunità di far crescere la tua rete.
- **_Eseguire test dei progetti prima della produzione._**  
    piano Starter fornisce un ambiente per gli sviluppatori e i tester per passare rapidamente dal loro ambiente locale a un vero ambiente blockchain cloud.  Questo meccanismo consente agli sviluppatori e ai tester di concentrarsi sulle funzionalità e di passare facilmente da test d'unità a test funzionali. Anche i team di test di sistemi, soluzioni e prestazioni possono usare l'ambiente senza lo sforzo supplementare di configurare una rete localmente.
- **_{{site.data.keyword.blockchainfull_notm}} Platform operativo per la formazione._**  
    {{site.data.keyword.blockchainfull_notm}} Platform fornisce virtualmente la stessa interfaccia utente<!--the same user interface--> in piano Starter e negli piano Enterprise, garantendo che la tua formazione personalizzata utilizzi gli stessi flussi di lavoro che la tua organizzazione utilizzerà in un piano Enterprise.
- **_Distribuire applicazioni di esempio rapidamente utilizzando Toolchain._**  
    Piano Starter ti consente la distribuzione di applicazioni di esempio utilizzando Toolchain con solo pochi clic. Questi esempi assisteranno gli sviluppatori nella fornitura di un crescente set di esempi con codice da modificare e sviluppare ulteriormente.


## Specializzazioni piano Starter

Piano Starter consente di gestire le adesioni con l'Autorità di certificazione, eseguire l'approvazione di transazioni, fornire i servizi di ordinazione, creare canali privati, gestire i cicli di vita del chaincode e collaborare con altre persone in una rete attiva, proprio come in un piano Enterprise.

In modo specifico, piano Starter offre una rete blockchain preconfigurata che puoi sviluppare, gestire e operare tramite il monitoraggio della rete. Fornisce anche dei semplici approcci alla distribuzione di applicazioni di esempio e integra le applicazioni che sviluppi con {{site.data.keyword.blockchainfull_notm}} Platform: Develop.

- **_Rete pronta in un clic_**  
    Piano Starter ti offre una rete con un servizio ordini, Autorità di certificazione (CA, Certificate Authority), un canale predefinito e due organizzazioni (con un peer per ogni organizzazione) con un singolo clic. {{site.data.keyword.blockchainfull_notm}} Platform gestisce la creazione e la configurazione di questa rete (potrai aggiornarla dopo che sarà attiva).<!--The free trial provides you up to two organizations and two peers.-->
- **_Efficienza in termini di costi_**  
    L'opzione di adesione piano Starter fornisce molte delle stesse funzionalità blockchain delle opzioni di adesione piano Enterprise, ma a un costo più basso.  <!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->Nella fase Beta, puoi usare piano Starter gratuitamente.
- **_Simulazione di una rete con più organizzazioni_**  
    Puoi usare piano Starter per simulare la creazione di una rete con più organizzazioni. Non devi effettivamente invitare altre organizzazioni alla tua rete, ma puoi agire tu stesso come altre organizzazioni. Questo meccanismo ti consente di apprendere in che modo una nuova organizzazione può aderire alla rete e come più organizzazioni lavorano insieme nella rete e così via. Puoi passare da un'organizzazione all'altra dal monitoraggio della rete per visualizzare e gestire la rete dalla prospettiva di organizzazioni differenti.
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_API Swagger_**  
    Piano Starter presenta diverse API REST che puoi provare da un'interfaccia Swagger. Per ulteriori informazioni, vedi [Utilizzo delle API Swagger](swagger_apis.html).
- **_Applicazioni di esempio_**  
    Piano Starter si avvale del servizio Toolchain in {{site.data.keyword.cloud_notm}} e consente una distribuzione di esempio con dei semplici clic.  Dopo che hai distribuito e avviato un esempio, il chaincode e le applicazioni vengono eseguiti automaticamente per la tua rete blockchain.  Per ulteriori informazioni sulle applicazioni di esempio, vedi [Distribuzione di applicazioni di esempio](howto/prebuilt_samples.html).
- Integrazione delle applicazioni **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    Piano Starter ti consente di distribuire applicazioni {{site.data.keyword.blockchainfull_notm}} Platform: Develop nella tua rete.  Per ulteriori informazioni, vedi [Importazione di applicazioni IBP: Develop](link).

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## Considerazioni su piano Starter
{: #considerations}

Piano Starter è un punto d'ingresso a {{site.data.keyword.blockchainfull_notm}} Platform ed è pensato per scopi di sviluppo e test.  Controlla i seguenti elementi prima di utilizzare piano Starter.

- **Requisiti dell'account {{site.data.keyword.cloud_notm}} **  
    Piano Starter è gratuito per la durata della beta.  Tuttavia, dovresti eseguire l'upgrade del tuo account {{site.data.keyword.cloud_notm}} a un account a pagamento, ad esempio, un tipo **Pagamento a consumo**. Tutti i piani di appartenenza che {{site.data.keyword.blockchainfull_notm}} Platform offre richiedono un account a pagamento {{site.data.keyword.cloud_notm}}. Per eseguire l'upgrade del tuo account a un tipo Pagamento a consumo, vai a **Gestisci** > **Fatturazione e utilizzo** > **Fatturazione** nella console {{site.data.keyword.cloud_notm}} e fai clic su **Aggiungi carta di credito**.
- **Differenze rispetto al piano Enterprise**
    - [Autorità di certificazione (CA, Cerificate Authority)](glossary.html#ca) e [servizio di ordinazione](glossary.html#orderer) non sono tolleranti ai guasti perché ciascuna organizzazione ha una sola CA e una rete ha un solo [ordinante](glossary.html#orderer).
    - Il servizio ordini usa esclusivamente il [consenso](glossary.html#consensus) [SOLO](glossary.html#SOLO). Una rete piano Starter consiste in un solo [ordinante](glossary.html#orderer) che esegue il consenso per tutti i peer.
    - [HSM (Hardware Security Module)](glossary.html#hsm) non è disponibile per salvaguardare e gestire le chiavi digitali per un'elaborazione della crittografia e un'autenticazione avanzate.
- **Limitazione della risorsa di rete**  
    Piano Starter assegna 1 CPU, 2 GB RAM e 20 Gi di archiviazione hardware costantemente per ogni istanza del servizio  {{site.data.keyword.cloud_notm}}. Se aggiungi ulteriori risorse di rete alla stessa rete, le tue risorse condividono le assegnazioni hardware. Tuttavia, quando inviti un membro ad unirsi alla rete, il membro crea una nuova istanza del servizio. Pertanto, piano Starter assegna un'altra serie di 1 CPU, 2 GB RAM e 20 Gi di archiviazione hardware al nuovo membro.
- **Eliminazione rete non attiva**  
    Dopo avere creato una rete piano Starter (Beta), se non visiti la rete o emetti una transazione per 15 giorni, {{site.data.keyword.blockchainfull_notm}} Platform eliminerà la rete.
    - Se non hai più bisogno della rete, non devi fare nulla e sarà eliminata automaticamente.
    - Se hai ancora bisogno della rete, puoi mantenerla attiva emettendo transazioni su essa. Puoi trovare la tua rete piano Starter (Beta) nel tuo dashboard [{{site.data.keyword.cloud_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps/){:new_window}. Se non hai transazioni regolari da emettere, segui le [istruzioni](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) per provare le applicazioni di esempio.
- **Manutenzione e upgrade**  
    Gli aggiornamenti di manutenzione e di rete di piano Starter sono eseguiti in base a una pianificazione fissa. Durante il periodo di manutenzione, non puoi eseguire il provisioning di nuove reti e potresti notare dei brevi periodi di interruzione della rete.
- **Conservazione di dati**  
    Piano Starter non garantisce la conservazione dei dati con gli upgrade delle release, compreso il passaggio da Beta a GA.
- **Considerazioni sulla migrazione **  
    La migrazione di dati da una rete piano Starter ad altri piani di adesione non è attualmente supportata. È tuttavia possibile migrare file `.bna`, chaincode e applicazioni che erano stati testati su piano Starter. Per ulteriori informazioni, vedi [Migrazione da piano Starter a piano Enterprise](get_start_starter_plan.html#migrate).
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

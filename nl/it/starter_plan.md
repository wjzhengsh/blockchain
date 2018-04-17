---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informazioni su Starter Plan
{: #overview}

{{site.data.keyword.blockchainfull}} Platform è un'opzione entry-level che consente alle organizzazioni di simulare delle reti blockchain di più organizzazioni, di sviluppare rapidamente delle applicazioni e di gestire gli esempi forniti. Offre anche la stessa esperienza di IU delle altre opzioni di adesione, aiutando ad eliminare eventuali curve di apprendimento.
{:shortdesc}

**Nota**: {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan è un ambiente di sviluppo e test. Se ti serve un ambiente di produzione, vedi [Informazioni su Enterprise Plan](enterprise_plan.html).

Registrati per la tua adesione [{{site.data.keyword.blockchainfull_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) e prova Starter Plan ora! Nota: devi scegliere **Stati Uniti Sud** come regione in {{site.data.keyword.cloud_notm}} per creare delle reti blockchain con Starter Plan Beta.


## Gruppi di destinatari

Se ti riconosci in una di queste situazioni, Starter Plan è adatto per te per iniziare la tua esperienza con blockchain.
- **_Vuoi saperne di più su {{site.data.keyword.blockchainfull_notm}} Platform._**  
    Se sei incuriosito da {{site.data.keyword.blockchainfull_notm}} Platform o anche se non conosci ancora la blockchain, Starter Plan ti offre un ottimo modo per imparare a sviluppare e gestire una vera rete blockchain. Puoi trovare i componenti in cui si articola una rete, imparare a distribuire e gestire la chaincode (nota anche come "smart contract"), come aggiungere canali (e come gestire le autorizzazioni ad essi) e tenere traccia di quando viene generato un nuovo blocco, proprio come in una rete di produzione. 
- **_Vuoi agire come più organizzazioni per facilitare lo sviluppo di rete._**  
    Starter Plan ti consente di agire come più organizzazioni, il che ti consente di appurare il modo in cui IBP (IBM Blockchain Platform) gestisce attività collaborative quali la creazione di canali e l'istanziazione di chaincode, nonché l'esecuzione di test di applicazioni e il richiamo di transazioni. Puoi anche invitare altre persone a collaborare a una rete Starter Plan (come negli Enterprise Plan).
- **_Creazione di soluzioni demo in una rete attiva._**  
    Starter Plan fornisce un potente ambiente per l'esecuzione di test di definizioni di rete (integrando un file `.bna` sviluppato utilizzando {{site.data.keyword.blockchainfull_notm}} Platform: Develop) e applicazioni blockchain. La rete blockchain pronta per l'uso consente delle rapide presentazioni ai colleghi, ai dirigenti e ai partner mediante gli strumenti operativi e di gestione forniti dal Network Monitor.
- **_Sviluppo e test iterativi di applicazioni blockchain._**  
    Starter Plan ti offre un'area di gestione temporanea per sviluppare e testare continuativamente il tuo codice su una rete blockchain. Puoi sviluppare in modo iterativo il tuo codice ed eseguire la distribuzione nella tua integrazione continua e nell'architettura di distribuzione continua. Starter Plan fornisce le stesse funzionalità di rete blockchain così come gli strumenti operativi e di gestione di Enterprise Plan. Quando sei pronto ad eseguire il push del tuo progetto a uno degli Enterprise Plan, puoi operare nello stesso modo che in Starter Plan, ma con più opportunità di far crescere la tua rete.
- **_Eseguire test dei progetti prima della produzione._**  
    Starter Plan fornisce un ambiente per gli sviluppatori e i tester per passare rapidamente dal loro ambiente locale a un vero ambiente blockchain cloud. Questo meccanismo consente agli sviluppatori e ai tester di concentrarsi sulle funzionalità e di passare facilmente da test d'unità a test funzionali. Anche i team di test di sistemi, soluzioni e prestazioni possono usare l'ambiente senza lo sforzo supplementare di configurare una rete localmente.
- **_{{site.data.keyword.blockchainfull_notm}} Platform operativo per la formazione._**  
    {{site.data.keyword.blockchainfull_notm}} Platform fornisce virtualmente la stessa interfaccia utente<!--the same user interface--> in Starter Plan e negli Enterprise Plan, garantendo che la tua formazione personalizzata utilizzi gli stessi flussi di lavoro che la tua organizzazione utilizzerà in un Enterprise Plan.
- **_Distribuire applicazioni di esempio rapidamente utilizzando Toolchain._**  
    Starter Plan ti consente la distribuzione di applicazioni di esempio utilizzando Toolchain con solo pochi clic. Questi esempi assisteranno gli sviluppatori nella fornitura di un crescente set di esempi con codice da modificare e sviluppare ulteriormente.


## Specifiche di Starter Plan

Starter Plan consente di gestire le adesioni con l'Autorità di certificazione, eseguire l'approvazione di transazioni, fornire i servizi di ordinazione, creare canali privati, gestire i cicli di vita del chaincode e collaborare con altre persone in una rete attiva, proprio come in un Enterprise Plan.

In modo specifico, Starter Plan offre una rete blockchain preconfigurata che puoi sviluppare, gestire e operare tramite il Network Monitor. Fornisce anche dei semplici approcci alla distribuzione di applicazioni di esempio e integra le applicazioni che sviluppi con {{site.data.keyword.blockchainfull_notm}} Platform: Develop.

- **_Rete pronta in un clic_**  
    Starter Plan ti offre una rete con un servizio di ordinazione, Autorità di certificazione (CA, Certificate Authority), un canale predefinito e due organizzazioni (con un peer per ogni organizzazione) con un singolo clic. {{site.data.keyword.blockchainfull_notm}} Platform gestisce la creazione e la configurazione di questa rete (potrai aggiornarla dopo che sarà attiva).<!--The free trial provides you up to two organizations and two peers.-->
- **_Efficienza in termini di costi_**  
    L'opzione di adesione Starter Plan fornisce molte delle stesse funzionalità blockchain delle opzioni di adesione Enterprise Plan, ma a un costo più basso. <!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->Nella fase Beta, puoi usare Starter Plan gratuitamente.
- **_Simulazione di una rete con più organizzazioni_**  
    Puoi usare Starter Plan per simulare la creazione di una rete con più organizzazioni. Non devi effettivamente invitare altre organizzazioni alla tua rete, ma puoi agire tu stesso come altre organizzazioni. Questo meccanismo ti consente di apprendere in che modo una nuova organizzazione può aderire alla rete e come più organizzazioni lavorano insieme nella rete e così via. Puoi passare da un'organizzazione all'altra dal Netwok Monitor per visualizzare e gestire la rete dalla prospettiva di organizzazioni differenti.
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_API Swagger_**  
    Starter Plan presenta diverse API REST che puoi provare da un'interfaccia Swagger. Per ulteriori informazioni, vedi [Utilizzo delle API Swagger](swagger_apis.html).
- **_Applicazioni di esempio_**  
    Starter Plan si avvale del servizio Toolchain in {{site.data.keyword.cloud_notm}} e consente una distribuzione di esempio con dei semplici clic. Dopo che hai distribuito e avviato un esempio, i chaincode e le applicazioni vengono eseguiti automaticamente per la tua rete blockchain. Per ulteriori informazioni sulle applicazioni di esempio, vedi [Distribuzione di applicazioni di esempio](howto/prebuilt_samples.html).
- Integrazione delle applicazioni **_{{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    Starter Plan ti consente di distribuire applicazioni {{site.data.keyword.blockchainfull_notm}} Platform: Develop nella tua rete. Per ulteriori informazioni, vedi [Importazione di applicazioni IBP: Develop](link).

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

## Informazioni necessarie

- Starter Plan è un punto d'ingresso a {{site.data.keyword.blockchainfull_notm}} Platform ed è diverso da Enterprise Plan nei seguenti modi:
    - [Autorità di certificazione (CA, Cerificate Authority)](glossary.html#ca) e [servizio di ordinazione](glossary.html#orderer) non sono tolleranti ai guasti perché ciascuna organizzazione ha una sola CA e una rete ha un solo [ordinante](glossary.html#orderer).
    - Il servizio di ordinazione usa esclusivamente il [consenso](glossary.html#consensus) [SOLO](glossary.html#SOLO). Una rete Starter Plan consiste in un solo [ordinante](glossary.html#orderer) che esegue il consenso per tutti i peer.
    - [HSM (Hardware Security Module)](glossary.html#hsm) non è disponibile per salvaguardare e gestire le chiavi digitali per un'elaborazione della crittografia e un'autenticazione avanzate.
- Gli aggiornamenti di manutenzione e di rete di Starter Plan sono eseguiti in base a una pianificazione fissa. Durante il periodo di manutenzione, non puoi eseguire il provisioning di nuove reti e potresti notare dei brevi periodi di interruzione della rete.
- Starter Plan non garantisce la conservazione dei dati con gli upgrade delle release, compreso il passaggio da Beta a GA.
- La migrazione di dati da una rete Starter Plan ad altre opzioni di adesione non è attualmente supportata. È tuttavia possibile migrare file `.bna`, chaincode e applicazioni che erano stati testati su Starter Plan. Per ulteriori informazioni, vedi [Migrazione da Starter Plan a Enterprise Plan](get_start_starter_plan.html#migrate).

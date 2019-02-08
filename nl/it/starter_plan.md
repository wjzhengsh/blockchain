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

# Informazioni sul piano Starter
{: #overview}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform piano Starter è un'opzione entry-level che consente alle organizzazioni di simulare delle reti blockchain con più organizzazioni, sviluppare rapidamente delle applicazioni e lavorare con reti di business e smart contract di esempio. Offre anche la stessa esperienza di IU delle altre opzioni di adesione, aiutando ad eliminare eventuali curve di apprendimento. Le nuove reti piano Starter create dopo il 4 ottobre 2018 sono sviluppate su Hyperledger Fabric V1.2.1. Le reti piano Starter meno recenti rimangono al livello Fabric V1.1.0.
{:shortdesc}

**Note:**
- {{site.data.keyword.blockchainfull_notm}} Platform piano Starter è un ambiente di sviluppo e test  non è adatto per i carichi di lavoro di produzione. Se ti serve un ambiente di produzione, vedi [Informazioni sul piano Enterprise](/docs/services/blockchain/enterprise_plan.html). Potresti controllare [Considerazioni su piano Starter](#considerations) prima di utilizzare piano Starter.
- {{site.data.keyword.blockchainfull_notm}} Platform è un servizio di piattaforma su {{site.data.keyword.cloud_notm}} e tutte le offerte di adesione seguono i [termini dei servizi {{site.data.keyword.cloud_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} - Termini dei servizi") sugli SLA (service level agreement). Le reti piano Starter vengono fornite in **più ambienti** in data center geograficamente separati.

## Cosa offre il piano Starter

- **_Rete attiva con un solo clic_**
    Il piano Starter ti offre una rete blockchain attiva con un singolo clic. Ogni rete viene fornita con una CA, un ordinante, due organizzazioni (con un peer per ogni organizzazione) e un canale predefinito su cui eseguire transazioni e distribuire il chaincode. {{site.data.keyword.blockchainfull_notm}} Platform gestisce la creazione e la configurazione di questa rete (potrai aggiornarla dopo che sarà attiva), consentendoti di concentrarti sullo sviluppo. Le reti piano Starter sono sviluppate su Fabric V1.2 e utilizzano CouchDB come database dello stato. <!--The free trial provides you up to two organizations and two peers.-->
- **_Efficienza in termini di costi_**
    L'opzione di adesione piano Starter fornisce molte delle stesse funzionalità blockchain delle opzioni di adesione piano Enterprise, ma a un costo più basso. {{site.data.keyword.blockchainfull_notm}} Platform offre $500 di crediti cloud ai nuovi utenti del piano Starter. Questi crediti ti consentono di eseguire il provisioning di una rete blockchain con risorse di rete di base gratuitamente per un mese. Per ulteriori dettagli, vedi [Considerazioni su piano Starter](#starter-plan-considerations).
- **_Simulazione di una rete con più organizzazioni_**
    Puoi usare il piano Starter per simulare la creazione di una rete con più organizzazioni. Non devi effettivamente invitare altre organizzazioni alla tua rete, ma puoi agire tu stesso come altre organizzazioni. Questo meccanismo ti consente di apprendere in che modo una nuova organizzazione può aderire alla rete e come più organizzazioni lavorano insieme nella rete e così via. Puoi passare da un'organizzazione all'altra dal Monitoraggio della rete per visualizzare e gestire la rete dalla prospettiva di organizzazioni differenti.
    **Nota**: se aggiungi ulteriori risorse, potresti incorrere in costi extra rispetto alle risorse di base di prova gratuite che consistono in due organizzazioni e due peer.
<!-- - **_Easy to deploy sample applications_**
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html). -->
- **_Integrazione delle applicazioni {{site.data.keyword.blockchainfull_notm}} Platform: Develop_**
    Il piano Starter ti consente di distribuire reti di business che sono state sviluppate su [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login). Per ulteriori informazioni, consulta [Distribuzione di una rete di business su piano Starter](/docs/services/blockchain/develop_starter.html).

## Il piano Starter è adatto a te?

Se ti riconosci in una di queste situazioni, piano Starter è adatto per te per iniziare la tua esperienza con blockchain.
- **_Vuoi saperne di più su {{site.data.keyword.blockchainfull_notm}} Platform._**
    Se sei incuriosito da {{site.data.keyword.blockchainfull_notm}} Platform o anche se non conosci ancora la blockchain, piano Starter ti offre un ottimo modo per imparare come sviluppare e gestire una vera rete blockchain. Puoi trovare i componenti in cui si articola una rete, gestire l'adesione, imparare come distribuire e gestire il chaincode (noto anche come "smart contract"), come aggiungere canali (e come gestire le autorizzazioni ad essi) e tenere traccia di quando viene generato un nuovo blocco, proprio come in una rete di produzione.
- **_Creazione di soluzioni demo su una rete attiva._**
    Il piano Starter fornisce un potente ambiente per dimostrare le applicazioni blockchain. La rete blockchain pronta per l'uso consente delle rapide presentazioni ai colleghi, ai dirigenti e ai partner mediante gli strumenti operativi e di gestione forniti dal Monitoraggio della rete.
- **_Andare oltre lo sviluppo di una singola organizzazione._**
    Il piano Starter ti permette di agire come più organizzazioni, il che ti consente di vedere in che modo {{site.data.keyword.blockchainfull_notm}} Platform gestisce attività collaborative quali la creazione di canali e l'istanziazione di chaincode, nonché l'esecuzione di test di applicazioni e il richiamo di transazioni. Puoi anche invitare altre persone a collaborare a una rete piano Starter (come negli piano Enterprise). Questo ti aiuta a creare in un ambiente più realistico, con più parti che approvano il chaincode e le transazioni.
- **_Sviluppo e test iterativi di applicazioni blockchain._**
    Piano Starter ti offre un'area di gestione temporanea per sviluppare e testare continuativamente il tuo codice su una rete blockchain. Il passaggio al cloud consente agli sviluppatori e ai tester di concentrarsi sulle funzionalità e di passare facilmente da test d'unità a test funzionali. Piano Starter fornisce le stesse funzionalità di rete blockchain così come gli strumenti operativi e di gestione di piano Enterprise. Quando sei pronto ad eseguire il push del tuo progetto a uno dei piani Enterprise, puoi operare nello stesso modo che in piano Starter, ma con più opportunità di far crescere la tua rete.


## Considerazioni su piano Starter
{: #considerations}

Piano Starter è un punto d'ingresso a {{site.data.keyword.blockchainfull_notm}} Platform ed è pensato per scopi di sviluppo e test.  Controlla i seguenti elementi prima di utilizzare piano Starter.

- **Requisito dell'account {{site.data.keyword.cloud_notm}}**
    Devi disporre di un account {{site.data.keyword.cloud_notm}} a pagamento, ad esempio uno di tipo **Pagamento a consumo**. Tutti i piani di adesione che {{site.data.keyword.blockchainfull_notm}} Platform offre richiedono un account a pagamento {{site.data.keyword.cloud_notm}}. Per eseguire l'upgrade del tuo account a un tipo Pagamento a consumo, vai a **Gestisci** > **Fatturazione e utilizzo** > **Fatturazione** nella console {{site.data.keyword.cloud_notm}} e fai clic su **Aggiungi carta di credito**.
- **Crediti cloud per {{site.data.keyword.blockchainfull_notm}} Platform**
    {{site.data.keyword.blockchainfull_notm}} Platform offre $500 di crediti cloud ai nuovi utenti del piano Starter. Questi crediti aiutano gli utenti a iniziare coprendo il costo di una configurazione di rete predefinita per un mese.
    - I nuovi utenti devono registrarsi per i crediti cloud. Non vengono applicati automaticamente. [Registrati ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "Registrati") per richiedere i crediti se non l'hai già fatto. Attendi 24 ore per assicurarti che i crediti siano nel tuo [account ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "account") prima di accedere al piano Starter. In caso contrario, potresti ricevere un addebito prima dell'applicazione dei crediti.
    - I crediti cloud si applicano a tutti i servizi {{site.data.keyword.cloud_notm}}. Possono essere consumati dai prodotti diversi da {{site.data.keyword.blockchainfull_notm}} Platform che utilizzi su {{site.data.keyword.cloud_notm}}.
    - L'offerta fornisce crediti a vita. Puoi conservare i tuoi crediti fino a quando mantieni il tuo account {{site.data.keyword.cloud_notm}}. Non devi usarli nel primo mese.
    Scopri di più sull'utilizzo dei crediti di prova utilizzando la [guida per la determinazione dei prezzi](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing).
- **Differenze rispetto al piano Enterprise**
    - [Autorità di certificazione (CA, Cerificate Authority)](/docs/services/blockchain/glossary.html#ca) e [servizio ordini](/docs/services/blockchain/glossary.html#orderer) non sono tolleranti ai guasti perché ciascuna organizzazione ha una sola CA e una rete ha un solo [ordinante](/docs/services/blockchain/glossary.html#orderer).
    - Il servizio ordini usa esclusivamente il [consenso](/docs/services/blockchain/glossary.html#consensus) [SOLO](/docs/services/blockchain/glossary.html#solo). Una rete Starter Plan consiste in un solo [ordinante](/docs/services/blockchain/glossary.html#orderer) che esegue il consenso per tutti i peer.
    - [HSM (Hardware Security Module)](/docs/services/blockchain/glossary.html#hsm) non è disponibile per salvaguardare e gestire le chiavi digitali per un'elaborazione della crittografia e un'autenticazione avanzate.
- **Versioni e upgrade del piano Starter**
    - Le nuove reti piano Starter create dopo il 4 ottobre 2018 sono sviluppate su Hyperledger Fabric V1.2.1. Le reti piano Starter meno recenti rimangono al livello Fabric V1.1.0.
    - I nuovi peer aggiunti a reti piano Starter meno recenti saranno basati su Fabric v1.2.1. Le prestazioni della tua rete non ne risentono grazie alla compatibilità con le versioni precedenti.
    - Puoi utilizzare la funzione di [dati privati ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "dati privati") di Hyperledger Fabric v1.2 nelle nuove reti piano Starter. Per ulteriori informazioni su come utilizzare i dati privati con il tuo chaincode, vedi [Dati privati](/docs/services/blockchain/howto/develop_chaincode.html#private-data).
    - Hai l'opzione di utilizzare le impostazioni di [configurazione del canale![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "configurazione del canale") più avanzate e gli [Elenchi di controllo dell'accesso (ACL, Access Control List)![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/access_control.html "Elenchi di controllo dell'accesso (ACL, Access Control List)") quando crei o aggiorni un canale.
    - La funzione di [rilevamento di servizi![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "rilevamento di servizi") di Hyperledger Fabric v1.2 non è supportata su {{site.data.keyword.blockchainfull_notm}}.
    - Se [reimposti](/docs/services/blockchain/v10_dashboard.html#reset-network) una rete piano Starter meno recente che è a Fabric V1.1.0, la tua nuova rete sarà al livello Fabric V1.2. Se reimposti la tua rete, devi installare il tuo chaincode o i file .bna sulla nuova rete e invitare nuovamente i membri della tua vecchia rete.
- **Limitazione delle risorse di rete**
    Il piano Starter assegna 1 CPU e 4 Gi di RAM per ciascun peer e  20 Gi di archiviazione per ogni istanza del servizio {{site.data.keyword.cloud_notm}}, compresi i peer. I contenitori chaincode e i blocchi del libro mastro sono i componenti di rete più ricchi di risorse. Gli utenti che hanno molti peer sulla loro rete, che generano molti blocchi o che utilizzano file chaincode di grandi dimensioni, potrebbero riscontrare l'effetto delle limitazioni di risorse sulle prestazioni. Puoi visualizzare l'utilizzo di archiviazione delle tue reti nella [schermata "Panoramica" del tuo Monitoraggio della rete](/docs/services/blockchain/v10_dashboard.html#storage).
- **Manutenzione e upgrade**
    Gli aggiornamenti di manutenzione e di rete di piano Starter sono eseguiti in base a una pianificazione fissa. Durante il periodo di manutenzione, non puoi eseguire il provisioning di nuove reti e potresti notare dei brevi periodi di interruzione della rete.
- **Conservazione dei dati**
    Il piano Starter non garantisce la conservazione dei dati con gli upgrade delle release.
- **Considerazioni sulla migrazione**
    La migrazione dei dati da una rete piano Starter ad altri piani di adesione non è attualmente supportata. È tuttavia possibile migrare file `.bna`, chaincode e applicazioni che erano stati testati su piano Starter. Per ulteriori informazioni, vedi [Migrazione da piano Starter a piano Enterprise](/docs/services/blockchain/howto/migrate_sp_ep.html).


<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](/docs/services/blockchain/develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](/docs/services/blockchain/v10_application.html#adding-network-api-endpoints-to-your-application).
-->

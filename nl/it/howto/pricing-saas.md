---

copyright:
  years: 2019
lastupdated: "2019-05-31"

keywords: pricing model, hourly, per hour, VPC, CPU, vCPU, virtual core, cost, scalability, estimation, optimize your cost

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Prezzi
{: #ibp-saas-pricing}

Questa guida ti aiuta a comprendere il modello di prezzi per {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} e quanto pagherai quando sviluppi e accresci la tua rete blockchain aggiungendo componenti peer, ordinanti e autorità di certificazione basati su Hyperledger Fabric v1.4.1.
{:shortdesc}

_Questo modello di prezzi è solo per {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}. Se stai utilizzando il piano Starter o Enterprise e hai delle domande sui prezzi, vedi i [prezzi](/docs/services/blockchain?topic=blockchain-ibp-pricing) del piano Starter o Enterprise._

{{site.data.keyword.blockchainfull_notm}} Platform introduce un nuovo modello di prezzi orario basato sugli utilizzi del VPC (virtual processor core). Questo modello semplificato si basa sulla quantità di CPU (o VPC) che i tuoi nodi {{site.data.keyword.blockchainfull_notm}} Platform utilizzano su base oraria, a una tariffa forfettaria di **$0,29 USD/VPC-ora**.

Un VPC è un'unità di misura utilizzato per determinare il costo di licenza dei prodotti {{site.data.keyword.IBM_notm}}. Si basa sul numero di core virtuali (vCPU) disponibili per il prodotto. Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico. Per scopi di stima dei costi di {{site.data.keyword.blockchainfull_notm}} Platform, **1 VPC = 1 CPU = 1 vCPU = 1 Core**.
{:note}

Per una stima dei costi totale, ricorda che la tua rete blockchain è formata da un cluster Kubernetes {{site.data.keyword.cloud_notm}} che contiene i componenti di {{site.data.keyword.blockchainfull_notm}} Platform e utilizza l'archiviazione di tua scelta. Il tuo cluster Kubernetes {{site.data.keyword.cloud_notm}} e l'archiviazione che scegli comportano addebiti separati. Non ti sarà effettuato un addebito per il cluster su cui è in esecuzione l'istanza di Operational Tooling, nota anche come console. Per un'illustrazione, vedi l'argomento [Riferimento per l'architettura](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview-architecture). Vengono di seguito descritti ulteriori dettagli su come calcolare gli addebiti.

Gli sviluppatori possono iniziare con il nostro [extension for VS Code ![Icona link esterno](../images/external_link.svg "Icona link esterno") gratuito](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform VS Code Extension"). Utilizza questo ambiente di sviluppo integrato per scrivere, verificare, eseguire il debug e impacchettare gli smart contract in locale e per la distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform, nonché per scrivere le applicazioni client. Inizia da zero o accedi alle esercitazioni e agli esempi per conoscere le basi della blockchain.

## Vantaggi con il nuovo modello di prezzi
{: #ibp-saas-pricing-benefits}

- **Nessuna quota di appartenenza**: nessuna quota di appartenenza significa che puoi investire direttamente nei tuoi componenti blockchain.
- **Chiarezza sulla stima**: un modello di prezzi orario semplice rende la stima dei costi chiara e facile utilizzando lo strumento di stima dei costi disponibile nel dashboard {{site.data.keyword.cloud_notm}}.
- **Nessuna quota minima richiesta**: paghi solo per ciò che utilizzi, non è richiesto alcun pacchetto orario VPC minimo, che rende molto costoso iniziare.
- **Scalabilità delle risorse**: hai l'opzione di aumentare le tue risorse durante i periodi di utilizzo di picco o diminuirle a una frazione minima della capacità quando non sono necessarie per minimizzare le spese.  

In sintesi, queste funzioni rimuovono la complessità di contabilizzare i limiti dell'appartenenza o di acquistare delle risorse in base ai tuoi bisogni.

## Elementi chiave del costo
{: #ibp-saas-pricing-elements}

Poiché la tua rete blockchain è formata da un cluster Kubernetes {{site.data.keyword.cloud_notm}} che contiene i componenti di {{site.data.keyword.blockchainfull_notm}} Platform e utilizza l'archiviazione di tua scelta, ognuno dei seguenti elementi forma il tuo costo totale:

- **Tariffa forfettaria per {{site.data.keyword.blockchainfull_notm}} Platform** di $0,29 USD/VPC-ora.
- **Prezzi a livelli del cluster {{site.data.keyword.cloud_notm}} Kubernetes Service** visibili in {{site.data.keyword.cloud_notm}} quando esegui il provisioning del tuo cluster a pagamento. Sono inclusi gli addebiti per le tue risorse, ossia, CPU e memoria. I {{site.data.keyword.cloud_notm}} Kubernetes Service sono prezzati in base a un modello a livelli basato sul numero di ore di utilizzo al mese. Pertanto, quando esamini i piani dei prezzi, considera che un utilizzo di 24 ore al giorno per 7 giorni alla settimana equivale a 720 ore al mese. Fai riferimento alla tabella sulla [pagina del catalogo Kubernetes Service ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/kubernetes/catalog/cluster "Kubernetes Service") per ulteriori dettagli sui prezzi del cluster.
- Scegli il piano di **archiviazione** adatto ai tuoi bisogni. Vedi l'argomento [Descrizione dei principi di base dell'archiviazione Kubernetes](/docs/containers?topic=containers-kube_concepts#kube_concepts) per ulteriori informazioni sulle tue opzioni della classe di archiviazione e su quanto [costano ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/cloud/file-storage/pricing). I nodi {{site.data.keyword.blockchainfull_notm}} Platform utilizzano la classe di archiviazione predefinita per il cluster. Quando esegui il provisioning di un cluster Kubernetes in {{site.data.keyword.cloud_notm}}, viene preconfigurato con l'[archiviazione file al livello Bronze](/docs/containers?topic=containers-file_storage#file_predefined_storageclass) come il plugin di archiviazione persistente.

## Esempi di prezzi
{: #ibp-saas-pricing-scenarios}

La seguente tabella fornisce due esempi di prezzi con [assegnazioni di risorse predefinite]( #ibp-saas-pricing-default) a meno che non sia diversamente indicato.
- Lo scenario **Test network** è adatto per iniziare ad utilizzare e verificare gli smart contract.
- Lo scenario **Join a production network** include due peer ed è consigliato per l'elevata disponibilità e un'autorità di certificazione (CA) necessaria per l'appartenenza all'organizzazione.
   - Questi peer possono essere uniti a una rete {{site.data.keyword.blockchainfull_notm}} Platform di produzione ospitata altrove.
   - I nodi possono sempre essere ridotti a uno stato di utilizzo minimo (0.001 CPU) quando non sono utilizzati per [ridurre i costi](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-reallocate-resources).
   - Poiché questo scenario è pensato per un ambiente di **produzione**:
     - Le risorse di calcolo predefinite sono state raddoppiate per fornire una maggiore capacità.
     - La classe di archiviazione [Silver ![Icona link esterno](../images/external_link.svg "Icona link esterno")](/docs/containers?topic=containers-file_storage#file_silver) viene scelta per prestazioni più veloci.

| Opzioni di prezzo** (1 VPC = 1 CPU)| **Test Network** | **Join a Production Network** |
|-|------------|-----------------------------|
| **Assegnazione CPU** |  1,85 CPU <br> Include: <br> - 1 peer <br> - 2 CA <br> - 1 ordinante| 4,9 CPU <br> Include: <br> - 2 peer (per l'HA) <br> **(2x calcolo predefinito)** <br>- 1 CA <br>  |
| **Costo orario:{{site.data.keyword.blockchainfull_notm}} Platform** | $0,54 USD <br> (1,85 CPU x $0,29 USD/VPC-ora) | $1,42 USD <br> (4,9 CPU x $0,29 USD/VPC-ora) |
| **Costo orario: cluster Kubernetes {{site.data.keyword.cloud_notm}}**    | $0,12 USD <br> (Calcolo: 2 x 4 livelli) <br> (Assegnazione IP: $16 USD/mese) | $0,46 USD <br> (Calcolo: 8 x 32 livelli) <br> (Assegnazione IP: $16 USD/mese) |
| **Costo orario: archiviazione** | $0,07 USD <br> 340GB  <br> [Bronze ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/cloud/file-storage/pricing) <br>  2 IOPS/GB | $0,13 USD <br> 420GB <br> [Silver ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/cloud/file-storage/pricing) <br> 4 IOPS/GB  |
| **Costo orario totale** | **$0,73 USD** | **$2,01 USD**| |
** Visualizza un'anteprima di {{site.data.keyword.blockchainfull_notm}} Platform gratuitamente per 30 giorni quando colleghi la tua istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform a un cluster gratuito {{site.data.keyword.cloud_notm}} Kubernetes.  Le prestazioni saranno limitate da velocità effettiva, archiviazione e funzionalità. {{site.data.keyword.cloud_notm}} eliminerà il tuo cluster Kubernetes dopo 30 giorni e non puoi migrare i nodi o i dati da un cluster gratuito a uno a pagamento.   

I tuoi costi effettivi varieranno in base a diversi fattori come ad esempio la velocità di transazione, il numero di canali che richiedi, la dimensione del payload sulle transazioni e il numero massimo di transazioni simultanee.
{:note}

Non esiste limite al numero di istanze del servizio di cui puoi eseguire il provisioning e associare a un solo cluster Kubernetes, ma devi garantire che siano disponibili delle risorse adeguate monitorando l'utilizzo di CPU, memoria e archiviazione per evitare un'interruzione del servizio. I nodi {{site.data.keyword.blockchainfull_notm}} Platform non devono essere nel proprio cluster. Puoi avere altri servizi {{site.data.keyword.cloud_notm}} in esecuzione nel tuo cluster in cui vengono eseguiti i tuoi componenti blockchain, ma devi nuovamente garantire di avere risorse di calcolo e archiviazione adeguate per affrontare tutti i requisiti di tutte le istanze del servizio.

## Assegnazioni di risorse predefinite
{: #ibp-saas-pricing-default}

I valori nella seguente tabella sono utili per stimare il costo orario della tua rete personalizzata in base a CPU, memoria e archiviazione.

| **Componente** (tutti i contenitori) | CPU  | Memoria (GB) | Archiviazione (GB) |
|--------------------------------|---------------|-----------------------|------------------------|
| **Peer**                       |  1,2          | 2,4                   | 200 (include 100GB per peer e 100GB per CouchDB)|
| **CA**                         | 0.1            | 0.2                    | 20                     |
| **Ordinante**                    | 0.45           | 0.9                    | 100                    |

## Fatturazione
{: #ibp-saas-pricing-billing}

Le tue informazioni su fatturazione e utilizzo per il tuo account **Pagamento a consumo** sono disponibili nel dashboard {{site.data.keyword.cloud_notm}} sul tuo tile [utilizzo](https://cloud.ibm.com/billing/usage). Un servizio di misurazione acquisisce delle istantanee orarie del tuo utilizzo VPC {{site.data.keyword.blockchainfull_notm}} Platform totale in modo che la quantità di utilizzo cumulativa mensile venga rispecchiata nel tile **Utilizzo**.

Quando crei un nuovo nodo, ci vorrà fino a un'ora prima che l'utilizzo VPC venga aggiornato nel tile **Utilizzo** del dashboard {{site.data.keyword.cloud_notm}}.
{:note}

Vai a **Gestione** nella parte superiore del tuo dashboard {{site.data.keyword.cloud_notm}}, fai clic su **Fatturazione e utilizzo** e poi su **Utilizzo** nel menu di sinistra. Il grafico a torta nella sezione secondaria **Servizi** ti fornisce una suddivisione del tuo costo totale per i tipi di offerte del servizio che hai utilizzato questo mese. Utilizza questo grafico per scoprire come {{site.data.keyword.blockchainfull_notm}} Platform, il servizio Kubernetes e l'archiviazione contribuiscono al tuo costo totale.

<!--
![Usage Tile](../images/pricing1.png "Usage Tile")  
*Figure 1. View your Usage on the dashboard*-->

Quando scorri verso il basso, puoi visualizzare una suddivisione simile per **Tipo** e **Costo** in una vista elenco. Puoi trovare "Kubernetes Service", "Block Storage per VPC" o "File Storage per VPC" e "{{site.data.keyword.blockchainfull_notm}} Platform" tra gli altri. Fai clic su **"visualizza piani"** accanto ad ognuno di questi elementi per scoprire la suddivisione del tuo costo per metrica. Ad esempio, `VIRTUAL_PROCESSOR_CORE_HOURS` determinerà il numero totale di ore in cui sono stati utilizzati i VPC e quanto costano. Utilizzalo per scoprire come ti stanno venendo effettuai gli addebiti in base a varie metriche sui prezzi.

<!--
![View Plans](../images/pricing2.png "View Plans")  
*Figure 2. Find out how much cost you're incurring on Blockchain Service, Storage and more*

![Breakdown by Metrics](../images/pricing3.png "Breakdown by Metrics")  
*Figure 3. Track how many VPC hours you're utilizing, and more*
-->

## Ottimizzazione del costo dei tuoi nodi
{: #ibp-saas-pricing-shutdown}

Uno dei vantaggi chiave del modello di prezzi {{site.data.keyword.blockchainfull_notm}} Platform è la capacità di ridurre o eliminare delle risorse quando non sono necessarie.

- **Passa i tuoi nodi allo stato di utilizzo minimo**  
  La CPU sui nodi individuali può essere ridotta a 0,001 CPU per minimizzare del tutto gli addebiti. Con queste azioni si rende il nodo non funzionale. Quando in seguito sono necessarie delle risorse di calcolo, puoi utilizzare l'opzione di riassegnazione nella console {{site.data.keyword.blockchainfull_notm}} Platform per aumentarle finché necessario. Per ulteriori informazioni su come possono essere riassegnate le risorse, vedi [Riallocazione di risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-reallocate-resources).

- **Elimina il peer non utilizzato e distribuiscine uno nuovo quando necessario**  
  Poiché il libro mastro viene archiviato nell'ordinante, quando distribuisci un nuovo peer e unisci un canale, il peer riceve una copia del libro mastro distribuito. Lo svantaggio di questo approccio è che devi generare dei nuovi certificati e unire il peer nuovamente ai canali.

  Non ti consigliamo di eliminare mai un nodo della CA perché i dati su di esso non possono venire mai ripristinati. Allo stesso modo, se hai solo un nodo ordinante, non dovresti mai eliminarlo.  
  {:important}

- **Monitora e modifica la tua assegnazione di risorse in base ai tuoi bisogni**  
  Quando monitori il tuo utilizzo delle risorse nel tempo, puoi decidere di diminuire le risorse assegnate a un nodo continuando a garantire delle prestazioni adeguate. Quando segui le istruzioni per [riassegnare le risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-reallocate-resources) nella console, gli effetti sul VPC totale per il nodo vengono aggiornati e possono essere utilizzati per stimare i costi mensili revisionati.

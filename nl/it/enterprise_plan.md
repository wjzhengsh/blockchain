---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informazioni su piano Enterprise
{: #enterprise-plan-about}

{{site.data.keyword.blockchainfull}} Platform piano Enterprise è un'offerta pronta per la produzione per le organizzazioni che desiderano creare o aderire a una rete blockchain per attività aziendali reali. Questo piano fornisce l'infrastruttura chiave insieme agli strumenti e al supporto per avviare facilmente una rete blockchain pronta per la produzione e altamente sicura. In data 15 maggio 2018 viene eseguito l'upgrade del piano Enterprise da Hyperledger Fabric V1.0 alla V1.1. Tutte le reti create dopo il 15 maggio 2018 solo al livello Fabric V1.1. Tuttavia, le reti che sono state creata precedentemente rimarranno al livello V1.0.
{:shortdesc}

**Il piano Enterprise** è un ambiente di produzione che offre elevati livelli di sicurezza e supporto. La distribuzione della tua rete sul piano Enterprise ti consente di sfruttare le seguenti funzioni:

* Un ambiente ad altissima sicurezza con molte funzioni di sicurezza hardware, firmware e software.
* Un'architettura consolidata con scalabilità, resilienza e disponibilità.
* Ottimizzato per le prestazioni e in esecuzione sul calcolo Linux più veloce del mondo.

La gestione della tua rete su {{site.data.keyword.blockchainfull_notm}} Platform include strumenti e funzionalità per semplificare le attività amministrative:

* Dashboard per il monitoraggio e la gestione delle risorse sulla rete.
* Upgrade senza problemi di tutto lo stack di codice.
* Supporto tecnico 24/7 integrato nel portale.
* Stack di sicurezza consolidato senza accesso privilegiato, resistenza a malware e manomissioni, crittografia al 100% e molte altre funzioni per le reti con dati sensibili nei settori regolamentati.
* Backup offsite delle reti Enterprise eseguito ogni 24 ore. In casi di emergenza, queste reti possono essere ripristinate sullo stesso sito o su un sito alternativo.

Il piano Enterprise è un servizio di piattaforma su {{site.data.keyword.cloud_notm}} e segue i [termini dei servizi {{site.data.keyword.cloud_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} - Termini dei servizi") negli SLA (service level agreement). Nota che il provisioning delle reti del piano Enterprise viene eseguito in **più ambienti** in data center geograficamente separati.

**Note:**
- {{site.data.keyword.blockchainfull_notm}} Platform piano Enterprise fornisce un ambiente di produzione. Se hai bisogno di un ambiente di sviluppo e test, vedi [Informazioni su piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform è un servizio di piattaforma su {{site.data.keyword.cloud_notm}} e tutte le offerte di adesione rispettano i termini dei servizi [{{site.data.keyword.cloud_notm}} termini dei servizi ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}}") negli SLA (service level agreement). Le reti piano Enterprise vengono fornite in **più ambienti** in data center geograficamente separati.

Per i membri che inizieranno la rete, IBM fornisce un'interfaccia grafica utente (GUI) per assistere passo dopo passo l'iniziatore della rete nelle procedure chiave per configurare la rete e fornirla. Ciò include l'invito di altri membri e l'impostazione delle regole di governance. Per ulteriori informazioni, vedi [Governance della rete piano Enterprise](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan). Dopo che la rete è stata distribuita, è disponibile un'interfaccia grafica utente (GUI), il Monitoraggio della rete, per monitorare lo stato e l'attività della rete, gestire le attività di rete chiave tra cui le nuove distribuzioni, l'aggiunta o la rimozione di membri, il ciclo di vita del chaincode e la gestione dei canali e per cercare assistenza tecnica. Per ulteriori informazioni, consulta [Utilizzo del Monitoraggio della rete](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

Registrati ora per la tua [adesione a {{site.data.keyword.blockchainfull_notm}}![Icona link esterno](images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod).

{{site.data.keyword.blockchainfull_notm}} Platform è sviluppato con componenti Hyperledger Fabric chiave, che includono un'Autorità di certificazione (CA) e almeno 1 peer (massimo 6).  Piano Enterprise fornisce anche un servizio ordini Kafka con tolleranza di errori anomali per i membri della rete.

La CA Fabric è l'Autorità di certificazione fornita con piano Enterprise. Sono fornite due CA intermedie per membro, che concedono l'adesione alla rete. Utilizzando la CA, il membro può anche fornire i certificati di adesione agli utenti della rete.

È importante comprendere che l'accodamento di una transazione al libro mastro si articola in tre fasi:
1. Simulazione e approvazione della transazione (peer)
2. Ordine (servizio ordini)
3. Convalida e commit (peer)

I peer Fabric appartenenti ai membri sono l'interfaccia o il gateway per le applicazioni per eseguire il chaincode, fornendo la logica di business per eseguire le transazioni sul libro mastro. Tutte le transazioni devono essere approvate. Gli altri membri della rete concedono questa approvazione. Dopo l'approvazione, le transazioni vengono inviate a un servizio ordini fornito da IBM.

Oltre ai componenti blockchain principali, l'opzione di adesione Enterprise fornisce un'infrastruttura con memorizzazione di dati e comunicazioni protetti (TLS) e un'elevata disponibilità.  Mentre le reti Fabric condividono queste risorse d'infrastruttura, viene fornito un isolamento per i nodi componente Fabric in una rete e ciascun nodo viene eseguito in un contenitore Docker sicuro che protegge l'ambiente di esecuzione.

Il solo aspetto che deve essere determinato è la dimensione dei peer richiesta dalla rete. Questa decisione è basata sul numero di canali richiesti, più il carico di lavoro per canale, l'utilizzo della memoria e lo spazio su disco (memorizzazione).

Dovresti utilizzare piano Enterprise per delle distribuzioni più stabili a livello di produzione o quasi di produzione. Per finalità di test, usa [piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about) o [installa le immagini Docker in locale](http://hyperledger-fabric.readthedocs.io/en/release-1.1/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Prezzi
{: #enterprise-plan-about-pricing}

Per utilizzare piano Enterprise, i membri della rete devono pagare $1.000 al mese come costo di adesione e altri $1.000 al mese per ciascuno dei loro peer nella rete.  I costi mensili vengono addebitati ogni giorno su base proporzionale.  Ad esempio, un membro (costo di adesione associato di $1.000) di due peer (costo per ogni peer di $1.000 X 2 peer) deve pagare $3.000 ogni mese.  Se il mese ha 30 giorni, il membro paga $ 100 ($3.000/30) ogni giorno.  Nota: se hai bisogno di un'elevata disponibilità (HA, high availability), devi raddoppiare il tuo numero di peer richiesti per fornire le funzionalità HA.

I membri della rete possono pagare la loro fattura con i loro account {{site.data.keyword.cloud_notm}} che contengono lo spazio per creare l'istanza di rete. In alternativa, un membro della rete può coprire la fattura per tutti i membri nella rete. Per ulteriori informazioni su come pagare per le reti blockchain, vedi [Pagamento per la rete](/docs/services/blockchain/howto/paying_mode.html#paying-mode).

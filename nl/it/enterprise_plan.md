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

# Informazioni su Enterprise Plan

{{site.data.keyword.blockchainfull}} Platform Enterprise Plan è un'offerta pronta per la produzione per le organizzazioni che desiderano creare o aderire a una rete blockchain per attività aziendali reali. Questo piano fornisce l'infrastruttura chiave insieme agli strumenti e al supporto per avviare facilmente una rete blockchain pronta per la produzione e altamente sicura.

**Nota**: {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan fornisce un ambiente di produzione. Se hai bisogno di un ambiente di sviluppo e test, vedi [Informazioni su Starter Plan](starter_plan.html).

Per i membri che inizieranno la rete, IBM fornisce un'interfaccia grafica utente (GUI) per assistere passo dopo passo l'iniziatore della rete nelle procedure chiave per configurare la rete e fornirla. Ciò include l'invito di altri membri e l'impostazione delle regole di governance. Per ulteriori informazioni, vedi [Governance della rete Enterprise Plan](get_start.html). Dopo che la rete è stata distribuita, è disponibile un'interfaccia grafica utente (GUI), il Network Monitor, per monitorare lo stato e l'attività della rete, gestire le attività di rete chiave tra cui le nuove distribuzioni, l'aggiunta o la rimozione di membri, il ciclo di vita del chaincode e la gestione dei canali e per cercare assistenza tecnica. Per ulteriori informazioni sul Monitor Network, vedi [Gestione della rete Enterprise Plan](v10_dashboard.html).

Registrati ora per la tua [adesione a {{site.data.keyword.blockchainfull_notm}}![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

{{site.data.keyword.blockchainfull_notm}} Platform è sviluppato con componenti Hyperledger Fabric chiave, che includono un'Autorità di certificazione (CA) e almeno 1 peer (massimo 6). Enterprise Plan fornisce anche un servizio di ordinazione Kafka con tolleranza agli errori anomali per i membri della rete.

La CA Fabric è l'Autorità di certificazione fornita con Enterprise Plan. Sono fornite due CA intermedie per membro, che concedono l'adesione alla rete. Utilizzando la CA, il membro può anche fornire l'adesione (certificati) agli utenti della rete.

È importante comprendere che l'accodamento di una transazione al libro mastro si articola in tre fasi:  
1. Simulazione e approvazione della transazione (peer)
2. Ordinazione (servizio di ordinazione)
3. Convalida e commit (peer)

I peer Fabric appartenenti ai membri sono l'interfaccia o il gateway per le applicazioni per eseguire il chaincode, fornendo la logica di business per eseguire le transazioni sul libro mastro.  Tutte le transazioni devono essere approvate. Gli altri membri della rete concedono questa approvazione. Dopo l'approvazione, le transazioni vengono inviate a un servizio di ordinazione fornito da IBM (Kafka).

Oltre ai componenti blockchain principali, l'opzione di adesione Enterprise fornisce un'infrastruttura con memorizzazione di dati e comunicazioni protetti (TLS) e un'elevata disponibilità.  Mentre le reti Fabric condividono queste risorse d'infrastruttura, viene fornito un isolamento per i nodi componente Fabric in una rete e ciascun nodo viene eseguito in un contenitore docker sicuro che protegge l'ambiente di esecuzione.

Il solo aspetto che deve essere determinato è la dimensione dei peer richiesta dalla rete. Questa decisione è basata sul numero di canali richiesti, più il carico di lavoro per canale, l'utilizzo della memoria e lo spazio su disco (memorizzazione). 

Dovresti utilizzare Enterprise Plan per delle distribuzioni più stabili a livello di produzione o quasi di produzione. Per finalità di test, usa [Starter Plan](starter_plan.html), [sviluppa nel servizio IBM Container](https://ibm-blockchain.github.io/) oppure [installa le immagini Docker in locale](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Prezzi  
Per utilizzare Enterprise Plan, i membri della rete devono pagare $1.000 al mese come costo di adesione e altri $1.000 al mese per ciascuno dei loro peer nella rete. I costi mensili vengono addebitati ogni giorno su base proporzionale. Ad esempio, un membro (costo di adesione associato di $1.000) di due peer (costo per ogni peer di $1.000 X 2 peer) deve pagare $3.000 ogni mese. Se il mese ha 30 giorni, il membro paga $ 100 ($3.000/30) ogni giorno. Nota: se hai bisogno di un'elevata disponibilità (HA, high availability), devi raddoppiare il tuo numero di peer richiesti per fornire le funzionalità HA.

I membri della rete possono pagare la loro fattura con i loro account {{site.data.keyword.cloud_notm}} che contengono lo spazio per creare l'istanza di rete. In alternativa, un membro della rete può coprire l'addebito per tutti i membri nella rete. Per ulteriori informazioni su come pagare per le reti blockchain, vedi [Pagamento per la rete](howto/pay_for_the_network.html).

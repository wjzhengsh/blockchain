---

copyright:
  years: 2017
lastupdated: "2017-09-20"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Piano Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform

Il piano Enterprise di {{site.data.keyword.blockchainfull}} Platform è la prima opzione di adesione disponibile per le organizzazioni che desiderano creare, o aderire a, una rete di business blockchain. Questo piano fornisce l'infrastruttura chiave insieme agli strumenti e al supporto per distribuire facilmente una rete blockchain pronta per la produzione altamente sicura.

Per i membri che inizieranno la rete, IBM fornisce un'interfaccia grafica utente (GUI) per assistere passo dopo passo l'iniziatore della rete nelle procedure chiave per configurare la rete e fornirla. Ciò include l'invito di altri membri e l'impostazione delle regole di governance. Dopo che la rete è stata distribuita, è disponibile un'interfaccia grafica utente (GUI) per monitorare lo stato e l'attività della rete, gestire le attività di rete chiave quali le nuove distribuzioni, l'aggiunta/rimozione di membri, il ciclo di vita del chaincode e la gestione dei canali e per cercare supporto tecnico. Accedi ad ulteriori informazioni su come ottenere [supporto](ibmblockchain_support.html).

{{site.data.keyword.blockchainfull_notm}} Platform è sviluppata con componenti Hyperledger Fabric chiave, compresi un'Autorità di certificazione (CA) e almeno 1 peer (massimo 6).  IBM fornisce anche un servizio ordini Kafka con tolleranza di errori anomali per i membri della rete. 

La CA Fabric è l'autorità di certificazione fornita con il piano Enterprise. Sono fornite due CA intermedie per membro, che concedono l'adesione alla rete. Utilizzando la CA, il membro può anche fornire l'adesione (certificati) agli utenti finali della rete.

È importante comprendere che l'accodamento di una transazione al libro mastro coinvolge tre fasi:  
1. Simulazione e approvazione della transazione (peer)
2. Ordinazione (servizio ordini)
3. Convalida e registrazione (peer)

I peer Fabric appartenenti ai membri sono l'interfaccia o il gateway per le applicazioni per eseguire il chaincode, fornendo la logica di business per eseguire le transazioni sul libro mastro.  Tutte le transazioni devono essere approvate. Gli altri membri della rete concedono questa approvazione. Dopo l'approvazione, le transazioni vengono inviate a un servizio ordini fornito da IBM (Kafka)

Oltre ai componenti blockchain principali, l'opzione di adesione Enterprise fornisce un'infrastruttura con memorizzazione di dati e comunicazioni protetti (TLS) e un'elevata disponibilità.  Mentre le reti Fabric condividono queste risorse d'infrastruttura, viene fornito un isolamento per i nodi componente Fabric in una rete e ciascun nodo viene eseguito in un contenitore docker sicuro che protegge l'ambiente di esecuzione.

Il solo aspetto che deve essere determinato è la dimensione dei peer richiesta per la rete. Questa decisione è basata sul numero di canali richiesti, più il carico di lavoro per canale, l'utilizzo della memoria e lo spazio su disco (memorizzazione). Sotto troverai le dimensioni di peer disponibili tramite il piano di servizio di livello Enterprise e delle indicazioni per scegliere il peer corretto.

La piattaforma IBM Blockchain dovrebbe venire utilizzata per una produzione più stabile o per le distribuzioni quasi al livello di produzione. Per scopi di test, utilizza il servizio IBM Container o le immagini scaricate localmente.

Il piano Enterprise fornisce la CA e il servizio ordini. La tariffa di iscrizione è 1.000$ e una tariffa per peer di 1.000$ viene associata alla rete. Se desideri avere l'elevata disponibilità (HA), devi acquistare un ulteriore peer per fornire le funzionalità HA. Ad esempio, un'organizzazione (tariffa di iscrizione associata di 1.000$) di due peer (1.000 X 2 peer) con HA (1.000$ per 2 peer HA) richiede un addebito mensile di 5.000$.

Registrati ora per la tua {{site.data.keyword.IBM_notm}} [adesione a {{site.data.keyword.blockchainfull_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

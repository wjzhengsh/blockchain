---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}


# Amministrazione della tua console
{: #ibp-console-manage-console}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Ci sono diverse azioni che puoi eseguire per gestire la modalità di funzionamento della tua console. Questo argomento descrive le azioni al di fuori delle operazioni del nodo blockchain che ti aiutano nell'utilizzo quotidiano della console.
{:shortdesc}

## Aggiungi e rimuovi utenti dalla console

Il provisioning di questa console {{site.data.keyword.blockchainfull}} Platform viene eseguito con l'indirizzo email del proprietario {{site.data.keyword.IBM_notm}} come un amministratore della console. L'amministratore può quindi concedere ad altri utenti l'accesso alla console.

Fai clic su **Utenti** nella navigazione a sinistra per concedere a nuovi utenti l'accesso alla console tramite il loro indirizzo email. Fai clic su **Aggiungi nuovi utenti** e specifica un elenco di indirizzi email che desideri autorizzare insieme al loro tipo di utente: `Admin` o `General`. Gli utenti con il tipo `Admin` possono aggiungere nuovi utenti o eliminare utenti esistenti e visualizzare i log della console. Gli utenti `General` possono eseguire l'accesso alla console ma non possono aggiungere o rimuovere utenti oppure visualizzare i log.


Gli utenti elencati in questo pannello sono semplicemente gli indirizzi email di utenti che possono eseguire l'accesso alla console. Non hanno alcuna relazione con le **Organizzazioni disponibili** nella scheda Organizzazioni o con le identità archiviate dal portafoglio della console.
{:note}

## Aggiorna l'indirizzo e-mail dell'amministratore

Se la console viene utilizzata da più utenti od organizzazioni, ti consigliamo di creare un indirizzo email funzionale per accedere alla tua rete. L'utilizzo di una email funzionale ti consente di accedere alla console se l'amministratore originale lascia la tua organizzazione o ha il suo indirizzo email sospeso. Se non è possibile creare una email funzionale, puoi fornire l'accesso `Admin` a più utenti per evitare di dipendere da un singolo utente.

Per aggiornare l'indirizzo email dell'amministratore della console che era stato configurato quando è stata distribuita la console, devi avere eseguito l'accesso come un amministratore della console. Vai alla scheda **Utenti** e fai clic su **Configura** sul tile **ID IBM**. Nel pannello che viene aperto, specifica un nuovo indirizzo email per l'amministratore della console.


## Visualizzazione dei tuoi log della console

Puoi facilmente accedere ai log della console se devi eseguire il debug di problemi che riscontri quando utilizzi la console o ti servi dei tuoi nodi. Puoi anche aumentare o diminuire la quantità di log raccolti dalla console impostando il livello di registrazione. I log della console sono raccolti separatamente dai [log dei tuoi nodi](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), che sono raccolti dal servizio {{site.data.keyword.IBM_notm}} Kubernetes.

Vai alla scheda **Impostazioni** nel browser della console per modificare le impostazioni di registrazione. I log della console sono raccolti da due origini separate:

  * **Registrazione client:** questi log vengono raccolti quando i comandi vengono inviati dal tuo browser alla console.
  * **Registrazione server:** questi log vengono raccolti quando la console invia i comandi ai tuoi nodi e dalla distribuzione della console. Questi log includono l'output di registrazione di Hyperledger Fabric.

Imposta la quantità di log raccolti utilizzando l'elenco a discesa sotto ogni tipo di log. Ad esempio, **Errore** e **Avvertenza** raccolgono la minima quantità di log, mentre **Debug** e **Tutti** raccolgono quella massima.

Puoi visualizzare i log della console solo se hai eseguito l'accesso come un amministratore della console. Per visualizzare i log dalla scheda **Impostazioni**, sostituisci la parola `settings` nell'URL del browser con `api/v1/logs`. Ad esempio, se l'URL della tua console è `localhost:3001/settings`, puoi visualizzare i tuoi log andando a `localhost:3001/api/v1/logs`. I log di client e server vengono raccolti in file separati. I log più recenti si trovano nella parte superiore della pagina.


## Visualizzazione dei tuoi log di nodo
{: #ibp-console-manage-console-node-logs}

I log dei tuoi peer, dei tuoi ordinanti e delle tue CA (Certificate Authority, Autorità di certificazione) sono raccolti dal servizio {{site.data.keyword.IBM_notm}} Kubernetes. Utilizza la procedura di seguito indicata per visualizzare i log dei tuoi nodi dal cluster dove hai distribuito la tua rete {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Apri il [dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps/) e vai alla schermata della panoramica del tuo cluster del servizio {{site.data.keyword.IBM_notm}} Kubernetes.
2. Sopra la schermata della panoramica del cluster, fai clic su **Dashboard Kubernetes**.
3. Nel dashboard Kubernetes, utilizza l'elenco a discesa Spazio dei nomi della navigazione di sinistra per modificare lo spazio dei nomi nell'istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform. Il nome dell'istanza del servizio sarà una lunga stringa di lettere e numeri. Puoi trovare il tuo nome dell'istanza del servizio all'inizio dell'URL della tua console {{site.data.keyword.blockchainfull_notm}} Platform.
4. Nella navigazione di sinistra, fai clic su **Pod** per visualizzare l'elenco di pod di nodi che hai distribuito.
5. Fai clic su un pod. Fai quindi clic su **Visualizza log** nel menu superiore per aprire i log del tuo nodo. Sopra i log, puoi utilizzare il menu a discesa dopo **Log da** per visualizzare i log da diversi contenitori all'interno del pod. Ad esempio, il tuo peer e il database dello stato (CouchDB ad esempio) sono eseguiti in contenitori differenti e generano dei log differenti.

Per impostazione predefinita, i log dei tuoi nodi vengono raccolti localmente all'interno del tuo cluster. Puoi anche utilizzare il servizio {{site.data.keyword.cloud_notm}} Log Analysis oppure un servizio di terze parti per raccogliere, archiviare e analizzare i log dalla tua rete. Per informazioni, vedi [Registrazione e monitoraggio per il servizio {{site.data.keyword.IBM_notm}} Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Registrazione e monitoraggio per il servizio {{site.data.keyword.IBM_notm}} Kubernetes").


## Monitoraggio dell'utilizzo delle risorse

Quando i nodi {{site.data.keyword.cloud_notm}} Platform vengono distribuiti, sono preconfigurati con impostazioni di CPU, memoria a archiviazione predefinite e tali valori non possono essere modificati. Puoi monitorare il tuo utilizzo delle risorse utilizzando il dashboard del servizio {{site.data.keyword.IBM_notm}} Kubernetes. Se provi a creare un nuovo nodo nella console {{site.data.keyword.cloud_notm}} Platform e ottieni un errore relativo alla necessità di espandere il tuo cluster Kubernetes, è possibile aggiungere ulteriore archiviazione al cluster Kubernetes. Vedi queste informazioni [ ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Modifica della dimensione e dell'IOPS del tuo dispositivo di archiviazione esistente") su come aumentare le prestazioni o la capacità di archiviazione del tuo volume esistente.

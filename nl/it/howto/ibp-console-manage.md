---

copyright:
  years: 2019
lastupdated: "2019-04-23"

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


# Amministrazione della tua console
{: #ibp-console-manage-console}

Ci sono diverse azioni che puoi eseguire per gestire la modalità di funzionamento della tua console. Questo argomento descrive le azioni al di fuori delle operazioni del nodo blockchain che ti aiutano nell'utilizzo quotidiano della console.
{:shortdesc}

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain.

## Aggiungi e rimuovi utenti dalla console
{: #ibp-console-manage-console-add-remove}

Il processo di autorizzazione della console sta subendo delle variazioni nel corso del programma beta. Se hai un'istanza del servizio blockchain esistente che utilizzava l'autenticazione ID {{site.data.keyword.IBM_notm}} originale, non sei più in grado di vedere i tuoi utenti elencati nella scheda **Utenti**. Devi concedere loro l'accesso alla console utilizzando il processo IAM descritto di seguito.
{:important}

A ogni utente che accede alla console deve essere assegnata una politica di accesso con definito un ruolo utente {{site.data.keyword.cloud_notm}} IAM (Identity and Access Management). La politica determina quali azioni può eseguire l'utente all'interno della console. Il provisioning della console {{site.data.keyword.blockchainfull}} Platform viene eseguito con l'indirizzo e-mail del proprietario {{site.data.keyword.cloud_notm}} come amministratore della console. Per impostazione predefinita, a tale utente {{site.data.keyword.cloud_notm}} viene dato il ruolo di gestore (`Manager`) per il servizio Blockchain Platform 2.0 in IAM. L'amministratore della console può concedere l'accesso alla console ad altri utenti utilizzando l'IU IAM. Per ulteriori informazioni su IAM, vedi il documento relativo a [cos'è IAM ![Icona link esterno](../images/external_link.svg "Icona link esterno") ](/docs/iam?topic=iam-iamoverview#iamoverview "Cos'è IAM?").  

Quando [utilizzi IAM per invitare utenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](/docs/iam?topic=iam-iamuserinv#iamuserinv "Utilizzo di IAM per invitare gli utenti"), devi completare la seguente procedura per configurare i loro ruoli e accedere alla console.
 1. Dalla barra dei menu, fai clic su **Gestisci** > **Accesso (IAM)** e seleziona quindi **Utenti**.
 2. Fai clic su **Invita utente**.
 3. Immetti l'indirizzo email dell'utente o degli utenti.
 4. Dall'elenco a discesa `Servizi`, seleziona `Blockchain Platform 2.0`.
 5. Scorri verso il basso a `Seleziona i ruoli`.
 6. In `Assegna i ruoli di accesso al servizio`, scegli un ruolo per l'utente, che può essere `Gestore`, `Scrittore` e `Lettore`.
 7. Fai clic su **Invita utenti**.

| Ruolo | Funzionalità |
|--------|----------|
| Gestore | In qualità di Gestore, disponi di autorizzazioni al di sopra del ruolo di Scrittore. Puoi fare tutto quello che fa un Lettore e uno Scrittore e puoi inoltre: <ul><li>Aggiungere nuovi componenti.</li><li>Eliminare i componenti di cui è stato eseguito il provisioning.</li><li>Modificare i livelli di registrazione nei log della console.</li></ul> |
| Scrittore | In qualità di Scrittore, disponi di autorizzazioni che vanno oltre il ruolo di Lettore, tra cui: <ul><li>Importare componenti.</li><li>Rimuovere i componenti importati.</li><li>Registrare gli utenti su una CA.</li></ul>  |
| Lettore | In qualità di Lettore, puoi eseguire azioni di sola lettura, tra cui: <ul><li>Visualizzare l'IU della console.</li><li>Visualizzare il log della console.</li><li>Esportare componenti.</li></ul> | |

 Le autorizzazioni sono cumulative. Se selezioni un ruolo `Gestore`. l'utente potrà anche eseguire tutte le azioni di `Scrittore` e `Lettore`, senza dover selezionare anche questi ruoli. Analogamente, un utente con il ruolo `Scrittore` potrà eseguire tutte le azioni previste nel ruolo `Lettore`.  Per l'accesso alla console, devi solo selezionare un ruolo in `Ruoli di accesso al servizio`, non devi operare alcuna selezione in `Ruoli di accesso alla piattaforma`.

![Aggiorna versione Kubernetes](../images/AddICPUser.gif)

Dopo che hai aggiunto dei nuovi utente alla console, potrebbero non essere in grado di visualizzare tutti i nodi, canali o chaincode distribuiti da altri utenti. Per lavorare con questi componenti, ciascun utente deve importare le identità associate in un suo portafoglio della console. Per ulteriori informazioni, vedi [Memorizzazione delle identità nel tuo portafoglio della console](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet).
{:important}

Se devi modificare il ruolo di un utente:
 1. Dalla barra dei menu, fai clic su **Gestisci** > **Accesso (IAM)** e seleziona quindi **Utenti**.
 2. Fai clic sul menu Azioni accanto all'utente che desideri modificare e seleziona **Assegna accesso**.
 3. Seleziona il tile **Assegna l'accesso alle risorse**.
 4. Dall'elenco a discesa `Servizi`, seleziona `Blockchain Platform 2.0`.
 5. Scorri verso il basso a `Seleziona i ruoli`.
 6. In `Assegna i ruoli di accesso al servizio`, scegli un ruolo per l'utente, che può essere `Gestore`, `Scrittore` e `Lettore`.
 7. Fai clic su **Assegna**.

Quando devi rimuovere l'accesso di un utente alla console, seguire le istruzioni nell'[argomento di IAM relativo alla rimozione di utenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](/docs/iam?topic=iam-remove#remove "Rimozione di utenti").

### Assegna ruoli di accesso a singoli o gruppi di utenti in IAM
{: #ibp-console-manage-console-users-groups}

Quando imposti le politiche {{site.data.keyword.cloud_notm}} IAM, puoi assegnare ruoli a un singolo utente o a un gruppo di utenti.

<dl>
<dt>Singoli utenti</dt>
<dd>Potresti avere un utente specifico che ha bisogno di più o meno autorizzazioni rispetto al resto del tuo team. Puoi personalizzare le autorizzazioni su base individuale in modo che ogni persona disponga delle autorizzazioni necessarie per completare le proprie attività. Puoi assegnare più di un ruolo {{site.data.keyword.Bluemix_notm}} IAM a ogni utente.</dd>
<dt>Più utenti in un gruppo di accesso</dt>
<dd>Puoi creare un gruppo di utenti e quindi assegnare le autorizzazioni a tale gruppo. Ad esempio, puoi raggruppare tutti i team leader e assegnare al gruppo l'accesso come amministratore. Quindi, puoi raggruppare tutti gli sviluppatori e assegnare a quel gruppo solo l'accesso in scrittura. Puoi assegnare più di un ruolo {{site.data.keyword.Bluemix_notm}} IAM a ogni gruppo di accesso. Quando assegni le autorizzazioni a un gruppo, viene interessato qualsiasi utente aggiunto o rimosso da quel gruppo. Se aggiungi un utente al gruppo, anch'egli ha l'accesso aggiuntivo. Se viene rimosso, il suo accesso viene revocato.</dd>
</dl>

Gli utenti che aggiungi in IAM sono semplicemente gli indirizzi email di utenti che possono eseguire l'accesso alla console. Non hanno alcuna relazione con le **Organizzazioni disponibili** nella scheda Organizzazioni o con le identità archiviate dal portafoglio della console.
{:note}

## Aggiorna l'indirizzo email dell'amministratore

Se la console viene utilizzata da più utenti od organizzazioni, ti consigliamo di creare un indirizzo email funzionale per accedere alla tua rete. L'utilizzo di una email funzionale ti consente di continuare ad accedere alla console anche quando l'amministratore originale lascia la tua organizzazione o ha il suo indirizzo email sospeso. Solo un singolo indirizzo email può essere utilizzato come contatto dell'amministratore.

Per aggiornare l'indirizzo email dell'amministratore della console che era stato configurato quando è stata distribuita la console, devi avere eseguito l'accesso come amministratore della console. Vai alla scheda **Utenti** e fai clic su **Configura** sul tile **ID {{site.data.keyword.IBM_notm}}**. Nel pannello che viene aperto, specifica un nuovo indirizzo email per l'amministratore della console.

## Visualizzazione dei tuoi log
{: #ibp-console-manage-logs}

Quando utilizzi la console {{site.data.keyword.blockchainfull_notm}} Platform, potresti aver bisogno di visualizzare i log per eseguire il debug di un problema.

### Visualizzazione dei tuoi log della console
{: #ibp-console-manage-console-logs}

Puoi facilmente accedere ai log della console se devi eseguire il debug di problemi che riscontri quando utilizzi la console o ti servi dei tuoi nodi. Puoi anche impostare il livello di registrazione nei log per aumentare o diminuire la quantità di log raccolta dalla console. I log della console sono raccolti separatamente dai [log dei nodi](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), che sono raccolti dal servizio {{site.data.keyword.cloud_notm}} Kubernetes.

Vai alla scheda **Impostazioni** nel browser della console per modificare le impostazioni di registrazione. I log della console sono raccolti da due origini separate:

  * **Registrazione client:** questi log vengono raccolti quando i comandi vengono inviati dal tuo browser alla console.
  * **Registrazione server:** questi log vengono raccolti quando la console invia i comandi ai tuoi nodi e dalla distribuzione della console. Questi log includono l'output di registrazione di Hyperledger Fabric.

Imposta la quantità di log raccolti utilizzando l'elenco a discesa sotto ogni tipo di log. Ad esempio, **Errore** e **Avvertenza** raccolgono la minima quantità di log, mentre **Debug** e **Tutti** raccolgono quella massima.

Puoi visualizzare i log della console solo se hai eseguito l'accesso come un amministratore della console. Per visualizzare i log dalla scheda **Impostazioni**, sostituisci la parola `settings` nell'URL del browser con `api/v1/logs`. Ad esempio, se l'URL della tua console è `localhost:3001/settings`, puoi visualizzare i tuoi log andando a `localhost:3001/api/v1/logs`. I log di client e server vengono raccolti in file separati. I log più recenti si trovano nella parte superiore della pagina.

### Visualizzazione dei tuoi log di nodo
{: #ibp-console-manage-console-node-logs}

I log dei tuoi peer, dei tuoi ordinanti e delle tue CA (Certificate Authority, Autorità di certificazione) sono raccolti dal servizio {{site.data.keyword.IBM_notm}} Kubernetes. Utilizza la procedura di seguito indicata per visualizzare i log dei tuoi nodi dal cluster dove hai distribuito la tua rete {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Apri il [dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/resources) e vai alla schermata della panoramica del tuo cluster {{site.data.keyword.IBM_notm}} Kubernetes Service.
2. Sopra la schermata della panoramica del cluster, fai clic su **Dashboard Kubernetes**.
3. Nel dashboard Kubernetes, utilizza l'elenco a discesa Spazio dei nomi della navigazione di sinistra per modificare lo spazio dei nomi nell'istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform. Il nome dell'istanza del servizio sarà una lunga stringa di lettere e numeri. Puoi trovare il tuo nome dell'istanza del servizio all'inizio dell'URL della tua console {{site.data.keyword.blockchainfull_notm}} Platform.
4. Nella navigazione di sinistra, fai clic su **Pod** per visualizzare l'elenco di pod di nodi che hai distribuito.
5. Fai clic su un pod. Fai quindi clic su **Visualizza log** nel menu superiore per aprire i log del tuo nodo. Sopra i log, puoi utilizzare il menu a discesa dopo **Log da** per visualizzare i log da diversi contenitori all'interno del pod. Ad esempio, il tuo peer e il database dello stato (CouchDB ad esempio) sono eseguiti in contenitori differenti e generano dei log differenti.

Per impostazione predefinita, i log dei tuoi nodi vengono raccolti localmente all'interno del tuo cluster. Puoi anche utilizzare il servizio {{site.data.keyword.cloud_notm}} Log Analysis oppure un servizio di terze parti per raccogliere, archiviare e analizzare i log dalla tua rete. Per ulteriori informazioni, vedi [Registrazione e monitoraggio per il servizio {{site.data.keyword.IBM_notm}} Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Registrazione e monitoraggio per il servizio {{site.data.keyword.IBM_notm}} Kubernetes"). TI consigliamo di avvalerti del servizio [LogDNA di {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-kube#kube "Gestione dei log di cluster Kubernetes con IBM Log Analysis with LogDNA") che ti consente di analizzare facilmente i log in tempo reale.

### Visualizzazione dei tuoi log del contenitore dello smart contract
{: #ibp-console-manage-console-container-logs}

Se riscontri dei problemi con il tuo smart contract, puoi visualizzare i log del contenitore dello smart contract o del chaincode per eseguire il debug di un problema.

- Apri il tuo dashboard Kubernetes e fai clic sul pod del peer in cui è in esecuzione lo smart contract.
- Fai clic sul link `exec` dal tuo dashboard per eseguire exec nel pod. Per impostazione predefinita punta al contenitore peer.
- Passa al contenitore `dind` selezionandolo dall'elenco a discesa.
- Esegui il comando `docker ps -a` per visualizzare l'elenco di contenitori chaincode.
- Esegui `docker logs <chaincode-container-ID>` sostituendo <chaincode-container-ID> con l'ID del tuo contenitore chaincode.

## Scadenza del cluster Kubernetes
{: #ibp-console-manage-console-cluster-expiration}

Se stai utilizzando un cluster {{site.data.keyword.cloud_notm}} Kubernetes Service gratuito, scadrà dopo 30 giorni. Quando questo accade, non puoi più accedere alla tua console. Vengono eliminati anche tutti i nodi e i certificati associati. Puoi avere un solo cluster gratuito per volta. Pertanto, prima di poter creare un'altra istanza del servizio blockchain, devi assicurarti che il cluster scaduto e la relativa istanza del servizio associata siano stati eliminati da {{site.data.keyword.cloud_notm}}. Per confermare se sono già stati eliminati o per eliminare manualmente queste risorse, attieniti alla seguente procedura:

1. Nel tuo dashboard {{site.data.keyword.cloud_notm}}, fai clic sul menu a tre linee e apri l'**Elenco risorse**.
2. Scorri al triangolino **Servizi** ed espandilo per visualizzare la tua istanza del servizio.
3. Se la tua istanza è elencata, fai clic sul menu delle azioni per l'istanza del servizio e quindi su **Elimina**, per eliminare questa istanza del servizio.
4. Scorri al triangolino **Cluster Kubernetes** ed espandilo per visualizzare il tuo cluster gratuito.
5. Se il tuo cluster gratuito è elencato, fai clic sul menu delle azioni per il cluster e fai quindi clic su **Elimina** per eliminare il cluster gratuito.

Una volta completate queste azioni, puoi attenerti alla [procedura originale](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks) per creare un nuovo cluster Kubernetes e una nuova istanza del servizio blockchain dalla pagina del Catalogo di IBM Cloud.

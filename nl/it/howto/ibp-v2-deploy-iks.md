---

copyright:
  years: 2019
lastupdated: "2019-03-05"

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

# Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta
{: #ibp-v2-deploy-iks}

{{site.data.keyword.blockchainfull}} Platform 2.0 è una release beta gratuita che include la console {{site.data.keyword.blockchainfull_notm}} Platform, una GUI che può semplificare e accelerare le operazioni da te eseguite per distribuire e gestire i componenti blockchain. Questa esercitazione descrive come iniziare a lavorare con {{site.data.keyword.blockchainfull_notm}} Platform 2.0 e utilizzare la console per distribuire e gestire componenti blockchain nel tuo cluster del servizio {{site.data.keyword.IBM_notm}} Kubernetes su {{site.data.keyword.cloud_notm}}.
{:shortdesc}

**Gruppi di destinatari:** questo argomento è pensato per gli amministratori del sistema che sono responsabili della configurazione di un cluster Kubernetes in {{site.data.keyword.IBM_notm}} e per la distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform.

 Dopo che hai distribuito {{site.data.keyword.blockchainfull}} Platform al tuo cluster Kubernetes, puoi avviare la console per creare e gestire i tuoi componenti blockchain. Utilizzando un cluster del servizio {{site.data.keyword.IBM_notm}} Kubernetes per distribuire {{site.data.keyword.blockchainfull_notm}} Platform 2.0, riscontri i seguenti importanti vantaggi:

- **Controllo:** controlli e gestisci i tuoi certificati e i tuoi componenti blockchain da una singola console centrale. Distribuisci solo i componenti necessari per la tua attività di business e ne aggiungi altri man mano che le tue esigenze aumentano.
- **Distribuzione basata su Kubernetes flessibile:** puoi avvalerti delle opzioni di calcolo (CPU, memoria, archiviazione) per il tuo cluster Kubernetes e sfruttare le opzioni HA e DR integrate.


## Considerazioni
{: #ibp-v2-deploy-iks-considerations}

Prima di distribuire la console, assicurati di aver compreso le seguenti considerazioni:

- {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta è sviluppato con Hyperledger Fabric v1.4.
- Tutti i peer distribuiti con la versione beta 2.0 gratuita utilizzano CouchDB come loro database dello stato.
- Hai l'opzione di utilizzare un cluster Kubernetes gratuito per la valutazione dell'offerta beta; tuttavia, capacità e prestazioni sono limitate, nessuno dei tuoi dati può essere migrato e il cluster viene eliminato dopo 30 giorni.
- Sei responsabile della gestione del monitoraggio dell'integrità, della sicurezza e della registrazione del tuo cluster Kubernetes. Consulta queste informazioni sulla [ ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Responsabilità di gestione del cluster") per i dettagli su quanto gestito da {{site.data.keyword.cloud_notm}} e su quanto è una tua responsabilità.
- Sei anche responsabile del monitoraggio dell'utilizzo delle risorse del tuo cluster Kubernetes utilizzando il dashboard Kubernetes. Se devi aumentare la capacità di archiviazione o le prestazioni del tuo cluster, consulta queste informazioni relative alla modalità di [modifica del tuo volume esistente ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "Modifica della dimensione e dell'IOPS del tuo dispositivo di archiviazione esistente").
- Sei responsabile della gestione e della protezione dei tuoi certificati e delle tue chiavi pubbliche e private. IBM non archivia i tuoi certificati nel cluster Kubernetes.
- L'offerta beta 2.0 gratuita è disponibile solo nella regione **Dallas** del servizio {{site.data.keyword.cloud_notm}} Kubernetes. Questa regione include i data center in Dallas, San Jose, Houston e Brasile. Pertanto, tutti i componenti blockchain possono risiedere in uno qualsiasi di questi quattro data center. Non vengono distribuiti altrove.
- Mentre l'offerta beta è gratuita, dovrai ancora pagare per il tuo cluster Kubernetes, se scegli un cluster a pagamento.
- Kubernetes deve essere alla versione 1.11 o successiva nel tuo cluster Kubernetes {{site.data.keyword.cloud_notm}}. Utilizza queste istruzioni per [eseguire un upgrade dei tuoi cluster nuovi ed esistenti](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) a questa versione.

## Esercitazione video
{: #ibp-v2-deploy-video}

Guarda il seguente video per saperne di più su {{site.data.keyword.blockchainfull}} Platform free 2.0 beta e su come puoi iniziare a distribuire {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Video di IBM Blockchain Platform free 2.0 beta - esercitazione di distribuzione" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/gPnkVQiHRqk" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

## Prima di cominciare
{: #ibp-v2-deploy-iks-prereq}

Prima di cominciare:

- Assicurati di avere un [account a pagamento {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.cloud.ibm.com/catalog/services/blockchain "Catalogo"). Se non hai un account:
1. Fai clic sul pulsante **Registrazione**.
2. Dopo aver creato un account gratuito, aggiornalo a un tipo **Pagamento a consumo** andando in **Gestisci** > **Fatturazione e utilizzo** > **Fatturazione** nella console {{site.data.keyword.cloud_notm}} e facendo clic su **Aggiungi carta di credito**.

Quando intendi utilizzare l'istanza del servizio nel contesto di una più ampia soluzione a livello dell'organizzazione, si consiglia che le organizzazioni partecipanti utilizzino un indirizzo email funzionale per creare la loro rete. In questo caso, l'accesso alla rete non dipende dalla disponibilità di alcun singolo utente.
{:tip}  

- Se intendi utilizzare un cluster del servizio {{site.data.keyword.cloud_notm}} Kubernetes esistente, controlla la versione di Kubernetes ed eseguine l'upgrade alla versione 1.11 o successiva, se necessario. Per ulteriori informazioni su come determinare quale sia la versione di Kubernetes che il tuo cluster sta eseguendo e su come eseguire un upgrade della versione, vedi [Aggiornamento della versione di Kubernetes del tuo cluster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).

### Browser
{: #ibp-v2-deploy-iks-browsers}
Il seguente elenco specifica il software browser richiesto minimo per la console {{site.data.keyword.blockchainfull_notm}} Platform.

- Chrome: la versione più recente per il tuo sistema operativo
- Firefox: le versioni regolari (non ESR) più recenti per il tuo sistema operativo
- Safari: la versione più recente per Mac

### Risorse richieste
{: #ibp-v2-deploy-iks-resources-required}

Per distribuire la console {{site.data.keyword.blockchainfull_notm}} Platform in un cluster del servizio {{site.data.keyword.cloud_notm}} Kubernetes, devi assicurarti che il tuo cluster Kubernetes soddisfi i requisiti minimi relativi alle risorse hardware:

|Tipo di cluster Kubernetes | Caso di utilizzo | CPU | RAM | Nodi di lavoro |
|-----------|------|-----|-----------------------|
|Standard (consigliato) | Adatto per MVP | 4 (condivisa) | 16 GB (condivisa)|multipli|
|Gratuito | Adatto per la valutazione | 2 | 4 GB | 1 |  

Queste risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Se utilizzi un cluster Kubernetes gratuito, tieni presente che il cluster verrà eliminato dopo il periodo di prova di 30 giorni e tutti gli asset associati verranno rimossi. Le prestazioni sono significativamente più lente in un cluster gratuito.
{:note}

## Fase uno: Crea un'istanza del servizio in {{site.data.keyword.cloud_notm}}
{: #ibp-v2-deploy-iks-create-service-instance}

Utilizza la seguente procedura per creare un'istanza del servizio di {{site.data.keyword.blockchainfull_notm}} Platform 2.0 su {{site.data.keyword.cloud_notm}}.

1. Individua il [servizio blockchain ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.cloud.ibm.com/catalog/services/blockchain) nel catalogo {{site.data.keyword.cloud_notm}} oppure cerca `Blockchain` nella tua pagina del catalogo {{site.data.keyword.cloud_notm}}.
2. Puoi, facoltativamente, rinominare il **Nome servizio** per la tua istanza in modo che tu possa riconoscerla facilmente in futuro.
3. Per Beta, **Dallas** è la sola regione disponibile e non può essere modificata.
4. Puoi lasciare i campi relativi alle tag e ai gruppi di risorse invariate.
5. Fai clic su **Crea** per eseguire il provisioning dell'istanza del servizio.

## Passo due: Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform 2.0
{: #ibp-v2-deploy-iks-steps}

Puoi attenerti alle istruzioni per distribuire {{site.data.keyword.blockchainfull_notm}} Platform 2.0 immediatamente dopo che hai creato l'istanza del servizio.

1. Il passo **Benvenuti e prerequisiti**. Se già hai un cluster del servizio {{site.data.keyword.IBM_notm}} Kubernetes esistente nella regione **Dallas** e vuoi utilizzarlo per il tuo servizio blockchain, seleziona la casella di spunta. **Se utilizzi un cluster esistente, puoi saltare il prossimo passo; ma assicurati che la versione Kubernetes sia alla v1.11 o successiva**. Fai clic su **Continua**.
2. Il passo **Crea cluster**. Se selezioni la casella di spunta nel passo 1 per utilizzare un cluster Kubernetes esistente, questo passo viene tralasciato. Altrimenti, fai clic su **Crea un nuovo cluster**, che avvia il dashboard Kubernetes {{site.data.keyword.cloud_notm}} per creare un cluster. Per ulteriori informazioni, vedi [Introduzione al servizio {{site.data.keyword.cloud_notm}} Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/containers/container_index.html). Prevedi del tempo supplementare per il completamento di questo processo.
  - Indipendentemente dal tipo di cluster che scegli, devi selezionare l'ubicazione del cluster Kubernetes di **Dallas** per la release Beta.
  - Scegli **Cluster standard (consigliato):**  se hai bisogno di un'opzione a più lungo termine che include più nodi per l'elevata disponibilità e che può essere migrata dall'offerta Beta all'offerta GA quando è disponibile. **Assicurati di scegliere la versione Kubernetes v1.11 o successiva.**
  - Scegli **Cluster gratuito.** se intendi utilizzare il cluster per meno di 30 giorni. **Nota** che non è possibile eseguire la migrazione da un cluster gratuito a un cluster a pagamento. Il tipo di cluster gratuito offre un'archiviazione e una velocità effettiva delle transazioni limitate.
  - Per ulteriori informazioni sulle differenze tra i cluster Kubernetes gratuito e a pagamento su {{site.data.keyword.cloud_notm}}, vedi [Confronto tra i cluster standard e gratuito![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "Confronto tra i cluster standard e gratuito").  

   Devi ritornare a questa scheda nel tuo browser dopo che hai creato il cluster in modo da poter completare il processo di distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform 2.0.  
   {:important}  

  Devi attendere fino a che il tuo cluster non sarà stata distribuito correttamente. Fai quindi clic sul pulsante **Ho un cluster**.
3. La tua versione Kubernetes in esecuzione nel tuo cluster deve essere alla versione 1.11 o successiva. Attieniti alla seguente [procedura](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) per controllare la tua versione del cluster ed eseguire l'upgrade, se necessario. Torna quindi indietro e procedi con queste istruzioni.
4. Il passo **Distribuisci su cluster**. Seleziona il cluster Kubernetes in cui vuoi distribuire {{site.data.keyword.blockchainfull_notm}} Platform 2.0 dall'elenco a discesa e fai clic su **Distribuisci al cluster**.  

  Se il tuo cluster Kubernetes non è visibile nell'elenco a discesa, potrebbe essere a causa di una delle seguenti condizioni:
  - Il completamento del processo di creazione del cluster può richiedere fino a 60 minuti. Se hai creato un cluster, prevedi del tempo supplementare finché lo stato del tuo cluster non diventa **Normale**.
  - I cluster che si trovano fuori dalla regione **Dallas** non sono visibili e non possono essere utilizzati.
  - Controlla per assicurati che non stai utilizzando la versione ESR di Firefox. In questo caso, passa a un altro browser, come ad esempio Chrome, e riprova.

5. Il passo **Avvia console**. Dopo che il tuo {{site.data.keyword.blockchainfull_notm}} Platform 2.0 è stato distribuito correttamente, fai clic su **Avvia {{site.data.keyword.blockchainfull_notm}} Platform** per aprire la console {{site.data.keyword.blockchainfull_notm}} Platform. Ci potrebbe volere qualche minuto perché il pulsante diventi abilitato mentre viene eseguito il provisioning della console.

## (Facoltativo) Aggiungi ulteriori utenti alla console
{: #ibp-v2-deploy-iks-add-users}

Per impostazione predefinita, la console utilizza [l'ID {{site.data.keyword.IBM_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/iam/index.html#iamoverview "IBM Cloud Identity and Access Management") come provider del servizio di identità. Il provisioning della tua console {{site.data.keyword.blockchainfull_notm}} Platform viene eseguito configurando l'indirizzo email del proprietario {{site.data.keyword.IBM_notm}} come amministratore della console. Come amministratore, questo utente è autorizzato a concedere ad altri utenti l'accesso alla console tramite i loro indirizzi email.  

### Come aggiungere un nuovo utente
{: #ibp-v2-deploy-iks-add-users-howto}

Fai clic sull'icona **Utenti** nella navigazione di sinistra per visualizzare l'elenco di utenti che possono eseguire l'accesso alla console. Se sei collegato come un amministratore della console, puoi fare clic su **Aggiungi nuovi utenti** per autorizzare i nuovi utenti alla console e concedere loro l'accesso di **Admin** o quello **General**. Gli utenti con il ruolo **Amministratore** può aggiungere o eliminare gli utenti.

## Passi successivi
{: #ibp-v2-deploy-iks-next-steps}

Ora che la tua console è pronta per l'utilizzo, puoi procedere con l'[Esercitazione di creazione di una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Aggiornamento della versione di Kubernetes del tuo cluster
{: #ibp-v2-deploy-iks-updating-kubernetes}

Se utilizzi un cluster del servizio {{site.data.keyword.cloud_notm}} Kubernetes esistente, assicurati che Kubernetes sia alla versione 1.11 o successiva.

Puoi controllare la versione di Kubernetes del tuo cluster nella [pagina dei cluster Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.cloud.ibm.com/containers-kubernetes/clusters) su {{site.data.keyword.cloud_notm}}, che elenca tutti i tuoi cluster in una tabella.

Se la versione di Kubernetes non è alla versione 1.11 o successiva, devi completare la seguente procedura per aggiornare la versione di Kubernetes del tuo cluster.

1. Fai clic sull'icona del menu di overflow alla fine della riga e seleziona **Aggiorna versione**. Il completamento di questo processo richiede circa un'ora. Se la versione viene aggiornata correttamente, puoi vedere la versione aggiornata del tuo cluster nella colonna **Versione Kubernetes**.  
2. Seleziona una versione di 1.11 o successiva dall'elenco a discesa delle versioni di Kubernetes e fai clic su **Aggiorna**.
3. Fai clic sul tuo cluster e vai alla scheda **Nodi di lavoro**. Seleziona la casella di spunta davanti al nodo di lavoro che desideri aggiornare e fai clic su **Aggiorna Kubernetes** dalla barra dei menu a comparsa. Se il tuo cluster contiene più nodi di lavoro, devono essere aggiornati tutti.

  Gli aggiornamenti ai nodi di lavoro possono causare tempi di inattività per applicazioni e servizi. Viene ricercata l'immagine della macchina del nodo di lavoro e i dati vengono eliminati se non sono [memorizzati esternamente al pod ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview).
  {:important}

![Aggiorna versione Kubernetes](../images/update_k8s_version.gif)

per ulteriori informazioni sull'aggiornamento della versione di Kubernetes per un cluster del servizio {{site.data.keyword.IBM_notm}} Kubernetes e i nodi di lavoro, vedi, [Aggiornamento dei cluster, dei nodi di lavoro e degli elementi aggiuntivi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/containers/cs_cluster_update.html#update).  

Devi attendere il completamento dell'aggiornamento prima di poter [riprendere la distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps).

## Eliminazione di un'istanza del servizio
{: #ibp-v2-deploy-iks-delete-service-instance}

Quando non hai più bisogno della tua istanza del servizio, la puoi eliminare dal tuo cluster Kubernetes per liberare risorse. Puoi utilizzare l'interfaccia utente IBM Cloud per eliminare un'istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta.

1. Passa al tuo [Dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/ "Dashboard").
2. Individua il tile **Riepilogo risorse** e fai clic su **Servizi**.
3. Nella forma a triangolo **Servizi**, individua l'istanza del servizio che vuoi eliminare e fai clic su **Elimina** nel menu Azioni.

Se l'eliminazione della tua istanza del servizio ha esito negativo, potrebbe essere perché il cluster Kubernetes non è accessibile. Se questo si verifica, apri un [ticket di supporto](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases) per farti eliminare l'istanza del servizio.

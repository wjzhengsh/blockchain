---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Distribuzione di una CA (Certificate Authority) su {{site.data.keyword.cloud_notm}} Private
{: #CA-deploy}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Dopo aver importato {{site.data.keyword.blockchainfull}} Platform sul grafico {{site.data.keyword.cloud_notm}} Private (ICP) Helm, puoi distribuire i componenti individuali. La CA (Certificate Authority) è la radice di attendibilità per la tua organizzazione e ti consente di generare le credenziali per altri componenti che vorrai distribuire. Di conseguenza, devi distribuire una CA prima di distribuire altri componenti. Ogni organizzazione in una rete blockchain a più cloud deve distribuire la propria CA.  Per ulteriori informazioni sulle CA e sul loro ruolo in una rete blockchain, consulta [Autorità di certificazione (CA, Certificate Authority)](/docs/services/blockchain/blockchain_component_overview.html#ca).
{:shortdesc}

Prima di distribuire una CA (Certificate Authority), esamina [Considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

## Risorse richieste
{: #ca-resources-required}

Assicurati che il tuo cluster ICP soddisfi i requisiti della risorsa di hardware minimi:

| Componente | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |

**Note:**
- Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti della CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in ICP. VPC è un'unità di misura per determinare il costo di licenza dei prodotti IBM. Per ulteriori informazioni sugli scenari per decidere VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Virtual Processor Core").
- Il requisito di archiviazione dei dati dipende da quante identità e certificati vengono archiviati. L'archiviazione della CA non è pesante come per l'archiviazione peer e ordinante, ma dipenderà dal caso di utilizzo. Più sono gli utenti e maggiore è l'archiviazione di cui si ha bisogno.
- Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un grande numero di transazioni, è importante assegnare una quantità sufficientemente grande di archiviazione alla tua CA. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se esaurisci l'archiviazione sulla tua CA, devi distribuire una nuova CA con un file system più grande e consentirgli di eseguire la sincronizzazione tramite le tue altre CA sugli stessi canali.

## Archiviazione
{: #storage}

Devi determinare l'archiviazione che verrà utilizzata dalla tua CA. Se utilizzi le impostazioni predefinite, il grafico Helm creerà una nuova attestazione di volume persistente (PVC) di 2 Gi con il nome di `fabric-ca-pvc` per la tua CA.

Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* `storageClass` durante l'installazione di ICP o che l'amministratore di sistema Kubernetes debba creare una nuova storageClass prima della tua distribuzione della CA.

Puoi scegliere di distribuire la CA sulle piattaforme AMD64 o S390X. Tuttavia, tieni presente che il [Provisioning dinamico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) è disponibile solo per i nodi AMD64 in ICP. Se il tuo cluster include una combinazione di nodi di lavoro S390X e AMD64, non è possibile utilizzare il provisioning dinamico.

Se non utilizzi il provisioning dinamico, devono essere creati e configurati dei [Volumi persistenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) con etichette che possono essere utilizzati per perfezionare il processo di bind della PVC Kubernetes.

## Prerequisiti per la distribuzione di una CA
{: #prerequisites-ca-icp}

1. Prima di poter installare una CA su ICP, devi [installare ICP](/docs/services/blockchain/ICP_setup.html) e [installare il grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html).

2. Se utilizzi la Community Edition e vuoi eseguire questo grafico Helm su un cluster ICP senza la connettività Internet, devi creare degli archivi su una macchina collegata a Internet prima di poter installare gli archivi sul tuo cluster ICP. Per ulteriori informazioni, consulta [Adding featured applications to clusters without Internet connectivity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Tieni presente che puoi trovare il file della specifica manifest.yaml in ibm-blockchain-platform-dev/ibm_cloud_pak nel grafico Helm.

3. Richiama il valore dell'indirizzo IP proxy del cluster dalla console ICP. **Nota:** dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Cluster administrator roles and actions") per accedere al tuo IP proxy. Accedi al cluster ICP. Nel pannello di navigazione di sinistra, fai clic su **Piattaforma** e quindi su **Nodi** per visualizzare i nodi definiti nel cluster. Fai clic sul nodo con il ruolo `proxy` e copia il valore dell'`IP host` dalla tabella. **Importante:** salva questo valore perché lo utilizzerai quando configuri il campo `Proxy IP` del grafico Helm.

4. Crea il nome utente e la password dell'amministratore della CA e archiviali all'interno di un oggetto segreto in ICP. Puoi trovare le istruzioni per creare il segreto nella [prossima sezione](#admin-secret).

## Creazione del segreto amministratore CA
{: #admin-secret}

Quando avvii la CA per la prima volta, contiene un'identità amministratore creata per utilizzare la tua CA. Devi creare un nome utente e una password per questa identità amministratore prima di distribuire la CA. È importante prendere nota di questi valori. Saranno utilizzati successivamente quando utilizzi la tua CA e distribuisci ulteriori componenti di rete. Crea un [Segreto Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/configuration/secret/ "Secrets") per archiviare il nome utente (`username`) e la `password` dell'amministratore per la tua distribuzione CA. Un segreto Kubernetes ti consente di proteggere e condividere le informazioni senza doverle passare direttamente alla distribuzione.

1. Crea un nome utente e una password amministratore e codifica i valori nel formato base64. Immetti il seguente comando in una finestra di terminale e sostituisci il valore di `admin` e `adminpw` con i valori che vuoi utilizzare.
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **Importante:** salva i valori di `admin` e `adminpw` creati in precedenza. Salva anche i valori codificati di questi campi, perché sono necessari quando crei l'oggetto segreto Kubernetes.

2. Accedi alla console ICP. Nel pannello di navigazione di sinistra, fai clic su **Configurazione** e quindi su **Segreti**. Fai clic sul pulsante **Crea segreto** per aprire un pannello a comparsa che ti consente di generare un nuovo oggetto segreto.

3. Nella scheda **Generale**, completa i seguenti campi:
  - **Nome:** fornisci al tuo segreto un nome univoco all'interno del tuo cluster. Utilizzerai questo nome per configurare la tua CA. Il nome deve essere tutto in minuscolo.
  - **Namespace:** lo spazio dei nomi per aggiungere il tuo segreto. Seleziona lo spazio dei nomi (`namespace`) a cui vuoi distribuire la tua CA.
  - **Tipo:** immetti il valore `Opaque`.

4. Lascia vuota la scheda **Annotazioni**.

5. Nella scheda **Dati**, aggiungi il nome utente e la password come coppie chiave/valore.
  1. Nel primo campo **Nome**, immetti `ca-admin-name`.
  2. Nel primo campo **Valore**, immetti il risultato di `echo -n admin | base64` dal precedente passo.
  3. Fai clic sul pulsante **Aggiungi dati** per aggiungere una seconda coppia chiave/valore.
  4. Nel secondo campo **Nome**, immetti `ca-admin-password`.
  5. Nel secondo campo **Valore**, immetti il risultato di `echo -n adminpw | base64` dal passo precedente.  
    La **Figura 1** mostra ciò che potrebbe contenere il segreto della console ICP in base ai valori che hai specificato per le chiavi `ca-admin-name` e `ca-admin-password`.

    ![Segreto console ICP](../images/CreateCASecret.png "Segreto console ICP")  
    *Figura 1. Segreto console ICP*
  6. Fai clic su **Crea** per creare un novo oggetto segreto.

Il grafico Helm CA ti richiede di utilizzare i nomi `ca-admin-name` e `ca-admin-password` come valori della chiave per distribuire il segreto alla release Helm.

**Nota:** il segreto di amministrazione della CA non viene rimosso dal tuo cluster ICP quando elimini la tua release Helm. Sei responsabile della gestione dei tuoi segreti nel tuo cluster ICP. Se intendi distribuire un'altra CA in futuro, puoi riutilizzare il segreto di amministrazione della CA. Altrimenti, sei responsabile della sua eliminazione dal tuo cluster ICP.

## Configurazione
{: #ca-configuration}

Dopo aver creato il tuo segreto di amministrazione della CA, puoi utilizzare le seguenti istruzioni per configurare e installare la tua CA. Puoi installare solo una CA alla volta.

1. Accedi alla console ICP e fai clic su **Catalogo** nell'angolo superiore destro.
2. Fai clic su `Blockchain` nel pannello di navigazione di sinistra per individuare il tile etichettato `ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` se hai scaricato la Community edition. Fai clic sul tile per aprirlo e per visualizzare un file Readme che include le informazioni sull'installazione e la configurazione del grafico Helm.
3. Fai clic sulla scheda **Configurazione** all'inizio del pannello oppure fai clic sul pulsante **Configura** nell'angolo in basso a destra.
4. Specifica i valori per i [Parametri di configurazione generali](#ca-global-parameters) e accetta il contratto di licenza.
5. Apri la forma a triangolo `Tutti i parametri` e specifica il valore per i [Parametri di configurazione globali](#ca-global-parameters).
6. Scorri fino alla sezione **Configurazione CA**. Seleziona la casella di spunta `Installa CA` e completa le [impostazioni di configurazione](#ca-parameters) per la CA.  
7. Fai clic su **Installa**.

### Parametri di configurazione
{: #icp-ca-configuration-parms}

La seguente tabella elenca i parametri configurabili di {{site.data.keyword.blockchainfull_notm}} Platform, **specifici per il componente CA** e i relativi valori predefiniti.  

**Anche se l'IU del grafico Helm indica che non è necessaria dell'altra configurazione, devi immettere alcuni parametri per distribuire una CA.**

#### Parametri di configurazione generali e globali
{: #ca-global-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
| `Helm release name`| Il nome della tua release Helm. Deve iniziare con una lettera minuscola e terminare con un qualsiasi carattere alfanumerico, deve contenere solo trattini e caratteri alfanumerici minuscoli. Devi utilizzare un nome della release Helm univoco ogni volta che tenti di installare un componente. | nessuno | sì |
| `Target namespace`| Scegli lo spazio dei nomi Kubernetes per installare il grafico Helm. | nessuno | sì |
|**Configurazione globale**| Parametri che si applicano a tutti i componenti nel grafico Helm.|||
| `Service account name`| Immetti il nome dell'[account del servizio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Configure Service Accounts for Pods") che utilizzerai per eseguire il pod. | valore predefinito | no |

#### Parametri di configurazione CA
{: #ca-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
| `Install CA`| Seleziona per installare una CA. | non selezionato | sì, se vuoi distribuire una CA |
| `CA name`| Specifica un nome da utilizzare per la CA (Certificate Authority). **Importante:** prendi nota di questo valore. È necessario successivamente, quando configuri un ordinante o un peer. | SampleOrgCA | sì |
| `CA worker node architecture`| Seleziona la tua architettura del nodo di lavoro ICP (ADM64 o S390X). | AMD64 | sì|
| `CA database type`| Il tipo di database per archiviare i dati della CA. È supportato solo SQLite. | SQLite | sì |
| `CA data persistence enabled` | Se selezionato, i dati saranno disponibili al riavvio del contenitore. Altrimenti, tutti i dati andranno persi in caso di failover o di riavvio del pod. | verificato | no |
| `CA use dynamic provisioning` | Seleziona per abilitare il provisioning dinamico dei tuoi volumi di archiviazione. | verificato | no |
| `CA storage class name`| Specifica un nome della classe di archiviazione univoco. Altrimenti, viene utilizzata la classe di archiviazione predefinita nel cluster. | nessuno | Dipende dal modo in cui viene configurato il cluster ICP. Verifica con il tuo amministratore del cluster |
| `CA existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `CA selector label`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | nessuno | no |
| `CA selector value`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | nessuno | no |
| `CA storage access mode`| Specifica la [modalità di accesso](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") dell'archiviazione per il PVC. | ReadWriteMany | sì |
| `CA volume claim size`| Scegli la dimensione del disco da utilizzare. | 2Gi | sì |
| `CA image repository`| Ubicazione del grafico Helm CA. | ibmcom/ibp-fabric-ca | sì |
| `CA Docker image tag`| Il valore della tag associata all'immagine CA. Questo campo viene automaticamente compilato con la versione dell'immagine. Non modificarlo. | 1.2.1 | sì |
| `CA service type` | Utilizzato per specificare se le [porte esterne devono essere esposte ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sul peer. Seleziona NodePort per esporre le porte esternamente (consigliato) e ClusterIP per non esporle. LoadBalancer e ExternalName non sono supportati in questa release | NodePort | sì |
| `CA secret (Required)`| Immetti il nome dell'oggetto segreto Kubernetes che hai creato per i tuoi `ca-admin-name` e `ca-admin-password`. | nessuno | sì |
| `CA CPU request`| Specifica il numero minimo di CPU da assegnare alla CA. | 1 | sì |
| `CA CPU limit`| Specifica il numero massimo di CPU da assegnare alla CA. | 2 | sì |
| `CA memory request`| Specifica la quantità minima di memoria da assegnare alla CA. | 1Gi | sì |
| `CA memory limit`| Specifica la quantità massima di memoria da assegnare alla CA. | 4 Gi | sì |
| `CA TLS instance name`| Specifica un nome dell'istanza TLS CA che sarà utilizzato per iscrivere un ordinante o un peer. | tlsca | sì |
| `CSR common name`| Specifica il nome comune (CN) che sarà presentato dal certificato root CA generato quando viene contattato. | tlsca-common | sì |
| `Proxy IP`| Immetti l'[IP del nodo proxy per il cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install_proxy.html "IBM Cloud Private installation behind an HTTP proxy") dove viene distribuita la CA. | 127.0.0.1 | sì |


### Utilizzo della riga di comando Helm per installare la release Helm
{: #icp-helm-cli}

In alternativa, puoi utilizzare la CLI Helm per installare la release Helm. Prima di eseguire il comando `helm install`, assicurati di [aggiungere il repository Helm del tuo cluster all'ambiente CLI Helm ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

Puoi configurare i parametri necessari per l'installazione creando un file `yaml` e passandolo al seguente comando `helm install`.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

Dove:

- `<helm_release name>` rappresenta il nome che vuoi dare alla tua release Helm.
- `<helm_chart>` rappresenta il nome del grafico Helm importato nel catalogo.
- `<helm_chart_version>` rappresenta la versione del grafico Helm importato nel catalogo.
- `<customvalues.yaml>` è il nome del file yaml che contiene i parametri di configurazione.

Ad esempio:

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values ca-s390x-values.yaml \
--tls
```

Puoi creare un nuovo file `yaml` modificando il `values.yaml` incluso nel file di archivio scaricato. Il file `values.yaml` include tutti i parametri necessari con le rispettive impostazioni predefinite.

## Verifica dell'installazione della CA
{: #verifiying-ca-installation}

Una volta che hai terminato il tuo lavoro con i parametri di configurazione, fai clic sul pulsante **Installa** e sul pulsante **Visualizza release Helm** per visualizzare la tua distribuzione. Se l'azione è stata eseguita correttamente, dovresti vedere il valore 1 nei campi `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` nella tabella Distribuzione. Potresti dover fare clic su Aggiorna e attendere l'aggiornamento della tabella. Puoi anche trovare la tabella Distribuzione facendo clic sull'icona **Menu** nell'angolo in alto a sinistra nella console ICP. Dall'elenco di menu, fai clic su **Workload** e poi su **Release Helm**.

Se scorri in basso fino alla sezione `Note`, puoi trovare delle informazioni importanti che utilizzerai per [utilizzare la tua CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

Dopo aver installato la CA di {{site.data.keyword.blockchainfull_notm}} Platform in ICP, viene creata una mappa di configurazione con le impostazioni delle variabili di ambiente predefinite. Successivamente puoi modificare o aggiungere delle variabili di ambiente per il server CA per configurarne il comportamento. Per ulteriori informazioni sui parametri di configurazione del server CA, consulta la [documentazione del server CA ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-server "Fabric CA Server").

Dopo aver configurato la mappa di configurazione, devi riavviare il server CA prima che le modifiche diventino effettive. Per riavviare il server CA, puoi eliminare il POD del server CA Fabric. ICP creerà un nuovo POD che rispecchia le modifiche.

## Visualizzazione dei log CA
{: #ca-deploy-view-logs}

È possibile visualizzare i log del componente dalla riga di comando utilizzando i [`comandi della CLI kubectl`](/docs/services/blockchain/howto/CA_operate.html#ca-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster ICP. Per ulteriori informazioni, consulta queste [istruzioni per l'accesso ai log](/docs/services/blockchain/howto/CA_operate.html#ca-operate-view-logs).

## Gestione della tua CA 
{: #operate-ca}

La tua CA sarà la radice di attendibilità per la tua organizzazione. Devi utilizzare la tua CA per generare i certificati per i tuoi altri componenti.  Di conseguenza, prima di distribuire un ordinante o un peer, devi [configurare la tua CA e completare diversi passi operativi](/docs/services/blockchain/howto/CA_operate.html)

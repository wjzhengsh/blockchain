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

# Distribuzione dei peer in {{site.data.keyword.cloud_notm}} Private
{: #peer-icp}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Le seguenti istruzioni descrivono come distribuire un peer {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.cloud_notm}} Private (ICP). Queste istruzioni ti consentono di collegarti a {{site.data.keyword.blockchainfull_notm}} Platform on ICP. Se vuoi collegare un peer a una rete del piano Starter o Enterprise su {{site.data.keyword.cloud_notm}}, vedi [Distribuzione dei peer per il collegamento al piano Starter o Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html).
{:shortdesc}

Prima di distribuire un peer, rivedi [Considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

## Risorse richieste
{: #peer-resources-required}

Assicurati che il tuo sistema ICP soddisfi i requisiti della risorsa di hardware minimi:

| Componente | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| Peer | 2 | 2 GB | 50 GB con capacità di espansione |
| CouchDB for Peer | 2| 2 GB |50 GB con capacità di espansione |

 **Note:**
 - Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti della CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in ICP. VPC è un'unità di misura per determinare il costo di licenza dei prodotti IBM. Per ulteriori informazioni sugli scenari per decidere VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un grande volume di transazioni, è importante assegnare una quantità sufficientemente grande di archiviazione; ad esempio 250 GB per il tuo peer. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se stai per esaurire l'archiviazione sul tuo peer o ordinante, devi distribuire un nuovo peer o ordinante con un file system più grande e consentirgli di eseguire la sincronizzazione tramite i tuoi altri componenti sugli stessi canali.

## Archiviazione
{: #storage}

Devi determinare l'archiviazione che verrà utilizzata dal tuo peer. Se utilizzi le impostazioni predefinite, il grafico Helm creerà una nuova attestazione di volume persistente (PVC) di 8 Gi con il nome di `my-data-pvc` per i tuoi dati del peer e un'altra PVC di 8 Gi con il nome di `statedb-pvc` per il tuo database dello stato.

Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* `storageClass` durante l'installazione di ICP o che l'amministratore di sistema Kubernetes debba creare una nuova storageClass prima della tua distribuzione.

Puoi scegliere di distribuire il peer sulle piattaforme amd64 o s390x. Tuttavia, tieni presente che il [Provisioning dinamico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) è disponibile solo per i nodi amd64 in ICP. Se il tuo cluster include una combinazione di nodi di lavoro s390x e amd64, non è possibile utilizzare il provisioning dinamico.

Se non utilizzi il provisioning dinamico, devono essere creati e configurati dei [Volumi persistenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) con etichette che possono essere utilizzati per perfezionare il processo di bind della PVC Kubernetes.

## Prerequisiti per la distribuzione di un peer
{: #prerequisites-peer-icp}

1. Prima di poter installare un peer su ICP, devi [installare ICP](/docs/services/blockchain/ICP_setup.html) e [installare il grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html).

2. Se utilizzi la Community Edition e vuoi eseguire questo grafico Helm su un cluster ICP senza la connettività Internet, devi creare degli archivi su una macchina collegata a Internet prima di poter installare gli archivi sul tuo cluster ICP. Per ulteriori informazioni, consulta [Adding featured applications to clusters without Internet connectivity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Tieni presente che puoi trovare il file della specifica `manifest.yaml` in `ibm-blockchain-platform-dev/ibm_cloud_pak` nel grafico Helm.

3. Devi prima [distribuire una CA](/docs/services/blockchain/howto/CA_deploy_icp.html) su ICP. Devi utilizzare la CA per creare un [file di configurazione del peer e archivialo come un segreto Kubernetes in ICP](#peer-config-file).

4. Richiama il valore dell'indirizzo IP proxy del cluster della tua CA dalla console ICP. **Nota:** dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Cluster administrator roles and actions") per accedere al tuo IP proxy. Accedi al cluster ICP. Nel pannello di navigazione di sinistra, fai clic su **Piattaforma** e quindi su **Nodi** per visualizzare i nodi definiti nel cluster. Fai clic sul nodo con il ruolo `proxy` e copia il valore dell'`IP host` dalla tabella. **Importante:** salva questo valore perché lo utilizzerai quando configuri il campo `Proxy IP` del grafico Helm.


## Creazione del segreto di configurazione del peer
{: #peer-config-file}

Per distribuire un peer, devi creare un file di configurazione che contiene le informazioni importanti sull'identità del peer e la tua CA (Certificate Authority). Successivamente, devi passare questo file al grafico Helm durante la configurazione utilizzando un oggetto [segreto Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/configuration/secret/). Questo file consentirà al tuo peer di ottenere i certificati di cui ha bisogno dalla CA (Certificate Authority) per aderire a una rete blockchain. Contiene inoltre un certificato di gestione che ti consentirà di utilizzare il tuo peer. Attieniti alle istruzioni per l'[utilizzo della CA per distribuire un ordinante o un peer](/docs/services/blockchain/howto/CA_operate.html#deploy-orderer-peer) prima di configurare il peer.

Devi fornire i nomi host CSR al file di configurazione. È incluso il nome host del servizio (`service host name`) che sarà lo stesso valore del nome della release Helm (`helm release name`) che specifichi durante la distribuzione. Ad esempio, se specifichi un nome della release Helm (`helm release name`) di `org1peer1`, ricorda di inserire il seguente valore nella sezione `"csr"` del file:
```
"csr": {
  "hosts": [
     "9.42.134.44",
      "org1peer1"
    ]
}
```
{:codeblock}

Dopo aver salvato il file di configurazione, devi codificarlo nel formato base64 prima di fornirlo a ICP come un oggetto segreto. Un segreto Kubernetes ti consente di proteggere e condividere le informazioni senza doverle passare direttamente alla distribuzione.

1. Per codificare il file di configurazione nel formato base64, immetti i seguenti comandi da una finestra terminale:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
```
{:codeblock}

**Nota:** è importante che la stringa generata dal precedente comando sia formattata come una singola riga. Dovrebbe essere simile alla seguente:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```

Ma non simile a questa:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
```

Salva l'output risultante per il seguente passo 4.

2. Accedi alla console ICP. Nel pannello di navigazione di sinistra, fai clic su **Configurazione** e quindi su **Segreti**. Fai clic sul pulsante **Crea segreto** per aprire un pannello a comparsa che ti consente di generare un nuovo oggetto segreto.

3. Nella scheda **Generale**, completa i seguenti campi:
  - **Nome:** fornisci al tuo segreto un nome univoco all'interno del tuo cluster. Utilizzerai questo nome quando distribuisci il tuo peer. Il nome deve essere tutto in minuscolo.  
  **Nota:** quando distribuisci un peer, viene automaticamente generato un nuovo segreto dalla distribuzione con il nome di `<helm_release_name>-secret`. Pertanto, quando dai un nome al tuo segreto, assicurati che sia diverso da `<helm_release_name>-secret` . Altrimenti, la distribuzione del grafico Helm avrà esito negativo perché il segreto che tenta di creare esiste già.
  - **Namespace:** lo spazio dei nomi per aggiungere il tuo segreto. Seleziona lo spazio dei nomi (`namespace`) a cui vuoi distribuire il tuo peer.
  - **Tipo:** immetti il valore `Opaque`.

4. Fai clic sulla scheda **Dati** nel pannello di navigazione di sinistra di questa finestra.
  - Nei campi `Name`, specifica `secret.json` e nel campo del valore incolla la stringa codificata `base64` del tuo file di configurazione.

5. (Facoltativo) Se pensi di utilizzare `CouchDB` come tuo database dello stato, devi aggiungere due ulteriori valori a questo oggetto segreto. Nella scheda **Dati**, fai clic sul pulsante **Aggiungi dati** per aggiungere l'ID utente e la password CouchDB da utilizzare per il database quando viene distribuito il contenitore.
  1. Crea il tuo nome utente e la tua password e codifica i valori nel formato base64. Immetti il seguente comando in una finestra di terminale e sostituisci `admin` e `adminpw` con i valori che vuoi utilizzare.
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. Nel campo **Nome**, immetti il valore `couchdbuser`. Nel campo **Valore** corrispondente, immetti `echo -n 'couch' | base64 $FLAG` dal passo precedente.
  3. Fai clic sul pulsante **Aggiungi dati** per aggiungere una seconda coppia chiave/valore.
  4. Nel secondo campo **Nome**, immetti il valore `couchdbpwd`. Nel campo **Valore** corrispondente, immetti `echo -n 'couchpw' | base64 $FLAG` dal passo precedente.

6. Fai clic su **Crea** per salvare il tuo oggetto segreto.

**Nota:** il segreto di configurazione del peer non sarà rimosso dal tuo cluster ICP quando elimini la tua release Helm. Se elimini il tuo peer, devi eliminare anche questo segreto.

## Configurazione
{: #peer-configuration}

Dopo aver creato il tuo oggetto segreto di configurazione del peer, puoi configurare e installare il tuo peer in ICP con la seguente procedura. Puoi installare solo un peer alla volta.


1. Accedi alla console ICP e fai clic su **Catalogo** nell'angolo superiore destro.
2. Fai clic su `Blockchain` nel pannello di navigazione di sinistra per individuare il tile etichettato `ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` se hai scaricato la Community edition. Fai clic sul tile per aprirlo e per visualizzare un file Readme che include le informazioni sull'installazione e la configurazione del grafico Helm.
3. Fai clic sulla scheda **Configurazione** all'inizio del pannello oppure fai clic sul pulsante **Configura** nell'angolo in basso a destra.
4. Specifica i valori per i [Parametri di configurazione generali](#peer-global-parameters) e accetta il contratto di licenza.
5. Apri la forma a triangolo `Tutti i parametri` e specifica il valore per i [Parametri di configurazione globali](#peer-global-parameters).
6. Scorri fino alla sezione **Configurazione del peer**. Seleziona la casella di spunta `Installa peer` e completa le [impostazioni di configurazione](#peer-parameters) per il peer.
7. Fai clic su **Installa**.

### Parametri di configurazione
{: #icp-peer-configuration-parms}

La seguente tabella elenca i parametri configurabili di {{site.data.keyword.blockchainfull_notm}} Platform, **specifici per il componente peer** e i relativi valori predefiniti. Se stai facendo degli esperimenti con il peer o lo stai iniziando ad utilizzare per la prima volta, utilizza i valori predefiniti dei parametri di archiviazione e database. Scorri fino alla sezione del componente peer per selezionare la casella di spunta `Installa peer` e fornisci le informazioni di configurazione associate solo a tale componente. **Anche se l'IU del grafico Helm indica che non è necessaria dell'altra configurazione, devi immettere alcuni parametri per distribuire un peer.**

#### Parametri di configurazione generali e globali
{: #peer-global-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
| `Helm release name`| Il nome della tua release Helm. Deve iniziare con una lettera minuscola e terminare con un qualsiasi carattere alfanumerico, deve contenere solo trattini e caratteri alfanumerici minuscoli. Devi utilizzare un nome della release Helm univoco ogni volta che tenti di installare un componente. **Importante:** questo valore deve corrispondere a quello utilizzato per generare il 'nome host del servizio' per il campo "hosts" nel tuo [file del segreto JSON.](#peer-config-file) | nessuno | sì |
| `Target namespace`| Scegli lo spazio dei nomi Kubernetes per installare il grafico Helm. | nessuno | sì |
|**Configurazione globale**| Parametri che si applicano a tutti i componenti nel grafico Helm.|||
| `Service account name`| Immetti il nome dell'[account del servizio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) che utilizzerai per eseguire il pod. | valore predefinito | no |

#### Parametri di configurazione del peer
{: #peer-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
|**Cluster configuration** |**Informazioni sulla configurazione del cluster** | ||
| `Install Peer` | Seleziona per installare un peer.|non selezionato | sì, se vuoi distribuire un peer |
| `Peer worker node architecture`| Seleziona l'architettura della tua piattaforma cloud (AMD64 o S390x)| AMD64 | sì |
| `Peer image repository`| Ubicazione del grafico Helm del peer. Questo campo viene automaticamente compilato con il percorso installato. | ibmcom/ibp-fabric-peer | sì |
| `Peer Docker image tag`| Il valore della tag associata all'immagine peer. |1.2.1, compilato automaticamente sul valore corretto.|sì|
| `Peer configuration`| Puoi personalizzare la configurazione del peer incollando il tuo file di configurazione `core.yaml` in questo campo. Per vedere un file `core.yaml` di esempio, consulta [`core.yaml` sample config ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **Solo per utenti esperti**. | nessuno | no |
| `Peer configuration secret (Required)`|Il nome del [segreto di configurazione del peer](#peer-config-secret) che hai creato in ICP. | nessuno | sì |
|`Organization MSP (Required)`| Puoi creare un nuovo valore MSPID dell'organizzazione come ad esempio 'org1' o specificare una MSP dell'organizazione esistente di cui farà parte il peer. Se hai distribuito un'organizzazione ordinante, assicurati che tutti gli MSPID del peer siano diversi dall'MSPID dell'ordinante. Inoltre, prendi nota di questo valore, perché ne avrai in seguito bisogno per `CORE_PEER_LOCALMSPID` e `configtx.yaml`. | nessuno | sì |
|`Peer service type`| Utilizzato per specificare se le [porte esterne devono essere esposte ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sul peer. Seleziona NodePort per esporre le porte esternamente (consigliato) e ClusterIP per non esporle. LoadBalancer e ExternalName non sono supportati in questa release. | NodePort | sì |
| `State database`| Il [database dello stato](/docs/services/blockchain/glossary.html#state-database) utilizzato per memorizzare il tuo libro mastro del canale. Il peer deve utilizzare lo stesso database della tua [rete blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | nessuno | sì |
|`CouchDB image repository`| Si applica solo se CouchDB è stato selezionato come database libro mastro. Questo campo viene automaticamente compilato con il percorso installato. Se stai utilizzando la Community Edition e non hai accesso a Internet, dovrebbe corrispondere alla directory in cui hai scaricato l'immagine CouchDB Fabric.| ibmcom/ibp-fabric-couchdb | sì |
| `CouchDB Docker image tag`| Si applica solo se CouchDB è stato selezionato come database libro mastro. Il valore della tag associata all'immagine CouchDB. | Compilato automaticamente sul valore corretto. | sì |
| `Peer Data persistence enabled`| Abilita la capacità di rendere persistenti i dati dopo il riavvio o un errore del cluster. Vedi [archiviazione in Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/ "Volumi") per ulteriori informazioni. *Se non selezionato, tutti i dati andranno persi in caso di failover o di riavvio del pod.* | verificato | no |
| `Peer use dynamic provisioning`| Seleziona per abilitare il provisioning dinamico dei tuoi volumi di archiviazione. | verificato | no |
| `Peer persistent volume claim`| Solo per la nuova attestazione. Immetti un nome per la tua nuova Attestazione di volume persistente (PVC) da creare. | my-data-pvc | no |
| `Peer storage class name`| Specifica un nome della classe di archiviazione per il peer. | Lascia vuoto se si crea una nuova PVC; altrimenti, specifica la classe di archiviazione associata alla PVC esistente. | no |
| `Peer existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `Peer selector label`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC.| nessuno | no |
| `Peer selector value`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | nessuno | no |
| `Peer storage access mode`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modalità di accesso") all'archiviazione per la PVC.  | ReadWriteMany| no |
| `Peer volume claim size`| Dimensione dell'attestazione di volume, deve essere maggiore di 2 Gi. | 8 Gi  | sì |
| `State database persistent volume claim`| Solo per la nuova attestazione. Immetti un nome per la tua nuova Attestazione di volume persistente (PVC) da creare. | statedb-pvc | no |
| `State database storage class name`| Specifica un nome della classe di archiviazione per il database dello stato. | nessuno | no |
| `State database existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `State database selector label`| [Etichetta del selettore](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) per la tua PVC| nessuno | no |
| `State database selector value`| [Valore del selettore](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) per la tua PVC | nessuno | no |
| `State database storage access mode`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modalità di accesso") all'archiviazione per la PVC. | ReadWriteMany| no |
| `State database volume claim size`| Scegli la dimensione del disco da utilizzare. | 8 Gi | sì |
| `CouchDB - Data persistence enabled`| Per il contenitore CouchDB, i dati del libro mastro saranno disponibili al riavvio del contenitore. *Se non selezionato, tutti i dati andranno persi in caso di failover o di riavvio del pod.*| verificato | no |
| `CouchDB - Use dynamic provisioning`| Per il contenitore CouchDB utilizza l'archiviazione dinamica di Kubernetes.| verificato | no |
| `Peer CPU request` | Numero minimo di CPU da assegnare al peer. | 1 | sì |
| `Peer CPU limit` | Numero massimo di CPU da assegnare al peer.| 2 | sì |
| `Peer Memory request` | Quantità minima di memoria da assegnare al peer. | 1Gi | sì |
| `Peer Memory limit` | Quantità massima di memoria da assegnare al peer. | 4 Gi | sì |
| `CouchDB CPU request` | Numero minimo di CPU da assegnare a CouchDB.| 1 | sì |
| `CouchDB CPU limit` | Numero massimo di CPU da assegnare a CouchDB. | 2 | sì |
| `CouchDB Memory request` | Quantità minima di memoria da assegnare a CouchDB.| 1Gi | sì |
| `CouchDB Memory limit` | Quantità massima di memoria da assegnare a CouchDB. | 4 Gi | sì |


Quando si seleziona CouchDB come database peer, nel pod vengono creati due contenitori, uno per il peer e uno per CouchDB.
Il contenitore peer include un singolo montaggio del volume sulla PVC del peer che memorizza i blocchi e le transazioni sul file system. Il contenitore CouchDB monta la PVC del database dello stato del peer che contiene i dati del libro mastro.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Utilizzo della riga di comando Helm per installare la release Helm
{: #icp-helm-cli}

In alternativa, puoi utilizzare la CLI `helm` per installare la release Helm. Prima di eseguire il comando `helm install`, assicurati di [aggiungere il repository Helm del tuo cluster all'ambiente CLI Helm ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

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
--values peer-s390x-values.yaml \
--tls
```

Puoi creare un nuovo file `yaml` modificando il `values.yaml` incluso nel file di archivio scaricato, che include tutti i parametri necessari con le rispettive impostazioni.

## Verifica l'installazione del peer
{: #verify-installation-icp}

Una volta che hai terminato il tuo lavoro con i parametri di configurazione, fai clic sul pulsante **Installa** e sul pulsante **Visualizza release Helm** per visualizzare la tua distribuzione. Se l'azione è stata eseguita correttamente, dovresti vedere il valore 1 nei campi `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` nella tabella Distribuzione. Potresti dover fare clic su Aggiorna e attendere l'aggiornamento della tabella. Puoi anche trovare la tabella Distribuzione facendo clic sull'icona **Menu** nell'angolo in alto a sinistra nella console ICP. Dall'elenco di menu, fai clic su **Workload** e poi su **Release Helm**.

Se scorri in basso fino alla sezione `Note`, sono presenti delle informazioni importanti che utilizzerai per [utilizzare il tuo peer](/docs/services/blockchain/howto/peer_operate_icp.html).

## Visualizzazione dei log del peer
{: #peer-deploy-view-logs}

È possibile visualizzare i log del peer utilizzando i [comandi della CLI kubectl](/docs/services/blockchain/howto/peer_operate_icp.html#peer-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"). Per ulteriori informazioni, consulta queste [istruzioni per l'accesso ai log](/docs/services/blockchain/howto/peer_operate_icp.html#peer-icp-view-logs).

## Operazioni successive

Dopo aver distribuito il peer, devi completare diversi passi operativi prima di poter inviare transazioni e leggere il libro mastro distribuito dalla rete blockchain. Per ulteriori informazioni, vedi [Utilizzo dei peer con una rete multi-cloud](/docs/services/blockchain/howto/peer_operate_icp.html).

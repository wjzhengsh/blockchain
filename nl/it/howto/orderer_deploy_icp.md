---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Distribuzione di un ordinante in {{site.data.keyword.cloud_notm}} Private
{: #icp-orderer-deploy}

Gli ordinanti autenticano i client, ordinano le transazioni ed eseguono il broadcast delle transazioni in una rete blockchain con il componente ordinante. Per ulteriori informazioni sugli ordinanti e sul loro ruolo in una rete blockchain, consulta [Panoramica sui componenti blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).
{:shortdesc}

Prima di distribuire un servizio ordini, rivedi [Considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Risorse richieste
{: #icp-orderer-deploy-resources-required}

Assicurati che il sistema {{site.data.keyword.cloud_notm}} Private soddisfi i requisiti di risorse hardware minimi:

| Componente | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| Ordinante | 2 | 512 MB | 100 GB con capacità di espansione |


 **Note:**
 - Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti di CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in {{site.data.keyword.cloud_notm}} Private. VPC è un'unità di misura per determinare il costo di licenza dei prodotti IBM. Per ulteriori informazioni sugli scenari per decidere il VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un grande volume di transazioni, è importante allocare una quantità sufficientemente grande di archiviazione; ad esempio 500 GB per il tuo servizio ordini. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se stai per esaurire l'archiviazione sul tuo ordinante, devi distribuire un nuovo ordinante con un file system più grande e consentirgli di eseguire la sincronizzazione tramite i tuoi altri componenti sugli stessi canali.

## Archiviazione
{: #icp-orderer-deploy-storage}

Devi determinare l'archiviazione che verrà utilizzata dal tuo ordinante. Se utilizzi le impostazioni predefinite, il grafico Helm creerà una nuova attestazione di volume persistente (PVC) di 8 Gi con il nome di `orderer-data` per i tuoi dati dell'ordinante.

Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* `storageClass` durante l'installazione di {{site.data.keyword.cloud_notm}} Private, altrimenti l'amministratore di sistema Kubernetes deve creare una nuova storageClass prima della tua distribuzione.

Puoi scegliere di distribuire l'ordinante sulle piattaforme amd64 o s390x. Tuttavia, tieni presente che [il provisioning dinamico ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Provisioning del volume dinamico") è disponibile solo per i nodi AMD64 in {{site.data.keyword.cloud_notm}} Private. Se il tuo cluster include una combinazione di nodi di lavoro s390x e amd64, non è possibile utilizzare il provisioning dinamico.

Se non utilizzi il provisioning dinamico, [i volumi persistenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumi persistenti") devono essere creati e configurati con etichette che possono essere utilizzate per perfezionare il processo di bind della PVC Kubernetes.


## Prerequisiti per la distribuzione di un ordinante
{: #icp-orderer-deploy-prerequisites}

1. Prima di poter installare un ordinante su {{site.data.keyword.cloud_notm}} Private, devi [installare {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) e [installare il grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Se utilizzi la Community Edition e vuoi eseguire questo grafico Helm su un cluster {{site.data.keyword.cloud_notm}} Private senza la connettività Internet, devi creare degli archivi su una macchina connessa a Internet prima di poter installare gli archivi sul tuo cluster {{site.data.keyword.cloud_notm}} Private. Per ulteriori informazioni, consulta [Adding featured applications to clusters without Internet connectivity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Tieni presente che puoi trovare il file della specifica `manifest.yaml` in ibm-blockchain-platform-dev/ibm_cloud_pak nel grafico Helm.

3. Richiama il valore dell'indirizzo IP proxy del cluster della tua CA dalla console {{site.data.keyword.cloud_notm}} Private. **Nota:** dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Cluster administrator roles and actions") per accedere al tuo IP proxy. Accedi al cluster {{site.data.keyword.cloud_notm}} Private. Nel pannello di navigazione di sinistra, fai clic su **Piattaforma** e quindi su **Nodi** per visualizzare i nodi definiti nel cluster. Fai clic sul nodo con il ruolo `proxy` e copia il valore dell'`IP host` dalla tabella. **Importante:** salva questo valore perché lo utilizzerai quando configuri il campo `Proxy IP` del grafico Helm.

4. Crea un [file di configurazione dell'ordinante e archivialo come un segreto Kubernetes in {{site.data.keyword.cloud_notm}} Private.](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file).

## Creazione di un file di configurazione dell'ordinante
{: #icp-orderer-deploy-config-file}

Prima di distribuire un ordinante, devi creare un file di configurazione che contiene le informazioni importanti sull'identità dell'ordinante e la tua CA. Successivamente, devi passare questo file al grafico Helm durante la configurazione utilizzando un oggetto [segreto Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/configuration/secret/). Questo file consentirà all'ordinante di ottenere i certificati di cui ha bisogno dalla CA per aderire a una rete blockchain. Contiene inoltre un certificato di amministrazione che ti consentirà di utilizzare l'ordinante come un utente amministratore. Attieniti alle istruzioni sull'[utilizzo della CA per distribuire un ordinante o un peer](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer) prima di configurare l'ordinante.

Devi fornire i nomi host CSR al file di configurazione. I nomi host CSR includono l'indirizzo IP proxy del cluster in cui distribuirai il componente nonché il nome host del servizio che sarà il tuo nome host del grafico Helm. Il nome host del servizio (`service host name`) si basa sul nome della release Helm (`helm release name`) che specifichi durante la distribuzione. Il nome host del servizio (`service host name`) è il nome della release Helm (`helm_release_name`) che specifichi con la stringa `-orderer` aggiunta alla fine. Ad esempio, se specifichi un nome della release Helm (`helm release name`) di `orderer1`, puoi inserire il seguente valore nella sezione `"csr"` del file:

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

Dopo aver salvato il file di configurazione, devi codificarlo nel formato base64 prima di fornire il file a {{site.data.keyword.cloud_notm}} Private come un oggetto segreto. Un segreto Kubernetes ti consente di proteggere e condividere le informazioni senza doverle passare direttamente alla distribuzione.

1. Codifica il file di configurazione nel formato base64 immettendo i seguenti comandi da una finestra terminale:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** è importante che la stringa generata utilizzando il precedente comando sia formattata come una singola riga. Dovrebbe essere simile alla seguente:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   non simile a questa:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Salva l'output risultante per il seguente passo 4.

2. Accedi alla console {{site.data.keyword.cloud_notm}} Private. Nel pannello di navigazione di sinistra, fai clic su **Configurazione** e quindi su **Segreti**. Fai clic sul pulsante **Crea segreto** per aprire un pannello a comparsa che ti consente di generare un nuovo oggetto segreto.

3. Nella scheda **Generale**, completa i seguenti campi:
  - **Nome:** fornisci al tuo segreto un nome univoco all'interno del tuo cluster. Utilizzerai questo nome quando distribuisci il tuo ordinante. Il nome deve essere tutto in minuscolo.  
  **Nota:** quando distribuisci un ordinante, viene automaticamente generato un nuovo segreto dalla distribuzione con il nome `<helm_release_name>-orderer-mspsecret`. Pertanto, quando denomini il tuo segreto, assicurati che sia diverso da `<helm_release_name>-orderer-mspsecret`. Altrimenti, la distribuzione del grafico Helm avrà esito negativo perché il segreto che tenta di creare esiste già.
  - **Spazio dei nomi** lo spazio dei nomi per aggiungere il tuo segreto. Seleziona lo spazio dei nomi (`namespace`) a cui vuoi distribuire il tuo ordinante.
  - **Tipo** immetti il valore `Opaque`.

4. Fai clic sulla scheda **Dati** nel pannello di navigazione di sinistra di questa finestra. Nei campi `Nome`, specifica `secret.json` e nel campo del valore incolla la stringa codificata `base64` del tuo file di configurazione.

5. Fai clic su **Crea** per salvare il tuo oggetto segreto.

**Nota:** il segreto di configurazione dell'ordinante non sarà rimosso dal tuo cluster {{site.data.keyword.cloud_notm}} Private quando elimini la tua release Helm. Se elimini il tuo ordinante, devi eliminare anche questo segreto.

## Configurazione
{: #icp-orderer-deploy-configuration}


Dopo aver creato il tuo oggetto segreto di configurazione dell'ordinante, puoi configurare e installare il tuo ordinante in {{site.data.keyword.cloud_notm}} Private con la seguente procedura. Puoi installare solo un ordinante alla volta.

1. Accedi alla console {{site.data.keyword.cloud_notm}} Private e fai clic su **Catalogo** nell'angolo superiore destro.
2. Fai clic su `Blockchain` nel pannello di navigazione di sinistra per individuare il tile etichettato `ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` se hai scaricato la Community edition. Fai clic sul tile per aprirlo e per visualizzare un file Readme che include le informazioni sull'installazione e la configurazione del grafico Helm.
3. Fai clic sulla scheda **Configurazione** all'inizio del pannello oppure fai clic sul pulsante **Configura** nell'angolo in basso a destra.
4. Specifica i valori per i [Parametri di configurazione generali](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-configuration-parms) e accetta il contratto di licenza.
5. Apri la forma a triangolo `Tutti i parametri` e specifica il valore per i [Parametri di configurazione globali](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-global-parameters).
6. Scorri fino alla sezione **Configurazione dell'ordinante**. Seleziona la casella di spunta `Installa ordinante` e completa le [impostazioni di configurazione](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-parameters) per l'ordinante.
7. Fai clic su **Installa**.


**Nota:** quando distribuisci l'ordinante a s390x, il contenitore iniziale potrebbe restituire il seguente errore:

`panic: Error while trying to open DB: no locks available`

Questo è dovuto al fatto che l'ordinante utilizza un DB file flat e il file system NFS ha bisogno di un ulteriore pacchetto per permettere al servizio ordini di eseguire delle query dei file per verificare se sono presenti dei blocchi esclusi su di essi e per la creazione di blocchi esclusivi. Per correggere il problema, devi abilitare il pacchetto `rpc-statd`.

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

Questo comando deve essere eseguito sul sistema da cui viene fornito il file system NFS.

### Parametri di configurazione
{: #icp-orderer-configuration-parms}

La seguente tabella elenca i parametri configurabili di {{site.data.keyword.blockchainfull_notm}} Platform, **specifici per il componente ordinante** e i relativi valori predefiniti. **Anche se l'IU del grafico Helm indica che non è necessaria dell'altra configurazione, devi immettere alcuni parametri per distribuire un ordinante.**

#### Parametri di configurazione generali e globali
{: #icp-orderer-deploy-global-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
|**Parametri generali**| Parametri che configurano il grafico Helm | | |
| `Helm release name`| Il nome della tua release di helm. Deve iniziare con una lettera minuscola e terminare con un qualsiasi carattere alfanumerico, deve contenere solo trattini e caratteri alfanumerici minuscoli. Devi utilizzare un nome della release Helm univoco ogni volta che tenti di installare un componente. **Importante:** questo valore deve corrispondere a quello utilizzato per generare il 'nome host del servizio' per il campo "hosts" nel tuo [file del segreto JSON.](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file) | nessuno | sì  |
| `Target namespace`| Scegli lo spazio dei nomi Kubernetes per installare il grafico Helm. | nessuno | sì |
| `Target namespace policies`| Visualizza le politiche di sicurezza del pod dello spazio dei nomi scelto, che devono includere una politica **`ibm-privileged-psp`**. Altrimenti, [associa una politica di sicurezza del pod](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp) al tuo spazio dei nomi. | nessuno | no |
|**Configurazione globale**| Parametri che si applicano a tutti i componenti nel grafico Helm.|||
| `Service account name`| Immetti il nome dell'[account del servizio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) che utilizzerai per eseguire il pod. | valore predefinito | no |

#### Parametri di configurazione dell'ordinante
{: #icp-orderer-deploy-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
| `Install Orderer`| Seleziona per installare un ordinante. | non selezionato | sì, se vuoi distribuire un ordinante |
| `Orderer worker node architecture`| Seleziona la tua architettura del nodo di lavoro {{site.data.keyword.cloud_notm}} Private (AMD64 o S390X). | Architettura rilevata automaticamente in base al tuo nodo master | sì |
| `Orderer configuration`| Puoi personalizzare la configurazione dell'ordinante incollando il tuo file di configurazione `orderer.yaml` in questo campo. Per vedere un file `orderer.yaml` di esempio, consulta [`orderer.yaml` sample config ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/orderer.yaml) **Solo per utenti esperti**. | nessuno | no |
| `Organization MSP secret (Required)`| Specifica il nome dell'oggetto segreto che contiene le chiavi e i certificati MSP dell'organizzazione. | nessuno | sì |
| `Orderer data persistence enabled` | I dati saranno disponibili al riavvio del contenitore. Se non selezionato, tutti i dati andranno persi in caso di failover o di riavvio del pod. | verificato | no |
| `Orderer use dynamic provisioning` | Seleziona per abilitare il provisioning dinamico dei tuoi volumi di archiviazione. | verificato | no |
| `Orderer image repository` | Ubicazione del grafico Helm dell'ordinante. Questo campo viene automaticamente compilato con il percorso installato. Se stai utilizzando la Community Edition e non hai accesso a Internet, modifica questo campo con l'ubicazione in cui hai scaricato l'immagine dell'ordinante Fabric. | ibmcom/ibp-fabric-orderer | no |
| `Orderer Docker image tag`| Un record dell'immagine Docker. Questo campo viene automaticamente compilato con la versione dell'immagine. Non modificarlo.| 1.4.0 | sì |
| `Orderer consensus type`| Il tipo di consenso del servizio ordini. | SOLO | sì |
| `Orderer organization name`| Specifica il nome che vorresti utilizzare per l'organizzazione dell'ordinante. Se pensi anche di distribuire i peer, assicurati di utilizzare un nome diverso rispetto a quello che fornirai ai tuoi peer. Ad esempio, fornisci alla tua organizzazione dell'ordinante un nome come `ordererOrg` | nessuno | sì |
| `Orderer Org MSP ID`| Specifica il nome che vuoi utilizzare per l'ID MSP dell'organizzazione dell'ordinante. Deve essere lo stesso nome che dai alla tua organizzazione dell'ordinante e sarà impostato come una variabile di ambiente dal processo di distribuzione. Prendi nota di questo valore perché dovrai utilizzarlo successivamente. | nessuno | sì |
| `Orderer storage class name`| Specifica un nome della classe di archiviazione per l'ordinante. | nessuno | Dipende dal modo in cui viene configurato il cluster {{site.data.keyword.cloud_notm}} Private. Verifica con il tuo amministratore del cluster |
| `Orderer existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `Orderer selector label`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) per la tua PVC. | nessuno | no |
| `Orderer selector value`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) per la tua PVC. | nessuno | no |
| `Orderer storage access mode`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) all'archiviazione per la PVC. | ReadWriteMany | sì |
| `Orderer volume claim size`| Scegli la dimensione del disco da utilizzare, che deve essere almeno 2 Gi. | 8 Gi | sì |
| `Orderer service type` | Utilizzato per specificare se le [porte esterne devono essere esposte ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sul peer. Seleziona NodePort per esporre le porte esternamente (consigliato) e ClusterIP per non esporle. LoadBalancer e ExternalName non sono supportati in questa release | NodePort | sì |
| `Orderer CPU request`| Specifica il numero minimo di CPU da allocare all'ordinante. | 1 | sì |
| `Orderer CPU limit`| Specifica il numero massimo di CPU da allocare all'ordinante. | 2 | sì |
| `Orderer memory request`| Specifica la quantità minima di memoria da allocare all'ordinante. | 1Gi | sì |
| `Orderer memory limit`| Specifica la quantità massima di memoria da allocare all'ordinante. | 2Gi | sì |
| `gRPC web proxy CPU request`| Specifica il numero minimo di CPU in millicpu (m) da allocare al proxy web gRP. | 100 m | sì |
| `gRPC web proxy CPU limit`| Specifica il numero massimo di CPU in millicpu (m) da allocare al proxy web gRP. | 200 m | sì |
| `gRPC web proxy memory request`| Specifica la quantità minima di memoria da allocare al proxy web gRPC. | 100Mi | sì |
| `gRPC web proxy memory limit`| Specifica la quantità massima di memoria da allocare al proxy web gRPC. | 200Mi | sì |


### Utilizzo della riga di comando Helm per installare la release Helm
{: #icp-orderer-deploy-helm-cli}

In alternativa, puoi utilizzare la CLI Helm per installare la release Helm. Prima di eseguire il comando `helm install`, assicurati di [aggiungere il repository Helm del tuo cluster all'ambiente CLI Helm ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

Puoi configurare i parametri necessari per l'installazione creando un file `yaml` e passandolo al seguente comando `helm install`.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

dove:

- `<helm_release name>` rappresenta il nome che vuoi dare alla tua release Helm.
- `<helm_chart>` rappresenta il nome del grafico Helm importato nel catalogo.
- `<helm_chart_version>` rappresenta la versione del grafico Helm importato nel catalogo.
- `<customvalues.yaml>` è il nome del file yaml che contiene i parametri di configurazione.

Ad esempio:

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values orderer-s390x-values.yaml \
--tls
```

Puoi creare un nuovo file `yaml` modificando il `values.yaml` incluso nel file di archivio scaricato, che include tutti i parametri necessari con le rispettive impostazioni.

## Verifica dell'installazione dell'ordinante
{: #icp-orderer-deploy-verify-install}

Una volta che hai terminato il tuo lavoro con i parametri di configurazione, fai clic sul pulsante **Installa** e sul pulsante **Visualizza release Helm** per visualizzare la tua distribuzione. Se l'azione è stata eseguita correttamente, dovresti vedere il valore 1 nei campi `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` nella tabella Distribuzione. Potresti dover fare clic su Aggiorna e attendere l'aggiornamento della tabella. Puoi anche trovare la tabella Distribuzione facendo clic sull'icona **Menu** nell'angolo in alto a sinistra nella console {{site.data.keyword.cloud_notm}} Private. Dall'elenco di menu, fai clic su **Workload** e poi su **Release Helm**.

## Visualizzazione dei log dell'ordinante
{: #icp-orderer-deploy-view-logs}

È possibile visualizzare i log del componente dalla riga di comando utilizzando i [`comandi della CLI kubectl`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster {{site.data.keyword.cloud_notm}} Private. Per ulteriori informazioni, consulta queste [istruzioni per l'accesso ai log](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-view-logs).

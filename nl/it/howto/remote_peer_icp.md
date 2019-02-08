---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Distribuzione dei peer remoti in {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-icp}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Le seguenti istruzioni descrivono come installare un peer remoto di {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.cloud_notm}} Private (ICP) e connetterlo a una rete su {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

{{site.data.keyword.cloud_notm}} Private è una piattaforma applicativa per lo sviluppo e la gestione di applicazioni in contenitori in loco. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer on {{site.data.keyword.cloud_notm}} Private è un prodotto {{site.data.keyword.blockchainfull_notm}} che viene fornito con ICP per consentire ai clienti di distribuire i peer nel proprio ambiente locale. Dopo aver installato il grafico Helm del peer remoto, puoi trovarlo sotto forma di applicazione nel catalogo ICP. Il peer remoto per ICP sfrutta i servizi di archiviazione, sicurezza, registrazione e supporto di ICP per la gestione dei peer. Per ulteriori informazioni su ICP, consulta la [documentazione di {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private - documentazione").

Prima di installare i peer remoti su ICP, esamina le [considerazioni sui peer remoti](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Prerequisiti
{: #prerequisites-icp}

Prima di installare i peer remoti su ICP, è necessario completare le seguenti procedure su {{site.data.keyword.cloud_notm}} Private, sul tuo sistema locale e su {{site.data.keyword.blockchainfull_notm}} Platform.

1. Il tuo sistema locale

  Devi installare la CLI di {{site.data.keyword.cloud_notm}} Private [2.1.0.3 ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "Installazione della CLI {{site.data.keyword.cloud_notm}} Private").  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} Private

  Hai bisogno di un cluster [{{site.data.keyword.cloud_notm}} Private ![Icona link esterno](../images/external_link.svg "Icona link esterno")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v2.1.0.3 - documentazione") versione 2.1.0.3. Se stai utilizzando il peer remoto per lo sviluppo, il test o la sperimentazione, puoi installare [{{site.data.keyword.cloud_notm}} Private Community Edition ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "Installazione di {{site.data.keyword.cloud_notm}}e Private-CE") gratuitamente.

  **Note**:
    - Devi determinare l'archiviazione che verrà utilizzata dal tuo peer. Se utilizzi le impostazioni predefinite, il grafico Helm crea una nuova attestazione di volume persistente di 8 Gi con il nome di `my-data-pvc` per i dati del peer. Se selezioni CouchDB come tuo database libro mastro, il grafico Helm crea un'altra attestazione di volume persistente di 8 Gi con il nome di `statedb-pvc` per il tuo database libro mastro.
    - Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* storageClass durante l'installazione di ICP o che l'amministratore di sistema Kubernetes debba creare una nuova storageClass prima della tua distribuzione.
    - [Il provisioning dinamico ![Icona link esterno](../images/external_link.svg "Icona link esterno")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Provisioning del volume dinamico") è disponibile solo per i nodi amd64 in ICP. Pertanto, se il tuo cluster include una combinazione di nodi di lavoro s390x e amd64, non è possibile utilizzare il provisioning dinamico.
    - Se non utilizzi il provisioning dinamico, i [Volumi persistenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumi persistenti") devono essere creati e configurati con etichette che possono essere utilizzate per perfezionare il processo di bind dell'attestazione di volume persistente (o PVC, Persistent Volume Claim) di Kubernetes.

  Oltre ai requisiti hardware ICP, il peer remoto richiede almeno:
    - 2x vCPU
    - 2 GB di RAM
    - 4 GB di spazio per il chaincode
    - 10 GB di spazio per il libro mastro con possibilità di aumentare man mano che il libro mastro si espande

3. {{site.data.keyword.blockchainfull_notm}} Platform  

  Per utilizzare un peer remoto, devi disporre di un'organizzazione che sia un membro di una rete blockchain ospitata su IBM {{site.data.keyword.blockchainfull_notm}} Platform. Devi utilizzare il Monitoraggio della rete su {{site.data.keyword.cloud_notm}} per accedere alle credenziali di rete e agli endpoint API della tua rete. Se non sei membro di alcuna rete blockchain, devi creare o aderire a una rete. Per ulteriori informazioni, vedi [Creazione di una rete](/docs/services/blockchain/get_start.html#creating-a-network) o [Adesione a una rete](/docs/services/blockchain/get_start.html#joining-a-network).

## Importa il grafico Helm in ICP

Il pacchetto {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer è disponibile in [Github ![Icona link esterno](../images/external_link.svg "Icona link esterno")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer"). Il grafico Helm viene importato nella console ICP utilizzando la riga di comando. Dalla tua macchina locale, completa la seguente procedura per importare il grafico Helm:

1. Scarica il file del grafico Helm `.tgz` da [Github ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "Grafico Helm di IBM Blockchain Remote Peer").<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. Se non sei già connesso, accedi al tuo cluster ICP dalla CLI ICP e quindi accedi al registro delle immagini private Docker.  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  Nel codice, `<cluster_CA_domain>` è il dominio CA (Certificate Authority). Se non hai specificato un dominio CA, il valore
predefinito è `mycluster.icp`.

3. Dalla directory in cui hai memorizzato il grafico Helm scaricato, immetti il seguente comando nella CLI ICP per importare il grafico Helm nel tuo cluster ICP. Sostituisci `<helm_chart_from_github>` con il nome del file `.tgz` che hai scaricato da GitHub.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  Se questo comando viene completato correttamente, puoi visualizzare qualcosa di simile alle seguenti informazioni:

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  Il tile del grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer dovrebbe ora essere visibile nella pagina del catalogo ICP. Fai clic sul pulsante **Catalogo** nella console ICP per verificare che l'importazione sia stata eseguita correttamente.

### Distribuisci il peer remoto

La seguente procedura descrive il processo necessario per distribuire il peer remoto in ICP. Prima di configurare il peer remoto in ICP, richiama alcune informazioni sull'endpoint del tuo {{site.data.keyword.blockchainfull_notm}} Platform e crea un'identità utente per il peer remoto. Dovrai utilizzare queste informazioni nei passi successivi.

## Passo uno: Richiamo delle informazioni di configurazione del peer remoto
{: #network-endpoints}

Durante la configurazione, devi fornire gli endpoint API della tua rete al peer remoto. Questi endpoint consentono a un peer remoto di trovare e connettersi alla rete su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata **Panoramica** del tuo Monitoraggio della rete, fai clic sul pulsante **Configurazione peer remoto**.

![Configurazione peer remoto](../images/myresources_starter.png "Configurazione peer remoto")
*Figura 1. Configurazione peer remoto*

Si apre una finestra a comparsa che mostra i valori dei seguenti campi. Salva questi valori per quando configurerai il peer remoto.

  - **ID della rete**
  - **MSP organizzazione**
  - **Nome CA (Certificate Authority)**
  - **URL CA (Certificate Authority)**
  - **Certificato TLS CA (Certificate Authority)**

Puoi copiare e incollare singolarmente i campi o salvarli come file JSON facendo clic sul link **Scarica**. **Nota** che se scarichi le informazioni in JSON, devi convertire il certificato TLS in formato PEM prima di fornirlo al peer remoto. Puoi utilizzare la CLI per convertire il certificato in formato PEM immettendo il comando `echo -e "<CERT>" > catls.pem`.

## Passo due: Registrazione di un peer remoto
{: #register-peer}

Devi aggiungere una nuova identità peer alla tua organizzazione su {{site.data.keyword.blockchainfull_notm}} Platform prima che il peer remoto possa aderire alla rete. Completa la seguente procedura per iscrivere e registrare un peer remoto.

1. Accedi a {{site.data.keyword.blockchainfull_notm}} Platform e al tuo Monitoraggio della rete. Nella schermata "CA (Certificate Authority)", puoi visualizzare tutte le identità che sono state registrate con la tua organizzazione, come ad esempio l'amministratore o le applicazioni client.
  ![Schermata CA](../images/CA_screen_starter.png "Schermata CA")
  *Figura 2. Schermata CA*

2. Fai clic sul pulsante **Aggiungi utente** nella parte superiore della schermata. Si apre una schermata a comparsa che ti consente di registrare il tuo peer remoto dopo aver compilato i seguenti campi. Salva i valori dell'ID e del segreto poiché dovrai utilizzarli quando configuri il tuo peer.
  - **ID:** il nome utente del tuo peer, a cui si fa riferimento come `ID di registrazione` quando configuri il peer. **Salva questo valore** per usi futuri quando distribuisci il peer remoto.
  - **Segreto:** la password del tuo peer, a cui si fa riferimento come `segreto di registrazione` quando configuri il peer. **Salva questo valore** per usi futuri quando distribuisci il peer remoto.
  - **Tipo:** seleziona `peer` per questo campo.
  - **Affiliazione:** si tratta dell'affiliazione sotto la tua organizzazione, ad esempio `org1`, a cui apparterrà il tuo peer remoto. Seleziona un'affiliazione esistente dall'elenco a discesa o immettine una nuova.
  - **Registrazioni massime:** utilizza questo campo per limitare il numero di volte in cui puoi registrare o generare certificati utilizzando questa identità. Se non specificato, il valore predefinito è di registrazioni illimitate.

  Dopo aver completato questi campi, fai clic su **Invia** per registrare il peer remoto. Il peer registrato viene quindi elencato nella tabella come identità nella tua organizzazione.

## Passo tre: Configurazione e installazione di un peer remoto

Dopo aver registrato l'identità peer nel Monitoraggio della rete, puoi configurare e installare il peer remoto in ICP utilizzando le istruzioni della console ICP riportate di seguito.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. Accedi alla console ICP e fai clic su **Catalogo** nell'angolo superiore destro.
2. Individua il grafico Helm per {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer e fai clic su di esso per aprirlo. Puoi visualizzare un file readme che include informazioni sull'installazione e la configurazione del grafico Helm.
3. Fai clic su **Configura**. Nella pagina di configurazione, completa le impostazioni di configurazione per il grafico Helm e conferma di aver letto e accettato il contratto di licenza. 
4. Fai clic su **Installa**.

### Parametri di configurazione
{: #icp-configuration-parms}

Devi fornire le informazioni che servono al tuo peer remoto per connettersi alla rete su {{site.data.keyword.blockchainfull_notm}} Platform.
La seguente tabella elenca i parametri configurabili del grafico di {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer e i relativi valori predefiniti.


|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
|**Configurazione** | **Configura il nome della release e lo spazio dei nomi di destinazione per il tuo peer remoto** |  ||
| `Nome release`| Il nome della tua release di helm. | nessuno | sì |
| `Spazio dei nomi di destinazione`| Lo spazio dei nomi di Kubernetes. | valore predefinito | sì |
| | | | |
|**Configurazione cluster** |**Informazioni sulla configurazione del cluster** | ||
| `Architettura nodo di lavoro`| Seleziona l'architettura della tua piattaforma cloud (amd64 o S390x).| Architettura rilevata automaticamente in base al tuo nodo master | sì |
| `Immagine`| Percorso del grafico Helm. | Compilato automaticamente sul percorso installato, non modificare questo valore | sì |
| | | | |
|**Rete blockchain** | **Le informazioni sulla configurazione della rete richieste per il peer remoto**| | |
| `ID della rete`| Valore dell'ID di rete trovato nel Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. Fai clic sul pulsante `Configurazione peer remoto` nella schermata "Panoramica", quindi copia e incolla qui le informazioni.| nessuno | sì |
| `MSP organizzazione`| Valore dell'ID di MSP dell'organizzazione trovato nel Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. Fai clic sul pulsante `Configurazione peer remoto` nella schermata "Panoramica", quindi copia e incolla qui le informazioni.| nessuno | sì |
| `Nome CA (Certificate Authority)`| Valore del nome CA trovato nel Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. Fai clic sul pulsante `Configurazione peer remoto` nella schermata "Panoramica" e copia e incolla qui le informazioni.| nessuno | sì |
| `URL CA (Certificate Authority)`| Valore dell'URL CA trovato nel Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. Fai clic sul pulsante `Configurazione peer remoto` nella schermata "Panoramica" e copia e incolla qui le informazioni.| nessuno | sì |
| `Certificato TLS CA (Certificate Authority)` | Stringa del certificato TLS CA nel Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. Fai clic sul pulsante `Configurazione peer remoto` nella schermata "Panoramica" e copia e incolla qui le informazioni.| nessuno | sì |
| | | | |
|**Identità peer remoto** | **L'ID e il segreto di registrazione utilizzati per registrare il tuo peer remoto**| | |
| `ID di registrazione peer`| Questo è l'ID di registrazione che hai immesso nella schermata "CA (Certificate Authority)" del Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. | nessuno | sì |
| `Segreto di registrazione peer`| Questo è il segreto di registrazione che hai immesso nella schermata "CA (Certificate Authority)" del Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform.| nessuno | sì |
| | | | |
|**Database peer remoto** | **Tipo di database libro mastro**| | |
| `Database libro mastro`| Il [database dello stato](/docs/services/blockchain/glossary.html#state-database) utilizzato per memorizzare il tuo libro mastro del canale. Il peer remoto deve utilizzare lo stesso database della tua [rete blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | nessuno | sì |
| `Immagine CouchDB`| Si applica solo se CouchDB è stato selezionato come database libro mastro. | Compilato automaticamente sul percorso installato, non modificare questo valore. | sì |
|**Persistenza dati** | Abilita la capacità di rendere persistenti i dati dopo il riavvio o un errore del cluster. Vedi [archiviazione in Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/ "Volumi") per ulteriori informazioni. | | |
| `Persistenza dati abilitata`| I dati dello stato saranno disponibili al riavvio del contenitore. *Se non verificato, tutti i dati andranno persi in caso di failover o di riavvio del pod.* | verificato | no |
| `Utilizza provisioning dinamico`| Utilizza l'archiviazione dinamica di Kubernetes. | verificato | no |
| | | | |
|**Configurazione del volume persistente** | **Attestazione di volume persistente che deve essere utilizzata dal tuo peer** |  |  |
| `Attestazione di volume persistente`| Immetti un nome per la tua nuova Attestazione di volume persistente (PVC) che verrà creata. | my-data-pvc | no |
| `Nome classe di archiviazione`| Scegli il nome della classe di archiviazione. | Lascia vuoto se si crea una nuova PVC; altrimenti, specifica la classe di archiviazione associata alla PVC esistente. | no |
| `Attestazione di volume esistente`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nome nuova attestazione | no |
| `Modalità di accesso all'archiviazione`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modalità di accesso") all'archiviazione per la PVC.  | ReadOnlyMany| no |
| `Etichetta del selettore`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC.| valore predefinito | no |
| `Valore del selettore`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | valore predefinito | no |
| `Dimensione attestazione di volume`| Dimensione dell'attestazione di volume, deve essere maggiore di 2 Gi. | 8 Gi  | sì |
| | | | |
|**Persistenza dei dati nel database dello stato** | **Rendere persistenti i dati dopo il riavvio o un errore del contenitore. Vedi [archiviazione in Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/ "Volumi") per ulteriori informazioni** | | |
| `Persistenza dati abilitata`| I dati del libro mastro saranno disponibili al riavvio del contenitore. *Se non verificato, tutti i dati andranno persi in caso di failover o di riavvio del pod.*| verificato | no |
| `Utilizza provisioning dinamico`| Utilizza l'archiviazione dinamica di Kubernetes. | verificato | no |
| | | | |
|**Configurazione del volume persistente del database dello stato** |**Attestazione di volume persistente che deve essere utilizzata per il database dello stato. Vedi [archiviazione in Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/ "Volumi") per ulteriori informazioni.** |||
| `Attestazione di volume persistente`| Immetti un nome o utilizza il valore predefinito per la tua nuova attestazione di volume persistente CouchDB che verrà creata. | statedb-pvc | no |
| `Nome classe di archiviazione del database dello stato`|Scegli il nome della classe di archiviazione.| nessuno | no |
| `Attestazione di volume esistente del database dello stato`| Per utilizzare un'attestazione di volume esistente, immetti il nome pvc e lascia vuoti tutti gli altri campi. | nessuno | no |
| `Modalità di accesso all'archiviazione del database dello stato`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modalità di accesso") all'archiviazione per la PVC.  | ReadOnlyMany| no |
| `Etichetta del selettore`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") della tua PVC.| valore predefinito | no |
| `Valore del selettore`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") della tua PVC.| valore predefinito | no |
| `Dimensione attestazione di volume del database dello stato`| Dimensione dell'attestazione di volume persistente. | 8 Gi  | no |
| | | | |
| **Risorse peer** | **CPU e memoria minime e massime per il contenitore peer** | | |
| `Richiesta di CPU peer` | Numero minimo di CPU da assegnare al peer. | 2 | sì |
| `Limite di CPU peer` | Numero massimo di CPU da assegnare al peer. | 2 | sì |
| `Richiesta di memoria peer` | Quantità minima di memoria da assegnare al peer. | 4 Gi | sì |
| `Limite di memoria peer` | Quantità massima di memoria da assegnare al peer. | 4 Gi | sì |
| | | | |
|**Risorse CouchDB** | **CPU e memoria minime e massime da assegnare al contenitore CouchDB. Questa sezione si applica solo se CouchDB è stato selezionato come database dello stato**| | | |
| `Richiesta di CPU CouchDB` | Numero minimo di CPU da assegnare a CouchDB.| 2 | sì |
| `Limite di CPU CouchDB` | Numero massimo di CPU da assegnare a CouchDB. | 2 | sì |
| `Richiesta di memoria CouchDB` | Quantità minima di memoria da assegnare a CouchDB.| 4 Gi | sì |
| `Limite di memoria CouchDB` | Quantità massima di memoria da assegnare a CouchDB. | 4 Gi | sì |

Quando si seleziona CouchDB come database peer remoto, nel pod vengono creati due contenitori, uno per il peer e uno per CouchDB.
Il contenitore peer include un singolo montaggio del volume sulla PVC del peer che memorizza i blocchi e le transazioni sul file system. Il contenitore CouchDB monta la PVC del database dello stato del peer che contiene i dati del libro mastro.

Quando si seleziona LevelDB come database peer remoto, nel pod viene creato un singolo contenitore per l'esecuzione dei processi peer e
LevelDB. Questo contenitore ha due montaggi del volume, uno per la PVC del peer e l'altro per la PVC del database dello stato del peer che contiene i dati del libro mastro.

| Selezione del database peer remoto  | Contenuti del contenitore #1  | Contenuti del contenitore #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer remoto che monta la PVC del peer| CouchDB che monta la PVC del database dello stato |
| LevelDB | Peer remoto e LevelDB che montano la PVC del peer e la PVC del database dello stato | n/d |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](/docs/services/blockchain/howto/remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  Questo comando genera il file `peer-cert.pem`, che è il certificato TLS del tuo peer.

--->

## Passo quattro: Verifica dell'installazione del peer remoto
{: #verify-installation-icp}

Dopo aver installato il peer remoto, puoi visualizzare il log del peer per verificare se l'installazione del tuo peer ha avuto esito positivo.

1. Dopo aver fatto clic su **Installa**, fai clic sul pulsante **Visualizza release Helm** nella finestra a comparsa. Scorri fino alla sezione **Pod**. Quando lo stato dei pod mostra **In esecuzione**, puoi trovare la tua istanza del peer remoto sotto la sezione **Distribuzioni**.

2. Nella console ICP, fai clic sull'icona **Menu** nell'angolo superiore sinistro. Dall'elenco di menu, fai clic su **Carichi di lavoro** > **Distribuzioni**. Nella tabella di distribuzione, fai clic sull'istanza del peer remoto che crei e si aprirà la schermata di panoramica della distribuzione. Fai clic sulla scheda **Log** per visualizzare i log del tuo peer remoto. Puoi immettere la stringa `Started` nel campo di ricerca.

  - Se il tuo peer remoto viene avviato correttamente, puoi visualizzare i log simili al seguente esempio:
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - Se non vedi alcun log nella schermata, il tuo peer remoto non è stato avviato correttamente. Per visualizzare ulteriori log per risolvere il problema, fai clic sull'icona **Menu** nell'angolo superiore sinistro e quindi su **Carichi di lavoro** > **Release Helm**. Fai clic sulla tua release helm per aprirla. Fai clic sul link **Visualizza log** accanto al **Pod** associato per visualizzare ulteriori log del grafico nell'interfaccia Kibana.

3. Per verificare che il peer remoto possa connettersi alla tua rete {{site.data.keyword.blockchainfull}} Platform, puoi
eseguire un comando della CLI del peer dall'interno del contenitore peer remoto. Esegui il comando della CLI `peer channel fetch` per recuperare il blocco genesi da un canale:

   1. Se non è già connesso al tuo cluster ICP, segui queste [istruzioni](/docs/services/blockchain/howto/remote_peer_operate_icp.html#remote-peer-kubectl-configure) per stabilire la connessione e utilizza la CLI dall'interno del contenitore peer.

   2. La tua organizzazione deve essere aggiunta a un canale nella rete prima che tu possa recuperare il blocco genesi.

     - Puoi avviare un nuovo canale per il peer remoto. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](/docs/services/blockchain/howto/create_channel.html#creating-a-channel). Tieni presente che devi disporre di almeno un peer su {{site.data.keyword.blockchainfull_notm}} Platform prima di poter creare un canale nel Monitoraggio della rete.  

     - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [aggiornamento del canale](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). Un membro del canale con i peer su {{site.data.keyword.blockchainfull_notm}} Platform può utilizzare il Monitoraggio della rete per aggiungere la tua organizzazione al canale anche se non ospiti alcun peer sulla piattaforma.

      Il peer remoto carica il proprio certificato di firma durante l'installazione, pertanto dovrai solo sincronizzare il certificato sul canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si è unita la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale.

   3. Richiama le informazioni di configurazione dal file `creds.json` disponibile nella schermata "Panoramica" del tuo Monitoraggio della rete. Fai clic su **Profilo connessione** e quindi su **Scarica**.

     - Trova l'URL ordinante ricercando gli **ordinanti**, che puoi trovare in `orderers > url`. Prendi nota dell'URL che inizia con il nome della rete. L'URL è simile al seguente esempio:

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - Trova il nome della tua organizzazione ricercando le **organizzazioni**. Questa deve essere la stessa organizzazione utilizzata per registrare il tuo peer remoto. Puoi trovare il nome della tua organizzazione insieme al suo `mspid` associato. Prendi nota del valore dell'`mspid`.

    4. Immetti i seguenti comandi per impostare le variabili di ambiente nel contenitore peer.

       ```
       export ORDERER_1=<ORDERER_URL>
       export CHANNEL=<CHANNEL_NAME>
       export CC_NAME=<CC_NAME>
       export ORGID=<ORGANIZATION_MSP_ID>
       export PEERADDR=<PEER_ADDR>
       ```
       {:codeblock}

       Sostituisci i campi con le tue proprie informazioni.
         - Sostituisci `<ORDERER_URL>` con il nome host e la porta dell'ordinante dal file `creds.json`.
         - Sostituisci `<CHANNEL_NAME>` con il nome del canale a cui si unisce il peer.
         - Sostituisci `<CC_NAME>` con qualsiasi nome che si riferisca al tuo chaincode.
         - Sostituisci `<ORGANIZATION_MSP_ID>` con il nome dell'organizzazione dal file `creds.json`.
         - Sostituisci `<PEER_ADDR>` con `localhost:7051`

       Ad esempio:

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. Immetti il seguente comando della CLI del peer per recuperare il blocco genesi del canale.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **Nota:** è possibile che quando esegui uno di questi comandi della CLI, venga visualizzata la seguente avvertenza che può essere tranquillamente ignorata.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     Verifica che il comando abbia funzionato correttamente e che il blocco genesi sia stato aggiunto al contenitore peer immettendo il seguente comando:

     ```
     ls *.block
     ```
     {:codeblock}

     Sai che il blocco genesi viene aggiunto correttamente quando viene visualizzato qualcosa di simile al seguente esempio:

     ```
     defaultchannel_0.block
     ```

## Operazioni successive
{: #whats-next-icp}

Dopo aver configurato il peer remoto in ICP, devi completare diversi passi operativi prima di poter inviare transazioni e leggere il libro mastro distribuito dalla rete blockchain. Per ulteriori informazioni, vedi [Gestione di un peer remoto](/docs/services/blockchain/howto/remote_peer_operate_icp.html).

## Alta disponibilità
{: #high-availability}

Come procedura ottimale per l'alta disponibilità, si consiglia vivamente di distribuire almeno due peer per organizzazione.

Completa la seguente procedura per distribuire peer remoti altamente disponibili su ICP:

1. Esamina come [configurare il tuo cluster ICP per l'alta disponibilità ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "Cluster {{site.data.keyword.cloud_notm}} Private ad alta disponibilità").

2. Installa il peer più volte sul tuo cluster per distribuire i peer su diversi nodi di lavoro. Il grafico Helm contiene una politica di anti-affinità che si basa sui tuoi ID rete e ID organizzazione. Il cluster ICP rileva se i peer si connettono alla stessa rete dalla stessa organizzazione e tenta di organizzare tali peer su diversi nodi di lavoro.  
  **Nota:** sebbene la politica "preferisca" mettere i peer su diversi nodi di lavoro, non lo impone. Ad esempio, se hai un solo nodo di lavoro, tutti i peer vengono distribuiti su questo stesso nodo.

Se distribuisci almeno due peer, dovrai anche configurare le [applicazioni per l'alta disponibilità](/docs/services/blockchain/v10_application.html#ha-app).

## Considerazioni sulla sicurezza
{: #icp-security}

I peer remoti vengono distribuiti al di fuori di {{site.data.keyword.blockchainfull}} Platform; pertanto, sei responsabile della gestione della sicurezza del peer remoto. Ciò include importanti aree di sicurezza fornite dalle reti piano Enterprise, come la gestione delle chiavi e la crittografia dei dati. Esamina i seguenti argomenti in merito alle considerazioni sulla sicurezza per i peer remoti.

### Identità e controllo degli accessi
{: #icp-security-node}

Al di fuori di {{site.data.keyword.cloud_notm}}, devi configurare la gestione delle identità, l'autenticazione e il controllo degli accessi per gli utenti che interagiscono con i peer remoti. Per configurare queste funzionalità, puoi utilizzare la [gestione utenti ICP ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "Sicurezza").

### Sicurezza dei dati
{: #icp-security-data}

Il piano Enterprise di {{site.data.keyword.blockchainfull}} Platform utilizza la crittografia completa del disco basata sulla [crittografia a chiave simmetrica ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Crittografia simmetrica") per proteggere tutti i dati utilizzati dalle reti. Devi adottare misure simili nel tuo ambiente per proteggere i dati del tuo peer remoto.

### Residenza dei dati
{: #icp-data-residency}

La residenza dei dati richiede che l'elaborazione e l'archiviazione di tutti i dati del libro mastro blockchain rimangano all'interno dei confini di un singolo paese.
Per ulteriori informazioni su come completare la residenza dei dati, vedi [Residenza dei dati](/docs/services/blockchain/howto/remote_peer.html#data-residency).

### Gestione delle chiavi
{: #icp-security-key-management}

La gestione delle chiavi è un aspetto fondamentale della sicurezza dei peer remoti. Se una chiave privata viene compromessa o persa, gli attori ostili potrebbero essere in grado di accedere ai dati e alle funzionalità del tuo peer remoto. Il piano Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform utilizza gli [Hardware Security Module](/docs/services/blockchain/glossary.html#hsm) (HSM) per memorizzare le chiavi private della tua rete. HSM è un'appliance fisica che impedisce ad altre parti di accedere alla tua chiave privata.

Quando distribuisci un peer remoto su ICP, sei responsabile della gestione delle tue chiavi private. Sebbene {{site.data.keyword.blockchainfull_notm}} Platform generi le tue chiavi private, tali chiavi non vengono memorizzate sulla piattaforma. È essenziale assicurarsi di memorizzare le chiavi in un luogo sicuro in modo che non vengano compromesse. Puoi trovare la chiave privata del tuo peer remoto nella cartella keystore dell'MSP del peer, nella directory `/mnt/crypto/peer/peer/msp/keystore/` all'interno del tuo contenitore peer. Per ulteriori informazioni sui certificati all'interno del tuo peer remoto, vedi la sezione [Membership Services Provider](/docs/services/blockchain/certificates.html#msp) dell'esercitazione [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

Puoi utilizzare Key Escrow per recuperare le chiavi private perse. Questo deve essere eseguito prima della perdita di qualsiasi chiave. Se non è possibile recuperare una chiave privata, devi ottenere nuove chiavi private registrando una nuova identità con la tua Autorità di certificazione. Devi inoltre rimuovere e sostituire il tuo signCert da tutti i canali a cui ti sei unito.

<!---
In IBP, quando viene creata una chiave privata, vengono generati due insiemi di materiali della chiave indipendenti, in custodia di due entità diverse. Questi due insiemi di materiali della chiave vengono quindi combinati per creare la chiave privata.
--->

### TLS
{: #icp-security-tls}

[Transport Layer Security ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Una panoramica dell'handshake SSL o TLS") (TLS) è integrato nel modello di attendibilità di Hyperledger Fabric. Tutti i componenti in {{site.data.keyword.blockchainfull_notm}} Platform utilizzano TLS per autenticarsi e comunicare tra loro. Pertanto, i componenti di rete su {{site.data.keyword.blockchainfull_notm}} Platform devono essere in grado di completare un handshake TLS con i tuoi peer remoti. Ciò implica, tra le altre cose, che devi abilitare il pass-through, utilizzando ad esempio l'elenco elementi consentiti nel firewall web dalle applicazioni client al tuo peer.

Puoi utilizzare il TLS reciproco, che richiede l'autenticazione bidirezionale (server-client) anziché unidirezionale (solo server), per proteggere la comunicazione tra la tua applicazione e le reti piano Enterprise. Puoi utilizzare il Monitoraggio della rete [per abilitare il TLS reciproco](/docs/services/blockchain/v10_dashboard.html#mutual-tls) per i peer su {{site.data.keyword.blockchainfull_notm}} Platform. Per abilitare il TLS reciproco sul tuo peer remoto, segui le istruzioni per [abilitare il TLS reciproco per i nodi peer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Protezione della comunicazione con Transport Layer Security") nella documentazione di Hyperledger Fabric. Si consiglia vivamente di abilitare il TLS reciproco per le applicazioni.

### Configurazione di Membership Service Provider
{: #icp-security-MSP}

I componenti di {{site.data.keyword.blockchainfull_notm}} Platform utilizzano le identità tramite gli MSP (Membership Service Provider). Gli MSP associano i certificati emessi dalle CA ai ruoli di rete e di canale. Per ulteriori informazioni sul funzionamento degli MSP con il peer remoto, vedi [Membership Service Provider (MSP)](/docs/services/blockchain/certificates.html#msp).

### Sicurezza delle applicazioni
{: #icp-security-appl}

Poiché tutti i richiami del chaincode sono firmati, Fabric gestisce la sicurezza delle applicazioni. Inoltre, Fabric include anche controlli a livello di applicazione basati su ACL.

## Licenza e determinazione dei prezzi
{: #license-pricing-icp}

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer on {{site.data.keyword.cloud_notm}} Private è un'edizione Beta gratuita adatta per la valutazione e la sperimentazione e viene distribuita come applicazione ICP. Puoi accedere al [pacchetto Github ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and--> e scaricare il [grafico Helm ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Helm Chart").

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

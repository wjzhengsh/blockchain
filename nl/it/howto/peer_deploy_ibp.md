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

# Distribuzione dei peer in {{site.data.keyword.cloud_notm}} Private e collegamento al piano Starter o Enterprise
{: #peer-ibp}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Le seguenti istruzioni descrivono come distribuire un peer {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.cloud_notm}} Private (ICP) che si collegherà alla rete del piano Starter o Enterprise su {{site.data.keyword.cloud_notm}} o sul tuo ICP locale.
{:shortdesc}

Prima di distribuire un peer, rivedi [Considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

La tua rete del piano Starter o Enterprise deve eseguire Hyperledger Fabric v1.1 o v1.2.1. Puoi trovare la tua versione di Hyperledger Fabric aprendo la [finestra Preferenze della rete](/docs/services/blockchain/v10_dashboard.html#network-preferences) nel tuo Monitoraggio della rete.

## Risorse richieste
{: #peer-resources-required}

Assicurati che il tuo sistema ICP soddisfi i requisiti della risorsa di hardware minimi:

| Componente | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| Peer | 2 | 2 GB | 50 GB con capacità di espansione |
| CouchDB for Peer | 2| 2 GB | 50 GB con capacità di espansione |

 **Note:**
 - Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti della CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in ICP. VPC è un'unità di misura per determinare il costo di licenza dei prodotti IBM. Per ulteriori informazioni sugli scenari per decidere VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un grande volume di transazioni, è importante assegnare una quantità sufficientemente grande di archiviazione; ad esempio 250 GB per il tuo peer. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se stai per esaurire l'archiviazione sul tuo peer, devi distribuire un nuovo peer con un file system più grande e consentirgli di eseguire la sincronizzazione tramite i tuoi altri componenti sugli stessi canali.

## Archiviazione
{: #storage}

Devi determinare l'archiviazione che verrà utilizzata dal tuo peer. Se utilizzi le impostazioni predefinite, il grafico Helm creerà una nuova attestazione di volume persistente (PVC) di 8 Gi con il nome di `my-data-pvc` per i tuoi dati del peer e un'altra PVC di 8 Gi con il nome di `statedb-pvc` per il tuo database dello stato.

Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* `storageClass` durante l'installazione di ICP o che l'amministratore di sistema Kubernetes debba creare una nuova storageClass prima della tua distribuzione.

Puoi scegliere di distribuire il peer sulle piattaforme amd64 o s390x. Tuttavia, tieni presente che il [Provisioning dinamico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) è disponibile solo per i nodi amd64 in ICP. Se il tuo cluster include una combinazione di nodi di lavoro s390x e amd64, non è possibile utilizzare il provisioning dinamico.

Se non utilizzi il provisioning dinamico, devono essere creati e configurati dei [Volumi persistenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) con etichette che possono essere utilizzati per perfezionare il processo di bind della PVC Kubernetes.

## Prerequisiti per la distribuzione di un peer
{: #prerequisites-peer-ibp}

1. Prima di poter installare un peer su ICP, devi [installare ICP](/docs/services/blockchain/ICP_setup.html) e [installare il grafico Helm di {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html).

2. Se utilizzi la Community Edition e vuoi eseguire questo grafico Helm su un cluster ICP senza la connettività Internet, devi creare degli archivi su una macchina collegata a Internet prima di poter installare gli archivi sul tuo cluster ICP. Per ulteriori informazioni, consulta [Adding featured applications to clusters without Internet connectivity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Tieni presente che puoi trovare il file della specifica manifest.yaml in ibm-blockchain-platform-dev/ibm_cloud_pak nel grafico Helm.

3. Devi disporre di un'organizzazione che sia un membro di una rete del piano Starter o piano Enterprise su {{site.data.keyword.cloud_notm}}. Per operare, il peer si avvale degli endpoint API, delle CA Hyperledger Fabric e del servizio ordini della rete di {{site.data.keyword.blockchainfull_notm}} Platform. Se non sei membro di alcuna rete blockchain, devi creare o aderire a una rete. Per ulteriori informazioni, vedi [Creazione di una rete](/docs/services/blockchain/get_start.html#creating-a-network) o [Adesione a una rete](/docs/services/blockchain/get_start.html#joining-a-network).

4. Devi prima [distribuire una CA](/docs/services/blockchain/howto/CA_deploy_icp.html) su ICP. Utilizzerai questa CA come una CA TLS. Segui i passi preliminari per [utilizzare una CA su ICP](/docs/services/blockchain/howto/CA_operate.html#prerequisites) prima di distribuire il tuo peer. Non dovrai procedere oltre con questa procedura.

5. Richiama il valore dell'indirizzo IP proxy del cluster della tua CA TLS dalla console ICP. **Nota:** devi essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Cluster administrator roles and actions") per accedere al tuo IP proxy. Accedi al cluster ICP. Nel pannello di navigazione di sinistra, fai clic su **Piattaforma** e quindi su **Nodi** per visualizzare i nodi definiti nel cluster. Fai clic sul nodo con il ruolo `proxy` e copia il valore dell'`IP host` dalla tabella. **Importante:** salva questo valore perché lo utilizzerai quando configuri il campo `Proxy IP` del grafico Helm.

6. Crea un file di configurazione del peer e archivialo come un segreto Kubernetes in ICP. Puoi trovare le istruzioni per creare questo file nella [prossima sezione](#peer-config-file).

## Creazione del file di configurazione
{: #peer-config-file}

Prima di distribuire un peer, devi creare un file JSON di configurazione che contiene le informazioni importanti sull'identità del peer e la tua CA (Certificate Authority) su {{site.data.keyword.cloud_notm}}. Successivamente, devi passare questo file al grafico Helm durante la configurazione utilizzando un oggetto [segreto Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/configuration/secret/). Questo file consente al tuo peer di ottenere i certificati di cui ha bisogno dalla CA (Certificate Authority) su {{site.data.keyword.cloud_notm}} per aderire a una rete del piano Starter o Enterprise. Questo file contiene inoltre un certificato di gestione che ti consente di utilizzare il tuo peer come un utente amministratore.

In queste istruzioni, ti forniremo un JSON template che puoi modificare e salvare sul tuo file system locale. Successivamente, ti guideremo nell'utilizzo della tua CA per completare il file di configurazione.

### File di configurazione

Il template per il file di configurazione può essere trovato qui di seguito:
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

Copia questo file completo in un editor di testo in cui puoi modificarlo e salvarlo sul tuo file system locale come un file JSON. Tu dovrai soltanto compilare le due prime sezioni di questo file: `"enrollment"` e `"tls"`.

Per completare il file di configurazione, devi completare alcuni passi dal Monitoraggio della rete della tua rete del piano Starter o Enterprise.

1. [Richiama le informazioni sull'endpoint della tua CA del piano Starter o Enterprise](#ibp-ca-endpoint).
2. [Registra il peer con la tua CA](#register-peer).
3. [Registra un amministratore del peer](#register-admin) che utilizzerai per gestire il tuo peer. Non devi completare questo passo se hai già registrato un amministratore per distribuire un altro peer.

Devi inoltre completare diversi passi utilizzando il client CA Fabric e la tua CA TLS distribuiti su ICP.

1. Utilizza il client CA Fabric per [generare la cartella MSP di gestione del peer](#enroll-admin).
2. [Richiama le informazioni sull'endpoint della tua CA TLS](#tls-ca-endpoint).
3. Utilizza il client CA Fabric per [registrare il peer con la tua CA TLS](#tls-register-peer).


### Informazioni sulla CA del piano Starter o Enterprise
{: #ibp-ca-endpoint}

Per prima cosa, devi fornire le informazioni di connessione della tua CA su {{site.data.keyword.cloud_notm}} al file di configurazione. Accedi all'IU Monitoraggio della rete sul piano Starter o Enterprise. Nella schermata **Panoramica** del tuo Monitoraggio della rete, fai clic sul pulsante **Configurazione peer remoto**. Verrà aperta una finestra a comparsa che contiene le informazioni necessarie sulla tua CA.

![Configurazione peer remoto](../images/myresources_starter.png "Configurazione peer remoto")
*Figura 1. Pannello di configurazione peer remoto*

La finestra a comparsa contiene i seguenti campi:

  - **MSP organizzazione**
  - **Nome CA (Certificate Authority)**
  - **URL CA (Certificate Authority)**
  - **Certificato TLS CA (Certificate Authority)**

Nel file nella sezione `"components"`, immetti i seguenti valori dal passo precedente:
- `"caname"` è il valore del **Nome CA (Certificate Authority)**
- `"cahost"` è il nome host dell'URL della CA. Ad esempio se l'URL della CA è `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, il valore di `"cahost"` sarà `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com`
- `"caport"` è la porta da `"cahost"`. Ad esempio se l'URL della CA è `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, `"caport"` sarà `31011`.
- `"cacert"` è il valore dal campo **Certificato TLS CA (Certificate Authority)**. Prima di inserire il certificato nel file, devi codificarlo nel formato base64 immettendo i seguenti comandi e sostituendo la stringa `<paste in Certificate Authority (CA) TLS Certificate>` con il valore che hai copiato dal tuo Monitoraggio della rete.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
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

Incolla la stringa risultante nel campo `"cacert"` in `"catls"` nel file. Dopo l'aggiornamento, il campo `"cacert"` è simile al seguente esempio:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Registra il peer
{: #register-peer}

Per unire i tuoi peer ai canali e installare e istanziare il chaincode, devi prima registrare il peer con la tua CA su {{site.data.keyword.cloud_notm}}. La distribuzione del tuo peer può quindi generare i certificati necessari al peer per partecipare a una rete del piano Starter o Enterprise. Completa la seguente procedura per registrare un peer con un `enroll ID` e un `enroll secret`. Incollerai questi due valori nel file di configurazione.

1. Accedi a {{site.data.keyword.blockchainfull_notm}} Platform e al tuo Monitoraggio della rete. Nella schermata "CA (Certificate Authority)", puoi visualizzare tutte le identità che sono state registrate con la tua organizzazione, come ad esempio l'amministratore o le applicazioni client.
  ![Schermata CA](../images/CA_screen_starter.png "Schermata CA")
  *Figura 2. Schermata CA*

2. Fai clic sul pulsante **Aggiungi utente** sulla schermata. Si apre una schermata a comparsa che ti consente di registrare il tuo peer dopo aver compilato i seguenti campi. Salva i valori dell'ID e del segreto poiché dovrai utilizzarli quando configuri il tuo peer.
  - **ID di registrazione:** il nome utente del tuo peer, a cui si fa riferimento come `enroll ID` quando configuri il peer. **Salva questo valore** per il file di configurazione.
  - **Segreto di registrazione:** la password del tuo peer, a cui si fa riferimento come `enroll Secret` quando configuri il peer. **Salva questo valore** per il file di configurazione.
  - **Tipo:** seleziona `Peer` per questo campo.
  - **Affiliazione:** si tratta dell'affiliazione sotto la tua organizzazione, ad esempio `org1`, a cui apparterrà il tuo peer. Seleziona un'affiliazione esistente dall'elenco a discesa o immettine una nuova.
  - **Iscrizioni massime:** puoi utilizzare questo campo per limitare il numero di volte in cui puoi registrare o generare certificati utilizzando questa identità. Se non specificato, il valore predefinito è di registrazioni illimitate.

  Dopo aver completato questi campi, fai clic su **Invia** per registrare il peer. Il peer registrato viene quindi elencato nella tabella come identità nella tua organizzazione. Come misura di sicurezza, utilizza ogni identità e l'enrollID e il segreto di accompagnamento, per distribuire solo un peer. Non riutilizzare le password e gli ID del peer.

3. Nel file di configurazione nella sezione `"components"`, immetti i seguenti valori:
  - `"enrollid"` è il valore di `enroll ID` dal passo precedente.
  - `"enrollsecret"` è il valore di `enroll secret` dal passo precedente.


### Creazione di un amministratore
{: #register-admin}

Dopo aver registrato l'identità peer, devi anche creare un'identità amministratore che utilizzerai per gestire il peer. Per prima cosa, devi registrare questa nuova identità con la tua CA e utilizzarla per generare una cartella MSP. Successivamente, dovrai aggiungere il signCert degli utenti amministratore al file di configurazione, dove verrà creato un certificato di gestione del peer durante la distribuzione. Questo ti consente di utilizzare i certificati dell'identità amministratore per utilizzare la tua rete blockchain, come ad esempio per avviare un nuovo canale o per installare il chaincode sui tuoi peer.

Devi creare solo un'identità amministratore per i componenti che appartengono alla tua organizzazione. Se stai distribuendo più peer, devi completare questa procedura solo una volta. Puoi utilizzare il signCert che hai generato per un peer per distribuire tutti gli altri peer che appartengono alla tua organizzazione.

1. Accedi a {{site.data.keyword.blockchainfull_notm}} Platform e al tuo Monitoraggio della rete. Nella schermata "CA (Certificate Authority)", puoi visualizzare tutte le identità che sono state registrate con la tua organizzazione, come ad esempio l'amministratore o le applicazioni client.
  ![Schermata CA](../images/CA_screen_starter.png "Schermata CA")
  *Figura 2. Schermata CA*

2. Fai clic sul pulsante **Aggiungi utente** sulla schermata. Si apre una schermata a comparsa che ti consente di registrare il tuo peer dopo aver compilato i seguenti campi. Salva i valori dell'ID e del segreto poiché dovrai utilizzarli quando configuri il tuo peer.
  - **ID di registrazione:** il nome utente del tuo amministratore del peer, a cui si fa riferimento come `enroll ID` quando configuri il peer. **Salva questo valore** per quando generi la cartella MSP di gestione.
  - **Segreto di registrazione:** la password del tuo amministratore del peer, a cui si fa riferimento come `enroll Secret` quando configuri il peer. **Salva questo valore** per quando generi la cartella MSP di gestione.
  - **Tipo:** seleziona `Peer` per questo campo.
  - **Affiliazione:** si tratta dell'affiliazione sotto la tua organizzazione, ad esempio `org1`, a cui apparterrà il tuo peer. Seleziona un'affiliazione esistente dall'elenco a discesa o immettine una nuova.
  - **Iscrizioni massime:** puoi utilizzare questo campo per limitare il numero di volte in cui puoi registrare o generare certificati utilizzando questa identità. Se non specificato, il valore predefinito è di registrazioni illimitate.

  Dopo aver immesso questi campi, fai clic su **Invia** per creare l'amministratore. L'amministratore creato viene quindi elencato nella tabella come un'identità nella tua organizzazione.

### Generazione della cartella MSP di gestione del peer
{: #enroll-admin}

Dopo aver registrato l'identità di gestione, devi generare il signCert e la cartella di gestione del peer. Pertanto, devi eseguire un comando di registrazione sulla tua CA del piano Starter o Enterprise.

1. Scarica il [client CA Fabric](/docs/services/blockchain/howto/CA_operate.html#fabric-ca-client) se non lo hai già fatto.
2. Passa alla directory in cui archivierai il tuo materiale di crittografia e crea la cartella in cui archivierai la cartella MSP del tuo amministratore del peer.

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. Imposta il percorso in cui il client può creare i tuoi certificati su `$FABRIC_CA_CLIENT_HOME`. Assicurati di rimuovere il materiale di configurazione che potrebbe essere stato creato da dei tentativi precedenti. Se precedentemente non hai eseguito il comando `enroll`, la cartella `msp` e il file `.yaml` non esistono.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. Apri il file JSON del **Profilo connessione** dal pannello "Panoramica" nel Monitoraggio della rete e trova le seguenti variabili:
  - URL per CA: `url` in `certificateAuthorities`
  - Nome CA: `caName`

5. Scarica i certificati TLS da {{site.data.keyword.cloud_notm}} a seconda del piano di servizi, dell'ubicazione e del cluster che utilizzi. Puoi trovare il tuo cluster in base al nome del dominio del tuo URL della CA (Certificate Authority): ad esempio `us01.blockchain.ibm.com:31011` o `us02.blockchain.ibm.com:31011`.

  - Certificato TLS root per piano Starter
    - Stati Uniti: [us01.blockchain.ibm.com.cert ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Regno Unito: [uk01.blockchain.ibm.com.cert ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [Certificato TLS root per piano Enterprise ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Salva i contenuti in una directory a cui puoi fare riferimento nei comandi futuri.

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. Immetti il seguente comando per generare i certificati con la tua identità di gestione del peer:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  `<enroll_id>` e `<enroll_password>` sono l'**ID** e il **Segreto** dell'amministratore del peer che è stato [registrato utilizzando il Monitoraggio della rete](#register-admin). `<ca_name>` e `<ca_url_with_port>` sono i valori di `caName` e `url` dal tuo profilo di connessione. Tralascia il `http://` all'inizio dell'URL della CA.

  Una vera chiamata potrebbe essere simile al seguente comando di esempio:

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  Dopo che questo comando è stato completato correttamente, genererà una nuova cartella MSP nella directory che hai specificato come `$FABRIC_CA_CLIENT_HOME`. Questa directory contiene i certificati che utilizzerai per gestire i tuoi peer.

7. Nella cartella MSP che è appena stata creata, apri il file signCert del nuovo utente e convertilo nel formato base64. Troverai il signCert nella cartella `signcerts` della directory MSP. Se stai utilizzando la procedura di esempio, puoi utilizzare i seguenti comandi:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
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

  Questo comando riprodurrà a schermo una stringa simile al seguente esempio:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  Immetti questa stringa nel campo `"admincerts"` nella sezione del componente nel file di configurazione.

### Informazioni sulla CA TLS
{: #tls-ca-endpoint}

I campi `"tls"` nel file di configurazione richiedono le informazioni dalla CA che hai distribuito su ICP. Utilizzerai questa CA come una CA TLS separata, che rende il tuo peer più sicuro. Utilizza le seguenti istruzioni per generare le informazioni pertinenti:

- I valori `"cahost"` e `"caport"` sono l'URL e la porta dell'[URL della CA](/docs/services/blockchain/howto/CA_operate.html#ca-url). Ad esempio, se l'URL della CA è `http://9.30.94.174:30167`, il valore di `cahost` sarà `9.30.94.174` e `caport` sarà `30167`.
- `"caname"` è il nome della CA TLS della CA che hai distribuito su ICP. Il nome della CA TLS è il valore che hai fornito al campo `CA TLS instance name` durante la configurazione della CA.
- `"cacert"` è un certificato TLS codificato base64 della tua CA. Come prerequisito, aggiorna la seguente sezione con il valore dell'output del comando quando richiami il tuo [certificato TLS della CA](/docs/services/blockchain/howto/CA_operate.html#ca-tls).

  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  Dopo l'aggiornamento, il campo `"cacert"` è simile al seguente esempio:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Registrazione del peer con la CA del TLS
{: #tls-register-peer}

Devi registrare il tuo peer con la CA del TLS su ICP utilizzando il client CA Fabric.

1. Al momento, dovresti avere il file del certificato TLS `tls.pem` nella cartella `$HOME/fabric-ca-client/catls`. Se non è così, puoi copiare il certificato TLS che hai [scaricato da ICP](/docs/services/blockchain/howto/CA_operate.html#ca-tls) in una directory a cui puoi fare riferimento in comandi futuri. Assicurati di essere nella tua directory `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. Devi eseguire l'iscrizione utilizzando l'amministratore della CA del TLS. Modifica `$FABRIC_CA_CLIENT_HOME` con una directory in cui vuoi archiviare i tuoi certificati di gestione della CA del TLS.

  ```
  cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. Immetti il seguente comando per generare i certificati con l'amministratore della CA del TLS.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  `<enroll_id>` e `<enroll_password>` nel comando sono [il nome utente e la password dell'amministratore CA](/docs/services/blockchain/howto/CA_deploy_icp.html#admin-secret) che hai passato al segreto Kubernetes quando hai distribuito la CA (Certificate Authority). Immetti l'[URL della CA](/docs/services/blockchain/howto/CA_operate.html#ca-url) in `<ca_url_with_port>`. Tralascia il `http://` all'inizio. `<tls_ca_name>` è quello che hai specificato durante la [configurazione della CA](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms).

  `<ca_tls_cert_file>` è il tuo nome del file del [certificato TLS CA](/docs/services/blockchain/howto/CA_operate.html#ca-tls) con il proprio percorso completo.

  Una chiamata reale potrebbe essere simile al seguente esempio:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Dopo aver eseguito l'iscrizione, disponi dei certificati necessari per registrare il peer con la CA del TLS.

4. Immetti il seguente comando per trovare il nome della tua affiliazione e della tua organizzazione.

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Il tuo comando è simile al seguente esempio:

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Dovresti visualizzare informazioni simili al seguente esempio:

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  Prendi nota del valore **affiliation** della tua organizzazione, ad esempio, `org1.department1` per l'organizzazione `org1` nel precedente esempio. Dovrai utilizzare questo valore nel comando riportato di seguito.

5. Immetti il seguente comando per registrare il peer.

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Devi utilizzare il tuo nome della CA del TLS nel campo `--caname`.  Crea un nome e una password per il peer e utilizzali per sostituire `name` e `secret`. **Importante:** prendi nota di queste informazioni. Devi immettere per `name` e `secret` i valori `"enrollid"` e `"enrollsecret"` nella sezione `"tls"` del file di configurazione.

  Un comando di esempio sarà simile al seguente:

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Dopo aver immesso questo comando, puoi aggiornare il file utilizzando i valori dal passo precedente:

  ```
  "tls": {...
    },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
  ```

  Puoi registrare un'entità solo una volta. Se riscontri un problema, prova un comando con un nuovo nome utente e password. Come misura di sicurezza, utilizza ogni identità e l'enrollID e il segreto di accompagnamento, per distribuire solo un peer. Non riutilizzare le password e gli ID del peer.

  Se il comando viene completato correttamente, dovresti visualizzare informazioni simili al seguente esempio:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

Puoi eseguire un comando tree per verificare il lavoro fatto per preparare il file di configurazione. Passa alla directory in cui hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Host della richiesta di firma del certificato (CSR)
{: #csr-hosts}

Devi fornire i nomi host CSR per distribuire un peer. I nomi host CSR includono l'indirizzo IP proxy del cluster in cui distribuirai il componente nonché il nome host del servizio (`service host name`) che sarà il tuo nome host del grafico Helm.

#### Individuazione del valore dell'indirizzo IP proxy del cluster

Se vuoi distribuire un peer sullo stesso cluster ICP su cui hai distribuito la tua CA TLS, immetti lo stesso IP proxy che hai utilizzato quando hai [configurato la tua CA TLS](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms). Se vuoi distribuire il componente su un cluster diverso, puoi richiamare il valore dell'indirizzo IP proxy del cluster dalla console ICP. Devi avere il ruolo di amministratore del cluster ICP in cui sarà distribuito il peer.

1. Accedi alla console ICP. Nel pannello di navigazione di sinistra, fai clic su **Piattaforma** e quindi su **Nodi** per visualizzare i nodi definiti nel cluster.
2. Fai clic sul nodo con il ruolo `proxy` e copia il valore dell'`IP host` dalla tabella.
3. Inserisci l'`IP host` come il valore per `"hosts"` nella sezione `"csr"` del seguente file di configurazione:

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### Determinazione del nome host del servizio

Il nome host del servizio (`service host name`) sarà il nome della release Helm (`helm release name`) che specifichi durante la distribuzione. Se l'indirizzo proxy IP del cluster è 9.42.134.44" e il nome della release Helm (`helm release name`) è `org1peer1`, potrai inserire la sezione `"csr"` del file:

```
"csr": {
  "hosts": [
     "9.42.134.44",
      "org1peer1"
    ]
}
```
{:codeblock}

### Completamento del file di configurazione

Dopo aver completato tutti i passi, il tuo file di configurazione aggiornato sarà simile al seguente esempio:

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer",
			"enrollsecret": "peerpw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
        "hosts": [
           "9.42.134.44",
      "org1peer1"
    ]
			}
		}
  }
}
```
{:codeblock}

Dopo aver terminato di compilare questo file, devi salvarlo nel formato JSON e passarlo alla tua distribuzione del peer come un segreto Kurbernetes. Crea il segreto utilizzando la procedura nella [prossima sezione](#peer-config-file-ibp).

## Creazione del segreto di configurazione
{: #peer-config-file-ibp}

Un [Segreto Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/configuration/secret/) ti consente di proteggere e condividere le informazioni senza doverle passare direttamente alla distribuzione. Dopo aver salvato il file di configurazione, devi codificarlo in `base64`.

1. Per codificare il file di configurazione nel formato base64, immetti i seguenti comandi da una finestra terminale:

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

  Salva l'output risultante per il seguente passo quattro.

2. Accedi alla console ICP. Nel pannello di navigazione di sinistra, fai clic su **Configurazione** e quindi su **Segreti**. Fai clic sul pulsante **Crea segreto** per aprire un pannello a comparsa che ti consente di generare un nuovo oggetto segreto.

3. Nella scheda **Generale**, completa i seguenti campi:
  - **Nome:** fornisci al tuo segreto un nome univoco all'interno del tuo cluster. Utilizzerai questo nome quando distribuisci il tuo peer. Il nome deve essere tutto in minuscolo.  
  **Nota:** quando distribuisci un peer, viene automaticamente generato un nuovo segreto dalla distribuzione con il nome di `<helm_release_name>-secret`. Pertanto, quando dai un nome al tuo segreto, assicurati che sia diverso da `<helm_release_name>-secret` . Altrimenti, la distribuzione del grafico Helm avrà esito negativo perché il segreto che tenta di creare esiste già.
  - **Namespace:** lo spazio dei nomi per aggiungere il tuo segreto. Seleziona lo spazio dei nomi (`namespace`) a cui vuoi distribuire il tuo peer.
  - **Tipo:** immetti il valore `Opaque`.

4. Fai clic sulla scheda **Dati** nel pannello di navigazione di sinistra di questa finestra. Nei campi `Name`, specifica `secret.json` e nel campo del valore incolla la stringa codificata `base64` del tuo file di configurazione.

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
{: #ibp-peer-configuration-parms}

La seguente tabella elenca i parametri configurabili di {{site.data.keyword.blockchainfull_notm}} Platform, **specifici per il componente peer** e i relativi valori predefiniti. Se stai facendo degli esperimenti con il peer o lo stai iniziando ad utilizzare per la prima volta, utilizza i valori predefiniti dei parametri di archiviazione e database. Scorri fino alla sezione del componente peer per selezionare la casella di spunta `Installa peer` e fornisci le informazioni di configurazione associate solo a tale componente. **Anche se l'IU del grafico Helm indica che non è necessaria dell'altra configurazione, devi immettere alcuni parametri per distribuire un peer.**

#### Parametri di configurazione generali e globali
{: #peer-global-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
| `Helm release name`| Il nome della tua release Helm. Deve iniziare con una lettera minuscola e terminare con un qualsiasi carattere alfanumerico, deve contenere solo trattini e caratteri alfanumerici minuscoli. Devi utilizzare un nome della release Helm univoco ogni volta che tenti di installare un componente. **Importante:** questo valore deve corrispondere a quello utilizzato per generare il 'nome host del servizio' per il campo "hosts" nel tuo [file del segreto JSON.](#csr-hosts) | nessuno | sì  |
| `Target namespace`| Scegli lo spazio dei nomi Kubernetes per installare il grafico Helm. | nessuno | sì |
|**Configurazione globale**| Parametri che si applicano a tutti i componenti nel grafico Helm.|||
| `Service account name`| Immetti il nome dell'[account del servizio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) che utilizzerai per eseguire il pod. | valore predefinito | no |

#### Parametri di configurazione del peer
{: #peer-parameters}

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
|**Cluster configuration** |**Informazioni sulla configurazione del cluster** | ||
| `Install Peer` | Seleziona per installare un peer. |non selezionato | sì, se vuoi installare un peer |
| `Peer worker node architecture`| Seleziona l'architettura della tua piattaforma cloud (AMD64 o S390x) | AMD64 | sì |
| `Peer image repository`| Ubicazione del grafico Helm del peer. Questo campo viene automaticamente compilato con il percorso installato. Se stai utilizzando la Community Edition e non hai accesso a Internet, dovrebbe corrispondere alla directory in cui hai scaricato l'immagine peer Fabric. | ibmcom/ibp-fabric-peer | sì |
| `Peer Docker image tag`|Il valore della tag associata all'immagine peer |1.2.1, compilato automaticamente sul valore corretto.|sì|
| `Peer configuration`|Puoi personalizzare la configurazione del peer incollando il tuo file di configurazione `core.yaml` in questo campo. Per vedere un file `core.yaml` di esempio, consulta [`core.yaml` sample config ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **Solo per utenti esperti**. |nessuno|no|
| `Peer configuration secret (Required)`| Il nome del [segreto di configurazione del peer](#peer-config-secret) che hai creato in ICP.  |nessuno|sì|
|`Organization MSP (Required)`|Questo valore può essere trovato nel Monitoraggio della rete (IU IBP) facendo clic su "Configurazione peer remota" sulla schermata Panoramica.  |nessuno|sì|
|`Peer service type`| Utilizzato per specificare se le [porte esterne devono essere esposte ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sul peer. Seleziona NodePort per esporre le porte esternamente (consigliato) e ClusterIP per non esporle. LoadBalancer e ExternalName non sono supportati in questa release. | NodePort |sì|
| `State database`| Il [database dello stato](/docs/services/blockchain/glossary.html#state-database) utilizzato per memorizzare il tuo libro mastro del canale. Il peer deve utilizzare lo stesso database della tua [rete blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | LevelDB | sì |
|`CouchDB image repository`| Si applica solo se CouchDB è stato selezionato come database libro mastro. Questo campo viene automaticamente compilato con il percorso installato. Se stai utilizzando la Community Edition e non hai accesso a Internet, dovrebbe corrispondere alla directory in cui hai scaricato l'immagine CouchDB Fabric. | ibmcom/ibp-fabric-couchdb | sì |
| `CouchDB Docker image tag`| Si applica solo se CouchDB è stato selezionato come database libro mastro. Il valore della tag associata all'immagine CouchDB. | Compilato automaticamente sul valore corretto. | sì |
| `Peer Data persistence enabled`| Abilita la capacità di rendere persistenti i dati dopo il riavvio o un errore del cluster. Vedi [archiviazione in Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/ "Volumi") per ulteriori informazioni.  *Se non selezionato, tutti i dati andranno persi in caso di failover o di riavvio del pod.* | verificato | no |
| `Peer use dynamic provisioning`| Seleziona per abilitare il provisioning dinamico dei tuoi volumi di archiviazione. | verificato | no |
| `Peer persistent volume claim`| Solo per la nuova attestazione.  Immetti un nome per la tua nuova Attestazione di volume persistente (PVC) da creare. | my-data-pvc | no |
| `Peer storage class name`| Specifica un nome della classe di archiviazione per il peer. | Lascia vuoto se si crea una nuova PVC; altrimenti, specifica la classe di archiviazione associata alla PVC esistente. | no |
| `Peer existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `Peer selector label`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC.| nessuno | no |
| `Peer selector value`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | nessuno | no |
| `Peer storage access mode`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modalità di accesso") all'archiviazione per la PVC.  | ReadWriteMany| no |
| `Peer volume claim size`| Dimensione dell'attestazione di volume, deve essere maggiore di 2 Gi. | 8 Gi  | sì |
| `State database persistent volume claim`| Solo per la nuova attestazione.  Immetti un nome per la tua nuova Attestazione di volume persistente (PVC) da creare. | statedb-pvc | no |
| `State database storage class name`| Specifica un nome della classe di archiviazione per il database dello stato. | nessuno | no |
| `State database existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `State database selector label`| [Etichetta del selettore](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) per la tua PVC| nessuno | no |
| `State database selector value`| [Valore del selettore](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) per la tua PVC| nessuno | no |
| `State database storage access mode`| Specifica la [modalità di accesso ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modalità di accesso") all'archiviazione per la PVC. | ReadWriteMany| no |
| `State database volume claim size`| Scegli la dimensione del disco da utilizzare. | 8 Gi | sì |
| `CouchDB - Data persistence enabled`|Per il contenitore CouchDB, i dati del libro mastro saranno disponibili al riavvio del contenitore. *Se non selezionato, tutti i dati andranno persi in caso di failover o di riavvio del pod.*| verificato | no |
| `CouchDB - Use dynamic provisioning`| Per il contenitore CouchDB utilizza l'archiviazione dinamica di Kubernetes.| verificato | no |
| `Peer CPU request` | Numero minimo di CPU da assegnare al peer. | 1 | sì |
| `Peer CPU limit` | Numero massimo di CPU da assegnare al peer.| 2 | sì |
| `Peer Memory request` | Quantità minima di memoria da assegnare al peer. | 1Gi | sì |
| `Peer Memory limit` | Quantità massima di memoria da assegnare al peer. | 4 Gi | sì |
| `CouchDB CPU request` | Numero minimo di CPU da assegnare a CouchDB.| 1 | sì |
| `CouchDB CPU limit` | Numero massimo di CPU da assegnare a CouchDB. | 2 | sì |
| `CouchDB Memory request` | Quantità minima di memoria da assegnare a CouchDB.| 1Gi | sì |
| `CouchDB Memory limit` | Quantità massima di memoria da assegnare a CouchDB. | 4 Gi | sì |


Quando si seleziona CouchDB come database peer, nel pod vengono creati due contenitori, uno per il peer e uno per CouchDB. Il contenitore peer include un singolo montaggio del volume sulla PVC del peer che memorizza i blocchi e le transazioni sul file system. Il contenitore CouchDB monta la PVC del database dello stato del peer che contiene i dati del libro mastro.

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
{: #ibp-helm-cli}

In alternativa, puoi utilizzare la CLI Helm per installare la release Helm. Prima di eseguire il comando `helm install`, assicurati di [aggiungere il repository Helm del tuo cluster all'ambiente CLI Helm ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

Puoi configurare i parametri necessari per l'installazione creando un file `yaml` e passandolo al seguente comando `helm install`.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

dove:
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
{: #verify-installation-ibp}

Una volta che hai terminato il tuo lavoro con i parametri di configurazione, fai clic sul pulsante **Installa** e sul pulsante **Visualizza release Helm** per visualizzare la tua distribuzione. Se l'azione è stata eseguita correttamente, dovresti vedere il valore 1 nei campi `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` nella tabella Distribuzione. Potresti dover fare clic su Aggiorna e attendere l'aggiornamento della tabella. Puoi anche trovare la tabella Distribuzione facendo clic sull'icona **Menu** nell'angolo in alto a sinistra nella console ICP. Dall'elenco di menu, fai clic su **Workload** e poi su **Release Helm**.

Se scorri in basso fino alla sezione `Note`, sono presenti delle informazioni importanti che utilizzerai per [utilizzare il tuo peer](/docs/services/blockchain/howto/peer_operate_ibp.html).

<!--
### Verifying the peer can connect to Starter or Enterprise Plan network

You can run a peer CLI command from inside the peer container to verify that your peer has connected to your network on the {{site.data.keyword.blockchainfull_notm}} Platform. Complete the following instructions to run the `peer channel fetch` command to fetch the genesis block from a channel:

1. If you have not already connected to your ICP cluster, follow these [instructions](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-kubectl-configure) to connect to it and use the cli from inside the peer container.

2. If you deploy your peer behind a firewall, you need to open a passthru to enable the network on the platform to complete a TlS handshake with your peer. You only need to enable a passthru for the Node port bound to port 7051 of your peer. For more information, see [finding your peer endpoint information](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-endpoint).

3. Your organization needs to be added to a channel in the network before you can fetch the genesis block.

  - You can start a new channel for the peer. If you are using IBP Starter or Enterprise Plan, you can automatically include your organization during [channel creation](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    The peer uploads its signCert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joined and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

4. You also need to upload the signCert of your peer admin to Starter Plan or Enterprise Plan in order for a remote CLI or application to perform channel operations such as fetching the channel genesis block and instantiating chaincode.
    1. On your local machine, cat the file `HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem` and then copy it to the clipboard.
    2. Enter the Network Monitor of your network on {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Click **Members** in the left navigator and click the **Certificates** tab.
    4. Click the **Add Certificate** button.
    5. In the **Add certificate** window, give your certificate a name, such as "peer-admin", paste in the certificate you just copied to the clipboard and click **Submit**.
    6. You will be asked whether you want to restart the peers. Click **Restart**.
    7. Click **Channels** in the left navigator and locate the channel that the peer will join.
    8. Click the three dots under the **Actions** header and click **Sync Certificate**. In the **Sync certificate** window, click **Submit**.

5. Retrieve configuration information from your Connection profile available on the "Overview" screen of your Network Monitor. Click **Connection Profile** and then **Download**.

  - Find the orderer URL by searching for **orderers**, which is located under `orderers > url`. Make a note of the URL that begins with the network name. The URL is similar to the following example:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Find the name of your organization by searching for **organizations**. This should be the same organization as you use to register your peer. You can find your organization's name together with its associated `mspid`. Make a note of the value of the `mspid`.

6. Run the following commands to set the environment variables in the peer container.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Replace the fields with your own information.
  - Replace `<ORDERER_URL>` with the hostname and port of the orderer from the `creds.json` file.
  - Replace `<CHANNEL_NAME>` with the name of the channel that the peer joins.
  - Replace `<CC_NAME>` with any name to refer to your chaincode.
  - Replace `<ORGANIZATION_MSP_ID>` with the name of the organization from the `creds.json` file.
  - Replace `<PEER_ADDR>` with `localhost:7051`

  For example:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=org1
  export PEERADDR=localhost:7051
  ```
  {:codeblock}

7. Run the following peer CLI command to fetch the genesis block of the channel.

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
  ```
  {:codeblock}

  **Note:** It is possible that when you run any of these CLI commands, you might experience the following warning that can be safely ignored.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```

  Verify that the command worked successfully and that genesis block is added to your peer container by running the following command:

  ```
  ls *.block
  ```
  {:codeblock}

  You know that the genesis block is added successfully when you see something that is similar to the following example:

  ```
  defaultchannel_0.block
  ```

  Successfully fetching the genesis block indicates that your peer can connect to your Starter or Enterprise Plan network.
  You still need to join the peer to the channel and install chaincode. See [operating your peer](/docs/services/blockchain/howto/peer_operate_ibp.html) for more information.

-->

## Visualizzazione dei log del peer
{: #peer-ibp-view-logs}

È possibile visualizzare i log del componente dalla riga di comando utilizzando i [`comandi della CLI kubectl`](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster ICP. Per ulteriori informazioni, consulta queste [istruzioni per l'accesso ai log](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-ibp-view-logs).

## Operazioni successive

Dopo aver distribuito il peer, devi completare diversi passi operativi prima di poter inviare transazioni e leggere il libro mastro distribuito dalla rete blockchain. Per ulteriori informazioni, consulta [Utilizzo dei peer con il piano Starter o Enterprise](/docs/services/blockchain/howto/peer_operate_ibp.html).

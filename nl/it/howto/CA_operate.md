---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: IBM Cloud Private, Certificate Authority, operate CA, CA admin secret, CA logs, Helm chart, on-prem, CLI, CA TLS cert, CA endpoint, TLS CA

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Utilizzo di una CA (Certificate Authority) su {{site.data.keyword.cloud_notm}} Private
{: #ca-operate}

Le Autorità di certificazione (CA, Certificate Authority) forniscono le identità sulla rete. Una CA può essere considerata in modo simile a un notaio pubblicamente affidabile e viene utilizzata per stabilire la fiducia tra più parti. A ogni entità nella rete viene fornito un certificato che una CA root firma per incapsulare l'identità digitale dell'entità. Questo certificato è la radice di attendibilità per tutte le operazioni di firma e di verifica eseguite sulla rete.
{:shortdesc}

Ogni organizzazione in una rete blockchain a più cloud dovrebbe distribuire la propria CA. La CA della tua organizzazione firmerà le richieste per i componenti utilizzati dalla tua organizzazione, come ad esempio il servizio di ordinazione, i peer o le applicazioni. Pertanto, devi distribuire la tua CA prima di qualsiasi altro componente.

Devi eseguire alcuni passi preliminari per utilizzare la tua CA (Certificate Authority):

- [Configura le tue CLI](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure)
- [Richiama il tuo URL della CA (Certificate Authority)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url)
- [Scarica il tuo certificato TLS CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)
- [Configura il client CA Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)
- [Genera i certificati con il tuo amministratore CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin)

Dopo aver completato i passi preliminari, puoi utilizzare la tua CA per registrare delle nuove identità nella rete con la tua organizzazione e generare dei certificati che possono essere utilizzati dalle tue applicazioni.

- [Utilizzo della CA per distribuire un ordinante o un peer](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)

Questa guida fornisce anche alcune informazioni concettuali importanti sull'utilizzo della tua CA e su come utilizzare i suoi certificati per gestire una rete blockchain.

- [Membership Service Provider (MSP)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)
- [Certificati TLS](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)

## Prerequisiti
{: #ca-operate-prerequisites}

Sia che tu stia fondando o aderendo a una rete, devi completare questi passi preliminari per utilizzare la tua CA e distribuire altri componenti e identità che appartengono alla tua organizzazione.

### Configurazione delle CLI
{: #ca-operate-kubectl-configure}

Devi utilizzare lo strumento di riga di comando **kubectl** per stabilire una connessione al contenitore CA che viene eseguito in {{site.data.keyword.cloud_notm}} Private.

1. Accedi all'IU del tuo cluster {{site.data.keyword.cloud_notm}} Private. Passa alla scheda **Strumenti di riga comandi** e fai clic su **CLI di Cloud Private**. Vedrai i seguenti strumenti che puoi scaricare.

   * Installa i plug-in e la CLI di IBM Cloud Private
   * Installa la CLI Kubectl
   * Installa Helm
   * Installa la CLI Istio

  Per utilizzare una CA, devi utilizzare i primi **tre** strumenti, tra cui Helm è facoltativo. Fai clic su ciascuno di essi ed esegui i comandi `curl` per il tipo di macchina che stai usando. Immetti quindi i comandi `chmod` e `sudo mv` per ciascuno strumento. Il comando `chmod` modificherà le autorizzazioni della CLI in questione per renderla eseguibile e il comando `sudo mv` sposterà il file e la rinominerà.

  I comandi per il primo strumento, **cloudctl**, potrebbero essere simili al seguente esempio:

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  I comandi per il secondo strumento, **kubectl**, potrebbero essere simili al seguente esempio:

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Dopo aver configurato **kubectl**, immetti il seguente comando:

  ```
  kubectl get pods
  ```
  {:codeblock}

  Se ha esito positivo, il comando dovrebbe restituire una risposta simile al seguente esempio, dove `ca-6cf85f6687-hcxpl` è il nome del contenitore della CA.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Sei ora pronto ad utilizzare lo strumento **kubectl** per richiamare l'URL della CA.

3. Facoltativamente, se vuoi utilizzare **Helm**, completa qualche altro passo. Nota: l'installazione di Helm è facoltativa e non hai bisogno di utilizzarlo in queste istruzioni. Può tuttavia essere utile per gestire le tue release Helm e per creare dei tuoi archivi da distribuire in {{site.data.keyword.cloud_notm}} Private.

  1. Fai clic su "Installa Helm" ed esegui il comando `curl` dall'IU {{site.data.keyword.cloud_notm}} Private.
  2. Decomprimi il file `tar` eseguendo questo comando:  
    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}
  3. Esegui il comando `sudo mv` accodando `/helm` a `darwin-amd64`. Nota: non hai bisogno di eseguire il comando `chmod` per Helm. Ad esempio:  
    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Puoi eseguire il comando `helm help` per confermare che Helm è installato correttamente.

### Richiamo del tuo URL della CA (Certificate Authority)
{: #ca-operate-url}

Devi indicare l'URL della CA per le richieste per generare i certificati o la registrazione con una nuova identità. Puoi trovare l'URL della CA utilizzando l'IU della console {{site.data.keyword.cloud_notm}} Private. Dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Cluster administrator roles and actions") per completare le seguenti istruzioni:

1. Accedi alla tua console {{site.data.keyword.cloud_notm}} Private e fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. Nella sezione **Note**, puoi vedere una serie di comandi per aiutarti a gestire la tua distribuzione della CA.
5. Se non lo hai ancora fatto, segui le istruzioni per configurare la [CLI kubectl](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure), di cui hai bisogno per interagire con il tuo contenitore della CA.
6. Nella tua CLI, esegui il primo comando nella nota, che segue **1. Get the application URL by running these commands:** Questo comando riproduce a schermo l'URL e la porta della CA, che è simile al seguente esempio. Salva questo valore come l'URL dell'applicazione da utilizzare con i comandi successivi.

  ```
  http://9.30.94.174:30167
  ```

**Nota:** se distribuisci la tua CA dietro un firewall, devi aprire una passthru per abilitare la rete sulla piattaforma per completare un handshake TLS con la tua CA. Devi abilitare un passthru solo per la porta che è stata concatenata con l'URL della CA.


### Richiamo del tuo certificato TLS CA
{: #ca-operate-tls}

Devi scaricare il tuo certificato TLS CA e trasmetterlo insieme ai tuoi comandi per comunicare con la tua CA da un client remoto.

1. Accedi alla tua console {{site.data.keyword.cloud_notm}} Private. Fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. Nella sezione **Note**, puoi vedere una serie di comandi per aiutarti a gestire la tua distribuzione della CA.
5. Se non lo hai ancora fatto, segui le istruzioni per configurare la [CLI kubectl](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure), di cui hai bisogno per interagire con il tuo contenitore della CA.
6. Nella tua CLI, esegui i comandi nella terza nota, che segue **3. Get TLS certificate:** Questo comando salva i tuoi certificati TLS come file `tls.pem` sulla tua directory locale. Devi fare riferimento a questo certificato in un comando futuro. Prendi nota della sua ubicazione.
7. Il comando converte inoltre il certificato nel formato base64 e lo riproduce sullo schermo. Il risultato è simile alla seguente stringa:

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  Devi copiare questa stringa e salvarla come tuo **certificato TLS CA {{site.data.keyword.cloud_notm}} Private**. Lo utilizzerai quando distribuisci degli ulteriori componenti.
  {:important}

### Configurazione del client CA Fabric
{: #ca-operate-fabric-ca-client}

Puoi utilizzare il client CA Fabric per utilizzare la tua CA. Queste istruzioni spiegano come utilizzare il client CA Fabric per iscrivere e registrare le identità per altri componenti che appartengono alla tua organizzazione. Puoi anche utilizzare gli SDK Fabric per completare i passi preliminari.

1. Devi scaricare il [Fabric CA client ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Download Fabric CA client") sul tuo file system locale.

  Il modo più semplice per ottenere il client CA Fabric consiste nello scaricare direttamente tutti i file binari dello strumento Fabric. Passa a una directory dove vuoi scaricare i file binari con la tua riga di comando e recuperali immettendo il seguente comando [Curl ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl").

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
  ```
  {:codeblock}

  Questo comando installa i file binari in una directory `bin/`.

2. Imposta il percorso `PATH` alla directory dove hai scaricato i file binari dello strumento Fabric:

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  Ad esempio, se hai installato i file binari nella tua directory home, imposterai il tuo `PATH` come:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Crea la cartella dove archivierai i certificati del tuo amministratore della CA.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. Imposta il valore della variabile di ambiente `$FABRIC_CA_CLIENT_HOME` in modo che sia il percorso dove il client CA archivierà i certificati [MSP](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) generati. Assicurati di rimuovere il materiale di configurazione che potrebbe essere stato creato da dei tentativi precedenti. Se precedentemente non hai eseguito il comando `enroll`, la cartella `msp` e il file `.yaml` non esistono.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Copia il certificato TLS che hai [scaricato da {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) in una directory a cui puoi fare riferimento in comandi futuri. Assicurati di essere nella tua directory `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### Generazione dei certificati con il tuo amministratore CA
{: #ca-operate-enroll-ca-admin}

Prima di distribuire i componenti o le applicazioni client con la tua CA, devi generare i certificati che ti autenticano come un amministratore con la capacità di registrare dei nuovi utenti. Il processo di generazione dei certificati necessari, della tua chiave privata e del tuo certificato (noto anche come certificato di iscrizione o signCert) viene denominato **iscrizione**.

Puoi generare i certificati solo utilizzando le identità che sono state registrate con la tua CA (Certificate Authority). Fornisci il nome e il segreto dell'identità per generare i certificati. Un'identità **amministratore CA** è stata automaticamente registrata per te quando hai distribuito la tua CA. Puoi ora utilizzare tali nome e password amministratore per emettere un comando `enroll` con il client CA Fabric per generare una cartella MSP con i certificati che vengono poi utilizzati per registrare altre identità ordinanti o peer.

1. Assicurati di completare le istruzioni per [configurare il client CA Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) e che `$FABRIC_CA_CLIENT_HOME` sia impostata sulla directory in cui vuoi archiviare i tuoi certificati di amministrazione della CA.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. Immetti il seguente comando per generare i certificati:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  `<enroll_id>` e `<enroll_password>` nel comando sono [il nome utente e la password dell'amministratore CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret) che hai passato al segreto Kubernetes quando hai distribuito la CA (Certificate Authority). Inserisci l'[URL della CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) in `<ca_url_with_port>`. Tralascia il `http://` all'inizio. `<ca_name>` è il valore che hai fornito al campo `CA Name` quando hai distribuito la CA.

  `<ca_tls_cert_path>` è il percorso completo del tuo [certificato TLS CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls).

  Una vera chiamata potrebbe essere simile al seguente comando di esempio:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  **Suggerimento:** se il valore dell'URL di iscrizione, ossia il valore del parametro `-u`, contiene un carattere speciale, devi codificare il carattere speciale o racchiudere l'URL tra virgolette singole. Ad esempio, `!` diventa `%21` o il comando è simile a:

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
  ```
  {:codeblock}

  Il comando `enroll` genera una serie completa di certificati, nota come cartella MSP (Membership Service Provider), ubicata nella directory in cui hai impostato il percorso su `$HOME` per il tuo client CA Fabric. Ad esempio, `$HOME/fabric-ca-client/ca-admin`. Per ulteriori informazioni sulle MSP e su cosa la cartella MSP contiene, consulta [MSP (Membership Service Provider)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp).

  Se il comando `enroll` ha esito negativo, consulta l'[argomento Risoluzione dei problemi](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting) per le cause possibili.

Puoi eseguire un comando tree per verificare di aver completato tutti i passi preliminari. Passa alla directory dove hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── catls
    └── tls.pem    
```

## Utilizzo della CA per distribuire un ordinante o un peer
{: #ca-operate-deploy-orderer-peer}

Prima di distribuire un ordinante o un peer, devi creare un file JSON di configurazione che contiene le informazioni importanti sull'identità del componente e la tua CA. Successivamente, devi passare questo file al grafico Helm durante la configurazione utilizzando un oggetto [segreto Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/configuration/secret/). Questo file consente al tuo ordinante o peer di ottenere i certificati di cui ha bisogno dalla CA per aderire a una rete blockchain. Questo file contiene inoltre un certificato di gestione che ti consente di utilizzare il tuo componente come un utente amministratore.

Le seguenti istruzioni ti forniscono un [file di configurazione JSON template](/docs/services/blockchain/howto/CA_operate.html#ca-operate-config-file-template) che puoi modificare e salvare sul tuo file system locale e che ti guida su come utilizzare la tua CA per completare questo file.

- Attieniti alle seguenti istruzioni se stai distribuendo un ordinante su {{site.data.keyword.cloud_notm}} Private o un peer per la connessione a un consorzio ospitato su {{site.data.keyword.cloud_notm}} Private.
- Se vuoi distribuire un peer per la connessione al piano Starter o Enterprise, segui invece le istruzioni sulla [Distribuzione dei peer in IBM Cloud Private per il collegamento al piano Starter o Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy). Questi passi illustrano come utilizzare la CA su {{site.data.keyword.blockchainfull_notm}} Platform per distribuire il nostro peer su {{site.data.keyword.cloud_notm}} Private.

### File di configurazione
{: #ca-operate-config-file-template}

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

Copia questo file completo in un editor di testo in cui puoi modificarlo e salvarlo sul tuo file system locale come un file JSON.

### Informazioni connessione CA

Per prima cosa, devi fornire le informazioni di connessione della tua CA su {{site.data.keyword.cloud_notm}} Private. Utilizza le seguenti informazioni per completare i valori nella sezione `"component"` del file:

- I valori `"cahost"` e `"caport"` sono l'URL e la porta dall'[URL della CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url). Ad esempio, se il tuo URL della CA è http://9.30.94.174:30167, il valore di `"cahost"` sarà `9.30.94.174` e `"caport"` sarà `30167`.
- `"caname"` è il nome della CA che è stato specificato durante la distribuzione del grafico Helm CA. Hai fatto riferimento al nome della CA nel tuo comando quando hai eseguito l'iscrizione utilizzando l'[identità di amministratore della CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin).
- `"cacert"` è un certificato TLS codificato base64 della tua CA. [Richiama il certificato TLS della tua CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) dalla console {{site.data.keyword.cloud_notm}} Private utilizzando la nota nella tua release di Helm e inserisci l'output base64 dei comandi di nota nella seguente sezione.
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

- I campi nella seguente sezione `"tls"` richiedono le stesse informazioni che hai passato alle sezioni precedenti del componente, con l'eccezione che devi utilizzare il valore del nome dell'istanza TLS CA specificato durante la distribuzione della CA. Utilizza lo stesso certificato TLS per il valore `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

Dopo aver richiamato le tue informazioni di connessione della CA (Certificate Authority), devi utilizzare il client CA Fabric per completare diversi passi operativi:

1. [Registra il nuovo componente con la tua CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-component).

2. Devi anche [registrare un amministratore del componente](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin) e successivamente [generare i certificati per l'amministratore del componente](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin) in una cartella MSP. Non devi completare questo passo se hai già registrato un amministratore per distribuire un altro componente.

3. [Registra il nuovo componente con la tua CA TLS](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls-register-component).

### Registrazione dell'identità del componente con la CA
{: #ca-operate-register-component}

Se vuoi formare un consorzio distribuendo un sevizio ordini e aggiungendogli delle organizzazioni, oppure per distribuire i peer e unirli ai canali, devi prima registrare l'identità del componente con la tua CA. La distribuzione del tuo componente può quindi generare i certificati necessari al peer o all'ordinante per partecipare a una rete.

1. [Genera i certificati con il tuo amministratore CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin) utilizzando il client CA Fabric. Utilizza questi certificati di gestione per emettere i seguenti comandi. Assicurati che `$FABRIC_CA_CLIENT_HOME` sia impostato su `$HOME/fabric-ca-client/ca-admin`.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. Immetti il seguente comando per trovare il nome della tua affiliazione e della tua organizzazione.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Il tuo comando è simile al seguente esempio:
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Dovresti visualizzare informazioni simili al seguente esempio:
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  Prendi nota del secondo valore **affiliation**, ad esempio, `org1.department1`. Dovrai utilizzare questo valore nel comando riportato di seguito.

3. Immetti il seguente comando per registrare il peer o l'ordinante.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Crea un nome e una password per il componente e utilizzali per sostituire `name` e `secret`. **Importante:** prendi nota di queste informazioni. Imposta `--id.type` su `orderer` se stai distribuendo un ordinante, oppure su `peer` se stai distribuendo un peer. Il comando potrebbe essere simile al seguente esempio:

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Dopo aver eseguito il comando, devi immettere `name` e `secret` come `"enrollid"` e `"enrollsecret"` nel file di configurazione, nella sezione `"component"`:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```

  Puoi registrare un'entità solo una volta. Se riscontri un problema, prova un comando con un nuovo nome utente e password. Come misura di sicurezza, utilizza ogni identità e l'enrollID e il segreto di accompagnamento, per distribuire solo un peer. Mentre puoi utilizzare un'identità **amministratore** per diversi componenti (questa è la nostra strategia di distribuzione consigliata), non riutilizzare gli ID e le password del peer.

  Se il comando viene completato correttamente, dovresti visualizzare informazioni simili al seguente esempio:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### Registrazione dell'amministratore
{: #ca-operate-register-admin}

Dopo aver registrato il componente, devi anche creare un'identità amministratore che puoi utilizzare per gestire il componente. Per prima cosa, devi registrare questa nuova identità con la tua CA e utilizzarla per generare una cartella MSP. Successivamente, devi aggiungere il signCert degli utenti amministratore al file di configurazione, dove verrà creato un certificato di gestione dell'ordinante o del peer durante la distribuzione. Questo ti consente di utilizzare i certificati dell'identità amministratore per utilizzare la tua rete blockchain, come ad esempio per avviare un nuovo canale o per installare il chaincode sui tuoi peer.

Devi creare solo un'identità amministratore per i componenti che appartengono alla tua organizzazione. Se stai distribuendo più peer o un ordinante, devi completare questa procedura solo una volta. Puoi utilizzare il signCert che hai generato per un componente per distribuire tutti i peer o l'ordinante.

Assicurati che il tuo `$FABRIC_CA_CLIENT_HOME` sia impostato sul percorso alla MSP del tuo amministratore della CA.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Registra l'identità di amministrazione del componente con la CA immettendo il seguente comando:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Crea dei nuovi `name` e `secret` dell'identità utente per l'amministratore. Questa identità verrà resa l'amministratore di tutti i componenti che distribuisci. Assicurati di utilizzare dei valori differenti rispetto a quelli dell'identità peer o ordinante che hai appena registrato. Il comando è simile al seguente esempio.

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**Importante:** prendi nota di queste informazioni. Utilizzerai questi `name` e `secret` per generare la cartella MSP utilizzando il comando `enroll`.

#### Generazione della cartella MSP dell'amministratore
{: #ca-operate-enroll-admin}

Dopo aver registrato l'amministratore del componente, puoi generare i certificati utilizzando il seguente comando:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Ad esempio:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Dopo che questo comando è stato completato correttamente, genera una nuova cartella MSP nella directory che hai specificato utilizzando l'indicatore `-M`. Questa directory contiene i certificati che utilizzerai per gestire i tuoi componenti. Puoi verificare che il comando enroll abbia funzionato correttamente utilizzando un comando tree. Passa alla directory in cui hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:


```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```


In questa directory MSP, apri il file signCert del nuovo utente e convertilo nel formato base64 utilizzando i seguenti comandi:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
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

Ad esempio:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

Questo comando riproduce a schermo una stringa simile al seguente esempio:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

Copia questa stringa nel campo `"admincerts"` nella sezione del componente nel file di configurazione.

#### Registrazione dell'identità del componente con la CA TLS
{: #ca-operate-tls-register-component}

Devi registrare anche l'ordinante o il peer con la CA TLS. Per far ciò, dovrai prima eseguire l'iscrizione utilizzando l'amministratore della CA TLS. Modifica `$FABRIC_CA_CLIENT_HOME` con una directory in cui vuoi archiviare i tuoi certificati di amministrazione della CA TLS.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Immetti il seguente comando per l'iscrizione come amministratore per la CA TLS. Il comando è lo stesso che hai utilizzato per l'iscrizione come [amministratore della CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin), soltanto tu utilizzerai il nome dell'istanza della CA TLS che hai specificato durante la [configurazione della CA.](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms) Assicurati di utilizzare gli stessi `ca-admin-name` e `ca-admin-password` nel tuo segreto della CA.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Una chiamata reale potrebbe essere simile al seguente esempio:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Dopo aver eseguito l'iscrizione, disponi dei certificati necessari per registrare il tuo componente con la CA TLS. Immetti il seguente comando per registrare il peer o l'ordinante:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Questo comando è simile a quello utilizzato per registrare l'identità del componente con la CA, con l'eccezione di utilizzare il nome della CA TLS. Se stai distribuendo un ordinante invece di un peer, imposta `--id.type` su `orderer` invece di `peer`. Devi fornire a questa identità un nome utente e una password diversi rispetto a quelli utilizzati con la CA predefinita. Una registrazione reale è simile al seguente comando:

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Copia i valori di `name` e `secret` con `"enrollid"` e `"enrollsecret"` nella sezione `"tls"` del file di configurazione.

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### Host della richiesta di firma del certificato (CSR)
{: #ca-operate-csr-hosts}

Devi fornire un nome host della CSR per distribuire un ordinante o un peer. Questo nome host include l'indirizzo IP proxy del cluster in cui distribuirai il componente e il nome host del servizio (`service host name`) generato quando viene distribuito il grafico Helm.

#### Individuazione del valore dell'indirizzo IP proxy del cluster
{: #ca-operate-cluster-proxy-ip}

Se vuoi distribuire un ordinante o un peer sullo stesso cluster {{site.data.keyword.cloud_notm}} Private in cui hai distribuito la tua CA, assicurati di avere il ruolo di [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Cluster administrator roles and actions") sul cluster {{site.data.keyword.cloud_notm}} Private in cui sarà distribuito il peer o l'ordinante. Successivamente, immetti lo stesso IP proxy che hai utilizzato quando [hai configurato la tua CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms). Se vuoi distribuire l'ordinante o il peer su un cluster diverso, puoi richiamare il valore dell'indirizzo IP proxy del cluster dalla console {{site.data.keyword.cloud_notm}} Private completando la seguente procedura:

1. Accedi alla console {{site.data.keyword.cloud_notm}} Private. Nel pannello di navigazione di sinistra, fai clic su **Piattaforma** e quindi su **Nodi** per visualizzare i nodi definiti nel cluster.
2. Fai clic sul nodo con il ruolo `proxy` e copia il valore dell'`IP host` dalla tabella.
3. Inserisci l'`IP host` come il valore per `"hosts"` nella sezione `"csr"` del seguente file di configurazione:

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### Determinazione del nome host del servizio

Viene generato un nome host del servizio (`service host name`) quando viene distribuito un grafico Helm. Si basa sul nome della release Helm (`helm release name`) che utilizzi quando distribuisci il grafico Helm.

- Se stai distribuendo un ordinante, il nome host del servizio (`service host name`) è il nome della release Helm (`helm_release_name`) che specifichi durante la distribuzione con la stringa `-orderer` aggiunta alla fine. Ad esempio, se il tuo indirizzo proxy IP del cluster è `9.42.134.44` e specifichi un nome della release Helm (`helm release name`) di `org1orderer`, puoi inserire i seguenti valori nella sezione `"csr"` del file:

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- Se stai distribuendo un peer, il nome host del servizio (`service host name`) è il nome della release Helm (`helm release name`) che specifichi durante la distribuzione senza una stringa supplementare. Ad esempio, se l'indirizzo proxy IP del cluster è 9.42.134.44" e il nome della release Helm (`helm release name`) è `org1peer1`, puoi inserire i seguenti valori in csr "hosts":

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
{: #ca-operate-config-file}

Dopo aver completato tutti i precedenti passi, il tuo file di configurazione aggiornato potrebbe essere simile al seguente esempio:


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer1",
            "enrollsecret": "peer1pw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}



Puoi lasciare gli altri campi vuoti. Salva questo file perché ne hai bisogno quando distribuisci un [ordinante](/docs/services/blockchain/howto/orderer_deploy_icp.html) o un [peer](/docs/services/blockchain/howto/peer_deploy_ibp.html).

## Membership Service Provider (MSP)
{: #ca-operate-msp}

I componenti di {{site.data.keyword.blockchainfull_notm}} Platform utilizzano le identità tramite gli MSP (Membership Service Provider). Le MSP associano i certificati emessi dalle CA ai ruoli e alle autorizzazioni. Per ulteriori informazioni sulle MSP, vedi [l'argomento sull'appartenenza nella documentazione di Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "l'argomento sull'appartenenza nella documentazione di Hyperledger Fabric").

Le cartelle MSP devono avere una struttura definita in modo da essere utilizzate dai componenti Fabric. Il client CA Fabric stabilisce questa struttura creando le seguenti cartelle MSP:

- **cacerts:** contiene il certificato della CA root della tua rete.
- **intermediatecerts:** questi sono i certificati di tutte le CA intermedie nella tua catena di attendibilità (che riconducono a una CA root o alle CA). Ogni organizzazione del piano Enterprise ha due CA intermedie per il failover e l'alta disponibilità.
- **signCerts:** questa cartella contiene il tuo certificato di firma, noto anche come signCert o certificato di iscrizione. Questo certificato viene allegato alle chiamate in rete (ad esempio, un richiamo del chaincode) quando fai riferimento alla tua directory MSP dalla riga di comando o crei un oggetto di contesto utente con gli SDK. Puoi caricare questo certificato su {{site.data.keyword.blockchainfull_notm}} Platform se vuoi gestire una rete dall'SDK o dalla riga di comando.
- **keystore:** questa cartella contiene la tua chiave privata. Questa chiave viene utilizzata per firmare le chiamate alla rete quando fai riferimento alla tua directory MSP dalla riga di comando o crei un oggetto di contesto utente con gli SDK, ma non viene collegata alle chiamate nel modo di un signCert. È **cruciale** che questa chiave sia tenuta al sicuro. Se viene compromessa, può essere utilizzata per impersonare la tua identità.

Puoi anche creare una cartella MSP a cui il client CA Fabric può fare riferimento utilizzando le API Swagger e il Monitoraggio della rete.

- **cacerts** e **intermediatecerts**: puoi recuperare questi certificati con le [API Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) inviando una richiesta `Get` all'API MSP.
- **signCerts** e **keystore**: puoi generare questi certificati facendo clic sul pulsante **Genera certificati** nel pannello "CA (Certificate Authority)". Si apre una finestra a comparsa con due certificati elencati. Copia e memorizza il **Certificato** in signCerts e la **Chiave privata** nel keystore. Conserva questi certificati in un luogo sicuro perché non vengono memorizzati sulla piattaforma.

Molti componenti Fabric contengono informazioni aggiuntive all'interno della propria cartella MSP. Ad esempio, se gestisci un peer remoto, potresti vedere le seguenti cartelle:

- **admincerts:** questa cartella contiene l'elenco di amministratori per questa organizzazione o questo componente. Devi caricare il tuo signCert in questa cartella se stai gestendo un peer remoto dalla riga di comando o dagli SDK. Se utilizzi il client CA Fabric, hai bisogno anche di una cartella admincerts nella tua MSP che contiene il signCert corrispondente che deve essere riconosciuto come certificato di amministrazione.
- **tls:** questa è una cartella in cui memorizzi i certificati TLS utilizzati per la comunicazione con altri componenti di rete.

Per ulteriori informazioni sulla struttura dei MSP, vedi [Membership ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Membership") e [Membership Service Providers ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/msp.html "Membership Service Providers") nella documentazione di Hyperledger Fabric.


## Visualizzazione dei log CA
{: #ca-operate-view-logs}

È possibile visualizzare i log dei componenti dalla riga di comando utilizzando i [`comandi della CLI kubectl CLI`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster {{site.data.keyword.cloud_notm}} Private.

- Utilizza il comando `kubectl logs` per visualizzare i log dei contenitori all'interno del pod. Se non sei sicuro del tuo nome di pod, esegui questo comando per visualizzare il tuo elenco di pod.

   ```
   kubectl get pods
   ```
   {:codeblock}

   Successivamente, immetti il seguente comando per richiamare i log per il contenitore della CA che risiede all'interno del pod sostituendo `pod_name` con il nome del tuo pod dal precedente output del comando:

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   Per ulteriori informazioni sul comando `kubectl logs`, vedi la [documentazione di Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- In alternativa, puoi accedere ai log e agli eventi di distribuzione utilizzando la [console di gestione del cluster {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/troubleshoot/events.html), che apre i log in Kibana.

  **Nota:** quando visualizzi i tuoi log in Kibana, potresti ricevere la risposta `No results found`. Questa condizione può verificarsi se {{site.data.keyword.cloud_notm}} Private utilizza l'indirizzo IP del tuo nodo di lavoro come suo nome host. Per risolvere questo problema, rimuovi il filtro che inizia con `node.hostname.keyword` nella parte superiore del pannello e i log diventeranno visibili.

## Sicurezza
{: #ca-operate-security}

"Se viene emesso solo un numero limitato di certificati (ad esempio, se vengono emessi certificati di soli peer, server Node.js e applicazioni client), la CA potrebbe venire lasciata offline per garantire maggiore sicurezza ed evitare di compromettere il materiale chiave della CA. Tuttavia, la CA dovrebbe essere online quando è necessaria l'emissione di certificati su richiesta agli utenti. Ti consigliamo fortemente di proteggere la tua chiave privata di gestione CA con [HSM](/docs/services/blockchain/glossary.html#glossary-hsm) se possibile.

## Risoluzione dei problemi
{: #ca-operate-troubleshooting}

### **Problema** errore durante l'esecuzione del comando `enroll`
{: #ca-operate-enroll-error1}

Quando esegui il comando enroll del client CA Fabric, è possibile che abbia esito negativo con il seguente errore:

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**Soluzione:**

Questo errore può verificarsi quando il tuo client CA Fabric tenta l'iscrizione ma non può collegarsi alla tua CA. Questo può succedere se:   

- Il tuo comando `enroll` contiene un ulteriore `https://` nel parametro `-u`.
- Il tuo nome della CA non è corretto.
- Il nome utente o la password non è corretto.

Rivedi i parametri che hai specificato nel tuo comando `enroll` e assicurati che non sia presente alcuna di queste condizioni.

### **Problema:** errore con l'URL della CA quando si esegue il comando `enroll`
{: #ca-operate-enroll-error2}

Il comando enroll del client CA Fabric può avere esito negativo se l'URL di iscrizione, ossia il valore del parametro `-u`, contiene un carattere speciale. Ad esempio, il seguente comando con l'ID di iscrizione e la password di `admin:C25A06287!0`,

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

avrà esito negativo e produrrà il seguente errore:

```
!pw@9.12.19.115: event not found
```

### **Soluzione:**
{: #ca-operate-enroll-error2-solution}

Devi codificare il carattere speciale o racchiudere l'URL tra virgolette singole. Ad esempio, `!` diventa `%21` o il comando è simile a:

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

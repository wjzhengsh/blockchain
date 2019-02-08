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

# Gestione dei peer su {{site.data.keyword.cloud_notm}} Private con piano Starter o Enterprise 
{: #peer-operate_icp}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Dopo aver configurato un peer di {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private (ICP), devi completare diversi passi operativi prima che il tuo peer possa inviare transazioni a una rete del piano Starter o Enterprise. I passi includono l'aggiunta della tua organizzazione a un canale, l'unione del tuo peer al canale, l'installazione del chaincode sul peer, l'istanziazione del chaincode sul canale e la connessione delle applicazioni al peer.
{:shortdesc}

Per gestire il tuo peer devi prima completare alcune operazioni preliminari dal tuo cluster ICP.

**Prerequisiti:**
- [Configura le tue CLI](#peer-kubectl-configure)
- [Richiama le tue informazioni dell'endpoint del peer](#peer-endpoint)
- [Scarica il tuo certificato TLS del peer](#peer-tls)

Puoi quindi utilizzare uno dei seguenti metodi per gestire il tuo peer.

**Percorsi operativi:**
- [Utilizzo degli SDK Fabric](#peer-operate-with-sdk)
- [Utilizzo della riga di comando](#peer-cli-operate)

Gli SDK Fabric sono il percorso consigliato, sebbene le istruzioni presuppongano la familiarità con il funzionamento dell'SDK. Se preferisci utilizzare la riga di comando, puoi utilizzare il client peer Fabric.

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](/docs/services/blockchain/howto/peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](/docs/services/blockchain/v10_application.html#ha-app).
-->

**Nota**: un peer di {{site.data.keyword.blockchainfull_notm}} Platform non ha accesso alle funzionalità o al supporto completi dei peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform. Di conseguenza, non puoi utilizzare il Monitoraggio della rete per gestire un peer su ICP. Prima di iniziare a eseguire i peer, assicurati di aver esaminato [considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

## Prerequisiti

Sia che tu scelga di utilizzare gli SDK o la riga di comando, devi completare i passi indicati in questo corso per la gestione del tuo peer. Puoi completare tutta questa procedura prima di iniziare. 

### Configurazione delle CLI
{: #peer-kubectl-configure}

Devi utilizzare lo strumento di riga di comando **kubectl** per il collegamento al contenitore del peer eseguito in ICP.

1. Accedi alla tua IU cluster ICP. Passa alla scheda **Strumenti di riga comandi** e fai clic su **CLI di Cloud Private**. Vedrai i seguenti strumenti che è possibile scaricare.

   * Installa la CLI e i plugin IBM Cloud Private
   * Installa la CLI Kubectl
   * Installa Helm
   * Installa la CLI Istio

  Per utilizzare un peer, devi utilizzare i primi **tre** strumenti, tra cui Helm è facoltativo. Fai clic su ognuno di essi ed esegui i comandi `curl` per il tipo di macchina che stai utilizzando. Successivamente, immetti i comandi `chmod` e `sudo mv` per ogni strumento. Il comando `chmod` modificherà l'autorizzazione della CLI in questione per renderla eseguibile e il comando `sudo mv` sposterà e ridenominerà il file.

  I comandi per il primo strumento **cloudctl** possono essere simili al seguente esempio:

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  I comandi per il secondo strumento **kubectl** possono essere simili al seguente esempio:

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

  Se ha esito positivo, il comando restituisce una risposta simile al seguente esempio, dove `peer-6cf85f6687-hcxpl` è il nome del contenitore del peer.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Sei ora pronto ad utilizzare lo strumento **kubectl** per richiamare le informazioni sull'endpoint del peer.

3. Facoltativamente, se vuoi utilizzare **Helm**, completa alcuni ulteriori passi. Tieni presente che l'installazione di Helm è facoltativa e non hai bisogno di utilizzarlo in queste istruzioni. Tuttavia, può essere utile gestire le tue release Helm e creare i tuoi archivi per la distribuzione in ICP.

  1. Fai clic su "Installa Helm" ed esegui il comando `curl` dall'IU ICP.
  2. Decomprimi il file `tar` immettendo il seguente comando:

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. Esegui il comando `sudo mv` accodando `/helm` a `darwin-amd64`. Tieni presente che non devi eseguire il comando `chmod` per Helm. Ad esempio:

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Puoi eseguire il comando `helm help` per confermare che Helm sia stato installato correttamente.

### Richiamo delle informazioni sull'endpoint del peer
{: #peer-endpoint}

Devi indicare come destinazione il tuo endpoint del peer dall'SDK o dal client CA Fabric per unirti al canale o installare gli smart contract. Puoi trovare l'endpoint del tuo peer utilizzando la tua IU della console ICP. Dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Cluster administrator roles and actions") per completare le seguenti istruzioni:

1. Accedi alla tua console ICP e fai clic sull'icona **Menu** nell'angolo in alto a sinistra.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. Nella sezione **Note**, puoi vedere una serie di comandi per aiutarti a gestire la tua distribuzione del peer.
5. Attieniti alla procedura per configurare la [CLI kubeclt](#peer-kubectl-configure), se non l'hai già fatto. Utilizzerai kubectl per interagire con il tuo contenitore peer. 
6. Nella tua CLI, esegui il primo comando nella nota, che segue **1. Get the application URL by running these commands:** Questo comando riprodurrà a schermo l'URL e la porta del peer, che è simile al seguente esempio. Salva questo URL per i comandi futuri. 

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Nota:** se distribuisci il tuo peer dietro un firewall, devi aprire una passthru per abilitare la rete sulla piattaforma per completare un handshake TlS con il tuo peer. Devi abilitare una passthru solo per la porta del nodo associato alla porta 7051 del tuo peer. Per ulteriori informazioni, vedi [ricerca delle informazioni sul tuo endpoint del peer](#peer-endpoint).

### Scarica il tuo certificato TLS del peer 
{: #peer-tls}

Devi scaricare il certificato TLS del tuo peer e passarlo ai tuoi comandi per comunicare con il tuo peer da un client remoto. 

1. Accedi alla tua console ICP e fai clic sull'icona **Menu** nell'angolo in alto a sinistra.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. Nella sezione **Note**, puoi vedere una serie di comandi per aiutarti a gestire la tua distribuzione del peer.
5. Attieniti alla procedura per configurare la [CLI kubeclt](#peer-kubectl-configure), se non l'hai già fatto. Utilizzerai kubectl per interagire con il tuo contenitore peer. 
6. Nella tua CLI, esegui il primo comando nella nota, che segue **3. Get peer TLS root cert file**. Questo comando salverà il tuo certificato TLS come il file `cacert.pem` sulla tua macchina locale.
7. Sposta il certificato nell'ubicazione dove puoi fare riferimento a esso nei comandi futuri e rinominalo come `peertls.pem`. 

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Utilizzo di SDK Fabric per gestire il peer
{: #peer-operate-with-sdk}

Gli SDK Hyperledger Fabric forniscono una potente serie di API che consentono alle applicazioni di interagire con le reti blockchain e di gestire tali reti. Puoi trovare l'elenco più aggiornato dei linguaggi supportati e l'elenco completo delle API disponibili all'interno degli SDK Fabric nella [documentazione della community Hyperledger Fabric SDK ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Documentazione della community Hyperledger Fabric SDK"). Puoi utilizzare gli SDK Fabric per unire il tuo peer a un canale su {{site.data.keyword.blockchainfull_notm}} Platform, installare un chaincode sul tuo peer e istanziare il chaincode su un canale.

Le seguenti istruzioni utilizzano l'[SDK Node Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node Fabric") per gestire il peer e assumere una certa familiarità con l'SDK. Puoi utilizzare l'[esercitazione sullo sviluppo di applicazioni](/docs/services/blockchain/v10_application.html) per imparare a utilizzare l'SDK Node prima di iniziare e come guida allo sviluppo di applicazioni con il tuo peer quando sei pronto a richiamare e interrogare il chaincode.

### Installazione dell'SDK Node

Puoi utilizzare NPM per installare l'[SDK Node ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node"):

```
npm install fabric-client@1.2
```
{:codeblock}

Si consiglia di utilizzare la versione 1.2 dell'SDK Node.

### Preparazione dell'SDK per lavorare con il peer
{: #remote-peer-node-sdk}

Il tuo peer viene distribuito con all'interno il signCert del tuo amministratore del peer. Questo ti consente di utilizzare la cartella MSP e i certificati dell'amministratore del peer per gestirlo.

Localizza i certificati che hai creato quando hai [iscritto il tuo amministratore del peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#enroll-admin). Se hai utilizzato i comandi di esempio, puoi trovare la tua cartella MSP di gestione del peer in `$HOME/fabric-ca-client/peer-admin`.
  - Puoi creare il contesto utente di gestione del peer con l'SDK utilizzando il signCert (chiave pubblica) e la chiave privata nella cartella MSP. Puoi trovare queste chiavi nelle seguenti ubicazioni:
    - Il signCert può essere trovato nella cartella **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - La chiave privata può essere trovata nella cartella **keystore:**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    Puoi trovare un esempio di come creare un contesto utente e gestire l'SDK utilizzando solo la chiave pubblica e privata in [questa sezione dell'esercitazione di sviluppo delle applicazioni](/docs/services/blockchain/v10_application.html#enroll-panel).

Puoi anche trovare l'SDK per generare la chiave privata e il signCert di gestione del peer utilizzando le informazioni sull'endpoint della CA sul piano Starter o Enterprise e i tuoi [nome utente e password di gestione del peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#register-admin).

### Caricamento del signCert dell'amministratore del peer nel piano Starter o Enterprise
{: #remote-peer-upload-sdk}

Devi caricare il signCert dell'amministratore del peer nella rete su {{site.data.keyword.blockchainfull_notm}} Platform in modo che la tua applicazione abbia l'autorizzazione per utilizzare la rete.

- Puoi trovare il tuo signCert nella directory in cui l'SDK ha generato il tuo materiale crittografico, nel file denominato dopo peer admi. Copia il certificato all'interno delle virgolette dopo il campo `certificate`, che inizia con  `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE-----`. Puoi utilizzare la CLI per convertire il certificato in formato PEM immettendo il comando `echo -e "<CERT>" > admin.pem`. Puoi quindi incollare il contenuto del certificato nella tua rete blockchain utilizzando il Monitoraggio della rete. Accedi alla rete su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata "Membri" del Monitoraggio della rete, fai clic su **Certificati** > **Aggiungi certificato**. Specifica un qualsiasi nome per il certificato e incolla il contenuto nel campo **Certificato**. Fai clic su **Riavvia** quando ti viene richiesto se vuoi riavviare i tuoi peer. Questa azione deve essere eseguita prima che la tua organizzazione si unisca al canale.

### Passaggio del certificato TLS del tuo peer all'SDK
{: #icp-peer-download-tlscert}

Devi fare riferimento al certificato TLS dei tuoi peer per autenticare la comunicazione con il tuo SDK. Individua il certificato TLS che hai [scaricato dal tuo contenitore peer](#peer-tls) e salvalo dove la tua applicazione può fare riferimento ad esso. Se hai utilizzato i comandi di esempio, puoi trovare il certificato TLS del tuo peer in `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Puoi quindi importare il certificato TLS nella tua applicazione utilizzando un semplice comando di lettura file.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Trasmissione delle informazioni sull'endpoint del peer all'SDK
{: #peer-SDK-endpoints}

Trova le [informazioni sull'endpoint del tuo peer](#peer-endpoint) e forniscile all'SDK dichiarando una nuova variabile peer o aggiornando il tuo Profilo connessione. Il seguente esempio definisce il peer come endpoint sulla tua rete fabric e lo trasmette al certificato TLS che hai importato.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**Nota:** poiché il peer è all'esterno di {{site.data.keyword.cloud_notm}, le altre organizzazioni sulla rete {{site.data.keyword.blockchainfull_notm}} Platform non saranno in grado di trovare le informazioni sull'endpoint del tuo peer nel loro Profilo connessione. Se altre organizzazioni devono inviare al tuo peer una transazione da approvare, devi fornire loro l'URL del peer in un'operazione fuori banda.

### Utilizzo dell'SDK per l'adesione a un canale
{: #peer-join-channel-sdk}

La tua organizzazione deve essere un membro di un canale prima che tu possa unirti al canale con il tuo peer. 

  - Puoi avviare un nuovo canale per il peer. Se sei l'iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [channel update](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Una volta che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il signCert del tuo peer al canale in modo che gli altri membri possano verificare la tua firma digitale durante le transazioni. Il peer carica il proprio signCert durante l'installazione, il che significa che devi solo sincronizzare il certificato sul canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si unisce la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale. Prima di immettere i comandi `join channel`, potresti dover attendere qualche minuto in modo che la sincronizzazione del canale possa essere completata.

    **Nota:** potrai soltanto visualizzare i nuovi blocchi aggiunti al canale, il chaincode in fase di istanziazione e gli altri aggiornamenti al canale nella schermata "Canali" del tuo Monitoraggio della rete se il peer che stai aggiungendo a ICP e collegando a una rete del piano Starter o Enterprise fa parte delle stesse organizzazioni di un peer aggiunto con il Monitoraggio della rete. Questo perché il Monitoraggio della rete raccoglie le informazioni nella schermata "Canali" dal tuo peer e non ha la visibilità dei peer all'esterno di {{site.data.keyword.cloud_notm}}. Se nessuno dei tuoi peer sta utilizzando la funzione Dati Privati, le informazioni nel Monitoraggio della rete saranno le stesse di un peer in un'organizzazione o dell'altro peer.

Dopo che la tua organizzazione è diventata un membro di un canale, attieniti alle istruzioni per [unire il tuo peer a un canale](/docs/services/blockchain/v10_application.html#join-channel-sdk) utilizzando l'SDK. Devi fornire l'URL del servizio ordini e il nome del canale.

### Utilizzo dell'SDK per installare il chaincode sul peer
{: #peer-install-cc-sdk}

Utilizza le seguenti istruzioni per usare l'SDK per [installare un chaincode](/docs/services/blockchain/v10_application.html#install-cc-sdk) sul tuo peer. 

### Utilizzo dell'SDK per istanziare il chaincode sul canale
{: #peer-instantiate-cc-sdk}

Solo un membro del canale deve istanziare o aggiornare il chaincode. Pertanto, qualsiasi membro di rete del canale con i peer su {{site.data.keyword.blockchainfull_notm}} Platform può utilizzare il Monitoraggio della rete per istanziare il chaincode e specificare le politiche di approvazione. Tuttavia, se vuoi utilizzare il peer per istanziare il chaincode su un canale, puoi usare l'SDK e seguire le istruzioni per [istanziare un chaincode](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).

## Utilizzo della CLI per gestire il peer
{: #peer-cli-operate}

Puoi gestire il tuo peer anche dalla riga di comando utilizzando il client `peer` Fabric.

Il tuo peer è stato distribuito con al suo interno il signCert del tuo amministratore peer, consentendo a tale identità di gestire il peer. Le seguenti istruzioni utilizzeranno la cartella MSP dell'amministratore peer generata quando hai [distribuito il tuo peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#register-admin) per unire il peer a un canale, installare un chaincode sul peer e istanziare quindi il chaincode su un canale.

### Scaricamento del client peer Fabric
{: #peer-client}

Il modo più semplice per ottenere il client peer consiste nello scaricare tutti i file binari dello strumento Fabric da Hyperledger. Passa a una directory dove vuoi scaricare i file binari con la tua riga di comando e recuperali immettendo il seguente comando. Se non hai installato [Curl ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl"), dovrai farlo per prima cosa.

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

Il comando installerà i file binari in una directory `bin/`. Imposta il tuo percorso ai tuoi strumenti, dove sono scaricati gli strumenti Fabric sopra indicati:
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

Ad esempio, se hai installato i file binari nella tua directory home, imposterai il tuo `PATH` come:

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

Il download dei file binari creerà una cartella `config` che contiene un file core.yaml, orderer.yaml e configtx.yaml. Imposta il percorso di configurazione di Fabric su questa directory `config`: 

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

Ad esempio:
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}


### Gestione dei certificati sul tuo sistema locale
{: #manage-certs}

Passa alla directory in cui viene generata la cartella MSP di gestione del peer. Se ti sei attenuto alla procedura di esempio indicata in questa documentazione, puoi trovare la cartella MSP in una directory simile alla seguente: 

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Prima di poter utilizzare il peer, devi eseguire alcune operazioni di gestione sui certificati sulla tua macchina locale. Ad esempio, dovrai caricare il signCert dell'amministratore del peer nel piano Starter o Enterprise per assicurarti di poter accedere ai certificati TLS dal peer e dalla tua rete su {{site.data.keyword.cloud_notm}}. Per ulteriori informazioni sui certificati da utilizzare e sulle azioni da eseguire, consulta [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

1. Sposta il signCert del tuo amministratore del peer in una nuova cartella denominata `admincerts`: 

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Carica questo signCert alla rete su {{site.data.keyword.cloud_notm}} in modo che un'applicazione o una CLI remota possa eseguire le operazioni di canale, come il recupero del blocco di genesi del canale o l'istanziazione del chaincode.
    1. Sulla tua macchina locale, apri il file `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` e copialo negli appunti.
    2. Accedi al Monitoraggio della rete della tua rete su {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Fai clic su **Membri** nel navigatore di sinistra e fai clic sulla scheda **Certificati**.
    4. Fai clic sul pulsante **Aggiungi certificato**.
    5. Nella finestra **Aggiungi certificato**, fornisci un nome al tuo certificato, ad esempio "peer-admin", incolla il certificato che hai appena copiato negli appunti e fai clic su **Invia**.
    6. Ti verrà chiesto se vuoi riavviare i peer. Fai clic su **Riavvia**.
    7. Fai clic su **Canali** nel navigatore di sinistra e individua il canale a cui si unirà il peer.
    8. Fai clic sui tre punti sotto l'intestazione **Azioni** e fai clic su **Sincronizza certificato**. Nella finestra **Sincronizza certificato**, fai clic su **Invia**.

    **Nota**: è importante sincronizzare il certificato del canale prima che il peer si unisca al canale o istanzi il chaincode sul canale. Prima di immettere i comandi di unione al canale o di istanziazione del chaincode, potresti dover attendere qualche minuto in modo che la sincronizzazione del canale possa essere completata.

3. Assicurati di aver [scaricato il certificato TLS del tuo peer](#peer-tls) e di poter fare riferimento ad esso dalla tua riga di comando. Sei ti sei attenuto ai comandi di esempio, puoi trovare questo certificato TLS nel file `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

4. Hai anche bisogno di fare riferimento al certificato TLS che hai utilizzato per comunicare con la tua CA del piano Starter o Enterprise quando hai [iscritto il tuo amministratore peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#enroll-admin). Sei ti sei attenuto ai comandi di esempio indicati in questa documentazione, puoi trovare il certificato TLS nel file `$HOME/fabric-ca-client/tls-ibp/tls.pem`.

Puoi eseguire un comando tree per verificare di aver completato queste istruzioni. Passa alla directory in cui hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:
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
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
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

### Impostazione delle variabili di ambiente CLI 
{: #environment-variables}

Dopo aver spostato tutti i nostri certificati nell'ubicazione necessaria, devi impostare alcune variabili di ambiente per utilizzare i tuoi comandi. Successivamente, sei pronto ad utilizzare il client peer Fabric per gestire il tuo peer.

1. Richiama le informazioni di configurazione dal piano Starter o Enterprise dal file **Profilo connessione** disponibile nella schermata **Panoramica** del Monitoraggio della rete. Fai clic su **Profilo connessione** e quindi su **Scarica**.

  - Trova l'URL ordinante ricercando gli **ordinanti**, che puoi trovare in `orderers > url`. Prendi nota dell'URL che inizia con il nome della rete. L'URL è simile al seguente esempio:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Trova il nome della tua organizzazione ricercando le **organizzazioni**. Questa deve essere la stessa organizzazione utilizzata per registrare il tuo peer. Puoi trovare il nome della tua organizzazione insieme al suo `mspid` associato. Prendi nota del valore dell'`mspid`.

2. [Trova le informazioni sull'endpoint del tuo peer](#peer-endpoint). Devi utilizzare l'endpoint del peer per impostare la variabile di ambiente `PEERADDR`. Assicurati di escludere `http://` all'inizio.

3. Immetti i seguenti comandi per impostare le variabili di ambiente. 

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Sostituisci i campi con le tue proprie informazioni.
    - Sostituisci `<full_path_to_config_folder>` con la cartella config che è stata creata quando hai [scaricato il client peer](#peer-client)
    - Sostituisci `<CHANNEL_NAME>` con il nome del canale a cui si unisce il peer.
    - Sostituisci `<CC_NAME>` con qualsiasi nome che si riferisca al tuo chaincode.
    - Sostituisci `<PEERADDR>` con l'endpoint del peer dal passo precedente per il peer che stai utilizzando al momento.
    - Sostituisci `<ORDERER_URL>` con il nome host e la porta dell'ordinante dal tuo profilo di connessione.
    - Sostituisci `<PATH_TO_ADMIN_MSP>` con il percorso alla cartella MSP del tuo amministratore del peer.
    - Sostituisci `<PATH_TO_PEER_TLS_CERT>` con il percorso al certificato TLS del peer che hai scaricato.
    - Sostituisci `<MSPID_OF_PEER_BEING_USED>` con l'MSPID dell'organizzazione del peer che stai utilizzando per recuperare un blocco di genesi, unire un canale o installare il chaincode.

  Ad esempio:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  Devi anche abilitare TLS, operazione che puoi eseguire immettendo: 

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Utilizzo della CLI per unire il peer al canale 
{: #icp-cli-join-peer-to-channel}

Prima di poter eseguire i comandi della CLI per unire il peer a un canale, è necessario aggiungere la tua organizzazione a un canale nella rete in uno dei seguenti modi. 

  - Puoi avviare un nuovo canale per il peer. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).
  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [channel update](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Una volta che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il signCert del tuo peer al canale in modo che gli altri membri possano verificare la tua firma digitale durante le transazioni. Il peer carica il proprio signCert durante l'installazione, in modo che devi solo sincronizzare il certificato sul canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si unisce la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale.

    **Nota:** se il tuo peer ICP che si collega a una rete del piano Starter o Enterprise fa parte della stessa organizzazione di un altro peer aggiunto con il Monitoraggio della rete, puoi visualizzare solo i nuovi blocchi che vengono aggiunti al canale, il chaincode che viene istanziato e gli aggiornamenti degli altri canali nella schermata "Canali" del tuo Monitoraggio della rete. Questo perché il Monitoraggio della rete raccoglie le informazioni nella schermata "Canali" dal tuo peer e non ha la visibilità dei peer all'esterno di {{site.data.keyword.cloud_notm}}. Se nessuno dei tuoi peer utilizza la funzione Dati Privati, le informazioni nel Monitoraggio della rete sono le stesse di un peer in un'organizzazione o dell'altro peer.

1. Assicurati di aver impostato le [variabili di ambiente nella tua CLI](#environment-variables).

2. Recupera il blocco genesi del canale per creare il libro mastro del canale sul tuo peer. Tieni presente che `$HOME/fabric-ca-client/tls-ibp/tls.pem` rappresenta il percorso al tuo certificato TLS del piano Starter o Enterprise. Se il tuo percorso è differente, assicurati di impostare quello corretto.

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
  ```
  {:codeblock}

  **Nota:** è possibile che quando esegui uno di questi comandi della CLI, venga visualizzata la seguente avvertenza che può essere tranquillamente ignorata.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  Immetti il seguente comando per verificare che il blocco genesi sia stato aggiunto correttamente al tuo contenitore peer:

  ```
  ls *.block
  ```
  {:codeblock}

  Sai che il blocco genesi viene aggiunto correttamente quando viene visualizzato qualcosa di simile al seguente esempio:

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. Dopo aver recuperato il blocco di genesi del canale, puoi unire il peer al canale utilizzando il seguente comando:

  ```
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  Se eseguito correttamente, dovresti visualizzare informazioni simili al seguente esempio:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilizzo della CLI per installare il chaincode sul peer
{: #icp-toolcontainer-install-cc}

A questo punto, siamo pronti per installare e istanziare il chaincode sul peer. Per queste istruzioni, installiamo il chaincode `fabcar` dal repository `fabric-samples`. Assicurati di aver prima [configurato il tuo GOPATH ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Set your GOPATH") e scarica quindi il chaincode `fabric-samples` da github immettendo i seguenti comandi:

```
cd $GOPATH/src
git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

Immetti il seguente comando della CLI del peer per installare il chaincode `fabcar` sul peer:

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**Nota:** se questo chaincode è stato già installato e istanziato su un altro peer in esecuzione sul piano Starter o Enterprise IBP, devono essere eseguiti alcuni altri passi prima di installare il chaincode sul peer. Consulta questo [argomento sulla risoluzione dei problemi](#peer-ibp-troubleshooting) per ulteriori istruzioni su come evitare degli errori associati al modo in cui viene installato il chaincode.

### Utilizzo della CLI per istanziare il chaincode su un canale 
{: #icp-toolcontainer-instantiate-cc}

Poiché un solo peer deve istanziare il chaincode su un canale, non devi utilizzare il tuo peer per istanziare il chaincode. I peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform possono eseguire questa operazione. Tuttavia, se vuoi che il peer istanzi il chaincode sul canale, immetti il seguente comando. Assicurati di aver impostato il valore di `CORE_PEER_TLS_ROOTCERT_FILE` sul percorso del certificato TLS della tua CA sul piano Starter o Enterprise.

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

Dopo che il chaincode è stato istanziato, puoi utilizzare i comandi di query e richiamo (invoke) del chaincode per leggere e scrivere dati nel libro mastro del canale. Per ulteriori informazioni, vedi i comandi [peer chaincode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) nella documentazione Hyperledger Fabric. Dovrai passare l'endpoint dell'ordinante ai tuoi comandi di richiamo (invoke) utilizzando l'IP proxy e la porta ordinante esterna. Devi passare l'endpoint del peer solo a un comando query. 

## Aggiornamento del chaincode

Nel corso del tempo è probabile che tu debba modificare il chaincode in esecuzione sul peer. Poiché il chaincode è installato sui peer e istanziato sul canale, devi aggiornare il chaincode su tutti i peer presenti sul canale.

Completa la seguente procedura per aggiornare il tuo chaincode:

1. Per aggiornare il chaincode su ogni peer, è sufficiente rieseguire il processo che hai utilizzato per installare il chaincode sui peer, utilizzando un'applicazione client o un comando della CLI. Assicurati di specificare lo stesso nome chaincode utilizzato originariamente. Tuttavia, questa volta incrementa la `Version` del chaincode. Tutti i peer devono utilizzare lo stesso nome e la stessa versione del chaincode.

2. Dopo aver installato il nuovo chaincode su tutti i peer nel canale, utilizza il Monitoraggio della rete o il comando
[peer chaincode upgrade ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) per aggiornare il canale per utilizzare il nuovo chaincode.

Consulta il passo due di queste [istruzioni](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) per ulteriori informazioni sull'utilizzo del pannello "Installa il codice" del Monitoraggio della rete per aggiornare il chaincode sul canale.

## Visualizzazione dei log del peer
{: #peer-ibp-view-logs}

È possibile visualizzare i log del componente dalla riga di comando utilizzando i [`comandi della CLI kubectl`](#ca-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster ICP.

- Utilizza il comando `kubectl logs` per visualizzare il log del contenitore all'interno del pod. Se non sei sicuro del tuo nome del pod, immetti il seguente comando per visualizzare il tuo elenco di pod.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Successivamente, immetti il seguente comando per richiamare i log per il contenitore del peer che risiede nel pod sostituendo `<pod_name>` con il nome del tuo pod dal precedente output del comando:

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  Per ulteriori informazioni sul comando `kubectl logs`, consulta la [documentazione Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- In alternativa, puoi accedere ai log utilizzando la [Console di gestione del cluster ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), che apre i log in Kibana.

   **Nota:** quando visualizzi i tuoi log in Kibana, potresti ricevere la risposta `No results found`. Questa condizione può verificarsi se ICP utilizza il tuo indirizzo IP del nodo di lavoro come proprio nome host. Per risolvere questo problema, rimuovi il filtro che inizia con `node.hostname.keyword` all'inizio del pannello e i log diventeranno visibili.

## Risoluzione dei problemi
{: #peer-ibp-troubleshooting}

### **Problema:** il comando di richiamo (invoke) ha avuto esito negativo sul peer con un errore `chaincode fingerprint mismatch` 
{: #icp-cc-install-error}

È possibile ricevere un errore `chaincode fingerprint mismatch` quando esegui una richiesta `peer chaincode invoke` su un peer in esecuzione in {{site.data.keyword.cloud_notm}} Private:

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

Questo errore può verificarsi se è presente un'incongruenza tra i peer che eseguono il chaincode in una delle seguenti arre:

- Nome chaincode
- Versione chaincode
- Percorso chaincode
- File binario chaincode

Questo errore può verificarsi se l'IU del Monitoraggio della rete è stata utilizzata per installare e istanziare il chaincode su un peer in esecuzione nel piano Starter o Enterprise e hai poi installato il chaincode su un peer in esecuzione su {{site.data.keyword.cloud_notm}} Private. L'errore si verifica sulla richiesta `invoke` perché i percorsi del chaincode risultanti sui peer sono diversi.

**Soluzione:**
se vuoi eseguire il chaincode sui peer in entrambi i {{site.data.keyword.cloud_notm}}, come ad esempio il piano Starter o Enterprise e {{site.data.keyword.cloud_notm}} Private, non utilizzare l'IU del Monitoraggio della rete per installare il chaincode. Invece, impacchetta il chaincode con il comando [`peer chaincode package`![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) e installa quindi il pacchetto su tutti i peer eseguendo il comando [`peer chaincode install`](#icp-toolcontainer-install-cc).

Se il chaincode è già installato e istanziato su un canale prima che tenti di installarlo su un peer {{site.data.keyword.cloud_notm}} Private, devi completare la seguente procedura per evitare il problema:

1. Impacchetta il chaincode con il comando [`peer chaincode package`![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package).
2. Installa il pacchetto chaincode sul peer in esecuzione su {{site.data.keyword.cloud_notm}} Private eseguendo il comando `peer chaincode install`.
3. Se disponi dei file binari della piattaforma, puoi eseguire il comando [`peer chaincode upgrade`![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) per eseguire l'upgrade del chaincode in esecuzione sul peer del piano Starter o Enterprise, che utilizza il pacchetto chaincode.
4. Istanzia il chaincode appena installato sul canale utilizzando la CLI o l'IU del Monitoraggio della rete.

Il processo di upgrade del chaincode può essere trovato in [`Chaincode for Operators`![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) nella documentazione Hyperledger Fabric.

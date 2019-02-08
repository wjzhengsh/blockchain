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

# Gestione dei peer in {{site.data.keyword.cloud_notm}} Private con una rete multi-cloud
{: #peer-operate_icp}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Dopo aver configurato un peer di {{site.data.keyword.blockchainfull}} Platform su ICP ({{site.data.keyword.cloud_notm}} Private), devi completare diversi passi operativi prima che il tuo peer possa emettere transazioni per eseguire query e richiamare il libro mastro della rete blockchain. I passi includono l'aggiunta della tua organizzazione a un canale, l'unione del tuo peer al canale, l'installazione del chaincode sul peer, l'istanziazione del chaincode sul canale e la connessione delle applicazioni al tuo peer.
Se vuoi connettere il tuo peer a una rete piano Starter o Enterprise, vedi [Gestione dei peer su {{site.data.keyword.cloud_notm}} Private con piano Starter o Enterprise](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-operate_icp)
{:shortdesc}

Per gestire il tuo peer, devi prima completare alcuni passi prerequisiti dal tuo cluster ICP.

**Prerequisiti:**
- [Configura le tue CLI](#peer-kubectl-configure)
- [Richiama le informazioni sull'endpoint del tuo peer](#peer-endpoint)
- [Scarica il certificato TLS del tuo peer](#peer-tls)

Puoi quindi utilizzare uno dei seguenti metodi per gestire il tuo peer.

**Percorsi operativi:**
- [Utilizzo degli SDK Fabric](#peer-operate-with-sdk)
- [Utilizzo della riga di comando](#peer-cli-operate)

Gli SDK Fabric sono il percorso consigliato, sebbene le istruzioni presuppongano la familiarità con il funzionamento dell'SDK. Se preferisci utilizzare la riga di comando, puoi utilizzare i file binari di peer Fabric.

Se la tua organizzazione non è ancora un membro di un consorzio o di un canale, puoi utilizzare la seguente procedura per [creare un canale](#create-channel). Le istruzioni ti guideranno nella procedura per [preparare una definizione di organizzazione](#organization-definition). Questa definizione verrà utilizzata per fare di te un membro del consorzio mediante aggiunta a un canale di sistema ordinante. Successivamente, potrai formare un nuovo canale [creando una transazione del canale](#peer-icp-channeltx)
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](/docs/services/blockchain/howto/peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](/docs/services/blockchain/v10_application.html#ha-app).
-->

## Prerequisiti

Sia che tu scelga di utilizzare gli SDK o la riga di comando, devi completare i passi indicati in questa sezione per la gestione del tuo peer. Puoi completare tutta questa procedura prima di iniziare.

### Configurazione delle CLI
{: #peer-kubectl-configure}

Devi utilizzare lo strumento di riga di comando **kubectl** per stabilire una connessione al contenitore peer che viene eseguito in ICP.

1. Accedi alla IU del tuo cluster ICP. Passa alla scheda **Command Line Tools** e fai clic su **Cloud Private CLI**. Vedrai i seguenti strumenti che puoi scaricare.

   * Installa i plug-in e la CLI di IBM Cloud Private
   * Installa la CLI Kubectl
   * Installa Helm
   * Installa la CLI Istio

  Per gestire un peer, devi utilizzare i primi **tre** strumenti, tra cui Helm è facoltativo. Fai clic su ciascuno di essi ed esegui i comandi `curl` per il tipo di macchina che stai usando. Immetti quindi i comandi `chmod` e `sudo mv` per ciascuno strumento. Il comando `chmod` modificherà le autorizzazioni della CLI in questione per renderla eseguibile e il comando `sudo mv` sposterà il file e la rinominerà.

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

2. Dopo che hai configurato **kubectl**, esegui questo comando:

  ```
  kubectl get pods
  ```
  {:codeblock}

  Se ha esito positivo, il comando restituisce una risposta simile al seguente esempio, dove `peer-6cf85f6687-hcxpl` è il nome del contenitore peer.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Sei ora pronto a usare lo strumento **kubectl** per ottenere le informazioni sull'endpoint del peer.

3. Facoltativamente, se vuoi utilizzare **Helm**, completa qualche altro passo. Nota: l'installazione di Helm è facoltativa e non hai bisogno di utilizzarlo in queste istruzioni. Può tuttavia essere utile per gestire le tue release Helm e per creare dei tuoi archivi da distribuire in ICP.

  1. Fai clic su "Install Helm" ed esegui il comando `curl` dall'IU ICP.
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

### Richiamo delle informazioni sull'endpoint del peer
{: #peer-endpoint}

Devi indicare come destinazione il tuo endpoint del peer dall'SDK o dal client CA Fabric per unirti al canale o installare gli smart contract. Puoi trovare l'endpoint del tuo peer utilizzando la tua IU della console ICP. Per completare questa procedura, dovrai essere un [amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Ruoli e azioni dell'amministratore del cluster"):

1. Accedi alla tua console ICP e fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. Nella sezione **Note**, puoi vedere una serie di comandi per aiutarti a gestire la tua distribuzione dei peer.
5. Attieniti alla procedura per configurare la [CLI kubeclt](#peer-kubectl-configure), se non l'hai già fatto. Utilizzerai kubectl per interagire con il tuo contenitore peer.
6. Nella tua CLI, esegui il primo comando nella nota, che segue **1. Get the application URL by running these commands:** Questo comando stamperà l'URL e la porta del peer, che è simile al seguente esempio. Salva questo URL per i comandi futuri.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Nota:** se distribuisci il tuo peer dietro un firewall, devi aprire una passthru per abilitare la rete sulla piattaforma a completare l'handshake TLS con il tuo peer. Devi abilitare una passthru solo per la porta del nodo associata alla porta 7051 del tuo peer. Per ulteriori informazioni, vedi la sezione relativa alla [ricerca delle informazioni sul tuo endpoint del peer](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-endpoint).

### Scarica il certificato TLS del tuo peer
{: #peer-tls}

Devi scaricare il certificato TLS del tuo peer e passarlo ai tuoi comandi per comunicare con il tuo peer da un client remoto.

1. Accedi alla tua console ICP e fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. Nella sezione **Note**, puoi vedere una serie di comandi per aiutarti a gestire la tua distribuzione dei peer.
5. Attieniti alla procedura per configurare la [CLI kubeclt](#peer-kubectl-configure), se non l'hai già fatto. Utilizzerai kubectl per interagire con il tuo contenitore peer.
6. Nella tua CLI, esegui il primo comando nella nota, che segue **3. Get peer TLS root cert file**. Questo comando salverà il tuo certificato TLS come file `cacert.pem` sulla tua macchina locale.
7. Sposta il certificato nell'ubicazione dove puoi fare riferimento a esso nei comandi futuri e rinominalo come `peertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Utilizzo degli SDK Fabric per gestire il peer
{: #peer-operate-with-sdk}

Gli SDK Hyperledger Fabric forniscono una potente serie di API che consentono alle applicazioni di interagire con le reti blockchain e di gestire tali reti. Puoi trovare l'elenco più aggiornato dei linguaggi supportati e l'elenco completo delle API disponibili all'interno degli SDK Fabric nella [documentazione della community Hyperledger Fabric SDK ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Documentazione della community Hyperledger Fabric SDK"). Puoi utilizzare gli SDK Fabric per unire il tuo peer a un canale su {{site.data.keyword.blockchainfull_notm}} Platform, installare un chaincode sul tuo peer e istanziare il chaincode su un canale.

Le seguenti istruzioni utilizzano l'[SDK Node Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node Fabric") per gestire il peer e presumono una precedente dimestichezza con l'SDK. Puoi utilizzare l'[esercitazione sullo sviluppo di applicazioni](/docs/services/blockchain/v10_application.html) per imparare a utilizzare l'SDK Node prima di iniziare e come guida allo sviluppo di applicazioni con il tuo peer quando sei pronto a richiamare e interrogare mediante query il chaincode.

### Installazione dell'SDK Node

Puoi utilizzare NPM per installare l'[SDK Node ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node"):

```
npm install fabric-client@1.2
```
{:codeblock}

Si consiglia di utilizzare la versione 1.2 dell'SDK Node.

### Preparazione dell'SDK per lavorare con il peer
{: #peer-node-sdk}

Il tuo peer è stato distribuito con il signCert del tuo amministratore peer all'interno. Ciò ti consentirà di utilizzare la cartella MSP e i certificati dell'amministratore peer per gestire il peer.

Individua i certificati che hai creato quando hai [iscritto il tuo amministratore peer](/docs/services/blockchain/howto/CA_operate.html#enroll-admin). Se hai usato i comandi di esempio, puoi trovare la cartella MSP del tuo amministratore peer in `$HOME/fabric-ca-client/peer-admin`.
  - Puoi creare il contesto di utente amministratore peer con l'SDK utilizzando il signCert (chiave pubblica) e la chiave privata nella cartella MSP. Puoi trovare queste chiavi nelle seguenti ubicazioni:
    - Il signCert è disponibile nella cartella **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - La chiave privata è disponibile nella cartella **keystore:**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    Per un esempio di come formare un contesto d'uso e gestire l'SDK utilizzando solo la chiave pubblica e privata, vedi [Generazione di certificati utilizzando il Monitoraggio della rete](/docs/services/blockchain/v10_application.html#enroll-panel).

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](/docs/services/blockchain/howto/CA_operate.html#register-admin). -->

### Passaggio del certificato TLS del tuo peer all'SDK
{: #icp-peer-download-tlscert}

Devi fare riferimento al certificato TLS del tuo peer per autenticare le comunicazioni dal tuo SDK. Individua il certificato TLS che hai [scaricato dal tuo contenitore peer](#peer-tls) e salvalo dove la tua applicazione può fare riferimento ad esso. Se hai utilizzato i comandi di esempio, puoi trovare il certificato TLS del tuo peer in `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Puoi quindi importare il certificato TLS nella tua applicazione utilizzando un semplice comando di lettura file.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Trasmissione delle informazioni sull'endpoint del peer all'SDK
{: #peer-SDK-endpoints}

Trova le [informazioni sull'endpoint del tuo peer](#peer-endpoint) e forniscile all'SDK dichiarando una nuova variabile peer. Il seguente esempio definisce il peer come endpoint sulla tua rete fabric e lo trasmette al certificato TLS che hai importato.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

Se hai una rete di grandi dimensioni, con più peer che appartengono a organizzazioni differenti che devono approvare le tue transazioni, faresti meglio a lavorare con il tuo consorzio per [creare un profilo di connessione comune ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/tutorial-network-config.html "profilo di connessione comune")

### Trasferimento del certificato TLS del tuo ordinante all'SDK
{: #icp-orderer-download-tlscert}

Ti serve anche il certificato TLS dell'ordinante del tuo consorzio per unirti ai canali e inoltrare le transazioni. Se sei l'amministratore dell'ordinante, attieniti alle istruzioni per [scaricare il certificato TLS dell'ordinante](/docs/services/blockchain/howto/orderer_operate.html#orderer-tls).  Se hai usato i comandi di esempio, puoi trovare il certificato TLS del tuo peer in `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`. Se l'ordinante è controllato da un'altra organizzazione, è necessario che ti forniscano il certificato TLS in un'operazione fuori banda. Puoi quindi importare il certificato TLS nella tua applicazione.

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### Trasmissione delle informazioni sull'ordinante all'SDK
{: #orderer-SDK-endpoints}

Per utilizzare l'SDK, ti servono anche le informazioni sull'endpoint degli ordinanti nel tuo consorzio. Se sei l'amministratore dell'ordinante, puoi utilizzare queste istruzioni per [richiamare le informazioni sull'endpoint dell'ordinante](/docs/services/blockchain/howto/orderer_operate.html#orderer-endpoint). Se l'ordinante è controllato da un'altra organizzazione, è necessario che ti forniscano l'URL dell'ordinante in un'operazione fuori banda. Il seguente esempio definisce un ordinante come un endpoint e passa ad esso il certificato TLS dell'ordinante.

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### Utilizzo dell'SDK per l'adesione a un canale
{: #peer-join-channel-sdk}

La tua organizzazione deve essere un membro di un canale prima che tu possa unirti al canale con il tuo peer. Se non sei un membro di un canale, devi attenerti alle istruzioni per [creare un nuovo canale](#create-channel).

Dopo che la tua organizzazione è diventata un membro di un canale, attieniti alle istruzioni per [unire i tuoi peer a un canale](/docs/services/blockchain/v10_application.html#join-channel-sdk) utilizzando l'SDK.

### Utilizzo dell'SDK per installare il chaincode sul peer
{: #peer-install-cc-sdk}

Utilizza le seguenti istruzioni per usare l'SDK per [installare un chaincode](/docs/services/blockchain/v10_application.html#install-cc-sdk) sul tuo peer.

### Utilizzo dell'SDK per istanziare il chaincode sul canale
{: #peer-instantiate-cc-sdk}

Solo un membro del canale deve istanziare o aggiornare il chaincode. Pertanto, qualsiasi membro di un canale che ha installato il chaincode sui suoi peer può istanziare il chaincode e specificare le politiche di approvazione. Tuttavia, se vuoi utilizzare il peer per istanziare il chaincode su un canale, puoi usare l'SDK e attenerti alle istruzioni per [istanziare un chaincode](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).

## Utilizzo della CLI per gestire il peer
{: #peer-cli-operate}

Puoi anche gestire il tuo peer dalla riga di comando utilizzando i file binari di peer Fabric.

Il tuo peer è stato distribuito con al suo interno il signCert del tuo amministratore peer, consentendo a tale identità di gestire il peer. Le seguenti istruzioni utilizzeranno la cartella MSP dell'amministratore peer che era stata generata quando hai [distribuito il tuo peer](/docs/services/blockchain/howto/CA_operate.html#register-admin) per unire il peer a un canale, installare un chaincode sul peer e istanziare quindi il chaincode su un canale.

### Download del client peer Fabric
{: #peer-client}

Per interagire con il tuo peer da un client remoto, devi scaricare il [client peer Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "comando peer Fabric").

Il modo più facile per ottenere il client peer consiste nello scaricare i file binari dello strumento Fabric dal progetto Hyperledger. Crea una directory dove vuoi scaricare i file binari con la tua riga di comando e recuperali immettendo il comando di seguito indicato. Devi prima installare [Curl ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl").

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

Il download dei file binari creerà una cartella di configurazione che contiene un file core.yaml, un file orderer.yaml e un file configtx.yaml. Imposta il percorso di configurazione di Fabric su questa directory di configurazione:

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

Passa alla directory dove è generata la cartella MSP dell'amministratore peer. Se ti sei attenuto alla procedura di esempio indicata in questa documentazione, puoi trovare la cartella MSP in una directory simile alla seguente:

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Prima di poter gestire il peer, devi eseguire alcune operazioni di gestione sui certificati sulla tua macchina locale. Devi anche assicurarti di poter accedere ai certificati TLS dal peer. Per ulteriori informazioni sui certificati da usare, vedi [Membership Service Provider](/docs/services/blockchain/howto/CA_operate.html#msp) in [Gestione di una CA (Certificate Authority - Autorità di certificazione) su {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html).

1. Sposta il signCert del tuo amministratore peer in una nuova cartella denominata `admincerts`:

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
    mkdir admincerts
    cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. Assicurati di [scaricare il certificato TLS del tuo peer](#peer-tls) e di poter fare riferimento ad esso dalla tua riga di comando. Sei ti sei attenuto ai comandi di esempio indicati in questa documentazione, puoi trovare questo certificato TLS nel file `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

3. Devi fare anche riferimento al certificato TLS del tuo ordinante. Se sei l'amministratore dell'ordinante, attieniti alle istruzioni per [scaricare il certificato TLS dell'ordinante](/docs/services/blockchain/howto/orderer_operate.html#orderer-tls). Se l'ordinante è controllato da un'altra organizzazione, è necessario che ti forniscano il certificato TLS in un'operazione fuori banda. Salva questo certificato in una ubicazione da cui puoi fare riferimento ad esso nei comandi futuri.

Puoi eseguire un comando tree per verificare di aver completato questa procedura. Passa alla directory dove hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Impostazione delle variabili di ambiente CLI
{: #environment-variables}

Dopo che hai trasferito tutti i tuoi certificati all'ubicazione necessaria, dobbiamo impostare alcune variabili di ambiente che vengono utilizzate dai comandi CLI del nostro peer. Saremo quindi pronti a utilizzare il client peer Fabric per gestire il nostro peer.

1. Assicurati di aver impostato le [variabili di ambiente nella tua CLI](#environment-variables)

2. Imposta il percorso di configurazione Fabric su questa directory di configurazione che era stata creata quando hai [scaricato i file binari dello strumento Fabric](#peer-client):

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    L'impostazione di questa variabile ti consentirà di eseguire i comandi utilizzando il client peer in qualsiasi directory.

3. Hai bisogno delle informazioni sull'endpoint del tuo ordinante. Se sei l'amministratore dell'ordinante, puoi utilizzare queste istruzioni per [richiamare le informazioni sull'endpoint dell'ordinante](/docs/services/blockchain/howto/orderer_operate.html#orderer-endpoint). Se l'ordinante è controllato da un'altra organizzazione, è necessario che ti forniscano l'URL dell'ordinante in un'operazione fuori banda. 

4. [Richiama le informazioni sull'endpoint del tuo peer](#peer-endpoint). Useremo questo URL per impostare la variabile di ambiente `PEERADDR`. Devi tralasciare `http://` all'inizio.

5. Esegui i seguenti comandi per impostare le variabili di ambiente:

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Sostituisci i campi con le tue proprie informazioni.
    - Sostituisci `<CHANNEL_NAME>` con il nome del canale a cui si unisce il peer.
    - Sostituisci `<CC_NAME>` con qualsiasi nome che si riferisca al tuo chaincode.
    - Sostituisci `<PEERADDR>` con il nome host e la porta del peer dal passo precedente.
    - Sostituisci `<ORDERER_URL>` con il nome host e la porta del tuo ordinante del consorzio.
    - Sostituisci `<PATH_TO_ADMIN_MSP>` con il percorso alla cartella MSP del tuo amministratore peer.
    - Sostituisci `<MSPID_OF_PEER_BEING_USED>` con l'MSPID organizzazione del peer che stai usando per recuperare un blocco genesi o per unirti a un canale o installare un chaincode.Questo valore è stato fornito durante la distribuzione del grafico Helm.

  Ad esempio:

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
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

La tua organizzazione deve essere un membro di un canale prima che tu possa unirti al canale con il tuo peer. Se non sei un membro di un canale, devi attenerti alle istruzioni per [creare un nuovo canale](#create-channel).

1. Assicurati di aver impostato le [variabili di ambiente nella tua CLI](#environment-variables).

2. Recupera il blocco genesi del canale per creare il libro mastro del canale sul tuo peer. Imposta `CORE_PEER_TLS_ROOTCERT_FILE` sul percorso del certificato TLS dell'ordinante ed esegui questo comando 

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Se questa operazione ha esito positivo, vedrai dei risultati simili a:
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **Nota:** è possibile che quando esegui uno di questi comandi della CLI, venga visualizzata la seguente avvertenza che può essere tranquillamente ignorata.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```
  {:codeblock}

  Immetti il seguente comando per verificare che il blocco genesi sia stato aggiunto correttamente al tuo contenitore peer:

  ```
  ls *.block
  ```
  {:codeblock}

  Sai che il blocco genesi viene aggiunto correttamente quando viene visualizzato qualcosa di simile al seguente esempio:

  ```
  defaultchannel_0.block
  ```

3. Una volta che hai recuperato il blocco genesi del canale, puoi unire il peer al canale.  Imposta `CORE_PEER_TLS_ROOTCERT_FILE` sul percorso del certificato TLS del tuo peer e unisciti al canale utilizzando il seguente comando:

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
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

A questo punto, siamo pronti per installare e istanziare il chaincode sul peer. Per queste istruzioni, installiamo il chaincode `fabcar` dal repository `fabric-samples`. Assicurati di avere prima [configurato il tuo GOPATH ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Imposta il tuo GOPATH") e scarica quindi il chaincode `fabric-samples` da github utilizzando i seguenti comandi:

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Esegui questo comando della CLI del peer per installare il chaincode `fabcar` nel peer. Imposta `CORE_PEER_TLS_ROOTCERT_FILE` sul percorso del certificato TLS del tuo peer.

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

<!--
**Note:** If this chaincode has already been installed and instantiated on another peer running in IBP Starter or Enterprise Plan, there are additional steps that must be performed before installing the chaincode on the peer. For more instructions about how to avoid errors that are associated with how this chaincode is installed, see the [troubleshooting topic](#icp-cc-install-error).
-->

### Utilizzo della CLI per istanziare il chaincode su un canale
{: #icp-toolcontainer-instantiate-cc}

Poiché un solo peer deve istanziare il chaincode su un canale, non sei tenuto a utilizzare il tuo peer per istanziare il chaincode. I peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform possono eseguire questa operazione. Tuttavia, se vuoi che il peer istanzi il chaincode sul canale, puoi farlo eseguendo il comando di seguito indicato. Imposta il valore di `CORE_PEER_TLS_ROOTCERT_FILE` sul percorso del certificato TLS del tuo peer. Imposta il valore di `--cafile` sul certificato TLS dell'ordinante.

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

Dopo che il chaincode è stato istanziato, puoi utilizzare i comandi di query e richiamo (invoke) del chaincode per leggere e scrivere dati nel libro mastro del canale. Per ulteriori informazioni, vedi i comandi [peer chaincode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) nella documentazione di Hyperledger Fabric. Dovrai passare l'endpoint dell'ordinante ai tuoi comandi di richiamo (invoke) utilizzando l'IP proxy e la porta ordinante esterna. Devi solo passare l'endpoint del peer a un comando query.

## Visualizzazione dei log del peer in {{site.data.keyword.cloud_notm}} Private
{: #peer-log-icp}

Puoi accedere ai log del peer dalla console ICP e controllare i log nell'interfaccia Kibana.

1. Nella console ICP, fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Dall'elenco di menu, fai clic su **Workload** > **Release Helm**.
3. Nella tabella Release Helm, fai clic sul nome della tua release Helm.
4. Scorri fino alla sezione **Pod** e fai clic sul link **Visualizza log** alla fine della riga della tabella. I log del peer vengono aperti nell'interfaccia Kibana.

## Aggiornamento del chaincode

Nel corso del tempo, è probabile che tu debba modificare il chaincode in esecuzione sul peer. Poiché il chaincode è installato sui peer e istanziato sul canale, devi aggiornare il chaincode su tutti i peer presenti sul canale.

Completa la seguente procedura per aggiornare il tuo chaincode:

1. Per aggiornare il chaincode su ogni peer, è sufficiente rieseguire il processo che hai utilizzato per installare il chaincode sui peer, utilizzando un'applicazione client o un comando della CLI. Assicurati di specificare lo stesso nome chaincode utilizzato originariamente. Tuttavia, questa volta incrementa la `Version` del chaincode. Tutti i peer devono utilizzare lo stesso nome e la stessa versione del chaincode.

2. Dopo che hai installato il nuovo chaincode su tutti i peer nel canale, utilizza il comando
[peer chaincode upgrade ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) per aggiornare il canale per utilizzare il nuovo chaincode.

## Riavvio di un peer eseguito in ICP
{: #peer-restart}

Ogni volta che un certificato viene aggiunto al peer, è necessario riavviare il nodo peer affinché riconosca il nuovo certificato.

Puoi utilizzare i comandi **kubectl** per riavviare il peer eseguito in ICP. Il comando richiede come parametri il nome del **pod** del peer e il suo **contenitore**. Per ulteriori informazioni sull'utilizzo dei comandi kubectl, vedi [Configurazione della CLI kubectl](#peer-kubectl-configure).

1. Nella console ICP, trova il nome **pod** e il nome **contenitore** per la tua distribuzione del peer.

  1. Fai clic sull'icona di menu nell'angolo superiore sinistro della console e quindi su **Workload > Distribuzioni**.
  2. Individua e fai clic sulla release del peer nella tabella per aprirla.
  3. Scorri verso il basso per visualizzare il nome del **pod** associato. Prendi nota del nome del **pod**.
  4. Fai clic sul pod per aprirlo.
  5. Fai clic sulla scheda **Contenitori**. Prendi nota del nome del **contenitore** peer.

2. Dalla tua riga di comando, immetti il seguente comando per riavviare il peer sostituendo `<PEER_POD_NAME>` e `<PEER_CONTAINER_NAME>` con i valori recuperati dal passo precedente.

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. Puoi verificare che il peer sia stato riavviato eseguendo il comando `kubectl get pods` ed esaminando l'output per il conteggio di **RESTART** per il tuo pod.

## Creazione di un canale
{: #create-channel}

Se la tua organizzazione non è ancora un membro di un consorzio o di un canale, puoi utilizzare la seguente procedura per creare un canale. Puoi anche utilizzare questa procedura per aggiornare un canale esistente. Le istruzioni ti guideranno nella procedura per [preparare una definizione di organizzazione](#organization-definition). Questa definizione verrà utilizzata per fare di te un [membro del consorzio](/docs/services/blockchain/howto/orderer_operate.html#add-organizations-to-consortium) mediante aggiunta a un canale di sistema ordinante. Successivamente, potrai formare un nuovo canale [creando una transazione del canale](#peer-icp-channeltx)

Se già ti sei unito al consorzio, devi completare solo il passo di preparazione della transazione del canale. Un altro membro del consorzio può anche formare un nuovo canale con la tua organizzazione come membro. Puoi aggiornare la definizione dell'organizzazione quando devi inviare degli aggiornamenti al consorzio, come ad esempio la definizione di nuovi peer di ancoraggio, l'aggiunta di nuovi certificati di gestione o l'aggiornamento del tuo materiale di crittografia.

### Download dello strumento configtxgen
{: #peer-icp-configtxgen}

Se la tua organizzazione deve unirsi a un consorzio o a un canale, devi scaricare lo strumento [configtxgen ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen").

Il modo più facile per ottenere configtxgen consiste nello scaricare tutti i file binari dello strumento Fabric dal progetto Hyperledger. Passa a una directory dove vuoi scaricare i file binari con la tua riga di comando e recuperali immettendo il comando di seguito indicato. Devi prima installare [Curl ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl").

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

Il comando installa configtxgen in una directory `bin/`. Imposta il percorso `PATH` alla directory dove hai scaricato i file binari dello strumento Fabric:

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

Ad esempio, se hai installato i file binari nella tua directory home, imposterai il tuo `PATH` come:

```
export PATH=$PATH:$HOME/bin
```

## Preparazione di una definizione di organizzazione
{: #organization-definition}

Dopo che hai distribuito i tuoi componenti, la tua organizzazione può unirsi a un consorzio preparando un file di definizione di organizzazione. La definizione contiene tutte le informazioni di cui hai bisogno per partecipare alla governance del consorzio, ad esempio creando nuovi canali o unendoti a essi, aggiungendo i tuoi peer ai canali o istanziando il chaincode. La definizione contiene il nome della tua organizzazione, i certificati MSP e l'MSPID. Tutte le organizzazioni devono completare questo passo.

### Prepara il tuo materiale di crittografia

Prima di preparare una definizione di organizzazione, devi registrare e iscrivere l'[amministratore dei tuoi peer](/docs/services/blockchain/howto/CA_operate.html#register-admin). Passa alla directory dove hai creato la cartella MSP del tuo amministratore peer. La procedura di esempio ha creato questa cartella in `$HOME/fabric-ca-client/peer-admin/msp`. Devi eseguire alcuni passi aggiuntivi all'interno di questa cartella prima che l'MSP possa essere utilizzato dallo strumento `configtxgen`.

1. Copia il tuo certificato TLS dalla tua CA e inseriscilo in una nuova cartella denominata `tlscacerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  Dovrai quindi copiare il certificato nella directory `tlscacerts` che hai creato:

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  Il tuo comando potrebbe essere qualcosa di simile a:

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. Sposta il tuo signCert dalla cartella `signcerts` in una nuova cartella denominata `admincerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

Lo strumento `configtxgen` crea la definizione utilizzando un file configtx.yaml. Puoi trovare una versione di esempio di questo file qui di seguito:

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

Questo file contiene le informazioni che definiscono la tua organizzazione all'interno del consorzio. Una versione più complessa di questo file è anche disponibile nella cartella `/config` del [client peer Fabric che hai scaricato](/docs/services/blockchain/howto/peer_operate_icp.html#peer-client). Puoi scegliere di modificare tale file o di sostituirlo con l'esempio in alto. Annota l'ubicazione di questa cartella `/config` per impostare il valore del `FABRIC_CFG_PATH` di seguito. Modifica la sezione `Organizations` di questo file e imposta i seguenti valori:

- Il nome (`Name:`) può essere qualsiasi nome tu voglia usare per la tua organizzazione.

- I valori `ID:` del tuo MSPID, che diventano l'identificativo univoco della tua organizzazione. Questo valore di MSP dell'organizzazione è stato specificato quando hai distribuito il tuo grafico Helm del peer. È visibile anche all'interno del contenitore peer eseguendo i seguenti comandi, sostituendo `<peer pod name>` con il valore del pod del tuo peer:

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  L'output potrebbe essere simile a:
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  Pertanto, l'`ID` MSP è org1.

- La `MSPDir:` è il percorso completo del tuo MSP amministratore, ad esempio: `/Users/chandra/fabric-ca-client/peer-admin/msp`

- Il campo `AnchorPeers:` è il peer che la tua organizzazione seleziona come peer principale per le comunicazioni tra organizzazioni mediante gossip. Viene utilizzato per funzioni quali il rilevamento di servizi o i dati privati; tuttavia, il rilevamento di servizi e i dati privati non sono attualmente supportati dal grafico Helm del peer, anche se il campo è comunque obbligatorio. I valori `Host` e `Port` sono disponibili nella pagina Helm Release del peer ICP nella sezione Notes `#1. Get the application URL by running these commands`. L'output sarà simile a:

```
http://9.30.250.70:30481
```

Dove `Host:` = `9.30.250.70` e `Port:` = `30481`

Usa quindi il seguente comando per stampare la definizione dell'organizzazione:

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

La tua chiamata potrebbe essere simile al seguente comando di esempio:

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

Se questo comando ha esito positivo, `configtxgen` stamperà la definizione dell'organizzazione in formato JSON. Devi inviare questo file all'organizzazione ordinante in un'operazione fuori banda per unirti al consorzio. L'organizzazione ordinante puoi quindi [formare un consorzio o essere aggiunta a un consorzio esistente](/docs/services/blockchain/howto/orderer_operate.html#consortium) aggiungendo la definizione al canale di sistema, consentendoti di creare nuovi canali e di essere aggiunto ai canali da altri membri del consorzio.

## Creazione della transazione del canale
{: #peer-icp-channeltx}

Prima di poter creare un nuovo canale, la tua organizzazione deve aver preparato una [definizione di organizzazione](#organization-definition) ed essere diventata un membro del consorzio. Attieniti a queste istruzioni se devi [formare un consorzio o essere aggiunto a uno](/docs/services/blockchain/howto/orderer_operate.html#consortium). I membri del consorzio possono anche essere facilmente aggiunti a nuovi canali, se la loro organizzazione è già stata aggiunta al canale di sistema. Le organizzazioni che non sono membri del canale di sistema possono unirsi a un canale solo in modo manuale, aggiungendo la loro definizione di organizzazione al canale utilizzando una [richiesta di aggiornamento del canale ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html). Puoi anche utilizzare questa procedura per aggiornare un canale esistente.

### Formazione di un nuovo canale
{: #peer-icp-create-channel}

Per formare un nuovo canale, un'organizzazione deve creare una proposta di transazione di creazione di canale utilizzando lo [strumento configtxgen![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen"). Questo strumento utilizza un file `configtx.yaml` che definisce i nuovi membri del canale. Un file `configtx.yaml` di esempio viene fornito di seguito. Una versione più complessa di questo file è anche disponibile nella cartella `/config` del [client peer Fabric che hai scaricato](/docs/services/blockchain/howto/peer_operate_icp.html#peer-client). Puoi scegliere di modificare tale file o sostituirlo con l'esempio. Annota l'ubicazione di questa cartella `/config` per impostare il valore del `FABRIC_CFG_PATH` di seguito. 
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

Le tre sezioni pertinenti per la creazione di un nuovo canale sono **Organizations**, **Capabilities** e **Profiles**:

- **Organizations:** questa sezione definisce tutti i membri del consorzio. Ogni organizzazione ha un ancoraggio, come ad esempio `&Org1`. Sotto tale ancoraggio, puoi trovare il nome dell'organizzazione, l'MSPID, la directory alla relativa cartella MSP e i peer di ancoraggio della relativa organizzazione per le comunicazioni tra peer mediante gossip. Puoi compilare il profilo dell'organizzazione per ogni membro del consorzio attenendoti alla seguente procedura:
  1. Specifica nome (`Name`) e `ID` dell'organizzazione utilizzando il relativo MSPID. L'organizzazione che crea il canale deve conoscere l'MSPID che era stato specificato quando il grafico Helm del peer era stato distribuito.
  2. In `MSPDir`, specifica il percorso completo alla cartella MSP che hai usato per creare la tua [definizione dell'organizzazione](#organization-definition). Nota: il nome del tuo materiale di crittografia viene utilizzato nella transazione di creazione del canale. Lo strumento *configtxgen* ignorerà il contenuto effettivo dell'MSP. Tuttavia, prevede una cartella MSP a tale ubicazione, con la sottostruttura di cartelle corretta.
  3. Il parametro `AnchorPeers` viene utilizzato per identificare il peer principale utilizzato da ciascuna organizzazione per le comunicazioni tra organizzazioni mediante gossip. Specifica il nome host e la porta del peer che fungerà da [peer di ancoraggio ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html) per questa organizzazione. Questo valore è importante per l'utilizzo di funzioni quali il rilevamento di servizi o i dati privati; tuttavia, il rilevamento di servizi e i dati privati non sono attualmente supportati dal grafico Helm del peer.

- **Capabilities:** questa sezione deve essere presente nel file `configtx.yaml` ma non è richiesta alcuna modifica.

- Sezione **Profiles:**. Questa sezione contiene le informazioni che sono necessarie per creare un nuovo canale. Il profilo contiene le seguenti informazioni:
  1. Il nome del nuovo canale.
  2. Il nome del consorzio definito all'interno del canale di sistema e che verrà utilizzato per creare il canale.
  3. L'elenco delle organizzazioni che verranno aggiunte al canale. I nomi organizzazione elencati utilizzeranno i peer di ancoraggio elencati nella sezione `Organizations` per trovare l'MSPID delle organizzazioni elencate e i peer di ancoraggio associati.

  Lo strumento *configtxgen* utilizza questa sezione `Profiles` per creare la proposta di creazione del canale.

Dopo l'aggiornamento del file di configurazione, genera la proposta di transazione di aggiornamento del canale utilizzando il seguente comando:

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

Un comando effettivo sarà simile al seguente esempio:
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


Se ha esito positivo, vedrai un risultato simile all'output indicato di seguito. Il comando scriverà la proposta di transazione di aggiornamento del canale nella tua directory `FABRIC_CFG_PATH`.

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

Se la tua politica di approvazione non richiede firme aggiuntive dagli altri membri del consorzio, puoi inoltrare la proposta direttamente all'ordinante per formare il nuovo canale. Esegui questo comando dalla stessa ubicazione dove hai generato la transazione di aggiornamento del canale per creare il canale.

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

Dove `<ORG_MSPID>` è l'MSPID della tua organizzazione e `<PATH_TO_ADMIN_MSP>` è il percorso della cartella MSP del tuo amministratore peer. All'interno del comando, `<orderer_url>` è l'URL dell'ordinante che creerà il canale e `<orderer_tls_cert>` è il percorso del certificato TLS dell'ordinante. Un comando effettivo potrebbe essere simile al seguente esempio:
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## Approvazione della proposta e inoltro della richiesta

Quando la nuova proposta di creazione del canale è firmata da un numero sufficiente di organizzazioni per soddisfare la politica di approvazione del canale del sistema, puoi inoltrare la transazione di aggiornamento del canale all'ordinante per formare il nuovo canale

Puoi utilizzare le API SDK Fabric Node per completare la firma e l'inoltro in una singola transazione. Per ulteriori informazioni, vedi l'esercitazione [How to create a Hyperledger Fabric Channel ![Icona link esterno](../images/external_link.svg "Icona link esterno")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) nella documentazione dell'SDK Node.

## Visualizzazione dei log di peer
{: #peer-icp-view-logs}

I log dei componenti possono essere visualizzati dalla riga del comando utilizzando i [`comandi della CLI kubectl`](#peer-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster ICP.

- Utilizza il comando `kubectl logs` per visualizzare i log dei contenitori all'interno del pod. Se non sei sicuro del tuo nome di pod, esegui questo comando per visualizzare il tuo elenco di pod.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Esegui quindi il seguente comando per richiamare i log per il contenitore peer che si trova all'interno del pod sostituendo `<pod_name>` con il nome del tuo pod dall'output del comando in alto:

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  Per ulteriori informazioni sul comando `kubectl logs`, vedi la [documentazione di Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- In alternativa, puoi accedere ai log utilizzando la [console di gestione del cluster ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), che apre i log in Kibana.

  **Nota:** quando visualizzi i tuoi log in Kibana, potresti ricevere la risposta `No results found`. Questa condizione può verificarsi se ICP utilizza l'indirizzo IP del tuo nodo di lavoro come suo nome host. Per risolvere questo problema, rimuovi il filtro che inizia con `node.hostname.keyword` nella parte superiore del pannello e i log diventeranno visibili.


## Risoluzione dei problemi
{: #peer-icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```
{:codeblock}
2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the selected version is compatible with ICP 2.1.0.3. `v2.7.2 ` is recommended.

3.  After the helm installation completes, [login](#peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```
 {:codeblock}
 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```
 {:codeblock}

5. Check helm version:

  ```
  helm version --tls
  ```
  {:codeblock}

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problema:** errore durante l'accesso al contenitore peer
{: #icp-bash-error}

Questo problema si verifica quando utilizzi lo strumento kubectl per l'esecuzione nel tuo peer immettendo il seguente comando:

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}

Non riesce e produce un messaggio di errore simile al seguente esempio:

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Soluzione:**

Questo errore si verifica quando sui sistemi big endian, come IBM System Z, sono aperte due sessioni bash Docker nello stesso contenitore Docker. La seconda sessione bash potrebbe non essere in grado di connettersi al contenitore in esecuzione. Per risolvere questo problema, [riavvia il contenitore peer](#peer-restart) e ritenta il comando `kubectl exec` dopo il riavvio del peer.

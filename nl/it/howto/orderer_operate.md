---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: command line, orderer system channel, IBM Blockchain Platform, orderer, IBM Cloud Private, operate an orderer

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Utilizzo di un ordinante su {{site.data.keyword.cloud_notm}} Private
{: #icp-orderer-operate}

Dopo aver installato l'ordinante di {{site.data.keyword.blockchainfull}} Platform in {{site.data.keyword.cloud_notm}} Private, viene creata una mappa di configurazione che contiene le impostazioni predefinite per le variabili di ambiente. Successivamente puoi modificare o aggiungere delle variabili di ambiente per l'ordinante per configurarne il comportamento.

Come regola generale, gli amministratori dell'ordinante sono responsabili di eseguire il bootstrap e della conservazione degli ordinanti, ma questo non è necessario in una distribuzione SOLO in cui è presente un solo ordinante. In una distribuzione SOLO, l'amministratore dell'ordinante è responsabile dell'aggiunta di nuove organizzazioni al canale del sistema ordinante.

## Prerequisiti
{: #icp-orderer-operate-prerequisites}

Per gestire il tuo ordinante devi prima completare alcune operazioni preliminari dal tuo cluster {{site.data.keyword.cloud_notm}} Private.

**Prerequisiti:**
- [Configura le tue CLI](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)
- [Richiama le tue informazioni dell'endpoint dell'ordinante](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
- [Scarica il tuo certificato TLS dell'ordinante](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)

Puoi quindi utilizzare le istruzioni in questo argomento per utilizzare il tuo ordinante. Tieni presente che utilizzerai il tuo ordinante tramite la riga di comando, che richiede di ottenere il file binario della CLI `peer`. Il file binario della CLI `peer` viene scaricato insieme ad altri file binari, come ad esempio `configtxlator`. Pertanto, molti dei comandi in questo argomento iniziano con la parola "peer". Non viene realmente utilizzato il peer, è soltanto il nome del file binario.

### Configurazione delle CLI
{: #icp-orderer-operate-kubectl-configure}

Devi utilizzare lo strumento di riga di comando **kubectl** per il collegamento al contenitore dell'ordinante eseguito in {{site.data.keyword.cloud_notm}} Private.

1. Accedi all'IU del tuo cluster {{site.data.keyword.cloud_notm}} Private. Passa alla scheda **Strumenti di riga comandi** e fai clic su **CLI di Cloud Private**. Vedrai i seguenti strumenti che è possibile scaricare.

   * Installa la CLI e i plugin IBM Cloud Private
   * Installa la CLI Kubectl
   * Installa Helm
   * Installa la CLI Istio

  Per utilizzare gli ordinanti, devi utilizzare i primi **tre** strumenti, tra cui Helm è facoltativo. Fai clic su ognuno di essi ed esegui i comandi `curl` per il tipo di macchina che stai utilizzando. Successivamente, immetti i comandi `chmod` e `sudo mv` per ogni strumento. Il comando `chmod` modificherà l'autorizzazione della CLI in questione per renderla eseguibile e il comando `sudo mv` sposterà e ridenominerà il file.

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

  Se ha esito positivo, il comando dovrebbe restituire una risposta simile al seguente esempio, dove `orderer-6cf85f6687-hcxpl` è il nome del contenitore dell'ordinante.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Sei ora pronto ad utilizzare lo strumento **kubectl** per richiamare le informazioni sull'endpoint dell'ordinante.

3. Facoltativamente, se vuoi utilizzare **Helm**, completa alcuni ulteriori passi. Tieni presente che l'installazione di Helm è facoltativa e non hai bisogno di utilizzarlo in queste istruzioni. Può tuttavia essere utile per gestire le tue release Helm e per creare dei tuoi archivi da distribuire in {{site.data.keyword.cloud_notm}} Private.

  1. Fai clic su "Installa Helm" ed esegui il comando `curl` dall'IU {{site.data.keyword.cloud_notm}} Private.
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

### Richiamo delle informazioni sull'endpoint dell'ordinante
{: #icp-orderer-operate-orderer-endpoint}

Devi selezionare il tuo endpoint dell'ordinante per effettuare degli aggiornamenti al canale del sistema ordinante. Dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Cluster administrator roles and actions") per completare le seguenti istruzioni:

1. Accedi alla tua console {{site.data.keyword.cloud_notm}} Private e fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. La sezione **Note** include una serie di comandi per aiutarti ad utilizzare la tua distribuzione dell'ordinante.
5. Se non lo hai ancora fatto, segui le istruzioni per configurare la [CLI kubectl](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure). Hai bisogno di utilizzarla per interagire con il tuo contenitore dell'ordinante.
6. Nella tua CLI, esegui il primo comando nella nota, che segue **1. Get the application URL by running these commands:** Questo comando riprodurrà a schermo l'URL e la porta dell'ordinante, che è simile al seguente esempio. Salva questo URL perché avrai bisogno di utilizzarlo nei comandi successivi per impostare l'indirizzo proxy e la porta del nodo esterno.

  ```
  http://9.30.94.174:30159
  ```

In questo esempio, l'indirizzo IP proxy è `9.30.94.174` e la porta del nodo esterno che corrisponde a 7050 è `30159`.  

**Nota:** se stai distribuendo il tuo ordinante dietro un firewall, devi aprire una passthru per abilitare la rete sulla piattaforma per completare questa attività.

### Scaricamento del tuo certificato TLS dell'ordinante
{: #icp-orderer-operate-tls-cert}

Devi scaricare il tuo certificato TLS dell'ordinante e trasmetterlo ai tuoi comandi per comunicare con il tuo ordinante da un client remoto. Dovrai essere un [Amministratore del cluster ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Cluster administrator roles and actions") per completare le seguenti istruzioni:

1. Accedi alla tua console {{site.data.keyword.cloud_notm}} Private e fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Fai clic su **Workload** > **Release Helm**.
3. Trova il nome della tua release Helm e apri il relativo pannello dei dettagli.
4. Scorri verso il basso fino alla sezione **Note** in fondo al pannello. La sezione **Note** include una serie di comandi per aiutarti ad utilizzare la tua distribuzione dell'ordinante.
5. Se non lo hai ancora fatto, segui le istruzioni per configurare la [CLI kubectl](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure). Hai bisogno di utilizzarla per interagire con il tuo contenitore dell'ordinante.
6. Nella tua CLI, esegui il terzo comando nella nota, che segue **3. Get orderer TLS root cert file**.  Questo comando salverà il tuo certificato TLS come il file `cert.pem` sulla tua macchina locale.

  **Nota:** prima di eseguire i comandi, puoi rimuovere `app=orderer` come mostrato nel seguente comando:

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. Sposta il certificato generato in un'ubicazione a cui puoi fare riferimento in comandi futuri e ridenominalo con `orderertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### Gestione dei certificati sul tuo sistema locale
{: #orderer-operate-manage-certs}

Passa alla directory in cui viene generata la cartella MSP di gestione dell'ordinante. A seconda di come hai seguito le istruzioni di esempio in questa documentazione o di quanti componenti stai distribuendo, puoi trovare la cartella MSP in `$HOME/fabric-ca-client/orderer-admin/msp` o `$HOME/fabric-ca-client/peer-admin/msp`

Prima di poter utilizzare l'ordinante, devi eseguire alcune operazioni di gestione sui certificati sulla tua macchina locale. Devi anche assicurarti di poter accedere ai certificati TLS dall'ordinante. Per ulteriori informazioni sui certificati da usare, vedi [Membership Service Provider](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) in [Gestione di una CA (Certificate Authority - Autorità di certificazione) su {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

1. Sposta il signCert del tuo amministratore ordinante in una nuova cartella denominata `admincerts`:

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Assicurati di [scaricare il certificato TLS del tuo ordinante](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert) e di poter fare riferimento ad esso dalla tua riga di comando. Sei ti sei attenuto ai comandi di esempio indicati in questa documentazione, puoi trovare questo certificato TLS nel file `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`.

Puoi eseguire un comando tree per verificare di aver completato queste istruzioni. Passa alla directory in cui hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:
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
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
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

## Aggiunta di organizzazioni al canale del sistema ordinante
{: #icp-orderer-operate-add-organizations-to-consortium}

**Nota:** se pensi di distribuire un peer e di collegarlo a una rete del piano Starter o Enterprise, puoi saltare questo passo.

Quando distribuisci un ordinante utilizzando il grafico Helm {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, il blocco di genesi viene automaticamente creato con l'organizzazione ordinante come unico amministratore del canale del sistema. Questo significa che l'amministratore dell'organizzazione ordinante ha la sola capacità di aggiungere delle organizzazioni al consorzio. La politica che gestisce questa operazione, nota come `mod_policy` per il canale del sistema ordinante, non dovrebbe essere modificata dopo l'aggiunta di nuove organizzazioni al canale del sistema ordinante.

L'aggiunta delle organizzazioni al canale del sistema ordinante le rende parte del "consorzio", cioè l'elenco dei membri che possono, per impostazione predefinita, creare i canali. Essere un membro del consorzio rende inoltre più semplice l'aggiunta a un canale utilizzando i certificati e le informazioni elencate nel canale del sistema.

L'aggiornamento del canale del sistema ordinante viene eseguito tramite le "transazioni di aggiornamento della configurazione", in cui le informazioni MSP pertinenti delle organizzazioni vengono aggiunte alla configurazione del canale del sistema ordinante.

L'aggiunta di organizzazioni al canale del sistema ordinante è essenzialmente lo stesso flusso dell'aggiornamento della configurazione di ogni canale per aggiungere un'organizzazione. Tuttavia, devi apportare alcune modifiche perché il canale da aggiornare non è un canale dell'applicazione e l'amministratore pertinente è quello dell'ordinante invece di quello di un'organizzazione peer.

Tieni presente che puoi aggiungere un'organizzazione a un canale senza aderire prima al canale del sistema. Per ulteriori informazioni, vedi l'[esercitazione Adding an Org to a Channel ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channel_update_tutorial.html "Adding an org to a channel") nella documentazione di Hyperledger Fabric.

Il seguente elenco mostra i passi e le attività generali che saranno eseguite da serie diverse di organizzazioni del tuo consorzio.

1. Ogni organizzazione per aderire al consorzio deve [preparare una definizione dell'organizzazione](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).
2. L'amministratore dell'organizzazione ordinante [forma il consorzio](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium) aggiungendo delle organizzazioni al canale del sistema ordinante.
3. Ogni organizzazione del consorzio può [creare un nuovo canale](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx) preparando una transazione di configurazione del canale.

## Ottenimento degli strumenti Fabric
{: #icp-orderer-operate-get-fabric-tools}

Devi scaricare i seguenti strumenti Hyperledger Fabric per aggiornare il canale del sistema.
- [peer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peercommand.html), che ti consentirà di recuperare il blocco di genesi e aggiornare il canale del sistema.
- [configtxlator ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/configtxlator.html), che converte il formato protobuf di una configurazione di canale nel formato JSON che è più facile da leggere e aggiornare.

1. Decidi dove vuoi archiviare gli strumenti ed esegui quindi questo comando:

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
  ```
  {:codeblock}

2. Imposta il tuo percorso ai tuoi strumenti, dove sono scaricati gli strumenti Fabric sopra indicati:

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  Ad esempio, se hai installato i file binari nella tua directory home, imposterai il tuo `PATH` come:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Il download dei file binari creerà una cartella di configurazione che contiene un file core.yaml, un file orderer.yaml e un file configtx.yaml. Imposta il percorso di configurazione di Fabric su questa directory di configurazione:

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  Ad esempio:
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## Creazione di una definizione dell'organizzazione
{: #icp-orderer-operate-org-definition}

La **definizione** di un'organizzazione contiene il nome dell'organizzazione (ID MSP) e i certificati pertinenti. Il canale del sistema e i canali dell'applicazione utilizzeranno questa definizione per includere la tua organizzazione nelle politiche che controllano la creazione, gli aggiornamenti e l'approvazione della transazione del canale. Ogni organizzazione che vuole aderire al consorzio deve completare questo passo. Per ulteriori informazioni, consulta [preparazione di una definizione di organizzazione](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).

## Formazione del consorzio
{: #icp-orderer-operate-consortium}

Richiama il flusso di livello superiore per formare un consorzio:

1. Il canale del sistema ordinante è formato con solo l'organizzazione ordinante come membro del canale del sistema. Tale organizzazione può effettuare degli aggiornamenti senza richiedere ulteriori firme.
2. L'amministratore dell'organizzazione ordinante riceve le definizioni dell'organizzazione dai membri che vogliono aderire al consorzio. Le organizzazioni ordinanti utilizzano le definizioni dell'organizzazione per aggiornare la configurazione del canale del sistema ordinante.

**Nota:** se il tuo peer viene distribuito in un cluster {{site.data.keyword.cloud_notm}} Private diverso rispetto all'ordinante, devi [completare ulteriori passi](#icp-orderer-operate-consortium-multi-cluster) per assicurarti che l'indirizzo dell'ordinante nel canale di sistema sia aggiornato con i propri indirizzo IP proxy e porta del nodo. Ad esempio:
  ```
  "OrdererAddresses": {
    "mod_policy": "/Channel/Orderer/Admins",
    "value": {
      "addresses": [
        "9.12.19.49:30576"
      ]
    },
    "version": "0"
  }
  ```

### Ottenimento delle definizioni dell'organizzazione

L'ordinante deve ricevere le [definizioni dell'organizzazione](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) dai membri che vogliono aderire al consorzio. Questa operazione deve essere completata in un'operazione fuori banda con degli altri membri che ti inviano i file JSON che includono i loro ID MSP e il materiale di crittografia. Come riferimento per i seguenti comandi, assumiamo che hai creato una cartella denominata `org-definitions` e posizionato tutti i file pertinenti in tale directory.

### Recupero del blocco di genesi del canale del sistema

1. Imposta le seguenti variabili di ambiente utilizzando il tuo nome MSP dell'organizzazione ordinante che hai stabilito durante la distribuzione, il percorso alla tua MSP ordinante e il percorso al certificato TLSCA del tuo ordinante.

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  Sostituisci i campi con le tue proprie informazioni.

    - Sostituisci `<CORE_PEER_LOCALMSPID>` con l'ID MSP della tua organizzazione ordinante. È visibile anche all'interno del contenitore ordinante eseguendo questi comandi, sostituendo `<orderer pod name>` con il valore del pod del tuo ordinante.

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      L'output potrebbe essere simile al seguente:

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      Pertanto, `CORE_PEER_LOCALMSPID` è ordererOrg.

    - Sostituisci `<CORE_PEER_MSPCONFIGPATH>` con il percorso alla cartella MSP di gestione dell'organizzazione ordinante.
    - Sostituisci `<CORE_PEER_TLS_ROOTCERT_FILE>` con il percorso al certificato CA TLS.
    - Sostituisci `<PROXY_IP>` con l'indirizzo IP proxy dalle [informazioni sull'endpoint ordinante](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
    - Sostituisci `<EXTERNAL_NODE_PORT>` con la porta del nodo esterno dalle [informazioni sull'endpoint ordinante](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)

  Le tue variabili di ambiente sono simili al seguente esempio:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  Puoi controllare queste variabili di ambiente in qualsiasi momento, immettendo i seguenti comandi:

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. Imposta il nome del canale come una variabile di ambiente. Il nome del canale del sistema ordinante sarà sempre `test-system-channel-name`.

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. Recupera il blocco di genesi del canale del sistema. Per prima cosa, crea una directory denominata `configupdate` in `org-definitions` per archiviare i file di configurazione generati durante il processo di aggiornamento della configurazione. Puoi dare a questa directory un qualsiasi nome, ma i seguenti comandi di esempio utilizzeranno `configupdate.`

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  Sostituisci `<orderer_TLS_root_cert_file>` con il percorso al file `orderertls.pem` che hai creato in questo [passo](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert). Ad esempio, il tuo comando è simile al seguente:

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Se ha esito positivo, questo comando genererà il seguente output.

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  Successivamente, puoi anche trovare il blocco `genesis.pb` nel tuo file system .

### Creazione dell'aggiornamento del canale del sistema
{: #icp-orderer-operate-system-channel-update}

Lo [strumento Fabric](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-get-fabric-tools) `configtxtlator` scaricato, traduce il formato protobuf di una configurazione del canale nel formato JSON e viceversa.

Questi passi seguono il flusso generale dell'esercitazione di aggiornamento del canale circa la [conversione del blocco in formato JSON ![Icona link esterno](../images/external_link.svg "Icona link esterno")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down "Convert the Configuration to JSON and Trim It Down"). Devi apportare alcune modifiche ai comandi nell'esercitazione per tener conto del fatto che stai aggiornando il canale del sistema ordinante invece di un canale dell'applicazione. Puoi visitare l'esercitazione per ulteriori dettagli su questo processo. Questa sezione ti fornisce semplicemente i comandi.

1. Copia il file JSON della definizione dell'organizzazione dalla cartella in cui hai [creato la tua organizzazione](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) nella tua cartella `configupdate`. Nel seguente comando di esempio, il file JSON della definizione dell'organizzazione è `org1definition.json`:

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. Converti il blocco di genesi nel formato JSON. Immetti i seguenti comandi:

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. Immetti il seguente comando per aggiungere il materiale di crittografia di un'organizzazione alla configurazione del consorzio. Sostituisci <NEWORGMSP> con l'ID MSP dell'organizzazione per l'[organizzazione che hai creato](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  Il comando è simile al seguente esempio:

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. Ripeti questo comando per ogni organizzazione che deve aderire al consorzio. Assicurati di modificare il file `./orgdefinition.json` in modo che sia il nuovo file JSON dell'organizzazione.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. Immetti i seguenti comandi per convertire il file `modified_config.json` con l'aggiornamento della configurazione nel formato JSON.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. Dopo il completamento di questi comandi, inserisci un envelope in `config_update`.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. Immetti il seguente comando per riportare l'aggiornamento della configurazione al formato protobuf:

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### Invio dell'aggiornamento al canale del sistema

Prima di completare questa procedura, assicurati che `FABRIC_CFG_PATH`, `$PROXY` e `ORDERER_PORT` siano configurati correttamente. Immetti il seguente comando:

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
```
{:codeblock}

Le tue variabili di ambiente dovrebbero essere simili al seguente esempio:

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

Dopo aver creato l'aggiornamento di configurazione del canale, cioè `config_update_in_envelope.json`, l'organizzazione ordinante può inviare l'aggiornamento del canale utilizzando il seguente comando:

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

Questo comando firma simultaneamente la richiesta di aggiornamento e la invia all'ordinante. Poiché l'organizzazione ordinante è l'unico amministratore del canale del sistema, è necessaria solo una firma per questo aggiornamento perché sia una richiesta valida. Se l'aggiornamento del canale del sistema ha esito positivo, visualizzerai il seguente output:

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

È possibile includere più definizioni dell'organizzazione in un solo aggiornamento di configurazione del canale del sistema ordinante, ma è una prassi consigliata aggiornare il canale con un'organizzazione alla volta e verificare che gli aggiornamenti abbiano avuto esito positivo.

### Aggiornamento del canale di sistema per distribuzioni multicluster
{: #icp-orderer-operate-consortium-multi-cluster}

Se distribuisci il tuo peer in un altro cluster {{site.data.keyword.cloud_notm}} Private rispetto all'ordinante, il tuo peer non può comunicare con l'ordinante con le informazioni della release Helm dell'ordinante. Dopo aver aggiunto la tua organizzazione al canale di sistema, devi aggiornare il canale di sistema in modo che utilizzi le [informazioni endpoint dell'ordinante](#icp-orderer-operate-orderer-endpoint), ossia, i propri indirizzo IP proxy e numero di porta invece delle informazioni della release Helm. Immetti il seguente comando per aggiornare l'indirizzo dell'ordinante con `genesis-config.json`.

1. Converti il blocco di genesi nel formato JSON.

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

2. Immetti i seguenti comandi per convertire il file `modified_config.json` con l'aggiornamento della configurazione nel formato JSON.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

3. Inserisci un envelope in `config_update`.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

4. Immetti il seguente comando per riportare l'aggiornamento della configurazione al formato protobuf.

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

5. Invia l'aggiornamento al canale del sistema.
  ```
  peer channel update -f update_in_envelope.pb -c $CHANNEL_NAME -o $ORDERER_URL --tls --cafile $ORDERER_TLS_CERTIFICATE
  ```
  {:codeblock}

## Visualizzazione dei log dell'ordinante
{: #icp-orderer-operate-orderer-view-logs}

È possibile visualizzare i log del componente dalla riga di comando utilizzando i [`comandi della CLI kubectl`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) o tramite [Kibana ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.elastic.co/products/kibana "Your window into the Elastic Search"), che è incluso nel tuo cluster {{site.data.keyword.cloud_notm}} Private.

- Utilizza il comando `kubectl logs` per visualizzare il log del contenitore all'interno del pod. Se non sei sicuro del tuo nome del pod, immetti il seguente comando per visualizzare il tuo elenco di pod.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Successivamente, esegui questo comando per richiamare i log per il contenitore orderer che risiede all'interno del pod sostituendo `pod_name` con il nome del tuo pod dal precedente output del comando:

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  Per ulteriori informazioni sul comando `kubectl logs`, consulta la [documentazione Kubernetes ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- In alternativa, puoi accedere ai log utilizzando la [console di gestione del cluster {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/troubleshoot/events.html), che apre i log in Kibana.

  **Nota:** quando visualizzi i tuoi log in Kibana, potresti ricevere la risposta `No results found`. Questa condizione può verificarsi se {{site.data.keyword.cloud_notm}} Private utilizza l'indirizzo IP del tuo nodo di lavoro come suo nome host. Per risolvere questo problema, rimuovi il filtro che inizia con `node.hostname.keyword` all'inizio del pannello e i log diventeranno visibili.

## Risoluzione dei problemi
{: #icp-orderer-operate-troubleshooting}

### **Problema:** il comando `peer channel update` ha esito negativo con un errore.
{: #icp-orderer-operate-peer-channel-update-error}

È possibile ricevere il seguente errore quando esegui un comando `peer channel update`:

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

Questo errore può verificarsi quando il peer sta utilizzando lo stesso ID MSP dell'organizzazione dell'ordinante.  

**Soluzione:**  

Per risolvere questo problema devi ridistribuire il tuo peer specificando un ID MSP dell'organizzazione diverso rispetto a quello utilizzato durante la distribuzione dell'ordinante.

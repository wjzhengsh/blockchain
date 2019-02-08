---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Gestione dei peer in AWS
{: #remote-peer-operate-aws}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Dopo che hai configurato i peer {{site.data.keyword.blockchainfull}} Platform in AWS, devi completare diversi passi operativi prima che il tuo peer possa emettere transazioni per eseguire query e richiami del libro mastro della rete blockchain. I passi includono l'aggiunta della tua organizzazione a un canale, l'unione del tuo peer al canale, l'installazione del chaincode sul peer, l'istanziazione del chaincode sul canale e la connessione delle applicazioni al tuo peer. Puoi utilizzare gli [SDK Fabric](#aws-peer-operate-with-sdk) o la [riga di comando](#aws-peer-cli-operate) per completare questi passi operativi. Gli SDK Fabric sono il percorso consigliato, sebbene le istruzioni presuppongano la familiarità con il funzionamento dell'SDK.

**Nota**: un peer {{site.data.keyword.blockchainfull_notm}} Platform in AWS non ha accesso alla piena funzionalità o al supporto completo dei peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform. Di conseguenza, non puoi utilizzare il Monitoraggio della rete per gestire un peer. Prima di iniziare a eseguire i peer in AWS, assicurati di aver esaminato le [considerazioni](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Utilizzo degli SDK Fabric per gestire il peer
{: #aws-peer-operate-with-sdk}

Gli SDK Hyperledger Fabric forniscono una potente serie di API che consentono alle applicazioni di interagire con le reti blockchain e di gestire tali reti. Puoi trovare l'elenco più aggiornato dei linguaggi supportati e l'elenco completo delle API disponibili all'interno degli SDK Hyperledger Fabric nella documentazione della [documentazione della community degli SDK Hyperledger Fabric![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Community degli SDK Hyperledger Fabric"). Puoi utilizzare gli SDK Fabric per unire il tuo peer a un canale su {{site.data.keyword.blockchainfull_notm}} Platform, installare un chaincode sul tuo peer e istanziare il chaincode su un canale.

Le seguenti istruzioni utilizzano l'[SDK Node di Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Hyperledger Fabric per jode.js") per gestire il peer e presumono una precedente dimestichezza con l'SDK. Puoi utilizzare l'[esercitazione sullo sviluppo di applicazioni](/docs/services/blockchain/v10_application.html) per imparare a utilizzare l'SDK Node prima di iniziare e come guida allo sviluppo di applicazioni con il tuo peer quando sei pronto ad eseguire richiami e query del chaincode.

Il peer {{site.data.keyword.blockchainfull_notm}} Platform su Quick Start AWS crea due peer per l'alta disponibilità. Pertanto, devi seguire i passi operativi una volta per ogni peer. Quando sei pronto ad eseguire query e richiami del chaincode dalla tua applicazione, fai in modo che il tuo SDK si connetta a entrambi i peer per garantire che le tue [applicazioni siano altamente disponibili](/docs/services/blockchain/v10_application.html#ha-app).

### Installazione dell'SDK Node

Puoi utilizzare NPM per installare l'[SDK Node ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Hyperledger Fabric per jode.js"):
```
npm install fabric-client@1.2
```
{:codeblock}

Si consiglia di utilizzare la versione 1.2 dell'SDK Node.

### Preparazione dell'SDK per lavorare con il peer
{: #remote-peer-node-sdk}

Prima di utilizzare l'SDK per gestire il peer, devi generare i certificati necessari (iscrizione) che consentiranno alla tua applicazione di comunicare con la tua rete su {{site.data.keyword.blockchainfull_notm}} Platform e con il tuo peer. Segui la procedura per eseguire l'[iscrizione con l'SDK](/docs/services/blockchain/v10_application.html#enroll-app-sdk) utilizzando la tua identità **admin**. L'esercitazione [Sviluppo di applicazioni](/docs/services/blockchain/v10_application.html) esegue anche l'iscrizione come **admin**, pertanto non devi modificare il codice di esempio.

### Caricamento di un certificato di firma su IBM Blockchain Platform
{: #remote-peer-upload-SDK}

Devi caricare il tuo certificato di firma SDK sulla rete in {{site.data.keyword.blockchainfull_notm}} Platform in modo che gli altri membri possano riconoscere la tua firma digitale.

- Puoi trovare il tuo certificato di firma nella directory in cui l'SDK ha generato il tuo materiale crittografico, nel file denominato admin. Copia il certificato all'interno delle virgolette dopo il campo `certificate`, che inizia con  `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE-----`. Puoi utilizzare la CLI per convertire il certificato in formato PEM immettendo il comando `echo -e "<CERT>" > admin.pem`. Puoi quindi incollare il contenuto del certificato nella tua rete blockchain utilizzando il Monitoraggio della rete. Accedi alla rete su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata "Membri" del Monitoraggio della rete, fai clic su **Certificati** > **Aggiungi certificato**. Specifica un qualsiasi nome per il certificato e incolla il contenuto nel campo **Certificato**. Fai clic su **Riavvia** quando ti viene richiesto se vuoi riavviare i tuoi peer. Questa azione deve essere eseguita prima che la tua organizzazione si unisca al canale.

### Caricamento di un certificato di firma sul peer
{: #remote-peer-upload-signcert}

Devi anche caricare il certificato di firma dell'SDK nel peer remoto e riavviarlo. Devi installare lo stesso certificato di firma che hai [caricato su IBM Blockchain Platform](#remote-peer-upload-SDK) all'interno del contenitore peer remoto.

Esegui SSH nella tua istanza VPC selezionando l'istanza nella console AWS (fai clic su **Services > EC2 > Instances**) e facendo quindi clic sul pulsante Connect. Attieniti alle istruzioni da AWS per immettere il comando ssh.

Esegui i seguenti comandi dall'interno del contenitore peer per caricare il tuo certificato.  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- Sostituisci `<PEER_ENROLL_ID>` con l'ID di iscrizione specificato nel template Quick Start e associato a questa istanza del peer remoto.  
- Sostituisci `<CERT.PEM>` con il tuo certificato di firma, che inizia con `-----BEGIN CERTIFICATE-----` e finisce con `-----END CERTIFICATE-----`.    

  **Nota:** se il file `cert.pem` esiste, non sovrascriverlo ma crea un nuovo file, ad esempio `cert2.pem`.

Poiché aggiungi un nuovo certificato, devi riavviare il contenitore affinché il peer recuperi il certificato. Segui queste [istruzioni](#remote-peer-aws-restart) per riavviare il tuo peer.

### Passaggio del certificato TLS del tuo peer all'SDK
{: #aws-remote-peer-download-tlscert}

Devi copiare il contenuto del `cacert.pem` TLS dal contenitore peer alla tua applicazione per autenticare la comunicazione dal tuo SDK. Esegui il seguente comando nel contenitore del peer. Sostituisci `<PEER_ENROLL_ID>` con il nome dello stack del peer remoto specificato nel template Quick Start. (Ricordati che vengono create due istanze VPC).
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

Copia negli appunti l'intero testo del file `ca.crt`, che inizia con il primo `-----BEGIN CERTIFICATE-----` e termina con l'ultimo `-----END CERTIFICATE-----`. Salva questo certificato nel tuo file system locale in formato PEM. Puoi quindi importare il certificato TLS nella tua applicazione utilizzando un semplice comando di lettura file.
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### Trasmissione delle informazioni sull'endpoint del peer all'SDK
{: #remote-peer-SDK-endpoints}

Per richiamare le informazioni sull'endpoint del tuo peer, individua la tua istanza peer nella console AWS. Prendi nota del valore di `AWS EC2 dashboard Public DNS (IPv4)` per l'istanza EC2 e forniscilo all'SDK dichiarando una nuova variabile peer oppure aggiornando il tuo profilo di connessione. Concatena l'indirizzo DNS pubblico con la porta `7051`. Il seguente esempio definisce il peer sulla tua rete fabric e gli trasmette il certificato TLS che hai importato.

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**Nota:** poiché il peer è remoto, le altre organizzazioni sulla rete {{site.data.keyword.blockchainfull_notm}} Platform non saranno in grado di trovare le informazioni sull'endpoint del tuo peer nel loro profilo di connessione. Se altre organizzazioni devono inviare al tuo peer una transazione da approvare, devi fornire l'IP pubblico e i certificati TLS in un'operazione fuori banda.

### Utilizzo dell'SDK per l'adesione a un canale
{: #remote-peer-join-channel-sdk}

Come membro della rete blockchain, la tua organizzazione deve essere aggiunta a un canale nella rete prima che tu possa unire il tuo peer al canale. 

  - Puoi avviare un nuovo canale per il peer. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [aggiornamento canale](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). 

    Una volta che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il certificato di firma del peer al canale in modo che gli altri membri possano verificare la tua firma digitale durante le transazioni. Il peer carica il proprio certificato di firma durante l'installazione, pertanto dovrai solo sincronizzare il certificato al canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si unisce la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale. Prima di immettere i comandi di unione al canale, potresti dover attendere qualche minuto in modo che la sincronizzazione del canale possa essere completata.

Una volta che la tua organizzazione fa parte del canale, segui le istruzioni relative all'[Unione a un canale](/docs/services/blockchain/v10_application.html#join-channel-sdk). Devi fornire l'URL del servizio ordini e il nome del canale.

### Utilizzo dell'SDK per installare il chaincode sul peer
{: #remote-peer-install-cc-sdk}

Attieniti alle istruzioni per utilizzare l'SDK per [installare un chaincode](/docs/services/blockchain/v10_application.html#install-cc-sdk) sul tuo peer.

### Utilizzo dell'SDK per istanziare il chaincode sul canale
{: #remote-peer-instantiate-cc-sdk}

Solo un membro del canale deve istanziare o aggiornare il chaincode. Pertanto, qualsiasi membro di rete del canale con i peer su {{site.data.keyword.blockchainfull_notm}} Platform può utilizzare il Monitoraggio della rete per istanziare il chaincode e specificare le politiche di approvazione. Tuttavia, se vuoi utilizzare il peer per istanziare il chaincode su un canale, puoi usare l'SDK e attenerti alle istruzioni per [istanziare un chaincode](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).


## Utilizzo della CLI per gestire il peer
{: #aws-peer-cli-operate}

Puoi gestire il tuo peer anche dalla riga di comando utilizzando il client CA Fabric e il contenitore degli strumenti Fabric. In queste istruzioni, genereremo prima i certificati richiesti utilizzando il client CA Fabric. Utilizzeremo quindi il contenitore degli strumenti Fabric per gestire il peer unendolo a un canale, installando un chaincode e istanziando quindi il chaincode su un canale.

### Iscrizione mediante il client CA Fabric
{: #peer-client-enroll}

Il primo passo consiste nel generare i certificati richiesti (iscrizione) utilizzando il client CA Fabric. Devi installare prima il client CA Fabric. Scarica i [file binari fabric-ca v1.2.0 per la tua piattaforma](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) sulla tua macchina locale, estraili e spostali in una cartella come `$HOME/fabric-ca-remote/`.

1.  Prepara il tuo ambiente per utilizzare il client CA Fabric. Passa alla directory in cui hai spostato i file binari del client in modo da poter fare riferimento a essa direttamente nei comandi.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Imposta il percorso in cui il client può creare le tue chiavi.  Se non hai già eseguito il comando `enroll`, la cartella `msp` e il file `.yaml` non esistono. Se hai già eseguito il comando `enroll`, è importante eliminare qualsiasi materiale di configurazione dai tentativi di iscrizione precedenti utilizzando i comandi `rm` riportati di seguito:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Dovrai richiamare le informazioni sull'endpoint della tua CA (Certificate Authority) su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata "Panoramica" del Monitoraggio della rete, fai clic sul pulsante **Profilo connessione** e apri il file JSON che contiene le credenziali di rete. Scorri fino alla sezione `certificateAuthorities` e prendi nota delle informazioni indicate di seguito.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  Dovrai anche richiamare un certificato TLS da {{site.data.keyword.blockchainfull_notm}} Platform per comunicare con la CA. Le credenziali di rete sopra riportate includono il certificato necessario. Nella sezione `certificateAuthorities` del file Profilo connessione, copia il certificato che segue `"pem:"`, che inizia con `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE----- `. **Non includere le virgolette**.

    Immetti il seguente comando da una finestra di terminale, sostituendo `<certificate>` con il certificato che hai copiato.
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 Memorizza questo certificato in un posto da cui puoi fare riferimento ad esso nei comandi futuri.  

5.  Sei ora pronto per generare i certificati richiesti. Immetti il seguente comando:
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    Ad esempio:
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

   Se il comando viene completato correttamente, puoi visualizzare una risposta simile al seguente esempio:
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    Il comando genererà i certificati richiesti e li memorizzerà quindi in una cartella denominata `msp` in `$FABRIC_CA_CLIENT_HOME`. Questa cartella e i certificati all'interno saranno importanti nei seguenti passi.

### Gestione dei certificati sul tuo sistema locale
{: #manage-certs}

Prima di poter operare con il peer, dobbiamo eseguire alcune operazioni di gestione dei certificati sulla nostra macchina locale e caricare alcuni dei certificati che il client CA Fabric ha generato su {{site.data.keyword.blockchainfull_notm}} Platform e nel tuo peer. Dobbiamo anche scaricare i certificati TLS dalla piattaforma e dal peer. Se vuoi saperne di più sui certificati con cui lavorerai e sulle attività da svolgere, visita [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

Sulla tua macchina locale, apri un terminale di comando e passa alla directory in cui hai spostato i file binari del client CA Fabric e memorizzato la cartella MSP.

1. Copia il file `cert.pem` dalla cartella `signcerts` in una nuova cartella `admincerts`.
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. Copia il certificato TLS del tuo ordinante da {{site.data.keyword.blockchainfull_notm}} Platform nella cartella MSP. Il tuo profilo di connessione contiene il certificato necessario. Nella schermata "Panoramica" del Monitoraggio della rete, fai clic sul pulsante **Profilo connessione** e apri il file JSON che contiene le credenziali di rete. Vai alla sezione `orderers`. Copia il certificato che segue `"pem:"`, iniziando con `-----BEGIN CERTIFICATE-----` e finendo con `-----END CERTIFICATE----- `. Non includere le virgolette.

    Dalla finestra del terminale, esegui questi comandi:
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. Devi anche copiare il certificato TLS del tuo peer dal contenitore peer su AWS alla tua macchina locale. 

    - [Attieniti a queste istruzioni](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test) per eseguire l'accesso al tuo contenitore peer ed eseguire il seguente comando, sostituendo <PEER_ENROLL_ID> con il nome dello stack del peer, che hai specificato nel template Quick Start, seguito dal suo numero. (Ricordati che vengono create due istanze VPC).
      ```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
      ```
      {:codeblock}

      Copia negli appunti l'intero testo del file `cacert.pem`, che inizia con il primo `-----BEGIN CERTIFICATE-----` e termina con l'ultimo `-----END CERTIFICATE-----`.

    - Torna alla tua macchina locale e immetti i seguenti comandi. Sostituisci il testo `<CACERT.PEM>` con il testo che hai copiato negli appunti.
      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
      ```
      {:codeblock}

      **Nota:** per impostazione predefinita, il template Quick Start AWS crea due istanze peer in due diverse zone di disponibilità. Se già hai completato questa procedura per uno di questi peer, quando la esegui per la seconda istanza il file cacert.pem esisterà già. Procedi e sostituiscilo con il certificato dal secondo peer.

4. Per fornire alla tua CLI l'autorizzazione per installare il chaincode sul peer, carica il signCert generato dal client CA Fabric nella cartella admin dei peer e riavvia il peer. Pertanto, copia il certificato `admincert/cert.pem` dalla tua macchina locale nella directory `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` nel contenitore peer come `cert2.pem`.

    - Sulla tua macchina locale, immetti i seguenti comandi:

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copia negli appunti il testo del file `cert.pem` che inizia con `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE-----`.

    - Torna al contenitore Peer e immetti i seguenti comandi per aggiungere cert.pem ai certificati peer. Sostituisci il testo `<CERT.PEM>` con il testo che hai copiato negli appunti.

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Nota:** un file cert.pem esiste già in questa directory. Non sovrascriverlo.

    - Poiché abbiamo aggiunto un nuovo certificato, dobbiamo riavviare il contenitore perché il peer acquisisca il certificato, Attieniti a queste istruzioni per [riavviare il contenitore peer](#remote-peer-aws-restart).

5. Carica lo stesso certificato `admincert/cert.pem` dalla tua macchina locale su {{site.data.keyword.blockchainfull_notm}} Platform affinché una CLI o un'applicazione remota eseguano operazioni di canale come il recupero del blocco genesi del canale e l'istanziazione del chaincode.
    1. Sulla tua macchina locale, taglia il file `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` e incollalo negli appunti.
    2. Accedi al Monitoraggio della rete della tua rete su {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Fai clic su **Membri** nel navigatore di sinistra e fai clic sulla scheda **Certificati**.
    4. Fai clic sul pulsante **Aggiungi certificato**.
    5. Nella finestra **Aggiungi certificato**, fornisci un nome al tuo certificato, ad esempio `fabrictools.pem`, incolla il certificato che hai appena copiato negli appunti e fai clic su **Invia**.
    6. Ti verrà chiesto se vuoi riavviare i peer. Fai clic su **Riavvia**.
    7. Fai clic su **Canali** nel navigatore di sinistra e individua il canale a cui si unirà il peer.
    8. Fai clic sui tre punti sotto l'intestazione **Azioni** e fai clic su **Sincronizza certificato**. Nella finestra **Sincronizza certificato**, fai clic su **Invia**.

    **Nota**: è importante sincronizzare il certificato del canale prima che il peer si unisca al canale o istanzi il chaincode sul canale. Prima di immettere i comandi di unione al canale o di istanziazione del chaincode, potresti dover attendere qualche minuto in modo che la sincronizzazione del canale possa essere completata.   

### Configurazione del contenitore degli strumenti Fabric
{: #setup-fabric-cli}

Dopo che hai spostato tutti i nostri certificati nell'ubicazione necessaria, puoi installare e utilizzare il contenitore degli strumenti Fabric da Docker. Assicurati di eseguire questi comandi dalla directory in cui hai memorizzato la tua cartella MSP.

1.  Scarica l'immagine Docker degli strumenti Fabric con il seguente comando:
    ```
    docker pull ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

2.  Immetti i seguenti comandi per avviare il contenitore degli strumenti Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

    Questo comando monterà la directory MSP all'interno del tuo contenitore degli strumenti.

<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### Utilizzo della CLI degli strumenti Fabric per unire il peer al canale
{: #fabric-cli-join-peer-to-channel}

Prima di poter eseguire i comandi della CLI per unire il peer a un canale, è necessario aggiungere la tua organizzazione a un canale nella rete.

  - Puoi avviare un nuovo canale per il peer. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [aggiornamento canale](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). 

    Una volta che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il certificato di firma del peer al canale in modo che gli altri membri possano verificare la tua firma digitale durante le transazioni. Il peer carica il proprio certificato di firma durante l'installazione, pertanto dovrai solo sincronizzare il certificato al canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si unisce la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale.

1. Richiama le informazioni di configurazione dal tuo profilo di connessione (`Connection Profile`) disponibile nel pannello Panoramica del Monitoraggio della rete. Fai clic su **Profilo connessione** e quindi su **Scarica**.

  - Trova l'URL ordinante ricercando gli **ordinanti**, che puoi trovare in `orderers > url`. Prendi nota dell'URL che inizia con il nome della rete. L'URL è simile al seguente esempio:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Trova il nome della tua organizzazione ricercando le **organizzazioni**. Questa deve essere la stessa organizzazione utilizzata per registrare il tuo peer. Puoi trovare il nome della tua organizzazione insieme al suo `mspid` associato. Prendi nota del valore dell'`mspid`.

2. Individua le informazioni sull'endpoint del tuo peer VPC AWS (l'indirizzo IPv4 DNS pubblico) in AWS EC2 dashboard Public DNS (IPv4). Sarà qualcosa di simile a:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  Utilizza queste informazioni per creare la variabile di ambiente `PEERADDR` concatenando l'indirizzo con la porta '7051', in modo che il valore di `PEERADDR` sia simile a:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. Esegui i seguenti comandi per impostare le variabili di ambiente all'interno del contenitore degli strumenti Fabric.

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
      - Sostituisci `<PEER_ADDR>` con il valore che hai creato nel passo precedente.  

  Ad esempio:

    ```
    export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
    export CHANNEL=defaultchannel
    export CC_NAME=mycc
    export ORGID=PeerOrg1
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. Recupera il blocco genesi del canale per creare il libro mastro del canale sul tuo peer. Esegui prima `cd` alla tua directory root ed esegui quindi il comando per recuperare il blocco genesi.

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
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

  Dopo che il blocco genesi è stato aggiunto correttamente, dovresti vedere qualcosa di simile al seguente esempio:
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. Ora unisci il peer al canale, passando il blocco genesi con il seguente comando:

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Quando tale operazione viene eseguita con esito positivo, dovresti vedere qualcosa di simile a:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilizzo del contenitore di strumenti Fabric per installare il chaincode sul peer
{: #aws-toolcontainer-install-cc}

A questo punto, siamo pronti per installare e istanziare il chaincode sul peer. Per queste istruzioni, installeremo il chaincode `fabcar` dal repository `fabric-samples`. Scarica il chaincode `fabric-samples` da GitHub utilizzando i seguenti comandi:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Esegui questo comando della CLI del peer per installare il chaincode `fabcar` nel peer. 

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilizzo del contenitore degli strumenti Fabric per istanziare il chaincode su un canale
{: #aws-toolcontainer-instantiate-cc}

Poiché un solo peer deve istanziare il chaincode su un canale, non sei tenuto a utilizzare il tuo peer per istanziare il chaincode. I peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform possono eseguire questa operazione. Tuttavia, se vuoi che il peer istanzi il chaincode sul canale, puoi immettere il seguente comando nel contenitore degli strumenti Fabric:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Riavvio di un peer che è in esecuzione in AWS
{: #remote-peer-aws-restart}

Puoi avviare, arrestare o riavviare il peer nell'ambiente in cui distribuisci il peer. Ogni volta che un certificato viene aggiunto al peer, è necessario riavviare il peer affinché il nuovo certificato venga riconosciuto. Questa operazione può essere eseguita in più modi:

- Nella console AWS, riavvia l'istanza VPC del peer.
- Dall'interno del tuo contenitore peer, esegui questo comando:

 ```
 docker restart peer
 ```
 {:codeblock}  

Inoltre, puoi utilizzare la [richiesta HEAD](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes) per verificare la disponibilità del tuo peer.

## Visualizzazione dei log di peer

Esegui SSH nell'istanza peer VPC AWS ed esegui quindi questo comando per visualizzare i log di peer:

```
docker logs peer
```
{:codeblock}

## Aggiornamento del chaincode

Nel corso del tempo, è probabile che tu debba modificare il chaincode in esecuzione sul peer. Poiché il chaincode è installato sui peer e istanziato sul canale, devi aggiornare il chaincode su tutti i peer presenti sul canale.

Completa la seguente procedura per aggiornare il tuo chaincode:

1. Per aggiornare il chaincode su ogni peer in AWS, è sufficiente rieseguire il processo che hai utilizzato per installare il chaincode sui peer, utilizzando un'applicazione client o un comando della CLI. Assicurati di specificare lo stesso nome chaincode utilizzato originariamente. Tuttavia, questa volta incrementa il numero di `Version` del chaincode.

2. Dopo aver installato il nuovo chaincode su tutti i peer nel canale, utilizza il Monitoraggio della rete o il comando
[peer chaincode upgrade ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) per aggiornare il canale per utilizzare il nuovo chaincode.

Vedi il passo due di queste [istruzioni](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) per ulteriori informazioni sull'utilizzo del pannello Installa il codice del Monitoraggio della rete per aggiornare il chaincode sul canale.

## Risoluzione dei problemi

### **Problema:** il peer remoto non è in grado di stabilire una connessione alla rete blockchain

La creazione dello stack viene completata correttamente ma i log Docker contengono l'errore:

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

**Soluzione:**  
Questo errore può essere causato da una mancata specifica di una porta sul CAUrl quando è stato distribuito il template Quick Start.
Il CAUrl dovrebbe essere simile a `https://<network>-org1-ca.stage.blockchain.ibm.com:31011`.
Ridistribuisci il template Quick Start, facendo attenzione a specificare il valore appropriato per il CAUrl.

### **Problema:** l'istanziazione del chaincode non riesce con un errore

L'istanziazione del chaincode non riesce con:
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**Soluzione:**   
Assicurati che, dopo che il certificato di gestione è stato caricato nel Monitoraggio della rete, venga eseguita la sincronizzazione dei certificati sul canale. Per ulteriori informazioni, fai riferimento al passo cinque di queste [istruzioni](#manage-certs). Tieni presente che è importante sincronizzare il certificato del canale prima che il peer si unisca al canale.

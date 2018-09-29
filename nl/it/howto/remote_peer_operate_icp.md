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

# Gestione dei peer remoti in {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-operate}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Dopo aver configurato i peer remoti di {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.cloud_notm}} Private (ICP), devi completare diversi passi operativi prima che il tuo peer possa emettere transazioni per eseguire query e richiamare il libro mastro della rete blockchain. I passi includono l'aggiunta della tua organizzazione a un canale, l'unione del tuo peer remoto al canale, l'installazione del chaincode sul peer remoto, l'istanziazione del chaincode sul canale e la connessione delle applicazioni al peer remoto.
{:shortdesc}

Per gestire il tuo peer remoto devi prima completare alcune operazioni preliminari dal tuo cluster ICP.

**Prerequisiti:**
- [Richiamo delle informazioni sull'endpoint del peer remoto](#remote-peer-retrieve-endpoint-info)
- [Accesso al tuo contenitore peer utilizzando kubectl](#remote-peer-kubectl-configure)
- [Riavvio del peer remoto](#remote-peer-restart)

Puoi quindi utilizzare uno dei seguenti metodi per gestire il tuo peer remoto.

**Percorsi operativi:**
- [Utilizzo degli SDK Fabric](#remote-peer-operate-with-sdk)
- [Utilizzo della riga di comando](#remote-peer-cli-operate)

Gli SDK Fabric sono il percorso consigliato, sebbene le istruzioni presuppongano la familiarità con il funzionamento dell'SDK.

Si consiglia di distribuire almeno due istanze del grafico helm del peer remoto per l'[alta disponibilità](remote_peer_icp.html#high-availability). Pertanto, devi seguire questi passi operativi una volta per ogni peer. Quando sei pronto per richiamare e interrogare tramite query il chaincode dalla tua applicazione, connettiti a entrambi i peer per assicurarti che le tue [applicazioni siano altamente disponibili](../v10_application.html#ha-app).

**Nota**: un peer remoto di {{site.data.keyword.blockchainfull_notm}} Platform non ha accesso alle funzionalità o al supporto completo dei peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform. Di conseguenza, non puoi utilizzare il Monitoraggio della rete per gestire un peer remoto. Prima di iniziare a eseguire i peer remoti, assicurati di aver esaminato le [considerazioni](remote_peer.html#remote-peer-limitations).

## Prerequisiti

Sia che tu scelga di utilizzare gli SDK o la riga di comando, devi completare i passi indicati in questa sezione per la gestione del tuo peer. Prima di iniziare, puoi completare i primi di due passi, ossia richiamare l'endpoint del peer e configurare kubectl.

### Richiamo delle informazioni sull'endpoint del peer remoto
{: #remote-peer-retrieve-endpoint-info}

Accedi alla tua console ICP. Fai clic sull'icona **Menu** nell'angolo superiore sinistro. Fai clic su **Carico di lavoro** > **Distribuzioni**. Quindi, trova e fai clic sul nome dell'istanza del peer remoto per aprire la schermata di panoramica della distribuzione. Scorri fino alla sezione **Esponi dettagli** e cerca l'`Ingress IP` e `Ports` del tuo peer remoto.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

Prendi nota dell'indirizzo IP Ingress e delle porte del nodo per connetterti al peer remoto. Devi utilizzare queste informazioni quando installi e istanzi il chaincode sul peer remoto. Nel seguente esempio, l'indirizzo del tuo peer è `9.46.126.89:30162`, `9.46.126.89:30260` o `9.46.126.89:32051`.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### Configurazione della CLI kubectl
{: #remote-peer-kubectl-configure}

Devi utilizzare lo strumento di riga di comando **kubectl** per eseguire alcune operazioni necessarie all'interno del contenitore peer eseguito in ICP. Accedi al tuo cluster utilizzando lo strumento della CLI {{site.data.keyword.cloud_notm}} Private:

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

Accedi quindi al dashboard del tuo cluster ICP. Fai clic sul tuo avatar utente nell'angolo superiore destro dell'interfaccia utente ICP e quindi fai clic su **Configura client**. Copia, incolla ed esegui i comandi nella finestra del terminale sulla tua macchina locale. Puoi verificare che la configurazione abbia funzionato correttamente immettendo il seguente comando:

```
kubectl -n <namespace> get pods
```
{:codeblock}

dove `-n <namespace>` deve essere specificato quando non viene utilizzato lo spazio dei nomi `default`. Sostituisci `<namespace>` con il valore del
tuo spazio dei nomi nel cluster ICP.


Il comando dovrebbe generare una risposta, che è simile al seguente esempio. `remotepeer-bd65c4769-95rmm` è il nome del contenitore peer.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

Se non riconosci il nome pod dei tuoi peer remoti dalla risposta, puoi trovare il nome del pod nel riquadro **Pod** del pannello di distribuzione.

Effettua l'esecuzione nel contenitore peer remoto immettendo il seguente comando, sostituendo il valore di `<PEER_CONTAINER_NAME>` con il nome del Pod recuperato nel passo precedente.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
dove `-n <namespace>` deve essere specificato quando non viene utilizzato lo spazio dei nomi `default`. Sostituisci `<namespace>` con il valore del
tuo spazio dei nomi nel cluster ICP.

Ad esempio:

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

Sei ora pronto per utilizzare la CLI del contenitore peer. Devi utilizzare la CLI per scaricare il certificato TLS dei peer e caricare il certificato di firma nel peer se utilizzi gli SDK o la riga di comando.

## Riavvio di un peer remoto eseguito in ICP
{: #remote-peer-restart}

Ogni volta che un certificato viene aggiunto al peer remoto, è necessario riavviare il nodo peer affinché riconosca il nuovo certificato.

Puoi utilizzare i comandi **kubectl** per riavviare il peer remoto eseguito in ICP. Il comando richiede come parametri il nome del **pod** del peer remoto e il suo **contenitore**. Per ulteriori informazioni sull'utilizzo dei comandi kubectl, vedi [Configurazione della CLI kubectl](#remote-peer-kubectl-configure).

1. Nella console ICP, trova il nome **pod** e il nome **contenitore** per la tua distribuzione del peer remoto.

  1. Fai clic sull'icona di menu nell'angolo superiore sinistro della console e quindi su **Carichi di lavoro > Distribuzioni**.
  2. Individua e fai clic sulla release del peer remoto nella tabella per aprirla.
  3. Scorri verso il basso per visualizzare il nome del **pod** associato. Prendi nota del nome del **pod**.
  4. Fai clic sul pod per aprirlo.
  5. Fai clic sulla scheda **Contenitori**. Prendi nota del nome del **contenitore** peer remoto.

2. Dalla tua riga di comando, immetti il seguente comando per riavviare il peer sostituendo `<REMOTE_PEER_POD_NAME>` e `<REMOTE_PEER_CONTAINER_NAME>` con i valori recuperati dal passo precedente.

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. Puoi verificare che il peer remoto sia stato riavviato eseguendo il comando `kubectl get pods` ed esaminando l'output per il conteggio di **RESTART** del tuo pod.

Inoltre, puoi utilizzare la [richiesta HEAD](monitor_network.html#monitor-nodes) per verificare la disponibilità del tuo peer remoto.


## Utilizzo di SDK Fabric per gestire il peer remoto
{: #remote-peer-operate-with-sdk}

Gli SDK Hyperledger Fabric forniscono una potente serie di API che consentono alle applicazioni di interagire con le reti blockchain e di gestire tali reti. Puoi trovare l'elenco più aggiornato dei linguaggi supportati e l'elenco completo delle API disponibili all'interno degli SDK Fabric nella [documentazione della community Hyperledger Fabric SDK ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Documentazione della community Hyperledger Fabric SDK"). Puoi utilizzare gli SDK Fabric per unire il tuo peer remoto a un canale su {{site.data.keyword.blockchainfull_notm}} Platform, installare un chaincode sul tuo peer e istanziare il chaincode su un canale.

Le seguenti istruzioni utilizzano l'[SDK Node Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node Fabric") per gestire il peer remoto e assumere una certa familiarità con l'SDK. Puoi utilizzare l'[esercitazione sullo sviluppo di applicazioni](../v10_application.html) per imparare a utilizzare l'SDK Node prima di iniziare e come guida allo sviluppo di applicazioni con il tuo peer remoto quando sei pronto a richiamare e interrogare il chaincode.

### Installazione dell'SDK Node

Puoi utilizzare NPM per installare l'[SDK Node ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fabric-sdk-node.github.io/ "SDK Node"):
```
npm install fabric-client@1.1
```
{:codeblock}

Si consiglia di utilizzare la versione 1.1 dell'SDK Node.

### Preparazione dell'SDK per lavorare con il peer remoto
{: #remote-peer-node-sdk}

Prima di utilizzare l'SDK per gestire il peer remoto, devi generare i certificati necessari (registrazione) che consentiranno alla tua applicazione di comunicare con la rete su {{site.data.keyword.blockchainfull_notm}} Platform e con il tuo peer remoto. Segui la procedura per eseguire la [registrazione con l'SDK](../v10_application.html#enroll-app-sdk) utilizzando la tua identità **admin**. L'esercitazione [Sviluppo di applicazioni](../v10_application.html) esegue anche la registrazione come **admin**, pertanto non devi modificare il codice di esempio.

### Caricamento di un signCert in {{site.data.keyword.blockchainfull_notm}} Platform
{: #remote-peer-upload-sdk}

Devi caricare il certificato di firma dell'SDK nella rete su {{site.data.keyword.blockchainfull_notm}} Platform in modo che la tua applicazione abbia l'autorizzazione per utilizzare la rete.

- Puoi trovare il tuo certificato di firma nella directory in cui l'SDK ha generato il tuo materiale crittografico, nel file denominato admin. Copia il certificato all'interno delle virgolette dopo il campo `certificate`, che inizia con  `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE-----`. Puoi utilizzare la CLI per convertire il certificato in formato PEM immettendo il comando `echo -e "<CERT>" > admin.pem`. Puoi quindi incollare il contenuto del certificato nella tua rete blockchain utilizzando il Monitoraggio della rete. Accedi alla rete su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata "Membri" del Monitoraggio della rete, fai clic su **Certificati** > **Aggiungi certificato**. Specifica un qualsiasi nome per il certificato e incolla il contenuto nel campo **Certificato**. Fai clic su **Riavvia** quando ti viene richiesto se vuoi riavviare i tuoi peer. Questa azione deve essere eseguita prima che la tua organizzazione si unisca al canale.

### Caricamento di un signCert nel peer remoto
{: #remote-peer-upload-signcert}

Devi anche caricare il certificato di firma dell'SDK nel peer remoto e riavviarlo. Dovrai installare lo stesso certificato di firma che hai [caricato in {{site.data.keyword.blockchainfull_notm}} Platform](#remote-peer-upload-sdk) all'interno del contenitore peer remoto.

Immetti i seguenti comandi dall'[interno del tuo contenitore peer](#remote-peer-kubectl-configure) per caricare il certificato.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

Sostituisci il testo `<CERT.PEM>` con il tuo signCert, che inizia con `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE-----`. **Nota** che un file cert.pem esiste già in questa directory. Denomina il nuovo certificato in `cert2.pem` o qualcosa di simile per evitare di sovrascrivere il certificato già presente nel contenitore.

Poiché hai aggiunto un nuovo certificato, devi riavviare il contenitore affinché il peer recuperi il certificato. Segui queste [istruzioni](#remote-peer-restart) per riavviare il peer.

### Passaggio del certificato TLS del tuo peer remoto all'SDK
{: #icp-remote-peer-download-tlscert}

Devi copiare il contenuto del `cacert.pem` TLS dal contenitore peer alla tua applicazione per autenticare la comunicazione dal tuo SDK. Nella [CLI del contenitore peer](#remote-peer-kubectl-configure), esegui questo comando:
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

Copia negli appunti l'intero testo del file `cacert.pem`, che inizia con il primo `-----BEGIN CERTIFICATE-----` e termina con l'ultimo `-----END CERTIFICATE-----`. Salva questo certificato nel tuo file system locale in formato PEM. Puoi quindi importare il certificato TLS nella tua applicazione utilizzando un semplice comando di lettura file.
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### Trasmissione delle informazioni sull'endpoint del peer remoto all'SDK
{: #remote-peer-SDK-endpoints}

Trova le [informazioni sull'endpoint del tuo peer remoto](#remote-peer-retrieve-endpoint-info) e forniscile all'SDK dichiarando una nuova variabile peer o aggiornando il tuo Profilo connessione. Il seguente esempio definisce il peer come endpoint sulla tua rete fabric e lo trasmette al certificato TLS che hai importato.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

Devi specificare un `ssl-target-name-override` di `<something>.blockchain.com` in modo che il peer possa risolvere la richiesta DNS.

**Nota:** poiché il peer è remoto, le altre organizzazioni sulla rete {{site.data.keyword.blockchainfull_notm}} Platform non saranno in grado di trovare le informazioni sull'endpoint del tuo peer nel loro Profilo connessione. Se altre organizzazioni devono inviare al tuo peer remoto una transazione da approvare, devi fornire l'IP pubblico e i certificati TLS in un'operazione fuori banda.

### Utilizzo dell'SDK per l'adesione a un canale
{: #remote-peer-join-channel-sdk}

Come membro della rete blockchain, la tua organizzazione deve essere aggiunta a un canale nella rete prima che tu possa unire il tuo peer remoto al canale. 

  - Puoi avviare un nuovo canale per il peer remoto. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](create_channel.html#creating-a-channel). Tieni presente che devi disporre di almeno un peer su {{site.data.keyword.blockchainfull_notm}} Platform prima di poter creare un canale nel Monitoraggio della rete.  

  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [channel update](create_channel.html#updating-a-channel). Un membro del canale con i peer su {{site.data.keyword.blockchainfull_notm}} Platform può utilizzare il Monitoraggio della rete per aggiungere la tua organizzazione al canale anche se non ospiti alcun peer sulla piattaforma.

    Una volta che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il certificato di firma del peer al canale in modo che gli altri membri possano verificare la tua firma digitale durante le transazioni. Il peer remoto carica il proprio certificato di firma durante l'installazione, pertanto dovrai solo sincronizzare il certificato sul canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si unisce la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale.

Quando la tua organizzazione fa parte del canale, segui le istruzioni relative all'[adesione a un canale](../v10_application.html#join-channel-sdk). Devi fornire l'URL del servizio ordini e il nome del canale.

### Utilizzo dell'SDK per installare il chaincode sul peer
{: #remote-peer-install-cc-sdk}

Attieniti alla seguenti istruzioni per utilizzare l'SDK per [installare un chaincode](../v10_application.html#install-cc-sdk) sul tuo peer remoto.

### Utilizzo dell'SDK per istanziare il chaincode sul canale
{: #remote-peer-instantiate-cc-sdk}

Solo un membro del canale deve istanziare o aggiornare il chaincode. Pertanto, qualsiasi membro di rete del canale con i peer su {{site.data.keyword.blockchainfull_notm}} Platform può utilizzare il Monitoraggio della rete per istanziare il chaincode e specificare le politiche di approvazione. Tuttavia, se vuoi utilizzare il peer remoto per istanziare il chaincode su un canale, puoi usare l'SDK e seguire le istruzioni per [istanziare un chaincode](../v10_application.html#instantiate-cc-sdk).


## Utilizzo della CLI per gestire il peer remoto
{: #remote-peer-cli-operate}

Puoi gestire il tuo peer remoto anche dalla riga di comando utilizzando il client CA Fabric e il contenitore di strumenti Fabric. In queste istruzioni, per prima cosa generiamo i certificati richiesti utilizzando il client CA e quindi utilizziamo il contenitore di strumenti Fabric per gestire il peer unendolo a un canale, installando un chaincode e infine istanziando il chaincode su un canale.

### Iscrizione mediante il client CA Fabric
{: #peer-client-enroll}

Il primo passo consiste nel generare i certificati richiesti (iscrizione) utilizzando il client CA Fabric. Per prima cosa devi installare il client CA Fabric. Scarica i [file binari fabric-ca v1.1.0 per la tua piattaforma](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) nella tua macchina locale, estraili e spostali in una cartella come `$HOME/fabric-ca-remote/`.

1.  Prepara il tuo ambiente per utilizzare il client CA Fabric. Passa alla directory in cui hai spostato i file binari del client in modo da poter fare riferimento a essa direttamente nei comandi.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Imposta il percorso in cui il client può creare le tue chiavi. Se è la prima volta che esegui il comando `enroll`, la cartella `msp` e il file `.yaml` non esistono. Se hai già eseguito il comando `enroll`, è importante eliminare qualsiasi materiale di configurazione dai tentativi di registrazione precedenti utilizzando i comandi `rm` riportati di seguito:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Devi richiamare le informazioni sull'endpoint della tua CA (Certificate Authority) su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata "Panoramica" del Monitoraggio della rete, fai clic sul pulsante **Profilo connessione** e apri il file JSON che contiene le credenziali di rete. Scorri fino alla sezione `certificateAuthorities` e prendi nota delle informazioni indicate di seguito.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  Devi anche richiamare un certificato TLS da {{site.data.keyword.blockchainfull_notm}} Platform per comunicare con la CA. Le credenziali di rete sopra riportate includono il certificato necessario. Nella sezione `certificateAuthorities` del file Profilo connessione, copia il certificato che segue `"pem:"`, che inizia con `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE----- `. **Non includere le virgolette**.

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

Prima di poter operare con il peer remoto, dobbiamo eseguire una certa gestione dei certificati sulla nostra macchina locale e caricare alcuni dei certificati che il client CA Fabric ha generato su {{site.data.keyword.blockchainfull_notm}} Platform e nel tuo peer. Dobbiamo anche scaricare i certificati TLS dalla piattaforma e dal peer. Se vuoi saperne di più sui certificati con cui lavorerai e sulle attività da svolgere, vedi [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

Sulla tua macchina locale, apri un terminale di comando e passa alla directory in cui hai spostato i file binari del client CA Fabric e memorizzato la cartella MSP.
1. Copia il file `cert.pem` dalla cartella `signcerts` in una nuova cartella `admincerts`.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. Dovrai anche copiare il certificato TLS del tuo peer remoto dal contenitore peer su ICP nella tua macchina locale. 

  - [Accedi al tuo contenitore peer](#remote-peer-kubectl-configure) e immetti il seguente comando:
    ```
    cat /mnt/certs/tls/cacert.pem
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

3. Per fornire alla tua CLI l'autorizzazione per installare il chaincode sul peer, devi caricare il signCert generato dal client CA Fabric nella cartella admin dei peer e riavviare il peer. Pertanto, copia il certificato `admincert/cert.pem` dalla tua macchina locale nella directory `/mnt/crypto/peer/peer/msp/admincerts/` del contenitore peer come `cert2.pem`.

    - Sulla tua macchina locale, immetti i seguenti comandi:

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copia negli appunti il testo del file `cert.pem` che inizia con `-----BEGIN CERTIFICATE-----` e termina con `-----END CERTIFICATE-----`.

    - Torna al contenitore peer e immetti i seguenti comandi per aggiungere cert.pem ai certificati peer. Sostituisci il testo `<CERT.PEM>` con il testo che hai copiato negli appunti.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Nota:** un file cert.pem esiste già in questa directory. Non sovrascriverlo.

    - Poiché hai aggiunto un nuovo certificato, devi riavviare il contenitore affinché il peer recuperi il certificato. Segui queste [istruzioni](#remote-peer-restart) per riavviare il peer.

4. Devi caricare lo stesso certificato `admincert/cert.pem` dalla tua macchina locale su {{site.data.keyword.blockchainfull_notm}} Platform affinché una CLI o un'applicazione remota esegua operazioni di canale come il recupero del blocco genesi del canale e l'istanziazione del chaincode.
    1. Sulla tua macchina locale, taglia il file `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` e incollalo negli appunti.
    2. Accedi al Monitoraggio della rete della tua rete su {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Fai clic su **Membri** nel navigatore di sinistra e fai clic sulla scheda **Certificati**.
    4. Fai clic sul pulsante **Aggiungi certificato**.
    5. Nella finestra **Aggiungi certificato**, fornisci un nome al tuo certificato, ad esempio `fabrictools.pem`, incolla il certificato che hai appena copiato negli appunti e fai clic su **Invia**.
    6. Ti verrà chiesto se vuoi riavviare i peer. Fai clic su **Riavvia**.
    7. Fai clic su **Canali** nel navigatore di sinistra e individua il canale a cui si unirà il peer remoto.
    8. Fai clic sui tre punti sotto l'intestazione **Azioni** e fai clic su **Sincronizza certificato**. Nella finestra **Sincronizza certificato**, fai clic su **Invia**.

    **Nota**: è importante sincronizzare il certificato del canale prima che il peer si unisca al canale sottostante.

### Configurazione del contenitore di strumenti Fabric

Dopo aver spostato tutti i nostri certificati nella posizione necessaria, siamo pronti per installare e utilizzare il contenitore di strumenti Fabric da docker. Ancora una volta, dobbiamo eseguire questi comandi dalla directory in cui hai memorizzato la tua cartella MSP.

1.  Scarica l'immagine docker degli strumenti Fabric con il seguente comando:
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  Immetti i seguenti comandi per avviare il contenitore di strumenti Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    Questo comando monta la directory MSP all'interno del tuo contenitore di strumenti.

3.  L'indirizzo del peer remoto deve essere risolto in un nome dominio con il suffisso `blockchain.com`. Per farlo, puoi utilizzare il tuo DNS o modificare il file /etc/hosts. Ai fini di queste istruzioni, modificheremo il file `/etc/hosts` nel contenitore di strumenti Fabric. Accedi alla tua console ICP e trova le [informazioni sull'endpoint](#remote-peer-retrieve-endpoint-info) dei tuoi peer remoti. Una volta ottenuto l'`INGRESS_IP` dei peer, immetti il seguente comando con l'indirizzo del tuo peer remoto.
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Utilizzo del contenitore di strumenti Fabric per unire il peer remoto al canale
{: #icp-cli-join-peer-to-channel}

Prima di poter eseguire i comandi della CLI per unire il peer remoto a un canale, è necessario aggiungere la tua organizzazione a un canale nella rete. 

  - Puoi avviare un nuovo canale per il peer remoto. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](create_channel.html#creating-a-channel). Tieni presente che devi disporre di almeno un peer su {{site.data.keyword.blockchainfull_notm}} Platform prima di poter creare un canale nel Monitoraggio della rete.  

  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [channel update](create_channel.html#updating-a-channel). Un membro del canale con i peer su {{site.data.keyword.blockchainfull_notm}} Platform può utilizzare il Monitoraggio della rete per aggiungere la tua organizzazione al canale anche se non ospiti alcun peer sulla piattaforma.

    Una volta che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il certificato di firma del peer al canale in modo che gli altri membri possano verificare la tua firma digitale durante le transazioni. Il peer remoto carica il proprio certificato di firma durante l'installazione, pertanto dovrai solo sincronizzare il certificato sul canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si unisce la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale.

1. Richiama le informazioni di configurazione dal tuo file `creds.json` disponibile nel pannello **Panoramica** del Monitoraggio della rete. Fai clic su **Profilo connessione** e quindi su **Scarica**.

  - Trova l'URL ordinante ricercando gli **ordinanti**, che puoi trovare in `orderers > url`. Prendi nota dell'URL che inizia con il nome della rete. L'URL è simile al seguente esempio:
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Trova il nome della tua organizzazione ricercando le **organizzazioni**. Questa deve essere la stessa organizzazione utilizzata per registrare il tuo peer remoto. Puoi trovare il nome della tua organizzazione insieme al suo `mspid` associato. Prendi nota del valore dell'`mspid`.

2. [Trova le informazioni sull'endpoint dei peer remoti](#remote-peer-retrieve-endpoint-info), questa volta prendendo nota delle `Ports` elencate accanto a `Ingress IP`.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  Prendi nota delle porte nell'intervallo 30K. Queste sono le tue porte indirizzabili dal contenitore di strumenti Fabric, che useremo per impostare la variabile di ambiente `PEERADDR`. 

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  Pertanto, il `PEERADDR` che utilizziamo viene creato con il valore che abbiamo specificato nel file `/etc/hosts` insieme alla porta associata a `7051`, ossia `30162` nel nostro esempio. Quindi, il valore risultante di `PEERADDR`
  sarà `remotepeer.blockchain.com:30162`.

3. Immetti i seguenti comandi per impostare le variabili di ambiente nel contenitore di strumenti Fabric.

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
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. Recupera il blocco genesi del canale per creare il libro mastro del canale sul tuo peer. Per prima cosa, esegui `cd` nella tua directory root e quindi esegui il comando per recuperare il blocco genesi.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
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

5. Unisci il peer al canale utilizzando il seguente comando:

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Se eseguito correttamente, dovresti visualizzare informazioni simili al seguente esempio:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilizzo del contenitore di strumenti Fabric per installare il chaincode sul peer remoto
{: #icp-toolcontainer-install-cc}
A questo punto, siamo pronti per installare e istanziare il chaincode sul peer remoto. Per queste istruzioni, installeremo il chaincode `fabcar` dal repository `fabric-samples`. Scarica il chaincode `fabric-samples` da `github.com` utilizzando i seguenti comandi:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

Immetti il seguente comando della CLI del peer per installare il chaincode `fabcar` sul peer remoto.

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

### Utilizzo del contenitore di strumenti Fabric per istanziare il chaincode su un canale
{: #icp-toolcontainer-instantiate-cc}

Poiché un solo peer deve istanziare il chaincode su un canale, non devi utilizzare il tuo peer remoto per istanziare il chaincode. I peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform possono eseguire questa operazione. Tuttavia, se vuoi che il peer remoto istanzi il chaincode sul canale, puoi immettere il seguente comando nel contenitore di strumenti Fabric:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

Se questo comando viene completato correttamente, dovresti visualizzare qualcosa di simile a:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Visualizzazione dei log del peer remoto in {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-log-icp}

Puoi accedere ai log del peer remoto dalla console ICP e controllare i log nell'interfaccia Kibana.

1. Nella console ICP, fai clic sull'icona **Menu** nell'angolo superiore sinistro.
2. Dall'elenco di menu, fai clic su **Carichi di lavoro** > **Release Helm**.
3. Nella tabella Release Helm, fai clic sul nome della tua release Helm.
4. Scorri fino alla sezione **Pod** e fai clic sul link **Visualizza log** alla fine della riga della tabella. I log del peer remoto vengono aperti nell'interfaccia Kibana.

## Aggiornamento del chaincode

Nel corso del tempo è probabile che tu debba modificare il chaincode in esecuzione sul peer remoto. Poiché il chaincode è installato sui peer e istanziato sul canale, devi aggiornare il chaincode su tutti i peer presenti sul canale.

Completa la seguente procedura per aggiornare il tuo chaincode:

1. Per aggiornare il chaincode su ogni peer remoto, è sufficiente rieseguire il processo che hai utilizzato per installare il chaincode sui peer, utilizzando un'applicazione client o un comando della CLI. Assicurati di specificare lo stesso nome chaincode utilizzato originariamente. Tuttavia, questa volta incrementa la `Version` del chaincode. Tutti i peer devono utilizzare lo stesso nome e la stessa versione del chaincode.

2. Dopo aver installato il nuovo chaincode su tutti i peer nel canale, utilizza il Monitoraggio della rete o il comando
[peer chaincode upgrade ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) per aggiornare il canale per utilizzare il nuovo chaincode.

Consulta il passo due di queste [istruzioni](install_instantiate_chaincode.html#updating-a-chaincode) per ulteriori informazioni sull'utilizzo del pannello "Installa il codice" del Monitoraggio della rete per aggiornare il chaincode sul canale.

## Risoluzione dei problemi
{: #icp-troubleshooting}

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

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

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

5. Check helm version:

  ```
  helm version --tls
  ```

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
che non riesce e produce un messaggio di errore simile al seguente esempio:
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Soluzione:**  

Questo errore si verifica quando sui sistemi big endian, come IBM System Z, vengono aperte due sessioni bash docker nello stesso contenitore docker. La seconda sessione bash potrebbe non essere in grado di connettersi al contenitore in esecuzione. Per risolvere questo problema, [riavvia il contenitore peer](#remote-peer-restart) e ritenta il comando `kubectl exec` dopo il riavvio del peer.

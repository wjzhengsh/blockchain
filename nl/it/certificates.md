---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform
{: #certificates}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform è basato su Hyperledger Fabric e crea reti blockchain autorizzate. I partecipanti sono noti come membri della rete e le loro attività e il loro accesso alle risorse di rete vengono verificati continuamente. L'identità di un partecipante è fornita sotto forma di un certificato digitale x509 attendibile. La verifica viene fornita da un'infrastruttura a chiave pubblica sottostante e da operazioni di firma/verifica che proteggono le transazioni e la gestione all'interno della rete.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform gestisce la maggior parte delle operazioni di certificazione senza che gli utenti debbano gestire i propri certificati. Tuttavia, ci sono delle volte in cui dovrai gestire i certificati che ti consentono di comunicare con la rete, ad esempio lo sviluppo di applicazioni o l'espansione della tua rete con un peer remoto. Di seguito è riportata una breve guida alla tua autorità di certificazione e all'infrastruttura di certificazione sottostante. Questa esercitazione  può aiutarti a comprendere i certificati con cui lavorerai e le attività che devi eseguire.

## Autorità di certificazione (CA, Certificate Authority)
{: #network-ca}

Le Autorità di certificazione (CA, Certificate Authority) forniscono identità sulla rete. Una CA può essere considerata come un notaio pubblicamente affidabile che funge da ancora di fiducia tra più parti. A tutte le entità della rete viene assegnato un certificato firmato da una CA root che incapsula la loro identità digitale. Questo certificato è la radice di attendibilità per tutte le operazioni di firma e di verifica eseguite sulla rete. Per ulteriori dettagli su come vengono utilizzate le autorità di certificazione per stabilire l'identità, consulta la [documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html).

Ogni membro della rete possiede la propria CA, che riceve un certificato dalla CA root della rete. Questo legame con la CA root stabilisce l'ancora di fiducia per la tua organizzazione. La CA della tua organizzazione firma le richieste per tutte le entità di proprietà dell'organizzazione, ad esempio l'amministratore, i peer o le applicazioni. Se vuoi aggiungere un peer remoto o una nuova applicazione alla tua rete, devi registrare la nuova identità con la tua CA (registrazione). Quindi, la CA può fornire alla nuova entità i certificati di cui avrà bisogno per interagire con la rete (iscrizione).

### Registrazione mediante il Monitoraggio della rete
{: #ca-panel}

Puoi utilizzare il Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform per visualizzare le identità registrate con la tua CA e per aggiungerne di nuove. Vai al pannello "CA (Certificate Authority)" del Monitoraggio della rete. Questo pannello visualizza tutte le identità che sono state registrate con la tua CA, tra cui l'amministratore dell'organizzazione, i peer e le applicazioni client. Per registrare una nuova identità nella tua organizzazione, fai clic sul pulsante **Aggiungi utente** nella parte superiore del pannello. Si apre una finestra a comparsa che visualizza i seguenti campi che sono necessari per registrare una nuova identità.
  - **ID:** questo sarà il nome della tua nuova identità, a volte indicato come `enroll ID`. **Salva questo valore** per quando configuri un peer remoto o registri una nuova applicazione.
  - **Segreto:** questa sarà la password per la tua identità, a volte indicata come `enroll Secret`.  **Salva questo valore** per quando configuri un peer remoto o registri una nuova applicazione.  
  - **Tipo:** seleziona il tipo di identità che vuoi registrare, peer o applicazione client. 
  - **Affiliazione:** questa sarà l'affiliazione all'interno della tua organizzazione, ad esempio `org1`, a cui appartiene l'identità.
  - **Registrazioni massime:** utilizza questo campo per limitare il numero di volte in cui puoi registrare o generare certificati utilizzando questa identità. Se lasci il campo vuoto, il valore predefinito è un numero illimitato di registrazioni.

Puoi utilizzare questo pannello per registrare una nuova identità peer se stai distribuendo un [peer remoto](howto/remote_peer.html). In alternativa, puoi registrare un client se stai sviluppando un'applicazione che può inviare transazioni alla tua rete. Consulta l'[esercitazione sullo sviluppo di applicazioni](v10_application.html) per ulteriori informazioni sull'utilizzo degli SDK Fabric con la piattaforma.

### Generazione di certificati lato client (registrazione)
{: #enrollment}
Prima di connettere un client di terze parti a {{site.data.keyword.blockchainfull_notm}} Platform, dovrai essere autenticato. Il processo di generazione dei certificati necessari, della tua chiave privata e del tuo certificato pubblico (noto anche come certificato di registrazione o signCert) viene denominato iscrizione. Questi certificati saranno necessari ogni volta che il tuo client comunica con la rete. Qualsiasi client che invia chiamate alla rete dovrà firmare i payload utilizzando una chiave privata e allegare un certificato x509 correttamente firmato.

Consulta l'[esercitazione sullo sviluppo di applicazioni](v10_application.html) per informazioni su come effettuare l'[iscrizione utilizzando l'SDK Node Fabric](v10_application.html). L'iscrizione con l'SDK genera un signCert, una chiave pubblica che è stata utilizzata per creare il signCert e la tua chiave privata.

Puoi anche generare i certificati dalla [riga di comando](#enroll-register-caclient) utilizzando il client CA Fabric. Il client CA Fabric restituisce una serie più completa di certificati all'interno di una cartella MSP (Membership Service Provider). Questa cartella contiene il certificato root firmato dalla CA, i certificati intermedi collegati alla tua affiliazione, una chiave privata e il tuo signCert. Per ulteriori informazioni su MSP e sui contenuti di questa cartella, vedi la sezione [Membership Service Provider](#msp) più avanti.

### Caricamento dei certificati di firma in {{site.data.keyword.blockchainfull_notm}} Platform
{: #upload-certs}

Un'applicazione richiede solo un signCert valido per inviare le transazioni alla rete. Tuttavia, se un client vuole gestire la rete, ad esempio installando il chaincode sui peer o unendo i peer ai canali, è necessario che il client venga riconosciuto come amministratore. Ogni componente riconosce un insieme di certificati signCert di proprietà di un amministratore. Se vuoi gestire la tua rete da un client, dovrai caricare il tuo signCert e aggiungerlo all'elenco di certificati amministratore. Puoi eseguire questa operazione sulla piattaforma caricando il signCert nella scheda **Certificati** del [pannello "Panoramica"](v10_dashboard.html#members) del tuo Monitoraggio della rete. Sincronizza questo certificato con i tuoi peer premendo il pulsante di riavvio che viene visualizzato dopo il caricamento. Successivamente, il tuo client può essere riconosciuto come amministratore della rete.

<!----
When you add an organization to a channel, this will upload the organization signCert to the channel MSP. Synching the Sign Certs does something.
--->

## Utilizzo dei certificati TLS
{: #tls}

[Transport Layer Security ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) è integrato nel modello di attendibilità di Hyperledger Fabric. Tutti i componenti in {{site.data.keyword.blockchainfull_notm}} Platform utilizzano TLS per autenticarsi e comunicare tra loro. Pertanto, devi allegare un certificato TLS emesso dalla piattaforma alle tue chiamate al fine di convalidare e crittografare la comunicazione. Gli altri certificati discussi in questa esercitazione proteggono la tua capacità di effettuare transazioni e gestire la rete. I certificati TLS vengono utilizzati per proteggere le chiamate alla rete.

I certificati TLS vengono emessi pubblicamente dalla piattaforma e sono gli stessi per tutti i componenti della rete. Puoi scaricare i certificati TLS dai seguenti link, a seconda del piano di adesione e dell'ubicazione del cloud. Puoi anche trovare i certificati TLS nel [profilo delle credenziali](v10_dashboard.html#enterprise-connection-profile "profilo delle credenziali"). Questo certificato può risiedere ovunque, a condizione che tu possa farvi riferimento dalla tua applicazione o riga di comando.

- Certificato TLS root per piano Starter  
  - Stati Uniti: [us01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - Regno Unito: [uk01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sydney: [aus01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
- [Certificato TL root per piano Enterprise ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

Tutte le reti {{site.data.keyword.blockchainfull_notm}} Platform utilizzano il TLS lato server, in cui la rete deve autenticare i tuoi client. Le reti piano Enterprise possono anche abilitare il TLS reciproco, in cui il client e il server si autenticano a vicenda, per proteggere ulteriormente le applicazioni. I certificati TLS lato client (per il TLS reciproco) sono emessi dalla CA del client e sono univoci per la tua rete. Se utilizzi una rete piano Enterprise, si consiglia di abilitare il TLS reciproco. Per ulteriori informazioni sul TLS reciproco, consulta queste [istruzioni](v10_dashboard.html#mutual-tls "istruzioni sul TLS reciproco").

## Membership Service Provider (MSP)
{: #msp}

I componenti di {{site.data.keyword.blockchainfull_notm}} Platform utilizzano le identità tramite gli MSP (Membership Service Provider). Gli MSP associano i certificati emessi dalle CA ai ruoli di rete e di canale. Per ulteriori informazioni sugli MSP, vedi l'[argomento sul concetto di adesione nella documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "argomento sul concetto di adesione nella documentazione di Hyperledger Fabric").

Le cartelle MSP all'interno di Fabric hanno una struttura definita. Quando effettui l'iscrizione utilizzando il client CA Fabric, il client memorizza i certificati in una cartella MSP sul tuo file system locale con le seguenti sottocartelle:

- **cacerts:** questa cartella contiene il certificato root della CA root della tua rete.
- **intermediatecerts:** questi sono i certificati delle CA intermedie della tua rete. Queste CA intermedie sono collegate alla CA root e formano una catena di attendibilità. Ogni organizzazione del piano Enterprise ha due CA intermedie per il failover e l'alta disponibilità.
- **signcerts:** questa cartella contiene il tuo certificato di firma pubblico, chiamato anche signCert o certificato di registrazione. Questo certificato viene allegato alle chiamate in rete (ad esempio, un richiamo del chaincode) quando fai riferimento alla tua directory MSP dalla riga di comando o crei un oggetto di contesto utente con gli SDK. Puoi caricare questo certificato sulla piattaforma se vuoi gestire una rete dall'SDK o dalla riga di comando.
- **keystore:** questa cartella contiene la tua chiave privata. Questa chiave viene utilizzata per firmare le tue chiamate alla rete quando fai riferimento alla tua directory MSP dalla riga di comando o crei un oggetto di contesto utente con gli SDK. Tieni questa chiave al sicuro per proteggere la tua rete e i tuoi dati.

Puoi anche creare una cartella MSP a cui il client CA Fabric può fare riferimento utilizzando le API di Monitoraggio della rete e Swagger.

- **cacerts** e **intermediatecerts**: puoi recuperare questi certificati con le [API Swagger](howto/swagger_apis.html) inviando una richiesta Get all'API MSP.
- **signcerts** e **keystore**: puoi generare questi certificati facendo clic sul pulsante **Genera certificati** nel pannello "CA (Certificate Authority)". Si apre una finestra a comparsa con due certificati elencati. Copia e memorizza il **Certificato** in signcerts e la **Chiave privata** nel keystore. Conserva questi certificati in un luogo sicuro perché non vengono memorizzati sulla piattaforma.

Molti componenti Fabric contengono informazioni aggiuntive all'interno della propria cartella MSP. Ad esempio, se gestisci un peer remoto, potresti vedere le seguenti cartelle:

- **admincerts:** questa cartella contiene l'elenco di amministratori per questa organizzazione o questo componente. Dovrai caricare il tuo certificato di firma in questa cartella se stai gestendo un peer remoto dalla riga di comando o dagli SDK. 
- **tls:** questa è una cartella in cui memorizzi i certificati TLS utilizzati per la comunicazione con altri componenti di rete.

Per ulteriori informazioni sulla struttura dei MSP, vedi [Membership ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Membership") e [Membership Service Providers ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") nella documentazione di Hyperledger Fabric.

## Iscrizione e registrazione mediante il client CA Fabric.
{: #enroll-register-caclient}

Puoi anche utilizzare il client CA Fabric per generare certificati e registrare una nuova identità con l'Autorità di certificazione. Le istruzioni riportate di seguito generano certificati utilizzando la tua identità di amministratore, quindi utilizzano tali certificati per registrare un nuovo client. L'iscrizione mediante il client CA Fabric genererà i certificati descritti nella [sezione Membership Services Provider](#msp).

### Iscrizione utilizzando il client CA Fabric
{: #enroll-app-caclient}

1. Scarica i [file binari fabric-ca](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) nella tua macchina locale, estraili e spostali in una cartella come `$HOME/fabric-ca-platform/`. Passa alla directory in cui hai spostato i file binari del client in modo da poter fare riferimento a essa direttamente nei comandi.
    ```
    cd $HOME/fabric-ca-platform/
    ```

2.  Imposta il percorso in cui il client può creare le tue chiavi. Assicurati di rimuovere il materiale di configurazione che è stato creato nello scorso tentativo. Se non hai già eseguito il comando `enroll`, la cartella `msp` e il file `.yaml` non esistono.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```

3. Scarica i certificati TLS da {{site.data.keyword.cloud_notm}} a seconda del piano di servizi, dell'ubicazione e del cluster che utilizzi.
  - Certificato TLS root per piano Starter  
    - Stati Uniti: [us01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Regno Unito: [uk01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Certificato TL root per piano Enterprise ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Salva il contenuto in una cartella, ad esempio ``$HOME/tls``. Questo passo consente di crittografare il flusso di dati in transito.

4. Apri il file JSON del **Profilo connessione** dal pannello "Panoramica" nel Monitoraggio della rete e trova le seguenti variabili:
  * URL per CA: `url` in `certificateAuthorities`
  * ID utente amministratore: ``enrollId``
  * Password amministratore: ``enrollSecret``
  * Nome CA: ``caName``

5. Puoi utilizzare il client CA Fabric per inviare una chiamata `enroll` all'Autorità di certificazione passando il percorso certificati TLS e le quattro stringhe riportate sopra con il seguente comando:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Devi eseguire questo comando nella directory in cui sposti il client CA Fabric. Una vera chiamata potrebbe essere simile al seguente comando di esempio:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

6. Trova il tuo certificato amministratore in `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. Puoi quindi caricare il certificato amministratore nella tua rete blockchain dal Monitoraggio della rete. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificati" del pannello "Membro"](v10_dashboard.html#members) nel Monitoraggio della rete.

  Puoi anche trovare il certificato root CA e la chiave privata amministratore nelle seguenti directory:
  * Certificato root CA: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * Chiave privata amministratore: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Per vedere un esempio di dove ti iscrivi utilizzando il client CA Fabric e utilizzi i certificati generati per gestire un componente di rete, vedi le istruzioni in [Gestione di un peer remoto](howto/remote_peer_operate_icp.html#remote-peer-cli-operate).

### Registrazione utilizzando il client CA Fabric
{: #register-app-caclient}

1. Immetti il seguente comando per trovare il nome della tua affiliazione e della tua organizzazione nella rete blockchain.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  Dovresti visualizzare informazioni simili al seguente esempio:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  Prendi nota del secondo valore **affiliation**, ad esempio `ibp.PeerOrg1`. Dovrai utilizzare questo valore nel comando riportato di seguito.

2. Immetti il seguente comando per registrare il peer.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  Specifica un nome e una password per il tuo peer e sostituisci `name` e `password` con il nome e la password del tuo peer. Prendi nota di queste informazioni che ti serviranno quando configuri il tuo peer. Ad esempio:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  Puoi registrare un'entità solo una volta. Se riscontri un problema, prova un comando con un nuovo nome utente e password.

  Se il comando viene completato correttamente, dovresti visualizzare informazioni simili al seguente esempio:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

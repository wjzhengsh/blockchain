---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: TLS, TLS certificates, client applications, digital certificates, certificate authority, intermediate certificate, client-side certificate, generate certificates, manage certificates

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform
{: #managing-certificates}

{{site.data.keyword.blockchainfull}} Platform è basato su Hyperledger Fabric e crea reti blockchain autorizzate. I partecipanti sono noti come membri della rete e le loro attività e il loro accesso alle risorse di rete vengono verificati continuamente. L'identità di un partecipante è fornita sotto forma di un certificato digitale x509 attendibile. La verifica viene fornita da un'infrastruttura a chiave pubblica sottostante e da operazioni di firma/verifica che proteggono le transazioni e la gestione all'interno della rete.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform gestisce la maggior parte delle operazioni di certificazione senza che gli utenti debbano gestire i propri certificati. Tuttavia, ci sono delle volte in cui dovrai gestire i certificati che ti consentono di comunicare con la rete, ad esempio lo sviluppo di applicazioni o l'espansione della tua rete con un peer remoto. Di seguito è riportata una breve guida alla tua autorità di certificazione e all'infrastruttura di certificazione sottostante. Questa esercitazione può aiutarti a comprendere i certificati con cui lavorerai e le attività che devi eseguire.

## Autorità di certificazione (CA, Certificate Authority)
{: #managing-certificates-network-ca}

Le Autorità di certificazione (CA, Certificate Authority) forniscono identità sulla rete. Una CA può essere considerata come un notaio pubblicamente affidabile che funge da ancora di fiducia tra più parti. A tutte le entità della rete viene assegnato un certificato firmato da una CA root che incapsula la loro identità digitale. Questo certificato è la radice di attendibilità per tutte le operazioni di firma e di verifica eseguite sulla rete. Per ulteriori dettagli su come vengono utilizzate le autorità di certificazione per stabilire l'identità, consulta la [documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/identity/identity.html).

Ogni membro nella rete possiede la propria CA. La CA della tua organizzazione firma le richieste per tutte le entità e i componenti che gestisci, ad esempio l'amministratore, i peer o le applicazioni. Se vuoi aggiungere un peer remoto o una nuova applicazione alla tua rete, devi registrare la nuova identità con la tua CA (registrazione). Quindi, la CA può fornire alla nuova entità i certificati di cui ha bisogno per interagire con la rete (iscrizione).

### Registrazione con il Monitoraggio della rete
{: #managing-certificates-ca-panel}

Puoi utilizzare il Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform per visualizzare le identità registrate con la tua CA e per aggiungerne di nuove. Vai al pannello "CA (Certificate Authority)" del Monitoraggio della rete. Questo pannello visualizza tutte le identità che sono state registrate con la tua CA, tra cui l'amministratore dell'organizzazione, i peer e le applicazioni client. Per registrare una nuova identità nella tua organizzazione, fai clic sul pulsante **Aggiungi utente** nel pannello. Si apre una finestra a comparsa che visualizza i seguenti campi che sono necessari per registrare una nuova identità.
  - **ID di iscrizione:** questo sarà il nome della tua nuova identità, a volte indicato come `enroll ID`. **Salva questo valore** per quando configuri un peer remoto o ti iscrivi a una nuova applicazione.
  - **Segreto di iscrizione:** questa sarà la password per la tua identità, a volte indicata come `enroll Secret`. **Salva questo valore** per quando configuri un peer remoto o ti iscrivi a una nuova applicazione.
  - **Tipo:** seleziona il tipo di identità che vuoi registrare, peer o applicazione client.
  - **Affiliazione:** questa sarà l'affiliazione all'interno della tua organizzazione, ad esempio `org1`, a cui appartiene l'identità.
  - **Numero massimo di iscrizioni:** puoi utilizzare questo campo per limitare il numero di volte in cui puoi iscrivere o generare certificati utilizzando questa identità. Se lasci il campo vuoto, il valore predefinito è un numero illimitato di registrazioni.

Puoi utilizzare questo pannello per registrare una nuova identità peer se stai distribuendo un [peer remoto](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about). In alternativa, puoi registrare un client se stai sviluppando un'applicazione che può inviare transazioni alla tua rete. Consulta l'[esercitazione sullo sviluppo di applicazioni](/docs/services/blockchain/v10_application.html#dev-app) per ulteriori informazioni sull'utilizzo degli SDK Fabric con la piattaforma.

### Generazione di certificati lato client (iscrizione)
{: #managing-certificates-enrollment}
Prima di poter connettere un client di terze parti a {{site.data.keyword.blockchainfull_notm}} Platform, devi essere autenticato. Il processo di generazione dei certificati necessari, della tua chiave privata e del tuo certificato (noto anche come certificato di iscrizione o signCert) viene denominato iscrizione. Questi certificati saranno necessari ogni volta che il tuo client comunica con la rete. Qualsiasi client che invia chiamate alla rete deve firmare i payload utilizzando una chiave privata e allegare un certificato x509 correttamente firmato.

Consulta l'[esercitazione sullo sviluppo di applicazioni](/docs/services/blockchain/v10_application.html#dev-app) per informazioni su come effettuare l'[iscrizione utilizzando l'SDK Node Fabric](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk). L'iscrizione con l'SDK genera 3 elementi separati: una chiave privata, signCert e una chiave pubblica che è stata utilizzata per creare il signCert.

Puoi anche generare i certificati dalla riga di comando utilizzando il [client CA Fabric](/docs/services/blockchain/certificates.html#managing-certificates-enroll-register-caclient). Il client CA Fabric restituisce una serie più completa di certificati all'interno di una cartella MSP (Membership Service Provider). Questa cartella contiene il certificato root firmato dalla CA, i certificati intermedi, una chiave privata e il tuo signCert. Per ulteriori informazioni su MSP e su cosa la cartella MSP contiene, consulta [MSP (Membership Service Provider)](/docs/services/blockchain/certificates.html#managing-certificates-msp).

Puoi generare i certificati con solo le identità che sono state registrate con la tua CA (Certificate Authority), utilizzando il segreto e il nome di tale identità. Per impostazione predefinita, è già stata registrata un'identità di **amministratore** con la tua CA e viene elencata nella schermata "CA (Certificate Authority)". Puoi trovare il segreto dell'identità di amministratore nel tuo profilo di connessione facendo clic sul pulsante **Profilo connessione** nella schermata "Panoramica" del tuo Monitoraggio della rete. Puoi anche registrare una nuova identità facendo clic sul pulsante [Aggiungi utente](/docs/services/blockchain/certificates.html#managing-certificates-ca-panel) nella schermata "CA (Certificate Authority)" del Monitoraggio della rete e poi generare i certificati con il nome e il segreto della nuova identità.

**Nota:** se segui le istruzioni per generare i certificati utilizzando l'SDK Node di Fabric o il precedente client CA Fabric, inizia l'iscrizione utilizzando l'identità di amministratore. Quindi utilizza tali certificati per registrare una nuova identità client con la tua CA. Se utilizzi le istruzioni dell'SDK nello [Sviluppo di applicazioni](/docs/services/blockchain/v10_application.html#dev-app), dovrai nuovamente iscriverti utilizzando l'identità client. Puoi quindi utilizzare questi certificati per inviare le transazioni alla rete. <!---You can an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

### Generazione dei certificati utilizzando il Monitoraggio della rete
{: #managing-certificates-certs-panel}

Puoi utilizzare il Monitoraggio della rete per generare i certificati utilizzando l'identità di amministratore e poi passarli direttamente all'SDK. Fai clic sul pulsante **Genera certificato** accanto alla tua identità amministratore per ottenere un nuovo signCert e una nuova chiave privata dalla tua CA. Il campo **Certificato** contiene il signCert, appena sopra **Chiave privata**. Puoi fare clic sull'icona di copia alla fine di ogni campo per copiare il valore. Devi poi salvare questi certificati in un posto da cui puoi importarli nella tua applicazione. Per ulteriori informazioni, vedi l'[esercitazione sullo sviluppo di applicazioni](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel). **Nota** che {{site.data.keyword.blockchainfull_notm}} Platform non memorizza questi certificati. Devi salvarli e memorizzarli in modo sicuro.

### Caricamento dei certificati di firma in {{site.data.keyword.blockchainfull_notm}} Platform
{: #managing-certificates-upload-certs}

Un'applicazione richiede solo un signCert valido per inviare le transazioni alla rete. Tuttavia, se un client vuole gestire la rete, ad esempio installando il chaincode sui peer o unendo i peer ai canali, è necessario che il client venga riconosciuto come amministratore. Ogni componente riconosce un insieme di signCert di proprietà di un amministratore. Se vuoi gestire la tua rete da un client, devi caricare il tuo signCert e aggiungerlo all'elenco dei certificati di amministrazione. Puoi eseguire questa operazione sulla piattaforma caricando il signCert nella scheda **Certificati** del [pannello "Panoramica"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) del tuo Monitoraggio della rete. Sincronizza questo certificato con i tuoi peer premendo il pulsante di riavvio che viene visualizzato dopo il caricamento. Successivamente, il tuo client sarà in grado di gestire la rete. Puoi anche caricare il tuo signCert utilizzando l'[API Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) per aggiungere un certificato di amministrazione.

I canali inoltre riconoscono un insieme di certificati di amministrazione dalle identità che possono utilizzare il canale, inclusa la possibilità di istanziare un chaincode sul canale. Se utilizzi un nuovo signCert con un client remoto, devi sincronizzare il certificato sul canale prima di poter istanziare un chaincode. Attieniti alla seguente procedura nel Monitoraggio della rete per aggiungere il certificato al canale:

1. Passa alla scheda **Certificati** della schermata "Membri". Fai clic sul pulsante **Aggiungi certificato** per caricare il signCert sulla piattaforma.
2. Passa alla schermata "Canali" e trova il nome del canale pertinente.
3. Fai clic su **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azioni** per aggiungere il nuovo certificato all'elenco dei certificati di amministrazione del canale.

**Nota:** i certificati generati utilizzando l'identità amministratore della tua organizzazione non vengono automaticamente considerati certificati di amministrazione. Un signCert e una chiave privata generati utilizzando il pulsante **Genera certificati** non consentiranno al tuo SDK di utilizzare la rete. Sono stati generati dal client CA Fabric, dall'SDK o dal Monitoraggio della rete e non hanno connessione all'elenco preesistente di certificati di amministrazione riconosciuti dai componenti della rete. Devi caricare il signCert nel Monitoraggio della rete prima che il tuo SDK possa utilizzare la rete.

### Scadenza dei certificati
{: #managing-certificates-expiration}

I certificati generati dalle CA su {{site.data.keyword.blockchainfull_notm}} Platform scadono dopo uno o tre anni. Il periodo di scadenza è lo stesso per i certificati generati utilizzando gli SDK Fabric, il client CA Fabric o il [Monitoraggio della rete](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel). Se i certificati scadono, le tue applicazioni non possono più interagire con la rete. Devi eseguire nuovamente l'iscrizione per generare nuovi certificati. Se hai raggiunto il limite di iscrizioni di un utente, puoi registrare un nuovo utente e quindi effettuare l'iscrizione. Se per gestire la tua rete hai utilizzato i vecchi certificati, devi inoltre caricare i nuovi certificati sulla piattaforma.

Puoi utilizzare la tua riga di comando per controllare la data di scadenza dei certificati. Immetti il seguente comando per visualizzare i tuoi certificati in un formato leggibile:
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

Il tuo certificato client sarà simile al seguente esempio:

```
Certificate:
    Data:
        Version: 3 (0x2)
    Serial Number:
        20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18
Signature Algorithm: ecdsa-with-SHA256
    Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA
    Validity
        Not Before: Nov 28 19:18:00 2018 GMT
        Not After : Nov 28 19:23:00 2019 GMT
    Subject: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin
    ...
    ...
```

Puoi trovare la data di scadenza nella sezione **Validity** dopo `Not After:` In questo esempio, il certificato scadrà il 28 novembre 2019.

## Utilizzo dei certificati TLS
{: #managing-certificates-tls}

[Transport Layer Security ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) è integrato nel modello di attendibilità di Hyperledger Fabric. Tutti i componenti in {{site.data.keyword.blockchainfull_notm}} Platform utilizzano TLS per autenticarsi e comunicare tra loro. Pertanto, devi allegare un certificato TLS emesso dalla piattaforma alle tue chiamate al fine di convalidare e crittografare la comunicazione. Gli altri certificati discussi in questa esercitazione proteggono la tua capacità di effettuare transazioni e gestire la rete. I certificati TLS vengono utilizzati per proteggere le chiamate alla rete.

I certificati TLS vengono emessi pubblicamente dalla piattaforma e sono gli stessi per tutti i componenti della rete. Puoi scaricare i certificati TLS dai seguenti link, a seconda del piano di adesione e dell'ubicazione del cloud. Puoi anche trovare i certificati TLS nel [profilo delle credenziali](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-connection-profile). Questo certificato può risiedere ovunque, a condizione che tu possa farvi riferimento dalla tua applicazione o riga di comando.

- Certificato TLS per piano Starter
  - US: [us01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
  [us03.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
  [us05.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
  [us07.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
  - Regno Unito: [uk01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
  [uk03.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
  - Sydney: [aus01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")
- [TLS Cert for Enterprise Plan ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

Tutte le reti {{site.data.keyword.blockchainfull_notm}} Platform utilizzano il TLS lato server, in cui la rete deve autenticare i tuoi client. Le reti piano Enterprise possono anche abilitare il TLS reciproco, in cui il client e il server si autenticano a vicenda, per proteggere ulteriormente le applicazioni. I certificati TLS lato client (per il TLS reciproco) sono emessi dalla CA del client e sono univoci per la tua rete. Se utilizzi una rete piano Enterprise, si consiglia di abilitare il TLS reciproco. Per ulteriori informazioni sul TLS reciproco, consulta queste [istruzioni sul TLS reciproco](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-mutual-tls).

### Richiamo del nome del dominio dai certificati TLS.
{: #managing-certificates-retrieve-domain}

Quando esegui un collegamento a un componente Hyperledger Fabric ubicato all'esterno di {{site.data.keyword.blockchainfull_notm}} Platform, la tua chiamata deve essere inviata utilizzando il nome del dominio corretto. Se viene inviata una chiamata a un indirizzo IP del componente senza fornire il nome del dominio del componente, la chiamata sarà rifiutata e sarà restituito un errore.

Puoi trovare il nome del dominio e l'URL completo dei componenti ospitati sulla piattaforma nel tuo profilo di connessione. Il seguente esempio mostra l'URL completo di un peer, che è concatenato con il nome del dominio del peer, `us01.blockchain.ibm.com`.
```
grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us01.blockchain.ibm.com:31002"
```

Puoi anche trovare un nome del dominio del componente dal relativo certificato TLS.

1. Scarica uno dei certificati TLS root dal precedente elenco e salvalo nella tua macchina locale.
2. Nella stessa directory dei certificati TLS root, immetti il seguente comando. Questo comando può visualizzare il certificato in un formato leggibile nella tua riga di comando. Dopodiché puoi trovare le informazioni importanti, come ad esempio il nome del dominio, nella riga di comando.
  ```
  openssl x509 -in <certificate file> -text
  ```
  {:codeblock}

Ad esempio, se scarichi il primo certificato elencato e immetti il seguente comando, `openssl x509 -in us01.blockchain.ibm.com.cert -text`, puoi visualizzare il seguente codice come parte dell'output.

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            05:5c:42:fb:90:b9:cb:3d:60:7c:e4:ec:7f:7f:8e:38
    Signature Algorithm: ecdsa-with-SHA256
        Issuer: C=US, O=DigiCert Inc, CN=DigiCert ECC Secure Server CA
        Validity
            Not Before: Jun 11 00:00:00 2018 GMT
            Not After : Jun 19 12:00:00 2019 GMT
        Subject: C=US, ST=New York, L=Armonk, O=International Business Machines Corporation, CN=*.us01.blockchain.ibm.com
    ...
    ...
```

Il nome del dominio viene elencato nella riga dell'oggetto come `CN=*.us01.blockchain.ibm.com`. Puoi anche trovare dei nomi del dominio alternativi elencati più in basso nel certificato.

Il richiamo del nome del dominio di un componente dai certificati TLS può essere utile se vuoi comunicare con un peer remoto o un componente Fabric ospitato all'esterno di {{site.data.keyword.blockchainfull_notm}} Platform. Puoi quindi aggiungere il nome del dominio a una sovrascrittura SSL quando utilizzi gli SDK o aggiungere il nome del dominio e l'indirizzo IP corrispondente al tuo file `etc.hosts`.

## Membership Service Provider (MSP)
{: #managing-certificates-msp}

I componenti di {{site.data.keyword.blockchainfull_notm}} Platform utilizzano le identità tramite gli MSP (Membership Service Provider). Gli MSP associano i certificati emessi dalle CA ai ruoli di rete e di canale. Per ulteriori informazioni sugli MSP, vedi l'[argomento sul concetto di adesione nella documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html "argomento sul concetto di adesione nella documentazione di Hyperledger Fabric").

Le cartelle MSP all'interno di Fabric hanno una struttura definita. Quando effettui l'iscrizione utilizzando il client CA Fabric, il client memorizza i certificati in una cartella MSP sul tuo file system locale con le seguenti sottocartelle:

- **cacerts:** questa cartella contiene il certificato root della CA root della tua rete.
- **intermediatecerts:** questi sono i certificati delle CA intermedie della tua rete. Queste CA intermedie sono collegate alla CA root e formano una catena di attendibilità. Ogni organizzazione del piano Enterprise ha due CA intermedie per il failover e l'alta disponibilità.
- **signcerts:** questa cartella contiene il tuo certificato di firma, chiamato anche signCert o certificato di iscrizione. Questo certificato viene allegato alle chiamate in rete (ad esempio, un richiamo del chaincode) quando fai riferimento alla tua directory MSP dalla riga di comando o crei un oggetto di contesto utente con gli SDK. Puoi caricare questo certificato sulla piattaforma se vuoi gestire una rete dall'SDK o dalla riga di comando.
- **keystore:** questa cartella contiene la tua chiave privata. Questa chiave viene utilizzata per firmare le tue chiamate alla rete quando fai riferimento alla tua directory MSP dalla riga di comando o crei un oggetto di contesto utente con gli SDK. Tieni questa chiave al sicuro per proteggere la tua rete e i tuoi dati.

Puoi anche creare una cartella MSP a cui il client CA Fabric può fare riferimento utilizzando le API Swagger e il Monitoraggio della rete.

- **cacerts** e **intermediatecerts**: puoi recuperare questi certificati con le [API Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) inviando una richiesta Get all'API MSP.
- **signcerts** e **keystore**: puoi generare questi certificati facendo clic sul pulsante **Genera certificati** nel pannello "CA (Certificate Authority)". Si apre una finestra a comparsa con due certificati elencati. Copia e memorizza il **Certificato** in signcerts e la **Chiave privata** nel keystore. Conserva questi certificati in un luogo sicuro perché non vengono memorizzati sulla piattaforma.

Molti componenti Fabric contengono informazioni aggiuntive all'interno della propria cartella MSP. Ad esempio, se gestisci un peer remoto, potresti vedere le seguenti cartelle:

- **admincerts:** questa cartella contiene l'elenco di amministratori per questa organizzazione o questo componente. Devi caricare il tuo signCert in questa cartella se stai gestendo un peer remoto dalla riga di comando o dagli SDK. Se utilizzi il client CA Fabric, hai bisogno anche di una cartella admincerts nella tua MSP che contiene i signCert corrispondenti che devono essere riconosciuti come certificati di amministrazione.
- **tls:** questa è una cartella in cui memorizzi i certificati TLS utilizzati per la comunicazione con altri componenti di rete.

Per ulteriori informazioni sulla struttura dei MSP, vedi [Membership ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html "Membership") e [Membership Service Providers ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/msp.html "Membership Service Providers") nella documentazione di Hyperledger Fabric.

## Iscrizione e registrazione mediante il client CA Fabric
{: #managing-certificates-enroll-register-caclient}

Puoi anche utilizzare il client CA Fabric per generare certificati e registrare una nuova identità con la CA (Certificate Authority). Le istruzioni riportate di seguito generano certificati utilizzando la tua identità di amministratore, quindi utilizzano tali certificati per registrare un nuovo client. Per ulteriori informazioni sull'iscrizione utilizzando il client CA Fabric e la generazione dei certificati, consulta [MSP (Membership Services Provider)](/docs/services/blockchain/certificates.html#managing-certificates-msp).

### Iscrizione utilizzando il client CA Fabric
{: #managing-certificates-enroll-app-caclient}

1. Scarica i [file binari CA Fabric](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) sulla tua macchina locale, estraili e spostali in una cartella come `$HOME/fabric-ca-platform/`. Passa alla directory in cui hai spostato i file binari del client in modo da poter fare riferimento a essa direttamente nei comandi.
    ```
    cd $HOME/fabric-ca-platform/
    ```
    {:codeblock}

2.  Imposta il percorso in cui il client può creare le tue chiavi. Assicurati di rimuovere il materiale di configurazione che è stato creato nello scorso tentativo. Se non hai già eseguito il comando `enroll`, la cartella `msp` e il file `.yaml` non esistono.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3. Scarica i certificati TLS da {{site.data.keyword.cloud_notm}} a seconda del piano di servizi, dell'ubicazione e del cluster che utilizzi. Puoi trovare il tuo piano di servizi in base all'URL della tua CA (Certificate Authority).
  - Certificato TLS per piano Starter
    - Stati Uniti: [us01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert"); [us03.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert"); [us05.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert"); [us07.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - Regno Unito: [uk01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
  [uk03.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")
  - [TLS Cert for Enterprise Plan ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

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

6. Trova il tuo certificato di amministrazione in `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. Puoi quindi caricare il certificato di amministrazione nella tua rete blockchain dal Monitoraggio della rete. Per ulteriori informazioni sull'aggiunta di certificati, vedi [la scheda "Certificati" del pannello "Membro"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) nel Monitoraggio della rete.

  Puoi anche trovare il certificato root CA e la chiave privata amministratore nelle seguenti directory:
  * Certificato root CA: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * Chiave privata amministratore: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Per un esempio di dove ti iscrivi utilizzando il client CA Fabric e utilizzi i certificati generati per gestire un componente di rete, vedi le istruzioni in [Utilizzo di un peer remoto](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-cli-operate).

### Registrazione utilizzando il client CA Fabric
{: #register-app-caclient}

1. Immetti il seguente comando per trovare il nome della tua affiliazione e della tua organizzazione nella rete blockchain.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Dovresti visualizzare informazioni simili al seguente esempio:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```

  Prendi nota del secondo valore **affiliation**, ad esempio `ibp.PeerOrg1`. Dovrai utilizzare questo valore nel comando riportato di seguito.

2. Immetti il seguente comando per registrare il peer.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Specifica un nome e una password per il tuo peer e sostituisci `name` e `password` con il nome e la password del tuo peer. Prendi nota di queste informazioni che ti serviranno quando configuri il tuo peer. Ad esempio:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

  Puoi registrare un'entità solo una volta. Se riscontri un problema, prova un comando con un nuovo nome utente e password.

  Se il comando viene completato correttamente, dovresti visualizzare informazioni simili al seguente esempio:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```

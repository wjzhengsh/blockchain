---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: APIs, build a network, authentication, service credentials, API key, API endpoint, IAM access token, Fabric CA client, import a network, generate certificates

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:curl: #curl .ph data-hd-programlang='curl'}

# Creazione di una rete con le API
{: #ibp-v2-apis}

{{site.data.keyword.blockchainfull}} Platform ti espone le API RESTful per creare, importare, modificare ed eliminare i tuoi componenti blockchain, nonché gestire le impostazioni della console, delle notifiche e della registrazione. Puoi utilizzare le API e gli SDK corrispondenti, per sviluppare delle applicazioni che interagiscono con la tua rete blockchain.
{: shortdesc}

Questa esercitazione introduce il flusso generico per creare una rete blockchain con le API {{site.data.keyword.blockchainfull_notm}} Platform. Per ulteriori informazioni su ciascuna API, vedi la documentazione di riferimento API [documentazione di riferimento API {{site.data.keyword.blockchainfull_notm}} Platform ![Icona link esterno](../images/external_link.svg "Icona link esterno")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform"){: new_window}.

Queste API sono compatibili solo con {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} v0.1.77 o superiore. Per controllare la versione, vai a `https://[your_console_url]/version.txt`, dove *`[your_console_url]`* è l'URL della tua console {{site.data.keyword.blockchainfull_notm}} Platform. Ad esempio: https://1ee1869ffa6496d6bc1ce4b-optools.bp01.blockchain.cloud.ibm.com/version.txt
{:note}

## Prima di cominciare
{: #ibp-v2-apis-prereq}

Devi avere un'istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} in modo da poter immettere delle chiamate API per interagire con la rete. Se non hai ancora un'istanza del servizio, segui le [Istruzioni introduttive](/docs/services/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks) per crearne una.

## Autenticazione
{: #ibp-v2-apis-authentication}

Per poter utilizzare le API per accedere alla tua rete blockchain che crei con {{site.data.keyword.blockchainfull_notm}} Platform, la tua applicazione deve essere in grado di autenticarsi con {{site.data.keyword.cloud_notm}} e connettersi alla tua istanza del servizio.

### Richiamo delle credenziali del servizio 
{: #ibp-v2-apis-retrieve-service-credentials}

Hai bisogno delle credenziali di autenticazione di base per assicurarti di avere l'accesso alla tua istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}.

1. Nel tuo [elenco di risorse {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/resources), apri l'istanza del servizio blockchain che hai creato.
2. Fai clic su **Credenziali del servizio** dal navigator di sinistra.
3. Fai clic sul pulsante "Nuova credenziale" nella pagina **Credenziali del servizio** per creare una nuova credenziale.
  1. Fornisci un nome alla credenziale, ad esempio *UseAPIs*.
  2. Puoi lasciare vuoto il campo "Aggiungi parametro di configurazione inline". 
  3. Fai clic sul pulsante **Aggiungi**. 
4. Dopo avere creato la nuova credenziale, fai clic su **Visualizza credenziali** nell'intestazione **AZIONI** di tale credenziale. I contenuti della credenziale sono simili al seguente esempio:

  ```
  {
    "api_endpoint": "https://1088ac8563e44f5a92539d946733ad7e-optools.so01.blockchain.test.cloud.ibm.com"
    "apikey": "nvASKts6B6lMZGZUNTGVP7MLK2BujMnxz0plSPYaqc3R",
    "configtxlator": "https://1088ac8563e44f5a92539d946733ad7e-configtxlator.so01.blockchain.test.cloud.ibm.com",
    "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:blockchain:us-south:a/9d46037caee397faa45c55e087d26baa:1088ac85-63e4-4f5a-9253-9d946733ad7e::",
    "iam_apikey_name": "auto-generated-apikey-c1981f5c-cff0-464f-9a78-ed2f52e24d1a",
    "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
    "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/9d46037caee397faa45c55e087d26baa::serviceid:ServiceId-774190c5-9c37-4f68-8572-8d6e2aabbc7e",
  }
  ```

Nelle credenziali del servizio, puoi trovare la **chiave API** (`apikey`) e l'**endpoint API** (`api_endpoint`) di cui hai bisogno per richiamare un token di accesso {{site.data.keyword.iamshort}} (IAM).

### Richiamo di un token di accesso
{: #ibp-v2-apis-retrieve-token}

Puoi autenticarti con {{site.data.keyword.blockchainfull_notm}} Platform richiamando un token di accesso da IAM. Con un token di accesso, puoi utilizzare l'API {{site.data.keyword.blockchainfull_notm}} Platform al posto del tuo servizio o applicazione in o all'esterno di {{site.data.keyword.cloud_notm}}, senza dover condividere le tue credenziali utente personali.  

Richiama l'API {{site.data.keyword.iamshort}} per richiamare il tuo token di accesso.

```cURL
curl -X POST \
  "https://iam.cloud.ibm.com/identity/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  -d "grant_type=urn%3Aibm%3Aparams%3Aoauth%3Agrant-type%3Aapikey&apikey=<API_KEY>" \
```
{: codeblock}

Nella richiesta, sostituisci `<API_KEY>` con il valore `apikey` dalle tue credenziali del servizio. Il seguente esempio troncato mostra l'output del token:

```
{
"access_token": "eyJraWQiOiIyM...",
"refresh_token": "...",
"expires_in":3600,
"expiration":1555558683,
"scope":"ibm openid"}
```
{: screen}

Utilizza il valore `access_token` completo, preceduto dal tipo di token `Bearer`, per gestire in modo programmatico la tua rete blockchain utilizzando le API.

I token di accesso sono validi per un'ora, ma puoi rigenerarli se necessario. Per mantenere l'accesso al servizio, aggiorna regolarmente il token di accesso per la tua chiave API richiamando l'API {{site.data.keyword.iamshort}}.   
{: tip}

## Creazione della tua richiesta API
{: #ibp-v2-apis-form-api-request}

Quando effettui una chiamata API al servizio, strutturala in base a come hai eseguito inizialmente il provisioning della tua istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform.

Per creare la tua richiesta, accoppia un endpoint API alle credenziali di autenticazione appropriate nel seguente formato:

```cURL
curl -X <API method> \
    <API_endpoint>/<API> \
    -H 'authorization: Bearer <access_token>' \
    -H 'Content-Type: application/json' \
    -d '<request body>' \
```
{: codeblock}

Comandi curl di esempio per ogni API nella documentazione di riferimento API [documentazione di riferimento API {{site.data.keyword.blockchainfull_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](/apidocs/blockchain "{{site.data.keyword.blockchainfull_notm}} Platform").

Inoltre, puoi utilizzare la funzione **Provalo** nella documentazione di riferimento API per verificare le tue chiamate API prima di aggiungerle alle tue applicazioni. Devi aver eseguito l'accesso a {{site.data.keyword.cloud_notm}} per poter utilizzare la funzione **Provalo**. Puoi selezionare qualsiasi istanza del servizio dall'elenco a discesa. Tutte le richieste API vengono inviate alla rete specificata nell'endpoint API.

## Limitazioni
{: #ibp-v2-apis-limitations}

Puoi importare solo CA, peer e nodi di ordinazione esistenti da altre reti {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}.

## Creazione di una rete utilizzando le API
{: #ibp-v2-apis-build-with-apis}

Puoi utilizzare le API per creare i componenti blockchain nella tua istanza di {{site.data.keyword.blockchainfull_notm}} Platform. Utilizza la seguente procedura per creare una rete blockchain utilizzando le API {{site.data.keyword.blockchainfull_notm}}.

1. Crea un'autorità di certificazione (CA) richiamando [`POST /ak/api/v1/kubernetes/components/ca`](/apidocs/blockchain?code=try#create-a-ca).

  Ricorda il tuo input e la tua risposta, ne avari bisogno più avanti.
  {: tip}

  Devi attendere l'avvio della CA. Potrebbe impiegarci diversi minuti a seconda dell'ambiente. Puoi richiamare l'API `GET <ca_url>/cainfo` per controllare lo stato della tua CA. Riceverai degli errori ripetuti, poi un codice di stato `200`, il quale indica che puoi procedere al passo successivo. Tieni presente che questa chiamata API va in timeout dopo un minuto.

2. Utilizza la tua CA per registrare il tuo componente e le identità amministratore e generare i certificati necessari. Puoi utilizzare il client CA Fabric per completare la seguente procedura: 

  - [Configura il client CA Fabric](#ibp-v2-apis-config-fabric-ca-client).
  - [Genera i certificati con il tuo amministratore CA](#ibp-v2-apis-enroll-ca-admin).
  - [Registra il nuovo componente con la tua CA](#ibp-v2-apis-config-register-component).
  - Devi anche [registrare un amministratore dell'organizzazione](#ibp-v2-apis-config-register-admin) e successivamente [generare i certificati per l'amministratore](#ibp-v2-apis-config-enroll-admin) in una cartella MSP. Non devi completare questo passo se hai già registrato la tua identità amministratore.
  - [Registra il nuovo componente con la tua CA TLS](#ibp-v2-apis-config-register-component-tls).

  Puoi anche completare questa procedura utilizzando la tua console {{site.data.keyword.blockchainfull_notm}} Platform. Per ulteriori informazioni, vedi [Creazione e gestione delle identità](/docs/services/blockchain/howto/ibp-console-identities.html).

3. [Crea una definizione MSP per la tua organizzazione](#ibp-v2-apis-msp) richiamando [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?#import-a-membership-service-provide-msp).

4. [Crea il file di configurazione](#ibp-v2-apis-config) necessario per creare un ordinante o un peer. Devi creare un file di configurazione univoco per ogni ordinante o peer che desideri creare.

5. Crea un ordinante richiamando [`POST /ak/api/v1/kubernetes/components/orderer`](/apidocs/blockchain?code=try#create-an-orderer).

6. Crea un peer richiamando [`POST /ak/api/v1/kubernetes/components/peer`](/apidocs/blockchain?code=try#create-a-peer).

7. Se vuoi utilizzare la console per gestire i tuoi componenti blockchain, devi importare la tua identità amministratore nel tuo portafoglio della console. Utilizza la scheda del portafoglio per importare il certificato e la chiave privata del tuo amministratore del nodo nella console e crea un'identità. Devi poi utilizzare la console per associare questa identità ai componenti che hai creato. Per ulteriori informazioni, vedi [Importazione di un'identità amministratore nella console {{site.data.keyword.blockchainfull_notm}} Platform](#ibp-v2-apis-admin-console).

8. Dopo aver distribuito la tua rete, puoi utilizzare gli SDK Fabric, la CLI peer o l'IU della console per creare dei canali e installare o istanziare gli smart contract.

Le credenziali del servizio utilizzate per l'autenticazione API devono avere il ruolo di gestore (`Manager`) in IAM per poter creare i componenti. Per ulteriori informazioni, vedi la tabella in questo argomento sui [ruoli utente](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).
{: note}

## Importa una rete utilizzando le API
{: #ibp-v2-apis-import-with-apis}

Puoi utilizzare le API anche per importare i componenti {{site.data.keyword.blockchainfull_notm}} creati utilizzando le API o la console {{site.data.keyword.blockchainfull_notm}} Platform in un'altra istanza del servizio di {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Importa una CA richiamando [`POST /ak/api/v1/components/ca`](/apidocs/blockchain?code=try#import-a-ca).

  Ricorda il tuo input e la tua risposta, ne avari bisogno più avanti.
  {: tip}

  Devi attendere l'avvio della CA. Potrebbe impiegarci diversi minuti a seconda dell'ambiente. Puoi richiamare [`GET /components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) per controllare lo stato della CA. Riceverai degli errori ripetuti prima di un codice di stato `200` per andare al passo successivo. Tieni presente che questa chiamata API va in timeout in un minuto.

2. Importa una definizione MSP dell'organizzazione richiamando [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp).

3. Importa un ordinante richiamando [`POST /ak/api/v1/components/orderer`](/apidocs/blockchain?code=try#import-a-orderer).

4. Importa un peer richiamando [`POST /ak/api/v1/components/peer`](/apidocs/blockchain?code=try#import-a-peer).

5. Se pensi di utilizzare la console {{site.data.keyword.blockchainfull_notm}} Platform per gestire i tuoi componenti blockchain, devi importare le tue identità amministratore del componente nel tuo portafoglio della console. Per ulteriori informazioni, vedi [Importazione di un'identità amministratore nella console {{site.data.keyword.blockchainfull_notm}} Platform](#ibp-v2-apis-admin-console).

6. Dopo aver distribuito la tua rete, puoi utilizzare gli SDK Fabric, la CLI peer o l'IU della console per creare dei canali e installare o istanziare gli smart contract.

Le credenziali del servizio utilizzate per l'autenticazione API devono avere il ruolo di scrittore (`Writer`) in IAM per poter importare i componenti. Per ulteriori informazioni, vedi la tabella in questo argomento sui [ruoli utente](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).
{: note}

## Gestione della tua CA utilizzando il client CA Fabric
{: #ibp-v2-apis-config-fabric-ca-client}

Puoi utilizzare il client CA Fabric per utilizzare le tue CA. Immetti i seguenti comandi del client CA Fabric per registrare il tuo componente e le identità amministratore e generare i certificati necessari. 

### Configurazione del client CA Fabric
{: #ibp-v2-apis-setup-fabric-ca-client}

1. Scarica il [Fabric CA client ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Download Fabric CA client") sul tuo file system locale.

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

5. Richiama il certificato TLS della tua CA in modo che venga utilizzato dal client CA Fabric. Se stai utilizzando la console {{site.data.keyword.blockchainfull_notm}} Platform, apri la CA e fai clic su **Impostazioni** e cerca il certificato in formato base64 nel campo **Certificato TLS**. Se stai utilizzando le API, puoi richiamare [`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) e trovare il certificato TLS CA nel campo `"PEM"`. Se hai creato la CA utilizzando l'API `Create a Fabric CA`, puoi trovare il certificato TLS anche nel corpo della risposta.

  Devi convertire il certificato da base64 nel formato PEM per utilizzarlo per comunicare con la tua CA. Inserisci la stringa codificata base64 del certificato TLS nel seguente comando. Assicurati di essere nella tua directory `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > tls.pem
  ```
  {:codeblock}

### Genera i certificati con il tuo amministratore CA
{: #ibp-v2-apis-enroll-ca-admin}

Un'identità **amministratore CA** è stata automaticamente registrata per te quando hai creato la tua CA. Puoi ora utilizzare tali nome e password amministratore per emettere un comando `enroll` con il client CA Fabric per generare una cartella MSP con i certificati che vengono poi utilizzati per registrare altre identità ordinanti o peer.

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

  `<enroll_id>` e `<enroll_password>` nel comando sono i valori `enroll_id` e `enroll_secret` che hai specificato quando hai creato la CA. Utilizza l'**URL endpoint dell'autorità di certificazione** dalla tua console {{site.data.keyword.blockchainfull_notm}} Platform o il `"ca_url"` restituito dalla tua chiamata API come valore per `<ca_url_with_port>`. Tralascia il `http://` all'inizio. `<ca_name>` è il **Nome CA** dalla tua console o il `"ca_name"` restituito dalle API.

  `<ca_tls_cert_path>` è il percorso completo del tuo certificato TLS CA.

  Una vera chiamata potrebbe essere simile al seguente comando di esempio:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```  
  {:codeblock}

**Suggerimento:** se il valore dell'URL di iscrizione, ossia il valore del parametro `-u`, contiene un carattere speciale, devi codificare il carattere speciale o racchiudere l'URL tra virgolette singole. Ad esempio, `!` diventa `%21` o il comando è simile a:

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname ca
  ```
  {:codeblock}

  Il comando `enroll` genera una serie completa di certificati, nota come cartella MSP (Membership Service Provider), ubicata nella directory in cui hai impostato il percorso su `$HOME` per il tuo client CA Fabric. Ad esempio, `$HOME/fabric-ca-client/ca-admin`. Per ulteriori informazioni sulle MSP e su cosa la cartella MSP contiene, consulta [MSP (Membership Service Provider)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp).

  Se il comando `enroll` ha esito negativo, consulta l'[argomento Risoluzione dei problemi](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting) per le cause possibili.

  Puoi eseguire un comando tree per verificare di aver completato tutti i passi preliminari. Passa alla directory in cui hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:

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

### Registrazione dell'identità del componente con la CA
{: #ibp-v2-apis-config-register-component}

Per prima cosa, devi registrare un'identità del componente con la tua CA. Il tuo componente utilizzerà questa identità per generare i certificati di cui ha bisogno quando viene distribuito.

1. [Genera i certificati con il tuo amministratore CA](#ibp-v2-apis-enroll-ca-admin) utilizzando il client CA Fabric. Utilizza questi certificati di gestione per emettere i seguenti comandi. Assicurati che `$FABRIC_CA_CLIENT_HOME` sia impostato su `$HOME/fabric-ca-client/ca-admin`.

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
  fabric-ca-client affiliation list --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
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

  Crea un nome e una password per il componente e utilizzali per sostituire `name` e `secret`.  Prendi nota di queste informazioni Imposta `--id.type` su `orderer` se stai distribuendo un ordinante, oppure su `peer` se stai distribuendo un peer. Il comando potrebbe essere simile al seguente esempio:

  ```
  fabric-ca-client register --caname ca --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Devi salvare i valori `"enrollid"` e `"enrollsecret"` dal comando precedente per quando creerai il tuo file di configurazione.
  {: important}

  Puoi registrare un'entità solo una volta. Se riscontri un problema, prova un comando con un nuovo nome utente e password. Come misura di sicurezza, utilizza ogni identità, e l'ID iscrizione e il segreto di accompagnamento, per distribuire solo un singolo peer. Mentre puoi utilizzare un'identità **amministratore** per diversi componenti (questa è la nostra strategia di distribuzione consigliata), non riutilizzare gli ID e le password del peer.

  Se il comando viene completato correttamente, dovresti visualizzare informazioni simili al seguente esempio:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

### Registrazione del tuo amministratore dell'organizzazione
{: #ibp-v2-apis-config-register-admin}

Devi anche creare un'identità amministratore che puoi utilizzare per gestire la tua rete. Dovrai utilizzare questa identità per gestire componenti specifici, ad esempio installando uno smart contract sul tuo peer. Puoi anche utilizzare questa identità come un amministratore della tua organizzazione e utilizzarla per creare e modificare i canali.  

Devi registrare questa nuova identità con la tua CA e utilizzarla per generare una cartella MSP. Puoi rendere questa identità un amministratore dell'organizzazione aggiungendo il suo signCert al tuo MSP dell'organizzazione. Dovrai anche aggiungere il signCert al tuo file di configurazione in modo che possa diventare il certificato amministratore dell'ordinante o del peer durante a distribuzione. Devi creare solo un'identità amministratore per la tua organizzazione. Di conseguenza, devi completare questa procedura solo una volta. Puoi utilizzare il signCert che hai generato per distribuire i tuoi peer o ordinanti. 

Assicurati che il tuo `$FABRIC_CA_CLIENT_HOME` sia impostato sul percorso alla MSP del tuo amministratore della CA.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Registra l'identità di amministrazione del componente con la CA immettendo il seguente comando:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type client --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Crea dei nuovi `name` e `secret` dell'identità utente per l'amministratore. Assicurati di utilizzare dei valori differenti rispetto a quelli dell'identità peer o ordinante che hai appena registrato. Il comando è simile al seguente esempio:

```
fabric-ca-client register --caname ca --id.name peeradmin --id.affiliation org1.department1 --id.type client --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Prendi nota di queste informazioni Utilizzerai questi `name` e `secret` per generare la cartella MSP utilizzando il comando `enroll`.
{: important}

### Generazione della cartella MSP (Membership Service Provider) di gestione
{: #ibp-v2-apis-config-enroll-admin}

Dopo aver registrato l'amministratore del componente, puoi generare i certificati immettendo il seguente comando:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Ad esempio:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname ca -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```
{:codeblock}

Dopo che questo comando è stato completato correttamente, genera una nuova cartella MSP nella directory che hai specificato utilizzando l'indicatore `-M`. Questa directory contiene i certificati che devi utilizzare per gestire i tuoi componenti. Puoi verificare che il comando enroll abbia funzionato correttamente utilizzando un comando tree. Passa alla directory in cui hai archiviato i tuoi certificati. Un comando tree dovrebbe generare un risultato simile alla seguente struttura:


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

Dovrai ritornare a questa cartella quando crei la definizione MSP dell'organizzazione e i file di configurazione.
{: important}

### Registrazione dell'identità del componente con la CA TLS
{: #ibp-v2-apis-config-register-component-tls}

Quando hai creato la tua CA, è stata distribuita una CA TLS insieme alla tua CA predefinita. Devi registrare anche l'ordinante o il peer con la tua CA TLS. Per far ciò, dovrai prima eseguire l'iscrizione utilizzando l'amministratore della CA TLS. Modifica `$FABRIC_CA_CLIENT_HOME` con una directory in cui vuoi archiviare i tuoi certificati di gestione della CA TLS.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Immetti il seguente comando per l'iscrizione come amministratore per la CA TLS. La password e l'ID di iscrizione del tuo amministratore CA TLS sono gli stessi della CA predefinita. Di conseguenza, il seguente comando è lo stesso che hai utilizzato per l'iscrizione come [amministratore della CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin) soltanto con il nome della tua CA TLS. Il tuo nome della CA TLS è il valore **Nome CA TLS** dal pannello **Impostazioni** della CA nella tua console oppure il valore per `"tlsca_name"` restituito dall'API `Create a CA`.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Una chiamata reale potrebbe essere simile al seguente esempio:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Dopo aver eseguito l'iscrizione, disponi dei certificati necessari per registrare il tuo componente con la CA TLS. Immetti il seguente comando per registrare il peer o l'ordinante:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Questo comando è simile a quello utilizzato per registrare l'identità del componente con la CA, con l'eccezione che devi utilizzare il nome della CA TLS. Se stai distribuendo un ordinante invece di un peer, imposta `--id.type` su `orderer` invece di `peer`. Devi fornire a questa identità un nome utente e una password diversi rispetto a quelli utilizzati con la CA predefinita. Una registrazione reale è simile al seguente comando:

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Devi salvare i valori `"enrollid"` e `"enrollsecret"` dal comando precedente per quando creerai il tuo file di configurazione.
{: important}

## Creazione di una definizione MSP dell'organizzazione
{: #ibp-v2-apis-msp}

Puoi utilizzare le API per creare una definizione MSP dell'organizzazione richiamando [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp). Questo MSP contiene i certificati che definiscono la tua organizzazione in un consorzio blockchain, nonché i certificati di gestione che puoi utilizzare per gestire la tua rete. Se hai seguito la precedente procedura, hai già generato i certificati necessari per creare un MSP dell'organizzazione. Utilizza la seguente procedura per completare il corpo della richiesta della chiamata API.

1. Immetti `msp` come tipo (`type`) di richiesta.

2. Seleziona un ID MSP per la tua organizzazione. L'ID MSP è il nome formale della tua organizzazione all'interno del consorzio. L'ID MSP utilizzato per creare l'MSP dell'organizzazione deve essere lo stesso che utilizzi per distribuire i tuoi peer.

3. Seleziona un nome di visualizzazione per il tuo MSP.

4. Devi fornire il signCert, nel formato base64, del tuo amministratore dell'organizzazione che hai registrato e iscritto utilizzando il client CA Fabric.  

  Vai alla directory MSP che è stata creata quando hai [generato i certificati utilizzando il tuo amministratore dell'organizzazione](#ibp-v2-apis-config-enroll-admin).
  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
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
  Non simile a questa:

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

  Fornisci questa stringa al campo `admins` della richiesta API.

5. Devi anche fornire il certificato root della tua CA. Questo certificato è stato creato quando hai [generato i certificati utilizzando il tuo amministratore della CA](#ibp-v2-apis-enroll-ca-admin).

  Passa alla directory MSP dell'amministratore CA.
  ```
  cd $HOME/fabric-ca-client/ca-admin/msp
  ```
  In questa directory, apri la cartella cacerts e converti il certificato al suo interno nel formato base64 utilizzando i seguenti comandi:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-ca-admin>/msp/cacerts/<ca_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  Questo comando riprodurrà a schermo il certificato root come una stringa codificata base64.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGekNDQWIyZ0F3SUJBZ0lVQmZnZzcvVnIrL25OVEFNQlQ4UUtHL00wQU8wd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU1TURVd016RXpNamt3TUZvWERUTTBNRFF5T1RFek1qa3dNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVXMUtvN2lWeVE2VWkwdDVqbU5KaWVuSUwKR3pNM1BDWHlhL2VSQ0NWMmFQb0dTZ1lrVUg2UWN5RjAzbFlMZFU4Y0drNTQ0alViVC9KT1lYeVgzTWc4bHFORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZDK2lJR0NSb2Zvb3FsVkZoU3dOMmk2MXNJaVBNQW9HQ0NxR1NNNDlCQU1DQTBnQU1FVUNJUURTYW9RL1E0QzkKbFl1VGNhVXVHb3d6YmhUZHBuN2F3S2lHN1Nvd2lSQXVld0lnUWlyM3RNR3IvYWo2aU5lRXJFN2NyOVowQ0gvTwp3QnNQcWd4RVR3MjVqZUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  Fornisci questa stringa al campo `root_certs` della richiesta API.

6. Devi anche fornire il certificato root della tua CA. Il certificato root TLS permette ai tuoi peer di partecipare ai gossip su un canale.

  Vai alla directory MSP che è stata generata quando hai [iscritto il tuo amministratore CA TLS](#ibp-v2-apis-config-register-component-tls).
  ```
  cd $HOME/fabric-ca-client/tlsca-admin/msp
  ```
  In questa directory, apri la cartella cacerts e converti il certificato al suo interno nel formato base64 utilizzando i seguenti comandi:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-tlsca-admin>/msp/cacerts/<tls_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  Questo comando riprodurrà a schermo il certificato root CA TLS come una stringa codificata base64.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVWUVQWnprNXV2b3dobEtacG5JMXplODdIQUlnd0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWURWUVFERXdWMGJITmpZVEFlCkZ3MHhPVEExTURNeE16STVNREJhRncwek5EQTBNamt4TXpJNU1EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdzVPYjNKMGFDQkRZWEp2YkdsdVlURVVNQklHQTFVRUNoTUxTSGx3WlhKc1pXUm5aWEl4RHpBTgpCZ05WQkFzVEJrWmhZbkpwWXpFT01Bd0dBMVVFQXhNRmRHeHpZMkV3V1RBVEJnY3Foa2pPUFFJQkJnZ3Foa2pPClBRTUJCd05DQUFRdSs2UnZWd2w5T2dDVlAraEVxbjVxdExRVG9LWkw4a1lic0pOeU1JbERoc3hlNWx6cW1zQkoKbTk2eUR2TVV6OSsxL2pzb1M4M1JqMVAwc3M2TnJNb3FvMXd3V2pBT0JnTlZIUThCQWY4RUJBTUNBUVl3RWdZRApWUjBUQVFIL0JBZ3dCZ0VCL3dJQkFUQWRCZ05WSFE0RUZnUVVnUEc4anJEK1BxVjdoelc3WDlsbTFrMS91WjR3CkZRWURWUjBSQkE0d0RJY0VxVGJESW9jRUNwbnBkVEFLQmdncWhrak9QUVFEQWdOSUFEQkZBaUVBenk3cHJZaVMKQmlDVWdYeWRkY09WMm9mZmtqaEI0N091QXFjQWNqZS9SWkVDSUdKZFgzZ1ErTDRIN3duY1RoZkwrenU1ejV1UApGUWhXTmlNS3hQWEYrZnYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  Fornisci questa stringa al campo `tls_root_certs` della richiesta API.

## Creazione di un file di configurazione
{: #ibp-v2-apis-config}

Devi completare un file di configurazione per poter creare un peer o un ordinante utilizzando le API. Questo file viene fornito all'API come oggetto `config` nel corpo della richiesta della chiamata API. Devi distribuire una CA alla tua istanza del servizio {{site.data.keyword.cloud_notm}} Platform e seguire la procedura per registrare ed iscrivere le identità richieste prima di completare il file.

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

Copia questo file completo in un editor di testo in cui puoi modificarlo e salvarlo sul tuo file system locale come un file JSON. Utilizza la seguente procedura per completare questo file di configurazione e utilizzarlo per distribuire un ordinante o un peer.

### Richiama le informazioni di connessione della CA
{: #ibp-v2-apis-config-connx-info}

Per prima cosa, devi fornire le informazioni di connessione della tua CA su {{site.data.keyword.cloud_notm}} Platform. Puoi utilizzare l'IU della console o le API per ottenere le informazioni necessarie sulla tua CA.

**Se stai utilizzando la console {{site.data.keyword.blockchainfull_notm}} Platform:**
Apri la CA nella tua console e fai clic su **Impostazioni** e quindi sul pulsante **Esporta** per esportare le informazioni sulla CA in un file JSON. Puoi utilizzare i valori da questo file per completare il tuo file di configurazione.

**Se stai utilizzando le API:**
Puoi richiamare [`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) per ottenere le informazioni di connessione sulla tua CA. Se hai creato la CA utilizzando l'API `Create a Fabric CA`, puoi trovare le informazioni necessarie anche nel corpo della risposta.

- I valori `"cahost"` e `"caport"` sono visibili nel campo `ca_url` nel corpo della risposta o nel file JSON CA che hai esportato.  Ad esempio, se il tuo `ca_url` è https://9.30.94.174:30167, il valore di `"cahost"` sarà `9.30.94.174` e `"caport"` sarà `30167`.
- `"caname"` è il nome della CA che è stato specificato quando hai distribuito la CA. Questo è il valore del campo `ca_name` nel corpo della risposta o nel file JSON esportato.
- `"cacert"` è un certificato TLS codificato base64 della tua CA. Questo è il valore del campo `pem` nel corpo della risposta o nel file JSON esportato.

- I campi nella seguente sezione `"tls"` richiedono le stesse informazioni che hai passato alle sezioni precedenti del componente, con l'eccezione che devi utilizzare il valore del nome dell'istanza TLS CA specificato durante la distribuzione della CA. Sostituisci `caname` con il valore di `tlsca_name` nel corpo della risposta o nel file JSON esportato. Utilizza lo stesso certificato TLS per il valore `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

### Fornisci i tuoi segreto e ID di iscrizione del componente

1. Incolla il nome (`name`) e il segreto (`secret`) utilizzati per [registrare il tuo componente con la CA predefinita](#ibp-v2-apis-config-register-component) come i valori `"enrollid"` e `"enrollsecret"` nel file di configurazione, nella sezione `"component"`:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```
2. Incolla il nome (`name`) e il segreto (`secret`) utilizzati per [registrare il tuo componente con la CA TLS](#ibp-v2-apis-config-register-component-tls) come i valori `"enrollid"` e `"enrollsecret"` nel file di configurazione, nella sezione `"tls"`:

  ```
  "tls": {...
    },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
  ```

### Fornisci il signCert del tuo amministratore dell'organizzazione

Vai alla directory MSP che è stata creata quando hai [generato i certificati utilizzando il tuo amministratore dell'organizzazione](#ibp-v2-apis-config-enroll-admin).
```
cd $HOME/fabric-ca-client/peer-admin/msp
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
Non simile a questa:

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

### Host della richiesta di firma del certificato (CSR)

Hai l'opzione di fornire un dominio personalizzato al tuo componente utilizzando la sezione `"csr"` del file di componenti.

```
"csr": {
  "hosts": [""]
  }
```
{:codeblock}

Questa sezione viene fornita per gli utenti esperti per specificare un nome host personalizzato che può essere utilizzato per indirizzare l'endpoint del peer. La maggior parte degli utenti può lasciare vuota questa sezione.

### Completamento del file di configurazione
{: #ibp-v2-apis-config-file}

Dopo aver completato tutti i precedenti passi, il tuo file di configurazione aggiornato potrebbe essere simile al seguente esempio:


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "ca",
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
                ]
            }
        }
    }
}
```
{:codeblock}

Puoi lasciare gli altri campi vuoti. Dopo aver completato questo file, puoi passarlo come il campo `config` al corpo della richiesta dell'API `Create an orderer` o `Create a peer`.

### Importazione di un'identità amministratore nella console {{site.data.keyword.blockchainfull_notm}} Platform.
{: #ibp-v2-apis-admin-console}

Se vuoi utilizzare la console {{site.data.keyword.blockchainfull_notm}} Platform per gestire i tuoi componenti blockchain, devi importare la tua identità amministratore nel tuo portafoglio della console. Apri il pannello del portafoglio nella tua console. Fai clic sul pulsante **Aggiungi identità** sulla schermata della panoramica. Facendo clic su questo pulsante si aprirà un pannello laterale che ti consente di aggiungere il certificato di firma e la chiave privata di un'identità direttamente alla console.
- **Nome:** immetti un nome di visualizzazione per l'identità che viene utilizzato solo come riferimento.
- **Certificato:** carica il certificato di firma del tuo amministratore. Se hai seguito le precedenti istruzioni, puoi trovare questa chiave nella cartella `$HOME/fabric-ca-client/peer-admin/msp/signcerts/`.
- **Chiave privata:** carica la chiave privata dei tuoi amministratori. Se hai seguito le precedenti istruzioni, puoi trovare questa chiave nella cartella `$HOME/fabric-ca-client/peer-admin/msp/keystore/`.

Dopo aver importato la tua identità amministratore, puoi associarla ai componenti che hai creato. Puoi utilizzare la console per gestire la tua rete.

---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:important: .important}
{:note: .note}
{:pre: .pre}

# Creazione e gestione delle identità
{: #ibp-console-identities}

I nodi di {{site.data.keyword.blockchainfull_notm}} Platform si basano su Hyperledger Fabric e creano reti blockchain autorizzate. Ciò significa che tutti i partecipanti al consorzio blockchain devono disporre di identità continuamente verificate dall'infrastruttura a chiave pubblica (PKI). La console {{site.data.keyword.blockchainfull_notm}} Platform ti consente di creare queste identità utilizzando le CA (Certificate Authority) della tua organizzazione. Devi archiviare queste identità nel tuo portafoglio della console in modo da poterle utilizzare per gestire la tua rete.

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain.

## Gestione delle autorità di certificazione (CA)
{: #ibp-console-identities-manage-ca}

Una CA è simile a un notaio pubblicamente affidabile che funge da punto di riferimento di attendibilità tra più parti, con ciascuna organizzazione nel consorzio che mantiene la propria CA. La tua CA crea le identità che appartengono alla tua organizzazione ed emette una chiave pubblica e una privata per ogni identità. Queste chiavi sono ciò che consente a tutti i nodi e alle applicazioni di firmare e verificare le loro azioni. Per ulteriori dettagli su come vengono utilizzate le CA per stabilire le identità, consulta [l'argomento sulle identità ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "identity") nella documentazione di Hyperledger Fabric.

Quando crei una CA tramite la console {{site.data.keyword.blockchainfull_notm}} Platform, vengono create due CA con lo stesso URL endpoint: una CA root e una CA TLS. Puoi visualizzare entrambe queste CA con lo stesso nome di visualizzazione nella pagina **Nodi** della tua console. La CA root fornisce le chiavi per i tuoi nodi e le tue applicazioni. La CA TLS fornisce i certificati utilizzati per proteggere le comunicazioni all'interno della tua rete. Per ulteriori informazioni su TLS sulla tua rete, vedi [Utilizzo della tua CA TLS](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca).

Quando crei i nodi, devi utilizzare la tua CA root per creare le seguenti identità richieste per la distribuzione, il funzionamento e l'interazione con la tua rete:
- **Amministratore CA:** la tua CA contiene un'identità di amministratore CA predefinita. Questa identità ha un ID e un segreto di registrazione, analoghi a un nome utente e una password, che specifichi durante la distribuzione della tua CA. Puoi utilizzare questo nome utente e password dell'amministratore per gestire la CA e creare nuove identità. Se hai creato una CA utilizzando la console, l'amministratore CA della tua CA root è anche l'amministratore della CA TLS, a meno che tu non decida di creare un amministratore CA TLS separato.
- **Peer o ordinanti:** devi registrare un'identità per ciascuno dei peer e degli ordinanti che appartengono alla tua organizzazione. I tuoi nodi useranno quindi queste identità durante la distribuzione per generare le chiavi di cui hanno bisogno per partecipare alla rete. Per motivi di sicurezza, crea un ID e un segreto di registrazione univoco per ogni nodo che distribuisci.
- **Amministratori del peer o dell'ordinante:** i nodi di {{site.data.keyword.blockchainfull_notm}} Platform vengono distribuiti con le chiavi pubbliche delle identità degli amministratori dei componenti al loro interno. Questi certificati consentono agli amministratori di gestire il componente da un client remoto o mediante la console.
- **Amministratori dell'organizzazione:** quando ti unisci a un consorzio ospitato da un servizio ordini, fornisci le chiavi pubbliche delle identità che diventeranno gli amministratori della tua organizzazione. Puoi utilizzare queste identità per creare o modificare i canali.
- **Applicazioni:** le tue applicazioni devono firmare le loro transazioni prima di inviarle per la convalida da parte della rete. Devi creare le identità che puoi utilizzare per firmare le transazioni dalle tue applicazioni client.

Puoi utilizzare la console per creare queste identità attraverso il [processo di registrazione](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Dopo aver registrato le tue identità di amministratore, devi emettere per ogni identità una chiave pubblica e privata, fornire la chiave pubblica alla definizione MSP della tua organizzazione e aggiungere l'identità al tuo portafoglio della console. Puoi completare questi passi per un'identità di amministratore quando [crei l'MSP della tua organizzazione](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Puoi utilizzare identità separate come amministratori dell'organizzazione o amministratori del nodo oppure puoi utilizzare un'identità per eseguire entrambe le attività. L'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) utilizza un'identità come amministratore per ogni organizzazione creata nell'esercitazione.

## Impostazione di un'identità della CA
{: #ibp-console-identities-ca-identity}

Prima di lavorare con le identità, devi impostare l'identità dell'amministratore della CA, utilizzando l'identità di amministratore creata durante la creazione della CA o stabilendone una nuova. Apri la CA nella scheda **Nodi**. L'ID di registrazione dell'identità attualmente attiva è visibile accanto al nome CA nella parte superiore del pannello. Puoi utilizzare tale identità di amministratore per creare altre identità mediante il pulsante **Registra** per registrare le identità che diventeranno amministratori dell'organizzazione e identità dei nodi o utilizzare il pulsante **Iscrivi** per generare un'identità ed esportarla nel portafoglio.

Per passare a un'identità diversa da utilizzare come amministratore CA, fai clic sull'icona **Impostazioni** e quindi su **Imposta identità** nel dispositivo di scorrimento. Puoi specificare un'identità esistente nel portafoglio utilizzando la scheda **Identità esistente**. In alternativa, puoi utilizzare la scheda **Nuova identità** per incollare i certificati per un nuovo amministratore o per caricare un file JSON che contiene i certificati.

Non tutte le identità hanno la capacità di registrare nuovi utenti. Per ulteriori informazioni, incluso come stabilire un ulteriore amministratore CA, vedi [Creazione di nuovi amministratori della CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin).
{: note}

## Registrazione delle identità
{: #ibp-console-identities-register}

Il primo passo nella creazione di un'identità è noto come **registrazione**. Durante la registrazione, vengono creati un ID e un segreto di registrazione che possono quindi essere utilizzati da un amministratore del nodo o dell'organizzazione per **iscrivere** (enroll) questa identità generando una chiave pubblica e privata.

Quando registri una nuova identità con la tua CA, devi immettere le seguenti informazioni.

- **ID di registrazione** e **Segreto di registrazione**: questa identità ha un ID e un segreto, analoghi a un nome utente e una password, che specifichi durante la distribuzione della tua CA. Puoi utilizzare questo ID e segreto di amministratore per creare certificati con questa identità usando la tua console o il client CA Fabric.
- **Tipo**: quando la CA è stata distribuita, l'amministratore ha specificato i tipi di identità emessi dalla CA. Esempi comuni di tipi di identità comprendono peer, ordinante e client (applicazioni). Seleziona il tipo di identità per questo utente dall'elenco dei tipi disponibili.
- **Affiliazione**: la parte della tua organizzazione che vuoi affiliare a questo utente. Ad esempio, potrebbe essere un reparto o un'unità che gestisce un'applicazione o un peer. È possibile limitare un particolare amministratore della CA a poter solo registrare gli utenti del suo reparto a un'organizzazione.
- **Numero massimo di iscrizioni**: facoltativamente, immetti il numero di volte che puoi iscrivere o generare certificati utilizzando questa identità. La specifica di un numero limitato di iscrizioni consente di proteggere la sicurezza dei tuoi nodi e delle tue applicazioni. Il valore predefinito prevede un numero illimitato di iscrizioni.
- **Attributi**: facoltativamente, puoi specificare qualsiasi attributo di [controllo dell'accesso basato sugli attributi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "Attribute-Based Access Control") per l'utente. Ad esempio, puoi utilizzare questa sezione per [creare un altro amministratore della CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) con l'autorizzazione a registrare e iscrivere nuove identità. Puoi visualizzare un elenco completo degli attributi CA Fabric disponibili nella sezione [Registering a new identity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registering a new identity") nella guida per gli utenti della CA Fabric.

Fai clic sul pulsante **Registra utente** nel pannello di panoramica della CA per creare un nuovo utente. Prima di tentare questa attività, assicurati di aver [impostato l'identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)
utilizzando un'identità che ha la capacità di registrare nuovi utenti. In generale, questo è il tuo utente `admin`. Se il pulsante è grigio, ciò implica che non hai impostato un'identità o che l'identità non può creare nuove identità.  

Facendo clic su **Registra utente** viene aperta una serie di pannelli laterali:
1. Nel primo pannello laterale, immetti l'**ID di registrazione** e il **Segreto di registrazione** della nuova identità. **Salva questi valori** in quanto non vengono memorizzati dalla console.
2. Nel secondo pannello laterale, seleziona il **Tipo** di identità. Il menu a discesa contiene l'elenco dei tipi supportati da questa CA. Seleziona quindi l'**Affiliazione** a cui apparterrà la nuova identità. Puoi scegliere un'affiliazione esistente dall'elenco a discesa o immetterne una nuova.
3. Nel terzo pannello laterale, immetti il **Numero massimo di iscrizioni** consentite per questa identità. Se non specificato, il valore predefinito è di iscrizioni illimitate.
4. Nell'ultimo pannello laterale, aggiungi gli **Attributi** dell'identità che stai creando.

Dopo aver fatto clic su **Registra**, la nuova identità verrà aggiunta all'elenco di **Utenti autenticati** che sono stati creati dalla tua CA. Le identità sono elencate in base al loro **ID di registrazione**, insieme al proprio **Tipo** e **Affiliazione**. Facendo clic su un'identità nella tabella verrà aperto un pannello laterale che ti consente di visualizzare il **Numero massimo di iscrizioni** e gli **Attributi** che sono stati creati durante la registrazione.

### Creazione di nuovi amministratori della CA
{: #ibp-console-identities-ca-admin}

Per impostazione predefinita, solo l'amministratore della CA creato durante la distribuzione ha la capacità di registrare nuove identità. Puoi anche creare altre identità con la capacità di registrare nuovi utenti utilizzando il pannello **Attributi** del processo di registrazione.

Nel pannello laterale 4, fai clic sul pulsante **Aggiungi attributo**. Fornisci un **nome attributo** di `hf.Registrar.Roles`. Immetti un **valore attributo** di `*`. Puoi utilizzare questo pannello anche per creare un'identità che possa registrare solo determinati tipi di identità, come client o peer, o solo all'interno di una determinata affiliazione. Puoi anche creare un'identità che abbia la capacità di revocare un'identità e tutti i certificati emessi per l'identità. Puoi visualizzare un elenco completo degli attributi nella sezione [Registering a new identity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registering a new identity") nella guida per gli utenti della CA Fabric.

## Iscrizione di un'identità
{: #ibp-console-identities-enroll}

Puoi generare il certificato pubblico e la chiave privata per ogni identità che è stata registrata con la tua CA. Se hai registrato altre identità amministratore con la tua CA, puoi generare le chiavi per l'identità amministratore e poi facoltativamente includerla quando [crei l'MSP della tua organizzazione](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp).

Prima di eseguire l'iscrizione di un'identità, devi [impostare l'identità](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) in modo che possa gestire la CA. Normalmente si desidera impostare l'identità amministratore specificata quando si crea la CA. Puoi confermare che la tua CA è impostata su tale identità esaminando la pagina dei dettagli della CA e visualizzando l'ID di registrazione dell'identità al momento attiva accanto al nome della CA. Dopo aver confermato che l'identità impostata è la tua identità amministratore, fai clic su **Iscrivi identità** sul menu di overflow dell'utente per generare il certificato e la chiave per tutti gli utenti registrati con la CA.

- Immetti il segreto di registrazione (`Enroll secret`) dell'utente.
- Nel passo successivo, vengono visualizzate le chiavi generate.
  - La chiave pubblica viene visualizzata nel campo **Certificato**. Questo certificato viene indicato anche come certificato di iscrizione, certificato di firma o signCert. Devi esportare il signCert in un file nel tuo sistema locale in modo da poterlo utilizzare durante la creazione di un'applicazione client con l'estensione VSCode.
  - Puoi trovare la chiave privata corrispondente nel campo **Chiave privata**. Devi nuovamente esportare la chiave privata nel tuo sistema locale per utilizzarla con un'applicazione client creata con l'estensione VSCode.
  - Il certificato e la chiave privata creati facendo clic su **Iscrivi identità** vengono generati solo una volta e non vengono memorizzati dalla console o dal tuo browser. Facendo clic sul pulsante **Iscrivi identità** verrà conteggiato anche il numero massimo di iscrizioni che hai impostato per l'amministratore della CA. Come parte dell'iscrizione, devi memorizzare la coppia di chiavi scaricando l'identità nel tuo file system locale o aggiungendola al tuo portafoglio della console. Immetti un nuovo nome per questa coppia di chiavi pubblica e privata nel campo **Nome** per poterle recuperare.
- **Importante:** fai clic su **Esporta identità** per scaricare il certificato e la chiave nel tuo sistema locale come un singolo file nel formato JSON. Sei responsabile per la protezione e la gestione di queste chiavi.
- Fai clic su **Aggiungi identità al portafoglio** per aggiungere questi certificati al portafoglio della console. Puoi quindi trovare il nome e le chiavi di questa identità in un nuovo tile nella [scheda Portafoglio](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)

Puoi anche utilizzare il client CA Fabric o gli SDK Fabric per iscrivere le identità che hai creato nella console. La console ti fornisce tutte le informazioni necessarie per completare questi passi. Assicurati di aver salvato l'**ID di registrazione** e il **Segreto di registrazione** che hai specificato durante la registrazione.

## Utilizzo della tua CA TLS
{: #ibp-console-identities-tlsca}

La comunicazione all'interno della tua rete è protetta da certificati TLS. TLS crittografa la comunicazione tra i nodi e tra i nodi e le applicazioni. L'utilizzo di TLS impedisce ad aggressori di interrompere la comunicazione tra i tuoi nodi o di leggere le transazioni inviate dalle tue applicazioni. Le chiavi e i certificati utilizzati per TLS sono diversi dai certificati utilizzati per firmare e convalidare le tue transazioni e vengono emessi da un'autorità di certificazione (CA) separata.

Ogni CA creata dalla console {{site.data.keyword.blockchainfull_notm}} Platform contiene una CA root e una CA TLS. Puoi visualizzare entrambe queste CA con lo stesso nome di visualizzazione nella scheda Nodi della tua console. Fai clic sul pannello di panoramica della tua CA e seleziona **Autorità di certificazione TLS** per gestire la CA TLS. La tua CA TLS ha lo stesso [processo di registrazione](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) e [di iscrizione](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) della tua CA root. Entrambe le CA vengono distribuite con lo stesso ID e segreto di amministratore della CA.

Ogni nodo peer od ordinante che distribuisci deve generare un certificato TLS pubblico. Quando crei un nodo utilizzando la console, ti viene chiesto di fornire un ID e un segreto di registrazione di un'identità registrata con la tua CA TLS. Per praticità, nell'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) viene utilizzata l'identità di amministratore della CA. Tuttavia, è consigliabile registrare un'identità univoca con la tua CA TLS per ciascun nodo. Il nodo utilizzerà questa identità per generare il suo certificato TLS durante la distribuzione. Questo certificato sarà richiesto da qualsiasi applicazione che deve comunicare con l'ordinante o il peer. Puoi trovare il certificato TLS di un nodo accedendo al pannello di panoramica del nodo e facendo clic su Impostazioni. Puoi anche trovare i certificati TLS dei tuoi peer e ordinanti nel profilo di connessione che è possibile scaricare [dalla scheda degli smart contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).

Quando crei un peer o un ordinante utilizzando la tua console, puoi anche utilizzare la CA TLS per specificare un nome di dominio aggiuntivo per ciascun nodo. Immetti il nuovo nome di dominio nel campo **Nome host CSR TLS** quando distribuisci il tuo ordinante o peer. Questo nome host verrà aggiunto all'elenco dei nomi comuni nel certificato TLS emesso sul tuo nodo.


## Scadenza dei certificati
{: #ibp-console-identities-expiration}

I certificati generati tramite le CA di {{site.data.keyword.blockchainfull_notm}} Platform 2.0 scadono dopo un anno. Il periodo di scadenza è lo stesso per i certificati generati utilizzando gli SDK Fabric, il client CA Fabric o la console. Se i certificati scadono, le tue applicazioni non possono più interagire con la rete e dovrai eseguire nuovamente l'iscrizione per generare i nuovi certificati. Se hai raggiunto il limite di iscrizioni di un utente, puoi registrare un nuovo utente e quindi effettuare l'iscrizione.

Puoi utilizzare la tua riga di comando per controllare la data di scadenza dei certificati. Per prima cosa, dovrai convertire i certificati che sono in base64 nel formato PEM immettendo il seguente comando sulla tua macchina locale:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

Immetti il seguente comando per visualizzare il certificato con codifica PEM in un formato leggibile:
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

Il certificato sarà simile al seguente esempio:

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

Puoi trovare la data di scadenza nella sezione **Validity** dopo `Not After:`. In questo esempio, il certificato scadrà il 28 novembre 2019.

## Memorizzazione delle identità nel tuo portafoglio della console
{: #ibp-console-identities-wallet}

Il portafoglio memorizza le identità e le chiavi utilizzate dalla console {{site.data.keyword.blockchainfull_notm}} Platform per gestire i nodi della tua rete. Devi aggiungere i tuoi amministratori di peer, ordinante e organizzazione a questo portafoglio prima di poter utilizzare la console per lavorare con i canali e gli smart contract. Puoi anche utilizzare il portafoglio per memorizzare opportunamente le identità che verranno utilizzate dalle tue applicazioni. Puoi utilizzare il portafoglio per esportarle in qualsiasi momento. Usa il riquadro di navigazione a sinistra per passare al pannello di panoramica del portafoglio. Puoi aggiungere, aggiornare ed esportare le identità da questo portafoglio usando la schermata di panoramica.

Il portafoglio è un componente della console e non un servizio separato. Memorizza le tue chiavi nell'archiviazione locale del browser che usi per accedere alla console invece del tuo file system locale. Se accedi alla tua console da un browser diverso o avvii una sessione di navigazione privata, non sarai in grado di accedere alle identità memorizzate nel portafoglio. **Si consiglia di esportare le tue identità di amministratore dalla console e di memorizzarle in un luogo sicuro**.
{:important}

### Aggiunta di identità
{: #ibp-console-identities-add}

Puoi aggiungere un'identità di amministratore al tuo portafoglio quando [crei l'MSP della tua organizzazione](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Una CA gestita dalla console può anche aggiungere un'identità al tuo portafoglio durante il [processo di iscrizione](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

Puoi utilizzare il pulsante **Aggiungi identità** nella schermata di panoramica per importare un'identità direttamente nel portafoglio. Facendo clic su questo pulsante si aprirà un pannello laterale che ti consente di aggiungere la coppia di chiavi pubblica e privata di un'identità.
- **Nome:** immetti un nome di identità che viene utilizzato solo come riferimento.
- **Certificato:** incolla la chiave pubblica dell'identità che hai generato tramite la tua CA.
- **Chiave privata:** incolla la chiave privata dell'identità che hai generato tramite la tua CA.


Puoi anche aggiungere un'identità caricando un file JSON nel seguente formato. Puoi inoltre utilizzare il pulsante **Carica JSON** per caricare più identità alla volta.

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Se hai iscritto un'identità utilizzando il client CA Fabric o gli SDK Fabric, le tue chiavi devono essere convertite dal formato PEM al formato base64. Puoi convertire i certificati in formato base64 immettendo il seguente comando sulla tua macchina locale:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

### Visualizzazione e aggiornamento delle identità
{: #ibp-console-identities-update-identities}

Dalla scheda **Portafoglio**, fai clic su un tile per visualizzare, aggiornare o rimuovere un'identità dal portafoglio. Potrebbe essere necessario aggiornare le tue identità se i loro certificati sono scaduti e devono essere emesse nuove chiavi dalla CA. Puoi anche utilizzare questa scheda per eliminare le chiavi dalla console e dal sistema locale.

Facendo clic su un'identità si apre un pannello laterale che visualizza le sue chiavi pubbliche e private in formato base64. Fai clic su **Esporta** per scaricare i certificati dell'identità nel tuo file system locale. Fai clic su **Aggiorna** per modificare il nome dell'identità nel portafoglio o incollare una nuova serie di chiavi nel pannello. Fai clic su **Rimuovi** quando non ha più bisogno di utilizzare questa identità e vuoi eliminare le sue chiavi.

## Associazione di identità
{: #ibp-console-identities-associate-admin}

Devi fornire la chiave pubblica degli amministratori dell'organizzazione e del nodo [alla definizione MSP della tua organizzazione](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). I nodi o i canali creati dalla console utilizzano i certificati dalla definizione MSP per verificare chi sia un amministratore valido. Di conseguenza, la stessa coppia di chiavi pubblica e privata che hai usato per aggiungere un certificato di amministratore alla definizione MSP deve essere memorizzata nel tuo portafoglio della console.

Quando utilizzi la console per creare un ordinante o un peer, troverai un pannello **Associa identità**. Seleziona un'identità nel portafoglio il cui certificato si trova anche all'interno della definizione MSP della tua organizzazione. Dovrai anche selezionare un'identità di amministratore nella sezione **Associa identità** quando crei o modifichi un canale. Ciò consentirà alla tua console di sapere quale identità utilizzare quando comunica con i tuoi peer, ordinanti e con il consorzio del servizio ordini.

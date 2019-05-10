---

copyright:
  years: 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Gestione di organizzazioni
{: #ibp-console-organizations}

Puoi utilizzare la console {{site.data.keyword.blockchainfull}} Platform per creare una definizione di organizzazione formale nota come MSP (Membership Services Provider). La definizione MSP della tua organizzazione consente ad altri membri del consorzio blockchain di verificare l'identità dei tuoi nodi e delle tue applicazioni. La tua definizione MSP contiene anche i certificati di amministrazione della tua organizzazione.

Puoi anche utilizzare la console per gestire quali organizzazioni sono membri della tua rete. L'amministratore del servizio ordini può utilizzare la scheda delle organizzazioni per aggiungere membri al [consorzio](/docs/services/blockchain/glossary.html#glossary-consortium) blockchain. I membri del consorzio possono quindi utilizzare la console per aggiungere i membri a canali nuovi o esistenti.

**Gruppi di destinatari:** questo argomento è pensato per gli operatori di rete che sono responsabili della creazione, del monitoraggio e della gestione della rete blockchain.

## Descrizione degli MSP
{: #console-organizations-about-msp}

{{site.data.keyword.blockchainfull_notm}} Platform è basato su Hyperledger Fabric e crea reti blockchain autorizzate. I partecipanti devono essere noti alla rete prima di poter inoltrare le transazioni e interagire con gli asset nel libro mastro. Il Fabric riconosce l'identità attraverso un gruppo di organizzazioni invitate, noto come consorzio. Le organizzazioni del consorzio sono in grado di emettere credenziali valide per i loro membri e consentire loro di diventare partecipanti alla rete. I partecipanti possono quindi utilizzare i nodi blockchain e inoltrare le transazioni dalle applicazioni client.

Ogni organizzazione del consorzio deve utilizzare la propria CA (Certificate Authority), nota come CA root. Questa Certificate Authority (o le sue CA intermedie) crea tutte le identità appartenenti alla tua organizzazione ed emette per ogni identità una chiave pubblica e una privata. Queste chiavi sono firmate dalla CA e utilizzate dai membri della tua organizzazione per firmare e verificare le loro azioni. L'adesione al consorzio consente ad altre organizzazioni di riconoscere la firma delle tue CA e di verificare che i tuoi peer e le tue applicazioni siano partecipanti validi. Per ulteriori informazioni sull'adesione in Hyperledger Fabric, vedi l'[argomento relativo al concetto di adesione![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Adesione") nella documentazione di Fabric.

Prima di potersi unire a un consorzio, la tua organizzazione deve creare una definizione di organizzazione nota come **MSP (Membership Services Provider)**. L'MSP contiene le seguenti informazioni:
- Un certificato firmato dalla tua **CA (Certificate Authority, Autorità di certificazione) root**. Questo certificato viene utilizzato per verificare l'identità dei tuoi nodi, dei tuoi canali e delle tue applicazioni.
- Un certificato firmato dalla tua **CA TLS**. Un certificato TLS root consente ai tuoi peer di partecipare al gossip tra organizzazioni, che è necessario per avvalersi delle funzioni di [raccolte di **dati privati**](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) e [rilevamento dei servizi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Rilevamento dei servizi") di Hyperledger Fabric.
- L'**ID MSP**. L'ID MSP è il nome formale della tua organizzazione all'interno del consorzio. Devi ricordarti l'ID MSP per i tuoi nodi e le tue applicazioni.
- I **certificati di amministrazione** dalle tue identità **Amministratore peer** e **Amministratore organizzazione**. Questi certificati vengono passati al servizio ordini e vengono utilizzati per verificare quali sono le identità nella tua organizzazione a cui è consentito creare o modificare canali. Quando utilizzi la tua console per creare un ordinante o un peer, i certificati di amministrazione all'interno MSP vengono distribuiti all'interno del nuovo nodo. Questi certificati possono essere quindi utilizzati per gestire i peer o gli ordinanti dalla tua console o da un'applicazione client.

## Gestione degli MSP nella console

Vai alla scheda **Organizzazioni**. Puoi utilizzare questa scheda per [creare una definizione di MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp) utilizzando una CA (Certificate Authority, Autorità di certificazione) che esiste nella tua console. Puoi anche utilizzare questa scheda per [importare un MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) che era stato creato da un'altra organizzazione.

Puoi visualizzare tutti gli MSP che hai creato o importato in **Organizzazioni disponibili**. Puoi utilizzare le definizioni di MSP nella scheda Organizzazioni per funzioni importanti all'interno della tua console:
- Se stai creando dei nodi peer od ordinante, l'MSP della tua organizzazione viene utilizzato per fornire i certificati di amministrazione al nuovo nodo.
- Se sei l'amministratore di un nodo di ordine, puoi utilizzare gli MSP per [aggiungere nuove organizzazioni al consorzio](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).
- Se sei un membro del consorzio, puoi importare gli MSP di altri membri del consorzio nella tua console e aggiungere quindi i membri ai canali nuovi o esistenti.

## Creazione di un MSP per la tua organizzazione
{: #console-organizations-create-msp}

Utilizza la scheda **Organizzazioni** per generare una definizione di MSP per la tua organizzazione. Quando fai clic su **Crea MSP**, viene aperto un pannello laterale che ti consente di immettere tutte le informazioni necessarie: la CA root, l'ID MSP e gli amministratori della tua organizzazione.

- Utilizza la sezione **Dettagli MSP** del pannello per selezionare l'ID MSP della tua organizzazione. Questo ID è il nome formale che verrà utilizzato dagli altri membri della rete per fare riferimento alla tua organizzazione. **Salva il tuo ID MSP.**

- Utilizza la sezione **Dettagli autorità di certificazione (CA) root** per selezionare la CA root della tua organizzazione. Questa è la CA che hai utilizzato (o utilizzerai) per creare tutte le identità nodo e applicazione che appartengono alla tua organizzazione. Se utilizzi delle CA intermedie, questa è la CA che hai utilizzato per creare queste CA. Seleziona la tua CA root dall'elenco di CA gestite utilizzando la tua console. Se hai creato una CA utilizzando la console, la selezione di una CA root creerà anche un certificato TLS root.

- Puoi anche utilizzare la sezione **Dettagli autorità di certificazione (CA) root** per generare uno dei certificati di amministrazione della tua organizzazione. Prima di creare la definizione di MSP della tua organizzazione, devi registrare i tuoi amministratori di organizzazione e nodo presso la tua CA root. Devi quindi completare la seguente procedura per utilizzare queste identità per gestire la tua rete:

  1. Crea una coppia composta da una chiave pubblica e una privata per ogni identità amministratore.
  2. Fornisci la chiave pubblica (certificato) di ciascuna identità amministratore nella definizione di MSP.
  3. Aggiungi l'identità al portafoglio della tua console. I nodi o i canali creati dalla console utilizzano i certificati dall'MSP per controllare chi è un amministratore valido. Di conseguenza, la stessa coppia di chiave pubblica e chiave privata che hai utilizzato per aggiungere un certificato di amministrazione all'MSP deve essere conservata all'interno del portafoglio della tua console.

  Puoi utilizzare il pannello **Crea definizione MSP** per completare queste azioni per una delle tue identità amministratore. Dopo che hai selezionato la tua CA root, completa la seguente procedura nella sezione **Generare il certificato dell'amministratore dell'organizzazione**:
  1. Immetti l'ID di iscrizione e il segreto di iscrizione di un'identità amministratore registrata presso la tua CA root. Dopo che hai immesso l'ID di iscrizione e il segreto di iscrizione, scegli un nome per visualizzare l'identità nel tuo portafoglio della console.
  2. Fai clic su **Genera**. Questo genererà una nuova serie di chiavi pubbliche e private e aggiungerà automaticamente le chiavi al tuo portafoglio della console. Puoi quindi trovare la tua identità amministratore nel tuo portafoglio utilizzando il nome che hai selezionato in questo pannello. Queste chiavi vengono memorizzate solo nell'archiviazione locale del browser; pertanto, se cambi i browser, esse non saranno presenti nel tuo portafoglio della console. Dovrai esportare l'identità dal browser originale e importarla nel portafoglio della console del tuo nuovo browser.
  3. Fai quindi clic su **Esporta** per scaricare la coppia di chiavi sul tuo file system e proteggerla.

- La sezione **Amministratori (facoltativo)** del pannello laterale contiene le chiavi pubbliche dei tuoi amministratori. Il certificato che hai generato utilizzando la sezione **Generare il certificato dell'amministratore dell'organizzazione** è disponibile nella prima riga del campo **certificato amministratore**. Se vuoi utilizzare più identità amministratore per gestire la tua rete, puoi incollare i certificati di amministratori del nodo o dell'organizzazione aggiuntivi nei campi **certificato amministratore**.

Poiché i tuoi certificati di amministrazione vengono passati ai tuoi nodi e ai tuoi canali utilizzando la definizione di MSP, devi assicurarti che ciascuno dei tuoi certificati di amministrazione di nodo e organizzazione sia memorizzato nell'MSP. Quando utilizzi la console per creare un ordinante, un peer o un canale, devi associare (mediante l'opzione **Associa**) una delle identità che hai esportato nel tuo portafoglio della console con i certificati di amministrazione che erano stato forniti alla definizione dell'MSP . Quando riscontri una sezione o un pannello **Associa identità**, seleziona un'identità che hai generato e salvato nel portafoglio durante la creazione della definizione dell'MSP.

Dopo che hai selezionato la tua CA root, l'ID MSP e creato i tuoi certificati di amministrazione, fai clic su **Crea definizione dell'MSP** per creare la definizione dell'MSP. È ora elencata come un'organizzazione nella scheda Organizzazioni. Utilizzerai la definizione dell'MSP quando distribuisci i tuoi nodi e ti unisci a un consorzio blockchain.

## Creazione manuale di un file JSON MSP
{: #console-organizations-build-msp}

**Questa opzione è per gli utenti avanzati che hanno dimestichezza sul modo in cui i certificati vengono utilizzati nella gestione delle identità della blockchain.**

Se preferisci utilizzare i certificati per il tuo peer od ordinante da una **CA esterna**, una che non è ospitata da {{site.data.keyword.IBM_notm}}, devi creare un file JSON di definizione MSP che rappresenta la definizione MSP dell'organizzazione peer od ordinante. Crea un file JSON utilizzando il seguente formato:

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name**: specifica qualsiasi nome da utilizzare per identificare questa definizione MSP nella console.
- **organization_id**: specifica un id che viene utilizzato per rappresentare questo MSP internamente nella console.
- **root_certs**: (Facoltativo) Incolla un array che contiene uno o più certificati root dalla CA esterna in formato `base64`. Devi fornire un certificato root CA o un certificato CA intermedio; puoi anche fornire entrambi.
- **intermediate_certs**: (facoltativo) incolla una array che contiene uno o più certificati dalla CA intermedia esterna in formato `base64`. Devi fornire un certificato root CA o un certificato CA intermedio; puoi anche fornire entrambi.
- **admins**: incolla il certificato di firma dell'amministratore dell'organizzazione in formato `base64`.
- **tls_root_certs**: (facoltativo) incolla un array che contiene uno o più certificati root dalla CA TLS in formato `base64`. Devi fornire un certificato root CA TLS esterna o un certificato CA TLS intermedia esterna; puoi anche fornire entrambi.
- **tls_intermediate_certs**: (facoltativo) Incolla un array che contiene uno o più certificati dalla CA TLS intermedia in formato `base64`. Devi fornire un certificato root CA TLS esterna o un certificato CA TLS intermedia esterna; puoi anche fornire entrambi.  

Nella definizione MSP sono disponibili anche i seguenti campi aggiuntivi che non sono però obbligatori.
- **organizational_unit_identifiers**: un elenco delle unità organizzative (OU, Organizational Unit) che i membri validi di questo MSP devono includere nel loro certificato X.509. Questo è un parametro di configurazione facoltativo che viene utilizzato quando più organizzazioni si avvalgono della stessa radice di attendibilità e delle stesse CA intermedie e che hanno riservato un campo OU per i loro membri. Un'organizzazione è spesso divisa in più unità organizzative (OU, Organizational Unit), ciascuna delle quali ha uno specifico insieme di responsabilità. Ad esempio, l'organizzazione ORG1 può avere sia ORG1-MANUFACTURING che ORG1-DISTRIBUTION OUs per riflettere queste linee di business separate. Quando una CA emette i certificati X.509, il campo OU nel certificato specifica la linea di business a cui appartiene l'identità. Per ulteriori informazioni, vedi questo argomento nella documentazione di Fabric sulla [classificazione delle identità ![Icona link esterno](../images/external_link.svg "Icona link esterno") ](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "Classificazione delle identità").  
- **fabric_node_OUs**: OU specifiche per Fabric che abilitano la classificazione delle identità. Le `NodeOU` contengono informazioni su come distinguere client, peer e ordinanti in base alla loro OU. Se viene implementato il controllo, impostando Enabled su true, l'MSP considererà l'identità valida se è un'identità di un `client`, un `peer` o un ordinante (`orderer`). Un'identità deve avere solo una di queste OU speciali. Vedi questo argomento per un esempio di come specificare la `fabric_node_OU` in un MSP nella [documentazione di Fabric Service Discovery ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query).
- **revocation_list**: un elenco di certificati che non sono più validi. Per le identità basate su X.509, questi identificativi sono coppie di stringhe note come SKI (Subject Key Identifier) e AKI (Authority Access Identifier) e sono controllati ogni volta che si fa uso del certificato X.509 per garantire che non sia stato revocato. Vedi questo argomento nella documentazione di Fabric relativo agli [elenchi di revoca di certificati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "revoca di un certificato o di un'identità").

Ad esempio, il tuo file JSON sarà simile a:

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

Nota: tutti i certificati devono essere codificati in formato base64.
{:important}

Puoi convertire il contenuto del tuo file certificato, `<cert.pem>`, dal formato `PEM` a una stringa in formato base64 eseguendo questo comando sulla tua macchina locale:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}

Salva questa definizione come un file `JSON`.  

Hai creato una definizione MSP, che definisce l'organizzazione per i tuoi nodi peer od ordinante, che utilizza i certificati da una CA esterna. Puoi ora ritornare alle istruzioni che descrivono [come utilizzare i certificati da una CA esterna con il tuo peer od ordinante](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

## Importazione di un MSP
{: #console-organizations-import-msp}

Solo l'amministratore ordinante può aggiungere nuove organizzazioni al consorzio. Se sei l'amministratore ordinante, dovrai raccogliere le definizioni di MSP di tutte le organizzazioni che sono state invitate al consorzio e importare gli MSP nella console. Puoi quindi aggiungere gli MSP al servizio ordini utilizzando il nodo ordinante.

Dopo che l'amministratore ha creato una definizione di MSP, può utilizzare la scheda organizzazioni per scaricare l'MSP in formato JSON sul suo filesystem locale. Può quindi inviarti il file JSON MSP in un'operazione fuori banda. Vai alla scheda **Organizzazioni** e utilizza **Importa definizione dell'MSP** per importare il file MSP nella tua console. Dopo che la definizione dell'MSP è visibile nella sezione **Organizzazioni disponibili**, puoi andare al tuo nodo ordinante per [aggiungere l'organizzazione al consorzio](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Aggiunta di un'organizzazione a un consorzio
{: #console-organizations-add-consortium}

Il consorzio di organizzazioni è ospitato dal servizio ordini.

Se sei l'amministratore del servizio ordini, puoi utilizzare la console per aggiungere un'organizzazione al consorzio. Vai alla scheda **Nodi** e fai clic sul nodo di ordine. Nel pannello del nodo di ordine, sotto **Membri del consorzio**, fai clic su **Aggiungi organizzazione**. Questo aprirà un pannello laterale che ti consentirà di operare una selezione dall'elenco di definizioni di MSP disponibili che hai [importato nella tua scheda delle organizzazioni](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). Puoi anche utilizzare l'opzione **Carica JSON** per importare il file di definizione dell'MSP creato da un'altra organizzazione direttamente.

## Creazione e modifica di un canale
{: #console-organizations-create-channel}

Dopo essere stata aggiunta al consorzio, un'organizzazione può utilizzare il servizio ordini per creare un nuovo canale o poter essere aggiunta a un canale esistente. Le informazioni che ti consentono di partecipare a un canale, come l'unione dei tuoi peer al canale, l'istanziazione di smart contract e l'inoltro di transazioni, vengono fornite utilizzando le definizioni di MSP.

Dopo che la tua organizzazione è stata aggiunta a un consorzio, puoi creare un canale utilizzando la seguente procedura:

1. Importa il nodo di ordine che ospita il consorzio nella tua console. Non devi essere un amministratore del nodo di ordine; ma devi fornire il nome del nodo di ordine e le informazioni sull'endpoint nella tua console.
2. Importa gli MSP delle organizzazioni che desideri aggiungere al nuovo canale nella tua console utilizzando la scheda **Organizzazioni**. **Nota** che le organizzazioni devono essere aggiunte al consorzio prima di poter essere aggiunte a un canale.
3. Vai alla scheda **Canali** e fai clic su **Crea canale**. Questo aprirà un pannello laterale che ti consente di specificare il nome canale, l'adesione e le politiche del canale. Puoi aggiungere qualsiasi organizzazione che è stata aggiunta al consorzio al nuovo canale.

Per ulteriori informazioni su questi passi, vedi la sezione relativa alla [creazione di un canale](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) nell'esercitazione **Crea una rete**.

### Aggiornamento di un MSP in una definizione di canale
{: #console-organizations-update-channel}

Nel corso del tempo potresti dover aggiornare i certificati in una definizione MSP che è già associata a un canale. Quando si verifica tale situazione, segui questa procedura per aggiornare la definizione MSP di un'organizzazione nel canale:  

1. Passa alla scheda **Canali** nella tua console.
2. Fai clic sul canale che contiene l'MSP dell'organizzazione che vuoi aggiornare e aprilo.
3. Fai clic sulla scheda **Dettagli del canale**.
4. Fai clic sul tile del membro del canale associato che vuoi aggiornare.
5. Se non hai ancora importato la definizione MSP aggiornata nella console, puoi caricare qui il file. **Nota:** questa azione non aggiornerà la definizione MSP associata nella scheda Organizzazioni. Se hai già aggiornato la definizione MSP nella scheda Organizzazioni della console, puoi selezionarla dall'elenco a discesa.

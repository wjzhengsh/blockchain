---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Distribuzione delle reti di business sul piano Starter ed Enterprise
{: #deploying-a-business-network}

{{site.data.keyword.IBM}} non fornisce supporto per le reti che utilizzano Hyperledger Composer in produzione, compresi la CLI Composer, le API JavaScript, il server REST e Web Playground.
{:note}

Le [reti di business](/docs/services/blockchain/glossary.html#glossary-business-network) possono essere sviluppate e distribuite sulle reti del piano Starter ed Enterprise utilizzando l'ambiente per gli sviluppatori {{site.data.keyword.blockchainfull_notm}} Platform e il set di strumenti per gli sviluppatori di Hyperledger Composer.
{:shortdesc}

Utilizzando l'ambiente per gli sviluppatori, puoi modellare e testare rapidamente le reti di business blockchain e distribuirle a una rete del piano Starter o Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform.

## Distribuzione di una rete di business sul piano Starter
{: #deploying-a-business-network-starter-plan}

### Prima di cominciare
{: #deploying-a-business-network-before-begin}

Assicurati di aver letto le [Informazioni sul piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about) e di aver creato una rete del piano Starter seguendo le istruzioni in [Introduzione a piano Starter](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).

Assicurati di aver installato Node v8.9 o superiore, npm v5.x e Hyperledger Composer:

- Se la tua rete è alla versione 1.2 di Fabric, utilizza Hyperledger Composer v0.20.x.
- Se la tua rete è alla versione 1.1 di Fabric, utilizza Hyperledger Composer v0.19.x.

Puoi trovare la tua versione Fabric aprendo la [finestra Preferenze della rete](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) nel tuo Monitoraggio della rete.

### Passo uno: Richiamo del segreto di amministrazione (admin secret)
{: #deploying-a-business-network-retrieve-admin-secret}

1. Dalla schermata di panoramica di piano Starter, fai clic su **Profilo connessione** e scarica. Rinomina questo file con 'connection-profile.json'.

2. Sposta questo file nella stessa directory del tuo file `.bna`.

3. All'interno del profilo di connessione, scendi finché non vedi 'registrar'. All'interno di 'registrar', in 'enrollId' c'è una proprietà **enrollSecret**. Richiama il segreto e salvane una copia.

    ![Richiamo del segreto di amministrazione](images/get_enroll_secret.gif "Richiamo del segreto di amministrazione ")

### Passo due: Creazione di una scheda dell'Autorità di certificazione
{: #deploying-a-business-network-CA-card}

Il segreto richiamato nel passo precedente sarà utilizzato per creare una scheda di rete di business per l'Autorità di certificazione (CA, Certificate Authority). La scheda di CA verrà quindi importata e verrà utilizzata per scambiare il **enrollSecret** per i certificati validi dall'Autorità di certificazione di piano Starter.

1. Utilizzo del **enrollSecret** annotato dal passo uno, esegui questo comando per creare la scheda di rete di business CA:

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  Sostituisci `enrollSecret` nel comando precedente con il segreto di amministrazione (admin secret) richiamato dal profilo di connessione.

2. Importa la scheda utilizzando il seguente comando:

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. Ora che la scheda è stata importata, è possibile utilizzarla per scambiare il **enrollSecret** per i certificati validi dall'Autorità di certificazione (CA, Certificate Authority). Esegui questo comando per richiedere certificati dall'Autorità di certificazione.

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  Sostituisci `enrollSecret` nel comando precedente con il segreto di amministrazione (admin secret) richiamato dal profilo di connessione. Il comando `composer identity request` crea una directory `credentials` che contiene i file `.pem` di certificato.

### Passo tre: aggiunta di certificati all'istanza piano Starter
{: #deploying-a-business-network-add-certs-to-starter-plan}

I certificati devono essere aggiunti alla rete piano Starter. Per praticità, puoi aggiungerli utilizzando il Monitoraggio della rete di {{site.data.keyword.blockchainfull_notm}} Platform. I certificati devono essere aggiunti; i peer devono essere quindi riavviati e i certificati devono essere sincronizzati nel canale. Il certificato necessario si trova nel file `admin-pub.pem` che è stato generato dal comando precedente, che è una directory `credentials`.

1. Nel Monitoraggio della rete del piano Starter, fai clic sulla scheda **Membri**, su **Certificati** e quindi su **Aggiungi certificato**. Passa alla tua directory `credentials` e copia e incolla i contenuti del file `admin-pub.pem` nella casella del certificato. Invia il certificato e riavvia i peer. Nota: il riavvio dei peer necessita di un minuto.

    ![Aggiungi certificati](images/add_cert.gif "Aggiungi certificati")

2. Successivamente, i certificati devono essere sincronizzati nel canale. Fai clic sulla scheda **Canali**, poi sul pulsante **Azioni**, su **Sincronizza certificato** e **Invia**.

    ![Sincronizza certificati](images/sync_cert.gif "Sincronizza certificati")

### Passo quattro: Creazione di una scheda di rete di business di amministrazione
{: #deploying-a-business-network-create-admin-card}

Ora che i certificati corretti sono stati sincronizzati con i peer, è possibile creare schede di rete di business che hanno le autorizzazioni per installare il runtime Hyperledger Composer e avviare il chaincode.

1. Crea una scheda di amministrazione con i ruoli di amministratore canale e amministratore peer utilizzando il seguente comando:

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   Questa scheda verrà utilizzata per distribuire una rete di business a piano Starter.

2. Importa la scheda creata nel passo precedente utilizzando il seguente comando:

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### Passo cinque: Installazione e avvio della rete di business
{: #deploying-a-business-network-install-start-network}

Successivamente, la scheda creata nel passo precedente può essere utilizzata per installare e avviare una rete di business. Per questa guida, installeremo l'esempio di rete vehicle manufacturing, utilizza questo esempio o installa la tua rete, ma assicurati di modificare i nomi della rete di business specificati nei comandi. Il comando per avviare una rete di business creerà anche una scheda. Per piano Starter, questa scheda deve essere eliminata; il comando di esempio fornito denomina questa scheda come `delete_me.card` e può pertanto essere distinta facilmente.

1. Installa il runtime Hyperledger Composer con il seguente comando:

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   Prendi nota del numero di versione della rete di business che viene restituito quando esegui questo comando. Sarà necessario nel prossimo passo.

2. Avvia la rete di business con il seguente comando. Se ricevi un errore, attendi un minuto e riprova. Utilizza il numero di versione dall'ultimo passo dopo l'opzione `-V`.

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Elimina la scheda di rete di business denominata `delete_me.card`.

4. Crea una nuova scheda di rete di business e fai riferimento ai certificati richiamati in precedenza con il seguente comando:

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. Importa la scheda di rete di business con il seguente comando:

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

La rete di business viene ora distribuita all'istanza del piano Starter.

### Passo sei: Esecuzione del ping della rete di business per verificarne la corretta esecuzione
{: #deploying-a-business-network-ping-business-network}

Immetti il seguente comando per eseguire il ping della rete di business:

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Per visualizzare i log del chaincode, fai clic su **Canali** e seleziona quindi il tuo canale. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> Fai clic sulla scheda **Chaincode**. Estendi la riga del chaincode e fai clic sul pulsante **JSON** o **Log**.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

La rete di business è stata ora distribuita correttamente all'istanza del piano Starter.

## Distribuzione di una rete di business sul piano Enterprise
{: #deploying-a-business-network-to-enterprise-plan}

Gli strumenti per gli sviluppatori di {{site.data.keyword.blockchainfull_notm}} Platform ti aiutano a creare una BND (**Business Network Definition**) che può quindi essere impacchettata in un archivio di rete di business (`.bna`). L'ambiente per gli sviluppatori ti consente di distribuire i file `.bna` a una {{site.data.keyword.blockchain}} locale o cloud per lo sviluppo e la condivisione.

Questa esercitazione di occupa del passo successivo di un ciclo di vita della rete di business, che consiste nell'attivare la tua rete di business distribuendo il `.bna` a {{site.data.keyword.blockchainfull_notm}} Platform piano Enterprise.

### Prima di cominciare
{: #deploying-a-business-network-enterprise-plan-before-begin}

Assicurati di avere installato l'ambiente per gli sviluppatori {{site.data.keyword.blockchainfull_notm}} e di essere a tuo agio con lo sviluppo e la distribuzione delle reti di business. Le istruzioni relative alla scrittura di reti di business sono disponibili nella [documentazione di Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Devi accedere a un'istanza piano Enterprise di {{site.data.keyword.blockchainfull_notm}} e di aver prima creato i tuoi peer. Per ulteriori informazioni su {{site.data.keyword.blockchainfull_notm}} Platform piano Enterprise, vedi la [panoramica di piano Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

### Passo uno: Creazione di un profilo di connessione per IBM Blockchain Platform
{: #deploying-a-business-network-create-connection-profile}

1. Crea una directory per memorizzare i tuoi dettagli della connessione, ad esempio:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Ogni profilo di connessione deve contenere un file `connection.json`. Crea una nuova directory in `.composer-connection-profiles`, in questo caso `bmx-hlfv11`. Questo sarà il nome del profilo che userai quando lavorerai con Hyperledger Composer e {{site.data.keyword.blockchainfull_notm}} Platform.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Dovresti avere ora la seguente struttura di directory:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

### Passo due: Richiamo del tuo profilo di connessione
{: #deploying-a-business-network-retrieve-connection-profile}

1. Dal tuo dashboard {{site.data.keyword.blockchainfull_notm}} Platform , seleziona **Panoramica**, poi **Profilo connessione** e il pulsante **Scarica** per richiamare il profilo di connessione.

2. Salva il profilo di connessione nella struttura di directory creata nel passo precedente. Assegna il nome **connection.json**.

### Passo tre: Aggiunta delle informazioni sul canale
{: #deploying-a-business-network-add-channel-information}

1. Aggiungi il valore di canale in `connection.json` in modo che corrisponda al canale in cui intendi creare e distribuire la tua rete di business. Nel template del profilo di connessione di esempio fornito, l'elemento channels è il seguente: `"channels": {},`.

### Passo quattro: Preparazione dei tuoi peer
{: #deploying-a-business-network-prepare-peers}

Questo passo **DEVE** essere eseguito prima di creare il canale a cui distribuire una rete di business. Se questo passo viene eseguito dopo la creazione del canale, una rete di business distribuita **potrebbe non avviarsi**.
{:note}

Nel documento Profilo connessione in **certificateAuthorities** c'è un attributo **registrar** che contiene gli attributi per **enrollId** e **enrollSecret** nel seguente formato:

  ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
  ```

1. Crea una scheda di rete di business per la CA utilizzando il seguente comando:

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Assicurati di eseguire il comando nella stessa directory del tuo profilo di connessione.

2. Importa la scheda di rete di business utilizzando il seguente comando:

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. Dopo aver importato la scheda, la puoi utilizzare per acquisire i certificati dalla CA (Certificate Authority) utilizzando il seguente comando:

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    Il comando `composer identity request` crea una directory delle credenziali che contiene i file di certificato pertinenti.

4. Seleziona **Membri** dal menu di navigazione e seleziona quindi l'opzione di menu **Certificati** e fai clic sul pulsante **Aggiungi certificato**.

5. Immetti un nome univoco per questo certificato nel campo **Nome**; questo nome non può includere trattini o segni meno.

6. Apri il file `admin-pub.pem` creato in precedenza e copia il contenuto di questo file nel campo **Chiave** e premi il pulsante **Invia**.

7. Utilizza l'interfaccia utente per arrestare i peer e quindi per avviarli.

8. Il certificato dovrebbe ora essere presente nell'elenco di certificati.

### Passo cinque: Creazione del tuo canale
{: #deploying-a-business-network-create-your-channel}

1. Seleziona **Canali** dal menu di navigazione nel pannello di sinistra e fai clic sul pulsante **Nuovo canale**.

2. Immetti un nome canale e una descrizione facoltativa e fai clic su **Avanti**. Nota: il nome canale deve corrispondere al nome da te specificato nel profilo di connessione per l'attributo del canale.

3. Concedi le autorizzazioni come necessario e fai clic su **Avanti**.

4. Seleziona la politica del numero di operatori che devono accettare gli aggiornamenti del canale e inoltra la richiesta.

5. Dovresti ora essere indirizzato alla sezione **Notifiche** dove dovrebbe esserci una nuova richiesta da revisionare. Fai clic sul pulsante **Esamina richiesta**.

6. Fai clic sul pulsante **Accetta**; verrai riportato alla sezione **Notifiche** dove puoi vedere che la richiesta può ora essere inoltrata. Fai clic sul pulsante **Invia richiesta** per aprire la finestra di dialogo di inoltro e fai quindi clic sul pulsante **Invia**. Il nuovo canale è stato creato.

7. Seleziona **Canali** dal menu di navigazione. Il nuovo canale è presente nell'elenco di canali e dovrebbe avere “Non è stato ancora aggiunto alcun peer”. Fai clic sul menu delle azioni accanto ad esso e seleziona **Unisci peer**. Seleziona le caselle di spunta accanto ai peer che vuoi aggiungere e premi **Aggiungi selezionati**.

### Passo sei: Creazione di una nuova identità per amministrare la tua rete di business
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

Crea la scheda di rete di business utilizzando i certificati richiesti. Questa scheda di rete di business disporrà dell'autorizzazione necessaria per installare il chaincode nei peer che hanno il tuo certificato pubblico caricato e sarà un emittente per le autorità di certificazione.

1. Per creare la scheda, esegui questo comando:

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    Dove `bmx-hlfv11` è il nome del profilo da te precedentemente creato.

2. Dopo aver creato la scheda, importala utilizzando il seguente comando:

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    Ora siamo pronti a distribuire il tuo `.bna` a{{site.data.keyword.blockchainfull_notm}} Platform.


### Passo sette: Distribuzione della rete di business
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

Ora puoi distribuire il tuo file `.bna` a {{site.data.keyword.blockchainfull_notm}} Platform.

1. Per utilizzare la scheda creata nel passo precedente, devi prima installare e poi avviare la rete di business. Installa la rete di business utilizzando il seguente comando:

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. Dopo aver installato la rete di business, avviala utilizzando il seguente comando:

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Per controllare che la rete di business sia stata distribuita correttamente, crea un'identità e una scheda di rete di business associata che possono essere utilizzate per eseguire il ping della rete.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. Importa la scheda di rete di business utilizzando il seguente comando:

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. Esegui il ping della rete per controllare che sia in esecuzione:

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

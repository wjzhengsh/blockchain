---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Distribuzione di una rete di business su piano Enterprise
{: #deploying-a-business-network}

**IBM consiglia di utilizzare Hyperledger Composer esclusivamente per le demo e per i modelli di verifica (PoC, Proof of Concept). IBM non fornisce supporto per le reti che utilizzano Hyperledger Composer in produzione, compresi la CLI Composer, le API JavaScript, il server REST e Web Playground.**

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Gli strumenti per gli sviluppatori di {{site.data.keyword.blockchainfull}} Platform: Develop ti aiutano a creare una BND (**Business Network Definition**) che può quindi essere impacchettata in un archivio di rete di business (`.bna`). L'ambiente per gli sviluppatori ti consente di distribuire i file `.bna` a una {{site.data.keyword.blockchain}} locale o cloud per lo sviluppo e la condivisione.

Questa esercitazione di occupa del passo successivo di un ciclo di vita della rete di business, che consiste nell'attivare la tua rete di business distribuendo il `.bna` a {{site.data.keyword.blockchainfull_notm}} Platform piano Enterprise.

## Prima di cominciare

Assicurati di avere installato l'ambiente per gli sviluppatori {{site.data.keyword.blockchainfull_notm}} e di essere a tuo agio con lo sviluppo e la distribuzione delle reti di business. Le istruzioni relative alla scrittura di reti di business sono disponibili nella [documentazione di Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Devi accedere a un'istanza piano Enterprise di {{site.data.keyword.blockchainfull_notm}} e di aver prima creato i tuoi peer. Per ulteriori informazioni su {{site.data.keyword.blockchainfull_notm}} Platform piano Enterprise, vedi la [panoramica di piano Enterprise](./enterprise_plan.html).

## Passo uno: Creazione di un profilo di connessione per {{site.data.keyword.blockchainfull_notm}} Platform

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

## Passo due: Richiamo del tuo profilo di connessione

1. Dal tuo dashboard {{site.data.keyword.blockchainfull_notm}} Platform , seleziona **Panoramica**, poi **Profilo connessione** e il pulsante **Scarica** per richiamare il profilo di connessione.

2. Salva il profilo di connessione nella struttura di directory creata nel passo precedente. Assegna il nome **connection.json**.

## Passo tre: Aggiunta delle informazioni del canale

1. Aggiungi il valore di canale in `connection.json` in modo che corrisponda al canale in cui intendi creare e distribuire la tua rete di business. Nel template del profilo di connessione di esempio fornito, l'elemento channels è il seguente: `"channels": {},`.

## Passo quattro: Preparazione dei tuoi peer

**Nota**: questo passo **deve** essere eseguito prima che crei il canale a cui distribuire una rete di business. Se questo passo viene eseguito dopo la creazione del canale, una rete di business distribuita **non può essere avviata**.

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

## Passo cinque: Creazione del tuo canale

1. Seleziona **Canali** dal menu di navigazione nel pannello di sinistra e fai clic sul pulsante **Nuovo canale**.

2. Immetti un nome canale e una descrizione facoltativa e fai clic su **Avanti**. Nota: il nome canale deve corrispondere al nome da te specificato nel profilo di connessione per l'attributo del canale.

3. Concedi le autorizzazioni come necessario e fai clic su **Avanti**.

4. Seleziona la politica del numero di operatori che devono accettare gli aggiornamenti del canale e inoltra la richiesta.

5. Dovresti ora essere indirizzato alla sezione **Notifiche** dove dovrebbe esserci una nuova richiesta da revisionare. Fai clic sul pulsante **Esamina richiesta**.

6. Fai clic sul pulsante **Accetta**; verrai riportato alla sezione **Notifiche** dove puoi vedere che la richiesta può ora essere inoltrata. Fai clic sul pulsante **Invia richiesta** per aprire la finestra di dialogo di inoltro e fai quindi clic sul pulsante **Invia**. Il nuovo canale è stato creato.

7. Seleziona **Canali** dal menu di navigazione. Il nuovo canale è presente nell'elenco di canali e dovrebbe avere “Non è stato ancora aggiunto alcun peer”. Fai clic sul menu delle azioni accanto ad esso e seleziona **Unisci peer**. Seleziona le caselle di spunta accanto ai peer che vuoi aggiungere e premi **Aggiungi selezionati**.

## Passo sei: Creazione di una nuova identità per amministrare la tua rete di business

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


## Passo sette: Distribuzione della rete di business

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

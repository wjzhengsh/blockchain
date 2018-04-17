---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Sviluppare la rete
{: #develop-the-network}

Lo sviluppo di soluzioni blockchain utilizzando {{site.data.keyword.blockchainfull}} Platform si avvale del set di strumenti di sviluppo open source Hyperledger Composer. Hyperledger Composer utilizza un linguaggio di modellazione su misura, che è combinato con le transazioni JavaScript e le regole di controllo accessi per modellare completamente una rete di business blockchain _prima_ che tu esegua alcuna distribuzione a una rete blockchain reale.
{:shortdesc}

**Nota**: Starter Plan fornisce applicazioni di esempio che puoi apprendere e provare. Puoi eseguire le applicazioni di esempio in Starter Plan prima di sviluppare la tua rete di business personalizzata. Per ulteriori informazioni, vedi [Informazioni su Starter Plan](starter_plan.html).

Questa esercitazione spiega lo sviluppo di una soluzione {{site.data.keyword.blockchain}} che inizia con la creazione e la modellazione di una rete di business e la distribuisce quindi a una rete blockchain in esecuzione.

La seguente procedura descrive lo sviluppo di base di una soluzione {{site.data.keyword.blockchain}} ospitata su {{site.data.keyword.blockchainfull_notm}} Platform:

1. Crea la struttura di dati che conterrà la definizione di rete di business. La rete di business è definita in termini di asset, partecipanti e transazioni utilizzando il [linguaggio di modellazione](https://hyperledger.github.io/composer/latest/reference/cto_language).

2. Modella la rete di business definendo le classi asset, le classi partecipante, le classi transazione, le classi evento e le regole di controllo accessi che verranno utilizzate dopo la distribuzione.

3. Impacchetta la rete di business definita in un singolo file distribuibile.

4. Distribuisci la rete di business impacchettata in {{site.data.keyword.blockchainfull_notm}} Platform. Dopo che la tua rete di business è stata distribuita, è possibile generare un server REST per consentire l'integrazione con le applicazioni esterne.

## Prima di cominciare

Assicurati di avere installato gli strumenti di sviluppo di [{{site.data.keyword.blockchainfull_notm}}: Develop](./develop_install.html).

## Passo uno: Creazione della struttura di rete di business

Un concetto chiave per lo sviluppo di soluzioni {{site.data.keyword.blockchain}} è la **BND (Business Network Definition)**. La BND definisce il modello di dati, la logica di transazione e le regole di controllo accessi per la tua soluzione blockchain. Per creare una BND, devi creare una struttura di progetto adatta su disco.

Il modo più facile per iniziare consiste nell'utilizzare il generatore Yeoman per creare una rete di business di base. Il generatore Yeoman crea una directory che contiene tutti i componenti di una rete di business che possono essere modificati per il tuo specifico caso d'uso.

1. Usa Yeoman per creare una rete di business di base. Il seguente comando richiede un nome di rete di business, una descrizione, un nome dell'autore, l'indirizzo email dell'autore, la selezione della licenza e lo spazio dei nomi:

        yo hyperledger-composer:businessnetwork

2. Immetti `tutorial-network` per il nome della rete e le informazioni desiderate per la descrizione, il nome dell'autore e l'indirizzo email dell'autore.

3. Seleziona `Apache-2.0` come licenza.

4. Seleziona `org.acme.biznet` come spazio dei nomi.

## Passo due: Definizione della rete di business

Una rete di business è composta da asset, partecipanti, transazioni, regole di controllo accessi e, facoltativamente, eventi e query. La rete di business di base creata nel passo uno include un file modello (`.cto`) che contiene le definizioni di classe per tutti gli asset, tutti i partecipanti e tutte le transazioni nella rete di business. La rete di business di base contiene anche un documento di controllo accessi (`permissions.acl`) con le regole di controllo accessi di base, un file script (`logic.js`) che contiene le funzioni del processore delle transazioni e un file di pacchetto (`package.json`) che contiene i metadati di rete di business.

### Modellazione di asset, partecipanti e transazioni

Il primo documento da aggiornare è il file modello (`.cto`). Il file viene scritto utilizzando il [linguaggio di modellazione Hyperledger Composer](https://hyperledger.github.io/composer/latest/reference/cto_language). Il file modello contiene le definizioni di ciascuna classe si asset, transazione, partecipante ed evento. Estende implicitamente il modello di sistema descritto nella documentazione del linguaggio di modellazione.

1. Apri il file modello `org.acme.biznet.cto`.

2. Sostituisci il contenuto con queste informazioni:

        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }

3. Salva le tue modifiche al file `org.acme.biznet.cto`.


### Aggiunta di logica di transazione JavaScript

Nel file modello, era definita una transazione `Trade`, che specifica una relazione a un asset e un partecipante. Il file della funzione processore delle transazioni contiene la logica JavaScript per eseguire le transazioni definite nel file modello.

La funzione prevista della transazione `Trade` è semplicemente quella di accettare l'identificativo dell'asset `Commodity` in fase di negoziazione e l'identificativo del partecipante `Trader` da impostare come nuovo proprietario.

1. Apri il file script `logic.js` nella directory `lib`.

2. Sostituisci il contenuto con queste informazioni:

        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }

3. Salva le tue modifiche a `logic.js`.

### Aggiunta del controllo accessi

Il controllo accessi è una parte chiave di una rete di business. Tutte le reti di business devono avere un file di controllo accessi denominato `permissions.acl`. Ogni volta che un processo viene richiamato in una rete di business distribuita, l'elenco di controllo accessi viene controllato per garantire che l'identità o il partecipante che sta eseguendo il richiamo possa richiamare l'operazione.

Ai fini di questa esercitazione, imposta un semplice elenco di controllo accessi (ACL, Access Control List). Nota che esso non deve essere distribuito a un ambiente di produzione.

1. Crea un file `permissions.acl` nella directory `tutorial-network`.

2. Aggiungi le seguenti regole di controllo accessi a `permissions.acl`:

        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }

3. Salva le tue modifiche a `permissions.acl`.

## Passo tre: Impacchettamento della rete di business

Dopo che la rete di business è stata definita, deve essere impacchettata in un file di archivio di rete di business distribuibile (`.bna`).

1. Utilizzando la riga di comando, passa alla directory `tutorial-network`.

2. Dalla directory `tutorial-network`, esegui questo comando:

        composer archive create -t dir -n .

Dopo che hai eseguito il comando, viene creato un file di archivio di rete di business (`tutorial-network@0.0.1.bna`) nella directory `tutorial-network`.

## Passo quattro: Distribuzione della rete di business e generazione di un server REST

Distribuisci la rete di business sulla {{site.data.keyword.blockchainfull_notm}} Platform utilizzando [Starter Plan](./develop_starter.html) o [Enterprise Plan](./develop_enterprise.html).

Dopo che la tua rete di business è stata distribuita a {{site.data.keyword.blockchainfull_notm}} Platform, è possibile generare un server REST personalizzato. Il server REST esamina il contenuto della rete di business e crea chiamate API RESTful per gli asset, le transazioni e i partecipanti della rete. Le istruzioni per generare un server REST sono incluse nella [documentazione di Hyperledger Composer](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).

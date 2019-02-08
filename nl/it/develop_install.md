---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Installazione di un ambiente di sviluppo della rete di business
{: #installing-a-development-environment}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Attieniti alle seguenti istruzioni per ottenere gli strumenti di sviluppo {{site.data.keyword.blockchainfull}} Platform: per creare e testare reti di business. Per fornire flessibilità e per abilitare il numero massimo di scenari di sviluppo, test e distribuzione, gli strumenti di sviluppo vengono forniti come moduli npm, che sono controllati dalla riga di comando.

## Prima di cominciare

Assicurati di installare i seguenti prerequisiti:

- Ubuntu Linux 14.04 / 16.04 LTS (entrambi a 64 bit) o Mac OS 10.12
- Node v8.9 o superiore (la v9 non è supportata)
- npm v5.x
- git v2.9.x o superiore
- Python v2.7.x
- Un editor di codice a tua scelta, come VSCode.

Se stai utilizzando piano Enterprise, installa i seguenti prerequisiti aggiuntivi:

- Docker Engine v17.03 o superiore
- Docker-Compose v1.8 or superiore

Se stai usando VSCode, è disponibile un'estensione Hyperledger Composer VSCode [qui](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).

## Passo uno: Installazione degli strumenti di riga di comando

Gli strumenti di riga di comando includono **composer-cli**, il modulo di riga di comando {{site.data.keyword.blockchainfull_notm}} Platform: Develop principale; **generator-hyperledger-composer**, **composer-rest-server** e **Yeoman** sono moduli utilizzati per generare reti di business, server REST personalizzati e applicazioni Angular. La versione da installare dipende dalla versione dell'Hyperledger Fabric in esecuzione nel tuo piano Starter o Enterprise. Puoi trovare la tua versione di Fabric aprendo la [finestra Preferenze della rete](/docs/services/blockchain/v10_dashboard.html#network-preferences) nel tuo Monitoraggio della rete.

1. Per l'utilizzo con le nuove istanze del piano Starter che eseguono l'Hyperledger Fabric v1.2.1, installa **composer-cli** utilizzando il seguente comando:

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Per l'utilizzo con le istanze del piano Enterprise e Starter che eseguono l'Hyperledger Fabric v1.1, installa **composer-cli** utilizzando il seguente comando:

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Per l'utilizzo con le nuove istanze del piano Starter che eseguono l'Hyperledger Fabric v1.2.1, installa **composer-rest-server** utilizzando il seguente comando:

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Per l'utilizzo con le istanze del piano Enterprise e Starter che eseguono l'Hyperledger Fabric v1.1, installa **composer-rest-server** utilizzando il seguente comando:

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 Usa il modulo **composer-rest-server** per creare un server REST sulla tua macchina per presentare le tue reti di business come API RESTful.

3. Per l'utilizzo con le nuove istanze del piano Starter che eseguono l'Hyperledger Fabric v1.2.1, installa **generator-hyperledger-composer** utilizzando il seguente comando:

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Per l'utilizzo con le istanze del piano Enterprise e Starter che eseguono l'Hyperledger Fabric v1.1, installa **generator-hyperledger-composer** utilizzando il seguente comando:

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Usa **generator-hyperledger-composer** per generare le strutture di rete di business di base, i modelli e un'applicazione Angular.

4. Yeoman è uno strumento per generare le applicazioni che utilizza `generator-hyperledger-composer`:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Passo due: Installazione di un playground locale

Il playground Hyperledger Composer è un'interfaccia utente che può essere utilizzata come un ambiente simulato per testare una rete di business. Installa il playground utilizzando il seguente comando:

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## Facoltativo: Configura il tuo IDE

Puoi utilizzare il playground per sviluppare, modificare e testare reti di business. Tuttavia, se vuoi sviluppare una rete di business in un IDE, è disponibile un'estensione VSCode che aggiunge un'evidenziazione della sintassi per il linguaggio di modellazione Hyperledger Composer.

1. Installa VSCode da questo URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Apri VSCode, vai a Estensioni, quindi cerca e installa l'estensione Hyperledger Composer dal marketplace.


### Avvio e arresto di Hyperledger Fabric

La directory `fabric-tools` contiene una serie di script che controllano l'istanza Hyperledger Fabric. Puoi arrestare e avviare il runtime utilizzando `~/fabric-tools/stopFabric.sh` e `~/fabric-tools/startFabric.sh`.

`~/fabric-tools/teardownFabric.sh` richiederà che tu crei una nuova scheda PeerAdmin al successivo avvio dell'ambiente.

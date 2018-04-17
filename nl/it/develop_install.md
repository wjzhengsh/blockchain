---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Installazione di un ambiente di sviluppo
{: #installing-a-development-environment}

Attieniti alle seguenti istruzioni per ottenere gli strumenti di sviluppo {{site.data.keyword.blockchainfull}} Platform: Develop per creare e testare reti di business. Per fornire flessibilità e per abilitare il numero massimo di scenari di sviluppo, test e distribuzione, gli strumenti di sviluppo vengono forniti come moduli npm, che sono controllati dalla riga di comando.

## Prima di cominciare

Assicurati di installare i seguenti prerequisiti:

- Ubuntu Linux 14.04 / 16.04 LTS (entrambi a 64 bit) o Mac OS 10.12
- Node v8.9 o superiore (la v9 non è supportata)
- npm v5.x
- git v2.9.x o superiore
- Python v2.7.x
- Docker Engine v17.03 o superiore
- Docker-Compose v1.8 or superiore
- Un editor di codice a tua scelta, come VSCode.

Se stai usando VSCode, è disponibile un'estensione Hyperledger Composer VSCode [qui](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).


## Passo uno: Installazione degli strumenti di riga di comando

Gli strumenti di riga di comando includono **composer-cli**, il modulo di riga di comando {{site.data.keyword.blockchainfull_notm}} Platform: Develop principale; **generator-hyperledger-composer**, **composer-rest-server** e **Yeoman** sono moduli utilizzati per generare reti di business, server REST personalizzati e applicazioni Angular.

1. Installa **composer-cli** utilizzando il seguente comando:

        npm install -g composer-cli@next

2. Installa **composer-rest-server** utilizzando il seguente comando:

        npm install -g composer-rest-server@next

    Usa il modulo **composer-rest-server** per creare un server REST sulla tua macchina per presentare le tue reti di business come API RESTful.

3. Installa **generator-hyperledger-composer** utilizzando il seguente comando:

        npm install -g generator-hyperledger-composer@next

    Usa **generator-hyperledger-composer** per generare le strutture di rete di business di base, i modelli e un'applicazione Angular.

4. Yeoman è uno strumento per generare le applicazioni che utilizza `generator-hyperledger-composer`:

        npm install -g yo

## Passo due: Installazione di un playground locale

Il playground Hyperledger Composer è un'interfaccia utente che può essere connessa a un vero {{site.data.keyword.blockchain}} o utilizzata come un ambiente simulato per testare una rete di business. Installa il playground utilizzando il seguente comando:

        npm install -g composer-playground@next

## Facoltativo: Configura il tuo IDE

Puoi utilizzare il playground per sviluppare, modificare e testare reti di business. Tuttavia, se vuoi sviluppare una rete di business in un IDE, è disponibile un'estensione VSCode che aggiunge un'evidenziazione della sintassi per il linguaggio di modellazione Hyperledger Composer.

1. Installa VSCode da questo URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Apri VSCode, vai a Extensions, quindi cerca e installa l'estensione Hyperledger Composer dal marketplace.

## Passo tre: Installazione di un Hyperledger Fabric locale

Distribuendo un'istanza Hyperledger Fabric locale, puoi testare appieno le regole di controllo accessi e le reti di business.

1. Crea la directory `fabric-tools` utilizzando il seguente comando:

        mkdir ~/fabric-tools && cd ~/fabric-tools

In una directory a tua scelta, ad esempio `~/fabric-tools`, ottieni il file `.tar.gz` che contiene gli strumenti per installare Hyperledger Fabric:

2. Scarica il file `tar.gz` che contiene gli strumenti per installare Hyperledger Fabric in un ambiente Docker.

        curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz

    È anche disponibile un `.zip`. Sostituisci il file `.tar.gz` con `fabric-dev-servers.zip` e il comando `tar -xvf` con un comando `unzip` nel frammento precedente.

3. Utilizza i seguenti comandi per scaricare un runtime locale Hyperledger Fabric v1.1:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./downloadFabric.sh

## Passo quattro: Avvio dell'istanza Hyperledger Fabric

La directory `fabric-tools` contiene una serie di script che controllano l'istanza Hyperledger Fabric scaricata. Devi eseguire il comando `./createPeerAdminCard.sh` per generare una scheda di rete di business, che puoi utilizzare per distribuire la rete di business al Docker che ospita l'istanza Hyperledger Fabric.

1. Esegui questi comandi per avviare l'istanza Hyperledger Fabric v1.1:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./startFabric.sh
        ./createPeerAdminCard.sh

## Facoltativo: stabilisci una connessione all'istanza Hyperledger Fabric utilizzando il playground

Per iniziare a sviluppare una rete di business, utilizza il playground di Hyperledger Composer o il tuo IDE.

1. Per avviare il playground, esegui questo comando:

        composer-playground

    Il playground si apre al seguente URL: http://localhost:8080/login. La scheda **PeerAdmin@hlfv1** creata nel passo precedente può essere utilizzata per distribuire una rete di business.


## Avvio e arresto di Hyperledger Fabric

La directory `fabric-tools` contiene una serie di script che controllano l'istanza Hyperledger Fabric. Puoi arrestare e avviare il runtime utilizzando `~/fabric-tools/stopFabric.sh` e `~/fabric-tools/startFabric.sh`.

`~/fabric-tools/teardownFabric.sh` richiederà che tu crei una nuova scheda PeerAdmin al successivo avvio dell'ambiente.

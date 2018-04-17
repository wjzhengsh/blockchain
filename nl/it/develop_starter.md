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

# Distribuzione di una rete di business su Starter Plan
{: #deploying-a-business-networks-on-starter-plan}

Le reti di business possono essere sviluppate e distribuite a un ambiente Starter Plan utilizzando l'ambiente per gli sviluppatori {{site.data.keyword.blockchainfull}} Platform: Develop e il set di strumenti per gli sviluppatori di Hyperledger Composer.

Utilizzando l'ambiente per gli sviluppatori, puoi modellare e testare rapidamente le reti di business {{site.data.keyword.blockchain}} e distribuirle a un'istanza di {{site.data.keyword.blockchainfull_notm}} Platform.

## Prima di cominciare

Assicurati di leggere [Informazioni su Starter Plan](./starter_plan.html) e [Introduzione a Starter Plan](./get_start_starter_plan.html). Assicurati anche di aver installato l'ambiente per sviluppatori [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop_install.html) e di aver creato l'istanza di {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan attenendoti alle istruzioni contenute in [Governance della rete Starter Plan](./get_start_starter_plan.html).


## Passo uno: Richiamo del segreto di amministrazione (admin secret)

1. Dalla schermata di panoramica di Starter Plan, fai clic su **Connection Profile**.

2. Il profilo connessione contiene una proprietà **admin secret**. Richiama il segreto e salvane una copia.

## Passo due: Creazione di una scheda di Autorità di certificazione

Il segreto richiamato nel passo precedente sarà utilizzato per creare una scheda di rete di business per l'Autorità di certificazione (CA, Certificate Authority). La scheda CA verrà quindi importata e verrà utilizzata per scambiare il segreto di amministrazione (**admin secret**) per i certificati validi dall'Autorità di certificazione di Starter Plan.

1. Utilizzando il segreto annotato dal passo uno, esegui questo comando per creare la scheda di rete di business CA:

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. Importa la scheda utilizzando il seguente comando:

        composer card import -f ca.card -n ca

3. Ora che la scheda è stata importata, è possibile utilizzarla per scambiare il segreto di amministrazione (**admin secret**) per i certificati validi dall'Autorità di certificazione (CA, Certificate Authority). Esegui questo comando per richiedere certificati dall'Autorità di certificazione.

        composer identity request --card ca --path ./credentials

Il comando `composer identity request` crea una directory `credentials` che contiene i file `.pem` di certificato.

## Passo tre: Aggiunta di certificati all'istanza Starter Plan

I certificati devono essere aggiunti all'istanza Starter Plan. Per praticità, è possibile aggiungerli utilizzando la API {{site.data.keyword.blockchainfull_notm}} Platform. I certificati devono essere aggiunti; i peer devono essere quindi arrestati e riavviati e i certificati devono essere sincronizzati. I dati necessari nelle variabili delle chiamate API possono essere richiamati dal profilo di connessione (**Connection Profile**) nel Network Monitor di Starter Plan.

1. Aggiungi il certificato utilizzando la seguente chiamata API:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    Nell'esempio precedente, il corpo viene lasciato vuoto; il seguente codice è un esempio del corpo della richiesta API:

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. Arresta i peer utilizzando la seguente chiamata API:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. Riavvia i peer utilizzando la seguente chiamata API:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. Sincronizza i certificati utilizzando la seguente chiamata API:

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## Passo quattro: Creazione di una scheda di rete di business di gestione

Ora che i certificati corretti sono stati sincronizzati con i peer, è possibile creare schede di rete di business che hanno le autorizzazioni per installare il runtime Hyperledger Composer e avviare il chaincode.

1. Crea una scheda di amministrazione con i ruoli di amministratore canale e amministratore peer utilizzando il seguente comando:

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    Questa scheda verrà utilizzata per distribuire una rete di business a Starter Plan.

2. Importa la scheda creata nel passo precedente utilizzando il seguente comando:

        composer card import -f adminCard.card -n adminCard

## Passo cinque: Installazione e avvio della rete di business

Successivamente, la scheda creata nel passo precedente può essere utilizzata per installare e avviare una rete di business. Per questa guida, installeremo l'esempio di rete vehicle manufacturing. Il comando per avviare una rete di business creerà anche una scheda. Per Starter Plan, questa scheda deve essere eliminata; il comando di esempio fornito denomina questa scheda come `delete_me.card` e può pertanto essere distinta facilmente.

1. Installa il runtime Hyperledger Composer con il seguente comando:

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. Avvia la rete di business con il seguente comando:

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. Elimina la scheda di rete di business denominata `delete_me.card`.

4. Crea una nuova scheda di rete di business e fai riferimento ai certificati richiamati in precedenza con il seguente comando:

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. Importa la scheda di rete di business con il seguente comando:

        composer card import -f ./admin@vehicle-manufacture-network.card

La rete di business viene ora distribuita all'istanza Starter Plan.

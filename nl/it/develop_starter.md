---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Distribuzione di una rete di business su piano Starter
{: #deploying-a-business-network}

Le reti di business possono essere sviluppate e distribuite a un ambiente piano Starter utilizzando l'ambiente per gli sviluppatori {{site.data.keyword.blockchainfull}} Platform e il set di strumenti per gli sviluppatori di Hyperledger Composer.

Utilizzando l'ambiente per gli sviluppatori, puoi modellare e testare rapidamente le reti di business {{site.data.keyword.blockchain}} e distribuirle a un'istanza di {{site.data.keyword.blockchainfull_notm}} Platform.

## Prima di cominciare

Assicurati di leggere [Informazioni su piano Starter](./starter_plan.html) e [Introduzione a piano Starter](./get_start_starter_plan.html). Assicurati anche di aver installato l'ambiente per sviluppatori [{{site.data.keyword.blockchainfull_notm}} Platform](./develop_install.html) e di aver creato l'istanza di {{site.data.keyword.blockchainfull_notm}} Platform piano Starter attenendoti alle istruzioni contenute in [Governance della rete piano Starter](./get_start_starter_plan.html). Assicurati di avere Node v8.9 o successivo, npm v5.x e Hyperledger Composer alla v0.19.x.


## Passo uno: Richiamo del segreto di amministrazione (admin secret)

1. Dalla schermata di panoramica di piano Starter, fai clic su **Profilo connessione** e scarica. Ridenomina questo file con 'connection-profile.json'.

2. Sposta questo file nella stessa directory del tuo file `.bna`.

3. All'interno del profilo di connessione, scendi finché non vedi 'registrar'. All'interno di 'registrar', in 'enrollId' c'è una proprietà **enrollSecret**. Richiama il segreto e salvane una copia.

    ![D8KBag](https://i.makeagif.com/media/4-12-2018/D8KBag.gif)


## Passo due: Creazione di una scheda di Autorità di certificazione

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

Sostituisci `enrollSecret` nel comando precedente con il segreto di amministrazione (admin secret) richiamato dal profilo di connessione.Il comando `composer identity request` crea una directory `credentials` che contiene i file `.pem` di certificato.

## Passo tre: Aggiunta di certificati all'istanza piano Starter

I certificati devono essere aggiunti all'istanza piano Starter. Per praticità, è possibile aggiungerli utilizzando la IU {{site.data.keyword.blockchainfull_notm}} Platform. I certificati devono essere aggiunti; i peer devono essere quindi riavviati e i certificati devono essere sincronizzati nel canale. Il certificato necessario si trova nel file `admin-pub.pem` che è stato generato dal comando precedente, che è una directory `credentials`.

1. Nella IU piano Starter, fai clic sulla scheda **Membri**, su **Certificati** e **Aggiungi certificato**. Passa alla tua directory `credentials` e copia e incolla i contenuti del file `admin-pub.pem` nella casella del certificato. Invia il certificato e riavvia i peer. Nota: il riavvio dei peer necessita di un minuto.

    ![jlEb2y](https://i.makeagif.com/media/4-12-2018/jlEb2y.gif)

2. Successivamente, i certificati devono essere sincronizzati nel canale. Fai clic sulla scheda **Canali**, poi sul pulsante **Azioni**, su **Sincronizza certificato** e **Invia**.

    ![E-sVV5](https://i.makeagif.com/media/4-12-2018/E-sVV5.gif)

## Passo quattro: Creazione di una scheda di rete di business di gestione

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

## Passo cinque: Installazione e avvio della rete di business

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

## Passo sei: esegui il ping della rete di business per assicurarti che sia in esecuzione correttamente

1. Immetti il seguente comando per eseguire il ping della rete di business:

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Per visualizzare i log del chaincode, fai clic su **Canali** e seleziona il tuo canale. Fai clic sulla freccia a discesa per visualizzare i log o sul simbolo Azioni per una visualizzazione più dettagliata.

![fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif)

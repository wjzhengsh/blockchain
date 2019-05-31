---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: logging levels, metrics, health check, peer, orderer

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Gestione dei nodi con Operations Service
{: #operations_service}

{{site.data.keyword.blockchainfull}} Platform v2.0 si basa su Hyperledger Fabric v1.4.1. La piattaforma supporta la funzione Operations Service che offre un'API “operations” RESTful per gli operatori per eseguire i controlli di integrità del nodo, eseguire il pull delle metriche operative dai nodi peer e ordinante e gestire i livelli di log. Il peer e l'ordinante ospitano un server HTTP che offre l'API “operations” RESTful.  Per ulteriori informazioni su Operations Service, vedi [The Operations Service ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html "The Operations Service"){:new_window}.
{:shortdesc}


## Considerazioni e limitazioni
{: #operations_service_consideration_limitation}

- Tutti i nodi peer e ordinanti sono configurati con `clientAuthRequired: false` in modo che sia possibile accedere al programma di controllo dell'integrità. Poiché `clientAuthRequired` è impostato su `false` e inoltre il TLS mutuale è disabilitato, quando accedi al server REST, devi trasmettere le identità TLS per poter eseguire l'autenticazione. Questa impostazione garantisce che solo gli utenti con le chiavi appropriate possano visualizzare i log corrispondenti.
- Solo il modello di pull delle metriche basato su [Prometheus ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#prometheus) è supportato per ora.

## Prima di cominciare
{: #operations_service_before_you_begin}

Devi raccogliere le seguenti informazioni dal tuo peer o ordinante.

- **`peer-endpoint`** o **`orderer-endpoint`**

  Puoi trovare l'URL endpoint operativo del peer o dell'ordinante nel file JSON del peer o dell'ordinante che esporti dalla console.

    1. Fai clic sul peer o l'ordinante nella scheda **Nodo**.
    2. Sulla pagina del peer o dell'ordinante, fai clic sull'icona **Impostazioni** accanto al nome del peer o dell'ordinante.
    3. Nel pannello laterale, fai clic su **Esporta** per salvare il file JSON del peer o dell'ordinante.
    4. Trova l'URL endpoint operativo che corrisponde al valore del parametro `operations_url` nel file JSON. Viene fatto riferimento a questo valore come a `peer-endpoint` o `orderer-endpoint` nei comandi più avanti in questo argomento. Ad esempio:

      ```
      {
      "short_name": "Peer1Org1_0",
      "name": "Peer1 Org1",
      "url": "https://169.46.208.93:32739",
      "type": "fabric-peer",
      "msp_id": "org1msp",
      "operations_url": "https://169.46.208.93:32101"
      }
      ```

      ```
      {
      "short_name": "Orderer_0",
      "name": "Orderer",
      "url": "https://169.46.208.93:31612",
      "type": "fabric-orderer",
      "msp_id": "orderermsp",
      "operations_url": "https://169.46.208.93:30115"
      }
      ```

- **`client-tls-cert`** e **`client-tls-key`**

  Puoi trovare il certificato e la chiave privata della CA TLS nella console.

  1. Fai clic sul nodo CA del peer o dell'ordinante nella scheda **Nodo**.
  2. Nella pagina della CA, fai clic su **Autorità di certificazione TLS**.
  3. Nella tabella della CA TLS, individua l'utente `admin` e fai clic sui punti in verticale in **Azioni**. Poi, fai clic su **Iscrivi identità**.
  4. Nel pannello laterale, fai clic su **Avanti** per visualizzare il certificato e la chiave privata della CA TLS.
  5. Fornisci all'identità un nome di visualizzazione e fai clic su **Esporta identità** e poi su **Aggiungi identità al portafoglio** per salvare il certificato e la chiave privata della CA TLS in un file JSON.
  6. Apri il file JSON esportato.
  7. Trova la chiave privata che corrisponde al valore del parametro `private_key` nel file JSON. Questo è il tuo `client-tls-key` da utilizzare nei seguenti comandi.
  8. Trova il certificato CA TLS che corrisponde al valore del parametro `cert` nel file JSON. Questo è il tuo `client-tls-cert` da utilizzare nei seguenti comandi.

- **`peer tls-ca cert`** o **`orderer tls-ca cert`**

  Puoi trovare il certificato CA TLS del peer o dell'ordinante nella console.

  1. Fai clic sul nodo del peer o dell'ordinante nella scheda **Nodo**.
  2. Fai clic sull'icona **Impostazioni** accanto al nome del nodo.
  3. Copia la stringa del certificato CA TLS del nodo. Questo è il tuo `peer tls-ca cert` o `orderer tls-ca cert` che puoi utilizzare nei seguenti comandi.

**Nota:** per tutti i certificati e le chiavi, devi convertire le stringhe del certificato, che sono nel formato `base64`, in file `.pem` individuali utilizzando i seguenti comandi:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > <key_name>.pem
  ```
  {:codeblock}
{:important}


## Controllo dell'integrità del nodo 
{: #operations_service_health_check}

Esegui il comando `curl -k <peer-endpoint>/healthz` o `curl -k <orderer-endpoint>/healthz` per controllare l'integrità del nodo peer o ordinante. Ad esempio:

```
curl -k https://169.46.208.93:3210/healthz
```
{:codeblock}

Per ulteriori informazioni sui controlli di integrità, vedi [Health checks ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#health-checks "Health checks").


## Visualizzazione delle metriche
{: #operations_service_view_metrics}

Immetti il seguente comando per visualizzare le metriche del peer. Per visualizzare le metriche dell'ordinante dovrai eseguire lo stesso comando ma sostituire `<peer-endpoint>` con `<orderer-endpoint>`.

```
curl -k <peer-endpoint>/metrics --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

Ad esempio:

```
curl -k https://169.55.231.152:30766/metrics --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```
{:codeblock}


Per ulteriori informazioni sulle metriche, vedi [Metrics ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#metrics "Metrics").


## Visualizzazione dei livelli di registrazione
{: #operations_service_log_level_view}

Immetti il seguente comando per visualizzare il livello di registrazione. Visualizzerai il livello di log sul tuo terminale dopo il completamento del comando.

```
curl -k <peer-endpoint>/logspec --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

Ad esempio:
```
curl -k https://169.46.208.93:3210/logspec --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

<!--
```
curl https://169.46.208.93:3210/logspec --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

Puoi visualizzare un risultato simile al seguente esempio:

```
{"spec":"info"}
```

## Impostazione dei livelli di registrazione
{: #operations_service_log_level_set}

Per modificare le impostazioni del livello di registrazione esistente, immetti il seguente comando, che utilizza il metodo `PUT` con il corpo JSON formato da un solo attributo denominato `spec`. Sostituisci `<log-level>` con i tuoi livelli di registrazione previsti. Per ulteriori informazioni sui livelli di registrazione che puoi impostare, vedi [Logging specification ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/logging-control.html#logging-specification "Logging specification") nella documentazione Hyperledger Fabric.

```
curl -X PUT  <peer-endpoint>/logspec -d '{"spec":"<log-level>"}' --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

Ad esempio:
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

Dopo aver impostato un nuovo livello di registrazione, puoi utilizzare il [comando di visualizzazione del livello di registrazione](#operations_service_log_level_view) per controllare le tue impostazioni.

<!--
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

Per ulteriori informazioni sulla configurazione del livello di log, vedi [Log level management ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#log-level-management "Log level management") nella documentazione Hyperledger Fabric.

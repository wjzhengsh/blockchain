---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Applicazioni di esempio

Puoi distribuire e testare applicazioni di esempio per ottenere una migliore comprensione della rete {{site.data.keyword.blockchainfull}} e dello sviluppo delle applicazioni.
{:shortdesc}

## Prima di cominciare

Assicurati di installare tutti i prerequisiti software sul tuo file system locale.  Per ulteriori informazioni, vedi [Configurazione dell'ambiente di sviluppo di applicazioni](/docs/services/blockchain/v10_application.html#setting-up-application-development-environment).

Devi anche avere una rete {{site.data.keyword.blockchain}} su {{site.data.keyword.Bluemix_short}} con un canale e i suoi peer configurati.  Per ulteriori informazioni, vedi [Governance della rete](/docs/services/blockchain/get_start.html).  Quando la tua rete è pronta per l'uso, richiama gli endpoint API delle tue risorse di rete a cui accederà la tua applicazione.  Per ulteriori informazioni, vedi [Aggiunta delle credenziali del servizio di rete alla tua applicazione](/docs/services/blockchain/v10_application.html#adding-network-service-credentials-to-your-application).


## Applicazione Marbles

Nell'applicazione Marbles, più utenti possono creare delle biglie con proprietà differenti e trasferirle ad altri.  L'applicazione Marbles è scritta in JavaScript e il chaincode è scritto in Go.

Puoi trovare il codice di esempio e le istruzioni in [Marbles in GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM-Blockchain/marbles).


## Applicazione Fabric car

Nell'applicazione Fabric car, puoi eseguire **query** e **aggiornamenti** sui record relativi alle automobili nel libro mastro.  L'applicazione Fabric car è scritta in JavaScript e il chaincode è scritto in Go.

Puoi trovare il codice di esempio in [Fabric car in GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) e le istruzioni in [Writing Your First Application ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

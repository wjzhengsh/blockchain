---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop è sviluppato sul set di strumenti Hyperledger Composer open-source, uno dei progetti nell'Hyperledger Project della Linux Foundation. Hyperledger Composer consente agli architetti e agli sviluppatori di creare rapidamente soluzioni {{site.data.keyword.blockchain}} con le API REST che presentano la logica di business ad applicazioni web o mobili, nonché l'integrazione blockchain con i SOR (systems of record) aziendali esistenti.
{:shortdesc}

Hyperledger Composer è composto da un runtime, un'interfaccia riga di comando, un server REST, un connettore LoopBack, un'interfaccia utente Playground, un generatore di codice Yeoman e i plugin editor VSCode e Atom. Per ulteriori informazioni su Hyperledger Composer, vedi la [documentazione di Hyperledger![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)


## Reti di business

Hyperledger Composer ti consente di modellare rapidamente la tua rete di business corrente, che contiene i tuoi asset esistenti e le transazioni ad esse correlate; gli asset sono merci, servizi o proprietà tangibili o intangibili. Come parte del tuo modello di rete di business, definisci le transazioni che possono interagire con gli asset. Le reti di business includono anche i partecipanti che interagiscono con esse, ognuno dei quali può essere associato a una identità univoca nell'ambito di più reti di business.

Per ulteriori informazioni sulla creazione di reti di business, vedi [Sviluppo di reti di business](../develop.html) o la [documentazione di Hyperledger Composer![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/introduction/introduction.html).

## Query

Le query vengono utilizzate per restituire i dati sullo stato globale della blockchain. Le query vengono definite in una rete di business e possono includere parametri variabili per una semplice personalizzazione. Utilizzando le query, i dati possono essere facilmente estratti dalla tua rete {{site.data.keyword.blockchain}}. Le query vengono inviate utilizzando la [API Hyperledger Composer![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/api/api-doc-index).

Per ulteriori informazioni sull'utilizzo delle query nella tua rete di business, consulta il documento relativo a [query e filtro dei dati di rete di business ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/tutorials/queries).

## Identità e schede di rete di business

In una rete di business, i partecipanti sono associati a un'identità. Le schede di rete di business sono una combinazione di un'identità, un profilo di connessione e metadati e vengono utilizzate per connettersi a una rete di business e dimostrare l'identità per le transazioni. Le schede di rete di business semplificano il processo di connessione a una rete di business ed estendono il concetto di un'identità fuori dalla rete di business a un "portafoglio" di identità, ciascuna associata a una rete di business e a un profilo di connessione specifici.

Per ulteriori informazioni sulle identità e sulle schede di rete di business, consulta il documento relativo a [partecipanti e identità ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/managing/participantsandidentities).

## Server REST Hyperledger Composer

Il server REST Hyperledger Composer genera automaticamente un API REST Swagger per una rete di business. Il server REST si basa su LoopBack e converte il modello di rete di business in una definizione Open API. Al runtime, il server REST implementa il supporto di creazione, lettura, aggiornamento ed eliminazione per gli asset e i partecipanti e consente l'inoltro per l'elaborazione o il richiamo con le query delle transazioni.

Per ulteriori informazioni sul server REST Hyperledger Composer, vedi il documento relativo alla [generazione di una API REST![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).

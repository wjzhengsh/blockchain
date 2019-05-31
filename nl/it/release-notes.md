---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: release note, latest changes, Hyperledger Fabric

subcollection: blockchain

---

{:new_window: target="_blank"}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}

# Note di rilascio
{: #release-notes-saas-20}

Utilizza queste note sulla release raggruppate in base alla data per informazioni sulle modifiche più recenti a {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} che è sviluppato su Hyperledger Fabric v1.4.1.
{:shortdesc}


## 9 maggio 2019
{: #05-09-2019}

**Introduzione delle API della console {{site.data.keyword.blockchainfull_notm}} Platform**

Sono ora disponibili delle API per eseguire il provisioning, modificare ed eliminare i nodi peer, ordinante e CA, rendendo possibile eseguire lo script di compilazione della tua rete blockchain. Utilizza la documentazione nel repository della documentazione API di [{{site.data.keyword.cloud_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](/apidocs/blockchain#introduction "Introduzione") per ulteriori informazioni sulle API e per provarle ad utilizzare. Inoltre, vedi l'argomento sulla [Creazione di una rete con le API](/docs/services/blockchain?topic=blockchain-ibp-v2-apis) per delle istruzioni su come utilizzare le API per creare la tua rete.  

**Governance del canale**  

Gli aggiornamenti alla governance del canale consentono alle politiche di venire riconfigurate. Puoi anche controllare quali membri del canale devono firmare un aggiornamento del canale e puoi organizzare la raccolta delle firme.

## 17 aprile 2019
{: #04-17-2019}

**Capacità dimensionare e ridimensionare le risorse di nodo**  

Quando distribuisci un nodo, puoi ora specificare la quantità di CPU, memoria e archiviazione per i tuoi contenitori, dove applicabile. Puoi successivamente ridimensionare le loro risorse ampliandole o riducendole in base ai modelli d'uso. Per ulteriori informazioni, vedi [Allocazione di risorse](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources).

**Utilizzo di IAM  IBM Cloud IAM (Identity and Access Management)**  

IAM viene utilizzato per controllare l'accesso degli utenti all'IU della console e per limitare le azioni che possono eseguire nell'IU.  Vedi questo argomento per informazioni su come [aggiungere e rimuovere utenti dalla console](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).

## 3 aprile 2019
{: #04-03-2019}

**Supporto per la CA esterna**

Quando aggiungi un nodo peer od ordinante, hai l'opzione di utilizzare i certificati da una CA esterna, una che non è ospitata da {{site.data.keyword.IBM_notm}}. Per ulteriori informazioni, vedi questo argomento sull'[utilizzo di certificati da una CA esterna con il tuo peer od ordinante](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca).

**Regolazione delle prestazioni dell'ordinante**

Nella console sono disponibili dei nuovi parametri di regolazione dell'ordinante per darti maggior controllo sulla velocità di trasmissione e le prestazioni del tuo ordinante. Vedi questo argomento sulla [regolazione del tuo ordinante](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning) per le istruzioni su come configurare i parametri.

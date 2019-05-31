---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-31"

keywords: IBM Blockchain Platform, release, new features

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Novità
{: #whats-new}

## 31 maggio 2019
{: #whats-new-5-31-2019}

{{site.data.keyword.blockchainfull}} Platform di seconda generazione, ti consente di distribuire, gestire e monitorare la tua rete blockchain poiché diventa generalmente disponibile. Questa release include una nuova console di interfaccia utente che può essere utilizzata per distribuire e gestire i componenti della blockchain nel tuo cluster {{site.data.keyword.IBM_notm}} Kubernetes Service su {{site.data.keyword.cloud_notm}}. 

Questa release {{site.data.keyword.blockchainfull_notm}} Platform include le seguenti funzioni chiave:

**COMPILAZIONE ---- Esperienza di sviluppatore integrata**
- **Codifica facilmente** i tuoi smart contract in Node.js, Golang o Java, scrivi le applicazioni client utilizzando la nuova estensione {{site.data.keyword.blockchainfull_notm}} VS Code, avvaliti dell'**integrazione SDK** con la console e impara dalle nostre esercitazioni e dai nostri esempi molto esaurienti.
- **DevOps semplificato** ti consente di passare dallo sviluppo alla verifica e alla produzione aumentando le tue risorse Kubernetes per aggiungere ulteriori componenti.
- **Funzioni chiave di Fabric aggiornate**. Avvaliti delle funzioni più recenti di Hyperledger Fabric v1.4.1:
  -  [Raft ordering service ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft "Raft ordering service")]
  - **Integrazione del servizio {{site.data.keyword.cloud_notm}}.** Avvaliti dei servizi {{site.data.keyword.cloud_notm}} integrati, come il dashboard del servizio {{site.data.keyword.cloud_notm}} Kubernetes, l'analisi log {{site.data.keyword.IBM_notm}} con LogDNA e {{site.data.keyword.cloud_notm}} IAM (Identity and Access Management).
  - [Raccolte di **dati privati**](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) che forniscono una maggiore riservatezza dei dati garantendo che i dati del libro mastro siano condivisi solo con i peer autorizzati tramite il protocollo gossip.
  - [Rilevamento dei servizi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Rilevamento dei servizi"), che ti consente di rilevare e aggiornare dinamicamente il modo in cui la tua applicazione interagisce con la tua rete.
  - [Elenchi di controllo accessi dei canali ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/access_control.html "Elenchi di controllo accessi") che ti offrono un controllo aggiuntivo sulla governance dei tuoi canali e degli smart contract.

**UTILIZZO --- Controllo totale delle tue distribuzioni**
- **Distribuisci solo i componenti di cui hai bisogno**. Connetti un peer a più canali e reti oppure ospita un servizio ordini a cui possono connettersi i business partner.
- **Mantieni un controllo completo delle tue identità**. Memorizza e gestisci le chiavi utilizzate per amministrare i tuoi nodi senza memorizzare le tue chiavi private in {{site.data.keyword.cloud_notm}}.
- **Gestione centralizzata**. La console {{site.data.keyword.blockchainfull_notm}} Platform ti consente di distribuire e gestire tutte le tue organizzazioni e tutti i tuoi nodi in **una singola console centrale** senza dover fare affidamento su {{site.data.keyword.IBM_notm}} o altri fornitori per gestire i tuoi ordinanti o la tua Certificate Authority. Puoi anche aggiungere o rimuovere membri da un consorzio blockchain, creare e unirti a canali e installare e istanziare smart contract dalla tua console.
- **Ospita o unisciti a una rete**. Distribuisci i peer ospitati nel tuo cluster a più canali su più cloud oppure invita altre organizzazioni a unirsi al tuo consorzio o ai tuoi canali mentre le organizzazioni gestiscono i loro nodi in modo indipendente nelle infrastrutture.
- **Gestisci l'accesso** degli utenti che possono amministrare o monitorare i tuoi nodi.
- **Accesso diretto ai log** dei tuoi nodi dal servizio {{site.data.keyword.IBM_notm}} Kubernetes. Utilizza il servizio di analisi log {{site.data.keyword.cloud_notm}} oppure un servizio di terze parti per estrarre e analizzare i tuoi log.
- **Interagisci direttamente con i tuoi pod** utilizzando il dashboard Kubernetes. Esegui il comando exec nei tuoi pod e nei tuoi contenitori per immettere comandi e aggiornare certificati dalla riga di comando.
- **Raccolta della firma dinamica** che permette un migliore controllo sulla governance collaborativa sulle configurazioni del canale.

**CRESCITA --- Scalabilità e flessibilità**
- **Scegli la tua capacità di calcolo**. Hai la flessibilità di decidere la quantità di CPU, memoria e archiviazione di cui desideri eseguire il provisioning nel tuo cluster Kubernetes. Per ulteriori informazioni, vedi [In che modo il servizio {{site.data.keyword.cloud_notm}} Kubernetes interagisce con la console](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
- **Ridimensiona** ampliando e riducendo le risorse nel tuo cluster Kubernetes, pagando solo per quello di cui hai bisogno. Per ulteriori informazioni, vedi [Prezzi](/docs/services/blockchain/howto/pricing.html#ibp-pricing).
- **Ripristino di emergenza e alta disponibilità multizona.** Questa opzione duplica la tua distribuzione Kubernetes tra le zone, abilitando l'alta disponibilità (HA, High Availability) dei tuoi componenti e il ripristino di emergenza (DR, Disaster Recovery).
- **Esegui ovunque** (istruzioni presto disponibili). Grazie alla **base di codice unificata** della console {{site.data.keyword.blockchainfull_notm}} Platform, è possibile eseguire i tuoi componenti su {{site.data.keyword.cloud_notm}}, {{site.data.keyword.cloud_notm}} Private e cloud pubblici di terze parti.

- Ulteriori informazioni su {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sono disponibili in [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Le istruzioni su come distribuire la release 2.0 beta gratuita in un cluster {{site.data.keyword.IBM_notm}} Kubernetes Service sono disponibili in [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- Delle nuove esercitazioni per utilizzare {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sono disponibili nella sottosezione **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** sotto la categoria **COME**.
  * L'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) ti guida attraverso il processo di hosting di una rete creando un ordinante e un peer.
  * [L'esercitazione relativa all'unione a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) spiega come unirsi a una rete esistente creando un peer e unendolo a un canale.
  * [Distribuire uno smart contract sulla rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) fornisce informazioni su come scrivere uno smart contract e distribuirlo sulla tua rete.
- L'offerta {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta è basata su Hyperledger Fabric v1.4.1 e supporta il gossip peer-to-peer, il rilevamento dei servizi e i dati privati. Visita questo [argomento](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) per ulteriori informazioni su come configurare i dati privati sulla tua rete.

## 9 maggio 2019
{: #whats-new-5-09-2019}

Release di {{site.data.keyword.blockchainfull_notm}} versione 1.0.0 dell'estensione {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio (VS) Code. Questo toolkit sviluppatori contiene le seguenti funzionalità chiave:

**Un'interfaccia utente riconfigurata per una navigazione più semplice**

**Informazioni prescrittive e dettagliate che coprono come:**
- Sviluppare uno smart contract
- Eseguire il provisioning di un'istanza del servizio {{site.data.keyword.blockchainfull_notm}} su {{site.data.keyword.cloud_notm}}
- Distribuire ed eseguire transazioni con la tua istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform

**Tutte le funzioni popolari dalle precedenti release, tra cui:**
- Capacità di eseguire il debug degli smart contract
- Codice di esempio
- Funzionalità del portafoglio aggiornata

Per ulteriori informazioni, vedi [Sviluppo di smart contract con l'estensione Visual Studio Code](/docs/services/blockchain/vscode-extension.html "Sviluppo di smart contract con l'estensione Visual Studio Code").

## 23 aprile 2019
{: #whats-new-4-23-2019}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private rilascia la v1.0.2, che utilizza la base di codice Hyperledger Fabric v1.4.0 e supporta la distribuzione su {{site.data.keyword.cloud_notm}} Private v3.1.2.

Se desideri eseguire un upgrade del tuo {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private esistente alla v1.0.2, vedi [Upgrade del grafico Helm su {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading).

Per ulteriori informazioni su Hyperledger Fabric v1.4.0, vedi la [documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/ "documentazione di Hyperledger Fabric v1.4"). Per ulteriori informazioni su {{site.data.keyword.cloud_notm}} Private, vedi la [documentazione di {{site.data.keyword.cloud_notm}} Private v3.1.2 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.2 - documentazione").

## 8 febbraio 2019
{: #whats-new-2-08-2019}

{{site.data.keyword.blockchainfull}} Platform rilascia una versione beta 2.0 gratuita, la prossima generazione di soluzioni {{site.data.keyword.blockchainfull_notm}} Platform che ti consente di distribuire, gestire e monitorare la tua rete blockchain. {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta include una nuova console di interfaccia utente che può essere utilizzata per distribuire e gestire i componenti della blockchain nel tuo cluster {{site.data.keyword.IBM_notm}} Kubernetes Service su {{site.data.keyword.cloud_notm}}. {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta ti consentirà di:

**Creare la tua rete in modo più rapido e facile all'interno di un'esperienza senza soluzione di continuità**

*	Agevole integrazione tra sviluppo di smart contract (codice VS) e gestione della rete
*	DevOps semplificato ti consente di passare dallo sviluppo alla verifica e alla produzione da un singola console
*	Supporto per la scrittura di smart contract nei linguaggi Javascript, Java e Go

**Utilizza e gestisci le reti con un controllo totale**

*	Distribuisci solo i componenti blockchain di cui hai bisogno (peer, servizio ordini, CA (Certificate Authority))
*	Una console riprogettata ti consente di gestire i componenti di rete in un unico luogo, indipendentemente da dove sono distribuiti
*	Mantieni un controllo completo delle tue identità, del tuo libro mastro e dei tuoi smart contract

**Espandi facilmente le reti distribuite con la flessibilità multi-cloud di recente abilitazione**

*	Stabilisci connessioni ai nodi in esecuzione in qualsiasi ambiente (cloud ibridi, pubblici e in loco)
*	Connetti facilmente un singolo peer a molteplici reti del settore
*	Inizia in piccolo e, man mano che cresci, paghi per quello che usi senza investimenti iniziali ed esegui facilmente un upgrade utilizzando Kubernetes.

- Ulteriori informazioni su {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sono disponibili in [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Le istruzioni su come distribuire la release 2.0 beta gratuita in un cluster {{site.data.keyword.IBM_notm}} Kubernetes Service sono disponibili in [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- Delle nuove esercitazioni per utilizzare {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sono disponibili nella sottosezione **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** sotto la categoria **COME**.
  * L'[Esercitazione: crea una rete](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) ti guida attraverso il processo di hosting di una rete creando un ordinante e un peer.
  * [L'esercitazione relativa all'unione a una rete](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) spiega come unirsi a una rete esistente creando un peer e unendolo a un canale.
  * [Distribuire uno smart contract sulla rete](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) fornisce informazioni su come scrivere uno smart contract e distribuirlo sulla tua rete.
- L'offerta {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta è basata su Hyperledger Fabric v1.4 e supporta il gossip peer-to-peer, il rilevamento dei servizi e i dati privati. Visita questo [argomento](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) per ulteriori informazioni su come configurare i dati privati sulla tua rete.

- L'estensione {{site.data.keyword.blockchainfull_notm}} Visual Studio Code è disponibile dal Visual Studio Code Marketplace. Gli sviluppatori possono utilizzare l'estensione per creare, testare e distribuire gli smart contract ad un'istanza di Hyperledger Fabric. L'estensione è compatibile con Hyperledger Fabric 1.3 e successive. Per informazioni sull'utilizzo dell'estensione VS Code, vedi [Strumenti per smart contract](/docs/services/blockchain/vscode-extension.html#develop-vscode).  

L'indice è migliorato raggruppando tutti gli argomenti introduttivi insieme in una sezione denominata **Esercitazioni introduttive**. Inoltre, un'introduzione a ciascuna delle offerte {{site.data.keyword.blockchainfull_notm}} Platform è contenuta nella sottosezione **Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform** nella sezione **APPRENDI**.

**Nota:** i crediti cloud gratuiti non sono più disponibili per gli utenti del piano Starter.

## 7 dicembre 2018
{: #whats-new-12-07-2018}

Gli argomenti *Gestione della rete piano Starter* e *Gestione della rete piano Enterprise* sono stati combinati e sostituiti da una singola esercitazione sull'[Utilizzo del monitoraggio della rete](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## 27 novembre 2018
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private. {{site.data.keyword.cloud_notm}} Private è una piattaforma dell'applicazione per sviluppare e gestire applicazioni inserite nel contenitore che sono basate su Kubernetes e consente agli utenti di distribuire CA (Certificate Authority, Autorità di certificazione), ordinanti e peer su x86, LinuxONE e IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private è basato su Hyperledger Fabric v1.2.1 ed è distribuito utilizzando i grafici Helm Kubernetes. Dopo che hai installato il grafico Helm, puoi trovarlo come un servizio in bundle nel catalogo {{site.data.keyword.cloud_notm}} Private. Nota: [questa offerta](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) è più adatta per gli utenti Fabric esperti.

Per ulteriori informazioni su {{site.data.keyword.cloud_notm}} Private, vedi la [documentazione di {{site.data.keyword.cloud_notm}} Private v3.1.0 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0, documentazione di").

La release di {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private segna anche la fine del programma {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer (Beta). Continua a essere possibile distribuire un peer in {{site.data.keyword.cloud_notm}} Private o in AWS e connetterlo a una rete piano Starter o Enterprise. Per ulteriori informazioni, vedi [distribuzione di un peer rispetto a un piano Starter o Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) e [distribuzione di un peer in AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). Si ritiene che questi peer siano peer **distribuiti** invece di peer remoti perché la distribuzione è gestita dal cliente e, di conseguenza, non c'è alcuna ubicazione centrale rispetto a cui essere remoti.

Questa release introduce anche alcuni miglioramento all'indice della documentazione. Se sei un utente Starter o Enterprise, il tuo contenuto continua a essere disponibile nelle sezioni **APPRENDI**, **COME**, **RIFERIMENTO** e **GUIDA** e i link continuano a essere gli stessi, Potrebbe semplicemente trovarsi in una sottosezione differente nell'indice.

## 13 novembre 2018
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia {{site.data.keyword.blockchainfull_notm}} Platform for AWS (Amazon Web Services).

{{site.data.keyword.blockchainfull_notm}} Platform for AWS ti consente di eseguire i peer in AWS Cloud e di connettere i peer alle reti blockchain esistenti in {{site.data.keyword.cloud_notm}}. I tuoi peer in AWS possono avvalersi del profilo di connessione e di altri componenti blockchain nelle reti esistenti, offrendoti al tempo stesso più flessibilità di co-locare i tuoi peer con altre applicazioni al di fuori di {{site.data.keyword.cloud_notm}}. Per ulteriori informazioni, vedi [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 4 ottobre 2018
{: #whats-new-10-04-2018}

{{site.data.keyword.blockchainfull_notm}} Platform esegue l'upgrade del piano Starter da Hyperledger Fabric v1.1.0 alla v1.2.1. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Importante:** Fabric v1.2.1 non è attualmente compatibile con Remote Peer beta, che utilizza un peer v1.1.0. Le reti piano Starter, create prima del 4 ottobre 2018 e basate su Fabric v1.1.0, non sono influenzate e possono ancora essere utilizzate con Remote Peer beta. {{site.data.keyword.blockchainfull_notm}} Platform aggiornerà Remote Peer beta per utilizzare il peer v1.2.1, che sarà compatibile con le nuove reti Starter, che sono al livello Fabric v1.2.1, e le reti Enterprise, che sono al livello Fabric v1.1.0. Finché Remote Peer beta non sarà stato aggiornato a Fabric v1.2.1, non provare a distribuire una rete remota v1.1.0 con una nuova rete Starter v1.2.1.

## 4 settembre 2018
{: #whats-new-9-04-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia la versione beta dell'offerta Remote Peer. L'offerta Remote Peer è basata su Hyperledger Fabric v1.1.0. Utilizzando Remote Peer, puoi eseguire i nodi peer {{site.data.keyword.blockchainfull_notm}} Platform nel tuo ambiente {{site.data.keyword.cloud_notm}} Private o AWS (Amazon Web Services) cloud. Per ulteriori informazioni, vedi [Informazioni sui peer remoti](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).

## 15 giugno 2018
{: #whats-new-6-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia la GA del piano Starter. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 15 maggio 2018
{: #whats-new-5-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform esegue l'upgrade del piano Enterprise da Hyperledger Fabric v1.0.0 alla v1.1.0. Per ulteriori informazioni, vedi [Informazioni sul piano Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## 18 marzo 2018
{: #whats-new-3-18-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia la versione beta del piano Starter. Il piano Starter ti offre un ambiente di sviluppo e test per apprendere e fare pratica in una rete {{site.data.keyword.blockchainfull_notm}} Platform. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 11 agosto 2017
{: #whats-new-8-11-2017}

{{site.data.keyword.blockchainfull_notm}} Platform sostituisce {{site.data.keyword.blockchainfull_notm}} on Cloud e rilascia il piano Enterprise. Per ulteriori informazioni, vedi [Informazioni sul piano Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

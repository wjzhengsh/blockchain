---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

## 23 aprile 2019
{: #whats-new-4-17-2019}

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
{: #whats-new-12-7-2018}

Gli argomenti *Gestione della rete piano Starter* e *Gestione della rete piano Enterprise* sono stati combinati e sostituiti da una singola esercitazione sull'[Utilizzo del monitoraggio della rete](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## 27 novembre 2018
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private. {{site.data.keyword.cloud_notm}} Private è una piattaforma dell'applicazione per sviluppare e gestire applicazioni inserite nel contenitore che sono basate su Kubernetes e consente agli utenti di distribuire CA (Certificate Authority, Autorità di certificazione), ordinanti e peer su x86, LinuxONE e IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private è basato su Hyperledger Fabric v1.2.1 ed è distribuito utilizzando i grafici Helm Kubernetes. Dopo che hai installato il grafico Helm, puoi trovarlo come un servizio in bundle nel catalogo {{site.data.keyword.cloud_notm}} Private. Nota: [questa offerta](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) è più adatta per gli utenti Fabric esperti.

Per ulteriori informazioni su {{site.data.keyword.cloud_notm}} Private, vedi la [documentazione di {{site.data.keyword.cloud_notm}} Private v3.1.0 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0, documentazione di").

La release di {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private segna anche la fine del programma IBM Blockchain Platform Remote Peer (Beta). Continua a essere possibile distribuire un peer in {{site.data.keyword.cloud_notm}} Private o in AWS e connetterlo a una rete piano Starter o Enterprise. Per ulteriori informazioni, vedi [distribuzione di un peer rispetto a un piano Starter o Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) e [distribuzione di un peer in AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). Si ritiene che questi peer siano peer **distribuiti** invece di peer remoti perché la distribuzione è gestita dal cliente e, di conseguenza, non c'è alcuna ubicazione centrale rispetto a cui essere remoti.

Questa release introduce anche alcuni miglioramento all'indice della documentazione. Se sei un utente Starter o Enterprise, il tuo contenuto continua a essere disponibile nelle sezioni **APPRENDI**, **COME**, **RIFERIMENTO** e **GUIDA** e i link continuano a essere gli stessi, Potrebbe semplicemente trovarsi in una sottosezione differente nell'indice.

## 13 novembre 2018
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform rilascia {{site.data.keyword.blockchainfull_notm}} Platform for AWS (Amazon Web Services).

{{site.data.keyword.blockchainfull_notm}} Platform for AWS ti consente di eseguire i peer in AWS Cloud e di connettere i peer alle reti blockchain esistenti in {{site.data.keyword.cloud_notm}}. I tuoi peer in AWS possono avvalersi del profilo di connessione e di altri componenti blockchain nelle reti esistenti, offrendoti al tempo stesso più flessibilità di co-locare i tuoi peer con altre applicazioni al di fuori di {{site.data.keyword.cloud_notm}}. Per ulteriori informazioni, vedi [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 4 ottobre 2018
{: #whats-new-10-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform esegue l'upgrade del piano Starter da Hyperledger Fabric v1.1.0 alla v1.2.1. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Importante:** Fabric v1.2.1 non è attualmente compatibile con Remote Peer beta, che utilizza un peer v1.1.0. Le reti piano Starter, create prima del 4 ottobre 2018 e basate su Fabric v1.1.0, non sono influenzate e possono ancora essere utilizzate con Remote Peer beta. {{site.data.keyword.blockchainfull_notm}} Platform aggiornerà Remote Peer beta per utilizzare il peer v1.2.1, che sarà compatibile con le nuove reti Starter, che sono al livello Fabric v1.2.1, e le reti Enterprise, che sono al livello Fabric v1.1.0. Finché Remote Peer beta non sarà stato aggiornato a Fabric v1.2.1, non provare a distribuire una rete remota v1.1.0 con una nuova rete Starter v1.2.1.

## 4 settembre 2018
{: #whats-new-9-4-2018}

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

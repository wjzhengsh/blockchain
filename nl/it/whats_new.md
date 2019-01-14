---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Novità
{: #whatsnew}

***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 7 dicembre 2018

Gli argomenti *Gestione della rete piano Starter* e *Gestione della rete piano Enterprise* sono stati combinati e sostituiti da una singola esercitazione sull'[Utilizzo del monitoraggio della rete](v10_dashboard.html).

## 27 novembre 2018

{{site.data.keyword.blockchainfull}} Platform rilascia {{site.data.keyword.blockchainfull_notm}} Platform for ICP ({{site.data.keyword.cloud_notm}} Private). ICP è una piattaforma dell'applicazione per sviluppare e gestire applicazioni inserite nel contenitore che sono basate su Kubernetes e consente agli utenti di distribuire CA (Certificate Authority, Autorità di certificazione), ordinanti e peer su x86, LinuxONE e IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for ICP è basato su Hyperledger Fabric v1.2.1 ed è distribuito utilizzando i grafici Helm Kubernetes. Dopo che hai installato il grafico Helm, puoi trovarlo come un servizio in bundle nel catalogo ICP. Nota: [questa offerta](ibp-for-icp-about.html) è più adatta per gli utenti Fabric esperti.

Per ulteriori informazioni su ICP, vedi la [documentazione di {{site.data.keyword.cloud_notm}} Private v3.1.0 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0, documentazione di").

La release di {{site.data.keyword.blockchainfull_notm}} Platform for ICP segna anche la fine del programma IBM Blockchain Platform Remote Peer (Beta). Continua a essere possibile distribuire un peer in ICP o in AWS e connetterlo a una rete piano Starter o Enterprise. Per ulteriori informazioni, vedi [distribuzione di un peer rispetto a un piano Starter o Enterprise](howto/peer_deploy_ibp.html) e [distribuzione di un peer in AWS](howto/remote_peer_aws.html). Si ritiene che questi peer siano peer **distribuiti** invece di peer remoti perché la distribuzione è gestita dal cliente e, di conseguenza, non c'è alcuna ubicazione centrale rispetto a cui essere remoti.

Questa release introduce anche alcuni miglioramento all'indice della documentazione. Se sei un utente Starter o Enterprise, il tuo contenuto continua a essere disponibile nelle sezioni **APPRENDI**, **COME**, **RIFERIMENTO** e **GUIDA** e i link continuano a essere gli stessi, Potrebbe semplicemente trovarsi in una sottosezione differente nell'indice.

## 13 novembre 2018

{{site.data.keyword.blockchainfull_notm}} Platform rilascia {{site.data.keyword.blockchainfull_notm}} Platform for AWS (Amazon Web Services).

{{site.data.keyword.blockchainfull_notm}} Platform for AWS ti consente di eseguire i peer in AWS Cloud e di connettere i peer alle reti blockchain esistenti in {{site.data.keyword.cloud_notm}}. I tuoi peer in AWS possono avvalersi del profilo di connessione e di altri componenti blockchain nelle reti esistenti, offrendoti al tempo stesso più flessibilità di co-locare i tuoi peer con altre applicazioni al di fuori di {{site.data.keyword.cloud_notm}}. Per ulteriori informazioni, vedi [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](howto/remote_peer.html).

## 4 ottobre 2018

{{site.data.keyword.blockchainfull_notm}} Platform esegue l'upgrade del piano Starter da Hyperledger Fabric v1.1.0 alla v1.2.1. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](starter_plan.html).

**Importante:** Fabric v1.2.1 non è attualmente compatibile con Remote Peer beta, che utilizza un peer v1.1.0. Le reti piano Starter, create prima del 4 ottobre 2018 e basate su Fabric v1.1.0, non sono influenzate e possono ancora essere utilizzate con Remote Peer beta. {{site.data.keyword.blockchainfull_notm}} Platform aggiornerà Remote Peer beta per utilizzare il peer v1.2.1, che sarà compatibile con le nuove reti Starter, che sono al livello Fabric v1.2.1, e le reti Enterprise, che sono al livello Fabric v1.1.0. Finché Remote Peer beta non sarà stato aggiornato a Fabric v1.2.1, non provare a distribuire una rete remota v1.1.0 con una nuova rete Starter v1.2.1.

## 4 settembre 2018

{{site.data.keyword.blockchainfull_notm}} Platform rilascia la versione beta dell'offerta Remote Peer. L'offerta Remote Peer è basata su Hyperledger Fabric v1.1.0. Utilizzando Remote Peer, puoi eseguire i nodi peer {{site.data.keyword.blockchainfull_notm}} Platform nel tuo ambiente ICP (IBM Cloud Private) o AWS (Amazon Web Services) Cloud. Per ulteriori informazioni, vedi [Informazioni sui peer remoti](howto/remote_peer.html).

## 15 giugno 2018

{{site.data.keyword.blockchainfull_notm}} Platform rilascia la GA del piano Starter. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](starter_plan.html).

## 15 maggio 2018

{{site.data.keyword.blockchainfull_notm}} Platform esegue l'upgrade del piano Enterprise da Hyperledger Fabric v1.0.0 alla v1.1.0. Per ulteriori informazioni, vedi [Informazioni sul piano Enterprise](enterprise_plan.html).

## 18 marzo 2018

{{site.data.keyword.blockchainfull_notm}} Platform rilascia la versione beta del piano Starter. Il piano Starter ti offre un ambiente di sviluppo e test per apprendere e fare pratica in una rete {{site.data.keyword.blockchainfull_notm}} Platform. Per ulteriori informazioni, vedi [Informazioni sul piano Starter](starter_plan.html).

## 11 agosto 2017

{{site.data.keyword.blockchainfull_notm}} Platform sostituisce {{site.data.keyword.blockchainfull_notm}} on Cloud e rilascia il piano Enterprise. Per ulteriori informazioni, vedi [Informazioni sul piano Enterprise](enterprise_plan.html).

---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp}

{{site.data.keyword.blockchainfull}} Platform fornisce un'offerta Baas (blockchain-as-a-service) gestita e full-stack che ti consente di distribuire componenti blockchain in ambienti a tua scelta. L'ambiente può essere {{site.data.keyword.cloud_notm}}, in loco tramite {{site.data.keyword.cloud_notm}} Private e cloud di terze parti, come AWS (Amazon Web Services). In questa esercitazione, ti mostreremo il processo generale di configurazione di una rete blockchain di base con {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

**Importante:** prima di utilizzare un'offerta {{site.data.keyword.blockchainfull_notm}} Platform, leggi le informazioni tecniche e di supporto nella sezione [Dichiarazione di non responsabilità](/docs/services/blockchain/needtoknow.html#disclaimer).


## Prima di cominciare
{: #get-started-ibp-prereqs}

{{site.data.keyword.blockchainfull_notm}} Platform fornisce diverse offerte per aiutare tutti i tipi di utenti ad iniziare il loro percorso blockchain e a passare le loro applicazioni in produzione. Devi decidere il tuo ambiente e l'offerta che utilizzerai. La Figura 1 elenca le funzionalità, i gruppi di destinatari, i prezzi e le piattaforme cloud per diverse offerte. Per ulteriori informazioni su ciascuna offerta, fai clic sull'offerta nella seguente tabella.

| **Offerte** | **Cos'è incluso** | **Politica di fatturazione** | **Piattaforma cloud** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**{{site.data.keyword.blockchainfull_notm}} Platform Extension for VSCode**](/docs/services/blockchain?topic=blockchain-develop-vscode#develop-vscode) | Gli sviluppatori possono iniziare con l'IDE che fornisce un programma di esplorazione e i comandi accessibili dalla tavolozza dei comandi per lo sviluppo di smart contract in tempi rapidi. | Gratuito | Viene eseguito sulla tua macchina locale |
| [**piano Starter**](/docs/services/blockchain/starter_plan.html#starter-plan-about) | Rete gestita da {{site.data.keyword.IBM_notm}} con livello di servizio di base, ambiente di sviluppo e test. | Sottoscrizione mensile | {{site.data.keyword.cloud_notm}} |
| [**piano Enterprise**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) | Rete gestita da {{site.data.keyword.IBM_notm}} con livelli di servizio avanzati e ambiente pronto per la produzione enterprise. | Sottoscrizione mensile | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | Console {{site.data.keyword.blockchainfull_notm}} Platform per distribuire e gestire i componenti blockchain nel tuo cluster {{site.data.keyword.cloud_notm}} Kubernetes. | Gratuito per Beta | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | Grafici Helm CA, Ordinante e Peer distribuibili | [Prezzi VPC](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) e Community Edition gratuita | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | Template Quick Start AWS per distribuire i peer remoti che si trovano fuori da {{site.data.keyword.cloud_notm}} | Gratuito | AWS |

*Figura 1. Offerte {{site.data.keyword.blockchainfull_notm}} Platform*

Non utilizzare il piano Starter o la versione beta 2.0 gratuita per l'utilizzo di produzione. È un ambiente di sviluppo e test e non è adatto per i carichi di lavoro di produzione.
{: important}

## Passo uno: Ottieni un'offerta
{: #get-started-ibp-step1}

Assicurati di avere l'account cloud o la licenza PPA per ottenere un'offerta {{site.data.keyword.blockchainfull_notm}} Platform.

* **{{site.data.keyword.blockchainfull_notm}} Platform Extension for VSCode**

  Questa estensione VSCode è disponibile gratuitamente nel [Visual Studio Marketplace ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform Extension for VSCode") e può essere utilizzata per sviluppare, sottoporre a debug e testare gli smart contract per un'eventuale distribuzione in {{site.data.keyword.blockchainfull_notm}}.

* **Piano Starter**, **Piano Enterprise** e **{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta**

  Queste offerte sono disponibili in {{site.data.keyword.cloud_notm}} e puoi individuarle nel [dashboard Catalogo ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/catalog "Catalog") di {{site.data.keyword.cloud_notm}}.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Questa offerta viene fornita come un grafico Helm distribuibile e ha sia un'edizione a pagamento che un'edizione Community gratuita. Puoi scaricare la Enterprise Edition da [Passport Advantage Online ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/software/passportadvantage/pao_customer.html) oppure scaricare l'edizione Community gratuita da [GitHub ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz).

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Questa offerta è disponibile in AWS e puoi distribuire un peer blockchain in AWS utilizzando il [template Quick Start ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Passo due: Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp-step2}

* **Piano Starter**, **Piano Enterprise** e **{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta**

  Accedi a {{site.data.keyword.cloud_notm}} e crea un'istanza del servizio con l'offerta. Se utilizzi il piano Starter, puoi ottenere una rete blockchain con la configurazione predefinita immediatamente dopo che hai [creato l'istanza del servizio](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan). Se utilizzi il piano Enterprise oppure {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta, devi attenerti alla procedura guidata per completare la configurazione iniziale per la tua rete. Per ulteriori informazioni, vedi [Creazione di una rete piano Enterprise](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) o [Distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform sul servizio {{site.data.keyword.cloud_notm}} Kubernetes](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Prima di distribuire una rete, devi [importare il grafico Helm sul tuo {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). Puoi quindi [distribuire i tuoi componenti di rete iniziando da una CA](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#step-three-set-up-your-cas) per creare la tua rete.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Questa offerta è disponibile in AWS e puoi distribuire un peer blockchain in AWS utilizzando il [template Quick Start ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Passi successivi
{: #get-started-ibp-next-steps}

Dopo che hai distribuito {{site.data.keyword.blockchainfull_notm}} Platform nell'ambiente di tua scelta, puoi configurare la rete con il consorzio, i nodi, i canali e gli smart contract e puoi iniziare le transazioni su di esso. Per ulteriori informazioni, vedi gli argomenti relativi alle attività nella sezione **COME** nella navigazione di sinistra di questa documentazione.

## Richiesta di assistenza tecnica
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}} offre diverse opzioni di supporto sulle soluzioni blockchain implementate da {{site.data.keyword.IBM_notm}}. Per ulteriori informazioni sul supporto {{site.data.keyword.blockchainfull_notm}} Platform, vedi [Richiesta di assistenza tecnica](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

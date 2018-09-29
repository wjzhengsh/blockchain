---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informazioni sui peer remoti
{: #remote-peer-overview}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Puoi eseguire i peer remoti di {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> dopo averli collegati a una rete blockchain esistente. L'esecuzione dei peer al di fuori di {{site.data.keyword.cloud_notm}} fornisce maggiore flessibilità per espandere o unire una rete blockchain sfruttando al contempo una rete esistente all'interno di {{site.data.keyword.cloud_notm}}. I peer remoti utilizzano le CA (Certificate Authority) e il servizio ordini sulla piattaforma, ma ti consentono di collocare il tuo peer con altre applicazioni al di fuori di {{site.data.keyword.cloud_notm}}.
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} Private (ICP) è una piattaforma basata su Kubernetes per la creazione di un cloud privato in un ambiente installato in loco. Puoi utilizzare ICP per eseguire un peer remoto e collegare il peer remoto a una rete blockchain su {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP sfrutta i servizi di archiviazione, sicurezza, registrazione e supporto di ICP in modo che tu possa gestire i peer remoti nel tuo ambiente installato in loco. Per ulteriori informazioni su ICP, consulta la [documentazione di {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private - documentazione").  

**Nota:** {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer è attualmente un'offerta Beta adatta per la valutazione e la sperimentazione. **Questa edizione Beta non è destinata all'utilizzo di produzione.** Per accedere e per ulteriori informazioni, vedi [Licenza e determinazione dei prezzi](#remote-peer-license-pricing).

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

L'edizione Beta supporta la piattaforma cloud di {{site.data.keyword.cloud_notm}} Private (ICP), v2.1.0.3. Nota che la rete su {{site.data.keyword.blockchainfull_notm}} Platform e i nodi peer remoti in ICP devono essere eseguiti nella stessa **versione Fabric 1.1**.

## Considerazioni
{: #remote-peer-limitations}

Un peer remoto non ha accesso alle funzionalità o al supporto completo dei peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform. Prima di eseguire i peer remoti, assicurati di comprendere le seguenti restrizioni e limitazioni:
- I peer remoti che vengono eseguiti in altri ambienti cloud non sono visibili nel Monitoraggio della rete della rete blockchain su {{site.data.keyword.cloud_notm}}.
- I peer remoti non possono essere indirizzati utilizzando l'interfaccia utente Swagger nell'interfaccia utente di Monitoraggio della rete.
- Sei responsabile della gestione del monitoraggio dell'integrità, della sicurezza, della registrazione e dell'uso delle risorse dei tuoi peer remoti.
- Puoi connettere i tuoi peer remoti solo alle reti blockchain che si trovano a livello di Hyperledger Fabric v1.1.
- Il tipo di database del peer remoto deve corrispondere al tipo di database della tua rete blockchain, LevelDB o CouchDB.
- L'interfaccia CouchDB Fauxton non è disponibile sul peer remoto.
- [Gossip](../glossary.html#gossip) per i peer remoti non è attualmente supportato.


## Prerequisiti
{: #remote-peer-prereq}

Per utilizzare un peer remoto, devi disporre di un'organizzazione che sia un membro di una rete del piano Starter o piano Enterprise su {{site.data.keyword.blockchainfull_notm}} Platform. Per operare, il peer remoto si avvale degli endpoint API, delle CA Hyperledger Fabric e del servizio ordini della rete piano {{site.data.keyword.blockchainfull_notm}} Platform. Se non sei membro di alcuna rete blockchain, devi creare o aderire a una rete. Per ulteriori informazioni, vedi [Creazione di una rete](../get_start.html#creating-a-network) o [Adesione a una rete](../get_start.html#joining-a-network).


## Licenza e determinazione dei prezzi
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->Per accedere ai peer remoti da eseguire su ICP, vedi [Licenza e determinazione dei prezzi per i peer remoti su ICP](remote_peer_icp.html#license-pricing-icp "Licenza e determinazione dei prezzi per i peer remoti su ICP"). Le licenze per l'edizione Beta dei peer remoti sono gratuite.<!--[AWS] for both platforms.--> In seguito, un'offerta generalmente disponibile (GA) sostituirà l'edizione beta.

La migrazione da Beta a GA non è supportata. Devi scaricare e installare i nuovi peer remoti per utilizzare l'offerta GA, quando verrà rilasciata. Puoi quindi unire i nuovi peer remoti agli stessi canali della stessa rete dei tuoi peer remoti Beta.

**Nota:** per utilizzare un peer remoto, devi disporre di un'organizzazione che appartenga a una rete del piano Starter o piano Enterprise su {{site.data.keyword.blockchainfull_notm}} Platform. Ciò implica che tu o un altro membro della rete paghi il [costo di adesione](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) di IBM Blockchain per la tua organizzazione. Per ulteriori informazioni sul pagamento dei costi, vedi [Modalità di pagamento](paying_mode.html).  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## Distribuzione di un peer remoto
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

Il seguente diagramma descrive la procedura per distribuire un peer remoto di {{site.data.keyword.blockchainfull_notm}} Platform su ICP. Per ulteriori informazioni su come distribuire i peer remoti su ICP, vedi [Distribuzione dei peer remoti in {{site.data.keyword.cloud_notm}} Private](remote_peer_icp.html "Distribuzione dei peer remoti in {{site.data.keyword.cloud_notm}} Private").

![Flusso di distribuzione dei peer remoti su ICP](../images/remote_peer_ICP_flow.png "Flusso di distribuzione dei peer remoti su ICP")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*Figura 1. Flusso di distribuzione dei peer remoti su ICP*


## Gestione di un peer remoto
{: #operate-remote-peer}

Dopo aver distribuito il peer remoto, devi completare diversi passi operativi prima che il tuo peer possa inviare transazioni alla rete. I passi operativi includono l'aggiunta della tua organizzazione a un canale, l'unione del tuo peer remoto al canale, l'installazione del chaincode sul peer remoto, l'istanziazione del chaincode sul canale e la connessione delle applicazioni al peer remoto. Per ulteriori informazioni, vedi <!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[Gestione dei peer remoti in {{site.data.keyword.cloud_notm}} Private](remote_peer_operate_icp.html#remote-peer-operate).

## Residenza dei dati
{: #data-residency}

I requisiti di residenza dei dati sono restrizioni che controllano dove possono risiedere i dati. Il requisito più comune sulla residenza dei dati è associato alle leggi di alcuni paesi, che impongono che tutti i dati del cliente elaborati e memorizzati in un sistema IT debbano rimanere all'interno dei confini di un determinato paese. Allo stesso modo, alcune aziende in settori altamente regolamentati, come il governo, la sanità e i servizi finanziari, richiedono che tutti i loro dati siano memorizzati interamente dietro il proprio firewall. Inoltre, alcune società hanno una politica aziendale per cui determinati tipi di dati, in genere le informazioni di identificazione personale, devono rimanere private ed essere interamente dietro il loro firewall aziendale. Pertanto, per ottenere la residenza dei dati, tutti i componenti della rete blockchain devono far parte dello stesso [canale](../glossary.html#channel) e risiedere all'interno del confine di un singolo paese.

Per soddisfare i requisiti di residenza dei dati, è importante comprendere l'architettura di Hyperledger Fabric che sta alla base di {{site.data.keyword.blockchainfull_notm}} Platform. L'architettura è incentrata su tre componenti chiave: servizio ordini, autorità di certificazione (CA) e peer. Un peer riceve gli aggiornamenti di stato ordinati sotto forma di blocchi dal servizio ordini e mantiene lo stato e il libro mastro. Pertanto, un peer e un servizio ordini hanno una relazione diretta. Il libro mastro contiene i valori più recenti per tutte le chiavi e i dati inclusi nei log delle transazioni.

Inoltre, le applicazioni client utilizzano gli [SDK Fabric](../v10_application.html#using-the-fabric-sdks) per inviare transazioni ai peer e al servizio ordini. Queste transazioni includono dati della [serie di lettura-scrittura![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "Semantica della serie di lettura-scrittura"), che contengono le coppie chiave-valore sul libro mastro.

Se la residenza dei dati all'interno del paese è un requisito per la tua azienda, il servizio ordini, i nodi peer e l'applicazione client devono risiedere nello stesso paese. Quando viene creata una rete {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}}, hai la possibilità di selezionare un'ubicazione per la rete. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Per  ulteriori informazioni sulle regioni e ubicazioni, vedi [Regioni e ubicazioni {{site.data.keyword.blockchainfull_notm}} Platform](../reference/ibp_regions.html). Per ottenere la residenza dei dati in uno di questi paesi, il tuo peer remoto deve risiedere nello stesso paese dell'ubicazione della rete di {{site.data.keyword.blockchainfull_notm}} Platform.

Se la rete {{site.data.keyword.blockchainfull_notm}} Platform contiene nodi di peer remoto e servizio ordini oltre i confini del paese, puoi utilizzare i canali per separare i dati in un sottoinsieme di peer sulla rete. I nodi di ordine sono ubicati sempre nel data center che hai selezionato per ospitare la rete. Non è possibile avere ordinanti oltre i confini del paese. Tuttavia, i peer possono trovarsi nel data center o in un'ubicazione remota al di fuori di {{site.data.keyword.cloud_notm}}. Pertanto, per ottenere la residenza dei dati, è possibile creare un canale in cui l'ordinante e tutti i peer, sia i peer locali al data center che i peer remoti, risiedono nello stesso paese. In questo modo, tutti i dati condivisi tra l'ordinante e i peer restano all'interno dei confini di un singolo paese. Se necessario possono ancora esistere altri canali, dove l'ordinante e i peer remoti si trovano oltre i confini del paese e non è richiesta la residenza dei dati.

In futuro, la nuova tecnologia in Hyperledger Fabric migliorerà la capacità di ottenere un'ulteriore residenza dei dati utilizzando le [Raccolte di dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Raccolte di dati privati") e la Zero Knowledge Proof (dimostrazione a conoscenza zero).

- Una raccolta di dati privati garantisce che i dati privati siano condivisi peer-to-peer (tramite il protocollo gossip) solo con i peer autorizzati a vederli, ad esempio, i peer che si trovano all'interno dei confini nazionali. I dati vengono memorizzati in un database privato sul peer.  Qui il servizio ordini non è coinvolto e non vede i dati privati. Un hash di questi dati viene scritto nei libri mastro di ogni peer sul canale. L'hash utilizzato per la convalida dello stato funge da prova della transazione e può essere utilizzato per scopi di controllo.

- Una Zero-Knowledge Proof (ZKP) consente a un “prover” di assicurare a un “verificatore” di essere a conoscenza di un segreto senza rivelare il segreto stesso. È un modo per dimostrare che sai qualcosa che soddisfa un'affermazione senza mostrare ciò che sai.

Per ulteriori informazioni su queste tecnologie, consulta il white paper sulle [Transazioni private e confidenziali con Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transazioni private e confidenziali con Hyperledger Fabric").

## Richiesta di assistenza tecnica
{: #remote-peer-support}

L'edizione Beta dell'offerta {{site.data.keyword.blockchainfull_notm}} Remote Peer è pensata per l'esplorazione, lo sviluppo e il test. **Non utilizzare questa edizione per la produzione.** {{site.data.keyword.blockchainfull_notm}} Platform non fornisce supporto per questa edizione. Se riscontri problemi correlati al tuo peer remoto, vedi [Risorse blockchain e forum di supporto](../v10_dashboard.html#support-forums). Puoi anche visualizzare le risorse di supporto nella schermata **Richiedi assistenza** del Monitoraggio della rete.  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

Per i problemi correlati a {{site.data.keyword.cloud_notm}} Private, ICP offre [supporto digitale gratuito e supporto per Community Edition ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "supporto digitale gratuito e supporto per Community Edition di ICP").

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

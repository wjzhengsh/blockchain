---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: IBM Blockchain Platform, remote peer, multi-cloud, private data, AWS Cloud

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

# Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services
{: #remote-peer-aws-about}

**Nota:** il programma {{site.data.keyword.blockchainfull}} Platform Remote Peer su {{site.data.keyword.cloud_notm}} Private (Beta) è terminato. Se vuoi continuare a eseguire i peer nel tuo ambiente {{site.data.keyword.cloud_notm}} Private, utilizza invece l'offerta **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**. Per ulteriori informazioni, vedi [Informazioni sui peer su {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-peer).

Puoi eseguire il peer {{site.data.keyword.blockchainfull_notm}} Platform nell'AWS Cloud dopo che lo hai connesso a una rete blockchain esistente in {{site.data.keyword.cloud_notm}}. L'esecuzione di peer remoti che sono al di fuori di {{site.data.keyword.cloud_notm}} fornisce maggiore flessibilità per espandere, o unirsi a, una rete blockchain sfruttando al contempo una rete esistente all'interno di {{site.data.keyword.cloud_notm}}. I tuoi peer remoti nell'AWS cloud si avvalgono delle CA (Certificate Authority) e del servizio ordini sulla piattaforma, ma puoi collocare i tuoi peer con altre applicazioni al di fuori di {{site.data.keyword.cloud_notm}}.
{:shortdesc}


## Considerazioni
{: #remote-peer-aws-about-limitations}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS non ha accesso alla piena funzionalità o al supporto completo dei peer ospitati su {{site.data.keyword.blockchainfull_notm}} Platform. Prima di eseguire {{site.data.keyword.blockchainfull_notm}} Platform for AWS, assicurati di comprendere le seguenti restrizioni e limitazioni:
- I peer che vengono eseguiti in altri ambienti cloud non sono visibili nel Monitoraggio della rete della rete blockchain su {{site.data.keyword.cloud_notm}}.
- Non ci si può occupare dei peer in esecuzione su {{site.data.keyword.blockchainfull_notm}} Platform for AWS utilizzando l'interfaccia utente Swagger nell'interfaccia utente di Monitoraggio della rete.
- Sei responsabile della gestione del monitoraggio dell'integrità, della sicurezza, della registrazione e dell'uso delle risorse dei tuoi nodi peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS.
- Puoi connettere i tuoi peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS solo alle reti blockchain che sono al livello Fabric v1.1 o v1.2.1. Puoi trovare la tua versione Fabric aprendo la [finestra Preferenze della rete](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) nel tuo Monitoraggio della rete.
- Il tipo di database del peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS deve corrispondere al tipo di database della tua rete blockchain, che è LevelDB o CouchDB.
- L'interfaccia CouchDB Fauxton non è disponibile sul peer AWS.
- [Gossip](/docs/services/blockchain/glossary.html#glossary-gossip) per i peer AWS non è attualmente supportato. Ciò implica che anche le funzioni Fabric che dipendono da gossip, come i [dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "dati privati") e il [rilevamento di servizi ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "rilevamento di servizi"), non sono supportate.

## Prerequisiti
{: #remote-peer-aws-about-prereq}

Per utilizzare un peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS, devi avere un'organizzazione che sia un membro di una rete piano Starter o piano Enterprise su {{site.data.keyword.blockchainfull_notm}} Platform. Per funzionare, il peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS si avvale degli endpoint API, delle CA Hyperledger Fabric e del servizio ordini della rete {{site.data.keyword.blockchainfull_notm}} Platform. Se non sei membro di alcuna rete blockchain, devi creare o aderire a una rete. Per ulteriori informazioni, vedi [Creazione di una rete](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) o [Adesione a una rete](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

## Licenza e determinazione dei prezzi
{: #remote-peer-aws-about-license-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS è attualmente offerto come una Community Edition, gratuitamente; in futuro,, {{site.data.keyword.blockchainfull_notm}} Platform for AWS potrebbe essere modificato in un modello BYOL (Bring-Your-Own-License), che richiederà l'acquisto di una licenza da IBM.

**Nota:** per utilizzare un peer AWS, devi disporre di un'organizzazione che appartenga a una rete piano Starter o piano Enterprise su {{site.data.keyword.blockchainfull_notm}} Platform. Ciò implica che tu o un altro membro nella rete dobbiate pagare il {{site.data.keyword.blockchainfull_notm}} [costo di adesione](/docs/services/blockchain/howto/pricing.html#ibp-pricing-key-elements) per la tua organizzazione. Per ulteriori informazioni sul pagamento dei costi, vedi [Modalità di pagamento](/docs/services/blockchain/howto/paying_mode.html#paying-mode).


## Distribuzione di un peer AWS
{: #remote-peer-aws-about-deploy}

Utilizza il [templateQuick Start ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/ "template Quick Start") per distribuire facilmente {{site.data.keyword.blockchainfull_notm}} Platform for AWS. Per ulteriori informazioni, consulta il manuale [{{site.data.keyword.blockchainfull_notm}} Platform for AWS Quick Start Deployment Guide ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://s3.amazonaws.com/aws-quickstart/quickstart-ibm-fabric/doc/ibm-blockchain-platform-for-aws.pdf "{{site.data.keyword.blockchainfull_notm}} Platform for AWS Quick Start Reference Deployment").

Per istruzioni su come distribuire {{site.data.keyword.blockchainfull_notm}} Platform for AWS, vedi il documento relativo alla [distribuzione di peer in Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).

Il seguente diagramma su cui è possibile fare clic descrive il processo di distribuzione di un peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS. Puoi fare clic su ogni passo per leggere le istruzioni dettagliate.

<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_AWS_flow.png" width="750" alt="Fai clic su una casella per ottenere ulteriori dettagli sul processo." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-account" alt="Configura o accedi a AWS" title="Configura o accedi" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-account" alt="Crea una coppia di chiavi" title="Crea una coppia di chiavi" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-prerequisites" alt="Crea o unisciti a una rete" title="Crea o unisciti a una rete" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-sdk" alt="Unisciti a un canale" title="Unisciti a un canale" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-register-peer" alt="Registra l'identità del peer" title="Registra l'identità del peer" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-network-endpoints" alt="Richiama le informazioni di configurazione del peer" title="Richiama le informazioni di configurazione del peer" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-launchqs" alt="Fai clic sul link" title="Fai clic sul link" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-launchqs" alt="Configura le istanze del peer" title="Configura le istanze del peer" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test" alt="Verifica la distribuzione" title="Verifica la distribuzione" shape="rect" coords="443.95, 258.43, 570.53, 333.48" />
<area href="/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-sdk" alt="Utilizza SDK Fabric" title="Utilizza SDK Fabric" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-cli-operate" alt="Utilizza la CLI degli strumenti Fabric" title="Utilizza la CLI degli strumenti Fabric" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*Figura 1. Flusso di distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for AWS su AWS*


## Gestione di un peer AWS
{: #remote-peer-aws-about-operate-remote-peer}

Dopo aver distribuito il peer AWS, devi completare diversi passi operativi prima che il tuo peer possa inoltrare transazioni alla rete. I passi operativi includono l'aggiunta della tua organizzazione a un canale, l'unione del tuo peer al canale, l'installazione del chaincode sul peer, l'istanziazione del chaincode sul canale e la connessione delle applicazioni al tuo peer. Per ulteriori informazioni, vedi [Utilizzo dei peer in Amazon Web Service](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate).

## Residenza dei dati
{: #remote-peer-aws-about-data-residency}

Poiché le reti blockchain non rilevano il tipo di dati che viene elaborato, è a volte necessario eseguire delle operazioni supplementari per mantenere al sicuro certi tipi di dati. Il requisito più comune sulla residenza dei dati è associato alle leggi di alcuni paesi, che impongono che tutti i dati elaborati e memorizzati in un sistema IT debbano rimanere all'interno dei confini di un determinato paese. Allo stesso modo, alcune aziende in settori altamente regolamentati, come il governo, la sanità e i servizi finanziari, richiedono che i dati siano memorizzati interamente dietro il proprio firewall. Pertanto, per ottenere la residenza dei dati, tutti i componenti della rete blockchain devono far parte dello stesso [canale](/docs/services/blockchain/glossary.html#glossary-channel) e risiedere all'interno del confine di un singolo paese.

Per soddisfare i requisiti di residenza dei dati, è importante comprendere l'architettura di Hyperledger Fabric che sta alla base di {{site.data.keyword.blockchainfull_notm}} Platform. L'architettura è incentrata su tre componenti chiave: CA (Certificate Authority, Autorità di certificazione), ordinante e peer. Un peer riceve gli aggiornamenti di stato ordinati sotto forma di blocchi dal servizio ordini e mantiene lo stato e il libro mastro. Pertanto, un peer e un ordinante hanno una relazione diretta. Il libro mastro contiene i valori più recenti per tutte le chiavi e i dati inclusi nei log delle transazioni.

Inoltre, le applicazioni client utilizzano gli [SDK Fabric](/docs/services/blockchain/v10_application.html#dev-app-fabric-sdks) per inviare transazioni ai peer e al servizio ordini. Queste transazioni includono dati della [serie di lettura-scrittura![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Semantica della serie di lettura-scrittura"), che contengono le coppie chiave-valore sul libro mastro.

Se la residenza dei dati all'interno del paese è un requisito per il tuo business, l'ordinante, i peer e le applicazioni client devono risiedere nello stesso paese. Quando viene creata una rete {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}}, hai la possibilità di selezionare un'ubicazione per la rete. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Per ulteriori informazioni sulle regioni e sulle ubicazioni, vedi [Regioni e ubicazioni {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/reference/ibp_regions.html#ibp-regions-locations). Per ottenere la residenza dei dati in uno di questi paesi, il tuo peer deve risiedere nello stesso paese dell'ubicazione della rete di {{site.data.keyword.blockchainfull_notm}} Platform.

### Un caso d'uso per la residenza dei dati
{: #remote-peer-aws-about-data-res-use-case}

Prendi in considerazione una rete {{site.data.keyword.blockchainfull_notm}} Platform che include l'ordinante e la CA (Certificate Authority) insieme a un consorzio di quattro organizzazioni. Le organizzazioni hanno uno o più nodi peer. Tutte e quattro le organizzazioni fanno parte di un singolo canale e tutti i componenti della rete risiedono nella regione (ad esempio Francoforte) dove è stata distribuita la rete {{site.data.keyword.blockchainfull_notm}} Platform. Infine, le applicazioni client che interagiscono con i peer risiedono anch'esse in Germania. La residenza dei dati viene mantenuta.  

![Residenza dei dati quando tutti i componenti si trovano nello stesso paese](../images/remote_peer_data_res_1.png "Residenza dei dati quando tutti i componenti si trovano nello stesso paese")  
*Figura 3. Residenza dei dati quando tutti i componenti si trovano nello stesso paese*

Prendiamo ora in considerazione le implicazioni quando un **peer** si unisce a una delle organizzazioni.  Un peer può risiedere nella stessa regione del resto della rete oppure dovunque al di fuori della regione della rete {{site.data.keyword.blockchainfull_notm}} Platform:

-	Se il peer risiede nello stesso paese del resto della rete, la residenza dei dati viene mantenuta. Tutti i dati di libro mastro restano all'interno della Germania come nella **Figura 3** in alto.
-	Se il peer risiede in un altro paese (ad esempio negli Stati Uniti), la residenza dei dati non viene più mantenuta poiché i dati nel libro mastro del peer sono condivisi fuori dai confini nazionali.

Per risolvere questo problema, è possibile utilizzare i **canali** per segregare i dati a un sottoinsieme di peer sulla rete. Quando la rete {{site.data.keyword.blockchainfull_notm}} Platform contiene peer e ordinanti che oltrepassano i confini nazionali, i canali forniscono l'isolamento dei dati di libro mastro dalle organizzazioni con dei peer fuori dai confini nazionali.  

**Nota:** gli ordinanti si trovano sempre nella regione del data center che hai selezionato per ospitare la rete. Non è possibile avere più ordinanti oltre i confini nazionali. Tuttavia, i peer possono trovarsi nel data center o in un'ubicazione remota al di fuori di {{site.data.keyword.cloud_notm}}.

![Residenza dei dati quando i peer si trovano fuori dal paese della regione di {{site.data.keyword.blockchainfull_notm}} Platform ](../images/remote_peer_data_res_2.png "Residenza dei dati quando i peer si trovano fuori dal paese della regione di {{site.data.keyword.blockchainfull_notm}} Platform ")  
*Figura 4. Residenza dei dati quando i peer si trovano fuori dal paese della regione di {{site.data.keyword.blockchainfull_notm}} Platform*

Nella **Figura 4**, la residenza dei dati non è richiesta per `OrgC` e `OrgD`. In effetti, `OrgD` ora include due peer, `OrgD-peer1` e `OrgD-peer2`, che si trovano negli *Stati Uniti*. Pertanto, affinché `OrgA`, `OrgB` e le loro rispettive applicazioni client e i loro rispettivi peer che risiedono in Germania isolino i dati di libro mastro sul canale `X`, viene creato un nuovo canale `Y` per `OrgC` e `OrgD`.

Per una comprensione più approfondita del flusso di dati sulla rete {{site.data.keyword.blockchainfull_notm}} Platform, fai riferimento alla [documentazione di Fabric sul flusso di transazioni![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flusso di transazioni").

In futuro, la nuova tecnologia in Hyperledger Fabric migliorerà la capacità di ottenere un'ulteriore residenza dei dati utilizzando le [Raccolte di dati privati ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Raccolte di dati privati") e la Zero Knowledge Proof (dimostrazione a conoscenza zero).

- Una raccolta di dati privati garantisce che i dati privati siano condivisi peer-to-peer (tramite il protocollo gossip) solo con i peer autorizzati a vederli, ad esempio, i peer che si trovano all'interno dei confini nazionali. I dati vengono memorizzati in un database privato sul peer.  Qui il servizio ordini non è coinvolto e non vede i dati privati. Un hash di questi dati viene scritto nei libri mastro di ogni peer sul canale. L'hash utilizzato per la convalida dello stato funge da prova della transazione e può essere utilizzato per scopi di controllo. I dati privati sono disponibili per le reti in {{site.data.keyword.blockchainfull_notm}} Platform in esecuzione su Fabric versione 1.2.1. Tuttavia, la funzione dei dati privati non è disponibile per i peer remoti.

- Una Zero-Knowledge Proof (ZKP) consente a un “prover” di assicurare a un “verificatore” di essere a conoscenza di un segreto senza rivelare il segreto stesso. È un modo per dimostrare che sai qualcosa che soddisfa un'affermazione senza mostrare ciò che sai.

Puoi ottenere maggiori informazioni su queste tecnologie nel white paper sulle [Transazioni private e confidenziali con Hyperledger Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Transazioni private e confidenziali con Hyperledger Fabric").

## Richiesta di assistenza tecnica
{: #remote-peer-aws-about-support}

{{site.data.keyword.blockchainfull_notm}} Platform non fornisce supporto per questa offerta. Se riscontri dei problemi correlati al tuo peer, puoi utilizzare le risorse per sviluppatori di blockchain e i forum di supporto gratuiti e richiedere assistenza da {{site.data.keyword.IBM_notm}} e dalla community di Fabric. Per ulteriori informazioni, vedi [Risorse blockchain e forum di supporto](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources). Puoi anche visualizzare le risorse di supporto nella schermata **Richiedi assistenza** del Monitoraggio della rete.

- Per problemi correlati a AWS, puoi utilizzare sia i [forum di supporto della community di AWS![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://forums.aws.amazon.com/index.jspa "forum di supporto della community di AWS") che il [supporto premium AWS![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://aws.amazon.com/premiumsupport/ "supporto premium AWS").

{{site.data.keyword.blockchainfull_notm}} non supporta i casi aperti in {{site.data.keyword.cloud_notm}} e correlati a {{site.data.keyword.blockchainfull_notm}} Platform for AWS. La Community Edition è progettata per l'esplorazione, lo sviluppo e l'esecuzione di test; non utilizzarla per la produzione.

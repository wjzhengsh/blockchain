---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Panoramica sul componente Blockchain

I componenti e la struttura di {{site.data.keyword.blockchainfull}} Platform (IBP) si basano sugli strumenti e l'infrastruttura sottostanti di [Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/), una soluzione blockchain con autorizzazioni open source di cui {{site.data.keyword.IBM_notm}} è uno dei principali collaboratori. Le reti basate su Fabric includono diversi componenti standard che possono essere distribuiti in alcune configurazioni per supportare un'ampia varietà di casi di utilizzo.

Per una visione più completa delle reti Fabric e dell'interrelazione dei componenti che la comprendono, consulta [questo documento sulla struttura di una rete blockchain](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) dalla documentazione della community Fabric che mostra come una rete può essere avviata e fatta evolvere.

Per una panoramica di alto livello dei componenti in una rete basata su Fabric, consulta il seguente video:

<iframe class="embed-responsive-item" id="youtubeplayer" title="Video del piano Starter" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Sebbene questo video si concentra sui componenti dalla prospettiva delle reti Starter e Enterprise, le informazioni sono ancora altamente pertinenti per la soluzione gestita dal cliente di IBP on ICP (IBM Cloud Platform).*

Ai fini di questa panoramica, ci concentreremo solo sulle autorità di certificazione (CA, Certificate Authority), sugli ordinanti, sui peer, sugli smart contract e sulle applicazioni. Come puoi vedere dall'argomento [Guida di distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp_for_icp_deployment_guide.html), questa sequenza non è arbitraria; rispecchia la sequenza in cui i componenti in una rete basata su Fabric saranno distribuiti.

## Autorità di certificazione (CA, Certificate Authority) 
{: #ca}

La base di una rete blockchain basata su Fabric sono le identità e le autorizzazioni. Le identità prendono la forma di certificati x.509 emessi da una CA e sono come una carta di credito in cui *identificano* qualcuno e possono includere attributi ad esso relativi. Questi certificati vengono poi collegati alle autorizzazioni tramite la loro inclusione nelle cartelle MSP al livello di componente o canale. Quindi, ad esempio, una MSP peer avrà una sottocartella MSP denominata **admins**. Ogni utente i cui certificati sono all'interno della cartella admin è quindi un amministratore del peer, il che significa che ha la possibilità di eseguire tutte le azioni che possono essere effettuate dall'amministratore di tale peer. Un sistema di convalida all'interno del peer esegue un controllo ogni volta che un utente, identificato dal proprio certificato di firma, prova ad eseguire un'azione di gestione. Il certificato corrisponde a quello nella cartella "admin"? Se corrisponde, l'azione può essere eseguita. Se non corrisponde, la richiesta per eseguire l'azione sarà rifiutata.

Le CA {{site.data.keyword.blockchainfull_notm}} si basano sull'[Hyperledger Fabric-CA](https://hyperledger-fabric-ca.readthedocs.io/en/latest/), sebbene sia possibile utilizzare un'altra CA finché utilizza una PKI basata sui certificati x.509. Ci possono essere e normalmente dovrebbe essere così, più livelli di CA. La "CA root" per una rete non sarà normalmente esposta tranne che per fornire i certificati alle "CA intermedie", che emetteranno i certificati direttamente agli utenti e ai componenti o a più livelli di CA intermedie. Per ulteriori dettagli su come vengono utilizzate le autorità di certificazione per stabilire l'identità e l'appartenenza, consulta la [documentazione di Hyperledger Fabric sull'identità ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) e sull'[appartenenza ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html)

## Ordinanti
{: #orderer}

Mentre viene spesso fatto riferimento al servizio di ordinazione come al "cuore" di una rete, la sua funzione è al momento piuttosto semplice: ordinare le transazioni in blocchi e restituirle ai peer per essere scritte nei loro libri mastri. Nelle precedenti versioni di Fabric, questa funzionalità era stata integrata all'interno del peer, ma a partire da Fabric v1.0, è stata divisa in un componente separato per aumentare le prestazioni del peer e ridurre le anomalie che potrebbero causare fork di stato potenziali.

A un livello fisico, questa funzione di ordinazione normalmente richiede una serie di ordinanti collettivamente conosciuti come "servizio di ordinazione", anche se negli ambienti di test o POC è possibile utilizzare un nodo singolo (noto come ordinante SOLO).

## Peer
{: #peer}

A un livello fisico, una rete blockchain è formata principalmente da nodi peer (o semplicemente peer). I peer sono un elemento fondamentale della rete perché ospitano i libri mastri e gli smart contract (contenuti nei "chaincode"). Più precisamente, i peer ospitano le **istanze** del libro mastro e le **istanze** degli smart contract. Poiché gli smart contract e i libri mastri vengono utilizzati, rispettivamente, per incapsulare i processi condivisi e le informazioni condivise in una rete, questi aspetti di un peer li rendono un buon punto di partenza per comprendere cosa faccia realmente una rete Fabric.

Per maggiori informazioni specifiche sui peer, consulta [questo documento che si concentra solo sui peer](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html) dalla documentazione della community Fabric.

## Smart contract

Prima che le aziende di business possano negoziare tra loro, una comprensione comune sulle regole e sui processi deve essere raggiunta e definita in una sorta di contratto. Presi insieme, questi contratti definiscono il "modello di business" che regola tutte le interazioni tra i partner di business.

Esiste la stessa necessità nelle reti blockchain e il termine tecnico per questi modelli di business è "smart contract", Fabric (e IBP) contiene questi contratti in una struttura più grande nota come "chaincode", che include non solo la logica di business ma anche l'infrastruttura sottostante che convalida le identità degli utenti che provano a richiamare lo smart contract.

Nel mondo degli affari i contratti sono firmati e depositati tramite studi legali, mentre gli smart contract sono installati sui peer e "istanziati" su un canale.

## Applicazioni

Le applicazioni client in una rete basata su Fabric come IBP, utilizzano le infrastrutture sottostanti come le API, gli SDK e gli smart contract per consentire le interazioni tra i client (richiami e query) a un livello superiore di astrazione.

Per dare un'occhiata a come le applicazioni interagiscono con una rete basata su Fabric, consulta [documentation on writing your first application ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "Writing Your First Application") e [developing application section ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "Developing Applications").

Tieni presente che l'ultimo link fa riferimento a delle funzionalità di Fabric, come ad esempio la classe `Contract`, che non sono compatibili con Fabric v1.2, su cui si basano il piano Starter e la release di IBP on ICP. Tuttavia, la sezione contiene informazioni concettuali e altri suggerimenti utili che vale la pena tenere a mente quando sviluppi le applicazioni che si interfacceranno con i componenti Fabric v1.2.

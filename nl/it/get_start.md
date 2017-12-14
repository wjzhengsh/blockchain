---

copyright:
  years: 2017
lastupdated: "2017-12-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Governance della rete
{: #getting-started-with-blockchain}

{{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.Bluemix_notm}} fornisce una rete {{site.data.keyword.blockchain}} con sicurezza, integrità, scalabilità e prestazioni elevate. Puoi fornire rapidamente una rete {{site.data.keyword.blockchain}} pienamente funzionante e utilizzare il Network Monitor per eseguire immediatamente chaincode e applicazioni senza dover progettare e configurare una rete blockchain privata. 
{:shortdesc}
 
Questa esercitazione introduttiva presenta i prerequisiti e i passi a cui dovrai attenerti per ottenere una rete {{site.data.keyword.blockchain}} ospitata in un ambiente altamente disponibile e sicuro di IBM.  

I seguenti passi indicano il flusso di base per avviare una rete {{site.data.keyword.blockchain}} con molteplici organizzazioni membro:
1. Un **iniziatore di rete**, in quanto tipo speciale di membro di rete, crea la rete {{site.data.keyword.blockchain}} e definisce le politiche di governance. L'iniziatore di rete può quindi invitare altre istituzioni ad aderire a questa rete {{site.data.keyword.blockchain}} come membri della rete.  Per ulteriori dettagli, vedi [Creazione di una rete](#creating-a-network).
2. I **membri della rete** ricevono una notifica email che fornisce le istruzioni per consentire loro di aderire a una rete {{site.data.keyword.blockchain}}. Oltre alle istruzioni nella notifica email, puoi anche attenerti alla procedura indicata in [Adesione a una rete](#joining-a-network).
3. Tutti i **membri della rete**, dopo aver creato, o aver aderito a, una rete, possono accedere al Network Monitor, che è un dashboard GUI, per configurare e gestire le loro risorse di rete. Puoi configurare i canali con un gruppo di membri della rete per eseguire transazioni private su un libro mastro specifico per il canale, a cui possono accedere solo i membri del canale. Nel Network Monitor, puoi anche indicare l'adesione dei tuoi peer al canale e installare e istanziare il chaincode su di essi. Per ulteriori dettagli, vedi [Configurazione delle risorse di rete e dell'ambiente](#configuring-network-resources-and-environment).
4. Gli **sviluppatori di applicazioni** creano delle applicazioni che abilitano l'interazione con la rete {{site.data.keyword.blockchain}}. Per ulteriori informazioni, vedi [Abilitazione delle applicazioni a interagire con la rete](#enabling-applications-to-interact-with-the-network).
5. Gli **operatori di rete** monitorano le transazioni sui loro canali utilizzando il Network Monitor. Per ulteriori dettagli, vedi [Monitoraggio delle risorse di rete](#monitoring-network-resources).

## Creazione di una rete
Prima di iniziare, devi creare un'istanza del servizio di [{{site.data.keyword.blockchain}} Platform ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain) su {{site.data.keyword.Bluemix_notm}}. Dovrai eseguire l'accesso con il tuo ID {{site.data.keyword.Bluemix_notm}}. Se non hai un ID, fai clic sul pulsante **Registrati per creare**.  Rinomina i nomi di servizio e credenziali per la tua istanza in modo che tu possa riconoscerli facilmente in futuro. Seleziona la regione, l'organizzazione e lo spazio {{site.data.keyword.Bluemix_notm}} dove puoi distribuire la tua rete {{site.data.keyword.blockchain}}. Seleziona quindi **Piano Enterprise** dalla tabella dei piani di determinazione del prezzo e fai clic sul pulsante **Crea**.  

Puoi trovare la tua istanza del servizio {{site.data.keyword.blockchain}} Platform nel tuo [Dashboard servizio {{site.data.keyword.Bluemix_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} dashboard servizio").  

Se sei un iniziatore di rete, fai clic sul pulsante **Create Network** per iniziare una rete {{site.data.keyword.blockchain}}.  Attieniti alla procedura guidata per completare la configurazione di base della tua rete e delle risorse.  
![Procedura guidata Create Network](images/create_network_name.png "Procedura guidata Create Network")  

1. Nella schermata "Let's Get Started", dai un nome alla tua rete, scegli l'ubicazione dell'organizzazione {{site.data.keyword.Bluemix_notm}} e aggiungi il nome della tua istituzione. Quando inviti altri membri della rete, cercheranno questo nome di rete per aderire. Fai clic su **Next**.
2. (Facoltativo) Nella schermata "Invite Members", immetti il nome dell'istituzione e l'indirizzo email del membro che vuoi invitare alla tua rete. Il nome dell'istituzione da te designato non è un titolo ufficiale. Consente semplicemente all'istituzione di essere facilmente riconosciuta e può essere modificato quando aderisce alla rete. Nota: una rete può avere fino a 15 membri, incluso te. Questo passo è facoltativo e puoi invitare i membri alla tua rete in un secondo momento nel Network Monitor.  Fai clic su **Next**.
	I membri che inviti riceveranno una notifica email relativa al loro invito dopo che hai completato tutti i passi per creare la rete.
3. Nella schermata "Define Governance Rules", stabilisci le politiche per l'adesione, la creazione del canale e il chaincode. Per impostazione predefinita, tutti i membri della rete possono invitare altri membri ad aderire alla rete, creare canali e istanziare chaincode. Per questo GA, la tua rete utilizza le politiche di governance predefinite.  Fai clic su **Next**.
4. Nella schermata "Review Summary", verifica la tua configurazione di rete. Se vuoi apportare modifiche, fai clic su **Edit** accanto all'intestazione della sezione oppure fai clic sul pulsante **Previous** per tornare alle schermate precedenti. Una volta che hai completato la configurazione della rete, fai clic su **Done**.  
5. Nella schermata "Network Created", verrai informato che la tua rete è stata creata correttamente. Puoi fare clic su **Add Peers** per configurare le tue risorse di rete oppure puoi fare clic su **Enter Monitor** direttamente per aprire il Network Monitor. Se non aggiungi prima dei peer, puoi aggiungerli in un secondo momento nel Network Monitor.  Per ulteriori informazioni sui peer, vedi [Overview](v10_dashboard.html#overview).
    
Ora sei pronto a distribuire una rete {{site.data.keyword.blockchain}} che può supportare le seguenti risorse di rete:  
* Un'Autorità di certificazione (CA, Certificate Authority) specifica per il membro
* Delle politiche di governance predefinite
* Fino a 15 membri della rete  
* Tre ordinatori e due nodi CA intermedi
* Fino a tre piccoli peer per ogni membro   
* Un servizio ordini con tolleranza di errori anomali
* Fino a 150 canali
* Fino a 10 istanziazioni di chaincode


## Adesione a una rete
Analogamente alla creazione di una rete, devi creare un'istanza del servizio di [{{site.data.keyword.blockchain}} Platform ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain) su {{site.data.keyword.Bluemix_notm}}. Dovrai eseguire l'accesso con il tuo ID {{site.data.keyword.Bluemix_notm}}. Se non hai un ID, fai clic sul pulsante **Registrati per creare**.  Rinomina i nomi di servizio e credenziali per la tua istanza in modo che tu possa riconoscerli facilmente in futuro. Seleziona la regione, l'organizzazione e lo spazio {{site.data.keyword.Bluemix_notm}} dove puoi distribuire la tua rete {{site.data.keyword.blockchain}}. Seleziona quindi **Piano Enterprise** dalla tabella dei piani di determinazione del prezzo e fai clic sul pulsante **Crea**.  

Potrai trovare la tua istanza del servizio {{site.data.keyword.blockchain}} Platform nel [Dashboard servizio {{site.data.keyword.Bluemix_notm}} ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} Dashboard servizio"). 

Se sei un membro della rete invitato, fai clic sul pulsante **Pending Invite ->**, seleziona la rete a cui vuoi aderire dall'elenco a discesa e fai clic sul pulsante**Join Network!**. Attieniti alla procedura guidata per visualizzare la configurazione di base della tua rete e configurare i tuoi componenti di rete.  
![Procedura guidata Join Network](images/join_network_name.png "Procedura guidata Join Network")  

1. Nella schermata "Let's Get Started", aggiungi il nome della tua istituzione e verifica l'ubicazione della tua organizzazione {{site.data.keyword.Bluemix_notm}}. Fai clic su **Next**.
2. Nella schermata "Review Governance Rules", visualizza le politiche di governance della rete di adesione, creazione del canale e chaincode. Fai clic su **Next**.
3. (Facoltativo) Nella schermata "Add Peers", scegli la dimensione e la quantità di peer che vuoi aggiungere. Fai clic su **Next**. Ciascun membro in una rete può aggiungere fino a 3 peer; in questo momento, sono disponibili solo "piccoli" peer. Questo passo è facoltativo e puoi aggiungere i tuoi peer in un secondo momento nel Network Monitor. Per ulteriori informazioni sui peer, vedi [Overview](v10_dashboard.html#overview).
4. Nella schermata "Review Network Summary", verifica la configurazione di rete. Se vuoi apportare modifiche, fai clic su **Edit** accanto all'intestazione della sezione oppure fai clic sul pulsante **Previous** per tornare alle schermate precedenti. Una volta che hai completato la configurazione delle risorse, fai clic su **Done**. Sarai informato che la tua adesione alla rete è riuscita. Puoi quindi fare clic su **Enter Monitor** per aprire il Network Monitor o fare clic su **Create a Channel** per iniziare una richiesta di creazione di canale. Puoi creare i canali in un secondo momento nel Network Monitor. Per ulteriori informazioni, vedi [Canali](v10_dashboard.html#channels).
 

## Configurazione delle risorse di rete e dell'ambiente

1. Accedi al tuo Network Monitor dopo che hai creato, o hai aderito a, una rete {{site.data.keyword.blockchain}}. Il Network Monitor è un dashboard GUI dove puoi gestire le informazioni sullo stato della rete e tenerne traccia. Per ulteriori informazioni, consulta [Network Monitor](v10_dashboard.html).
2. Aggiungi i tuoi peer alla rete. Se già hai aggiunto abbastanza peer, tralascia questo passo. I peer eseguono il chaincode e sono l'endpoint per interagire con le tue applicazioni. Gai clic su **Add Peers** nella schermata "Overview" e seleziona la quantità e la dimensione dei tuoi peer. Per ulteriori informazioni, vedi [Overview](v10_dashboard.html#resources).
3. Configura un canale. A tutti i membri nello stesso canale viene fornito un libro mastro specifico per il canale, che offre isolamento e confidenzialità dei dati. Per informazioni su come creare un canale, vedi [Creazione di un canale](howto/create_channel.html#creating-a-channel).  
	
	Se sei un membro del canale che è stato invitato ad aderire a un canale, riceverai una notifica email con un link alla procedura guidata che ti consentirà di aderire al canale. 
4. Unisci i peer al canale.  Solo i peer associati al canale possono accedere al suo libro mastro. Per ulteriori informazioni, vedi [Canali](v10_dashboard.html#channels).
5. Installa e istanzia il chaincode. Tutti i membri del canale devono installare lo stesso chaincode con lo stesso nome e la stessa versione su ogni peer che eseguirà il chaincode. Dopo l'installazione, il chaincode deve essere istanziato sul canale prima di poter essere usato. Per ulteriori informazioni, vedi [Installazione e istanziazione di un chaincode](howto/install_instantiate_chaincode.html).  

**Nota**: per ottenere un'elevata disponibilità, ciascuna istituzione deve acquistare almeno 2 peer e, in un canale, ogni istituzione partecipante deve unire almeno 2 peer.

## Abilitazione delle applicazioni a interagire con la rete
Le applicazioni si avvalgono delle API SDK per interagire con i tuoi componenti di rete {{site.data.keyword.blockchain}}. Devi aggiungere le informazioni sull'endpoint API dei tuoi componenti di rete nella tua applicazione in modo che l'applicazione possa alla fine avere come obiettivo i tuoi peer le richieste di transazione. Puoi quindi aggiungere le informazioni sull'endpoint API dal Network Monitor. Le applicazioni possono essere ospitate sul tuo file system locale o su {{site.data.keyword.Bluemix_notm}}. Per ulteriori informazioni, vedi [Sviluppo di applicazioni](v10_application.html).

## Monitoraggio delle risorse di rete  
Dopo che una transazione è stata attivata dalla tua applicazione, puoi visualizzare le informazioni sullo stato della transazione nel Network Monitor. Per ulteriori informazioni sul monitoraggio di rete, vedi [Monitoraggio di una rete](howto/monitor_network.html).
  
## Uscire da una rete 
Se desideri uscire da una rete, elimina l'istanza del servizio blockchain dal tuo dashboard {{site.data.keyword.Bluemix_notm}}.  

**Nota**: prima di uscire da una rete, assicurati di non essere un membro dei canali della rete. Altrimenti, riscontrerai degli errori quando esci dalla rete. Una rimozione di un membro del canale deve completare il processo di aggiornamento del canale. Per ulteriori informazioni sul processo di aggiornamento del canale, consulta [Aggiornamento di un canale](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.0, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

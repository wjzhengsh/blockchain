---

copyright:
  years: 2017
lastupdated: "2017-12-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Operare la rete
{: #v10_dashboard}


Il Network Monitor fornisce una panoramica del tuo ambiente blockchain, compresi componenti di rete, membri, canali oggetto di adesione, dati delle prestazioni e chaincode distribuiti.
{:shortdesc}

Il Network Monitor presenta le seguenti schermate:
* Nella schermata "Overview", puoi visualizzare le credenziali del servizio di rete, le informazioni sullo stato dei componenti e aggiungere i peer.
* Nella schermata "Members", puoi gestire i certificati e i membri della rete.
* Nella schermata "Channels", puoi creare dei nuovi canali e visualizzare informazioni sui canali esistenti.
* Nella schermata "Chaincode", puoi installare e istanziare i chaincode sui tuoi peer.
* Nella schermata "Notifications", puoi gestire le approvazioni in sospeso e visualizzare le approvazioni completate.
* Nella schermata "Support", puoi trovare i link per le risorse di riferimento e vedere le funzioni nuove e modificate in ciascuna release.


## Overview

Lo schermo "Overview" visualizza le informazioni sullo stato in tempo reale relative ai componenti blockchain, compresi l'ordinante, la CA e i nodi peer. Ogni componente viene visualizzato in quattro intestazioni distinte: **Type**, **Name**, **Status** e **Actions**. Durante la creazione della tua rete blockchain, vengono automaticamente creati tre nodi ordinante e due nodi CA.  Le CA sono specifiche per i membri, mentre i nodi di ordine sono endpoint comuni condivisi nella rete.

La **Figura 1** mostra la schermata "Overview":

![Schermata Overview](images/myresources.png "Panoramica della rete")
*Figura 1. Panoramica della rete*

- Azioni nodo

  L'intestazione **Actions** della tabella fornisce i pulsanti per avviare o arrestare i tuoi componenti. Puoi anche avviare o arrestare un gruppo di nodi selezionando più nodi e facendo quindi clic sul pulsante **Start Selected** o **Stop Selected**. Il pulsante **Start Selected** o quello **Stop Selected** sono visualizzati sopra la tabella quando selezioni uno o più nodi.

  Puoi anche controllare i log componenti facendo clic su **View Logs** dall'elenco a discesa sotto l'intestazione **Actions**. I log espongono le chiamate di procedura remota che si verificano tra i diversi componenti di rete e sono utili per eseguire il debug e risolvere i problemi. Fai una prova, ad esempio, arrestando un peer e provando a sceglierlo come obiettivo di una transazione; vedrai degli errori di connettività gRPC. Quando riavvii il peer e tenti nuovamente la transazione, vedrai una connessione stabilita correttamente. Puoi anche
lasciare inattivo il peer per un periodi di tempo prolungato mentre il tuo canale continua a interagire con il libro mastro. Quando il peer
verrà di nuovo riattivato, noterai una sincronizzazione del libro mastro attraverso il protocollo gossip. Appena il peer ha completamente sincronizzato il registro, puoi eseguire i richiami e le query normali.  
- Service Credentials  
  Puoi visualizzare il file JSON in merito alle informazioni di rete di basso livello di ciascun componente facendo clic sul pulsante **Service Credentials** nella parte superiore destra della scheda "Resources". Queste sono tutte le informazioni di configurazione di cui avrai bisogno
per un'applicazione. Nota, tuttavia, che questo file contiene solo gli indirizzi per i tuoi specifici componenti e i nodi di ordine condivisi. Se hai bisogno di avere come obiettivo ulteriori peer, dovrai ottenerne gli endpoint.
  L'intestazione che contiene "url" visualizza l'endpoint API di ciascun componente. Questi endpoint sono necessari per
avere come obiettivo specifici componenti di rete da un'applicazione lato client e le loro definizioni si trovano solitamente
in un file di configurazione di tipo JSON che accompagna l'applicazione. Se stai personalizzando un'applicazione
che richiede l'approvazione da peer che non fanno parte della tua organizzazione, dovrai recuperare gli
indirizzi IP di questi peer dagli operatori pertinenti in un'operazione fuori banda. I client devono riuscire a connettersi a
un qualsiasi peer da cui necessitano di una risposta.  
- Aggiungi peer  
  Fai clic sul pulsante **Add Peers** nella parte superiore destra per aggiungere dei nodi peer alla tua rete. Ogni membro può aggiungere fino a tre peer in una rete. Puoi aggiungere nodi peer per la prima volta quando crei una rete o aderisci a essa oppure in un secondo momento nel Network Monitor.   
  Nel pannello "Add Peers" a comparsa, seleziona il numero e la dimensione dei nodi peer che vuoi aggiungere.  Attualmente sono disponibili solo dei "piccoli" peer per l'acquisto; tuttavia, saranno eventualmente disponibili dei peer "medi" e "grandi" per soddisfare carichi di lavoro più grandi e un throughput delle transazioni più elevato.  I dettagli sulle dimensioni dei peer e sulle metriche delle prestazioni saranno disponibili a breve...
  
## Members

La schermata "Members" contiene due schede per visualizzare le informazioni sui membri della rete nella scheda "Members" e le informazioni sui certificati nella scheda "Certificates".

La **Figura 2** mostra la schermata "Members" iniziale che visualizza i membri della tua rete nella schede "Members":

![Scheda Members nella schermata Members](images/monitor_members.png "Membri della rete")
*Figura 2. Membri della rete*

Oltre ai membri che inviti quando crei la rete, puoi invitare altri membri nella scheda "Members". Per invitare un membro alla tua rete, immettere il nome dell'istituzione e l'indirizzo email dell'operatore e fai clic su **Add Member**. Una rete può avere un totale di 15 membri (compreso l'iniziatore della rete). Per rimuovere un membro dalla tua rete, fai clic sul simbolo di "rimozione" alla fine della riga del membro.

La **Figura 3** mostra la schermata "Members" iniziale che visualizza i certificati dei membri nella scheda "Certificates":

![Scheda Certificates nella schermata Members](images/monitor_certificates.png "Certificati")
*Figura 3. Certificati*

Gli operatori possono gestire i certificati per i membri nella stessa istituzione nella scheda "Certificates". Fai clic su **Add Certificate** per aprire il pannello "Add Certificate". Dai un nome al tuo certificato, incolla i certificati lato client in formato PEM nel campo "Key" e fai clic su **Submit**. Devi riavviare i peer prima che i certificati lato client possano diventare effettivi.

Per informazioni sulla generazione della tua chiave di certificato, vedi [Generazione dei certificati lato client](v10_application.html#generating-the-client-side-certificates).

## Channels

Puoi suddividere la tua rete in canali, dove ogni canale rappresenta un sottoinsieme di membri autorizzati a visualizzare i dati per i chaincode istanziati su tale canale. Ogni rete deve avere almeno un canale perché le transazioni possano avere luogo. Ogni canale ha un libro mastro univoco e gli utenti devono essere correttamente autenticati per effettuare operazioni di lettura/scrittura su questo libro mastro. Se non ti trovi su un canale, non puoi vedere alcun dato.

La **Figura 4** mostra la schermata dashboard iniziale che visualizza una panoramica di tutti i canali nella tua rete.

![Canali](images/channels.png "Canali")
*Figura 4. Canali*

La creazione di un canale comporterà la generazione di un libro mastro specifico per il canale. Per ulteriori informazioni, vedi [Creazione di un canale](howto/create_channel.html).

Puoi anche selezionare un canale esistente per visualizzare dei dettagli più precisi sul canale, l'adesione e i chaincode attivi. Per ulteriori informazioni, vedi [Monitoraggio di una rete](howto/monitor_network.html).  


## Chaincode

Il chaincode definisce la logica di business e le istruzioni transazionali per la creazione e la modifica di risorse.

La **Figura 5** mostra la schermata del dashboard iniziale dei chaincode:

![Chaincode](images/chaincode_install_overview.png "Chaincode")
*Figura 5. Chaincode*

Il chaincode viene prima installato sul filesystem di un peer e quindi istanziato su un canale.  Per ulteriori informazioni, vedi [Installazione e istanziazione di un chaincode](howto/install_instantiate_chaincode.html).


## Notifications

Puoi gestire le richieste in sospeso e visualizzare le richieste completate nella schermata "Notifications". 

La **Figura 6** mostra la schermata "Notifications":

![Notifiche](images/notifications.png "Notifiche")
*Figura 6. Notifiche*

Quando crei un canale o viene invitato a un nuovo canale, nel Network Monitor comparirà una notifica. 

Le richieste sono raggruppate nelle sottoschede "All", "Pending" e "Completed". I numeri dopo l'intestazione della sottoscheda indicano il numero di richieste in ciascuna sottoscheda.
   * Puoi trovare tutte le tue richieste nella sottoscheda "All".
   * Le richieste che non hai accettato o rifiutato, o che non hai visualizzato, sono nella sottoscheda "Pending". Fai clic sul pulsante **Review Request** per visualizzare la richiesta, che include la politica e i membri del canale e lo stato di voto. Se sei un operatore del canale, puoi accettare **Accept** o declinare **Decline** la richiesta o gestirla in un altro momento facendo clic su **Later**. Se la richiesta viene accettata da un numero sufficiente di operatori del canale, puoi fare clic su **Submit Request** per attivare l'aggiornamento del canale. 
   * Una richiesta inoltrata sarà visualizzata nella sottoscheda "Completed".  Puoi fare clic su **Review Request** per visualizzarne i dettagli.
  
Quando hai un lungo elenco di richieste, puoi cercare una richiesta nel campo di ricerca in alto. 

Le richieste in sospeso possono essere eliminate selezionando le caselle davanti a esse e facendo clic su **Delete Request**. Nota: una richiesta completata non può essere eliminata.


## Support

La schermata "Support" contiene due schede per fornire le informazioni di supporto nella scheda "Support" e per descrivere le funzioni nuove e modificate di ciascuna release nella scheda "Release Notes".

Utilizza i link e le risorse in questa pagina per accedere ai forum di supporto e risoluzione dei problemi. Se non riesci a eseguire il debug del tuo problema o a trovare una risposta alla tua domanda, fai clic sul link **Open an {{site.data.keyword.Bluemix_notm}} support ticket** e segui le istruzioni per inoltrare dei ticket.

La **Figura 7** mostra la schermata "Support" iniziale che visualizza le informazioni di supporto nella scheda "Support":

![Supporto](images/support.png "Supporto")
*Figura 7. Supporto di blockchain*

* [{{site.data.keyword.blockchainfull_notm}} Service docs](index.html), che è il sito della documentazione, fornisce istruzioni introduttive a {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.Bluemix_notm}}. Puoi trovare gli argomenti corrispondenti dal navigator oppure cercare qualsiasi termine con la funzione della ricerca in alto.  
* [IBM Developer Works ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://developer.ibm.com/blockchain/) in **COMMUNITY HELP** contiene le risorse e le informazioni per gli sviluppatori.  
* [IBM dWAnswers ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://developer.ibm.com/answers/smartspace/blockchain/) in **SUPPORT TICKET** funge da piattaforma per domande e risposte. Puoi cercare risposte dalle domande fatte in precedenza o inoltrare una nuova domanda. Assicurati di includere **blockchain** nella domanda.   
  Puoi anche inoltrare un ticket al team di supporto {{site.data.keyword.blockchainfull_notm}} con l'opzione [Open a {{site.data.keyword.Bluemix_notm}} support ticket ![Icona link esterno](images/external_link.svg "Icona link esterno")]().  Condividi dettagli e frammenti di codice dalla tua istanza specifica {{site.data.keyword.Bluemix_notm}}.  
* [Le applicazioni di esempio ![Icona link esterno](images/external_link.svg "Icona link esterno")]() in ** {{site.data.keyword.blockchain}} SAMPLE APPLICATIONS** forniscono assistenza e frammenti di codice di esempio per agevolare lo sviluppo di applicazioni.  
* [Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/) e la [community Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")]() in **Hyperledger Fabric** forniscono ulteriori dettagli sullo stack Hyperledger Fabric.  
  Parla a un [esperto di Hyperledger ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://chat.hyperledger.org/channel/general) se hai delle domande sul codice Hyperledger Fabric.   
  
  
La **Figura 8** mostra la schermata "Members" iniziale che visualizza le funzioni nuove e modificate di ciascuna release nella scheda "Release Notes":

![Note sulla release](images/releasenotes.png "Note sulla release")
*Figura 8. Note sulla release*


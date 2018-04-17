---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Gestione della rete Starter Plan
{: #operate-starter-plan-network}

{{site.data.keyword.blockchainfull}} Platform offre un Network Monitor per fornire una panoramica del tuo ambiente blockchain, compresi risorse di rete, membri, canali oggetto di adesione, dati delle prestazioni delle transazioni e chaincode distribuiti. Il Network Monitor ti offre anche l'entry point per eseguire le API Swagger, sviluppare una rete con {{site.data.keyword.blockchainfull_notm}} Platform: Develop e provare le applicazioni di esempio.
{:shortdesc}

Il Network Monitor presenta le seguenti schermate in tre sezioni. Puoi passare a ciascuna schermata dal navigator a sinistra nel Network Monitor.
- La sezione **My network** contiene le schermate "[Overview](#overview)", "[Members](#members)", "[Channels](#channels)", "[Notifications](#notifications)" e "[APIs](#apis)".
- La sezione **My code** contiene le schermate "[Write code](#write_code)", "[Install code](#chaincode)" e "[Try samples](#samples)".
- La schermata "[Get help](#support)".

Puoi [passare da un'organizzazione a un'altra](#switch_organizations) che possiedi e [reimpostare la tua rete](#reset_network) dal menu a discesa nell'angolo superiore destro del Network Monitor.

Questa esercitazione descrive ciascuna delle schermate e delle funzioni di cui sopra.

## Overview
{: #overview}

La schermata "Overview" visualizza le informazioni sullo stato in tempo reale relative alle risorse blockchain, compresi l'ordinante, la CA e il peer. Ogni risorsa viene visualizzata in quattro intestazioni distinte: **Type**, **Name**, **Status** e **Actions**. Quando la tua rete viene avviata, un ordinante, una CA e un peer sono in esecuzione. La CA è specifica per l'organizzazione, mentre l'ordinante è un endpoint comune condiviso nella rete.

La **Figura 1** mostra la schermata "Overview":

![Schermata Overview](images/myresources_starter.png "Panoramica della rete")
*Figura 1. Panoramica della rete*

### Azioni dei nodi
  L'intestazione **Actions** della tabella fornisce i pulsanti per avviare o arrestare i tuoi componenti. Puoi anche avviare o arrestare un gruppo di nodi selezionando più nodi e facendo quindi clic sul pulsante **Start Selected** o **Stop Selected**. Il pulsante **Start Selected** o quello **Stop Selected** sono visualizzati sopra la tabella quando selezioni uno o più nodi.

  Puoi anche controllare i log componenti facendo clic su **View Logs** dall'elenco a discesa sotto l'intestazione **Actions**. I log presentano le chiamate tra le diverse risorse di rete e sono utili per il debug e la risoluzione dei problemi. Fai una prova, ad esempio, arrestando un peer e provando a sceglierlo come obiettivo di una transazione; vedrai degli errori di connettività. Quando riavvii il peer e tenti nuovamente la transazione, vedrai una connessione stabilita correttamente. Puoi anche
lasciare inattivo il peer per un periodo di tempo prolungato mentre i tuoi canali continuano a interagire con il libro mastro. Quando il peer viene riattivato, noterai una sincronizzazione del libro mastro poiché riceve i blocchi di cui era stato eseguito il commit mentre era inattivo. Dopo che il libro mastro sarà stato completamente sincronizzato, puoi eseguire su di esso richiami e query normali.

### Connection Profile
  Puoi visualizzare il file JSON relativo alle informazioni di rete di basso livello di ciascuna risorsa facendo clic sul pulsante **Connection Profile**. Il profilo di connessione contiene tutte le informazioni di configurazione di cui hai bisogno per un'applicazione. Tuttavia, poiché questo file contiene solo gli indirizzi per i tuoi specifici componenti e l'ordinante, se hai bisogno di avere come obiettivo ulteriori peer devi ottenere i loro endpoint. L'intestazione che contiene "url" visualizza l'endpoint API di ciascun componente. Questi endpoint sono necessari per
avere come obiettivo specifici componenti di rete da un'applicazione lato client e le loro definizioni si trovano solitamente
in un file di configurazione di tipo JSON che accompagna l'applicazione. Se stai personalizzando un'applicazione che richiede l'approvazione da peer che non fanno parte della tua organizzazione, dovrai recuperare gli indirizzi IP di questi peer dagli operatori pertinenti in un'operazione fuori banda. I client devono riuscire a connettersi a
un qualsiasi peer da cui necessitano di una risposta.

### Add peers
{: #peers}
  I membri della rete devono avere dei peer per memorizzare le loro copie del libro mastro di rete e per eseguire il chaincode per eseguire query del libro mastro o per aggiornarlo. Se la politica di approvazione definisce un peer come peer di approvazione, il peer restituisce anche i risultati dell'approvazione alle applicazioni.

  Starter Plan crea un peer per ciascuna delle due organizzazioni per impostazione predefinita. Puoi aggiungere altri peer per le tue organizzazioni in base ai tuoi requisiti. Ti potresti trovare in scenari differenti in cui hai bisogno di più peer. Ad esempio, potresti volere più peer che si uniscono allo stesso canale per la ridondanza. Ogni peer elabora le transazioni del canale e scrive nelle loro rispettive copie del libro mastro. In caso di malfunzionamento di uno dei peer, l'altro peer (o molteplici altri peer) possono continuare ad elaborare transazioni e richieste dell'applicazione. Puoi anche bilanciare simmetricamente il carico di tutte le richieste di applicazione tra i peer o potresti indicare come obiettivo peer differenti per funzioni differenti. Puoi ad esempio usare un peer per eseguire query del libro mastro e utilizzare un altro peer per elaborare le approvazioni per gli aggiornamenti del libro mastro.

    Fai clic sul pulsante **Add Peers** nella parte superiore destra per aggiungere dei nodi peer alla tua rete. Nel pannello "Add Peers" a comparsa, seleziona il numero e la dimensione dei nodi peer che vuoi aggiungere.


## Members
{: #members}

La schermata "Members" contiene due schede per visualizzare le informazioni sui membri della rete nella scheda "Members" e le informazioni sui certificati nella scheda "Certificates".

### Members
{: #members_tab}
La **Figura 2** mostra la schermata "Members" iniziale che visualizza i membri della tua rete nella schede "Members":

![Scheda Members nella schermata Members](images/monitor_members_starter.png "Membri della rete")
*Figura 2. Membri della rete*

Fare clic su **Add Member** per invitare più membri alla rete. In Starter Plan, hai due opzioni:
- **Invite a member**. Puoi invitare altre organizzazioni a diventare membri della tua rete. Le organizzazioni invitate possono quindi aderire e collaborare con te nella rete.
- **Create member**. Puoi anche creare un membro utilizzando il tuo indirizzo email. Ne avresti il controllo proprio come lo hai delle due organizzazioni che ricevi per impostazione predefinita con Starter Plan.

La **Figura 3** mostra la finestra "Add member".

![Add member](images/invite_member_starter.png "Add member")
*Figura 3. Add member*

### Certificates
La **Figura 4** mostra la schermata "Members" iniziale che visualizza i certificati dei membri nella scheda "Certificates":

![Scheda Certificates nella schermata Members](images/monitor_certificates_starter.png "Certificati")
*Figura 4. Certificates*

Gli operatori possono gestire i certificati per i membri nella stessa istituzione nella scheda "Certificates". Fai clic su **Add Certificate** per aprire il pannello "Add Certificate". Dai un nome al tuo certificato, incolla i certificati lato client in formato PEM nel campo "Key" e fai clic su **Submit**. Devi riavviare i peer prima che i certificati lato client possano diventare effettivi.

Per ulteriori informazioni sulla generazione della tua chiave di certificato, vedi [Generazione dei certificati lato client](v10_application.html#generating-the-client-side-certificates).


## Channels
{: #channels}

Formati da un sottoinsieme di membri della rete che vogliono eseguire transazioni in privato, i canali forniscono l'isolamento e la confidenzialità dei dati consentendo ai membri di un canale di stabilire delle specifiche regole e un libro mastro separato a cui possono accedere solo i membri del canale. Ogni rete deve avere almeno un canale perché le transazioni possano avere luogo. Ogni canale ha un libro mastro univoco e gli utenti devono essere correttamente autenticati per effettuare operazioni di lettura/scrittura su questo libro mastro. Se non ti trovi su un canale, non puoi vedere alcun dato.

La **Figura 5** mostra la schermata dashboard iniziale che visualizza una panoramica di tutti i canali nella tua rete:

![Canali](images/channels_starter.png "Canali")
*Figure 5. Channels*

La creazione di un canale comporta la generazione di un libro mastro specifico per il canale. Per ulteriori informazioni, vedi [Creazione di un canale](howto/create_channel.html).

Puoi anche selezionare un canale esistente per visualizzare dei dettagli più precisi sul canale, l'adesione e i chaincode attivi. Per ulteriori informazioni, vedi [Monitoraggio di una rete](howto/monitor_network.html).


## Notifications
{: #notifications}

Puoi gestire le richieste in sospeso e visualizzare le richieste completate nella schermata "Notifications".

La **Figura 6** mostra la schermata "Notifications":

![Notifications](images/notifications_starter.png "Notifications")
*Figura 6. Notifications*

Quando crei un canale o viene invitato a un nuovo canale, nel Network Monitor compare una notifica.

Le richieste sono raggruppate nelle sottoschede "All", "Pending" e "Completed". I numeri dopo l'intestazione indicano il numero di richieste in ciascuna sottoscheda.
   * Puoi trovare tutte le tue richieste nella sottoscheda "All".
   * Le richieste che non hai accettato o rifiutato, o che non hai visualizzato, sono nella sottoscheda "Pending". Fai clic sul pulsante **Review Request** per visualizzare la richiesta, che include la politica e i membri del canale e lo stato di voto. Se sei un operatore del canale, puoi accettare **Accept** o declinare **Decline** la richiesta o gestirla in un altro momento facendo clic su **Later**. Se la richiesta viene accettata da un numero sufficiente di operatori del canale, puoi fare clic su **Submit Request** per attivare l'aggiornamento del canale.
   * Una richiesta inoltrata sarà visualizzata nella sottoscheda "Completed".  Puoi fare clic su **Review Request** per visualizzarne i dettagli.

Se hai un lungo elenco di richieste, puoi cercare una richiesta nel campo di ricerca in alto.

Le richieste in sospeso possono essere eliminate selezionando le caselle davanti a esse e facendo clic su **Delete Request**.


## APIs
{: #apis}

Per facilitare lo sviluppo delle applicazioni, {{site.data.keyword.blockchainfull_notm}} Platform presenta delle API che puoi testare sulla tua rete in una IU Swagger.

La **Figura 7** mostra la schermata "APIs":

![APIs](images/API_screen_starter.png "APIs")
*Figura 7. APIs*

Fai clic sul link **Swagger UI** per aprire l'IU Swagger. Nota: prima di eseguire le API, devi autorizzare l'IU Swagger con le tue credenziali di rete (che sono disponibili in questa pagina delle API). Per ulteriori informazioni, vedi [Utilizzo delle API Swagger](howto/swagger_apis.html).


## Write code
{: #write-code}

Starter Plan integra {{site.data.keyword.blockchainfull_notm}} Platform: Develop e fornisce un ambiente di sviluppo con strumenti e tecnologie standard del settore. Puoi sviluppare la tua rete in un ambiente online oppure in locale. Dopo che hai sviluppato una rete, puoi distribuirla nuovamente alla tua rete Starter Plan.

La **Figura 8** mostra la schermata "Write code":

![Write code](images/write_code_starter.png "Write code")
*Figura 8. Write code*

Per ulteriori informazioni sullo sviluppo e sulla distribuzione del tuo codice con Starter Plan, vedi [Sviluppo di reti di business con Starter Plan](develop_starter.html).


## Install code
{: #chaincode}

I chaincode, noti anche come "smart contract", sono parti di software che contengono una serie di funzioni per eseguire query e aggiornamenti del libro mastro. Vengono installati sui peer e istanziati su un canale.

La **Figura 9** mostra la schermata "Install code":

![Install code](images/chaincode_install_overview_starter.png "Install code")
*Figura 9. Chaincode*

Un chaincode viene prima installato sul file system di un peer e viene quindi istanziato su un canale. Per ulteriori informazioni, vedi [Installazione e istanziazione di un chaincode](howto/install_instantiate_chaincode.html).


## Try samples
{: #samples}

Le applicazioni di esempio ti aiutano ad ottenere una migliore comprensione della rete blockchain e dello sviluppo di applicazioni. Starter Plan ti consente di distribuire e avviare applicazioni di esempio nel Network Monitor.

La **Figura 10** mostra la schermata "Samples":

![Try samples](images/sample_overview_starter.png "Try samples")
*Figura 10. Samples*

La distribuzione di esempio si avvale del [servizio DevOps Toolchain ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://console.bluemix.net/devops/toolchains) per automatizzare il tuo processo di controllo origine, delivery pipeline, e abilitare il chaincode. Scegli un'applicazione di esempio e fai clic su **Deploy via Toolchain**. Per ulteriori informazioni, vedi [Distribuzione di applicazioni di esempio](howto/prebuilt_samples.html).


## Get help
{: #support}

La schermata "Get help" contiene due schede per fornire le informazioni di supporto nella scheda "Support" e per descrivere le funzioni nuove e modificate di ciascuna release nella scheda "Release Notes".

La **Figura 11** mostra la schermata "Support" iniziale che visualizza le informazioni di supporto nella scheda "Support":

![Supporto](images/support_starter.png "Supporto")
*Figura 11. Supporto blockchain*

Utilizza i link e le risorse in questa schermata per accedere ai forum di supporto e risoluzione dei problemi.

* [{{site.data.keyword.blockchainfull_notm}} Service docs](index.html) in **Getting started**, che è il sito della documentazione, fornisce istruzioni introduttive a {{site.data.keyword.blockchainfull}} Platform su {{site.data.keyword.Bluemix_notm}}. Puoi trovare gli argomenti corrispondenti dal navigator a sinistra oppure cercare qualsiasi termine con la funzione di ricerca in alto.
* [IBM Developer Works ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://developer.ibm.com/blockchain/) in **COMMUNITY HELP** contiene le risorse e le informazioni per gli sviluppatori.
* [IBM dWAnswers ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://developer.ibm.com/answers/smartspace/blockchain/) in **SUPPORT TICKET** funge da piattaforma per domande e risposte. Puoi cercare risposte dalle domande fatte in precedenza o inoltrare una nuova domanda. Assicurati di includere **blockchain** nella domanda.
  Puoi anche inoltrare un ticket al team di supporto {{site.data.keyword.blockchainfull_notm}} con l'opzione **Open an {{site.data.keyword.Bluemix_notm}} support ticket**.  Condividi dettagli e frammenti di codice dalla tua istanza specifica {{site.data.keyword.Bluemix_notm}}.
* [Le applicazioni di esempio ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://github.com/ibm-blockchain) in **Blockchain sample applications** forniscono assistenza e frammenti di codice di esempio per agevolare lo sviluppo di applicazioni.
* [Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/) e la [community Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://jira.hyperledger.org/secure/Dashboard.jspa) in **Hyperledger Fabric** forniscono ulteriori dettagli sullo stack Hyperledger Fabric.
  Parla a un [esperto di Hyperledger ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://chat.hyperledger.org/channel/general) se hai delle domande sul codice Hyperledger Fabric.

Se non riesci ad eseguire il debug del tuo problema o a individuare una risposta alla tua domanda, inoltra un caso di supporto al portale servizio di IBM Cloud. Per ulteriori informazioni, vedi [Richiesta di assistenza tecnica](ibmblockchain_support.html).

La **Figura 12** mostra la schermata "Support" iniziale che visualizza le funzioni nuove e modificate di ciascuna release nella scheda "Release Notes":

![Note sulla release di Helios](images/releasenotes_helios_starter.png "Note sulla release di Helios")
![Note sulla release di Fabric](images/releasenotes_Fabric_starter.png "Note sulla release di Fabric")
*Figura 12. Note sulla release*


## Passaggio da un'organizzazione all'altra
{: #switch-organizations}

Se simuli tu stesso una rete blockchain con più organizzazioni, puoi passare a qualsiasi organizzazione di tua proprietà, ad esempio Organizzazione A. Puoi quindi visualizzare e gestire le risorse di rete dell'Organizzazione A, quali i peer, i canali e i chaincode nel Network Monitor. Questa funzione ti consente di creare un canale attenendoti alle politiche del canale e di aggiungere dei peer da più organizzazioni al canale.

Fai clic sull'angolo in alto a destra dell'IU, dove dovresti vedere il tuo nome. Dal menu a discesa sotto **SWITCH ORGANIZATION**, scegli il nome dell'organizzazione a cui vuoi passare. L'Organizzazione A è selezionata per impostazione predefinita. Dopo che hai scelto un'organizzazione a cui passare, il tuo Network Monitor viene aggiornato automaticamente e puoi visualizzare la rete come tale organizzazione.

La **Figura 13** mostra la funzione "Switch organization":

![Switch organization](images/switch_orgs.png "Switch organization")
*Figura 13. Switch organization*


## Reimposta la rete
{: #reset-network}

Starter Plan ti consente di modificare la tua configurazione di rete senza eliminare e creare nuovamente una rete. La tua rete viene reimpostata nuovamente sulla configurazione di rete iniziale, che include due organizzazioni, un peer per ciascuna organizzazione e un canale predefinito. Ciò è utile, ad esempio, quando esegui dei cicli di test sulla rete blockchain, poiché ti consente di ricominciare daccapo da una rete relativamente pulita.

**Attenzione**: dopo che reimposti la rete, gli endpoint API dei tuoi peer, del tuo ordinante e della tua CA sono modificati. Devi regolare le informazioni sugli endpoint API nelle tue applicazioni.

Fai clic sull'angolo superiore destro e apri il menu a discesa. Fai clic sul pulsante **Reset Network** nel menu. Se sei pronto a reimpostare la tua rete, fai clic su **OK** per continuare. Il tuo Network Monitor verrà aggiornato per riflettere le tue nuove impostazioni.

La **Figura 14** mostra la funzione "Switch organizations":

![Reset network](images/reset_network.png "Reset Network")
*Figura 14. Reset network*

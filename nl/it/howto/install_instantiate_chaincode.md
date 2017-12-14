---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Installazione e istanziazione di un chaincode

Il chaincode è un software, scritto in Go o Java, che incapsula la logica di business e le istruzioni transazionali
per la creazione e la modifica di risorse nel libro mastro.  Un chaincode viene eseguito in un contenitore docker associato a un qualsiasi peer che deve interagire con esso.  Per ulteriori informazioni sullo sviluppo di chaincode, vedi le [esercitazioni per il chaincode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

Il chaincode viene prima installato sul file system di un peer e viene quindi istanziato su un canale.  Tutti i membri del canale devono installare il chaincode su ogni singolo peer che eseguirà questo chaincode.  Per utilizzare lo stesso chaincode, i membri del canale devono dare al chaincode lo stesso nome e la stessa versione durante l'installazione del chaincode.  Il passo di creazione dell'istanza implica
l'inizializzazione di coppie chiave/valore e la distribuzione del contenitore chaincode.  Tutti i peer, tramite i quali i peer interagiranno con un chaincode, devono avere il chaincode installato sul file system del peer e devono disporre di un contenitore chaincode in esecuzione.  Tuttavia, se un peer utilizza lo stesso chaincode su più canali, ha bisogno di una sola istanza del contenitore chaincode.  

La combinazione di installazione e istanziazione è una funzione potente in quanto consente a un peer di interagire con lo stesso contenitore chaincode su più canali.  L'unico prerequisito è che i file di origine effettivi del chaincode siano installati sul file system del peer.  In questo modo, se una parte di chaincode comune viene utilizzata in dozzine di canali, un peer avrà bisogno solo di un unico contenitore per eseguire operazioni di lettura/scrittura su tutti i libri mastro di canale.  Questo semplice approccio si rivela vantaggioso in termini di produttività e prestazioni di calcolo man mano che chaincode e scala di rete diventano più elaborate.  

## Installazione di un chaincode
Devi installare il chaincode su ogni singolo peer che eseguirà questo chaincode.  Per installare un chaincode, completa la seguente procedura:
1. Nella schermata "Chaincode" del tuo Network Monitor, seleziona un peer dall'elenco a discesa su cui installare il chaincode.  Fai clic sul pulsante **Install Chaincode**.
  ![Schermata Chaincode](../images/chaincode_install_overview.png "Schermata Chaincode")  
  
2. Nel pannello a comparsa **Installa chaincode**, immetti il nome e la versione del tuo chaincode. **Nota**: le stringhe di nome e versione verranno utilizzate nelle applicazioni per interagire con i chaincode installati.  Fai clic sul pulsante **Sfoglia** e accedi tramite il tuo file system locale alla posizione in cui sono memorizzati i file di origine chaincode.  Seleziona uno o più di file di origine chaincode da installare sul peer.  Questo esempio mostra un file di origine chaincode `fabcar.go` con un nome di `Chaincode1` e una versione di `v001` che è installato sul peer `fabric-peer-org2-17439a`.
  ![Installa chaincode](../images/chaincode_install.png "Installa chaincode")

## Istanzia un chaincode
Dopo essere stato installato sul file system di ogni singolo peer che aderisce a un canale, il chaincode deve essere quindi istanziato sul canale in modo che i peer possano interagire con il libro mastro tramite il contenitore del chaincode.  L'istanziazione esegue tutta l'inizializzazione necessaria del chaincode.  Spesso ciò implicherà l'impostazione di coppie chiave/valore che compongono lo stato globale iniziale di un chaincode.  

È necessario disporre di autorizzazione di operatore (**Operator**) o di scrittore (**Writer**) sul canale per istanziare il chaincode.  Il chaincode che ha lo stesso nome e la stessa versione su peer diversi deve essere istanziato solo una volta per distribuirne il contenitore.  Per istanziare un chaincode, completare la seguente procedura:
1. Nella schermata "Chaincode" del tuo Monitor di rete, seleziona il peer dove hai installato il chaincode e individua il chaincode che vuoi istanziare dalla tabella dei chaincode.  Fai quindi clic sul pulsante **Istanzia** sotto l'intestazione **Azione**.  
  ![Istanzia chaincode](../images/chaincode_instantiate.png "Istanzia chaincode")  
  
2. Nel pannello a comparsa **Istanzia chaincode**, imposta le coppie chiave/valore come argomenti per l'istanziazione del chaincode e seleziona il canale su cui eseguire l'istanziazione.  Fai clic su **Inoltra**.
  ![Pannello Istanzia chaincode](../images/chaincode_instantiate_panel.png "Pannello Istanzia chaincode")   

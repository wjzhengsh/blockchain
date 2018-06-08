---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Installazione, istanziazione e aggiornamento di un chaincode 

Il chaincode è un software, scritto in Go, Java o Node.js, che incapsula la logica di business e le istruzioni transazionali
per la creazione e la modifica di asset nel libro mastro. Un chaincode viene eseguito in un contenitore docker associato a un qualsiasi peer che deve interagire con esso.  Per ulteriori informazioni sullo sviluppo del chaincode, vedi le [esercitazioni per il chaincode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

Il chaincode viene installato sul file system di un peer e viene quindi istanziato su un canale. **Tutti i membri del canale devono installare il chaincode su ogni peer che eseguirà questo chaincode.** Per utilizzare lo stesso chaincode, i membri del canale devono dare al chaincode lo stesso nome e la stessa versione quando lo installano. Quindi, il passo di istanziazione implica l'inizializzazione di coppie chiave/valore e la distribuzione del contenitore chaincode. Tutti i peer, tramite i quali le applicazioni interagiranno con un chaincode, devono avere il chaincode che è installato sul file system del peer e devono disporre di un contenitore chaincode in esecuzione. **Tuttavia, se un peer utilizza lo stesso chaincode su più canali, ha bisogno solo di una singola istanza del contenitore chaincode**.

La **combinazione di installazione e istanziazione** è una funzione potente in quanto consente a un peer di interagire con lo stesso contenitore chaincode su più canali. L'unico prerequisito è che i file di origine effettivi del chaincode siano installati sul file system del peer. In questo modo, se una parte di chaincode comune viene utilizzata in dozzine di canali, un peer avrà bisogno solo di un unico contenitore per eseguire operazioni di lettura/scrittura su tutti i libri mastro di canale. Questo semplice approccio si rivela vantaggioso in termini di produttività e prestazioni di calcolo man mano che chaincode e scala di rete diventano più elaborate.

**Nota**: se sviluppi il tuo chaincode in modo iterativo e devi aggiornare un chaincode, devi ripetere sia il passo di installazione che quello di istanziazione per il chaincode. 


## Installazione di un chaincode
{: #installchaincode}

Devi installare il chaincode su ogni peer che eseguirà questo chaincode. Per installare un chaincode, completa la seguente procedura:
1. Nella schermata "Installa codice" del tuo monitoraggio della rete, seleziona un peer dall'elenco a discesa su cui installare il chaincode. Fai clic sul pulsante **Installa chaincode**.
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. Nel pannello a comparsa **Installa chaincode**, immetti il nome e la versione del tuo chaincode. **Nota**: le stringhe di nome e versione verranno utilizzate nelle applicazioni per interagire con il chaincode installato. Fai clic sul pulsante **Sfoglia** e accedi tramite il tuo file system locale alla posizione in cui sono memorizzati i file di origine chaincode. Seleziona uno o più di file di origine chaincode da installare sul peer. Quindi seleziona il tuo linguaggio chaincode dal menu a discesa **Tipo chaincode**.

  ![Installa Chaincode](../images/chaincode_install.png "Installa Chaincode")



## Istanzia un chaincode
Dopo essere stato installato sul file system di ogni peer che aderisce a un canale, il chaincode deve essere quindi istanziato sul canale in modo che i peer possano interagire con il libro mastro tramite il contenitore del chaincode. L'istanziazione esegue tutta l'inizializzazione necessaria del chaincode. Spesso ciò implicherà l'impostazione di coppie chiave/valore che compongono lo stato globale iniziale di un chaincode.

È necessario disporre di autorizzazione di operatore (**Operatore**) o di scrittore (**Scrittore**) sul canale per istanziare il chaincode. Il chaincode che ha lo stesso nome e la stessa versione su peer diversi deve essere istanziato solo una volta per distribuirne il contenitore. Per istanziare un chaincode, completare la seguente procedura:
1. Nella schermata "Installa il codice" del tuo Monitor di rete, seleziona il peer dove hai installato il chaincode e individua il chaincode che vuoi istanziare dalla tabella dei chaincode. Fai quindi clic sul pulsante **Istanzia** sotto l'intestazione **Azione**.
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. Nel pannello a comparsa **Crea un'istanza del chaincode**, imposta le coppie chiave/valore come argomenti per l'inizializzazione del chaincode e seleziona il canale su cui eseguire l'istanziazione.  Fai clic su **Invia**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

## Aggiornamento di un chaincode
Puoi aggiornare un chaincode per modificare la programmazione del chaincode mentre conservi la sua relazione agli asset del libro mastro. A causa della combinazione di installazione e istanziazione, devi aggiornare il chaincode su tutti i peer presenti sul canale con questo chaincode. Completa la seguente procedura per aggiornare il tuo chaincode. 

1. Installa un chaincode con lo stesso nome del tuo precedente chaincode, ma con una versione differente. Puoi seguire gli stessi passi di [Installazione di un chaincode](install_instatiate_chaincode.html#Installing a chaincode). Assicurati di selezionare lo stesso canale del tuo chaincode originale.

  ![Aggiorna chaincode](../images/upgrade_chaincode.png "Aggiorna chaincode")

2. Trova il tuo nuovo chaincode nella tabella e fai clic sul pulsante **Aggiorna** nell'intestazione **Azione**. Questa azione istanzia nuovamente il tuo chaincode e sostituisce il contenitore del chaincode con uno nuovo. Tieni presente che non devi immettere alcun nuovo argomento come parte della funzione di aggiornamento.

  ![Pulsante Aggiorna](../images/upgrade_button.png "Pulsante Aggiorna")

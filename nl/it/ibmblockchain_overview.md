---

copyright:
  years: 2016, 2017
lastupdated: "2017-07-28"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Concetti di base del blockchain
{: #ibmblockchain_overview}

Il blockchain è una DLT (distributed ledger technology) che ottimizza i processi di business stabilendo dei nuovi livelli di attendibilità, responsabilità e trasparenza per una nuova generazione di applicazioni transazionali. La rete blockchain è stata inizialmente introdotta sul mercato per gli scambi di bitcoin ma i suoi usi pratici vanno bel oltre le transazioni di criptovaluta. Insieme al progetto Hyperledger della Linux Foundation, {{site.data.keyword.blockchainfull}} sta reinventando gli scambi di business più fondamentali, aprendo le porte a un nuovo mondo di transazioni digitali.

{{site.data.keyword.blockchain}} riduce il costo e la complessità delle transazioni interaziendali mediante la creazione di reti efficienti ed altamente sicure in cui è possibile tracciare e commerciare praticamente qualsiasi cosa di valore senza affidarsi a un punto di controllo centralizzato. Nel mondo della finanza, le reti blockchain possono consentire di regolare le transazioni di titoli in minuti, piuttosto che giorni. Nel mondo del commercio, queste reti possono facilitare la gestione delle catene di approvvigionamento e consentono di tracciare e registrare il flusso di merci e pagamenti in tempo reale. 

## Panoramica della rete blockchain

In una rete {{site.data.keyword.blockchain}}, i record delle transazioni di rete sono conservati in un libro mastro condiviso che viene replicato a tutti i membri della rete o a un loro sottoinsieme (i libri mastro esistono nell'ambito di un canale e quindi, se il peer di un membro non è registrato a un canale, non disporrà delle transazioni di quel canale). I record di tutte le transazioni, sia quelle valide che quelle non valide, sono registrati in blocchi e accodati alla catena hash (ossia il blockchain) per ciascun canale.  Le transazioni valide, a differenza di quelle non valide, aggiorneranno il database dello stato. I chaincode (noti anche come "smart contract") sono parti di software che contengono una serie di funzioni che consente l'esecuzione di operazioni di lettura e scrittura sul libro mastro.  Le applicazioni lato client si avvalgono di un SDK per interfacciarsi con uno o più peer e, alla fine, per richiamare le funzioni su uno specifico chaincode.  Ci sono due API Fabric chiave che consentono a un chaincode di eseguire letture o scritture - `getState` & `putState`.

La **Figura 1** mostra una rete blockchain con autorizzazioni di esempio, che presenta un'architettura peer-to-peer distribuita e centralizzata e una CA (Certificate Authority) che gestisce i ruoli utente e le autorizzazioni: ![Rete blockchain](images/Architecture_network_and_application.png "Rete blockchain con autorizzazioni di esempio")
*Figura 1. Una rete blockchain con autorizzazioni: il flusso di dati e l'accesso alla rete sono controllati dai ruoli membro*

Le seguenti descrizioni corrispondono all'architettura e al flusso mostrati nella Figura 1 (nota: non rappresentano un processo sequenziale):

**A:** un utente blockchain inoltra una transazione alla rete blockchain. La transazione può essere una distribuzione, un richiamo o una query, e viene emessa tramite un'applicazione lato client che si avvale di un SDK oppure direttamente tramite una API REST.  

**B:** le reti di business attendibili forniscono un accesso ad autorità di regolamentazione e revisori (la SEC nel mercato azionario statunitense, ad esempio).  

**C:** un operatore di rete blockchain gestisce le autorizzazioni dei membri quali la registrazione dell'autorità di regolamentazione (B) come un "revisore" e l'utente blockchain (A) come un "client". Un revisore può essere limitato solo all'esecuzione di query al libro mastro mentre un client può essere autorizzato a distribuire, richiamare ed eseguire query di specifici tipi di chaincode. 

**D:** uno sviluppatore blockchain scrive il chaincode e le applicazioni lato client. Lo sviluppatore blockchain può distribuire il chaincode direttamente alla rete tramite un'interfaccia REST. Per includere le credenziali da un'origine dati tradizionale nel chaincode, lo sviluppatore può utilizzare una connessione fuori banda per accedere ai dati (G). 

**E:** un utente blockchain si connette alla rete tramite un nodo peer (A). Prima di procedere con eventuali transazioni, il nodo richiama la registrazione dell'utente e i certificati di transazione dall'autorità di certificazione (CA, Certificate Authority). Gli utenti devono possedere questi certificati digitali per interagire con il libro mastro su una rete con autorizzazioni.

**F:** a un utente che prova a gestire il chaincode potrebbe essere richiesto di verificare le sue credenziali su un'origine dati tradizionale (G). Per confermare l'autorizzazione dell'utente, il chaincode può utilizzare una connessione fuori banda a questi dati tramite una piattaforma di elaborazione tradizionale.

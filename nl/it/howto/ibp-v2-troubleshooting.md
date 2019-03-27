---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Risoluzione dei problemi
{: #ibp-v2-troubleshooting}

Si possono verificare dei problemi generali quando utilizzi la console per gestire i nodi, i canali o gli smart contract. In molti casi, puoi risolvere questi problemi seguendo pochi semplici passi.

## Perché le mie operazioni del nodo hanno esisto negativo dopo che creo il mio peer o ordinante?
{: #ibp-console-build-network-troubleshoot-entry1}

È possibile che tu stia riscontrando un errore durante la gestione di un nodo esistente. Quando questo si verifica, è spesso utile consultare i log del nodo.  

Ad esempio, quando tenti di gestire il nodo, l'azione potrebbe avere esito negativo.
{: tsSymptoms}

Dopo aver creato un nuovo peer o ordinante, a seconda della tua configurazione di archiviazione del cluster, potrebbero essere necessari alcuni minuti perché il nodo diventi operativo.
{: tsCauses}

Controlla il tuo dashboard Kubernetes e assicurati che lo stato del peer o del nodo sia `Running`. Poi riprova la tua azione. Se stai ancora riscontrando dei problemi dopo che il nodo diventa attivo, [controlla i tuoi log del nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) per degli errori.  
{: tsResolve}

## Perché il mio peer non si avvia?
{: #ibp-console-build-network-troubleshoot-entry2}

È possibile che tu stia riscontrando questo errore per molti motivi. 

Il log del peer include `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`
{: tsSymptoms}

- Questo errore può verificarsi nelle seguenti condizioni:
  - Quando hai creato la definizione MSP dell'organizzazione peer o ordinante, hai specificato un segreto e un ID di registrazione che corrispondono a un'identità del tipo `peer` e non `client`. Deve essere del tipo `client`.
  - Quando hai creato la definizione MSP dell'organizzazione peer o ordinante, hai specificato un segreto e un ID di registrazione che non corrispondono a quelli dell'identità amministratore dell'organizzazione corrispondente.
  - Quando hai creato il peer o l'ordinante, hai specificato il segreto e l'ID di registrazione di un'identità che non è del tipo 'peer'.

- Apri il tuo nodo CA peer o ordinante e visualizza le identità registrate elencate nella tabella **Utenti registrati**.
- Elimina il peer o l'ordinante e ricrealo, stando attendo a specificare il segreto e l'ID di registrazione corretti.
- Tieni presente che prima di creare il peer o l'ordinante, devi creare un ID di amministratore dell'organizzazione, di tipo 'client'. Assicurati di specificare lo stesso ID di quello di registrazione quando crei la definizione MSP dell'organizzazione. Consulta queste istruzioni per [registrare le identità del peer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) e queste istruzioni per [registrare le identità dell'ordinante](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Perché l'installazione, l'iniziazione o l'upgrade del mio smart contract hanno esito negativo?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

È possibile che tu stia riscontrando un errore durante l'installazione, l'iniziazione o l'upgrade di uno smart contract. Ad esempio, quanto tenti di installare uno smart contract su un peer, hai il seguente errore `An error occurred when installing smart contract on peer.`
{: tsSymptoms}

Puoi ricevere questo errore se questa versione dello smart contract esiste già sul peer o se il tuo peer ha esaurito le risorse.
{: tsCauses}

- Apri il tuo dashboard Kubernetes e assicurati che lo stato del peer sia `Running`.  
- Apri il nodo del peer e assicurati che lo smart contract non esista già sul peer e riprova con la versione corretta.
- Se stai ancora riscontrando dei problemi dopo che il nodo diventa attivo, [controlla i tuoi log del nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) per degli errori.  
{: tsResolve}

## Come posso visualizzare i miei log del contenitore dello smart contract?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

Potresti dover visualizzare i tuo log del contenitore dello smart contract o del chaincode per eseguire il debug di un problema con lo smart contract.
{: tsSymptoms}

Segui queste istruzioni per [visualizzare i tuoi log del contenitore](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs).
{: tsResolve}

## Il mio canale, gli smart contract e le identità sono scomparsi dalla console. Come posso recuperarli?
{: #ibp-v2-troubleshooting-browser-storage}

Le tue identità del portafoglio della console sono costituite da una coppia di chiavi pubblica e privata che ti permette di gestire i tuoi componenti blockchain ma essi vengono archiviati solo nella tua memoria locale del browser. Sei responsabile per la protezione e la gestione di queste identità. Ti consigliamo di esportarle nel tuo file system dopo averle create. Se crei un nuovo nodo, associa un'identità dal tuo portafoglio della console al nodo. Questa identità amministratore è quella che ti consente di gestire il nodo. Quando passi da un browser a un altro o a un browser su una macchina differente, queste identità non sono più presenti nel tuo portafoglio. Pertanto, non puoi gestire i componenti.
{: tsSymptoms}

Una delle nuove funzioni di {{site.data.keyword.blockchainfull_notm}} Platform 2.0 è che ora sei responsabile della protezione e della gestione dei tuoi certificati. Pertanto, vengono conservati solo nella memoria locale del browser per consentirti di gestire il componente.
{: tsCauses}

- Ogni volta che crei una nuova definizione MSP dell'organizzazione, generi delle chiavi per un'identità a cui è consentito gestire l'organizzazione. Pertanto, durante tale processo devi fare clic sui pulsanti **Genera** e **Esporta** per archiviare l'identità generata nel tuo portafoglio della console e salvarla nel tuo file system come un file JSON.
- Per risolvere questo problema nel tuo browser, devi importare tali identità e associarle al nodo corrispondente:
  - Nel browser in cui stai riscontrando il problema, fai clic sulla scheda **Portafoglio** e poi su **Aggiungi identità** per importare il file JSON nel tuo portafoglio.
  - Fai clic su **Carica JSON** e seleziona il file JSON che hai esportato utilizzando il pulsante **Aggiungi file**.
  - Fai clic su **Invia**.
  - Ora apri il nodo peer o ordinante a cui era stata in origine associata questa identità e fai clic sull'icona **Impostazioni**.
  - Fai clic sul pulsante **Associa identità**.
  - Seleziona l'identità che hai appena importato nel tuo portafoglio della console dall'elenco a discesa.
  - Fai clic su **Associa**. 
- Ripeti questo processo per ogni identità presente nel portafoglio del browser originale.
{: tsResolve}

## Perché sto ricevendo l'errore `An error occurred when updating channel` mentre provo ad aggiungere un'organizzazione al mio canale?
{: #ibp-v2-troubleshooting-update-channel}

Quando tenti di aggiungere un'altra organizzazione a un canale, l'aggiornamento non riesce con il messaggio `An error occurred when updating channel`.
{: tsSymptoms}

Questo errore si verifica quando l'**ID MSP del programma di aggiornamento del canale** selezionato sul pannello **Aggiorna canale** non è un amministratore del canale.
{: tsCauses}

Sul pannello **Aggiorna canale**, scorri in basso fino all'**ID MSP del programma di aggiornamento del canale** e seleziona l'ID MSP che era stato specificato durante la creazione del canale o specifica l'ID MSP che è l'amministratore del canale.
{: tsResolve}

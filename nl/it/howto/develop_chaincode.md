---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Scrittura degli smart contract
{: #develop-smart-contracts}

Il chaincode, a cui a volte viene fatto riferimento come agli smart contract, è il software che ti consente di leggere e aggiornare i dati sul libro mastro blockchain. Il chaincode può trasformare la logica di business in un programma eseguibile accettato e verificato da tutti i membri della rete blockchain. La logica di business include la definizione degli asset scambiati tra le parti. È formata anche dai termini e dalle condizioni necessari per l'esecuzione di una transazione. La trasformazione di queste regole in codice su una blockchain consente alle attività aziendali di semplificare il processo di business e di controllare e ridurre delle grandi quantità di elaborazione e documentazione manuale.

Ad esempio, immagina che una rete di concessionarie automobilistiche, società di assicurazioni e organi governativi abbiano deciso di utilizzare blockchain per tenere traccia della proprietà degli autoveicoli. Il chaincode potrebbe richiedere che tutti i veicoli abbiano una registrazione valida e un numero identificativo del veicolo per poter essere aggiunti alla rete. Quando un veicolo viene venduto, il chaincode può richiedere che vengano depositati in garanzia dei fondi finché il veicolo non viene registrato dal suo nuovo proprietario da un'autorità regolatrice. Quando viene completata la nuova registrazione, il nuovo proprietario sarà registrato e i fondi saranno trasferiti automaticamente.

La seguente esercitazione ti guiderà tra gli aspetti di base della creazione del chaincode, incluso:

- [Come iniziare a scrivere il chaincode](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-write)
- [La relazione tra il chaincode e i dati](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-data)
- [Transazioni tra chaincode](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)

L'esercitazione introduce anche degli aspetti importanti di Fabric accessibili tramite il chaincode:

- [Utilizzo degli indici con couchDB](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-indexes)

## Scrittura del chaincode
{: #develop-smart-contracts-write}

Il chaincode può essere scritto in più linguaggi e {{site.data.keyword.blockchainfull_notm}} Platform supporta Go e Node.js. Il chaincode consente agli utenti di eseguire query e modificare i dati archiviati nella blockchain utilizzando le API fornite dall'interfaccia di chaincode Fabric. I dati sulla blockchain sono archiviati in coppie chiave-valore nello stato globale del [libro mastro ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "ledger") del canale. Il chaincode utilizza i comandi get per richiamare i valori e i comandi put per creare o aggiornare i valori. Utilizzando queste operazioni di base, puoi creare delle funzioni che definiscono le regole di business della tua rete. Queste funzioni possono essere richiamate dalle tue applicazioni e sono visibili per gli utenti finali della rete. Per continuare ad utilizzare l'esempio della rete di veicoli, puoi creare una funzione che consente a un concessionario automobilistico di utilizzare un comando put per aggiungere una nuova automobile al libro mastro solo se viene fornito un numero ID del veicolo valido.

Puoi imparare come iniziare a scrivere il chaincode visitando l'[esercitazione Chaincode for developers ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "chaincode for developers tutorial") nella documentazione della community Hyperledger Fabric. L'esercitazione ti guiderà nella costruzione di un semplice chaincode che crea e legge gli asset e ti introduce quali API vengono utilizzate nel processo. Puoi anche trovare la guida di riferimento all'API chaincode per tutti i linguaggi chaincode. Esistono ulteriori esempi nella cartella chaincode del [Repository di esempi Fabric ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-samples "Fabric samples").  

Uno smart contract può normalmente convalidare le richieste, applicare le regole aziendali e restituisce un risultato deterministico. Tuttavia, esistono delle situazioni in cui sono richieste delle informazioni supplementari o la rete di business vuole assicurarsi che le informazioni fornite dai client siano vere. Hyperledger Fabric non impedisce chiamate esterne a sistemi di terze parti dagli smart contract. Tuttavia, è responsabilità dello sviluppatore dello smart contract di garantire che le serie di R/W risultanti siano deterministiche.
{:note}

## Installazione del chaincode
{: #develop-smart-contracts-install}

Poiché il chaincode fornisce la struttura delle transazioni su un canale, deve essere installato un chaincode su tutti i peer che hanno aderito al canale e che vogliono utilizzare il chaincode per aggiornare o interrogare mediante query il libro mastro del canale. Successivamente un membro del canale può istanziare il chaincode su un canale e configurare la politica di approvazione del chaincode. È possibile eseguire l'installazione e l'avvio del chaincode utilizzando l'IU del Monitoraggio della rete, l'interfaccia di riga comando del peer Fabric o da un'applicazione client utilizzando l'[SDK Fabric](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk). Per ulteriori informazioni su come utilizzare l'IU di Monitoraggio della rete per distribuire il chaincode, vedi [Installazione, istanziazione e aggiornamento di un chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).

## Chaincode e dati
{: #develop-smart-contracts-data}

Ogni canale ha solo un libro mastro e i dati su di esso sono suddivisi da una chiave univoca e dal chaincode che ha aggiunto la coppia chiave-valore al libro mastro. I membri possono solo leggere e aggiornare i dati sul libro mastro del canale utilizzando la chiave corretta e il chaincode associato. Ai dati a cui può accedere un chaincode viene fatto riferimento come allo spazio dei nomi del chaincode e tutti i dati sul libro mastro si trovano all'interno dello spazio dei nomi di un chaincode. Un chaincode può interagire con i dati all'esterno del proprio spazio dei nomi solo utilizzando una [transazione tra chaincode](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode) al chaincode che può accedere ai dati pertinenti.

Possiamo utilizzare il chaincode fabcar dal repository degli esempi di Fabric come esempio di come il chaincode interagisce con i dati. Lo spazio dei nomi viene creato quando viene istanziato lo smart contract. Alcuni chaincode accettano una serie di argomenti di coppie chiave-valore quando il chaincode viene istanziato e utilizza tali dati per inizializzare lo spazio dei nomi. Il chaincode fabcar dal repository degli esempi di Fabric non accetta alcun argomento quando viene istanziato. Per fabcar, devi aggiungere i dati allo spazio dei nomi utilizzando la funzione initLedger o createCar. Ad esempio, passando l'argomento `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` si renderà la coppia chiave-valore `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` lo spazio dei nomi fabcar.

Solo il chaincode fabcar può modificare questi dati. Supponi che l'utente voglia trasferire l'automobile a un nuovo proprietario utilizzando la seguente funzione changeCarOwner:
```
func (s *SmartContract) changeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {

	if len(args) != 2 {
		return shim.Error("Incorrect number of arguments. Expecting 2")
	}

	carAsBytes, _ := APIstub.GetState(args[0])
	car := Car{}

	json.Unmarshal(carAsBytes, &car)
	car.Owner = args[1]

	carAsBytes, _ = json.Marshal(car)
	APIstub.PutState(args[0], carAsBytes)

	return shim.Success(nil)
}
```
{:codeblock}

Puoi richiamare il chaincode utilizzando i seguenti argomenti:
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

Il chaincode utilizza quindi i comandi get per generare un read set dallo spazio dei nomi del chaincode. Utilizza i comandi put per creare il write set della transazione. Il richiamo del chaincode crea inoltre un ID della transazione univoco.
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Ogni peer di approvazione con il chaincode installato simulerà le transazioni utilizzando il read set e il write set. Se le simulazioni di ogni peer sono congruenti, le transazioni vengono considerate valide e ne può venire eseguito il commit al libro mastro. Dopo la transazione, lo spazio dei nomi fabcar diventerà `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`. Tieni presente che il proprietario è stato modificato da Tom a Mark.

La relazione 1-1 tra il chaincode e i dati crea delle considerazioni importanti per lo sviluppo delle reti e delle applicazioni. Il software chaincode deve essere aggiornato installando un chaincode con lo stesso nome e una versione diversa, invece di utilizzare un nuovo chaincode. Questa procedura di aggiornamento preserva lo spazio dei nomi del chaincode e ti consentirà di continuare ad utilizzare i dati esistenti. Puoi anche utilizzare un chaincode diverso per limitare l'accesso ai dati sensibili o ai dati di lettura e scrittura in parallelo.

## Transazioni tra chaincode
{: #develop-smart-contracts-cross-chaincode}

Puoi utilizzare un chaincode per richiamare altri chaincode. Questo consente a un chaincode di eseguire query e di scrivere i dati all'esterno del proprio spazio dei nomi. Il chaincode può sia leggere che aggiornare i dati utilizzando il chaincode istanziato sullo stesso canale. Tuttavia è possibile eseguire delle query dei dati soltanto utilizzando il chaincode su canali differenti. L'organizzazione che richiama il chaincode originale deve essere autorizzata a richiamare il chaincode che è la destinazione della transazione.

Ad esempio, puoi utilizzare la seguente funzione `crossChaincodeChangeCarOwner` per richiamare fabcar da un altro chaincode. Poiché la funzione aggiorna i dati modificando il proprietario dell'automobile, deve richiamare un chaincode fabcar istanziato sullo stesso canale.
```
func (s *SmartContract) crossChaincodeChangeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {
	newOwnerArgs := toChaincodeArgs("changeCarOwner", args[0], args[1])

	chaincodeName := "fabcar"
	channelName := "defaultchannel"

	APIstub.InvokeChaincode(chaincodeName, newOwnerArgs, channelName)

	return shim.Success(nil)
}
```
{:codeblock}

Se crei un nuovo contratto utilizzando questo codice e richiami la funzione `crossChaincodeChangeCarOwner`, la transazione genererà un nuovo ID della transazione e un write set.
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Questo TXid e il write set saranno passati al chaincode fabcar.
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

Poiché il `fabcar` è sullo stesso canale di `newContract`, alla funzione `crossChaincodeChangeCarOwner` è consentito di scrivere nello spazio dei nomi `fabcar`. Il nuovo spazio dei nomi `fabcar` sarà `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`.


## Utilizzo degli indici con CouchDB
{: #develop-smart-contracts-indexes}

Se utilizzi CouchDB come tuo database dello stato, puoi eseguire le query di dati JSON dal tuo chaincode sui dati di stato del canale. Ti consigliamo vivamente di creare indici per le tue query JSON e di utilizzarli nel tuo chaincode. Gli indici consentono alle tue applicazioni di richiamare in modo efficiente i dati mentre la tua rete aggiunge blocchi aggiuntivi di transazioni e voci nello stato globale.

Per ulteriori informazioni su CouchDB e su come configurare gli indici, vedi [CouchDB as the State Database ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} nella documentazione Hyperledger Fabric. Puoi anche trovare un esempio che utilizza un indice con il chaincode nell'[esercitazione di Fabric CouchDB ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html). Visita [Passi ottimali quando si utilizza CouchDB](/docs/services/blockchain/best_practices.html#best-practices-app-couchdb-indices) nell'esercitazione Sviluppo di applicazioni per ulteriori informazioni su come eseguire query dei dati dalle tue applicazioni.

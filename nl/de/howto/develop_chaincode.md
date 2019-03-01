---

copyright:
  years: 2017, 2019
lastupdated: "2018-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Smart Contracts schreiben
{: #develop-smart-contracts}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Chaincode, auch unter der Bezeichnung "Smart Contracts" bekannt, ist eine Software, die Ihnen das Lesen und Aktualisieren von Daten im Blockchain-Ledger ermöglicht. Chaincode kann Geschäftslogik in ein ausführbares Programm umwandeln, das alle Mitglieder im Blockchain-Netz vereinbart und verifiziert haben. Die Geschäftslogik enthält die Definition von Assets, die zwischen Parteien gehandelt werden. Sie besteht außerdem aus den Bedingungen, die für die Ausführung einer Transaktion erforderlich sind. Wenn diese Regeln in eine Blockchain umgewandelt werden, können Unternehmen Geschäftsprozesse und -überwachung optimieren sowie manuelle und papierbasierte Prozesse erheblich reduzieren.

Als Beispiel dient zur Veranschaulichung ein Netz von Kraftfahrzeughändlern, Versicherungsgesellschaften und staatlichen Regulierungsbehörden, die sich dafür entschieden haben, das Eigentum an Fahrzeugen mithilfe einer Blockchain zu überwachen. Der Chaincode kann erforderlich machen, dass alle Fahrzeuge eine gültige Registrierung und Fahrzeug-Identifizierungsnummer besitzen müssen, damit sie zum Netz hinzugefügt werden können. Wenn ein Fahrzeug verkauft wird, kann es der Chaincode erforderlich machen, dass die Geldsumme treuhänderisch hinterlegt wird, bis das Fahrzeug durch eine Regulierungsbehörde auf seinen neuen Eigentümer eingetragen wurde. Sobald die neue Registrierung vorgenommen wurde, wird der neue Eigentümer eingetragen und die Geldsumme automatisch überwiesen.

Im folgenden Lernprogramm werden Sie durch die grundlegenden Punkte bei der Erstellung von Chaincode geführt. Dies umfasst die folgenden Aspekte:

- [Einstieg in das Schreiben von Chaincode](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-write)
- [Beziehung zwischen Chaincode und Daten](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-data)
- [Chaincodeübergreifende Transaktionen](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)

Das Lernprogramm stellt außerdem wichtige Strukturaspekte vor, die durch Chaincode zugänglich sind:

- [Indizes mit couchDB verwenden](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-indexes)

## Chaincode schreiben
{: #develop-smart-contracts-write}

Chaincode kann in mehreren Sprachen geschrieben werden; {{site.data.keyword.blockchainfull_notm}} Platform unterstützt Chaincode, der in Go und Node.js geschrieben ist. Mittels Chaincode können Benutzer Daten abfragen und ändern, die in der Blockchain gespeichert sind. Dies erfolgt unter Verwendung von APIs, die von der Fabric-Chaincodeschnittstelle bereitgestellt werden. Die Daten der Blockchain werden in Schlüssel/Wert-Paaren im World-Status des Kanal-[Ledgers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "Ledger") gespeichert. Chaincode verwendet GET-Befehle zum Abrufen von Werten und PUT-Befehle zum Erstellen oder Aktualisieren von Werten. Mit diesen Basisoperationen können Sie Funktionen erstellen, die die Geschäftsregeln Ihres Netzes definieren. Diese Funktionen können durch Ihre Anwendungen aufgerufen und für die  Benutzer des Netzes zugänglich gemacht werden. Im Fall des Fahrzeugnetzbeispiels bedeutet dies, dass Sie eine Funktion erstellen können, die die Verwendung eines PUT-Befehls durch einen Fahrzeughändler zum Hinzufügen eines neuen Fahrzeugs zum Ledger nur dann zulässt, wenn hierbei eine gültige Fahrzeugidentnummer angegeben wird.

Der Einstieg in das Schreiben von Chaincode ist im [Lernprogramm über Chaincode für Entwickler ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "Chaincode for developers tutorial") in der Dokumentation der Hyperledger Fabric-Community beschrieben. Das Lernprogramm führt Sie durch die Erstellung eines einfachen Chaincodes, der Assets erstellt und liest, und stellt die APIs vor, die im Prozess verwendet werden. Außerdem können Sie das API-Referenzhandbuch für alle Chaincodesprachen hinzuziehen. Zusätzliche Beispiele sind im Chaincodeordner des [Fabric-Beispielrepositorys ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-samples "Fabric-Beispiele") verfügbar.

## Chaincode installieren
{: #develop-smart-contracts-install}

Da Chaincode die Struktur der Transaktionen in einem Kanal bereitstellt, muss ein Chaincode auf allen Peers installiert werden, die am Kanal teilnehmen und den Chaincode zum Aktualisieren oder Abfragen des Kanal-Ledgers verwenden wollen. Anschließend kann ein Mitglied des Kanals den Chaincode in einem Kanal instanziieren und die Bewilligungsrichtlinie des Chaincodes festlegen. Die Installation und Instanziierung von Chaincode kann in der Benutzerschnittstelle von Network Monitor, in der Fabric-Befehlszeilenschnittstelle "peers" oder in einer Clientanwendung unter Verwendung des [Fabric-SDK](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk) ausgeführt werden. Informationen zum Bereitstellen von Chaincode mit der Benutzerschnittstelle von Network Monitor finden Sie unter [Chaincode installieren, instanziieren und aktualisieren](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).

## Chaincode und Daten
{: #develop-smart-contracts-data}

Jeder Kanal verfügt nur über ein einziges Ledger. Die Daten in diesem Ledger werden durch einen eindeutigen Schlüssel sowie den Chaincode partitioniert, der das Schlüssel/Wert-Paar zum Ledger hinzugefügt hat. Mitglieder können Daten im Kanal-Ledger nur mithilfe des korrekten Schlüssels und des zugehörigen Chaincodes lesen oder aktualisieren. Daten, auf die durch einen Chaincode zugegriffen werden kann, werden als "Namensbereich" des Chaincodes bezeichnet; alle Daten im Ledger gehören zum Namensbereich eines einzigen Chaincodes. Die Interaktion eines Chaincodes mit Daten außerhalb seines Namensbereichs ist nur mithilfe einer [chaincodeübergreifenden Transaktion](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode) für den Chaincode möglich, der auf die relevanten Daten zugreifen kann.

Der fabcar-Chaincode aus dem Fabric-Beispielrepository ist ein Beispiel dafür, wie Chaincode mit Daten interagiert. Der Namensbereich wird erstellt, wenn der Smart Contract instanziiert wird. Manche Chaincodes akzeptieren eine Gruppe von Argumenten in Form von Schlüssel/Wert-Paaren, wenn der Chaincode instanziiert wird, und verwenden diese Daten zum Initialisieren des Namensbereichs. Der fabcar-Chaincode aus dem Fabric-Beispielrepository akzeptiert keine Argumente, wenn er instanziiert wird. Bei fabcar müssen Sie Daten mit der Funktion "initLedger" oder "createCar" zum Namensbereich hinzufügen. Beispielsweise wird durch Übergabe des Arguments `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` das Schlüssel/Wert-Paar `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` für den fabcar-Namensbereich erstellt.

Diese Daten können nur vom fabcar-Chaincode geändert werden. Angenommen, der Benutzer möchte das Fahrzeug mit der folgenden Funktion "changeCarOwner" auf einen neuen Eigentümer übertragen:
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

Der Chaincode kann dann unter Verwendung der folgenden Argumente aufgerufen werden:
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

Anschließend verwendet der Chaincode die GET-Befehle, um einen Lesesatz aus dem Chaincodenamensbereich zu generieren. Mit den PUT-Befehlen erstellt er den Schreibsatz für die Transaktion. Durch den Aufruf des Chaincodes wird außerdem eine eindeutige Transaktions-ID erstellt.
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Jeder einwilligende Peer, auf dem der Chaincode installiert ist, simuliert die Transaktion mithilfe des Lesesatzes und des Schreibsatzes. Falls die Simulationen durch alle Peers konsistent sind, wird die Transaktion als gültig betrachtet und kann im Ledger festgeschrieben werden. Nach der Transaktion wird der fabcar-Namensbereich zu `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`. Sie können feststellen, dass sich der Eigentümer von Tom in Mark geändert hat.

Die Eins-zu-eins-Beziehung zwischen Chaincode und Daten bietet wichtige Aspekte für die Entwicklung von Netzen und Anwendungen. Chaincode-Software sollte durch die Installation eines Chaincodes mit demselben Namen und einer anderen Version und nicht durch die Verwendung eines neuen Chaincodes aktualisiert werden. Durch diese Aktualisierungsprozedur bleibt der Chaincodenamensbereich erhalten und die vorhandenen Daten können weiterhin genutzt werden. Durch die Verwendung von anderem Chaincode können Sie auch den Zugriff auf sensible Daten oder parallele Lese- und Schreibvorgänge für Daten einschränken.

## Chaincodeübergreifende Transaktionen
{: #develop-smart-contracts-cross-chaincode}

Sie können einen Chaincode zum Aufrufen anderer Chaincodes verwenden. Auf diese Weise kann ein Chaincode Daten außerhalb seines eigenen Namensbereichs abfragen und schreiben. Chaincode kann mithilfe von Chaincode, der in demselben Kanal instanziiert wird, Daten sowohl lesen als auch aktualisieren. Das Abfragen von Daten ist jedoch nur unter Verwendung von Chaincode in anderen Kanälen möglich. Die Organisation, die den ursprünglichen Chaincode aufruft, muss zum Aufrufen des Chaincodes berechtigt sein, der das Ziel der Transaktion ist.

Beispielsweise können Sie mit der folgenden Funktion `crossChaincodeChangeCarOwner` den fabcar-Chaincode aus einem anderen Chaincode heraus aufrufen. Da die Funktion Daten aktualisiert, weil der Fahrzeugeigentümer geändert wird, muss sie einen fabcar-Chaincode aufrufen, der in demselben Kanal instanziiert ist.
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

Falls Sie unter Verwendung dieses Codes einen neuen Smart Contract erstellen und die Funktion `crossChaincodeChangeCarOwner` aufrufen, generiert die Transaktion eine neue Transaktions-ID und einen Schreibsatz.
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Diese Transaktions-ID und dieser Schreibsatz werden an den fabcar-Chaincode übergeben.
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

Da sich `fabcar` in demselben Kanal wie `newContract` befindet, darf die Funktion `crossChaincodeChangeCarOwner` Daten in den Namensbereich von `fabcar` schreiben. Der neue Namensbereich von `fabcar` ist dann `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`.


## Indizes mit CouchDB verwenden
{: #develop-smart-contracts-indexes}

Wenn Sie CouchDB als Statusdatenbank verwenden, können Sie JSON-Datenabfragen aus dem Chaincode für die Statusdaten des Kanals ausführen. Es wird dringend empfohlen, Indizes für die JSON-Abfragen zu erstellen und sie im Chaincode zu verwenden. Mithilfe von Indizes können Ihre Anwendungen Daten effizient abrufen, während das Netz zusätzliche Blöcke von Transaktionen und Einträgen im World-Status hinzufügt.

Weitere Informationen zu CouchDB und zum Konfigurieren von Indizes enthält der Abschnitt über [CouchDB als Statusdatenbank ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} in der Hyperledger Fabric-Dokumentation. Im [Fabric-Lernprogramm für CouchDB ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html) finden Sie außerdem ein Beispiel, das einen Index mit Chaincode verwendet. Der Abschnitt [Best Practices bei der Verwendung von CouchDB](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices) im Lernprogramm "Anwendungen entwickeln" bietet weitere Informationen dazu, wie Sie Daten aus Ihren Anwendungen abfragen.

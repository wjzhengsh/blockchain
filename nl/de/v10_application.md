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

# Anwendungen entwickeln
{: #dev_app}
Mit Anwendungen können Sie den Chaincode aufrufen, um ein kanalspezifisches Hauptbuch (Ledger) in Ihrem {{site.data.keyword.blockchain}}-Netz abzufragen oder zu aktualisieren.
{:shortdesc}

## Anwendungsentwicklungsumgebung einrichten
Sie müssen die vorausgesetzte Software installieren, um Anwendungen zu entwickeln, die mit dem {{site.data.keyword.blockchain}}-Netz unter {{site.data.keyword.Bluemix}} interagieren können.
{:shortdesc}

*	Git ([Git-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://git-scm.com/downloads){:new_window})

	Git ist ein Tool zur Versionssteuerung für die Entwicklung von Chaincode und die Softwareentwicklung im Allgemeinen. Git Bash, das mit Git unter Windows installiert wird, ist eine leistungsfähige Alternative zur Windows-Eingabeaufforderung.

	Verwenden Sie den folgenden Befehl, um Ihre Git-Installation zu überprüfen.  Es sollte etwa folgende Ausgabe angezeigt werden:
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([GoLang-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://golang.org/dl){:new_window})

	Bei der GoLang-Installation wird eine Reihe von Go-CLI-Tools installiert, die Sie beim Schreiben von Chaincode unterstützen. Sie können beispielsweise mit dem Befehl `go build` prüfen, ob Ihr Chaincode tatsächlich kompiliert wurde, bevor Sie versuchen, ihn in einem Netz bereitzustellen. Zum Zeitpunkt der Abfassung war bekannt, dass dieser Chaincode mit Version `1.8.3` erfolgreich erstellt wird.

	Verwenden Sie den folgenden Befehl, um Ihre GoLang-Version zu überprüfen. Es sollte etwa folgende Ausgabe angezeigt werden:
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}

	Entnehmen Sie den [Installationsanweisungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://golang.org/doc/install){:new_window}, wie die Umgebungsvariablen eingestellt werden müssen. Prüfen Sie Ihren `GOPATH` mit dem folgenden Befehl. Beachten Sie, dass Ihr Wert für `GOPATH` nicht mit dem Beispiel übereinstimmen muss. Es ist nur wichtig, dass diese Variable auf ein gültiges Verzeichnis in Ihrem Dateisystem gesetzt ist.
	```
	$ echo $GOPATH
C:\gopath
	```
	{:screen}

	Anschließend können Sie Ihre GoLang-Installation überprüfen, indem Sie GoLang-Code mit dem Beispiel [ Hello World  ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://golang.org/doc/install#testing){:new_window} erstellen.

*	Node.js ([Node.js-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://nodejs.org/en/download/){:new_window}). Wählen Sie eine Version zwischen 6.9.5 und 7 aus. Node-Versionen ab Version 7 führen zu Fehlern beim Herunterladen der SDK-Module.

	Verwenden Sie die folgenden Befehle, um Ihre Node.js-Installation zu überprüfen. Es sollte etwa folgende Ausgabe angezeigt werden:
	```
	$ node -v
	v6.10.1

	$ npm -v
	3.10.10
	```
	{:screen}

## Clientseitige Zertifikate generieren
Auf den x509-Standard und die Public Key Infrastructure soll hier nicht näher eingegangen werden. Hierzu gibt es eine Vielzahl externer Ressourcen. Es sei nur erwähnt, dass der Kommunikationsfluss in Fabric die Sign/Verify-Operationen für jede Nahtstelle (Touchpoint) verwendet. Aus diesem Grund muss jeder Client, der Aufrufe (d. h. Transaktionen) an das Netz sendet, die Nutzdaten (privater Schlüssel) signieren und ein ordnungsgemäß signiertes x509-Zertifikat zu Überprüfungszwecken (signiertes Zertifikat) anhängen. Der private Schlüssel und das signierte Zertifikat ergeben zusammen mit einer MSP-ID und dem Stammzertifikat der Zertifizierungsstelle das "Benutzerkontext"-Objekt. Auch hier besteht keine Notwendigkeit für zusätzliche Details. Es wird einfach mit der entsprechenden Zertifizierungsstelle kommuniziert und es werden die Schlüssel und Zertifikate abgerufen, mit denen das Objekt erstellt werden kann -- dieser Prozess wird als Eintragung bezeichnet. Wenn Sie das Benutzerkontextobjekt erstellt haben, muss nur noch eine API von Ihrer Anwendung aufgerufen werden, um den Benutzerkontext festzulegen ("set") oder abzurufen ("get"). Nun ist die Anwendung (d. h. der Client) mit allen erforderlichen Artefakten ausgestattet und bereit, mit dem Netz zu kommunizieren. Es werden zwei Möglichkeiten für den Abruf der Schlüssel und Zertifikate gezeigt.

### Befehlszeile
Dies ist die einfachere der beiden Methoden. Befolgen Sie zunächst die Anweisungen für den Build des [Fabric CA-Clients ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Dieser Schritt gibt Ihnen die Möglichkeit, mit einem CA-Server zu kommunizieren und von dort korrekt formatierte Zertifikate und Schlüssel zu erhalten.

Laden Sie anschließend die TLS-Zertifikate von [{{site.data.keyword.Bluemix_notm}} ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) herunter und speichern Sie den Inhalt in einem Ordner (z. B. ``$HOME/tls``). Dieser Schritt ermöglicht das Verschlüsseln der Daten während der Übertragung.

Öffnen Sie zum Schluss die JSON-Datei mit dem **Verbindungsprofil** auf der Anzeige **Übersicht** im Netzmonitor und suchen Sie die relevanten Variablen auf:
* URL für CA: `url` unter `certificateAuthorities`
* Administrator-ID: ``enrollId``
* Administratorkennwort: ``enrollSecret``
* CA-Name: ``caName``

Unter Verwendung des Fabric CA-Clients kann ein "enroll"-Aufruf an die Zertifizierungsstelle gesendet werden - durch Übergabe des TLS-Zertifikatspfads und der vier oben angegebenen Zeichenfolgen mit dem folgenden Befehl:
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

Die Binärdatei ``fabric-ca-client`` befindet sich im Verzeichnis ``$GOPATH/bin``. Das heißt, Sie müssen diesen Befehl von der richtigen Position auf Ihrer lokalen Maschine ausführen. Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```

Sie finden das Administratorzertifikat in `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Sie können das Administratorzertifikat dann vom Netzmonitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Netzmonitor.

Das CA-Stammzertifikat und der private Schlüssel für den Administrator befinden sich auch in folgenden Verzeichnissen:
* CA-Stammzertifikat: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* Privater Schlüssel des Administrators: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Es gibt zwei Hyperledger-Repositorys mit Ressourcen und Scripts, die besonders gut die programmgesteuerte Interaktion mit einer Zertifizierungsstelle veranschaulichen. Das Repository ``fabric-samples`` enthält das Beispiel "Balance Transfer" und das Repository ``fabric-sdk-node`` enthält eine Reihe von Tests für CA-Services. Wenn Sie beabsichtigen, Ihre Eintragungsanforderungen auf der Anwendungsseite auszugeben, müssen Sie die APIs gut kennen, die in den Paketen ``fabric-ca-client`` und ``fabric-client`` zugänglich gemacht werden müssen. Verwenden Sie diese Scripts und Repositorys als Basis für die Strukturierung Ihrer App.

Im Folgenden werden einige wichtige Codeausschnitte (Snippets) aus dem Beispiel "Balance Transfer" vorgestellt:

Zunächst muss ein Clientobjekt erstellt und eine Schlüssel/Wert-Speicherinstanz festgelegt werden, in der die Zertifikate und Schlüssel abgelegt werden sollen. Dies kann mithilfe einer einfachen Factory-Methode - ``newCryptoSuite`` - geschehen, die sich auf die ``Client``-Klasse von ``BaseClient`` erstreckt. Im Folgenden wird der Code gezeigt:

```
# <PUBLIC_PRIVATE_KEY_PATH> ist der Pfad auf Ihrer lokalen Maschine, in dem Sie Schlüssel und Zertifikat speichern wollen
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

Die gängige Praxis besteht darin, eine Umgebungsvariable zu exportieren, die den Schlüssel/Wert-Pfad auf Ihrer Maschine definiert, und diese an die obige Funktion zu übergeben. Da die KVS-Definition abgeschlossen ist, sollen nun verschiedene Methoden aus der Klasse ``FabricCAServices`` verwendet werden. Diese Klasse ist eine Implementierung des Fabric CA-Clients und ermöglicht somit die Kommunikation mit dem CA-Server. Zunächst müssen bestimmte Informationen an den CA-Client übergeben werden, nämlich die CA-URL:

```
# Die caURL kann manuell definiert oder über eine Umgebungsvariable festgelegt werden.
# Die copService-Variable ist am Anfang des Programms definiert.
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

Als Nächstes soll der "enroll"-Aufruf an den CA-Server gesendet werden. Dadurch wird ein privater Schlüssel und ein öffentlicher Schlüssel (in einem x509-Zertifikat enthalten und von der Zielzertifizierungsstelle signiert) an den Client zurückgegeben. Dies wird als "signcert" oder Eintragungszertifikat bezeichnet. Dieses Eintragungszertifikat oder "eCert" ist der zentrale Bestandteil, weil es Netzentitäten die Möglichkeit gibt, Transaktionen und Aufrufe zu überprüfen, die von dem Client stammen:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

Die abschließende Aufgabe besteht darin, die Verschlüsselungssuite festzulegen und den Benutzerkontext zu erstellen:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

Sie können das Administratorzertifikat dann vom Netzmonitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Netzmonitor.

## Anwendungen entwickeln
{: #developing-applications}
Sie können Ihre Anwendung in JavaScript oder Java entwickeln und die verfügbaren APIs in Hyperledger Fabric-Client-SDKs nutzen, um die Interaktion zwischen Ihrer Anwendung und Ihrem Netz zu ermöglichen. Eine Anwendung muss mindestens die folgenden Informationen enthalten:
* Name und Version des Chaincodes, der aufgerufen werden soll.
* API-Endpunktinformationen Ihrer Netzressourcen, einschließlich Anordnungsknoten, Zertifizierungsstellen und Peers.
* Funktionen zum Abfragen oder Aktualisieren des Hauptbuchs im Netz. Wenn Sie eine hohe Verfügbarkeit benötigen, müssen Sie eine Failover-Funktionalität für die Knoten in Ihrer Anwendung in Betracht ziehen.

Beispielanwendungen für den **Enterprise Plan** unter {{site.data.keyword.Bluemix_short}} finden Sie unter [Beispielanwendungen](howto/sample_applications.html). Verwenden Sie diese beispielhaften Chaincodes und Anwendungen als Vorlage für die Erstellung Ihrer eigenen Geschäftslösung.

## Netz-API-Endpunkte der Anwendung hinzufügen
Sie müssen Ihrer Anwendung API-Endpunkte Ihrer Netzressourcen hinzufügen, sodass sie mit Ihrem {{site.data.keyword.blockchain}}-Netz in {{site.data.keyword.Bluemix_short}} interagieren kann. Wenn Sie über kein {{site.data.keyword.blockchain}}-Netz in {{site.data.keyword.Bluemix_short}} verfügen, können Sie ein Netz mit Starter Plan oder Enterprise Plan erstellen. Weitere Informationen finden Sie unter [Starter Plan-Netz steuern](get_start_starter_plan.html) und [Enterprise Plan-Netz steuern](get_start.html.

Sie finden die API-Endpunkte im Verbindungsprofils Ihres Netzes. Das Verbindungsprofil liegt im JSON-Format vor und enthält die API-Endpunktinformationen und die Eintrags-IDs (enrollIDs) und geheimen Schlüssel (secrets) für Ihre Netzressourcen, das heißt, für Anordnungsknoten, Zertifizierungsstellen (CA) und Peerknoten. Ihre Anwendung interagiert mit Peers und anderen Netzressourcen durch diese API-Endpunkte.

1. Rufen Sie die API-Endpunktinformationen Ihrer Netzressourcen in Ihrem Netzmonitor mit einer der folgenden Methoden ab:
	* Um die API-Endpunktinformationen, die sich auf einen bestimmten Chaincode beziehen, in der Anzeige des Kanals abzurufen, auf dem der Chaincode ausgeführt wird, suchen Sie den Chaincode und klicken Sie auf die Schaltfläche **JSON**.
	![API-Endpunkte pro Chaincode](images/channel_chaincode_detail.png "API-Endpunkte pro Chaincode")
	* Um einen vollständigen Satz von API-Endpunktinformationen zu allen Ihren Netzressourcen abzurufen, klicken Sie auf die Schaltfläche **Verbindungsprofil** in der Anzeige "Übersicht>".
	![Verbindungsprofil im Netzmonitor](images/service_credentials.png "Verbindungsprofil im Netzmonitor")

2. Ermitteln Sie die Informationen zu API-Endpunkten Ihrer Netzressourcen, die in etwa wie im folgenden Beispiel aussehen:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**Hinweis**: Wenn Sie zusätzliche Peers im Netz als Ziele verwenden wollen (z. B. wenn Sie die Bewilligung durch einen Peer benötigen, der nicht zu Ihrer Organisation gehört), müssen Sie die richtigen API-Endpunktinformationen dieser Peers anfordern. Außerdem müssen Sie die Zertifikate der Zertifizierungsstelle für die andere Organisation speichern, um Antworten überprüfen zu können, die an Ihre Anwendung zurückgegeben werden. Diese Informationen gehen aus Ihrem Verbindungsprofil nicht hervor. Sie müssen sich daher an den entsprechenden Administrator der Cloud Foundry-Organisation wenden und diese Informationen mit einer Out-of-band-Operation anfordern. Die URL des Anordnungsservice ist für das gesamte Netz gültig; Sie benötigen keinerlei mitgliedsspezifische Informationen für diesen Anordnungsservice.

3. Fügen Sie die API-Endpunktinformationen in eine Konfigurationsdatei Ihrer Anwendung ein, wie aus folgendem Beispiel hervorgeht:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## Anwendungen hosten
Sie können Ihre Anwendung auf Ihrem lokalen Dateisystem hosten oder sie mit einer Push-Operation an {{site.data.keyword.Bluemix_notm}} übertragen. Um Ihre Anwendung mit einer Push-Operation an {{site.data.keyword.Bluemix_notm}} zu übertragen, führen Sie die folgenden Schritte aus:
1. Installieren Sie das [Cloud Foundry-Befehlszeileninstallationsprogramm ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://github.com/cloudfoundry/cli/releases).  Testen Sie Ihre Installation mit dem Befehl `cf`.
    * Wenn die Installation erfolgreich war, sollte eine entsprechende Textausgabe auf Ihrem Terminal angezeigt werden.
    * Wenn "Befehl nicht gefunden" angezeigt wird, war Ihre Installation entweder nicht erfolgreich oder CF ist nicht in Ihrem Systempfad enthalten.
2. Richten Sie den API-Endpunkt ein und melden Sie sich mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID und dem Kennwort an, indem Sie die folgenden Befehle eingeben:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
3. Wechseln Sie in das Verzeichnis Ihrer Anwendung und führen Sie eine Push-Operation für Ihre Anwendung durch, indem Sie den folgenden Befehl eingeben. Dies kann einige Minuten je nach Größe Ihrer Anwendung dauern. Es werden Protokolle von {{site.data.keyword.Bluemix_notm}} auf Ihrem Terminal angezeigt; sobald die Anwendung erfolgreich gestartet wurde, werden keine Protokolle mehr angezeigt.
	```
	> cf push NAME_IHRER_APP
	```
	Sie können Ihre Anwendungsprotokolle prüfen, indem Sie die folgenden Befehle eingeben:
	* `> cf logs NAME_IHRER_APP`
	* `> cf logs NAME_IHRER_APP --recent`


## Anwendung vom Netz trennen
{: #disconnect}
Führen Sie die folgenden Schritte aus, um die Verbindung zwischen Ihrer Anwendung und dem {{site.data.keyword.blockchain}}-Netz unter {{site.data.keyword.Bluemix_short}} zu trennen.
1. Entfernen Sie die API-Endpunktinformationen aus der Konfigurationsdatei Ihrer Anwendung. Weitere Informationen finden Sie unter [Netz-API-Endpunkte der Anwendung hinzufügen](#adding-network-api-endpoints-to-your-application).
2. Löschen Sie Ihren Chaincode-Container.
	1. Suchen Sie in der Anzeige "Kanal" des Netzmonitors den Kanal, auf dem Ihr Chaincode installiert ist.
	2. Suchen Sie in der betreffenden Kanalanzeige den Chaincode, den Sie inaktivieren möchten.
	3. Klicken Sie auf die Schaltfläche **Löschen** und klicken Sie in der Anzeige zum Löschen von Chaincode auf **Übergeben**. Ihr Chaincode-Container wird entfernt.
	![Chaincode löschen](images/channel_chaincode_detail.png "Chaincode löschen")

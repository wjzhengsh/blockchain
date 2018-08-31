---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Anwendungen entwickeln
{: #dev_app}
Mit Anwendungen können Sie den Chaincode aufrufen, um ein kanalspezifisches Ledger in Ihrem Blockchain-Netz abzufragen oder zu aktualisieren.
{:shortdesc}

## Anwendungsentwicklungsumgebung einrichten
Sie müssen die vorausgesetzte Software installieren, um Anwendungen zu entwickeln, die mit dem Blockchain-Netz unter {{site.data.keyword.cloud}} interagieren können.
{:shortdesc}

*	Git ([Git-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://git-scm.com/downloads){:new_window})  
	Git ist ein Tool zur Versionssteuerung für die Entwicklung von Chaincode und die Softwareentwicklung im Allgemeinen. Git Bash, das mit Git unter Windows installiert wird, ist eine leistungsfähige Alternative zur Windows-Eingabeaufforderung.

*	GoLang ([GoLang-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://golang.org/dl){:new_window})  
	Bei der GoLang-Installation wird eine Reihe von Go-CLI-Tools installiert, die Sie beim Schreiben von Chaincode unterstützen. Sie können beispielsweise mit dem Befehl `go build` prüfen, ob Ihr Chaincode tatsächlich kompiliert wurde, bevor Sie versuchen, ihn in einem Netz bereitzustellen.

	Entnehmen Sie den [Installationsanweisungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://golang.org/doc/install){:new_window}, wie die Umgebungsvariablen eingestellt werden müssen. Prüfen Sie Ihren `GOPATH` mit dem folgenden Befehl. Beachten Sie, dass Ihr Wert für `GOPATH` nicht mit dem Beispiel übereinstimmen muss. Es ist nur wichtig, dass diese Variable auf ein gültiges Verzeichnis in Ihrem Dateisystem gesetzt ist.

	```
	$ echo $GOPATH
C:\gopath
	```
	{:codeblock}

	Anschließend können Sie Ihre GoLang-Installation überprüfen, indem Sie GoLang-Code mit dem Beispiel [ Hello World  ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://golang.org/doc/install#testing){:new_window} erstellen.

* Java ([Java-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://java.com/en/download/){:new_window}).
*	Node.js ([Node.js-Downloadseite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://nodejs.org/en/download/){:new_window}).

Die Entwicklung von Chaincode wird zum gegenwärtigen Zeitpunkt in Node.js, Go und Java unterstützt.

## Clientseitige Zertifikate generieren
Auf den x509-Standard und die Public Key Infrastructure soll hier nicht näher eingegangen werden. Hierzu gibt es eine Vielzahl externer Ressourcen. Es sei nur erwähnt, dass der Kommunikationsfluss in Fabric die Sign/Verify-Operationen für jede Nahtstelle (Touchpoint) verwendet. Daher muss jeder Client, der Aufrufe (d. h. Ledgerabfragen oder -aktualisierungen) an das Netz sendet, die Nutzdaten signieren und ein ordnungsgemäß signiertes x509-Zertifikat zu Überprüfungszwecken anhängen. Der private Schlüssel und das signierte Zertifikat ergeben zusammen mit einer MSP-ID und dem Stammzertifikat der Zertifizierungsstelle das "Benutzerkontext"-Objekt.

Der Prozess der "Eintragung" ist hier von Bedeutung. Dabei werden die Schlüssel und Zertifikate, die die Erstellung des Objekts ermöglichen, von der entsprechenden Zertifizierungsstelle abgerufen. Wenn Sie das Benutzerkontextobjekt erstellt haben, können Sie eine API von Ihrer Anwendung aus aufrufen, um den Benutzerkontext festzulegen ("set") oder abzurufen ("get"). Nun ist die Anwendung (d. h. der Client) mit allen erforderlichen Artefakten ausgestattet und bereit, mit dem Netz zu kommunizieren. Es werden zwei Möglichkeiten für den Abruf der Schlüssel und Zertifikate gezeigt: Befehlszeile und SDK. 

### Befehlszeile
Die Vorgehensweise über die Befehlszeile stellt die einfachere der beiden Methoden dar. 

1. Befolgen Sie die Anweisungen für den Build des [Fabric CA-Clients ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Dieser Schritt gibt Ihnen die Möglichkeit, mit einem CA-Server zu kommunizieren und von dort korrekt formatierte Zertifikate und Schlüssel zu erhalten. 

2. Laden Sie die TLS-Zertifikate von {{site.data.keyword.cloud_notm}} herunter, die dem verwendeten Serviceplan, Standort und Cluster entsprechen. 
  - TLS-Stammzertifikat für den Starter Plan  
    - US: [us01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [TLS-Stammzertifikat für den Enterprise Plan ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Speichern Sie den Inhalt in einem Ordner, z. B. ``$HOME/tls``. Dieser Schritt ermöglicht das Verschlüsseln der Daten während der Übertragung.

3. Öffnen Sie die JSON-Datei mit dem **Verbindungsprofil** in der Anzeige **Übersicht** im Network Monitor und suchen Sie die folgenden Variablen: 
  * URL für CA: `url` unter `certificateAuthorities`
  * Administrator-ID: ``enrollId``
  * Administratorkennwort: ``enrollSecret``
  * CA-Name: ``caName``

4. Unter Verwendung des Fabric CA-Clients kann ein "enroll"-Aufruf an die Zertifizierungsstelle gesendet werden - durch Übergabe des TLS-Zertifikatspfads und der vier oben angegebenen Zeichenfolgen mit dem folgenden Befehl:
  ```
  $GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Die Binärdatei ``fabric-ca-client`` befindet sich im Verzeichnis ``$GOPATH/bin``. Das heißt, Sie müssen diesen Befehl von der richtigen Position auf Ihrer lokalen Maschine ausführen.  Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:
  ```
  ./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
  ```
  {:codeblock}

5. Sie finden das Administratorzertifikat in `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Sie können das Administratorzertifikat dann vom Network Monitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Network Monitor.

  Das CA-Stammzertifikat und der private Schlüssel für den Administrator befinden sich auch in folgenden Verzeichnissen:
  * CA-Stammzertifikat: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * Privater Schlüssel des Administrators: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Es gibt eine Reihe von Fabric-Repositorys mit Ressourcen und Scripts, die besonders gut die programmgesteuerte Interaktion mit einer Zertifizierungsstelle veranschaulichen. Das Repository ``fabric-samples`` enthält das Beispiel ``balance transfer`` und das Repository ``fabric-sdk-node`` enthält eine Reihe von Tests für CA-Services. Wenn Sie beabsichtigen, Ihre Eintragungsanforderungen auf der Anwendungsseite auszugeben, müssen Sie die APIs gut kennen, die in den Paketen ``fabric-ca-client`` und ``fabric-client`` zugänglich gemacht werden müssen. Verwenden Sie diese Scripts und Repositorys als Basis für die Strukturierung Ihrer App.

Im Folgenden werden eine Reihe wichtiger Snippets aus dem Beispiel `balance transfer` kurz dargestellt:

Zuerst muss ein Clientobjekt erstellt und eine Schlüssel/Wert-Speicherinstanz festgelegt werden, in der die Zertifikate und Schlüssel abgelegt werden sollen. Dies kann mithilfe einer einfachen Factory-Methode - ``newCryptoSuite`` - geschehen, die sich auf die ``Client``-Klasse von ``BaseClient`` erstreckt. Im Folgenden wird der Code gezeigt:

```
# <PUBLIC_PRIVATE_KEY_PATH> ist der Pfad auf Ihrer lokalen Maschine, in dem Sie Schlüssel und Zertifikat speichern wollen
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```
{:codeblock}

Die gängige Praxis besteht darin, eine Umgebungsvariable zu exportieren, die den Schlüssel/Wert-Pfad auf Ihrer Maschine definiert, und diese an die obige Funktion zu übergeben. Da die KVS-Definition abgeschlossen ist, sollen nun verschiedene Methoden aus der Klasse ``FabricCAServices`` verwendet werden. Diese Klasse ist eine Implementierung des Fabric CA-Clients und ermöglicht somit die Kommunikation mit dem CA-Server. Zunächst müssen bestimmte Informationen an den CA-Client übergeben werden, nämlich die CA-URL:

```
# Die caURL kann manuell definiert oder über eine Umgebungsvariable festgelegt werden.
# Die copService-Variable ist am Anfang des Programms definiert.
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```
{:codeblock}

Als Nächstes soll der "enroll"-Aufruf an den CA-Server gesendet werden. Dadurch wird ein privater Schlüssel und ein öffentlicher Schlüssel (in einem x509-Zertifikat enthalten und von der Zielzertifizierungsstelle signiert) an den Client zurückgegeben. Dies wird als "signcert" oder Eintragungszertifikat bezeichnet. Dieses Eintragungszertifikat oder "eCert" ist der zentrale Bestandteil, weil es Netzentitäten die Möglichkeit gibt, Transaktionen und Aufrufe zu überprüfen, die von dem Client stammen:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```
{:codeblock}

Die abschließende Aufgabe besteht darin, die Verschlüsselungssuite festzulegen und den Benutzerkontext zu erstellen:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```
{:codeblock}

Sie können das Administratorzertifikat dann vom Network Monitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Network Monitor.

## Anwendungen entwickeln
{: #developing-applications}

Eine einfache Möglichkeit, mit der Entwicklung von Anwendungen zu beginnen, ist die Verwendung von Chaincode- und Anwendungsbeispielen als Vorlagen für die Erstellung eigener Unternehmenslösungen. Beispielanwendungen für die Blockchain-Netze finden Sie in {{site.data.keyword.cloud_notm}} unter [Beispielanwendungen](howto/prebuilt_samples.html). Sie können Anwendungen auch völlig neu auf der Basis Ihrer Geschäftsanforderungen entwickeln.

Sie können Ihre Anwendung in JavaScript oder Java entwickeln und die verfügbaren APIs in Hyperledger Fabric-Client-SDKs nutzen, um die Interaktion zwischen Ihrer Anwendung und Ihrem Netz zu ermöglichen.  Eine Anwendung muss mindestens die folgenden Informationen enthalten:
* Name und Version des Chaincodes, der aufgerufen werden soll.
* API-Endpunktinformationen Ihrer Netzressourcen, einschließlich Anordnungsknoten, Zertifizierungsstellen und Peers.
* Funktionen zum Abfragen oder Aktualisieren des Ledgers im Netz.  Wenn Sie eine hohe Verfügbarkeit benötigen, müssen Sie eine Failover-Funktionalität für die Knoten in Ihrer Anwendung in Betracht ziehen.

### Fabric-SDKs
{: #use-sdks}

Fabric bietet zurzeit SDKs für Node.js und Java und wird in zukünftigen Releases weitere Programmiersprachen, z. B. Python, REST oder GO, unterstützen. Weitere Informationen zu Fabric-SDKs und zum Einsatz dieser SDKs finden Sie in den folgenden Dokumentationen: 

- [Hyperledger Fabric-Dokumentation zum Node-SDK ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/){:new_window}
- [Hyperledger Fabric-Dokumentation zum Java-SDK ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-sdk-java){:new_window}

### Zeitlimitwerte in Fabric-SDKs festlegen
{: #set-timeout-in-sdk}

Mit Fabric-SDKs werden Standardzeitlimitwerte in Clientanwendungen für Ereignisse im Blockchain-Netz festgelegt. Das folgende Beispiel veranschaulicht die Standardzeitlimiteinstellungen im Fabric-Java-SDK. Der Dateipfad lautet `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

Möglicherweise müssen Sie jedoch die Standardzeitlimitwerte in Ihrer eigenen Anwendung ändern. Beispiel: Wenn Ihre Anwendung eine Transaktion mit einer Antwortzeit von mehr als 5000 ms - hierbei handelt es sich um den Standardzeitlimitwert für eine Ereignishubverbindung - aufruft, wird ein Fehler aufgrund einer fehlgeschlagenen Transaktion ausgegeben, da das Aufrufereignis nach 5000 ms beendet wird, bevor die Transaktion abgeschlossen ist. Sie können die Systemeigenschaft so festlegen, dass die Standardwerte der Clientanwendung überschrieben werden. Da die Standardwerte vor dem Festlegen der Systemeigenschaft initialisiert werden, ist diese möglicherweise nicht wirksam. Daher müssen Sie die Systemeigenschaft für das Zeitlimit in einem statischen Konstrukt in der Clientanwendung festlegen. Das folgende Beispiel veranschaulicht das Ändern des Zeitlimitwerts für Ereignishubverbindungen in 15000 ms im Fabric-Java-SDK. Der Dateipfad lautet `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}


## Netz-API-Endpunkte der Anwendung hinzufügen
Sie müssen Ihrer Anwendung API-Endpunkte Ihrer Netzressourcen hinzufügen, sodass sie mit Ihrem {{site.data.keyword.blockchain}}-Netz in {{site.data.keyword.Bluemix_short}} interagieren kann.  Wenn Sie über kein {{site.data.keyword.blockchain}}-Netz in {{site.data.keyword.Bluemix_short}} verfügen, können Sie ein Netz mit dem Starter Plan oder dem Enterprise Plan erstellen. Weitere Informationen finden Sie unter [Starter Plan-Netz steuern](get_start_starter_plan.html) und [Enterprise Plan-Netz steuern](get_start.html).

Sie finden die API-Endpunkte im Verbindungsprofils Ihres Netzes. Das Verbindungsprofil liegt im JSON-Format vor und enthält die API-Endpunktinformationen und die Eintrags-IDs (enrollIDs) und geheimen Schlüssel (secrets) für Ihre Netzressourcen, das heißt, für Anordnungsknoten, Zertifizierungsstellen (CA) und Peerknoten. Ihre Anwendung interagiert mit Peers und anderen Netzressourcen durch diese API-Endpunkte.

1. Rufen Sie die API-Endpunktinformationen Ihrer Netzressourcen in Ihrem Network Monitor mit einer der folgenden Methoden ab:
	* Um die API-Endpunktinformationen, die sich auf einen bestimmten Chaincode beziehen, in der Anzeige des Kanals abzurufen, auf dem der Chaincode ausgeführt wird, suchen Sie den Chaincode und klicken Sie auf die Schaltfläche **JSON**.
	![API-Endpunkte pro Chaincode](images/channel_chaincode.png "API-Endpunkte pro Chaincode")
	* Um einen vollständigen Satz von API-Endpunktinformationen zu allen Ihren Netzressourcen abzurufen, klicken Sie auf die Schaltfläche **Verbindungsprofil** in der Anzeige "Übersicht>".
	![Verbindungsprofil im Network Monitor](images/service_credentials.png "Verbindungsprofil im Network Monitor")

2. Ermitteln Sie die Informationen zu API-Endpunkten Ihrer Netzressourcen, die in etwa wie im folgenden Beispiel aussehen:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```
	{:codeblock}
	**Hinweis**: Wenn Sie zusätzliche Peers im Netz als Ziele verwenden wollen (z. B. wenn Sie die Bewilligung durch einen Peer benötigen, der nicht zu Ihrer Organisation gehört), müssen Sie die richtigen API-Endpunktinformationen dieser Peers anfordern.  Außerdem müssen Sie die Zertifikate der Zertifizierungsstelle für die andere Organisation speichern, um Antworten überprüfen zu können, die an Ihre Anwendung zurückgegeben werden. Diese Informationen gehen aus Ihrem Verbindungsprofil nicht hervor. Sie müssen sich daher an den entsprechenden Administrator der Cloud Foundry-Organisation wenden und diese Informationen mit einer Out-of-band-Operation anfordern. Die URL des Anordnungsservice ist für das gesamte Netz gültig; Sie benötigen keinerlei mitgliedsspezifische Informationen für diesen Anordnungsservice.

3. Fügen Sie die API-Endpunktinformationen in eine Konfigurationsdatei Ihrer Anwendung ein, wie aus folgendem Beispiel hervorgeht:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```
	{:codeblock}

## CouchDB-Indizes verwenden

Wenn das Netz CouchDB verwendet und Sie die CouchDB-Indexierungsfunktion (zur Verbesserung der CouchDB-Leistung) nutzen möchten, müssen die Indizes zusammen mit dem Chaincode in einem Paket enthalten sein.

Weitere Informationen zu CouchDB und zum Einrichten von Indizes finden Sie in der [Fabric-Dokumentation zu CouchDB ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html).

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
    {:codeblock}
3. Wechseln Sie in das Verzeichnis Ihrer Anwendung und führen Sie eine Push-Operation für Ihre Anwendung durch, indem Sie den folgenden Befehl eingeben. Dies kann einige Minuten je nach Größe Ihrer Anwendung dauern. Es werden Protokolle von {{site.data.keyword.Bluemix_notm}} auf Ihrem Terminal angezeigt; sobald die Anwendung erfolgreich gestartet wurde, werden keine Protokolle mehr angezeigt.
	```
	> cf push NAME_IHRER_APP
	```
	{:codeblock}
	Sie können Ihre Anwendungsprotokolle prüfen, indem Sie die folgenden Befehle eingeben:
	* `> cf logs NAME_IHRER_APP`
	* `> cf logs NAME_IHRER_APP --recent`


## Anwendung vom Netz trennen
{: #disconnect}
Führen Sie die folgenden Schritte aus, um die Verbindung zwischen Ihrer Anwendung und dem {{site.data.keyword.blockchain}}-Netz unter {{site.data.keyword.Bluemix_short}} zu trennen.
1. Entfernen Sie die API-Endpunktinformationen aus der Konfigurationsdatei Ihrer Anwendung. Weitere Informationen finden Sie unter [Netz-API-Endpunkte der Anwendung hinzufügen](#adding-network-api-endpoints-to-your-application).
2. Löschen Sie Ihren Chaincode-Container.
	1. Suchen Sie in der Anzeige "Kanal" des Network Monitor den Kanal, auf dem Ihr Chaincode installiert ist.
	2. Suchen Sie in der betreffenden Kanalanzeige den Chaincode, den Sie inaktivieren möchten.
	3. Klicken Sie auf die Schaltfläche **Löschen** und klicken Sie in der Anzeige zum Löschen von Chaincode auf **Übergeben**. Ihr Chaincode-Container wird entfernt.
	![Chaincode löschen](images/channel_chaincode.png "Chaincode löschen")

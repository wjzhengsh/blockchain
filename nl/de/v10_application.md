---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Anwendungen entwickeln
{: #dev_app}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform stellt APIs bereit, die Sie verwenden können, um eine Verbindung zwischen Anwendungen und Ihrem Blockchain-Netz herzustellen. Sie können die API-Endpunkte des Netzes im Verbindungsprofil verwenden, um Ihren Chaincode aufzurufen und das kanalspezifische Ledger auf Ihren Peers zu aktualisieren oder abzufragen. Außerdem können Sie die APIs in der [Swagger-Benutzerschnittstelle](howto/swagger_apis.html) verwenden, um Knoten, Kanäle und die Mitglieder des Netzes zu verwalten.
{:shortdesc}

Mithilfe des vorliegenden Lernprogramms können Sie sich mit der Vorgehensweise beim Zugriff auf die {{site.data.keyword.blockchainfull_notm}} Platform-APIs und mit der Verwendung dieser APIs zur Eintragung und Registrierung Ihrer Anwendung beim Netz vertraut machen. Hier erfahren Sie außerdem, wie Sie mit dem Netz interagieren und Transaktionen über Ihre Anwendung absetzen können. Das Lernprogramm basiert auf dem Lernprogramm zum [Schreiben Ihrer ersten Anwendung ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) in der Hyperledger Fabric-Dokumentation. Sie werden häufig die gleichen Dateien und Befehle wie im Lernprogramm zum **Schreiben Ihrer ersten Anwendung** benutzen, diese jedoch zur Interaktion mit einem Netz unter {{site.data.keyword.blockchainfull_notm}} Platform einsetzen. Dieses Lernprogramm enthält Beschreibungen zu allen Schritten der Anwendungsentwicklung mit dem Hyperledger Fabric-Node-SDK. Außerdem erfahren Sie hier, wie Benutzer alternativ zur Verwendung des SDK mithilfe des Fabric-CA-Clients eingetragen und registriert werden können.

Zusätzlich zu diesem Lernprogramm können Sie außerdem Beispielanwendungen und Chaincode verwenden, der von {{site.data.keyword.blockchainfull}} Platform in Form von Vorlagen bereitgestellt wird, wenn Sie eigene Business Solutions erstellen. Weitere Informationen finden Sie unter [Beispielanwendungen bereitstellen](howto/prebuilt_samples.html).

## Voraussetzungen
Sie müssen die folgenden Voraussetzungen erfüllen, um das Lernprogramm zum **Schreiben Ihrer ersten Anwendung** unter {{site.data.keyword.blockchainfull_notm}} Platform verwenden zu können.

- Wenn Ihnen unter {{site.data.keyword.cloud_notm}} kein Blockchain-Netz zur Verfügung steht, dann müssen Sie ein solches Netz entweder mit einem Starter- oder Enterprise-Mitgliedschaftsplan erstellen. Weitere Informationen zu diesem Thema finden Sie im Abschnitt zum [Erstellen eines Starter Plan-Netzes](get_start_starter_plan.html#creating-a-network) oder im Abschnitt zum [Erstellen eines Enterprise Plan-Netzes](get_start.html#creating-a-network).

  Nachdem Sie den Network Monitor Ihres Netzes aufgerufen haben, müssen Sie mindestens einen Peer für Ihre Organisation zur Anzeige "Übersicht" hinzufügen. Anschließend muss mindestens ein Kanal in Ihrem Netz erstellt werden. Weitere Informationen finden Sie unter [Kanal erstellen](howto/create_channel.html#creating-a-channel). **Hinweis:** Wenn Sie ein Starter Plan-Netz verwenden, dann existiert in Ihrem Netz bereits ein Kanal mit dem Namen `defaultchannel`, den Sie zum Bereitstellen des Chaincodes verwenden können.

- Installieren Sie die erforderlichen Tools, um Hyperledger Fabric-Beispiele herunterzuladen und das Node-SDK zu verwenden.
  * [Curl ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") oder [Git ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- Installieren Sie die Hyperledger Fabric-Beispiele, indem Sie das Verzeichnis `fabric-samples` herunterladen. Sie können dazu den [Leitfaden zur Einführung![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html) in der Hyperledger Fabric-Dokumentation verwenden.

  Nachdem Sie das Verzeichnis `fabric-samples` auf Ihr lokales System heruntergeladen haben, müssen Sie zum Verzeichnis `fabric-samples/fabcar` navigieren. Sie können eine Kopie dieses Verzeichnisses erstellen und es so umbenennen, dass Sie sich mit der Beispielanwendung in einem bereinigten Verzeichnis vertraut machen und diese testen können.

  Führen Sie im Verzeichnis `fabcar` den Befehl `npm install` aus, um die erforderlichen Pakete zur Verwendung des Fabric-SDK zu installieren, z. B. `fabric-client` und `fabric-ca-client`.

- Installieren und instanziieren Sie den fabcar-Chaincode mithilfe des [Network Monitor](howto/install_instantiate_chaincode.html#installchaincode) auf Ihrem Kanal. Der fabcar-Chaincode befindet sich im Ordner `fabric-samples` unter `fabric-samples > Chaincode > fabcar > Go`.

- Rufen Sie das Verbindungsprofil Ihres Netzes in der Anzeige "Übersicht" des Network Monitor ab. Speichern Sie das Verbindungsprofil im Verzeichnis `fabcar` und benennen Sie es in `creds.json` um.

## Fabric-SDKs verwenden
{: #using-the-fabric-sdks}

Die Hyperledger Fabric-SDKs stellen eine leistungsstarke Gruppe von APIs zur Verfügung, die Anwendungen die Interaktion mit Blockchain-Netzen ermöglichen. Die aktuellste Version der Liste der unterstützten Sprachen finden Sie in der [Hyperledger Fabric SDK Community-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community-Dokumentation"){:new_window}. Es wird empfohlen, mit {{site.data.keyword.blockchainfull_notm}} Platform das Node-SDK oder das Java-SDK zu verwenden. Sie können mehr über die APIs erfahren, die die SDKs in den einzelnen Repositorys der SDKs bereitstellen.

Dieses Lernprogramm nutzt das [Node-SDK ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Node-SDK"){:new_window}, um Ihre Anwendung zu registrieren und einzutragen und sie anschließend zum Absetzen von Transaktionen durch Aufruf und Abfragen des Chaincodes zu verwenden. In diesem Lernprogramm werden die Informationen beschrieben, die Sie für das SDK bereitstellen müssen, damit Ihre Anwendung eine Verbindung zu Ihrem Blockchain-Netz herstellen kann. Außerdem enthält es eine Einführung zu bestimmten APIs, die Sie verwenden können, sowie zur Interaktion des SDK mit dem Blockchain-Netz und zur Übergabe von Transaktionen an das Blockchain-Netz.

## Netz-API-Endpunkte der Anwendung hinzufügen
{: #api-endpoints}

Sie müssen für Ihre Anwendung die API-Endpunkte bestimmter Netzressourcen (einschließlich der Anordnungs-, CA- und Peerknoten) im Blockchain-Netz unter {{site.data.keyword.cloud_notm}} bereitstellen. Ihre Anwendung kann über diese API-Endpunkte mit dem Netz interagieren. Sie finden die API-Endpunkte im Verbindungsprofil Ihres Netzes. Das Verbindungsprofil ist im JSON-Format verfügbar und enthält die Informationen zu den API-Endpunkten sowie die Eintrags-IDs (enrollIDs) und die geheimen Schlüssel für Ihre Netzressourcen.

1. Rufen Sie die API-Endpunktinformationen Ihrer Netzressourcen in Ihrem Network Monitor mit einer der folgenden Methoden ab:
  * Klicken Sie in der Anzeige "Übersicht" auf **Verbindungsprofil**. Das Verbindungsprofil enthält einen vollständigen Satz der API-Endpunktinformationen zu allen Netzressourcen. ![Verbindungsprofil im Network Monitor](images/service_credentials.png "Verbindungsprofil im Network Monitor")

  * Wenn in Ihrem Netz Chaincode ausgeführt wird, dann können Sie die speziellen API-Endpunktinformationen für diesen Chaincode abrufen. Klicken Sie in der Anzeige "Kanäle" auf die Kanalzeile, in der der Chaincode ausgeführt wird, um die entsprechende Kanalanzeige zu öffnen. Suchen Sie anschließend den Chaincode und klicken Sie auf die Schaltfläche **JSON**.
	   ![API-Endpunkte pro Chaincode](images/channel_chaincode.png "API-Endpunkte pro Chaincode")

2. Suchen Sie die Informationen zu den API-Endpunkten Ihrer Netzressourcen, die der URL der Zeile `peer1-org1` im folgenden Beispiel entsprechen:
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **Hinweis**: In bestimmten Fällen kann es erforderlich sein, Netzressourcen außerhalb Ihrer Organisation mit Ihrer Anwendung zu adressieren. Wenn beispielsweise eine [Bewilligungsrichtlinie](howto/install_instantiate_chaincode.html#specifying-chaincode-endorsement-policies) für den Chaincode vorsieht, dass Bewilligungen von anderen Organisationen auf dem Kanal erforderlich sind, dann müssen Sie die Endpunktinformationen der entsprechenden Peers und die zugehörigen TLS-Zertifikate anfordern. Diese Informationen befinden sich im Peerabschnitt des Verbindungsprofils. Allerdings müssen Sie den Administrator der anderen Organisationen kontaktieren, um in Erfahrung zu bringen, welche Peers den jeweiligen Kanälen hinzugefügt wurden.

3. Fügen Sie die API-Endpunktinformationen in eine Konfigurationsdatei Ihrer Anwendung ein, wie aus folgendem Beispiel hervorgeht:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  Sie können auch [HEAD-Anforderungen](howto/monitor_network.html#monitor-nodes) an diese Endpunkte senden, um die Verfügbarkeit Ihrer Netzressourcen zu überprüfen.

  Wenn Sie die Fabric-SDKs verwenden, dann können Sie die Netzverbindung auch über das Verbindungsprofil herstellen. In diesem Lernprogramm werden die Endpunktinformationen Ihres Netzes für das SDK manuell bereitgestellt. In einem späteren Abschnitt finden Sie aber ein Lernprogramm und eine Anleitung zur [Verwendung Ihres Verbindungsprofils mit dem SDK](#using-your-connection-profile-with-the-sdk).

## Anwendung eintragen
{: #enroll-app}

Bevor Sie unter {{site.data.keyword.blockchainfull_notm}} Platform eine Verbindung zwischen einer Anwendung und Ihrem Netz herstellen können, müssen Sie die Authentizität Ihrer Anwendung gegenüber dem Netz nachweisen. Auf den x509-Standard und die Public Key Infrastructure soll hier nicht näher eingegangen werden. Hierzu gibt es eine Vielzahl [externer Ressourcen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window}. Vereinfacht ausgedrückt verwenden die Kommunikationsabläufe in Fabric Signier- und Verifizierungsoperationen bei jedem Touchpoint. Aus diesem Grund muss jede Anwendung, die Aufrufe (z. B. Ledgerabfragen oder -aktualisierungen) an das Netz sendet, die Nutzdaten mit dem privaten Schlüssel signieren und ein ordnungsgemäß signiertes x509-Zertifikat zu Überprüfungszwecken beifügen. Als **Eintragung** wird der Vorgang zum Generieren der erforderlichen Schlüssel und Zertifikate über die zuständige Zertifizierungsstelle bezeichnet. Nach der Eintragung ist Ihre Anwendung bereit für die Kommunikation mit dem Netz.

In diesem Abschnitt wird im Rahmen des Lernprogramms zum **Schreiben Ihrer ersten Anwendung** erläutert, wie Schlüssel und Zertifikate mithilfe des Fabric-SDK abgerufen werden können. Außerdem erfahren Sie hier, wie der Fabric-CA-Client über die Befehlszeile verwendet werden kann, um Ihre Anwendung einzutragen.

<!-- this last sentance when the certificates article goes live --->

### Eintragung mithilfe des Fabric-SDK
{: #enroll-app-sdk}

Öffnen Sie im Verzeichnis `fabcar` im Ordner `fabric-samples` die Datei `enrollAdmin.js` in einem Texteditor.

1. Mit der Datei wird zuerst eine Instanz des Fabric-Clients erstellt.
  ```
  var fabric_client = new Fabric_Client();
  ```

2. Anschließend wird mit der Datei ein Schlüssel/Wert-Speicher (Key-value Store; KVS) zum Management Ihrer Zertifikate erstellt. Das SDK verwendet die Klasse [KeyValueStore ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} zum Erstellen des Schlüssel/Wert-Speichers und die Klasse [CryptoSuite ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} zur Durchführung der Verschlüsselungsberechnungen. Im Folgenden ist der relevante Codeblock dargestellt.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. Nachdem der KVS definiert wurde, können Sie verschiedene Methoden der Klasse [Fabric Client ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Client.html "Fabric Client") und die API des Fabric-CA-Clients verwenden, <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> um mit dem CA-Server zu kommunizieren. Sie müssen für das SDK den Namen und die URL Ihrer Zertifizierungsstelle angeben. Öffnen Sie die JSON-Datei mit dem **Verbindungsprofil** in der Anzeige **Übersicht** im Network Monitor und suchen Sie die folgenden Variablen im Abschnitt `certificateAuthorites`:
  * URL für CA: `url` unter `certificateAuthorities`
  * Administrator-ID: ``enrollId``
  * Administratorkennwort: ``enrollSecret``
  * CA-Name: `caName`

  **Bearbeiten** Sie die relevanten Zeilen der Datei `enrollAdmin.js` mit diesen Informationen wie folgt:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  Beispiel:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```
  {:codeblock}

  Der Fabric-Client überprüft dann, ob Ihre Anwendung bereits eingetragen wurde. **Bearbeiten** Sie die folgende Zeile mit dem Wert für `enrollID` aus Ihrem Verbindungsprofil:
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. Sie müssen den Aufruf für die Eintragung ("enroll") an den CA-Server senden. Ihr Administrator (`admin`) wurde bereits bei Ihrem Netz registriert. Mit dem Eintragungsaufruf werden ein privater und ein öffentlicher Schlüssel abgerufen, die in ein x509-Zertifikat eingebunden sind, das von der zuständigen Zertifizierungsstelle (CA) signiert wurde. Dieses eingebundene und signierte Zertifikat wird als signCert-Zertifikat bezeichnet. Das signCert-Zertifikat erlaubt Netzmitgliedern die Verifizierung von Aufrufen, die vom Client stammen. Sie müssen Ihren Organisationsnamen, das Kennwort und die MSP-ID aus der Berechtigungsnachweisdatei angeben. Im Abschnitt `certificateAuthorities` Ihrer Netzberechtigungsnachweise müssen Sie die Angaben für `enrollID`, `enrollSecret` und `x-mspid` suchen. **Bearbeiten** Sie den folgenden Codeblock mit diesen Werten und ersetzen Sie den relevanten Abschnitt in Ihrer Datei.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. Speichern Sie die Datei `enrollAdmin.js`.

Tragen Sie im Verzeichnis `fabcar` den Administrator ein, indem Sie den folgenden Befehl absetzen:
```
node enrollAdmin.js
```
{:codeblock}

Der Eintragungsbefehl generiert das signCert-Zertifikat und exportiert es in einen Ordner mit dem Namen `hfc-key-store`. Die zukünftigen Dateien in diesem Lernprogramm werden in diesem Ordner nach Ihren Zertifikaten suchen. Wenn Sie die Administratorzertifikate im Ordner `hfc-key-store` finden können, dann funktioniert der Eintragungsbefehl.

Wenn Sie das [Netz mit einem SDK betreiben wollen](#operate-sdk), dann müssen Sie das signCert-Zertifikat des Administrators auf {{site.data.keyword.blockchainfull}} Platform hochladen. Das signCert-Zertifikat des Administrators befindet sich im Ordner `hfc-key-store`. Öffnen Sie die Datei `admin` und kopieren Sie das Zertifikat in Anführungszeichen an die Position nach dem Feld `certificate`. Mit einem Tool oder Texteditor können Sie das Zertifikat ins PEM-Format umwandeln. Sie können das Administratorzertifikat dann vom Network Monitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Network Monitor. Dies ist nicht erforderlich, wenn Sie das SDK nur zum Aufrufen oder Abfragen des Chaincodes verwenden.

<!-- you can eliminate this section when the certificates article goes live --->

### Eintragung mithilfe des Fabric-CA-Clients durchführen
{: #enroll-app-caclient}

1. Laden Sie die [fabric-ca-Binärkomponenten](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) auf Ihr lokales System herunter und extrahieren Sie sie. Verschieben Sie den ausführbaren Client `fabric-ca` von dem Verzeichnis, in das er heruntergeladen wurde, in ein Verzeichnis, in dem er zur Ausführung von Befehlen eingesetzt werden kann.

2. Laden Sie die TLS-Zertifikate von {{site.data.keyword.cloud_notm}} herunter, die dem verwendeten Serviceplan, Standort und Cluster entsprechen.
  - TLS-Stammzertifikat für den Starter Plan  
    - US: [us01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [TLS-Stammzertifikat für den Enterprise Plan ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Speichern Sie den Inhalt in einem Ordner, z. B. ``$HOME/tls``. Dieser Schritt ermöglicht das Verschlüsseln der Daten während der Übertragung.

3. Öffnen Sie die JSON-Datei mit dem **Verbindungsprofil** in der Anzeige **Übersicht** im Network Monitor und suchen Sie die folgenden Variablen:
  * URL für CA: `url` unter `certificateAuthorities`
  * Administrator-ID: ``enrollId``
  * Administratorkennwort: ``enrollSecret``
  * CA-Name: ``caName``

4. Sie können den Fabric-CA-Client zum Senden eines `enroll`-Aufrufs an die Zertifizierungsstelle verwenden, indem Sie den Pfad der TLS-Zertifikate und die vier hier aufgeführten Zeichenfolgen mit dem folgenden Befehl übergeben:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Sie müssen diesen Befehl in dem Verzeichnis ausführen, in das der Fabric-CA-Client verschoben wird. Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. Sie finden das Administratorzertifikat in `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Sie können das Administratorzertifikat dann vom Network Monitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Network Monitor.

  Das CA-Stammzertifikat und der private Schlüssel für den Administrator befinden sich auch in folgenden Verzeichnissen:
  * CA-Stammzertifikat: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * Privater Schlüssel des Administrators: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`


## Anwendung registrieren
{: #register-app}

Nach der Generierung der clientseitigen Zertifikate müssen Sie Ihre Anwendung bei der Zertifizierungsstelle des Netzes registrieren. Durch die Registrierung wird Ihre Anwendung zur Liste der Komponenten hinzugefügt, die vom Netz erkannt werden können. Es hat sich bewährt, Anwendungen als separate Identität zu registrieren, anstatt `admin` zum Signieren von Anforderungen zu verwenden.

### Mit SDK registrieren
{: #register-app-sdk}

Mit der Datei `registerUser.js` können Sie die Anwendung als `user1` registrieren und eintragen, indem Sie das signCert-Zertifikat für `admin` verwenden. Öffnen Sie `registerUser.js` in einem Texteditor.

1. Geben Sie die CA-URL und den Namen für eine neue Instanz des Fabric-CA-Clients an.

2. Geben Sie den Wert für "enrollID" für die Methode `getUserContext` aus dem [Fabric-Client ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Client.html) an, um zu überprüfen, ob Ihr Administrator (`admin`) eingetragen wurde und zur Ausgabe dieser Anforderung berechtigt ist. **Bearbeiten** Sie den relevanten Codeblock in Ihrer Datei anhand des folgenden Beispiels:
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. Verwenden Sie den **Fabric-CA-Client** zum Registrieren und Eintragen des Benutzers bei der Zertifizierungsstelle (CA) und dann den **Fabric-Client** zum Erstellen des neuen signCert-Zertifikats. **Bearbeiten** Sie den folgenden Block mit Ihrer MSP-ID und der Organisationszugehörigkeit. Sie finden die Angaben für `x-affiliations` im Abschnitt für die Zertifizierungsstellen Ihrer Netzberechtigungsnachweise und können alle aufgelisteten Angaben für die Zugehörigkeit (affiliation) verwenden. Fügen Sie den Namen des Benutzers hinzu, der erstellt werden soll. Im fabcar-Beispiel wird standardmäßig der Benutzer `user1` verwendet.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. Speichern Sie die Datei `registerUser.js`.

Führen Sie den Befehl `node registerUser.js` aus, um `user1` zu registrieren und einzutragen. Wenn Sie die Zertifikate für `user1` im Ordner `hfc-key-store` finden können, dann arbeitet der Befehl korrekt. Eine Identität kann immer nur einmal registriert werden. Tritt ein Problem auf, dann wiederholen Sie die Ausführung von `registerUser.js` mit einem neuen Benutzernamen.

<!-- you can eliminate this section when the certificates article goes live --->

### Registrierung mithilfe des Fabric-CA-Clients durchführen
{: #register-app-caclient}

1. Setzen Sie den folgenden Befehl ab, um Ihre Zugehörigkeit und den Namen Ihrer Organisation im Blockchain-Netz zu suchen.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  Daraufhin werden die folgenden Informationen angezeigt:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  Notieren Sie den zweiten Wert für **affiliation**. Beispiel: `ibp.PeerOrg1`. Sie müssen diesen Wert im folgenden Befehl verwenden.

2. Führen Sie den folgenden Befehl aus, um den Peer zu registrieren.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  Geben Sie einen Namen und ein Kennwort für Ihren Peer an und ersetzen Sie `name` und `password` durch den Peernamen und das zugehörige Kennwort. Notieren Sie diese Informationen. Sie benötigen diese Informationen bei der Konfiguration Ihres Peers. Beispiel:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  Eine Identität kann immer nur einmal registriert werden. Tritt ein Problem auf, dann wiederholen Sie den Vorgang mit einem Befehl, für den ein neuer Benutzername und ein neues Kennwort angegeben werden.

  Wird der Befehl erfolgreich ausgeführt, dann werden die im folgenden Beispiel dargestellten Informationen angezeigt:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

### Registrierung mit Network Monitor durchführen

Alternativ können Sie Ihre Clientanwendung auch über die Registerkarte **Zertifizierungsstelle** des Network Monitor registrieren und eintragen. Weitere Anweisungen finden Sie in diesen [Informationen](v10_dashboard.html#ca).

## Transaktionen durch Aufruf und Abfrage des Chaincodes ausgeben
{: #invoke-query}

Ihre Anwendung muss mit dem gesamten Blockchain-Netz interagieren, um eine Transaktion zu übergeben.

1. Die Anwendung sendet einen Transaktionsvorschlag, der von den Peers auf dem Kanal bewilligt werden soll.
2. Die Peers, die die Bewilligung durchführen, geben die bewilligte Transaktion an die Anwendung zurück.
3. Die Anwendung sendet die bewilligte Transaktion an den Anordnungsservice, um die Transaktion dem Ledger hinzuzufügen.

Weitere Informationen zum vollständigen Transaktionsablauf finden Sie unter [Transaktionsablauf![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Transaktionsablauf"){:new_window} in der Hyperledger Fabric-Dokumentation. Nachdem Sie die ersten Schritte mit diesem Lernprogramm ausgeführt haben, sollten Sie den Abschnitt zur [Anwendungskonnektivität und -verfügbarkeit](#app-connectivity-availability) aufrufen. Dort finden Sie Tipps, wie Sie die Art und Weise, in der das SDK mit dem Netz interagieren soll, verwalten können.

In den folgenden Beispielen wird gezeigt, wie das Node-SDK die Netztopologie einrichtet, den Transaktionsvorschlag definiert und dann die Transaktion an das Netz übergibt. Sie können die Datei `invoke.js` verwenden, um Funktionen im `fabcar`-Chaincode aufzurufen. Diese Funktionen ermöglichen Ihnen das Erstellen und Übertragen von Assets im Blockchain-Ledger. Dieses Lernprogramm verwendet die Funktion `initLedger` zum Hinzufügen neuer Daten zu Ihrem Kanal und verwendet dann die Datei `query.js` zum Abfragen der Daten.

### Chaincode aufrufen
{: #invoke}

Öffnen Sie die Datei `invoke.js` in einem Texteditor.

1. Fügen Sie `var creds = require('./creds.json')` oben in der Datei hinzu. Diese Codezeile ermöglicht der Datei `invoke.js` das Lesen der Informationen aus der Berechtigungsnachweisdatei `creds.json`.

2. Verwenden Sie die Klasse [Fabric Client ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window}, um das Fabric-Netz mithilfe der API-Endpunkte Ihrer Netzressourcen einzurichten. Mit diesem Schritt definieren Sie den Kanal und die Peers, an die Ihr Client den Vorschlag übergeben wird, und den Anordnungsservice, an den das SDK dann die bewilligte Transaktion sendet. **Bearbeiten** Sie den folgenden relevanten Codeblock. Mit der Zeile `creds.peers["org1-peer1"].url` wird die Peer-URL aus Ihrer Berechtigungsnachweisdatei importiert.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  Wenn Sie die Peer-URL zur Methode `fabric_client.newPeer` hinzufügen, dann importieren Sie auch die relevanten TLS-Zertifikate aus Ihrem Verbindungsprofil, indem Sie das folgende Code-Snippet verwenden. Dasselbe Ergebnis erbringt die Hinzufügen der URL des Anordnungsservice. Sie müssen diese TLS-Zertifikate zur Authentifizierung der Kommunikation mit Ihrem Netz verwenden.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  Wenn die Bewilligungsrichtlinie vorgibt, dass Transaktionen von anderen Organisationen auf dem Kanal bewilligt werden müssen, dann müssen Sie die Peers dieser Organisationen mit den Methoden `newPeer()` und `channel.addPeer()` hinzufügen, wenn Sie das Netz einrichten. Die Organisationen müssen Ihnen die Liste der Peers senden, die sie zu einem bestimmten Kanal hinzugefügt haben. Die Endpunktinformationen und TLS-Zertifikate werden im Verbindungsprofil bereitgestellt. Das SDK sendet die Transaktion an alle Peers, die zu dem Kanal hinzugefügt wurden.

  Sie können auch weitere zu Ihrer Organisation gehörende Peers hinzufügen, die zum Kanal hinzugefügt wurden, um [die Anwendung hoch verfügbar zu machen](#ha-app). Dadurch erhält das SDK eine Failover-Möglichkeit für den Fall, dass einer Ihrer Peers ausfällt.

3. Nachdem Sie das Fabric-Netz eingerichtet und die Anwendungsidentität und das signCert-Zertifikat aus dem Registrierungsschritt importiert haben, definiert die Datei `invoke.js` den Vorschlag, den Sie ans Netz übergeben werden. Sie können die Funktion `initLedger` im `fabcar`-Chaincode verwenden, um bestimmte Anfangsdaten zum Ledger hinzuzufügen. Darüber hinaus können Sie den Codeblock bearbeiten, um weitere Funktionen aufzurufen, die im `fabcar`-Chaincode enthalten sind.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  Nachdem die Anforderung definiert wurde, können Sie einen [Transaktionsvorschlag ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") an die Peers auf dem Kanal senden. Nachdem der Vorschlag von den Peers zurückgegeben wurde, können Sie die [Transaktion ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") an den Anordnungsservice senden.

4. Sie können einen Ereignisservice hinzufügen, um den Transaktionsablauf effizienter zu gestalten. **Bearbeiten** Sie den folgenden Abschnitt:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Obwohl im Beispiel ein peerbasierter Ereignisservice verwendet wird, sollten Sie einen kanalbasierten Listener benutzen. Weitere Informationen erhalten Sie im Abschnitt zum [Verwalten von Transaktionen](#managing-transactions) und in der [Node-SDK-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Kanalbasierter Ereignisservice"){:new_window}.

5. Standardmäßig übergibt `invoke.js` die Transaktion als `user1`. Wenn Sie einen anderen Namen registriert haben, können Sie die Datei  `invoke.js` entsprechend bearbeiten.

Nachdem Sie die Bearbeitung der Datei abgeschlossen haben, geben Sie den Befehl `node invoke.js` aus, um die Transaktion an das Netz zu übergeben. Daraufhin erhalten Sie die folgende Nachricht, in der bestätigt wird, dass der Aufruf erfolgreich ausgeführt wurde.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

Mit dieser Nachricht wird angegeben, dass Ihre Anwendung den Chaincode erfolgreich aufrufen und Daten zum Ledger hinzufügen konnte.

### Chaincode abfragen
{: #query}

Nun können Sie `query.js` verwenden, um das Ledger zu lesen. Öffnen Sie die Datei `query.js` in einem Texteditor.

1. Fügen Sie `var creds = require('./creds.json')` oben in der Datei hinzu.
2. Aktualisieren Sie die Datei mit dem Kanalnamen und den Endpunktinformationen des Peers. Da mit dieser Operation lediglich Daten gelesen werden, die auf dem Peer gespeichert sind, müssen Sie keine Endpunktinformationen des Anordnungsservice hinzufügen. Bei `query.js` wird außerdem davon ausgegangen, dass der Vorschlag unter `user1` gesendet wird.
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

Nachdem Sie die Bearbeitung der Datei abgeschlossen haben, müssen Sie den Befehl `node query.js` absetzen. Daraufhin wird die Liste der Automobile im Ledger wie im folgenden Beispiel dargestellt.
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

Weitere Informationen zur fabcar-Anwendung und den von dieser Anwendung benutzten Funktionen finden Sie im vollständigen Lernprogramm zum [Schreiben Ihrer ersten Anwendung ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "Schreiben Ihrer ersten Anwendung"){:new_window} in der Hyperledger Fabric-Dokumentation.

## Verbindungsprofil mit SDK verwenden
{: #using-your-connection-profile-with-the-sdk}

Anstatt die Endpunktinformationen Ihres Netzes manuell zu importieren, können Sie über das SDK eine Verbindung zu Ihrem Netz herstellen. Hierzu verwenden Sie die Option **Verbindungsprofil** in der Anzeige **Übersicht** des Network Monitor. Auf diese Weise kann der Prozess der Verbindungsherstellung zu Ihrer Zertifizierungsstelle zum Zweck der Eintragung und Registrierung optimiert werden. Darüber hinaus wird so das Definieren Ihres Fabric-Netzes vor dem Übergeben einer Transaktion überflüssig. Das SDK kann die Peers und Anordnungsknoten auf dem relevanten Kanal direkt über das Verbindungsprofil suchen. Weitere Informationen zur Vorgehensweise bei der Verwendung eines Verbindungsprofils finden Sie in der [Node-SDK-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-network-config.html "Lernprogramm zum Verbindungsprofil"){:new_window}.

Im Folgenden wird die Datei `invoke.js` als Beispiel herangezogen, um die Effizienz der Verwendung eines Verbindungsprofils gegenüber der Verwendung manueller Endpunkte zu zeigen. Sie können eine neue Instanz des Fabric-Clients mit der Klasse `loadFromConfig` einrichten. Ersetzen Sie dabei `var fabric_client = new Fabric_Client();` durch den folgenden Code.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

Anstatt das Fabric-Netz einzurichten, indem Sie zuerst die Peers und Anordnungsknoten erstellen und diese dann zum Kanal hinzufügen, können Sie die folgende Zeile verwenden, um einen neuen Kanal zu erstellen.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

Das SDK fügt dann die Peers und den Anordnungsservice hinzu, die über das Verbindungsprofil auf dem Kanal definiert werden. Auf diese Weise kann das Schreiben von Anwendungen effizienter gestaltet und das Aktualisieren Ihrer Anwendungen vereinfacht werden, wenn Netzmitglieder zu Kanälen hinzugefügt, aus diesen entfernt oder wenn neue Kanäle gestartet werden sollen. Informationen zu den zusätzlich auszuführenden Schritten finden Sie im [Lernprogramm zum Verbindungsprofil ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-network-config.html "Lernprogramm zum Verbindungsprofil"){:new_window} in der Node-SDK-Dokumentation. Sie können diese [Version des fabcar-Lernprogramms ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} verwenden, das anstelle von manuellen Endpunktverbindungen mit dem Verbindungsprofil arbeitet.

Sie können Transaktionen zur Bewilligung an Peers senden, die sich außerhalb Ihrer Organisation befinden. Hierzu bearbeiten Sie das Verbindungsprofil. Das Verbindungsprofil enthält bereits die Endpunktinformationen und TLS-Zertifikate der Peers aus anderen Organisationen, die sich im {{site.data.keyword.blockchainfull}} Platform-Netz befinden. Fügen Sie im Abschnitt `channels` des Profils den Namen des Peers zum relevanten Kanal hinzu, um den Peer zum Kanal hinzuzufügen.

## Anwendungskonnektivität und -verfügbarkeit
{: #app-connectivity-availability}

Der [Transaktionsablauf![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Transaktionsablauf"){:new_window} von Hyperledger Fabric umfasst mehrere Komponenten, wobei die Clientanwendungen eine zentrale Rolle einnehmen. Das SDK übergibt Transaktionsvorschläge an die Peers, um die Bewilligung zu erhalten. Anschließend erfasst es die bewilligten Vorschläge, die an den Anordnungsservice gesendet werden müssen. Dieser sendet dann Blöcke von Transaktionen an die Peers, die den Ledgern der Kanäle hinzugefügt werden müssen. Entwickler von Produktionsanwendungen sollten darauf vorbereitet sein, dass ihre Interaktionen zwischen dem SDK und ihren Netzen mit der Zielsetzung der Effizienz und Verfügbarkeit verwaltet werden müssen.

### Transaktionen verwalten
{: #managing-transactions}

Anwendungsclients müssen sicherstellen, dass ihre Transaktionsvorschläge überprüft werden und dass die Vorschläge erfolgreich abgeschlossen werden. Ein Vorschlag kann aus verschiedenen Gründen verzögert werden oder verloren gehen, z. B. durch einen Netzausfall oder einen Komponentenfehler. Sie sollten die erforderlichen Vorbereitungen für die [Hochverfügbarkeit](#ha-app) Ihrer Anwendung treffen, um bei Auftreten eines Komponentenfehlers handlungsfähig zu bleiben. Des Weiteren können Sie in Ihrer Anwendung die [Zeitlimitwerte erhöhen](#set-timeout-in-sdk), um zu verhindern, dass für Vorschläge Zeitlimitüberschreitungen auftreten, bevor das Netz reagieren kann.

Wenn ein Chaincode nicht ausgeführt wird, dann wird er durch den ersten Transaktionsvorschlag gestartet, der an den Chaincode gesendet wird. Während der Chaincode gestartet wird, werden alle anderen Vorschläge mit einer Fehlernachricht zurückgewiesen, in der Sie darüber informiert werden, dass der Chaincode gerade gestartet wird. Dieser Vorgang unterscheidet sich von der Transaktionsinvalidierung. Wird ein Vorschlag zurückgewiesen, während der Chaincode gestartet wird, dann müssen die Anwendungsclients die zurückgewiesenen Vorschläge nach Abschluss des Chaincodestarts erneut senden. Anwendungsclients können eine Nachrichtenwarteschlange verwenden, um den Verlust von Transaktionsvorschlägen zu vermeiden.

Sie können einen kanalbasierten Ereignisservice verwenden, um Transaktionen zu überwachen und Nachrichtenwarteschlangen zu erstellen. Anstelle peerbasierter Services sollten kanalbasierte Listener benutzt werden, da diese die Fähigkeit zum Skalieren auf mehrere Kanäle und zur Unterscheidung zwischen dem Datenverkehr unterschiedlicher Kanäle haben. Die Klasse [channelEventHub ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} kann Listener auf Basis von Transaktions- und Blockereignissen sowie Chaincode-Ereignissen registrieren. In der Node-SDK-Dokumentation finden Sie Informationen zur Vorgehensweise beim Einrichten eines [kanalbasierten Ereignisservice ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Kanalbasierter Ereignisservice"){:new_window}.

### Netzverbindungen schließen

Wenn Sie Peers und Anordnungsknoten zu Ihrem SDK hinzufügen, bevor Sie einen Transaktionsvorschlag übergeben, dann wird eine grpc-Streaming-Verbindung zur Netzkomponente geöffnet. Beispiel:
```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
Mit diesem Befehl wird eine Verbindung zu `org1-peer1` geöffnet. Wenn Sie über eine kontinuierlich ausgeführte Anwendung verfügen, dann sollten Sie die Verbindungen schließen, wenn diese nicht benötigt werden. Benutzen Sie die Befehle `peer.close()` und `orderer.close()`, um Ressourcen freizugeben und Leistungseinbußen zu verhindern. Weitere Einzelheiten zu diesem Thema finden Sie in den Abschnitten zu den Klassen [peer](https://fabric-sdk-node.github.io/Peer.html#close__anchor) und [orderer](https://fabric-sdk-node.github.io/Orderer.html#close__anchor) in der Node-SDK-Dokumentation.

### Hoch verfügbare Anwendungen
{: #ha-app}

Als Verfahren zur Sicherstellung der Hochverfügbarkeit hat es sich bewährt, mindestens zwei Peers pro Organisation bereitzustellen, um so die Möglichkeit zum Failover zu schaffen. Sie müssen Ihre Anwendungen ebenfalls für die Hochverfügbarkeit anpassen. Installieren Sie den Chaincode auf beiden Peers und fügen Sie sie zu Ihren Kanälen hinzu. Anschließend müssen Sie an beide Peerendpunkte [Transaktionsvorschläge übergeben](#invoke), wenn Sie das Netz einrichten und eine Peerzielliste erstellen. Wenn Sie über ein Enterprise Plan-Netz verfügen, können Sie auch einen weiteren Anordnungsknoten zu Ihrem Netz hinzufügen. Wenn Sie Ihr [Verbindungsprofil](#using-your-connection-profile-with-the-sdk) verwenden, anstatt die Netzendpunkte manuell hinzuzufügen, dann müssen Sie sicherstellen, dass Ihr Profil auf dem aktuellsten Stand ist und dass die zusätzlichen Peers und Anordnungsknoten im Abschnitt `channels` des Profils dem relevanten Kanal hinzugefügt wurden. Das SDK fügt dann die Komponenten hinzu, die über das Verbindungsprofil mit dem Kanal verbunden sind.

## Gegenseitige TLS-Authentifizierung aktivieren
{: #mutual-tls}

Wenn Sie Enterprise Plan-Netze ausführen, die Fabric V1.1 entsprechen, dann haben Sie die Option, für Ihre Anwendungen die [gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) zu aktivieren](v10_dashboard.html#network-preferences). Wenn Sie MTLS aktivieren, müssen Sie die Anwendungen so aktualisieren, dass sie diese Funktion unterstützen. Andernfalls können Ihre Anwendungen nicht mit dem Netz kommunizieren.

Suchen Sie im Verbindungsprofil nach dem Abschnitt `certificateAuthorities`. Hier finden Sie die folgenden Attribute, die für die Eintragung und das Abrufen der Zertifikate für die Kommunikation mit dem Netz über MTLS erforderlich sind.

- `url`: URL für das Herstellen einer Verbindung zur Zertifizierungsstelle, die MTLS-Zertifikate ausstellen kann.
- `enrollId`: Eintragungs-ID für das Abrufen eines Zertifikats.
- `enrollSecret`: Geheimer Eintragungsschlüssel für das Abrufen eines Zertifikats.
- `x-tlsCAName`: Name der Zertifizierungsstelle für das Abrufen des Zertifikats, das der Anwendung die Kommunikation über MTLS (Mutual TLS; gegenseitige TLS-Authentifizierung) ermöglicht.

Weitere Informationen zum Aktualisieren der Anwendungen für die Unterstützung von MTLS finden Sie im Abschnitt zur [Vorgehensweise bei der Konfiguration der gegenseitigen TLS-Authentifizierung (Mutual TLS, MTLS)![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "Gegenseitige TLS-Authentifizierung"){:new_window}.

## (Optional) Netz mithilfe des SDK betreiben
{: #operate-sdk}

Sie können das SDK auch verwenden, um Ihr Blockchain-Netz zu betreiben. Im vorliegenden Lernprogramm wird erläutert, wie Sie das SDK verwenden können, um Ihre Peers zu einem Kanal hinzuzufügen, den Chaincode auf Ihren Peers zu installieren und den Chaincode auf Kanälen zu instanziieren. Diese Schritte sind optional, weil Sie diese Operationen auch über den Network Monitor oder die APIs in der [Swagger-Benutzerschnittstelle](howto/swagger_apis.html) ausführen können, wenn alle Peers auf der {{site.data.keyword.blockchainfull}} Platform ausgeführt werden.

Sie müssen das signCert-Zertifikat des Administrators auf {{site.data.keyword.blockchainfull}} Platform hochladen, um diese Schritte abzuschließen. Anweisungen zur Vorgehensweise beim Hochladen Ihres signCert-Zertifikats finden Sie am Ende des [Eintragungsabschnitts](#enroll-app-sdk).

### Kanal beitreten
{: #join-channel-sdk}

Nachdem Sie einen Kanal mit dem Network Monitor oder der API erstellt haben, können Sie das SDK verwenden, um Ihren Peer zu dem Kanal hinzuzufügen.

1. [Rufen Sie den Genesis-Block ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "Genesis-Block abrufen"){:new_window} des Kanals aus dem Anordnungsservice ab.
2. Übergeben Sie den Genesis-Block an die Methode für den [Kanalbeitritt ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window}, um Ihren Peer zum Kanal hinzuzufügen.

Um das `fabcar`-Beispiel zu verwenden, um einem Kanal beizutreten, müssen Sie die Datei `invoke.js` als Ausgangspunkt verwenden. Sie müssen diese Anforderung als Administrator und nicht über die Anwendung senden. Ersetzen Sie deshalb `user1` in der Methode `getUserContext` durch `admin`. Beginnen Sie an der Stelle, an der Sie die Chaincode-Aufrufanforderung bei `var request = {` definiert haben. Ersetzen Sie den Transaktionsablauf durch eine Kanalbeitrittsanforderung, die auf dem folgenden Code-Snippet aus der [Node-SDK-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "Gegenseitige TLS-Authentifizierung"){:new_window} stammt.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```
### Chaincode installieren
{: #install-cc-sdk}

Sie können die Methode zum [Installieren von Chaincode ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} aus der Klasse  [Fabric Client ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} zum Installieren des Chaincodes auf Ihrem Peer verwenden.

Zur Verwendung des `fabcar`-Beispiels für die Installation des `fabcar`-Chaincodes auf Ihrem Peer verwenden Sie die Datei `query.js` als Basis und bearbeiten sie. Sie müssen diese Anforderung als Administrator und nicht über die Anwendung senden. Ersetzen Sie deshalb `user1` in der Methode `getUserContext` durch `admin`. Ersetzen Sie das Transaktionsvorschlagsobjekt durch eine Chaincode-Anforderung für die Installation. Verwenden Sie dazu das folgende Beispiel:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

Senden Sie dieses Objekt nicht an die Zeile `return channel.queryByChaincode(request);`, die sich momentan in der Datei befindet, sondern an `return fabric_client.installChaincode(request);`.

### Chaincode instanziieren
{: #instantiate-cc-sdk}

Zum Instanziieren des Chaincodes müssen Sie einen [Instanziierungsvorschlag ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} an den Peer senden und anschließend eine [Transaktionsanforderung ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction) {:new_window} an den Anordnungsservice senden.

Um das `fabcar`-Beispiel zu verwenden, um Ihren Chaincode zu instanziieren, müssen Sie die Datei `invoke.js` als Ausgangspunkt verwenden. Sie müssen diese Anforderung als Administrator und nicht über die Anwendung senden. Ersetzen Sie deshalb `user1` in der Methode `getUserContext` durch `admin`. Ersetzen Sie das Transaktionsvorschlagsobjekt durch eine Chaincode-Anforderung für die Installation. Verwenden Sie dazu das folgende Beispiel:
```
var request = {
		targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
		channelNames: 'mychannel'
		};

```
{:codeblock}

Senden Sie diese Anforderung nicht an die Zeile `return channel.sendTransactionProposal(request);`, die sich momentan in der Datei befindet, sondern an `return channel.sendInstantiateProposal(request);`. Nach dem Senden der Instanziierungsanforderung an den Kanal müssen Sie den bewilligten Vorschlag als Transaktion an den Anordnungsservice senden. Dabei werden die gleichen Methoden wie beim Senden einer Transaktion verwendet, sodass die restliche Datei unverändert beibehalten werden kann. Möglicherweise möchten Sie im Instanziierungsvorschlag den [Zeitlimitwert erhöhen](#set-timeout-in-sdk). Andernfalls wird für die Anforderung eventuell das Zeitlimit überschritten, bevor die Plattform den Chaincode-Container starten kann.

## (Optional) Zeitlimitwerte in Fabric-SDKs einrichten
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

Möglicherweise müssen Sie jedoch die Standardzeitlimitwerte in Ihrer eigenen Anwendung ändern. Beispiel: Wenn Ihre Anwendung eine Transaktion mit einer Antwortzeit von mehr als 5000 ms (Standardzeitlimitwert für eine Ereignishubverbindung) aufruft, wird ein Fehler aufgrund einer fehlgeschlagenen Transaktion ausgegeben, da das Aufrufereignis nach 5000 ms beendet wird, bevor die Transaktion abgeschlossen ist. Sie können die Systemeigenschaft so festlegen, dass die Standardwerte der Clientanwendung überschrieben werden. Da die Standardwerte vor dem Festlegen der Systemeigenschaft initialisiert werden, ist diese möglicherweise nicht wirksam. Daher müssen Sie die Systemeigenschaft für das Zeitlimit in einem statischen Konstrukt in der Clientanwendung festlegen. Das folgende Beispiel veranschaulicht das Ändern des Zeitlimitwerts für Ereignishubverbindungen in 15000 ms im Fabric-Java-SDK. Der Dateipfad lautet `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Wenn Sie das Node-SDK verwenden, können Sie die Zeitlimitwerte direkt in der aufgerufenen Methode angeben. Als Beispiel können Sie die folgende Zeile verwenden, um den Zeitlimitwert für die [Instanziierung eines Chaincodes ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") auf fünf Minuten zu erhöhen.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## (Optional) CouchDB-Indizes verwenden
{: #couchdb-indices}

Wenn das Netz CouchDB verwendet und Sie die CouchDB-Indexierungsfunktion zur Verbesserung der CouchDB-Leistung nutzen möchten, müssen die Indizes zusammen mit dem Chaincode in einem Paket zusammengefasst werden.

Weitere Informationen zu CouchDB und zum Einrichten von Indizes finden Sie im Abschnitt zu [CouchDB als Statusdatenbank ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB als Statusdatenbank"){:new_window} in der Hyperledger Fabric-Dokumentation. Es steht auch ein Beispiel zur Verfügung, das einen Index mit Chaincode in diesem [Fabric-Lernprogramm ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window} verwendet.

<!--# Next steps-->

## Anwendungen hosten
{: #host-app}

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
3. Wechseln Sie in das Verzeichnis Ihrer Anwendung und führen Sie eine Push-Operation für Ihre Anwendung durch, indem Sie den folgenden Befehl eingeben. Dies kann einige Minuten je nach Größe Ihrer Anwendung dauern. Sie können die Protokolle aus {{site.data.keyword.Bluemix_notm}} an Ihrem Terminal anzeigen. Die Protokolle werden ausgeblendet, wenn die Anwendung erfolgreich gestartet wurde.
	```
	> cf push NAME_IHRER_APP
	```
	{:codeblock}
	Sie können Ihre Anwendungsprotokolle prüfen, indem Sie die folgenden Befehle eingeben:
	* `> cf logs NAME_IHRER_APP`
	* `> cf logs NAME_IHRER_APP --recent`

## Anwendung vom Netz trennen
{: #disconnect-app}

Führen Sie die folgenden Schritte aus, um die Verbindung zwischen Ihrer Anwendung und dem Blockchain-Netz unter {{site.data.keyword.cloud_notm}} zu entfernen.
1. Entfernen Sie die API-Endpunktinformationen aus der Konfigurationsdatei Ihrer Anwendung. Weitere Informationen finden Sie unter [Netz-API-Endpunkte der Anwendung hinzufügen](#api-endpoints).
2. Löschen Sie Ihren Chaincode-Container.
  1. Suchen Sie in der Anzeige "Kanal" des Network Monitor den Kanal, auf dem Ihr Chaincode installiert ist.
  2. Suchen Sie in der betreffenden Kanalanzeige den Chaincode, den Sie inaktivieren möchten.
  3. Klicken Sie auf die Schaltfläche **Löschen** und klicken Sie in der Anzeige zum Löschen von Chaincode auf **Übergeben**. Ihr Chaincode-Container wird entfernt.
	![Chaincode löschen](images/channel_chaincode.png "Chaincode löschen")

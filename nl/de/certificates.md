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


# Zertifikate in {{site.data.keyword.blockchainfull_notm}} Platform verwalten
{: #certificates}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform basiert auf Hyperledger Fabric und dient zur Erstellung genehmigter Blockchain-Netze. Die Teilnehmer werden als Mitglieder des Netzes bezeichnet und ihre Aktivitäten und Zugriffsaktionen auf die Netzressourcen werden kontinuierlich überprüft. Die Identität eines Teilnehmers wird in Form eines anerkannten digitalen x509-Zertifikats bereitgestellt. Die Verifizierung wird über eine zugrunde liegende PKI (PKI = Public Key Infrastructure) sowie über Signier- und Verifizierungsoperationen bereitgestellt, mit denen die Transaktionen und das Management innerhalb des Netzes gesichert werden.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform verwaltet die Mehrzahl der Zertifikatsoperationen, ohne dass der Benutzer seine Zertifikate bearbeiten muss. In bestimmten Fällen müssen Sie die Zertifikate, die zur Kommunikation mit dem Netz dienen, jedoch verwalten. Dies ist z. B. bei der Entwicklung von Anwendungen oder beim Erweitern Ihres Netzes durch ferne Peers erforderlich. Im Folgenden finden Sie einen kurzen Leitfaden zur Zertifizierungsstelle (CA = Certificate Authority) sowie zur zugrunde liegenden Zertifikatsinfrastruktur. Dieses Lernprogramm erleichtert Ihnen das Verständnis der Zertifikate, mit denen Sie arbeiten werden, sowie der Tasks, die ausgeführt werden müssen.

## Zertifizierungsstellen
{: #network-ca}

Zertifizierungsstellen (CAs; Certificate Authorities) ermöglichen die Identitätsfeststellung innerhalb des Netzes. Eine CA kann als öffentlich anerkannter Notar betrachtet werden, der als Vertrauensanker zwischen mehreren Parteien fungiert. Alle Entitäten innerhalb des Netzes erhalten ein Zertifikat, das von einer sog. Root-CA (Stammzertifizierungsstelle) signiert wurde und in dem die digitale Identität der betreffenden Entitäten enthalten ist. Dieses Zertifikat stellt die Vertrauensgrundlage für alle Signier- und Verifizierungsoperationen dar, die innerhalb des Netzes ausgeführt werden. Weitere Einzelheiten zur Verwendung von Zertifizierungsstellen zur Erstellung einer Identität finden Sie in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html).

Jedes Mitglied des Netzes verfügt über eine eigene CA, die ein von der Root-CA des Netzes ausgestelltes Zertifikat erhält. Diese Verbindung zur Root-CA bildet den Vertrauensanker für Ihre Organisation. Ihre Organisations-CA signiert Anforderungen für alle Entitäten, deren Eigner Ihre Organisation ist. Hierzu gehören z. B. der Administrator, Peers oder Anwendungen. Wenn Sie einen fernen Peer oder eine neue Anwendung zu Ihrem Netz hinzufügen wollen, dann müssen Sie die neue Identität bei Ihrer Zertifizierungsstelle (Registrierung) registrieren. Anschließend kann die Zertifizierungsstelle (CA) der neuen Entität die Zertifikate zur Verfügung stellen, die sie zur Interaktion mit dem Netz (Eintragung) benötigt.

### Registrierung mit Network Monitor durchführen
{: #ca-panel}

Mit dem {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor können Sie die Identitäten anzeigen, die bei Ihrer CA registriert wurden, und neue Identitäten hinzuzufügen. Navigieren Sie zur Anzeige "Zertifizierungsstelle" im Network Monitor. Diese Anzeige enthält alle Identitäten, die bei Ihrer CA registriert wurden (einschließlich der Administratoren, Peers und Clientanwendungen Ihrer Organisation). Zur Registrierung einer neuen Identität in Ihrer Organisation müssen Sie auf die Schaltfläche **Benutzer hinzufügen** oben in der Anzeige klicken. Daraufhin wird ein Popup-Fenster geöffnet, in dem die folgenden Felder angezeigt werden, die zur Registrierung einer neuen Identität benötigt werden.
  - **ID:** Dies ist der Name Ihrer neuen Identität, der auch als `Eintragungs-ID` bezeichnet wird. **Speichern Sie diesen Wert** zur Konfiguration eines fernen Peers oder zur Eintragung einer neuen Anwendung.
  - **Geheimer Schlüssel:** Dies ist das Kennwort für Ihre Identität, das auch als `geheimer Eintragungsschlüssel` bezeichnet wird. **Speichern Sie diesen Wert** zur Konfiguration eines fernen Peers oder zur Eintragung einer neuen Anwendung.  
  - **Typ:** Wählen Sie den Typ der Identität aus, die registriert werden soll (Peer oder Clientanwendung).
  - **Zugehörigkeit:** Dies ist die Zugehörigkeit innerhalb Ihrer Organisation, z. B. `org1`, zu der die Identität gehören wird.
  - **Maximale Anzahl der Eintragungen:** In diesem Feld können Sie die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit einer bestimmten Identität einschränken. Erfolgt in dem Feld keine Angabe, dann wird standardmäßig der Wert für eine unbeschränkte Anzahl von Eintragungen verwendet.

In dieser Anzeige können Sie eine neue Peeridentität registrieren, wenn Sie einen [fernen Peer](howto/remote_peer.html) bereitstellen. Alternativ hierzu können Sie einen Client registrieren, wenn Sie eine Anwendung entwickeln, mit der Transaktionen an Ihr Netz übergeben werden können. Im [Lernprogramm 'Anwendungen entwickeln'](v10_application.html) finden Sie weiterführende Informationen zur Verwendung der Fabric-SDKs auf der Plattform.

### Clientseitige Zertifikate generieren (Eintragung)
{: #enrollment}
Bevor Sie eine Verbindung zwischen einem Client eines anderen Anbieters und {{site.data.keyword.blockchainfull_notm}} Platform herstellen können, müssen Sie sich authentifizieren. Der Prozess zum Generieren der erforderlichen Zertifikate, Ihres privaten Schlüssels und Ihres öffentlichen Zertifikats (sog. Eintragungszertifikat oder signCert-Zertifikat) wird als Eintragung bezeichnet. Diese Zertifikate werden immer dann benötigt, wenn der Client mit dem Netz kommuniziert. Jeder Client, der Aufrufe an das Netz übergibt, muss die Nutzdaten mit einem privaten Schlüssel signieren und ein korrekt signiertes x509-Zertifikat beifügen.

Rufen Sie das [Lernprogramm 'Anwendungen entwickeln'](v10_application.html) auf, um weiterführende Informationen zur [Eintragung mit dem Fabric Node-SDK](v10_application.html) zu erhalten. Die Eintragung mit dem SDK generiert ein signCert-Zertifikat, einen öffentlichen Schlüssel, der zum Erstellen des signCert-Zertifikats verwendet wurde, und Ihren privaten Schlüssel.

Sie können Zertifikate auch mit dem Fabric-CA-Client über die [Befehlszeile](#enroll-register-caclient) generieren. Der Fabric-CA-Client gibt eine umfassende Gruppe von Zertifikaten in einem MSP-Ordner (MSP = Membership Service Provider) zurück. Dieser Ordner enthält das von der CA signierte Stammzertifikat, die Zwischenzertifikate, die Ihrer Zugehörigkeit zugeordnet sind, einen privaten Schlüssel sowie Ihr signCert-Zertifikat. Weitere Informationen zum Membership Service Provider (MSP) und zum Inhalt dieses Ordners finden Sie im folgenden Abschnitt [Membership Service Providers](#msp).

### Signierzertifikate in {{site.data.keyword.blockchainfull_notm}} Platform hochladen
{: #upload-certs}

Eine Anwendung benötigt ein gültiges signCert-Zertifikat nur zum Übergeben von Transaktionen an das Netz. Wenn ein Netz jedoch mithilfe eines Clients betrieben werden soll, indem auf den Peers Chaincode installiert wird oder indem Peers zu Kanälen hinzugefügt werden, dann muss der Client als Administrator erkennbar sein. Jede Komponente erkennt eine Gruppe von signCert-Zertifikaten, deren Eigner ein bestimmter Administrator ist. Soll Ihr Netz über einen Client betrieben werden, dann müssen Sie das signCert-Zertifikat hochladen und zur Liste der Administratorzertifikate hinzufügen. Dieser Arbeitsschritt kann auf der Plattform ausgeführt werden, indem Sie das signCert-Zertifikat auf der Registerkarte **Zertifikate** der [Anzeige "Übersicht"](v10_dashboard.html#members) des Network Monitor hochladen. Synchronisieren Sie dieses Zertifikat mit Ihren Peers, indem Sie die Schaltfläche für den Neustart verwenden, die nach dem Hochladen angezeigt wird. Anschließend wird Ihr Client als Administrator des Netzes erkannt.

<!----
When you add an organization to a channel, this will upload the organization signCert to the channel MSP. Synching the Sign Certs does something.
--->

## TLS-Zertifikate verwenden
{: #tls}

[Transport Layer Security ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) ist in das Hyperledger Fabric-Vertrauensmodell eingebettet. Alle Komponenten von {{site.data.keyword.blockchainfull_notm}} Platform verwenden TLS zur Authentifizierung und Kommunikation. Aus diesem Grund müssen Sie ein TLS-Zertifikat, das von der Plattform ausgegeben wird, an Ihre Aufrufe anfügen, um Ihre Kommunikation zu überprüfen und zu verschlüsseln. Die anderen Zertifikate, die in diesem Lernprogramm erläutert werden, dienen zum Schutz der Funktionalität, über die Transaktionen mit dem Netz ausgeführt werden und mit der das Netz verwaltet wird. TLS-Zertifikate werden verwendet, um Ihre Aufrufe an das Netz zu sichern.

TLS-Zertifikate werden von der Plattform öffentlich ausgegeben und sind für alle Netzkomponenten identisch. Sie können die TLS-Zertifikate über die folgenden Links herunterladen, wobei der zu verwendende Link von Ihrem Mitgliedschaftsplan und dem Cloudstandort abhängig ist. Die TLS-Zertifikate befinden sich außerdem in Ihrem [Berechtigungsnachweisprofil](v10_dashboard.html#connection-profile "Berechtigungsnachweisprofil"). Dieses Zertifikat kann an einer beliebigen Position gespeichert werden, sofern Sie über Ihre Anwendung oder die Befehlszeile darauf verweisen können.

- TLS-Stammzertifikat für den Starter Plan  
  - US: [us01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - UK: [uk01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sydney: [aus01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
- [TLS-Stammzertifikat für den Enterprise Plan ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

Alle {{site.data.keyword.blockchainfull_notm}} Platform-Netze verwenden serverseitiges TLS. Hierbei muss das Netz Ihre Clients authentifizieren. Enterprise Plan-Netze können auch die gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) aktivieren. In diesem Fall authentifizieren sich der Client und der Server gegenseitig, um Ihre Anwendungen weitergehend zu sichern. Clientseitige TLS-Zertifikate (zur gegenseitigen TLS-Authentifizierung) werden von der Client-CA ausgegeben und sind innerhalb Ihres Netzes eindeutig. Wenn Sie ein Enterprise Plan-Netz verwenden, dann sollten Sie die gegenseitige TLS-Authentifizierung aktivieren. Weitere Informationen zur gegenseitige TLS-Authentifizierung finden Sie in diesen [Anweisungen](v10_dashboard.html#mutual-tls "Anweisungen zur gegenseitigen TLS-Authentifizierung").

## Membership Service Providers (MSPs)
{: #msp}

{{site.data.keyword.blockchainfull_notm}} Platform-Komponenten verbrauchen Identitäten über Membership Service Providers (MSPs). MSPs ordnen die Zertifikate zu, die von den CAs mit den entsprechenden Netz- und Kanalrollen ausgegeben werden. Weitere Informationen zu MSPs finden Sie unter dem [Konzeptthema zur Mitgliedschaft in der Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Konzeptthema zur Mitgliedschaft in der Hyperledger Fabric-Dokumentation").

Die MSP-Ordner in Fabric verfügen über eine definierte Struktur. Wenn Sie die Eintragung mit dem Fabric-CA-Client durchführen, dann speichert der Client die Zertifikate in einem MSP-Ordner im lokalen Dateisystem in den folgenden Unterordnern:

- **cacerts:** Dieser Ordner enthält das Stammzertifikat der Root-CA Ihres Netzes.
- **intermediatecerts:** Hier befinden sich Zertifikate der temporären Zertifizierungsstellen (CAs) Ihres Netzes. Diese temporären Zertifizierungsstellen sind mit der Root-CA verknüpft und bilden eine Zertifikatskette. Jede Enterprise Plan-Organisation verfügt über zwei temporäre Zertifizierungsstellen, um so die Möglichkeit zum Failover (Funktionsübernahme) und die Hochverfügbarkeit sicherzustellen.
- **signcerts:** Dieser Ordner enthält Ihr öffentliches Signierzertifikat, das auch als signCert-Zertifikat oder Eintragungszertifikat bezeichnet wird. Dieses Zertifikat wird Ihren Aufrufen ans Netz (z. B. einem Chaincode-Aufruf) hinzugefügt, wenn Sie über die Befehlszeile auf das MSP-Verzeichnis verweisen oder ein Benutzerkontextobjekt mit den SDKs erstellen. Sie können dieses Zertifikat auf die Plattform hochladen, wenn Sie ein Netz über das SDK oder die Befehlszeile betreiben wollen.
- **keystore:** Dieser Ordner enthält Ihren privaten Schlüssel. Dieser Schlüssel wird zum Signieren Ihrer Aufrufe an das Netz verwendet, wenn Sie über die Befehlszeile auf Ihr MSP-Verzeichnis verweisen oder ein Benutzerkontextobjekt mit den SDKs erstellen. Bewahren Sie diesen Schlüssel an einem sicheren Ort auf, um Ihr Netz und Ihre Daten zu schützen.

Sie können auch einen MSP-Ordner erstellen, auf den vom Fabric-CA-Client mit dem Network Monitor und den Swagger-APIs verwiesen werden kann.

- **cacerts** und **intermediatecerts**: Sie können diese Zertifikate mithilfe der [Swagger-APIs](howto/swagger_apis.html) abrufen, indem Sie eine Get-Anforderung an die MSP-API absetzen.
- **signcerts** und **keystore**: Sie können diese Zertifikate generieren, indem Sie in der Anzeige "Zertifizierungsstelle" auf die Schaltfläche **Zertifikate generieren** klicken. Daraufhin wird ein Popup-Fenster geöffnet, in dem zwei Zertifikate aufgelistet werden. Kopieren Sie das **Zertifikat** in "signcerts" und den **privaten Schlüssel** in "keystore". Bewahren Sie diese Zertifikate an einem sicheren Ort auf, weil sie nicht auf der Plattform gespeichert werden.

Zahlreiche Fabric-Komponenten enthalten zusätzliche Informationen in ihrem MSP-Ordner. Wenn Sie z. B. mit einem fernen Peer arbeiten, dann werden möglicherweise die folgenden Ordner angezeigt:

- **admincerts:** Dieser Ordner enthält die Liste der Administratoren für diese Organisation oder Komponente. Sie müssen Ihr Signierzertifikat in diesen Ordner hochladen, wenn Sie mit einem fernen Peer über die Befehlszeile oder mithilfe der SDKs arbeiten.
- **tls:** In diesem Ordner werden TLS-Zertifikate gespeichert, die zur Kommunikation mit anderen Netzkomponenten verwendet werden.

Weitere Informationen zur Struktur der MSPs finden Sie im Abschnitt zur [Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Mitgliedschaft") und im Abschnitt zu [Membership Service Providers ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") in der Hyperledger Fabric-Dokumentation.

## Eintragung und Registrierung mithilfe des Fabric-CA-Clients durchführen
{: #enroll-register-caclient}

Zum Generieren von Zertifikaten und zum Registrieren einer neuen Identität bei der Zertifizierungsstelle können Sie auch den Fabric-CA-Client verwenden. Die folgenden Anweisungen dienen zum Generieren von Zertifikaten anhand Ihrer Administratoridentität und zur anschließenden Verwendung dieser Zertifikate zur Registrierung eines neuen Clients. Durch die Eintragung mithilfe des Fabric-CA-Clients werden die Zertifikate generiert, die im [Abschnitt zu Membership Services Providers](#msp) beschrieben werden.

### Eintragung mithilfe des Fabric-CA-Clients durchführen
{: #enroll-app-caclient}

1. Laden Sie die [fabric-ca-Binärkomponenten](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) auf Ihr lokales System herunter und extrahieren Sie sie. Verschieben Sie sie in einen Ordner wie z. B. `$HOME/fabric-ca-platform/`. Wechseln Sie in das Verzeichnis, in das Sie die Clientbinärkomponenten verschoben haben, sodass Sie in Ihren Befehlen direkt darauf verweisen können.
    ```
    cd $HOME/fabric-ca-platform/
    ```

2.  Legen Sie den Pfad fest, in dem der Client Ihre Schlüssel erstellen kann. Vergewissern Sie sich, dass das Konfigurationsmaterial entfernt wurde, das beim letzten Versuch erstellt wurde. Wenn Sie den Befehl `enroll` zuvor noch nicht ausgeführt haben, dann sind der Ordner `msp` und die Datei `.yaml` nicht vorhanden.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```

3. Laden Sie die TLS-Zertifikate von {{site.data.keyword.cloud_notm}} herunter, die dem verwendeten Serviceplan, Standort und Cluster entsprechen.
  - TLS-Stammzertifikat für den Starter Plan  
    - US: [us01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [TLS-Stammzertifikat für den Enterprise Plan ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Speichern Sie den Inhalt in einem Ordner, z. B. ``$HOME/tls``. Dieser Schritt ermöglicht das Verschlüsseln der Daten während der Übertragung.

4. Öffnen Sie die JSON-Datei mit dem **Verbindungsprofil** in der Anzeige "Übersicht" im Network Monitor und suchen Sie die folgenden Variablen:
  * URL für CA: `url` unter `certificateAuthorities`
  * Administrator-ID: ``enrollId``
  * Administratorkennwort: ``enrollSecret``
  * CA-Name: ``caName``

5. Unter Verwendung des Fabric-CA-Clients kann ein `enroll`-Aufruf an die Zertifizierungsstelle gesendet werden. Hierzu übergeben Sie den Pfad für die TLS-Zertifikate sowie die vier oben angegebenen Zeichenfolgen mit dem folgenden Befehl:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Sie müssen diesen Befehl in dem Verzeichnis ausführen, in das der Fabric-CA-Client verschoben wird. Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

6. Suchen Sie das Administratorzertifikat in `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. Sie können das Administratorzertifikat dann vom Network Monitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglieder"](v10_dashboard.html#members) im Network Monitor.

  Das CA-Stammzertifikat und der private Schlüssel für den Administrator befinden sich auch in folgenden Verzeichnissen:
  * CA-Stammzertifikat: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * Privater Schlüssel des Administrators: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Ein Beispiel zur Eintragung mithilfe des Fabric-CA-Clients und zur Verwendung der generierten Zertifikate zum Betrieb der Netzkomponenten finden Sie in den Anweisungen zum [Betrieb eines fernen Peers](howto/remote_peer_operate_icp.html#remote-peer-cli-operate).

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
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

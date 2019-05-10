---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Zertifikate in {{site.data.keyword.blockchainfull_notm}} Platform verwalten
{: #managing-certificates}

{{site.data.keyword.blockchainfull}} Platform basiert auf Hyperledger Fabric und dient zur Erstellung genehmigter Blockchain-Netze. Die Teilnehmer werden als Mitglieder des Netzes bezeichnet und ihre Aktivitäten und Zugriffsaktionen auf die Netzressourcen werden kontinuierlich überprüft. Die Identität eines Teilnehmers wird in Form eines anerkannten digitalen x509-Zertifikats bereitgestellt. Die Verifizierung wird über eine zugrunde liegende PKI (PKI = Public Key Infrastructure) sowie über Signier- und Verifizierungsoperationen bereitgestellt, mit denen die Transaktionen und das Management innerhalb des Netzes gesichert werden.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform verwaltet die Mehrzahl der Zertifikatsoperationen, ohne dass der Benutzer seine Zertifikate bearbeiten muss. In bestimmten Fällen müssen Sie die Zertifikate, die zur Kommunikation mit dem Netz dienen, jedoch verwalten. Dies ist z. B. bei der Entwicklung von Anwendungen oder beim Erweitern Ihres Netzes durch ferne Peers erforderlich. Im Folgenden finden Sie einen kurzen Leitfaden zur Zertifizierungsstelle (CA = Certificate Authority) sowie zur zugrunde liegenden Zertifikatsinfrastruktur. Dieses Lernprogramm erleichtert Ihnen das Verständnis der Zertifikate, mit denen Sie arbeiten werden, sowie der Tasks, die ausgeführt werden müssen.

## Zertifizierungsstellen
{: #managing-certificates-network-ca}

Zertifizierungsstellen (CAs; Certificate Authorities) ermöglichen die Identitätsfeststellung innerhalb des Netzes. Eine Zertifizierungsstelle kann als öffentlich anerkannter Notar betrachtet werden, der als Vertrauensanker zwischen mehreren Parteien fungiert. Alle Entitäten innerhalb des Netzes erhalten ein Zertifikat, das von einer so genannten Stammzertifizierungsstelle (Root-CA) signiert wurde und in dem die digitale Identität der betreffenden Entitäten enthalten ist. Dieses Zertifikat stellt die Vertrauensgrundlage für alle Signier- und Verifizierungsoperationen dar, die innerhalb des Netzes ausgeführt werden. Weitere Einzelheiten zur Verwendung von Zertifizierungsstellen zur Erstellung einer Identität finden Sie in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/identity/identity.html).

Jedes Mitglied des Netzes verfügt über eine eigene Zertifizierungsstelle. Die Zertifizierungsstelle Ihrer Organisations signiert Anforderungen für alle Entitäten und Komponenten, deren Eigner Sie sind. Hierzu gehören z. B. der Administrator, Peers oder Anwendungen. Wenn Sie einen fernen Peer oder eine neue Anwendung zu Ihrem Netz hinzufügen wollen, dann müssen Sie die neue Identität bei Ihrer Zertifizierungsstelle (Registrierung) registrieren. Anschließend kann die Zertifizierungsstelle der neuen Entität die Zertifikate zur Verfügung stellen, die die Entität zur Interaktion mit dem Netz (Eintragung) benötigt.

### Registrierung mit Network Monitor durchführen
{: #managing-certificates-ca-panel}

Mit dem {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor können Sie die Identitäten anzeigen, die bei Ihrer Zertifizierungsstelle registriert wurden, und neue Identitäten hinzuzufügen. Navigieren Sie zur Anzeige "Zertifizierungsstelle" im Network Monitor. Diese Anzeige enthält alle Identitäten, die bei Ihrer Zertifizierungsstelle registriert wurden (einschließlich der Administratoren, Peers und Clientanwendungen Ihrer Organisation). Zur Registrierung einer neuen Identität in Ihrer Organisation müssen Sie in der Anzeige auf die Schaltfläche **Benutzer hinzufügen**. Daraufhin wird ein Popup-Fenster geöffnet, in dem die folgenden Felder angezeigt werden, die zur Registrierung einer neuen Identität benötigt werden.
  - **Eintragungs-ID:** Dies ist der Name Ihrer neuen Identität. `` **Speichern Sie diesen Wert.** Sie benötigen ihn, wenn Sie einen fernen Peer konfigurieren oder eine neue Anwendung eintragen.
  - **Geheimer Eintragungsschlüssel:** Dies ist das Kennwort für Ihre Identität.`` **Speichern Sie diesen Wert.** Sie benötigen ihn, wenn Sie einen fernen Peer konfigurieren oder eine neue Anwendung eintragen.
  - **Typ:** Wählen Sie den Typ der Identität aus, die registriert werden soll (Peer oder Clientanwendung).
  - **Zugehörigkeit:** Dies ist die Zugehörigkeit innerhalb Ihrer Organisation, z. B. `org1`, zu der die Identität gehören wird.
  - **Maximale Anzahl der Eintragungen:** In diesem Feld können Sie die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit einer bestimmten Identität einschränken. Erfolgt in dem Feld keine Angabe, dann wird standardmäßig der Wert für eine unbeschränkte Anzahl von Eintragungen verwendet.

In dieser Anzeige können Sie eine neue Peeridentität registrieren, wenn Sie einen [fernen Peer](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) bereitstellen. Alternativ hierzu können Sie einen Client registrieren, wenn Sie eine Anwendung entwickeln, mit der Transaktionen an Ihr Netz übergeben werden können. Im Lernprogramm [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html#dev-app) finden Sie weiterführende Informationen zur Verwendung der Fabric-SDKs auf der Plattform.

### Clientseitige Zertifikate generieren (Eintragung)
{: #managing-certificates-enrollment}
Bevor Sie eine Verbindung zwischen einem Client eines anderen Anbieters und {{site.data.keyword.blockchainfull_notm}} Platform herstellen können, müssen Sie sich authentifizieren. Der Prozess zum Generieren der erforderlichen Zertifikate, Ihres privaten Schlüssels und Ihres öffentlichen Zertifikats (sog. Eintragungszertifikat oder signCert-Zertifikat) wird als Eintragung bezeichnet. Diese Zertifikate werden immer dann benötigt, wenn der Client mit dem Netz kommuniziert. Jeder Client, der Aufrufe an das Netz übergibt, muss die Nutzdaten mit einem privaten Schlüssel signieren und ein korrekt signiertes x509-Zertifikat beifügen.

Rufen Sie das [Lernprogramm 'Anwendungen entwickeln'](/docs/services/blockchain/v10_application.html#dev-app) auf, um weiterführende Informationen zur [Eintragung mit dem Fabric Node-SDK](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk) zu erhalten. Die Eintragung mit dem SDK generiert drei separate Elemente: einen privaten Schlüssel, ein signCert-Zertifikat und einen öffentlichen Schlüssel, der zum Erstellen des signCert-Zertifikats verwendet wurde.

Sie können Zertifikate auch mit dem [Fabric-CA-Client](/docs/services/blockchain/certificates.html#managing-certificates-enroll-register-caclient) über die Befehlszeile generieren. Der Fabric-CA-Client gibt eine umfassende Gruppe von Zertifikaten in einem MSP-Ordner (MSP = Membership Service Provider) zurück. Dieser Ordner enthält das von der Zertifizierungsstelle signierte Stammzertifikat, die Zwischenzertifikate, einen privaten Schlüssel sowie Ihr signCert-Zertifikat. Weitere Informationen zu MSP und zum Inhalt des MSP-Ordners finden Sie unter [Membership Service Providers (MSPs)](/docs/services/blockchain/certificates.html#managing-certificates-msp).

Sie können Zertifikate nur mit Identitäten generieren, die bei Ihrer Zertifizierungsstelle registriert sind, indem Sie den Namen und den geheimen Schlüssel der jeweiligen Identität verwenden. Die Identität namens **admin** ist standardmäßig bereits bei Ihrer Zertifizierungsstelle registriert und wird in der Anzeige "Zertifizierungsstelle" aufgelistet. Den geheimen Schlüssel der Identität "admin" können Sie in Ihrem Verbindungsprofil ermitteln, indem Sie in der Anzeige "Übersicht" vom Network Monitor auf die Schaltfläche **Verbindungsprofil** klicken. Außerdem können Sie eine neue Identität registrieren, indem Sie in der Anzeige "Zertifizierungsstelle" vom Network Monitor auf die Schaltfläche [Benutzer hinzufügen](/docs/services/blockchain/certificates.html#managing-certificates-ca-panel) klicken, und anschließend Zertifikate mit dem Namen und dem geheimen Schlüssel der neuen Identität generieren.

**Hinweis:** Falls Sie die obigen Anweisungen zum Generieren von Zertifikaten mit Fabric Node SDK oder dem Fabric-CA-Client verwenden, tragen Sie als erstes die Identität "admin" ein. Anschließend verwenden Sie diese Zertifikate, um eine neue Clientidentität bei Ihrer Zertifizierungsstelle zu registrieren. Falls Sie die SDK-Anweisungen im Abschnitt [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html#dev-app) verwenden, tragen Sie sich erneut unter Verwendung der Clientidentität ein. Anschließend können Sie diese Zertifikate verwenden, um Transaktionen an das Netz zu übergeben. <!---You can an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

### Zertifikate mit Network Monitor generieren
{: #managing-certificates-certs-panel}

Mit Network Monitor können Sie unter Verwendung der Administratoridentität Zertifikate generieren und diese Zertifikate anschließend direkt an das SDK übergeben. Klicken Sie auf die Schaltfläche **Zertifikat generieren** neben Ihrer Administratoridentität, um ein neues signCert-Zertifikat sowie einen privaten Schlüssel von Ihrer Zertifizierungsstelle abzurufen. Das Feld **Zertifikat** enthält das signCert-Zertifikat, direkt über **Privater Schlüssel**. Sie können auf das Kopiersymbol klicken, das sich am Ende aller Felder befindet, um den Wert zu kopieren. Anschließend müssen Sie diese Zertifikate an einer Position speichern, von der aus Sie sie danach in Ihre Anwendung importieren können. Weitere Informationen enthält das Lernprogramm [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel). **Hinweis:** Diese Zertifikate werden von {{site.data.keyword.blockchainfull_notm}} Platform nicht gespeichert. Sie müssen sie an einem sicheren Ort aufbewahren und speichern.

### Signierzertifikate in {{site.data.keyword.blockchainfull_notm}} Platform hochladen
{: #managing-certificates-upload-certs}

Eine Anwendung benötigt nur ein gültiges signCert-Zertifikat zum Übergeben von Transaktionen an das Netz. Wenn ein Netz jedoch mithilfe eines Clients betrieben werden soll, indem auf den Peers Chaincode installiert wird oder indem Peers zu Kanälen hinzugefügt werden, dann muss der Client als Administrator erkennbar sein. Jede Komponente erkennt eine Gruppe von signCert-Zertifikaten, deren Eigner ein bestimmter Administrator ist. Soll Ihr Netz über einen Client betrieben werden, dann müssen Sie das signCert-Zertifikat hochladen und zur Liste der Administratorzertifikate hinzufügen. Dieser Arbeitsschritt kann auf der Plattform ausgeführt werden, indem Sie das signCert-Zertifikat auf der Registerkarte **Zertifikate** der [Anzeige "Übersicht"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) des Network Monitor hochladen. Synchronisieren Sie dieses Zertifikat mit Ihren Peers, indem Sie die Schaltfläche für den Neustart verwenden, die nach dem Hochladen angezeigt wird. Danach kann Ihr Client das Netz betreiben. Sie können Ihr signCert-Zertifikat auch mithilfe der [Swagger-API](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) hochladen, um ein Administratorzertifikat hinzuzufügen.

Kanäle erkennen ebenfalls eine Gruppe von Administratorzertifikaten unter den Identitäten, die zum Betreiben des Kanals berechtigt sind, was die Möglichkeit einschließt, einen Chaincode auf dem Kanal zu instanziieren. Falls Sie bei einem fernen Client ein neues signCert-Zertifikat verwenden, müssen Sie das Zertifikat für den Kanal synchronisieren, bevor Sie einen Chaincode instanziieren können. Führen Sie die folgenden Schritte in Network Monitor aus, um das Zertifikat zum Kanal hinzuzufügen:

1. Navigieren Sie zu der Registerkarte **Zertifikate** der Anzeige "Mitglieder". Klicken Sie auf die Schaltfläche **Zertifikat hinzufügen**, um das signCert-Zertifikat in die Plattform hochzuladen.
2. Navigieren Sie zur Anzeige "Kanäle" und suchen Sie nach dem Namen des entsprechenden Kanals.
3. Klicken Sie in der Dropdown-Liste unter der Überschrift **Aktionen** auf **Zertifikat synchronisieren**, um das neue Zertifikat zur Liste der Administratorzertifikate für den Kanal hinzuzufügen.

**Hinweis:** Zertifikate, die unter Verwendung der Administratoridentität (admin) Ihrer Organisation generiert werden, werden nicht automatisch als Administratorzertifikate betrachtet. Ein signCert-Zertifikat und ein privater Schlüssel, die durch Auswahl der Schaltfläche **Zertifikat generieren** erstellt werden, ermöglichen Ihrem SDK nicht den Betrieb des Netzes. Sie wurden durch den Fabric-CA-Client, das SDK oder von Network Monitor generiert und weisen keine Verbindung zu der bereits vorhandenen Liste von Administratorzertifikaten auf, die von den Netzkomponenten erkannt werden. Sie müssen das signCert-Zertifikat in Network Monitor hochladen, bevor Ihr SDK das Netz betreiben kann.

### Ablauf von Zertifikaten
{: #managing-certificates-expiration}

Zertifikate, die von Zertifizierungsstellen für {{site.data.keyword.blockchainfull_notm}} Platform generiert werden, laufen nach einem oder drei Jahren ab. Der Ablaufzeitraum ist derselbe für Zertifikate, die mit den Fabric-SDKs, dem Fabric-CA-Client oder mit dem [Network Monitor](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel) generiert werden. Wenn die Zertifikate ablaufen, können Ihre Anwendungen nicht mehr mit Ihrem Netz interagieren. Sie müssen sich erneut registrieren, um neue Zertifikate zu generieren. Wenn Sie das Eintragungslimit für einen Benutzer erreicht haben, können Sie einen neuen Benutzer registrieren und dann eintragen. Sie müssen die neuen Zertifikate auch auf die Plattform hochladen, wenn Sie die alten Zertifikate zum Betrieb Ihres Netzes verwendet haben.

Sie können das Ablaufdatum für Ihre Zertifikate über die Befehlszeile überprüfen. Führen Sie den folgenden Befehl aus, um Ihre Zertifikate in einer vom Benutzer lesbaren Form anzuzeigen:
```
openssl x509 -in <Zertifikatsdatei> -text
```
{:codeblock}

Das Clientzertifikat sieht ähnlich wie im folgenden Beispiel aus:

```
Certificate:
    Data:
        Version: 3 (0x2)
    Serial Number:
        20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18
Signature Algorithm: ecdsa-with-SHA256
    Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA
    Validity
        Not Before: Nov 28 19:18:00 2018 GMT
        Not After : Nov 28 19:23:00 2019 GMT
    Subject: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin
    ...
    ...
```

Das Ablaufdatum finden Sie im Abschnitt **Validity** nach `Not After:`. Im vorliegenden Beispiel läuft das Zertifikat am 28. November 2019 ab.

## TLS-Zertifikate verwenden
{: #managing-certificates-tls}

[Transport Layer Security ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) ist in das Hyperledger Fabric-Vertrauensmodell eingebettet. Alle Komponenten von {{site.data.keyword.blockchainfull_notm}} Platform verwenden TLS zur Authentifizierung und Kommunikation. Aus diesem Grund müssen Sie ein TLS-Zertifikat, das von der Plattform ausgegeben wird, an Ihre Aufrufe anfügen, um Ihre Kommunikation zu überprüfen und zu verschlüsseln. Die anderen Zertifikate, die in diesem Lernprogramm erläutert werden, dienen zum Schutz der Funktionalität, über die Transaktionen mit dem Netz ausgeführt werden und mit der das Netz verwaltet wird. TLS-Zertifikate werden verwendet, um Ihre Aufrufe an das Netz zu sichern.

TLS-Zertifikate werden von der Plattform öffentlich ausgegeben und sind für alle Netzkomponenten identisch. Sie können die TLS-Zertifikate über die folgenden Links herunterladen, wobei der zu verwendende Link von Ihrem Mitgliedschaftsplan und dem Cloudstandort abhängig ist. Die TLS-Zertifikate befinden sich außerdem in Ihrem [Berechtigungsnachweisprofil](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-connection-profile). Dieses Zertifikat kann an einer beliebigen Position gespeichert werden, sofern Sie über Ihre Anwendung oder die Befehlszeile darauf verweisen können.

- TLS-Zertifikat für den Starter Plan
  - US: [us01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
  [us03.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
  [us05.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
  [us07.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
  - UK: [uk01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
  [uk03.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
  - Sydney: [aus01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")
- [TLS-Zertifikat für den Enterprise Plan ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

Alle {{site.data.keyword.blockchainfull_notm}} Platform-Netze verwenden serverseitiges TLS. Hierbei muss das Netz Ihre Clients authentifizieren. Enterprise Plan-Netze können auch die gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) aktivieren. In diesem Fall authentifizieren sich der Client und der Server gegenseitig, um Ihre Anwendungen weitergehend zu sichern. Clientseitige TLS-Zertifikate (zur gegenseitigen TLS-Authentifizierung) werden von der Client-CA ausgegeben und sind innerhalb Ihres Netzes eindeutig. Wenn Sie ein Enterprise Plan-Netz verwenden, dann sollten Sie die gegenseitige TLS-Authentifizierung aktivieren. Weitere Informationen zur gegenseitige TLS-Authentifizierung finden Sie in diesen [Anweisungen zur gegenseitigen TLS-Authentifizierung](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-mutual-tls).

### Domänennamen aus TLS-Zertifikaten abrufen
{: #managing-certificates-retrieve-domain}

Wenn Sie mit einer Hyperledger Fabric-Komponente kommunizieren, die sich außerhalb von {{site.data.keyword.blockchainfull_notm}} Platform befindet, muss Ihr Aufruf unter Verwendung des korrekten Domänennamens übergeben werden. Wenn ein Aufruf an die IP-Adresse einer Komponente übergeben wird, ohne dass der Domänenname der Komponente aufgelöst wird, wird der Aufruf zurückgewiesen und mit einem Fehler zurückgegeben.

Die vollständige URL und den Domänennamen von Komponenten, die auf der Plattform gehostet werden, finden Sie in Ihrem Verbindungsprofil. Das folgende Beispiel zeigt die vollständige URL eines Peers, die mit dem Domänennamen des Peers verknüpft ist (`us01.blockchain.ibm.com`).
```
grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us01.blockchain.ibm.com:31002"
```

Den Domänennamen einer Komponente können Sie auch in ihrem TLS-Zertifikat ermitteln.

1. Laden Sie eines der TLS-Stammzertifikate aus der obigen Liste herunter und speichern Sie es auf Ihrer lokalen Maschine.
2. Führen Sie den folgenden Befehl in demjenigen Verzeichnis aus, in dem sich auch das TLS-Stammzertifikat befindet. Dieser Befehl kann das Zertifikat in einem lesbaren Format in der Befehlszeile ausgeben. Anschließend können Sie in der Befehlszeile nach wichtigen Informationen wie beispielsweise dem Domänennamen suchen.
  ```
  openssl x509 -in <Zertifikatsdatei> -text
  ```
  {:codeblock}

Wenn Sie beispielsweise das erste aufgelistete Zertifikat herunterladen und den Befehl `openssl x509 -in us01.blockchain.ibm.com.cert -text` ausgeben, wird im Rahmen der Ausgabe der folgende Code angezeigt.

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            05:5c:42:fb:90:b9:cb:3d:60:7c:e4:ec:7f:7f:8e:38
    Signature Algorithm: ecdsa-with-SHA256
        Issuer: C=US, O=DigiCert Inc, CN=DigiCert ECC Secure Server CA
        Validity
            Not Before: Jun 11 00:00:00 2018 GMT
            Not After : Jun 19 12:00:00 2019 GMT
        Subject: C=US, ST=New York, L=Armonk, O=International Business Machines Corporation, CN=*.us01.blockchain.ibm.com
    ...
    ...
```

Der Domänenname ist in der mit "Subject" beginnenden Zeile als `CN=*.us01.blockchain.ibm.com` aufgeführt. Sie können auch nach alternativen Domänennamen suchen, die im Zertifikat weiter unten aufgeführt sind.

Das Abrufen eines Domänennamens einer Komponente aus TLS-Zertifikaten kann nützlich sein, wenn Sie mit einem fernen Peer oder einer Fabric-Komponente kommunizieren, die sich außerhalb von {{site.data.keyword.blockchainfull_notm}} Platform befindet. Anschließend können Sie bei Verwendung der SDKs den Domänennamen zu einer SSL-Überschreibung hinzufügen oder den Domänennamen und die entsprechende IP-Adresse zu Ihrer Datei `etc.hosts` hinzufügen.

## Membership Service Providers (MSPs)
{: #managing-certificates-msp}

{{site.data.keyword.blockchainfull_notm}} Platform-Komponenten verbrauchen Identitäten über Membership Service Providers (MSPs). MSPs ordnen die Zertifikate zu, die von den Zertifizierungsstellen mit den entsprechenden Netz- und Kanalrollen ausgegeben werden. Weitere Informationen zu MSPs finden Sie unter dem [Konzeptthema zur Mitgliedschaft in der Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html "Konzeptthema 'Membership' in der Hyperledger Fabric-Dokumentation").

Die MSP-Ordner in Fabric verfügen über eine definierte Struktur. Wenn Sie die Eintragung mit dem Fabric-CA-Client durchführen, dann speichert der Client die Zertifikate in einem MSP-Ordner im lokalen Dateisystem in den folgenden Unterordnern:

- **cacerts:** Dieser Ordner enthält das Stammzertifikat der Stammzertifizierungsstelle Ihres Netzes.
- **intermediatecerts:** Hier befinden sich Zertifikate der Zwischenzertifizierungsstellen Ihres Netzes. Diese Zwischenzertifizierungsstellen sind mit der Stammzertifizierungsstelle verknüpft und bilden eine Zertifikatskette. Jede Enterprise Plan-Organisation verfügt über zwei Zwischenzertifizierungsstellen, um so die Möglichkeit zum Failover (Funktionsübernahme) und die Hochverfügbarkeit sicherzustellen.
- **signcerts:** Dieser Ordner enthält Ihr öffentliches Signierzertifikat, das auch als signCert-Zertifikat oder Eintragungszertifikat bezeichnet wird. Dieses Zertifikat wird Ihren Aufrufen ans Netz (z. B. einem Chaincode-Aufruf) hinzugefügt, wenn Sie über die Befehlszeile auf das MSP-Verzeichnis verweisen oder ein Benutzerkontextobjekt mit den SDKs erstellen. Sie können dieses Zertifikat auf die Plattform hochladen, wenn Sie ein Netz über das SDK oder die Befehlszeile betreiben wollen.
- **keystore:** Dieser Ordner enthält Ihren privaten Schlüssel. Dieser Schlüssel wird zum Signieren Ihrer Aufrufe an das Netz verwendet, wenn Sie über die Befehlszeile auf Ihr MSP-Verzeichnis verweisen oder ein Benutzerkontextobjekt mit den SDKs erstellen. Bewahren Sie diesen Schlüssel an einem sicheren Ort auf, um Ihr Netz und Ihre Daten zu schützen.

Sie können auch einen MSP-Ordner erstellen, auf den vom Fabric-CA-Client mit dem Network Monitor und den Swagger-APIs verwiesen werden kann.

- **cacerts** und **intermediatecerts**: Sie können diese Zertifikate mithilfe der [Swagger-APIs](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) abrufen, indem Sie eine Get-Anforderung an die MSP-API absetzen.
- **signcerts** und **keystore**: Sie können diese Zertifikate generieren, indem Sie in der Anzeige "Zertifizierungsstelle" auf die Schaltfläche **Zertifikate generieren** klicken. Daraufhin wird ein Popup-Fenster geöffnet, in dem zwei Zertifikate aufgelistet werden. Kopieren Sie das **Zertifikat** in "signcerts" und den **privaten Schlüssel** in "keystore". Bewahren Sie diese Zertifikate an einem sicheren Ort auf, weil sie nicht auf der Plattform gespeichert werden.

Zahlreiche Fabric-Komponenten enthalten zusätzliche Informationen in ihrem MSP-Ordner. Wenn Sie z. B. mit einem fernen Peer arbeiten, dann werden möglicherweise die folgenden Ordner angezeigt:

- **admincerts:** Dieser Ordner enthält die Liste der Administratoren für diese Organisation oder Komponente. Sie müssen Ihr signCert-Zertifikat in diesen Ordner hochladen, wenn Sie mit einem fernen Peer über die Befehlszeile oder mithilfe der SDKs arbeiten. Wenn Sie den Fabric-CA-Client verwenden, benötigen Sie außerdem einen Ordner "admincerts" in Ihrem MSP, der die entsprechenden signCert-Zertifikate enthält, die als Administratorzertifikate erkannt werden sollen.
- **tls:** In diesem Ordner werden TLS-Zertifikate gespeichert, die zur Kommunikation mit anderen Netzkomponenten verwendet werden.

Weitere Informationen zur Struktur der MSPs finden Sie im Abschnitt zur [Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html "Membership") und im Abschnitt zu [Membership Service Providers ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/msp.html "Membership Service Providers") in der Hyperledger Fabric-Dokumentation.

## Eintragung und Registrierung mithilfe des Fabric-CA-Clients durchführen
{: #managing-certificates-enroll-register-caclient}

Zum Generieren von Zertifikaten und zum Registrieren einer neuen Identität bei der Zertifizierungsstelle können Sie auch den Fabric-CA-Client verwenden. Die folgenden Anweisungen dienen zum Generieren von Zertifikaten anhand Ihrer Administratoridentität und zur anschließenden Verwendung dieser Zertifikate zur Registrierung eines neuen Clients. Weitere Informationen zur Eintragung mithilfe des Fabric-CA-Clients und zur Generierung von Zertifikaten finden Sie im Abschnitt [Membership Services Providers (MSPs)](/docs/services/blockchain/certificates.html#managing-certificates-msp).

### Eintragung mithilfe des Fabric-CA-Clients durchführen
{: #managing-certificates-enroll-app-caclient}

1. Laden Sie die [Fabric-CA-Binärkomponenten](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) auf Ihr lokales System herunter und extrahieren Sie sie. Verschieben Sie sie in einen Ordner wie z. B. `$HOME/fabric-ca-platform/`. Wechseln Sie in das Verzeichnis, in das Sie die Clientbinärkomponenten verschoben haben, sodass Sie in Ihren Befehlen direkt darauf verweisen können.
    ```
    cd $HOME/fabric-ca-platform/
    ```
    {:codeblock}

2.  Legen Sie den Pfad fest, in dem der Client Ihre Schlüssel erstellen kann. Vergewissern Sie sich, dass das Konfigurationsmaterial entfernt wurde, das beim letzten Versuch erstellt wurde. Wenn Sie den Befehl `enroll` zuvor noch nicht ausgeführt haben, dann sind der Ordner `msp` und die Datei `.yaml` nicht vorhanden.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3. Laden Sie die TLS-Zertifikate von {{site.data.keyword.cloud_notm}} herunter, die dem verwendeten Serviceplan, Standort und Cluster entsprechen. Ihren Serviceplan können Sie anhand der URL Ihrer Zertifizierungsstelle ermitteln.
  - TLS-Zertifikat für den Starter Plan
    - US: [us01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert"); [us03.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert"); [us05.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert"); [us07.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert"); [uk03.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")
  - [TLS-Zertifikat für den Enterprise Plan ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

  Speichern Sie den Inhalt in einem Ordner, z. B. ``$HOME/tls``. Dieser Schritt ermöglicht das Verschlüsseln der Daten während der Übertragung.

4. Öffnen Sie die JSON-Datei mit dem **Verbindungsprofil** in der Anzeige "Übersicht" im Network Monitor und suchen Sie die folgenden Variablen:
  * URL für Zertifizierungsstelle: `url` unter `certificateAuthorities`
  * Administrator-ID: ``enrollId``
  * Administratorkennwort: ``enrollSecret``
  * Name der Zertifizierungsstelle: ``caName``

5. Unter Verwendung des Fabric-CA-Clients kann ein `enroll`-Aufruf an die Zertifizierungsstelle gesendet werden. Hierzu übergeben Sie den Pfad für die TLS-Zertifikate sowie die vier oben angegebenen Zeichenfolgen mit dem folgenden Befehl:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Sie müssen diesen Befehl in dem Verzeichnis ausführen, in das der Fabric-CA-Client verschoben wird. Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

6. Suchen Sie das Administratorzertifikat in `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. Sie können das Administratorzertifikat dann vom Network Monitor in Ihr Blockchain-Netz hochladen. Weitere Informationen zum Hinzufügen von Zertifikaten finden Sie auf der [Registerkarte "Zertifikate" der Anzeige "Mitglieder"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) im Network Monitor.

  Das CA-Stammzertifikat und der private Schlüssel für den Administrator befinden sich auch in folgenden Verzeichnissen:
  * CA-Stammzertifikat: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * Privater Schlüssel des Administrators: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Ein Beispiel zur Eintragung mithilfe des Fabric-CA-Clients und zur Verwendung der generierten Zertifikate zum Betrieb der Netzkomponenten finden Sie in den Anweisungen zum [Betrieb eines fernen Peers](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-cli-operate).

### Registrierung mithilfe des Fabric-CA-Clients durchführen
{: #register-app-caclient}

1. Setzen Sie den folgenden Befehl ab, um Ihre Zugehörigkeit und den Namen Ihrer Organisation im Blockchain-Netz zu suchen.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Daraufhin werden die folgenden Informationen angezeigt:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```

  Notieren Sie den zweiten Wert für **affiliation**. Beispiel: `ibp.PeerOrg1`. Sie müssen diesen Wert im folgenden Befehl verwenden.

2. Führen Sie den folgenden Befehl aus, um den Peer zu registrieren.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Geben Sie einen Namen und ein Kennwort für Ihren Peer an und ersetzen Sie `name` und `password` durch den Peernamen und das zugehörige Kennwort. Notieren Sie diese Informationen. Sie benötigen diese Informationen bei der Konfiguration Ihres Peers. Beispiel:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

  Eine Identität kann immer nur einmal registriert werden. Tritt ein Problem auf, dann wiederholen Sie den Vorgang mit einem Befehl, für den ein neuer Benutzername und ein neues Kennwort angegeben werden.

  Wird der Befehl erfolgreich ausgeführt, dann werden die im folgenden Beispiel dargestellten Informationen angezeigt:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```

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
{:important: .important}
{:note: .note}
{:pre: .pre}

# Identitäten erstellen und verwalten
{: #ibp-console-identities}

Die Knoten von {{site.data.keyword.blockchainfull_notm}} Platform basieren auf Hyperledger Fabric und dienen zur Erstellung genehmigter Blockchain-Netze. Dies bedeutet, dass alle Teilnehmer des Blockchain-Konsortiums Identitäten haben müssen, die ständig von einer Public Key Infrastructure überprüft werden. In der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole können Sie diese Identitäten mithilfe der Zertifizierungsstellen (Certificate Authorities, CAs) Ihrer Organisation erstellen. Sie müssen diese Identitäten in Ihrer Konsolenwallet speichern, damit Sie sie für den Betrieb Ihres Netzes verwendet werden können. 

**Zielgruppe:** Dieser Abschnitt richtet sich an Netzoperatoren, die für die Erstellung, Überwachung und Verwaltung des Blockchain-Netzes verantwortlich sind.

## Zertifizierungsstellen verwalten
{: #ibp-console-identities-manage-ca}

Eine Zertifizierungsstelle (CA) ähnelt einem öffentlich anerkannten Notar, der als Vertrauensanker zwischen mehreren Parteien fungiert, wobei jede Organisation in einem Konsortium ihre eigene Zertifizierungsstelle verwaltet. Ihre Zertifizierungsstelle erstellt die Identitäten, die zu Ihrer Organisation gehören, und gibt jede Identität als öffentlichen und privaten Schlüssel aus. Diese Schlüssel ermöglichen es allen Ihren Knoten und Anwendungen, ihre Aktionen zu signieren und zu überprüfen. Weitere Informationen darüber, wie Zertifizierungsstellen zum Erstellen von Identitäten verwendet werden, finden Sie [im Abschnitt zur Identität ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "Identität") in der Hyperledger Fabric-Dokumentation.

Wenn Sie eine Zertifizierungsstelle über die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole erstellen, werden zwei Zertifizierungsstellen mit derselben Endpunkt-URL erstellt: eine Stammzertifizierungsstelle und eine TLS-Zertifizierungsstelle. Diese beiden Zertifizierungsstellen werden unter demselben Anzeigenamen auf der Seite **Knoten** der Konsole angezeigt. Die Stammzertifizierungsstelle stellt Schlüssel für Ihre Knoten und Anwendungen bereit. Die TLS-Zertifizierungsstelle stellt Zertifikate bereit, die zum Schutz der Kommunikation innerhalb Ihres Netzes verwendet werden. Weitere Informationen zu TLS in Ihrem Netz finden Sie im Abschnitt [TLS-Zertifizierungsstelle verwenden](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca).

Wenn Sie Ihre Knoten erstellen, müssen Sie die Stammzertifizierungsstelle verwenden, um die folgenden Identitäten zu erstellen, die für die Implementierung, den Betrieb und die Interaktion mit Ihrem Netz erforderlich sind:
- **CA-Administrator:** Ihre Zertifizierungsstelle enthält eine standardmäßig CA-Administratoridentität. Diese Identität verfügt über eine Eintragungs-ID und einen geheimen Schlüssel, die analog zu einem Benutzernamen und einem Kennwort sind, die Sie während der Implementierung Ihrer Zertifizierungsstelle angeben. Sie können diesen Benutzernamen des Administrators und das Kennwort verwenden, um Ihre Zertifizierungsstelle zu betreiben und neue Identitäten zu erstellen. Wenn Sie eine Zertifizierungsstelle unter Verwendung der Konsole erstellt haben, ist der Administrator Ihrer Stammzertifizierungsstelle auch der Administrator der TLS-Zertifizierungsstelle, es sei denn, Sie entscheiden sich für die Erstellung eines separaten Administrators für die TLS-Zertifizierungsstelle.
- **Peers oder Anordnungsknoten:** Sie müssen eine Identität für jeden der Peers und Anordnungsknoten registrieren, die zu Ihrer Organisation gehören. Ihre Knoten verwenden dann diese Identitäten während der Implementierung, um die Schlüssel zu generieren, die sie für die Teilnahme am Netzwerk benötigen. Erstellen Sie aus Gründen der Sicherheit eine eindeutige Eintragungs-ID und einen geheimen Schlüssel für jeden Knoten, den Sie implementieren.
- **Administratoren von Peers oder Anordnungsknoten:** {{site.data.keyword.blockchainfull_notm}} Platform-Knoten werden mit öffentlichen Schlüsseln, die die Identitäten von Komponentenadministratoren enthalten, bereitgestellt. Diese Zertifikate ermöglichen es den Administratoren, die Komponente über einen fernen Client oder über die Konsole zu betreiben.
- **Organisationsadministratoren:** Wenn Sie einem Konsortium beitreten, das von einem Anordnungsservice gehostet wird, stellen Sie die öffentlichen Schlüssel von Identitäten bereit, die zu den Administratoren für Ihre Organisation werden. Sie können diese Identitäten verwenden, um Kanäle zu erstellen oder zu bearbeiten.
- **Anwendungen:** Ihre Anwendungen müssen ihre Transaktionen signieren, bevor sie sie zur Prüfung durch das Netz übergeben. Sie müssen Identitäten erstellen, die Sie verwenden können, um Transaktionen von Ihren Clientanwendungen zu signieren.

Sie können diese Identitäten über die Konsole mithilfe des [Registrierungsprozesses](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) erstellen. Nachdem Sie die Administratoridentitäten registriert haben, müssen Sie für jede Identität einen öffentlichen Schlüssel ausgeben, diesen der MSP-Definition Ihrer Organisation bereitstellen und die Identität zu Ihrer Kosolenwallet hinzufügen. Sie können diese Schritte für eine Administrationsidentität ausführen, wenn Sie [den MSP Ihrer Organisation erstellen](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Sie können separate Identitäten als Organisations- oder Knotenadministrator verwenden oder aber mit einer Identität beide Tasks abdecken. Das [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) verwendet eine Identität als Administrator für jede Organisation, die im Lernprogramm erstellt wurde.

## Identität für Zertifizierungsstelle festlegen
{: #ibp-console-identities-ca-identity}

Bevor Sie mit Identitäten arbeiten, müssen Sie die Identität des CA-Administrators festlegen, indem Sie entweder die Administratoridentität verwenden, die während der Erstellung der Zertifizierungsstelle erstellt wurde, oder indem Sie eine neue Identität erstellen. Öffnen Sie die Zertifizierungsstelle auf der Registerkarte **Knoten**. Die Eintragungs-ID der aktuell aktiven Identität wird neben dem Namen der Zertifizierungsstelle oben in der Anzeige angezeigt. Sie können diese Administratoridentität verwenden, um andere Identitäten zu erstellen, indem Sie über die Schaltfläche **Registrieren** Identitäten registrieren, die zu Organisationsadministratoren und Knotenidentitäten werden, oder über die Schaltfläche **Eintragen** Identitäten generieren und sie in die Wallet exportieren.

Wenn Sie eine andere Identität als CA-Administrator festlegen möchten, klicken Sie auf das Symbol **Einstellungen** und anschließend in der Auswahlleiste auf **Identität festlegen**. Sie können über die Registerkarte **Vorhandene Identität** eine Identität angeben, die in der Wallet vorhanden ist. Alternativ können Sie die Registerkarte **Neue Identität** verwenden, um entweder Zertifikate für einen neuen Administrator einzufügen oder eine JSON-Datei mit den Zertifikaten hochzuladen. 

Nicht alle Identitäten haben die Möglichkeit, neue Benutzer zu registrieren. Weitere Informationen, einschließlich der Vorgehensweise zum Erstellen eines weitere CA-Administrators, finden Sie im Abschnitt zum [Erstellen neuer CA-Administratoren](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin).
{: note}

## Identitäten registrieren
{: #ibp-console-identities-register}

Der erste Schritt beim Erstellen einer Identität wird als **Registrierung** bezeichnet. Während der Registrierung werden eine Eintragungs-ID und ein geheimer Schlüssel erstellt, die dann von einem Knoten oder einem Organisationsadministrator verwendet werden können, um diese Identität durch die Generierung eines öffentlichen und privaten Schlüssels **einzutragen**.

Sie müssen die folgenden Informationen eingeben, wenn Sie eine neue Identität bei Ihrer Zertifizierungsstelle registrieren.

- **Eintragungs-ID** und **Geheimer Eintragungsschlüssel**: Diese Identität verfügt über eine ID und einen geheimen Schlüssel, die analog zu einem Benutzernamen und einem Kennwort sind, die Sie während der Implementierung Ihrer Zertifizierungsstelle angeben. Sie können diese Administrator-ID und den geheimen Schlüssel verwenden, um Zertifikate mit dieser Identität über die Konsole oder den Fabric-CA-Client zu erstellen.
- **Typ**: Wenn die Zertifizierungsstelle implementiert wurde, hat der Administrator die Identitätstypen angegeben, die von der Zertifizierungsstelle ausgegeben wurden. Gängige Beispiele für Identitätstypen sind Peer, Anordnungsknoten und Client (Anwendungen). Wählen Sie den Identitätstyp für diesen Benutzer aus der Liste der verfügbaren Typen aus.
- **Zugehörigkeit**: Der Teil Ihrer Organisation, der mit diesem Benutzer verbunden werden soll. Dies kann z. B. eine Abteilung oder Einheit sein, die eine Anwendung oder einen Peer betreibt. Es ist möglich, einen bestimmten CA-Administrator so zu beschränken, dass er nur Benutzer in seine eigenen Abteilung für eine Organisation registrieren kann.
- **Maximale Anzahl der Eintragungen:** Geben Sie optional die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit einer bestimmten Identität ein. Durch die Angabe einer begrenzten Anzahl von Eintragungen können Sie Ihre Knoten und Anwendungen besser schützen. Standardmäßig ist eine unbegrenzte Anzahl von Eintragungen möglich.
- **Attribute**: Optional können Sie eine beliebige [attributbasierte Zugriffssteuerung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "Attributbasierte Zugriffssteuerung") für den Benutzer angeben. Beispielsweise können Sie diesen Abschnitt zum [Erstellen eines weiteren CA-Administrators](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) mit der Berechtigung zum Registrieren und Eintragen neuer Identitäten verwenden. Eine vollständige Liste der verfügbaren Fabric-CA-Attribute finden Sie im Abschnitt [Neue Identität registrieren ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Neue Identität registrieren") im Fabric-CA-Benutzerhandbuch.

Klicken Sie auf der Übersichtsanzeige für die Zertifizierungsstelle auf die Schaltfläche **Benutzer registrieren**, um einen neuen Benutzer zu erstellen. Stellen Sie sicher, dass Sie [die Identität festgelegt haben](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity), indem Sie eine Identität verwenden, die neue Benutzer registrieren kann, bevor Sie diese Task ausführen. Für gewöhnlich ist dies der Benutzer `admin`. Wenn die Schaltfläche grau ist, bedeutet dies, dass Sie entweder keine Identität festgelegt haben oder dass die Identität keine neuen Identitäten erstellen kann.  

Wenn Sie auf **Benutzer registrieren** klicken, wird eine Reihe von Seitenanzeigen geöffnet:
1. Geben Sie in der Anzeige der ersten Seite die **Eintragungs-ID** und den **geheimen Schlüssel** für die neue Identität ein. Da sie nicht von der Konsole gespeichert werden, **speichern Sie diese Werte**.
2. Wählen Sie in der zweiten Anzeige den **Typ** der Identität aus. Die Dropdown-Liste enthält die Liste der Typen, die von dieser Zertifizierungsstelle unterstützt werden. Wählen Sie dann die entsprechende **Zugehörigkeit** für die neue Identität aus. Sie können eine vorhandene Zugehörigkeit in der Dropdown-Liste auswählen oder eine neue Zugehörigkeit eingeben.
3. Geben Sie in der dritten Anzeige die **maximale Anzahl der Eintragungen** ein, die für diese Identität zulässig sind. Erfolgt hier keine Angabe, dann wird der Standardwert (unbegrenzte Anzahl an Eintragungen) verwendet.
4. Fügen Sie in der Anzeige der letzten Seite die **Attribute** der Identität hinzu, die Sie erstellen.

Nachdem Sie auf **Registrieren** geklickt haben, wird die neue Identität zur Liste der **authentifizierten Benutzer** hinzugefügt, die von Ihrer Zertifizierungsstelle erstellt wurden. Die Identitäten werden nach ihrer **Eintragungs-ID** zusammen mit ihrem **Typ** und ihrer **Zugehörigkeit** aufgeführt. Wenn Sie auf eine Identität in der Tabelle klicken, wird eine Seitenanzeige geöffnet, in der Sie die Anzahl der **maximalen Eintragungen** und die **Attribute** anzeigen können, die während der Registrierung erstellt wurden.

### Neue CA-Administratoren erstellen
{: #ibp-console-identities-ca-admin}

Standardmäßig kann nur der CA-Administrator, der während der Bereitstellung erstellt wurde, neue Identitäten registrieren. Über die Anzeige **Attribute** des Registrierungsprozesses können Sie auch weitere Identitäten erstellen, die neue Benutzer registrieren können.

Klicken Sie in der Seitenanzeige 4 auf die Schaltfläche **Attribut hinzufügen**. Geben Sie als **Attributname** die Bezeichnung `hf.Registrar.Roles` an. Geben Sie als **Attributwert** den Wert `*` ein. Sie können in dieser Anzeige auch eine Identität erstellen, die nur bestimmte Typen von Identitäten, beispielsweise Clients oder Peers, oder Identitäten in einer bestimmten Zugehörigkeit registrieren kann. Sie können auch eine Identität erstellen, die die Möglichkeit hat, eine Identität und alle Zertifikate, die der Identität ausgegeben wurden, zu widerrufen. Eine vollständige Liste der Attribute finden Sie im Abschnitt [Neue Identität registrieren ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Neue Identität registrieren") im Fabric-CA-Benutzerhandbuch.

## Identität eintragen
{: #ibp-console-identities-enroll}

Sie können den öffentlichen und den privaten Schlüssel für jede Identität generieren, die bei Ihrer Zertifizierungsstelle registriert wurde. Wenn Sie zusätzliche Administratoridentitäten bei Ihrer Zertifizierungsstelle registriert haben, können Sie die Schlüssel für die Administratoridentitäten erstellen und anschließend zusätzlich einschließen, wenn Sie die [MSP der Organisation erstellen](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp).

Bevor Sie eine Identität eintragen, müssen Sie die [Identität festlegen](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity), damit die Zertifizierungsstelle betrieben werden kann. Normalerweise wird die Administratoridentität festgelegt, die Sie beim Erstellen der Zertifizierungsstelle angegeben haben. Sie können bestätigen, dass diese Identität für die Zertifizierungsstelle (Certificate Authority, CA) festgelegt ist, indem Sie die Seite mit CA-Details überprüfen und die Eintragungs-ID der momentan aktiven Identität neben dem CA-Namen anzeigen. Nachdem Sie bestätigt haben, dass Ihre Administrator-ID als Identität festgelegt ist, klicken Sie im Überlaufmenü des Benutzers auf **Eintragungs-ID**, um das Zertifikat und den Schlüssel für einen beliebigen Benutzer zu generieren, der bei der CA registriert ist.

- Geben Sie den `geheimen Eintragungsschlüssel` des Benutzers ein.
- Im nächsten Schritt werden die generierten Schlüssel angezeigt. 
  - Der öffentliche Schlüssel wird im Feld **Zertifikat** angezeigt. Dieses Zertifikat wird auch als Eintragungszertifikat, Signierzertifikat oder signCert-Zertifikat bezeichnet. Sie müssen das signCert-Zertifikat in eine Datei auf Ihrem lokalen System exportieren, damit es beim Erstellen einer Clientanwendung mit der VSCode-Erweiterung verwendet werden kann.
  - Der entsprechende private Schlüssel befindet sich im Feld **Privater Schlüssel**. Sie müssen den privaten Schlüssel in Ihr lokales Dateisystem exportieren, damit er für eine Clientanwendung verwendet werden kann, die mit der VSCode-Erweiterung erstellt wurde. 
  - Das Paar aus Zertifikat und privatem Schlüssel, das durch Klicken auf **Eintragungs-ID** erstellt wurde, wird nur einmal generiert und weder von der Konsole noch von Ihrem Browser gespeichert. Das Klicken auf die Schaltfläche **Eintragungs-ID** wird auch für die maximale Anzahl der Eintragungen berücksichtigt, die Sie für den CA-Administrator festgelegt haben. Im Rahmen dieser Eintragung sollten Sie das Schlüsselpaar speichern, indem Sie die Identität in Ihr lokales Dateisystem herunterladen oder zu Ihrer Konsolenwallet hinzufügen. Geben Sie einen neuen Namen für dieses Schlüsselpaar aus öffentlichem und privatem Schlüssel im Feld **Name** ein, um diese Schlüssel abzurufen.
- **Wichtig:** Klicken Sie auf **Identität exportieren**, um das Zertifikat und den Schlüssel als einzelne JSON-Datei in Ihr lokales Dateisystem herunterzuladen. Sie sind für den Schutz und die Verwaltung dieser Schlüssel verantwortlich. 
- Klicken Sie auf **Identität zu Wallet hinzufügen**, um diese Zertifikate zur Konsolenwallet hinzuzufügen. Der Name und die Schlüssel für diese Identität befinden sich dann in einer neuen Kachel auf der [Registerkarte für das Wallet](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet).

Sie können auch den Fabric-CA-Client oder die Fabric-SDKs verwenden, um die Identitäten einzutragen, die Sie in der Konsole erstellt haben. Die Konsole bietet Ihnen alle Informationen, die Sie benötigen, um diese Schritte auszuführen. Stellen Sie sicher, dass Sie die **Eintragungs-ID** und den **geheimen Eintragungsschlüssel** gespeichert haben, die Sie während der Registrierung angegeben haben.

## TLS-Zertifizierungsstelle verwenden
{: #ibp-console-identities-tlsca}

Die Kommunikation innerhalb Ihres Netzes wird durch TLS-Zertifikate geschützt. TLS verschlüsselt die Kommunikation zwischen Ihren Knoten und zwischen Ihren Knoten und Ihren Anwendungen. Die Verwendung von TLS verhindert, dass Angreifer die Kommunikation zwischen Ihren Knoten unterbrechen oder die Transaktionen lesen, die von Ihren Anwendungen übergeben wurden. Die Schlüssel und Zertifikate, die für TLS verwendet werden, unterscheiden sich von den Zertifikaten, die zum Signieren und Prüfen Ihrer Transaktionen verwendet werden, und werden von einer separaten Zertifizierungsstelle ausgegeben.

Jede von der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole erstellte Zertifizierungsstelle enthält eine Stammzertifizierungsstelle und eine TLS-Zertifizierungsstelle. Sie können beide Zertifizierungsstellen unter dem gleichen Anzeigenamen auf der Registerkarte "Knoten" Ihrer Konsole anzeigen. Klicken Sie auf die Übersichtsanzeige der Zertifizierungsstelle und dann auf **TLS-Zertifizierungsstelle**, um Ihre TLS-Zertifizierungsstelle zu betreiben. Ihre TLS-Zertifizierungsstelle verfügt über die [gleiche Registrierung](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) und den gleichen [Eintragungsprozess](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) wie die Stammzertifizierungsstelle. Beide Zertifizierungsstellen werden mit der gleichen ID und dem gleichen geheimen Schlüssel des CA-Administrators bereitgestellt.

Jeder Peer oder Anordnungsknoten, den Sie implementieren, muss ein öffentliches TLS-Zertifikat generieren. Bei der Erstellung des Knotens über die Konsole werden Sie zur Angabe einer Eintragungs-ID und eines geheimen Schlüssels einer Identität aufgefordert, die für Ihre TLS-Zertifizierungsstelle registriert ist. Im [Lernprogramm zum Erstellen des Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) wird die Identität des CA-Administrators verwendet. Es ist jedoch Best Practice, für jeden Knoten eine eindeutige Identität mit Ihrer TLS-Zertifizierungsstelle zu registrieren. Der Knoten verwendet diese Identität, um das TLS-Zertifikat während der Implementierung zu generieren. Dieses Zertifikat wird von jeder Anwendung benötigt, die mit dem Anordnungsknoten oder Peer kommunizieren muss. Sie können das TLS-Zertifikat eines Knotens finden, indem Sie zur Übersichtsanzeige des Knotens navigieren und auf "Einstellungen" klicken. Die TLS-Zertifikate für Ihre Peers und Anordnungsknoten befinden sich im Verbindungsprofil, das Sie von [der Registerkarte mit den Smart Contracts](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK) herunterladen können.

Beim Erstellen eines Peers oder Anordnungsknotens über die Konsole können Sie über die TLS-Zertifizierungsstelle auch einen zusätzlichen Domänenname für jeden Knoten angeben. Geben Sie bei der Bereitstellung des Peers oder Anordnungsknotens den neuen Domänennamen in das Feld **TLS-CSR-Hostname** ein. Dieser Hostname wird der Liste der allgemeinen Namen in dem TLS-Zertifikat hinzugefügt, das an den Knoten ausgegeben wird.


## Ablauf von Zertifikaten
{: #ibp-console-identities-expiration}

Zertifikate, die mithilfe von {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Zertifizierungsstellen generiert werden, laufen nach einem Jahr ab. Der Ablaufzeitraum ist derselbe für Zertifikate, die mit den Fabric-SDKs, dem Fabric-CA-Client oder über die Konsole generiert werden. Wenn die Zertifikate ablaufen, können Ihre Anwendungen nicht mehr mit Ihrem Netz interagieren und Sie müssen zum Generieren neuer Zertifikate eine erneute Eintragung vornehmen. Wenn Sie das Eintragungslimit für einen Benutzer erreicht haben, können Sie einen neuen Benutzer registrieren und dann eintragen.

Sie können das Ablaufdatum für Ihre Zertifikate über die Befehlszeile überprüfen. Zunächst müssen Sie Zertifikate, die im Base64-Format vorliegen, in das PEM-Format konvertieren, indem Sie auf Ihrer lokalen Maschine den folgenden Befehl ausführen:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<pfad-zum-zertifikat>/cert.pem | base64 $FLAG
```
{:codeblock}

Führen Sie den folgenden Befehl aus, um das PEM-codierte Zertifikat in einer vom Benutzer lesbaren Form anzuzeigen:
```
openssl x509 -in <Zertifikatsdatei> -text
```
{:codeblock}

Das Zertifikat sieht ähnlich wie im folgenden Beispiel aus:

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

## Identitäten in der Konsolenwallet speichern
{: #ibp-console-identities-wallet}

Die Wallet speichert die Identitäten und Schlüssel, mit denen die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole die Knoten Ihres Netzes betreibt. Sie müssen die Administratoren der Peers, Anordnungsknoten und Organisationen zu dieser Wallet hinzufügen, bevor Sie die Konsole verwenden können, um mit Kanälen und Smart Contracts zu arbeiten. Sie können mit dieser Wallet auch problemlos die Identitäten speichern, die von Ihren Anwendungen verwendet werden. Sie können die Identitäten jederzeit aus der Wallet exportieren. Verwenden Sie die linke Navigationsseite, um zur Übersichtsanzeige der Wallet zu wechseln. Sie können über diese Übersichtsseite Identitäten hinzufügen, aktualisieren und exportieren.

Die Wallet ist eine Komponente der Konsole und kein separater Service. Sie speichert die Schlüssel anstatt im lokalen Dateisystem im lokalen Speicher des Browsers, den Sie zum Zugriff auf die Konsole verwenden. Wenn Sie von einem anderen Browser aus auf Ihre Konsole zugreifen oder eine private Browsersitzung starten, können Sie nicht auf die Identitäten zugreifen, die in der Wallet gespeichert sind. **Es wird empfohlen, dass Sie Ihre Administratoridentitäten aus der Konsole exportieren und an einem sicheren Ort speichern**.
{:important}

### Identitäten hinzufügen
{: #ibp-console-identities-add}

Sie können Ihrer Wallet eine Administratoridentität hinzufügen, wenn Sie [den MSP Ihrer Organisation erstellen](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Eine über die Konsole verwaltete Zertifizierungsstelle kann auch während des [Eintragungsprozesses](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) eine Identität zur Wallet hinzufügen.

Sie können die Schaltfläche **Identität hinzufügen** in der Übersichtsanzeige verwenden, um eine Identität direkt in die Wallet zu importieren. Wenn Sie auf diese Schaltfläche klicken, wird eine Seitenanzeige geöffnet, in der Sie das Schlüsselpaar aus öffentlichem und privatem Schlüssel einer Identität hinzufügen können.
- **Name:** Geben Sie einen Identitätsnamen ein, der nur für als Referenz verwendet wird.
- **Öffentlicher Schlüssel:** Fügen Sie den öffentlichen Schlüssel der Identität ein, den Sie mit Ihrer Zertifizierungsstelle generiert haben.
- **Privater Schlüssel:** Fügen Sie den privaten Schlüssel der Identität ein, den Sie mit Ihrer Zertifizierungsstelle generiert haben.


Sie können auch eine Identität hinzufügen, indem Sie eine JSON-Datei in dem unten stehenden Format hochladen. Sie können auch die Schaltfläche **JSON hochladen** verwenden, um mehrere Identitäten auf einmal hochzuladen.

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Wenn Sie eine Identität mit dem Fabric-CA-Client oder mit den Fabric-SDKs eingetragen haben, müssen Ihre Schlüssel vom PEM-Format in das Base64-Format konvertiert werden. Sie können Zertifikate in das Base64-Format konvertieren, indem Sie den folgenden Befehl auf Ihrer lokalen Maschine ausführen:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<pfad-zum-zertifikat>/cert.pem | base64 $FLAG
```
{:codeblock}

### Identitäten anzeigen und aktualisieren
{: #ibp-console-identities-update-identities}

Klicken Sie auf der Registerkarte **Wallet** auf eine Kachel, um eine Identität aus der Wallet anzuzeigen, zu aktualisieren oder zu entfernen. Es kann erforderlich sein, die Identitäten zu aktualisieren, wenn ihre Zertifikate abgelaufen sind, und die Identitäten müssen neue Schlüssel von der Zertifizierungsstelle erhalten. Sie können diese Registerkarte auch verwenden, um Schlüssel aus Ihrer Konsole und Ihrem lokalen System zu löschen.

Wenn Sie auf eine Identität klicken, wird eine Seitenanzeige geöffnet, in der die öffentlichen und privaten Schlüssel im Base64-Format angezeigt werden. Klicken Sie auf **Exportieren**, um die Zertifikate der Identität in das lokale Dateisystem herunterzuladen. Klicken Sie auf **Aktualisieren**, um den Identitätsnamen in der Wallet zu ändern oder eine neue Gruppe von Schlüsseln in die Anzeige einzufügen. Klicken Sie auf **Entfernen**, wenn Sie diese Identität nicht mehr verwenden und ihre Schlüssel löschen möchten.

## Identitäten zuordnen
{: #ibp-console-identities-associate-admin}

Sie müssen den öffentlichen Schlüssel Ihrer Organisation und der Knotenadministratoren [der MSP-Definition Ihrer Organisation](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp) bereitstellen. Die Knoten oder Kanäle, die von der Konsole erstellt werden, verwenden die Zertifikate aus der MSP-Definition, um zu überprüfen, wer ein gültiger Administrator ist. Daher muss das gleiche Schlüsselpaar aus öffentlichem und privatem Schlüssel, das Sie zum Hinzufügen eines Administratorzertifikats zur MSP-Definition verwendet haben, in der Konsolenwallet gespeichert werden.

Wenn Sie die Konsole verwenden, um einen Anordnungsknoten oder Peer zu erstellen, wird eine Anzeige **Identität zuordnen** angezeigt. Wählen Sie eine Identität in der Wallet aus, deren Zertifikat auch in der MSP-Definition Ihres Organisation enthalten ist. Wenn Sie einen Kanal erstellen oder bearbeiten, müssen Sie im Abschnitt **Identität zuordnen** auch eine Administratoridentität auswählen. Dies ermöglicht es der Konsole, die Identität zu kennen, die verwendet wird, wenn sie mit den Peers, Anordnungsknoten und dem Konsortium des Anordnungsservice kommuniziert.

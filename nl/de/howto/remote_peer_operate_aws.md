---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Peers in AWS betreiben
{: #remote-peer-aws-operate}

Nach der Einrichtung von Peers unter {{site.data.keyword.blockchainfull}} Platform in AWS müssen Sie verschiedene operative Schritte ausführen, bevor Ihr Peer Transaktionen zum Abfragen und Aufrufen des Ledgers für das Blockchain-Netz absetzen kann. Die Schritte umfassen das Hinzufügen Ihrer Organisation zu einem Kanal, das Hinzufügen Ihres Peers zum Kanal, das Installieren des Chaincodes auf dem Peer, die Instanziierung des Chaincodes auf dem Kanal und die Verbindung von Anwendungen mit dem Peer. Sie können entweder die [Fabric-SDKs](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-with-sdk) oder die [Befehlszeile](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-cli-operate) verwenden, um diese Arbeitsschritte auszuführen. Die Fabric-SDKs stellen den empfohlenen Pfad dar, wobei in den Anweisungen davon ausgegangen wird, dass Sie bereits mit dem Betrieb des SDK vertraut sind.

**Hinweis**: Ein Peer unter {{site.data.keyword.blockchainfull_notm}} in AWS verfügt nicht über Zugriff auf die vollständige Funktionalität oder Unterstützung von Peers, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Demzufolge können Sie den Network Monitor nicht zum Betreiben Ihres Peers verwenden. Bevor Sie mit der Ausführung von Peers in AWS beginnen, müssen Sie sich mit den [wichtigen Hinweisen](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-limitations) vertraut machen.

## Fabric-SDKs zum Betrieb des Peers verwenden
{: #remote-peer-aws-operate-with-sdk}

Die Hyperledger Fabric-SDKs stellen eine leistungsstarke Gruppe von APIs zur Verfügung, die Anwendungen die Interaktion mit und den Betrieb von Blockchain-Netzen ermöglichen. Die aktuellste Version der Liste der unterstützten Sprachen und die vollständige Liste der verfügbaren APIs in den Hyperledger Fabric-SDKs finden Sie in der [Hyperledger Fabric SDK Community-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community-Dokumentation"). Sie können die Fabric-SDKs verwenden, um Ihren Peer zu einem Kanal unter {{site.data.keyword.blockchainfull_notm}} Platform hinzuzufügen, einen Chaincode auf dem Peer zu installieren und den Chaincode auf einem Kanal zu instanziieren.

Die folgenden Anweisungen verwenden das [Fabric Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js"), um den Peer zu betreiben. Dabei wird davon ausgegangen, dass Sie bereits über Kenntnisse zu dem SDK verfügen. Sie können das [Lernprogramm "Anwendungen entwickeln"](/docs/services/blockchain/v10_application.html#dev-app) benutzen, um sich mit der Vorgehensweise zur Verwendung des Node-SDK vertraut zu machen. Außerdem dient es als Leitfaden für die Entwicklung von Anwendungen mit dem Peer, sobald Sie zum Aufrufen und Abfragen des Chaincodes bereit sind.

Der Schnelleinstieg für Peers von {{site.data.keyword.blockchainfull_notm}} Platform in AWS erstellt zwei Peers für die Hochverfügbarkeit. Aus diesem Grund müssen Sie die operativen Schritte einmal pro Peer ausführen. Sobald Sie zum Abfragen und Aufrufen des Chaincodes über Ihre Anwendung bereit sind, dann stellen Sie für beide Peers vom SDK eine Verbindung her, um sicherzustellen, dass Ihre [Anwendungen hoch verfügbar](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) sind.

### Node-SDK installieren
{: #remote-peer-aws-operate-install-sdk}

Sie können NPM verwenden, um das [Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js") zu installieren:
```
npm install fabric-client@1.2
```
{:codeblock}

Hierbei wird die Verwendung von Version 1.2 des Node-SDK empfohlen.

### SDK zur Nutzung mit dem Peer vorbereiten
{: #remote-peer-aws-operate-sdk}

Bevor Sie das SDK zum Betreiben des Peers benutzen können, müssen Sie die erforderlichen Zertifikate (Eintragung) generieren, auf deren Basis Ihre Anwendung mit dem Netz unter {{site.data.keyword.blockchainfull_notm}} Platform und dem Peer kommunizieren kann. Führen Sie die Schritte für die [Eintragung beim SDK](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk) aus und verwenden Sie dabei Ihre Identität **admin**. Das Lernprogramm [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html#dev-app) wird ebenfalls unter **admin** eingetragen, sodass der Beispielcode nicht geändert werden muss.

### signCert-Zertifikat in IBM Blockchain Platform hochladen
{: #remote-peer-aws-operate-upload-SDK}

Sie müssen das SDK-Signierzertifikat in das Netz unter {{site.data.keyword.blockchainfull_notm}} Platform hochladen, damit andere Mitglieder Ihre digitale Signatur erkennen können.

- Ihr Signierzertifikat befindet sich in dem Verzeichnis, in dem das SDK die Verschlüsselungsinformationen in einer Datei mit dem Namen "admin" generiert hat. Kopieren Sie das Zertifikat, das in Anführungszeichen angegeben ist, an die Position nach dem Feld `certificate`. Das entsprechende Element beginnt mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` und endet mit der Zeichenfolge `-----END CERTIFICATE-----`. Sie können die Befehlszeilenschnittstelle verwenden, um das Zertifikat ins PEM-Format umzuwandeln, indem Sie den Befehl `echo -e "<CERT>" > admin.pem` absetzen. Anschließend können Sie die Inhalte des Zertifikats mithilfe von Network Monitor in das Blockchain-Netz einfügen. Melden Sie sich beim Netz unter {{site.data.keyword.blockchainfull_notm}} Platform an. Klicken Sie in der Anzeige "Mitglieder" des Network Monitor auf **Zertifikate** > **Zertifikate hinzufügen**. Geben Sie für Ihr Zertifikat einen beliebigen Namen an und fügen Sie die Inhalte in das Feld für das **Zertifikat** ein. Klicken Sie auf **Erneut starten**, wenn Sie dazu aufgefordert werden anzugeben, ob die Peers erneut gestartet werden sollen. Diese Aktion muss ausgeführt werden, bevor Ihre Organisation dem Kanal beitritt.

### signCert-Zertifikat in Peer hochladen
{: #remote-peer-aws-operate-upload-signcert}

Sie müssen auch das Signierzertifikat des SDK in den fernen Peer hochladen und dann einen Neustart durchführen. Sie müssen im Container des fernen Peers dasselbe Signierzertifikat installieren, das Sie in [IBM Blockchain Platform hochgeladen](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-upload-SDK) haben.

Stellen Sie eine SSH-Verbindung zu Ihrer VPC-Instanz her, indem Sie die Instanz in der AWS-Konsole auswählen (klicken Sie hierzu auf **Services > EC2 > Instanzen**) und anschließend auf die Schaltfläche "Verbinden" klicken. Befolgen Sie die Anweisungen in AWS, um den Befehl "ssh" auszugeben.

Führen Sie die folgenden Befehle innerhalb des Peer-Containers aus, um Ihr Zertifikat hochzuladen.  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- Ersetzen Sie `<PEER_ENROLL_ID>` durch die Eintragungs-ID, die in der Schnelleinstiegsvorlage angegeben wurde und dieser fernen Peerinstanz zugeordnet ist.  
- Ersetzen Sie `<CERT.PEM>` durch Ihr signCert-Zertifikat; dieses beginnt mit `-----BEGIN CERTIFICATE-----` und endet mit `-----END CERTIFICATE-----`.    

  **Hinweis:** Falls die Datei `cert.pem` vorhanden ist, überschreiben Sie sie nicht, sondern erstellen Sie eine neue Datei, beispielsweise `cert2.pem`.

Da Sie ein neues Zertifikat hinzugefügt haben, müssen Sie für den Container einen Neustart durchführen, damit der Peer das Zertifikat erkennt. Führen Sie die folgenden [Anweisungen](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-restart) aus, um für den Peer einen Neustart durchzuführen.

### TLS-Zertifikat des Peers an SDK übergeben
{: #remote-peer-aws-operate-download-tlscert}

Sie müssen die Inhalte des TLS-Zertifikats `cacert.pem` aus dem Peer-Container in Ihre Anwendung kopieren, um die Kommunikation des SDK zu authentifizieren. Führen Sie den folgenden Befehl im Peer-Container aus. Ersetzen Sie `<PEER_ENROLL_ID>` durch den Stacknamen des fernen Peers, den Sie in der Schnelleinstiegsvorlage angegeben haben. (Denken Sie daran, dass zwei VPC-Instanzen erstellt werden.)
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

Kopieren Sie den gesamten Text der Datei `ca.crt` in die Zwischenablage. Der Text beginnt mit dem ersten Vorkommen von `-----BEGIN CERTIFICATE-----` und endet mit dem letzten Vorkommen von `-----END CERTIFICATE-----`. Speichern Sie dieses Zertifikat im PEM-Format auf dem lokalen Dateisystem. Anschließend können Sie das TLS-Zertifikat in Ihre Anwendung importieren, indem Sie einen einfachen Dateilesebefehl benutzen.
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### Endpunktinformationen des Peers für SDK bereitstellen
{: #remote-peer-aws-operate-SDK-endpoints}

Zum Abrufen der Endpunktinformationen Ihres Peers suchen Sie in der AWS-Konsole nach Ihrer Peerinstanz. Notieren Sie den Wert des `öffentlichen DNS (IPv4) im AWS-EC2-Dashboard` für die EC2-Instanz und geben Sie ihn für das SDK an, indem Sie eine neue Peervariable deklarieren oder Ihr Verbindungsprofil aktualisieren. Verknüpfen Sie die öffentliche DNS-Adresse mit Port `7051`. Im folgenden Beispiel wird der Peer im Fabric-Netz definiert und es wird das von Ihnen importierte TLS-Zertifikat an den Peer übergeben.

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**Hinweis:** Da es sich bei dem Peer um eine ferne Einheit handelt, können andere Organisationen im {{site.data.keyword.blockchainfull_notm}} Platform-Netz die Endpunktinformationen Ihres Peers nicht in ihrem Verbindungsprofil finden. Wenn andere Organisationen Ihrem Peer eine zu bewilligende Transaktion senden müssen, müssen Sie die öffentliche IP und die TLS-Zertifikate in einer Out-of-band-Operation bereitstellen.

### SDK zum Beitreten zu einem Kanal verwenden
{: #remote-peer-aws-operate-join-channel-sdk}

Als Mitglied des Blockchain-Netzes muss Ihre Organisation zu einem Kanal im Netz hinzugefügt werden, bevor der Peer dem Kanal beitreten kann.

  - Sie können einen neuen Kanal für den Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) automatisch einbinden.

  - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel) durchführt.

    Nachdem Ihre Organisation zu einem Kanal hinzugefügt wurde, müssen Sie das Signierzertifikat Ihres Peers zu dem Kanal hinzufügen, damit andere Mitglieder Ihre digitale Signatur während der Ausführung von Transaktionen überprüfen können. Der Peer lädt sein Signierzertifikat während der Installation hoch, sodass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beitreten soll, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert. Möglicherweise müssen Sie einige Minuten auf den Abschluss der Kanalsynchronisation warten, bevor Sie Befehle "join channel" ausgeben können.

Nachdem Ihre Organisation Teil des Kanals geworden ist, führen Sie die Anweisungen aus, die im Abschnitt zum [Beitreten zu einem Kanal](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk) aufgeführt sind. Sie müssen die URL des Anordnungsservice und den Kanalnamen angeben.

### SDK zum Installieren des Chaincodes auf dem Peer verwenden
{: #remote-peer-aws-operate-install-cc-sdk}

Befolgen Sie die Anweisungen, um das SDK zum [Installieren eines Chaincodes](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk) auf Ihrem Peer zu verwenden.

### SDK zur Instanziierung des Chaincodes auf dem Kanal verwenden
{: #remote-peer-aws-operate-instantiate-cc-sdk}

Nur ein Mitglied des Kanals muss den Chaincode instanziieren oder aktualisieren. Aus diesem Grund kann jedes Netzmitglied auf dem Kanal, das über Peers unter {{site.data.keyword.blockchainfull_notm}} Platform verfügt, den Network Monitor zum Instanziieren des Chaincodes und zur Angabe der Bewilligungsrichtlinien verwenden. Wenn Sie den Peer allerdings zum Instanziieren des Chaincodes auf einem Kanal verwenden wollen, dann können Sie das SDK benutzen und die Anweisungen im Abschnitt zum [Instanziieren eines Chaincodes](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk) befolgen.


## Befehlszeilenschnittstelle zum Betrieb des Peers verwenden
{: #remote-peer-aws-operate-cli-operate}

Sie können den Peer auch über die Befehlszeile betreiben, indem Sie den Fabric-CA-Client und den Fabric-Container für Tools verwenden. In den vorliegenden Anweisungen generieren Sie zunächst die erforderlichen Zertifikate mit dem Fabric-CA-Client. Anschließend wird der Fabric-Container für Tools verwendet, um den Peer zu betreiben. Dazu wird der Peer zu einem Kanal hinzugefügt, es wird ein Chaincode installiert und dann der Chaincode auf einem Kanal instanziiert.

### Eintragung unter Verwendung des Fabric-CA-Clients durchführen
{: #remote-peer-aws-operate-client-enroll}

Der erste Schritt umfasst das Generieren der erforderlichen Zertifikate (Eintragung) mithilfe des Fabric-CA-Clients. Sie müssen zuerst den Fabric-CA-Client installieren. Laden Sie [fabric-ca-Binärkomponenten v1.2.1 für Ihre Plattform ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/ "Index der Repositorys") auf Ihr lokales System herunter und extrahieren Sie sie. Verschieben Sie sie in einen Ordner wie z. B. `$HOME/fabric-ca-remote/`.

1.  Bereiten Sie Ihre Umgebung zur Verwendung des Fabric-CA-Clients vor. Wechseln Sie in das Verzeichnis, in das Sie die Clientbinärkomponenten verschoben haben, sodass Sie in Ihren Befehlen direkt darauf verweisen können.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Legen Sie den Pfad fest, in dem der Client Ihre Schlüssel erstellen  kann.  Falls Sie den Befehl `enroll` zuvor noch nie ausgeführt haben, sind der Ordner `msp` und die Datei `.yaml` nicht vorhanden. Wenn Sie den Befehl `enroll` zuvor bereits einmal ausgeführt haben, ist es wichtig, dass Sie das gesamte Konfigurationsmaterial früherer Eintragungsversuche mit den folgenden `rm`-Befehlen löschen:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Sie müssen die Endpunktinformationen Ihrer Zertifizierungsstelle unter {{site.data.keyword.blockchainfull_notm}} Platform abrufen. Klicken Sie in der Anzeige "Übersicht" im Network Monitor auf die Schaltfläche **Verbindungsprofil** und öffnen Sie die JSON-Datei, die die Netzberechtigungsnachweise enthält. Blättern Sie nach unten bis zum Abschnitt `certificateAuthorities` und notieren Sie die folgenden Informationen.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  Außerdem müssen Sie ein TLS-Zertifikat aus {{site.data.keyword.blockchainfull_notm}} Platform abrufen, um mit der Zertifizierungsstelle (CA) kommunizieren zu können. Die Netzberechtigungsnachweise umfassen das erforderliche Zertifikat. Kopieren Sie im Abschnitt `certificateAuthorities` der Verbindungsprofildatei das Zertifikat, das nach `"pem:"` folgt und mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` beginnt und mit der Zeichenfolge `-----END CERTIFICATE----- ` endet. **Die Anführungszeichen dürfen nicht eingeschlossen werden**.

    Führen Sie den folgenden Befehl über ein Terminalfenster aus und ersetzen Sie dabei `<certificate>` durch das kopierte Zertifikat.
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 Speichern Sie dieses Zertifikat an einer Speicherposition, unter der Sie in zukünftigen Befehlen darauf verweisen können.  

5.  Nun sind Sie bereit zum Generieren der erforderlichen Zertifikate. Führen Sie den folgenden Befehl aus:
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    Beispiel:
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    **Tipp:** Wenn der Wert der Eintragungs-URL bzw. der Wert des Parameters `-u` ein Sonderzeichen enthält, müssen Sie entweder das Sonderzeichen codieren oder die URL in die Hochkommas einschließen. Beispiel: `!` wird zu `%21` oder der Befehl sieht wie folgt aus:

    ```
    ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    Wird der Befehl erfolgreich ausgeführt, dann wird die im folgenden Beispiel dargestellte Ausgabe angezeigt:
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    Der Befehl generiert die erforderlichen Zertifikate und speichert sie in einem Ordner mit dem Namen `msp` in `$FABRIC_CA_CLIENT_HOME`. Dieser Ordner und die darin enthaltenen Zertifikate werden in den folgenden Schritten benötigt.

### Zertifikate auf dem lokalen System verwalten
{: #remote-peer-aws-operate-manage-certs}

Bevor Sie einen Peer betreiben können, müssen verschiedene Managementschritte für die Zertifikate auf dem lokalen System ausgeführt werden. Einige der Zertifikate, die vom Fabric-CA-Client generiert wurden, müssen auf {{site.data.keyword.blockchainfull_notm}} Platform und den Peer hochgeladen werden. Außerdem müssen die TLS-Zertifikate von Platform und dem Peer heruntergeladen werden. Wenn Sie weitere Informationen zu den Zertifikaten, mit denen Sie arbeiten werden, und zu den Tasks benötigen, die auszuführen sind, dann lesen Sie den Abschnitt zum [Verwalten von Zertifikaten unter {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

Öffnen Sie auf dem lokalen System ein Command Terminal und navigieren Sie zu dem Verzeichnis, in dem die Binärkomponenten des Fabric-CA-Clients und der MSP-Ordner abgelegt wurden.

1. Kopieren Sie die Datei `cert.pem` aus dem Ordner `signcerts` in einen neuen Ordner `admincerts`.
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. Kopieren Sie das TLS-Zertifikat Ihres Anordnungsknotens aus {{site.data.keyword.blockchainfull_notm}} Platform in den MSP-Ordner. Ihr Verbindungsprofil enthält das benötigte Zertifikat. Klicken Sie in der Anzeige "Übersicht" im Network Monitor auf die Schaltfläche **Verbindungsprofil** und öffnen Sie die JSON-Datei, die die Netzberechtigungsnachweise enthält. Navigieren Sie zum Abschnitt `orderers`. Kopieren Sie das Zertifikat, das auf `"pem:"` folgt; es beginnt mit `-----BEGIN CERTIFICATE-----` und endet mit `-----END CERTIFICATE-----`. Beziehen Sie nicht die Anführungszeichen ein.

    Führen Sie in Ihrem Terminalfenster die folgenden Befehle aus:
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. Außerdem müssen Sie das TLS-Zertifikat des Peers aus dem Peer-Container unter AWS auf das lokale System kopieren.

    - Befolgen Sie [diese Anweisungen](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test), um sich bei Ihrem Peer-Container anzumelden, und führen Sie den folgenden Befehl aus; ersetzen Sie hierbei <PEER_ENROLL_ID> durch den Stacknamen des Peers, den Sie in der Schnelleinstiegsvorlage angegeben haben, gefolgt durch seine Nummer. (Denken Sie daran, dass zwei VPC-Instanzen erstellt werden.)
      ```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
      ```
      {:codeblock}

      Kopieren Sie den gesamten Text der Datei `cacert.pem` in die Zwischenablage. Der Text beginnt mit dem ersten Vorkommen von `-----BEGIN CERTIFICATE-----` und endet mit dem letzten Vorkommen von `-----END CERTIFICATE-----`.

    - Wechseln Sie wieder zum lokalen System und führen Sie die folgenden Befehle aus. Ersetzen Sie dabei den Text `<CACERT.PEM>` durch den Text aus der Zwischenablage.
      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
      ```
      {:codeblock}

      **Hinweis:** Die AWS-Schnelleinstiegsvorlage erstellt standardmäßig zwei Peerknoten in unterschiedlichen Verfügbarkeitszonen. Wenn Sie diese Schritte für einen der Peers bereits ausgeführt haben, ist die Datei "cacert.pem" beim Durchführen der Schritte für die zweite Instanz bereits vorhanden. Fahren Sie fort und ersetzen Sie sie durch das Zertifikat aus dem zweiten Peer.

4. Um Ihrer Befehlszeilenschnittstelle die Berechtigung zur Installation des Chaincodes auf dem Peer zu erteilen, laden Sie das signCert-Zertifikat, das vom Fabric-CA-Client generiert wurde, in den Administratorordner (admin) des Peers hoch und starten Sie den Peer erneut. Kopieren Sie deshalb das Zertifikat `admincert/cert.pem` vom lokalen System in das Verzeichnis `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` im Peer-Container las `cert2.pem`.

    - Führen Sie auf dem lokalen System die folgenden Befehle aus:

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Kopieren Sie den Text der Datei `cert.pem` in die Zwischenablage. Das entsprechende Element beginnt mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` und endet mit der Zeichenfolge `-----END CERTIFICATE-----`.

    - Wechseln Sie wieder zum Peer-Container und führen Sie die folgenden Befehle aus, um die Datei "cert.pem" zu den Peerzertifikaten hinzuzufügen. Ersetzen Sie dabei den Text `<CERT.PEM>` durch den Text aus der Zwischenablage.

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Hinweis:** In diesem Verzeichnis ist bereits eine Datei mit dem Namen "cert.pem" vorhanden. Die Datei darf nicht überschrieben werden.

    - Da Sie ein neues Zertifikat hinzugefügt haben, müssen Sie für den Container einen Neustart durchführen, damit der Peer das Zertifikat erkennt. Führen Sie die folgenden Anweisungen aus, um den [Peer-Container erneut zu starten](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-restart).

5. Laden Sie dasselbe Zertifikat `admincert/cert.pem` vom lokalen System auf {{site.data.keyword.blockchainfull_notm}} Platform hoch, damit die ferne Befehlszeilenschnittstelle oder Anwendung die Kanaloperationen (z. B. Abrufen des Genesis-Blocks des Kanals und Instanziieren des Chaincodes) durchführen kann.
    1. Schneiden Sie auf dem lokalen System die Datei `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` aus und kopieren Sie sie in die Zwischenablage.
    2. Rufen Sie den Network Monitor Ihres Netzes unter {{site.data.keyword.blockchainfull_notm}} Platform auf.
    3. Klicken Sie im linken Navigator auf **Mitglieder** und dann auf die Registerkarte **Zertifikate**.
    4. Klicken Sie auf die Schaltfläche **Zertifikat hinzufügen**.
    5. Ordnen Sie im Fenster **Zertifikat hinzufügen** Ihrem Zertifikat einen Namen (z. B. `fabrictools.pem`) zu, fügen Sie das soeben kopierte Zertifikat in die Zwischenablage ein und klicken Sie dann auf **Abschicken**.
    6. Sie können gefragt werden, ob Sie die Peers erneut starten möchten. Klicken Sie dazu auf **Erneut starten**.
    7. Klicken Sie im linken Navigator auf **Kanäle**  und suchen Sie den Kanal, dem der Peer beitreten soll.
    8. Klicken Sie auf die drei Punkte unter der Überschrift **Aktionen** und dann auf **Zertifikat synchronisieren**. Klicken Sie im Fenster **Zertifikat synchronisieren** auf **Abschicken**.

    **Hinweis**: Das Kanalzertifikat muss unbedingt synchronisiert werden, bevor der Peer dem Kanal beitritt oder Chaincode im Kanal instanziiert. Möglicherweise müssen Sie einige Minuten auf den Abschluss der Kanalsynchronisation warten, bevor Sie Befehle für das Beitreten zum Kanal oder das Instanziieren von Chaincode ausgeben können.   

### Fabric-Container für Tools einrichten
{: #remote-peer-aws-operate-fabric-cli}

Nachdem alle Zertifikate an die erforderliche Position verschoben wurden, können Sie den Fabric-Container für Tools aus Docker installieren und verwenden. Diese Befehle sollten lokal auf Ihrer Maschine ausgeführt werden. Denken Sie daran, die Befehle in dem Verzeichnis auszuführen, in dem der MSP-Ordner gespeichert wurde. Bevor Sie diese Schritte ausführen, sollte [Git ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Getting Started - Installing Git") auf Ihrer lokalen Maschine installiert sein.   

Laden Sie das Docker-Image der Fabric-Tools mit dem folgenden Befehl herunter:

```
docker pull ibmblockchain/fabric-tools:1.2.1
```
{:codeblock}

#### Ressourcen für den Container zusammenstellen    

Es müssen einige Ordner auf Ihrer lokalen Maschine eingerichtet werden, die das Beispiel `fabcar` enthalten, das später bei der Installation und Instanziierung von Chaincode verwendet wird. Führen Sie die folgenden Befehle aus, um die Ordnerstruktur für den Beispiel-Chaincode zu konfigurieren, und laden Sie es anschließend mit Hilfe von Git herunter.

```
mkdir toolsrc
cd toolsrc
mkdir mycc
mkdir vendor
cd vendor
git clone https://github.com/hyperledger/fabric.git
```
{:codeblock}

**Hinweis:** Wenn Sie eigene Chaincode-Dateien haben, können Sie sie in den Ordner `mycc` setzen, damit sie auch von den Peer-CLI-Befehlen verwendet werden können.  

Führen Sie die folgenden Befehle aus, um den Fabric-Container für Tools zu starten. Mit dem zweiten Befehl werden die Ordner für die MSP- und die Fabric-Beispielordner in Ihrem Tool-Container angehängt. Denken Sie daran, die Befehle in dem Verzeichnis auszuführen, in dem der MSP-Ordner gespeichert wurde.

```
docker network create blockchain.com
docker run -ti --network blockchain.com -v ${PWD}:/mnt -v path/to/toolsrc:/src ibmblockchain/fabric-tools:1.2.1
```
{:codeblock}


<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### CLI für Fabric-Tools für Beitritt des Peers zum Kanal verwenden
{: #remote-peer-aws-operate-cli-join-channel}

Bevor Sie die CLI-Befehle für den Beitritt des Peers zu einem Kanal ausführen können, muss Ihre Organisation zu einem Kanal im Netz hinzugefügt werden.

  - Sie können einen neuen Kanal für den Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) automatisch einbinden.

  - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel) durchführt.

    Nachdem Ihre Organisation zu einem Kanal hinzugefügt wurde, müssen Sie das Signierzertifikat Ihres Peers zu dem Kanal hinzufügen, damit andere Mitglieder Ihre digitale Signatur während der Ausführung von Transaktionen überprüfen können. Der Peer lädt sein Signierzertifikat während der Installation hoch, sodass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beitreten soll, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert.

1. Rufen Sie die Konfigurationsinformationen aus Ihrem `Verbindungsprofil` ab, das in der Network Monitor-Anzeige "Übersicht" verfügbar ist. Klicken Sie auf **Verbindungsprofil** und dann auf **Download**.

  - Suchen Sie die URL des Anordnungsknotens, indem Sie nach **orderers** suchen. Das Element befindet sich unter `orderers > url`. Notieren Sie die URL, die mit dem Netznamen beginnt. Daraufhin wird die URL wie im folgenden Beispiel angezeigt:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Suchen Sie den Namen Ihrer Organisation, indem Sie bei der Suche **organizations** angeben. Hierbei sollte es sich um dieselbe Organisation handeln, die Sie zum Registrieren Ihres Peers verwenden. Der Name Ihrer Organisation wird mit dem zugehörigen Wert für `mspid` angezeigt. Notieren Sie den Wert für `mspid`.

2. Suchen Sie im AWS-EC2-Dashboard für den öffentlichen DNS (IPv4) nach den Endpunktinformationenen Ihres AWS-VPC-Peers (also nach der öffentlichen IPv4-Adresse des DNS). Dabei handelt es sich um eine URL ähnlich der folgenden:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  Erstellen Sie unter Verwendung dieser Informationen die Umgebungsvariable `PEERADDR`, indem Sie die Adresse mit Port 7051 verknüpfen. Der Wert für `PEERADDR` sieht daher etwa wie folgt aus:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. Führen Sie die folgenden Befehle aus, um die Umgebungsvariablen im Fabric-Container für Tools festzulegen.

    ```
    export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
    ```
    {:codeblock}

    Ersetzen Sie die Feldinhalte durch Ihre eigenen Angaben.
      - Ersetzen Sie `<ORDERER_URL>` durch den Hostnamen und den Port des Anordnungsknotens aus der Datei `creds.json`.
      - Ersetzen Sie `<CHANNEL_NAME>` durch den Namen des Kanals, an dem der Peer teilnimmt.
      - Ersetzen Sie `<CC_NAME>` durch einen Namen, der auf Ihren Chaincode verweist.
      - Ersetzen Sie `<ORGANIZATION_MSP_ID>` durch den Namen der Organisation aus der Datei `creds.json`.
      - Ersetzen Sie `<PEER_ADDR>` durch den Wert, den Sie im vorherigen Schritt erstellt haben.  

  Beispiel:

    ```
    export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
    export CHANNEL=defaultchannel
    export CC_NAME=mycc
    export ORGID=PeerOrg1
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. Rufen Sie den Genesis-Block des Kanals ab, um das Kanalledger Ihres Peers zu erstellen. Wechseln Sie zunächst mit dem Befehl `cd` ins Stammverzeichnis und führen Sie dann den Befehl zum Abrufen des Genesis-Blocks aus.

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
  ```
  {:codeblock}

  **Hinweis:** Möglicherweise werden Sie bei der Ausführung eines dieser CLI-Befehle feststellen, dass die folgende Warnung angezeigt wird, die jedoch ignoriert werden kann.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Genesis-Block erfolgreich zum Peer-Container hinzugefügt wurde:

  ```
  ls *.block
  ```
  {:codeblock}

  Wenn der Genesis-Block erfolgreich hinzugefügt wurde, sieht die Ausgabe etwa wie folgenden Beispiel aus:
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. Fügen Sie jetzt den Peer dem Kanal hinzu, indem Sie den Genesis-Block mit dem folgenden Befehl übergeben:

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric-Container für Tools zum Installieren des Chaincodes auf dem Peer verwenden
{: #aws-toolcontainer-install-cc}

Nun sind Sie bereit zur Installation und Instanziierung des Chaincodes auf dem Peer. In den folgenden Anweisungen wird der Chaincode `fabcar` aus dem Hyperledger-Repository `fabric-samples` installiert, das Sie bereits auf Ihre lokale Maschine heruntergeladen haben sollten, wenn Sie den [Fabric-Container für Tools einrichten](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-cli-operate).  

Führen Sie den folgenden Peer-CLI-Befehl aus, um den `fabcar`-Chaincode auf dem Peer zu installieren.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric-Container für Tools zum Instanziieren des Chaincodes auf einem Kanal verwenden
{: #remote-peer-aws-operate-oolcontainer-instantiate-cc}

Da nur ein Peer den Chaincode auf einem Kanal instanziieren muss, müssen Sie den Peer nicht zum Instanziieren des Chaincodes verwenden. Dieser Arbeitsschritt kann von Peers ausgeführt werden, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Wenn jedoch der Peer den Chaincode auf dem Kanal instanziieren soll, können Sie den folgenden Befehl im Fabric-Container für Tools ausführen:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## In AWS ausgeführten Peer erneut starten
{: #remote-peer-aws-operate-restart}

In der Umgebung, in der Sie Ihren Peer bereitstellen, können Sie ihn starten, stoppen oder erneut starten. Wenn ein Zertifikat zu dem Peer hinzugefügt wird, dann muss der Peer neu gestartet werden, damit das neue Zertifikat erkannt werden kann. Hierfür gibt es mehrere Verfahren:

- Starten Sie in der AWS-Konsole die VPC-Instanz für den Peer erneut.
- Führen Sie im Peer-Container den folgenden Befehl aus:

 ```
 docker restart peer
 ```
 {:codeblock}  

Darüber hinaus können Sie die [HEAD-Anforderung](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-nodes) verwenden, um die Verfügbarkeit Ihres Peers zu überprüfen.

## Protokolle des Peers anzeigen

Stellen Sie mit SSH eine Verbindung zur AWS-VPC-Instanz für den Peer her und führen Sie den folgenden Befehl aus, um die Protokolle des Peers anzuzeigen:

```
docker logs peer
```
{:codeblock}

## Chaincode aktualisieren
{: #remote-peer-aws-operate-update-cc}

Im Zeitverlauf muss der Chaincode, der auf dem Peer ausgeführt wird, möglicherweise geändert werden. Da der Chaincode auf den Peers installiert ist und auf dem Kanal instanziiert wird, müssen Sie den Chaincode auf allen Peers des Kanals aktualisieren.

Führen Sie die folgenden Schritte aus, um den Chaincode zu aktualisieren:

1. Zum Aktualisieren des Chaincodes auf den einzelnen Peers in AWS müssen Sie einfach den Prozess erneut ausführen, den Sie zum Installieren des Chaincodes auf den Peers verwendet haben. Dazu können Sie entweder eine Clientanwendung oder einen CLI-Befehl benutzen. Beachten Sie hierbei unbedingt, dass der gleiche Chaincode-Name verwendet werden muss, der ursprünglich verwendet wurde. Allerdings muss dabei die `Versionsnummer` des Chaincodes erhöht werden.

2. Nach der Installation des neuen Chaincodes auf allen Peers des Kanals können Sie mit dem Network Monitor oder dem Befehl für ein [Upgrade des Chaincodes des Peers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) den Kanal aktualisieren, sodass der neue Chaincode verwendet wird.

Weitere Informationen zur Verwendung der Network Monitor-Anzeige "Code installieren" zur Aktualisierung des Chaincodes auf dem Kanal finden Sie in Schritt 2 dieser [Anweisungen](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc).

## Fehlerbehebung
{: #remote-peer-aws-operate-troubleshooting}

### **Problem:** Ferner Peer kann keine Verbindung zu Blockchain-Netz herstellen
{: #remote-peer-aws-operate-problem-1}

Der Stack wird erfolgreich erstellt, aber Docker-Protokolle enthalten den folgenden Fehler:

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

**Lösung:**  
Dieser Fehler kann dadurch verursacht werden, dass bei der Bereitstellung der Schnelleinstiegsvorlage kein Port in der URL für die Zertifizierungsstelle angegeben wurde.
Die URL der Zertifizierungsstelle sollte etwa folgendermaßen aussehen: `https://<network>-org1-ca.stage.blockchain.ibm.com:31011`.
Stellen Sie die Schnelleinstiegsvorlage erneut bereit und achten Sie darauf, den richtigen Wert als URL für die Zertifizierungsstelle anzugeben.

### **Problem:** Instanziieren von Chaincode schlägt mit Fehler fehl
{: #remote-peer-aws-operate-problem-2}

Die Instanziierung des Chaincodes schlägt mit dem folgenden Fehler fehl:
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**Lösung:**   
Stellen Sie sicher, dass nach dem Hochladen des Administratorzertifikats in Network Monitor die Zertifikate auf dem Kanal synchronisiert werden. Weitere Informationen enthält Schritt 5 der vorliegenden [Anweisungen](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate-manage-certs). Denken Sie daran, dass das Kanalzertifikat unbedingt synchronisiert werden muss, bevor der Peer dem Kanal beitritt.

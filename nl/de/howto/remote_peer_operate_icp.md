---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Ferne Peers in {{site.data.keyword.cloud_notm}} Private betreiben
{: #remote-peer-operate}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Nach der Einrichtung von fernen Peers unter {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private (ICP) müssen Sie verschiedene operative Schritte ausführen, bevor Ihr Peer Transaktionen zum Abfragen und Aufrufen des Ledgers für das Blockchain-Netz absetzen kann. Die Schritte umfassen das Hinzufügen Ihrer Organisation zu einem Kanal, das Hinzufügen Ihres fernen Peers zum Kanal, das Installieren des Chaincodes auf dem fernen Peer, die Instanziierung des Chaincodes auf dem Kanal und die Verbindung von Anwendungen mit dem fernen Peer. {:shortdesc}

Sie müssen einige vorausgesetzte Schritte über den ICP-Cluster ausführen, um Ihren fernen Peer betreiben zu können.

**Voraussetzungen:**
- [Abrufen der Endpunktinformationen Ihres fernen Peers](#remote-peer-retrieve-endpoint-info)
- [Anmelden bei Ihrem Peer-Container mithilfe von kubectl](#remote-peer-kubectl-configure)
- [Durchführen eines Neustarts Ihres fernen Peers](#remote-peer-restart)

Zum Betreiben Ihres fernen Peers können Sie eine der folgenden Methoden verwenden.

**Operationspfade:**
- [Verwenden der Fabric-SDKs](#remote-peer-operate-with-sdk)
- [Verwenden der Befehlszeile](#remote-peer-cli-operate)

Die Fabric-SDKs stellen den empfohlenen Pfad dar, wobei in den Anweisungen davon ausgegangen wird, dass Sie bereits mit dem Betrieb des SDK vertraut sind.

Es wird empfohlen, dass Sie mindestens zwei Instanzen des Helm-Diagramms für den fernen Peer bereitstellen, um [Hochverfügbarkeit](/docs/services/blockchain/howto/remote_peer_icp.html#high-availability) zu erreichen. Aus diesem Grund müssen Sie diese operativen Schritte einmal pro Peer ausführen. Wenn Sie bereit sind zum Aufrufen und Abfragen des Chaincodes über Ihre Anwendung, dann stellen Sie für beide Peers eine Verbindung her, um sicherzustellen, dass Ihre [Anwendungen hoch verfügbar](/docs/services/blockchain/v10_application.html#ha-app) sind.

**Hinweis**: Ein ferner Peer unter {{site.data.keyword.blockchainfull_notm}} verfügt nicht über Zugriff auf die vollständige Funktionalität oder Unterstützung von Peers, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Demzufolge können Sie den Network Monitor nicht zum Betreiben eines fernen Peers verwenden. Bevor Sie mit der Ausführung ferner Peers beginnen, müssen Sie sich mit den [wichtigen Hinweisen](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations) vertraut machen.

## Voraussetzungen

Unabhängig davon, ob Sie SDKs oder die Befehlszeile verwenden wollen, müssen Sie die Schritte in diesem Abschnitt während des Betriebs Ihres Peers ausführen. Sie können die ersten beiden Schritte (Peerendpunkt abrufen und kubectl konfigurieren) ausführen, bevor Sie beginnen.

### Endpunktinformationen des fernen Peers abrufen
{: #remote-peer-retrieve-endpoint-info}

Melden Sie sich bei der ICP-Konsole an. Klicken Sie auf das Symbol **Menü** in der oberen linken Ecke. Klicken Sie auf **Workload** > **Bereitstellungen**. Suchen Sie den Namen Ihrer Instanz des fernen Peers und klicken Sie darauf, um die Anzeige mit der Bereitstellungsübersicht zu öffnen. Blättern Sie nach unten bis zum Abschnitt **Details zugänglich machen** und suchen Sie die `Ingress-IP` und die `Ports` Ihres fernen Peers.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

Notieren Sie die Ingress-IP-Adresse und die Note-Ports, um eine Verbindung zu Ihrem fernen Peer herzustellen. Sie benötigen diese Informationen zur Installation und Instanziierung des Chaincodes auf dem fernen Peer. Im folgenden Beispiel lautet die Peeradresse entweder `9.46.126.89:30162`, `9.46.126.89:30260` oder `9.46.126.89:32051`.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### kubectl-Befehlszeilenschnittstelle konfigurieren
{: #remote-peer-kubectl-configure}

Mit dem Befehlszeilentool **kubectl** können Sie verschiedene erforderliche Operationen im Peer-Container durchführen, der in ICP ausgeführt wird. Melden Sie sich bei Ihrem Cluster mithilfe des {{site.data.keyword.cloud_notm}} Private-CLI-Tools an:

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

Melden Sie sich dann beim Dashboard Ihres ICP-Clusters an. Klicken Sie in der oberen rechten Ecke der ICP-Benutzerschnittstelle auf Ihren Benutzeravatar und dann auf **Client konfigurieren**. Kopieren Sie die Befehle, fügen Sie sie im Terminalfenster auf Ihrem lokalen System ein, und führen Sie sie aus. Sie können überprüfen, ob die Konfiguration erfolgreich ausgeführt wurde, indem Sie den folgenden Befehl ausführen:

```
kubectl -n <namespace> get pods
```
{:codeblock}

Hierbei muss `-n <namespace>` angegeben werden, wenn nicht der Namensbereich `default` verwendet wird. Ersetzen Sie `<namespace>` durch den Wert Ihres Namensbereichs im ICP-Cluster.


Der Befehl gibt die im folgenden Beispiel dargestellte Antwort zurück. Dabei stellt `remotepeer-bd65c4769-95rmm` den Namen des Peer-Containers dar.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

Wenn der Podname Ihres fernen Peers in der Antwort nicht aufgeführt ist, dann können Sie ihn in der Anzeige **Pods** der Bereitstellungsanzeige suchen.

Verwenden Sie den Befehl "exec", um den Container des fernen Peers aufzurufen, indem Sie den folgenden Befehl eingeben und dabei den Wert für `<PEER_CONTAINER_NAME>` durch den Namen des Pods oben ersetzen.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
Hierbei muss `-n <namespace>` angegeben werden, wenn nicht der Namensbereich `default` verwendet wird. Ersetzen Sie `<namespace>` durch den Wert Ihres Namensbereichs im ICP-Cluster.

Beispiel:

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

Nun sind Sie bereit zur Verwendung der Befehlszeilenschnittstelle des Peer-Containers. Sie müssen die Befehlszeilenschnittstelle zum Herunterladen des TLS-Zertifikats des Peers und zum Hochladen Ihres Signierzertifikats auf den Peer verwenden, wenn Sie mit den SDKs oder der Befehlszeile arbeiten.

## Neustart eines in ICP ausgeführten fernen Peers durchführen
{: #remote-peer-restart}

Wenn ein Zertifikat zu dem fernen Peer hinzugefügt wird, dann muss der Peerknoten neu gestartet werden, damit das neue Zertifikat erkannt werden kann.

Mit den **kubectl**-Befehlen können Sie den fernen Peer, der unter ICP ausgeführt wird, neu starten. Im Befehl müssen der Name für den **Pod** des fernen Peers und der **Container** als Befehlsparameter angegeben werden. Weitere Informationen zur Verwendung der kubectl-Befehle finden Sie im Abschnitt zum [Konfigurieren der kubectl-Befehlszeilenschnittstelle](#remote-peer-kubectl-configure).

1. Suchen Sie in der ICP-Konsole nach dem Namen für den **Pod** und dem Namen für den **Container** für die Bereitstellung des fernen Peers.

  1. Klicken Sie auf das Menüsymbol in der oberen linken Ecke der Konsole und anschließend auf **Workloads > Bereitstellungen**.
  2. Suchen Sie das Release des fernen Peers in der Tabelle und klicken Sie darauf, um das Element zu öffnen.
  3. Blättern Sie nach unten, um den zugehörigen Namen für den **Pod** anzuzeigen. Notieren Sie den Namen für den **Pod**.
  4. Klicken Sie auf den Pod, um ihn zu öffnen.
  5. Klicken Sie auf die Registerkarte **Container**. Notieren Sie den Namen für den **Container** Ihres fernen Peers.

2. Führen Sie in der Befehlszeile den folgenden Befehl aus, um für den Peer einen Neustart durchzuführen. Ersetzen Sie dabei `<REMOTE_PEER_POD_NAME>` und `<REMOTE_PEER_CONTAINER_NAME>` durch die oben angegebenen Werte.

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. Sie können überprüfen, ob der ferne Peer neu gestartet wurde, indem Sie den Befehl `kubectl get pods` ausführen und die Ausgabe auf den Zähler für **RESTART** Ihres Pods überprüfen.

Darüber hinaus können Sie die [HEAD-Anforderung](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes) verwenden, um die Verfügbarkeit Ihres fernen Peers zu überprüfen.


## Fabric-SDKs zum Betrieb des fernen Peers verwenden
{: #remote-peer-operate-with-sdk}

Die Hyperledger Fabric-SDKs stellen eine leistungsstarke Gruppe von APIs zur Verfügung, die Anwendungen die Interaktion mit und den Betrieb von Blockchain-Netzen ermöglichen. Die aktuellste Version der Liste der unterstützten Sprachen und die vollständige Liste der verfügbaren APIs in den Fabric-SDKs finden Sie in der [Hyperledger Fabric SDK Community-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community-Dokumentation"). Sie können die Fabric-SDKs verwenden, um Ihren fernen Peer zu einem Kanal unter {{site.data.keyword.blockchainfull_notm}} Platform hinzuzufügen, einen Chaincode auf dem Peer zu installieren und den Chaincode auf einem Kanal zu instanziieren.

Die folgenden Anweisungen verwenden das [Fabric Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Fabric Node-SDK"), um den fernen Peer zu betreiben. Dabei wird davon ausgegangen, dass Sie bereits über Kenntnisse zu dem SDK verfügen. Sie können das [Lernprogramm 'Anwendungen entwickeln'](/docs/services/blockchain/v10_application.html) benutzen, um sich mit der Vorgehensweise zur Verwendung des Node-SDK vertraut zu machen. Außerdem dient es als Leitfaden für die Entwicklung von Anwendungen mit dem fernen Peer, wenn Sie bereit sind zum Aufrufen und Abfragen des Chaincodes.

### Node-SDK installieren

Sie können NPM verwenden, um das [Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Node-SDK") zu installieren:
```
npm install fabric-client@1.1
```
{:codeblock}

Hierbei wird die Verwendung von Version 1.1 des Node-SDK empfohlen.

### SDK zur Nutzung mit dem fernen Peer vorbereiten
{: #remote-peer-node-sdk}

Bevor Sie das SDK zum Betreiben des fernen Peers benutzen können, müssen Sie die erforderlichen Zertifikate (Eintragung) generieren, auf deren Basis Ihre Anwendung mit dem Netz unter {{site.data.keyword.blockchainfull_notm}} Platform und dem fernen Peer kommunizieren kann. Führen Sie die Schritte für die [Eintragung beim SDK](/docs/services/blockchain/v10_application.html#enroll-app-sdk) aus und verwenden Sie dabei Ihre Identität **admin**. Das Lernprogramm [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html) wird ebenfalls unter **admin** eingetragen, sodass der Beispielcode nicht geändert werden muss.

### signCert-Zertifikat auf {{site.data.keyword.blockchainfull_notm}} Platform hochladen
{: #remote-peer-upload-sdk}

Sie müssen das SDK-Signierzertifikat in das Netz unter {{site.data.keyword.blockchainfull_notm}} Platform hochladen, sodass Ihre Anwendung die Berechtigung für den Betrieb des Netzes erhält.

- Ihr Signierzertifikat befindet sich in dem Verzeichnis, in dem das SDK die Verschlüsselungsinformationen in einer Datei mit dem Namen "admin" generiert hat. Kopieren Sie das Zertifikat, das in Anführungszeichen angegeben ist, an die Position nach dem Feld `certificate`. Das entsprechende Element beginnt mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` und endet mit der Zeichenfolge `-----END CERTIFICATE-----`. Sie können die Befehlszeilenschnittstelle verwenden, um das Zertifikat ins PEM-Format umzuwandeln, indem Sie den Befehl `echo -e "<CERT>" > admin.pem` absetzen. Anschließend können Sie die Inhalte des Zertifikats mithilfe des Network Monitor in das Blockchain-Netz einfügen. Melden Sie sich beim Netz unter {{site.data.keyword.blockchainfull_notm}} Platform an. Klicken Sie in der Anzeige "Mitglieder" des Network Monitor auf **Zertifikate** > **Zertifikate hinzufügen**. Geben Sie für Ihr Zertifikat einen beliebigen Namen an und fügen Sie die Inhalte in das Feld für das **Zertifikat** ein. Klicken Sie auf **Erneut starten**, wenn Sie dazu aufgefordert werden anzugeben, ob die Peers erneut gestartet werden sollen. Diese Aktion muss ausgeführt werden, bevor Ihre Organisation dem Kanal beitritt.

### signCert-Zertifikat auf fernen Peer hochladen
{: #remote-peer-upload-signcert}

Sie müssen auch das Signierzertifikat des SDK auf den fernen Peer hochladen und dann einen Neustart durchführen. Sie müssen im Container des fernen Peers dasselbe Signierzertifikat installieren, das Sie auf [{{site.data.keyword.blockchainfull_notm}} Platform hochgeladen](#remote-peer-upload-sdk) haben.

Führen Sie die folgenden Befehle [innerhalb des Peer-Containers](#remote-peer-kubectl-configure) aus, um Ihr Zertifikat hochzuladen.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

Ersetzen Sie den Text `<CERT.PEM>` durch Ihr signCert-Zertifikat. Das entsprechende Element beginnt mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` und endet mit der Zeichenfolge `-----END CERTIFICATE-----`. **Beachten Sie** hierbei, dass in diesem Verzeichnis bereits eine Datei mit dem Namen "cert.pem" vorhanden ist. Ordnen Sie dem neuen Zertifikat den Namen `cert2.pem` oder einen ähnlichen Namen zu, um die Überschreibung des bereits im Container befindlichen Zertifikats zu vermeiden.

Da Sie ein neues Zertifikat hinzugefügt haben, müssen Sie für den Container einen Neustart durchführen, damit der Peer das Zertifikat erkennt. Führen Sie die folgenden [Anweisungen](#remote-peer-restart) aus, um für den Peer einen Neustart durchzuführen.

### TLS-Zertifikat des fernen Peers an SDK übergeben
{: #icp-remote-peer-download-tlscert}

Sie müssen die Inhalte des TLS-Zertifikats `cacert.pem` aus dem Peer-Container in Ihre Anwendung kopieren, um die Kommunikation des SDK zu authentifizieren. Führen Sie im [CLI-Container des Peers](#remote-peer-kubectl-configure) den folgenden Befehl aus:
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

Kopieren Sie den gesamten Text der Datei `cacert.pem` in die Zwischenablage. Der Text beginnt mit dem ersten Vorkommen von `-----BEGIN CERTIFICATE-----` und endet mit dem letzten Vorkommen von `-----END CERTIFICATE-----`. Speichern Sie dieses Zertifikat im PEM-Format auf dem lokalen Dateisystem. Anschließend können Sie das TLS-Zertifikat in Ihre Anwendung importieren, indem Sie einen einfachen Dateilesebefehl benutzen.
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### Endpunktinformationen des fernen Peers für SDK bereitstellen
{: #remote-peer-SDK-endpoints}

Suchen Sie die [Endpunktinformationen Ihres fernen Peers](#remote-peer-retrieve-endpoint-info) und stellen Sie diese Informationen für das SDK bereit, indem Sie eine neue Peervariable deklarieren oder das Verbindungsprofil aktualisieren. Im folgenden Beispiel wird der Peer als Endpunkt im Fabric-Netz definiert und es wird das von Ihnen importierte TLS-Zertifikat an den Peer übergeben.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

Sie müssen für `ssl-target-name-override` den Wert `<something>.blockchain.com` angeben, damit der Peer die DNS-Anforderung auflösen kann.

**Hinweis:** Da es sich bei dem Peer um eine ferne Einheit handelt, können andere Organisationen im {{site.data.keyword.blockchainfull_notm}} Platform-Netz die Endpunktinformationen Ihres Peers nicht in ihrem Verbindungsprofil finden. Wenn andere Organisationen Ihrem fernen Peer eine zu bewilligende Transaktion senden müssen, müssen Sie die öffentliche IP und die TLS-Zertifikate in einer Out-of-band-Operation bereitstellen.

### SDK zum Beitreten zu einem Kanal verwenden
{: #remote-peer-join-channel-sdk}

Als Mitglied des Blockchain-Netzes muss Ihre Organisation zu einem Kanal im Netz hinzugefügt werden, bevor der ferne Peer dem Kanal beitreten kann.

  - Sie können einen neuen Kanal für den fernen Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#creating-a-channel) automatisch einbinden. Beachten Sie hierbei, dass Sie über mindestens einen Peer unter {{site.data.keyword.blockchainfull_notm}} Platform verfügen müssen, um einen Kanal im Network Monitor zu erstellen.  

  - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#updating-a-channel) durchführt. Ein Mitglied des Kanals mit Peers unter {{site.data.keyword.blockchainfull_notm}} Platform kann den Network Monitor verwenden, um Ihre Organisation zum Kanal hinzuzufügen, auch wenn Sie auf der Plattform keine Peers hosten.

    Nachdem Ihre Organisation zu einem Kanal hinzugefügt wurde, müssen Sie das Signierzertifikat Ihres Peers zu dem Kanal hinzufügen, damit andere Mitglieder Ihre digitale Signatur während der Ausführung von Transaktionen überprüfen können. Der ferne Peer lädt sein Signierzertifikat während der Installation hoch, sodass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beitreten soll, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert.

Wenn Ihre Organisation Teil des Kanals ist, dann führen Sie die Anweisungen aus, die im Abschnitt zum [Beitreten zu einem Kanal](/docs/services/blockchain/v10_application.html#join-channel-sdk) aufgeführt sind. Sie müssen die URL des Anordnungsservice und den Kanalnamen angeben.

### SDK zum Installieren des Chaincodes auf dem Peer verwenden
{: #remote-peer-install-cc-sdk}

Führen Sie die folgenden Anweisungen aus, um das SDK zum [Installieren eines Chaincodes](/docs/services/blockchain/v10_application.html#install-cc-sdk) auf Ihrem fernen Peer zu verwenden.

### SDK zur Instanziierung des Chaincodes auf dem Kanal verwenden
{: #remote-peer-instantiate-cc-sdk}

Nur ein Mitglied des Kanals muss den Chaincode instanziieren oder aktualisieren. Aus diesem Grund kann jedes Netzmitglied auf dem Kanal, das über Peers unter {{site.data.keyword.blockchainfull_notm}} Platform verfügt, den Network Monitor zum Instanziieren des Chaincodes und zur Angabe der Bewilligungsrichtlinien verwenden. Wenn Sie den fernen Peer allerdings zum Instanziieren des Chaincodes auf einem Kanal verwenden wollen, dann können Sie das SDK benutzen und die Anweisungen im Abschnitt zum [Instanziieren eines Chaincodes](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk) befolgen.


## Befehlszeilenschnittstelle zum Betrieb des fernen Peers verwenden
{: #remote-peer-cli-operate}

Sie können den fernen Peer auch über die Befehlszeile betreiben, indem Sie den Fabric-CA-Client und den Fabric-Container für Tools verwenden. In diesen Anweisungen werden zuerst mithilfe des Fabric-CA-Clients die erforderlichen Zertifikate generiert. Anschließend wird der Fabric-Container für Tools verwendet, um den Peer zu betreiben. Dazu wird der Peer zu einem Kanal hinzugefügt, es wird ein Chaincode installiert und dann der Chaincode auf einem Kanal instanziiert.

### Eintragung unter Verwendung des Fabric-CA-Clients durchführen
{: #peer-client-enroll}

Der erste Schritt umfasst das Generieren der erforderlichen Zertifikate (Eintragung) mithilfe des Fabric-CA-Clients. Sie müssen zuerst den Fabric-CA-Client installieren. Laden Sie die [fabric-ca-Binärkomponenten der Version 1.1.0 für Ihre Plattform](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) auf Ihr lokales System herunter und extrahieren Sie sie. Verschieben Sie sie in einen Ordner wie z. B. `$HOME/fabric-ca-remote/`.

1.  Bereiten Sie Ihre Umgebung zur Verwendung des Fabric-CA-Clients vor. Wechseln Sie in das Verzeichnis, in das Sie die Clientbinärkomponenten verschoben haben, sodass Sie in Ihren Befehlen direkt darauf verweisen können.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Legen Sie den Pfad fest, in dem der Client Ihre Schlüssel erstellen soll. Wenn Sie den Befehl `enroll` zuvor noch nicht ausgeführt haben, dann sind der Ordner `msp` und die Datei `.yaml` noch nicht vorhanden. Wenn Sie den Befehl `enroll` zuvor bereits einmal ausgeführt haben, ist es wichtig, dass Sie das gesamte Konfigurationsmaterial früherer Eintragungsversuche mit den folgenden `rm`-Befehlen löschen:
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
{: #manage-certs}

Bevor Sie einen fernen Peer betreiben können, müssen verschiedene Managementschritte für die Zertifikate auf dem lokalen System ausgeführt werden. Einige der Zertifikate, die vom Fabric-CA-Client generiert wurden, müssen auf {{site.data.keyword.blockchainfull_notm}} Platform und den Peer hochgeladen werden. Außerdem müssen die TLS-Zertifikate von Platform und dem Peer heruntergeladen werden. Wenn Sie weitere Informationen zu den Zertifikaten, mit denen Sie arbeiten werden, und zu den Tasks benötigen, die auszuführen sind, dann lesen Sie den Abschnitt zum [Verwalten von Zertifikaten unter {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

Öffnen Sie auf dem lokalen System ein Command Terminal und navigieren Sie zu dem Verzeichnis, in dem die Binärkomponenten des Fabric-CA-Clients und der MSP-Ordner abgelegt wurden.
1. Kopieren Sie die Datei `cert.pem` aus dem Ordner `signcerts` in einen neuen Ordner `admincerts`.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. Außerdem müssen Sie das TLS-Zertifikat des fernen Peers aus dem Peer-Container unter ICP auf das lokale System kopieren.

  - [Melden Sie sich beim Peer-Container an](#remote-peer-kubectl-configure) und führen Sie den folgenden Befehl aus:
    ```
    cat /mnt/certs/tls/cacert.pem
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

3. Um Ihrer Befehlszeilenschnittstelle die Berechtigung zur Installation des Chaincodes auf dem Peer zu erteilen, müssen Sie das signCert-Zertifikat, das vom Fabric-CA-Client generiert wurde, in den Administratorordner (admin) der Peers hochladen und für den Peer einen Neustart durchführen. Kopieren Sie deshalb das Zertifikat `admincert/cert.pem` unter dem Namen `cert2.pem` vom lokalen System in das Verzeichnis `/mnt/crypto/peer/peer/msp/admincerts/` im Peer-Container.

    - Führen Sie auf dem lokalen System die folgenden Befehle aus:

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Kopieren Sie den Text der Datei `cert.pem` in die Zwischenablage. Das entsprechende Element beginnt mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` und endet mit der Zeichenfolge `-----END CERTIFICATE-----`.

    - Wechseln Sie wieder zum Peer-Container und führen Sie die folgenden Befehle aus, um die Datei "cert.pem" zu den Peerzertifikaten hinzuzufügen. Ersetzen Sie dabei den Text `<CERT.PEM>` durch den Text aus der Zwischenablage.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Hinweis:** In diesem Verzeichnis ist bereits eine Datei mit dem Namen "cert.pem" vorhanden. Die Datei darf nicht überschrieben werden.

    - Da Sie ein neues Zertifikat hinzugefügt haben, müssen Sie für den Container einen Neustart durchführen, damit der Peer das Zertifikat erkennt. Führen Sie die folgenden [Anweisungen](#remote-peer-restart) aus, um für den Peer einen Neustart durchzuführen.

4. Sie müssen dieses Zertifikat `admincert/cert.pem` vom lokalen System auf {{site.data.keyword.blockchainfull_notm}} Platform hochladen, damit die ferne Befehlszeilenschnittstelle oder Anwendung die Kanaloperationen (z. B. Abrufen des Genesis-Blocks des Kanals und Instanziieren des Chaincodes) durchführen kann.
    1. Schneiden Sie auf dem lokalen System die Datei `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` aus und kopieren Sie sie in die Zwischenablage.
    2. Rufen Sie den Network Monitor Ihres Netzes unter {{site.data.keyword.blockchainfull_notm}} Platform auf.
    3. Klicken Sie im linken Navigator auf **Mitglieder** und dann auf die Registerkarte **Zertifikate**.
    4. Klicken Sie auf die Schaltfläche **Zertifikat hinzufügen**.
    5. Ordnen Sie im Fenster **Zertifikat hinzufügen** Ihrem Zertifikat einen Namen (z. B. `fabrictools.pem`) zu, fügen Sie das soeben kopierte Zertifikat in die Zwischenablage ein und klicken Sie dann auf **Abschicken**.
    6. Sie werden aufgefordert anzugeben, ob die Peers neu gestartet werden sollen. Klicken Sie auf **Erneut starten**.
    7. Klicken Sie im linken Navigator auf **Kanäle** und suchen Sie den Kanal, dem der ferne Peer beitreten soll.
    8. Klicken Sie auf die drei Punkte unter der Überschrift **Aktionen** und dann auf **Zertifikat synchronisieren**. Klicken Sie im Fenster **Zertifikat synchronisieren** auf **Abschicken**.

    **Hinweis**: Die Synchronisation des Kanalzertifikats vor dem Beitritt des Peers zum Kanal ist wichtig.

### Fabric-Container für Tools einrichten

Nachdem alle Zertifikate an die erforderliche Position verschoben wurden, sind Sie bereit, den Fabric-Container für Tools aus Docker zu installieren und zu verwenden. Auch im vorliegenden Fall müssen die Befehle in dem Verzeichnis ausgeführt werden, in dem der MSP-Ordner gespeichert wurde.

1.  Laden Sie das Docker-Image der Fabric-Tools mit dem folgenden Befehl herunter:
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  Führen Sie die folgenden Befehle aus, um den Fabric-Container für Tools zu starten.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    Mit diesem Befehl wird das MSP-Verzeichnis in Ihrem Container mit den Tools angehängt.

3.  Die Adresse des fernen Peers muss in einen Domänennamen mit dem Suffix `blockchain.com` aufgelöst werden. Hierzu können Sie entweder den DNS verwenden oder die Datei "/etc/hosts" bearbeiten. Für die vorliegenden Anweisungen ändern Sie die Datei `/etc/hosts` im Fabric-Container für Tools. Melden Sie sich an der ICP-Konsole an und suchen Sie die [Endpunktinformationen](#remote-peer-retrieve-endpoint-info) der fernen Peers. Sobald Sie die Angabe für `INGRESS_IP` Ihrer Peers lokalisiert haben, führen Sie den folgenden Befehl mit der Adresse Ihres fernen Peers aus.
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Fabric-Container für Tools für Beitritt des fernen Peers zum Kanal verwenden
{: #icp-cli-join-peer-to-channel}

Bevor Sie die CLI-Befehle für den Beitritt des fernen Peers zu einem Kanal ausführen können, muss Ihre Organisation zu einem Kanal im Netz hinzugefügt werden.

  - Sie können einen neuen Kanal für den fernen Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#creating-a-channel) automatisch einbinden. Beachten Sie hierbei, dass Sie über mindestens einen Peer unter {{site.data.keyword.blockchainfull_notm}} Platform verfügen müssen, um einen Kanal im Network Monitor zu erstellen.  

  - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#updating-a-channel) durchführt. Ein Mitglied des Kanals mit Peers unter {{site.data.keyword.blockchainfull_notm}} Platform kann den Network Monitor verwenden, um Ihre Organisation zum Kanal hinzuzufügen, auch wenn Sie auf der Plattform keine Peers hosten.

    Nachdem Ihre Organisation zu einem Kanal hinzugefügt wurde, müssen Sie das Signierzertifikat Ihres Peers zu dem Kanal hinzufügen, damit andere Mitglieder Ihre digitale Signatur während der Ausführung von Transaktionen überprüfen können. Der ferne Peer lädt sein Signierzertifikat während der Installation hoch, sodass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beitreten soll, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert.

1. Rufen Sie die Konfigurationsinformationen aus der Datei `creds.json` ab, die in der Anzeige "Übersicht" des Network Monitor verfügbar ist. Klicken Sie auf **Verbindungsprofil** und dann auf **Download**.

  - Suchen Sie die URL des Anordnungsknotens, indem Sie nach **orderers** suchen. Das Element befindet sich unter `orderers > url`. Notieren Sie die URL, die mit dem Netznamen beginnt. Daraufhin wird die URL wie im folgenden Beispiel angezeigt:
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Suchen Sie den Namen Ihrer Organisation, indem Sie bei der Suche **organizations** angeben. Hierbei sollte es sich um dieselbe Organisation handeln, die Sie zum Registrieren Ihres fernen Peers verwenden. Der Name Ihrer Organisation wird mit dem zugehörigen Wert für `mspid` angezeigt. Notieren Sie den Wert für `mspid`.

2. [Suchen Sie die Endpunktinformationen der fernen Peers](#remote-peer-retrieve-endpoint-info) und notieren Sie dieses Mal die `Ports`, die neben der `Ingress-IP` aufgelistet werden.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  Notieren Sie die Ports im 30-K-Bereich. Hierbei handelt es sich um die adressierbaren Ports aus dem Fabric-Container für Tools, die zum Setzen der Umgebungsvariablen `PEERADDR` verwendet werden.

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  Demzufolge wird das verwendete Element `PEERADDR` mit dem Wert erstellt, der in der Datei `/etc/hosts` zusammen mit dem Port mit der Bindung an `7051` angegeben wurde. Im vorliegenden Beispiel lautet der Wert `30162`. Der resultierende Wert für `PEERADDR` lautet also `remotepeer.blockchain.com:30162`.

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
    - Ersetzen Sie `<CC_NAME>` durch einen Namen, mit dem auf Ihren Chaincode verwiesen wird.
    - Ersetzen Sie `<ORGANIZATION_MSP_ID>` durch den Namen der Organisation aus der Datei `creds.json`.
    - Ersetzen Sie `<PEER_ADDR>` durch den Wert, den Sie im vorherigen Schritt erstellt haben.

  Beispiel:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. Rufen Sie den Genesis-Block des Kanals ab, um das Kanalledger Ihres Peers zu erstellen. Wechseln Sie zunächst mit dem Befehl `cd` ins Stammverzeichnis und führen Sie dann den Befehl zum Abrufen des Genesis-Blocks aus.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
  ```
  {:codeblock}

  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Genesis-Block erfolgreich zum Peer-Container hinzugefügt wurde:

  ```
  ls *.block
  ```
  {:codeblock}

  Sie wissen, dass der Genesis-Block erfolgreich hinzugefügt wurde, wenn Sie die im folgenden Beispiel dargestellte Ausgabe erhalten:
  ```
  defaultchannel_0.block
  ```

5. Fügen Sie den Peer dem Kanal hinzu und verwenden Sie dazu den folgenden Befehl:

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Nachdem dieser Schritt erfolgreich ausgeführt wurde, werden die im folgenden Beispiel dargestellten Informationen angezeigt:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Fabric-Container für Tools zum Installieren des Chaincodes auf dem fernen Peer verwenden
{: #icp-toolcontainer-install-cc}
Nun sind Sie bereit zur Installation und Instanziierung des Chaincodes auf dem fernen Peer. In den folgenden Anweisungen wird der `fabcar`-Chaincode aus dem Repository `fabric-samples` installiert. Laden Sie den Chaincode für `fabric-samples` von `github.com` herunter und verwenden Sie dazu die folgenden Befehle:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

Führen Sie den folgenden Peer-CLI-Befehl aus, um den `fabcar`-Chaincode auf dem fernen Peer zu installieren.

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
{: #icp-toolcontainer-instantiate-cc}

Da nur ein Peer den Chaincode auf einem Kanal instanziieren muss, müssen Sie den fernen Peer nicht zum Instanziieren des Chaincodes verwenden. Dieser Arbeitsschritt kann von Peers ausgeführt werden, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Wenn jedoch der ferne Peer den Chaincode auf dem Kanal instanziieren soll, können Sie den folgenden Befehl im Fabric-Container für Tools ausführen:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Protokolle ferner Peers in {{site.data.keyword.cloud_notm}} Private anzeigen
{: #remote-peer-log-icp}

Über die ICP-Konsole können Sie auf die Protokolle ferner Peers zugreifen und diese Protokolle in der Kibana-Schnittstelle überprüfen.

1. Klicken Sie in der ICP-Konsole auf das Symbol **Menü** in der oberen linken Ecke. 
2. Klicken Sie in der Menüliste auf **Workloads** > **Helm-Releases**.
3. Klicken Sie in der Tabelle der Helm-Releases auf den Namen Ihres Helm-Release.
4. Blättern Sie abwärts bis zum Abschnitt **Pod** und klicken Sie dann auf den Link **Protokolle anzeigen** am Ende der Tabellenzeile. Die Protokolle der fernen Peers werden in der Kibana-Schnittstelle geöffnet.

## Chaincode aktualisieren

Im Zeitverlauf muss der Chaincode, der auf dem fernen Peer ausgeführt wird, möglicherweise geändert werden. Da der Chaincode auf den Peers installiert ist und auf dem Kanal instanziiert wird, müssen Sie den Chaincode auf allen Peers des Kanals aktualisieren.

Führen Sie die folgenden Schritte aus, um den Chaincode zu aktualisieren:

1. Zum Aktualisieren des Chaincodes auf den einzelnen fernen Peers müssen Sie einfach den Prozess erneut ausführen, den Sie zum Installieren des Chaincodes auf den Peers verwendet haben. Dazu können Sie entweder eine Clientanwendung oder einen CLI-Befehl benutzen. Beachten Sie hierbei unbedingt, dass der gleiche Chaincode-Name verwendet werden muss, der ursprünglich verwendet wurde. Allerdings muss dabei die `Version` des Chaincodes erhöht werden. Alle Peers müssen den gleichen Chaincode-Namen und die gleiche Chaincode-Version benutzen.

2. Nach der Installation des neuen Chaincodes auf allen Peers des Kanals können Sie mit dem Network Monitor oder dem Befehl für ein
[Upgrade des Chaincodes des Peers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) den Kanal aktualisieren, sodass der neue Chaincode verwendet wird.

Weitere Informationen zur Verwendung der Anzeige "Code installieren" des Network Monitor zur Aktualisierung des Chaincodes auf dem Kanal finden Sie in Schritt 2 dieser [Anweisungen](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode).

## Fehlerbehebung
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problem:** Fehler beim Anmelden beim Peer-Container
{: #icp-bash-error}

Dieses Problem kann auftreten, wenn Sie das kubectl-Tool verwenden, um mit "exec" für den Peer den folgenden Befehl auszuführen:
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
Schlägt der Befehl fehl, wird die im folgenden Beispiel dargestellte Fehlernachricht ausgegeben:
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Lösung:**  

Dieser Fehler tritt auf, wenn auf Big Endian-Systemen (z. B. IBM System Z) zwei Docker-Bash-Sitzungen in demselben Docker-Container geöffnet werden. Die zweite Bash-Sitzung kann in diesem Fall möglicherweise keine Verbindung zum aktiven Container herstellen. Zur Behebung dieses Problems müssen Sie einen [Neustart des Peer-Containers durchführen](#remote-peer-restart) und die Ausführung des Befehls `kubectl exec` nach dem Neustart des Peers wiederholen.

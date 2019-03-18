---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Peers in {{site.data.keyword.cloud_notm}} Private mit Starter Plan oder Enterprise Plan betreiben
{: #ibp-peer-operate}

Nach der Einrichtung eines Peers unter {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private müssen Sie verschiedene operative Schritte ausführen, bevor Ihr Peer Transaktionen an ein Starter Plan- oder Enterprise Plan-Netz übergeben kann. Die Schritte umfassen das Hinzufügen Ihrer Organisation zu einem Kanal, das Hinzufügen Ihres Peers zum Kanal, das Installieren des Chaincodes auf dem Peer, die Instanziierung des Chaincodes auf dem Kanal und die Verbindung von Anwendungen mit dem Peer.
{:shortdesc}

Sie müssen einige vorausgesetzte Schritte über den {{site.data.keyword.cloud_notm}} Private-Cluster ausführen, um Ihren Peer betreiben zu können.

**Voraussetzungen:**
- [CLIs konfigurieren](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)
- [Endpunktinformationen für den Peer abrufen](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)
- [TLS-Zertifikat des Peers herunterladen](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)

Zum Betreiben Ihres Peers können Sie eine der folgenden Methoden verwenden.

**Operationspfade:**
- [Fabric-SDKs verwenden](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-operate-with-sdk)
- [Befehlszeile verwenden](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-cli-operate)

Die Fabric-SDKs stellen den empfohlenen Pfad dar, wobei in den Anweisungen davon ausgegangen wird, dass Sie bereits mit dem Betrieb des SDK vertraut sind. Wenn Sie die Befehlszeile verwenden möchten, können Sie den Fabric-Client "peer" verwenden.

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

**Hinweis**: Ein Peer unter {{site.data.keyword.blockchainfull_notm}} verfügt nicht über Zugriff auf die vollständige Funktionalität oder Unterstützung von Peers, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Demzufolge können Sie den Network Monitor nicht zum Betreiben eines Peers in {{site.data.keyword.cloud_notm}} Private verwenden. Bevor Sie mit der Ausführung Peers beginnen, müssen Sie sich mit den [Hinweisen und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations) vertraut machen.

## Voraussetzungen
{: #ibp-peer-operate-prerequisites}

Unabhängig davon, ob Sie SDKs oder die Befehlszeile verwenden wollen, müssen Sie die Schritte in diesem Abschnitt während des Betriebs Ihres Peers ausführen. Alle diese Schritte können Sie im Vorfeld ausführen.

### CLIs konfigurieren
{: #ibp-peer-operate-kubectl-configure}

Sie müssen mit dem Befehlszeilentool **kubectl** eine Verbindung zu dem in {{site.data.keyword.cloud_notm}} Private ausgeführten Peer-Container herstellen.

1. Melden Sie sich bei der Benutzerschnittstelle Ihres {{site.data.keyword.cloud_notm}} Private-Clusters an. Navigieren Sie zur Registerkarte **Befehlszeilentools** und klicken Sie auf **Cloud Private-CLI**. Daraufhin werden die folgenden Tools angezeigt, die Sie herunterladen können.

   * IBM Cloud Private-CLI und Plug-ins installieren
   * CLI "kubectl" installieren
   * Helm installieren
   * Istio-CLI installieren

  Zum Betrieb eines Peers benötigen Sie die ersten **drei** Tools, wobei Helm optional ist. Klicken Sie auf jedes Tool und führen Sie die `curl`-Befehle für den von Ihnen verwendeten Maschinentyp aus. Geben Sie anschließend für jedes Tool die Befehle `chmod` und `sudo mv` aus. Der Befehl `chmod` ändert die Berechtigung der entsprechenden CLI, damit sie ausführbar wird; der Befehl `sudo mv` versetzt die Datei und benennt sie um.

  Die Befehle für das erste Tool **cloudctl** könnten wie im folgenden Beispiel aussehen:

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  Die Befehle für das zweite Tool **kubectl** könnten wie im folgenden Beispiel aussehen:

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Führen Sie nach dem Konfigurieren von **kubectl** den folgenden Befehl aus:

  ```
  kubectl get pods
  ```
  {:codeblock}

  Falls der Befehl erfolgreich ausgeführt wurde, gibt er eine Antwort zurück, die dem folgenden Beispiel ähnelt, wobei `peer-6cf85f6687-hcxpl` der Name des Peer-Containers ist.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Nun können Sie das Tool **kubectl** verwenden, um die Endpunktinformationen für den Peer abzurufen.

3. Falls Sie **Helm** verwenden wollen, führen Sie optional ein paar weitere Schritte aus. Bitte beachten Sie, dass die Installation von Helm optional ist und Helm in den vorliegenden Anweisungen nicht verwendet werden muss. Es kann jedoch zur Verwaltung Ihrer Helm-Releases und zur Erstellung eigener Archive für die Bereitstellung in {{site.data.keyword.cloud_notm}} Private von Nutzen sein.

  1. Klicken Sie auf "Helm installieren" und führen Sie den Befehl `curl` über die {{site.data.keyword.cloud_notm}} Private-Benutzerschnittstelle aus.
  2. Entpacken Sie die Datei `tar` mit dem folgenden Befehl:

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. Führen Sie den Befehl `sudo mv` aus und hängen Sie hierbei `/helm` an `darwin-amd64` an. Bitte beachten Sie, dass Sie den Befehl `chmod` für Helm nicht ausführen müssen. Beispiel:

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Mit dem Befehl `helm help` können Sie sich vergewissern, dass Helm erfolgreich installiert wurde.

### Endpunktinformationen des Peers abrufen
{: #ibp-peer-operate-endpoint}

Sie müssen Ihren Peerendpunkt im SDK oder im Fabric-CA-Client als Ziel verwenden, um einem Kanal beizutreten oder Smart Contracts zu installieren. Den Endpunkt Ihres Peers können Sie mithilfe der Benutzerschnittstelle in der {{site.data.keyword.cloud_notm}} Private-Konsole ermitteln. Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um die folgenden Schritte ausführen zu können:

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**.
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Im Abschnitt **Hinweise** ist eine Reihe von Befehlen aufgeführt, die Sie bei der Bereitstellung Ihres Peers unterstützen.
5. Führen Sie, sofern noch nicht geschehen, die Schritte zum Konfigurieren der [CLI "kubectl"](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) aus. Sie verwenden "kubectl" für die Interaktion mit Ihrem Peer-Container.
6. Führen Sie in der CLI den ersten Befehl in den Hinweisen aus, der nach **1. Rufen Sie die Anwendungs-URL mit den folgenden Befehlen ab:** angegeben ist. Dieser Befehl gibt die URL und den Port für den Peer aus, der ähnlich wie im folgenden Beispiel lautet. Speichern Sie diese URL für zukünftige Befehle.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Hinweis:** Wenn Sie Ihren Peer hinter einer Firewall bereitstellen, müssen Sie einen Durchgriff öffnen, damit das Netz auf der Plattform einen TLS-Handshake mit Ihrem Peer durchführen kann. Sie müssen lediglich einen Durchgriff für den Knotenport aktivieren, der an Port 7051 Ihres Peers gebunden ist. Weitere Informationen enthält der Abschnitt [Endpunktinformationen des Peers ermitteln](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint).

### TLS-Zertifikat des Peers herunterladen
{: #ibp-peer-operate-tls-cert}

Sie müssen das TLS-Zertifikat Ihres Peers herunterladen und es an Ihre Befehlen übergeben, damit Sie von einem fernen Client aus mit Ihrem Peer kommunizieren können.

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**.
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Im Abschnitt **Hinweise** ist eine Reihe von Befehlen aufgeführt, die Sie bei der Bereitstellung Ihres Peers unterstützen.
5. Führen Sie, sofern noch nicht geschehen, die Schritte zum Konfigurieren der [CLI "kubectl"](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) aus. Sie verwenden "kubectl" für die Interaktion mit Ihrem Peer-Container.
6. Führen Sie in der CLI den ersten Befehl im Hinweis aus, der auf **3. TLS-Stammzertifikatdatei für Peer abrufen:** folgt. Mit diesem Befehl wird das TLS-Zertifikat als Datei `cacert.pem` auf Ihrer lokalen Maschine gespeichert.
7. Versetzen Sie das Zertifikat an eine Position, unter der Sie in künftigen Befehlen darauf verweisen können, und benennen Sie es in `peertls.pem` um.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Fabric-SDKs zum Betrieb des Peers verwenden
{: #ibp-peer-operate-operate-with-sdk}

Die Hyperledger Fabric-SDKs stellen eine leistungsstarke Gruppe von APIs zur Verfügung, die Anwendungen die Interaktion mit und den Betrieb von Blockchain-Netzen ermöglichen. Die aktuellste Version der Liste der unterstützten Sprachen und die vollständige Liste der verfügbaren APIs in den Fabric-SDKs finden Sie in der [Hyperledger Fabric SDK Community-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community-Dokumentation"). Sie können die Fabric-SDKs verwenden, um Ihren Peer zu einem Kanal unter {{site.data.keyword.blockchainfull_notm}} Platform hinzuzufügen, einen Chaincode auf dem Peer zu installieren und den Chaincode auf einem Kanal zu instanziieren.

Die folgenden Anweisungen verwenden das [Fabric Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Fabric Node SDK"), um den Peer zu betreiben. Dabei wird davon ausgegangen, dass Sie bereits über Kenntnisse zu dem SDK verfügen. Sie können das [Lernprogramm 'Anwendungen entwickeln'](/docs/services/blockchain/v10_application.html#dev-app) benutzen, um sich mit der Vorgehensweise zur Verwendung des Node-SDK vertraut zu machen. Außerdem dient es als Leitfaden für die Entwicklung von Anwendungen mit dem Peer, wenn Sie bereit sind zum Aufrufen und Abfragen des Chaincodes.

### Node-SDK installieren
{: #ibp-peer-operate-install-sdk}

Sie können NPM verwenden, um das [Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Node SDK") zu installieren:

```
npm install fabric-client@1.2
```
{:codeblock}

Hierbei wird die Verwendung von Version 1.2 des Node-SDK empfohlen.

### SDK zur Nutzung mit dem Peer vorbereiten
{: #ibp-peer-operate-prepare-node-sdk}

Ihr Peer wird mit dem integrierten signCert-Zertifikat Ihres Peeradministrators bereitgestellt. Hierdurch können Sie die Zertifikate und den MSP-Ordner des Peeradministrators verwenden, um den Peer zu betreiben.

Suchen Sie nach den Zertifikaten, die Sie bei der [Eintragung des Peeradministrators](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin) erstellt haben. Falls Sie hierzu die Beispielbefehle ausgeführt haben, finden Sie den MSP-Ordner des Peeradministrators an der Position `$HOME/fabric-ca-client/peer-admin`.
  - Sie können den Benutzerkontext des Peeradministrators mit dem SDK unter Verwendung des signCert-Zertifikats (öffentlicher Schlüssel) und des privaten Schlüssels im MSP-Ordner erstellen. Diese Schlüssel finden Sie an den folgenden Positionen:
    - Das signCert-Zertifikat befindet sich im Ordner **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - Der private Schlüssel befindet sich im Ordner **keystore**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`.
    Ein Beispiel für die Bildung eines Benutzerkontextes und die Verwendung des SDK nur mit dem öffentlichen und dem privaten Schlüssel enthält [einer der Abschnitte des Lernprogramms für die Anwendungsentwicklung](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel).

Mit dem SDK können Sie auch das signCert-Zertifikat und den privaten Schlüssel des Peeradministrators generieren; hierzu verwenden Sie die Endpunktinformationen der Zertifizierungsstelle für den Starter Plan oder Enterprise Plan sowie [den Benutzernamen und das Kennwort Ihres Peeradministrators](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin).

### signCert-Zertifikat des Peeradministrators in Starter Plan oder Enterprise Plan hochladen
{: #ibp-peer-operate-upload-sdk}

Sie müssen das signCert-Zertifikat des Peeradministrators in das Netz unter {{site.data.keyword.blockchainfull_notm}} Platform hochladen, sodass Ihre Anwendung die Berechtigung für den Betrieb des Netzes erhält.

- Ihr signCert-Zertifikat befindet sich in dem Verzeichnis, in dem das SDK die Verschlüsselungsinformationen in einer Datei generiert hat, die nach dem Peeradministrator benannt ist. Kopieren Sie das Zertifikat, das in Anführungszeichen angegeben ist, an die Position nach dem Feld `certificate`. Das entsprechende Element beginnt mit der Zeichenfolge `-----BEGIN CERTIFICATE-----` und endet mit der Zeichenfolge `-----END CERTIFICATE-----`. Sie können die Befehlszeilenschnittstelle verwenden, um das Zertifikat ins PEM-Format umzuwandeln, indem Sie den Befehl `echo -e "<CERT>" > admin.pem` absetzen. Anschließend können Sie die Inhalte des Zertifikats mithilfe des Network Monitor in das Blockchain-Netz einfügen. Melden Sie sich beim Netz unter {{site.data.keyword.blockchainfull_notm}} Platform an. Klicken Sie in der Anzeige "Mitglieder" des Network Monitor auf **Zertifikate** > **Zertifikate hinzufügen**. Geben Sie für Ihr Zertifikat einen beliebigen Namen an und fügen Sie die Inhalte in das Feld für das **Zertifikat** ein. Klicken Sie auf **Erneut starten**, wenn Sie dazu aufgefordert werden anzugeben, ob die Peers erneut gestartet werden sollen. Diese Aktion muss ausgeführt werden, bevor Ihre Organisation dem Kanal beitritt.

### TLS-Zertifikat des Peers an SDK übergeben
{: #ibp-peer-operate-download-tlscert}

Sie müssen auf das TLS-Zertifikat Ihrer Peers verweisen, um die Kommunikation mit Ihrem SDK zu authentifizieren. Ermitteln Sie die Position des TLS-Zertifikats, das Sie [aus dem Peer-Container heruntergeladen haben](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert), und speichern Sie es an einer Position, unter der Ihre Anwendung darauf verweisen kann. Falls Sie die Beispielbefehle ausgeführt haben, finden Sie das TLS-Zertifikat an der Position `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Anschließend können Sie das TLS-Zertifikat in Ihre Anwendung importieren, indem Sie einen einfachen Dateilesebefehl benutzen.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Endpunktinformationen des Peers für SDK bereitstellen
{: #ibp-peer-operate-SDK-endpoints}

Suchen Sie die [Endpunktinformationen Ihres Peers](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint) und stellen Sie diese Informationen für das SDK bereit, indem Sie eine neue Peervariable deklarieren oder das Verbindungsprofil aktualisieren. Im folgenden Beispiel wird der Peer als Endpunkt im Fabric-Netz definiert und es wird das von Ihnen importierte TLS-Zertifikat an den Peer übergeben.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**Hinweis:** Da sich der Peer außerhalb von {{site.data.keyword.cloud_notm}} befindet, können andere Organisationen im {{site.data.keyword.blockchainfull_notm}} Platform-Netz die Endpunktinformationen Ihres Peers nicht in ihrem Verbindungsprofil finden. Wenn andere Organisationen Ihrem Peer eine zu bewilligende Transaktion senden müssen, müssen Sie ihnen die Peer-URL in einer externen Operation zur Verfügung stellen.

### SDK zum Beitreten zu einem Kanal verwenden
{: #ibp-peer-operate-peer-join-channel-sdk}

Ihre Organisation muss Mitglied eines Kanals sein, damit Sie mit Ihrem Peer dem Kanal beitreten können.

  - Sie können einen neuen Kanal für den Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) automatisch einbinden.

  - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel) durchführt.

    Nachdem Ihre Organisation zu einem Kanal hinzugefügt wurde, müssen Sie das signCert-Zertifikat Ihres Peers zu dem Kanal hinzufügen, damit andere Mitglieder Ihre digitale Signatur während der Ausführung von Transaktionen überprüfen können. Der Peer lädt sein signCert-Zertifikat während der Installation hoch. Dies bedeutet, dass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beitreten soll, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert. Möglicherweise müssen Sie einige Minuten auf den Abschluss der Kanalsynchronisation warten, bevor Sie Befehle `join channel` ausgeben können.

    **Hinweis:** Sie können nur dann neu zum Kanal hinzugefügte Blöcke, instanziierten Chaincode und andere Kanalaktualisierungen in der Anzeige "Kanäle" von Network Monitor sehen, falls der Peer, den Sie zu {{site.data.keyword.cloud_notm}} Private hinzufügen und mit einem Starter Plan- oder Enterprise Plan-Netz verbinden, zu derselben Organisation wie ein mit Network Monitor hinzugefügter Peer gehört. Dies liegt daran, dass Network Monitor Informationen in der Anzeige "Kanäle" aus Ihrem Peer erfasst und Peers außerhalb von {{site.data.keyword.cloud_notm}} für Network Monitor nicht sichtbar sind. Falls keiner Ihrer Peers die Funktion für private Daten verwendet, sind die Informationen in Network Monitor für einen Peer in einer Organisation mit den anderen Peers identisch.

Nachdem Ihre Organisation Mitglied eines Kanals geworden ist, befolgen Sie die Anweisungen für den [Beitritt Ihres Peers zu einem Kanal](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk), der unter Verwendung des SKD erfolgt. Sie müssen die URL des Anordnungsservice und den Kanalnamen angeben.

### SDK zum Installieren des Chaincodes auf dem Peer verwenden
{: #ibp-peer-operate-install-cc-sdk}

Führen Sie die folgenden Anweisungen aus, um das SDK zum [Installieren eines Chaincodes](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk) auf Ihrem Peer zu verwenden.

### SDK zur Instanziierung des Chaincodes auf dem Kanal verwenden
{: #ibp-peer-operate-instantiate-cc-sdk}

Nur ein Mitglied des Kanals muss den Chaincode instanziieren oder aktualisieren. Aus diesem Grund kann jedes Netzmitglied auf dem Kanal, das über Peers unter {{site.data.keyword.blockchainfull_notm}} Platform verfügt, den Network Monitor zum Instanziieren des Chaincodes und zur Angabe der Bewilligungsrichtlinien verwenden. Wenn Sie den Peer allerdings zum Instanziieren des Chaincodes auf einem Kanal verwenden wollen, dann können Sie das SDK benutzen und die Anweisungen im Abschnitt zum [Instanziieren eines Chaincodes](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk) befolgen.

## Befehlszeilenschnittstelle zum Betrieb des Peers verwenden
{: #ibp-peer-operate-cli-operate}

Sie können den Peer auch über die Befehlszeile betreiben, indem Sie die Fabric-Client `peer` verwenden.

Ihr Peer wurde mit dem integrierten signCert-Zertifikat Ihres Peeradministrators bereitgestellt, wodurch diese Identität den Peer betreiben darf. Die folgenden Anweisungen verwenden den MSP-Ordner des Peeradministrators, der beim [Bereitstellen Ihres Peers](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin) generiert wurde, um den Peer zu einem Kanal hinzuzufügen, einen Chaincode auf dem Peer zu installieren und dann den Chaincode in einem Kanal zu instanziieren.

### Fabric-Client "peer" herunterladen
{: #ibp-peer-operate-download-fabric-client}

Am einfachsten erhalten Sie den Client "peer", indem Sie alle Binärdateien für die Fabric-Tools bei Hyperledger herunterladen. Navigieren Sie zu einem Verzeichnis, in das Sie die Binärdateien mithilfe der Befehlszeile herunterladen wollen, und rufen Sie die Dateien durch Ausgabe des nachfolgenden Befehls ab. Falls [Curl ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") nicht installiert ist, müssen Sie dies zuvor ausführen.

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

Der Befehl installiert die Binärdateien in einem Verzeichnis namens `bin/`. Legen Sie als Pfad zu den Tools die Position fest, an die die obigen Fabric-Tools heruntergeladen wurden:
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

Falls Sie die Binärdateien beispielsweise in Ihrem Ausgangsverzeichnis installiert haben, würden Sie `PATH` wie folgt festlegen:

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

Beim Download der Binärdateien wird ein Ordner `config` erstellt, der eine Datei "core.yaml", "orderer.yaml" und "configtx.yaml" enthält. Legen Sie den Fabric-Konfigurationspfad zu diesem Verzeichnis `config` fest:

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

Beispiel:
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}


### Zertifikate auf dem lokalen System verwalten
{: #manage-certs}

Wechseln Sie in das Verzeichnis, in dem der MSP-Ordner für den Peeradministrator generiert wurde. Falls Sie die Schritte in den Beispielen dieser Dokumentation ausgeführt haben, ist der MSP-Ordner in einem Verzeichnis zu finden, das etwa wie folgt heißt:

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Bevor Sie den Peer betreiben können, müssen Sie einige Management-Tasks für die Zertifikate auf Ihrer lokalen Maschine ausführen. Sie müssen beispielsweise das signCert-Zertifikat des Peeradministrators in den Starter Plan oder Enterprise Plan hochladen und sicherstellen, dass Sie vom Peer und Ihrem Netz in {{site.data.keyword.cloud_notm}} aus auf die TLS-Zertifikate zugreifen können. Weitere Informationen zu den zu verwendenden Zertifikaten und den auszuführenden Tasks enthält der Abschnitt [Zertifikate in {{site.data.keyword.blockchainfull_notm}} Platform verwalten](/docs/services/blockchain/certificates.html#managing-certificates).

1. Versetzen Sie das signCert-Zertifikat für den Peeradministrator in einen neuen Ordner namens `admincerts`:

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Laden Sie dieses signCert-Zertifikat in das Netz in {{site.data.keyword.cloud_notm}} hoch, damit eine ferne CLI oder Anwendung Kanaloperationen wie das Abrufen des Genesis-Blocks für den Kanal oder das Instanziieren von Chaincode ausführen kann.
    1. Öffnen Sie auf Ihrer lokalen Maschine die Datei `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` und kopieren Sie sie in die Zwischenablage.
    2. Rufen Sie den Network Monitor Ihres Netzes unter {{site.data.keyword.blockchainfull_notm}} Platform auf.
    3. Klicken Sie im linken Navigator auf **Mitglieder** und dann auf die Registerkarte **Zertifikate**.
    4. Klicken Sie auf die Schaltfläche **Zertifikat hinzufügen**.
    5. Ordnen Sie im Fenster **Zertifikat hinzufügen** Ihrem Zertifikat einen Namen zu (z. B. "peer-admin"), fügen Sie das soeben kopierte Zertifikat in die Zwischenablage ein und klicken Sie dann auf **Abschicken**.
    6. Sie werden aufgefordert anzugeben, ob die Peers neu gestartet werden sollen. Klicken Sie auf **Erneut starten**.
    7. Klicken Sie im linken Navigator auf **Kanäle** und suchen Sie den Kanal, dem der Peer beitreten soll.
    8. Klicken Sie auf die drei Punkte unter der Überschrift **Aktionen** und dann auf **Zertifikat synchronisieren**. Klicken Sie im Fenster **Zertifikat synchronisieren** auf **Abschicken**.

    **Hinweis**: Das Kanalzertifikat muss unbedingt synchronisiert werden, bevor der Peer dem Kanal beitritt oder Chaincode im Kanal instanziiert. Möglicherweise müssen Sie einige Minuten auf den Abschluss der Kanalsynchronisation warten, bevor Sie Befehle für das Beitreten zum Kanal oder das Instanziieren von Chaincode ausgeben können.

3. Stellen Sie sicher, dass Sie das [TLS-Zertifikat des Peers heruntergeladen](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) haben und in der Befehlszeile darauf verweisen können. Wenn Sie die Beispielbefehle befolgt haben, befindet sich das TLS-Zertifikat in der Datei `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

4. Sie müssen außerdem auf das TLS-Zertifikat verweisen, das Sie für die Kommunikation mit der Zertifizierungsstelle für den Starter Plan oder den Enterprise Plan verwendet haben, als Sie Ihren [Peeradministrator eingetragen haben](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin). Wenn Sie die Beispielbefehle in dieser Dokumentation befolgt haben, befindet sich das TLS-Zertifikat in der Datei `$HOME/fabric-ca-client/tls-ibp/tls.pem`.

Durch einen Befehl "tree" können Sie prüfen, ob Sie diese Schritte ausgeführt haben. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Zertifikate gespeichert haben. Der Befehl "tree" sollte ein Ergebnis ähnlich der folgenden Struktur generieren:
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CLI-Umgebungsvariablen festlegen
{: #ibp-peer-operate-environment-variables}

Nachdem alle Zertifikate an die erforderliche Position verschoben wurden, müssen einige Umgebungsvariablen festgelegt werden, die von den Befehlen verwendet werden. Anschließend sind alle Vorbereitungen für den Betrieb des Peers mit dem Fabric-Client "peer" abgeschlossen.

1. Rufen Sie die Konfigurationsinformationen für den Starter Plan oder Enterprise Plan aus dem **Verbindungsprofil** ab, das in der Anzeige **Übersicht** in Network Monitor verfügbar ist. Klicken Sie auf **Verbindungsprofil** und dann auf **Download**.

  - Suchen Sie die URL des Anordnungsknotens, indem Sie nach **orderers** suchen. Das Element befindet sich unter `orderers > url`. Notieren Sie die URL, die mit dem Netznamen beginnt. Daraufhin wird die URL wie im folgenden Beispiel angezeigt:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Suchen Sie den Namen Ihrer Organisation, indem Sie bei der Suche **organizations** angeben. Hierbei sollte es sich um dieselbe Organisation handeln, die Sie zum Registrieren Ihres Peers verwenden. Der Name Ihrer Organisation wird mit dem zugehörigen Wert für `mspid` angezeigt. Notieren Sie den Wert für `mspid`.

2. [Ermitteln Sie die Endpunktinformationen des Peers](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint). Der Peerendpunkt wird zum Festlegen der Umgebungsvariablen `PEERADDR` verwendet. Achten Sie darauf, die Angabe `http://` am Beginn wegzulassen.

3. Führen Sie die folgenden Befehle aus, um die Umgebungsvariablen festzulegen.

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Ersetzen Sie die Feldinhalte durch Ihre eigenen Angaben.
    - Ersetzen Sie `<full_path_to_config_folder>` durch den Konfigurationsordner, der erstellt wurde, als Sie den [Client "peer" heruntergeladen haben](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-download-fabric-client).
    - Ersetzen Sie `<CHANNEL_NAME>` durch den Namen des Kanals, an dem der Peer teilnimmt.
    - Ersetzen Sie `<CC_NAME>` durch einen Namen, mit dem auf Ihren Chaincode verwiesen wird.
    - Ersetzen Sie `<PEERADDR>` durch den Peerendpunkt aus dem vorherigen Schritt für den Peer, den Sie gegenwärtig verwenden.
    - Ersetzen Sie `<ORDERER_URL>` durch den Hostnamen und den Port für den Anordnungsknoten aus Ihrem Verbindungsprofil.
    - Ersetzen Sie `<PATH_TO_ADMIN_MSP>` durch den Pfad zum MSP-Ordner Ihres Peeradministrators.
    - Ersetzen Sie `<PATH_TO_PEER_TLS_CERT>` durch den Pfad zum TLS-Zertifikat des Peers, das Sie heruntergeladen haben.
    - Ersetzen Sie `<MSPID_OF_PEER_BEING_USED>` durch die MSP-ID der Organisation für Ihren Peer, die Sie zum Abrufen eines Genesis-Blocks, zum Beitreten zu einem Kanal oder zum Installieren von Chaincode verwenden.

  Beispiel:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  Es kann außerdem sinnvoll sein, TLS zu aktivieren. Hierzu geben Sie Folgendes aus:

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Peer mit der CLI zu einem Kanal hinzufügen
{: #ibp-peer-operate-cli-join-channel}

Bevor Sie die CLI-Befehle für den Beitritt des Peers zu einem Kanal ausführen können, muss Ihre Organisation mit einem der folgenden Verfahren zu einem Kanal im Netz hinzugefügt werden.

  - Sie können einen neuen Kanal für den Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel) automatisch einbinden.
  - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel) durchführt.

    Nachdem Ihre Organisation zu einem Kanal hinzugefügt wurde, müssen Sie das signCert-Zertifikat Ihres Peers zu dem Kanal hinzufügen, damit andere Mitglieder Ihre digitale Signatur während der Ausführung von Transaktionen überprüfen können. Der Peer lädt sein signCert-Zertifikat während der Installation hoch. Dies bedeutet, dass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beitreten soll, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert.

    **Hinweis:** Falls Ihr {{site.data.keyword.cloud_notm}} Private-Peer, der eine Verbindung zu einem Starter Plan- oder Enterprise Plan-Netz herstellt, zu derselben Organisation wie ein anderer Peer gehört, der mit Network Monitor hinzugefügt wurde, können Sie neu zum Kanal hinzugefügte Blöcke, instanziierten Chaincode und andere Kanalaktualisierungen nur in der Anzeige "Kanäle" von Network Monitor sehen. Dies liegt daran, dass Network Monitor Informationen in der Anzeige "Kanäle" aus Ihrem Peer erfasst und Peers außerhalb von {{site.data.keyword.cloud_notm}} für Network Monitor nicht sichtbar sind. Falls keiner Ihrer Peers die Funktion für private Daten verwendet, sind die Informationen in Network Monitor für einen Peer in einer Organisation mit den anderen Peers identisch.

1. Stellen Sie sicher, dass Sie die [Umgebungsvariablen in Ihrer Befehlszeilenschnittstelle](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-environment-variables) festgelegt haben.

2. Rufen Sie den Genesis-Block des Kanals ab, um das Kanalledger Ihres Peers zu erstellen. `$HOME/fabric-ca-client/tls-ibp/tls.pem` stellt den Pfad zum TLS-TLS-Zertifikat für den Starter Plan oder Enterprise Plan dar. Falls Ihr Pfad von dieser Angabe abweicht, achten Sie darauf, den richtigen Namen festzulegen.

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
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

  Sie wissen, dass der Genesis-Block erfolgreich hinzugefügt wurde, wenn Sie die im folgenden Beispiel dargestellte Ausgabe erhalten:

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. Nachdem Sie den Genesis-Block des Kanals abgerufen haben, können Sie den Peer mit dem folgenden Befehl zum Kanal hinzufügen.

  ```
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  Nachdem dieser Schritt erfolgreich ausgeführt wurde, werden die im folgenden Beispiel dargestellten Informationen angezeigt:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Chaincode mit CLI auf dem Peer installieren
{: #ibp-peer-operate-toolcontainer-install-cc}

Nun sind Sie bereit zur Installation und Instanziierung des Chaincodes auf dem Peer. In den folgenden Anweisungen wird der `fabcar`-Chaincode aus dem Repository `fabric-samples` installiert. Stellen Sie sicher, dass Sie zuvor [GOPATH konfiguriert haben ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Set your GOPATH"), und laden Sie dann den Chaincode namens `fabric-samples` mit den folgenden Befehlen bei GitHub herunter:

```
cd $GOPATH/src
git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

Führen Sie den folgenden Peer-CLI-Befehl aus, um den `fabcar`-Chaincode auf dem Peer zu installieren:

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**Hinweis:** Falls dieser Chaincode bereits auf einem anderen Peer installiert und instanziiert wurde, der in einem Starter Plan oder Enterprise Plan von IBP ausgeführt wird, müssen Sie vor der Installation des Chaincodes auf dem Peer zusätzliche Schritte ausführen. Ein [Fehlerbehebungsabschnitt](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-troubleshooting) enthält weitere Informationen zum Vermeiden von Fehlern, die mit der Installation dieses Chaincodes zusammenhängen.

### Chaincode mit der CLI auf einem Kanal instanziieren
{: #ibp-peer-operate-toolcontainer-instantiate-cc}

Da nur ein Peer den Chaincode auf einem Kanal instanziieren muss, müssen Sie den Peer nicht zum Instanziieren des Chaincodes verwenden. Dieser Arbeitsschritt kann von Peers ausgeführt werden, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Wenn jedoch der Peer den Chaincode auf dem Kanal instanziieren soll, können Sie hierzu den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie für `CORE_PEER_TLS_ROOTCERT_FILE` den Pfad des TLS-Zertifikats der Zertifizierungsstelle für den Starter Plan oder Enterprise Plan festgelegt haben.

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

Nachdem der Chaincode instanziiert wurde, können Sie mit den Chaincode-Befehlen zum Abfragen (query) und Aufrufen (invoke) Daten im Kanalledger lesen und schreiben. Weitere Informationen enthalten die Angaben über die [Chaincode-Befehle für Peers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) in der Dokumentation von Hyperledger Fabric. Sie müssen den Endpunkt des Anordnungsknotens unter Verwendung der Proxy-IP und des externen Anordnungsknotenports an Ihre Aufrufbefehle übergeben. An einen Abfragebefehl muss lediglich der Peerendpunkt übergeben werden.

## Chaincode aktualisieren
{: #ibp-peer-operate-update-chaincode}

Im Zeitverlauf muss der Chaincode, der auf dem Peer ausgeführt wird, möglicherweise geändert werden. Da der Chaincode auf den Peers installiert ist und auf dem Kanal instanziiert wird, müssen Sie den Chaincode auf allen Peers des Kanals aktualisieren.

Führen Sie die folgenden Schritte aus, um den Chaincode zu aktualisieren:

1. Zum Aktualisieren des Chaincodes auf den einzelnen Peers müssen Sie einfach den Prozess erneut ausführen, den Sie zum Installieren des Chaincodes auf den Peers verwendet haben. Dazu können Sie entweder eine Clientanwendung oder einen CLI-Befehl benutzen. Beachten Sie hierbei unbedingt, dass der gleiche Chaincode-Name verwendet werden muss, der ursprünglich verwendet wurde. Allerdings muss dabei die `Version` des Chaincodes erhöht werden. Alle Peers müssen den gleichen Chaincode-Namen und die gleiche Chaincode-Version benutzen.

2. Nach der Installation des neuen Chaincodes auf allen Peers des Kanals können Sie mit dem Network Monitor oder dem Befehl für ein
[Upgrade des Chaincodes des Peers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) den Kanal aktualisieren, sodass der neue Chaincode verwendet wird.

Weitere Informationen zur Verwendung der Anzeige "Code installieren" des Network Monitor zur Aktualisierung des Chaincodes auf dem Kanal finden Sie in Schritt 2 dieser [Anweisungen](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc).

## Protokolle des Peers anzeigen
{: #ibp-peer-operate-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [Befehlen der CLI `"kubectl"`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster enthalten ist.

- Mit dem Befehl `kubectl logs` können Sie die Containerprotokolle innerhalb des Pods anzeigen. Falls Sie den Podnamen nicht genau kennen, führen den folgenden Befehl aus, um Ihre Podliste anzuzeigen.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Rufen Sie anschließend mit dem folgenden Befehl die Protokolle des Peer-Containers ab, der sich im Pod befindet; ersetzen Sie hierbei `<pod_name>` durch den Namen Ihres Pods aus der obigen Befehlsausgabe:

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  Weitere Informationen zum Befehl `kubectl logs` enthält die [Kubernetes-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”).

- Alternativ können Sie auf Protokolle auch über die [{{site.data.keyword.cloud_notm}} Private-Konsole für das Cluster-Management](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html) zugreifen, die die Protokolle in Kibana öffnet.

   **Hinweis:** Wenn Sie Ihre Protokolle in Kibana anzeigen, empfangen Sie möglicherweise die Antwort `Keine Ergebnisse gefunden`. Diese Bedingung kann auftreten, wenn {{site.data.keyword.cloud_notm}} Private die IP-Adresse Ihres Workerknotens als Hostnamen verwendet. Entfernen Sie zur Lösung dieses Problems oben in der Anzeige den Filter, der mit `node.hostname.keyword` beginnt. Anschließend sind die Protokolle sichtbar.

## Fehlerbehebung
{: #ibp-peer-operate-troubleshooting}

### **Problem:** Aufrufbefehl schlägt auf Peer mit der Nachricht `chaincode fingerprint mismatch` fehl
{: #ibp-peer-operate-install-error}

Möglicherweise empfangen Sie den Fehler `chaincode fingerprint mismatch`, wenn Sie eine Anforderung `peer chaincode invoke` für einen Peer ausführen, der in {{site.data.keyword.cloud_notm}} Private ausgeführt wird:

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

Dieser Fehler kann dadurch verursacht werden, dass in einem der folgenden Bereiche eine Inkonsistenz zwischen den Peers besteht, die den Chaincode ausführen:

- Name des Chaincodes
- Version des Chaincodes
- Pfad des Chaincodes
- Binärdatei des Chaincodes

Dieser Fehler kann auftreten, falls Chaincode mit der Network Monitor-Benutzerschnittstelle auf einem Peer installiert und instanziiert wurde, der in einem Starter Plan oder Enterprise Plan ausgeführt wird, und der Chaincode später in einem Peer installiert wird, der in {{site.data.keyword.cloud_notm}} Private ausgeführt wird. Der Fehler tritt bei der Anforderung `invoke` auf, weil die resultierenden Chaincodepfade auf den Peers unterschiedlich sind.

**Lösung:**
Falls Sie Chaincode in Peers sowohl in {{site.data.keyword.cloud_notm}} (z. B. Starter Plan oder Enterprise Plan) als auch in {{site.data.keyword.cloud_notm}} Private ausführen wollen, installieren Sie den Chaincode nicht mithilfe der Network Monitor-Benutzerschnittstelle. Erstellen Sie das Chaincode-Paket stattdessen mit dem Befehl [`peer chaincode package`![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) und installieren Sie das Paket anschließend mit dem Befehl [`peer chaincode install`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-toolcontainer-install-cc) auf allen Peers.

Falls auf einem Kanal bereits Chaincode installiert und instanziiert wurde, bevor Sie versuchen, den Chaincode auf einem Peer in {{site.data.keyword.cloud_notm}} Private zu installieren, müssen Sie zur Vermeidung des Problems die folgenden Schritte ausführen:

1. Erstellen Sie ein Chaincode-Paket mit dem Befehl [`peer chaincode package`![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package).
2. Installieren Sie das Chaincode-Paket auf dem Peer, der in {{site.data.keyword.cloud_notm}} Private ausgeführt wird. Verwenden Sie hierzu den Befehl `peer chaincode install`.
3. Falls Ihnen die plattformspezifischen Binärdateien zur Verfügung stehen, können Sie mit dem Befehl [`peer chaincode upgrade`![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) ein Upgrade für den Chaincode durchführen, der im Peer des Starter Plan oder Enterprise ausgeführt wird und das Chaincode-Paket verwendet.
4. Instanziieren Sie den neu installierten Chaincode auf dem Kanal entweder mithilfe der Network Monitor-Benutzerschnittstelle oder der CLI.

Der Prozess für das Upgrade von Chaincode ist auch im Abschnitt [`Chaincode for Operators`![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) der Dokumentation von Hyperledger Fabric beschrieben.

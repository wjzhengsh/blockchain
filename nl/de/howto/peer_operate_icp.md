---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Peers in {{site.data.keyword.cloud_notm}} Private mit einem Multi-Cloud-Netz betreiben
{: #peer-operate_icp}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Nach der Einrichtung eines Peers unter {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private (ICP) müssen Sie verschiedene operative Schritte ausführen, bevor Ihr Peer Transaktionen zum Abfragen und Aufrufen des Ledgers für das Blockchain-Netz absetzen kann. Die Schritte umfassen das Hinzufügen Ihrer Organisation zu einem Kanal, das Hinzufügen Ihres Peers zum Kanal, das Installieren des Chaincodes auf dem Peer, die Instanziierung des Chaincodes auf dem Kanal und die Verbindung von Anwendungen mit dem Peer. Falls Sie Ihren Peer mit einem Starter Plan- oder Enterprise Plan-Netz verbinden wollen, lesen Sie den Abschnitt [Peers unter {{site.data.keyword.cloud_notm}} Private mit Starter Plan- oder Enterprise Plan- Netz betreiben](peer_operate_ibp.html#peer-operate_icp).
{:shortdesc}

Sie müssen einige vorausgesetzte Schritte über den ICP-Cluster ausführen, um Ihren Peer betreiben zu können.

**Voraussetzungen:**
- [CLIs konfigurieren](#peer-kubectl-configure)
- [Endpunktinformationen für den Peer abrufen](#peer-endpoint)
- [TLS-Zertifikat des Peers herunterladen](#peer-tls)

Zum Betreiben Ihres Peers können Sie eine der folgenden Methoden verwenden.

**Operationspfade:**
- [Fabric-SDKs verwenden](#peer-operate-with-sdk)
- [Befehlszeile verwenden](#peer-cli-operate)

Die Fabric-SDKs stellen den empfohlenen Pfad dar, wobei in den Anweisungen davon ausgegangen wird, dass Sie bereits mit dem Betrieb des SDK vertraut sind. Wenn Sie die Befehlszeile verwenden möchten, können Sie die Fabric-Binärdateien für "peer" verwenden.

Wenn Ihre Organisation noch nicht Mitglied eines Konsortiums oder eines Kanals ist, können Sie diese Schritte zum [Erstellen eines Kanals](#create-channel) verwenden. Die Anweisungen führen Sie durch die [Vorbereitung einer Organisationsdefinition](#organization-definition). Diese Definition macht Sie zu einem Mitglied des Konsortiums, indem Sie zu einem Systemkanal eines Anordnungsknotens hinzugefügt werden. Anschließend können Sie einen neuen Kanal erstellen, indem Sie eine [Kanaltransaktion erstellen](#peer-icp-channeltx).
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

## Voraussetzungen

Unabhängig davon, ob Sie SDKs oder die Befehlszeile verwenden wollen, müssen Sie die Schritte in diesem Abschnitt während des Betriebs Ihres Peers ausführen. Alle diese Schritte können Sie im Vorfeld ausführen.

### CLIs konfigurieren
{: #peer-kubectl-configure}

Sie müssen mit dem Befehlszeilentool **kubectl** eine Verbindung zu dem in ICP ausgeführten Peer-Container herstellen.

1. Melden Sie sich bei der Benutzerschnittstelle Ihres ICP-Clusters an. Navigieren Sie zur Registerkarte **Befehlszeilentools** und klicken Sie auf **Cloud Private-CLI**. Daraufhin werden die folgenden Tools angezeigt, die Sie herunterladen können.

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

3. Falls Sie **Helm** verwenden wollen, führen Sie optional ein paar weitere Schritte aus. Bitte beachten Sie, dass die Installation von Helm optional ist und Helm in den vorliegenden Anweisungen nicht verwendet werden muss. Es kann jedoch zur Verwaltung Ihrer Helm-Releases und zur Erstellung eigener Archive für die Bereitstellung in ICP von Nutzen sein.

  1. Klicken Sie auf "Helm installieren" und führen Sie den Befehl `curl` über die ICP-Benutzerschnittstelle aus.
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
{: #peer-endpoint}

Sie müssen Ihren Peerendpunkt im SDK oder im Fabric-CA-Client als Ziel verwenden, um einem Kanal beizutreten oder Smart Contracts zu installieren. Den Endpunkt Ihres Peers können Sie mithilfe der Benutzerschnittstelle in der ICP-Konsole ermitteln. Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um die folgenden Schritte ausführen zu können:

1. Melden Sie sich bei der ICP-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**. 
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Im Abschnitt **Hinweise** ist eine Reihe von Befehlen aufgeführt, die Sie bei der Bereitstellung Ihres Peers unterstützen.
5. Führen Sie, sofern noch nicht geschehen, die Schritte zum Konfigurieren der [CLI "kubectl"](#peer-kubectl-configure) aus. Sie verwenden "kubectl" für die Interaktion mit Ihrem Peer-Container.
6. Führen Sie in der CLI den ersten Befehl in den Hinweisen aus, der nach **1. Rufen Sie die Anwendungs-URL mit den folgenden Befehlen ab:** angegeben ist. Dieser Befehl gibt die URL und den Port für den Peer aus, der ähnlich wie im folgenden Beispiel lautet. Speichern Sie diese URL für zukünftige Befehle.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Hinweis:** Wenn Sie Ihren Peer hinter einer Firewall bereitstellen, müssen Sie einen Durchgriff öffnen, damit das Netz auf der Plattform einen TLS-Handshake mit Ihrem Peer durchführen kann. Sie müssen lediglich einen Durchgriff für den Knotenport aktivieren, der an Port 7051 Ihres Peers gebunden ist. Weitere Informationen enthält der Abschnitt [Endpunktinformationen des Peers ermitteln](peer_operate_ibp.html#peer-endpoint).

### TLS-Zertifikat des Peers herunterladen
{: #peer-tls}

Sie müssen das TLS-Zertifikat Ihres Peers herunterladen und es an Ihre Befehlen übergeben, damit Sie von einem fernen Client aus mit Ihrem Peer kommunizieren können. 

1. Melden Sie sich bei der ICP-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**. 
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Im Abschnitt **Hinweise** ist eine Reihe von Befehlen aufgeführt, die Sie bei der Bereitstellung Ihres Peers unterstützen.
5. Führen Sie, sofern noch nicht geschehen, die Schritte zum Konfigurieren der [CLI "kubectl"](#peer-kubectl-configure) aus. Sie verwenden "kubectl" für die Interaktion mit Ihrem Peer-Container.
6. Führen Sie in der CLI den ersten Befehl im Hinweis aus, der auf **3. TLS-Stammzertifikatdatei für Peer abrufen:** folgt. Mit diesem Befehl wird das TLS-Zertifikat als Datei `cacert.pem` auf Ihrer lokalen Maschine gespeichert.
7. Versetzen Sie das Zertifikat an eine Position, unter der Sie in künftigen Befehlen darauf verweisen können, und benennen Sie es in `peertls.pem` um.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Fabric-SDKs zum Betrieb des Peers verwenden
{: #peer-operate-with-sdk}

Die Hyperledger Fabric-SDKs stellen eine leistungsstarke Gruppe von APIs zur Verfügung, die Anwendungen die Interaktion mit und den Betrieb von Blockchain-Netzen ermöglichen. Die aktuellste Version der Liste der unterstützten Sprachen und die vollständige Liste der verfügbaren APIs in den Fabric-SDKs finden Sie in der [Hyperledger Fabric SDK Community-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK Community-Dokumentation"). Sie können die Fabric-SDKs verwenden, um Ihren Peer zu einem Kanal unter {{site.data.keyword.blockchainfull_notm}} Platform hinzuzufügen, einen Chaincode auf dem Peer zu installieren und den Chaincode auf einem Kanal zu instanziieren.

Die folgenden Anweisungen verwenden das [Fabric Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Fabric Node SDK"), um den Peer zu betreiben. Dabei wird davon ausgegangen, dass Sie bereits über Kenntnisse zu dem SDK verfügen. Sie können das [Lernprogramm 'Anwendungen entwickeln'](../v10_application.html) benutzen, um sich mit der Vorgehensweise zur Verwendung des Node-SDK vertraut zu machen. Außerdem dient es als Leitfaden für die Entwicklung von Anwendungen mit dem Peer, wenn Sie bereit sind zum Aufrufen und Abfragen des Chaincodes.

### Node-SDK installieren

Sie können NPM verwenden, um das [Node-SDK ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Node SDK") zu installieren:

```
npm install fabric-client@1.2
```
{:codeblock}

Hierbei wird die Verwendung von Version 1.2 des Node-SDK empfohlen.

### SDK zur Nutzung mit dem Peer vorbereiten
{: #peer-node-sdk}

Ihr Peer wurde mit dem integrierten signCert-Zertifikat Ihres Peeradministrators bereitgestellt. Hierdurch können Sie die Zertifikate und den MSP-Ordner des Peeradministrators verwenden, um den Peer zu betreiben.

Suchen Sie nach den Zertifikaten, die Sie bei der [Eintragung des Peeradministrators](CA_operate.html#enroll-admin) erstellt haben. Falls Sie hierzu die Beispielbefehle ausgeführt haben, finden Sie den MSP-Ordner des Peeradministrators an der Position `$HOME/fabric-ca-client/peer-admin`.
  - Sie können den Benutzerkontext des Peeradministrators mit dem SDK unter Verwendung des signCert-Zertifikats (öffentlicher Schlüssel) und des privaten Schlüssels im MSP-Ordner erstellen. Diese Schlüssel finden Sie an den folgenden Positionen:
    - Das signCert-Zertifikat befindet sich im Ordner **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - Der private Schlüssel befindet sich im Ordner **keystore**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`.
    Ein Beispiel für die Bildung eines Benutzerkontextes und die Verwendung des SDK nur mit dem öffentlichen und dem privaten Schlüssel enthält der Abschnitt [Zertifkate mit Network Monitor generieren](../v10_application.html#enroll-panel).

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](CA_operate.html#register-admin). -->

### TLS-Zertifikat des Peers an SDK übergeben
{: #icp-peer-download-tlscert}

Sie müssen auf das TLS-Zertifikat Ihres Peers verweisen, um die Kommunikation im SDK zu authentifizieren. Ermitteln Sie die Position des TLS-Zertifikats, das Sie [aus dem Peer-Container heruntergeladen haben](#peer-tls), und speichern Sie es an einer Position, unter der Ihre Anwendung darauf verweisen kann. Falls Sie die Beispielbefehle ausgeführt haben, finden Sie das TLS-Zertifikat an der Position `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Anschließend können Sie das TLS-Zertifikat in Ihre Anwendung importieren, indem Sie einen einfachen Dateilesebefehl benutzen.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Endpunktinformationen des Peers für SDK bereitstellen
{: #peer-SDK-endpoints}

Ermitteln Sie die Position der [Endpunktinformationen Ihres Peers](#peer-endpoint) und geben Sie diese für das SDK an, indem Sie eine neue Peervariable deklarieren. Im folgenden Beispiel wird der Peer als Endpunkt im Fabric-Netz definiert und es wird das von Ihnen importierte TLS-Zertifikat an den Peer übergeben.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

Falls Sie ein umfangreiches Netz verwenden, bei dem mehrere Peers zu unterschiedlichen Organisationen gehören, die Ihre Transaktionen bewilligen müssen, kann es sinnvoll sein, zusammen mit Ihrem Konsortium [ein allgemeines Verbindungsprofil zu erstellen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-network-config.html "Common connection profile").

### TLS-Zertifikat des Anordnungsknotens an SDK übergeben
{: #icp-orderer-download-tlscert}

Um Kanälen beitreten und Transaktionen übergeben zu können, benötigen Sie außerdem das TLS-Zertifikat für den Anordnungsknoten Ihres Konsortiums. Befolgen Sie als Administrator des Anordnungsknotens die Anweisungen für den [Download des TLS-Zertifikats für den Anordnungsknoten](orderer_operate.html#orderer-tls).  Falls Sie die Beispielbefehle ausgeführt haben, finden Sie das TLS-Zertifikat des Peers an der Position `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`. Wenn der Anordnungsknoten von einer anderen Organisation gesteuert wird, muss diese Ihnen das TLS-Zertifikat in einer externen Operation zur Verfügung stellen. Anschließend können Sie das TLS-Zertifikat in Ihre Anwendung importieren.

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### Informationen zum Anordnungsknoten für SDK bereitstellen
{: #orderer-SDK-endpoints}

Um das SDK verwenden zu können, benötigen Sie außerdem die Endpunktinformationen der Anordnungsknoten in Ihrem Konsortium. Als Administrator des Anordnungsknotens können Sie mit diesen Anweisungen die [Endpunktinformationen des Anordnungsknotens abrufen](orderer_operate.html#orderer-endpoint). Wenn der Anordnungsknoten von einer anderen Organisation gesteuert wird, muss diese Ihnen die URL des Anordnungsknotens in einer externen Operation zur Verfügung stellen. Im folgenden Beispiel wird ein Anordnungsknoten als Endpunkt definiert und das TLS-Zertifikat des Anordnungsknotens an ihn übergeben.

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### SDK zum Beitreten zu einem Kanal verwenden
{: #peer-join-channel-sdk}

Ihre Organisation muss Mitglied eines Kanals sein, damit Sie mit Ihrem Peer dem Kanal beitreten können. Falls Sie nicht Mitglied eines Kanals sind, können Sie die Anweisungen zum [Erstellen eines neuen Kanals](#create-channel) ausführen.

Nachdem Ihre Organisation Mitglied eines Kanals geworden ist, befolgen Sie die Anweisungen für den [Beitritt Ihrer Peers zu einem Kanal](../v10_application.html#join-channel-sdk), der unter Verwendung des SKD erfolgt.

### SDK zum Installieren des Chaincodes auf dem Peer verwenden
{: #peer-install-cc-sdk}

Führen Sie die folgenden Anweisungen aus, um das SDK zum [Installieren eines Chaincodes](../v10_application.html#install-cc-sdk) auf Ihrem Peer zu verwenden.

### SDK zur Instanziierung des Chaincodes auf dem Kanal verwenden
{: #peer-instantiate-cc-sdk}

Nur ein Mitglied des Kanals muss den Chaincode instanziieren oder aktualisieren. Aus diesem Grund kann jedes Mitglied des Kanals, auf dessen Peers der Chaincode installiert ist, den Chaincode instanziieren und Bewilligungsrichtlinien angeben. Wenn Sie den Peer allerdings zum Instanziieren des Chaincodes auf einem Kanal verwenden wollen, dann können Sie das SDK benutzen und die Anweisungen im Abschnitt zum [Instanziieren eines Chaincodes](../v10_application.html#instantiate-cc-sdk) befolgen.

## Befehlszeilenschnittstelle zum Betrieb des Peers verwenden
{: #peer-cli-operate}

Sie können den Peer auch über die Befehlszeile betreiben, indem Sie die Fabric-Binärdateien für Peers verwenden.

Ihr Peer wurde mit dem integrierten signCert-Zertifikat Ihres Peeradministrators bereitgestellt, wodurch diese Identität den Peer betreiben darf. Die folgenden Anweisungen verwenden den MSP-Ordner des Peeradministrators, der beim [Bereitstellen Ihres Peers](CA_operate.html#register-admin) generiert wurde, um den Peer zu einem Kanal hinzuzufügen, einen Chaincode auf dem Peer zu installieren und dann den Chaincode in einem Kanal zu instanziieren.

### Fabric-Client "peer" herunterladen
{: #peer-client}

Um von einem fernen Client aus mit Ihrem Peer zu interagieren, müssen Sie den [Fabric-Client "peer"![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "Fabric peer command") herunterladen.

Am einfachsten erhalten Sie den Peer-Client, indem Sie die Binärdateien für die Fabric-Tools aus dem Hyperledger-Projekt herunterladen. Erstellen Sie ein Verzeichnis, in das Sie die Binärdateien mithilfe der Befehlszeile herunterladen wollen, und rufen Sie die Dateien durch Ausgabe des nachfolgenden Befehls ab. Sie müssen zuerst [Curl ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") installieren.

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

Beim Download der Binärdateien wird ein Konfigurationsordner erstellt, der eine Datei "core.yaml", "orderer.yaml" und "configtx.yaml" enthält. Legen Sie den Fabric-Konfigurationspfad zu diesem Konfigurationsverzeichnis fest:

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

Bevor Sie den Peer betreiben können, müssen Sie einige Management-Tasks für die Zertifikate auf Ihrer lokalen Maschine ausführen. Außerdem müssen Sie sicherstellen, dass Sie vom Peer aus auf die TLS-Zertifikate zugreifen können. Weitere Angaben über die zu verwendenden Zertifikate enthält der Abschnitt [Membership Service Providers (MSPs)](CA_operate.html#msp) unter [Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private betreiben](CA_operate.html).

1. Versetzen Sie das signCert-Zertifikat für den Peeradministrator in einen neuen Ordner namens `admincerts`:

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
    mkdir admincerts
    cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. Stellen Sie sicher, dass Sie das [TLS-Zertifikat des Peers heruntergeladen](#peer-tls) haben und in der Befehlszeile darauf verweisen können. Wenn Sie die Beispielbefehle in dieser Dokumentation befolgt haben, befindet sich das TLS-Zertifikat in der Datei `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

3. Sie müssen außerdem auf das TLS-Zertifikat des Anordnungsknotens verweisen. Befolgen Sie als Administrator des Anordnungsknotens die Anweisungen für den [Download des TLS-Zertifikats für den Anordnungsknoten](orderer_operate.html#orderer-tls).  Wenn der Anordnungsknoten von einer anderen Organisation gesteuert wird, muss diese Ihnen das TLS-Zertifikat in einer externen Operation zur Verfügung stellen. Speichern Sie dieses Zertifikat an einer Position, unter der Sie in zukünftigen Befehlen darauf verweisen können.

Durch einen Befehl "tree" können Sie prüfen, ob Sie diese Schritte ausgeführt haben. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Zertifikate gespeichert haben. Der Befehl "tree" sollte ein Ergebnis ähnlich der folgenden Struktur generieren:
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### CLI-Umgebungsvariablen festlegen
{: #environment-variables}

Nachdem alle Zertifikate an die erforderliche Position verschoben wurden, müssen einige Umgebungsvariablen festgelegt werden, die von den Befehlen der CLI  "peer" verwendet werden. Anschließend sind alle Vorbereitungen für den Betrieb des Peers mit dem Fabric-Client "peer" abgeschlossen.

1. Stellen Sie sicher, dass Sie die [Umgebungsvariablen in Ihrer Befehlszeilenschnittstelle](#environment-variables) festgelegt haben.

2. Legen Sie als Fabric-Konfigurationspfad dieses Konfigurationsverzeichnis fest, das beim [Download der Binärdateien für die Fabric-Tools](#peer-client) erstellt wurde:

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    Durch das Festlegen dieser Variablen können Sie die Befehle mit dem Peer-Client in jedem beliebigen Verzeichnis ausführen.

3. Sie benötigen die Endpunktinformationen für den Anordnungsknoten. Als Administrator des Anordnungsknotens können Sie mit diesen Anweisungen die [Endpunktinformationen des Anordnungsknotens abrufen](orderer_operate.html#orderer-endpoint). Wenn der Anordnungsknoten von einer anderen Organisation gesteuert wird, muss diese Ihnen die URL des Anordnungsknotens in einer externen Operation zur Verfügung stellen. 

4. [Rufen Sie die Endpunktinformationen des Peers ab](#peer-endpoint). Diese URL wird zum Festlegen der Umgebungsvariablen `PEERADDR` verwendet. Sie müssen die Angabe `http://` am Anfang weglassen.

5. Führen Sie die folgenden Befehle aus, um die Umgebungsvariablen festzulegen:

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Ersetzen Sie die Feldinhalte durch Ihre eigenen Angaben.
    - Ersetzen Sie `<CHANNEL_NAME>` durch den Namen des Kanals, an dem der Peer teilnimmt.
    - Ersetzen Sie `<CC_NAME>` durch einen Namen, mit dem auf Ihren Chaincode verwiesen wird.
    - Ersetzen Sie `<PEERADDR>` durch den Hostnamen und den Port des Peerendpunkts aus dem vorherigen Schritt.
    - Ersetzen Sie `<ORDERER_URL>` durch den Hostnamen und den Port für den Anordnungsknoten Ihres Konsortiums.
    - Ersetzen Sie `<PATH_TO_ADMIN_MSP>` durch den Pfad zum MSP-Ordner Ihres Peeradministrators.
    - Ersetzen Sie `<MSPID_OF_PEER_BEING_USED>` durch die MSP-ID der Organisation für Ihren Peer, die Sie zum Abrufen eines Genesis-Blocks, zum Beitreten zu einem Kanal oder zum Installieren von Chaincode verwenden. Dieser Wert wurde während der Bereitstellung des Helm-Diagramms angegeben.

  Beispiel:

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  Es kann außerdem sinnvoll sein, TLS zu aktivieren. Hierzu geben Sie Folgendes aus:

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Peer mit der CLI zu einem Kanal hinzufügen
{: #icp-cli-join-peer-to-channel}

Ihre Organisation muss Mitglied eines Kanals sein, damit Sie mit Ihrem Peer dem Kanal beitreten können. Falls Sie nicht Mitglied eines Kanals sind, können Sie die Anweisungen zum [Erstellen eines neuen Kanals](#create-channel) ausführen.

1. Stellen Sie sicher, dass Sie die [Umgebungsvariablen in Ihrer Befehlszeilenschnittstelle](#environment-variables) festgelegt haben.

2. Rufen Sie den Genesis-Block des Kanals ab, um das Kanalledger Ihres Peers zu erstellen. Legen Sie für `CORE_PEER_TLS_ROOTCERT_FILE` den Pfad des TLS-Zertifikats für den Anordnungsknoten fest und führen Sie den folgenden Befehl aus:

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Wenn dieser Befehl erfolgreich ausgeführt wird, wird in etwa das folgende Ergebnis angezeigt:
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **Hinweis:** Möglicherweise werden Sie bei der Ausführung eines dieser CLI-Befehle feststellen, dass die folgende Warnung angezeigt wird, die jedoch ignoriert werden kann.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
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

3. Sobald Sie den Genesis-Block des Kanals abgerufen haben, können Sie den Peer zum Kanal hinzufügen. Legen Sie für `CORE_PEER_TLS_ROOTCERT_FILE` den Pfad des TLS-Zertifikats für den Peer fest und treten Sie mit dem folgenden Befehl zum Kanal bei:

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
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
{: #icp-toolcontainer-install-cc}

Nun sind Sie bereit zur Installation und Instanziierung des Chaincodes auf dem Peer. In den folgenden Anweisungen wird der `fabcar`-Chaincode aus dem Repository `fabric-samples` installiert. Stellen Sie sicher, dass Sie zuvor [GOPATH konfiguriert haben ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Set your GOPATH"), und laden Sie dann den Chaincode namens `fabric-samples` mit den folgenden Befehlen bei GitHub herunter:

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Führen Sie den folgenden Peer-CLI-Befehl aus, um den `fabcar`-Chaincode auf dem Peer zu installieren. Legen Sie für `CORE_PEER_TLS_ROOTCERT_FILE` den Pfad des TLS-Zertifikats für Ihren Peer fest.

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

<!--
**Note:** If this chaincode has already been installed and instantiated on another peer running in IBP Starter or Enterprise Plan, there are additional steps that must be performed before installing the chaincode on the peer. For more instructions about how to avoid errors that are associated with how this chaincode is installed, see the [troubleshooting topic](#icp-cc-install-error).
-->

### Chaincode mit der CLI auf einem Kanal instanziieren
{: #icp-toolcontainer-instantiate-cc}

Da nur ein Peer den Chaincode auf einem Kanal instanziieren muss, müssen Sie den Peer nicht zum Instanziieren des Chaincodes verwenden. Dieser Arbeitsschritt kann von Peers ausgeführt werden, die unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet werden. Wenn jedoch der Peer den Chaincode auf dem Kanal instanziieren soll, können Sie hierzu den folgenden Befehl ausführen. Legen Sie für `CORE_PEER_TLS_ROOTCERT_FILE` den Pfad des TLS-Zertifikats für Ihren Peer fest. Legen Sie als Wert für `--cafile` das TLS-Zertifikat des Anordnungsknotens fest.

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

Nachdem der Chaincode instanziiert wurde, können Sie mit den Chaincode-Befehlen zum Abfragen (query) und Aufrufen (invoke) Daten im Kanalledger lesen und schreiben. Weitere Informationen enthalten die Angaben über die [Chaincode-Befehle für Peers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) in der Dokumentation von Hyperledger Fabric. Sie müssen den Endpunkt des Anordnungsknotens unter Verwendung der Proxy-IP und des externen Anordnungsknotenports an Ihre Aufrufbefehle übergeben. An einen Abfragebefehl muss lediglich der Peerendpunkt übergeben werden.

## Protokolle von Peers in {{site.data.keyword.cloud_notm}} Private anzeigen
{: #peer-log-icp}

Über die ICP-Konsole können Sie auf die Protokolle von Peers zugreifen und diese Protokolle in der Kibana-Schnittstelle überprüfen.

1. Klicken Sie in der ICP-Konsole auf das Symbol **Menü** in der oberen linken Ecke.
2. Klicken Sie in der Menüliste auf **Workloads** > **Helm-Releases**.
3. Klicken Sie in der Tabelle der Helm-Releases auf den Namen Ihres Helm-Release.
4. Blättern Sie abwärts bis zum Abschnitt **Pod** und klicken Sie dann auf den Link **Protokolle anzeigen** am Ende der Tabellenzeile. Die Protokolle fernen Peers werden in der Kibana-Schnittstelle geöffnet.

## Chaincode aktualisieren

Im Zeitverlauf muss der Chaincode, der auf dem Peer ausgeführt wird, möglicherweise geändert werden. Da der Chaincode auf den Peers installiert ist und auf dem Kanal instanziiert wird, müssen Sie den Chaincode auf allen Peers des Kanals aktualisieren.

Führen Sie die folgenden Schritte aus, um den Chaincode zu aktualisieren:

1. Zum Aktualisieren des Chaincodes auf den einzelnen Peers müssen Sie einfach den Prozess erneut ausführen, den Sie zum Installieren des Chaincodes auf den Peers verwendet haben. Dazu können Sie entweder eine Clientanwendung oder einen CLI-Befehl benutzen. Beachten Sie hierbei unbedingt, dass der gleiche Chaincode-Name verwendet werden muss, der ursprünglich verwendet wurde. Allerdings muss dabei die `Version` des Chaincodes erhöht werden. Alle Peers müssen den gleichen Chaincode-Namen und die gleiche Chaincode-Version benutzen.

2. Nach der Installation des neuen Chaincodes auf allen Peers des Kanals können Sie mit dem Befehl für ein [Upgrade des Chaincodes des Peers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) den Kanal aktualisieren, sodass der neue Chaincode verwendet wird.

## In ICP ausgeführten Peer erneut starten
{: #peer-restart}

Wenn ein Zertifikat zu dem Peer hinzugefügt wird, dann muss der Peerknoten neu gestartet werden, damit das neue Zertifikat erkannt werden kann.

Mit den **kubectl**-Befehlen können Sie den Peer, der unter ICP ausgeführt wird, neu starten. Im Befehl müssen der Name für den **Pod** des Peers und der **Container** als Befehlsparameter angegeben werden. Weitere Informationen zur Verwendung der kubectl-Befehle finden Sie im Abschnitt [CLI "kubectl" konfigurieren](#peer-kubectl-configure).

1. Suchen Sie in der ICP-Konsole nach dem Namen für den **Pod** und dem Namen für den **Container** für die Bereitstellung des Peers.

  1. Klicken Sie auf das Menüsymbol in der oberen linken Ecke der Konsole und anschließend auf **Workloads > Bereitstellungen**.
  2. Suchen Sie das Release des Peers in der Tabelle und klicken Sie darauf, um das Element zu öffnen.
  3. Blättern Sie nach unten, um den zugehörigen Namen für den **Pod** anzuzeigen. Notieren Sie den Namen für den **Pod**.
  4. Klicken Sie auf den Pod, um ihn zu öffnen.
  5. Klicken Sie auf die Registerkarte **Container**. Notieren Sie den Namen für den **Container** Ihres Peers.

2. Führen Sie in der Befehlszeile den folgenden Befehl aus, um für den Peer einen Neustart durchzuführen. Ersetzen Sie dabei `<PEER_POD_NAME>` und `<PEER_CONTAINER_NAME>` durch die oben angegebenen Werte.

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. Sie können überprüfen, ob der Peer neu gestartet wurde, indem Sie den Befehl `kubectl get pods` ausführen und die Ausgabe auf den Zähler für **RESTART** Ihres Pods überprüfen.

## Kanal erstellen
{: #create-channel}

Wenn Ihre Organisation noch nicht Mitglied eines Konsortiums oder eines Kanals ist, können Sie die nachfolgenden Schritte verwenden, um einen Kanal zu erstellen. Sie können diese Schritte auch verwenden, um einen vorhandenen Kanal zu aktualisieren. Die Anweisungen führen Sie durch die [Vorbereitung einer Organisationsdefinition](#organization-definition). Diese Definition macht Sie zu einem [Mitglied des Konsortiums](orderer_operate.html#add-organizations-to-consortium), indem Sie zu einem Systemkanal eines Anordnungsknotens hinzugefügt werden. Anschließend können Sie einen neuen Kanal erstellen, indem Sie eine [Kanaltransaktion erstellen](#peer-icp-channeltx).

Wenn Sie dem Konsortium bereits beigetreten sind, müssen Sie nur den Schritt zur Vorbereitung der Kanaltransaktion ausführen. Ein anderes Mitglied des Konsortiums kann ebenfalls einen neuen Kanal erstellen, dessen Mitglied Ihre Organisation ist. Sie können die Organisationsdefinition aktualisieren, wenn Sie Aktualisierungen an das Konsortium senden müssen, beispielsweise zum Definieren neuer Ankerpeers, zum Hinzufügen neuer Administratorzertifikate oder zum Aktualisieren Ihres Verschlüsselungsmaterials.

### Tool "configtxgen" herunterladen
{: #peer-icp-configtxgen}

Falls Ihre Organisation zu einem Konsortium oder Kanal beitreten muss, müssen Sie das Tool [configtxgen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen") herunterladen.

Am einfachsten erhalten Sie das Tool "configtxgen", indem Sie alle Binärdateien für die Fabric-Tools aus dem Hyperledger-Projekt herunterladen. Navigieren Sie zu einem Verzeichnis, in das Sie die Binärdateien mithilfe der Befehlszeile herunterladen wollen, und rufen Sie die Dateien durch Ausgabe des nachfolgenden Befehls ab. Sie müssen zuerst [Curl ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") installieren.

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

Dieser Befehl installiert das Tool "configtxgen" in einem Verzeichnis namens `bin/`. Legen Sie für `PATH` den Pfad zu dem Verzeichnis fest, in das Sie die Binärdateien für die Fabric-Tools heruntergeladen haben:

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

Falls Sie die Binärdateien beispielsweise in Ihrem Ausgangsverzeichnis installiert haben, würden Sie `PATH` wie folgt festlegen:

```
export PATH=$PATH:$HOME/bin
```

## Organisationsdefinition vorbereiten
{: #organization-definition}

Sobald Sie Ihre Komponenten bereitgestellt haben, kann Ihre Organisation einem Konsortium beitreten, indem eine Organisationsdefinitionsdatei vorbereitet wird. Die Definition enthält alle Informationen, die Sie für die Teilnahme an der Governance des Konsortiums benötigen, z. B. durch das Erstellen oder Verbinden neuer Kanäle, das Hinzufügen von Peers zu Kanälen oder die Instanziierung von Chaincode. Die Definition enthält Ihren Organisationsnamen, die MSP-ID und MSP-Zertifikate. Dieser Schritt muss von allen Organisationen ausgeführt werden.

### Verschlüsselungsmaterial vorbereiten

Bevor Sie eine Organisationsdefinition vorbereiten, müssen Sie sich registrieren und den [Administrator Ihrer Peers](CA_operate.html#register-admin) eintragen. Navigieren Sie zu dem Verzeichnis, in dem Sie den MSP-Ordner für den Peeradministrator erstellt haben. Bei den Beispielschritten wurde dieser Ordner an der Position `$HOME/fabric-ca-client/peer-admin/msp` erstellt. Innerhalb dieses Ordners müssen Sie einige zusätzliche Schritte ausführen, damit der MSP durch das Tool `configtxgen` verwendet werden kann.

1. Kopieren Sie Ihr TLS-Zertifikat aus Ihrer Zertifizierungsstelle und legen Sie es in einem neuen Ordner namens `tlscacerts` ab.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  Anschließend müssen Sie das Zertifikat in das von Ihnen erstellte Verzeichnis `tlscacerts` kopieren:

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  Ihr Befehl könnte beispielsweise so aussehen:

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. Versetzen Sie Ihr signCert-Zertifikat aus dem Ordner `signcerts` in einen neuen Ordner namens `admincerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

Das Tool `configtxgen` erstellt die Definition durch die Verarbeitung einer Datei "configtx.yaml". Eine Beispielversion dieser Version ist nachfolgend dargestellt:

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

Diese Datei enthält die Informationen, die Ihre Organisation innerhalb des Konsortiums definieren. Eine komplexere Version dieser Datei ist außerdem im Ordner `/config` des [heruntergeladenen Fabric-Clients "peer"](peer_operate_icp.html#peer-client) verfügbar. Sie können diese Datei bearbeiten, aber auch durch das obige Beispiel ersetzen. Notieren Sie sich die Position dieses Ordners `/config`, die nachfolgend als Wert für `FABRIC_CFG_PATH` festgelegt wird. Bearbeiten Sie den Abschnitt `Organizations` dieser Datei und legen Sie die folgenden Werte fest:

- `Name:` Diesen Wert können Sie für Ihre Organisation frei wählen.

- `ID:` Die Werte Ihrer MSP-ID werden zur eindeutigen Kennung für Ihre Organisation. Dieser MSP-Wert für die Organisation wurde angegeben, als Sie das Helm-Diagramm für Peers bereitgestellt haben. Er ist auch innerhalb des Peer-Containers sichtbar, wenn Sie die folgenden Befehle ausführen und hierbei `<peer pod name>` durch den Wert für den Pod Ihres Peers ersetzen:

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  Die Ausgabe könnte wie folgt aussehen:
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  Der Wert für die MSP-`ID` lautet somit "org1".

- `MSPDir:` Dies ist der vollständig qualifizierte Pfad zu Ihrem Administrator-MSP. Beispiel: `/Users/chandra/fabric-ca-client/peer-admin/msp`

- `AnchorPeers:` Dies ist der Peer, den Ihre Organisation als primären Peer für die Kommunikation zwischen Organisationen über Gossip auswählt. Er wird für Funktionen wie private Daten oder Serviceerkennung verwendet. Private Daten und die Serviceerkennung werden zwar gegenwärtig nicht durch das Helm-Diagramm für Peers unterstützt, aber das Feld ist dennoch erforderlich. Die Werte für `Host` und `Port` sind auf der Seite des Helm-Release für ICP-Peers unter dem Hinweisabschnitt `1. Rufen Sie die Anwendungs-URL mit den folgenden Befehlen ab` verfügbar. Die Ausgabe könnte wie folgt aussehen:

```
http://9.30.250.70:30481
```

Hierbei lauten die Werte wie folgt: `Host:` = `9.30.250.70` und `Port:` = `30481`.

Verwenden Sie anschließend den folgenden Befehl, um die Organisationsdefinition auszugeben:

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

Ihr Aufruf könnte ähnlich wie der folgende Beispielbefehl aussehen:

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

Wenn dieser Befehl erfolgreich ausgeführt wird, gibt das Tool `configtxgen` die Organisationsdefinition im JSON-Format aus. Diese Datei müssen Sie in einer externen Operation an die Organisation des Anordnungsknotens senden, um dem Konsortium beizutreten. Die Organisation des Anordungsknotens kann anschließend [ein Konsortium gründen oder einem bestehenden Konsortium beitreten](orderer_operate.html#consortium), indem die Definition zum Systemkanal hinzugefügt wird; hierdurch können Sie neue Kanäle erstellen und von anderen Konsortiumsmitgliedern zu Kanälen hinzugefügt werden.

## Kanaltransaktion erstellen
{: #peer-icp-channeltx}

Bevor Sie einen neuen Kanal erstellen können, muss Ihre Organisation eine [Organisationsdefinition](#organization-definition) vorbereitet haben und Mitglied des Konsortiums geworden sein. Befolgen Sie diese Anweisungen, wenn Sie ein [Konsortium gründen oder einem Konsortium beitreten](orderer_operate.html#consortium) wollen. Mitglieder des Konsortiums können ohne großen Aufwand zu neuen Kanälen hinzugefügt werden, falls ihre Organisation bereits zum Systemkanal hinzugefügt wurde. Organisationen, die nicht Mitglied des Systemkanals sind, können einem Kanal nur manuell beitreten, indem sie ihre Organisationsdefinition mithilfe einer [Kanalaktualisierungsanforderung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html) hinzufügen. Sie können diese Schritte auch verwenden, um einen vorhandenen Kanal zu aktualisieren.

### Neuen Kanal gründen
{: #peer-icp-create-channel}

Um einen neuen Kanal zu gründen, muss eine Organisation einen Transaktionsvorschlag für eine Kanalaktualisierung mit dem Tool [configtxgen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen") erstellen. Dieses Tool verarbeitet eine Datei `configtx.yaml`, in der die Mitglieder des neuen Kanals definiert sind. Ein Beispiel für die Datei `configtx.yaml` ist nachfolgend dargestellt. Eine komplexere Version dieser Datei ist außerdem im Ordner `/config` des [heruntergeladenen Fabric-Clients "peer"](peer_operate_icp.html#peer-client) verfügbar. Sie können diese Datei bearbeiten, aber auch durch das Beispiel ersetzen. Notieren Sie sich die Position dieses Ordners `/config`, die nachfolgend als Wert für `FABRIC_CFG_PATH` festgelegt wird. 
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

Für die Erstellung eines neuen Kanals sind die drei Abschnitte **Organizations**, **Capabilities** und **Profiles** relevant:

- **Organizations:** In diesem Abschnitt sind alle Mitglieder der Organisation definiert. Jede Organisation besitzt einen Anker, z. B. `&Org1`. Unter diesem Anker sind der Name, die MSP-ID, das Verzeichnis des MSP-Ordners und die Ankerpeers der Organisation für die peerübergreifende Kommunikation mit Gossip zu finden. Mit den folgenden Schritten können Sie das Organisationsprofil für jedes Mitglied des Konsortiums ausfüllen:
  1. Geben Sie `Name` und `ID` der Organisation unter Verwendung der MSP-ID an. Die Organisation, die den Kanal erstellt, muss die MSP-ID kennen, die beim Bereitstellen des Helm-Diagramms für Peers angegeben wurde.
  2. Geben Sie bei `MSPDir` den vollständig qualifizierten Pfad zum MSP-Ordner an, den Sie zum Erstellen der [Organisationsdefinition](#organization-definition) verwendet haben. Bitte beachten Sie, dass Ihr Verschlüsselungsmaterial nicht in der Transaktion für die Kanalerstellung verwendet wird. Das Tool *configtxgen* ignoriert den tatsächlichen Inhalt des MSP. Es erwartet jedoch an dieser Position einen MSP-Ordner mit der korrekten Ordnerunterstruktur.
  3. Mit dem Parameter `AnchorPeers` wird der von jeder Organisation verwendete primäre Peer für die organisationsübergreifende Kommunikation mittels Gossip angegeben. Geben Sie den Hostnamen und den Port des Peers an, der als [Ankerpeer ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html) für diese Organisation dient. Dieser Wert ist für die Verwendung von Funktionen wie private Daten oder Serviceerkennung wichtig. Private Daten und die Serviceerkennung werden jedoch gegenwärtig nicht durch das Helm-Diagramm für Peers unterstützt.

- **Capabilities:** Dieser Abschnitt muss in der Datei `configtx.yaml` enthalten sein, aber es sind keine Änderungen erforderlich.

- Der Abschnitt **Profiles:** enthält die Informationen, die zum Erstellen eines neuen Kanals benötigt werden. Das Profil enthält die folgenden Informationen:
  1. Den Namen des neuen Kanals.
  2. Den Namen des Konsortiums, das im Systemkanal definiert ist und zum Erstellen des Kanals verwendet wird.
  3. Die Liste der Organisationen, die zum Kanal hinzugefügt werden. Die aufgeführten Organisationsnamen verwenden die im Abschnitt `Organizations` aufgelisteten Ankerpeers, um nach den MSP-IDs der aufgeführten Organisationen und den zugehörigen Ankerpeers zu suchen.

  Das Tool *configtxgen* erstellt mithilfe des Abschnitts `Profiles` den Vorschlag für die Kanalerstellung.

Nachdem Sie die Konfigurationsdatei aktualisiert haben, erstellen Sie mit dem folgenden Befehl den Transaktionsvorschlag für die Kanalaktualisierung:

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

Ein echter Befehl könnte wie im folgenden Beispiel aussehen:
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


Wenn dieser Befehl erfolgreich ausgeführt wird, wird ein Ergebnis angezeigt, das Ähnlichkeit mit der folgenden Ausgabe hat. Der Befehl schreibt den Transaktionsvorschlag für die Kanalaktualisierung in das Verzeichnis `FABRIC_CFG_PATH`.

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

Falls Ihre Bewilligungsrichtlinie keine zusätzlichen Signaturen von anderen Mitgliedern des Konsortiums erforderlich macht, können Sie den Vorschlag direkt an den Anordnungsknoten übergeben, um den neuen Kanal zu gründen. Führen Sie zum Erstellen des Kanals den folgenden Befehl an derselben Position aus, an der Sie auch die Transaktion für die Kanalaktualisierung generiert haben.

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

Hierbei steht `<ORG_MSPID>` für die MSP-ID Ihrer Organisation und `<PATH_TO_ADMIN_MSP>` für den Pfad zum MSP-Ordner des Peeradministrators. Innerhalb des Befehls ist `<orderer_url>` die URL des Anordnungsknotens, der den Kanal erstellt, und `<orderer_tls_cert>` der Pfad zum TLS-Zertifikat des Anordnungsknotens. Ein echter Befehl könnte wie im folgenden Beispiel aussehen:
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## Vorschlag bewilligen und Anforderung übergeben

Nachdem der Vorschlag für die Erstellung eines neuen Kanals von genügend Organisationen signiert wurde und somit die Bewilligungsrichtlinie des Systemkanals erfüllt, können Sie die Transaktion für die Kanalaktualisierung an den Anordnungsknoten übergeben, um den neuen Kanal zu gründen.

Mit den APIs von Node Fabric SDK können Sie Signierung und Übergabe in einer einzigen Transaktion vornehmen. Weitere Informationen bietet das Lernprogramm [How to create a Hyperledger Fabric Channel ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) in der Dokumentation von Node SDK.

## Protokolle des Peers anzeigen
{: #peer-icp-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [`Befehlen der CLI "kubectl"`](#peer-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem ICP-Cluster enthalten ist. 

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

- Alternativ können Sie auf Protokolle auch über die [ICP-Konsole für das Cluster-Management](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html) zugreifen, die die Protokolle in Kibana öffnet.

  **Hinweis:** Wenn Sie Ihre Protokolle in Kibana anzeigen, empfangen Sie möglicherweise die Antwort `Keine Ergebnisse gefunden`. Diese Bedingung kann auftreten, wenn ICP die IP-Adresse Ihres Workerknotens als Hostnamen verwendet. Entfernen Sie zur Lösung dieses Problems oben in der Anzeige den Filter, der mit `node.hostname.keyword` beginnt. Anschließend sind die Protokolle sichtbar.


## Fehlerbehebung
{: #peer-icp-troubleshooting}

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
{:codeblock}
2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the selected version is compatible with ICP 2.1.0.3. `v2.7.2 ` is recommended.

3.  After the helm installation completes, [login](#peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```
 {:codeblock}
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
 {:codeblock}

5. Check helm version:

  ```
  helm version --tls
  ```
  {:codeblock}

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
{:codeblock}

Der Befehl schlägt fehl und erzeugt eine Fehlernachricht, die ähnlich wie das folgende Beispiel lautet:

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Lösung:**

Dieser Fehler tritt auf, wenn auf Big Endian-Systemen (z. B. IBM System Z) zwei Docker-Bash-Sitzungen in demselben Docker-Container geöffnet werden. Die zweite Bash-Sitzung kann in diesem Fall möglicherweise keine Verbindung zum aktiven Container herstellen. Zur Behebung dieses Problems müssen Sie einen [Neustart des Peer-Containers durchführen](#peer-restart) und die Ausführung des Befehls `kubectl exec` nach dem Neustart des Peers wiederholen.

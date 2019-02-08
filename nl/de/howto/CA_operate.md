---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private betreiben
{: #ca-operate}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Zertifizierungsstellen (CAs) stellen Identitäten im Netz zur Verfügung. Eine Zertifizierungsstelle ist mit einem öffentlich anerkannten Notar vergleichbar, der zwischen mehreren Parteien als Vertrauensperson fungiert. Jede Entität im Netz erhält ein von der Stammzertifizierungsstelle (Root-CA) signiertes Zertifikat, in dem die digitale Identität der Entität enthalten ist. Dieses Zertifikat stellt die Vertrauensgrundlage für alle Signier- und Verifizierungsoperationen dar, die innerhalb des Netzes ausgeführt werden.
{:shortdesc}

Jede Organisation in einem Blockchain-Netz mit mehreren Clouds muss ihre eigene Zertifizierungsstelle bereitstellen. Die Zertifizierungsstelle Ihrer Organisation signiert Anforderungen für die Komponenten, die Ihr Unternehmen betreibt, z. B. Anordnungsservice, Peers oder Anwendungen. Daher müssen Sie Ihre Zertifizierungsstelle bereitstellen, bevor Sie andere Komponenten bereitstellen.

Für den Betrieb Ihrer Zertifizierungsstelle sind einige vorausgesetzte Schritte auszuführen:

- [CLIs konfigurieren](#ca-kubectl-configure)
- [URL der Zertifizierungsstelle abrufen](#ca-url)
- [TLS-Zertifikat der Zertifizierungsstelle herunterladen](#ca-tls)
- [Fabric-CA-Client einrichten](#fabric-ca-client)
- [Zertifikate als CA-Administrator generieren](#enroll-admin)

Nachdem Sie die vorausgesetzten Schritte ausgeführt haben, können Sie mithilfe Ihrer Zertifizierungsstelle neue Identitäten im Netz bei Ihrer Organisation registrieren und Zertifikate generieren, die von Ihren Anwendungen verwendet werden können.

- [Anordnungsknoten oder Peer mithilfe der Zertifizierungsstelle bereitstellen](#deploy-orderer-peer)
<!-- [Register and enroll client applications](#register-enroll-app)-->

Das vorliegende Handbuch bietet auch einige wichtige Informationen zu Konzepten für den Betrieb der Zertifizierungsstelle und die Verwaltung eines Blockchain-Netzes unter Verwendung ihrer Zertifikate.

- [Membership Service Providers (MSPs)](#msp)
- [TLS-Zertifikate](#tls)

## Voraussetzungen
{: #prerequisites}

Unabhängig davon, ob Sie ein Netz gründen oder einem Netz beitreten, müssen Sie die folgenden vorausgesetzten Schritte ausführen, damit Sie Ihre Zertifizierungsstelle verwenden und andere Komponenten sowie Identitäten bereitstellen können, die zu Ihrer Organisation gehören.

### CLIs konfigurieren
{: #ca-kubectl-configure}

Sie müssen mit dem Befehlszeilentool **kubectl** eine Verbindung zu dem in ICP ausgeführten Container für Zertifizierungsstellen herstellen.

1. Melden Sie sich bei der Benutzerschnittstelle Ihres ICP-Clusters an. Navigieren Sie zur Registerkarte **Befehlszeilentools** und klicken Sie auf **Cloud Private-CLI**. Daraufhin werden die folgenden Tools angezeigt, die Sie herunterladen können.

   * IBM Cloud Private-CLI und Plug-ins installieren
   * CLI "kubectl" installieren
   * Helm installieren
   * Istio-CLI installieren

  Zum Betrieb einer Zertifizierungsstelle benötigen Sie die ersten **drei** Tools, wobei Helm optional ist. Klicken Sie auf jedes Tool und führen Sie die `curl`-Befehle für den von Ihnen verwendeten Maschinentyp aus. Geben Sie anschließend für jedes Tool die Befehle `chmod` und `sudo mv` aus. Der Befehl `chmod` ändert die Berechtigung der entsprechenden CLI, damit sie ausführbar wird; der Befehl `sudo mv` versetzt die Datei und benennt sie um.

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

  Falls der Befehl erfolgreich ausgeführt wurde, sollte er eine Antwort zurückgeben, die dem folgenden Beispiel ähnelt, wobei `ca-6cf85f6687-hcxpl` der Name des Containers für Zertifizierungsstellen ist.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Nun können Sie das Tool **kubectl** verwenden, um die URL der Zertifizierungsstelle abzurufen.

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

### URL der Zertifizierungsstelle abrufen
{: #ca-url}

Anforderungen zum Generieren von Zertifikaten oder zum Registrieren einer neuen Identität müssen an die URL der Zertifizierungsstelle gerichtet werden. Die URL Ihrer Zertifizierungsstelle können Sie in der Benutzerschnittstelle der ICP-Konsole ermitteln. Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um die folgenden Schritte ausführen zu können:

1. Melden Sie sich bei der ICP-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**. 
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Im Abschnitt **Hinweise** ist eine Reihe von Befehlen aufgeführt, die Sie beim Betrieb der Bereitstellung Ihrer Zertifizierungsstelle unterstützen.
5. Führen Sie, sofern noch nicht geschehen, die Schritte zum Konfigurieren der [CLI "kubectl"](#ca-kubectl-configure) aus; Sie benötigen die CLI für die Interaktion mit dem Container für Ihre Zertifizierungsstelle.
6. Führen Sie in der CLI den ersten Befehl in den Hinweisen aus, der nach **1. Rufen Sie die Anwendungs-URL mit den folgenden Befehlen ab:** angegeben ist. Dieser Befehl gibt die URL und den Port für die Zertifizierungsstelle aus, der ähnlich wie im folgenden Beispiel lautet. Speichern Sie diesen Wert als Anwendungs-URL zur Verwendung bei den nachfolgenden Befehlen.

  ```
  http://9.30.94.174:30167
  ```

**Hinweis:** Wenn Sie Ihre Zertifizierungsstelle hinter einer Firewall bereitstellen, müssen Sie einen Durchgriff öffnen, damit das Netz auf der Plattform einen TLS-Handshake mit Ihrer Zertifizierungsstelle durchführen kann. Der Durchgriff muss nur für den Port aktiviert werden, der mit der URL der Zertifizierungsstelle verkettet ist.


### TLS-Zertifikat der Zertifizierungsstelle abrufen
{: #ca-tls}

Sie müssen das TLS-Zertifikat Ihrer Zertifizierungsstelle herunterladen und es zusammen mit Ihren Befehlen übergeben, damit Sie von einem fernen Client aus mit Ihrer Zertifizierungsstelle kommunizieren können.

1. Melden Sie sich bei der ICP-Konsole an. Klicken Sie auf das Symbol **Menü** in der oberen linken Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**. 
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Im Abschnitt **Hinweise** ist eine Reihe von Befehlen aufgeführt, die Sie beim Betrieb der Bereitstellung Ihrer Zertifizierungsstelle unterstützen.
5. Führen Sie, sofern noch nicht geschehen, die Schritte zum Konfigurieren der [CLI "kubectl"](#ca-kubectl-configure) aus; Sie benötigen die CLI für die Interaktion mit dem Container für Ihre Zertifizierungsstelle.
6. Führen Sie in der CLI den Befehl im dritten Hinweis aus, der auf **3. TLS-Zertifikat abrufen:** folgt. Dieser Befehl speichert Ihr TLS-Zertifikat als Datei `tls.pem` in Ihrem lokalen Verzeichnis. Auf dieses Zertifikat müssen Sie in einem späteren Befehl verweisen. Notieren Sie sich die Position.
7. Der Befehl konvertiert außerdem das Zertifikat in das Base64-Format und gibt es aus. Das Ergebnis hat Ähnlichkeit mit der folgenden Zeichenfolge:

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  **Wichtig:** Sie müssen diese Zeichenfolge kopieren und sie als `TLS-Zertifikat für Ihre ICP-Zertifizierungsstelle` speichern. Sie benötigen es, wenn Sie zusätzliche Komponenten bereitstellen.

### Fabric-CA-Client einrichten
{: #fabric-ca-client}

Zum Betrieb Ihrer Zertifizierungsstelle können Sie den Fabric-CA-Client verwenden. Die folgenden Anweisungen erläutern, wie Sie mit dem Fabric-CA-Client Identitäten für andere Komponenten eintragen und registrieren, die zu Ihrer Organisation gehören. Zur Ausführung der vorausgesetzten Schritte können Sie auch die Fabric-SDKs verwenden.

1. Sie müssen den [Fabric-CA-Client ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-client "Download Fabric CA client") in Ihr lokales Dateisystem herunterladen.

  Am einfachsten erhalten Sie den Fabric-CA-Client, indem Sie alle Binärdateien für die Fabric-Tools direkt herunterladen. Navigieren Sie zu einem Verzeichnis, in das Sie die Binärdateien mithilfe der Befehlszeile herunterladen wollen, und rufen Sie die Dateien durch die Ausführung des folgenden [curl-Befehls ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") ab.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

  Dieser Befehl installiert die Binärdateien in einem Verzeichnis namens `bin/`.

2. Legen Sie für `PATH` den Pfad zu dem Verzeichnis fest, in das Sie die Binärdateien für die Fabric-Tools heruntergeladen haben:

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  Falls Sie die Binärdateien beispielsweise in Ihrem Ausgangsverzeichnis installiert haben, würden Sie `PATH` wie folgt festlegen:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Erstellen Sie den Ordner, in dem Sie die Zertifikate Ihres CA-Administrators speichern wollen.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. Legen Sie als Wert der Umgebungsvariablen `$FABRIC_CA_CLIENT_HOME` den Pfad fest, in dem der CA-Client die generierten [MSP](#msp)-Zertifikate speichert. Achten Sie darauf, das Konfigurationsmaterial zu entfernen, das möglicherweise bei früheren Versuchen erstellt wurde. Falls Sie den Befehl `enroll` zuvor noch nie ausgeführt haben, sind der Ordner `msp` und die Datei `.yaml` nicht vorhanden.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Kopieren Sie das [von ICP heruntergeladene](#ca-tls) TLS-Zertifikat in ein Verzeichnis, in dem Sie bei künftigen Befehlen darauf verweisen können. Stellen Sie sicher, dass Sie sich im Verzeichnis `$HOME/fabric-ca-client` befinden.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### Zertifikate als CA-Administrator generieren
{: #enroll-ca-admin}

Bevor Sie Komponenten oder Clientanwendungen mit Ihrer Zertifizierungsstelle bereitstellen, müssen Sie Zertifikate generieren, die Sie als Administrator authentifizieren, der zum Registrieren neuer Benutzer berechtigt ist. Der Prozess zum Generieren der erforderlichen Zertifikate, Ihres privaten Schlüssels und Ihres öffentlichen Zertifikats (sog. Eintragungszertifikat oder signCert-Zertifikat) wird als **Eintragung** bezeichnet. 

Die Generierung von Zertifikaten ist nur unter Verwendung von Identitäten möglich, die bei Ihrer Zertifizierungsstelle registriert wurden. Geben Sie den Namen und den geheimen Schlüssel der Identität an, um Zertifikate zu generieren. Beim Bereitstellen Ihrer Zertifizierungsstelle wurde automatisch die Identität eines **CA-Administrators** registriert. Sie können nun den Namen und das Kennwort dieses Administrators verwenden, um mit dem Fabric-CA-Client einen Befehl `enroll` zum Generieren eines MSP-Ordners mit Zertifikaten auszugeben, die anschließend zum Registrieren anderer Identitäten für Peers oder Anordnungsknoten verwendet werden.

1. Stellen Sie sicher, dass Sie die Schritte für die [Einrichtung des Fabric-CA-Clients](#fabric-ca-client) ausgeführt und für `$FABRIC_CA_CLIENT_HOME` das Verzeichnis festgelegt haben, in dem Sie die Zertifikate für den CA-Administrator speichern wollen.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. Führen Sie den folgenden Befehl aus, um Zertifikate zu generieren:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  Die Angaben `<enroll_id>` und `<enroll_password>` im Befehl stehen für [den Benutzernamen und das Kennwort des CA-Administrators](/docs/services/blockchain/howto/CA_deploy_icp.html#admin-secret), die Sie beim Bereitstellen der Zertifizierungsstelle an den geheimen Kubernetes-Schlüssel übergeben haben. Fügen Sie die [URL der Zertifizierungsstelle](#ca-url) in `<ca_url_with_port>` ein. Lassen Sie die Angabe `http://` am Beginn weg. `<ca_name>` steht für den Wert, den Sie beim Bereitstellen der Zertifizierungsstelle im Feld `CA-Name` angegeben haben.

  `<ca_tls_cert_path>` steht für den vollständigen Pfad des [TLS-Zertifikats der Zertifizierungsstelle](#ca-tls).

  Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Der Befehl `enroll` generiert einen vollständigen Satz von Zertifikaten, der auch als "MSP-Ordner" (MSP = Membership Service Provider) bezeichnet wird und sich innerhalb des Verzeichnisses befindet, das Sie als Pfad von `$HOME` für Ihren Fabric-CA-Client festgelegt haben. Beispiel: `$HOME/fabric-ca-client/ca-admin`. Weitere Informationen zu MSPs und zum Inhalt des MSP-Ordners finden Sie unter [Membership Service Providers (MSPs)](#msp).

  Falls der Befehl `enroll` fehlschlägt, finden Sie unter [Fehlerbehebung](#ca-enroll-error) Informationen zu möglichen Ursachen.

Durch einen Befehl "tree" können Sie prüfen, ob Sie alle vorausgesetzten Schritte ausgeführt haben. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Zertifikate gespeichert haben. Der Befehl "tree" sollte ein Ergebnis ähnlich der folgenden Struktur generieren:

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
└── catls
    └── tls.pem    
```

## Zertifizierungsstelle zum Bereitstellen eines Anordnungsknotens oder Peers verwenden
{: #deploy-orderer-peer}

Bevor Sie einen Anordnungsknoten oder einen Peer bereitstellen, müssen Sie eine JSON-Konfigurationsdatei erstellen, die wichtige Informationen zur Identität der Komponente und zur Zertifizierungsstelle enthält. Anschließend müssen Sie diese Datei während der Konfiguration mithilfe eines [Kubernetes-Objekts für einen geheimen Schlüssel ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/configuration/secret/) an das Helm-Diagramm übergeben. Diese Datei ermöglicht es dem Anordnungsknoten oder Peer, von der Zertifizierungsstelle die Zertifikate abzrufen, die für den Beitritt zu einem Blockchain-Netz benötigt werden. Außerdem enthält diese Datei ein Administratorzertifikat, mit dem Sie Ihre Komponente als Benutzer mit Administratorberechtigung betreiben können.

Die folgenden Anweisungen bieten Ihnen eine [Vorlage für die JSON-Konfigurationsdatei](#config-file-template), die Sie bearbeiten und in Ihrem lokalen Dateisystem speichern können. Außerdem erfahren Sie, wie Sie diese Datei mithilfe Ihrer Zertifizierungsstelle erstellen.

- Befolgen Sie die nachstehenden Anweisungen, wenn Sie einen Anordnungsknoten in ICP oder einen Peer für eine Verbindung zu einem in ICP gehosteten Konsortium bereitstellen.
- Falls Sie einen Peer für die Vebindung zu einem Starter Plan oder Enterprise Plan bereitstellen wollen, befolgen Sie stattdessen die Anweisungen im Abschnitt [Peers in IBM Cloud Private zur Verbindung mit Starter Plan oder Enterprise Plan bereitstellen](/docs/services/blockchain/howto/peer_deploy_ibp.html). Die dortigen Schritte führen Sie durch die Bereitstellung von Peers in ICP mithilfe der Zertifizierungsstelle von {{site.data.keyword.blockchainfull_notm}} Platform.

### Konfigurationsdatei
{: #config-file-template}

Die Vorlage für die Konfigurationsdatei ist nachfolgend dargestellt:
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

Kopieren Sie diese gesamte Datei in einen Texteditor, in dem Sie sie bearbeiten und als JSON-Datei in Ihrem lokalen Dateisystem speichern können.

### Verbindungsinformationen der Zertifizierungsstelle

Zunächst müssen Sie die Verbindungsinformationen Ihrer Zertifizierungsstelle in ICP angeben. Füllen Sie mithilfe der folgenden Informationen die Werte im Abschnitt `"component"` der Datei aus:

- Die Werte für `"cahost"` und `"caport"` sind die URL und der Port aus der [URL der Zertifizierungsstelle](#ca-url). Lautet beispielsweise die URL Ihrer Zertifizierungsstelle "http://9.30.94.174:30167" wäre `9.30.94.174` der Wert für `"cahost"` und `30167` der Wert für `"caport"`.
- Der Wert für `"caname"` ist der Name der Zertifizierungsstelle, den Sie beim Bereitstellen des Helm-Diagramms für die Zertifizierungsstelle angegeben haben. Auf den Namen der Zertifizierungsstelle (CA-Name) haben Sie bei der Eintragung mithilfe der [Identität des CA-Administrators](#enroll-admin) verwiesen.
- Der Wert für `"cacert"` ist das in Base64 codierte TLS-Zertifikat Ihrer Zertifizierungsstelle. Unter Verwendung des Hinweises in Ihrem Helm-Release können Sie in der ICP-Konsole das [TLS-Zertifikat Ihrer Zertifizierungsstelle abrufen](#ca-tls); fügen Sie die Base64-Ausgabe der Befehle in den Hinweisen im folgenden Abschnitt ein.
  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  Nach der Aktualisierung sieht das Feld `"cacert"` ungefähr wie im folgenden Beispiel aus:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

- In den Feldern im nachfolgenden Abschnitt `"tls"` sind dieselben Informationen erforderlich wie in den obigen Abschnitten für die Komponenten. Die einzige Ausnahme besteht darin, dass Sie den Wert des TLS-Instanznamens für die Zertifizierungsstelle verwenden müssen, der während der Bereitstellung der Zertifizierungsstelle angegeben wird. Verwenden Sie dasselbe TLS-Zertifikat als Wert für `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

Nachdem Sie die Verbindungsinformationen für die Zertifizierungsstelle abrufen haben, müssen Sie mit dem Fabric-CA-Client einige operative Schritte ausführen:

1. [Registrieren Sie die neue Komponente bei Ihrer Zertifizierungsstelle](#register-component).

2. Außerdem müssen Sie einen [Komponentenadministrator registrieren](#register-admin) und anschließend innerhalb eines MSP-Ordners [Zertifikate für den Komponentenadministrator generieren](#enroll-admin). Falls Sie bereits einen Administrator für die Bereitstellung einer anderen Komponente registriert haben, müssen Sie diesen Schritt nicht ausführen.

3. [Registrieren Sie die neue Komponente bei Ihrer TLS-Zertifizierungsstelle](#tls-register-component).

### Komponentenidentität bei der Zertifizierungsstelle registrieren
{: #register-component}

Falls Sie ein Konsortium gründen wollen, indem Sie einen Anordnungsservice bereitstellen und diesem dann Organisationen hinzufügen, oder falls Sie Peers bereitstellen und diese an Kanälen teilnehmen lassen wollen, müssen Sie zuerst die Komponentenidentität bei Ihrer Zertifizierungsstelle registrieren. Ihre Komponentenbereitstellung kann anschließend Zertifikate generieren, die der Peer oder Anordnungsknoten zur Teilnahme an einem Netz benötigt.

1. [Generieren Sie Zertifikate als CA-Administrator](#enroll-ca-admin); hierzu verwenden Sie den Fabric-CA-Client. Verwenden Sie diese Administratorzertifikate, um die folgenden Befehle auszugeben. Stellen Sie sicher, dass für `$FABRIC_CA_CLIENT_HOME` der Wert `$HOME/fabric-ca-client/ca-admin` festgelegt ist.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. Geben Sie den folgenden Befehl aus, um Ihre Zugehörigkeit und Ihren Organisationsnamen zu ermitteln.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Ihr Befehl könnte wie im folgenden Beispiel aussehen:
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Daraufhin werden die folgenden Informationen angezeigt:
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  Notieren Sie sich den zweiten Wert für **affiliation**, z. B. `org1.department1`. Sie müssen diesen Wert im folgenden Befehl verwenden.

3. Führen Sie den folgenden Befehl aus, um den Anordnungsknoten oder Peer zu registrieren.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Erstellen Sie einen Namen und ein Kennwort für die Komponente und ersetzen Sie `name` und `secret` durch diese Werte. **Wichtig:** Notieren Sie sich diese Informationen. Legen Sie für `--id.type` den Wert `orderer` fest, falls Sie einen Anordnungsknoten bereitstellen; legen Sie `peer` fest, falls Sie einen Peer bereitstellen. Der Befehl könnte wie im folgenden Beispiel aussehen:

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer --id.secret peerpw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Nachdem Sie diesen Befehl ausgeführt haben, müssen Sie die Werte für `name` und `secret` bei `"enrollid"` und `"enrollsecret"` unter dem Abschnitt `"component"` in der Konfigurationsdatei eingeben:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peerpw",
  ```

  Eine Identität kann immer nur einmal registriert werden. Tritt ein Problem auf, dann wiederholen Sie den Vorgang mit einem Befehl, für den ein neuer Benutzername und ein neues Kennwort angegeben werden. Stellen Sie als Sicherheitsmaßnahme mit jeder Identität und den zugehörigen Werten für "enrollID" und "secret" nur einen einzigen Peer bereit. Sie können zwar eine einzige **Administratoridentität** für mehrere Komponenten verwenden (dies entspricht auch der empfohlenen Bereitstellungsstrategie), sollten aber keine IDs und Kennwörter für Peers wiederverwenden.

  Wird der Befehl erfolgreich ausgeführt, dann werden die im folgenden Beispiel dargestellten Informationen angezeigt:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### Administrator registrieren
{: #register-admin}

Nachdem Sie die Komponente registriert haben, müssen Sie außerdem eine Administratoridentität erstellen, mit der Sie die Komponente betreiben können. Diese neue Identität müssen Sie zuerst bei Ihrer Zertifizierungsstelle registrieren; anschließend verwenden Sie sie, um einen MSP-Ordner zu generieren. Danach müssen Sie das signCert-Zertifikat der Benutzer mit Administratorberechtigung zur Konfigurationsdatei hinzufügen, wo es während der Bereitstellung zum Administratorzertifikat des Anordnungsknotens oder Peers wird. Auf diese Weise können Sie die Zertifikate der Administratoridentität beim Betrieb Ihres Blockchain-Netzes verwenden, beispielsweise zum Starten eines neuen Kanals oder zum Installieren von Chaincode auf den Peers.

Für die zu Ihrer Organisation gehörenden Komponenten müssen Sie nur eine einzige Administratoridentität erstellen. Falls Sie mehrere Peers oder einen Anordnungsknoten bereitstellen, müssen Sie diese Schritte nur ein einziges Mal ausführen. Sie können das signCert-Zertifikat, das Sie für eine Komponente generiert haben, zum Bereitstellen aller anderen Peers oder Anordungsknoten verwenden.

Stellen Sie sicher, dass für `$FABRIC_CA_CLIENT_HOME` der Pfad zum MSP Ihrer CA-Domäne festgelegt ist.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Registrieren Sie die Administratoridentität für die Komponente bei der Zertifizierungsstelle, indem Sie den folgenden Befehl ausführen:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Erstellen Sie für den Administrator eine neue Benutzeridentität mit den Werten für `name` and `secret`. Diese Identität wird als Administrator für alle von Ihnen bereitgestellten Komponenten definiert. Achten Sie darauf, andere Werte als für die Peer- oder Anordnungsknotenidentität zu verwenden, die Sie gerade registriert haben. Der Befehl sieht ähnlich wie im folgenden Beispiel aus.

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**Wichtig:** Notieren Sie sich diese Informationen. Sie verwenden diese Werte für `name` und `secret` zum Generieren des MSP-Ordners mit dem Befehl `enroll`.

#### MSP-Ordner für Administrator generieren
{: #enroll-admin}

Nachdem Sie den Komponentenadministrator registriert haben, können Sie die Zertifikate mit dem folgenden Befehl generieren:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Beispiel:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Nach der erfolgreichen Ausführung dieses Befehls wird ein neuer MSP-Ordner in dem Verzeichnis generiert, das Sie mit dem Flag `-M` angegeben haben. Dieses Verzeichnis enthält die Zertifikate, die Sie zum Betrieb Ihrer Komponenten verwenden. Mit einem Befehl "tree" können Sie überprüfen, ob der Befehl "enroll" erfolgreich ausgeführt wurde. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Zertifikate gespeichert haben. Der Befehl "tree" sollte ein Ergebnis ähnlich der folgenden Struktur generieren:

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
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
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

Öffnen Sie in diesem MSP-Verzeichnis die signCert-Datei des neuen Benutzers und konvertieren Sie sie mit den folgenden Befehlen in das Base64-Format:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**Hinweis:** Die mit dem obigen Befehl generierte Zeichenfolge muss unbedingt in einer einzigen Zeile formatiert sein. Sie sollte in etwa wie folgt aussehen:

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
 ```
 Und nicht so:

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
 ```

Beispiel:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

Dieser Befehl gibt eine Zeichenfolge aus, die ähnlich wie das folgende Beispiel aussieht:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

Kopieren Sie diese Zeichenfolge in das Feld `"admincerts"` unter dem Abschnitt "component" in der Konfigurationsdatei.

#### Komponentenidentität bei der TLS-Zertifizierungsstelle registrieren
{: #tls-register-component}

Der Anordnungsknoten oder Peer muss auch bei der TLS-Zertifizierungsstelle registriert werden. Hierzu müssen Sie zuerst die Eintragung mithilfe des Administrators der TLS-Zertifizierungsstelle vornehmen. Ändern Sie den Wert für `$FABRIC_CA_CLIENT_HOME` in ein Verzeichnis, in dem die Administratorzertifikate für die TLS-Zertifizierungsstelle gespeichert werden sollen.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Führen Sie den folgenden Befehl aus, um sich als Administrator für die TLS-Zertifizierungsstelle einzutragen. Der Befehl ist mit dem Befehl identisch, den Sie zum Eintragen des [CA-Administrators](#enroll-ca-admin) verwendet haben, mit lediglich der Ausnahme, dass Sie hier den TLS-Instanznamen für die Zertifizierungsstelle verwenden, den Sie während der [Konfiguration der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms) angegeben haben. Achten Sie darauf, in Ihrem geheimen Schlüssel für die Zertifizierungsstelle dieselben Werte für `ca-admin-name` und `ca-admin-password` zu verwenden.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Ein echter Aufruf könnte wie im folgenden Beispiel aussehen:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Nach der Eintragung besitzen Sie die erforderlichen Zertifikate für die Registrierung Ihrer Komponente bei der TLS-Zertifizierungsstelle. Führen Sie den folgenden Befehl aus, um den Anordnungsknoten oder Peer zu registrieren:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Dieser Befehl ähnelt dem Befehl, den Sie zum Registrieren der Komponentenidentität bei der Zertifizierungsstelle verwendet haben, mit Ausnahme, dass hier der Name der TLS-Zertifizierungsstelle verwendet wird. Falls Sie anstelle eines Peers einen Anordnungsknoten registrieren, legen Sie für `--id.type` den Wert `orderer` statt `peer` fest. Sie müssen für diese Identität einen anderen Benutzernamen und ein anderes Kennwort vergeben, als Sie für Ihre Standardzertifizierungsstelle verwendet haben. Eine echte Registrierung könnte ähnlich wie der folgende Befehl aussehen:

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Kopieren Sie die Werte für `name` und `secret` in `"enrollid"` und `"enrollsecret"` unter dem Abschnitt `"tls"` der Konfigurationsdatei:

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### Hosts für Zertifikatssignieranforderungen (Certificate Signing Request, CSR)
{: #csr-hosts}

Zum Bereitstellen eines Anordnungsknotens oder Peers müssen Sie einen CSR-Hostnamen angeben. Dieser Hostname beinhaltet die Proxy-IP-Adresse des Clusters, in dem Sie die Komponente bereitstellen, sowie den `Service-Host-Namen`, der beim Bereitstellen des Helm-Diagramms generiert wird.

#### Wert der Proxy-IP-Adresse für den Cluster ermitteln

Falls Sie einen Anordnungsknoten oder Peer in demselben ICP-Cluster bereitstellen wollen, in dem Sie Ihre Zertifizierungsstelle bereitgestellt haben, müssen Sie unbedingt die Rolle eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") in dem ICP-Cluster besitzen, in dem der Anordnungsknoten bzw. Peer bereitgestellt werden soll. Geben Sie dieselbe Proxy-IP ein, die Sie bei der [Konfiguration der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms) verwendet haben. Wenn Sie den Anordnungsknoten oder Peer in einem anderen Cluster bereitstellen wollen, können Sie den Wert für die Proxy-IP-Adresse des Clusters über die ICP-Konsole abrufen, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich bei der ICP-Konsole an. Klicken Sie im linken Navigationsfenster auf **Plattform** und anschließend auf **Knoten**, um die im Cluster definierten Knoten anzuzeigen. 
2. Klicken Sie auf den Knoten mit der Rolle `Proxy` und kopieren Sie den Wert, der in der Tabelle für die `Host-IP` angegeben ist. 
3. Fügen Sie die `Host-IP` als Wert für  `"hosts"` im nachfolgend gezeigten Abschnitt `"csr"` der Konfigurationsdatei ein:

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### Service-Host-Namen ermitteln

Ein `Service-Host-Name` wird generiert, wenn ein Helm-Diagramm bereitgestellt wird. Er basiert auf dem `Namen des Helm-Release`, den Sie beim Bereitstellen des Helm-Diagramms verwenden.

- Falls Sie einen Anordnungsknoten bereitstellen, ist der `Service-Host-Name` der Wert für `helm_release_name`, den Sie während der Bereitstellung mit der am Ende hinzugefügten Zeichenfolge `-orderer` angeben. Hat Ihr Cluster beispielsweise die Proxy-IP-Adresse `9.42.134.44` und geben Sie für den `Namen des Helm-Release` den Wert `org1orderer` an, können Sie die folgenden Werte im Abschnitt `"csr"` der Datei einfügen:

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- Falls Sie einen Anordnungsknoten bereitstellen, ist der `Service-Host-Name` der `Name des Helm-Release`, den Sie während der Bereitstellung ohne eine ergänzende Zeichenfolge angeben. Lautet die Proxy-IP-Adresse des Clusters beispielsweise "9.42.134.44" und der `Name des Helm-Release` `org1peer1`, können Sie die folgenden Werte im Abschnitt "hosts" unter "csr" einfügen:

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
      "org1peer1"
    ]
  }
  ```
  {:codeblock}

### Konfigurationsdatei vervollständigen

Nachdem Sie alle obigen Schritte ausgeführt haben, könnte Ihre aktualisierte Konfigurationsdatei ähnlich wie im folgenden Beispiel aussehen:

```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer",
            "enrollsecret": "peerpw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}

Die anderen Felder können leer bleiben. Speichern Sie diese Datei; Sie benötigen sie, wenn Sie einen [Anordnungsknoten](/docs/services/blockchain/howto/orderer_deploy_icp.html) oder [Peer](/docs/services/blockchain/howto/peer_deploy_icp.html) bereitstellen.

## Membership Service Providers (MSPs)
{: #msp}

{{site.data.keyword.blockchainfull_notm}} Platform-Komponenten verbrauchen Identitäten über Membership Service Providers (MSPs). MSPs ordnen die Zertifikate zu, die von den Zertifizierungsstellen mit den Berechtigungen und Rollen ausgegeben werden. Weitere Informationen zu MSPs enthält der [Abschnitt über die Mitgliedschaft in der Dokumentation von Hyperledger Fabric ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Abschnitt 'Membership' in der Dokumentation von Hyperledger Fabric").

MSP-Ordner müssen eine definierte Struktur besitzen, damit sie von Fabric-Komponenten verwendet werden können. Der Fabric-CA-Client baut diese Struktur durch die Erstellung der folgenden MSP-Ordner auf:

- **cacerts:** Enthält das Zertifikat der Stammzertifizierungsstelle Ihres Netzes.
- **intermediatecerts:** Dies sind die Zertifikate von allen Zwischenzertifizierungsstellen in Ihrer Zertifikatskette (die zu einer oder mehreren Stammzertifizierungsstellen zurückführt). Jede Enterprise Plan-Organisation ist für Failover und Hochverfügbarkeit mit zwei Zwischenzertifizierungsstellen ausgestattet.
- **signCerts:** Dieser Ordner enthält Ihr öffentliches Signierzertifikat, das auch als signCert-Zertifikat oder Eintragungszertifikat bezeichnet wird. Dieses Zertifikat wird Ihren Aufrufen ans Netz (z. B. einem Chaincode-Aufruf) hinzugefügt, wenn Sie über die Befehlszeile auf das MSP-Verzeichnis verweisen oder ein Benutzerkontextobjekt mit den SDKs erstellen. Sie können dieses Zertifikat in IBP hochladen, falls Sie ein Netz über das SDK oder die Befehlszeile betreiben wollen.
- **keystore:** Dieser Ordner enthält Ihren privaten Schlüssel. Dieser Schlüssel wird zum Signieren der Aufrufe an das Netz verwendet, wenn Sie über die Befehlszeile auf Ihr MSP-Verzeichnis verweisen oder ein Benutzerkontextobjekt mit den SDKs erstellen, wird jedoch nicht so zu Aufrufen zugeordnet wie ein signCert-Zertifikat. Dieser Schlüssel muss **unbedingt** sicher aufbewahrt werden. Falls er in unbefugte Hände gelangt, kann er verwendet werden, um Ihre Identität vorzutäuschen.

Sie können auch einen MSP-Ordner erstellen, auf den vom Fabric-CA-Client mit dem Network Monitor und den Swagger-APIs verwiesen werden kann.

- **cacerts** und **intermediatecerts**: Sie können diese Zertifikate mithilfe der [Swagger-APIs](/docs/services/blockchain/howto/swagger_apis.html) abrufen, indem Sie eine Anforderung `Get` an die MSP-API absetzen.
- **signCerts** und **keystore**: Sie können diese Zertifikate generieren, indem Sie in der Anzeige "Zertifizierungsstelle" auf die Schaltfläche **Zertifikate generieren** klicken. Daraufhin wird ein Popup-Fenster geöffnet, in dem zwei Zertifikate aufgelistet werden. Kopieren Sie das **Zertifikat** in "signCerts" und den **privaten Schlüssel** in "keystore". Bewahren Sie diese Zertifikate an einem sicheren Ort auf, weil sie nicht auf der Plattform gespeichert werden.

Zahlreiche Fabric-Komponenten enthalten zusätzliche Informationen in ihrem MSP-Ordner. Wenn Sie z. B. mit einem fernen Peer arbeiten, dann werden möglicherweise die folgenden Ordner angezeigt:

- **admincerts:** Dieser Ordner enthält die Liste der Administratoren für diese Organisation oder Komponente. Sie müssen Ihr signCert-Zertifikat in diesen Ordner hochladen, wenn Sie mit einem fernen Peer über die Befehlszeile oder mithilfe der SDKs arbeiten. Wenn Sie den Fabric-CA-Client verwenden, benötigen Sie außerdem einen Ordner "admincerts" in Ihrem MSP, der das entsprechende signCert-Zertifikat enthält, das als Administratorzertifikat erkannt werden soll.
- **tls:** In diesem Ordner werden TLS-Zertifikate gespeichert, die zur Kommunikation mit anderen Netzkomponenten verwendet werden.

Weitere Informationen zur Struktur der MSPs finden Sie im Abschnitt zur [Mitgliedschaft ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Membership") und im Abschnitt zu [Membership Service Providers ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") in der Hyperledger Fabric-Dokumentation.


## Protokolle der Zertifizierungsstelle anzeigen
{: #ca-operate-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [`Befehlen der CLI "kubectl"`](#ca-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem ICP-Cluster enthalten ist. 

- Mit dem Befehl `kubectl logs` können Sie die Containerprotokolle innerhalb des Pods anzeigen. Falls Sie den Podnamen nicht genau kennen, führen den folgenden Befehl aus, um Ihre Podliste anzuzeigen.

   ```
   kubectl get pods
   ```
   {:codeblock}

   Rufen Sie anschließend mit dem folgenden Befehl die Protokolle des Containers für Zertifizierungsstellen ab, der sich im Pod befindet; ersetzen Sie hierbei `pod_name` durch den Namen Ihres Pods, der in der Ausgabe des obigen Befehls aufgeführt ist:

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   Weitere Informationen zum Befehl `kubectl logs` enthält die [Kubernetes-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”).

- Alternativ können Sie auf Ereignisse und Protokolle für die Bereitstellung auch über die [ICP-Konsole für das Cluster-Management](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html) zugreifen, die die Protokolle in Kibana öffnet.

  **Hinweis:** Wenn Sie Ihre Protokolle in Kibana anzeigen, empfangen Sie möglicherweise die Antwort `Keine Ergebnisse gefunden`. Diese Bedingung kann auftreten, wenn ICP die IP-Adresse Ihres Workerknotens als Hostnamen verwendet. Entfernen Sie zur Lösung dieses Problems oben in der Anzeige den Filter, der mit `node.hostname.keyword` beginnt. Anschließend sind die Protokolle sichtbar.

## Fehlerbehebung
{: #ca-operate-troubleshooting}

### **Problem:** Fehler bei Ausführung des Befehls `enroll`
{: #ca-enroll-error}

Bei der Ausführung des Befehls "enroll" im Fabric-CA-Client schlägt der Befehl möglicherweise mit dem folgenden Fehler fehl:

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**Lösung:**

Dieser Fehler kann auftreten, wenn der Fabric-CA-Client versucht, eine Eintragung vorzunehmen, jedoch keine Verbindung zur Ihrer Zertifizierungsstelle herstellen kann. Dies kann in den folgenden Fällen vorkommen:   

- Der Befehl `enroll` enthält eine zusätzliche Angabe `https://` im Parameter `-u`.
- Der Name Ihrer Zertifizierungsstelle ist falsch.
- Ihr Benutzername oder Ihr Kennwort ist falsch.

Prüfen Sie die Parameter, die Sie im Befehl `enroll` angegeben haben, und stellen Sie sicher, dass keine dieser Bedingungen vorliegt.

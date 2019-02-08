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

# Ferne Peers in {{site.data.keyword.cloud_notm}} Private bereitstellen
{: #remote-peer-icp}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


In den folgenden Anweisungen wird die Vorgehensweise zur Installation eines fernen Peers für {{site.data.keyword.blockchainfull}} Platform unter {{site.data.keyword.cloud_notm}} Private (ICP) und die Herstellung einer Verbindung dieses Peers zu {{site.data.keyword.blockchainfull_notm}} Platform beschrieben.
{:shortdesc}

Bei {{site.data.keyword.cloud_notm}} Private handelt es sich um eine Anwendungsplattform zur Entwicklung und zum Management lokaler containerisierter Anwendungen. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on {{site.data.keyword.cloud_notm}} Private ist ein {{site.data.keyword.blockchainfull_notm}}-Produkt, das in einem Produktpaket mit ICP angeboten wird und mit dem Kunden Peers in Ihrer lokalen Umgebung bereitstellen können. Nach der Installation des Helm-Diagramms des fernen Peers ist es als Anwendung im ICP-Katalog enthalten. Der ferne Peer für ICP nutzt den Speicher, die Sicherheits- und Protokollierungsfunktionen sowie die Unterstützungsleistungen von ICP für das Peer-Management. Weitere Informationen zu ICP finden Sie in der [{{site.data.keyword.cloud_notm}} Private-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-Dokumentation").

Vor der Installation ferner Peers unter ICP sollten Sie sich mit den Informationen im Abschnitt mit den [wichtigen Hinweisen zu fernen Peers](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations) vertraut machen.

## Voraussetzungen
{: #prerequisites-icp}

Vor der Installation ferner Peers unter ICP müssen Sie die folgenden Schritte unter {{site.data.keyword.cloud_notm}} Private, auf Ihrem lokalen System und unter {{site.data.keyword.blockchainfull_notm}} Platform ausführen.

1. Lokales System

  Sie müssen die {{site.data.keyword.cloud_notm}} Private-Befehlszeilenschnittstelle Version [2.1.0.3 ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "Installieren der {{site.data.keyword.cloud_notm}} Private-CLI") installieren.  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} Private

  Sie benötigen einen Cluster mit [{{site.data.keyword.cloud_notm}} Private ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v2.1.0.3-Dokumentation") der Version 2.1.0.3. Wenn Sie den fernen Peer zur Entwicklung sowie zu Test- oder Versuchszwecken einsetzen, dann können Sie die [{{site.data.keyword.cloud_notm}} Private Community Edition ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "Installieren von {{site.data.keyword.cloud_notm}} Private-CE") kostenlos installieren.

  **Hinweise:**
    - Sie müssen den Speicher ermitteln, der von Ihrem Peer benutzt werden soll. Wenn Sie die Standardeinstellungen verwenden, dann erstellt das Helm-Diagramm einen neuen 8 GI Persistent Volume Claim (PVC) mit dem Namen `my-data-pvc` für Ihre Peerdaten. Wenn Sie sich für den Einsatz einer CouchDB-Ledgerdatenbank entscheiden, dann erstellt das Helm-Diagramm einen weiteren 8 Gi Persistent Volume Claim mit dem Namen `statedb-pvc` für Ihre Ledgerdatenbank.
    - Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der ICP-Installation eine *neue* Speicherklasse (storageClass) eingerichtet wurde. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse erstellen, bevor Sie die Bereitstellung durchführen können.
    - Die [dynamische Bereitstellung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamische Datenträgerbereitstellung") steht nur für amd64-Knoten in ICP zur Verfügung. Wenn in Ihrem Cluster eine Kombination aus s390x- und amd64-Workerknoten eingesetzt wird, dann kann die dynamische Bereitstellung demzufolge nicht verwendet werden.
    - Wenn Sie nicht mit der dynamischen Bereitstellung arbeiten, müssen [Persistent Volumes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistent Volumes") erstellt und mit Bezeichnungen eingerichtet werden, die zum Optimieren des PVC-Bindungsprozesses (PVC = Persistent Volume Claim) von Kubernetes verwendet werden können.

  Zusätzlich zu den bestehenden ICP-Hardwarevoraussetzungen gelten für den fernen Peer die folgenden Mindestvoraussetzungen:
    - 2x vCPU
    - 2 GB RAM
    - 4 GB Speicherplatz für den Chaincode
    - 10 GB Speicherplatz für das Ledger sowie Erweiterungsmöglichkeit bei einem ansteigenden Volumen des Ledgers

3. {{site.data.keyword.blockchainfull_notm}} Platform  

  Zur Verwendung eines fernen Peers benötigen Sie eine Organisation, die Mitglied eines Blockchain-Netzes ist, das unter {{site.data.keyword.blockchainfull_notm}} Platform gehostet wird. Sie müssen den Network Monitor unter {{site.data.keyword.cloud_notm}} verwenden, um auf die Netzberechtigungsnachweise und die API-Endpunkte Ihres Netzes zugreifen zu können. Wenn Sie nicht Mitglied eines Blockchain-Netzes sind, dann müssen Sie ein Netz erstellen oder einem Netz beitreten. Weitere Informationen hierzu finden Sie im Abschnitt zum [Erstellen eines Netzes](/docs/services/blockchain/get_start.html#creating-a-network) oder [Beitritt zu einem Netz](/docs/services/blockchain/get_start.html#joining-a-network).

## Helm-Diagramm in ICP importieren

Das {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer-Paket steht über [GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer") zur Verfügung. Das Helm-Diagramm wird über die Befehlszeile in die ICP-Konsole importiert. Führen Sie auf dem lokalen System die folgenden Schritte aus, um das Helm-Diagramm zu importieren:

1. Laden Sie die Datei des Helm-Diagramms (`.tgz`) von [GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "IBM Blockchain Remote Peer-Helm-Diagramm") herunter.<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. Wenn Sie noch nicht angemeldet sind, dann melden Sie sich nun über die ICP-Befehlszeilenschnittstelle bei Ihrem ICP-Cluster und anschließend bei der privaten Docker-Image-Registry an.  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  Im Code steht `<cluster_CA_domain>` für die Domäne der Zertifizierungsstelle (CA). Wenn Sie keine CA-Domäne angegeben haben, dann wird der Standardwert `mycluster.icp` verwendet.

3. Führen Sie in dem Verzeichnis, in dem Sie das heruntergeladene Helm-Diagramm gespeichert haben, den folgenden Befehl in der ICP-Befehlszeilenschnittstelle aus, um das Helm-Diagramm in Ihren ICP-Cluster zu importieren. Ersetzen Sie dabei `<helm_chart_from_github>` durch den Namen der Datei mit der Erweiterung `.tgz`, die Sie von GitHub heruntergeladen haben.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  Wird dieser Befehl erfolgreich ausgeführt, dann werden die folgenden Informationen angezeigt:

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  Auf der Seite des ICP-Katalogs wird nun die Kachel des Helm-Diagramms für {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer angezeigt. Klicken Sie auf die Schaltfläche **Katalog** in der ICP-Konsole, um zu überprüfen, ob der Import erfolgreich verlaufen ist.

### Fernen Peer bereitstellen

In den folgenden Schritten wird der Prozess beschrieben, der zur Bereitstellung des fernen Peers in ICP ausgeführt werden muss. Bevor Sie den fernen Peer in ICP konfigurieren können, müssen Sie bestimmte Endpunktinformationen zu Ihrer {{site.data.keyword.blockchainfull_notm}} Platform-Instanz abrufen und eine Benutzeridentität für den fernen Peer erstellen. Diese Informationen müssen in späteren Schritten verwendet werden.

## Schritt 1: Konfigurationsinformationen des fernen Peers abrufen
{: #network-endpoints}

Sie müssen während der Konfiguration für Ihren fernen Peer die API-Endpunkte Ihres Netzes angeben. Diese Endpunkte erlauben einem fernen Peer die Suche nach dem Netz unter  {{site.data.keyword.blockchainfull_notm}} Platform sowie die Herstellung einer Verbindung zu diesem Netz. Klicken Sie in der Anzeige **Übersicht** Ihres Network Monitor auf die Schaltfläche zur **Konfiguration ferner Peers**.

![Konfiguration ferner Peers](../images/myresources_starter.png "Konfiguration ferner Peers")
*Abbildung 1. Konfiguration ferner Peers*

In einem daraufhin geöffneten Popup-Fenster werden die Werte der folgenden Felder angezeigt. Speichern Sie diese Werte für die Konfiguration des fernen Peers.

  - **Netz-ID**
  - **MSP der Organisation**
  - **Name der Zertifizierungsstelle (CA)**
  - **URL der Zertifizierungsstelle (CA)**
  - **TLS-Zertifikat der Zertifizierungsstelle (CA)**

Sie können die Felder einzeln kopieren und einfügen oder sie als JSON-Datei speichern, indem Sie auf den Link **Download** klicken. **Beachten** Sie hierbei, dass beim Herunterladen der Informationen in einer JSON-Datei das TLS-Zertifikat ins PEM-Format umgewandelt werden muss, bevor Sie es für den fernen Peer bereitstellen. Sie können die Befehlszeilenschnittstelle verwenden, um das Zertifikat ins PEM-Format umzuwandeln, indem Sie den Befehl `echo -e "<CERT>" > catls.pem` absetzen.

## Schritt 2: Fernen Peer registrieren
{: #register-peer}

Sie müssen unter {{site.data.keyword.blockchainfull_notm}} Platform eine neue Peeridentität zu Ihrer Organisation hinzufügen, bevor der ferne Peer an dem Netz teilnehmen kann. Führen Sie die folgenden Schritte aus, um einen fernen Peer einzutragen und zu registrieren.

1. Melden Sie sich bei {{site.data.keyword.blockchainfull_notm}} Platform an und greifen Sie auf den Network Monitor zu. In der Anzeige "Zertifizierungsstelle" können Sie alle Identitäten anzeigen, die für Ihre Organisation registriert wurden. Hierzu gehören der Administrator und die Clientanwendungen.
  ![CA-Anzeige](../images/CA_screen_starter.png "CA-Anzeige")
  *Abbildung 2. CA-Anzeige*

2. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** oben in der Anzeige. Daraufhin wird ein Dialogfenster geöffnet, in dem Sie Ihren fernen Peer registrieren können, nachdem Sie die folgenden Felder ausgefüllt haben. Speichern Sie die Werte für die ID und den geheimen Schlüssel, die Sie zum Konfigurieren Ihres Peers benötigen.
  - **ID:** Der Verwendungsname Ihres Peers, der bei der Konfiguration des Peers auch als `Eintragungs-ID` bezeichnet wird. **Speichern Sie diesen Wert** zum späteren Gebrauch, wenn Sie den fernen Peer bereitstellen wollen.
  - **Geheimer Schlüssel:** Das Kennwort Ihres Peers, das bei der Konfiguration des Peers auch als `Geheimer Eintragungsschlüssel` bezeichnet wird. **Speichern Sie diesen Wert** zum späteren Gebrauch, wenn Sie den fernen Peer bereitstellen wollen.
  - **Typ:** Wählen Sie für dieses Feld den Wert `peer` aus.
  - **Zugehörigkeit:** Dies ist die Zugehörigkeit in Ihrer Organisation (z. B. `org1`), zu der Ihr ferner Peer gehören wird. Wählen Sie eine vorhandene Zugehörigkeit in der Dropdown-Liste aus oder geben Sie eine neue Zugehörigkeit ein.
  - **Maximale Anzahl der Eintragungen:** In diesem Feld können Sie die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit einer bestimmten Identität einschränken. Erfolgt hier keine Angabe, dann wird der Standardwert (Unbegrenzte Eintragungen) verwendet.

  Nachdem Sie die Felder ausgefüllt haben, klicken Sie auf **Abschicken**, um den fernen Peer zu registrieren. Der registrierte Peer wird dann in der Tabelle als Identität in Ihrer Organisation aufgelistet.

## Schritt 3: Fernen Peer konfigurieren und installieren

Nach der Registrierung der Peeridentität im Network Monitor können Sie den fernen Peer in ICP konfigurieren und installieren, indem Sie die folgenden ICP-Konsolenanweisungen befolgen.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. Melden Sie sich bei der ICP-Konsole an und klicken Sie auf den Link **Katalog** in der oberen rechten Ecke.
2. Suchen Sie das Helm-Diagramm für {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer und klicken Sie darauf, um es zu öffnen. Es wird eine Readme-Datei angezeigt, die Informationen zur Installation und Konfiguration des Helm-Diagramms enthält.
3. Klicken Sie auf **Konfigurieren**. Füllen Sie auf der Seite "Konfiguration" die Felder für die Konfigurationseinstellungen für das Helm-Diagramm aus und bestätigen Sie, dass Sie die Lizenzvereinbarung gelesen haben und dieser zustimmen.
4. Klicken Sie auf **Installieren**.

### Konfigurationsparameter
{: #icp-configuration-parms}

Sie müssen die Informationen angeben, die Ihr ferner Peer benötigt, um eine Verbindung zu dem Netz unter {{site.data.keyword.blockchainfull_notm}} Platform herzustellen.
Die folgende Tabelle enthält eine Auflistung der konfigurierbaren Parameter des {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer-Diagramms sowie die zugehörigen Standardwerte.


|  Parameter     | Beschreibung   | Standardwert | Erforderlich |
| --------------|-----------------|-------|------- |
|**Konfiguration** | **Konfigurieren des Releasenamens und Zielnamensbereichs für Ihren fernen Peer** |  ||
| `Releasename`| Der Name Ihres Helm-Release. | Nein | Ja |
| `Zielnamensbereich`| Der Kubernetes-Namensbereich. | Standardwert | Ja |
| | | | |
|**Clusterkonfiguration** |**Informationen zur Clusterkonfiguration** | ||
| `Workerknotenarchitektur`| Auswählen der gewünschten Cloudplattformarchitektur (amd64 oder S390x).| Automatisch erkannte Architektur auf Basis des Masterknotens | Ja |
| `Image`| Pfad zum Helm-Diagramm. | Automatisch mit installiertem Pfad ausgefüllt; diesen Wert nicht ändern | Ja |
| | | | |
|**Blockchain-Netz** | **Für fernen Peer erforderliche Informationen zur Netzkonfiguration**| | |
| `Netz-ID`| Der Wert der Netz-ID, der im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor gefunden wurde. Klicken Sie auf die Schaltfläche `Konfiguration ferner Peers` in der Anzeige "Übersicht", kopieren Sie die Informationen und fügen Sie sie hier ein. | Nein | Ja |
| `MSP der Organisation`| Der Wert für den MSP der Organisation, der im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor gefunden wurde. Klicken Sie auf die Schaltfläche `Konfiguration ferner Peers` in der Anzeige "Übersicht", kopieren Sie die Informationen und fügen Sie sie hier ein. | Nein | Ja |
| `Name der Zertifizierungsstelle (CA)`| Der Wert des CA-Namens, der im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor gefunden wurde. Klicken Sie auf die Schaltfläche `Konfiguration ferner Peers` in der Anzeige "Übersicht", kopieren Sie die Informationen und fügen Sie sie hier ein. | Nein | Ja |
| `URL der Zertifizierungsstelle (CA)`| Der Wert der CA-URL, der im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor gefunden wurde. Klicken Sie auf die Schaltfläche `Konfiguration ferner Peers` in der Anzeige "Übersicht", kopieren Sie die Informationen und fügen Sie sie hier ein. | Nein | Ja |
| `TLS-Zertifikat der Zertifizierungsstelle (CA)` | Die Zeichenfolge des TLS-Zertifikats der Zertifizierungsstelle (CA) im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor. Klicken Sie auf die Schaltfläche `Konfiguration ferner Peers` in der Anzeige "Übersicht", kopieren Sie die Informationen und fügen Sie sie hier ein. | Nein | Ja |
| | | | |
|**Identität des fernen Peers** | **Zur Registrierung des fernen Peers verwendete Eintragungs-ID und der verwendete geheime Schlüssel**| | |
| `Eintragungs-ID des Peers`| Dies ist die Eintragungs-ID, die Sie in der Anzeige "Zertifizierungsstelle" im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor eingegeben haben. | Nein | Ja |
| `Geheimer Eintragungsschlüssel des Peers`| Dies ist der geheime Eintragungsschlüssel, den Sie in der Anzeige "Zertifizierungsstelle" im {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor eingegeben haben. | Nein | Ja |
| | | | |
|**Datenbank des fernen Peers** | **Typ der Ledgerdatenbank**| | |
| `Ledgerdatenbank`| Zur Speicherung des Kanalledgers verwendete [Statusdatenbank](/docs/services/blockchain/glossary.html#state-database). Der ferne Peer muss dieselbe Datenbank wie das [Blockchain-Netz](/docs/services/blockchain/v10_dashboard.html#network-preferences) verwenden. | Nein | Ja |
| `CouchDB-Image`| Gilt nur, wenn CouchDB als Ledgerdatenbank ausgewählt wurde. | Automatisch mit installiertem Pfad ausgefüllt; diesen Wert nicht ändern | Ja |
|**Datenpersistenz** | Aktivieren der Funktionalität zum persistenten Speichern von Daten nach Neustart oder Ausfall des Clusters. Siehe hierzu [Speicher in Kubernetes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/ "Volumes"). | | |
| `Datenpersistenz aktiviert`| Statusdaten stehen zur Verfügung, wenn der Container neu gestartet wird. *Wird diese Option inaktiviert, gehen alle Daten bei einem Failover oder Podneustart verloren.* | Aktiviert | Nein |
| `Dynamische Bereitstellung verwenden`| Verwenden des dynamischen Kubernetes-Speichers. | Aktiviert | Nein |
| | | | |
|**Persistent Volume-Konfiguration** | **Von Ihrem Peer zu verwendender Persistent Volume Claim (PVC)** |  |  |
| `Persistent Volume Claim`| Geben Sie den Namen des neuen Persistent Volume Claim (PVC) ein, der erstellt werden soll. | my-data-pvc | Nein |
| `Speicherklassenname`| Auswählen des Speicherklassennamens. | Leer, wenn ein neuer PVC erstellt wird; andernfalls muss die Speicherklasse angegeben werden, die dem vorhandenen PVC zugeordnet ist | Nein |
| `Vorhandener Volume Claim`| Angeben des Namens eines vorhandenen Volume Claims. Alle anderen Felder leer lassen. | Neuer Claim-Name | Nein |
| `Speicherzugriffsmodus`| Angeben des [Zugriffsmodus ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Zugriffsmodi") des Speichers für den PVC. | ReadOnlyMany| Nein |
| `Selektorkennsatz`| [Selektorkennsatz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Kennsätze und Selektoren") für Ihren PVC.| Standardwert | Nein |
| `Selektorwert`| [Selektorwert ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Kennsätze und Selektoren") für Ihren PVC. | Standardwert | Nein |
| `Volume Claim-Größe`| Die Größe des Volume Claims, die größer als 2Gi sein muss. | 8Gi  | Ja |
| | | | |
|**Datenpersistenz der Statusdatenbank** | **Persistentes Speichern der Daten nach Neustart oder Ausfall Ihres Containers. Weitere Informationen hierzu siehe [Speicher in Kubernetes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/ "Volumes")** | | |
| `Datenpersistenz aktiviert`| Ledgerdaten stehen zur Verfügung, wenn der Container neu gestartet wird. *Wird diese Option inaktiviert, gehen alle Daten bei einem Failover oder Podneustart verloren.* | Aktiviert | Nein |
| `Dynamische Bereitstellung verwenden`| Verwenden des dynamischen Kubernetes-Speichers. | Aktiviert | Nein |
| | | | |
|**Persistent Volume-Konfiguration der Statusdatenbank** |**Für Statusdatenbank verwendeter Persistent Volume Claim. Weitere Informationen hierzu siehe [Speicher in Kubernetes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/ "Volumes").** |||
| `Persistent Volume Claim`| Eingeben eines Namens oder Verwenden des Standardwerts für den neuen CouchDB-PVC (Persistent Volume Claim), der erstellt wird. | statedb-pvc | Nein |
| `Speicherklassenname der Statusdatenbank`| Auswählen des Speicherklassennamens. | Nein | Nein |
| `Vorhandener Volume Claim der Statusdatenbank`| Zur Verwendung eines vorhandenen Volume Claim müssen Sie den PVC-Namen eingeben und alle anderen Felder leer lassen. | Nein | Nein |
| `Speicherzugriffsmodus für Statusdatenbank`| Angeben des [Zugriffsmodus ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Zugriffsmodi") des Speichers für den PVC. | ReadOnlyMany| Nein |
| `Selektorkennsatz`| [Selektorkennsatz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Kennsätze und Selektoren") für Ihren PVC. | Standardwert | Nein |
| `Selektorwert`| [Selektorwert ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Kennsätze und Selektoren") für Ihren PVC. | Standardwert | Nein |
| `Volume Claim-Größe der Statusdatenbank`| Größe des Persistent Volume Claim (PVC). | 8Gi | Nein |
| | | | |
| **Peerressourcen** | **Minimaler und maximaler Wert für CPU und Speicher für Peer-Container** | | |
| `Peer-CPU-Anforderung` | Mindestanzahl der CPUs, die dem Peer zugeordnet werden soll. | 2 | Ja |
| `Peer-CPU-Limit` | Maximale Anzahl CPUs, die dem Peer zugeordnet werden soll. | 2 | Ja |
| `Peer-Speicheranforderung` | Mindestens erforderlicher Speicher, der dem Peer zugeordnet werden soll. | 4Gi | Ja |
| `Peer-Speicherbegrenzung` | Maximalwert für Speicher, der dem Peer zugeordnet werden soll. | 4Gi | Ja |
| | | | |
|**CouchDB-Ressourcen** | **Minimaler und maximaler Wert für CPU und Speicher, der dem CouchDB-Container zugeordnet wird. Dieser Abschnitt gilt nur, wenn für die Statusdatenbank die Option "CouchDB" ausgewählt wurde**| | | |
| `CouchDB-CPU-Anforderung` | Mindestanzahl der CPUs, die für CouchDB zugeordnet werden soll. | 2 | Ja |
| `CouchDB-CPU-Limit` | Maximale Anzahl CPUs, die für CouchDB zugeordnet werden soll. | 2 | Ja |
| `CouchDB-Speicheranforderung` | Mindestens erforderlicher Speicher, der für CouchDB zugeordnet werden soll. | 4Gi | Ja |
| `CouchDB-Speicherbegrenzung` | Maximalwert für Speicher, der für CouchDB zugeordnet werden soll. | 4Gi | Ja |

Wird für die Datenbank des fernen Peers "CouchDB" ausgewählt, werden zwei Container im Pod erstellt, und zwar einer für den Peer und der andere für CouchDB. 
Der Peer-Container enthält einen einzelnen Datenträgermount für den Peer-PVC, in dem die Blöcke und Transaktionen im Dateisystem gespeichert sind. Der CouchDB-Container hängt den PVC für die Statusdatenbank des Peers an, der die Ledgerdaten enthält.

Wird für die Datenbank des fernen Peers "LevelDB" ausgewählt, wird ein einzelner Container im Pod erstellt, über den sowohl der Peer als auch LevelDB-Prozesse ausgeführt werden. Dieser Container verfügt über zwei Datenträgermounts. Einen für den Peer-PVC und einen zweiten für den PVC der Statusdatenbank des Peers, der die Ledgerdaten enthält.

| Auswahl der Datenbank des fernen Peers | Inhalte von Container Nr. 1 | Inhalte von Container Nr. 2 |
| --------------|-----------------|---------------|
| CouchDB | Ferner Peer, der den Peer-PVC anhängt | CouchDB-Einheit, die den PVC der Statusdatenbank anhängt |
| LevelDB | Ferner Peer und LevelDB-Einheit, die den Peer-PVC und den PVC der Statusdatenbank anhängen | Nicht zutreffend |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](/docs/services/blockchain/howto/remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  Mit diesem Befehl wird die Datei "peer-cert.pem" generiert, die das TLS-Zertifikat des Peers enthält.

--->

## Schritt 4: Installation des fernen Peers überprüfen
{: #verify-installation-icp}

Nach der Installation des fernen Peers können Sie das Peerprotokoll anzeigen, um zu überprüfen, ob die Peerinstallation erfolgreich verlaufen ist.

1. Klicken Sie, nachdem Sie auf **Installieren** geklickt haben, als Nächstes auf die Schaltfläche **Helm-Release anzeigen** im Popup-Fenster. Blättern Sie nach unten bis zum Abschnitt **Pods**. Wenn der Podstatus **Aktiv** lautet, dann finden Sie die Instanz Ihres fernen Peers im Abschnitt **Bereitstellungen**.
2. Klicken Sie in der ICP-Konsole auf das Symbol **Menü** in der oberen linken Ecke. Klicken Sie in der Menüliste auf **Workloads** > **Bereitstellungen**. Klicken Sie in der Bereitstellungstabelle auf die Instanz des fernen Peers, die Sie erstellen. Daraufhin wird die Anzeige mit der Bereitstellungsübersicht geöffnet. Klicken Sie auf die Registerkarte **Protokolle**, um die Protokolle Ihres fernen Peers anzuzeigen. Sie können im Suchfeld die Zeichenfolge `Started` (Gestartet) eingeben.
  - Wenn Ihr ferner Peer erfolgreich gestartet wurde, dann werden die im folgenden Beispiel dargestellten Protokolle angezeigt:
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - Werden keine Protokolle in der Anzeige aufgeführt, dann ist der Start Ihres fernen Peers fehlgeschlagen. Zur Anzeige zusätzlicher Protokolle zur Untersuchung des Problems müssen Sie auf das Symbol **Menü** in der oberen linken Ecke klicken und dann auf **Workloads** > **Helm-Releases** klicken. Klicken Sie auf das gewünschte Helm-Release, um es zu öffnen. Klicken Sie dann auf den Link **Protokolle anzeigen** neben dem zugeordneten **Pod**, um zusätzliche Diagrammprotokolle in der Kibana-Schnittstelle anzuzeigen.
3. Um zu überprüfen, ob der ferne Peer eine Verbindung zum {{site.data.keyword.blockchainfull}} Platform-Netz herstellen kann, können Sie einen CLI-Befehl für den Peer im Container des fernen Peers ausführen. Führen Sie den CLI-Befehl `peer channel fetch` aus, um den Genesis-Block aus einem Kanal abzurufen:

   1. Wenn noch keine Verbindung zum ICP-Cluster hergestellt wurde, dann führen Sie die folgenden [Anweisungen](/docs/services/blockchain/howto/remote_peer_operate_icp.html#remote-peer-kubectl-configure) aus, um die Verbindung herzustellen, und verwenden Sie die CLI im Peer-Container.

   2. Ihre Organisation muss zu einem Kanal im Netz hinzugefügt werden, bevor Sie den Genesis-Block abrufen können.

     - Sie können einen neuen Kanal für den fernen Peer starten. Als Kanalinitiator können Sie Ihre Organisation während der [Kanalerstellung](/docs/services/blockchain/howto/create_channel.html#creating-a-channel) automatisch einbinden. Beachten Sie hierbei, dass Sie über mindestens einen Peer unter {{site.data.keyword.blockchainfull_notm}} Platform verfügen müssen, um einen Kanal im Network Monitor zu erstellen.  

     - Ein anderes Mitglied des Blockchain-Netzes kann Ihre Organisation ebenfalls zu einem vorhandenen Kanal hinzufügen, indem es eine [Kanalaktualisierung](/docs/services/blockchain/howto/create_channel.html#updating-a-channel) durchführt. Ein Mitglied des Kanals mit Peers unter {{site.data.keyword.blockchainfull_notm}} Platform kann den Network Monitor verwenden, um Ihre Organisation zum Kanal hinzuzufügen, auch wenn Sie auf der Plattform keine Peers hosten.

      Der ferne Peer lädt sein Signierzertifikat während der Installation hoch, sodass Sie das Zertifikat lediglich mit dem Kanal synchronisieren müssen. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, dem Ihre Organisation beigetreten ist, und wählen Sie in der Dropdown-Liste unter der Überschrift **Aktion** die Option **Zertifikat synchronisieren** aus. Durch diese Aktion werden die Zertifikate für alle Peers auf dem Kanal synchronisiert.

   3. Rufen Sie die Konfigurationsinformationen aus der Datei `creds.json` ab, die in der Anzeige "Übersicht" des Network Monitor verfügbar ist. Klicken Sie auf **Verbindungsprofil** und dann auf **Download**.

     - Suchen Sie die URL des Anordnungsknotens, indem Sie nach **orderers** suchen. Das Element befindet sich unter `orderers > url`. Notieren Sie die URL, die mit dem Netznamen beginnt. Daraufhin wird die URL wie im folgenden Beispiel angezeigt:

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - Suchen Sie den Namen Ihrer Organisation, indem Sie bei der Suche **organizations** angeben. Hierbei sollte es sich um dieselbe Organisation handeln, die Sie zum Registrieren Ihres fernen Peers verwenden. Der Name Ihrer Organisation wird mit dem zugehörigen Wert für `mspid` angezeigt. Notieren Sie den Wert für `mspid`.

    4. Führen Sie die folgenden Befehle aus, um die Umgebungsvariablen im Peer-Container festzulegen.

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
         - Ersetzen Sie `<PEER_ADDR>` durch `localhost:7051`.

       Beispiel:

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. Führen Sie den folgenden CLI-Befehl für den Peer aus, um den Genesis-Block des Kanals abzurufen.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **Hinweis:** Möglicherweise werden Sie bei der Ausführung eines dieser CLI-Befehle feststellen, dass die folgende Warnung angezeigt wird, die jedoch ignoriert werden kann.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     Überprüfen Sie, ob der Befehl erfolgreich ausgeführt wurde und ob der Genesis-Block zum Peer-Container hinzugefügt wurde, indem Sie den folgenden Befehl ausführen:

     ```
     ls *.block
     ```
     {:codeblock}

     Sie wissen, dass der Genesis-Block erfolgreich hinzugefügt wurde, wenn Sie die im folgenden Beispiel dargestellte Ausgabe erhalten:

     ```
     defaultchannel_0.block
     ```

## Weitere Schritte
{: #whats-next-icp}

Nachdem Sie nun den fernen Peer in ICP eingerichtet haben, können Sie verschiedene operative Schritte ausführen, bevor Sie Transaktionen an das Blockchain-Netz übergeben und das verteilte Ledger aus dem Blockchain-Netz lesen können. Weitere Informationen finden Sie im Abschnitt zum [Betrieb eines fernen Peers](/docs/services/blockchain/howto/remote_peer_operate_icp.html).

## Hochverfügbarkeit
{: #high-availability}

Als Verfahren zur Sicherstellung der Hochverfügbarkeit hat es sich bewährt, mindestens zwei Peers pro Organisation bereitzustellen.

Führen Sie die folgenden Schritte aus, um unter ICP hoch verfügbare ferne Peers bereitzustellen:

1. Machen Sie sich mit der Vorgehensweise zum [Konfigurieren Ihres ICP-Clusters für die Hochverfügbarkeit ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "Hochverfügbarkeit für {{site.data.keyword.cloud_notm}} Private-Cluster") vertraut.
2. Installieren Sie den Peer mehrfach auf Ihrem Cluster, um Peers für unterschiedliche Workerknoten bereitzustellen. Das Helm-Diagramm enthält eine Anti-Affinitätsrichtlinie, die auf Ihrer Netz-ID und Organisations-ID (OrgID) basiert. Der ICP-Cluster erkennt, ob Ihre Peers eine Verbindung zu demselben Netz derselben Organisation herstellen und versucht, diese Peers auf unterschiedlichen Workerknoten anzuordnen.  
  **Hinweis:** Obwohl auf Basis der Richtlinie Peers möglichst auf unterschiedlichen Workerknoten platziert werden sollten, ist dies nicht zwingend erforderlich. Wenn Sie beispielsweise über nur einen Workerknoten verfügen, dann werden alle Peers auf diesem Knoten bereitgestellt.

Wenn Sie zumindest zwei Peers bereitstellen, dann müssen Sie Ihre [Anwendungen auch für die Hochverfügbarkeit](/docs/services/blockchain/v10_application.html#ha-app) konfigurieren.

## Sicherheitsaspekte
{: #icp-security}

Die fernen Peers werden außerhalb von {{site.data.keyword.blockchainfull}} Platform bereitgestellt. Aus diesem Grund sind Sie für das Management der Sicherheit der fernen Peers verantwortlich. Dies umfasst wichtige Bereiche der Sicherheit, die von Enterprise Plan-Netzen bereitgestellt werden, z. B. das Schlüsselmanagement und die Datenverschlüsselung. Machen Sie sich mit den folgenden Themen vertraut, wenn Sie die Sicherheit für Ihre fernen Peers überprüfen.

### Identität und Zugriffssteuerung
{: #icp-security-node}

Außerhalb von {{site.data.keyword.cloud_notm}} müssen Sie das Identitätsmanagement, die Authentifizierung und die Zugriffssteuerung für Benutzer einrichten, die mit den fernen Peers interagieren. Sie können das [ICP-Benutzermanagement ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "Sicherheit") verwenden, um diese Funktionen zu konfigurieren.

### Datensicherheit
{: #icp-security-data}

{{site.data.keyword.blockchainfull}} Platform Enterprise Plan verwendet die Verschlüsselung ganzer Platten, die auf einer [symmetrischen Schlüsselverschlüsselung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Symmetrische Verschlüsselung") basiert, um alle Daten zu schützen, die im Netz verwendet werden. Sie müssen in der eigenen Umgebung ähnliche Schritte ausführen, um die Daten des fernen Peers zu schützen.

### Datenspeicherort
{: #icp-data-residency}

Für den Datenspeicherort ist es erforderlich, dass die Verarbeitung und Speicherung aller Blockchain-Ledgerdaten innerhalb der Grenzen eines bestimmten Landes stattfinden.
Weitere Informationen zur Erfüllung der Kriterien für den Datenspeicherort finden Sie unter [Datenspeicherort](/docs/services/blockchain/remote_peer.html#data-residency).

### Schlüsselmanagement
{: #icp-security-key-management}

Das Schlüsselmanagement stellt einen kritischen Aspekt für die Sicherheit ferner Peers dar. Wenn ein privater Schlüssel manipuliert wird oder verloren geht, dann können feindliche Akteure möglicherweise auf die Daten und die Funktionalität Ihres fernen Peers zugreifen. {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan verwendet [Hardware Security Modules](/docs/services/blockchain/glossary.html#hsm) (HSM), um die privaten Schlüssel Ihres Netzes zu speichern. Bei HSM handelt es sich um eine physische Appliance, die verhindert, dass andere Parteien auf Ihren privaten Schlüssel zugreifen können.
Wenn Sie einem fernen Peer unter ICP bereitstellen, dann sind Sie für das Management Ihrer privaten Schlüssel verantwortlich. Obwohl {{site.data.keyword.blockchainfull_notm}} Platform Ihre privaten Schlüssel generiert, werden diese Schlüssel nicht unter Platform gespeichert. Es ist wichtig sicherzustellen, dass Ihre Schlüssel an einem sicheren Ort gespeichert werden, damit sie nicht manipuliert werden können. Sie finden den privaten Schlüssel Ihres fernen Peers im Keystore-Ordner des Peer-MSP im Verzeichnis `/mnt/crypto/peer/peer/msp/keystore/` des Peer-Containers. Weitere Informationen zu den Zertifikaten im fernen Peer finden Sie im Abschnitt zum [Membership Services Provider](/docs/services/blockchain/certificates.html#msp) im Lernprogramm [Zertifikate unter {{site.data.keyword.blockchainfull_notm}} Platform verwalten](/docs/services/blockchain/certificates.html).

Sie können Key Escrow verwenden, um verloren gegangene private Schlüssel wiederherzustellen. Der entsprechende Arbeitsschritt muss ausgeführt werden, bevor Sie einen Schlüssel verlieren. Wenn ein privater Schlüssel nicht wiederhergestellt werden kann, dann müssen sie neue private Schlüssel anfordern. Hierzu müssen Sie eine neue Identität bei Ihrer Zertifizierungsstelle registrieren. Außerdem sollten Sie in diesem Fall das signCert-Zertifikat von allen Kanälen entfernen, denen Sie beigetreten sind, oder es auf diesen Kanälen ersetzen.

<!---
Wenn in ICP ein privater Schlüssel erstellt wird, dann werden zwei Sätze der unabhängigen Schlüsselinformationen generiert, die in zwei unterschiedlichen Entitäten aufbewahrt werden. Diese beiden Sätze mit Schlüsselinformationen werden dann kombiniert, um den privaten Schlüssel zu erstellen.
--->

### TLS
{: #icp-security-tls}

[Transport Layer Security ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Übersicht zum SSL- oder TLS-Handshake") (TLS) ist im Vertrauensmodell von Hyperledger Fabric eingebettet. Alle Komponenten unter {{site.data.keyword.blockchainfull_notm}} Platform verwenden TLS zur Authentifizierung und Kommunikation mit anderen Komponenten. Aus diesem Grund müssen die Netzkomponenten unter {{site.data.keyword.blockchainfull_notm}} Platform in der Lage sein, den TLS-Handshake mit Ihren fernen Peers auszuführen. Dies hat u. a. zur Folge, dass Sie den Durchgriff von den Client-Apps zu Ihrem Peer in Ihrer Web-Firewall (z. B. über ein Whitelisting) aktivieren müssen.

Sie können die gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) verwenden, für die eine 2-Wege-Authentifizierung (Server-Client) und keine 1-Wege-Authentifizierung (nur Server) erforderlich ist, um die Kommunikation zwischen Ihrer Anwendung und den Enterprise Plan-Netzen zu sichern. Sie können den Network Monitor zur [Aktivierung der gegenseitigen TLS-Authentifizierung](/docs/services/blockchain/v10_dashboard.html#mutual-tls) für Peers unter {{site.data.keyword.blockchainfull_notm}} Platform verwenden. Um die gegenseitige TLS-Authentifizierung auf Ihrem fernen Peer zu aktivieren, müssen Sie die Anweisungen zum [Aktivieren der gegenseitigen TLS-Authentifizierung für Peerknoten ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Sichern der Kommunikation mit Transport Layer Security") in der Hyperledger Fabric-Dokumentation befolgen. Es wird dringend empfohlen, die gegenseitige TLS-Authentifizierung für Ihre Anwendungen zu aktivieren.

### Konfiguration von Membership Service Provider
{: #icp-security-MSP}

{{site.data.keyword.blockchainfull_notm}} Platform-Komponenten verbrauchen Identitäten über Membership Service Providers (MSPs). MSPs ordnen die Zertifikate zu, die von den CAs mit den entsprechenden Netz- und Kanalrollen ausgegeben werden. Weitere Informationen zur Vorgehensweise von MSPs beim Arbeiten mit einem fernen Peer finden Sie im Abschnitt zu den [Membership Service Providers (MSPs)](/docs/services/blockchain/certificates.html#msp).

### Anwendungssicherheit
{: #icp-security-appl}

Da alle Chaincode-Aufrufe signiert werden, verwaltet Fabric die Anwendungssicherheit. Zusätzlich hierzu umfasst Fabric auch ACL-basierte Überprüfungen der Anwendungsebene.

## Lizenz und Preisstruktur
{: #license-pricing-icp}

{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer on {{site.data.keyword.cloud_notm}} Private ist eine kostenlose Betaedition, die sich zu Evaluierungs- und Versuchszwecken eignet und als ICP-Anwendung bereitgestellt wird. Sie können auf das [GitHub-Paket ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and--> zugreifen und das [Helm-Diagramm ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer-Helm-Diagramm") herunterladen.

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

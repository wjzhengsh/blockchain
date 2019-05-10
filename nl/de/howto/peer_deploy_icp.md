---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

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

# Peers in {{site.data.keyword.cloud_notm}} Private bereitstellen
{: #icp-peer-deploy}

In den folgenden Anweisungen wird die Vorgehensweise zur Bereitstellung eines Peers für {{site.data.keyword.blockchainfull}} Platform unter {{site.data.keyword.cloud_notm}} Private beschrieben. Mit diesen Anweisungen können Sie eine Verbindung zu Komponenten von {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private herstellen. Falls Sie einen Peer mit einem Starter Plan- oder Enterprise Plan-Netz unter {{site.data.keyword.cloud_notm}} verbinden wollen, finden Sie im Abschnitt [Peers bereitstellen und mit Starter Plan- oder Enterprise Plan-Netz verbinden](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) weitere Informationen.
{:shortdesc}

Lesen Sie vor der Bereitstellung eines Peers den Abschnitt mit [Hinweisen und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).


## Erforderliche Ressourcen
{: #icp-peer-deploy-resources-required}

Stellen Sie sicher, dass Ihr {{site.data.keyword.cloud_notm}} Private-System die Mindestvoraussetzungen für Hardwareressourcen erfüllt:

| Komponente | vCPU | RAM | Platte für Datenspeicherung |
|-----------|------|-----|-----------------------|
| Peer | 2 | 2 GB | 50 GB mit der Möglichkeit zur Erweiterung |
| CouchDB für Peer<br>(Nur bei Verwendung von CouchDB zutreffend) | 2| 2 GB | 50 GB mit der Möglichkeit zur Erweiterung |

 **Hinweise:**
 - Eine vCPU (virtuelle CPU) ist ein virtueller Kern, der einer virtuellen Maschine oder einem physischen Prozessorkern zugeordnet wird, wenn der Server nicht für virtuelle Maschinen partitioniert ist. Bei der Festlegung des virtuellen Prozessorkerns (VPC) für Ihre Bereitstellung in {{site.data.keyword.cloud_notm}} Private müssen Sie bestimmte vCPU-Voraussetzungen berücksichtigen. VPC ist eine Maßeinheit, mit der die Lizenzgebühren für IBM Produkte bestimmt werden. Weitere Informationen zu Szenarios für die Festlegung des VPC finden Sie unter [Virtueller Prozessorkern (Virtual Processor Core, VPC) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "IBM Licence Metric Tool 9.2").
 - Diese Mindestressourcenwerte sind für Tests und Versuchsreihen ausreichend. Für eine Umgebung mit einer größeren Menge von Transaktionen muss unbedingt eine ausreichend große Speicherkapazität zugeordnet sein, beispielsweise 250 GB für Ihren Peer. Die zu verwendende Speicherkapazität richtet sich nach der Anzahl der Transaktionen und der Anzahl der Signaturen, die in Ihrem Netz benötigt werden. Falls eine Überlastung des Speichers auf Ihrem Peer oder Anordnungsknoten droht, müssen Sie einen neuen Peer oder Anordnungsknoten mit einem größeren Dateisystem bereitstellen und über Ihre anderen Komponenten auf denselben Kanälen synchronisieren lassen.

## Speicher
{: #icp-peer-deploy-storage}

Sie müssen den Speicher ermitteln, der von Ihrem Peer benutzt werden soll. Falls Sie die Standardeinstellungen verwenden, erstellt das Helm-Diagramm eine neue Anforderung für einen persistenten Datenträger mit 8 Gi namens `my-data-pvc` für Ihre Peerdaten und einen weitere Anforderung für einen persistenten Datenträger mit 8 Gi namens `statedb-pvc` für Ihre Statusdatenbank.

Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der {{site.data.keyword.cloud_notm}} Private-Installation eine neue Speicherklasse (`storageClass`) eingerichtet wird. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse (`storageClass`) erstellen, bevor Sie {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.

Für die Bereitstellung des Peers stehen die Plattformen amd64 oder s390x zur Auswahl. Es ist jedoch zu beachten, dass die [dynamische Bereitstellung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamische Datenträgerbereitstellung") nur für amd64-Knoten in {{site.data.keyword.cloud_notm}} Private verfügbar ist. Wenn in Ihrem Cluster eine Kombination aus s390x- und amd64-Workerknoten eingesetzt wird, kann die dynamische Bereitstellung nicht verwendet werden.

Wenn Sie nicht mit der dynamischen Bereitstellung arbeiten, müssen [persistente Datenträger ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistente Datenträger") erstellt und mit Bezeichnungen eingerichtet werden, die zum Optimieren des PVC-Bindungsprozesses (PVC = Persistent Volume Claim) von Kubernetes verwendet werden können.

## Voraussetzungen für die Bereitstellung eines Peers
{: #icp-peer-deploy-prerequisites}

1. Bevor Sie in {{site.data.keyword.cloud_notm}} Private einen Peer installieren können, müssen Sie [{{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) installieren und [das {{site.data.keyword.blockchainfull_notm}} Platform Helm-Diagramm installieren](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Falls Sie Community Edition verwenden und dieses Helm-Diagramm in einem {{site.data.keyword.cloud_notm}} Private-Cluster ohne Internetkonnektivität ausführen wollen, müssen Sie auf einer mit dem Internet verbundenen Maschine Archive erstellen, bevor Sie die Archive im {{site.data.keyword.cloud_notm}} Private-Cluster installieren können. Weitere Informationen finden Sie unter [Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen"){:new_window}. Die Spezifikationsdatei `manifest.yaml` ist im Helm-Diagramm unter `ibm-blockchain-platform-dev/ibm_cloud_pak` zu finden.

3. Zunächst müssen Sie in {{site.data.keyword.cloud_notm}} Private eine [Zertifizierungsstelle bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Sie benötigen die Zertifizierungsstelle, um eine [Konfigurationsdatei für den Peer zu erstellen und als geheimen Kubernetes-Schlüssel in {{site.data.keyword.cloud_notm}} Private zu speichern](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-config-file).

4. Rufen Sie den Wert für die Proxy-IP-Adresse des Clusters Ihrer Zertifizierungsstelle in der {{site.data.keyword.cloud_notm}} Private-Konsole ab. **Hinweis:** Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um auf Ihre Proxy-IP-Adresse zugreifen zu können. Melden Sie sich beim {{site.data.keyword.cloud_notm}} Private-Cluster an. Klicken Sie im linken Navigationsfenster auf **Plattform** und anschließend auf **Knoten**, um die im Cluster definierten Knoten anzuzeigen. Klicken Sie auf den Knoten mit der Rolle `Proxy` und kopieren Sie den Wert, der in der Tabelle für die `Host-IP` angegeben ist. **Wichtig:** Speichern Sie diesen Wert. Sie benötigen ihn, wenn Sie das Feld für die `Proxy-IP-Adresse` des Helm-Diagramms konfigurieren.


## Geheimen Schlüssel für Peerkonfiguration erstellen
{: #icp-peer-deploy-config-file}

Damit Sie einen Peer bereitstellen können, müssen Sie eine Konfigurationsdatei erstellen, die wichtige Informationen zur Identität des Peers und zur Zertifizierungsstelle enthält. Anschließend müssen Sie diese Datei während der Konfiguration mithilfe eines [Kubernetes-Objekts für einen geheimen Schlüssel ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/configuration/secret/) an das Helm-Diagramm übergeben. Diese Datei ermöglicht es dem Peer, von der Zertifizierungsstelle die Zertifikate abzrufen, die für den Beitritt zu einem Blockchain-Netz benötigt werden. Außerdem enthält diese Datei ein Administratorzertifikat, mit dem Sie Ihren Peer betreiben können. Befolgen Sie die Anweisungen im Abschnitt über die [Verwendung der Zertifizierungsstelle zum Bereitstellen eines Anordnungsknotens oder Peers](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer), bevor Sie den Peer konfigurieren.

Sie müssen die CSR-Hostnamen in der Konfigurationsdatei angeben. Hierzu gehört auch der `Service-Host-Name`, der mit dem während der Bereitstellung von Ihnen angegebenen `Namen des Helm-Release` identisch ist. Falls Sie beispielsweise für den `Namen des Helm-Release` den Wert `org1peer1` angeben, müssen Sie den folgenden Wert im Abschnitt `"csr"` der Datei einfügen:
```
"csr": {
  "hosts": [
     "9.42.134.44",
    "org1peer1"
  ]
}
```
{:codeblock}

Nachdem Sie die Konfigurationsdatei gespeichert haben, müssen Sie sie im Base64-Format codieren, bevor Sie die Datei für {{site.data.keyword.cloud_notm}} Private als Objekt mit einem geheimen Schlüssel bereitstellen. Mit einem geheimen Kubernetes-Schlüssel können Sie Informationen schützen und gemeinsam nutzen, ohne die Angaben direkt an die Bereitstellung übergeben zu müssen.

1. Codieren Sie die Konfigurationsdatei im Base64-Format, indem Sie die folgenden Befehle in einem Terminalfenster ausführen:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <config_file> | base64 $FLAG
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

Speichern Sie die Ausgabe, die im obigen Schritt 4 als Ergebnis erzielt wird.

2. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an. Klicken Sie im linken Navigationsfenster auf **Konfiguration** und anschließend auf **Geheime Schlüssel**. Klicken Sie auf die Schaltfläche **Geheimen Schlüssel erstellen**, um ein Popup-Fenster zu öffnen, in dem Sie ein neues Objekt für einen geheimen Schlüssel generieren können.

3. Füllen Sie auf der Registerkarte **Allgemein** die folgenden Felder aus:
  - **Name:** Geben Sie für Ihren geheimen Schlüssel einen Namen ein, der in Ihrem Cluster eindeutig ist. Diesen Namen verwenden Sie beim Bereitstellen Ihres Peers. Der Name darf nur aus Kleinbuchstaben bestehen.  
  **Hinweis:** Wenn Sie einen Peer bereitstellen, wird durch die Bereitstellung automatisch ein neuer geheimer Schlüssel mit dem Namen `<helm release name you intend to use>-secret` generiert. Achten Sie daher beim Benennen des geheimen Schlüssels darauf, dass sich der Name des geheimen Schlüssels von `<helm release name you intend to use>-secret` unterscheidet. Andernfalls schlägt die Bereitstellung des Helm-Diagramms fehl, weil der geheime Schlüssel, den sie zu erstellen versucht, bereits vorhanden ist.
  - **Namensbereich:** Dies ist der Namensbereich, zu dem Ihr geheimer Schlüssel hinzugefügt werden soll. Wählen Sie den `Namensbereich` aus, in dem Sie Ihren Peer bereitstellen wollen.
  - **Typ:** Geben Sie den Wert `Opaque` ein.

4. Klicken Sie im linken Navigationsbereich dieses Fensters auf die Registerkarte **Daten**.
  - Geben Sie in den Feldern `Name` den Wert `secret.json` an und fügen Sie im Feld für den Wert die im `Base64-Format` codierte Zeichenfolge Ihrer Konfigurationsdatei ein.

5. (Optional) Falls Sie `CouchDB` als Statusdatenbank verwenden wollen, müssen Sie zu diesem Objekt für den geheimen Schlüssel zwei zusätzliche Werte hinzufügen. Klicken Sie auf der Registerkarte **Daten** auf die Schaltfläche **Daten hinzufügen**, um die Benutzer-ID und das Kennwort für CouchDB hinzuzufügen, die beim Bereitstellen des Containers für die Datenbank verwendet werden sollen.
  1. Erstellen Sie Ihren Benutzernamen und Ihr Kennwort und codieren Sie die Werte im Base64-Format. Führen Sie die folgenden Befehle in einem Terminalfenster aus und ersetzen Sie hierbei `admin` und `adminpw` durch die Werte, die Sie verwenden wollen.
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. Geben Sie im Feld **Name** den Wert `couchdbuser` ein. Geben Sie im entsprechenden Feld **Wert** das Ergebnis des Befehls `echo -n 'couch' | base64 $FLAG` aus Schritt 1 oben ein.
  3. Klicken Sie auf die Schaltfläche **Daten hinzufügen**, um ein zweites Schlüssel/Wert-Paar hinzuzufügen.
  4. Geben Sie im zweiten Feld **Name** den Wert `couchdbpwd` ein. Geben Sie im entsprechenden Feld **Wert** das Ergebnis des Befehls `echo -n 'couchpw' | base64 $FLAG` aus Schritt 1 oben ein.

6. Klicken Sie auf **Erstellen**, um das Objekt für den geheimen Schlüssel zu speichern.

**Hinweis:** Der geheime Schlüssel für die Konfiguration des Peers wird nicht aus dem {{site.data.keyword.cloud_notm}} Private-Cluster entfernt, wenn Sie das Helm-Release löschen. Wenn Sie Ihren Peer löschen, müssen Sie diesen geheimen Schlüssel ebenfalls löschen.

## Konfiguration
{: #peer-configuration}

Nachdem Sie das Objekt für den geheimen Schlüssel der Konfiguration des Peers erstellt haben, können Sie Ihren Peer mit den folgenden Schritten in {{site.data.keyword.cloud_notm}} Private konfigurieren und installieren. Sie können jeweils nur einen einzigen Peer installieren.


1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf den Link **Katalog** in der oberen rechten Ecke.
2. Klicken Sie im linken Navigationsfenster auf `Blockchain` und suchen Sie nach der Kachel namens `ibm-blockchain-platform-prod` bzw. `ibm-blockchain-platform-dev`, falls Sie die Community Edition heruntergeladen haben. Klicken Sie auf die Kachel, um sie zu öffnen. Daraufhin wird eine Readme-Datei mit Informationen zur Installation und Konfiguration des Helm-Diagramms angezeigt.
3. Klicken Sie oben in der Anzeige auf die Registerkarte **Konfiguration** oder klicken Sie in der rechten unteren Ecke der Anzeige auf die Schaltfläche **Konfigurieren**.
4. Geben Sie die Werte für die [allgemeinen Konfigurationsparameter](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-configuration-parms) an und akzeptieren Sie die Lizenzvereinbarung.
5. Öffnen Sie das Twistie `Alle Parameter` und geben Sie den Wert für die [globalen Konfigurationsparameter](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-global-parameters) an.
6. Blättern Sie bis zum Abschnitt **Konfiguration des Peers** vor. Wählen Sie das Kontrollkästchen `Peer installieren` aus und füllen Sie die [Konfigurationseinstellungen](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-parameters) für den Peer aus.
7. Klicken Sie auf **Installieren**.

### Konfigurationsparameter
{: #icp-peer-deploy-configuration-parms}

Die folgende Tabelle enthält eine Auflistung der **speziell für die Peerkomponente** konfigurierbaren Parameter von {{site.data.keyword.blockchainfull_notm}} Platform sowie die zugehörigen Standardwerte. Wenn Sie mit den Peer ausprobieren wollen oder diese Komponente zum ersten Mal verwenden, verwenden Sie die Standardwerte für die Datenbank- und Speicherparameter. Blättern Sie zum Abschnitt für die Peerkomponente, wählen Sie das Kontrollkästchen `Peer installieren` aus und geben Sie die zugehörigen Konfigurationsinformationen nur für diese Komponente an. **Obwohl in der Benutzerschnittstelle für das Helm-Diagramm angegeben ist, dass keine weitere Konfiguration erforderlich ist, müssen Sie bestimmte Parameter eingeben, um einen Peer bereitzustellen.**

#### Allgemeine und globale Konfigurationsparameter
{: #icp-peer-deploy-global-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
|**Allgemeine Parameter**| Parameter zur Konfiguration des Helm-Diagramms | | |
| `Helm-Releasename`| Der Name Ihres Helm-Release. Er muss mit einem Kleinbuchstaben beginnen und mit einem alphanumerischen Zeichen enden, darf jedoch ausschließlich Bindestriche und alphanumerische Zeichen in Kleinschreibung enthalten. Bei jedem Versuch, eine Komponente zu installieren, müssen Sie einen eindeutigen Namen für das Helm-Release verwenden. **Wichtig:** Dieser Wert muss mit dem Wert übereinstimmen, den Sie zum Generieren des Service-Host-Namens für das Feld "hosts" in Ihrer [JSON-Datei mit dem geheimen Schlüssel](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-config-file) verwendet haben. | Nein | Ja |
| `Zielnamensbereich`| Wählen Sie den Kubernetes-Namensbereich für die Installation des Helm-Diagramms aus. | Nein | Ja |
| `Richtlinien für den Zielnamensbereich`| Zeigt die Pod-Sicherheitsrichtlinien des ausgewählten Namensbereichs an, die eine Richtlinie **`ibm-privileged-psp`** beinhalten müssen. Andernfalls [binden Sie eine Pod-Sicherheitsrichtlinie](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp) an Ihren Namensbereich. | Nein | Nein |
|**Globale Konfiguration**| Diese Parameter gelten für alle Komponenten im Helm-Diagramm.|||
| `Name des Servicekontos`| Geben Sie den Namen des [Servicekontos ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) ein, das Sie zur Ausführung des Pods verwenden wollen. | Standardwert | Nein |

#### Konfigurationsparameter für Peers
{: #icp-peer-deploy-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
|**Clusterkonfiguration** |**Informationen zur Clusterkonfiguration** | ||
| `Peer installieren` | Wählen Sie diese Option aus, um einen Peer zu installieren.|Nicht ausgewählt | Ja, falls Sie einen Peer bereitstellen wollen. |
| `Workerknotenarchitektur für Peer`| Wählen Sie Ihre Cloud-Plattformarchitektur aus (AMD64 oder S390x).| AMD64 | Ja |
| `Image-Repository für Peer`| Die Position des Helm-Diagramms für den Peer. In diesem Feld wird automatisch der installierte Pfad eingetragen. | ibmcom/ibp-fabric-peer | Ja |
| `Tag für Docker-Image des Peers`|Der Wert des Tags, das dem Image des Peers zugeordnet ist. |1.4.0 (wird automatisch mit dem richtigen Wert gefüllt)|Ja|
| `Peerkonfiguration`| Sie können die Konfiguration des Peers anpassen, indem Sie in diesem Feld eine eigene Konfigurationsdatei `core.yaml` einfügen. Ein Beispiel für eine Datei `core.yaml` finden Sie auf der Seite [`core.yaml` sample config ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/core.yaml) **(nur für fortgeschrittene Benutzer)**. | Nein | Nein |
| `Geheimer Schlüssel für Peerkonfiguration (erforderlich)`|Der Name des [geheimen Schlüssels für die Peerkonfiguration](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy-config-file), den Sie in {{site.data.keyword.cloud_notm}} Private erstellt haben. | Nein | Ja |
|`MSP der Organisation (erforderlich)`| Sie können für die MSP-ID der Organisation einen neuen Wert wie beispielsweise "org1" erstellen oder einen vorhandenen MSP für eine Organisation angeben, zu der der Peer gehören soll. Falls Sie eine Organisation für den Anordnungsknoten bereitgestellt haben, müssen Sie darauf achten, dass sich die MSP-IDs für Peers von der MSP-ID für den Anordnungsknoten unterscheiden. Notieren Sie sich außerdem diesen Wert; Sie benötigen ihn später für `CORE_PEER_LOCALMSPID` und `configtx.yaml`. | Nein | Ja |
|`Peer-Service-Typ`| Hiermit wird angegeben, ob auf dem Peer [externe Ports zugänglich gemacht werden sollen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types). Wählen Sie "NodePort" aus, damit die Ports extern zugänglich gemacht werden (empfohlen); wählen Sie "ClusterIP" aus, damit die Ports nicht zugänglich gemacht werden. Die Optionen "LoadBalancer" und "ExternalName" werden in diesem Release nicht unterstützt. | NodePort | Ja |
| `Statusdatenbank`| Zur Speicherung des Kanalledgers verwendete [Statusdatenbank](/docs/services/blockchain/glossary.html#glossary-state-database). | Nein | Ja |
|`Image-Repository für CouchDB`| Gilt nur, wenn CouchDB als Ledgerdatenbank ausgewählt wurde. In diesem Feld wird automatisch der installierte Pfad eingetragen. Falls Sie die Community Edition verwenden und kein Zugang zum Internet besteht, sollte dieser Wert mit dem Verzeichnis übereinstimmen, in das Sie das Fabric-Image für CouchDB heruntergeladen haben.| ibmcom/ibp-fabric-couchdb | Ja |
| `Tag für Docker-Image von CouchDB`| Gilt nur, wenn CouchDB als Ledgerdatenbank ausgewählt wurde. Der Wert des Tags, der dem CouchDB-Image zugeordnet ist. | Wird automatisch mit dem richtigen Wert gefüllt. | Ja |
| `Datenpersistenz für Peer aktiviert`| Aktivieren der Funktionalität zum persistenten Speichern von Daten nach Neustart oder Ausfall des Clusters. Siehe hierzu [Speicher in Kubernetes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/ "Volumes"). *Wird diese Option inaktiviert, gehen alle Daten bei einem Failover oder Podneustart verloren.* | Aktiviert | Nein |
| `Dynamische Bereitstellung für Peer verwenden`| Wählen Sie diese Option aus, um die dynamische Bereitstellung für Speicherdatenträger zu aktivieren. | Aktiviert | Nein |
| `Volume Claim für Peer`| Nur für eine neue Anforderung eines Datenträgers (Volume Claim). Geben Sie den Namen der neuen Anforderung für persistenten Datenspeicher (Persistent Volume Claim, PVC) ein, die erstellt werden soll. | my-data-pvc | Nein |
| `Speicherklassenname für Peer`| Geben Sie einen Speicherklassennamen für den Peer an. | Leer, wenn ein neuer PVC erstellt wird; andernfalls muss die Speicherklasse angegeben werden, die dem vorhandenen PVC zugeordnet ist | Nein |
| `Vorhandener Volume Claim für Peer`| Angeben des Namens eines vorhandenen Volume Claims. Alle anderen Felder leer lassen. | Nein | Nein |
| `Selektorkennsatz für Peer`| [Selektorkennsatz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") für Ihren PVC.| Nein | Nein |
| `Selektorwert für Peer`| [Selektorwert ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") für Ihren PVC. | Nein | Nein |
| `Speicherzugriffsmodus für Peer`| Angeben des [Zugriffsmodus ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") des Speichers für den PVC.  | ReadWriteMany| Nein |
| `Volume Claim-Größe für Peer`| Die Größe des Volume Claims, die größer als 2Gi sein muss. | 8Gi  | Ja |
| `Persistent Volume Claim der Statusdatenbank`| Nur für eine neue Anforderung eines Datenträgers (Volume Claim). Geben Sie den Namen der neuen Anforderung für persistenten Datenspeicher (Persistent Volume Claim, PVC) ein, die erstellt werden soll. | statedb-pvc | Nein |
| `Speicherklassenname der Statusdatenbank`| Geben Sie einen Speicherklassennamen für die Statusdatenbank an. | Nein | Nein |
| `Vorhandener Volume Claim der Statusdatenbank`| Angeben des Namens eines vorhandenen Volume Claims. Alle anderen Felder leer lassen. | Nein | Nein |
| `Selektorkennsatz für Statusdatenbank`| Der [Selektorkennsatz](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) für den PVC.| Nein | Nein |
| `Selektorwert für Statusdatenbank`| Der [Selektorwert](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) für den PVC. | Nein | Nein |
| `Speicherzugriffsmodus für Statusdatenbank`| Angeben des [Zugriffsmodus ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") des Speichers für den PVC. | ReadWriteMany| Nein |
| `Volume Claim-Größe der Statusdatenbank`| Wählen Sie die Größe der zu verwendenden Platte aus. | 8Gi | Ja |
| `Datenpersistenz für CouchDB aktiviert`| Ledgerdaten sind für CouchDB-Container beim Neustart des Containers verfügbar. *Wird diese Option inaktiviert, gehen alle Daten bei einem Failover oder Podneustart verloren.*| Aktiviert | Nein |
| `Dynamische Bereitstellung für CouchDB verwenden`| Für CouchDB-Container wird dynamischer Speicher von Kubernetes verwendet.| Aktiviert | Nein |
| `CPU-Anforderung für Docker-in-Docker`| Geben Sie die Mindestanzahl der CPUs an, die dem Container zugeordnet werden sollen, in dem der Chaincode ausgeführt wird. | 1 | Ja |
| `CPU-Grenzwert für Docker-in-Docker`| Geben Sie die maximale Anzahl der CPUs an, die dem Container zugeordnet werden sollen, in dem der Chaincode ausgeführt wird. | 2 | Ja |
| `Speicheranforderung für Docker-in-Docker`| Geben Sie den Mindestumfang des Hauptspeichers an, der dem Container zugeordnet werden soll, in dem der Chaincode ausgeführt wird. | 1 Gi | Ja |
| `Speicherbegrenzung für Docker-in-Docker`| Geben Sie den Höchstumfang des Hauptspeichers an, der dem Container zugeordnet werden soll, in dem der Chaincode ausgeführt wird. | 4Gi | Ja |
| `CPU-Anforderung für gRPC-Web-Proxy`| Geben Sie die Mindestanzahl der CPUs in Millicore (m) an, die dem gRPC-Web-Proxy zugeordnet werden soll. | 100 m | Ja |
| `CPU-Grenzwert für gRPC-Web-Proxy`| Geben Sie die maximale Anzahl der CPUs in Millicore (m) an, die dem gRPC-Web-Proxy zugeordnet werden soll. | 200 m | Ja |
| `Speicheranforderung für gRPC-Web-Proxy`| Geben Sie den Mindestumfang des Hauptspeichers an, der dem gRPC-Web-Proxy zugeordnet werden soll. | 100 Mi | Ja |
| `Speicherbegrenzung für gRPC-Web-Proxy`| Geben Sie den Höchstumfang des Hauptspeichers an, der dem gRPC-Web-Proxy zugeordnet werden soll. | 200 Mi | Ja |
| `Peer-CPU-Anforderung` | Mindestanzahl der CPUs, die dem Peer zugeordnet werden soll. | 1 | Ja |
| `Peer-CPU-Limit` | Maximale Anzahl CPUs, die dem Peer zugeordnet werden soll.| 2 | Ja |
| `Peer-Speicheranforderung` | Mindestens erforderlicher Speicher, der dem Peer zugeordnet werden soll. | 1 Gi | Ja |
| `Peer-Speicherbegrenzung` | Maximalwert für Speicher, der dem Peer zugeordnet werden soll. | 4Gi | Ja |
| `CouchDB-CPU-Anforderung` | Mindestanzahl der CPUs, die für CouchDB zugeordnet werden soll.| 1 | Ja |
| `CouchDB-CPU-Limit` | Maximale Anzahl CPUs, die für CouchDB zugeordnet werden soll. | 2 | Ja |
| `CouchDB-Speicheranforderung` | Mindestens erforderlicher Speicher, der für CouchDB zugeordnet werden soll.| 1 Gi | Ja |
| `CouchDB-Speicherbegrenzung` | Maximalwert für Speicher, der für CouchDB zugeordnet werden soll. | 4Gi | Ja |


Wird für die Datenbank des Peers 'CouchDB' ausgewählt, werden zwei Container im Pod erstellt, und zwar einer für den Peer und der andere für CouchDB.
Der Peer-Container enthält einen einzelnen Datenträgermount für den Peer-PVC, in dem die Blöcke und Transaktionen im Dateisystem gespeichert sind. Der CouchDB-Container hängt den PVC für die Statusdatenbank des Peers an, der die Ledgerdaten enthält.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Helm-Release mit der Helm-Befehlszeile installieren
{: #icp-peer-deploy-helm-cli}

Zur Installation des Helm-Release können Sie alternativ auch die CLI `helm` verwenden. Stellen Sie vor Ausführung des Befehls `helm install` sicher, dass Sie [das Helm-Repository Ihres Clusters zur Helm-CLI-Umgebung hinzugefügt haben ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Internes Helm-Repository zur Helm-CLI hinzufügen").

Die für die Installation erforderlichen Parameter können Sie festlegen, indem Sie eine Datei `yaml` erstellen und sie an den folgenden Befehl `helm install` übergeben.
```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

Hierbei gilt Folgendes:
- `<helm_release name>` steht für den Namen, den Sie für das Helm-Release vergeben möchten.
- `<helm_chart>` steht für den Namen des Helm-Diagramms, das in den Katalog importiert wird.
- `<helm_chart_version>` steht für die Version des Helm-Diagramms, das in den Katalog importiert wird.
- `<customvalues.yaml>` ist der Name der YAML-Datei, in der die Konfigurationsparameter enthalten sind.

Beispiel:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```

Sie können eine neue Datei `yaml` erstellen, indem Sie die Datei `values.yaml` bearbeiten, die in der heruntergeladenen Archivdatei enthalten ist. In dieser Datei befinden sich alle erforderlichen Parameter mit ihren Standardeinstellungen.

## Peer-Installation überprüfen
{: #icp-peer-deploy-verify-installation-icp}

Nachdem Sie die Konfigurationsparameter ausgefüllt und auf die Schaltfläche **Installieren** geklickt haben, können Sie die Bereitstellung prüfen, indem Sie auf die Schaltfläche **Helm-Release anzeigen** klicken. Wenn die Bereitstellung erfolgreich durchgeführt wurde, sollte der Wert 1 in den Feldern `GEWÜNSCHT`, `DERZEIT`, `AKTUELL` und `VERFÜGBAR` der Tabelle "Bereitstellung" angezeigt werden. Möglicherweise müssen Sie zur Aktualisierung klicken und warten, bis die Tabelle aktualisiert angezeigt wird. Die Tabelle "Bereitstellung" können Sie außerdem aufrufen, indem Sie in der linken oberen Ecke der {{site.data.keyword.cloud_notm}} Private-Konsole auf das **Menüsymbol** klicken. Klicken Sie in der Menüliste auf **Workloads** und dann auf **Helm-Releases**.

Wenn Sie bis zum Abschnitt `Hinweise` vorblättern, finden Sie wichtige Informationen, die Sie zum [Betrieb Ihres Peers](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate) verwenden.

## Protokolle des Peers anzeigen
{: #icp-peer-deploy-view-logs}

Peerprotokolle können mit den [Befehlen der CLI "kubectl"](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your window into the Elastic Search") angezeigt werden. Weitere Informationen enthalten die [Anweisungen für den Zugriff auf die Protokolle](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-view-logs).

## Nächste Schritte
{: #icp-peer-deploy-next-steps}

Nachdem Sie den Peer bereitgestellt haben, müssen Sie verschiedene operative Schritte ausführen, bevor Sie Transaktionen an das Blockchain-Netz übergeben und das verteilte Ledger aus dem Blockchain-Netz lesen können. Weitere Informationen finden Sie unter [Peers in einem Multi-Cloud-Netz betreiben](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate).

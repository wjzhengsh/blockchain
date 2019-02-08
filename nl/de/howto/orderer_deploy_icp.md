---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Anordnungsknoten in {{site.data.keyword.cloud_notm}} Private bereitstellen
{: #orderer-deploy}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Anordnungsknoten authentifizieren Clients, ordnen Transaktionen an und senden Transaktionen in einem Blockchain-Netz mit der Anordnungskomponente. Weitere Informationen zu Anordnungsknoten und der Rolle, die sie in einem Blockchain-Netz übernehmen, enthält der Abschnitt [Blockchain-Komponenten im Überblick](/docs/services/blockchain/blockchain_component_overview.html).{:shortdesc}

Lesen Sie vor der Bereitstellung eines Anordnungsknotens den Abschnitt mit [Hinweisen und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

## Erforderliche Ressourcen
{: #ca-resources-required}

Stellen Sie sicher, dass Ihr ICP-System die Mindestvoraussetzungen für Hardwareressourcen erfüllt:

| Komponente | vCPU | RAM | Platte für Datenspeicherung |
|-----------|------|-----|-----------------------|
| Anordnungsknoten | 2 | 512 MB | 100 GB mit der Möglichkeit zur Erweiterung |


 **Hinweise:**
 - Eine vCPU (virtuelle CPU) ist ein virtueller Kern, der einer virtuellen Maschine oder einem physischen Prozessorkern zugeordnet wird, wenn der Server nicht für virtuelle Maschinen partitioniert ist. Bei der Festlegung des virtuellen Prozessorkerns (VPC) für Ihre Bereitstellung in ICP müssen Sie bestimmte vCPU-Voraussetzungen berücksichtigen. VPC ist eine Maßeinheit, mit der die Lizenzgebühren für IBM Produkte bestimmt werden. Weitere Informationen zu Szenarios für die Festlegung des VPC finden Sie unter [Virtueller Prozessorkern (VPC) ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Diese Mindestressourcenwerte sind für Tests und Versuchsreihen ausreichend. Für eine Umgebung mit einer größeren Menge von Transaktionen muss unbedingt eine ausreichend große Speicherkapazität zugeordnet sein, beispielsweise 500 GB für Ihren Anordnungsservice. Die zu verwendende Speicherkapazität richtet sich nach der Anzahl der Transaktionen und der Anzahl der Signaturen, die in Ihrem Netz benötigt werden. Falls eine Überlastung des Speichers auf Ihrem Anordnungsknoten droht, müssen Sie einen neuen Anordnungsknoten mit einem größeren Dateisystem bereitstellen und über Ihre anderen Komponenten auf denselben Kanälen synchronisieren lassen.

## Speicher
{: #storage}

Sie müssen den Speicher ermitteln, der von Ihrem Anordnungsknoten verwendet wird. Falls Sie die Standardeinstellungen verwenden, erstellt das Helm-Diagramm eine neue Anforderung für einen persistenten Datenträger mit 8 Gi namens `my-data-pvc` für Ihre Anordnungsknotendaten und einen weitere Anforderung für einen persistenten Datenträger mit 8 Gi namens `statedb-pvc` für Ihre Statusdatenbank.

Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der ICP-Installation eine *neue* Speicherklasse `(storageClass)` eingerichtet wurde. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse erstellen, bevor Sie die Bereitstellung vornehmen können.

Für die Bereitstellung des Anordnungsknotens stehen die Plattformen amd64 oder s390x zur Auswahl. Bitte beachten Sie jedoch, dass die [dynamische Bereitstellung](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) in ICP nur für amd64-Knoten verfügbar ist. Wenn in Ihrem Cluster eine Kombination aus s390x- und amd64-Workerknoten eingesetzt wird, kann die dynamische Bereitstellung nicht verwendet werden.

Wenn Sie nicht mit der dynamischen Bereitstellung arbeiten, müssen [persistente Datenträger](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) erstellt und mit Bezeichnungen eingerichtet werden, die zum Optimieren des PVC-Bindungsprozesses von Kubernetes verwendet werden können.


## Voraussetzungen für die Bereitstellung eines Anordnungsknotens
{: #prerequisites-orderer-icp}

1. Bevor Sie in ICP einen Anordnungsknoten installieren können, müssen Sie [ICP installieren](/docs/services/blockchain/ICP_setup.html) und das [Helm-Diagramm für {{site.data.keyword.blockchainfull_notm}} Platform installieren](/docs/services/blockchain/howto/helm_install_icp.html).

2. Falls Sie Community Edition verwenden und dieses Helm-Diagramm in einem ICP-Cluster ohne Internetkonnektivität ausführen wollen, müssen Sie auf einer mit dem Internet verbundenen Maschine Archive erstellen, bevor Sie die Archive im ICP-Cluster installieren können. Weitere Informationen finden Sie unter [Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen"){:new_window}. Die Spezifikationsdatei `manifest.yaml` ist im Helm-Diagramm unter "ibm-blockchain-platform-dev/ibm_cloud_pak" zu finden.

3. Rufen Sie den Wert für die Proxy-IP-Adresse des Clusters Ihrer Zertifizierungsstelle in der ICP-Konsole ab. **Hinweis:** Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um auf Ihre Proxy-IP-Adresse zugreifen zu können. Melden Sie sich beim ICP-Cluster an. Klicken Sie im linken Navigationsfenster auf **Plattform** und anschließend auf **Knoten**, um die im Cluster definierten Knoten anzuzeigen. Klicken Sie auf den Knoten mit der Rolle `Proxy` und kopieren Sie den Wert, der in der Tabelle für die `Host-IP` angegeben ist. **Wichtig:** Speichern Sie diesen Wert. Sie benötigen ihn, wenn Sie das Feld für die `Proxy-IP-Adresse` des Helm-Diagramms konfigurieren.

4. Erstellen Sie eine [Konfigurationsdatei für den Anordnungsknoten und speichern Sie sie als geheimen Kubernetes-Schlüssel in ICP](#orderer-config-file).

## Konfigurationsdatei für Anordnungsknoten erstellen
{: #orderer-config-file}

Bevor Sie einen Anordnungsknoten bereitstellen, müssen Sie eine Konfigurationsdatei erstellen, die wichtige Informationen zur Identität des Anordnungsknotens und zur Zertifizierungsstelle enthält. Anschließend müssen Sie diese Datei während der Konfiguration mithilfe eines [Kubernetes-Objekts für einen geheimen Schlüssel ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/configuration/secret/) an das Helm-Diagramm übergeben. Diese Datei ermöglicht es dem Anordnungsknoten, von der Zertifizierungsstelle die Zertifikate abzrufen, die für den Beitritt zu einem Blockchain-Netz benötigt werden. Außerdem enthält diese Datei ein Administratorzertifikat, mit dem Sie Ihren Anordnungsknoten als Benutzer mit Administratorberechtigung betreiben können. Befolgen Sie die Anweisungen im Abschnitt über die [Verwendung der Zertifizierungsstelle zum Bereitstellen eines Anordnungsknotens oder Peers](/docs/services/blockchain/howto/CA_operate.html#deploy-orderer-peer), bevor Sie den Anordnungsknoten konfigurieren.

Sie müssen die CSR-Hostnamen in der Konfigurationsdatei angeben. Hierzu gehört auch der `Service-Host-Name`, der auf dem während der Bereitstellung von Ihnen angegebenen `Namen des Helm-Release` basiert. Der `Service-Host-Name` ist der Wert für `helm_release_name`, den Sie angeben, mit der am Ende hinzugefügten Zeichenfolge `-orderer`. Falls Sie beispielsweise für den `Namen des Helm-Release` den Wert `orderer1` angeben, können Sie den folgenden Wert im Abschnitt `"csr"` der Datei einfügen:

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

Nachdem Sie die Konfigurationsdatei gespeichert haben, müssen Sie sie im Base64-Format codieren, bevor Sie die Datei für ICP als Objekt mit einem geheimen Schlüssel bereitstellen. Mit einem geheimen Kubernetes-Schlüssel können Sie Informationen schützen und gemeinsam nutzen, ohne die Angaben direkt an die Bereitstellung übergeben zu müssen.

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

2. Melden Sie sich bei der ICP-Konsole an. Klicken Sie im linken Navigationsfenster auf **Konfiguration** und anschließend auf **Geheime Schlüssel**. Klicken Sie auf die Schaltfläche **Geheimen Schlüssel erstellen**, um ein Popup-Fenster zu öffnen, in dem Sie ein neues Objekt für einen geheimen Schlüssel generieren können.

3. Füllen Sie auf der Registerkarte **Allgemein** die folgenden Felder aus:
  - **Name:** Geben Sie für Ihren geheimen Schlüssel einen Namen ein, der in Ihrem Cluster eindeutig ist. Diesen Namen verwenden Sie beim Bereitstellen Ihres Anordnungsknotens. Der Name darf nur aus Kleinbuchstaben bestehen.  
  **Hinweis:** Wenn Sie einen Anordnungsknoten bereitstellen, wird durch die Bereitstellung automatisch ein neuer geheimer Schlüssel namens `<helm_release_name>-orderer-mspsecret` generiert. Achten Sie daher beim Benennen des geheimen Schlüssels darauf, dass sich der Name von `<helm_release_name>-orderer-mspsecret` unterscheidet. Andernfalls schlägt die Bereitstellung des Helm-Diagramms fehl, weil der geheime Schlüssel, den sie zu erstellen versucht, bereits vorhanden ist.
  - **Namensbereich:** Dies ist der Namensbereich, zu dem Ihr geheimer Schlüssel hinzugefügt werden soll. Wählen Sie den `Namensbereich` aus, in dem Sie Ihren Anordnungsknoten bereitstellen wollen.
  - **Typ:** Geben Sie den Wert `Opaque` ein.

4. Klicken Sie im linken Navigationsbereich dieses Fensters auf die Registerkarte **Daten**. Geben Sie in den Feldern `Name` den Wert `secret.json` an und fügen Sie im Feld für den Wert die im `Base64-Format` codierte Zeichenfolge Ihrer Konfigurationsdatei ein.

5. Klicken Sie auf **Erstellen**, um das Objekt für den geheimen Schlüssel zu speichern.

**Hinweis:** Der geheime Schlüssel für die Konfiguration des Anordnungsknotens wird nicht aus dem ICP-Cluster entfernt, wenn Sie das Helm-Release löschen. Wenn Sie Ihren Anordnungsknoten löschen, müssen Sie diesen geheimen Schlüssel ebenfalls löschen.

## Konfiguration
{: #orderer-configuration}

Nachdem Sie das Objekt für den geheimen Schlüssel der Konfiguration des Anordnungsknotens erstellt haben, können Sie Ihren Anordnungsknoten mit den folgenden Schritten in ICP konfigurieren und installieren. Sie können jeweils nur einen einzigen Anordnungsknoten installieren.

1. Melden Sie sich bei der ICP-Konsole an und klicken Sie auf den Link **Katalog** in der oberen rechten Ecke.
2. Klicken Sie im linken Navigationsfenster auf `Blockchain` und suchen Sie nach der Kachel namens `ibm-blockchain-platform-prod` bzw. `ibm-blockchain-platform-dev`, falls Sie die Community Edition heruntergeladen haben. Klicken Sie auf die Kachel, um sie zu öffnen. Daraufhin wird eine Readme-Datei mit Informationen zur Installation und Konfiguration des Helm-Diagramms angezeigt.
3. Klicken Sie oben in der Anzeige auf die Registerkarte **Konfiguration** oder klicken Sie in der rechten unteren Ecke der Anzeige auf die Schaltfläche **Konfigurieren**.
4. Geben Sie die Werte für die [allgemeinen Konfigurationsparameter](#orderer-global-parameters) an und akzeptieren Sie die Lizenzvereinbarung.
5. Öffnen Sie das Twistie `Alle Parameter` und geben Sie den Wert für die [globalen Konfigurationsparameter](#orderer-global-parameters) an.
6. Blättern Sie bis zum Abschnitt **Konfiguration des Anordnungsknotens** vor. Wählen Sie das Kontrollkästchen `Anordnungsknoten installieren` aus und füllen Sie die [Konfigurationseinstellungen](#orderer-parameters) für den Anordnungsknoten aus.
7. Klicken Sie auf **Installieren**.


**Hinweis:** Beim Bereitstellen des Anordnungsknotens für s390x gibt der Container "init" möglicherweise den folgenden Fehler zurück:

`panic: Error while trying to open DB: no locks available`

Dies liegt daran, dass der Anordnungsknoten eine unstrukturierte Datei als Datenbank verwendet und das NFS-Dateisystem ein zusätzliches Paket benötigt, damit der Anordnungsservice Dateien abfragen darf, um zu prüfen, ob exklusive Sperren für die Dateien bestehen, und damit er exklusive Sperren erstellen darf. Zur Lösung des Problems müssen Sie das Paket `rpc-statd` aktivieren.

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

Dies muss auf dem System ausgeführt werden, von dem aus das NFS-Dateisystem bereitgestellt wird.

### Konfigurationsparameter
{: #icp-orderer-configuration-parms}

Die folgende Tabelle enthält eine Auflistung der **speziell für die Anordnungsknotenkomponente** konfigurierbaren Parameter von {{site.data.keyword.blockchainfull_notm}} Platform sowie die zugehörigen Standardwerte. **Obwohl in der Benutzerschnittstelle für das Helm-Diagramm angegeben ist, dass keine weitere Konfiguration erforderlich ist, müssen Sie bestimmte Parameter eingeben, um einen Anordnungsknoten bereitzustellen.**

#### Allgemeine und globale Konfigurationsparameter
{: #orderer-global-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
| `Helm-Releasename`| Der Name Ihres Helm-Release. Er muss mit einem Kleinbuchstaben beginnen und mit einem alphanumerischen Zeichen enden, darf jedoch ausschließlich Bindestriche und alphanumerische Zeichen in Kleinschreibung enthalten. Bei jedem Versuch, eine Komponente zu installieren, müssen Sie einen eindeutigen Namen für das Helm-Release verwenden. **Wichtig:** Dieser Wert muss mit dem Wert übereinstimmen, den Sie zum Generieren des Service-Host-Namens für das Feld "hosts" in Ihrer [JSON-Datei mit dem geheimen Schlüssel](#orderer-config-file) verwendet haben. | Nein | Ja  |
| `Zielnamensbereich`| Wählen Sie den Kubernetes-Namensbereich für die Installation des Helm-Diagramms aus. | Nein | Ja |
|**Globale Konfiguration**| Diese Parameter gelten für alle Komponenten im Helm-Diagramm. |||
| `Name des Servicekontos`| Geben Sie den Namen des [Servicekontos ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) ein, das Sie zur Ausführung des Pods verwenden wollen. | Standardwert | Nein |

#### Konfigurationsparameter für Anordnungsknoten
{: #orderer-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
| `Anordnungsknoten installieren`| Wählen Sie diese Option aus, um einen Anordnungsknoten zu installieren. | Nicht ausgewählt | Ja, falls Sie einen Anordnungsknoten bereitstellen wollen. |
| `Workerknotenarchitektur für Anordnungsknoten`| Wählen Sie Ihre ICP-Workerknotenarchitektur (AMD64 oder S390X) aus. | Automatisch erkannte Architektur auf Basis des Masterknotens | Ja |
| `Konfiguration des Anordnungsknotens`| Sie können die Konfiguration des Anordnungsknotens anpassen, indem Sie in diesem Feld eine eigene Konfigurationsdatei `orderer.yaml` einfügen. Ein Beispiel für eine Datei `orderer.yaml` finden Sie auf der Seite [`orderer.yaml` sample config ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/orderer.yaml) **(nur für fortgeschrittene Benutzer)**. | Nein | Nein |
| `Geheimer Schlüssel für MSP der Organisation (erforderlich)`| Geben Sie den Namen des Objekts für den geheimen Schlüssel an, das die MSP-Zertifikate und -Schlüssel für die Organisation enthält. | Nein | Ja |
| `Datenpersistenz für Anordnungsknoten aktiviert`| Die Daten stehen zur Verfügung, wenn der Container neu gestartet wird. Wird diese Option inaktiviert, gehen alle Daten bei einem Failover oder Podneustart verloren. | Aktiviert | Nein |
| `Dynamische Bereitstellung für Anordnungsknoten verwenden`| Wählen Sie diese Option aus, um die dynamische Bereitstellung für Speicherdatenträger zu aktivieren. | Aktiviert | Nein |
| `Image-Repository für Anordnungsknoten`| Die Position des Helm-Diagramms für den Anordnungsknoten. In diesem Feld wird automatisch der installierte Pfad eingetragen. Falls Sie die Community Edition verwenden und kein Zugang zum Internet besteht, ändern Sie dieses Feld in die Position, an die Sie das Fabric-Image für den Anordnungsknoten heruntergeladen haben. | ibmcom/ibp-fabric-orderer | Nein |
| `Tag für Docker-Image des Anordnungsknotens`| Ein Datensatz mit dem Docker-Image. Dieses Feld wird automatisch mit der Imageversion gefüllt. Ändern Sie den Wert nicht. | 1.2.1 | Ja |
| `Konsenstyp des Anordnungsknotens`| Der Konsenstyp des Anordnungsservice. | SOLO | Ja |
| `Organisationsname des Anordnungsknotens`| Geben Sie den Namen an, den Sie für die Organisation des Anordnungsknotens verwenden wollen. Wenn Sie ebenfalls Peers bereitstellen wollen, müssen Sie darauf achten, einen anderen Namen als für Ihre Peers zu verwenden. Vergeben Sie für die Organisation des Anordnungsknotens beispielsweise einen Namen wie `ordererOrg`. | Nein | Ja |
| `MSP-ID für Organisation des Anordnungsknotens`| Geben Sie den Namen an, den Sie als MSP-ID für die Organisation des Anordnungsknotens verwenden wollen. Er sollte mit dem Namen identisch sein, den Sie für die Organisation des Anordnungsknotens vergeben, und wird vom Bereitstellungsprozess als Umgebungsvariable festgelegt. Notieren Sie sich diesen Wert; Sie müssen später auf ihn verweisen. | Nein | Ja |
| `Speicherklassenname für Anordnungsknoten`| Geben Sie einen Speicherklassennamen für den Anordnungsknoten an. | Nein | Ist davon abhängig, wie der ICP-Cluster konfiguriert ist. Erfragen Sie dies bei Ihrem Clusteradministrator. |
| `Vorhandener Volume Claim für Anordnungsknoten`| Geben Sie den Namen eines vorhandenen Volume Claims an und lassen Sie alle anderen Felder leer. | Nein | Nein |
| `Selektorkennsatz für Anordnungsknoten`| Der [Selektorkennsatz (Selector label) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) für Ihre Anforderung eines persistenten Datenträgers (Persistent Volume Claim, PVC). | Nein | Nein |
| `Selektorwert für Anordnungsknoten`| Der [Selektorwert (Selector value) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) für Ihre Anforderung eines persistenten Datenträgers (Persistent Volume Claim, PVC). | Nein | Nein |
| `Speicherzugriffsmodus für Anordnungsknoten`| Geben Sie den [Zugriffsmodus ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) für den PVC-Speicher an. | ReadWriteMany | Ja |
| `Volume Claim-Größe für Anordnungsknoten`| Wählen Sie die Größe der zu verwendenden Platte aus, die mindestens 2 Gi betragen muss. | 8 Gi | Ja |
| Servicetyp des Anordnungsknotens | Hiermit wird angegeben, ob auf dem Peer [externe Ports zugänglich gemacht werden sollen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types). Wählen Sie "NodePort" aus, damit die Ports extern zugänglich gemacht werden (empfohlen); wählen Sie "ClusterIP" aus, damit die Ports nicht zugänglich gemacht werden. Die Optionen "LoadBalancer" und "ExternalName" werden in diesem Release nicht unterstützt. | NodePort | Ja |
| `CPU-Anforderung für Anordnungsknoten` | Geben Sie die Mindestanzahl der CPUs an, die dem Anordnungsknoten zugeordnet werden sollen. | 1 | Ja |
| `CPU-Grenzwert für Anordnungsknoten` | Geben Sie die maximale Anzahl der CPUs an, die dem Anordnungsknoten zugeordnet werden sollen. | 2 | Ja |
| `Speicheranforderung für Anordnungsknoten` | Geben Sie den Mindestumfang des Speichers an, der dem Anordnungsknoten zugeordnet werden soll. | 1Gi | Ja |
| `Speicherbegrenzung für Anordnungsknoten` | Geben Sie den Höchstumfang des Speichers an, der dem Anordnungsknoten zugeordnet werden soll. | 2Gi | Ja |

### Helm-Release mit der Helm-Befehlszeile installieren
{: #icp-helm-cli}

Zur Installation des Helm-Release können Sie alternativ auch die Helm-CLI verwenden. Stellen Sie vor Ausführung des Befehls `helm install` sicher, dass Sie [das Helm-Repository Ihres Clusters zur Helm-CLI-Umgebung hinzugefügt haben ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Internes Helm-Repository zur Helm-CLI hinzufügen").

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
--values orderer-s390x-values.yaml \
--tls
```

Sie können eine neue Datei `yaml` erstellen, indem Sie die Datei `values.yaml` bearbeiten, die in der heruntergeladenen Archivdatei enthalten ist. In dieser Datei befinden sich alle erforderlichen Parameter mit ihren Standardeinstellungen.

## Installation des Anordnungsknotens prüfen

Nachdem Sie die Konfigurationsparameter ausgefüllt und auf die Schaltfläche **Installieren** geklickt haben, können Sie die Bereitstellung prüfen, indem Sie auf die Schaltfläche **Helm-Release anzeigen** klicken. Wenn die Bereitstellung erfolgreich durchgeführt wurde, sollte der Wert 1 in den Feldern `GEWÜNSCHT`, `DERZEIT`, `AKTUELL` und `VERFÜGBAR` der Tabelle "Bereitstellung" angezeigt werden. Möglicherweise müssen Sie zur Aktualisierung klicken und warten, bis die Tabelle aktualisiert angezeigt wird. Die Tabelle "Bereitstellung" können Sie außerdem aufrufen, indem Sie in der linken oberen Ecke der ICP-Konsole auf das **Menüsymbol** klicken. Klicken Sie in der Menüliste auf **Workloads** und dann auf **Helm-Releases**.

## Protokolle des Anordnungsknotens anzeigen
{: #orderer-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [`Befehlen der CLI "kubectl"`](/docs/services/blockchain/howto/orderer_operate.html#orderer-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem ICP-Cluster enthalten ist. Weitere Informationen enthalten die [Anweisungen für den Zugriff auf die Protokolle](/docs/services/blockchain/howto/orderer_operate.html#orderer-view-logs).

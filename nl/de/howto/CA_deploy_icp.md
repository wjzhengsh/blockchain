---

copyright:
  years: 2018, 2019
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

# Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private bereitstellen
{: #ca-deploy}

Nachdem Sie das Helm-Diagramm für {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.cloud_notm}} Private importiert haben, können Sie die einzelnen Komponenten bereitstellen. Die Zertifizierungsstelle (Certificate Authority, CA) ist das Stammverzeichnis der vertrauenswürdigen Elemente für Ihre Organisation und ermöglicht Ihnen die Generierung von Berechtigungsnachweisen für die anderen Komponenten, die Sie bereitstellen wollen. Infolgedessen müssen Sie eine Zertifizierungsstelle bereitstellen, bevor Sie die anderen Komponenten bereitstellen. Jede Organisation in einem Blockchain-Netz mit mehreren Clouds muss ihre eigene Zertifizierungsstelle bereitstellen.  Weitere Informationen zu Zertifizierungsstellen und der Rolle, die sie in einem Blockchain-Netz übernehmen, enthält der Abschnitt [Zertifizierungsstellen](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).
{:shortdesc}

Lesen Sie vor der Bereitstellung einer Zertifizierungsstelle den Abschnitt mit [Hinweisen und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Erforderliche Ressourcen
{: #ca-deploy-resources-required}

Stellen Sie sicher, dass Ihr {{site.data.keyword.cloud_notm}} Private-Cluster die Mindestvoraussetzungen für Hardwareressourcenressourcen erfüllt:

| Komponente | vCPU | RAM | Platte für Datenspeicherung |
|-----------|------|-----|-----------------------|
| Zertifizierungsstelle (CA) | 1 |192 MB | 1 GB |

**Hinweise:**
- Eine vCPU (virtuelle CPU) ist ein virtueller Kern, der einer virtuellen Maschine oder einem physischen Prozessorkern zugeordnet wird, wenn der Server nicht für virtuelle Maschinen partitioniert ist. Bei der Festlegung des virtuellen Prozessorkerns (VPC) für Ihre Bereitstellung in {{site.data.keyword.cloud_notm}} Private müssen Sie bestimmte vCPU-Voraussetzungen berücksichtigen. VPC ist eine Maßeinheit, mit der die Lizenzgebühren für IBM Produkte bestimmt werden. Weitere Informationen zu Szenarios für die Festlegung des VPC finden Sie unter [Virtueller Prozessorkern (Virtual Processor Core, VPC) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Virtueller Prozessorkern").
- Der Datenspeicherbedarf hängt davon ab, wie viele Identitäten und Zertifikate gespeichert werden. Der Speicher für die Zertifizierungsstelle ist nicht so umfangreich wie der Anordnungsknoten- oder Peerspeicher, variiert jedoch je nach Anwendungsfall. Je mehr Benutzer vorhanden sind, desto mehr Speicher wird benötigt.
- Diese Mindestressourcenwerte sind für Tests und Versuchsreihen ausreichend. Für eine Umgebung mit einer großen Anzahl von Transaktionen muss unbedingt eine ausreichende Speicherkapazität für die Zertifizierungsstelle zugeordnet werden. Die benötigte Speicherkapazität ist von der Anzahl der Transaktionen und der Anzahl der Signaturen abhängig, die in Ihrem Netz benötigt werden. Falls der Speicher der Zertifizierungsstelle überlastet wird, müssen Sie eine neue Zertifizierungsstelle mit einem größeren Dateisystem bereitstellen und sie über die anderen Zertifizierungsstellen auf denselben Kanälen synchronisieren lassen.

## Speicher
{: #ca-deploy-storage}

Sie müssen den Speicher ermitteln, der von Ihrer Zertifizierungsstelle verwendet wird. Falls Sie die Standardeinstellungen verwenden, erstellt das Helm-Diagramm eine neue Anforderung für einen persistenten Datenträger mit 2 Gi namens `fabric-ca-pvc` für Ihre Zertifizierungsstelle.

Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der Installation von {{site.data.keyword.cloud_notm}} Private eine *neue* Speicherklasse `(storageClass)` eingerichtet wurde. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse erstellen, bevor Sie die Zertifizierungsstelle bereitstellen können.

Für die Bereitstellung der Zertifizierungsstelle stehen die Plattformen AMD64 oder S390X zur Auswahl. Bitte beachten Sie jedoch, dass die [dynamische Bereitstellung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) in {{site.data.keyword.cloud_notm}} Private nur für AMD64-Knoten verfügbar ist. Wenn in Ihrem Cluster eine Kombination aus S390X- und AMD64-Workerknoten eingesetzt wird, kann die dynamische Bereitstellung nicht verwendet werden.

Wenn Sie nicht mit der dynamischen Bereitstellung arbeiten, müssen [persistente Datenträger ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) erstellt und mit Bezeichnungen eingerichtet werden, die zum Optimieren des PVC-Bindungsprozesses von Kubernetes verwendet werden können.

## Voraussetzungen für die Bereitstellung einer Zertifizierungsstelle
{: #ca-deploy-prerequisites}

1. Bevor Sie in {{site.data.keyword.cloud_notm}} Private eine Zertifizierungsstelle installieren können, müssen Sie [{{site.data.keyword.cloud_notm}} Private installieren](/docs/services/blockchain/ICP_setup.html#icp-setup) und das [Helm-Diagramm für {{site.data.keyword.blockchainfull_notm}} Platform installieren](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Falls Sie die Community Edition verwenden und dieses Helm-Diagramm in einem {{site.data.keyword.cloud_notm}} Private-Cluster ohne Internetkonnektivität ausführen wollen, müssen Sie auf einer mit dem Internet verbundenen Maschine Archive erstellen, bevor Sie die Archive im {{site.data.keyword.cloud_notm}} Private-Cluster installieren können. Weitere Informationen finden Sie unter [Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen"){:new_window}. Die Spezifikationsdatei "manifest.yaml" ist im Helm-Diagramm unter "ibm-blockchain-platform-dev/ibm_cloud_pak" zu finden.

3. Rufen Sie den Wert für die Proxy-IP-Adresse des Clusters in der {{site.data.keyword.cloud_notm}} Private-Konsole ab. **Hinweis:** Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um auf Ihre Proxy-IP-Adresse zugreifen zu können. Melden Sie sich am {{site.data.keyword.cloud_notm}} Private Cluster an. Klicken Sie im linken Navigationsfenster auf **Plattform** und anschließend auf **Knoten**, um die im Cluster definierten Knoten anzuzeigen. Klicken Sie auf den Knoten mit der Rolle `Proxy` und kopieren Sie den Wert, der in der Tabelle für die `Host-IP` angegeben ist.

  Speichern Sie diesen Wert. Sie benötigen ihn, wenn Sie das Feld für die `Proxy-IP-Adresse` des Helm-Diagramms konfigurieren.
  {:important}

4. Erstellen Sie den Namen und das Kennwort des Benutzers mit Administratorberechtigung für die Zertifizierungsstelle (CA-Administrator) und speichern Sie diese Werte in {{site.data.keyword.cloud_notm}} Private in einem Objekt für einen geheimen Schlüssel. Die Schritte zum Erstellen des geheimen Schlüssels sind im [nächsten Abschnitt](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret) beschrieben.

## Geheimen Schlüssel für CA-Administrator erstellen
{: #ca-deploy-admin-secret}

Beim erstmaligen Start enthält Ihre Zertifizierungsstelle eine Administratoridentität (admin), die Sie zum Betrieb der Zertifizierungsstelle erstellen. Für diese Administratoridentität müssen Sie einen Benutzernamen und ein Kennwort erstellen, bevor Sie die Zertifizierungsstelle bereitstellen. Notieren Sie sich unbedingt diese Werte. Sie benötigen sie später beim Betreiben der Zertifizierungsstelle und beim Bereitstellen von zusätzlichen Netzkomponenten. Erstellen Sie einen [geheimen Kubernetes-Schlüssel ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/configuration/secret/ "Kubernetes Secret"), um den `Benutzernamen` und das `Kennwort` des Administrators für Ihre Bereitstellung der Zertifizierungsstelle zu speichern. Mit einem geheimen Kubernetes-Schlüssel können Sie Informationen schützen und gemeinsam nutzen, ohne die Angaben direkt an die Bereitstellung übergeben zu müssen.

1. Erstellen Sie einen Benutzernamen und ein Kennwort für den Administrator und codieren Sie die Werte im Base64-Format. Führen Sie die folgenden Befehle in einem Terminalfenster aus und ersetzen Sie hierbei die Werte `admin` und `adminpw` durch die Werte, die Sie verwenden wollen.
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **Wichtig:** Speichern Sie die mit dem obigen Befehl erstellten Werte für `admin` und `adminpw`. Speichern Sie außerdem die verschlüsselten Werte dieser Felder, die beim Erstellen des Kubernetes-Objekts für den geheimen Schlüssel benötigt werden.

2. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an. Klicken Sie im linken Navigationsfenster auf **Konfiguration** und anschließend auf **Geheime Schlüssel**. Klicken Sie auf die Schaltfläche **Geheimen Schlüssel erstellen**, um ein Popup-Fenster zu öffnen, in dem Sie ein neues Objekt für einen geheimen Schlüssel generieren können.

3. Füllen Sie auf der Registerkarte **Allgemein** die folgenden Felder aus:
  - **Name:** Geben Sie für Ihren geheimen Schlüssel einen Namen ein, der in Ihrem Cluster eindeutig ist. Diesen Namen verwenden Sie zum Konfigurieren Ihrer Zertifizierungsstelle. Der Name darf nur aus Kleinbuchstaben bestehen.
  - **Namensbereich:** Dies ist der Namensbereich, zu dem Ihr geheimer Schlüssel hinzugefügt werden soll. Wählen Sie den `Namensbereich` aus, in dem Sie Ihre Zertifizierungsstelle bereitstellen wollen.
  - **Typ:** Geben Sie den Wert `Opaque` ein.

4. Lassen Sie die Registerkarte **Annotationen** leer.

5. Fügen Sie auf der Registerkarte **Daten** den Benutzernamen und das Kennwort als Schlüssel/Wert-Paare hinzu.
  1. Geben Sie im ersten Feld **Name** den Wert `ca-admin-name` ein.
  2. Geben Sie im ersten Feld **Wert** das Ergebnis des Befehls `echo -n admin | base64` aus Schritt 1 oben ein.
  3. Klicken Sie auf die Schaltfläche **Daten hinzufügen**, um ein zweites Schlüssel/Wert-Paar hinzuzufügen.
  4. Geben Sie im zweiten Feld **Name** den Wert `ca-admin-password` ein.
  5. Geben Sie im zweiten Feld **Wert** das Ergebnis des Befehls `echo -n adminpw | base64` aus Schritt 1 oben ein.  
    **Abbildung 1** zeigt einen möglichen Inhalt des geheimen Schlüssels in der {{site.data.keyword.cloud_notm}} Private-Konsole (abhängig von den Werten, die Sie für die Schlüssel `ca-admin-name` und `ca-admin-password` eingegeben haben).

    ![{{site.data.keyword.cloud_notm}} Private, geheimer Schlüssel in der Konsole](../images/CreateCASecret.png "{{site.data.keyword.cloud_notm}} Private, geheimer Schlüssel")  
    *Abbildung 1. Geheimer Schlüssel in der {{site.data.keyword.cloud_notm}} Private-Konsole*
  6. Klicken Sie auf **Erstellen**, um ein neues Objekt für einen geheimen Schlüssel zu generieren.

Das Helm-Diagramm für die Zertifizierungsstelle macht die Verwendung der Namen `ca-admin-name` und `ca-admin-password` als Schlüsselwerte erforderlich, damit der geheime Schlüssel für das Helm-Release bereitgestellt werden kann.

**Hinweis:** Der geheime Schlüssel für den CA-Administrator wird nicht aus dem {{site.data.keyword.cloud_notm}} Private-Cluster entfernt, wenn Sie das Helm-Release löschen. Für die Verwaltung Ihrer geheimen Schlüssel in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster sind Sie selbst verantwortlich. Falls Sie künftig eine andere Zertifizierungsstelle bereitstellen wollen, können Sie den geheimen Schlüssel für den CA-Administrator wiederverwenden. Andernfalls müssen Sie sich darum kümmern, dass er aus Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster gelöscht wird.

## Konfiguration
{: #ca-deploy-configuration}

Nachdem Sie den geheimen Schlüssel für den CA-Administrator erstellt haben, können Sie Ihre Zertifizierungsstelle mit den folgenden Schritten konfigurieren und installieren. Sie können jeweils nur eine einzige Zertifizierungsstelle installieren.

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf den Link **Katalog** in der oberen rechten Ecke.
2. Klicken Sie im linken Navigationsfenster auf `Blockchain` und suchen Sie nach der Kachel namens `ibm-blockchain-platform-prod` bzw. `ibm-blockchain-platform-dev`, falls Sie die Community Edition heruntergeladen haben. Klicken Sie auf die Kachel, um sie zu öffnen. Daraufhin wird eine Readme-Datei mit Informationen zur Installation und Konfiguration des Helm-Diagramms angezeigt.
3. Klicken Sie oben in der Anzeige auf die Registerkarte **Konfiguration** oder klicken Sie in der rechten unteren Ecke der Anzeige auf die Schaltfläche **Konfigurieren**.
4. Geben Sie die Werte für die [allgemeinen Konfigurationsparameter](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters) an und akzeptieren Sie die Lizenzvereinbarung.
5. Öffnen Sie das Twistie `Alle Parameter` und geben Sie den Wert für die [globalen Konfigurationsparameter](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters) an.
6. Blättern Sie bis zum Abschnitt **CA-Konfiguration** vor. Wählen Sie das Kontrollkästchen `CA installieren` aus und füllen Sie die [Konfigurationseinstellungen](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-parameters) für die Zertifizierungsstelle aus.  
7. Klicken Sie auf **Installieren**.

### Konfigurationsparameter
{: #ca-deploy-configuration-parms}

Die folgende Tabelle enthält eine Auflistung der **speziell für die CA-Komponente** konfigurierbaren Parameter von {{site.data.keyword.blockchainfull_notm}} Platform sowie die zugehörigen Standardwerte.  

**Obwohl in der Benutzerschnittstelle für das Helm-Diagramm angegeben ist, dass keine weitere Konfiguration erforderlich ist, müssen Sie bestimmte Parameter eingeben, um eine Zertifizierungsstelle bereitzustellen.**

#### Allgemeine und globale Konfigurationsparameter
{: #ca-deploy-global-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
| `Helm-Releasename`| Der Name Ihres Helm-Release. Er muss mit einem Kleinbuchstaben beginnen und mit einem alphanumerischen Zeichen enden, darf jedoch ausschließlich Bindestriche und alphanumerische Zeichen in Kleinschreibung enthalten. Bei jedem Versuch, eine Komponente zu installieren, müssen Sie einen eindeutigen Namen für das Helm-Release verwenden. | Nein | Ja |
| `Zielnamensbereich`| Wählen Sie den Kubernetes-Namensbereich für die Installation des Helm-Diagramms aus. | Nein | Ja |
|**Globale Konfiguration**| Diese Parameter gelten für alle Komponenten im Helm-Diagramm.|||
| `Name des Servicekontos`| Geben Sie den Namen des [Servicekontos ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Configure Service Accounts for Pods") ein, das Sie für die Ausführung des Pods verwenden wollen. | Standardwert | Nein |

#### Konfigurationsparameter für Zertifizierungsstellen
{: #ca-deploy-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
| `CA installieren`| Wählen Sie diese Option aus, um eine Zertifizierungsstelle zu installieren. | Nicht ausgewählt | Ja, falls Sie eine Zertifizierungsstelle bereitstellen wollen. |
| `CA-Name`| Geben Sie einen Namen an, der für die Zertifizierungsstelle verwendet werden soll. **Wichtig:** Notieren Sie sich diesen Wert. Sie benötigen ihn später beim Konfigurieren eines Anordnungsknotens oder Peers. | SampleOrgCA | Ja |
| `CA-Workerknotenarchitektur`| Wählen Sie Ihre {{site.data.keyword.cloud_notm}} Private Workerknotenarchitektur (ADM64 oder S390X) aus. | AMD64 | Ja|
| `CA-Datenbanktyp`| Der Typ der Datenbank, in der die Daten der Zertifizierungsstelle gespeichert werden. Es wird ausschließlich SQLite unterstützt. | SQLite | Ja |
| `CA-Datenpersistenz aktiviert` | Wenn diese Option aktiviert ist, sind die Daten beim Neustart des Containers verfügbar. Andernfalls gehen alle Daten bei einem Failover oder einem Podneustart verloren. | Aktiviert | Nein |
| `Dynamische Bereitstellung für CA verwenden` | Wählen Sie diese Option aus, um die dynamische Bereitstellung für Speicherdatenträger zu aktivieren. | Aktiviert | Nein |
| `CA-Speicherklassenname`| Geben Sie einen eindeutigen Speicherklassennamen an. Andernfalls wird die Standardspeicherklasse im Cluster verwendet. | Nein | Ist von der Konfiguration des {{site.data.keyword.cloud_notm}} Private-Clusters abhängig. Erfragen Sie dies bei Ihrem Clusteradministrator. |
| `Vorhandener Volume Claim für CA`| Geben Sie den Namen eines vorhandenen Volume Claims an und lassen Sie alle anderen Felder leer. | Nein | Nein |
| `CA-Selektorkennsatz`| [Selektorkennsatz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") für Ihren PVC. | Nein | Nein |
| `CA-Selektorwert`| [Selektorwert ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") für Ihren PVC. | Nein | Nein |
| `CA-Speicherzugriffsmodus`| Angeben des [Zugriffsmodus ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") des Speichers für den PVC. | ReadWriteMany | Ja |
| `Volume Claim-Größe für CA`| Wählen Sie die Größe der zu verwendenden Platte aus. | 2Gi | Ja |
| `CA-Image-Repository`| Die Position des Helm-Diagramms für die Zertifizierungsstelle. | ibmcom/ibp-fabric-ca | Ja |
| `Tag für CA-Docker-Image`| Der Wert des Tags, das dem Image der Zertifizierungsstelle zugeordnet ist. Dieses Feld wird automatisch mit der Imageversion gefüllt. Ändern Sie den Wert nicht.| 1.2.1 | Ja |
| `CA-Servicetyp` | Hiermit wird angegeben, ob auf dem Peer [externe Ports zugänglich gemacht werden sollen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types). Wählen Sie "NodePort" aus, damit die Ports extern zugänglich gemacht werden (empfohlen); wählen Sie "ClusterIP" aus, damit die Ports nicht zugänglich gemacht werden. Die Optionen "LoadBalancer" und "ExternalName" werden in diesem Release nicht unterstützt. | NodePort | Ja |
| `Geheimer Schlüssel für CA (erforderlich)`| Geben Sie den Namen des Kubernetes-Objekts für den geheimen Schlüssel ein, das Sie für `ca-admin-name` und `ca-admin-password` erstellt haben. | Nein | Ja |
| `CA-CPU-Anforderung`| Geben Sie die Mindestanzahl der CPUs an, die der Zertifizierungsstelle zugeordnet werden sollen. | 1 | Ja |
| `CA-CPU-Limit`| Geben Sie die maximale Anzahl der CPUs an, die der Zertifizierungsstelle zugeordnet werden sollen. | 2 | Ja |
| `CA-Speicheranforderung`| Geben Sie den Mindestumfang des Speichers an, der der Zertifizierungsstelle zugeordnet werden soll. | 1Gi | Ja |
| `CA-Speicherbegrenzung`| Geben Sie den Höchstumfang des Speichers an, der der Zertifizierungsstelle zugeordnet werden soll. | 4Gi | Ja |
| `Name der CA-TLS-Instanz`| Geben Sie den Namen der CA-TLS-Instanz an, die zum Eintragen eines Anordnungsknotens oder Peers verwendet wird. | tlsca | Ja |
| `Allgemeiner CSR-Name`| Geben Sie den allgemeinen Namen an, den das generierte CA-Stammzertifikat bei der Kontaktaufnahme angibt. | tlsca-common | Ja |
| `Proxy-IP`| Geben Sie die [IP-Adresse des Proxy-Knotens für den Cluster ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install_proxy.html "IBM Cloud Private-Installation hinter einem HTTP-Proxy") an, auf dem die Zertifizierungsstelle bereitgestellt wird. | 127.0.0.1 | Ja |


### Helm-Release mit der Helm-Befehlszeile installieren
{: #ca-deploy-helm-cli}

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
--values ca-s390x-values.yaml \
--tls
```

Sie können eine neue Datei `yaml` erstellen, indem Sie die Datei `values.yaml` bearbeiten, die in der heruntergeladenen Archivdatei enthalten ist. Die Datei `values.yaml` enthält alle erforderlichen Parameter mit den entsprechenden Standardeinstellungen.

## Installation der Zertifizierungsstelle prüfen
{: #verifiying-ca-installation}

Nachdem Sie die Konfigurationsparameter ausgefüllt und auf die Schaltfläche **Installieren** geklickt haben, können Sie die Bereitstellung prüfen, indem Sie auf die Schaltfläche **Helm-Release anzeigen** klicken. Wenn die Bereitstellung erfolgreich durchgeführt wurde, sollte der Wert 1 in den Feldern `GEWÜNSCHT`, `DERZEIT`, `AKTUELL` und `VERFÜGBAR` der Tabelle "Bereitstellung" angezeigt werden. Möglicherweise müssen Sie zur Aktualisierung klicken und warten, bis die Tabelle aktualisiert angezeigt wird. Die Tabelle "Bereitstellung" können Sie außerdem aufrufen, indem Sie in der linken oberen Ecke der {{site.data.keyword.cloud_notm}} Private-Konsole auf das **Menüsymbol** klicken. Klicken Sie in der Menüliste auf **Workloads** und dann auf **Helm-Releases**.

Wenn Sie bis zum Abschnitt `Hinweise` vorblättern, finden Sie wichtige Informationen, die Sie zum [Betrieb der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_operate.html#ca-operate) verwenden.

Nachdem Sie die Zertifizierungsstelle für {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private installiert haben, wird eine Konfigurationsübersicht mit Standardeinstellungen für Umgebungsvariablen erstellt. Danach können Sie Umgebungsvariablen für den Server der Zertifizierungsstelle ändern oder hinzufügen, um sein Verhalten anzupassen. Weitere Informationen zu den Konfigurationsparametern für den Server der Zertifizierungsstelle enthält die [Dokumentation zum Fabric-CA-Server ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-server "Fabric CA Server").

Nachdem Sie die Konfigurationsübersicht konfiguriert haben, müssen Sie den Server der Zertifizierungsstelle erneut starten, damit die Änderungen wirksam werden. Zum Neustart des Servers der Zertifizierungsstelle können Sie den Pod für den Fabric-CA-Server löschen. {{site.data.keyword.cloud_notm}} Private erstellt einen neuen Pod, der die Änderungen abbildet.

## Protokolle der Zertifizierungsstelle anzeigen
{: #ca-deploy-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [`Befehlen der CLI "kubectl"`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster enthalten ist. Weitere Informationen enthalten die [Anweisungen für den Zugriff auf die Protokolle](/docs/services/blockchain/howto/CA_operate.html#ca-operate-view-logs).

## Zertifizierungsstelle betreiben
{: #ca-deploy-operate}

Ihre Zertifizierungsstelle wird als Stammverzeichnis für die vertrauenswürdigen Elemente Ihrer Organisation verwendet. Sie müssen Ihre Zertifizierungsstelle verwenden, um Zertifikate für Ihre anderen Komponenten zu generieren.  Infolgedessen müssen Sie vor der Bereitstellung eines Anordnungsknotens oder Peers Ihre [Zertifizierungsstelle einrichten und mehrere operative Schritte ausführen](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

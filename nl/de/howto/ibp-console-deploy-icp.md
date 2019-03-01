---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform-Konsole in {{site.data.keyword.cloud_notm}} Private bereitstellen
{: #ibp-console-deploy-icp}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

In den folgenden Anweisungen wird die Vorgehensweise zur Bereitstellung einer {{site.data.keyword.blockchainfull}} Platform-Konsole in der eigenen Infrastruktur über {{site.data.keyword.cloud_notm}} Private beschrieben. Sie müssen die Konsole nicht in derselben Umgebung wie Ihre anderen Blockchain-Komponenten bereitstellen.
{:shortdesc}

Sie verwenden ein Helm-Diagramm, um die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole auf Ihrem {{site.data.keyword.cloud_notm}} Private-Server bereitzustellen. Jede der Bereitstellungen installiert eine einzelne Instanz der Konsole im {{site.data.keyword.cloud_notm}} Private-Namensbereich.

## Wichtige Hinweise
{: #ibp-console-deploy-icp-considerations}

Stellen Sie vor der Implementierung der Konsole sicher, dass Sie die folgenden Aspekte verstehen:

- Die Blockchain-Komponenten müssen bereits in {{site.data.keyword.cloud_notm}} Private oder Amazon Web Services (AWS) bereitgestellt worden sein. Ist dies noch nicht geschehen, finden Sie entsprechende Informationen im Abschnitt '[Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private]' oder '[Informationen zu IBM Blockchain Platform for AWS]'.
- Sie sind für das Management der Statusüberwachung, der Sicherheits- und Protokollierungsfunktionen sowie der Ressourcennutzung der Konsole verantwortlich.
- Sie können die Konsole nur mit Blockchain-Komponenten verbinden, die die Version 1.2.1 von Hyperledger Fabric aufweisen.

## Erforderliche Ressourcen
{: #ibp-console-deploy-icp-resources-required}

Stellen Sie sicher, dass Ihr {{site.data.keyword.cloud_notm}} Private-System die Mindestvoraussetzungen für Hardwareressourcenressourcen erfüllt:

| Komponente | vCPU | RAM | Platte für Datenspeicherung |
|-----------|------|-----|-----------------------|
| IBP-Konsole | 1 | 192 MB | 1 GB |
| CouchDB for IBP-Konsole | 1 | 1 GB | 1 GB |

 **Hinweise:**
 - Eine vCPU (virtuelle CPU) ist ein virtueller Kern, der einer virtuellen Maschine oder einem physischen Prozessorkern zugeordnet wird, wenn der Server nicht für virtuelle Maschinen partitioniert ist. Bei der Festlegung des virtuellen Prozessorkerns (VPC) für Ihre Bereitstellung in {{site.data.keyword.cloud_notm}} Private müssen Sie bestimmte vCPU-Voraussetzungen berücksichtigen. VPC ist eine Maßeinheit, mit der die Lizenzgebühren für IBM Produkte bestimmt werden. Weitere Informationen zu Szenarios für die Festlegung des VPC finden Sie unter [Virtueller Prozessorkern (VPC) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Diese Mindestressourcenwerte sind für Tests und Versuchsreihen ausreichend. Für eine Umgebung mit einer größeren Menge von Transaktionen muss unbedingt eine ausreichend große Speicherkapazität zugeordnet sein, beispielsweise 250 GB für die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole. Die zu verwendende Speicherkapazität richtet sich nach der Anzahl der Transaktionen und der Anzahl der Signaturen, die in Ihrem Netz benötigt werden. Falls eine Überlastung des Speichers auf Ihrem Peer oder Anordnungsknoten droht, müssen Sie eine neue Konsole mit einem größeren Dateisystem bereitstellen und die Netzkomponenten entsprechend erneut importieren.

## Speicher
{: #ibp-console-deploy-icp-resources-required-storage}

Sie müssen den Speicher ermitteln, der von Ihren Komponenten verwendet wird. Falls Sie die Standardeinstellungen verwenden, erstellt das Helm-Diagramm eine neue Anforderung für einen persistenten Datenträger mit 8 Gi namens `console-pvc` für Ihre Konsolendaten und eine weitere Anforderung für einen persistenten Datenträger mit 8 Gi namens `couchdb-pvc` für die CouchDB-Instanz.

Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der {{site.data.keyword.cloud_notm}} Private-Installation eine *neue* Speicherklasse `(storageClass)` eingerichtet wurde. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse erstellen, bevor Sie die Bereitstellung vornehmen können.

Für die Bereitstellung der Konsole stehen die Plattformen AMD64 oder S390X zur Auswahl. Beachten Sie jedoch, dass die [dynamische Bereitstellung](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) in {{site.data.keyword.cloud_notm}} Private nur für AMD64-Knoten verfügbar ist. Wenn in Ihrem Cluster eine Kombination aus S390X- und AMD64-Workerknoten eingesetzt wird, kann die dynamische Bereitstellung nicht verwendet werden.

Wenn Sie nicht mit der dynamischen Bereitstellung arbeiten, müssen [persistente Datenträger ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) erstellt und mit Bezeichnungen eingerichtet werden, die zum Optimieren des PVC-Bindungsprozesses von Kubernetes verwendet werden können.

## Voraussetzungen
{: #ibp-console-icp-prereq}

Stellen Sie vor dem Bereitstellen der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole in {{site.data.keyword.cloud_notm}} Private sicher, dass Sie die Schritte in den Lernprogrammen zum [Konfigurieren von {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) durchführen.

Sie müssen zudem die folgenden Abhängigkeiten installieren oder konfigurieren, bevor Sie die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole bereitstellen.

- [Registrierung beim Service 'App ID' von IBM](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Installation von Docker](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### Registrierung beim Service 'App ID'
{: #ibp-console-icp-prereq-app-id}

[App ID ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/docs/services/appid/index.html#gettingstarted "App ID") ist ein von {{site.data.keyword.cloud_notm}} bereitgestellter Benutzermanagement-Service. Die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole verwendet diesen Service, um die Benutzer des Dashboards zu verwalten und das Blockchain-Netz zu betreiben. Auf diese Weise können Benutzer der Benutzerschnittstelle Berechtigungsnachweise verwenden, die von Ihrer eigenen Organisation oder von Dritten wie Google oder Facebook ausgegeben werden, ohne dass sie IBM IDs haben müssen. Nur der Benutzer, der die Konsole implementiert, benötigt eine {{site.data.keyword.IBM_notm}} ID, um sich beim Service zu registrieren.

Bevor Sie die Konsole implementieren, müssen Sie eine IBM ID abrufen, um eine einmalige Registrierung beim Cloudverzeichnis von App ID durchzuführen. Anschließend müssen Sie die Berechtigungsnachweise für den Service 'App ID' abrufen, die an die Konsole übergeben werden sollen. Führen Sie die folgenden Schritte aus, um sich beim Service zu registrieren und die Berechtigungsnachweise für den Service abzurufen.

1. Wenn Sie noch keine ID haben, müssen Sie eine [{{site.data.keyword.IBM_notm}} ID ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid) erstellen.

2. Verwenden Sie Ihre {{site.data.keyword.IBM_notm}} ID, um sich anzumelden oder für [{{site.data.keyword.cloud_notm}} ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID") zu registrieren. Wechseln Sie dann zum Service [App ID ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID") im Katalog. Wählen Sie abhängig von Ihren Serviceanforderungen den `Lite`-Plan oder die `gestaffelte Preisstruktur` aus. Klicken Sie anschließend auf die Schaltfläche **Erstellen**, um den Begrüßungsbildschirm zu starten.

3. Klicken Sie in der Begrüßungsanzeige auf den Link **Verwalten** im linken Navigationsfenster, um die bevorzugten Identitätsprovider anzuzeigen oder zu aktualisieren. Dies können Drittanbieter wie Google oder Ihr eigener Identitätsservice sein.

4. Klicken Sie in der Tabelle auf die Registerkarte **Einstellungen**, um eine Umleitungs-URL zu konfigurieren. Dies ist die URL, unter der Sie die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole bereitstellen und auf diese zugreifen. Um den lokalen Host ('localhost') zu verwenden, geben Sie `http://localhost/auth/cb` an und klicken Sie auf das `+`-Zeichen.

5. Fügen Sie Ihre E-Mail-Adresse dem Service hinzu, damit Sie sich anmelden können, wenn Sie die Konsole erstmalig konfigurieren. Klicken Sie auf den Link **Benutzer** im linken Navigationsfenster und anschließend auf die Schaltfläche **Benutzer hinzufügen**. Geben Sie Ihre E-Mail-Informationen ein und klicken Sie auf **Speichern**.

6. Klicken Sie im linken Navigationsfenster auf den Link **Serviceberechtigungsnachweise**. Wenn Sie den Service zum ersten Mal verwenden, müssen Sie Berechtigungsnachweise generieren, indem Sie auf **Neuer Berechtigungsnachweis** klicken. Klicken Sie auf **Berechtigungsnachweise anzeigen**, um die Berechtigungsnachweise zu kopieren, die für den Zugriff auf den Service erforderlich sind.  Wenn die Details zum Erstellen neuer Berechtigungsnachweise angezeigt werden, wählen Sie die Option `Leseberechtigter` und eine Service-ID aus, wenn Sie der Benutzerschnittstelle einen bestimmten Namen zuordnen möchten. Die Berechtigungsnachweise für App ID ähneln den nachfolgenden JSON-Werten:
  ![Berechtigungsnachweise für Service](../images/service_credentials.gif "Berechtigungsnachweise für Service")

7. Klicken Sie auf das Symbol 'Kopieren' und speichern Sie Ihre Berechtigungsnachweise. Sie benötigen die Berechtigungsnachweise beim Konfigurieren der Konsole.

### Docker installieren
{: #ibp-console-icp-prereq-docker}

Installieren Sie [Docker ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.docker.com/get-started) Version 17.06.2-ce oder höher in der Umgebung, in der Sie die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole implementieren möchten. Sie können die Version von Docker überprüfen, die Sie mit dem folgenden Befehl über ein Terminalfenster installiert haben:

```
docker --version
```
{:codeblock}

**Hinweis:** Wenn Sie Docker für Mac oder Windows oder Docker Toolbox installieren, wird auch Docker Compose installiert. Wenn Sie Docker bereits installiert haben, sollten Sie prüfen, ob Docker Compose Version 1.14.0 oder höher installiert ist. Wenn dies nicht der Fall ist, wird empfohlen, eine aktuellere Version von Docker zu installieren. Sie können die Version von Docker Compose überprüfen, die Sie mit dem folgenden Befehl über ein Terminalfenster installiert haben:

```
docker-compose --version
```
{:codeblock}


## Schritt 1: Paket für die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole von {{site.data.keyword.IBM_notm}} Passport Advantage herunterladen
{: #ibp-console-icp-download-icp}

Wenden Sie sich an Ihren {{site.data.keyword.IBM_notm}} Vertriebsmitarbeiter, um das Paket für die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole von [Passport Advantage ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online für Kunden") herunterzuladen. Sie haben keinen IBM Vertriebsmitarbeiter? Registrieren Sie sich unter diesem <link> und besorgen Sie sich einen ...

## Schritt 2: Helm-Diagramme der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole in {{site.data.keyword.cloud_notm}} Private importieren
{: #ibp-console-icp-import-helmchart}

Entsprechende Referenzinformationen finden Sie im Abschnitt [{{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private installieren](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

## Schritt 3: {{site.data.keyword.blockchainfull_notm}} Platform-Konsole in {{site.data.keyword.cloud_notm}} Private bereitstellen
{: #ibp-console-deploy-helmchart-icp}

Nachdem Sie das Helm-Diagramm für die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole importiert haben, können Sie die folgenden Schritte ausführen, um die Konsole zu konfigurieren und zu installieren.

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf den Link **Katalog** in der oberen rechten Ecke.
2. Klicken Sie im linken Navigationsfenster auf `Blockchain` und suchen Sie nach der Kachel namens `ibm-blockchain-platform-ui`. Klicken Sie auf die Kachel, um sie zu öffnen. Daraufhin wird eine Readme-Datei mit Informationen zur Installation und Konfiguration des Helm-Diagramms angezeigt.
3. Klicken Sie oben in der Anzeige auf die Registerkarte **Konfiguration** oder klicken Sie in der rechten unteren Ecke der Anzeige auf die Schaltfläche **Konfigurieren**.
4. Nehmen Sie die gewünschten [Konfigurationseinstellungen](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters) vor.
5. Klicken Sie auf **Installieren**.

### Parameter der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole
{: #ibp-console-icp-parameters}

In der folgenden Tabelle sind die konfigurierbaren Parameter der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole und die zugehörigen Standardwerte aufgelistet.

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
| `Helm-Releasename`| Der Name Ihres Helm-Release. Er muss mit einem Kleinbuchstaben beginnen und mit einem alphanumerischen Zeichen enden, darf jedoch ausschließlich Bindestriche und alphanumerische Zeichen in Kleinschreibung enthalten. Bei jedem Versuch, eine Konsole zu installieren, müssen Sie einen eindeutigen Namen für das Helm-Release verwenden. | Nein | Ja |
| `Zielnamensbereich`| Wählen Sie den Kubernetes-Namensbereich für die Installation des Helm-Diagramms aus. | Nein | Ja |
| `Image-Repository`| Die Position des Helm-Diagramms für die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole. | registry.ng.bluemix.net/op-tools/op-tools | Ja |
| `Image-Tag`| Der Wert des Tags, der dem Image der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole zugeordnet ist. Dieses Feld wird automatisch mit der Imageversion gefüllt. Ändern Sie den Wert nicht. | v0.0.34 | Ja |
| `Servicetyp` | Hiermit wird angegeben, ob auf dem Peer [externe Ports zugänglich gemacht werden sollen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types). Wählen Sie "NodePort" aus, damit die Ports extern zugänglich gemacht werden (empfohlen); wählen Sie "ClusterIP" aus, damit die Ports nicht zugänglich gemacht werden. Die Optionen "LoadBalancer" und "ExternalName" werden in diesem Release nicht unterstützt. | NodePort | Ja |
| `Konsolenzugriff` | Geben Sie den Hostnamen an, der für den Zugriff auf die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole verwendet werden soll. | Nein | Nein |
| `Configtxlator-Zugriff` | Geben Sie den Hostnamen an, den Sie für den Zugriff auf den 'configtxlator'-Service verwenden wollen. | Nein | Nein |
| `Persistenz aktiviert` | Wenn diese Option aktiviert ist, sind die Daten beim Neustart des Containers verfügbar. Andernfalls gehen alle Daten bei einem Failover oder einem Podneustart verloren. | Aktiviert | Nein |
| `Dynamische Bereitstellung verwenden` | Wählen Sie diese Option aus, um die dynamische Bereitstellung für Speicherdatenträger zu aktivieren. | Aktiviert | Nein |
| `Speicherklassenname`| Geben Sie einen eindeutigen Speicherklassennamen an. Andernfalls wird die Standardspeicherklasse im Cluster verwendet. | Nein | Ist von der Konfiguration des {{site.data.keyword.cloud_notm}} Private-Clusters abhängig. Erkundigen Sie sich bei Ihrem Clusteradministrator. |
| `Vorhandener Volume Claim`| Angeben des Namens eines vorhandenen Volume Claims. Alle anderen Felder leer lassen. | Nein | Nein |
| `Selektorkennsatz`| [Selektorkennsatz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") für Ihren PVC. | Nein | Nein |
| `Selektorwert`| [Selektorwert ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Labels and Selectors") für Ihren PVC. | Nein | Nein |
| `Speicherzugriffsmodus`| Geben Sie den [Zugriffsmodus](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") für den Speicher für PVC an. | ReadWriteMany | Ja |
| `Datenträgername`| Geben Sie den Namen des PVC an. | Nein | Ja |
| `CPU-Anforderung` | Mindestanzahl der CPUs, die der Konsole zugeordnet werden soll. | 250m | Ja |
| `CPU-Grenzwert` | Maximale Anzahl der CPUs, die der Konsole zugeordnet werden soll. | 500m | Ja |
| `Speicheranforderung` | Minimale Speicherkapazität, die der Konsole zugeordnet werden soll. | 256 Mi | Ja |
| `Speicherbegrenzung` | Maximale Speicherkapazität, die der Konsole zugeordnet werden soll. | 1Gi | Ja |

## Schritt 4: Proxy-IP konfigurieren
{: #ibp-console-icp-config-proxy-ip}

Klicken Sie nach Abschluss der Installation auf die Schaltfläche **Helm-Release anzeigen**, um die Seite für das Helm-Release in der Konsole zu öffnen. Sie können auch **Menü** > **Workloads** > **Helm-Releases** in {{site.data.keyword.cloud_notm}} Private auswählen und und auf Ihr Helm-Release klicken.

Suchen Sie auf der Seite für das Helm-Release der Konsole die Proxy-IP des {{site.data.keyword.cloud_notm}} Private-Clusters, z. B. `9.12.19.115`.

Fügen Sie auf der Clientmaschine, auf der Sie auf die Konsole zugreifen möchten, Einträge in `/etc/hosts` hinzu, um die Hostnamen, die Sie im vorherigen Schritt für optools- und configtxlator-Services eingegeben haben, zur Proxy-IP zuzuordnen. Beispiel:
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## Schritt 5: {{site.data.keyword.blockchainfull_notm}} Platform-Konsole konfigurieren
{: #ibp-console-icp-setup}

*Dieser Abschnitt würde von einer GIF-Animation profitieren.*

1. Rufen Sie in {{site.data.keyword.cloud_notm}} im linken Navigationsfenster **Identitätsprovider** > **Verwalten** auf und wählen Sie die Registerkarte **Authentifizierungseinstellungen** aus.
2. Geben Sie im Feld **Webumleitungs-URLs hinzufügen** die URL an, die Sie für den Zugriff auf die Konsole mit Ihrer App-ID verwenden möchten.
3. Greifen Sie in Ihrem Webbrowser mit dem Hostnamen auf die Konsole zu, den Sie in {{site.data.keyword.cloud_notm}} Private konfiguriert haben. Bei der ersten Anmeldung werden die Anzeigen für die Konsolenkonfiguration angezeigt.
  1. Geben Sie auf der Registerkarte **Authentifizierung** Ihre App-ID ein.
  2. Fügen Sie auf der Registerkarte **Konfiguration** die Berechtigungsnachweise für den Service 'App ID' ein, die Sie in [Schritt 1: Registrierung beim Service 'App ID'](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id) kopiert haben.
  3. Geben Sie auf der Registerkarte **Benutzer hinzufügen** eine Liste der E-Mail-Adressen der `Administratoren` und `allgemeinen Benutzer` an, die für die Verwendung der Konsole berechtigt sind. Die E-Mail-Domänen sind auf die Gruppe der Identitätsprovider beschränkt, z. B. "corporate", "Google" oder "Facebook", die ausgewählt wurden, als der Service 'App ID' in {{site.data.keyword.cloud_notm}} registriert wurde.
    - Die Rolle `Administrator` ist erforderlich, um neue Benutzer hinzufügen oder vorhandene Benutzer aus der Berechtigungsliste der Konsole entfernen zu können. **Tipp:** Wenn Sie die Person sein werden, die die Konsole verwaltet, denken Sie daran, Ihre eigene E-Mail-Adresse in die Liste aufzunehmen.
    - Mit der Rolle `Allgemein` können Benutzer die Komponenten in der Konsole **nur anzeigen**.

    Schließlich müssen Sie eine einzige `Kontakt-E-Mail für Administratoren` angeben, die als Kontakt-E-Mail-Adresse angezeigt wird, wenn ein nicht berechtigter Benutzer, der sich nicht in einer der oben genannten Listen befindet, versucht, auf die Konsole zuzugreifen. Diese E-Mail-Adresse muss kein Benutzer mit Administratorberechtigung in der Konsole sein. Beachten Sie, dass diese Informationen auch später auf der Registerkarte **Mitglieder** in der Konsole erstellt oder geändert werden können.
  4. Klicken Sie auf **Weiter mit Anmeldung**. Alle in den obigen Listen aufgeführten Benutzer können Sie nun mit ihren E-Mail-Adressen bei der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole anmelden.

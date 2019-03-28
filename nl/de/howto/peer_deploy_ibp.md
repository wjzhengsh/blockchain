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

# Peers in {{site.data.keyword.cloud_notm}} Private bereitstellen und mit Starter Plan oder Enterprise Plan verbinden
{: #ibp-peer-deploy}

In den folgenden Anweisungen wird beschrieben, wie ein {{site.data.keyword.blockchainfull}} Plattform-Peer auf dem {{site.data.keyword.cloud_notm}} privaten Server bereitgestellt wird und der Peer zum Starter Plan- oder Enterprise Plan-Netz auf {{site.data.keyword.cloud_notm}} oder auf der lokalen {{site.data.keyword.cloud_notm}} Private-Instanz verbunden wird.
{:shortdesc}

Lesen Sie vor der Bereitstellung eines Peers den Abschnitt mit [Hinweisen und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

Im Starter Plan- oder Enterprise Plan-Netz muss Hyperledger Fabric Version 1.1 oder 1.2.1 ausgeführt werden. Ihre Version von Hyperledger Fabric können Sie ermitteln, indem Sie das [Fenster "Netzvorgaben"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) im Network Monitor öffnen.

## Erforderliche Ressourcen
{: #ibp-peer-deploy-resources-required}

Stellen Sie sicher, dass Ihr {{site.data.keyword.cloud_notm}} Private-System die Mindestvoraussetzungen für Hardwareressourcen erfüllt:

| Komponente | vCPU | RAM | Platte für Datenspeicherung |
|-----------|------|-----|-----------------------|
| Peer | 2 | 2 GB | 50 GB mit der Möglichkeit zur Erweiterung |
| CouchDB für Peer | 2| 2 GB |50 GB mit der Möglichkeit zur Erweiterung |

 **Hinweise:**
 - Eine vCPU (virtuelle CPU) ist ein virtueller Kern, der einer virtuellen Maschine oder einem physischen Prozessorkern zugeordnet wird, wenn der Server nicht für virtuelle Maschinen partitioniert ist. Bei der Festlegung des virtuellen Prozessorkerns (VPC) für Ihre Bereitstellung in {{site.data.keyword.cloud_notm}} Private müssen Sie bestimmte vCPU-Voraussetzungen berücksichtigen. VPC ist eine Maßeinheit, mit der die Lizenzgebühren für IBM Produkte bestimmt werden. Weitere Informationen zu Szenarios für die Festlegung des VPC finden Sie unter [Virtueller Prozessorkern (VPC) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Diese Mindestressourcenwerte sind für Tests und Versuchsreihen ausreichend. Für eine Umgebung mit einer größeren Menge von Transaktionen muss unbedingt eine ausreichend große Speicherkapazität zugeordnet sein, beispielsweise 250 GB für Ihren Peer. Die zu verwendende Speicherkapazität richtet sich nach der Anzahl der Transaktionen und der Anzahl der Signaturen, die in Ihrem Netz benötigt werden. Falls eine Überlastung des Speichers auf Ihrem Peer droht, müssen Sie einen neuen Peer mit einem größeren Dateisystem bereitstellen und über Ihre anderen Komponenten auf denselben Kanälen synchronisieren lassen.

## Speicher
{: #ibp-peer-deploy-storage}

Sie müssen den Speicher ermitteln, der von Ihrem Peer benutzt werden soll. Falls Sie die Standardeinstellungen verwenden, erstellt das Helm-Diagramm eine neue Anforderung für einen persistenten Datenträger mit 8 Gi namens `my-data-pvc` für Ihre Peerdaten und einen weitere Anforderung für einen persistenten Datenträger mit 8 Gi namens `statedb-pvc` für Ihre Statusdatenbank.

Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der {{site.data.keyword.cloud_notm}} Private-Installation eine *neue* Speicherklasse `(storageClass)` eingerichtet wird. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse erstellen, bevor Sie die Bereitstellung vornehmen können.

Für die Bereitstellung des Peers stehen die Plattformen amd64 oder s390x zur Auswahl. Es ist jedoch zu beachten, dass die [dynamische Bereitstellung ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamische Bereitstellung") nur für amd64-Knoten in {{site.data.keyword.cloud_notm}} Private verfügbar ist. Wenn in Ihrem Cluster eine Kombination aus s390x- und amd64-Workerknoten eingesetzt wird, kann die dynamische Bereitstellung nicht verwendet werden.

Wenn Sie nicht mit der dynamischen Bereitstellung arbeiten, müssen [persistente Datenträger![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistente Datenträger") erstellt und mit Bezeichnungen eingerichtet werden, die zum Optimieren des PVC-Bindungsprozesses (PVC = Persistent Volume Claim) von Kubernetes verwendet werden können.

## Voraussetzungen für die Bereitstellung eines Peers
{: #ibp-peer-deploy-prerequisites}

1. Bevor Sie in {{site.data.keyword.cloud_notm}} Private einen Peer installieren können, müssen Sie [{{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) installieren und [das {{site.data.keyword.blockchainfull_notm}} Platform Helm-Diagramm installieren](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Falls Sie Community Edition verwenden und dieses Helm-Diagramm in einem {{site.data.keyword.cloud_notm}} Private-Cluster ohne Internetkonnektivität ausführen wollen, müssen Sie auf einer mit dem Internet verbundenen Maschine Archive erstellen, bevor Sie die Archive im {{site.data.keyword.cloud_notm}} Private-Cluster installieren können. Weitere Informationen finden Sie unter [Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen"){:new_window}. Die Spezifikationsdatei "manifest.yaml" ist im Helm-Diagramm unter "ibm-blockchain-platform-dev/ibm_cloud_pak" zu finden.

3. Sie benötigen eine Organisation, die Mitglied eines Starter Plan- oder Enterprise Plan-Netzes in {{site.data.keyword.cloud_notm}} ist. Der Peer nutzt die API-Endpunkte, Hyperledger Fabric-CAs und den Anordnungsservice des {{site.data.keyword.blockchainfull_notm}} Platform-Netzes zum Betrieb. Wenn Sie nicht Mitglied eines Blockchain-Netzes sind, müssen Sie ein Netz erstellen oder einem Netz beitreten. Weitere Informationen hierzu finden Sie im Abschnitt [Netz erstellen](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) oder [Am Netz teilnehmen](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

4. Zunächst müssen Sie in {{site.data.keyword.cloud_notm}} Private eine [Zertifizierungsstelle bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Diese Zertifizierungsstelle wird als TLS-Zertifizierungsstelle verwendet. Führen Sie die [vorausgesetzten Schritte](/docs/services/blockchain/howto/CA_operate.html#ca-operate-prerequisites) für den Betrieb einer Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private aus, bevor Sie den Peer bereitstellen. Die übrigen Schritte im entsprechenden Abschnitt müssen Sie nicht ausführen.

5. Rufen Sie den Wert für die Proxy-IP-Adresse des Clusters Ihrer TLS-Zertifizierungsstelle in der {{site.data.keyword.cloud_notm}} Private-Konsole ab. **Hinweis:** Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um auf Ihre Proxy-IP-Adresse zugreifen zu können. Melden Sie sich beim {{site.data.keyword.cloud_notm}} Private-Cluster an. Klicken Sie im linken Navigationsfenster auf **Plattform** und anschließend auf **Knoten**, um die im Cluster definierten Knoten anzuzeigen. Klicken Sie auf den Knoten mit der Rolle `Proxy` und kopieren Sie den Wert, der in der Tabelle für die `Host-IP` angegeben ist. **Wichtig:** Speichern Sie diesen Wert. Sie benötigen ihn, wenn Sie das Feld für die `Proxy-IP-Adresse` des Helm-Diagramms konfigurieren.

6. Erstellen Sie eine Konfigurationsdatei für den Peer und speichern Sie sie als geheimen Kubernetes-Schlüssel in {{site.data.keyword.cloud_notm}} Private. Die Schritte zum Erstellen dieser Datei sind im [nächsten Abschnitt](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file) beschrieben.

## Konfigurationsdatei erstellen
{: #ibp-peer-deploy-config-file}

Damit Sie einen Peer bereitstellen können, müssen Sie eine JSON-Konfigurationsdatei erstellen, die wichtige Informationen zur Identität des Peers und zur Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} enthält. Anschließend müssen Sie diese Datei während der Konfiguration mithilfe eines [Kubernetes-Objekts für einen geheimen Schlüssel ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/configuration/secret/ "Geheimer Schlüssel"). Diese Datei ermöglicht es dem Peer, von der Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} die Zertifikate abzrufen, die für den Beitritt zu einem Starter Plan- oder Enterprise Plan-Netz benötigt werden. Außerdem enthält diese Datei ein Administratorzertifikat, mit dem Sie Ihren Peer als Benutzer mit Administratorberechtigung betreiben können.

Die vorliegenden Anweisungen bieten eine JSON-Vorlage, die Sie bearbeiten und in Ihrem lokalen Dateisystem speichern können. Anschließend werden Sie durch die Vervollständigung der Konfigurationsdatei unter Verwendung der Zertifizierungsstelle geführt.

### Konfigurationsdatei

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

Kopieren Sie diese gesamte Datei in einen Texteditor, in dem Sie sie bearbeiten und als JSON-Datei in Ihrem lokalen Dateisystem speichern können. Sie müssen nur die ersten beiden Abschnitte dieser Datei namens `"enrollment"` und `"tls"` ausfüllen.

Zum Vervollständigen der Konfigurationsdatei müssen Sie mehrere Schritte in der Network Monitor-Instanz des Starter Plan- oder Enterprise Plan-Netzes ausführen.

1. [Sie müssen die Endpunktinformationen für die Zertifizierungsstelle Ihres Starter Plan- oder Enterprise Plan-Netzes abrufen](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-ibp-ca-endpoint).
2. [Sie müssen den Peer bei der Zertifizierungsstelle registrieren](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-peer).
3. [Sie müssen einen Peeradministrator registrieren](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin), der zum Betrieb des Peers verwendet wird. Falls Sie bereits einen Administrator für die Bereitstellung eines anderen Peers registriert haben, müssen Sie diesen Schritt nicht ausführen.

Außerdem müssen Sie einige Schritte mit dem Fabric-CA-Client und Ihrer in {{site.data.keyword.cloud_notm}} Private bereitgestellten TLS-Zertifizierungsstelle ausführen.

1. Sie müssen mit dem Fabric-CA-Client den [MSP-Ordner des Peeradministrators generieren](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin).
2. [Sie müssen die Endpunktinformationen für Ihre TLS-Zertifizierungsstelle abrufen](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-ca-endpoint).
3. Sie müssen mit dem Fabric-CA-Client den [Peer bei Ihrer TLS-Zertifizierungsstelle registrieren](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-register-peer).


### Informationen zur Zertifizierungsstelle für Starter Plan oder Enterprise Plan
{: #ibp-peer-deploy-ibp-ca-endpoint}

Zunächst müssen Sie für die Konfigurationsdatei die Verbindungsinformationen Ihrer Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} angeben. Melden Sie sich bei der Network Monitor-Benutzerschnittstelle für den Starter Plan oder Enterprise Plan an. Klicken Sie in der Anzeige **Übersicht** Ihres Network Monitor auf die Schaltfläche zur **Konfiguration ferner Peers**. Dadurch wird ein Dialogfenster geöffnet, in dem die erforderlichen Informationen zu Ihrer Zertifizierungsstelle enthalten sind.

![Konfiguration ferner Peers](../images/myresources_starter.png "Konfiguration ferner Peers")
*Abbildung 1. Anzeige für die Konfiguration ferner Peers*

Das Popup-Fenster enthält die folgenden Felder:

  - **MSP der Organisation**
  - **Name der Zertifizierungsstelle (CA)**
  - **URL der Zertifizierungsstelle (CA)**
  - **TLS-Zertifikat der Zertifizierungsstelle (CA)**

Geben Sie in der Datei unter dem Abschnitt `"components"` die folgenden Werte aus den obigen Feldern an:
- `"caname"` ist der Wert des Feldes **Name der Zertifizierungsstelle (CA)**.
- `"cahost"` ist der Hostname aus der URL der Zertifizierungsstelle. Lautet die URL für die Zertifizierungsstelle beispielsweise `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, müssten Sie für `"cahost"` den Wert `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com` angeben.
- `"caport"` ist der Port aus dem Wert von `"cahost"`. Lautet die URL für die Zertifizierungsstelle beispielsweise `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, müssten Sie für `"caport"` den Wert `31011` angeben.
- `"cacert"` ist der Wert des Feldes **TLS-Zertifikat der Zertifizierungsstelle (CA)**. Bevor Sie das Zertifikat in der Datei einfügen können, müssen Sie es im Base64-Format codieren, indem Sie die folgenden Befehle ausführen; ersetzen Sie hierbei die Zeichenfolge `<paste in Certificate Authority (CA) TLS Certificate>` durch den Wert, den Sie aus Network Monitor kopiert haben.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
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

Fügen Sie die resultierende Zeichenfolge im Feld `"cacert"` unter dem Abschnitt `"catls"` in der Datei ein. Nach der Aktualisierung sieht das Feld `"cacert"` ungefähr wie im folgenden Beispiel aus:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Peer registrieren
{: #ibp-peer-deploy-register-peer}

Damit Ihre Peers an Kanälen teilnehmen sowie Chaincode installieren und instanziieren können, müssen Sie einen Peer zuerst bei Ihrer Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} registrieren. Ihre Peerbereitstellung kann anschließend Zertifikate generieren, die der Peer zur Teilnahme an einem Starter Plan- oder Enterprise Plan-Netz benötigt. Führen Sie die folgenden Schritte aus, um einen Peer mit einer `Eintragungs-ID` und einem `geheimen Eintragungsschlüssel` zu registrieren. Diese beiden Werte werden in die Konfigurationsdatei eingefügt.

1. Melden Sie sich bei {{site.data.keyword.blockchainfull_notm}} Platform an und greifen Sie auf den Network Monitor zu. In der Anzeige "Zertifizierungsstelle" können Sie alle Identitäten anzeigen, die für Ihre Organisation registriert wurden. Hierzu gehören der Administrator und die Clientanwendungen.
  ![CA-Anzeige](../images/CA_screen_starter.png "CA-Anzeige")
  *Abbildung 2. CA-Anzeige*

2. Klicken Sie in der Anzeige auf die Schaltfläche **Benutzer hinzufügen**. Daraufhin wird ein Dialogfenster geöffnet, in dem Sie Ihren Peer registrieren können, nachdem Sie die folgenden Felder ausgefüllt haben. Speichern Sie die Werte für die ID und den geheimen Schlüssel, die Sie zum Konfigurieren Ihres Peers benötigen.
  - **Eintragungs-ID**: Der Benutzername Ihres Peers, der bei der Konfiguration des Peers als `Eintragungs-ID` bezeichnet wird. **Speichern Sie diesen Wert** für die Konfigurationsdatei.
  - **Geheimer Eintragungsschlüssel:** Das Kennwort Ihres Peers, das bei der Konfiguration des Peers als `geheimer Eintragungsschlüssel` bezeichnet wird. **Speichern Sie diesen Wert** für die Konfigurationsdatei.
  - **Typ:** Wählen Sie für dieses Feld den Wert `Peer` aus.
  - **Zugehörigkeit:** Dies ist die Zugehörigkeit in Ihrer Organisation (z. B. `org1`), zu der Ihr Peer gehören wird. Wählen Sie eine vorhandene Zugehörigkeit in der Dropdown-Liste aus oder geben Sie eine neue Zugehörigkeit ein.
  - **Maximale Anzahl der Eintragungen:** In diesem Feld können Sie die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit einer bestimmten Identität einschränken. Erfolgt hier keine Angabe, dann wird der Standardwert (Unbegrenzte Eintragungen) verwendet.

  Nachdem Sie die Felder ausgefüllt haben, klicken Sie auf **Abschicken**, um den Peer zu registrieren. Der registrierte Peer wird dann in der Tabelle als Identität in Ihrer Organisation aufgelistet. Stellen Sie als Sicherheitsmaßnahme mit jeder Identität und den zugehörigen Werten für "enrollID" und "secret" nur einen einzigen Peer bereit. Verwenden Sie IDs und Kennwörter von Peers nicht erneut.

3. Geben Sie in der Konfigurationsdatei unter dem Abschnitt `"components"` die folgenden Werte ein:
  - `"enrollid"` ist der Wert des Feldes `Eintragungs-ID` aus dem vorherigen Schritt.
  - `"enrollsecret"` ist der Wert des Feldes `Geheimer Eintragungsschlüssel` aus dem vorherigen Schritt.


### Administrator erstellen
{: #ibp-peer-deploy-register-admin}

Nachdem Sie die Peeridentität registriert haben, müssen Sie außerdem eine Administratoridentität erstellen, mit der Sie den Peer betreiben. Diese neue Identität müssen Sie zuerst bei Ihrer Zertifizierungsstelle registrieren; anschließend verwenden Sie sie, um einen MSP-Ordner zu generieren. Danach müssen Sie das signCert-Zertifikat der Benutzer mit Administratorberechtigung zur Konfigurationsdatei hinzufügen, wo es während der Bereitstellung zum Administratorzertifikat des Peers wird. Auf diese Weise können Sie die Zertifikate der Administratoridentität beim Betrieb Ihres Blockchain-Netzes verwenden, beispielsweise zum Starten eines neuen Kanals oder zum Installieren von Chaincode auf den Peers.

Für die zu Ihrer Organisation gehörenden Komponenten müssen Sie nur eine einzige Administratoridentität erstellen. Falls Sie mehrere Peers bereitstellen, müssen Sie diese Schritte nur ein einziges Mal ausführen. Sie können das signCert-Zertifikat, das Sie für einen Peer generiert haben, zum Bereitstellen aller anderen Peers verwenden, die zu Ihrer Organisation gehören.

1. Melden Sie sich bei {{site.data.keyword.blockchainfull_notm}} Platform an und greifen Sie auf den Network Monitor zu. In der Anzeige "Zertifizierungsstelle" können Sie alle Identitäten anzeigen, die für Ihre Organisation registriert wurden. Hierzu gehören der Administrator und die Clientanwendungen.
  ![CA-Anzeige](../images/CA_screen_starter.png "CA-Anzeige")
  *Abbildung 2. CA-Anzeige*

2. Klicken Sie in der Anzeige auf die Schaltfläche **Benutzer hinzufügen**. Daraufhin wird ein Dialogfenster geöffnet, in dem Sie Ihren Peer registrieren können, nachdem Sie die folgenden Felder ausgefüllt haben. Speichern Sie die Werte für die ID und den geheimen Schlüssel, die Sie zum Konfigurieren Ihres Peers benötigen.
  - **Eintragungs-ID**: Der Benutzername Ihres Peeradministrators, der bei der Konfiguration des Peers als `Eintragungs-ID` bezeichnet wird. **Speichern Sie diesen Wert.** Sie benötigen ihn, wenn Sie den MSP-Ordner des Administrators generieren.
  - **Geheimer Eintragungsschlüssel:** Das Kennwort Ihres Peeradministrators, das bei der Konfiguration des Peers als `geheimer Eintragungsschlüssel` bezeichnet wird. **Speichern Sie diesen Wert.** Sie benötigen ihn, wenn Sie den MSP-Ordner des Administrators generieren.
  - **Typ:** Wählen Sie für dieses Feld den Wert `Peer` aus.
  - **Zugehörigkeit:** Dies ist die Zugehörigkeit in Ihrer Organisation (z. B. `org1`), zu der Ihr Peer gehören wird. Wählen Sie eine vorhandene Zugehörigkeit in der Dropdown-Liste aus oder geben Sie eine neue Zugehörigkeit ein.
  - **Maximale Anzahl der Eintragungen:** In diesem Feld können Sie die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit einer bestimmten Identität einschränken. Erfolgt hier keine Angabe, dann wird der Standardwert (Unbegrenzte Eintragungen) verwendet.

  Klicken Sie nach dem Ausfüllen dieser Felder auf **Senden**, um den Administrator zu erstellen. Der erstellte Administrator wird dann in der Tabelle als Identität in Ihrer Organisation aufgelistet.

### MSP-Ordner für Peeradministrator generieren
{: #ibp-peer-deploy-enroll-admin}

Nachdem Sie die Administratoridentität registriert haben, müssen Sie den MSP-Ordner und das signCert-Zertifikat des Administrators generieren. Daher müssen Sie einen Eintragungsbefehl (enroll) für die Zertifizierungsstelle Ihres Starter Plan oder Enterprise Plan ausführen.

1. Laden Sie den [Fabric-CA-Client](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) herunter, falls Sie dies noch nicht ausgeführt haben.
2. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihr Verschlüsselungsmaterial speichern wollen, und erstellen Sie den Ordner, in dem Sie den MSP-Ordner Ihres Peeradministrators speichern wollen.

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. Legen Sie den Pfad, in dem der Client Ihre Zertifikate erstellen kann, als Wert für `$FABRIC_CA_CLIENT_HOME` fest. Achten Sie darauf, das Konfigurationsmaterial zu entfernen, das möglicherweise bei früheren Versuchen erstellt wurde. Falls Sie den Befehl `enroll` zuvor noch nie ausgeführt haben, sind der Ordner `msp` und die Datei `.yaml` nicht vorhanden.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. Öffnen Sie die JSON-Datei mit dem **Verbindungsprofil** in der Anzeige "Übersicht" im Network Monitor und suchen Sie die folgenden Variablen:
  - URL für CA: `url` unter `certificateAuthorities`
  - CA-Name: `caName`

5. Laden Sie die TLS-Zertifikate von {{site.data.keyword.cloud_notm}} herunter, die dem verwendeten Serviceplan, Standort und Cluster entsprechen. Den Cluster können Sie anhand des Domänennamens in der URL Ihrer Zertifizierungsstelle ermitteln, z. B. `us01.blockchain.ibm.com:31011` oder `us02.blockchain.ibm.com:31011`.

  - TLS-Zertifikat für den Starter Plan
    - US: [us01.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
    [us03.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
    [us05.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
    [us07.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - UK: [uk01.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert");
    [uk03.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert");
  - [TLS-Zertifikat für den Enterprise Plan ![External link icon](../images/external_link.svg "External link icon")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Speichern Sie den Inhalt in einem Verzeichnis, unter dem Sie in zukünftigen Befehlen darauf verweisen können.

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. Geben Sie den folgenden Befehl aus, um Zertifikate mit der Identität des Peeradministrators zu generieren:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  Die Angaben `<enroll_id>` und `<enroll_password>` im obigen Befehl stehen für die **ID** und den **geheimen Schlüssel** des Peeradministrators, der [mit Network Monitor registriert wurde](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin). Die Angaben `<ca_name>` und `<ca_url_with_port>` stehen für die Werte der Felder `caName` und `url` aus Ihrem Verbindungsprofil. Lassen Sie die Angabe `http://` am Beginn der URL für die Zertifizierungsstelle weg.

  Ein realer Aufruf könnte ähnlich wie im folgenden Beispiel aussehen:

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  Nach der erfolgreichen Ausführung dieses Befehls wird ein neuer MSP-Ordner in dem Verzeichnis generiert, das Sie mit `$FABRIC_CA_CLIENT_HOME` angegeben haben. Dieses Verzeichnis enthält die Zertifikate, die Sie zum Betrieb Ihres Peers verwenden.

7. Öffnen Sie im soeben erstellten MSP-Ordner die signCert-Datei des neuen Benutzers und konvertieren Sie sie in das Base64-Format. Die signCert-Datei befindet sich im Ordner `signcerts` des MSP-Verzeichnisses. Falls Sie anhand der Beispielschritte vorgehen, können Sie hierzu die folgenden Befehle verwenden:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
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

  Dieser Befehl gibt eine Zeichenfolge aus, die ähnlich wie das folgende Beispiel aussieht:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  Fügen Sie diese Zeichenfolge im Feld `"admincerts"` unter dem Abschnitt "component" in der Konfigurationsdatei ein.

### Informationen zur TLS-Zertifizierungsstelle
{: #ibp-peer-deploy-tls-ca-endpoint}

Für die Felder im Abschnitt `"tls"` der Konfigurationsdatei sind Informationen aus der Zertifizierungsstelle erforderlich, die Sie in {{site.data.keyword.cloud_notm}} Private bereitgestellt haben. Diese Zertifizierungsstelle verwenden Sie als separate TLS-Zertifizierungsstelle, was Ihren Peer stärker schützt. Anhand der folgenden Anweisungen können Sie die relevanten Informationen generieren:

- Die Werte für `"cahost"` und `"caport"` sind die URL und der Port aus der [URL der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url). Lautet beispielsweise die URL Ihrer Zertifizierungsstelle `http://9.30.94.174:30167`, wäre `9.30.94.174` der Wert für `cahost` und `30167` der Wert für `caport`.
- Der Wert für `"caname"` ist der Name der TLS-Zertifizierungsstelle, die Sie in {{site.data.keyword.cloud_notm}} Private bereitgestellt haben. Der Name der TLS-Zertifizierungsstelle ist der Wert, den Sie während der Konfiguration der Zertifizierungsstelle im Feld `Name der CA-TLS-Instanz` angegeben haben.
- Der Wert für `"cacert"` ist das in Base64 codierte TLS-Zertifikat Ihrer Zertifizierungsstelle. Aktualisieren Sie den folgenden Abschnitt mit dem Wert der Befehlsausgabe, wenn Sie das [TLS-Zertifikat der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) als Voraussetzung abrufen.

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

### Peer bei der TLS-Zertifizierungsstelle registrieren
{: #ibp-peer-deploy-tls-register-peer}

Sie müssen Ihren Peer mit dem Fabric-CA-Client bei der TLS-Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private registrieren.

1. Die TLS-Zertifikatsdatei `tls.pem` sollte jetzt im Ordner `$HOME/fabric-ca-client/catls` vorhanden sein. Wenn dies nicht der Fall ist, können Sie das TLS-Zertifikat, das Sie [aus {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) heruntergeladen haben, in ein Verzeichnis kopieren, in dem Sie in den nachfolgenden Befehlen darauf verweisen können. Stellen Sie sicher, dass Sie als Ausgangsposition das Verzeichnis `$HOME/fabric-ca-client` verwenden.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. Sie müssen unter Verwendung des Administrators für die TLS-Zertifizierungsstelle eine Eintragung vornehmen. Ändern Sie den Wert für `$FABRIC_CA_CLIENT_HOME` in ein Verzeichnis, in dem Sie Ihre Administratorzertifikate für die TLS-Zertifizierungsstelle speichern wollen.

  ```
  cd $HOME/fabric-ca-client
  mkdir tlsca-admin
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. Führen Sie die folgenden Befehle aus, um Zertifikate als Administrator der TLS-Zertifizierungsstelle zu generieren.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  Die Angaben `<enroll_id>` und `<enroll_password>` im Befehl stehen für [den Benutzernamen und das Kennwort des CA-Administrators](/docs/services/blockchain/CA_deploy.html#ca-deploy-admin-secret), die Sie beim Bereitstellen der Zertifizierungsstelle an den geheimen Kubernetes-Schlüssel übergeben haben. Fügen Sie die [URL der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) in `<ca_url_with_port>`. Lassen Sie die Angabe `http://` am Beginn weg. Den Wert für `<tls_ca_name>` haben Sie während der [Konfiguration der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms) angegeben.

  Die Angabe `<ca_tls_cert_file>` ist der Dateiname Ihres [TLS-Zertifikats der Zertifizierungsstelle](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) mit dem vollständigen Pfad.

  Ein echter Aufruf könnte wie im folgenden Beispiel aussehen:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Nach der Eintragung besitzen Sie die erforderlichen Zertifikate für die Registrierung des Peers bei der TLS-Zertifizierungsstelle.

4. Geben Sie den folgenden Befehl aus, um Ihre Zugehörigkeit und Ihren Organisationsnamen zu ermitteln.

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Ihr Befehl könnte wie im folgenden Beispiel aussehen:

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Daraufhin werden die folgenden Informationen angezeigt:

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  Notieren Sie sich den Wert Ihrer Organisation für **affiliation**, z. B. `org1.department1` für die Organisation `org1` im obigen Beispiel. Sie müssen diesen Wert im folgenden Befehl verwenden.

5. Führen Sie den folgenden Befehl aus, um den Peer zu registrieren.

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Im Feld `--caname` müssen Sie den Namen Ihrer TLS-Zertifizierungsstelle verwenden.  Erstellen Sie einen Namen und ein Kennwort für den Peer und ersetzen Sie `name` und `secret` durch diese Werte. **Wichtig:** Notieren Sie sich diese Informationen. Sie müssen die Werte für `name` und `secret` bei `"enrollid"` und `"enrollsecret"` im Abschnitt `"tls"` der Konfigurationsdatei eingeben.

  Ein Beispielbefehl könnte wie folgt aussehen:

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Nachdem Sie diesen Befehl ausgegeben haben, können Sie die Datei mit den Werten von oben aktualisieren:

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

  Eine Identität kann immer nur einmal registriert werden. Tritt ein Problem auf, dann wiederholen Sie den Vorgang mit einem Befehl, für den ein neuer Benutzername und ein neues Kennwort angegeben werden. Stellen Sie als Sicherheitsmaßnahme mit jeder Identität und den zugehörigen Werten für "enrollID" und "secret" nur einen einzigen Peer bereit. Verwenden Sie IDs und Kennwörter von Peers nicht erneut.

  Wird der Befehl erfolgreich ausgeführt, dann werden die im folgenden Beispiel dargestellten Informationen angezeigt:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

Durch einen Befehl "tree" können Sie die Arbeit prüfen, mit der Sie die Konfigurationsdatei vorbereitet haben. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Zertifikate gespeichert haben. Der Befehl "tree" sollte ein Ergebnis ähnlich der folgenden Struktur generieren:

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
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
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

### Hosts für Zertifikatssignieranforderungen (Certificate Signing Request, CSR)
{: #ibp-peer-deploy-csr-hosts}

Sie müssen die CSR-Hostnamen angeben, um einen Peer bereitzustellen. Die CSR-Hostnamen beinhalten die Proxy-IP-Adresse des Clusters, in dem Sie die Komponente bereitstellen, sowie den `Service-Host-Namen`, der der Hostname für das Helm-Diagramm ist.

#### Wert der Proxy-IP-Adresse für den Cluster ermitteln

Wenn Sie einen Peer in demselben {{site.data.keyword.cloud_notm}} Private-Cluster bereitstellen wollen, in dem Sie Ihre TLS-Zertifizierungsstelle bereitgestellt haben, geben Sie dieselbe Proxy-IP ein, die Sie bei der [Konfiguration für Ihre TLS-Zertifizierungsstelle](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms) verwendet haben. Wenn Sie den Peer in einem anderen Cluster bereitstellen wollen, können Sie den Wert für die Proxy-IP-Adresse des Clusters über die {{site.data.keyword.cloud_notm}} Private-Konsole abrufen. Sie müssen die Rolle eines Clusteradministrators für den {{site.data.keyword.cloud_notm}} Private-Cluster besitzen, in dem der Peer bereitgestellt werden soll.

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an. Klicken Sie im linken Navigationsfenster auf **Plattform** und anschließend auf **Knoten**, um die im Cluster definierten Knoten anzuzeigen.
2. Klicken Sie auf den Knoten mit der Rolle `Proxy` und kopieren Sie den Wert, der in der Tabelle für die `Host-IP` angegeben ist.
3. Fügen Sie die `Host-IP` als Wert für  `"hosts"` im nachfolgend gezeigten Abschnitt `"csr"` der Konfigurationsdatei ein:

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### Service-Host-Namen ermitteln
{: #ibp-peer-deploy-determine-svc-host-name}

Der `Service-Host-Name` ist der während der Bereitstellung von Ihnen angegebene `Name des Helm-Release`. Hat der Cluster die Proxy-IP-Adresse "9.42.134.44" und der `Name des Helm-Release` den Wert `org1peer1`, müssen Sie Folgendes im Abschnitt `"csr"` der Datei einfügen:

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
{: #ibp-peer-deploy-complete-config}

Nachdem Sie alle Schritte ausgeführt haben, sieht Ihre aktualisierte Konfigurationsdatei ähnlich wie im folgenden Beispiel aus:

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer1",
			"enrollsecret": "peer1pw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
           "org1peer1"
        ]
			}
		}
  }
}
```
{:codeblock}

Nachdem Sie diese Datei vollständig ausgefüllt haben, müssen Sie sie im JSON-Format speichern und als geheimen Kubernetes-Schlüssel an Ihre Peerbereitstellung übergeben. Erstellen Sie den geheimen Schlüssel mithilfe der Schritte im [nächsten Abschnitt](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp).

## Geheimen Schlüssel für Konfiguration erstellen
{: #ibp-peer-deploy-config-file-ibp}

Mit einem [geheimen Kubernetes-Schlüssel ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/configuration/secret/ "Geheimer Schlüssel") können Sie Informationen schützen und gemeinsam nutzen, ohne die Angaben direkt an die Bereitstellung übergeben zu müssen. Nachdem Sie die Konfigurationsdatei gespeichert haben, müssen Sie sie in `Base64` codieren.

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

  Speichern Sie die resultierende Ausgabe für Schritt 4 unten.

2. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an. Klicken Sie im linken Navigationsfenster auf **Konfiguration** und anschließend auf **Geheime Schlüssel**. Klicken Sie auf die Schaltfläche **Geheimen Schlüssel erstellen**, um ein Popup-Fenster zu öffnen, in dem Sie ein neues Objekt für einen geheimen Schlüssel generieren können.

3. Füllen Sie auf der Registerkarte **Allgemein** die folgenden Felder aus:
  - **Name:** Geben Sie für Ihren geheimen Schlüssel einen Namen ein, der in Ihrem Cluster eindeutig ist. Diesen Namen verwenden Sie beim Bereitstellen Ihres Peers. Der Name darf nur aus Kleinbuchstaben bestehen.  
  **Hinweis:** Wenn Sie einen Peer bereitstellen, wird durch die Bereitstellung automatisch ein neuer geheimer Schlüssel namens `<helm_release_name>-secret`. Achten Sie daher beim Benennen des geheimen Schlüssels darauf, dass sich der Name von `<helm_release_name>-secret` unterscheidet. Andernfalls schlägt die Bereitstellung des Helm-Diagramms fehl, weil der geheime Schlüssel, den sie zu erstellen versucht, bereits vorhanden ist.
  - **Namensbereich:** Dies ist der Namensbereich, zu dem Ihr geheimer Schlüssel hinzugefügt werden soll. Wählen Sie den `Namensbereich` aus, in dem Sie Ihren Peer bereitstellen wollen.
  - **Typ:** Geben Sie den Wert `Opaque` ein.

4. Klicken Sie im linken Navigationsbereich dieses Fensters auf die Registerkarte **Daten**. Geben Sie in den Feldern `Name` den Wert `secret.json` an und fügen Sie im Feld für den Wert die im `Base64-Format` codierte Zeichenfolge Ihrer Konfigurationsdatei ein.

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
{: #ibp-peer-deploy-peer-configuration}

Nachdem Sie das Objekt für den geheimen Schlüssel der Konfiguration des Peers erstellt haben, können Sie Ihren Peer mit den folgenden Schritten in {{site.data.keyword.cloud_notm}} Private konfigurieren und installieren. Sie können jeweils nur einen einzigen Peer installieren.

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf den Link **Katalog** in der oberen rechten Ecke.
2. Klicken Sie im linken Navigationsfenster auf `Blockchain` und suchen Sie nach der Kachel namens `ibm-blockchain-platform-prod` bzw. `ibm-blockchain-platform-dev`, falls Sie die Community Edition heruntergeladen haben. Klicken Sie auf die Kachel, um sie zu öffnen. Daraufhin wird eine Readme-Datei mit Informationen zur Installation und Konfiguration des Helm-Diagramms angezeigt.
3. Klicken Sie oben in der Anzeige auf die Registerkarte **Konfiguration** oder klicken Sie in der rechten unteren Ecke der Anzeige auf die Schaltfläche **Konfigurieren**.
4. Geben Sie die Werte für die [allgemeinen Konfigurationsparameter](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-configuration-parms) an und akzeptieren Sie die Lizenzvereinbarung.
5. Öffnen Sie das Twistie `Alle Parameter` und geben Sie den Wert für die [globalen Konfigurationsparameter](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-global-parameters) an.
6. Blättern Sie bis zum Abschnitt **Konfiguration des Peers** vor. Wählen Sie das Kontrollkästchen `Peer installieren` aus und füllen Sie die [Konfigurationseinstellungen](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-parameters) für den Peer aus.
7. Klicken Sie auf **Installieren**.

### Konfigurationsparameter
{: #ibp-peer-deploy-peer-configuration-parms}

Die folgende Tabelle enthält eine Auflistung der **speziell für die Peerkomponente** konfigurierbaren Parameter von {{site.data.keyword.blockchainfull_notm}} Platform sowie die zugehörigen Standardwerte. Wenn Sie mit den Peer ausprobieren wollen oder diese Komponente zum ersten Mal verwenden, verwenden Sie die Standardwerte für die Datenbank- und Speicherparameter. Blättern Sie zum Abschnitt für die Peerkomponente, wählen Sie das Kontrollkästchen `Peer installieren` aus und geben Sie die zugehörigen Konfigurationsinformationen nur für diese Komponente an. **Obwohl in der Benutzerschnittstelle für das Helm-Diagramm angegeben ist, dass keine weitere Konfiguration erforderlich ist, müssen Sie bestimmte Parameter eingeben, um einen Peer bereitzustellen.**

#### Allgemeine und globale Konfigurationsparameter
{: #ibp-peer-deploy-global-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
| `Helm-Releasename`| Der Name Ihres Helm-Release. Er muss mit einem Kleinbuchstaben beginnen und mit einem alphanumerischen Zeichen enden, darf jedoch ausschließlich Bindestriche und alphanumerische Zeichen in Kleinschreibung enthalten. Bei jedem Versuch, eine Komponente zu installieren, müssen Sie einen eindeutigen Namen für das Helm-Release verwenden. **Wichtig:** Dieser Wert muss mit dem Wert übereinstimmen, den Sie zum Generieren des Service-Host-Namens für das Feld "hosts" in Ihrer [JSON-Datei mit dem geheimen Schlüssel](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-csr-hosts) verwendet haben. | Nein | Ja  |
| `Zielnamensbereich`| Wählen Sie den Kubernetes-Namensbereich für die Installation des Helm-Diagramms aus. | Nein | Ja |
|**Globale Konfiguration**| Diese Parameter gelten für alle Komponenten im Helm-Diagramm.|||
| `Name des Servicekontos`| Geben Sie den Namen des [Servicekontos ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Servicekonten für Pods konfigurieren") ein, das Sie für die Ausführung des Pods verwenden wollen. | Standardwert | Nein |

#### Konfigurationsparameter für Peers
{: #ibp-peer-deploy-peer-parameters}

|  Parameter     | Beschreibung    | Standardwert  | Erforderlich |
| --------------|-----------------|-------|------- |
|**Clusterkonfiguration** |**Informationen zur Clusterkonfiguration** | ||
| `Peer installieren` | Wählen Sie diese Option aus, um einen Peer zu installieren.|Nicht ausgewählt | Ja, falls Sie einen Peer installieren wollen. |
| `Workerknotenarchitektur für Peer`| Wählen Sie Ihre Cloud-Plattformarchitektur aus (AMD64 oder S390x).| AMD64 | Ja |
| `Image-Repository für Peer`| Die Position des Helm-Diagramms für den Peer. In diesem Feld wird automatisch der installierte Pfad eingetragen. Falls Sie die Community Edition verwenden und kein Zugang zum Internet besteht, sollte dieser Wert mit dem Verzeichnis übereinstimmen, in das Sie das Fabric-Image für den Peer heruntergeladen haben. | ibmcom/ibp-fabric-peer | Ja |
| `Tag für Docker-Image des Peers`|Der Wert des Tags, der dem Peer-Image zugeordnet ist. |1.2.1 (wird automatisch mit dem richtigen Wert gefüllt)|Ja|
| `Peerkonfiguration`|Sie können die Konfiguration des Peers anpassen, indem Sie in diesem Feld eine eigene Konfigurationsdatei `core.yaml` einfügen. Eine Beispieldatei `core.yaml` finden Sie in der Beispielkonfiguration [`core.yaml` ![External link icon](../images/external_link.svg "External link icon")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml "hyperledger/fabric/core.yaml") **Nur für fortgeschrittene Benutzer**. |Nein|Nein|
| `Geheimer Schlüssel für Peerkonfiguration (erforderlich)`| Der Name des [geheimen Schlüssels für die Peerkonfiguration](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp), den Sie in {{site.data.keyword.cloud_notm}} Private erstellt haben.  |Nein|Ja|
|`MSP der Organisation (erforderlich)`|Diesen Wert finden Sie in Network Monitor (Starter Plan- und Enterprise Plan-Benutzerschnittstelle), indem Sie in der Übersichtsanzeige auf "Konfiguration des fernen Peers" klicken.  |Nein|Ja|
|`Peer-Service-Typ`| Hiermit wird angegeben, ob auf dem Peer [externe Ports zugänglich gemacht werden sollen ![External link icon](../images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types "Publishing services - service types"). Wählen Sie "NodePort" aus, damit die Ports extern zugänglich gemacht werden (empfohlen); wählen Sie "ClusterIP" aus, damit die Ports nicht zugänglich gemacht werden. Die Optionen "LoadBalancer" und "ExternalName" werden in diesem Release nicht unterstützt. | NodePort |Ja|
| `Statusdatenbank`| Zur Speicherung des Kanalledgers verwendete [Statusdatenbank](/docs/services/blockchain/glossary.html#glossary-state-database). Der Peer muss dieselbe Datenbank wie das [Blockchain-Netz](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) verwenden. | LevelDB | Ja |
|`Image-Repository für CouchDB`| Gilt nur, wenn CouchDB als Ledgerdatenbank ausgewählt wurde. In diesem Feld wird automatisch der installierte Pfad eingetragen. Falls Sie die Community Edition verwenden und kein Zugang zum Internet besteht, sollte dieser Wert mit dem Verzeichnis übereinstimmen, in das Sie das Fabric-Image für CouchDB heruntergeladen haben.| ibmcom/ibp-fabric-couchdb | Ja |
| `Tag für Docker-Image von CouchDB`| Gilt nur, wenn CouchDB als Ledgerdatenbank ausgewählt wurde. Der Wert des Tags, der dem CouchDB-Image zugeordnet ist. | Wird automatisch mit dem richtigen Wert gefüllt.| Ja |
| `Datenpersistenz für Peer aktiviert`| Aktivieren der Funktionalität zum persistenten Speichern von Daten nach Neustart oder Ausfall des Clusters. Siehe hierzu [Speicher in Kubernetes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/ "Volumes").  *Wird diese Option inaktiviert, gehen alle Daten bei einem Failover oder Podneustart verloren.* | Aktiviert | Nein |
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
| `Peer-CPU-Anforderung` | Mindestanzahl der CPUs, die dem Peer zugeordnet werden soll. | 1 | Ja |
| `Peer-CPU-Limit` | Maximale Anzahl CPUs, die dem Peer zugeordnet werden soll.| 2 | Ja |
| `Peer-Speicheranforderung` | Mindestens erforderlicher Speicher, der dem Peer zugeordnet werden soll. | 1Gi | Ja |
| `Peer-Speicherbegrenzung` | Maximalwert für Speicher, der dem Peer zugeordnet werden soll. | 4Gi | Ja |
| `CouchDB-CPU-Anforderung` | Mindestanzahl der CPUs, die für CouchDB zugeordnet werden soll.| 1 | Ja |
| `CouchDB-CPU-Limit` | Maximale Anzahl CPUs, die für CouchDB zugeordnet werden soll. | 2 | Ja |
| `CouchDB-Speicheranforderung` | Mindestens erforderlicher Speicher, der für CouchDB zugeordnet werden soll.| 1Gi | Ja |
| `CouchDB-Speicherbegrenzung` | Maximalwert für Speicher, der für CouchDB zugeordnet werden soll. | 4Gi | Ja |


Wird für die Datenbank des Peers "CouchDB" ausgewählt, werden zwei Container im Pod erstellt, und zwar einer für den Peer und der andere für CouchDB. Der Peer-Container enthält einen einzelnen Datenträgermount für den Peer-PVC, in dem die Blöcke und Transaktionen im Dateisystem gespeichert sind. Der CouchDB-Container hängt den PVC für die Statusdatenbank des Peers an, der die Ledgerdaten enthält.

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
{: #ibp-peer-deploy-helm-cli}

Zur Installation des Helm-Release können Sie alternativ auch die Helm-CLI verwenden. Stellen Sie vor Ausführung des Befehls `helm install` sicher, dass Sie [das Helm-Repository Ihres Clusters zur Helm-CLI-Umgebung hinzugefügt haben ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Internes Helm-Repository zur Helm-CLI hinzufügen").

Die für die Installation erforderlichen Parameter können Sie festlegen, indem Sie eine Datei `yaml` erstellen und sie an den folgenden Befehl `helm install` übergeben.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

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
{: #ibp-peer-deploy-verify-installation-ibp}

Nachdem Sie die Konfigurationsparameter ausgefüllt und auf die Schaltfläche **Installieren** geklickt haben, können Sie die Bereitstellung prüfen, indem Sie auf die Schaltfläche **Helm-Release anzeigen** klicken. Wenn die Bereitstellung erfolgreich durchgeführt wurde, sollte der Wert 1 in den Feldern `GEWÜNSCHT`, `DERZEIT`, `AKTUELL` und `VERFÜGBAR` der Tabelle "Bereitstellung" angezeigt werden. Möglicherweise müssen Sie zur Aktualisierung klicken und warten, bis die Tabelle aktualisiert angezeigt wird. Die Tabelle "Bereitstellung" können Sie außerdem aufrufen, indem Sie in der linken oberen Ecke der {{site.data.keyword.cloud_notm}} Private-Konsole auf das **Menüsymbol** klicken. Klicken Sie in der Menüliste auf **Workloads** und dann auf **Helm-Releases**.

Wenn Sie bis zum Abschnitt `Hinweise` vorblättern, finden Sie wichtige Informationen, die Sie zum [Betrieb Ihres Peers](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate) verwenden.

## Protokolle des Peers anzeigen
{: #ibp-peer-deploy-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [`Befehlen der CLI "kubectl"`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster enthalten ist. Weitere Informationen enthalten die [Anweisungen für den Zugriff auf die Protokolle](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-view-logs).

## Nächste Schritte
{: #ibp-peer-deploy-whats-next}

Nachdem Sie den Peer bereitgestellt haben, müssen Sie verschiedene operative Schritte ausführen, bevor Sie Transaktionen an das Blockchain-Netz übergeben und das verteilte Ledger aus dem Blockchain-Netz lesen können. Weitere Informationen finden Sie unter [Peers mit Starter Plan oder Enterprise Plan betreiben](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).


## Fehlerbehebung
{: #ibp-peer-deploy-troubleshooting}

### **Problem:** Fehler bei der URL der Zertifizierungsstelle, wenn der Befehl `enroll` ausgeführt wird.
{: #ibp-peer-deploy-ca-enroll-error}

Der Befehl "enroll" im Fabric-CA-Client kann fehlschlagen, wenn die Eintragungs-URL, der Parameterwert `-u`, ein Sonderzeichen enthält. Beispiel: Der folgende Befehl mit der Eintragungs-ID und dem Kennwort `admin:C25A06287!0`

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

schlägt fehl und führt zu folgendem Fehler:

```
!pw@9.12.19.115: event not found
```

### **Lösung:**
Sie müssen entweder das Sonderzeichen codieren oder die URL in die Hochkommas einschließen. Beispiel: `!` wird zu `%21` oder der Befehl sieht wie folgt aus:

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

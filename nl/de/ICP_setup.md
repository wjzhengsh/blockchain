---

copyright:
  years: 2018, 2019
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

# {{site.data.keyword.cloud_notm}} Private einrichten
{: #icp-setup}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Bevor Sie Komponenten von {{site.data.keyword.blockchainfull}} Platform bereitstellen und ein Blockchain-Netz in {{site.data.keyword.cloud_notm}} Private (ICP) aufbauen, müssen Sie {{site.data.keyword.cloud_notm}} Private in Ihrer eigenen Umgebung einrichten.
{:shortdesc}

## Voraussetzungen
{: #icp-setup-prerequisites}

Führen Sie die folgenden vorausgesetzten Schritte aus und bereiten Sie Ihre Umgebung für die Installation von {{site.data.keyword.cloud_notm}} Private vor.

### Docker
Für {{site.data.keyword.cloud_notm}} Private muss Docker installiert sein. Befolgen Sie zur Installation von Docker die zugehörigen Anweisungen unter [{{site.data.keyword.cloud_notm}} Private installieren ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html "Installing {{site.data.keyword.cloud_notm}} Private"), um Docker zu installieren.

### Einstellungen von {{site.data.keyword.cloud_notm}} Private
Vor der Installation von {{site.data.keyword.cloud_notm}} Private helfen Ihnen die folgenden Tipps bei der Vorbereitung der Knoten für die {{site.data.keyword.cloud_notm}} Private-Installation. Weitere Voraussetzungen für {{site.data.keyword.cloud_notm}} Private finden Sie in der [{{site.data.keyword.cloud_notm}}{{site.data.keyword.cloud_notm}} Private-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "Cluster für die Installation vorbereiten").

#### Einstellung `vm.max_map_count` aktualisieren
{{site.data.keyword.cloud_notm}} verwendet Elastic Search zur Protokollierung und Messung. Zur Vermeidung von Ausnahmebedingungen durch unzureichenden Speicher ist für Elastic Search die Konfiguration der Systemeigenschaft `vm.max_map_count` erforderlich. Lesen Sie vor der Installation von {{site.data.keyword.cloud_notm}} Private die [Konfigurationsanweisungen für Elastic Search ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Virtueller Speicher"), um diese Eigenschaft auf jedem Knoten zu konfigurieren. Mit den folgenden Befehlen können Sie diese Eigenschaft permanent festlegen:

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Datei `/etc/hosts` auf jeden Knoten im Cluster konfigurieren

- {{site.data.keyword.cloud_notm}} Private verwendet [Kubernetes ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Kubernetes-Grundlagen kennenlernen") für die Verwaltung von containerisierten Anwendungen. Der Domänennamensserver (DNS) von Kubernetes schlägt fehl, falls nicht auf jedem Knoten in der Datei `/etc/hosts` Hostnamen konfiguriert sind. [Fügen Sie die IP-Adresse, den Hostnamen und den Kurznamen jedes Knotens in Ihrem Cluster ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "Cluster konfigurieren") auf jedem Knoten in der Datei `/etc/hosts` ein.

- [IPv6 wird von {{site.data.keyword.cloud_notm}} Private nicht unterstützt ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "IPv6 wird nicht unterstützt"). Inaktivieren Sie zur Vermeidung von Problemen mit dem DNS-Service in einem {{site.data.keyword.cloud_notm}} Private-Cluster auf jedem Knoten die IPv6-Einstellungen in der Datei `/etc/hosts`, indem Sie die folgende Zeile durch Eingabe des Zeichens `#` am Beginn der Zeile auf Kommentar setzen:
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Erforderliche Ressourcen
{: #icp-setup-resources}

Stellen Sie sicher, dass Ihr {{site.data.keyword.cloud_notm}} Private-System die Mindestvoraussetzungen für Hardwareressourcen erfüllt:

| Komponente | vCPU | RAM | Platte für Datenspeicherung |
|-----------|------|-----|-----------------------|
| Zertifizierungsstelle (CA) | 1 |192 MB | 1 GB |
| Anordnungsknoten | 2 | 512 MB | 100 GB mit der Möglichkeit zur Erweiterung |
| Peer | 2 | 2 GB | 50 GB mit der Möglichkeit zur Erweiterung |
| CouchDB für Peer | 2| 2 GB |50 GB mit der Möglichkeit zur Erweiterung |

 **Hinweise:**
 - Eine vCPU (virtuelle CPU) ist ein virtueller Kern, der einer virtuellen Maschine oder einem physischen Prozessorkern zugeordnet wird, wenn der Server nicht für virtuelle Maschinen partitioniert ist. Bei der Festlegung des virtuellen Prozessorkerns (VPC) für Ihre Bereitstellung in {{site.data.keyword.cloud_notm}} Private müssen Sie bestimmte vCPU-Voraussetzungen berücksichtigen. VPC ist eine Maßeinheit, mit der die Lizenzgebühren für {{site.data.keyword.IBM_notm}} Produkte bestimmt werden. Weitere Informationen zu Szenarios für die Festlegung des VPC finden Sie unter [Virtueller Prozessorkern (VPC) ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Diese Mindestressourcenwerte sind für Tests und Versuchsreihen ausreichend. Für eine Umgebung mit einer größeren Menge von Transaktionen muss unbedingt eine ausreichend große Speicherkapazität zugeordnet sein, beispielsweise 250 GB für Ihren Peer und 500 GB für Ihren Anordnungsservice. Die zu verwendende Speicherkapazität richtet sich nach der Anzahl der Transaktionen und der Anzahl der Signaturen, die in Ihrem Netz benötigt werden. Falls eine Überlastung des Speichers auf Ihrem Peer oder Anordnungsknoten droht, müssen Sie einen neuen Peer oder Anordnungsknoten mit einem größeren Dateisystem bereitstellen und über Ihre anderen Komponenten auf denselben Kanälen synchronisieren lassen.

#### Hinweise zum Speicher
{: #icp-setup-storage-considerations}

* Sie müssen den Speicher ermitteln, der von Ihren Komponenten verwendet wird. Falls Sie die Standardeinstellungen verwenden, erstellt das Helm-Diagramm für den Peer eine neue Anforderung für einen persistenten Datenträger namens `my-data-pvc` für Ihre Peerdaten. Falls Sie CouchDB als Ledgerdatenbank auswählen, erstellt das Helm-Diagramm eine weitere Anforderung für einen persistenten Datenträger namens `statedb-pvc` für Ihre Ledgerdatenbank.
* Wenn Sie nicht mit den Standardspeichereinstellungen arbeiten wollen, dann vergewissern Sie sich, dass während der {{site.data.keyword.cloud_notm}} Private-Installation eine *neue* Speicherklasse (storageClass) eingerichtet wird. Andernfalls muss der Kubernetes-Systemadministrator eine Speicherklasse erstellen, bevor Sie die Bereitstellung durchführen können.
* Die [dynamische Bereitstellung ![External link icon](images/external_link.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamische Datenträgerbereitstellung") ist nur für amd64-Knoten in {{site.data.keyword.cloud_notm}} Private verfügbar. Wenn in Ihrem Cluster eine Kombination aus s390x- und amd64-Workerknoten eingesetzt wird, dann kann die dynamische Bereitstellung demzufolge nicht verwendet werden.
* Falls die dynamische Bereitstellung nicht verwendet wird, müssen [persistente Datenträger ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistente Datenträger") erstellt und mit Kennzeichnungen konfiguriert werde, mit deren Hilfe der Bindungsprozess von Kubernetes Persistent Volume Claim (PVC) optimiert werden kann.
* Falls Sie persistente Datenträger von NFS v2/v3 verwenden, müssen Sie auf dem Hostsystem, auf dem sich das NFS-Dateisystem befindet, das Modul **NFS status monitor for NFSv2/v3 Filesystem Locks** aktivieren, das auch unter der Bezeichnung **rpc-statd** bekannt ist. Dieses Modul ermöglicht es Ihrem NFS-Dateisystem, die exklusiven Sperren für Dateien zu überprüfen, die andere
Prozesse halten. Führen Sie zum Aktivieren dieses Moduls die folgenden Befehle aus:

  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## {{site.data.keyword.cloud_notm}} Private installieren
{: #icp-setup-install}

Führen Sie die folgenden Schritte aus, um {{site.data.keyword.cloud_notm}} Private in Ihrer Umgebung zu installieren und einzurichten.

1. Installieren Sie einen Cluster von [{{site.data.keyword.cloud_notm}} Private ![External link icon](images/external_link.svg "External link icon") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) mit Version 3.1.0. Falls Sie das Helm-Diagramm für Entwicklung, Tests oder Versuchsreihen verwenden wollen, können Sie [{{site.data.keyword.cloud_notm}} Private Community Edition Version 3.1.0 ![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE version 3.1.0") kostenlos installieren.

2. Installieren Sie die CLI von {{site.data.keyword.cloud_notm}} Private [3.1.0 ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html), um die Zertifizierungsstelle zu installieren und zu betreiben.

Nach der Installation von {{site.data.keyword.cloud_notm}} Private können Sie mit dem [Import des {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private-Helm-Diagramms](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster fortfahren.

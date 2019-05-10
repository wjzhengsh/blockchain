---

copyright:
  years: 2017, 2019
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

# {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private installieren
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private wird als Helm-Diagrammdatei geliefert, die in einem lokalen {{site.data.keyword.cloud_notm}} Private-Cluster installiert werden kann. Nachdem Sie das Helm-Diagramm installiert haben, finden Sie {{site.data.keyword.blockchainfull_notm}} Platform als Anwendung im {{site.data.keyword.cloud_notm}} Private-Katalog.

Lesen Sie vor der Installation von {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private die [Hinweise und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations). Weitere Informationen zum Bereitstellen der Blockchain-Komponenten, die im Helm-Diagramm enthalten sind, finden Sie im Abschnitt [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private steht in zwei Editionen zur Verfügung:

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private wird über Passport Advantage (PPA) bereitgestellt. Sie benötigen die erforderliche Lizenz, um auf  [Passport Advantage Online ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") zugreifen zu können. Die technische Unterstützung für {{site.data.keyword.blockchainfull_notm}} Platform ist im Kauf enthalten.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition ist ein kostenloses Angebot für Erkundung, Entwicklung und Tests. Auf diese kostenlose Version kann über [GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts") zugegriffen werden. Bitte beachten Sie, dass  {{site.data.keyword.IBM_notm}} für die Community Edition keinen Support bietet.

## Voraussetzungen für die Installation des Helm-Diagramms
{: #helm-install-prereqs}

Vor der Installation des Helm-Diagramms müssen Sie einen {{site.data.keyword.cloud_notm}} Private-Cluster konfiguriert und einen neuen Zielnamensbereich erstellt haben, der an eine Pod-Sicherheitsrichtlinie gebunden ist. Lesen Sie hierzu die Anweisungen im Abschnitt über die [Einrichtung und Konfiguration eines {{site.data.keyword.cloud_notm}} Private-Clusters](/docs/services/blockchain/ICP_setup.html#icp-setup).

## {{site.data.keyword.blockchainfull_notm}} Platform hinter einer Firewall installieren
{: #helm-install-prereqs-firewall}

Sie können die Komponenten von {{site.data.keyword.blockchainfull_notm}} Platform hinter einer Firewall ohne Internetkonnektivität bereitstellen. Das PPA-Paket für das Helm-Diagramm enthält alle Docker-Images für die Fabric-Komponenten, die von {{site.data.keyword.blockchainfull_notm}} Platform verwendet werden; die Komponenten müssen nicht während der Bereitstellung von Docker Hub heruntergeladen werden.

Das Helm-Diagramm für Community Edition enthält jedoch nicht die erforderlichen Docker-Images für die Fabric-Komponenten, weil diese Edition so konfiguriert ist, dass die entsprechenden Images während der Bereitstellung von Docker Hub heruntergeladen werden. Falls keine Internetkonnektivität besteht, schlägt die Bereitstellung fehl. Daher müssen Sie zusätzliche Schritte ausführen, um auf einer Maschine mit Internetverbindung Archive zu erstellen, bevor Sie die Archive in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster installieren können. Die folgenden Images sind erforderlich:
- [Fabric-Peer ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Fabric peer")
- [Fabric-Zertifizierungsstelle ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric-Anordnungsknoten ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric-Couch-Datenbank ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init`-Image für Bootstrap und Konfiguration von Komponenten, inklusive der MSP-Ordner ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Docker-in-Docker-Image (DinD), das von Peers zum Ausführen von Chaincode-Containern verwendet wird ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Weitere Informationen zur Verwendung dieser Images finden Sie unter  [Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html). Die Spezifikationsdatei `manifest.yaml` ist im Helm-Diagramm unter dem Verzeichnis  `ibm-blockchain-platform-dev/ibm_cloud_pak` zu finden.

## Helm-Diagramm in {{site.data.keyword.cloud_notm}} Private importieren
{: #helm-install-importing}

1. Laden Sie die Helm-Diagrammdatei für IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private bei [Passport Advantage Online ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") oder für die kostenlose Community Edition bei [GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts") herunter.  Dieses Helm-Diagrammpaket enthält drei untergeordnete Helm-Diagramme für die Zertifizierungsstelle, den Anordnungsknoten und den Peer.

2. Wenn Sie sich noch nicht angemeldet haben, melden Sie sich bei Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster an.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. Stellen Sie sicher, dass die  [Docker-CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html)  konfiguriert ist. Greifen Sie nach dem Konfigurieren der Docker-CLI mit dem folgenden Befehl auf die Image-Registry in Ihrem Cluster zu:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. Suchen Sie mit dem folgenden Befehl nach dem Namen des Repositorys in {{site.data.keyword.cloud_notm}} Private, um das Helm-Diagramm hochzuladen:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Wenn dieser Befehl erfolgreich ausgeführt wurde, wird eine Liste der Repositorys in Ihrem Cluster angezeigt. Wählen Sie den Namen des Zielrepositorys aus und speichern Sie ihn. Sie benötigen ihn im folgenden Befehl.

5. Importieren Sie das Helm-Diagramm über die Befehlszeile.
  Welchen Befehl Sie zum Importieren des Helm-Diagramms ausführen müssen, ist davon abhängig, ob Sie das Helm-Diagramm bei Passport Advantage (PPA) oder bei GitHub heruntergeladen haben.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Befolgen Sie diese Anweisungen, wenn Sie das Helm-Diagramm bei PPA heruntergeladen haben.
    Führen Sie den folgenden Befehl in der {{site.data.keyword.cloud_notm}} Private-CLI ausgehend von dem Verzeichnis aus, in dem Sie das bei PPA heruntergeladene Helm-Diagrammpaket gespeichert haben, um das Helm-Diagramm in Ihren {{site.data.keyword.cloud_notm}} Private-Cluster zu importieren.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Ersetzen Sie hierbei die folgenden Werte:
    - `<archive-name>` durch den Namen der heruntergeladenen Datei `.tgz`
    - `<cluster_CA_domain>:8500` durch die Domäne, die Sie zur Anmeldung bei Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster verwenden.
    - `<repo-name>` durch das Helm-Repository, in das Sie das Diagramm hochladen wollen ( mit dem Befehl 'cloudctl catalog repos' können Sie Ihre Repositorys auflisten)

    Wenn dieser Befehl erfolgreich ausgeführt wird, werden Informationen ähnlich den Folgenden angezeigt:

    ```  
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
      Processing image: ibmcom/ibp-couchdb-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
      Processing image: ibmcom/ibp-couchdb-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
      Processing image: ibmcom/ibp-dind-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
      Processing image: ibmcom/ibp-dind-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-1.0.2.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```  
    </details>

  - **Bei GitHub heruntergeladene Community Edition**
    Befolgen Sie diese Anweisungen, wenn Sie das Helm-Diagramm bei GitHub heruntergeladen haben.

    Führen Sie den folgenden Befehl in der {{site.data.keyword.cloud_notm}} Private-CLI ausgehend von dem Verzeichnis aus, in dem Sie das bei GitHub heruntergeladene Helm-Diagrammpaket gespeichert haben, um das Helm-Diagramm in Ihren {{site.data.keyword.cloud_notm}} Private-Cluster zu importieren. Importieren Sie das bei GitHub heruntergeladene Helm-Diagramm mit dem folgenden Befehl:
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Ersetzen Sie hierbei die folgenden Werte:
    - `<helm_chart_from_github>` durch den Namen der heruntergeladenen Datei ".tgz"
    - `<repo-name>` durch das Helm-Repository, in das Sie das Diagramm hochladen wollen ( mit dem Befehl 'cloudctl catalog repos' können Sie Ihre Repositorys auflisten)

    Wenn dieser Befehl erfolgreich ausgeführt wird, werden Informationen ähnlich den Folgenden angezeigt:
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

Klicken Sie in der {{site.data.keyword.cloud_notm}} Private-Konsole auf die Schaltfläche **Katalog** und dann im linken Navigationsfenster auf **Blockchain**, um zu prüfen, ob der Import erfolgreich verlaufen ist. Wenn dies der Fall ist, sollte auf der {{site.data.keyword.cloud_notm}} Private-Seite "Katalog" die Kachel **ibm-blockchain-platform-prod** oder **ibm-blockchain-platform-dev** angezeigt werden.

## Einzelne Komponenten bereitstellen
{: #helm-install-deploying-components}

Klicken Sie nach der Installation des Helm-Diagramms in Ihrem {{site.data.keyword.cloud_notm}} Private-Katalog auf die Kachel **ibm-blockchain-platform-prod** oder **ibm-blockchain-platform-dev**, um sie zu öffnen. Auf der Konfigurationsseite können Sie jede gewünschte Komponente Ihres Blockchain-Netzes einzeln bereitstellen. Weitere Informationen zu den Komponenten, die für Ihre Blockchain-Lösung erforderlich sind, und zu der Reihenfolge, in der sie bereitgestellt werden müssen, finden Sie in der [Einführung zu {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp).

Stellen Sie anschließend die einzelnen Komponenten bereit:

- Falls Sie einen Anordnungsknoten bereitstellen wollen, müssen Sie zunächst eine Zertifizierungsstelle für den Anordnungsknoten einrichten. Die Zertifizierungsstelle generiert Zertifikate, die von anderen Komponenten in Ihrer Organisation verwendet werden. Weitere Informationen enthält der Abschnitt [Zertifizierungsstelle für {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Anschließend können Sie den Anordnungsknoten bereitstellen, der als allgemeine Bindung des Netzes dient. Weitere Informationen finden Sie unter [Anordnungsknoten von {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private bereitstellen](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy).

- Falls Sie einen Peer bereitstellen wollen, müssen Sie zunächst eine Zertifizierungsstelle für den Peer einrichten. Die Zertifizierungsstelle generiert Zertifikate, die vom Peer verwendet werden. Weitere Informationen enthält der Abschnitt [Zertifizierungsstelle für {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Sobald Sie anschließend bereit dafür sind, an einem Netz teilzunehmen, können Sie die Peers bereitstellen, die Kanälen beitreten, Transaktionen zulassen und Ihre Daten speichern. Weitere Informationen finden Sie abhängig von dem Blockchain-Netz, an dem der Peer teilnehmen soll, im Abschnitt [{{site.data.keyword.blockchainfull_notm}}-Peer in {{site.data.keyword.cloud_notm}} Private bereitstellen](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) oder [{{site.data.keyword.blockchainfull_notm}}-Peer für Starter Plan- oder Enterprise Plan-Netz bereitstellen](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy).

## Upgrade des Helm-Diagramms auf {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading}

Wenn Sie {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Version 1.0.1 verwenden, können Sie ein Upgrade Ihres Helms-Diagramms auf {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Version 1.0.2 durchführen, indem Sie die Anweisungen unter [Upgrades für Bundle-Produkte ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "Upgrades für Bundle-Produkte") in der Dokumentation zu {{site.data.keyword.cloud_notm}} Private befolgen. Zusätzlich zu dem Befehl `helm upgrade` in den Anweisungen können Sie auch [die {{site.data.keyword.cloud_notm}} Private-Konsole verwenden, um ein Upgrade des Helm-Release durchzuführen](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui).

Für das Upgrade eines Helm-Diagramms müssen Sie über die Rolle **Clusteradministrator** oder **Teamadministrator** für {{site.data.keyword.cloud_notm}} Private verfügen.
{:note}

Ein Rollback von {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Version 1.0.2 auf Version 1.0.1 wird nicht unterstützt.

### Upgrade des Helm-Release über die {{site.data.keyword.cloud_notm}} Private-Konsole
{: #helm-install-upgrading-ui}

Wenn Sie ein Upgrade für ein Helm-Release über die {{site.data.keyword.cloud_notm}} Private-Konsole durchführen, müssen Sie die `wiederverwendeten Werte` überprüfen und dürfen keine Parameter verändern.
{:important}  

Führen Sie die folgenden Schritte aus, um ein Upgrade für das Helm-Release über die {{site.data.keyword.cloud_notm}} Private-Konsole durchzuführen:
1. Klicken Sie in der Menüleiste auf **Workloads** > **Helm-Releases**.
2. Wählen Sie das Helm-Release aus, für das Sie ein Upgrade durchführen möchten.
3. Überprüfen Sie die Versionsnummer unter `Verfügbare Version` im Abschnitt **Details und Upgrades**.
4. Klicken Sie unter der Versionsnummer auf **ReadMe**, um die Releaseinformationen auf wesentliche Änderungen zu prüfen.
5. Klicken Sie auf **Upgrade** und wählen Sie aus der Dropdown-Liste das richtige Repository und die richtige Version aus.
6. Achten Sie darauf, dass `Werte wiederverwenden` ausgewählt ist.
7. Klicken Sie auf **Upgrade**.

Warten Sie, bis die Bestätigungsnachricht und der aktualisierte Status des Release neben dem Releasenamen oben links in der Konsole erscheinen.

### Weitere Schritte für das Upgrade eines Peers
{: #helm-install-upgrading-peer}

Nach dem Upgrade eines Peers sind einige zusätzliche Schritte erforderlich, um den Upgradeprozess abzuschließen. Auf der Peer-Seite für das Helm-Release werden unter dem Abschnitt **Pod** zwei Peer-Pods angezeigt:
- Der neue Peer-Pod mit dem Status `CrashLoopBackOff`
- Der ursprüngliche Peer-Pod mit dem Status `Aktiv`

Notieren Sie die zugehörigen Peernamen, die Sie in den folgenden Schritten verwenden müssen.
{:tip}

Führen Sie in der Befehlszeilenschnittstelle die folgenden Schritte aus, um den Peer-Upgrade-Prozess abzuschließen:
1. Melden Sie sich über die {{site.data.keyword.cloud_notm}} Private-Befehlszeilenschnittstelle bei Ihrem Cluster an und führen Sie den Befehl `kubectl get replicaset` aus.
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. Suchen Sie die Peer-Replikatgruppe, die dem ursprünglichen Peer entspricht. Der Name der Peer-Replikatgruppe muss mit dem Anfangsabschnitt des Peernamens im Abschnitt **Pod** der {{site.data.keyword.cloud_notm}} Private-Konsole übereinstimmen.
3. Löschen Sie die Peer-Replikatgruppe.
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. Löschen Sie den neuen Peer-Pod. Dies ist der Peer-Pod mit dem Status `CrashLoopBackOff` in der Benutzerschnittstelle.
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

Das Upgrade für den Peer war erfolgreich, wenn Sie sehen, dass ein neuer Peer-Pod mit dem Status `Aktiv` erstellt wurde.

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

# {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private installieren
{: #remote-peer-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private wird als Helm-Diagrammdatei geliefert, die on einem lokalen ICP-Cluster installiert werden kann. Nachdem Sie das Helm-Diagramm installiert haben, finden Sie {{site.data.keyword.blockchainfull_notm}} Platform als Anwendung im ICP-Katalog.

Lesen Sie vor der Installation von {{site.data.keyword.blockchainfull_notm}} Platform for ICP die  [Hinweise und Einschränkungen](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations). Weitere Informationen zum Bereitstellen der Blockchain-Komponenten, die im Helm-Diagramm enthalten sind, finden Sie im Abschnitt  [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp-for-icp-about.html).

{{site.data.keyword.blockchainfull_notm}} Platform for ICP steht in zwei Editionen zur Verfügung:

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private wird über Passport Advantage (PPA) bereitgestellt. Sie benötigen die erforderliche Lizenz, um auf  [Passport Advantage Online ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") zugreifen zu können. Die technische Unterstützung für {{site.data.keyword.blockchainfull_notm}} Platform ist im Kauf enthalten.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition ist ein kostenloses Angebot für Erkundung, Entwicklung und Tests. Auf diese kostenlose Version kann über [GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts") zugegriffen werden. Bitte beachten Sie, dass  {{site.data.keyword.IBM_notm}} für die Community Edition keinen Support bietet.

## Voraussetzungen für die Installation des Helm-Diagramms
{: #helm-install-prereqs}

Vor der Installation des Helm-Diagramms müssen Sie einen ICP-Cluster konfiguriert haben. Lesen Sie hierzu die Anweisungen im Abschnitt über die  [Einrichtung und Konfiguration eines ICP-Clusters](/docs/services/blockchain/ICP_setup.html).

## {{site.data.keyword.blockchainfull_notm}} Platform hinter einer Firewall installieren
{: #helm-install-prereqs-firewall}

Sie können die Komponenten von {{site.data.keyword.blockchainfull_notm}} Platform hinter einer Firewall ohne Internetkonnektivität bereitstellen. Das PPA-Paket für das Helm-Diagramm enthält alle Docker-Images für die Fabric-Komponenten, die von {{site.data.keyword.blockchainfull_notm}} Platform verwendet werden; die Komponenten müssen nicht während der Bereitstellung von Docker Hub heruntergeladen werden.

Das Helm-Diagramm für Community Edition enthält jedoch nicht die erforderlichen Docker-Images für die Fabric-Komponenten, weil diese Edition so konfiguriert ist, dass die entsprechenden Images während der Bereitstellung von Docker Hub heruntergeladen werden. Falls keine Internetkonnektivität besteht, schlägt die Bereitstellung fehl. Daher müssen Sie zusätzliche Schritte ausführen, um auf einer Maschine mit Internetverbindung Archive zu erstellen, bevor Sie die Archive in Ihrem ICP-Cluster installieren können. Die folgenden Images sind erforderlich:
- [Fabric-Peer ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Fabric peer")
- [Fabric-Zertifizierungsstelle ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric-Anordnungsknoten ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric-Couch-Datenbank ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init`-Image für Bootstrap und Konfiguration von Komponenten, inklusive der MSP-Ordner ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Docker-in-Docker-Image (DinD), das von Peers zum Ausführen von Chaincode-Containern verwendet wird ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Weitere Informationen zur Verwendung dieser Images finden Sie unter  [Ausgewählte Anwendungen ohne Internetverbindung zu Clustern hinzufügen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html). Die Spezifikationsdatei `manifest.yaml` ist im Helm-Diagramm unter dem Verzeichnis  `ibm-blockchain-platform-dev/ibm_cloud_pak` zu finden.

## Helm-Diagramm in ICP importieren

1. Laden Sie die Helm-Diagrammdatei für IBM Blockchain Platform for ICP bei [Passport Advantage Online ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") oder für die kostenlose Community Edition bei [GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts") herunter.  Dieses Helm-Diagrammpaket enthält drei untergeordnete Helm-Diagramme für die Zertifizierungsstelle, den Anordnungsknoten und den Peer.

2. Melden Sie sich bei Ihrem ICP-Cluster an, wenn Sie dort noch nicht angemeldet sind.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. Stellen Sie sicher, dass die  [Docker-CLI](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html)  konfiguriert ist. Greifen Sie nach dem Konfigurieren der Docker-CLI mit dem folgenden Befehl auf die Image-Registry in Ihrem Cluster zu:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. Suchen Sie mit dem folgenden Befehl nach dem Namen des Repositorys in ICP, um das Helm-Diagramm hochzuladen:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Wenn dieser Befehl erfolgreich ausgeführt wurde, wird eine Liste der Repositorys in Ihrem Cluster angezeigt. Wählen Sie den Namen des Zielrepositorys aus und speichern Sie ihn. Sie benötigen ihn im folgenden Befehl.

6. Importieren Sie das Helm-Diagramm über die Befehlszeile. Welchen Befehl Sie zum Importieren des Helm-Diagramms ausführen müssen, ist davon abhängig, ob Sie das Helm-Diagramm bei Passport Advantage (PPA) oder bei GitHub heruntergeladen haben.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Befolgen Sie diese Anweisungen, wenn Sie das Helm-Diagramm bei PPA heruntergeladen haben. Führen Sie den folgenden Befehl in der ICP-CLI ausgehend von dem Verzeichnis aus, in dem Sie das bei PPA heruntergeladene Helm-Diagrammpaket gespeichert haben, um das Helm-Diagramm in Ihren ICP-Cluster zu importieren.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Ersetzen Sie hierbei die folgenden Werte:
    - `<archive-name>` durch den Namen der heruntergeladenen Datei `.tgz`
    - `<cluster_CA_domain>:8500` durch die Domäne, die Sie zur Anmeldung bei Ihrem ICP-Cluster nutzen
    - `<repo-name>` durch das Helm-Repository, in das Sie das Diagramm hochladen wollen (mit dem Befehl 'cloudctl catalog repos' können Sie Ihre Repositorys auflisten)

    Wenn dieser Befehl erfolgreich ausgeführt wird, werden Informationen ähnlich den Folgenden angezeigt:
    ```
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      Processing image: ibmcom/icp-dind-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      Processing image: ibmcom/icp-dind-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-0.1.5.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```

  - **Bei GitHub heruntergeladene Community Edition**
    Befolgen Sie diese Anweisungen, wenn Sie das Helm-Diagramm bei GitHub heruntergeladen haben.

    Führen Sie in dem Verzeichnis, in dem Sie das heruntergeladene Helm-Diagramm gespeichert haben, den folgenden Befehl in der ICP-Befehlszeilenschnittstelle aus, um das Helm-Diagramm in Ihren ICP-Cluster zu importieren. Importieren Sie das bei GitHub heruntergeladene Helm-Diagramm mit dem folgenden Befehl:
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Ersetzen Sie hierbei die folgenden Werte:
    - `<helm_chart_from_github>` durch den Namen der heruntergeladenen Datei ".tgz"
    - `<repo-name>` durch das Helm-Repository, in das Sie das Diagramm hochladen wollen (mit dem Befehl 'cloudctl catalog repos' können Sie Ihre Repositorys auflisten)

    Wenn dieser Befehl erfolgreich ausgeführt wird, werden Informationen ähnlich den Folgenden angezeigt:
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

Klicken Sie in der ICP-Konsole auf die Schaltfläche **Katalog** und dann im linken Navigationsfenster auf  **Blockchain**, um zu prüfen, ob der Import erfolgreich verlaufen ist. Wenn dies der Fall ist, sollte auf der ICP-Seite "Katalog" die Kachel **ibm-blockchain-platform-prod** oder **ibm-blockchain-platform-dev** angezeigt werden.


## Voraussetzungen für Podsicherheitsrichtlinie (PodSecurityPolicy)

Nachdem Sie das Helm-Diagramm in {{site.data.keyword.cloud_notm}} Private importiert haben, müssen Sie vor der Installation der Komponenten eine [Podsicherheitsrichtlinie (PodSecurityPolicy) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Pod Security Policies") an den Zielnamensbereich binden. Wählen Sie entweder eine vordefinerte Podsicherheitsrichtlinie aus oder lassen Sie von Ihrem Clusteradministrator eine Podsicherheitsrichtlinie erstellen:
- Name der vordefinierten Podsicherheitsrichtlinie (PodSecurityPolicy): [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- Definition einer angepassten Podsicherheitsrichtlinie:
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- Angepasste Clusterrolle (ClusterRole) für die angepasste Podsicherheitsrichtlinie (PodSecurityPolicy):
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- Angepasste Clusterrollenbindung (ClusterRoleBinding) für die angepasste Clusterrolle (ClusterRole):
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## Einzelne Komponenten bereitstellen

Klicken Sie nach der Installation des Helm-Diagramms in Ihrem ICP-Katalog auf die Kachel **ibm-blockchain-platform-prod** oder **ibm-blockchain-platform-dev**, um sie zu öffnen. Auf der Konfigurationsseite können Sie jede gewünschte Komponente Ihres Blockchain-Netzes einzeln bereitstellen. Weitere Informationen zu den Komponenten, die für Ihre Blockchain-Lösung erforderlich sind, und zu der Reihenfolge, in der sie bereitgestellt werden müssen, enthält das Handbuch [{{site.data.keyword.blockchainfull_notm}} Platform for ICP - Bereitstellung](/docs/services/blockchain/ibp_for_icp_deployment_guide.html).

Stellen Sie anschließend die einzelnen Komponenten bereit:

- Falls Sie einen Anordnungsknoten bereitstellen wollen, müssen Sie zunächst eine Zertifizierungsstelle für den Anordnungsknoten einrichten. Die Zertifizierungsstelle generiert Zertifikate, die von anderen Komponenten in Ihrer Organisation verwendet werden. Weitere Informationen enthält der Abschnitt [Zertifizierungsstelle für {{site.data.keyword.blockchainfull_notm}} Platform in ICP bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html). Anschließend können Sie den Anordnungsknoten bereitstellen, der als allgemeine Bindung des Netzes dient. Weitere Informationen finden Sie unter [Anordnungsknoten von {{site.data.keyword.blockchainfull_notm}} Platform in ICP bereitstellen](/docs/services/blockchain/howto/orderer_deploy_icp.html).

- Falls Sie einen Peer bereitstellen wollen, müssen Sie zunächst eine Zertifizierungsstelle für den Peer einrichten. Die Zertifizierungsstelle generiert Zertifikate, die vom Peer verwendet werden. Weitere Informationen enthält der Abschnitt [Zertifizierungsstelle für {{site.data.keyword.blockchainfull_notm}} Platform in ICP bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html). Sobald Sie anschließend bereit dafür sind, an einem Netz teilzunehmen, können Sie die Peers bereitstellen, die Kanälen beitreten, Transaktionen zulassen und Ihre Daten speichern. Weitere Informationen finden Sie abhängig von dem Blockchain-Netz, an dem der Peer teilnehmen soll, im Abschnitt [{{site.data.keyword.blockchainfull_notm}}-Peer in ICP bereitstellen](/docs/services/blockchain/howto/peer_deploy_icp.html) bzw. [{{site.data.keyword.blockchainfull_notm}}-Peer für Starter Plan- oder Enterprise Plan-Netz bereitstellen](/docs/services/blockchain/howto/peer_deploy_ibp.html).

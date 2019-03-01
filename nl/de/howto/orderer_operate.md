---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Anordnungsknoten in {{site.data.keyword.cloud_notm}} Private betreiben
{: #icp-orderer-operate}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Nachdem Sie den Anordnungsknoten für {{site.data.keyword.blockchainfull}} Platform in {{site.data.keyword.cloud_notm}} Private installiert haben, wird eine Konfigurationsübersicht mit Standardeinstellungen für Umgebungsvariablen erstellt. Danach können Sie Umgebungsvariablen für den Anordnungsknoten ändern oder hinzufügen, um sein Verhalten anzupassen.

Administratoren von Anordnungsknoten sind generell für das Bootstrapping und die Wartung von Anordnungsknoten zuständig. In einer SOLO-Bereitstellung, bei der es nur einen einzigen Anordnungsknoten gibt, ist dies jedoch nicht erforderlich. Bei einer SOLO-Bereitstellung ist der Administrator des Anordnungsknotens dafür zuständig, neue Organisationen zum Systemkanal des Anordnungsknotens hinzuzufügen.

## Voraussetzungen
{: #icp-orderer-operate-prerequisites}

Sie müssen einige vorausgesetzte Schritte über den {{site.data.keyword.cloud_notm}} Private-Cluster ausführen, um Ihren Anordnungsknoten betreiben zu können.

**Voraussetzungen:**
- [CLIs konfigurieren](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)
- [Endpunktinformationen für den Anordnungsknoten abrufen](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
- [TLS-Zertifikat des Anordnungsknotens herunterladen](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)

Anschließend können Sie Ihren Anordnungsknoten mithilfe der Anweisungen in diesem Abschnitt betreiben. Bitte beachten Sie, dass der Anordnungsknoten über die Befehlszeile betrieben wird, was einen Abruf der Binärdatei für die CLI `peer` erforderlich macht. Die Binärdatei für die CLI `peer` wird zusammen mit den anderen Binärdateien heruntergeladen, z. B. `configtxlator`. Daher beginnen viele Befehle in diesem Abschnitt mit dem Wort "peer". Dies bedeutet nicht, dass der Peer verwendet wird, sondern ist lediglich der Name der Binärdatei.

### CLIs konfigurieren
{: #icp-orderer-operate-kubectl-configure}

Sie müssen mit dem Befehlszeilentool **kubectl** eine Verbindung zu dem in {{site.data.keyword.cloud_notm}} Private ausgeführten Container für den Anordnungsknoten herstellen.

1. Melden Sie sich bei der Benutzerschnittstelle Ihres {{site.data.keyword.cloud_notm}} Private-Clusters an. Navigieren Sie zur Registerkarte **Befehlszeilentools** und klicken Sie auf **Cloud Private-CLI**. Daraufhin werden die folgenden Tools angezeigt, die Sie herunterladen können.

   * IBM Cloud Private-CLI und Plug-ins installieren
   * CLI "kubectl" installieren
   * Helm installieren
   * Istio-CLI installieren

  Zum Betrieb von Anordnungsknoten benötigen Sie die ersten **drei** Tools, wobei Helm optional ist. Klicken Sie auf jedes Tool und führen Sie die `curl`-Befehle für den von Ihnen verwendeten Maschinentyp aus. Geben Sie anschließend für jedes Tool die Befehle `chmod` und `sudo mv` aus. Der Befehl `chmod` ändert die Berechtigung der entsprechenden CLI, damit sie ausführbar wird; der Befehl `sudo mv` versetzt die Datei und benennt sie um.

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

  Falls der Befehl erfolgreich ausgeführt wurde, sollte er eine Antwort zurückgeben, die dem folgenden Beispiel ähnelt, wobei `orderer-6cf85f6687-hcxpl` der Name des Containers für Anordnungsknoten ist.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Nun können Sie das Tool **kubectl** verwenden, um die Endpunktinformationen für den Anordnungsknoten abzurufen.

3. Falls Sie **Helm** verwenden wollen, führen Sie optional ein paar weitere Schritte aus. Bitte beachten Sie, dass die Installation von Helm optional ist und Helm in den vorliegenden Anweisungen nicht verwendet werden muss. Es kann jedoch zur Verwaltung Ihrer Helm-Releases und zur Erstellung eigener Archive für die Bereitstellung in {{site.data.keyword.cloud_notm}} Private von Nutzen sein.

  1. Klicken Sie auf "Helm installieren" und führen Sie den Befehl `curl` über die {{site.data.keyword.cloud_notm}} Private-Benutzerschnittstelle aus.
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

### Endpunktinformationen des Anordnungsknotens abrufen
{: #icp-orderer-operate-orderer-endpoint}

Um Aktualisierungen am Systemkanal des Anordnungsknotens vorzunehmen, müssen Sie den Endpunkt des Anordnungsknotens als Ziel angeben. Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um die folgenden Schritte ausführen zu können:

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**.
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Der Abschnitt **Hinweise** enthält eine Reihe von Befehlen, die Sie beim Betrieb der Bereitstellung Ihres Anordnungsknotens unterstützen.
5. Befolgen Sie, sofern noch nicht geschehen, die Anweisungen zum Konfigurieren der [CLI "kubectl"](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure). Sie benötigen sie, um mit dem Container für Anordnungsknoten zu interagieren.
6. Führen Sie in der CLI den ersten Befehl in den Hinweisen aus, der nach **1. Rufen Sie die Anwendungs-URL mit den folgenden Befehlen ab:** angegeben ist. Dieser Befehl gibt die URL und den Port für den Anordnungsknoten aus, der ähnlich wie im folgenden Beispiel lautet. Speichern Sie diese URL; Sie benötigen sie in späteren Befehlen, um die Proxy-Adresse und den externen Knotenport festzulegen.

  ```
  http://9.30.94.174:30159
  ```

In diesem Beispiel ist `9.30.94.174` die Proxy-IP-Adresse; der externe Knotenport, der 7050 entspricht, ist `30159`.  

**Hinweis:** Wenn Sie Ihren Anordnungsknoten hinter einer Firewall bereitstellen, müssen Sie einen Durchgriff öffnen, damit das Netz auf der Plattform diese Task ausführen kann.

### TLS-Zertifikat des Anordnungsknotens herunterladen
{: #icp-orderer-operate-tls-cert}

Sie müssen das TLS-Zertifikat Ihres Anordnungsknotens herunterladen und es an Ihre Befehlen übergeben, damit Sie von einem fernen Client aus mit Ihrem Anordnungsknoten kommunizieren können. Sie müssen die Berechtigung eines [Clusteradministrators ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Clusteradministratorrolle und -aktionen") besitzen, um die folgenden Schritte ausführen zu können:

1. Melden Sie sich bei der {{site.data.keyword.cloud_notm}} Private-Konsole an und klicken Sie auf das **Menüsymbol** in der linken oberen Ecke.
2. Klicken Sie auf **Workload** > **Helm-Releases**.
3. Suchen Sie nach dem Namen Ihres Helm-Release und öffnen Sie die Anzeige mit den Details des Helm-Release.
4. Blättern Sie bis zum Abschnitt **Hinweise** vor, der sich am Ende der Anzeige befindet. Der Abschnitt **Hinweise** enthält eine Reihe von Befehlen, die Sie beim Betrieb der Bereitstellung Ihres Anordnungsknotens unterstützen.
5. Befolgen Sie, sofern noch nicht geschehen, die Anweisungen zum Konfigurieren der [CLI "kubectl"](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure). Sie benötigen sie, um mit dem Container für Anordnungsknoten zu interagieren.
6. Führen Sie in der CLI den dritten Befehl im Hinweis aus, der auf **3. TLS-Stammzertifikatdatei für Anordnungsknoten abrufen:** folgt.  Mit diesem Befehl wird das TLS-Zertifikat als Datei `cert.pem` auf Ihrer lokalen Maschine gespeichert.

  **Hinweis:** Vor der Ausführung der Befehle können Sie `app=orderer` wie im nachfolgenden Befehl gezeigt entfernen:

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. Versetzen Sie das generierte Zertifikat an eine Position, unter der Sie in künftigen Befehlen darauf verweisen können, und benennen Sie es in `orderertls.pem` um.

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### Zertifikate auf dem lokalen System verwalten
{: #manage-certs}

Wechseln Sie in das Verzeichnis, in dem der MSP-Ordner für den Administrator des Anordnungsknotens generiert wurde. Abhängig davon, wie Sie die Beispielschritte in dieser Dokumentation befolgt haben oder wie viele Komponenten Sie bereitstellen, finden Sie den MSP-Ordner unter `$HOME/fabric-ca-client/orderer-admin/msp` oder `$HOME/fabric-ca-client/peer-admin/msp`.

Bevor Sie den Anordnungsknoten betreiben können, müssen Sie einige Management-Tasks für die Zertifikate auf Ihrer lokalen Maschine ausführen. Außerdem müssen Sie sicherstellen, dass Sie vom Anordnungsknoten aus auf die TLS-Zertifikate zugreifen können. Weitere Angaben über die zu verwendenden Zertifikate enthält der Abschnitt [Membership Service Providers (MSPs)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) unter [Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private betreiben](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

1. Versetzen Sie das signCert-Zertifikat für den Administrator des Anordnungsknotens in einen neuen Ordner namens `admincerts`:

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Stellen Sie sicher, dass Sie das [TLS-Zertifikat des Anordnungsknotens heruntergeladen](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert) haben und in der Befehlszeile darauf verweisen können. Wenn Sie die Beispielbefehle in dieser Dokumentation befolgt haben, befindet sich das TLS-Zertifikat in der Datei `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`.

Durch einen Befehl "tree" können Sie prüfen, ob Sie diese Schritte ausgeführt haben. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Zertifikate gespeichert haben. Der Befehl "tree" sollte ein Ergebnis ähnlich der folgenden Struktur generieren:
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
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
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

## Organisationen zum Systemkanal des Anordnungsknotens hinzufügen
{: #icp-orderer-operate-add-organizations-to-consortium}

**Hinweise:** Falls Sie einen Peer bereitstellen und mit einem Starter Plan- oder Enterprise Plan-Netz verbinden wollen, können Sie diesen Schritt überspringen.

Wenn Sie einen Anordnungsknoten mithilfe des Helm-Diagramms für {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private bereitstellen, wird der Genesis-Block automatisch erstellt und enthält die Organisation des Anordnungsknotens als einzigen Administrator für den Systemkanal. Dies bedeutet, dass ausschließlich der Administrator für die Organisation des Anordnungsknotens in der Lage ist, Organisationen zum Konsortium hinzuzufügen. Die Richtlinie, die dies steuert, ist unter dem Namen `mod_policy` für den Systemkanal des Anordnungsknotens bekannt und sollte nicht geändert werden, nachdem neue Organisationen zum Systemkanal des Anordnungsknotens hinzugefügt wurden.

Durch das Hinzufügen von Organisationen zum Systemkanal des Anordnungsknotens werden diese zu einem Teil des so genannten "Konsortiums", also der Liste der Mitglieder, die standardmäßig Kanäle erstellen können. Ein Mitglied des Konsortiums kann unter Verwendung der Zertifikate und Informationen, die im Systemkanal aufgeführt sind, außerdem einfach zu einem Kanal hinzugefügt werden.

Die Aktualisierung des Systemkanals des Anordnungsknotens wird durch Transaktionen für Konfigurationsaktualisierungen erreicht, bei denen die relevanten MSP-Informationen der Organisationen zur Kanalkonfiguration für den Systemkanal des Anordnungsknotens hinzugefügt werden.

Das Hinzufügen von Organisationen zum Systemkanal des Anordnungsknotens folgt im Wesentlichen demselben Ablauf wie das Aktualisieren der Konfiguration eines beliebigen Kanals zum Hinzufügen einer Organisation. Sie müssen jedoch einige wenige Änderungen vornehmen, da der zu aktualisierende Kanal kein Anwendungskanal und der relevante Administrator der Administrator des Anordnungsknotens und nicht einer Peerorganisation ist.

Sie können übrigens eine Organisation zu einem Kanal hinzufügen, ohne zuvor dem Systemkanal beitreten zu müssen. Weitere Informationen finden Sie in der Dokumentation zu Hyperledger Fabric im Lernprogramm [Adding an Org to a Channel Tutorial ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html "Adding an org to a channel") .

In der folgenden Liste sind die allgemeinen Schritte angegeben; ausgeführt werden die Tasks durch unterschiedliche Gruppen von Organisationen in Ihrem Konsortium.

1. Jede Organisation, die dem Konsortium beitreten soll, muss eine [Organisationsdefinition vorbereiten](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).
2. Der Administrator für die Organisation des Anordnungsknotens muss das Konsortium [gründen](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium), indem er Organisationen zum Systemkanal des Anordnungsknotens hinzufügt.
3. Jede Organisation im Konsortium kann einen [neuen Kanal erstellen](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx), indem sie eine Transaktion für die Kanalkonfiguration vorbereitet.

## Fabric-Tools abrufen
{: #icp-orderer-operate-get-fabric-tools}

Zum Aktualisieren des Systemkanals müsssen Sie die folgenden Tools von Hyperledger Fabric herunterladen.
- [peer ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html): Damit können Sie den Genesis-Block abrufen und den Systemkanal aktualisieren.
- [configtxlator ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html): Dieses Tool setzt das Protobuf-Format einer Kanalkonfiguration in das JSON-Format um, das einfacher zu lesen und zu aktualisieren ist.

1. Legen Sie fest, wo Sie die Tools speichern wollen, und führen Sie dann den folgenden Befehl aus:

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. Legen Sie als Pfad zu den Tools die Position fest, an die die obigen Fabric-Tools heruntergeladen wurden:

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  Falls Sie die Binärdateien beispielsweise in Ihrem Ausgangsverzeichnis installiert haben, würden Sie `PATH` wie folgt festlegen:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Beim Download der Binärdateien wird ein Konfigurationsordner erstellt, der eine Datei "core.yaml", "orderer.yaml" und "configtx.yaml" enthält. Legen Sie den Fabric-Konfigurationspfad zu diesem Konfigurationsverzeichnis fest:

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  Beispiel:
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## Organisationsdefinition erstellen
{: #icp-orderer-operate-org-definition}

Die **Definition** einer Organisation enthält den Organisationsnamen (MSP-ID) und die entsprechenden Zertifikate. Der Systemkanal und die Anwendungskanäle verwenden diese Definition, um Ihre Organisation in die Richtlinien aufzunehmen, die Erstellung, Aktualisierungen und Transaktionsbewilligung von Kanälen steuern. Dieser Schritt muss von jeder Organisation ausgeführt werden, die Mitglied eines Konsortiums werden möchte. Weitere Informationen finden Sie unter [Organisationsdefinition vorbereiten](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).

## Konsortium gründen
{: #icp-orderer-operate-consortium}

Die Gründung eines Konsortiums vollzieht sich allgemein wie folgt:

1. Der Systemkanal des Anordnungsknotens wird mit der Organisation des Anordnungsknotens als einzigem Mitglied des Systemkanals gegründet. Diese Organisation kann Aktualisierungen vornehmen, ohne dass zusätzliche Signaturen erforderlich sind.
2. Der Administrator für die Organisation des Anordnungsknotens empfängt Organisationsdefinitionen von Mitgliedern, die dem Konsortium beitreten möchten. Die Organisation des Anordnungsknotens aktualisiert mithilfe der Organisationsdefinitionen die Konfiguration für den Systemkanal des Anordnungsknotens.

### Organisationsdefinitionen abrufen

Der Anordnungsknoten muss die [Organisationsdefinitionen](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) von Mitgliedern empfangen, die dem Konsortium beitreten wollen. Dies muss in einer externen Operation mit anderen Mitgliedern erfolgen, von denen die JSON-Dateien gesendet werden, die deren MSP-ID und Verschlüsselungsmaterial enthalten. In den nachfolgenden Befehlen wird davon ausgegangen, dass Sie einen Ordner namens `org-definitions` erstellt und alle relevanten Dateien in diesem Verzeichnis abgelegt haben.

### Genesis-Block des Systemkanals abrufen

1. Legen Sie unter Verwendung des MSP-Namens für die Organisation Ihres Anordnungsknotens, der während der Bereitstellung eingerichtet wird, die folgenden Umgebungsvariablen, den Pfad zum MSP Ihres Anordnungsknotens und den Pfad zum TLS-Zertifikat der Zertifizierungsstelle für Ihren Anordnungsknoten fest.

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  Ersetzen Sie die Feldinhalte durch Ihre eigenen Angaben.

    - Ersetzen Sie `<CORE_PEER_LOCALMSPID>` durch die MSP-ID für die Organisation Ihres Anordnungsknotens. Diese ist auch innerhalb des Containers für Anforderungsknoten sichtbar, wenn Sie die folgenden Befehle ausführen und hierbei `<orderer pod name>` durch den Wert für den Pod Ihres Anordnungsknotens ersetzen:

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      Die Ausgabe könnte wie folgt aussehen:

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      Der Wert für `CORE_PEER_LOCALMSPID` lautet somit "ordererOrg".

    - Ersetzen Sie `<CORE_PEER_MSPCONFIGPATH>` durch den Pfad zum MSP-Ordner des Administrators für die Organisation des Anordnungsknotens.
    - Ersetzen Sie `<CORE_PEER_TLS_ROOTCERT_FILE>` durch den Pfad zum TLS-Zertifikat der Zertifizierungsstelle.
    - Ersetzen Sie `<PROXY_IP>` durch die Proxy-IP-Adresse aus den [Endpunktinformationen des Anordnungsknotens](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint).
    - Ersetzen Sie `<EXTERNAL_NODE_PORT>` durch den externen Knotenport aus den [Endpunktinformationen des Anordnungsknotens](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint).

  Ihre Umgebungsvariablen könnten wie im folgenden Beispiel aussehen:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  Diese Umgebungsvariablen können Sie jederzeit durch die Ausgabe der folgenden Befehle prüfen:

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. Legen Sie den Kanalnamen als Umgebungsvariable fest. Der Name für den Systemkanal des Anordnungsknotens lautet stets `test-system-channel-name`.

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. Rufen Sie den Genesis-Block des Systemkanals ab. Erstellen Sie zunächst ein Verzeichnis namens `configupdate` in `org-definitions`, um die Konfigurationsdateien zu speichern, die während des Konfigurationsaktualisierungsprozesses generiert werden. Den Namen dieses Verzeichnisses können Sie frei wählen; in den folgenden Beispielbefehlen wird als Name `configupdate` verwendet.

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  Ersetzen Sie `<orderer_TLS_root_cert_file>` durch den Pfad zur Datei `orderertls.pem`, die Sie in [diesem Schritt](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert) erstellt haben. Ihr Befehl könnte beispielsweise wie folgt lauten:

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Wenn dieser Befehl erfolgreich ausgeführt wird, generiert er die folgende Ausgabe.

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  Den Block `genesis.pb` finden Sie anschließend auch in Ihrem Dateisystem.

### Aktualisierung des Systemkanals erstellen
{: #icp-orderer-operate-system-channel-update}

Das heruntergeladene [Fabric-Tool](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-get-fabric-tools) `configtxtlator` setzt das Protobuf-Format einer Kanalkonfiguration in das JSON-Format um (und umgekehrt).

Diese Schritte folgen dem allgemeinen Ablauf des Lernprogramms zur Kanalaktualisierung zum [Konvertieren des Genesis-Blocks in das JSON-Format ![External link icon](../images/external_link.svg "External link icon")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down "Convert the Configuration to JSON and Trim It Down"). Sie müssen einige Änderungen an den Befehlen im Lernprogramm vornehmen, um die Tatsache zu berücksichtigen, dass Sie den Kanal des Anordnungsknotens und nicht einen Anwendungskanal aktualisieren. Weitere Details zu diesem Prozess können Sie dem Lernprogramm entnehmen. Im vorliegenden Abschnitt sind lediglich die von Ihnen auszuführenden Befehle angegeben.

1. Kopieren Sie die JSON-Datei mit der Organisationsdefinition aus dem Ordner, in dem Sie [Ihre Organisation erstellt haben](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition), in den Ordner `configupdate`. Im folgenden Beispielbefehl heißt die JSON-Datei mit der Organisationsdefinition `org1definition.json`:

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. Konvertieren Sie den Genesis-Block in das JSON-Format. Führen Sie die folgenden Befehle aus:

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. Führen Sie den folgenden Befehl aus, um das Verschlüsselungsmaterial einer Organisation zur Konfiguration des Konsortiums hinzuzufügen. Ersetzen Sie < NEWORGMSP> durch die MSP-ID der [Organisation, die Sie erstellt haben](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  Der Befehl könnte wie im folgenden Beispiel aussehen:

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. Wiederholen Sie diesen Befehl für jede Organisation, die dem Konsortium beitreten soll. Achten Sie darauf, die Datei `./orgdefinition.json` in die neue JSON-Datei für die Organisation zu ändern.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. Führen Sie die folgenden Befehle aus, um die Datei `modified_config.json` in die Konfigurationsaktualisierung im JSON-Format zu konvertieren.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. Nachdem Sie diese Befehle ausgeführt haben, schließen Sie `config_update` in ein übergeordnetes Element ein.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. Führen Sie den folgenden Befehl aus, um die Konfigurationsaktualisierung zurück in das Protobuf-Format zu konvertieren:

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### Aktualisierung an den Systemkanal senden

Stellen Sie vor Ausführung der folgenden Schritte sicher, dass `FABRIC_CFG_PATH`, `$PROXY` und `ORDERER_PORT` korrekt festgelegt sind. Führen Sie den folgenden Befehl aus:

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
```
{:codeblock}

Ihre Umgebungsvariablen sollten dem folgenden Beispiel ähneln:

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

Nachdem Sie die Kanalkonfigurationsaktualisierung (`config_update_in_envelope.json`) erstellt haben, kann die Organisation des Anordnungsknotens die Kanalaktualisierung mit dem folgenden Befehl übergeben:

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

Mit diesem Befehl wird die Aktualisierungsanforderung gleichzeitig signiert und an den Anordnungsknoten gesendet- Da die Organisation des Anordnungsknotens der einzige Administrator des Systemkanals ist, wird für die Gültigkeit dieser Aktualisierungsanforderung nur eine einzige Signatur benötigt. Falls der Systemkanal erfolgreich aktualisiert wurde, wird die folgende Ausgabe angezeigt:

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

Es ist zwar möglich, mehrere Organisationsdefinitionen in eine einzige Konfigurationsaktualisierung für den Systemkanal des Anordnungsknotens einzubeziehen, aber es hat sich das Verfahren bewährt, den Kanal jeweils nur mit einer Organisation zu aktualisieren und anschließend zu prüfen, ob die Aktualisierung erfolgreich war.

## Protokolle des Anordnungsknotens anzeigen
{: #icp-orderer-operate-orderer-view-logs}

Komponentenprotokolle können über die Befehlszeile mit den [`Befehlen der CLI "kubectl"`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) oder über [Kibana ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/products/kibana "Your Window into the Elastic Search") angezeigt werden, das in Ihrem {{site.data.keyword.cloud_notm}} Private-Cluster enthalten ist. 

- Mit dem Befehl `kubectl logs` können Sie die Containerprotokolle innerhalb des Pods anzeigen. Falls Sie den Podnamen nicht genau kennen, führen den folgenden Befehl aus, um Ihre Podliste anzuzeigen.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Rufen Sie anschließend mit dem folgenden Befehl die Protokolle des Containers für Anordnungsknoten ab, der sich im Pod befindet; ersetzen Sie hierbei `<pod_name>` durch den Namen Ihres Pods aus der obigen Befehlsausgabe:

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  Weitere Informationen zum Befehl `kubectl logs` enthält die [Kubernetes-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”).

- Alternativ können Sie auf die Protokolle zugreifen, indem Sie die [{{site.data.keyword.cloud_notm}} Private Cluster-Managementkonsole ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html) verwenden, die die Protokolle in Kibana öffnet.

  **Hinweis:** Wenn Sie Ihre Protokolle in Kibana anzeigen, empfangen Sie möglicherweise die Antwort `Keine Ergebnisse gefunden`. Diese Bedingung kann auftreten, wenn {{site.data.keyword.cloud_notm}} Private die IP-Adresse Ihres Workerknotens als Hostnamen verwendet. Entfernen Sie zur Lösung dieses Problems oben in der Anzeige den Filter, der mit `node.hostname.keyword` beginnt. Anschließend sind die Protokolle sichtbar.

## Fehlerbehebung
{: #icp-orderer-operate-troubleshooting}

### **Problem:** Befehl `peer channel update` schlägt mit einem Fehler fehl
{: #icp-orderer-operate-peer-channel-update-error}

Möglicherweise empfangen Sie bei der Ausführung eines Befehls `peer channel update` den folgenden Fehler:

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

Dieser Fehler kann dadurch versursacht werden, dass der Peer dieselbe MSP-ID für die Organisation wie der Anordnungsknoten verwendet.  

**Lösung:**  

Zur Lösung dieses Problems müssen Sie Ihren Peer erneut bereitstellen und hierbei eine MSP-ID für die Organisation angeben, die sich von der bei der Bereitstellung des Anordnungsknotens verwendeten MSP-ID unterscheidet.

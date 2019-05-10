---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Tools für Smart Contracts
{: #develop-vscode}

Die {{site.data.keyword.blockchainfull}} Platform-Erweiterung für Visual Studio Code stellt innerhalb von Visual Studio Code eine Umgebung für die Entwicklung, Erstellung und Bereitstellung von Smart-Contract-Paketen zur Verfügung. Die Erweiterung enthält außerdem Befehle, mit denen eine vorkonfigurierte Instanz von Hyperledger Fabric für eine vereinfachte lokale Smart-Contract-Entwicklung eingerichtet werden kann.

**Hinweis:** Die {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung ist mit Starter Plan-Netzen von {{site.data.keyword.blockchainfull_notm}} Platform und mit Hyperledger Fabric 1.3.x und höher kompatibel.

## Voraussetzungen
{: #develop-vscode-prerequisites}

Vor der Installation der {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung für Visual Studio Code müssen die folgenden vorausgesetzten Schritte ausgeführt worden sein.

- **Installation von Yeoman**

  Yeoman ist ein Generatortool, das zum Erstellen von Gerüstprojekten für Smart Contracts verwendet wird. Installieren Sie Yeoman mit dem folgenden Befehl: `npm install -g yo`

- **Installation des Fabric-Generators für Yeoman**

  Installieren Sie nach der Installation von Yeoman den Fabric-Generator für die Erstellung von Gerüstpaketen für Smart Contracts. Führen Sie zum Installieren des Fabric-Generators den Befehl `npm install -g generator-fabric` aus.

- **Installation von Docker**

  Damit die vorkonfigurierte Instanz von Hyperledger Fabric ausgeführt werden kann, müssen Sie sicherstellen, dass [Docker ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.docker.com/) installiert ist.

- **Betriebssystemvoraussetzungen**

  Diese Erweiterung ist gegenwärtig mit Mac, Windows und Linux kompatibel.

- **Versionsvoraussetzungen für Hyperledger Fabric**

  Diese Erweiterung ist mit Hyperledger Fabric Version 1.3.0 und höher kompatibel.

- **Versionsvoraussetzungen für Hyperledger Fabric**

  Diese Erweiterung ist mit Hyperledger Fabric Version 1.3.0 und höher kompatibel.

## Erweiterung installieren
{: #develop-vscode-installing-the-extension}

1. Navigieren Sie zur [Seite mit dem Marktplatz für Visual Studio Code-Erweiterungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) oder suchen Sie in der Visual Studio Code-Anzeige für Erweiterungen nach **{{site.data.keyword.blockchainfull_notm}} Platform**.
2. Klicken Sie auf **Installieren**.
3. Starten Sie Visual Studio Code erneut, um die Installation der Erweiterung vollständig abzuschließen.

## Befehlspalette von Visual Studio Code
{: #develop-vscode-command-palette}

Diese Erweiterung fügt viele Befehle zur Befehlspalette von Visual Studio Code hinzu. Viele der im vorliegenden Dokument ausführlich beschriebenen Operationen können auch durch eine Ausführung des Befehls in der Befehlspalette von Visual Studio Code gestartet werden.

## Projekt erstellen
{: #develop-vscode-creating-a-project}

Führen Sie zum Erstellen eines neuen Projekts die folgenden Schritte aus:

1. Öffnen Sie die Befehlspalette und führen Sie den Befehl **Blockchain: Smart-Contract-Projekt erstellen** aus.
2. Wählen Sie die Sprache aus, in der Sie einen Smart Contract erstellen wollen; gegenwärtig stehen als Optionen JavaScript oder TypeScript zur Verfügung.
3. Erstellen Sie einen Ordner mit dem Namen Ihres Projekts und öffnen Sie ihn.
4. Wählen Sie aus, wie das neue Projekt geöffnet werden soll. Der Projektordner sollte jetzt geöffnet sein.

## Verbindung zu einem Netz herstellen und trennen
{: #develop-vscode-connecting-and-disconnecting}

Bei der Verwendung dieser Erweiterung installieren und instanziieren Sie Smart-Contract-Pakete auf Peers und Kanälen in Ihrer Hyperledger Fabric-Instanz. Diese Erweiterung kann mithilfe von Docker eine vorkonfigurierte lokale Instanz von Hyperledger Fabric initialisieren.

### Verbindung zur vorkonfigurierten Hyperledger Fabric-Instanz herstellen
{: #develop-vscode-connecting-to-preconfigured-Fabric-instance}

Vergewissern Sie sich beim Herstellen einer Verbindung zur vorkonfigurierten Hyperledger Fabric-Instanz zunächst, ob Docker auf Ihrer Maschine aktiv ist:

1. Öffnen Sie in Visual Studio Code die Registerkarte für **{{site.data.keyword.blockchainfull_notm}} Platform**.
2. Klicken Sie in der Anzeige **{{site.data.keyword.blockchainfull_notm}} Platform** auf  **local_fabric**. Falls Docker aktiv ist, sollte die lokale Hyperledger Fabric-Instanz heruntergeladen und gestartet werden.
3. Nachdem die lokale Hyperledger Fabric-Instanz gestartet worden ist, doppelklicken Sie auf **local_fabric**, um eine Verbindung herzustellen. Es sollte jetzt ein Kanal namens `mychannel` angezeigt werden.

#### Vorkonfigurierte Hyperledger Fabric-Laufzeit erneut starten
{: #develop-vscode-restarting-Fabric-runtime}

So starten Sie die Laufzeit für `local_fabric` erneut:

1. Klicken Sie mit der rechten Maustaste auf die Verbindung `local_fabric`, nachdem diese eingerichtet worden ist.
2. Wählen Sie die Option **Fabric-Laufzeit erneut starten** aus.

Die Hyperledger Fabric-Container werden daraufhin gestoppt und erneut gestartet.

#### Hyperledger Fabric-Laufzeit umrüsten
{: #develop-vscode-teardown-Fabric}

So rüsten Sie die Laufzeit für `local_fabric` um:

1. Klicken Sie mit der rechten Maustaste auf die Verbindung `local_fabric`, nachdem diese eingerichtet worden ist.
2. Wählen Sie die Option **Fabric-Laufzeit umrüsten** aus.

Bei einer Umrüstung des Netzes für `local_fabric` werden alle Hyperledger Fabric-Container geschlossen. **Hinweis:** Hierdurch gehen die Ledgerdaten und die Daten für den World-Status verloren.

#### Entwicklungsmodus für die vorkonfigurierte Hyperledger Fabric-Laufzeit aktivieren
{: #develop-vscode-enabling-development-mode-Fabric}

Beim normalen Betrieb wird durch einen Peer ein Chaincode-Container erstellt und verwaltet, um instanziierte Smart Contracts auszuführen. Ein Wechsel in den Entwicklungsmodus bewirkt, dass der Peer die manuelle Ausführung des Chaincode-Containers zulässt. Bei einer manuellen Ausführung des Chaincode-Containers in der Befehlszeile oder in einem Terminal besteht eine Unterstützung für die iterative Entwicklung und Fehlerbehebung von Smart Contracts.

So aktivieren Sie den Entwicklungsmodus für die vorkonfigurierte Hyperledger Fabric-Laufzeit:

1. Klicken Sie mit der rechten Maustaste auf die Verbindung `local_fabric`, nachdem diese eingerichtet worden ist.
2. Wählen Sie die Option **Entwicklungsmodus ein-/ausschalten** aus.

Der Entwicklungsmodus muss aktiviert sein, damit die Debugfunktion der Erweiterung verwendet werden kann.

### Verbindung zu Starter Plan von {{site.data.keyword.blockchainfull_notm}} Platform herstellen
{: #develop-vscode-connecting-to-Starter-Plan}

Mit der {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung für Visual Studio Code kann eine Verbindung zu einer Starter Plan-Instanz von {{site.data.keyword.blockchainfull_notm}} Platform hergestellt werden. Nachdem die Verbindung aufgebaut wurde, können Sie mithilfe der Erweiterung Smart Contracts entwickeln und bereitstellen.

**Hinweis:** Mit dem Enterprise Plan von  {{site.data.keyword.blockchainfull_notm}} Platform ist die Erweiterung gegenwärtig nicht kompatibel.

1. alls Sie keine Starter Plan-Instanz von {{site.data.keyword.blockchainfull_notm}} Platform verfügen können Sie eine [erstellen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod).
2. Öffnen Sie die {{site.data.keyword.blockchainfull_notm}} Platform-Benutzerschnittstelle, indem Sie auf **Starten** klicken.
3. Klicken Sie auf **Übersicht** > **Verbindungsprofil** > **Herunterladen**, um Ihr Verbindungsprofil abzurufen.
4. Generieren Sie die erforderlichen Administratorzertifikate; klicken Sie hierzu auf **Zertifizierungsstelle** > **Zertifikat generieren** und speichern Sie anschließend das Zertifikat und den privaten Schlüssel in Ihrem Dateisystem.
5. Fügen Sie die Zertifikate zu Ihrer Starter Plan-Instanz von  {{site.data.keyword.blockchainfull_notm}} Platform hinzu, indem Sie auf **Mitglieder** > **Zertifikate** > **Zertifikat hinzufügen** klicken und das im vorherigen Schritt erstellte Zertifikat auswählen.
6. Öffnen Sie in Visual Studio Code die Ansicht für die {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung und klicken Sie auf **Neue Verbindung hinzufügen**.
7. Geben Sie den Namen der Verbindung und den Pfad zum Verbindungsprofil ein und wählen Sie entweder eine vorhandene [Wallet![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") in Ihrem Dateisystem aus oder erstellen Sie mit einem Zertifikat und einem privaten Schlüssel eine neue Wallet.

### Verbindung zu einer eigenen Hyperledger Fabric-Instanz herstellen
{: #develop-vscode-connecting-to-own-Fabric-instance}

Mithilfe dieser Erweiterung können Sie eine Verbindung zu einer vorkonfigurierten lokalen Hyperledger Fabric-Instanz oder auch zu Ihrer eigenen Hyperledger Fabric-Instanz herstellen. Falls Sie eine Verbindung zu Ihrer eigenen Fabric-Instanz herstellen wollen, muss sie  Fabric 1.3.0 oder höher verwenden.

Führen Sie zum Herstellen einer Verbindung zu Ihrer eigenen Hyperledger Fabric-Instanz die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code die Registerkarte für **{{site.data.keyword.blockchainfull_notm}} Platform**.
2. Klicken Sie in der Anzeige **{{site.data.keyword.blockchainfull_notm}} Platform** auf **Neue Verbindung hinzufügen**.
3. Geben Sie einen Namen für die Verbindung ein. Dieser Name wird in der Anzeige **{{site.data.keyword.blockchainfull_notm}} Platform** angezeigt.
4. Geben Sie den vollständig qualifizierten Dateipfad Ihres Hyperledger Fabric-Verbindungsprofils ein.
5. Wählen Sie entweder das vorhandene Dateisystem [Wallet ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet")aus, oder erstellen Sie ein neues Dateisystem "Wallet", indem Sie die Dateipfade des Zertifikats und des privaten Schlüssels für Ihre Fabric-Identität eingeben.
6. Ihre Verbindung sollte unter `local_fabric` in der Verbindungsliste angezeigt werden. Doppelklicken Sie auf den Verbindungsnamen, um eine Verbindung herzustellen.

Falls noch keine Hyperledger Fabric-Instanz der Version 1.3.0 oder höher vorhanden ist, können Sie zum Herstellen der Verbindung die vorkonfigurierte Hyperledger Fabric-Instanz verwenden oder die folgenden Befehle ausführen, um die Images für Version 1.3.0 mit dem Befehl "pull" zu aktualisieren und mit dem Befehl "tag" Tags für das aktualisierte Image hinzuzufügen:

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### Verbindung zu einem Netz trennen
{: #develop-vscode-disconnecting}

Wenn Sie mit einem Netz verbunden sind, können Sie die Verbindung schließen, indem Sie in der rechten oberen Ecke des Fensters "Blockchain-Verbindungen" auf das Symbol "Trennen" klicken. Zum Trennen der Verbindung können Sie auch den Befehl **Von Blockchain trennen** in der Befehlspalette ausführen.

## Verbindung bearbeiten oder löschen
{: #develop-vscode-editing-or-deleting-connection}

Verbindungen können im Fenster "Verbindungen" bearbeitet oder gelöscht werden.

### Verbindung bearbeiten
{: #develop-vscode-editing-connection}

Durch die Bearbeitung einer Verbindung können Sie den Dateipfad für das Verbindungsprofil, den Namen der Verbindung und die Dateipfade für Identitätszertifikate und private Schlüssel ändern.

So bearbeiten Sie eine Verbindung:

1. Klicken Sie in der linken unteren Ecke der Erweiterung mit der rechten Maustaste auf die Verbindung, die Sie bearbeiten möchten. Daraufhin wird ein Kontextmenü mit Optionen zum Hinzufügen einer Identität sowie zum Bearbeiten und zum Löschen der Verbindung geöffnet.
2. Wählen Sie **Verbindung bearbeiten** aus.
3. Die Seite **Benutzereinstellungen** wird geöffnet; hierbei sind die Verbindungsdetails hervorgehoben.
4. Nehmen Sie die gewünschten Änderungen vor und speichern Sie die Seite mit den Einstellungen.

### Verbindung löschen
{: #develop-vscode-deleting-connection}

Verbindungen können wie folgt gelöscht werden:

1. Klicken Sie in der linken unteren Ecke der Erweiterung mit der rechten Maustaste auf die Verbindung, die Sie bearbeiten möchten. Daraufhin wird ein Kontextmenü mit Optionen zum Hinzufügen einer Identität sowie zum Bearbeiten und zum Löschen der Verbindung geöffnet.
2. Wählen Sie **Verbindung löschen** aus.
3. Nun wird ein Dialogfeld angezeigt, in dem das Löschen der Verbindung bestätigt werden muss. Klicken Sie auf **Ja**.

Die Verbindung wird gelöscht.

## Identitäten hinzufügen
{: #develop-vscode-adding-identities}

Wenn Sie eine Verbindung hinzufügen, müssen Sie entweder eine vorhandene elektronische Brieftasche (Wallet) mit der Identität auswählen, die verwendet werden soll, oder eine neue elektronische Brieftasche erstellen, indem Sie das Zertifikat und den privaten Schlüssel für eine Identität verwenden. Weitere Informationen zu Wallets finden Sie im [Wallet-Abschnitt ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") in der Fabric-Dokumentation.

So fügen Sie eine Identität zu einer bereits eingerichteten Verbindung hinzu:

1. Klicken Sie in der linken unteren Ecke der Erweiterung mit der rechten Maustaste auf die Verbindung, die Sie bearbeiten möchten. Daraufhin wird ein Kontextmenü mit Optionen zum Hinzufügen einer Identität sowie zum Bearbeiten und zum Löschen der Verbindung geöffnet.
2. Wählen Sie **Identität hinzufügen** aus.
3. Wählen Sie eine vorhandene elektronische Brieftasche (Wallet) aus, die die Identität enthält, die hinzugefügt werden soll, oder erstellen Sie eine neue elektronische Brieftasche, indem Sie die Dateipfade der Zertifikate und des privaten Schlüssels angeben.

## Verbindungen untersuchen
{: #develop-vscode-exploring-connections}

Nachdem Sie eine Verbindung zu einer Hyperledger Fabric-Instanz hergestellt haben, wird im Fenster  **{{site.data.keyword.blockchainfull_notm}} Platform** eine Liste der verfügbaren Kanäle und der Peers in diesen Kanälen angezeigt. Nehmen Sie sich einen Moment Zeit für die Untersuchung Ihrer Hyperledger Fabric-Instanz, um die Struktur kennenzulernen. Zuerst werden die Kanäle in einer Verbindung aufgelistet; unter einem Kanal befindet sich eine Liste der Peers, die Mitglieder dieses Kanals sind, sowie eine Liste der Smart-Contract-Pakete, die auf diesem Kanal instanziiert wurden. Unter jedem Peer in der Liste befindet sich eine Liste aller Smart-Contract-Pakete, die auf diesem Peer installiert sind. Außerdem werden die Versionen der installierten oder instanziierten Smart-Contract-Pakete angezeigt.

### Verbindungsdetails für die vorkonfigurierte Hyperledger Fabric-Laufzeit exportieren
{: #develop-vscode-exploring-connection-details}

Die Verbindungsdetails, die zum Herstellen der Verbindung für `local_fabric` erforderlich sind, können exportiert werden. Die Verbindungsdetails der Verbindung für `local_fabric` sind beim Testen von Clientanwendungen hilfreich, die eine Verbindung zu Ihrer Hyperledger Fabric-Instanz herstellen oder mit ihr interagieren sollen.

So exportieren Sie die Verbindungsdetails für `local_fabric`:

1. Starten Sie die vorkonfigurierte Hyperledger Fabric-Laufzeit.
2. Klicken Sie mit der rechten Maustaste auf die Verbindung `local_fabric` und wählen Sie die Option **Verbindungsdetails exportieren** aus.

Die Verbindungsdetails werden in einem Verzeichnis namens `local_fabric` gespeichert, das in Ihrem aktuellen Projektverzeichnis enthalten ist.

## Smart-Contract-Paket erstellen
{: #packaging-a-smart-contract}

Für einen Smart Contract, der für die Bereitstellung bereit ist, muss zunächst ein Paket erstellt werden. Führen Sie zum Erstellen eines Smart-Contract-Pakets die folgenden Schritte aus:

1. Navigieren Sie in Visual Studio Code zur Anzeige **{{site.data.keyword.blockchainfull_notm}} Platform**.
2. Klicken Sie im Fenster **Smart-Contract-Pakete** auf das Pluszeichen (+). Falls in der Dateianzeigefunktion ein Smart-Contract-Projekt geöffnet ist, wird daraus automatisch ein Paket erstellt und im Fenster **Smart-Contract-Pakete** angezeigt. Sind mehrere Smart-Contract-Ordner geöffnet, wird angefragt, für welchen Ordner das Paket erstellt werden soll. Für den Fall, dass keine Smart-Contract-Ordner geöffnet sind, wird eine Fehlernachricht ausgegeben.

## Smart-Contract-Pakete installieren
{: #develop-vscode-installing-smart-contract-packages}

Nachdem Sie eine Verbindung zu einer Hyperledger Fabric-Instanz hergestellt haben, können Sie Smart-Contract-Pakete auf den Peers installieren und instanziieren.

1. Navigieren Sie im Fenster {{site.data.keyword.blockchainfull_notm}} Platform zu dem Peer, auf dem Sie das Smart-Contract-Paket installieren wollen.
2. Klicken Sie mit der rechten Maustaste auf den Peer, auf dem das Smart-Contract-Paket installiert werden soll, und wählen Sie die Option **Smart Contract installieren** aus.

## Smart-Contract-Pakete instanziieren
{: #develop-vscode-instantiating-smart-contract-packages}

Damit ein installiertes Smart-Contract-Paket auf einem Kanal ausgeführt werden kann, muss es zunächst instanziiert werden.

1. Stellen Sie eine Verbindung zu der Hyperledger Fabric-Instanz her, auf der das Smart-Contract-Paket installiert ist.
2. Klicken Sie mit der rechten Maustaste auf den Kanal, in dem Sie das Smart-Contract-Paket instanziieren möchten, und klicken Sie dann auf **Smart Contract instanziieren**.
3. Wählen Sie das Smart-Contract-Paket und die Version für die Instanziierung aus. Das Smart-Contract-Paket muss auf einem Peer installiert sein, der Mitglied dieses Kanals ist.
4. Geben Sie den Namen der Instanzerstellungsfunktion in Ihrem Smart Contract ein.
5. Geben Sie alle gegebenenfalls von der Instanzerstellungsfunktion benötigten Argumente ein.

## Smart-Contract-Paket exportieren oder löschen
{: #develop-vscode-exporting-deleting-smart-contract-package}

Nachdem ein Smart-Contract-Paket erstellt wurde, kann es als Datei `.cds` exportiert werden. Falls es nicht mehr benötigt wird, kann es gelöscht werden.

So löschen Sie ein Smart-Contract-Paket:

1. Klicken Sie in der Anzeige der Erweiterung "{{site.data.keyword.blockchainfull_notm}} Platform" mit der rechten Maustaste auf das zu löschende Smart-Contract-Paket.
2. Wählen Sie **Paket löschen** aus.

Das Paket wird jetzt gelöscht und nicht mehr in der Liste der Smart-Contract-Pakete angezeigt.

So exportieren Sie ein Smart-Contract-Paket:

1. Klicken Sie in der Anzeige der Erweiterung "{{site.data.keyword.blockchainfull_notm}} Platform" mit der rechten Maustaste auf das zu exportierende Smart-Contract-Paket.
2. Wählen Sie **Paket exportieren** aus.
3. Wählen Sie das Verzeichnis aus, in dem die Datei mit Ihrem Smart-Contract-Paket gespeichert werden soll, und klicken Sie auf **Exportieren**.

## Instanziierten Smart Contract testen
{: #develop-vscode-testing-instantiated-smart-contract}

Nachdem ein Smart Contract instanziiert wurde, können Tests für einen Smart Contract generiert werden. Die Tests können entweder in JavaScript oder in TypeScript generiert, ausgeführt oder einer Fehlerbehebung unterzogen werden.

So generieren Sie Tests für Smart Contracts:

1. Vergewissern Sie sich, dass der Smart Contract instanziiert wurde.
2. Klicken Sie unter **Smart Contracts instanziieren** mit der rechten Maustaste auf den Smart Contract, für den Tests generiert werden sollen.
3. Wählen Sie die Option **Tests für Smart Contract generieren** aus.
4. Wählen Sie jetzt die Sprache für die Testdatei aus (entweder  **JavaScript** oder **TypeScript**). Die {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung installiert daraufhin erforderliche npm-Module und erstellt die Testdatei.

Nachdem die Testdatei erstellt wurde, können Sie die Tests ausführen, indem Sie in der Datei auf die Schaltfläche **Run Tests** (Tests ausführen) klicken.

## Debugging für einen Smart Contract mit der vorkonfigurierten Hyperledger Fabric-Laufzeit ausführen
{: #develop-vscode-debugging}

Die Möglichkeit, einen Smart Contract lokal zu debuggen, hilft einem Smart Contract-Entwickler dabei, seine Smart Contract-Funktionen zu iterieren und Programmfehler zu beheben, bevor er versucht, die Funktionen nach der Instanziierung aufzurufen. Beim Debugging für einen Smart Contract können Sie die Transaktionen des Smart Contracts mit Unterbrechungspunkten und Ausgabe durchlaufen und sich vergewissern, dass die Transaktionen wie beabsichtigt funktionieren. So führen Sie das Debugging für Ihren Smart Contract aus:

1. Stellen Sie sicher, dass die Verbindung für `local_fabric` im Entwicklungsmodus besteht.
2. Öffnen Sie Ihr Smart-Contract-Projekt.
3. Öffnen Sie die Debugansicht in Visual Studio Code über die linke Navigationsleiste.
4. Wählen Sie die Option für das Debugging der Smart-Contract-Konfiguration in der Dropdown-Liste links oben aus.
5. Erstellen Sie das Smart-Contract-Paket und installieren Sie den Smart Contract, indem Sie auf die Schaltfläche für die **Wiedergabe** klicken.
6. Fügen Sie Unterbrechungspunkte zum Smart Contract hinzu, indem Sie in den Smart-Contract-Dateien auf die entsprechenden Zeilennummern klicken.
7. Klicken Sie mit der rechten Maustaste auf den installierten Smart Contract und wählen Sie die Option **Instanziieren** aus. Nun können Sie mit der rechten Maustaste klicken, um Transaktionen zu übergeben; die Ausführung wird dann bei den definierten Unterbrechungspunkten angehalten.

Um während des Debuggings Änderungen am Smart Contract vorzunnehmen, klicken Sie auf die Schaltfläche **Erneut starten**, nachdem Sie den Smart Contract geändert haben. Durch das erneute Starten des Debuggings müssen Sie den Smart Contract nicht noch einmal instanziieren.

**Hinweis:** Beim Neustart des Debuggings wird der Smart Contract im lokalen Speicher abgelegt. Bei einer größen Anzahl von Änderungen an einem umfangreichen Smart Contract müssen Sie den Smart Contract unter Umständen reinstanziieren.

## Upgrade für instanziierten Smart Contract durchführen
{: #develop-vscode-upgrading-instantiated-smart-contract}

Nachdem ein Smart Contract auf einem Peer installiert und auf einem Kanal instanziiert wurde, kann ein Upgrade durchgeführt werden, um eine neuere Version des Smart Contracts bereitzustellen.

1. Stellen Sie sicher, dass der Smart Contract instanziiert wurde, für den Sie ein Upgrade durchführen wollen.
2. Installieren Sie die neue Version des Smart Contracts auf einem Peer in demselben Netz.
3. Klicken Sie mit der rechten Maustaste auf den instanziierten Smart Contract und wählen Sie die Option **Upgrade für Smart Contract durchführen** aus.
4. Wählen Sie optional eine Transaktion aus, die nach der Instanziierung des neuen Smart Contracts ausgeführt werden soll.

## Transaktionen übergeben
{: #develop-vscode-submitting-transactions}

Nachdem ein Smart Contract installiert und instanziiert wurde, können die Transaktionen im Fenster "Verbindungen" der Anzeige der Erweiterung "{{site.data.keyword.blockchainfull_notm}} Platform" übergeben werden.

So übergeben Sie eine Transaktion:

1. Stellen Sie sicher, dass der Smart Contract installiert sowie instanziiert wurde und dass Sie mit dem Netz verbunden sind.
2. Erweitern Sie im Fenster "Verbindungen" den Eintrag **Instanziierte Smart Contracts**.
3. Erweitern Sie den Smart Contract, der die zu übergebende Transaktion enthält.
4. Klicken Sie mit der rechten Maustaste auf die zu übergebende Transaktion und wählen Sie die Option **Transaktion übergeben** aus.
5. Geben Sie alle gegebenenfalls von der Transaktion benötigten Argumente ein und drücken Sie die **Eingabetaste**.

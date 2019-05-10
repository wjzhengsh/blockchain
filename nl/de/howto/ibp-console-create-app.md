---

copyright:
  years: 2019
lastupdated: "2019-03-20"

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

# Anwendungen erstellen
{: #ibp-console-app}

Nach der Installation von Smart Contracts und der Bereitstellung Ihrer Knoten können Sie Clientanwendungen verwenden, um mit anderen Mitgliedern Ihres Netzes Transaktionen durchführen zu können. Anwendungen können die Geschäftslogik in Smart Contracts zum Erstellen, Übertragen oder Aktualisieren von Assets im Blockchain-Ledger aufrufen. In diesem Lernprogramm erfahren Sie, wie Sie Clientanwendungen für die Interaktion mit Netzen verwenden können, die Sie über die {{site.data.keyword.blockchainfull}} Platform-Konsole verwalten.
{:shortdesc}

**Zielgruppe:** Dieser Abschnitt richtet sich an Anwendungsentwickler, die mehr darüber erfahren möchten, wie eine Clientanwendung erstellt wird, die mit einem Blockchain-Netz interagiert.

## Lernressourcen
{: #ibp-console-app-learning-resources}

Weitere Informationen darüber, wie Anwendungen und Smart Contracts miteinander funktionieren, finden Sie im [Abschnitt zum Entwickeln von Anwendungen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Anwendungen entwickeln") in der Dokumentation zu Hyperledger Fabric. Dieser Abschnitt enthält einen hypothetischen Anwendungsfall, bei dem Banken und Unternehmen mit Wertpapieren handeln, und es wird beschrieben, wie die Transaktionen in Smart Contracts verschlüsselt sind. Das [Wertpapier-Lernprogramm (commercial-paper) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Wertpapier-Lernprogramm (commercial-paper)") erweitert dieses Thema, indem es Ihnen ermöglicht, die Smart Contracts im Fabric-Netz zu implementieren und Musteranwendungscode zum Erstellen und Übertragen des Wertpapiers zwischen Netzmitgliedern zu verwenden.

**Anwendungsbeispiele und Lernprogramme**

|  Beispiel  |  Beschreibung    |  Komplexität    | Sprache | Standort |
| -----------------|---------|---------|---------|
| [FabCar ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/patterns/write-a-smart-contract-for-the-fabcarcommercial-paper-or-iks-cluster-with-saas-v2-beta-network-think/ "Blockchain")| Verwenden Sie einen IKS-Cluster, um einen einfachen Smart Contract für ein Fabric-Netz in Blockchain Platform bereitzustellen. | Mittel | Node.js | DeveloperWorks-Dokumentation|
| [Commercial Paper ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Commercial Paper")| Verwenden Sie den Smart Contract für Wertpapiere (commercial paper) für den Handel mit einer lokalen Instanz von Hyperledger Fabric. | Mittel | Node.js | Hyperledger Fabric-Dokumentation|
| **Demnächst verfügbar:** Advanced Commercial Paper  | Erweitertes Beispiel mit bewährten Verfahren für Besitz und Datenschutz unter Verwendung privater Datensammlungen und statusbasierter Billigung. | Hoch | Node.js | Hyperledger Fabric-Dokumentation|
| [Run a commercial paper smart contract with the {{site.data.keyword.blockchainfull_notm}} VSCode extension ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/tutorials/run-commercial-paper-smart-contract-with-ibm-blockchain-vscode-extension/ "Run a commercial paper smart contract with VSCode")|  Erfahren Sie, wie Sie die VScode-Erweiterung zum Installieren und Aufrufen des Smart Contract für Wertpapiere in einer lokalen Instanz von Hyperledger Fabric verwenden können. | Mittel | Node.js | IBM Developer|
| [Run the commercial paper sample on the {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)| Erfahren Sie, wie Sie den Smart Contract für Wertpapiere in einem {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Netz aufrufen können. | Mittel |  Node.js | {{site.data.keyword.blockchainfull_notm}} Platform-Dokumentation|  

Für die Entwicklung einer Anwendung ist möglicherweise eine Koordination zwischen zwei verschiedenen Benutzern (dem Netzoperator und dem Anwendungsentwickler) erforderlich:
- **Der Netzoperator** ist der Administrator, der die {{site.data.keyword.blockchainfull_notm}} Platform-Konsole verwendet, um die Knoten Ihrer Organisation zu implementieren und die Smart Contracts in Ihrem Netz einzurichten.
- **Der Anwendungsentwickler** erstellt die Clientanwendung, die von Endbenutzern verwendet wird. Der Entwickler verwendet die [Hyperledger Fabric-SDKs ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric-SDKs"){:new_window}, um Transaktionen aufzurufen, die in den Smart Contracts angegeben sind.

Wenn Sie der **Netzoperator** sind, müssen Sie die folgenden Schritte ausführen, bevor der Anwendungsentwickler mit Ihrem Netz interagieren kann:
1. Verwenden Sie die Zertifizierungsstelle Ihrer Organisation, um [eine Anwendungsidentität zu registrieren](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities).
2. [Laden Sie das Verbindungsprofil](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) aus der Anzeige für Smart Contracts herunter.
3. Senden Sie dem Anwendungsentwickler die folgenden Objekte und Informationen:
  - Die Eintragungs-ID und der geheime Schlüssel der Anwendungsidentität.
  - Das Verbindungsprofil.
  - Den Namen des Smart Contract.
  - Den Namen des Kanals, auf dem der Smart Contract instanziiert wurde.  

Wenn Sie der **Anwendungsentwickler** sind, verwenden Sie die vom Netzoperator bereitgestellten Informationen, um die folgenden Schritte auszuführen:
1. Generieren Sie ein Schlüsselpaar aus öffentlichem und privatem Schlüssel unter Verwendung der Eintragungs-ID und des geheimen Schlüssels der Anwendungsidentität zusammen mit Endpunktinformationen der Zertifizierungsstelle in Ihrem Verbindungsprofil.
2. Verwenden Sie das Verbindungsprofil, den Kanalnamen, den Namen des Smart Contract und die Anwendungsschlüssel, um den Smart Contract aufzurufen.  

Der Anwendungsentwickler kann zwei Programmiermodelle für die Interaktion mit dem Netz verwenden:

**Übergeordnete APIs des Fabric-SDK**

Ab Fabric Version 1.4 können Benutzer ein Programmiermodell für vereinfachte Anwendungen und Smart Contracts nutzen. Das neue Modell reduziert die Anzahl der Schritte und den Umfang des Codes, der für die Übergabe einer Transaktion erforderlich ist. Dieses Modell wird nur für Anwendungen unterstützt, die in **Node.js** geschrieben sind. Wenn Sie das neue Modell nutzen möchten, können Sie dieses Lernprogramm verwenden, um die folgenden Aktionen in einem {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Netz auszuführen:

- [Zertifikate für die Anwendung](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll) mithilfe des SDK generieren.
- [Smart Contract über das SDK aufrufen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke).
- [IBM Blockchain-Erweiterung für VSCode](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) zum Schreiben, Bereitstellen und interaktivem Entwickeln und Testen von Smart Contracts verwenden.
- Mehr zur Anwendungsentwicklung durch das Bereitstellen des [Wertpapier-Lernprogramms (commercial-paper)](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) auf den Knoten erfahren, die von der Konsole aus verwaltet werden. Dieses Lernprogramm enthält weitere Hintergrundinformationen zur Verwendung von Fabric-Wallets und -Gateways.


**Untergeordnete APIs des Fabric-SDK**

Wenn Sie weiterhin Ihren bestehenden Smart Contract und Anwendungscode verwenden oder die anderen, von der Hyperledger-Community bereitgestellten Fabric-SDK-Sprachen nutzen möchten, können Sie mit den [untergeordneten APIs des Fabric-SDK](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level) eine Verbindung zu Ihrem Netz herstellen.

## Anwendungsidentität registrieren
{: #ibp-console-app-identities}

Anwendungen müssen die Transaktionen signieren, die sie an {{site.data.keyword.blockchainfull_notm}}-Knoten übergeben, und einen öffentlichen Schlüssel zuordnen, der von Knoten verwendet wird, um zu überprüfen, ob die Transaktionen von der richtigen Partei gesendet werden. Dadurch wird sichergestellt, dass Transaktionen von den teilnahmeberechtigten Organisationen übergeben werden.

Der Netzoperator muss die Zertifizierungsstelle der Organisation verwenden, um eine Anwendungsidentität zu registrieren, die dann vom Anwendungsentwickler für die Generierung eines öffentlichen und privaten Schlüssels verwendet werden kann. Der Operator kann die Eintragungs-ID und den geheimen Schlüssel der Identität gemeinsam mit den Endpunktinformationen der Zertifizierungsstelle bereitstellen, die vom SDK zur Generierung von Zertifikaten verwendet werden sollen. Durch das Eintragen auf der Clientseite stellt der Anwendungsentwickler sicher, dass keine andere Partei Zugriff auf den privaten Schlüssel der Anwendung hat. Für zusätzliche Sicherheit kann der Netzoperator während der Registrierung das Eintragungslimit '1' eingeben. Nachdem sich der Anwendungsentwickler eingetragen hat, können die Eintragungs-ID und der geheime Schlüssel nicht mehr zum Generieren eines weiteren privaten Schlüssels verwendet werden.

Wenn Sie der Sicherheit eher eine untergeordnete Rolle zuschreiben, kann der Netzoperator über die [Registerkarte "Zertifizierungsstelle"](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) eine Anwendungsidentität eintragen. Der Operator kann dann die Identität herunterladen oder in die Konsolenwallet exportieren. Um die Zertifikate aus dem SDK verwenden zu können, müssen die Schlüssel von Base64 in das PEM-Format decodiert werden. Sie können die Zertifikate decodieren, indem Sie den folgenden Befehl auf Ihrer lokalen Maschine ausführen:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64-zeichenfolge> | base64 --decode $FLAG > <schlüssel>.pem
```
{:codeblock}

## Verbindungsprofil herunterladen
{: #ibp-console-app-profile}

Anwendungen können Transaktionen nur an die Smart Contracts übergeben, die auf Kanälen instanziiert wurden. Daher sind die Informationen, die Sie für die Interaktion mit einem Smart Contract benötigen, in der Liste der instanziierten Smart Contracts in Ihrer Konsole enthalten. Dies bedeutet, dass Sie Ihren Smart Contract bereits installiert und instanziiert haben müssen.

Der [Transaktionsfluss ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaktionsfluss"){:new_window} von Hyperledger Fabric umfasst mehrere Komponenten, wobei die Clientanwendungen Bewilligungen von Peers erfassen und bewilligte Transaktionen an den Anordnungsservice senden. Das Verbindungsprofil stellt Ihre Anwendung mit den Endpunkten der Peers und Anordnungsknoten bereit, die für die Übergabe einer Transaktion benötigt werden. Es enthält außerdem Informationen zu Ihrer Organisation, zu Ihren Zertifizierungsstellen und zu Ihrer MSP-ID. Die Fabric-SDKs können das Verbindungsprofil direkt lesen, ohne dass Sie Code schreiben müssen, der den Transaktions- und Bewilligungsablauf verwaltet.

Um die Vorteile der Funktion zur [Serviceerkennung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery") von Hyperledger Fabric nutzen zu können, müssen Sie Ankerpeers konfigurieren. Mit dieser Funktion kann die Anwendung lernen, welche Peers im Kanal außerhalb Ihrer Organisation eine Transaktion bewilligen müssen. Ohne Serviceerkennung müssen Sie die Endpunktinformationen dieser Peers von anderen Organisationen in einer externen Operation abrufen und sie zu Ihrem Verbindungsprofil hinzufügen. Weitere Informationen zum Konfigurieren von Ankerpeers finden Sie in Schritt 3 des Abschnitts zu [privaten Daten](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) im Lernprogramm zur Bereitstellung von Smart Contracts.

Navigieren Sie zur Registerkarte 'Smart Contracts' in der Platform-Konsole. Navigieren Sie neben jedem instanziierten Smart Contract zum Überlaufmenü. Klicken Sie auf die Schaltfläche **Verbindung zum SDK herstellen**. Dadurch wird ein Seitenfenster geöffnet, in dem Sie Ihr Verbindungsprofil erstellen und herunterladen können. Zuerst müssen Sie die Zertifizierungsstelle Ihrer Organisation auswählen, die Sie zum Registrieren Ihrer Anwendungsidentität verwendet haben. Sie müssen außerdem die MSP-Definition Ihres Unternehmens auswählen. Anschließend können Sie das Verbindungsprofil herunterladen, das Sie zum Generieren von Zertifikaten und zum Aufrufen des Smart Contract verwenden können.

## Mithilfe des SDK eintragen
{: #ibp-console-app-enroll}

Sobald der Netzoperator die Eintragungs-ID und den geheimen Schlüssel der Anwendungsidentität und das Netzverbindungsprofil bereitgestellt hat, kann ein Anwendungsentwickler die Fabric-SDKs oder den Fabric-CA-Client verwenden, um clientseitige Zertifikate zu generieren. Sie können die folgenden Schritte ausführen, um eine Anwendungsidentität mit dem [Fabric-SDK für Node.js ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Fabric-SDK for Node.js") einzutragen.

1. Speichern Sie das Verbindungsprofil auf Ihrem lokalen System und benennen Sie es in `connection.json` um.
2. Speichern Sie den folgenden Codeblock als `enrollUser.js` in dem Verzeichnis, in dem sich auch das Verbindungsprofil befindet:

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Neuen CA-Client für die Interaktion mit der Zertifizierungsstelle erstellen.
      const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
      const ca = new FabricCAServices(caURL);

      // Neue dateisystembasierte Wallet für die Verwaltung von Identitäten erstellen.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Überprüfen, ob der Benutzer mit Administratorberechtigung bereits eingetragen ist.
      const userExists = await wallet.exists('user1');
      if (userExists) {
        console.log('An identity for "user1" already exists in the wallet');
        return;
      }

      // Benutzer mit Administratorberechtigung eintragen und die neue Identität in die Wallet importieren.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      await wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. Bearbeiten Sie die Datei `enrollUser.js`, um die folgenden Werte zu ersetzen:
  - Ersetzen Sie ``<CA_Name>`` durch den Namen der Zertifizierungsstelle (CA) Ihrer Organisation. Der Name der Zertifizierungsstelle befindet sich im Abschnitt "Organisationen" Ihres Verbindungsprofils unter "Zertifizierungsstellen". Verwenden Sie nicht den Wert für "caName" im Abschnitt "Zertifizierungsstellen".
  - Ersetzen Sie ``<app_enroll_id>`` durch die Eintragungs-ID der Anwendung, die von Ihrem Netzoperator bereitgestellt wird.
  - Ersetzen Sie ``<app_enroll_secret>`` durch den geheimen Eintragungsschlüssel der Anwendung, der von Ihrem Netzoperator bereitgestellt wird.
  - Ersetzen Sie ``<msp_id>`` durch die MSP-ID Ihrer Organisation. Sie finden Ihre MSP-ID unter dem Abschnitt "Organisationen" Ihres Verbindungsprofils.
4. Navigieren Sie mit einem Terminal zu `enrollUser.js` und führen Sie den Befehl `node enrollUser.js` aus. Wenn der Befehl erfolgreich ausgeführt wird, sollte die folgende Ausgabe angezeigt werden:

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
  Das SDK erstellt und speichert Ihre Zertifikate im Verzeichnis `wallet/user1/` , das mit dem Befehl erstellt wurde. Dieses Verzeichnis ist die Wallet des Dateisystems, mit der zukünftige Transaktionen übergeben werden.

Die von den Fabric-SDKs verwendeten Wallets unterscheiden sich von der Wallet in der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole. Die in Ihrer Konsolenwallet gespeicherten Identitäten können nicht direkt vom SDK verwendet werden.
{: note}

## Smart Contract über das SDK aufrufen
{: #ibp-console-app-invoke}

Nachdem Sie den öffentlichen und den privaten Schlüssel der Anwendung generiert und in einer Wallet gespeichert haben, können Sie eine Transaktion übergeben. Sie müssen den Namen des Smart Contract und den Namen des Kanals kennen, auf dem der Smart Contract instanziiert wurde. Sie können die folgenden Schritte ausführen, um einen Smart Contract mit dem [Fabric-SDK für Node.js ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ "Fabric-SDK für Node.js") aufzurufen.


1. Speichern Sie nachfolgende Datei auf Ihrer lokalen Maschine unter dem Namen `invoke.js`. Speichern Sie die Datei im selben Verzeichnis wie `enrollUser.js`.

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Verbindungsprofil parsen. Dies wäre der Pfad zu der Datei, die von
        // der IBM Blockchain Platform-Betriebskonsole heruntergeladen wurde.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Wallet konfigurieren. Diese Wallet muss bereits mit einer Identität vorbereitet sein, die
        // die Anwendung für die Interaktion mit dem Peerknoten verwenden kann.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Neues Gateway erstellen und eine Verbindung zu dem/den Peerknoten des Gateways herstellen. Die festgelegte
        // Identität muss bereits in der angegebenen Wallet vorhanden sein.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {enabled: true, asLocalhost:false }});

        // Netzkanal abrufen, in dem der Smart Contract bereitgestellt wurde.
        const network = await gateway.getNetwork('<channel_name>');

        // Smart Contract aus dem Netzkanal abrufen.
        const contract = network.getContract('<name_des_smart_contract>');

        // Transaktion 'createCar' an den Smart Contract übergeben und darauf warten, dass sie im
        // Ledger festgeschrieben wird.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. Bearbeiten Sie `invoke.js`, um die folgenden Werte zu ersetzen:
  - Ersetzen Sie ``<channel_name>`` durch den Namen des Kanals, auf dem der Smart Contract instanziiert wurde. Sie finden den Namen der Zertifizierungsstelle unter dem Abschnitt "Zertifizierungsstellen" Ihres Verbindungsprofils.
  - Ersetzen Sie ``<smart_contract_name>`` durch den Namen des installierten Smart Contract. Sie können diesen Wert von Ihrem Netzoperator erfragen.
  - Bearbeiten Sie den Inhalt von `submitTransaction`, um eine Funktion innerhalb Ihres Smart Contract aufzurufen. Die Datei `invoke.js` wird geschrieben, um den [fabcar-Chaincode (Smart Contract) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar) aufzurufen. Wenn Sie die nachstehende Datei zum Übergeben einer Transaktion ausführen möchten, installieren Sie 'fabcar' und instanziieren Sie den Smart Contract in einem Ihrer Kanäle.

3. Navigieren Sie mit einem Terminal zu `invoke.js` und führen Sie den Befehl `node invoke.js` aus. Wenn der Befehl erfolgreich ausgeführt wird, sollte die folgende Ausgabe angezeigt werden:

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  Wenn Sie über die Konsole zu einem Kanal navigieren, können Sie einen weiteren Block sehen, der von der Transaktion hinzugefügt wurde.


## Verbindung zur {{site.data.keyword.blockchainfull_notm}}-Erweiterung für VScode herstellen
{: #ibp-console-app-vscode}

Die {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung für Visual Studio Code stellt innerhalb von Visual Studio Code eine Umgebung für die Entwicklung, Erstellung und Bereitstellung von Smart Contract-Paketen zur Verfügung. Stellen Sie sicher, dass Sie über Ihr Verbindungsprofil und die Gruppe der Schlüsseldateien verfügen, die mit Ihrer Zertifizierungsstelle generiert wurden. Sie können dann die VSCode-Erweiterung verwenden, um eine Verbindung zu einem Netz herzustellen, das Sie mit Ihrer Konsole verwalten.

Befolgen Sie die Anweisungen zum Herunterladen der [VSCode-Erweiterung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VSCode-Erweiterung") im Marketplace für Visual Studio Code. Sobald die Erweiterung installiert ist, öffnen Sie VScode. Sie können auf die Erweiterung zugreifen, indem Sie auf **Ansicht> Befehlspalette** klicken. Geben Sie den Befehl *IBM Blockchain Platform: SmartContract-Projekt erstellen* ein, um ein neues Smart Contract-Projekt zu erstellen.

Nachdem Sie das neue Projekt erstellt haben, können Sie mithilfe der Informationen in Ihrem instanziierten Smart Contract von Ihrer Konsole aus eine direkte Verbindung von Visual Studio Code zu Ihrem Netz herstellen. Verwenden Sie die Tabelle mit den **instanziierten Smart Contracts** auf der Registerkarte "Smart Contracts", um Ihr [Verbindungsprofil](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) auf das lokale Dateisystem herunterzuladen. [Erstellen Sie dann eine Anwendungsidentität](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) und erstellen Sie über Ihre Zertifizierungsstelle ein Zertifikat (öffentlichen Schlüssel) und einen privaten Schlüssel und laden Sie sie herunter. Folgen Sie den Anweisungen oben, um den privaten Schlüssel und das Zertifikat in das PEM-Format zu konvertieren. Wenn Sie Ihr Verbindungsprofil und die Schlüssel heruntergeladen haben, verwenden Sie die folgenden Schritte, um eine Verbindung zu Ihrem Netz herzustellen.

1. Öffnen Sie in Visual Studio Code die Registerkarte für _{{site.data.keyword.blockchainfull_notm}} Platform_.
2. Klicken Sie in der Anzeige _{{site.data.keyword.blockchainfull_notm}} Platform_ auf **Neue Verbindung hinzufügen**.
3. Geben Sie einen Namen für die Verbindung ein. Dieser Name wird in der Anzeige _{{site.data.keyword.blockchainfull_notm}} Platform_ angezeigt.
4. Geben Sie den vollständig qualifizierten Dateipfad Ihres Verbindungsprofils ein.
5. Geben Sie den vollständig qualifizierten Dateipfad Ihres Zertifikats (öffentlicher Schlüssel) im PEM-Format ein.
6. Geben Sie den vollständig qualifizierten Dateipfad des privaten Schlüssels im PEM-Format ein.
7. Ihre Verbindung sollte jetzt in der Verbindungsliste unter `local_fabric` angezeigt werden. Doppelklicken Sie auf den Verbindungsnamen, um eine Verbindung herzustellen.

Sobald Sie eine Verbindung von VSCode hergestellt haben, können Sie die Liste der Peers aus Ihrer Organisation und den Kanälen anzeigen, die sie im Teilfenster für Blockchain-Verbindungen verknüpft haben. Sie können die Liste der installierten Smart Contracts unter den einzelnen Peers anzeigen. Wenn Sie einen neuen Smart Contract aus Ihrem Projekt in Ihrem Netz installieren möchten, klicken Sie zunächst mit der rechten Maustaste auf einen Peer und wählen Sie **Smart Contract installieren** aus. Anschließend können Sie diesen Smart Contract instanziieren, indem Sie mit der rechten Maustaste auf einen Kanal klicken und **Smart Contract installieren** auswählen. Weitere Informationen zur Verwendung der {{site.data.keyword.blockchainfull_notm}} Platform-Erweiterung finden Sie in der Dokumentation im [Marketplace von Visual Studio Code ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VSCode-Erweiterung").


## Beispiel für Wertpapiere ausführen
{: #ibp-console-app-commercial-paper}

Das [Wertpapier-Lernprogramm (commercial-paper) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Wertpapier-Lernprogramm (commercial-paper)") in der Dokumentation zu Hyperledger Fabric führt Entwickler durch einen Anwendungsfall, in dem mehrere Parteien Wertpapiere kaufen, verkaufen und einlösen. Es dient als Erweiterung des [Abschnitts zum Entwickeln von Anwendungen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Anwendungen entwickeln"), indem es einen Beispiel-Smart Contract und Anwendungscode bereitstellt, mit denen Sie Assets erstellen und mit diesen Assets in einer lokalen Instanz von Fabric handeln können.

Sie können den Beispielcode aus dem Wertpapier-Lernprogramm (commercial-paper) auch in einem {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Netz bereitstellen. Auf diese Weise können Sie schnell die Interaktion mit Ihrem Netz starten und das Beispiel verwenden, um die erforderlichen Abhängigkeiten herunterzuladen. Der Beispielcode enthält zudem Beispiele zur Vorgehensweise beim Importieren von Zertifikaten in eine [Wallet ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") und zur Verwendung des Verbindungsprofils zum Erstellen eines [Fabric-Gateways ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric-Gateway"). Das Lernprogramm kann von zwei verschiedenen Organisationen verwendet werden, um verschiedene Transaktionen mit einem einzigen Asset abzuschließen. Wenn Sie das [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) verwendet haben, um zwei Organisation der Peers bereitzustellen, die mit einem Kanal verbunden sind, können Sie mit dem Lernprogramm interagieren, indem Sie beide Organisationen verwenden.

Führen Sie die folgenden Schritte aus, um das Beispiel in Ihrem Netz zu implementieren. Sie können sich das Lernprogramm in der Fabric-Dokumentation zum [Wertpapier-Lernprogramm (commercial-tutorial) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Wertpapier-Lernprogramm (commercial-tutorial)") ansehen, um weitere Details zu den Smart Contracts und zur Anwendungsstruktur zu erhalten.

### Voraussetzungen

Bevor Sie das Wertpapier-Beispiel (commercial-paper) bereitstellen können, müssen Sie die erforderlichen Tools auf Ihrer lokalen Maschine installieren:
  * [Git ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

Außerdem benötigen Sie einen Texteditor, um Dateien im Beispiel bearbeiten und speichern zu können. Sie können viele der qualitativ hochwertigen Editoren verwenden, die kostenlos verfügbar sind, wie z. B. [Atom ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://atom.io/ "Atom"), [Sublime Text ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://www.sublimetext.com/ "Git") oder [Brackets ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://brackets.io/ "Brackets").

### Schritt 1: Beispiel herunterladen

Sie laden das Wertpapier-Beispiel (commercial-paper) herunter, indem Sie das [Repository mit den Fabric-Beispielen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-samples "Fabric Repository mit den Fabric-Beispielen") herunterladen:

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

Wenn Sie die Fabric-Beispiele heruntergeladen haben, führen Sie die folgenden Befehle aus, um sicherzustellen, dass Sie die Version der mit Fabric v1.4 kompatiblen Beispiele verwenden.

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

Sie finden das Beispiel im Ordner `commercial-paper` von `fabric-samples`.

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

Das Lernprogramm enthält zwei Beispielorganisationen: **magnetocorp** und **digibank**. Jede Organisation verfügt über einen eigenen Beispielanwendungscode, der in zwei separaten Ordnern unter dem Verzeichnis `organization` gespeichert ist.

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

Im Verlauf des Lernprogramms verwenden Sie als Erstes den Anwendungscode für 'magnetocorp', um ein Wertpapier auszustellen. Anschließend können Sie mit dem Anwendungscode für 'digibank' das Wertpapier kaufen und einlösen. Beide Verzeichnisse enthalten denselben Smart Contract.

Navigieren Sie zu dem Anwendungscode innerhalb des Verzeichnisses 'magnetocorp'.

```
cd magnetocorp/application
```
{:codeblock}

Sobald Sie sich innerhalb des Verzeichnisses befinden, können Sie die Anwendungsabhängigkeiten herunterladen, indem Sie den folgenden Befehl ausführen:

```
npm install
```
{:codeblock}

### Schritt 2: Smart Contract installieren und instanziieren

Sie finden den Smart Contract 'commercial-paper' im Ordner `contract` der Verzeichnisse `digibank` und `magnetocorp`. Sie müssen diesen Smart Contract mithilfe des Lernprogramms auf allen Peers der Organisationen installieren. Sie müssen den Contract dann auf einem Kanal instanziieren. Der Smart Contract wird in ein [CDS-Format ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "Smart Contracts paketieren") paketiert, das dann über die Konsole installiert werden kann.

Zum Paktieren des Smart Contract können Sie die [IBM Blockchain-Erweiterung für VSCode](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) verwenden. Verwenden Sie nach der Installation der Erweiterung den Code von Visual Studio, um den Ordner `contracts` in Ihrem Arbeitsbereich zu öffnen. Öffnen Sie die Registerkarte _{{site.data.keyword.blockchainfull_notm}} Platform_. Navigieren Sie im Teilfenster _{{site.data.keyword.blockchainfull_notm}} Platform_ zu dem Abschnitt mit den Smart Contract-Paketen und klicken Sie auf die Option zum **Erstellen eines Pakets für ein Smart Contract**. Die VSCode-Erweiterung verwendet die Dateien im Ordner `contracts`, um ein neues Paket mit dem Namen `papernet-js@.0.0.1.cds` zu erstellen. Klicken Sie mit der rechten Maustaste auf dieses Paket, um es in Ihr lokales Dateisystem zu exportieren. Anschließend können Sie die Konsole verwenden, um [die Smart Contracts auf den Peers zu installieren](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install), und anschließend [den Smart Contract auf einem Kanal instanziieren](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).

### Schritt 3: Zertifikate für Ihre Wallet generieren

Anwendungen müssen die Anforderungen signieren, die sie an Fabric-Komponenten senden. Wenn die Komponenten die Organisationen, die die Transaktionen übergeben, nicht erkennen, werden die Transaktionen zurückgewiesen und mit einem Fehler zurückgegeben. Im Rahmen des Wertpapier-Beispiels wird ein Dateisystemwallet erstellt, in dem die Zertifikate gespeichert und die Transaktionen signiert werden. Weitere Informationen dazu, wie Anwendungen Wallets verwenden, finden Sie im Abschnitt zu [Wallets ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Wallet") in der Fabric-Dokumentation. Die von den Fabric-SDKs verwendeten Wallets unterscheiden sich von der Wallet in der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole. Die in Ihrer Konsolenwallet gespeicherten Identitäten können nicht direkt vom SDK verwendet werden.

Das ursprüngliche Beispiel verwendet die Datei `addToWallet.js`, um eine Dateisystemwallet mit Zertifikaten aus dem Fabric-Beispielordner zu erstellen. Sie erstellen eine neue Datei, die das SDK verwendet, um clientseitige Zertifikate zu generieren und sie direkt in einer neuen Wallet zu speichern.

Wählen Sie die Zertifizierungsstelle der Organisation 'magnetocorp' aus, die Sie im Lernprogramm verwenden möchten. Sie können z. B. 'Org1' verwenden, wenn Sie das Lernprogramm zum Erstellen eines Netzes abgeschlossen haben. Verwenden Sie die Zertifizierungsstelle, um [eine Anwendungsidentität zu erstellen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities). **Speichern** Sie die Eintragungs-ID und den geheimen Schlüssel.

Verwenden Sie die Konsole, um [das Verbindungsprofil herunterzuladen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Speichern Sie das Verbindungsprofil auf Ihrem lokalen Dateisystem und benennen Sie es in `connection.json` um. Verwenden Sie anschließend den folgenden Befehl, um das Verbindungsprofil in ein Verzeichnis zu versetzen, in dem es von zukünftigen Befehlen referenziert wird.

  ```
  mv $HOME/<pfad_zu_berechtigungsnachweisen>/connection.json /gateway/connection.json
  ```
  {:codeblock}

Navigieren Sie zum Verzeichnis `/magnetocorp/application` und speichern Sie den folgenden Codeblock als `enrollUser.js`.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Neuen CA-Client für die Interaktion mit der Zertifizierungsstelle erstellen.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Neue dateisystembasierte Wallet für die Verwaltung von Identitäten erstellen.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Überprüfen, ob der Benutzer mit Administratorberechtigung bereits eingetragen ist.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Benutzer mit Administratorberechtigung eintragen und die neue Identität in die Wallet importieren.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        await wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

Nehmen Sie sich einen Moment Zeit, um zu untersuchen, wie diese Datei funktioniert, bevor Sie sie bearbeiten. Zuerst importiert `enrollUser.js` die Klassen `FileSystemWallet` und `X509WalletMixin` aus der Bibliothek `fabric-network`.

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

Die Datei verwendet dann die Klasse `FileSystemWallet`, um auf Ihrem lokalen Dateisystem eine Wallet zu erstellen. Sie können die folgende Zeile bearbeiten, um die Wallet an einer anderen Position zu speichern.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

Nachdem Sie die Wallet erstellt haben, verwendet das Code-Snippet die Eintragungs-ID und den geheimen Schlüssel für die Eintragung mithilfe der Zertifizierungsstelle Ihrer Organisation. Anschließend wird eine Identität für das Schlüsselpaar aus öffentlichem und privatem Schlüssel erstellt, die in die Wallet importiert wird. Beachten Sie, dass die Datei die MSP-ID Ihres Unternehmens auch in die Wallet übergibt.

```
// Benutzer mit Administratorberechtigung eintragen und die neue Identität in die Wallet importieren.
const enrollment = await ca.enroll({ enrollmentID: '<eintragungs-id_der_app>', enrollmentSecret: '<geheimer_eintragungsschlüssel_der_app>' });
const identity = X509WalletMixin.createIdentity('<msp-id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

**Bearbeiten** Sie die Datei `enrollUser.js`, um die folgenden Werte zu ersetzen:
- Ersetzen Sie `'<CA_Name>'` durch den Namen der Zertifizierungsstelle (CA) Ihrer Organisation. Der Name der Zertifizierungsstelle befindet sich im Abschnitt "Organisationen" Ihres Verbindungsprofils unter "Zertifizierungsstellen". Verwenden Sie nicht den Wert für "caName" im Abschnitt "Zertifizierungsstellen".
- Ersetzen Sie `'<app_enroll_id>` durch die Eintragungs-ID der Anwendung, die von Ihrem Netzoperator bereitgestellt wird.
- Ersetzen Sie `'<app_enroll_secret>'` durch den geheimen Eintragungsschlüssel der Anwendung, der von Ihrem Netzoperator bereitgestellt wird.
- Ersetzen Sie `'<msp_id>'` durch die MSP-ID Ihrer Organisation. Sie finden diese MSP-ID unter dem Abschnitt "Organisationen" Ihres Verbindungsprofils.

Speichern Sie die Datei `enrollUser.js` und schließen Sie sie. Führen Sie im Verzeichnis `magnetocorp` den folgenden Befehl aus:
```
node enrollUser.js
```
{:codeblock}

Wenn der Befehl erfolgreich abgeschlossen wurde, sollte die folgende Ausgabe angezeigt werden:

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

Die erstellte Wallet befindet sich im Verzeichnis `magnetocorp` im Ordner `identity`.

### Schritt 4: Verbindungsprofil zum Erstellen eines Fabric-Gateways verwenden

Der [Transaktionsfluss![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Transaktionsfluss"){:new_window} von Hyperledger Fabric umfasst mehrere Komponenten, wobei die Clientanwendungen eine zentrale Rolle einnehmen. Ihre Anwendung muss eine Verbindung zu den Peers herstellen, die die Transaktion zulassen, und zu dem Anordnungsservice, der die Transaktion anordnet und einem Block hinzufügt. Sie können die Endpunkte dieser Knoten für Ihre Anwendung bereitstellen, indem Sie Ihr Verbindungsprofil verwenden, um ein Fabric-Gateway zu erstellen. Das Gateway führt dann die Interaktionen auf niedriger Ebene mit Ihrem Fabric-Netz durch. Weitere Informationen finden Sie im Abschnitt zum [Fabric-Gateway ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric gateway") in der Fabric-Dokumentation.

Sie haben Ihr Verbindungsprofil bereits heruntergeladen und verwendet, um eine Verbindung zur Zertifizierungsstelle Ihres Unternehmens herzustellen. Jetzt verwenden Sie das Verbindungsprofil, um ein Gateway zu erstellen.

Öffnen Sie die Datei `issue.js` in einem Texteditor. Beachten Sie vor dem Bearbeiten der Datei, dass sie die Klassen `FileSystemWallet` und `Gateway` aus der Fabric-Netzbibliothek importiert.

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

Die Klasse `Gateway` wird verwendet, um ein Gateway zu erstellen, das Sie zur Übergabe Ihrer Transaktion verwenden werden.

```
const gateway = new Gateway()
```
{:codeblock}

Die Klasse `FileSystemWallet` wird verwendet, um die im vorherigen Schritt erstellte Wallet zu laden. **Bearbeiten** Sie die folgende Zeile, wenn Sie die Position der Wallet in Ihrem Dateisystem geändert haben.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

Verwenden Sie nach dem Importieren Ihrer Wallet den folgenden Code, um Ihr Verbindungsprofil und die Wallet an das neue Gateway zu übergeben. Sie müssen den Code wie folgt **bearbeiten**, damit er dem Codeausschnitt unten ähnelt. Die Zeilen, die das Drucken von Protokollen anzeigen, wurden der Kürze halber entfernt.
- Aktualisieren Sie den Benutzernamen (`userName`) so, dass er mit dem Wert übereinstimmt, den Sie für `identityLabel` in `enrollUser.js` ausgewählt haben.
- Aktualisieren Sie die Erkennungsoptionen, um die Serviceerkennung in Ihrem Netz zu nutzen. Geben Sie `discovery: { enabled: true, asLocalhost: false }` an.  
- Aktualisieren Sie den Abschnitt zum Importieren Ihres Verbindungsprofils. Das Konsolenverbindungsprofil liegt im JSON-Format vor und nicht in der YAML-Datei, die von dem Beispiel verwendet wird.  

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const connectionProfile = JSON.parse(ccpJSON);

// Verbindungsoptionen festlegen; Identität und Wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

Dieses Code-Snippet verwendet das Gateway, um GRPC-Verbindungen zu den Peer- und Anordnungsknoten zu öffnen und mit Ihrem Netz zu interagieren.

### Schritt 5: Smart Contract aufrufen

Nachdem Sie das Gateway konfiguriert haben, um eine Verbindung zum Netz herzustellen, das von Ihrer Konsole verwaltet wird, bearbeiten Sie nun den Teil der Datei `issue.js`, der die Verbindung zum Smart Contract 'commercial-paper' herstellt. Sie müssen dem Gateway den Vertragsnamen und den Kanal bereitstellen, auf dem Sie den Smart Contract instanziiert haben.

**Bearbeiten** Sie die folgende Zeile, indem Sie `mychannel` durch Ihren Kanalnamen ersetzen.

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

Nach einer Codezeile, die eine Nachricht an Ihre Konsole ausgibt, befindet sich eine Zeile, die dem Gateway den Namen des Smart Contract zur Verfügung stellt. **Bearbeiten** Sie die folgende Zeile, um den Namen `papercontract` in den Namen des Vertrags zu ändern, den Sie installiert haben.

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

Das Gateway verfügt nun über alle Informationen, die es für die Übergabe einer Transaktion benötigt. Die folgende Zeile ruft die Funktion `issue` im Smart Contract für Wertpapiere mit den Argumenten auf, die das neue Wertpapierasset definieren.

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

Nachdem die Transaktion mit dem Gateway übergeben wurde, trennt die Datei `issue.js` die Gateway-Verbindung.

```
gateway.disconnect();
```
{:codeblock}

Mit diesem Befehl werden die von Ihrem Gateway geöffneten GRPC-Verbindungen geschlossen. Das Trennen von Verbindungen spart Netzwerkressourcen und verbessert die Leistung.

Nachdem Sie die Änderungen in diesem Schritt und **Schritt 4** ausgeführt haben, speichern Sie die Datei `issue.js` und schließen Sie sie. Übergeben Sie die Transaktion, mit der das neue Wertpapier (commercial-paper) erstellt wird, mit dem folgenden Befehl:

```
node issue.js
```
{:codeblock}

Wenn die Transaktion erfolgreich ist, sollte die folgende Ausgabe in Ihrem Terminal angezeigt werden:

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### Schritt 6: Beispiel als 'digibank' ausführen

Nachdem Sie das Wertpapier (commercial-paper) als 'magnetocorp' erstellt haben, können Sie das Wertpaper nun im Rahmen des Lernprogramms als 'digibank' erwerben und einlösen. Sie können den Anwendungscode für 'digibank' verwenden und dabei dieselbe Organisation wie 'magnetocorp' verwenden, oder die Zertifizierungsstelle, Peers und das Verbindungsprofil einer anderen Organisation verwenden. Wenn Sie das [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) abgeschlossen haben, ist dies eine gute Gelegenheit, das Lernprogramm als 'Org2' auszuführen.

Navigieren Sie zum Verzeichnis `digibank/application`. Sie können die in **Schritt 3** bereitgestellten Anweisungen befolgen, um die Zertifikate und die Wallet zu erstellen, die die Transaktion als 'digibank' signieren wird. Anschließend können Sie die Datei `buy.js` verwenden, um das Wertpapier von 'magnetocorp' zu kaufen, und anschließend mit `redeem.js` das Wertpapier einlösen. Sie können **Schritt 4** und **Schritt 5** ausführen, um diese Dateien so zu bearbeiten, dass sie auf das richtige Verbindungsprofil, den Kanal und Smart Contract verweisen.

## Verbindung zu Ihrem Netz mit untergeordneten APIs des Fabric-SDK herstellen
{: #ibp-console-app-low-level}

Wenn Sie Ihren vorhandenen Anwendungscode beibehalten oder Fabric-SDKs für andere Sprachen als Node.js verwenden möchten, können Sie weiterhin eine Verbindung zu Ihrem Netz über untergeordnete APIs des Fabric-SDK herstellen. Verwenden Sie die Konsole, um [Ihr Verbindungsprofil herunterzuladen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Sie können dann die Endpunkte der Peers und Anordnungsknoten Ihres Kanals direkt aus dem Verbindungsprofil importieren oder die Knotenendpunktinformationen verwenden, um Peer- und Anordnungsobjekte manuell hinzuzufügen. Sie müssen die Zertifizierungsstelle auch verwenden, um [eine Anwendungsidentität zu erstellen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities), und anschließend die Endpunktinformationen zum Eintragen auf der Clientseite verwenden oder Zertifikate mit Ihrer Konsole generieren.

Die Dokumentation zum [Fabric-SDK für Node.js ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link ")](https://fabric-sdk-node.github.io "Fabric-SDK für Node.js") enthält ein Lernprogramm zum [Verbinden des Netzes über ein Verbindungsprofil ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-network-config.html "Lernprogramm für Verbindungsprofil"){:new_window}. Das Lernprogramm verwendet die Endpunktinformationen der Zertifizierungsstelle in Ihrem Verbindungsprofil, um Schlüssel mit dem SDK zu generieren. Sie können auch Ihre Konsole verwenden, um einen öffentlichen und privaten Schlüssel zu generieren und die Schlüssel in das PEM-Format zu konvertieren. Anschließend können Sie einen Benutzerkontext festlegen, indem Sie mithilfe des nachfolgenden Codes Ihre Schlüssel direkt an die [Fabric-Client-Klasse ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Client.html "Fabric Client class") des SDK übergeben:

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

Wenn Sie über die untergeordneten SDK-APIs eine Verbindung zu Ihrem Netz herstellen, können Sie zusätzliche Schritte ausführen, um die Leistung und die Verfügbarkeit Ihrer Anwendung zu verwalten. Weitere Informationen finden Sie im Abschnitt mit den [Best Practices für Anwendungskonnektivität und -verfügbarkeit](/docs/services/blockchain/best_practices.html#best-practices-app-connectivity-availability).


## Indizes mit CouchDB verwenden
{: #console-app-couchdb}

Wenn Sie CouchDB als Statusdatenbank verwenden, können Sie JSON-Datenabfragen aus dem Smart Contract für die Statusdaten des Kanals ausführen. Es wird dringend empfohlen, Indizes für die JSON-Abfragen zu erstellen und sie in den Smart Contracts zu verwenden. Mithilfe von Indizes können Ihre Anwendungen Daten effizient abrufen, wenn das Netz zusätzliche Blöcke von Transaktionen und Einträgen im World-Status hinzufügt. Informationen zum Verwenden von Indizes mit Ihren Smart Contracts und Anwendungen finden Sie im Abschnitt zu den [Best Practices bei der Verwendung von CouchDB](/docs/services/blockchain/best_practices.html#best-practices-app-couchdb-indices).

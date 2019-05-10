---

copyright:
  years: 2019
lastupdated: "2019-04-19"

keywords: smart contract, private data, private data collection, anchor peer

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

# Smart Contract im Netz-Lernprogramm bereitstellen
{: #ibp-console-smart-contracts}

Ein Smart Contract (manchmal auch als "Chaincode" bezeichnet) ist der Code, der es Ihnen ermöglicht, Daten im Blockchain-Ledger zu lesen und zu aktualisieren. Ein Smart Contract kann Geschäftslogik in ein ausführbares Programm umwandeln, das alle Mitglieder im Blockchain-Netz vereinbart und verifiziert haben. Dieses Lernprogramm ist der dritte Teil in der [Beispielserie für das Netz-Lernprogramm](/docs/services/blockchain/howto/ibp-console-smart-contracts.md.html#ibp-console-smart-contracts-structure) und beschreibt, wie Sie Smart Contracts bereitstellen, um Transaktionen im Blockchain-Netz zu starten.
{:shortdesc}

**Zielgruppe:** Dieser Abschnitt richtet sich an Netzoperatoren, die für die Erstellung, Überwachung und Verwaltung des Blockchain-Netzes verantwortlich sind. Darüber hinaus können die Abschnitte zum Erstellen eines Smart Contract für Anwendungsentwickler von Interesse sein.

## Lernprogrammreihe für Beispielnetze
{: #ibp-console-smart-contracts-structure}

Diese dreiteilige Lernprogrammreihe führt Sie durch den Prozess der Erstellung und Verbindung eines relativ einfachen Hyperledger Fabric-Netzes mit mehreren Knoten, indem Sie mithilfe der {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Konsole ein Netz in Ihrem Kubernetes-Cluster installieren und einen Smart Contract instanziieren.

* Im [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) erfahren Sie, wie Sie durch das Erstellen eines Anordnungsknotens und eines Peers ein Netz hosten.
* Im [Lernprogramm zum Teilnehmen an einem Netz](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) erfahren Sie, wie Sie einem vorhandenen Netz beitreten können, indem Sie einen Peer erstellen und diesen mit einem Kanal verknüpfen.
* Im **Lernprogramm zum Bereitstellen eines Smart Contract im Netz** erfahren Sie, wie Sie einen Smart Contract schreiben und in Ihrem Netz bereitstellen.

Sie können die Schritte in diesen Lernprogrammen verwenden, um ein Netz mit mehreren Organisationen in einem Cluster für Entwicklungs- und Testzwecke zu erstellen. Verwenden Sie das Lernprogramm zum **Erstellen eines Netzes**, wenn Sie ein Blockchain-Konsortium gründen möchten, indem Sie einen Anordnungsknoten erstellen und Organisationen hinzufügen. Verwenden Sie das Lernprogramm zum **Teilnehmen an einem Netz**, um einen Peer mit dem Netz zu verbinden. Indem Sie die Lernprogramme mit unterschiedlichen Konsortiumsmitgliedern durchführen, können Sie ein wirklich **verteiltes** Blockchain-Netz erstellen.  

In diesem abschließenden Lernprogramm erfahren Sie, wie Sie einen Smart Contract erstellen und paketieren, wie Sie einen Smart Contract auf einem Peer installieren und wie Sie den Smart Contract in einem Kanal instanziieren.  

Smart Contracts werden auf Peers installiert und anschließend auf Kanälen instanziiert. **Alle Mitglieder, die Transaktionen übergeben oder Daten mithilfe eines Smart Contracts lesen wollen, müssen den Smart Contract auf Ihrem Peer installieren.** Ein Smart Contract wird durch seinen Namen und seine Version definiert. Daher müssen sowohl der Name als auch die Version des installierten Smart Contracts für alle Peers auf dem Kanal, die den Smart Contract ausführen wollen, konsistent sein.

Nachdem ein Smart Contract auf den Peers installiert wurde, wird der Smart Contract von einem einzelnen Netzmitglied auf dem Kanal instanziiert. Das Netzmitglied muss dem Kanal beigetreten sein, damit diese Aktion ausgeführt werden kann. Bei der Instanziierung wird das Ledger mit den Anfangsdaten aktualisiert, die vom Smart Contract verwendet werden. Anschließend werden die Smart Contract-Container auf den Peers gestartet, die dem Kanal mit dem installierten Smart Contract beigetreten sind. Die Peers können anschließend die aktiven Container zur Durchführung von Transaktionen verwenden.  
- **Es muss nur ein Netzmitglied einen Smart Contract instanziieren.**
- **Wenn ein Peer in einem installierten Smart Contract einem Kanal beitritt, auf dieselbe Smart Contract-Version bereits instanziiert wurde, dann wird der Smart Contract-Container automatisch gestartet.**  

In diesem Lernprogramm werden die Schritte zur Verwendung der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole ausgeführt, um die Implementierung von Smart Contracts in Ihrem Netz zu verwalten:

- [Installieren Sie Smart Contracts auf Ihren Peers](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install).
- [Instanziieren Sie Smart Contracts auf Kanälen](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).
- [Geben Sie Bewilligungsrichtlinien an](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse).
- [Aktualisieren Sie Smart Contract-Richtlinien und Code](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).


## Vorbemerkungen

Bevor Sie einen Smart Contract installieren können, müssen Sie sicherstellen, dass Folgendes bereits durchgeführt wurde.

- Sie müssen entweder [ein Netz erstellen](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) oder [an einem Netz teilnehmen](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network), indem Sie Ihre {{site.data.keyword.blockchainfull_notm}} Platform-Konsole verwenden.
- Da Smart Contracts auf Peers installiert werden, stellen Sie sicher, dass Sie Ihrer Konsole [Peers hinzugefügt](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1) haben.  
- Smart Contracts werden auf einem Kanal instanziiert. Daher müssen Sie mit der Konsole [einen Kanal erstellen](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) oder [einem Kanal beitreten](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

## Schritt 1: Schreiben Sie einen Smart Contract

Die {{site.data.keyword.blockchainfull_notm}}-Konsole verwaltet die *Bereitstellung* von Smart Contracts und nicht deren Entwicklung. Wenn Sie an der Entwicklung von Smart Contracts interessiert sind, können Sie mit Hilfe von Lernprogrammen, die von der Hyperledger Fabric-Community bereitgestellt werden, und mit den von {{site.data.keyword.IBM_notm}} bereitgestellten Tools Ihre ersten Schritte unternehmen.

- Informationen dazu, wie Smart Contracts zur Durchführung von Transaktionen zwischen mehreren Parteien verwendet werden können, finden Sie im Abschnitt [Anwendungen entwickeln ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Developing applications topic") in der Dokumentation zu Hyperledger Fabric.
- Ein vollständiges Lernprogramm zur Verwendung einer Anwendung für die Interaktion mit Smart Contracts finden Sie im Abschnitt [Hyperledger Fabric Commercial Paper-Lernprogramm ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Hyperledger Fabric Commercial Paper tutorial").
- Weitere Informationen zum Integrieren von Zugriffssteuerungsmechanismen in Ihren Smart Contract finden Sie unter [Chaincode für Entwickler ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "Chaincode for Developers").
- Wenn Sie mit dem Erstellen von Smart Contracts loslegen, können Sie mit der [{{site.data.keyword.blockchainfull_notm}} Erweiterung für Visual Studio Code ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") ein eigenes Smart Contract-Projekt erstellen. Sie können diese Erweiterung auch verwenden, um [eine direkte Verbindung zu Ihrem Netz mit Visual Studio-Code herzustellen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode).
- Wenn Sie zur Installation bereit sind, muss der Smart Contract in das [.cds-Format ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "packaging smart contracts") gepackt werden, damit es auf den Peers installiert werden kann. Weitere Informationen finden Sie im Abschnitt [Smart Contracts packen](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). Alternativ können Sie die [Peer-CLI-Befehle![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/commands/peerchaincode.html#peer-chaincode-package "peer chaincode package") verwenden, um das Paket zu erstellen.
<!-- Update the tutorial link to release1-4 when it is published -->


## Schritt 2: Installieren Sie einen Smart Contract
{: #ibp-console-smart-contracts-install}

Führen Sie die folgenden Schritte aus, indem Sie die Konsole verwenden:

1. Klicken Sie auf die Registerkarte **Smart Contracts**, um einen oder mehrere Smart Contracts zu installieren.
2. Klicken Sie auf **Smart Contract installieren**, um die Package-Datei mit dem Smart Contract im [.cds-Format ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4noah.html#packaging "packaging smart contracts") hochzuladen.
Sie können die {{site.data.keyword.blockchainfull_notm}}-Erweiterung für Visual Studio Code verwenden, um [Smart Contract-Paket zu erstellen](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). Wenn Sie das Paket über die Registerkarte **Smart Contracts** installieren, können Sie einen oder mehrere Peer-Knoten auswählen, auf dem die Smart Contracts installiert werden sollen.

Wenn in der Konsole nur ein Peer vorhanden ist, dann wird der Smart Contract auf diesen Peer installiert. Sie werden nicht aufgefordert, einen Peer auszuwählen, auf dem der Smart Contract installiert werden soll. Sie können zur Registerkarte "Knoten" navigieren und auf einen Peer klicken, der von Ihrer Konsole verwaltet wird, um die Liste der Smart Contracts anzuzeigen, die auf einem einzelnen Peer installiert wurden.

Sie können zu einem späteren Zeitpunkt auf die Registerkarte **Smart Contracts** zurückkehren, um den Smart Contract auf zusätzlichen Peers zu installieren, selbst nachdem der Smart Contract auf dem Kanal instanziiert wurde. Wenn die Version des Smart Contracts, den Sie installieren, mit der instanziierten Version identisch ist, können diese zusätzlichen Peers genau wie die vorhandenen Peers Transaktionen verarbeiten.
{:tip}

**Diese Konsole kann nicht zum Installieren von `.bna`-Dateien von Hyperledger Composer verwendet werden.**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode" "peer chaincode").  -->

## Schritt 3: Instanziieren Sie einen Smart Contract
{: #ibp-console-smart-contracts-instantiate}

Smart Contracts werden auf einem Kanal instanziiert. Jedes Konsolenmitglied mit Peers, die einem Kanal beigetreten sind, kann einen Smart Contract instanziieren und die zugehörige [Bewilligungsrichtlinie](/docs/services/blockchain/glossary.html#glossary-endorsement-policy) angeben.

Führen Sie die folgenden Schritte aus, indem Sie die Konsole verwenden:

1. Suchen Sie auf der Registerkarte "Smart Contract" den Smart Contract aus der Liste, die auf den Peers installiert ist, und klicken Sie im Überlaufmenü rechts neben der Zeile auf **Instanziieren**.
2. In der Seitenanzeige, die daraufhin geöffnet wird, wählen Sie einen Kanal aus, für den der Smart Contract instanziiert werden soll. Sie können den Kanal mit dem Namen `channel1` auswählen, den Sie erstellt haben. Klicken Sie anschließend auf **Weiter**.
3. Geben Sie die [Bewilligungsrichtlinie für den Smart Contract](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse) an, die im folgenden Abschnitt beschrieben wird. Wenn mehrere Organisationen Mitglieder des Kanals sind, haben Sie die Möglichkeit auszuwählen, wie viele Organisationen die Smart Contract-Transaktionen zulassen müssen.
4. Sie müssen außerdem die Organisationsmitglieder auswählen, die in die Bewilligungsrichtlinie aufgenommen werden sollen. Falls Sie dem Lernprogramm folgen, wäre dies `org1msp` und möglicherweise `org2msp`, wenn Sie sowohl die Lernprogramme **Build a network** (Ein Netz erstellen) als auch **Join a network** (Einem Netz beitreten) ausgeführt haben.
5. Wenn Ihr Smart Contract Sammlungen privater Fabric-Daten umfasst, müssen Sie die zugeordnete JSON-Konfigurationsdatei für die Datensammlung hochladen; ansonsten können Sie diesen Schritt überspringen. In diesem Abschnitt finden Sie weitere Informationen zur Nutzung [privater Daten](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).
6. Im letzten Fenster werden Sie aufgefordert, die Smart Contract-Funktion, die beim Start des Smart Contracts ausgeführt werden soll, sowie die zugehörigen Argumente anzugeben, die an diese Funktion übergeben werden sollen.

Sie können alle Smart Contracts anzeigen, die auf einem Kanal instanziiert wurden, indem Sie in der linken Navigation auf das Kanalsymbol klicken, einen Kanal aus der Tabelle auswählen und dann auf die Registerkarte **Kanaldetails** klicken.

Beachten Sie, dass bei der Verwendung eines kostenlosen {{site.data.keyword.cloud_notm}} Kubernetes Service-Clusters die Instanziierung erheblich länger dauern kann als bei einem kostenpflichtigen Cluster. Abhängig von der Anzahl der Peers, die Sie in Ihrem Cluster bereitgestellt haben, kann dies einige Minuten dauern.

Die Kombination von **Installation und Instanziierung** ist eine leistungsfähige Funktion, da ein Peer auf diese Weise in zahlreichen Kanälen mit demselben Smart Contract arbeiten kann. In bestimmten Fällen sollen Peers möglicherweise mehreren Kanälen beitreten, die mit demselben Smart Contract arbeiten, jedoch über unterschiedliche Gruppen von Netzmitgliedern verfügen, die auf die Daten zugreifen können. Ein Peer kann den Smart Contract einmal installieren und anschließend denselben Smart Contract-Container auf jedem Kanal verwenden, auf dem eine Instanziierung durchgeführt wurde. Dieser einfache Ansatz spart Rechenleistung und Speicherplatz und unterstützt Sie bei der Skalierung Ihres Netzes.

## Schritt 4: Senden Sie Transaktionen mit Ihren Clientanwendungen
{: #ibp-console-smart-contracts-connect-to-SDK}

Nachdem ein Smart Contract auf einem Kanal instanziiert wurde, können Sie eine Clientanwendung für Transaktionen mit anderen Mitgliedern Ihres Netzes verwenden. Anwendungen können die Geschäftslogik in Smart Contracts zum Erstellen, Übertragen oder Aktualisieren von Assets im Blockchain-Ledger aufrufen.

### Verbindung zum SDK herstellen
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
Die Registerkarte **Smart Contracts** enthält die Informationen, die Sie benötigen, um über eine Client-App eine Verbindung zu einem instanziierten Smart Contract herzustellen. Navigieren Sie neben jedem instanziierten Smart Contract zum Überlaufmenü. Klicken Sie auf die Schaltfläche **Verbindung zu Ihrem SDK herstellen**. Daraufhin wird ein Seitenfenster geöffnet, die die Informationen enthält, die Sie benötigen, um eine Verbindung zu diesem Smart Contract herzustellen: Smart Contract-Name, Kanalname und Ihr Verbindungsprofil. Weitere Informationen finden Sie unter [Anwendungen erstellen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Eine Bewilligungsrichtlinie angeben
{: #ibp-console-smart-contracts-endorse}

Jeder Smart Contract muss eine Bewilligungsrichtlinie haben, die bei der Instanziierung angegeben wird. Die Bewilligungsrichtlinie gibt die Gruppe von Organisationen (die Peers) auf einem Kanal an, der den Smart Contract ausführen und die Transaktionsausgabe unabhängig validieren kann. Eine Bewilligungsrichtlinie kann z. B. angeben, dass eine Transaktion nur zum Ledger hinzugefügt wird, wenn eine Mehrheit der Mitglieder im Kanal die Transaktion bewilligt. Die Organisation, von der die Smart Contract-Instanz erstellt wird, kann unter den Mitgliedern, von denen der Smart Contract installiert wurde, Prüfer auswählen und legt die Bewilligungsrichtlinie für alle Kanalmitglieder fest. Sie können die Bewilligungsrichtlinie wie im Abschnitt zum [Aktualisieren des Smart Contracts](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade) beschrieben aktualisieren.

Wenn Sie die Schritte zum [Instanziieren eines Smart Contracts](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate) ausführen, können Sie nach der Auswahl des Kanals das Seitenfenster verwenden, um die Bewilligungsrichtlinie für den Smart Contract festzulegen. Verwenden Sie diese Anzeige, um eine einfache Richtlinie anzugeben, indem Sie die Peers auswählen, die die Transaktion aus der Liste der Peers bewilligen müssen, die den Smart Contract auf dem Kanal installiert haben. Sie können auf diese Weise eine Bewilligungsrichtlinie angeben, die von allen Kanalmitgliedern, einer Mehrheit der Mitglieder, einem einzelnen Mitglied oder einem zusätzlichen Mitglied bewilligt werden muss. Durch die Festlegung der Bewilligung auf ein zusätzliches Mitglied kann eine Bewilligung durch den Urheber selbst verhindert werden. Die Standard-Bewilligungsrichtlinie ist auf `1 von x` festgelegt, d. h., es ist nur ein einziges Mitglied erforderlich, um eine Smart Contract-Transaktion zu bewilligen.

Klicken Sie auf die Schaltfläche **Erweitert**, wenn Sie eine Richtlinie im JSON-Format angeben wollen. Sie können diese Methode verwenden, um kompliziertere Bewilligungsrichtlinien anzugeben, z. B. die Anforderung, dass ein bestimmtes Mitglied des Kanals eine Transaktion sowie eine Mehrheit der anderen Mitglieder bewilligen muss. Weitere [Beispiele für erweiterte Bewilligungsrichtlinien ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Example endorsement policies") finden Sie in der Dokumentation zu Hyperledger Fabric. Weitere Informationen zum Schreiben von Bewilligungsrichtlinien in JSON finden Sie in der [Hyperledger Fabric Node SDK-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "Hyperledger Fabric Node SDK documentation").

## Upgrade eines Smart Contracts durchführen
{: #ibp-console-smart-contracts-upgrade}

Sie können einen Smart Contract aktualisieren, um seinen Code, seine Bewilligungsrichtlinie oder seine Sammlung privater Daten zu ändern und gleichzeitig die Beziehung zu den Assets im Ledger beibehalten. Es gibt verschiedene Gründe, warum Sie ein Upgrade eines instanziierten Smart Contracts durchführen.
1. Sie können für den Smart Contract ein Upgrade durchführen, um eine Funktionalität zum Code hinzuzufügen oder aus dem Code zu entfernen und die Logik Ihres Geschäftsnetzes zu iterieren.
2. Immer wenn einem Kanal ein neues Mitglied hinzugefügt wird, muss die Bewilligungsrichtlinie der instanziierten Smart Contracts ** aktualisiert werden, damit das neue Kanalmitglied einbezogen wird. Um mit dem neuen Kanalmitglied arbeiten zu können, muss der Smart Contract mit einer neuen Versionsnummer neu gepackt und auf dem Kanal instanziiert werden, selbst wenn der Smart Contract selbst nicht geändert wird. Andernfalls ist die Bewilligung von Transaktionen nicht erfolgreich.
3. Wenn eine Sammlung privater Daten geändert wurde, z. B. wenn eine Organisation hinzugefügt oder entfernt wurde, müssen Sie Ihren Smart Contract aktualisieren. Alternativ können Sie diese Aktion anwenden, sobald der JSON-Konfigurationsdatei für die Datensammlung eine neue Sammlung privater Daten hinzugefügt wird.
4. Die Argumente für die Initialisierung des Smart Contracts haben sich geändert.

**Vor dem Upgrade eines instanziierten Smart Contracts muss die neue Version des Smart Contracts auf allen Peers im Kanal installiert werden, die die vorherige Version des Smart Contracts ausführen.**

### Vorgehensweise beim Upgrade eines Smart Contracts
{: #ibp-console-smart-contracts-upgrade-howto}

Wenn Sie ein Upgrade für einen Smart Contract durchführen möchten, installieren Sie den aktualisierten Code, indem Sie denselben Namen für den Smart Contract angeben, jedoch die neue Versionsnummer verwenden. Wenn Sie eine aktuellere Version eines Smart Contracts auf einem Peer im Kanal installiert haben, beachten Sie Folgendes: Neben der Originalversion wird jetzt die Schaltfläche `Verfügbares Upgrade` in der Tabelle **Instanziierte Smart Contracts** angezeigt.

{:important}
Wenn ein neues Mitglied, das den Smart Contract ausführen möchte, dem Kanal beitritt, muss der Smart Contract aktualisiert werden, indem eine neue Version auf allen Peers installiert und diese auf dem Kanal mit einer geänderten Bewilligungsrichtlinie instanziiert wird, die das neue Mitglied enthält.

- Blättern Sie in der Tabelle **Instanziierte Smart Contracts** abwärts.
- Suchen Sie in der Tabelle **Instanziierte Smart Contracts** die Version und den Kanal des Smart Contracts, für den Sie ein Upgrade durchführen möchten. Neben dem Smart Contract muss die Bezeichnung **Verfügbares Upgrade** stehen.
- Klicken Sie auf das **Überlaufmenü** auf der rechten Seite der Smart Contract-Zeile, und klicken Sie auf **Upgrade**, um die folgenden Schritte auszuführen:  

 1. Wählen Sie in der Dropdown-Liste die Smart Contract-Version aus, für die Sie ein Upgrade auf dem Kanal durchführen möchten.
 2. Aktualisieren Sie die Bewilligungsrichtlinie, indem Sie Kanalmitglieder hinzufügen oder entfernen. Sie können auch auf **Erweitert** klicken, um eine neue Zeichenfolge mit JSON-Formatierung einzufügen, die die vorhandene Richtlinie ändert.
 3. Wenn Sie dem Smart Contract eine Konfigurationsdatei für eine Sammlung privater Daten zuordnen möchten, können Sie die JSON-Datei hochladen. Sie können die JSON-Datei aber auch hochladen, wenn Sie eine bereits vorhandene Sammlungskonfiguration aktualisieren möchten.   
 Wenn der Smart Contract bereits mit einer Konfigurationsdatei für Sammlungen instanziiert wurde, **müssen** Sie die vorherige Version oder eine neue Version dieser Datei in diesem Schritt erneut hochladen.  
 {:important}
 4. (Optional) Ändern Sie die Werte für die Initialisierungsargumente des Smart Contracts, wenn sich die Parameter geändert haben. Wenn Sie sich nicht sicher sind, wenden Sie sich an Ihren Smart Contract-Entwickler. Wenn sie sich nicht geändert haben, können Sie dieses Feld leer lassen.

Nachdem Sie den Smart Contract aktualisiert haben, ändern Sie die Version des Smart Contracts, der auf dem Kanal instanziiert ist, und ändern Sie den Smart Contract-Container für alle Peers, die die neue Version installiert haben. Wenn Sie mit Sammlungen privater Daten arbeiten, müssen Sie sicherstellen, dass Sie im Kanal Ankerpeers konfiguriert haben.

### Überlegungen beim Upgrade von Smart Contracts
{: #ibp-console-smart-contracts-upgrade-considerations}

1. Muss ich die neue Version des Smart Contracts auf allen Peers installieren?  

  Wenn ein Smart Contract auf einem Peer aufgerufen wird, wird versucht, die Version auszuführen, die auf dem Kanal instanziiert ist. Wenn eine frühere Version auf dem Peer ausgeführt wird, führt dies zu einem Fehler. Vor dem Upgrade eines Smart Contracts auf einem Kanal hat es sich *bewährt, die aktuelle Version des Smart Contracts auf allen Peers zu installieren, auf denen die vorherige Version ausgeführt wird.*  

2. Kann eine nachfolgende Version des Smart Contracts Daten aus einer früheren Version des Smart Contracts im Ledger verarbeiten?  

  Ja. Solange der Code des neuen Smart Contracts die Daten auf eine kompatible Weise bearbeitet (durch Hinzufügen neuer JSON-Felder und ohne Änderung der Semantik oder des Typs der vorhandenen Felder), kann jede nachfolgende Version des Smart Contracts mit den Daten aus einer früheren Version ausgeführt werden.

3. Was passiert mit meinen Peers, wenn ich vergesse, sie auf die aktuelle Version zu aktualisieren, bevor ich den Smart Contract auf dem Kanal aktualisiert habe?  

  Nach der Aktualisierung des Kanals, um die neue Version des Smart Contracts zu verwenden, und wenn noch Peers auf dem Kanal mit der Vorgängerversion vorhanden sind, können diese Peers keine Transaktionen mehr für den Smart Contract bewilligen. Außerdem besteht die Gefahr, dass nicht genügend Bewilligungen für Transaktionen vorhanden sind, die im Ledger festgeschrieben werden müssen, je nachdem, wie die Bewilligungsrichtlinie für den Smart Contract definiert ist. Allerdings ist es möglich, die neue Version des Smart Contracts später auf diesen Peers zu installieren. Diese werden dann wieder in der Lage sein, Transaktionen zu bewilligen und Bewilligungen effektiv aufzuholen.

4. Was passiert, wenn ich eine Organisation aus meiner Sammlung privater Daten entferne?

   Die Peers in dieser Organisation speichern weiterhin Daten in der Sammlung privater Daten, bis der zugehörige Ledger den Block erreicht, durch den die Zugehörigkeit aus der Sammlung entfernt wird. Anschließend empfangen die Peers in zukünftigen Transaktionen keine privaten Daten und _Clients_ dieser Organisation können die privaten Daten nicht mehr mittels Chaincode aus einem Peer abrufen.

## Private Daten
{: #ibp-console-smart-contracts-private-data}

Private Daten sind ein Feature von Hyperledger Fabric-Netzen ab Version 1.2 und werden verwendet, um sensible Informationen von anderen Organisationsmitgliedern **auf einem Kanal** privat zu halten. Der Datenschutz wird durch die Verwendung von [privaten Datensammlungen ![Symbol für externen Link")](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "Was ist eine private Datensammlung?") sichergestellt. Beispielsweise können mehrere Großhändler und eine Gruppe von Landwirten einem einzigen Kanal beitreten. Wenn ein Landwirt und ein Großhändler eine Transaktion privat durchführen möchten, können sie zu diesem Zweck einen Kanal erstellen. Sie können sich jedoch auch dafür entscheiden, eine private Datensammlung für den Smart Contract zu erstellen, die ihre Geschäftsbeziehungen regelt, um die Vertraulichkeit über sensible Aspekte des Verkaufs wie den Preis zu wahren, ohne einen zweiten Kanal erstellen zu müssen. Wenn Sie mehr darüber erfahren möchten, wann private Daten in einer Blockchain verwendet werden können, besuchen Sie den Artikel zum Konzept [Private Daten ![Symbol für externen Link")](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "Private Daten") in der Fabric-Dokumentation.

Um private Daten mit {{site.data.keyword.blockchainfull_notm}} Platform Free 2.0 Beta verwenden zu können, müssen die folgenden drei Bedingungen erfüllt sein:  
1. **Definieren Sie die private Datensammlung.** Sie können Ihrem Smart Contract eine private Datensammlungsdatei hinzufügen. Anschließend können Sie zur Laufzeit spezielle Chaincode-APIs für private Daten verwenden, um Daten in die Datensammlung einzugeben und aus ihr abzurufen. Weitere Informationen zur Verwendung von Sammlungen privater Daten mit Ihrem Smart Contract finden Sie im Fabric-SDK-Lernprogramm zur [Nutzung privater Daten ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-private-data.html "Nutzung privater Daten") in der Dokumentation zum Fabric-SDK.  

2. **Installieren und instanziieren Sie den Smart Contract.** Nachdem die private Datensammlung für den Smart Contract definiert wurde, müssen Sie den Smart Contract auf den Peers installieren, die Mitglieder des Kanals sind. Wenn Sie den Smart Contract in dem Kanal über die Konsole instanziieren, müssen Sie die JSON-Konfigurationsdatei zur Datensammlung hochladen. Weitere Informationen zur [Erstellung einer JSON-Definitionsdatei zur Datensammlung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-private-data.html "Nutzung privater Daten") finden Sie im entsprechenden Abschnitt der Dokumentation zum Fabric-SDK.

  Anstatt die Installation und die Instanziierung Ihres Smart Contract mit einer Konfigurationsdatei für die Datensammlung über die Konsole auszuführen, können Sie auch das Fabric-SDK verwenden. Diese Anweisungen sind auch im Abschnitt über die [Nutzung privater Daten ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "Verwendung von privaten Daten") in der Node-SDK-Dokumentation enthalten.  

  **Hinweis:** Ein Client muss der Administrator Ihres Peers sein, damit ein Smart Contract über das SDK installiert oder instanziiert werden kann. Aus diesem Grund müssen Sie die Zertifikate der Peer-Administratoridentität aus der Konsolenwallet herunterladen und den öffentlichen und privaten Schlüssel des Peer-Administrators direkt an das SDK übergeben, anstatt eine Anwendungsidentität zu erstellen. Ein Beispiel für die Vorgehensweise beim Übergeben eines Schlüsselpaares an das SDK finden Sie unter [Verbindung zu Ihrem Netz mit Low-Level-Fabric-SDK-APIs herstellen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level).  


3. **Konfigurieren Sie Ankerpeers.** Da das organisationsübergreifende [Gossip-Protokoll ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip-Datenverteilungsprotokoll") für private Daten aktiviert sein muss, muss für jede Organisation in der Sammlungsdefinition ein Ankerpeer vorhanden sein. Hier finden Sie Informationen zum [Konfigurieren von Ankerpeers](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-channels-anchor-peers) für Ihr Netz.

Ihr Kanal ist jetzt für die Verwendung privater Daten konfiguriert.

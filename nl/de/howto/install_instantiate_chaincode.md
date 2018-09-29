---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Chaincode installieren, instanziieren und aktualisieren


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Bei Chaincode handelt es sich um Software, die die Geschäftslogik und transaktionsorientierten Anweisungen zum Erstellen und Ändern von Assets im Ledger zusammenfasst. Chaincode kann in unterschiedlichen Sprachen geschrieben werden. {{site.data.keyword.blockchainfull}} Platform bietet Unterstützung für Chaincode, der auf Go und Node.js basiert. Chaincode wird in einem Docker-Container ausgeführt, der den Peers zugeordnet ist, die mit diesem Container interagieren müssen. Weitere Informationen zum Entwickeln von Chaincode finden Sie in den [Chaincode-Lernprogrammen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

Chaincode wird auf einem Peer installiert und anschließend in einem Kanal instanziiert. **Alle Mitglieder, die Transaktionen übergeben oder Daten mithilfe von Chaincode lesen wollen, müssen den Chaincode auf Ihrem Peer installieren.** Ein Chaincode wird anhand seines Namens und seiner Version definiert. Sowohl der Name als auch die Version des installierten Chaincodes müssen für die Peers eines Kanals einheitlich sein.

Nachdem der Chaincode auf den Peers installiert wurde, wird der Chaincode von einem einzelnen Netzmitglied auf dem Kanal instanziiert. Das Netzmitglied muss dem Kanal beigetreten sein, damit diese Aktion ausgeführt werden kann. Bei der Instanziierung werden die vom Chaincode verwendeten Anfangsdaten eingegeben. Anschließend werden die Chaincode-Container auf den Peers gestartet, die dem Kanal mit dem installierten Chaincode beigetreten sind. Die Peers können anschließend die aktiven Container zur Durchführung von Transaktionen verwenden. **Beachten Sie hierbei, dass nur ein Netzmitglied einen Chaincode instanziieren muss.** Wenn ein Peer in einem installierten Chaincode einem Kanal beitritt, auf dem er bereits instanziiert wurde, dann wird der Chaincode-Container automatisch gestartet.

Die Kombination von **Installation und Instanziierung** ist eine leistungsfähige Funktion, da ein Peer auf diese Weise in zahlreichen Kanälen mit ein und demselben Chaincode arbeiten kann. In bestimmten Fällen sollen Peers möglicherweise mehreren Kanälen beitreten, die mit demselben Chaincode arbeiten, jedoch über unterschiedliche Gruppen von Netzmitgliedern verfügen, die auf die Daten zugreifen können. Ein Peer kann den Chaincode einmal installieren und anschließend denselben Chaincode-Container auf jedem Kanal verwenden, auf dem eine Instanziierung durchgeführt wurde. Dieser einfache Ansatz spart Rechenleistung und Speicherplatz und unterstützt Sie bei der Skalierung Ihres Netzes.

## Chaincode installieren
{: #installchaincode}

Sie müssen den Chaincode auf allen Peers installieren, auf denen dieser Chaincode ausgeführt werden soll. Führen Sie die folgenden Schritte aus, um einen Chaincode zu installieren:
1. Wählen Sie in der Anzeige "Code installieren" Ihres Network Monitor einen Peer in der Dropdown-Liste aus, um den Chaincode zu installieren. Klicken Sie auf die Schaltfläche **Chaincode installieren**.
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. Geben Sie in der Popup-Anzeige **Chaincode** den Namen und die Version Ihres Chaincodes ein. **Hinweis:** Die Zeichenfolgen für den Namen und die Version werden in Anwendungen für die Interaktion mit dem installierten Chaincode verwendet. Klicken Sie auf die Schaltfläche **Durchsuchen**, um im lokalen Dateisystem zur Speicherposition der Chaincode-Quellendateien zu navigieren. Wählen Sie mindestens eine Chaincode-Quellendatei aus, die für den Peer installiert werden soll. Wählen Sie dann die Chaincode-Sprache in der Dropdown-Liste **Chaincode-Typ** aus.

Sie können Chaincode installieren, indem Sie GO- oder NODE-Dateien hochladen. Sie können auch Chaincode in einer ZIP-Datei hochladen. Wenn Sie eine ZIP-Datei verwenden, wird der Chaincode mit der gesamten Verzeichnisstruktur beibehalten. Dies ist hilfreich, wenn Pakete mit Abhängigkeiten berücksichtigt oder Indizes mit CouchDB verwendet werden sollen. Ein Beispiel zum Einfügen von Indizes bei Ihrem Chaincode finden Sie im Abschnitt zum [Verwenden von CouchDB über Chaincode ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html#using-couchdb-from-chaincode){:new_window} oder in diesem [Lernprogramm ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window} in der Hyperledger Fabric-Dokumentation. Darüber hinaus können Sie auch Informationen zum [Verwalten externer Abhängigkeiten für in GO geschriebenen Chaincode![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html#managing-external-dependencies-for-chaincode-written-in-go){:new_window} aufrufen.

  ![Chaincode installieren](../images/chaincode_install.png "Chaincode installieren")

## Chaincode instanziieren
Nachdem ein Chaincode im Dateisystem jedes Peers installiert wurde, der einem Kanal beitritt, muss der Chaincode anschließend auf dem Kanal instanziiert werden, sodass die Peers mit dem Ledger über den Chaincode-Container interagieren können. Die Instanziierung führt die erforderliche Initialisierung des Chaincodes durch. Dazu ist es häufig notwendig, die Schlüssel/Wert-Paare anzugeben, die den ursprünglichen World-Status eines Chaincodes darstellen.

Sie müssen über die Berechtigung **Operator** oder **Schreibberechtigter** für den Kanal verfügen, um den Chaincode zu instanziieren. Chaincode, der auf unterschiedlichen Peers denselben Namen und die dieselbe Version hat, muss nur einmal instanziiert werden, um den Chaincode-Container bereitzustellen. Führen Sie die folgenden Schritte aus, um einen Chaincode zu instanziieren:
1. Wählen Sie in der Anzeige "Code installieren" Ihres Network Monitor den Peer aus, für den Sie den Chaincode installiert haben, und lokalisieren Sie den zu instanziierenden Chaincode in der Chaincode-Tabelle. Klicken Sie anschließend auf die Schaltfläche **Instanziieren** unter der Überschrift **Aktion**.
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. Legen Sie in der Popup-Anzeige **Chaincode instanziieren** die Schlüssel/Wert-Paare als Argumente für die Chaincode-Initialisierung fest und wählen Sie den Kanal, auf dem instanziiert werden soll.  Klicken Sie auf **Weiter**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

3. Geben Sie die [Bewilligungsrichtlinie](../glossary.html#endorsement-policy) Ihres Chaincodes an. Anleitungen zum Festlegen von Bewilligungsrichtlinien finden Sie im [nächsten Abschnitt](#specifying-chaincode-endorsement-policies).

## Bewilligungsrichtlinien des Chaincodes angeben

Mit Bewilligungsrichtlinien können Sie die Gruppe von Peers angeben, von denen eine neue Transaktion validiert werden muss. Eine Bewilligungsrichtlinie kann z. B. angeben, dass eine Transaktion nur zum Ledger hinzugefügt wird, wenn eine Mehrheit der Mitglieder im Kanal die Transaktion bewilligt.

Die Bewilligungsrichtlinie wird festgelegt, wenn ein Chaincode in einem Kanal instanziiert wird. Die Organisation, von der die Chaincode-Instanz erstellt wird, kann unter den Kanalmitgliedern, von denen der Chaincode installiert wurde, Prüfer auswählen und legt die Bewilligungsrichtlinie für alle Kanalmitglieder fest. Sie können die Bewilligungsrichtlinie wie im Abschnitt zum [Aktualisieren des Chaincodes](#updating-a-chaincode) beschrieben aktualisieren, indem Sie im zweiten Schritt beim Reinstanziieren des Chaincodes anschließend eine neue Richtlinie angeben.

Wenn Sie die Bewilligungsrichtlinie über den Network Monitor festlegen, können Sie über die Benutzerschnittstelle eine **einfache Richtlinie** angeben oder mit JSON eine **erweiterte Richtlinie** angeben.

* **Einfache Richtlinie über die Benutzerschnittstelle angeben: ** Klicken Sie zunächst auf die Schaltfläche **Mitglied hinzufügen**, um die Gruppe von Mitgliedern auszuwählen, die Transaktionen validieren können. Legen Sie dann im Bereich für die **Bewilligungsrichtlinie** die Anzahl der Mitglieder in der Liste fest, von denen die Transaktion validiert werden muss, bevor sie bewilligt wird. Sie können auf diese Weise eine Bewilligungsrichtlinie angeben, die von allen Kanalmitgliedern, einer Mehrheit der Mitglieder, einem einzelnen Mitglied oder einem zusätzlichen Mitglied (d. h. z. B. von 2 von 5 Mitgliedern) bewilligt werden muss. Durch die Festlegung der Bewilligung auf ein zusätzliches Mitglied kann eine Bewilligung durch den Urheber selbst verhindert werden. Wenn Sie keine Änderungen vornehmen, gilt die Standardrichtlinie, die besagt, dass eine Transaktion von einem beliebigen Mitglied eines Kanals bewilligt werden kann.

  ![Einfache Bewilligungsrichtlinie](../images/simple_endorsement.png "Einfache Bewilligungsrichtlinie")

* **Erweiterte Richtlinie mit JSON angeben:** Über erweiterte Richtlinien können Sie eine Bewilligung durch wichtige Mitglieder oder Administratoren erforderlich machen oder den Bewilligungen bestimmter Mitglieder mehr Gewicht verleihen.

  Am einfachsten können Sie eine erweiterte Richtlinie angeben, indem Sie zunächst über die entsprechende Benutzerschnittstellenanzeige eine einfache Richtlinie erstellen. Klicken Sie dann auf die Schaltfläche **Erweiterte Richtlinie**. Daraufhin wird automatisch eine JSON-Version der Richtlinie mit denselben Mitgliedern und Regeln wie in der einfachen Richtlinie erstellt. Sie können den JSON-Code anschließend bearbeiten, um eine komplexere Version zu schreiben. Weitere Informationen zum Schreiben von Bewilligungsrichtlinien in JSON finden Sie in der [Node-SDK-Dokumentation von Hyperledger Fabric ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest). <!--You can also find examples of advanced endorsement policies in the main [Hyperledger Fabric documentation![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/arch-deep-dive.html#example-endorsement-policies)-->

  ![Erweiterte Bewilligungsrichtlinie](../images/advanced_endorsement.png "Erweiterte Bewilligungsrichtlinie")

## Chaincode aktualisieren

Sie können Chaincode aktualisieren, um die Chaincode-Programmierung zu ändern, und gleichzeitig die Beziehung beibehalten, die zwischen dem Chaincode und den Assets im Ledger besteht. Aufgrund der Kombination aus Installation und Instanziierung müssen Sie den Chaincode für alle Peers, die sich mit diesem Chaincode im Kanal befinden, aktualisieren. Führen Sie die folgenden Schritte aus, um den Chaincode zu aktualisieren.

1. Installieren Sie Chaincode, dessen Name mit dem des alten Chaincodes identisch ist, der jedoch eine andere Version aufweist. Hierzu können Sie dieselben Schritte ausführen wie für das [Installieren von Chaincode](#installchaincode). Stellen Sie sicher, dass Sie denselben Kanal auswählen wie beim ursprünglichen Chaincode.

  ![Chaincode aktualisieren](../images/upgrade_chaincode.png "Chaincode aktualisieren")

2. Suchen Sie den neuen Chaincode in der Tabelle und klicken Sie auf die Schaltfläche **Aktualisieren** unter der Überschrift **Aktion**. Mit dieser Aktion wird der Chaincode erneut instanziiert und der Chaincode-Container wird durch einen neuen ersetzt. Beachten Sie, dass im Rahmen der Aktualisierungsfunktion keine neuen Argumente eingegeben werden müssen. Diese Upgradeaktion wird auf dem Kanal ausgeführt und muss von nur einer Organisation ausgeführt werden.

  ![Schaltfläche 'Aktualisieren'](../images/upgrade_button.png "Schaltfläche 'Aktualisieren'")

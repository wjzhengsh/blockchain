---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Chaincode installieren und instanziieren

Bei Chaincode handelt es sich um Software (in Go oder Java geschrieben), die die Geschäftslogik und transaktionsorientierten Anweisungen zum Erstellen und Ändern von Assets im Hauptbuch (Ledger) umfasst. Ein Chaincode wird in einem Docker-Container ausgeführt, der den Peers zugeordnet ist, die mit diesem Container interagieren müssen. Weitere Informationen zum Entwickeln von Chaincodes finden Sie unter [Chaincode Tutorials ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

Chaincode wird zunächst im Dateisystem eines Peers installiert und anschließend auf einem Kanal instanziiert. Alle Kanalmitglieder müssen den Chaincode auf allen Peers installieren, auf denen dieser Chaincode ausgeführt werden soll. Damit derselbe Chaincode verwendet wird, müssen die Kanalmitglieder für den Chaincode während der Chaincode-Installation denselben Namen und dieselbe Version angeben. Bei der Instanziierung müssen Schlüssel/Wert-Paare initialisiert und der Chaincode-Container bereitgestellt werden.  Für jeden Peer, über den Anwendungen mit einem Chaincode interagieren sollen, muss der Chaincode im Dateisystem des Peers installiert sein und ein aktiver Chaincode-Container muss ausgeführt werden. Wenn ein Peer jedoch dieselbe Chaincode-Anwendung auf mehreren Kanälen verwendet, benötigt er nur eine einzelne Instanz des Chaincode-Containers.   

Die Kombination von Installation und Instanziierung ist eine leistungsfähige Funktion, da so ein Peer mit demselben Chaincode-Container auf mehreren Kanälen interagieren kann. Die einzige Voraussetzung ist, dass die eigentlichen Chaincode-Quellendateien im Dateisystem des Peers installiert sind. Wenn nämlich ein Teil eines allgemeinen Chaincodes auf Dutzenden von Kanälen verwendet wird, würde ein Peer nur einen einzigen Chaincode-Container benötigen, um Lese-/Schreibvorgänge für alle Kanalhauptbücher durchzuführen. Dieser einfache Ansatz ist für die Rechenleistung und den Durchsatz von Vorteil, da Netze skalieren und der Chaincode optimiert wird.  

## Chaincode installieren
Sie müssen den Chaincode auf allen Peers installieren, auf denen dieser Chaincode ausgeführt werden soll. Führen Sie die folgenden Schritte aus, um einen Chaincode zu installieren:
1. Wählen Sie in der Anzeige "Chaincode" Ihres Netzmonitors einen Peer in der Dropdown-Liste aus, um den Chaincode zu installieren. Klicken Sie auf die Schaltfläche **Chaincode installieren**.
  ![Anzeige 'Chaincode'](../images/chaincode_install_overview.png "Anzeige 'Chaincode'")  
  
2. Geben Sie in der Popup-Anzeige **Chaincode** den Namen und die Version Ihres Chaincodes ein. **Hinweis**: Die Zeichenfolgen für den Namen und die Version werden in Anwendungen für die Interaktion mit den installierten Chaincodes verwendet. Klicken Sie auf die Schaltfläche **Durchsuchen**, um im lokalen Dateisystem zur Speicherposition der Chaincode-Quelle zu navigieren. Wählen Sie mindestens eine Chaincode-Quellendatei aus, die für den Peer installiert werden soll. Dieses Beispiel zeigt eine Chaincode-Quellendatei `fabcar.go` mit dem Namen `Chaincode1` und der Version `v001`, die auf dem Peer `fabric-peer-org2-17439a` installiert werden soll.
  ![Chaincode installieren](../images/chaincode_install.png "Chaincode installieren")

## Chaincode instanziieren
Wenn ein Chaincode auf dem Dateisystem jedes Peers installiert wurde, der einem Kanal zugeordnet wird, muss der Chaincode anschließend auf dem Kanal instanziiert werden, sodass die Peers mit dem Hauptbuch über den Chaincode-Container interagieren können. Die Instanziierung führt die erforderliche Initialisierung des Chaincodes durch. Dazu ist es häufig notwendig, die Schlüssel/Wert-Paare anzugeben, die den ursprünglichen World-Status eines Chaincodes umfassen.  

Sie müssen über die Berechtigung **Operator** oder **Schreiben** für den Kanal verfügen, um den Chaincode zu instanziieren. Chaincode, der auf unterschiedlichen Peers denselben Namen und die dieselbe Version hat, muss nur einmal instanziiert werden, um den Chaincode-Container bereitzustellen. Führen Sie die folgenden Schritte aus, um einen Chaincode zu instanziieren:
1. Wählen Sie in der Anzeige "Chaincode" Ihres Netzmonitors den Peer aus, für den Sie den Chaincode installiert haben, und lokalisieren Sie den zu instanziierenden Chaincode in der Chaincode-Tabelle. Klicken Sie anschließend auf die Schaltfläche **Instanziieren** unter der Überschrift **Aktion**.  
  ![Chaincode instanziieren](../images/chaincode_instantiate.png "Chaincode instanziieren")  
  
2. Legen Sie in der Popup-Anzeige **Chaincode instanziieren** die Schlüssel/Wert-Paare als Argumente für die Chaincode-Initialisierung fest und wählen Sie den Kanal, auf dem instanziiert werden soll. Klicken Sie auf **Übergeben**.
  ![Anzeige 'Chaincode instanziieren'](../images/chaincode_instantiate_panel.png "Anzeige 'Chaincode instanziieren'")   

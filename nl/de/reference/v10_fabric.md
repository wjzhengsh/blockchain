---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Fabric
Das {{site.data.keyword.blockchainfull}}-Netz basiert auf dem Hyperledger Fabric-Stack, einem der Blockchain-Projekte im Hyperledger Project der Linux Foundation.  Dabei handelt es sich um ein "genehmigtes" Netz, in dem alle Benutzer und Komponenten bekannte Identitäten haben.  An jeder Nahtstelle in der Kommunikation ist Signatur-/Verifizierungslogik implementiert und über die Transaktionen wird durch eine Reihe von Bewilligungs- und Validierungsprüfungen entschieden.  Somit bestehen große Unterschiede zu herkömmlichen Blockchain-Implementierungen, die Anonymität unterstützen und auf Kryptowährungen und hohe Rechenleistung angewiesen sind, um Transaktionen zu validieren.  
{:shortdesc}

Hyperledger Fabric bietet eine modulare Architektur zur Erhöhung von Skalierbarkeit und Leistung an.  In diesem Abschnitt werden einige wichtige Komponenten von Hyperledger Fabric vorgestellt.  Eine vollständige Einführung in Hyperledger Fabric finden Sie in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.  

## Zertifizierungsstelle (CA)  
Als Plattform für **genehmigte** Blockchain-Netze enthält Hyperledger Fabric eine modulare **CA-Komponente** (Zertifizierungsstelle) für das Management der Netzidentitäten aller Mitgliedsorganisationen und ihrer Benutzer. Die Voraussetzung einer genehmigten Identität für jeden Benutzer ermöglicht eine ACL-basierte Steuerung der Netzaktivität und stellt sicher, dass jede Transaktion letztlich auf einen registrierten Benutzer zurückgeführt werden kann.  
* Die CA (standardmäßig die Fabric-CA) gibt ein Stammzertifikat (**rootCert**) an jedes **Mitglied** (Organisation oder Einzelperson) aus, das zur Teilnahme am Netz berechtigt ist.
* Die CA gibt auch ein Eintragungszertifikat (**eCert**) an alle Mitgliedskomponenten, serverseitigen Anwendungen und gelegentlich an Benutzer aus.
* Jeder eingetragene Benutzer erhält auch eine Zuordnung von Transaktionszertifikaten (**tCerts**).  Jedes **tCert** dient zur Berechtigung einer Netztransaktion.

Diese zertifikatbasierte Steuerung der Netzmitgliedschaft und Aktionen gibt Mitgliedern die Möglichkeit, den Zugriff auf private und vertrauliche Kanäle, Anwendungen und Daten durch bestimmte Benutzeridentitäten einzuschränken.

Weitere Informationen zur CA-Komponente von Hyperledger Fabric finden Sie in der Veröffentlichung [Fabric CA User’s Guide ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/){:new_window}.

## Membership Service Provider  
Hyperledger Fabric enthält eine Komponente **Membership Service Provider (MSP)**, die die Abstraktion aller kryptografischen Mechanismen und Protokolle ermöglicht, die der Ausgabe und Validierung von Zertifikaten sowie der Benutzerauthentifizierung zugrunde liegen.  Die MSP-Komponente wird auf jedem Kanalpeer installiert, um sicherzustellen, dass Transaktionsanforderungen, die an den Peer ausgegeben werden, von einer authentifizierten und autorisierten Benutzeridentität stammen.

Weitere Informationen zur MSP-Komponente des Hyperledger Fabric finden Sie unter *[Membership Service Providers (MSP) ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/msp.html){:new_window}* in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

## Transaktionsfluss  
Zur Sicherstellung der Datenkonsistenz und -integrität implementiert Hyperledger Fabric mehrere Prüfpunkte im Transaktionsfluss, zu denen Clientauthentifizierung, Bewilligung, Anordnung und Festschreibung im Ledger gehören.

**Abbildung 1** beschreibt den Transaktionsfluss in einem Hyperledger Fabric-Blockchain-Netz:
![Transaktionsfluss](../images/v10_txflow.png "Transaktionsfluss in einem Hyperledger Fabric-Netz")
*Abbildung 1. Transaktionsfluss in einem Hyperledger Fabric-Netz*

In einem Hyperledger Fabric-Netz wird der Datenfluss für Abfragen und Transaktionen von einer clientseitigen Anwendung initiiert, die eine Transaktionsanforderung an einen Peer auf einem Kanal übergibt. Der erste Datenfluss über das Netz gilt für Abfragen und Transaktionen gemeinsam:

1. Mit der im SDK enthaltenen API `channel.SendTransactionProposal` signiert und übergibt eine Clientanwendung einen Transaktionsvorschlag an die entsprechenden bewilligenden Peers auf dem angegebenen Kanal.  Dieser erste Transaktionsvorschlag ist eine **Anforderung** für eine Bewilligung.   
2. Jeder Peer auf dem Kanal überprüft die Identität und Berechtigung des übergebenden Clients und (falls gültig) führt den angegebenen Chaincode für die übergebenen Eingaben (Schlüssel/Werte) aus. Auf der Grundlage der Transaktionsergebnisse und der Bewilligungsrichtlinie für den aufgerufenen Chaincode gibt jeder Peer eine signierte Antwort YES oder NO an die Anwendung zurück. Jede signierte Antwort YES ist eine **Bewilligung** der Transaktion.

	An diesem Punkt im Transaktionsfluss verzweigt sich der Prozess für Abfragen und Transaktionen.  Wenn der Vorschlag eine Abfragefunktion im Chaincode aufgerufen hat, gibt die Anwendung die Daten an den Client zurück.  Wenn der Vorschlag eine Funktion im Chaincode zum Aktualisieren des Ledgers aufgerufen hat, wird die Anwendung mit den folgenden Schritten fortgesetzt:  
3. Die Anwendung leitet die Transaktion (Lesen/Schreiben und Bewilligungen) an den **Anordnungsservice** des Netzes weiter.  
4. Die Transaktion wird dann an das Partitionsthema (Partition Topic) des Kanals im Kafka-Cluster zum Festlegen einer Anordnung übermittelt.  Alle Kanalpeers validieren jede Transaktion im Block, indem Sie die Chaincode-spezifische Prüfrichtlinie anwenden und einen 'Concurrency Control Version Check' ausführen.  
	* Alle Transaktionen, die den Validierungsprozess nicht bestehen, werden im Block als ungültig markiert und der Block wird an die Hashchain des Kanals angehängt.  
	* Alle gültigen Transaktionen aktualisieren die Statusdatenbank entsprechend mit den geänderten Schlüssel/Wert-Paaren.  

Das **Gossip-Datenverteilungsprotokoll** überträgt kontinuierlich Ledgerdaten über den Kanal, um sicherzustellen, dass die Ledger zwischen den Peers synchronisiert sind.  Weitere Informationen finden Sie unter *[Gossip Data Dissemination Protocol ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/gossip.html){:new_window}* in der
[Hyperledger Fabric-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

Eine Schritt-für-Schritt-Einführung in den Transaktionsfluss finden Sie unter *[Transaction Flow ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/txflow.html){:new_window}* in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.  

## Anordnungsservice
Hyperledger Fabric beinhaltet einen Kafka-basierten Service für das Anordnen und Übertragen von Netztransaktionen. Kafka bietet außerdem eine Fehlertoleranzfunktion für Ihr Netz. Das heißt, auch wenn eine akzeptierte Anzahl von Anordnungsserviceknoten nicht verfügbar ist, setzt der Service das Anordnen und Verteilen von Transaktionsblöcken an Kanalpeers fort.

Clientseitige Anwendungen rufen die API 'channel.sendTransaction' auf, um bewilligte Transaktionen an den Anordnungsservice weiterzuleiten. Anordnungsserviceknoten nutzen anschließend den Kafka-Service und den zugehörigen ZooKeeper-Server, um die Transaktionen in einem Block anzuordnen. Der geordnete Transaktionsblock wird schließlich zur Validierung und Festschreibung im Ledger an die Kanalpeers "übergeben".

Anordnungsserviceknoten stellen die folgenden Services zur Verfügung:
1. Authentifizierung von Clients
2. Wartung einer Systemkette, die Anordnungsservicekonfigurationen, Stammzertifikate und MSP-IDs für authentifizierte Organisationen und eine Gruppierung von Profilen mit den verschiedenen Konsortien im Netz definiert.
3. Filterung und Validierung für Konfigurationstransaktionen, die einen Kanal neu konfigurieren oder erstellen.  

Weitere Informationen zum Hyperledger Fabric-Anordnungsservice finden Sie unter *[Bringing up a Kafka-based Ordering Service ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/kafka.html){:new_window}* in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

## HFC-SDKs
Mit den SDKs für Hyperledger Fabric Client (HFC) können Anwendungsentwickler Anwendungen erstellen, die mit einem Blockchain-Netz interagieren. Mit dem HFC-SDK können Anwendungen den Lebenszyklus von Kanälen und Chaincode leichter verwalten.

Hyperledger Fabric stellt SDKs für Node.js und Java zur Verfügung und bietet die folgenden Funktionen zur Interaktion mit dem Blockchain-Netz:
* Registrieren und Eintragen von Benutzern
* Erstellen von Kanälen
* Zuordnen von Peers zu einem Kanal
* Aktualisieren einer Systemkanal- oder Anwendungskanalkonfiguration
* Installieren von Chaincode auf Peers
* Instanziieren von Chaincode auf einem Kanal
* Upgrade von Chaincode auf einem Kanal
* Aufrufen von Chaincode-Funktionen zur Aktualisierung des Ledgers
* Abfragen des Ledgers in Bezug auf bestimmte Transaktionen, Blöcke oder Schlüssel
* Überwachen von Ereignissen auf einem Kanal (z. B. erfolgreiche Festschreibung einer Transaktion)

Weitere Informationen zu HFC-SDKs finden Sie unter *[Hyperledger Fabric SDKs ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/fabric-sdks.html){:new_window}* in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.

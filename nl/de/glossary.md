---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Glossar
{: #glossary}

{{site.data.keyword.blockchainfull}} Platform macht das Kennenlernen leicht. In diesem Abschnitt werden Begriffe definiert, die in dieser Dokumentation Verwendung finden und dazu gedacht sind, Sie in die Blockchain-Konzepte einzuführen. Ein eingehendere Erläuterung von Begriffen finden Sie unter [Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/master/glossary.html).
{:shortdesc}

## Aktueller Status
Der aktuelle Status des Ledgers stellt die neuesten Werte für alle Schlüssel dar, die im zugehörigen Chaintransaktionsprotokoll enthalten sind. Da der aktuelle Status die neuesten Werte aller Schlüssel darstellt, die dem Kanal bekannt sind, wird er auch als World-Status bezeichnet. Chaincode führt Transaktionsvorschläge für Daten mit dem aktuellen Status aus. Der aktuelle Status ändert sich jedes Mal, wenn der Wert eines Schlüssels geändert oder ein neuer Schlüssel hinzugefügt wird. Der aktuelle Status ist für einen Transaktionsablauf kritisch, da das neueste Schlüssel/Wert-Paar vor einer Änderung bekannt sein muss. Peers schreiben die neuesten Werte für jede gültige Transaktion in einem Block im aktuellen Status des Ledgers fest. Der aktuelle Status wird in einer Ledger-Datenbank auf den Peers gespeichert. 

## Anordnungsknoten
Ein Anordnungsserviceknoten ("Orderer"). Eine Blockchain-Netzressource, die Clientauthentifizierungsservices bereitstellt. Darüber hinaus stellt der Anordnungsknoten Services zum Anordnen und übertragen von Transaktionen bereit.

## Anordnungsservice
Jedes Blockchain-Netz erfordert einen Anordnungsservice. Der Anordnungsservice erfasst Transaktionen von Netzmitgliedern, ordnet die Transaktionen und bündelt sie zu Blöcken. Der Anordnungsservice verteilt anschließend die neuen Blöcke an Peers, die wiederum die Blöcke prüfen und den Ledgern in jedem Kanal hinzufügen. Der Anordnungsservice dient darüber hinaus als gemeinsame Bindung für das gesamte Netz. Er enthält das Identitätsmaterial für die Verschlüsselung, das an jedes Mitglied gebunden ist, und authentifiziert die Identität von Clients und Peers für den Zugriff auf das Netz.

## Asset
Materielle oder immaterielle Güter, Services oder Liegenschaften, die als Element dargestellt werden, die im Blockchain-Netz gehandelt werden.

## Benutzer
Ein Benutzer ist ein Teilnehmer an einem Blockchain-Netz, der durch eine Vertrauensbeziehung zu einem vorhandenen Mitglied direkten Zugriff auf das Ledger hat.

## Bewilligung
Der Prozess, durch den Chaincodetransaktionen validiert werden. Bewilligungsregeln werden durch die Angabe von Bewilligungsrichtlinien implementiert.

## Bewilligungsrichtlinie
Definiert die Peerknoten für einen Kanal, die Transaktionen ausführen müssen, die an eine bestimmte Chaincode-Anwendung angehängt sind, sowie die angeforderte Kombination von Antworten (Bewilligungen). Eine Richtlinie könnte zum Beispiel erfordern, dass eine Transaktion von einer Mindestanzahl bewilligender Peers, durch einen Mindestprozentsatz der bewilligenden Peers oder durch alle bewilligenden Peers bewilligt wird, die einer bestimmten Chaincode-Anwendung zugeordnet sind. Richtlinien können je nach Anwendung und gewünschter Ausfallsicherheitstufe gegen schädliches Verhalten unabhängig davon, ob vorsätzlich oder nicht, durch die bewilligenden Peers betreut werden. Eine übergebene Transaktion muss die Bewilligungsrichtlinie erfüllen, bevor sie durch festschreibende Peers als gültig markiert wird. Darüber hinaus ist auch eine bestimmte Bewilligungsrichtlinie zum Installieren und Instanziieren von Transaktionen erforderlich.

## Block
Eine geordnete Gruppe von Transaktionen, die kryptografisch mit dem vorangehenden Block in einem Kanal verknüpft ist.

## Chain
Die Chain (Kette) des Ledgers ist ein Transaktionsprotokoll, das die Struktur von durch Hashwerte verknüpften Blöcken von Transaktionen hat. Peers empfangen Blöcke von Transaktionen aus dem Anordnungsservice, markieren die Transaktionen des Blocks je nach Bewilligungsrichtlinien und Verletzungen des gemeinsamen Zugriffs als gültig oder ungültig und hängen den Block an die Hash-Chain im Dateisystem des Peers an.

## Chaincode
Bei Chaincode, auch als Smart Contract bezeichnet, handelt es sich um Stücke von Software, die eine Gruppe von Funktionen zum Abfragen oder Aktualisieren des Ledgers enthalten.

## Client
Der Client stellt die Entität dar, die im Namen eines Benutzers agiert. Er muss eine Verbindung zu einem Peer herstellen, um mit der Blockchain kommunizieren zu können. Der Client kann die Verbindung zu einem beliebigen Peer seiner Wahl herstellen. Clients erstellen Transaktionen und rufen sie dadurch auf. Der Client übergibt einen tatsächlichen Transaktionsaufruf an die Bewilliger und sendet Transaktionsvorschläge an den Anordnungsservice.

## CouchDB
Ein Dokumentspeicher für die Ledger-Datenbank in Starter Plan-Netzen. CouchDB ist neben LevelDB eine Option für Enterprise Plan-Netze. CouchDB unterstützt die Verwendung von Indizes und ermöglicht Ihnen komplexe Abfragen der Daten auf dem Peer. 

## Dynamische Mitgliedschaft
Ein Mitglied kann dem Netz von einem Benutzer mit der Berechtigung **Registrator** (registrar) dynamisch hinzugefügt werden. Mitglieder werden auch Rollen und Attribute zugeordnet, von denen ihr Zugriff und ihre Berechtigung im Netz gesteuert wird. Weder Rollen noch Attribute können jedoch dynamisch zugeordnet werden. Hyperledger Fabric unterstützt das Hinzufügen oder Entfernen von Mitgliedern, Peers und Anordnungsserviceknoten ohne den Betrieb des Gesamtnetzes zu beeinträchtigen. Die dynamische Mitgliedschaftsänderung ist von entscheidender Bedeutung, wenn Geschäftsbeziehungen aus verschiedenen Gründen angepasst werden und Entitäten hinzugefügt oder entfernt werden müssen.

## Genesis-Block
Der Konfigurationsblock, der ein Blockchain-Netz oder einen Blockchain-Kanal initialisiert und zugleich als erster Block in einer Chain dient.

## HSM
Hardwaresicherheitsmodul (Hardware Security Module). Stellt Verschlüsselung, Schlüsselmanagement und Schlüsselspeicher als verwalteten Service auf Anforderung zur Verfügung. HSM ist eine physische Einheit (Appliance), die die ressourcenintensiven Tasks der Verschlüsselungsverarbeitung abwickelt und die Latenz für Anwendungen verringert. Weitere Informationen finden Sie unter [Hardware Security Module ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/cloud/hardware-security-module).

## Hyperledger Composer
[Hyperledger Composer ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/introduction/introduction.html) ist ein quelloffenes Entwicklungstoolset. Es verwendet eine spezielle Modellierungssprache, die mit JavaScript-Transaktionen und Zugriffssteuerungsregeln kombiniert wird, um ein Blockchain-Unternehmensnetz vollständig zu modellieren. Sie können Hyperledger Composer für die Integration vorhandener Systeme und Daten in Ihre Blockchain-Anwendung verwenden, bevor Sie irgendwelche Elemente in einer realen Blockchain bereitstellen.

## Hyperledger Fabric
[Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/master/) ist ein unternehmensorientiertes Blockchain-Framework, das von der Linux Foundation als Basis für die Entwicklung von Blockchain-Anwendungen oder -Lösungen mit einer modularen Architektur zur Verfügung gestellt wird. Hyperledger Fabric-Komponenten wie Konsens- und Mitgliedschaftsservices sind Plug-and-play-fähig.

## Installation
Der Prozess des Anordnens eines Chaincodes im Dateisystem eines Peers. Sie müssen den Chaincode auf allen Peers installieren, auf denen dieser Chaincode ausgeführt werden soll.

## Instanziieren
Der Prozess des Startens und Initialisierens eines Chaincode-Containers auf einem bestimmten Kanal. Wenn der Chaincode auf den Peers installiert wurde und jeder Peer dem Kanal zugeordnet wurde, muss der Chaincode auf dem Kanal instanziiert werden. Durch die Instanziierung wird die erforderliche Initialisierung des Chaincodes durchgeführt. Dazu gehört die Einstellung der Schlüssel/Wert-Paare, die den ersten World-Status eines Chaincodes bilden. Nach der Instanziierung können Peers, auf denen der Chaincode installiert ist, Chaincode-Aufrufe akzeptieren.

## Kafka
Eine Implementierung eines Konsens-Plug-ins für Hyperledger Fabric, deren Ergebnis ein Cluster mit Anordnungsserviceknoten im Blockchain-Netz ist. Eine Kafka-Implementierung ist für ein Produktionsnetz konzipiert.

## Kanal
Ein Kanal besteht aus einer Untergruppe der Netzmitglieder, die Transaktionen privat ausführen möchten. Kanäle sorgen für Datenisolation und Vertraulichkeit, indem sie es den Mitgliedern eines Kanals ermöglichen, bestimmte Regeln und ein separates Ledger einzurichten, auf das nur Kanalmitglieder Zugriff haben. Peers, bei denen es sich um Knoten handelt, die als Transaktionsendpunkte für Organisationen fungieren, werden mit Kanälen verknüpft.

## Knoten
Die Kommunikationsentität der Blockchain. Es gibt drei Typen von Knoten: Zertifizierungsstellenknoten (CA), Peerknoten und Anordnungsserviceknoten (die Gruppe von Anordnungsservices für einen Kanal).

## Konsens
Ein kooperativer Prozess, durch den die Ledgertransaktionen im gesamten Netz synchron gehalten werden. Der Konsens stellt sicher, dass Ledger nur aktualisiert werden, wenn die richtigen Teilnehmer Transaktionen genehmigen, und dass Ledger mit denselben Transaktionen in derselben Reihenfolge aktualisiert werden. Es gibt mehrere algorithmische Möglichkeiten, den Konsens zu realisieren.

## Ledger
Das Ledger besteht aus einer Literalkette von Blöcken ("chain of blocks"), die die unveränderliche Aufzeichnung von Transaktionen sowie eine Statusdatenbank speichern, in der der aktuelle Status verwaltet wird. Es gibt ein Ledger pro Kanal und Aktualisierungen des Ledgers werden durch den Konsensprozess nach den Richtlinien eines bestimmten Kanals verwaltet.

## Ledger-Datenbank
Daten zum aktuellen Status werden in einer Datenbank auf den Peers gespeichert und können so effizient über den Chaincode gelesen und abgefragt werden. Bei Starter Plan-Netzen wird CouchDB als Ledger-Datenbank verwendet. Enterprise Plan-Netze können LevelDB oder CouchDB verwenden. 

## LevelDB
Ein Schlüssel/Wert-Speicher, der neben CouchDB als Ledger-Datenbankoption für Enterprise Plan-Netze zur Verfügung steht. In LevelDB wird der aktuelle Status als Schlüssel/Wert-Paar gespeichert, die Verwendung von Indizes oder komplexen Abfragen wird nicht unterstützt. 

## Mitglied
Mitglieder in einem Blockchain-Netz, die auch als "Organisationen" bezeichnet werden, bilden ganz ähnlich wie Mitglieder einer beliebigen Gruppe die Struktur des Netzes. Ein Mitglied kann so groß wie ein multinationales Unternehmen oder auch so klein wie eine Einzelperson sein. Mitglieder werden im Netz mit einem Zertifikat eingetragen, das ihnen Berechtigungen zur Verwendung des Netzes als Service-Provider (z. B.: Zertifikate ausstellen, Transaktionen validieren/anordnen) oder als Nutzer erteilt. Das Erstere stellt grundlegende Blockchain-Services bereit, die Services für Transaktionsvalidierung, Transaktionsanordnung und Zertifikatsmanagement einschließen. Nutzermitglieder verwenden das Netz, um Transaktionen für das verteilte Ledger aufzurufen. Mitglieder können mehrere Peers haben.

## MSP
Membership Service Provider.  Eine Gruppe von Verschlüsselungsmechanismen und Protokollen zur Ausstellung und Validierung von Zertifikaten und Entitäten im gesamten Blockchain-Netz. Identitäten, die im Gültigkeitsbereich eines MSP ausgegeben werden, können innerhalb der Regelvalidierungsrichtilinien dieses MSP ausgewertet werden. Die MSP-Komponente wird auf jedem Kanalpeer installiert, um sicherzustellen, dass Transaktionsanforderungen, die an den Peer ausgegeben werden, von einer authentifizierten und autorisierten Benutzeridentität stammen.

## Network Monitor
Das GUI-Dashboard, das von der {{site.data.keyword.blockchainfull_notm}} Platform zum Anzeigen und Verwalten des Blockchain-Netzes bereitgestellt wird.

## Netz
Eine Instanz eines {{site.data.keyword.blockchainfull_notm}} Platform-Service in {{site.data.keyword.cloud_notm}}.

## Netzberechtigungsnachweise
Werden in der Anzeige "APIs" im Network Monitor angezeigt. Berechtigungsnachweise beinhalten Ihren Schlüssel ("key" - Benutzername) und Ihren geheimen Schlüssel ("secret" - Kennwort) in der Swagger-Benutzerschnittstelle. Sie müssen diese Netzberechtigungsnachweise zum Authentifizieren verwenden, bevor Sie die REST-APIs ausprobieren.

## Organisation
Siehe [Mitglied](#member).

## Peer
Eine Blockchain-Netzressource, die die Services zur Ausführung und Validierung von Transaktionen sowie zur Verwaltung von Ledgern bereitstellt. Der Peer führt Chaincode aus und enthält das Transaktionsprotokoll und den aktuellen Status der Assets auf den Netzkanälen, das heißt, des Ledgers. Peers gehören zu Organisationen und werden Kanälen zugeordnet.

## SDK
Hyperledger Fabric unterstützt zwei Software Development Kits (SDKs). Ein Node-SDK und ein Java-SDK.  Das Node-SDK kann über NPM und das Java-SDK über Maven installiert werden.  Die SDKs haben eigene Git-Repositorys, nämlich [Fabric Node SDK ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-sdk-node) und [Fabric Java SDK ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-sdk-java), mit Dokumentation für die verfügbaren APIs. Die Hyperledger Fabric Client-SDKs ermöglichen die Interaktion zwischen Ihrer Clientanwendung und Ihrem Blockchain-Netz.

## Serviceberechtigungsnachweise
Serviceberechtigungsnachweise liegen im JSON-Format vor und enthalten die Informationen zu den API-Endpunkten sowie die Eintrags-IDs (enrollIDs) und die geheimen Schlüssel (secrets) für Ihre Netzressourcen, das heißt für Peers, Anordnungsknoten und Zertifizierungsstellen (CA). Ihre Anwendung interagiert mit Netzressourcen über diese API-Endpunkte.

## SOLO
Eine Implementierung eines Konsens-Plug-ins für Hyperledger Fabric, die zur Folge hat, dass nur ein Anordnungsservice im Blockchain-Netz vorhanden ist. Das Starter Plan-Netz verwendet die SOLO-Implementierung. Eine SOLO-Implementierung ist nicht für ein Produktionsnetz gedacht. Die Alternative zu SOLO ist ein Kafka-Cluster.

## Teilnehmer
Eine Organisation, Einzelperson, Anwendung oder Einheit (Gerät), die mit dem Blockchain-Netz interagiert. Unter dem Teilnehmerbegriff werden zwei verschiedenartige Gruppierungen, nämlich Mitglieder und Benutzer, zusammengefasst.

## Transaktion
Der Mechanismus, über den Teilnehmer im Blockchain-Netz mit Assets interagieren. Eine Transaktion erstellt entweder einen neuen Chaincode oder ruft eine Operation in einem vorhanden Chaincode auf.

## Unternehmensnetz
Eine Definition eines Blockchain-Netzes, die das Datenmodell, die Transaktionslogik und Zugriffssteuerungsregeln für Ihre Blockchain-Lösung enthält. Unternehmensnetzdefinitionen werden mit [Hyperledger Composer](#hyperledger-composer) erstellt. Unternehmensnetzdefinitionen werden in bereitstellbare BNA-Dateien (**.bna** - business network archive) gepackt. 

## Verbindungsprofil
Das Verbindungsprofil ist in der Anzeige "Übersicht" im Network Monitor sichtbar, wenn Sie auf die Schaltfläche **Verbindungsprofil** klicken. Die Informationen sind im JSON-Format verfügbar und enthalten die Informationen zu den API-Endpunkten sowie die Eintrags-IDs (enrollIDs) und die geheimen Schlüssel (secrets) für Ihre Netzressourcen, das heißt für Peers, Anordnungsknoten und Zertifizierungsstellen (CA). Ihre Anwendung interagiert mit Netzressourcen über diese API-Endpunkte.

## World-Status
Siehe [Aktueller Status](#current-state).
## Zertifizierungsstelle (CA)
Zertifizierungsstelle (Certificate Authority). Eine Blockchain-Netzressource, die Zertifikate an alle teilnehmenden Mitglieder ausstellt. Diese Zertifikate stellen die Identität eines Mitglieds dar. Alle Entitäten im Netz (Peers, Anordnungsknoten, Clients usw.) müssen eine Identität haben, um kommunizieren, sich authentifizieren und letztlich Transaktionen ausführen zu können. Diese Identitäten sind für jede direkte Teilnahme am Blockchain-Netz erforderlich. Sie können eine Unternehmensnetzkarte für die Zertifizierungsstelle hinzufügen. Die [CA-Karte](develop_starter.html#developing-business-networks-with-starter-plan) kann anschließend importiert und verwendet werden, um den geheimen Administratorschlüssel für gültige Zertifikate aus der Starter Plan-Zertifizierungsstelle auszutauschen.


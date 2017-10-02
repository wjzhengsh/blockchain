---

copyright:
  years: 2017
lastupdated: "2017-09-20"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan

{{site.data.keyword.blockchainfull}} Platform Enterprise Plan ist die erste verfügbare Mitgliedschaftsoption für Organisationen, die ein Blockchain-Unternehmensnetz einrichten oder einem Blockchain-Unternehmensnetz beitreten wollen. Dieser Plan stellt die zentrale Infrastruktur sowie die Tools und den Support bereit, mit denen Sie ohne großen Aufwand ein sehr sicheres Blockchain-Netz für den Produktionseinsatz bereitstellen können.

Für Mitglieder, die das Netz initiieren werden, stellt IBM eine grafische Benutzerschnittstelle zur Verfügung, die den Netzinitiator durch die grundlegenden Schritte zum Einrichten des Netzes führt. Dies umfasst das Einladen anderer Mitglieder und das Festlegen von Governance-Regeln. Wenn das Netz bereitgestellt wurde, steht eine interaktive grafische Benutzerschnittstelle zur Verfügung, die Ihnen folgende Möglichkeiten bietet: Überwachen des Zustands und der Aktivität des Netzes; Management der zentralen Netzaktivitäten wie z. B. neue Bereitstellungen, Hinzufügen/Entfernen von Mitgliedern, Chaincode-Lebenszyklus und Kanalmanagement; Anfordern technischer Unterstützung. Weitere Informationen zum Abrufen von Unterstützungsfunktionen finden Sie unter [Support](ibmblockchain_support.html).

Die {{site.data.keyword.blockchainfull_notm}} Platform wird mit grundlegenden Hyperledger Fabric-Komponenten erstellt, einschließlich einer Zertifizierungsstelle (CA) und mit mindestens einem Peer (max. 6).  IBM stellt außerdem einen fehlertoleranten (Crash Fault Tolerant, CFT) Kafka-Anordnungsservice für die Netzmitglieder bereit. 

Die Fabric-CA ist die Zertifizierungsstelle, die mit dem Enterprise Plan zur Verfügung gestellt wird. Pro Mitglied werden zwei temporäre Zertifizierungsstellen bereitgestellt, die die Netzmitgliedschaft gewähren. Mithilfe der Zertifizierungsstelle (CA) hat das Mitglied auch die Möglichkeit, die Mitgliedschaft (Zertifikate) an Endbenutzer des Netzes weitergeben.

Das Zuordnen einer Transaktion zu einem Hauptbuch umfasst drei Phasen:  
1. Transaktionssimulation und Bewilligung (Peer)
2. Festlegen einer Anordnung (Anordnungsservice)
3. Validierung und Festschreibung (Peer)

Die Fabric-Peers der Mitglieder sind die Schnittstelle oder das Gateway für Anwendungen zur Ausführung des Chaincodes, um die Geschäftslogik zur Ausführung von Transaktionen für das Hauptbuch bereitzustellen.  Alle Transaktionen müssen bewilligt werden. Die übrigen Mitglieder des Netzes geben diese Bewilligung. Nach der Bewilligung werden die Transaktionen an einen von IBM bereitgestellten Anordnungsservice (Kafka) gesendet.

Neben den zentralen Blockchain-Komponenten bietet die Enterprise-Mitgliedschaftsoption eine Infrastruktur mit sicherer Datenspeicherung und Kommunikation (TLS) sowie hoher Verfügbarkeit.  Während Fabric-Netze diese Infrastrukturressourcen gemeinsam nutzen, wird für die Fabric-Komponentenknoten im Netz eine Isolierung vorgenommen und jeder Knoten zum Schutz der Ausführungsumgebung in einem sicheren Docker-Container ausgeführt.

Der einzige Aspekt, der festgelegt werden muss, ist die Größe der für das Netz erforderlichen Peers. Diese Entscheidung ist abhängig von der Anzahl der erforderlichen Kanäle sowie von der Workload pro Kanal, der Hauptspeicherbelegung und dem Plattenspeicherplatz. Im Folgenden finden Sie die Peergrößen, die über den Enterprise-Serviceplan erhältlich sind, sowie Hinweise zur Auswahl des richtigen Peers.

Verwenden Sie die IBM Blockchain-Plattform für stabilere Bereitstellungen auf Produktionsniveau oder vergleichbarem Niveau. Verwenden Sie den IBM Container-Service oder lokale Download-Images zu Testzwecken. 

Im Rahmen des Enterprise-Plans werden der Anordnungs- und CA-Service bereitgestellt. Die Mitgliedsgebühr beträgt $ 1000, die dem jeweiligen Netz zugeordnete Peer-Gebühr $ 1000. Zur Bereitstellung von Hochverfügbarkeitfunktionen (HA-Funktionalität) müssen Sie einen zusätzlichen Peer erwerben. Beispiel: Für eine Organisation (zugehörige Mitgliedsgebühr $ 1000) mit zwei Peers $ 1000 X 2 Peers) und HA ($ 1000 X 2 HA-Peers) fallen monatlich Gebühren von $ 5000 an.

Melden Sie sich jetzt für die {{site.data.keyword.IBM_notm}} [{{site.data.keyword.blockchainfull_notm}}-Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) an.

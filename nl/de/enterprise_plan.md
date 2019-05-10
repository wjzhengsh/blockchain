---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informationen zum Enterprise Plan
{: #enterprise-plan-about}

Beim {{site.data.keyword.blockchainfull}} Platform Enterprise Plan handelt es sich um ein produktionsbereites Angebot für Organisationen, die ein Blockchain-Netz für reale Geschäftsaktivitäten erstellen oder an einem solchen Netz teilnehmen wollen. Dieser Plan stellt die wichtige Infrastruktur mit Tools und Unterstützung bereit, um ein hochsicheres und produktionsbereites Netz ohne großen Aufwand zu starten. Am 15. Mai 2018 wurde für den Enterprise Plan ein Upgrade von Hyperledger Fabric Version 1.0 auf Version 1.1 durchgeführt. Nach dem 15. Mai 2018 erstellte Netze basieren auf Fabric Version 1.1. Zuvor erstellte Netze weisen jedoch weiterhin die Version 1.0 von Fabric auf.
{:shortdesc}

Der **Enterprise Plan** stellt eine Produktionsumgebung mit hohem Sicherheits- und Unterstützungsniveau dar. Wenn Sie Ihr Netz im Rahmen des Enterprise Plan implementieren, können Sie die folgenden Funktionen nutzen:

* Umgebung mit sehr hohem Sicherheitsniveau und mit zahlreichen Sicherheitsfeatures für Hardware, Firmware und Software.
* Robuste Architektur für Skalierbarkeit, Ausfallsicherheit und Verfügbarkeit.
* Optimierte Leistung und Ausführung auf den weltweit schnellsten Linux-Rechenressourcen.

Der Betrieb Ihres Netzes auf der {{site.data.keyword.blockchainfull_notm}} Platform umfasst Tools und Funktionen zur Vereinfachung administrativer Aufgaben:

* Dashboards für die Überwachung und das Management der Ressourcen im Netz.
* Nahtlose Upgrades des vollständigen Code-Stacks.
* Technische Unterstützung rund um die Uhr (24x7) - im Portal integriert.
* Robuster Sicherheitsstack ohne Zugriffsprivilegien, mit Malware- und Manipulationsschutz, 100-prozentiger Verschlüsselung und vielen weiteren Funktionen für Netze mit sensiblen Daten in regulierten Branchen.
* Unternehmensnetze werden alle 24 Stunden ein Mal an einem anderen Standort gespeichert. Im Falle eines schwerwiegenden Ausfalls können diese Netze am selben Standort oder einem anderen Standort wiederhergestellt werden.

**Hinweise:**
- Der Enterprise Plan bietet eine Produktionsumgebung. Wenn Sie eine Entwicklungs- und Testumgebung benötigen, lesen Sie die [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform ist ein Plattformservice unter {{site.data.keyword.cloud_notm}} und alle Mitgliedschaftsangebote unterliegen den [{{site.data.keyword.cloud_notm}} Services-Bedingungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} Services-Bedingungen") der Service-Level-Agreements (SLAs). Enterprise Plan-Netze werden in einem Rechenzentrum in einer einzigen Region bereitgestellt. Eine Liste der verfügbaren Regionen finden Sie unter [Standorte von {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain?topic=blockchain-ibp-regions-locations#ibp-regions-locations).

Für Mitglieder, die das Netz initiieren werden, stellt IBM eine grafische Benutzerschnittstelle zur Verfügung, die den Netzinitiator durch die grundlegenden Schritte zum Einrichten des Netzes führt. Dies umfasst das Einladen anderer Mitglieder und das Festlegen von Governance-Regeln. Weitere Informationen finden Sie unter [Enterprise Plan-Netz steuern](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan). Wenn das Netz bereitgestellt wurde, steht Ihnen der Network Monitor, eine interaktive grafische Benutzerschnittstelle, zur Verfügung, die folgende Möglichkeiten bietet: Überwachen des Zustands und der Aktivität des Netzes, Management der zentralen Netzaktivitäten wie zum Beispiel neue Bereitstellungen, Hinzufügen und Entfernen von Mitgliedern, Chaincode-Lebenszyklus und Kanalmanagement sowie Anforderung von technischer Unterstützung. Weitere Informationen finden Sie unter [Network Monitor verwenden](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

Melden Sie sich jetzt für die [{{site.data.keyword.blockchainfull_notm}}-Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) an.

Die {{site.data.keyword.blockchainfull_notm}} Platform wird mit grundlegenden Hyperledger Fabric-Komponenten erstellt, zu denen eine Zertifizierungsstelle (CA) und mindestens 1 Peer (maximal 6) gehören.  Der Enterprise Plan stellt außerdem einen fehlertoleranten (Crash Fault Tolerant, CFT) Kafka-Anordnungsservice für die Netzmitglieder bereit.

Die Fabric-CA ist die Zertifizierungsstelle, die mit dem Enterprise Plan zur Verfügung gestellt wird. Pro Mitglied werden zwei Zwischenzertifizierungsstellen bereitgestellt, die die Netzmitgliedschaft gewähren. Mithilfe der Zertifizierungsstelle (CA) hat das Mitglied auch die Möglichkeit, die Mitgliedschaft an Benutzer des Netzes weiterzugeben.

Das Zuordnen einer Transaktion zu einem Ledger umfasst drei Phasen:
1. Transaktionssimulation und Bewilligung (Peer)
2. Festlegen einer Anordnung (Anordnungsservice)
3. Validierung und Festschreibung (Peer)

Die Fabric-Peers der Mitglieder sind die Schnittstelle oder das Gateway für Anwendungen zur Ausführung des Chaincodes, um die Geschäftslogik zur Ausführung von Transaktionen für das Ledger bereitzustellen. Alle Transaktionen müssen bewilligt werden. Die übrigen Mitglieder des Netzes geben diese Bewilligung. Nach der Bewilligung werden die Transaktionen an einen von IBM bereitgestellten Anordnungsservice gesendet.

Neben den zentralen Blockchain-Komponenten bietet die Enterprise-Mitgliedschaftsoption eine Infrastruktur mit sicherer Datenspeicherung und Kommunikation (TLS) sowie hoher Verfügbarkeit.  Während Fabric-Netze diese Infrastrukturressourcen gemeinsam nutzen, wird für die Fabric-Komponentenknoten im Netz eine Isolierung vorgenommen und jeder Knoten in einem sicheren Docker-Container ausgeführt, der die Ausführungsumgebung schützt.

Der einzige Aspekt, der festgelegt werden muss, ist die Größe der für das Netz erforderlichen Peers. Diese Entscheidung ist abhängig von der Anzahl der erforderlichen Kanäle sowie von der Workload pro Kanal, der Hauptspeicherbelegung und dem Plattenspeicherplatz.

Sie sollten den Enterprise Plan zur Einrichtung stabilerer, produktionsgeeigneter bzw. produktionsnaher Bereitstellungen verwenden. Verwenden Sie zu Testzwecken den [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) oder [installieren Sie Docker-Images lokal](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Preisstruktur
{: #enterprise-plan-about-pricing}

Zur Verwendung von Enterprise Plan müssen Netzmitglieder eine monatliche Mitgliedsgebühr von 1.000 $ sowie zusätzlich monatlich 1.000 $ für jeden ihrer Peers im Netz bezahlen.  Die monatlichen Gebühren werden anteilmäßig nach Tagen in Rechnung gestellt.  Beispiel: Ein Mitglied (zugeordnete Mitgliedsgebühr 1.000 $) von zwei Peers (Peergebühr 1.000 $ x 2 Peers) muss 3.000 $ pro Monat bezahlen.  Wenn der Monat 30 Tage hat, bezahlt das Mitglied 100 $ (3.000 $/30) pro Tag.  Falls hohe Verfügbarkeit (HA) erforderlich ist, müssen Sie beachten, dass Sie die Anzahl der erforderlichen Peers verdoppeln müssen, um die HA-Funktionalität bereitzustellen.

Netzmitglieder können ihre Rechnung über ihre eigenen {{site.data.keyword.cloud_notm}}-Konten bezahlen, die den Bereich zum Erstellen der Netzinstanz enthalten. Alternativ kann ein Netzmitglied die Rechnung für alle Mitglieder im Netz übernehmen. Weitere Informationen zur Bezahlung für Blockchain-Netze finden Sie unter [Bezahlung für das Netz](/docs/services/blockchain/howto/paying_mode.html#paying-mode).

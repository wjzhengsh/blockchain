---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informationen zum Enterprise Plan

Beim {{site.data.keyword.blockchainfull}} Platform Enterprise Plan handelt es sich um ein produktionsbereites Angebot für Organisationen, die ein Blockchain-Netz für reale Geschäftsaktivitäten erstellen oder an einem solchen Netz teilnehmen wollen. Dieser Plan stellt die wichtige Infrastruktur mit Tools und Unterstützung bereit, um ein hochsicheres und produktionsbereites Netz ohne großen Aufwand zu starten. Am 15. Mai 2018 wurde für den Enterprise Plan ein Upgrade von Hyperledger Fabric Version 1.0 auf Version 1.1 durchgeführt. Nach dem 15. Mai 2018 erstellte Netze basieren auf Fabric Version 1.1. Zuvor erstellte Netze weisen jedoch weiterhin die Version 1.0 von Fabric auf.
{:shortdesc}

**Hinweis:** Mit dem {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan wird eine Produktionsumgebung bereitgestellt. Wenn Sie eine Entwicklungs- und Testumgebung benötigen, lesen Sie die [Informationen zum Starter Plan](starter_plan.html).

Für Mitglieder, die das Netz initiieren werden, stellt IBM eine grafische Benutzerschnittstelle zur Verfügung, die den Netzinitiator durch die grundlegenden Schritte zum Einrichten des Netzes führt. Dies umfasst das Einladen anderer Mitglieder und das Festlegen von Governance-Regeln. Weitere Informationen finden Sie unter [Enterprise Plan-Netz steuern](get_start.html). Wenn das Netz bereitgestellt wurde, steht Ihnen der Network Monitor, eine interaktive grafische Benutzerschnittstelle, zur Verfügung, die folgende Möglichkeiten bietet: Überwachen des Zustands und der Aktivität des Netzes, Management der zentralen Netzaktivitäten wie zum Beispiel neue Bereitstellungen, Hinzufügen und Entfernen von Mitgliedern, Chaincode-Lebenszyklus und Kanalmanagement sowie Anforderung von technischer Unterstützung. Weitere Informationen zum Network Monitor finden Sie unter [Enterprise Plan-Netz betreiben](v10_dashboard.html).

Melden Sie sich jetzt für die [{{site.data.keyword.blockchainfull_notm}}-Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) an.

Die {{site.data.keyword.blockchainfull_notm}} Platform wird mit grundlegenden Hyperledger Fabric-Komponenten erstellt, zu denen eine Zertifizierungsstelle (CA) und mindestens 1 Peer (maximal 6) gehören.  Der Enterprise Plan stellt außerdem einen fehlertoleranten (Crash Fault Tolerant, CFT) Kafka-Anordnungsservice für die Netzmitglieder bereit.

Die Fabric-CA ist die Zertifizierungsstelle, die mit dem Enterprise Plan zur Verfügung gestellt wird. Pro Mitglied werden zwei temporäre Zertifizierungsstellen bereitgestellt, die die Netzmitgliedschaft gewähren. Mithilfe der Zertifizierungsstelle (CA) hat das Mitglied auch die Möglichkeit, die Mitgliedschaft (Zertifikate) an Benutzer des Netzes weiterzugeben.

Das Zuordnen einer Transaktion zu einem Ledger umfasst drei Phasen:  
1. Transaktionssimulation und Bewilligung (Peer)
2. Festlegen einer Anordnung (Anordnungsservice)
3. Validierung und Festschreibung (Peer)

Die Fabric-Peers der Mitglieder sind die Schnittstelle oder das Gateway für Anwendungen zur Ausführung des Chaincodes, um die Geschäftslogik zur Ausführung von Transaktionen für das Ledger bereitzustellen.  Alle Transaktionen müssen bewilligt werden. Die übrigen Mitglieder des Netzes geben diese Bewilligung. Nach der Bewilligung werden die Transaktionen an einen von IBM bereitgestellten Anordnungsservice (Kafka) gesendet.

Neben den zentralen Blockchain-Komponenten bietet die Enterprise-Mitgliedschaftsoption eine Infrastruktur mit sicherer Datenspeicherung und Kommunikation (TLS) sowie hoher Verfügbarkeit.  Während Fabric-Netze diese Infrastrukturressourcen gemeinsam nutzen, wird für die Fabric-Komponentenknoten im Netz eine Isolierung vorgenommen und jeder Knoten in einem sicheren Docker-Container ausgeführt, der die Ausführungsumgebung schützt.

Der einzige Aspekt, der festgelegt werden muss, ist die Größe der für das Netz erforderlichen Peers. Diese Entscheidung ist abhängig von der Anzahl der erforderlichen Kanäle sowie von der Workload pro Kanal, der Hauptspeicherbelegung und dem Plattenspeicherplatz.

Sie sollten den Enterprise Plan zur Einrichtung stabilerer, produktionsgeeigneter bzw. produktionsnaher Bereitstellungen verwenden. Zu Testzwecken verwenden Sie den [Starter Plan](starter_plan.html), [entwickeln in IBM Container Service](https://ibm-blockchain.github.io/) oder [installieren Docker-Images lokal](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Preisstruktur  
Zur Verwendung des Enterprise-Plans müssen Netzmitglieder eine monatliche Mitgliedsgebühr von 1.000 $ sowie zusätzlich monatlich 1.000 $ für jeden ihrer Peers im Netz bezahlen.  Die monatlichen Gebühren werden anteilmäßig nach Tagen in Rechnung gestellt.  Beispiel: Ein Mitglied (zugeordnete Mitgliedsgebühr 1.000 $) von zwei Peers (Peergebühr 1.000 $ x 2 Peers) muss 3.000 $ pro Monat bezahlen.  Wenn der Monat 30 Tage hat, bezahlt das Mitglied 100 $ (3.000 $/30) pro Tag.  Falls hohe Verfügbarkeit (HA) erforderlich ist, müssen Sie beachten, dass Sie die Anzahl der erforderlichen Peers verdoppeln müssen, um die HA-Funktionalität bereitzustellen.

Netzmitglieder können ihre Rechnung über ihre eigenen {{site.data.keyword.cloud_notm}}-Konten bezahlen, die den Bereich zum Erstellen der Netzinstanz enthalten.  Alternativ kann ein Netzmitglied die Rechnung für alle Mitglieder im Netz übernehmen.  Weitere Informationen zur Bezahlung für Blockchain-Netze finden Sie unter [Bezahlung für das Netz](howto/pay_for_the_network.html).

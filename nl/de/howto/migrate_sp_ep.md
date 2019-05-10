---

copyright:
  years: 2018,2019
lastupdated: "2019-04-17"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Von Starter Plan auf Enterprise Plan migrieren
{: #migrate_starter_to_enterprise}

{{site.data.keyword.blockchainfull}} Platform [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) bietet Ihnen eine Test- und Entwicklungsumgebung zur Ausführung Ihrer Machbarkeitsnachweise (PoC = Proof of Concept) und Demos sowie zum Testen Ihres Chaincodes und Ihrer Anwendungen. Wenn Sie bereit sind, um Ihre Entwicklungsergebnisse in eine Produktionsumgebung zu überführen, dann können Sie eine Migration von einem Starter Plan-Netz auf ein [Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about)-Netz durchführen.
{:shortdesc}

Enterprise Plan-Netze umfassen die folgenden produktionsbereiten Funktionen, um Sie bei der Verarbeitung der Produktionsworkload zu unterstützen:

- Blockchain-Netze, die gegenüber Starter Plan-Netzen an zusätzlichen globalen Positionen bereitgestellt werden.
- Keine Speicherbegrenzung (20 GB für Starter Plan).
- Verbessertes CPU- und RAM-Management zur Sicherstellung des reibungslosen Betriebs aller Netze.
- Erweiterte Sicherheitseinrichtungen einschließlich der folgenden Funktionen:
  - Secure Service Container (SSC) stellt sicher, dass das Blockchain-Image nicht manipuliert und zu jedem beliebigen Zeitpunkt geladen werden kann und dass die Vertraulichkeit des Appliance-Codes und der entsprechenden Daten sowohl während der Nutzung als auch während der Speicherung gewährleistet bleibt.
  - [Hardware Secure Module (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) für das Schlüsselmanagement und die Speicherung von Schlüsseln.
  - Zeitnahe und standortunabhängige Plattenverschlüsselung.
- Hochverfügbarkeit, Disaster-Recovery, Fehlertoleranz bei Abstürzen und schrittweise Upgrades.
- Optionale erweiterte Unterstützung.

## Wichtige Hinweise
{: #migrate_starter_to_enterprise_considerations}

Bevor Sie die Migration Ihres Starter Plan-Netzes auf ein Enterprise Plan-Netz durchführen, sollten Sie die folgenden wichtigen Hinweise lesen.

- **Preisstruktur:** Die monatliche Gebühr Ihrer Organisation für die Nutzung eines Enterprise Plan-Netzes umfasst eine pro Instanz berechnete Mitgliedschaftsgebühr von 1000 Dollar und eine pro Peer berechnete Peergebühr von ebenfalls 1000 Dollar. Weitere Informationen zu diesem Thema finden Sie im Abschnitt zur [Enterprise Plan-Preisstruktur](/docs/services/blockchain/howto/pricing.html#ibp-pricing-enterprise-plan).
- **Hyperledger Fabric-Version:** Enterprise Plan-Netze arbeiten mit Hyperledger Fabric v1.1. Starter Plan-Netze werden unter Hyperledger Fabric v1.2 ausgeführt.
- **Betroffene Ressourcen:** Chaincode (Smart Contracts), Clientanwendungen. Achten Sie auch hier darauf, ob Ihr Chaincode eine Komponente oder Funktionalität von Fabric v1.2 nutzt, die nicht mit Netzen von v1.1 kompatibel ist.
- **Erforderliche Zeit:** Die Migration eines Basisnetzes von der Starter Plan-Version auf die Enterprise Plan-Version nimmt mindestens einen halben Tag in Anspruch.
- **Vorhandene Ledgerdaten** können nicht aus Starter Plan-Netzen in Enterprise Plan-Netze verschoben werden, da Testdaten nicht in einer Produktionsumgebung vorhanden sein sollten.
- **Hyperledger Composer:** IBM bietet keinen Support für Netze, die Hyperledger Composer in Produktionsumgebungen nutzen, inklusive Composer-Befehlszeilenschnittstelle, JavaScript-APIs, REST-Server und Web Playground.

**Hinweis:** Ein *Basisnetz* umfasst zwei Organisationen mit zwei Peers, einem einzelnen Kanal und einer einzelnen Chaincode-Datei. Die zur Migration tatsächlich benötigte Zeit kann abhängig von der Größe und Komplexität der Netzkomponenten, die im Enterprise Plan-Netz benötigt werden, variieren.

## Migrationsprüfliste
{: #migrate_starter_to_enterprise_checklist}

Zur Vorbereitung der Überführung eines Starter Plan-Netzes in ein Enterprise Plan-Netz sind verschiedene Tasks erforderlich. Ausführliche Anweisungen hierzu erhalten Sie in nachfolgenden Abschnitten. Im Folgenden ist eine Zusammenfassung aufgeführt:

- Erstellen eines Enterprise Plan-Netzes.
- Neuerstellen der gewünschten Konfiguration der Organisationen und Peers auf Basis Ihres Starter Plan-Netzes.
- Identifizieren des (in Go oder Node geschriebenen) Chaincodes, der im Enterprise Plan-Netz ausgeführt werden soll.
- Aktualisieren der Clientanwendungen mit neuen Informationen zum API-Endpunkt für das Enterprise Plan-Netz.

### Enterprise Plan-Netz erstellen
{: #migrate_starter_to_enterprise_create_network}

Vor der Migration müssen Sie ein Enterprise Plan-Netz erstellen. Weitere Informationen hierzu finden Sie im Abschnitt zum [Erstellen eines Enterprise Plan-Netzes](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network).

### Netzkonfiguration neu erstellen
{: #migrate_starter_to_enterprise_config_network}

Sie können die Konfiguration von Organisationen (Mitgliedern), Kanälen und Peers Ihres Starter Plan-Netzes in Ihrem Enterprise Plan-Netz neu erstellen. Sie können die Network Monitor-Benutzerschnittstelle verwenden, um diese Netzressourcen neu zu erstellen, indem Sie die geeigneten Organisationen einladen, Kanäle und Peers erstellen. (Beachten Sie hierbei, dass ein **Wechsel** der Organisationen nicht möglich ist, wie dies in Starter der Fall ist. Außerdem ist zu beachten, dass eingeladene Organisationen eigene Peers erstellen müssen.)

1. Melden Sie sich bei Ihrem Enterprise Plan-Netz unter {{site.data.keyword.cloud_notm}} an und rufen Sie den Network Monitor auf.
2. Erstellen Sie Organisationen (Mitglieder) in der Anzeige "Mitglieder", Kanäle in der Anzeige "Kanäle" und Peers in der Anzeige "Übersicht" neu. Weitere Informationen zur Erstellung von Netzressourcen finden Sie im Abschnitt zum [Network Monitor verwenden](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-overview).
3. Konfigurieren Sie Kanäle, indem Sie Mitglieder hinzufügen und Kanalrichtlinien auf die gleiche Weise festlegen, wie dies im Starter Plan-Netz der Fall ist.

**Hinweis:** Zur Erzielung einer hohen Verfügbarkeit müssen Sie mindestens zwei Peers für Ihre Organisation erstellen, diese demselben Kanal hinzufügen und Clientanwendungen korrekt codieren, sodass eine Umschaltung von einem Peer auf den nächsten bei Bedarf möglich ist.

### Chaincode migrieren
{: #migrate_starter_to_enterprise_cc}

Chaincode wird extern in Ihrer lokalen Umgebung entwickelt und von Ihren Clientanwendungen aufgerufen. Zum Installieren und Instanziieren von Chaincode, der in Ihrem Starter Plan-Netz getestet wurde, auf ausgewählten Peers im Enterprise Plan-Netz müssen Sie die Anweisungen im Abschnitt [Installieren, Instanziieren und Aktualisieren von Chaincode](/docs/services/blockchain
/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc).

### Clientanwendungen aktualisieren
{: #migrate_starter_to_enterprise_app}

Sie müssen vorhandene Clientanwendungen, die im Starter Plan-Netz getestet wurden, mit neuen Informationen zum API-Endpunkt für das Enterprise Plan-Netz aktualisieren. Weitere Informationen finden Sie im Abschnitt zum [Abrufen von Netzberechtigungsnachweisen und Verbindungsprofilen](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-retrieve-credentials).

In Hinblick auf die Hochverfügbarkeit müssen die Clientanwendungen erkennen, wenn ein Peer nicht antwortet, sodass Transaktionen an einen anderen Peer weitergeleitet werden.

## Verwendung vorhandener Starter Plan-Netze
{: #migrate_starter_to_enterprise_existing_network}

Sie können bereits vorhandene Starter Plan-Netze als Sandboxumgebung weiterhin verwenden, um neue Projekte zu entwickeln und Änderungen an bereits vorhandenem Chaincode zu testen. Zusätzlich hierzu können Sie die Ledgertestdaten, die nicht auf das Enterprise Plan-Netz migriert werden, in Ihrem Starter Plan-Netz beibehalten.

Löschen Sie Ihr Starter-Netz erst, nachdem Sie alle Tests abgeschlossen und überprüft haben, dass das System ordnungsgemäß ausgeführt wird. Sobald Sie das Starter-Netz und die darin enthaltenen Ledgerdaten nicht mehr benötigen, können Sie das Netz problemlos löschen. Weitere Informationen hierzu finden Sie im Abschnitt zum [Löschen oder Beibehalten eines Netzes](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-delete-network).

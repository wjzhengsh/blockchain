---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Netz entwickeln
{: #develop-the-network}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Bei der Entwicklung von Blockchain-Lösungen mithilfe von {{site.data.keyword.blockchainfull}} Platform kann das quelloffene Hyperledger Composer-Entwicklungstoolset genutzt werden. Hyperledger Composer verwendet eine speziell entwickelte Modellierungssprache, die in Kombination mit JavaScript-Transaktionen und Zugriffssteuerungsregeln zum Modellieren eines vollständigen Blockchain-Unternehmensnetzes _vor_ der Bereitstellung in einem realen Blockchain-Netz verwendet wird.
{:shortdesc}

**Anmerkung:** Mit dem Starter Plan werden Beispielanwendungen bereitgestellt, die Sie ausprobieren und zum Lernen verwenden können. Sie können Beispielanwendungen im Starter Plan ausführen, bevor Sie ein angepasstes Unternehmensnetz entwickeln. Weitere Informationen finden Sie unter [Informationen zum Starter Plan](starter_plan.html).

Dieses Lernprogramm führt Sie durch die Entwicklung einer {{site.data.keyword.blockchain}}-Lösung, bei der zu Beginn ein Unternehmensnetz erstellt und modelliert und anschließend diese Lösung in einem aktiven Blockchain-Netz bereitgestellt wird.

Die folgenden Schritte beschreiben kurz die grundlegende Entwicklung einer {{site.data.keyword.blockchain}}-Lösung, die auf {{site.data.keyword.blockchainfull_notm}} Platform bereitgestellt wird:

1. Erstellen Sie die Datenstruktur, die die Unternehmensnetzdefinition enthalten soll. Das Unternehmensnetz wird durch Assets, Teilnehmer und Transaktionen mithilfe der [Modellierungssprache](https://hyperledger.github.io/composer/latest/reference/cto_language) definiert.

2. Modellieren Sie das Unternehmensnetz, indem Sie die Assetklassen, Teilnehmerklassen, Transaktionsklassen, Ereignisklassen und Zugriffssteuerungsregeln definieren, die nach der Bereitstellung verwendet werden.

3. Packen Sie das definierte Unternehmensnetz in eine einzelne bereitstellbare Datei.

4. Stellen Sie das gepackte Unternehmensnetz auf {{site.data.keyword.blockchainfull_notm}} Platform bereit. Wenn das Unternehmensnetz bereitgestellt ist, kann ein REST-Server generiert werden, der eine Integration mit externen Anwendungen ermöglicht.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die Entwicklungstools von [{{site.data.keyword.blockchainfull_notm}}: Develop](./develop_install.html) installiert haben.

## Schritt 1: Unternehmensnetzstruktur erstellen

Ein wichtiges Konzept für die Entwicklung von {{site.data.keyword.blockchain}}-Lösungen ist die **Unternehmensnetzdefinition** (BND - Business Network Definition). Die BND definiert das Datenmodell, die Transaktionslogik und die Zugriffssteuerungsregeln für Ihre Blockchain-Lösung. Zum Erstellen einer BND müssen Sie eine entsprechende Projektstruktur auf der Platte erstellen.

Die einfachste Möglichkeit für den Einstieg ist die Verwendung des Yeoman-Generators, um ein Gerüst für das Unternehmensnetz zu erstellen. Der Yeoman-Generator erstellt ein Verzeichnis, das alle Komponenten eines Unternehmensnetzes enthält und für den jeweiligen Anwendungsfall geändert werden kann.

1. Erstellen Sie ein neues Verzeichnis für Ihr Projekt und navigieren Sie in der Befehlszeile in dieses Verzeichnis.

2. Verwenden Sie in diesem neuen Verzeichnis Yeoman, um ein Unternehmensnetzgerüst zu erstellen. Der folgende Befehl erfordert einen Namen, eine Beschreibung, einen Autorennamen, eine E-Mail-Adresse des Autors, eine Lizenzauswahl und einen Namensbereich für das Unternehmensnetz:
    ```
    yo hyperledger-composer:businessnetwork
    ```
    {:codeblock}

3. Geben Sie `tutorial-network` als Netznamen und die gewünschten Informationen für Beschreibung, Autorenname und E-Mail-Adresse des Autors ein.

4. Wählen Sie `Apache-2.0` als Lizenz aus.

5. Wählen Sie `org.acme.biznet` als Namensbereich aus.

## Schritt 2: Unternehmensnetz definieren

Ein Unternehmensnetz besteht aus Assets, Teilnehmern, Transaktionen, Zugriffssteuerungsregeln sowie optional aus Ereignissen und Abfragen. Das Gerüst für das Unternehmensnetz, das in Schritt 1 erstellt wird, enthält eine Modelldatei (`.cto`), in der die Klassendefinitionen für alle Assets, Teilnehmer und Transaktionen in dem Unternehmensnetz enthalten sind. Das Gerüst für das Unternehmensnetz enthält außerdem ein Zugriffssteuerungsdokument (`permissions.acl`) mit den grundlegenden Zugriffssteuerungsregeln, eine Scriptdatei (`logic.js`), die die Transaktionsprozessorfunktionen enthält, sowie eine Paketdatei (`package.json`), die Metadaten des Unternehmensnetzes enthält.

### Assets, Teilnehmer und Transaktionen modellieren

Das erste Dokument, das aktualisiert werden muss, ist die Modelldatei (`.cto`). Die Modelldatei wird mithilfe der [Hyperledger Composer-Modellierungssprache ](https://hyperledger.github.io/composer/latest/reference/cto_language) geschrieben. Die Modelldatei enthält die Definitionen der einzelnen Klassen von Assets, Transaktionen, Teilnehmern und Ereignissen. Sie erweitert implizit das Systemmodell, das in der Dokumentation zur Modellierungssprache beschrieben wird.

1. Öffnen Sie die Modelldatei `org.acme.biznet.cto` in einem Editor Ihrer Wahl. Sie befindet sich im Ordner `models` des Unternehmensnetzes, das Sie im letzten Schritt erstellt haben.

2. Ersetzen Sie den Inhalt durch die folgenden Informationen:

    ```
        /**
         * Mein Warenhandelsnetz
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }
    ```
    {:codeblock}

3. Speichern Sie die Änderungen an der Datei `org.acme.biznet.cto`.


### JavaScript-Transaktionslogik hinzufügen

In der Modelldatei wurde eine Transaktion `Trade` definiert, die eine Beziehung zu einem Asset und einen Teilnehmer angibt. Die Datei für die Transaktionsprozessorfunktionen enthält die JavaScript-Logik, um die Transaktionen auszuführen, die in der Modelldatei definiert sind.

Die Transaktion `Trade` ist nur dazu gedacht, die ID des Assets `Commodity`, das gehandelt wird, zu akzeptieren und die ID des Teilnehmers `Trader` als neuen Eigner festzulegen.

1. Öffnen Sie die Scriptdatei `logic.js` im Verzeichnis `lib`.

2. Ersetzen Sie den Inhalt durch die folgenden Informationen:

    ```
        /**
         * Handel einer Ware (Commodity) von einem Händler (Trader) zum anderen verfolgen
         * @param {org.acme.biznet.Trade} trade - der zu verarbeitende Handel
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }
    ```
    {:codeblock}

3. Speichern Sie die Änderungen an der Datei `logic.js`.

### Zugriffssteuerung hinzufügen

Die Zugriffssteuerung ist ein wesentlicher Bestandteil eines Unternehmensnetzes. Jedes Unternehmensnetz muss eine Zugriffssteuerungsdatei mit dem Namen `permissions.acl` besitzen. Immer wenn ein Prozess in einem bereitgestellten Unternehmensnetz aufgerufen wird, wird die Zugriffssteuerungsliste geprüft, um sicherzustellen, dass die aufrufende Identität oder der aufrufende Teilnehmer die Operation aufrufen kann.

Richten Sie für die Zwecke dieses Lernprogramms eine einfache Zugriffssteuerungsliste (ACL - Access Control List) ein. Beachten Sie, dass diese Liste nicht in einer Produktionsumgebung bereitgestellt werden sollte.

1. Erstellen Sie eine Datei `permissions.acl` im Verzeichnis `tutorial-network`.

2. Fügen Sie der Datei `permissions.acl` die folgenden Zugriffssteuerungsregeln hinzu:

    ```
        /**
         * Zugriffssteuerungsregeln für tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }
    ```
    {:codeblock}

3. Speichern Sie die Änderungen an der Datei `permissions.acl`.

## Schritt 3: Unternehmensnetz packen

Wenn das Unternehmensnetz definiert ist, muss es in eine bereitstellbare Unternehmensnetzarchivdatei (`.bna`) gepackt werden.

1. Navigieren Sie in der Befehlszeile in das Verzeichnis `tutorial-network`.

2. Führen Sie im Verzeichnis `tutorial-network` den folgenden Befehl aus:

    ```
    composer archive create -t dir -n .
    ```
    {:codeblock}

Durch die Ausführung des Befehls wird eine Unternehmensnetzarchivdatei (`tutorial-network@0.0.1.bna`) im Verzeichnis `tutorial-network` erstellt.

## Schritt 4: Unternehmensnetz bereitstellen und REST-Server generieren

Stellen Sie das Unternehmensnetz auf der {{site.data.keyword.blockchainfull_notm}} Platform bereit, indem Sie den [Starter Plan](./develop_starter.html) oder den [Enterprise Plan](./develop_enterprise.html) verwenden.

Nach der Bereitstellung des Unternehmensnetzes auf der {{site.data.keyword.blockchainfull_notm}} Platform kann ein spezieller REST-Server generiert werden. Der REST-Server untersucht den Inhalt des Unternehmensnetzes und erstellt REST-konforme API-Aufrufe, Transaktionen und Teilnehmer des Netzes. Die Anweisungen zum Generieren eines REST-Servers sind in der [Hyperledger Composer-Dokumentation](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api) enthalten.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Entwicklungsumgebung für Unternehmensnetze installieren
{: #installing-a-development-environment}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Führen Sie die nachfolgenden Anweisungen aus, um die Entwicklungstools von {{site.data.keyword.blockchainfull}} Platform: Develop zum Erstellen und Testen von Unternehmensnetzen abzurufen. Zur Bereitstellung hoher Flexibilität und der maximalen Anzahl von Entwicklungs-, Test- und Bereitstellungsszenarios werden die Entwicklungstools in Form von NPM-Modulen geliefert, die über die Befehlszeile gesteuert werden.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die folgenden Voraussetzungen installieren:

- Ubuntu Linux 14.04 / 16.04 LTS (beide 64-Bit) oder Mac OS 10.12
- Node Version 8.9 oder höher (Version 9 wird nicht unterstützt)
- npm Version 5.x
- git Version 2.9.x oder höher
- Python Version 2.7.x
- Codeeditor Ihrer Wahl wie zum Beispiel VSCode

Wenn Sie den Enterprise Plan verwenden, installieren Sie die folgenden zusätzlich erforderlichen Produkte:

- Docker Engine Version 17.03 oder höher
- Docker-Compose Version 1.8 oder höher

Wenn Sie VSCode verwenden, ist eine Hyperledger Composer-Erweiterung für VSCode [hier](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client) verfügbar.

## Schritt 1: Befehlszeilentools installieren

Zu den Befehlszeilentools gehören **composer-cli**, das Hauptbefehlszeilenmodul von {{site.data.keyword.blockchainfull_notm}} Platform: Develop, **generator-hyperledger-composer**, **composer-rest-server** und **Yeoman**. Diese Module werden zum Generieren von Unternehmensnetzen, speziellen REST-Servern und Angular-Anwendungen verwendet. Welche Version Sie installieren, ist von der Hyperledger Fabric-Version abhängig, mit der Ihr Starter Plan oder Enterprise Plan ausgeführt wird. Ihre Fabric-Version können Sie ermitteln, indem Sie das [Fenster "Netzvorgaben"](/docs/services/blockchain/v10_dashboard.html#network-preferences) im Network Monitor öffnen.

1. Installieren Sie zur Verwendung bei neuen Starter Plan-Instanzen, die Hyperledger Fabric v1.2.1 ausführen, **composer-cli** mit dem folgenden Befehl:

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Installieren Sie zur Verwendung bei Enterprise Plan- und Starter Plan-Instanzen, die Hyperledger Fabric v1.1, ausführen,  **composer-cli** mit dem folgenden Befehl:

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Installieren Sie zur Verwendung bei neuen Starter Plan-Instanzen, die Hyperledger Fabric v1.2.1 ausführen,  **composer-rest-server** mit dem folgenden Befehl:

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Installieren Sie zur Verwendung bei Enterprise Plan- und Starter Plan-Instanzen, die Hyperledger Fabric v1.1, ausführen,  **composer-rest-server** mit dem folgenden Befehl:

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 Verwenden Sie das Modul **composer-rest-server** zum Erstellen eines REST-Servers auf Ihrer Maschine, um Ihre Unternehmensnetze als REST-konforme APIs verfügbar zu machen.

3. Installieren Sie zur Verwendung bei neuen Starter Plan-Instanzen, die Hyperledger Fabric v1.2.1 ausführen,  **generator-hyperledger-composer** mit dem folgenden Befehl:

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Installieren Sie zur Verwendung bei Enterprise Plan- und Starter Plan-Instanzen, die Hyperledger Fabric v1.1, ausführen,  **generator-hyperledger-composer** mit dem folgenden Befehl:

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Verwenden Sie **generator-hyperledger-composer**, um Gerüststrukturen für Unternehmensnetze, Modelle und eine Angular-Anwendung zu generieren.

4. Yeoman ist ein Tool zum Generieren von Anwendungen, das `generator-hyperledger-composer` verwendet:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Schritt 2: Lokale Playground-Umgebung installieren

Hyperledger Composer Playground ist eine Benutzerschnittstelle, die als simulierte Umgebung zum Testen eines Unternehmensnetzes verwendet werden kann. Installieren Sie die Playground-Umgebung mit dem folgenden Befehl:

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## Optional: IDE einrichten

Sie können die Playground-Umgebung zum Entwickeln, Bearbeiten und Testen von Unternehmensnetzen verwenden. Wenn Sie jedoch ein Unternehmensnetz in einer integrierten Entwicklungsumgebung (IDE) entwickeln möchten, ist eine VSCode-Erweiterung verfügbar, die Funktionen zur Syntaxhervorhebung für die Hyperledger Composer-Modellierungssprache hinzufügt.

1. Installieren Sie VSCode über die folgende URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Öffnen Sie VSCode, wechseln Sie zu Extensions (Erweiterungen), suchen Sie nach der Hyperledger Composer-Erweiterung im Marktplatz und installieren Sie sie.


### Hyperledger Fabric starten und stoppen

Es steht eine Gruppe von Scripts im Verzeichnis `fabric-tools` für die Steuerung der Hyperledger Fabric-Instanz zur Verfügung. Sie können die Laufzeit mit den Scripts `~/fabric-tools/stopFabric.sh` und `~/fabric-tools/startFabric.sh` stoppen und starten.

Das Script `~/fabric-tools/teardownFabric.sh` macht die Erstellung einer neuen PeerAdmin-Karte erforderlich, wenn die Umgebung das nächste Mal gestartet wird.

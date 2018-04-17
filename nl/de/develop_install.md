---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Entwicklungsumgebung installieren
{: #installing-a-development-environment}

Führen Sie die nachfolgenden Anweisungen aus, um die Entwicklungstools von {{site.data.keyword.blockchainfull}} Platform: Develop zum Erstellen und Testen von Unternehmensnetzen abzurufen. Zur Bereitstellung hoher Flexibilität und der maximalen Anzahl von Entwicklungs-, Test- und Bereitstellungsszenarios werden die Entwicklungstools in Form von NPM-Modulen geliefert, die über die Befehlszeile gesteuert werden.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die folgenden Voraussetzungen installieren:

- Ubuntu Linux 14.04 / 16.04 LTS (beide 64-Bit) oder Mac OS 10.12
- Node Version 8.9 oder höher (Version 9 wird nicht unterstützt)
- npm Version 5.x
- git Version 2.9.x oder höher
- Python Version 2.7.x
- Docker Engine Version 17.03 oder höher
- Docker-Compose Version 1.8 oder höher
- Codeeditor Ihrer Wahl wie zum Beispiel VSCode

Wenn Sie VSCode verwenden, ist eine Hyperledger Composer-Erweiterung für VSCode [hier](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client) verfügbar.


## Schritt 1: Befehlszeilentools installieren

Zu den Befehlszeilentools gehören **composer-cli**, das Hauptbefehlszeilenmodul von {{site.data.keyword.blockchainfull_notm}} Platform: Develop, **generator-hyperledger-composer**, **composer-rest-server** und **Yeoman**. Diese Module werden zum Generieren von Unternehmensnetzen, speziellen REST-Servern und Angular-Anwendungen verwendet.

1. Installieren Sie **composer-cli** mit dem folgenden Befehl:

        npm install -g composer-cli@next

2. Installieren Sie **composer-rest-server** mit dem folgenden Befehl:

        npm install -g composer-rest-server@next

    Verwenden Sie das Modul **composer-rest-server** zum Erstellen eines REST-Servers auf Ihrer Maschine, um Ihre Unternehmensnetze als REST-konforme APIs verfügbar zu machen.

3. Installieren Sie **generator-hyperledger-composer** mit dem folgenden Befehl:

        npm install -g generator-hyperledger-composer@next

    Verwenden Sie **generator-hyperledger-composer**, um Gerüststrukturen für Unternehmensnetze, Modelle und eine Angular-Anwendung zu generieren.

4. Yeoman ist ein Tool zum Generieren von Anwendungen, das `generator-hyperledger-composer` verwendet:

        npm install -g yo

## Schritt 2: Lokale Spielumgebung installieren

Die Hyperledger Composer-Spielumgebung (Playground) ist eine Benutzerschnittstelle, die mit einer realen {{site.data.keyword.blockchain}} verbunden werden kann oder als simulierte Umgebung zum Testen eines Unternehmensnetzes verwendet werden kann. Installieren Sie die Spielumgebung mit dem folgenden Befehl:

        npm install -g composer-playground@next

## Optional: IDE einrichten

Sie können die Spielumgebung (Playground) zum Entwickeln, Bearbeiten und Testen von Unternehmensnetzen verwenden. Wenn Sie jedoch ein Unternehmensnetz in einer integrierten Entwicklungsumgebung (IDE) entwickeln möchten, ist eine VSCode-Erweiterung verfügbar, die Funktionen zur Syntaxhervorhebung für die Hyperledger Composer-Modellierungssprache hinzufügt.

1. Installieren Sie VSCode über die folgende URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Öffnen Sie VSCode, wechseln Sie zu Extensions (Erweiterungen), suchen Sie nach der Hyperledger Composer-Erweiterung im Marktplatz und installieren Sie sie.

## Schritt 3: Lokale Hyperledger Fabric-Instanz installieren

Durch Bereitstellen einer lokalen Hyperledger Fabric-Instanz können Sie Zugriffssteuerungsregeln und Unternehmensnetze vollständig testen.

1. Erstellen Sie das Verzeichnis `fabric-tools` mit dem folgenden Befehl:

        mkdir ~/fabric-tools && cd ~/fabric-tools

Rufen Sie in ein Verzeichnis Ihrer Wahl, zum Beispiel `~/fabric-tools` die `.tar.gz`-Datei ab, die die Tools zum Installieren von Hyperledger Fabric enthält:

2. Laden Sie die `tar.gz`-Datei herunter, die Tools zum Installieren von Hyperledger Fabric in einer Docker-Umgebung enthält.

        curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz

    Es ist auch eine ZIP-Datei (`.zip`) verfügbar. Ersetzen Sie im vorangehenden Ausschnitt die `.tar.gz`-Datei durch die Datei `fabric-dev-servers.zip` und den Befehl `tar -xvf` durch einen Befehl `unzip`.

3. Verwenden Sie die folgenden Befehle, um eine lokale Hyperledger Fabric-Laufzeit der Version 1.1 herunterzuladen:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./downloadFabric.sh

## Schritt 4: Hyperledger Fabric-Instanz starten

Das Verzeichnis `fabric-tools` enthält eine Gruppe von Scripts, durch die die heruntergeladene Hyperledger Fabric-Instanz gesteuert wird. Sie müssen den Befehl `./createPeerAdminCard.sh` ausführen, um eine Unternehmensnetzkarte zu generieren, die Sie zum Bereitstellen des Unternehmensnetzes in dem Docker-Container verwenden können, der die Hyperledger Fabric-Instanz enthält.

1. Führen Sie die folgenden Befehle aus, um die Hyperledger Fabric-Instanz der Version 1.1 zu starten:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./startFabric.sh
        ./createPeerAdminCard.sh

## Optional: Verbindung zur Hyperledger Fabric-Instanz über die Spielumgebung herstellen

Verwenden Sie Hyperledger Composer-Playground oder Ihre IDE, um mit der Entwicklung eines Unternehmensnetzes zu beginnen.

1. Führen Sie den folgenden Befehl aus, um die Spielumgebung 'Playground' zu starten:

        composer-playground

    Die Spielumgebung wird unter der folgenden URL geöffnet: http://localhost:8080/login. Die Karte **PeerAdmin@hlfv1**, die im vorherigen Schritt erstellt wurde, kann zum Bereitstellen eines Unternehmensnetzes verwendet werden.


## Hyperledger Fabric starten und stoppen

Es steht eine Gruppe von Scripts im Verzeichnis `fabric-tools` für die Steuerung der Hyperledger Fabric-Instanz zur Verfügung. Sie können die Laufzeit mit den Scripts `~/fabric-tools/stopFabric.sh` und `~/fabric-tools/startFabric.sh` stoppen und starten.

Das Script `~/fabric-tools/teardownFabric.sh` macht die Erstellung einer neuen PeerAdmin-Karte erforderlich, wenn die Umgebung das nächste Mal gestartet wird. 

---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Unternehmensnetze im Starter und Enterprise Plan bereitstellen
{: #deploying-a-business-network}

*[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)*

{{site.data.keyword.IBM}} bietet keinen Support für Netze, die Hyperledger Composer in Produktionsumgebungen nutzen, inklusive Composer-Befehlszeilenschnittstelle, JavaScript-APIs, REST-Server und Web Playground.
{:note}

[Unternehmensnetze](/docs/services/blockchain/glossary.html#glossary-business-network) können unter Verwendung der Entwicklerumgebung von {{site.data.keyword.blockchainfull_notm}} Platform und mithilfe des Hyperledger Composer-Toolsets entwickelt und in Starter Plan- und Enterprise Plan-Netzen bereitgestellt werde.
{:shortdesc}

In der Entwicklerumgebung können Sie Blockchain-Unternehmensnetze schnell entwickeln und in einem Starter Plan oder Enterprise Plan-Netz von {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.

## Unternehmensnetz im Starter Plan bereitstellen
{: #deploying-a-business-network-starter-plan}

### Vorbemerkungen
{: #deploying-a-business-network-before-begin}

Stellen Sie sicher, dass Sie die [Inforationen zum Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) lesen und anhand der Anweisungen im Abschnitt [Einführung in den Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan) ein Starter Plan-Netz erstellt haben.

Stellen Sie sicher, dass Sie Node v8.9 oder höher, npm v5.x und Hyperledger Composer installiert haben:

- Wenn Ihr Netz den Stand von Fabric Version 1.2 aufweist, verwenden Sie Hyperledger Composer v0.20.x.
- Wenn Ihr Netz den Stand von Fabric Version 1.2 aufweist, verwenden Sie Hyperledger Composer v0.19.x.

Ihre Fabric-Version können Sie ermitteln, indem Sie das [Fenster "Netzvorgaben"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) im Network Monitor öffnen.

### Schritt 1: Geheimen Administratorschlüssel abrufen
{: #deploying-a-business-network-retrieve-admin-secret}

1. Klicken Sie in der Anzeige der Starter Plan-Übersicht auf **Verbindungsprofil** und dann auf 'Download'. Benennen Sie diese Datei in 'connection-profile.json' um.

2. Verschieben Sie diese Datei in das Verzeichnis, in dem sich auch die `.bna`-Datei befindet.

3. Blättern Sie im Verbindungsprofil nach unten bis zum Eintrag 'registrar'. In 'registrar' ist unter 'enrollId' die Eigenschaft **enrollSecret** (geheimer Eintragungsschlüssel) aufgeführt. Rufen Sie den geheimen Schlüssel ab und speichern Sie eine Kopie davon.

    ![Geheimen Administratorschlüssel abrufen](images/get_enroll_secret.gif "Geheimen Administratorschlüssel abrufen")

### Schritt 2: Zertifizierungsstellenkarte erstellen
{: #deploying-a-business-network-CA-card}

Der im vorherigen Schritt abgerufene geheime Schlüssel wird zum Erstellen einer Unternehmensnetzkarte für die Zertifizierungsstelle (CA) verwendet. Die Zertifizierungsstellenkarte (CA-Karte) wird anschließend importiert und verwendet, um den **geheimen Eintragungsschlüssel** für gültige Zertifikate aus der Starter Plan-Zertifizierungsstelle auszutauschen.

1. Verwenden Sie den **geheimen Eintragungsschlüssel** aus Schritt 1 und führen Sie den folgenden Befehl aus, um die CA-Unternehmensnetzkarte zu erstellen:

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  Ersetzen Sie im vorangehenden Befehl `enrollSecret` durch den geheimen Administratorschlüssel, der im Verbindungsprofil abgerufen wurde.

2. Importieren Sie die Karte mit dem folgenden Befehl:

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. Nach dem Importieren der Karte kann sie verwendet werden, um den geheimen Schlüssel (**enrollSecret**) für gültige Zertifikate aus der Zertifizierungsstelle (CA) auszutauschen. Führen Sie den folgenden Befehl aus, um Zertifikate aus der Zertifizierungsstelle anzufordern:

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  Ersetzen Sie im vorangehenden Befehl `enrollSecret` durch den geheimen Administratorschlüssel, der im Verbindungsprofil abgerufen wurde. Der Befehl `composer identity request` erstellt ein Verzeichnis `credentials`, das PEM-Zertifikatsdateien (`.pem`) enthält.

### Schritt 3: Zertifikate zur Starter Plan-Instanz hinzufügen
{: #deploying-a-business-network-add-certs-to-starter-plan}

Die Zertifikate müssen zum Starter Plan-Netz hinzugefügt werden. Zur Vereinfachung können Sie sie mit dem {{site.data.keyword.blockchainfull_notm}} Platform Network Monitor hinzufügen. Die Zertifikate müssen hinzugefügt werden. Anschließend müssen die Peers erneut gestartet werden und dann müssen die Zertifikate im Kanal synchronisiert werden. Das benötigte Zertifikat ist die Datei `admin-pub.pem`, die durch die Ausführung des vorherigen Befehls generiert wurde und die sich im Verzeichnis `credentials` befindet.

1. Klicken Sie im Netzwork Monitor des Starter-Plans auf die Registerkarte **Mitglieder**, dann auf **Zertifikate** und anschließend auf **Zertifikat hinzufügen**. Rufen Sie das Verzeichnis `credentials` auf. Kopieren Sie den Inhalt der Datei `admin-pub.pem` und fügen Sie ihn in das Zertifikatsfeld ein. Übergeben Sie das Zertifikat und starten Sie die Peers erneut. Hinweis: Der Neustart der Peers nimmt eine Minute in Anspruch.

    ![Zertifikate hinzufügen](images/add_cert.gif "Zertifikate hinzufügen")

2. Als nächstes müssen die Zertifikate im Kanal synchronisiert werden. Klicken Sie auf die Registerkarte **Kanäle**, dann auf die Schaltfläche **Aktionen** und dann auf **Zertifikat synchronisieren** und auf **Übergeben**.

    ![Zertifikate synchronisieren](images/sync_cert.gif "Zertifikate synchronisieren")

### Schritt 4: Unternehmensnetzkarte für den Administrator erstellen
{: #deploying-a-business-network-create-admin-card}

Da jetzt die richtigen Zertifikate mit den Peers synchronisiert sind, können Unternehmensnetzkarten erstellt werden, die über die Berechtigung zum Installieren der Hyperledger Composer-Laufzeit und zum Starten des Chaincodes verfügen.

1. Erstellen Sie mit dem folgenden Befehl eine Administratorkarte mit den Rollen 'Kanaladministrator' (ChannelAdmin) und Peeradministrator (PeerAdmin):

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   Diese Karte wird zum Bereitstellen eines Unternehmensnetzes im Starter Plan verwendet.

2. Importieren Sie die im vorherigen Schritt erstellte Karte mit dem folgenden Befehl:

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### Schritt 5: Unternehmensnetz installieren und starten
{: #deploying-a-business-network-install-start-network}

Als Nächstes kann die im vorherigen Schritt erstellte Karte zum Installieren und Starten eines Unternehmensnetzes verwendet werden. Im Rahmen dieser Anleitung wird das Netzbeispiel für die Fahrzeugproduktion installiert. Sie können entweder dieses Beispiel verwenden oder ein eigenes Unternehmensnetz installieren, Sie müssen in diesem Fall jedoch die in den Befehlen angegebenen Unternehmensnetznamen entsprechend ändern. Der Befehl zum Starten eines Unternehmensnetzes erstellt auch eine Karte. Für Starter Plan muss diese Karte gelöscht werden. Der angegebene Beispielbefehl gibt dieser Karte den Namen `delete_me.card`, sodass er sich leicht erkennen lässt.

1. Installieren Sie die Hyperledger Composer-Laufzeit mit dem folgenden Befehl:

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   Notieren Sie sich die Versionsnummer des Unternehmensnetzes, die beim Ausführen dieses Befehls zurückgegeben wird. Sie wird im nächsten Schritt benötigt.

2. Starten Sie das Unternehmensnetz mit dem nachfolgend angegebenen Befehl. Wenn ein Fehler zurückgegeben wird, warten Sie eine Minute und wiederholen Sie den Vorgang. Verwenden Sie die Versionsnummer aus dem letzten Schritt nach der Option `-V`.

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Löschen Sie die Unternehmensnetzkarte mit dem Namen `delete_me.card`.

4. Erstellen Sie mit dem folgenden Befehl eine neue Unternehmensnetzkarte und referenzieren Sie die zuvor abgerufenen Zertifikate:

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. Importieren Sie die Unternehmensnetzkarte mit dem folgenden Befehl:

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

Das Unternehmensnetz ist jetzt in der Starter Plan-Instanz bereitgestellt.

### Schritt 6: Unternehmensnetz mit einem Pingbefehl überprüfen, um sicherzustellen, dass es korrekt ausgeführt wird
{: #deploying-a-business-network-ping-business-network}

Führen Sie den folgenden Befehl aus, um das Unternehmensnetz mit einem Pingbefehl zu überprüfen:

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Wenn Sie die Chaincode-Protokolle anzeigen möchten, klicken Sie auf **Kanäle** und wählen Sie dann den gewünschten Kanal aus. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> Klicken Sie auf die Schaltfläche **Chaincode**. Erweitern Sie die Ansicht der Chaincode-Zeile und klicken Sie dann auf die Schaltfläche **JSON** oder **Protokolle**.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

Das Unternehmensnetz wurde jetzt erfolgreich in der Starter-Plan-Instanz bereitgestellt.

## Unternehmensnetz im Enterprise Plan bereitstellen
{: #deploying-a-business-network-to-enterprise-plan}

Mithilfe der Entwicklertools von {{site.data.keyword.blockchainfull_notm}} Platform können Sie eine **Unternehmensnetzdefinition** erstellen, die anschließend in ein Unternehmensnetzarchiv (Dateierweiterung `.bna`) gepackt werden kann. In der Entwicklerumgebung können Sie BNA-Dateien (`.bna`) in einer lokalen Blockchain-Instanz oder in einer Cloud-{{site.data.keyword.blockchain}}-Instanz zu Entwicklungszwecken und zur gemeinsamen Nutzung dieser Dateien bereitstellen.

Dieses Lernprogramm erläutert den nächsten Schritt des Lebenszyklus eines Unternehmensnetzes, das heißt, die Aktivierung Ihres Unternehmensnetzes durch Bereitstellen der BNA-Dateien (`.bna`) im {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

### Vorbemerkungen
{: #deploying-a-business-network-enterprise-plan-before-begin}

Stellen Sie sicher, dass Sie die {{site.data.keyword.blockchainfull_notm}}-Entwicklerumgebung installiert haben und mit der Entwicklung und Bereitstellung von Unternehmensnetzen vertraut sind. Anleitungen zum Schreiben von Unternehmensnetzen sind in der [Hyperledger Composer-Dokumentation](https://hyperledger.github.io/composer/latest/business-network/business-network-index) verfügbar.

Sie benötigen Zugriff auf eine Enterprise Plan-Instanz von {{site.data.keyword.blockchainfull_notm}} Platform und müssen bereits Ihre Peers erstellt haben. Weitere Informationen zum {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan finden Sie in der [Übersicht zu Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

### Schritt 1: Verbindungsprofil für IBM Blockchain Platform erstellen
{: #deploying-a-business-network-create-connection-profile}

1. Erstellen Sie ein Verzeichnis zum Speichern Ihrer Verbindungsdetails. Beispiel:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Jedes Verbindungsprofil muss eine Datei `connection.json` enthalten. Erstellen Sie ein neues Verzeichnis unter `.composer-connection-profiles`, in dieser Beispielinstanz `bmx-hlfv11`. Dies ist der Name des Profils, das Sie beim Arbeiten mit Hyperledger Composer und {{site.data.keyword.blockchainfull_notm}} Platform verwenden.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Sie sollten jetzt über die folgende Verzeichnisstruktur verfügen:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

### Schritt 2: Verbindungsprofil abrufen
{: #deploying-a-business-network-retrieve-connection-profile}

1. Wählen Sie im {{site.data.keyword.blockchainfull_notm}} Platform-Dashboard die Option **Übersicht**, anschließend  **Verbindungsprofil** und dann die Schaltfläche **Herunterladen** aus, um Ihr Verbindungsprofil abzurufen.

2. Speichern Sie das Verbindungsprofil in der Verzeichnisstruktur, die im vorherigen Schritt erstellt wurde. Benennen Sie es mit **connection.json**.

### Schritt 3: Kanalinformationen hinzufügen
{: #deploying-a-business-network-add-channel-information}

1. Fügen Sie den Wert für "channel" in der Datei `connection.json` so hinzu, dass er mit dem Namen des Kanals übereinstimmt, für den Sie Ihr Unternehmensnetz erstellen und bereitstellen wollen. In der verfügbaren Beispielvorlage für Verbindungsprofile lautet das Kanalelement wie folgt: `"channels": {},`.

### Schritt 4: Peers vorbereiten
{: #deploying-a-business-network-prepare-peers}

Dieser Schritt **MUSS** ausgeführt werden, bevor Sie den Kanal erstellen, für den ein Unternehmensnetz bereitgestellt werden soll. Falls dieser Schritt nach der Kanalerstellung ausgeführt wird, lässt sich ein bereitgestelltes Unternehmensnetz **möglicherweise nicht starten**.
{:note}

Im Verbindungsprofildokument befindet sich unter **certificateAuthorities** ein Attribut namens  **registrar**, das Attribute für **enrollId** und **enrollSecret** mit dem folgenden Format enthält:

  ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
  ```

1. Erstellen Sie mit dem folgenden Befehl eine Unternehmensnetzkarte für die Zertifizierungsstelle:

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Achten Sie darauf, den Befehl in dem Verzeichnis auszuführen, in dem sich Ihr Verbindungsprofil befindet.

2. Importieren Sie die Unternehmensnetzkarte mit dem folgenden Befehl:

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. Nachdem die Karte importiert worden ist, kann sie zum Anfordern der Zertifikate bei der Zertifizierungsstelle mit dem folgenden Befehl verwendet werden:

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    Der Befehl `composer identity request` erstellt ein Verzeichnis für Berechtigungsnachweise, das die relevanten Zertifikatsdateien enthält.

4. Wählen Sie **Mitglieder** im Navigationsmenü aus. Wählen Sie dann die Menüoption **Zertifikate** aus und klicken Sie auf die Schaltfläche **Zertifikat hinzufügen**.

5. Geben Sie einen eindeutigen Namen für dieses Zertifikat in das Feld **Name** ein. Dieser Name kann keine Gedankenstriche oder Bindestrich enthalten.

6. Öffnen Sie die zuvor erstellte Datei `admin-pub.pem` und kopieren Sie den Inhalt dieser Datei in das Feld **Schlüssel**. Drücken Sie die Schaltfläche **Übergeben**.

7. Verwenden Sie die Benutzerschnittstelle, um die Peers zu stoppen und erneut zu starten.

8. Das Zertifikat sollte jetzt in der Liste der Zertifikate aufgeführt werden.

### Schritt 5: Kanal erstellen
{: #deploying-a-business-network-create-your-channel}

1. Wählen Sie **Kanäle** im Navigationsmenü in der linken Anzeige aus und klicken Sie auf die Schaltfläche **Neuer Kanal**.

2. Geben Sie einen Namen und eine optionale Beschreibung für den Kanal ein und klicken Sie auf **Weiter**. Beachten Sie, dass der Kanalname mit dem Namen übereinstimmen muss, den Sie in Ihrem Verbindungsprofil für das Kanalattribut angeben.

3. Erteilen Sie Berechtigungen nach Bedarf und klicken Sie auf **Weiter**.

4. Wählen Sie die Richtlinie der Reihe von Operatoren aus, die Kanalaktualisierungen akzeptieren müssen, und schicken Sie die Anforderung ab.

5. Sie sollten jetzt zum Abschnitt **Benachrichtigungen** geführt werden, in dem eine neue Anforderung zum Prüfen angezeigt wird. Klicken Sie auf die Schaltfläche **Anforderung überprüfen**.

6. Klicken Sie auf die Schaltfläche **Zustimmen**. Sie werden zum Abschnitt **Benachrichtigungen** zurückgeführt, in dem Sie jetzt sehen können, dass die Anforderung übergeben werden kann. Klicken Sie auf die Schaltfläche **Anforderung übergeben**, um den Übergabedialog zu öffnen. Klicken Sie anschließend auf die Schaltfläche **Übergeben**. Der neue Kanal ist jetzt erstellt.

7. Wählen Sie **Kanäle** im Navigationsmenü aus. Der neue Kanal wird in der Liste der Kanäle angezeigt und sollte den Hinweis “Noch keine Peers hinzugefügt” zeigen. Klicken Sie auf das Aktionsmenü neben dem Kanal und wählen Sie die Option **Peers zuordnen** aus. Wählen Sie die Kontrollkästchen neben den Peers aus, die Sie hinzufügen wollen, und klicken Sie auf **Ausgewählte hinzufügen**.

### Schritt 6: Neue Identität zum Verwalten des Unternehmensnetzes erstellen
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

Erstellen Sie die Unternehmensnetzkarte unter Verwendung der angeforderten Zertifikate. Diese Unternehmensnetzkarte  verfügt über die Berechtigung, Chaincode auf den Peers zu installieren, die Ihr hochgeladenes öffentliches Zertifikat besitzen, und fungiert als Aussteller für die Zertifizierungsstellen (CA).

1. Erstellen Sie die Karte mit dem folgenden Befehl:

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    Dabei ist `bmx-hlfv11` der Name des Profils, das Sie zuvor erstellt haben.

2. Nachdem Sie die Karte erstellt haben, importieren Sie sie mit dem folgenden Befehl:

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    Jetzt können Sie Ihre `.bna`-Datei auf der {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.


### Schritt 7: Unternehmensnetz bereitstellen
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

Jetzt können Sie Ihre `.bna`-Datei auf der {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.

1. Damit Sie die im vorherigen Schritt erstellte Karte verwenden können, müssen Sie zunächst das Unternehmensnetz installieren und anschließend starten. Installieren Sie das Unternehmensnetz mit dem folgenden Befehl:

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. Nachdem Sie das Unternehmensnetz installiert haben, starten Sie es mit dem folgenden Befehl:

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Um zu prüfen, ob das Unternehmensnetz ordnungsgemäß bereitgestellt wurde, erstellen Sie eine Identität und eine zugehörige Unternehmensnetzkarte, mit denen das Netz über ein Pingsignal überprüft werden kann.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. Importieren Sie die Unternehmensnetzkarte mit dem folgenden Befehl:

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. Überprüfen Sie das Netz mit einem Pingsignal, um festzustellen, ob das Netz ausgeführt wird:

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

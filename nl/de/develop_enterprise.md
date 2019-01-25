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

# Unternehmensnetz im Enterprise Plan bereitstellen
{: #deploying-a-business-network}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Mithilfe der Entwicklertools von {{site.data.keyword.blockchainfull}} Platform: Develop können Sie eine **Unternehmensnetzdefinition** erstellen, die anschließend in ein Unternehmensnetzarchiv (Dateierweiterung `.bna`) gepackt werden kann. In der Entwicklerumgebung können Sie BNA-Dateien (`.bna`) in einer lokalen Blockchain-Instanz oder in einer Cloud-{{site.data.keyword.blockchain}}-Instanz zu Entwicklungszwecken und zur gemeinsamen Nutzung dieser Dateien bereitstellen.

Dieses Lernprogramm erläutert den nächsten Schritt des Lebenszyklus eines Unternehmensnetzes, das heißt, die Aktivierung Ihres Unternehmensnetzes durch Bereitstellen der BNA-Dateien (`.bna`) im {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die {{site.data.keyword.blockchainfull_notm}}-Entwicklerumgebung installiert haben und mit der Entwicklung und Bereitstellung von Unternehmensnetzen vertraut sind. Anleitungen zum Schreiben von Unternehmensnetzen sind in der [Hyperledger Composer-Dokumentation](https://hyperledger.github.io/composer/latest/business-network/business-network-index) verfügbar.

Sie benötigen Zugriff auf eine Enterprise Plan-Instanz von {{site.data.keyword.blockchainfull_notm}} Platform und müssen bereits Ihre Peers erstellt haben. Weitere Informationen zum {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan finden Sie in der [Übersicht zu Enterprise Plan](./enterprise_plan.html).

## Schritt 1: Verbindungsprofil für {{site.data.keyword.blockchainfull_notm}} Platform erstellen

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

## Schritt 2: Verbindungsprofil abrufen

1. Wählen Sie im {{site.data.keyword.blockchainfull_notm}} Platform-Dashboard die Option **Übersicht**, anschließend  **Verbindungsprofil** und dann die Schaltfläche **Herunterladen** aus, um Ihr Verbindungsprofil abzurufen.

2. Speichern Sie das Verbindungsprofil in der Verzeichnisstruktur, die im vorherigen Schritt erstellt wurde. Benennen Sie es mit **connection.json**.

## Schritt 3: Kanalinformationen hinzufügen

1. Fügen Sie den Wert für "channel" in der Datei `connection.json` so hinzu, dass er mit dem Namen des Kanals übereinstimmt, für den Sie Ihr Unternehmensnetz erstellen und bereitstellen wollen. In der verfügbaren Beispielvorlage für Verbindungsprofile lautet das Kanalelement wie folgt: `"channels": {},`.

## Schritt 4: Peers vorbereiten

**Hinweis:** Dieser Schritt **muss** ausgeführt werden, bevor Sie den Kanal erstellen, für den ein Unternehmensnetz bereitgestellt werden soll. Falls dieser Schritt nach der Kanalerstellung ausgeführt wird, lässt sich ein bereitgestelltes Unternehmensnetz **möglicherweise nicht starten**.

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

## Schritt 5: Kanal erstellen

1. Wählen Sie **Kanäle** im Navigationsmenü in der linken Anzeige aus und klicken Sie auf die Schaltfläche **Neuer Kanal**.

2. Geben Sie einen Namen und eine optionale Beschreibung für den Kanal ein und klicken Sie auf **Weiter**. Beachten Sie, dass der Kanalname mit dem Namen übereinstimmen muss, den Sie in Ihrem Verbindungsprofil für das Kanalattribut angeben.

3. Erteilen Sie Berechtigungen nach Bedarf und klicken Sie auf **Weiter**.

4. Wählen Sie die Richtlinie der Reihe von Operatoren aus, die Kanalaktualisierungen akzeptieren müssen, und schicken Sie die Anforderung ab.

5. Sie sollten jetzt zum Abschnitt **Benachrichtigungen** geführt werden, in dem eine neue Anforderung zum Prüfen angezeigt wird. Klicken Sie auf die Schaltfläche **Anforderung überprüfen**.

6. Klicken Sie auf die Schaltfläche **Zustimmen**. Sie werden zum Abschnitt **Benachrichtigungen** zurückgeführt, in dem Sie jetzt sehen können, dass die Anforderung übergeben werden kann. Klicken Sie auf die Schaltfläche **Anforderung übergeben**, um den Übergabedialog zu öffnen. Klicken Sie anschließend auf die Schaltfläche **Übergeben**. Der neue Kanal ist jetzt erstellt.

7. Wählen Sie **Kanäle** im Navigationsmenü aus. Der neue Kanal wird in der Liste der Kanäle angezeigt und sollte den Hinweis “Noch keine Peers hinzugefügt” zeigen. Klicken Sie auf das Aktionsmenü neben dem Kanal und wählen Sie die Option **Peers zuordnen** aus. Wählen Sie die Kontrollkästchen neben den Peers aus, die Sie hinzufügen wollen, und klicken Sie auf **Ausgewählte hinzufügen**.

## Schritt 6: Neue Identität zum Verwalten des Unternehmensnetzes erstellen

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


## Schritt 7: Unternehmensnetz bereitstellen

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

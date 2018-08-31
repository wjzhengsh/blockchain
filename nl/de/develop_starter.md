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

# Unternehmensnetz im Starter Plan bereitstellen
{: #deploying-a-business-network}

Unternehmensnetze können unter Verwendung der Entwicklerumgebung von {{site.data.keyword.blockchainfull}} Platform und mithilfe des Hyperledger Composer-Toolsets entwickelt und in einer Starter Plan-Umgebung bereitgestellt werden.

In der Entwicklerumgebung können Sie {{site.data.keyword.blockchain}}-Unternehmensnetze schnell entwickeln und in einer Instanz von {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die [Informationen zum Starter Plan](./starter_plan.html) und die [Einführung in Starter Plan](./get_start_starter_plan.html) lesen. Stellen Sie außerdem sicher, dass Sie die [{{site.data.keyword.blockchainfull_notm}} Platform-Entwicklerumgebung](./develop_install.html) installiert und eine {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan-Instanz nach den Anweisungen in [Starter Plan-Netz steuern](./get_start_starter_plan.html) erstellt haben. Node v8.9 oder höher, npm v5.x und Hyperledger Composer v0.19.x müssen vorhanden sein.


## Schritt 1: Geheimen Administratorschlüssel abrufen

1. Klicken Sie in der Anzeige der Starter Plan-Übersicht auf **Verbindungsprofil** und dann auf 'Download'. Benennen Sie diese Datei in 'connection-profile.json' um.

2. Verschieben Sie diese Datei in das Verzeichnis, in dem sich auch die `.bna`-Datei befindet.

3. Blättern Sie im Verbindungsprofil nach unten bis zum Eintrag 'registrar'. In 'registrar' ist unter 'enrollId' die Eigenschaft **enrollSecret** (geheimer Eintragungsschlüssel) aufgeführt. Rufen Sie den geheimen Schlüssel ab und speichern Sie eine Kopie davon.

    ![D8KBag](https://i.makeagif.com/media/4-12-2018/D8KBag.gif)


## Schritt 2: Zertifizierungsstellenkarte erstellen

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

## Schritt 3: Zertifikate zur Starter Plan-Instanz hinzufügen

Die Zertifikate müssen der Starter Plan-Instanz hinzugefügt werden. Aus Gründen der Bedienungsfreundlichkeit können Zertifikate mithilfe der {{site.data.keyword.blockchainfull_notm}} Platform-Benutzerschnittstelle hinzugefügt werden. Die Zertifikate müssen hinzugefügt werden. Anschließend müssen die Peers erneut gestartet werden und dann müssen die Zertifikate im Kanal synchronisiert werden. Das benötigte Zertifikat ist die Datei `admin-pub.pem`, die durch die Ausführung des vorherigen Befehls generiert wurde und die sich im Verzeichnis `credentials` befindet.

1. Klicken Sie in der Starter Plan-Benutzerschnittstelle auf die Registerkarte **Mitglieder**, dann auf **Zertifikate** und dann auf **Zertifikat hinzufügen**. Rufen Sie das Verzeichnis `credentials` auf. Kopieren Sie den Inhalt der Datei `admin-pub.pem` und fügen Sie ihn in das Zertifikatsfeld ein. Übergeben Sie das Zertifikat und starten Sie die Peers erneut. Hinweis: Der Neustart der Peers nimmt eine Minute in Anspruch.

    ![jlEb2y](https://i.makeagif.com/media/4-12-2018/jlEb2y.gif)

2. Als nächstes müssen die Zertifikate im Kanal synchronisiert werden. Klicken Sie auf die Registerkarte **Kanäle**, dann auf die Schaltfläche **Aktionen** und dann auf **Zertifikat synchronisieren** und auf **Übergeben**.

    ![E-sVV5](https://i.makeagif.com/media/4-12-2018/E-sVV5.gif)

## Schritt 4: Unternehmensnetzkarte für den Administrator erstellen

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

## Schritt 5: Unternehmensnetz installieren und starten

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

## Schritt 6: Überprüfen Sie das Unternehmensnetz mit einem Pingbefehl, um sicherzustellen, dass es korrekt ausgeführt wird.

1. Führen Sie den folgenden Befehl aus, um das Unternehmensnetz mit einem Pingbefehl zu überprüfen:

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Wenn Sie die Chaincodeprotokolle anzeigen möchten, klicken Sie auf **Kanäle** und wählen Sie dann den gewünschten Kanal aus. Klicken Sie auf den Dropdown-Pfeil, um die Protokolle anzuzeigen, oder auf das Aktionssymbol, um zusätzliche Details aufzurufen.

![fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif)

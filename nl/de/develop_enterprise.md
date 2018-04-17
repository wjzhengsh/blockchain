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

# Unternehmensnetz in Enterprise Plan bereitstellen

Mithilfe der Entwicklertools von {{site.data.keyword.blockchainfull}} Platform: Develop können Sie eine **Unternehmensnetzdefinition** erstellen, die anschließend in ein Unternehmensnetzarchiv (Dateierweiterung `.bna`) gepackt werden kann. In der Entwicklerumgebung können Sie BNA-Dateien (`.bna`) in einer lokalen Blockchain oder in einer Cloud-{{site.data.keyword.blockchain}} zu Entwicklungszwecken und zur gemeinsamen Nutzung dieser Dateien bereitstellen.

Dieses Lernprogramm erläutert den nächsten Schritt des Lebenszyklus eines Unternehmensnetzes, das heißt, die Aktivierung Ihres Unternehmensnetzes durch Bereitstellen der BNA-Dateien (`.bna`) in {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die {{site.data.keyword.blockchainfull}}: Develop-Entwicklerumgebung installiert haben und mit der Entwicklung und Bereitstellung von Unternehmensnetzen vertraut sind. Anleitungen zum Schreiben von Unternehmensnetzen sind in der [Hyperledger Composer-Dokumentation](https://hyperledger.github.io/composer/latest/business-network/business-network-index) verfügbar.

Sie benötigen Zugriff auf eine Enterprise Plan-Instanz von {{site.data.keyword.blockchainfull_notm}} Platform. Weitere Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan finden Sie in der [Übersicht zu Enterprise Plan](./enterprise_plan.html).

## Schritt 1: Verbindungsprofil für {{site.data.keyword.blockchainfull_notm}} Platform erstellen

1. Erstellen Sie ein Verzeichnis zum Speichern Ihrer Verbindungsdetails. Beispiel:

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Jedes Verbindungsprofil muss eine Datei `connection.json` enthalten. Erstellen Sie ein neues Verzeichnis unter `.composer-connection-profiles`, in dieser Beispielinstanz `bmx-hlfv1`. Dies ist der Name des Profils, das Sie beim Arbeiten mit Hyperledger Composer und {{site.data.keyword.blockchainfull_notm}} Platform verwenden.

        mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1

2. Sie sollten jetzt über die folgende Verzeichnisstruktur verfügen:

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Erstellen Sie eine Datei in dem neu erstellten Verzeichnis und geben Sie ihr den Namen `connection.json`. Sie können die folgende Vorlage für Ihre Datei `connection.json` verwenden:

        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }

    Sie füllen die neu erstellte Datei `connection.json` mit Attributen, die über Ihr {{site.data.keyword.blockchainfull_notm}} Platform-Dashboard bereitgestellt werden. Wählen Sie in Ihrem Dashboard die Option **Übersicht** aus, klicken Sie auf die Schaltfläche **Serviceberechtigungsnachweise**, um die Endpunkt- und Zertifikatsinformationen für die Mitglieder des Kanals anzuzeigen.

## Schritt 2: Informationen zu Anordnungsknoten hinzufügen

1. Jetzt kann mit der Änderung der Vorlage mit den Informationen begonnen werden, die von den Serviceberechtigungsnachweisen bereitgestellt werden. Es sind möglicherweise mehrere Anordnungsknoten in den Serviceberechtigungsnachweisen vorhanden, jedoch ist nur einer für eine Datei `connection.json` erforderlich.

    Ersetzen Sie die URL-Werte für die Anordnungsknoten in der Vorlage durch die relevanten Informationen aus Ihren Serviceberechtigungsnachweisen im folgenden Format:

        “url”: “grpcs://abca.4.secure.blockchain.ibm.com:12345”

## Schritt 3: Informationen der Zertifizierungsstelle hinzufügen

1. Ersetzen Sie unter 'ca' in der Datei `connection.json` den Wert für **url** und den Wert für **caName** durch die Werte aus beiden Einträgen im Abschnitt **certificateAuthorities**.

## Schritt 4: Peerinformationen hinzufügen

1. Die Anforderungs-URL (**requestURL**) und die Ereignis-URL (**eventURL**) müssen für jeden Peer festgelegt werden. Ersetzen Sie das Attribut **url** durch den Wert für **url**, der sich in Ihren Serviceberechtigungsnachweisen befindet. Ersetzen Sie das Attribut **eventURL** durch die Ereignis-URL (**eventUrl**), die sich in Ihren Serviceberechtigungsnachweisen befindet. Nach diesen Änderungen sollte der Abschnitt "peers" in der Datei `connection.json` das folgende Format haben:

        "peers": [
          {
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"

## Schritt 5: keyValStore-Informationen hinzufügen

1. Legen Sie das Attribut **keyValStore** so fest, dass es auf das richtige Verzeichnis verweist. Erstellen Sie ein Verzeichnis, das als Schlüsselwertspeicher (**keyValStore**) verwendet werden soll. Erstellen Sie zum Beispiel ein neues Verzeichnis unter Ihrem Ausgangsverzeichnis mit dem Namen `.composer-credentials-mychannel`. Stellen Sie sicher, dass das Attribut **keyValStore** auf Ihr neu erstelltes Verzeichnis im folgenden Format verweist:

        "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",

## Schritt 6: Kanalinformationen hinzufügen

1. Ersetzen Sie den Wert für "channel"in der Datei `connection.json` durch den Namen des Kanals, für den Sie Ihr Unternehmensnetz erstellen und bereitstellen wollen.

## Schritt 7: MSP-ID hinzufügen

Der Wert **mspID** in Ihrer Datei `connection.json` muss auf den mspID-Wert Ihrer Organisation gesetzt werden. Die Serviceberechtigungsnachweise geben eine Liste der Organisationen mit ihren zugeordneten mspID-Werten an. Sie müssen den Wert aus dem Attribut **mspid** Ihrer Organisation verwenden.

## Schritt 8: globalCert-Wert hinzufügen
1. {{site.data.keyword.blockchainfull_notm}} Platform verwendet ein allgemeines TLS-Zertifikat für die Anordnungsknoten und Peers. Für jeden Anordnungsknoten und jeden Peer gibt es ein Attribut **tlsCACerts**, das jeweils dasselbe Zertifikat enthält. Ersetzen Sie den Pseudowert in der Datei `connection.json` durch den Wert aus **tlsCACerts**. Der Wert sollte das folgende Format haben:

        "globalCert": "-----BEGIN CERTIFICATE-----\r\.......

## Schritt 9: Peers vorbereiten

**Hinweis:** Dieser Schritt **muss** ausgeführt werden, bevor Sie den Kanal erstellen, für den ein Unternehmensnetz bereitgestellt werden soll. Falls dieser Schritt nach der Kanalerstellung ausgeführt wird, lässt sich ein bereitgestelltes Unternehmensnetz **nicht starten**.

Im Dokument der Serviceberechtigungsnachweise befindet sich unter **certificateAuthorities** ein Attribut **registrar**, das Attribute für **enrollId** und **enrollSecret** im folgenden Format enthält:

        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],

1. Fordern Sie Zertifikate mit dem folgenden Befehl an:

        composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12

    Durch diesen Befehl werden drei Dateien in das Verzeichnis `.identityCredentials` unter Ihrem Ausgangsverzeichnis heruntergeladen. Die interessanten Dateien basieren auf dem Wert für **enrollId**. Im obigen Beispiel sind daher zwei Dateien mit den Namen **admin-pub.pem** und **admin-priv.pem** vorhanden.

3. Wählen Sie **Mitglieder** im Navigationsmenü aus. Wählen Sie dann die Menüoption **Zertifikate** aus und klicken Sie auf die Schaltfläche **Zertifikat hinzufügen**.

4. Geben Sie einen eindeutigen Namen für dieses Zertifikat in das Feld **Name** ein. Dieser Name kann keine Gedankenstriche oder Bindestrich enthalten.

5. Öffnen Sie die zuvor erstellte Datei `admin-pub.pem` und kopieren Sie den Inhalt dieser Datei in das Feld **Schlüssel**. Drücken Sie die Schaltfläche **Übergeben**.

6. Verwenden Sie die Benutzerschnittstelle, um die Peers zu stoppen und erneut zu starten.

7. Das Zertifikat sollte jetzt in der Liste der Zertifikate aufgeführt werden.

## Schritt 10: Kanal erstellen

1. Wählen Sie **Kanäle** im Navigationsmenü in der linken Anzeige aus und klicken Sie auf die Schaltfläche **Neuer Kanal**.

2. Geben Sie einen Namen und eine optionale Beschreibung für den Kanal ein und klicken Sie auf **Weiter**. Beachten Sie, dass der Kanalname mit dem Namen übereinstimmen muss, den Sie in Ihrem Verbindungsprofil für das Kanalattribut angeben.

3. Erteilen Sie Berechtigungen nach Bedarf und klicken Sie auf **Weiter**.

4. Wählen Sie die Richtlinie der Reihe von Operatoren aus, die Kanalaktualisierungen akzeptieren müssen, und schicken Sie die Anforderung ab.

5. Sie sollten jetzt zum Abschnitt **Benachrichtigungen** geführt werden, in dem eine neue Anforderung zum Prüfen angezeigt wird. Klicken Sie auf die Schaltfläche **Anforderung überprüfen**.

6. Klicken Sie auf die Schaltfläche **Zustimmen**. Sie werden zum Abschnitt **Benachrichtigungen** zurückgeführt, in dem Sie jetzt sehen können, dass die Anforderung übergeben werden kann. Klicken Sie auf die Schaltfläche **Anforderung übergeben**, um den Übergabedialog zu öffnen. Klicken Sie anschließend auf die Schaltfläche **Übergeben**. Der neue Kanal ist jetzt erstellt.

7. Wählen Sie **Kanäle** im Navigationsmenü aus. Der neue Kanal wird in der Liste der Kanäle angezeigt und sollte den Hinweis “Noch keine Peers hinzugefügt” zeigen. Klicken Sie auf das Aktionsmenü neben dem Kanal und wählen Sie die Option **Peers zuordnen** aus. Wählen Sie die Kontrollkästchen neben den Peers aus, die Sie hinzufügen wollen, und klicken Sie auf **Ausgewählte hinzufügen**.

## Schritt 11: Neue Identität zum Verwalten des Unternehmensnetzes importieren

Erstellen Sie eine Identität in Composer, indem Sie die Zertifikate verwenden, die zuvor angefordert worden sind. Diese neue Identität verfügt über die Berechtigung, Chaincode auf den Peers zu installieren, die Ihr hochgeladenes öffentliches Zertifikat besitzen, und fungiert als Aussteller für die Zertifizierungsstellen (CA).

1. Führen Sie den folgenden Befehl aus, um eine neue Identität zu erstellen:

        composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem

    Dabei ist `bmx-hlfv1` der Name des Profils, das Sie zuvor erstellt haben. Jetzt können Sie Ihre `.bna`-Datei auf der {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.


## Schritt 12: Unternehmensnetz bereitstellen

Jetzt können Sie Ihre `.bna`-Datei auf der {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.

1. Führen Sie den folgenden Befehl mit der im vorherigen Schritt erstellten Identität aus, um das Unternehmensnetz bereitzustellen:

        composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString

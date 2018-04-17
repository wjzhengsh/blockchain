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

# Unternehmensnetz in Starter Plan bereitstellen
{: #deploying-a-business-networks-on-starter-plan}

Unternehmensnetze können unter Verwendung der Entwicklerumgebung von {{site.data.keyword.blockchainfull}} Platform: Develop und mithilfe des Hyperledger Composer-Toolsets entwickelt und in einer Starter Plan-Umgebung bereitgestellt werden.

In der Entwicklerumgebung können Sie {{site.data.keyword.blockchain}}-Unternehmensnetze schnell entwickeln und in einer Instanz von {{site.data.keyword.blockchainfull_notm}} Platform bereitstellen.

## Vorbemerkungen

Stellen Sie sicher, dass Sie die [Informationen zu Starter Plan](./starter_plan.html) und die [Einführung in Starter Plan](./get_start_starter_plan.html) lesen. Stellen Sie außerdem sicher, dass Sie die [{{site.data.keyword.blockchainfull_notm}} Platform: Develop-Entwicklerumgebung](./develop_install.html) installiert und eine Instanz von {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan nach den Anweisungen in [Starter Plan-Netz steuern](./get_start_starter_plan.html) erstellt haben.


## Schritt 1: Geheimen Administratorschlüssel abrufen

1. Klicken Sie in der Anzeige der Starter Plan-Übersicht auf **Verbindungsprofil**.

2. Im Verbindungsprofil ist eine Eigenschaft **admin secret** (geheimer Administratorschlüssel) verfügbar. Rufen Sie den geheimen Schlüssel ab und speichern Sie eine Kopie davon.

## Schritt 2: Zertifizierungsstellenkarte erstellen

Der im vorherigen Schritt abgerufene geheime Schlüssel wird zum Erstellen einer Unternehmensnetzkarte für die Zertifizierungsstelle (CA) verwendet. Die Zertifizierungsstellenkarte (CA-Karte) wird anschließend importiert und verwendet, um den **geheimen Adminstratorschlüssel** für gültige Zertifikate aus der Starter Plan-Zertifizierungsstelle auszutauschen.

1. Führen Sie mit dem in Schritt 1 genannten geheimen Schlüssel den folgenden Befehl aus, um die CA-Unternehmensnetzkarte zu erstellen:

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. Importieren Sie die Karte mit dem folgenden Befehl:

        composer card import -f ca.card -n ca

3. Nach dem Importieren der Karte kann sie verwendet werden, um den geheimen Administratorschlüssel (**admin secret**) für gültige Zertifikate aus der Zertifizierungsstelle (CA) auszutauschen. Führen Sie den folgenden Befehl aus, um Zertifikate aus der Zertifizierungsstelle anzufordern:

        composer identity request --card ca --path ./credentials

Der Befehl `composer identity request` erstellt ein Verzeichnis `credentials`, das PEM-Zertifikatsdateien (`.pem`) enthält.

## Schritt 3: Zertifikate zur Starter Plan-Instanz hinzufügen

Die Zertifikate müssen der Starter Plan-Instanz hinzugefügt werden. Aus Gründen der Bedienungsfreundlichkeit können Zertifikate mithilfe der {{site.data.keyword.blockchainfull_notm}} Platform-API hinzugefügt werden. Die Zertifikate müssen hinzugefügt werden. Anschließend müssen Peers gestoppt und erneut gestartet werden, sodass die Zertifikate synchronisiert werden. Die Daten, die in den Variablen der API-Aufrufe erforderlich sind, können aus dem **Verbindungsprofil** im Starter Plan-Netzmonitor abgerufen werden.

1. Fügen Sie die Zertifikate mit dem folgenden API-Aufruf hinzu:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    Im obigen Beispiel wurde der Hauptteil (Body) nicht angegeben. Der folgende Code ist ein Beispiel für den Hauptteil der API-Anforderung:

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. Stoppen Sie die Peers mit dem folgenden API-Aufruf:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. Starten Sie die Peers mit dem folgenden API-Aufruf erneut:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. Synchronisieren Sie die Zertifikate mit dem folgenden API-Aufruf:

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## Schritt 4: Unternehmensnetzkarte für den Administrator erstellen

Da jetzt die richtigen Zertifikate mit den Peers synchronisiert sind, können Unternehmensnetzkarten erstellt werden, die über die Berechtigung zum Installieren der Hyperledger Composer-Laufzeit und zum Starten des Chaincodes verfügen.

1. Erstellen Sie mit dem folgenden Befehl eine Administratorkarte mit den Rollen 'Kanaladministrator' (ChannelAdmin) und Peeradministrator (PeerAdmin):

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    Diese Karte wird zum Bereitstellen eines Unternehmensnetzes in Starter Plan verwendet.

2. Importieren Sie die im vorherigen Schritt erstellte Karte mit dem folgenden Befehl:

        composer card import -f adminCard.card -n adminCard

## Schritt 5: Unternehmensnetz installieren und starten

Als Nächstes kann die im vorherigen Schritt erstellte Karte zum Installieren und Starten eines Unternehmensnetzes verwendet werden. Für die Zwecke dieser Anleitung soll das Beispiel für das Netz 'Vehicle Manufacture' installiert werden. Der Befehl zum Starten eines Unternehmensnetzes erstellt auch eine Karte. Für Starter Plan muss diese Karte gelöscht werden. Der angegebene Beispielbefehl gibt dieser Karte den Namen `delete_me.card`, sodass er sich leicht erkennen lässt.

1. Installieren Sie die Hyperledger Composer-Laufzeit mit dem folgenden Befehl:

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. Starten Sie das Unternehmensnetz mit dem folgenden Befehl:

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. Löschen Sie die Unternehmensnetzkarte mit dem Namen `delete_me.card`.

4. Erstellen Sie mit dem folgenden Befehl eine neue Unternehmensnetzkarte und referenzieren Sie die zuvor abgerufenen Zertifikate:

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. Importieren Sie die Unternehmensnetzkarte mit dem folgenden Befehl:

        composer card import -f ./admin@vehicle-manufacture-network.card

Das Unternehmensnetz ist jetzt in der Starter Plan-Instanz bereitgestellt.

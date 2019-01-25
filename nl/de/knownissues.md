---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-04"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Bekannte Probleme


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Die folgenden Probleme sind bereits dokumentiert:
- **Das Konfigurieren einer externen Zertifizierungsstelle (CA) wird noch nicht unterstützt**. Als Alternative können Sie Administratorzertifikate über den Network Monitor generieren und hochladen. Weitere Informationen finden Sie unter [Clientseitige Zertifikate generieren](v10_application.html#enroll-app) und in der Beschreibung auf [der Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Network Monitor.
- Wenn Sie im Network Monitor eines Starter Plan-Netzes auf **Protokolle anzeigen** für die in der Anzeige "Übersicht" aufgeführten Knoten klicken, wird die Kibana-Schnittstelle für {{site.data.keyword.cloud}} Logging geöffnet. **Standardmäßig ist Kibana so vorkonfiguriert, dass Protokolle zu den Aktivitäten der letzten 30 Tage angezeigt werden**. Wenn in den letzten 30 Tagen keine Aktivitäten stattgefunden haben, wird die Nachricht *Keine Ergebnisse gefunden* angezeigt. Wenn Sie andere Protokolle anzeigen möchten, können Sie auf das Zeitgebersymbol in der rechten oberen Ecke unterhalb Ihres Benutzernamens klicken und einen längeren Zeitraum festlegen, z. B. Zeiträume wie *Laufendes Jahr bis zum aktuellen Datum*.
- Die Protokolle des Starter Plan-Netzes werden vom [{{site.data.keyword.cloud_notm}} Log Analysis-Service ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/log-analysis) erfasst. Standardmäßig werden Ihre Protokolle über den Lite Plan des Log Analysis-Service erfasst. Dieser Plan ist kostenfrei und **ermöglicht Ihnen lediglich ein Durchsuchen der ersten 500 MB Ihrer Protokolle pro Tag**. Umfassen Ihre Netzprotokolle mehr als 500 MB können neue Protokolle nicht in Kibana angezeigt werden. Werden in Ihrem Netz Protokolle mit einem Gesamtumfang über 500 MB generiert, können Sie ein Upgrade auf eine gebührenpflichtige Version des Log Analysis-Service durchführen.
- Da der Starter Plan keine Produktionsumgebung bereitstellt, **können Anwendungen Netzressourcen möglicherweise nicht sofort erreichen**.
  - Ist dies der Fall, empfiehlt es sich, zunächst die Standardzeitlimitwerte im Fabric-SDK zu erhöhen. Weitere Informationen zum Festlegen von Zeitlimitwerten finden Sie in [Zeitlimitwerte in Fabric-SDKs festlegen](v10_application.html#set-timeout-in-sdk).
  - Sie können auch versuchen, Ihre Anforderung auf der Anwendungsebene auszuführen.
- **Chaincode-Container werden unter Umständen zeitweise gestoppt**, weil im Hintergrund ein Netzproblem vorliegt, und müssen möglicherweise nach dem Aufruf durch einen Benutzer erneut erstellt und gestartet werden. Wenn dies geschieht, kann es einige Minuten dauern, bis der Chaincode antwortet.
- Aufgrund der Ressourcenbegrenzung in Starter Plan-Netzen auf 1 CPU und 4 Gi RAM pro Peer **kann ein `REQUEST_TIMEOUT`-Fehler bei der Chaincode-Instanziierung auftreten**. Ist dies der Fall, wiederholen Sie den für die Instanziierung erforderlichen Schritt. Tritt der Fehler weiterhin auf, können Sie das Zeitlimit für die Chaincode-Instanziierung erhöhen. Im Verbindungsprofil ist das Zeitlimit für die Chaincode-Instanziierung mit 300 Sekunden definiert.
  - Wenn Sie den Standardwert für das Zeitlimit im SDK verwenden, kopieren Sie den Abschnitt **client** im Verbindungsprofil (s. u.), mit dem ein Wert von 300 Sekunden für das Zeitlimit festgelegt wird, und stellen Sie sicher, dass dieser Wert von Ihrem SDK gelesen wird. Dabei ist zu beachten, dass sich diese Verbindungsprofileinstellung für die Zeitlimitüberschreitung beim Node-SDK auf alle Aufrufe, z. B. Aufrufe mit `invoke` und `Abfragen`, auswirkt.
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - Wenn Sie die Einstellung für die Zeitlimitüberschreitung bei den Befehlen für die Chaincode-Instanziierung in Ihren SDKs überschreiben, setzen Sie den Wert auf den Standardwert zurück oder verwenden Sie einen Wert von 300 Sekunden oder mehr.
    - Wenn Sie das Node-SDK verwenden, können Sie die Einstellung für die Zeitlimitüberschreitung der Methode `sendInstantiateProposal(request, timeout)` ändern. Weitere Informationen hierzu finden Sie im Abschnitt [sendInstantiateProposal(request, timeout) ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal).
    - Wenn Sie das Java-SDK verwenden, können Sie die Einstellung für die Zeitlimitüberschreitung des Befehls `sendInstantiateProposal(request, timeout)` in der Datei `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java` ändern.

Informationen zum Anfordern von Unterstützung und Hilfe für Ihr {{site.data.keyword.blockchainfull_notm}} Platform-Netz in {{site.data.keyword.cloud_notm}} finden Sie in [Unterstützung anfordern](ibmblockchain_support.html).


## Chaincode für Upgrade auf Enterprise Plan-Netz aktualisieren
{: #chaincode-migration}

Wenn Ihr Enterprise Plan-Netz auf Hyperledger Fabric Version 1.0 basiert, sieht {{site.data.keyword.blockchainfull_notm}} Platform zu einem geplanten Zeitpunkt ein Netzupgrade mit einer Migration auf Hyperledger Fabric Version 1.1 vor. Nach diesem Netzupgrade müssen Sie die Abhängigkeiten in Ihrem Chaincode aktualisieren, wenn Sie komplexen Chaincode mit Abhängigkeiten verwenden. Andernfalls treten möglicherweise Serviceunterbrechungen auf.

**Hinweise:**
- Wenn Sie einfachen Chaincode mit einer einzigen Datei `.go` bzw. `.js` verwenden, ist eine Aktualisierung des Chaincodes nicht erforderlich.
- Der aktualisierte Chaincode kann möglicherweise im alten Netz nicht mehr ausgeführt werden. Sie müssen Ihren Chaincode deshalb nach dem geplanten Netzupgrade aktualisieren.
- Sie können Ihren Chaincode testen, indem Sie den Chaincode in einem Starter Plan-Netz installieren und instanziieren. Chaincode, der im Starter Plan ordnungsgemäß ausgeführt wird, wird auch im Enterprise Plan nach dem Netzupgrade problemlos ausgeführt.

Führen Sie die folgenden Schritte aus, um den Chaincode zu aktualisieren:
1. Aktualisieren Sie den Chaincode mithilfe eines Golang-Tools eines beliebigen Anbieters. Es empfiehlt sich, dasselbe Tool wie beim Einfügen von Abhängigkeiten in die Originaldatei zu verwenden. Wird vom Chaincode beispielsweise wie in vielen Fabric-Beispielen das Tool **govendor** verwendet, können Sie die Abhängigkeiten im Chaincode aktualisieren, indem Sie den folgenden Befehl in dem Verzeichnis ausführen, das dem Ordner des jeweiligen Anbieters übergeordnet ist:
    ```
    govendor update all +v
    ```
    {:codeblock}

    Mit dem Befehl `go build` können Sie anschließend überprüfen, ob der neue Code kompiliert wurde und der aktualisierte Chaincode funktionsfähig ist.

2. Nach dem Netzupgrade können Sie den [Chaincode im Network Monitor aktualisieren](howto/install_instantiate_chaincode.html#updating-a-chaincode).

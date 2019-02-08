---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-04"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Blockchain-Netz überwachen


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Dieses Lernprogramm zeigt, wie Sie Statusinformationen zu Ihrem {{site.data.keyword.blockchain}}-Netz in {{site.data.keyword.cloud_notm}} anzeigen und überwachen können.
{:shortdesc}


## Peers, Anordnungsknoten und Zertifizierungsstellen überwachen
{: #monitor-nodes}

Sie können eine HTTP-Anforderung **HEAD** für einen Netzknoten ausgeben, um den Knotenstatus zu überprüfen. Bei einem Netzwerkknoten kann es sich um einen Peer, einen Anordnungsknoten oder eine Zertifizierungsstelle im Blockchain-Netz handeln. Eine **HEAD**-Anforderung gleicht einer GET-Anforderung und sendet lediglich Header ohne den Anforderungshauptteil. Sie können mit etwa 200 Antworten rechnen, wenn der Knoten ordnungsgemäß ausgeführt wird.

1. Klicken Sie in der Anzeige "Übersicht" im Network Monitor auf **Verbindungsprofil**. Klicken Sie anschließend auf **Unaufbereiteter JSON-Code**, um das Verbindungsprofil im Web-Browser anzuzeigen, oder auf **Herunterladen**, um das Verbindungsprofil lokal zu speichern.
2. Suchen Sie im Verbindungsprofil die URL-Informationen des Netzknotens, der überprüft werden soll. Die URL des Anordnungsknotens `fabric-orderer-20190b` lautet z. B. `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`. ![Beispiel für Anordnungsknoten-URL](../images/orderer_url.png "Beispiel für Anordnungsknoten-URL")
3. Ersetzen Sie **grpcs** in der URL durch **https**. Die URL im voranstehenden Beispiel wird zu `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Geben Sie die **HEAD**-Anforderung für die URL mit einem Tool wie "cURL" oder der Chrome-App "Postman" aus.
    - Wenn Sie etwa 200 Statusantworten erhalten, wird der Netzknoten ordnungsgemäß ausgeführt.
    - Schlägt die **HEAD**-Anforderung mit einem Verbindungsfehler fehl, ist der Netzknoten nicht aktiv, die Knoten-URL falsch oder der Zugriff auf den Knoten wird durch eine Firewall blockiert.  Dieser Fehler muss behoben werden. Ihre Anwendungen können ansonsten keine Verbindung zum Knoten herstellen.

Bei dem folgenden Beispiel handelt es sich um eine **HEAD**-Anforderung mit etwa 200 Antworten in cURL. Der grpc-Fehler kann ignoriert werden, da die HTTP-Anforderung **HEAD** überprüft, ob auf den Knoten zugegriffen werden kann. Ist der Zugriff möglich, wird die grpc-Anforderung auf den Knoten auch in Ihrer Anwendung ausgeführt.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

Bei dem folgenden Beispiel handelt es sich um eine **HEAD**-Anforderung mit einem Verbindungsfehler in cURL.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

In der folgenden Abbildung ist eine **HEAD**-Anforderung mit etwa 200 Antworten in der Chrome-App "Postman" zu sehen.

  ![HEAD-Beispielanforderung mit Postman](../images/orderer_head_postman.png "HEAD-Beispielanforderung mit Postman")

## Netzprotokolle verwenden
In der Anzeige "Übersicht" des Network Monitor wird der Status Ihres Anordnungsservice, der Zertifizierungsstelle und der Peers angezeigt. Klicken Sie in der Dropdown-Liste unter der Überschrift **Aktionen** auf **Protokolle anzeigen**, um die Protokolle einer speziellen Netzkomponente anzuzeigen. Wenn Sie mit Enterprise Plan-Netzen arbeiten, dann können Sie Komponentenprotokolle im Textdateiformat anzeigen. Wenn Sie Starter Plan-Netze verwenden, dann werden die Komponentenprotokolle vom [{{site.data.keyword.cloud_notm}} Log Analysis-Service ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/log-analysis) erfasst und Sie können die Protokolle in [Kibana](#viewing-logs-in-kibana-in-starter-plan) anzeigen.

Jede Komponente generiert Protokolle zu verschiedenen Aktivitäten. Dies ist darauf zurückzuführen, dass jede Komponente unterschiedliche Rollen in der [Netzarchitektur ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) und in den [Transaktionsabläufen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html) von Hyperledger Fabric spielt.

- **Protokolle des Anordnungsservice**
  Der Anordnungsservice stellt die einheitliche Bindekomponente des Blockchain-Netzes dar. Alle bewilligten Transaktionsvorschläge der Peers sowie Kanal- oder Netzmitgliedschaftsaktualisierungen werden zur Verifizierung an den Anordnungsservice gesendet. Aus diesem Grund enthält der Anordnungsservice Protokolle für den Zeitpunkt, zu dem das Netz gestartet wurde. Außerdem enthält er Protokolle für eine Transaktion, die zurückgewiesen wurde, weil sie nicht von den richtigen Organisationen ordnungsgemäß bewilligt wurde. Des Weiteren gibt es Protokolle für dem Zeitpunkt, zu dem Kanäle erstellt oder aktualisiert wurden oder zu dem Kanalaktualisierungen fehlgeschlagen sind.

- **Protokolle der Zertifizierungsstelle**
  Die Zertifizierungsstelle verwaltet die Identität der Teilnehmer innerhalb des Netzes. In den Protokollen für Zertifizierungsstellen (CA) finden Sie Protokolle für den Zeitpunkt, zu dem die Teilnehmer öffentliche und private Schlüssel generiert haben, um mit dem Netz zu kommunizieren (Eintragung), oder zu dem neue Mitglieder, Peers oder Anwendungen sich bei der Zertifizierungsstelle registriert haben. Die Protokolle der Zertifizierungsstelle können auch zum Debuggen verwendet werden, wenn Probleme mit der Zertifikatsprüfung aufgetreten sind.

- **Protokolle für Peers**  
  In den Protokollen für Peers werden die Ergebnisse der Installation, der Instanziierung und des Aufrufs des Chaincodes gespeichert. Sie können nach einem Chaincode-Namen und der Chaincode-Version suchen, um die Protokolle eines bestimmten Chaincodes zu finden. Außerdem können Sie die Protokolle eines bestimmten Chaincodes im [Abschnitt zum Chaincode des Kanalmonitors](#monitor-channel-cc) anzeigen. Die Nachrichten, die von Ihren Transaktionsvorschlägen generiert werden, sowie alle Probleme aufgrund von Zeitlimitüberschreitungen bei den Vorschlagsanforderungen werden in den Peerprotokollen dokumentiert. Die Protokolle der Peers enthalten außerdem Fehler für Transaktionen, die zurückgewiesen wurden, weil sie die [Bewilligungsrichtlinie des Chaincodes](/docs/services/blockchain/howto/install_instantiate_chaincode.html#endorsement-policy) nicht erfüllten. Des Weiteren finden Sie dort die Ergebnisse von Kanalbeitrittsanforderungen.

Hyperledger Fabric stellt verschiedene [Protokollierungsstufen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/logging-control.html "Logging control") auf Basis der Fehlerkategorie einer Nachricht bereit. Die Standardprotokollierungsstufe von {{site.data.keyword.blockchainfull_notm}} Platform ist `INFO`. Zur Anzeige zusätzlicher Protokolle können Sie ein [Support-Ticket](/docs/services/blockchain/ibmblockchain_support.html#submitting-support-cases) öffnen, um die Protokollierungsstufe `DEBUG` festzulegen, bei der ausführlichere Informationen angezeigt werden. Beachten Sie hierbei, dass bei Protokollen der Stufe `DEBUG` beträchtliche Volumen an Gossip-Nachrichten angezeigt werden, die möglicherweise gefiltert werden müssen. Durchsuchen Sie die Nachrichten auf die Angabe `warning` oder `error`, um Probleme mit Hyperledger Fabric-Komponenten zu finden. Um festzustellen, ob die Ausführung des Komponentencontainers fehlgeschlagen ist oder ob seine Ausführung abgebrochen wurde, müssen Sie nach Nachrichten mit der Angabe `panic` oder `killed` suchen, die von {{site.data.keyword.cloud_notm}} gesendet wurden.

## Protokolle in Kibana in Starter Plan anzeigen
Die Protokolle des Starter Plan-Netzes werden vom [{{site.data.keyword.cloud_notm}} Log Analysis-Service ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/log-analysis "Log Analysis-Service") erfasst. Standardmäßig werden Ihre Protokolle über den Lite Plan des Log Analysis-Service erfasst. Hierbei handelt es sich um einen kostenlosen Plan, mit dem **Ihre Protokolle für eine Dauer von drei Tagen gespeichert** werden, bevor sie gelöscht werden. Darüber hinaus können Sie mit diesem Service auch nur in den **ersten 500 MB Ihrer pro Tag erfassten Protokolle suchen**. Umfassen Ihre Netzprotokolle mehr als 500 MB, können neue Protokolle in Kibana nicht angezeigt werden. Werden in Ihrem Netz Protokolle mit einem Gesamtumfang über 500 MB generiert oder sollen die Protokolle für einen längeren Zeitraum als drei Tage aufbewahrt werden, können Sie ein Upgrade auf eine gebührenpflichtige Version des Log Analysis-Service durchführen.

Klicken Sie in der Anzeige "Übersicht" Ihres Network Monitor in der Dropdown-Liste unter der Überschrift **Aktionen** auf **Protokolle anzeigen**, um die einzelnen Protokolle der Netzkomponenten in der Kibana-Schnittstelle zu öffnen. Wenn Kibana geöffnet wird, dann werden dort die Protokolle angezeigt, die über eine Suchleiste oben in der Anzeige gefiltert werden können. Wenn Sie z. B. auf die Option zur Anzeige Ihrer Peerprotokolle klicken, dann wird der Suchvorgang nach Ihrer Netz-ID und Ihrer Peer-ID gefiltert: `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939" && NODE_NAME_str:"org1-peer1"`. Sie können ein zusätzliches Feld in der Suchleiste eingeben, wenn detailliertere Protokolle angezeigt werden sollen. Sie können beispielsweise die Zeichenfolge `&& "marbles"` hinzufügen, um die Protokolle aus dem Chaincode `"marbles"` anzuzeigen. Wenn Sie den Begriff für eine bestimmte Komponente löschen und nur anhand der Netz-ID suchen (z. B. `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939"`), dann werden die Protokolle aller Netzkomponenten angezeigt.

Mit der Schaltfläche für den Zeitbereich oben rechts in der Anzeige können Sie den Zeitraum ändern, für den Protokolle angezeigt werden sollen. Darüber hinaus können Sie die Registerkarte auf der linken Seite der Anzeige verwenden, um Felder zur Suche hinzuzufügen oder aus ihr zu entfernen. Das wichtigste Feld, das angezeigt werden kann, ist das Nachrichtenfeld. Möglicherweise ist es sinnvoll, mit einer Nachricht ohne die Zeitmarke zu suchen, um alle Instanzen des entsprechenden Nachrichtenprotokolls zu finden. Klicken Sie auf die Schaltfläche **Speichern**, um die aktuelle Suche zu speichern und zu einer bestimmten Ansicht zurückzukehren. Weitere Informationen zur Anzeige von Daten in Kibana finden Sie im [Kibana-Benutzerhandbuch ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.elastic.co/guide/en/kibana/6.2/index.html "Kibana-Benutzerhandbuch"). Außerdem können Sie [Ihre Protokolle herunterladen](https://console.bluemix.net/docs/services/CloudLogAnalysis/how-to/manage-logs/downloading_logs_cloud.html#downloading_logs) und sie im lokalen Dateisystem speichern. Verwenden Sie dazu die Log Analysis-Befehlszeilenschnittstelle.

**Hinweis:** Standardmäßig ist Kibana so vorkonfiguriert, dass Protokolle zu Aktivitäten für einen Zeitraum von 30 Tagen angezeigt werden. Wenn in den letzten 30 Tagen keine Aktivitäten stattgefunden haben, wird die Nachricht *Keine Ergebnisse gefunden* angezeigt. Wenn Sie andere Protokolle anzeigen möchten, können Sie auf das Zeitgebersymbol in der rechten oberen Ecke unterhalb Ihres Benutzernamens klicken und einen längeren Zeitraum festlegen, z. B. Zeiträume wie *Jahr bis zum aktuellen Datum*.

## Kanäle überwachen
{: #monitor-channnels}

Rufen Sie den Network Monitor auf und suchen Sie nach dem Kanal in der Anzeige "Kanal", den Sie anzeigen und überwachen möchten. In der Anzeige des betreffenden Kanals können Sie die Datenstatusinformationen, Mitglieder und den instanziierten Chaincode dieses Kanals auf drei Registerkarten anzeigen:

### Kanalüberblick
{: #monitor-channel-overview}

Die Registerkarte "Kanalüberblick" zeigt die Blockinformationen zu diesem Kanal:
  * Eine Reihe von Datenpunkten, zu denen die Gesamtzahl der erstellten Blöcke gehört, das Zeitintervall seit der letzten Transaktion, die Anzahl von Chaincode-Instanziierungen und die Anzahl von Chaincode-Aufrufen.
  * Eine Tabelle, in der alle Blöcke dieses Kanals aufgeführt werden. Wenn Sie einen Block erweitern, werden detaillierte Informationen zu dem Block angezeigt.

  ![Kanalüberblick](../images/channel_overview_detail.png "Kanalüberblick")

### Mitglieder
{: #monitor-channel-members}

Auf der Registerkarte "Mitglieder" werden die Informationen der Mitglieder auf diesem Kanal einschließlich der E-Mail-Adressen für die Operatoren der Organisation angezeigt.

  ![Kanalmitglieder](../images/channel_members.png "Kanalmitglieder")

### Chaincode
{: #monitor-channel-cc}

Auf der Registerkarte "Chaincode" wird der gesamte Chaincode aufgelistet, der auf diesem Kanal instanziiert wird - mit der Chaincode-ID, der Version und der Anzahl Peers, die den Chaincode ausführen.

Erweitern Sie eine Chaincode-Zeile, um detaillierte Informationen zu dem Chaincode abzurufen:
  * Sie können auf **JSON** klicken, um die JSON-Datei des Chaincodes anzuzeigen.
  * Sie können auf **Protokolle** klicken, um Protokolle des Chaincodes anzuzeigen. In dieser Ansicht werden Protokolle angezeigt, in denen Angaben dazu enthalten sind, über welchen Peer der Chaincode installiert wurde. Diese Informationen können anhand des Chaincode-Namens und der Chaincode-Version gefiltert werden.

    Es wird empfohlen, eindeutige Erfolgs- oder Fehlernachrichten nach jeder Chaincode-Funktion hinzuzufügen, um die Überwachung und das Debuggen des Chaincodes zu vereinfachen. Bei einem komplexen Chaincode, der mit vielen verschiedenen Dateien arbeitet, können Sie ein eindeutiges Schlüsselwort zu den Chaincode-Protokollen hinzufügen, mit denen die Suche nach Nachrichten aus unterschiedlichen Transaktionsphasen vereinfacht wird.
   * Sie können auf **Löschen** klicken, um die aktiven Chaincode-Container zu entfernen. Beachten Sie hierbei, dass durch das Löschen des aktiven Chaincode-Containers der Chaincode selbst nicht gelöscht wird. Ein instanziierter Chaincode in einem Blockchain-Netz kann nicht gelöscht werden.

  ![Kanal-Chaincode](../images/channel_chaincode.png "Kanal-Chaincode")


## Chaincode überwachen
{: #monitor-chaincode}

Rufen Sie den Network Monitor auf und öffnen Sie die Anzeige "Code installieren". Wenn aktiver Chaincode vorhanden ist, wird der Chaincode mit den zugehörigen Chaincode-IDs und Chaincode-Versionen in der Tabelle angezeigt. Wählen Sie einen Peer in der Dropdown-Liste aus, um den gesamten Chaincode für diesen Peer in der Tabelle anzuzeigen. Sie können die Protokolle des Chaincodes auf der [Registerkarte "Chaincode"](#monitor-channel-cc) Ihrer speziellen Anzeige "Kanal" anzeigen.

  ![Chaincode](../images/installed_cc.png "Chaincode")

<!----
## Monitoring sample applications
{: #monitor-apps}

In a Starter Plan network, you can view and access sample applications in the "Try Samples" screen of the Network Monitor.  After you deploy a sample application, you can click the **Launch** button to enter your application interface, or the **View on GitHub** link to visit the code repository.  For more information, see [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html).

  ![Sample applications](../images/sampleappflow0.png "Sample applications")
--->

---

copyright:
  years: 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Best Practices für die Anwendungsentwicklung
{: #best-practices-app}

Dieser Leitfaden ist für Benutzer, die bereits die Grundlagen der Anwendungsentwicklung erlernt haben und zur Skalierung ihrer Lösung bereit sind. Machen Sie sich diese Best Practices zunutze, um die Leistung Ihres Netzes zu maximieren und um Anwendungsausfallzeiten zu vermeiden.
{:shortdesc}

## Anwendungskonnektivität und -verfügbarkeit
{: #best-practices-app-connectivity-availability}

Der [Transaktionsfluss ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Transaktionsfluss"){:new_window} von Hyperledger Fabric umfasst mehrere Komponenten, wobei die Clientanwendungen eine zentrale Rolle einnehmen. Das SDK übergibt Transaktionsvorschläge an die Peers, um die Bewilligung zu erhalten. Anschließend erfasst es die bewilligten Vorschläge, die an den Anordnungsservice gesendet werden müssen. Dieser sendet dann Blöcke von Transaktionen an die Peers, die den Ledgern der Kanäle hinzugefügt werden müssen. Entwickler von Produktionsanwendungen sollten darauf vorbereitet sein, dass ihre Interaktionen zwischen dem SDK und ihren Netzen mit der Zielsetzung der Effizienz und Verfügbarkeit verwaltet werden müssen.

### Transaktionen verwalten
{: #best-practices-app-managing-transactions}

Anwendungsclients müssen sicherstellen, dass ihre Transaktionsvorschläge überprüft werden und dass die Vorschläge erfolgreich abgeschlossen werden. Ein Vorschlag kann aus verschiedenen Gründen verzögert werden oder verloren gehen, z. B. durch einen Netzausfall oder einen Komponentenfehler. Sie sollten Ihre Anwendung für die [Hochverfügbarkeit](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) codieren, um bei Auftreten eines Komponentenfehlers handlungsfähig zu bleiben. Des Weiteren können Sie in Ihrer Anwendung die [Zeitlimitwerte erhöhen](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk), um zu verhindern, dass für Vorschläge Zeitlimitüberschreitungen auftreten, bevor das Netz reagieren kann.

Wenn ein Chaincode nicht ausgeführt wird, dann wird er durch den ersten Transaktionsvorschlag gestartet, der an den Chaincode gesendet wird. Während der Chaincode gestartet wird, werden alle anderen Vorschläge mit einer Fehlernachricht zurückgewiesen, in der Sie darüber informiert werden, dass der Chaincode gerade gestartet wird. Dieser Vorgang unterscheidet sich von der Transaktionsinvalidierung. Wird ein Vorschlag zurückgewiesen, während der Chaincode gestartet wird, dann müssen die Anwendungsclients die zurückgewiesenen Vorschläge nach Abschluss des Chaincodestarts erneut senden. Anwendungsclients können eine Nachrichtenwarteschlange verwenden, um den Verlust von Transaktionsvorschlägen zu vermeiden.

Sie können einen kanalbasierten Ereignisservice verwenden, um Transaktionen zu überwachen und Nachrichtenwarteschlangen zu erstellen. Die Klasse [channelEventHub ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} kann Listener auf Basis von Transaktions- und Blockereignissen sowie Chaincode-Ereignissen registrieren. Kanalbasierte Listener aus dem Kanalereignishub können auf mehrere Kanäle skaliert und zur Unterscheidung zwischen dem Datenverkehr unterschiedlicher Kanäle verwendet werden.

Es wird empfohlen, nicht die alte Klasse 'EventHub', sondern 'channelEventHub' zu verwenden. Die Klasse 'EventHub' ist ein Einzelthreadelement und enthält Ereignisse aus allen Kanälen, die Listener kanalübergreifend verlangsamen oder sogar blockieren könnten. Außerdem gewährleistet die Klasse "eventHub" nicht, dass ein Ereignis übermittelt wird, und bietet kein Verfahren zum Abrufen von Ereignissen aus  einer bestimmten Position wie beispielsweise einer Blocknummer, damit fehlende Ereignisse überwacht werden.

**Hinweis:** Die Klasse 'EventHub' für Peers wird in einem künftigen Release des Fabric-SDK nicht mehr unterstützt. Falls Sie Anwendungen einsetzen, die die Klasse 'EventHub' für Peers verwenden, aktualisieren Sie diese Anwendungen auf die Verwendung der Kanalklasse 'EventHub'. Weitere Informationen enthält der Abschnitt über die [Verwendung des kanalbasierten Ereignisservice ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Verwendung des kanalbasierten Ereignisservice"){:new_window} in der Node SDK-Dokumentation.

### Netzverbindungen öffnen und schließen
{: #best-practices-app-connections}

Wenn Sie mit dem SDK vor der Übergabe von Transaktionsvorschlägen Peer- und Anordnungsknotenobjekte erstellen, öffnen Sie eine gRPC-Verbindung zwischen Ihrer Anwendung und der Netzkomponente. Beispielsweise öffnet der folgende Befehl eine Verbindung zu `org1-peer1`. Diese Verbindung bleibt aktiv, während Ihre Anwendung ausgeführt wird.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

Beim Verwalten der Verbindungen zwischen Ihrer Anwendung und Ihrem Netz sind die folgenden Empfehlungen möglicherweise für Sie von Nutzen.

- Verwenden Sie bei der Interaktion mit Ihrem Netz Peer- und Anordnungsknotenobjekte wieder, statt neue Verbindungen für die Übergabe von Transaktionen zu öffnen. Die Wiederverwendung von Peer- und Anordnungsknotenobjekten kann Ressourcen sparen und ein besseres Leistungsverhalten ergeben.  
- Verwenden Sie [gRPC-Keepalive-Pakete ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "gRPC-Keepalive-Pakete"), um eine persistente Verbindung zu Ihren Netzkomponenten aufrecht zu erhalten. Keepalive-Pakete halten die gRPC-Verbindung aktiv und verhindern, dass eine nicht genutzte Verbindung geschlossen wird. Beim folgenden Beispiel für eine Peerverbindung werden gRPC-Optionen zum Objekt für die [Verbindungsoptionen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "Verbindung") hinzugefügt. Für die gRPC-Optionen werden von {{site.data.keyword.blockchainfull_notm}} Platform empfohlene Werte festgelegt.  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  Diese Variablen finden Sie auch bei den empfohlenen Einstellungen im Abschnitt `"peers"` Ihres Netzverbindungsprofils. Die empfohlenen Optionen werden automatisch in Ihre Anwendung importiert, falls Sie zum Herstellen der Verbindung zu Ihren Netzendpunkten das [Verbindungsprofil mit dem SDK verwenden](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile).

- Wenn eine Verbindung nicht mehr benötigt wird, verwenden Sie die Befehle `peer.close()` und `orderer.close()`, um Ressourcen freizugeben und Leistungseinbußen zu verhindern. Weitere Informationen enthalten die Angaben über die Klassen [peer close ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "peer close") und [orderer close![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "orderer close") in der Node SDK-Dokumentation. Falls Sie Peers und Anordnungsknoten mithilfe eines Verbindungsprofils zu einem Kanalobjekt hinzugefügt haben, können Sie alle Verbindungen, die diesem Kanal zugeordnet sind, mit einem Befehl `channel.close()` schließen.

### Hoch verfügbare Anwendungen
{: #best-practices-app-ha-app}

Als Verfahren zur Sicherstellung der Hochverfügbarkeit hat es sich bewährt, mindestens zwei Peers pro Organisation bereitzustellen, um so die Möglichkeit zum Failover zu schaffen. Sie müssen Ihre Anwendungen ebenfalls für die Hochverfügbarkeit anpassen. Installieren Sie den Chaincode auf beiden Peers und fügen Sie sie zu Ihren Kanälen hinzu. Anschließend müssen Sie an beide Peerendpunkte Transaktionsvorschläge übergeben, wenn Sie das Netz einrichten und eine Peerzielliste erstellen. Enterprise Plan-Netze sind zum Failover mit mehreren Anordnungsknoten ausgestattet, wodurch Ihre Clientanwendung bewilligte Transaktionen an einen anderen Anordnungsknoten senden kann, falls einer der Anordnungsknoten nicht verfügbar ist. Falls Sie stattdessen Ihr [Verbindungsprofil](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) verwenden, um Netzendpunkte manuell hinzuzufügen, müssen Sie sicherstellen, dass Ihr Profil auf dem aktuellen Stand ist und dass die zusätzlichen Peers und Anordnungsknoten im Abschnitt `channels` des Profils zum entsprechenden Kanal hinzugefügt wurden. Das SDK kann dann die Komponenten hinzufügen, die über das Verbindungsprofil mit dem Kanal verbunden sind.

## Gegenseitige TLS-Authentifizierung aktivieren
{: #best-practices-app-mutual-tls}

Wenn Sie Enterprise Plan-Netze ausführen, die Fabric V1.1 entsprechen, dann haben Sie die Option, für Ihre Anwendungen die [gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) zu aktivieren](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences). Wenn Sie MTLS aktivieren, müssen Sie die Anwendungen so aktualisieren, dass sie diese Funktion unterstützen. Andernfalls können Ihre Anwendungen nicht mit dem Netz kommunizieren.

Suchen Sie im Verbindungsprofil nach dem Abschnitt `certificateAuthorities`. Hier finden Sie die folgenden Attribute, die für die Eintragung und das Abrufen der Zertifikate für die Kommunikation mit dem Netz über MTLS erforderlich sind.

- `url`: URL für das Herstellen einer Verbindung zur Zertifizierungsstelle, die MTLS-Zertifikate ausstellen kann.
- `enrollId`: Eintragungs-ID für das Abrufen eines Zertifikats.
- `enrollSecret`: Geheimer Eintragungsschlüssel für das Abrufen eines Zertifikats.
- `x-tlsCAName`: Name der Zertifizierungsstelle für das Abrufen des Zertifikats, das der Anwendung die Kommunikation über MTLS (Mutual TLS; gegenseitige TLS-Authentifizierung) ermöglicht.

Weitere Informationen zum Aktualisieren der Anwendungen für die Unterstützung von MTLS finden Sie unter [How to configure mutual TLS ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "Mutual TLS"){:new_window}.


## (Optional) Zeitlimitwerte in Fabric-SDKs einrichten
{: #best-practices-app-set-timeout-in-sdk}

Mit Fabric-SDKs werden Standardzeitlimitwerte in Clientanwendungen für Ereignisse im Blockchain-Netz festgelegt. Das folgende Beispiel veranschaulicht die Standardzeitlimiteinstellungen im Fabric-Java-SDK. Der Dateipfad lautet `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

Möglicherweise müssen Sie jedoch die Standardzeitlimitwerte in Ihrer eigenen Anwendung ändern. Beispiel: Wenn Ihre Anwendung eine Transaktion mit einer Antwortzeit von mehr als 5000 ms (Standardzeitlimitwert für die Antwort einer Ereignishubverbindung) aufruft, wird ein Fehler aufgrund einer fehlgeschlagenen Transaktion ausgegeben, da das Aufrufereignis nach 5000 ms beendet wird, bevor die Transaktion abgeschlossen ist. Sie können die Systemeigenschaft so festlegen, dass die Standardwerte der Clientanwendung überschrieben werden. Da die Standardwerte vor dem Festlegen der Systemeigenschaft initialisiert werden, ist diese möglicherweise nicht wirksam. Daher müssen Sie die Systemeigenschaft für das Zeitlimit in einem statischen Konstrukt in der Clientanwendung festlegen. Das folgende Beispiel veranschaulicht das Ändern des Zeitlimitwerts für Ereignishubverbindungen in 15000 ms im Fabric-Java-SDK. Der Dateipfad lautet `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Wenn Sie das Node-SDK verwenden, können Sie die Zeitlimitwerte direkt in der aufgerufenen Methode angeben. Als Beispiel können Sie die folgende Zeile verwenden, um den Zeitlimitwert für die [Instanziierung eines Chaincodes ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") auf fünf Minuten zu erhöhen.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## Best Practices bei der Verwendung von CouchDB
{: #best-practices-app-couchdb-indices}

Wenn Sie CouchDB als Statusdatenbank verwenden, können Sie JSON-Datenabfragen aus dem Chaincode für die Statusdaten des Kanals ausführen. Es wird dringend empfohlen, Indizes für Ihre JSON-Abfragen zu erstellen und in Ihrem Chaincode zu verwenden. Mithilfe von Indizes können Ihre Anwendungen Daten effizient abrufen, während das Netz zusätzliche Blöcke von Transaktionen und Einträgen im World-Status hinzufügt.

Weitere Informationen zu CouchDB und zum Konfigurieren von Indizes enthält der Abschnitt über [CouchDB als Statusdatenbank![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} in der Hyperledger Fabric-Dokumentation. Im [Fabric-Lernprogramm für CouchDB ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html) finden Sie außerdem ein Beispiel, das einen Index mit Chaincode verwendet.

Verwenden Sie Chaincode nach Möglichkeit nicht für Abfragen, die das Durchsuchen der gesamten CouchDB-Datenbank zur Folge haben. Vollständige Datenbankscans führen zu langen Antwortzeiten und vermindern die Leistung Ihres Netzes. Zur Vermeidung von umfangreichen Abfragen können Sie einige der folgenden Schritte ausführen:
- Richten Sie in Ihrem Chaincode Indizes ein.
- Alle Felder im Index müssen auch im Selektor oder in den Sortierabschnitten Ihrer Abfrage für den Index vorhanden sein.
- Komplexere Abfragen haben eine geringere Leistung und werden weniger wahrscheinlich einen Index verwenden.
- Vermeiden Sie die Verwendung von Operatoren, die einen vollständigen Tabellenscan oder einen vollständigen Indexscan verursachen, beispielsweise `$or`, `$in` und `$regex`.

Beispiele zur Veranschaulichung zur Vorgehensweise bei der Verwendung von Indizes durch Abfragen sowie dazu, welcher Typ von Abfragen am leistungsfähigsten ist, finden Sie im [Lernprogramm zu Fabric CouchDB ![Symbol für externen Link](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes).

Peers unter {{site.data.keyword.blockchainfull_notm}} Platform haben eine festgelegte Abfragebegrenzung (queryLimit) und geben nur 10.000 Einträge aus der Statusdatenbank zurück. Wenn Ihre Abfrage die Abfragebegrenzung erreicht, können Sie mehrere Abfragen verwenden, um die restlichen Ergebnisse abzurufen. Wenn Sie mehr Ergebnisse aus einer Bereichsabfrage benötigen, beginnen Sie nachfolgende Abfragen mit dem letzten Schlüssel, der durch die vorherige Abfrage zurückgegeben wurde. Wenn Sie mehr Ergebnisse aus JSON-Abfragen benötigen, sortieren Sie Ihre Abfrage unter Verwendung einer der Variablen in Ihren Daten und verwenden Sie dann den letzten Wert aus der vorherigen Abfrage in einem "Größer-als"-Filter für die nächste Abfrage.

Fragen Sie zur Berichterstellung oder Aggregation nicht die gesamte Datenbank ab. Falls Sie im Rahmen Ihrer Anwendung ein Dashboard erstellen oder ein hohes Datenvolumen erfassen wollen, können Sie eine nicht zum Blockchain-Netz gehörende Datenbank abfragen, in der die Daten aus Ihrem Blockchain-Netz repliziert sind. Auf diese Weise können Sie sich über die Daten in der Blockchain informieren, ohne die Leistung Ihres Netzes zu beeinträchtigen oder Transaktionen zu unterbrechen.

Mit dem kanalbasierten Ereignisservice-Client, der in den Fabric-SDKs bereitgestellt wird, können Sie einen nicht zum Blockchain-Netz gehörenden Datenspeicher erstellen. Beispielsweise können Sie mit einem Blocklistener die letzten Transaktionen abrufen, die zu einem Kanalledger hinzugefügt wurden. Mit den Lese- und Schreibsätzen der gültigen Transaktionen kann dann eine Kopie des World-Status aktualisiert werden, die in einer separaten Datenbank gespeichert wurde. Weitere Informationen enthält der Abschnitt über die [Verwendung des kanalbasierten Ereignisservice ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Verwendung des kanalbasierten Ereignisservice"){:new_window} in der Node SDK-Dokumentation.

---

copyright:
  years: 2017
lastupdated: "2017-08-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Netz betreiben
{: #v10_dashboard}


Der Netzmonitor vermittelt einen Überblick über die Blockchain-Umgebung, einschließlich Netzkomponenten, Mitglieder, zugeordneter Kanäle, Leistungsdaten und bereitgestellter Chaincodes.
{:shortdesc}

Der Netzmonitor umfasst die folgenden Anzeigen: 
* In der Anzeige "Übersicht" können Sie Netzservice-Berechtigungsnachweise und Komponentenstatusinformationen anzeigen und Peers hinzufügen.
* Über die Anzeige "Mitglieder" können Sie Netzmitglieder und Zertifikate verwalten.
* Über die Anzeige "Kanäle" können Sie neue Kanäle erstellen und Informationen über die vorhandene Kanäle anzeigen.
* Über die Anzeige "Chaincode" können Sie Chaincodes auf den Peers installieren und instanziieren.
* Über die Anzeige "Benachrichtigungen" können Sie anstehende Genehmigungen bearbeiten und abgeschlossene Genehmigungen anzeigen.
* Auf der Anzeige "Support" können Sie Links zu Referenzressourcen und Informationen zu neuen und geänderten Funktionen in jedem Release anzeigen.


## Übersicht

Die Anzeige "Übersicht" zeigt Statusinformationen zu Blockchain-Komponenten (einschließlich Anordnungs-, CA- und Peerknoten) in Echtzeit an. Jede Komponente wird unter vier unterschiedlichen Überschriften angezeigt: **Typ**, **Name**, **Status** und **Aktionen**. Bei der Erstellung des Blockchain-Netzes werden automatisch drei Anordnungsknoten und zwei CA-Knoten erstellt. Die Zertifizierungsstellen (Certificate Authorities, CAs) beziehen sich auf bestimmte Mitglieder, während die Anordnungsknoten allgemeine Endpunkte sind, die im gesamten Netz genutzt werden.

**Abbildung 1** zeigt die Anzeige "Übersicht": 

![Anzeige 'Übersicht'](images/myresources.png "Netzübersicht")
*Abbildung 1. Netzübersicht*

- Knotenaktionen

  Unter der Überschrift **Aktionen** befinden sich Schaltflächen zum Starten oder Stoppen der Komponenten. Sie können auch eine Gruppe von Knoten starten oder stoppen, indem Sie mehrere Knoten auswählen und dann auf **Ausgewählte starten** oder **Ausgewählte stoppen** klicken. Die Schaltfläche **Ausgewählte starten** oder **Ausgewählte stoppen** wird oben in der Tabelle angezeigt, wenn Sie einen oder mehrere Knoten auswählen.

  Sie können auch Komponentenprotokolle prüfen, indem Sie in der Dropdown-Liste unter der Überschrift **Aktionen** auf **Protokolle anzeigen** klicken. Die Protokolle zeigen die Prozedurfernaufrufe zwischen verschiedenen Netzkomponenten und erweisen sich für die Fehlerbehebung als hilfreich. Experimentieren Sie z. B. ein wenig, indem Sie einen Peer stoppen und ihn als Ziel einer Transaktion auswählen, was jedoch zu gRPC-Verbindungfehlern führt. Wenn Sie den Peer erneut starten und versuchen, die Transaktion auszuführen, wird eine erfolgreiche Verbindung angezeigt. Sie können einen Peer auch für einen längeren Zeitraum inaktiv lassen, während über Ihre Kanäle weiterhin Transaktionen durchgeführt werden. Wenn der Peer wieder aktiviert wird, werden Sie bemerken, dass mithilfe des Gossip-Protokolls eine Synchronisierung des Hauptbuchs durchgeführt wird. Sobald der Peer vollständig das Hauptbuch synchronisiert hat, können Sie normale Aufrufe und Abfragen ausführen.  
- Serviceberechtigungsnachweise  
  Sie können die JSON-Datei zu Low-Level-Netzinformationen zu den einzelnen Komponenten anzeigen, indem Sie auf die Schaltfläche **Serviceberechtigungsnachweise** oben rechts auf der Registerkarte "Ressourcen" klicken. Dies sind die Konfigurationsinformationen, die Sie für eine Anwendung benötigen. Beachten Sie jedoch, dass diese Datei nur die Adressen für Ihre spezifischen Komponenten und für die gemeinsam genutzten Anordnungsknoten enthält. Wenn Sie zusätzliche Peers als Ziel angeben müssen, müssen Sie diese Endpunkte abrufen. Unter der Überschrift "url" wird der API-Endpunkt für jede Komponente angezeigt. Diese Endpunkte sind für die zielgerichtete Verwendung bestimmter Netzkomponenten von einer clientseitigen Anwendung erforderlich und ihre Definitionen befinden sich üblicherweise in einer JSON-modellierten Konfigurationsdatei als Bestandteil der App. Wenn Sie eine Anwendung anpassen, für die eine Bewilligung durch Peers erforderlich ist, die nicht Teil Ihrer Organisation sind, dann müssen Sie die IP-Adressen dieser Peers von den entsprechenden Operatoren mit einer Out-of-band-Operation abrufen. Clients müssen eine Verbindung zu allen Peers herstellen können, von denen sie eine Antwort benötigen.  
- Peers hinzufügen  
    Klicken Sie auf die Schaltfläche **Peers hinzufügen** in der rechten oberen Ecke, um Peerknoten zu Ihrem Netz hinzuzufügen. Jedes Mitglied kann bis zu sechs Peers in einem Netz hinzufügen. Sie können Peerknoten hinzufügen, wenn Sie ein Netz erstellen oder einem Netz beitreten - oder später im Netzmonitor.   
  Wählen Sie im Popup-Fenster "Peers hinzufügen" die Anzahl und Größe der Peerknoten aus, die Sie hinzufügen wollen. Derzeit sind nur "kleine" Peers erhältlich. Zu einem späteren Zeitpunkt wird es aber "mittlere" und "große" Peers geben, um größere Workloads und einen höheren Transaktionsdurchsatz zu ermöglichen. Details zur Dimensionierung von Peers sowie Leistungsmetriken werden in Kürze zur Verfügung gestellt. 
  
## Mitglieder

Die Anzeige "Mitglieder" enthält zwei Registerkarten. Auf der Registerkarte "Mitglieder" werden Informationen zu Netzmitgliedern angezeigt und auf der Registerkarte "Zertifikate" werden Zertifikatsinformationen angezeigt. 

**Abbildung 2** zeigt die erste Anzeige "Mitglieder", die Ihre Netzmitglieder im Netz auf der Registerkarte "Mitglieder" anzeigt: 

![Registerkarte 'Mitglieder' in der Anzeige 'Mitglieder'](images/monitor_members.png "Netzmitglieder")
*Abbildung 2. Netzmitglieder*

Neben den Mitgliedern, die Sie einladen, wenn Sie das Netz erstellen, können Sie weitere Mitglieder auf der Registerkarte "Mitglieder" einladen. Um ein Mitglied in Ihr Netz einzuladen, geben Sie den Namen der Institution und die E-Mail-Adresse des Operators ein und klicken Sie auf **Mitglied hinzufügen**. Ein Netz kann insgesamt 15 Mitglieder haben (einschließlich des Netzinitiators). Um ein Mitglied aus Ihrem Netz zu entfernen, klicken Sie auf das Symbol "Entfernen" am Ende der Zeile des Mitglieds. 

**Abbildung 3** zeigt die erste Anzeige "Mitglieder", die Mitgliederzertifikate auf der Registerkarte "Zertifikate" anzeigt: 

![Registerkarte 'Zertifikate' in der Anzeige 'Mitglieder'](images/monitor_certificates.png "Zertifikate")
*Abbildung 3. Zertifikate*

Operatoren können die Zertifikate für die Mitglieder in derselben Institution auf der Registerkarte "Zertifikate" verwalten.
Klicken Sie auf **Zertifikat hinzufügen**, um die Anzeige "Zertifikat hinzufügen" zu öffnen. Geben Sie Ihrem Zertifikat einen Namen, fügen Sie die clientseitigen Zertifikate im PEM-Format in das Feld "Schlüssel" ein und klicken Sie auf **Übergeben**. Sie müssen die Peers erneut starten, damit die clientseitigen Zertifikate wirksam werden.

Weitere Informationen zum Generieren Ihres Zertifikatsschlüssels finden Sie unter [Clientseitige Zertifikate generieren](v10_application.html#generating-the-client-side-certificates).

## Kanäle

Sie können Ihr Netz in Kanäle aufteilen, wobei jeder Kanal eine Untergruppe von Mitgliedern darstellt, die die Daten für die Chaincodes anzeigen dürfen, die für diesen Kanal instanziiert sind. Jedes Netz muss mindestens einen Kanal aufweisen, damit Transaktionen durchgeführt werden können. Jeder Kanal verfügt über ein eindeutiges Hauptbuch (Ledger) und Benutzer müssen ordnungsgemäß authentifiziert sein, um Lese-/Schreiboperationen für dieses Hauptbuch durchführen zu können. Befinden Sie sich nicht auf diesem Kanal, können Sie die Daten nicht sehen. 

**Abbildung 4** zeigt die Anfangsanzeige des Dashboards mit einer Übersicht aller Kanäle in Ihrem Netz:

![Kanäle](images/channels.png "Kanäle")
*Abbildung 4. Kanäle*

Beim Erstellen eines Kanals wird ein kanalspezifisches Erstellung generiert. Weitere Informationen finden Sie unter [Kanal erstellen](howto/create_channel.html).

Sie können auch einen vorhandenen Kanal auswählen, um genauere Details zu Kanal, Mitgliedschaft und aktiven Chaincodes anzuzeigen. Weitere Informationen finden Sie unter [Netz überwachen](howto/monitor_network.html).  


## Chaincode

Chaincode definiert die Geschäftslogik und die transaktionsorientierten Anweisungen zum Erstellen und Ändern von Assets.

**Abbildung 5** zeigt die Anfangsanzeige des Dashboards für Chaincodes:

![Chaincodes](images/chaincode_install_overview.png "Chaincodes")
*Abbildung 5. Chaincodes*

Chaincode wird zunächst im Dateisystem eines Peers installiert und anschließend auf einem Kanal instanziiert. Weitere Informationen finden Sie unter [Chaincode installieren und instanziieren](howto/install_instantiate_chaincode.html).


## Benachrichtigungen

In der Anzeige "Benachrichtigungen" können Sie anstehende Anforderungen bearbeiten und abgeschlossene Anforderungen anzeigen. 

**Abbildung 6** zeigt die Anzeige "Benachrichtigungen": 

![Benachrichtigungen](images/notifications.png "Benachrichtigungen")
*Abbildung 6. Benachrichtigungen*

* Wenn Sie einen Kanal erstellen oder zu einem neuen Kanal eingeladen werden, wird eine Benachrichtigung im Netzmonitor angezeigt. 
* Die Anforderungen werden unter den untergeordneten Registerkarten "Alle", "Anstehend" und "Abgeschlossen" gruppiert. Die Zahlen hinter der Registerkartenüberschrift geben die Anzahl der Anforderungen in den einzelnen untergeordneten Registerkarten an.
   * In der untergeordneten Registerkarte "Alle" werden alle Ihre Anforderungen angezeigt. 
   * Anforderungen, die Sie nicht akzeptiert oder abgelehnt haben oder die Sie noch nicht angezeigt haben, befinden sich auf der untergeordneten Registerkarte "Nicht gelesen". Klicken Sie auf die Schaltfläche **Anforderung überprüfen**, um die Anforderung anzuzeigen, die die Kanalrichtlinie und Mitglieder enthält, und wählen Sie anschließend **Akzeptieren** oder **Ablehnen** aus. Sie können die Anforderung auch zu einem späteren Zeitpunkt bearbeiten, indem Sie auf **Später** klicken. Wenn Sie eine Anforderung akzeptieren und die Anforderung von einer ausreichenden Zahl von Kanaloperatoren akzeptiert wird, können Sie auf **Anforderung übergeben** klicken, um die Kanalaktualisierung zu aktivieren.  
   * Übergebene Anforderungen werden auf der untergeordneten Registerkarte "Abgeschlossen" angezeigt. Sie können auf **Anforderung überprüfen** klicken, um die zugehörigen Details anzuzeigen. 
  
Wenn Sie eine lange Liste von Anforderungen haben, können Sie im Suchfeld oben nach einer Anforderung suchen. Anstehende Anforderungen können gelöscht werden, indem Sie die davor befindlichen Felder auswählen und auf **Anforderungen löschen** klicken. Beachten Sie, dass eine abgeschlossene Anforderung nicht gelöscht werden kann.


## Support

Die Anzeige "Support" enthält zwei Registerkarten. Auf der Registerkarte "Support" werden Support-Informationen bereitgestellt und auf der Registerkarte "Releaseinformationen" werden neue und geänderte Funktionen für jedes Release beschrieben.

Über die Links und Ressourcen auf dieser Seite können Sie auf Fehlerbehebungs- und Unterstützungsforen zugreifen. Wenn Sie Ihr Problem nicht beheben können oder keine Antwort auf Ihre Frage finden, klicken Sie auf den Link **Bluemix-Support-Ticket öffnen** und folgen Sie den Anweisungen zum Abschicken von Tickets.

**Abbildung 7** zeigt die "Support"-Anfangsanzeige mit Unterstützungsinformationen auf der Registerkarte "Support":

![Support](images/support.png "Support")
*Abbildung 7. Blockchain-Support*

* [{{site.data.keyword.blockchainfull_notm}}-Servicedokumente](index.html) (also diese Site mit Dokumentation) mit Hinweisen zum Einstieg in {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.Bluemix_notm}}. Die einzelnen Themen können Sie über den Navigator aufrufen oder Sie können in der oben befindlichen Suchfunktion einen Suchbegriff eingeben.  
* [IBM Developer Works ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/blockchain/) unter **Community-Hilfe** enthält Ressourcen und Informationen für Entwickler.   
* [IBM dWAnswers ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/answers/smartspace/blockchain/) unter **Support-Ticket** dient als Plattform für Fragen und Antworten. Sie können nach Antworten auf bereits gestellte Fragen suchen oder eine neue Frage stellen. Achten Sie darauf, das Stichwort **blockchain** in Ihrer Frage zu verwenden.   
  Sie können auch ein Ticket an das {{site.data.keyword.blockchainfull_notm}}-Support-Team schicken. Verwenden Sie dazu die Option [{{site.data.keyword.Bluemix_notm}}-Support-Ticket öffnen![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](). Hier können Sie Details und Code-Snippets aus Ihrer jeweiligen Bluemix-Instanz gemeinsam nutzen.  
* [Beispielanwendungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")]() unter **{{site.data.keyword.blockchain}}-Beispielanwendungen** bietet Anleitungen und Beispielcodeausschnitte zur Unterstützung bei der Anwendungsentwicklung.   
* [Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/) und [Hyperledger Fabric-Community ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")]() unter **Hyperledger Fabric** bieten nähere Details zum Hyperledger Fabric-Stack.  
  Wenden Sie sich an einen [Hyperledger-Experten ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://chat.hyperledger.org/channel/general) mit Fragen zum Hyperledger Fabric-Code.   
  
  
**Abbildung 8** zeigt die erste Anzeige "Mitglieder" mit den neuen und geänderten Funktionen für jedes Release auf der Registerkarte "Releaseinformationen":

![Releaseinformationen](images/releasenotes.png "Releaseinformationen")
*Abbildung 8. Releaseinformationen*


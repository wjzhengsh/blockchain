---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Starter Plan-Netz betreiben
{: #operate-starter-plan-network}

{{site.data.keyword.blockchainfull}} Platform enthält einen Network Monitor, der eine Übersicht über Ihre Blockchain-Umgebung, einschließlich Netzressourcen, Mitgliedern, zugeordneter Kanäle, Transaktionsleistungsdaten und bereitgestelltem Chaincode, zur Verfügung stellt. Der Network Monitor stellt auch den Eingangspunkt zur Ausführung von Swagger-APIs dar. Sie können ein Netz mit {{site.data.keyword.blockchainfull_notm}} Platform: Develop entwickeln und Beispielanwendungen ausprobieren.
{:shortdesc}

Sie können im Network Monitor [den Namen des Starter Plan-Netzes ändern](#sp-network-name) oder [zwischen verschiedenen von Ihnen erstellten Starter Plan-Netzen wechseln](#switch-sp-network).

Der Network Monitor präsentiert die folgenden Anzeigen in drei Abschnitten. Sie können über den Navigator auf der linken Seite zu jeder Anzeige im Network Monitor navigieren.
- Der Abschnitt **Eigenes Netz** enthält die Anzeigen "[Übersicht](#overview)", "[Mitglieder](#members)", "[Kanäle](#channels)", "[Benachrichtigungen](#notifications)" und "[APIs](#apis)".
- Der Abschnitt **Eigener Code** enthält die Anzeigen "[Code entwickeln](#write-code)", "[Code installieren](#chaincode)" und "[Beispiele ausprobieren](#samples)".
- Die Anzeige "[Hilfe anfordern](#support)".

Über das Dropdown-Menü in der rechten oberen Ecke im Network Monitor können Sie [zwischen Organisationen wechseln](#switch-organizations), deren Eigner Sie sind, [Netzvorgaben überprüfen](#network-preferences) und [das Netz zurücksetzen](#reset-network).

In diesem Lernprogramm werden die oben aufgeführten Anzeigen und Funktionen beschrieben.

## Netznamen aktualisieren
{: #sp-network-name}

Wenn Sie ein Starter Plan-Netz erstellen, weist {{site.data.keyword.blockchainfull_notm}} Platform dem Netz einen Namen zu. Sie können diesen Netznamen jedoch zu jedem beliebigen Zeitpunkt im Network Monitor aktualisieren.

Klicken Sie oben im linken Navigator im Network Monitor auf den Netznamen. Damit wird die Datei bearbeitbar. Geben Sie den neuen Netznamen ein, den Sie verwenden möchten, und drücken Sie die **Eingabetaste**. Die Aktualisierung des Netznamens nimmt einige Sekunden in Anspruch.

**Abbildung 1** zeigt die Schritte zur Aktualisierung des Starter Plan-Netznamens vom zugewiesenen Namen in "Starter Plan Network".

![Netznamen aktualisieren](images/update_network_name.gif "Netznamen aktualisieren")
*Abbildung 1. Netznamen aktualisieren*


## Zwischen Starter Plan-Netzen wechseln
{: #switch-sp-network}

Wenn Sie mehrere Netze mit dem Starter Plan erstellen, können Sie im Network Monitor zwischen den Netzen wechseln.

Klicken Sie oben im linken Navigator im Network Monitor auf das Pfeilsymbol neben dem Netznamen. Wählen Sie den Namen des Netzes, zu dem Sie wechseln möchten, in der Dropdown-Liste aus und klicken Sie darauf. Der Web-Browser wird aktualisiert und der Network Monitor wird für das Netz, zu dem Sie wechseln, geöffnet.

**Abbildung 2** zeigt die Schritte zum Wechseln zu einem anderen Starter Plan-Netz.

![Netz wechseln](images/switch_network.gif "Netz wechseln")
*Abbildung 2. Netz wechseln*


## Übersicht
{: #overview}

Die Anzeige "Übersicht" zeigt Statusinformationen zu Ihren Blockchain-Ressourcen (einschließlich Anordnungsservice, Zertifizierungsstelle und Peers) in Echtzeit an. Jede Ressource wird unter vier unterschiedlichen Überschriften angezeigt: **Typ**, **Name**, **Status** und **Aktionen**. Wenn Ihr Netz gestartet wird, werden ein Anordnungsknoten, eine Zertifizierungsstelle (CA) und ein Peer ausgeführt. Die Zertifizierungsstelle ist für die Organisation spezifisch, während der Anordnungsknoten ein allgemeiner Endpunkt ist, der im gesamten Netz gemeinsam genutzt wird.

**Abbildung 3** zeigt die Anzeige "Übersicht":

![Anzeige 'Übersicht'](images/myresources_starter.png "Netzübersicht")
*Abbildung 3. Netzübersicht*

### Knotenaktionen
  Unter der Überschrift **Aktionen** befinden sich Schaltflächen zum Starten oder Stoppen der Komponenten. Sie können auch eine Gruppe von Knoten starten oder stoppen, indem Sie mehrere Knoten auswählen und dann auf **Ausgewählte starten** oder **Ausgewählte stoppen** klicken. Die Schaltfläche **Ausgewählte starten** oder **Ausgewählte stoppen** wird oben in der Tabelle angezeigt, wenn Sie einen oder mehrere Knoten auswählen.

  Sie können auch Komponentenprotokolle prüfen, indem Sie in der Dropdown-Liste unter der Überschrift **Aktionen** auf **Protokolle anzeigen** klicken. Die Protokolle zeigen die Aufrufe zwischen den verschiedenen Netzressourcen und sind für die Fehlersuche und -behebung von Nutzen.

  **Hinweis** für {{site.data.keyword.blockchainfull_notm}}: Wenn Sie in der Benutzerschnittstelle für das Starter Plan-Netz auf die Aktion 'Protokolle anzeigen' für die Knoten klicken, die in der Übersichtsanzeige aufgeführt sind, wird die Kibana-Schnittstelle für IBM Cloud Logging geöffnet. Standardmäßig ist Kibana so vorkonfiguriert, dass Protokolle mit Aktivitäten der letzten 15 Minuten angezeigt werden. Wenn in den letzten 15 Minuten keine Aktivitäten stattgefunden haben, wird die Nachricht 'Keine Ergebnisse gefunden' angezeigt. Wenn Sie alle Protokolle anzeigen möchten, klicken Sie auf das Zeitgebersymbol in der rechten oberen Ecke unterhalb des Benutzernamens und legen Sie einen längeren Zeitraum fest, z. B. die aktuelle Woche oder den aktuellen Monat.

  Zur Veranschaulichung der Effekte des Startens und Stoppens eines Peers können Sie einen Peer probeweise stoppen und dann versuchen, eine Transaktion an diesen Peer zu senden; in den Protokollen werden daraufhin Konnektivitätsfehler angezeigt. Wenn Sie den Peer erneut starten und die Transaktion erneut versuchen, wird eine erfolgreiche Verbindung angezeigt. Sie können einen Peer auch für einen längeren Zeitraum inaktiv lassen, während über Ihre Kanäle weiterhin Transaktionen durchgeführt werden. Wenn der Peer wieder aktiviert wird, werden Sie eine Synchronisation des Ledgers bemerken, da der Peer die Blöcke empfängt, die festgeschrieben wurden, als er inaktiv war. Wenn das Ledger vollständig synchronisiert wurde, können Sie normale Aufrufe und Abfragen für das Ledger ausführen.

### Verbindungsprofil
  Sie können die JSON-Datei zu Low-Level-Netzinformationen zu den einzelnen Ressourcen anzeigen, indem Sie auf die Schaltfläche **Verbindungsprofil** klicken. Das Verbindungsprofil enthält sämtliche Konfigurationsinformationen, die Sie für eine Anwendung benötigen. Da diese Datei jedoch nur die Adressen für Ihre bestimmten Komponenten und den Anordnungsknoten enthält, müssen Sie, wenn weitere Peers als Ziele hinzugefügt werden sollen, deren Endpunkte ermitteln. Der Header, der "url" enthält, zeigt den API-Endpunkt jeder Komponente an. Diese Endpunkte sind für die zielgerichtete Verwendung bestimmter Netzkomponenten von einer clientseitigen Anwendung erforderlich und ihre Definitionen befinden sich üblicherweise in einer JSON-modellierten Konfigurationsdatei, die die App begleitet. Wenn Sie eine Anwendung anpassen, für die eine Bewilligung durch Peers erforderlich ist, die nicht Teil Ihrer Organisation sind, dann müssen Sie die IP-Adressen dieser Peers von den entsprechenden Operatoren mit einer Out-of-band-Operation abrufen. Clients müssen eine Verbindung zu allen Peers herstellen können, von denen sie eine Antwort benötigen.

### Peers hinzufügen
{: #peers}
  Netzmitglieder müssen Peers haben, um ihre Kopien des Netzhauptbuchs zu speichern und Chaincode zum Abfragen und Aktualisieren des Ledgers auszuführen. Wenn die Bewilligungsrichtlinie einen Peer als bewilligenden Peer definiert, gibt der Peer außerdem Bewilligungsergebnisse an Anwendungen zurück.

  Der Starter Plan erstellt standardmäßig einen Peer für jede der beiden Organisationen. Sie können weitere Peers für Ihre Organisationen Ihren Anforderungen entsprechend hinzufügen. In anderen Szenarios können auch mehr Peers erforderlich sein. Sie könnten mehrere Peers zum Beispiel zu Redundanzzwecken verwenden, um sie mit demselben Kanal zu verknüpfen. Jeder Peer verarbeitet die Transaktionen des Kanals und schreibt in seine jeweilige Kopie des Ledgers. Wenn einer der Peers ausfällt, kann der andere Peer (oder auch mehrere andere Peers) die Verarbeitung von Transaktionen und Anwendungsanforderungen fortsetzen. Sie können außerdem alle Anwendungsanforderungen symmetrisch auf die Peers verteilen oder Sie können verschiedene Peers für verschiedene Funktionen vorsehen. Sie können zum Beispiel einen Peer zum Abfragen des Ledgers und einen anderen Peer zur Verarbeitung von Bewilligungen für Ledgeraktualisierungen verwenden.

  Klicken Sie auf die Schaltfläche **Peers hinzufügen** in der rechten oberen Ecke, um Ihrem Netz Peerknoten hinzuzufügen. Wählen Sie im Popup-Fenster "Peers hinzufügen" die Anzahl und Größe der Peerknoten aus, die Sie hinzufügen wollen.


## Mitglieder
{: #members}

Die Anzeige "Mitglieder" enthält zwei Registerkarten. Auf der Registerkarte "Mitglieder" werden Informationen zu Netzmitgliedern angezeigt und auf der Registerkarte "Zertifikate" werden Zertifikatsinformationen angezeigt.

### Mitglieder
{: #members_tab}
**Abbildung 4** zeigt die erste Anzeige "Mitglieder", die Ihre Netzmitglieder im Netz auf der Registerkarte "Mitglieder" anzeigt:

![Registerkarte 'Mitglieder' in der Anzeige 'Mitglieder'](images/monitor_members_starter.png "Netzmitglieder")
*Abbildung 4. Netzmitglieder*

Klicken Sie auf **Mitglied hinzufügen**, um weitere Mitglieder in Ihr Netz einzuladen. Im Starter Plan haben Sie zwei Optionen:
- **Mitglied einladen**. Sie können andere Organisationen einladen, Mitglieder Ihres Netzes zu werden. Die eingeladenen Organisationen können dann teilnehmen und mit Ihnen im Netz zusammenarbeiten.
- **Mitglied erstellen**. Sie können auch ein Mitglied erstellen, indem Sie Ihre eigene E-Mail-Adresse verwenden. Sie haben dann ebenso Kontrolle über dieses Mitglied wie über die beiden Organisationen, die Sie standardmäßig mit dem Starter Plan empfangen.

**Abbildung 5** zeigt das Fenster "Mitglied hinzufügen".

![Mitglied hinzufügen](images/invite_member_starter.png "Mitglied hinzufügen")
*Abbildung 5. Mitglied hinzufügen*

### Zertifikate
**Abbildung 6** zeigt die erste Anzeige "Mitglieder", die Mitgliedszertifikate auf der Registerkarte "Zertifikate" anzeigt:

![Registerkarte 'Zertifikate' in der Anzeige 'Mitglieder'](images/monitor_certificates_starter.png "Zertifikate")
*Abbildung 6. Zertifikate*

Operatoren können die Zertifikate für die Mitglieder in derselben Institution auf der Registerkarte "Zertifikate" verwalten. Klicken Sie auf **Zertifikat hinzufügen**, um die Anzeige "Zertifikat hinzufügen" zu öffnen. Geben Sie Ihrem Zertifikat einen Namen, fügen Sie die clientseitigen Zertifikate im PEM-Format in das Feld "Schlüssel" ein und klicken Sie auf **Übergeben**. Sie müssen die Peers erneut starten, damit die clientseitigen Zertifikate wirksam werden.

Weitere Informationen zur Generierung Ihres Zertifikatsschlüssels finden Sie unter [Clientseitige Zertifikate generieren](v10_application.html#generating-the-client-side-certificates).


## Kanäle
{: #channels}

Kanäle, die aus einer Untergruppe von Netzmitgliedern bestehen, die private Transaktionen ausführen wollen, sorgen für Datenisolation und Vertraulichkeit, indem sie es den Mitgliedern eines Kanals ermöglichen, bestimmte Regeln und ein separates Ledger einzurichten, auf das nur die Kanalmitglieder Zugriff haben. Jedes Netz muss mindestens einen Kanal aufweisen, damit Transaktionen durchgeführt werden können. Jeder Kanal verfügt über ein eindeutiges Ledger und Benutzer müssen ordnungsgemäß authentifiziert sein, um Lese-/Schreiboperationen für dieses Ledger durchführen zu können. Befinden Sie sich nicht auf diesem Kanal, können Sie die Daten nicht sehen.

**Abbildung 7** zeigt die Anfangsanzeige des Dashboards mit einer Übersicht aller Kanäle in Ihrem Netz:

![Kanäle](images/channels_starter.png "Kanäle")
*Abbildung 7. Kanäle*

Durch das Erstellen eines Kanals wird ein kanalspezifisches Ledger generiert. Weitere Informationen finden Sie unter [Kanal erstellen](howto/create_channel.html).

Sie können auch einen vorhandenen Kanal auswählen, um genauere Details zu Kanal, Mitgliedschaft und aktivem Chaincode anzuzeigen. Weitere Informationen finden Sie unter [Netz überwachen](howto/monitor_network.html).


## Benachrichtigungen
{: #notifications}

In der Anzeige "Benachrichtigungen" können Sie anstehende Anforderungen bearbeiten und abgeschlossene Anforderungen anzeigen.

**Abbildung 8** zeigt die Anzeige "Benachrichtigungen":

![Benachrichtigungen](images/notifications_starter.png "Benachrichtigungen")
*Abbildung 8. Benachrichtigungen*

Wenn Sie einen Kanal erstellen oder in einen neuen Kanal eingeladen werden, wird eine Benachrichtigung im Network Monitor angezeigt.

Die Anforderungen werden auf den untergeordneten Registerkarten "Alle", "Anstehend" und "Abgeschlossen" gruppiert. Die Zahlen hinter der Überschrift geben die Anzahl der Anforderungen auf den einzelnen untergeordneten Registerkarten an.
   * Auf der untergeordneten Registerkarte "Alle" werden alle Ihre Anforderungen angezeigt.
   * Anforderungen, die Sie nicht akzeptiert oder abgelehnt haben oder die Sie noch nicht angezeigt haben, befinden sich auf der untergeordneten Registerkarte "Anstehend". Klicken Sie auf die Schaltfläche **Anforderung überprüfen**, um die Anforderung anzuzeigen, die die Kanalrichtlinie und Mitglieder sowie den Abstimmungsstatus enthält. Ein Kanaloperator kann die Anforderung entweder **Akzeptieren** oder **Ablehnen** oder sie zu einem anderen Zeitpunkt bearbeiten, indem er auf **Später** klickt. Wenn die Anforderung von einer ausreichenden Zahl von Kanaloperatoren akzeptiert wird, können Sie auf **Anforderung übergeben** klicken, um die Kanalaktualisierung zu aktivieren.
   * Übergebene Anforderungen werden auf der untergeordneten Registerkarte "Abgeschlossen" angezeigt.  Sie können auf **Anforderung überprüfen** klicken, um die zugehörigen Details anzuzeigen.

Wenn Sie eine lange Liste von Anforderungen haben, können Sie im Suchfeld oben nach einer Anforderung suchen.

Anstehende Anforderungen können gelöscht werden, indem Sie die davor befindlichen Felder auswählen und auf **Anforderung löschen** klicken.


## APIs
{: #apis}

Zur Vereinfachung der Anwendungsentwicklung stellt {{site.data.keyword.blockchainfull_notm}} Platform APIs zur Verfügung, die Sie für Ihr Netz in einer Swagger-Benutzerschnittstelle (UI) testen können.

**Abbildung 9** zeigt die Anzeige "APIs":

![APIs](images/API_screen_starter.png "APIs")
*Abbildung 9. APIs*

Klicken Sie auf den Link für die **Swagger-Benutzerschnittstelle (UI)**, um die Swagger-Benutzerschnittstelle zu öffnen. Beachten Sie, dass Sie die Swagger-Benutzerschnittstelle mit Ihren Netzberechtigungsnachweisen (die auf dieser Seite "APIs" zu finden sind) berechtigen müssen, bevor Sie die APIs ausführen können. Weitere Informationen finden Sie unter [APIs mit Swagger ausprobieren](howto/swagger_apis.html).


## Code entwickeln
{: #write-code}

Der Starter Plan integriert {{site.data.keyword.blockchainfull_notm}} Platform: Develop und stellt eine Entwicklungsumgebung mit Tools und Technologien bereit, die dem Industriestandard entsprechen. Sie können Ihr Unternehmensnetz in der Umgebung online oder lokal entwickeln. Wenn Sie ein Unternehmensnetz entwickelt haben, können Sie es wieder in Ihrem Starter Plan-Netz bereitstellen.

**Abbildung 10** zeigt die Anzeige "Code entwickeln":

![Code entwickeln](images/write_code_starter.png "Code entwickeln")
*Abbildung 10. Code entwickeln*

Weitere Informationen zum Entwickeln von Unternehmensnetzen und zur Bereitstellung eines Netzes als Starter Plan-Netz finden Sie in [Unternehmensnetze mit dem Starter Plan entwickeln](develop_starter.html).


## Code installieren
{: #chaincode}

Bei Chaincode, der auch als "Smart Contract" bezeichnet wird, handelt es sich um Stücke von Software, die eine Gruppe von Funktionen zum Abfragen und Aktualisieren des Ledgers enthalten. Sie werden auf Peers installiert und auf einem Kanal instanziiert.

**Abbildung 11** zeigt die Anzeige "Code installieren":

![Code installieren](images/chaincode_install_overview_starter.png "Code installieren")
*Abbildung 11. Chaincode*

Ein Chaincode wird zunächst im Dateisystem eines Peers installiert und anschließend auf einem Kanal instanziiert. Weitere Informationen finden Sie in [Chaincode installieren, instanziieren und aktualisieren](howto/install_instantiate_chaincode.html).


## Beispiele ausprobieren
{: #samples}

Beispielanwendungen helfen Ihnen, sich mit einem Blockchain-Netz und der Anwendungsentwicklung vertraut zu machen. Der Starter Plan bietet die Möglichkeit, Beispielanwendungen über den Network Monitor bereitzustellen und zu starten.

**Abbildung 12** zeigt die Anzeige "Beispiele ausprobieren":

![Beispiele ausprobieren](images/sample_overview_starter.png "Beispiel ausprobieren")
*Abbildung 12. Beispiele*

Die Beispielbereitstellung nutzt den [DevOps Toolchain-Service ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/devops/toolchains), um Ihren Prozess der Quellcodeverwaltung und Zustellungspipeline zu automatisieren und den Chaincode zu aktivieren. Wählen Sie eine Beispielanwendung aus und klicken Sie auf **Über Toolchain bereitstellen**. Weitere Informationen finden Sie unter [Beispielanwendungen bereitstellen](howto/prebuilt_samples.html).


## Hilfe anfordern
{: #support}

Die Anzeige "Hilfe anfordern" enthält zwei Registerkarten. Auf der Registerkarte "Support" werden Support-Informationen bereitgestellt und auf der Registerkarte "Releaseinformationen" werden neue und geänderte Funktionen für jedes Release beschrieben.

**Abbildung 13** zeigt die erste Anzeige "Hilfe anfordern" mit Unterstützungsinformationen auf der Registerkarte "Support":

![Support](images/support_starter.png "Support")
*Abbildung 13. Blockchain-Support*

Über die Links und Ressourcen auf dieser Anzeige können Sie auf Fehlerbehebungs- und Unterstützungsforen zugreifen.

* [{{site.data.keyword.blockchainfull_notm}}-Servicedokumente](index.html) unter **Einführung** (d. h. diese Dokumentationssite) mit Anleitungen zum Einstieg in {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}}. Die entsprechenden Themen können Sie über den Navigator auf der linken Seite aufrufen oder Sie können in der Suchfunktion im oberen Bereich einen Suchbegriff eingeben.
* [IBM Developer Works ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/blockchain/) unter **Community-Hilfe** enthält Ressourcen und Informationen für Entwickler.
* [IBM dWAnswers ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/answers/smartspace/blockchain/) unter **Support-Ticket** dient als Plattform für Fragen und Antworten. Sie können nach Antworten auf bereits gestellte Fragen suchen oder eine neue Frage stellen. Achten Sie darauf, das Stichwort **blockchain** in Ihrer Frage zu verwenden.
  Sie können außerdem ein Ticket über die Option **{{site.data.keyword.Bluemix_notm}}-Support-Ticket öffnen** an das {{site.data.keyword.blockchainfull_notm}}-Support-Team senden.  Hier können Sie Details und Code-Snippets aus Ihrer jeweiligen {{site.data.keyword.Bluemix_notm}}-Instanz mit anderen teilen.
* Über [Beispielanwendungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://github.com/ibm-blockchain) unter **Blockchain-Beispielanwendungen** finden Sie Anleitungen und Beispielcodeausschnitte zur Unterstützung bei der Anwendungsentwicklung.
* Über [Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/) und [Hyperledger Fabric-Community ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://jira.hyperledger.org/secure/Dashboard.jspa) unter **Hyperledger Fabric** finden Sie nähere Details zum Hyperledger Fabric-Stack.
  Wenden Sie sich an einen [Hyperledger-Experten ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://chat.hyperledger.org/channel/general) mit Fragen zum Hyperledger Fabric-Code.

Wenn Sie Ihr Problem nicht beheben können oder keine Antwort auf Ihre Frage finden, reichen Sie einen Supportfall im IBM Cloud Service Portal ein. Weitere Informationen finden Sie unter [Support anfordern](ibmblockchain_support.html).

**Abbildung 14** und **Abbildung 15** zeigen die Eingangsanzeige für die Unterstützung. Diese enthält neue und geänderte Funktionen der jeweiligen Releases auf der Registerkarte "Releaseinformationen":

![Releaseinformationen für Helios](images/releasenotes_helios_starter.png "Releaseinformationen für Helios")
*Abbildung 14. Releaseinformationen für Helios

![Releaseinformationen für Fabric](images/releasenotes_Fabric_starter.png "Releaseinformationen für Fabric")
*Abbildung 15. Releaseinformationen für Fabric*


## Organisationen wechseln
{: #switch-organizations}

Wenn Sie ein Blockchain-Netz mit mehreren Organisationen in Ihrem eigenen Netz simulieren, können Sie zu jeder der Organisationen wechseln, deren Eigner Sie sind, zum Beispiel zu Organisation A. Anschließend können Sie die Netzressourcen von Organisation A im Network Monitor anzeigen und verwalten, wie zum Beispiel Peers, Kanäle und Chaincode. Diese Funktion bietet die Möglichkeit, einen Kanal durch Befolgung von Kanalrichtlinien zu erstellen und dem Kanal Peers aus mehreren Organisationen hinzuzufügen.

Klicken Sie auf die rechte obere Ecke der Benutzerschnittstelle, in der Ihr Name angezeigt werden müsste. Wählen Sie im Dropdown-Menü unter **Organisation wechseln** (SWITCH ORGANIZATION) den Namen der Organisation aus, zu der Sie wechseln möchten. Organisation A ist standardmäßig ausgewählt. Nach der Auswahl einer Organisation, zu der gewechselt werden soll, wird die Anzeige des Network Monitor automatisch aktualisiert, sodass das Netz aus der Perspektive der ausgewählten Organisation angezeigt wird.

**Abbildung 16** zeigt die Funktion "Netz zurücksetzen":

![Organisationen wechseln](images/switch_orgs_starter.gif "Organisationen wechseln")   
*Abbildung 16. Organisationen wechseln*


## Netzvorgaben
{: #network-preferences}

Klicken Sie auf die rechte obere Ecke, um das Dropdown-Menü zu öffnen, und dann auf die Schaltfläche **Netzvorgaben** im Menü. Das Fenster mit den Netzvorgaben wird geöffnet. Das Fenster mit den Netzvorgaben enthält die Basisinformatinen Ihres Netzes, wie z. B. den Netznamen, die Fabric-Version, die Netzadresse in {{site.data.keyword.cloud_notm}} und den Ledgerdatenbanktyp. Wenn Sie der Netzinitiator sind, können Sie auch das Web-Inaktivitätszeitlimit im Fenster mit den Netzvorgaben verwalten.

### Web-Inaktivitätszeitlimit
{: #web-inactivity-timeout}

**Hinweis**: Nur der **Netzinitiator** kann die Einstellung für das Web-Inaktivitätszeitlimit ändern. Hierbei handelt es sich um eine Einstellung auf Netzebene, die sich auf alle Netzmitglieder auswirkt.

Standardmäßig ist für das Web-Inaktivitätszeitlimit der Wert **Aus** festgelegt. Wenn Sie für das Web-Inaktivitätszeitlimit den Wert **Ein** festlegen, werden Mitglieder des Netzes nach 10 Minuten Inaktivität automatisch abgemeldet. Wenn der Web-Inaktivitätszeitgeber das Zeitlimit von 10 Minuten erreicht, beendet die Web-Inaktivitätszeitlimitfunktion die inaktiven Websitzungen, um die Sicherheit der Konten der Netzmitglieder zu gewährleisten. Wenn Sie auf einen Link klicken oder den Network Monitor aktualisieren, wird der Web-Inaktivitätszeitgeber zurückgesetzt. Vor dem Erreichen des Limits von 10 Minuten kann die Websitzung auch durch das Schließen des Browserfensters oder der Browserregisterkarte beendet werden.

**Abbildung 17** zeigt das Fenster "Netzvorgaben":

![Netzvorgaben](images/network_preferences.gif "Netzvorgaben")
*Abbildung 17. Netzvorgaben*


## Netz zurücksetzen
{: #reset-network}

Der Starter Plan bietet die Möglichkeit, die Netzkonfiguration zu bearbeiten, ohne ein Netz zu löschen und erneut zu erstellen. Ihr Netz wird auf die Erstkonfiguration des Netzes zurückgesetzt, die zwei Organisationen, einen Peer pro Organisation und einen Standardkanal umfasst. Dies ist zum Beispiel nützlich, wenn Sie mehrere Testrunden im Blockchain-Netz ausführen, da Sie immer wieder mit einem relativ sauberen Netz starten können.

**Achtung:** Wenn Sie das Netz zurücksetzen, werden die API-Endpunkte Ihrer Peers, Ihres Anordnungsknotens und der Zertifizierungsstelle (CA) geändert. Sie müssen die Informationen zu API-Endpunkten in Ihren Anwendungen anpassen.

Klicken Sie in der rechten oberen Ecke, um das Dropdown-Menü zu öffnen. Klicken Sie im Menü auf die Schaltfläche **Netz zurücksetzen**. Wenn Sie bereit sind, Ihr Netz zurückzusetzen, klicken Sie auf **OK**, um fortzufahren. Die Anzeige Ihres Network Monitor wird aktualisiert, um die neuen Einstellungen wiederzugeben.

**Abbildung 18** zeigt die Funktion "Netz zurücksetzen":

![Netz zurücksetzen](images/reset_network.png "Netz zurücksetzen")
*Abbildung 18. Netz zurücksetzen*

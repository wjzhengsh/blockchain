---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Enterprise Plan-Netz steuern
{: #getting-started-with-blockchain}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Mit dem {{site.data.keyword.blockchainfull}} Platform Enterprise Plan wird ein Blockchain-Netz mit hoher Sicherheit, Integrität, Skalierbarkeit und Leistung bereitgestellt. Sie können schnell ein voll funktionsfähiges Netz einrichten und den Network Monitor (ein GUI-Dashboard) verwenden, um sofort [Chaincode](/docs/services/blockchain/glossary.html#chaincode) und Anwendungen auszuführen, ohne ein Netz völlig neu entwerfen und konfigurieren zu müssen.
{:shortdesc}

**Hinweis:** Mit dem {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan wird eine Produktionsumgebung bereitgestellt. Wenn Sie eine Entwicklungs- und Testumgebung benötigen, lesen Sie die [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html).

Dieses Lernprogramm führt in die Voraussetzungen und die Schritte ein, die Sie ausführen müssen, um Enterprise Plan-Netz zu erhalten, das in der hoch verfügbaren und sicheren IBM Umgebung gehostet wird.  

Die folgenden Schritte beschreiben den grundlegenden Ablauf zum Starten eines Enterprise Plan-Netzes mit mehreren [Netzmitgliedern](/docs/services/blockchain/glossary.html#member):
1. Ein **Netzinitiator**, als spezieller Typ von Netzmitglied, erstellt das Netz und definiert Governance-Richtlinien. Der Netzinitiator kann anschließend andere [Organisationen](/docs/services/blockchain/glossary.html#organization) einladen, als Netzmitglieder an diesem Netz teilzunehmen.  Weitere Informationen finden Sie unter [Netz erstellen](#creating-a-network).
2. Eingeladene **Netzmitglieder** erhalten eine E-Mail-Benachrichtigung mit Anweisungen dazu, wie sie an einem {{site.data.keyword.blockchain}}-Netz teilnehmen. Außer den Anweisungen in der E-Mail-Benachrichtigung können Sie auch die unter [Am Netz teilnehmen](#joining-a-network) beschriebenen Schritte ausführen.
3. Alle **Netzmitglieder** können, wenn sie ein Netz erstellt haben oder an einem Netz teilnehmen, den Network Monitor aufrufen, um ihre Netzressourcen zu konfigurieren und zu verwalten. Sie können [Kanäle](/docs/services/blockchain/glossary.html#channel) mit einer Gruppe von Mitgliedern im Netz einrichten, um private Transaktionen für ein kanalspezifisches Ledger auszuführen, auf das nur Kanalmitglieder zugreifen können. Im Network Monitor können Sie auch Ihre eigenen Peers zum Kanal hinzufügen und auf den Peers anschließend Chaincode installieren und instanziieren. Weitere Informationen finden Sie unter [Netzressourcen und Umgebung konfigurieren](#configuring-network-resources-and-environment).
4. Nach der Entwicklung von Anwendungen richten **Anwendungsentwickler** die Interaktion zwischen ihren Anwendung und dem Netz ein. Weitere Informationen finden Sie unter [Anwendungen für die Interaktion mit dem Netz befähigen](#enabling-applications-to-interact-with-the-network).
5. **Netzoperatoren** überwachen Transaktionen auf ihren Kanälen im Network Monitor. Weitere Informationen finden Sie unter [Netzressourcen überwachen](#monitoring-network-resources).


## Netz erstellen
Bevor Sie beginnen, müssen Sie eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) unter {{site.data.keyword.Bluemix_notm}} erstellen. Sie müssen sich mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID anmelden. Wenn Sie nicht über eine ID verfügen, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**.  Benennen Sie den Service und die Berechtigungsnachweise für Ihre Instanz um, sodass Sie sie in Zukunft leichter erkennen können. Wählen Sie unter {{site.data.keyword.Bluemix_notm}} die Region, die Organisation und den Bereich für die Bereitstellung Ihres {{site.data.keyword.blockchain}}-Netzes aus. Wählen Sie dann **Enterprise-Mitgliedschaftsplan** aus der Tabelle mit den Preisstrukturplänen aus und klicken Sie auf die Schaltfläche **Erstellen**.  

Sie finden Ihre {{site.data.keyword.blockchain}} Platform-Serviceinstanz im [{{site.data.keyword.Bluemix_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}-Service-Dashboard").  

Wenn Sie ein Netzinitiator sind, klicken Sie auf die Schaltfläche **Netz erstellen**, um ein {{site.data.keyword.blockchain}}-Netz zu initiieren.  Folgen Sie den Anweisungen im Assistenten, um die grundlegende Konfiguration für Ihr Netz und Ihre Ressourcen ausführen.  
![Assistent 'Netz erstellen'](images/create_network_name.png "Assistent 'Netz erstellen'")  

1. Geben Sie in der Anzeige "Beginnen" einen Namen für Ihr Netz ein, wählen Sie die Position Ihrer {{site.data.keyword.Bluemix_notm}}-Organisation und fügen Sie den Namen Ihrer Institution hinzu. Wenn Sie andere Netzmitglieder einladen, werden diese nach dem Namen des Netzes suchen, um ihm beizutreten. Klicken Sie auf **Weiter**.
2. (Optional) Geben Sie in der Anzeige "Mitglieder einladen" den Namen der Institution und die E-Mail-Adresse des Mitglieds ein, das Sie in Ihr Netz einladen wollen. Der von Ihnen angegebene Institutionsname ist kein offizieller Titel. Er dient einfach dazu, die Institution schnell zu erkennen, und kann beim Netzbeitritt geändert werden. Beachten Sie, dass ein Netz bis zu 15 Mitglieder einschließlich Ihrer selbst enthalten kann. Dieser Schritt ist optional und Sie können im Network Monitor auch später Mitglieder in Ihr Netz einladen.  Klicken Sie auf **Weiter**.
	Die Mitglieder, die Sie einladen, erhalten eine E-Mail-Benachrichtigung über Ihre Einladung, nachdem Sie alle Schritte zur Erstellung des Netzes ausgeführt haben.
3. Richten Sie in der Anzeige "Governance-Regeln definieren" die Richtlinien für die Mitgliedschaft, die Kanalerstellung und den Chaincode ein. Standardmäßig können alle Mitglieder des Netzes andere Mitglieder einladen, dem Netz beizutreten, sie können Kanäle erstellen und Chaincode instanziieren. Gegenwärtig verwendet Ihr Netz die Standard-Governance-Richtlinien.  Klicken Sie auf **Weiter**.
4. Überprüfen Sie in der Anzeige "Zusammenfassung prüfen" Ihre Netzkonfiguration. Wenn Sie Änderungen vornehmen möchten, klicken Sie auf **Bearbeiten** neben der Abschnittsüberschrift oder klicken Sie auf die Schaltfläche **Zurück**, um zu den vorherigen Anzeigen zurückzukehren. Wenn Sie die Netzkonfiguration abgeschlossen haben, klicken Sie auf **Fertig**.  
5. In der Anzeige "Netz erstellt" werden Sie benachrichtigt, dass Ihr Netz erfolgreich erstellt worden ist. Sie können auf **[Peers](/docs/services/blockchain/glossary.html#peer) hinzufügen** klicken, um Ihre Netzressourcen zu konfigurieren, oder Sie können direkt auf **Monitor aufrufen** klicken, um den Network Monitor zu öffnen. Sie können Peers auch später im Network Monitor hinzufügen.  Weitere Informationen zu Peers finden Sie unter [Peers hinzufügen](/docs/services/blockchain/v10_dashboard.html#peers).

Sie können jetzt erfolgreich ein {{site.data.keyword.blockchain}}-Netz bereitstellen, das die folgenden Ressourcen unterstützt:  
* Eine mitgliedsspezifische Zertifizierungsstelle (CA, Certificate Authority)
* Standard-Governance-Richtlinien
* Bis zu 15 Netzmitglieder  
* Drei Anordnungsknoten und zwei CA-Zwischenknoten
* Bis zu drei kleine Peers für jedes Mitglied  
* Einen fehlertoleranten Anordnungsservice
* Bis zu 150 Kanäle
* Bis zu 10 Instanziierungen des Chaincodes pro Mitglied im Network Monitor


## Am Netz teilnehmen
Wie beim Erstellen eines Netzes müssen Sie eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) unter {{site.data.keyword.Bluemix_notm}} erstellen. Sie müssen sich mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID anmelden. Wenn Sie nicht über eine ID verfügen, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**.  Benennen Sie den Service und die Berechtigungsnachweise für Ihre Instanz um, sodass Sie sie in Zukunft leichter erkennen können. Wählen Sie unter {{site.data.keyword.Bluemix_notm}} die Region, die Organisation und den Bereich für die Bereitstellung Ihres {{site.data.keyword.blockchain}}-Netzes aus. Wählen Sie dann **Enterprise-Mitgliedschaftsplan** aus der Tabelle mit den Preisstrukturplänen aus und klicken Sie auf die Schaltfläche **Erstellen**.

Sie finden Ihre {{site.data.keyword.blockchain}} Platform-Serviceinstanz im [{{site.data.keyword.Bluemix_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}-Service-Dashboard").

Wenn Sie ein eingeladenes Netzmitglied sind, klicken Sie auf die Schaltfläche **Anstehende Einladung ->**, wählen Sie das Netz, dem Sie beitreten wollen, aus der Dropdown-Liste und klicken Sie auf die Schaltfläche **Am Netz teilnehmen!**. Folgen Sie den Anweisungen im Assistenten, um die grundlegende Konfiguration Ihres Netzes anzuzeigen und die eigenen Netzressourcen zu konfigurieren.  
![Assistent 'Am Netz teilnehmen'](images/join_network_name.png "Assistent 'Am Netz teilnehmen'")  

1. Geben Sie in der Anzeige "Beginnen" den Namen Ihrer Organisation ein und klicken Sie auf **Weiter**.
2. Prüfen Sie in der Anzeige "Governance-Regeln überprüfen" die Governance-Richtlinien für Mitgliedschaft, Kanalerstellung und Chaincode des Netzes. Klicken Sie auf **Weiter**.
3. (Optional) Wählen Sie in der Anzeige "Peers hinzufügen" die Anzahl der Peers aus, die Sie hinzufügen möchten. Klicken Sie auf **Weiter**. Jedes Mitglied in einem Netz kann bis zu drei Peers hinzufügen. Dieser Schritt ist optional und Sie können Peers auch später im Network Monitor hinzufügen. Weitere Informationen zu Peers finden Sie unter [Peers hinzufügen](/docs/services/blockchain/v10_dashboard.html#peers).
4. Überprüfen Sie in der Anzeige "Netzzusammenfassung prüfen" die Netzkonfiguration. Wenn Sie Änderungen vornehmen möchten, klicken Sie auf die Schaltfläche **Zurück**, um zu den vorherigen Anzeigen zurückzukehren. Wenn Sie die Ressourcenkonfiguration abgeschlossen haben, klicken Sie auf **Fertig**. Sie werden benachrichtigt, dass Sie dem Netz erfolgreich beigetreten sind. Anschließend können Sie auf **Monitor aufrufen** klicken, um den Network Monitor zu öffnen.

<!-- or click **Create a Channel** to initiate a channel creation request. You can create channels later in the Network Monitor. For more information, see [Channels](/docs/services/blockchain/v10_dashboard.html#channels).  -->


## Netzressourcen und Umgebung konfigurieren

1. Rufen Sie Ihren Network Monitor auf, nachdem Sie ein {{site.data.keyword.blockchain}}-Netz erstellt haben oder ihm beigetreten sind. Der Network Monitor ist ein GUI-Dashboard, in dem Sie Netzstatusinformationen verwalten und verfolgen können. Weitere Informationen finden Sie unter [Network Monitor](/docs/services/blockchain/v10_dashboard.html).
2. Fügen Sie Ihre eigenen Peers zu dem Netz hinzu. Wenn Sie bereits genügend Peers hinzugefügt haben, überspringen Sie diesen Schritt. Peers führen Chaincode aus und sie sind der Endpunkt für die Interaktion mit Ihren Anwendungen. Klicken Sie in der Anzeige "Übersicht" auf **Peers hinzufügen** und wählen Sie die Anzahl und Größe der Peers aus. Weitere Informationen finden Sie unter [Übersicht](/docs/services/blockchain/v10_dashboard.html#overview).
3. Richten Sie einen Kanal ein. Für alle Mitglieder im selben Kanal wird ein kanalspezifisches Ledger eingerichtet, das Datenisolierung und Datenschutz ermöglicht. Weitere Informationen zum Erstellen eines Kanals finden Sie unter [Kanal erstellen](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).  
    Wenn Sie ein Kanalmitglied sind, das eingeladen wurde, einem Kanal beizutreten, erhalten Sie eine E-Mail-Benachrichtigung mit einem Link zu dem Assistenten, der Ihnen ermöglicht, dem Kanal beizutreten.
4. Ordnen Sie Peers einem Kanal hinzu.  Nur Peers, die einem Kanal zugeordnet sind, können auf das zugehörige Ledger zugreifen. Weitere Informationen finden Sie unter [Kanäle](/docs/services/blockchain/v10_dashboard.html#channels).
5. Installieren und instanziieren Sie Chaincode. Alle Kanalmitglieder müssen denselben Chaincode mit demselben Namen und derselben Version auf jedem Peer installieren, auf dem der Chaincode ausgeführt werden soll. Nach der Installation des Chaincodes müssen Sie ihn auf dem Kanal instanziieren, bevor Sie ihn verwenden können. Weitere Informationen finden Sie in [Chaincode installieren, instanziieren und aktualisieren](/docs/services/blockchain/howto/install_instantiate_chaincode.html).  

**Hinweis:** Um hohe Verfügbarkeit zu erreichen, muss jede Organisation mindestens zwei Peers erwerben und in einem Kanal muss jedes teilnehmende Mitglied mindestens zwei Peers beitreten.


## Netzberechtigungsnachweise und Verbindungsprofil abrufen
Wenn Sie ein Enterprise Plan-Netz in {{site.data.keyword.cloud_notm}} erstellt haben, können Sie die Netzberechtigungsnachweise und das Verbindungsprofil auf der Serviceinstanzseite oder im Network Monitor abrufen.

### Von der Serviceinstanzseite abrufen
Sie befinden sich auf der Serviceinstanzseite gleich, nachdem Sie eine Serviceinstanz erstellt haben. Sie können auch auf Ihren Service im [{{site.data.keyword.cloud_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}-Service-Dashboard") klicken, um Ihre Serviceinstanzseite zu öffnen.

Führen Sie die folgenden Schritte aus, um Ihre Serviceberechtigungsnachweise abzurufen:
1. Klicken Sie auf der Serviceinstanzseite auf **Serviceberechtigungsnachweise** im Navigator auf der linken Seite, um die Anzeige "Serviceberechtigungsnachweise" anzuzeigen.
2. Klicken Sie auf **Neuer Berechtigungsnachweis** in der Anzeige "Serviceberechtigungsnachweise".
3. Geben Sie in der Anzeige "Neuen Berechtigungsnachweis hinzufügen" dem Berechtigungsnachweis einen Namen und geben Sie im Feld "Inline-Konfigurationsparameter hinzufügen' **{"type": "service_instance_token"}** ein. Klicken Sie auf **Hinzufügen**. Der neue Berechtigungsnachweis wird in der Tabelle hinzugefügt. Sie können auf **Berechtigungsnachweise anzeigen** in der Spalte "AKTIONEN" klicken, um die Berechtigungsnachweisdetails anzuzeigen. Dieser Berechtigungsnachweis enthält den API-Schlüssel und den geheimen Schlüssel ('secret'), mit denen Sie APIs berechtigen können.

### Im Network Monitor abrufen
Sie finden die Netzberechtigungsnachweise in der Anzeige "APIs" in Ihrem Network Monitor. Weitere Informationen zur Verwendung von APIs finden Sie unter [APIs mit Swagger ausprobieren](/docs/services/blockchain/howto/swagger_apis.html).

Sie können das Verbindungsprofil in der Anzeige "Übersicht" im Network Monitor abrufen. Klicken Sie auf die Schaltfläche **Verbindungsprofil** in der Anzeige "Übersicht". Das Verbindungsprofil wird auf einer neuen Seite angezeigt.


## Angepasste Unternehmensnetze entwickeln und bereitstellen
Sie können Unternehmensnetze auf Basis Ihrer Geschäftsanforderungen in der {{site.data.keyword.blockchainfull_notm}} Platform: Develop-Entwicklerumgebung und mithilfe des Hyperledger Composer-Entwicklertoolsets entwickeln. Wenn Sie ein Netz für Ihr Unternehmen entwickelt haben, können Sie Ihr Unternehmensnetz im Enterprise Plan-Netz bereitstellen.

Weitere Informationen finden Sie unter [Netz entwickeln](/docs/services/blockchain/develop.html) und [Unternehmensnetz im Enterprise Plan bereitstellen](/docs/services/blockchain/develop_enterprise.html).


## Anwendungen für die Interaktion mit dem Netz befähigen
Anwendungen nutzen die SDK-APIs, um mit Ihren {{site.data.keyword.blockchain}}-Netzressourcen zu interagieren. Sie müssen Ihrer Anwendung die API-Endpunktinformationen Ihrer Netzressourcen hinzufügen, sodass die Anwendung schließlich Transaktionsanforderungen an Ihre Peers richten kann. Anschließend können Sie die API-Endpunktinformationen aus dem Network Monitor hinzufügen. Anwendungen können von Ihrem lokalen Dateisystem oder von {{site.data.keyword.Bluemix_notm}} gehostet werden. Weitere Informationen finden Sie unter [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html).

## Netzressourcen überwachen  
Wenn eine Transaktion von Ihrer Anwendung ausgelöst wurde, können Sie Transaktionsstatusinformationen im Network Monitor anzeigen. Weitere Informationen zur Netzüberwachung finden Sie unter [Netz überwachen](/docs/services/blockchain/howto/monitor_network.html).

## Netz verlassen
Wenn Sie ein Netz verlassen möchten, löschen Sie die Blockchain-Serviceinstanz aus Ihrem {{site.data.keyword.Bluemix_notm}}-Dashboard.  

**Hinweis**: Bevor Sie ein Netz verlassen, müssen Sie sicherstellen, dass Sie kein Mitglied eines Kanals des Netzes sind. Andernfalls treten Fehler auf, wenn Sie das Netz verlassen. Für das Entfernen eines Kanalmitglieds ist das Ausführen des Kanalaktualisierungsprozesses erforderlich. Weitere Informationen zum Kanalaktualisierungsprozess finden Sie in [Kanal aktualisieren](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](/docs/services/blockchain/index.html).
* For more information about Hyperledger Fabric, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/){:new_window}.
-->

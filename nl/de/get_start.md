---

copyright:
  years: 2017
lastupdated: "2017-09-04"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Am Netz teilnehmen
{: #getting-started-with-blockchain}

Die {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}} bietet ein {{site.data.keyword.blockchain}}-Netz mit hoher Sicherheit, Integrität, Skalierbarkeit und Leistung. Sie können in kürzester Zeit ein voll funktionsfähiges {{site.data.keyword.blockchain}}-Netz einrichten und den Netzmonitor verwenden, um sofort Chaincode und Anwendungen auszuführen, ohne ein privates Blockchain-Netz entwerfen und konfigurieren zu müssen.
{:shortdesc}
 
Dieses Lernprogramm zur Einführung beschreibt die Voraussetzungen und die Schritte, die Sie ausführen müssen, um über ein {{site.data.keyword.blockchain}}-Netz zu verfügen, das in der hoch verfügbaren und sicheren IBM Umgebung gehostet wird.  

Die folgenden Schritte zeigen den grundlegenden Ablauf bei der Inbetriebnahme eines {{site.data.keyword.blockchain}}-Netzes mit mehreren Mitgliedorganisationen:
1. Ein **Netzinitiator**, als eine spezielle Art von Netzmitglied, erstellt das {{site.data.keyword.blockchain}}-Netz und definiert Governance-Richtlinien. 
Der Netzinitiator kann anschließend andere Institutionen einladen, diesem {{site.data.keyword.blockchain}}-Netz als Mitglieder beizutreten. Weitere Informationen finden Sie unter [Netz erstellen](#creating-a-network).
2. **Netzmitglieder** erhalten eine E-Mail-Benachrichtigung mit Hinweisen dazu, wie sie einem {{site.data.keyword.blockchain}}-Netz beitreten können. Außer den Anweisungen in der E-Mail-Benachrichtigung können Sie auch die unter [Am Netz teilnehmen](#joining-a-network) beschriebenen Schritte ausführen. 
3. Alle **Netzmitglieder** können - nachdem sie ein Netz erstellt haben oder einem Netz beigetreten sind - den Netzmonitor (ein GUI-Dashboard) aufrufen, um ihre Netzressourcen zu konfigurieren und zu verwalten. Sie können Kanäle mit einer Gruppe von Mitgliedern im Netz einrichten, um private Transaktionen für ein kanalspezifisches Hauptbuch auszuführen, auf das nur Kanalmitglieder zugreifen können. Im Netzmonitor können Sie auch Ihre eigenen Peers zum Kanal hinzufügen und auf den Peers Chaincode installieren und instanziieren. Weitere Informationen finden Sie unter [Netzressourcen und Umgebung konfigurieren](#configuring-network-resources-and-environment).
4. **Anwendungsentwickler** erstellen Anwendungen, die die Interaktion mit dem {{site.data.keyword.blockchain}}-Netz ermöglichen. Weitere Informationen finden Sie unter [Anwendungen für die Interaktion mit dem Netz befähigen](#enabling-applications-to-interact-with-the-network).
5. **Netzoperatoren** überwachen die Transaktionen auf ihren Kanälen mithilfe des Netzmonitors. Weitere Informationen finden Sie unter [Netzressourcen überwachen](#monitoring-network-resources).

## Netz erstellen
Bevor Sie beginnen, müssen Sie eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) unter {{site.data.keyword.Bluemix_notm}} erstellen. Sie müssen sich mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID anmelden. Wenn Sie nicht über eine ID verfügen, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**. Benennen Sie den Service und die Berechtigungsnachweise für Ihre Instanz um, sodass Sie sie in Zukunft leichter erkennen können. Wählen Sie unter {{site.data.keyword.Bluemix_notm}} die Region, die Organisation und den Bereich für die Bereitstellung Ihres {{site.data.keyword.blockchain}}-Netzes aus. Wählen Sie dann **Enterprise Plan** aus der Tabelle mit der Preisstruktur aus und klicken Sie auf die Schaltfläche **Erstellen**.  

Sie finden Ihre {{site.data.keyword.blockchain}} Platform-Serviceinstanz in Ihrem [{{site.data.keyword.Bluemix_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "Bluemix-Service-Dashboard").  

Wenn Sie ein Netzinitiator sind, klicken Sie auf die Schaltfläche **Netz erstellen**, um ein {{site.data.keyword.blockchain}}-Netz zu initiieren. Folgen Sie den Anweisungen im Assistenten, um die grundlegende Konfiguration für Ihr Netz und Ihre Ressourcen ausführen.  
![Assistent 'Netz erstellen'](images/create_network_name.png "Assistent 'Netz erstellen'")  

1. Geben Sie auf der Anzeige "Beginnen" einen Namen für Ihr Netz ein, wählen Sie die Position Ihrer {{site.data.keyword.Bluemix_notm}}-Organisation und fügen Sie den Namen Ihrer Institution hinzu. Wenn Sie andere Netzmitglieder einladen, werden diese nach dem Namen des Netzes suchen, um ihm beizutreten. Klicken Sie auf **Weiter**.
2. (Optional) Geben Sie in der Anzeige "Mitglieder einladen" den Namen der Institution und die E-Mail-Adresse des Mitglieds ein, das Sie zu Ihrem Netz einladen wollen. Der von Ihnen angegebenen Institutionsname ist kein offizieller Titel. Er dient einfach dazu, die Institution schnell zu erkennen, und kann beim Netzbeitritt geändert werden. Beachten Sie, dass ein Netz bis zu 15 Mitglieder einschließlich Ihrer selbst enthalten kann. Dieser Schritt ist optional und Sie können im Netzmonitor auch später Mitglieder zu Ihrem Netz einladen. Klicken Sie auf **Weiter**.
Die Mitglieder, die Sie einladen, erhalten eine E-Mail-Benachrichtigung über Ihre Einladung, nachdem Sie alle Schritte zur Erstellung des Netzes ausgeführt haben.
3. Richten Sie in der Anzeige "Governance-Regeln definieren" die Richtlinien für die Mitgliedschaft, die Kanalerstellung und den Chaincode ein. Standardmäßig können alle Mitglieder des Netzes andere Mitglieder einladen, dem Netz beizutreten, sie können Kanäle erstellen und Chaincode instanziieren. Für dieses GA verwendet Ihr Netz die Standard-Governance-Richtlinien. Klicken Sie auf **Weiter**.
4. Überprüfen Sie in der Anzeige "Zusammenfassung prüfen" Ihre Netzkonfiguration. Wenn Sie Änderungen vornehmen möchten, klicken Sie auf **Bearbeiten** neben der Abschnittsüberschrift oder klicken Sie auf die Schaltfläche **Zurück**, um zu den vorherigen Anzeigen zurückzukehren. Wenn Sie die Netzkonfiguration abgeschlossen haben, klicken Sie auf **Fertig**.  
5. In der Anzeige "Netz erstellt" werden Sie benachrichtigt, dass Ihr Netz erfolgreich erstellt worden ist. Klicken Sie auf **Peers hinzufügen**, um Ihre Netzressourcen zu konfigurieren, oder klicken Sie direkt auf **Monitor aufrufen**, um den Netzmonitor zu öffnen. Wenn Sie zu Beginn keine Peers hinzufügen, können Sie dies später im Netzmonitor tun. Weitere Informationen zu Peers finden Sie unter [Übersicht](v10_dashboard.html#overview).
    
Sie können jetzt ein {{site.data.keyword.blockchain}}-Netz bereitstellen, das die folgenden Ressourcen unterstützen kann:  
* Eine mitgliedsspezifische Zertifizierungsstelle (CA, Certificate Authority)
* Standard-Governance-Richtlinien
* Bis zu 15 Netzmitglieder  
* Drei Anordnungsknoten und zwei CA-Zwischenknoten
* Bis zu sechs kleine Peers für jedes Mitglied  
* Einen fehlertoleranten Anordnungsservice
* Bis zu 150 Kanäle
* Bis zu 10 Instanziierungen von Chaincodes


## Am Netz teilnehmen
Wie beim Erstellen eines Netzes müssen Sie eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) unter {{site.data.keyword.Bluemix_notm}} erstellen. Sie müssen sich mit Ihrer {{site.data.keyword.Bluemix_notm}}-ID anmelden. Wenn Sie nicht über eine ID verfügen, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**. Benennen Sie den Service und die Berechtigungsnachweise für Ihre Instanz um, sodass Sie sie in Zukunft leichter erkennen können. Wählen Sie unter {{site.data.keyword.Bluemix_notm}} die Region, die Organisation und den Bereich für die Bereitstellung Ihres {{site.data.keyword.blockchain}}-Netzes aus. Wählen Sie dann **Enterprise Plan** aus der Tabelle mit der Preisstruktur aus und klicken Sie auf die Schaltfläche **Erstellen**.  

Sie finden Ihre {{site.data.keyword.blockchain}} Platform-Serviceinstanz im [{{site.data.keyword.Bluemix_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "Bluemix-Service-Dashboard"). 

Wenn Sie ein eingeladenes Netzmitglied sind, klicken Sie auf die Schaltfläche **Anstehende Einladung ->**, wählen Sie das Netz, dem Sie beitreten wollen, aus der Dropdown-Liste aus und klicken Sie auf die Schaltfläche **Am Netz teilnehmen!**. Folgen Sie den Anweisungen im Assistenten, um die grundlegende Konfiguration Ihres Netzes anzuzeigen und um Ihre eigenen Netzkomponenten zu konfigurieren.  
![Assistent 'Am Netz teilnehmen'](images/join_network_name.png "Assistent 'Am Netz teilnehmen'")  

1. Fügen Sie in der Anzeige "Beginnen" den Namen Ihrer Institution hinzu und überprüfen Sie die Position Ihrer {{site.data.keyword.Bluemix_notm}}-Organisation. Klicken Sie auf **Weiter**.
2. Zeigen Sie in der Anzeige "Governance-Regeln überprüfen" die Governance-Richtlinien des Netzes für die Mitgliedschaft, die Kanalerstellung und den Chaincode an. Klicken Sie auf **Weiter**.
3. (Optional) Wählen Sie in der Anzeige "Peers hinzufügen" die Größe und Anzahl der Peers aus, die Sie hinzufügen möchten. Klicken Sie auf **Weiter**. Jedes Mitglied in einem Netz kann bis zu drei Peers hinzufügen; derzeit sind nur "kleine" Peers verfügbar. Dieser Schritt ist optional und Sie können im Netzmonitor auch später Peers hinzufügen. Weitere Informationen zu Peers finden Sie unter [Übersicht](v10_dashboard.html#overview).
4. Überprüfen Sie in der Anzeige "Netzzusammenfassung prüfen" die Netzkonfiguration. Wenn Sie Änderungen vornehmen möchten, klicken Sie auf **Bearbeiten** neben der Abschnittsüberschrift oder klicken Sie auf die Schaltfläche **Zurück**, um zu den vorherigen Anzeigen zurückzukehren. Wenn Sie die Ressourcenkonfiguration abgeschlossen haben, klicken Sie auf **Fertig**. Sie werden benachrichtigt, dass Sie dem Netz erfolgreich beigetreten sind. Anschließend können Sie auf **Monitor aufrufen** klicken, um den Netzmonitor zu öffnen, oder Sie können auf **Kanal erstellen** klicken, um eine Anforderung zur Kanalerstellung zu initiieren. Sie können Kanäle auch später im Netzmonitor erstellen. Weitere Informationen finden Sie unter [Kanäle](v10_dashboard.html#channels).
 

## Netzressourcen und Umgebung konfigurieren

1. Rufen Sie Ihren Netzmonitor auf, nachdem Sie ein {{site.data.keyword.blockchain}}-Netz erstellt haben oder ihm beigetreten sind. Der Netzmonitor ist ein GUI-Dashboard, in dem Sie Netzstatusinformationen verwalten und verfolgen können. Weitere Informationen finden Sie unter [Netzmonitor](v10_dashboard.html).
2. Fügen Sie Ihre eigenen Peers zu dem Netz hinzu. Wenn Sie bereits genügend Peers hinzugefügt haben, überspringen Sie diesen Schritt. Peers führen Chaincode aus und sie sind der Endpunkt für die Interaktion mit Ihren Anwendungen. Klicken Sie in der Anzeige "Übersicht" auf **Peers hinzufügen** und wählen Sie die Anzahl und Größe der Peers aus. Weitere Informationen finden Sie unter [Übersicht](v10_dashboard.html#resources).
3. Richten Sie einen Kanal ein. Für alle Mitglieder im selben Kanal wird ein kanalspezifisches Hauptbuch eingerichtet, das Datenisolierung und Datenschutz ermöglicht. Weitere Informationen zum Erstellen eines Kanals finden Sie unter [Kanal erstellen](howto/create_channel.html#creating-a-channel).  
	
	Wenn Sie ein Kanalmitglied sind, das eingeladen wurde, einem Kanal beizutreten, erhalten Sie eine E-Mail-Benachrichtigung mit einem Link zu dem Assistenten, der Ihnen ermöglicht, dem Kanal beizutreten.
4. Ordnen Sie Peers einem Kanal hinzu. Nur Peers, die einem Kanal zugeordnet sind, können auf das zugehörige Hauptbuch zugreifen. Weitere Informationen finden Sie unter [Kanäle](v10_dashboard.html#channels).
5. Installieren und instanziieren Sie Chaincode. Alle Kanalmitglieder müssen denselben Chaincode mit demselben Namen und derselben Version auf jedem Peer installieren, auf dem der Chaincode ausgeführt werden soll. Nach der Installation muss der Chaincode auf dem Kanal instanziiert werden, bevor er verwendet werden kann. Weitere Informationen finden Sie unter [Chaincode installieren und instanziieren](howto/install_instantiate_chaincode.html).  

**Hinweis:** Um hohe Verfügbarkeit zu erreichen, muss jede Institution mindestens zwei Peers erwerben und in einem Kanal muss jede teilnehmende Institution mindestens zwei Peers beitreten. 

## Anwendungen für die Interaktion mit dem Netz befähigen
Anwendungen nutzen die SDK-APIs, um mit Ihren {{site.data.keyword.blockchain}}-Netzkomponenten zu interagieren. Sie müssen die API-Endpunktinformationen Ihrer Netzkomponenten zu Ihrer Anwendung hinzufügen, sodass die Anwendung schließlich Transaktionsanforderungen an Ihre Peers richten kann. Anschließend können Sie die API-Endpunktinformationen aus dem Netzmonitor hinzufügen. Anwendungen können von Ihrem lokalen Dateisystem oder von {{site.data.keyword.Bluemix_notm}} gehostet werden. Weitere Informationen finden Sie unter [Anwendungen entwickeln](v10_application.html).

## Netzressourcen überwachen  
Nachdem eine Transaktion von Ihrer Anwendung ausgelöst wurde, können Sie Transaktionsstatusinformationen im Netzmonitor anzeigen. Weitere Informationen zur Netzüberwachung finden Sie unter [Netz überwachen](howto/monitor_network.html).

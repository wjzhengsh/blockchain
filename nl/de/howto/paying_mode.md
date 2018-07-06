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

# Zahlungsmodus

Für {{site.data.keyword.blockchainfull}} Platform werden monatliche Mitgliedsgebühren und Peer-Gebühren in Rechnung gestellt. Netzmitglieder können ihre Rechnung über ihre IBM Cloud-Konten bezahlen, die den Bereich zum Erstellen der Netzinstanz enthalten. Alternativ kann ein Netzmitglied die Rechnung für alle Mitglieder im Netz übernehmen und für das gesamte Netz bezahlen.
{:shortdesc}

Ihre Kosten sind je nach ausgewähltem Netzplan und Größe der genutzten Ressourcen unterschiedlich. Weitere Informationen zur Preisstruktur finden Sie unter [Preisstruktur](pricing.html). 

## Voraussetzungen
Jedes Netzmitglied muss über ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto, z. B. ein **nutzungsabhängiges** Konto, verfügen. Wenn Sie über kein Konto verfügen, [registrieren](https://console.bluemix.net/registration/) Sie sich für ein Konto und führen Sie ein Upgrade auf ein gebührenpflichtiges Konto durch. Für ein Upgrade Ihres Kontos auf ein nutzungsabhängiges Konto rufen Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der IBM Cloud-Konsole auf und klicken Sie auf **Kreditkarte hinzufügen**- 


## Eigene Gebühren bezahlen
Nach dem Erstellen von Blockchain-Netzen mit {{site.data.keyword.blockchainfull_notm}} Platform werden Ihre {{site.data.keyword.cloud_notm}}-Konten täglich mit Ihrer Mitgliedsgebühr und Ihren Peer-Gebühren belastet.  


## Für das gesamte Netz bezahlen
Ein Mitglied im Netz kann die Gebühren für alle Netzmitglieder übernehmen.  Zur Bezahlung in diesem Einzelzahlermodus müssen der Zahler und die anderen Netzmitglieder die folgenden Schritte ausführen.

1. Der Zahler erstellt separate Cloud Foundry-Bereiche in {{site.data.keyword.cloud_notm}} für jeden Benutzer, der in das Netz eingeladen werden soll:
   1. Melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
   2. Klicken Sie in der Menüleiste auf **Verwalten** > **Konto** > **Cloud Foundry-Organisationen**.
   3. Klicken Sie auf **Details anzeigen** der Cloud Foundry-Organisation, mit der Sie das Netz erstellen.  Wenn Sie keine Organisation für das Netz haben, klicken Sie auf die Schaltfläche **Neue Cloud Foundry-Organisation hinzufügen**, um eine zu erstellen.
   4. Klicken Sie auf die Schaltfläche **Cloud Foundry-Bereich hinzufügen**, um einen Bereich für ein Netzmitglied zu erstellen.  Wählen Sie die Region für den Bereich aus, benennen Sie ihn und klicken Sie auf **Hinzufügen**.  Beachten Sie, dass nur der Bereichsersteller auf den erstellten Bereich zugreifen kann.
   5. Wiederholen Sie Schritt 4, um separate Bereiche für jeden Benutzer zu erstellen, der in das Netz eingeladen werden soll.
2. Der Zahler lädt andere Benutzer in das {{site.data.keyword.cloud_notm}}-Konto des Zahlers ein und gibt Ihnen Zugriff auf den bestimmten Bereich.  Jeder Benutzer hat nur Zugriff auf einen Bereich.
   1. Klicken Sie in der Menüleiste auf **Verwalten** > **Konto** > **Benutzer**.  
   2. Klicken Sie auf **Benutzer einladen** und erteilen Sie den Benutzerzugriff:
      1. Geben Sie die E-Mail-Adresse eines einzelnen Benutzers ein, den Sie in Ihre Cloud Foundry-Organisation einladen wollen.
      2. Geben Sie im Abschnitt **Cloud Foundry-Zugriff** die folgenden Optionen an:
         - **Organisation:** Die Cloud Foundry-Organisation, für die das Netz erstellt wird.
         - **Organisationsrollen:** Auditor.
         - **Region:** Die Region, in der Sie den Bereich für diesen Benutzer erstellt haben.
         - **Bereich:** Der Bereich, den Sie für diesen Benutzer erstellt haben.
         - **Bereichsrollen:** Entwickler.
      3. Klicken Sie auf **Benutzer einladen**.
   3. Wiederholen Sie die Schritte, um Benutzer einzuladen und jedem Benutzer Benutzerzugriff zu erteilen.
3. Der Zahler lädt die Benutzer im {{site.data.keyword.blockchainfull_notm}} Platform-Dashboard in das Blockchain-Netz ein. Weitere Informationen zur Einladung von Netzmitgliedern finden Sie unter [Mitglieder](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Jeder Benutzer empfängt eine Benachrichtigungs-E-Mail, die eine Einladung zur Teilnahme am Netz enthält.  Der Benutzer kann die folgenden Schritte ausführen, um dem Netz beizutreten.
   1. Klicken Sie auf die Schaltfläche "Fortfahren" in der Benachrichtigungs-E-Mail, sodass Sie zur Blockchain-Serviceseite in {{site.data.keyword.cloud_notm}} gelangen.
   2. Achten Sie darauf, die richtige {{site.data.keyword.cloud_notm}}-Organisation und den richtigen Bereich zu verwenden.
      1. Melden Sie sich bei {{site.data.keyword.cloud_notm}} an und klicken Sie auf den Profilavatar in der rechten oberen Ecke.
      2. Wählen Sie das Konto des Zahlers in der Dropdown-Liste **Konto** aus.  Beachten Sie, dass dem Konto und der Organisation, unter denen Sie die Blockchain-Serviceinstanz bereitstellen, die Rechnung gestellt wird.  
   4. Erstellen Sie auf der Blockchain-Serviceseite Ihre Blockchain-Serviceinstanz:
      1. Geben Sie in das Feld **Servicename** einen beschreibenden Namen zur künftigen Erkennung ein.
      2. Vergewissern Sie sich, dass die Organisation und der Bereich diejenigen sind, in die Sie der Zahler eingeladen hat.
      3. Wählen Sie den Mitgliedschaftsplan aus, mit dem Ihre Netzinstanz erstellt werden soll.
      4. Klicken Sie auf **Erstellen**.
   5. Befolgen Sie nach der Erstellung der Blockchain-Serviceinstanz die Anweisungen des Assistenten, um dem Netz beizutreten.  Weitere Informationen finden Sie unter [Am Netz teilnehmen](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Bekannte Einschränkungen
- Da sich alle Mitglieder innerhalb des {{site.data.keyword.cloud_notm}}-Kontos des Zahlers befinden, hat der Zahler Zugriff auf die Blockchain-Instanzen aller Mitglieder und kann deren Identität annehmen.  Daher wird dieser Zahlungsmodus am besten für POC-Umgebungen verwendet oder in solchen Fällen verwendet, in denen der Zahlungspflichtige das gesamte Management der {{site.data.keyword.blockchainfull_notm}} Platform abwickelt und den Mitgliedern eine Anwendung nur bereitgestellt wird.  
- Wenn der Zahler alle Mitglieder zum {{site.data.keyword.cloud_notm}}-Konto des Zahlers hinzugefügt und ihnen Zugriff zum Bereitstellen von Blockchain-Instanzen erteilt hat und die Mitglieder dem Netz beigetreten sind, erteilt der Zahler den Mitgliedern auch Zugriff zum Erstellen weiterer Services, die zusätzliche Gebühren verursachen können.  
- Für alle Mitglieder in der Cloud Foundry-Organisation des Zahlers sind alle Bereiche in der Organisation sichtbar.  Allerdings können die Mitglieder die Organisationen nicht bearbeiten oder ändern, da sie nicht die entsprechenden Berechtigungen haben.

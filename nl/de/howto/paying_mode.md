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

# Zahlungsmodus
{: #paying-mode}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Für {{site.data.keyword.blockchainfull}} Platform werden monatliche Mitgliedsgebühren und Peer-Gebühren in Rechnung gestellt. Netzmitglieder können ihre Rechnung über ihre {{site.data.keyword.cloud_notm}}-Konten bezahlen, die den Bereich zum Erstellen der Netzinstanz enthalten. Alternativ hierzu kann ein Netzmitglied die Rechnung für andere (oder auch alle) Mitglieder im Netz übernehmen und für das gesamte Netz bezahlen. Der Zahler kann die Abrechnungen bei Bedarf auch an andere Mitglieder zurückübertragen.
{:shortdesc}

Ihre Kosten sind je nach ausgewähltem Netzplan und Größe der genutzten Ressourcen unterschiedlich. Weitere Informationen zur Preisstruktur finden Sie unter [Preisstruktur](pricing.html).

## Voraussetzungen
{: #prereq}

Jedes Netzmitglied muss über ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto verfügen, z. B. ein **nutzungsabhängiges** Konto. Dadurch ist es möglich, {{site.data.keyword.blockchainfull_notm}} Platform-Serviceinstanzen unter {{site.data.keyword.cloud_notm}} zu erstellen. Wenn Sie über kein Konto verfügen, [registrieren](https://console.bluemix.net/registration/) Sie sich für ein Konto und führen Sie ein Upgrade auf ein gebührenpflichtiges Konto durch. Für ein Upgrade Ihres Kontos auf ein nutzungsabhängiges Konto rufen Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der {{site.data.keyword.cloud_notm}}-Konsole auf und klicken Sie auf **Kreditkarte hinzufügen**.


## Eigene Gebühren bezahlen
{: #pay-your-own}

Nachdem Sie Blockchain-Netze erstellt oder einem Blockchain-Netz unter {{site.data.keyword.blockchainfull_notm}} Platform beigetreten sind, werden Ihre {{site.data.keyword.cloud_notm}}-Konten mit Ihrer Mitgliedschaftsgebühr und den Peergebühren belastet.


## Für andere Netzmitglieder bezahlen
{: #pay-for-others}

Ein Mitglied innerhalb eines Netzes kann die Gebühren für einzelne oder mehrere andere Netzmitglieder im selben Blockchain-Netz übernehmen. Zur Bezahlung in diesem Modus müssen der Zahler und andere Netzmitglieder die folgenden Schritte ausführen.

1. Der Zahler erstellt separate Cloud Foundry-Bereiche in {{site.data.keyword.cloud_notm}} für die Mitglieder, für die der Zahler bezahlen möchte.
   1. Melden Sie sich bei {{site.data.keyword.cloud_notm}} an.
   2. Klicken Sie in der Menüleiste auf **Verwalten** > **Konto** > **Cloud Foundry-Organisationen**.
   3. Klicken Sie auf **Details anzeigen** der Cloud Foundry-Organisation, mit der Sie das Netz erstellen. Wenn Sie keine Organisation für das Netz haben, klicken Sie auf die Schaltfläche **Neue Cloud Foundry-Organisation hinzufügen**, um eine zu erstellen.
   4. Klicken Sie auf die Schaltfläche **Cloud Foundry-Bereich hinzufügen**, um einen Bereich für ein weiteres Netzmitglied zu erstellen. Wählen Sie die Region für den Bereich aus, benennen Sie ihn und klicken Sie auf **Hinzufügen**.  Beachten Sie, dass nur der Bereichsersteller auf den erstellten Bereich zugreifen kann.  
   **Hinweis**: Wenn der Zahler für mehrere Mitglieder bezahlt, dann muss er diesen Schritt wiederholen, um separate Bereiche für jedes der Mitglieder zu erstellen, für das der Zahler bezahlen möchte.
2. Der Zahler lädt andere Mitglieder in das {{site.data.keyword.cloud_notm}}-Konto des Zahlers ein und erteilt ihnen Zugriff auf ihre speziellen Bereiche. Vergewissern Sie sich, dass jedes Mitglied nur auf einen Bereich zugreifen kann.
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
   **Hinweis**: Wenn der Zahler für mehrere Mitglieder bezahlt, dann muss er diesen Schritt wiederholen, um weitere Mitglieder einzuladen und diesen Mitgliedern Zugriff zu erteilen.
3. Der Zahler erstellt ein Blockchain-Netz unter {{site.data.keyword.blockchainfull_notm}} Platform und lädt andere Mitglieder in das Blockchain-Netz in der Network Monitor-Instanz des Netzes ein. Weitere Informationen zur Einladung von Netzmitgliedern finden Sie unter [Mitglieder](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Jedes Mitglied empfängt eine Benachrichtigungs-E-Mail, die eine Einladung zur Teilnahme am Netz enthält. Das Mitglied kann die folgenden Schritte ausführen, um dem Netz beizutreten.
   1. Klicken Sie auf die Schaltfläche "Fortfahren" in der Benachrichtigungs-E-Mail, sodass Sie zur Blockchain-Serviceseite in {{site.data.keyword.cloud_notm}} gelangen.
   2. Melden Sie sich bei {{site.data.keyword.cloud_notm}} an und vergewissern Sie sich, dass Sie die korrekte {{site.data.keyword.cloud_notm}}-Organisation und den korrekten Bereich verwenden, zu denen Sie der Zahler hinzugefügt hat.
      1. Klicken Sie in der {{site.data.keyword.cloud_notm}}-Konsole auf den Profilavatar in der oberen rechten Ecke.
      2. Wählen Sie das Konto des Zahlers in der Dropdown-Liste **Konto** aus.  Beachten Sie, dass dem Konto und der Organisation, unter denen Sie die Blockchain-Serviceinstanz bereitstellen, die Rechnung gestellt wird.  
   3. Suchen Sie im {{site.data.keyword.cloud_notm}}-Katalog nach der **Blockchain** und erstellen Sie eine Serviceinstanz.
      1. Geben Sie in das Feld **Servicename** einen beschreibenden Namen zur künftigen Erkennung ein.
      2. Vergewissern Sie sich, dass die Organisation und der Bereich mit der Organisation und dem Bereich übereinstimmen, zu denen Sie der Zahler hinzugefügt hat.
      3. Wählen Sie den Mitgliedschaftsplan aus, mit dem Ihre Netzinstanz erstellt werden soll. Ihr Mitgliedschaftsplan muss mit dem Mitgliedschaftsplan identisch sein, mit dem der Zahler das Netz erstellt hat.
      4. Klicken Sie auf **Erstellen**.
   4. Befolgen Sie nach der Erstellung der Blockchain-Serviceinstanz die Anweisungen des Assistenten, um dem Netz beizutreten.  Weitere Informationen finden Sie unter [Am Netz teilnehmen](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Bekannte Einschränkungen
{: #pay-for-others-limitation}
- Da sich alle Mitglieder innerhalb des {{site.data.keyword.cloud_notm}}-Kontos des Zahlers befinden, hat der Zahler Zugriff auf die Blockchain-Instanzen aller Mitglieder und kann deren Identität annehmen. Daher wird dieser Zahlungsmodus am besten für PoC-Umgebungen (PoC = Proof of Concept; Machbarkeitsnachweis) oder in Fällen verwendet, in denen der Zahlungspflichtige das gesamte Management für {{site.data.keyword.blockchainfull_notm}} Platform abwickelt und den Mitgliedern lediglich eine Anwendung bereitgestellt wird.  
- Für alle Mitglieder in der Cloud Foundry-Organisation des Zahlers sind alle Bereiche in der Organisation sichtbar.  Allerdings können die Mitglieder die Organisationen nicht bearbeiten oder ändern, da sie nicht die entsprechenden Berechtigungen haben.  
- Wenn der Zahler alle Mitglieder zum {{site.data.keyword.cloud_notm}}-Konto des Zahlers hinzugefügt und ihnen Zugriff zum Bereitstellen von Blockchain-Instanzen erteilt hat und die Mitglieder dem Netz beigetreten sind, erteilt der Zahler den Mitgliedern auch Zugriff zum Erstellen weiterer Services, die zusätzliche Gebühren verursachen können. Alle Mitglieder können eine Vereinbarung unterzeichnen, in der festgehalten wird, dass diese Mitglieder keine nicht autorisierten Services in ihren Konten erstellen werden und so zusätzliche Kosten für das Konto des Zahlers verursachen.  

## Mitglied bezahlen lassen
{: #let-other-pay}

Eine Alternative zum Hinzufügen weiterer Mitgliederkonten zum Konto des Zahlers stellt die Vorgehensweise dar, bei der weitere Mitglieder das Konto des Zahlers auch als **Abrechnungsmanager** zum eigenen Konto hinzufügen. Zur Bezahlung in diesem Modus müssen der Zahler und andere Netzmitglieder die folgenden Schritte ausführen.

1. Der Zahler erstellt ein Blockchain-Netz unter {{site.data.keyword.blockchainfull_notm}} Platform über das Konto des Zahlers.
2. Andere Mitglieder fügen das Konto des Zahlers zu ihrem {{site.data.keyword.cloud_notm}}-Konto als **Abrechnungsmanager** hinzu.
  1. Klicken Sie in der Menüleiste oben in der {{site.data.keyword.cloud_notm}}-Konsole auf **Verwalten** > **Konto** > **Benutzer**.
  2. Klicken Sie auf der Seite **Benutzer** auf die Schaltfläche **Benutzer einladen**.
  3. Fügen Sie das Konto des Zahlers unter dem Feld **E-Mail-Adresse** hinzu.
  4. Wählen Sie im Abschnitt **Cloud Foundry-Zugriff** in der Dropdown-Liste Ihre Organisation aus, zu der das Konto des Zahlers hinzugefügt werden soll.
  5. Wählen Sie als **Organisationsrolle** die Option **Abrechnungsmanager** aus.
  6. Konfigurieren Sie die weiteren Felder entsprechend und klicken Sie dann auf **Benutzer einladen**.  
3. Der Zahler lädt weitere Mitglieder zum Blockchain-Netz in der Network Monitor-Instanz des Netzes ein. Weitere Informationen zur Einladung von Netzmitgliedern finden Sie unter [Mitglieder](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Jedes Mitglied empfängt eine Benachrichtigungs-E-Mail, die eine Einladung zur Teilnahme am Netz enthält. Das Mitglied kann dem Netz nun beitreten. Weitere Informationen finden Sie in **Schritt 4** unter [Für andere Netzmitglieder bezahlen](#pay-for-others).

### Bekannte Einschränkung
{: #let-other-pay-limitation}

Nachdem das {{site.data.keyword.cloud_notm}}-Konto des Zahlers als Abrechnungsmanager zu den Konten anderer Mitglieder hinzugefügt wurde und diese Mitglieder weitere Services über ihr Konto erworben haben, muss der Zahler die entsprechenden Gebühren ebenfalls bezahlen. Alle Mitglieder können eine Vereinbarung unterzeichnen, in der festgehalten wird, dass diese Mitglieder keine nicht autorisierten Services in ihren Konten erstellen werden und so zusätzliche Kosten für das Konto des Zahlers verursachen.  


## Abrechnungen an andere Mitglieder übertragen
{: #transfer-billing}

Während Netzmitglieder ein anderes Mitglied für sich bezahlen lassen können, können zu einem späteren Zeitpunkt andere Mitglieder die Abrechnungen übernehmen. Sie können das Konto des Zahlers aus Ihren Konten entfernen, sodass der Zahler die Bezahlung für Sie einstellen kann.

1. Klicken Sie in der Menüleiste oben in der {{site.data.keyword.cloud_notm}}-Konsole auf **Verwalten** > **Konto** > **Cloud Foundry-Organisationen**.
2. Klicken Sie auf die Organisation, zu der Sie das Konto des Zahlers hinzugefügt haben.
3. Wechseln Sie zur Registerkarte **Benutzer**.
4. Suchen Sie das Konto des Zahlers in der Benutzertabelle und klicken Sie auf den Link für **Organisation entfernen**.

Nach der Entfernung des Zahlerkontos aus Ihrem Konto wird Ihr Konto mit der Gebühr für die Mitgliedschaft im Blockchain-Netz und mit der Peergebühr belastet.

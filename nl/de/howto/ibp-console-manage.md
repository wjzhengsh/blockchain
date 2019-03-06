---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}


# Konsole verwalten
{: #ibp-console-manage-console}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Es gibt verschiedene Aktionen, die Sie ausführen können, um das Verhalten Ihrer Konsole zu verwalten. In diesem Abschnitt werden Aktionen außerhalb der Blockchain-Knotenoperationen beschrieben, die Sie bei der täglichen Verwendung der Konsole unterstützen.
{:shortdesc}

## Benutzer zur Konsole hinzufügen und aus ihr entfernen

Diese {{site.data.keyword.blockchainfull}} Platform-Konsole wird mit der E-Mail-Adresse des {{site.data.keyword.IBM_notm}} Eigners als Administrator der Konsole bereitgestellt. Der Administrator kann dann anderen Benutzern den Zugriff auf die Konsole erteilen.

Klicken Sie in der linken Navigationsleiste auf **Benutzer**, um neuen Benutzern anhand ihrer E-Mail-Adressen Zugriff auf die Konsole zu gewähren. Klicken Sie auf **Neue Benutzer hinzufügen** und geben Sie eine Liste der E-Mail-Adressen an, die Sie zusammen mit ihrem Benutzertyp autorisieren möchten: `Administrator` oder `Allgemein`. Benutzer mit dem Typ `Admininistrator` können neue Benutzer hinzufügen oder vorhandene Benutzer löschen und Konsolenprotokolle anzeigen. Benutzer des Typs `Allgemein` können sich an der Konsole anmelden, können jedoch keine Benutzer hinzufügen oder entfernen oder die Protokolle anzeigen.


Bei den in dieser Anzeige aufgeführten Benutzern handelt es sich lediglich um E-Mail-Adressen von Benutzern, die sich an der Konsole anmelden können. Sie stehen in keiner Beziehung zu den **verfügbaren Organisationen** auf der Registerkarte "Organisationen" oder zu den Identitäten, die von der Konsolenwallet gespeichert werden.
{:note}

## E-Mail-Adresse des Administrators aktualisieren

Wenn die Konsole von mehreren Personen oder Organisationen verwendet wird, wird empfohlen, dass Sie eine funktionale E-Mail-Adresse für den Zugriff auf das Netz erstellen. Durch die Verwendung einer solchen E-Mail-Adresse können Sie auf die Konsole zugreifen, wenn der ursprüngliche Administrator Ihre Organisation verlässt oder dessen E-Mail-Adresse ausgesetzt wurde. Wenn es nicht möglich ist, eine funktionale E-Mail zu erstellen, können Sie `Administrator`-Zugriff für mehrere Benutzer bereitstellen, um eine Abhängigkeit von einer einzelnen Person zu vermeiden.

Um die E-Mail-Adresse des Konsolenadministrators zu aktualisieren, der konfiguriert wurde, als die Konsole implementiert wurde, müssen Sie als Konsolenadministrator angemeldet sein. Navigieren Sie zur Registerkarte **Benutzer** und klicken Sie auf der Kachel **IBM ID** auf **Konfigurieren**. Geben Sie in der Anzeige, die geöffnet wird, eine neue E-Mail-Adresse für den Konsolenadministrator an.


## Konsolenprotokolle anzeigen

Sie können problemlos auf die Konsolenprotokolle zugreifen, wenn Sie Fehler beheben müssen, die bei der Verwendung der Konsole und der Knoten aufgetreten sind. Sie können die Anzahl der Protokolle, die von der Konsole erfasst werden, auch erhöhen oder verringern, indem Sie die Protokollierungsstufe festlegen. Die Protokolle der Konsole werden getrennt von den [Protokollen der Knoten](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) erfasst, die vom {{site.data.keyword.IBM_notm}} Kubernetes-Service erfasst werden.

Navigieren Sie zu der Registerkarte **Einstellungen** im Konsolenbrowser, um die Protokollierungseinstellungen zu ändern. Die Konsolenprotokolle werden aus zwei separaten Quellen erfasst:

  * **Clientprotokollierung:** Diese Protokolle werden erfasst, wenn Befehle von Ihrem Browser an die Konsole gesendet werden.
  * **Serverprotokollierung:** Diese Protokolle werden erfasst, wenn die Konsole Befehle an Ihre Knoten und von der Konsolenimplementierung sendet. Zu diesen Protokollen gehört die Protokollausgabe von Hyperledger Fabric.

Legen Sie die Menge der erfassten Protokolle fest, indem Sie die Dropdown-Liste unter den einzelnen Protokolltypen verwenden. Beispiel: **Fehler** und **Warnung** erfassen die wenigsten Protokolle, während mit **Debug** und **Alle** die meisten Protokolle erfasst werden.

Sie können die Konsolenprotokolle nur anzeigen, wenn Sie als Konsolenadministrator angemeldet sind. Wenn Sie die Protokolle auf der Registerkarte **Einstellungen** anzeigen möchten, ersetzen Sie das Wort `settings` in der Browser-URL durch `api/v1/logs`. Wenn Ihre Konsolen-URL z. B. `localhost:3001/settings` lautet, können Sie Ihre Protokolle anzeigen, indem Sie zu `localhost:3001/api/v1/logs` navigieren. Client- und Serverprotokolle werden in separaten Dateien erfasst. Die neuesten Protokolle befinden sich oben auf der Seite.


## Konsolenprotokolle anzeigen
{: #ibp-console-manage-console-node-logs}

Die Protokolle Ihrer Peers, Anordnungsknoten und Zertifizierungsstellen werden vom {{site.data.keyword.IBM_notm}} Kubernetes-Service erfasst. Führen Sie die folgenden Schritte aus, um die Protokolle Ihrer Knoten aus dem Cluster anzuzeigen, in dem Sie Ihr {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Netz implementiert haben.

1. Öffnen Sie das [{{site.data.keyword.cloud_notm}}-Dashboard ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/apps/) und navigieren Sie zu der Übersichtsanzeige Ihres {{site.data.keyword.IBM_notm}} Kubernetes-Service-Clusters.
2. Klicken Sie oberhalb der Übersichtsanzeige für den Cluster auf **Kubernetes-Dashboard**.
3. Verwenden Sie im Kubernetes-Dashboard die Dropdown-Liste der Namensbereiche in der linken Navigationsleiste, um den Namensbereich in die {{site.data.keyword.blockchainfull_notm}} Platform-Serviceinstanz zu ändern. Der Name der Serviceinstanz ist eine lange Zeichenfolge aus Buchstaben und Zahlen. Sie finden den Namen der Serviceinstanz am Anfang der URL für Ihre {{site.data.keyword.blockchainfull_notm}} Platform-Konsole.
4. Klicken Sie in der linken Navigationsleiste auf **Pods**, um die Liste der von Ihnen bereitgestellten Knotenpods anzuzeigen.
5. Klicken Sie auf einen Pod. Klicken Sie anschließend im oberen Menü auf **Protokolle anzeigen**, um die Protokolle Ihres Knotens zu öffnen. Über den Protokollen können Sie das Dropdown-Menü nach **Protokolle von** verwenden, um die Protokolle aus den verschiedenen Containern innerhalb des Pods anzuzeigen. Ihr Peer und die Statusdatenbank (z. B. CouchDB) werden beispielsweise in verschiedenen Containern ausgeführt und generieren daher unterschiedliche Protokolle.

Standardmäßig werden die Protokolle Ihrer Knoten lokal in Ihrem Cluster erfasst. Sie können auch den {{site.data.keyword.cloud_notm}} Log Analysis-Service oder den Service eines Drittanbieters verwenden, um die Protokolle aus Ihrem Netz zu erfassen, zu speichern und zu analysieren. Weitere Informationen finden Sie im Abschnitt zur [Protokollierung und Überwachung für den {{site.data.keyword.IBM_notm}} Kubernetes-Service ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Protokollierung und Überwachung für den {{site.data.keyword.IBM_notm}} Kubernetes-Service").


## Ressourcennutzung überwachen

Wenn die {{site.data.keyword.cloud_notm}} Platform-Knoten bereitgestellt werden, werden sie mit Standardeinstellungen für CPU, Speicher und Speicherung vordefiniert und diese Wert können nicht geändert werden. Sie können die Ressourcennutzung mithilfe des Dashboards des {{site.data.keyword.IBM_notm}} Kubernetes-Service überwachen. Wenn Sie versuchen, einen neuen Knoten in der {{site.data.keyword.cloud_notm}} Platform-Konsole zu erstellen und einen Fehler darüber erhalten, dass Sie Ihren Kubernetes-Cluster erweitern müssen, können Sie dem Kubernetes-Cluster mehr Speicher hinzufügen. Informationen darüber, wie Sie die Speicherkapazität oder Leistung Ihres bestehenden Datenträgers erhöhen können, finden Sie [hier ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Größe und IOPS der vorhandenen Speichereinheit ändern").

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

# Swagger-APIs verwenden, um mit dem Netz zu interagieren

{{site.data.keyword.blockchainfull_notm}} Platform stellt eine Reihe von REST-APIs in Swagger bereit, mit denen Sie die Knoten, Kanäle, Peers und Mitglieder Ihres Netzes verwalten können. Ihre Anwendungen können mithilfe dieser APIs wichtige Netzressourcen ohne den Network Monitor steuern. 

{:shortdesc}

Bevor Sie beginnen, müssen Sie eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) in {{site.data.keyword.Bluemix_notm}} erstellen und ein Starter Plan-Blockchain-Netz erstellen oder an einem Starter Plan-<!--or Enterprise Plan -->Blockchain-Netz teilnehmen.


## Netzberechtigungsnachweise abrufen

{: #retrieving-network-credentials}

Wechseln Sie in den Network Monitor Ihres Blockchain-Netzes und öffnen Sie die Anzeige "APIs" im Navigator auf der linken Seite. Ihre Netzberechtigungsnachweise für die REST-APIs werden angezeigt. Später berechtigen Sie die APIs, indem Sie die hier angezeigten Werte des Schlüssels ("key") und des geheimen Schlüssels ("secret") verwenden und die APIs mit dem Parameter "network_id" (Netz-ID) ausführen. Klicken Sie auf **Geheimen Schlüssel anzeigen**, um den Wert des Felds für den geheimen Schlüssel ("secret") sichtbar zu machen. Kopieren Sie die Werte der Felder für Schlüssel, geheimen Schlüssel und Netz-ID, die Sie später in der Swagger-Benutzerschnittstelle verwenden können.

In **Abbildung 1** ist die Anzeige "APIs" zu sehen:
![Anzeige 'APIs'](../images/API_screen_starter.png "Anzeige 'APIs'")
*Abbildung 1. APIs*

Wenn Sie den Starter Plan verwenden, ist es möglich, im Network Monitor zwischen Organisationen zu wechseln. Mit dem Starter Plan werden standardmäßig zwei Organisationen konfiguriert. Das Wechseln zwischen Organisationen kann nützlich sein, um die REST-APIs aus der Perspektive jeder Organisation auszuprobieren. Zum Abrufen der Berechtigungsnachweise für eine andere Organisation in Ihrem Netz klicken Sie auf Ihren Benutzernamen in der rechten oberen Ecke der Network Monitorkonsole. Klicken Sie in dem Menü, das sich daraufhin öffnet, auf den Dropdown-Pfeil neben der Organisation, um alle Organisationen anzuzeigen. Wählen Sie die Organisation aus, zu der Sie wechseln möchten, und zeigen Sie die zugeordneten Netzberechtigungsnachweise an.

**Abbildung 2** zeigt das Wechseln zwischen Organisationen:

![Wechseln zwischen Organisationen](../images/switch_orgs_starter.gif "Wechseln zwischen Organisationen")  
*Abbildung 2. Wechseln zwischen Organisationen*


## Swagger-APIs berechtigen

Klicken Sie auf den Link für die **Swagger-Benutzerschnittstelle (UI)** in der Anzeige "APIs", um die Swagger-Benutzerschnittstelle zu öffnen.  
<!-- remove this line because the link is different depending on if you are starter or enterprise plan
You can also open the Swagger UI with the URL in the connection profiles. For example, `http://blockchain-swagger-dev.stage1.mybluemix.net`.
-->

Klicken Sie in der Swagger-Benutzerschnittstelle auf die Schaltfläche **Berechtigen**. Das Berechtigungsfenster wird angezeigt. Geben Sie den Wert für den Schlüssel ("key") und den geheimen Schlüssel ("secret") in Ihren Netzberechtigungsnachweisen als Benutzername und Kennwort ein und klicken Sie auf **Berechtigen** und anschließend auf **Fertig**. Sie können jetzt die APIs ausführen. Beachten Sie, dass Sie Ihre Berechtigungsnachweise erneut berechtigen müssen, wenn Sie Ihre Browseransicht aktualisieren.

Bei Verwendung der Basisauthentifizierung werden alle Berechtigungsnachweise, die Sie im Fenster **Berechtigen** angeben, nach dem Klick auf die Schaltflächen **Berechtigen** und **Fertig** gespeichert und mit jedem REST-API-Aufruf übergeben.

In **Abbildung 3** wird die Autorisierung von Swagger-APIs dargestellt:

![APIs autorisieren](../images/swaggerUIAuthorize.gif "APIs autorisieren")  
*Abbildung 3. Autorisierung von APIs*


## APIs ausprobieren

Klicken Sie auf die REST-API, die Sie ausführen möchten, und klicken Sie auf die Schaltfläche für das **Ausprobieren**. 

In **Abbildung 4** ist die Schaltfläche für das Ausprobieren in der Swagger-Benutzerschnittstelle zu sehen: 

![Schaltfläche für das Ausprobieren in der Swagger-Benutzerschnittstelle](../images/swaggerUITryItOut.png "Schaltfläche für das Ausprobieren in der Swagger-Benutzerschnittstelle")  
*Abbildung 4. Schaltfläche für das Ausprobieren in der Swagger-Benutzerschnittstelle*

Nach einem Klick auf die Schaltfläche für das **Ausprobieren** können Sie für die API erforderliche Parameter eingeben. Die `Netz-ID` finden Sie in Ihren Netzberechtigungsnachweisen, sonstige Parameter im Network Monitor. Klicken Sie nach der Parametereingabe auf die Schaltfläche für das **Ausführen**, um den REST-API-Aufruf für das gesamte Netz auszuführen. 

**Abbildung 5** zeigt Parameter in der Swagger-Benutzerschnittstelle: 

![Parameter in der Swagger-Benutzerschnittstelle](../images/swaggerUIParams.png "Parameter in der Swagger-Benutzerschnittstelle")  
*Abbildung 5. Parameter eingeben*  

Nach einem Klick auf die Schaltfläche für das **Ausführen** werden die Antworten zu dem API-Aufruf für das Netz angezeigt. Ihnen wird auch ein cURL-Befehl angezeigt, mit dem die API direkt über die Befehlszeile aufgerufen werden kann. 

In **Abbildung 6** sind der Hauptteil der API-Antwort, die URL und der cURL-Befehl zu sehen: 

![API-Antwort in der Swagger-Benutzerschnittstelle](../images/swaggerUICurlResponse.png "API-Antwort in der Swagger-Benutzerschnittstelle")  
*Abbildung 6. API-Antwort*    

## Tipps zur Fehlerbehebung

### 401 Keine Berechtigung  
  Stellen Sie sicher, dass Sie die REST-API durch Bereitstellen Ihrer Netzberechtigungsnachweise berechtigt haben. Weitere Informationen finden Sie unter [Swagger-APIs berechtigen](#authorizing-swagger-apis).

### 400 Fehler: Falsche Anforderung
  Einige APIs akzeptieren möglicherweise ein Argument im Hauptteil (Body) der Anforderung, das als Filter für die Anzeige von Ergebnissen nur für einen bestimmten Peer verwendet wird. Ein Beispielausschnitt wird im Hauptteil bereitgestellt. Wenn dieser Ausschnitt verwendet wird, muss er bearbeitet werden, um den Peer bzw. die Liste von Peers anzugeben, nach denen Sie filtern möchten. Zur Vermeidung dieses Fehlers bearbeiten Sie entweder den Ausschnitt, um einen Peer in Ihrem Netz anzugeben, oder Sie entfernen den Ausschnitt völlig.

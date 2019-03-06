---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Einführung in den Starter Plan
{: #getting-started-with-starter-plan}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Der {{site.data.keyword.blockchainfull}} Platform Starter Plan stellt durch einen Klick ein vorkonfiguriertes Blockchain-Netz bereit. Das Angebot stellt standardmäßig ein genehmigtes Netz mit Konfiguration von zwei [Organisationen](/docs/services/blockchain/glossary.html#glossary-organization), einem [Peer](/docs/services/blockchain/glossary.html#glossary-peer) pro Organisation und einem [Kanal](/docs/services/blockchain/glossary.html#glossary-channel) bereit. Wenn das Netz erstellt ist, können Sie Ihr Netz skalieren und ihm weitere Organisationen und Peers hinzufügen. Diese Netze sind für Benutzer mit wenig Vorkenntnissen gedacht, die {{site.data.keyword.blockchainfull_notm}} Platform zum ersten Mal nutzen. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

Mit dem Starter Plan können Sie sich mit {{site.data.keyword.blockchainfull_notm}} Platform vertraut machen und sich Kenntnisse aneignen, Beispielanwendungen ausführen, eigene Anwendungen testen und ein Szenario mit mehreren Organisationen simulieren. Dieses Lernprogramm zur Einführung zeigt Ihnen, wie Sie den Starter Plan für die Entwicklung eines Blockchain-Netzes und die Interaktion mit diesem Netz einsetzen können.

Wenn Sie {{site.data.keyword.blockchainfull_notm}} Platform und Blockchains noch nicht kennen, können Sie sich in der  [Übersicht über die grundlegenden Komponenten](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview) von Netzen, die auf dem Open-Source-Angebot [Hyperledger Fabric](/docs/services/blockchain/reference/v10_fabric.html#hyperledger-fabric) aufbauen, weiter über Blockchains informieren. Zusätzliche Angaben finden Sie auch in der [Dokumentation von Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/blockchain.html "Introduction to Blockchain").

**Hinweis**: Beim {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan handelt es sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist. Wenn Sie eine Produktionsumgebung benötigen, lesen Sie die [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## Übersicht
{: #getting-started-with-starter-plan-overview}

**Eigenes Konsortium erstellen**

Der erste Schritt bei der Erstellung mit Blockchains ist die Gründung eines Konsortiums von Organisationen, die Blockchains für die Interaktion nutzen wollen, und die Einladung dieser Organisationen in Ihr Netz. Starter Plan-Benutzer können beim Ausprobieren der Technologie ein Konsortium mit mehreren Organisationen simulieren, indem sie selbst neue Organisationen erstellen.

- [Starter Plan-Netz erstellen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network)
- [Organisationen in das Netz einladen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-inviting-members)
- [An einem Starter Plan-Netz teilnehmen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-joining-a-network)

Innerhalb des Konsortiums der Organisationen, die an Ihren Netzen teilnehmen, können Sie durch die Erstellung von Kanälen mit einer Gruppe dieser Organisationen privat interagieren.

- [Kanäle erstellen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-create-channels)

**Netz und Anwendungen entwickeln**

Nachdem Sie Ihr Konsortium gegründet haben, müssen Sie den Chaincode (auch "Smart Contracts" genannt) schreiben, der die Geschäftslogik für Ihr Netz enthält und Ihnen die Interaktion mit Daten im Blockchain-Ledger ermöglicht. Anschließend müssen Sie die Fabric-SDKs zusammen mit diesen Smart Contracts nutzen, um aus der clientseitigen Anwendung Transaktionen an Ihr Netz zu übergeben.

- [Chaincode entwickeln](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-develop-chaincode)
- [Anwendungen mit Fabric-SDKs entwickeln](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-develop-apps)

{{site.data.keyword.blockchainfull_notm}} Platform bietet Beispielanwendungen, die Sie mit nur ein paar Klicks in Ihrem Starter Plan-Netz bereitstellen können.

- [Beispielanwendungen bereitstellen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-sample-applications)

**Netz betreiben und regeln**

{{site.data.keyword.blockchainfull_notm}} Platform bietet Tools und APIs, mit denen Sie Mitgliedschaft, Lebenszyklus und Allgemeinzustand Ihres Blockchain-Netzes verwalten können. Viele dieser Funktionen sind über die Benutzerschnittstelle von Network Monitor zugänglich.

- [Netzressourcen überwachen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-monitoring-resources)
- [Netzberechtigungsnachweise und Verbindungsprofil abrufen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-retrieving-network-credentials)
- [Netz mit Swagger-APIs verwalten](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-swagger)
- [Netz zurücksetzen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-resetting-network)
- [Von Starter Plan auf Enterprise Plan migrieren](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-migrate)
- [Netz löschen oder verlassen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-delete-network)


## Netz erstellen
{: #getting-started-with-starter-plan-creating-a-network}

Sie können ein Starter Plan-[Netz](/docs/services/blockchain/glossary.html#glossary-network) mit der Standardkonfiguration gleich im Anschluss an Ihre Erstellung einer {{site.data.keyword.blockchainfull_notm}} Platform-Serviceinstanz abrufen.

1. Lokalisieren Sie den [Blockchain-Service ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) im {{site.data.keyword.cloud_notm}}-Katalog.
    **Hinweis**: Sie müssen sich mit Ihrem gebührenpflichtigen {{site.data.keyword.cloud_notm}}-Konto anmelden. Wenn Sie kein Konto haben, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**. Nach der Erstellung eines kostenfreien Testkontos führen Sie ein Upgrade auf ein **nutzungsabhängig Konto** durch, indem Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der {{site.data.keyword.cloud_notm}}-Konsole aufrufen und auf **Kreditkarte hinzufügen** klicken.
2. Wählen Sie die Region in {{site.data.keyword.cloud_notm}} zum Erstellen des Netzes aus.
3. Wählen Sie Ihre Cloud Foundry-Organisation und den Bereich zum Erstellen des Netzes aus.
4. Wählen Sie **Starter-Mitgliedschaftsplan** in der Tabelle mit den Preisstrukturplänen aus.
5. Klicken Sie auf die Schaltfläche **Erstellen**. Beachten Sie, dass ein Popup-Eingangsfenster angezeigt wird, wenn Sie zur Teilnahme an einem Netz eingeladen wurden. Zum Erstellen eines Netzes wählen Sie **Mit Ihrem Netz fortfahren** aus und klicken auf **Weiter**. Informationen zum Teilnehmen an einem Netz finden Sie in Schritt 5 unter [Am Netz teilnehmen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-joining-a-network).
  Jetzt sind Sie bereit, Ihr Starter Plan-Netz mit der Standardkonfiguration zu verwenden. Das Netz wird mit einem Anordnungsknoten ("orderer"), der als "SOLO"-Anordnungsservice bezeichnet wird, zwei Organisationen, einer Zertifizierungsstelle (CA) und einem Peer pro Organisation ausgeführt. Darüber hinaus wird ein Standardkanal erstellt.
6. Klicken Sie auf die Schaltfläche **Starten**.

Sie finden Ihre Blockchain-Serviceinstanz in Ihrem [{{site.data.keyword.Bluemix_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}-Service-Dashboard").


## Mitglieder einladen
{: #getting-started-with-starter-plan-inviting-members}

Sie können andere [Organisationen](/docs/services/blockchain/glossary.html#glossary-organization) zur Teilnahme an Ihrem Starter Plan-Netz als [Mitglieder](/docs/services/blockchain/glossary.html#glossary-member) einladen, sodass Sie [Transaktionen](/docs/services/blockchain/glossary.html#glossary-transaction) miteinander ausführen können. Wenn Sie den Starter Plan zum Erwerb von Kenntnissen und zu Testzwecken verwenden möchten, können Sie ein Netz mit mehreren Organisationen simulieren, indem Sie dem Netz selbst weitere Mitglieder hinzufügen.

1. Klicken Sie in der Anzeige "Mitglieder" Ihres Network Monitor auf die Schaltfläche **Mitglieder einladen**.
2. Das Fenster "Mitglied einladen" wird geöffnet.
    - Wenn Sie eine andere Organisation einladen möchten, wählen Sie "Mitglied einladen" aus.  Geben Sie den Namen und die E-Mail-Adresse des Operators der Organisation an, die Sie einladen wollen.  Sie können darüber hinaus zusätzliche Informationen, die Ihrer Einladung beigefügt werden sollen, in das Feld "Hinweis hinzufügen" eingeben.  Klicken Sie auf die Schaltfläche **Einladung senden**.  Die eingeladene Organisation empfängt eine Einladungs-E-Mail und kann anschließend die Anweisungen in der E-Mail ausführen, um an Ihrem Netz teilzunehmen.
    - Wenn Sie weitere Organisationen hinzufügen wollen, die einem Kanal hinzugefügt werden können, wählen Sie "Mitglied hinzufügen" aus.  Geben Sie einen Namen für Ihre neue Organisation an. Sie können Ihrer neuen Organisation optional Peers hinzufügen oder dies später im Network Monitor tun.  Klicken Sie auf die Schaltfläche **Erstellen**. Beachten Sie, dass Sie nach dem Hinzufügen von Peers für Ihre neue Organisation zu dieser neuen Organisation wechseln müssen, um Ihre Peers anzuzeigen. Sie können zu einer anderen Organisation wechseln, indem Sie auf die obere rechte Ecke klicken und die Zielorganisation in der Dropdown-Liste unter dem Abschnitt **ORGANISATION WECHSELN** auswählen.


## Am Netz teilnehmen
{: #getting-started-with-starter-plan-joining-a-network}

Wenn Sie von einem Netzinitiator eingeladen werden, empfangen Sie eine E-Mail-Benachrichtigung mit Anweisungen, wie Sie an dem Netz teilnehmen. Führen Sie die Anweisungen in der E-Mail aus, sodass Sie zu einem der Mitglieder in dem Netz werden.

Sie müssen eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) mit der Option der Starter Plan-Mitgliedschaft in {{site.data.keyword.cloud_notm}} erstellen.

1. Melden Sie sich mit Ihrem {{site.data.keyword.cloud_notm}}-Konto an. Wenn Sie kein Konto haben, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**.
2. Wählen Sie die Cloud Foundry-Organisation aus, in der Sie Ihr {{site.data.keyword.blockchain}}-Netz speichern wollen.
3. Wählen Sie **Starter-Mitgliedschaftsplan** in der Tabelle mit den Preisstrukturplänen aus.
4. Klicken Sie auf die Schaltfläche **Erstellen**. Die Seite der Serviceinstanz wird mit einer Popup-Eingangsanzeige geöffnet. Beachten Sie, dass Sie auswählen können, an einem Netz teilzunehmen oder mit dem Erstellen eines eigenen Netzes fortzufahren. Informationen zum Erstellen eines Netzes finden Sie in Schritt 4 unter [Netz erstellen](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network).
5. Wählen Sie in der Eingangsanzeige die Option **An vorhandenem Netz teilnehmen** aus, wählen Sie das Netz, an dem Sie teilnehmen wollen, in der Dropdown-Liste aus, und klicken Sie auf **Weiter**.

Sie finden Ihre Blockchain-Serviceinstanz im [{{site.data.keyword.cloud_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}-Service-Dashboard").


## Kanäle erstellen
{: #getting-started-with-starter-plan-create-channels}

Kanäle werden durch Gruppen von Organisationen verwendet, um Datentransaktionen durchzuführen, ohne die Daten anderen Organisationen zugänglich zu machen. Sie können einen [Kanal](/docs/services/blockchain/glossary.html#glossary-channel) mit ausgewählten Mitgliedern Ihres Starter Plan-Netzes sowie Richtlinien dafür erstellen, wer den Kanal aktualisieren darf.

Weitere Informationen finden Sie unter [Kanal erstellen](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel). Dieser Abschnitt enthält auch Angaben darüber, wie Sie eine Einladung akzeptieren und Ihre Peers am Kanal beteiligen, wenn eine andere Organisation Sie in einen Kanal einlädt.

## Chaincode entwickeln
{: #getting-started-with-starter-plan-develop-chaincode}

[Chaincode](/docs/services/blockchain/glossary.html#glossary-chaincode), auch unter der Bezeichnung "Smart Contracts" bekannt, ist eine Software, die Ihnen das Lesen und Aktualisieren von Daten im Blockchain-Ledger ermöglicht. Chaincode kann Geschäftslogik in ein ausführbares Programm umwandeln, das alle Mitglieder im Blockchain-Netz vereinbart und verifiziert haben.

Weitere Informationen finden Sie im Lernprogramm [Chaincode entwickeln](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts), in dem Sie erfahren, wie Sie in das Schreiben von Chaincode einsteigen können und welche Fabric-Funktionen über Chaincode zugänglich sind.

## Chaincode installieren und instanziieren
{: #getting-started-with-starter-plan-install-instantiate-chaincode}
Sobald Sie an Kanälen teilnehmen und Ihre Geschäftslogik entwickelt haben, müssen Sie Chaincode auf den Peers im Netz installieren. Mit Network Monitor können Sie nicht nur Chaincode auf den Peers Ihrer Organisation installieren und instanziieren, sondern auch aktualisieren, was die kontinuierliche Entwicklung erleichtert.

Weitere Informationen zum Bereitstellen von vordefinierten Beispielen finden Sie unter [Chaincode installieren, instanziieren und aktualisieren](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).


## Anwendungen mit Fabric-SDKs entwickeln
{: #getting-started-with-starter-plan-develop-apps}

{{site.data.keyword.blockchainfull_notm}} Platform stellt API-Endpunkte zur Verfügung, zu denen Sie mithilfe der Fabric-SDKs eine Verbindung herstellen können. Unter Verwendung der SDKs können Sie Chaincode aus Ihren Anwendungen heraus aufrufen und Transaktionen an Ihr Blockchain-Netz übergeben.

Weitere Informationen bietet das Lernprogramm [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html#dev-app).


## Beispielanwendungen bereitstellen
{: #getting-started-with-starter-plan-sample-applications}

Der Starter Plan bietet Ihnen die Möglichkeit, Beispielanwendungen mit wenigen Klicks in Ihrem Netz bereitzustellen. Anhand dieser Beispiele können Sie mehr über Chaincode lernen und sie als Ausgangspunkte für eigene Entwicklungen nutzen.

Weitere Informationen finden Sie unter [Beispielanwendungen bereitstellen](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications).


## Netzressourcen überwachen
{: #getting-started-with-starter-plan-monitoring-resources}

Wenn Ihre Anwendung eine Transaktion anfordert, können Sie Informationen zum Transaktionsstatus im Network Monitor anzeigen. Weitere Informationen zur Netzüberwachung finden Sie unter [Netz überwachen](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network).


## Netzberechtigungsnachweise und Verbindungsprofil abrufen
{: #getting-started-with-starter-plan-retrieving-network-credentials}

Wenn Sie ein Starter Plan-Netz in {{site.data.keyword.cloud_notm}} erstellt haben, können Sie die Netzberechtigungsnachweise und das Verbindungsprofil auf der Serviceinstanzseite oder im Network Monitor abrufen.

### Von der Serviceinstanzseite abrufen
{: #getting-started-with-starter-plan-retrieve-service-instance}

Sie befinden sich auf der Serviceinstanzseite gleich, nachdem Sie eine Serviceinstanz erstellt haben. Sie können auch auf Ihren Service im [{{site.data.keyword.cloud_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}-Service-Dashboard") klicken, um Ihre Serviceinstanzseite zu öffnen.

Führen Sie die folgenden Schritte aus, um Ihre Serviceberechtigungsnachweise abzurufen:
1. Klicken Sie auf der Serviceinstanzseite auf **Serviceberechtigungsnachweise** im Navigator auf der linken Seite, um die Anzeige "Serviceberechtigungsnachweise" anzuzeigen.
2. Klicken Sie auf **Neuer Berechtigungsnachweis** in der Anzeige "Serviceberechtigungsnachweise".
3. Geben Sie in der Anzeige "Neuen Berechtigungsnachweis hinzufügen" dem Berechtigungsnachweis einen Namen und geben Sie im Feld "Inline-Konfigurationsparameter hinzufügen' **{"type": "service_instance_token"}** ein. Klicken Sie auf **Hinzufügen**. Der neue Berechtigungsnachweis wird in der Tabelle hinzugefügt. Sie können auf **Berechtigungsnachweise anzeigen** in der Spalte "AKTIONEN" klicken, um die Berechtigungsnachweisdetails anzuzeigen. Dieser Berechtigungsnachweis enthält den API-Schlüssel und den geheimen Schlüssel ('secret'), mit denen Sie APIs berechtigen können.

![Netzberechtigungsnachweise abrufen](images/service_credentials.gif "Netzberechtigungsnachweise abrufen")

### Im Network Monitor abrufen
{: #getting-started-with-starter-plan-network-creds}

Sie finden die Netzberechtigungsnachweise in der Anzeige "APIs" in Ihrem Network Monitor. Weitere Informationen zur Verwendung von APIs finden Sie in [Swagger-APIs verwenden, um mit dem Netz zu interagieren](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger).

Sie können das Verbindungsprofil in der Anzeige "Übersicht" im Network Monitor abrufen. Klicken Sie auf die Schaltfläche **Verbindungsprofil** in der Anzeige "Übersicht". Das Verbindungsprofil wird auf einer neuen Seite angezeigt.

## Netz mit Swagger-APIs verwalten
{: #getting-started-with-starter-plan-swagger}

{{site.data.keyword.blockchainfull_notm}} Platform stellt eine Reihe von REST-APIs in Swagger bereit, mit denen Sie die Knoten, Kanäle, Peers und Mitglieder Ihres Netzes verwalten können. Ihre Anwendungen können mithilfe dieser APIs wichtige Netzressourcen ohne den Network Monitor steuern.

Weitere Informationen finden Sie unter [Über Swagger-APIs mit dem Netz interagieren](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger).

## Netz zurücksetzen
{: #getting-started-with-starter-plan-reset-nw}

Wenn Sie angepasste Konfigurationen, aktiven Chaincode oder bereitgestellte Anwendungen bereinigen wollen, können Sie Ihr Netz auf die Standarderstkonfiguration zurücksetzen. Weitere Informationen finden Sie unter [Netz zurücksetzen](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network).


## Von Starter Plan auf Enterprise Plan migrieren
{: #getting-started-with-starter-plan-migrate}

Sie können Chaincode und Anwendungen, die Sie in einem Starter Plan-Netz testen, in einem Enterprise Plan-Netz bereitstellen. Wenn Sie Chaincode, den Sie in einem Starter Plan-Netz testen, im Enterprise Plan bereitstellen wollen, führen Sie die Anweisungen unter [Chaincode installieren, instanziieren und aktualisieren](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc) aus.

Es können nur Chaincode und Anwendungen migriert werden; Daten können nicht zwischen Starter Plan- und Entprise Plan-Netzen migriert werden.

<!--
As you can see in [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications#deploy_sample_applications_manually).
-->

Wenn Sie eine Beispielanwendung in Ihrem Starter Plan-Netz bereitstellen und diese Anwendung in einem Enterprise Plan-Netz bereitgestellt werden soll, können Sie Ihr Anwendungsrepository mit Verzweigungen in ein neues Repository kopieren und anschließend die Anweisungen ausführen, die in den Beispielanwendungen beschrieben sind, um diese im Enterprise Plan-Netz bereitzustellen.


## Netz löschen oder verlassen
{: #getting-started-with-starter-plan-delete-network}

Wenn Sie ein Netz löschen oder verlassen möchten, können Sie die Blockchain-Serviceinstanz aus Ihrem {{site.data.keyword.cloud_notm}}-Dashboard löschen.

Bevor Sie ein Netz verlassen, müssen Sie sicherstellen, dass Sie kein Mitglied eines Kanals des Netzes sind. Andernfalls treten Fehler auf, wenn Sie das Netz verlassen. Für das Entfernen eines Kanalmitglieds ist das Ausführen des Kanalaktualisierungsprozesses erforderlich. Weitere Informationen zum Kanalaktualisierungsprozess finden Sie in [Kanal aktualisieren](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).{:note}

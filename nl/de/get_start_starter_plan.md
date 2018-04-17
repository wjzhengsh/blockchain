---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Starter Plan-Netz steuern
{: #getting-started-with-starter-plan}

{{site.data.keyword.blockchainfull}} Platform Starter Plan stellt durch einen Klick ein vorkonfiguriertes Blockchain-Netz bereit. Das Angebot <!--offers you a free trial of 30 days and -->stellt standardmäßig ein genehmigtes Netz mit Konfiguration von zwei [Organisationen](glossary.html#organization), einem [Peer](glossary.html#peer) pro Organisation und einem [Kanal](glossary.html#channel) bereit. Wenn das Netz erstellt ist, können Sie Ihr Netz skalieren und ihm weitere Organisationen und Peers hinzufügen. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**Hinweise:**
1. {{site.data.keyword.blockchainfull}} Platform Starter Plan ist eine Entwicklungs- und Testumgebung. Wenn Sie eine Produktionsumgebung benötigen, lesen Sie die [Informationen zu Enterprise Plan](enterprise_plan.html).
2. Starter Plan befindet sich zurzeit im Betastadium und ist nur in der Region **Vereinigte Staaten (Süden)** in {{site.data.keyword.cloud_notm}} verfügbar.

Mithilfe von Starter Plan können Sie sich mit {{site.data.keyword.blockchainfull_notm}} Platform vertraut machen und sich Kenntnisse aneignen, Beispielanwendungen ausführen, eigene Anwendungen testen und ein Szenario mit mehreren Organisationen simulieren. Dieses Einführungslernprogramm erläutert die Voraussetzungen und beschreibt die Schritte, die Sie ausführen müssen um ein Starter Plan-Netz zu erstellen und zu verwenden.

Wenn Sie {{site.data.keyword.blockchainfull_notm}} Platform und Blockchain noch nicht kennen, können Sie das [Glossar](glossary.html) lesen, um sich mit den in dieser Dokumentation verwendeten Begriffen vertraut zu machen. Weitere Informationen zu Blockchain finden Sie in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html).


## Netz erstellen
Sie können ein Starter Plan-[Netz](glossary.html#network) mit der Standardkonfiguration gleich im Anschluss an Ihre Erstellung einer {{site.data.keyword.blockchainfull_notm}} Platform-Serviceinstanz abrufen.

1. Lokalisieren Sie den [Blockchain-Service ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) im {{site.data.keyword.cloud_notm}}-Katalog. Sie müssen sich mit Ihrem {{site.data.keyword.cloud_notm}}-Konto anmelden. Wenn Sie kein Konto haben, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**. Stellen Sie sicher, dass Sie die Region **Vereinigte Staaten (Süden)** in {{site.data.keyword.cloud_notm}} auswählen.
2. Wählen Sie Ihre Cloud Foundry-Organisation und den Bereich aus, in dem Sie Ihr Netz erstellen wollen.
3. Wählen Sie **Starter-Mitgliedschaftsplan** in der Preisstrukturtabelle aus.
4. Klicken Sie auf die Schaltfläche **Erstellen**. Beachten Sie, dass ein Popup-Eingangsfenster angezeigt wird, wenn Sie zur Teilnahme an einem Netz eingeladen wurden. Zum Erstellen eines Netzes wählen Sie **Mit Ihrem Netz fortfahren** aus und klicken auf **Weiter**. Informationen zum Teilnehmen an einem Netz finden Sie in Schritt 5 unter [Am Netz teilnehmen](#joining-a-network).
  Jetzt sind Sie bereit, Ihr Starter Plan-Netz mit der Standardkonfiguration zu verwenden. Das Netz wird mit einem Anordnungsknoten ("orderer"), der als "SOLO"-Anordnungsservice bezeichnet wird, zwei Organisationen, einer Zertifizierungsstelle (CA) und einem Peer pro Organisation ausgeführt. Darüber hinaus wird ein Standardkanal erstellt.
5. Klicken Sie auf die Schaltfläche **Starten**.

Sie finden Ihre Blockchain-Serviceinstanz in Ihrem [{{site.data.keyword.Bluemix_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}-Service-Dashboard").


## Mitglieder einladen
Sie können andere [Organisationen](glossary.html#organization) zur Teilnahme an Ihrem Starter Plan-Netz als [Mitglieder](glossary.html#member) einladen, sodass Sie [Transaktionen](glossary.html#transaction) miteinander ausführen können. Wenn Sie Starter Plan zum Erwerb von Kenntnissen und zu Testzwecken verwenden möchten, können Sie ein Netz mit mehreren Organisationen simulieren, indem Sie dem Netz selbst weitere Mitglieder hinzufügen.

1. Klicken Sie in der Anzeige "Mitglieder" Ihres Netzmonitors auf die Schaltfläche **Mitglieder einladen**.
2. Das Fenster "Mitglied einladen" wird geöffnet.
    - Wenn Sie eine andere Organisation einladen möchten, wählen Sie "Mitglied einladen" aus.  Geben Sie den Namen und die E-Mail-Adresse des Operators der Organisation an, die Sie einladen wollen. Sie können darüber hinaus zusätzliche Informationen, die Ihrer Einladung beigefügt werden sollen, in das Feld "Hinweis hinzufügen" eingeben. Klicken Sie auf die Schaltfläche **Einladung senden**. Die eingeladene Organisation empfängt eine Einladungs-E-Mail und kann anschließend die Anweisungen in der E-Mail ausführen, um an Ihrem Netz teilzunehmen.
    - Wenn Sie weitere Organisationen hinzufügen wollen, die einem Kanal hinzugefügt werden können, wählen Sie "Mitglied hinzufügen" aus. Geben Sie einen Namen für Ihre neue Organisation an. Sie können Ihrer neuen Organisation optional Peers hinzufügen oder dies später im Netzmonitor tun. Klicken Sie auf die Schaltfläche **Erstellen**. Beachten Sie, dass Sie nach dem Hinzufügen von Peers für Ihre neue Organisation zu dieser neuen Organisation wechseln müssen, um Ihre Peers anzuzeigen. Weitere Informationen zum Wechseln von Organisationen finden Sie unter [Organisationen wechseln](dashboard.html#switch-organizations).


## Am Netz teilnehmen
Wenn Sie von einem Netzinitiator eingeladen werden, empfangen Sie eine E-Mail-Benachrichtigung mit Anweisungen, wie Sie an dem Netz teilnehmen. Führen Sie die Anweisungen in der E-Mail aus, sodass Sie zu einem der Mitglieder in dem Netz werden.

Sie müssen eine [{{site.data.keyword.blockchain}} Platform-Serviceinstanz ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain) mit der Option der Starter Plan-Mitgliedschaft in {{site.data.keyword.cloud_notm}} erstellen.

1. Melden Sie sich mit Ihrem {{site.data.keyword.cloud_notm}}-Konto an. Wenn Sie kein Konto haben, klicken Sie auf die Schaltfläche **Für Erstellung registrieren**.
2. Wählen Sie die Cloud Foundry-Organisation aus, in der Sie Ihr {{site.data.keyword.blockchain}}-Netz speichern wollen.
3. Wählen Sie **Starter-Mitgliedschaftsplan** in der Preisstrukturtabelle aus.
4. Klicken Sie auf die Schaltfläche **Erstellen**. Die Seite der Serviceinstanz wird mit einer Popup-Eingangsanzeige geöffnet. Beachten Sie, dass Sie auswählen können, an einem Netz teilzunehmen oder mit dem Erstellen eines eigenen Netzes fortzufahren. Informationen zum Erstellen eines Netzes finden Sie in Schritt 4 unter [Netz erstellen](#creating-a-network).
5. Wählen Sie in der Eingangsanzeige die Option **An vorhandenem Netz teilnehmen** aus, wählen Sie das Netz, an dem Sie teilnehmen wollen, in der Dropdown-Liste aus, und klicken Sie auf **Weiter**.

Sie finden Ihre Blockchain-Serviceinstanz im [{{site.data.keyword.cloud_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}-Service-Dashboard").

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing and instantiating a chaincode](howto/install_instantiate_chaincode.html).
-->


## Netzberechtigungsnachweise und Verbindungsprofil abrufen
Wenn Sie ein Starter Plan-Netz in {{site.data.keyword.cloud_notm}} erstellt haben, können Sie die Netzberechtigungsnachweise und das Verbindungsprofil auf der Serviceinstanzseite oder im Netzmonitor abrufen.

### Von der Serviceinstanzseite abrufen
Sie befinden sich auf der Serviceinstanzseite gleich, nachdem Sie eine Serviceinstanz erstellt haben. Sie können auch auf Ihren Service im [{{site.data.keyword.cloud_notm}}-Service-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}-Service-Dashboard") klicken, um Ihre Serviceinstanzseite zu öffnen.

Führen Sie die folgenden Schritte aus, um Ihre Serviceberechtigungsnachweise abzurufen:
1. Klicken Sie auf der Serviceinstanzseite auf **Serviceberechtigungsnachweise** im Navigator auf der linken Seite, um die Anzeige "Serviceberechtigungsnachweise" anzuzeigen.
2. Klicken Sie auf **Neuer Berechtigungsnachweis** in der Anzeige "Serviceberechtigungsnachweise".
3. Geben Sie auf der Anzeige "Neuen Berechtigungsnachweis hinzufügen" dem Berechtigungsnachweis einen Namen und klicken Sie auf **Hinzufügen**. Der neue Berechtigungsnachweis wird in der Tabelle hinzugefügt. Sie können auf **Berechtigungsnachweise anzeigen** in der Spalte "AKTIONEN" klicken, um die Berechtigungsnachweisdetails anzuzeigen. Dieser Berechtigungsnachweis enthält den API-Schlüssel und den geheimen Schlüssel ('secret'), mit denen Sie APIs berechtigen können.
    Wenn Sie das Verbindungsprofil Ihres Netzes anzeigen wollen, geben Sie **{"legacy": true}** als Inline-Konfigurationsparameter an, wenn Sie neue Berechtigungsnachweise erstellen. Das Verbindungsprofil enthält die API-Endpunkte für Ihre Netzressourcen, die Sie in Ihren APIs und Anwendungen verwenden können.

### Im Netzmonitor abrufen
Sie finden die Netzberechtigungsnachweise auf der Anzeige "APIs" in Ihrem Netzmonitor. Weitere Informationen zur Verwendung von APIs finden Sie unter [APIs mit Swagger ausprobieren](apis.html).

Sie können das Verbindungsprofil auf der Anzeige "Übersicht" im Netzmonitor abrufen. Klicken Sie auf die Schaltfläche **Verbindungsprofil** auf der Anzeige "Übersicht". Das Verbindungsprofil wird auf einer neuen Seite angezeigt.


## Beispielanwendungen bereitstellen
Starter Plan bietet Ihnen die Möglichkeit, Beispielanwendungen mit wenigen Klicks in Ihrem Netz bereitzustellen. Mithilfe der Beispielanwendungen können Sie sich auf einfache Weise mit der Funktionsweise von Chaincodes und Anwendungen in einem Netz vertraut machen.

Weitere Informationen finden Sie unter [Beispielanwendungen bereitstellen](howto/prebuilt_samples.html).


## Angepasste Unternehmensnetze entwickeln und bereitstellen
Starter Plan integriert die Entwicklerumgebung von IBM Blockchain Platform: Develop und das Hyperledger Composer-Entwicklertoolset. Sie können Ihr Blockchain-Netz Ihren Geschäftsanforderungen entsprechend entwickeln. Wenn Sie ein Netz für Ihr Unternehmen entwickelt haben, können Sie Ihr Unternehmensnetz im Starter Plan-Netz bereitstellen.

Weitere Informationen finden Sie unter [Netz entwickeln](develop.html) und [Unternehmensnetze mit Starter Plan bereitstellen](develop_starter.html).


## Netzressourcen überwachen
Wenn Ihre Anwendung eine Transaktion anfordert, können Sie Informationen zum Transaktionsstatus im Netzmonitor anzeigen. Weitere Informationen zur Netzüberwachung finden Sie unter [Netz überwachen](howto/monitor_network.html).


## Netz zurücksetzen
Wenn Sie Ihre angepassten Konfigurationen, aktiven Chaincodes oder bereitgestellte Anwendungen bereinigen wollen, können Sie Ihr Netz auf die Standarderstkonfiguration zurücksetzen. Weitere Informationen finden Sie unter [Netz zurücksetzen](dashboard.html#reset-network).


## Von Starter Plan auf Enterprise Plan migrieren
{: #migrate}

Sie können eine BNA-Datei (`.bna`), Chaincode und Anwendungen, die Sie in einem Starter Plan-Netz testen, in einem Enterprise Plan-Netz bereitstellen.

Wenn Sie Ihre Unternehmensnetzarchivdatei (`.bna`) zur Hand haben, befolgen Sie die Anweisungen zur [Bereitstellung eines Unternehmensnetzes in Enterprise Plan](./develop_enterprise.html). Wenn Ihre `.bna`-Datei noch nicht vorliegt, rufen Sie sie aus der Starter Plan-Instanz mit dem Befehl `composer network download` ab. Weitere Informationen zum Befehl `composer network download` finden Sie in der [Hyperledger Composer-Befehlszeilendokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/reference/commands){:new_window}.

Chaincodes, die `.bna`-Dateien ähnlich sind, werden extern entwickelt. Wenn Sie einen Chaincode, den Sie in einem Starter Plan-Netz testen, in Enterprise Plan bereitstellen wollen, führen Sie die Anweisungen unter [Chaincode installieren und instanziieren](howto/install_instantiate_chaincode.html#installchaincode) aus.

Wie Sie im Abschnitt [Beispielanwendungen bereitstellen](howto/prebuilt_samples.html) sehen können, vereinfacht Starter Plan die Integration einer Beispielanwendung in Ihr Netz durch die Verwendung von Toolchain. Diese Einrichtung ermöglicht weiterhin eine kontinuierliche Integration, indem sie Ihre Beispielanwendung automatisch aktualisiert, wenn Ihr durch die Forkfunktion kopiertes Anwendungsrepository geändert wird. Wenn Sie diese Anwendung in einem Enterprise Plan-Netz bereitstellen wollen, können Sie Ihr kopiertes Anwendungsrepository in ein neues Repository kopieren und anschließend die Anweisungen ausführen, die unter [Beispielanwendungen manuell bereitstellen](howto/prebuilt_samples.html#deploy_sample_applications_manually) beschrieben sind.


## Netz löschen oder verlassen
Wenn Sie ein Netz löschen oder verlassen möchten, können Sie die Blockchain-Serviceinstanz aus Ihrem {{site.data.keyword.cloud_notm}}-Dashboard löschen.

**Hinweis**: Bevor Sie ein Netz verlassen, müssen Sie sicherstellen, dass Sie kein Mitglied eines Kanals des Netzes sind. Andernfalls treten Fehler auf, wenn Sie das Netz verlassen. Für das Entfernen eines Kanalmitglieds ist das Ausführen des Kanalaktualisierungsprozesses erforderlich. Weitere Informationen zum Kanalaktualisierungsprozess finden Sie in [Kanal aktualisieren](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

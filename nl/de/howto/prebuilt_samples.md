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

# Beispielanwendungen bereitstellen
{: #overview}

{{site.data.keyword.blockchainfull}} Platform stellt Beispielanwendungen zur Verfügung, die Sie bereitstellen und testen können, um sich mit Blockchain-Netzen und der Anwendungsentwicklung besser vertraut zu machen.
{: shortdesc}

Nach der Bereitstellung eines Starter Plan-Netzes können Sie die Beispielanwendungen im Network Monitor bereitstellen, der die Schritte zur Aktivierung der Beispielanwendungen für die Ausführung in Ihrem Netz automatisiert. Sie können außerdem die schrittweise Einführung in die Beispielanwendungen aktivieren, um sich mit dem gesamten Prozess der Anwendungsbereitstellung vertraut zu machen, den Sie ausführen müssen, wenn Sie eigene Anwendungen bereitstellen.

## Beispielanwendungen im Starter Plan bereitstellen

Der Starter Plan stellt unter Verwendung des Toolchain-Service in {{site.data.keyword.cloud_notm}} eine einfache Lösung zum Bereitstellen von Beispielanwendungen durch einige wenige Klicks bereit. Wenn eine Beispielanwendung bereitgestellt und gestartet wird, wird sie automatisch in Ihrem Blockchain-Netz ausgeführt.

Der Starter Plan stellt Ihnen zwei Beispielanwendungen für den Einstieg zur Verfügung.

* **Marbles**

  Mit der Beispielanwendung "Marbles" können Benutzer virtuelle Murmeln mit verschiedenen Eigenschaften erstellen und diese zwischen sich und anderen Benutzern übertragen. Weitere Informationen zu "Marbles" finden Sie unter [Marbles-Demo ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM-Blockchain/marbles).

<!--
* **Perishable Goods**

  The Perishable Goods sample enables users to deploy legal contracts that purchase goods in a supply chain based on delivery and temperature readings. For more information about this sample, see [Perishable Goods ![External link icon](../images/external_link.svg "External link icon")](https://github.com/clauseHQ/demo-clause-ibm-perishable-goods).

-->

* **Vehicle Manufacture**

  Die Beispielanwendung 'Vehicle Manufacture' führt Benutzer durch den Lebenszyklus eines virtuellen Fahrzeugs. Weitere Informationen zu diesem Beispiel finden Sie unter [Vehicle Manufacture ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM-Blockchain/vehicle-manufacture).

Führen Sie die folgenden Schritte aus, um eine Beispielanwendung bereitzustellen:

1. Rufen Sie den **Network Monitor** Ihres Starter Plan-Netzes auf. Wenn Sie keinen Network Monitor haben, finden Sie weitere Informationen unter [Netz erstellen](../get_start_starter_plan.html#creating-a-network).

2. Öffnen Sie die Anzeige zum Ausprobieren der Beispiele in Ihrem Network Monitor. Wählen Sie die Beispielanwendung aus, die Sie bereitstellen möchten, und klicken Sie auf die Schaltfläche **Über Toolchain bereitstellen**.
<!--
    ![sampleappflow0](../images/sampleappflow0.png)
-->
3. Ein Fenster für die Konfiguration des Toolchain-Service wird geöffnet. Stellen Sie sicher, dass alle erforderlichen Tools ordnungsgemäß integriert sind. Beachten Sie, dass Sie bei Verwendung mehrerer Organisationen sicherstellen müssen, dass Sie den richtigen Organisationsnamen eingeben. Der Organisationsname sollte die E-Mail-Adresse sein, die Sie zur Registrierung für das Netz verwenden.
    **Tipp:** Sie müssen Popup-Blocker inaktivieren, sodass sich die Seite für die Konfiguration des Toolchain-Service öffnen kann.
<!--
    ![sampleappflow1](../images/sampleappflow1.png)
-->
  Wenn dies die erste Anwendung ist, die Sie über Toolchain bereitstellen, müssen Sie Toolchain für den Zugriff auf das GitHub-Repository berechtigen.

  ![sampleappflow2](../images/sampleappflow2.png)

  Nach dem Klick auf die Schaltfläche "Berechtigen" werden Sie zu GitHub geleitet. Wenn Sie kein GitHub-Konto haben, müssen Sie eines erstellen. Erteilen Sie Toolchain Zugriff auf Ihre Repositorys, indem Sie Ihre Kontoinformationen eingeben. Wenn Sie Toolchain diesen Zugriff nicht geben möchten, können Sie die Beispielanwendungen manuell bereitstellen. Weitere Informationen finden Sie unter [Beispielanwendungen manuell bereitstellen](#deploy_sample_applications_manually).

5. Klicken Sie auf die Schaltfläche **Erstellen** am unteren Ende der Toolchain-Seite. Dies sollte Sie zum Network Monitor zurückführen, in dem die Bereitstellung von "Marbles" in Bearbeitung sein sollte. Dieser Prozess sollte etwa fünf bis zehn Minuten dauern.
<!--
    ![sampleappflow3](../images/sampleappflow3.png)
-->
Wenn die Bereitstellung abgeschlossen ist, können Sie mit der Verwendung der Beispielanwendung "Marbles" in Ihrem Starter Plan-Netz beginnen.

Da dieser Prozess ein durch eine Fork-Operation kopiertes GitHub-Repository erstellt, auf das Sie Zugriff haben und das Sie steuern können, können Sie Änderungen an Marbles in dem erstellten Repository vornehmen und diese festschreiben. Diese Festschreibungen lösen eine automatische Erstellung Ihrer Anwendung Marbles aus und ermöglichen Ihnen die Verwendung der Anwendung als Demo in {{site.data.keyword.cloud_notm}}.

## Beispielanwendungen manuell bereitstellen
{: #deploy_sample_applications_manually}

Wenn Sie Beispielanwendungen ohne Network Monitor bereitstellen wollen, stellen Sie sicher, dass Sie alle Softwarevoraussetzungen in Ihrem lokalen Dateisystem installieren. Weitere Informationen finden Sie unter [Anwendungsentwicklungsumgebung einrichten](../v10_application.html#setting-up-application-development-environment).

Darüber hinaus benötigen Sie ein Blockchain-Netz in {{site.data.keyword.cloud_notm}} mit dem Starter Plan oder Enterprise Plan und Sie müssen einen Kanal und die zugehörigen Peers konfigurieren. Weitere Informationen finden Sie unter [Starter Plan-Netz steuern](../get_start_starter_plan.html) und [Enterprise Plan-Netz steuern](../get_start.html). Wenn Sie ein Netz bereitgestellt haben und Anwendungen in dem Netz bereitstellen können, rufen Sie die API-Endpunkte Ihrer Netzressourcen ab, auf die Ihre Anwendung zugreifen soll. Weitere Informationen finden Sie unter [Netz-API-Endpunkte der Anwendung hinzufügen](../v10_application.html#adding-network-api-endpoints-to-your-application).

Sie können eine der folgenden Beispielanwendungen in Ihrem Netz bereitstellen:

- **Marbles**

  In der Marbles-Anwendung können mehrere Benutzer "Marbles" (Murmeln) mit verschiedenen Eigenschaften erstellen und sie an andere übertragen. Die Marbles-Anwendung ist in JavaScript geschrieben und der Chaincode ist in Go geschrieben.

  Sie finden den Beispielcode sowie Anweisungen unter [Marbles in GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM-Blockchain/marbles).

  Verwenden Sie die Bluemix-Anweisungen anstatt der Anweisungen für ein lokales Hosting von Marbles und geben Sie die relevanten Informationen aus Ihrem Netz ein. Beachten Sie, dass die Screenshots für Marbles auf GitHub die Enterprise Plan-Benutzerschnittstelle zeigen (da der Enterprise Plan nur den manuellen Pfad für die Bereitstellung von Marbles unterstützt), die sich ein wenig von der Starter Plan-Benutzerschnittstelle unterscheidet. Ungeachtet dessen enthalten beide Benutzerschnittstellen die gleichen Grundelemente und Sie finden die Namen Ihrer Peers, Kanäle und andere Informationen zu Serviceberechtigungsnachweisen auf den entsprechenden Anzeigen.

- **Fabcar**

  In Fabcar können Sie **Abfragen** und **Ledgeraktualisierungen** für KFZ-Datensätze im Ledger ausführen. Fabcar ist in JavaScript geschrieben und der Chaincode ist in Go geschrieben.

  Sie finden Beispielcode unter [Fabric car in GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) sowie eine Anleitung unter [Writing Your First Application ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

- **Sonstige Anwendungen**

  Weitere Informationen zum Hosting eigener Anwendungen in {{site.data.keyword.cloud_notm}} finden Sie unter [Anwendungen hosten](../v10_application.html#hosting-applications).

## Beispielanwendung löschen

Zum Löschen einer Beispielanwendung, die durch den Toolchain-Prozess eingerichtet wurde, navigieren Sie zu der Position der Beispielanwendung in der Benutzerschnittstelle. Da Beispielanwendungen in einem Kanal instanziiert werden, ist die Beispielanwendung in Kanälen zu finden. Klicken Sie auf **Kanäle** in der Navigation auf der linken Seite, um die Anzeige "Kanäle" zu öffnen. Klicken Sie auf den relevanten Kanal, in dem die Beispielanwendung instanziiert ist, und klicken Sie auf **Chaincode**. Dadurch wird der Chaincode angezeigt, der in diesem Kanal instanziiert ist.

Wenn Sie auf den Chaincode Ihrer Anwendung klicken, wird eine Registerkarte **Löschen** eingeblendet. Allerdings wird die Beispielanwendung nur durch Klicken auf **Löschen** nicht gelöscht, sondern es wird nur der Chaincode-Container gelöscht.  Sie müssen außerdem zum {{site.data.keyword.cloud_notm}}-Dashboard und zum Toolchain-Dashboard navigieren, um die Beispielanwendung auch dort zu löschen.

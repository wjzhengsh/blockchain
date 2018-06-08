---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informationen zu Starter Plan
{: #overview}

Der {{site.data.keyword.blockchainfull}} Platform Starter Plan ist eine Einstiegsoption, die Organisationen befähigt, Blockchain-Netze für mehrere Organisationen zu simulieren, schnell Anwendungen zu entwickeln und mit den bereitgestellten Beispielen zu arbeiten. Dabei verfügt die Plattform über dieselbe Benutzerschnittstellenbedienung wie andere Mitgliedschaftsoptionen, um den Einarbeitungsaufwand zu minimieren. Starter Plan-Netze basieren auf Hyperledger Fabric V1.1.
{:shortdesc}

**Hinweis**: Beim {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan handelt es sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist. Wenn Sie eine Produktionsumgebung benötigen, lesen Sie die [Informationen zum Enterprise Plan](enterprise_plan.html). Bevor Sie den Starter Plan verwenden, können Sie die [Starter Plan-Aspekte](#considerations) lesen.

Registrieren Sie sich für Ihre [{{site.data.keyword.blockchainfull_notm}}-Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) und probieren Sie den Starter Plan jetzt aus! Beachten Sie, dass Sie die Region **Vereinigte Staaten (Süden)** in {{site.data.keyword.cloud_notm}} auswählen müssen, um Blockchain-Netze mit der Starter Plan-Betaversion zu erstellen.


## Zielgruppen

Wenn eine der folgenden Situationen auf Sie zutrifft, ist der Starter Plan für Sie geeignet, um in die Arbeit mit Blockchain-Netzen einzusteigen.
- **_Sie wollen {{site.data.keyword.blockchainfull_notm}} Platform kennenlernen._**  
    Wenn Sie sich für {{site.data.keyword.blockchainfull_notm}} Platform interessieren oder Blockchain noch nicht kennen, bietet Ihnen der Starter Plan eine gute Möglichkeit, sich mit der Entwicklung und Steuerung eines realen Blockchain-Netzes vertraut zu machen. Sie finden die Komponenten, aus denen ein Netz besteht, Sie erfahren, wie Chaincode (auch als "Smart Contracts" bezeichnet) bereitgestellt und verwaltet wird und wie Kanäle hinzugefügt (und Kanalberechtigungen verwaltet) werden und Sie können verfolgen, wann ein neuer Block generiert wird. All dies genau wie in einem für die Produktion genutzten Netz.
- **_Sie wollen als mehrere Organisationen agieren, um die Netzentwicklung zu vereinfachen._**  
    Starter Plan bietet Ihnen die Möglichkeit, für mehrere Organisationen zu agieren, sodass Sie sehen können, wie IBM Blockchain Platform (IBP) kooperative Tasks wie die Kanalerstellung und die Chaincode-Instanziierung sowie das Testen von Anwendungen und das Aufrufen von Transaktionen verwaltet. Sie können darüber hinaus andere zur Mitarbeit in einem Starter Plan-Netz einladen (ebenso wie in Enterprise-Plänen).
- **_Sie möchten Demo-Lösungen in einem Live-Netz erstellen._**  
    Der Starter Plan stellt eine leistungsfähige Umgebung für das Testen von Netzdefinitionen (durch Integration einer `.bna`-Datei, die mit {{site.data.keyword.blockchainfull_notm}} Platform: Develop entwickelt wurde) und Blockchain-Anwendungen bereit. Das sofort einsatzfähige Blockchain-Netz ermöglicht schnelle Präsentationen für Kollegen, das Management und Partner durch die Betriebs- und Verwaltungstools, die durch den Network Monitor bereitgestellt werden.
- **_Sie arbeiten mit iterativen Entwicklungs- und Testphasen für Blockchain-Anwendungen._**  
    Der Starter Plan bietet Ihnen einen Staging-Bereich, sodass Sie Ihren Code kontinuierlich in einem Blockchain-Netz entwickeln und testen können. Sie können Ihren Code iterativ entwickeln und in Ihrer Architektur für kontinuierliche Integration und kontinuierliche Bereitstellung zur Verfügung stellen. Der Starter Plan stellt die gleichen Blockchain-Netzfunktionen und die gleichen Betriebs- und Verwaltungstools wie der Enterprise Plan bereit. Wenn Sie bereit sind, Ihr Projekt in einen der Enterprise-Pläne zu verschieben, können Sie dort auf die gleiche Weise wie im Starter Plan operieren, jedoch mit mehr Möglichkeiten, Ihr Netz auszubauen.
- **_Sie wollen Projekte vor der Einführung in die Produktionsumgebung testen._**  
    Der Starter Plan stellt eine Umgebung für Entwickler und Tester bereit, die ein schnelles Verschieben aus der lokalen Umgebung in eine reale Cloud-Blockchain-Umgebung ermöglicht.  Dieser Mechanismus ermöglicht es Entwicklern und Testern, sich auf die Funktionalitäten zu konzentrieren und ohne großen Aufwand vom Komponententest zum Funktionstest überzugehen. Teams für System-, Lösungs- und Leistungstests können die Umgebung ebenfalls ohne zusätzlichen Aufwand zum Einrichten eines lokalen Netzes verwenden.
- **_Sie wünschen sich eine funktionsfähige {{site.data.keyword.blockchainfull_notm}} Platform für Schulungszwecke._**  
    {{site.data.keyword.blockchainfull_notm}} Platform stellt praktisch die gleiche Benutzerschnittstelle<!--the same user interface--> im Starter Plan und den Enterprise-Plänen bereit, sodass sichergestellt ist, dass Ihre angepasste Schulung mit den gleichen Arbeitsabläufen arbeitet, die Ihre Organisation auch in einem Enterprise-Plan verwendet.
- **_Sie möchten Beispielanwendungen mithilfe von Toolchain ohne großen Aufwand bereitstellen._**  
    Der Starter Plan ermöglicht die Bereitstellung von Beispielanwendungen unter Verwendung von Toolchain durch einige wenige Klicks. Diese Beispiele unterstützen Entwickler, indem Sie eine wachsende Gruppe von Beispielen mit Code bereitstellen, die geändert und weitergegeben werden können.


## Starter Plan-Spezialisierungen

Der Starter Plan bietet die Möglichkeit, Mitgliedschaften mit einer Zertifizierungsstelle (CA) zu verwalten, Bewilligungen von Transaktionen durchzuführen, Anordnungsservices bereitzustellen, private Kanäle zu erstellen, Chaincode-Lebenszyklen zu verwalten und mit anderen in einem Live-Netz ebenso wie in einem Enterprise-Plan zusammenzuarbeiten.

Insbesondere bietet der Starter Plan ein vorkonfiguriertes Blockchain-Netz an, das Sie über den Network Monitor entwickeln, steuern und betreiben können. Der Starter Plan bietet darüber hinaus einfache Methoden zur Bereitstellung von Beispielanwendungen und integriert Ihre Anwendungen, die Sie mit {{site.data.keyword.blockchainfull_notm}} Platform: Develop entwickeln.

- **_Netzbereitstellung durch 1 Klick_**  
    Der Starter Plan stellt ein Netz mit einem Anordnungsservice, Zertifizierungsstellen (CA), einem Standardkanal und zwei Organisationen (mit je einem Peer) durch einen einzigen Klick zur Verfügung. {{site.data.keyword.blockchainfull_notm}} Platform übernimmt die Erstellung und Konfiguration dieses Netzes (Sie können es nach der Live-Bereitstellung aktualisieren). <!--The free trial provides you up to two organizations and two peers.-->
- **_Kosteneffizienz_**  
    Die Option der Starter Plan-Mitgliedschaft stellt viele der Blockchain-Funktionen bereit, die auch durch die Optionen der Enterprise Plan-Mitgliedschaft bereitgestellt werden, jedoch zu geringeren Kosten.  <!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->In der Betaversion können Sie den Starter Plan kostenlos nutzen.
- **_Simulation von Netzen mit mehreren Organisationen_**  
    Sie können den Starter Plan verwenden, um die Erstellung eines Netzes mit mehreren Organisationen zu simulieren. Dabei müssen Sie nicht wirklich andere Organisationen in Ihr Netz einladen, sondern können selbst als andere Organisationen agieren. Durch diesen Mechanismus können Sie lernen, wie eine neue Organisation an dem Netz teilnehmen kann, wie mehrere Organisationen im Netz zusammenarbeiten usw. Sie können zwischen Ihren Organisationen im Network Monitor wechseln, um das Netz aus der Sicht verschiedener Organisationen anzuzeigen und zu verwalten.
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_Swagger-APIs_**  
    Der Starter Plan macht verschiedene REST-APIs verfügbar, die Sie über eine Swagger-Schnittstelle ausprobieren können. Weitere Informationen finden Sie unter [APIs mit Swagger ausprobieren](swagger_apis.html).
- **_Beispielanwendungen_**  
    Der Starter Plan nutzt den Toolchain-Service in {{site.data.keyword.cloud_notm}} und ermöglicht eine Bereitstellung von Beispielen mit wenigen Klicks.  Wenn Sie ein Beispiel bereitstellen und starten, werden der Chaincode und die Anwendungen für Ihr Blockchain-Netz automatisch ausgeführt. Weitere Informationen zu Beispielanwendungen finden Sie unter [Beispielanwendungen bereitstellen](howto/prebuilt_samples.html).
- **Integration von _{{site.data.keyword.blockchainfull_notm}} Platform: Develop-Anwendungen_**  
    Der Starter Plan ermöglicht Ihnen die Bereitstellung von {{site.data.keyword.blockchainfull_notm}} Platform: Develop-Anwendungen in Ihrem Netz.  Weitere Informationen finden Sie unter [IBP: Develop-Anwendungen importieren](link).

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## Starter Plan-Aspekte
{: #considerations}

Beim Starter Plan handelt es sich um einen Einstiegspunkt für {{site.data.keyword.blockchainfull_notm}} Platform, der zu Entwicklungs- und Testzwecken genutzt werden kann. Überprüfen Sie die nachfolgend aufgeführten Punkte, bevor Sie den Starter Plan verwenden.

- **Voraussetzung für {{site.data.keyword.cloud_notm}}-Konto**  
    Der Starter Plan ist während der Betaphase kostenfrei. Sie müssen jedoch für Ihr {{site.data.keyword.cloud_notm}}-Konto ein Upgrade auf ein gebührenpflichtiges Konto durchführen, z. B. auf ein **nutzungsabhängiges Konto**. Für alle von {{site.data.keyword.blockchainfull_notm}} Platform angebotenen Mitgliedschaftspläne ist ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto erforderlich. Für ein Upgrade Ihres Kontos auf ein nutzungsabhängiges Konto rufen Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der {{site.data.keyword.cloud_notm}}-Konsole auf und klicken Sie auf **Kreditkarte hinzufügen**.
- **Unterschiede zum Enterprise Plan**
    - Die [Zertifizierungsstelle (CA)](glossary.html#ca) und der [Anordnungsservice](glossary.html#orderer) sind nicht fehlertolerant, weil jede Organisation nur eine Zertifizierungsstelle und ein Netz nur einen [Anordnungsknoten](glossary.html#orderer) hat.
    - Der Anordnungsservice verwendet nur den [SOLO](glossary.html#SOLO)-[Konsens](glossary.html#consensus). Ein Starter Plan-Netz besteht nur aus einem [Anordnungsknoten](glossary.html#orderer), der die Konsensverarbeitung für alle Peers ausführt.
    - Das [Hardwaresicherheitsmodul (HSM)](glossary.html#hsm) ist nicht verfügbar, um digitale Schlüssel für eine strikte Authentifizierung und die Verschlüsselungsverarbeitung zu schützen und zu verwalten.
- **Begrenzung für Netzressourcen**  
    Im Starter Plan werden 1 CPU, 2 GB RAM und 20 Gi Speicher an Hardware konstant für jede {{site.data.keyword.cloud_notm}}-Serviceinstanz zugewiesen. Wenn Sie weitere Netzressourcen zum selben Netz hinzufügen, verwenden diese Ressourcen die Hardwarezuweisungen gemeinsam. Wenn Sie jedoch ein Mitglied zur Teilnahme am Netz einladen, erstellt dieses Mitglied eine neue Serviceinstanz. Daher weist der Starter Plan dem neuen Mitglied erneut 1 CPU, 2 GB RAM und 20 Gi Speicher an Hardware zu.
- **Löschen inaktiver Netze**  
    Wenn Sie nach dem Erstellen eines Starter Plan-Netzes (Betaversion) über einen Zeitraum von 15 Tagen hinweg das Netz nicht aufrufen bzw. keine Transaktionen im Netz absetzen, löscht {{site.data.keyword.blockchainfull_notm}} Platform das Netz.
    - Wenn Sie das Netz nicht mehr benötigen, müssen Sie keine Aktion ausführen und das Netz wird automatisch gelöscht.
    - Wenn Sie das Netz noch benötigen, können Sie es im aktiven Status behalten, indem Sie Transaktionen in diesem Netz absetzen. Sie finden Ihr Starter Plan-Netz (Betaversion) im [{{site.data.keyword.cloud_notm}}-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/apps/){:new_window}. Wenn keine regulären Transaktionen vorhanden sind, die Sie absetzen können, führen Sie die [Anweisungen](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) zum Testen der Beispielanwendungen aus.
- **Wartung und Upgrade**  
    Wartungs- und Netzaktualisierungen für den Starter Plan erfolgen nach einem festen Zeitplan. Während des Wartungszeitraums können Sie keine neuen Netze bereitstellen und Sie bemerken möglicherweise kurze Netzunterbrechungsphasen.
- **Datenaufbewahrung**  
    Der Starter Plan garantiert keine Datenaufbewahrung bei Releaseaktualisierungen, einschließlich des Wechsels von Beta auf die allgemein verfügbare Version (GA-Version).
- **Migrationsaspekte**  
    Das Migrieren der Daten aus einem Starter Plan-Netz in einen anderen Mitgliedschaftsplan wird gegenwärtig nicht unterstützt. Es ist jedoch möglich, `.bna`-Dateien, Chaincode und Anwendungen zu migrieren, die im Starter Plan getestet wurden. Weitere Informationen finden Sie unter [Vom Starter Plan auf den Enterprise Plan migrieren](get_start_starter_plan.html#migrate).
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

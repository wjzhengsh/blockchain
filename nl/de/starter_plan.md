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

# Informationen zu Starter Plan
{: #overview}

Der {{site.data.keyword.blockchainfull}} Platform Starter Plan ist eine Einstiegsoption, die Organisationen befähigt, Blockchain-Netze für mehrere Organisationen zu simulieren, schnell Anwendungen zu entwickeln und mit Smart Contracts und Unternehmensnetzen zu arbeiten. Dabei verfügt die Plattform über dieselbe Benutzerschnittstellenbedienung wie andere Mitgliedschaftsoptionen, um den Einarbeitungsaufwand zu minimieren. Starter Plan-Netze basieren auf Hyperledger Fabric V1.1.
{:shortdesc}

**Hinweise:**
1. Beim {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan handelt es sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist. Wenn Sie eine Produktionsumgebung benötigen, lesen Sie die [Informationen zum Enterprise Plan](enterprise_plan.html). Bevor Sie den Starter Plan verwenden, können Sie die [Starter Plan-Aspekte](#considerations) lesen.
2. Der Starter Plan, der bisher nur als Betaversion verfügbar war, hat am 14. Juni 2018 das GA-Stadium (GA = General Availability, allgemeine Verfügbarkeit) erreicht. Wenn Sie über mit der Betaversion erstellte Netze verfügen, dürften Sie die [Migrationshinweise](#beta-to-ga) interessieren. 

Melden Sie sich für Ihre [{{site.data.keyword.blockchainfull_notm}}-Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) an und probieren Sie jetzt den Starter Plan mit dem [Testguthaben](howto/pricing.html#starter-plan-pricing) aus! <!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## Vorzüge des Starter Plans

- **_Netzbereitstellung durch 1 Klick_**  
Der Starter Plan stellt mit einem einzigen Klick ein Live-Blockchain-Netz bereit. Jedes Netz ist mit einem Anordnungsservice, Zertifizierungsstellen, zwei Organisationen (mit jeweils einem Peer pro Organisation) und einem Standardkanal ausgestattet, über den Chaincode bereitgestellt wird und Transaktionen ausgeführt werden. {{site.data.keyword.blockchainfull_notm}} Platform übernimmt die Erstellung und Konfiguration dieses Netzes (Sie können es nach der Live-Bereitstellung aktualisieren), sodass Sie sich ganz auf die Entwicklung konzentrieren können. Starter Plan-Netze basieren auf Hyperledger Fabric Version 1.1 und verwenden CouchDB als Ledger-Datenbank. <!--The free trial provides you up to two organizations and two peers.-->
- **_Kosteneffizienz_**  
    Die Option der Starter Plan-Mitgliedschaft stellt viele der Blockchain-Funktionen bereit, die auch durch die Optionen der Enterprise Plan-Mitgliedschaft bereitgestellt werden, jedoch zu geringeren Kosten. Im Testzeitraum des Starter Plans können Sie kostenfrei ein Blockchain-Netz mit Basisnetzressourcen bereitstellen. 
- **_Simulation von Netzen mit mehreren Organisationen_**  
    Sie können den Starter Plan verwenden, um die Erstellung eines Netzes mit mehreren Organisationen zu simulieren. Dabei müssen Sie nicht wirklich andere Organisationen in Ihr Netz einladen, sondern können selbst als andere Organisationen agieren. Durch diesen Mechanismus können Sie lernen, wie eine neue Organisation an dem Netz teilnehmen kann, wie mehrere Organisationen im Netz zusammenarbeiten usw. Sie können zwischen Ihren Organisationen im Network Monitor wechseln, um das Netz aus der Sicht verschiedener Organisationen anzuzeigen und zu verwalten.
    **Hinweis**: Wenn Sie mehr Ressourcen hinzufügen als in der Gratis-Testversion als Basisressourcen vorgesehen (zwei Unternehmen und zwei Peers), können zusätzliche Kosten entstehen. 
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **Integration von _{{site.data.keyword.blockchainfull_notm}} Platform: Develop-Anwendungen_**  
    Der Starter Plan ermöglicht es Ihnen, Unternehmensnetze bereitzustellen, die Sie unter [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login) entwickelt haben. Weitere Informationen finden Sie unter [IBP: Develop-Anwendungen importieren](link).

## Zielgruppen

Wenn eine der folgenden Situationen auf Sie zutrifft, ist der Starter Plan für Sie geeignet, um in die Arbeit mit Blockchain-Netzen einzusteigen.
- **_{{site.data.keyword.blockchainfull_notm}} Platform kennenlernen_**  
    Wenn Sie sich für {{site.data.keyword.blockchainfull_notm}} Platform interessieren oder Blockchain noch nicht kennen, bietet Ihnen der Starter Plan eine gute Möglichkeit, sich mit der Entwicklung und Steuerung eines realen Blockchain-Netzes vertraut zu machen. Sie finden die Komponenten, aus denen ein Netz besteht, Sie verwalten die Mitgliedschaft, Sie erfahren, wie Chaincode (auch als "Smart Contracts" bezeichnet) bereitgestellt und verwaltet wird und wie Kanäle hinzugefügt (und Kanalberechtigungen verwaltet) werden und Sie können verfolgen, wann ein neuer Block generiert wird. All dies genau wie in einem für die Produktion genutzten Netz. 
- **_Demo-Lösungen in einem Live-Netz erstellen_**  
    Der Starter Plan stellt eine leistungsfähige Umgebung zur Veranschaulichung von Blockchain-Anwendungen bereit. Das sofort einsatzfähige Blockchain-Netz ermöglicht schnelle Präsentationen für Kollegen, das Management und Partner durch die Betriebs- und Verwaltungstools, die durch den Network Monitor bereitgestellt werden.
- **_Über die Entwicklung einzelner Organisationen hinausgehende Entwicklung_**  
    Der Starter Plan bietet Ihnen die Möglichkeit, für mehrere Organisationen zu agieren, sodass Sie sehen können, wie IBM Blockchain Platform (IBP) kooperative Tasks wie die Kanalerstellung und die Chaincode-Instanziierung sowie das Testen von Anwendungen und das Aufrufen von Transaktionen verwaltet. Sie können darüber hinaus andere zur Mitarbeit in einem Starter Plan-Netz einladen (ebenso wie in Enterprise-Plänen). Dies erleichtert es Ihnen, in einer realistischeren Umgebung zu arbeiten und dies mit mehreren Parteien, die Chaincode und Transaktionen unterstützen und bewilligen. 
- **_Sie arbeiten mit iterativen Entwicklungs- und Testphasen für Blockchain-Anwendungen._**  
    Der Starter Plan bietet Ihnen einen Staging-Bereich, sodass Sie Ihren Code kontinuierlich in einem Blockchain-Netz entwickeln und testen können. Der Weg in die Cloud ermöglicht es Entwicklern und Testern, sich auf die Funktionalitäten zu konzentrieren und ohne großen Aufwand vom Komponententest zum Funktionstest überzugehen. Der Starter Plan stellt die gleichen Blockchain-Netzfunktionen und die gleichen Betriebs- und Verwaltungstools wie der Enterprise Plan bereit. Wenn Sie bereit sind, Ihr Projekt in einen der Enterprise-Pläne zu verschieben, können Sie dort auf die gleiche Weise wie im Starter Plan operieren, jedoch mit mehr Möglichkeiten, Ihr Netz auszubauen.


## Starter Plan-Aspekte
{: #considerations}

Beim Starter Plan handelt es sich um einen Einstiegspunkt für {{site.data.keyword.blockchainfull_notm}} Platform, der zu Entwicklungs- und Testzwecken genutzt werden kann.  Überprüfen Sie die nachfolgend aufgeführten Punkte, bevor Sie den Starter Plan verwenden.

- **Voraussetzung für {{site.data.keyword.cloud_notm}}-Konto**  	
    Sie müssen über ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto, z. B. ein **nutzungsabhängiges Konto**, verfügen. Für alle von {{site.data.keyword.blockchainfull_notm}} Platform angebotenen Mitgliedschaftspläne ist ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto erforderlich. Für ein Upgrade Ihres Kontos auf ein nutzungsabhängiges Konto rufen Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der {{site.data.keyword.cloud_notm}}-Konsole auf und klicken Sie auf **Kreditkarte hinzufügen**.   
- **Unterschiede zum Enterprise Plan**
    - Die [Zertifizierungsstelle (CA)](glossary.html#ca) und der [Anordnungsservice](glossary.html#orderer) sind nicht fehlertolerant, weil jede Organisation nur eine Zertifizierungsstelle und ein Netz nur einen [Anordnungsknoten](glossary.html#orderer) hat.
    - Der Anordnungsservice verwendet nur den [SOLO](glossary.html#SOLO)-[Konsens](glossary.html#consensus). Ein Starter Plan-Netz besteht nur aus einem [Anordnungsknoten](glossary.html#orderer), der die Konsensverarbeitung für alle Peers ausführt.
    - Das [Hardwaresicherheitsmodul (HSM)](glossary.html#hsm) ist nicht verfügbar, um digitale Schlüssel für eine strikte Authentifizierung und die Verschlüsselungsverarbeitung zu schützen und zu verwalten.
- **Begrenzung für Netzressourcen**  
    Im Starter Plan werden 1 CPU, 2 Gi RAM für jeden Peer und 20 Gi Speicher für jede {{site.data.keyword.cloud_notm}}-Serviceinstanz zugewiesen.  
- **Löschen inaktiver Netze**  
    Wenn Sie nach dem Erstellen eines Starter Plan-Netzes über einen Zeitraum von 15 Tagen hinweg das Netz nicht aufrufen bzw. keine Transaktionen im Netz absetzen, wird das Netz von {{site.data.keyword.blockchainfull_notm}} Platform gelöscht. 
    - Wenn Sie das Netz nicht mehr benötigen, müssen Sie keine Aktion ausführen und das Netz wird automatisch gelöscht.
    - Wenn Sie das Netz noch benötigen, können Sie es im aktiven Status behalten, indem Sie Transaktionen in diesem Netz absetzen. Sie finden Ihr Starter Plan-Netz im [{{site.data.keyword.cloud_notm}}-Dashboard ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/dashboard/apps/){:new_window}. Wenn keine regulären Transaktionen vorhanden sind, die Sie absetzen können, führen Sie die [Anweisungen](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) zum Testen der Beispielanwendungen aus.
- **Wartung und Upgrade**
    Wartungs- und Netzaktualisierungen für den Starter Plan erfolgen nach einem festen Zeitplan. Während des Wartungszeitraums können Sie keine neuen Netze bereitstellen und Sie bemerken möglicherweise kurze Netzunterbrechungsphasen.
- **Datenspeicherung**
    Der Starter Plan garantiert kein Beibehalten von Daten bei Releaseaktualisierungen, einschließlich des Wechsels von Beta auf die allgemein verfügbare Version (GA-Version). 
- **Migrationsaspekte**
    - Das Migrieren der Daten aus einem Starter Plan-Netz in einen anderen Mitgliedschaftsplan wird gegenwärtig nicht unterstützt. Es ist jedoch möglich, `.bna`-Dateien, Chaincode und Anwendungen zu migrieren, die im Starter Plan getestet wurden. Weitere Informationen finden Sie unter [Vom Starter Plan auf den Enterprise Plan migrieren](get_start_starter_plan.html#migrate).

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## Migration von Betaversion auf GA-Version
{: #beta-to-ga}

Der Starter Plan hat am 14. Juni 2018 das GA-Stadium (GA = General Availability, allgemeine Verfügbarkeit) erreicht. Mit Erreichen des GA-Stadiums bietet {{site.data.keyword.blockchainfull_notm}} Platform für jedes {{site.data.keyword.cloud_notm}}-Konto ein Testguthabenn in Höhe von 500 $ für das Erstellen von Blockchain-Netzen mit dem Starter Plan. Weitere Informationen zu dem Testguthaben finden Sie im Abschnitt zur *Starter Plan-Testversion* im Abschnitt zur [Preisstruktur des Starter Plans](howto/pricing.html#starter-plan-pricing). Stellen Sie sicher, dass Sie über ein gebührenpflichtiges IBM Cloud-Konto, z. B. ein **nutzungsabhängiges Konto**, verfügen. 

Mit der Betaversion des Starter Plans erstellte Blockchain-Netze bleiben **kostenfrei**, bis sie **30 Tage** nach Erreichen des GA-Stadiums für den Starter Plan gelöscht werden. Eine Datenmigration von mit der Betaversion des Starter Plans erstellten Netzen zu GA-Netzen wird nicht unterstützt. **Ihre Daten in mit der Betaversion erstellten Netzen bleiben nicht erhalten.** Sie können jedoch den Chaincode sowie Ihre Unternehmensnetze und Anwendungen manuell migrieren. 
- Wenn Sie über aktiven Chaincode in mit der Betaversion erstellten Netzen verfügen, installieren und instanziieren Sie den Chaincode in GA-Netzen. Weitere Informationen finden Sie in [Chaincode installieren, instanziieren und aktualisieren](howto/install_instantiate_chaincode.html).
- Wenn Sie ein Unternehmensnetz in mit der Betaversion erstellten Netzen bereitgestellt haben, stellen Sie das Unternehmensnetz mit der `.bna`-Datei in GA-Netzen bereit. Weitere Informationen hierzu können Sie dem Abschnitt [Unternehmensnetz im Starter Plan bereitstellen](develop_starter.html) entnehmen. 
- Wenn Sie selbst entwickelte Anwendungen für mit der Betaversion erstellte Netze ausgeführt haben, aktualisieren Sie die API-Endpunkte so, dass sie auf GA-Netzknoten verweisen. Weitere Informationen finden Sie unter [Netz-API-Endpunkte der Anwendung hinzufügen](v10_application.html#adding-network-api-endpoints-to-your-application).

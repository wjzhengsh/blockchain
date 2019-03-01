---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Informationen zu Starter Plan
{: #starter-plan-about}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Der {{site.data.keyword.blockchainfull}} Platform Starter Plan ist eine Einstiegsoption, die Organisationen befähigt, Blockchain-Netze für mehrere Organisationen zu simulieren, schnell Anwendungen zu entwickeln und mit Smart Contracts und Unternehmensnetzen zu arbeiten. Dabei verfügt die Plattform über dieselbe Benutzerschnittstellenbedienung wie andere Mitgliedschaftsoptionen, um den Einarbeitungsaufwand zu minimieren. Nach dem 4. Oktober 2018 werden neue Starter Plan-Netze auf der Basis von Hyperledger Fabric Version 1.2.1 erstellt. Ältere Starter Plan-Netze behalten Fabric Version 1.1.0 bei.
{:shortdesc}

**Starter Plan** ist eine Umgebung für diejenigen, die Blockchain-Netze entwickeln möchten oder mehr zur Blockchain-Technologie erfahren möchten. Sie können den Starter Plan als Entwicklungs- oder Testumgebung verwenden, mit dem Sie klein anfangen und das Mitgliedschafts- oder Transaktionsvolumen Ihres Netzes skalieren können, bevor Sie in eine Produktionsumgebung wechseln.

 Wenn Sie ein Starter Plan-Netz bereitstellen möchten, melden Sie sich jetzt für Ihre [Starter-Mitgliedschaft ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) unter {{site.data.keyword.cloud_notm}} an. Wenn Sie mit der Entwicklung Ihres Netzes beginnen möchten, besuchen Sie [Einführung in den Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).


**Hinweise:**
- Beim {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan handelt es sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist. Wenn Sie eine Produktionsumgebung benötigen, lesen Sie die [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform ist ein Plattformservice unter {{site.data.keyword.cloud_notm}} und alle Mitgliedschaftsangebote unterliegen den [{{site.data.keyword.cloud_notm}} Services-Bedingungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} Services-Bedingungen") der Service-Level-Agreements (SLAs). Starter Plan-Netze werden in **mehreren Umgebungen** in geografisch getrennten Rechenzentren bereitgestellt.

## Vorzüge des Starter Plans
{: #starter-plan-about-what-starter-plan-offers}

- **_Netzbereitschaft mit einem Klick_**
    Der Starter Plan stellt mit einem einzigen Klick ein Live-Blockchain-Netz bereit. Jedes Netz ist mit Zertifizierungsstellen, einem Anordnungsservice, zwei Organisationen (mit jeweils einem Peer pro Organisation) und einem Standardkanal ausgestattet, über den Chaincode bereitgestellt wird und Transaktionen ausgeführt werden. {{site.data.keyword.blockchainfull_notm}} Platform übernimmt die Erstellung und Konfiguration dieses Netzes (Sie können es nach der Live-Bereitstellung aktualisieren), sodass Sie sich ganz auf die Entwicklung konzentrieren können. Starter Plan-Netze basieren auf Fabric Version 1.2 und verwenden CouchDB als Statusdatenbank. 
- **_Kosteneffizienz_**
    Die Option der Starter Plan-Mitgliedschaft stellt viele der Blockchain-Funktionen bereit, die auch durch die Optionen der Enterprise Plan-Mitgliedschaft bereitgestellt werden, jedoch zu geringeren Kosten. Weitere Informationen zu diesem Thema finden Sie im Abschnitt zur [Starter Plan-Preisstruktur](/docs/services/blockchain/howto/pricing.html#ibp-pricing-starter-pricing).
- **_Simulation von Netzen mit mehreren Organisationen_**
    Sie können den Starter Plan verwenden, um die Erstellung eines Netzes mit mehreren Organisationen zu simulieren. Dabei müssen Sie nicht wirklich andere Organisationen in Ihr Netz einladen, sondern können selbst als andere Organisationen agieren. Durch diesen Mechanismus können Sie lernen, wie eine neue Organisation an dem Netz teilnehmen kann, wie mehrere Organisationen im Netz zusammenarbeiten usw. Sie können zwischen Ihren Organisationen im Network Monitor wechseln, um das Netz aus der Sicht verschiedener Organisationen anzuzeigen und zu verwalten.

{{site.data.keyword.IBM_notm}} bietet keinen Support für Netze, die Hyperledger Composer in Produktionsumgebungen nutzen, inklusive Composer-Befehlszeilenschnittstelle, JavaScript-APIs, REST-Server und Web Playground.
{:note}

## Eignung von Starter Plan für Ihre Zwecke
{: #starter-plan-about-is-starter-suitable-for-you}

Wenn eine der folgenden Situationen auf Sie zutrifft, ist der Starter Plan für Sie geeignet, um in die Arbeit mit Blockchain-Netzen einzusteigen.
- **_Schulung für {{site.data.keyword.blockchainfull_notm}} Platform._**
    Wenn Sie sich für {{site.data.keyword.blockchainfull_notm}} Platform interessieren oder Blockchain noch nicht kennen, bietet Ihnen der Starter Plan eine gute Möglichkeit, sich mit der Entwicklung und Steuerung eines realen Blockchain-Netzes vertraut zu machen. Sie finden die Komponenten, aus denen ein Netz besteht, Sie verwalten die Mitgliedschaft, Sie erfahren, wie Chaincode (auch als "Smart Contracts" bezeichnet) bereitgestellt und verwaltet wird und wie Kanäle hinzugefügt (und Kanalberechtigungen verwaltet) werden und Sie können verfolgen, wann ein neuer Block generiert wird. All dies genau wie in einem für die Produktion genutzten Netz.
- **_Erstellung von Demo-Lösungen in einem Live-Netz._**
    Der Starter Plan stellt eine leistungsfähige Umgebung zur Veranschaulichung von Blockchain-Anwendungen bereit. Das sofort einsatzfähige Blockchain-Netz ermöglicht schnelle Präsentationen für Kollegen, das Management und Partner durch die Betriebs- und Verwaltungstools, die durch den Network Monitor bereitgestellt werden.
- **_Entwicklung nicht nur für einzelne Organisationen._**
    Der Starter Plan bietet Ihnen die Möglichkeit, für mehrere Organisationen zu agieren, sodass Sie sehen können, wie {{site.data.keyword.blockchainfull_notm}} Platform kooperative Tasks wie die Kanalerstellung und die Chaincode-Instanziierung sowie das Testen von Anwendungen und das Aufrufen von Transaktionen verwaltet. Sie können darüber hinaus andere zur Mitarbeit in einem Starter Plan-Netz einladen (ebenso wie in Enterprise-Plänen). Dies erleichtert es Ihnen, in einer realistischeren Umgebung zu arbeiten und dies mit mehreren Parteien, die Chaincode und Transaktionen unterstützen und bewilligen.
- **_Interaktive Entwicklung und interaktiver Test von Blockchain-Anwendungen_**
    Der Starter Plan bietet Ihnen einen Staging-Bereich, sodass Sie Ihren Code kontinuierlich in einem Blockchain-Netz entwickeln und testen können. Der Weg in die Cloud ermöglicht es Entwicklern und Testern, sich auf die Funktionalitäten zu konzentrieren und ohne großen Aufwand vom Komponententest zum Funktionstest überzugehen. Der Starter Plan stellt die gleichen Blockchain-Netzfunktionen und die gleichen Betriebs- und Verwaltungstools wie der Enterprise Plan bereit. Wenn Sie bereit sind, Ihr Projekt in einen der Enterprise-Pläne zu verschieben, können Sie dort auf die gleiche Weise wie im Starter Plan operieren, jedoch mit mehr Möglichkeiten, Ihr Netz auszubauen.

## Starter Plan-Aspekte
{: #starter-plan-about-considerations}

Beim Starter Plan handelt es sich um einen Einstiegspunkt für {{site.data.keyword.blockchainfull_notm}} Platform, der zu Entwicklungs- und Testzwecken genutzt werden kann.  Überprüfen Sie die nachfolgend aufgeführten Punkte, bevor Sie den Starter Plan verwenden.

- **Voraussetzung für {{site.data.keyword.cloud_notm}}-Konto**
    Sie müssen über ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto, z. B. ein **nutzungsabhängiges Konto**, verfügen. Für alle von {{site.data.keyword.blockchainfull_notm}} Platform angebotenen Mitgliedschaftspläne ist ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto erforderlich. Für ein Upgrade Ihres Kontos auf ein nutzungsabhängiges Konto rufen Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der {{site.data.keyword.cloud_notm}}-Konsole auf und klicken Sie auf **Kreditkarte hinzufügen**.
- **Unterschiede zum Enterprise Plan**
    - Die [Zertifizierungsstelle (CA)](/docs/services/blockchain/glossary.html#glossary-CA) und der Anordnungsservice sind nicht fehlertolerant, weil jede Organisation nur eine Zertifizierungsstelle und ein Netz nur einen [Anordnungsknoten](/docs/services/blockchain/glossary.html#glossary-orderer) hat.
    - Der Anordnungsservice verwendet nur den [SOLO](/docs/services/blockchain/glossary.html#glossary-solo) [Konsens](/docs/services/blockchain/glossary.html#glossary-consensus). Ein Starter Plan-Netz besteht nur aus einem Anordnungsknoten, der die Konsensverarbeitung für alle Peers ausführt.
    - Das [Hardwaresicherheitsmodul (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) ist nicht verfügbar, um digitale Schlüssel für eine strikte Authentifizierung und die Verschlüsselungsverarbeitung zu schützen und zu verwalten.
- **Starter Plan-Versionen und -Upgrade**
    - Nach dem 4. Oktober 2018 werden neue Starter Plan-Netze auf der Basis von Hyperledger Fabric Version 1.2.1 erstellt. Ältere Starter Plan-Netze behalten Fabric Version 1.1.0 bei.
    - Neue Peers, die zu älteren Starter Plan-Netzen hinzugefügt werden, werden basierend auf Fabric Version 1.2.1 erstellt. Die Leistung Ihres Netzes wird durch die Abwärtskompatibilität nicht beeinträchtigt.
    - Sie haben die Möglichkeit, erweiterte Einstellungen für die [Kanalkonfiguration![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "Channel configuration") und [Zugriffssteuerungslisten ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-v1.2/access_control.html "Access Control Lists") zu verwenden, wenn Sie einen Kanal erstellen oder aktualisieren.
    - Die [Serviceerkennung ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "service discovery") und [privaten Daten ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "private data") sind Funktionen, die von Hyperledger Fabric v1.2 im Starter Plan nicht unterstützt werden.
    - Falls Sie ein älteres Starter Plan-Netz mit Fabric Version 1.1.0 [zurücksetzen](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network), hat das neue Netz den Stand von Fabric Version 1.2. Wenn Sie Ihr Netz zurücksetzen, müssen Sie Ihren Chaincode oder Ihre Dateien ".bna" im neuen Netz installieren und Mitglieder Ihres alten Netzes erneut einladen.
- **Netzressourcenbeschränkung**
    Im Starter Plan werden 1 CPU, 4 Gi RAM für jeden Peer und 20 Gi Speicher für jede {{site.data.keyword.cloud_notm}}-Serviceinstanz (einschließlich Peers) zugewiesen. Chaincode-Container und Ledgerblöcke stellen die ressourcenintensivsten Netzkomponenten dar. Benutzer, die in ihrem Netz über zahlreiche Peers verfügen, viele Blöcke generieren oder große Chaincode-Dateien verwenden, werden die Auswirkungen der Ressourcenbeschränkungen auf die Leistung bemerken. Die Auslastung des Speichers durch Ihre Netze können Sie in der [Anzeige "Übersicht" von Network Monitor](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-storage) einsehen.
- **Wartung und Upgrade**
    Wartungs- und Netzaktualisierungen für den Starter Plan erfolgen nach einem festen Zeitplan. Während des Wartungszeitraums können Sie keine neuen Netze bereitstellen und Sie bemerken möglicherweise kurze Netzunterbrechungsphasen.
- **Datenaufbewahrung**
    Der Starter Plan garantiert keine Datenaufbewahrung bei Releaseaktualisierungen.
- **Hinweise zur Migration**
    Das Migrieren der Daten aus einem Starter Plan-Netz in einen anderen Mitgliedschaftsplan wird gegenwärtig nicht unterstützt. Es ist jedoch möglich, `.bna`-Dateien, Chaincode und Anwendungen zu migrieren, die im Starter Plan getestet wurden. Weitere Informationen finden Sie unter [Vom Starter Plan auf den Enterprise Plan migrieren](/docs/services/blockchain/howto/migrate_sp_ep.html#migrate_starter_to_enterprise).

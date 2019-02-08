---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informationen zu Starter Plan
{: #overview}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Der {{site.data.keyword.blockchainfull}} Platform Starter Plan ist eine Einstiegsoption, die Organisationen befähigt, Blockchain-Netze für mehrere Organisationen zu simulieren, schnell Anwendungen zu entwickeln und mit Smart Contracts und Unternehmensnetzen zu arbeiten. Dabei verfügt die Plattform über dieselbe Benutzerschnittstellenbedienung wie andere Mitgliedschaftsoptionen, um den Einarbeitungsaufwand zu minimieren. Nach dem 4. Oktober 2018 werden neue Starter Plan-Netze auf der Basis von Hyperledger Fabric Version 1.2.1 erstellt. Ältere Starter Plan-Netze behalten Fabric Version 1.1.0 bei.
{:shortdesc}

**Hinweise:**
- Beim {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan handelt es sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist. Wenn Sie eine Produktionsumgebung benötigen, lesen Sie die [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html). Bevor Sie den Starter Plan verwenden, können Sie die [Starter Plan-Aspekte](#considerations) lesen.
- {{site.data.keyword.blockchainfull_notm}} Platform ist ein Plattformservice unter {{site.data.keyword.cloud_notm}} und alle Mitgliedschaftsangebote unterliegen den [{{site.data.keyword.cloud_notm}} Services-Bedingungen![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "{{site.data.keyword.cloud_notm}} Services-Bedingungen") der Service-Level-Agreements (SLAs). Starter Plan-Netze werden in **mehreren Umgebungen** in geografisch getrennten Rechenzentren bereitgestellt.

## Vorzüge des Starter Plans

- **_Netzbereitschaft mit einem Klick_**
    Der Starter Plan stellt mit einem einzigen Klick ein Live-Blockchain-Netz bereit. Jedes Netz ist mit Zertifizierungsstellen, einem Anordnungsservice, zwei Organisationen (mit jeweils einem Peer pro Organisation) und einem Standardkanal ausgestattet, über den Chaincode bereitgestellt wird und Transaktionen ausgeführt werden. {{site.data.keyword.blockchainfull_notm}} Platform übernimmt die Erstellung und Konfiguration dieses Netzes (Sie können es nach der Live-Bereitstellung aktualisieren), sodass Sie sich ganz auf die Entwicklung konzentrieren können. Starter Plan-Netze basieren auf Fabric Version 1.2 und verwenden CouchDB als Statusdatenbank. <!--The free trial provides you up to two organizations and two peers.-->
- **_Kosteneffizienz_**
    Die Option der Starter Plan-Mitgliedschaft stellt viele der Blockchain-Funktionen bereit, die auch durch die Optionen der Enterprise Plan-Mitgliedschaft bereitgestellt werden, jedoch zu geringeren Kosten.{{site.data.keyword.blockchainfull_notm}} Platform bietet 500 Dollar an Cloud-Punkten für neue Starter Plan-Benutzer an. Dieses Guthaben ermöglicht Ihnen die Bereitstellung eines kostenfreien Blockchain-Netzes mit den Basisnetzressourcen für einen Zeitraum von einem Monat. Weitere Einzelheiten hierzu finden Sie im Abschnitt mit den [wichtigen Hinweisen zum Starter Plan](#starter-plan-considerations).
- **_Simulation von Netzen mit mehreren Organisationen_**
    Sie können den Starter Plan verwenden, um die Erstellung eines Netzes mit mehreren Organisationen zu simulieren. Dabei müssen Sie nicht wirklich andere Organisationen in Ihr Netz einladen, sondern können selbst als andere Organisationen agieren. Durch diesen Mechanismus können Sie lernen, wie eine neue Organisation an dem Netz teilnehmen kann, wie mehrere Organisationen im Netz zusammenarbeiten usw. Sie können zwischen Ihren Organisationen im Network Monitor wechseln, um das Netz aus der Sicht verschiedener Organisationen anzuzeigen und zu verwalten.
    **Hinweis**: Wenn Sie mehr Ressourcen hinzufügen als in der Gratis-Testversion als Basisressourcen vorgesehen (zwei Unternehmen und zwei Peers), können zusätzliche Kosten entstehen.
<!-- - **_Easy to deploy sample applications_**
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html). -->
- **_{{site.data.keyword.blockchainfull_notm}} Platform: Entwicklung von Anwendungsintegration_**
    Der Starter Plan ermöglicht es Ihnen, Unternehmensnetze bereitzustellen, die Sie unter [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login) entwickelt haben. Weitere Informationen hierzu können Sie dem Abschnitt [Unternehmensnetz im Starter Plan bereitstellen](/docs/services/blockchain/develop_starter.html) entnehmen.

## Eignung von Starter Plan für Ihre Zwecke

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
{: #considerations}

Beim Starter Plan handelt es sich um einen Einstiegspunkt für {{site.data.keyword.blockchainfull_notm}} Platform, der zu Entwicklungs- und Testzwecken genutzt werden kann.  Überprüfen Sie die nachfolgend aufgeführten Punkte, bevor Sie den Starter Plan verwenden.

- **Voraussetzung für {{site.data.keyword.cloud_notm}}-Konto**
    Sie müssen über ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto, z. B. ein **nutzungsabhängiges Konto**, verfügen. Für alle von {{site.data.keyword.blockchainfull_notm}} Platform angebotenen Mitgliedschaftspläne ist ein gebührenpflichtiges {{site.data.keyword.cloud_notm}}-Konto erforderlich. Für ein Upgrade Ihres Kontos auf ein nutzungsabhängiges Konto rufen Sie **Verwalten** > **Abrechnung und Nutzung** > **Abrechnung** in der {{site.data.keyword.cloud_notm}}-Konsole auf und klicken Sie auf **Kreditkarte hinzufügen**.
- **Cloud-Punkte von {{site.data.keyword.blockchainfull_notm}} Platform**
    {{site.data.keyword.blockchainfull_notm}} Platform bietet 500 Dollar an Cloud-Punkten für neue Starter Plan-Benutzer an. Dieses Guthaben unterstützt Benutzer bei den ersten Schritten, indem es die Kosten einer Standardnetzkonfiguration für einen Monat abdeckt.
    - Neue Benutzer müssen sich anmelden, um Cloud-Punkte erwerben zu können. Sie werden nicht automatisch angelegt. [Melden Sie sich an ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "Melden Sie sich an"), um die Punkte in Anspruch nehmen zu können, wenn Sie diesen Vorgang noch nicht ausgeführt haben. Warten Sie 24 Stunden, um sicherzustellen, dass die Punkte sich auf Ihrem [Konto ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "Konto") befinden, bevor Sie auf Starter Plan zugreifen. Andernfalls fallen möglicherweise Gebühren an, bevor das Guthaben angelegt wurde.
    - Die Cloud-Punkte gelten für alle {{site.data.keyword.cloud_notm}}-Services. Es kann auch für andere als {{site.data.keyword.blockchainfull_notm}} Platform-Produkte verbraucht werden, die Sie in {{site.data.keyword.cloud_notm}} nutzen.
    - Das Angebot bietet ein lebenslanges Guthaben. Das Guthaben bleibt so lange erhalten, wie Ihr {{site.data.keyword.cloud_notm}}-Konto besteht. Sie müssen es nicht innerhalb des ersten Monats verwenden.
    Weitere Informationen zur Verwendung des Testguthabens finden Sie im [Leitfaden zur Preisstruktur](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing).
- **Unterschiede zum Enterprise Plan**
    - Die [Zertifizierungsstelle (CA)](/docs/services/blockchain/glossary.html#ca) und der [Anordnungsservice](/docs/services/blockchain/glossary.html#orderer) sind nicht fehlertolerant, weil jede Organisation nur eine Zertifizierungsstelle und ein Netz nur einen [Anordnungsknoten](/docs/services/blockchain/glossary.html#orderer) hat.
    - Der Anordnungsservice verwendet nur den [SOLO](/docs/services/blockchain/glossary.html#solo)-[Konsens](/docs/services/blockchain/glossary.html#consensus). Ein Starter Plan-Netz besteht nur aus einem [Anordnungsknoten](/docs/services/blockchain/glossary.html#orderer), der die Konsensverarbeitung für alle Peers ausführt.
    - Das [Hardwaresicherheitsmodul (HSM)](/docs/services/blockchain/glossary.html#hsm) ist nicht verfügbar, um digitale Schlüssel für eine strikte Authentifizierung und die Verschlüsselungsverarbeitung zu schützen und zu verwalten.
- **Starter Plan-Versionen und -Upgrade**
    - Nach dem 4. Oktober 2018 werden neue Starter Plan-Netze auf der Basis von Hyperledger Fabric Version 1.2.1 erstellt. Ältere Starter Plan-Netze behalten Fabric Version 1.1.0 bei.

    - Neue Peers, die zu älteren Starter Plan-Netzen hinzugefügt werden, werden basierend auf Fabric Version 1.2.1 erstellt. Die Leistung Ihres Netzes wird durch die Abwärtskompatibilität nicht beeinträchtigt.
    - Sie können die Funktion für [private Daten ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "Private Data") von Hyperledger Fabric Version 1.2 in neuen Starter Plan-Netzen verwenden. Weitere Informationen zur Verwendung von privaten Daten bei Ihrem Chaincode enthält der Abschnitt [Private Daten](/docs/services/blockchain/howto/develop_chaincode.html#private-data).
    - Sie haben die Möglichkeit, erweiterte Einstellungen für die [Kanalkonfiguration![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "Channel configuration") und [Zugriffssteuerungslisten ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/access_control.html "Access Control Lists") zu verwenden, wenn Sie einen Kanal erstellen oder aktualisieren.
    - Die Funktion für die [Serviceerkennung ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Service discovery") aus Hyperledger Fabric Version 1.2 wird bei {{site.data.keyword.blockchainfull_notm}} nicht unterstützt.
    - Falls Sie ein älteres Starter Plan-Netz mit Fabric Version 1.1.0 [zurücksetzen](/docs/services/blockchain/v10_dashboard.html#reset-network), hat das neue Netz den Stand von Fabric Version 1.2. Wenn Sie Ihr Netz zurücksetzen, müssen Sie Ihren Chaincode oder Ihre Dateien ".bna" im neuen Netz installieren und Mitglieder Ihres alten Netzes erneut einladen.
- **Netzressourcenbeschränkung**
    Im Starter Plan werden 1 CPU, 4 Gi RAM für jeden Peer und 20 Gi Speicher für jede {{site.data.keyword.cloud_notm}}-Serviceinstanz (einschließlich Peers) zugewiesen. Chaincode-Container und Ledgerblöcke stellen die ressourcenintensivsten Netzkomponenten dar. Benutzer, die in ihrem Netz über zahlreiche Peers verfügen, viele Blöcke generieren oder große Chaincode-Dateien verwenden, werden die Auswirkungen der Ressourcenbeschränkungen auf die Leistung bemerken. Die Auslastung des Speichers durch Ihre Netze können Sie in der [Anzeige "Übersicht" von Network Monitor](/docs/services/blockchain/v10_dashboard.html#storage) einsehen.
- **Wartung und Upgrade**
    Wartungs- und Netzaktualisierungen für den Starter Plan erfolgen nach einem festen Zeitplan. Während des Wartungszeitraums können Sie keine neuen Netze bereitstellen und Sie bemerken möglicherweise kurze Netzunterbrechungsphasen.
- **Datenaufbewahrung**
    Der Starter Plan garantiert keine Datenaufbewahrung bei Releaseaktualisierungen.
- **Hinweise zur Migration**
    Das Migrieren der Daten aus einem Starter Plan-Netz in einen anderen Mitgliedschaftsplan wird gegenwärtig nicht unterstützt. Es ist jedoch möglich, `.bna`-Dateien, Chaincode und Anwendungen zu migrieren, die im Starter Plan getestet wurden. Weitere Informationen finden Sie unter [Vom Starter Plan auf den Enterprise Plan migrieren](/docs/services/blockchain/howto/migrate_sp_ep.html).


<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](/docs/services/blockchain/develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](/docs/services/blockchain/v10_application.html#adding-network-api-endpoints-to-your-application).
-->

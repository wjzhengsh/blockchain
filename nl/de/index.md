---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**ACHTUNG:** Lesen Sie vor der Verwendung eines {{site.data.keyword.blockchainfull}} Platform-Angebots die technischen Angaben und Unterstützungsinformationen im Abschnitt [Haftungsausschluss](/docs/services/blockchain/needtoknow.html).
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform (IBP) ist die einzige integrierte und für den sofortigen Einsatz bereite Plattform, die den gesamten Lebenszyklus eines Blockchain-Netzes mit mehreren teilnehmenden Organisationen umfasst. Die Lösung wurde konzipiert, um die Einrichtung globaler, unternehmensgerechter Blockchain-Netze durch die Zusammenarbeit in allen Phasen zu beschleunigen, die die Leistung und Sicherheit für die anspruchsvollsten Anwendungsfälle und die reguliertesten Branchen bieten. Von {{site.data.keyword.IBM_notm}} verwaltete Netzangebote in {{site.data.keyword.cloud_notm}} sind für Blockchain-Neukunden geeignet. Kunden mit Erfahrungen in der Verwendung von Hyperledger Fabric können über {{site.data.keyword.cloud_notm}} Private (ICP) oder Amazon Web Services (AWS) Netzkomponenten oder ein vollständiges Netz in ihrer eigenen Infrastruktur bereitstellen.

{{site.data.keyword.blockchainfull_notm}} Platform bietet unterschiedliche Angebote, die die verschiedenen Benutzertypen beim Einstieg in die Arbeit mit Blockchains unterstützen und den Benutzern helfen, ihre Anwendungen in die Produktionsphase zu bringen.

|       | [**Starter Plan**](#starter-enterprise)      | [ **Enterprise Plan**](#starter-enterprise)  | [**IBP for ICP**](#ibp-for-icp) | [**IBP for AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **Inhalt** | **Service-Levels (Basis), Entwicklungs- und Testumgebung** | **Service-Levels (Erweitert) und produktionsbereite Umgebung für Unternehmen** |**Bereitstellbare Helm-Diagramme für Zertifizierungsstellen, Anordnungsknoten und Peers** | **AWS-Peer mit Schnelleinstieg**  |
| **Abrechnungsmodell** | **Monatliches Abonnement mit [verfügbaren Cloud-Punkten](/docs/services/blockchain/howto/pricing.html#starter-plan-pricing)** | **Monatliche Subskription** |  **[VPC-Preisstruktur](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-pricing) und kostenlose Community Edition** | **Kostenlos** |
| **Cloudplattform**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud Private**| **AWS**|

**Achtung:** Der **Starter Plan** darf nicht in Produktionsumgebungen verwendet werden. Es handelt sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist.

## Leistungsspektrum von {{site.data.keyword.blockchainfull_notm}} Platform

Die {{site.data.keyword.blockchainfull_notm}}-Angebote basieren auf der [Hyperledger Fabric](/docs/services/blockchain/reference/v10_fabric.html)-Codebasis und bieten aufgrund ihrer modularen Architektur die für die Anforderungen Ihres Unternehmens erforderliche Sicherheit, Datenintegrität, Skalierbarkeit und Leistung.

{{site.data.keyword.blockchainfull_notm}} Platform stellt ein sehr sicheres, genehmigtes Blockchain-Netz bereit, in dem authentifizierte Mitglieder Assets leicht definieren und die Geschäftslösungen für deren Änderung und Austausch erstellen können. Mit den {{site.data.keyword.blockchainfull_notm}} Platform-Angeboten können Sie die Vorteile eines Orchestrierungsframeworks nutzen, in dem Sie **Ihr Konsortium in kurzer Zeit in einem aktiven Blockchain-Netz strukturieren** können. {{site.data.keyword.blockchainfull_notm}} Platform bietet geeignete Tools, die es mehreren Institutionen erleichtern, sich zusammenzuschließen und ein demokratisch gesteuertes Netz zu erstellen. Die Erstellungs-, Governance- und Betriebsaufgaben des Netzes werden durch einen integrierten Dashboard-Monitor und bereitgestellte Dienstprogramme intuitiver und transparenter gestaltet. Anstelle der umständlichen Prozesse der Netzerstellung und Governance-Implementierung können sich Mitglieder von Konsortien **auf die Bereitstellung von Smart Contracts und die Übertragung von Assets und Informationen konzentrieren**.

Die **hohe Verfügbarkeit** der integralen Ressourcen des Netzes (Peers, Anordnungsknoten, Zertifizierungsstellen) eliminiert die verheerenden Effekte, die durch Single Points of Failure entstehen können. Ein integrierter Dashboard-Monitor ermöglicht eine einfache Verwaltung dieser Ressourcen und stellt einen leistungsfähigen Mechanismus zur Visualisierung von Assets und Smart Contracts bereit. *Diese Funktionalität wird nur beim Starter Plan und beim Enterprise Plan unterstützt.*.

Die **Modularität** der Hyperledger Fabric-Architektur und die klare Trennung von Netzrollen stellt eine Infrastruktur bereit, die eine hohe Skalierbarkeit und die Anpassung an vielfältige Anwendungsfälle ermöglicht. *Diese Funktionalität wird bei allen {{site.data.keyword.blockchainfull_notm}} Platform-Angeboten zur Verfügung gestellt und unterstützt.*

Die Überprüfungen und Ausgleichungen, die während des Lebenszyklus einer Transaktion auftreten, führen zu konsistenten und vollständig überprüften Ergebnissen und die Ledger bleiben während der Implementierung des Gossip-Protokolls durchgehend synchron. Identität und Zugriffssteuerung werden ohne großen Aufwand mit **sign/verify**-Operationen durchgesetzt, die fortlaufend im Netz durchgeführt werden. *Diese Operationen werden bei allen {{site.data.keyword.blockchainfull_notm}} Platform-Angeboten zur Verfügung gestellt und unterstützt.*

Mit **Governance-Tools** können Mitglieder die kritischen Geschäftsregeln für ihr Netz verwalten. Beispielsweise können Sie eine Richtlinie implementieren, die definiert, wie viele Mitglieder eines Netzes dem Beitritt eines neuen Mitglieds zustimmen müssen. Oder es gibt ein Asset, das von jedem Teilnehmer bewilligt werden muss, damit eine Änderung vorgenommen werden kann. Governance-Regeln sind von grundlegender Bedeutung für alle Unternehmensnetze und sind häufig sehr umfangreich. Governance-Tools (z. B. Richtlinieneditoren) vereinfachen diesen Prozess in hohem Maße. *Diese Funktionalität ist nur beim Starter Plan und beim Enterprise Plan verfügbar.*.

Enterprise Plan-Netze werden in einer **extrem sicheren und isolierten** Umgebung ohne externen Zugriff (einschließlich Rootzugriff) auf Netzressourcen ausgeführt. Daten werden bei der Ausführung und im Ruhezustand verschlüsselt und Module zur Hardwaresicherheitsunterstützung sorgen dafür, dass digitale Schlüssel in Übereinstimmung mit branchenspezifischen Vorschriften geschützt werden. Hardwarevirtualisierung wird verwendet, um die einzelnen Knoten in einer isolierten Umgebung auszuführen. Dadurch werden andere Knoten im Netz vor Peers mit möglicherweise nicht regelkonformem oder schädlichem Chaincode geschützt. Hashing, Operationen zum Signieren/Verifizieren und die Kommunikation zwischen Knoten werden durch erweiterte Verschlüsselungsimplementierungen beschleunigt. *Diese Funktionalität ist nur beim Enterprise Plan verfügbar.*

Weitere Informationen zu allen Hyperledger Fabric-Funktionen und -Features finden Sie in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/ "Hyperledger Fabric-Dokumentation").

**Abbildung 1** zeigt ein Beispiel eines bereitgestellten Blockchain-Netzes, das aus vier Mitgliedern (mit jeweils zwei Peers), Zertifizierungsstellen, die für das Verteilen kryptografischer Identitätsmaterialien zuständig sind, und einem Anordnungsservice, der Richtlinien und Netzteilnehmer definiert, besteht. Der blaue Kanal enthält alle vier Netzmitglieder und der gelbe Kanal ist auf drei Mitglieder beschränkt: die Banken B, C und D. Außerdem ist zu erkennen, dass Bank A die Rolle des Netzinitiators hat und Bank D nur als Mitglied im Kontext des gelben Kanals vorhanden ist. Schließlich kann ein Benutzer oder eine Anwendung mit einem ordnungsgemäß signierten x509-Zertifikat Aufrufe an Peers im Netz senden.

![Blockchain-Netz](images/blockchain_network_2-01.png "Blockchain-Beispielnetz")

*Abbildung 1. Beispiel für ein Blockchain-Netz mit vier Mitgliedern, die Kanäle zum Isolieren von Daten nutzen*

## Anwendungen für die Ausführung im Netz entwickeln

Sie können die Blockchain-Entwicklung kennenlernen und beschleunigen und dabei von den Best Practices aus mehr als 400 Kundenprojekten (Client-Engagements) profitieren:

* Gemeinsame Ausrichtung über Geschäftsbereiche hinweg - mit Technologie, die die Blockchain-Anwendungsentwicklungszeiten erheblich reduziert (in bestimmten Fällen kann die Entwicklungszeit von sechs Wochen auf weniger als zwei Tage verringert werden).
* Rascher Aufbau von Blockchain-Know-how unter Ihrem bestehenden Stamm von Programmierern durch die Nutzung der {{site.data.keyword.blockchainfull_notm}}-Entwicklertools.
* Flexibler Ansatz, bei dem die Entwickler in ihrer bevorzugten Umgebung Know-how aufbauen und Anwendungen entwickeln und dazu ein offenes und modernes Toolset verwenden können.

Als Unternehmer können Sie Ihren Anwendungsfall mit Unterstützung der zahlreichen Branchen- und Blockchain-Experten von IBM bearbeiten, die über die {{site.data.keyword.blockchainfull_notm}} Garage erreichbar sind und die dort zusammenarbeiten, um das ganze Potenzial der {{site.data.keyword.blockchainfull_notm}} Platform auszuschöpfen.

Als Entwickler können Sie Geschäftsanforderungen schnell und einfach umsetzen und die Blockchain-Anwendungsentwicklung in der {{site.data.keyword.blockchainfull_notm}} Platform-Netzumgebung beschleunigen, indem Sie eine interaktive Playground-Umgebung zum Entwickeln, Iterieren und Testen von Unternehmensnetzen verwenden. Mit diesen Tools können Sie in Ihrer bevorzugten Umgebung Geschäftsregeln in Unternehmensnetzcode umwandeln:

* **Online erkunden** - Verwenden Sie [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](/docs/services/blockchain/develop.html)<!--, which is powered by an open source development tool-->, um zentrale Blockchain-Konzepte kennenzulernen, Netzdefinitionen zu erstellen sowie die Vorteile von wiederverwendbaren Branchenmodellen und von Smart-Contract-Bibliotheken zu nutzen.

  Nachdem Sie Ihr Unternehmensnetz entwickelt haben, können Sie es in einem Live-Netz bereitstellen, das unter {{site.data.keyword.blockchainfull_notm}} Platform ausgeführt wird. Weitere Informationen finden Sie unter [Unternehmensnetze mit dem Starter Plan bereitstellen](/docs/services/blockchain/develop_starter.html) und [Unternehmensnetze mit dem Enterprise Plan bereitstellen](/docs/services/blockchain/develop_enterprise.html).

* **Lokal installieren** - Mit Hyperledger Fabric können Sie Entwicklung und Tests direkt auf Ihrem Laptop ausführen. Weitere Informationen finden Sie im Abschnitt über die [Erstellung Ihres ersten Netzes](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html).

* **In einer Cloudumgebung zusammenarbeiten** - Verwenden Sie die sofort einsatzfähigen Live-Netze mit Starter Plan- und Enterprise Plan-Optionen, um Ihren Code zusammen mit anderen zu entwickeln und gemeinsam zu nutzen. Weitere Informationen finden Sie in den [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html) und den [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html).

## {{site.data.keyword.IBM_notm}} Support

{{site.data.keyword.IBM_notm}} bietet verschiedene Supportoptionen für von {{site.data.keyword.IBM_notm}} implementierte Blockchain-Lösungen. Weitere Informationen zum {{site.data.keyword.blockchainfull_notm}}-Support finden Sie unter [Support anfordern](/docs/services/blockchain/ibmblockchain_support.html).


## Starter Plan und Enterprise Plan
{: #starter-enterprise}

Mit dem Starter Plan und dem Enterprise Plan können Sie dezentrale Blockchain-Netze mit einem sofort einsatzbereiten und sicheren Service bereitstellen und betreiben. Beim **Starter Plan** handelt es sich um eine Lern- und Einstiegsumgebung für die Entwicklung von Blockchain-Netzen. Der **Enterprise Plan** stellt eine Produktionsumgebung mit hohem Sicherheits- und Unterstützungsniveau dar. Starten Sie mit einer überschaubaren Lösung und passen Sie Ihr Netz flexibel an höhere Mitgliederzahlen und Transaktionsvolumen an, indem Sie folgende Features verwenden:

* Umgebung mit sehr hohem Sicherheitsniveau und mit zahlreichen Sicherheitsfeatures für Hardware, Firmware und Software.
* Robuste Architektur für Skalierbarkeit, Ausfallsicherheit und Verfügbarkeit.
* Optimierte Leistung und Ausführung auf den weltweit schnellsten Linux-Rechenressourcen.

Der Betrieb Ihres Netzes auf der {{site.data.keyword.blockchainfull_notm}} Platform umfasst Tools und Funktionen zur Vereinfachung administrativer Aufgaben:

* Dashboards für die Überwachung und das Management der Ressourcen im Netz.
* Nahtlose Upgrades des vollständigen Code-Stacks.
* Technische Unterstützung rund um die Uhr (24x7) - im Portal integriert.
* Robuster Sicherheitsstack ohne Zugriffsprivilegien, mit Malware- und Manipulationsschutz, 100-prozentiger Verschlüsselung und vielen weiteren Funktionen für Netze mit sensiblen Daten in regulierten Branchen.

Der Starter Plan und der Enterprise Plan sind Plattformservices unter {{site.data.keyword.cloud_notm}}; beide Pläne unterliegen den [{{site.data.keyword.cloud_notm}} Services-Bedingungen![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "{{site.data.keyword.cloud_notm}} Services-Bedingungen") der Service-Level-Agreements (SLAs). Beachten Sie hierbei, dass sowohl Starter Plan- als auch Enterprise Plan-Netze in **mehreren Umgebungen** in geografisch getrennten Rechenzentren bereitgestellt werden.

Weiterführende Angaben finden Sie in den [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html) und den [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html). Wenn Sie startbereit sind, registrieren Sie sich jetzt für Ihre [IBP Starter- oder Enterprise-Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) on {{site.data.keyword.cloud_notm}}!

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-for-icp}

{{site.data.keyword.blockchainfull_notm}} Platform for ICP liefert die Komponenten, die Sie für die Ausführung eines Blockchain-Netzes in Ihrer eigenen Infrastruktur über ICP benötigen. Zu den Komponenten gehören Hyperledger Fabric, eine Zertifizierungsstelle, ein Anordnungsknoten sowie ein Peer, die Sie mithilfe von Kubernetes-Helm-Diagrammen bereitstellen, verwalten und konfigurieren. **Dieses Angebot richtet sich an Kunden, die bereits hinlängliche Erfahrung mit Hyperledger Fabric besitzen.**

IBP for ICP ermöglicht die Bereitstellung von Blockchain-Netzen in einer privaten Cloud, um Anforderungen für den Datenspeicherort, marktrelevante Verordnungen und Infrastrukturvorgaben abzudecken. Die Bereitstellung der wesentlichen Elemente eines Blockchain-Netzes in Ihrer eigenen Infrastruktur wird hierbei durch {{site.data.keyword.cloud_notm}} Private vereinfacht, einer Plattform für auf Kubernetes basierende Anwendungen zur Entwicklung und Verwaltung von lokalen containerisierten Anwendungen.

 * Kunden können IBP-Netze mit ihrer eigenen Infrastruktur verwalten. Die kostenlose Community Edition können Kunden in ihren eigenen isolierten und sicheren Umgebungen ausführen; allerdings wird hier kein Support bereitgestellt.
 * Fabric kann von Kunden unter Kubernetes mithilfe von Helm-Diagrammen und der ausführlichen Dokumentation für den Betrieb konfiguriert werden.
 * Mit Ausnahme der Community Edition sind Kunden für die erweiterte technische Unterstützung (Advanced Technical Support) berechtigt.

 Weitere Angaben über IBP for ICP enthält der Abschnitt [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html).

## {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #ibp-for-aws}

Mit {{site.data.keyword.blockchainfull_notm}} Platform for AWS können Peers das Verbindungsprofil, die Hyperledger Fabric-Zertifizierungsstellen und den Anordnungsservice eines vorhandenen Starter Plan- oder Enterprise Plan-Netzes in {{site.data.keyword.cloud_notm}} nutzen, um Transaktionen mithilfe einer AWS-Schnelleinstiegsvorlage zu verarbeiten. Der Schnelleinstieg ermöglicht Ihnen die Bereitstellung von Peers unter Verwendung von AWS CloudFormation-Vorlagen. Diese Vorlage ist für Entscheidungsträger und Systemadministratoren der IT-Infrastruktur geeignet, die von AWS gehostete und mit einem Starter Plan- oder Enterprise Plan-Netz verbundenen IBP-Peers zeitnah konfigurieren, bereitstellen und ausführen wollen. Mithilfe der Vorlage können Sie eine neue virtuelle private Cloud (VPC) in AWS erstellen oder den Peer in einer vorhandenen VPC bereitstellen.

Der Schnelleinstieg konfiguriert Folgendes:
 * Hochverfügbarkeitsarchitektur, die sich auf zwei Verfügbarkeitszonen erstreckt.
 * Mit öffentlichen Teilnetzen gemäß den Best Practice für AWS konfigurierte VPC. Hierdurch erhalten Sie ein eigenes virtuelles Netz in AWS.
 * Internet-Gateway für den Zugriff auf das Internet.
 * Zwei Peers in zwei Verfügbarkeitszonen in den öffentlichen Teilnetzen (1 Peer pro Teilnetz).
 * Ein Peer-Container in jedem öffentlichen Teilnetz mit entweder einer integrierten LevelDB-Datenbank oder einem sekundären CouchDB-Container.

Weitere Angaben über IBP for AWS enthält der Abschnitt [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer.html).

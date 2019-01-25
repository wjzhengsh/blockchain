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

# Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services
{: #remote-peer-overview}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**Hinweis:** Das Programm für die Betaversion von {{site.data.keyword.blockchainfull}} Platform Remote Peer on ICP wurde eingestellt. Wenn Sie weiterhin Peers in Ihrer ICP-Umgebung ausführen wollen, verwenden Sie stattdessen das Angebot **{{site.data.keyword.blockchainfull_notm}} Platform for ICP**. Weitere Angaben enthält der Abschnitt [Informationen zu Peers in ICP](../ibp-for-icp-about.html#ibp-icp-peer).

Sie können den Peer von {{site.data.keyword.blockchainfull_notm}} Platform in der AWS-Cloud ausführen, nachdem Sie dafür eine Verbindung zu einem vorhandenen Blockchain-Netz in {{site.data.keyword.cloud_notm}} hergestellt haben. Die Ausführung von Peers außerhalb von {{site.data.keyword.cloud_notm}} bietet ein höheres Maß an Flexibilität in Bezug auf die Vergrößerung eines Blockchain-Netzes sowie in Bezug auf den Beitritt zu einen Blockchain-Netz, während gleichzeitig die Vorteile eines bereits vorhandenen Netzes in {{site.data.keyword.cloud_notm}} genutzt werden können. Peers in der AWS-Cloud nutzen die Zertifizierungsstellen (CAs) und den Anordnungsservice der Plattform, ermöglichen Ihnen jedoch die gemeinsame Verwendung Ihres Peers mit anderen Anwendungen außerhalb von {{site.data.keyword.cloud_notm}}. {:shortdesc}


## Wichtige Hinweise
{: #remote-peer-limitations}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS verfügt nicht über Zugriff auf die vollständige Funktionalität oder Unterstützung von Peers, die auf der {{site.data.keyword.blockchainfull_notm}}-Plattform gehostet werden. Stellen Sie vor der Ausführung von {{site.data.keyword.blockchainfull_notm}} Platform for AWS sicher, dass Sie die folgenden Rahmenbedingungen und Einschränkungen verstehen:
- Peers, die in anderen Cloudumgebungen ausgeführt werden, sind in der Network Monitor-Instanz des Blockchain-Netzes unter {{site.data.keyword.cloud_notm}} nicht sichtbar.
- Peers, die in {{site.data.keyword.blockchainfull_notm}} Platform for AWS ausgeführt werden, können nicht über die Swagger-Benutzerschnittstelle in der Benutzerschnittstelle des Network Monitor aufgerufen werden.
- Sie sind für das Management der Statusüberwachung, der Sicherheits- und Protokollierungsfunktionen sowie der Ressourcennutzung Ihrer Peerknoten von {{site.data.keyword.blockchainfull_notm}} Platform for AWS verantwortlich.
- Sie können Ihre Peers von {{site.data.keyword.blockchainfull_notm}} Platform for AWS nur mit Blockchain-Netzen verbinden, die mit Fabric Version 1.1 oder 1.2.1 arbeiten. Ihre Fabric-Version können Sie ermitteln, indem Sie das [Fenster "Netzvorgaben"](../v10_dashboard.html#network-preferences) im Network Monitor öffnen.
- Der Datenbanktyp des Peers von {{site.data.keyword.blockchainfull_notm}} Platform for AWS muss mit dem Datenbanktyp des Blockchain-Netzes (entweder LevelDB oder CouchDB) übereinstimmen.
- Die CouchDB-Fauxton-Schnittstelle steht auf dem AWS-Peer nicht zur Verfügung.
- [Gossip](../glossary.html#gossip) für AWS-Peers wird momentan nicht unterstützt. Dies impliziert, dass Fabric-Funktionen, die von Gossip abhängig sind (z. B. [private Daten ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "Private Data") und [Serviceerkennung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Service discovery") ebenfalls nicht unterstützt werden.

## Voraussetzungen
{: #remote-peer-prereq}

Zur Nutzung eines Peers von {{site.data.keyword.blockchainfull_notm}} Platform for AWS benötigen Sie eine Organisation, die Mitglied eines Starter Plan- oder Enterprise Plan-Netzes unter {{site.data.keyword.blockchainfull_notm}} Platform ist. Der Peer von {{site.data.keyword.blockchainfull_notm}} Platform for AWS nutzt die API-Endpunkte, Hyperledger Fabric-CAs und den Anordnungsservice des {{site.data.keyword.blockchainfull_notm}} Platform-Netzes zum Betrieb. Wenn Sie nicht Mitglied eines Blockchain-Netzes sind, dann müssen Sie entweder ein Netz erstellen oder einem Netz beitreten. Weitere Informationen finden Sie im Abschnitt zum [Erstellen eines Netzes](../get_start.html#creating-a-network) oder [Teilnehmen an einem Netz](../get_start.html#joining-a-network).

## Lizenz und Preisstruktur
{: #remote-peer-license-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform for AWS wird gegenwärtig als kostenlose Community Edition angeboten. Künftig ändert sich IBP for AWS jedoch möglicherweise in ein Bring-Your-Own-License-Modell (BYOL-Modell), das den Kauf einer Lizenz von IBM erforderlich macht.

**Hinweis:** Zum Betrieb eines AWS-Peers benötigen Sie eine Organisation, die zu einem Starter Plan- oder Enterprise Plan-Netz unter {{site.data.keyword.blockchainfull_notm}} Platform gehört. Dies hat zur Folge, dass Sie selbst oder ein anderes Mitglied des Netzes die {{site.data.keyword.blockchainfull_notm}}-[Mitgliedsgebühr](pricing.html#key-elements-of-pricing) für Ihre Organisation bezahlen müssen. Weitere Informationen zur Zahlung von Gebühren finden Sie im Abschnitt zum [Zahlungsmodus](paying_mode.html).


## AWS-Peer bereitstellen
{: #deploy-remote-peer}

Mit der [Schnelleinstiegsvorlage ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/ "Schnelleinstiegsvorlage") für AWS können Sie {{site.data.keyword.blockchainfull_notm}} Platform for AWS ohne großen Aufwand bereitstellen. Weitere Informationen finden Sie im Dokument [{{site.data.keyword.blockchainfull_notm}} Platform for AWS Quick Start Deployment Guide ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://s3.amazonaws.com/aws-quickstart/quickstart-ibm-fabric/doc/ibm-blockchain-platform-for-aws.pdf "IBM Blockchain Platform for AWS Quick Start Reference Deployment").

Anweisungen zur Bereitstellung von {{site.data.keyword.blockchainfull_notm}} Platform for AWS enthält der Abschnitt [Peers in Amazon Web Services bereitstellen](remote_peer_aws.html "Peers in Amazon Web Services bereitstellen").

Das folgende Diagramm beschreibt den Prozess, der zum Bereitstellen eines Peers von {{site.data.keyword.blockchainfull_notm}} Platform for AWS erforderlich ist.

<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_AWS_flow.png" width="750" alt="Klicken Sie auf ein Feld, um weitere Details über den Prozess zu erhalten." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="AWS konfigurieren oder darauf zugreifen" title="Konfigurieren oder zugreifen" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Schlüsselpaar erstellen" title="Schlüsselpaar erstellen" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="remote_peer_aws.html#prerequisites-aws" alt="Netz erstellen oder daran teilnehmen" title="Netz erstellen oder daran teilnehmen" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="An Kanal teilnehmen" title="An Kanal teilnehmen" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="remote_peer_aws.html#aws-register-peer" alt="Peeridentität registrieren" title="Peeridentität registrieren" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="remote_peer_aws.html#aws-network-endpoints" alt="Peerkonfigurationsdaten abrufen" title="Peerkonfigurationsdaten abrufen" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Auf Link klicken" title="Auf Link klicken" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Peerinstanzen konfigurieren" title="Peerinstanzen konfigurieren" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-test" alt="Bereitstellung prüfen" title="Bereitstellung prüfen" shape="rect" coords="443.95, 258.43, 570.53, 333.48" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Fabric-SDK verwenden" title="Fabric-SDK verwenden" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="remote_peer_operate_aws.html#aws-peer-cli-operate" alt="Befehlszeilenschnittstelle für Fabric-Tools verwenden" title="Befehlszeilenschnittstelle für Fabric-Tools verwenden" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*Abbildung 1. Bereitstellungsablauf für {{site.data.keyword.blockchainfull_notm}} Platform for AWS unter AWS*


## AWS-Peer betreiben
{: #operate-remote-peer}

Nach der Bereitstellung des Peers müssen Sie verschiedene operative Schritte ausführen, bevor Ihr Peer Transaktionen an das Netz übergeben kann. Die operativen Schritte umfassen das Hinzufügen Ihrer Organisation zu einem Kanal, das Hinzufügen Ihres Peers zum Kanal, das Installieren des Chaincodes auf dem Peer, die Instanziierung des Chaincodes auf dem Kanal und die Verbindung von Anwendungen mit dem Peer. Weitere Informationen enthält der Abschnitt [Peers in Amazon Web Service betreiben](remote_peer_operate_aws.html#remote-peer-operate-aws).

## Datenspeicherort
{: #data-residency}

Da Blockchain-Netze dem Typ der verarbeiteten Daten keine Beachtung schenken, müssen bisweilen zusätzliche Schritte ausgeführt werden, damit bestimmte Arten von Daten geschützt bleiben. Die gängigste Anforderung zum Datenspeicherort besteht in Bezug auf die Gesetzgebung einiger Länder, die vorgibt, dass alle Daten, die in einem IT-System verarbeitet und gespeichert werden, innerhalb der Grenzen eines bestimmten Landes verbleiben müssen. Ähnlich wird von bestimmten Unternehmen in stark regulierten Branchen (z. B. Behörden, Gesundheitswesen und Finanzdienstleistungen) vorgegeben, dass Daten vollständig hinter einer Firewall gespeichert werden müssen. Zur Erfüllung der Kriterien für den Datenspeicherort müssen alle Komponenten des Blockchain-Netzes deshalb Teil desselben [Kanals](../glossary.html#channel) sein und innerhalb der Grenzen eines bestimmten Landes gespeichert werden.

Zur Erfüllung der Anforderungen für den Datenspeicherort sollten Sie sich mit der Hyperledger Fabric-Architektur vertraut machen, die die Grundlage von {{site.data.keyword.blockchainfull_notm}} Platform bildet. Die Architektur basiert auf den drei folgenden Schlüsselkomponenten: Zertifizierungsstelle (CA), Anordnungsknoten und Peer. Ein Peer empfängt angeordnete Statusaktualisierungen in Form von Blöcken vom Anordnungsservice und verwaltet den Status und das Ledger. Aus diesem Grund besteht eine direkte Verbindung zwischen einem Peer und einem Anordnungsknoten. Das Ledger enthält die aktuellsten Werte für alle Schlüssel und die Daten, die in den Transaktionsprotokollen enthalten sind.

Darüber hinaus verwenden Clientanwendungen die [Fabric-SDKs](../v10_application.html#using-the-fabric-sdks) zum Senden von Transaktionen an die Peers und Anordnungsservices. Diese Transaktionen umfassen die Daten für den [Lese-/Schreibsatz ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Read-Write set semantics"), der die Schlüssel/Wert-Paare des Ledgers enthält.

Wenn der landesinterne Datenspeicherort eine Anforderung für Ihr Unternehmen darstellt, dann müssen sich der Anordnungsknoten, die Peers und die Clientanwendungen innerhalb desselben Landes befinden. Wenn ein {{site.data.keyword.blockchainfull_notm}} Platform-Netz in {{site.data.keyword.cloud_notm}} erstellt wird, dann haben Sie die Möglichkeit, einen Standort für Ihr Netz auszuwählen. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Weitere Informationen zu Regionen und Standorten finden Sie im Abschnitt zu den [{{site.data.keyword.blockchainfull_notm}} Platform-Regionen und -Standorten](../reference/ibp_regions.html). Zur Erfüllung der Kriterien für den Datenspeicherort in einem dieser Länder muss Ihr Peer sich in demselben Land wie das {{site.data.keyword.blockchainfull_notm}} Platform-Netz befinden.

### Anwendungsfall für Datenspeicherort

Zur Veranschaulichung wird ein {{site.data.keyword.blockchainfull_notm}} Platform-Netz angenommen, das den Anordnungsknoten und die Zertifizierungsstelle zusammen mit einem Konsortium aus vier Organisationen enthält. Die Organisationen besitzen einen oder mehrere Peerknoten. Alle Organisationen sind Bestandteil eines einzigen Kanals und alle Komponenten des Netzes befinden sich in der Region (z. B. Frankfurt), in der das {{site.data.keyword.blockchainfull_notm}} Platform-Netz bereitgestellt wurde. Die Clientanwendungen, die mit den Peers interagieren, befinden sich ebenfalls in Deutschland. Die Kriterien für den Datenspeicherort werden erfüllt.  

![Datenspeicherort mit allen Komponenten in demselben Land](../images/remote_peer_data_res_1.png "Datenspeicherort mit allen Komponenten in demselben Land")
  
*Abbildung 3. Datenspeicherort mit allen Komponenten in demselben Land*

Die Teilnahme eines **Peers** an einer der Organisationen hat nun bestimmte Auswirkungen. Ein Peer kann sich in derselben Region wie der Rest des Netzes oder an einem beliebigen anderen Standort außerhalb der IBP-Netzregion befinden:

-	Wenn sich der Peer in demselben Land wie das übrige Netz befindet, werden die Kriterien für den Datenspeicherort erfüllt. Alle Ledger-Daten verbleiben wie in **Abbildung 3** oben in Deutschland.
-	Wenn sich der Peer in einem anderen Land (z. B. den USA) befindet, werden die Kriterien für den Datenspeicherort nicht mehr erfüllt, da die Daten im Peer-Ledger außerhalb der Landesgrenzen gemeinsam genutzt werden.

Zur Lösung dieses Problems können Daten mithilfe von **Kanälen** für eine Untergruppe von Peers im Netz isoliert werden. Wenn das {{site.data.keyword.blockchainfull_notm}} Platform-Netz Peers und Anordnungsknoten länderübergreifend enthält, können Ledger-Daten mittels Kanälen von Organisationen mit Peers, die sich außerhalb der Landesgrenze befinden, isoliert werden.  

**Hinweis:** Anordnungsknoten befinden sich immer in dem Rechenzentrum, das Sie zum Hosten des Netzes ausgewählt haben. Es ist nicht möglich, mehrere Anordnungsknoten zu definieren, die sich außerhalb der Landesgrenzen befinden. Peers können sich jedoch entweder in dem Rechenzentrum oder an einem fernen Standort außerhalb von {{site.data.keyword.cloud_notm}} befinden.

![Datenspeicherort mit Peers außerhalb des Landes der IBM Blockchain Platform-Region](../images/remote_peer_data_res_2.png "Datenspeicherort mit Peers außerhalb des Landes der IBM Blockchain Platform-Region")  
*Abbildung 4. Datenspeicherort mit Peers außerhalb des Landes der IBM Blockchain Platform-Region*

In **Abbildung 4** ist der Datenspeicherort für `OrgC` und `OrgD` nicht erforderlich. `OrgD` enthält jetzt tatsächlich die Peers `OrgD-peer1` und `OrgD-peer2`, die sich in den *Vereinigten Staaten* befinden. Damit `OrgA`, `OrgB` und ihre jeweiligen Clientanwendungen und Peers, die sich in Deutschland befinden, die Ledger-Daten im Kanal `X` isolieren können, wird daher ein neuer Kanal `Y` für `OrgC` und `OrgD` erstellt.

Zum besseren Verständnis des Datenflusses im {{site.data.keyword.blockchainfull_notm}} Platform-Netz lesen Sie die [Fabric-Dokumentation zum Transaktionsfluss![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Transaction Flow").

In Zukunft wird durch neue Hyperledger Fabric-Technologien die Möglichkeit zur Erschließung weiterer Datenspeicherorte verbessert. Hierbei werden [private Datensammlungen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Private data collections") und der sogenannte "Zero-Knowledge-Beweis" (kenntnisfreier Beweis) zum Einsatz kommen.

- Eine private Datensammlung stellt sicher, dass die privaten Daten nur im Peer-to-Peer-Bereich (über das Gossip-Protokoll) gemeinsam genutzt werden und dass nur die entsprechend berechtigten Peers sie anzeigen können. Dies sind z. B. Peers, die sich innerhalb der Grenzen eines bestimmten Landes befinden. Die Daten werden in einer privaten Datenbank auf dem Peer gespeichert.  Der Anordnungsservice ist hier nicht eingebunden und kann die privaten Daten nicht anzeigen. Ein Hashwert dieser Daten wird auf die Ledger aller Peers im Kanal geschrieben. Der Hashwert, der für die Statusvalidierung verwendet wird, dient als Nachweis der Transaktion und kann zu Prüfzwecken verwendet werden. Private Daten sind für Netze unter {{site.data.keyword.blockchainfull_notm}} Platform verfügbar, die mit Fabric Version 1.2.1 ausgeführt werden. Die Funktion für private Daten ist jedoch für Peers nicht verfügbar.

- Bei einem Zero-Knowledge-Beweis überzeugt ein sogenannter Beweiser (Prover) einen Verifizierer (Verifier) davon, dass er einen bestimmten geheimen Schlüssel (Secret) kennt, ohne dabei den geheimen Schlüssel selbst preiszugeben. Diese Methode eröffnet eine Möglichkeit, um zu zeigen, dass Sie über Kenntnisse verfügen, die eine bestimmte Aussage erfüllen, ohne offenzulegen, um welche Informationen es sich dabei handelt.

Weitere Informationen zu diesen Technologien finden Sie im Whitepaper zu den [privaten und vertraulichen Transaktionen mit Hyperledger Fabric ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Private und vertrauliche Transaktionen mit Hyperledger Fabric").

## Support anfordern
{: #remote-peer-support}

IBM Blockchain Platform bietet keinen Support für dieses Angebot. Falls Sie im Zusammenhang mit Ihrem Peer Probleme feststellen, können Sie frei zugängliche Ressourcen- und Unterstützungsforen für Blockchainentwickler nutzen und Hilfe von {{site.data.keyword.IBM_notm}} und der Fabric-Community erhalten. Weitere Informationen finden Sie unter [Ressourcen- und Unterstützungsforen für Blockchain](../ibmblockchain_support.html#resources). Außerdem können Sie die Unterstützungsressourcen in der Anzeige **Hilfe anfordern** im Network Monitor anzeigen.

- Bei Problemen im Zusammenhang mit AWS können Sie sowohl die [Community-Unterstützungsforen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://forums.aws.amazon.com/index.jspa "Unterstützungsforen der AWS-Community") als auch den [AWS Premium Support ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://aws.amazon.com/premiumsupport/ "AWS Premium Support") nutzen.

{{site.data.keyword.blockchainfull_notm}} unterstützt keine Fälle, die in {{site.data.keyword.cloud_notm}} geöffnet werden und sich auf {{site.data.keyword.blockchainfull_notm}} Platform for AWS beziehen. Community Edition ist für Erkundung, Entwicklung und Tests gedacht und nicht für die Produktion zu verwenden. 

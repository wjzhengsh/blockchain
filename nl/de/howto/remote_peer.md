---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Informationen zu fernen Peers
{: #remote-peer-overview}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Sie können ferne Peers von {{site.data.keyword.blockchainfull}} Platform unter {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> ausführen, nachdem Sie eine Verbindung dieser Peers zu einem vorhandenen Blockchain-Netz hergestellt haben. Die Ausführung von Peers außerhalb von {{site.data.keyword.cloud_notm}} bietet ein höheres Maß an Flexibilität in Bezug auf die Vergrößerung eines Blockchain-Netzes sowie in Bezug auf den Beitritt zu einen Blockchain-Netz, während gleichzeitig die Vorteile eines bereits vorhandenen Netzes in {{site.data.keyword.cloud_notm}} genutzt werden können. Ferne Peers nutzen die Zertifizierungsstellen (CAs) und den Anordnungsservice der Plattform, ermöglichen Ihnen jedoch die gemeinsame Verwendung Ihres Peers mit anderen Anwendungen außerhalb von {{site.data.keyword.cloud_notm}}.
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} Private (ICP) ist eine Kubernetes-basierte Plattform für die Erstellung einer privaten Cloud in einer lokalen Umgebung (On-Premises). Sie können ICP verwenden, um einen fernen Peer auszuführen und den fernen Peer mit einem Blockchain-Netz unter {{site.data.keyword.blockchainfull_notm}} Platform zu verbinden. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP nutzt den Speicher, die Sicherheits- und Protokollierungsfunktionen sowie die Unterstützungsservices von ICP, sodass Sie Ihre fernen Peers in der eigenen lokalen Umgebung nutzen können. Weitere Informationen zu ICP finden Sie in der [{{site.data.keyword.cloud_notm}} Private-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-Dokumentation").  

**Hinweis:** {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer wird momentan als Betaversion angeboten, die sich zu Evaluierungs- und Versuchszwecken eignet. **Diese Betaedition ist nicht für den Einsatz in Produktionsumgebungen vorgesehen.** Informationen zum Zugriff auf diese Version und weiterführende Informationen finden Sie im Abschnitt zur [Lizenz und Preisstruktur](#remote-peer-license-pricing).

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

Die Betaedition unterstützt die Cloudplattform von {{site.data.keyword.cloud_notm}} Private (ICP), v2.1.0.3. Beachten Sie hierbei, dass das Netz unter {{site.data.keyword.blockchainfull_notm}} Platform und die fernen Peerknoten in ICP beide mit der identischen **Fabric Version 1.1** arbeiten müssen.

## Wichtige Hinweise
{: #remote-peer-limitations}

Ein ferner Peer verfügt nicht über Zugriff auf die vollständige Funktionalität oder Unterstützung von Peers, die auf der {{site.data.keyword.blockchainfull_notm}}-Plattform gehostet werden. Stellen Sie vor der Ausführung ferner Peers sicher, dass Sie die folgenden Rahmenbedingungen und Einschränkungen verstehen:
- Ferne Peers, die in anderen Cloudumgebungen ausgeführt werden, sind im Network Monitor des Blockchain-Netzes unter {{site.data.keyword.cloud_notm}} nicht sichtbar.
- Ferne Peers können nicht über die Swagger-Benutzerschnittstelle in der Benutzerschnittstelle des Network Monitor aufgerufen werden.
- Sie sind für das Management der Statusüberwachung, der Sicherheits- und Protokollierungsfunktionen sowie der Ressourcennutzung Ihrer fernen Peers verantwortlich.
- Sie können ferne Peers nur mit Blockchain-Netzen verbinden, die mit Hyperledger Fabric v1.1 arbeiten.
- Der Datenbanktyp des fernen Peers muss mit dem Datenbanktyp des Blockchain-Netzes (entweder LevelDB oder CouchDB) übereinstimmen.
- Die CouchDB-Fauxton-Schnittstelle steht auf dem fernen Peer nicht zur Verfügung.
- [Gossip](../glossary.html#gossip) für ferne Peers wird momentan nicht unterstützt.


## Voraussetzungen
{: #remote-peer-prereq}

Zur Nutzung eines fernen Peers benötigen Sie eine Organisation, die Mitglied eines Starter Plan- oder Enterprise Plan-Netzes unter {{site.data.keyword.blockchainfull_notm}} Platform ist. Der ferne Peer nutzt die API-Endpunkte, Hyperledger Fabric-CAs und den Anordnungsservice des {{site.data.keyword.blockchainfull_notm}} Platform Plan-Netzes zum Betrieb. Wenn Sie nicht Mitglied eines Blockchain-Netzes sind, dann müssen Sie entweder ein Netz erstellen oder einem Netz beitreten. Weitere Informationen finden Sie im Abschnitt zum [Erstellen eines Netzes](../get_start.html#creating-a-network) oder [Teilnehmen an einem Netz](../get_start.html#joining-a-network).


## Lizenz und Preisstruktur
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->Um auf ferne Peers zuzugreifen, um sie unter ICP auszuführen, sollten Sie sich mit den Informationen zur [Lizenz und Preisstruktur für ferne Peers unter ICP](remote_peer_icp.html#license-pricing-icp "Lizenz und Preisstruktur für ferne Peers unter ICP") vertraut machen. Die Lizenzen für die Betaedition ferner Peers sind kostenlos.<!--[AWS] for both platforms.--> Später kann die Betaedition durch ein Angebot mit allgemeiner Verfügbarkeit (GA = General Availability) ersetzt werden.

Die Migration von der Betaedition auf die GA-Version wird nicht unterstützt. Sie müssen neue ferne Peers herunterladen und installieren, um das GA-Angebot nach dessen Freigabe zu nutzen. Anschließend können Sie neue ferne Peers denselben Kanälen desselben Netzes hinzufügen, zu denen auch die Betaversionen Ihrer fernen Peers gehören.

**Hinweis:** Zum Betrieb eines fernen Peers benötigen Sie eine Organisation, die zu einem Starter Plan- oder Enterprise Plan-Netz unter {{site.data.keyword.blockchainfull_notm}} Platform gehört. Dies hat zur Folge, dass Sie selbst oder ein anderes Mitglied des Netzes die [Mitgliedschaftsgebühr](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) für IBM Blockchain für Ihre Organisation bezahlen müssen. Weitere Informationen zur Zahlung von Gebühren finden Sie im Abschnitt zum [Zahlungsmodus](paying_mode.html).  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## Fernen Peer bereitstellen
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

Das folgende Diagramm enthält eine Beschreibung der Schritte, die zum Bereitstellen eines fernen Peers von {{site.data.keyword.blockchainfull_notm}} Platform unter ICP erforderlich sind. Weitere Informationen zur Vorgehensweise bei der Bereitstellung ferner Peers unter ICP finden Sie im Abschnitt zum [Bereitstellen ferner Peers in {{site.data.keyword.cloud_notm}} Private](remote_peer_icp.html "Bereitstellen ferner Peers in {{site.data.keyword.cloud_notm}} Private").

![Bereitstellungsablauf ferner Peers unter ICP](../images/remote_peer_ICP_flow.png "Bereitstellungsablauf ferner Peers unter ICP")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*Abbildung 1. Bereitstellungsablauf ferner Peers unter ICP*


## Betrieb eines fernen Peers ausführen
{: #operate-remote-peer}

Nach der Bereitstellung des fernen Peers müssen Sie verschiedene operative Schritte ausführen, bevor Ihr Peer Transaktionen an das Netz übergeben kann. Die operativen Schritte umfassen das Hinzufügen Ihrer Organisation zu einem Kanal, das Hinzufügen Ihres fernen Peers zum Kanal, das Installieren des Chaincodes auf dem fernen Peer, die Instanziierung des Chaincodes auf dem Kanal und die Verbindung von Anwendungen mit dem fernen Peer. Weitere Informationen finden Sie im Abschnitt zum <!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[Betrieb ferner Peers in {{site.data.keyword.cloud_notm}} Private](remote_peer_operate_icp.html#remote-peer-operate).

## Datenspeicherort
{: #data-residency}

Die Anforderungen an den Datenspeicherort beziehen sich auf die Einschränkungen, die in Bezug auf den Standort gelten, an dem Daten gespeichert werden können. Die gängigste Anforderung zum Datenspeicherort besteht in Bezug auf die Gesetzgebung einiger Länder, die vorgibt, dass alle Kundendaten, die in einem IT-System verarbeitet und gespeichert werden, innerhalb der Grenzen eines bestimmten Landes verbleiben müssen. Ähnlich wird von bestimmten Unternehmen in stark regulierten Branchen (z. B. Behörden Gesundheitswesen und Finanzdienstleistungen) vorgegeben, dass alle Daten vollständig hinter einer Firewall gespeichert werden müssen. Zusätzlich hierzu verfügen bestimmte Unternehmen über eine Unternehmensrichtlinie, die vorgibt, dass bestimmte Datentypen (normalerweise personenbezogene Daten) vertraulich behandelt und vollständig hinter der Unternehmensfirewall gespeichert werden müssen. Zur Erfüllung der Kriterien für den Datenspeicherort müssen alle Komponenten des Blockchain-Netzes deshalb Teil desselben [Kanals](../glossary.html#channel) sein und innerhalb der Grenzen eines bestimmten Landes gespeichert werden.

Zur Erfüllung der Anforderungen für den Datenspeicherort sollten Sie sich mit der Hyperledger Fabric-Architektur vertraut machen, die die Grundlage von {{site.data.keyword.blockchainfull_notm}} Platform bildet. Die Architektur basiert auf den drei folgenden Schlüsselkomponenten: Anordnungsservice, Zertifizierungsstelle (CA) und Peer. Ein Peer empfängt angeordnete Statusaktualisierungen in Form von Blöcken vom Anordnungsservice und verwaltet den Status und das Ledger. Aus diesem Grund besteht eine direkte Verbindung zwischen einem Peer und einem Anordnungsservice. Das Ledger enthält die aktuellsten Werte für alle Schlüssel und die Daten, die in den Transaktionsprotokollen enthalten sind.

Darüber hinaus verwenden Clientanwendungen die [Fabric-SDKs](../v10_application.html#using-the-fabric-sdks) zum Senden von Transaktionen an die Peers und Anordnungsservices. Diese Transaktionen umfassen die Daten für die [Read-Write-Gruppe ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "Semantik für Read-Write-Gruppen"), die die Schlüssel/Wert-Paare des Ledgers enthält.

Wenn der landesinterne Datenspeicherort eine Anforderung für Ihr Unternehmen darstellt, dann müssen der Anordnungsservice, die Peerknoten und die Clientanwendung innerhalb desselben Landes resident sein. Wenn ein {{site.data.keyword.blockchainfull_notm}} Platform-Netz in {{site.data.keyword.cloud_notm}} erstellt wird, dann haben Sie die Möglichkeit, einen Standort für Ihr Netz auszuwählen. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Weitere Informationen zu Regionen und Standorten finden Sie im Abschnitt zu den [{{site.data.keyword.blockchainfull_notm}} Platform-Regionen und -Standorten](../reference/ibp_regions.html). Zur Erfüllung der Kriterien für den Datenspeicherort in einem dieser Länder muss Ihr ferner Peer sich in demselben Land wie das {{site.data.keyword.blockchainfull_notm}} Platform-Netz befinden.

Wenn das {{site.data.keyword.blockchainfull_notm}} Platform-Netz ferne Peers und Anordnungsserviceknoten außerhalb der Landesgrenzen enthält, dann können Sie Kanäle verwenden, um Daten auf einer Untergruppe von Peers im Netz zu isolieren. Anordnungsknoten befinden sich immer in dem Rechenzentrum, das Sie zum Hosten des Netzes ausgewählt haben. Es ist nicht möglich, Anordnungsknoten zu definieren, die sich außerhalb der Landesgrenzen befinden. Allerdings können Peers sich entweder in dem Rechenzentrum oder an einem fernen Standort außerhalb von {{site.data.keyword.cloud_notm}} befinden. Aus diesem Grund kann zur Erfüllung der Kriterien für den Datenspeicherort ein Kanal erstellt werden, in dem der Anordnungsknoten und alle Peers (lokale Peers des Rechenzentrums oder ferne Peers) sich innerhalb desselben Landes befinden. Auf diese Weise verbleiben alle vom Anordnungsknoten und den Peers gemeinsam genutzten Daten innerhalb der Grenzen eines einzelnen Landes. Andere Kanäle, bei denen sich der Anordnungsknoten und die fernen Peers in mehreren Ländern befinden und die Kriterien für den Datenspeicherort nicht erfüllt werden müssen, können bei Bedarf weiterhin vorhanden bleiben.

In Zukunft wird durch neue Hyperledger Fabric-Technologien die Möglichkeit zur Erschließung weiterer Datenspeicherorte verbessert. Hierbei werden [Private-Datensammlungen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Private-Datensammlungen") und der sog. Zero-Knowledge-Beweis (kenntnisfreier Beweis) zum Einsatz kommen.

- Eine Private-Datensammlung stellt sicher, dass die privaten Daten nur im Peer-to-Peer-Bereich (über das Gossip-Protokoll) gemeinsam genutzt werden und dass nur entsprechend berechtigte Peers sie anzeigen können. Dies sind z. B. Peers, die sich innerhalb der Grenzen eines bestimmten Landes befinden. Die Daten werden in einer privaten Datenbank auf dem Peer gespeichert. Der Anordnungsservice ist hier nicht eingebunden und kann die privaten Daten nicht anzeigen. Ein Hashwert dieser Daten wird auf die Ledger aller Peers im Kanal geschrieben. Der Hashwert, der für die Statusvalidierung verwendet wird, dient als Nachweis der Transaktion und kann zu Prüfzwecken verwendet werden.

- Bei einem Zero-Knowledge-Beweis überzeugt ein sog. Beweiser (Prover) einen Verifizierer (Verifier) davon, dass er ein bestimmtes Geheimnis (Secret) kennt, ohne dabei das Geheimnis selbst preiszugeben. Diese Methode eröffnet eine Möglichkeit, um zu zeigen, dass Sie über Kenntnisse verfügen, die eine bestimmte Aussage erfüllen, ohne offenzulegen, um welche Informationen es sich dabei handelt.

Weitere Informationen zu diesen Technologien finden Sie im Whitepaper zu den [privaten und vertraulichen Transaktionen mit Hyperledger Fabric ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Private und vertrauliche Transaktionen mit Hyperledger Fabric").

## Support anfordern
{: #remote-peer-support}

Die Betaedition des {{site.data.keyword.blockchainfull_notm}} Remote Peer-Angebots wurde für die Exploration, Bereitstellung und für Tests konzipiert. **Verwenden Sie diese Edition nicht in einer Produktionsumgebung.** {{site.data.keyword.blockchainfull_notm}} Platform bietet keine Unterstützung für diese Edition. Wenn Sie Probleme feststellen, die in Zusammenhang mit Ihrem fernen Peer stehen, dann sollten Sie die Informationen im Abschnitt zu den [Blockchain-Ressourcen und zu den Unterstützungsforen](../v10_dashboard.html#support-forums) zurate ziehen. Außerdem können Sie die Unterstützungsressourcen in der Anzeige **Hilfe anfordern** im Network Monitor anzeigen.  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

Bei Problemen, die in Zusammenhang mit {{site.data.keyword.cloud_notm}} Private stehen, bietet ICP [kostenlosen digitalen Support und Community Edition-Support ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "Kostenloser digitaler ICP-Support und Community Edition-Support") an.

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

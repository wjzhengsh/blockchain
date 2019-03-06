---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-11"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Blockchain-Komponenten im Überblick
{: #blockchain-component-overview}

Die Komponenten und Struktur von {{site.data.keyword.blockchainfull}} Platform basieren auf der zugrunde liegenden Infrastruktur und den entsprechenden Tools von [Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/), einer Open-Source-Lösung für genehmigte Blockchains mit {{site.data.keyword.IBM_notm}} als einem der Hauptmitwirkenden. Auf Fabric basierende Netze beinhalten einige Standardkomponenten, die in einer Reihe von Konfigurationen bereitgestellt werden können, um eine Vielzahl von Anwendungsfällen zu unterstützen.

Eine umfassendere Betrachtung von Fabric-Netzen und der Wechselbeziehungen zwischen den Komponenten, aus denen sie bestehen, enthält das [Dokument über die Struktur eines Blockchain-Netzes](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) in der Dokumentation der Fabric-Community, in dem erläutert wird, wie ein Netz aufgebaut und weiterentwickelt werden kann.

Eine allgemeine Übersicht über die Komponenten in einem auf Fabric basierenden Netz bietet das folgende Video:

<iframe class="embed-responsive-item" id="youtubeplayer" title="Starter Plan-Videos" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Dieses Video behandelt die Komponenten zwar schwerpunktmäßig aus der Perspektive von Starter- und Enterprise-Netzen, die Informationen sind jedoch auch für die vom Kunden verwaltete Lösung von {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private äußerst relevant.*

Die vorliegende Übersicht konzentriert sich lediglich auf Zertifizierungsstellen, Anordnungsknoten, Peers, Smart Contracts und Anwendungen. Im [{{site.data.keyword.blockchainfull_notm}} Platform 2.0-Handbuch zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) und im Handbuch [{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private - Bereitstellung](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp) ist erläutert, dass diese Auflistung nicht willkürlich ist, sondern die Reihenfolge abbildet, in der die Komponenten in einem auf Fabric basierenden Netz bereitgestellt werden.

## Zertifizierungsstellen
{: #blockchain-component-overview-ca}

Grundbausteine eines auf Fabric basierenden Blockchain-Netzes sind Identitäten und Berechtigungen. Identitäten sind x.509-Zertifikate, die von einer Zertifizierungsstelle ausgegeben werden, und insofern mit einer Kreditkarte vergleichbar, als sie jemanden *identifizieren*, was auch zugehörige Attribute beinhalten kann. Diese Zertifikate werden dann durch Aufnahme in MSP-Ordner auf Komponenten- oder Kanalebene mit Berechtigungen verknüpft. Ein Peer-MSP besitzt beispielsweise einen MSP-Unterordner namens **admin**. Jeder Benutzer, dessen Zertifikat sich in diesem Ordner "admin" befindet, ist dann ein Administrator des Peers und kann somit alle Aktionen ausführen, zu denen der Administrator dieses Peers berechtigt ist. Ein Validierungssystem innerhalb des Peers führt immer dann eine Prüfung durch, wenn ein durch sein Signierzertifikat identifizierter Benutzer versucht, eine Administratoraktion auszuführen. Falls das Zertifikat mit dem Zertifikat im Ordner "admin" übereinstimmt, kann die Aktion ausgeführt werden. Trifft dies nicht zu, wird die Anforderung zur Ausführung der Aktion abgelehnt.

Zertifizierungsstellen für {{site.data.keyword.blockchainfull_notm}} Platform basieren auf der [Zertifizierungsstelle für Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.2/ "Zertifizierungsstelle für Hyperledger Fabric"); es ist jedoch möglich, eine andere Zertifizierungsstelle zu verwenden, solange diese einen PKI auf der Basis von x.509-Zertifikaten verwendet. Es kann - und sollte in der Regel auch - mehrere Ebenen von Zertifizierungsstellen geben. Die Stammzertifizierungsstelle für ein Netz ist normalerweise lediglich zur Bereitstellung von Zertifikaten für Zwischenzertifizierungsstellen verfügbar, die anschließend Zertifikate entweder direkt für Benutzer und Komponenten oder für weitere Ebenen von Zwischenzertifizierungsstellen ausstellen. Weitere Details über den Aufbau von Identität und Mitgliedschaft mithilfe von Zertifizierungsstellen enthält die [Dokumentation von Hyperledger Fabric zur Identität![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/identity/identity.html) und zur [Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html).

## Anordnungsknoten
{: #blockchain-component-overview-orderer}

Der Anordnungsservice wird zwar häufig als "Herstück" eines Netzes bezeichnet, aber seine Funktion ist eigentlich ziemlich einfach, denn er dient dazu, Transaktionen, die von den Peers validiert wurden, in Blöcken anzuordnen und sie an die Peers zurückzusenden, damit diese sie in ihre Ledger schreiben können. In älteren Versionen von Fabric wurde diese Funktionalität im Peer selbst gebündelt. Ab der Fabric-Version 1.0 wurde sie jedoch in eine separate Komponente ausgegliedert, um die Peer-Leistung zu verbessern und Abweichungen zu verhindern, die zu potenziellen Statusverzweigungen führen könnten.

Auf physischer Ebene erfordert diese Anordnungsfunktion normalerweise eine Reihe von Anordnungsknoten, die zusammen als "Anordnungsservice" bezeichnet werden; in Umgebungen für Tests oder Machbarkeitsnachweise (Prof of Concept, PoC) kann jedoch auch ein einzelner Knoten verwendet werden, der dann "SOLO-Anordnungsknoten" genannt wird.

## Peers
{: #blockchain-component-overview-peer}

Physisch gesehen besteht ein Blockchain-Netz primär aus Peerknoten (kurz "Peers" genannt). Peers sind die grundlegenden Elemente des Netzes, da sie Ledger und Smart Contracts hosten (die in ["Chaincode" ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "Chaincode-Namensbereich") enthalten sind). Genauer gesagt hosten die Peers **Instanzen** des Ledgers und **Instanzen** von Smart Contracts. Da Smart Contracts und Ledger eingesetzt werden, um die gemeinsam genutzten Prozesse bzw. Informationen in einem Netzwerk zu kapseln, sind diese Aspekte eines Peers ein guter Ansatzpunkt für das Verständnis dessen, was ein Fabric-Netz eigentlich ausführt.

Weitere spezielle Informationen zu Peers enthält das [Dokument über Peers ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html) in der Dokumentation der Fabric-Community.

## Kanäle
{: #blockchain-component-overview-channels}

Ein Kanal ist ein Mechanismus, der eine offene Ebene der Kommunikation zwischen Mitgliedern im Netz bereitstellt. Es können mehrere Kanäle zwischen Untergruppen von Mitgliedern erstellt werden, wodurch einer der [vielen Mechanismen zum Bereitstellen des Datenschutzes ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Private und vertrauliche Transaktionen mit Hyperledger Fabric") unterstützt wird. Daten im Blockchain-Netz werden in den Ledgern des Kanals gespeichert. Diese Ledger werden auf den Peers der Organisationen gehostet, die dem Kanal beigetreten sind. Weitere Informationen zu Kanälen und zur Verwendung dieser Kanäle finden Sie in der [Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channels.html).

## Smart Contracts
{: #blockchain-component-overview-smart-contracts}

Bevor Unternehmen miteinander interagieren können, muss Einvernehmen über Regeln und Prozesse erzielt und in einer Art Vertrag definiert werden. Zusammengenommen legen diese Verträge das "Geschäftsmodell" an, das alle Interaktionen zwischen Geschäftspartnern regelt.

Dieselbe Anforderung besteht auch bei Blockchain-Netzen. Der branchenspezifische Begriff für diese Geschäftsmodelle lautet zwar "Smart Contracts", aber Fabric und {{site.data.keyword.blockchainfull_notm}} Platform enthalten diese Verträge in einer umfangreicheren Struktur, die als "Chaincode" bezeichnet wird und nicht nur die Geschäftslogik, sondern auch die zugrunde liegende Infrastruktur umfasst, mit der die Identitäten der Benutzer validiert werden, die versuchen, einen Smart Contract aufzurufen.

Während Verträge in der Geschäftswelt unter Hinzuziehung von Anwaltskanzleien unterzeichnet und archiviert werden, werden Smart Contracts auf Peers installiert und in einem Kanal "instanziiert".

## Anwendungen
{: #blockchain-component-overview-applications}

Clientanwendungen in einem Fabric-basierten Netz wie {{site.data.keyword.blockchainfull_notm}} Platform nutzen die zugrunde liegenden Infrastrukturen (z. B. APIs, SDKs und Smart Contracts), um Clientinteraktionen (Aufrufe und Abfragen) mit einer höheren Abstraktionsebene zu ermöglichen.

Eine Beschreibung der Interaktion von Anwendungen mit einem auf Fabric basierenden Netz können Sie dem [Dokument über das Schreiben einer ersten eigenen Anwendung ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "Writing Your First Application") entnehmen. Beachten Sie außerdem, dass in diesem Bereich in Fabric v1.4 erhebliche Verbesserungen vorgenommen werden, die in {{site.data.keyword.blockchainfull_notm}} Platform 2.0 verfügbar sind. Weitere Informationen finden Sie im Hyperledger Fabric-Thema zum [Entwickeln von Anwendungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Developing Applications"). Diese direkten Funktionen, z. B. die Vertragsklasse, sind nicht kompatibel mit Fabric v1.2. Dies ist die aktuelle Version von Starter Plan und {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, die konzeptionelle Informationen und andere nützliche Tipps enthält, die Sie bei Ihrem Anwendungsdesign beachten sollten, damit Sie bereit für die Zukunft sind.

Eine Beschreibung der Interaktion einer Anwendung mit einem auf Fabric basierenden Netz können Sie der Dokumentation zum [Erstellen von Anwendungen](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app) entnehmen. 

## Beispielnetz
{: #blockchain-component-overview-example-network}

**Abbildung 1** zeigt ein Beispiel eines bereitgestellten Blockchain-Netzes, das aus vier Mitgliedern (mit jeweils zwei Peers), Zertifizierungsstellen, die für das Verteilen kryptografischer Identitätsmaterialien zuständig sind, und einem Anordnungsservice, der Richtlinien und Netzteilnehmer definiert, besteht. Der blaue Kanal enthält alle vier Netzmitglieder und der gelbe Kanal ist auf drei Mitglieder beschränkt: die Banken B, C und D. Außerdem ist zu erkennen, dass Bank A die Rolle des Netzinitiators hat und Bank D nur als Mitglied im Kontext des gelben Kanals vorhanden ist. Schließlich kann ein Benutzer oder eine Anwendung mit einem ordnungsgemäß signierten x509-Zertifikat Aufrufe an Peers im Netz senden.

![Blockchain-Netz](images/blockchain_network_2-01.png "Blockchain-Beispielnetz")

*Abbildung 1. Beispiel für ein Blockchain-Netz mit vier Mitgliedern, die Kanäle zum Isolieren von Daten nutzen*

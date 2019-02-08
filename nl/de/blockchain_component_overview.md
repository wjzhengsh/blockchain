---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Blockchain-Komponenten im Überblick

Komponenten und Struktur von {{site.data.keyword.blockchainfull}} Platform (IBP) basieren auf der zugrunde liegenden Infrastruktur und den entsprechenden Tools von [Hyperledger Fabric ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/), einer Open-Source-Lösung für genehmigte Blockchains mit {{site.data.keyword.IBM_notm}} als einem der Hauptmitwirkenden. Auf Fabric basierende Netze beinhalten einige Standardkomponenten, die in einer Reihe von Konfigurationen bereitgestellt werden können, um eine Vielzahl von Anwendungsfällen zu unterstützen.

Eine umfassendere Betrachtung von Fabric-Netzen und der Wechselbeziehungen zwischen den Komponenten, aus denen sie bestehen, enthält das [Dokument über die Struktur eines Blockchain-Netzes](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) in der Dokumentation der Fabric-Community, in dem erläutert wird, wie ein Netz aufgebaut und weiterentwickelt werden kann.

Eine allgemeine Übersicht über die Komponenten in einem auf Fabric basierenden Netz bietet das folgende Video:

<iframe class="embed-responsive-item" id="youtubeplayer" title="Starter Plan-Videos" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Dieses Video behandelt die Komponenten zwar schwerpunktmäßig aus der Perspektive von Starter- und Enterprise-Netzen, die Informationen sind jedoch auch für die vom Kunden verwaltete Lösung von IBP on ICP (IBM Cloud Platform) äußerst relevant.*

Die vorliegende Übersicht konzentriert sich lediglich auf Zertifizierungsstellen, Anordnungsknoten, Peers, Smart Contracts und Anwendungen. Im Handbuch [{{site.data.keyword.blockchainfull_notm}} Platform for ICP - Bereitstellung](/docs/services/blockchain/ibp_for_icp_deployment_guide.html) ist erläutert, dass diese Auflistung nicht willkürlich ist, sondern die Reihenfolge abbildet, in der die Komponenten in einem auf Fabric basierenden Netz bereitgestellt werden.

## Zertifizierungsstellen
{: #ca}

Grundbausteine eines auf Fabric basierenden Blockchain-Netzes sind Identitäten und Berechtigungen. Identitäten sind x.509-Zertifikate, die von einer Zertifizierungsstelle ausgegeben werden, und insofern mit einer Kreditkarte vergleichbar, als sie jemanden *identifizieren*, was auch zugehörige Attribute beinhalten kann. Diese Zertifikate werden dann durch Aufnahme in MSP-Ordner auf Komponenten- oder Kanalebene mit Berechtigungen verknüpft. Ein Peer-MSP besitzt beispielsweise einen MSP-Unterordner namens **admin**. Jeder Benutzer, dessen Zertifikat sich in diesem Ordner "admin" befindet, ist dann ein Administrator des Peers und kann somit alle Aktionen ausführen, zu denen der Administrator dieses Peers berechtigt ist. Ein Validierungssystem innerhalb des Peers führt immer dann eine Prüfung durch, wenn ein durch sein Signierzertifikat identifizierter Benutzer versucht, eine Administratoraktion auszuführen. Falls das Zertifikat mit dem Zertifikat im Ordner "admin" übereinstimmt, kann die Aktion ausgeführt werden. Trifft dies nicht zu, wird die Anforderung zur Ausführung der Aktion abgelehnt.

{{site.data.keyword.blockchainfull_notm}}-Zertifizierungsstellen basieren auf der [Zertifizierungsstelle von Hyperledger Fabric](https://hyperledger-fabric-ca.readthedocs.io/en/latest/). Es kann jedoch auch eine andere Zertifizierungsstelle verwendet werden, sofern sie eine auf X.509-Zertifikaten basierende PKI verwendet. Es kann - und sollte in der Regel auch - mehrere Ebenen von Zertifizierungsstellen geben. Die Stammzertifizierungsstelle für ein Netz ist normalerweise lediglich zur Bereitstellung von Zertifikaten für Zwischenzertifizierungsstellen verfügbar, die anschließend Zertifikate entweder direkt für Benutzer und Komponenten oder für weitere Ebenen von Zwischenzertifizierungsstellen ausstellen. Weitere Details über den Aufbau von Identität und Mitgliedschaft mithilfe von Zertifizierungsstellen enthält die [Dokumentation von Hyperledger Fabric zur Identität![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) und zur [Mitgliedschaft ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html).

## Anordnungsknoten
{: #orderer}

Der Anordnungsservice wird zwar häufig als "Herstück" eines Netzes bezeichnet, aber seine Funktion ist eigentlich ziemlich einfach, denn er dient dazu, Transaktionen in Blöcken anzuordnen und sie an die Peers zurückzusenden, damit diese sie in ihre Ledger schreiben können. In Vorgängerversionen von Fabric wurde diese Funktionalität im Peer selbst gebündelt. Ab der Fabric-Version 1.0 wurde sie jedoch in eine separate Komponente ausgegliedert, um die Peer-Leistung zu verbessern und Abweichungen zu verhindern, die zu potenziellen Statusverzweigungen führen könnten.

Auf physischer Ebene erfordert diese Anordnungsfunktion normalerweise eine Reihe von Anordnungsknoten, die zusammen als "Anordnungsservice" bezeichnet werden; in Umgebungen für Tests oder Machbarkeitsnachweise (Prof of Concept, PoC) kann jedoch auch ein einzelner Knoten verwendet werden, der dann "SOLO-Anordnungsknoten" genannt wird.

## Peer
{: #peer}

Physisch gesehen besteht ein Blockchain-Netz primär aus Peerknoten (kurz "Peers" genannt). Peers sind ein grundlegendes Element des Netzes, da sie Ledger und Smart Contracts hosten (die in so genannten "Chaincodes" enthalten sind). Genauer gesagt hosten die Peers **Instanzen** des Ledgers und **Instanzen** von Smart Contracts. Da Smart Contracts und Ledgers eingesetzt werden, um die gemeinsam genutzten Prozesse bzw. Informationen in einem Netzwerk zu kapseln, sind diese Aspekte eines Peers ein guter Ansatzpunkt für das Verständnis dessen, was ein Fabric-Netz eigentlich ausführt.

Weitere spezielle Informationen zu Peers enthält das [Dokument über Peers](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html) in der Dokumentation der Fabric-Community.

## Smart Contracts

Bevor Unternehmen miteinander interagieren können, muss Einvernehmen über Regeln und Prozesse erzielt und in einer Art Vertrag definiert werden. Zusammengenommen legen diese Verträge das "Geschäftsmodell" an, das alle Interaktionen zwischen Geschäftspartnern regelt.

Dieselbe Anforderung besteht auch bei Blockchain-Netzen. Der branchenspezifische Begriff für diese Geschäftsmodelle lautet zwar "Smart Contracts", aber Fabric (und IBP) enthalten diese Verträge in einer umfangreicheren Struktur, die als "Chaincode" bezeichnet wird und nicht nur die Geschäftslogik, sondern auch die zugrunde liegende Infrastruktur umfasst, mit der die Identitäten der Benutzer validiert werden, die versuchen, einen Smart Contract aufzurufen.

Während Verträge in der Geschäftswelt unter Hinzuziehung von Anwaltskanzleien unterzeichnet und archiviert werden, werden Smart Contracts auf Peers installiert und in einem Kanal "instanziiert".

## Anwendungen

Clientanwendungen in einem Fabric-basierten Netz wie IBP nutzen die zugrunde liegenden Infrastrukturen (z. B. APIs, SDKs und Smart Contracts), um Clientinteraktionen (Aufrufe und Abfragen) mit einer höheren Abstraktionsebene zu ermöglichen.

Eine Beschreibung der Interaktion von Anwendungen mit einem auf Fabric basierenden Netz können Sie dem [Dokument über das Schreiben einer ersten eigenen Anwendung ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "Writing Your First Application") und dem [Abschnitt über die Entwicklung von Anwendungen ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "Developing Applications") entnehmen.

Bitte beachten Sie, dass sich der zweite Link auf Fabric-Funktionen (z. B. die Klasse `Contract`) bezieht, die mit Fabric Version 1.2, also der Basis für den Starter Plan und das Release von IBP on ICP, nicht kompatibel sind. Der Abschnitt enthält jedoch Informationen zu Konzepten und andere hilfreiche Tipps, deren Berücksichtigung bei der Entwicklung von Anwendungen sinnvoll ist, die zusammen mit Komponenten von Fabric Version 1.2 eingesetzt werden.

---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Haftungsausschluss
{: #disclaimer}

**ACHTUNG:** Sie müssen die folgenden Informationen überprüfen, bevor Sie {{site.data.keyword.blockchainfull}}-Pläne verwenden können.

## IBM Support-Aussage
{: #disclaimer-support-statement}

Mit den {{site.data.keyword.blockchainfull_notm}} Platform-Angeboten für {{site.data.keyword.cloud_notm}} setzt {{site.data.keyword.IBM}} seine langjährige Tradition der Bereitstellung innovativer Lösungen fort. Blockchain ist eine rasch fortschreitende Technologie, die das Ziel verfolgt, die Finanzwirtschaft, lokale und weltweite Supply-Chains sowie die logistische Unterstützung zahlreicher Geschäftsfelder neu zu gestalten. Durch verschiedene Early-Adoption-Programme haben {{site.data.keyword.IBM_notm}} Kunden und Business Partner Blockchain als Branchenlösung aktiv vorangetrieben. {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} ist eines dieser Programme. **Wie bei jeder neuen Technologie sollten sich Benutzer von {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} darüber im Klaren sein, dass schnelle und grundlegende Änderungen möglich sind**.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} baut auf der Architektur des Hyperledger Fabric-Projekts der Linux Foundation auf. Jeder Beitrag der Open-Source-Community verbessert Hyperledger Fabric, kann jedoch im Hinblick auf die Akzeptanz eine Herausforderung darstellen. **{{site.data.keyword.IBM_notm}} warnt vor dem direkten Definieren oder Austauschen von Finanzanlagen<!--, or any assets of value,--> in einem auf Hyperledger Fabric basierenden Blockchain-Netz**.

{{site.data.keyword.IBM_notm}} bietet keinen Support für Netze, die Hyperledger Composer in Produktionsumgebungen nutzen, inklusive Composer-Befehlszeilenschnittstelle, JavaScript-APIs, REST-Server und Web Playground.
{:note}

## Open-Source-Aussage
{: #disclaimer-open-source-statement}

{{site.data.keyword.blockchainfull_notm}} bietet Pläne auf {{site.data.keyword.cloud_notm}} an, die auf dem Hyperledger Fabric-Stack der Linux Foundation aufbauen. Hyperledger Project-Mitglieder, einschließlich {{site.data.keyword.IBM_notm}}, tragen weiterhin zu verschiedenen Unterprojekten unter dem Hyperledger-Umbrella bei.  Alle Beiträge werden von der Community überprüft, untersucht und getestet.

## Support-Aussage für Chaincode
{: #disclaimer-chaincode-support-statement}

Die folgenden Programmierverfahren werden in {{site.data.keyword.blockchainfull_notm}}-Netzen NICHT unterstützt:

1. Verwendung von assoziativen Feldgruppen mit Iteration (Reihenfolge ist in Go randomisiert).
2. Lesen einer Liste von Einträgen aus einer KVS-Tabelle (Reihenfolge ist nicht garantiert).
3. Schreiben von nicht threadsicherem Chaincode (Abfrage und Aufruf könnten parallel erfolgen).
4. Ersetzen von globalem Hauptspeicher oder Cachespeicher für Ledgerstatusvariablen im Chaincode.
5. Direkter Zugriff auf externe Services wie Datenbanken aus dem Chaincode.
6. Verwendung von Bibliotheken oder globalen Variablen, die zu Nichtdeterminismus führen könnten (z. B. "random" oder "time").

Das Schreiben von nicht deterministischem Chaincode wird nicht empfohlen, da sich dadurch Risiken für die Datenkonsistenz und -integrität ergeben. Beachten Sie, dass die Hyperledger Fabric-Architektur mit einer Reihe von Bewilligungs- und Validierungsprüfungen entwickelt wurde, um der Erstellung von nicht deterministischem Chaincode entgegenzuwirken. Es wird jedoch weiterhin dringend empfohlen, deterministische Funktionen zu codieren, die nicht auf nicht statischen globalen Variablen (z. B. Zeit) basieren.

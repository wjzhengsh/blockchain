---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Haftungsausschluss
{: #disclainer}

**ACHTUNG:** Sie müssen die folgenden Informationen überprüfen, bevor Sie {{site.data.keyword.blockchainfull}}-Pläne verwenden können.

## IBM Support-Aussage

Mit den Plänen des {{site.data.keyword.blockchainfull_notm}}-Angebots für {{site.data.keyword.Bluemix_notm}} setzt IBM seine langjährige Tradition der Bereitstellung innovativer Lösungen fort. {{site.data.keyword.blockchain}} ist eine rasch fortschreitende Technologie, die das Ziel verfolgt, die Finanzwirtschaft, lokale und weltweite Supply-Chains sowie die logistische Unterstützung zahlreicher Geschäftsfelder neu zu gestalten. Durch verschiedene Early-Adoption-Programme haben IBM Kunden und Business Partner Blockchain als Branchenlösung aktiv vorangetrieben. {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} ist eines dieser Programme. **Wie bei jeder neuen Technologie sollten sich Benutzer von {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} darüber im Klaren sein, dass schnelle und grundlegende Änderungen möglich sind**.  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} baut auf der Architektur des Hyperledger Fabric Projects der Linux Foundation auf. Jeder Beitrag der Open-Source-Community verbessert Hyperledger Fabric, kann jedoch im Hinblick auf die Akzeptanz eine Herausforderung darstellen. **IBM warnt vor dem direkten Definieren oder Austauschen von Finanzanlagen oder anderen Assets von Wert in einem auf Hyperledger Fabric basierenden Blockchain-Netz**.  

## Open-Source-Aussage

{{site.data.keyword.blockchainfull_notm}} bietet Pläne auf {{site.data.keyword.Bluemix_notm}} an, die auf dem Hyperledger Fabric-Stack der Linux Foundation aufbauen. Hyperledger Project-Mitglieder, einschließlich IBM, tragen weiterhin zu verschiedenen Unterprojekten unter dem Hyperledger-Umbrella bei.  Alle Beiträge werden von der Community überprüft, untersucht und getestet.

## Support-Aussage für Chaincode

Die folgenden Programmierverfahren werden in {{site.data.keyword.blockchainfull_notm}}-Netzen NICHT unterstützt:

1. Verwendung von assoziativen Feldgruppen mit Iteration (Reihenfolge ist in Go randomisiert).
2. Lesen einer Liste von Einträgen aus einer KVS-Tabelle (Reihenfolge ist nicht garantiert).
3. Schreiben von nicht threadsicherem Chaincode (Abfrage und Aufruf könnten parallel erfolgen).
4. Ersetzen von globalem Hauptspeicher oder Cachespeicher für Ledgerstatusvariablen im Chaincode.
5. Direkter Zugriff auf externe Services wie Datenbanken aus dem Chaincode.
6. Verwendung von Bibliotheken oder globalen Variablen, die zu Nichtdeterminismus führen könnten (z. B. "random" oder "time").  

Das Schreiben von nicht deterministischem Chaincode wird nicht empfohlen, da sich dadurch Risiken für die Datenkonsistenz und -integrität ergeben.  Beachten Sie, dass die Hyperledger Fabric-Architektur mit einer Reihe von Bewilligungs- und Validierungsprüfungen entwickelt wurde, um der Erstellung von nicht deterministischem Chaincode entgegenzuwirken. Es wird jedoch weiterhin dringend empfohlen, deterministische Funktionen zu codieren, die nicht auf nicht statischen globalen Variablen (z. B. Zeit) basieren.  

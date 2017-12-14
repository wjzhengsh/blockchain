---

copyright:
  years: 2017
lastupdated: "2017-07-19"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Haftungsausschluss
{: #etn_overview}

**ACHTUNG:** Sie müssen die folgenden Informationen überprüfen, bevor Sie {{site.data.keyword.blockchainfull}}-Pläne verwenden können.

## IBM Support-Aussage

Mit den {{site.data.keyword.blockchainfull_notm}}-Angebotsplänen für {{site.data.keyword.Bluemix_notm}} setzt IBM seine langjährige Tradtion der Bereitstellung innovativer Lösungen fort. {{site.data.keyword.blockchain}} ist eine sich schnell weiterentwickelnde Technologie, die das Ziel verfolgt, die Finanzwirtschaft, lokale und weltweite Supply-Chains sowie die logistische Unterstützung zahlreicher Geschäftsfelder zu revolutionieren. Durch verschiedene Early-Adoption-Programme haben IBM Kunden und Business Partner aktiv Blockchain als eine Branchenlösung vorangetrieben. {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} ist eines dieser Programme. **Wie bei jeder neuen Technologie sollten sich Benutzer von {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} einer raschen Abfolge von grundlegenden Änderungen bewusst sein**.  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} stützt sich auf die Architektur des Hyperledger Fabric Projects der Linux Foundation. Jeder Beitrag der Open-Source-Community verbessert Hyperledger Fabric, kann jedoch im Hinblick auf die Akzeptanz eine Herausforderung darstellen. **IBM warnt vor dem direkten Definieren oder Austauschen von Finanzanlagen oder anderen Assets in einem Blockchain-Netz**.  

## Open-Source-Aussage

Die {{site.data.keyword.blockchainfull_notm}}-Angebotspläne für {{site.data.keyword.Bluemix_notm}} basieren auf dem Hyperledger Fabric V1.0-Stack der Linux Foundation. Hyperledger Project-Mitglieder, einschließlich IBM, tragen weiterhin zu verschiedenen Unterprojekten unter dem Hyperledger-Umbrella bei.  Alle Beiträge werden von der Community überprüft, untersucht und getestet. 

Hyperledger Fabric gab am 11. Juli 2017 bekannt, dass für V1.0 der *Production Level Status* erreicht wurde. 

## Support-Aussage für Chaincode

Die folgenden Programmierungsverfahren werden in {{site.data.keyword.blockchainfull_notm}}-Netzen NICHT unterstützt:

1. Verwendung von assoziativen Feldgruppen mit Iteration (Reihenfolge ist in Go randomisiert).
2. Lesen einer Liste von Einträgen aus einer KVS-Tabelle (Reihenfolge ist nicht garantiert).
3. Schreiben von nicht threadsicherem Chaincode (Abfrage und Aufruf können parallel erfolgen).
4. Ersetzen von globalem Hauptspeicher oder Cachespeicher für Hauptbuchstatusvariablen im Chaincode.
5. Direkter Zugriff auf externe Services wie Datenbanken aus dem Chaincode.
6. Verwendung von Bibliotheken oder globalen Variablen, die zu Nichtdeterminismus führen könnten (z. B. "random" oder "time").  

Das Schreiben von nicht deterministischem Chaincode wird nicht empfohlen, da sich dadurch Risiken für die Datenkonsistenz und -integrität ergeben.  Es sei darauf hingewiesen, dass die Hyperledger Fabric-Architektur mit einem speziellen Bewilligungs- und Validierungssystem entwickelt wurde, um der Erstellung von nicht deterministischem Chaincode entgegenzuwirken. Es wird weiterhin dringend empfohlen, deterministische Funktionen zu codieren, die auf nicht statischen globalen Variablen (z. B. Zeit) basieren.  

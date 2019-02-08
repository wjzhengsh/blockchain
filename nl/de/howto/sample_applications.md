---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Beispielanwendungen

Sie können Beispielanwendungen bereitstellen und testen, um ein besseres Verständnis der {{site.data.keyword.blockchainfull}}-Netz-/-Anwendungsentwicklung zu erhalten.
{:shortdesc}

## Vorbemerkungen

Sie müssen die gesamte vorausgesetzte Software auf Ihrem lokalen Dateisystem installieren.  Weitere Informationen finden Sie unter [Anwendungsentwicklungsumgebung einrichten](/docs/services/blockchain/v10_application.html#setting-up-application-development-environment).

Sie müssen außerdem über ein {{site.data.keyword.blockchain}}-Netz unter {{site.data.keyword.Bluemix_short}} verfügen, für das ein Kanal mit den zugehörigen Peers konfiguriert ist.  Weitere Informationen finden Sie unter [Netz steuern](/docs/services/blockchain/get_start.html).  Wenn Ihr Netz betriebsbereit ist, rufen Sie die API-Endpunkte Ihrer Netzressourcen ab, auf die Ihre Anwendung zugreift.  Weitere Informationen finden Sie unter [Netzservice-Berechtigungsnachweise zur Anwendung hinzufügen](/docs/services/blockchain/v10_application.html#adding-network-service-credentials-to-your-application).


## Marbles-Anwendung

In der Marbles-Anwendung können mehrere Benutzer "Marbles" (Murmeln) mit verschiedenen Eigenschaften erstellen und sie mit anderen übertragen.  Die Marbles-Anwendung ist in JavaScript geschrieben und der Chaincode ist in Go geschrieben.

Sie finden den Beispielcode sowie Anweisungen unter [Marbles in GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/IBM-Blockchain/marbles).


## Fabric-Kfz-Anwendung

In der Fabric-Kfz-Anwendung können Sie Kfz-Datensätze im Hauptbuch (Ledger) **abfragen** und **aktualisieren**.  Die Fabric-Kfz-Anwendung ist in JavaScript geschrieben und der Chaincode ist in Go geschrieben.

Sie finden Beispielcode unter [Fabric car in GitHub ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) sowie eine Anleitung unter [Writing Your First Application ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

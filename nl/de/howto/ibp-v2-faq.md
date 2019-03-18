---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# Häufig gestellte Fragen
{: #ibp-v2-faq}

## Welche Version von Hyperledger Fabric wird in der kostenlosen Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 verwendet?
{: #ibp-v2-faq-fabric-version}
{: faq}

Die kostenlose Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 basiert auf Hyperledger Fabric v1.4 und beinhaltet Unterstützung für Gossip, Serviceerkennung und private Daten.

## Muss ich für die kostenlose Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 etwas zahlen?
{: #ibp-v2-faq-cost}
{: faq}

Die Betaversion ist kostenlos. Es können jedoch Gebühren für Ihren {{site.data.keyword.cloud_notm}} Kubernetes-Cluster anfallen, sofern Sie keinen kostenlosen Cluster ausgewählt haben. Beachten Sie, dass der kostenlose Cluster gemäß der {{site.data.keyword.cloud_notm}} Kubernetes-Servicerichtlinie nach 30 Tagen automatisch gelöscht wird und eine begrenzte Leistung und Kapazität bietet. Wenn Ihr Service auch nach Ablauf von 30 Tagen bestehen bleiben soll, müssen Sie sich für eine kostenpflichtige Standardinstanz des Kubernetes-Service entscheiden. 

## Kann ich meinen bestehenden {{site.data.keyword.cloud_notm}} Kubernetes Service-Cluster verwenden?
{: #ibp-v2-faq-existing-cluster}
{: faq}

Ihr bestehender Kubernetes-Cluster kann mit der kostenlosen Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 verwendet werden, wenn die folgenden Bedingungen erfüllt sind: 
- Der Cluster befindet sich in der {{site.data.keyword.cloud_notm}}-Region 'Dallas'.
- Auf dem Cluster wird Kubernetes v1.11 oder höher ausgeführt. Weitere Informationen finden Sie im Abschnitt [Kubernetes-Version Ihres Clusters aktualisieren](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).
- Im Cluster sind genügend verfügbare Ressourcen vorhanden. Weitere Informationen finden Sie im Abschnitt [Mindestvoraussetzungen für Ressourcen](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required).

## Welche Datenbank verwenden die Peers für ihre Ledger?
{: #ibp-v2-faq-couchDB}
{: faq}

Alle Peers, die mit der kostenlosen Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 bereitgestellt werden, verwenden die CouchDB-Datenbank. Die CouchDB-Datenbank kann jedoch nicht direkt abgefragt werden.

## Welche Sprachen werden für Smart Contracts unterstützt?
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform unterstützt Smart Contracts, die in den Programmiersprachen Go, Node.js und Java geschrieben sind. Das neue Hyperledger Fabric-Programmiermodell unterstützt derzeit nur Node.js. Weitere Sprachen folgen in Kürze. Wenn Sie Ihren vorhandenen Anwendungscode beibehalten oder Fabric-SDKs für andere Sprachen als Node.js, verwenden möchten, können Sie weiterhin eine Verbindung zu Ihrem Netzwerk der kostenlosen Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 herstellen, indem Sie untergeordnete APIs des Fabric-SDK verwenden.

## Kann ich von der Betaversion 2.0 auf die allgemein verfügbare Version 2.0 migrieren? 
{: #ibp-v2-faq-migrate}
{: faq}

Kunden mit Enterprise Plan können auf IBM Blockchain Platform 2.0 migrieren, sobald das Produkt allgemein verfügbar ist. Es wird jedoch nicht die Möglichkeit geben, Ihre Betaversion 2.0 auf die allgemein verfügbare Produktversion 2.0 zu migrieren. Außerdem kann eine Starter Plan-Instanz weder auf die Betaversion 2.0 noch auf die künftige, allgemein verfügbare Produktversion 2.0 migriert werden.

## Besteht Zugriff auf Protokollierungsservices und welche Protokolle sind für mich verfügbar?
{: #ibp-v2-faq-logs}
{: faq}

Mit {{site.data.keyword.blockchainfull_notm}} Platform 2.0, können Sie jetzt über das Kubernetes-Dashboard direkt auf Ihre Peer-, CA- und Anordnungsknotenprotokolle zugreifen. Es wird empfohlen, den Service [{{site.data.keyword.cloud_notm}} LogDNA ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis with LogDNA") zu nutzen, der das Analysieren der Protokolle in Echtzeit ohne großen Aufwand ermöglicht.

## Welchen Mehrwert bietet die kostenlose Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 im Vergleich zum nativen Produkt Hyperledger Fabric?
{: #ibp-v2-faq-native-fabric}
{: faq}

Die kostenlose Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 ermöglicht es Clients, ein angepasstes Blockchain-Netz ohne großen Aufwand bereitzustellen. Die Plattform enthält eine intuitive Konsole für schnelle Netzbereitstellung und einfache Installation und Instanziierung von Smart Contracts. Diese Plattform bietet Ihnen außerdem Unterstützung durch Fachleute von {{site.data.keyword.IBM_notm}} beim Entwickeln Ihres Smart Contract und beim Einrichten Ihres Netzes.

## Wie kann ich die Leistung maximieren und meine {{site.data.keyword.blockchainfull_notm}} Platform-Lösung skalieren?
{: #ibp-v2-faq-perf-scale}
{: faq}

Informationen zu Faktoren, die sich auf die Leistung Ihrer Lösung auswirken, und zur Skalierung von Blockchain finden Sie [ in dieser Blog-Reihe ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Answering your questions on Hyperledger Fabric performance and scale").

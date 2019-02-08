---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Neuerungen
{: #whatsnew}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 7. Dezember 2018

Die Abschnitte *Starter Plan-Netz betreiben* und *Enterprise Plan-Netz betreiben* wurden kombiniert und durch ein einziges Lernprogramm namens [Network Monitor verwenden](/docs/services/blockchain/v10_dashboard.html) ersetzt.

## 27. November 2018

{{site.data.keyword.blockchainfull}} Platform gibt {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private (ICP) frei. ICP ist eine Anwendungsplattform für die Entwicklung und Verwaltung von containerisierten Anwendungen, die auf Kubernetes basieren, und ermöglicht Benutzern die Bereitstellung von Zertifizierungsstellen, Anordnungsknoten und Peers unter x86, LinuxONE und IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform for ICP basiert auf Hyperledger Fabric Version 1.2.1 und wird unter Verwendung von Kubernetes-Helm-Diagrammen bereitgestellt. Nach der Installation des Helm-Diagramms ist die Plattform als Servicepaket im ICP-Katalog enthalten. Beachten Sie, dass [dieses Angebot](/docs/services/blockchain/ibp-for-icp-about.html) eher für erfahrene Fabric-Benutzer geeignet ist.

Weitere Informationen zu ICP enthält die [Dokumentation zu {{site.data.keyword.cloud_notm}} Private Version 3.1.0 ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private Version 3.1.0 - Dokumentation").

Mit dem Release von {{site.data.keyword.blockchainfull_notm}} Platform for ICP ist gleichzeitig das Programm der Betaversion von IBM Blockchain Platform Remote Peer beendet. Ein Peer kann weiterhin in ICP oder AWS bereitgestellt und mit einem Starter Plan- oder Enterprise Plan-Netz verbunden werden. Weitere Informationen enthalten die Abschnitte [Peer für Starter oder Enterprise Plan bereitstellen](/docs/services/blockchain/howto/peer_deploy_ibp.html) und [Peer in AWS bereitstellen](/docs/services/blockchain/howto/remote_peer_aws.html). Diese Peers werden nicht als ferne Peers, sondern als **verteilte** Peers betrachtet, weil die Bereitstellung durch den Kunden verwaltet wird und es daher keine zentrale Position und somit auch keinen als fern definierten Standort gibt.

In diesem Release wurden auch einige Verbesserungen am Inhaltsverzeichnis für die Dokumentation vorgenommen. Als Starter- oder Enterprise-Benutzer befindet sich der für Sie relevante Inhalt weiterhin in den Abschnitten **INFORMATIONEN**, **VORGEHENSWEISE**, **REFERENZINFORMATIONEN** und  **HILFE**; auch die Links sind identisch. Der Inhalt befindet sich möglicherweise lediglich in einem anderen Unterabschnitt des Inhaltsverzeichnisses.

## 13. November 2018

{{site.data.keyword.blockchainfull_notm}} Platform gibt {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS) frei.

Mit {{site.data.keyword.blockchainfull_notm}} Platform for AWS können Sie Peers in der AWS-Cloud ausführen und mit vorhandenen Blockchain-Netzen in {{site.data.keyword.cloud_notm}} verbinden. Ihre Peers in AWS können das Verbindungsprofil und andere Blockchain-Komponenten in den vorhandenen Netzen nutzen; gleichzeitig erhalten Sie eine größere Flexibilität bei der Zusammenfassung Ihrer Peers mit anderen Anwendungen außerhalb von {{site.data.keyword.cloud_notm}}. Weitere Angaben enthält der Abschnitt [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer.html).

## 4. Oktober 2018

{{site.data.keyword.blockchainfull_notm}} Platform-Upgrade für den Starter Plan von Hyperledger Fabric Version 1.1.0 auf Version 1.2.1. Weitere Informationen finden Sie unter [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html).

**Wichtig:** Fabric Version 1.2.1 ist gegenwärtig nicht mit der Betaversion von Remote Peer kompatibel, die einen Peer von Version 1.1.0 nutzt. Starter Plan-Netze, die vor dem 4. Oktober 2018 erstellt wurden und auf Fabric Version 1.1.0 basieren, sind hiervon nicht betroffen und können weiterhin zusammen mit der Betaversion von Remote Peer eingesetzt werden. {{site.data.keyword.blockchainfull_notm}} Platform wird zu einem späteren Zeitpunkt die Betaversion von Remote Peer für die Verwendung des Peers von Version 1.2.1 aktualisieren, wodurch eine Kompatibilität mit neueren Starter-Netzen mit der Fabric-Version 1.2.1 und Enterprise-Netzen mit der Fabric-Version 1.1.0 erreicht wird. Versuchen Sie bis zur Aktualisierung der Betaversion von Remote Peer auf die Fabric-Version 1.2.1 nicht, einen fernen Peer der Version 1.1.0 mit einem neuen Starter-Netz der Version 1.2.1 bereitzustellen.

## 4. September 2018

{{site.data.keyword.blockchainfull_notm}} Platform gibt die Betaversion für das Remote Peer-Angebot frei. Das Remote Peer-Angebot basiert auf Hyperledger Fabric Version 1.1.0. Mithilfe von Remote Peer können Sie Peerknoten von {{site.data.keyword.blockchainfull_notm}} in Ihrer eigenen Cloudumgebung von IBM Cloud Private (ICP) oder Amazon Web Services (AWS) ausführen. Weitere Informationen finden Sie unter [Informationen zu fernen Peers](/docs/services/blockchain/howto/remote_peer.html).

## 15. Juni 2018

{{site.data.keyword.blockchainfull_notm}} Platform gibt GA-Version von Starter Plan frei. Weitere Informationen finden Sie unter [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html).

## 15. Mai 2018

{{site.data.keyword.blockchainfull_notm}} Platform-Upgrade für den Enterprise Plan von Hyperledger Fabric Version 1.0.0 auf Version 1.1.0. Weitere Angaben enthält der Abschnitt [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html).

## 18. März 2018

{{site.data.keyword.blockchainfull_notm}} Platform gibt die Betaversion für den Starter Plan frei. Der Starter Plan bietet Ihnen eine Entwicklungs- und Testumgebung, in der Sie die Verwendung in einem {{site.data.keyword.blockchainfull_notm}} Platform-Netz lernen und üben können. Weitere Informationen finden Sie unter [Informationen zum Starter Plan](/docs/services/blockchain/starter_plan.html).

## 11. August 2017

{{site.data.keyword.blockchainfull_notm}} Platform ersetzt {{site.data.keyword.blockchainfull_notm}} on Cloud und gibt den Enterprise Plan frei. Weitere Angaben enthält der Abschnitt [Informationen zum Enterprise Plan](/docs/services/blockchain/enterprise_plan.html).

---

copyright:
  years: 2017, 2019
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
{:pre: .pre}

# Einführung in {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp}

{{site.data.keyword.blockchainfull}} Platform bietet einen verwalteten und umfassenden Stack-Blockchain-as-a-Service (BaaS) an, mit dem Sie Blockchain-Komponenten in Umgebungen Ihrer Wahl bereitstellen können. Die Umgebung kann {{site.data.keyword.cloud_notm}}, lokal über {{site.data.keyword.cloud_notm}} Private und Drittanbieter-Clouds, wie z. B. Amazon Web Services (AWS), sein. In diesem Lernprogramm führen wir Sie durch den allgemeinen Prozess, wie Sie ein grundlegendes Blockchain-Netz mit {{site.data.keyword.blockchainfull_notm}} Platform konfigurieren.
{:shortdesc}

**Wichtig:** Lesen Sie vor der Verwendung eines {{site.data.keyword.blockchainfull_notm}} Platform-Angebots die technischen Angaben und Unterstützungsinformationen im Abschnitt [Haftungsausschluss](/docs/services/blockchain/needtoknow.html#disclaimer).


## Vorbemerkungen
{: #get-started-ibp-prereqs}

{{site.data.keyword.blockchainfull_notm}} Platform bietet unterschiedliche Angebote, die die verschiedenen Benutzertypen beim Einstieg in die Arbeit mit Blockchains unterstützen und den Benutzern helfen, ihre Anwendungen in die Produktionsphase zu bringen. Sie müssen Ihre Umgebung und das Angebot festlegen, die Sie verwenden möchten. In Abbildung 1 sind die Funktionen, Zielgruppen, Preisangaben und Cloudplattformen für verschiedene Angebote aufgelistet. Klicken Sie in der folgenden Tabelle auf das jeweilige Angebot, um weitere Informationen zum Angebot zu erhalten.

| **Angebote** | **Inhalt** | **Abrechnungsmodell** | **Cloudplattform** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**Starter Plan**](/docs/services/blockchain/starter_plan.html#starter-plan-about) | {{site.data.keyword.IBM_notm}}-verwaltetes Netz mit grundlegender Service-Level-, Entwicklungs- und Testumgebung | Monatliches Abonnement | {{site.data.keyword.cloud_notm}} |
| [**Enterprise Plan**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) | {{site.data.keyword.IBM_notm}}-verwaltetes Netz mit erweiterter Service-Level-, Entwicklungs- und Testumgebung | Monatliches Abonnement | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform Free 2.0 Beta**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | {{site.data.keyword.blockchainfull_notm}} Platform-Konsole zum Bereitstellen und Verwalten von Blockchain-Komponenten in Ihrem {{site.data.keyword.cloud_notm}} Kubernetes-Cluster | Kostenlose Betaversion | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | Bereitstellbare Helm-Diagramme für Zertifizierungsstellen, Anordnungsknoten und Peers | [VPC-Preisstruktur](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) und kostenlose Community Edition | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | AWS-Schnelleinstiegsvorlage zum Bereitstellen von fernen Peers außerhalb {{site.data.keyword.cloud_notm}} | Kostenlos | AWS |

*Abbildung 1: {{site.data.keyword.blockchainfull_notm}} Platform-Angebote*

Verwenden Sie weder den Starter Plan noch die kostenlose 2.0 Beta-Version für die Produktionsverwendung. Es handelt sich um eine Entwicklungs- und Testumgebung, die für Produktionsworkloads nicht geeignet ist.
{: important}

## Schritt 1: Holen Sie ein Angebot ein
{: #get-started-ibp-step1}

Stellen Sie sicher, dass Sie über das Cloud-Konto oder die PPA-Lizenz verfügen, um ein {{site.data.keyword.blockchainfull_notm}} Platform-Angebot zu erhalten.

* **Starter Plan**, **Enterprise Plan** und **{{site.data.keyword.blockchainfull_notm}} Platform Free 2.0 Beta**

  Diese Angebote sind in {{site.data.keyword.cloud_notm}} verfügbar, und Sie können sie im [Katalog-Dashboard ![External link icon](images/external_link.svg "External link icon")](https://cloud.ibm.com/catalog "Catalog") von {{site.data.keyword.cloud_notm}} finden.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Dieses Angebot wird als bereitstellbares Helm-Diagramm geliefert und hat sowohl eine kostenpflichtige Edition als auch eine kostenlose Community-Edition. Sie können die Enterprise Edition von [Passport Advantage Online![External link icon](images/external_link.svg "External link icon")](https://www.ibm.com/software/passportadvantage/pao_customer.html) oder die kostenlose Community-Edition von [GitHub ![External link icon](images/external_link.svg "External link icon")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz) herunterladen.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Dieses Angebot ist in AWS verfügbar und Sie können einen Blockchain-Peer in AWS mithilfe der [Schnelleinstiegsvorlage ![External link icon](images/external_link.svg "External link icon")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/) bereitstellen.

## Schritt 2: Stellen Sie {{site.data.keyword.blockchainfull_notm}} Platform bereit
{: #get-started-ibp-step2}

* **Starter Plan**, **Enterprise Plan** und **{{site.data.keyword.blockchainfull_notm}} Platform Free 2.0 Beta**

  Melden Sie sich bei {{site.data.keyword.cloud_notm}} an und erstellen Sie eine Serviceinstanz mit dem Angebot. Wenn Sie den Starter Plan verwenden, können Sie sofort nach dem [Erstellen der Serviceinstanz](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan) ein Blockchain-Netz mit der Standardkonfiguration abrufen. Wenn Sie den Enterprise Plan oder die kostenlose Betaversion {{site.data.keyword.blockchainfull_notm}} Platform 2.0 verwenden, müssen Sie dem Assistenten folgen, um die Erstkonfiguration für Ihr Netz abzuschließen. Weitere Informationen finden Sie im Abschnitt [Enterprise Plan-Netz erstellen](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) oder [Deploying {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Kubernetes Service](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks) bereitstellen.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Bevor Sie ein Netz bereitstellen, müssen Sie das [Helm-Diagramm in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) importieren. Anschließend können Sie [{{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-deploy-icp) konfigurieren und installieren.

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Dieses Angebot ist in AWS verfügbar und Sie können einen Blockchain-Peer in AWS mithilfe der [Schnelleinstiegsvorlage ![External link icon](images/external_link.svg "External link icon")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/) bereitstellen.

## Nächste Schritte
{: #get-started-ibp-next-steps}

Nachdem Sie {{site.data.keyword.blockchainfull_notm}} Platform in der Umgebung Ihrer Wahl bereitgestellt haben, können Sie das Netz mit Konsortium, Knoten, Kanälen und Smart Contracts einrichten und Transaktionen starten. Weitere Informationen finden Sie in der linken Navigation in dieser Dokumentation in den Aufgabenthemen im Abschnitt **VORGEHENSWEISE**.

## Support anfordern
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}} bietet verschiedene Supportoptionen für von {{site.data.keyword.IBM_notm}} bereitgestellte Blockchain-Lösungen an. Weitere Informationen zum {{site.data.keyword.blockchainfull_notm}} Platform-Support finden Sie unter [Support anfordern](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Einführung in {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #get-started-icp}

***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private ermöglicht Benutzern das Bereitstellen von Zertifizierungsstellen (CAs), Anordnungsknoten und Peers auf x86-, LinuxONE- und IBM Z-Systemen mit Kubernetes-Helm-Diagrammen in einer On-Premises-Umgebung, wie z. B. {{site.data.keyword.cloud_notm}} Private, und verbinden Sie sie mit Komponenten, die in mehreren Cloud-Umgebungen gehostet werden. {{site.data.keyword.cloud_notm}} Private ist eine Anwendungsplattform für die Entwicklung und Verwaltung von lokaler und containerisierten Anwendungen. Es handelt sich um eine integrierte Umgebung für die Verwaltung von Containern, die den Container-Orchestrator Kubernetes, eine private Image-Registry, eine Managementkonsole und Überwachungsframeworks enthalten.{:shortdesc}

Blockchain-Netze, die auf [Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.2/) basieren, können in einer quasi unbegrenzten Vielzahl von Konfigurationen zur Unterstützung ebenso vieler Anwendungsfälle bereitgestellt werden. Ungeachtet dieser Vielseitigkeit gibt es eine Reihe von Best Practices, insbesondere hinsichtlich der ordnungsgemäßen **Reihenfolge** bei der Konfiguration und Bereitstellung von Netzkomponenten.

In diesem Bereitstellungshandbuch ist neben den Best Practices und den bedenkenswerten Aspekten bei der Bereitstellung die korrekte Reihenfolge für die Einrichtung eines funktionsfähigen {{site.data.keyword.blockchainfull}} Platform-Netzes in {{site.data.keyword.cloud_notm}} Private beschrieben. Die Grundregeln gelten jedoch auch, wenn Sie lediglich eine funktionsfähige Zertifizierungsstelle, einen Anordnungsknoten oder einen Peer konfigurieren wollen. Bitte beachten Sie, dass der SOLO-Anordnungsservice, der nur einen einzigen Anordnungsknoten bereitstellt, nicht für Produktionsumgebungen gedacht ist. Kein Netz oder Kanal, von dem SOLO ausgeführt wird, kann als Produktionsumgebung betrachtet werden. Es ist jedoch möglich, den Peer und die Zertifizierungsstelle in einer Produktionsumgebung bereitzustellen, insbesondere dann, wenn diese hoch verfügbar sind.

Der Prozess für die Bereitstellung von {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private ist komplex und setzt ein großes Fachwissen über Fabric voraus. Falls Sie Fabric, {{site.data.keyword.blockchainfull_notm}} Platform oder {{site.data.keyword.cloud_notm}} Private noch nicht kennen und die Einrichtung einer Umgebung für Entwicklung oder Machbarkeitsnachweise anstreben, kann stattdessen die Verwendung des [Starter Plans](/docs/services/blockchain/starter_plan.html#starter-plan-about) für Sie sinnvoll sein. Bitte beachten Sie außerdem, dass nicht jede potenzielle Bereitstellungskonfiguration unterstützt wird. Aufgrund der Komplexität wird davon ausgegangen, dass der Prozess für die Bereitstellung und die Verbindung der einzelnen Komponenten von Fabric-Experten durchgeführt wird, bevor Administrationsaufgaben an andere Beteiligte übertragen werden. Diese Experten bilden die Zielgruppe dieses Handbuchs sowie der Dokumentation, die allgemein für Helm-Diagrammkomponenten verfügbar ist.
{:important}

## Schritt 1: Legen Sie Ihre Netzkonfiguration fest

Die Struktur eines Blockchain-Netzes muss durch den Anwendungsfall vorgegeben werden. Solche grundlegenden Geschäftsentscheidungen variieren in unterschiedlichen Situationen, von denen dennoch einige genauer betrachtet werden sollen.

* **Entwicklungsumgebung** einrichten

  Dies ist der Zweck, für den der [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) gedacht ist. Falls Sie jedoch Ihre eigenen Komponenten in einer Entwicklungsumgebung verwalten wollen, benötigen Sie eine Basiskonfiguration, die aus einem Anordnungsknoten und einem Peer (mit jeweils einer Zertifizierungsstelle für jede Organisation) besteht. Für jede Organisation können Sie lediglich einen einzigen Anordnungsknoten und einen einzigen Peer verwenden (ähnlich wie bei der Konfiguration des [fabcar-Netzes](https://hyperledger-fabric.readthedocs.io/en/release-1.2/understand_fabcar_network.html)). Ebenso können Sie festlegen, dass Sie für alle diese Komponenten nur einen einzigen Benutzer mit Administratorberechtigung benötigen, statt für jede Komponente separate Benutzer zu erstellen.

* **Produktionskomponenten** entweder für neues Netz oder für vorhandenes Netz bereitstellen

  Produktionskomponenten und Produktionsnetze weisen andere Anforderungen als Entwicklungsumgebungen oder Machbarkeitsnachweise auf. Unter anderem wird hier die Hochverfügbarkeit zu einer Priorität. Ein SOLO-Anordnungsservice, der nur einen einzigen Anordnungsknoten enthält (und somit einen Single Point of Failure darstellt), ist definitionsbedingt nicht für die Produktion geeignet.

**Hinweis:** Im vorliegenden Bereitstellungshandbuch werden nicht alle Iterationen und potenziellen Netzkonfigurationen beschrieben, sondern allgemeine Richtlinien und Regeln beschrieben, die zu berücksichtigen sind.

## Schritt 2: Richten Sie einen Kubernetes-Cluster unter {{site.data.keyword.cloud_notm}} Private ein

Nachdem Sie die Netzstruktur festgelegt haben, richten Sie in {{site.data.keyword.cloud_notm}} Private einen Kubernetes-Cluster für Ihre Anwendungsfälle ein. Weitere Informationen finden Sie unter [{{site.data.keyword.cloud_notm}} Private einrichten](/docs/services/blockchain/ICP_setup.html#icp-setup).

## Schritt 3: Konfigurieren Sie Ihre Zertifizierungsstellen

In Blockchain-Netzen, die auf Fabric basieren, muss als erste Komponente eine Zertifizierungsstelle bereitgestellt werden. Dies liegt daran, dass die Konfiguration einer Komponente mindestens eine Benutzeridentität beinhalten muss, die zum Betreiben der Komponente berechtigt ist, bevor diese bereitgestellt wird.

Die konzeptionellen Aspekte sind in diesem Zusammenhang bisweilen nicht einfach nachzuvollziehen. Beispielsweise kann ein elektronisches Gerät wie ein neues Laptop häufig von allen möglichen Personen genutzt werden. Die erste Person, die das Gerät einschaltet, wird als sein "Eigner" betrachtet und ist daher berechtigt, Kennwörter zu registrieren und aufzustellen, die jeder anschließende Benutzer kennen muss. Dies entspricht jedoch nicht der Art, in der die Komponenten in Fabric-basierten Blockchain-Netzen konfiguriert werden. Die Verwaltungsinformationen müssen bereits in die Peers und Anordnungsknoten integriert worden sein, wenn sie zum ersten Mal bereitgestellt werden.

**Es wird empfohlen, für jede Organisation eine Zertifizierungsstelle bereitzustellen.** Wenn Sie beispielsweise drei Peers bereitstellen, die einer einzigen Organisation zugeordnet sind, sowie einen Anordungsknoten, der einer anderen Organisation zugeordnet ist, müssen Sie zwei Zertifizierungsstellen bereitstellen. Eine Zertifizierungsstelle wird für die Organisation der Peers verwendet, die andere für die Organisation des Anordnungsknotens.

Um eine unendliche Regression von Zertifizierungsstellen zu verhindern (bei der jede Zertifizierungsstelle mit einer anderen Zertifizierungsstelle verknüpft werden muss usw. usf.), ist Zertifizierungsstellen ein Standardpaar aus Benutzername und Kennwort zugeordnet. Diesen Benutzernamen und das zugehörige Kennwort geben Sie bei der Bereitstellung der Zertifizierungsstelle an. Bei dieser Zertifizierungsstelle handelt es sich um die "Stammzertifizierungsstelle", also um das anerkannte Stammelement Ihrer Komponente. In Produktionsnetzen empfiehlt sich die Bereitstellung  von mindestens einer weiteren Zertifizierungsstelle, die ihr Zertifikat aus dieser Stammzertifizierungsstelle abruft, und die Verwendung dieser Zertifizierungsstelle, die als "Zwischenzertifizierungsstelle" bezeichnet wird, zur Ausgabe von Zertifikaten an Benutzer und Komponenten. Im vorliegenden Release wird die Verwendung von Zwischenzertifizierungsstellen allerdings nicht unterstützt.

Jede Organisation muss eine Zertifizierungsstelle für die Eintragung und eine TLS-Zertifizierungsstelle besitzen.  Wenn Sie eine Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private bereitstellen, wird standardmäßig in demselben Container eine TLS-Zertifizierungsstelle bereitgestellt. Diese TLS-Zertifizierungsstelle generiert und verwaltet Ihre TLS-Zertifikate. Die Zertifizierungsstelle in einem Starter Plan- oder Enterprise Plan-Netz enthält keine TLS-Zertifizierungsstelle, aber eine Zertifizierungsstelle, die für die Eintragung verwendet wird. Falls Sie Ihren Peer mit einem Starter Plan- oder Enterprise Plan-Netz verbinden wollen, müssen Sie daher vor der Bereitstellung des Peers auch eine [Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private bereitstellen](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy), die als TLS-Zertifizierungsstelle für diesen Peer dient. Lesen Sie in diesem Zusammenhang auch den Abschnitt [Voraussetzungen für die Verbindung eines {{site.data.keyword.cloud_notm}} Private-Peers mit {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-prerequisites). Zu beachten ist, dass die TLS-Zertifizierungsstelle ausschließlich für die Ausgabe von Zertifikaten verwendet wird und beendet werden kann, nachdem diese Aktivität abgeschlossen ist.

Weitere Informationen zu TLS finden Sie in der Hyperledger Fabric-Dokumentation unter [Securing Communication With Transport Layer Security (TLS) ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/enable_tls.html "Securing Communication With Transport Layer Security (TLS)").

### MSPs für Anordnungsknoten und Peers vorbereiten

Da der Prozess von {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private so umfangreich ist, empfiehlt sich während der Ersteinrichtung die Verwendung einer einzigen Administratorbenutzeridentität als Administrator für alle Netzkomponentenknoten. Dies sollte Bereitstellungs- und Verbindungsfehler reduzieren, weil hierdurch sichergestellt ist, dass ein einziger Benutzer die Konfiguration und Verbindungen zwischen verschiedenen Komponenten einrichten und deren ordnungsgemäße Funktionsweise sicherstellen kann. Es ist jedoch äußerst wichtig, dass jede Komponente über unterschiedliche Zertifikate verfügt. Manchmal ist die Unterscheidung hier leicht zu übersehen. Die Entität, die einen Transaktionsvorschlag signiert, ist nicht der Administrator des Peers, sondern der **Peer selbst**. Daher muss der Peer eingetragen sein und ein Zertifikat, das er jeder ausgeführten Aktion zuordnet, sowie einen privaten Schlüssel besitzen, mit dem er bestimmte Arten von Signaturen generieren kann. Weitere Informationen zu Identitäten und Berechtigungen in einem Fabric-basierten Blockchain-Netz finden Sie unter [Identity ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/identity/identity.html "Identity") und [Membership ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.3/membership/membership.html "Membership") in der Fabric-Dokumentation.

Der Fabric-CA-Client, der mithilfe der Anweisungen im Abschnitt [Zertifizierungsstelle betreiben](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) installiert werden sollte, wird zum Registrieren und Eintragen von Identitäten verwendet. Diese Identität ist der Administrator der Netzkomponenten, die Sie nachfolgend bereitstellen; mit ihrer Hilfe können Konfigurationen geändert und Komponenten miteinander verbunden werden. Falls Sie die Verwaltung dieser Komponenten später auf andere Benutzer übertragen wollen, können Sie bei Bedarf neue Administratoren für diese Komponenten registrieren und eintragen und sich selbst als Administrator entfernen.

Nach der Bereitstellung des Anordnungsknotens oder des Peers verwendet ein Container `init`, der dem Anordungsknoten oder Peer zugeordnet ist, ein Objekt mit einem geheimen Kubernetes-Schlüssel, um den MSP für die Komponente zu erstellen. Im Abschnitt [Zertifizierungsstelle betreiben](/docs/services/blockchain/howto/CA_operate.html#ca-operate) erfahren Sie, wie Sie ein Objekt mit einem geheimen Schlüssel erstellen. Wie bereits erläutert müssen Sie daran denken, eine Zertifizierungsstelle einzurichten und diesen Ablauf für jede Organisation zu wiederholen.

## Schritt 4: Stellen Sie Anordnungsknoten und Peers bereit

Nachdem ein geheimer Kubernetes-Schlüssel erstellt wurde, können Sie eine Komponente bereitstellen. Falls Sie die Bereitstellung von Kanälen beabsichtigen, empfiehlt es sich, vor den Peers den Anordnungsknoten bereitzustellen. Verwenden Sie unbedingt unterschiedliche Organisationsnamen für alle Ihre Komponenten.

- **[Stellen Sie den Anordnungsknoten bereit](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)**. Bitte beachten Sie, dass gegenwärtig nur der SOLO-Anordnungsservice unterstützt wird. Im Bereitstellungsprozess gibt es Optionen hinsichtlich der Berechnung.

- **[Stellen Sie den Peer bereit, der mit einem {{site.data.keyword.cloud_notm}} Private-Anordnungsknoten verbunden werden soll](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy)**. Im Helm-Diagramm können Sie eine Reihe von Optionen für die Bereitstellung des Peers (einschließlich des Datenbanktyps) angeben. Stellen Sie sicher, dass sich die MSP-ID für die Organisation des Peers von der MSP-ID für die Organisation des Anordnungsknotens unterscheidet.

- **[Stellen Sie den Peer bereit, der mit einem {{site.data.keyword.blockchainfull_notm}} Platform-Netz verbunden werden soll](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy)**. Der Prozess für die Bereitstellung eines Peers und seine Verbindung mit einem [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about)- oder [Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise_plan-about)-Netz {{site.data.keyword.cloud_notm}} verläuft anders, weil Verbindungsprofile und die Network Monitor-Benutzerschnittstelle verwendet werden. Bitte beachten Sie, dass die Organisation, zu der der Peer gehört, bereits an einem Kanal im Netz teilnehmen muss. Achten Sie auch hier darauf, dass sich die MSP-ID für die Organisation des Peers von der MSP-ID für die Organisation des Anordnungsknotens unterscheidet.

## Nächste Schritte

Nachdem Sie alle Knoten bereitgestellt haben, können Sie mit dem Betrieb beginnen und Transaktionen übergeben. Weitere Informationen finden Sie unter den folgenden Links:

- [Zertifizierungsstelle in {{site.data.keyword.cloud_notm}} Private betreiben](/docs/services/blockchain/howto/CA_operate.html#ca-operate)
- [Anordnungsknoten in {{site.data.keyword.cloud_notm}} Private betreiben](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate)
- [Peers in {{site.data.keyword.cloud_notm}} Private mit einem Multi-Cloud-Netz betreiben](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)
- [Peers in {{site.data.keyword.cloud_notm}} Private mit Starter Plan oder Enterprise Plan betreiben](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)

## Netz vergrößern

Falls Sie beabsichtigen, eine Entwicklungsumgebung oder einen
Machbarkeitsnachweis zu konfigurieren, müssen Sie Peerorganisationen zum
Systemkanal des Anordnungsknotens hinzufügen, der während der
Bereitstellung des Anordnungsknotens erstellt wird. Hierbei handelt es sich um einen mehrstufigen Prozess, der alle Komponenten nutzt und in den entsprechenden Themen über den Betrieb dokumentiert ist.

1. Bevor Sie eine Organisation hinzufügen können, müssen Sie sie erstellen und ihre MSP-ID festlegen. Lesen Sie hierzu den Abschnitt [Zertifizierungsstelle betreiben](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer).

2. Nachdem die Organisation erstellt worden ist, kann sie zum
Systemkanal des Anordnungsknotens hinzugefügt werden. Weitere Informationen finden Sie unter [Anordnungsknoten betreiben](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-add-organizations-to-consortium).

3. Sobald eine Organisation im Systemkanal des Anordungsknotens
aufgeführt wird, ist sie Mitglied des "Konsortiums" und in der Lage, einen Anwendungskanal zu erstellen, also solche Kanäle, auf denen Transaktionen stattfinden. Weitere Informationen zum Erstellen eines Kanals finden Sie unter [Kanal erstellen](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel).

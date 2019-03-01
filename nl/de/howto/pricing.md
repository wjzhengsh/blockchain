---

copyright:
  years: 2017, 2018
lastupdated: "2018-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Preisstruktur
{: #ibp-pricing}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


In diesem Abschnitt finden Sie Informationen zur Preisstruktur der Mitgliedschaftspläne für {{site.data.keyword.blockchainfull}} Platform und zu den Kosten, die beim Entwickeln und Erweitern Ihres Blockchain-Netzes entstehen.  
{:shortdesc}

Bei {{site.data.keyword.blockchainfull_notm}} Platform werden Organisationen, die Blockchain-Netze erstellen, monatliche Mitgliedschafts- und Peergebühren berechnet. Die Gebühren variieren je nach ausgewähltem Netzplan und den im Netz verwendeten Netzressourcen. Die folgende Tabelle enthält einen Überblick über die Preisstruktur bei {{site.data.keyword.blockchainfull_notm}} Platform.

| Preiskomponenten | Starter Plan-Kosten pro Monat | Enterprise Plan-Kosten pro Monat |
|-----|-----|-----|
| Mitgliedsgebühr | 250 $ | 1000 $ |
| Peergebühr | 125 $ | 1000 $ |

*Abbildung 1. Übersicht über die Preisstruktur bei {{site.data.keyword.blockchainfull_notm}} Platform*

Die monatliche Gebühr wird nach täglichen Anteilen in Rechnung gestellt. Beispiel: Ein Mitglied (zugeordnete Mitgliedsgebühr 1.000 $) von zwei Peers (Peergebühr 1.000 $ x 2 Peers) muss 3.000 $ pro Monat bezahlen. Wenn der Monat 30 Tage hat, bezahlt das Mitglied 100 $ (3.000 $/30) pro Tag. Weitere Informationen zur Bezahlung für Ihre Netze finden Sie unter [Zahlungsmodus](/docs/services/blockchain/howto/paying_mode.html#paying-mode).


## Basisnetzkomponenten
{: #ibp-pricing-components}

Um die Preisstruktur problemlos nachvollziehen zu können, müssen Sie mit den Basiskomponenten eines Netzes vertraut sein: {{site.data.keyword.blockchainfull_notm}} Platform ermöglicht das Erstellen eines Blockchain-Netzes, das auf Hyperledger Fabric basiert. Grundsätzlich besteht ein Fabric-Blockchain-Netz aus den folgenden Basiskomponenten:

-	**Organisation** – Eine Entität, bei der ein Bedarf für das Verwalten einer Kopie des Blockchain-Ledgers und das Validieren von Transaktionen besteht. In einer Firma kann es mehrere Blockchain-Organisationen geben.
-	**Peer** – Der zu einer Organisation gehörige Knoten, der das Blockchain-Ledger enthält und Transaktionen validiert. Peers sind einer bestimmten Blockchain-Organisation zugeordnet.
-	**Anordnungsservice** – Besteht aus einem einzelnen Anordnungsknoten (SOLO) oder einer Gruppe von Anordnungsknoten. Der Anordnungsservice ordnet die Transaktionen, erstellt Blöcke und sendet Blöcke zur Validierung an Peers.
-	**Zertifizierungsstelle (CA = Certificate Authority)** – Gibt digitale Zertifikate zu Identifikationszwecken an interaktive Netzkomponenten aus.

{{site.data.keyword.blockchainfull_notm}} Platform stellt zwei Mitgliedschaftspläne in {{site.data.keyword.cloud_notm}} zur Auswahl: den **Starter Plan** und den **Enterprise Plan**. Beide Pläne ermöglichen das Erstellen von Organisationen und stellen eine Zertifizierungsstelle zur Verfügung. Die Pläne unterscheiden sich in Bezug auf die Peers, die Zertifizierungsstellen und den Anordnungsservice.

Der Enterprise Plan stellt hoch verfügbare Zertifizierungsstellen und Peers mit einem fehlertoleranten Anordnungsservice zur Verfügung. Der Starter Plan beinhaltet keine Hochverfügbarkeitsoptionen und verwendet einen Basisanordnungsservice (SOLO). Der Starter Plan ist dementsprechend als Einstieg für {{site.data.keyword.blockchainfull_notm}} Platform in Umgebungen für Entwicklungs- und Testzwecke sowie in Umgebungen für Machbarkeitsnachweise geeignet. Der Enterprise Plan ist die ideale Option für Netze, die für Pilot- und Produktionsumgebungen ausgelegt sind.

## Schlüsselelemente der Preisstruktur
{: #ibp-pricing-key-elements}

Der Starter Plan beinhaltet ebenso wie der Enterprise Plan zwei für den Preis relevante Elemente:

- **Mitgliedsgebühr** – Deckt das Erstellen von Organisationen, den Zugriff auf den Anordnungsservice und auf die Zertifizierungsstelle ab und wird **pro Instanz** berechnet. In dieser Gebühr eingeschlossen ist die Bearbeitung des Anordnungsservice und der Zertifizierungsstelle für Ihr Netz durch {{site.data.keyword.blockchainfull_notm}} Platform. Diese Gebühr ist für den Zugriff auf ein unter {{site.data.keyword.blockchainfull_notm}} Platform erstelltes Netz erforderlich.

  -	Der Starter Plan lässt eine *unbegrenzte* Anzahl von Organisationen pro Mitgliedschaft zu und ermöglicht einen Wechsel zwischen Organisationen im Network Monitor. Da der Starter Plan für Umgebungen für Entwicklungs- und Testzwecke sowie für Umgebungen für Machbarkeitsnachweise konzipiert ist, können Sie Simulationen in Umgebungen mit mehreren Organisationen durchführen. **Hinweis**: Der gesamte Netzspeicher, einschließlich des Speicherbedarfs für die Komponenten, den Chaincode und die Ledgerdaten, ist auf 20 GB begrenzt. Die simulierten Organisationen nutzen die 20 GB an Speicher gemeinsam im Blockchain-Netz.

  -	Der Enterprise Plan lässt eine Organisation pro Mitgliedschaft zu. Da der Enterprise Plan für Pilot- und Produktionsumgebungen ausgelegt ist, sind Sie mit Ihrer jeweiligen Organisation verknüpft.

-	**Peergebühr** – Deckt den Betrieb von Peers einer Organisation ab und wird **pro Peer** berechnet. Diese Gebühr ist nur erforderlich, wenn die Verwaltung einer Ledger-Kopie und das Validieren von Transaktionen über einen Peer erfolgen soll.

### Beispiel für Mitgliedsgebühren
{: #ibp-pricing-example}

In Abbildung 2 werden die Mitgliedsgebühren anhand eines Beispiels mit Netzinstanzen erläutert. In der Abbildung werden über das betreffende {{site.data.keyword.cloud_notm}}-Konto drei Netzinstanzen bereitgestellt: eine Enterprise Plan-Instanz mit der Bezeichnung *Blockchain-11* und zwei Starter Plan-Instanzen mit den Bezeichnungen *Blockchain-cz* und *Blockchain-da*. Für jede Instanz ist ein eigener Anordnungsservice sowie eine Zertifizierungsstelle erforderlich. In diesem Fall entstehen für das betreffende {{site.data.keyword.cloud_notm}}-Konto drei Mitgliedsgebühren (jeweils eine Mitgliedsgebühr für die einzelnen Netzinstanzen).

![Blockchain-Netzinstanzen](../images/ibp_instance_example.png "Blockchain-Netzinstanzen")  
*Abbildung 2. Blockchain-Netzinstanzen*


## Preisstruktur des Starter Plans
{: #ibp-pricing-starter-pricing}

Starter Plan-Netze werden mit einer Standardkonfiguration bestehend aus zwei Organisationen und einem Peer pro Organisation für eine Monatsgebühr von insgesamt 500 $ bereitgestellt. Bei dieser Standardkonfiguration ist die Umgebung auch ohne weitere Bearbeitung startbereit. Sie können weitere Organisationen hinzufügen, ohne dass dadurch Gebühren entstehen, und für 125 $ pro Monat und Peer weitere Peers zu den Organisationen hinzufügen. Die daraus resultierende Rechnung ist in Abbildung 3 zu sehen. Bei der Rechnung wird davon ausgegangen, dass Sie ein Starter Plan-Netz mit der Standardkonfiguration erstellen bzw. an einem derartigen Netz teilnehmen.

| Preiskomponenten | Kosten pro Monat |
|-----|----------------|
| Mitgliedsgebühr | 250 $ |
| Peergebühr | 125 $ |
| Peergebühr | 125 $ |
| Gebühren zum Monatsende | 500 $ |

*Abbildung 3. Gebühren für ein Starter Plan-Standardnetz*

### Beispiele für die Preisstruktur des Starter Plans
{: #ibp-pricing-starter-plan-pricing-example}

#### Zusätzliche Peers
{: #ibp-pricing-additional-peers}

Die Anzahl der Peers, die Sie zu Ihrem Netz hinzufügen können, ist beim Starter Plan nicht begrenzt. Wenn Sie zwei Peers (einen Peer für jede der beiden Organisationen) zum Starter Plan-Standardnetz hinzufügen, erhöht sich die Rechnung um 250 $ pro Monat. Die daraus resultierende Rechnung ist in Abbildung 4 zu sehen. Bei der Rechnung wird davon ausgegangen, dass Sie am Anfang des ersten Monats zwei zusätzliche Peers hinzufügen, wenn Sie ein Starter Plan-Netz erstellen oder an einem Starter Plan-Netz teilnehmen.

| Preiskomponenten | Kosten pro Monat |
|-----|----------------|
| Mitgliedsgebühren für Netz 1 | 250 $ |
| Peergebühr | 125 $ |
| Peergebühr | 125 $ |
| Peergebühr | 125 $ |
| Peergebühr | 125 $ |
| Summe | 750 $ |
| Gebühren zum Ende des ersten Monats | 750 $ |

*Abbildung 4. Gebühren für ein Starter Plan-Standardnetz mit zwei zusätzlichen Peers*

#### Zusätzliche Netze
{: #ibp-pricing-additional-networks}
Auch die Anzahl der Netzinstanzen, die Sie bereitstellen können, ist beim Starter Plan nicht begrenzt. Wenn Sie eine zweite Starter Plan-Netzinstanz bereitstellen, entstehen Gebühren für eine zweite Mitgliedschaft sowie weitere Peergebühren für das zweite Netz. Ihre Rechnung für die Verwendung der Standardnetzkonfiguration erhöht sich somit um 500 $ pro Monat. Möglicherweise wird es für Sie erforderlich, zusätzliche Starter-Netze für Ihre voneinander getrennten Umgebungen für Entwicklungszwecke, Testzwecke und Machbarkeitsnachweise einzusetzen. Ein Beispiel für dieses Szenario ist eine Situation, in der den Qualitätsentwicklern wichtige Funktionstests unabhängig von der Entwicklungsumgebung ermöglicht werden sollen.

Die daraus resultierende Rechnung ist in Abbildung 5 zu sehen. Bei der Rechnung wird davon ausgegangen, dass Sie am Anfang des ersten Monats ein zusätzliches Netz hinzufügen, wenn Sie ein Starter Plan-Netz erstellen oder an einem Starter Plan-Netz teilnehmen.

| Preiskomponenten | Kosten pro Monat |
|-----|----------------|
| Mitgliedsgebühren für Netz 1 | 250 $ |
| Peergebühr | 125 $ |
| Peergebühr | 125 $ |
| Mitgliedsgebühren für Netz 2 | 250 $ |
| Peergebühr | 125 $ |
| Peergebühr | 125 $ |
| Gebühren zum Ende des ersten Monats | 1000 $  |

*Abbildung 5. Gebühren für zwei Starter Plan-Standardnetze*

#### Anzahl der Peers verringern
{: #ibp-pricing-removing-peers}

Sie können auch auf einen Peer in der Starter Plan-Standardnetzkonfiguration verzichten. In diesem Fall verringert sich Ihr Rechnungsbetrag jedoch um 125 $ pro Monat. Dieses Szenario entspricht möglicherweise Ihren Vorstellungen, wenn Sie mit einem anderen Mitglied in einer Starter Plan-Umgebung zusammenarbeiten und für jeden ein Peer ausreicht. Die daraus resultierende Rechnung ist in Abbildung 6 zu sehen. Bei der Rechnung wird davon ausgegangen, dass Sie auf einen Peer im Starter Plan-Netz verzichten.

| Preiskomponenten | Kosten pro Monat |
| ----- | ---------------- |
| Mitgliedsgebühren für Netz 1 | 250 $ |
| Peergebühr | 125 $ |
| Peergebühr | - |
| Gebühren zum Ende des ersten Monats |  375 $ |

*Abbildung 6. Gebühren für ein Starter Plan-Standardnetz nach der Entfernung eines Peers*


## Preisstruktur des Enterprise Plans
{: #ibp-pricing-enterprise-plan}

Bei {{site.data.keyword.blockchainfull_notm}} Platform ist keine Standardkonfiguration für Enterprise Plan-Netze vorgesehen. Sie können mit der von Ihnen gewünschten Konfiguration beginnen. Wenn Sie sich für den Enterprise Plan entscheiden, sollten Sie sich darüber im Klaren sein, welche Netzkonfiguration Sie benötigen. Für ein hoch verfügbares Netz ist ein Minimum von zwei Peers pro Organisation dringend angeraten, um Ihre Organisation wirksam vor einem Netzausfall zu schützen.

Wenn Sie in einem Enterprise Plan-Netz mit einem anderen Netzmitglied zusammenarbeiten und jeder zwei Peers zur Organisation hinzufügt, entspricht die Rechnung der in Abbildung 7 dargestellten Rechnung.

| Preiskomponenten | Kosten pro Monat |
|-----|----------------|
| Mitgliedsgebühr | 1000 $ |
| Peergebühr | 1000 $ |
| Peergebühr | 1000 $ |
| Gebühren zum Monatsende | 3000 $ |

*Abbildung 7. Gebühren für ein Enterprise Plan-Basisnetz*

### Beispiele für die Preisstruktur des Enterprise Plans
{: #ibp-pricing-enterprise-plan-pricing}


#### Zusätzliche Peers
{: #ibp-pricing-enterprise-additional-peers}

Bezogen auf das vorausgehende Beispiel werden bei diesem Beispiel zwei weitere Peers zur Organisation hinzugefügt. Ihre Rechnung erhöht sich um 2000 $ pro Monat. Sie entspricht der in Abbildung 8 dargestellten Rechnung:

| Preiskomponenten | Kosten pro Monat |
|-----|----------------|
| Mitgliedsgebühr | 1000 $ |
| Peergebühr | 1000 $ |
| Peergebühr | 1000 $ |
| Peergebühr | 1000 $ |
| Peergebühr | 1000 $ |
| Gebühren zum Ende des ersten Monats | 5000 $ |

*Abbildung 8. Gebühren für ein Enterprise Plan-Netz mit vier Peers*

Wenn das andere Mitglied dieselbe Netzkonfiguration beibehält, entspricht die Rechnung dieses Mitglieds weiterhin der in Abbildung 7 dargestellten Rechnung.

#### Zusätzliche Netze
{: #ibp-pricing-enterprise-additional-networks}

Die Anzahl der Netzinstanzen, die Sie bereitstellen bzw. an denen Sie teilnehmen können, ist auch beim Enterprise Plan nicht begrenzt. Wenn Sie ein zweites Enterprise Plan-Netz mit derselben Basisnetzkonfiguration (eine Organisation mit zwei Peers) erstellen, entspricht Ihre Rechnung der in Abbildung 11 dargestellten Rechnung. Möglicherweise entspricht dieses Szenario Ihren Vorstellungen, wenn Sie mehrere Netze erstellt haben und/oder an mehreren Netzen teilnehmen.

| Preiskomponenten | Kosten pro Monat |
|-----|----------------|
| Mitgliedsgebühren für Netz 1 | 1000 $ |
| Peergebühr | 1000 $ |
| Peergebühr | 1000 $|
| Mitgliedsgebühren für Netz 2 | 1000 $ |
| Peergebühr | 1000 $ |
| Peergebühr | 1000 $|
| Summe | 6000 $ |

*Abbildung 9. Gebühren für zwei Enterprise Plan-Netze mit Basisnetzkonfiguration*

Wenn das andere Mitglied lediglich ein Enterprise Plan-Netz verwendet und dieselbe Netzkonfiguration beibehält, entspricht die Rechnung dieses Mitglieds weiterhin der in Abbildung 7 dargestellten Rechnung.

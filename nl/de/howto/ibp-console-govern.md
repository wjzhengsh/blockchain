---

copyright:
  years: 2019
lastupdated: "2019-04-23"

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

# Komponenten regulieren
{: #ibp-console-govern}

Nach der Erstellung von Zertifizierungsstellen, Peers, Anordnungsknoten und Kanälen können Sie diese Komponenten über die Konsole aktualisieren.
{:shortdesc}

**Zielgruppe:** Dieser Abschnitt richtet sich an Netzoperatoren, die für die Erstellung, Überwachung und Verwaltung des Blockchain-Netzes verantwortlich sind.  

## Interaktion des {{site.data.keyword.cloud_notm}} Kubernetes-Service mit der Konsole
{: #ibp-console-govern-iks-console-interaction}

Es liegt in der Verantwortung des Netzoperators, CPU-, Hauptspeicher- und Speichernutzung zu überwachen und sicherzustellen, dass ausreichend Ressourcen verfügbar sind, **bevor** versucht wird, einen Knoten zu erstellen oder seine Größe zu ändern.
{:important}

Da Ihre Instanz der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole und Ihr {{site.data.keyword.cloud_notm}} Kubernetes Service-Cluster nicht direkt über die Ressourcen kommunizieren, die in Ihrem Cluster verfügbar sind, muss der Prozess zum Bereitstellen oder Ändern der Größe von Komponenten über die Konsole diesem Muster folgen:

1. **Größe der Bereitstellung, die Sie vornehmen möchten**. Die Anzeigen für die **Ressourcenzuordnung** für die Zertifizierungsstelle, den Peer und den Anordnungsknoten in der Konsole bieten standardmäßige CPU-, Hauptspeicher- und Speicherzuordnungen für jeden Knoten. Möglicherweise müssen Sie diese Werte in Übereinstimmung mit Ihrem Anwendungsfall anpassen. Wenn Sie sich nicht sicher sind, beginnen Sie mit den Standardzuordnungen und passen Sie sie mit zunehmender Erkenntnis Ihrer Anforderungen an. In ähnlicher Weise zeigt die Anzeige **Ressourcenzuordnung** die vorhandenen Ressourcenzuordnungen an.

  Die auf diese Liste folgende Tabelle gibt Ihnen mit aktuellen Standardwerten für den Peer, den Anordnungsknoten und die Zertifizierungsstelle einen Überblick darüber, wie viel Speicher und Rechenleistung Sie in Ihrem Cluster benötigen.

2. **Überprüfen Sie, ob in Ihrem {{site.data.keyword.cloud_notm}} Kubernetes-Service-Cluster** genügend Ressourcen vorhanden sind. Zur Überwachung Ihrer Kubernetes-Ressourcen wird empfohlen, das Tool [{{site.data.keyword.cloud_notm}} SysDig ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/cloud/sysdig "IBM Cloud Monitoring mit Sysdig") in Kombination mit Ihrem {{site.data.keyword.cloud_notm}} Kubernetes-Dashboard zu verwenden. Wenn in Ihrem Cluster nicht genügend Speicherplatz vorhanden ist, um Ressourcen bereitzustellen oder ihre Größe zu ändern, müssen Sie die Größe Ihres {{site.data.keyword.cloud_notm}} Kubernetes Service-Clusters erhöhen. Weitere Informationen zum Erhöhen der Größe eines Clusters finden Sie unter [Cluster skalieren ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](/docs/containers?topic=containers-ca#ca "Cluster skalieren"). Wenn in Ihrem Cluster genügend Speicherplatz vorhanden ist, können Sie mit Schritt 3 fortfahren.
3. **Verwenden Sie die Konsole, um Ihren Knoten bereitzustellen oder seine Größe zu ändern**. Wenn der Workerknoten, auf dem der Pod ausgeführt wird, nicht genügend Ressourcen besitzt, können Sie Ihrem Cluster einen neuen, größeren Workerknoten hinzufügen und dann den vorhandenen Workerknoten löschen.

| **Komponente** (alle Container) | CPU (in Millicore) | CPU (in CPUs) | Hauptspeicher (in Megabyte) | Hauptspeicher (in Gigabyte) | Speicher (in Gigabyte) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **Peer**                       | 1100               | 1,1           | 2200                  | 2,2                   | 200                    |
| **Zertifizierungsstelle**                         | 300                | 0,3            | 600                   | 0,6                    | 10                     |
| **Anordnungsknoten**                    | 450                | 0,45           | 900                   | 0,9                    | 100                    |

Wenn ein Benutzer Gebühren minimieren möchte, ohne einen Knoten vollständig zu inaktivieren oder zu löschen, ist es möglich, einen Knoten auf ein Minimum von 0,001 CPU (1 Millicore) zu skalieren. Beachten Sie, dass der Knoten bei Verwendung einer solchen CPU-Menge nicht funktionsfähig ist.{:important}

## Ressourcen zuordnen
{: #ibp-console-govern-allocate-resources}

Während Benutzer eines kostenlosen Clusters für die ihren Knoten zugeordneten Container **Standardgrößen verwenden müssen**, können Benutzer von gebührenpflichtigen Clustern diese Werte während der Erstellung ihrer Knoten festlegen.

Die Anzeige **Ressourcenzuordnung** in der Konsole bietet Standardwerte für die verschiedenen Felder, die an der Erstellung eines Knotens beteiligt sind. Diese Werte wurden gewählt, weil sie einen guten Einstieg bieten. Allerdings ist jeder Anwendungsfall anders. Auch wenn dieses Thema Tipps zur Auswahl dieser Werte bietet, liegt es letztlich beim Benutzer, seine Knoten zu überwachen und die für sie zu passenden Größen zu finden. Daher ist es mit Ausnahme von Fällen, wo sich der Benutzer sicher ist, andere Werte als die Standardwerte zu benötigen, eine praktische Strategie, diese Standardwerte zu verwenden und später anzupassen. Eine Übersicht über die Leistung und den Umfang von Hyperledger Fabric, auf dem {{site.data.keyword.blockchainfull_notm}} Platform basiert, finden Sie im [Antworten auf Ihre Fragen zur Leistung und Skalierung von Hyperledger Fabric ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Blog zur Leistung und Skalierung von Hyperledger Fabric").

Alle Container, die einem Knoten zugeordnet sind, verfügen über Felder für **CPU** und **Hauptspeicher**, während bestimmte Container, die dem Peer, Anordnungsknoten und der Zertifizierungsstelle zugeordnet sind, auch über Felder für **Speicher** verfügen. Weitere Informationen zu den verschiedenen Speicheroptionen, die in {{site.data.keyword.cloud_notm}} für Sie verfügbar sind, finden Sie unter [Speicheroptionen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](/docs/containers?topic=containers-kube_concepts#kube_concepts "Optionen für Kubernetes-Speicher"). Untersuchen Sie die Optionen sorgfältig, bevor Sie entscheiden, welche Speicherklasse verwendet werden soll.

Obgleich CPU und Hauptspeicher über die Konsole und das {{site.data.keyword.cloud_notm}} Kubernetes Service Dashboard geändert werden können, kann nach der Erstellung eines Knotens der Speicher später nur mithilfe der {{site.data.keyword.cloud_notm}}-Befehlszeilenschnittstelle geändert werden.
{:note}

Jeder Knoten verfügt über einen Web-Proxy-Container für gRPC, der die Übertragungsebene zwischen der Konsole und einem Knoten booten kann. Dieser Container verfügt über feste Ressourcenwerte und ist in der Anzeige "Ressourcenzuordnung" enthalten, um eine genaue Schätzung des Speicherplatzbedarfs im Kubernetes-Cluster für den bereitzustellenden Knoten zu liefern. Da die Werte für diesen Container nicht änderbar sind, wird der gRPC-Web-Proxy in den folgenden Abschnitten nicht besprochen.

### Zertifizierungsstellen (CAs)
{: #ibp-console-govern-CA}

Im Gegensatz zu Peers und Anordnungsknoten, die aktiv am Transaktionsprozess beteiligt sind, sind Zertifizierungsstellen nur an der Registrierung und Eintragung von Identitäten und an der Erstellung eines MSP beteiligt. Dies bedeutet, dass sie weniger CPU und Hauptspeicher benötigen. Um eine CA zu belasten, müsste ein Benutzer sie mit Anforderungen (wahrscheinlich mit APIs und einem Script) überfordern oder so viele Zertifikate ausgegeben haben, dass der Speicher nicht mehr ausreicht. Im Rahmen typischer Operationen sollte keine dieser Situationen eintreten, wenngleich diese Werte wie immer nur die Bedürfnisse eines bestimmten Anwendungsfalls widerspiegeln.

Die Zertifizierungsstelle verfügt über nur einen zugeordneten Container, der angepasst werden kann:

* **Die Zertifizierungsstelle selbst**: Kapselt die internen Prozesse der Zertifizierungsstelle, z. B. die Registrierung und den Eintrag von Knoten und Benutzern, sowie das Speichern einer Kopie jedes Zertifikats, das von ihr ausgegeben wird.

#### Ändern der Größe einer Zertifizierungsstelle während der Erstellung
{: #ibp-console-govern-CA-sizing-creation}

Die Zertifizierungsstelle verfügt nur über einen einzigen Container mit Werten, die Sie sorgfältig wählen müssen, da die Werte des gRPC-Web-Proxy-Servers nicht änderbar sind.

| Ressourcen | Bedingung für eine Erhöhung |
|-----------------|-----------------------|
| **CPU und Hauptspeicher des Containers für Zertifizierungsstellen** | Wenn Sie erwarten, dass Ihre Zertifizierungsstelle von Registrierungen und Eintragungen überfordert wird. |
| **Speicher der Zertifizierungsstelle** | Wenn Sie beabsichtigen, diese Zertifizierungsstelle zu verwenden, um eine große Anzahl von Benutzern und Anwendungen zu registrieren. |

### Peers
{: #ibp-console-govern-peers}

Der Peer verfügt über drei zugeordnete Container, die wir anpassen können:

- **Der Peer selbst**: Kapselt die internen Peer-Prozesse (z. B. die Prüfung von Transaktionen) und die Blockchain (d.h. das Transaktionsprotokoll) für alle Kanäle, denen er angehört. Beachten Sie, dass der Speicher des Peers auch die auf dem Peer installierten Smart Contracts enthält.
- **CouchDB**: Gibt an, wo die Statusdatenbanken des Peers gespeichert sind. Denken Sie daran, dass jeder Kanal eine eigene Statusdatenbank besitzt.
- **Smart Contract**: Denken Sie daran, dass während einer Transaktion der betreffende Smart Contract "aufgerufen" (also ausgeführt) wird. Beachten Sie, dass alle Smart Contracts, die Sie auf dem Peer installieren, in separaten Containern innerhalb Ihres Smart Contract-Containers ausgeführt werden, der auch als Docker-in-Docker-Container bezeichnet wird.

#### Ändern der Größe eines Peers während der Erstellung
{: #ibp-console-govern-peers-sizing-creation}

Wie in unserem Abschnitt zur [Interaktion von Kubernetes mit der Konsole](#ibp-console-govern-iks-console-interaction) erwähnt, wird empfohlen, die Standardwerte für diese Peer-Container zu verwenden und sie später anzupassen, wenn sich herausstellt, wie diese Peer-Container genutzt werden.

| Ressourcen | Bedingung für eine Erhöhung |
|-----------------|-----------------------|
| **CPU und Hauptspeicher des Peer-Containers** | Wenn Sie von Beginn an einen hohen Transaktionsdurchsatz erwarten. |
| **Peer-Speicher** | Wenn Sie davon ausgehen, dass auf diesem Peer viele Smart Contracts installiert werden und er an vielen Kanälen teilnimmt. Denken Sie daran, dass dieser Speicher auch dazu verwendet wird, Smart Contracts aus allen Kanälen zu speichern, mit denen der Peer verbunden ist. Wie gesagt, wird von einer "kleinen" Transaktion im Bereich von 10.000 Bytes (10k) ausgegangen. Da der Standardspeicher 100 G beträgt, bedeutet dies, dass insgesamt bis zu 10 Millionen Transaktionen in den Peer-Speicher passen, bevor er erweitert werden muss (in der Praxis wird die maximale Anzahl kleiner sein, da Transaktionen in der Größe variieren können und diese Zahl keine Smart Contracts berücksichtigt). Während 100 G also viel größer als erforderlich erscheinen mag, ist zu beachten, dass Speicher relativ preiswert ist und dass der Prozess zur Erhöhung des Speichers schwieriger ist (eine Befehlszeile erfordert) als die Erhöhung der CPU oder des Hauptspeichers. |
| **CPU und Hauptspeicher des CouchDB-Containers** | Wenn Sie von einem hohen Volumen an Abfragen einer umfangreichen Statusdatenbank ausgehen. Dieser Effekt kann durch die Verwendung von [Indizes ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html#couchdb-indexes "Hyperledger Fabric - Index-Dokumentation") etwas gemindert werden. Dennoch können hohe Datenträger CouchDB belasten, was zu Zeitlimitüberschreitungen bei Abfragen und Transaktionen führen kann. |
| **CouchDB- (Ledgerdaten-) Speicher** | Wenn Sie einen hohen Durchsatz auf vielen Kanälen erwarten und nicht vorhaben, Indizes zu verwenden. Jedoch beträgt der Standard-CouchDB-Speicher wie der Peerspeicher 100 G, was signifikant ist. |
| **CPU und Hauptspeicher des Smart Contract-Containers** | Wenn Sie einen hohen Durchsatz auf einem Kanal erwarten, insbesondere in Fällen, in denen mehrere Smart Contracts gleichzeitig aufgerufen werden.|

### Anordnungsknoten
{: #ibp-console-govern-ordering-nodes}

Da Anordnungsknoten die Statusdatenbank nicht pflegen und keine Smart Contracts hosten, benötigen sie weniger Container als Peers. Sie hosten allerdings die Blockchain (das Transaktionsprotokoll), da in der Blockchain die Kanalkonfiguration gespeichert ist und der Anordnungsknoten Kenntnis der aktuellen Kanalkonfiguration haben muss, um seine Rolle erfüllen zu können.

Ähnlich wie die Zertifizierungsstelle verfügt der Anordnungsknoten nur über einen zugeordneten Container, der angepasst werden kann:

* **Der Anordnungsknoten selbst**: Kapselt die internen Prozesse des Anordnungsknotens (z. B. die Prüfung von Transaktionen) und die Blockchain für alle von ihm gehosteten Kanäle. 

#### Ändern der Größe eines Anordnungsknotens während der Erstellung
{: #ibp-console-govern-peers-sizing-creation}

Wie in unserem Abschnitt zur [Interaktion von Kubernetes mit der Konsole](#ibp-console-govern-iks-console-interaction) erwähnt, wird empfohlen, die Standardwerte für diese Container des Anordnungsknotens zu verwenden und sie später anzupassen, wenn sich herausstellt, wie diese Container des Anordnungsknotens genutzt werden.

| Ressourcen | Bedingung für eine Erhöhung |
|-----------------|-----------------------|
| **CPU und Hauptspeicher des Containers für den Anordnungsknoten** | Wenn Sie von Beginn an einen hohen Transaktionsdurchsatz erwarten. |
| **Speicher des Anordnungsknotens** | Wenn Sie erwarten, dass dieser Anordnungsknoten Teil eines Anordnungsservice auf vielen Kanälen sein wird. Denken Sie daran, dass Anordnungsknoten eine Kopie der Blockchain für jeden Kanal, dem sie angehören, behalten. Der Standardspeicher für den Anordnungsknoten beträgt 100 G, so wie der Container für den Peer selbst. |

Die CPU und den Hauptspeicher Ihrer Anordnungsknoten etwa doppelt so groß einzurichten wie Ihre Peers, ist zwar nicht zwingend, wird aber als bewährteste Methode angesehen. Wird ein einzelner Anordnungsknoten überbeansprucht, kann es zu Zeitlimitüberschreitungen und zur Löschung von Transaktionen kommen, sodass Transaktionen erneut übergeben werden müssen. Dies schadet einem Netzwerk viel mehr als ein einzelner Peer, der nur mit Mühe betrieben werden kann. In einer Raft-Anordnungsservice-Konfiguration kann es vorkommen, dass ein überlasteter Führungsknoten keine Überwachungssignalnachrichten mehr sendet, eine Führungswahl und ein zeitweiliges Aussetzen der Transaktionenanordnung auslöst. Ebenso kann ein nachfolgender Knoten Nachrichten verpassen und versuchen, eine Führungswahl auszulösen, die nicht erforderlich ist.{:important}

## Ressourcen erneut zuordnen
{: #ibp-console-govern-reallocate-resources}

Nachdem Sie die Größe eines Knotens geändert haben, kann es zu einer Verzögerung kommen, bis die Änderung wirksam wird, weil die Container neu erstellt werden müssen.
{:important}

Wie bereits erwähnt, wird empfohlen, eine [{{site.data.keyword.cloud_notm}} SysDig ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://www.ibm.com/cloud/sysdig "{{site.data.keyword.cloud_notm}} Überwachung mit dem Tool Sysdig") in Kombination mit Ihrem {{site.data.keyword.cloud_notm}} Kubernetes-Dashboard zu verwenden, um die Nutzung Ihrer Kubernetes-Ressource zu überwachen. Wenn Sie feststellen, dass ein Workerknoten nicht genügend Ressourcen besitzt, können Sie Ihrem Cluster einen neuen, größeren Workerknoten hinzufügen und dann den vorhandenen Workerknoten löschen.
{:note}

Wenngleich es einfacher ist, genügend Ressourcen im {{site.data.keyword.cloud_notm}} Kubernetes-Service bereitgestellt zu haben und die Pods und Workerknoten bedarfsgerecht erweitern zu können, ohne erst ihre Bereitstellung im {{site.data.keyword.cloud_notm}} Kubernetes-Service zu erweitern, erhöhen sich die Kosten der Bereitstellung im {{site.data.keyword.cloud_notm}} Kubernetes-Service mit zunehmender Größe. Benutzer müssen ihre Optionen sorgfältig prüfen und sich der Beeinträchtigungen bewusst sein, die sie mit der jeweiligen Option in Kauf nehmen.

Sie können die Ressourcen, die Sie den Ihrem Knoten zugewiesenen Containern zuordnen, mit einer der folgenden Methoden erneut zuordnen.

1. **Verwenden Sie den {{site.data.keyword.cloud_notm}} Kubernetes-Service-Autoscaler**. Der Autoscaler skaliert Ihre Workerknoten als Reaktion auf Ihre Pod-Spezifikationseinstellungen und Ressourcenanforderungen nach oben oder unten. Weitere Informationen zum {{site.data.keyword.cloud_notm}} Kubernetes-Service-Autoscaler und seiner Einrichtung finden Sie unter [Cluster skalieren ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](/docs/containers?topic=containers-ca#ca "Cluster skalieren") in der Dokumentation zu IBM {{site.data.keyword.cloud_notm}}. Beachten Sie, dass die Anpassung Ihrer Ressourcen durch den Autoscaler zu Gebühren für Ihr {{site.data.keyword.cloud_notm}} Kubernetes-Service-Konto entsprechend Ihrer Nutzung führt.
2. **Manuell skalieren**. Dies umfasst die Überwachung Ihrer Nutzung in der Konsole und im {{site.data.keyword.cloud_notm}} Kubernetes-Service-Cluster. Während dies mehr manuelle Schritte erfordert als die Verwendung des Autoscsalers, entsteht der Vorteil, dass der Benutzer immer weiß, welche Gebühren für sein {{site.data.keyword.cloud_notm}} Kubernetes-Service-Konto anfallen.

Für eine manuelle Skalierung klicken Sie auf der Seite **Knoten** auf den Knoten, den Sie anpassen möchten, und klicken Sie dann auf die Registerkarte **Nutzung**. Es wird die Schaltfläche **Neuzuordnung** angezeigt, mit der die Registerkarte **Ressourcenzuordnung** geöffnet wird, die große Ähnlichkeit mit der Registerkarte hat, die bei der Erstellung des Knotens angezeigt wurde. Zum Verringern der Menge der verfügbaren Ressourcen geben Sie einfach niedrigere Werte an und klicken Sie auf dieser Registerkarte und der sich daraufhin öffnenden Seite **Zusammenfassung** auf **Ressourcen-Neuzuordnung**.

Zur Erhöhung von CPU und Hauptspeicher für einen Knoten verwenden Sie die Registerkarte **Ressourcenzuordnung**, in der Sie die Werte erhöhen können. Im weißen Feld am unteren Rand der Seite werden die neuen Werte addiert. Nachdem Sie auf **Ressourcen-Neuzuordnung** geklickt haben, setzt die Seite **Zusammenfassung** diesen Wert in eine **VPC**-Menge um, auf deren Grundlage Ihre Rechnung erstellt wird. Anschließend müssen Sie zu Ihrem {{site.data.keyword.cloud_notm}} Kubernetes Service Dashboard navigieren, um sicherzustellen, dass Ihr Cluster über genügend Ressourcen für diese Neuzuordnung verfügt. Ist dies der Fall, können Sie auf **Ressourcen-Neuzuordnung** klicken. Wenn keine ausreichenden Ressourcen verfügbar sind, müssen Sie die Größe des Clusters mithilfe des {{site.data.keyword.cloud_notm}} Kubernetes Service Dashboard erhöhen.

Die Methode zur Vergrößerung des Speichers hängt von der Speicherklasse ab, die Sie für Ihren Cluster ausgewählt haben. Lesen Sie hierzu nochmals die Dokumentation zur Erhöhung des Speichers im Abschnitt zu [Speicheroptionen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](/docs/containers?topic=containers-kube_concepts#kube_concepts "storage options).

Im Gegensatz zu CPU und Hauptspeicher, die über die Konsole erhöht werden können (wenn in Ihrem {{site.data.keyword.cloud_notm}} Kubernetes-Service-Cluster Ressourcen verfügbar sind), müssen Sie den Speicher Ihrer Knoten über die {{site.data.keyword.cloud_notm}}-Befehlszeilenschnittstelle erhöhen. Ein Lernprogramm hierzu finden Sie unter [Größe und IOPS Ihrer bestehenden Speichereinheit ändern ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](/docs/containers?topic=containers-file_storage#file_change_storage_configuration "Größe und IOPS Ihrer bestehenden Speichereinheit ändern").

## Anordnungsknoten optimieren
{: #ibp-console-govern-orderer-tuning}

Die Leistung einer Blockchain-Plattform kann von vielen Variablen wie Transaktionsgröße, Blockgröße, Netzgröße sowie von Grenzen der Hardware beeinflusst sein. Der Anordnungsknoten enthält eine Gruppe von Optimierungsparametern, die zusammen verwendet werden können, um den Durchsatz und die Leistung des Anordnungsknotens zu steuern. Sie können diese Parameter verwenden, um anzupassen, wie Ihr Anordnungsknoten Transaktionen verarbeitet, abhängig davon, ob Sie viele kleine häufige Transaktionen oder weniger, aber große Transaktionen mit geringerer Häufigkeit haben. Im Wesentlichen haben Sie die Kontrolle über die Entscheidung, wann die Blöcke abgeschnitten werden, basierend auf der Größe, Menge und Eingangsrate Ihrer Transaktionen.

Die folgenden Parameter sind in der Konsole verfügbar, indem Sie auf den Anordnungsknoten auf der Registerkarte **Knoten** und dann auf das Symbol **Einstellungen** klicken. Klicken Sie auf die Schaltfläche **Erweitert**, um die **Erweiterte Kanalkonfiguration** für den Anordnungsknoten zu öffnen.

### Stapelgröße
{: #ibp-console-govern-orderer-tuning-batch-size}

Die folgenden drei Parameter steuern gemeinsam, wann ein Block geschnitten wird, basierend auf einer Kombination aus der festgelegten maximalen Anzahl von Transaktionen in einem Block sowie der Blockgröße selbst.

- **Absolute maximale Byteanzahl**  
  Setzen Sie diesen Wert auf die größte Blockgröße in Byte, die vom Anordnungsknoten geschnitten werden kann. Es kann keine Transaktion größer als der Wert von `Absolute maximale Byteanzahl` sein. In der Regel kann diese Einstellung zwei bis zehn Mal größer als Ihre `bevorzugte maximale Byteanzahl` sein.    
  **Hinweis**: Die maximal zulässige Größe beträgt 99 MB.
- **Maximale Nachrichtenanzahl**   
  Legen Sie diesen Wert auf die maximale Anzahl von Transaktionen fest, die in einem einzelnen Block enthalten sein kann.
- **Bevorzugte maximale Byteanzahl**  
  Setzen Sie diesen Wert auf die größte Blockgröße in Byte; er muss jedoch geringer als `Absolute maximale Byteanzahl` sein. Eine Mindesttransaktionsgröße, die keine Bewilligungen enthält, beträgt etwa 1 KB.  Wenn Sie 1 KB pro erforderliche Bewilligung hinzufügen, beträgt eine typische Transaktionsgröße ca. 3-4 KB. Daher wird empfohlen, den Wert für die `bevorzugte maximale Byteanzahl` auf ungefähr `Maximale Nachrichtenanzahl * Erwartete durchschnittliche tx-Größe` festzulegen. Zur Laufzeit werden die Blocks diese Größe nach Möglichkeit nicht überschreiten. Wenn eine Transaktion eintrifft, die zur Überschreitung dieser Größe des Blocks führt, wird der Block abgeschnitten und ein neuer Block für diese Transaktion erstellt. Trifft jedoch eine Transaktion ein, die diesen Wert überschreitet, ohne dass die `absolute maximale Byteanzahl` überschritten wird, wird die Transaktion einbezogen. Wenn ein Block eintrifft, der größer als die `bevorzugte maximale Byteanzahl` ist, enthält er nur eine einzige Transaktion, deren Größe nicht größer als die `absolute maximale Byteanzahl` sein darf.

Zusammen können diese Parameter für einen optimalen Durchsatz Ihres Anordnungsknotens konfiguriert werden.

### Zeitlimit für Stapelbetrieb
{: #ibp-console-govern-orderer-tuning-batch-timeout}

Legen Sie den Wert für das **Zeitlimit** auf die Zeit in Sekunden fest, die gewartet wird, nachdem die erste Transaktion eintrifft, bevor der Block abgeschnitten wird. Wenn Sie diesen Wert zu niedrig eingestellt haben, besteht die Gefahr, dass Stapel nicht bis zu Ihrer bevorzugten Größe gefüllt werden. Legen Sie diesen Wert zu hoch fest, kann es sein, dass der Anordnungsknoten auf Blöcke wartet und die Gesamtleistung beeinträchtigt wird. Im Allgemeinen wird empfohlen, den Wert für das `Stapelzeitlimit` auf mindestens die `maximale Nachrichtenanzahl/maximale Anzahl Transaktionen pro Sekunde` festzulegen.

Wenn Sie diese Parameter ändern, beeinflussen Sie nicht das Verhalten der vorhandenen Kanäle für den Anordnungsknoten. Vielmehr werden alle Änderungen an der Konfiguration des Anordnungsknotens nur auf neue Kanäle angewandt, die Sie für diesen Anordnungsknoten erstellen.
{:important}

## Kanäle modifizieren
{: #ibp-console-govern-channels}

### Ankerpeers konfigurieren
{: #ibp-console-govern-channels-anchor-peers}

Da das organisationsübergreifende [Gossip ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Gossip-Datenverteilungsprotokoll") für die Funktionen von Serviceerkennung und privaten Daten aktiviert sein muss, muss für jede Organisation ein Ankerpeer vorhanden sein. Diese Ankerpeer ist kein spezieller Peer-**Typ**, sondern einfach der Peer, den die Organisation anderen Organisationen bekannt macht und über den gesamten Organisations-Gossip bootet. Aus diesem Grund muss in der Sammlungsdefinition für jede Organisation mindestens ein [Ankerpeer ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Ankerpeers") definiert sein.

Zum Konfigurieren eines Ankerpeers klicken Sie auf die Registerkarte **Kanäle** und öffnen Sie den Kanal, in dem der Smart Contract instanziiert wurde.
 - Klicken Sie auf die Registerkarte **Kanaldetails**.
 - Blättern Sie hinunter bis zur Ankerpeer-Tabelle und klicken Sie auf **Ankerpeer hinzufügen**.
 - Wählen Sie aus jeder Organisation in der Sammlungsdefinition mindestens einen Peer aus, der als Ankerpeer für die Organisation dienen soll. Aus Gründen der Redundanz können Sie in Erwägung ziehen, mehr als einen Peer aus jeder Organisation in der Sammlung auszuwählen.

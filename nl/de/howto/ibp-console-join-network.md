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
{:pre: .pre}

# Lernprogramm zum Teilnehmen an einem Netzes
{: #ibp-console-join-network}

{{site.data.keyword.blockchainfull}} Platform ist ein Blockchain-as-a-Service-Angebot, mit dem Sie Blockchain-Anwendungen und -Netze entwickeln, bereitstellen und betreiben können. In der [Übersicht zu den Blockchain-Komponenten](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview) erfahren Sie mehr zu den Blockchain-Komponenten und darüber, wie sie miteinander interagieren. Dieses Lernprogramm ist der zweite Teil der [Lernprogrammreihe für Beispielnetze](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial), in dem die Vorgehensweise zum Erstellen von Knoten in der {{site.data.keyword.blockchainfull_notm}} Platform-Konsole und dem Verbinden dieser Knoten mit einem Blockchain-Konsortium beschrieben wird, das in einem anderen Cluster gehostet ist.
{:shortdesc}


**Zielgruppe:** Dieser Abschnitt richtet sich an Netzoperatoren, die für die Erstellung, Überwachung und Verwaltung des Blockchain-Netzes verantwortlich sind.  

Wenn Sie die Konsole noch nicht unter Verwendung des {{site.data.keyword.cloud_notm}}-Kubernetes-Service in einem Kubernetes-Cluster bereitgestellt haben, finden Sie entsprechende Informationen hierzu im Abschnitt [Einführung in {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Sie können einen neuen Kubernetes-Cluster für die Konsolenimplementierung erstellen oder einen vorhandenen Kubernetes-Cluster in Ihrem {{site.data.keyword.cloud_notm}}-Konto verwenden. Nachdem Sie die {{site.data.keyword.blockchainfull_notm}} Platform-Instanz in Ihrem Kubernetes-Cluster implementiert haben, können Sie die Konsole starten, um Ihre Blockchain-Komponenten zu erstellen und zu verwalten.

Unabhängig davon, ob Sie einen bezahlten oder kostenlosen Kubernetes-Cluster bereitstellen, verwenden Sie das Kubernetes-Dashboard, um bei der Bereitstellung von Knoten und Erstellung von Kanälen ein besonderes Augenmerk auf die Ihnen zur Verfügung stehenden Ressourcen zu legen. Es liegt in Ihrer Verantwortung, Ihren Kubernetes-Cluster zu verwalten und bei Bedarf zusätzliche Ressourcen zu implementieren. Obwohl Komponenten erfolgreich in einem kostenlosen Cluster bereitgestellt werden können, gilt dennoch Folgendes: Je mehr Komponenten Sie hinzufügen, desto langsamer werden die Komponenten ausgeführt.
{: note}

## Lernprogrammreihe für Beispielnetze
{: #ibp-console-join-network-structure}

Diese dreiteilige Lernprogrammreihe führt Sie durch den Prozess der Erstellung und Verbindung eines relativ einfachen Hyperledger Fabric-Netzes mit mehreren Knoten, indem Sie mithilfe der {{site.data.keyword.blockchainfull_notm}} Platform 2.0-Konsole ein Netz in Ihrem Kubernetes-Cluster installieren und einen Smart Contract instanziieren.

* Im [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) erfahren Sie, wie Sie durch das Erstellen eines Anordnungsknotens und eines Peers ein Netz hosten.
* Im **Lernprogramm zum Teilnehmen an einem Netz** erfahren Sie, wie Sie durch Erstellen eines Peers und Verknüpfen des Peers mit einem Kanal an einem vorhandenen Netz teilnehmen können.
* Im [Lernprogramm zum Bereitstellen eines Smart Contract im Netz](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) erfahren Sie, wie Sie einen Smart Contract schreiben und in Ihrem Netz bereitstellen.

Sie können die Schritte in diesen Lernprogrammen verwenden, um ein Netz mit mehreren Organisationen in einem Cluster für Entwicklungs- und Testzwecke zu erstellen. Verwenden Sie das Lernprogramm zum **Erstellen eines Netzes**, wenn Sie ein Blockchain-Konsortium gründen möchten, indem Sie einen Anordnungsknoten erstellen und Organisationen hinzufügen. Verwenden Sie das Lernprogramm zum **Teilnehmen an einem Netz**, um einen Peer mit dem Netz zu verbinden. Indem Sie die Lernprogramme mit unterschiedlichen Konsortiumsmitgliedern durchführen, können Sie ein wirklich **verteiltes** Blockchain-Netz erstellen.  


In diesem Lernprogramm soll gezeigt werden, wie Sie einen Peer mit einem **vorhandenen** Netz verknüpfen. Es setzt voraus, dass ein Anordnungsknoten, der das Netz hostet, bereits in Ihrem Cluster oder in einem anderen {{site.data.keyword.blockchainfull_notm}} Platform-Cluster vorhanden ist. Wenn kein Netz vorhanden ist, dem Sie beitreten können, erfahren Sie im [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), wie Sie ein Netz erstellen können. Das Lernprogramm zum **Teilnehmen an einem Netz** führt Sie durch die Schritte zum Erstellen der folgenden Blockchain-Komponenten für `Org2`, die im blauen Kasten markiert sind:
![Struktur zum Teilnehmen an einem Netz](../images/ib2-join-network.png "Teilnehmen an einem Netz")  
*Abbildung 1. Struktur zum Teilnehmen an einem Netz*  
Führen Sie die Schritte im Lernprogramm zum **Teilnehmen an einem Netz** aus, um die folgenden Komponenten zu erstellen und die folgenden Aktionen auszuführen:

* **Eine Peerorganisation** `Org2`  
  Erstellen Sie die MSP-Definition (MSP, Membership Services Provider) für 'Org12', die die Organisation `Org2` definiert.
* **Ein Peer** `Peer Org2`   
  Das Blockchain-Ledger (`Ledger x` in der Abbildung oben) wird von verteilten Peers verwaltet. Der Peer wird mit [CouchDB ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) als Datenbank bereitgestellt.
* **Eine Zertifizierungsstelle (CA)** `Org2 CA`
  Eine Zertifizierungsstelle (CA) ist der Knoten, der Zertifikate für alle Organisationsmitglieder ausgibt. Sie erstellen eine Zertifizierungsstelle für die Peerorganisation `Org2`.
* **Einem Kanal beitreten**
  Dieses Lernprogramm beschreibt die Vorgehensweise zum Beitreten zu einem Kanal, der im Rahmen des [Lernprogramms zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) erstellt wurde. 

In diesem Lernprogramm werden **empfohlene Werte** für einige der Felder in der Konsole angegeben. Auf diese Weise können Sie die Namen und Identitäten in den Registerkarten und Dropdown-Listen leichter erkennen. Diese Werte sind zwar nicht obligatorisch, Sie werden sie jedoch als hilfreich empfinden. Nach jeder Tasks finden Sie eine Tabelle mit den empfohlenen Werten.
{:tip}

## Schritt 1: Zusätzliche Organisation und Einstiegspunkt zur Blockchain erstellen
{: #ibp-console-join-network-create-ca-org2}

Für jede Organisation, die Sie mit der Konsole erstellen möchten, sollten Sie mindestens eine Zertifizierungsstelle implementieren. Eine Zertifizierungsstelle (CA) ist der Knoten, der Zertifikate für alle Netzteilnehmer (Peers, Anordnungsknoten, Clients usw.) ausgibt. Mit diesen Zertifikaten, die ein öffentliches und ein privates Schlüsselpaar enthalten, können Netzteilnehmer miteinander kommunizieren, sich authentifizieren und letztendlich Transaktionen ausführen. Diese Zertifizierungsstellen erstellen alle Identitäten und Zertifikate, die zu Ihrer Organisation gehören. Außerdem definieren sie die Organisation an sich. Anschließend können Sie diese Identitäten verwenden, um Knoten zu implementieren, Ihr Netz zu betreiben und Transaktionen an die Blockchain zu übergeben. Weitere Informationen zu Ihrer Zertifizierungsstelle und zu den Identitäten, die Sie erstellen müssen, finden Sie im Abschnitt [Identitäten verwalten](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

In diesem Lernprogramm erstellen Sie eine Organisation und **eine Zertifizierungsstelle**.

### Zertifizierungsstelle für die Peerorganisation erstellen
{: #ibp-console-join-network-create-CA-org2CA}

Im Rahmen dieses Lernprogramms gibt Ihre Zertifizierungsstelle die öffentlichen und privaten Schlüssel für Ihre Benutzer und Knoten aus. Diese Identitäten werden nicht von {{site.data.keyword.IBM_notm}} verwaltet und die Schlüssel nicht in Ihrem Kubernetes-Cluster oder in der Konsole gespeichert. Sie werden nur im lokalen Speicher Ihres Browsers gespeichert. Stellen Sie daher sicher, dass Sie Ihre Identitäten und den MSP der Organisation exportieren. Wenn Sie versuchen, von einer anderen Maschine oder einem anderen Browser auf die Konsole zuzugreifen, müssen Sie diese Identitäten und Organisationsdefinitionen importieren.  
{:important}

Führen Sie die folgenden Schritte in der Konsole aus:  

1. Navigieren Sie zu der Registerkarte **Knoten** auf der linken Seite und klicken Sie auf **Zertifizierungsstelle hinzufügen**. In den sich aus vier Schritten zusammensetzenden Seitenanzeigen können Sie die Zertifizierungsstelle anpassen, die Sie erstellen möchten, und die Organisation, für die die Zertifizierungsstelle Schlüssel ausgibt.
2. Klicken Sie auf **{{site.data.keyword.cloud_notm}}** unter **Zertifizierungsstelle erstellen**.
3. Verwenden Sie die zweite Seitenanzeige, um der Zertifizierungsstelle einen **Anzeigename** zu geben. Der empfohlener Wert für diese Zertifizierungsstelle ist `Org2 CA`.
4. Geben Sie in der nächsten Anzeige die Administratorberechtigungsnachweise für die Zertifizierungsstelle an, indem als **Administrator-ID** den Wert `admin` festlegen und alle von Ihnen benötigten geheimen Schlüssel angeben. Im Rahmen dieses Lernprogramms wird jedoch die Verwendung von `adminpw`.
5. Klicken Sie auf **Weiter** und anschließend auf **Zertifizierungsstelle hinzufügen**.

**Task: Zertifizierungsstelle für die Peerorganisation erstellen**

  | **Feld** | **Anzeigename** | **Eintragungs-ID** | **Geheimer Schlüssel** |
  | ------------------------- |-----------|-----------|-----------|
  | **Zertifizierungsstelle erstellen** | Org2 CA  | admin | adminpw |

*Abbildung 2. Zertifizierungsstelle für die Peerorganisation erstellen*  
Nach der Bereitstellung der Zertifizierungsstelle verwenden Sie diese zum Erstellen des MSP für Ihre Organisation, zum Registrieren von Benutzern und zum Erstellen des Einstiegspunkts für ein Netz (den **Peer**).

### Zertifizierungsstelle zum Registrieren von Identitäten verwenden
{: #ibp-console-join-network-use-CA-org2}

Jeder Knoten oder jede Anwendung, die Sie erstellen möchten, benötigt öffentliche und private Schlüssel für die Teilnahme am Blockchain-Netz. Außerdem müssen Sie Administratorschlüssel für diese Knoten und Anwendungen erstellen, damit Sie sie über die Konsole verwalten können. Sie verwenden die Zertifizierungsstelle zum Erstellen von zwei Identitäten:

* **Organisationsadministrator** Diese Identität ermöglicht es Ihnen, Knoten über die Platform-Konsole zu bedienen.
* **Peeridentität** Diese Identität ermöglicht Ihnen die Implementierung eines Peers.

Führen Sie die folgenden Schritte aus, um diese Zertifikate zu generieren:

1. Navigieren Sie in der Konsole auf der Registerkarte **Knoten** zu der von Ihnen erstellten Zertifizierungsstelle `Org2 CA`.
2. Nachdem Sie Ihre Zertifizierungsstelle ausgewählt haben, müssen Sie einen Administrator für diese Organisation (`Org2`) sowie eine Identität für den Peer selbst registrieren. Sie sollten bereits eine Identität auf dieser Seite sehen; es ist der Administrator, den Sie für die Zertifizierungsstelle erstellt haben. Um die neuen Benutzer zu registrieren, klicken Sie auf die Schaltfläche **Benutzer registrieren**.
3. Geben Sie für den Organisationsadministrator die Eintragungs-ID `org2admin` an. Sie können jeden beliebigen Schlüssel verwenden, es wird jedoch im Rahmen des Lernprogramms die Verwendung von `org2adminpw` empfohlen. Klicken Sie auf **Weiter**.
4. Geben Sie im nächsten Schritt den Typ für diese Identität als `client` an und wählen Sie in der Dropdown-Liste eine der verbundenen Organisationen aus. Das Feld für die Zugehörigkeit (affiliation) ist für erweiterte Benutzer und wird in diesem Lernprogramm nicht verwendet. Die Einträge in der Liste sind Standardzugehörigkeiten von der Fabric-Zertifizierungsstelle. Wenn Sie mehr darüber erfahren möchten, wie Zugehörigkeiten von der Fabric-Zertifizierungsstelle verwendet werden, lesen Sie die Informationen im Abschnitt zum [Registrieren einer neuen Identität ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Wählen Sie für jetzt eine beliebige Zugehörigkeit aus der Liste aus, z. B. `org2`, und klicken Sie auf **Weiter**.
5. Sie können die Felder **Maximale Anzahl der Eintragungen** und **Attribute hinzufügen** leer lassen. Sie werden von diesem Lernprogramm nicht verwendet. Im Abschnitt zum [Registrieren von Identitäten](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) finden Sie jedoch Informationen zur Verwendung dieser Felder.
6. Nachdem der Organisationsadministrator registriert wurde, wiederholen Sie die Schritte zwei bis fünf für die Identität des Peers mit derselben Zertifizierungsstelle (`Org2 CA`). Weisen Sie dem Peer jedoch die Eintragungs-ID `peer2` zu. Wie zuvor wird Ihnen im Rahmen des Lernprogramms die Verwendung des geheimen Schlüssels `peer2pw` empfohlen. Dies ist eine Knotenidentität. Wählen Sie daher `Peer` als **Typ** für den nächsten Schritt aus. Ignorieren Sie dann wie bereits vorher die Felder **Maximale Anzahl der Eintragungen** und **Attribute**.

**Task: Benutzer registrieren**

  |  **Feld** | **Beschreibung** | **Eintragungs-ID** | **Geheimer Schlüssel** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Benutzer registrieren** |  Org2 admin | org2admin | org2adminpw |
  | | Peeridentität |  peer2 | peer2pw |

*Abbildung 3. Zertifizierungsstelle zum Registrieren von Benutzern verwenden*  

### MSP-Definition der Peerorganisation erstellen
{: #ibp-console-join-network-create-peers-org2}

Nachdem Sie nun die Zertifizierungsstelle des Peers erstellt und diese zum **Registrieren** der Organisationsidentitäten verwendet haben, müssen Sie eine formale Definition der Peerorganisation erstellen, die als MSP-Definition (MSP, Membership Services Provider) bezeichnet wird. Viele Peers können zu einer Organisation gehören. **Sie müssen nicht jedes Mal, wenn Sie einen Peer erstellen, auch eine neue Organisation erstellen.** Da Sie dieses Lernprogramm zum ersten Mal durchlaufen, erstellen Sie die MSP-ID für diese Organisation. Während der Erstellung des MSP werden Sie Zertifikate für die Identität `org2admin` generieren und sie zu Ihrer Konsolenwallet hinzufügen.

1. Navigieren Sie zur Registerkarte **Organisationen** in der linken Navigationsleiste und klicken Sie auf **MSP-Definition erstellen**.
2. Geben Sie Ihrem MSP einen Anzeigenamen, wie z. B. `Org2 MSP`, und eine ID, wie `org2msp`. Wenn Sie in diesem Feld eine eigene MSP-ID angeben möchten, müssen Sie die Spezifikationen zu den Einschränkungen für diesen Namen in der QuickInfo befolgen.
3. Geben Sie unter den **Details für die Stammzertifizierungsstelle** die Zertifizierungsstelle des Peers an, die Sie als Stammzertifizierungsstelle für Ihre Organisation erstellt haben. Wenn Sie dieses Lernprogramm zum ersten Mal verwenden, sollten Sie nur eine Zertifizierungsstelle sehen: `Org2 CA`. Wählen Sie sie aus.
4. Die Felder **Eintragungs-ID** und **Geheimer Eintragungsschlüssel** darunter werden automatisch mit der Eintragungs-ID und dem geheimen Schlüssel für den ersten Benutzer gefüllt, den Sie mit der Zertifizierungsstelle erstellt haben. Sie können diese Werte zwar verwenden, aber es wird nicht empfohlen, Ihre CA-Administratoridentität als Organisationsadministrator zu verwenden. Aus Sicherheitsgründen wird stattdessen empfohlen, die separate Eintragungs-ID mit dem geheimen Schlüssel einzugeben, die Sie für Ihren Organisationsadministrator erstellt haben (`org2admin` und `org2adminpw`). Geben Sie dann dieser Identität einen Anzeigenamen, z. B. `Org2 Admin`.
5. Klicken Sie auf die Schaltfläche **Generieren**, um diese Identität als Administrator Ihrer Organisation einzutragen und die Identität in die Wallet zu exportieren, wo sie verwendet wird, wenn Sie den Peer und Kanäle erstellen.
6. Klicken Sie auf **Exportieren**, um die Administratorzertifikate in Ihr Dateisystem zu exportieren. Wie bereits oben erwähnt, wird diese Identität nicht in Ihrem Cluster gespeichert oder von {{site.data.keyword.IBM_notm}} verwaltet. Sie wird nur im lokalen Speicher Ihres Browsers gespeichert. Wenn Sie zu einem anderen Browser wechseln, müssen Sie diese Identität in Ihre Konsolenwallet importieren, damit Sie den Peer verwalten können.
7. Klicken Sie auf **MSP-Definition erstellen**.

**Task: MSP-Definition für die Peerorganisation erstellen**

  |  | **Anzeigename** | **MSP-ID** | **Eintragungs-ID**  | **Geheimer Schlüssel** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Organisation erstellen** | Org2 MSP | org2msp |||
  | **Stammzertifizierungsstelle** | Org2 CA ||||
  | **Administratorzertifikat für Organisation** | |  | org2admin | org2adminpw |
  | **Identität** | Org2 Admin |||||

  *Abbildung 4. MSP-Definition für Peerorganisation erstellen*  

Nachdem Sie den MSP erstellt haben, sollten Sie den Administrator der Peerorganisation in der Konsolenwallet sehen können.

**Task: Konsolenwallet überprüfen**

  | **Feld** |  **Anzeigename** | **Beschreibung** |
  | ------------------------- |-----------|----------|
  | **Identität** | Org2 Admin | Org2 identity |

  *Abbildung 5. Konsolenwallet überprüfen*  

Weitere Informationen zum MSP finden Sie im Abschnitt [Organisationen verwalten](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Das Exportieren und Speichern des MSP ist wichtig, da dadurch Ihre Organisation zu einem Konsortium hinzugefügt werden kann, das in einer anderen Konsole gehostet wird.
{:important}

### Peer erstellen
{: #ibp-console-join-network-peer-create}

Nachdem Sie eine [Zertifizierungsstelle erstellt](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA), diese zum Registrieren von Identität verwendet und den [MSP der Peerorganisation erstellt haben](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2), können Sie nun einen Peer erstellen.

#### Welche Rolle spielen Peers?
{: #ibp-console-join-network-peer-role}

Es ist wichtig zu wissen, dass die Organisationen selbst keine Ledger pflegen. Dies übernehmen Peers. Organisationen verwenden auch Peers, um Transaktionsvorschläge zu signieren und Kanalkonfigurationsaktualisierungen zu genehmigen. Da das Vorhandensein von mindestens zwei Peers in einem Kanal diese hoch verfügbar macht, wird die Implementierung von mindestens zwei Peers, die mit einem Kanal verbunden sind, als Best Practice für Implementierungen auf Produktionsebene betrachtet. Dieses Lernprogramm enthält jedoch nur den Prozess zum Erstellen eines einzelnen Peers.

Aus Sicht einer Ressourcenzuordnung ist es möglich, dieselben Peers mit mehreren Kanälen zu verknüpfen. Das Design des Peers stellt sicher, dass die Daten von einem Kanal nicht über den Peer an einen anderen Kanal übergeben werden können. Da der Peer jedoch für jeden Kanal ein separates Ledger speichert, muss sichergestellt werden, dass der Peer genügend Verarbeitungsleistung und Speicher hat, um die Transaktion und das Laden der Daten zu verarbeiten.

#### Peer bereitstellen
{: #ibp-console-join-network-deploy-peer-role}

Verwenden Sie die Konsole, um die folgenden Schritte auszuführen:

1. Klicken Sie auf der Seite **Knoten** auf **Peer hinzufügen**.
2. Klicken Sie auf {{site.data.keyword.cloud_notm}} unter **Neuen Peer erstellen** und dann auf **Weiter**.
3. Geben Sie für den Peer als **Anzeigename** den Namen `Peer Org2` an.
4. Wählen Sie in der nächsten Anzeige die Option `Org2 CA` als Zertifizierungsstelle aus. Geben Sie dann die Eintragungs-ID und den geheimen Schlüssel für die Peeridentität ein, die Sie für den Peer erstellt haben (`peer2` und `peer2pw`). Wählen Sie anschließend in der Dropdown-Liste Ihren MSP (`Org2 MSP`) aus und klicken Sie auf **Weiter**.
5. In der nächsten Seitenanzeige werden Informationen zur TLS-Zertifizierungsstelle abgefragt. Es ist zwar möglich, separate Administratoren für die TLS-Zertifizierungsstelle zu erstellen, die mit Ihrer Zertifizierungsstelle implementiert ist; dies ist jedoch nicht zwingend erforderlich.
   - Verwenden Sie für die **TLS-Eintragungs-ID** (`admin`) und den geheimen Schlüssel (`adminpw`) dieselben Werte wie für die Eintragungs-ID und den zugehörigen geheimen Schlüssel, die Sie beim Erstellen der Zertifizierungsstelle angegeben haben.
   - Der **TLS-CSR-Hostname** ist für fortgeschrittener Benutzer zum Angeben eines angepassten Domänennamens für den Peerendpunkt. Lassen Sie den Wert für **TLS-CSR-Hostname** jetzt leer, da er in diesem Lernprogramm nicht verwendet wird.
6. In der letzten Seitenanzeige werden Sie zum **Zuordnen einer Identität** aufgefordert, die Sie als Administrator des Peers festlegen können. Wählen Sie `Org2 Admin` als Administratoridentität für den Peer aus.
7. Überprüfen Sie die Zusammenfassung und klicken Sie auf **Peer hinzufügen**.

**Task: Peer bereitstellen**

  |  | **Anzeigename** | **MSP-ID** | **Eintragungs-ID** | **Geheimer Schlüssel** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Peer erstellen** | Peer Org2 | org2msp |||
  | **Zertifizierungsstelle** | Org2 CA ||||
  | **Peeridentität** | |  | peer2 | peer2pw |
  | **Administratorzertifikat** | org2msp ||||
  | **TLS-Zertifizierungsstelle** | Org2 CA ||||
  | **ID der TLS-Zertifizierungsstelle** | || admin | adminpw |
  | **Identität zuordnen** | Org2 Admin |||||

  *Abbildung 6. Peer bereitstellen*  

## Schritt 2: Organisation zur Liste der Organisationen hinzufügen, die Transaktionen durchführen können
{: #ibp-console-join-network-add-org2}

Wie bereits erwähnt, muss eine Peerorganisation Mitglied des Konsortiums eines Anordnungsknotens sein, bevor sie einen Kanal erstellen oder mit ihm verknüpft werden kann. Dies liegt daran, dass Kanäle technisch gesehen **Nachrichtenübertragungswege** zwischen Peers über einen Anordnungsknoten sind. So wie ein Peer mit mehreren Kanälen verknüpft werden kann, ohne dass Informationen von einem Kanal zu einem anderen Kanal übergeben werden, kann ein Anordnungsknoten von mehreren Kanälen durchzogen sein, ohne dass Daten an Organisationen auf anderen Kanälen offengelegt werden.

Da nur Administratoren von Anordnungsknoten Peerorganisationen zum Konsortium hinzufügen können, müssen Sie entweder ein solcher Administrator **sein** oder MSP-Informationen an den Administrator des Anordnungsknotens **senden**. In diesem Lernprogramm können Sie entweder **die Organisation des Peers zum Anordnungsknoten in Ihrer Konsole hinzufügen**, wenn Sie den Anordnungsknoten hosten. Oder Sie müssen **Ihre Organisationsinformationen exportieren** und dem Administrator des Anordnungsknotens übergeben, der das Netz erstellt hat, in dem sich der Anordnungsknoten befindet. Der Administrator des Anordnungsknoten kann dann Ihre Organisation importieren und Sie zum Konsortium hinzufügen.

### Organisation des Peers zum Anordnungsknoten in der eigenen Konsole hinzufügen
{: #ibp-console-join-network-add-org2-local}

**Führen Sie die folgenden Schritte nur aus, wenn die Konsole bereits den Anordnungsknoten und Kanal enthält, denen Sie beitreten möchten.** Andernfalls fahren Sie mit dem [Exportieren der Organisationsinformationen](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote) fort.  

Da nur Administratoren von Anordnungsknoten Peerorganisationen zum Konsortium hinzufügen können, müssen Sie der Administrator des Anordnungsknotens sein, was bedeutet, dass die Administratoridentität der Organisation des Anordnungsknotens in Ihrer Konsolenwallet vorhanden sein muss.  

1. Navigieren Sie zur Registerkarte **Knoten**.
2. Blättern Sie abwärts zu dem Anordungsknoten, den Sie verwenden möchten, und klicken Sie auf den Knoten, um ihn zu öffnen.
3. Klicken Sie unter **Konsortiumsmitglieder** auf **Organisation hinzufügen**.
4. Wählen Sie in der Dropdown-Liste `Org2 MSP` aus, da es sich hier um den MSP handelt, der die Organisation `org2` des Peers darstellt.
5. Klicken Sie auf **Organisation hinzufügen**.

Fügen Sie nun die Peerorganisation zum Kanal hinzu.
1. Navigieren Sie zur Registerkarte **Kanäle** und klicken Sie auf `channel1`.
2. Klicken Sie auf das Symbol **Einstellungen**, um den Kanal zu aktualisieren und die Peerorganisation zum Kanal hinzuzufügen.
3. Stellen Sie sicher, dass in der Dropdown-Liste **Verfügbaren Anordnungsknoten auswählen** der Eintrag `Orderer` ausgewählt ist.
4. Stellen Sie sicher, dass in der Dropdown-Liste **MSP-ID des Kanalaktualisierungsberechtigten** der Eintrag `org1MSP` ausgewählt ist.
5. Stellen Sie sicher, dass in der Dropdown-Liste **Verfügbare Identität zuordnen** der Eintrag `Org1Admin` ausgewählt ist.
6. Blättern Sie abwärts zum Abschnitt `Organisationen zum Kanal hinzufügen` und öffnen Sie die Dropdown-Liste `Kanalmitglied auswählen`. Wählen Sie den MSP der Peerorganisation, `Org2 MSP`, für das Lernprogramm aus.
7. Klicken Sie auf **Hinzufügen** und ordnen Sie dann Berechtigungen für diese Organisation zu. Sie sollten Berechtigungen des Typs `Operator` verwenden, damit der Kanal aktualisiert werden kann.
8. Klicken Sie auf **Kanal aktualisieren**.

Wenn dieser Prozess abgeschlossen ist, kann `org2` einen Kanal erstellen oder mit einem Kanal verknüpft werden, der auf Ihrem `Anordnungsknoten` gehostet ist. Sie können mit [Schritt 3: Peer mit dem Kanal verknüpfen](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) fortfahren.

### Organisationsinformationen exportieren
{: #ibp-console-join-network-add-org2-remote}

**Führen Sie diese Schritte nur aus, wenn der Anordnungsknoten und der Kanal, denen der Peer beitritt, sich in einer anderen {{site.data.keyword.blockchainfull_notm}} Platform-Serviceinstanz** befinden. Andernfalls können Sie mit [Schritt 3: Peer mit dem Kanal verknüpfen](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) fortfahren.

Sie müssen die MSP-Definition Ihrer Organisation an den Administrator des Anordnungsknoten senden und mit den nachfolgenden Schritten zum Konsortium hinzufügen.  

Wenn Sie die folgenden Schritte ausführen, müssen Sie der Administrator der **Peerorganisation** sein. Dies bedeutet, dass Sie über die Administratoridentität der Peerorganisation in der Konsolenwallet verfügen:  
1. Navigieren Sie zur Registerkarte **Organisationen**. Die Organisationen, die für den Export zur Verfügung stehen, sind unter **Verfügbare Organisationen** aufgelistet. Klicken Sie auf die Schaltfläche **Herunterladen** innerhalb der Kachel mit den Organisationen, um die JSON-Konfigurationsdatei herunterzuladen, die den MSP der Peerorganisation darstellt.
2. Senden Sie diese Datei in einer externen Operation an den Administrator des Anordnungsknotens.

### Organisationsdefinition importieren
{: #ibp-console-join-network-import-remote-msp}
Der Administrator des Anordnungsknotens muss diese JSON-Datei importieren, um Ihre Organisation zu seiner Konsole hinzuzufügen:
1. Navigieren Sie zur Registerkarte **Organisationen**, klicken Sie auf die Schaltfläche **MSP-Definition importieren** und wählen Sie die JSON-Datei aus, die die MSP-Definition der Peerorganisation darstellt.  
2. Navigieren Sie zur Seite **Knoten** und klicken Sie auf Ihren Anordnungsknoten. Klicken Sie in der Anzeige für den Anordnungsknoten unter **Konsortiumsmitglieder** auf **Organisation hinzufügen**. Wählen Sie in der Seitenanzeige, die geöffnet wird, `Org2 MSP` aus der Liste der MSP-Definitionen auf der Registerkarte **Organisationen** aus.
3. Navigieren Sie zur Registerkarte **Kanäle**, klicken Sie auf `channel1` und anschließend auf das Symbol **Einstellungen**, um den Kanal zu aktualisieren, und fügen Sie die Peerorganisation zum Kanal hinzu.
4. Stellen Sie sicher, dass in der Dropdown-Liste **Verfügbaren Anordnungsknoten auswählen** der Eintrag `Orderer` ausgewählt ist.
5. Stellen Sie sicher, dass in der Dropdown-Liste **MSP-ID des Kanalaktualisierungsberechtigten** der Eintrag `org1MSP` ausgewählt ist.
6. Stellen Sie sicher, dass in der Dropdown-Liste **Verfügbare Identität zuordnen** der Eintrag `Org1Admin` ausgewählt ist.
7. Blättern Sie abwärts zum Abschnitt `Organisationen zum Kanal hinzufügen` und öffnen Sie die Dropdown-Liste `Kanalmitglied auswählen`. Wählen Sie den MSP der Peerorganisation, `Org2 MSP`, für das Lernprogramm aus.
8. Klicken Sie auf **Hinzufügen** und ordnen Sie dann Berechtigungen für diese Organisation zu. Sie sollten Berechtigungen des Typs `Operator` verwenden, damit der Kanal aktualisiert werden kann.
9. Klicken Sie auf **Kanal aktualisieren**.

Nachdem der Administrator des Anordnungsknotens die Peerorganisation mit dem Konsortium und dem Kanal verbunden hat, müssen Sie den Anordnungsknoten in Ihre Konsole importieren. Sie können dann Kanälen beitreten, die vom Anordnungsservice gehostet werden. Führen Sie die folgenden Schritte aus, um den Anordnungsknoten zu **importieren** :

Der Administrator der **Organisation des Anordnungsknotens** muss zuerst die folgenden Schritte ausführen:
1. Navigieren Sie auf der Registerkarte **Knoten** zum Anordnungsknoten und klicken Sie auf das Symbol **Einstellungen** rechts neben dem Knotennamen, um die Seitenanzeige zu öffnen. Klicken Sie unter **Aktionen** auf die Schaltfläche **Exportieren**, um eine JSON-Konfigurationsdatei herunterzuladen.
2. Senden Sie diese Datei in einer externen Operation an die Peerorganisation. Der Administrator der Peerorganisation kann dann die Konfigurationsdatei verwenden, um den Anordnungsknoten zur Konsole hinzuzufügen.

### Anordnungsknoten aus einem anderen Cluster importieren
{: #ibp-console-join-network-import-remote-orderer}

Sie können die folgenden Schritte ausführen, wenn Sie der Administrator der **Peerorganisation** sind:
1. Navigieren Sie zur Seite **Knoten** und klicken Sie auf **Anordnungsknoten hinzufügen**.
2. Klicken Sie auf {{site.data.keyword.cloud_notm}} unter **Vorhandenen Anordnungsknoten importieren**.
3. Klicken Sie anschließend auf die Schaltfläche **JSON hochladen** und wählen Sie die JSON aus, die den Knoten selbst darstellt.
4. Wenn Sie im nächsten Schritt zum Zuordnen einer Identität aufgefordert werden, wählen Sie die Identität der Peerorganisation aus. Im vorliegenden Lernprogramm ist dies `Org2 Admin`. Klicken Sie anschließend auf **Anordnungsknoten importieren**.


## Schritt 3: Peer mit dem Kanal verknüpfen
{: #ibp-console-join-network-join-peer-org2}

Sie haben es fast geschafft. Ihr Peer kann nun mit einem vorhandenen Kanal verknüpft werden. Sie müssen in einer externen Operation den `Kanalname` von dem Netzoperator erfragen, der den Kanal erstellt hat. Im Lernprogramm zum **Erstellen eines Netzes** haben Sie einen Kanal namens `channel1` erstellt. Navigieren Sie dazu ggf. zur Registerkarte **Kanäle** in der linken Navigationsleiste.

Führen Sie die folgenden Schritte in der Konsole aus:

1. Klicken Sie auf die Schaltfläche **Kanal beitreten**, um die Seitenanzeigen zu starten.
2. Wählen Sie den Anordnungsknoten mit dem Namen 'Orderer' aus und klicken Sie auf **Weiter**.
3. Geben Sie den Namen des Kanals ein, dem Sie beitreten möchten (`channel1`), und klicken Sie auf **Weiter**.
4. Wählen Sie die Peers aus, die dem Kanal beitreten sollen. Klicken Sie im Rahmen dieses Lernprogramms auf `Peer Org2`.
5. Klicken Sie auf **Peer zuordnen**.

Wenn Sie die Funktion [Private Data ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/private-data/private-data.html "Private Data") oder [Service Discovery ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-overview.html "Service Discovery") von Hyperledger Fabric nutzen möchten, müssen Sie Ankerpeers in Ihren Organisationen über die Registerkarte **Kanäle** konfigurieren. In diesem Thema finden Sie Informationen darüber, [wie Sie Ankerpeers für private Daten](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) über die Registerkarte **Kanäle** in der Konsole konfigurieren können.

Sie können auch einen neuen Kanal erstellen, sobald Ihre Organisation Mitglied des Konsortiums ist. Führen Sie die Schritte zum [Erstellen eines Kanals](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) im [Lernprogramm zum Erstellen eines Netzes](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) aus.

## Nächste Schritte
{: #ibp-console-join-network-next-steps}

Nachdem Sie den Peer mit einem Kanal verknüpft haben, verwenden Sie nun die folgenden Schritte, um einen Smart Contract bereitzustellen und mit der Übertragung von Transaktionen an die Blockchain zu beginnen:

- [Stellen Sie einen Smart Contract in Ihrem Netz bereit](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts). Verwenden Sie dazu die Konsole.
- Nachdem Sie Ihren Smart Contract installiert und instanziiert haben, können Sie [Transaktionen mit Ihrer Clientanwendung übergeben](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Verwenden Sie das [Wertpapier-Beispiel (commercial-paper)](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper), um einen beispielhaften Smart Contract bereitzustellen und Transaktionen mithilfe von Anwendungscode zu übergeben.

---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Network Monitor verwenden
{: #ibp-dashboard}

{{site.data.keyword.blockchainfull}} Platform enthält einen Network Monitor, der eine Übersicht über Ihre Blockchain-Umgebung, einschließlich Netzressourcen, Mitgliedern, zugeordneter Kanäle, Transaktionsleistungsdaten und bereitgestelltem Chaincode, zur Verfügung stellt. Der Network Monitor stellt auch den Eingangspunkt zur Ausführung von Swagger-APIs dar. Sie können ein Netz mit {{site.data.keyword.blockchainfull_notm}} Platform entwickeln und Beispielanwendungen ausprobieren.
{:shortdesc}

In diesem Lernprogramm erfahren Sie, wie Sie mit Network Monitor ein Enterprise Plan- oder Starter Plan-Netz betreiben. Die meisten Screenshots im Lernprogramm beziehen sich zwar auf einen Enterprise Plan, aber die Anweisungen sind ebenfalls für einen Starter Plan gültig. Wenn bestimmte Funktionen nur bei einem der Pläne zugänglich sind, ist der entsprechende Abschnitt mit dem Hinweis **für Starter Plan-Netze** bzw. **für Enterprise Plan-Netze** versehen.

## Linkes Navigationsfenster
{: #ibp-dashboard-left-navigation}

Der Network Monitor präsentiert die folgenden Anzeigen in drei Abschnitten. Sie können über den Navigator auf der linken Seite zu jeder Anzeige in Network Monitor navigieren.
- Der Abschnitt **Eigenes Netz** enthält die Anzeigen "[Übersicht](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-overview)", "[Mitglieder](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members)", "[Kanäle](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-channels)", "[Benachrichtigungen](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-notifications)", "[Zertifizierungsstelle](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-ca)" und "[APIs](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-apis)".
- Der Abschnitt **Eigener Code** enthält die Anzeigen "[Code entwickeln](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-write_code)", "[Code installieren](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-chaincode)" und "[Beispiele ausprobieren](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-samples)".
- Die Anzeige "[Hilfe anfordern](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-support)" zeigt Unterstützungsinformationen und Releaseinformationen für Helios und Hyperledger Fabric (die Codebasis von {{site.data.keyword.blockchainfull_notm}} Platform) an.

Im linken Navigationsfenster ist oben der Name Ihres Blockchain-Netzes angegeben. In Network Monitor können Sie den [Namen des Netzes ändern](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-name).

Über das Dropdown-Menü in der rechten oberen Ecke in Network Monitor können Sie die [Netzvorgaben überprüfen und konfigurieren](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences).

In diesem Lernprogramm werden die oben aufgeführten Anzeigen und Funktionen beschrieben.

## Übersicht
{: #ibp-dashboard-overview}

Die Anzeige "Übersicht" zeigt Statusinformationen zu Blockchain-Ressourcen (einschließlich Anordnungs-, CA- und Peerknoten) in Echtzeit an. Jede Ressource wird unter vier unterschiedlichen Überschriften angezeigt: **Typ**, **Name**, **Status** und **Aktionen**. Bei der Erstellung des Blockchain-Netzes werden automatisch drei Anordnungsknoten und zwei Zertifizierungsstellenknoten (CA-Knoten) erstellt. Die Zertifizierungsstellen gehören zu einem bestimmten Mitglied, während die Anordnungsknoten allgemeine Endpunkte sind, die im gesamten Netz genutzt werden.

In **Abbildung 1** ist die Anzeige "Übersicht" zu sehen:

![Anzeige 'Übersicht'](images/myresources.png "Netzübersicht")
*Abbildung 1. Netzübersicht*

### Knotenaktionen
{: #ibp-dashboard-node-actions}

Unter der Überschrift **Aktionen** befinden sich Schaltflächen zum Starten oder Stoppen Ihrer Ressourcen. Sie können auch eine Gruppe von Knoten starten oder stoppen, indem Sie mehrere Knoten auswählen und dann auf **Ausgewählte starten** oder **Ausgewählte stoppen** klicken. Die Schaltfläche **Ausgewählte starten** oder **Ausgewählte stoppen** wird oben in der Tabelle angezeigt, wenn Sie einen oder mehrere Knoten auswählen.

Die Stopp- und die Startaktion sind für einen Anordnungsknoten nicht verfügbar. Im Allgemeinen besteht keine Notwendigkeit, Peer- oder CA-Knoten in einem Netz zu stoppen oder zu starten. Die Stopp- und die Startaktion sind für den Fall vorhanden, dass Sie einen Peer erneut starten müssen, zum Beispiel um ihn in einem bereinigten Status verfügbar zu machen.

Sie können auch Komponentenprotokolle prüfen, indem Sie in der Dropdown-Liste unter der Überschrift **Aktionen** auf **Protokolle anzeigen** klicken. Die Protokolle zeigen die Aufrufe zwischen den verschiedenen Netzressourcen und sind für die Fehlersuche und -behebung von Nutzen. Weitere Informationen zur Verwendung der Netzprotokolle enthält der Abschnitt [Blockchain-Netz überwachen](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network).

Zur Veranschaulichung der Effekte des Startens und Stoppens eines Peers können Sie einen Peer probeweise stoppen und dann versuchen, eine Transaktion an diesen Peer zu senden; in den Protokollen werden daraufhin Konnektivitätsfehler angezeigt. Wenn Sie den Peer erneut starten und die Transaktion erneut versuchen, wird eine erfolgreiche Verbindung angezeigt. Sie können einen Peer auch für einen längeren Zeitraum inaktiv lassen, während über Ihre Kanäle weiterhin Transaktionen durchgeführt werden. Wenn der Peer wieder aktiviert wird, werden Sie eine Synchronisation des Ledgers bemerken, da der Peer die Blöcke empfängt, die festgeschrieben wurden, als er inaktiv war. Wenn das Ledger vollständig synchronisiert wurde, können Sie normale Aufrufe und Abfragen für das Ledger ausführen.

### Ferne Peers konfigurieren
{: #ibp-dashboard-peer-connection-information}

Wenn Sie einen Peer außerhalb von {{site.data.keyword.cloud_notm}} bereitstellen, dann müssen Sie die Informationen zum API-Endpunkt Ihres Netzes während der Konfiguration angeben. Klicken Sie auf die Schaltfläche **Konfiguration ferner Peers**, um die Informationen zum API-Endpunkt des Netzes abzurufen und den Peer zu konfigurieren. Das Popup-Fenster enthält die Informationen zum API-Endpunkt mit der Netz-ID, dem MSP der Organisation, dem CA-Namen, der CA-URL sowie dem CA-TLS-Zertifikat. Sie können entweder auf das Kopiersymbol klicken, das sich am Ende aller Felder befindet, um den Wert dieses Felds zu kopieren, oder auf die Schaltfläche **Download**, um die Werte aller Felder in einer JSON-Datei zu speichern. Weitere Angaben finden Sie unter [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) und [Informationen zu {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

### Verbindungsprofil
{: #ibp-dashboard-connection-profile}

Sie können die JSON-Datei zu Low-Level-Netzinformationen zu den einzelnen Ressourcen anzeigen, indem Sie auf die Schaltfläche **Verbindungsprofil** klicken. Das Verbindungsprofil enthält sämtliche Konfigurationsinformationen, die Sie für eine Anwendung benötigen. Da diese Datei jedoch nur die Adressen für Ihre bestimmten Komponenten und den Anordnungsknoten enthält, müssen Sie, wenn weitere Peers als Ziele hinzugefügt werden sollen, deren Endpunkte ermitteln. Der Header, der "url" enthält, zeigt den API-Endpunkt jeder Komponente an. Diese Endpunkte sind für die zielgerichtete Verwendung bestimmter Netzkomponenten von einer clientseitigen Anwendung erforderlich und ihre Definitionen befinden sich üblicherweise in einer JSON-modellierten Konfigurationsdatei, die die App begleitet. Wenn Sie eine Anwendung anpassen, für die eine Bewilligung durch Peers erforderlich ist, die nicht Teil Ihrer Organisation sind, dann müssen Sie die IP-Adressen dieser Peers von den entsprechenden Operatoren mit einer Out-of-band-Operation abrufen. Clients müssen eine Verbindung zu allen Peers herstellen können, von denen sie eine Antwort benötigen.

### Peers hinzufügen
{: #ibp-dashboard-peers}

Netzmitglieder stellen [Peers](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer) bereit, um ihre Kopien des Ledgers zu speichern und Chaincode zum Abfragen und Aktualisieren des Ledgers auszuführen. Wenn die Bewilligungsrichtlinie einen Peer als bewilligenden Peer definiert, gibt der Peer außerdem Bewilligungsergebnisse an Anwendungen zurück.

Klicken Sie auf die Schaltfläche **Peers hinzufügen** in der rechten oberen Ecke, um Ihrem Netz Peerknoten hinzuzufügen. Wählen Sie im Popup-Fenster "Peers hinzufügen" die Anzahl und Größe der Peerknoten aus, die Sie hinzufügen wollen. Sie können bei Bedarf weitere Peers für Ihre Organisationen hinzufügen. In anderen Szenarios kann eine höhere Anzahl an Peers erforderlich sein. Sie könnten z. B. mehrere Peers benötigen, um sie zu Redundanzzwecken mit demselben Kanal zu verknüpfen. Jeder Peer verarbeitet die Transaktionen des Kanals und schreibt in seine jeweilige Kopie des Ledgers. Wenn einer der Peers ausfällt, kann der andere Peer (oder auch mehrere andere Peers) die Verarbeitung von Transaktionen und Anwendungsanforderungen fortsetzen. Sie können außerdem alle Anwendungsanforderungen symmetrisch auf die Peers verteilen oder Sie können verschiedene Peers für verschiedene Funktionen vorsehen. Sie können zum Beispiel einen Peer zum Abfragen des Ledgers und einen anderen Peer zur Verarbeitung von Bewilligungen für Ledgeraktualisierungen verwenden.

Der **Starter Plan** erstellt standardmäßig einen Peer für jede der beiden Organisationen, die beim Starten des Netzes gebildet werden.

### Speicher (für Starter Plan-Netze)
{: #ibp-dashboard-storage}

In **Abbildung 2** ist die Registerkarte "Speicher" dargestellt, auf der die Speichernutzung Ihres Netzes angezeigt wird.

![Registerkarte 'Speicher' in der Anzeige 'Übersicht'](images/monitor_storage_starter.png "Speicher")
*Abbildung 2. Speicher*

Im Diagramm "Ressourcen" wird der Speicherplatz angezeigt, der von Peers und Zertifizierungsstellen verwendet wird. In diesem Pool sind alle Organisationen enthalten, die Sie erstellt oder zur Teilnahme an Ihrem Netz eingeladen haben. Die Organisationen, die Ressourcen verbrauchen, sind in der Anzeige unter dem zweiten Listenpunkt aufgeführt.

Der Anordnungsservice nutzt Speicher aus einem separaten Ressourcenpool. Es kann sein, dass der Anordnungsservice mehr Ressourcen verbraucht, wenn Mitglieder des Netzes neue Kanäle erstellen und neue Transaktionsblöcke generieren.

## Mitglieder
{: #ibp-dashboard-members}

Die Anzeige "Mitglieder" enthält zwei Registerkarten. Auf der Registerkarte "Mitglieder" werden Informationen zu Netzmitgliedern angezeigt und auf der Registerkarte "Zertifikate" werden Zertifikatsinformationen angezeigt.

### Mitglieder zu Enterprise Plan-Netzen hinzufügen
{: #ibp-dashboard-members-tab}

In **Abbildung 3** ist die erste Anzeige "Mitglieder" zu sehen, die Ihre Netzmitglieder im Netz auf der Registerkarte "Mitglieder" anzeigt:

![Registerkarte 'Mitglieder' in der Anzeige 'Mitglieder'](images/monitor_members.png "Netzmitglieder")
*Abbildung 3. Netzmitglieder*

Auf der Registerkarte "Mitglieder" können Sie andere Mitglieder einladen und zu denjenigen Mitgliedern hinzufügen, die Sie bei der Erstellung des Netzes ursprünglich eingeladen hatten. Um ein Mitglied in Ihr Netz einzuladen, geben Sie den Namen der Institution und die E-Mail-Adresse des Operators ein und klicken Sie auf **Mitglied hinzufügen**. Ein Netz kann insgesamt 15 Mitglieder haben (einschließlich des Netzinitiators). Um ein Mitglied aus Ihrem Netz zu entfernen, klicken Sie auf das Symbol "Entfernen" am Ende der Zeile des Mitglieds.

### Mitglieder zu Starter Plan-Netzen hinzufügen

**Abbildung 4** zeigt das Fenster "Mitglied hinzufügen".

![Mitglied hinzufügen](images/invite_member_starter.png "Mitglied hinzufügen")
*Abbildung 4. Mitglied hinzufügen*

Wenn Sie auf **Mitglied hinzufügen** klicken, werden zwei Optionen angezeigt:
- **Mitglied einladen**. Sie können andere Organisationen einladen, Mitglieder Ihres Netzes zu werden. Die eingeladenen Organisationen können dann teilnehmen und mit Ihnen im Netz zusammenarbeiten.
- **Mitglied erstellen**. Sie können auch ein Mitglied erstellen, indem Sie Ihre eigene E-Mail-Adresse verwenden. Sie haben dann ebenso Kontrolle über dieses Mitglied wie über die beiden Organisationen, die Sie standardmäßig mit dem Starter Plan empfangen.


### Zertifikate
{: #ibp-dashboard-certificates}

In **Abbildung 5** ist die erste Anzeige "Mitglieder" dargestellt, die Mitgliedszertifikate auf der Registerkarte "Zertifikate" anzeigt:

![Registerkarte 'Zertifikate' in der Anzeige 'Mitglieder'](images/monitor_certificates.png "Zertifikate")
*Abbildung 5. Zertifikate*

Operatoren können die Zertifikate für die Mitglieder in derselben Institution auf der Registerkarte "Zertifikate" verwalten. Klicken Sie auf **Zertifikat hinzufügen**, um die Anzeige "Zertifikat hinzufügen" zu öffnen. Geben Sie Ihrem Zertifikat einen Namen, fügen Sie die clientseitigen Zertifikate im PEM-Format in das Feld "Schlüssel" ein und klicken Sie auf **Übergeben**. Sie müssen die Peers erneut starten, damit die clientseitigen Zertifikate wirksam werden.

Weitere Informationen zur Generierung Ihres Zertifikatsschlüssels finden Sie unter [Anwendung eintragen](/docs/services/blockchain/v10_application.html#dev-app-enroll).

## Kanäle
{: #ibp-dashboard-channels}

Kanäle, die aus einer Untergruppe von Netzmitgliedern bestehen, die private Transaktionen ausführen wollen, sorgen für Datenisolation und Vertraulichkeit, indem sie es den Mitgliedern eines Kanals ermöglichen, bestimmte Regeln und ein separates Ledger einzurichten, auf das nur die Kanalmitglieder Zugriff haben. Jedes Netz muss mindestens einen Kanal aufweisen, damit Transaktionen durchgeführt werden können. Jeder Kanal verfügt über ein eindeutiges Ledger und Benutzer müssen ordnungsgemäß authentifiziert sein, um Lese-/Schreiboperationen für dieses Ledger durchführen zu können. Befinden Sie sich nicht auf diesem Kanal, können Sie die Daten nicht sehen.

In **Abbildung 6** ist die Anfangsanzeige des Dashboards mit einer Übersicht aller Kanäle in Ihrem Netz zu sehen:

![Kanäle](images/channels.png "Kanäle")
*Abbildung 6. Kanäle*

Durch das Erstellen eines Kanals wird ein kanalspezifisches Ledger generiert. Weitere Informationen finden Sie unter [Kanal erstellen](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel).

Sie können auch einen vorhandenen Kanal auswählen, um genauere Details zu Kanal, Mitgliedschaft und aktivem Chaincode anzuzeigen. Weitere Informationen finden Sie unter [Netz überwachen](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network).

Wenn Sie unter Verwendung der [Registerkarte "Zertifikate"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-certificates) in der Anzeige "Mitglieder" ein neues Zertifikat in die Plattform hochgeladen haben, können Sie diese Anzeige verwenden, um das Zertifikat zu einem Kanal hinzuzufügen. Klicken Sie in der Dropdown-Liste unter der Überschrift **Aktionen** neben dem entsprechenden Kanal auf **Zertifikat synchronisieren**. Auf diese Weise können Sie den Kanal von einem fernen Client aus betreiben, was auch die Möglichkeit einschließt, einen Chaincode auf dem Kanal zu instanziieren. Weitere Informationen enthält der Abschnitt [Signierzertifikate in {{site.data.keyword.blockchainfull_notm}} Platform hochladen](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs) im Lernprogramm [Zertifikate verwalten](/docs/services/blockchain/certificates.html#managing-certificates).

## Benachrichtigungen
{: #ibp-dashboard-notifications}

Wenn Sie einen Kanal erstellen oder in einen neuen Kanal eingeladen werden, wird eine Benachrichtigung in Network Monitor angezeigt. In der Anzeige "Benachrichtigungen" können Sie diese Anforderungen anzeigen und darauf reagieren.

In **Abbildung 7** ist die Anzeige "Benachrichtigungen" dargestellt:

![Benachrichtigungen](images/notifications.png "Benachrichtigungen")
*Abbildung 7. Benachrichtigungen*

Die Anforderungen werden auf den untergeordneten Registerkarten "Alle", "Anstehend" und "Abgeschlossen" gruppiert. Die Zahlen hinter der Registerkartenüberschrift geben die Anzahl der Anforderungen auf den einzelnen untergeordneten Registerkarten an.
   * Auf der untergeordneten Registerkarte "Alle" werden alle Ihre Anforderungen angezeigt.
   * Anforderungen, die Sie nicht akzeptiert oder abgelehnt haben oder die Sie noch nicht angezeigt haben, befinden sich auf der untergeordneten Registerkarte "Anstehend". Klicken Sie auf die Schaltfläche **Anforderung überprüfen**, um die Anforderung anzuzeigen, die die Kanalrichtlinie und Mitglieder sowie den Abstimmungsstatus enthält. Ein Kanaloperator kann die Anforderung entweder **Akzeptieren** oder **Ablehnen** oder sie zu einem anderen Zeitpunkt bearbeiten, indem er auf **Später** klickt. Wenn die Anforderung von einer ausreichenden Zahl von Kanaloperatoren akzeptiert wird, können Sie auf **Anforderung übergeben** klicken, um die Kanalaktualisierung zu aktivieren.
   * Übergebene Anforderungen werden auf der untergeordneten Registerkarte "Abgeschlossen" angezeigt. Sie können auf **Anforderung überprüfen** klicken, um die zugehörigen Details anzuzeigen.

Wenn Sie eine lange Liste von Anforderungen haben, können Sie im Suchfeld oben nach einer Anforderung suchen.

Anstehende Anforderungen können gelöscht werden, indem Sie die davor befindlichen Felder auswählen und auf **Anforderung löschen** klicken. Beachten Sie, dass eine abgeschlossene Anforderung nicht gelöscht werden kann.

## Zertifizierungsstelle (CA)
{: #ibp-dashboard-ca}

Die Tabelle in der Anzeige "Zertifizierungsstelle (CA)" enthält alle Identitäten, die bei Ihrer Organisation registriert wurden (einschließlich des Administrators, der Peers und der Clientanwendungen). Sie können diese Anzeige auch zum Registrieren neuer Identitäten verwenden.

In **Abbildung 8** ist die Anzeige "Zertifizierungsstelle" dargestellt:

![Zertifizierungsstelle](images/CA_screen.png "Zertifizierungsstelle")
*Abbildung 8. Zertifizierungsstelle*

Klicken Sie auf die Schaltfläche **Zertifikat generieren** neben Ihrer Administratoridentität, um ein neues öffentliches Zertifikat sowie einen privaten Schlüssel von Ihrer CA abzurufen. Das Feld **Zertifikat** enthält das öffentliche Zertifikat, das auch als signCert-Zertifikat oder Eintragungszertifikat bezeichnet wird. Es befindet sich direkt über dem Feld **Privater Schlüssel**. Sie können auf das Kopiersymbol klicken, das sich am Ende aller Felder befindet, um den Wert zu kopieren. Diese Anzeige bietet eine alternative Möglichkeit zum Generieren eines Paars aus öffentlichem und privatem Schlüssel für eine Clientanwendung, die mit dem Fabric-SDK arbeitet. Weitere Informationen bietet das Lernprogramm [Anwendungen entwickeln](/docs/services/blockchain/v10_application.html#dev-app). **Beachten Sie hierbei**, dass diese Zertifikate unter {{site.data.keyword.blockchainfull_notm}} Platform nicht gespeichert werden. Sie müssen sie an einem sicheren Ort aufbewahren und speichern.

Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, um eine neue Identität bei Ihrer Organisation zu registrieren. Füllen Sie im Popup-Fenster **Benutzer hinzufügen** die folgenden Felder aus und klicken Sie anschließend auf **Abschicken**.
  - **Eintragungs-ID:** Dies ist der Name Ihrer neuen Identität. `` **Speichern Sie diesen Wert.** Sie benötigen ihn, wenn Sie einen fernen Peer konfigurieren oder eine neue Anwendung eintragen.
  - **Geheimer Eintragungsschlüssel:** Dies ist das Kennwort für Ihre Identität.`` **Speichern Sie diesen Wert.** Sie benötigen ihn, wenn Sie einen fernen Peer konfigurieren oder eine neue Anwendung eintragen.
  - **Typ:** Wählen Sie den Typ der Identität aus, die registriert werden soll (Peer oder Clientanwendung).
  - **Zugehörigkeit:** Dies ist die Zugehörigkeit innerhalb Ihrer Organisation, z. B. `org1`, zu der die Identität gehören wird.
  - **Maximale Anzahl der Eintragungen:** In diesem Feld können Sie die Anzahl der Wiederholungen für die Eintragung oder die Generierung von Zertifikaten mit dieser Identität einschränken. Erfolgt in dem Feld keine Angabe, dann wird standardmäßig der Wert für eine unbeschränkte Anzahl von Eintragungen verwendet.

Weitere Informationen zu Ihrer Zertifizierungsstelle (CA) erhalten Sie, wenn Sie das Lernprogramm [Zertifikate in {{site.data.keyword.blockchainfull_notm}} Platform verwalten](/docs/services/blockchain/certificates.html#managing-certificates) aufrufen.

## APIs
{: #ibp-dashboard-apis}

{{site.data.keyword.blockchainfull_notm}} Platform stellt eine Reihe von REST-APIs in Swagger bereit, mit denen Sie die Knoten, Kanäle, Peers und Mitglieder Ihres Netzes verwalten können. Ihre Anwendungen können mithilfe dieser APIs wichtige Netzressourcen ohne den Network Monitor steuern.

In **Abbildung 9** ist die Anzeige "APIs" zu sehen:

![APIs](images/API_screen.png "APIs")
*Abbildung 9. APIs*

Klicken Sie auf den Link für die **Swagger-Benutzerschnittstelle (UI)**, um die Swagger-Benutzerschnittstelle zu öffnen. Beachten Sie, dass Sie die Swagger-Benutzerschnittstelle mit Ihren Netzberechtigungsnachweisen (die auf dieser Seite "APIs" zu finden sind) berechtigen müssen, bevor Sie die APIs ausführen können. Weitere Informationen finden Sie in [Swagger-APIs verwenden, um mit dem Netz zu interagieren](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger).

## Code entwickeln
{: #ibp-dashboard-write-code}

{{site.data.keyword.IBM_notm}} bietet keinen Support für Netze, die Hyperledger Composer in Produktionsumgebungen nutzen, inklusive Composer-Befehlszeilenschnittstelle, JavaScript-APIs, REST-Server und Web Playground.{:note}

Der Starter Plan und der Enterprise Plan bieten eine Entwicklungsumgebung mit Tools und Technologien nach Branchenstandard an. Wenn Sie ein Netz entwickelt haben, können Sie es in Ihrem Netz bereitstellen.

In **Abbildung 10** ist die Anzeige "Code entwickeln" dargestellt:

![Code entwickeln](images/write_code.png "Code entwickeln")
*Abbildung 10. Code entwickeln*

Weitere Informationen zum Entwickeln und Bereitstellen Ihrer Unternehmensnetze finden Sie im Abschnitt [Unternehmensnetze auf Basis des Starter und Enterprise Plans bereitstellen](/docs/services/blockchain/develop_starter_enterprise.html#deploying-a-business-network).

## Code installieren
{: #ibp-dashboard-chaincode}

Bei Chaincode, der auch als "Smart Contract" bezeichnet wird, handelt es sich um Stücke von Software, die eine Gruppe von Funktionen zum Abfragen und Aktualisieren des Ledgers enthalten. Sie werden auf Peers installiert und auf einem Kanal instanziiert.

In **Abbildung 11** ist die Anzeige "Code installieren" zu sehen:

![Code installieren](images/chaincode_install_overview.png "Code installieren")
*Abbildung 11. Code installieren*

Ein Chaincode wird zunächst im Dateisystem eines Peers installiert und anschließend auf einem Kanal instanziiert. Weitere Informationen finden Sie in [Chaincode installieren, instanziieren und aktualisieren](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).

## Beispiele ausprobieren
{: #ibp-dashboard-samples}

eispielanwendungen helfen Ihnen, sich mit Blockchain-Netzen und der Anwendungsentwicklung vertraut zu machen. Rufen Sie die Links für **Auf GitHub anzeigen** auf und informieren Sie sich, wie die Beispiele genutzt und in {{site.data.keyword.blockchainfull_notm}} Platform bereitgestellt werden können. Weitere Informationen zur Entwicklung und Bereitstellung eigener Beispiele finden Sie unter [Beispielanwendungen bereitstellen](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications).

In **Abbildung 12** ist die Anzeige "Beispiele ausprobieren" dargestellt:

![Beispiele ausprobieren](images/sample_overview_ep.png "Beispiel ausprobieren")
*Abbildung 12. Beispiele*

## Hilfe anfordern
{: #ibp-dashboard-support}

Die Anzeige "Hilfe anfordern" enthält eine Registerkarte "Support", auf der eine Liste mit Ressourcen für Entwickler bereitgestellt wird, sowie eine Registerkarte "Releaseinformationen", auf der neue und geänderte Funktionen in {{site.data.keyword.blockchainfull_notm}} Platform beschrieben sind.

**Abbildung 13** zeigt die Informationen, die anfangs auf der Registerkarte "Support" zu sehen sind:

![Support](images/support.png "Support")
*Abbildung 13. Blockchain-Support*

### Blockchain-Ressourcen und Unterstützungsforen
{: #ibp-dashboard-support-forums}

Mit den Ressourcen auf der Registerkarte "Support" können Sie Fehler beheben sowie Hilfe von {{site.data.keyword.IBM_notm}} und der Fabric-Community anfordern. Weitere Informationen zu den Links auf der Registerkarte "Support" finden Sie unter [Ressourcen und Unterstützungsforen](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources) im Abschnitt [Support anfordern](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

[IBM dWAnswers ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://developer.ibm.com/answers/smartspace/blockchain/index.html) ist ein von IBM Experten betreutes Community-Forum für Benutzer von {{site.data.keyword.blockchainfull_notm}} Platform und Hyperledger Fabric. Sie können nach Antworten auf bereits gestellte Fragen suchen oder eine neue Frage stellen. Wenn Sie Ihr Problem nicht beheben können oder keine Antwort auf Ihre Frage finden, reichen Sie einen Supportfall im {{site.data.keyword.cloud_notm}} Service Portal ein. Weitere Informationen finden Sie unter [Supportfälle einreichen](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases).


### Releaseinformationen zu Fabric
{: #ibp-dashboard-release-notes}

Auf der Registerkarte "Releaseinformationen" werden die neuesten Funktionen Ihres Netzes angezeigt. Nach Auswahl der Schaltfläche "Network Monitor-Benutzerschnittstelle" werden neue Funktionen und Fehlerkorrekturen bei der Funktionalität für den Benutzer von {{site.data.keyword.blockchainfull_notm}} Platform aufgelistet. Die Schaltfläche "Hyperledger Fabric" leitet Sie zu den Releaseinformationen zur Version von Hyperledger Fabric und der Fabric-Zertifizierungsstelle weiter, die in Ihrem Netz verwendet wird.

In **Abbildung 14** sind die Releaseinformationen zur Network Monitor-Benutzerschnittstelle dargestellt.

![Releaseinformationen für Helios](images/releasenotes_helios.png "Releaseinformationen zur Network Monitor-Benutzerschnittstelle")
*Abbildung 14. Releaseinformationen zur Network Monitor-Benutzerschnittstelle*

**Abbildung 15** zeigt die Releaseinformationen zur Version von Hyperledger Fabric und der Fabric-Zertifizierungsstelle, die in Ihrem Netz verwendet wird.

![Releaseinformationen für Fabric](images/releasenotes_Fabric.png "Releaseinformationen zu Fabric")
*Abbildung 15. Releaseinformationen für Fabric*

## Netzvorgaben
{: #ibp-dashboard-network-preferences}

Klicken Sie auf die rechte obere Ecke, um das Dropdown-Menü zu öffnen, und dann auf **Netzvorgaben**. Das Fenster mit den Netzvorgaben wird geöffnet. Das Fenster mit den Netzvorgaben enthält die Basisinformationen Ihres Netzes, wie z. B. den Netznamen, die Fabric-Version, die Netzadresse in {{site.data.keyword.cloud_notm}} und den Statusdatenbanktyp.

Nach dem 15. Mai 2018 erstellte **Enterprise Plan-Netze** werden unter Hyperledger Fabric Version 1.1.1 ausgeführt. Wenn Sie nach dem Upgrade Netze erstellen, können Sie auch Web-Inaktivitätszeitlimits und die gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) für Ihr Netz im Fenster mit den Netzvorgaben verwalten. Diese Einstellungen können nur vom Netzinitiator geändert werden.

### Web-Inaktivitätszeitlimit
{: #ibp-dashboard-web-inactivity-timeout}

**Hinweis**: Nur der **Netzinitiator** kann die Einstellung für das Web-Inaktivitätszeitlimit ändern. Hierbei handelt es sich um eine Einstellung auf Netzebene, die sich auf alle Netzmitglieder auswirkt.

Standardmäßig ist für das Web-Inaktivitätszeitlimit der Wert **Aus** festgelegt. Wenn Sie für das Web-Inaktivitätszeitlimit den Wert **Ein** festlegen, werden Mitglieder des Netzes nach 10 Minuten Inaktivität automatisch abgemeldet. Wenn der Web-Inaktivitätszeitgeber das Zeitlimit von 10 Minuten erreicht, beendet die Web-Inaktivitätszeitlimitfunktion die inaktiven Websitzungen, um die Sicherheit der Konten der Netzmitglieder zu gewährleisten. Wenn Sie auf einen Link klicken oder den Network Monitor aktualisieren, wird der Web-Inaktivitätszeitgeber zurückgesetzt. Vor dem Erreichen des Limits von 10 Minuten kann die Websitzung auch durch das Schließen des Browserfensters oder der Browserregisterkarte beendet werden.

In **Abbildung 16** ist das Fenster "Netzvorgaben" dargestellt:

![Netzvorgaben](images/network_preferences.gif "Netzvorgaben")
*Abbildung 16. Netzvorgaben*

### Gegenseitige TLS-Authentifizierung (für Enterprise Plan-Netze)
{: #ibp-dashboard-mutual-tls}

Bei **Enterprise Plan-Netzen** können Sie die gegenseitige TLS-Authentifizierung (Mutual TLS, MTLS) aktivieren, um die Kommunikation zwischen Ihrer Anwendung und den Blockchain-Komponenten zu schützen.

**Hinweis**: Nur ein **Netzinitiator** kann MTLS aktivieren oder inaktivieren. Hierbei handelt es sich um eine Einstellung auf Netzebene, die sich auf alle Netzmitglieder auswirkt.

Standardmäßig ist für die TLS-Schaltfläche die Einstellung **Aus** festgelegt. Wenn Sie MTLS aktivieren, müssen Sie die Anwendungen so aktualisieren, dass sie diese Funktion unterstützen. Andernfalls können die Anwendungen nicht mit dem Netz kommunizieren.

Bei einem Enterprise Plan-Netz in Fabric 1.1 verfügt jede Organisation über eine eigene MTLS-Zertifizierungsstelle. Die Informationen, die für die Herstellung einer Verbindung zur MTLS-Zertifizierungsstelle erforderlich sind, stehen im [Verbindungsprofil](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-connection-profile) zur Verfügung, auf das Sie über die Anzeige **Übersicht** in Network Monitor zugreifen können, indem Sie auf die Schaltfläche **Verbindungsprofil** klicken. Das Verbindungsprofil enthält die Informationen, die erforderlich sind, um eine Verbindung zur Zertifizierungsstelle herzustellen und die Zertifikate abzurufen, die Sie für die Verbindung zu Ihrem Netz benötigen.

Suchen Sie im Verbindungsprofil nach dem Abschnitt `certificateAuthorities`. Hier finden Sie die folgenden Attribute, die für die Eintragung und das Abrufen der Zertifikate für die Kommunikation mit dem Netz über MTLS erforderlich sind.

- `url`: URL für das Herstellen einer Verbindung zur Zertifizierungsstelle, die MTLS-Zertifikate ausstellen kann.
- `enrollId`: Eintragungs-ID für das Abrufen eines Zertifikats.
- `enrollSecret`: Geheimer Eintragungsschlüssel für das Abrufen eines Zertifikats.
- `x-tlsCAName`: Name der Zertifizierungsstelle für das Abrufen des Zertifikats, das der Anwendung die Kommunikation über MTLS (Mutual TLS) ermöglicht.

Weitere Informationen zum Aktualisieren der Anwendungen für die Unterstützung von MTLS finden Sie unter [How to configure mutual TLS ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html).

<!--

### CouchDB state database
{: #couchdb}

**Note**: Only the **network initiator** can switch the state database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.

Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your state database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.

To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.

If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up indexes, see [Best practices when using CouchDB](/docs/services/blockchain/best_practices.html#best-practices-app-couchdb-indices) in the Developing applications tutorial. For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc).

-->

In **Abbildung 17** ist das Fenster "Netzvorgaben" dargestellt:

![Netzvorgaben](images/network_preferences_ep_tmp.png "Netzvorgaben")
*Abbildung 17. Netzvorgaben*

## Netznamen aktualisieren
{: #ibp-dashboard-network-name}

Wenn Sie eine Starter Plan- oder Enterprise Plan-Instanz erstellen, weist {{site.data.keyword.blockchainfull_notm}} Platform dem Netz einen Namen zu. Sie können diesen Netznamen jedoch zu jedem beliebigen Zeitpunkt in Network Monitor aktualisieren.

Klicken Sie oben im linken Navigator in Network Monitor auf den Netznamen. Damit wird das Feld bearbeitbar. Geben Sie den neuen Netznamen ein, den Sie verwenden möchten, und drücken Sie die **Eingabetaste**. Die Aktualisierung des Netznamens nimmt einige Sekunden in Anspruch.

**Abbildung 18** zeigt die Schritte zur Aktualisierung des Starter Plan-Netznamens vom zugewiesenen Namen in "Starter Plan Network".

![Netznamen aktualisieren](images/update_network_name_ep.gif "Netznamen aktualisieren")
*Abbildung 18. Netznamen aktualisieren*


## Zwischen Netzen wechseln (für Starter Plan-Netze)
{: #ibp-dashboard-switch-network}

Wenn Sie mehrere Netze mit dem Starter Plan erstellen, können Sie in Network Monitor zwischen den Netzen wechseln.

Klicken Sie oben im linken Navigator in Network Monitor auf das Pfeilsymbol neben dem Netznamen. Wählen Sie den Namen des Netzes, zu dem Sie wechseln möchten, in der Dropdown-Liste aus und klicken Sie darauf. Der Web-Browser wird aktualisiert und der Network Monitor wird für das Netz, zu dem Sie wechseln, geöffnet.

**Abbildung 19** zeigt die Schritte zum Wechseln zu einem anderen Starter Plan-Netz.

![Netz wechseln](images/switch_network.gif "Netz wechseln")
*Abbildung 19. Netz wechseln*


## Netz zurücksetzen (für Starter Plan-Netze)
{: #ibp-dashboard-reset-network}

Starter Plan-Netze bieten die Möglichkeit, die Netzkonfiguration zu bearbeiten, ohne ein Netz zu löschen und erneut zu erstellen. Ihr Netz wird auf die Erstkonfiguration des Netzes zurückgesetzt, die zwei Organisationen, einen Peer pro Organisation und einen Standardkanal umfasst. Dies ist zum Beispiel nützlich, wenn Sie mehrere Testrunden im Blockchain-Netz ausführen und immer wieder mit einem sauberen Netz beginnen müssen.

**Achtung:** Wenn Sie das Netz zurücksetzen, werden die API-Endpunkte Ihrer Peers, Ihres Anordnungsknotens und der Zertifizierungsstelle (CA) geändert. Sie müssen die Informationen zu API-Endpunkten in Ihren Anwendungen anpassen.

Klicken Sie in der rechten oberen Ecke, um das Dropdown-Menü zu öffnen. Klicken Sie im Menü auf die Schaltfläche **Netz zurücksetzen**. Wenn Sie bereit sind, Ihr Netz zurückzusetzen, klicken Sie auf **OK**, um fortzufahren. Die Anzeige von Network Monitor wird aktualisiert, um die neuen Einstellungen wiederzugeben.

In **Abbildung 20** ist die Funktion "Netz zurücksetzen" zu sehen:

![Netz zurücksetzen](images/reset_network.png "Netz zurücksetzen")
*Abbildung 20. Netz zurücksetzen*

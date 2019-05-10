---

copyright:
  years: 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Organisationen verwalten
{: #ibp-console-organizations}

Sie können die {{site.data.keyword.blockchainfull}} Platform-Konsole verwenden, um eine formale Organisationsdefinition zu erstellen, die als "Membership Service Provider (MSP)" bezeichnet wird. Die MSP-Definition Ihres Unternehmens ermöglicht es anderen Mitgliedern des Blockchain-Konsortiums, die Identität Ihrer Knoten und Anwendungen zu überprüfen. Ihre MSP-Definition enthält auch die Administratorzertifikate Ihrer Organisation.

Sie können mit der Konsole auch verwalten, welche Organisationen Mitglieder Ihres Netzes sind. Der Administrator des Anordnungsservices kann die Registerkarte "Organisationen" verwenden, um Mitglieder zum Blockchain-[Konsortium](/docs/services/blockchain/glossary.html#glossary-consortium) hinzuzufügen. Die Mitglieder des Konsortiums können dann die Konsole verwenden, um Mitglieder zu neuen oder vorhandenen Kanälen hinzuzufügen.

**Zielgruppe:** Dieser Abschnitt richtet sich an Netzoperatoren, die für die Erstellung, Überwachung und Verwaltung des Blockchain-Netzes verantwortlich sind.

## Informationen zu MSPs
{: #console-organizations-about-msp}

{{site.data.keyword.blockchainfull_notm}} Platform basiert auf Hyperledger Fabric und dient zur Erstellung genehmigter Blockchain-Netze. Dem Netz müssen die Teilnehmer bekannt sein, bevor sie Transaktionen übergeben und mit den Assets im Ledger interagieren können. Fabric erkennt die Identität durch eine Gruppe von eingeladenen Organisationen, die als Konsortium bezeichnet werden. Die Organisationen des Konsortiums sind in der Lage, gültige Berechtigungsnachweise an ihre Mitglieder auszugeben und sie zu Teilnehmern im Netz zu machen. Die Teilnehmer können dann Blockchain-Knoten betreiben und Transaktionen von Clientanwendungen übergeben.

Jede Organisation im Konsortium muss eine eigene Zertifizierungsstelle betreiben, die als Stammzertifizierungsstelle (Root-CA) bezeichnet wird. Diese Zertifizierungsstelle (oder ihre temporären Zertifizierungsstellen) erstellt alle Identitäten, die zu Ihrer Organisation gehören, und gibt jede Identität als öffentlichen und privaten Schlüssel aus. Diese Schlüssel werden von der Zertifizierungsstelle signiert und von den Mitgliedern Ihrer Organisation verwendet, um ihre Aktionen zu signieren und zu überprüfen. Wenn Sie dem Konsortium beitreten, können andere Organisationen Ihre Zertifizierungsstellen-Signatur erkennen und sicherstellen, dass Ihre Peers und Anwendungen gültige Teilnehmer sind. Weitere Informationen zur Mitgliedschaft in Hyperledger Fabric finden Sie im Konzeptthema [Mitgliedschaft ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Membership") in der Fabric-Dokumentation.

Bevor Ihre Organisation Mitglied eines Konsortiums werden kann, muss sie eine Organisationsdefinition erstellen, die als **Membership Services Provider (MSP)** bezeichnet wird. Der MSP enthält die folgenden Informationen:
- Ein Zertifikat, das von Ihrer **Stammzertifizierungsstelle** signiert wurde. Dieses Zertifikat wird verwendet, um die Identität Ihrer Knoten, Kanäle und Anwendungen zu überprüfen.
- Ein Zertifikat, das von Ihrer **TLS-Zertifizierungsstelle** signiert wurde. Das TLS-Stammzertifikat ermöglicht Ihren Peers die Teilnahme an organisationsübergreifenden Gossip. Dies ist erforderlich, um die Vorteile der Features von [**Privaten ** Datensammlungen](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) und [Serviceerkennungen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Serviceerkennungen") von Hyperledger Fabric zu nutzen.
- Die **MSP-ID**. Die MSP-ID ist der formale Name Ihrer Organisation innerhalb des Konsortiums. Sie müssen sich die MSP-ID für Ihre Knoten und Anwendungen merken.
- **Administratorzertifikate** von Ihren Identitäten für den **Peer-Administrator** und **Organisationsadministrator**. Diese Zertifikate werden an den Anordnungsservice übergeben und werden verwendet, um zu prüfen, welche Identitäten in Ihrem Unternehmen Kanäle erstellen oder bearbeiten dürfen. Wenn Sie Ihre Konsole zum Erstellen eines Anordnungsknotens oder Peers verwenden, werden die Administratorzertifikate im MSP innerhalb des neuen Knotens bereitgestellt. Diese Zertifikate können dann verwendet werden, um die Peers oder Anordnungsknoten von Ihrer Konsole oder von einer Clientanwendung aus zu betreiben.

## MSPs in der Konsole verwalten

Navigieren Sie zur Registerkarte **Organisationen**. Sie können diese Registerkarte verwenden, um [eine MSP-Definition zu erstellen](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp), indem Sie eine Zertifizierungsstelle verwenden, die in der Konsole vorhanden ist. Sie können diese Registerkarte auch zum [Importieren eines MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) verwenden, der von einer anderen Organisation erstellt wurde.

Sie können alle MSPs anzeigen, die Sie unter **Verfügbare Organisationen** erstellt oder importiert haben. Sie können die MSP-Definitionen auf der Registerkarte "Organisationen" für wichtige Funktionen in Ihrer Konsole verwenden:
- Wenn Sie Peerknoten oder Anordnungsknoten erstellen, wird der MSP Ihrer Organisation verwendet, um Administratorzertifikate für den neuen Knoten bereitzustellen.
- Wenn Sie der Administrator eines Anordnungsknotens sind, können Sie die MSPs verwenden, um [neue Organisationen zum Konsortium hinzuzufügen](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).
- Wenn Sie Mitglied des Konsortiums sind, können Sie die MSPs von anderen Konsortiumsmitgliedern in Ihre Konsole importieren und die Mitglieder dann zu neuen oder vorhandenen Kanälen hinzufügen.

## Einen MSP für Ihre Organisation erstellen
{: #console-organizations-create-msp}

Verwenden Sie die Registerkarte **Organisationen**, um eine MSP-Definition für Ihre Organisation zu generieren. Wenn Sie auf **MSP erstellen** klicken, wird ein Seitenfenster geöffnet, in dem Sie alle erforderlichen Informationen eingeben können: die Stammzertifizierungsstelle, die MSP-ID und Ihre Organisationsadministratoren.

- Verwenden Sie den Abschnitt **MSP-Details** in der Anzeige, um die MSP-ID Ihrer Organisation auszuwählen. Diese ID ist der formale Name, den andere Mitglieder des Netzes verwenden, um auf Ihre Organisation zu verweisen. **Speichern Sie Ihre MSP-ID.**

- Verwenden Sie den Abschnitt **Stammzertifizierungsstellendetails**, um die Stammzertifizierungsstelle (Root-CA) Ihrer Organisation auszuwählen. Dies ist die Zertifizierungsstelle (CA), die Sie verwendet haben (oder verwenden), um alle Knoten- und Anwendungsidentitäten zu erstellen, die zu Ihrer Organisation gehören. Wenn Sie temporäre Zertifizierungsstelle verwenden, ist dies die Zertifizierungsstelle, die Sie zum Erstellen dieser CAs verwendet haben. Wählen Sie Ihre Stammzertifizierungsstelle aus der Liste der CAs aus, die mit der Konsole verwaltet werden. Wenn Sie eine Zertifizierungsstelle über die Konsole erstellt haben, wird bei Auswahl einer Stammzertifizierungsstelle auch ein TLS-Stammzertifikat erstellt.

- Sie können auch den Abschnitt **Stammzertifizierungsstellendetails** verwenden, um eine der Administratorzertifikate Ihrer Organisation zu generieren. Bevor Sie die MSP-Definition Ihres Unternehmens erstellen, müssen Sie Ihre Organisations- und Knotenadministratoren bei Ihrer Stammzertifizierungsstelle registrieren. Sie müssen dann die folgenden Schritte ausführen, um diese Identitäten für den Betrieb des Netzes zu verwenden:

  1. Erstellen Sie ein öffentliches und ein privates Schlüsselpaar für jede Administratoridentität.
  2. Geben Sie den öffentlichen Schlüssel (Zertifikat) jeder Administratoridentität in der MSP-Definition an.
  3. Fügen Sie Ihrer Konsolenwallet die Identität hinzu. Die von der Konsole erstellten Knoten oder Kanäle verwenden die Zertifikate aus dem MSP, um zu prüfen, wer ein gültiger Administrator ist. Daher muss das gleiche öffentliche private Schlüsselpaar, das Sie zum Hinzufügen eines Administratorzertifikats zum MSP verwendet haben, in der Konsolenwallet gespeichert werden.

  Sie können die Anzeige **MSP-Definition erstellen** verwenden, um diese Aktionen für eine Ihrer Administratoridentitäten auszuführen. Nachdem Sie Ihre Stammzertifizierungsstelle ausgewählt haben, führen Sie die folgenden Schritte im Abschnitt **Administratorzertifikat Ihrer Organisation generieren** aus:
  1. Geben Sie die Eintragungs-ID und den geheimen Eintragungsschlüssel einer Administratoridentität ein, die bei Ihrer Stammzertifizierungsstelle registriert ist. Nachdem Sie die Eintragungs-ID und den geheimen Eintragungsschlüssel, wählen Sie einen Namen aus, um die Identität in Ihrer Konsolenwallet anzuzeigen.
  2. Klicken Sie auf **Generieren**. Dadurch wird eine neue Gruppe von öffentlichen und privaten Schlüsseln generiert und die Schlüssel werden automatisch zu Ihrer Konsolenwallet hinzugefügt. Sie können dann Ihre Administratoridentität in Ihrer Wallet finden, indem Sie den Namen verwenden, den Sie in dieser Anzeige ausgewählt haben. Diese Schlüssel werden nur im lokalen Speicher Ihres Browsers gespeichert. Wenn Sie also den Browser ändern, sind sie nicht mehr in der Konsolenwallet enthalten. Sie müssen die Identität aus Ihrem ursprünglichen Browser exportieren und sie in die Konsolenwallet Ihres neuen Browsers importieren.
  3. Klicken Sie anschließend auf **Exportieren**, um das Schlüsselpaar in Ihr Dateisystem herunterzuladen und zu sichern.

- Der Abschnitt **Administratoren (Optional)** des Seitenfensters enthält die öffentlichen Schlüssel Ihrer Administratoren. Das Zertifikat, das Sie mit dem Abschnitt **Administratorzertifikat Ihrer Organisation generieren** generiert haben, befindet sich in der ersten Zeile des Felds **Administratorzertifikat**. Wenn Sie mehrere Administratoridentitäten für den Betrieb Ihres Netzes verwenden möchten, können Sie die Zertifikate zusätzlicher Knoten oder Organisationsadministratoren in die Felder **Administratorzertifikat** einfügen.

Da Ihre Administratorzertifikate mithilfe der MSP-Definition an Ihre Knoten und Kanäle übergeben werden, müssen Sie sicherstellen, dass jedes Administratorzertifikat Ihrer Knoten und Organisationen im MSP gespeichert wird. Wenn Sie mit der Konsole einen Anordnungsknoten, einen Peer oder einen Kanal erstellen, müssen Sie eine der Identitäten **zuordnen**, die Sie in Ihre Konsolenwallet mit den Administratorzertifikaten, die der MSP-Definition bereitgestellt wurden, exportiert haben. Wenn Sie den Abschnitt oder das Fenster **Identität zuordnen** auswählen, wählen Sie eine Identität aus, die Sie beim Erstellen der MSP-Definition generiert und in der Wallet gespeichert haben.

Nachdem Sie die Stammzertifizierungsstelle und die MSP-ID ausgewählt sowie die Administratorzertifikate erstellt haben, klicken Sie auf **MSP-Definition erstellen**, um die MSP-Definition zu erstellen. Sie wird jetzt als Organisation auf der Registerkarte "Organisationen" aufgeführt. Sie verwenden die MSP-Definition, wenn Sie Ihre Knoten bereitstellen und einem Blockchain-Konsortium beitreten.

## MSP-JSON-Datei manuell erstellen
{: #console-organizations-build-msp}

**Diese Option ist nur für fortgeschrittene Benutzer vorgesehen, die mit der Verwendung von Zertifikaten im Blockchain-Identitätsmanagement vertraut sind.**

Wenn Sie es vorziehen, Zertifikate für Ihren Peer oder Anordnungsknoten von einer **unabhängigen Zertifizierungsstelle** zu verwenden, die nicht von {{site.data.keyword.IBM_notm}} gehostet wird, müssen Sie eine JSON-Datei für eine MSP-Definition erstellen, die die MSP-Definition für den Peer oder Anordnungsknoten darstellt. Erstellen Sie eine JSON-Datei im folgenden Format:

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name**: Geben Sie einen beliebigen Namen an, der zur Identifizierung dieser MSP-Definition in der Konsole verwendet werden soll.
- **organization_id**: Geben Sie eine ID an, mit der diese MSP intern in der Konsole dargestellt werden soll.
- **root_certs**: (optional) Fügen Sie ein Array ein, das ein oder mehrere Stammzertifikate aus der unabhängigen Zertifizierungsstelle im `Base64`-Format enthält. Sie müssen entweder ein CA-Stammzertifikat oder ein CA-Zwischenzertifikat angeben oder können auch beides angeben.
- **intermediate_certs**: (optional) Fügen Sie ein Array ein, das ein oder mehrere Zertifikate von der unabhängigen temporären Zertifizierungsstelle im `Base64`-Format enthält. Sie müssen entweder ein CA-Stammzertifikat oder ein CA-Zwischenzertifikat angeben oder können auch beides angeben.
- **admins**: Fügen Sie das Signierzertifikat des Organisationsadministrators im `Base64`-Format ein.
- **tls_root_certs**: (optional) Fügen Sie ein Array, das ein oder mehrere Stammzertifikate von der TLS-Zertifizierungsstelle enthält, im `Base64`-Format ein. Sie müssen entweder ein Stammzertifikat einer unabhängigen TLS-Zertifizierungsstelle oder ein Zertifikat einer unabhängigen temporären Zertifizierungsstelle angeben oder können auch beides angeben.
- **tls_intermediate_certs**: (optional) Fügen Sie ein Array ein, das ein oder mehrere Zertifikate von der temporären TLS-Zertifizierungsstelle enthält, im `Base64`-Format ein. Sie müssen entweder ein Stammzertifikat einer unabhängigen TLS-Zertifizierungsstelle oder ein Zertifikat einer unabhängigen temporären Zertifizierungsstelle angeben oder können auch beides angeben.  

Zudem stehen in Ihrer MSP-Definition folgende zusätzliche Felder zur Verfügung, die jedoch nicht erforderlich sind:
- **organizational_unit_identifiers**: Eine Liste der Organisationseinheiten (Organizational Units, OU), die gültige Mitglieder dieses MSP in ihr X.509-Zertifikat einbinden sollten. Dies ist ein optionaler Konfigurationsparameter, der verwendet wird, wenn mehrere Organisationen dieselbe Vertrauenswurzel (Root of Trust) und dieselben temporären Zertifizierungsstellen nutzen und ein OU-Feld für ihre Mitglieder reserviert haben. Eine Organisation wird häufig in mehrere Organisationseinheiten (OUs) unterteilt, die jeweils eine bestimmte Menge von Zuständigkeiten haben. Beispielsweise kann die Organisation ORG1 sowohl ORG1-MANUFACTURING- als auch ORG1-DISTRIBUTION-OUs besitzen, die diese separaten Geschäftsbereiche widerspiegeln. Wenn eine Zertifizierungsstelle X.509-Zertifikate ausgibt, gibt das Feld "OU" im Zertifikat das Geschäftsfeld an, zu dem die Identität gehört. Weitere Informationen hierzu finden Sie in der Fabric-Dokumentation unter [Identitätsklassifizierung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link") ](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "Identitätsklassifizierung").  
- **fabric_node_OUs**: Fabric-spezifische OUs, die die Identitätsklassifizierung ermöglichen. `NodeOUs` enthalten Informationen zur Unterscheidung von Clients, Peers und Anordnungsknoten auf Grundlage ihrer OU. Wenn die Prüfung erzwungen wird und die Einstellung "Aktiviert" auf "true" gesetzt ist, betrachtet der MSP eine Identität als gültig, wenn es sich um die Identität `client`, `peer` oder `Anordnungsknoten` handelt. Eine Identität sollte nur eine dieser speziellen OUs besitzen. Lesen Sie dieses Thema in der [Dokumentation der Fabric-Service-Erkennung ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query), um ein Beispiel für die Angabe der `fabric_node_OU` in einem MSP zu erhalten.
- **revocation_list**: Eine Liste von nicht mehr gültigen Zertifikaten. Bei X.509-basierten Identitäten sind diese IDs Paare von Zeichenfolgen, auch als "Subject Key Identifier (SKI)" und "Authority Access Identifier" (AKI) bezeichnet, die immer dann überprüft werden, wenn das X.509-Zertifikat verwendet wird, um sicherzustellen, dass das Zertifikat nicht widerrufen wurde. Lesen Sie dieses Thema in der Fabric-Dokumentation, um weitere Informationen über das [Zertifikatwiderrufs-Listen ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "Widerrufen eines Zertifikats oder einer Identität") zu erhalten.

Daraus ergibt sich eine JSON-Datei ähnlich der folgenden:

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

Beachten Sie, dass alle Zertifikate im Base64-Format codiert sein müssen.
{:important}

Sie können den Inhalt Ihrer Zertifikatsdatei, `<cert.pem>`, vom `PEM`-Format in eine Base64-Zeichenfolge konvertieren, indem Sie in Ihrem lokalen System den folgenden Befehl ausführen:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}

Speichern Sie diese Definition als `JSON`-Datei.  

Sie haben eine MSP-Definition erstellt, die die Organisation für Ihren Peer oder Anordnungsknoten definiert, der Zertifikate von einer unabhängigen Zertifizierungsstelle verwendet. Sie können jetzt zu den Anweisungen zurückkehren, die die [Verwendung von Zertifikaten von einer unabhängigen Zertifizierungsstelle mit dem Peer oder Anordnungsknoten](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca) beschreiben.

## MSP importieren
{: #console-organizations-import-msp}

Nur der Anordnungsknoten-Administrator kann dem Konsortium neue Organisationen hinzufügen. Wenn Sie der Anordnungsknoten-Administrator sind, müssen Sie die MSP-Definitionen aller Organisationen erfassen, die zum Konsortium eingeladen wurden, und die MSPs in die Konsole importieren. Anschließend können Sie dem Anordnungsservice die MSPs hinzufügen, indem Sie den Anordnungsknoten verwenden.

Nachdem ein Administrator eine MSP-Definition erstellt hat, kann die Registerkarte "Organisationen" verwendet werden, um den MSP im JSON-Format in das lokale Dateisystem herunterzuladen. Dann kann Ihnen die MSP-JSON-Datei in einer Out-of-Band-Operation gesendet werden. Navigieren Sie zur Registerkarte **Organisationen** und verwenden Sie die Option **MSP-Definition importieren**, um die MSP-Datei in Ihre Konsole zu importieren. Sobald eine MSP-Definition im Abschnitt **Verfügbare Organisationen** angezeigt wird, können Sie zu Ihrem Anordnungsknoten navigieren, um [dem Konsortium die Organisation hinzuzufügen](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Einem Konsortium eine Organisation hinzufügen
{: #console-organizations-add-consortium}

Das Konsortium aus Organisationen wird vom Anordnungsservice gehostet.

Wenn Sie der Administrator des Anordnungsservices sind, können Sie die Konsole verwenden, um dem Konsortium eine Organisation hinzuzufügen. Navigieren Sie zur Registerkarte **Knoten** und klicken Sie auf den Anordnungsknoten. Klicken Sie in der Anzeige des Anordnungsknotens unter **Konsortiumsmitglieder** auf **Organisation hinzufügen**. Dadurch wird ein Seitenfenster geöffnet, in dem Sie eine Auswahl aus der Liste der verfügbaren MSP-Definitionen treffen können, die Sie [in Ihre Registerkarte "Organisationen" importiert haben](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). Sie können auch die Option **JSON hochladen** verwenden, um die MSP-Definitionsdatei direkt zu importieren, die von einer anderen Organisation erstellt wurde.

## Kanal erstellen und bearbeiten
{: #console-organizations-create-channel}

Nachdem eine Organisation zum Konsortium hinzugefügt wurde, kann die Organisation den Anordnungsservice verwenden, um einen neuen Kanal zu erstellen, oder sie kann zu einem vorhandenen Kanal hinzugefügt werden. Die Informationen, die Ihnen die Teilnahme an einem Kanal ermöglichen (z. B. die Verbindung Ihrer Peers mit dem Kanal, die Instanziierung von Smart Contracts und die Übergabe von Transaktionen) werden in den MSP-Definitionen bereitgestellt.

Nachdem Ihre Organisation zu einem Konsortium hinzugefügt wurde, können Sie einen Kanal erstellen. Führen Sie dazu die folgenden Schritte aus:

1. Importieren Sie den Anordnungsknoten, der das Konsortium hostet, in Ihre Konsole. Sie müssen kein Administrator des Anordnungsknotens sein, aber Sie müssen den Namen das Anordnungsknotens und die Endpunktinformationen in Ihrer Konsole bereitstellen.
2. Importieren Sie die MSPs von Organisationen, die zu dem neuen Kanal hinzugefügt werden sollen, in die Registerkarte **Organisationen** Ihrer Konsole. **Hinweis:** Organisationen müssen dem Konsortium hinzugefügt werden, bevor sie einem Kanal hinzugefügt werden können.
3. Navigieren Sie zur Registerkarte **Kanäle** und klicken Sie auf **Kanal erstellen**. Dadurch wird die Seitenanzeige geöffnet, in der Sie den Kanalnamen, die Mitgliedschaft und Kanalrichtlinien angeben können. Sie können alle Organisationen hinzufügen, die dem Konsortium zum neuen Kanal hinzugefügt wurden.

Weitere Informationen zu diesen Schritten finden Sie im Abschnitt [Kanal erstellen](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) im **Lernprogramm zum Erstellen eines Netzes**.

### MSP in einer Kanaldefinition aktualisieren
{: #console-organizations-update-channel}

Im Laufe der Zeit müssen Sie möglicherweise die Zertifikate in einer MSP-Definition aktualisieren, die bereits einem Kanal zugeordnet ist. Wenn diese Situation eintritt, führen Sie die folgenden Schritte aus, um die MSP-Definition einer Organisation im Kanal zu aktualisieren:  

1. Navigieren Sie zur Registerkarte **Kanäle** in Ihrer Konsole.
2. Klicken Sie auf den Kanal mit dem Organisations-MSP, den Sie aktualisieren möchten, und öffnen Sie ihn.
3. Klicken Sie auf die Registerkarte **Kanaldetails**.
4. Klicken Sie auf die Kachel des zugeordneten Kanalmitglieds, das Sie aktualisieren möchten.
5. Wenn Sie die aktualisierte MSP-Definition noch nicht in die Konsole importiert haben, können Sie die Datei hier hochladen. **Anmerkung:** Durch diese Aktion wird die zugeordnete MSP-Definition auf der Registerkarte 'Organisationen' nicht aktualisiert. Wenn Sie die MSP-Definition bereits auf der Registerkarte 'Organisationen' der Konsole aktualisiert haben, können Sie sie in der Dropdown-Liste auswählen.

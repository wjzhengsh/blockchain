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
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Fehlerbehebung
{: #ibp-v2-troubleshooting}

Es können allgemeine Probleme auftreten, wenn Sie die Konsole verwenden, um Knoten, Kanäle oder Smart Contracts zu verwalten. In vielen Fällen können Sie diese Probleme durch Ausführen weniger einfacher Schritte beheben. 

## Warum schlagen meine Operationen fehl, nachdem ich meinen Peer- oder Anordnungsknoten erstellt habe?
{: #ibp-console-build-network-troubleshoot-entry1}

Es ist möglich, dass bei der Verwaltung eines vorhandenen Knotens ein Fehler auftritt. In diesem Fall ist es häufig hilfreich, die Knotenprotokolle zu Rate zu ziehen.   

Wenn Sie z. B. versuchen, eine Aktionen für den Betrieb des Knotens auszuführen, kann die Aktion fehlschlagen.{: tsSymptoms}

Nachdem Sie einen neuen Peer- oder Anordnungsknoten erstellt haben, kann es in Abhängigkeit von Ihrer Clusterspeicherkonfiguration einige Minuten dauern, bis der Knoten betriebsbereit ist. {: tsCauses}

Überprüfen Sie Ihr Kubernetes-Dashboard und stellen Sie sicher, dass der Peer oder Knoten den Status `Aktiv` aufweist. Versuchen Sie anschließend erneut, die Aktion auszuführen. Treten weiterhin Probleme auf, obwohl der Knoten betriebsbereit ist, [überprüfen Sie Ihre Knotenprotokolle ](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) auf Fehler.  
{: tsResolve}

## Warum kann mein Peer nicht gestartet werden?
{: #ibp-console-build-network-troubleshoot-entry2}

Dieser Fehler kann unter verschiedenen Bedingungen auftreten.

Das Peerprotokoll enthält die Angabe `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`.
{: tsSymptoms}

- Dieser Fehler kann unter den folgenden Bedingungen auftreten:
  - Beim Erstellen der MSP-Definition für die Organisation des Peer- oder Anordnungsknotens haben Sie eine Kombination aus Eintragungs-ID und geheimem Schlüssel angegeben, die einer ID des Typs `peer` und nicht `client` entspricht. Die ID muss den Typ `client` aufweisen.
  - Beim Erstellen der MSP-Definition für die Organisation des Peer- oder Anordnungsknotens haben Sie eine Kombination aus Eintragungs-ID und geheimem Schlüssel angegeben, die nicht mit der Eintragungs-ID und dem geheimen Schlüssel der entsprechenden Organisationsadministrator-ID übereinstimmt.
  - Beim Erstellen des Peer- oder Anordnungsknotens haben Sie die Eintragungs-ID und den geheimen Schlüssel einer ID angegeben, die nicht den Typ 'peer' aufweist.

- Öffnen Sie den CA-Knoten für Ihren Peer- oder Anordnungsknoten und zeigen Sie die registrierten IDs an, die in der Tabelle **Registrierte Benutzer** aufgelistet werden.
- Löschen Sie den Peer- oder Anordnungsknoten und erstellen Sie ihn erneut. Achten Sie dabei sorgfältig darauf, die Eintragungs-ID und den geheimen Schlüssel korrekt anzugeben. 
- Beachten Sie Folgendes: Vor dem Erstellen des Peer- oder Anordnungsknotens müssen Sie eine Organisationsadministrator-ID des Typs 'client' erstellen. Stellen Sie sicher, dass beim Erstellen der MSP-Definition für die Organisation dieselbe ID als Eintragungs-ID angegeben wird. Weitere Informationen enthalten die Anweisungen in den Abschnitten [Peer-IDs registrieren](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) und [Anordnungsknoten-IDs registrieren](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Warum ist die Installation, die Instanziierung oder das Upgrade für meinen Smart Contract fehlgeschlagen?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

Es ist möglich, dass bei der Installation, Instanziierung oder beim Upgrade eines Smart Contract ein Fehler auftritt. Beispielsweise kann beim Installieren eines Smart Contract auf einem Peer der Fehler `An error occurred when installing smart contract on peer.` ausgegeben werden.
{: tsSymptoms}

Dieser Fehler kann auftreten, wenn die betreffende Smart Contract-Version auf dem Peer bereits vorhanden ist, oder wenn die verfügbaren Ressourcen auf dem Peer ausgeschöpft sind.
{: tsCauses}

- Öffnen Sie das Kubernetes-Dashboard und stellen Sie sicher, dass der Peer den Status `Aktiv` aufweist.  
- Öffnen Sie den Peerknoten, stellen Sie sicher, dass die Smart Contract-Version auf dem Peer noch nicht vorhanden ist, und wiederholen Sie den Vorgang mit der geeigneten Version.
- Treten weiterhin Probleme auf, obwohl der Knoten betriebsbereit ist, [überprüfen Sie Ihre Knotenprotokolle ](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) auf Fehler.  
{: tsResolve}

## Wie kann ich meine Smart Contract-Containerprotokolle anzeigen?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

Es kann vorkommen, dass Sie die Containerprotokolle für Smart Contract oder Chaincode aufrufen müssen, um ein Problem mit Smart Contract zu beheben.
{: tsSymptoms}

Mit den folgenden Schritten können Sie [Containerprotokolle anzeigen](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs).
{: tsResolve}

## Mein Kanal, meine Smart Contracts und meine IDs werden in der Konsole nicht mehr angezeigt. Wie kann ich diese Informationen wieder anzeigen?
{: #ibp-v2-troubleshooting-browser-storage}

Ihre Konsolenwallet-IDs umfassen jeweils ein Schlüsselpaar aus privatem und öffentlichem Schlüssel zum Verwalten Ihrer Blockchain-Komponenten, das jedoch nur im lokalen Speicher Ihres Browsers gespeichert ist. Sie sind für den Schutz und die Verwaltung dieser IDs verantwortlich. Es wird empfohlen, sie nach dem Erstellen in Ihr Dateisystem zu exportieren. Bei jedem Erstellen eines neuen Knotens ordnen Sie dem Knoten eine ID aus Ihrer Konsolenwallet zu. Diese Administrator-ID ermöglicht Ihnen, den Knoten zu verwalten. Wenn Sie den Browser wechseln oder einen Browser auf einer anderen Maschine verwenden, sind diese IDs nicht mehr in Ihrer Wallet enthalten. Dies bedeutet, dass Sie die Komponenten nicht verwalten können.
{: tsSymptoms}

Eine Neuerungen in {{site.data.keyword.blockchainfull_notm}} Platform 2.0 besteht darin, dass Sie jetzt für den Schutz und die Verwaltung Ihrer Zertifikate selbst verantwortlich sind. Aus diesem Grund werden die Zertifikate zum Verwalten der Komponente nur im lokalen Speicher Ihres Browsers als persistent definiert.
{: tsCauses}

- Jedes Mal, wenn Sie eine neue MSP-Definition für eine Organisation erstellen, generieren Sie Schlüssel für eine ID, die zum Verwalten der Organisation berechtigt ist. Daher müssen Sie während dieses Prozesses auf die Schaltflächen **Generieren** und anschließend auf **Exportieren** klicken, um die generierte ID in Ihrer Konsolenwallet zu speichern und anschließend als JSON-Datei in Ihrem Dateisystem zu sichern.
- Um dieses Problem in Ihrem Browser zu beheben, müssen Sie die betreffenden IDs importieren und dem entsprechenden Knoten zuordnen. Gehen Sie dazu  wie folgt vor:
  - Klicken Sie in dem Browser, in dem das Problem auftritt, auf die Registerkarte **Wallet** und anschließend auf **Identität hinzufügen**, um die JSON-Datei in Ihre Wallet zu importieren.
  - Klicken Sie auf **JSON hochladen** und navigieren Sie mithilfe der Schaltfläche **Dateien hinzufügen** zu der JSON-Datei, die Sie exportiert haben.
  - Klicken Sie auf **Übergeben**.
  - Öffnen Sie nun den Peer- oder Anordnungsknoten, dem die betreffende Identität ursprünglich zugeordnet wurde, und klicken Sie auf das Symbol **Einstellungen**.
  - Klicken Sie auf die Schaltfläche **Identität zuordnen**.
  - Wählen Sie in der Dropdown-Liste die Identität aus, die Sie gerade in Ihre Konsolenwallet importiert haben.
  - Klicken Sie auf **Zuordnen**.
- Wiederholen Sie diesen Vorgang für jede Identität, die in der Wallet des ursprünglichen Browsers enthalten war.
{: tsResolve}

## Warum wird der Fehler `An error occurred when updating channel` ausgegeben, wenn ich versuche, eine Organisation zu meinem Kanal hinzuzufügen?
{: #ibp-v2-troubleshooting-update-channel}

Wenn Sie versuchen, eine andere Organisation zu einem Kanal hinzuzufügen, schlägt die Aktualisierung mit der Nachricht `An error occurred when updating channel` fehl.
{: tsSymptoms}

Dieser Fehler tritt auf, wenn die ausgewählte **MSP-ID des Kanalaktualisierungsberechtigten** in der Anzeige **Kanal aktualisieren** kein Administrator des Kanals ist.
{: tsCauses}

Blättern Sie in der Anzeige **Kanal aktualisieren** abwärts zur **MSP-ID des Kanalaktualisierungsberechtigten** und wählen Sie die MSP-ID aus, die bei der Erstellung des Kanals angegeben wurde, oder geben Sie die MSP-ID des Kanaladministrators an.
{: tsResolve}

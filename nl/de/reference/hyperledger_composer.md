---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}


***[Ist diese Seite hilfreich? Teilen Sie uns Ihre Meinung mit.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop baut auf dem quelloffenen Hyperledger Composer-Toolset auf, einem der Projekte im Hyperledger-Projekt der Linux Foundation. Hyperledger Composer bietet Architekten und Entwicklern die Möglichkeit, {{site.data.keyword.blockchain}}-Lösungen schnell mithilfe von REST-APIs zu erstellen, die die Geschäftslogik für Webanwendungen oder mobile Anwendungen zugänglich machen, sowie Blockchain mit vorhandenen Unternehmenssystemen der Vergangenheit zu integrieren.
{:shortdesc}

Hyperledger Composer besteht aus einer Laufzeit, einer Befehlszeilenschnittstelle, einem REST-Server, einem LoopBack-Konnektor, einer Playground-Benutzerschnittstelle, einem Yeoman-Codegenerator und den Plug-ins für die Editoren VSCode und Atom. Weitere Informationen zu Hyperledger Composer finden Sie in der [Hyperledger Composer-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/introduction/introduction.html).


## Unternehmensnetze

Mit Hyperledger Composer können Sie schnell Ihr aktuelles Unternehmensnetz modellieren, das Ihre vorhandenen Assets und die zugehörigen Transaktionen enthält. Als Assets werden hier materielle oder immaterielle Güter, Services oder Liegenschaften bezeichnet. In Ihrem Unternehmensnetzmodell definieren Sie die Transaktionen, die mit Assets interagieren. Unternehmensnetze können darüber hinaus die Teilnehmer einschließen, die mit ihnen interagieren und die über mehrere Unternehmensnetze hinweg jeweils einer eindeutigen Identität zugeordnet werden können.

Weitere Informationen zur Erstellung von Unternehmensnetzen finden Sie unter [Unternehmensnetze entwickeln](../develop.html) oder in der [Hyperledger Composer-Dokumentation ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/introduction/introduction.html).

## Abfragen

Abfragen werden zum Abrufen von Daten zum globalen Blockchain-Status (World-State) verwendet. Abfragen werden in einem Unternehmensnetz definiert und können Variablenparameter zur einfachen Anpassung enthalten. Durch Abfragen können Daten ohne großen Aufwand aus Ihrem {{site.data.keyword.blockchain}}-Netz extrahiert werden. Abfragen werden über die [Hyperledger Composer-API ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/api/api-doc-index) gesendet.

Weitere Informationen zur Verwendung von Abfragen in Ihrem Unternehmensnetz finden Sie unter [Querying and filtering business network data ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/tutorials/queries).

## Identitäten und Unternehmennetzkarten

In einem Unternehmensnetz werden Teilnehmer einer Identität zugeordnet. Unternehmensnetzkarten sind eine Kombination aus Identität, Verbindungsprofil und Metadaten und werden zum Herstellen einer Verbindung zu einem Unternehmensnetz sowie zum Nachweis der Identität für Transaktionen verwendet. Unternehmensnetzkarten vereinfachen den Prozess der Verbindungsherstellung zu einem Unternehmensnetz und erweitern das Konzept der Identität außerhalb des Unternehmensnetzes zu einem 'Wallet' von Identitäten, die jeweils einem bestimmten Unternehmensnetz und einem bestimmten Verbindungsprofil zugeordnet sind.

Weitere Informationen zu Identitäten und Unternehmensnetzkarten finden Sie unter [Participants and identities ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/managing/participantsandidentities).

## Hyperledger Composer-REST-Server

Der Hyperledger Composer-REST-Server generiert automatisch eine Swagger-REST-API für ein Unternehmensnetz. Der REST-Server basiert auf LoopBack und konvertiert das Unternehmensnetzmodell in eine Open API-Definition. Während der Ausführung implementiert der REST-Server Unterstützung für die Operationen 'Create', 'Read', 'Update' und 'Delete' für Assets und Teilnehmer und ermöglicht es, dass Transaktionen zur Verarbeitung übergeben oder durch Abfragen abgerufen werden.

Weitere Informationen zum REST-Server von Hyperledger Composer finden Sie unter [Generating a REST API ![Symbol für externen Link](../images/external_link.svg "Symbol für externen Link")](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).

---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Kanal erstellen oder aktualisieren

Kanäle sind ein äußerst leistungsfähiger Mechanismus für die Partitionierung und Isolierung von Daten zum Datenschutz. Nur Mitglieder desselben Kanals können auf die Daten dieses Kanals zugreifen.
{:shortdesc}

Um die Kanalsicherheit sicherzustellen, wird eine Kanalaktualisierungsrichtlinie konfiguriert, um die Anzahl der Kanaloperatoren zu definieren, die gemeinsam eine Anforderung zur Kanalerstellung oder -aktualisierung bewilligen müssen, bevor ein Kanal erstellt oder aktualisiert wird.

## Kanal erstellen
Klicken Sie auf die Schaltfläche **Neuer Kanal** in der Anzeige "Kanäle" Ihres Network Monitor und führen Sie die folgenden Schritte aus, um eine Anforderung zur Kanalerstellung zu übergeben:
1. Wählen Sie einen Namen, der das Geschäftsziel des Kanals beschreibt, fügen Sie bei Bedarf eine Beschreibung hinzu und klicken Sie auf **Weiter**. Der Kanalname muss in einem Blockchain-Netz eindeutig sein. Er muss mit einem Buchstaben beginnen und darf nur Kleinbuchstaben, Ziffern oder Bindestriche enthalten.
  ![Kanalerstellung 1](../images/create_channel.png "Kanalerstellung - Anzeige 1")

2. Laden Sie eine beliebige Kombination Ihrer Netzmitglieder ein, indem Sie die Netzmitglieder auswählen und auf die Schaltfläche **Mitglied hinzufügen** klicken. Passen Sie Berechtigungen an, indem Sie Rollen für die einzelnen eingeladenen Mitglieder zuordnen, und klicken Sie auf **Weiter**.
  ![Kanalerstellung 2](../images/create_channel_2.png "Kanalerstellung - Anzeige 2")

    * Ein Kanaloperator kann das Ledger des Kanals abfragen oder aktualisieren. Der Kanaloperator hat die Berechtigung zum **Akzeptieren** oder **Ablehnen** einer Anforderung zur Kanalerstellung und zum Übergeben einer Anforderung zur Kanalaktualisierung. Für jeden Kanal muss es mindestens einen **Operator** geben.
    * Ein Mitglied mit Schreibberechtigung kann das Ledger des Kanals aktualisieren.
    * Ein Mitglied mit Leseberechtigung kann das Ledger des Kanals nur abfragen.

3. Konfigurieren Sie die Kanalaktualisierungsrichtlinie durch Auswahl der Anzahl von Kanaloperatoren, die eine Anforderung zur Kanalaktualisierung genehmigen müssen, und klicken Sie auf **Anforderung übergeben**.
  ![Kanalerstellung 3](../images/create_channel_3.png "Kanalerstellung Anzeige 3")

Die eingeladenen Mitglieder erhalten eine Einladungs-E-Mail. Sie können die Anforderung auch auf der Unterregisterkarte "Alle" oder "Anstehend" in der Anzeige **Benachrichtigungen** des Network Monitor finden.
* Mitglieder, die als Kanaloperatoren eingeladen werden, können auf die Schaltfläche **Anforderung überprüfen** klicken, um die Kanalkonfiguration zu überprüfen, und anschließend **Akzeptieren** oder **Ablehnen** für die Anforderung wählen. Die Spalte "Eigener Status" enthält den Abstimmungsstatus des Operators für die Anforderung:
    * _Abstimmung anstehend_: Der Operator hat die Anforderung noch nicht bearbeitet.
    * _Abstimmung akzeptiert_: Der Operator hat die Anforderung akzeptiert.
    * _Abstimmung abgelehnt_: Der Operator hat die Anforderung abgelehnt.
    * _Abstimmung wird abgeschlossen_: Die Anforderung erhält ausreichend Stimmen des Typs **Akzeptieren** und ein Akzeptieren bzw. Ablehnen des Operators ist nicht mehr erforderlich.
* Mitgliedern, die als Schreib- oder Leseberechtigte für einen Kanal eingeladen werden, wird unter der Spalte "Eigener Status" *Nicht erforderlich* angezeigt. Bevor für die Anforderung ausreichend Stimmen des Typs **Akzeptieren** von den Kanaloperatoren vorliegen, können Schreib- oder Leseberechtigte auf die Schaltfläche **Anforderung überprüfen** klicken, um die Kanalkonfiguration zu überprüfen.

Wenn eine ausreichende Anzahl von Kanaloperatoren der Anforderung zustimmen, kann jedes beliebige Kanalmitglied auf die Schaltfläche **Anforderung übergeben** klicken und der neue Kanal wird erstellt. Alle Kanalmitglieder finden den Kanal in der Anzeige "Kanäle" in ihrem Network Monitor.

### Verwaltete Kanäle erstellen

Auf Devisenmärkten mit strikten gesetzlichen Auflagen, um ein Beispiel zu nennen, kann es erforderlich sein, einem vertrauenswürdigen Dritten die Verantwortung für die Verwaltungsrollen auf den Kanälen zu geben, die normalerweise von verschiedenen Operatoren oder Mitgliedern ausgeübt werden.

In diesem Fall würde sich der vertrauenswürdige Dritter zum einzigen “Operator” für einen Kanal machen und die anderen Mitglieder als “Schreibberechtigte” zuordnen. Dadurch erhielte dieser Dritte die alleinige Berechtigung, den Kanal zu bearbeiten, während die beiden Banken weiterhin die Möglichkeit haben, Transaktionen aufzurufen. Ein verwalteter "schreibgeschützter" Kanal könnte auch dadurch erstellt werden, dass die anderen Mitglieder als "Leseberechtigte" festgelegt werden.

## Kanal aktualisieren
Wenn Sie die Konfiguration eines Kanals ändern wollen (wenn Sie z. B. Kanalmitglieder hinzufügen oder entfernen oder die Richtlinie zur Kanalaktualisierung ändern wollen), können Sie eine Anforderung zur Kanalaktualisierung übergeben. Suchen Sie in der Anzeige "Kanäle" des Network Monitor nach dem Kanal, den Sie ändern möchten, und wählen Sie **Kanal bearbeiten** in der Dropdown-Liste unter der Überschrift **Aktion** aus. Navigieren Sie durch die Anzeigen, um Änderungen an den gewünschten Entitäten vorzunehmen, und klicken Sie auf **Anforderung übergeben**, um eine Kanalaktualisierungsanforderung zu initiieren.

Alle Kanalmitglieder erhalten E-Mail-Benachrichtigungen über die Anforderung zur Kanalaktualisierung:
* Die neu eingeladenen Mitglieder erhalten E-Mail-Benachrichtigungen, um sie einzuladen, dem Kanal beizutreten. Sie finden die Anforderung mit dem Status "Abstimmung anstehend" auch in der Anzeige **Benachrichtigungen** des Network Monitor.
    * Mitglieder, die als Kanaloperatoren eingeladen werden, können auf die Schaltfläche **Anforderung überprüfen** klicken, um die Kanalkonfiguration zu überprüfen, und anschließend **Akzeptieren** oder **Ablehnen** für die Anforderung zur Kanalaktualisierung wählen.  Die Spalte "Eigener Status" enthält den Abstimmungsstatus des Operators für die Anforderung:
        * _Abstimmung anstehend_: Der Operator hat die Anforderung noch nicht bearbeitet.
        * _Abstimmung akzeptiert_: Der Operator hat die Anforderung akzeptiert.
        * _Abstimmung abgelehnt_: Der Operator hat die Anforderung abgelehnt.
        * _Abstimmung wird abgeschlossen_: Die Anforderung erhält ausreichend Stimmen des Typs **Akzeptieren** und ein Akzeptieren bzw. Ablehnen des Operators ist nicht mehr erforderlich.
    * Mitgliedern, die als Schreib- oder Leseberechtigte für einen Kanal eingeladen werden, wird unter der Spalte "Eigener Status" *Nicht erforderlich* angezeigt. Bevor für die Anforderung ausreichend Stimmen des Typs **Akzeptieren** von den Kanaloperatoren vorliegen, können Schreib- oder Leseberechtigte auf die Schaltfläche **Anforderung überprüfen** klicken, um die Kanalkonfiguration zu überprüfen.
* Die entfernten Mitglieder erhalten E-Mail-Benachrichtigungen zu der Kanaländerung.
* Vorhandene Kanaloperatoren erhalten E-Mail-Benachrichtigungen zur Kanalaktualisierung. Sie finden die Anforderung mit dem Status _Abstimmung anstehend_ in der Anzeige **Benachrichtigungen** des Network Monitor, wo **Akzeptieren** oder **Ablehnen** auswählen können.
* Vorhandene Schreib- oder Leseberechtigte für einen Kanal erhalten ebenfalls E-Mail-Benachrichtigungen zur Kanalaktualisierung. Sie finden die Anforderung mit dem Status _Nicht erforderlich_ in der Anzeige **Benachrichtigungen** des Network Monitor.

Wenn eine ausreichende Anzahl von Kanaloperatoren der Anforderung zustimmen, kann jedes beliebige Kanalmitglied auf die Schaltfläche **Anforderung übergeben** klicken und der Kanal wird aktualisiert. Alle Kanalmitglieder finden den aktualisierten Kanal in der Anzeige "Kanäle" im Network Monitor.

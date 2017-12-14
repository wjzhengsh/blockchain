---

copyright:
  years: 2017
lastupdated: "2017-12-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Verwaltete Kanäle erstellen 

**ACHTUNG:** Bevor Sie ein {{site.data.keyword.blockchainfull}}-Angebot verwenden, lesen Sie die technischen Angaben und Unterstützungsinformationen im Abschnitt [Haftungsausschluss](needtoknow.html).  
{:shortdesc}

Bei einigen Anwendungsfällen - z. B. auf Devisenmärkten mit strikten gesetzlichen Auflagen - kann es erforderlich sein, einem vertrauenswürdigen Dritten die Verantwortung für die Verwaltungsrollen auf den Kanälen zu geben, die normalerweise von verschiedenen Operatoren oder Mitgliedern gehandhabt werden. 

Der Prozess der Einrichtung eines solchen Netzes gleicht im Wesentlichen der Erstellung aller anderen Netze. Der Hauptunterschied besteht darin, dass den Mitgliedern Berechtigungen zugewiesen werden, die bestimmen, wie sie Transaktionen in Kanälen ausführen können.  

Die Schritte zum Erstellen eines Netzes und zum Einladen von Mitgliedern finden Sie hier: [Netz steuern](get_start.html#creating-a-network). 

**Hinweis**: In einem realen Beispiel hat der Operator eines solchen Netzes während der Phase "Netz erstellen" mit dem Richtlinieneditor die Möglichkeit, angepasste Chaincodes zu installieren. In diesem Beispiel wird aber von der Standardnetzkonfiguration ausgegangen. 

## Kanal erstellen

Wenn Sie über ein Netz verfügen und die Mitglieder, die Sie eingeladen haben, den Onboarding-Prozess durchlaufen haben, navigieren Sie in der Symbolleiste zu **Kanäle**. 

Klicken Sie auf **Neuer Kanal**. Dadurch werden Sie zu einer Anzeige geführt, in der Sie für Ihren Kanal einen Namen und eine Beschreibung angeben (die Beschreibung ist optional). 

Nach dem Klicken auf **Weiter** gelangen Sie zu der Anzeige, auf der Sie Mitglieder für den Kanal einladen und ihre Berechtigungen verwalten können. In diesem Beispiel wird davon ausgegangen, dass Sie bei einem Devisentausch als vertrauenswürdiger Dritter zwischen zwei Banken fungieren. Sie - als vertrauenswürdiger Dritter - ordnen sich als einziger die Rolle "Operator" für den Kanal zu und geben den Banken die Schreibberechtigung. Dadurch erhalten Sie die alleinige Berechtigung zur Bearbeitung des Kanals (z. B. für das Instanziieren des dortigen Chaincodes -- mehr dazu später), während die beiden Banken immer noch die Möglichkeit haben, Transaktionen aufzurufen. Die Anzeige sollte etwa wie folgt aussehen: 

  ![Mitgliedsrollen auswählen](images/selectmemberroles.png "Mitgliedsrollen auswählen") 
*Hierbei sind Sie "JoeCo", der vertrauenswürdige Dritte, und "IBM" und "Chris" sind die beiden Banken (dies ist nur ein Beispiel).* 

Wenn Sie die gewünschten Berechtigungen zugewiesen haben, klicken Sie auf **Weiter**. 

Dadurch gelangen Sie zu einer Anzeige für Kanalrichtlinienaktualisierungen. Da es nur einen Operator für diesen Kanal gibt (das sind Sie), wählen Sie für die Anzahl der Mitglieder, die zum Erstellen erforderlich sind, die Zahl "1" aus. Klicken Sie anschließend auf **Anforderung übergeben**. 

Hinweis: Auch wenn Sie der einzige Operator dieses Kanals sind, handelt es sich hier nur um eine Anforderung. Sie müssen trotzdem zu der entsprechenden Anzeige navigieren, um die Anforderung zu genehmigen und zu übergeben und den Kanal zu erstellen. 

E-Mails werden an die Mitglieder gesendet, die Sie einladen, und fordern sie auf, dem Kanal beizutreten. Diese verfügen jedoch nur über Schreibberechtigungen und Sie sind für den Abschluss des Kanalerstellungsprozesses zuständig. Navigieren Sie zur Anzeige "Benachrichtigungen" im Netzmonitor, wo ein Alert zu Erstellung des Kanals angezeigt wird. Suchen Sie die zu bearbeitende Anforderung auf den Unterregisterkarten "Alle" oder "Anstehend". Klicken Sie auf **Anforderung überprüfen**, um Details zum Kanal anzuzeigen, und klicken Sie auf **Genehmigen**. Klicken Sie anschließend auf **Anforderung übergeben**. 

Herzlichen Glückwunsch! Sie haben soeben einen verwalteten Kanal erstellt. 

In einem Netz mit 15 Mitgliedern (14 Banken und Sie als vertrauenswürdiger Dritter) könnten Dutzende solcher Kanäle angezeigt werden. Bank A und B auf einem Kanal. Bank B und F auf einem Kanal. Banken können unterschiedliche Kanäle für jede von ihnen gehandelte Währung wählen. In jedem Fall aber erstellt der vertrauenswürdige Dritte den Kanal, bewilligt die Transaktionen und fungiert als alleiniger Operator. 

# Chaincode instanziieren

Der Kanal wurde erfolgreich erstellt, aber es muss noch Chaincode zugeordnet werden. In einem Kanal, der für Devisentauschgeschäfte vorgesehen ist, setzt dieser Chaincode vermutlich alle drei Mitglieder des Kanals voraus (die beiden Banken und Sie als vertrauenswürdigen Dritten), um **jede** Transaktion zu bewilligen. Dieser Chaincode kann auch geschrieben werden, um einen Datensatz des *Ergebnisses* dieser Transaktion an einen verwalteten "Nur-Lese"-Kanal zu senden, der aus allen Mitgliedern im Netz besteht (mehr dazu später). Bevor ein Chaincode instanziiert werden kann, muss er zuvor auf den Peers der Mitglieder installiert werden. Sobald dies der Fall ist, kann er durch den Kanaloperator (das sind Sie) instanziiert werden.  

Die Vorgehensweise zum Installieren und Instanziieren dieser Art von Chaincode unterscheidet sich nicht von dem Vorgehen bei anderem Chaincode (im Unterschied zur Anzahl der Operatoren, die erforderlich ist, um die Instanziierung zu genehmigen). Also folgen Sie den Anweisungen unter [Chaincode installieren und instanziieren](install_instantiate_chaincode.html.html).

# Verwalteter Nur-Lese-Kanal

Angenommen - um diesen Anwendungsfall fortzusetzen -, finanzwirtschaftliche Regelungen zu Devisentauschgeschäften erfordern auch einen Kanal im Netz, auf dem sich jede Bank in einem passiven "Nur-Lese"-Modus befindet, sodass das **Ergebnis** dieser Währungstransaktionen überwacht werden kann. Dadurch wird z. B. sichergestellt, dass Bank A - die gerade Millionen von Dollars bei einer "Wette" an Bank B verloren hat - nicht versäumt, die Transaktion zur Überweisung des Millionenbetrags an Bank B zu bewilligen (bei der Wette ging es darum, wie viel der Wert einer Währung innerhalb eines bestimmten Zeitraums steigen oder fallen würde). 

Um einen solchen Kanal zu erstellen, müssen Sie dieselben Verfahren zum Erstellen eines Kanals befolgen wie zuvor, nur anstatt zwei Banken einzuladen und Ihnen die "Schreibberechtigung" zu geben (mit Ihnen als alleinigem Operator), laden Sie **alle** Banken ein und geben ihnen allen die "Leseberechtigung". Befolgen Sie dann denselben Prozess für die Installation und Instanziierung des Chaincodes. 

## {{site.data.keyword.IBM_notm}} Support 

{{site.data.keyword.IBM_notm}} bietet Support für von {{site.data.keyword.IBM_notm}} implementierte {{site.data.keyword.blockchain}}-Lösungen. Rufen Sie die {{site.data.keyword.blockchainfull_notm}}-Support-Details über [{{site.data.keyword.blockchainfull_notm}} DockerHub
![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](https://hub.docker.com/u/ibmblockchain/) ab und durchsuchen Sie die verfügbaren Support-Engagements.

Umfassende Informationen zu allen Hyperledger Fabric v1.0-Features und -Funktionen finden Sie in der
[Hyperledger Fabric-Dokumentation ![Symbol für externen Link](images/external_link.svg "Symbol für externen Link")](http://hyperledger-fabric.readthedocs.io/en/latest/).

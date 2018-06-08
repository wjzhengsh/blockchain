---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-29"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Bekannte Probleme

Die folgenden Probleme sind bereits dokumentiert:
- Das Konfigurieren einer externen Zertifizierungsstelle (CA) wird noch nicht unterstützt.  Als Alternative können Sie Administratorzertifikate über den Network Monitor generieren und hochladen. Weitere Informationen finden Sie unter [Clientseitige Zertifikate generieren](v10_application.html#generating-the-client-side-certificates) und in der Beschreibung auf [der Registerkarte "Zertifikate" der Anzeige "Mitglied"](v10_dashboard.html#members) im Network Monitor.
- Wenn Sie im Network Monitor eines Starter Plan-Netzes auf **Protokolle anzeigen** für die in der Anzeige "Übersicht" aufgeführten Knoten klicken, wird die Kibana-Schnittstelle für {{site.data.keyword.cloud}} Logging geöffnet. Standardmäßig ist Kibana so vorkonfiguriert, dass Protokolle mit Aktivitäten der letzten 15 Minuten angezeigt werden. Wenn in den letzten 15 Minuten keine Aktivitäten stattgefunden haben, wird die Nachricht 'Keine Ergebnisse gefunden' angezeigt. Wenn Sie andere Protokolle anzeigen möchten, können Sie auf das Zeitgebersymbol in der rechten oberen Ecke unterhalb des Benutzernamens klicken und einen längeren Zeitraum festlegen, z. B. die aktuelle Woche oder den aktuellen Monat.
- Möglicherweise treten Stabilitätsprobleme auf, wie z. B. das Zurückfallen von Proxys, und Sie müssen einen Neustart durchführen. Versuchen Sie vor dem Neustart, die Standardzeitlimitwerte in den Fabric-SDKs Ihrer Anwendungen durch höhere Werte zu überschreiben. Weitere Informationen zum Festlegen von Zeitlimitwerten finden Sie in [Zeitlimitwerte in Fabric-SDKs festlegen](v10_application.html#set-timeout-in-sdk).

Informationen zum Anfordern von Unterstützung und Hilfe für Ihr {{site.data.keyword.blockchainfull_notm}} Platform-Netz in {{site.data.keyword.Bluemix}} finden Sie in [Unterstützung anfordern](ibmblockchain_support.html).

<!--
## Updating chaincode with Enterprise Plan migration to Hyperledger Fabric 1.1
-	Users who migrate from networks based on Hyperledger Fabric 1.0 to networks based on Fabric 1.1 will need to update the dependencies in their chaincode. If they do not, there is a risk of a service disruption.
- This does not apply to users that uploaded their chaincode without dependencies, using a .go file.
- **Update your chaincode using the following steps:**
  **1.** You can use any  golang vendoring tool to update your chaincode. It will be easiest to use the same tool that was used to include dependencies in the original file. Many early Fabric samples used the govendor tool. If your chaincode used govendor, you can update your dependencies using the following command in the directory above the vendor folder.
      govendor update all +v
  * You can use `go build` to check that the new code compiles and that the update worked.
  * You can test your chaincode by installing and instantiating it on a Starter Plan Network. All chaincode that works on Starter Plan will also work on Enterprise Plan after the upgrade.
  **2.** Once your chaincode dependencies are up to date, you can use the network monitor to [update your chaincode](install_instatiate_chaincode.html#Updating a chaincode).
-->

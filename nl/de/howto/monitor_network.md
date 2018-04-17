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

# {{site.data.keyword.blockchain}}-Netz überwachen

Dieses Lernprogramm zeigt, wie Sie Statusinformationen zu Ihrem {{site.data.keyword.blockchain}}-Netz in {{site.data.keyword.Bluemix_short}} anzeigen und überwachen können.
{:shortdesc}


## Kanäle überwachen

Rufen Sie den Netzmonitor auf und suchen Sie nach dem Kanal in der Anzeige "Kanal", den Sie anzeigen und überwachen möchten. In der Anzeige des betreffenden Kanals können Sie die Datenstatusinformationen, Mitglieder und den instanziierten Chaincode dieses Kanals auf drei Registerkarten anzeigen:

* **Kanalüberblick**  
  Die Registerkarte "Kanalüberblick" zeigt die Blockinformationen zu diesem Kanal:
    * Eine Reihe von Datenpunkten, zu denen die Gesamtzahl der erstellten Blöcke gehört, das Zeitintervall seit der letzten Transaktion, die Anzahl von Chaincode-Instanziierungen und die Anzahl von Chaincode-Aufrufen.
    * Eine Tabelle, in der alle Blöcke dieses Kanals aufgeführt werden. Wenn Sie einen Block erweitern, werden detaillierte Informationen zu dem Block angezeigt.  

  ![Kanalüberblick](../images/channel_overview_detail.png "Kanalüberblick")  

* **Mitglieder**  
  Auf der Registerkarte "Mitglieder" werden die Informationen der Mitglieder auf diesem Kanal angezeigt, einschließlich der E-Mail-Adressen für die Operatoren der Organisation.
  ![Kanalmitglieder](../images/channel_members.png "Kanalmitglieder")  

* **Chaincode**  
  Auf der Registerkarte "Chaincode" werden alle Chaincodes aufgelistet, die auf diesem Kanal instanziiert werden - mit der Chaincode-ID, der Version und der Anzahl Peers, die den Chaincode ausführen.   

  Erweitern Sie eine Chaincode-Zeile, um detaillierte Informationen zu dem Chaincode abzurufen:  
    * Sie können auf **JSON** klicken, um die JSON-Datei des Chaincodes anzuzeigen.
    * Sie können auf **Protokolle** klicken, um Protokolle des Chaincodes anzuzeigen.
    * Sie können auf **Löschen** klicken, um die aktiven Chaincode-Container zu entfernen.
    **Hinweis**: Durch das Löschen des aktiven Chaincode-Containers wird der Chaincode selbst nicht gelöscht. Ein instanziierter Chaincode in einem Blockchain-Netz kann nicht gelöscht werden.

  ![Kanal-Chaincode](../images/channel_chaincode.png "Kanal-Chaincode")


## Chaincodes überwachen

Rufen Sie den Netzmonitor auf und öffnen Sie die Anzeige "Code installieren" (Anzeige "Chaincode" in einem Enterprise Plan-Netz). Wenn Sie aktive Chaincodes haben, wählen Sie einen Peer in der Dropdown-Liste aus, sodass alle Chaincodes für diesen Peer in der Tabelle mit IDs und Versionen der Chaincodes angezeigt werden. Über diese Anzeige können Sie die Installation und Instanziierung für Ihren Chaincode durchführen. Weitere Informationen finden Sie unter [Chaincode installieren und instanziieren](install_instantiate_chaincode.html).

  ![Chaincode](../images/chaincode_install_overview.png "Chaincode")


## Beispielanwendungen überwachen

In einem Starter Plan-Netz können Sie Beispielanwendungen in der Anzeige "Beispiele ausprobieren" des Netzmonitors anzeigen und darauf zugreifen. Wenn Sie eine Beispielanwendung bereitgestellt haben, können Sie auf die Schaltfläche **Starten** klicken, um die Schnittstelle Ihrer Anwendung aufzurufen, oder Sie können auf den Link **Auf GitHub anzeigen** klicken, um das Code-Repository aufzusuchen. Weitere Informationen finden Sie unter [Beispielanwendungen bereitstellen](prebuilt_samples.html).

  ![Beispielanwendungen](../images/sampleappflow0.png "Beispielanwendungen")

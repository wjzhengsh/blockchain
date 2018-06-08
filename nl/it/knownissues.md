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


# Problemi noti

I seguenti problemi sono già stati segnalati:
- La configurazione di una CA esterna non è ancora supportata.  In alternativa, puoi generare e caricare i certificati di gestione tramite il monitoraggio della rete. Per ulteriori informazioni, vedi [Generazione dei certificati lato client](v10_application.html#generating-the-client-side-certificates) e la descrizione nella [scheda "Certificati" della schermata "Membro"](v10_dashboard.html#members) nel monitoraggio della rete.
- Nel monitoraggio della rete di una rete piano Starter, quando fai clic su **Visualizza log** nei nodi elencati nella schermata "Panoramica", viene aperta l'interfaccia kibana di registrazione {{site.data.keyword.cloud}}. Per impostazione predefinita, kibana è preconfigurato per mostrare i log degli ultimi 15 minuti di attività. Se non c'è stata attività negli ultimi 15 minuti, visualizzerai un messaggio 'Nessun risultato trovato'. Per visualizzare altri log, puoi semplicemente fare clic sull'icona timer nell'angolo in alto a destra sotto il tuo nome utente e impostare un intervallo di tempo ampio, come 'Questa settimana' o 'Questo mese'.
- Potresti riscontrare problemi di stabilità, come il failover dei proxy e dovrai iniziare nuovamente. Prima di riavviare, prova a sovrascrivere i valori di timeout predefiniti nelle SDK Fabric delle tue applicazioni con numeri più grandi. Per ulteriori informazioni sull'impostazione dei valori di timeout, vedi [Configurazione dei valori di timeout nelle SDK Fabric](v10_application.html#set-timeout-in-sdk).

Per supporto e assistenza con la tua rete {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.Bluemix}}, consulta [Richiesta di assistenza tecnica](ibmblockchain_support.html).

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

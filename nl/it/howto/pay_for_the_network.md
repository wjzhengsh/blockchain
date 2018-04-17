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

# Pagamento per la rete

**Nota**: {{site.data.keyword.blockchainfull}} Platform Starter Plan è gratuito per Beta.

{{site.data.keyword.blockchainfull_notm}} Platform prevede un addebito mensile con costi per le adesioni e costi per i peer. A seconda del piano di rete da te scelto e dalla quantità di risorse da te utilizzata, i tuoi pagamenti sono differenti. Prendendo ad esempio Enterprise Plan, i membri della rete devono pagare $1.000 al mese come costo di adesione e altri $1.000 al mese per ciascuno dei loro peer nella rete. Il costo mensile viene addebitato ogni giorno su base proporzionale. Ad esempio, un membro (costo di adesione associato di $1.000) di due peer (costo per ogni peer di $1.000 X 2 peer) deve pagare $3.000 ogni mese. Se il mese ha 30 giorni, il membro paga $ 100 ($3.000/30) ogni giorno.

I membri della rete possono pagare la loro fattura con i loro account IBM Cloud che contengono lo spazio per creare l'istanza di rete. In alternativa, un membro della rete può coprire l'addebito per tutti i membri nella rete e pagare per l'intera rete.


## Pagamento solo dei tuoi costi
L'addebito ai tuoi account {{site.data.keyword.cloud_notm}} verrà eseguito quotidianamente per il tuo costo di adesione e per i costi dei peer.


## Pagamento per l'intera rete
Un singolo membro nella rete può coprire le spese per tutti i membri della rete. Per pagare in questa modalità a singolo pagatore, il pagatore e gli altri membri della rete devono completare la seguente procedura.

### Prerequisiti
Ogni membro della rete deve avere un account {{site.data.keyword.cloud_notm}}. [Registrane](https://console.bluemix.net/registration/) uno, se non lo hai.

### Procedure
1. Il pagatore crea degli spazi Cloud Foundry separati in {{site.data.keyword.cloud_notm}} per ogni utente che verrà invitato alla rete:
   1. Accedi a {{site.data.keyword.cloud_notm}}.
   2. Dalla barra dei menu, fai clic su **Gestisci** > **Account** > **Organizzazioni Cloud Foundry**.
   3. Fai clic su **Visualizza dettagli** dell'organizzazione Cloud Foundry per cui creerai la rete. Se non hai un'organizzazione per la rete, fai clic sul pulsante **Aggiungi una nuova organizzazione Cloud Foundry** per crearne una.
   4. Fai clic sul pulsante **Aggiungi uno spazio Cloud Foundry** per creare uno spazio per un membro della rete.  Seleziona la regione per lo spazio, denominala e fai clic su **Aggiungi**.  Nota: solo il creatore dello spazio può accedere allo spazio creato.
   5. Ripetere il passo 4 per creare degli spazi separati per tutti gli utenti che saranno invitati alla rete.
2. Il pagatore invita gli altri utenti al suo account {{site.data.keyword.cloud_notm}} e fornisce loro l'accesso allo specifico spazio. Ogni utente ha accesso solo a un singolo spazio.
   1. Dalla barra dei menu, fai clic su **Gestisci** > **Account** > **Utenti**.  
   2. Fai clic su **Invita utenti** e assegna l'accesso utente:
      1. Specifica l'indirizzo email di un singolo utente che inviterai alla tua organizzazione Cloud Foundry.
      2. Nella sezione **Accesso Cloud Foundry**, effettua le seguenti selezioni:
         - **Organizzazione**: l'organizzazione Cloud Foundry per creare la rete.
         - **Ruoli organizzazione**: Revisore
         - **Regione**: la regione dove hai creato lo spazio per questo utente.
         - **Spazio**: lo spazio che hai creato per questo utente.
         - **Ruoli spazio**: Sviluppatore.
      3. Fai clic su **Invita utenti**.
   3. Ripeti i passi per invitare gli utenti e assegnare l'accesso utente per ogni utente.
3. Nel dashboard {{site.data.keyword.blockchainfull_notm}} Platform, il pagatore invita gli utenti alla rete blockchain. Per ulteriori informazioni sull'invito di membri della rete, vedi [Membri](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Ogni utente riceve quindi una email di notifica che contiene un invito ad aderire alla rete. L'utente può completare la seguente procedura per aderire alla rete.
   1. Fai clic sul pulsante "Proceed" nella email di notifica; verrai così reindirizzato alla pagina del servizio blockchain in {{site.data.keyword.cloud_notm}}.
   2. Assicurati di utilizzare l'organizzazione e lo spazio {{site.data.keyword.cloud_notm}} corretti.
      1. Accedi a {{site.data.keyword.cloud_notm}} e fai clic sull'avatar del profilo nell'angolo superiore destro.
      2. Scegli l'account del pagatore dall'elenco a discesa **Account**. Nota: l'addebito sarà a carico dell'account e dell'organizzazione dove esegui il provisioning dell'istanza del servizio blockchain.  
   4. Nella pagina del servizio blockchain, crea la tua istanza del servizio blockchain:
      1. Nel campo **Service Name**, immetti un nome descrittivo per poterlo poi in seguito riconoscere.
      2. Verificare che l'organizzazione e lo spazio siano quelli a cui ti ha invitato il pagatore.
      3. Seleziona il piano di adesione con cui creare la tua istanza di rete.
      4. Fai clic su **Create**.
   5. Dopo che hai creato l'istanza del servizio blockchain, attieniti alla procedura guidata per aderire alla rete. Per ulteriori informazioni, vedi [Adesione a una rete](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitazioni note
- Poiché tutti i membri sono all'interno dell'account {{site.data.keyword.cloud_notm}} dei pagatori, il pagatore ha accesso alle istanze blockchain di tutti i membri e può impersonarli.Pertanto, l'uso di questa modalità di pagamento è indicata per gli ambienti POC oppure nei casi il cui il pagatore stia occupandosi di tutta la gestione di {{site.data.keyword.blockchainfull_notm}} Platform e che il providing dei membri venga eseguito solo con un'applicazione.  
- Dopo aver aggiunto tutti i membri all'account {{site.data.keyword.cloud_notm}} del pagatore e avere assegnato loro l'accesso per eseguire il provisioning delle istanze blockchain e aderire alla rete, il pagatore dà anche ai membri l'accesso per creare altri servizi, il che potrebbe causare degli addebiti aggiuntivi.  
- Tutti i membri nell'organizzazione Cloud Foundry del pagatore possono vedere tutti gli spazi nell'organizzazione. Non possono tuttavia modificare le organizzazioni perché non dispongono degli accessi.

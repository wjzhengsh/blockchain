---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Modalità di pagamento

{{site.data.keyword.blockchainfull}} Platform prevede un addebito mensile con costi per le adesioni e costi per i peer. I membri della rete possono pagare la loro fattura con i loro account IBM Cloud che contengono lo spazio per creare l'istanza di rete. In alternativa, un membro della rete può coprire la fattura per tutti i membri nella rete e pagare per l'intera rete.
{:shortdesc}

A seconda del piano di rete da te scelto e dalla quantità di risorse da te utilizzata, i tuoi pagamenti sono differenti. Per ulteriori informazioni sui prezzi, vedi [Prezzi](pricing.html).

## Prerequisiti
Ogni membro della rete deve avere un account {{site.data.keyword.cloud_notm}} a pagamento, ad esempio un account **Pagamento a consumo**. Se non hai un account, [registrane](https://console.bluemix.net/registration/) uno ed eseguine l'upgrade a un account a pagamento. Per eseguire l'upgrade del tuo account a un tipo Pagamento a consumo, vai a **Gestisci** > **Fatturazione e utilizzo** > **Fatturazione** nella console IBM Cloud e fai clic su **Aggiungi carta di credito**.


## Pagamento solo dei tuoi costi
Dopo che hai creato eventuali reti blockchain con {{site.data.keyword.blockchainfull_notm}} Platform, ai tuoi account {{site.data.keyword.cloud_notm}} verrà eseguito quotidianamente l'addebito del tuo costo di adesione e dei costi di peer. 


## Pagamento per l'intera rete
Un singolo membro nella rete può coprire le spese per tutti i membri della rete.  Per pagare in questa modalità a singolo pagatore, il pagatore e gli altri membri della rete devono completare la seguente procedura.

1. Il pagatore crea degli spazi Cloud Foundry separati in {{site.data.keyword.cloud_notm}} per ogni utente che verrà invitato alla rete:
   1. Accedi a {{site.data.keyword.cloud_notm}}.
   2. Dalla barra dei menu, fai clic su **Gestisci** > **Account** > **Organizzazioni Cloud Foundry**.
   3. Fai clic su **Visualizza dettagli** dell'organizzazione Cloud Foundry per cui creerai la rete.  Se non hai un'organizzazione per la rete, fai clic sul pulsante **Aggiungi una nuova organizzazione Cloud Foundry** per crearne una.
   4. Fai clic sul pulsante **Aggiungi uno spazio Cloud Foundry** per creare uno spazio per un membro della rete.  Seleziona la regione per lo spazio, denominala e fai clic su **Aggiungi**.  Nota: solo il creatore dello spazio può accedere allo spazio creato.
   5. Ripetere il passo 4 per creare degli spazi separati per tutti gli utenti che saranno invitati alla rete.
2. Il pagatore invita gli altri utenti al suo account {{site.data.keyword.cloud_notm}} e fornisce loro l'accesso allo specifico spazio.  Ogni utente ha accesso solo a un singolo spazio.
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
4. Ogni utente riceve quindi una email di notifica che contiene un invito ad aderire alla rete.  L'utente può completare la seguente procedura per aderire alla rete.
   1. Fai clic sul pulsante "Procedi" nella email di notifica; verrai così reindirizzato alla pagina del servizio blockchain in {{site.data.keyword.cloud_notm}}.
   2. Assicurati di utilizzare l'organizzazione e lo spazio {{site.data.keyword.cloud_notm}} corretti.
      1. Accedi a {{site.data.keyword.cloud_notm}} e fai clic sull'avatar del profilo nell'angolo superiore destro.
      2. Scegli l'account del pagatore dall'elenco a discesa **Account**.  Nota: l'addebito sarà a carico dell'account e dell'organizzazione dove esegui il provisioning dell'istanza del servizio blockchain.  
   4. Nella pagina del servizio blockchain, crea la tua istanza del servizio blockchain:
      1. Nel campo **Nome servizio**, immetti un nome descrittivo per poterlo poi in seguito riconoscere.
      2. Verificare che l'organizzazione e lo spazio siano quelli a cui ti ha invitato il pagatore.
      3. Seleziona il piano di adesione con cui creare la tua istanza di rete.
      4. Fai clic su **Crea**.
   5. Dopo che hai creato l'istanza del servizio blockchain, attieniti alla procedura guidata per aderire alla rete.  Per ulteriori informazioni, vedi [Adesione a una rete](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitazioni note
- Poiché tutti i membri sono all'interno dell'account {{site.data.keyword.cloud_notm}} dei pagatori, il pagatore ha accesso alle istanze blockchain di tutti i membri e può impersonarli.  Pertanto, l'uso di questa modalità di pagamento è indicata per gli ambienti POC oppure nei casi il cui il pagatore stia occupandosi di tutta la gestione di {{site.data.keyword.blockchainfull_notm}} Platform e che il providing dei membri venga eseguito solo con un'applicazione.  
- Dopo aver aggiunto tutti i membri all'account {{site.data.keyword.cloud_notm}} del pagatore e avere assegnato loro l'accesso per eseguire il provisioning delle istanze blockchain e aderire alla rete, il pagatore dà anche ai membri l'accesso per creare altri servizi, il che potrebbe causare degli addebiti aggiuntivi.  
- Tutti i membri nell'organizzazione Cloud Foundry del pagatore possono vedere tutti gli spazi nell'organizzazione.  Non possono tuttavia modificare le organizzazioni perché non dispongono degli accessi.

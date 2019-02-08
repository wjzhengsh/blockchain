---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Modalità di pagamento
{: #paying-mode}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform prevede un addebito mensile con costi per le adesioni e costi per i peer. I membri della rete possono pagare la fattura con i loro account {{site.data.keyword.cloud_notm}} che contengono lo spazio per creare l'istanza della rete. In alternativa, un membro della rete può coprire la fattura per altri membri nella rete o anche per tutti i membri e pagare per l'intera rete. Il pagatore può anche trasferire le fatture ad altri membri, se necessario.
{:shortdesc}

A seconda del piano di rete da te scelto e dalla quantità di risorse da te utilizzata, i tuoi pagamenti sono differenti. Per ulteriori informazioni sui prezzi, vedi [Prezzi](/docs/services/blockchain/howto/pricing.html).

## Prerequisiti
{: #prereq}

Ogni membro della rete deve avere un account {{site.data.keyword.cloud_notm}} a pagamento, ad esempio un account con **Pagamento a consumo**, in modo che possano creare istanze del servizio {{site.data.keyword.blockchainfull_notm}} Platform su {{site.data.keyword.cloud_notm}}. Se non hai un account, [registrane](https://console.bluemix.net/registration/) uno ed eseguine l'upgrade a un account a pagamento. Per eseguire l'upgrade del tuo account a un tipo Pagamento a consumo, vai a **Gestisci** > **Fatturazione e utilizzo** > **Fatturazione** nella console {{site.data.keyword.cloud_notm}} e fai clic su **Aggiungi carta di credito**.


## Pagamento solo dei tuoi costi
{: #pay-your-own}

Dopo aver creato o unito le reti blockchain su {{site.data.keyword.blockchainfull_notm}} Platform, ai tuoi account {{site.data.keyword.cloud_notm}} verranno addebitati i costi di adesione e i costi di peer.


## Pagamento per altri membri della rete
{: #pay-for-others}

Un membro della rete può coprire le spese per uno o più altri membri della rete nella stessa rete blockchain. Per pagare in questa modalità, il pagatore e gli altri membri della rete devono completare la seguente procedura.

1. Il pagatore crea spazi Cloud Foundry separati in {{site.data.keyword.cloud_notm}} per i membri per i quali pagherà.
   1. Accedi a {{site.data.keyword.cloud_notm}}.
   2. Dalla barra dei menu, fai clic su **Gestisci** > **Account** > **Organizzazioni Cloud Foundry**.
   3. Fai clic su **Visualizza dettagli** dell'organizzazione Cloud Foundry per cui creerai la rete. Se non hai un'organizzazione per la rete, fai clic sul pulsante **Aggiungi una nuova organizzazione Cloud Foundry** per crearne una.
   4. Fai clic sul pulsante **Aggiungi uno spazio Cloud Foundry** per creare uno spazio per un altro membro della rete. Seleziona la regione per lo spazio, denominala e fai clic su **Aggiungi**.  Nota: solo il creatore dello spazio può accedere allo spazio creato.  
   **Nota**: se il pagatore paga per più di un membro, dovrà ripetere questo passo per creare spazi separati per ciascuno dei membri per i quali pagherà.
2. Il pagatore invita gli altri membri al suo account {{site.data.keyword.cloud_notm}} e fornisce loro l'accesso ai suoi specifici spazi. Assicurati che ogni membro possa avere accesso a un solo spazio.
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
   **Nota**: se il pagatore paga per più di un membro, dovrà ripetere questo passo per invitare altri membri e assegnare l'accesso per ciascuno di essi.
3. Il pagatore crea una rete blockchain {{site.data.keyword.blockchainfull_notm}} Platform e invita altri membri alla rete blockchain nel Monitoraggio della rete della rete. Per ulteriori informazioni sull'invito di membri della rete, vedi [Membri](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Ogni membro riceve quindi una email di notifica che contiene un invito ad aderire alla rete. Il membro può completare la seguente procedura per aderire alla rete.
   1. Fai clic sul pulsante "Procedi" nella email di notifica; verrai così reindirizzato alla pagina del servizio blockchain in {{site.data.keyword.cloud_notm}}.
   2. Accedi a {{site.data.keyword.cloud_notm}} e assicurati di utilizzare l'organizzazione e lo spazio {{site.data.keyword.cloud_notm}} corretti, a cui ti aggiunge il pagatore.
      1. Nella console {{site.data.keyword.cloud_notm}}, fai clic sull'avatar del profilo nell'angolo superiore destro.
      2. Scegli l'account del pagatore dall'elenco a discesa **Account**.  Nota: l'addebito sarà a carico dell'account e dell'organizzazione dove esegui il provisioning dell'istanza del servizio blockchain.  
   3. Nel catalogo {{site.data.keyword.cloud_notm}}, cerca **Blockchain** e crea un'istanza del servizio.
      1. Nel campo **Nome servizio**, immetti un nome descrittivo per poterlo poi in seguito riconoscere.
      2. Verifica che l'organizzazione e lo spazio siano quelli a cui ti ha aggiunto il pagatore.
      3. Seleziona il piano di adesione con cui creare la tua istanza di rete. Il tuo piano di adesione deve essere uguale a quello con cui il pagatore crea la rete.
      4. Fai clic su **Crea**.
   4. Dopo che hai creato l'istanza del servizio blockchain, attieniti alla procedura guidata per aderire alla rete.  Per ulteriori informazioni, vedi [Adesione a una rete](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitazioni note
{: #pay-for-others-limitation}
- Poiché tutti i membri sono all'interno dell'account {{site.data.keyword.cloud_notm}} dei pagatori, il pagatore ha accesso alle istanze blockchain di tutti i membri e può impersonarli. Pertanto, questa modalità di pagamento viene utilizzata al meglio per gli ambienti PoC o nei casi in cui il pagatore si occupi di tutta la gestione di {{site.data.keyword.blockchainfull_notm}} Platform e ai membri venga fornita solo un'applicazione.  
- Tutti i membri nell'organizzazione Cloud Foundry del pagatore possono vedere tutti gli spazi nell'organizzazione.  Non possono tuttavia modificare le organizzazioni perché non dispongono degli accessi.  
- Dopo aver aggiunto tutti i membri all'account {{site.data.keyword.cloud_notm}} del pagatore e avere assegnato loro l'accesso per eseguire il provisioning delle istanze blockchain e aderire alla rete, il pagatore dà anche ai membri l'accesso per creare altri servizi, il che potrebbe causare degli addebiti aggiuntivi. Tutti i membri possono firmare un accordo con la fiducia che non creeranno alcun servizio non autorizzato nei loro account per causare costi aggiuntivi all'account del pagatore.  

## Lasciare che un membro paghi per te
{: #let-other-pay}

In alternativa all'aggiunta di account di altri membri all'account del pagatore, gli altri membri possono anche aggiungere l'account del pagatore ai propri account come **Gestore di fatturazione**. Per pagare in questa modalità, il pagatore e gli altri membri della rete devono completare la seguente procedura.

1. Il pagatore crea una rete blockchain {{site.data.keyword.blockchainfull_notm}} Platform con l'account del pagatore.
2. Gli altri membri aggiungono l'account del pagatore al loro account {{site.data.keyword.cloud_notm}} come **Gestore di fatturazione**.
  1. Dalla barra dei menu nella parte superiore della console {{site.data.keyword.cloud_notm}}, fai clic su **Gestisci** > **Account** > **Utenti**.
  2. Nella pagina **Utenti**, fai clic sul pulsante **Invita utenti**.
  3. Aggiungi l'account del pagatore sotto il campo **Indirizzo email**.
  4. Nella sezione **Accesso Cloud Foundry**, scegli l'organizzazione a cui vuoi aggiungere l'account del pagatore dall'elenco a discesa.
  5. Scegli **Gestore di fatturazione** come **Ruoli organizzazione**.
  6. Configura di conseguenza gli altri campi, quindi fai clic su **Invita utenti**.  
3. Il pagatore invita gli altri membri alla rete blockchain nel Monitoraggio della rete della rete. Per ulteriori informazioni sull'invito di membri della rete, vedi [Membri](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Ogni membro riceve quindi una email di notifica che contiene un invito ad aderire alla rete. Il membro può quindi aderire alla rete. Per ulteriori informazioni, vedi il **passo 4** in [Pagamento per altri membri della rete](#pay-for-others).

### Limitazione nota
{: #let-other-pay-limitation}

Dopo aver aggiunto l'account {{site.data.keyword.cloud_notm}} del pagatore come gestore di fatturazione all'account di altri membri e gli altri membri acquistano altri servizi utilizzando il proprio account, il pagatore deve coprire anche tali spese. Tutti i membri possono firmare un accordo con la fiducia che non creeranno alcun servizio non autorizzato nei loro account per causare costi aggiuntivi all'account del pagatore.  


## Trasferimento di fatturazioni ad altri membri
{: #transfer-billing}

Mentre i membri della rete possono avere un membro che paghi per loro, in seguito, altri membri possono subentrare nelle fatturazioni. Puoi rimuovere l'account del pagatore dai tuoi account in modo che il pagatore possa smettere di pagare per te.

1. Dalla barra dei menu nella parte superiore della console {{site.data.keyword.cloud_notm}}, fai clic su **Gestisci** > **Account** > **Organizzazioni Cloud Foundry**.
2. Fai clic sull'organizzazione a cui aggiungi l'account del pagatore.
3. Passa alla scheda **Utenti**.
4. Trova l'account del pagatore nella tabella degli utenti e fai clic sul link **Rimuovi da organizzazione**.

Dopo aver rimosso l'account del pagatore dal tuo account, al tuo account verrà addebitato il costo di adesione e il costo di peer della tua rete blockchain.

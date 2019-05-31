---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: Swagger APIs, authorize, service credentials, disable API access, IBM Cloud

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Interazione con la rete utilizzando le API Swagger
{: #ibp-swagger}

{{site.data.keyword.blockchainfull_notm}} Platform presenta diverse API REST in Swagger che puoi utilizzare per gestire i nodi, i canali, i peer e i membri della tua rete. Le tue applicazioni possono utilizzare queste API per controllare importanti risorse di rete senza usare il Monitoraggio della rete.

{:shortdesc}

Prima di iniziare, devi creare un'istanza del servizio [{{site.data.keyword.blockchain}} Platform ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) su {{site.data.keyword.cloud_notm}} e creare, o aderire a, una rete blockchain piano Starter <!--or Enterprise Plan -->.


## Richiamo delle credenziali di rete
{: #ibp-swagger-retrieving-network-credentials}

Accedi al Monitoraggio della rete della tua rete Blockchain e apri la schermata "API" dal navigatore a sinistra. Puoi visualizzare le tue credenziali di rete per le API REST. Autorizzerai successivamente le API utilizzando i valori di chiave ("key") e segreto ("secret") qui visualizzati ed eseguirai le API con il "network_id" come parametro. Fai clic su **Show secret** per visualizzare il valore del campo segreto. Copia i valori dei campi key, secret e network_id, che puoi utilizzare successivamente nell'IU Swagger.

La **Figura 1** mostra la schermata "API":
![Schermata API](../images/API_screen_starter.png "Schermata API")
*Figura 1. API*

Se stai utilizzando piano Starter, è possibile passare da un'organizzazione all'altra nel Monitoraggio della rete. Con piano Starter, le due organizzazioni sono configurate per impostazione predefinita. Passare da un'organizzazione all'altra può essere utile per provare le API REST dalla prospettiva di ciascuna organizzazione. Per ottenere le credenziali per un'altra organizzazione nella tua rete, fai clic sul tuo nome utente nell'angolo superiore destro della console Monitoraggio della rete. Nel menu che si apre, fai clic sulla freccia a discesa accanto all'organizzazione per visualizzare tutte le organizzazioni. Seleziona l'organizzazione a cui desideri passare e visualizza le credenziali di rete associate.

La **Figura 2** mostra come passare da un'organizzazione all'altra:

![Switch tra le organizzazioni](../images/switch_orgs_starter.gif "Switch tra le organizzazioni")  
*Figura 2. Switch tra le organizzazioni*


## Autorizzazione delle API Swagger
{: #ibp-swagger-authorizing-swagger}

Fai clic sul link **IU Swagger** nella schermata "API" per aprire l'IU Swagger.  

Nell'IU Swagger, fai clic sul pulsante **Autorizza**; viene visualizzata la finestra di autorizzazione. Immetti il valore di chiave (key) e segreto (secret) nelle tue credenziali di rete come nome utente e password e fai clic su **Autorizza** e quindi su **Fatto**. Ora sei pronto ad eseguire le API. Nota: se aggiorni il tuo browser, devi eseguire nuovamente l'autorizzazione con le tue credenziali.

Utilizzando l'autenticazione di tipo Autenticazione di base, qualsiasi credenziale da te specificata nella finestra Autorizza viene memorizzata dopo che fai clic sui pulsanti **Autorizza** e quindi **Fatto** e viene passata in ciascuna chiamata API REST.

La **Figura 3** mostra il processo per autorizzare le API Swagger:

![Autorizza API](../images/swaggerUIAuthorize.gif "Autorizza API")  
*Figura 3. Autorizza API*


## Prova delle API
{: #ibp-swagger-try-out}

Fai clic sulla API REST che vuoi eseguire e fai clic sul pulsante **Provalo adesso**.

La **Figura 4** mostra il pulsante "Try it out" nella "IU Swagger":

![Pulsante Try it out nella IU Swagger](../images/swaggerUITryItOut.png "Pulsante Try it out nella IU Swagger")  
*Figura 4. Pulsante "Try it out" nella "IU Swagger"*

Dopo che hai fatto clic sul pulsante **Try it out**, puoi immettere i parametri obbligatori per utilizzare la API. Puoi trovare `networkID` nelle tue credenziali di rete e trovare gli altri parametri nel tuo Monitoraggio della rete. Dopo che hai immesso i parametri, fai clic su **Esegui** per eseguire la chiamata API REST sulla tua rete.

La **Figura 5** mostra i parametri nella "IU Swagger":

![Parametri nell'IU Swagger](../images/swaggerUIParams.png "Parametri nell'IU Swagger")  
*Figura 5. Immissione dei parametri*  

Dopo che hai fatto clic su **Esegui**, puoi vedere la risposta della chiamata API sulla tua rete. Puoi anche vedere un comando CURL che può richiamare la API direttamente dalla tua riga di comando.

La **Figura 6** mostra il comando CURL, l'URL e il corpo della risposta della API:

![Risposta della API nell'IU Swagger](../images/swaggerUICurlResponse.png "Risposta della API nell'IU Swagger")  
*Figura 6. Risposta della API*    

## Disabilitazione dell'accesso API
{: #ibp-swagger-turn-off}

Per impostazione predefinita, tutti gli utenti con un ruolo non Revisore in IBM Cloud possono visualizzare e utilizzare le **Credenziali di rete** visibili nel pannello delle API Swagger e possono pertanto gestire la loro rete utilizzando le API. Tuttavia, se preferisci non esporre le tue credenziali di rete API Swagger nell'IU, puoi copiare e proteggere i tuoi valori di chiave e segreto esistenti e generare delle nuove credenziali che non sono valide per l'uso con le API Swagger. Viene fornito un indicatore denominato resetCredentials che ti consente di controllare l'accesso completando la seguente procedura:

1. Attieniti alla procedura per generare delle nuove credenziali di rete, come descritto nel [dashboard Credenziali del servizio](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token).
2. Tuttavia, nella casella **Aggiungi parametri di configurazione inline**, incolla il seguente valore:
   ```
   {
     "resetCredentials": true
   }
   ```
   {:codeblock}
3. Fai clic su **Aggiungi**.

Ora, quando un utente accede al pannello delle API Swagger dall'IU, le informazioni **Credenziali di rete** nell'IU conterranno un valore di chiave e segreto generico che non è valido per gestire la tua rete. Le eventuali richieste API inoltrate utilizzando queste credenziali non verranno elaborate.  

Se in un secondo momento desideri esporre delle credenziali di rete valide nell'IU, ti basta ripetere la procedura sopra indicata per generare delle nuove credenziali lasciando però questa volta vuota la casella **Aggiungi parametri di configurazione inline**. Non hai bisogno di specificare alcun parametro.

Ora, le credenziali valide originali sono visibili nelle informazioni **Credenziali di rete** nell'IU e possono essere utilizzate per autenticare le API Swagger.

## Suggerimenti per la soluzione dei problemi
{: #ibp-swagger-troubleshooting}

### 401 Non autorizzato  
{: #ibp-swagger-401}

  Assicurati di disporre delle autorizzazioni necessarie per la API REST fornendo le tue credenziali di rete. Per ulteriori informazioni, vedi [Autorizzazione delle API Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-authorizing-swagger).

### Errore 400: Richiesta non valida
{: #ibp-swagger-400}

  Alcune API potrebbero prendere un argomento nel corpo della richiesta che funge da filtro per visualizzare i risultati solo per uno specifico peer. Nel corpo viene fornito un frammento di esempio che, se utilizzato, deve essere modificato per specificare il peer o l'elenco di peer su cui desiderare applicare il filtro. Per evitare questo errore, modificare il frammento per specificare un peer nella tua rete oppure rimuovere del tutto il frammento.

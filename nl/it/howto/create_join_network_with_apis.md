---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Creazione o unione di una rete con le API Swagger

{{site.data.keyword.blockchainfull}} Platform presenta diverse API REST che puoi utilizzare per creare o unire una rete blockchain in {{site.data.keyword.cloud_notm}}. Puoi provare queste API utilizzando la [IU Swagger](swagger_apis.html) associata alla tua rete.
{:shortdesc}


## Richiamo delle credenziali di autenticazione di base dell'API
{: #retrieve-id-token}

Prima di iniziare, devi creare un'istanza del servizio [{{site.data.keyword.blockchainfull_notm}} Platform ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.bluemix.net/catalog/services/blockchain) con piano Starter o piano Enterprise in {{site.data.keyword.cloud_notm}}.

Per utilizzare le API Swagger per creare o unire una rete, hai bisogno delle credenziali di autenticazione di base per assicurarti di avere l'accesso all'istanza del servizio in {{site.data.keyword.cloud_notm}}.

1. Nel tuo [dashboard {{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://console.bluemix.net/dashboard/apps/), apri l'istanza del servizio che hai creato.
2. Fai clic su **Credenziali del servizio** dal navigator di sinistra.
3. Fai clic sul pulsante "Nuova credenziale" nella pagina **Credenziali del servizio** per creare una nuova credenziale. Fornisci un nome alla credenziale, ad esempio *CreateJoin* e fai clic sul pulsante **Aggiungi**. Non devi immettere nulla nel campo "Aggiungi parametro di configurazione inline".
    ![Richiama credenziali del servizio](../images/service_credentials.gif "Richiama le credenziali del servizio")
4. Dopo avere creato la nuova credenziale, fai clic su **Visualizza credenziali** nell'intestazione **AZIONI** di tale credenziale. I contenuti della credenziale sono simili al seguente esempio:

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    `service_instance_id` viene utilizzato come ID utente di autenticazione di base e `service_instance_token` come password di autenticazione di base. Utilizza questi valori come credenziali di autenticazione di base quando richiami le API **Crea rete** o **Unisciti alla rete**.

    Se crei una rete blockchain prima di seguire i precedenti passi per creare una credenziale del servizio, i contenuti della credenziale includono anche le informazioni sulla rete e sono simili al seguente esempio:

    ```
    {
      "PeerOrg1": {
        "url": "https://ibmblockchain_xyz.ng.bluemix.net",
        "network_id": "92d511f7e587413c8a9848fdae595ef2",
        "key": "PeerOrg1",
        "secret": "T8eUA65l-qtznUHL10KzQ7IK-3BVWWfHu5-hpCiDdXCRQyNfeyIm1p5NT7g17l6U"
      }
    }
    ```
    {:codeblock}

    **Nota**: per l'API **Invita**, `key` viene utilizzato come ID utente di autenticazione di base e `secret` come password.


## Controllo delle ubicazioni della rete disponibili
{: #check-location}

Puoi utilizzare le API per creare le reti blockchain solo nelle ubicazioni della rete disponibili. Prima di creare una rete, utilizza la seguente API per controllare le ubicazioni della rete disponibili.

```
/network-locations/available
```
{:codeblock}


## Creazione di una rete

**Nota**: se utilizzi piano Starter, non hai bisogno di creare una rete con le API perché viene fornita una rete predefinita quando crei l'istanza del servizio blockchain in {{site.data.keyword.cloud_notm}}.

Se utilizzi piano Enterprise, devi completare due passi per creare una rete con le API.

1. Crea un'istanza del servizio blockchain in {{site.data.keyword.cloud_notm}} con piano Enterprise<!-- or Enterprise Plus Plan-->.  Richiama i tuoi token e ID istanza del servizio come password e nome utente di autenticazione di base. Per ulteriori informazioni, vedi [Richiamo delle credenziali di autenticazione di base dell'API](#retrieve-id-token).

2. Richiama l'API **Crea rete** utilizzando queste credenziali del servizio.

```
/networks
```
{:codeblock}

**Parametri**:
- `location_id`: l'ID di un'ubicazione della rete disponibile. Per ulteriori informazioni, vedi [Controllo delle ubicazioni della rete disponibili](check-location).
- `company_name`: il tuo identificativo come membro della rete.
- `email`: il tuo indirizzo email per ricevere le notifiche.
- `peers`: il numero di peer che vuoi creare per questo membro. I valori validi sono 0 - 6. Puoi inoltre creare i peer per il tuo membro successivamente nella IU monitoraggio della rete.
- `ledger_type`: il tipo di libro mastro di questa rete. I valori validi sono levelDB e couchDB. **levelDB** è il valore predefinito.


## Invito di nuovi membri in una rete.

Dopo aver creato una rete blockchain, puoi invitare altri membri ad unirsi alla tua rete. Devi specificare l'ID della rete a cui vuoi invitare i nuovi membri ad unirsi. Le credenziali di autenticazione di base necessarie per invitare un membro sono diverse da quelle utilizzate nell'API **Crea rete** API. <!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". --> Puoi ottenere le credenziali per invitare un membro con l'API **Richiama credenziali rete** nella [IU Swagger ](swagger_apis##retrieving-network-credentials) o [richiamare le informazioni di autenticazione di base per l'API](#retrieve-id-token) dalla tua istanza del servizio in {{site.data.keyword.cloud_notm}}.

```
/networks/{networkID}/invite
```
{:codeblock}

**Parametri**:
- `email`: l'indirizzo email del membro da invitare nella rete.
- `company_name`: l'identificativo che definisci per il membro per unirsi alla rete. I membri invitati possono modificare i propri identificativi quando si uniscono alla rete.

Il membro invitato riceverà un invito email con le istruzioni su come unirsi alla tua rete.


## Adesione a una rete

Se vieni invitato ad unirti a una rete blockchain, riceverai un invito email.  Puoi seguire le istruzioni nell'email o utilizzare la seguente API per unirti alla rete.

**Nota**: prima di unirti a una rete devi creare un'istanza del servizio {{site.data.keyword.blockchainfull_notm}} Platform e richiamare i tuoi token e ID istanza del servizio come password e nome utente di autenticazione di base. Per ulteriori informazioni, vedi [Richiamo delle informazioni di autenticazione di base per l'API](#retrieve-id-token). Devi specificare l'ID della rete a cui vuoi unirti. 

```
/networks/{networkID}/join
```
{:codeblock}

**Parametri**:
- `company_name`: il tuo identificativo come membro della rete. Questo sostituirà il nome assegnato dal mittente dell'invito.
- `email`: il tuo indirizzo email per ricevere le notifiche. Questo dovrebbe corrispondere all'indirizzo email nella notifica di invito.
- `peers`: il numero di peer che vuoi creare per questo membro. I valori validi sono da 0 a 6. Puoi inoltre creare i peer per il tuo membro successivamente nel monitoraggio della rete.


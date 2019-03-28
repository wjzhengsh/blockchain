---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Distribuzione della console {{site.data.keyword.blockchainfull_notm}} Platform su {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-icp}

Queste istruzioni descrivono come distribuire una console {{site.data.keyword.blockchainfull}} Platform nella tua infrastruttura tramite {{site.data.keyword.cloud_notm}} Private. Non è necessario distribuire la console nello stesso ambiente degli altri tuoi componenti blockchain.
{:shortdesc}

Utilizzerai un grafico Helm per distribuire la console {{site.data.keyword.blockchainfull_notm}} Platform sul tuo {{site.data.keyword.cloud_notm}} Private. Ciascuna delle tue distribuzioni installa una singola istanza della console sul tuo spazio dei nomi di {{site.data.keyword.cloud_notm}} Private.

## Considerazioni
{: #ibp-console-deploy-icp-considerations}

Prima di distribuire la console, assicurati di aver compreso le seguenti considerazioni:

- I componenti blockchain devono essere già stati distribuiti in {{site.data.keyword.cloud_notm}} Private o AWS (Amazon Web Services). Se non lo hai già fatto, vedi [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private] o Informazioni su [IBM Blockchain Platform for AWS] per ulteriori informazioni.
- Sei responsabile della gestione del monitoraggio dell'integrità, della sicurezza, della registrazione e dell'uso delle risorse della tua console.
- Puoi connettere la tua console solo ai componenti blockchain che si trovano a livello di Hyperledger Fabric v1.2.1.

## Risorse richieste
{: #ibp-console-deploy-icp-resources-required}

Assicurati che il sistema {{site.data.keyword.cloud_notm}} Private soddisfi i requisiti di risorse hardware minimi:

| Componente | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| Console IBP | 1 | 192 MB | 1 GB |
| CouchDB per la console IBP | 1 | 1 GB | 1 GB |

 **Note:**
 - Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti di CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in {{site.data.keyword.cloud_notm}} Private. VPC è un'unità di misura per determinare il costo di licenza dei prodotti IBM. Per ulteriori informazioni sugli scenari per decidere il VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un grande volume di transazioni, è importante assegnare una quantità sufficientemente grande di archiviazione; ad esempio 250 GB per la tua console{{site.data.keyword.blockchainfull_notm}} Platform. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se stai per esaurire l'archiviazione sul tuo peer o sul tuo ordinante, devi distribuire una nuova console con un file system più grande e importare di nuovo i componenti di rete di conseguenza.

## Archiviazione
{: #ibp-console-deploy-icp-resources-required-storage}

Devi determinare l'archiviazione che verrà utilizzata dalla tua console. Se utilizzi le impostazioni predefinite, il grafico Helm creerà una nuova attestazione di volume persistente (PVC) di 8 Gi con il nome di `console-pvc` per i dati della tua console e un'altra PVC di 8 Gi con il nome di `couchdb-pvc` per il tuo CouchDB.

Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* `storageClass` durante l'installazione di {{site.data.keyword.cloud_notm}} Private o che l'amministratore di sistema Kubernetes debba creare una nuova storageClass prima della tua distribuzione.

Puoi scegliere di distribuire la console sulle piattaforme AMD64 o S390X. Tuttavia, tieni presente che il [provisioning dinamico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) è disponibile solo per i nodi AMD64 in {{site.data.keyword.cloud_notm}} Private. Se il tuo cluster include una combinazione di nodi di lavoro S390X e AMD64, non è possibile utilizzare il provisioning dinamico.

Se non utilizzi il provisioning dinamico, devono essere creati e configurati dei [Volumi persistenti ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) con etichette che possono essere utilizzati per perfezionare il processo di bind della PVC Kubernetes.

## Prerequisiti
{: #ibp-console-icp-prereq}

Prima di distribuire la console {{site.data.keyword.blockchainfull_notm}} Platform su {{site.data.keyword.cloud_notm}} Private, assicurati di completare i passi nelle esercitazioni di [Configurazione di {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

Prima di distribuire la console {{site.data.keyword.blockchainfull_notm}} Platform, devi inoltre installare o configurare le seguenti dipendenze.

- [Registrazione al servizio IBM App ID](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Installa Docker](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### Registrazione al servizio App ID
{: #ibp-console-icp-prereq-app-id}

[App ID ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/docs/services/appid/index.html#gettingstarted "App ID") è un servizio di gestione utenti fornito da {{site.data.keyword.cloud_notm}}. La console {{site.data.keyword.blockchainfull_notm}} Platform utilizza il servizio App ID per gestire chi sarà in grado di utilizzare il dashboard e gestire la tua rete blockchain. Questo permette agli utenti dell'IU di utilizzare le credenziali emesse dalla tua propria organizzazione o da terze parti come Google o Facebook, senza che abbiano bisogno di avere l'ID IBM. Solo l'utente che distribuisce la console dovrà avere un ID {{site.data.keyword.IBM_notm}} per registrarsi al servizio.

Prima di distribuire la console, devi ottenere un ID IBM per eseguire un'unica registrazione alla directory cloud App ID. Quindi, devi recuperare le credenziali del servizio APP ID per passare alla console. Utilizza i seguenti passi per registrarti al servizio e ottenere le tue credenziali del servizio.

1. Se non ne hai già uno, devi creare un [ID {{site.data.keyword.IBM_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid).

2. Utilizza il tuo ID {{site.data.keyword.IBM_notm}} per accedere o registrarti a [{{site.data.keyword.cloud_notm}} ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/app-id "IBM Cloud App ID"). Quindi, vai al servizio [App ID ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/app-id "IBM Cloud App ID") nel catalogo. Seleziona il piano `Lite` o il `Livello progressivo` a seconda delle tue esigenze di servizio. Fai quindi clic sul pulsante **Crea** per avviare la schermata di "Benvenuto".

3. Quando sei nella schermata di "Benvenuto" fai clic sul link **Gestisci** nel riquadro di navigazione a sinistra per visualizzare o aggiornare i provider di identità preferiti. Questi possono essere provider di terze parti, come Google, o il servizio di identità della tua organizzazione.

4. Fai clic sulla scheda **Impostazioni** nella tabella per configurare un URL di reindirizzamento. Questo è l'URL da cui distribuirai e accederai alla console {{site.data.keyword.blockchainfull_notm}} Platform. Per utilizzare l'host locale, specifica `http://localhost/auth/cb` e fai clic sul segno `+`.

5. Aggiungi il tuo indirizzo email al servizio in modo da poter effettuare l'accesso quando configuri la console per la prima volta. Fai clic sul link **Utenti** nel pannello di navigazione a sinistra e quindi sul pulsante **Aggiungi utente**. Immetti le tue informazioni email e fai clic su **Salva**.

6. Fai clic sul link **Credenziali del servizio** nel pannello di navigazione a sinistra. Se è la prima volta che utilizzi il servizio, devi generare le credenziali facendo clic su **Nuova credenziale**. Fai clic su **Visualizza credenziali** per copiare le credenziali richieste per accedere al servizio.  Quando vengono visualizzati i dettagli per la creazione di nuove credenziali, seleziona l'opzione `reader` e un ID di servizio se vuoi assegnare all'IU un nome specifico. Le credenziali di App ID sono simili ai valori JSON come riportato di seguito:
  ![Credenziali del servizio](../images/service_credentials.gif "Credenziali del servizio")

7. Fai clic sull'icona Copia e salva le tue credenziali. Le utilizzerai durante la configurazione della console.

### Installazione di Docker
{: #ibp-console-icp-prereq-docker}

Installa [Docker ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.docker.com/get-started) versione 17.06.2-ce o superiore nell'ambiente in cui distribuirai la console {{site.data.keyword.blockchainfull_notm}} Platform. Puoi controllare la versione di Docker che hai installato con il seguente comando da una finestra di terminale:

```
docker --version
```
{:codeblock}

**Nota:** l'installazione di Docker per Mac, Windows o Docker Toolbox installerà anche Docker Compose. Se hai già installato Docker, verifica di aver installato Docker Compose versione 1.14.0 o successiva. Se non lo hai installato, ti consigliamo di installare una versione più recente di Docker. Puoi controllare la versione di Docker Compose che hai installato con il seguente comando da una finestra di terminale:

```
docker-compose --version
```
{:codeblock}


## Passo uno: scarica il pacchetto della console {{site.data.keyword.blockchainfull_notm}} Platform da {{site.data.keyword.IBM_notm}} Passport Advantage
{: #ibp-console-icp-download-icp}

Contatta il tuo rivenditore {{site.data.keyword.IBM_notm}} per scaricare il pacchetto della console {{site.data.keyword.blockchainfull_notm}} Platform da [Passport Advantage ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online for customers"). Non hai un rivenditore IBM? Visita questo <link> per registrarti e ottenerne uno...

## Passo due: importa il grafico Helm della console {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-icp-import-helmchart}

Per informazioni di riferimento, vedi [Installazione di {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

## Passo tre: distribuisci la console {{site.data.keyword.blockchainfull_notm}} Platform su {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-helmchart-icp}

Dopo aver importato il grafico Helm della console {{site.data.keyword.blockchainfull_notm}} Platform, puoi utilizzare i seguenti passi per configurare e installare la tua console.

1. Accedi alla console {{site.data.keyword.cloud_notm}} Private e fai clic su **Catalogo** nell'angolo superiore destro.
2. Fai clic su `Blockchain` nel pannello di navigazione a sinistra per individuare il tile etichettato `ibm-blockchain-platform-ui`. Fai clic sul tile per aprirlo e per visualizzare un file Readme che include informazioni sulla configurazione e installazione del grafico Helm.
3. Fai clic sulla scheda **Configurazione** all'inizio del pannello oppure fai clic sul pulsante **Configura** nell'angolo in basso a destra.
4. Completa le [impostazioni di configurazione](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters).
5. Fai clic su **Installa**.

### Parametri della console {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-icp-parameters}

La seguente tabella elenca i parametri configurabili della console {{site.data.keyword.blockchainfull_notm}} Platform e i relativi valori predefiniti.

|  Parametro     | Descrizione    | Valore predefinito  | Obbligatorio |
| --------------|-----------------|-------|------- |
| `Helm release name`| Il nome della tua release Helm. Deve iniziare con una lettera minuscola e terminare con un qualsiasi carattere alfanumerico, deve contenere solo trattini e caratteri alfanumerici minuscoli. Devi utilizzare un nome di release Helm univoco ogni volta che tenti di installare una console. | nessuno | sì |
| `Target namespace`| Scegli lo spazio dei nomi Kubernetes per installare il grafico Helm. | nessuno | sì |
| `Image repository`| Ubicazione del grafico Helm della console {{site.data.keyword.blockchainfull_notm}} Platform. | registry.ng.bluemix.net/op-tools/op-tools | sì |
| `Image tag`| Il valore della tag associata all'immagine della console {{site.data.keyword.blockchainfull_notm}} Platform. Questo campo viene automaticamente compilato con la versione dell'immagine. Non modificarlo. | v0.0.34 | sì |
| `Service type` | Utilizzato per specificare se le [porte esterne devono essere esposte ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sul peer. Seleziona NodePort per esporre le porte esternamente (consigliato) e ClusterIP per non esporle. LoadBalancer e ExternalName non sono supportati in questa release. | NodePort | sì |
| `Console ingress` | Specifica il nome host che vuoi utilizzare per accedere alla console {{site.data.keyword.blockchainfull_notm}} Platform. | nessuno | no |
| `Configtxlator ingress` | Specifica il nome host che vuoi utilizzare per accedere al servizio configtxlator. | nessuno | no |
| `Persistence enabled` | Se selezionato, i dati saranno disponibili al riavvio del contenitore. Altrimenti, tutti i dati andranno persi in caso di failover o di riavvio del pod. | verificato | no |
| `Use dynamic provisioning` | Seleziona per abilitare il provisioning dinamico dei tuoi volumi di archiviazione. | verificato | no |
| `Storage class name`| Specifica un nome della classe di archiviazione univoco. Altrimenti, viene utilizzata la classe di archiviazione predefinita nel cluster. | nessuno | Dipende dal modo in cui viene configurato il cluster {{site.data.keyword.cloud_notm}} Private. Verifica con il tuo amministratore del cluster. |
| `Existing volume claim`| Specifica il nome di un'attestazione di volume esistente e lascia vuoti tutti gli altri campi. | nessuno | no |
| `Selector label`| [Etichetta del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | nessuno | no |
| `Selector value`| [Valore del selettore ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etichette e selettori") per la tua PVC. | nessuno | no |
| `Storage access mode`| Specifica la [modalità di accesso](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Access Modes") dell'archiviazione per il PVC. | ReadWriteMany | sì |
| `Volume name`| Specifica il nome della PVC. | nessuno | sì |
| `CPU request` | Numero minimo di CPU da assegnare alla console. | 250m | sì |
| `CPU limit` | Numero massimo di CPU da assegnare alla console.| 500m | sì |
| `Memory request` | Quantità minima di memoria da assegnare alla console. | 256 Mi | sì |
| `Memory limit` | Quantità massima di memoria da assegnare alla console. | 1Gi | sì |

## Passo quattro: configura l'IP proxy
{: #ibp-console-icp-config-proxy-ip}

Al termine dell'installazione, fai clic sul pulsante **Visualizza release Helm** per aprire la pagina della release Helm della tua console. Puoi anche fare clic su **Menu** > **Carichi di lavoro** > **Release Helm** in {{site.data.keyword.cloud_notm}} Private e quindi fare clic sulla tua release Helm.

Nella pagina della release Helm della tua console, trova l'IP proxy del cluster {{site.data.keyword.cloud_notm}} Private, ad esempio `9.12.19.115`.

Nella macchina client da cui vuoi accedere alla console, aggiungi voci in `/etc/hosts` per associare i nomi host che hai immesso nel passo precedente per optools e i servizi configtxlator all'IP proxy. Ad esempio:
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## Passo cinque: configura la console {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-icp-setup}

*Questa sezione potrebbe beneficiare di un'animazione gif.*

1. In {{site.data.keyword.cloud_notm}}, vai a **Provider di identità** > **Gestisci** dal pannello di navigazione a sinistra e seleziona la scheda **Impostazioni di autenticazione**.
2. Nel campo **Aggiungi URL di reindirizzamento web**, specifica l'URL che vuoi utilizzare per accedere alla console con il tuo ID applicazione.
3. Nel tuo browser web, accedi alla console con il nome host che hai configurato in {{site.data.keyword.cloud_notm}} Private. Per il primo accesso, visualizzerai i pannelli di configurazione della console.
  1. Nella scheda **Autenticazione**, immetti il tuo ID applicazione.
  2. Nella scheda **Configurazione**, incolla le credenziali del servizio App ID che hai copiato nel [Passo uno: registrazione al servizio App ID](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id).
  3. Nella scheda **Aggiungi utenti**, specifica un elenco di indirizzi email di utenti `Admin` e `General` autorizzati sulla console. I domini email sono limitati alla serie di provider di identità, come ad esempio aziendale, Google o Facebook, selezionati quando il servizio App ID è stato registrato in {{site.data.keyword.cloud_notm}}.
    - Il ruolo `Admin` è richiesto per poter aggiungere nuovi utenti o rimuovere utenti esistenti dall'elenco di autorizzazioni della console.   **Suggerimento:** se sarai la persona che amministra la console, ricordati di includere il tuo indirizzo email nell'elenco.
    - Il ruolo `General` fornisce agli utenti l'accesso di **sola visualizzazione** ai componenti nella console.

    Infine, devi specificare una singola `Email di contatto dell'amministratore` che viene visualizzata come indirizzo email di contatto quando un utente non autorizzato, un utente che non si trova in uno degli elenchi precedenti, tenta di accedere alla console. Questo indirizzo email non deve essere un utente amministratore nella console. Nota che queste informazioni possono anche essere create o modificate successivamente nella scheda **Membri** della console.
  4. Fai clic su **Procedi all'accesso**. Tutti gli utenti specificati negli elenchi precedenti possono ora accedere alla console {{site.data.keyword.blockchainfull_notm}} Platform utilizzando i propri indirizzi email.

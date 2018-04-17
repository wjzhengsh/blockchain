---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Distribuzione di applicazioni di esempio
{: #overview}

{{site.data.keyword.blockchainfull}} Platform fornisce applicazioni di esempio che puoi distribuire e testare in modo da poter comprendere meglio le reti blockchain e lo sviluppo delle applicazioni.
{: shortdesc}

Dopo che hai eseguito il provisioning di una rete Starter Plan, puoi distribuire le applicazioni di esempio nel Network Monitor, che automatizza la procedura per abilitare l'esecuzione delle applicazioni di esempio sulla tua rete. Puoi anche abilitare gli esempi passo dopo passo per apprendere l'intero processo di distribuzione delle applicazioni, a cui ti devi attenere quando distribuisci le tue applicazioni.

## Distribuzione di applicazioni di esempio in Starter Plan

Starter Plan fornisce un semplice approccio alla distribuzione di applicazioni di esempio avvalendosi del servizio Toolchain su {{site.data.keyword.cloud_notm}} con solo pochi clic. Dopo che l'hai distribuita e avviata, l'applicazione di esempio viene eseguita sulla tua rete blockchain automaticamente.

Starter Plan fornisce due applicazioni di esempio con cui puoi iniziare.

* **Marbles**

  L'esempio Marbles consente agli utenti di creare delle biglie virtuali con proprietà differenti e di trasferirle ad altri utenti. Per ulteriori informazioni su Marbles, vedi [Marbles Demo ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM-Blockchain/marbles).

* **Vehicle Manufacture**

  L'esempio Vehicle Manufacture consente agli utenti di esplorare le varie fasi del ciclo di vita di un veicolo virtuale. Per ulteriori informazioni su questo esempio, vedi [Vehicle Manufacture ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM-Blockchain/vehicle-manufacture). 

Per distribuire un'applicazione di esempio, completa la seguente procedura:

1. Accedi al **Network Monitor** della tua rete Starter Plan. Se non hai una, vedi [Creazione di una rete](../get_start_starter_plan.html#creating-a-network).

2. Apri la schermata "Try samples" nel Network Monitor. Scegli l'applicazione di esempio che vuoi distribuire e fai clic sul pulsante **Deploy via Toolchain**.
<!--
    ![sampleappflow0](../images/sampleappflow0.png)
-->
3. Viene visualizzata una finestra di configurazione del servizio Toolchain. Assicurati che tutti gli strumenti richiesti siano integrati correttamente. Nota: se hai più organizzazioni, assicurati di immettere il nome organizzazione corretto. Il nome organizzazione dovrebbe essere l'indirizzo email che usi per registrarti per la rete.
    **Suggerimento**: devi disabilitare i blocchi pop-up per consentire l'apertura della pagina di configurazione del servizio Toolchain.
<!--
    ![sampleappflow1](../images/sampleappflow1.png)
-->
  Se questa è la prima applicazione che distribuisci tramite Toolchain, devi autorizzare Toolchain ad accedere al repository GitHub.

  ![sampleappflow2](../images/sampleappflow2.png)

  Dopo che hai fatto clic sul pulsante "Authorize", vieni indirizzato a GitHub. Se non hai un account GitHub, devi crearne uno. Concedi a Toolchain l'accesso ai tuoi repository immettendo le tue informazioni di account. Se non vuoi concedere tale accesso a Toolchain, puoi distribuire le applicazioni di esempio in modo manuale. Per ulteriori informazioni, vedi [Distribuzione di applicazioni di esempio in modo manuale](#deploy_sample_applications_manually).

5. Fai clic sul pulsante **Create** nella parte inferiore della pagina Toolchain. Questo ti riporterà al Network Monitor, dove dovrebbe essere in corso la distribuzione di Marbles. Questo processo dovrebbe impiegare tra i 5 e i 10 minuti.
<!--
    ![sampleappflow3](../images/sampleappflow3.png)
-->
Una volta completata la distribuzione, puoi iniziare a utilizzare l'esempio Marbles sulla tua rete Starter Plan.

Poiché questo processo crea un repository GitHub duplicato a cui hai accesso e su cui hai il controllo, puoi apportare delle modifiche a Marbles nel repository duplicato ed eseguirne il commit. Questi commit attiveranno una compilazione automatica della tua applicazione Marbles e ti consentiranno di eseguirla in {{site.data.keyword.cloud_notm}}.

## Distribuzione di applicazioni di esempio in modo manuale
{: #deploy_sample_applications_manually}

Se vuoi distribuire applicazioni di esempio senza utilizzare il Network Monitor, assicurati di installare tutti i prerequisiti software sul tuo file system locale. Per ulteriori informazioni, vedi [Configurazione dell'ambiente di sviluppo di applicazioni](../v10_application.html#setting-up-application-development-environment).

Ti serve anche una rete blockchain su {{site.data.keyword.cloud_notm}} con Starter Plan o Enterprise Plan e devi configurare un canale e i suoi peer. Per ulteriori informazioni, vedi [Governance della rete Starter Plan](../get_start_starter_plan.html) e [Governance della rete Enterprise Plan](../get_start.html). Dopo che hai eseguito il provisioning di una rete e puoi distribuire applicazioni su di essa, richiama gli endpoint API delle tue risorse di rete a cui accederà la tua applicazione. Per ulteriori informazioni, vedi [Aggiunta di endpoint API di rete alla tua applicazione](../v10_application.html#adding-network-api-endpoints-to-your-application).

Puoi distribuire una delle seguenti applicazioni di esempio sulla tua rete:

- **Marbles**

  Nell'applicazione Marbles, più utenti possono creare delle biglie con proprietà differenti e trasferirle ad altri. L'applicazione Marbles è scritta in JavaScript e il chaincode è scritto in Go.

  Puoi trovare il codice di esempio e le istruzioni in [Marbles in GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM-Blockchain/marbles).

  Utilizza le istruzioni Bluemix invece delle istruzioni per ospitare Marbles in loco e immetti le informazioni pertinenti dalla tua rete. Nota: le acquisizioni di schermo nel Marbles GitHub mostrano l'IU Enterprise Plan (poiché Enterprise Plan supporta solo il percorso manuale per la distribuzione di Marbles), che è leggermente differente dall'IU Starter Plan. Ciò nonostante, entrambe le IU hanno le stesse parti di base e puoi trovare i nomi dei tuoi peer, dei tuoi canali e altre informazioni sulle credenziali di servizio nelle schermate appropriate.

- **Fabcar**

  In Fabcar, puoi eseguire **query** e **aggiornamenti del libro mastro** sui record relativi alle automobili nel libro mastro. Fabcar è scritta in JavaScript e il chaincode è scritto in Go.

  Puoi trovare il codice di esempio in [Fabric car in GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) e le istruzioni in [Writing Your First Application ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

- **Altre applicazioni**

  Per ulteriori informazioni su come ospitare le tue applicazioni in {{site.data.keyword.cloud_notm}}, vedi [Host delle applicazioni](../v10_application.html#hosting-applications).

## Eliminazione di un'applicazione di esempio

Per eliminare un'applicazione di esempio che era stata acquisita tramite il processo Toolchain, accedi al punto nell'IU dove si trova l'esempio. Poiché le applicazioni di esempio sono istanziate su un canale, puoi trovare l'esempio nei canali. Fai clic su **Channels** nella navigazione a sinistra per aprire la schermata "Channels". Fai clic sul canale pertinente su cui è istanziato l'esempio e fai quindi clic su **Chaincode**. Vengono visualizzati i chaincode istanziati su tale canale.

Se fai clic sul chaincode della tua applicazione, puoi vedere una scheda **Delete**. Tuttavia, limitarsi a fare clic su **Delete** non elimina l'applicazione di esempio ma solo il contenitore del chaincode. Devi anche andare al dashboard {{site.data.keyword.cloud_notm}} e al dashboard Toolchain per eliminare l'esempio in essi.

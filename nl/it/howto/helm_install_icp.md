---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# Installazione di {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private viene fornito come un file di grafico Helm che può essere installato su un cluster {{site.data.keyword.cloud_notm}} Private locale. Dopo aver installato il grafico Helm, puoi trovare {{site.data.keyword.blockchainfull_notm}} Platform come applicazione nel catalogo di {{site.data.keyword.cloud_notm}} Private.

Prima di installare {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, esamina le [Considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations). Per ulteriori informazioni su come distribuire i componenti blockchain inclusi nel grafico Helm, i prezzi e il supporto, consulta [Informazioni su {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private offre due edizioni:

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private viene fornito tramite Passport Advantage (PPA). Devi disporre della licenza necessaria per accedere a [Passport Advantage Online ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Quando effettui l'acquisto, il supporto tecnico per {{site.data.keyword.blockchainfull_notm}} Platform è incluso.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition è un'offerta gratuita per l'esplorazione, lo sviluppo e il test. È possibile accedere a questa versione gratuita tramite [GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts"). Nota che {{site.data.keyword.IBM_notm}} non fornisce supporto per la Community Edition.

## Prerequisiti per l'installazione del grafico Helm
{: #helm-install-prereqs}

Prima di installare il grafico Helm, devi aver configurato un cluster {{site.data.keyword.cloud_notm}} Private e creato un nuovo spazio dei nomi di destinazione associato a una politica di sicurezza del pod. Rivedi le istruzioni per [impostare e configurare un cluster {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

## Installazione di {{site.data.keyword.blockchainfull_notm}} Platform dietro un firewall
{: #helm-install-prereqs-firewall}

Puoi distribuire i componenti di {{site.data.keyword.blockchainfull_notm}} Platform dietro un firewall senza connettività Internet. Il pacchetto PPA include tutte le immagini Docker del componente Fabric che sarà utilizzato da {{site.data.keyword.blockchainfull_notm}} Platform e non devi scaricarle da DockerHub durante la distribuzione.

Tuttavia, il grafico Helm di Community Edition non include le immagini Docker del componente Fabric necessarie perché questa edizione è configurata per scaricarle da DockerHub durante la distribuzione. La distribuzione avrà esito negativo se non è disponibile alcuna connettività Internet. Pertanto, devi completare alcuni passi aggiuntivi per creare degli archivi su una macchina connessa a Internet prima di poter installare gli archivi sul tuo cluster {{site.data.keyword.cloud_notm}} Private. Le seguenti immagini sono obbligatorie:
- [Fabric peer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Fabric peer")
- [Fabric CA ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric orderer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric Couch database ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init` immagine utilizzata per eseguire il bootstrap e configurare i componenti, incluse le cartelle MSP ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Immagine Docker-in-Docker (DinD) utilizzata dai peer per eseguire i contenitori chaincode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Per ulteriori informazioni su come utilizzare queste immagini, consulta [Adding featured applications to clusters without Internet connectivity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html). Tieni presente che puoi trovare il file della specifica `manifest.yaml` nella directory `ibm-blockchain-platform-dev/ibm_cloud_pak` nel grafico Helm.

## Importazione del grafico Helm in {{site.data.keyword.cloud_notm}} Private
{: #helm-install-importing}

1. Scarica il file del grafico Helm di IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private da [Passport Advantage Online ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") o per la Community Edition gratuita da [GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts").  Questo pacchetto del grafico Helm contiene tre sottografici Helm per la CA, l'ordinante e il peer.

2. Se non l'hai già fatto, accedi al tuo cluster {{site.data.keyword.cloud_notm}} Private.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. Assicurati che la [CLI Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html) sia configurata. Dopo aver configurato la CLI Docker, accedi al registro delle immagini sul tuo cluster utilizzando il seguente comando:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. Trova il nome del repository in {{site.data.keyword.cloud_notm}} Private per caricare il tuo grafico Helm utilizzando il seguente comando:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Se questo comando viene completato correttamente, puoi visualizzare un elenco di repository nel tuo cluster. Scegli il nome del repository di destinazione e salvalo. Dovrai utilizzarlo nel comando riportato di seguito.

5. Importa il grafico Helm utilizzando la riga di comando.
  Il comando che devi eseguire per importare il grafico Helm dipende dal fatto che il grafico Helm sia stato scaricato da Passport Advantage (PPA) o da GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Attieniti a queste istruzioni se hai scaricato il grafico Helm da PPA.
    Dalla directory in cui hai memorizzato il pacchetto del grafico Helm scaricato da PPA, immetti il seguente comando nella CLI {{site.data.keyword.cloud_notm}} Private per importare il grafico Helm nel tuo cluster {{site.data.keyword.cloud_notm}} Private.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Sostituisci i seguenti valori:
    - `<archive-name>` con il nome del file `.tgz` scaricato.
    - `<cluster_CA_domain>:8500` con il dominio che utilizzi per eseguire l'accesso al tuo cluster {{site.data.keyword.cloud_notm}} Private.
    - `<repo-name>` con il repository Helm in cui vuoi caricare il grafico. Esegui 'cloudctl catalog repos' per elencare i tuoi repository.

    Se questo comando viene completato correttamente, puoi visualizzare qualcosa di simile alle seguenti informazioni:

    ```  
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
      Processing image: ibmcom/ibp-couchdb-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
      Processing image: ibmcom/ibp-couchdb-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
      Processing image: ibmcom/ibp-dind-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
      Processing image: ibmcom/ibp-dind-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-1.0.2.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
    Synch started
    OK
    ```  
    </details>

  - **Community edition scaricata da GitHub**
    Attieniti a queste istruzioni se hai scaricato il grafico Helm da GitHub.

    Dalla directory in cui hai memorizzato il pacchetto del grafico Helm scaricato da GitHub, immetti il seguente comando nella CLI {{site.data.keyword.cloud_notm}} Private per importare il grafico Helm nel tuo cluster {{site.data.keyword.cloud_notm}} Private. Per importare il grafico Helm scaricato da GitHub, immetti il seguente comando:
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Sostituisci i seguenti valori:
    - `<helm_chart_from_github>` con il nome del file .tgz che hai scaricato.
    - `<repo-name>` con il repository Helm in cui vuoi caricare il grafico. Esegui 'cloudctl catalog repos' per elencare i tuoi repository.

    Se questo comando viene completato correttamente, puoi visualizzare qualcosa di simile alle seguenti informazioni:
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

Fai clic sul pulsante **Catalogo** nella console {{site.data.keyword.cloud_notm}} Private e quindi su **Blockchain** nel pannello di navigazione di sinistra per verificare che l'importazione sia stata eseguita correttamente. Se è così, il tile **ibm-blockchain-platform-prod** o **ibm-blockchain-platform-dev** dovrebbe essere visibile nella pagina del catalogo {{site.data.keyword.cloud_notm}} Private.

## Distribuzione di componenti individuali
{: #helm-install-deploying-components}

Dopo aver installato il grafico Helm, fai clic sul tile **ibm-blockchain-platform-prod** o **ibm-blockchain-platform-dev** nel tuo catalogo {{site.data.keyword.cloud_notm}} Private per aprirlo. Puoi utilizzare la pagina di configurazione per distribuire tutti i componenti individuali della tua rete blockchain. Per ulteriori dettagli sui componenti necessari per la tua soluzione blockchain e l'ordine in cui devono essere distribuiti, vedi [Introduzione a {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp).

Successivamente, distribuisci i componenti individuali:

- Se stai distribuendo un ordinante, devi prima configurare una CA (Certificate Authority) per l'ordinante. La CA genererà dei certificati che saranno utilizzati dagli altri componenti nella tua organizzazione. Per ulteriori informazioni, vedi [Distribuzione di una CA (Certificate Authority) {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Successivamente, puoi distribuire l'ordinante che sarà il bind comune della rete. Per ulteriori informazioni, vedi [Distribuzione di un ordinante {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)

- Se stai distribuendo un peer, devi prima configurare una CA (Certificate Authority) per il peer. La CA genererà dei certificati che saranno utilizzati dal peer. Per ulteriori informazioni, vedi [Distribuzione di una CA (Certificate Authority) {{site.data.keyword.blockchainfull_notm}} Platform in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Successivamente, quando sei pronto ad unirti a una rete, puoi distribuire i peer che aderiranno al canale, approveranno le transazioni e archivieranno i tuoi dati. Per ulteriori informazioni, vedi [Distribuzione di un peer {{site.data.keyword.blockchainfull_notm}} in {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) o [Distribuzione di un peer {{site.data.keyword.blockchainfull_notm}} per una rete del piano Starter o Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy), a seconda della rete blockchain a cui si unirà il peer.

## Upgrade del grafico Helm su {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading}

Se utilizzi {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.1, puoi eseguire l'upgrade del tuo grafico Helm a {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 attenendoti alle istruzioni disponibili nel documento relativo all'[Upgrade dei prodotti in bundle ![Icona link esterno](../images/external_link.svg "icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "Upgrade dei prodotti in bundle") nella documentazione di {{site.data.keyword.cloud_notm}} Private. Oltre al comando `helm upgrade` nelle istruzioni, puoi anche [utilizzare la console {{site.data.keyword.cloud_notm}} Private per eseguire l'upgrade della release helm](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui).

Devi disporre del ruolo **amministratore del cluster** o **amministratore del team** su {{site.data.keyword.cloud_notm}} Private per eseguire l'upgrade di un grafico Helm.
{:note}

Il rollback da {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 alla v1.0.1 non è supportato.

### Upgrade della release di helm dalla console {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading-ui}

Quando esegui l'upgrade della release helm di un componente dalla console {{site.data.keyword.cloud_notm}} Private, devi selezionare `Reuse values` e non modificare alcun parametro.
{:important}  

Completa la seguente procedura per eseguire l'upgrade della release helm dalla console {{site.data.keyword.cloud_notm}} Private.
1. Dalla barra dei menu, fai clic su **Carichi di lavoro** > **Release Helm**.
2. Seleziona la release helm di cui desideri eseguire l'upgrade.
3. Controlla il numero versione sotto `Available Version` nella sezione **Dettagli e upgrade**.
4. Fai clic su **ReadMe** sotto il numero versione per esaminare le note sulla release per le eventuali modifiche rilevanti.
5. Fai clic su **Esegui upgrade**, quindi seleziona il repository e la versione corretti dall'elenco a discesa.
6. Assicurati che `Reuse values` sia selezionata.
7. Fai clic su **Esegui upgrade**.

Attendi il messaggio di conferma e l'aggiornamento sullo stato della release accanto al nome della release nell'angolo superiore sinistro della console.

### Completamento dei passi aggiuntivi per eseguire l'upgrade di un peer
{: #helm-install-upgrading-peer}

Dopo che hai eseguito l'upgrade di un peer, ci sono alcuni passi aggiuntivi che devi eseguire per completare il processo di upgrade. Nella pagina della release helm del peer, puoi vedere due pod del peer nella sezione **Pod**:
- Il nuovo pod del peer con lo stato di `CrashLoopBackOff`
- Il pod del peer originale con lo stato di `Running`

Annota i nomi peer associati; devi utilizzarli nei passi riportati di seguito.
{:tip}

Completa la seguente procedura dalla CLI per completare il processo di upgrade del peer:
1. Esegui l'accesso al tuo cluster dalla CLI {{site.data.keyword.cloud_notm}} Private ed esegui il comando `kubectl get replicaset`.
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. Individua il replicaset del peer che corrisponde al peer originale. Il nome del replicaset del peer deve corrispondere alla parte iniziale del nome nella sezione **Pod** nella console {{site.data.keyword.cloud_notm}} Private.
3. Elimina il replicaset peer.
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. Elimina il nuovo pod del peer. Questo è il pod del peer con lo stato di `CrashLoopBackOff` nell'IU.
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

L'upgrade del tuo peer è stato eseguito correttamente quando vedi che è stato creato un nuovo pod del peer con lo stato di `Running`.

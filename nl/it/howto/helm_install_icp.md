---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Installazione di {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private viene fornito come un file di grafico Helm che può essere installato su un cluster ICP locale. Dopo che hai installato il grafico Helm, puoi trovare {{site.data.keyword.blockchainfull_notm}} Platform come un'applicazione nel catalogo ICP. 

Prima di installare {{site.data.keyword.blockchainfull_notm}} Platform for ICP, rivedi [Considerazioni e limitazioni](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations). Per ulteriori informazioni su come distribuire i componenti blockchain inclusi nel grafico Helm, i prezzi e il supporto, consulta [About {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp-for-icp-about.html).

{{site.data.keyword.blockchainfull_notm}} Platform for ICP offre due edizioni:

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private viene fornito tramite Passport Advantage (PPA). Devi disporre della licenza necessaria per accedere a [Passport Advantage Online ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Quando effettui l'acquisto, il supporto tecnico per {{site.data.keyword.blockchainfull_notm}} Platform è incluso.

- {{site.data.keyword.blockchainfull_notm}} Platform Community Edition è un'offerta gratuita per l'esplorazione, lo sviluppo e il test. È possibile accedere a questa versione gratuita tramite [GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts"). Nota che {{site.data.keyword.IBM_notm}} non fornisce supporto per la Community Edition.

## Prerequisiti per l'installazione del grafico Helm 
{: #helm-install-prereqs}

Prima di installare il grafico Helm, devi aver configurato un cluster ICP. Rivedi le istruzioni per [impostare e configurare un cluster ICP](/docs/services/blockchain/ICP_setup.html).

## Installazione di {{site.data.keyword.blockchainfull_notm}} Platform dietro un firewall
{: #helm-install-prereqs-firewall}

Puoi distribuire i componenti di {{site.data.keyword.blockchainfull_notm}} Platform dietro un firewall senza connettività Internet. Il pacchetto PPA include tutte le immagini Docker del componente Fabric che sarà utilizzato da {{site.data.keyword.blockchainfull_notm}} Platform e non devi scaricarle da DockerHub durante la distribuzione.

Tuttavia, il grafico Helm di Community Edition non include le immagini Docker del componente Fabric necessarie perché questa edizione è configurata per scaricarle da DockerHub durante la distribuzione. La distribuzione avrà esito negativo se non è disponibile alcuna connettività Internet. Pertanto, devi completare alcuni passi ulteriori per creare degli archivi su una macchina collegata a Internet prima di poter installare gli archivi sul tuo cluster ICP. Le seguenti immagini sono obbligatorie:
- [Fabric peer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Fabric peer")
- [Fabric CA ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [Fabric orderer ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Fabric orderer")
- [Fabric Couch database ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Fabric couch database")
- [`Init` immagine utilizzata per eseguire il bootstrap e configurare i componenti, incluse le cartelle MSP ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Immagine Docker-in-Docker (DinD) utilizzata dai peer per eseguire i contenitori chaincode ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Per ulteriori informazioni su come utilizzare queste immagini, consulta [Adding featured applications to clusters without Internet connectivity ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html). Tieni presente che puoi trovare il file della specifica `manifest.yaml` nella directory `ibm-blockchain-platform-dev/ibm_cloud_pak` nel grafico Helm.

## Importazione del grafico Helm in ICP

1. Scarica il file del grafico Helm di IBM Blockchain Platform for ICP da [Passport Advantage Online ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") o per la Community edition gratuita da [GitHub ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts").  Questo pacchetto del grafico Helm contiene tre sottografici Helm per la CA, l'ordinante e il peer.

2. Se non l'hai già fatto, accedi al tuo cluster ICP.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. Assicurati che la [CLI Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html) sia configurata. Dopo aver configurato la CLI Docker, accedi al registro delle immagini sul tuo cluster utilizzando il seguente comando:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. Trova il nome del repository in ICP per caricare il tuo grafico Helm utilizzando il seguente comando:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Se questo comando viene completato correttamente, puoi visualizzare un elenco di repository nel tuo cluster. Scegli il nome del repository di destinazione e salvalo. Dovrai utilizzarlo nel comando riportato di seguito.

6. Importa il grafico Helm utilizzando la riga di comando.
  Il comando che devi eseguire per importare il grafico Helm dipende dal fatto che il grafico Helm sia stato scaricato da Passport Advantage (PPA) o da GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Attieniti a queste istruzioni se hai scaricato il grafico Helm da PPA.
    Dalla directory in cui hai memorizzato il pacchetto del grafico Helm scaricato da PPA, immetti il seguente comando nella CLI ICP per importare il grafico Helm nel tuo cluster ICP. 

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Sostituisci i seguenti valori:
    - `<archive-name>` con il nome del file `.tgz` scaricato.
    - `<cluster_CA_domain>:8500` con il dominio che utilizzi per accedere al tuo cluster ICP.
    - `<repo-name>` con il repository Helm in cui vuoi caricare il grafico. Esegui 'cloudctl catalog repos' per elencare i tuoi repository.

    Se questo comando viene completato correttamente, puoi visualizzare qualcosa di simile alle seguenti informazioni:
    ```
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      Processing image: ibmcom/icp-dind-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      Processing image: ibmcom/icp-dind-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-0.1.5.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```

  - **Community edition scaricata da GitHub**
    Attieniti a queste istruzioni se hai scaricato il grafico Helm da GitHub.

    Dalla directory in cui hai memorizzato il pacchetto del grafico Helm scaricato da GitHub, immetti il seguente comando nella CLI ICP per importare il grafico Helm nel tuo cluster ICP. Per importare il grafico Helm scaricato da GitHub, immetti il seguente comando:
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

Fai clic sul pulsante **Catalogo** nella console ICP e quindi su **Blockchain** nel pannello di navigazione di sinistra per verificare che l'importazione sia stata eseguita correttamente. Se è così, il tile **ibm-blockchain-platform-prod** o **ibm-blockchain-platform-dev** dovrebbe essere visibile nella pagina del catalogo ICP.


## Requisiti PodSecurityPolicy

Dopo aver importato il grafico Helm in {{site.data.keyword.cloud_notm}} Private, devi associarlo una delle [PodSecurityPolicy ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Pod Security Policies") allo spazio dei nomi di destinazione prima di installare i componenti. Scegli una PodSecurityPolicy predefinita o chiedi al tuo amministratore del cluster di crearti una PodSecurityPolicy personalizzata:
- Nome PodSecurityPolicy predefinita: [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- Definizione della PodSecurityPolicy personalizzata:
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- Il ClusterRole personalizzato per la PodSecurityPolicy personalizzata:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- Il ClusterRoleBinding per il ClusterRole personalizzato:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## Distribuzione di componenti individuali

Dopo aver installato il grafico Helm, fai clic sul tile **ibm-blockchain-platform-prod** o **ibm-blockchain-platform-dev** nel tuo catalogo ICP per aprirlo. Puoi utilizzare la pagina di configurazione per distribuire tutti i componenti individuali della tua rete blockchain. Per ulteriori dettagli sui componenti necessari per la tua soluzione blockchain e l'ordine in cui devono essere distribuiti, vedi [Guida alla distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp_for_icp_deployment_guide.html).

Successivamente, distribuisci i componenti individuali:

- Se stai distribuendo un ordinante, devi prima configurare una CA (Certificate Authority) per l'ordinante. La CA genererà dei certificati che saranno utilizzati dagli altri componenti nella tua organizzazione. Per ulteriori informazioni, vedi [Distribuzione di una CA (Certificate Authority) {{site.data.keyword.blockchainfull_notm}} Platform in ICP](/docs/services/blockchain/howto/CA_deploy_icp.html). Successivamente, puoi distribuire l'ordinante che sarà il bind comune della rete. Per ulteriori informazioni, vedi [Distribuzione di un ordinante {{site.data.keyword.blockchainfull_notm}} Platform in ICP](/docs/services/blockchain/howto/orderer_deploy_icp.html)

- Se stai distribuendo un peer, devi prima configurare una CA (Certificate Authority) per il peer. La CA genererà dei certificati che saranno utilizzati dal peer. Per ulteriori informazioni, vedi [Distribuzione di una CA (Certificate Authority) {{site.data.keyword.blockchainfull_notm}} Platform in ICP](/docs/services/blockchain/howto/CA_deploy_icp.html). Successivamente, quando sei pronto ad unirti a una rete, puoi distribuire i peer che aderiranno al canale, approveranno le transazioni e archivieranno i tuoi dati. Per ulteriori informazioni, consulta [Distribuzione di un peer {{site.data.keyword.blockchainfull_notm}} in ICP](/docs/services/blockchain/howto/peer_deploy_icp.html) o [Distribuzione di un peer {{site.data.keyword.blockchainfull_notm}} per una rete del piano Starter o Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html), a seconda della rete blockchain a cui si unirà il peer.

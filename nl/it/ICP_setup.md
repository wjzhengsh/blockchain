---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: IBM Cloud Private, data storage CA, cluster ICP, configuration

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

# Configurazione di {{site.data.keyword.cloud_notm}} Private
{: #icp-setup}

Prima di distribuire i componenti {{site.data.keyword.blockchainfull}} Platform e di creare la rete blockchain su {{site.data.keyword.cloud_notm}} Private, devi configurare {{site.data.keyword.cloud_notm}} Private nel tuo ambiente.
{:shortdesc}

## Prerequisiti
{: #icp-setup-prerequisites}

Completa i seguenti prerequisiti e prepara il tuo ambiente per installare {{site.data.keyword.cloud_notm}} Private.

### Docker
{{site.data.keyword.cloud_notm}} Private richiede che sia installato Docker. Attieniti alle istruzioni correlate in [Installing {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/install.html "Installing {{site.data.keyword.cloud_notm}} Private") per installare Docker.

### Impostazioni di {{site.data.keyword.cloud_notm}} Private
Prima di installare {{site.data.keyword.cloud_notm}} Private, i seguenti suggerimenti sono utili per preparare i tuoi nodi per l'installazione di {{site.data.keyword.cloud_notm}} Private. Ulteriori prerequisiti di {{site.data.keyword.cloud_notm}} Private possono essere trovati nella [documentazione {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/prep.html "Preparing your cluster for installation").

#### Aggiorna l'impostazione `vm.max_map_count`
{{site.data.keyword.cloud_notm}} Private utilizza Elastic Search per la registrazione e la misurazione. Per evitare eccezioni di memoria esaurita, Elastic Search richiede che sia configurata la proprietà di sistema `vm.max_map_count`. Prima di installare {{site.data.keyword.cloud_notm}} Private, vedi le [Istruzioni di configurazione di Elastic Search ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Memoria virtuale") per configurare questa proprietà su ciascun nodo. Puoi utilizzare i seguenti comandi per impostare questa proprietà permanentemente:

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Configura il file `/etc/hosts` su ciascun nodo nel tuo cluster

- {{site.data.keyword.cloud_notm}} Private utilizza [Kubernetes ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Informazioni sui principi di base di Kubernetes") per gestire le applicazioni inserite nel contenitore. Il DNS (Domain Name Server) Kubernetes non riesce se i nomi host non sono configurati nel file `/etc/hosts` su ciascun nodo. [Inserisci indirizzo IP, nome host e nome breve di ciascun nodo nel tuo cluster ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/prep_cluster.html "Configurazione del tuo cluster") nel file `/etc/hosts` su ciascun nodo.

- [IPv6 non è supportato da {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/getting_started/known_issues.html#ipv6 "IPv6 non è supportato"). Per evitare problemi con il servizio DNS in un cluster {{site.data.keyword.cloud_notm}} Private, disabilita le impostazioni IPv6 nel file `/etc/hosts` su ciascun nodo indicando come commento la seguente riga con un carattere `#` all'inizio della riga:
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Risorse richieste
{: #icp-setup-resources}

Assicurati che il tuo sistema {{site.data.keyword.cloud_notm}} Private soddisfi i requisiti di risorse hardware minimi per ogni componente di runtime Fabric.

| Componente | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |
| Ordinante | 2 | 512 MB | 100 GB con capacità di espansione |
| Peer | 2 | 2 GB | 50 GB con capacità di espansione. |
| CouchDB for Peer<br>(Applicabile solo se utilizzi CouchDB) | 2| 2 GB | 50 GB con capacità di espansione. |

 **Note:**
 - Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti di CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in {{site.data.keyword.cloud_notm}} Private. VPC è un'unità di misura per determinare il costo di licenza dei prodotti {{site.data.keyword.IBM_notm}}. Per ulteriori informazioni sugli scenari per decidere il VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un elevato volume di transazioni, è importante allocare una quantità sufficientemente grande di archiviazione, ad esempio 250 GB per il tuo peer e 500 GB per il tuo servizio ordini. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se stai per esaurire l'archiviazione sul tuo peer od ordinante, devi distribuire un nuovo peer od ordinante con un file system più grande e lasciare che esegua la sincronizzazione tramite i tuoi altri componenti sugli stessi canali.

#### Considerazioni sull'archiviazione
{: #icp-setup-storage-considerations}

* Devi determinare l'archiviazione che verrà utilizzata dai tuoi componenti. Se utilizzi le impostazioni predefinite, il grafico Helm del peer crea una nuova attestazione di volume persistente (o PVC, Persistent Volume Claim) con il nome di `my-data-pvc` per i dati del peer. Se selezioni CouchDB come tuo database libro mastro, il grafico Helm crea un'altra attestazione di volume persistente (o PVC, Persistent Volume Claim) con il nome `statedb-pvc` per il tuo database libro mastro.
* Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* storageClass durante l'installazione di {{site.data.keyword.cloud_notm}} Private o che l'amministratore di sistema Kubernetes crei una nuova storageClass prima della tua distribuzione.
* [Il provisioning dinamico ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Provisioning del volume dinamico") è disponibile solo per i nodi amd64 in {{site.data.keyword.cloud_notm}} Private. Pertanto, se il tuo cluster include una combinazione di nodi di lavoro s390x e amd64, non è possibile utilizzare il provisioning dinamico.
* Se non viene utilizzato il provisioning dinamico, i [volumi persistenti ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumi persistenti") devono essere creati e configurati con etichette che possono essere utilizzate per perfezionare il processo di bind dell'attestazione di volume persistente (o PVC, Persistent Volume Claim) di Kubernetes.
* Se utilizzi i volumi persistenti NFS v2/v3 , devi abilitare il modulo **NFS status monitor for NFSv2/v3 Filesystem Locks**, noto anche come **rpc-statd**, sul sistema host dove esiste il file system NFS. Questo modulo consente al tuo file system NFS di controllare l'eventuale presenza di blocchi esclusivi sui file detenuti da altri processi. Per abilitare questo modulo, esegui questi comandi:

  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## Installazione di {{site.data.keyword.cloud_notm}} Private
{: #icp-setup-install}

Completa la seguente procedura per installare e configurare {{site.data.keyword.cloud_notm}} Private nel tuo ambiente.

1. Installa un cluster [{{site.data.keyword.cloud_notm}} Private ![Icona link esterno](images/external_link.svg "Icona link esterno") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/kc_welcome_containers.html) alla versione 3.1.2. Se vuoi utilizzare il grafico Helm per sviluppo, test o sperimentazione, puoi installare [{{site.data.keyword.cloud_notm}} Private Community Edition versione 3.1.2 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE versione 3.1.2") gratuitamente.

2. Installa la CLI di {{site.data.keyword.cloud_notm}} Private [3.1.2 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/manage_cluster/install_cli.html) per installare e gestire la CA.

3. Configura la politica di sicurezza del pod per lo spazio dei nomi di destinazione. Le istruzioni sono fornite nella [prossima sezione](#icp-setup-psp).

Dopo che hai installato {{site.data.keyword.cloud_notm}} Private e associato una politica di sicurezza del pod a uno spazio dei nomi di destinazione, puoi continuare a [importare il grafico Helm {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) nel tuo cluster {{site.data.keyword.cloud_notm}} Private.

## Requisiti PodSecurityPolicy
{: #icp-setup-psp}

Prima di poter distribuire dei componenti utilizzando il grafico Helm, devi creare un nuovo spazio dei nomi di destinazione e associare ad esso una [politica di sicurezza del pod![Iconal link esterno](images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Politiche di sicurezza del pod").  Scegli una PodSecurityPolicy predefinita o chiedi al tuo amministratore del cluster di crearti una PodSecurityPolicy personalizzata:
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

---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Configurazione di ICP (IBM Cloud Private)


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Prima di distribuire i componenti {{site.data.keyword.blockchainfull}} Platform e creare una rete blockchain su ICP ({{site.data.keyword.cloud_notm}} Private), devi configurare ICP nel tuo ambiente.
{:shortdesc}

## Prerequisiti
{: #icp-setup-prerequisites}
Completa i seguenti prerequisiti e prepara il tuo ambiente per installare ICP.

### Docker
{{site.data.keyword.cloud_notm}} Private richiede che sia installato Docker. Attieniti alle istruzioni correlate in [Installing {{site.data.keyword.cloud_notm}} Private ![Icona link esterno](/images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html "Installing {{site.data.keyword.cloud_notm}} Private") per installare Docker.

### Impostazioni ICP
Prima di installare ICP, i seguenti suggerimenti sono utili per preparare i tuoi nodi per l'installazione ICP. Ulteriori prerequisiti di ICP sono disponibili nella [documentazione di ICP ![Icona link esterno](/images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "Preparing your cluster for installation").

#### Aggiorna l'impostazione `vm.max_map_count`
ICP utilizza Elastic Search per la registrazione e la misurazione. Per evitare eccezioni di memoria esaurita, Elastic Search richiede che sia configurata la proprietà di sistema `vm.max_map_count`. Prima di installare ICP, vedi le [Istruzioni di configurazione di Elastic Search ![Icona link esterno](/images/external_link.svg "Icona link esterno")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Memoria virtuale") per configurare questa proprietà su ciascun nodo. Puoi utilizzare i seguenti comandi per impostare questa proprietà permanentemente:

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Configura il file `/etc/hosts` su ciascun nodo nel tuo cluster

- ICP utilizza [Kubernetes ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Informazioni sui principi di base di Kubernetes") per gestire le applicazioni inserite nel contenitore. Il DNS (Domain Name Server) Kubernetes non riesce se i nomi host non sono configurati nel file `/etc/hosts` su ciascun nodo. [Inserisci indirizzo IP, nome host e nome breve di ciascun nodo nel tuo cluster ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "Configurazione del tuo cluster") nel file `/etc/hosts` su ciascun nodo.

- [IPv6 non è supportato da ICP ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "IPv6 non è supportato"). Per evitare problemi con il servizio DNS in un cluster ICP, disabilita le impostazioni IPv6 nel file `/etc/hosts` su ciascun nodo indicando come commento la seguente riga con un carattere `#` all'inizio della riga:
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Risorse richieste
{: #helm-icp-resources}

Assicurati che il sistema ICP soddisfi i requisiti di risorse hardware minimi:

| Componente  | CPU virtuale | RAM | Disco per l'archiviazione di dati |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |
| Ordinante | 2 | 512 MB | 100 GB con capacità di espansione. |
| Peer | 2 | 2 GB | 50 GB con capacità di espansione. |
| CouchDB per ogni peer | 2| 2 GB | 50 GB con capacità di espansione. |

 **Note:**
 - Una CPU virtuale è un core virtuale assegnato a una macchina virtuale o un core di processore fisico se il server non è partizionato per le macchine virtuali. Devi considerare i requisiti di CPU virtuale quando decidi il VPC (virtual processor core) per la tua distribuzione in ICP. VPC è un'unità di misura per determinare il costo di licenza dei prodotti IBM. Per ulteriori informazioni sugli scenari per decidere VPC, vedi [Virtual processor core (VPC) ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente con un elevato volume di transazioni, è importante assegnare una quantità sufficientemente grande di archiviazione, ad esempio 250 GB per il tuo peer e 500 GB per il tuo servizio ordini. La quantità di archiviazione da utilizzare dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se stai per esaurire l'archiviazione sul tuo peer od ordinante, devi distribuire un nuovo peer od ordinante con un file system più grande e lasciare che esegua la sincronizzazione tramite i tuoi altri componenti sugli stessi canali.

#### Considerazioni sull'archiviazione
{: #helm-icp-storage-considerations}

* Devi determinare l'archiviazione che verrà utilizzata dai tuoi componenti. Se utilizzi le impostazioni predefinite, il grafico Helm del peer crea una nuova attestazione di volume persistente (o PVC, Persistent Volume Claim) con il nome di `my-data-pvc` per i dati del peer. Se selezioni CouchDB come tuo database libro mastro, il grafico Helm crea un'altra attestazione di volume persistente (o PVC, Persistent Volume Claim) con il nome `statedb-pvc` per il tuo database libro mastro.
* Se non vuoi utilizzare le impostazioni di archiviazione predefinite, assicurati che venga configurata una *nuova* storageClass durante l'installazione di ICP o che l'amministratore di sistema Kubernetes debba creare una nuova storageClass prima della tua distribuzione.
* [Il provisioning dinamico ![Icona link esterno](/images/external_link.svg "Icona link esterno")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Provisioning del volume dinamico") è disponibile solo per i nodi amd64 in ICP. Pertanto, se il tuo cluster include una combinazione di nodi di lavoro s390x e amd64, non è possibile utilizzare il provisioning dinamico.
* Se non viene utilizzato il provisioning dinamico, i [volumi persistenti ![Icona link esterno](/images/external_link.svg "Icona link esterno")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumi persistenti") devono essere creati e configurati con etichette che possono essere utilizzate per perfezionare il processo di bind dell'attestazione di volume persistente (o PVC, Persistent Volume Claim) di Kubernetes.
* Se utilizzi i volumi persistenti NFS v2/v3 , devi abilitare il modulo **NFS status monitor for NFSv2/v3 Filesystem Locks**, noto anche come **rpc-statd**, sul sistema host dove esiste il file system NFS. Questo modulo consente al tuo file system NFS di controllare l'eventuale presenza di blocchi esclusivi sui file detenuti da altri processi. Per abilitare questo modulo, esegui questi comandi:
  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## Installazione di ICP
{: #icp-setup-install}

Completa la seguente procedura per installare e configurare {{site.data.keyword.cloud_notm}} Private nel tuo ambiente.

1. Installa un cluster [IBM Cloud Private ![Icona link esterno](images/external_link.svg "Icona link esterno") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) alla versione 3.1.0. Se vuoi utilizzare il grafico Helm per sviluppo, test o sperimentazione, puoi installare [ICP Community Edition versione 3.1.0 ![Icona link esterno](/images/external_link.svg "Icona link esterno")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE versione 3.1.0") gratuitamente.

2. Installa la CLI di IBM Cloud Private [3.1.0 ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html) per installare e gestire la CA.

Dopo che hai installato ICP, puoi continuare a [importare il grafico Helm {{site.data.keyword.blockchainfull_notm}} Platform for ICP](howto/helm_install_icp.html) nel tuo cluster ICP.

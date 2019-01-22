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

# Configuration d'IBM Cloud Private (ICP)


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Avant de déployer des composants {{site.data.keyword.blockchainfull}} Platform et de générer un réseau de blockchain sur {{site.data.keyword.cloud_notm}} Private (ICP), vous devez configurer ICP dans votre propre environnement.
{:shortdesc}

## Prérequis
{: #icp-setup-prerequisites}
Effectuez les opérations prérequises suivantes et préparez votre environnement pour installer ICP.

### Docker
{{site.data.keyword.cloud_notm}} Private exige que Docker soit installé. Suivez les instructions associées dans  [Installing {{site.data.keyword.cloud_notm}} Private ![Icône de lien externe](/images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html "Installing {{site.data.keyword.cloud_notm}} Private") pour installer Docker.

### Paramètres ICP
Avant d'installer ICP, les conseils suivants peuvent s'avérer utiles pour préparer vos noeuds à l'installation d'ICP. Des prérequis ICP supplémentaires figurent dans la [documentation ICM ![Icône de lien externe](/images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "Preparing your cluster for installation").

#### Mise à jour du paramètre `vm.max_map_count`
ICP utilise Elastic Search pour la consignation et le décompte. Pour éviter mes exceptions out-of-memory, Elastic Search exige que la propriété système `vm.max_map_count` soit configurée. Avant d'installer ICP, voir [instructions de configuration Elastic Search![Icône de lien externe](/images/external_link.svg "Icône de lien externe")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Virtual memory") pour configurer cette propriété sur chaque noeud. Vous pouvez utiliser les commandes suivantes pour définir cette propriété de façon permanente :

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Configuration du fichier `/etc/hosts` sur chaque noeud dans votre cluster

- ICP utilise [Kubernetes ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Learn Kubernetes Basics") pour gérer les applications conteneurisées. Le serveur DNS Kubernetes est défaillant si les noms d'hôte ne sont pas configurés dans le fichier `/etc/hosts` sur chaque noeud. [Insérez l'adresse IP, le nom d'hôte et le nom abrégé de chaque noeud dans votre cluster ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "Configuring your cluster") dans le fichier `/etc/hosts` sur chaque noeud.

- [IPv6 n'est pas pris en charge par ICP ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "IPv6 n'est pas pris en charge par ICP"). Pour éviter les problèmes avec le service DNS dans un cluster ICP, désactivez les paramètres IPv6 dans le fichier `/etc/hosts` sur chaque noeud en mettant en commentaire la ligne suivante à l'aide du signe `#` au début de la ligne :
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Ressources obligatoires
{: #helm-icp-resources}

Vérifiez que votre système ICP respecte les exigences de ressources matérielle minimum :

| Composant                 | vCPU | RAM   | Disque pour le stockage de données   |
|-----------|------|-----|-----------------------|
| Autorité de certification | 1    |192 Mo | 1 Go                                 |
| Noeud du service de tri   | 2    |512 Mo | 100 Go avec possibilité d'extension  |
| Homologue                 | 2    | 2 Go  | 50 Go avec possibilité d'extension   |
| CouchDB for Peer          | 2    | 2 Go  | 50 Go avec possibilité d'extension   |

 **Remarques :**
 - Une unité vCPU est un coeur virtuel qui est affecté à une machine virtuelle ou à un coeur de processeur physique si le serveur n'est pas partitionné pour les machines virtuelles. Vous devez tenir compte des exigences vCPU lorsque vous décidez d'utiliser le coeur de processeur virtuel (VPC) pour votre déploiement dans ICP. VPC est une unité de mesure pour déterminer les coûts de licences des produits IBM. Pour plus d'informations sur les scénarios VPC, voir[Virtual processor core (VPC) ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Ces niveaux de ressources minimum sont suffisants pour les tests et l'expérimentation. Pour un environnement avec un gros volume de transactions, il est important d'allouer une quantité de mémoire suffisamment important ; 250 Go pour votre homologue et 500 Go pour votre service de tri, par  exemple. La quantité de stockage à utiliser dépend du nombre de transactions et du nombre de signatures qui sont nécessaires depuis votre réseau. Si vous êtes sur le point d'épuiser la mémoire de votre homologue ou service de tri, vous devez déployer un nouvel homologue ou service de tri avec un système de fichiers plus important et le laisser se synchroniser par l'intermédiaire de vos autres composants sur les mêmes canaux.

#### Remarques relatives au stockage
{: #helm-icp-storage-considerations}

* Vous devez déterminer l'espace de stockage que vos composants vont utiliser. Si vous utilisez les paramètres par défaut, la charte Helm de l'homologue crée un nouveau volume persistant nommé `my-data-pvc` pour les données de votre homologue. Si vous sélectionnez CouchDB comme base de données de registre, la charte Helm crée un autre volume persistant  nommé `statedb-pvc` pour les données de votre registre. 
* Si vous ne voulez pas utiliser les paramètres de stockage par défaut, vérifiez qu'une *nouvelle* storageClass est définie lors de l'installation ICP ou l'administrateur système Kubernetes doit créer une storageClass avant le déploiement.
* [Dynamic provisioning ![Icône de lien externe](/images/external_link.svg "Icône de lien externe")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Dynamic Volume Provisioning") n'est disponible que pour les noeud amd64 dans ICP. Par conséquent, si votre cluster combine à la fois des noeuds worker s390x et amd64, la mise à disposition dynamique ne peut pas être utilisée.
* Si la mise à disposition dynamique n'est pas utilisée, des [Volumes permanents ![Icône de lien externe](/images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes permanents") doivent être créés et configurés avec des libellés qui peuvent être utilisés pour affiner le processus de liaison PVC (Persistent Volume Claim) Kubernetes. 
* Si vous utilisez des Volumes permanents NFS v2/v3, vous devez activer le module **NFS status monitor for NFSv2/v3 Filesystem Locks**, également appelé **rpc-statd**, sur le système hôte où réside le système de fichiers NFS. Ce module permet à votre système de fichiers NFS de vérifier les verrous exclusifs sur les fichiers maintenus par d'autres processus. Exécutez les commandes suivantes pour activer ce module :
  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## Installation d'ICP
{: #icp-setup-install}

Pour installer et configurer {{site.data.keyword.cloud_notm}} Private dans votre environnement, procédez comme suit.

1. Installez un cluster  [IBM Cloud Private ![Icône de lien externe](images/external_link.svg "Icône de lien externe") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) en version 3.1.0. Si vous voulez utiliser la charte Helm à des fins de développement, de test ou d'expérimentation, vous pouvez installer [ICP édition Community version 3.1.0 ![Icône de lien externe](/images/external_link.svg "Icône de lien externe")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE version 3.1.0") sans frais.

2. Installez l'interface CLI d'IBM Cloud Private [3.1.0 ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html) pour installer et utiliser l'autorité de certification.

Une fois ICP installé, vous pouvez continuer à [importer la charte Helm d'{{site.data.keyword.blockchainfull_notm}} Platform for ICP](howto/helm_install_icp.html) dans votre cluster ICP.

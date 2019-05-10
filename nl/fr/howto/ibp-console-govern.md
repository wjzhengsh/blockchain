---

copyright:
  years: 2019
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

# Gouvernance des composants
{: #ibp-console-govern}

Après avoir créé des autorités de certification, des homologues, des organisations et des canaux, vous pouvez utiliser la console pour mettre à jour ces composants.
{:shortdesc}

**Public cible :** Cette rubrique s'adresse aux opérateurs réseau qui sont responsables de la création, de la surveillance et de la gestion du réseau de blockchain.  

## Comment {{site.data.keyword.cloud_notm}} Kubernetes Service interagit avec la console
{: #ibp-console-govern-iks-console-interaction}

Il incombe à l'opérateur réseau de surveiller l'utilisation de l'UC, de la mémoire et du stockage et de s'assurer que les ressources adéquates sont disponibles **avant** d'essayer de créer ou de redimensionner un noeud.
{:important}

Comme votre instance de la console {{site.data.keyword.blockchainfull_notm}} Platform et votre cluster {{site.data.keyword.cloud_notm}} Kubernetes Service ne communiquent pas directement au sujet des ressources qui sont disponibles dans votre cluster, le processus de déploiement ou de redimensionnement des composants à l'aide de la console doit respecter le schéma suivant :

1. **Dimensionnez le déploiement que vous voulez effectuer**. Les panneaux **Allocation de ressources** pour l'autorité de certification, l'homologue et le service de tri sur la console proposent une allocation par défaut de l'UC, de la mémoire et du stockage pour chaque noeud. Vous devrez peut-être ajuster ces valeurs en fonction de votre cas d'utilisation. Si vous avez des doutes, commencez par les allocations par défaut et ajustez-les dès que vous avez connaissance de vos besoins. De même, le panneau **Allocation de ressources** affiche les allocations de ressource existantes.

  Pour vous faire une idée de la quantité de stockage et de calcul dont vous aurez besoin dans votre cluster, consultez le tableau à la suite de cette liste, qui contient les valeurs par défaut actuelles pour l'homologue, le service de tri et l'autorité de certification.

2. **Vérifiez si vous disposez de suffisamment de ressources dans votre cluster {{site.data.keyword.cloud_notm}} Kubernetes Service**. Pour la surveillance de vos ressources Kubernetes, nous vous conseillons d'utiliser l'outil [{{site.data.keyword.cloud_notm}} SysDig ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/cloud/sysdig "IBM Cloud Monitoring with Sysdig") en association avec votre tableau de bord {{site.data.keyword.cloud_notm}} Kubernetes. Si vous n'avez pas assez d'espace dans votre cluster pour déployer ou redimensionner les ressources, vous devez augmenter la taille de votre cluster {{site.data.keyword.cloud_notm}} Kubernetes Service. Pour plus d'informations sur l'augmentation de la taille d'un cluster, voir [Scaling clusters ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](/docs/containers?topic=containers-ca#ca "Scaling clusters"). Si vous disposez de suffisamment d'espace dans le cluster, vous pouvez passer à l'étape 3.
3. **Utilisez la console pour déployer ou redimensionner votre noeud**. Si le noeud worker sur lequel s'exécute le pod n'a plus de ressources, vous pouvez ajouter un nouveau noeud worker plus grand à votre cluster et supprimer le noeud worker existant.

| **Composant** (tous les conteneurs) | UC (en millicpus) | UC (en CPU) | Mémoire (en mégaoctets) | Mémoire (en gigaoctets) | Stockage (en gigaoctets) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **Homologue**                       | 1100               | 1,1           | 2200                  | 2,2                   | 200                    |
| **AC**                         | 300                | 0,3            | 600                   | 0,6                    | 10                     |
| **Service de tri**                    | 450                | 0,45           | 900                   | 0,9                    | 100                    |

Dans les cas où un utilisateur souhaite minimiser les frais sans arrêter complètement un noeud ou sans le supprimer, il est possible de réduire un noeud à un minimum de 0,001 CPU (1 milliCPU). Notez que le noeud ne sera pas opérationnel si cette quantité d'UC est utilisée.
{:important}

## Allocation de ressources
{: #ibp-console-govern-allocate-resources}

Les utilisateurs d'un cluster gratuit **doivent utiliser les tailles par défaut** pour les conteneurs associés à leurs noeuds, tandis que les utilisateurs de clusters payants peuvent définir ces valeurs pendant la création de leurs noeuds.

Le panneau **Allocation de ressources** sur la console fournit les valeurs par défaut pour les différentes zones impliquées dans la création d'un noeud. Ces valeurs sont choisies car elles représentent un bon moyen de démarrer. Toutefois, chaque cas d'utilisation est différent. Bien que cette rubrique fournisse des conseils sur la manière de définir ces valeurs, c'est à l'utilisateur au final de surveiller ses noeuds et de déterminer les dimensions qui leurs conviennent. Par conséquent, dans le cas où des utilisateurs sont certains qu'ils auront besoin de valeurs différent des valeurs par défaut, une stratégie pragmatique consiste à utiliser ces valeurs par défaut et à les ajuster ultérieurement. Pour une vue d'ensemble des performances et de l'échelle d'Hyperledger Fabric, qui constitue la base de {{site.data.keyword.blockchainfull_notm}} Platform, voir [Answering your questions on Hyperledger Fabric performance and scale ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Blog about Hyperledger Fabric performance and scale").

Tous les conteneurs qui sont associés à un noeud ont une **UC** et de la **mémoire**, alors que certains conteneurs qui sont associés à l'homologue, au service de tri et à l'autorité de certification ont également un **stockage**. Pour plus d'informations sur les différentes options de stockage disponibles dans {{site.data.keyword.cloud_notm}}, consultez les [options de stockage ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](/docs/containers?topic=containers-kube_concepts#kube_concepts "Kubernetes storage options"). Examinez ces options avec attention avant de décider de la classe de stockage à utiliser.

Bien qu'il soit possible de modifier l'UC et la mémoire depuis la console et le tableau de bord {{site.data.keyword.cloud_notm}} Kubernetes Service, une fois qu'un noeud a été créé, il est également possible de modifier le stockage ultérieurement à partir de l'interface CLI {{site.data.keyword.cloud_notm}}.
{:note}

Chaque noeud dispose d'un conteneur de proxy web gRPC qui amorce la couche de communication entre la console et un noeud. Ce conteneur a des valeurs de ressource fixes et il figure dans le panneau Allocation de ressources pour fournir une estimation précise de l'espace requis dans votre cluster Kubernetes pour que le noeud puisse être déployé. Comme les valeurs de ce conteneur ne peuvent pas être modifiées, nous ne parlerons pas du proxy web gRPC dans les sections ci-après.

### Autorités de certification (AC)
{: #ibp-console-govern-CA}

A la différence des homologues et des services de tri, qui sont activement impliqués dans le processus de transaction, les autorités de certification sont uniquement impliquées dans l'enregistrement et l'inscription des identités, et dans la création d'un MSP. Cela signifie qu'elles ont besoin de moins d'UC et de mémoire. Pour contraindre une autorité de certification, un utilisateur devrait la surcharger de demandes (probablement à l'aide d'API et d'un script), ou émettre un tel grand nombre de certificats qu'elle est à court de stockage. Dans des conditions de fonctionnement normales, rien de cela ne doit arriver, bien que comme toujours ces valeurs doivent refléter les besoins d'un cas d'utilisation particulier.

L'adaptateur de canal comporte un seul conteneur associé que nous pouvons ajuster :

* **L'autorité de certification elle-même**: elle encapsule les processus d'autorité de certification internes, comme l'enregistrement et l'inscription des noeuds et des utilisateurs, ainsi que le stockage d'une copie de chaque certificat émis.

#### Dimensionnement d'une autorité de certification pendant sa création
{: #ibp-console-govern-CA-sizing-creation}

L'adaptateur de canal n'a qu'un seul conteneur comportant des valeurs dont vous devez vous soucier car les valeurs du serveur proxy web gRPC ne peuvent pas être modifiées.

| Ressources | Condition d'accroissement |
|-----------------|-----------------------|
| **UC et mémoire du conteneur d'autorité de certification** | Lorsque vous prévoyez que votre autorité de certification va être bombardée d'enregistrements et d'inscriptions. |
| **Stockage de l'autorité de certification** | Lorsque vous prévoyez d'utiliser cette autorité de certification pour enregistrer un grand nombre d'utilisateurs et d'applications. |

### Homologues
{: #ibp-console-govern-peers}

L'homologue comporte trois conteneurs associés que nous pouvons ajuster :

- **L'homologue lui-même** : il encapsule les processus d'homologue internes (comme la validation des transactions) et la blockchain (en d'autres termes, l'historique de la transaction) pour l'ensemble des canaux auxquels il appartient. Notez que le stockage de l'homologue inclut également les contrats intelligents installées sur l'homologue.
- **CouchDB** : emplacement de stockage des bases de données d'état de l'homologue. Gardez à l'esprit que chaque canal a une base de données d'état distincte.
- **Contrat intelligent** : Souvenez-vous que lors d'une transaction, le contrat intelligent approprié est "appelé" (en d'autres termes, exécuté). Notez que tous les contrats intelligents que vous installez sur l'homologue s'exécuteront dans un conteneur distinct au sein du conteneur de votre contrat intelligent, appelé conteneur Docker-in-Docker.

#### Dimensionnement d'un homologue pendant sa création
{: #ibp-console-govern-peers-sizing-creation}

Comme nous l'avons indiqué dans la section relative à la [façon dont Kubernetes interagit avec la console](#ibp-console-govern-iks-console-interaction), il est recommandé d'utiliser les valeurs par défaut pour ces conteneurs d'homologues et de les ajuster ultérieurement lorsque leur utilisation devient plus claire.

| Ressources | Condition d'accroissement |
|-----------------|-----------------------|
| **UC et mémoire du conteneur d'homologues** | Lorsque vous anticipez un haut débit de transactions immédiat. |
| **Stockage d'homologue** | Lorsque vous anticipez d'installer de nombreux contrats intelligents sur cet homologue et de les joindre à de nombreux canaux. Gardez à l'esprit que ce stockage sera aussi utilisé pour stocker les contrats intelligents de tous les canaux que l'homologue a rejoint. N'oubliez pas qu'on estime qu'une "petite" transaction avoisine les 10 000 octets (10k). Le stockage par défaut étant de 100 Go, cela signifie qu'un total de 10 million de transactions pourront figurer dans le stockage de l'homologue avant qu'il ne soit nécessaire de l'étendre (en pratique, le nombre maximum sera inférieur à cela, car les transactions sont de différentes tailles et ce nombre n'inclut pas les contrats intelligents). Même si 100 Go peut par conséquent sembler un stockage bien supérieur que nécessaire, gardez en tête que le stockage es relativement bon marché, et qu'il est plus difficile de l'augmenter (ligne de commande nécessaire) que d'augmenter l'UC ou la mémoire. |
| **UC et mémoire du conteneur CouchDB** | Lorsque vous anticipez un volume important de requêtes sur une base de données d'état de grande taille. Cet effet peut être quelque peu atténué par l'utilisation d'[index ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html#couchdb-indexes "Documentation relative aux index Hyperledger Fabric"). Néanmoins, des volumes élevés peuvent exercer une contrainte sur CouchDB, ce qui peut entraîner des délais de requêtes et de transactions. |
| **Stockage CouchDB (données de registre)** | Lorsque vous prévoyez un débit élevé via de nombreux canaux et n'envisagez pas d'utiliser des index. Toutefois, comme pour le stockage d'homologue, le stockage CouchDB par défaut est de 100 Go, ce qui est élevé. |
| **UC et mémoire du conteneur de contrats intelligents** | Lorsque vous prévoyez un débit élevé sur un canal, notamment dans les cas où plusieurs contrats intelligents vont être appelés en une seule opération.|

### Noeuds de tri
{: #ibp-console-govern-ordering-nodes}

Comme les services de tri ne gèrent pas la base de données d'état et qu'ils n'hébergent pas des contrats intelligents, ils ont besoin de moins de conteneurs que les homologues. Ils hébergent cependant la blockchain (l'historique de la transaction) car il s'agit de l'emplacement où est stockée la configuration de canal, et le service de tri doit avoir connaissance de la configuration de canal la plus récente pour exécuter son rôle.

Comme avec l'autorité de certification, le service de tri comporte un seul conteneur associé que nous pouvons ajuster : 

* **Le service de tri lui-même** : il encapsule les processus de service de tri internes (comme la validation des transactions) et la blockchain pour l'ensemble des canaux qu'il héberge. 

#### Dimensionnement d'un service de tri pendant sa création
{: #ibp-console-govern-peers-sizing-creation}

Comme nous l'avons indiqué dans la section relative à la [façon dont Kubernetes interagit avec la console](#ibp-console-govern-iks-console-interaction), il est recommandé d'utiliser les valeurs par défaut pour ces conteneurs de services de tri et de les ajuster ultérieurement lorsque leur utilisation devient plus claire.

| Ressources | Condition d'accroissement |
|-----------------|-----------------------|
| **UC et mémoire du conteneur de services de tri** | Lorsque vous anticipez un haut débit de transactions immédiat. |
| **Stockage de service de tri** |  Lorsque vous anticipez que ce service de tri va faire partie d'un service de tri sur de nombreux canaux. N'oubliez pas que les services de tri conservent une copie de la blockchain pour chaque canal dont ils font partie. Le stockage par défaut du service de tri est de 100 Go, tout comme le conteneur pour l'homologue lui-même. |

Une bonne pratique, non obligatoire, consiste à faire en sorte que la taille de l'UC et de la mémoire de vos noeuds de tri soit plus ou moins équivalente au double de la taille de vos homologues. Si un noeud de service de tri est sursollicité, des délais peuvent se produire et il peut commencer à supprimer des transactions, lesquelles doivent alors de nouveau être soumises. Cela peut avoir des conséquences bien plus nuisibles pour un réseau qu'un simple homologue qui lutte pour maintenir l'activité. Dans une configuration de service de tri Raft, un noeud principal sursollicité peut cesser d'envoyer des messages de contact, entraînant le choix d'un élément principal, et une cessation temporaire de tri des transactions. De même, un noeud suiveur peut rater des messages et essayer de déclencher le choix d'un élément principal alors qu'aucun n'est nécessaire.
{:important}

## Réallocation des ressources
{: #ibp-console-govern-reallocate-resources}

Après le redimensionnement d'un noeud, vous pouvez constater un délai avant la prise en compte car les conteneurs sont en cours de reconstitution.{:important}

Comme indiqué plus haut, nous recommandons l'utilisation de l'outil [{{site.data.keyword.cloud_notm}} SysDig ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/cloud/sysdig "{{site.data.keyword.cloud_notm}} Surveillance à l'aide de Sysdig") en association avec votre tableau de bord {{site.data.keyword.cloud_notm}} Kubernetes pour surveiller l'utilisation de vos ressources Kubernetes. Si vous déterminez qu'un noeud worker n'a plus de ressources, vous pouvez ajouter un nouveau noeud worker plus grand à votre cluster et supprimer le noeud worker existant.{:note}

Même s'il est plus simple de déployer suffisamment de ressources dans {{site.data.keyword.cloud_notm}} Kubernetes Service et de pouvoir étendre vos pods et noeuds worker lorsque nécessaire, sans avoir à accroître d'abord votre déploiement dans {{site.data.keyword.cloud_notm}} Kubernetes Service, il est clair que plus grand sera le déploiement dans {{site.data.keyword.cloud_notm}} Kubernetes Service, plus il sera onéreux. Les utilisateurs doivent évaluer soigneusement toutes les possibilités et tenir compte des compromis qu'ils doivent effectuer quelle que soit l'option choisie.

Vous pouvez procéder de l'une des manières suivantes pour réallouer les ressources que vous affectez aux conteneurs qui sont associés à votre noeud.

1. **Utilisez la fonction Mise à l'échelle auto {{site.data.keyword.cloud_notm}} Kubernetes Service**. La fonction Mise à l'échelle auto permet d'augmenter ou de réduire vos noeuds worker en fonction des paramètres spécifiques de votre pod et des demandes de ressources. Pour plus d'informations sur la fonction {{site.data.keyword.cloud_notm}}Mise à l'échelle auto Kubernetes Service et comment la configurer, voir [Scaling clusters ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](/docs/containers?topic=containers-ca#ca "Scaling clusters") dans la documentation IBM {{site.data.keyword.cloud_notm}}. Notez que si la fonction de mise à l'échelle auto est utilisée pour l'ajustement de vos ressources, des frais seront appliqués à votre compte {{site.data.keyword.cloud_notm}} Kubernetes Service et ils varieront en fonction de votre utilisation.
2. **Effectuez une mise à l'échelle manuelle**. Cela implique de surveiller votre utilisation sur la console et dans le cluster {{site.data.keyword.cloud_notm}} Kubernetes Service. Même si les étapes manuelles nécessaires sont bien plus nombreuses qu'avec la fonction Mise à l'échelle auto, l'avantage est que l'utilisateur est toujours certain du montant facturé sur son compte {{site.data.keyword.cloud_notm}} Kubernetes Service.

Pour effectuer une mise à l'échelle manuelle, cliquez sur le noeud que vous voulez ajuster sur la page **Noeuds**, puis cliquez sur l'onglet **Utilisation**. Vous pouvez alors voir un bouton libellé **Réallouer**, lequel permet d'ouvrir un onglet **Allocation de ressources** qui est très similaire à celui que vous avez vu lors de la création du noeud. Si vous voulez réduire la quantité de ressources disponibles, indiquez simplement de plus petites valeurs et cliquez sur **Réallouer les ressources** sous cet onglet. La page **Récapitulatif** s'affiche.

Si vous voulez accroître l'UC et la mémoire d'un noeud, utilisez l'onglet **Allocation de ressources** pour augmenter ces valeurs. La boîte blanche au bas de la page ajoute les nouvelles valeurs. Une fois que vous avez cliqué sur **Réallouer les ressources**, la page **Récapitulatif** convertit cette valeur en un montant **VPC**, lequel est utilisé pour calculer votre facture. Vous devrez ensuite accéder à votre tableau de bord {{site.data.keyword.cloud_notm}} Kubernetes Service pour vous assurer que votre cluster dispose de suffisamment de ressources pour cette réallocation. Si tel est le cas, vous pouvez cliquer sur **Réallouer les ressources**. S'il n'y a pas suffisamment de ressources disponibles, vous devrez augmenter la taille de votre cluster à partir du tableau de bord {{site.data.keyword.cloud_notm}} Kubernetes Service.

La méthode que vous utiliserez pour accroître le stockage dépendra de la classe de stockage que vous avez choisie pour votre cluster. Consultez la documentation [options de stockage ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](/docs/containers?topic=containers-kube_concepts#kube_concepts "storage options) pour plus d'informations sur l'augmentation de votre stockage.

Contrairement à ce que vous avez effectué pour l'UC et la mémoire, qui peuvent être accrues à l'aide de la console (si des ressources sont disponibles dans votre cluster {{site.data.keyword.cloud_notm}} Kubernetes Service), vous devrez utiliser l'interface CLI {{site.data.keyword.cloud_notm}} pour accroître le stockage de vos noeuds. Pour suivre un tutoriel relatif à cette opération, voir [Modification de la taille et des IOPS de votre unité de stockage existante ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](/docs/containers?topic=containers-file_storage#file_change_storage_configuration "Modification de la taille et des IOPS de votre unité de stockage existante").

## Optimisation de votre service de tri
{: #ibp-console-govern-orderer-tuning}

Les performances de votre plateforme de blockchain peuvent être affectées par de nombreuses variables, telles que la taille de transaction, la taille de bloc, la taille de réseau, ainsi que les limites du matériel. Le noeud du service de tri inclut un ensemble de paramètres d'optimisation qui peuvent être utilisés pour contrôler le débit et les performances du service de tri.  Vous pouvez utiliser ces paramètres pour personnaliser la façon dont votre service de tri traite les transactions selon que vous avez un grand nombre de petites transactions fréquentes, ou moins de transactions de plus grande taille et moins fréquentes. Fondamentalement, vous avez la possibilité de décider à quel moment découper les blocs en fonction de la taille, de la quantité et du débit d'arrivée des transactions.

Les paramètres suivants sont disponibles sur la console en cliquant sur le noeud de service de tri sous l'onglet **Noeuds**, puis en cliquant sur son icône **Paramètres**. Cliquez sur le bouton **Avancé** pour ouvrir la **Configuration de canal avancée** du service de tri.

### Taille de lot
{: #ibp-console-govern-orderer-tuning-batch-size}

Les trois paramètres suivantes fonctionnent conjointement pour contrôler le moment où un bloc peut être découpé, en fonction du nombre maximum de transactions définies dans un bloc et de la taille de bloc elle-même.

- **Nombre max d'octets absolu**  
  Définissez cette valeur sur la taille de bloc la plus élevée en octets pouvant être découpée par le service de tri. Aucune transaction ne peut être supérieure à la valeur de `Nombre max d'octets absolu`. Généralement, ce paramètre peut être sans problème deux ou trois fois supérieur à la valeur de `Nombre max d'octets préféré`.    
  **Remarque **: La taille maximum autorisée est de 99 Mo.
- **Nombre max de messages**   
  Définissez cette valeur sur le nombre maximum de transactions pouvant être incluses dans un seul bloc.
- **Nombre max d'octets préféré**  
  Définissez cette valeur sur la taille de bloc idéale en octets, celle-ci devant être inférieure à la valeur de `Nombre max d'octets absolu`. Une taille de transaction minimum (transaction sans adhésions) est d'environ 1 Ko. Si vous ajoutez 1 Ko par adhésion requise, une taille de transaction standard est d'environ 3 à 4 Ko. Par conséquent, il est recommandé de définir pour `Nombre max d'octets préféré` une valeur égale à environ `Nombre max de messages * taille de transfert moyen prévu`. Lors de l'exécution, chaque fois que possible, les blocs ne doivent pas dépasser cette taille. Si lors de l'arrivée d'une transaction, la taille de bloc est dépassée, le bloc est découpé et un nouveau bloc est créé pour cette transaction. Néanmoins, si à l'arrivée d'une transaction, cette valeur est dépassée mais qu'elle ne dépasse pas la valeur de `Nombre max d'octets absolu`, la transaction est incluse. Si un bloc entrant est d'une taille supérieure à la valeur de la zone `Nombre max d'octets préféré`, il ne contiendra qu'une seule transaction, et cette dernière ne peut pas être d'une taille supérieure à la valeur de `Nombre max d'octets absolu`.

Ensemble, ces paramètres peuvent être configurés pour optimiser le débit de votre service de tri.

### Dépassement de délai de lot
{: #ibp-console-govern-orderer-tuning-batch-timeout}

Définissez la valeur de **Dépassement de délai** sur le délai d'attente, en secondes, à observer après l'arrivée de la première transaction avant de découper le bloc. Si vous définissez une valeur trop faible, les lots risquent de ne pas se remplir à la taille préférée. Si la valeur est trop élevée, il se peut que le service de tri attende les blocs et que les performances globales se dégradent. De manière générale, nous recommandons de définir la valeur de `Dépassement de délai de lot` sur une valeur au moins égale à `nombre de messages max / nombre max de transactions par seconde`.

Lorsque vous modifiez ces paramètres, cela n'a aucune incidence sur le comportement des canaux existants sur le service de tri ; en revanche, les modifications que vous apportez à la configuration de service de tri s'appliquent uniquement aux nouveaux canaux que vous créez sur ce service de tri.
{:important}

## Modification des canaux
{: #ibp-console-govern-channels}

### Configuration des homologues d'ancrage
{: #ibp-console-govern-channels-anchor-peers}

Comme la communication de protocole [gossip ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Protocole de dissémination des données gossip") entre organisations doit être activée pour la reconnaissance de service et les données privées, un homologue d'ancrage doit exister pour chaque organisation. Cet homologue d'ancrage n'est pas un **type** d'homologue spécial, il s'agit simplement de l'homologue que l'organisation présente aux autres organisations, et ce faisant amorce la communication gossip entre organisations. Par conséquent, au moins un [homologue d'ancrage ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Homologues d'ancrage") doit être défini pour chaque organisation dans la définition de collecte.

Pour configurer un homologue en tant qu'homologue d'ancrage, cliquez sur l'onglet **Canaux** et ouvrez le canal dans lequel le contrat intelligent a été instancié.
 - Cliquez sur l'onglet **Détails du canal**. 
 - Faites défiler jusqu'au tableau des homologues d'ancrage et cliquez sur **Ajouter un homologue d'ancrage**.
 - Sélectionnez au moins un homologue de chaque organisation dans la définition de collecte qui doit servir d'homologue d'ancrage pour l'organisation. Pour des raisons de redondance, vous pouvez envisager de sélectionner plusieurs homologues de chaque organisation dans la collecte.

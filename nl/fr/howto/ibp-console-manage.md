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


# Administration de votre console
{: #ibp-console-manage-console}

Vous pouvez gérer le comportement de votre console de plusieurs manières. Cette rubrique décrit des actions autres que les opérations de noeud de blockchain qui vous aident dans votre usage quotidien de la console.
{:shortdesc}

**Public cible :** Cette rubrique s'adresse aux opérateurs réseau qui sont responsables de la création, de la surveillance et de la gestion du réseau de blockchain.

## Ajout et retrait d'utilisateurs de la console

Cette console {{site.data.keyword.blockchainfull}} Platform est accessible avec l'adresse e-mail du propriétaire {{site.data.keyword.IBM_notm}} en tant qu'administrateur de la console. L'administrateur peut ensuite accorder l'accès de la console à d'autres utilisateurs.

Cliquez sur **Utilisateurs** dans la navigation de gauche pour accorder un accès de la console à de nouveaux utilisateurs à l'aide de leur adresse e-mail. Cliquez sur **Ajouter de nouveaux utilisateurs** et indiquez une liste d'adresses e-mail que vous voulez autoriser ainsi que leur type d'utilisateur : `Admin` ou `General`. Les utilisateurs de type `Admin` peuvent ajouter de nouveaux utilisateurs ou supprimer des utilisateurs existants et consulter les journaux de la console. Les utilisateurs de type `General` peuvent se connecter à la console mais ils ne peuvent pas ajouter ou retirer des utilisateurs ou consulter les journaux.


Les utilisateurs répertoriés sur ce panneau sont simplement les adresses e-mail des utilisateurs qui peuvent se connecter à la console. Ils n'ont aucune relation avec les **Organisations disponibles** dans l'onglet Organisations ou avec les identités stockées par le portefeuille de console.
{:note}

## Mise à jour de l'adresse e-mail de l'administrateur

Si la console est utilisée par plusieurs personnes ou organisations, il est recommandé de créer une adresse e-mail fonctionnelle pour accéder à votre réseau. Cette adresse e-mail vous permettra d'accéder à la console si l'admin d'origine quitte votre organisation ou encore si son adresse e-mail est suspendue. S'il n'est pas possible de créer une adresse e-mail fonctionnelle, vous pouvez accorder l'accès `Admin` à plusieurs utilisateurs afin d'éviter de dépendre d'une seule personne.

Pour pouvoir mettre à jour l'adresse e-mail de l'administrateur de console qui a été configuré lors du déploiement de la console, vous devez être connecté en tant qu'administrateur de console. Accédez à l'onglet **Utilisateurs** et cliquez sur **Configurer** sur la vignette **ID IBM**. Dans le panneau qui s'affiche, indiquez une nouvelle adresse e-mail pour l'administrateur de la console.

## Consultation des journaux 
{: #ibp-console-manage-logs}

Au cours de l'utilisation de la console {{site.data.keyword.blockchainfull_notm}} Platform, vous devrez peut-être consulter les journaux afin de déboguer un problème.

### Consultation des journaux de la console
{: #ibp-console-manage-console-logs}

Vous pouvez facilement accéder aux journaux de la console si vous avez besoin de déboguer des problèmes que vous rencontrez lors de l'utilisation de la console ou de l'exploitation de vos noeuds. Vous pouvez également augmenter ou diminuer le nombre de journaux collectés par la console en définissant un niveau de consignation. Les journaux de la console sont collectés séparément des [journaux de vos noeuds](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), lesquels sont collectés par le service {{site.data.keyword.IBM_notm}} Kubernetes.

Accédez à l'onglet **Paramètres** dans le navigateur de la console pour modifier les paramètres de journalisation. Les journaux de la console sont collectés à partir de deux sources distinctes :

  * **Journalisation client :** Ces journaux sont collectés lorsque des commandes sont envoyées depuis votre navigateur à la console.
  * **Journalisation serveur :** Ces journaux sont collectés lorsque la console envoie des commandes à vos noeuds et depuis le déploiement de la console. Ils incluent la sortie de journalisation d'Hyperledger Fabric.

Définissez le nombre de journaux collectés dans la liste déroulante sous chaque type de journal. Par exemple, **Erreur** et **Avertissement** correspondent à la collecte de journaux la moins élevée, tandis que **Débogage** et **Tout** correspondent à la collecte de journaux la plus élevée.

Vous pouvez uniquement afficher les journaux de la console si vous êtes connecté en tant qu'administrateur de console. Pour afficher les journaux depuis l'onglet **Paramètres**, remplacez le mot `settings` dans l'URL du navigateur par `api/v1/logs`. Par exemple, si l'URL de votre console est `localhost:3001/settings`, vous pouvez consulter vos journaux en accédant à l'adresse `localhost:3001/api/v1/logs`. Les journaux client et serveur sont collectés dans des fichiers distincts. Les journaux les plus récents figurent en haut de la page.

### Consultation des journaux de votre noeud
{: #ibp-console-manage-console-node-logs}

Les journaux de vos homologues, services de tri et autorités de certification sont collectés par le service {{site.data.keyword.IBM_notm}} Kubernetes. Utilisez les étapes ci-dessous pour consulter les journaux de vos noeuds depuis le cluster où vous avez déployé votre réseau {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Ouvrez le tableau de bord [{{site.data.keyword.cloud_notm}}![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/resources) et accédez à l'écran de présentation de votre cluster {{site.data.keyword.IBM_notm}} Kubernetes Service.
2. Au-dessus de l'écran de présentation du cluster, cliquez sur **Tableau de bord Kubernetes**.
3. Dans le tableau de bord Kubernetes, utilisez la liste déroulante Espace de nom dans la navigation de gauche pour remplacer l'espace de nom par l'instance de service {{site.data.keyword.blockchainfull_notm}} Platform. Le nom de l'instance de service sera une longue chaîne de lettres et de nombres. Vous pouvez trouver le nom de votre instance de service au début de l'URL de votre console {{site.data.keyword.blockchainfull_notm}} Platform.
4. Dans la navigation de gauche, cliquez sur **Pods** pour afficher la liste des pods de noeud que vous avez déployés.
5. Cliquez sur un pod. Cliquez ensuite sur **Afficher les journaux** dans le menu du haut pour ouvrir les journaux de votre noeud. Au-dessus des journaux, vous pouvez utiliser le menu déroulant en regard de **Journaux de ** pour consulter les journaux des différents conteneurs au sein du pod. Par exemple, votre homologue et la base de données d'état (CouchDB, par exemple) s'exécutent dans des conteneurs différents et génèrent des journaux différents.

Par défaut, les journaux de vos noeuds sont collectés en local au sein de votre cluster. Vous pouvez également utiliser le service {{site.data.keyword.cloud_notm}} Log Analysis ou un service tiers pour collecter, stocker et analyser les journaux de votre réseau. Pour plus d'informations, voir [Journalisation et surveillance pour {{site.data.keyword.IBM_notm}} Kubernetes Service ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Journalisation et surveillance pour {{site.data.keyword.IBM_notm}} Kubernetes Service"). Nous vous recommandons d'utiliser le service [{{site.data.keyword.cloud_notm}} LogDNA ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis avec LogDNA") qui vous permet de facilement analyser les journaux en temps réel.

### Consultation des journaux de votre conteneur de contrat intelligent 
{: #ibp-console-manage-console-container-logs}

Si vous rencontrez des problèmes au niveau de votre contrat intelligent, vous pouvez consulter les journaux du conteneur de contrat intelligent, ou de code blockchain, pour déboguer un problème :

- Ouvrir votre tableau de bord Kubernetes et cliquez sur le pod homologue dans lequel s'exécute le contrat intelligent.
- Cliquez sur le lien `exec` depuis votre tableau de bord dans lancer l'exec dans le pod. Par défaut, il pointe sur le conteneur homologue.
- Basculez vers le conteneur `dind` en le sélectionnant dans la liste déroulante.
- Exécutez la commande `docker ps -a` pour voir la liste des conteneurs de code blockchain.
- Exécutez `docker logs <chaincode-container-ID>` en remplaçant <chaincode-container-ID> par l'ID de votre conteneur de code blockchain.


## Surveillance de l'utilisation des ressources

Lorsque les noeuds {{site.data.keyword.cloud_notm}} Platform sont déployés, ils sont pré-configurés avec des paramètres par défaut pour l'UC, la mémoire et le stockage, et ces valeurs ne peuvent pas être modifiées. Vous pouvez surveiller votre utilisation des ressources à l'aide du tableau de bord {{site.data.keyword.IBM_notm}} Kubernetes Service. Si vous essayez de créer un nouveau noeud sur la console {{site.data.keyword.cloud_notm}} Platform et obtenez une erreur indiquant que vous devez étendre votre cluster Kubernetes, il est possible d'ajouter davantage d'espace de stockage au cluster Kubernetes. Consultez ces [informations ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "Modification de la taille et des IOPS de votre périphérique de stockage existant") sur l'accroissement de la capacité de stockage ou des performances de votre volume existant.

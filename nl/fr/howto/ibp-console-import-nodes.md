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

# Importation de noeuds
{: #ibp-console-import-nodes}

La console inclut une option d'importation des noeuds qui sont créés à l'aide d'une autre console {{site.data.keyword.blockchainfull}} Platform.
{:shortdesc}  

**Public cible :** Cette rubrique s'adresse aux opérateurs réseau qui sont responsables de la création, de la surveillance et de la gestion du réseau de blockchain.

## Pourquoi importer un noeud ?

Vous pouvez importer les noeuds d'une autorité de certification, d'un service de tri ou d'un homologue depuis d'autres consoles {{site.data.keyword.blockchainfull_notm}} Platform lorsque vous devez les exploiter avec des noeuds déjà existants sur votre console. Par exemple, vous pouvez utiliser l'option d'importation d'homologue lorsque vous voulez joindre des homologues d'organisations à l'extérieur de votre console à des canaux sur votre console. Comme les services {{site.data.keyword.blockchainfull_notm}} Platform 2.0 sont déployés dans plusieurs environnements {{site.data.keyword.cloud_notm}}, il est probable que certaines instances de service incluent uniquement les autorités de certification et les homologues, alors que d'autres hébergent des services de tri. L'importation de noeuds différents dans la console constitue un moyen d'afficher et d'exploiter ces composants distribués depuis une interface utilisateur unique afin qu'ils puissent effectuer des transactions ensembles sur la blockchain.

Une fois que vous avez importé des noeuds sur la console, vous disposez d'un puissant ensemble de fonctions pour :
- Ajouter de nouvelles organisations au consortium
- Créer de nouveaux canaux
- Joindre des homologues aux canaux
- Installer des contrats intelligents sur les homologues quel que soit l'emplacement où ils ont été déployés
- Instancier des contrats intelligents sur des canaux
- Mettre à niveau des contrats intelligents sur des canaux

Lors de l'importation d'un composant, vous devez fournir ses informations de connexion. Lorsque vous importez un composant, vous n'importez pas vraiment le composant physique lui-même ; il utilise au contraire les informations de connexion pour générer une représentation du composant sur la console de manière à pouvoir être exploité depuis la console.

 Une fois que vous avez importé un noeud sur la console, vous pouvez également modifier ses informations de connexion à l'aide de l'onglet **Paramètres** du noeud.

{: note}
Pour que des noeuds puissent être importés sur la console, ils doivent être exportés depuis la console {{site.data.keyword.blockchainfull_notm}} Platform où ils ont été créés.  Exportez simplement les informations de noeud depuis la console dans un fichier `JSON` et importez le fichier généré en utilisant le bouton `Télécharger JSON` à l'étape du panneau latéral du noeud d'importation de votre console.


## Premiers pas : Collecte des certificats ou des données d'identification

Avant d'importer un composant de blockchain existant à partir d'une autre instance de service {{site.data.keyword.blockchainfull_notm}} Platform 2.0, vous devez importer l'identité admin du composant dans votre portefeuille de console. L'importation de l'identité dans votre portefeuille de console simplifie le fonctionnement de noeud. L'opérateur réseau qui a créé le noeud doit vous fournir ces données d'identification.

## Importation d'une autorité de certification
{: #ibp-console-import-ca}

Un noeud d'autorité de certification est une zone de blockchain qui émet des certificats pour toutes les entités du réseau (homologues, services de tri, clients, et ainsi de suite) afin que ces entités puissent communiquer, s'authentifier et enfin effectuer des transactions. Chaque organisation a sa propre autorité de certification qui fait office de racine de confiance. Vous devriez ajouter vos organisations si vous rejoignez ou générez un consortium de blockchain. Pour plus de détails sur les autorités de certification, voir la [présentation des composants de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).  

Vous pouvez importer une autorité de certification sur votre console pour différentes raisons. Une fois l'autorité de certification importée, vous pouvez l'utiliser pour ajouter d'autres homologues à l'organisation homologue en cliquant sur **Enregistrer un utilisateur**. Vous pouvez également utiliser l'autorité de certification pour créer d'autres identités admin pour un homologue ou un service de tri.

Pour importer une autorité de certification sur la console {{site.data.keyword.blockchainfull_notm}} Platform et l'utiliser, vous devez avoir préalablement exporté cette autorité de certification depuis une autre console {{site.data.keyword.blockchainfull_notm}} Platform. L'importation d'une autorité de certification vous permet d'enregistrer de nouveaux utilisateurs et d'[inscrire des identités](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

### Avant de commencer
{: #ibp-console-import-ca-before-you-begin}

- Vérifiez que vous avez déjà importé le fichier JSON d'identité admin de l'autorité de certification dans votre portefeuille de console.
- Vérifiez que le fichier JSON d'autorité de certification qui a été exporté depuis la console où il a été créé est disponible.

### Procédure d'importation d'une autorité de certification  

L'importation d'une autorité de certification s'effectue à partir de l'onglet **Noeuds** . Cliquez sur **Ajouter une autorité de certification** `+` dans la section Autorités de certification, puis sélectionnez **Importer une autorité de certification existante**. Suivez les étapes indiquées dans le panneau latéral qui s'affiche pour spécifier les informations de connexion.

- Cliquez sur le bouton **Télécharger JSON** pour omettre la saisie manuelle des informations et accédez au fichier JSON qui a été exporté depuis la console où l'autorité de certification a été créée.
- Définissez l'identité admin de l'autorité de certification en cliquant sur **Identité existante** et en sélectionnant l'identité admin de l'autorité de certification dans votre portefeuille de console.

Vous pouvez aussi importer une autorité de certification et vous pouvez aussi indiquer les données d'identification de l'autorité de certification TLS supplémentaire le cas échéant.  

Une fois que vous avez importé l'autorité de certification sur la console, vous pouvez utiliser votre autorité de certification pour créer de nouvelles identités et générer les certificats nécessaires pour exploiter vos composants et soumettre des transactions au réseau. Pour en savoir plus, voir la section [Gestion des autorités de certification](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-manage-ca).

### Importation d'identités admin dans le portefeuille de console

Chaque composant {{site.data.keyword.blockchainfull_notm}} Platform est déployé avec sa propre clé publique, et le certificat signataire d'un administrateur de composant interne. Lorsque des actions sont effectuées sur le composent par l'administrateur, cette clé publique est associée à la transaction et validée par rapport à la configuration de composant. Les clés permettent à l'administrateur d'exploiter ses composants, notamment pour enregistrer de nouveaux utilisateurs, créer un nouveau canal ou installer des contrats intelligents sur des homologues. Si vous souhaitez utiliser la console pour exploiter un service de tri ou un homologue qui a été créé à partir d'une autre console, vous devez télécharger l'identité admin associée au portefeuille de console. Ensuite, vous pouvez associer le noeud importé avec l'identité du portefeuille de console. Ces identités doivent être exportées depuis la console où elles ont été créées et elles sont obligatoires lors de l'importation du noeud.

Pour importer une nouvelle identité, ouvrez l'onglet **Portefeuille** et cliquez sur **Ajouter une identité**. Cliquez sur **Télécharger JSON** pour rechercher le fichier d'identité JSON qui a été exporté par l'opérateur réseau depuis l'emplacement où a été créé le noeud.

Une fois que vous avez terminé de compléter le panneau **Ajouter une identité** et cliqué sur Soumettre, vous pouvez voir la nouvelle identité admin dans l'écran de présentation du portefeuille. Vous pouvez maintenant faire référence à ces identités lorsque vous importez une autorité de certification, un homologue, ou des composants de service de tri.

## Importation d'un service de tri
{: #ibp-console-import-orderer}

Un noeud de service de tri est le composant de blockchain qui collecte les transactions des membres réseau, trie les transactions et les regroupe dans des blocs. Le service de tri est la liaison commune des consortiums de blockchain et il doit être déployé si vous fondez un consortium que rejoindront d'autres organisations. Pour plus de détails sur les services de tri, voir la [présentation des composants de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

L'importation d'un service de tri sur la console vous permet de créer de nouveaux canaux pour les homologues afin qu'ils puissent effectuer des transactions en privé.

### Avant de commencer
{: #ibp-console-import-orderer-before-you-begin}

- Vérifiez que vous avez déjà importé le fichier JSON d'identité admin du service de tri dans votre portefeuille de console.
- Vérifiez que le fichier JSON de service de tri qui a été exporté depuis la console où il a été créé est disponible.

### Procédure d'importation d'un service de tri
L'importation d'un service de tri s'effectue à partir de l'onglet **Noeuds** . Cliquez sur **Ajouter un service de tri** `+` dans la section Service de tri, puis sélectionnez **Importer un service de tri existant**. Suivez les étapes indiquées dans le panneau latéral qui s'affiche pour spécifier manuellement les informations de connexion.
- Cliquez sur le bouton **Télécharger JSON** pour omettre la saisie manuelle des informations et accédez au fichier JSON qui a été exporté depuis la console où le service de tri a été créé.
- Définissez l'identité admin du service de tri en cliquant sur **Identité existante** et en sélectionnant l'identité admin du service de tri dans votre portefeuille de console.

Une fois que vous avez importé le service de tri sur la console, vous pouvez ajouter de nouveaux membres d'organisation et sélectionner le service de tri lors de la création de nouveaux canaux.

## Importation d'un homologue
{: #ibp-console-import-peer}

Un noeud homologue est un composant de blockchain qui gère un registre et exécute un contrat intelligent pour effectuer des opérations de requête et de mise à jour sur ce registre. Les membres d'une organisation détiennent et gèrent des homologues.  Chaque organisation qui rejoint un consortium doit déployer au moins un homologue. Pour plus de détails sur les homologues, voir la [présentation des composants de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer).  

Une fois que vous avez importé un homologue sur la console, vous pouvez installer des contrats intelligents sur cet homologue et joindre ce dernier à d'autres canaux dans votre blockchain.

**Remarque :** Si vous devez ajouter d'autres homologues à l'organisation homologue ou créer des identités admin supplémentaires pour un homologue, vous devez importer l'autorité de certification de l'homologue puis utiliser cette autorité de certification pour effectuer ces opérations.

### Avant de commencer
{: #ibp-console-import-peer-before-you-begin}

Avant d'importer un homologue, vous devez collecter les informations et certificats suivants :

- Vérifiez que vous avez déjà importé le fichier JSON d'identité admin de l'homologue dans votre portefeuille de console.
- Vérifiez que le fichier JSON d'homologue qui a été exporté depuis la console où il a été créé est disponible.

### Procédure d'importation d'un homologue

L'importation d'un homologue s'effectue à partir de l'onglet **Noeuds** . Cliquez sur **Ajouter un homologue** `+` dans la section Homologues, puis sélectionnez **Importer un homologue existant**. Suivez les étapes indiquées dans le panneau latéral qui s'affiche pour spécifier les informations de connexion.
- Cliquez sur le bouton **Télécharger JSON** pour omettre la saisie manuelle des informations et accédez au fichier JSON qui a été exporté depuis la console où l'homologue a été créé.
- Définissez l'identité admin de l'homologue en cliquant sur **Identité existante** et en sélectionnant l'identité admin de l'homologue dans votre portefeuille de console.

Une fois que vous avez importé l'homologue sur la console, vous pouvez installer des contrats intelligents sur cet homologue et joindre ce dernier à des canaux dans votre blockchain.


<!-- ## Importing an MSP -->

---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Installation, instanciation et mise à jour d'un code blockchain

Le code blockchain est un logiciel, écrit en Go, Java ou Node.js, qui encapsule la logique métier et les instructions transactionnelles
pour la création et la modification des actifs. Il s'exécute dans un conteneur Docker associé à un homologue qui doit interagir avec lui.  Pour plus d'informations sur le développement du code blockchain, voir [Chaincode Tutorials ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

Le code blockchain est installé sur le système de fichiers d'un homologue, puis il est instancié sur un canal. **Tous les membres de canal doivent installer le code blockchain sur chaque homologue qui va exécuter le code blockchain.** Pour utiliser le même code blockchain, les membres du canal doivent lui donner le même nom et la même version lors de l'installation de ce code blockchain. Ensuite, l'étape d'instanciation implique
l'initialisation des paires clé-valeur et le déploiement du conteneur de code blockchain. Tout homologue, via lequel les applications vont interagir avec un code blockchain, doit comporter le code blockchain installé sur son système de fichiers et disposer d'un conteneur de code blockchain opérationnel. **Toutefois, si un homologue utilise le même code blockchain sur plusieurs canaux, il n'a besoin que d'une seule instance du conteneur**.

La **combinaison installation/instanciation** est une fonction puissante car elle permet à un homologue d'interagir avec le même conteneur de code blockchain sur plusieurs canaux. Le seul prérequis est que les fichiers source de code blockchain réel soient installés sur le système de fichiers de l'homologue. De cette façon, si un élément de code blockchain est utilisé sur des douzaines de canaux, un homologue n'aura besoin que d'un seul conteneur de code blockchain pour exécuter des opérations de lecture/écriture sur tous les registres de canal. Cette approche souple se révèle bénéfique pour les performances et le débit de calcul dès lors que l'échelle des réseaux et les applications de code blockchain deviennent plus élaborés.

**Remarque** : Si vous développez votre code blockchain de manière interactive et devez mettre à jour un code blockchain, vous devez répéter les étapes d'installation et d'instanciation pour le code blockchain.


## Installation d'un code blockchain
{: #installchaincode}

Vous devez installer le code blockchain sur chaque homologue qui va exécuter le code blockchain. Pour installer un code blockchain, procédez comme suit :
1. Dans l'écran "Installer le code" de votre moniteur réseau, sélectionnez dans la liste déroulante un homologue sur lequel installer le code blockchain. Cliquez sur le bouton **Installer le code blockchain**.
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. Dans la fenêtre contextuelle **Installer le code blockchain**, entrez le nom et la version de votre code blockchain. **Notez** que les chaînes de nom et de version seront utilisées dans les applications pour interagir avec le code blockchain installé. Cliquez sur le bouton **Parcourir** et parcourez votre code système de fichiers local jusqu'à l'emplacement de stockage de la source de votre code blockchain. Sélectionnez un ou plusieurs fichiers source de code blockchain à installer sur votre homologue. Sélectionnez ensuite votre langage de code blockchain dans le menu déroulant **Type de code blockchain**.

Vous pouvez installer le code blockchain en téléchargeant un ou plusieurs fichiers GO ou NODE , ou encore télécharger le code blockchain dans un fichier .zip. L'utilisation d'un fichier .zip préserve votre code blockchain avec la structure de répertoire complète. Ceci s'avère utile si vous désirez inclure des modules de dépendances, ou utiliser les index avec CouchDB. Pour consulter un exemple d'inclusion d'index avec votre code blockchain, voir
[Using CouchDB from Chaincode![External link icon](../images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html#using-couchdb-from-chaincode){:new_window} dans la documentation Fabric. Vous pouvez également trouver des informations sur la [gestion de dépendances externes pour code blockchain rédigé dans GO![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html#managing-external-dependencies-for-chaincode-written-in-go){:new_window}

  ![Installer le code blockchain](../images/chaincode_install.png "Installer le code blockchain")

## Instanciation d'un code blockchain
Une fois le code blockchain installé sur le système de fichiers de chaque homologue qui rejoint un canal, il est nécessaire de l'instancier sur le canal de sorte que les homologues puissent interagir avec le registre via le conteneur de code blockchain. L'instanciation exécute l'initialisation nécessaire du code blockchain. Cela implique souvent la définition de paires clé-valeur qui comprennent la base de données "world state" initiale d'un code blockchain.

Vous devez posséder les droits **Opérateur** ou **Rédacteur** sur le canal pour pouvoir instancier le code blockchain. Le code blockchain dont le nom et la version sont identiques sur différents homologues doit être instancié une seule fois pour le déploiement du conteneur de code blockchain. Pour instancier un code blockchain, procédez comme suit :
1. Dans l'écran "Installer le code" de votre moniteur réseau, sélectionnez l'homologue sur lequel vous avez installé le code blockchain et localisez le code blockchain que vous voulez instancier dans le tableau de code blockchain. Ensuite, cliquez sur le bouton **Instancier** sous l'en-tête **Action**.
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. Dans la fenêtre contextuelle **Instancier un code blockchain**, définissez les paires clé/valeur en tant qu'arguments pour l'initialisation du code blockchain, puis sélectionnez le canal dans lequel effectuer l'instanciation.  Cliquez sur **Suivant**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

3. Spécifiez la [règle de validation](../glossary.html#endorsement-policy) de votre code blockchain. Plus d'informations sur la définition de règles de validation figurent dans la [section suivante](#specifying-chaincode-endorsement-policies).

## Spécification de règles de validation pour le code blockchain

Vous pouvez utiliser des règles de validation pour spécifier le groupe d'homologues devant valider une nouvelle transaction. Par exemple, une règle de validation peut spécifier qu'une transaction ne sera ajoutée au registre que si une majorité des membres du canal la ratifie.

La règle de validation est définie lorsqu'un code blockchain est instancié sur un canal. L'organisation qui instancie le code blockchain peut sélectionner parmi les membres du canal qui l'ont installé ceux devant faire office de valideurs et définir la règle de validation pour tous les membres du canal. Vous pouvez mettre à jour votre règle de validation en suivant la procédure de la rubrique [Mise à jour de votre code blockchain](#updating-a-chaincode), puis en spécifiant une nouvelle règle lorsque vous instanciez votre code blockchain à l'étape suivante.

Lorsque vous utilisez le Moniteur réseau pour définir votre règle de validation, vous pouvez soit utiliser l'interface utilisateur pour spécifier une **Règle simple**, soit utiliser JSON pour spécifier une **Règle avancée**.

* **Utilisation de l'interface utilisateur pour spécifier une Règle simple :** Cliquez d'abord sur le bouton **Ajouter un membre** pour sélectionner le groupe de membres pouvant valider des transactions. Puis, dans la section **Règle de validation**, indiquez combien de membres dans la liste doivent valider la transaction pour qu'elle soit approuvée. Vous pouvez utiliser cette méthode pour spécifier une règle de validation imposant l'approbation de tous les membres du canal, d'une majorité d'entre-eux, d'un seul membre, ou simplement une règle +1 empêchant les membres de s'auto-valider (deux membres sur cinq par exemple). Si vous n'effectuez pas de modifications, la règle par défaut permet à n'importe quel membre de ratifier une transaction.

  ![Règle de validation simple](../images/simple_endorsement.png "Règle de validation simple")

* **Utilisation de JSON pour spécifier une Règle avancée :** Utilisez des règles avancées pour imposer des validations par des membres importants ou des administrateurs, ou pour affecter à certains membres plus de poids.

  La méthode la plus simple pour définir une règle avancée consiste à générer tout d'abord une règle simple depuis l'écran de l'interface utilisateur. Cliquez ensuite sur le bouton **Règle avancée**, lequel renseigne automatiquement une version JSON de la règle avec les mêmes membres et règles que ceux que vous avez définis dans la règle simple. Vous pouvez ensuite éditer le code JSON pour composer une version plus avancée. Pour plus d'informations sur la composition de règles de validation dans JSON, reportez-vous à la [documentation du SDK Hyperledger Fabric Node![External link icon](../images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest). <!--You can also find examples of advanced endorsement policies in the main [Hyperledger Fabric documentation![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/arch-deep-dive.html#example-endorsement-policies)-->

  ![Règle de validation avancée](../images/advanced_endorsement.png "Règle de validation avancée")

## Mise à jour d'un code blockchain

Vous pouvez mettre à jour un code blockchain afin de modifier la programmation du code blockchain tout en conservant sa relation aux actifs dans le registre. En raison de la combinaison installation et instanciation, vous devez mettre à jour le code blockchain sur tous les homologues qui se trouvent sur le canal avec ce code blockchain. Pour mettre à jour un code blockchain, procédez comme suit.

1. Installez un code blockchain portant le même nom que votre ancien code blockchain, mais avec une version différente. Vous pouvez suivre les mêmes étapes que celles de la section [Installation d'un code blockchain](install_instatiate_chaincode.html#Installing a chaincode). Vérifiez que vous sélectionnez le même canal que votre code blockchain d'origine.

  ![Mettre à jour le code blockchain](../images/upgrade_chaincode.png "Mettre à jour le code blockchain")

2. Localisez votre nouveau code blockchain dans le tableau et cliquez sur le bouton **Mettre à jour** sous l'en-tête **Action**. Cette action réinstancie votre code blockchain et remplace son conteneur par un nouveau. Notez que vous n'avez pas besoin d'entrer de nouveaux arguments dans le cadre de la fonction de mise à jour.

  ![Bouton Mettre à jour](../images/upgrade_button.png "Bouton Mettre à jour")

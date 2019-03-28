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

# Création d'applications
{: #ibp-console-app}

Après avoir installé des contrats intelligents et déployé vos noeuds, vous pouvez utiliser des applications client pour effectuer des transactions avec d'autres membres de votre réseau. Les applications peuvent appeler la logique métier contenue dans les contrats intelligents pour créer, transférer ou mettre à jour des ressources sur le registre de blockchain. Utilisez ce tutoriel pour apprendre comment utiliser des applications client pour interagir avec des réseaux que vous gérez à partir de la console {{site.data.keyword.blockchainfull}} Platform.
{:shortdesc}

**Public cible :** Cette rubrique s'adresse aux développeurs d'application qui souhaitent apprendre à créer une application client qui interagit avec un réseau de blockchain.

## Ressources de formation
{: #ibp-console-app-learning-resources}

Pour en savoir plus sur la manière dont les applications et les contrats intelligents collaborent, voir la [rubrique relative au développement d'applications ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "développement d'applications") dans la documentation Hyperledger Fabric. Cette rubrique explore un cas
d'utilisation hypothétique dans lequel des banques et des entreprises échangent des documents commerciaux et elle explique comment les transactions sont validées dans des contrats intelligents. Le [tutoriel Document commercial ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "tutoriel Document commercial") développe cette rubrique en vous permettant de déployer les contrats intelligents sur un réseau Fabric et d'utiliser un modèle de code d'application pour créer et transférer le document commercial entre les membres du réseau.

**Modèles d'application et tutoriels**

|  Exemple  |  Description    |  Difficulté    | Langue | Emplacement |
| -----------------|---------|---------|---------|
| [FabCar ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/patterns/write-a-smart-contract-for-the-fabcarcommercial-paper-or-iks-cluster-with-saas-v2-beta-network-think/ "Blockchain")| Utiliser un cluster IKS pour déployer un contrat intelligent de réseau Fabric simple sur Blockchain Platform. | Intermédiaire | Node.js | Documentation DeveloperWorks|
| [Document commercial ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Document commercial")| Utiliser le contrat intelligent de document commercial pour échanger sur une instance locale d'Hyperledger Fabric. | Intermédiaire | Node.js | Documentation Hyperledger Fabric|
| **Bientôt disponible :** Document commercial avancé  | Modèle avancé comportant de meilleures pratiques en termes de propriété et de confidentialité avec l'utilisation de collection de données et d'une adhésion basée sur l'état. | Avancé | Node.js | Documentation Hyperledger Fabric|
| [Exécuter le contrat intelligent d'un document commercial avec l'extension {{site.data.keyword.blockchainfull_notm}} VSCode ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/tutorials/run-commercial-paper-smart-contract-with-ibm-blockchain-vscode-extension/ "Exécuter le contrat intelligent d'un document commercial avec VSCode")|  Apprendre à utiliser l'extension VScode pour installer et appeler le contrat intelligent de document commercial sur une instance locale d'Hyperledger Fabric. | Intermédiaire | Node.js | IBM Developer|
| [Exécuter l'exemple Document commercial dans la version 2.0 bêta gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)| Apprendre à déployer et à appeler le contrat Document commercial sur un réseau {{site.data.keyword.blockchainfull_notm}} Platform 2.0. | Intermédiaire |  Node.js | Documentation {{site.data.keyword.blockchainfull_notm}} Platform|  

Lors du développement d'une application, une coordination peut être nécessaire entre deux utilisateurs distincts de votre réseau, l'opérateur réseau et le développeur d'applications :
- **L'opérateur réseau ** est l'administrateur qui utilise la console {{site.data.keyword.blockchainfull_notm}} Platform afin de déployer les noeuds de votre organisation et il installe les contrats intelligents sur votre réseau.
- **Le développeur d'applications ** génère l'application client qui sera consommée par les utilisateurs finaux. Le développeur utilise les [Logiciels SDK Hyperledger Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Logiciels SDK Hyperledger Fabric"){:new_window} pour appeler les transactions écrites dans les contrats intelligents.

Si vous êtes l'**opérateur réseau**, vous devrez effectuer les étapes suivantes pour que le développeur d'applications puisse interagir avec votre réseau :
1. Utilisez l'autorité de certification de votre organisation pour [enregistrer une identité d'application](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities).
2. [Téléchargez le profil de connexion](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) depuis le panneau des contrats intelligents.
3. Envoyez au développeur d'applications les objets et informations suivants :
  - ID d'inscription et secret de l'identité d'application.
  - Profil de connexion.
  - Nom du contrat intelligent.
  - Nom du canal sur lequel le contrat intelligent a été instancié.  

Si vous êtes le **développeur d'applications**, utilisez les informations fournies par l'opérateur réseau pour effectuer les étapes suivantes :
1. Générer une paire de clés publiques et privées à l'aide de l'ID d'inscription et du secret de l'identité d'application et des informations de noeud final d'autorité de certification contenues dans votre profil de connexion.
2. Utiliser le profil de connexion, le nom de canal, le nom de contrat intelligent et les clés d'application pour appeler le contrat intelligent.  

Le développeur d'applications peut utiliser deux modèles de programmation pour interagir avec le réseau :

**API de logiciel SDK Fabric de niveau supérieur**

A compter de la version 1.4 de Fabric, les utilisateurs peuvent bénéficier d'une application simplifiée et d'un modèle de programmation de contrat intelligent. Le nouveau modèle réduit le nombre d'étapes et le volume de code nécessaires pour soumettre une transaction. Ce modèle est uniquement pris en charge pour les applications écrites en **Node.js**. Si vous souhaitez bénéficier du nouveau modèle, vous pouvez utiliser ce tutoriel pour effectuer les actions suivantes sur un réseau {{site.data.keyword.blockchainfull_notm}} Platform 2.0 :

- [Générez des certificats pour votre application](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll) à l'aide du logiciel SDK.
- [Appelez un contrat intelligent depuis le logiciel SDK](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke).
- Utilisez l'[extension IBM Blockchain VScode](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) pour écrire, déployer, puis développer et tester de manière itérative vos contrats intelligents.
- Apprenez-en davantage sur le développement d'application en déployant le [tutoriel Document commercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) sur les noeuds gérés depuis votre console. Ce tutoriel fournit davantage d'informations sur l'utilisation des portefeuilles et passerelles Fabric.


**API de logiciel SDK Fabric de niveau inférieur**

Si vous voulez continuer à utiliser votre contrat intelligent et votre code d'application existants, ou utiliser d'autres langages de logiciel Fabric fournis par la communauté Hyperledger, vous pouvez utiliser les  [API de logiciel SDK Fabric de niveau inférieur](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level) pour la connexion à votre réseau.

## Enregistrement d'une identité d'application 
{: #ibp-console-app-identities}

Les applications doivent signer les transactions qu'elles soumettent aux noeuds {{site.data.keyword.blockchainfull_notm}}, et elles doivent joindre une clé publique qui est utilisée par les noeuds pour vérifier que les transactions sont envoyées à la partie appropriée. Cela garantit que les transactions sont soumises par les organisations qui ont le droit de participer.

L'opérateur réseau doit utiliser l'autorité de certification de l'organisation pour enregistrer une identité d'application, laquelle peut ensuite être utilisée par le développeur d'applications pour générer une clé publique et privée. L'opérateur peut indiquer l'ID d'inscription et le secret de l'identité, ainsi que les informations de noeud final de l'autorité de certification, qui doivent être utilisées par le logiciel SDK pour générer des certificats. Avec l'inscription côté client, le développeur d'applications garantit qu'aucune autre partie n'a accès à la clé privée de l'application. Pour renforcer la sécurité, l'opérateur réseau peut définir une limite d'inscription sur 1 pendant l'enregistrement. Une fois le développeur d'applications inscrit, l'ID inscription et le secret ne peuvent pas être utilisés pour générer une autre clé privée.

Si vous vous préoccupez moins de la sécurité, l'opérateur réseau peut inscrire une identité d'application à partir de l'[onglet Autorité de certification](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll). L'opérateur peut ensuite télécharger l'identité ou l'exporter dans le portefeuille de console. Pour qu'il soit possible d'utiliser les certificats du logiciel SDK, les clés doivent être décodées de base64 au format PEM. Vous pouvez décoder les certificats en exécutant la commande suivante sur votre machine locale :

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

## Téléchargement de votre profil de connexion
{: #ibp-console-app-profile}

Les applications peuvent soumettre des transactions uniquement pour les contrats intelligents qui ont été instanciés sur les canaux. Par conséquent, les informations nécessaires à la connexion pour interagir avec un contrat intelligent figurent dans la liste des contrats intelligents instanciés sur votre console. Cela signifie que vous devez avoir déjà installé et instancié votre contrat intelligent.

Hyperledger Fabric [Flux de transactions![Icône de lien externe](../images/external_link.svg "Icône de lien externe")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Flux de transactions"){:new_window} s'étend sur plusieurs composants, les applications client collectant les adhésions des homologues et envoyant les transactions validées au service de tri. Le profil de connexion fournit à votre application les noeuds finaux des homologues et les noeuds de service de tri auxquels il doit soumettre une transaction. Il contient également des informations sur votre organisation, comme vos autorités de certification et votre ID MSP. Les logiciels SDK Fabric peuvent lire le profil de connexion directement, sans avoir à écrire du code qui gère le flux de transaction et de validation.

Si vous avez configuré des homologues d'ancrage lors de la création d'un canal, vous pouvez bénéficier de la fonction [Reconnaissance de service ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Reconnaissance de service") d'Hyperledger Fabric. La reconnaissance de service permet à votre application de détecter les homologues sur le canal à l'extérieur de votre organisation qui doivent valider une transaction. Sans la reconnaissance de service, vous devez obtenir les informations de noeud final de ces homologues hors bande à partir d'autres organisations et les ajouter à votre profil de connexion. Pour plus d'informations sur la configuration des homologues d'ancrage, voir l'étape 3 de la [rubrique relative aux données privées](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) dans le tutoriel Déployer un contrat intelligent.

Accédez à l'onglet Contrats intelligents sur la console de votre plateforme. En regard de chaque contrat intelligent instancié, accédez au menu déroulant dynamique. Cliquez sur le bouton nommé **Se connecter à l'aide de SDK**. Un panneau latéral s'affiche qui vous permet de générer et de télécharger votre profil de connexion. Tout d'abord, vous devez sélectionner l'autorité de certification de votre organisation que vous avez utilisé pour enregistrer votre identité d'application. Vous devrez également sélectionner la définition MSP de votre organisation. Vous pourrez ensuite télécharger le profil de connexion que vous pouvez utiliser pour générer des certificats et appeler le contrat intelligent.

## Inscription à l'aide du logiciel SDK
{: #ibp-console-app-enroll}

Une fois que l'opérateur réseau a fournit l'ID inscription et le secret de l'identité d'application et du profil de connexion, un développeur d'applications peut utiliser les logiciels SDK Fabric ou le client d'autorité de certification Fabric pour générer des certificats côté client. Vous pouvez utiliser la procédure suivante pour inscrire une identité d'application à l'aide du logiciel SDK [logiciel SDK Fabric pour Node.js ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Fabric pour Node.js").

1. Sauvegardez le profil de connexion sur votre machine locale et renommez-le `connection.json`.
2. Sauvegardez le bloc de code suivant sous `enrollUser.js` dans le même répertoire que votre profil de connexion:

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Create a new CA client for interacting with the CA.
      const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
      const ca = new FabricCAServices(caURL);

      // Create a new file system based wallet for managing identities.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Check to see if we've already enrolled the admin user.
      const userExists = await wallet.exists('user1');
      if (userExists) {
        console.log('An identity for "user1" already exists in the wallet');
        return;
      }

      // Enroll the admin user, and import the new identity into the wallet.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. Editez `enrollUser.js` afin de remplace les valeurs suivantes :
  - Remplacez ``<CA_Name>`` par le nom de l'autorité de certification de vitre organisation. Vous pouvez trouver le nom de votre autorité de certification dans la section "organizations" de votre profil de connexion sous "Certificate Authorities". N'utilisez pas le "caName" dans la section "Certificate Authorities".
  - Remplacez ``<app_enroll_id>`` par l'ID d'inscription de l'application fourni par votre opérateur réseau.
  - Remplacez ``<app_enroll_secret>`` par le secret d'inscrption de l'application fourni par votre opérateur réseau.
  - Remplacez ``<msp_id>``par l'ID MSP de votre organisation. Vous pouvez trouver votre ID MSP sous la section "organizations" de votre profil de connexion.
4. Accédez à `enrollUser.js` à partir d'un terminal et exécutez `node enrollUser.js`. Si la commande s'exécute correctement, vous devez voir le résultat suivant :

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
  Le logiciel SDK doit créer et stocker vos certificats dans le répertoire `wallet/user1/` créé par la commande. Ce répertoire est le système de fichiers que le portefeuille a utilisé pour soumettre de futures transactions.

Les portefeuilles utilisés par les logiciels SDK Fabric sont différents du portefeuille sur la console {{site.data.keyword.blockchainfull_notm}} Platform. Les identités stockées dans votre portefeuille de console ne peut pas être utilisées directement par le logiciel SDK.
{: note}

## Appel d'un contrat intelligent avec le logiciel SDK
{: #ibp-console-app-invoke}

Une fois que vous avez généré l'application la clé publique et privée de l'application et que vous les avez stockées dans un portefeuille, vous êtes prêt à soumettre une transaction. Vous devez connaître le nom du contrat intelligent et le nom du canal sur lequel il a été instancié. Vous pouvez utiliser la procédure ci-dessous pour appeler un contrat à l'aide du [Logiciel SDK Fabric pour Node.js ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Fabric pour Node.js").


1. Sauvegardez le fichier ci-dessous sur votre machine locale sous `invoke.js`. Sauvegardez le fichier dans le même répertoire que `enrollUser.js`

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Parse the connection profile. This would be the path to the file downloaded
        // from the IBM Blockchain Platform operational console.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Configure a wallet. This wallet must already be primed with an identity that
        // the application can use to interact with the peer node.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Create a new gateway, and connect to the gateway peer node(s). The identity
        // specified must already exist in the specified wallet.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {enabled: true, asLocalhost:false }});

        // Get the network channel that the smart contract is deployed to.
        const network = await gateway.getNetwork('<channel_name>');

        // Get the smart contract from the network channel.
        const contract = network.getContract('<smart_contract_name>');

        // Submit the 'createCar' transaction to the smart contract, and wait for it
        // to be committed to the ledger.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. Editez `invoke.js` afin de remplace les valeurs suivantes :
  - Remplacez ``<channel_name>`` par le nom du canal sur lequel le contrat intelligent a été instancié. Vous pouvez trouver le nom de votre autorité de certification sous la section "Certificate Authorities" de votre profil de connexion.
  - Remplacez ``<smart_contract_name>`` par le nom du contrat intelligent installé. Vous pouvez obtenir cette valeur auprès de votre opérateur réseau.
  - Editez le contenu de `submitTransaction` afin d'appeler une fonction au sein de votre contrat intelligent. Le fichier `invoke.js` est écrit pour appeler le [Contrat intelligent fabcar ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar). Si vous souhaitez exécuter le fichier ci-dessous pour soumettre une transaction, installez fabcar et instanciez le contrat intelligent sur l'un de vos canaux.

3. Accédez à `invoke.js` à partir d'un terminal et exécutez `node invoke.js`. Si la commande s'exécute correctement, vous devez voir le résultat suivant :

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  Si vous accédez à votre canal à partir de la console, vous pourrez voir un autre bloc ajouté par la transaction.


## Connexion avec l'extension {{site.data.keyword.blockchainfull_notm}} VScode 
{: #ibp-console-app-vscode}

L'extension Visual Studio Code de {{site.data.keyword.blockchainfull_notm}} Platform fournit un environnement au sein de Visual Studio Code pour le développement, le packaging et le déploiement de packages de contrat intelligent. Assurez-vous d'avoir votre profil de connexion et l'ensemble des fichiers de clés générés à l'aide de votre autorité de certification. Vous pouvez ensuite utiliser l'extension VScode pour la connexion à un réseau que vous gérez depuis votre console.

Suivez les instructions de téléchargement de l'[extension VScode ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "Extension VScode") sur la place du marché du code visual studio. Une fois l'extension installée, ouvrez VScode. Vous pouvez accéder à l'extension en cliquant sur **View > Command Palette**. Entrez la commande *IBM Blockchain Platform: Create Smart Contract Project* pour créer un nouveau projet de contrat intelligent.

Une fois que vous avez créé le projet, vous pouvez vous connecter directement à votre réseau à partir du code Visual Studio en utilisant les informations de votre contrat intelligent instancié depuis votre console. Utilisez le tableau **Contrats intelligents instanciés** sous l'onglet Contrats intelligents pour télécharger votre [profil de connexion](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) sur votre système de fichiers local. Ensuite, [créez une identité d'application](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) et utilisez votre autorité de certification pour créer et télécharger un certificat (clé publique) et une clé privée. Suivez les instructions ci-dessus pour convertir la clé privée et le certificat au format PEM. Une fois que vous avez téléchargé votre profil de connexion et les clés, procédez comme suit pour vous connecter à votre réseau.

1. Ouvrez l'onglet _{{site.data.keyword.blockchainfull_notm}} Platform_ dans Visual Studio Code.
2. Dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_, cliquez sur **Ajouter nouvelle connexion**.
3. Entrez un nom pour la connexion. Ce nom sera affiché dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_.
4. Entrez le chemin d'accès complet à votre profil de connexion.
5. Entrez le chemin d'accès complet à votre certificat (clé publique) au format PEM.
6. Entrez le chemin complet à la clé privée au format PEM.
7. Votre connexion doit maintenant apparaître dans la liste des connexions sous `local_fabric`. Cliquez deux fois sur le nom de la connexion pour vous connecter.

Une fois que vous êtes connecté à partir de VScode, vous pourrez voir la liste des homologues de votre organisation et les canaux qu'elles ont rejoint dans le volet des connexions de blockchain. Vous pourrez voir la liste des contrats intelligents installés sous chacun des homologues. Pour installer un nouveau contrat intelligent à partir de votre projet sur votre réseau, cliquez tout d'abord avec le bouton droit sur un homologue et sélectionnez **Installer un contrat intelligent**. Vous pouvez ensuite instancier ce contrat intelligent en effectuant un clic droit sur un canal et en sélectionnant **Installer un contrat intelligent**. Pour en apprendre plus sur l'utilisation de l'extension {{site.data.keyword.blockchainfull_notm}} Platform, consultez la documentation sur la [place du marché du code Visual Studio![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "Extension VScode").


## Utilisation de l'exemple Document commercial
{: #ibp-console-app-commercial-paper}

Le [tutoriel Document commercial ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Tutoriel Document commercial") dans la documentation Hyperledger Fabric guide les développeurs au sein d'un cas d'utilisation dans lequel plusieurs parties achètent, vendent et échangent un document commercial. Il développe la [rubrique relative au développement d'applications ![Icône de lien  externe](../images/external_link.svg "Icône de lien  externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Développement d'applications") en fournissant un modèle de contrat intelligent et de code d'application qui vous permettent de créer et d'échanger des ressources dans une instance locale de Fabric.

Vous pouvez également déployer l'exemple de code du tutoriel Document commercial sir un réseau {{site.data.keyword.blockchainfull_notm}} Platform 2.0. Vous pouvez ainsi rapidement vous initier à l'interaction avec votre réseau et utiliser l'exemple pour télécharger les dépendances nécessaires. L'exemple de code comporte également des exemples d'importation de certificats dans un [portefeuille ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Portefeuille") et utiliser votre profil de connexion pour générer une [passerelle Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Passerelle Fabric"). Le tutoriel peut être utilisé par deux organisations différentes pour effectuer différentes opérations avec une seule ressource. Si vous avez utilisé le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) pour déployer deux organisations homologues connectées à un canal, vous pouvez interagir avec le tutoriel avec les deux organisations.

Suivez les étapes ci-dessous pour déployer l'exemple sur votre réseau. Vous pouvez passer en revue le tutoriel dans la rubrique [Tutoriel Document commercial ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Tutoriel Document commercial") de la documentation Fabric pour plus de détails sur les contrats intelligents et la structure d'application.

### Prérequis

Avant de déployer l'exemple de document commercial, vous devez installer les outils requis sur votre machine locale :
  * [Git ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

Vous devrez également utiliser un éditeur de texte pour éditer et sauvegarder les fichiers dans l'exemple. Vous pouvez utilisez l'un des nombreux éditeurs gratuits de grande qualité, par exemple [Atom ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://atom.io/ "atom"), [Sublime text ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://www.sublimetext.com/ "Git") ou [Brackets ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://brackets.io/ "Brackets").

### Etape 1 : Télécharger l'exemple

Vous téléchargez le document commercial en clonant le [référentiel d'exemples Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-samples "Référentiel d'exemples Fabric"):

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

Une fois que vous avez téléchargé les exemples Fabric, exécutez les commandes suivantes pour vérifier que vous utilisez la version d'exemples compatible avec la version 4.1 de Fabric.

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

Vous trouverez l'exemple dans le dossier `commercial paper` de `fabric-samples`.

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

Le tutoriel contient deux exemples d'organisations, **magnetocorp** et **digibank**. Chaque organisation a son propre exemple de code d'application, stocké dans deux dossiers distincts dans le répertoire `organisation` .

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

Au cours de ce tutoriel, vous allez d'abord utiliser le code d'application magnetocorp pour émettre un document commercial. Vous pouvez ensuite utiliser le code d'application digibank pour acheter et échanger le document. Les deux répertoires contiennent le même contrat intelligent.

Accédez ai code d'application dans le répertoire magnetocorp.

```
cd magnetocorp/application
```
{:codeblock}

Une fois que vous êtes dans le répertoire, vous pouvez télécharger les dépendances d'applicaiton en exécutant la commande suivante :

```
npm install
```
{:codeblock}

### Etape 2 : Installer et instancier le contrat intelligent

Vous pouvez trouver le contrat intelligent du document commercial dans le dossier `contract` du répertoire `digibank` et `magnetocorp`. Vous devez installer ce contrat intelligent sur tous les homologues des organisations utilisant le tutoriel. Vous devrez ensuite instancier le contrat du document commercial sur un canal. Le contrat intelligent doit être packagé au [format .cds![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "Packaging des contrats intelligents") pour pouvoir être installé à partir de la console.

Vous pouvez utiliser l'[extension IBM Blockchain VScode](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) pour packager le contrat intelligent. Une fois l'extension installée, utilisez Visual Studio pour ouvrir le dossier `contracts` dans votre espace de travail. Accédez à l'onglet _{{site.data.keyword.blockchainfull_notm}} Platform_. Dans le panneau _{{site.data.keyword.blockchainfull_notm}} Platform_, accédez à la section des packages de contrat intelligent et cliquez sur **Package a Smart Contract Project**. L'extension VScode utilisera les fichiers du dossier `contracts` pour créer un package nommé `papernet-js@.0.0.1.cds`. Cliquez avec le bouton droit afin de l'exporter vers votre système de fichiers local. Vous pouvez ensuite utiliser votre console pour [installer les contrats intelligents sur vos homologues](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install), puis [instancier le contrat intelligent sur un canal](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).

### Etape 3 : Générer des certificats pour votre portefeuille

Les applications doivent signer les demandes qu'elles envoient aux composants Fabric. Si les composants ne reconnaissent pas les organisations qui soumettent les transactions, celles-ci seront rejetées et renvoyées avec une erreur. L'exemple de document commercial crée un portefeuille de système de fichiers qui va stocker vos certificats et signer vos transactions. Pour plus d'informations sur l'utilisation des portefeuilles par les applications, voir la rubrique [Portefeuille ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Portefeuille") dans la documentation Fabric. Les portefeuilles utilisés par les logiciels SDK Fabric sont différents du portefeuille sur la console {{site.data.keyword.blockchainfull_notm}} Platform. Les identités stockées dans votre portefeuille de console ne peut pas être utilisées directement par le logiciel SDK.

L'exemple d'origine utilise le fichier `addToWallet.js` pour créer un portefeuille de système de fichiers qui utilise des certificats du dossier d'exemples de Fabric. Nous allons créer un nouveau fichier qui utilise le logiciel SDK pour générer des certificats côté client et stocker ces derniers directement dans un nouveau portefeuille.

Choisissez l'autorité de certification de l'organisation que vous voulez utiliser dans le tutoriel en tant que magnetocorp. Par exemple, vous pouvez utiliser Org1 si vous avez terminé le tutoriel Générer un réseau. Utilisez l'autorité de certification pour [créer une identité d'application](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities). **Sauvegardez** l'ID d'inscription et le secret.

Utilisez votre console pour [télécharger votre profil de connexion](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Sauvegardez le profil de connexion sur votre système de fichiers local et renommez-le `connection.json`. Ensuite, utilisez la commande suivante pour déplacer le profil de connexion dans un répertoire où il sera référencé par des commandes futures.

  ```
  mv $HOME/<path_to_creds>/connection.json /gateway/connection.json
  ```
  {:codeblock}

Accédez au répertoire `/magnetocorp/application` et sauvegardez le bloc de code suivant sous `enrollUser.js`.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Create a new CA client for interacting with the CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Create a new file system based wallet for managing identities.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Check to see if we've already enrolled the admin user.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Enroll the admin user, and import the new identity into the wallet.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

Nous devons prendre le temps d'étudier comment fonctionne ce fichier avant de l'éditer. Tout d'abord, `enrollUser.js` importe les classes `FileSystemWallet` et `X509WalletMixin` depuis la bibliothèque `fabric-network`.

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

Le fichier utilise ensuite la classe `FileSystemWallet` pour générer un portefeuille sur votre système de fichiers local. Vous pouvez éditer la ligne ci-dessous pour stocker le portefeuille dans un autre emplacement.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

Une fois le portefeuille créé, le fragment de code utilise l'ID d'inscrîption et le secret pour l'inscription à l'aide de l'autorité de certification de votre organisation. Il crée ensuite une identité pour la paire de clés publiques et privées et les importe dans le portefeuille. Notez comment le fichier transmet l'IF MSP de votre organisation dans le portefeuille également.

```
// Enroll the admin user, and import the new identity into the wallet.
const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

**Editez ** `enrollUser.js` afin de remplacer les valeurs suivantes :
- Remplacez `'<CA_Name>'` par le nom de l'autorité de certification de vos organisations. Vous pouvez trouver le nom de votre autorité de certification dans la section "organizations" de votre profil de connexion sous "Certificate Authorities". N'utilisez pas le "caName" dans la section "Certificate Authorities".
- Remplacez `'<app_enroll_id>` par l'ID d'inscription de l'application fourni par votre opérateur réseau.
- Remplacez `'<app_enroll_secret>'` par le secret d'inscription de l'application fourni par votre opérateur réseau.
- Remplacez `'<msp_id>'` par l'ID MSP de votre organisation. Vous pouvez trouver cet ID MSP sous la section "organizations" de votre profil de connexion.

Sauvegardez `enrollUser.js` et fermez-le. Dans le répertoire `magnetocorp`, exécutez la commande suivante :
```
node enrollUser.js
```
{:codeblock}

Lorsque la commande s'exécute correctement, vous devez voir le résultat suivant :

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

Vous pouvez trouver le portefeuille qui a été créé dans le dossier `identité` du répertoire `magnetocorp`.

### Etape 4 : Utiliser le profil de connexion pour générer une passerelle Fabric

Hyperledger Fabric [Flux de transactions![Icône de lien externe](../images/external_link.svg "Icône de lien externe")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Flux de transactions"){:new_window} s'étend sur plusieurs composants, les applications client jouant un rôle unique. Votre application doit se connecter aux homologues qui doivent valider la transaction et au service de tri qui va trier la transaction et l'ajouter dans un bloc. Vous pouvez fournir les noeuds finaux de ces noeuds à votre application en utilisant votre profil de connexion pour construire une passerelle Fabric. La Passerelle mène ensuite les interactions de faible niveau avec votre réseau Fabric. Pour en savoir plus, voir la rubrique relative à la [passerelle Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Passerelle Fabric") dans la documentation Fabric.

Vous avez déjà téléchargé votre profil de connexion et vous l'avez utilisé pour la connexion à l'autorité de certification de votre organisation. Nous allons maintenant utiliser le profil de connexion pour générer une passerelle.

Ouvrez le fichier `issue.js` dans un éditeur de texte. Avant d'éditer le fichier, notez qu'il importe les classes `FileSystemWallet` et `Gateway` depuis la bibliothèque fabric-network.

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

La classe `Gateway` est utilisée pour construire une passerelle que vous allez utiliser pour soumettre votre transaction.

```
const gateway = new Gateway()
```
{:codeblock}

La classe `FileSystemWallet` est utilisée pour charger le portefeuille que vous avez créé à l'étape précédente. **Editez** la ligne ci-dessous si vous avez modifié l'emplacement du portefeuille sur votre système de fichiers.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

Une fois votre portefeuille importé, utilisez le code suivant pour transmettre votre profil de connexion et votre portefeuille à la nouvelle passerelle. Vous devrez apporter les **Modifications** suivantes au code afin qu'il soit similaire au fragment de code ci-dessous. Les lignes qui impriment les journaux ont été retirées afin de diminuer le volume de code.
- Mettez à jour la section `userName` afin qu'elle corresponde à la valeur que vous avez sélectionnée pour votre `identityLabel` dans `enrollUser.js`. 
- Mettez à jour les options de reconnaissance pour bénéficier de la reconnaissance de service sur votre réseau. Définissez `discovery: { enabled: true, asLocalhost: false }`.  
- Mettez à jour la section relative à l'importation de votre profil de connexion. Le profil de connexion de la console est un fichier au format JSON et non un fichier YAML.   

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const ccp = JSON.parse(ccpJSON);

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

Ce fragment de code utilise la passerelle pour ouvrir les connexions GRPC à l'homologue et aux noeuds de service de tri, ainsi que pour interagir avec votre réseau.

### Etape 5 : Appel du contrat intelligent

Après avoir configuré la passerelle pour la connexion au réseau géré par votre console, nous allons éditer la partie du fichier `issue.js` qui définit la connexion au contrat intelligent du document commercial. Vous devrez indiquer à la passerelle le nom du contrat et le canal sur lequel vous avez instancié le contrat intelligent.

**Editez** la ligne ci-dessous, en remplaçant `mychannel` par le nom de votre canal.

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

A la suite d'une ligne de code qui imprime un message sur votre console, vous pouvez voir une ligne qui indique à la passerelle le nom du contrat intelligent. **Editez** la ligne ci-dessous afin de remplacer le nom `papercontract` par le nom du contrat que vous avez installé.

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

La passerelle dispose à présent de toutes les informations dont elle a besoin pour soumettre une transaction. La ligne suivante appelle la fonction `issue` dans le contrat du document commercial, avec les arguments qui définissent la nouvelle ressource de document commercial.

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

Après avoir soumis la transaction à l'aide de la passerelle, le fichier `issue.js` désactive la connexion de passerelle.

```
gateway.disconnect();
```
{:codeblock}

Cette commande ferme les connexions GRPC ouvertes par votre passerelle. La fermeture des connexions permet d'économiser des ressources réseau et d'améliorer les performances.

Une fois les éditions de cette étape et de l'**étape 4** effectuées, sauvegardez `issue.js` et fermez-le. Soumettez la transaction qui crée le nouveau document commercial à l'aide de la commande suivante :

```
node issue.js
```
{:codeblock}

Si la transaction aboutit, vous devez voir la sortie suivante sur votre terminal :

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### Etape 6 : Utiliser l'exemple digibank

Après avoir utilisé le document commercial avec l'exemple magnetocorp, vous pouvez acheter et échanger ce document commercial avec l'exemple de tutoriel digibank. Vous pouvez utiliser le code d'application digibank avec la même organisation que magnetocorp, ou utiliser l'autorité de certification, les homologues et le profil de connexion d'une autre organisation. Si vous avez terminé le  [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), c'est l'occasion d'utiliser le tutoriel avec Org2.

Accédez au répertoire `digibank/application`. Vous pouvez suivre les instructions de l'**étape 3** pour créer et générer les certificats et le portefeuille qui vont signer la transaction avec digibank. Vous pouvez ensuite utiliser le fichier `buy.js` pour acheter le document commercial auprès de magnetocorp, puis utiliser le fichier `redeem.js` pour échanger le document. Vous pouvez suivre l'**étape 4** et l'**étape 5** pour éditer ces fichiers de sorte qu'ils pointent sur le profil de connexion, le canal et le contrat intelligent corrects.

## Connexion au réseau à l'aide des API de logiciel SDK Fabric de niveau inférieur
{: #ibp-console-app-low-level}

Si vous souhaitez conserver votre code de l'application existant, ou encore utiliser des logiciels SDK Fabric pour des langages autres que Node.js, vous pouvez encore vous connecter à votre réseau à l'aide des API de logiciel SDK Fabric de niveau inférieur. Utilisez la console pour [télécharger votre profil de connexion](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Vous pouvez ensuite importer les noeuds finaux des noeuds homologues et des noeuds de service de tri de votre canal directement depuis le profil de connexion, ou utiliser les informations de noeud final pour ajouter manuellement des objets homologue et service de tri. Vous devrez également utiliser votre autorité de certification pour [créer une identité d'application](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities), puis utiliser les informations de noeud final de l'autorité de certification pour l'enregistrement côté client, ou générer des certificats à partir de votre console.

La documentation relative aux [Logiciels SDK Node Fabric ![Icône de lien externe](../images/external_link.svg "External link icon ")](https://fabric-sdk-node.github.io "Logiciels SDK Node Fabric") fournit un tutoriel relatif à la [connexion à votre réseau à l'aide d'un profil de connexion ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-network-config.html "Tutoriel Profil"){:new_window}. Ce tutoriel utilise les informations de noeud final de l'autorité de certification dans votre profil de connexion pour générer des clés avec le logiciel SDK. Vous pouvez également utiliser votre console pour générer une clé publique et une clé privée et convertir les clés au format PEM. Vous pouvez ensuite définir un contexte utilisateur en transmettant vos clés directement à la [Classe client Fabric ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Client.html "Classe client Fabric") des logiciels SDK à l'aide du code ci-dessous :

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

Si vous utiliser des API de logiciel SDK de niveau inférieur pour la connexion à votre réseau, des étapes supplémentaires sont nécessaires pour gérer les performances et la disponibilité de votre application. Pour plus d'informations, voir [Meilleures pratiques pour la connectivité et la disponibilité des applications](/docs/services/blockchain/v10_application.html#dev-app-connectivity-availability).

## Utilisation des index avec CouchDB
{: #console-app-couchdb}

Si vous utilisez CouchDB comme base de données d'état, vous pouvez exécuter des requêtes de données JSON depuis vos contrats intelligents sur les données d'état du canal. Nous vous recommandons fortement de créer des index pour vos requêtes JSON et de les utiliser dans les contrats intelligents. Les index permettent à vos applications d'extraire les données de manière aussi efficace que votre réseau ajoute des blocs de transaction et des entrées supplémentaires dans le World State. Pour savoir comment utiliser les index avec vos contrats intelligents et vos applications, voir [Meilleures pratiques lors de l'utilisation de CouchDB](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices).

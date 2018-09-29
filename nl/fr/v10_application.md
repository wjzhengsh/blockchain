---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Développement d'applications
{: #dev_app}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform fournit des API que vous pouvez utiliser pour connecter des applications à votre réseau de blockchain. Vous pouvez utiliser les noeuds finaux d'API réseau dans le profil de connexion pour appeler votre code blockchain et mettre à jour ou interroger le registre spécifique à un canal sur vos homologues. Vous pouvez également utiliser des API dans l'[interface utilisateur Swagger](howto/swagger_apis.html) pour gérer des noeuds, des canaux et des membres de votre réseau.
{:shortdesc}

Vous pouvez utiliser ce tutoriel pour apprendre à accéder aux API d'{{site.data.keyword.blockchainfull_notm}} Platform et à les utiliser pour inscrire et enregistrer votre application auprès du réseau. Vous apprendrez également comment interagir avec votre réseau et émettre des transactions à partir de votre application. Le tutoriel repose sur le tutoriel [Writing Your First Application ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) dans la documentation Hyperledger Fabric. Vous utiliserez de nombreux fichiers et commandes communs au tutoriel **Writing Your First Application**, mais vous les utiliserez pour interagir avec un réseau sur {{site.data.keyword.blockchainfull_notm}} Platform. Ce tutoriel décrit chaque étape de développement d'une application à l'aide du logiciel SDK Fabric Node. Vous apprendrez également comment inscrire et enregistrer des utilisateurs à l'aide du client CA Fabric comme alternative à l'utilisation du logiciel SDK.

En plus de ce tutoriel, vous pouvez utiliser des modèles d'application et du code blockchain fournis par {{site.data.keyword.blockchainfull}} Platform en tant que modèles lorsque vous créez vos propres solutions pour l'entreprise. Pour plus d'informations, voir [Déploiement de modèles d'application](howto/prebuilt_samples.html).

## Prérequis
Vous devez respecter les prérequis suivants pour pouvoir utiliser le tutoriel **Writing Your First Application** sur {{site.data.keyword.blockchainfull_notm}} Platform.

- Si vous n'avez pas de réseau de blockchain sur {{site.data.keyword.cloud_notm}}, vous devez en créer un à partir d'un plan Starter ou Enterprise Membership. Pour plus d'informations, voir [Création d'un réseau de plan Starter](get_start_starter_plan.html#creating-a-network) ou [Création d'un réseau de plan Enterprise](get_start.html#creating-a-network).

  Dès que vous accédez au Moniteur réseau de votre réseau, ajoutez au moins un homologue pour votre organisation à l'écran "Présentation". Ensuite, créez au moins un canal dans votre réseau. Pour plus d'informations, voir [Création d'un canal](howto/create_channel.html#creating-a-channel). **Remarque ** : Si vous utilisez un réseau de plan Starter, votre réseau dispose déjà d'un canal du nom de `defaultchannel` que vous pouvez utiliser pour déployer du code blockchain.

- Installez les outils nécessaires pour télécharger des exemples Hyperledger Fabric et pour utiliser le logiciel SDK Node.
  * [Curl ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") ou [Git ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- Installez des exemples Hyperledger Fabric en téléchargeant le répertoire `fabric-samples`. Vous pouvez suivre le [guide d'initiation![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html) dans la documentation Hyperledger Fabric.

  Après que vous avez téléchargé le répertoire `fabric-samples` sur votre machine locale, accédez à `fabric-samples/fabcar`. Vous pouvez faire une copie de ce répertoire et le renommer afin de pouvoir essayer et tester le modèle d'application dans un nouveau répertoire.

  Dans le répertoire `fabcar`, exécutez la commande `npm install` pour installer les modules nécessaires à l'utilisation du logiciel SDK Fabric, qui incluent `fabric-client` et `fabric-ca-client`.

- Installez et instanciez le code blockchain fabcar dans votre canal à l'aide du [Moniteur réseau](howto/install_instantiate_chaincode.html#installchaincode). Vous pouvez trouver le code blockchain fabcar dans le dossier `fabric-samples` sous `fabric-samples > chaincode > fabcar > go`.

- Extrayez le profil de connexion de votre réseau à l'écran "Présentation" du Moniteur réseau. Sauvegardez le profil de connexion dans votre répertoire `fabcar` et renommez-le en `creds.json`.

## Utilisation de logiciels SDK Fabric
{: #using-the-fabric-sdks}

Les logiciels SDK Hyperledger Fabric fournissent un jeu puissant d'API qui permettent aux applications d'interagir avec des réseaux de blockchain. Vous trouverez la liste la plus récente des langues prises en charge dans la [documentation Hyperledger Fabric SDK Community![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "documentation Hyperledger Fabric SDK Community"){:new_window}. Il est recommandé d'utiliser le logiciel SDK Node ou Java avec {{site.data.keyword.blockchainfull_notm}} Platform. Vous pouvez en savoir plus sur les API fournies par les logiciels SDK dans les référentiels individuels des logiciels SDK.

Ce tutoriel utilise le [Logiciel SDK Node![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Node"){:new_window} pour enregistrer et inscrire votre application, puis utiliser l'application pour émettre des transactions par l'appel et l'interrogation de code blockchain. Ce tutoriel décrit les informations que vous devez fournir au logiciel SDK pour que votre application puisse se connecter à votre réseau de blockchain. Il présente également certaines des API que vous pouvez utiliser, et il indique comment le logiciel SDK interagit avec et soumet des transactions à votre réseau de blockchain.

## Ajout de noeuds finaux d'API à votre application
{: #api-endpoints}

Vous devez fournir à votre application les noeuds finaux d'API de ressources réseau spécifiques, notamment le programme de tri, l'AC et les noeuds homologues, dans le réseau de blockchain sur {{site.data.keyword.cloud_notm}}. Votre application peut interagir avec le réseau via ces noeuds finaux d'API. Vous pouvez rechercher des noeuds finaux d'API dans le profil de connexion de votre réseau. Le profil de connexion est au format JSON et il contient les informations de noeud final d'API et les ID d'inscription/valeurs confidentielles de vos ressources réseau.

1. Procédez à l'extraction des informations de noeud final d'API de vos ressources réseau à partir de votre moniteur réseau à l'aide de l'une des méthodes suivantes :
  * Depuis l'écran "Présentation", cliquez sur **Profil de connexion**. Le profil de connexion contient un jeu complet des informations de noeud final d'API de toutes vos ressources réseau.
    ![Profil de connexion dans le Moniteur réseau](images/service_credentials.png "Profil de connexion dans le Moniteur réseau")

  * Si du code blockchain est en cours d'exécution sur le réseau, vous pouvez obtenir les informations de noeud final d'API qui sont spécifiques au code blockchain. A l'écran "Canaux", cliquez sur la ligne de canal sur lequel s'exécute le code blockchain afin d'ouvrir l'écran de canal spécifique. Ensuite, localisez le code blockchain et cliquez sur le bouton **JSON**.
    ![Noeuds finaux d'API par code blockchain](images/channel_chaincode.png "Noeuds finaux d'API par code blockchain")

2. Localisez les informations de noeud final d'API de vos ressources réseau, qui doivent ressembler à l'URL de la ligne `peer1-org1` dans l'exemple suivant :
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **Remarque **: Vous pouvez si vous le souhaitez cibler des ressources réseau à l'extérieur de votre organisation avec votre application. Par exemple, si un code blockchain [endorsement policy](howto/install_instantiate_chaincode.html#specifying-chaincode-endorsement-policies) requiert la validation d'autres organisations du canal, vous devez obtenir les informations de noeud final de leurs homologues et les certificats TLS qui les accompagnent. Vous pouvez trouver ces informations dans la section peers du profil de connexion. Cependant, vous devez contacter l'administrateur des autres organisations pour lesquelles elles ont rejoint des canaux particuliers.

3. Connectez les informations de noeud final d'API dans un fichier de configuration de votre application comme illustré dans l'exemple suivant :
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  Vous pouvez également envoyer des [en-têtes HEAD](howto/monitor_network.html#monitor-nodes) à ces noeuds finaux pour vérifier la disponibilité de vos ressources réseau.

  Si vous utilisez des logiciels SDK Fabric, vous pouvez également vous connecter à votre réseau à l'aide de votre profil de connexion. Ce tutoriel fournit les informations de noeud final de votre réseau pour le logiciel SDK manuellement. Vous pouvez cependant trouver un tutoriel et des conseils sur l'[utilisation de votre profil de connexion avec le logiciel SDK](#using-your-connection-profile-with-the-sdk) dans une section ultérieure.

## Inscription de votre application
{: #enroll-app}

Avant de connecter une application à votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform, vous devrez prouver l'authenticité de votre application au réseau. Nous n'allons pas entrer dans le détail de l'infrastructure x509 et de l'infrastructure à clés publiques car il existe un tas de [ressources externes![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window} pour cela. Disons simplement que les flux de communication dans Fabric implémentent des opérations de signature/vérification pour chaque point de contact. Ainsi, toute application qui envoie des appels (des requêtes ou des mises à jour de registre, par exemple) au réseau doit signer les contenus avec sa clé privée et joindre un certificat x509 correctement signé à des fins de vérification.  L'**inscription** est le processus de génération des clés et certificats nécessaires à partir de l'autorité de certification appropriée. Après l'inscription, votre application est prête à communiquer avec le réseau.

Cette section explique comment extraire les clés et certificats à l'aide du logiciel SDK Fabric dans le cadre du tutoriel **Writing Your First Application**. Vous pouvez également apprendre à utiliser le client CA Fabric depuis la ligne de commande pour inscrire votre application.

<!-- this last sentance when the certificates article goes live --->

### Inscription avec le logiciel SDK Fabric
{: #enroll-app-sdk}

Depuis le répertoire `fabcar` de votre dossier `fabric-samples`, ouvrez le fichier `enrollAdmin.js` dans un éditeur de texte.

1. Le fichier crée tout d'abord une instance du client Fabric.
  ```
  var fabric_client = new Fabric_Client();
  ```

2. Le fichier crée ensuite un stockage de valeurs de clés (KVS) pour gérer les certificats. Le logiciel SDK utilise la classe [KeyValueStore ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} pour créer un stockage de valeurs de clés, ainsi que la classe [CryptoSuite ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} pour effectuer les calculs crypto. Vous pouvez voir le bloc de code approprié ci-dessous.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. Après la définition de KVS, vous pouvez recourir à quelques méthodes de la classe [Fabric Client ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Client.html "Fabric Client") et l'API Fabric-CA-Client <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> pour communiquer avec le serveur AC. Vous devrez fournir au logiciel SDK le nom et l'URL de votre autorité de certification. Ouvrez le fichier JSOB du **profil de connexion** depuis votre écran **Présentation** dans le Moniteur réseau, puis recherchez les variables suivantes sous la section `certificateAuthorites` :
  * URL de l'autorité de certification : `url` sous `certificateAuthorities`
  * ID Admin : ``enrollId``
  * Mot de passe Admin : ``enrollSecret``
  * Nom de l'autorité de certification : `caName`

  **Modifiez** les lignes pertinentes dans le fichier `enrollAdmin.js` avec ces informations de la manière suivante :
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  Par exemple :
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```
  {:codeblock}

  Le client Fabric vérifie ensuite si votre application est déjà inscrite. **Modifiez** la ligne ci-dessous avec l'élément `enrollID` de votre profil de connexion :
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. Vous devez envoyer l'appel d'inscription ("enroll") au serveur d'autorité de certification. Votre `admin` est déjà enregistré auprès de votre réseau. L'appel d'inscription extrait une clé privée et une clé publique regroupées dans un certificat x509 et signées par l'autorité de certification ciblée. On appelle cela un certificat signcert ou certificat d'inscription. Le certificat signCert permet aux membres du réseau de vérifier les appels en provenance du client. Vous devez fournir le nom, le mot de passe de votre organisation, ainsi que l'ID MSP de votre fichier de données d'identification. Dans la section `certificateAuthorities` de vos données d'identification réseau, recherchez vos éléments `enrollID`, `enrollSecret` et `x-mspid`. **Modifiez** le bloc de code ci-dessous avec ces valeurs et remplacez la section pertinente de votre fichier.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. Sauvegardez le fichier `enrollAdmin.js`.

Dans le répertoire `fabcar`, inscrivez l'admin en exécutant la commande suivante :
```
node enrollAdmin.js
```
{:codeblock}

La commande d'inscription génère le certificat signataire et l'exporte dans un dossier nommé `hfc-key-store`. Les futurs fichiers de ce tutoriel rechercheront vos certificats dans ce dossier. Si vous trouvez les certificats admin dans le dossier `hfc-key-store`, la commande d'inscription fonctionne.

Si vous voulez [exploiter votre réseau à l'aide du logiciel SDK](#operate-sdk), vous devez envoyer par téléchargement votre certificat signataire admin à {{site.data.keyword.blockchainfull}} Platform. Vous pouvez trouver votre certificat signataire admin dans le dossier `hfc-key-store`. Ouvrez le fichier `admin`, puis copiez le certificat entre les guillemets après la zone `certificate`. Utilisez un outil ou un éditeur de texte pour convertir le certificat au format PEM. Vous pouvez ensuite charger le certificat Admin sur votre réseau de blockchain depuis le moniteur réseau. Pour plus d'informations sur l'ajout de certificats, consultez [l'onglet "Certificats" de l'écran "Membre"](v10_dashboard.html#members) dans le moniteur réseau. Ceci n'est pas nécessaire si vous utilisez uniquement le logiciel SDK pour appeler ou interroger le code blockchain.

<!-- you can eliminate this section when the certificates article goes live --->

### Inscription avec le client CA Fabric
{: #enroll-app-caclient}

1. Téléchargez les fichiers [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) sur votre machine locale et procédez à leur extraction. Déplacez le client `fabric-ca` exécutable du répertoire dans lequel il a été téléchargé vers un répertoire dans lequel vous pouvez l'utiliser pour exécuter des commandes.

2. Téléchargez les certificats TLS depuis {{site.data.keyword.cloud_notm}} en fonction du plan de service, de l'emplacement et du cluster que vous utilisez.
  - Certificat TLS racine pour le plan Starter  
    - Etats-Unis : [us01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Royaume-Uni : [uk01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney : [aus01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Root TLS Cert for Enterprise Plan ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Sauvegardez le contenu dans un dossier, par exemple ``$HOME/tls``. Cette étape permet de chiffrer le flux de données sur la connexion.

3. Ouvrez le fichier JSON **Profil de connexion** depuis l'écran **Présentation** du Moniteur réseau, puis recherchez les variables suivantes :
  * URL de l'autorité de certification : `url` sous `certificateAuthorities`
  * ID Admin : ``enrollId``
  * Mot de passe Admin : ``enrollSecret``
  * Nom de l'autorité de certification : ``caName``

4. Vous pouvez utiliser le client d'autorité de certification Fabric pour envoyer un appel `enroll` à l'autorité de certification en le transmettant le chemin de certificat TLS et les quatre chaînes ci-dessus avec la commande suivante :
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Vous devez exécuter cette commande dans le répertoire où vous avez déplacé le client d'autorité de certification Fabric. Un appel réel doit ressembler à ce qui suit dans l'exemple de commande :
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. Localisez votre certificat Admin dans `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Vous pouvez ensuite charger le certificat Admin sur votre réseau de blockchain depuis le moniteur réseau. Pour plus d'informations sur l'ajout de certificats, consultez [l'onglet "Certificats" de l'écran "Membre"](v10_dashboard.html#members) dans le moniteur réseau.

  Vous pouvez également trouver le certificat racine de l'autorité de certification et la clé privée Admin dans les répertoires suivants :
  * Certificat racine de l'autorité de certification : `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * Clé privée Admin : `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`


## Enregistrement de votre application
{: #register-app}

Après avoir généré les certificats côté client, vous devez enregistrer votre application auprès du réseau de l'autorité de certification. L'enregistrement ajoute votre application à la liste des composants que le réseau peut reconnaître. Une meilleure pratique consiste à enregistrer votre application en tant qu'identité distincte au lieu d'utiliser `administration` pour les demandes de signature.

### Enregistrement à l'aide du logiciel SDK
{: #register-app-sdk}

Vous pouvez utiliser le fichier `registerUser.js` pour enregistrer et inscrire l'application en tant que `user1` à l'aide de votre certificat signataire `admin`. Ouvrez `registerUser.js` dans un éditeur de texte.

1. Indiquez URL et le nom de l'autorité de certification pour une nouvelle instance du client d'autorité de certification Fabric.

2. Fournissez l'ID d'inscription (enrollID) pour la méthode `getUserContext` du [client Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Client.html) afin de vérifier si votre `admin` est inscrit et autorisé à émettre cette demande. **Modifiez** le bloc de code pertinent dans votre fichier en vous aidant de l'exemple ci-dessous :
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. Utilisez le **client d'autorité de certification Fabric** pour enregistrer et inscrire l'tithe user with the CA, and then use the **Fabric Client** to create the new signCert. **Modifiez** le bloc suivant à l'aide de votre ID MSP et de votre affiliation d'organisation. Vous pouvez trouver votre affiliation `x-affiliations` dans la section des autorités de certification (certificate authorities) de vos données d'identification réseau et utiliser une affiliation répertoriée. Ajoutez le nom de l'utilisateur que vous voulez créer. L'exemple fabcar utilise `user1` par défaut.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. Sauvegardez le fichier `registerUser.js`.

Exécutez la commande `node registerUser.js` pour enregistrer et inscrire `user1`. Si vous trouvez les certificats `user1` dans le dossier `hfc-key-store`, la commande fonctionne. Vous ne pouvez enregistrer une identité qu'une seule fois. Si vous rencontrez un problème, essayez d'exécuter `registerUser.js` avec un nouveau nom d'utilisateur.

<!-- you can eliminate this section when the certificates article goes live --->

### Enregistrement avec le client d'autorité de certification Fabric
{: #register-app-caclient}

1. Exécutez la commande suivante pour rechercher votre affiliation et le nom de votre organisation dans votre réseau de blockchain.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  Vous devez voir des informations similaires à l'exemple suivant :
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  Notez la seconde valeur **affiliation**, par exemple, `ibp.PeerOrg1`. Vous devez utiliser cette valeur dans la commande ci-dessous.

2. Exécutez la commande suivante enregistrer l'homologue.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  Indiquez un nom et un mot de passe pour votre homologue et remplacez `name` et `password` par le nom et le mot de passe de votre homologue. Notez ces informations. Vous en aurez besoin lorsque vous configurerez votre homologue. Par exemple :
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  Vous ne pouvez enregistrer une identité qu'une seule fois. Si vous rencontrez un problème, essayez d'exécuter une commande avec un nouveau nom d'utilisateur et un nouveau mot de passe.

  Lorsque la commande aboutit, vous pouvez voir des informations semblables à l'exemple suivant :    
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

### Enregistrement à l'aide du Moniteur réseau

Vous pouvez aussi enregistrer et inscrire votre application client à partir de l'onglet **Autorité de certification** du Moniteur réseau. Consultez ces [informations](v10_dashboard.html#ca) pour les instructions.

## Emission de transactions par l'appel et l'interrogation de code blockchain
{: #invoke-query}

Votre application doit interagir avec le réseau de blockchain complet pour soumettre une transaction.

1. L'application envoie une proposition de transaction à approuver par les homologues sur le canal.
2. Les homologues de validation retournent la transaction validée à l'application.
3. L'application envoie les transactions validées au service de tri pour ajouter la transaction au registre.

Pour plus d'informations sur le flux de transactions complet, voir [Flux de transactions ![Icône de lien externe](images/external_link.svg "Icône de lien externe")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Flux de transactions"){:new_window} dans la documentation Hyperledger Fabric. Après avoir démarré avec ce tutoriel, consultez la section relative à la [connectivité et la disponibilité des applications](#app-connectivity-availability) pour obtenir des conseils sur la façon de gérer vos interactions SDK avec le réseau.

Les exemples suivants illustrent la manière dont le logiciel SDK Node configure la topologie de réseau, définit la proposition de transaction, puis soumet la transaction au réseau. Vous pouvez utiliser le fichier `invoke.js` pour appeler des fonctions au sein du code blockchain `fabcar`. Ces fonctions vous permettent de créer et de transférer des actifs dans le registre de blockchain. Ce tutoriel utilise la fonction `initLedger` pour ajouter de nouvelles données à votre canal, puis il utilise le fichier `query.js` pour interroger les données.

### Appel de code blockchain
{: #invoke}

Ouvrez le fichier `invoke.js` dans un éditeur de texte.

1. Ajoutez `var creds = require('./creds.json')` en haut du fichier. Cette ligne de code permet au fichier `invoke.js` de lire les informations depuis le fichier de données d'identification `creds.json`.

2. Utilisez la classe [Fabric Client ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} pour configurer le réseau Fabric à l'aide de noeuds finaux d'API de vos ressources réseau. Cette étape définit le canal et les homologues auxquels votre client va soumettre la proposition, ainsi que le service de tri auquel le logiciel SDK va ensuite envoyer la transaction validée. **Modifiez** le bloc de code approprié ci-dessous. La ligne `creds.peers["org1-peer1"].url` importe l'URL d'homologue depuis votre fichier de données d'identification.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  Lorsque vous ajoutez l'URL d'homologue à la méthode `fabric_client.newPeer`, vous importez également les certificats TLS pertinents depuis votre profil de connexion à l'aide du fragment de code ci-dessous. Vous avez réalisé cette même opération lors de l'ajout de l'adresse URL de service de tri. Vous devez utiliser ces certificats TLS pour authentifier les communications auprès de votre réseau.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  Si la règle de validation exige que les transactions soient validées par d'autres organisations du canal, vous devrez ajouter ces homologues d'organisation à l'aide des méthodes `newPeer()` et `channel.addPeer()` lors de la configuration du réseau. Les organisations doivent vous adresser la liste des homologues qui ont rejoint un canal particulier. Les informations de noeud final et les certificats TLS seront disponibles dans le profil de connexion. Le logiciel SDK enverra la transaction à tous les homologues ajoutés au canal.

  Vous pouvez également ajouter des homologues supplémentaires appartenant à votre organisation qui ont rejoint le canal en tant qu'étape pour [rendre votre application hautement disponible](#ha-app). Cela permettra au logiciel SDK d'effectuer une reprise en cas d'interruption de l'un de vos homologues.

3. Après la configuration du réseau Fabric et l'importation de l'identité et du signCert de l'application à l'étape d'enregistrement, le fichier `invoke.js` définit la proposition que vous allez soumettre au réseau. Vous pouvez utiliser la fonction `initLedger` dans le code blockchain `fabcar` afin d'ajouter des données initiales à votre registre. Vous pouvez également modifier le bloc de code pour appeler d'autres fonctions que vous pouvez trouver dans le code blockchain `fabcar`.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  Après avoir défini la demande, vous pouvez envoyer une [proposition de transaction![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") aux homologues sur le canal. Une fois la proposition retournée par les homologues, vous pouvez [envoyer la transaction ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") au service de tri.

4. Vous pouvez ajouter un service d'événement afin de rendre le flux de transactions plus efficace. **Modifiez** la section suivante :
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Même si l'exemple utilise un service d'événement basé sur un homologue, vous devez utiliser un programme d'écoute basé sur un canal. Vous pouvez en apprendre davantage dans la section relative à la [gestion des transactions](#managing-transactions) et dans la [documentation du logiciel SDK Node![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "service d'événement basé sur un canal"){:new_window}.

5. Par défaut, `invoke.js` soumet la transaction en tant que `user1`. Vous pouvez éditer le fichier `invoke.js` si vous avez enregistré un nom différent.

Lorsque vous avez terminé d'éditer le fichier, émettez la commande `node invoke.js` pour soumettre la transaction au réseau. Vous devez voir ce qui suit comme confirmation que l'appel a abouti.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

Cela indique votre application a pu appeler votre code blockchain et a ajouté des données dans votre registre.

### Interrogation de code blockchain
{: #query}

Vous pouvez utiliser `query.js` pour lire le registre. Ouvrez le fichier `query.js` dans un éditeur de texte.

1. Ajoutez `var creds = require('./creds.json')` en haut du fichier. 
2. Mettez à jour le fichier avec le nom de canal et les informations de noeud final de l'homologue. Comme cette opération lit uniquement les données qui sont stockées sur l'homologue, il n'est pas nécessaire que vous ajoutiez les informations de noeud final du service de tri. Le fichier `query.js` suppose également que vous envoyez la proposition en tant que `user1`.
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

Une fois que vous avez édité le fichier, émettez la commande `node query.js` et vous devez voir une liste de voitures dans votre registre, similaire à l'exemple ci-après.
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

Pour plus d'informations sur l'application fabcar et les fonctions utilisées par celle-ci, vous pouvez consulter le tutoriel complet relatif à l'[Writing Your First Application ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "écriture de votre première application"){:new_window} dans la documentation Hyperledger Fabric.

## Utilisation de votre profil de connexion avec le logiciel SDK
{: #using-your-connection-profile-with-the-sdk}

Au lieu d'importer les informations de noeud final de votre réseau manuellement, vous pouvez demander à votre logiciel SDK de se connecter à votre réseau à l'aide du **profil de connexion** depuis l'écran **Présentation** de votre Moniteur réseau. Cela permet de rationaliser le processus de connexion à votre autorité de certification à des fins d'inscription et d'enregistrement. Cela élimine également le besoin de définir votre réseau Fabric avant de soumettre une transaction. Le logiciel SDK trouvera les homologues et les programmes de tri sur le canal pertinent directement depuis le profil de connexion. Vous pouvez trouver plus d'informations sur l'utilisation d'un profil de connexion dans le [documentation relative au logiciel SDK Node ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-network-config.html "tutoriel relatif au profil de connexion"){:new_window}.

Voyons l'utilisation du fichier `invoke.js` comme exemple de l'efficacité d'utiliser un profil de connexion au lieu de noeuds finaux manuels. Vous pouvez établir une nouvelle instance du client Fabric à l'aide de la classe `loadFromConfig`. Remplacez `var fabric_client = new Fabric_Client();` par le code ci-après.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

Au lieu de configurer le réseau Fabric en créant des homologues et des programmes de tri, puis en les ajoutant à un canal, vous pouvez utiliser la ligne suivante pour créer un nouveau canal.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

Le logiciel SDK ajoute ensuite les homologues et le service de tri qui sont définis dans le canal à l'aide du profil de connexion. Cela rend l'écriture d'applications plus efficace, et simplifie la mise à jour de vos applications dès que des membres rejoignent, quittent et démarrent de nouveaux canaux. Pour en savoir plus sur les étapes supplémentaires impliquées, consultez le [tutoriel relatif au profil de connexion ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-network-config.html "tutoriel relatif au profil de connexion"){:new_window} dans la documentation du logiciel SDK Node. Vous pouvez utiliser cette [version du tutoriel fabcar![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} qui utilise un profil de connexion au lieu de connexions de noeud final manuelles.

Vous pouvez envoyer des transactions aux homologues qui se trouvent en dehors de votre organisation pour la validation en modifiant votre profil de connexion. Le profil de connexion contient déjà les informations de noeud final et certificats TLS des homologues d'autres organisations sur votre réseau {{site.data.keyword.blockchainfull}} Platform. Ajoutez le nom de l'homologue au canal pertinent dans la section `channels` du profil pour ajouter l'homologue au canal.

## Connectivité et disponibilité des applications
{: #app-connectivity-availability}

Hyperledger Fabric [Flux de transactions![Icône de lien externe](images/external_link.svg "Icône de lien externe")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Flux de transactions"){:new_window} s'étend sur plusieurs composants, les applications client jouant un rôle unique. Le logiciel SDK soumet des propositions de transaction aux homologues pour validation. Il collecte ensuite les propositions validées pour leur envoi au service de tri, lequel envoie ensuite les blocs de transactions aux homologues à ajouter aux registres de canal. Les développeurs d'applications de production doivent être préparés à la gestion de leurs interactions entre le logiciel SDK et leurs réseaux à des fins d'efficacité et de disponibilité.

### Gestion de transactions
{: #managing-transactions}

Les clients d'application doivent s'assurer que leurs propositions de transaction sont validées et que les propositions sont effectuées avec succès.  Une proposition peut être retardée ou perdue pour de multiples raisons, comme une indisponibilité du réseau ou une défaillance de composant. Vous devez préparer votre application pour la [haute disponibilité](#ha-app) afin de gérer les défaillances de composant. Vous pouvez aussi [accroître les valeurs de délai](#set-timeout-in-sdk) dans votre application afin d'éviter un dépassement du délai des propositions avant que le réseau puisse répondre.

Si un code blockchain n'est pas en cours d'exécution, la première proposition de transaction qui est envoyée à ce code blockchain démarrera le code blockchain. Alors que le code blockchain est en cours de démarrage, toutes les autres propositions sont rejetées avec une erreur qui indique que le code blockchain est actuellement en cours de démarrage. Cela est différent de l'invalidation de transaction. Si une proposition est rejetés alors que le code blockchain est en cours de démarrage, les clients d'application doivent envoyer de nouveau les propositions rejetées une fois le code blockchain démarré. Les clients d'application peuvent utiliser une file d'attente de messages pour éviter la perte des propositions de transaction.

Vous pouvez utiliser un service d'événement basé sur un canal pour surveiller les transactions et générer des file d'attente de messages. Les programmes d'écoute basés sur un canal doit être utilisés au lieu de services basés sur un homologue en raison de leur capacité à s'adapter à plusieurs canaux et à distinguer le trafic sur différents canaux. La classe [channelEventHub ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} peut enregistrer des programmes d'écoute basés sur des événements de transaction, de bloc et de séquence d'opérations. Vous pouvez en apprendre davantage sur la configuration d'un [service d'événement basé sur un canal ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "service d'événement basé sur un canal"){:new_window} dans la documentation du logiciel SDK Node.

### Fermeture des connexions réseau

Lorsque vous ajoutez des homologues et des programmes de tri à votre logiciel SDK avant de soumettre une proposition de transaction, vous ouvrez une connexion de flux grpc au composant réseau. Par exemple, la commande
```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
ouvre une connexion à `org1-peer1`. Si une application est constamment en cours d'exécution, vous devez fermer les connexions lorsqu'il n'est plus nécessaire d'utiliser les commandes `peer.close()` et `orderer.close()` pour libérer des ressources et éviter une dégradation des performances. Vous trouverez plus de détails dans les sections relatives aux classes [peer](https://fabric-sdk-node.github.io/Peer.html#close__anchor) et [orderer](https://fabric-sdk-node.github.io/Orderer.html#close__anchor) de la documentation du logiciel SDK Node.

### Applications hautement disponibles
{: #ha-app}

Comme pratique optimale pour haute disponibilité, nous vous recommandons fortement de déployer au moins deux homologues par organisation pour ma reprise en ligne. Vous devrez également adapter vos applications pour la haute disponibilité. Installez du code blockchain sur les deux homologues chaincode et ajoutez-les à vos canaux. Ensuite, soyez préparé à la [soumission de propositions de transaction](#invoke) sur les deux noeuds finaux homologue lors de la configuration de votre réseau et la génération de votre liste cible d'homologues. Si vous disposez d'un réseau de plan Enterprise, vous pouvez aussi ajouter un autre programme de tri à votre réseau. Si vous utilisez votre [profil de connexion](#using-your-connection-profile-with-the-sdk) au lieu d'ajouter des noeuds finaux de réseau manuellement, assurez-vous que votre profil est à jour et qu'un canal pertinent a été ajouté aux homologues et programmes de tri supplémentaires dans la section `channels` du profil. Le logiciel SDK ajoutera ensuite les composants rejoints sur le canal à l'aide du profil de connexion.

## Activation de TLS mutuel
{: #mutual-tls}

Si vous exécutez des réseaux de plan Enterprise au niveau Fabric V1.1, vous avez la possibilité d'[activer le TLS mutuel](v10_dashboard.html#network-preferences) pour vos applications. Si vous activez TLS mutuel, vous devez mettre à jour vos applications afin de prendre en charge cette fonction. Sinon, vos applications ne peuvent pas communiquer avec votre réseau.

Dans le profil de connexion, localisez la section `certificateAuthorities` où se trouvent les attributs suivants qui sont nécessaires à l'inscription et à l'obtention des certificats pour communiquer avec votre réseau à l'aide de TLS mutuel.

- `url` : URL pour la connexion à l'autorité de certification qui peut fournir les certificats TLS mutuel
- `enrollId` : ID d'inscription à utiliser pour l'obtention d'un certificat
- `enrollSecret` : valeur confidentielle d'inscription à utiliser pour l'obtention d'un certificat
- `x-tlsCAName` : nom d'autorité de certification à utiliser pour l'obtention d'un certificat qui permet à l'application de communiquer avec TLS mutuel.

Pour plus d'informations sur la mise à jour de vos applications pour la prise en charge de TLS mutuel, voir [Comment configurer le TLS mutuel ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "TLS mutuel"){:new_window}.

## (Facultatif) Exploitation de votre réseau avec le logiciel SDK
{: #operate-sdk}

Vous pouvez également utiliser le logiciel SDK pour exploiter votre réseau de blockchain. Ce tutoriel explique comment vous pouvez utiliser le logiciel SDK pour joindre vos pairs à des canaux, installer du code blockchain sur vos homologues, et instancier du code blockchain sur des canaux. Ces étapes sont facultatives car vous pouvez également effectuer ces opérations à l'aide du Moniteur réseau ou d'API dans l'[interface utilisateur swagger](howto/swagger_apis.html) si tous vos homologues sont en cours d'exécution sur {{site.data.keyword.blockchainfull}}. Platform

Vous devez télécharger votre signCert administrateur sur {{site.data.keyword.blockchainfull}} Platform pour pouvoir effectuer ces étapes. Vous trouverez les instructions sur la façon de télécharger votre signCert à la fin de la [section d'inscription](#enroll-app-sdk)

### Rejoindre un canal
{: #join-channel-sdk}

Après avoir crée un canal à l'aide du Moniteur réseau ou d'API, vous pouvez utiliser le logiciel SDK pou joindre votre homologue au canal.

1. [Extraction du bloc d'origine ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "extraction du bloc d'origine"){:new_window} du canal depuis le service de tri.
2. Transmettez le bloc d'origine à la méthode [join channel ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} pour joindre votre réseau au canal.

Pour utiliser l'exemple `fabcar` pour rejoindre un canal, utilisez le fichier `invoke.js` comme point de départ. Vous devez envoyer cette demande en tant qu'administrateur et non en tant qu'application ; vous devez donc remplacer `user1` par `admin` dans la méthode `getUserContext`. A partir de l'emplacement ù vous définissez la demande d'appel de votre code blockchain à la section `var request = {`, remplacez le flux de transaction par une demande pour rejoindre le canal sur la base du fragment de code ci-dessous à partir de[la documentation du logiciel SDK Node![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "TLS mutuel"){:new_window}.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```
### Installation d'un code blockchain
{: #install-cc-sdk}

Vous pouvez utiliser la méthode [install chaincode ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} de la classe [Fabric Client ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} pour installer du code blockchain sur votre homologue.

Pour utiliser l'exemple `fabcar` pour installer le code blockchain `fabcar` sur votre homologue, utilisez le fichier `query.js` comme référence et éditez-le. Vous devez envoyer cette demande en tant qu'administrateur et non en tant qu'application ; vous devez donc remplacer `user1` par `admin` dans la méthode `getUserContext`. Remplacez l'objet de proposition de transaction par une demande d'installation de code blockchain à l'aide de l'exemple ci-dessous :
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

Envoyez cet objet à `return fabric_client.installChaincode(request);`, au lieu de la ligne `return channel.queryByChaincode(request);` figurant actuellement dans le fichier.

### Instanciation d'un code blockchain
{: #instantiate-cc-sdk}

Pour instancier le code blockchain, vous devez envoyer une [proposition d'instanciation![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} à l'homologue, puis envoyer une [demande de transaction ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} au service de tri.

Pour utiliser l'exemple `fabcar` pour instancier votre code blockchain, utilisez le fichier `invoke.js` comme point de départ. Vous devez envoyer cette demande en tant qu'administrateur et non en tant qu'application ; vous devez donc remplacer `user1` par `admin` dans la méthode `getUserContext`. Remplacez l'objet de proposition de transaction par une demande d'installation de code blockchain à l'aide de l'exemple ci-dessous :
```
var request = {
		targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
		channelNames: 'mychannel'
		};

```
{:codeblock}

Envoyez cette demande à `return channel.sendInstantiateProposal(request);` au lieu de la ligne `return channel.sendTransactionProposal(request);` figurant actuellement dans le fichier. Après envoi de la demande d'instanciation au canal, vous devez ensuite envoyer la proposition validée en tant que transaction au service de tri. Celui-ci utilise les mêmes méthodes que pour l'envoi d'une transaction ; vous pouvez donc laisser le reste du fichier inchangé. Vous pouvez si vous le souhaitez [augmenter la valeur de délai](#set-timeout-in-sdk) dans la proposition d'instanciation. Sinon la demande peut dépasser le délai d'attente avant que la plateforme ne puisse démarrer le conteneur de code blockchain.

## (Facultatif) Définition de valeurs de délai dans les logiciels SDK Fabric
{: #set-timeout-in-sdk}

Les logiciels SDK Fabric définissent des valeurs de délai par défaut dans les applications client pour les événements du réseau de blockchain. Consultez l'exemple suivant concernant les paramètres de délai par défaut dans le kit SDK Fabric Java. Le chemin du fichier est `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

Cependant, vous devrez peut-être modifier les valeurs de délai par défaut dans votre propre application. Par exemple, si votre application appelle une transaction qui a besoin de plus de 5000 millisecondes, qui correspond à la valeur de délai par défaut pour la connexion au concentrateur d'événements, en réponse, vous obtenez une erreur car l'événement d'appel s'achève à 5000 ms avant la fin de la transaction. Vous pouvez définir la propriété système afin de remplacer les valeurs par défaut de votre application client. Etant donné que les valeurs par défaut sont initialisées avant que vous ne définissiez la propriété système, celle-ci pourrait ne pas être prise en compte. Par conséquent, vous devez définir la propriété système du délai dans une construction statique de votre application client. Consultez l'exemple suivant sur la définition de la valeur de délai pour la connexion au concentrateur d'événements sur 15000 ms dans le kit SDK Java. Le chemin du fichier est `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Si vous utilisez le logiciel SDK Node, vous pouvez définir les valeurs de délai d'expiration directement dans la méthode appelée. Par exemple, vous pourriez utiliser la ligne ci-dessous pour augmenter la valeur du délai pour [instancier un code blockchain![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") sur 5 minutes.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## (Facultatif) Utilisation d'index CouchDB
{: #couchdb-indices}

Si votre réseau utilise CouchDB et si vous voulez tirer parti de la fonction d'indexation CouchDB pour améliorer les performances de CouchDB, vous devez packager les index avec le code blockchain.

Pour plus d'informations sur CouchDB et la façon de définir des index, voir [CouchDB as the State Database ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window}. dans la documentation Hyperledger Fabric. Vous pouvez également trouver un exemple qui utilise un index avec un code blockchain dans ce [tutoriel Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window}.

<!--# Next steps-->

## Hébergement d'applications
{: #host-app}

Vous pouvez héberger votre application sur votre système de fichiers local ou l'envoyer sur {{site.data.keyword.Bluemix_notm}}. Pour envoyer votre application sur {{site.data.keyword.Bluemix_notm}}, procédez comme suit :
1. Installez le [programme d'installation en ligne de commande Cloud Foundry![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/cloudfoundry/cli/releases).  Testez votre installation à l'aide de la commande `cf`.
    * Si votre installation aboutit, vous devez voir du texte s'afficher sur votre terminal.
    * Si "command not found" s'affiche, votre installation n'a pas abouti ou CF n'est pas ajouté à votre chemin de système.
2. Configurez un noeud final d'API et connectez-vous avec vos ID et mot de passe {{site.data.keyword.Bluemix_notm}} en exécutant les commandes suivantes :
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. Accédez au répertoire de votre application, et envoyez votre application à l'aide de la commande ci-après. Cette opération peut prendre plusieurs minutes en fonction de la taille de votre application. Vous pouvez voir les journaux de {{site.data.keyword.Bluemix_notm}} sur votre terminal. Les journaux cessent lorsque l'application est lancée correctement.
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	Vous pouvez consulter les journaux de votre application en exécutant l'une des commandes suivantes :
	* `> cf logs NOM_DE_VOTRE_APPLICATION`
	* `> cf logs NOM_DE_VOTRE_APPLICATION -recent`

## Déconnexion de votre application du réseau
{: #disconnect-app}

Procédez comme suit pour supprimer la connexion entre votre application et le réseau de blockchain sur {{site.data.keyword.cloud_notm}}.
1. Retirez les informations de noeud final d'API de votre fichier de configuration d'application. Pour plus d'informations, voir [Ajout de noeuds finaux d'API à votre application](#api-endpoints).
2. Supprimez votre conteneur de code blockchain.
  1. Dans l'écran "Canal" du moniteur réseau, localisez le canal dans lequel est installé votre code blockchain.
  2. Dans l'écran spécifique au canal, localisez le code blockchain à désactiver.
  3. Cliquez sur le bouton **Supprimer**, puis sur **Soumettre** dans l'écran de suppression du code blockchain. Votre conteneur de code blockchain sera retiré.
	![Suppression d'un code blockchain](images/channel_chaincode.png "Suppression d'un code blockchain")

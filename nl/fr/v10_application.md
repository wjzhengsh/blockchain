---

copyright:
  years: 2017
lastupdated: "2017-08-30"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Développement d'applications

Les applications vous permettent d'appeler du code blockchain afin d'interroger ou de mettre à jour un  registre spécifique à un canal dans votre réseau {{site.data.keyword.blockchain}}.
{:shortdesc}

## Configuration de l'environnement de développement d'une application
Vous devez installer certains logiciels prérequis pour développer des applications qui peuvent interagir avec le réseau {{site.data.keyword.blockchain}} sur {{site.data.keyword.Bluemix}}.
{:shortdesc}

*	Git ([Page de téléchargement de Git![External link icon](images/external_link.svg "External link icon")](https://git-scm.com/downloads){:new_window})
	
	Git est un outil de contrôle des versions que vous pouvez utiliser pour vous familiariser avec le développement de code blockchain et le développement logiciel en général. De plus, git bash, qui est installé avec git sous Windows, est une excellente alternative à l'invite de commande Windows.
 
	Utilisez la commande ci-après pour vérifier votre installation de Git. Vous devez obtenir un résultat semblable au suivant :  
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([Page de téléchargement de GoLang![External link icon](images/external_link.svg "External link icon")](https://golang.org/dl){:new_window})

	Le programme d'installation de GoLang installe un jeu d'outils Go CLI, qui sont très utiles pour écrire du code blockchain. Par exemple, la commande `go build` vous permet de vérifier que votre code blockchain se compile réellement avant toute tentative de déploiement sur un réseau. Au stade de cette publication, ce code blockchain se compile généralement bien en version `1.8.3`.

	Utilisez la commande ci-après pour vérifier votre version de GoLang. Vous devez obtenir un résultat semblable au suivant :
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}
	
	Suivez les [Instructions d'installation![External link icon](images/external_link.svg "External link icon")](https://golang.org/doc/install){:new_window} pour définir correctement les variables d'environnement. Testez votre installation `GOPATH` à l'aide de la commande ci-dessous. Notez que votre installation `GOPATH` ne correspond pas nécessairement à cet exemple ; il suffit seulement que cette variable soit définie sur un répertoire valide dans votre système de fichiers.   
	```
	$ echo $GOPATH
C:\gopath
	```
	{:screen}

	Vous pouvez ensuite vérifier votre installation de GoLang en générant du code GoLang à partir de l'exemple [hello world ![External link icon](images/external_link.svg "External link icon")](https://golang.org/doc/install#testing){:new_window}.

*	Node.js (page de téléchargement de [Node.js ![External link icon](images/external_link.svg "External link icon")](https://nodejs.org/en/download/){:new_window}). Choisissez une version comprise entre 6.9.5 < 7. Les versions de noeud supérieures à 7 produiront des erreurs lors du téléchargement des modules SDK.  

	Utilisez les commandes ci-après pour vérifier votre installation de Node.js. Vous devez obtenir un résultat semblable au suivant :  
	```
	$ node -v
	v6.10.1
	
	$ npm -v
	3.10.10
	```
	{:screen}

## Génération de certificats côté client
Nous n'allons pas entrer dans le détail de l'infrastructure x509 et de l'infrastructure à clés publiques car il existe un tas de ressources externes sur le sujet. Disons simplement que les flux de communication dans Fabric implémentent des opérations de signature/vérification pour chaque point de contact. Ainsi, tout client qui envoie des appels (des transactions, par exemple) au réseau devra signer des contenus (clé privée) et connecter un certificat x509 correctement signé à des fins de vérification  (certificat signé).  Le clé privée et le certificat signé, associés à un identificateur MSP et un certificat racine d'autorité de certification, constituent ce qu'on appelle l'objet "contexte d'utilisateur". Là encore, nul besoin de fournir des informations supplémentaires. Nous communiquerons simplement l'autorité de certification appropriée et procéderons à l'extraction des clés et des certificats permettant la formation de l'objet (ce processus est appelé enregistrement). Une fois l'objet de contexte utilisateur formé, il est aussi facile d'appeler une API depuis votre application pour définir ("set") ou obtenir ("get") le contexte de cet utilisateur. A ce stade, l'application (c'est-à-dire, le client) est dotée de tous les artefacts nécessaires et prête pour communiquer avec le réseau.  Nous allons examiner deux approches pour l'extraction des clés et des certificats.

### Ligne de commande
Il s'agit de la plus simple des deux approches. Pour commencer, suivez les instructions de création du [client d'autorité de certification Fabric![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Cette étape vous permet de communiquer avec un serveur d'autorité de certification et de recevoir en retour des certificats et des clés correctement mis en forme.  

Ensuite, téléchargez les certificats TLS depuis [votre Bluemix ![External link icon](images/external_link.svg "External link icon")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) et sauvegardez le contenu dans un dossier, par exemple ``$HOME/tls``. Cette étape permet de chiffrer le flux de données sur la connexion.

Enfin, ouvrez le fichier JSON **Service Credentials** depuis l'écran **Présentation** du moniteur de réseau, puis  vérifiez les informations suivantes :
* URL de l'autorité de certification : `url` sous `certificateAuthorities`
* ID Admin : ``enrollId``
* Mot de passe Admin : ``enrollSecret``
* Nom de l'autorité de certification : ``caName``

Grâce au client d'autorité de certification Fabric, nous pouvons envoyer un appel d'enregistrement ("enroll") à notre autorité de certification en transmettant le chemin de certificat TLS et les quatre chaînes ci-dessus à l'aide de la commande suivante :  
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

L'élément binaire ``fabric-ca-client`` est placé dans ``$GOPATH/bin``, de sorte que vous devez vous trouver à l'emplacement approprié sur votre machine locale lors de l'exécution de cette commande. Un appel réel doit ressembler à ce qui suit dans l'exemple de commande  :  
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```
  
Localisez votre certificat Admin dans `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Vous pouvez ensuite charger le certificat Admin sur votre réseau de blockchain depuis le moniteur réseau. Pour plus d'informations sur l'ajout de certificats, consultez [l'onglet "Certificats" de l'écran "Membre"](v10_dashboard.html#members) dans le moniteur réseau.

Vous pouvez également trouver le certificat racine de l'autorité de certification et la clé privée Admin dans les répertoires suivants :
* Certificat racine de l'autorité de certification : `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* Clé privée Admin : `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Deux référentiels Hyperledger contiennent d'excellents scripts et ressources qui décrivent comment interagir par programme avec une autorité de certification. Le référentiel ``fabric-samples`` contient l'exemple "balance transfer" et le référentiel ``fabric-sdk-node`` comporte une série de tests de service d'autorité de certification. Si vous envisagez d'émettre vos demandes d'inscription côté application, vous devrez pleinement maîtriser les API qui doivent être exposées au sein des modules ``fabric-ca-client`` et  ``fabric-client``. Utilisez ces scripts et référentiels en tant que base de référence pour structurer votre application.

Procédons maintenant à un examen rapide de quelques fragments de code à partir de l'exemple "balance transfer" :

Vous devez d'abord créer notre objet de client et définir une instance de magasin clé/valeur dans laquelle seront stockées les certificats et les clés.  Pour cela, il suffit d'utiliser une simple méthode de fabrique - ``newCryptoSuite`` - qui étend la classe ``Client`` à partir de ``BaseClient``.  Voici un aperçu rapide du code :

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

Une pratique courante consiste à exporter une variable d'environnement qui définit le chemin clé/valeur sur votre machine et la transmet à la fonction ci-dessous. A présent que nous avons défini la table KVS, nous pouvons utiliser quelques méthodes de la classe ``FabricCAServices``. Cette classe est une implémentation du client d'autorité de certification Fabric, qui permet de communiquer avec le serveur d'autorité de certification. Tout d'abord, vous devez transmettre certaines informations à notre client d'autorité de certification, c'est-à-dire l'URL de l'autorité de certification :

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

Ensuite, nous devons envoyer l'appel d'inscription ("enroll") au serveur d'autorité de certification. Cette opération va renvoyer au client une clé privée et une clé publique regroupées dans un certificat x509 et signées par l'autorité de certification ciblée. On appelle cela un certificat signcert ou certificat d'inscription. Ce certificat d'inscription ou "eCert" est l'élément clé car il permet aux entités réseau de vérifier les transactions et les appels en provenance du client :

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

Et la tâche finale consiste à définir effectivement la suite de chiffrement et à générer le contexte d'utilisateur :

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

Vous pouvez ensuite charger le certificat Admin sur votre réseau de blockchain depuis le moniteur réseau. Pour plus d'informations sur l'ajout de certificats, consultez [l'onglet "Certificats" de l'écran "Membre"](v10_dashboard.html#members) dans le moniteur réseau.

## Développement d'applications
Vous pouvez développer votre application en Javascript ou Java, et optimiser les API disponibles dans les kits SDK Hyperledger Fabric Client afin de permettre une interaction entre votre application et votre réseau. Une application doit inclure au moins les informations suivantes :
* Nom et version du code blockchain à appeler.
* Informations de noeud final d'API de vos ressources réseau, y compris les programmes de tri, les autorités de certification et les homologues.
* Fonctions pour l'interrogation ou la mise à jour du registre dans le réseau. Si vous souhaitez la haute disponibilité, vous devez envisager la reprise en ligne de noeud dans votre application.

Vous pouvez trouver des modèles d'applications du **plan d'entreprise** sous {{site.data.keyword.Bluemix_short}} dans [Modèles d'application](howto/sample_applications.html).  Utilisez ces exemples de code blockchain et d'applications comme modèle pour la création de votre propre solution métier.

## Ajout de données d'identification de service réseau à votre application
Vous devez ajouter les données d'identification du service de vos composants réseau à votre application afin qu'elle puisse interagir avec votre réseau {{site.data.keyword.blockchain}} sous {{site.data.keyword.Bluemix_short}}.  Si vous n'avez pas de réseau {{site.data.keyword.blockchain}} sous {{site.data.keyword.Bluemix_short}}, consultez [Mise en route de la plateforme IBM Blockchain](get_start.html). 

Les données d'identification du service sont au format JSON et contiennent les informations de noeud final API et les enrollIDs/valeurs confidentielles de vos composants réseau, y compris le programme de tri, l'autorité de certification et les noeuds homologues.  Votre application pourra interagir avec les homologues et d'autres composants réseau via ces noeuds finaux d'API.  

1. Procédez à l'extraction des informations de noeud final d'API de vos composants réseau à partir de votre moniteur réseau à l'aide de l'une des méthodes suivantes :
	* Pour obtenir les informations de noeud final d'API spécifiques à un code blockchain, dans l'écran de canal spécifique, sur lequel s'exécute le code blockchain, localisez le code blockchain et cliquez sur le bouton **JSON**.
	![Service credentials per chaincode](images/channel_chaincode_detail.png "Service credentials per chaincode")
	* Pour obtenir un jeu complet des informations de noeud final d'API concernant l'ensemble de vos composants réseau, cliquez sur le bouton **Données d'identification pour le service** dans l'écran "Ressources".
	![Moniteur réseau des données d'identification du service](images/service_credentials.png "Moniteur réseau des données d'identification du service")
	 
2. Localisez les données d'identification et les informations de noeud final d'API de vos ressources réseau. Exemple :
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**Remarque **: Si vous voulez cibler des homologues supplémentaires du réseau, par exemple, vous avez besoin de la validation d'un homologue qui n'appartient pas à votre organisation, puis vous devez obtenir les informations de noeud final d'API correctes de ces homologues. Vous devez aussi stocker le certificat d'autorité de certification d'autres organisations afin de vérifier les réponses renvoyées à votre application. Ces informations ne sont pas exposées dans vos données d'identification du service, par conséquent vous devez contacter l'administrateur approprié de l'organisation Bluemix et acquérir ces informations dans une opération externe. L'adresse URL de service de tri est commune au sein du réseau ; vous n'avez pas besoin d'informations spécifiques aux membres pour le service de tri.  

3. Connectez les informations de noeud final d'API dans un fichier de configuration de votre application comme illustré dans l'exemple suivant :  
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

## Hébergement d'applications
Vous pouvez héberger votre application sur votre système de fichiers local ou l'envoyer sur {{site.data.keyword.Bluemix_notm}}. Pour envoyer votre application sur {{site.data.keyword.Bluemix_notm}}, procédez comme suit :
1. Installez le [programme d'installation en ligne de commande Cloud Foundry![External link icon](images/external_link.svg "External link icon")](https://github.com/cloudfoundry/cli/releases).  Testez votre installation à l'aide de la commande `cf`.	
    * Si votre installation aboutit, vous devez voir du texte s'afficher sur votre terminal. 
    * Si "command not found" s'affiche, votre installation n'a pas abouti ou CF n'est pas ajouté à votre chemin de système.	
2. Configurez un noeud final d'API et connectez-vous avec vos ID et mot de passe {{site.data.keyword.Bluemix_notm}} en exécutant les commandes suivantes :
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```  
3. Accédez au répertoire de votre application, et envoyez votre application à l'aide de la commande ci-après. Cette opération peut prendre plusieurs minutes en fonction de la taille de votre application. Les journaux de {{site.data.keyword.Bluemix_notm}} s'affichent sur votre terminal ; leur affichage cesse une fois l'application lancée.  
	```
	> cf push YOUR_APP_NAME_HERE
	```  
	Vous pouvez consultez les journaux de votre application en exécutant l'une des commandes suivantes :
	* `> cf logs NOM_DE_VOTRE_APPLICATION`
	* `> cf logs NOM_DE_VOTRE_APPLICATION -recent`


## Déconnexion de votre application du réseau
Procédez comme suit pour supprimer la connexion entre votre application et le réseau {{site.data.keyword.blockchain}} sur {{site.data.keyword.Bluemix_short}}.
1. Retirez les informations de noeud final d'API de votre fichier de configuration d'application. Pour plus de détails, voir [Ajout de données d'identification de service réseau à votre application](#adding-network-service-credentials-to-your-application).
2. Supprimez votre conteneur de code blockchain.
	1. Dans l'écran "Canal" du moniteur réseau, localisez le canal dans lequel est installé votre code blockchain.
	2. Dans l'écran spécifique au canal, localisez le code blockchain à désactiver.
	3. Cliquez sur le bouton **Supprimer**, puis sur **Soumettre** dans l'écran de suppression du code blockchain. Votre conteneur de code blockchain sera retiré.  
	![Suppression d'un code blockchain](images/channel_chaincode_detail.png "Suppression d'un code blockchain")

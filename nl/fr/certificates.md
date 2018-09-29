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


# Gestion des certificats sur {{site.data.keyword.blockchainfull_notm}} Platform
{: #certificates}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform repose sur Hyperledger Fabric et elle génère des réseaux de blockchain privés. Les participants sont reconnus comme étant des membres du réseau et leur activités et leur accès au ressources réseau sont vérifiées en continu. L'identité d'un participant est fournie sous la forme d'un certificat numérique x509 sécurisé. La vérification est effectuée par une infrastructure PKI sous-jacente et des opérations de signature/vérification qui sécurisent les transactions et la gestion au sein du réseau.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform gère la plupart des opérations de certificat sans que les utilisateurs aient besoin de gérer leurs certificats. Toutefois, il peut arriver que vous ayez à gérer des certificats qui vous permettent de communiquer avec le réseau, comme le développement d'applications ou l'extension de votre réseau avec un homologue distant. Voici un petit guide pour votre autorité de certification et l'infrastructure de certificat sous-jacente. Ce tutoriel peut vous aider à en apprendre davantage sur les certificats que vous allez utiliser et les tâches que vous allez exécuter.

## Autorités de certification
{: #network-ca}

Les autorités de certification (AC) fournissent l'identité sur le réseau. Une autorité de certification peut être considérée comme un notaire public de confiance qui agit comme point d'ancrage entre plusieurs parties. Toutes les entités du réseau reçoivent un certificat signé par une autorité de certification racine qui encapsule leur identité numérique. Ce certificat est la racine de confiance pour toutes les opérations de signature et de vérification effectuées sur le réseau. Pour plus de détails sur la façon dont les autorités de certification sont utilisées pour établir une identité, voir la [documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html).

Chaque membre du réseau possède sa propre autorité de certification, pour laquelle est émis un certificat de l'autorité de certification racine du réseau. Ce lien à l'autorité de certification racine établit un point d'ancrage de confiance pour votre organisation. L'autorité de certification de votre organisation signe des demandes pour toutes les entités dont votre organisation est propriétaire, par exemple votre admin, vos homologues ou vos applications. Si vous voulez ajouter un homologue distant ou une nouvelle application à votre réseau, vous devez enregistrer la nouvelle identité auprès de votre autorité de certification (enregistrement). Ensuite, l'autorité de certification peut fournir la nouvelle entité avec les certificats dont elle aura besoin pour interagir avec le réseau (inscription).

### Enregistrement à l'aide du Moniteur réseau
{: #ca-panel}

Vous pouvez utiliser le Moniteur réseau de {{site.data.keyword.blockchainfull_notm}} Platform pour consulter les identités qui sont enregistrées auprès de votre autorité de certification et en ajouter de nouvelles. Pour cela, accédez au panneau "Autorité de certification" du Moniteur réseau. Ce panneau affiche toutes les identités qui ont été enregistrés auprès de votre autorité de certification, y compris l'admin, les homologues et les applications client de votre organisation. Pour enregistrer une nouvelle identité dans votre organisation, cliquez sur le bouton **Ajouter un utilisateur** dans la partie supérieure du panneau. Une fenêtre en incrustation s'ouvre et affiche les zones suivantes qui sont nécessaires pour l'enregistrement d'une nouvelle identité. 
  - **ID :** Il s'agit du nom de votre nouvelle identité, parfois appelée `ID inscription`. **Sauvegardez cette valeur** à des fins de configuration d'un homologue distant ou d'inscription d'une nouvelle application.
  - **Valeur confidentielle :** Il s'agit du mot de passe de votre identité, parfois appelé `Valeur confidentielle d'inscription`  **Sauvegardez cette valeur**  à des fins de configuration d'un homologue distant ou d'inscription d'une nouvelle application.  
  - **Type :** Sélectionnez le type d'identité que vous voulez enregistrer, qu'il s'agisse d'un homologue ou d'une application client.
  - **Affiliation :** Il doit s'agir de l'affiliation au sein de votre organisation, par exemple `org1`, à laquelle l'identité va appartenir.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si vous laissez cette zone vide, la valeur par défaut est un nombre illimité d'inscriptions.

Vous pouvez utiliser ce panneau pour enregistrer une nouvelle identité d'homologue si vous déployez un [homologue distant](howto/remote_peer.html). Sinon, vous pouvez enregistrer un client si vous développez une application qui peut soumettre des transactions à votre réseau. Consultez le [tutoriel relatif au développement d'applications](v10_application.html) pour en savoir plus sur l'utilisation de logiciels SDK Fabric avec la plateforme.

### Génération de certificats côté client (inscription)
{: #enrollment}
Avant de connecter un client tiers à {{site.data.keyword.blockchainfull_notm}} Platform, vous devez doit être authentifié. Le processus de génération des certificats nécessaires, de votre clé privée et de votre certificat public (également appelé certificat d'inscription ou signCert) est appelé inscription. Ces certificats sont nécessaires chaque fois que votre client communique avec le réseau. Un client qui soumet des appels au réseau devra signer des contenus à l'aide d'une clé privée et joindre un certificat x509 correctement signé.

Consultez le [tutoriel relatif au développement d'applications](v10_application.html) pour en savoir plus sur l'[inscription à l'aide du logiciel SDK Fabric Node](v10_application.html). L'inscription avec le logiciel SDK génère un signCert, une clé publique qui a été utilisée pour créer le signCert, et votre clé privée.

Vous pouvez également générer des certificats depuis la [ligne de commande](#enroll-register-caclient) à l'aide du client d'autorité de certification Fabric. Ce dernier renvoie un jeu de certificats plus complet au sein d'un dossier MSP. Ce dossier contient le certificat racine signé par l'autorité de certification racine, les certificats intermédiaires liés à votre affiliation, une clé privée et votre signCert. Pour en savoir plus sur les MSP et le contenu de ce dossier, voir la section relative aux [Fournisseur de services aux membres (MSP)](#msp) ci-dessous.

### Envoi par téléchargement de certificats à {{site.data.keyword.blockchainfull_notm}} Platform
{: #upload-certs}

Une application requiert uniquement un signCert pour soumettre des transactions au réseau. Toutefois, si un client souhaite exploiter le réseau, en installant du code blockchain sur des homologues ou joindre des homologues à des canaux, par exemple, ce client doit être reconnu en tant qu'administrateur. Chaque composant reconnaît un ensemble de certificats signCerts détenus par un administrateur. Si vous devez exploiter votre réseau à partir d'un client, vous devrez envoyer par téléchargement votre signCert et l'ajouter à la liste des certificats d'admin. Pour cela, vous devez depuis la plateforme envoyer par téléchargement votre signCert sous l'onglet **Certificats** du [panneau "Présentation"](v10_dashboard.html#members) de votre Moniteur réseau. Synchronisez ce certificat sur vos homologues en appuyant sur le bouton de redémarrage qui apparaît après l'envoi par téléchargement. Par la suite, votre client peut être reconnu en tant qu'admin du réseau.

<!----
When you add an organization to a channel, this will upload the organization signCert to the channel MSP. Synching the Sign Certs does something.
--->

## Utilisation de certificats TLS
{: #tls}

[Transport Layer Security ![Icône de lien extere](images/external_link.svg "Icône de lien extere")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) est intégré au modèle de confiance d'Hyperledger Fabric. Tous les composants sur {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS pour s'authentifier et communiquer entre eux. Par conséquent, vous devez joindre un certificat TLS émis par la plateforme pour vos appels afin de valider et de chiffrer votre communication. Les autres certificats présentés dans ce tutoriel protègent votre capacité à effectuer des transactions avec le réseau et à gérer ce dernier. Les certificats TLS sont utilisés pour sécuriser vos appels vers le réseau.

Les certificats TLS sont émis publiquement par la plateforme et sont identiques pour tous les composants de votre réseau. Vous pouvez télécharger les certificats TLS avec les liens suivants, en fonction de votre plan d'appartenance de votre emplacement de cloud. Vous pouvez également trouver des certificats TLS dans votre [profil de données d'identification](v10_dashboard.html#enterprise-connection-profile "profil de données d'identification"). Ce certificat peut se trouver n'importe où, pourvu que vous puissiez y faire référence à partir de la ligne de commande ou de votre application.

- Certificat TLS racine pour le plan Starter  
  - Etats-Unis : [us01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - Royaume-Uni : [uk01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sydney : [aus01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
- [Root TLS Cert for Enterprise Plan ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

Tous les réseaux de {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS côté serveur, dans lequel le réseau doit authentifier vos clients. Les réseau du plan Enterprise peuvent aussi activer TLS mutuel, dans lequel le client et le serveur s'authentifient entre eux, pour sécuriser encore davantage vos applications. Les certificats TLS côté client (pour TLS mutuel) sont émis par l'autorité de certification du client et ils sont uniques pour votre réseau. Si vous utilisez un réseau du plan Enterprise, il est recommandé d'activer TLS mutuelle. Pour plus d'informations sur TLS mutuel, voir consultez les [](v10_dashboard.html#mutual-tls "instructions TLS mutuel").

## Fournisseur de services aux membres (MSP)
{: #msp}

Les composants de {{site.data.keyword.blockchainfull_notm}} Platform consomment des identités via des Fournisseur de services aux membres (MSP). Les MSP associent les certificats émis par les autorités de certification à des rôles de réseau et de canal. Pour plus d'informations sur les MSP, consultez la [rubrique relative au concept d'appartenance dans la documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Rubrique relative au concept d'appartenance dans la documentation Hyperledger Fabric").

Les dossiers MSP dans Fabric ont un structure définie. Lorsque vous effectuez une inscription à l'aide du client d'autorité de certification Fabric, le client stocke les certificats dans un dossier MSP sur votre système de fichiers local avec les sous-dossiers suivants :

- **cacerts :** Ce dossier contient le certificat racine de l'autorité de certification racine de votre réseau.
- **intermediatecerts :** Il s'agit des certificats des autorités de certification intermédiaires de votre réseau. Ces autorités de certification intermédiaires sont liées à l'autorité de certification racine et elles forment une chaîne de confiance. Chaque organisation du plan Enterprise dispose de deux autorités de certification intermédiaires pour la reprise et la haute disponibilité.
- **signcerts :** Ce dossier contient votre certificat signataire public, également appelé signCert ou certificat d'enregistrement. Ce certificat est joint à vos appels vers le réseau (un appel de code blockchain, par exemple) lorsque vous faite référence à votre répertoire MSP depuis la ligne de commande ou générez un objet de contexte utilisateur à l'aide de logiciels SDK. Vous pouvez télécharger ce certificat sur la plateforme si vous souhaitez exploiter un réseau à partir d'un logiciel SDK ou d'une ligne de commande.
- **keystore :** Ce dossier contient votre clé privée. Cette clé est utilisée pour signer vos appels vers le réseau lorsque vous faite référence à votre répertoire MSP depuis la ligne de commande ou générez un objet de contexte utilisateur à l'aide de logiciels SDK. Conservez cette clé en lieu sûr afin de protéger votre réseau et vos données.

Vous pouvez aussi créer un dossier MSP qui peut être référencé par le client d'autorité de certification Fabric à l'aide du Moniteur réseau et d'API swagger.

- **cacerts** et **intermediatecerts** : Vous pouvez extraire ces certificats avec les [API Swagger](howto/swagger_apis.html) en émettant une demande Get à l'API MSP.
- **signcerts** et **keystore** : Vous pouvez générer ces certificats en cliquant sur le bouton **Générer des certificats** dans le panneau "Autorité de certification". Une fenêtre en incrustation s'ouvre avec deux certificats répertoriés. Copiez et stockez le **certificat** dans signcerts et la **clé privée** dans le fichier de clés. Conservez ces certificats en lieu sûr car ils ne sont pas stockés sur la plate-forme.

De nombreux composants Fabric contiennent des informations supplémentaires dans leur dossier MSP. Par exemple, si vous exploitez un homologue distant, vous pouvez voir les dossiers suivants :

- **admincerts :** Ce dossier contient la liste des administrateurs pour cette organisation ou ce composant. Vous devrez envoyer par téléchargement votre certificat signataire dans ce dossier si vous exploitez un homologue distant à partir de la ligne de commande ou de logiciels SDK. 
- **tls :** Il s'agit d'un dossier dans lequel vous stockez les certificats TLS utilisés pour communiquer avec d'autres composants réseau.

Pour plus d'informations sur la structure des MSP, voir [Appartenance ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Appartenance") et [Fournisseur de services aux membres (MSP)![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Fournisseur de services aux membres (MSP)") dans la documentation Hyperledger Fabric.

## Enregistrement et inscription à l'aide du client d'autorité de certification Fabric
{: #enroll-register-caclient}

Vous pouvez également utiliser le client d'autorité de certification Fabric pour générer des certificats et enregistrer une nouvelle identité avec l'autorité de certification. Les instructions ci-dessous permettent de générer des certificats à l'aide de votre identité admin, puis d'utiliser ces certificats pour enregistrer un nouveau client. L'inscription à l'aide du client d'autorité de certification Fabric va générer les certificats qui sont décrits dans la [section relative aux fournisseurs de services d'appartenance](#msp).

### Inscription avec le client d'autorité de certification Fabric
{: #enroll-app-caclient}

1. Téléchargez les fichiers [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) sur votre machine locale, puis extrayez-les et placez-les dans un dossier tel que `$HOME/fabric-ca-platform/`. Accédez au répertoire dans lequel vous avez placé les fichiers binaires du client afin de pouvoir y faire référence directement dans vos commandes.
```
    cd $HOME/fabric-ca-platform/
    ```

2.  Définissez le chemin dans lequel le client peut créer vos clés. Vérifiez que vous avez supprimé la configuration matérielle pour une tentative de création préalable. S'il s'agit de la première fois que vous exécutez la commande `enroll`, le dossier `msp` et le fichier `.yaml` n'existent pas.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```

3. Téléchargez les certificats TLS depuis {{site.data.keyword.cloud_notm}} en fonction du plan de service, de l'emplacement et du cluster que vous utilisez.
  - Certificat TLS racine pour le plan Starter  
    - Etats-Unis : [us01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Royaume-Uni : [uk01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney : [aus01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Root TLS Cert for Enterprise Plan ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Sauvegardez le contenu dans un dossier, par exemple ``$HOME/tls``. Cette étape permet de chiffrer le flux de données sur la connexion.

4. Ouvrez le fichier JSON **Connection Profile** depuis le panneau "Présentation" dans le Moniteur réseau, puis recherchez les variables suivantes :
  * URL de l'autorité de certification : `url` sous `certificateAuthorities`
  * ID Admin : ``enrollId``
  * Mot de passe Admin : ``enrollSecret``
  * Nom de l'autorité de certification : ``caName``

5. Vous pouvez utiliser le client d'autorité de certification Fabric pour envoyer un appel `enroll` à l'autorité de certification en le transmettant le chemin de certificat TLS et les quatre chaînes ci-dessus avec la commande suivante :
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Vous devez exécuter cette commande dans le répertoire où vous avez déplacé le client d'autorité de certification Fabric. Un appel réel doit ressembler à ce qui suit dans l'exemple de commande :
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

6. Localisez votre certificat admin dans `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. Vous pouvez ensuite charger le certificat Admin sur votre réseau de blockchain depuis le moniteur réseau. Pour plus d'informations sur l'ajout de certificats, consultez [le panneau "Certificats" de l'écran "Membre"](v10_dashboard.html#members) dans le Moniteur réseau. 

  Vous pouvez également trouver le certificat racine de l'autorité de certification et la clé privée Admin dans les répertoires suivants :
  * Certificat racine de l'autorité de certification : `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * Clé privée admin : `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Pour voir un exemple d'inscription à l'aide du client de l'autorité de certification Fabric et de l'utilisation de certificats générés pour exploiter un composant réseau dans les instructions relatives à l'[exploitation d'un homologue distant](howto/remote_peer_operate_icp.html#remote-peer-cli-operate).

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
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
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

Chaque membre du réseau possède sa propre autorité de certification. L'autorité de certification de votre organisation signe des demandes pour toutes les entités et tous les composants dont votre organisation est propriétaire, par exemple votre admin, vos homologues ou vos applications. Si vous voulez ajouter un homologue distant ou une nouvelle application à votre réseau, vous devez enregistrer la nouvelle identité auprès de votre autorité de certification (enregistrement). Ensuite, l'autorité de certification peut fournir la nouvelle entité avec les certificats dont elle aura besoin pour interagir avec le réseau (inscription).

### Enregistrement auprès du Moniteur réseau
{: #ca-panel}

Vous pouvez utiliser le Moniteur réseau de {{site.data.keyword.blockchainfull_notm}} Platform pour consulter les identités qui sont enregistrées auprès de votre autorité de certification et en ajouter de nouvelles. Pour cela, accédez au panneau "Autorité de certification" du Moniteur réseau. Ce panneau affiche toutes les identités qui ont été enregistrés auprès de votre autorité de certification, y compris l'admin, les homologues et les applications client de votre organisation. Pour enregistrer une nouvelle identité dans votre organisation, cliquez sur le bouton **Ajouter un utilisateur** dans le panneau. Une fenêtre en incrustation s'ouvre et affiche les zones suivantes qui sont nécessaires pour l'enregistrement d'une nouvelle identité.
  - **ID d'inscription :** Il s'agit du nom de votre nouvelle identité, parfois appelée `ID inscription`. **Sauvegardez cette valeur** à des fins de configuration d'un homologue distant ou d'inscription d'une nouvelle application.
  - **Secret d'inscription :** Il s'agit du mot de passe de votre identité, parfois appelé `Secret d'inscription`. **Sauvegardez cette valeur** à des fins de configuration d'un homologue distant ou d'inscription d'une nouvelle application.
  - **Type :** Sélectionnez le type d'identité que vous voulez enregistrer, qu'il s'agisse d'un homologue ou d'une application client.
  - **Affiliation :** Il doit s'agir de l'affiliation au sein de votre organisation, par exemple `org1`, à laquelle l'identité va appartenir.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si vous laissez cette zone vide, la valeur par défaut est un nombre illimité d'inscriptions.

Vous pouvez utiliser ce panneau pour enregistrer une nouvelle identité d'homologue si vous déployez un [homologue distant](/docs/services/blockchain/howto/remote_peer.html). Sinon, vous pouvez enregistrer un client si vous développez une application qui peut soumettre des transactions à votre réseau. Consultez le [tutoriel relatif au développement d'applications](/docs/services/blockchain/v10_application.html) pour en savoir plus sur l'utilisation de logiciels SDK Fabric avec la plateforme.

### Génération de certificats côté client (inscription)
{: #enrollment}
Avant de connecter un client tiers à {{site.data.keyword.blockchainfull_notm}} Platform, vous devez doit être authentifié. Le processus de génération des certificats nécessaires, de votre clé privée et de votre certificat public (également appelé certificat d'inscription ou signCert) est appelé inscription. Ces certificats sont nécessaires chaque fois que votre client communique avec le réseau. Un client qui soumet des appels au réseau devra signer des contenus à l'aide d'une clé privée et joindre un certificat x509 correctement signé.

Consultez le [tutoriel relatif au développement d'applications](/docs/services/blockchain/v10_application.html) pour en savoir plus sur l'[inscription à l'aide du logiciel SDK Fabric Node](/docs/services/blockchain/v10_application.html#enroll-app). L'inscription avec le logiciel SDK génère 3 éléments distincts : une clé publique, un certificat signCert, et une clé privée qui a été utilisée pour créer le certificat signCert.

Vous pouvez également générer des certificats depuis la ligne de commande à l'aide du [client d'autorité de certification Fabric](#enroll-register-caclient). Ce dernier renvoie un jeu de certificats plus complet au sein d'un dossier MSP. Ce dossier contient le certificat racine signé par l'autorité de certification racine, les certificats intermédiaires, une clé privée et votre certificat signCert. Pour plus d'informations sur les dossiers MSP et leur contenu, voir [Fournisseur de services aux membres (MSP)](#msp).

Vous pouvez générer des certificats uniquement à l'aide d'identités qui ont été enregistrés auprès de votre autorité de certification, à l'aide du nom et du secret de cette identité. Par défaut, une identité **admin** est déjà enregistrée auprès de votre autorité de certification et elle est répertoriée à l'écran "Autorité de certification". Vous pouvez trouver le secret de l'identité admin dans votre profil de connexion en cliquant sur le bouton **Profil de connexion** à l'écran "Présentation" de votre Moniteur réseau. Vous pouvez également enregistrer une nouvelle identité en cliquant sur le bouton [Ajouter un utilisateur](#ca-panel) à l'écran "Autorité de certification" du Moniteur réseau, puis générer des certificats avec le nom et le secret de la nouvelle identité.

**Remarque :** Si vous suivez les instructions permettant de générer des certificats à l'aide du logiciel SDK Fabric Node ou du client d'autorité de certification Fabric ci-dessus, vous commencez par vous inscrire à l'aide de l'identité admin. Vous utilisez ensuite ces certificats pour enregistrer une nouvelle identité client auprès de votre autorité de certification. Si vous utilisez les instructions de logiciel SDK dans la section relative au [développement d'applications](/docs/services/blockchain/v10_application.html), vous devez vous inscrire à nouveau à l'aide de l'identité client. Vous utiliserez ensuite ces certificats pour soumettre des transactions sur le réseau. <!---You can an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

### Génération de certificats à l'aide du Moniteur réseau
{: #certs-panel}

Vous pouvez utiliser le Moniteur réseau pour générer des certificats à l'aide de l'identité de l'admin, puis transmettre ces certificats directement au logiciel SDK. Cliquez sur le bouton **Génération de certificat** en regard de votre identité admin pour obtenir un nouveau certificat signCert et une clé privée auprès de votre autorité de certification. La zone **Certificate** contient le certificat signCert, juste au-dessus de la **Clé privée**. Vous pouvez cliquer sur l'icône copier à la fin de chaque champ pour copier la valeur. Vous devez ensuite sauvegarder ces certificats dans un endroit où vous pouvez ensuite les importer dans votre application. Pour plus d'informations, voir le [tutoriel relatif au développement d'applications](/docs/services/blockchain/v10_application.html#enroll-panel). **Notez** qu'{{site.data.keyword.blockchainfull_notm}} Platform ne stocke pas ces certificats. Vous devez les enregistrer et les stocker en lieu sûr.

### Envoi par téléchargement de certificats à {{site.data.keyword.blockchainfull_notm}} Platform
{: #upload-certs}

Une application requiert uniquement un certificat signCert valide pour soumettre des transactions sur le réseau. Toutefois, si un client souhaite exploiter le réseau, en installant du code blockchain sur des homologues ou joindre des homologues à des canaux, par exemple, ce client doit être reconnu en tant qu'administrateur. Chaque composant reconnaît un ensemble de certificats signCerts détenus par un administrateur. Si vous devez exploiter votre réseau à partir d'un client, vous devez envoyer par téléchargement votre certificat signCert et l'ajouter à la liste des certificats d'admin. Pour cela, vous devez depuis la plateforme envoyer par téléchargement votre signCert sous l'onglet **Certificats** du [panneau "Présentation"](/docs/services/blockchain/v10_dashboard.html#members) de votre Moniteur réseau. Synchronisez ce certificat sur vos homologues en appuyant sur le bouton de redémarrage qui apparaît après l'envoi par téléchargement. Par la suite, votre client pourra exploiter le réseau. Vous pouvez également envoyer par téléchargement votre certificat signCert à l'aide de l'[API Swagger](/docs/services/blockchain/howto/swagger_apis.html) pour ajouter un certificat admin.

Les canaux reconnaissent également un ensemble de certificats admin à partir des identités qui sont autorisées à utiliser le canal, y compris la capacité d'instancier un code blockchain sur le canal. Si vous utilisez un nouveau certificat signCert avec un client distant, vous devez synchroniser ce certificat sur le canal pour pouvoir instancier un code blockchain. Procédez comme suit dans le Moniteur réseau pour ajouter le certificat au canal :

1. Accédez à l'onglet **Certificats** de l'écran "Membres". Cliquez sur le bouton **Ajouter un certificat** pour envoyer par téléchargement le certificat signCert à la plateforme.
2. Accédez à l'écran "Canaux" et recherchez le nom du canal correspondant.
3. Cliquez sur **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Actions** pour ajouter le nouveau certificat à la liste des certificats admin de canal.

**Remarque :** Les certificats qui sont générés à l'aide de l'identité admin de votre organisation ne sont pas automatiquement considérés certificats admin. Un certificat signCert et une clé privée qui sont générés à l'aide du bouton **Générer des certificats** ne permettront pas à votre logiciel SDK d'utiliser le réseau. Ils ont été générés par le client d'autorité de certification Fabric, le logiciel SDK, ou le Moniteur réseau, et ils n'ont aucune connexion à la liste pré-existante de certificats admin reconnus par les composants réseau. Vous devez envoyer par téléchargement le certificat signCert au Moniteur réseau pour que votre logiciel SDK puisse utiliser le réseau.

## Utilisation de certificats TLS
{: #tls}

[Transport Layer Security ![Icône de lien extere](images/external_link.svg "Icône de lien extere")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) est intégré au modèle de confiance d'Hyperledger Fabric. Tous les composants sur {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS pour s'authentifier et communiquer entre eux. Par conséquent, vous devez joindre un certificat TLS émis par la plateforme pour vos appels afin de valider et de chiffrer votre communication. Les autres certificats présentés dans ce tutoriel protègent votre capacité à effectuer des transactions avec le réseau et à gérer ce dernier. Les certificats TLS sont utilisés pour sécuriser vos appels vers le réseau.

Les certificats TLS sont émis publiquement par la plateforme et sont identiques pour tous les composants de votre réseau. Vous pouvez télécharger les certificats TLS avec les liens suivants, en fonction de votre plan d'appartenance de votre emplacement de cloud. Vous pouvez également trouver des certificats TLS dans votre [profil de données d'identification](/docs/services/blockchain/v10_dashboard.html#connection-profile "profil de données d'identification"). Ce certificat peut se trouver n'importe où, pourvu que vous puissiez y faire référence à partir de la ligne de commande ou de votre application.

- Certificat TLS racine pour le plan Starter
  - Etats-Unis : [us01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - Royaume-Uni : [uk01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sydney : [aus01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
- [Root TLS Cert for Enterprise Plan ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

Tous les réseaux de {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS côté serveur, dans lequel le réseau doit authentifier vos clients. Les réseau du plan Enterprise peuvent aussi activer TLS mutuel, dans lequel le client et le serveur s'authentifient entre eux, pour sécuriser encore davantage vos applications. Les certificats TLS côté client (pour TLS mutuel) sont émis par l'autorité de certification du client et ils sont uniques pour votre réseau. Si vous utilisez un réseau du plan Enterprise, il est recommandé d'activer TLS mutuelle. Pour plus d'informations sur TLS mutuel, voir consultez les [](/docs/services/blockchain/v10_dashboard.html#mutual-tls "instructions TLS mutuel").

### Extraction du nom de domaine à partir de certificats TLS

Lorsque vous communiquez avec un composant Hyperledger Fabric qui se situe à l'extérieur d'{{site.data.keyword.blockchainfull_notm}} Platform, votre appel doit être soumis à l'aide d'un nom de domaine correct. Si un appel est soumis à l'adresse IP d'un composant sans la résolution de nom de domaine du composant, l'appel sera rejeté et il renvoie une erreur.

Vous pouvez rechercher l'URL complète et le nom de domaine des composants hébergés sur la plateforme dans votre profil de connexion. L'exemple suivant illustre l'URL complète d'un homologue, qui est concaténée avec le nom de domaine de l'homologue, `us01.blockchain.ibm.com`.
```
grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us01.blockchain.ibm.com:31002"
```

Vous pouvez également rechercher le nom de domaine d'un composant à partir de son certificat TLS.

1. Téléchargez l'un des certificats TLS racine de la liste ci-dessus et sauvegardez-le sur votre machine locale.
2. Dans le même répertoire que les certificats TLS racine, exécutez la commande suivante. Cette commande permet d'afficher le certificat dans une forme lisible en ligne de commande. Vous pouvez ensuite trouver d'importantes informations, telles que le nom de domaine, sur la ligne de commande.
  ```
  openssl x509 -in <certificate file> -text
  ```
  {:codeblock}

Par exemple, si vous téléchargez le premier certificat répertorié, puis exécutez la commande suivante, `openssl x509 -in us01.blockchain.ibm.com.cert -text`, vous pouvez voir le code suivant dans la sortie.

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            05:5c:42:fb:90:b9:cb:3d:60:7c:e4:ec:7f:7f:8e:38
    Signature Algorithm: ecdsa-with-SHA256
        Issuer: C=US, O=DigiCert Inc, CN=DigiCert ECC Secure Server CA
        Validity
            Not Before: Jun 11 00:00:00 2018 GMT
            Not After : Jun 19 12:00:00 2019 GMT
        Subject: C=US, ST=New York, L=Armonk, O=International Business Machines Corporation, CN=*.us01.blockchain.ibm.com
    ...
    ...
```

Le nom de domaine est indiqué sur la ligne d'objet sous la forme `CN=*.us01.blockchain.ibm.com`. Vous pouvez également rechercher des noms de domaine secondaires qui sont répertoriés plus bas dans le certificat.

L'extraction du nom de domaine d'un composant à partir des certificats TLS peut être utile si vous communiquer avec un homologue distant ou un composant Fabric qui est hébergé à l'extérieur de la plateforme {{site.data.keyword.blockchainfull_notm}} . Vous pouvez ensuite ajouter le nom de domaine à une substitution SSL lorsque vous utilisez des logiciels SDK, ou ajouter le nom de domaine et l'adresse IP correspondante à votre fichier `etc.hosts` fichier.

## Fournisseur de services aux membres (MSP)
{: #msp}

Les composants de {{site.data.keyword.blockchainfull_notm}} Platform consomment des identités via des Fournisseur de services aux membres (MSP). Les MSP associent les certificats émis par les autorités de certification à des rôles de réseau et de canal. Pour plus d'informations sur les MSP, consultez la [rubrique relative au concept d'appartenance dans la documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Rubrique relative au concept d'appartenance dans la documentation Hyperledger Fabric").

Les dossiers MSP dans Fabric ont un structure définie. Lorsque vous effectuez une inscription à l'aide du client d'autorité de certification Fabric, le client stocke les certificats dans un dossier MSP sur votre système de fichiers local avec les sous-dossiers suivants :

- **cacerts :** Ce dossier contient le certificat racine de l'autorité de certification racine de votre réseau.
- **intermediatecerts :** Il s'agit des certificats des autorités de certification intermédiaires de votre réseau. Ces autorités de certification intermédiaires sont liées à l'autorité de certification racine et elles forment une chaîne de confiance. Chaque organisation du plan Enterprise dispose de deux autorités de certification intermédiaires pour la reprise et la haute disponibilité.
- **signcerts :** Ce dossier contient votre certificat signataire public, également appelé signCert ou certificat d'enregistrement. Ce certificat est joint à vos appels vers le réseau (un appel de code blockchain, par exemple) lorsque vous faite référence à votre répertoire MSP depuis la ligne de commande ou générez un objet de contexte utilisateur à l'aide de logiciels SDK. Vous pouvez télécharger ce certificat sur la plateforme si vous souhaitez exploiter un réseau à partir d'un logiciel SDK ou d'une ligne de commande.
- **keystore :** Ce dossier contient votre clé privée. Cette clé est utilisée pour signer vos appels vers le réseau lorsque vous faite référence à votre répertoire MSP depuis la ligne de commande ou générez un objet de contexte utilisateur à l'aide de logiciels SDK. Conservez cette clé en lieu sûr afin de protéger votre réseau et vos données.

Vous pouvez aussi créer un dossier MSP qui peut être référencé par le client d'autorité de certification Fabric à l'aide du Moniteur réseau et des API Swagger.

- **cacerts** et **intermediatecerts** : Vous pouvez extraire ces certificats avec les [API Swagger](/docs/services/blockchain/howto/swagger_apis.html) en émettant une demande Get à l'API MSP.
- **signcerts** et **keystore** : Vous pouvez générer ces certificats en cliquant sur le bouton **Générer des certificats** dans le panneau "Autorité de certification". Une fenêtre en incrustation s'ouvre avec deux certificats répertoriés. Copiez et stockez le **certificat** dans signcerts et la **clé privée** dans le fichier de clés. Conservez ces certificats en lieu sûr car ils ne sont pas stockés sur la plate-forme.

De nombreux composants Fabric contiennent des informations supplémentaires dans leur dossier MSP. Par exemple, si vous exploitez un homologue distant, vous pouvez voir les dossiers suivants :

- **admincerts :** Ce dossier contient la liste des administrateurs pour cette organisation ou ce composant. Vous devrez envoyer par téléchargement votre certificat signCert dans ce dossier si vous exploitez un homologue distant à partir de la ligne de commande ou de logiciels SDK. Si vous utilisez le client d'autorité de certification Fabric, vous avez également besoin d'un dossier admincerts dans votre MSP qui contienne le certificat signCert correspondant qui doit être reconnu en tant que certificat administrateur.
- **tls :** Il s'agit d'un dossier dans lequel vous stockez les certificats TLS utilisés pour communiquer avec d'autres composants réseau.

Pour plus d'informations sur la structure des MSP, voir [Appartenance ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Appartenance") et [Fournisseur de services aux membres (MSP)![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Fournisseur de services aux membres (MSP)") dans la documentation Hyperledger Fabric.

## Inscription et enregistrement à l'aide du client d'autorité de certification Fabric
{: #enroll-register-caclient}

Vous pouvez également utiliser le client d'autorité de certification Fabric pour générer des certificats et enregistrer une nouvelle identité avec l'autorité de certification. Les instructions ci-dessous permettent de générer des certificats à l'aide de votre identité admin, puis d'utiliser ces certificats pour enregistrer un nouveau client. Pour plus d'informations sur l'inscription à l'aide du client d'autorité de certification Fabric et la génération de certificats, voir [Membership Services Providers](#msp).

### Inscription avec le client d'autorité de certification Fabric
{: #enroll-app-caclient}

1. Téléchargez les fichiers [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) sur votre machine locale, puis extrayez-les et placez-les dans un dossier tel que `$HOME/fabric-ca-platform/`. Accédez au répertoire dans lequel vous avez placé les fichiers binaires du client afin de pouvoir y faire référence directement dans vos commandes.
    ```
    cd $HOME/fabric-ca-platform/
    ```
    {:codeblock}

2.  Définissez le chemin dans lequel le client peut créer vos clés. Vérifiez que vous avez supprimé la configuration matérielle pour une tentative de création préalable. S'il s'agit de la première fois que vous exécutez la commande `enroll`, le dossier `msp` et le fichier `.yaml` n'existent pas.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3. Téléchargez les certificats TLS depuis {{site.data.keyword.cloud_notm}} en fonction du plan de service, de l'emplacement et du cluster que vous utilisez. Vous pouvez rechercher votre plan de service basé sur l'URL de votre autorité de certification.
  - Certificat TLS racine pour le plan Starter
    - Etats-Unis : [us01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Royaume-Uni : [uk01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney : [aus01.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
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

6. Localisez votre certificat admin dans `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem`. Vous pouvez ensuite charger le certificat Admin sur votre réseau de blockchain depuis le moniteur réseau. Pour plus d'informations sur l'ajout de certificats, consultez [le panneau "Certificats" de l'écran "Membre"](/docs/services/blockchain/v10_dashboard.html#members) dans le Moniteur réseau.

  Vous pouvez également trouver le certificat racine de l'autorité de certification et la clé privée Admin dans les répertoires suivants :
  * Certificat racine de l'autorité de certification : `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * Clé privée admin : `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Pour voir un exemple d'inscription à l'aide du client d'autorité de certification Fabric et de l'utilisation de certificats générés pour exploiter un composant réseau dans les instructions relatives à l'[exploitation d'un homologue distant](/docs/services/blockchain/howto/peer_operate_icp.html#peer-cli-operate).

### Enregistrement avec le client d'autorité de certification Fabric
{: #register-app-caclient}

1. Exécutez la commande suivante pour rechercher votre affiliation et le nom de votre organisation dans votre réseau de blockchain.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Vous devez voir des informations similaires à l'exemple suivant :
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```

  Notez la seconde valeur **affiliation**, par exemple, `ibp.PeerOrg1`. Vous devez utiliser cette valeur dans la commande ci-dessous.

2. Exécutez la commande suivante enregistrer l'homologue.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Indiquez un nom et un mot de passe pour votre homologue et remplacez `name` et `password` par le nom et le mot de passe de votre homologue. Notez ces informations. Vous en aurez besoin lorsque vous configurerez votre homologue. Par exemple :
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

  Vous ne pouvez enregistrer une identité qu'une seule fois. Si vous rencontrez un problème, essayez d'exécuter une commande avec un nouveau nom d'utilisateur et un nouveau mot de passe.

  Lorsque la commande aboutit, vous pouvez voir des informations semblables à l'exemple suivant :
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```

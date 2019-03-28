---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Déploiement d'un réseau d'entreprise dans un plan Enterprise
{: #deploying-a-business-network}

**IBM recommande d'utiliser Hyperledger Composer uniquement pour les démonstrations et les validations de concept. IBM n'assure pas de support pour les réseaux utilisant Hyperledger Composer en production, y compris l'interface CLI Composer, les API JavaScript, le serveur REST et l'aire de jeu Web.**

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Les outils de développeur d'{{site.data.keyword.blockchainfull}} Platform: Develop vous permettent de créer une **définition de réseau d'entreprise** qui peut ensuite être packagée dans une archive de réseau d'entreprise (fichier `.bna`). L'environnement de développeur vous permet de déployer des fichiers `.bna` dans un {{site.data.keyword.blockchain}} local ou cloud à des fins de développement et de partage.

Ce tutoriel concerne l'étape suivante du cycle de vie d'un réseau d'entreprise, qui consiste à activer votre réseau d'entreprise en déployant le fichier `.bna` dans le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform.

## Avant de commencer

Vous devez vérifier que vous avez installé l'environnement de développeur de {{site.data.keyword.blockchainfull_notm}}. Vous devez aussi savoir comment développer et déployer des réseaux d'entreprise. Pour plus de détails sur la création de réseaux d'entreprise, vous pouvez consulter la [documentation Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Vous devez accéder à une instance du plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform et vous devez avoir créé vos homologues au préalable. Pour plus d'informations sur le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform, voir [Présentation du plan Enterprise](./enterprise_plan.html).

## Etape 1 : Créer un profil de connexion pour {{site.data.keyword.blockchainfull_notm}} Platform

1. Créez un répertoire dans lequel stocker vos détails de connexion, par exemple :

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Chaque profil de connexion doit contenir un fichier `connection.json`. Créez un nouveau répertoire sous `.composer-connection-profiles`, dans cet exemple `bmx-hlfv11`. Il s'agira du nom du profil que vous allez utiliser lors de l'utilisation de Hyperledger Composer et d'{{site.data.keyword.blockchainfull_notm}}. Platform

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Vous devez avoir maintenant la structure de répertoire suivante :

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

## Etape 2 : Extraire votre profil de connexion

1. Depuis le tableau de bord de {{site.data.keyword.blockchainfull_notm}} Platform, sélectionnez **Présentation**, puis **Profil de connexion ** et le bouton **Télécharger** pour extraire votre profil de connexion.

2. Sauvegardez le profil de connexion dans la structure de répertoire créée à l'étape précédente. Nommez-le **connection.json**.

## Etape 3 : Ajouter les informations de canal

1. Ajoutez la valeur channel du fichier `connection.json` afin qu'elle corresponde au nom du canal dans lequel vous allez créer et déployer votre réseau d'entreprise. Dans le modèle de profil de connexion fourni en exemple, l'élément channels est défini comme suit : `"channels": {},`.

## Etape 4 : Préparer vos homologues

**Remarque **: Cette étape **doit** être accomplie avant la création du canal pour le déploiement d'un réseau d'entreprise. Dans le cas contraire, un réseau d'entreprise déployé **ne démarrera peut-être pas**.

Dans le profil de connexion sous **certificateAuthorities** figure un attribut **registrar** qui contient les attributs des valeurs **enrollId** et **enrollSecret** au format suivant :

 ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```

1. Créez une carte réseau d'entreprise pour l'autorité de certification à l'aide de la commande suivante :

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Vérifiez que vous exécutez la commande dans le même répertoire que votre profil de connexion.

2. Importez la carte de réseau d'entreprise à l'aide de la commande suivante :

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. Une fois la carte importée, elle peut être utilisée pour acquérir les certificats de l'autorité de certification à l'aide de la commande suivante :

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    La commande `composer identity request` crée un répertoire credentials qui contient les fichiers certificats pertinents.

4. Sélectionnez **Membres** dans le menu de navigation, puis choisissez l'option de menu **Certificats** et cliquez sur le bouton **Ajouter un certificat**.

5. Entrez un nom unique pour ce certificat dans la zone **Nom**. Ce nom ne peut pas comporter des tirets ou des traits d'union.

6. Ouvrez le fichier `admin-pub.pem` créé plus haut et copiez son contenu dans la zone **Clé** et cliquez sur le bouton **Soumettre**.

7. A partir de l'interface utilisateur, arrêtez puis redémarrez les homologues.

8. Le certificat doit maintenant figurer dans la liste des certificats.

## Etape 5 : Créer votre canal

1. Sélectionnez **Canaux** dans le menu de navigation de gauche et cliquez sur le bouton **Nouveau canal**.

2. Entrez un nom de canal et une description facultative, puis cliquez sur **Suivant**. Notez que le nom de canal doit correspondre au nom que vous avez indiqué dans votre profil de connexion de l'attribut channel.

3. Accordez les droits nécessaires et cliquez sur **Suivant**.

4. Sélectionnez la règle relative au nombre d'opérateurs qui doivent accepter les mises à jour de canal et soumettre la demande.

5. Vous devez ensuite être redirigé vers la section **Notifications** où une nouvelle demande doit être passée en revue. Cliquez sur le bouton **Inspecter la demande**.

6. Cliquez sur le bouton **Accepter** afin de revenir à la section **Notifications** où vous pouvez voir que la demande peut maintenant être soumise. Cliquez sur le bouton **Soumettre une demande** afin d'ouvrir la boîte de dialogue de soumission, puis cliquez sur le bouton **Soumettre**. Le nouveau canal est créé.

7. Sélectionnez **Canaux** dans le menu de navigation. Le nouveau canal apparaît dans la liste des canaux et il doit indiquer “Aucun homologue encore ajouté”. Cliquez sur le menu des actions et sélectionnez **Rejoindre des homologues**. Cochez les cases en regard des homologues à ajouter et cliquez sur **Ajouter la sélection**.

## Etape 6 : Créer une nouvelle identité pour administrer votre réseau d'entreprise

Créez une carte de réseau d'entreprise à l'aide des certificats demandés. Cette carte de réseau d'entreprise doit avoir le droit d'installer du code blockchain sur les homologues qui comportent votre certificat public téléchargé et il s'agira d'un émetteur pour les autorités de certification.

1. Pour créer la carte, exécutez la commande suivante :

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    Où `bmx-hlfv11` est le nom du profil que vous avez préalablement créé.

2. Une fois la carte créée, importez-la à l'aide de la commande suivante :

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    Tout est maintenant prêt pour le déploiement de votre fichier `.bna` sur {{site.data.keyword.blockchainfull_notm}}. Platform


## Etape 7 : Déployer le réseau d'entreprise

Vous pouvez maintenant déployer votre fichier `.bna` sur {{site.data.keyword.blockchainfull_notm}} Platform.

1. Pour utiliser la carte qui est créée à l'étape précédente, vous devez d'abord installer puis démarrer le réseau d'entreprise. Installez le réseau d'entreprise à l'aide de la commande suivante :

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. Une fois le réseau d'entreprise installé, démarrez-le à l'aide de la commande suivante :

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Pour vérifier que le réseau d'entreprise a été déployé correctement, créez une identité et une carte de réseau d'entreprise qui peuvent être utilisés pour effectuer un ping sur le réseau.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. Importez la carte de réseau d'entreprise à l'aide de la commande suivante :

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. Effectuez un ping du réseau pour vérifier que celui-ci est opérationnel :

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}

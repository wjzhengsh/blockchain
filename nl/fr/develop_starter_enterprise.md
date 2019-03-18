---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Déploiement de réseaux d'entreprise dans un plan Starter et Enterprise
{: #deploying-a-business-network}

{{site.data.keyword.IBM}} n'assure pas de support pour les réseaux utilisant Hyperledger Composer en production, y compris l'interface CLI Composer, les API JavaScript, le serveur REST et l'aire de jeu Web.
{:note}

Des [réseaux d'entreprise](/docs/services/blockchain/glossary.html#glossary-business-network) peuvent être développés et déployés dans un des réseaux de plan Starter et Enterprise à l'aide de l'environnement développeur d'{{site.data.keyword.blockchainfull_notm}} Platform et du jeu d'outils de développeur Hyperledger Composer.
{:shortdesc}

Avec l'environnement développeur, vous pouvez rapidement modéliser et tester des réseaux d'entreprise de blockchain et les déployer dans un réseau de plan Starter ou Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform.

## Déploiement d'un réseau d'entreprise dans un plan Starter
{: #deploying-a-business-network-starter-plan}

### Avant de commencer
{: #deploying-a-business-network-before-begin}

Vous devez avoir la section [A propos du plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about) et créé un réseau de plan Starter à l'aide des instructions de la section [Mise en route avec le plan Starter](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).

Assurez-vous d'avoir installé Node version 8.9 ou suivante, npm version 5.x et Hyperledger Composer :

- Si votre réseau est en version 1.2 de Fabric, utilisez Hyperledger Composer v0.20.x.
- Si votre réseau est en version 1.1 de Fabric, utilisez Hyperledger Composer v0.19.x.

Pour connaître votre version Fabric, ouvriez la [fenêtre Préférences réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) dans votre Moniteur réseau.

### Etape 1 : Extraire la valeur confidentielle admin
{: #deploying-a-business-network-retrieve-admin-secret}

1. Depuis l'écran de présentation du plan Starter, cliquez sur **Profil de connexion** puis procédez au téléchargement. Renommez ce fichier 'connection-profile.json'.

2. Déplacez ce fichier afin qu'il soit situé sous le même répertoire que votre fichier `.bna`.

3. Dans le profil de connexion, accédez à la section 'registrar'. Dans 'registrar', sous 'enrollId' figure une propriété **enrollSecret**. Récupérez la valeur confidentielle et enregistrez une copie de celle-ci.

    ![Extraire la valeur confidentielle admin](images/get_enroll_secret.gif "Extraire la valeur confidentielle admin")

### Etape 2 : Créer une carte d'autorité de certification
{: #deploying-a-business-network-CA-card}

La valeur confidentielle récupérée à l'étape précédente doit être utilisée pour créer une carte de réseau d'entreprise pour l'autorité de certification (AC). La Carte AC peut ensuite être importée et elle sera utilisée pour l'échange de **enrollSecret** pour les certificats valides de l'autorité de certification du plan Starter.

1. A l'aide de la valeur **enrollSecret** notée à l'étape 1, exécutez la commande suivante pour créer la carte de réseau d'entreprise AC :

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  Remplacez `enrollSecret` dans la commande précédente par la valeur confidentielle admin extraite du profil de connexion.

2. Importez la carte à l'aide de la commande suivante :

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. A présent que la carte est importée, elle peut être utilisée pour échanger la valeur **enrollSecret** pour les certificats valides de l'autorité de certification. Exécutez la commande suivante pour demander des certificats de l'autorité de certification :

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  Remplacez `enrollSecret` dans la commande précédente par la valeur confidentielle admin extraite du profil de connexion. La commande `composer identity request` crée un répertoire `credentials` qui contient les fichiers `.pem` du certificat.

### Etape 3 : Ajouter les certificats à l'instance de plan Starter
{: #deploying-a-business-network-add-certs-to-starter-plan}

Les certificats doivent être ajoutés au réseau de plan Starter. Pour plus de commodité, vous pouvez les ajouter à l'aide du Moniteur réseau d'{{site.data.keyword.blockchainfull_notm}} Platform. Les certificats doivent être ajoutés, puis les homologues doivent être redémarrés, et les certificats synchronisés sur le canal. Le certificat nécessaire est le fichier `admin-pub.pem` qui a été généré à partir de la commande précédente, qui se trouve dans le répertoire `credentials`.

1. Dans le Moniteur réseau du plan Starter, cliquez sur l'onglet **Membres**, **Certificats**, puis **Ajouter un certificat**. Accédez à votre répertoire `credentials`, puis copiez et collez le contenu du fichier `admin-pub.pem` dans la zone de certificat. Soumettez le certificat et redémarrez les homologues. Remarque : le redémarrage des homologues prend une minute.

    ![Ajouter des certificats](images/add_cert.gif "Ajouter des certificats")

2. Ensuite, les certificats doivent être synchronisés sur le canal. Cliquez sur l'onglet **Canaux**, puis sur le bouton **Actions**, puis sur **Synchroniser le certificat** et **Soumettre**.

    ![Synchroniser des certificats](images/sync_cert.gif "Synchroniser des certificats")

### Etape 4 : Créer une carte de réseau d'entreprise admin
{: #deploying-a-business-network-create-admin-card}

A présent que les certificats corrects sont synchronisés avec les homologues, les cartes de réseau d'entreprise peuvent être créées et elles donnent le droit d'installer l'environnement d'exécution Hyperledger Composer et de démarrer le code blockchain.

1. Créez une carte admin avec les rôles d'admin de canal et d'admin d'homologue à l'aide de la commande suivante :

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   Cette carte sera utilisée pour déployer un réseau d'entreprise dans un plan Starter.

2. Importez la carte créée à l'étape suivante à l'aide de la commande suivante :

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### Etape 5 : Installer et démarrer le réseau d'entreprise
{: #deploying-a-business-network-install-start-network}

Ensuite, la carte créée à l'étape précédente peut être utilisée pour installer et démarrer un réseau d'entreprise. Dans le cadre de ce guide, nous allons installer l'exemple réseau de fabrication de véhicules, utiliser cet exemple ou installer votre propre réseau d'entreprise, mais en nous assurant de modifier les noms de réseau d'entreprise spécifiés dans les commandes. La commande utilisée pour démarrer un réseau d'entreprise crée également une carte. Pour le plan Starter, cette carte doit être supprimée ; l'exemple de commande nomme cette carte `delete_me.card`, il est donc facile de le repérer.

1. Installez l'environnement d'exécution Hyperledger Composer à l'aide de la commande suivante :

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   Notez le numéro de version du réseau d'entreprise qui est renvoyé lors de l'exécution de cette commande. Il vous sera demandé à l'étape suivante.

2. Démarrez le réseau d'entreprise à l'aide de la commande ci-dessous. Si vous obtenez un message d'erreur, attendez environ une minute et réessayez. Utilisez le numéro de version de la dernière étape après l'option `-V`.

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Supprimez la carte de réseau d'entreprise nommée `delete_me.card`.

4. Créez une nouvelle carte de réseau d'entreprise et référencez les certificats qui ont été extraits plus haut à l'aide de la commande suivante :

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. Importez la carte de réseau d'entreprise à l'aide de la commande suivante :

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

Le réseau d'entreprise est à présent déployé dans l'instance de plan Starter.

### Etape six : Effectuer un ping du réseau d'entreprise afin de vérifier qu'il fonctionne correctement
{: #deploying-a-business-network-ping-business-network}

Exécutez la commande suivante pour effectuer un ping du réseau d'entreprise :

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Pour consulter les journaux de code blockchain, cliquez sur **Canaux**, puis sélectionnez votre canal. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> Cliquez sur l'onglet **Code blockchain**. Développez la ligne de code blockchain, puis cliquez sur le bouton **JSON** ou **Journaux**.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

Le réseau d'entreprise est déployé dans l'instance de plan Starter.

## Déploiement d'un réseau d'entreprise dans un plan Enterprise
{: #deploying-a-business-network-to-enterprise-plan}

Les outils de développeur d'{{site.data.keyword.blockchainfull_notm}} Platform vous permettent de créer une **définition de réseau d'entreprise** qui peut ensuite être packagée dans une archive de réseau d'entreprise (fichier `.bna`). L'environnement de développeur vous permet de déployer des fichiers `.bna` dans un {{site.data.keyword.blockchain}} local ou cloud à des fins de développement et de partage.

Ce tutoriel concerne l'étape suivante du cycle de vie d'un réseau d'entreprise, qui consiste à activer votre réseau d'entreprise en déployant le fichier `.bna` dans le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform.

### Avant de commencer
{: #deploying-a-business-network-enterprise-plan-before-begin}

Vous devez vérifier que vous avez installé l'environnement de développeur de {{site.data.keyword.blockchainfull_notm}}. Vous devez aussi savoir comment développer et déployer des réseaux d'entreprise. Pour plus de détails sur la création de réseaux d'entreprise, vous pouvez consulter la [documentation Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Vous devez accéder à une instance du plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform et vous devez avoir créé vos homologues au préalable. Pour plus d'informations sur le plan Enterprise d'{{site.data.keyword.blockchainfull_notm}} Platform, voir [Présentation du plan Enterprise](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

### Etape 1 : Créer un profil de connexion pour IBM Blockchain Platform
{: #deploying-a-business-network-create-connection-profile}

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

### Etape 2 : Extraire votre profil de connexion
{: #deploying-a-business-network-retrieve-connection-profile}

1. Depuis le tableau de bord de {{site.data.keyword.blockchainfull_notm}} Platform, sélectionnez **Présentation**, puis **Profil de connexion ** et le bouton **Télécharger** pour extraire votre profil de connexion.

2. Sauvegardez le profil de connexion dans la structure de répertoire créée à l'étape précédente. Nommez-le **connection.json**.

### Etape 3 : Ajouter les informations de canal
{: #deploying-a-business-network-add-channel-information}

1. Ajoutez la valeur channel du fichier `connection.json` afin qu'elle corresponde au nom du canal dans lequel vous allez créer et déployer votre réseau d'entreprise. Dans le modèle de profil de connexion fourni en exemple, l'élément channels est défini comme suit : `"channels": {},`.

### Etape 4 : Préparer vos homologues
{: #deploying-a-business-network-prepare-peers}

Cette étape **DOIT** être accomplie avant la création du canal pour le déploiement d'un réseau d'entreprise. Dans le cas contraire, un réseau d'entreprise déployé **ne démarrera peut-être pas**.
{:note}

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

### Etape 5 : Créer votre canal
{: #deploying-a-business-network-create-your-channel}

1. Sélectionnez **Canaux** dans le menu de navigation de gauche et cliquez sur le bouton **Nouveau canal**.

2. Entrez un nom de canal et une description facultative, puis cliquez sur **Suivant**. Notez que le nom de canal doit correspondre au nom que vous avez indiqué dans votre profil de connexion de l'attribut channel.

3. Accordez les droits nécessaires et cliquez sur **Suivant**.

4. Sélectionnez la règle relative au nombre d'opérateurs qui doivent accepter les mises à jour de canal et soumettre la demande.

5. Vous devez ensuite être redirigé vers la section **Notifications** où une nouvelle demande doit être passée en revue. Cliquez sur le bouton **Inspecter la demande**.

6. Cliquez sur le bouton **Accepter** afin de revenir à la section **Notifications** où vous pouvez voir que la demande peut maintenant être soumise. Cliquez sur le bouton **Soumettre une demande** afin d'ouvrir la boîte de dialogue de soumission, puis cliquez sur le bouton **Soumettre**. Le nouveau canal est créé.

7. Sélectionnez **Canaux** dans le menu de navigation. Le nouveau canal apparaît dans la liste des canaux et il doit indiquer “Aucun homologue encore ajouté”. Cliquez sur le menu des actions et sélectionnez **Rejoindre des homologues**. Cochez les cases en regard des homologues à ajouter et cliquez sur **Ajouter la sélection**.

### Etape 6 : Créer une nouvelle identité pour administrer votre réseau d'entreprise
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

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


### Etape 7 : Déployer le réseau d'entreprise
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

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

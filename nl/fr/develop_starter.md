---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Déploiement d'un réseau d'entreprise dans un plan Starter
{: #deploying-a-business-network}

Des réseaux d'entreprise peuvent être développés et déployés dans un environnement de plan Starter à l'aide de l'environnement développeur de la plateforme {{site.data.keyword.blockchainfull}} et du jeu d'outils de développeur Hyperledger Composer.

Avec l'environnement développeur, vous pouvez rapidement modéliser et tester des réseaux d'entreprise {{site.data.keyword.blockchain}} et les déployer dans une instance de la plateforme {{site.data.keyword.blockchainfull_notm}}.

## Avant de commencer

Lisez les sections [A propos du plan Starter](./starter_plan.html) et [Mise en route avec le plan Starter](./get_start_starter_plan.html). Assurez-vous également d'avoir installé l'environnement développeur de la plateforme [{{site.data.keyword.blockchainfull_notm}} : Develop](./develop_install.html) et d'avoir créé une instance du plan Starter de la plateforme {{site.data.keyword.blockchainfull_notm}} en suivant les instructions de la section [Gouvernance d'un réseau de plan Starter](./get_start_starter_plan.html). Assurez-vous de disposer de Node v8.9 ou supérieur, npm v5.x et Hyperledger Composer en version 0.19.x.


## Etape 1 : Extraire la valeur confidentielle admin

1. Depuis l'écran de présentation du plan Starter, cliquez sur **Profil de connexion** puis procédez au téléchargement. Renommez ce fichier 'connection-profile.json'.

2. Déplacez ce fichier afin qu'il soit situé sous le même répertoire que votre fichier `.bna`.

3. Dans le profil de connexion, accédez à la section 'registrar'. Dans 'registrar', sous 'enrollId' figure une propriété **enrollSecret**. Récupérez la valeur confidentielle et enregistrez une copie de celle-ci.

    ![D8KBag](https://i.makeagif.com/media/4-12-2018/D8KBag.gif)


## Etape 2 : Créer une carte d'autorité de certification

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

Remplacez `enrollSecret` dans la commande précédente par la valeur confidentielle admin extraite du profil de connexion.La commande `composer identity request` crée un répertoire `credentials` qui contient les fichiers `.pem` du certificat.

## Etape 3 : Ajouter les certificats à l'instance de plan Starter

Les certificats doivent être ajoutés à l'instance de plan Starter. Pour plus de commodité, elles peuvent être ajoutées à l'aide de l'interface utilisateur de la plateforme {{site.data.keyword.blockchainfull_notm}}. Les certificats doivent être ajoutés, puis les homologues doivent être redémarrés, et les certificats synchronisés sur le canal. Le certificat nécessaire est le fichier `admin-pub.pem` qui a été généré à partir de la commande précédente, qui se trouve dans le répertoire `credentials`.

1. Dans l'interface utilisateur du plan Starter, cliquez sur l'onglet **Membres**, **Certificats**, puis **Ajouter un certificat**. Accédez à votre répertoire `credentials`, puis copiez et collez le contenu du fichier `admin-pub.pem` dans la zone de certificat. Soumettez le certificat et redémarrez les homologues. Remarque : le redémarrage des homologues prend une minute.

    ![jlEb2y](https://i.makeagif.com/media/4-12-2018/jlEb2y.gif)

2. Ensuite, les certificats doivent être synchronisés sur le canal. Cliquez sur l'onglet **Canaux**, puis sur le bouton **Actions**, puis sur **Synchroniser le certificat** et **Soumettre**.

    ![E-sVV5](https://i.makeagif.com/media/4-12-2018/E-sVV5.gif)

## Etape 4 : Créer une carte de réseau d'entreprise admin

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

## Etape 5 : Installer et démarrer le réseau d'entreprise

Ensuite, la carte créée à l'étape précédente peut être utilisée pour installer et démarrer un réseau d'entreprise. Dans le cadre de ce guide, nous allons installer l'exemple réseau de fabrication de véhicules, utiliser cet exemple ou installer votre propre réseau d'entreprise, mais en nous assurant de modifier les noms de réseau d'entreprise spécifiés dans les commandes. La commande utilisée pour démarrer un réseau d'entreprise crée également une carte. Pour le plan Starter, cette carte doit être supprimée ; l'exemple de commande nomme cette carte `delete_me.card`, il est donc facile de le repérer.

1. Installez l'environnement installer Hyperledger Composer à l'aide de la commande suivante :

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

## Etape six : Effectuer un ping du réseau d'entreprise afin de vérifier qu'il fonctionne correctement

1. Exécutez la commande suivante pour effectuer un ping du réseau d'entreprise :

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Pour consulter les jours de code blockchain, cliquez sur **Canaux**, puis sélectionnez votre canal. Cliquez sur la flèche du menu déroulant pour afficher les journaux, ou sur le symbole Actions pour afficher plus de détails.

![fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif)

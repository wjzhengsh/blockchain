---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Déploiement d'un réseau d'entreprise dans un plan Enterprise

Les outils de développeur de la plateforme {{site.data.keyword.blockchainfull}} : Develop vous permettent de créer une**définition de réseau d'entreprise** qui peut ensuite être packagée dans une archive de réseau d'entreprise (fichier `.bna`). L'environnement de développeur vous permet de déployer des fichiers `.bna` dans une {{site.data.keyword.blockchain}} locale ou cloud à des fins de développement et de partage.

Ce tutoriel concerne l'étape suivante du cycle de vie d'un réseau d'entreprise, qui consiste à activer votre réseau d'entreprise en déployant le fichier `.bna` dans le plan Enterprise de la plateforme {{site.data.keyword.blockchainfull_notm}}.

## Avant de commencer

Vous devez vérifier que vous avez installé l'environnement de développeur de {{site.data.keyword.blockchainfull}}: Develop. Vous devez aussi savoir comment développer et déployer des réseaux d'entreprise. Pour plus de détails sur la création de réseaux d'entreprise, vous pouvez consulter la [documentation Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Vous devez accéder à une instance du plan Enterprise de la plateforme {{site.data.keyword.blockchainfull_notm}}. Pour plus d'informations sur le plan Enterprise de la plateforme {{site.data.keyword.blockchainfull_notm}}, voir [Présentation du plan Enterprise](./enterprise_plan.html).

## Etape 1 : Créer un profil de connexion pour la plateforme {{site.data.keyword.blockchainfull_notm}}

1. Créez un répertoire dans lequel stocker vos détails de connexion, par exemple :

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Chaque profil de connexion doit contenir un fichier `connection.json`. Créez un nouveau répertoire sous `.composer-connection-profiles`, dans cet exemple `bmx-hlfv1`. Il s'agira du nom du profil que vous allez utiliser lors de l'utilisation de Hyperledger Composer et de la plateforme {{site.data.keyword.blockchainfull_notm}}.

        mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1

2. Vous devez avoir maintenant la structure de répertoire suivante :

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Créez un fichier dans le répertoire tout juste créé et nommez-le `connection.json`. Vous pouvez vous inspirer du modèle ci-dessous pour votre fichier `connection.json` :

        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }

    Vous devez remplir le fichier `connection.json` que vous venez de créer à l'aide des attributs fournis via le tableau de bord de la plateforme {{site.data.keyword.blockchainfull_notm}}. Depuis le tableau de bord, sélectionnez **Présentation**, puis le bouton **Données d'identification pour le service** afin d'afficher les informations de noeud final et de certificat pour les membres du canal.

## Etape 2 : Ajouter les informations de programme de tri

1. Nous pouvons maintenant commencer à modifier le modèle à l'aide des informations fournies par les données d'identification du service. Il peut y avoir plusieurs programmes de tri dans les données d'identification du service, mais un seul est nécessaire pour un fichier `connection.json`.

    Remplacez les valeurs url du programme de tri dans le modèle à l'aide des informations appropriées de vos données d'identification du service en utilisant le format suivant :

        “url”: “grpcs://abca.4.secure.blockchain.ibm.com:12345”

## Etape 3 : Ajouter les informations relatives à l'autorité de certification

1. Remplacez la valeur ca du fichier `connection.json` par les valeurs **url** et **caName** des entrées de la section **certificateAuthorities**.

## Etape 4 : Ajouter les informations relatives à l'homologue

1. Il est obligatoire de définir les valeurs **requestURL** et **eventURL** de chaque homologue. Remplacez l'attribut **url** par la valeur **url** qui figure dans vos données d'identification du service. Remplacez l'attribut **eventURL** par la valeur **eventUrl** qui figure dans vos données d'identification du service. Une fois les modifications effectuées, la section peers du fichier `connection.json` doit se présenter comme suit :

        "peers": [
      {
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"

## Etape 5 : Ajouter les informations keyValStore

1. Définissez l'attribut **keyValStore** afin qu'il pointe sur le répertoire approprié. Créez un répertoire à utiliser pour votre valeur **keyValStore**. Par exemple, créez un répertoire sous votre répertoire de base intitulé `.composer-credentials-mychannel`. Assurez-vous que l'attribut **keyValStore** pointe sur le répertoire que vous venez de créer avec le format suivant :

        "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",

## Etape 6 : Ajouter les informations de canal

1. Remplacez la valeur channel du fichier `connection.json` afin qu'elle corresponde au nom du canal dans lequel vous allez créer et déployer votre réseau d'entreprise.

## Etape 7 : Ajouter une valeur mspID

La valeur **mspID** de votre fichier `connection.json` doit être définie sur la valeur mspID de votre organisation. Les données d'identification du service fournissent une liste des organisations avec les valeurs mspid qui leur sont associées. Vous devez utiliser la valeur de l'attribut **mspid** pour votre organisation.

## Etape 8 : Ajouter la valeur globalCert
1. La plateforme {{site.data.keyword.blockchainfull_notm}} utilise un certificat TLS commun pour les programmes de tri et les homologues. Pour chaque programme de tri et homologue, il existe un attribut **tlsCACerts** qui contient le même certificat. Remplacez la valeur factice du fichier `connection.json` par la valeur **tlsCACerts**. Le format doit ressembler à ce qui suit :

        "globalCert": "-----BEGIN CERTIFICATE-----\r\.......

## Etape 9 : Préparer vos homologues

**Remarque **: Cette étape **doit** être accomplie avant la création du canal pour le déploiement d'un réseau d'entreprise. Dans le cas contraire, un réseau d'entreprise déployé **ne démarrera pas**.

Dans le détail des données d'identification sous **certificateAuthorities** figure un attribut **registrar** qui contient les attributs des valeurs **enrollId** et **enrollSecret** au format suivant :

        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],

1. Demandez les certificats à l'aide de la commande suivante :

        composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12

    Cette commande télécharge trois fichiers dans le répertoire `.identityCredentials` sous votre répertoire de base. Les fichiers d'intérêt reposent sur la valeur **enrollId**. Par conséquent, dans l'exemple ci-dessus nous avons deux fichiers nommés **admin-pub.pem** et **admin-priv.pem**

3. Sélectionnez **Membres** dans le menu de navigation, puis choisissez l'option de menu **Certificats** et cliquez sur le bouton **Ajouter un certificat**.

4. Entrez un nom unique pour ce certificat dans la zone **Nom**. Ce nom ne peut pas comporter des tirets ou des traits d'union.

5. Ouvrez le fichier `admin-pub.pem` créé plus haut et copiez son contenu dans la zone **Clé** et cliquez sur le bouton **Soumettre**.

6. A partir de l'interface utilisateur, arrêtez puis redémarrez les homologues.

7. Le certificat doit maintenant figurer dans la liste des certificats.

## Etape 10 : Créer votre canal

1. Sélectionnez **Canaux** dans le menu de navigation de gauche et cliquez sur le bouton **Nouveau canal**.

2. Entrez un nom de canal et une description facultative, puis cliquez sur **Suivant**. Notez que le nom de canal doit correspondre au nom que vous avez indiqué dans votre profil de connexion de l'attribut channel.

3. Accordez les droits nécessaires et cliquez sur **Suivant**.

4. Sélectionnez la règle relative au nombre d'opérateurs qui doivent accepter les mises à jour de canal et soumettre la demande.

5. Vous devez ensuite être redirigé vers la section **Notifications** où une nouvelle demande doit être passée en revue. Cliquez sur le bouton **Inspecter la demande**.

6. Cliquez sur le bouton **Accepter** afin de revenir à la section **Notifications** où vous pouvez voir que la demande peut maintenant être soumise. Cliquez sur le bouton **Soumettre une demande** afin d'ouvrir la boîte de dialogue de soumission, puis cliquez sur le bouton **Soumettre**. Le nouveau canal est créé.

7. Sélectionnez **Canaux** dans le menu de navigation. Le nouveau canal apparaît dans la liste des canaux et il doit indiquer “Aucun homologue encore ajouté”. Cliquez sur le menu des actions et sélectionnez **Rejoindre des homologues**. Cochez les cases en regard des homologues à ajouter et cliquez sur **Ajouter la sélection**.

## Etape 11 : Importer une nouvelle identité pour administrer votre réseau d'entreprise

Créez une identité dans Composer en utilisant les certificats qui sont préalablement demandés. Cette nouvelle identité doit avoir le droit d'installer du code blockchain sur les homologues qui comportent votre certificat public chargé et il s'agira d'un émetteur pour les autorités de certification.

1. Pour créer une nouvelle identité, exécutez la commande suivante :

        composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem

    Où `bmx-hlfv1` est le nom du profil que vous avez préalablement créé. Tout est maintenant prêt pour le déploiement de votre fichier `.bna` sur la plateforme {{site.data.keyword.blockchainfull_notm}}.


## Etape 12 : Déployer le réseau d'entreprise

Vous pouvez maintenant déployer votre fichier `.bna` sur la plateforme {{site.data.keyword.blockchainfull_notm}}.

1. A l'aide de l'identité créée à l'étape précédente, déployez le réseau d'entreprise à l'aide de la commande suivante :

        composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString

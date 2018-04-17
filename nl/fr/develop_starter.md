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

# Déploiement d'un réseau d'entreprise dans un plan Starter
{: #deploying-a-business-networks-on-starter-plan}

Il est possible de développer et de déployer des réseaux d'entreprise dans un environnement de plan Starter à l'aide de l'environnement développeur de la plateforme {{site.data.keyword.blockchainfull}} : Develop et du jeu d'outils de développeur Hyperledger Composer.

Avec l'environnement développeur, vous pouvez rapidement modéliser et tester des réseaux d'entreprise {{site.data.keyword.blockchain}} et les déployer dans une instance de la plateforme {{site.data.keyword.blockchainfull_notm}}.

## Avant de commencer

Lisez les sections [A propos du plan Starter](./starter_plan.html) et [Mise en route avec le plan Starter](./get_start_starter_plan.html). Assurez-vous également d'avoir installé l'environnement développeur de la plateforme [{{site.data.keyword.blockchainfull_notm}} : Develop](./develop_install.html) et d'avoir créé une instance du plan Starter de la plateforme {{site.data.keyword.blockchainfull_notm}} en suivant les instructions de la section [Gouvernance d'un réseau de plan Starter](./get_start_starter_plan.html).


## Etape 1 : Extraire la valeur confidentielle admin

1. Depuis l'écran de présentation du plan Starter, cliquez sur **Profil de connexion**.

2. Dans le profil de connexion figure une propriété **admin secret**. Récupérez la valeur confidentielle et enregistrez une copie de celle-ci.

## Etape 2 : Créer une carte d'autorité de certification

La valeur confidentielle récupérée à l'étape précédente doit être utilisée pour créer une carte de réseau d'entreprise pour l'autorité de certification (AC). La carte AC sera ensuite importée et elle sera utilisée pour l'échange de **valeur confidentielle admin** pour les certificats valides de l'autorité de certification du plan Starter. 

1. A l'aide de la valeur confidentielle notée à l'étape 1, exécutez la commande suivante pour créer la carte de réseau d'entreprise AC :

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. Importez la carte à l'aide de la commande suivante :

        composer card import -f ca.card -n ca

3. A présent que la carte est importée, elle peut être utilisée pour échanger la **valeur confidentielle admin** pour les certificats valides de l'autorité de certification. Exécutez la commande suivante pour demander des certificats de l'autorité de certification :

        composer identity request --card ca --path ./credentials

La commande `composer identity request` crée un répertoire `credentials` qui contient les fichiers `.pem` du certificat.

## Etape 3 : Ajouter les certificats à l'instance de plan Starter

Les certificats doivent être ajoutés à l'instance de plan Starter. Pour plus de commodité, elles peuvent être ajoutées à l'aide de l'API de plateforme {{site.data.keyword.blockchainfull_notm}}. Les certificats doivent être ajoutés, puis les homologues doivent être arrêtés et redémarrés, et les certificats synchronisés. Les données requises dans les variables des appels d'API peuvent être extraites du **Profil de connexion** dans le Moniteur réseau du plan Starter.

1. Ajoutez le certificat à l'aide de l'appel d'API suivant :

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    Dans l'exemple ci-dessus, le corps n'est pas renseigné, le code suivant est un exemple du corps de la demande d'API :

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. Arrêtez les homologues à l'aide de l'appel d'API suivant :

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. Redémarrez les homologues à l'aide de l'appel d'API suivant :

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. Synchronisez les certificats à l'aide de l'appel d'API suivant :

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## Etape 4 : Créer une carte de réseau d'entreprise admin

A présent que les certificats corrects sont synchronisés avec les homologues, les cartes de réseau d'entreprise peuvent être créées et elles donnent le droit d'installer l'environnement d'exécution Hyperledger Composer et de démarrer le code blockchain. 

1. Créez une carte admin avec les rôles d'admin de canal et d'admin d'homologue à l'aide de la commande suivante :

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    Cette carte sera utilisée pour déployer un réseau d'entreprise dans un plan Starter.

2. Importez la carte créée à l'étape suivante à l'aide de la commande suivante :

        composer card import -f adminCard.card -n adminCard

## Etape 5 : Installer et démarrer le réseau d'entreprise

Ensuite, la carte créée à l'étape précédente peut être utilisée pour installer et démarrer un réseau d'entreprise. Dans le cadre de ce guide, vous allez installer l'exemple de réseau de fabrication de véhicules. La commande utilisée pour démarrer un réseau d'entreprise crée également une carte. Pour le plan Starter, cette carte doit être supprimée ; l'exemple de commande nomme cette carte `delete_me.card`, il est donc facile de le repérer.

1. Installez l'environnement installer Hyperledger Composer à l'aide de la commande suivante :

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. Démarrez le réseau d'entreprise à l'aide de la commande suivante :

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. Supprimez la carte de réseau d'entreprise nommée `delete_me.card`.

4. Créez une nouvelle carte de réseau d'entreprise et référencez les certificats qui ont été extraits plus haut à l'aide de la commande suivante : 

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. Importez la carte de réseau d'entreprise à l'aide de la commande suivante :

        composer card import -f ./admin@vehicle-manufacture-network.card

Le réseau d'entreprise est à présent déployé dans l'instance de plan Starter.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Exploitation d'homologues distants dans {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-operate}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Après que vous avez configuré des homologues distants {{site.data.keyword.blockchainfull}} Platform sur {{site.data.keyword.cloud_notm}} Private (ICP), vous devez effectuer plusieurs étapes supplémentaires pour que votre homologue puisse émettre des transactions afin d'interroger et d'appeler le registre du réseau de blockchain. Ces étapes incluent l'ajout de votre organisation à un canal, l'association de votre homologue distant au canal, l'installation de code blockchain sur votre homologue distant, l'instanciation de code blockchain sur le canal, ainsi que la connexion d'applications à votre homologue distant.
{:shortdesc}

Vous devez effectuer quelques étapes préalables depuis votre cluster ICP pour exploiter votre homologue distant.

**Etapes préalables :**
- [Extraction des informations de noeud final de votre homologue distant](#remote-peer-retrieve-endpoint-info)
- [Connexion à votre conteneur homologue avec kubectl](#remote-peer-kubectl-configure)
- [Redémarrage de votre homologue distant](#remote-peer-restart)

Vous pouvez ensuite utiliser l'une des méthodes suivantes pour exploiter votre homologue distant.

**Opérations :**
- [Utilisation de kits SDK Fabric](#remote-peer-operate-with-sdk)
- [Utilisation de la ligne de commande](#remote-peer-cli-operate)

Les logiciels SDK Fabric sont recommandés. Cependant, les instructions supposent que vous avez une bonne connaissance du fonctionnement du logiciel SDK.

Il est conseillé de déployer au moins deux instances du tableau Helm des homologues distants pour la [haute disponibilité](remote_peer_icp.html#high-availability). Par conséquent, vous devez suivre ces opérations une fois pour chaque homologue. Lorsque vous êtes prêt à appeler et à interroger le code blockchain depuis votre application, connectez-vous aux deux homologues pour vous assurer que vos [applications sont hautement disponibles](../v10_application.html#ha-app).

**Remarque **: L'homologue distant {{site.data.keyword.blockchainfull_notm}} Platform n'a pas accès à toutes les fonctionnalités ou à la prise en charge des homologues qui sont hébergés sur {{site.data.keyword.blockchainfull_notm}} Platform. Par conséquent, vous ne pouvez pas utiliser le moniteur réseau pour exploiter un homologue distant. Avant de commencer à lancer des homologues distants, assurez-vous d'avoir passé en revue les [considérations](remote_peer.html#remote-peer-limitations).

## Prérequis

Quel que soit votre choix, utiliser les logiciels SDK ou la ligne de commande, vous devez effectuer les étapes de cette section lors de l'exploitation de votre homologue. Vous pouvez effectuer les deux premières étapes, qui consistent à extraire les informations de noeud final de votre homologue et à configurer kubectl, avant de démarrer.

### Extraction des informations de noeud final de votre homologue distant
{: #remote-peer-retrieve-endpoint-info}

Connectez-vous à votre console ICP. Cliquez sur l'icône **Menu** dans l'angle supérieur gauche. Cliquez sur **Charge de travail** > **Déploiements**. Recherchez le nom de votre instance d'homologue distant et cliquez dessus afin d'ouvrir l'écran de présentation du déploiement. Faites défiler jusqu'à la section **Exposer les détails** et recherchez les informations `IP Ingress` et `Ports` de votre homologue distant.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

Notez l'adresse IP Ingress et les ports de noeud pour la connexion à votre homologue distant. Vous devez utiliser ces informations lors de l'installation et de l'instanciation du code blockchain sur l'homologue distant. Dans l'exemple suivant, l'adresse de votre homologue est `9.46.126.89:30162`, `9.46.126.89:30260` ou `9.46.126.89:32051`.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### Configuration de l'interface de ligne de commande kubectl
{: #remote-peer-kubectl-configure}

Vous devez utiliser l'outil de ligne de commande **kubectl** pour mener certaines opérations nécessaires au sein du conteneur homologue qui s'exécute dans ICP. Connectez-vous à votre cluster à l'aide de l'outil {{site.data.keyword.cloud_notm}} Private CLI :

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

Connectez-vous ensuite au tableau de bord de votre cluster ICP. Cliquez sur votre avatar utilisateur dans l'angle supérieur droit de l'interface utilisateur ICP, puis cliquez sur **Configurer le client**. Copiez, collez et exécutez les commandes dans la fenêtre de votre terminal sur votre machine locale. Vous pouvez vérifier que la configuration fonctionne correctement en exécutant la commande suivante :

```
kubectl -n <namespace> get pods
```
{:codeblock}

où `-n <namespace>` doit être spécifié lorsque l'espace de nom `default` n'est pas utilisé. Remplacez `<namespace>` par la valeur de votre
espace de nom dans votre cluster ICP.


La commande doit produire une réponse, qui doit ressembler à l'exemple suivant. `remotepeer-bd65c4769-95rmm` est le nom du conteneur homologue.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

Si vous ne reconnaissez pas le nom de pod de vos homologues distants dans la réponse, vous pouvez trouver ce nom dans le panneau **Pods** de votre panneau de déploiement.

Exec dans le conteneur homologue distant par l'exécution de la commande suivante, en remplaçant la valeur de `<PEER_CONTAINER_NAME>` par le nom du Pod ci-dessus.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
où `-n <namespace>` doit être spécifié lorsque l'espace de nom `default` n'est pas utilisé. Remplacez `<namespace>` par la valeur de votre
espace de nom dans votre cluster ICP.

Par exemple :

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

Vous êtes maintenant prêt à utiliser l'interface de ligne de commande du conteneur homologue. Vous devez utiliser l'interface de ligne de commande pour télécharger le certificat TLS des homologues et envoyer par téléchargement votre certificat signé à l'homologue si vous utilisez le logiciel SDK ou la ligne de commande.

## Redémarrage d'un homologue distant qui s'exécute dans ICP
{: #remote-peer-restart}

Chaque fois qu'un certificat est ajouté à l'homologue distant, le noeud homologue doit être redémarré afin qu'il puisse reconnaître le nouveau certificat.

Vous pouvez utiliser les commandes **kubectl** pour redémarrer l'homologue distant qui s'exécute dans ICP. La commande requiert le nom de **pod** de l'homologue distant et son **conteneur** en tant que paramètres de commande. Pour plus d'informations sur l'utilisation des commandes kubectl, voir [Configuration de l'interface de ligne de commande kubectl](#remote-peer-kubectl-configure).

1. Sur la console ICP, recherchez le nom de **pod** et la nom de **conteneur** de déploiement de votre homologue distant.

  1. Cliquez sur l'icône de menu dans l'angle supérieur gauche de la console, puis cliquez sur **Charges de travail > Déploiements**.
  2. Recherchez la version de votre homologue distant et cliquez dessus dans le tableau afin de l'ouvrir.
  3. Faites défiler vers le bas pour afficher le nom de **pod** associé. Notez le nom de **pod**.
  4. Cliquez sur le pod afin de l'ouvrir. 
  5. Cliquez sur l'onglet **Conteneurs**. Notez le nom de **conteneur** de votre homologue distant.

2. Depuis votre ligne de commande, exécutez la commande suivante pour redémarrer votre homologue, en remplaçant `<REMOTE_PEER_POD_NAME>` et `<REMOTE_PEER_CONTAINER_NAME>` par les valeurs ci-dessus.

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. Vous pouvez vérifier que l'homologue distant a été redémarré en exécutant la commande `kubectl get pods` et en examinant le résultat du nombre de **RESTART** pour votre pod.

Vous pouvez également utiliser la [demande HEAD](monitor_network.html#monitor-nodes) pour vérifier la disponibilité de votre homologue distant.


## Utilisation de SDK Fabric pour l'exploitation de votre homologue distant
{: #remote-peer-operate-with-sdk}

Les logiciels SDK Hyperledger Fabric fournissent un puissant jeu d'API qui permettent aux applications d'interagir et d'exploiter les réseaux de blockchain. Pour obtenir la liste la plus récente des langues prises en charge et la liste complète des API disponibles au sein des logiciels SDK Fabric dans la [documentation Hyperledger Fabric SDK community![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentation Hyperledger Fabric SDK Community"). Vous pouvez utiliser les logiciels SDK Fabric pour associer votre homologue distant à un canal sur {{site.data.keyword.blockchainfull_notm}} Platform, installer un code blockchain sur votre homologue, et instancier le code blockchain sur un canal.

Les instructions suivantes utilisent le [Logiciel SDK Fabric Node![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Fabric Node") pour exploiter l'homologue distant et supposent une connaissance préalable du logiciel SDK. Vous pouvez utiliser le [tutoriel de développement d'applications](../v10_application.html) pour en savoir plus sur l'utilisation du logiciel SDK Node avant de commencer, et comme guide pour le développement d'applications avec votre homologue distant lorsque vous êtes prêt à appeler et à interroger le code blockchain.

### Installation du logiciel SDK Node

Vous pouvez utiliser NPM pour installer le [Logiciel SDK Node ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/ "Logiciel SDK Node") :
```
npm install fabric-client@1.1
```
{:codeblock}

Il est recommandé d'utiliser la version de 1.1 du logiciel SDK Node.

### Préparation du logiciel SDK pour l'utilisation de l'homologue distant
{: #remote-peer-node-sdk}

Avant d'utiliser le logiciel SDK pour exploiter l'homologue distant, vous devez générer les certificats nécessaires (inscription) qui permettront à votre application de communiquer avec votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform et votre homologue distant. Suivez les étapes d'[inscription auprès du logiciel SDK](../v10_application.html#enroll-app-sdk) avec votre identité **admin**. Le tutoriel [Développement d'applications](../v10_application.html) inscrit également en tant qu'**admin**, il n'est donc pas nécessaire de modifier l'exemple de code.

### Envoi par téléchargement d'un certificat signataire sur {{site.data.keyword.blockchainfull_notm}} Platform
{: #remote-peer-upload-sdk}

Vous devez envoyer par téléchargement le certificat signataire de votre logiciel SDK au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform afin que votre application ait le droit d'exploiter le réseau.

- Le certificat signataire se trouve dans le répertoire où votre logiciel SDK a généré votre matériel cryptographique, dans le fichier nommé admin. Copiez le certificat figurant entre guillemets après le champ `certificate`, commençant par `-----BEGIN CERTIFICATE-----` et se terminant par `-----END CERTIFICATE-----`. Vous pouvez utiliser l'interface CLI pour convertir le certificat au format PEM à l'aide de la commande `echo -e "<CERT>" > admin.pem`. Vous pouvez ensuite coller le contenu du certificat dans votre réseau de blockchain à l'aide du moniteur de réseau. Connectez-vous au réseau sur {{site.data.keyword.blockchainfull_notm}} Platform. A l'écran "Membres" du Moniteur réseau, cliquez sur **Certificats** > **Ajouter un certificat**. Indiquez un nom pour votre certificat et collez le contenu dans le champ **Certificat**. Cliquez sur **Redémarrer** lorsque vous êtes invité à indiquer si vous voulez redémarrer vos homologues. Cette opération doit être effectuée avant que votre organisation ne rejoigne le canal.

### Envoi par téléchargement d'un certificat signataire à l'homologue distant
{: #remote-peer-upload-signcert}

Vous devez également envoyer par téléchargement le certificat signataire du logiciel SDK à l'homologue distant et le redémarrer. Vous devez installer le même certificat signataire que celui que vous avez [envoyé par téléchargement à {{site.data.keyword.blockchainfull_notm}} Platform](#remote-peer-upload-sdk) au sein du conteneur d'homologue distant.

Exécutez les commandes suivantes [dans votre conteneur homologue](#remote-peer-kubectl-configure) pour envoyer par téléchargement votre certificat.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

Remplacez le texte `<CERT.PEM>` par votre certificat signataire, commençant par `-----BEGIN CERTIFICATE-----` et se terminant par `-----END CERTIFICATE-----`. **Remarque **: un fichier cert.pem existe déjà dans ce répertoire. Nommez le nouveau certificat `cert2.pem` ou donnez-lui un nom similaire afin d'éviter qu'il n'écrase le certificat déjà présent dans le conteneur.

Etant donné que vous avez ajouté un nouveau certificat, vous devez redémarrer le conteneur pour que l'homologue prélève le certificat. Suivez ces [instructions](#remote-peer-restart) pour redémarrer l'homologue.

### Transmission du certificat TLS de votre homologue distant au logiciel SDK
{: #icp-remote-peer-download-tlscert}

Vous devez copier le contenu du fichier `cacert.pem` de TLS du conteneur homologue à votre application afin d'authentifier la communication depuis votre logiciel SDK. Dans le [conteneur d'interface CLI de l'homologue](#remote-peer-kubectl-configure), exécutez la commande suivante :
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

Copiez l'intégralité du texte du fichier `cacert.pem` dans votre presse-papiers, qui commence par le premier `-----BEGIN CERTIFICATE-----` et se termine par le tout dernier `-----END CERTIFICATE-----`. Sauvegardez ce certificat sur votre système de fichiers local au format PEM. Vous pouvez ensuite importer le certificat TLS dans votre application à l'aide d'une simple commande de lecture de fichier.
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### Indication des informations de noeud final de l'homologue distant au logiciel SDK
{: #remote-peer-SDK-endpoints}

Recherchez les [informations de noeud final de votre homologue distant](#remote-peer-retrieve-endpoint-info) et indiquez-les au logiciel SDK en déclarant une nouvelle variable d'homologue ou en mettant à jour votre profil de connexion. L'exemple suivant définit l'homologue en tant que noeud final sur votre réseau Fabric et le transmet au certificat TLS que vous avez importé.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

Vous devez spécifier un élément `ssl-target-name-override` de `<something>.blockchain.com` afin que l'homologue résolve la demande DNS.

**Remarque :** Etant donné que l'homologue est distant, les autres organisations sur le réseau {{site.data.keyword.blockchainfull_notm}} Platform ne pourront pas trouver les informations de noeud final de votre homologue dans leur profil de connexion. Si d'autres organisations doivent envoyer à votre homologue distant une transaction à approuver, vous devez fournir l'adresse IP publique et les certificats TLS dans une opération hors bande.

### Utilisation du logiciel SDK pour rejoindre un canal
{: #remote-peer-join-channel-sdk}

En tant que membre du réseau blockchain, votre organisation doit être ajoutée à un canal du réseau pour que vous puissiez rejoindre votre homologue distant dans le canal.

  - Vous pouvez démarrer un nouveau canal pour l'homologue distant. En tant qu'initiateur de canal, vous pouvez inclure automatiquement votre organisation durant la [création de canal](create_channel.html#creating-a-channel). Notez que vous devez disposer d'au moins un homologue sur {{site.data.keyword.blockchainfull_notm}} Platform pour pouvoir créer un canal dans le Moniteur réseau.  

  - Un autre membre du réseau blockchain peut également ajouter votre organisation à un canal existant en utilisant une [mise à jour de canal](create_channel.html#updating-a-channel). Un membre du canal avec des homologues sur {{site.data.keyword.blockchainfull_notm}} Platform peut utiliser le Moniteur réseau pour ajouter votre organisation au canal même si vous n'hébergez aucun homologue sur la plateforme.

    Une fois que votre organisation est ajoutée à un canal, vous devez ajouter le certificat signataire de votre homologue au canal de sorte que les autres membres puissent vérifier votre signature numérique au cours des transactions. L'homologue distant envoie par téléchargement son certificat signataire lors de l'installation, de sorte que vous devez uniquement synchroniser le certificat pour le canal. Dans l'écran "Canaux" de votre Moniteur réseau, localisez le canal rejoint par votre organisation et sélectionnez **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Action**. Cette action synchronise les certificats entre tous les homologues sur le canal.

Lorsque votre organisation fait partie du canal, suivez les instructions permettant de [rejoindre un canal](../v10_application.html#join-channel-sdk). Vous devez indiquer l'URL du service de commande et le nom du canal.

### Utilisation du logiciel SDK pour installer le code blockchain sur l'homologue
{: #remote-peer-install-cc-sdk}

Utilisez les instructions suivantes pour utiliser le logiciel SDK pour [installer un code blockchain](../v10_application.html#install-cc-sdk) sur votre homologue distant.

### Utilisation du logiciel SDK pour instancier le code blockchain sur l'homologue
{: #remote-peer-instantiate-cc-sdk}

Un seul membre de ce canal doit instancier ou mettre à jour le code blockchain. Par conséquent, tout membre réseau du canal avec des homologues sur {{site.data.keyword.blockchainfull_notm}} Platform peut utiliser le Moniteur réseau pour instancier du code blockchain et spécifier les règles de validation. Toutefois, si vous souhaitez utiliser l'homologue distant pour instancier du code blockchain sur un canal, vous pouvez utiliser le logiciel SDK et suivre les instructions d'[instanciation d'un code blockchain](../v10_application.html#instantiate-cc-sdk).


## Utilisation de l'interface CLI pour exploiter l'homologue distant
{: #remote-peer-cli-operate}

Vous pouvez également exploiter votre homologue distant depuis la ligne de commande à l'aide du client CA Fabric et du conteneur d'outils Fabric. Dans ces instructions, nous générons d'abord les certificats requis à l'aide du client CA Fabric puis nous utilisons le conteneur d'outils Fabric pour exploiter l'homologue en l'associant à un canal, en installant un code blockchain, puis en instanciant ce code blockchain sur un canal.

### Inscription à l'aide du client CA Fabric
{: #peer-client-enroll}

La première étape consiste à générer les certificats requis (inscription) à l'aide du client CA Fabric. Vous devez installer le client CA Fabric en premier. Téléchargez les fichiers [binaires fabric-ca v1.1.0 de votre plateforme](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) sur votre machine locale, extrayez-les et placez-les dans un dossier, par exemple `$HOME/fabric-ca-remote/`.

1.  Préparez votre environnement pour utiliser le client CA Fabric. Accédez au répertoire dans lequel vous avez placé les fichiers binaires du client afin de pouvoir y faire référence directement dans vos commandes.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Définissez le chemin dans lequel le client peut créer vos clés. S'il s'agit de la première fois que vous exécutez la commande `enroll`, le dossier `msp` et le fichier `.yaml` n'existent pas. Si vous avez exécuté la commande `enroll` auparavant, il est important de supprimer tout matériel de configuration des précédentes tentatives d'inscription à l'aide des commandes `rm` ci-dessous :
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Vous devez extraire les informations de noeud final de votre autorité de certification sur {{site.data.keyword.blockchainfull_notm}} Platform. A l'écran "Présentation" de votre Moniteur réseau, cliquez sur le bouton **Profil de connexion** et ouvrez le fichier JSON qui contient les données d'identification réseau. Faites défiler jusqu'à la section `certificateAuthorities` et notez les informations ci-dessous.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  Vous devez également extraire un certificat TLS d'{{site.data.keyword.blockchainfull_notm}} Platform pour communiquer avec l'autorité de certification. Les données d'identification réseau incluent le certificat nécessaire. Dans la section `certificateAuthorities` du fichier du profil de connexion, copiez le certificat qui suit `"pem:"`, qui commence par `-----BEGIN CERTIFICATE----` et se termine par `-----END CERTIFICATE----`. **N'incluez pas les apostrophes**.

    Exécutez la commande suivante dans une fenêtre de terminal, en remplaçant `<certificate>` par votre certificat copié.
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 Conservez ce certificat dans un emplacement où vous pouvez y faire référence dans les commandes futures.

5.  Vous êtes maintenant prêt à générer les certificats requis. Exécution de la commande suivante :
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    Par exemple :
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    Lorsque la commande aboutit, vous pouvez voir une réponse semblable à l'exemple suivant :
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    La commande génère les certificats requis et les stocke dans un dossier nommé `msp` dans `$FABRIC_CA_CLIENT_HOME`. Ce dossier et les certificats qui y figurent sont importants dans les étapes suivantes.

### Gestion des certificats sur votre système local
{: #manage-certs}

Pour pouvoir exploiter l'homologue distant, nous devons effectuer des opérations de gestion des certificats sur la machine locale, et envoyer par téléchargement certains certificats générés par le client CA Fabric pour {{site.data.keyword.blockchainfull_notm}} Platform et votre homologue. Nous devons également télécharger les certificats TLS à partir de la plateforme et de l'homologue. Si vous souhaitez en savoir plus sur les certificats que vous allez utiliser et les tâches que vous allez exécuter, voir [Gestion des certificats sur {{site.data.keyword.blockchainfull_notm}}](../certificates.html) Platform.

Sur votre machine locale, ouvrez un terminal de commandes et accédez au répertoire dans lequel vous avez déplacé les fichiers binaires Fabric-CA-Client et stocké le dossier MSP.
1. Copiez le fichier `cert.pem` du dossier `signcerts` dans un nouveau dossier `admincerts`.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. Vous devrez aussi copier le certificat TLS de votre homologue distant du conteneur d'homologue sur ICP vers votre machine locale.

  - [Connectez-vous à votre conteneur homologue](#remote-peer-kubectl-configure) et exécutez la commande suivante :
    ```
    cat /mnt/certs/tls/cacert.pem
    ```
    {:codeblock}

    Copiez l'intégralité du texte du fichier `cacert.pem` dans votre presse-papiers, qui commence par le premier `-----BEGIN CERTIFICATE-----` et se termine par le tout dernier `-----END CERTIFICATE-----`. 

  - Revenez sur votre machine locale et exécutez les commandes suivantes. Remplacez le texte `<CACERT.PEM>` par le texte du presse-papiers.
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
    {:codeblock}

3. Pour donner à votre interface CLI l'autorisation d'installer le code blockchain sur l'homologue, vous devez envoyer par téléchargement le certificat signataire généré par le client CA Fabric dans le dossier admin de l'homologue et redémarrer ce dernier. Par conséquent, copiez le certificat `admincert/cert.pem` de votre machine locale dans le répertoire `/mnt/crypto/peer/peer/msp/admincerts/` dans le conteneur homologue en tant que `cert2.pem`.

    - Sur votre machine locale, exécutez les commandes suivantes :

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copiez le texte du fichier `cert.pem` dans votre presse-papiers à partir de `-----BEGIN CERTIFICATE-----` et jusqu'à `-----END CERTIFICATE-----`.

    - Revenez au conteneur homologue et exécutez les commandes suivantes pour ajouter le fichier cert.pem aux certificats de l'homologue. Remplacez le texte `<CERT.PEM>` par le texte du presse-papiers.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Remarque **: un fichier cert.pem existe déjà dans ce répertoire. Ne le remplacez pas.

    - Etant donné que vous avez ajouté un nouveau certificat, vous devez redémarrer le conteneur pour que l'homologue prélève le certificat. Suivez ces [instructions](#remote-peer-restart) pour redémarrer l'homologue.

4. Vous devez envoyer par téléchargement le même certificat `admincert/cert.pem` que celui de votre machine locale à {{site.data.keyword.blockchainfull_notm}} Platform afin qu'une interface CLI ou une application distante puisse effectuer des opérations de canal, comme l'extraction du bloc d'origine du canal et l'instanciation de code blockchain.
    1. Sur votre machine locale, coupez le fichier `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` et copiez-le dans le presse-papiers.
    2. Accédez au Moniteur réseau de votre réseau sur {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Cliquez sur **Membres** dans le navigateur de gauche et cliquez sur l'onglet **Certificats**.
    4. Cliquez sur le bouton **Ajouter un certificat**.
    5. Dans la fenêtre **Ajouter un certificat**, donnez un nom à votre certificat, par exemple `fabrictools.pem`, collez le certificat que vous venez de copier dans le presse-papiers et cliquez sur **Soumettre**.
    6. Il vous sera demandé si vous souhaitez redémarrer les homologues. Cliquer sur **Redémarrer**.
    7. Cliquez sur **Canaux** dans le navigateur de gauche et recherchez le canal que l'homologue distant va rejoindre.
    8. Cliquez sur les trois points sous l'en-tête **Actions** et cliquez sur **Synchroniser le certificat**. Dans la fenêtre **Synchroniser le certificat**, cliquez sur **Soumettre**.

    **Remarque **: Il est important de synchroniser le certificat de canal avant que l'homologue ne rejoigne le canal ci-dessous.

### Configuration du conteneur d'outils Fabric

Après avoir déplacé tous nos certificats vers l'emplacement nécessaire, nous sommes prêts à installer et à utiliser le conteneur d'outils Fabric de Docker. Une fois de plus, nous devons exécuter ces commandes à partir du répertoire dans lequel vous avez stocké votre dossier MSP.

1.  Téléchargez l'image Docker des outils Fabric à l'aide de la commande suivante :
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  Exécutez les commandes suivantes pour démarrer le conteneur d'outils Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    Cette commande monte le répertoire MSP dans votre conteneur d'outils.

3.  L'adresse de l'homologue distant doit être résolue par un nom de domaine avec un suffixe `blockchain.com`. Vous pouvez effectuer cela via votre DNS ou en modifiant votre fichier /etc/hosts. Dans le cadre des présentes instructions, nous modifierons le fichier `/etc/hosts` dans le conteneur d'outils Fabric. Connectez-vous à votre console ICP et recherchez les [informations de noeud final](#remote-peer-retrieve-endpoint-info) de vos homologues distants. Dès que vous disposez de l'information `INGRESS_IP` de vos homologues, exécutez la commande suivante avec l'adresse de votre homologue distant.
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Utilisation du conteneur d'outils Fabric pour joindre l'homologue distant au canal
{: #icp-cli-join-peer-to-channel}

Avant d'exécuter les commandes d'interface CLI pour joindre l'homologue distant à un canal, il est nécessaire d'ajouter votre organisation à un canal du réseau.

  - Vous pouvez démarrer un nouveau canal pour l'homologue distant. En tant qu'initiateur de canal, vous pouvez inclure automatiquement votre organisation durant la [création de canal](create_channel.html#creating-a-channel). Notez que vous devez disposer d'au moins un homologue sur {{site.data.keyword.blockchainfull_notm}} Platform pour pouvoir créer un canal dans le Moniteur réseau.  

  - Un autre membre du réseau blockchain peut également ajouter votre organisation à un canal existant en utilisant une [mise à jour de canal](create_channel.html#updating-a-channel). Un membre du canal avec des homologues sur {{site.data.keyword.blockchainfull_notm}} Platform peut utiliser le Moniteur réseau pour ajouter votre organisation au canal même si vous n'hébergez aucun homologue sur la plateforme.

    Une fois que votre organisation est ajoutée à un canal, vous devez ajouter le certificat signataire de votre homologue au canal de sorte que les autres membres puissent vérifier votre signature numérique au cours des transactions. L'homologue distant envoie par téléchargement son certificat signataire lors de l'installation, de sorte que vous devez uniquement synchroniser le certificat pour le canal. Dans l'écran "Canaux" de votre Moniteur réseau, localisez le canal rejoint par votre organisation et sélectionnez **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Action**. Cette action synchronise les certificats entre tous les homologues sur le canal.

1. Extrayez les informations de configuration de votre fichier `creds.json` qui se trouve dans le panneau **Présentation** du Moniteur réseau. Cliquez sur **Profil de connexion**, puis sur **Télécharger**.

  - Localisez l'URL du programme de tri en recherchant **orderers**, qui se trouve sous `orderers > url`. Notez l'URL qui commence par le nom réseau. L'URL est similaire à l'exemple suivant :
```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localisez le nom de votre organisation en recherchant **organizations**. Il doit s'agir de la même organisation que celle que vous utilisez pour enregistrez votre homologue distant. Vous pouvez trouver le nom de votre organisation ainsi que le `mspid` associé. Notez la valeur de `mspid`.

2. [Recherchez les informations de noeud final des homologues distants](#remote-peer-retrieve-endpoint-info), en notant cette fois les `Ports` répertoriés en regard de `IP Ingress`.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  Notez les ports dans la plage 30K. Il s'agit de vos ports adressables du conteneur d'outils Fabric, que nous allons utiliser pour définir la variable d'environnement `PEERADDR`.

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  Par conséquent, l'élément `PEERADDR` que nous utilisons est construit avec la valeur que nous avons spécifiée dans le fichier `/etc/hosts` avec le port lié à `7051`, c'est-à-dire `30162` dans notre exemple. La valeur qui résulte de `PEERADDR`
  serait `remotepeer.blockchain.com:30162`.

3. Exécutez les commandes suivantes pour définir les variables d'environnement dans le conteneur d'outils Fabric.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Remplacez les zones par vos informations.
    - Remplacez `<ORDERER_URL>` par le nom d'hôte et le port du programme de tri du fichier `creds.json`.
    - Remplacez `<CHANNEL_NAME>` par le nom du canal que rejoint l'homologue.
    - Remplacez `<CC_NAME>` par un nom pour faire référence à votre code blockchain.
    - Remplacez `<ORGANIZATION_MSP_ID>` par le nom de l'organisation du fichier `creds.json`.
    - Remplacez `<PEER_ADDR>` par la valeur que vous avez construite à l'étape précédente.

  Par exemple :

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. Extrayez le block d'origine du canal pour générer le registre de canal sur votre homologue. Accédez (`cd`) d'abord à votre répertoire racine, puis exécutez la commande d'extraction du bloc d'origine.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
  ```
  {:codeblock}

  Exécutez la commande suivante pour vérifier que le bloc d'origine est ajouté à votre conteneur homologue avec succès :

  ```
  ls *.block
  ```
  {:codeblock}

  Vous savez que le bloc d'origine est ajouté lorsque vous voyez quelque chose de semblable à l'exemple suivant :
  ```
  defaultchannel_0.block
  ```

5. Pour joindre l'homologue au canal, exécutez la commande suivante :

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Si l'opération aboutit, vous devez voir des informations similaires à l'exemple suivant :

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilisation du conteneur d'outils Fabric pour installer le code blockchain sur l'homologue
{: #icp-toolcontainer-install-cc}
Nous sommes maintenant prêts à installer et à instancier le code blockchain sur l'homologue distant. Dans le cadre des présentes instructions, nous installerons le code blockchain `fabcar` du référentiel `fabric-samples`. Téléchargez le code blockchain `fabric-samples` depuis `github.com` à l'aide des commandes suivantes :

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

Exécutez la commande CLI homologue suivante pour installer le code blockchain `fabcar` sur l'homologue distant.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Lorsque cette commande est exécutée correctement, vous devriez voir un quelque chose de similaire à ceci :

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilisation du conteneur d'outils Fabric pour instancier le code blockchain sur un canal
{: #icp-toolcontainer-instantiate-cc}

Etant donné qu'un seul homologue doit instancier du code blockchain sur un canal, vous n'avez pas à utiliser votre homologue distant pour instancier du code blockchain. Les homologues qui sont hébergés sur {{site.data.keyword.blockchainfull_notm}} Platform peuvent faire cela. Toutefois, si vous préférez que l'homologue distant instancie du code blockchain sur le canal, vous pouvez exécuter la commande suivante dans le conteneur d'outils Fabric :

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

Lorsque cette commande est exécutée correctement, vous devriez voir un quelque chose de similaire à ceci :

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Consultation des journaux d'homologue distant dans {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-log-icp}

Vous pouvez accéder aux journaux de l'homologue distant depuis la console ICP et consulter les journaux dans l'interface Kibana.

1. Sur la console ICP, cliquez sur l'icône **Menu** dans l'angle supérieur gauche.
2. Dans la liste de menus, cliquez sur **Charges de travail** > **Editions Helm**.
3. Dans le tableau Editions Helm, cliquez sur le nom de votre édition Helm.
4. Faites défiler jusqu'à la section **Pod** et cliquez sur le lien **Afficher les journaux** en fin de ligne de tableau. Les journaux de l'homologue distant s'ouvrent dans l'interface Kibana.

## Mise à jour du code blockchain

Il est probable qu'au fil du temps vous devrez modifier le code blockchain qui s'exécute sur l'homologue distant. Etant donne que le code blockchain est installé sur les homologues et instancié sur le canal, vous devez mettre à jour le code blockchain sur tous les homologues du canal.

Procédez comme suit pour mettre à jour votre code blockchain :

1. Pour mettre à jour le code blockchain sur chaque homologue distant, relancez simplement le processus utilisé pour installer le code blockchain sur les homologues, au moyen d'une application client ou d'une commande d'interface de ligne de commande. Veillez à indiquer le même nom de code blockchain que celui utilisé initialement. Toutefois, cette fois incrémentez la `version` de code blockchain. Tous les homologues doivent utiliser le même nom et la même version de code blockchain.

2. Une fois le nouveau code blockchain installé sur tous les homologues du canal, utilisez le Moniteur réseau ou la commande
[peer chaincode upgrade ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) pour mettre à jour le canal qui va utiliser le nouveau code blockchain.

Consultez l'étape 2 des présentes [instructions](install_instantiate_chaincode.html#updating-a-chaincode) pour plus d'informations sur l'utilisation du panneau "Installer le code" du Moniteur réseau pour la mise à jour du code blockchain sur le canal.

## Traitement des incidents
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problème :** Erreur lors de la connexion au conteneur homologue
{: #icp-bash-error}

Ce problème se produit lorsque vous utilisez l'outil kubectl pour exécution sur votre homologue par l'exécution de la commande suivante :
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
échoue et produit un message d'erreur similaire à l'exemple suivant :
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Solution :**  

Cette erreur se produit sur des systèmes big endian, tels que IBM System Z, lorsque deux sessions de hachage Docker sont ouvertes dans le même conteneur Docker. La deuxième session ne peut peut-être pas se connecter au conteneur en cours d'exécution. Pour résoudre ce problème, [redémarrez le conteneur homologue](#remote-peer-restart) et relancez la commande `kubectl exec` une fois l'homologue redémarré.

---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Utilisation d'une autorité de certification sur {{site.data.keyword.cloud_notm}} Private
{: #ca-operate}

Les autorités de certification fournissent des identités sur le réseau. Une autorité de certification peut être considérée comme étant similaire à un notaire public de confiance utilisé pour établir la confiance entre plusieurs parties. Chaque entité du réseau reçoit un certificat signé par une autorité de certification racine pour encapsuler l'identité numérique de l'entité. Ce certificat est la racine de confiance pour toutes les opérations de signature et de vérification effectuées sur le réseau.
{:shortdesc}

Chaque organisation dans un réseau de blockchain multi-cloud doit déployer sa propre autorité de certification. L'autorité de certification de votre organisation va signer les demandes pour les composants utilisés par votre organisation, par exemple un service de tri, des homologues ou des applications. Par conséquent, vous devez déployer votre autorité de certification avant de déployer d'autres composants.

Vous devez effectuer quelques étapes préalablement à l'utilisation de votre autorité de certification :

- [Configurer vos interfaces de ligne de commande](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure)
- [Extraire l'URL de votre autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url)
- [Télécharger le certificat TLS de votre autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)
- [Configurer le client d'autorité de certification Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)
- [Générer des certificats avec votre admin d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin)

Une fois les étapes prérequises effectuées, vous pouvez utiliser votre autorité de certification pour enregistrer de nouvelles identités dans le réseau auprès de votre organisation et générer des certificats que vos applications peuvent utiliser.

- [Utilisation de l'autorité de certification pour déployer un service de tri ou un homologue](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)

Ce guide fournit également d'importantes informations conceptuelles sur l'utilisation et votre autorité de certification et de ses certificats pour gérer un réseau de blockchain.

- [Fournisseur de services aux membres (MSP)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)
- [Certificats TLS](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)

## Prérequis
{: #ca-operate-prerequisites}

Que vous fondiez ou rejoigniez un réseau, vous devez effectuer ces étapes prérequises pour utiliser votre autorité de certification et déployer d'autres composants et identités appartenant à votre organisation.

### Configuration des interfaces de ligne de commande
{: #ca-operate-kubectl-configure}

Vous devez utiliser l'outil de ligne de commande **kubectl** pour vous connecter au conteneur d'autorité de certification qui s'exécute dans {{site.data.keyword.cloud_notm}} Private.

1. Connectez-vous à l'interface utilisateur de votre cluster {{site.data.keyword.cloud_notm}} Private. Accédez à l'onglet **Outils de ligne de commande** et cliquez sur **Interface de ligne de commande Cloud Private**. Vous verrez les outils suivants que vous pouvez télécharger.

   * Installer l'interface CLI et les plug-ins IBM Cloud Private
   * Installer l'interface CLI Kubectl
   * Installer Helm
   * Installer l'interface CLI Istio

  Pour utiliser une autorité de certification, vous devez utiliser les **trois** premiers outils, Helm étant facultatif. Cliquez sur chacun d'eux et exécutez les commandes `curl` pour le type de machine que vous utilisez. Ensuite, exécutez les commandes `chmod` et `sudo mv` pour chaque outil. La commande `chmod` modifie les droits de l'interface de ligne de commande en question afin de la rendre exécutable, et la commande `sudo mv` déplace le fichier et le renomme.

  Les commandes du premier outil **cloudctl** peuvent ressembler à l'exemple suivant :

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  Les commandes du deuxième outil **kubectl** peuvent ressembler à l'exemple suivant :

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Après avoir configuré **kubectl**, exécutez la commande suivante :

  ```
  kubectl get pods
  ```
  {:codeblock}

  Si l'opération aboutit, la commande doit renvoyer une réponse qui est similaire à l'exemple suivant, où `ca-6cf85f6687-hcxpl` est le nom du conteneur de l'autorité de certification.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Vous êtes maintenant prêt à utiliser l'outil **kubectl** pour obtenir l'URL de l'autorité de certification.

3. Si vous le souhaitez, vous pouvez utiliser **Helm** pour effectuer quelques étapes supplémentaires. Notez que Helm est un outil dont l'installation est facultative et il n'est pas nécessaire de l'utiliser dans ces instructions. Il peut toutefois être utile pour gérer vos éditions Helm et créer vos propres archives à déployer dans {{site.data.keyword.cloud_notm}} Private.

  1. Cliquez sur "Installer Helm" et exécutez la commande `curl` depuis l'interface utilisateur {{site.data.keyword.cloud_notm}} Private.
  2. Décompressez le fichier `tar` en exécutant la commande suivante :  
    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}
  3. Exécutez la commande `sudo mv` en ajoutant `/helm` à `darwin-amd64`. Notez qu'il n'est pas nécessaire d'exécuter la commande `chmod` pour Helm. Par exemple :  
    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Vous pouvez exécuter la commande `helm help` pour confirmer que Helm est bien installé.

### Extraction de l'URL de votre autorité de certification
{: #ca-operate-url}

Vous devez cibler l'URL de l'autorité de certification pour les demandes de génération de certificats ou d'enregistrement avec une nouvelle identité. Pour trouver l'URL de votre autorité de certification, vous pouvez utiliser l'interface utilisateur de votre console {{site.data.keyword.cloud_notm}} Private. Vous devrez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Rôles et actions de l'administrateur de cluster") pour effectuer les étapes suivantes :

1. Connectez-vous à la console {{site.data.keyword.cloud_notm}} Private et cliquez sur l'icône **Menu ** dans l'angle supérieur gauche.
2. Cliquez sur **Charge de travail** > **Editions Helm**.
3. Recherchez le nom de votre édition Helm et ouvrez l'écran des détails de l'édition Helm.
4. Faites défiler jusqu'à la section **Remarques** au bas de la fenêtre. Dans la section **Remarques**, vous pouvez voir un ensemble de commandes pour vous aider à effectuer le déploiement de votre autorité de certification.
5. Si ce n'est déjà fait, suivez les étapes permettant de configurer l'[interface CLI de kubectl](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure), que vous devez utiliser pour interagir avec votre conteneur d'autorité de certification.
6. Dans votre interface de ligne de commande, exécutez la première commande de la note, qui suit **1. Get the application URL by running these commands:** Cette commande produira l'URL et le port de l'autorité de certification, comme dans l'exemple suivant. Notez cette valeur car elle sera utilisée comme URL de l'application dans les commandes ultérieures.

  ```
  http://9.30.94.174:30167
  ```

**Remarque :** Si vous déployez votre autorité de certification derrière un pare-feu, vous devez ouvrir un passe-système afin de permettre au réseau sur la plateforme d'effectuer un établissement de liaison TLS avec votre autorité de certification. Vous devez activer un passe-système uniquement pour le port qui a été concaténé avec l'URL de l'autorité de certification.


### Extraction du certificat TLS de votre autorité de certification
{: #ca-operate-tls}

Vous devez télécharger le certificat TLS de votre autorité de certification et le transmettre à vos commandes afin de pouvoir communiquer avec votre autorité de certification depuis un client distant.

1. Connectez-vous à votre console {{site.data.keyword.cloud_notm}} Private. Cliquez sur l'icône **Menu** dans l'angle supérieur gauche.
2. Cliquez sur **Charge de travail** > **Editions Helm**.
3. Recherchez le nom de votre édition Helm et ouvrez l'écran des détails de l'édition Helm.
4. Faites défiler jusqu'à la section **Remarques** au bas de la fenêtre. Dans la section **Remarques**, vous pouvez voir un ensemble de commandes pour vous aider à effectuer le déploiement de votre autorité de certification.
5. Si ce n'est déjà fait, suivez les étapes permettant de configurer l'[interface CLI de kubectl](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure), que vous devez utiliser pour interagir avec votre conteneur d'autorité de certification.
6. Dans votre interface de ligne de commande, exécutez les commandes de la troisième note, qui suit **3. Get TLS certificate:** Cette commande sauvegarde certificat TLS dans le fichier `tls.pem` dans votre répertoire local. Vous devez faire référence à ce certificat dans une future commande. Notez son emplacement.
7. La commande convertit également le certificat au format base64 et l'imprime. Le résultat ressemble à la chaîne ci-dessous :

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  Vous devez copier cette chaîne et la sauvegarder en tant que certificat **{{site.data.keyword.cloud_notm}} Private CA TLS**. Vous l'utiliserez lors du déploiement de composants supplémentaires.
  {:important}

### Configuration du client d'autorité de certification Fabric
{: #ca-operate-fabric-ca-client}

Vous pouvez recourir au client de l'autorité de certification Fabric pour utiliser votre autorité de certification. Les présentes instructions vous expliquent comment utiliser le client d'autorité de certification Fabric pour inscrire et enregistrer les identités d'autres composants appartenant à votre organisation. Vous pouvez également utiliser les logiciels SDK Fabric pour effectuer les étapes prérequises.

1. Vous devez télécharger le [client d'autorité de certification Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-client "Téléchargement du client d'autorité de certification Fabric") sur votre système de fichiers local.

  Le moyen le plus simple d'obtenir le client d'autorité de certification Fabric est de télécharger tous les binaires de l'outil Fabric directement. Accédez à un répertoire dans lequel vous souhaitez télécharger les binaires à partir de votre ligne de commande, puis procédez à leur extraction en exécutant la commande [Curl ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") suivante.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

  Cette commande installe les binaires dans un répertoire `bin/`.

2. Définissez le chemin `PATH` sur le répertoire dans lequel vous avez téléchargé les binaires de l'outil Fabric :

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  Par exemple, si vous avez installé les binaires dans votre répertoire de base, vous définiriez votre `PATH` comme suit :

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Créez le dossier dans lequel vous allez stocker les certificats de l'admin de votre autorité de certification.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. Définissez la valeur de la variable d'environnement `$FABRIC_CA_CLIENT_HOME` sur le chemin dans lequel le client d'autorité de certification va stocker les certificats [MSP](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) générés. Vérifiez que vous avez supprimé la configuration matérielle qui a peut-être été créée par des tentatives préalables. S'il s'agit de la première fois que vous exécutez la commande `enroll`, le dossier `msp` et le fichier `.yaml` n'existent pas.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Copiez le certificat TLS que vous avez [téléchargé depuis {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) dans un répertoire où vous pouvez le référencer dans de futures commandes. Vérifiez que vous êtes au niveau de votre répertoire `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### Génération de certificats avec votre admin d'autorité de certification
{: #ca-operate-enroll-ca-admin}

Avant de déployer des composants ou applications client avec votre autorité de certification, vous devez générer des certificats qui vous authentifient en tant qu'administrateur avec la capacité d'enregistrer de nouveaux utilisateurs. Le processus de génération des certificats nécessaires, de votre clé privée et de votre certificat public (également appelé certificat d'inscription ou signCert) est appelé **inscription**.

Vous pouvez générer des certificats uniquement à l'aide d'identités qui ont été enregistrés auprès de votre autorité de certification. Indiquez le nom et le secret de l'identité pour générer des certificats. Une identité **CA admin** a été automatiquement enregistrée pour vous lors du déploiement de votre autorité de certification. Vous pouvez maintenant utiliser ce nom d'administrateur et ce mot de passe admin pour émettre une commande `enroll` auprès du client d'autorité de certification Fabric afin de générer un dossier MSP avec des certificats qui sont ensuite utilisés pour enregistrer d'autres identités d'homologue ou de service de tri.

1. Vérifiez que vous avez effectué les étapes relatives à la [configuration client d'autorité de certification Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) et que `$FABRIC_CA_CLIENT_HOME` est défini sur le répertoire où vous souhaitez stocker vos certificats d'admin de l'autorité de certification.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. Exécutez la commande suivante pour générer des certificats :

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  `<enroll_id>` et `<enroll_password>` dans la commande sont le [nom d'utilisateur et le mot de passe admin d'autorité de certification](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret) que vous avez transmis au secret Kubernetes lors du déploiement de l'autorité de certification. Insérez l'[URL d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) dans le `<ca_url_with_port>`. Retirez `http://` au début. Le `<ca_name>` est la valeur que vous avez fournie dans la zone `Nom de l'autorité de certification` lors du déploiement de l'autorité de certification.

  Le `<ca_tls_cert_path>` est le chemin d'accès complet au [certificat TLS de votre autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls).

  Un appel réel doit ressembler à ce qui suit dans l'exemple de commande :

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  **Astuce :** Si la valeur de l'URL d'enregistrement, valeur de paramètre `-u`, contient un caractère spécial, vous devez l'encoder ou indiquer l'URL d'enregistrement entre guillemets simples. Par exemple, `!` devient `%21`, ou la commande ressemble à ceci :

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
  ```
  {:codeblock}

  La commande `enroll` génère un ensemble complet de certificats, appelé dossier MSP, qui se trouve dans le répertoire où vous avez défini votre client d'autorité de certification Fabric sur le chemin`$HOME`. Par exemple, `$HOME/fabric-ca-client/ca-admin`. Pour plus d'informations sur les dossiers MSP et leur contenu, voir [Fournisseur de services aux membres (MSP)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp).

  Si la commande `enroll` échoue, voir la section relative au [Traitement des incidents](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting) pour connaître les causes possibles.

Vous pouvez exécuter une commande tree pour vérifier que vous avez effectué toutes les étapes prérequises. Accédez au répertoire dans lequel vous avez stocké vos certificats. Une commande tree doit générer un résultat similaire à la structure suivante :

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── catls
    └── tls.pem    
```

## Utilisation de l'autorité de certification pour déployer un service de tri ou un homologue
{: #ca-operate-deploy-orderer-peer}

Avant de déployer un service de tri ou un homologue, vous devez créer un fichier JSON de configuration contenant d'importantes informations relatives à l'identité du composant et à votre autorité de certification. Vous devez ensuite transmettre ce fichier à la charte Helm pendant la configuration à l'aide d'un objet [Valeur confidentielle de Kubernetes![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/configuration/secret/). Ce fichier permet à votre service de tri ou homologue d'obtenir les certificats dont il a besoin auprès de l'autorité de certification pour rejoindre un réseau de blockchain. Ce fichier contient également un certificat admin qui vous permet d'utiliser votre composant en tant qu'utilisateur admin.

les instructions suivantes vous fournissent un [modèle de fichier de configuration JSON](/docs/services/blockchain/howto/CA_operate.html#ca-operate-config-file-template) que vous pouvez éditer et sauvegarder sur votre système de fichiers local, et elles vous guident tout au long de l'utilisation de votre autorité de certification pour compléter ce fichier.

- Suivez les instructions ci-dessous si vous déployez un service de tri sur {{site.data.keyword.cloud_notm}} Private ou si vous déployez un homologue pour la connexion à un consortium hébergé sur {{site.data.keyword.cloud_notm}} Private.
- Si vous voulez déployer un homologue pour la connexion à un Starter Plan ou un Enterprise Plan, suivez plutôt les instructions relatives au [Déploiement d'homologues dans IBM Cloud Private pour la connexion à un Starter Plan ou un Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy). Ces étapes vous guident tout au long de l'utilisation de votre autorité de certification sur {{site.data.keyword.blockchainfull_notm}} Platform pour déployer notre homologue sur {{site.data.keyword.cloud_notm}} Private.

### Fichier de configuration
{: #ca-operate-config-file-template}

Voici un modèle de fichier de configuration :
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

Copiez l'intégralité de ce fichier dans un éditeur de texte dans lequel vous pouvez l'éditer et le sauvegarder sur votre système de fichiers local en tant que fichier JSON.

### Informations de connexion de l'autorité de certification

Tout d'abord, nous devons fournir les informations de connexion de votre autorité de certification sur {{site.data.keyword.cloud_notm}} Private. Utilisez les informations suivantes pour renseigner les valeurs de la section `"component"` du fichier :

- Les valeurs `"cahost"` et `"caport"` représentent l'URL et le port de l'[URL d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url). Par exemple, si l'URL de l'autorité de certification est http://9.30.94.174:30167, la valeur de `"cahost"` serait `9.30.94.174` et la valeur de `"caport"` serait `30167`.
- Le `"caname"` est le nom de l'autorité de certification qui a été spécifiée lors du déploiement de la charte Helm. Vous avez fait référence au nom de l'autorité de certification dans votre commande lors de l'inscription à l'aide de l'[identité admin de l'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin).
- La valeur `"cacert"` est le certificat TLS encodé en base64 de votre autorité de certification. [Procédez à l'extraction du certificat TLS de votre autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) depuis votre console {{site.data.keyword.cloud_notm}} Private à l'aide de la note de votre édition Helm, et insérez la sortie base64 des commandes de la note dans la section ci-après.
  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  Après la mise à jour, la zone `"cacert"` ressemble à l'exemple suivant :

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

- Les zones de la section `"tls"` ci-dessous requièrent les mêmes informations que celles transmises aux sections component ci-dessus, excepté que vous devez utiliser la valeur du nom d'instance TLS de l'autorité de certification qui est indiqué lors du déploiement de l'autorité de certification. Utilisez le même certificat TLS pour la valeur `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

Après extraction des informations à l'autorité de certification, vous devez utiliser le client d'autorité de certification Fabric pour effectuer plusieurs étapes opérationnelles :

1. [Enregistrer le nouveau composant auprès de votre autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-component).

2. Vous devez également [enregistrer un administrateur de composant](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin) puis [générer des certificats pour l'admin de composant](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin) dans un dossier MSP. Cette étape n'est pas nécessaire si vous avez déjà enregistré un administrateur pour déployer un autre composant.

3. [Enregistrer le nouveau composant auprès de votre autorité de certification TLS](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls-register-component).

### Enregistrement de l'identité du composant auprès de l'autorité de certification
{: #ca-operate-register-component}

Si vous souhaitez former un consortium en déployant un service de tri et en lui ajoutant des organisations, ou déployer des homologues et les joindre à des canaux, vous devez d'abord enregistrer l'identité du composant auprès de votre autorité de certification. Le déploiement de votre composant peut ensuite générer les certificats qui sont nécessaires pour que l'homologue ou le service de tri participe à un réseau.

1. [Générez des certificats avec votre admin d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin) à l'aide du client d'autorité de certification Fabric. Utilisez ces certificats admin pour exécuter les commandes suivantes. Assurez-vous que `$FABRIC_CA_CLIENT_HOME` est défini sur `$HOME/fabric-ca-client/ca-admin`.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. Exécutez la commande suivante pour rechercher votre affiliation et le nom de votre organisation.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  La commande peut ressembler à l'exemple suivant :
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Vous devez voir des informations similaires à l'exemple suivant :
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  Notez la seconde valeur **affiliation**, par exemple, `org1.department1`. Vous devez utiliser cette valeur dans la commande ci-dessous.

3. Exécutez la commande suivante pour enregistrer le service de tri ou l'homologue.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Créez un nom et un mot de passe pour le composant, puis utilisez-les pour remplacer `name` et `secret`. **Important :** Notez ces informations. Définissez `--id.type` sur `orderer` si vous déployez un service de tri, ou définissez-le sur `peer` si vous déployez un homologue. La commande peut ressembler à l'exemple suivant :

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Après exécution de cette commande, vous devez entrer le `nom` et le `secret`, `"enrollid"` et `"enrollsecret"`, dans le fichier de configuration, sous la section `"component"` :

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```

  Vous ne pouvez enregistrer une identité qu'une seule fois. Si vous rencontrez un problème, essayez d'exécuter une commande avec un nouveau nom d'utilisateur et un nouveau mot de passe. Par mesure de sécurité, utilisez chaque identité, ainsi que l'ID d'inscription et la valeur confidentielle qui l'accompagnent, pour déployer un seul homologue. Même si vous pouvez utiliser une identité **admin** pour plusieurs composants (il s'agit de la stratégie de déploiement recommandée), ne réutilisez pas les ID et les mots de passe d'homologue.

  Lorsque la commande aboutit, vous pouvez voir des informations semblables à l'exemple suivant :

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### Enregistrement de l'admin
{: #ca-operate-register-admin}

Après enregistrement du composant, vous devez aussi créer une identité admin que vous utiliserez pour exploiter le composant. Tout d'abord, vous devez enregistrer cette nouvelle identité auprès de votre autorité de certification, puis l'utiliser pour générer un dossier MSP. Ensuite, vous devez ajouter le certificat signCert des administrateurs au fichier de configuration, où il deviendra un certificat admin du service de tri ou de l'homologue lors du déploiement. Cela vous permet d'utiliser les certificats de l'identité admin pour faire fonctionner votre réseau blockchain, comme le démarrage d'un nouveau canal ou l'installation d'un code blockchain sur vos homologues.

Il vous suffit de créer une seule identité admin pour les composants appartenant à votre organisation. Si vous déployez plusieurs homologues ou un service de tri, vous ne devez effectuer ces étapes qu'une seule fois. Vous pouvez utiliser le certificat signCert généré pour un composant pour déployer des homologues ou un service de tri.

Vérifiez que `$FABRIC_CA_CLIENT_HOME` est défini sur le chemin du dossier MSP de l'admin de votre autorité de certification.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Enregistrez l'identité admin du composant auprès de l'autorité de certification en exécutant la commande suivante :

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Créez un `nom` et `secret` pour la nouvelle identité utilisateur de l'admin. Cette identité ne deviendra admin pour tous les composants que vous déployez. Assurez-vous d'utiliser des valeurs différentes de celles de l'homologue ou du service de tri que vous venez d'enregistrer. La commande ressemble à l'exemple suivant .

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**Important :** Notez ces informations. Vous utiliserez ce `nom` et ce `secret` pour générer le dossier MSP à l'aide de la commande `enroll`.

#### Génération du dossier MSP admin
{: #ca-operate-enroll-admin}

Après enregistrement de l'admin du composant, vous pouvez générer les certificats à l'aide de la commande suivante :

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Par exemple :

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Une fois cette commande exécutée, elle génère un nouveau dossier MSP dans le répertoire que vous avez spécifié à l'aide de l'indicateur `-M`. Ce répertoire contient les certificats que vous allez utiliser pour exploiter vos composants. Vous pouvez vérifier que la commande enroll a abouti à l'aide d'une commande tree. Accédez au répertoire dans lequel vous avez stocké vos certificats. Une commande tree doit générer un résultat similaire à la structure suivante :


```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```


Dans ce répertoire MSP, ouvrez le fichier SignCert du nouvel utilisateur et convertissez-le au format base64 à l'aide des commandes suivantes :

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**Remarque :** Il est important que la chaîne générée par l'utilisation de la commande ci-dessus soit mise en forme sur une seule ligne. Elle doit ressembler à ceci :

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
 ```
 et non à ceci :

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
 ```

Par exemple :

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

Cette commande produit une chaîne qui est semblable à l'exemple suivant :

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

Copiez cette chaîne dans la zone `"admincerts"` sous la section component dans le fichier de configuration.

#### Enregistrement du composant auprès de l'autorité de certification TLS
{: #ca-operate-tls-register-component}

Vous devez également enregistrer le service de tri ou l'homologue auprès de l'autorité de certification TLS. Pour ce faire, vous devez d'abord vous inscrire à l'aide de l'admin de l'autorité de certification TLS. Placez `$FABRIC_CA_CLIENT_HOME` dans un répertoire où vous voulez stocker vos certificats admin d'autorité de certification TLS.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Exécutez la commande ci-dessous pour vous inscrire en tant qu'admin auprès de l'autorité de certification TLS. La commande est la même que celle utilisée pour vous inscrire en tant que [admin de l'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin), à la seule différence que vous utiliserez le nom d'instance TLS de l'autorité de certification que vous avez spécifié lors de la [configuration de votre autorité de certification.](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms) Assurez-vous d'utiliser le même `ca-admin-name` et `ca-admin-password` que dans le secret de votre autorité de certification.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Un appel réel doit ressembler à l'exemple suivant :

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Une fois l'inscription effectuée, vous disposez des certificats nécessaires pour enregistrer votre composant auprès de l'autorité de certification TLS. Exécutez la commande suivante pour enregistrer le service de tri ou l'homologue :

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Cette commande est similaire à celle que vous avez utilisée pour enregistrer l'identité du composant auprès de l'autorité de certification, exception faite de l'utilisation du nom de l'autorité de certification TLS. Si vous déployez un service de tri au lieu d'un homologue, définissez `--id.type` sur `orderer` au lieu de `peer`. Vous devez attribuer à cette identité un nom d'utilisateur et un mot de passe différents de ceux que vous avez utilisés pour votre autorité de certification par défaut. Un enregistrement réel peut ressembler à la commande suivante :

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Copiez les valeurs de `nom` et `secret` pour `"enrollid"` et `"enrollsecret"` sous la section `"tls"` du fichier de configuration :

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### Hôtes CSR (Demande de signature de certificat)
{: #ca-operate-csr-hosts}

Vous devez fournir un nom d'hôte CSR pour déployer un service de tri ou un homologue. Ce nom d'hôte inclue l'adresse IP proxy du cluster où vous allez déployer le composant et le `nom d'hôte de service` qui est généré lors du déploiement de la charte Helm.

#### Recherche de la valeur de l'adresse IP proxy du cluster

Si vous voulez déployer un service de tri ou un homologue sur le même cluster {{site.data.keyword.cloud_notm}} Private que celui sur lequel vous avez déployé un rôle [Administrateur de cluster ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Rôles et actions de l'administrateur de cluster") sur le cluster {{site.data.keyword.cloud_notm}} Private où le service de tri ou l'homologue va être déployé. Ensuite, entrez la même IP proxy que celle utilisée lorsque vous avez [configuré votre autorité de certification](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms). Si vous voulez déployer le service de tri ou l'homologue sur un autre cluster, vous pouvez extraire la valeur de l'adresse IP proxy du cluster à partir de la console {{site.data.keyword.cloud_notm}} Private.

1. Connectez-vous à la console {{site.data.keyword.cloud_notm}} Private. Dans le panneau de navigation gauche, cliquez sur **Plateforme** puis sur **Noeuds** pour afficher les noeuds qui sont définis dans le cluster.
2. Cliquez sur le noeud avec le rôle `proxy`, puis copiez la valeur de l'`IP hôte` de la table.
3. Insérez l'`IP hôte` comme valeur de `"hosts"` dans la section `"csr"` du fichier de configuration ci-dessous :

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### Détermination du nom de l'hôte de service

Un `nom d'hôte de service` est généré lors du déploiement d'une charte Helm. Il est basé sur le `nom de l'édition Helm` que vous utilisez lors du déploiement de la charte Helm.

- Si vous déployez un service de tri, le `nom d'hôte de service` est la valeur `helm_release_name` que vous spécifiez lors du déploiement avec la chaîne `-orderer` ajoutée à la fin. Par exemple, si l'adresse de proxy IP de votre cluster est `9.42.134.44` et que vous indiquez un `nom d'édition Helm` `org1orderer`, vous pouvez insérer les valeurs suivantes dans la section `"csr"` du fichier :

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- Si vous déployez un homologue, le `nom d'hôte de service` est le `nom d'édition Helm` que vous spécifiez lors du déploiement sans aucune chaîne supplémentaire. Par exemple, si l'adresse de proxy IP du cluster est 9.42.134.44" et que le `nom d'édition Helm` est `org1peer1`, vous pouvez insérer les valeurs suivantes dans les "hosts" csr :

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
      "org1peer1"
    ]
  }
  ```
  {:codeblock}

### Finalisation du fichier de configuration

Une fois toutes les étapes ci-dessus effectuées, votre fichier de configuration mis à jour peut être similaire à l'exemple suivant :


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer1",
            "enrollsecret": "peer1pw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}



Vous pouvez laisser les autres zones vides. Sauvegardez ce fichier et vous devrez l'utiliser lorsque du déploiement d'un [service de tri](/docs/services/blockchain/howto/orderer_deploy_icp.html) ou d'un [homologue](/docs/services/blockchain/howto/peer_deploy_ibp.html).

## Fournisseur de services aux membres (MSP)
{: #ca-operate-msp}

Les composants d'{{site.data.keyword.blockchainfull_notm}} Platform consomment des identités via des Fournisseur de services aux membres (MSP). Les MSP associent les certificats émis par les autorités de certification à des droits et des rôles. Pour plus d'informations sur les MSP, consultez [la rubrique relative à l'appartenance dans la documentation Hyperledger Fabric![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "la rubrique relative à l'appartenance dans la documentation Hyperledger Fabric").

Les dossiers MSP doivent avoir une structure définie pour pouvoir être utilisés par les composants Fabric. Le client d'autorité de certification Fabric établit cette structure en créant les dossiers MSP suivants :

- **cacerts :** Contient le certificat de l'autorité de certification racine de votre réseau.
- **intermediatecerts :** Il s'agit des certificats des autorités de certification intermédiaires dans votre chaîne de confiance (qui renvoie à une ou plusieurs autorités de certification racines). Chaque organisation du plan Enterprise dispose de deux autorités de certification intermédiaires pour la reprise et la haute disponibilité.
- **signCerts :** Ce dossier contient votre certificat signataire public, également appelé signCert ou certificat d'enregistrement. Ce certificat est joint à vos appels vers le réseau (un appel de code blockchain, par exemple) lorsque vous faites référence à votre répertoire MSP depuis la ligne de commande ou générez un objet de contexte utilisateur à l'aide de logiciels SDK. Vous pouvez télécharger ce certificat sur {{site.data.keyword.blockchainfull_notm}} Platform si vous souhaitez exploiter un réseau à partir d'un logiciel SDK ou d'une ligne de commande.
- **keystore :** Ce dossier contient votre clé privée. Cette clé est utilisée pour signer les appels vers le réseau lorsque vous faite référence à votre répertoire MSP depuis la ligne de commande ou générez un objet de contexte utilisateur à l'aide de logiciels SDK. Il est **essentiel** que cette clé soit conservée en lieu sûr. Si elle est compromise, elle peut être utilisée pour simuler votre identité.

Vous pouvez aussi créer un dossier MSP qui peut être référencé par fabric-ca-client à l'aide du Moniteur réseau et des API Swagger.

- **cacerts** et **intermediatecerts** : Vous pouvez extraire ces certificats avec les [API Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) en émettant une demande `Get` à l'API MSP.
- **signCerts** et **keystore** : Vous pouvez générer ces certificats en cliquant sur le bouton **Générer des certificats** dans le panneau "Autorité de certification". Une fenêtre en incrustation s'ouvre avec deux certificats répertoriés. Copiez et stockez le **certificat** dans signCerts et la **clé privée** dans le fichier de clés. Conservez ces certificats en lieu sûr car ils ne sont pas stockés sur la plate-forme.

De nombreux composants Fabric contiennent des informations supplémentaires dans leur dossier MSP. Par exemple, si vous exploitez un homologue distant, vous pouvez voir les dossiers suivants :

- **admincerts :** Ce dossier contient la liste des administrateurs pour cette organisation ou ce composant. Vous devrez envoyer par téléchargement votre certificat signCert dans ce dossier si vous exploitez un homologue distant à partir de la ligne de commande ou de logiciels SDK. Si vous utilisez le client d'autorité de certification Fabric, vous avez également besoin d'un dossier admincerts dans votre MSP qui contienne le certificat signCert correspondant qui doit être reconnu en tant que certificat administrateur.
- **tls :** Il s'agit d'un dossier dans lequel vous stockez les certificats TLS utilisés pour communiquer avec d'autres composants réseau.

Pour plus d'informations sur la structure des MSP, voir [Appartenance ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Appartenance") et [Fournisseur de services aux membres (MSP)![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Fournisseur de services aux membres (MSP)") dans la documentation Hyperledger Fabric.


## Affichage des journaux de l'autorité de certification
{: #ca-operate-view-logs}

Les journaux de composant peuvent être affichés à partir de la ligne de commande à l'aide de [`commandes de l'interface CLI kubectl`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Votre fenêtre dans Elastic Search"), qui est inclus dans votre cluster {{site.data.keyword.cloud_notm}} Private.

- Utilisez la commande `kubectl logs` pour afficher les journaux de conteneur dans le pod. Si vous avez des doutes quant à votre nom de pod, exécutez la commande suivante pour afficher la liste des pods.

   ```
   kubectl get pods
   ```
   {:codeblock}

   Ensuite, exécutez la commande suivante pour extraire les journaux pour le conteneur d'autorité de certification qui réside dans le pod en remplaçant `pod_name` par le nom de votre pod à partir de la sortie de commande ci-dessus :

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   Pour plus d'informations sur la commande `kubectl logs`, consultez la [documentation Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Vous pouvez aussi accéder aux événements de déploiement et aux journaux en utilisant la [console de gestion de cluster {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), qui ouvre les journaux dans Kibana.

  **Remarque :** Lorsque vous affichez vos journaux dans Kibana, vous pouvez recevoir la réponse `No results found`. Cette condition peut se produire si {{site.data.keyword.cloud_notm}} Private utilise l'adresse IP de votre noeud worker comme nom d'hôte. Pour résoudre ce problème, supprimez le filtre qui commence par `node.hostname.keyword` en haut de l'écran et les journaux deviennent visibles.

## Sécurité
{: #ca-operate-security}

L'autorité de certification peut être maintenue hors ligne si seul un nombre limité de certificats sont émis, par exemple des homologues, un serveur Node.js, des applications client seulement, pour garantir une sécurité renforcée et éviter toute compromission des éléments d'une clé d'autorité de certification. Toutefois, l'autorité de certification doit être en ligne lorsque l'émission à la demande de certificats aux utilisateurs est obligatoire. Il est fortement recommandé de sécuriser la clé privée admin de votre autorité de certification avec [HSM](https://console.test.cloud.ibm.com/docs/services/blockchain/glossary.html#glossary-hsm) si possible.

## Traitement des incidents
{: #ca-operate-troubleshooting}

### **Problème :** Erreur lors de l'exécution de la commande `enroll`
{: #ca-operate-enroll-error1}

Lors de l'exécution de la commande enroll du client d'autorité de certification Fabric, il est possible que la commande échoue avec l'erreur suivante :

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**Solution :**

Cette erreur peut se produire lorsque votre client d'autorité de certification Fabric essaie de s'inscrire mais ne peut pas se connecter à votre autorité de certification. Cela peut se produire si :   

- Votre commande `enroll` contient un élément `https://` supplémentaire sur le paramètre `-u`.
- Le nom de votre autorité de certification est incorrect.
- Votre nom d'utilisateur ou votre mot de passe est incorrect.

Passez en revue les paramètres spécifiés dans votre commande `enroll` et assurez-vous qu'aucune de ces conditions n'existe.

### **Problème :** Erreur avec l'autorité de certification lors de l'exécution de la commande `enroll`
{: #ca-operate-enroll-error2}

La commande d'enregistrement du client d'autorité de certification Fabric peut échouer si l'URL d'enregistrement, valeur de paramètre `-u`, contient un caractère spécial. Par exemple, la commande suivante avec l'ID d'enregistrement et le mot de passe `admin:C25A06287!0`,

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

échoue et produit l'erreur suivante :

```
!pw@9.12.19.115: event not found
```

### **Solution :**
Vous devez encoder le caractère spécial ou indiquer l'URL d'enregistrement entre guillemets simples. Par exemple, `!` devient `%21`, ou la commande ressemble à ceci :

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Déploiement d'homologues dans {{site.data.keyword.cloud_notm}} Private et connexion à Starter Plan ou Enterprise Plan
{: #peer-ibp}


***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Les instructions ci-après expliquent comment déployer un homologue {{site.data.keyword.blockchainfull}} Platform sur {{site.data.keyword.cloud_notm}} Private (ICP) qui établira une connexion à un réseau Starter Plan ou Enterprise Plan sur {{site.data.keyword.cloud_notm}} ou votre ICP local.
{:shortdesc}

Avant de déployer un homologue, passez en revue la section [Considérations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

Votre réseau Starter Plan ou Enterprise Plan doit exécuter Hyperledger Fabric version 1.1 ou version 1.2.1. Pour connaître la votre version d'Hyperledger Fabric, ouvriez la [fenêtre Préférences réseau](/docs/services/blockchain/v10_dashboard.html#network-preferences) dans votre Moniteur réseau.

## Ressources obligatoires
{: #peer-resources-required}

Vérifiez que votre système ICP respecte les exigences de ressources matérielle minimum :

| Composant        | vCPU | RAM  | Disque pour le stockage de données   |
|-----------|------|-----|-----------------------|
| Homologue        | 2    | 2 Go | 50 Go avec possibilité d'extension   |
| CouchDB for Peer | 2    | 2 Go | 50 Go avec possibilité d'extension   |

 **Remarques :**
 - Une unité vCPU est un coeur virtuel qui est affecté à une machine virtuelle ou à un coeur de processeur physique si le serveur n'est pas partitionné pour les machines virtuelles. Vous devez tenir compte des exigences vCPU lorsque vous décidez d'utiliser le coeur de processeur virtuel (VPC) pour votre déploiement dans ICP. VPC est une unité de mesure pour déterminer les coûts de licences des produits IBM. Pour plus d'informations sur les scénarios VPC, voir[Virtual processor core (VPC) ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Ces niveaux de ressources minimum sont suffisants pour les tests et l'expérimentation. Pour un environnement avec un gros volume de transactions, il est important d'allouer une quantité de mémoire suffisamment important ; 250 Go pour votre homologue par  exemple. La quantité de mémoire à utiliser dépend du nombre de transactions et du nombre de signatures qui sont nécessaires à partir de votre réseau. Si vous êtes sur le point d'épuiser la mémoire de votre homologue, vous devez déployer un nouvel homologue avec un système de fichiers plus important et le laisser se synchroniser par l'intermédiaire de vos autres composants sur les mêmes canaux.

## Stockage
{: #storage}

Vous devez déterminer l'espace de stockage que votre homologue va utiliser. Si vous utilisez les paramètres par défaut, la charte Helm crée un nouveau volume persistant de 8 Gi nommé `my-data-pvc` pour les données de votre homologue, et un autre volume persistant de 8 Gi nommé `statedb-pvc` pour votre base de données d'état.

Si vous ne voulez pas utiliser les paramètres de stockage par défaut, vérifiez qu'une *nouvelle* `storageClass` est définie lors de l'installation ICP ou l'administrateur système Kubernetes doit créer une storageClass avant le déploiement.

Vous pouvez choisir de déployer les homologues sur les plateformes amd64 ou s390x. Toutefois, vous devez savoir que la [mise à disposition dynamique](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) est uniquement disponible pour les noeuds amd64 dans ICP. Si votre cluster combine à la fois des noeuds worker s390x et amd64, la mise à disposition dynamique ne peut pas être utilisée.

Si vous n'utilisez pas la mise à disposition dynamique, des[Volumes permanents ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) doivent être créés et configurés avec des libellés qui peuvent être utilisés pour affiner le processus de définition des accès Kubernetes PVC.

## Prérequis pour le déploiement d'un homologue
{: #prerequisites-peer-ibp}

1. Avant d'installer un homologue sur ICP, vous devez [installer ICP](/docs/services/blockchain/ICP_setup.html) et [installer la Charte Helm de {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html).

2. Si vous utilisez Community Edition et souhaitez exécuter cette charte Helm sur un cluster ICP sans connectivité Internet, vous devez créer des archives sur une machine connectée à Internet avant d'installer les archives sur votre cluster ICP. Pour plus d'informations, voir [Adding featured applications to clusters without Internet connectivity ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adding featured applications to clusters without Internet connectivity"){:new_window}. Remarque : Vous pouvez trouver le fichier de spécification manifest.yaml sous  ibm-blockchain-platform-dev/ibm_cloud_pak dans la charte Helm.

3. Vous devez disposer d'une organisation qui est membre d'un réseau de plan Starter ou Enterprise sur {{site.data.keyword.cloud_notm}}. L'homologue optimise les noeuds finaux d'API, les autorités de certification Hyperledger Fabric et le service de tri du réseau {{site.data.keyword.blockchainfull_notm}} Platform à exploiter. Si vous n'êtes membre d'aucun un réseau de blockchain, vous devez créer ou rejoindre un réseau. Pour plus d'informations, voir [Création d'un réseau de plan](/docs/services/blockchain/get_start.html#creating-a-network) ou [Rejoindre un réseau](/docs/services/blockchain/get_start.html#joining-a-network).

4. Vous devez d'abord [déployer une autorité de certification](/docs/services/blockchain/howto/CA_deploy_icp.html) sur ICP. Vous utiliserez cette autorité de certification en tant qu'autorité de certification TLS. Suivez les étapes prérequises pour l'[l'utilisation d'une autorité de certification sur ICP](/docs/services/blockchain/howto/CA_operate.html#prerequisites) avant de déployer votre homologue. Vous n'aurez pas besoin d'effectuer des étapes supplémentaires.

5. Procédez à l'extraction de la valeur de l'adresse IP proxy du cluster de votre autorité de certification TLS depuis la console ICP. **Remarque :** Vous devez être [administrateur de cluster![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Cluster administrator roles and actions") pour accéder à votre IP de proxy. Connectez-vous au cluster ICP. Dans le panneau de navigation gauche, cliquez sur **Plateforme** puis sur **Noeuds** pour afficher les noeuds qui sont définis dans le cluster. Cliquez sur le noeud avec le rôle `proxy`, puis copiez la valeur de l'`IP hôte` de la table. **Important :** Conservez cette valeur car vous allez l'utiliser lors de la configuration de la zone `Adresse IP du proxy` de la charte Helm.

6. Créez un fichier de configuration d'homologue et stockez-le en tant que valeur confidentielle Kubernetes dans ICP. Vous trouverez les étapes de création de ce fichier dans la [section suivante](#peer-config-file).

## Génération du fichier de configuration
{: #peer-config-file}

Avant de déployer un homologue, vous devez créer un fichier JSON de configuration contenant d'importantes informations relatives à l'identité de l'homologue et à votre autorité de certification sur {{site.data.keyword.cloud_notm}}. Vous devez ensuite transmettre ce fichier à la charte Helm  pendant la configuration à l'aide d'un objet [Valeur confidentielle de Kubernetes![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/configuration/secret/). Ce fichier permet à votre homologue d'obtenir les certificats dont il a besoin auprès de l'autorité de certification sur {{site.data.keyword.cloud_notm}} pour rejoindre un réseau Starter Plan ou Enterprise Plan. Ce fichier contient également un certificat admin qui vous permet d'utiliser votre homologue en tant qu'utilisateur admin.

Nous fournissons dans ces instructions un exemple de fichier JSON que vous pouvez éditer et sauvegarder sur votre système de fichiers local. Nous vous guiderons ensuite au cours des étapes d'utilisation de votre autorité de certification pour compléter le fichier de configuration.

### Fichier de configuration

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

Copiez l'intégralité de ce fichier dans un éditeur de texte dans lequel vous pouvez l'éditer et le sauvegarder sur votre système de fichiers local en tant que fichier JSON. Vous n'aurez à remplir que les deux premières sections de ce fichier : `"enrollment"` et `"tls"`.

Pour compléter le fichier de configuration, vous devez suivre plusieurs étapes du Moniteur réseau de votre réseau Starter Plan ou Enterprise Plan.

1. [Extraire les informations de noeud final de l'autorité de certification de votre Starter Plan ou Enterprise Plan](#ibp-ca-endpoint).
2. [Enregistrer l'homologue auprès de votre autorité de certification](#register-peer).
3. [Enregistrer un administrateur homologue](#register-admin) que vous utiliserez pour utiliser vos homologues. Cette étape n'est pas nécessaire si vous avez déjà enregistré un administrateur pour déployer un autre homologue.

Vous devez également suivre plusieurs étapes à l'aide du client de l'autorité de certification Fabric et de votre autorité de certification TLS déployée sur ICP.

1. Utilisez le client de l'autorité de certification Fabric pour [générer le dossier MSP admin de l'homologue](#enroll-admin).
2. [Procédez à l'extraction des informations de noeud final de votre autorité de certification TLS](#tls-ca-endpoint).
3. Utilisez le client de l'autorité de certification Fabric pour [enregistrer l'homologue auprès de votre autorité de certification TLS](#tls-register-peer).


### Informations relatives à l'autorité de certification Starter Plan ou Enterprise Plan
{: #ibp-ca-endpoint}

Tout d'abord, nous devons fournir les informations de connexion de votre autorité de certification sur {{site.data.keyword.cloud_notm}} au fichier de configuration. Connectez-vous à l'interface utilisateur du Moniteur réseau dans Starter Plan ou Enterprise Plan. A l'écran **Présentation** de votre Moniteur réseau, cliquez sur le bouton **Configuration de l'homologue distant**. Une fenêtre contextuelle s'affiche et elle contient les informations relatives à votre autorité de certification.

![Configuration de l'homologue distant](../images/myresources_starter.png "Configuration de l'homologue distant")
*Figure 1. Panneau Configuration de l'homologue distant*

La fenêtre en incrustation contient les zones suivantes :

  - **MSP de l'organisation**
  - **Nom de l'autorité de certification (CA)**
  - **URL de l'autorité de certification (CA)**
  - **Certificat TLS de l'autorité de certification (CA)**

Dans le fichier, sous la section `"components"`, entrez les valeurs suivantes indiquées plus haut :
- `"caname"` est la valeur du **Nom de l'autorité de certification (AC)**
- `"cahost"` est le nom d’hôte de l'URL de l'autorité de certification. Par exemple, si l'URL de l'autorité de certification est `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, la valeur de `"cahost"` serait `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com`
- `"caport"` est le port de `"cahost"`. Par exemple, si l'URL de l'autorité de certification est `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, la valeur de  `"caport"` serait `31011`.
- `"cacert"`  est la valeur de la zone **Certificat TLS de l'autorité de certification (AC)**. Avant d'insérer le certificat dans le fichier, vous devez l'encoder au format base64 en exécutant les commandes suivantes et en remplaçant la chaîne `<paste in Certificate Authority (CA) TLS Certificate>` par la valeur que vous avez copiée de votre Moniteur réseau.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
  ```
  {:codeblock}

  **Remarque :** Il est important que la chaîne générée par la commande ci-dessus soit mise en forme sur une seule ligne. Elle doit ressembler à ceci :

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```

  Mais pas à ceci :
  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
  ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
  Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
  VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
  WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

Collez la chaîne qui en résulte dans la zone `"cacert"` sous `"catls"` dans le fichier. Après la mise à jour, la zone `"cacert"` ressemble à l'exemple suivant :

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Enregistrement de l'homologue
{: #register-peer}

Pour joindre vos homologues à des canaux, installer et instancier du code blockchain, vous devez d'abord enregistrer l'homologue auprès de votre autorité de certification sur {{site.data.keyword.cloud_notm}}. Le déploiement de votre homologue peut ensuite générer les certificats qui sont nécessaires pour que l'homologue participe à un réseau Starter Plan ou Enterprise Plan. Procédez comme suit pour enregistrer un homologue avec un `ID d'inscription` et un `secret d'inscription`. Vous devez coller ces deux valeurs dans le fichier de configuration.

1. Connectez-vous à {{site.data.keyword.blockchainfull_notm}} Platform et accédez au Moniteur réseau. A l'écran "Autorité de certification", vous pouvez voir toutes les identités qui sont enregistrées auprès de votre organisation, comme votre admin ou vos applications client.
  ![CA screen](../images/CA_screen_starter.png "CA screen")
  *Figure 2. Ecran de l'autorité de certification*

2. Cliquez sur le bouton **Ajouter un utilisateur** à l'écran. Un écran contextuel s'ouvre et vous permet d'enregistrer votre homologue après que vous avez remplis les zones suivantes. Sauvegardez les valeurs ID et Valeur confidentielle car vous en aurez besoin lors de la configuration de votre homologue.
  - **ID d'inscription :** Nom d'utilisateur de votre homologue, référencé comme votre `ID d'inscription` lors de la configuration de votre homologue. **Sauvegardez cette valeur** pour le fichier de configuration.
  - **Secret d'inscription :** Mot de passe de votre homologue, référencé comme votre `Secret d'inscription` lors de la configuration de votre homologue. **Sauvegardez cette valeur** pour le fichier de configuration.
  - **Type :** Sélectionnez `Homologue` pour cette zone.
  - **Affiliation :** Il s'agit de l'affiliation de votre organisation, `org1` par exemple, à laquelle appartiendra votre homologue. Sélectionnez une affiliation existante dans la liste déroulante ou entrez-en une nouvelle.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si cette zone n'est pas renseignée, la valeur par défaut est un nombre illimité d'inscriptions.

  Après avoir renseigné les zones, cliquez sur **Soumettre** pour enregistrer l'homologue. L'homologue enregistré est ensuite répertorié dans le tableau en tant qu'identité dans votre organisation. Par mesure de sécurité, utilisez chaque identité, ainsi que l'ID d'inscription et la valeur confidentielle qui l'accompagnent, pour déployer un seul homologue. Ne réutilisez par les ID et les mots de passe de l'homologue.

3. Dans le fichier de configuration, sous la section `"components"`, entrez les valeurs suivantes :
  - `"enrollid"` est la valeur de l'`ID d'inscription` de l'étape précédente.
  - `"enrollsecret"` est la valeur du `secret d'inscription` de l'étape précédente.


### Création d'un administrateur
{: #register-admin}

Après enregistrement de l'identité de l'homologue, vous devez aussi créer une identité administrateur que vous utiliserez pour exploiter l'homologue. Vous devez d'abord enregistrer cette nouvelle identité auprès de votre autorité de certification, puis  l'utiliser pour générer un dossier MSP. Vous ajoutez ensuite ajouter le certificat signCert des administrateurs au fichier de configuration, où il deviendra un certificat admin de l'homologue lors du déploiement. Cela vous permet d'utiliser les certificats de l'identité admin pour faire fonctionner votre réseau blockchain, comme le démarrage d'un nouveau canal ou l'installation d'un code blockchain sur vos homologues.

Il vous suffit de créer une identité admin pour les composants appartenant à votre organisation. Si vous déployez plusieurs homologues, vous ne devez effectuer ces étapes qu'une seule fois. Vous pouvez utiliser le certificat signCert généré pour un homologue pour déployer d'autres homologues appartenant à votre organisation.

1. Connectez-vous à {{site.data.keyword.blockchainfull_notm}} Platform et accédez au Moniteur réseau. A l'écran "Autorité de certification", vous pouvez voir toutes les identités qui sont enregistrées auprès de votre organisation, comme votre admin ou vos applications client.
  ![CA screen](../images/CA_screen_starter.png "CA screen")
  *Figure 2. Ecran de l'autorité de certification*

2. Cliquez sur le bouton **Ajouter un utilisateur** à l'écran. Un écran contextuel s'ouvre et vous permet d'enregistrer votre homologue après que vous avez remplis les zones suivantes. Sauvegardez les valeurs ID et Valeur confidentielle car vous en aurez besoin lors de la configuration de votre homologue.
  - **ID d'inscription :** Nom d'utilisateur de votre admin homologue, référencé comme votre `ID d'inscription` lors de la configuration de votre homologue. **Sauvegardez cette valeur** à des fins de génération du dossier MSP admin.
  - **Secret d'inscription :** Mot de passe de votre admin homologue, référencé comme votre `Secret d'inscription` lors de la configuration de votre homologue. **Sauvegardez cette valeur** à des fins de configuration du dossier MSP admin.
  - **Type :** Sélectionnez `Homologue` pour cette zone.
  - **Affiliation :** Il s'agit de l'affiliation de votre organisation, `org1` par exemple, à laquelle appartiendra votre homologue. Sélectionnez une affiliation existante dans la liste déroulante ou entrez-en une nouvelle.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si cette zone n'est pas renseignée, la valeur par défaut est un nombre illimité d'inscriptions.

  Après avoir saisi ces zones, cliquez sur **Soumettre** pour créer l'administrateur. Les administrateurs enregistrés sont ensuite répertoriés dans le tableau en tant qu'identité dans votre organisation.

### Génération du dossier MSP admin homologue
{: #enroll-admin}

Une fois l'identité admin enregistrée, vous devez générer le dossier MSP admin homologue et le certificat SignCert. Par conséquent, vous devez exécuter une commande enroll pour votre autorité de certification Starter Plan ou Enterprise Plan.

1. Téléchargez le [client CA Fabric](/docs/services/blockchain/howto/CA_operate.html#fabric-ca-client) si ce n'est déjà fait.
2. Accédez au répertoire dans lequel vous allez stocker votre matériel cryptographique et créez le dossier dans lequel vous allez stocker le dossier MSP de votre admin homologue.

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. Définissez le chemin dans lequel le client peut créer vos certificats :`$FABRIC_CA_CLIENT_HOME`. Vérifiez que vous avez supprimé la configuration matérielle qui a peut-être été créée par des tentatives préalables. S'il s'agit de la première fois que vous exécutez la commande `enroll`, le dossier `msp` et le fichier `.yaml` n'existent pas.


  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. Ouvrez le fichier JSON **Connection Profile** depuis le panneau "Présentation" dans le Moniteur réseau, puis recherchez les variables suivantes :
  - URL de l'autorité de certification : `url` sous `certificateAuthorities`
  - Nom de l'autorité de certification : `caName`

5. Téléchargez les certificats TLS depuis {{site.data.keyword.cloud_notm}} en fonction du plan de service, de l'emplacement et du cluster que vous utilisez. Vous pouvez rechercher votre cluster en fonction du nom de domaine de l'URL de votre autorité de certification : `us01.blockchain.ibm.com:31011` ou `us02.blockchain.ibm.com:31011` par exemple.

  - Certificat TLS racine pour le plan Starter
    - Etats-Unis : [us01.blockchain.ibm.com.cert ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Royaume-Uni : [uk01.blockchain.ibm.com.cert ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney : [aus01.blockchain.ibm.com.cert ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [Root TLS Cert for Enterprise Plan ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Sauvegardez ce contenu dans un répertoire où vous pouvez y faire référence dans les commandes futures.

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. Exécutez la commande suivante pour générer des certificats avec votre identité admin homologue :

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  Le `<enroll_id>` et le `<enroll_password>` ci-dessus sont l'**ID** et le  **secret** de l'admin homologue qui a été [enregistré à l'aide du Moniteur réseau](#register-admin). Le `<ca_name>` et `<ca_url_with_port>` sont les valeurs de  `caName` et  `url` de votre profil de connexion. Laissez `http://` au début de l'URL de l'autorité de certification.

  Un appel réel doit ressembler à ce qui suit dans l'exemple de commande :

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  Une fois cette commande exécutée, elle génère un nouveau dossier MSP dans le répertoire que vous avez spécifié :`$FABRIC_CA_CLIENT_HOME`. Ce répertoire contient les certificats que vous allez utiliser pour exploiter votre homologue.

7. Dans le dossier MSP qui vient d'être créé, ouvrez le fichier SignCert du nouvel utilisateur et convertissez-le au format base64. Vous trouverez le fichier signCert dans le dossier `signcerts` du répertoire MSP. Si vous utilisez les étapes en exemple, vous pouvez utiliser les commandes suivantes :

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
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

  Cette commande produit une chaîne qui est semblable à l'exemple suivant :

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  Entrez cette chaîne dans la zone `"admincerts"` sous la section component dans le fichier de configuration.

### Informations relatives à l'autorité de certification TLS
{: #tls-ca-endpoint}

Les zones `"tls"` dans le fichier de configuration requièrent des informations de l'autorité de certification que vous avez déployée sur ICP. Vous utiliserez cette autorité de certification en tan qu'autorité de certification TLS distincte, ce qui rend votre homologue plus sûr. Utilisez les instructions suivantes pour générer les informations pertinentes :

- Les valeurs `"cahost"` et `"caport"` représentent l'URL et le port de  l'[URL d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-url). Par exemple, si l'URL de l'autorité de certification est `http://9.30.94.174:30167`, la valeur de `cahost` serait `9.30.94.174` et `caport` serait `30167`.
- Le `"caname"` est nom d'autorité de certification TLS de l'autorité de certification que vous avez déployée sur ICP. Le nom de l'autorité de certification TLS est la valeur que vous avez fournie dans la zone relative au `nom de l'instance TLS de l'autorité de certification` pendant la configuration de l'autorité de certification.
- La valeur `"cacert"` est le certificat TLS encodé en base64 de votre autorité de certification. Mettez à jour la section suivante avec la valeur du résultat de la commande lors de l'extraction de votre [certificat TLS de l'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-tls) en tant que prérequis.

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

### Enregistrement de l'homologue auprès de l'autorité de certification TLS
{: #tls-register-peer}

Vous devez enregistrer votre homologue auprès de l'autorité de certification TLS sur ICP à l'aide du client de l'autorité de certification Fabric.

1. Le fichier de certificat TLS `tls.pem` doit se trouver dans le dossier `$HOME/fabric-ca-client/catls`. Su ce n'est pas le cas, vous pouvez copier le certificat TLS que vous avez[téléchargé depuis ICP](/docs/services/blockchain/howto/CA_operate.html#ca-tls) dans un répertoire où vous pouvez le référencer dans les commandes ci-dessous. Vérifiez que vous êtes au niveau de votre répertoire `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. Vous devez vous inscrire à l'aide de l'admin de l'autorité de certification TLS. Placez `$FABRIC_CA_CLIENT_HOME` dans un répertoire où vous voulez stocker vos certificats admin d'autorité de certification TLS.

  ```
  cd $HOME/fabric-ca-client
  mkdir tlsca-admin
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. Exécutez la commande ci-dessous pour générer des certificats avec l'admin d'autorité d'administration TLS.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  Le `<enroll_id>` et le `<enroll_password>` dans la commande sont le[nom d'utilisateur et le mot de passe admin d'autorité de certification](/docs/services/blockchain/howto/CA_deploy_icp.html#admin-secret) que vous avez transmis au secret Kubernetes lors du déploiement de l'autorité de certification. Insérez  l'[URL d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-url) dans le `<ca_url_with_port>`. Laissez `http://` au début. Le `<tls_ca_name>` est celui que vous avez spécifié lors de la [configuration de l'autorité de certification](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms).

  Le `<ca_tls_cert_file>` est le nom de fichier de votre [certification TLS d'autorité de certification](/docs/services/blockchain/howto/CA_operate.html#ca-tls) avec son chemin d'accès complet.

  Un appel réel doit ressembler à l'exemple suivant :


  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Une fois l'inscription effectuée, vous disposez des certificats nécessaires pour enregistrer l'homologue auprès de l'autorité de certification TLS.

4. Exécutez la commande suivante pour rechercher votre affiliation et le nom de votre organisation.

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  La commande peut ressembler à l'exemple suivant :

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Vous devez voir des informations similaires à l'exemple suivant :

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  Notez la valeur **affiliation** de votre organisation, par exemple, `org1.department1` pour l'organisation `org1` dans l'exemple ci-dessus. Vous devez utiliser cette valeur dans la commande ci-dessous.

5. Exécutez la commande suivante enregistrer l'homologue.

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Vous devez utiliser le nom de l'autorité de certification de votre TLS dans la zone `--caname`. Créez un nom et un mot de passe pour l'homologue, puis utilisez-les pour remplacer `name` et `secret`. **Important :** Notez ces informations. Vous devez entrer la valeur `name` et `secret` pour `"enrollid"` et `"enrollsecret"` dans la section `"tls"` du fichier de configuration.

  Voici un exemple de commande :

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Une fois cette commande exécutée, vous pouvez mettre à jour le fichier en utilisant les valeurs précédentes :

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

  Vous ne pouvez enregistrer une identité qu'une seule fois. Si vous rencontrez un problème, essayez d'exécuter une commande avec un nouveau nom d'utilisateur et un nouveau mot de passe. Par mesure de sécurité, utilisez chaque identité, ainsi que l'ID d'inscription et la valeur confidentielle qui l'accompagnent, pour déployer un seul homologue. Ne réutilisez par les ID et les mots de passe de l'homologue.

  Lorsque la commande aboutit, vous pouvez voir des informations semblables à l'exemple suivant :

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

Vous pouvez exécuter une commande tree pour vérifier les opérations effectuées pour préparer le fichier de configuration. Accédez au répertoire dans lequel vous avez stocké vos certificats. Une commande tree doit générer un résultat similaire à la structure suivante :

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Hôtes CSR (Demande de signature de certificat)
{: #csr-hosts}

Vous devez fournir les noms d'hôte CSR pour déployer un homologue. Les noms d'hôte CSR incluent l'adresse IP proxy du cluster où vous déployez le composant ainsi que le `nom d'hôte de service` qui sera le nom d’hôte de votre charte Helm.

#### Recherche de la valeur de l'adresse IP proxy du cluster

Si vous voulez déployer un homologue sur le même cluster ICP que celui sur lequel vous avez déployé l'autorité de certification de votre TLS, entrez la même IP proxy que celle utilisée lorsque vous avez [configuré l'autorité de certification de votre TLS](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms). Si vous voulez déployer le composant sur un autre cluster, vous pouvez extraire la valeur de l'adresse IP proxy du cluster à partir de la console ICP. Vous devez avoir le rôle admin de cluster du cluster ICP où l'homologue va être déployé.

1. Connectez-vous à la console ICP. Dans le panneau de navigation gauche, cliquez sur **Plateforme** puis sur **Noeuds** pour afficher les noeuds qui sont définis dans le cluster.
2. Cliquez sur le noeud avec le rôle `proxy`, puis copiez la valeur de l'`IP hôte` de la table.
3. Insérez l'`IP hôte` comme valeur de `"hosts"` dans la section `"csr"` du fichier de configuration ci-dessous :

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### Détermination du nom de l'hôte de service

Le `nom de l'hôte de service` sera le `nom de l'édition Helm` que vous indiquez lors du déploiement. Si l'adresse proxy IP du cluster est 9.42.134.44" et que le `nom d'édition Helm` est `org1peer1`, vous devez insérer la section `"csr"` du fichier :

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

Une fois toutes les étapes effectuées, votre fichier de configuration mis à jour doit être similaire à l'exemple suivant :

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer",
			"enrollsecret": "peerpw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
           "org1peer1"
        ]
			}
		}
  }
}
```
{:codeblock}

Une fois que vous avez terminé de remplir ce fichier, vous devez le sauvegarder au format JSON et le transmettre au déploiement de votre homologue en tant que secret Kurbernetes. Créez le secret en suivant les étapes de la [section suivante](#peer-config-file-ibp).

## Création du secret de configuration
{: #peer-config-file-ibp}

Un [secret Kubernetes![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/configuration/secret/) vous permet de protéger et de partager des informations sans avoir à les transmettre directement au déploiement. Une fois que vous avez sauvegardé le fichier de configuration, vous devez l'encoder en `base64`.

1. Pour encoder le fichier de configuration au format base64, exécutez les commandes suivantes dans une fenêtre de terminal :

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
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

  Sauvegardez la sortie obtenue à l'étape quatre ci-dessous.

2. Connectez-vous à la console ICP. Dans le panneau de navigation gauche, cliquez sur **Configuration** puis sur **Secrets**. Cliquez sur le bouton **Créer Secret** pour ouvrir un panneau en incrustation qui vous permet de générer un nouvel objet secret.

3. Sous l'onglet **Général**, remplissez les zones suivantes :
  - **Nom :** Donnez un nom unique à votre secret au sein de votre cluster. Ce nom sera utilisé lors du déploiement de votre homologue. Il doit être entièrement en minuscules.  
  **Remarque :** Lorsque vous déployez un homologue, un nouveau secret est automatiquement généré par le déploiement sous le nom `<helm_release_name>-secret`. Par conséquent, lorsque vous nommez votre secret, assurez-vous que son nom soit  différent de `<helm_release_name>-secret` . Sinon, la déploiement de la charte Help échouera car le secret qu'il essaie de créer existe déjà.
  - **Espace de nom :** espace de nom pour l'ajout de votre secret. Sélectionnez l'`espace de nom` dans lequel vous voulez déployer votre homologue.
  - **Type :** entrez la valeur `Opaque`.

4. Cliquez sur l'onglet **Données** dans le panneau de navigation gauche de cette fenêtre. Dans les zones `Nom`, indiquez `secret.json` et dans la zone de valeur collez la chaîne encodée `base64` de votre fichier de configuration.

5. (Facultatif) Si vous prévoyez d'utiliser `CouchDB` comme base de données d'état, vous devez ajouter deux valeurs supplémentaires à cet objet secret. Sous l'onglet **Données**, cliquez sur le bouton **Ajouter des données** pour ajouter l'ID utilisateur et le mot de passe CouchDB à utiliser pour la base de données lors du déploiement du conteneur.
  1. Créez vos nom d'utilisateur et mot de passe et encodez les valeurs au format base64. Exécutez les commandes suivantes dans une fenêtre de terminal et remplacez `admin` et `adminpw` par les valeurs que vous souhaitez utiliser.
```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

   2. Dans la zone **Nom**, entrez la valeur `couchdbuser`. Dans la zone **Value** correspondante, entrez le résultat de `echo -n 'couch' | base64 $FLAG` dans l'étape 1 plus haut.
   3. Cliquez sur le bouton **Ajouter des données** pour ajouter une seconde paire clé-valeur.
   4. Dans la seconde zone **Nom**, entrez la valeur `couchdbpwd`. Dans la zone **Value** correspondante, entrez le résultat de `echo -n 'couchpw' | base64 $FLAG` dans l'étape 1 plus haut.

6. Cliquez sur **Créer** pour sauvegarder votre objet secret.

**Remarque :** Le secret de configuration de l'homologue ne sera pas retiré de votre cluster ICP lors de la suppression de votre édition Helm. Si vous supprimez votre homologue, vous devez également supprimer ce secret.

## Configuration
{: #peer-configuration}

Après avoir créé votre objet secret de configuration de l'homologue, vous pouvez configurer et installer votre homologue dans ICP en suivant les étapes ci-après. Vous pouvez installer un seul homologue à la fois.

1. Connectez-vous à la console ICP et cliquez sur le lien **Catalogue** dans l'angle supérieur droit.
2. Cliquez sur `Blockchain` dans le panneau de navigation gauche afin de localiser la vignette libellée `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` si vous avez téléchargé l'édition Community. Cliquez sur la vignette afin de l'ouvrir et consulter un fichier Readme qui contient des informations sur l'installation et la configuration de la charte Helm.
3. Cliquez sur l'onglet **Configuration** dans la partie supérieure du panneau ou cliquez sur le bouton **Configurer** dans l'angle inférieur droit.
4. Indiquez les valeurs relatives aux [paramètres de configuration généraux](#peer-global-parameters) et acceptez le contrat de licence.
5. Ouvrez le bouton de développement `Tous les paramètres` et indiquez la valeur des [paramètres de configuration généraux](#peer-global-parameters).
6. Faites défiler vers le bas jusqu'à la section **Configuration de l'homologue**. Sélectionnez la case à cocher relative à l'`installation de l'homologue` et remplissez les  [paramètres de configuration](#peer-parameters) de l'homologue.
7. Cliquez sur **Installer**.

### Paramètres de configuration
{: #ibp-peer-configuration-parms}

Le tableau suivant répertorie les paramètres configurables de {{site.data.keyword.blockchainfull_notm}} Platform, **spécifiques au composant de l'homologue**, ainsi que leurs valeurs par défaut. Si vous découvrez l'homologue ou si vous démarrez, utilisez les valeurs par défaut des paramètres de base de données et de stockage. Faites défiler jusqu'à la section relative au composant de l'homologue pour sélectionner la case relative à l'`installation de l'homologue` et fournissez les informations de configuration pour ce composant uniquement. **Même si l'interface utilisateur de la charte Helm indique qu'aucune configuration supplémentaire n'est nécessaire, vous devez entrer certains paramètres pour déployer un homologue.**

#### Paramètres de configuration généraux et globaux
{: #peer-global-parameters}

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
| `Helm release name`| Nom de votre édition Helm. Doit commencer par une lettre minuscule et se terminer par une caractère alphanumérique, doit contenir uniquement des traits d'union et des caractère alphanumérique minuscules. Vous devez utiliser un nom d'édition Helm unique chaque fois que vous essayez d'installer un composant. **Important :** Cette valeur doit correspondre à la valeur que vous avez utilisée pour générer le 'nom d'hôte de service' pour la zone "hosts" dans votre [fichier de secret JSON.](#csr-hosts) | none | yes  |
| `Target namespace`| Choisissez l'espace de nom Kubernetes pour installer la charte Helm. | none | yes |
| MD:CODE>Global configuration**| Paramètres qui s'appliquent à tous les composants de la charte Helm|||
| `Service account name`| Entrez le nom du compte de service  [![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que vous allez utiliser pour exécuter le pod. | default | no |

#### Paramètres de configuration de l'homologue
{: #peer-parameters}

|  Paramètre     | Description    | Val. déf  | Requis |
| --------------|-----------------|-------|------- |
|**Cluster configuration** |**Informations de configuration de cluster** | ||
| `Install Peer` | Sélectionner pour installer un homologue|non sélectionné | oui, si vous souhaitez installer un homologue |
| `Peer worker node architecture`| Sélection de votre architecture de plateforme cloud (AMD64 ou S390x).| AMD64 | yes |
| `Peer image repository`| Emplacement de la charge Helm de votre homologue. Cette zone est remplie automatiquement par le chemin installé. Si vous utilisez Community Edition et ne disposez pas d'un accès Internet, il doit s'agir du répertoire où vous avez téléchargé l'image d'homologue Fabric. | ibmcom/ibp-fabric-peer | yes |
| `Peer Docker image tag`|Valeur de la balise associée à l'image de l'homologue |1.2.1, renseigné automatiquement avec la valeur correcte.|yes|
| `Peer configuration`|Vous pouvez personnaliser la configuration de l'homologue en collant votre propre fichier de configuration `core.yaml` dans cette zone. Pour voir un exemple de fichier `core.yaml`, voir l'exemple de config [`core.yaml` ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **Pour les utilisateurs avancés uniquement**. |none|no|
| `Peer configuration secret (Required)`| Nom du [secret de configuration d'homologue](#peer-config-secret) que vous avez créé dans ICP.  |none|yes|
|`Organization MSP (Required)`|Cette valeur peut être trouvée dans le Moniteur réseau  (interface utilisateur IBP) en cliquant sur "Configuration de l'homologue distant" sur l'écran Présentation. |none|yes|
|`Peer service type`| Utilisé pour indiquer si des[ports externes doivent être exposés ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) sur l'homologue. Sélectionnez NodePort pour exposer les ports en externe (recommandé), et ClusterIP pour ne pas exposer les ports. LoadBalancer et ExternalName ne sont pas pris en charge dans cette édition. | NodePort |yes|
| `State database`| [Base de données d'état](/docs/services/blockchain/glossary.html#state-database) utilisée pour le stockage de votre registre de canal. L'homologue doit utiliser la même base de données que votre [réseau de blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | LevelDB | yes |
|`CouchDB image repository`| S'applique uniquement si CouchDB a été sélectionné comme base de données de registre. Cette zone est remplie automatiquement par le chemin installé. Si vous utilisez Community Edition et ne disposez pas d'un accès Internet, il doit s'agir du répertoire où vous avez téléchargé l'image CouchDB Fabric. | ibmcom/ibp-fabric-couchdb | yes |
| `CouchDB Docker image tag`| S'applique uniquement si CouchDB a été sélectionné comme base de données de registre. Valeur de la balise associée à l'image CouchDBde. | Renseigné automatiquement par la valeur correcte.| yes |
| `Peer Data persistence enabled`| Activité de la capacité de persistance des données après un redémarrage ou une défaillance du cluster. Voir la section relative au [stockage dans Kubernetes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/ "Volumes") pour plus de détails.  *Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod.* | checked | no |
| `Peer use dynamic provisioning`| Cochez pour activer la mise à disposition dynamique pour les volumes de stockage. | checked | no |
| `Peer persistent volume claim`| Pour une nouvelle réservation uniquement. Entrez un nom pour votre Réservation de volume persistant (PVC) à créer. | my-data-pvc | no |
| `Peer storage class name`| Indiquez une classe de stockage pour l'homologue. | Vide si création d'une nouvelle Réservation de volume persistant ; sinon, indiquez la classe de stockage qui est associée à la Réservation de volume persistant existante. | no |
| `Peer existing volume claim`| Indication du nom d'une Réservation de volume persistant existante et toutes les autres zones sont laissées vides. | none | no |
| `Peer selector label`| [Libellé de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs ") pour votre Réservation de volume persistant.| none | no |
| `Peer selector value`| [Valeur de sélecteur ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Libellés et sélecteurs") pour votre Réservation de volume persistant. | none | no |
| `Peer storage access mode`| Indication du [mode d'accès![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour la Réservation de volume persistant.  | ReadWriteMany| no |
| `Peer volume claim size`| Taille de la Réservation de volume doit être supérieure à 2 Gi. | 8Gi  | yes |
| `State database persistent volume claim`| Pour une nouvelle réservation uniquement. Entrez un nom pour votre Réservation de volume persistant (PVC) à créer. | statedb-pvc | no |
| `State database storage class name`| Indiquez une classe de stockage pour la base de données d'état. | none | no |
| `State database existing volume claim`| Indication du nom d'une Réservation de volume persistant existante et toutes les autres zones sont laissées vides. | none | no |
| `State database selector label`| [Libellé du sélecteur](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) pour votre PVC| none | no |
| `State database selector value`| [Valeur du sélecteur](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) pour votre PVC| none | no |
| `State database storage access mode`| Indication du [mode d'accès![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modes d'accès") au stockage pour la Réservation de volume persistant. | ReadWriteMany| no |
| `State database volume claim size`| Choisissez la taille de disque à utiliser. | 8Gi | yes |
| `CouchDB - Data persistence enabled`| Pour le conteneur CouchDB, les données de registre seront disponibles au redémarrage du conteneur. *Si ce paramètre est désélectionné, toutes les données seront perdues en cas de reprise en ligne ou de redémarrage du pod.*| checked | no |
| `CouchDB - Use dynamic provisioning`| Pour le conteneur CouchDB, utilisation de la mémoire dynamique Kubernetes. | checked | no |
| `Peer CPU request` | Nombre minimum d'UC à allouer à l'homologue. | 1 | yes |
| `Peer CPU limit` | Nombre maximum d'UC à allouer à l'homologue.| 2 | yes |
| `Peer Memory request` | Quantité minimum de mémoire à allouer à l'homologue. | 1Gi | yes |
| `Peer Memory limit` | Quantité maximum de mémoire à allouer à l'homologue. | 4Gi | yes |
| `CouchDB CPU request` | Nombre minimum d'UC à allouer à CouchDB.| 1 | yes |
| `CouchDB CPU limit` | Nombre maximum d'UC à allouer à CouchDB. | 2 | yes |
| `CouchDB Memory request` | Quantité minimum de mémoire à allouer à CouchDB.| 1Gi | yes |
| `CouchDB Memory limit` | Quantité maximum de mémoire à allouer à CouchDB. | 4Gi | yes |


Lorsque CouchDB est sélectionnée comme base de données de l'homologue, deux conteneurs sont créés dans le pod, l'un pour l'homologue et l'autre pour CouchDB.
Le conteneur homologue inclut un seul montage de volume pour la Réservation de volume persistant de l'homologue qui stocke les blocs et les transactions sur le système de fichiers. Le conteneur CouchDB monte la Réservation de volume persistant de la base de données d'état de l'homologue qui contient les données de registre.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Utilisation de la ligne de commande Helm pour installer l'édition Helm
{: #ibp-helm-cli}

Vous pouvez aussi utiliser l'interface de ligne de commande Helm pour installer l'édition Helm. Avant d'exécuter la commande `helm install`, assurez-vous  [d'ajouter le référentiel Helm de votre cluster à l'environnement de l'interface de ligne de commande Helm![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI").

Vous pouvez définir les paramètres requis pour l'installation en créant un fichier `yaml` et en le transmettant à la commande `helm install` suivante.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

où :
- `<helm_release name>` représente le nom que vous voulez affecter à votre édition Helm.
- `<helm_chart>` représente le nom de la charte Helm importée dans le catalogue.
- `<helm_chart_version>` représente la version de la charte Helm importée dans le catalogue.
- `<customvalues.yaml>` est le nom du fichier yaml qui contient les paramètres de configuration.

Par exemple :

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```
Vous pouvez créer un nouveau fichier `yaml` en éditant `values.yaml` inclus dans le fichier archive téléchargé, lequel inclut tous les paramètres nécessaires avec leurs valeurs par défaut.

## Vérification de l'installation de l'homologue
{: #verify-installation-ibp}

Une fois que vous avez entré les paramètres de configuration et cliqué sur le bouton **Installer**, cliquez sur le bouton **Afficher l'édition Helm** pour afficher votre déploiement. Si l'opération aboutit, vous devez voir la valeur 1 dans les zones  `DESIRED`, `CURRENT`, `UP TO DATE` et `AVAILABLE` dans le tableau Déploiement. Vous devrez peut-être cliquer sur Actualiser et attendre que le tableau soit mis à jour. Vous pouvez aussi afficher le tableau Déploiement en cliquant sur l'icône **Menu** dans l'angle supérieur gauche sur la console ICP. Dans la liste de menus, cliquez sur **Charges de travail**, puis sur **Editions Helm**.

Si vous faites défiler jusqu'à la section `Remarques`, vous verrez des informations importantes relatives à l'[exploitation de votre homologue](/docs/services/blockchain/howto/peer_operate_ibp.html).

<!--
### Verifying the peer can connect to Starter or Enterprise Plan network

You can run a peer CLI command from inside the peer container to verify that your peer has connected to your network on the {{site.data.keyword.blockchainfull_notm}} Platform. Complete the following instructions to run the `peer channel fetch` command to fetch the genesis block from a channel:

1. If you have not already connected to your ICP cluster, follow these [instructions](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-kubectl-configure) to connect to it and use the cli from inside the peer container.

2. If you deploy your peer behind a firewall, you need to open a passthru to enable the network on the platform to complete a TlS handshake with your peer. You only need to enable a passthru for the Node port bound to port 7051 of your peer. For more information, see [finding your peer endpoint information](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-endpoint).

3. Your organization needs to be added to a channel in the network before you can fetch the genesis block.

  - You can start a new channel for the peer. If you are using IBP Starter or Enterprise Plan, you can automatically include your organization during [channel creation](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    The peer uploads its signCert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joined and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

4. You also need to upload the signCert of your peer admin to Starter Plan or Enterprise Plan in order for a remote CLI or application to perform channel operations such as fetching the channel genesis block and instantiating chaincode.
    1. On your local machine, cat the file `HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem` and then copy it to the clipboard.
    2. Enter the Network Monitor of your network on {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Click **Members** in the left navigator and click the **Certificates** tab.
    4. Click the **Add Certificate** button.
    5. In the **Add certificate** window, give your certificate a name, such as "peer-admin", paste in the certificate you just copied to the clipboard and click **Submit**.
    6. You will be asked whether you want to restart the peers. Click **Restart**.
    7. Click **Channels** in the left navigator and locate the channel that the peer will join.
    8. Click the three dots under the **Actions** header and click **Sync Certificate**. In the **Sync certificate** window, click **Submit**.

5. Retrieve configuration information from your Connection profile available on the "Overview" screen of your Network Monitor. Click **Connection Profile** and then **Download**.

  - Find the orderer URL by searching for **orderers**, which is located under `orderers > url`. Make a note of the URL that begins with the network name. The URL is similar to the following example:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Find the name of your organization by searching for **organizations**. This should be the same organization as you use to register your peer. You can find your organization's name together with its associated `mspid`. Make a note of the value of the `mspid`.

6. Run the following commands to set the environment variables in the peer container.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Replace the fields with your own information.
  - Replace `<ORDERER_URL>` with the hostname and port of the orderer from the `creds.json` file.
  - Replace `<CHANNEL_NAME>` with the name of the channel that the peer joins.
  - Replace `<CC_NAME>` with any name to refer to your chaincode.
  - Replace `<ORGANIZATION_MSP_ID>` with the name of the organization from the `creds.json` file.
  - Replace `<PEER_ADDR>` with `localhost:7051`

  For example:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=org1
  export PEERADDR=localhost:7051
  ```
  {:codeblock}

7. Run the following peer CLI command to fetch the genesis block of the channel.

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
  ```
  {:codeblock}

  **Note:** It is possible that when you run any of these CLI commands, you might experience the following warning that can be safely ignored.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```

  Verify that the command worked successfully and that genesis block is added to your peer container by running the following command:

  ```
  ls *.block
  ```
  {:codeblock}

  You know that the genesis block is added successfully when you see something that is similar to the following example:

  ```
  defaultchannel_0.block
  ```

  Successfully fetching the genesis block indicates that your peer can connect to your Starter or Enterprise Plan network.
  You still need to join the peer to the channel and install chaincode. See [operating your peer](/docs/services/blockchain/howto/peer_operate_ibp.html) for more information.

-->

## Affichage des journaux de l'homologue
{: #peer-ibp-view-logs}

Les journaux de composant peuvent être affichés à partir de la ligne de commande à l'aide de [`commandes de l'interface CLI kubectl`](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-kubectl-configure) ou via [Kibana ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/products/kibana "Votre fenêtre dans Elastic Search"), qui est inclus dans votre cluster ICP. Pour plus d'informations, consultez les [instructions relatives à l'accès aux journaux](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-ibp-view-logs).

## Etapes suivantes

Après que vous avez déployé l'homologue, vous devez effectuer quelques étapes supplémentaires avant de soumettre des transactions et lire le registre partagé depuis le réseau de blockchain. Pour plus d'informations, voir  [Exploitation des homologues avec Starter Plan ou Enterprise Plan](/docs/services/blockchain/howto/peer_operate_ibp.html).

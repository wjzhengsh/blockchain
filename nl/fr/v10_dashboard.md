---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Exploitation d'un réseau de plan Enterprise
{: #v10_dashboard}

La plateforme {{site.data.keyword.blockchainfull}} comporte un Moniteur réseau qui fournit une vue d'ensemble de votre environnement de blockchain, notamment des composants réseau, des membres, des canaux rejoints, des données de performance et du code blockchain déployé. Le Moniteur réseau constitue également un point d'entrée pour l'exécution des API Swagger, le développement d'un réseau avec la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop, et le test de modèles d'application.
{:shortdesc}

Vous pouvez [changer le nom du réseau de votre plan Enterprise](#ep-network-name) dans le Moniteur réseau. 

Le Moniteur réseau présente les écrans suivants dans trois sections. Vous pouvez accéder à chaque écran dans le navigateur de gauche du Moniteur réseau.
- La section **Mon réseau** contient les écrans "[Présentation](#overview)", "[Membres](#members)", "[Canaux](#channels)", "[Notifications](#notifications)" et "[API](#apis)".
- La section **Mon code** contient les écrans "[Ecriture de code](#write_code)", "[Installer le code](#chaincode)" et "[Essayer les modèles](#samples)".
- L'écran "[Obtenir de l'aide](#support)" affiche des informations de support ainsi que les notes sur l'édition pour Helios et Hyperledger Fabric (codebase sur lequel repose la plateforme {{site.data.keyword.blockchainfull_notm}}).

Vous pouvez [vérifier et configurer les préférences réseau](#network-preferences) dans le menu déroulant dans l'angle supérieur droit du Moniteur réseau.

Ce tutoriel décrit chacun des écrans et fonctions ci-dessus.

## Mettre à jour le nom de réseau
{: #ep-network-name}

Lorsque vous créez un réseau du plan Enterprise, la plateforme {{site.data.keyword.blockchainfull_notm}} affecte un nom à votre réseau. Toutefois, vous pouvez à tout moment mettre à jour ce nom réseau dans votre Moniteur réseau.

Dans la partie supérieure gauche du Moniteur réseau, cliquez sur le nom de réseau et la zone devient éditable. Entrez le nouveau nom que vous voulez utiliser et appuyez sur **Entrée**. Le nom de votre réseau est mis à jour en quelques secondes.

La **Figure 1** illustre les étapes de mise à jour du nom de réseau du plan Starter : nom affecté remplacé par "Starter Plan Network".

![Mise à jour du nom réseau](images/update_network_name_ep.gif "Mise à jour du nom réseau")
*Figure 1. Mise à jour du nom réseau*


## Présentation
{: #overview}

L'écran "Présentation" affiche des informations d'état en temps réel concernant vos ressources blockchain, notamment le programme de tri, l'autorité de certification et les noeuds homologue. Chaque ressource s'affiche sous quatre en-têtes distincts : **Type**, **Nom**, **Statut** et **Actions**. Pendant la création de votre réseau de blockchain, trois noeuds de programme de tri et deux noeuds d'autorité de certification sont crées automatiquement. Les autorités de certification sont spécifiques à un membre, alors que les noeuds de programme de tri sont des noeuds finaux communs et partagés au sein du réseau.

La **Figure 2** illustre l'écran "Présentation" :

![Présentation du réseau](images/myresources.png "Présentation du réseau")
*Figure 2. Présentation du réseau*

### Actions de noeud
L'en-tête **Actions** du tableau comporte des boutons qui permettent de démarrer ou d'arrêter vos ressources. Vous pouvez également démarrer ou arrêter un groupe de noeuds en sélectionnant plusieurs noeuds et en cliquant sur le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés**. Le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés** apparaît au-dessus du tableau lorsque vous sélectionnez un ou plusieurs noeuds.

Notez que les actions Arrêter et Démarrer ne sont pas disponibles pour un noeud de programme de tri. En général, il n'est pas nécessaire d'arrêter et de démarrer des noeuds Homologues ou CA sur un réseau. Les opérations Arrêter et Démarrer sont fournies au cas où vous devez redémarrer un homologue, par exemple pour l'affichage dans un état nettoyé.

Vous pouvez également consulter les journaux de composant en cliquant sur **Afficher les journaux** dans la liste déroulante sous l'en-tête **Actions**. Ces journaux présentent les appels de procédure entre les différentes ressources réseau et ils sont utiles pour le débogage et le traitement des incidents. Vous pouvez par exemple les tester en arrêtant un homologue et en le ciblant avec une transaction ; vous verrez alors des erreurs de connectivité. Lorsque vous redémarrez l'homologue et relancez la transaction, vous verrez qu'une connexion est réussie. Vous
pouvez aussi arrêter un homologue pendant une longue période alors que vos canaux continuent à effectuer des transactions. l'homologue est redémarré, vous remarquez une synchronisation du registre dès qu'il reçoit les blocs qui ont été validés alors qu'il était arrêté. Une fois que le registre est complètement synchronisé, vous pouvez procéder à des appels et des requêtes normaux.

### Profil de connexion
{: #enterprise-connection-profile}
Vous pouvez consulter le ficher JSON pour les informations réseau de faible niveau de chaque ressource en cliquant sur le bouton **Profil de connexion**. Le profil de connexion contient toutes les informations de configuration dont vous avez besoin pour une application. Cependant, ce fichier contient uniquement les adresses de vos composants spécifiques et du programme de tri ; si vous devez cibler des homologues supplémentaires, vous devez obtenir leurs noeuds finaux. L'en-tête "url" affiche le noeud final d'API pour chaque composant. Ces noeuds finaux sont requis pour cibler les composants réseau spécifiques d'une application côté client, et leurs définitions résident généralement dans un fichier de configuration modélisé JSON qui est fourni avec l'application. Si vous personnalisez une application qui nécessite l'adhésion d'homologues qui ne font pas partie de votre organisation, vous devrez obtenir ces adresses IP auprès des opérateurs concernés dans le cadre d'une opération externe. Les clients doivent pouvoir se connecter aux
homologues dont ils attendent une réponse.

### Ajout d'homologues
{: #peers}
Cliquez sur le bouton **Ajouter des homologues** pour ajouter des noeuds homologue à votre réseau. Dans le plan Starter, deux homologues sont ajoutés automatiquement lors de la création du réseau. Dans le plan Enterprise, vous pouvez ajouter des noeuds homologue pour la première fois lorsque vous créez ou rejoignez un réseau ou ultérieurement dans le moniteur réseau. Vous pouvez vous trouver dans différents scénarios lorsque vous avez besoin d'homologues supplémentaires.  Par exemple, vous pouvez souhaiter que plusieurs homologues rejoignent le même canal à des fins de redondance. Chaque homologue traite les transactions du canal et leurs copies respectives du registre. Dans le cas où l'un des homologues est défaillant, les autres peuvent poursuivre le traitement des transactions et des demandes d'application.  Vous pouvez équilibrer de manière symétrique toutes les demandes d'applications entre homologues, ou vous pouvez cibler différents homologues pour différentes fonctions. Par exemple, vous pouvez utiliser un homologue pour interroger le registre et utiliser un autre homologue pour traiter les adhésions pour les mises à jour du registre.

  Dans la fenêtre contextuelle "Ajouter des homologues", sélectionnez le nombre de noeuds homologue que vous voulez ajouter. <!--Currently only "small" peers are available for purchase, however there will eventually be "medium" and "large" to help accommodate larger workloads and higher transaction throughput.-->

## Membres
{: #members}
L'écran "Membres" comporte deux onglets qui affichent des informations relatives aux membres du réseau (onglet "Membres") et des informations sur les certificats (onglet "Certificats").

### Membres
{: #members_tab}
La **Figure 3** illustre l'écran "Membres" initial qui affiche les membres de votre réseau sous l'onglet "Membres" :

![Onglet Membres de l'écran Membres](images/monitor_members.png "Membres du réseau")
*Figure 3. Membres du réseau*

Outre les membres que vous invitez lors de la création du réseau, vous pouvez inviter d'autres membres sous l'onglet "Membres". Pour inviter un membre à rejoindre votre réseau, entrez le nom de l'institution et l'adresse électronique de l'opérateur et cliquez sur **Ajouter un membre**. Un réseau peut comporter au total 15 membres (initiateur du réseau compris). Pour retirer un membre de votre réseau, cliquez sur le symbole "retirer" en fin de ligne du membre.

### Certificats
La **Figure 4** présente l'écran "Membres" initial qui affiche les certificats de membre sous l'onglet "Certificats" :

![Onglet Certificats de l'écran Membres](images/monitor_certificates.png "Certificats")
*Figure 4. Certificats*

Les opérateurs peuvent gérer les certificats des membres d'une même institution sous l'onglet "Certificats". Cliquez sur **Ajouter le certificat** pour afficher l'écran "Ajouter le certificat". Donnez un nom à votre certificat, collez vos certificats côté client au format PEM dans la zone "Clé", puis cliquez sur **Soumettre**. Vous devez redémarrer vos homologues pour que les certificats côté client puissent être pris en compte.

Pour plus d'informations sur la génération de votre clé de certificat, voir [Génération de certificats côté client](v10_application.html#generating-the-client-side-certificates).

## Canaux
{: #channels}

Vous pouvez diviser votre réseau en canaux, où chaque canal représente un sous-ensemble de membres qui sont autorisés à consulter les données du code blockchain instancié sur ce canal. Chaque réseau doit comporter au moins un canal pour que les transactions puissent s'effectuer. Chaque canal a un registre unique et les utilisateurs doivent être correctement authentifiés pour exécuter des opérations de lecture/écriture sur ce registre. Si vous n'êtes pas sur un canal, vous ne pouvez voir aucune donnée.

La **Figure 5** illustre l'écran de tableau de bord initial qui affiche une présentation de tous les canaux de votre réseau.

![Canaux](images/channels.png "Canaux")
*Figure 5. Canaux*

La création d'un canal entraîne la génération d'un registre spécifique à un canal. Pour plus d'informations, voir [Création d'un canal](howto/create_channel.html).

Vous pouvez aussi sélectionner un canal existant afin d'afficher des détails plus précis sur ce canal, l'appartenance et le code blockchain actif. Pour plus d'informations, voir [Surveillance d'un réseau](howto/monitor_network.html).

## Notifications
{: #notifications}

Vous pouvez traiter les demandes en attente et consulter les demandes terminées dans l'écran "Notifications".

La **Figure 6** illustre l'écran "Notifications" :

![Notifications](images/notifications.png "Notifications")
*Figure 6. Notifications*

Lorsque vous créez un canal ou êtes invité à rejoindre un nouveau canal, une notification s'affiche dans le moniteur réseau.

Les demandes sont regroupées dans les sous-onglets "Toutes", "En attente" et "Terminée". Les nombres figurant à la suite de l'en-tête du sous-onglet indiquent le nombre de demandes dans chaque sous-onglet.
   * Vous pouvez trouver toutes vos demandes sous le sous-onglet "Toutes".
   * Les demandes que vous n'avez pas acceptées ou que vous avez refusées, ou que vous n'avez pas encore consultées, figurent sous le sous-onglet "En attente". Cliquez sur le bouton **Inspecter la demande** pour consulter la demande, qui comporte la règle de canal et les membres, ainsi que le statut de vote. Si vous être un opérateur de canal, vous pouvez soit **Accepter** soit **Décliner** la demande, ou la traiter à un autre moment en cliquant sur **Plus tard**. Si la demande est acceptée par suffisamment d'opérateurs de canal, vous pouvez cliquer sur **Soumettre une demande** pour activer la mise à jour de canal.
   * Une demande soumise apparaît alors dans le sous-onglet "Terminée".  Vous pouvez cliquez sur **Inspecter la demande** pour afficher ses détails.

Si vous avez une longue liste de demandes, vous pouvez rechercher dans la zone de recherche située dans la partie supérieure de l'écran.

Les demandes en attente peuvent être supprimées en sélectionnant la case en regard et en cliquant sur **Supprimer la demande**. Notez qu'une demande terminée ne peut pas être supprimée.

## API
{: #apis}

Pour faciliter le développement d'applications, la plateforme {{site.data.keyword.blockchainfull_notm}} expose les API que vous pouvez tester sur votre réseau dans une interface utilisateur Swagger.

La **Figure 7** illustre l'écran "API" :

![API](images/API_screen.png "API")
*Figure 7. API*

Cliquez sur le lien **Interface utilisateur swagger** pour ouvrir l'identificateur Swagger. Notez que vous devez autoriser l'interface utilisateur swagger à l'aide de vos données d'identification réseau (qui se trouvent sur la page d'API) avant d'exécuter les API. Pour plus de détails, voir [Interaction avec le réseau à l'aide d'API Swagger](howto/swagger_apis.html).

## Développement de Code
{: #write-code}

Le plan Enterprise intègre la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop et fournit un environnement de développement avec des outils et des technologies standard du secteur. Vous pouvez développer votre réseau dans l'environnement en ligne ou en local. Dès que vous avez développé un réseau, vous pouvez le redéployer dans votre réseau de plan Enterprise.

La **Figure 8** illustre l'écran "Développement de code" :

![Développement de code](images/write_code.png "Développement de code")
*Figure 8. Développement de code*

Pour plus d'informations sur le développement et le déploiement de votre code avec le plan Enterprise, voir [Déploiement d'un réseau d'entreprise dans un plan Starter](develop_enterprise.html).

## Installer le code
{: #chaincode}

Le code blockchain, également appelé "contrat intelligent", est l'élément logiciel qui contient un ensemble de fonctions permettant d'interroger et de mettre à jour le registre. Ils sont installés sur des homologues et instanciés sur un canal.

La **Figure 9** illustre l'écran "Installer le code" :

![Installer le code](images/chaincode_install_overview.png "Installer le code")
*Figure 9. Installer le code*

Un code blockchain est tout d'abord installé sur le système de fichiers d'un homologue, puis il est instancié sur un canal. Pour plus d'informations, voir [Installation, instanciation et mise à jour d'un code blockchain](howto/install_instantiate_chaincode.html).

## Essayer les modèles
{: #samples}

Des modèles d'application vous permettent d'avoir une meilleure compréhension d'un réseau de blockchain et du développement d'application. Suivez le lien jusqu'au référentiel Billes dans le Moniteur réseau pour plus d'informations sur l'installation du modèle d'application Billes. Pour plus d'informations sur le développement et le déploiement de vos propres exemples, voir [Développement d'applications](v10_application.html).

La **Figure 10** illustre l'écran "Essayer les modèles" :

![Essayer les modèles](images/sample_overview_ep.png "Essayer les modèles")
*Figure 10. Essayer les modèles*

## Obtenir de l'aide
{: #support}

L'écran "Obtenir de l'aide" comporte deux onglets qui fournissent des informations de support sous l'onglet "Support" et une description des fonctions nouvelles et modifiées de chaque édition sous l'onglet "Notes sur l'édition".

La **Figure 11** illustre l'écran "Support" initial qui comporte des informations de support sous l'onglet "Support" :

![Support](images/support.png "Support")
*Figure 11. Support Blockchain*

Utilisez les liens et les ressources de cette page pour accéder à des forums de dépannage et de support.

* [Docs de service {{site.data.keyword.blockchainfull_notm}}](index.html), qui est le présent site de documentation, fournit une aide sur la mise en route de la plateforme {{site.data.keyword.blockchainfull}} sur {{site.data.keyword.Bluemix_notm}}. Vous pouvez accéder aux rubriques correspondantes depuis le navigateur de gauche ou rechercher un terme à l'aide de la fonction de recherche située en haut de l'écran.
* [IBM Developer Works ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/blockchain/) sous **Aide communautaire** comporte des ressources et des informations pour les développeurs.
* [IBM dWAnswers ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/answers/smartspace/blockchain/) sous **Ticket de demande de service** fait office de plateforme pour les questions et les réponses. Vous pouvez rechercher des réponses à des questions préalablement posées ou soumettre une nouvelle question. Assurez-vous d'inclure le mot clé **blockchain** dans votre question.
  Vous pouvez également soumettre un ticket pour l'équipe de support {{site.data.keyword.blockchainfull_notm}} à l'aide de l'option **Ouvrir un ticket de demande de service {{site.data.keyword.Bluemix_notm}}**.  Partagez les détails et les fragments de code de votre instance {{site.data.keyword.Bluemix_notm}} spécifique.
* [Modèles d'application![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/ibm-blockchain) sous **Modèles d'application blockchain** fournit une aide et des exemples de fragment de code pour vous aider dans le développement d'applications.
* [Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/) et [Communauté Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://jira.hyperledger.org/secure/Dashboard.jspa) sous **Hyperledger Fabric** fournissent des données plus détaillées sur la pile Hyperledger Fabric.
  Dialoguez avec un [expert Hyperledger![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://chat.hyperledger.org/channel/general) si vous avez des questions sur le code Hyperledger Fabric.

Si vous ne parvenez pas à déboguer votre problème ou à obtenir une réponse à votre question, soumettez un cas sur le portail IBM Cloud Service Portal. Pour plus d'informations, voir [Support](ibmblockchain_support.html).

Les Figures 12 et 13 affichent l'écran "Obtenir de l'aide" initial qui comporte les fonctions nouvelles et modifiées de chaque édition sous l'onglet "Notes sur l'édition" :

![Notes sur l'édition Helios](images/releasenotes_helios.png "Notes sur l'édition Helios")
*Figures 12. Notes sur l'édition pour Helios*

![Notes sur l'édition pour Fabric](images/releasenotes_Fabric.png "Notes sur l'édition pour Fabric")
*Figures 13. Notes sur l'édition pour Fabric*


## Préférences réseau
{: #network-preferences}

Cliquez dans l'angle supérieur droit et ouvrez le menu déroulant, puis les **Préférences réseau**. La fenêtre Préférences réseau s'affiche. Elle affiche les informations de base de votre réseau, comme le nom réseau, la version Fabric, l'emplacement réseau dans {{site.data.keyword.cloud_notm}}, ainsi que le type de base de données du registre.

Les réseaux du plan Enterprise seront prochainement mis à niveau vers Fabric v1.1 soon<!-- May 15th, 2018 will run on Hyperledger Fabric v1.1-->. Si vous créez des réseaux après la mise à niveau, vous pouvez également gérer les délai d'inactivité Web et le TLS mutuel pour votre réseau dans la fenêtre Préférences réseau. Ces paramètres peuvent être modifiés par l'initiateur du réseau uniquement.

<!--
Enterprise Plan networks that are created after May 15th, 2018 will run on Hyperledger Fabric v1.1. If you create networks after the upgrade, you can also manage web inactivity timeout, mutual TLS, and switch your ledger to CouchDB for your network in the Network preferences window. These settings can be changed by the network initiator only.
-->

### Délai d'inactivité Web
{: #web-inactivity-timeout}

**Remarque **: Seul l'**initiateur réseau** peut modifier le paramètre de délai d'attente d'inactivité Web. Il s'agit d'un paramètre de niveau réseau et il concerne tous les membres réseau.

Le délai d'attente d'inactivité Web est défini sur **Désactivé** par défaut. Si vous définissez le délai d'attente d'inactivité Web sur **Activé**, tout membre du réseau sera déconnecté automatiquement au bout de 10 minutes d'inactivité. Lorsque le délai d'attente d'inactivité Web atteint 10 minutes, la fonction Délai d'inactivité Web met fin aux sessions Web inactives afin de garantir la sécurité du compte des membres du réseau. Si vous cliquez sur un lien ou si vous actualisez le Moniteur réseau, le délai d'inactivité Web est réinitialisé. Avant d'atteindre les 10 minutes, il est possible d'arrêter la session Web en fermant la fenêtre ou l'onglet du navigateur. 

### TLS mutuel
{: #mutual-tls}

TLS mutuel sécurise la communication entre votre application et votre réseau, et garantit uniquement que vous pouvez communiquer avec votre réseau.

**Remarque **: Seul un **initiateur réseau** peut activer ou désactiver le TLS mutuel. Il s'agit d'un paramètre de niveau réseau et il concerne tous les membres réseau.

Le bouton TLS mutuel est défini sur **Désactivé** par défaut. Si vous activez TLS mutuel, vous devez mettre à jour vos applications afin de prendre en charge cette fonction. Sinon, vos applications ne pourront pas communiquer avec le réseau.

Pour un réseau du plan Enterprise Fabric 1.1, chaque organisation a sa propre autorité de certification TLS mutuel. Les informations nécessaires pour la connexion  à l'autorité de certification TLS mutuel sont disponibles dans le [Profil de connexion](##enterprise-connection-profile) accessible depuis votre écran **Présentation** dans le Moniteur réseau en cliquant sur le bouton **Profil de connexion**. Le profil de connexion contient les informations nécessaires à la connexion à l'autorité de certification et à l'obtention des certificats dont vous avez besoin pour la connexion à votre réseau. 

Dans le profil de connexion, localisez la section `certificateAuthorities` où vous trouverez les attributs suivants qui sont nécessaires à l'inscription et à l'obtention des certificats pour communiquer avec votre réseau à l'aide de TLS mutuel.

- `url` : URL pour la connexion à l'autorité de certification qui peut fournir les certificats TLS mutuel
- `enrollId` : ID d'inscription à utiliser pour l'obtention d'un certificat
- `enrollSecret` : valeur confidentielle d'inscription à utiliser pour l'obtention d'un certificat
- `x-tlsCAName` : nom d'autorité de certification à utiliser pour l'obtention d'un certificat qui permettra à l'application de communiquer avec TLS mutuel.

Pour plus d'informations sur la mise à jour de vos applications pour la prise en charge de TLS mutuel, voir [How to configure mutual TLS![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html)

<!--
### CouchDB ledger type
{: #couchdb}
**Note**: Only the **network initiator** can switch the ledger database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.
Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your ledger database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.
To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.
If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up index, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/couchdb_as_state_database.html). For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](howto/install_instantiate_chaincode.html#updating-a-chaincode).
-->

La **Figure 14** affiche la fenêtre "Préférences réseau" :

<!-- ![Network preferences](images/network_preferences_ep.gif "Network preferences") -->
![Préférences réseau](images/network_preferences_ep_tmp.png "Préférences réseau")  
*Figure 14. Préférences réseau*

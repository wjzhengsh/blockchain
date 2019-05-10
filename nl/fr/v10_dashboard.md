---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Utilisation du Moniteur réseau
{: #ibp-dashboard}

{{site.data.keyword.blockchainfull}} Platform comporte un Moniteur réseau qui fournit une vue d'ensemble de votre environnement de blockchain, notamment des composants réseau, des membres, des canaux rejoints, des données de performance et du code blockchain déployé. Le Moniteur réseau constitue également un point d'entrée pour l'exécution des API Swagger, le développement d'un réseau avec {{site.data.keyword.blockchainfull_notm}} Platform, et le test de modèles d'application.
{:shortdesc}

Utilisez ce tutoriel afin d'apprendre à utiliser votre Moniteur réseau pour exploiter un réseau de plan Enterprise ou Starter. Bien que la plupart des captures d'écran de ce tutoriel concernent le plan Enterprise, les instructions sont valables également pour le plan Starter. Lorsque certaines fonctions sont uniquement accessibles pour un plan, la section concernée est libellée **Pour les réseaux de plan Starter** ou **Pour les réseaux de plan Enterprise**.

## Panneau de navigation de gauche
{: #ibp-dashboard-left-navigation}

Le Moniteur réseau présente les écrans suivants dans trois sections. Vous pouvez accéder à chaque écran dans le navigateur de gauche du Moniteur réseau.
- La section **Mon réseau** contient les écrans "[Présentation](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-overview)", "[Membres](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members)", "[Canaux](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-channels)", "[Notifications](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-notifications)", "[Autorité de certification](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-ca)" et "[API](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-apis)".
- La section **Mon code** contient les écrans "[Ecriture de code](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-write_code)", "[Installer le code](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-chaincode)" et "[Essayer les modèles](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-samples)".
- L'écran "[Obtenir de l'aide](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-support)" affiche des informations de support ainsi que les notes sur l'édition pour Helios et Hyperledger Fabric (codebase sur lequel repose {{site.data.keyword.blockchainfull_notm}} Platform).

Le nom de votre réseau de blockchain figure en haut du panneau de navigation de gauche. Vous pouvez [changer le nom de votre réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-name) dans le Moniteur réseau.

Vous pouvez [vérifier et configurer les préférences réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) dans le menu déroulant dans l'angle supérieur droit du Moniteur réseau.

Ce tutoriel décrit chacun des écrans et fonctions ci-dessus.

## Présentation
{: #ibp-dashboard-overview}

L'écran "Présentation" affiche des informations d'état en temps réel concernant vos ressources blockchain, notamment le service de tri, l'autorité de certification et les noeuds homologue. Chaque ressource s'affiche sous quatre en-têtes distincts : **Type**, **Nom**, **Statut** et **Actions**. Pendant la création de votre réseau de blockchain, trois noeuds de service de tri et deux noeuds d'autorité de certification sont crées automatiquement. Les autorités de certification sont spécifiques à un membre, alors que les services de tri sont des noeuds finaux communs et partagés au sein du réseau.

La **Figure 1** illustre l'écran "Présentation" :

![Présentation du réseau](images/myresources.png "Présentation du réseau")
*Figure 1. Présentation du réseau*

### Actions de noeud
{: #ibp-dashboard-node-actions}

L'en-tête **Actions** du tableau comporte des boutons qui permettent de démarrer ou d'arrêter vos ressources. Vous pouvez également démarrer ou arrêter un groupe de noeuds en sélectionnant plusieurs noeuds et en cliquant sur le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés**. Le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés** apparaît au-dessus du tableau lorsque vous sélectionnez un ou plusieurs noeuds.

Les actions Arrêter et Démarrer ne sont pas disponibles pour un noeud de service de tri. En général, il n'est pas nécessaire d'arrêter et de démarrer des noeuds Homologues ou CA sur un réseau. Les opérations Arrêter et Démarrer sont fournies au cas où vous devez redémarrer un homologue, par exemple pour l'affichage dans un état nettoyé.

Vous pouvez également consulter les journaux de composant en cliquant sur **Afficher les journaux** dans la liste déroulante sous l'en-tête **Actions**. Ces journaux présentent les appels de procédure entre les différentes ressources réseau et ils sont utiles pour le débogage et le traitement des incidents. Pour plus d'informations sur l'utilisation de vos journaux réseau, voir [Surveillance d'un réseau de blockchain](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network)

Pour comprendre les effets de démarrage et d'arrêt d'un homologue, vous pouvez par exemple les tester en arrêtant un homologue et en le ciblant avec une transaction ; vous verrez alors des erreurs de connectivité dans les journaux. Lorsque vous redémarrez l'homologue et relancez la transaction, vous verrez qu'une connexion est réussie. Vous
pouvez aussi arrêter un homologue pendant une longue période alors que vos canaux continuent à effectuer des transactions. l'homologue est redémarré, vous remarquez une synchronisation du registre dès qu'il reçoit les blocs qui ont été validés alors qu'il était arrêté. Une fois que le registre est complètement synchronisé, vous pouvez procéder à des appels et des requêtes normaux.

### Configuration de l'homologue distant
{: #ibp-dashboard-peer-connection-information}

Si vous déployez un homologue en dehors de {{site.data.keyword.cloud_notm}}, vous devez indiquer les informations de noeud final d'API de votre réseau pendant la configuration. Cliquez sur le bouton **Configuration de l'homologue distant** pour extraire les informations de noeud final d'API du réseau afin de configurer votre homologue. La fenêtre en incrustation fournit les informations de noeud final d'API : ID réseau, MSP de l'organisation, Nom de l'autorité de certification, URL de l'autorité de certification et certificat TLS de l'autorité de certification. Vous pouvez cliquer sur l'icône copier à la fin de chaque zone pour copier la valeur de la zone, ou cliquer sur le bouton **Télécharger** pour sauvegarder les valeurs de toutes les zones dans un fichier JSON. Pour plus d'informations, voir [A propos de {{site.data.keyword.blockchainfull_notm}} Platform pour Amazon Web Services](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) et [A propose de {{site.data.keyword.blockchainfull_notm}} Platform pour {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

### Profil de connexion
{: #ibp-dashboard-connection-profile}

Vous pouvez consulter le ficher JSON pour les informations réseau de faible niveau de chaque ressource en cliquant sur le bouton **Profil de connexion**. Le profil de connexion contient toutes les informations de configuration dont vous avez besoin pour une application. Cependant, ce fichier contient uniquement les adresses de vos composants spécifiques et du service de tri ; si vous devez cibler des homologues supplémentaires, vous devez obtenir leurs noeuds finaux. L'en-tête "url" affiche le noeud final d'API pour chaque composant. Ces noeuds finaux sont requis pour cibler les composants réseau spécifiques d'une application côté client, et leurs définitions résident généralement dans un fichier de configuration modélisé JSON qui est fourni avec l'application. Si vous personnalisez une application qui nécessite une validation par des homologues qui ne font pas partie de votre organisation, vous devrez obtenir ces adresses IP auprès des opérateurs concernés dans le cadre d'une opération externe. Les clients doivent pouvoir se connecter aux
homologues dont ils attendent une réponse.

### Ajout d'homologues
{: #ibp-dashboard-peers}

Les membres du réseau déploient des [homologues](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer) pour stocker leurs copies de registre et exécuter du code blockchain pour interroger ou mettre à jour le registre. Si la règle de validation définit un homologue en tant qu'homologue d'adhésion, l'homologue renvoie également les résultats de la validation aux applications.

Cliquez sur le bouton **Ajouter des homologues** dans l'angle supérieur droit pour ajouter des noeuds homologue à votre réseau. Dans la fenêtre contextuelle "Ajouter des homologues", sélectionnez le nombre et la taille des noeuds homologue que vous voulez ajouter. Vous pouvez ajouter d'autres homologues pour vos organisations en fonction de vos besoins. Vous pouvez vous trouver dans différents scénarios lorsque vous avez besoin d'homologues supplémentaires. Par exemple, vous pouvez souhaiter que plusieurs homologues rejoignent le même canal à des fins de redondance. Chaque homologue traite les transactions du canal et leurs copies respectives du registre. Dans le cas où l'un des homologues est défaillant, les autres peuvent poursuivre le traitement des transactions et des demandes d'application. Vous pouvez équilibrer de manière symétrique toutes les demandes d'applications entre homologues, ou vous pouvez cibler différents homologues pour différentes fonctions. Par exemple, vous pouvez utiliser un homologue pour interroger le registre et utiliser un autre homologue pour traiter les adhésions pour les mises à jour du registre.

Le **plan Starter** crée un homologue pour chacune des deux organisations formées lorsque le réseau est démarré par défaut.

### Stockage (pour les réseaux de plan Starter)
{: #ibp-dashboard-storage}

La **Figure 2** illustre l'onglet "Stockage" qui affiche la consommation du stockage de votre réseau.

![Onglet Stockage dans l'écran Présentation](images/monitor_storage_starter.png "Stockage")
*Figure 2. Stockage*

Le diagramme "Ressources" affiche l'espace de stockage utilisé par les homologues et les autorités de certification. Toutes les organisations que vous créez ou invitez à rejoindre votre réseau sont incluses dans ce pool. Les organisations qui consomment des ressources sont répertoriés sur l'écran dans seconde puce.

Le service de tri consomme le stockage d'un pool de ressources distinct. Votre service de tri peut consommer plus de ressources lorsque des membres du réseau créent de nouveaux canaux et génèrent de nouveaux blocs de transactions.

## Membres
{: #ibp-dashboard-members}

L'écran "Membres" comporte deux onglets qui affichent des informations relatives aux membres du réseau (onglet "Membres") et des informations sur les certificats (onglet "Certificats").

### Ajout de membres aux réseaux de plan Enterprise
{: #ibp-dashboard-members-tab}

La **Figure 3** illustre l'écran "Membres" initial qui affiche les membres de votre réseau sous l'onglet "Membres" :

![Onglet Membres de l'écran Membres](images/monitor_members.png "Membres du réseau")
*Figure 3. Membres du réseau*

Vous pouvez inviter d'autres "membres" dans l'onglet "Membres" afin d'ajouter ceux qui sont initialement invités lorsque vous créez le réseau. Pour inviter un membre à rejoindre votre réseau, entrez le nom de l'institution et l'adresse électronique de l'opérateur et cliquez sur **Ajouter un membre**. Un réseau peut comporter au total 15 membres (initiateur du réseau compris). Pour retirer un membre de votre réseau, cliquez sur le symbole "retirer" en fin de ligne du membre.

### Ajout de membres aux réseaux de plan Starter

La **Figure 4** illustre l'écran "Ajouter un membre".

![Ajouter un membre](images/invite_member_starter.png "Ajouter un membre")
*Figure 4. Ajouter un membre*

Lorsque vous cliquez sur **Ajouter un membre**, deux options vous sont proposées :
- **Inviter un membre**. Vous pouvez inviter d'autres organisations à devenir membres du réseau. Les organisation invitées peuvent rejoindre votre réseau et collaborer avec vous.
- **Créer un membre**. Vous pouvez également créer un membre en utilisant votre adresse e-mail. Vous avez sur ce dernier le même contrôle que sur les deux organisations que vous recevez avec le plan Starter par défaut.


### Certificats
{: #ibp-dashboard-certificates}

La **Figure 5** présente l'écran "Membres" initial qui affiche les certificats de membre sous l'onglet "Certificats" :

![Onglet Certificats de l'écran Membres](images/monitor_certificates.png "Certificats")
*Figure 5. Certificats*

Les opérateurs peuvent gérer les certificats des membres d'une même institution sous l'onglet "Certificats". Cliquez sur **Ajouter le certificat** pour afficher l'écran "Ajouter le certificat". Donnez un nom à votre certificat, collez vos certificats côté client au format PEM dans la zone "Clé", puis cliquez sur **Soumettre**. Vous devez redémarrer vos homologues pour que les certificats côté client puissent être pris en compte.

Pour plus d'informations sur la génération de votre clé de certificat, voir [Inscription de votre application](/docs/services/blockchain/v10_application.html#dev-app-enroll).

## Canaux
{: #ibp-dashboard-channels}

Les canaux, qui se composent d'un sous-ensemble de membres réseau qui souhaitent effectuer des transactions en privé, fournissent l'isolement de données et la confidentialité en autorisant les membres d'un canal à établir des règles spécifiques et un registre distinct, auxquels seuls les membres du canal peuvent accéder. Chaque réseau doit comporter au moins un canal pour que les transactions puissent s'effectuer. Chaque canal a un registre unique et les utilisateurs doivent être correctement authentifiés pour exécuter des opérations de lecture/écriture sur ce registre. Si vous n'êtes pas sur un canal, vous ne pouvez voir aucune donnée.

La **Figure 6** illustre l'écran de tableau de bord initial qui affiche une présentation de tous les canaux de votre réseau.

![Canaux](images/channels.png "Canaux")
*Figure 6. Canaux*

La création d'un canal entraîne la génération d'un registre spécifique à un canal. Pour plus d'informations, voir [Création d'un canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel).

Vous pouvez aussi sélectionner un canal existant afin d'afficher des détails plus précis sur ce canal, l'appartenance et le code blockchain actif. Pour plus d'informations, voir [Surveillance d'un réseau](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network).

Si vous avez envoyé par téléchargement un nouveau certificat vers la plateforme à l'aide de l'[onglet "Certificats"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-certificates) de l'écran "Membres", vous pouvez utiliser ce panneau pour ajouter le certificat à un canal. Cliquez sur **Synchroniser le certificat** dans la liste déroulante sous l'en-tête **Actions** en regard du canal concerné. Cela vous permet d'utiliser le canal, avec la capacité d'instancier un code blockchain sur le canal. Pour plus d'informations, voir [Envoi par téléchargement de certificats à {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs) dans le tutoriel [Gestion des certificats](/docs/services/blockchain/certificates.html#managing-certificates).

## Notifications
{: #ibp-dashboard-notifications}

Lorsque vous créez un canal ou êtes invité à rejoindre un nouveau canal, une notification s'affiche dans le moniteur réseau. Vous pouvez afficher et répondre à ces demandes dans l'écran "Notifications".

La **Figure 7** illustre l'écran "Notifications" :

![Notifications](images/notifications.png "Notifications")
*Figure 7. Notifications*

Les demandes sont regroupées dans les sous-onglets "Toutes", "En attente" et "Terminée". Les nombres figurant à la suite de l'en-tête du sous-onglet indiquent le nombre de demandes dans chaque sous-onglet.
   * Vous pouvez trouver toutes vos demandes sous le sous-onglet "Toutes".
   * Les demandes que vous n'avez pas acceptées ou que vous avez refusées, ou que vous n'avez pas encore consultées, figurent sous le sous-onglet "En attente". Cliquez sur le bouton **Inspecter la demande** pour consulter la demande, qui comporte la règle de canal et les membres, ainsi que le statut de vote. Si vous être un opérateur de canal, vous pouvez soit **Accepter** soit **Décliner** la demande, ou la traiter à un autre moment en cliquant sur **Plus tard**. Si la demande est acceptée par suffisamment d'opérateurs de canal, vous pouvez cliquer sur **Soumettre une demande** pour activer la mise à jour de canal.
   * Une demande soumise apparaît alors dans le sous-onglet "Terminée". Vous pouvez cliquez sur **Inspecter la demande** pour afficher ses détails.

Si vous avez une longue liste de demandes, vous pouvez rechercher dans la zone de recherche située dans la partie supérieure de l'écran.

Les demandes en attente peuvent être supprimées en sélectionnant la case en regard et en cliquant sur **Supprimer la demande**. Notez qu'une demande terminée ne peut pas être supprimée.

## Autorité de certification
{: #ibp-dashboard-ca}

Le tableau à l'écran "Autorité de certification" panneau affiche toutes les identités qui ont été enregistrés auprès de votre autorité de certification, y compris l'admin, les homologues et les applications client de votre organisation. Vous pouvez également utiliser cet écran pour enregistrer une nouvelle identité.

La **Figure 8** illustre l'écran "Autorité de certification" :

![Autorité de certification](images/CA_screen.png "Autorité de certification")
*Figure 8. Autorité de certification*

Cliquez sur le bouton **Génération de certificat** en regard de votre identité admin pour obtenir un nouveau certificat public et une clé privée auprès de votre autorité de certification. La zone **Certificat** contient le certificat public, également appelé signCert ou certificat d'inscription, juste au-dessus de la **clé privée**. Vous pouvez cliquer sur l'icône de copie à la fin de chaque zone pour copier la valeur. Ce panneau peut être utilisé comme alternative à la génération d'une paire de clés publique et privée pour une application client qui utilise le logiciel SDK Fabric. Pour en savoir plus, voir le [tutoriel relatif au développement d'applications](/docs/services/blockchain/v10_application.html#dev-app). **Notez** qu'{{site.data.keyword.blockchainfull_notm}} Platform ne stocke pas ces certificats. Vous devez les enregistrer et les stocker en lieu sûr.

Cliquez sur le bouton **Ajouter un utilisateur** pour enregistrer une nouvelle identité à votre organisation. Dans la fenêtre en incrustation **Ajouter un utilisateur**, renseignez les zones suivantes, puis cliquez sur **Soumettre**.
  - **ID d'inscription :** Il s'agit du nom de votre nouvelle identité, parfois appelée `ID inscription`. **Sauvegardez cette valeur** à des fins de configuration d'un homologue distant ou d'inscription d'une nouvelle application.
  - **Secret d'inscription :** Il s'agit du mot de passe de votre identité, parfois appelé `Secret d'inscription`. **Sauvegardez cette valeur** à des fins de configuration d'un homologue distant ou d'inscription d'une nouvelle application.
  - **Type :** Sélectionnez le type d'identité que vous voulez enregistrer, qu'il s'agisse d'un homologue ou d'une application client.
  - **Affiliation :** Il doit s'agir de l'affiliation au sein de votre organisation, par exemple `org1`, à laquelle l'identité va appartenir.
  - **Nombre d'inscriptions maximum :** Vous pouvez utiliser cette zone pour limiter le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. Si vous laissez cette zone vide, la valeur par défaut est un nombre illimité d'inscriptions.

Vous pouvez en savoir plus sur l'autorité de certification en consultant le tutoriel relatif à la [Gestion des certificats sur {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

## API
{: #ibp-dashboard-apis}

{{site.data.keyword.blockchainfull_notm}} Platform expose dans Swagger un certain nombre d'API REST que vous pouvez utiliser pour gérer les noeuds, les homologues et les membres de votre réseau. Vos applications peuvent recourir à ces API pour gérer d'importantes ressources réseau sans utiliser le moniteur réseau.

La **Figure 9** illustre l'écran "API" :

![API](images/API_screen.png "API")
*Figure 9. API*

Cliquez sur le lien **Interface utilisateur swagger** pour ouvrir l'identificateur Swagger. Notez que vous devez autoriser l'interface utilisateur swagger à l'aide de vos données d'identification réseau (qui se trouvent sur la page d'API) avant d'exécuter les API. Pour plus de détails, voir [Interaction avec le réseau à l'aide d'API Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger).

## Développement de Code
{: #ibp-dashboard-write-code}

{{site.data.keyword.IBM_notm}} n'assure pas de support pour les réseaux utilisant Hyperledger Composer en production, y compris l'interface CLI Composer, les API JavaScript, le serveur REST et l'aire de jeu Web.{:note}

Le plan Starter et le plan Enterprise fournissent un environnement de développement avec des outils et des technologies standard du secteur. Dès que vous avez développé un réseau, vous pouvez le redéployer dans votre réseau.

La **Figure 10** illustre l'écran "Développement de code" :

![Développement de code](images/write_code.png "Développement de code")
*Figure 10. Développement de code*

Pour plus d'informations sur le développement et le déploiement de vos réseaux d'entreprise, voir [Déploiement de réseaux d'entreprise dans le plan Starter et le plan Enterprise](/docs/services/blockchain/develop_starter_enterprise.html#deploying-a-business-network).

## Installer le code
{: #ibp-dashboard-chaincode}

Le code blockchain, également appelé "contrat intelligent", est l'élément logiciel qui contient un ensemble de fonctions permettant d'interroger et de mettre à jour le registre. Ils sont installés sur des homologues et instanciés sur un canal.

La **Figure 11** illustre l'écran "Installer le code" :

![Installer le code](images/chaincode_install_overview.png "Installer le code")
*Figure 11. Installer le code*

Un code blockchain est tout d'abord installé sur le système de fichiers d'un homologue, puis il est instancié sur un canal. Pour plus d'informations, voir [Installation, instanciation et mise à jour d'un code blockchain](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).

## Essayer les modèles
{: #ibp-dashboard-samples}

Des modèles d'application vous permettent d'avoir une meilleure compréhension d'un réseau de blockchain et du développement d'application. Suivez les liens **Afficher sur GitHub** pour découvrir comment utiliser les modèles et les déployer sur {{site.data.keyword.blockchainfull_notm}} Platform. Pour plus d'informations sur le développement et le déploiement de vos modèles, voir [Déploiement de modèles d'application](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications).

La **Figure 12** illustre l'écran "Essayer les modèles" :

![Essayer les modèles](images/sample_overview_ep.png "Essayer les modèles")
*Figure 12. Modèles*

## Obtenir de l'aide
{: #ibp-dashboard-support}

L'écran "Obtenir de l'aide" comporte un onglet "Support" qui fournit une liste des ressources pour les développeurs et un onglet "Notes sur l'édition" qui décrit les nouvelles fonctions de  {{site.data.keyword.blockchainfull_notm}} Platform.

La **Figure 13** affiche des informations sous l'onglet "Support" initial :

![Support](images/support.png "Support")
*Figure 13. Support Blockchain*

### Ressources de blockchain et forums de support
{: #ibp-dashboard-support-forums}

Utilisez les ressources de l'onglet "Support" pour résoudre les problèmes et obtenir de l'aide auprès de {{site.data.keyword.IBM_notm}} et de la communauté  Fabric. Pour plus d'informations sur les liens de l'onglet "Support", voir [Ressources et forums de support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources) dans [Support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

[IBM dWAnswers ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/answers/smartspace/blockchain/index.html) est un forum de communauté pour les utilisateurs de {{site.data.keyword.blockchainfull_notm}} Platform et d'Hyperledger Fabric et il est surveillé par des experts IBM. Vous pouvez rechercher des réponses à des questions préalablement posées ou soumettre une nouvelle question. Si vous ne parvenez pas à déboguer votre problème ou à obtenir une réponse à votre question, soumettez un cas sur le portail de service {{site.data.keyword.cloud_notm}}. Pour plus d'informations, voir [Soumission de cas de support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases).


### Notes sur l'édition de Fabric
{: #ibp-dashboard-release-notes}

L'onglet "Notes sur l'édition" affiche les fonctions les plus récentes de votre réseau. Le bouton "Interface utilisateur du Moniteur réseau" permet d'affiche la liste des nouvelles fonctions et des correctifs pour l 'expérience utilisateur {{site.data.keyword.blockchainfull_notm}} Platform. Le bouton "Hyperledger Fabric" vous redirige vers les notes sur l'édition pour la version de votre réseau d'Hyperledger Fabric et de l'autorité de certification Fabric.

La **Figure 14** illustre les notes sur l'édition pour l'interface utilisateur du Moniteur réseau.

![Notes sur l'édition helios](images/releasenotes_helios.png "Notes sur l'édition de l'interface utilisateur du Moniteur réseau")
*Figures 14. Notes sur l'édition pour Helios*

La **Figure 15** illustrez les notes sur l'édition pour votre version de réseau  d'Hyperledger Fabric et de l'autorité de certification Fabric.

![Notes sur l'édition Fabric](images/releasenotes_Fabric.png "Notes sur l'édition de Fabric")
*Figures 15. Notes sur l'édition pour Fabric*

## Préférences réseau
{: #ibp-dashboard-network-preferences}

Cliquez dans l'angle supérieur droit et ouvrez le menu déroulant, puis les **Préférences réseau**. La fenêtre Préférences réseau s'affiche. Elle affiche les informations de base de votre réseau, comme le nom réseau, la version Fabric, l'emplacement réseau dans {{site.data.keyword.cloud_notm}}, ainsi que le type de base de données d'état.

Les **réseaux de plan Enterprise** créés après le 15 mai 2018 opéreront avec Hyperledger Fabric version 1.1.1. Si vous créez des réseaux après la mise à niveau, vous pouvez également gérer les délai d'inactivité Web et le TLS mutuel pour votre réseau dans la fenêtre Préférences réseau. Ces paramètres peuvent être modifiés par l'initiateur du réseau uniquement.

### Délai d'inactivité Web
{: #ibp-dashboard-web-inactivity-timeout}

**Remarque **: Seul l'**initiateur réseau** peut modifier le paramètre de délai d'attente d'inactivité Web. Il s'agit d'un paramètre de niveau réseau et il concerne tous les membres réseau.

Le délai d'attente d'inactivité Web est défini sur **Désactivé** par défaut. Si vous définissez le délai d'attente d'inactivité Web sur **Activé**, tout membre du réseau sera déconnecté automatiquement au bout de 10 minutes d'inactivité. Lorsque le délai d'attente d'inactivité Web atteint 10 minutes, la fonction Délai d'inactivité Web met fin aux sessions Web inactives afin de garantir la sécurité du compte des membres du réseau. Si vous cliquez sur un lien ou si vous actualisez le Moniteur réseau, le délai d'inactivité Web est réinitialisé. Avant d'atteindre les 10 minutes, il est possible d'arrêter la session Web en fermant la fenêtre ou l'onglet du navigateur.

La **Figure 16** illustre la fenêtre "Préférences réseau" :

![Préférences réseau](images/network_preferences.gif "Préférences réseau")
*Figure 16. Préférences réseau*

### TLS mutuel (pour les réseaux de plan Enterprise)
{: #ibp-dashboard-mutual-tls}

Les **réseaux de plan Enterprise** vous offrent la possibilité d'activer la fonction TLS mutuel afin de sécuriser la communication entre votre application et vos composants de blockchain.

**Remarque **: Seul un **initiateur réseau** peut activer ou désactiver le TLS mutuel. Il s'agit d'un paramètre de niveau réseau et il concerne tous les membres réseau.

Le bouton TLS mutuel est défini sur **Désactivé** par défaut. Si vous activez TLS mutuel, vous devez mettre à jour vos applications afin de prendre en charge cette fonction. Sinon, vos applications ne pourront pas communiquer avec le réseau.

Pour un réseau du plan Enterprise Fabric 1.1, chaque organisation a sa propre autorité de certification TLS mutuel. Les informations nécessaires pour la connexion  à l'autorité de certification TLS mutuel sont disponibles dans le [Profil de connexion](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-connection-profile) accessible depuis votre écran **Présentation** dans le Moniteur réseau en cliquant sur le bouton **Profil de connexion**. Le profil de connexion contient les informations nécessaires à la connexion à l'autorité de certification et à l'obtention des certificats dont vous avez besoin pour la connexion à votre réseau.

Dans le profil de connexion, localisez la section `certificateAuthorities` où vous trouverez les attributs suivants qui sont nécessaires à l'inscription et à l'obtention des certificats pour communiquer avec votre réseau à l'aide de TLS mutuel.

- `url` : URL pour la connexion à l'autorité de certification qui peut fournir les certificats TLS mutuel
- `enrollId` : ID d'inscription à utiliser pour l'obtention d'un certificat
- `enrollSecret` : valeur confidentielle d'inscription à utiliser pour l'obtention d'un certificat
- `x-tlsCAName` : nom d'autorité de certification à utiliser pour l'obtention d'un certificat qui permettra à l'application de communiquer avec TLS mutuel.

Pour plus d'informations sur la mise à jour de vos applications pour la prise en charge de TLS mutuel, voir [How to configure mutual TLS![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html)

<!--

### CouchDB state database
{: #couchdb}

**Note**: Only the **network initiator** can switch the state database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.

Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your state database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.

To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.

If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up indexes, see [Best practices when using CouchDB](/docs/services/blockchain/best_practices.html#best-practices-app-couchdb-indices) in the Developing applications tutorial. For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc).

-->

La **Figure 17** affiche la fenêtre "Préférences réseau" :

![Préférences réseau](images/network_preferences_ep_tmp.png "Préférences réseau")
*Figure 17. Préférences réseau*

## Mettre à jour le nom de réseau
{: #ibp-dashboard-network-name}

Lorsque vous créez une instance de plan Starter ou de plan Enterprise, {{site.data.keyword.blockchainfull_notm}} Platform affecte un nom à votre réseau. Toutefois, vous pouvez à tout moment mettre à jour ce nom réseau dans votre Moniteur réseau.

Dans la partie supérieure gauche du Moniteur réseau, cliquez sur l'icône de réseau et la zone devient éditable. Entrez le nouveau nom que vous voulez utiliser et appuyez sur **Entrée**. Le nom de votre réseau est mis à jour en quelques secondes.

La **Figure 18** illustre les étapes de mise à jour du nom de réseau du plan Starter : nom affecté remplacé par "Starter Plan Network".

![Mise à jour du nom réseau](images/update_network_name_ep.gif "Mise à jour du nom réseau")
*Figure 18. Mise à jour du nom réseau*


## Basculement entre réseaux (pour les réseau de plan Starter)
{: #ibp-dashboard-switch-network}

Si vous créez plusieurs réseaux avec le plan Starter, vous pouvez basculer entre vos réseaux dans le Moniteur de réseau.

Dans la partie supérieure gauche du Moniteur réseau, cliquez sur l'icône en forme de flèche en regard du nom de réseau. Sélectionnez et cliquez sur le nom de réseau à partir duquel vous voulez basculer dans la liste déroulante. Votre navigateur s'actualise et ouvre le Moniteur réseau du réseau vers lequel vous basculez.

La **Figure 19** illustre les étapes de basculement vers un autre réseau du plan Starter.

![Basculement entre les réseaux](images/switch_network.gif "Basculement entre les réseaux")
*Figure 19. Basculement entre les réseaux*


## Réinitialisation de réseau (pour les réseaux de plan Starter)
{: #ibp-dashboard-reset-network}

Les réseaux de plan Starter vous offrent la possibilité de modifier votre configuration réseau sans supprimer et recréer un réseau. Votre réseau est réinitialisé sur sa configuration réseau initiale, qui comprend deux organisations, un homologue par organisation, ainsi qu'un canal par défaut. Ceci est utile, par exemple, lorsque vous exécutez des tests sur le réseau de blockchain et vous devez reprendre à partir d'un réseau propre.

**Attention **: Une fois que vous avez réinitialisé le réseau, les noeuds finaux d'API de vos homologues, le service de tri et l'autorité de certification sont modifiés. Vous devez ajuster les informations de noeud final d'API dans vos applications.

Cliquez dans l'angle supérieur droit et ouvrez le menu déroulant. Cliquez sur le bouton **Réinitialiser le réseau**. Si vous êtes prêt à réinitialiser votre réseau, cliquez sur **OK** pour continuer. Votre Moniteur réseau est actualisé pour refléter les nouveaux paramètres.

La **Figure 20** présente la fonction de "réinitialisation du réseau" :

![Réinitialiser le réseau](images/reset_network.png "Réinitialiser le réseau")
*Figure 20. Réinitialiser le réseau*

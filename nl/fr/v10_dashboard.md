---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Exploitation d'un réseau de plan Enterprise
{: #v10_dashboard}

La plateforme {{site.data.keyword.blockchainfull}} comporte un Moniteur réseau qui fournit une vue d'ensemble de votre environnement de blockchain, notamment des composants réseau, des membres, des canaux rejoints, des données de performance et des codes blockchain déployés. Le Moniteur réseau constitue également un point d'entrée pour l'exécution des API Swagger, le développement d'un réseau avec la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop, et le test de modèles d'application.
{:shortdesc}

Le Moniteur réseau présente les écrans suivants dans trois sections. Vous pouvez accéder à chaque écran dans le navigateur de gauche du Moniteur réseau.
- La section **Mon réseau** contient les écrans "[Présentation](#overview)", "[Membres](#members)", "[Canaux](#channels)", "[Notifications](#notifications)" et "[API](#apis)".
- La section **Mon code** contient les écrans "[Ecriture de code](#write_code)", "[Installer le code](#chaincode)" et "[Essayer les modèles](#samples)".
- L'écran "[Obtenir de l'aide](#support)" affiche des informations de support ainsi que les notes sur l'édition pour Helios et Hyperledger Fabric (codebase sur lequel repose la plateforme {{site.data.keyword.blockchainfull_notm}}).


## Présentation
{: #overview}

L'écran "Présentation" affiche des informations d'état en temps réel concernant vos ressources blockchain, notamment le programme de tri, l'autorité de certification et les noeuds homologue. Chaque ressource s'affiche sous quatre en-têtes distincts : **Type**, **Nom**, **Statut** et **Actions**. Pendant la création de votre réseau de blockchain, trois noeuds de programme de tri et deux noeuds d'autorité de certification sont crées automatiquement. Les autorités de certification sont spécifiques à un membre, alors que les noeuds de programme de tri sont des noeuds finaux communs et partagés au sein du réseau.

La **Figure 1** illustre l'écran "Présentation" :

![Présentation du réseau](images/myresources.png "Présentation du réseau")
*Figure 1. Présentation du réseau*

### Actions de noeud
  L'en-tête **Actions** du tableau comporte des boutons qui permettent de démarrer ou d'arrêter vos ressources. Vous pouvez également démarrer ou arrêter un groupe de noeuds en sélectionnant plusieurs noeuds et en cliquant sur le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés**. Le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés** apparaît au-dessus du tableau lorsque vous sélectionnez un ou plusieurs noeuds.

  Notez que les actions Arrêter et Démarrer ne sont pas disponibles pour un noeud de programme de tri. En général, il n'est pas nécessaire d'arrêter et de démarrer des noeuds Homologues ou CA sur un réseau. Les opérations Arrêter et Démarrer sont fournies au cas où vous devez redémarrer un homologue, par exemple pour l'affichage dans un état nettoyé. 

  Vous pouvez également consulter les journaux de composant en cliquant sur **Afficher les journaux** dans la liste déroulante sous l'en-tête **Actions**. Ces journaux présentent les appels de procédure entre les différentes ressources réseau et ils sont utiles pour le débogage et le traitement des incidents. Vous pouvez par exemple les tester en arrêtant un homologue et en le ciblant avec une transaction ; vous verrez alors des erreurs de connectivité. Lorsque vous redémarrez l'homologue et relancez la transaction, vous verrez qu'une connexion est réussie. Vous
pouvez aussi arrêter un homologue pendant une longue période alors que vos canaux continuent à effectuer des transactions. l'homologue est redémarré, vous remarquez une synchronisation du registre dès qu'il reçoit les blocs qui ont été validés alors qu'il était arrêté. Une fois que le registre est complètement synchronisé, vous pouvez procéder à des appels et des requêtes normaux.

### Profil de connexion
  Vous pouvez consulter le ficher JSON pour les informations réseau de faible niveau de chaque ressource en cliquant sur le bouton **Profil de connexion**. Le profil de connexion contient toutes les informations de configuration dont vous avez besoin pour une application. Cependant, ce fichier contient uniquement les adresses de vos composants spécifiques et du programme de tri ; si vous devez cibler des homologues supplémentaires, vous devez obtenir leurs noeuds finaux. L'en-tête "url" affiche le noeud final d'API pour chaque composant. Ces noeuds finaux sont requis pour cibler les composants réseau spécifiques d'une application côté client, et leurs définitions résident généralement dans un fichier de configuration modélisé JSON qui est fourni avec l'application. Si vous personnalisez une application qui nécessite l'adhésion d'homologues qui ne font pas partie de votre organisation, vous devrez obtenir ces adresses IP auprès des opérateurs concernés dans le cadre d'une opération externe. Les clients doivent pouvoir se connecter aux
homologues dont ils attendent une réponse.

### Ajout d'homologues
{: #peers}
  Cliquez sur le bouton **Ajouter des homologues** pour ajouter des noeuds homologue à votre réseau. Dans le plan Starter, deux homologues sont ajoutés automatiquement lors de la création du réseau. Dans le plan Enterprise, vous pouvez ajouter des noeuds homologue pour la première fois lorsque vous créez ou rejoignez un réseau ou ultérieurement dans le moniteur réseau. Vous pouvez vous trouver dans différents scénarios lorsque vous avez besoin d'homologues supplémentaires. Par exemple, vous pouvez souhaiter que plusieurs homologues rejoignent le même canal à des fins de redondance. Chaque homologue traite les transactions du canal et leurs copies respectives du registre. Dans le cas où l'un des homologues est défaillant, les autres peuvent poursuivre le traitement des transactions et des demandes d'application. Vous pouvez équilibrer de manière symétrique toutes les demandes d'applications entre homologues, ou vous pouvez cibler différents homologues pour différentes fonctions. Par exemple, vous pouvez utiliser un homologue pour interroger le registre et utiliser un autre homologue pour traiter les adhésions pour les mises à jour du registre.

  Dans la fenêtre contextuelle "Ajouter des homologues", sélectionnez le nombre de noeuds homologue que vous voulez ajouter.  <!--Currently only "small" peers are available for purchase, however there will eventually be "medium" and "large" to help accommodate larger workloads and higher transaction throughput.-->

## Membres
{: #members}
L'écran "Membres" comporte deux onglets qui affichent des informations relatives aux membres du réseau (onglet "Membres") et des informations sur les certificats (onglet "Certificats").

### Membres
{: #members_tab}
La **Figure 2** illustre l'écran "Membres" initial qui affiche les membres de votre réseau sous l'onglet "Membres" :

![Onglet Membres de l'écran Membres](images/monitor_members.png "Membres du réseau")
*Figure 2. Membres du réseau*

Outre les membres que vous invitez lors de la création du réseau, vous pouvez inviter d'autres membres sous l'onglet "Membres". Pour inviter un membre à rejoindre votre réseau, entrez le nom de l'institution et l'adresse électronique de l'opérateur et cliquez sur **Ajouter un membre**. Un réseau peut comporter au total 15 membres (initiateur du réseau compris). Pour retirer un membre de votre réseau, cliquez sur le symbole "retirer" en fin de ligne du membre.

### Certificats
La **Figure 3** présente l'écran "Membres" initial qui affiche les certificats de membre sous l'onglet "Certificats" :

![Onglet Certificats de l'écran Membres](images/monitor_certificates.png "Certificats")
*Figure 3. Certificats*

Les opérateurs peuvent gérer les certificats des membres d'une même institution sous l'onglet "Certificats". Cliquez sur **Ajouter le certificat** pour afficher l'écran "Ajouter le certificat". Donnez un nom à votre certificat, collez vos certificats côté client au format PEM dans la zone "Clé", puis cliquez sur **Soumettre**. Vous devez redémarrer vos homologues pour que les certificats côté client puissent être pris en compte.

Pour plus d'informations sur la génération de votre clé de certificat, voir [Génération de certificats côté client](v10_application.html#generating-the-client-side-certificates).

## Canaux
{: #channels}

Vous pouvez diviser votre réseau en canaux, où chaque canal représente un sous-ensemble de membres qui sont autorisés à consulter les données des codes blockchain instanciés sur ce canal. Chaque réseau doit comporter au moins un canal pour que les transactions puissent s'effectuer. Chaque canal a un registre unique et les utilisateurs doivent être correctement authentifiés pour exécuter des opérations de lecture/écriture sur ce registre. Si vous n'êtes pas sur un canal, vous ne pouvez voir aucune donnée.

La **Figure 4** illustre l'écran de tableau de bord initial qui affiche une présentation de tous les canaux de votre réseau.

![Canaux](images/channels.png "Canaux")
*Figure 4. Canaux*

La création d'un canal entraîne la génération d'un registre spécifique à un canal. Pour plus d'informations, voir [Création d'un canal](howto/create_channel.html).

Vous pouvez aussi sélectionner un canal existant afin d'afficher des détails plus précis sur ce canal, l'appartenance et les codes blockchain actifs. Pour plus d'informations, voir [Surveillance d'un réseau](howto/monitor_network.html).


## Notifications
{: #notifications}

Vous pouvez traiter les demandes en attente et consulter les demandes terminées dans l'écran "Notifications".

La **Figure 5** illustre l'écran "Notifications" :

![Notifications](images/notifications.png "Notifications")
*Figure 5. Notifications*

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

![API](images/API_screen.png "API")
*Figure 6. API*

Cliquez sur le lien **Interface utilisateur swagger** pour ouvrir l'identificateur Swagger. Notez que vous devez autoriser l'interface utilisateur swagger à l'aide de vos données d'identification réseau (qui se trouvent sur la page d'API) avant d'exécuter les API. Pour plus de détails, voir [Test des API avec Swagger](howto/swagger_apis.html).


## Ecriture de Code
{: #write-code}

Le plan Enterprise intègre la plateforme {{site.data.keyword.blockchainfull_notm}} : Develop et fournit un environnement de développement avec des outils et des technologies standard du secteur. Vous pouvez développer votre réseau dans l'environnement en ligne ou en local. Dès que vous avez développé un réseau, vous pouvez le redéployer dans votre réseau de plan Enterprise.

![Ecriture de code](images/write_code.png "Ecriture de code")
*Figure 7. Ecriture de code*

Pour plus d'informations sur le développement et le déploiement de votre code avec le plan Enterprise, voir [Déploiement d'un réseau d'entreprise dans un plan Starter](develop_enterprise.html).


## Installer le code
{: #chaincode}

Les codes blockchain, également appelés "contrats intelligents", sont les éléments logiciels qui contiennent un ensemble de fonctions permettant d'interroger et de mettre à jour le registre. Ils sont installés sur des homologues et instanciés sur un canal.

![Installer le code](images/chaincode_install_overview.png "Installer le code")
*Figure 8. Installer le code*

Un code blockchain est tout d'abord installé sur le système de fichiers d'un homologue, puis il est instancié sur un canal. Pour plus d'informations, voir [Installation et instanciation d'un code blockchain](howto/install_instantiate_chaincode.html).


## Essayer les modèles
{: #samples}

Des modèles d'application vous permettent d'avoir une meilleure compréhension d'un réseau de blockchain et du développement d'application. Suivez le lien jusqu'au référentiel Billes dans le Moniteur réseau pour plus d'informations sur l'installation du modèle d'application Billes. Pour plus d'informations sur le développement et le déploiement de vos propres exemples, voir [Développement d'applications](v10_application.html).

La **Figure 9** illustre l'écran "Exemples" :

![Essayer les modèles](images/sample_overview.png "Essayer les modèles")
*Figure 9. Exemples*


## Obtenir de l'aide
{: #support}

L'écran "Obtenir de l'aide" comporte deux onglets qui fournissent des informations de support sous l'onglet "Support" et une description des fonctions nouvelles et modifiées de chaque édition sous l'onglet "Notes sur l'édition".

La **Figure 10** illustre l'écran "Support" initial qui comporte des informations de support sous l'onglet "Support" :

![Support](images/support.png "Support")
*Figure 10. Support Blockchain*

Utilisez les liens et les ressources de cette page pour accéder à des forums de dépannage et de support.

* [Docs de service {{site.data.keyword.blockchainfull_notm}}](index.html), qui est le présent site de documentation, fournit une aide sur la mise en route de la plateforme {{site.data.keyword.blockchainfull}} sur {{site.data.keyword.Bluemix_notm}}. Vous pouvez accéder aux rubriques correspondantes depuis le navigateur de gauche ou rechercher un terme à l'aide de la fonction de recherche située en haut de l'écran.
* [IBM Developer Works ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/blockchain/) sous **Aide communautaire** comporte des ressources et des informations pour les développeurs.
* [IBM dWAnswers ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/answers/smartspace/blockchain/) sous **Ticket de demande de service** fait office de plateforme pour les questions et les réponses. Vous pouvez rechercher des réponses à des questions préalablement posées ou soumettre une nouvelle question. Assurez-vous d'inclure le mot clé **blockchain** dans votre question.
  Vous pouvez également soumettre un ticket pour l'équipe de support {{site.data.keyword.blockchainfull_notm}} à l'aide de l'option **Ouvrir un ticket de demande de service {{site.data.keyword.Bluemix_notm}}**. Partagez les détails et les fragments de code de votre instance {{site.data.keyword.Bluemix_notm}} spécifique.
* [Modèles d'application![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/ibm-blockchain) sous **Modèles d'application blockchain** fournit une aide et des exemples de fragment de code pour vous aider dans le développement d'applications.
* [Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/) et [Communauté Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://jira.hyperledger.org/secure/Dashboard.jspa) sous **Hyperledger Fabric** fournissent des données plus détaillées sur la pile Hyperledger Fabric.
  Dialoguez avec un [expert Hyperledger![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://chat.hyperledger.org/channel/general) si vous avez des questions sur le code Hyperledger Fabric.

Si vous ne parvenez pas à déboguer votre problème ou à obtenir une réponse à votre question, soumettez un cas sur le portail IBM Cloud Service Portal. Pour plus d'informations, voir [Support](ibmblockchain_support.html).

Les Figures 11 et 12 affichent l'écran "Obtenir de l'aide" initial qui comporte les fonctions nouvelles et modifiées de chaque édition sous l'onglet "Notes sur l'édition" :

![Notes sur l'édition Helios](images/releasenotes_helios.png "Notes sur l'édition Helios")  
*Figures 11. Notes sur l'édition pour Helios*

![Notes sur l'édition Fabric](images/releasenotes_Fabric.png "Notes sur l'édition Fabric")  
*Figures 12. Notes sur l'édition pour Fabric*

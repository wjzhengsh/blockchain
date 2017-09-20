---

copyright:
  years: 2017
lastupdated: "2017-08-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Gestion du réseau
{: #v10_dashboard}


Le moniteur réseau fournit une vue d'ensemble de votre environnement de blockchain, notamment des composants réseau, des membres, des canaux rejoints, des données de performance et des codes blockchain déployés.
{:shortdesc}

Le moniteur réseau comporte les écrans suivants :
* Dans l'écran "Présentation", vous pouvez voir les données d'identification de service réseau, les informations d'état des composants, et ajouter des homologues.
* Dans l'écran "Membres", vous pouvez gérer les membres du réseau et les certificats.
* Dans l'écran "Canaux", vous pouvez créer de nouveaux canaux et consulter les informations sur les canaux existants.
* Dans l'écran "Code blockchain", vous pouvez installer et instancier des codes blockchain sur vos homologues.
* Dans l'écran "Notifications", vous pouvez traiter les approbations en attente et consulter les approbations terminées.
* Dans l'écran "Support", vous pouvez trouver des liens vers des ressources de référence et découvrir les fonctions nouvelles et modifiées de chaque version.


## Présentation

L'écran "Présentation" affiche des informations d'état en temps réel concernant vos composants blockchain, notamment le programme de tri, l'autorité de certification et les noeuds homologue. Chaque composant s'affiche sous quatre en-têtes distincts : **Type**, **Nom**, **Statut** et **Actions**. Pendant la création de votre réseau de blockchain, trois noeuds de programme de tri et deux noeuds d'autorité de certification sont crées automatiquement.  Les autorités de certification sont spécifiques à un membre, alors que les noeuds de programme de tri sont des noeuds finaux communs et partagés au sein du réseau.

La **Figure 1** illustre l'écran "Présentation" :

![Présentation du réseau](images/myresources.png "Présentation du réseau")
*Figure 1. Présentation du réseau*

- Actions de noeud

  L'en-tête **Actions** du tableau comporte des boutons qui permettent de démarrer ou d'arrêter vos composants. Vous pouvez également démarrer ou arrêter un groupe de noeuds en sélectionnant plusieurs noeuds et en cliquant sur le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés**. Le bouton **Démarrer les éléments sélectionnés** ou **Arrêter les éléments sélectionnés** apparaît au-dessus du tableau lorsque vous sélectionnez un ou plusieurs noeuds.

  Vous pouvez également consulter les journaux de composant en cliquant sur **Afficher les journaux** dans la liste déroulante sous l'en-tête **Actions**. Ces journaux présentent les appels de procédure distante qui ont lieu entre les différents composants réseau et s'avèrent utiles pour le débogage et le traitement des incidents. Vous pouvez par exemple les tester en arrêtant un homologue et en le ciblant avec une transaction ; vous verrez alors des erreurs de connectivité gRPC. Lorsque vous redémarrez l'homologue et relancez la transaction, vous verrez qu'une connexion est réussie. Vous pouvez aussi arrêter un homologue pendant une longue période alors que vos canaux continuent à effectuer des transactions. Lorsque l'homologue est redémarré, vous remarquez une synchronisation du registre via le protocole gossip. Une fois que l'homologue a complètement synchronisé le registre, vous pouvez procéder à des appels et des requêtes normaux.  
- Données d'identification pour le service  
  Vous pouvez consulter le ficher JSON pour les informations réseau de faible niveau de chaque composant en cliquant sur le bouton **Données d'identification pour le service** dans l'angle supérieur droit de l'onglet "Ressources". Il s'agit de l'intégralité des infos de configuration dont vous aurez besoin pour une application. Notez, cependant, que ce fichier contient uniquement les adresses de vos composants spécifiques et des noeuds de tri partagés. Si vous devez cibler d'autres homologues, vous aurez besoin d'obtenir ces noeuds finaux. L'en-tête "url" affiche le noeud final d'API pour chaque composant. Ces noeuds finaux sont requis pour cibler les composants réseau spécifiques d'une application côté client, et leurs définitions résident généralement dans un fichier de configuration modélisé JSON qui est fourni avec l'application. Si vous personnalisez une application qui nécessite l'adhésion d'homologues qui ne font pas partie de votre organisation, vous devrez obtenir ces adresses IP auprès des opérateurs concernés dans le cadre d'une opération externe. Les clients doivent pouvoir se connecter aux
homologues dont ils attendent une réponse.  
- Ajout d'homologues  
  Cliquez sur le bouton **Ajouter des homologues** dans l'angle supérieur droit pour ajouter des noeuds homologue à votre réseau. Chaque membre peut ajouter jusqu'à six homologues dans un réseau. Vous pouvez ajouter des noeuds homologue pour la première fois lorsque vous créez ou rejoignez un réseau ou ultérieurement dans le moniteur réseau.   
  Dans la fenêtre contextuelle "Ajouter des homologues", sélectionnez le nombre et la taille des noeuds homologue que vous voulez ajouter. Actuellement, seuls de "petits" homologues sont disponibles à la vente, cependant des homologues de taille "moyenne" ou "grande" seront bientôt proposés pour l'hébergement de charges de travail plus importantes et de débits de transaction plus élevés. Des détails concernant le dimensionnement et les mesures de performance des homologues seront bientôt disponibles...
  
## Membres

L'écran "Membres" comporte deux onglets qui affichent des informations relatives aux membres du réseau (onglet "Membres") et des informations sur les certificats (onglet "Certificats").

La **Figure 2** illustre l'écran "Membres" initial qui affiche les membres de votre réseau sous l'onglet "Membres" :

![Onglet Membres de l'écran Membres](images/monitor_members.png "Membres du réseau")
*Figure 2. Membres du réseau*

Outre les membres que vous invitez lors de la création du réseau, vous pouvez inviter d'autres membres sous l'onglet "Membres". Pour inviter un membre à rejoindre votre réseau, entrez le nom de l'institution et l'adresse électronique de l'opérateur et cliquez sur **Ajouter un membre**. Un réseau peut comporter au total 15 membres (initiateur du réseau compris). Pour retirer un membre de votre réseau, cliquez sur le symbole "retirer" en fin de ligne du membre.

La **Figure 3** présente l'écran "Membres" initial qui affiche les certificats de membre sous l'onglet "Certificats" :

![Onglet Certificats de l'écran Membres](images/monitor_certificates.png "Certificats")
*Figure 3. Certificats*

Les opérateurs peuvent gérer les certificats des membres d'une même institution sous l'onglet "Certificats". Cliquez sur **Ajouter le certificat** pour afficher l'écran "Ajouter le certificat". Donnez un nom à votre certificat, collez vos certificats côté client au format PEM dans la zone "Clé", puis cliquez sur **Soumettre**. Vous devez redémarrer vos homologues pour que les certificats côté client puissent être pris en compte.

Pour plus d'informations sur la génération de votre clé de certificat, voir [Génération de certificats côté client](v10_application.html#generating-the-client-side-certificates).

## Canaux

Vous pouvez diviser votre réseau en canaux, où chaque canal représente un sous-ensemble de membres qui sont autorisés à consulter les données des codes blockchain instanciés sur ce canal. Chaque réseau doit comporter au moins un canal pour que les transactions puissent s'effectuer. Chaque canal a un registre unique et les utilisateurs doivent être correctement authentifiés pour exécuter des opérations de lecture/écriture sur ce registre. Si vous n'êtes pas sur un canal, vous ne pouvez voir aucune donnée.

La **Figure 4** illustre l'écran de tableau de bord initial qui affiche une présentation de tous les canaux de votre réseau. 

![Canaux](images/channels.png "Canaux")
*Figure 4. Canaux*

La création d'un canal entraîne la génération d'un registre spécifique à un canal. Pour plus d'informations, voir [Création d'un canal](howto/create_channel.html).

Vous pouvez aussi sélectionner un canal existant afin d'afficher des détails plus précis sur ce canal, l'appartenance et les codes blockchain actifs. Pour plus d'informations, voir [Surveillance d'un réseau](howto/monitor_network.html).  


## Code blockchain

Le code blockchain définit la logique métier et les instructions transactionnelles pour la création et la modification des actifs. 

La **Figure 5** illustre l'écran de tableau de bord initial des codes blockchain :

![Codes blockchain](images/chaincode_install_overview.png "Codes blockchain")
*Figure 5. Codes blockchain*

Le code blockchain est tout d'abord installé sur le système de fichiers d'un homologue, puis il est instancié sur un canal.  Pour plus d'informations, voir [Installation et instanciation d'un code blockchain](howto/install_instantiate_chaincode.html).


## Notifications

Vous pouvez traiter les demandes en attente et consulter les demandes terminées dans l'écran "Notifications". 

La **Figure 6** illustre l'écran "Notifications" :

![Notifications](images/notifications.png "Notifications")
*Figure 6. Notifications*

* Lorsque vous créez un canal ou êtes invité à rejoindre un nouveau canal, une notification s'affiche dans le moniteur réseau. 
* Les demandes sont regroupées dans les sous-onglets "Toutes", "En attente" et "Terminée". Les nombres figurant à la suite de l'en-tête du sous-onglet indiquent le nombre de demandes dans chaque sous-onglet.
   * Vous pouvez trouver toutes vos demandes sous le sous-onglet "Toutes".
   * Les demandes que vous n'avez pas acceptées ou que vous avez refusées, ou que vous n'avez pas encore consultées, figurent sous le sous-onglet "Non lue". Cliquez sur le bouton **Inspecter la demande** pour consulter la demande, qui comporte la règle de canal et les membres, puis cliquez sur **Accepter** ou **Refuser**. Vous pouvez aussi décider de traiter la demande ultérieurement en cliquant sur **Plus tard**. Si vous acceptez une demande et que celle-ci est acceptée par suffisamment d'opérateurs de canal, vous pouvez cliquer sur **Soumettre une demande** pour activer la mise à jour de canal.  
   * Une demande soumise apparaît alors dans le sous-onglet "Terminée". Vous pouvez cliquez sur **Inspecter la demande** pour afficher ses détails.
  
Si vous avez une longue liste de demandes, vous pouvez rechercher dans la zone de recherche située dans la partie supérieure de l'écran. Les demandes en attente peuvent être supprimées en sélectionnant la case en regard et en cliquant sur **Supprimer les demandes**. Notez qu'une demande terminée ne peut pas être supprimée. 


## Support

L'écran "Support" comporte deux onglets qui fournissent des informations de support sous l'onglet "Support" et une description des fonctions nouvelles et modifiées de chaque édition sous l'onglet "Notes sur l'édition".

Utilisez les liens et les ressources de cette page pour accéder à des forums de dépannage et de support. Si vous ne parvenez pas à déboguer votre problème ou à obtenir une réponse à votre question, cliquez sur le lien **Ouvrez un ticket de demande de service Bluemix** et suivez les instructions pour soumettre des tickets.

La **Figure 7** illustre l'écran "Support" initial qui comporte des informations de support sous l'onglet "Support" :

![Support](images/support.png "Support")
*Figure 7. Support Blockchain*

* [Docs de service {{site.data.keyword.blockchainfull_notm}}](index.html), qui est le présent site de documentation, fournit une aide sur la mise en route de la plateforme {{site.data.keyword.blockchainfull_notm}} sur {{site.data.keyword.Bluemix_notm}}. Vous pouvez accéder aux rubriques correspondantes depuis le navigateur ou rechercher un termine à l'aide de la fonction de recherche située en haut de l'écran.  
* [IBM Developer Works ![External link icon](images/external_link.svg "External link icon")](https://developer.ibm.com/blockchain/) sous **Aide communautaire** comporte des ressources et des informations pour les développeurs.  
* [IBM dWAnswers ![External link icon](images/external_link.svg "External link icon")](https://developer.ibm.com/answers/smartspace/blockchain/) sous **Ticket de demande de service** fait office de plateforme pour les questions et les réponses. Vous pouvez rechercher des réponses à des questions préalablement posées ou soumettre une nouvelle question. Assurez-vous d'inclure le mot clé **blockchain** dans votre question.   
  Vous pouvez également soumettre un ticket pour l'équipe de support {{site.data.keyword.blockchainfull_notm}} à l'aide de l'option [Ouvrir un ticket de demande de service {{site.data.keyword.Bluemix_notm}}![External link icon](images/external_link.svg "External link icon")](). Partagez les détails et les fragments de code de votre instance Bluemix spécifique.  
* [Modèles d'application ![External link icon](images/external_link.svg "External link icon")]() sous Modèles d'application **{{site.data.keyword.blockchain}}** fournit une aide et des exemples de fragment de code pour vous aider dans le développement d'applications.  
* [Hyperledger Fabric ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/) et [Communauté Hyperledger Fabric![External link icon](images/external_link.svg "External link icon")]() sous **Hyperledger Fabric** fournissent des données plus détaillées sur la pile Hyperledger Fabric.  
  Dialoguez avec un [expert Hyperledger![External link icon](images/external_link.svg "External link icon")](https://chat.hyperledger.org/channel/general) si vous avez des questions sur le code Hyperledger Fabric.   
  
  
La **Figure 8** illustre l'écran "Members" initial qui comporte les fonctions nouvelles et modifiées de chaque édition sous l'onglet "Notes sur l'édition" :

![Notes sur l'édition](images/releasenotes.png "Notes sur l'édition")
*Figure 8. Notes sur l'édition*


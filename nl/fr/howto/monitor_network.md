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

# Surveillance d'un réseau de blockchain

Ce tutoriel vous explique comment afficher et surveiller les informations de statut de votre réseau {{site.data.keyword.blockchain}} sur {{site.data.keyword.cloud_short}}.
{:shortdesc}


## Surveillance des homologues, des tris et des autorités de certification
{: #monitor-nodes}

Vous pouvez lancer une requête HTTP **HEAD** sur l'un de vos noeuds réseau pour vérifier son statut. Il peut s'agir d'un homologue, d'un programme de tri ou d'une autorité de certification dans votre réseau Blockchain. Une requête **HEAD** est similaire à une requête GET et n'envoie que les en-têtes sans le corps de la requête. Si le noeud fonctionne normalement, une réponse 200 est renvoyée.

1. Depuis l'écran "Présentation" du Moniteur réseau, cliquez sur **Profil de connexion**. Vous pouvez ensuite cliquer sur **JSON brut** pour afficher le profil de connexion dans votre navigateur Web ou bien cliquer sur **Télécharger** pour enregistrer le profil de connexion sur le poste local.
2. Dans le profil de connexion, identifiez les informations d'URL du noeud que vous désirez vérifier. Par exemple, l'URL du programme de tri `fabric-orderer-20190b` est `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.  
    ![Exemple d'URL de programme de tri](../images/orderer_url.png "Exemple d'URL de programme de tri")
3. Remplacez **grpcs** par **https** dans l'URL. Dans l'exemple ci-dessus, l'URL devient `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Lancez la requête **HEAD** sur l'URL avec un outil tel que l'application curl ou Chrome Postman.
    - Si une réponse avec statut 200 est renvoyée, les noeuds de votre réseau fonctionnent normalement.
    - Si la requête **HEAD** échoue avec une erreur de connexion, il se peut que votre noeud réseau ne soit pas en opération, que l'URL du noeud soit erronée ou encore qu'un pare-feu bloque l'accès au noeud. Il vous faut résoudre cette erreur faute de quoi vos applications ne pourront pas se connecter au noeud.

L'exemple suivant illustre une requête **HEAD** avec une réponse 200 dans curl. Notez que vous pouvez ignorer l'erreur grpc puisque la requête HTTP **HEAD** vérifie si le noeud est accessible. Si la réponse est positive, la requête grpc sur le noeud fonctionne également dans votre application.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

L'exemple suivant illustre une requête **HEAD** avec une erreur de connexion dans curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

La figure suivant illustre une requête **HEAD** avec une réponse 200 dans l'application Chrome Postman.  

![Exemple de requête HEAD dans Postman](../images/orderer_head_postman.png "Exemple de requête HEAD dans Postman")


## Surveillance des canaux
{: #monitor-channnels}

Accédez au moniteur réseau et localisez le canal que vous voulez afficher et surveiller dans l'écran "Canal".  Dans l'écran de canal spécifique, vous pouvez consulter les informations de statut des données, les membres et le code blockchain instancié de ce canal sous trois onglets :

* **Vue d'ensemble du canal**  
  L'onglet "Vue d'ensemble du canal" affiche les informations de bloc concernant ce canal :
    * Série de points de données incluant le nombre total de blocs qui sont créés, l'intervalle de temps écoulé depuis la dernière transaction, le nombre d'instanciations de code blockchain et le nombre d'appels de code blockchain.
    * Tableau répertoriant l'ensemble des blocs sur ce canal.  Développez un bloc pour afficher ses informations détaillées.  

  ![Vue d'ensemble du canal](../images/channel_overview_detail.png "Vue d'ensemble du canal")  

* **Membres**  
  L'onglet "Membres" affiche les informations relatives aux membres de ce canal, notamment les adresses électroniques des opérateurs de l'organisation.
  ![Channel members](../images/channel_members.png "Channel members")  

* **Code blockchain**  
  L'onglet "Code blockchain" répertorie tout le code blockchain qui est instancié sur ce canal avec l'ID de code blockchain, la version et le nombre d'homologues qui exécutent ce code blockchain.   

  Développez un code blockchain pour afficher ses informations détaillées :  
    * Vous pouvez cliquer sur **JSON** pour afficher le fichier JSON du code blockchain.
    * Vous pouvez cliquer sur **Journaux** pour consulter les journaux du code blockchain.
    * Vous pouvez cliquer sur **Supprimer** pour supprimer le conteneur de code blockchain en cours d'exécution.
    **Remarque **: La suppression du conteneur de code blockchain n'a pas pour effet de supprimer réellement le code blockchain. Un code blockchain instancié sur un réseau de blockchain ne peut pas être supprimé.

  ![Code blockchain de canal](../images/channel_chaincode.png "Code blockchain de canal")


## Surveillance du code blockchain
{: #monitor-chaincode}

Accédez au Moniteur réseau et affichez l'écran "Installer le code". Si du code blockchain est en cours d'exécution, choisissez un homologue dans la liste déroulante afin d'afficher tout le code blockchain de cet homologue dans le tableau avec les ID et les versions de code blockchain.  Vous pouvez effectuer l'installation et l'instanciation de votre code blockchain sur cet écran.  Pour plus d'informations, voir [Installation, instanciation et mise à jour d'un code blockchain](install_instantiate_chaincode.html).

  ![Code blockchain](../images/chaincode_install_overview.png "Code blockchain")


## Surveillance des modèles d'application
{: #monitor-apps}

Dans un réseau de plan Starter, vous pouvez consulter et accéder à des modèles d'application dans l'écran "Essayer les modèles" du moniteur réseau.  Après avoir déployé un modèle d'application, vous pouvez cliquer sur le bouton **Lancer** pour accéder à l'interface de votre application ou sur le lien **Afficher sur GitHub** pour visiter le référentiel de code.  Pour plus d'informations, voir [Déploiement de modèles d'application](prebuilt_samples.html).

  ![Modèles d'application](../images/sampleappflow0.png "Modèles d'application")

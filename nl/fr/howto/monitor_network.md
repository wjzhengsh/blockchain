---

copyright:
  years: 2017
lastupdated: "2017-12-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Surveillance d'un réseau {{site.data.keyword.blockchain}}

Ce tutoriel vous explique comment afficher et surveiller les informations de statut de votre réseau {{site.data.keyword.blockchain}} sur {{site.data.keyword.Bluemix_short}}.
{:shortdesc}

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
  L'onglet "Code blockchain" répertorie tous les codes blockchain  qui sont instanciés sur ce canal avec l'ID de code blockchain, la version et le nombre d'homologues qui exécutent ce code blockchain.   
    
  Développez un code blockchain pour afficher ses informations détaillées :  
    * Vous pouvez cliquer sur **JSON** pour afficher le fichier JSON du code blockchain.
    * Vous pouvez cliquer sur  **Journaux** pour consulter les journaux du code blockchain.
    * Vous pouvez cliquer sur **Supprimer** pour supprimer le conteneur de code blockchain en cours d'exécution. 
    **Remarque **: La suppression du conteneur de code blockchain n'a pas pour effet de supprimer réellement le code blockchain. Un code blockchain instancié sur un réseau de blockchain ne peut pas être supprimé.
  
  ![Code blockchain de canal](../images/channel_chaincode.png "Code blockchain de canal") 
  

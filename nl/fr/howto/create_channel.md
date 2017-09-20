---

copyright:
  years: 2017
lastupdated: "2017-08-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Création ou mise à jour d'un canal

Les canaux offrent un mécanisme incroyablement puissant pour le partitionnement et l'isolement des données, et ils constituent la base essentielle
pour la confidentialité des données. Seuls les membres du même canal peuvent accéder aux données de ce canal.
{:shortdesc}

Pour garantir la sécurité du canal, les règles de mise à jour du canal sont configurées pour définir le nombre d'opérateurs de canal qui doivent s'accorder sur une demande de création ou de mise à jour des canaux afin que l'opération ne soit effectuée. 

## Création d'un canal
Cliquez sur le bouton **Nouveau canal** dans l'écran "Canaux" de votre moniteur réseau, puis procédez comme suit pour soumettre une demande de création de canal :  
1. Choisissez un nom qui reflète l'objectif métier du canal, ajoutez une description si vous le souhaitez, puis cliquez sur **Suivant**. Le nom de canal doit être unique dans un réseau de blockchain. Il doit commencer par une lettre et peut contenir uniquement des caractère en minuscule, des nombres ou des tirets.  
  ![Créer un canal 1](../images/create_channel.png "Ecran Créer un canal 1")  
    
2. Invitez des membres de votre réseau en les sélectionnant et en cliquant sur le bouton **Ajouter un membre**. Personnalisez les droits en affectant des rôles pour chacun des membres invités, puis cliquez sur **Suivant**.  
  ![Créer un canal 2](../images/create_channel_2.png "Ecran Créer un canal 2")  
  
    * Un opérateur de canal peut interroger ou mettre à jour le registre de canal. Cet opérateur a le droit d'**accepter** ou de **décliner** une demande de création de canal, et de soumettre une demande de mise à jour de canal. Il doit y avoir au moins un **Opérateur** dans chaque canal.  
    * Un rédacteur de canal peut mettre à jour le registre de canal.
    * Un lecteur de canal peut uniquement interroger le registre de canal.
  
3. Configurez les règles de mise à jour de canal en sélectionnant le nombre d'opérateurs de canal pour l'approbation d'une demande de mise à jour de canal, puis cliquez sur **Soumettre une demande**.   
  ![Créer un canal 3](../images/create_channel_3.png "Ecran Créer un canal 3")  

Les membres invités recevront un courrier électronique d'invitation. Ils peuvent aussi trouver la demande avec le statut "Vote en attente" dans l'écran **Notifications** du moniteur réseau :  
* Les membres qui sont invités en tant qu'opérateurs de canal peuvent cliquer sur le bouton **Inspecter la demande** pour passer en revue la configuration de canal, puis **Accepter** ou **Décliner** la demande de mise à jour de canal.  Si la demande de mise à jour de canal obtient suffisamment de votes **Accepter** des opérateurs de canal, son statut devient "Vote accepté". Un opérateur de canal peut ensuite cliquer sur bouton **Soumettre une demande** pour terminer la mise à jour de canal.  
* Les membres qui sont invités en tant que rédacteurs ou lecteurs de canal peuvent cliquer sur le bouton **Inspecter la demande** pour passer en revue la configuration de canal.

S'agissant d'un type spécial de demande de mise à jour de canal, lorsque suffisamment d'opérateurs de canal s'accordent sur la demande de création de canal, et qu'un opérateur de canal soumet la demande, le nouveau canal est créé. Tous les membres du canal peuvent voir le canal dans l'écran "Canaux" de leur moniteur réseau.

## Mise à jour d'un canal
Si vous voulez modifier la configuration d'un canal, par exemple, ajouter ou retirer des membres de canal, ou encore modifier les règles de mise à jour de canal, vous pouvez soumettre une demande de mise à jour de canal. Dans l'écran "Canaux" de votre moniteur réseau, localisez le canal que vous voulez modifier et sélectionnez **Editer le canal** dans la liste déroulante sous l'en-tête **Action**.  Pour soumettre une demande de mise à jour de canal, suivez les mêmes étapes que pour la soumission d'une demande de création de canal.

Tous les membres du canal recevront une notification par courrier électronique lors de la demande de mise à jour de canal :
* Les nouveaux membres invités reçoivent des notifications par courrier électronique pour les inviter à rejoindre le canal. Ils peuvent aussi trouver la demande avec le statut "Vote en attente" dans l'écran **Notifications** de leur moniteur réseau :  
    * Les membres qui sont invités en tant qu'opérateurs de canal peuvent cliquer sur le bouton **Inspecter la demande** pour passer en revue la configuration de canal, puis **Accepter** ou **Décliner** la demande de mise à jour de canal.  Si la demande de mise à jour de canal obtient suffisamment de votes **Accepter** des opérateurs de canal, son statut devient "Vote accepté". Un opérateur de canal peut ensuite cliquer sur bouton **Soumettre une demande** pour terminer la mise à jour de canal.  
    * Les membres qui sont invités en tant que rédacteurs ou lecteurs de canal peuvent cliquer sur le bouton **Inspecter la demande** pour passer en revue la configuration de canal.
* Les membres retirés reçoivent des notifications par courrier électronique concernant la modification de canal
* Les opérateurs de canal existants reçoivent également des notifications par courrier électronique lors de la mise à jour de canal.  Ils peuvent aussi trouver la demande avec le statut "Vote en attente" dans l'écran **Notifications** de leur moniteur réseau.

Lorsque suffisamment d'opérateurs de canal s'accordent sur la demande de mise à jour de canal et qu'un opérateur de canal soumet la demande, le canal est mis à jour. Tous les membres du canal peuvent voir le canal mis à jour dans l'écran "Canaux" de leur moniteur réseau.

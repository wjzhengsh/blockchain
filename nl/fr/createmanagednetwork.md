---

copyright:
  years: 2017
lastupdated: "2017-12-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Création de canaux gérés 

**ATTENTION :** Avant d'utiliser une offre {{site.data.keyword.blockchainfull}}, lisez les informations relatives aux données techniques et à l'aide dans la section [Clause de protection](/docs/services/blockchain/needtoknow.html).  
{:shortdesc}

Dans certains scénarios d'utilisation (marchés des changes extrêmement régulés, par exemple), il peut être  nécessaire de faire appel à un tiers pour la prise en charge des rôles d'administration des canaux. Cette fonction est habituellement confiée à différents opérateurs ou membres. 

Le processus de mise à disposition d'un réseau est semblable à la création de n'importe quel réseau. La principale différence réside dans l'attribution de droits aux membres qui définissent comment ces derniers peuvent réaliser des transactions au sein des canaux.  

Les étapes permettant de créer un réseau et d'inviter des membres sont accessibles ici : [Gouvernance d'un réseau](/docs/services/blockchain/get_start.html#creating-a-network). 

**Remarque **: Dans un exemple de scénario concret, l'opérateur d'un tel réseau pourrait installer des codes blockchain personnalisés lors de l'étape "Créer un réseau" à l'aide de l'éditeur de règles, mais dans le cas du présent exemple, nous partirons du principe que votre configuration réseau doit être de type standard. 

## Création du canal

Dès que vous disposez d'un réseau et que les membres que vous avez invités ont suivi les étapes du processus d'intégration, accédez au menu **Canaux** dans la barre d'outils. 

Cliquez sur **Nouveau canal**. Vous êtes redirigé vers un écran dans lequel vous pouvez attribuer un nom à vos canaux et entrer une description (facultatif). 

Après avoir cliqué sur **Suivant**, vous accédez à un écran dans lequel vous pouvez inviter des membres à rejoindre le canal et gérer leurs droits. Dans cet exemple, on suppose que vous agissez en tant que tiers digne de confiance dans le cadre d'un échange de devises entre deux banques. Vous (le tiers de confiance) vous définissez vous-même en tant qu'“Opérateur” unique pour le canal et attribuez aux deux autres banques le rôle de “Rédacteurs”. Vous devenez ainsi la seule autorité qui peut éditer le canal (instanciation de code, par exemple, opération décrite plus loin) tout en laissant aux deux banques la possibilité d'appeler des transactions. Votre écran doit ressembler à ceci : 

  ![Sélection des rôles de membre](images/selectmemberroles.png "Sélection des rôles de membre") 
*Où “JoeCo” est vous-même, le tiers de confiance, et “IBM” et “Chris” sont les deux banques (il s'agit juste d'un exemple).* 

Une fois que vous avez affecté les droits appropriés, cliquez sur **Suivant**. 

Vous accédez à un écran de mise à jour des règles de canal. Etant donné qu'il y a un seul opérateur sur ce canal (vous), sélectionnez le nombre de membres nécessaire à la création du canal : “1”. Cliquez ensuite sur **Soumettre une demande**. 

Remarque : même si vous êtes le seul opérateur de ce canal, il s'agit encore seulement d'une demande ; vous devez encore accéder à l'écran correct pour approuver et soumettre la demande de création du canal. 

Des courriers électroniques sont envoyés aux membres que vous invitez, pour les inviter à rejoindre le canal. Toutefois, ils disposent seulement de privilège en “écriture”, et vous êtes chargé de terminer le processus de création de canal. Accédez à l'écran “Notifications” du moniteur réseau, dans lequel une alerte de création du canal s'affiche. Localisez la demande à traiter dans les sous-onglets "Tout" ou "En attente". Cliquez sur **Inspecter la demande** pour afficher les détails du canal et cliquez sur **Approuver**. Cliquez ensuite sur **Soumettre une demande**. 

Félicitations ! Vous venez de créer un canal géré. 

Dans un réseau composé de 15 membres (14 banques et vous, le tiers digne de confiance), vous pourriez avoir des douzaine de ces canaux. Les banques A et B sur un canal. Les banques B et F sur un canal. Les banques pourraient avoir des canaux distincts pour chaque type de devise qu'elles utilisent. Mais dans tous les cas, le tiers digne de confiance crée le canal, valide les transactions, et fait office d'opérateur unique. 

# Instanciation du code blockchain

Votre canal est créé mais du code blockchain doit encore lui être associé. Dans un canal dédié aux changes de devises, ce code blockchain nécessiterait probablement l'ensemble des trois membres du canal (les deux banques en question et vous, le tiers digne de confiance) pour valider **chaque** transaction. Ce code blockchain pourrait aussi être écrit pour envoyer un enregistrement du *résultat* de cette transaction à un canal "en lecture seule" géré comprenant chaque membre du réseau (décrit plus loin). Pour que le code blockchain puisse être instancié, cependant, il doit d'abord être installé sur les homologues des membres. Une fois cette condition remplie, l'instanciation peut être effectuée par l'opérateur du canal (vous).  

La procédure effective d'installation et instanciation de ce type de code blockchain n'est pas différente de celle de tout autre code blockchain (si ce n'est le nombre d'opérateurs nécessaires pour approuver l'instanciation), vous devez donc suivre les instructions de la section [Installation et instanciation d'un code blockchain](/docs/services/blockchain/install_instantiate_chaincode.html.html).

# Canal en lecture seule géré

Les réglementations financières du marché de change de devises, pour rester avec ce même scénario, peuvent exiger qu'il y ait également un canal du réseau comportant chacune de ces banques dans un mode passif, “en lecture seule”, et ce afin de permettre le suivi du **résultat** de ces transactions de devise. Cela permet de s'assurer que, par exemple, la Banque A – qui vient de perdre des millions de dollars lors d'un “pari” avec la Banque B (sur l'augmentation ou la diminution de la valeur d'une devise au cours d'une période) – ne manquera pas de valider la transaction qui enverra ces millions de dollars à la Banque B. 

Pour créer ce type de canal, suivez la même procédure de création de canal que celle suivie plus haut, au lieu seulement d'inviter les deux banques et de les définir en tant que "Rédacteurs" (avec vous en tant que seul opérateur), vous invitez **chaque** banque et les définissez toutes en tant que "Lecteurs". Ensuite, suivez le même processus que pour l'installation et l'instanciation du code blockchain. 

## {{site.data.keyword.IBM_notm}} Support 

{{site.data.keyword.IBM_notm}} propose un support sur les solutions {{site.data.keyword.blockchain}} mises en oeuvre par {{site.data.keyword.IBM_notm}}. Vous pouvez accéder aux données relatives à {{site.data.keyword.blockchainfull_notm}} Support via [{{site.data.keyword.blockchainfull_notm}} DockerHub ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hub.docker.com/u/ibmblockchain/) et découvrir les engagements de support disponibles.

Pour une présentation complète des fonctions et fonctionnalités de Hyperledger Fabric v1.0,
consultez la [documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/latest/).

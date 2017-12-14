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
{:tip: .tip}

# Gouvernance du réseau
{: #getting-started-with-blockchain}

La plateforme {{site.data.keyword.blockchainfull}} sur {{site.data.keyword.Bluemix_notm}} fournit un réseau {{site.data.keyword.blockchain}} avec un haut niveau de sécurité, d'intégrité, d'évolutivité et de performance. Vous pouvez rapidement mettre à disposition un réseau {{site.data.keyword.blockchain}} pleinement opérationnel et utiliser le moniteur réseau pour exécuter immédiatement le code blockchain et les applications sans avoir à concevoir et à configurer un réseau de blockchain privé. 
{:shortdesc}
 
Ce tutoriel de mise en route présente les prérequis et les étapes que vous devrez suivre pour obtenir un réseau {{site.data.keyword.blockchain}} hébergé dans un environnement hautement disponible et sécurisé d'IBM.  

Les étapes ci-après indiquent le flot de base pour le lancement d'un réseau {{site.data.keyword.blockchain}} composé de plusieurs organisations membres :
1. Un **Initiateur du réseau**, en tant que type spécial de membre réseau, crée le réseau {{site.data.keyword.blockchain}} et définit des règles de gouvernance. L'initiateur du réseau peut ensuite inviter d'autres institutions à rejoindre ce réseau {{site.data.keyword.blockchain}} en tant que membres réseau.  Pour plus de détails, voir [Création d'un réseau](#creating-a-network).
2. >Les **membres du réseau ** reçoivent une notification par courrier électronique qui leur fournit les instructions pour rejoindre un réseau {{site.data.keyword.blockchain}}. Outre les instructions de la notification par courrier électronique, vous pouvez également suivre les étapes de la section [Rejoindre un réseau](#joining-a-network).
3. Tous les **membres du réseau**, après avoir créé ou rejoint un réseau, peuvent accéder au moniteur réseau, tableau de bord doté d'une interface graphique pour configurer et gérer leurs ressources réseau. Vous pouvez définir des canaux avec un groupe de membres du réseau pour l'exécution de transactions privées dans un registre spécifique à un canal, auquel ne peuvent accéder que les membres du canal. Dans le moniteur réseau, vous pouvez également joindre vos propres homologues au canal, puis installer et instancier du code blockchain sur ces derniers. Pour plus de détails, voir [Configuration des ressources réseau et de l'environnement](#configuring-network-resources-and-environment).
4. Les **développeurs d'applications** créent des applications qui permettent d'interagir avec le réseau {{site.data.keyword.blockchain}}. Pour plus d'informations, voir [Activation des applications pour une interaction avec le réseau](#enabling-applications-to-interact-with-the-network).
5. Les **opérateurs réseau** surveillent les transactions sur leurs canaux à l'aide du moniteur réseau. Pour plus de détails, voir [Surveillance des ressources réseau](#monitoring-network-resources).

## Création d'un réseau
Avant de commencer, vous devez créer une [instance de service de plateforme {{site.data.keyword.blockchain}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) sur {{site.data.keyword.Bluemix_notm}}. Vous devrez vous connecter avec votre ID {{site.data.keyword.Bluemix_notm}}. Si vous n'avez pas d'ID, cliquez sur le bouton **Inscrivez-vous pour créer**.  Renommez le service et les noms des données d'identification pour votre instance afin de pouvoir la reconnaître facilement dans le futur. Sélectionnez la région, l'organisation et l'espace {{site.data.keyword.Bluemix_notm}}, où vous pouvez déployer votre réseau {{site.data.keyword.blockchain}}. Ensuite, sélectionnez un **plan d'entreprise** dans le tableau Plans de tarification et cliquez sur le bouton **Créer**.  

Vous pouvez trouver votre instance de service de plateforme {{site.data.keyword.blockchain}} dans votre [{{site.data.keyword.Bluemix_notm}} tableau de bord du service ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} tableau de bord du service").  

Si vous êtes un initiateur du réseau, cliquez sur le bouton **Créer un réseau** pour initier un réseau {{site.data.keyword.blockchain}}.  Suivez les instructions de l'assistant pour effectuer la configuration de base de votre réseau et de vos ressources.  
![Assistant de création de réseau](images/create_network_name.png "Assistant de création de réseau")  

1. Dans l'écran "Mise en route", donnez un nom à votre réseau, choisissez l'emplacement de votre organisation {{site.data.keyword.Bluemix_notm}}, puis ajoutez le nom de votre institution. Lorsque vous invitez d'autres membres de réseau, ceux-ci recherchent ce nom pour rejoindre le réseau. Cliquez sur **Suivant**.
2. (Facultatif) Dans l'écran "Inviter des membres", entrez le nom de l'institution ainsi que l'adresse de courrier électronique du membre que vous voulez inviter à rejoindre votre réseau. Le nom d'institution que vous indiquez n'est pas un titre officiel. Il permet simplement de reconnaître facilement l'institution et il peut être modifié une fois le réseau rejoint. Notez qu'un réseau peut accueillir jusqu'à 15 membres, vous y compris. Cette étape est facultative, et vous pouvez inviter des membres à rejoindre votre réseau plus tard dans le moniteur réseau.  Cliquez sur **Suivant**.
	Les membres que vous invitez vont recevoir une notification par courrier électronique relative à votre invitation une fois que vous avez effectué toutes les étapes de création du réseau.
3. Dans l'écran "Définir des règles de gouvernance", définissez des règles pour l'appartenance, la création de canal et le code blockchain. Par défaut, tous les membres du réseau peuvent inviter d'autres membres à rejoindre le réseau, créer des canaux et instancier du code blockchain. Pour cette disponibilité générale, votre réseau utilise les règles de gouvernance par défaut.  Cliquez sur **Suivant**.
4. Dans l'écran "Vérifier le récapitulatif", vérifiez la configuration de votre réseau. Si vous voulez apporter des modifications, cliquez sur **Editer** en regard de l'en-tête de section ou cliquez sur le bouton **Précédent** pour revenir aux écrans précédents. Une fois la configuration réseau terminée, cliquez sur **Terminé**.  
5. Dans l'écran "Le réseau a été créé", vous êtes informé que la création de votre réseau a abouti. Vous pouvez cliquer sur **Ajouter des homologues** pour configurer vos ressources réseau ou cliquer directement sur **Afficher le moniteur** pour ouvrir le moniteur réseau. Si vous n'ajoutez pas des homologues maintenant, ils peuvent être ajoutés ultérieurement dans le moniteur réseau.  Pour plus d'informations sur les homologues, voir [Présentation](v10_dashboard.html#overview).
    
Vous êtes maintenant prêt pour le déploiement d'un réseau {{site.data.keyword.blockchain}} qui peut prendre en charge les ressources réseau suivantes :  
* Une autorité de certification spécifique aux membres
* Des règles de gouvernance par défaut
* Jusqu'à 15 membres de réseau  
* Trois programmes de tri et deux noeuds d'autorité de certification intermédiaires
* Jusqu'à trois petits homologues pour chaque membre  
* Un service de tri tolérant aux pannes
* Jusqu'à 150 canaux
* Jusqu'à 10 instanciations de codes blockchain


## Rejoindre un réseau
Comme pour la création d'un réseau, vous devez créer une instance de service de plateforme [{{site.data.keyword.blockchain}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) sur {{site.data.keyword.Bluemix_notm}}. Vous devrez vous connecter avec votre ID {{site.data.keyword.Bluemix_notm}}. Si vous n'avez pas d'ID, cliquez sur le bouton **Inscrivez-vous pour créer**.  Renommez le service et les noms des données d'identification pour votre instance afin de pouvoir la reconnaître facilement dans le futur. Sélectionnez la région, l'organisation et l'espace {{site.data.keyword.Bluemix_notm}}, où vous pouvez déployer votre réseau {{site.data.keyword.blockchain}}. Ensuite, sélectionnez un **plan d'entreprise** dans le tableau Plans de tarification et cliquez sur le bouton **Créer**.  

Vous pourrez trouver votre instance de service de plateforme {{site.data.keyword.blockchain}} dans le [{{site.data.keyword.Bluemix_notm}} tableau de bord du service ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} tableau de bord du service"). 

Si vous êtes un membre de réseau invité, cliquez sur le bouton **Invitation en attente ->**, sélectionnez le réseau que vous voulez rejoindre dans la liste déroulante, puis cliquez sur le bouton **Rejoindre le réseau** . Suivez les instructions de l'assistant pour afficher la configuration de base de votre réseau et configurer vos propres ressources réseau.  
![Assistant pour rejoindre le réseau](images/join_network_name.png "Assistant pour rejoindre le réseau")  

1. Dans l'écran "Mise en route", ajoutez le nom de votre institution et vérifiez l'emplacement de votre organisation {{site.data.keyword.Bluemix_notm}}. Cliquez sur **Suivant**.
2. Dans l'écran "Examiner les règles de gouvernance", affichez les règles de gouvernance du réseau relatives à l'appartenance, à la création de canal et au code blockchain. Cliquez sur **Suivant**.
3. (Facultatif) Dans l'écran "Ajouter des homologues", choisissez la taille et le nombre d'homologues à ajouter. Cliquez sur **Suivant**. Chaque membre d'un réseau peut ajouter jusqu'à 3 homologues ; à ce stade, seuls des "petits" homologues sont disponibles. Cette étape est facultative, et vous pouvez ajouter vos homologues plus tard dans le moniteur réseau. Pour plus d'informations sur les homologues, voir [Présentation](v10_dashboard.html#overview).
4. Dans l'écran "Vérification du récapitulatif du réseau", vérifiez la configuration du réseau. Si vous voulez apporter des modifications, cliquez sur **Editer** en regard de l'en-tête de section ou cliquez sur le bouton **Précédent** pour revenir aux écrans précédents. Une fois la configuration des ressources terminée, cliquez sur **Terminé**. Vous serez informé que vous avez rejoint le réseau. Vous pouvez ensuite cliquer sur **Afficher le moniteur** pour ouvrir le moniteur réseau ou cliquer sur **Créer un canal** pour initier une demande de création de canal. Vous pourrez créer des canaux plus tard dans le moniteur réseau. Pour plus d'informations, voir [Canaux](v10_dashboard.html#channels).
 

## Configuration des ressources réseau et de l'environnement

1. Accédez au moniteur réseau une fois que vous avez créé ou rejoint un réseau {{site.data.keyword.blockchain}}. Le moniteur réseau est un tableau de bord doté d'une interface graphique depuis lequel vous pouvez gérer et conserver un suivi des informations de statut réseau. Pour plus d'informations, voir [Moniteur réseau](v10_dashboard.html).
2. Ajoutez vos propres homologues au réseau. Si vous avez déjà ajouté suffisamment d'homologues, vous pouvez omettre cette étape. Les homologues exécutent du code blockchain et ils constituent le noeud final pour interagir avec vos applications. Cliquez sur **Ajouter des homologues** dans l'écran "Présentation", puis sélectionnez le nombre et la taille de vos homologues. Pour plus d'informations, voir [Présentation générale](v10_dashboard.html#resources).
3. Configurez un canal. Tous les membres du même canal sont mis à disposition avec un registre spécifique à un canal, lequel fournit l'isolement de données et la confidentialité. Pour plus d'informations sur la création d'un canal, voir [Création d'un canal](howto/create_channel.html#creating-a-channel).  
	
	Si vous êtes un membre de canal qui a été invité à rejoindre un canal, vous allez recevoir une notification par courrier électronique avec un lien vers l'assistant qui va vous autoriser à rejoindre ce canal. 
4. Joignez les homologues au canal.  Seuls les homologues qui sont associés au canal peuvent accéder à son registre. Pour plus d'informations, voir [Canaux](v10_dashboard.html#channels).
5. Installez et instanciez du code blockchain. Tous les membres de canal doivent installer le même code blockchain avec le même nom et la même version sur chaque homologue qui va exécuter le code blockchain. Une fois installé, le code blockchain doit être instancié sur le canal avant de pouvoir être installé. Pour plus d'informations, voir [Installation et instanciation d'un code blockchain](howto/install_instantiate_chaincode.html).  

**Remarque **: Pour atteindre une haute disponibilité, chaque institution doit acheter au moins 2 homologues, et au sein d'un canal, chaque institution participant doit rejoindre au moins 2 homologues.

## Activation des applications pour une interaction avec le réseau
Les applications optimisent les API SDK pour interagir avec vos composants réseau {{site.data.keyword.blockchain}}. Vous devez ajouter les informations de noeud final d'API de vos composants réseau dans votre application de sorte que celle-ci puisse finalement cibler vos homologues avec des demandes de transaction. Vous pouvez ensuite ajouter les informations de noeud final d'API depuis le moniteur réseau. Les applications peuvent être hébergées sur votre système de fichiers local ou sur {{site.data.keyword.Bluemix_notm}}. Pour plus d'informations, consultez [Développement d'applications](v10_application.html).

## Surveillance des ressources réseau  
Une fois qu'une transaction a été déclenchée depuis votre application, vous pouvez afficher les informations d'état de transaction dans le moniteur réseau. Pour plus d'informations sur la surveillance réseau, voir [Surveillance d'un réseau](howto/monitor_network.html).
  
## Quitter un réseau 
Si vous voulez quitter un réseau, supprimez l'instance de service de blockchain de votre tableau de bord {{site.data.keyword.Bluemix_notm}}.  

**Remarque **: Avant de quitter un réseau, vérifiez que vous n'êtes membre d'aucun des canaux du réseau. Dans le cas contraire, vous recevrez des messages lors de la sortie du réseau. Un retrait de membre de canal doit mettre fin au processus de mise à jour de canal. Pour plus d'informations sur le processus de mise à jour de canal, voir [Mise à jour d'un canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.0, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

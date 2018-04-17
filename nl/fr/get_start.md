---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Gouvernance d'un réseau de plan Enterprise
{: #getting-started-with-blockchain}

Le plan Enterprise de la plateforme {{site.data.keyword.blockchainfull}} fournit un réseau de blockchain avec un haut niveau de sécurité, d'intégrité, d'évolutivité et de performance. Vous pouvez rapidement mettre à disposition un réseau pleinement opérationnel et utiliser le Moniteur réseau, tableau de bord d'interface graphique, pour exécuter immédiatement le [code blockchain](glossary.html#chaincode) et les applications sans avoir à concevoir et à configurer un réseau de blockchain privé.
{:shortdesc}

**Remarque **: Le plan Enterprise de la plateforme {{site.data.keyword.blockchainfull_notm}} fournit un environnement de production. Si vous avez besoin d'un environnement de tests et de développement, voir [A propos du plan Starter](starter_plan.html).

Ce tutoriel présente les prérequis et les étapes que vous devez suivre pour obtenir un réseau de plan Enterprise hébergé dans un environnement hautement disponible et sécurisé d'IBM.  

Les étapes suivantes indiquent le flux de base permettant de lancer un réseau de plan Enterprise avec plusieurs [membres](glossary.html#member) réseau :
1. Un **Initiateur du réseau**, en tant que type spécial de membre réseau, crée le réseau et définit des règles de gouvernance. L'initiateur du réseau peut ensuite inviter d'autres [organisations](glossary.html#organization) à rejoindre ce réseau en tant que membres réseau.  Pour plus d'informations, voir [Création d'un réseau](#creating-a-network).
2. Les **membres réseau** invités reçoivent une notification par courrier électronique qui leur fournit les instructions pour rejoindre un réseau {{site.data.keyword.blockchain}}. Outre les instructions de la notification par courrier électronique, vous pouvez également suivre les étapes de la section [Rejoindre un réseau](#joining-a-network).
3. Tous les **membres du réseau**, après avoir créé ou rejoint un réseau, peuvent accéder au moniteur réseau pour configurer et gérer leurs ressources réseau. Vous pouvez définir des [canaux](glossary.html#channel) avec un groupe de membres du réseau pour l'exécution de transactions privées dans un registre spécifique à un canal, auquel ne peuvent accéder que les membres du canal. Dans le moniteur réseau, vous pouvez également joindre vos propres homologues au canal, puis installer et instancier du code blockchain sur ces derniers. Pour plus de détails, voir [Configuration des ressources réseau et de l'environnement](#configuring-network-resources-and-environment).
4. Les **développeurs d'application**, après avoir développé les applications, permettent l'interaction entre leurs applications et le réseau. Pour plus d'informations, voir [Activation des applications pour une interaction avec le réseau](#enabling-applications-to-interact-with-the-network).
5. Les **opérateurs réseau** surveillent les transactions sur leurs canaux dans le Moniteur réseau. Pour plus de détails, voir [Surveillance des ressources réseau](#monitoring-network-resources).


## Création d'un réseau
Avant de commencer, vous devez créer une [instance de service de plateforme {{site.data.keyword.blockchain}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) sur {{site.data.keyword.Bluemix_notm}}. Vous devez vous connecter avec votre ID {{site.data.keyword.Bluemix_notm}}. Si vous n'avez pas d'ID, cliquez sur le bouton **Inscrivez-vous pour créer**.  Renommez le service et les noms des données d'identification pour votre instance afin de pouvoir la reconnaître facilement dans le futur. Sélectionnez la région, l'organisation et l'espace {{site.data.keyword.Bluemix_notm}}, où vous pouvez déployer votre réseau {{site.data.keyword.blockchain}}. Ensuite, sélectionnez un **Plan d'appartenance d'entreprise** dans le tableau Plans de tarification et cliquez sur le bouton **Créer**.  

Vous pouvez trouver votre instance de service de plateforme {{site.data.keyword.blockchain}} dans votre tableau de bord du service [tableau de bord du service {{site.data.keyword.Bluemix_notm}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}").  

Si vous êtes un initiateur du réseau, cliquez sur le bouton **Créer un réseau** pour initier un réseau {{site.data.keyword.blockchain}}.  Suivez les instructions de l'assistant pour effectuer la configuration de base de votre réseau et de vos ressources.  
![Assistant de création de réseau](images/create_network_name.png "Assistant de création de réseau")  

1. Dans l'écran "Mise en route", donnez un nom à votre réseau, choisissez l'emplacement de votre organisation {{site.data.keyword.Bluemix_notm}}, puis ajoutez le nom de votre institution. Lorsque vous invitez d'autres membres de réseau, ceux-ci recherchent ce nom pour rejoindre le réseau. Cliquez sur **Suivant**.
2. (Facultatif) Dans l'écran "Inviter des membres", entrez le nom de l'institution ainsi que l'adresse de courrier électronique du membre que vous voulez inviter à rejoindre votre réseau. Le nom d'institution que vous indiquez n'est pas un titre officiel. Il permet simplement de reconnaître facilement l'institution et il peut être modifié une fois le réseau rejoint. Notez qu'un réseau peut accueillir jusqu'à 15 membres, vous y compris. Cette étape est facultative, et vous pouvez inviter des membres à rejoindre votre réseau plus tard dans le moniteur réseau.  Cliquez sur **Suivant**.
	Les membres que vous invitez vont recevoir une notification par courrier électronique relative à votre invitation une fois que vous avez effectué toutes les étapes de création du réseau.
3. Dans l'écran "Définir des règles de gouvernance", définissez des règles pour l'appartenance, la création de canal et le code blockchain. Par défaut, tous les membres du réseau peuvent inviter d'autres membres à rejoindre le réseau, créer des canaux et instancier du code blockchain. Actuellement, votre réseau utilise les règles de gouvernance par défaut.  Cliquez sur **Suivant**.
4. Dans l'écran "Vérifier le récapitulatif", vérifiez la configuration de votre réseau. Si vous voulez apporter des modifications, cliquez sur **Editer** en regard de l'en-tête de section ou cliquez sur le bouton **Précédent** pour revenir aux écrans précédents. Une fois la configuration réseau terminée, cliquez sur **Terminé**.  
5. Dans l'écran "Le réseau a été créé", vous êtes informé que la création de votre réseau a abouti. Vous pouvez cliquer sur **Ajouter [des homologues](glossary.html#peer)** pour configurer vos ressources réseau ou cliquer directement sur **Afficher le moniteur** pour ouvrir le moniteur réseau. Vous pourrez aussi ajouter des homologues plus tard dans le Moniteur réseau. Pour plus d'informations sur les homologues, voir [Ajout d'homologues](v10_dashboard.md#add_peers).

Vous avez maintenant déployé un réseau {{site.data.keyword.blockchain}} qui peut prendre en charge les ressources réseau suivantes :  
* Une autorité de certification spécifique aux membres
* Des règles de gouvernance par défaut
* Jusqu'à 15 membres de réseau  
* Trois programmes de tri et deux noeuds d'autorité de certification intermédiaires
* Jusqu'à trois petits homologues pour chaque membre  
* Un service de tri tolérant aux pannes
* Jusqu'à 150 canaux
* Jusqu'à 10 instanciations de codes blockchain


## Rejoindre un réseau
Comme pour la création d'un réseau, vous devez créer une instance de service de plateforme [{{site.data.keyword.blockchain}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) sur {{site.data.keyword.Bluemix_notm}}. Vous devez vous connecter avec votre ID {{site.data.keyword.Bluemix_notm}}. Si vous n'avez pas d'ID, cliquez sur le bouton **Inscrivez-vous pour créer**.  Renommez le service et les noms des données d'identification pour votre instance afin de pouvoir la reconnaître facilement dans le futur. Sélectionnez la région, l'organisation et l'espace {{site.data.keyword.Bluemix_notm}}, où vous pouvez déployer votre réseau {{site.data.keyword.blockchain}}. Ensuite, sélectionnez un **Plan d'appartenance d'entreprise** dans le tableau Plans de tarification et cliquez sur le bouton **Créer**.

Vous pouvez trouver votre instance de service de plateforme {{site.data.keyword.blockchain}} dans le tableau de bord du service [tableau de bord du service {{site.data.keyword.Bluemix_notm}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}").

Si vous êtes un membre de réseau invité, cliquez sur le bouton **Invitation en attente ->**, sélectionnez le réseau que vous voulez rejoindre dans la liste déroulante, puis cliquez sur le bouton **Rejoindre le réseau** . Suivez les instructions de l'assistant pour afficher la configuration de base de votre réseau et configurer vos propres ressources réseau.  
![Assistant pour rejoindre le réseau](images/join_network_name.png "Assistant pour rejoindre le réseau")  

1. Dans l'écran "Mise en route", entrez le nom de votre organisation et cliquez sur **Suivant**.
2. Dans l'écran "Examiner les règles de gouvernance", passez en revue les règles de gouvernance du réseau relatives à l'appartenance, à la création de canal et au code blockchain. Cliquez sur **Suivant**.
3. (Facultatif) Dans l'écran "Ajouter des homologues", choisissez le nombre d'homologues à ajouter. Cliquez sur **Suivant**. Chaque membre d'un réseau peut ajouter jusqu'à trois homologues dans un réseau. Cette étape est facultative, et vous pouvez ajouter vos homologues plus tard dans le moniteur réseau. Pour plus d'informations sur les homologues, voir [Ajout d'homologues](v10_dashboard.html#peers).
4. Dans l'écran "Vérification du récapitulatif du réseau", vérifiez la configuration du réseau. Si vous voulez apporter des modifications, cliquez sur le bouton **Précédent** pour revenir aux écrans précédents. Une fois la configuration des ressources terminée, cliquez sur **Terminé**. Vous serez informé que vous avez rejoint le réseau. Vous pouvez ensuite cliquer sur **Afficher le moniteur** pour ouvrir le moniteur réseau. 

<!-- or click **Create a Channel** to initiate a channel creation request. You can create channels later in the Network Monitor. For more information, see [Channels](v10_dashboard.html#channels).  -->


## Configuration des ressources réseau et de l'environnement

1. Accédez au moniteur réseau une fois que vous avez créé ou rejoint un réseau {{site.data.keyword.blockchain}}. Le moniteur réseau est un tableau de bord doté d'une interface graphique depuis lequel vous pouvez gérer et conserver un suivi des informations de statut réseau. Pour plus d'informations, voir [Moniteur réseau](v10_dashboard.html).
2. Ajoutez vos propres homologues au réseau. Si vous avez déjà ajouté suffisamment d'homologues, vous pouvez omettre cette étape. Les homologues exécutent du code blockchain et ils constituent le noeud final pour interagir avec vos applications. Cliquez sur **Ajouter des homologues** dans l'écran "Présentation", puis sélectionnez le nombre et la taille de vos homologues. Pour plus d'informations, voir [Présentation générale](v10_dashboard.html#resources).
3. Configurez un canal. Tous les membres du même canal sont mis à disposition avec un registre spécifique à un canal, lequel fournit l'isolement de données et la confidentialité. Pour plus d'informations sur la création d'un canal, voir [Création d'un canal](howto/create_channel.html#creating-a-channel).  
Si vous êtes un membre de canal qui a été invité à rejoindre un canal, vous allez recevoir une notification par courrier électronique avec un lien vers l'assistant qui va vous autoriser à rejoindre ce canal.

4. Joignez les homologues au canal.  Seuls les homologues qui sont associés au canal peuvent accéder à son registre. Pour plus d'informations, voir [Canaux](v10_dashboard.html#channels).
5. Installez et instanciez du code blockchain. Tous les membres de canal doivent installer le même code blockchain avec le même nom et la même version sur chaque homologue qui va exécuter le code blockchain. Une fois que vous avez installé le code blockchain, vous devez l'instancier sur un canal pour pouvoir l'utiliser. Pour plus d'informations, voir [Installation et instanciation d'un code blockchain](howto/install_instantiate_chaincode.html).  

**Remarque **: Pour atteindre une haute disponibilité, chaque organisation doit acheter au moins deux homologues, et au sein d'un canal, chaque membre participant doit rejoindre au moins deux homologues.


## Extraction des données d'identification réseau et du profil de connexion
Dès que vous avez créé un réseau de plan Enterprise dans {{site.data.keyword.cloud_notm}}, vous pouvez obtenir les données d'identification réseau et le profil de connexion depuis la page d'instance de service ou le Moniteur réseau. 

### Extraction à partir de la page d'instance de service
Vous accédez à la page d'instance de service dès que vous créez une instance de service. Vous pouvez également cliquer sur votre service dans le tableau de bord de service[{{site.data.keyword.cloud_notm}} - tableau de bord de service![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}") pour ouvrir votre page d'instance de service.

Pour extraire les données d'identification de votre service, procédez comme suit :
1. Sur la page d'instance de service, cliquez sur **Données d'identification du service** dans le navigateur de gauche pour afficher l'écran "Données d'identification du service".
2. Cliquez sur **Nouvelles données d'identification** dans l'écran "Données d'identification du service".
3. Dans l'écran "Ajouter de nouvelles données d'identification", donnez un nom aux données d'identification et cliquez sur **Ajouter**. Les nouvelles données d'identification sont ajoutées dans le tableau. Vous pouvez cliquer sur **Afficher les données d'identification** sous la colonne "ACTIONS" pour afficher les détails des données d'identification. Ces données d'identification contiennent la clé d'API et la valeur confidentielle, que vous pouvez utiliser pour autoriser les API.
    Si vous voulez voir le profil de connexion de votre connexion, entrez **{"legacy": true}** comme paramètre de configuration en ligne lors de la création de nouvelles données d'identification. Le profil de connexion contient des noeuds finaux d'API pour vos ressources réseau, que vous pouvez utiliser dans vos API et applications.

### Extraction dans le Moniteur réseau
Vous pouvez trouver les données d'identification réseau dans l'écran "API" de votre Moniteur réseau. Pour plus d'informations sur l'utilisation des API, voir [Test des API avec Swagger](apis.html).

Vous pouvez extraire le profil de connexion dans l'écran "Présentation" de votre Moniteur réseau. Cliquez sur le bouton **Profil de connexion** dans l'écran "Présentation" et le profil de connexion s'affiche dans une nouvelle page.


## Développement et déploiement de réseaux d'entreprise personnalisés
Vous pouvez développer des réseaux d'entreprise en fonction des besoins de votre activité à l'aide de l'environnement de développeur d'IBM Blockchain Platform: Develop et des outils de développeur Hyperledger Composer. Dès que vous avez développé un réseau pour votre activité, vous pouvez déployer votre réseau d'entreprise dans un réseau de plan Enterprise.

Pour plus d'informations, voir [Développement du réseau](develop.html) et [Déploiement d'un réseau d'entreprise dans un plan Enterprise](develop_enterprise.html).


## Activation des applications pour une interaction avec le réseau
Les applications optimisent les API SDK pour interagir avec vos ressources réseau {{site.data.keyword.blockchain}}. Vous devez ajouter les informations de noeud final d'API de vos ressources réseau dans votre application de sorte que celle-ci puisse finalement cibler vos homologues avec des demandes de transaction. Vous pouvez ensuite ajouter les informations de noeud final d'API depuis le moniteur réseau. Les applications peuvent être hébergées sur votre système de fichiers local ou sur {{site.data.keyword.Bluemix_notm}}. Pour plus d'informations, consultez [Développement d'applications](v10_application.html).

## Surveillance des ressources réseau  
Une fois qu'une transaction a été déclenchée depuis votre application, vous pouvez afficher les informations d'état de transaction dans le moniteur réseau. Pour plus d'informations sur la surveillance réseau, voir [Surveillance d'un réseau](howto/monitor_network.html).

## Quitter un réseau
Si vous voulez quitter un réseau, supprimez l'instance de service de blockchain de votre tableau de bord {{site.data.keyword.Bluemix_notm}}.  

**Remarque **: Avant de quitter un réseau, vérifiez que vous n'êtes membre d'aucun des canaux du réseau. Dans le cas contraire, vous recevrez des messages lors de la sortie du réseau. Un retrait de membre de canal doit mettre fin au processus de mise à jour de canal. Pour plus d'informations sur le processus de mise à jour de canal, voir [Mise à jour d'un canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

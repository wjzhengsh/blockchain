---

copyright:
  years: 2018
lastupdated: "2018-05-23"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Gouvernance d'un réseau de plan Starter
{: #getting-started-with-starter-plan}

Le plan Starter de la plateforme {{site.data.keyword.blockchainfull}} vous permet d'accéder d'un simple clic à un réseau de blockchain préconfiguré. Il <!--offers you a free trial of 30 days and -->met à disposition un réseau privé avec la configuration de deux [organisations](glossary.html#organization), un [homologue](glossary.html#peer) par organisation, et un [canal](glossary.html#channel) par défaut. Une fois le réseau créé, vous pouvez le dimensionner et lui ajouter d'autres organisations et homologues. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**Remarques **:
1. Le plan Starter de la plateforme {{site.data.keyword.blockchainfull}} est un environnement de tests et de développement, et il ne convient pas pour des charges de travail de production. Si vous avez besoin d'un environnement de production, consultez la section [A propos du plan Enterprise](enterprise_plan.html).
2. Le plan Starter en est à sa version bêta et il n'est disponible que dans la région **Sud des Etats-Unis** de {{site.data.keyword.cloud_notm}}.

Le plan Starter vous permet d'apprendre et de développer des compétences avec la plateforme {{site.data.keyword.blockchainfull_notm}}, d'exécuter des modèles d'application, de tester vos propres applications, et de simuler un scénario composé de plusieurs organisations.  Ce tutoriel de mise en route présente les prérequis et les étapes que vous devrez suivre pour créer et utiliser un réseau de plan Starter.

Si vous êtes un nouvel utilisateur de la plateforme {{site.data.keyword.blockchainfull_notm}} et de la technologie blockchain, vous pouvez consulter le [Glossaire](glossary.html) afin de vous familiariser avec les termes utilisés dans la présente documentation et la [documentation Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html) pour en savoir plus sur la blockchain.


## Création d'un réseau
Vous pouvez disposer d'un [réseau](glossary.html#network) de plan Starter avec la configuration par défaut dès que vous avez créé une instance de service de plateforme {{site.data.keyword.blockchainfull_notm}}.

1. Localisez le [service blockchain![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) dans le catalogue {{site.data.keyword.cloud_notm}}.   
    **Remarque **: Vous devez vous connecter avec votre compte payant {{site.data.keyword.cloud_notm}}. Si vous n'avez pas de compte, cliquez sur le bouton **Inscrivez-vous pour créer**. Après que vous avez créé un compte d'essai gratuit, effectuez une mise à niveau vers un type **Paiement à la carte** en accédant à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, et en cliquant sur **Ajouter carte de crédit**.
2. Assurez-vous de choisir la région **Sud des Etats-Unis** dans {{site.data.keyword.cloud_notm}}.
3. Sélectionnez votre organisation et votre espace Cloud Foundry, où vous allez créer votre réseau.
4. Choisissez **Plan d'appartenance Starter** dans le tableau des plans de tarification.
5. Cliquez sur le bouton **Créer**. Notez que si vous êtes invité à rejoindre un réseau, vous pouvez voir une fenêtre de bienvenue. Pour créer un réseau, choisissez **Poursuivez avec votre réseau** et cliquez sur **Continuer**. Pour rejoindre un réseau, reportez-vous à l'étape 5 de la section [Rejoindre un réseau](#joining-a-network).
  Vous êtes maintenant prêt à utiliser votre plan Starter avec la configuration par défaut. Le réseau se lance avec un programme de tri (appelé service de tri "SOLO"), deux organisations, une AC et un homologue par organisation. Un canal par défaut est également créé.
6. Cliquez sur le bouton **Lancer**.

Vous pouvez trouver votre instance de service de blockchain dans votre tableau de bord du serviceservice instance in your [tableau de bord du service {{site.data.keyword.Bluemix_notm}}![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}").


## Invitation de membres
Vous pouvez inviter d'autres [organisations](glossary.html#organization) à rejoindre votre réseau de plan Starter en tant que [membres](glossary.html#member), de manière à pouvoir [effectuer des transactions](glossary.html#transaction). En outre, si vous souhaitez utiliser le plan Starter Plan à des fins de formation et de tests, vous pouvez simuler un réseau composé de plusieurs organisations en ajoutant vous-même des membres au réseau.

1. A l'écran "Membres' de votre Moniteur réseau, cliquez sur le bouton **Inviter des membres**.
2. La fenêtre "Inviter un membre" s'affiche.
    - Si vous voulez inviter une autre organisation, choisissez "Inviter un membre".  Indiquez le nom et l'adresse e-mail opérateur de l'organisation à inviter.  Vous pouvez également entrer des informations supplémentaires à inclure dans votre invitation dans la zone "Ajouter une note".  Cliquez sur le bouton **Envoyer une invitation**.  L'organisation invitée recevra un e-mail d'invitation et elle pourra suivre les instructions qui y figurent pour rejoindre votre réseau.
    - Si vous souhaitez ajouter d'autres organisations pouvant être ajoutés à un canal, choisissez "Ajouter un membre".  Entrez un nom pour votre nouvelle organisation. Vous pouvez éventuellement ajouter des homologues à votre nouvelle organisation ou le faire ultérieurement dans le Moniteur réseau.  Cliquez sur le bouton **Créer**. Notez que si vous ajoutez des homologues pour votre nouvelle organisation, vous devez basculer vers cette nouvelle organisation pour voir vos homologues. Pour plus d'informations sur le basculement entre les organisations, voir [Basculement entre les organisations](dashboard.html#switch-organizations).


## Rejoindre un réseau
Si vous êtes invité par un initiateur du réseau, vous recevrez une notification par courrier électronique avec des instructions sur la manière de se connecter au réseau. Suivez les instructions fournies dans le courrier électronique afin de devenir l'un des membres du réseau.

Vous devez créer une [ instance de service de plateforme {{site.data.keyword.blockchain}} ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) avec l'option d'appartenance du plan Starter dans {{site.data.keyword.cloud_notm}}.

1. Connectez-vous avec votre compte {{site.data.keyword.cloud_notm}}. Si vous n'avez pas de compte, cliquez sur le bouton **Inscrivez-vous pour créer**.
2. Sélectionnez l'organisation Cloud Foundry où vous voulez stocker votre réseau {{site.data.keyword.blockchain}}.
3. Sélectionnez **Plan d'appartenance Starter** dans le tableau des plans de tarification.
4. Cliquez sur le bouton **Créer**. La page d'instance de service affiche une page d'accueil en incrustation. Notez que vous pouvez choisir de rejoindre un réseau ou de continuer pour créer le votre. Pour créer un réseau, reportez-vous à l'étape 4 de la section [Création d'un réseau](#creating-a-network).
5. Dans l'écran de bienvenue, choisissez **Rejoindre un réseau existant**, sélectionnez le réseau à rejoindre dans liste déroulante, puis cliquez sur **Continuer**.

Vous pouvez trouver votre instance de service de blockchain dans le tableau de bord de service [{{site.data.keyword.cloud_notm}}tableau de bord de service![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}").

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
-->


## Extraction des données d'identification réseau et du profil de connexion
Dès que vous avez créé un réseau de plan Starter dans {{site.data.keyword.cloud_notm}}, vous pouvez obtenir les données d'identification réseau et le profil de connexion depuis la page d'instance de service ou le Moniteur réseau.

### Extraction à partir de la page d'instance de service
Vous accédez à la page d'instance de service dès que vous créez une instance de service. Vous pouvez également cliquer sur votre service dans le tableau de bord de service [{{site.data.keyword.cloud_notm}} - tableau de bord de service![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}}") pour ouvrir votre page d'instance de service.

Pour extraire les données d'identification de votre service, procédez comme suit :
1. Sur la page d'instance de service, cliquez sur **Données d'identification du service** dans le navigateur de gauche pour afficher l'écran "Données d'identification du service".
2. Cliquez sur **Nouvelles données d'identification** dans l'écran "Données d'identification du service".
3. Dans l'écran "Ajouter de nouvelles données d'identification", donnez un nom aux données d'identification et cliquez sur **Ajouter**. Les nouvelles données d'identification sont ajoutées dans le tableau. Vous pouvez cliquer sur **Afficher les données d'identification** sous la colonne "ACTIONS" pour afficher les détails des données d'identification. Ces données d'identification contiennent la clé d'API et la valeur confidentielle, que vous pouvez utiliser pour autoriser les API.  
    Si vous voulez voir le profil de connexion de votre connexion, entrez **{"legacy": true}** comme paramètre de configuration en ligne lors de la création de nouvelles données d'identification. Le profil de connexion contient des noeuds finaux d'API pour vos ressources réseau, que vous pouvez utiliser dans vos API et applications.

![Extraction des données d'identification réseau](images/service_credentials.gif "Extraction des données d'identification réseau")

### Extraction dans le Moniteur réseau
Vous pouvez trouver les données d'identification réseau dans l'écran "API" de votre Moniteur réseau. Pour plus de détails sur l'utilisation des API, voir [Interaction avec le réseau à l'aide d'API Swagger](howto/swagger_apis.html).

Vous pouvez extraire le profil de connexion dans l'écran "Présentation" de votre Moniteur réseau. Cliquez sur le bouton **Profil de connexion** dans l'écran "Présentation" et le profil de connexion s'affiche dans une nouvelle page.


## Déploiement de modèles d'application
Le plan Starter vous permet de déployer en quelques clics des modèles d'application sur votre réseau. A partir de ces modèles, vous pouvez aisément comprendre le fonctionnement du code blockchain et des applications dans un réseau.

Pour plus d'informations, voir [Déploiement de modèles d'application](howto/prebuilt_samples.html).


## Développement et déploiement de réseaux d'entreprise personnalisés
Le plan Starter intègre l'environnement développeur IBM Blockchain Platform: Develop et le jeu d'outils de développeur Hyperledger Composer . Vous pouvez développer votre réseau de blockchain en fonction des besoins de votre entreprise.  Dès que vous avez développé un réseau pour votre activité, vous pouvez déployer votre réseau d'entreprise dans un réseau de plan Starter.

Pour plus d'informations, voir [Développement du réseau](develop.html) et [Déploiement de réseaux d'entreprise avec le plan Starter](develop_starter.html).


## Surveillance des ressources réseau
Si votre application demande une transaction, vous pouvez afficher les informations d'état de transaction dans le Moniteur réseau. Pour plus d'informations sur la surveillance réseau, voir [Surveillance d'un réseau](howto/monitor_network.html).


## Réinitialisation d'un réseau
Si vous souhaitez nettoyer vos configurations personnalisées, les exécutions de code blockchain ou des applications déployées, vous pouvez réinitialiser votre réseau à sa configuration initiale par défaut.  Pour plus de détails, voir [Réinitialisation du réseau](dashboard.html#reset-network).


## Migration d'un plan Starter vers un plan Enterprise
{: #migrate}

Vous pouvez déployer un fichier `.bna`, du code blockchain, ainsi que des applications que vous testez sur un réseau de plan Starter dans un réseau de plan Enterprise.

Si vous disposez d'un fichier archive de réseau d'entreprise (`.bna`), suivez les instructions relatives au [déploiement d'un réseau d'entreprise dans un plan Enterprise](./develop_enterprise.html). Si vous n'avez pas de fichier `.bna`, utilisez la commande `composer network download` pour l'extraire de l'instance de plan Starter. Pour plus d'informations sur la commande `composer network download`, consultez la [documentation relative à la ligne de commande Hyperledger Composer![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger.github.io/composer/reference/commands){:new_window}.

Le code blockchain, qui est semblable aux fichiers `.bna`, est développé en externe. Pour déployer un code blockchain que vous testez depuis un réseau de plan Starter dans un réseau de plan Enterprise, suivez les instructions de la section [Installation, instanciation et mise à jour d'un code blockchain](howto/install_instantiate_chaincode.html#installchaincode).

Comme vous pouvez le voir dans la section [Déploiement de modèles d'application](howto/prebuilt_samples.html), le plan Starter simplifie l'obtention d'un modèle d'application intégré à votre réseau à l'aide de Toolchain. Cette configuration permet également une intégration continue par la mise à jour automatique de votre modèle d'application chaque fois que votre référentiel d'application dévié est modifié. Si vous voulez déployer cette application dans un réseau de plan Enterprise, vous pouvez copier votre référentiel d'application dévié dans un nouveau référentiel, puis suivre les instructions de la section [Déploiement de modèles d'application manuellement](howto/prebuilt_samples.html#deploy_sample_applications_manually).


## Suppression ou sortie d'un réseau
Si vous voulez supprimer un réseau ou le quitter, vous pouvez supprimer l'instance de service de blockchain de votre tableau de bord {{site.data.keyword.cloud_notm}}.

**Remarque **: Avant de quitter un réseau, vérifiez que vous n'êtes membre d'aucun des canaux du réseau. Dans le cas contraire, vous recevrez des messages lors de la sortie du réseau. Un retrait de membre de canal doit mettre fin au processus de mise à jour de canal. Pour plus d'informations sur le processus de mise à jour de canal, voir [Mise à jour d'un canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Déploiement de modèles d'application
{: #overview}

La plateforme {{site.data.keyword.blockchainfull}} fournit des modèles d'application que vous pouvez déployer et tester, de manière à avoir une meilleure compréhension des réseaux de blockchain et du développement d'application.
{: shortdesc}

Dès que vous avez mis à disposition un réseau de plan Starter, vous pouvez déployer les modèles d'applications dans le Moniteur réseau, ce qui automatise les étapes qui permettent l'exécution des modèles d'application sur votre réseau. Vous pouvez également activer les modèles étape par étape pour connaître l'intégralité du processus de déploiement d'application, qui vous devez suivre lorsque vous déployez vos propres applications.

## Déploiement de modèles d'application dans le plan Starter

Le plan Starter fournit une approche simple pour le déploiement de modèles d'applications par l'optimisation du service Toolchain sur {{site.data.keyword.cloud_notm}} en quelques clics seulement. Dès que vous avez déployé et lancé un modèle d'application, celui-ci s'exécute automatiquement sur votre réseau de blockchain.

Le plan Starter fournit deux modèles d'application pour commencer.

* **Billes**

  Le modèle Billes permet aux utilisateurs de créer des billes virtuelles avec différentes propriétés et de les transférer à d'autres utilisateurs. Pour plus d'informations sur le modèle Billes, voir [Démo Billes ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM-Blockchain/marbles).

<!--
* **Perishable Goods**

  The Perishable Goods sample enables users to deploy legal contracts that purchase goods in a supply chain based on delivery and temperature readings. For more information about this sample, see [Perishable Goods ![External link icon](../images/external_link.svg "External link icon")](https://github.com/clauseHQ/demo-clause-ibm-perishable-goods).

-->

* **Fabrication de véhicules**

  Le modèle Fabrication de véhicules permet aux utilisateurs de parcourir tout le cycle de vie d'un véhicule virtuel. Pour plus d'informations sur ce modèle, voir [Fabrication de véhicules![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM-Blockchain/vehicle-manufacture).

Pour déployer un modèle d'application, procédez comme suit :

1. Accédez au **Moniteur réseau** de votre réseau de plan Starter. Si vous n'en avez pas, consultez la section [Création d'un réseau](../get_start_starter_plan.html#creating-a-network).

2. Affichez l'écran "Essayer les modèles" dans votre Moniteur réseau. Sélectionnez le modèle d'application que vous voulez déployer, puis cliquez sur **Deployer via Toolchain**.
<!--
    ![sampleappflow0](../images/sampleappflow0.png)
-->
3. Une fenêtre de configuration du service Toolchain s'ouvre. Vérifiez que tous les outils requis sont correctement intégrés. Notez que si vous avez plusieurs organisations, vous devez vous assurer d'entrer le nom d'organisation correct. Le nom de l'organisation doit être l'adresse électronique que vous utilisez pour vous connecter au réseau.
    **Conseil **: Vous devez désactiver les logiciels de blocage d'incrustation afin que la page configuration de service Toolchain puisse s'ouvrir.
<!--
    ![sampleappflow1](../images/sampleappflow1.png)
-->
  S'il s'agit de la première application que vous déployez via Toolchain, vous devez autoriser Toolchain à accéder au référentiel GitHub.

  ![sampleappflow2](../images/sampleappflow2.png)

  Dès que vous cliquez sur le bouton "Autoriser", vous êtes redirigé vers GitHub. Si ne disposez pas d'un compte GitHub, vous devez en créer un. Entrez vos informations de compte afin d'accorder l'accès à vos référentiels à Toolchain. Si vous ne voulez pas accorder cet accès à Toolchain, vous pouvez déployer le modèle d'application manuellement. Pour plus d'informations, voir [Déploiement de modèles d'application manuellement](#deploy_sample_applications_manually).

5. Cliquez sur le bouton **Créer** dans la partie inférieure de la page Toolchain. Cette opération doit vous ramener au Moniteur réseau, dans lequel le déploiement de l'application Billes doit être en cours. Ce processus doit prendre entre 5 et 10 minutes.
<!--
    ![sampleappflow3](../images/sampleappflow3.png)
-->
Une fois le déploiement terminé, vous pouvez commencer à utiliser le modèle Billes sur votre réseau de plan Starter.

Etant donné que ce processus crée un référentiel GitHub dévié auquel vous avez accès et dont vous avez le contrôle, vous pouvez apporter des modifications au modèle Billes dans le référentiel dévié et les valider. Ces validations déclenchent une génération automatique de votre application Billes et vous permettent de lancer une démonstration dans {{site.data.keyword.cloud_notm}}.

## Déploiement de modèles d'application manuellement
{: #deploy_sample_applications_manually}

Si vous souhaitez déployer des modèles d'application sans utiliser le Moniteur réseau, vérifiez que vous avez installé tous les logiciels prérequis sur votre système de fichiers local. Pour plus d'informations, voir [Configuration de l'environnement de développement d'une application](../v10_application.html#setting-up-application-development-environment).

Vous devez également avoir un réseau de blockchain sur {{site.data.keyword.cloud_notm}} avec un plan Starter ou un plan Enterprise, et vous devez configurer un canal et ses homologues. Pour plus d'informations, voir [Gouvernance d'un réseau de plan Starter](../get_start_starter_plan.html) et [Gouvernance d'un réseau de plan Enterprise](../get_start.html). Une fois que vous avez mis un réseau à disposition et que vous pouvez y déployer des applications, procédez à l'extraction des noeuds finaux d'API des ressources de votre réseau auxquelles votre application accédera. Pour plus d'informations, voir [Ajout de noeuds finaux d'API à votre application](../v10_application.html#adding-network-api-endpoints-to-your-application).

Vous pouvez déployer l'un des modèles d'applications suivants sur votre réseau :

- **Billes**

  Dans cette application, plusieurs utilisateurs peuvent créer des billes dotées de propriétés différentes et les transférer entre eux. L'application Billes est écrite en JavaScript et le code blockchain est écrit en Go.

  Vous pouvez trouver un exemple de code et des instructions dans [Marbles in GitHub ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM-Blockchain/marbles).

  Utilisez les instructions Bluemix au lieu des instructions d'hébergement du modèle Billes en local et saisissez les informations appropriées de votre réseau. Notez que les captures d'écran dans le GitHub Marbles indiquent l'interface utilisateur du plan Enterprise (car le plan Enterprise prend uniquement en charge le chemin d'accès manuel pour le déploiement du modèle Billes), qui est un peu différente de l'interface utilisateur du plan Starter. Néanmoins les deux interfaces comportent les mêmes éléments de base et vous pouvez voir les noms de vos homologues, canaux et autres informations de données d'identification du service dans les écrans appropriés.

- **Fabcar**

  Dans Fabcar, vous pouvez exécuter des **requêtes** et des **mises à jour de registre** sur les enregistrements de véhicule dans le registre. L'application Fabcar est écrite en JavaScript et le code blockchain est écrit en Go.

  Vous pouvez trouver un exemple de code dans [Fabric car in GitHub ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) et des instructions dans [Writing Your First Application ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

- **Autres applications**

  Pour plus d'informations sur l'hébergement de vos applications dans {{site.data.keyword.cloud_notm}}, voir [Hébergement d'applications](../v10_application.html#hosting-applications).

## Suppression d'un modèle d'application

Pour supprimer un modèle d'application qui a été acquis via le processus Toolchain, accédez à l'emplacement du modèle dans l'interface utilisateur. Etant donné que les modèles d'application sont instanciés sur un canal, vous pouvez trouver le modèle dans des canaux. Cliquez sur **Canaux** dans la navigation de gauche afin d'afficher l'écran "Canaux". Cliquez sur le canal dans lequel le modèle est instancié, puis sur **Code blockchain**. Le code blockchain instancié dans ce canal s'affiche.

Si vous cliquez sur le code blockchain de votre application, vous pouvez voir un onglet **Supprimer**. Toutefois, le fait de cliquer seulement sur **Supprimer** ne permet pas de supprimer le modèle d'application, mais de supprimer uniquement le conteneur de code blockchain.  Vous devez également accéder au tableau de bord {{site.data.keyword.cloud_notm}} et au tableau de bord Toolchain pour supprimer le modèle à ces emplacements.

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

# Modèles d'application

Vous pouvez déployer et tester des modèles d'application afin d'avoir une meilleure compréhension du réseau {{site.data.keyword.blockchainfull}} et du développement d'application.
{:shortdesc}

## Avant de commencer

Vérifiez que vous avez installé tous les logiciels prérequis sur votre système de fichiers local.  Pour plus d'informations, voir [Configuration de l'environnement de développement d'une application](../v10_application.html#setting-up-application-development-environment).

Vous devez aussi disposer d'un réseau {{site.data.keyword.blockchain}} sur {{site.data.keyword.Bluemix_short}} avec un canal et ses homologues configurés.  Pour plus d'informations, voir [Gouvernance du réseau](../get_start.html).  Lorsque votre réseau est prêt à être utilisé, procédez à l'extraction des noeuds finaux d'API des ressources de votre réseau auxquelles votre application accédera.  Pour plus d'informations, voir [Ajout de données d'identification de service réseau à votre application](../v10_application.html#adding-network-service-credentials-to-your-application).


## Application Marbles

Dans cette application, plusieurs utilisateurs peuvent créer des billes dotées de propriétés différentes et les transférer entre eux.  L'application Marbles est écrite en Javascript et le code blockchain est écrit en Go.

Vous pouvez trouver un exemple de code et des instructions dans [Marbles in GitHub ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/IBM-Blockchain/marbles).


## Application Fabric car

Dans cette application, vous pouvez présenter une **requête** et **mettre à jour** des enregistrements de voiture dans le registre.  L'application Fabric car est écrite en Javascript et le code blockchain est écrit en Go.

Vous pouvez trouver un exemple de code dans [Fabric car in GitHub ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) et des instructions dans [Writing Your First Application ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

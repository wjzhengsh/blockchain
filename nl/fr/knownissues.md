---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-29"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Problèmes connus

Les problèmes suivants ont été signalés :
- La configuration d'une autorité de certification externe n'est pas encore prise en charge.  Vous pouvez dans ce cas générer et envoyer par téléchargement des certificats admin via le moniteur réseau. Pour plus d'informations, consultez la section [Génération de certificats côté client](v10_application.html#generating-the-client-side-certificates) ainsi que la description figurant sous l'onglet ["Certificats" de l'écran "Membre"](v10_dashboard.html#members) dans le Moniteur réseau.
- Dans le Moniteur réseau d'un réseau du plan Starter, lorsque vous cliquez sur **Afficher les journaux** sur les noeuds qui sont répertoriés à l'écran "Présentation", l'interface kibana {{site.data.keyword.cloud}} Logging s'ouvre. Par défaut, kibana est préconfigurée pour afficher les journaux des 15 dernières minutes d'activité. S'il n'y a pas d'activité au cours des 15 dernières minutes, un message indique 'Aucun résultat trouvé'. Pour afficher tous les journaux, vous pouvez cliquer sur l'icône temporisateur dans l'angle supérieur droit sous votre nom d'utilisateur et définir un intervalle de temps plus large, comme 'cette semaine' ou 'ce mois'.
- Vous pouvez constater des problèmes de stabilité, comme des proxy défaillants, et vous devez alors effectuer un redémarrage. Avant cela, essayez de remplacer les valeurs de délai par défaut dans les kits SDK Fabric de vos applications par des nombres plus élevés. Pour plus d'informations sur la définition des valeurs de délai, voir [Définition de valeurs de délai dans les kits SDK Fabric](v10_application.html#set-timeout-in-sdk).

Pour obtenir de l'aide sur votre réseau de plateforme {{site.data.keyword.blockchainfull_notm}} sous {{site.data.keyword.Bluemix}}, consultez [Support](ibmblockchain_support.html).

<!--
## Updating chaincode with Enterprise Plan migration to Hyperledger Fabric 1.1
-	Users who migrate from networks based on Hyperledger Fabric 1.0 to networks based on Fabric 1.1 will need to update the dependencies in their chaincode. If they do not, there is a risk of a service disruption.
- This does not apply to users that uploaded their chaincode without dependencies, using a .go file.
- **Update your chaincode using the following steps:**
  **1.** You can use any  golang vendoring tool to update your chaincode. It will be easiest to use the same tool that was used to include dependencies in the original file. Many early Fabric samples used the govendor tool. If your chaincode used govendor, you can update your dependencies using the following command in the directory above the vendor folder.
      govendor update all +v
  * You can use `go build` to check that the new code compiles and that the update worked.
  * You can test your chaincode by installing and instantiating it on a Starter Plan Network. All chaincode that works on Starter Plan will also work on Enterprise Plan after the upgrade.
  **2.** Once your chaincode dependencies are up to date, you can use the network monitor to [update your chaincode](install_instatiate_chaincode.html#Updating a chaincode).
-->

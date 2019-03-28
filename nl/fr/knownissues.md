---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Problèmes connus
{: #known-issues}

Cette page décrit les problèmes connus que vous pouvez rencontrer lors de l'utilisation des plans Starter ou Enterprise.
{:shortdesc}

Les problèmes suivants ont été signalés :
- **La configuration d'une autorité de certification externe n'est pas encore prise en charge**. Vous pouvez dans ce cas générer et envoyer par téléchargement des certificats admin via le moniteur réseau. Pour plus d'informations, consultez la section [Génération de certificats côté client](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel) ainsi que la description figurant sous l'onglet ["Certificats" de l'écran "Membre"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) dans le Moniteur réseau.
- Dans le Moniteur réseau d'un réseau du plan Starter, lorsque vous cliquez sur **Afficher les journaux** sur les noeuds répertoriés à l'écran "Présentation", l'interface kibana {{site.data.keyword.cloud}} Logging s'ouvre. **Par défaut, Kibana est préconfigurée pour afficher les journaux des 30 derniers jours d'activité**. Si aucune activité n'est intervenue dans les 30 derniers jours, un message indique *Aucun résultat trouvé*. Pour afficher tous les journaux, vous pouvez cliquer sur l'icône temporisateur dans l'angle supérieur droit sous votre nom d'utilisateur et définir un intervalle de temps plus large, comme *date année*.
- Les journaux de votre réseau du plan Starter sont collectés par le service [{{site.data.keyword.cloud_notm}} Log Analysis ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/log-analysis). Par défaut, les journaux sont collectés par le plan Lite du service Log Analysis. Ce plan est gratuit et **ne vous permet d'effectuer une recherche que sur les premiers 500 Mo de vos journaux par jour**. Si les journaux de votre réseau dépassent 500 Mo, vous ne pouvez pas visualiser de nouveaux journaux dans Kibana. Si votre réseau génère plus de 500 Mo de journaux, vous pouvez effectuer une mise à niveau vers une version payante du service Log Analysis.
- Etant donné que le plan Starter n'est pas destiné à un environnement de production, **il se peut que des applications ne puissent pas accéder immédiatement à une ressource réseau**.
  - Si ceci se produit, il est recommandé en premier lieu d'augmenter les valeurs de délai d'attente dans le SDK Fabric. Pour plus d'informations sur la définition des valeurs de délai, voir [Définition de valeurs de délai dans les kits SDK Fabric](/docs/services/blockchain/v10_application.html#dev-app-set-timeout-in-sdk).
  - Vous pouvez également relancer votre demande au niveau de l'application.
- **Les conteneurs de Code blockchain peuvent parfois être arrêtés** par un problème réseau en arrière-plan et doivent éventuellement être régénérés et redémarrés après avoir été appelés par un utilisateur. Si ceci se produit, votre code blockchain peut demander quelques minutes pour répondre.
- En raison de la limitation des ressources dans le réseau du plan Starter, (à savoir 1 UC et 4 Go de RAM pour chaque homologue), **vous pourriez rencontrer une erreur `REQUEST_TIMEOUT` lors de l'instanciation du code blockchain**. Dans ce cas, relancez l'étape d'instanciation. Si l'erreur persiste, vous pouvez augmenter le délai d'attente de l'instanciation du code blockchain. Dans le profil de connexion, le délai d'attente d'instanciation du code blockchain est fixé à 300 secondes.
  - Si vous utilisez la valeur par défaut dans le SDK, copiez la section **client** du profil de connexion comme indiqué ci-dessous afin de définir le délai d'attente à 300 secondes et de vous assurer que votre SDK puisse le lire. Notez que pour Node SDK, ce paramètre de délai d'attente dans le profil de connexion affecte tous les appels, tels que `invoke`, `queries`, etc.
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - Si vous remplacez le délai d'attente des commandes d'instanciation du code blockchain dans vos SDK, rétablissez la valeur par défaut ou fixez-la à 300 secondes ou plus.
    - Si vous utilisez Node SDK, vous pouvez modifier le paramètre de délai d'attente de la méthode `sendInstantiateProposal(request, timeout)`. Pour plus d'informations, voir [sendInstantiateProposal(request, timeout) ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal).
    - Si vous utilisez le SDK Java, vous pouvez modifier le paramètre de délai d'attente de la commande `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)` en éditant le fichier `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java`.

Pour obtenir de l'aide sur votre réseau {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}}, consultez [Support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

## Mise à jour du code blockchain pour mise à niveau de réseau du plan Enterprise
{: #known-issues-ibp-about-chaincode-migration}

Si votre réseau du plan Enterprise est au niveau Hyperledger Fabric V1.0, {{site.data.keyword.blockchainfull_notm}} Platform planifiera une mise à niveau pour migrer le réseau vers Hyperledger Fabric V1.1. Après la mise à niveau du réseau, si vous utilisez un code blockchain complexe avec des dépendances, vous devrez mettre à jour les dépendances dans votre code blockchain. Sinon, vous risquez de rencontrer une interruption de service.

**Remarques **:
- Si vous utilisez un code blockchain avec un fichier `.go` ou `.js`, vous n'avez pas besoin de mettre à jour votre code blockchain.
- Il se peut que votre code blockchain ne fonctionne pas sur votre ancien réseau ; par conséquent, vous devez mettre à jour votre code blockchain après la mise à jour planifiée du réseau.
- Vous pouvez tester votre code blockchain en l'installant et en l'instanciant dans un réseau du plan Starter. Tout code blockchain qui fonctionne avec le plan Starter fonctionnera également avec le plan Enterprise après la mise à niveau du réseau.

Procédez comme suit pour mettre à jour votre code blockchain :
1. Utilisez n'importe quel outil de l'éditeur Golang pour mettre à jour votre code blockchain. Le plus simple est d'utiliser le même outil que celui utilisé pour inclure des dépendances dans le fichier d'origine. Par exemple, si votre code blockchain utilise l'outil **govendor** exploité dans beaucoup d'exemples Fabric, vous pouvez exécuter la commande suivante dans le répertoire au-dessus du dossier du fournisseur pour mettre à jour vos dépendances du code blockchain :
    ```
    govendor update all +v
    ```
    {:codeblock}

    Vous pouvez ensuite utiliser la commande `go build` pour vérifier que le nouveau code peut être compilé et si la mise à jour du code blockchain fonctionne.

2. Après la mise à niveau de votre réseau, vous pouvez [mettre à jour votre code blockchain](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) depuis le Moniteur réseau.

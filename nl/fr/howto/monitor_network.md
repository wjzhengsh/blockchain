---

copyright:
  years: 2018,2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Surveillance d'un réseau de blockchain
{: #monitor-blockchain-network}

Ce tutoriel vous explique comment afficher et surveiller les informations de statut de votre réseau {{site.data.keyword.blockchain}} sur {{site.data.keyword.cloud_notm}}.
{:shortdesc}


## Surveillance des homologues, des services de tri et des autorités de certification
{: #monitor-blockchain-network-monitor-nodes}

Vous pouvez lancer une requête HTTP **HEAD** sur l'un de vos noeuds réseau pour vérifier son statut. Il peut s'agir d'un homologue, d'un service de tri ou d'une autorité de certification dans votre réseau Blockchain. Une requête **HEAD** est similaire à une requête GET et n'envoie que les en-têtes sans le corps de la requête. Si le noeud fonctionne normalement, une réponse 200 est renvoyée.

1. Depuis l'écran "Présentation" du Moniteur réseau, cliquez sur **Profil de connexion**. Vous pouvez ensuite cliquer sur **JSON brut** pour afficher le profil de connexion dans votre navigateur Web ou bien cliquer sur **Télécharger** pour enregistrer le profil de connexion sur le poste local.
2. Dans le profil de connexion, identifiez les informations d'URL du noeud que vous désirez vérifier. Par exemple, l'URL du service de tri `fabric-orderer-20190b` est `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
    ![Exemple d'URL du service de tri](../images/orderer_url.png "Exemple d'URL du service de tri")
3. Remplacez **grpcs** par **https** dans l'URL. Dans l'exemple ci-dessus, l'URL devient `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Lancez la requête **HEAD** sur l'URL avec un outil tel que l'application curl ou Chrome Postman.
    - Si une réponse avec statut 200 est renvoyée, les noeuds de votre réseau fonctionnent normalement.
    - Si la requête **HEAD** échoue avec une erreur de connexion, il se peut que votre noeud réseau ne soit pas en opération, que l'URL du noeud soit erronée ou encore qu'un pare-feu bloque l'accès au noeud.  Il vous faut résoudre cette erreur faute de quoi vos applications ne pourront pas se connecter au noeud.

L'exemple suivant illustre une requête **HEAD** avec une réponse 200 dans curl. Notez que vous pouvez ignorer l'erreur grpc puisque la requête HTTP **HEAD** vérifie si le noeud est accessible. Si la réponse est positive, la requête grpc sur le noeud fonctionne également dans votre application.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```

L'exemple suivant illustre une requête **HEAD** avec une erreur de connexion dans curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```

La figure suivant illustre une requête **HEAD** avec une réponse 200 dans l'application Chrome Postman.

  ![Exemple de requête HEAD dans Postman](../images/orderer_head_postman.png "Exemple de requête HEAD dans Postman")

## Utilisation de vos journaux réseau
{: #monitor-blockchain-network-using-logs}

L'écran "Présentation" de votre Moniteur réseau affiche l'érat de votre autorité de certification, de votre service de tri et de vos homologues. Cliquez sur **Afficher les journaux** dans la liste déroulante sous l'en-tête **Actions** afin de consulter les journaux d'un composant réseau spécifique. Si vous utilisez des réseaux de plan Enterprise, vous pouvez afficher les journaux de composant dans un format de fichier texte. Si vous utilisez des réseaux de plan Starter, les journaux de composant sont regroupés par le [{{site.data.keyword.cloud_notm}} service Log Analysis ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/log-analysis) et vous pouvez consulter les journaux dans [Kibana](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-viewing-kibana-logs).

Chaque composant génère des journaux provenant de différentes activités car chaque composant joue des rôles différents au sein de l'[architecture réseau ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) et des [flux de transaction ![Icône de lien externe](../images/external_link.svg "Icône de lien externe") d'Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html).

- **Journaux de l'autorité de certification**
  L'autorité de certification gère l'identité des participants au sein du réseau. Dans les journaux de l'autorité de certification, vous pouvez trouver des journaux lorsque des participants génèrent des clés publiques et privées pour communiquer avec le réseau (inscription), ou lorsque de nouveaux membres, homologues ou applications s'enregistrent auprès de l'autorité de certification. Vous pouvez également utiliser les journaux de l'autorité de certification pour déboguer en cas de problèmes avec la vérification de certificat.

- **Journaux du service de tri**
  Le service de tri est le composant de liaison commun du réseau de blockchain. Toutes les propositions de transaction approuvées des homologues, des mises à jour de canal, ou des mises à jour de l'appartenance au réseau sont envoyés au service de tri à des fins de vérification. Par conséquent, le service de tri contient des journaux dès le démarrage du réseau. Il contient également les journaux d'une transaction qui a été rejetée car il n'a pas été correctement validée par les organisations appropriées. Vous pouvez également trouver des journaux à partir desquels des canaux sont créés ou mis à jour, ou lorsqu'une mise à jour de canal échoue.

- **Journaux des homologues**  
  Ces journaux contiennent les résultats relatifs à l'installation, l'instanciation et l'appel de code blockchain. Vous pouvez rechercher le nom et la version d'un code blockchain pour trouver les journaux d'un code blockchain particulier. Vous pouvez également consulter les journaux d'un code blockchain spécifique à partir de la [section relative au code blockchain du moniteur de canal](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-channel-cc). Les messages, générés par vos propositions de transaction, ou les problèmes de dépassement de délai au niveau de vos demandes de proposition, figurent dans vos journaux d'homologue. Les journaux de l'homologue contiennent également des erreurs de transactions qui ont été rejetées car elles ne respectent pas les [règles de validation du code blockchain](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-endorsement-policy). Vous pouvez également trouver les résultats des demandes d'association de canal.

Hyperledger Fabric fournit différents [niveaux de journalisation![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/logging-control.html "contrôle de journalisation") en fonction de la gravité du message. Le niveau de journalisation sur {{site.data.keyword.blockchainfull_notm}} Platform est `INFO`. Pour afficher des journaux supplémentaires, vous pouvez ouvrir un [ticket de demande de service](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases) pour définir le niveau de journalisation sur le niveau plus prolixe `DEBUG`. Sachez que les journaux de niveau `DEBUG` contiennent une quantité importante de messages Gossip que vous devrez peut-être filtrer. Recherchez `warning` ou `error` dans vos messages afin de détecter des problèmes relatifs aux composants Hyperledger Fabric. Pour détecter si le conteneur de composant échoue ou est arrêté, recherchez des messages `panic` ou `killed` envoyés par {{site.data.keyword.cloud_notm}}.

## Affichage des journaux dans Kibana dans le plan Starter
{: #monitor-blockchain-network-viewing-kibana-logs}

Les journaux de votre réseau du plan Starter sont collectés par le [{{site.data.keyword.cloud_notm}} service Log Analysis![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://cloud.ibm.com/catalog/services/log-analysis "service Log Analysis"). Par défaut, les journaux sont collectés par le plan Lite du service Log Analysis. Ce plan est gratuit et **stocke vos journaux pendant trois jours** avant de les supprimer. Il vous permet de **rechercher uniquement les premiers 500 Mo de vos journaux par jour**. Si les journaux de votre réseau dépassent 500 Mo, vous ne pouvez pas visualiser de nouveaux journaux dans Kibana. Si votre réseau génère plus de 500 Mo de journaux, ou si vous voulez conserver vos journaux plus de trois jours, vous pouvez effectuer une mise à niveau vers une version payante du service Log Analysis.

A l'écran "Présentation" de votre Moniteur réseau, cliquez sur **Afficher les journaux** dans la liste déroulante sous l'en-tête **Actions** afin d'ouvrir chacun des journaux des composants réseau dans l'interface Kibana. Lorsque l'insterface Kibana s'ouvre, elle affiche les journaux qui sont filtrés par une barre de recherche dans sa partie supérieure. Par exemple, lorsque vous cliquez pour afficher vos journaux d'homologue, la recherche est filtrée par ID réseau et ID homologue : `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939" && NODE_NAME_str:"org1-peer1"`. Vous pouvez entrer une zone supplémentaire dans la barre de recherche si vous souhaitez visualiser d'autres journaux spécifiques. Par exemple, vous pouvez ajouter `&& "marbles"` pour afficher les journaux du code blockchain `"marbles"`. La suppression d'un terme de composant spécifique et la recherche uniquement avec l'ID réseau, par exemple, `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939"`, permet d'afficher les journaux de tous les composants réseau.

Vous pouvez utiliser le bouton de plage horaire dans l'angle supérieur droit pour changer la période d'affichage des journaux. Vous pouvez également utiliser l'onglet sur le côté gauche de l'écran pour ajouter et retirer des zones de la recherche. La zone la plus importante à afficher est la zone de message. Il peut être utile de rechercher avec un message sans l'horodatage afin de trouver toutes les instances de ce journal des messages. Cliquez sur le bouton **Sauvegarder** votre recherche et revenir à une vue spécifique. Pour plus d'informations sur l'affichage de données dans Kibana, consultez le manuel [Kibana User Guide ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://www.elastic.co/guide/en/kibana/6.2/index.html "Kibana User Guide"). Vous pouvez également [télécharger vos journaux](https://cloud.ibm.com/docs/services/CloudLogAnalysis/how-to/manage-logs/downloading_logs_cloud.html#downloading_logs) sur votre système de fichiers local à l'aide de l'interface CLI de Log Analysis.

**Remarque :** Par défaut, Kibana est préconfigurée pour afficher les journaux des 30 derniers jours d'activité. Si aucune activité n'est intervenue dans les 30 derniers jours, un message indique *Aucun résultat trouvé*. Pour afficher tous les journaux, vous pouvez cliquer sur l'icône temporisateur dans l'angle supérieur droit sous votre nom d'utilisateur et définir un intervalle de temps plus large, comme *date année*.

## Surveillance des canaux
{: #monitor-blockchain-network-monitor-channnels}

Accédez au moniteur réseau et localisez le canal que vous voulez afficher et surveiller dans l'écran "Canal". Dans l'écran de canal spécifique, vous pouvez consulter les informations de statut des données, les membres et le code blockchain instancié de ce canal sous trois onglets :

### Vue d'ensemble du canal
{: #monitor-blockchain-network-monitor-channel-overview}

L'onglet "Vue d'ensemble du canal" affiche les informations de bloc concernant ce canal :
  * Série de points de données incluant le nombre total de blocs qui sont créés, l'intervalle de temps écoulé depuis la dernière transaction, le nombre d'instanciations de code blockchain et le nombre d'appels de code blockchain.
  * Tableau répertoriant l'ensemble des blocs sur ce canal. Développez un bloc pour afficher ses informations détaillées.

  ![Vue d'ensemble du canal](../images/channel_overview_detail.png "Vue d'ensemble du canal")

### Membres
{: #monitor-blockchain-network-monitor-channel-members}

L'onglet "Membres" affiche les informations relatives aux membres de ce canal, notamment les adresses électroniques des opérateurs de l'organisation.

  ![Membres de canal](../images/channel_members.png "Membres de canal")

### Code blockchain
{: #monitor-blockchain-network-monitor-channel-cc}

L'onglet "Code blockchain" répertorie tout le code blockchain qui est instancié sur ce canal avec l'ID de code blockchain, la version et le nombre d'homologues qui exécutent ce code blockchain.

Développez un code blockchain pour afficher ses informations détaillées :
  * Vous pouvez cliquer sur **JSON** pour afficher le fichier JSON du code blockchain.
  * Vous pouvez cliquer sur **Journaux** pour consulter les journaux du code blockchain. Cette vue affiche les journaux à partir de l'homologue depuis lequel vous avez installé le code blockchain et ceux-ci sont filtrés par nom et version de code blockchain.

    Il est recommandé d'ajouter des messages d'erreur ou de réussite uniques après chaque fonction de code blockchain pour vous aider à surveiller et à déboguer le code blockchain. Si vous utilisez un code blockchain complexe qui utilise un grand nombre de fichiers différents, vous pouvez ajouter un mot clé unique dans vos journaux de code blockchain pour localiser les messages des différentes stades de transaction.
   * Vous pouvez cliquer sur **Supprimer** pour supprimer le conteneur de code blockchain en cours d'exécution. Notez que la suppression du conteneur de code blockchain n'a pas pour effet de supprimer réellement le code blockchain. Un code blockchain instancié sur un réseau de blockchain ne peut pas être supprimé.

  ![Code blockchain de canal](../images/channel_chaincode.png "Code blockchain de canal")


## Surveillance du code blockchain
{: #monitor-blockchain-network-monitor-chaincode}

Accédez au Moniteur réseau et affichez l'écran "Installer le code". Si du code blockchain est en cours d'exécution, vous pouvez voir ce code blockchain avec des ID et des versions de code blockchain dans le tableau. Choisissez un homologue dans la liste déroulante afin d'afficher tout le code blockchain de cet homologue dans le tableau. Vous pouvez afficher les journaux du code blockchain sous l'[onglet "Code blockchain"](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-channel-cc) de votre écran "Canal" spécifique.

  ![Code blockchain](../images/installed_cc.png "Code blockchain")

<!----
## Monitoring sample applications
{: #monitor-apps}

In a Starter Plan network, you can view and access sample applications in the "Try Samples" screen of the Network Monitor.  After you deploy a sample application, you can click the **Launch** button to enter your application interface, or the **View on GitHub** link to visit the code repository.  For more information, see [Deploying sample applications](/docs/services/blockchain/prebuilt_samples.html#deploying-sample-applications).

  ![Sample applications](../images/sampleappflow0.png "Sample applications")
--->

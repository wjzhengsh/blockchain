---

copyright:
  years: 2019

lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Tutoriel Rejoindre un réseau
{: #ibp-console-join-network}

{{site.data.keyword.blockchainfull}} Platform est une offre de blockchain en tant que service qui vous permet de développer, déployer et exploiter des applications et des réseaux de blockchain. Vous pouvez en apprendre davantage sur les composants de blockchain et leur fonctionnement en consultant la section [Présentation du composant Blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Ce tutoriel est la deuxième partie de la [série d'exemples de tutoriel réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) ; il explique comment créer des noeuds sur la console {{site.data.keyword.blockchainfull_notm}} Platform et les connecter à un consortium de blockchain hébergé dans un autre cluster.
{:shortdesc}


**Public cible :** Cette rubrique s'adresse aux opérateurs réseau qui sont responsables de la création, de la surveillance et de la gestion du réseau de blockchain.  

Si vous n'avez pas déjà déployé la console dans un cluster Kubernetes à l'aide de {{site.data.keyword.cloud_notm}} Kubernetes Service, voir [Initiation à {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Vous pouvez créer un nouveau cluster Kubernetes pour le déploiement de la console ou utiliser un cluster existant dans votre compte {{site.data.keyword.cloud_notm}}. Après avoir déployé {{site.data.keyword.blockchainfull_notm}} Platform dans votre cluster Kubernetes, vous pouvez lancer la console afin de créer et de gérer vos composants de blockchain.

Que vous déployiez un cluster Kubernetes payant ou non, utilisez le tableau de bord Kubernetes pour porter une attention particulière aux ressources à votre disposition lorsque vous choisissez de déployer des noeuds et de créer des canaux. Il est de votre responsabilité de gérer votre cluster Kubernetes et de déployer des ressources supplémentaires si nécessaire. Même si des composants peuvent être déployés dans un cluster gratuit, plus vous ajoutez de composants, plus lente sera leur exécution.
{: note}

## Série d'exemples de tutoriel réseau
{: #ibp-console-join-network-structure}

Cette série de tutoriels en trois parties vous guide tout au long du processus de création et d'interconnexion d'un réseau Hyperledger Fabric à plusieurs noeuds relativement simple à l'aide de la console {{site.data.keyword.blockchainfull_notm}} Platform 2.0 pour le déploiement d'un réseau dans votre cluster Kubernetes, ainsi que l'installation et l'instanciation d'un contrat intelligent.

* Le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) vous guide tout au long du processus d'hébergement d'un réseau par la création d'un service de tri et d'un homologue.
* **Rejoindre un réseau** : Ce tutoriel vous guide tout au long du processus permettant de rejoindre un réseau existant en créant un homologue et en le joignant à un canal.
* [Déployer un contrat intelligent sur le réseau ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts): Ce tutoriel fournit des informations sur l'écriture d'un contrat intelligent et son déploiement sur votre réseau.

Vous pouvez utiliser les étapes de ces tutoriels pour générer un réseau comportant plusieurs organisations dans un cluster à des fins de développement et de test. Utilisez le tutoriel **Générer un réseau** si vous voulez former un consortium de blockchain en créant un noeud de service de tri et en ajoutant des organisations. Utilisez le tutoriel **Rejoindre un réseau** pour connecter un homologue au réseau. L'utilisation des tutoriels par différents membres de consortium vous permet de créer un réseau de blockchain réellement **distribué**.  


Ce tutoriel décrit comment joindre un homologue à un réseau **existant**. Il suppose qu'un service de tri, hébergeant le réseau, existe déjà dans votre cluster ou sur un autre cluster {{site.data.keyword.blockchainfull_notm}} Platform. Si vous ne disposez d'aucun réseau existant à joindre, consultez le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) pour apprendre à en créer un. Le tutoriel **Rejoindre un réseau** vous guide tout au long des étapes de création des composants de blockchain `Org2` suivants, mis en évidence dans la zone bleue :
![Rejoindre une structure de réseau](../images/ib2-join-network.png "Rejoindre une structure de réseau")  
*Figure 1. Rejoindre une structure de réseau*  
Effectuez les étapes du tutoriel **Rejoindre un réseau** pour créer les composants suivants et exécuter les actions ci-après :

* **Une organisation homologue ** `Org2`  
  Créez la définition MSP (Membership Services Provider) Service de tri qui définit l'organisation `Org2`.
* **Un homologue ** `Org2 homologue`   
  Le registre de blockchain, `Registre x` dans l'illustration ci-dessus, est géré par des homologues distribués. L'homologue est déployé avec [Couch DB ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) comme base de données.
* **Une autorité de certification ** `AC Org2`.
  Une AC (autorité de certification) est le noeud qui émet des certificats pour tous les membres d'une organisation. Nous créons une AC pour l'organisation homologue `Org2`.
* **Joindre un canal**
  Ce tutoriel décrit comment joindre le canal qui a été créé par le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

Tout au long de ce tutoriel, nous indiquons les **valeurs recommandées** pour certaines zones de la console. Les noms et les identités sont ainsi plus faciles à reconnaître dans les onglets et les listes déroulantes. Ces valeurs ne sont pas obligatoires, mais elles peuvent vous être utiles. Nous fournissons un tableau de valeurs recommandées après chaque tâche.
{:tip}

## Etape 1 : Créer une organisation supplémentaire et votre point d'entrée pour la blockchain
{: #ibp-console-join-network-create-ca-org2}

Pour chacune des organisations que vous voulez créer avec la console, vous devez déployer au moins une autorité de certification (AC). Une AC est le noeud qui émet des certificats pour tous les participants du réseau (homologues, services de tri, clients, et ainsi de suite). Ces certificats, qui incluent une paire de clés publiques et une paire de clés privées, permettent aux participants d'un réseau de communiquer, de s'authentifier et enfin d'effectuer des transactions. Ces AC vont créer l'ensemble des identités et des certificats qui appartiennent à votre organisation, en plus de définir l'organisation elle-même. Vous pouvez ensuite utiliser ces identités pour déployer des noeuds, exploiter votre réseau et soumettre des transactions à la blockchain. Pour plus d'informations sur votre AC et les identités que vous allez devoir créer, voir [Gestion des identités](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

Dans ce tutoriel, nous allons créer une organisation et **une autorité de certification**.

### Création de l'autorité de certification (AC) de votre organisation
{: #ibp-console-join-network-create-CA-org2CA}

Dans le cadre de ce tutoriel, votre AC émet les clés publiques et privées pour les utilisateurs et les noeuds. Ces identités ne sont pas gérées par {{site.data.keyword.IBM_notm}} et les clés ne sont pas stockées dans votre cluster Kubernetes ou sur la console. Elles sont uniquement stockées dans l'espace de stockage local de votre navigateur. Par conséquent, assurez-vous d'exporter la définition MSP de vos identités et de votre organisation. Si vous essayez d'accéder à la console à partir d'une autre machine ou d'un navigateur différent, vous devez importer ces identités et définitions d'organisation.  
{:important}

Procédez comme suit depuis votre console :  

1. Accédez à l'onglet **Noeuds** sur la gauche et cliquez sur **Ajouter une autorité de certification**. Les panneaux latéraux à quatre étapes vous permettent de personnaliser l'autorité de certification que vous voulez créer et l'organisation pour laquelle cette autorité de certification va émettre des clés.
2. Cliquez sur **{{site.data.keyword.cloud_notm}}** sous **Créer une autorité de certification**.
3. Utilisez le second panneau latéral pour affecter un **nom d'affichage** à votre autorité de certification. Notre valeur recommandée pour cette autorité de certification est `AC Org2`.
4. Sur le panneau suivant, affectez des données d'identification à l'admin de votre autorité de certification en indiquant **admin** comme `ID admin`, ainsi que le secret de votre choix, mais nous recommandons la valeur `adminpw` pour les besoins de ce tutoriel.
5. Cliquez sur **Suivant** et sur **Ajouter une autorité de certification**.

**Tâche : Créer l'autorité de certification de l'organisation homologue**

  | **Zone** | **Nom d'affichage** | **ID d'inscription** | **Secret** |
  | ------------------------- |-----------|-----------|-----------|
  | **Créer une autorité de certification** | AC Org2  | admin | adminpw |

*Figure 2. Création de l'autorité de certification de l'organisation homologue*  
Une fois l'autorité de certification déployée, vous allez l'utiliser lors de la création de la définition MSP de votre organisation, de l'enregistrement des utilisateurs, ainsi que pour créer votre point d'entrée sur un réseau, l'**homologue**.

### Utilisation de votre autorité de certification pour enregistrer des identités
{: #ibp-console-join-network-use-CA-org2}

Chaque noeud ou application que vous souhaitez créer a besoin de clés publiques et privées pour participer au réseau de blockchain. Vous devez également créer des clés admin pour ces noeuds et applications afin de pouvoir les gérer à partir de la console. Vous allez utiliser l'autorité de certification pour créer deux identités :

* **Un admin d'organisation** Cette identité vous permet d'exploiter des noeuds à l'aide de la console de plateforme.
* **Une identité homologue** Cette identité vous permet de déployer un homologue.

Pour générer ces certificats, effectuez les étapes suivantes :

1. Sur la console, utilisez l'onglet **Noeuds** pour accéder à l'autorité de certification `AC Org2` que vous avez créée.
2. Une fois l'autorité de certification sélectionnée, vous devrez enregistrer un admin pour cette organisation, `org2`, ainsi qu'une identité pour l'homologue lui-même. Vous devez déjà voir une identité sur cette page ; il s'agit de l'admin que vous avez créé pour l'autorité de certification. Pour enregistrer nos nouveaux utilisateurs, cliquez sur le bouton **Enregistrement d'utilisateur**.
3. Affectez l'ID d'inscription `org2admin` à l'admin de l'organisation. Vous pouvez utiliser n'importe quel secret, mais nous suggérons `org2adminpw` pour plus de commodité. Cliquez sur **Suivant**.
4. A l'étape suivante, définissez le Type de cette identité sur `client` et sélectionnez l'une des organisations affiliées dans la liste déroulante. La zone d'affiliation est destinée aux utilisateurs expérimentés et elle n'est pas utilisée dans le cadre de ce tutoriel. Les éléments de la liste sont les affiliations par défaut de l'autorité de certification Fabric. Si vous souhaitez en savoir davantage sur l'utilisation des affiliations par l'autorité de certification Fabric, consultez la rubrique relative à l'[enregistrement d'une nouvelle identité ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Pour l'instant, sélectionnez une affiliation à partir de la liste, par exemple `org2` et cliquez sur **Suivant**.
5. Vous n'êtes pas tenu de compléter les zones **Nombre maximal d'inscriptions** et **Ajouter des attributs**. Elles ne sont pas utilisées dans le cadre de ce tutoriel, mais vous pouvez en apprendre plus sur leurs fonctions dans la rubrique relative à l'[enregistrement des identités](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
6. Une fois l'admin de l'organisation enregistré, recommencez les étapes 2 à 5 pour l'identité de l'homologue, à l'aide de la même autorité de certification `AC Org2`, en lui attribuant l'ID d'inscription `peer2`. Comme précédemment, nous recommandons le secret `peer2pw` pour des raisons de commodité. Il s'agit d'une identité de noeud, vous devez donc sélectionner `homologue` comme **Type** à l'étape suivante. Ensuite, ignorez les zones **Nombre maximal d'inscriptions** et **Attributs**, comme précédemment.

**Tâche : Enregistrer des utilisateurs**

  |  **Zone** | **Description** | **ID d'inscription** | **Secret** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Enregistrer des utilisateurs** |  Admin Org2 | org2admin | org2adminpw |
  | | Identité de l'homologue |  peer2 | peer2pw |

*Figure 3. Utilisation de votre autorité de certification pour enregistrer des utilisateurs*  

### Création de la définition MSP d'une organisation homologue
{: #ibp-console-join-network-create-peers-org2}

A présent que nous avons créé l'autorité de certification de l'homologue et que nous l'avons utilisée pour **enregistrer** les identités de notre organisation, nous devons créer une définition formelle de l'organisation de l'homologue, laquelle est appelée définition MSP (Membership Services Provider). De nombreux homologues peuvent appartenir à une organisation. **Vous n'êtes pas tenu de créer une nouvelle organisation chaque fois que vous créez un homologue.** Comme il s'agit de la première fois que nous déroulons ce tutoriel, nous allons créer l'ID MSP pour cette organisation. Pendant le processus de création du MSP, nous allons générer les certificats pour l'identité `org2admin` et les ajouter à notre portefeuille de console.

1. Accédez à l'onglet **Organisations** dans la navigation de gauche et cliquez sur **Créer une définition de MSP**.
2. Attribuez à votre définition MSP un nom d'affichage, par exemple `MSP Org2` et un ID, par exemple `org2msp`. Si vous voulez indiquer votre propre ID MSP dans cette zone, assurez-vous de respecter les spécifications relatives aux limitations pour ce nom dans l'infobulle.
3. Sous **Détails d'autorité de certification racine**, indiquez l'autorité de certification homologue que nous avons créée comme autorité de certification racine de votre organisation. Si c'est la première fois que vous suivez ce tutoriel, vous ne devez voir qu'une seule : `AC Org2`. Sélectionnez-la.
4. Les zones **ID d'inscription** et **Secret d'inscription** ci-dessous sont remplies automatiquement avec l'ID d'inscription et le secret pour le premier utilisateur que vous avez créé avec votre autorité de certification. Vous pouvez utiliser ces valeurs, mais nous vous déconseillons d'utiliser l'identité de vitre admin d'autorité de certification comme admin de votre d'organisation.  Au lieu de cela, pour des raisons de sécurité, nous vous conseillons d'indiquer l'ID inscription et le secret que vous avez créés pour l'admin de votre organisation, `org2admin` et `org2adminpw`. Ensuite, affectez à cette identité un nom d'affichage, par exemple `Admin Org2`.
5. Cliquez sur le bouton **Générer** afin d'inscrire cette identité en tant qu'admin de votre organisation et exportez l'identité dans le portefeuille, où elle sera utilisée lors de la création de l'homologue et de la création de canaux.
6. Cliquez sur **Exporter** pour exporter les certificats admin sur votre système de fichiers. Comme nous l'avons dit plus haut, cette identité n'est pas stockée dans votre cluster ou gérée par {{site.data.keyword.IBM_notm}}. Elle est stockée uniquement dans l'espace de stockage de votre navigateur. Si vous changez de navigateur, vous devez importer cette identité dans votre portefeuille de console afin de pouvoir administrer l'homologue.
7. Cliquez sur **Créer une définition de MSP**.

**Tâche : Créer la définition MSP de l'organisation de l'homologue**

  |  | **Nom d'affichage** | **ID MSP** | **ID d'inscription**  | **Secret** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Créer une organisation** | Définition | org2msp |||
  | **Autorité de certification racine** | AC Org2 ||||
  | **Cert admin org** | |  | org2admin | org2adminpw |
  | **Identité** | Admin Org2 |||||

  *Figure 4. Création de la définition MSP d'une organisation homologue*  

Une fois la définition MSP créée, vous devez voir l'admin de l'organisation homologue dans votre portefeuille de console.

**Tâche : Consulter le portefeuille de console**

  | **Zone** |  **Nom d'affichage** | **Description** |
  | ------------------------- |-----------|----------|
  | **Identité** | Admin Org2 | Identité Org2 |

  *Figure 5. Consultation de votre portefeuille de console*  

Pour plus d'informations sur les définitions MSP, voir la rubrique relative à la [gestion des organisations](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

L'exportation et la sauvegarde de votre définition MSP sont importantes car cela permet à votre organisation d'être ajoutée à un consortium hébergé sur une autre console.
{:important}

### Création d'un homologue
{: #ibp-console-join-network-peer-create}

Une fois que vous avez [créé une autorité de certification](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA), que vous l'avez utilisée pour enregistrer des identités, et que vous avez créé la définition [MSP de l'organisation homologue](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2), vous êtes prêt(e) pour la création d'un homologue.

#### Quel rôle jouent les homologues ?
{: #ibp-console-join-network-peer-role}

Il est important de garder à l'esprit que les organisations elles-mêmes ne gèrent pas les registres. Ce sont les homologues qui s'en chargent. Les organisations utilisent également des homologues pour signer des propositions de transaction et approuver des mises à jour de configuration de canal. Etant donné qu'avoir au moins deux homologues sur un canal les rend hautement disponibles, une meilleure pratique consiste à avoir au moins deux homologues qui ont rejoint un canal pour les implémentations de niveau production. Dans le cadre de ce tutoriel, nous allons voir uniquement le processus de création d'un seul homologue.

Du point de vue de l'allocation de ressource, il est possible de joindre les mêmes homologues à plusieurs canaux. De par sa conception, l'homologue garantit que les données d'un canal ne peuvent pas passer dans un autre via l'homologue. Toutefois, étant donné que l'homologue va stocker un registre distinct pour chaque canal, il est nécessaire de s'assurer que l'homologue dispose de suffisamment de puissance de traitement et d'espace de stockage pour traiter la charge de transactions et de données.

#### Déploiement de votre homologue
{: #ibp-console-join-network-deploy-peer-role}

Utilisez votre console pour effectuer les étapes suivantes :

1. Sur la page **Noeuds**, cliquez sur **Ajouter un homologue**.
2. Cliquez sur {{site.data.keyword.cloud_notm}} sous **Créer un nouvel homologue**, puis cliquez sur **Suivant**.
3. Affectez à votre homologue le **nom d'affichage** `Org2 homologue`.
4. A l'écran suivant, sélectionnez `AC Org2` comme autorité de certification. Ensuite, indiquez l'ID d'inscription et le secret de l'identité homologue que vous avez créée pour votre homologue, `peer2` et `peer2pw`. Ensuite, sélectionnez votre MSP, `MSP Org2`, dans la liste déroulante et cliquez sur **Suivant**.
5. Le panneau latéral suivant vous invite à entrer des informations sur l'autorité de certification TLS. Bien qu'il soit possible de créer des admins distincts pour l'autorité de certification TLS qui est déployée avec votre autorité de certification, cela n'est pas obligatoire.
   - Affectez l'**ID d'inscription TLS**, l'`admin` et le secret `adminpw`, identiques aux valeurs indiquées pour l'ID d'inscription et le secret d'inscription lors de la création de l'autorité de certification.
   - Le **nom d'hôte CSR TLS** est réservé aux utilisateurs expérimentés pour indiquer un nom de domaine personnalisé qui peut être utilisé pour adresser le noeud final d'homologue. N'indiquez rien pour l'instant dans la zone **Nom d'hôte CSR TLS**, elle n'est pas utilisée dans le cadre de ce tutoriel.
6. Le dernier panneau latéral vous invite à **Associer une identité** et à la définir en tant qu'admin de votre homologue. Sélectionnez votre identité admin homologue `Admin Org2`.
7. Passez en revue le récapitulatif et cliquez sur **Ajouter un homologue**.

**Tâche : Déployer un homologue**

  |  | **Nom d'affichage** | **ID MSP** | **ID d'inscription** | **Secret** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Créer un homologue** | Org2 homologue | org2msp |||
  | **AC** | AC Org2 ||||
  | **Identité de l'homologue** | |  | peer2 | peer2pw |
  | **Certificat d'administrateur** | org2msp ||||
  | **Autorité de certification TLS** | AC Org2 ||||
  | **ID autorité de certification TLS** | || admin | adminpw |
  | **Associer une identité** | Admin Org2 |||||

  *Figure 6. Déploiement d'un homologue*  

## Etape 2 : Ajouter votre organisation à la liste des organisations pouvant effectuer des transactions
{: #ibp-console-join-network-add-org2}

Comme nous l'avons mentionné précédemment, une organisation homologue doit être un membre du consortium d'un service de tri pour pouvoir créer ou rejoindre un canal. Ceci est dû au fait que les canaux sont, a un niveau technique, des **chemins de messagerie** entre homologues via le service de tri. De la même manière qu'un homologue peut être joint à plusieurs canaux sans la transmission d'informations d'un canal à un autre, un service de tri peut également avoir plusieurs canaux qui s'exécutent en son sein sans que les données soient exposées à des organisations sur d'autres canaux.

Etant donné que seuls les admin de service de tri peuvent ajouter des organisations homologues au consortium, vous devrez **être** l'admin du service de tri ou vous devez **envoyer** les informations MSP à l'admin du service de tri. Dans ce tutoriel, vous pouvez **ajouter l'organisation de l'homologue au service de tri sur votre console**, si vous hébergez le service de tri. Ou bien vous devez **exporter les informations de votre organisation** et les transmettre à l'admin de votre service de tri, lequel a créé le réseau où réside le service de tri. L'admin du service de tri peut ensuite importer votre organisation et vous ajouter au consortium.

### Ajout de l'organisation de l'homologue au service de tri sur ma console
{: #ibp-console-join-network-add-org2-local}

**Suivez ces étapes uniquement si votre console inclut déjà le service de tri et le canal que vous voulez rejoindre.** Sinon, passez à la section [Exportation des informations de votre organisation](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).  

Etant donné que seuls les admin de service de tri peuvent ajouter des organisations homologues au consortium, vous devrez être l'admin du service de tri, ce qui signifie que vous disposez de l'identité admin de l'organisation du service de tri dans votre portefeuille de console.  

1. Accédez à l'onglet **Noeuds**.
2. Faites défiler jusqu'au service de tri que vous voulez utiliser et cliquez sur ce dernier pour l'ouvrir.
3. Sous **Membres du consortium**, cliquez sur **Ajouter une organisation**.
4. Dans la liste déroulante, sélectionnez `MSP Org2`, car il s'agit du MSP qui représente l'organisation de l'homologue `org2`.
5. Cliquez sur **Ajouter une organisation**. 

Ajoutez maintenant l'organisation homologue au canal.
1. Accédez à l'onglet **Canaux**, cliquez sur `canal1`.
2. Cliquez sur l'icône **Paramètres** pour mettre à jour le canal et ajouter l'organisation homologue au canal.
3. Dans la liste déroulante **Choisir l'un des services de tri disponibles**, assurez-vous que `Service de tri` est sélectionné.
4. Dans la liste déroulante **ID MSP de programme de mise à jour de canal**, assurez-vous que `org1MSP` est sélectionné.
5. Dans la liste déroulante **Associer une identité disponible**, assurez-vous que `Org1Admin` est sélectionné.
6. Faites défiler jusqu'à la section intitulée `Ajouter des organisations au canal` et ouvrez la liste déroulante `Sélectionner un membre de canal`, puis sélectionnez la définition MSP de l'organisation homologue, `MSP Org2` pour le tutoriel.
7. Cliquez sur **Ajouter** et affectez des droits pour cette organisation. Nous vous recommandons d'affecter les droits `Opérateur` afin qu'il soit possible de mettre à jour le canal.
8. Cliquez sur **Mettre à jour un canal**.

Une fois ce processus terminé, il est possible pour `org2` de créer ou de rejoindre un canal hébergé sur votre `Service de tri`. Vous pouvez passer à l'[Etape 3 : Joindre votre homologue au canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Exportation des informations de votre organisation
{: #ibp-console-join-network-add-org2-remote}

**Suivez ces étapes uniquement si le service de tri et le canal qui va être rejoint par votre homologue résident dans une autre instance de service {{site.data.keyword.blockchainfull_notm}} Platform.** Sinon, vous pouvez passer à m'[Etape 3 : Joindre votre homologue au canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

Vous devez envoyer la définition MSP de votre organisation au service de tri pour qu'il soit ajouté au consortium à l'aide des étapes ci-dessous.  

Lors de ces étapes, vous devez être l'admin de l'**organisation homologue**, ce qui signifie que vous disposez de l'identité admin de l'organisation de l'homologue dans votre portefeuille de console :  
1. Accédez à l'onglet **Organisations**. Vous pouvez voir les organisations qui sont disponibles pour l'exportation et sont répertoriés sous **Organisations disponibles**. Cliquez sur le bouton **Télécharger** dans la vignette de l'organisation pour télécharger le fichier de configuration JSON qui représente la définition MSP de l'organisation homologue.
2. Envoyez ce fichier à l'admin du service de tri dans une opération hors bande.

### Importation de la définition d'organisation
{: #ibp-console-join-network-import-remote-msp}
L'admin du service de tri doit importer ce fichier JSON pour ajouter votre organisation à sa console :
1. Accédez à l'onglet **Organisations**, cliquez sur le bouton **Importer une définition de MSP**, puis sélectionnez le fichier JSON qui représente la définition MSP de l'organisation de l'homologue.  
2. Accédez à la page **Noeuds** et cliquez sur votre noeud de service de tri. Sur le panneau du noeud de service de tri, sous **Membres du consortium**, cliquez sur **Ajouter une organisation**. Dans le panneau latéral qui s'ouvre, sélectionnez `MSP Org2` dans la liste des définitions MSP depuis votre onglet **Organisations**.
3. Accédez à l'onglet **Canaux**, cliquez sur `canal1`, puis cliquez sur l'icône **Paramètres** pour mettre à jour le canal et ajouter l'organisation homologue au canal.
4. Dans la liste déroulante **Choisir l'un des services de tri disponibles**, assurez-vous que `Service de tri` est sélectionné.
5. Dans la liste déroulante **ID MSP de programme de mise à jour de canal**, assurez-vous que `org1MSP` est sélectionné.
6. Dans la liste déroulante **Associer une identité disponible**, assurez-vous que `Org1Admin` est sélectionné.
7. Faites défiler jusqu'à la section intitulée `Ajouter des organisations au canal` et ouvrez la liste déroulante `Sélectionner un membre de canal`, puis sélectionnez la définition MSP de l'organisation homologue, `MSP Org2` pour le tutoriel.
8. Cliquez sur **Ajouter** et affectez des droits pour cette organisation. Nous vous recommandons d'affecter les droits `Opérateur` afin qu'il soit possible de mettre à jour le canal.
9. Cliquez sur **Mettre à jour un canal**.

Une fois que l'admin du service de tri a joint votre organisation homologue au consortium et au canal, vous devez importer le noeud de service de tri dans la console. Vous pouvez ensuite joindre les canaux qui sont hébergés par le service de tri. Pour **importer** le service de tri, procédez comme suit :

L'admin de l'**organisation du service de tri** doit d'abord effectuer les étapes suivantes :
1. Accéder au noeud du service de tri sous l'onglet **Noeuds** et cliquer sur l'icône **Paramètres** en regard du nom de noeud pour ouvrir le panneau latéral. Cliquer sur le bouton **Exporter** sous **Actions** pour télécharger un fichier de configuration JSON.
2. Envoyer ce fichier à l'organisation homologue dans une opération hors bande. L'administrateur de l'organisation homologue peut ensuite utiliser le fichier de configuration pour ajouter le service de tri à la console.

### Importation du service de tri depuis un autre cluster
{: #ibp-console-join-network-import-remote-orderer}

Vous pouvez suivre ces étapes si vous êtes l'admin de l'**organisation homologue**:
1. Accédez à la page **Noeuds** et cliquez sur **Ajouter un service de tri**.
2. Cliquez sur {{site.data.keyword.cloud_notm}} sous **Importer un service de tri existant**.
3. Ensuite, cliquez sur le bouton **Télécharger JSON** et sélectionnez le fichier JSON qui représente le noeud lui-même.
4. A l'étape suivante, lorsque vous êtes invité à associer une identité, sélectionnez l'identité de l'organisation de l'homologue. Pour ce tutoriel, vous pouvez sélectionner `Admin Org2` et cliquer sur **Importer une configuration de service de tri**.


## Etape 3 : Joindre votre homologue au canal
{: #ibp-console-join-network-join-peer-org2}

Nous avons presque terminé. Votre homologue peut désormais être joint à un canal existant. Vous devez obtenir le `nom de canal`, hors bande, auprès de l'opérateur réseau qui a créé le canal. Dans le tutoriel **Générer un réseau**, nous avons créé un canal nommé `canal1`. Si ce n'est déjà fait, accédez à l'onglet **Canaux** dans la navigation de gauche.

Procédez comme suit depuis votre console :

1. Cliquez sur le bouton **Joindre un canal** pour lancer les panneaux latéraux.
2. Sélectionnez votre service de tri nommé 'Service de tri' et cliquez sur **Suivant**.
3. Entrez le nom du canal que vous voulez rejoindre, `canal1` et cliquez sur **Suivant**.
4. Sélectionnez les homologues qui doivent rejoindre le canal. Pour les besoins de ce tutoriel, cliquez sur `Org2 homologue`.
5. Cliquez sur **Joindre des homologues**.

Si vous prévoyez d'optimiser les fonctions [Données privées ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/private-data/private-data.html "Données privées") ou [Reconnaissance de service ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-overview.html "Reconnaissance de service") d'Hyperledger Fabric, vous devez configurer des homologues d'ancrage dans vos organisations depuis l'onglet **Canaux**. Consultez la rubrique relative à la [configuration des homologues d'ancrage pour les données privées](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) à l'aide de l'onglet **Canaux** sur la console.

Vous pouvez également créer un nouveau canal dès que votre organisation est membre du consortium. Utilisez les étapes relatives à la [création d'un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) dans le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Etapes suivantes
{: #ibp-console-join-network-next-steps}

Une fois que vous avez joint votre homologue à un canal, utilisez les étapes suivantes pour déployer un contrat intelligent et commencer à soumettre des transactions à la blockchain :

- [Déployez un contrat intelligent sur votre réseau](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) à l'aide de la console.
- Une fois que vous avez installé et instancié votre contrat intelligent, vous pouvez [soumettre des transactions à l'aide de votre application client](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilisez [l'exemple de document commercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) pour déployer un exemple de contrat intelligent et soumettre des transactions à l'aide d'un exemple de code d'application.

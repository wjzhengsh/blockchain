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
{:important: .important}
{:note: .note}
{:pre: .pre}

# Création et gestion des identités
{: #ibp-console-identities}

Les noeuds d'{{site.data.keyword.blockchainfull_notm}} Platform reposent sur Hyperledger Fabric et ils génèrent des réseaux de blockchain privés. Cela signifie que tous les participants du consortium de blockchain doivent avoir des identités qui sont régulièrement vérifiées par une infrastructure PKI. La console {{site.data.keyword.blockchainfull_notm}} Platform vous permet de créer ces identités avec les autorités de certification de votre organisation. Vous devez stocker ces identités dans votre portefeuille de console afin de pouvoir les utiliser pour exploiter votre réseau.

**Public cible :** Cette rubrique s'adresse aux opérateurs réseau qui sont responsables de la création, de la surveillance et de la gestion du réseau de blockchain.

## Gestion des autorités de certification
{: #ibp-console-identities-manage-ca}

Une autorité de certification est semblable à un notaire public de confiance qui agit comme point d'ancrage entre plusieurs parties, chaque organisation d'un consortium gérant sa propre autorité de certification. Votre autorité de certification crée les identités appartenant à votre organisation et émet pour chaque identité une clé publique et une clé privée. Ces clés sont celles qui autorisent tous vos noeuds et applications à signer et à vérifier leurs actions. Pour plus de détails sur la façon dont les autorités de certification sont utilisées pour établir une identité, voir la [rubrique relative aux identités ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "Identité") dans la documentation Hyperledger Fabric.

Lorsque vous créez une autorité de certification à l'aide de la console {{site.data.keyword.blockchainfull_notm}} Platform, deux autorités de certification sont créées avec la même URL de noeud final : une autorité de certification racine et une autorité de certification TLS. Vous pouvez voir ces deux autorités de certification sous le même nom d'affichage dans la page **Noeuds** de votre console. L'autorité de certification racine fournit des clés à vos noeuds et applications. L'autorité de certification TLS fournit les certificats utilisés pour protéger la communication au sein de votre réseau. Pour plus d'informations sur TLS au sein de votre réseau, voir la rubrique [Utilisation de votre autorité de certification TLS](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca).

Lorsque vous créez vos noeuds, vous devez utiliser votre autorité de certification racine pour créer les identités suivantes qui sont obligatoires pour déployer, exploiter, et interagir avec votre réseau :
- **Admin de l'autorité de certification :** Votre autorité de certification contient une identité d'admin de l'autorité de certification par défaut. Cette identité a un ID et un secret d'inscription, lesquels sont analogues à un nom d'utilisateur et un mot de passe, que vous indiquez pendant le déploiement de votre autorité de certification. Vous pouvez utiliser ce nom d'utilisateur et mot de passe admin pour utiliser votre autorité de certification et créer des identités. Si vous avez créé une autorité de certification à l'aide de la console, l'admin de votre autorité de certification racine est également l'admin de l'autorité de certification TLS, sauf si vous décidez de créer une autorité d'administration TLS distincte.
- **Homologues ou services de tri :** Vous devez enregistrer une identité pour chacun des homologues et services de tri appartenant à votre organisation. Vos noeuds utiliseront ensuite ces identités au cours du déploiement pour générer les clés dont ils ont besoin pour participer au réseau. Pour des raisons de sécurité, créez un ID et un secret d'inscription uniques pour chaque noeud que vous déployez.
- **Admins homologue ou service de tri :** Les noeuds {{site.data.keyword.blockchainfull_notm}} Platform sont déployés avec les clés publiques des identités des administrateurs de composant au sein de ces derniers. Ces certificats permettent aux administrateurs d'utiliser le composant depuis un client distant ou à l'aide de la console.
- **Admins org :** Lorsque vous rejoignez un consortium hébergé par un service de tri, vous fournissez les clés publiques des identités qui vont devenir les administrateurs de votre organisation. Vous pouvez utiliser ces identités pour créer ou modifier des canaux.
- **Applications :** Vos applications doivent signer leurs transactions avant de les soumettre pour validation par le réseau. Vous devez créer des identités que vous pouvez utiliser pour signer les transactions de vos applications client.

Vous pouvez utiliser la console pour créer ces identités au moyen du [processus d'enregistrement](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Une fois que vous avez enregistré vos identités admin, vous devez émettre pour chaque identité une clé publique et privée, fournir la clé publique à la définition MSP de votre organisation, puis ajouter l'identité à votre portefeuille de console. Vous pouvez effectuer ces étapes pour une identité admin lorsque vous [créez la définition MSP de votre organisation](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Vous pouvez utiliser des identités distinctes comme admin org ou admin noeud, ou vous pouvez utiliser une identité pour effectuer les deux tâches. Le [tutoriel Générer un réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) utilise une identité en tant qu'admin pour chaque organisation créée dans le cadre du tutoriel.

## Définition d'une identité d'autorité de certification
{: #ibp-console-identities-ca-identity}

Avant de gérer des identités, vous devez définir l'identité de l'admin de l'autorité d'administration, à l'aide de l'identité admin créée lors de la création de l'autorité de certification ou en établissant une nouvelle identité. Ouvrez l'autorité de certification sous l'onglet **Noeuds**. L'ID d'inscription de l'identité actuellement active est visible en regard du nom de l'autorité de certification dans la partie supérieure du panneau. Vous pouvez utiliser cette identité admin pour créer d'autres identités en utilisant le bouton **Enregistrer** pour enregistrer des identités qui deviendront des admin org et des identités de noeud, ou utiliser le bouton **Inscrire** pour générer une identité et l'exporter dans le portefeuille.

Pour basculer vers une identité différente à utiliser en tant qu'admin d'autorité de certification, cliquez sur l'icône **Paramètres**, puis cliquez sur **Définir l'identité** sur la règle. Vous pouvez spécifier une identité qui existe dans le portefeuille à partir de l'onglet **Identité existante**. Vous pouvez aussi utiliser l'onglet **Nouvelle identité** pour coller des certificats pour l'admin, ou pour télécharger un JSON contenant les certificats.

Certaines identités ne peuvent pas enregistrer de nouveaux utilisateurs. Pour plus d'informations, notamment sur l'établissement d'une autorité d'administration supplémentaire, voir [Création de nouveaux admin d'autorité de certification](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin).
{: note}

## Enregistrement des identités
{: #ibp-console-identities-register}

La première étape de création d'une identité est appelé **enregistrement**. Pendant l'enregistrement, un ID et une secret d'inscription sont créés, et ils peuvent ensuite être utilisés par un admin de noeud ou d'organisation pour **inscrire** cette identité en générant une clé publique et une clé privée.

Vous devez entrer les informations suivantes lors de l'enregistrement d'une nouvelle identité auprès de votre autorité de certification.

- **ID d'inscription** et **Secret d'inscription** : Cette identité a un ID et un secret d'inscription, lesquels sont analogues à un nom d'utilisateur et un mot de passe, que vous indiquez pendant le déploiement de votre autorité de certification. Vous pouvez utiliser cet ID admin et ce secret pour créer des certificats avec cette identité à l'aide de la console ou du client d'autorité de certification Fabric.
- **Type** : Lors du déploiement de l'autorité de certification, l'administrateur a indiqué les types d'identités émises par l'autorité de certification. Voici quelques exemples de types d'identité courants : homologue, service de tri et client (applications). Sélectionnez le type d'identité pour cet utilisateur dans la liste des types disponibles.
- **Affiliation** : Partie de votre organisation que vous souhaitez affilier avec cet utilisateur. Il peut s'agir, par exemple, d'un département ou d'une unité qui exploite une application ou un homologue. Il est possible de limiter l'admin d'une autorité de certification particulière afin qu'il puisse uniquement enregistrer des utilisateurs au sein de son propre département auprès d'une organisation.
- **Nombre d'inscriptions maximum :** (Facultatif) Vous pouvez utiliser entrer le nombre de fois où vous pouvez inscrire ou générer des certificats à l'aide de cette identité. L'indication d'un nombre limité d'inscriptions permet d'assurer la sécurité de vos noeuds et applications. La valeur par défaut est un nombre illimité d'inscriptions.
- **Attributs** : (Facultatif) Vous pouvez indiquer des attributs [attribute-based access control ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "Attribute-Based Access Control") pour l'utilisateur. Par exemple, vous pouvez utiliser cette section pour [créer un autre admin d'autorité de certification](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) avec le droit d'enregistrer et d'inscrire de nouvelles identités. Vous pouvez afficher une liste complète des attributs d'autorité de certification Fabric disponibles dans la section [Enregistrement d'une nouvelle identité ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Enregistrement d'une nouvelle identité") du guide d'utilisation de l'autorité de certification Fabric.

Cliquez sur le bouton **Enregistrer un utilisateur** dans le panneau de présentation de l'autorité de certification afin de créer un nouvel utilisateur. Assurez-vous de [définir l'identité](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) à l'aide d'une identité ayant la possibilité d'enregistrer de nouveaux utilisateurs avant d'essayer d'effectuer cette tâche. Il s'agit en général de votre utilisateur `admin`. Si le bouton est grisé, cela signifie que vous n'avez pas défini une identité ou que l'identité ne peut pas créer de nouvelles identités.  

Lorsque vous cliquez sur **Enregistrer un utilisateur**, une série de panneaux latéraux s'affiche :
1. Dans le premier panneau latéral, entrez l'**ID d'inscription** et le **Secret d'inscription** de la nouvelle identité. **Sauvegardez ces valeurs** car elles ne sont pas stockées par la console.
2. Dans le second panneau latéral, sélectionnez le **Type** de l'identité. La liste déroulante contient la liste des types pris en charge par cette autorité de certification. Ensuite, sélectionnez l'**affiliation** à laquelle appartiendra la nouvelle identité. Vous pouvez choisir une affiliation existante dans la liste déroulante ou en entrer une nouvelle.
3. Dans le troisième panneau latéral, entrez le **Nombre d'inscriptions maximum** autorisé pour cette identité. Si cette zone n'est pas renseignée, la valeur par défaut est un nombre illimité d'inscriptions.
4. Dans le dernier panneau latéral, ajoutez les **attributs** de l'identité que vous créez.

Une fois que vous avez cliqué sur **Enregistrer**, la nouvelle identité est ajoutée à la liste de **Utilisateurs authentifiés** qui ont été créés par votre autorité de certification. Les identités sont répertoriées par leurs **ID d'inscription**, ainsi que par leur **Type** et leur **Affiliation**. Lorsque vous cliquez sur une identité dans le tableau, un panneau latéral s'affiche qui vous permet de voir le **Nombre d'inscriptions maximum** et les **Attributs** qui ont été créés lors de l'enregistrement.

### Création de nouveaux admin de l'autorité de certification
{: #ibp-console-identities-ca-admin}

Par défaut, seul l'admin de l'autorité de certification créé pendant le déploiement a la possibilité d'enregistrer de nouvelles identités. Vous pouvez également créer d'autres identités avec la possibilité d'enregistrer de nouveaux utilisateurs à partir du panneau **Attributs** du processus d'enregistrement.

Dans le panneau latéral 4, cliquez sur le bouton **Ajouter un attribut**. Fournissez un **nom d'attribut**, `hf.Registrar.Roles`. Entrez une **valeur d'attribut**, `*`. Vous pouvez également utiliser ce panneau pour créer une identité qui peut uniquement enregistrer certains types d'identité, tels que des clients ou des homologues, ou au sein d'une affiliation. Vous pouvez également créer une identité qui a la possibilité de révoquer une identité et tous les certificats que cette identité a émis. Vous pouvez afficher une liste complète des attributs dans la section [Enregistrement d'une nouvelle identité ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Enregistrement d'une nouvelle identité") du guide d'utilisation de l'autorité de certification Fabric.

## Inscription d'une identité
{: #ibp-console-identities-enroll}

Vous pouvez générer le certificat public et la clé privée pour chaque identité qui a été enregistrée auprès de votre autorité de certification. Si vous avez enregistré d'autres identités admin auprès de votre autorité de certification, vous pouvez générer les clés de l'identité admin puis l'inclure lors de la [création de la définition MSP de votre organisation](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp).

Avant d'inscrire une identité, vous devez [définir l'identité](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) afin de pouvoir explorer l'autorité de certification. Normalement, vous la définissez sur l'identité admin qui a été spécifiée lors de la création de l'autorité de certification. Vous pouvez confirmer que votre autorité de certification est définie sur cette identité en examinant la page des détails de l'autorité de certification et en recherchant l'ID d'inscription de l'identité actuellement active en regard du nom de l'autorité de certification. Une fois que vous avez la confirmation que l'identité est définie sur votre identité  admin, cliquez sur **Inscrire une identité** dans le menu déroulant dynamique de l'utilisateur  afin de générer le certificat et la clé d'un utilisateur enregistré auprès de l'autorité de certification.

- Entrez le `Secret d'inscription` de l'utilisateur.
- A l'étape suivante, les clés générées sont affichées.
  - La clé publique est affichée dans la zone **Certificat**. Ce certificat est également appelé certificat d'inscription, certificat signataire, ou signCert. Vous devez exporter le certificat signCert dans un fichier sur votre système local afin qu'il puisse être utilisé lors de la création d'un application client avec l'extension VSCode.
  - Vous pouvez trouver la clé privée correspondante dans la zone **Clé privée**. Encore une fois, vous devez exporter la clé privée sur votre système local afin qu'elle puisse être utilisée avec une application client créée avec l'extension VSCode.
  - Le certificat et la clé privée créés en cliquant sur **Inscrire une identité** sont générés une seule fois et ne sont pas stockés par la console ou votre navigateur. Lorsque vous cliquez sur le bouton **Inscrire une identité**, cette opération est dénombrée par rapport au nombre maximal d'inscriptions que vous avez défini pour l'administrateur de l'autorité de certification. Lors de l'inscription, vous devez stocker la paire de clés en téléchargeant l'identité sur votre système de fichiers local ou en l'ajoutant à votre portefeuille de console. Entrez un nouveau nom pour cette paire de clés publiques et privées dans la zone **Nom** afin de les extraire.
- **Important :** Cliquez sur **Exporter une identité** pour télécharger le certificat et la clé sur votre système de fichiers local en tant que simple fichier au format JSON. Vous êtes responsable de la sécurisation et de la gestion de ces clés.
- Cliquez sur **Ajouter une identité au portefeuille** pour ajouter ces certificats au portefeuille de console. Vous pouvez ensuite rechercher le nom et les clés de cette identité dans une nouvelle vignette sous votre [onglet portefeuille](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)

Vous pouvez également utiliser le client d'autorité de certification Fabric ou des logiciels SDK Fabric pour inscrire les identités que vous avez créées sur la console. La console vous fournit toutes les informations dont elle aura besoin pour effectuer ces étapes. Assurez-vous d'avoir sauvegardé l'**ID d'inscription** et le **secret d'inscription** que vous avez indiqués pendant l'enregistrement.

## Utilisation de votre autorité de certification TLS
{: #ibp-console-identities-tlsca}

La communication au sein de votre réseau est sécurisé par des certificats TLS. TLS chiffre la communication entre vos noeuds et entre vos noeuds et vos applications. L'utilisation de TLS permet d'éviter que des pirates interrompent la communication entre vos noeuds ou la lecture des transactions soumises depuis vos applications. Les clés et les certificats utilisés pour TLS sont différents des certificats utilisés pour signer et valider vos transactions et ils sont émis par une autorité de certification distincte.

Chaque autorité de certification créée par la console {{site.data.keyword.blockchainfull_notm}} Platform contient une autorité de certification racine et une autorité de certification TLS. Vous pouvez voir ces deux autorités de certification sous le même nom d'affichage sous l'onglet Noeuds de votre console. Cliquez sur le panneau de présentation de votre autorité de certification puis sur **Autorité de certification TLS** pour utiliser votre autorité de certification TLS. Votre autorité de certification TLS a le [même processus d'inscription](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) et le [même processus d'enregistrement](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) que votre autorité de certification racine. Les deux autorités de certification sont déployées avec le même ID et secret admin d'autorité de certification.

Chaque noeud d'homologue ou de service de tri que vous déployez doit générer un certificat TLS public. Lors de la création d'un noeud à l'aide de la console, vous êtes invité à fournir un ID et un secret d'inscription d'une identité enregistrée auprès de votre autorité de certification TLS. Le [tutoriel Générer votre réseau](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) utilise l'identité admin de l'autorité de certification pour des raisons pratiques. Toutefois, il est recommandé d'enregistrer une identité unique auprès de votre autorité de certification TLS pour chaque noeud. Le noeud utilisera cette identité pour générer son certificat TLS pendant le déploiement. Ce certificat sera requis par toute application qui doit communiquer avec le service de tri ou l'homologue. Vous pouvez trouver le certificat TLS d'un noeud en accédant au panneau de présentation du noeud et en cliquant sur Paramètres. Vous pouvez également trouver les certificats TLS de vos homologues et services de tri dans le profil de connexion qui peut être téléchargée [à partir de l'onglet des contrats intelligents](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).

Lors de la création d'un homologue ou d'un service de tri à l'aide de votre console, vous pouvez également utiliser l'autorité de certification TLS pour spécifier un nom de domaine supplémentaire pour chaque noeud. Entrez le nouveau nom de domaine dans la zone **Nom d'hôte CSR TLS** lors du déploiement de votre homologue ou service de tri. Ce nom d'hôte sera ajouté à la liste des noms communs dans le certificat TLS émis sur votre noeud.


## Expiration du certificat
{: #ibp-console-identities-expiration}

Les certificats générés à l'aide des autorités de certification d'{{site.data.keyword.blockchainfull_notm}} Platform 2.0 expirent au bout d'un an. La période d'expiration est la même pour les certificats qui sont générés par l'utilisation de logiciels SDK Fabric, du client d'autorité de certification Fabric, ou à l'aide de la console. Si les certificats expirent, vos applications ne peuvent plus interagir avec votre réseau et vous devez vous ré-inscrire pour générer de nouveaux certificats. Si vous avez atteint la limite d'inscription d'un utilisateur, vous pouvez en enregistrer un nouveau et procéder à l'inscription.

Vous pouvez utiliser votre ligne de commande pour vérifier la date d'expiration de vos certificats. Tout d'abord, vous devez convertir les certificats qui sont en base64 au format PEM en exécutant la commande suivante sur votre machine locale :

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

Exécutez la commande suivante pour afficher le certificat encodé au format PEM dans une forme lisible :
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

Le certificat ressemblera à l'exemple suivant :

```
Certificate:
    Data:
        Version: 3 (0x2)
    Serial Number:
        20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18
Signature Algorithm: ecdsa-with-SHA256
    Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA
    Validity
        Not Before: Nov 28 19:18:00 2018 GMT
        Not After : Nov 28 19:23:00 2019 GMT
    Subject: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin
    ...
    ...
```

Vous pouvez voir la date d'expiration dans la section **Validity**, à la suite de `Not After:`. Dans cet exemple, le certificat expire le 28 novembre 2019.

## Stockage des identités dans votre portefeuille de console
{: #ibp-console-identities-wallet}

Le portefeuille stocke les identités et les clés utilisées par la console {{site.data.keyword.blockchainfull_notm}} Platform pour exploiter les noeuds de votre réseau. Vous devez ajouter les admin de votre homologue, de votre service et de votre organisation à ce portefeuille pour pouvoir utiliser la console pour gérer les canaux et les contrats intelligents. Vous pouvez également utiliser ce portefeuille pour stocker facilement les identités qui vont être utilisés par vos applications. Vous pouvez utiliser ce portefeuille pour les exporter à tout moment. Utilisez la navigation de gauche pour accéder au panneau de présentation du portefeuille. Vous pouvez ajouter, mettre à jour et exporter des identités depuis ce portefeuille à partir de cet écran.

Le portefeuille est un composant de la console et non un service distinct. Il stocke vos clés dans l'espace de stockage local du navigateur que vous utilisez pour accéder à la console et non sur votre système de fichiers local. Si vous accédez à la console à partir d'un navigateur différent, ou si vous lancez une session de navigation privée, vous ne pourrez pas accéder aux identités stockées dans le portefeuille. **Il est recommandé d'exporter les identités admin à partir de la console et de les stocker en lieu sûr**.
{:important}

### Ajout d'identités
{: #ibp-console-identities-add}

Vous pouvez ajouter une identité admin à votre portefeuille lorsque vous [créez la définition MSP de votre organisation](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Une autorité de certification gérée par la console peut également ajouter une identité à votre portefeuille lors du [processus d'inscription](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

Vous pouvez utiliser le bouton **Ajouter une identité** sur l'écran de présentation pour importer une identité directement dans le portefeuille. Si vous cliquez sur ce bouton, un panneau latéral s'affiche dans lequel vous pouvez ajouter la paire de clés publiques et privées d'une identité.
- **Nom :** Entrez le nom d'une identité qui est utilisé à des fins de référence uniquement.
- **Certificat :** Collez ici la clé publique de l'identité que vous avez générée à l'aide de votre autorité de certification.
- **Clé privée :** Collez ici la clé privée de l'identité que vous avez générée à l'aide de votre autorité de certification.


Vous pouvez également ajouter une identité en téléchargeant un fichier JSON au format ci-dessous. Vous pouvez également utiliser le bouton **Télécharger JSON** pour télécharger plusieurs identités en une seule opération.

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Si vous avez inscrit une identité à l'aide du client d'autorité de certification Fabric ou de logiciels SDK Fabric, vos clés doivent être converties du format PEM au format base64. Vous pouvez convertir les certificats au format base64 en exécutant la commande suivante sur votre machine locale :
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

### Affichage et mise à jour des identités
{: #ibp-console-identities-update-identities}

Depuis l'onglet **Portefeuille**, cliquez sur une vignette afin d'afficher, de mettre, ou de retirer une identité du portefeuille. Il peut être nécessaire de mettre à jour vos identités si leurs certificats ont expiré, et s'ils doivent émettre de nouvelles clés à l'aide de l'autorité de certification. Vous pouvez également utiliser cet onglet pour supprimer des clés depuis votre console et votre système local.

Si vous cliquez sur une identité, un panneau latéral affiche les clés publiques et privées au format base64. Cliquez sur **Exporter** pour exporter les certificats de l'identité sur votre système de fichiers local. Cliquez sur **Mettre à jour** pour modifier le nom de l'identité dans le portefeuille ou pour coller un nouvelle ensemble de clé dans le panneau. Cliquez sur **Retirer** lorsque vous n'avez plus besoin de cette identité et souhaitez supprimer ses clés.

## Association d'identités
{: #ibp-console-identities-associate-admin}

Vous devez fournir la clé publique des admin de votre organisation et de votre noeud [à la définition MSP de votre organisation](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Les noeuds ou les canaux créés par la console utilisent les certificats de la définition MSP pour vérifier qui est un administrateur valide. Par conséquent, la même paire de clés publiques et privées que vous avez utilisée pour ajouter certificat admin à la définition MSP doit être stockée dans votre portefeuille de console.

Lorsque vous utilisez la console pour créer un service de tri ou un homologue, vous devez afficher le panneau **Associer une identité** panel. Sélectionnez dans le portefeuille une identité dont le certificat figure également dans la définition MSP de votre organisation. Vous devrez également sélectionner une identité admin dans la section **Associer une identité** lorsque vous créez ou éditez un canal. Votre console saura ainsi quelle identité utiliser lorsqu'elle communique avec vos homologues, services de tri, ainsi qu'avec le consortium de service de tri.

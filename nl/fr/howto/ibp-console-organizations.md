---

copyright:
  years: 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Gestion des organisations
{: #ibp-console-organizations}

Vous pouvez utiliser la console {{site.data.keyword.blockchainfull}} Platform pour créer une définition d'organisation formelle appelée Membership Services Provider (MSP). La définition MSP de votre organisation permet à d'autres membres du consortium de blockchain de vérifier l'identité de vos noeuds et applications. Votre définition MSP contient également les certificats admin de votre organisation.

Vous pouvez également utiliser la console pour gérer les organisations qui sont les membres de votre réseau. L'administrateur du service de tri peut utiliser l'onglet Organisations pour ajouter des membres au [consortium](/docs/services/blockchain/glossary.html#glossary-consortium) de blockchain. Les membres du consortium peuvent ensuite utiliser la console pour ajouter des membres à des canaux nouveaux ou existants.

**Public cible :** Cette rubrique s'adresse aux opérateurs réseau qui sont responsables de la création, de la surveillance et de la gestion du réseau de blockchain.

## Description des définitions MSP
{: #console-organizations-about-msp}

{{site.data.keyword.blockchainfull_notm}} Platform repose sur Hyperledger Fabric et génère des réseaux de blockchain privés. Les participants doivent être connus du réseau pour pouvoir soumettre des transactions et interagir avec les ressources sur le registre. Fabric reconnaît l'identité via un groupe d'organisations invitées, appelé consortium. Les organisations de ce consortium peuvent émettre des données d'identification valides pour leurs membres et les laisser devenir participants sur le réseau. Ces participants peuvent ensuite exploiter des noeuds de blockchain et soumettre des transactions depuis des applications client.

Chaque organisation du consortium doit utiliser sa propre autorité de certification, appelée autorité de certification racine. Cette autorité de certification (ou ses autorités de certification intermédiaires) crée toutes les identités qui appartiennent à votre organisation et elle émet pour chaque identité une clé publique et une clé privée. Ces clés sont signées par l'autorité de certification et utilisées par les membres de votre organisation pour signer et vérifier leurs actions. Le fait de rejoindre le consortium permet à d'autres organisations de reconnaître la signature de vos autorités de certification, et de vérifier que vos homologues et applications son des participants valides. Pour plus d'informations sur l'appartenance dans Hyperledger Fabric, voir la [rubrique relative au concept d'appartenance ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Appartenance") dans la documentation Fabric.

Pour pouvoir rejoindre un consortium, votre organisation doit créer une définition d'organisation appelée **Membership Services Provider (MSP)**. La définition MSP contient les informations suivantes :
- Un certificat signé par votre **autorité de certification racine**. Ce certificat est utilisé pour vérifier l'identité de vos noeuds, canaux, et applications.
- Un certificat signé par votre **autorité de certification TLS**. Un certificat TLS racine permet à vos homologues de participer à une communication gossip entre organisations, ce qui est nécessaire pour bénéficier des fonctions de [collecte de **données privées** ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) et de [reconnaissance de service ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Reconnaissance de service") d'Hyperledger Fabric.
- **ID MSP**. L'ID MSP est le nom formel de votre organisation au sein du consortium. Vous devez retenir l'ID MSP pour vos noeuds et applications.
- **Certificats admin** de vos identités **Admin homologue** et **Admin org**. Ces certificats sont transmis au service de tri et sont utilisés pour vérifier les identités de votre organisation qui sont autorisées à créer ou à éditer des canaux. Lorsque vous utilisez la console pour créer un service de tri ou un homologue, les certificats admin au sein de la définition MSP sont déployés dans le nouveau noeud. Ces certificats peuvent ensuite être utilisés pour exploiter les homologues ou les services de tri à partir de votre console ou d'une application client.

## Gestion des définitions MSP sur la console

Accédez à l'onglet **Organisations**. Vous pouvez utiliser cet onglet pour [créer une définition MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp) à l'aide d'une autorité de certification qui existe sur votre console. Vous pouvez également utiliser cet onglet pour [importer une définition MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) qui a été créée par une autre organisation.

Vous pouvez afficher toutes les définitions MSP que vous avez créées ou importées sous **Organisations disponibles**. Vous pouvez utiliser les définitions MSP de l'onglet Organisations pour des fonctions importantes sur la console :
- Si vous créez des noeuds d'homologue ou de service de tri, la définition MSP de votre organisation est utilisée pour fournir des certificats admin au nouveau noeud.
- Si vous êtes l'admin d'un noeud de service de tri, vous pouvez utiliser les définitions MSP pour [ajouter de nouvelles organisations au consortium](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).
- Si vous êtes membre du consortium, vous pouvez importer les définitions MSP d'autres membres du consortium sur votre console puis ajouter les membres à des canaux nouveaux ou existants.

## Création d'une définition MSP pour votre organisation
{: #console-organizations-create-msp}

Utilisez l'onglet **Organisations** pour générer une définition MSP pour votre organisation. Lorsque vous cliquez sur **Créer une définition MSP**, un panneau latéral s'affiche dans lequel vous pouvez entrer toutes les informations nécessaires : votre autorité de certification racine, l'ID MSP, ainsi que les administrateurs de votre organisation.

- Utilisez la section **Détails de MSP** du panneau pour sélectionner l'ID MSP de vos organisations. Cet ID est le nom formel que les autres membres du réseau utiliseront pour faire référence à votre organisation. **Sauvegardez votre ID MSP.**

- Utilisez la section **Détails d'autorité de certification racine** pour sélectionner l'autorité de certification racine de votre organisation. Il s'agit de l'autorité de certification que vous avez utilisé (ou que vous allez utiliser) pour créer l'ensemble des identités de noeud et d'application appartenant à votre organisation. Si vous utilisez des autorités de certification intermédiaires, il s'agit de l'autorité de certification utilisée pour créer ces autorités de certification. Sélectionnez votre autorité de certification racine dans la liste des autorités de certification gérées à l'aide de la console. Si vous avez créé une autorité de certification à l'aide de la console, la sélection d'une autorité de certification racine va également créer un certificat racine TLS.

- Vous pouvez également utiliser la section **Détails d'autorité de certification racine** pour générer l'un des certificats admin de vos organisations. Avant de créer la définition MSP de votre organisation, vous devez enregistrer les admin de votre organisation et de votre noeud auprès de votre autorité de certification racine. Vous devez ensuite effectuer les étapes suivantes afin d'utiliser ces identités pour exploiter votre réseau :

  1. Créer une paire de clés publiques et privées pour chaque identité admin.
  2. Fournir la clé publique (certificat) de chaque identité admin dans la définition MSP.
  3. Ajouter l'identité à votre portefeuille de console. Les noeuds ou les canaux créés par la console utilisent les certificats de la définition MSP pour vérifier qui est un administrateur valide. Par conséquent, la même paire de clés publiques et privées que vous avez utilisée pour ajouter un certificat admin à la définition MSP doit être stockée dans votre portefeuille de console.

  Vous pouvez utiliser le panneau **Créer une définition de MSP** pour effectuer ces actions pour l'une de vos identités admin. Après avoir sélectionné votre autorité de certification racine, procédez comme suit dans la section **Génération d'un certificat admin de l'organisation** :
  1. Entrez l'ID et le secret d'inscription d'une identité admin enregistrée auprès de votre autorité de certification racine. Après avoir entré l'ID et le secret d'inscription, choisissez le nom d'affichage de l'identité dans votre portefeuille de console.
  2. Cliquez sur **Générer**. Un nouvel ensemble de clés publiques et privées est généré et les clés sont ajoutées automatiquement à votre portefeuille de console. Vous pouvez ensuite rechercher votre identité admin dans votre portefeuille en utilisant le nom que vous avez sélectionné sur ce panneau. Ces clés sont stockées dans l'espace de stockage local de votre navigateur ; par conséquent, si vous changez de navigateur, elles ne seront pas dans votre portefeuille de console. Vous devrez exporter les identités depuis votre navigateur d'origine et les importer dans le portefeuille de console de votre nouveau navigateur.
  3. Ensuite, cliquez sur **Exporter** pour télécharger la paire de clés sur votre système de fichiers et la sécuriser.

- La section **Administrateurs (facultative)** du panneau latéral contient les clés publiques de vos admins. Le certificat que vous avez généré à partir de la section **Génération d'un certificat admin de l'organisation** figure sur la première ligne de la zone **admin certificate**. Si vous souhaitez utiliser plusieurs identités pour l'exploitation de votre réseau, vous pouvez coller les certificats des admins de noeud ou d'organisation supplémentaires dans les zones **admin certificate**.

Comme vos certificats admin sont transmis à vos noeuds et canaux à l'aide de la définition MSP, vous devez vous assurer que chacun de vos certificats admin de noeud ou d'organisation est stocké dans la définition MSP. Lorsque vous utilisez la console pour créer un service de tri, un homologue ou un canal, vous devez **associer** l'une des identités exportées dans votre portefeuille de console aux certificats admin qui ont été fournis à la définition MSP. Lorsque vous êtes dans une section ou un panneau **Associer une identité**, sélectionnez une identité que vous avez générée et sauvegardée dans le portefeuille lors de la création de la définition MSP.

Une fois que vous avez sélectionné votre autorité de certification racine, l'ID MSP et créé vos certificats admin, cliquez sur **Créer une définition MSP** pour créer la définition MSP. Elle est désormais répertoriée en tant qu'organisation sous l'onglet Organisations. Vous utiliserez cette définition MSP lorsque vous déploierez vos noeuds et rejoindrez un consortium de blockchain.

## Génération manuelle d'un fichier JSON MSP
{: #console-organizations-build-msp}

**Cette option est destinée aux utilisateurs expérimentés uniquement qui connaissent bien l'utilisation des certificats dans la gestion des identités de blockchain.**

Si vous préférez utiliser des certificats pour votre homologue ou service de tri à partir d'une **autorité de certification externe**, qui n'est pas hébergée par {{site.data.keyword.IBM_notm}}, vous devez générer un fichier JSON de définition MSP qui représente la définition MSP de l'organisation de l'homologue ou service de tri. Créez un fichier JSON en utilisant le format suivant :

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name** : indiquez un nom à utiliser pour identifier cette définition MSP sur la console.
- **organization_id** : indiquez un ID utilisé pour représenter cet MSP en interne sur la console.
- **root_certs** : (facultatif) collez un tableau contenant un ou plusieurs certificats racines à partir de l'autorité de certification externe au format `base64`. Vous devez fournir un certificat racine d'autorité de certification ou un certificat d'autorité de certification intermédiaire ; vous pouvez aussi fournir les deux.
- **intermediate_certs** : (facultatif) collez un tableau contenant un ou plusieurs certificats à partir de l'autorité de certification intermédiaire externe au format `base64`. Vous devez fournir un certificat racine d'autorité de certification ou un certificat d'autorité de certification intermédiaire ; vous pouvez aussi fournir les deux.
- **admins** : collez le certificat signataire de l'admin de l'organisation au format `base64`.
- **tls_root_certs**: (facultatif) collez un tableau contenant un ou plusieurs certificats racines à partir de l'autorité de certification TLS au format `base64`. Vous devez fournir un certificat racine d'autorité de certification TLS externe ou un certificat d'autorité de certification TLS intermédiaire externe ; vous pouvez aussi fournir les deux.
- **tls_intermediate_certs** : (facultatif) collez un tableau contenant un ou plusieurs certificats de l'autorité de certification TLS intermédiaire au format `base64`. Vous devez fournir un certificat racine d'autorité de certification TLS externe ou un certificat d'autorité de certification TLS intermédiaire externe ; vous pouvez aussi fournir les deux.  

Les zones supplémentaires suivantes sont également disponibles dans votre définition MSP mais elles ne sont pas obligatoires :
- **organizational_unit_identifiers** : liste des unités organisationnelles que les membres valides de cet MSP doivent inclure dans leur certificat X.509. Il s'agit d'un paramètre de configuration facultatif qui est utilisé lorsque plusieurs organisations optimisent la même racine de confiance et des autorités de certification intermédiaires, et qu'elles ont réservé une zone d'unité organisationnelle pour leurs membres. Une organisation est souvent divisée en plusieurs unités organisationnelles, chacune ayant un certain nombre de responsabilités. Par exemple, l'organisation ORG1 peuvent avoir les unités organisationnelles ORG1-MANUFACTURING et ORG1-DISTRIBUTION pour refléter ces différents secteurs d'activité. Lorsqu'une autorité de certification émet des certificats X.509, la zone Unité organisationnelle du certificat indique le secteur d'activité auquel appartient l'identité. Pour plus d'informations, consultez cette rubrique dans la Fabric [Identity Classification ![Icône de lien externe](../images/external_link.svg "Icône de lien externe") ](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "Identity Classification").  
- **fabric_node_OUs** : unités organisationnelles spécifiques à Fabric qui permettent la classification des identités. `NodeOUs` contient des informations sur la manière de distinguer les clients, les homologues et les services de tri en fonction de l'unité organisationnelle. Si la vérification est imposée, par la définition de Activé sur true, le MSP considère qu'une identité est valide s'il s'agit de l'identité d'un `client`, d'un `homologue` ou d'un `service de tri`. Une identité doit avoir uniquement l'une de ces unités organisationnelles spéciales. Consultez la rubrique contenant un exemple d'indication de `fabric_node_OU` dans un MSP dans la [documentation Fabric Service Discovery![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query).
- **revocation_list** : liste des certificats qui ne sont plus valides. Pour les identités basées sur X.509, ces identificateurs sont des paires de chaînes, appelées identificateur de clé de sujet (SKI) et identificateur d'accès de droits (AKI), et ils sont activés chaque fois que le certificat X.509 est utilisé pour vérifier que le certificat n'est pas révoqué. Consultez la documentation Fabric pour plus d'informations sur les [listes de révocation de certificat![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "Revoking a certificate or identity").

Par exemple, votre fichier JSON peut ressembler à ceci :

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

Notez que tous les certificats doivent être codés au format base64.
{:important}

Vous pouvez convertir le contenu de votre fichier certificat, `<cert.pem>` du format `PEM` en une chaîne base64, en exécutant la commande suivante sur votre machine locale :

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}

Sauvegardez cette définition dans un fichier `JSON`.  

Vous avez créé une définition MSP, qui définit l'organisation des noeuds de votre homologue ou service de tri et utilisé les certificats d'une autorité de certification externe. Vous pouvez maintenant revenir aux instructions qui décrivent [Comment utiliser les certificats d'une autorité de certification externe avec votre homologue ou service de tri](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

## Importation d'une définition MSP
{: #console-organizations-import-msp}

Seul l'admin du service de tri peut ajouter de nouvelles organisations au consortium. Si vous êtes l'admin du service de tri, vous devez collecter les définitions MSP de toutes les organisations qui ont été invités à rejoindre le consortium et importer ces définitions MSP sur la console. Vous pouvez ensuite ajouter les définitions MSP au service de tri, à l'aide du noeud de service de tri.

Une fois qu'un administrateur a créé une définition MSP, il peut utiliser l'onglet Organisations pour télécharger la définition MSP au format JSON sur son système de fichiers local. Il peut ensuite envoyer le fichier JSON MSP dans une opération hors bande. Accédez à l'onglet **Organisations** et utilisez **Importer une définition de MSP** pour importer le fichier MSP sur votre console. Dès qu'une définition MSP est visible dans la section **Organisations disponibles**, vous pouvez accéder à votre noeud de service de tri pour [ajouter l'organisation au consortium](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Ajout d'une organisation à un consortium
{: #console-organizations-add-consortium}

Le consortium des organisations est hébergé par le service de tri.

Si vous êtes l'administrateur du noeud de service de tri, vous pouvez utiliser la console pour ajouter une organisation au consortium. Accédez à l'onglet **Noeuds** et cliquez sur le noeud de service de tri. Sur le panneau du noeud de service de tri, sous **Membres du consortium**, cliquez sur **Ajouter une organisation**. Un panneau latéral s'affiche qui vous permet d'effectuer votre sélection dans la liste des définitions MSP disponibles que vous avez [importées dans votre onglet Organizations](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). Vous pouvez également utiliser l'option **Télécharger JSON** pour importer le fichier de définition MSP créé par une autre organisation directement.

## Création et édition d'un canal
{: #console-organizations-create-channel}

Une fois qu'une organisation est ajoutée au consortium, elle peut utiliser le service de tri pour créer un nouveau canal ou être ajoutée à un canal. Les informations qui vous permettent de participer à un canal, comme joindre vos homologues au canal, l'instanciation de contrats intelligents, et la soumission de transactions, sont fournies à l'aide de définitions MSP.

Une fois que votre organisation est ajoutée à un consortium, vous pouvez créer un canal à l'aide de la procédure suivante :

1. Importez le noeud de service de tri qui héberge le consortium sur votre console. Il n'est pas nécessaire que vous soyez administrateur du noeud de service de tri, mais vous devez fournir le nom du noeud de service de tri et les informations de noeud final sur votre console.
2. Importez les définitions MSP des organisations que vous voulez ajuter au nouveau canal sur votre console à partir de l'onglet **Organisations**. **Remarque ** : Ces organisations doivent être ajoutées au consortium pour pouvoir être ajoutées à un canal.
3. Accédez à l'onglet **Canaux** et cliquez sur **Créer un canal**. Un panneau latéral s'affiche qui vous permet d'indiquer le nom du canal, l'appartenance et les règles de canal. Vous pouvez ajouter des organisations qui ont été ajoutées au consortium sur le nouveau canal.

Pour plus d'informations sur ces étapes, voir la section relative à la [création d'un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) dans le tutoriel **Générer un réseau**.

### Mise à jour d'un MSP dans une définition de canal
{: #console-organizations-update-channel}

Au fil du temps, vous devrez peut-être mettre à jour les certificats dans une définition MSP qui est déjà associée à un canal. Lorsque cette situation se présentera, suivez les étapes de mise à jour de la définition MSP d'une organisation dans le canal :  

1. Accédez à l'onglet **Canaux** sur votre console.
2. Cliquez sur le canal qui contient le MSP d'organisation que vous voulez partition de service de transfert et ouvrez-le.
3. Cliquez sur l'onglet **Détails du canal**.
4. Cliquez sur la vignette du membre de canal associé que vous voulez mettre à jour.
5. Si vous n'avez pas déjà importé la définition MSP mise à jour sur la console, vous pouvez télécharger le fichier ici. **Remarque :** Cette action ne mettra pas à jour la définition MSP associée sous l'onglet Organisations. Si vous avez déjà mis à jour la définition MSP sous l'onglet Organisations de la console, vous pouvez la sélectionner dans la liste déroulante.

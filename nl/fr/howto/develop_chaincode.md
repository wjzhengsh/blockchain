---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Ecriture de smart contracts

***[Cette page est-elle utile ? Dites-nous.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Le code blockchain, également désigné sous l'appellation de smart contracts, est un logiciel qui vous permet de lire et de mettre à jour des données dans le registre de blockchain. Le code blockchain peut transformer la logique applicative en programme exécutable accepté et vérifié par tous les membres du réseau de blockchain. La logique applicative inclut la définition de ressources échangées entre les parties. Elle comporte également les dispositions nécessaires à l'exécution d'une transaction. La conversion de ces règles en code dans une blockchain permet aux entreprises de rationaliser les processus et les audits métier et de réduire les énormes volumes de traitement manuel et de paperasses.

Supposons, par exemple, qu'un réseau de concessionnaires automobiles, de compagnies d'assurances et d'organismes de réglementation gouvernementaux, décident d'utiliser la technologie blockchain pour suivre la propriété des véhicules. Le code blockchain peut nécessiter que tous les véhicules possèdent un numéro d'immatriculation et d'identification du véhicule valides pour pouvoir être ajoutés au réseau. Lors de la vente d'un véhicule, le code blockchain peut nécessiter que les fonds soient placés sur un compte bloqué, jusqu'à ce que le véhicule soit enregistré auprès de son nouveau propriétaire par un régulateur. Une fois le nouveau enregistrement effectué, le nouveau propriétaire sera enregistré et les fonds transférés automatiquement.

Le tutoriel ci-après vous permet d'aborder les concepts de base relatifs à la génération de code blockchain :

- [Comment commencer à écrire du code blockchain](#write-chaincode)
- [Relations entre le code blockchain et les données](#install-chaincode)
- [Transactions entre codes blockchain](#cross-chaincode-transactions)

Le tutoriel présente également des aspects importants de Fabric qui sont accessibles via le code blockchain :

- [Données personnelles
](#private-data)
- [Utilisation des index avec couchDB](#indexes)

## Ecriture de code blockchain
{: #write-chaincode}

Le code blockchain peut être écrit dans différents langages et le {{site.data.keyword.blockchainfull_notm}} Platform prend en charge le code blockchain écrit en Go et Node.js. Il permet aux utilisateurs d'interroger et de modifier des données qui sont stockées dans le code blockchain à l'aide d'API fournies par l'interface de blockchain Fabric. Les données de la blockchain sont stockées dans des paires clé/valeur dans le World State du [registre![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "ledger") de canal. Le code blockchain utilise des commandes get pour extraire des valeurs et des commandes put pour créer ou mettre à jour des valeurs. Grâce à ces opérations de base, vous pouvez générer des fonctions qui définissent les règles métier de votre réseau. Ces fonctions peuvent être appelées par vos applications et présentées aux utilisateurs finaux du réseau. Pour continuer à utiliser l'exemple de réseau de véhicules, vous pouvez créer une fonction qui autorise un concessionnaire automobile à utiliser une commande put pour ajouter une nouvelle voiture au registre uniquement si un numéro d'identification du véhicule valide est fourni.

Pour apprendre à commencer à écrire du code blockchain, consultez le [tutoriel Chaincode for developers![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "tutoriel Chaincode for developers") dans la documentation Hyperledger Fabric Community. Ce tutoriel vous guide tout au long des étapes de construction d'un exemple de code blockchain qui crée et lit des ressources et vous présente les API qui sont utilisées au cours du processus. Vous pouvez également trouver le guide de référence des API de code blockchain pour tous les langages de code blockchain. Des exemples supplémentaires sont fournis dans le dossier chaincode du référentiel[Référentiel Fabric samples![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-samples "Fabric samples").

## Installation du code blockchain
{: #install-chaincode}

Etant donné que le code blockchain fournit la structure des transactions sur un canal, il doit être installé sur tous les homologues joints sur le canal qui souhaitent utiliser le code blockchain pour mettre à jour ou interroger le registre de canal. Ensuite, un membre du canal peut instancier le code blockchain sur un canal et définir la règle d'adhésion du code blockchain. L'installation et l'instanciation du code blockchain peuvent être effectuées à l'aide de l'interface utilisateur du Moniteur réseau, de l'interface de ligne de commande de Fabric Peer ou à partir d'une application client utilisant le logiciel[Fabric SDK](../v10_application.html#operate-sdk). Pour savoir comment utiliser l'interface utilisateur du Moniteur réseau pour déployer du code blockchain, consultez [Installation, instanciation et mise à jour d'un code blockchain](install_instantiate_chaincode.html).

## Code blockchain et données
{: #chaincode-data}

Chaque canal ne peut avoir qu'un seul registre, et les données de ce registre sont partitionnées par une clé unique et le code blockchain qui a ajouté la paire clé-valeur au registre. Les membres peuvent uniquement lire ou mettre à jour les données du registre de canal à l'aide de la clé appropriée et du code blockchain associé. Les données auxquelles un code blockchain peut accéder sont appelées espace de nom du code blockchain et l'ensemble des données du registre figurent dans l'espace de nom d'un code blockchain. Un code blockchain peut interagir avec des données en dehors de son espace de nom uniquement à l'aide d'une [transaction entre codes blockchain](#cross-chaincode-transactions) pour que le code blockchain puisse accéder aux données pertinentes.

Nous pouvons utiliser le code blockchain fabcar du référentiel d'exemples Fabric comme exemple de l'interaction du code blockchain avec les données. L'espace de nom est créé lorsque le smart contract est instancié. Certains codes blockchain acceptent un ensemble d'arguments de paire clé-valeur lorsque le code blockchain est instancié et ils utilisent ces données pour initialiser l'espace de nom. Le code blockchain fabcar du référentiel d'exemples Fabric n'accepte aucun argument lorsqu'il est instancié. Pour fabcar, vous devez ajouter des données  à l'espace de noms à l'aide de la fonction initLedger ou createCar. Par exemple, la transmission de l'argument `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` fait de la paire clé-valeur  `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` l'espace de nom fabcar.

Seul le code blockchain fabcar peut modifier ces données. Supposons que l'utilisateur souhaite  transférer la voiture à un nouveau propriétaire à l'aide de la fonction  changeCarOwner ci-dessous :
```
func (s *SmartContract) changeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {

	if len(args) != 2 {
		return shim.Error("Incorrect number of arguments. Expecting 2")
	}

	carAsBytes, _ := APIstub.GetState(args[0])
	car := Car{}

	json.Unmarshal(carAsBytes, &car)
	car.Owner = args[1]

	carAsBytes, _ = json.Marshal(car)
	APIstub.PutState(args[0], carAsBytes)

	return shim.Success(nil)
}
```
{:codeblock}

Vous pouvez appeler le code blockchain à l'aide des arguments suivants :
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

le code blockchain utilise ensuite les commandes get pour générer un ensemble de lectures à partir de  l'espace de nom du code blockchain. Il utilise les commandes put pour créer l'ensemble d'écritures de  transaction. L'appel de code blockchain crée également un ID transaction unique.
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Chaque homologue de validation sur lequel est installé le code blockchain va  simuler les transactions à l'aide du jeu de lectures et du jeu d'écritures. Si les  simulations de chaque homologue sont cohérentes, elles sont considérées comme étant valides et elles peuvent être  validées dans le registre. Après la  transaction, l'espace de nom fabcar devient `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`. Notez que le propriétaire est passé de  Tom à Mark.

La relation 1-1 entre le code blockchain et les données crée d'importantes  considérations pour le développement de réseaux et d'applications. Le logiciel de code blockchain ne doit être mis à jour que par l'installation d'un code blockchain de même nom et de version différente, et non par l'utilisation d'un nouveau code blockchain. Cette procédure de mise à jour préserve l'espace de nom  du code blockchain et vous permet de continuer à utiliser les données existantes. Vous pouvez aussi utiliser un code blockchain différent pour retreindre l'accès aux données sensibles, ou lire et écrire des données en parallèle.

## Transactions entre codes blockchain
{: #cross-chaincode-transactions}

Vous pouvez utiliser un code blockchain pour appeler d'autres codes blockchain. Un code blockchain peut ainsi interroger et écrire des données à l'extérieur de son espace de nom. Un code blockchain peut à la fois lire et mettre à jour des données à l'aide d'un code blockchain instancié sur le même canal. Il peut cependant uniquement interroger des données à l'aide d'un code blockchain sur différents canaux. L'organisation qui appelle le code blockchain d'origine doit être autorisé à appeler le code blockchain qui est la cible de la transaction.

Par exemple, vous pouvez utiliser la fonction `crossChaincodeChangeCarOwner` ci-dessous pour appeler fabcar depuis un autre code blockchain. Etant donné que a fonction met à jour les données en changeant le propriétaire de la voiture, elle doit appeler un code blockchain fabcar instancié sur le même canal. 
```
func (s *SmartContract) crossChaincodeChangeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {
	newOwnerArgs := toChaincodeArgs("changeCarOwner", args[0], args[1])

	chaincodeName := "fabcar"
	channelName := "defaultchannel"

	APIstub.InvokeChaincode(chaincodeName, newOwnerArgs, channelName)

	return shim.Success(nil)
}
```
{:codeblock}

Si vous créez un nouveau contrat à l'aide de ce code et appelez la fonction `crossChaincodeChangeCarOwner`, la  transaction générera un ID transaction et un ensemble d'écritures.
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Ce TXid et cet ensemble d'écritures sera transmis au code blockchain fabcar.
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

Etant donné que `fabcar` est sur le même canal que `newContract`, la fonction `crossChaincodeChangeCarOwner` est autorisée à écrire dans l'espace de nom `fabcar`. Le nouvel espace de nom `fabcar` sera `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`.

## Données privées
{: #private-data}

Les collectes de données privées sont une fonction des réseaux Hyperledger Fabric en version 1.2 ou suivante. Les canaux permettent à des groupes d'organisations d'effectuer des transactions sans exposer les données à d'autres organisations. Cependant, certaines organisations souhaitent parfois conserver la confidentialité d'informations sensibles, en n'exposant pas les données à d'autres membres du canal. Dans ce cas, elles peuvent utiliser les collectes de données privées pour stocker des informations uniquement sur les homologues des organisations qui ont besoin de ces données. Par exemple, un grossiste et une ferme peuvent, s'ils le souhaitent, utiliser le même canal que d'autres fermes comme preuve de vente. Ils peuvent toutefois utiliser une collecte privée pour conserver la confidentialité des aspects sensibles de la vente, le prix par exemple. Pour en savoir plus sur l'utilisation de données privées au sein d'une blockchain, consultez l'article relatif au concept de  [Données privées![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "Données privées") dans la documentation Fabric.

Vous pouvez utiliser des données privées sur la plateforme  {{site.data.keyword.blockchainfull_notm}} dès lors que votre réseau est en version 1.2 ou suivante de Fabric. Vous pouvez ajouter un fichier de configuration de collecte de données privée à votre code blockchain avant de l'installer sur votre homologue. Vous pouvez ensuite utiliser des API de code blockchain spécifiques aux données privées pour entrer et extraire des données de la collection.

Pour plus d'informations sur l'utilisation de collectes de données privées avec votre code blockchain, voir  [Using private data ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "Using private data") dans la documentation Fabric. Vous ne pouvez pas utiliser le Moniteur réseau pour installer du code blockchain avec un fichier de configuration de collecte. Vous pouvez néanmoins utilise les logiciels SDK pour installer, instancier ou mettre à jour un code blockchain qui utilise des données privées. Pour plus d'informations, voir [How to use private data ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://fabric-sdk-node.github.io/release-1.3/tutorial-private-data.html "how to use private data") dans la documentation Node SDK. **Remarque :** Vous devez télécharger le certificat signCert du logiciel SDK sur la plateforme {{site.data.keyword.blockchainfull_notm}} afin de pouvoir utiliser les logiciels SDK pour  installer et instancier un code blockchain sur votre homologue. Pour plus d'informations sur l'interaction avec votre réseau et son exploitation avec les logiciels Fabric, voir [Développement d'applications](../v10_application.html) et [Exploitation de votre réseau à l'aide du logiciel SDK](../v10_application.html#operate-sdk).

## Utilisation des index avec CouchDB
{: #indexes}

Si les données de votre registre sont stockées dans CouchDB, nous vous recommandons fortement de créer des index pour vos requêtes CouchDB et de les utiliser dans votre code blockchain. Les index permettent à vos applications d'extraire les données de manière aussi efficace que votre réseau ajoute des blocs de transaction et des entrées supplémentaires dans le World State. CouchDB vous permet également d'effectuer des requêtes de données enrichies à partir de vos code blockchain sur les données d'un registre de canal.

Pour plus d'informations sur CouchDB et la façon de définir des index, voir [CouchDB as the State Database ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} dans la documentation Hyperledger Fabric. Vous trouverez également un exemple utilisant un index avec un code blockchain dans le[tutoriel Fabric CouchDB![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html). Consultez la section relative aux [meilleures pratiques lors de l'utilisation de  CouchDB](../v10_application.html#couchdb-indices) dans le tutoriel Développement d'applications pour plusd'informations sur l'interrogation de données depuis vos applications.

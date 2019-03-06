---

copyright:
  years: 2017, 2019
lastupdated: "2018-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# スマート・コントラクトの作成
{: #develop-smart-contracts}

***[このページは参考になりましたか。 ご意見をお聞かせください。](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

チェーンコードはスマート・コントラクトとも呼ばれ、ブロックチェーン台帳のデータの読み取りおよび更新を可能にするソフトウェアです。 チェーンコードは、ブロックチェーン・ネットワークのすべてのメンバーによって合意および検証された実行可能プログラムにビジネス・ロジックを変換できます。 ビジネス・ロジックには、団体間で取引される資産の定義が含まれます。 また、トランザクションの実行に必要な使用条件も含まれています。 これらのルールをブロックチェーン上のコードに変換することにより、企業はビジネス・プロセスと監査を合理化して、手動処理と書類作業を大幅に削減できます。

例として、車両所有権を追跡するためにブロックチェーンを使用することに決めた、自動車ディーラー、保険会社、および政府規制当局のネットワークを考えます。 チェーンコードでは、ネットワークに追加するために、すべての車両に有効な登録および車両識別番号が必要です。 車両が販売されると、チェーンコードでは、その車両が規制当局によって新しい所有者に登録されるまで、代金をエスクローに預託するよう要求します。 新規登録が完了すると、新しい所有者が記録され、代金が自動的に送金されます。

以下のチュートリアルでは、以下を含む、チェーンコードの作成の基本的な側面について説明します。

- [チェーンコードの作成を開始する方法](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-write)
- [チェーンコードとデータの関係](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-data)
- [チェーンコード間トランザクション](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)

このチュートリアルでは、チェーンコードを介してアクセス可能なファブリックの重要な側面についても紹介します。

- [couchDB での索引の使用](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-indexes)

## チェーンコードの作成
{: #develop-smart-contracts-write}

チェーンコードは複数の言語で作成でき、{{site.data.keyword.blockchainfull_notm}} Platform は Go および Node.js で作成されたチェーンコードをサポートしています。 チェーンコードにより、ユーザーは、Fabric チェーンコード・インターフェースが提供する API を使用して、ブロックチェーンに保管されているデータを照会および変更できます。 ブロックチェーン上のデータは、チャネル[台帳 ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "台帳") のワールド・ステートにキーと値のペアとして格納されます。 チェーンコードでは、get コマンドを使用して値を取得し、put コマンドを使用して値を作成または更新します。 これらの基本操作を使用して、ネットワークのビジネス・ルールを定義する関数を作成できます。 これらの関数は、アプリケーションから呼び出すことができ、ネットワークのエンド・ユーザーに表示されます。 引き続き車両ネットワークの例を使用すると、自動車ディーラーが有効な車両 ID 番号を提供した場合にのみ、put コマンドを使用して新しい車両を台帳に追加できるようにする関数を作成できます。

Hyperledger Fabric コミュニティー資料の[開発者向けチェーンコードのチュートリアル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "開発者向けチェーンコードのチュートリアル") では、チェーンコードの作成方法を学習できます。 このチュートリアルでは、資産の作成と読み取りを行う単純なチェーンコードの作成を順を追って説明し、このプロセスで使用されている API を紹介しています。 すべてのチェーンコード言語のチェーンコード API リファレンス・ガイドも参照できます。 [Fabric サンプル・リポジトリー ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://github.com/hyperledger/fabric-samples "Fabric サンプル") の chaincode フォルダーに、さらにサンプルが用意されています。

## チェーンコードのインストール
{: #develop-smart-contracts-install}

チェーンコードはチャネルでのトランザクションの構造を提供するため、チェーンコードを使用してチャネル台帳を更新または照会するチャネルに参加しているすべてのピアにチェーンコードをインストールする必要があります。 こうすると、チャネルのいずれかのメンバーがチャネル上でチェーンコードをインスタンス化し、チェーンコードのエンドースメント・ポリシーを設定できます。 チェーンコードのインストールおよびインスタンス化は、ネットワーク・モニター UI または Fabric ピアのコマンド・ライン・インターフェースを使用して実行できるほか、[Fabric SDK](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk) を使用してクライアント・アプリケーションから実行できます。 ネットワーク・モニター UI を使用してチェーンコードをデプロイする方法については、[チェーンコードのインストール、インスタンス化、および更新](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode)を参照してください。

## チェーンコードおよびデータ
{: #develop-smart-contracts-data}

各チャネルには台帳が 1 つのみあり、その台帳のデータは、固有のキーと、キーと値のペアを台帳に追加したチェーンコードによってパーティション化されます。 メンバーは、正しいキーおよび関連するチェーンコードを使用して、チャネル台帳のデータの読み取りまたは更新のみを行うことができます。 チェーンコードがアクセスできるデータは、そのチェーンコードの名前空間と呼ばれ、台帳のすべてのデータは 1 つのチェーンコードの名前空間内にあります。 チェーンコードは、関連データにアクセスできるチェーンコードへの[チェーンコード間トランザクション](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)を使用することによってのみ、その名前空間外のデータと対話できます。

チェーンコードがデータと対話する方法の例として、Fabric サンプル・リポジトリーの fabcar チェーンコードを使用できます。 名前空間は、スマート・コントラクトがインスタンス化されるときに作成されます。 一部のチェーンコードは、そのチェーンコードがインスタンス化されたときにキーと値のペアの引数のセットを受け入れ、そのデータを使用して名前空間を初期化します。 Fabric サンプル・リポジトリーの fabcar チェーンコードは、インスタンス化されたときに引数を受け入れません。 fabcar では、initLedger 関数または createCar 関数を使用して名前空間にデータを追加する必要があります。 例えば、引数 `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` を渡すと、キーと値のペアである `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` が fabcar 名前空間になります。

fabcar チェーンコードのみがこのデータを変更できます。 以下の changeCarOwner 関数を使用して、ユーザーが自動車を新しい所有者に移譲するとします。
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

以下の引数を使用して、チェーンコードを呼び出すことができます。
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

次に、チェーンコードは get コマンドを使用して、チェーンコード名前空間から読み取りセットを生成します。 また、put コマンドを使用して、トランザクション書き込みセットを作成します。 チェーンコード呼び出しによって、固有のトランザクション ID も作成されます。
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

チェーンコードがインストールされた各エンドース・ピアは、読み取りセットと書き込みセットを使用してトランザクションをシミュレートします。 各ピアによるシミュレーションに一貫性がある場合、トランザクションは有効と見なされ、台帳にコミットできます。 トランザクションの後、fabcar 名前空間は `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}` になります。 所有者が Tom から Mark に変更されたことに注意してください。

チェーンコードとデータの間に 1 対 1 の関係があることで、ネットワークおよびアプリケーションの開発に関する重要な考慮事項が生じます。 チェーンコード・ソフトウェアは、新しいチェーンコードを使用するのではなく、同じ名前でバージョンが異なるチェーンコードをインストールすることによって更新する必要があります。 この更新手順により、チェーンコード名前空間が保持され、ユーザーは既存のデータを引き続き使用することができます。 また、異なるチェーンコードを使用して機密データへのアクセスを制限したり、データの読み取りと書き込みを並行して行ったりすることもできます。

## チェーンコード間トランザクション
{: #develop-smart-contracts-cross-chaincode}

チェーンコードを使用して、他のチェーンコードを呼び出すことができます。 これにより、チェーンコードは、その名前空間外のデータを照会して書き込むことができます。 チェーンコードは、同じチャネルでインスタンス化されたチェーンコードを使用して、データの読み取りと更新の両方を行うことができます。 ただし、異なるチャネルのチェーンコードを使用した場合に実行できるのはデータの照会のみです。 元のチェーンコードを呼び出す組織には、トランザクションのターゲットであるチェーンコードを呼び出す権限が必要です。

例えば、以下の `crossChaincodeChangeCarOwner` 関数を使用して、別のチェーンコードから fabcar を呼び出すことができます。 この関数は自動車の所有者を変更してデータを更新するため、同じチャネルでインスタンス化された fabcar チェーンコードを呼び出す必要があります。
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

このコードを使用して新しい契約を作成し、関数 `crossChaincodeChangeCarOwner` を呼び出した場合、トランザクションによって新しいトランザクション ID と書き込みセットが生成されます。
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

この TXid および書き込みセットが、fabcar チェーンコードに渡されます。
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

`fabcar` は `newContract` と同じチャネル上にあるため、`crossChaincodeChangeCarOwner` 関数は `fabcar` 名前空間に書き込むことができます。 新しい `fabcar` 名前空間は `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}` になります。


## CouchDB での索引の使用
{: #develop-smart-contracts-indexes}

状態データベースに CouchDB を使用している場合、チェーンコードからチャネルの状態データに対する JSON データ照会を実行できます。JSON 照会用に索引を作成してチェーンコードで使用することを強くお勧めします。索引を使用すると、ネットワークがトランザクションおよびエントリーの追加ブロックをワールド・ステートに追加するため、アプリケーションで効率的にデータを取り出すことができます。

CouchDB について、および索引をセットアップする方法について詳しくは、Hyperledger Fabric の資料で [CouchDB as the State Database ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB as the State Database"){:new_window} を参照してください。 [Fabric CouchDB チュートリアル ![外部リンク・アイコン](../images/external_link.svg "外部リンク・アイコン")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html) にも、索引をチェーンコードで使用する例があります。 アプリケーションからデータを照会する方法について詳しくは、アプリケーションの開発チュートリアルの [CouchDB を使用する場合のベスト・プラクティス](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices)を参照してください。

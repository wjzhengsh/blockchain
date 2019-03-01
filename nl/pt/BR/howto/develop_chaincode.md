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

# Gravando contratos inteligentes
{: #develop-smart-contracts}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Chaincode, também conhecido como contratos inteligentes, é o software que permite ler e atualizar dados no livro-razão do blockchain. O chaincode pode transformar a lógica de negócios em um programa executável acordado e verificado por todos os membros da rede de blockchain. A lógica de negócios inclui a definição de ativos negociados entre partes. Ela também consiste nos termos e condições necessários para que uma transação seja executada. Transformar essas regras em código em uma blockchain permite que as empresas simplifiquem o processamento de negócios e a auditoria e reduzam grandes quantidades de processamento manual e papelada.

Como exemplo, imaginem que uma rede de concessionárias de automóveis, companhias de seguros e reguladores do governo decidiram usar o blockchain para rastrear a propriedade do veículo. O chaincode pode exigir que todos os veículos tenham um registo válido e um número de identificação do veículo, a fim de serem incluídos na rede. Quando um veículo é vendido, o chaincode pode exigir que os fundos sejam colocados em caução até que o veículo esteja registado em nome do seu novo proprietário por um regulador. Quando o novo registro estiver concluído, o novo proprietário será registrado e os fundos serão transferidos automaticamente.

O tutorial a seguir o levará por meio dos aspectos básicos da construção do chaincode, incluindo:

- [ Como Cometer a Gravação de Chaincode ](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-write)
- [O relacionamento entre o chaincode e os dados](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-data)
- [Transações de chaincode cruzado](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)

O tutorial também apresenta aspectos importantes da malha que estão acessíveis por meio do chaincode:

- [Usando índices com o CouchDB](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-indexes)

## Gravando o chaincode
{: #develop-smart-contracts-write}

O chaincode pode ser gravado em várias linguagens e o {{site.data.keyword.blockchainfull_notm}} Platform suporta o chaincode gravado em Go e Node.js. O chaincode permite que os usuários consultem e mudem dados que estão armazenados no blockchain usando APIs que a interface do Fabric Chaincode fornece. Os dados no blockchain são armazenados em pares de chave-valor no estado mundial do [livro-razão do canal ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "livro-razão"). O chaincode usa comandos get para recuperar valores e usa comandos put para criar ou atualizar valores. Usando essas operações básicas, é possível construir funções que definem as regras de negócios de sua rede. Essas funções podem ser chamadas por seus aplicativos e exibidas para usuários finais da rede. Para continuar a usar o exemplo de rede de veículo, é possível criar uma função que permita que uma concessionária de carros use um comando put para incluir um novo carro no livro-razão somente se for fornido um número de ID de veículo válido.

É possível aprender como iniciar a gravação de chaincode visitando o tutorial [Chaincode para desenvolvedores ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "Chaincode para desenvolvedores") na documentação da comunidade do Hyperledger Fabric. O tutorial o levará por meio da construção de um chaincode simples que cria e lê ativos e apresenta a você quais APIs são usadas no processo. Também é possível localizar o guia de referência da API do chaincode para todas as linguagens do chaincode. Há exemplos adicionais na pasta chaincode do [Repositório de amostras do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric-samples "Amostras do Fabric").

## Instalando o chaincode
{: #develop-smart-contracts-install}

Como o chaincode fornece a estrutura de transações em um canal, um chaincode precisa ser instalado em todos os peers associados ao canal que desejam usar o chaincode para atualizar ou consultar o livro-razão do canal. Em seguida, um membro do canal pode instanciar o chaincode em um canal e configurar a política de endosso do chaincode. A instalação e a instanciação do chaincode podem ser executadas usando a IU do Monitor de Rede, a interface da linha de comandos do Fabric Peer ou a partir de um aplicativo cliente usando o [Fabric SDK](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk). Para aprender como usar a IU do Monitor de Rede para implementar o chaincode, consulte [Instalando, instanciando e atualizando um chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).

## Chaincode e dados
{: #develop-smart-contracts-data}

Cada canal possui apenas um livro-razão e os dados nesse livro-razão são particionados por uma chave exclusiva e o chaincode que incluiu o par chave-valor no livro-razão. Os membros podem apenas ler ou atualizar dados no livro-razão do canal usando a chave correta e o chaincode associado. Os dados que podem ser acessados por um chaincode são referidos como namespace do chaincode e todos os dados no livro-razão estão dentro do namespace de um chaincode. Um chaincode pode interagir com dados fora de seu namespace apenas usando uma [transação de chaincode cruzado](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode) para o chaincode que pode acessar os dados relevantes.

Nós podemos usar o chaincode fabcar do repositório de amostras do Fabric como um exemplo de como o chaincode interage com os dados. O namespace é criado quando o contrato inteligente é instanciado. Algum chaincode aceita um conjunto de argumentos de par chave-valor quando o chaincode é instanciado e usa esses dados para inicializar o namespace. O chaincode fabcar do repositório de amostras do Fabric não aceita nenhum argumento quando ele é instanciado. Para fabcar, é necessário incluir dados no namespace usando a função initLedger ou createCar. Por exemplo, transmitir o argumento `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` tornará o par chave-valor de `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` o namespace fabcar.

Apenas o chaincode fabcar pode mudar esses dados. Suponha que o usuário deseja transferir o carro para um novo proprietário, usando a função changeCarOwner abaixo:
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

É possível chamar o chaincode usando os argumentos a seguir:
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

Em seguida, o chaincode usa os comandos get para gerar um conjunto de leitura a partir do namespace do chaincode. Ele usa os comandos put para criar o conjunto de gravação de transação. A chamada de chaincode também cria um ID de transação exclusivo.
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Cada par de endosso com o chaincode instalado simulará as transações usando o conjunto de leitura e o conjunto de gravação. Se as simulações em cada peer forem consistentes, as transações serão consideradas válidas e poderão ser confirmadas para o livro-razão. Após a transação, o namespace fabcar se tornará `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`. Observem que o proprietário mudou de Tom para Mark.

O relacionamento 1-1 entre o chaincode e os dados cria considerações importantes para o desenvolvimento de redes e aplicativos. O software chaincode deve ser atualizado instalando um chaincode com o mesmo nome e uma versão diferente, em vez do uso de um novo chaincode. Esse procedimento de atualização preserva o namespace do chaincode e permitirá que você continue utilizando os dados existentes. Também é possível usar um chaincode diferente para restringir o acesso a dados sensíveis ou ler e gravar dados em paralelo.

## Transações de chaincode cruzado
{: #develop-smart-contracts-cross-chaincode}

É possível usar um chaincode para chamar outros chaincodes. Isso permite que um chaincode consulte e grave em dados fora de seu namespace. O chaincode pode ler e atualizar dados usando o chaincode instanciado no mesmo canal. No entanto, eles podem consultar dados apenas usando o chaincode em diferentes canais. A organização que está chamando o chaincode original precisa estar autorizada a chamar o chaincode que é o destino da transação.

Por exemplo, é possível usar a função `crossChaincodeChangeCarOwner` abaixo para chamar o fabcar de outro chaincode. Como a função atualiza os dados mudando o proprietário do carro, ela precisa chamar um chaincode fabcar instanciado no mesmo canal.
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

Se você criar um novo contrato usando esse código e chamar a função `crossChaincodeChangeCarOwner`, a transação gerará um novo ID de transação e um conjunto de gravação.
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Esse TXid e o conjunto de gravação serão transmitidos para o chaincode fabcar.
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

Como o `fabcar` está no mesmo canal que `newContract`, a função `crossChaincodeChangeCarOwner` tem permissão para gravar no namespace `fabcar`. O novo namespace `fabcar` será `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`.


## Usando índices com o CouchDB
{: #develop-smart-contracts-indexes}

Se você usar o CouchDB como seu banco de dados de estado, será possível executar consultas de dados JSON por meio de seu chaincode com relação aos dados de estado do canal. É altamente recomendado que você crie índices para suas consultas JSON e os use em seu chaincode. Os índices permitem que seus aplicativos recuperem dados com eficiência, pois a rede inclui blocos adicionais de transações e entradas no estado mundial.

Para obter mais informações sobre o CouchDB e como configurar índices, consulte [CouchDB como banco de dados de estado ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB como banco de dados de estado"){:new_window} na documentação do Hyperledger Fabric. Também é possível localizar um exemplo que usa um índice com chaincode no [Tutorial do Fabric CouchDB ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html). Visite [Melhores práticas ao usar o CouchDB](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices) no tutorial Desenvolvendo Aplicativos para obter mais informações sobre como consultar dados de seus aplicativos.

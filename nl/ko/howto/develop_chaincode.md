---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: update data, private data, smart contract, CouchDB indexes, cross chaincode transaction

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# 스마트 계약 작성
{: #develop-smart-contracts}

체인코드(chaincode, 스마트 계약(smart contracts)이라고도 함)는 블록체인 원장에서 데이터를 읽고 업데이트하는 데 사용할 수 있는 소프트웨어입니다. 체인코드는 비즈니스 로직을 블록체인 네트워크의 모든 구성원이 승인하고 확인한 실행 가능 프로그램으로 변환할 수 있습니다. 비즈니스 로직에는 당사자 사이에 거래된 자산의 정의가 포함됩니다. 또한 비즈니스 로직은 실행될 트랜잭션에 필요한 이용 약관으로 구성됩니다. 이러한 규칙을 블록 체인의 코드로 변환하면 비즈니스에서 비즈니스 처리와 감사를 간소화하고 대량의 수동 처리 및 서류 작업을 줄일 수 있습니다.

예를 들어, 자동차 대리점, 보험 회사 및 정부 규제기관의 네트워크에서 블록체인을 사용하여 자동차 소유권을 추적하기로 결정했다고 가정해보십시오. 체인코드에는 모든 차량이 네트워크에 추가될 수 있도록 유효한 등록 및 차량 식별 번호가 필요할 수 있습니다. 차량이 판매될 때 체인코드에서는 규제 기관에서 차량을 소유자에게 등록할 때까지 자금을 에스크로에 예치하도록 요구합니다. 새 등록이 완료된 후 새 소유자가 기록되고 자금이 자동으로 이체됩니다.

다음 튜토리얼에서는 다음 사항을 포함하여 체인코드 빌드의 기본 요소를 안내합니다.

- [체인코드 작성을 시작하는 방법](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-write)
- [체인코드와 데이터 간의 관계](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-data)
- [교차 체인코드 트랜잭션](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)

해당 튜토리얼에서는 체인코드를 통하여 액세스할 수 있는 Fabric의 중요한 요소도 소개합니다.

- [couchDB에서 인덱스 사용](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-indexes)

## 체인코드 작성
{: #develop-smart-contracts-write}

체인코드는 여러 언어로 작성할 수 있으며 {{site.data.keyword.blockchainfull_notm}} Platform에서는 Go와 Node.js로 작성된 체인코드를 지원합니다. 체인코드를 사용하면 Fabric 체인코드 인터페이스에서 제공하는 API를 사용하여 blockchain에 저장된 데이터를 조회하고 변경할 수 있습니다. blockchain의 데이터는 채널 [원장 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "원장")의 세계 상태로 키-값 쌍에 저장됩니다. 체인코드는 get 명령을 사용하여 값을 검색하고 put 명령을 사용하여 값을 작성하거나 업데이트합니다. 이러한 기본 오퍼레이션을 사용하면 네트워크의 비즈니스 규칙을 정의하는 함수를 빌드할 수 있습니다. 이러한 함수는 애플리케이션에 의해 호출되어 네트워크의 일반 사용자에게 제공될 수 있습니다. 차량 네트워크 예를 계속 사용하려면 자동차 대리점에서 올바른 차량 ID 번호를 제공할 수 있는 경우 `PUT` 명령을 사용하여 자동차를 원장에 추가할 수 있도록 하는 함수를 작성할 수 있습니다.

Hyperledger Fabric 커뮤니티 문서의 [개발자용 체인코드 튜토리얼![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4ade.html "개발자용 체인코드 튜토리얼")을 참조하여 체인코드를 작성하는 방법을 학습할 수 있습니다. 이 튜토리얼에서는 자산을 작성하고 읽는 간단한 체인코드를 구현하는 과정을 안내하고 해당 프로세스에서 사용되는 API를 소개합니다. 모든 체인코드 언어에 해당하는 체인코드 API 참조 안내서를 찾을 수도 있습니다. [Fabric 샘플 저장소 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric-samples "Fabric 샘플")의 체인코드 폴더에 추가 예제가 있습니다.  

스마트 계약은 일반적으로 요청의 유효성을 검증하고 비즈니스 규칙을 적용한 후 결정적 결과를 리턴할 수 있습니다. 그러나 추가 사실이 필요하거나 비즈니스 네트워크가 클라이언트에서 제공하는 정보가 실제 사실인지 확인하려는 일부 경우가 있습니다. Hyperledger Fabric은 스마트 계약에서 서드파티 시스템에 대한 외부 호출이 수행되는 것을 막지 않습니다. 그러나 그로 인해 발생하는 R/W 세트가 결정적인지 확인하는 것은 스마트 계약 개발자의 책임입니다.
{:note}

## 체인코드 설치
{: #develop-smart-contracts-install}

체인코드는 채널에서 트랜잭션의 구조를 제공하므로 채널코드를 사용하여 채널 원장을 업데이트하거나 조회하려는 채널에 가입된 모든 피어에 체인코드를 설치해야 합니다. 그런 다음 채널의 한 멤버는 채널에서 체인코드를 인스턴스화하고 체인코드의 보증 정책을 설정할 수 있습니다. 체인코드의 설치 및 인스턴스화는 {{site.data.keyword.blockchainfull_notm}} Platform UI, Fabric 피어 명령행 인터페이스를 사용하거나 클라이언트 애플리케이션에서 [Fabric SDK](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk)를 사용하여 수행할 수 있습니다. {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}를 사용 중인 경우 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 사용하여 체인코드를 배치하는 방법을 알아보려면 [네트워크에 스마트 계약 배치 튜토리얼](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts)을 방문하십시오. 스타터 플랜 또는 엔터프라이즈 플랜을 사용 중인 경우 네트워크 모니터 UI를 사용하여 체인코드를 배치하는 방법을 알아보려면 [체인코드 설치, 인스턴스화 및 업데이트](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode)를 참조하십시오.

## 체인코드 및 데이터
{: #develop-smart-contracts-data}

각 채널에는 하나의 원장만 있으며 해당 원장의 데이터는 고유 키와 키 값 쌍을 원장에 추가한 체인코드로 분할됩니다. 멤버는 올바른 키와 연관된 체인코드를 사용하여 채널 원장에서 데이터를 읽거나 업데이트할 수만 있습니다. 체인코드에서 액세스할 수 있는 데이터는 해당 체인코드의 네임스페이스로 참조되고 해당 원장의 모든 데이터가 한 체인코드의 네임스페이스 내에 포함됩니다. 체인코드는 관련 데이터에 액세스할 수 있는 체인코드에 [교차 체인코드 트랜잭션](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts-cross-chaincode)을 사용해서만 해당 네임스페이스 외부의 데이터와 상호작용할 수 있습니다.

체인코드가 데이터와 상호작용하는 방법의 예로 Fabric 샘플 저장소의 fabcar 체인코드를 사용할 수 있습니다. 스마트 계약이 인스턴스화될 때 네임스페이스가 작성됩니다. 일부 체인코드는 체인코드가 인스턴스화될 때 키 값 쌍 인수 세트를 허용하고 이 데이터를 사용하여 네임스페이스를 초기화합니다. Fabric 샘플 저장소의 fabcar 체인코드는 초기화될 때 어떠한 인수도 허용하지 않습니다. fabcar의 경우 initLedger 또는 createCar 함수를 사용하여 데이터를 네임스페이스에 추가해야 합니다. 예를 들면, `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}` 인수를 전달하면 `{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}`의 키 값 쌍은 fabcar 네임스페이스가 됩니다.

fabcar 체인코드만 이 데이터를 변경할 수 있습니다. 사용자가 아래 changeCarOwner 함수를 사용하여 자동차를 새 소유자에게 양도하려고 한다고 가정하십시오.
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

다음 인수를 사용하여 체인코드를 호출할 수 있습니다.
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

그런 다음 체인코드는 get 명령을 사용하여 체인코드 네임스페이스에서 설정된 읽기 세트를 생성합니다. put 명령을 사용하여 트랜잭션 기록 세트를 작성합니다. 체인코드를 호출하면 고유 트랜잭션 ID도 작성됩니다.
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

체인코드가 설치된 각 보증 피어는 읽기 세트와 쓰기 세트를 사용하여 트랜잭션을 시뮬레이션합니다. 각 피어에 의한 시뮬레이션이 일관된 경우 트랜잭션은 유효한 것으로 간주되어 원장에 커미트할 수 있습니다. 트랜잭션 후, fabcar 네임스페이스는 `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`가 됩니다. 소유자가 Tom에서 Mark로 변경되었음에 유의하십시오.

체인코드와 데이터 간의 1-1 관계는 네트워크와 애플리케이션을 개발하는 데 필요한 중요한 고려사항을 작성합니다. 체인코드 소프트웨어는 새 체인코드를 사용하는 대신 동일한 이름과 다른 버전의 체인코드를 설치하여 업데이트해야 합니다. 이 업데이트 프로시저는 체인코드 네임스페이스를 유지하고 기존 데이터를 계속 사용할 수 있도록 합니다. 또한 다른 체인코드를 사용하여 민감한 데이터에 대한 액세스를 제한하거나 데이터를 병렬로 읽고 쓸 수도 있습니다.

## 교차 체인코드 트랜잭션
{: #develop-smart-contracts-cross-chaincode}

체인코드를 사용하여 다른 체인코드를 호출할 수 있습니다. 이를 통해 체인코드는 네임스페이스 외부의 데이터를 조회하고 쓸 수 있습니다. 체인코드는 동일한 채널에서 인스턴스화된 체인코드를 사용하여 데이터를 읽고 업데이트할 수 있습니다. 그러나 다른 채널의 체인코드를 사용해서는 데이터를 조회할 수만 있습니다. 원래 체인코드를 호출하는 조직에 트랜잭션 대상인 체인코드를 호출할 수 있는 권한이 부여되어야 합니다.

예를 들면, 다른 체인코드에서 fabcar를 호출하기 위해 아래 `crossChaincodeChangeCarOwner` 함수를 사용할 수 있습니다. 함수에서 자동차 소유자를 변경하여 데이터를 업데이트하므로 동일한 채널에서 인스턴스화된 fabcar 체인코드를 호출해야 합니다.
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

이 코드를 사용하여 새 계약을 작성하고 함수 `crossChaincodeChangeCarOwner`를 호출하면 해당 트랜잭션에서 새 트랜잭션 ID와 기록 세트를 생성합니다.
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

이 TXid와 기록 세트는 fabcar 체인코드로 전달됩니다.
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

`fabcar`가 `newContract`와 동일한 채널에 있으므로 `crossChaincodeChangeCarOwner` 함수는 `fabcar` 네임스페이스에 쓸 수 있습니다. 새 `fabcar` 네임스페이스는 `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`가 됩니다.


## CouchDB에서 인덱스 사용
{: #develop-smart-contracts-indexes}

CouchDB를 상태 데이터베이스로 사용하는 경우, 채널의 상태 데이터에 대해 체인코드에서 JSON 데이터 조회를 시작할 수 있습니다. JSON 조회를 위한 색인을 작성한 후 체인코드에서 해당 색인을 사용할 것을 적극 권장합니다. 색인을 사용하는 경우 네트워크에서 트랜잭션 및 항목의 추가 블록을 세계 상태로 추가하기 때문에 애플리케이션에서 효율적으로 데이터를 검색할 수 있습니다.

CouchDB에 관한 자세한 정보와 인덱스 작성 방법은 Hyperledger Fabric 문서에서 [상태 데이터베이스로서의 CouchDB![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_as_state_database.html "상태 데이터베이스로서의 CouchDB"){:new_window}를 참조하십시오. [Fabric CouchDB 튜토리얼 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html)에서 체인코드와 인텍스를 사용하는 예도 찾을 수 있습니다. 애플리케이션에서 데이터를 조회하는 방법에 대한 자세한 정보는 애플리케이션 개발 튜토리얼의 [CouchDB 사용 시의 우수 사례](/docs/services/blockchain/best_practices.html#best-practices-app-couchdb-indices)를 참조하십시오.

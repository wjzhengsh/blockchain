---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# 애플리케이션 작성
{: #ibp-console-app}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

스마트 계약을 설치하고 노드를 배치한 후에 클라이언트 애플리케이션을 사용하여 네트워크의
다른 구성원과 거래할 수 있습니다. 애플리케이션은 블록체인 원장의 자산을 작성, 전송 또는 업데이트하기 위해
스마트 계약에 포함된 비즈니스 로직을 호출할 수 있습니다. 이 튜토리얼을 사용하여
클라이언트 애플리케이션으로부터 {{site.data.keyword.blockchainfull}} 플랫폼에서
관리하는 네트워크와 상호작용하는 방법을 배울 수 있습니다.
{:shortdesc}

Hyperledger Fabric 문서의 [애플리케이션 개발 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "애플리케이션 개발")를
방문하여 애플리케이션 및 스마트 계약이 함께 작동하는 방법에 대해 배울 수 있습니다. 이 주제에서는
은행 및 기업이 상업 어음을 거래하는 가상 유스 케이스를 탐색하고 스마트 계약에서 트랜잭션이
인코딩되는 방법을 설명합니다. [상업 어음 튜토리얼 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "상업 어음 튜토리얼")에서는
패브릭 네트워크에 스마트 계약을 배치하고 샘플 애플리케이션 코드를 사용하여 네트워크 구성원 사이에서 상업 어음을 작성 및 전송하도록
허용함으로써 이 주제를 확장합니다.

애플리케이션을 개발하려면 네트워크의
구별되는 두 사용자, 즉, 네트워크 운영자 및 애플리케이션 개발자 간의
조정이 필요할 수 있습니다.
- **네트워크 운영자**는 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을
사용하여 조직의 노드를 배치하고 네트워크에 스마트 계약을 설치하는 관리자입니다.
- **애플리케이션 개발자**는 일반 사용자가 이용할 클라이언트 애플리케이션을 빌드합니다. 개발자는
[Hyperledger Fabric SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "Hyperledger Fabric SDK"){:new_window}를
사용하여 스마트 계약에 작성된 트랜잭션을 호출합니다.

사용자가 **네트워크 운영자**인 경우,
다음 단계를 완료해야만 애플리케이션 개발자가 사용자의 네트워크와 상호 작용할 수 있습니다.
1. 조직 CA를 사용하여
[애플리케이션 ID를 등록](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)하십시오.
2. 스마트 계약 패널에서 [연결 프로파일을 다운로드](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)하십시오.
3. 다음 오브젝트 및 정보를 애플리케이션 개발자에게 전송하십시오.
  - 애플리케이션 ID의 등록 ID 및 시크릿
  - 연결 프로파일
  - 스마트 계약 이름
  - 스마트 계약이 인스턴스화되는 채널의 이름  

사용자가 **애플리케이션 개발자**인 경우, 네트워크 운영자가 제공하는 정보를 사용하여 다음 단계를 완료하십시오.
1. 사용자의 연결 프로파일 내의 CA 엔드포인트 정보와 함께
애플리케이션 ID의 등록 ID 및 시크릿을 사용하여 공개 및 개인 키 쌍을 생성하십시오.
2. 연결 프로파일, 채널 이름, 스마트 계약 및 애플리케이션 키를 사용하여 스마트 계약을 호출하십시오.  

애플리케이션 개발자는 두 가지 프로그래밍 모델을 사용하여 네트워크와 상호작용할 수 있습니다.

**상위 레벨 Fabric SDK API**

Fabric v1.4부터는 사용자가 단순화된 애플리케이션 및 스마트 계약 프로그래밍 모델을 사용할 수 있습니다. 새 모델을
사용하면 트랜잭션을 제출하는 데 필요한 코드의 양 및 단계 수가 줄어듭니다. 이 모델은
**Node.js**로 작성된 애플리케이션에만 지원됩니다. 새 모델을
활용하려면 이 플랫폼을 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 네트워크에서 다음 조치를 완료하십시오.

- SDK를 사용하여 [애플리케이션에 대한 인증서를 생성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll)하십시오.
- [SDK에서 스마트 계약을 호출](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke)하십시오.
- [IBM Blockchain VScode 확장](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode)을
사용하여 스마트 계약을 작성 및 배치하고 반복적으로 개발 및 테스트하십시오.
- 콘솔에서 관리되는 노드에 [상업 어음 튜토리얼](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)을
배치하여 애플리케이션 개발에 대해 학습하십시오. 이 튜토리얼은
패브릭 지갑 및 게이트웨이를 사용하는 방법에 대한 더 자세한 백그라운드를 제공합니다.


**하위 레벨 Fabric SDK API**

기존 스마트 계약 및 애플리케이션 코드를 계속 사용하거나 Hyperledger 커뮤니티에 의해
제공되는 기타 Fabric SDK 언어를 사용하려면
[하위 레벨 Fabric SDK API](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level)를
사용하여 사용자의 네트워크에 연결할 수 있습니다.

## 애플리케이션 ID 등록
{: #ibp-console-app-identities}

해당 당사자가 전송하는 트랜잭션을 확인하려면
애플리케이션이 {{site.data.keyword.blockchainfull_notm}} 노드에 제출하는 트랜잭션에
서명해야 하며 노드에 의해 사용되는 공개 키를 첨부해야 합니다. 그러면
트랜잭션을 참여 권한이 있는 조직에 제출할 수 있습니다.

네트워크 운영자가 조직의 CA를 사용하여 애플리케이션 ID를 등록해야 합니다.
그러면 애플리케이션 개발자가 해당 ID를 사용하여 공개 및 개인 키를 생성할 수 있습니다. 운영자는
CA 엔드포인트 정보와 함께 SDK가 인증서를 생성하기 위해 사용하는
등록 ID 및 ID의 시크릿을 제공할 수 있습니다. 애플리케이션 개발자는
클라이언트 측에 등록함으로써 기타 당사자가 애플리케이션의 개인 키에 액세스하지 못하도록 할 수 있습니다. 추가적인
보안을 위해 네트워크 운영자가 등록 동안 등록 제한을 설정할 수 있습니다. 애플리케이션 개발자가
등록한 후에는 등록 ID 및 시크릿을 사용하여 다른 개인 키를 생성할 수 없습니다. 

보안을 크게 고려하지 않는 경우, 네트워크 운영자가
[CA 탭](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll)을
사용하여 애플리케이션 ID를 등록할 수 있습니다. 그런 다음 운영자가
ID를 다운로드하여 콘솔 지갑으로 내보낼 수 있습니다. SDK에서
인증서를 사용하려면 키가 base64에서 PEM 형식으로 디코딩되어야 합니다. 로컬 시스템에서 다음 명령을 실행하여 인증서를 디코딩할 수 있습니다.

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

## 연결 프로파일 다운로드
{: #ibp-console-app-profile}

애플리케이션은 채널에서 인스턴스화된 스마트 계약에만 트랜잭션을 제출할 수 있습니다. 따라서 스마트 계약과 상호작용하기 위해 연결해야 하는 정보는 콘솔 내의 인스턴스화된 스마트 계약 목록에서 찾을 수 있습니다. 즉,
이미 스마트 계약을 설치하고 인스턴스화한 상태여야 합니다.

Hyperledger Fabric [트랜잭션 플로우![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "트랜잭션 플로우"){:new_window}의
범위는 피어에서 보증을 수집하여 보증된 트랜잭션을 순서 지정 서비스로 전송하는 클라이언트 애플리케이션을 포함하여 다중
컴포넌트에 걸쳐 있습니다. 연결 프로파일은
트랜잭션을 제출하는 데 필요한 피어 및 순서 지정 노드의 엔드포인트를 애플리케이션에 제공합니다. 또한
인증 기관 및 MSP ID와 같은 조직에 관한 정보도 포함합니다. 사용자가 트랜잭션 및 보증 플로우를 관리하는 코드를
작성할 필요 없이 Fabric SDK가 직접 연결 프로파일을 읽을 수 있습니다.

채널을 작성할 때 앵커 피어를 구성한 경우, Hyperledger Fabric의 [서비스 발견 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "서비스 발견") 기능을 사용할 수 있습니다. 서비스 발견을
사용하면 애플리케이션이 조직 외부의 채널에 있는 피어 중 어느 피어가 트랜잭션을 보증해야 하는지 파악할 수 있습니다. 서비스 발견을
사용하지 않으면 기타 조직에서 대역 외 피어의 엔드포인트 정보를 가져와서
연결 프로파일에 추가해야 합니다. 앵커 피어를 구성하는 방법에 대한
자세한 정보는 스마트 계약 배치 튜토리얼에서
[개인용 데이터 주제](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data)의 3단계를 참조하십시오.

플랫폼 콘솔에서 스마트 계약 탭으로 이동하십시오. 인스턴스화된 각 스마트 계약 옆의 오버플로우 메뉴로 이동하십시오. **SDK로 연결**이라는
이름의 단추를 클릭하십시오. 그러면 연결 프로파일을 빌드하고 다운로드할 수 있는 사이드 패널이 열립니다. 먼저,
애플리케이션 ID를 등록하기 위해 사용한 조직의 CA를 선택하십시오. 또한 조직 MSP 정의도 선택해야 합니다. 그러면
스마트 계약을 호출하는 데 사용할 수 있는 연결 프로파일을 다운로드할 수 있습니다.

## SDK를 사용하여 등록
{: #ibp-console-app-enroll}

일단 네트워크 운영자가 네트워크 연결 프로파일 및 애플리케이션 ID의 등록 ID 및 시크릿을 제공하면 애플리케이션 개발자가
Fabric SDK 또는 Fabric CA 클라이언트를 사용하여 클라이언트 측 인증서를 생성할 수 있습니다. 다음
단계에 따라 [Node.js용 Fabric SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Node.js용 Fabric SDK")를
사용하여 애플리케이션 ID를 등록할 수 있습니다.

1. 연결 프로파일을 로컬 시스템에 저장하고 이름을 `connection.json`으로 바꾸십시오.
2. 다음 코드 블록을 연결 프로파일과 동일한 디렉토리에 `enrollUser.js`로 저장하십시오.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Create a new CA client for interacting with the CA.
      const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
      const ca = new FabricCAServices(caURL);

      // Create a new file system based wallet for managing identities.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Check to see if we've already enrolled the admin user.
      const userExists = await wallet.exists('user1');
      if (userExists) {
        console.log('An identity for "user1" already exists in the wallet');
        return;
      }

      // Enroll the admin user, and import the new identity into the wallet.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. `enrollUser.js`를 편집하여 다음 값을 바꾸십시오.
  - ``<CA_Name>``을 사용자의 조직 CA의 이름으로 바꾸십시오. "인증 기관" 아래에서 연결 프로파일의 "조직" 섹션에서 CA 이름을 찾을 수 있습니다. "인증 기관" 섹션의 "caName"을 사용하지 마십시오.
  - ``<app_enroll_id>``를 네트워크 운영자가 제공하는 애플리케이션 등록 ID로 바꾸십시오.
  - ``<app_enroll_secret>``을 네트워크 운영자가 제공하는 애플리케이션 등록 시크릿으로 바꾸십시오.
  - ``<msp_id>``를 조직의 MSP ID로 바꾸십시오. 연결 프로파일의 "조직" 섹션 아래에서 MSP ID를 찾을 수 있습니다.
4. 터미널을 사용하여 `enrollUser.js`로 이동하여 `node enrollUser.js`를 실행하십시오. 명령이 성공적으로 실행되면 다음과 같은 출력이 표시되어야 합니다.

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
SDK가 인증서를 작성하여 명령에 의해 작성된 `wallet/user1/` 디렉토리에 저장합니다. 이 디렉토리는
향후 트랜잭션을 제출하는 데 사용되는 파일 시스템 지갑입니다.


## SDK를 사용하여 스마트 계약 호출
{: #ibp-console-app-invoke}

애플리케이션 공개 및 개인 키를 생성하여 지갑에 저장했으면 트랜잭션을 제출할 준비가 된 것입니다. 스마트 계약의
이름 및 스마트 계약이 인스턴스화된 채널의 이름을 알아야 합니다. 아래 단계에 따라
[Node.js용 Fabric SDK![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/ "Node.js용 Fabric SDK")를
사용하여 스마트 계약을 호출할 수 있습니다.


1. 아래 파일을 로컬 시스템에 `invoke.js`로 저장하십시오. 파일을 `enrollUser.js`와 동일한 디렉토리에 저장하십시오.

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Parse the connection profile. This would be the path to the file downloaded
        // from the IBM Blockchain Platform operational console.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Configure a wallet. This wallet must already be primed with an identity that
        // the application can use to interact with the peer node.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Create a new gateway, and connect to the gateway peer node(s). The identity
        // specified must already exist in the specified wallet.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {discover: true, asLocalhost:false }});

        // Get the network channel that the smart contract is deployed to.
        const network = await gateway.getNetwork('<channel_name>');

        // Get the smart contract from the network channel.
        const contract = network.getContract('<smart_contract_name>');

        // Submit the 'createCar' transaction to the smart contract, and wait for it
        // to be committed to the ledger.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. `invoke.js`를 편집하여 다음 값을 대체하십시오.
  - ``<channel_name>``을 스마트 계약이 인스턴스화된 채널의 이름으로 바꾸십시오. 연결 프로파일의 "인증 기관" 섹션 아래에서 CA 이름을 찾을 수 있습니다.
  - ``<smart_contract_name>``을 설치된 스마트 계약의 이름으로 바꾸십시오. 네트워크 운영자로부터 이 값을 얻을 수 있습니다.
  - `submitTransaction`의 컨텐츠를 편집하여 스마트 계약 내의 기능을 호출하십시오. `invoke.js` 파일이
[fabcar 스마트 계약 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar)을
호출하도록 작성되었습니다. 아래 파일을 실행하여 트랜잭션을 제출하려면 fabcar를 설치하고 채널 중 하나에 스마트 계약을 인스턴스화하십시오.

3. 터미널을 사용하여 `invoke.js`로 이동하여 `node invoke.js`를 실행하십시오. 명령이 성공적으로 실행되면 다음과 같은 출력이 표시되어야 합니다.

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  콘솔을 사용하여 채널로 이동하는 경우, 트랜잭션이 추가한 다른 블록을 볼 수 있습니다.


## IBM Blockchain VScode 확장과 연결
{: #ibp-console-app-vscode}

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 확장은 스마트 계약 패키지의 개발, 패키지 및 배치를 위해 Visual Studio Code 내에 환경을 제공합니다. 연결 프로파일 및 CA를 사용하여 생성된 키 파일 세트가 있는지 확인하십시오. 그런
다음, VScode 확장을 사용하여 콘솔을 사용하여 관리 중인 네트워크에 연결할 수 있습니다.

지시사항에 따라 Visual Studio Code 마켓플레이스에 [VScode 확장 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode 확장")을
다운로드하십시오. 일단 확장이 설치되면 VScode를 열고 **보기** > **명령 팔레트**를
클릭하여 확장에 액세스할 수 있습니다. *IBM Blockchain Platform: Create Smart Contract Project* 명령을
입력하여 새 스마트 계약 프로젝트를 작성하십시오.

일단 새 프로젝트를 작성한 후에는
콘솔에서 인스턴스화된 스마트 계약의 정보를 사용하여 Visual Studio 코드에서 네트워크에 직접 연결할 수 있습니다. 스마트
계약 탭의 **인스턴스화된 스마트 계약** 표를 사용하여
[연결 프로파일](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)을
로컬 파일 시스템에 다운로드하십시오. 그런 다음
[애플리케이션 ID를 작성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)하고
CA를 사용하여 인증서(공개 키) 및 개인 키를 작성하고 다운로드하십시오. 앞의 지시사항에 따라
개인 키 및 인증서를 PEM 형식으로 변환하십시오. 연결 프로파일 및 키를 다운로드한 후에는 다음 단계에 따라 네트워크에 연결하십시오.

1. Visual Studio Code에서 _{{site.data.keyword.blockchainfull_notm}} Platform_ 탭을 여십시오.
2. _{{site.data.keyword.blockchainfull_notm}} Platform_ 분할창에서 **새 연결 추가**를 클릭하십시오.
3. 연결의 이름을 입력하십시오. 이름은 _{{site.data.keyword.blockchainfull_notm}} Platform_ 분할창에 표시됩니다.
4. 연결 프로파일의 완전한 파일 경로를 입력하십시오.
5. PEM 형식으로 인증서(공개 키)의 완전한 파일 경로를 입력하십시오.
6. PEM 형식으로 개인 키의 완전한 파일 경로를 입력하십시오.
7. 이제 연결이 `local_fabric` 아래의 연결 목록에 표시되어야 합니다. 연결할 연결 이름을 두 번 클릭하십시오.

일단 VScode에서 연결되면 조직에서 피어 목록을 볼 수 있으며 블록체인 연결 분할창에서 가입된 채널을 볼 수 있습니다. 각 피어
아래에서 설치된 스마트 계약의 목록을 볼 수 있습니다. 네트워크에 프로젝트의 새 스마트 계약을
설치하려면 먼저 마우스 오른쪽 단추로 피어를 클릭하고 **스마트 계약 설치**를 클릭하십시오. 그런 다음
마우스 오른쪽 단추로 채널을 클릭하여 해당 스마트 계약을 인스턴스화한 다음 **스마트 계약 설치**를
클릭하십시오. [Visual Studio Code 마켓플레이스 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "VScode 확장")의
문서를 방문하여
{{site.data.keyword.blockchainfull_notm}} Platform 확장 사용에 대한 자세한 정보를 볼 수 있습니다.


## 상업 어음 샘플 사용
{: #ibp-console-app-commercial-paper}

Hyperledger Fabric 문서의
[상업 어음 튜토리얼 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "상업 어음 튜토리얼")은
다중 당사자가 상업 어음을 구매, 판매 및 상환하는 유스 케이스를 개발자에게 설명합니다. 또한
Fabric의 로컬 인스턴스에서 자산을 작성 및 거래할 수 있도록 해주는 샘플 스마트 계약 및 애플리케이션 코드를 제공하여 [애플리케이션 개발 주제 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "애플리케이션 개발")를
확장합니다.

또한 상업 어음 튜토리얼 샘플 코드를 {{site.data.keyword.blockchainfull_notm}} Platform 2.0 네트워크에 배치할 수 있습니다. 그러면
신속하게 네트워크와 상호작용을 시작하고 샘플을 사용하여 필수 종속 항목을 다운로드할 수 있습니다. 또한 샘플 코드에는
인증서를 [지갑 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "지갑")으로
가져오고 연결 프로파일을 사용하여
[Fabric 게이트웨이 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric 게이트웨이")를
빌드하는 방법을 설명하는 예제가 포함되어 있습니다. 두 개의 다른 조직이 튜토리얼을 사용하여
단일 자산과 다른 트랜잭션을 완료할 수 있습니다. [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)을
사용하여 채널에 연결된 두 개의 피어 조직을 배치한 경우, 두 조직을 모두 사용하여 튜토리얼과 상호작용할 수 있습니다.

아래 단계에 따라 네트워크에 샘플을 배치하십시오. Fabric 문서 [상업 어음 튜토리얼![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "상업 어음 튜토리얼")에서
튜토리얼을 검토하여 스마트 계약 및 애플리케이션 구조에 대한 자세한 정보를 볼 수 있습니다.

### 전제조건

상업용 어음 샘플을 배치하기 전에 로컬 시스템에 필수 도구를 설치해야 합니다.
  * [Git ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

또한 문서 편집기를 사용하여 샘플의 파일을 편집하고 저장해야 합니다. [Atom ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://atom.io/ "atom"), [Sublime text ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](http://www.sublimetext.com/ "Git") 또는 [Brackets ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](http://brackets.io/ "Brackets") 등의 수많은 고품질 편집기를 무료로 사용할 수 있습니다.

### 1단계: 샘플 다운로드

[Fabric 샘플 저장소![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://github.com/hyperledger/fabric-samples "Fabric 샘플 저장소")을 복제하여 상업 어음 샘플을 다운로드할 수 있습니다.

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

일단 Fabric 샘플을 다운로드한 후에 다음 명령을 실행하여 Fabric v1.4와 호환되는 샘플 버전을 사용하고 있는지 확인하십시오.

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

`fabric-samples`의 `commercial paper` 폴더에서 샘플을 찾을 수 있습니다.

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

튜토리얼에는 두 개의 샘플 조직, 즉, **magnetocorp** 및 **digibank**가 포함되어 있습니다. 각 조직에는
자체 샘플 애플리케이션 코드가 있으며 `organization` 디렉토리 아래의 두 개의 별도 폴더에 저장되어 있습니다.

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

튜토리얼의 과정에서 먼저 magnetocorp 애플리케이션 코드를 사용하여 상업 어음을 발행할 것입니다. 그런 다음 digibank 애플리케이션 코드를
사용하여 어음을 구매하고 상환할 수 있습니다. 두 디렉토리 모두 동일한 스마트 계약을 포함합니다.

magnetocorp 디렉토리 내의 애플리케이션 코드로 이동하십시오.

```
cd magnetocorp/application
```
{:codeblock}

일단 디렉토리로 이동했으면 다음 명령을 실행하여 애플리케이션 종속 항목을 다운로드할 수 있습니다.

```
npm install
```
{:codeblock}

### 2단계: 스마트 계약 설치 및 인스턴스화

`digibank` 및 `magnetocorp` 디렉토리의 `contract` 폴더에서
상업 어음 스마트 계약을 찾을 수 있습니다. 튜토리얼을 사용하여 이 스마트 계약을 조직 내의 모든 피어에
설치해야 합니다. 그런 다음 채널에서 상업 어음 계약을 인스턴스화해야 합니다. 스마트 계약은 콘솔을 사용하여 설치될 수 있도록
[.cds 형식 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "스마트 계약 패키징")으로 패키지되어야 합니다.

[IBM Blockchain VScode 확장](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode)을
사용하여 스마트 계약을 패키지할 수 있습니다. 확장을 설치한 후 Visual Studio 코드를 사용하여 작업공간에서
`contracts` 폴더를 여십시오. _{{site.data.keyword.blockchainfull_notm}} Platform_ 탭을 여십시오. _{{site.data.keyword.blockchainfull_notm}} Platform_ 분할창에서
스마트 계약 패키지 섹션으로 이동하여 **스마트 계약 프로젝트 패키지**를 클릭하십시오. VScode 확장은
`contracts` 폴더의 파일을 사용하여 `papernet-js@.0.0.1.cds`라는 이름의 새 패키지를 작성합니다. 마우스 오른쪽 단추로
이 패키지를 클릭하여 로컬 파일 시스템으로 내보내십시오. 그런 다음 콘솔을 사용하여
[피어에 스마트 계약을 설치](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install)하고
[채널에서 스마트 계약을 인스턴스화](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate)할 수 있습니다.

### 3단계: 지갑에 대한 인증서 생성

애플리케이션이 패브릭 컴포넌트에 전송하는 요청에 서명해야 합니다. 컴포넌트가 트랜잭션을 제출하는
조직을 인식하지 못하면 트랜잭션이 거부되고 오류와 함께 리턴됩니다. 상업 어음 샘플은
사용자의 인증서를 저장하고 트랜잭션에 서명할 파일 시스템 지갑을 작성합니다. 애플리케이션이
지갑을 사용하는 방법에 대한 자세한 정보는 Fabric 문서에서 [지갑 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "지갑") 주제를
참조하십시오.

원래 샘플은 `addToWallet.js` 파일을 사용하여
패브릭 샘플 폴더의 인증서를 사용하는 파일 시스템 지갑을 작성합니다. 여기서는
SDK를 사용하는 새 파일을 작성하여 클라이언트 측 인증서를 생성하고 직접 새 지갑에 저장할 것입니다.


magnetocorp로서 튜토리얼을 작동하는 데 사용할 조직의 CA를 선택하십시오. 예를 들어, **네트워크 빌드** 튜토리얼을 완료한 경우,
Org1을 사용할 수 있습니다. CA를 사용하여 [애플리케이션 ID를 작성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)하십시오. 등록 ID 및 시크릿을 **저장**하십시오.

콘솔을 사용하여 [연결 프로파일을 다운로드](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)하십시오. 연결 프로파일을 로컬 파일 시스템에 저장하고 이름을 `connection.json`으로 바꾸십시오. 그 후,
다음 명령을 사용하여 연결 프로파일을
추후 명령에 의해 참조될 디렉토리로 이동하십시오.

  ```
  mv $HOME/<path_to_creds>/connection.json /gateway/connection.json
  ```
  {:codeblock}

`/magnetocorp/application` 디렉토리로 이동하여 다음 코드 블록을 `enrollUser.js`로 저장하십시오.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Create a new CA client for interacting with the CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Create a new file system based wallet for managing identities.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Check to see if we've already enrolled the admin user.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Enroll the admin user, and import the new identity into the wallet.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

이 파일을 편집하기 전에 이 파일의 작동 방법을 검토해야 합니다. 먼저, `enrollUser.js`가 `FileSystemWallet` 및 `X509WalletMixin` 클래스를 `fabric-network` 라이브러리에서 가져옵니다.

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

그런 다음 파일이 `FileSystemWallet` 클래스를 사용하여 로컬 파일 시스템에 지갑을 빌드합니다. 아래 행을
편집하여 지갑을 다른 위치에 저장할 수 있습니다.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

지갑을 작성한 후에 코드 스니펫이 등록 ID 및 시크릿과 함께 조직 CA를 사용하여 등록합니다. 그런 다음 공개 개인 키 쌍에
대한 ID를 작성하여 지갑으로 가져옵니다. 파일이 조직 MSP ID를 지갑으로 전달하는 방법에 유의하십시오.

```
// Enroll the admin user, and import the new identity into the wallet.
const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

`enrollUser.js`를 **편집**하여 다음 값을 바꾸십시오.
- `'<CA_Name>'`을 사용자의 조직 CA의 이름으로 바꾸십시오. "인증 기관" 아래에서 연결 프로파일의 "조직" 섹션에서 CA 이름을 찾을 수 있습니다. "인증 기관" 섹션의 "caName"을 사용하지 마십시오.
- `'<app_enroll_id>`를 네트워크 운영자가 제공하는 애플리케이션 등록 ID로 바꾸십시오.
- `'<app_enroll_secret>'`을 네트워크 운영자가 제공하는 애플리케이션 등록 시크릿으로 바꾸십시오.
- `'<msp_id>'`를 조직의 MSP ID로 바꾸십시오. 연결 프로파일의 "조직" 섹션 아래에서 이 MSP ID를 찾을 수 있습니다.

`enrollUser.js`를 저장하고 닫으십시오. `magnetocorp` 디렉토리에서 다음 명령을 실행하십시오.
```
node enrollUser.js
```
{:codeblock}

명령이 성공적으로 완료되면 다음과 비슷한 출력이 표시됩니다.

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

`magnetocorp` 디렉토리의 `identity` 폴더에서 작성된 지갑을 찾을 수 있습니다.

### 4단계: 연결 프로파일을 사용하여 패브릭 게이트웨이 빌드

Hyperledger Fabric [트랜잭션 플로우 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "트랜잭션 플로우"){:new_window}는 고유한 역할을 수행하는 클라이언트 애플리케이션과 함께 여러 컴포넌트를 포함합니다. 애플리케이션은
트랜잭션을 보증해야 하는 피어 및 트랜잭션의 순서를 지정하고 블록에 추가하는 순서 지정 서비스에
연결해야 합니다. 연결 프로파일을 사용하여 애플리케이션에 이러한 노드의 엔드포인트를 제공하여
Fabric 게이트웨이를 생성할 수 있습니다. 그런 다음 게이트웨이가 패브릭 네트워크와 하위 레벨 상호작용을 수행합니다. 자세한 내용을 보려면 Fabric 문서에서
[Fabric 게이트웨이 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Fabric 게이트웨이") 주제를
방문하십시오.

이미 연결 프로파일을 다운로드하여 조직의 인증 기관에 연결하는 데 사용했습니다. 이제 연결 프로파일을 사용하여 게이트웨이를 빌드합니다.

문서 편집기에서 `issue.js` 파일을 여십시오. 파일을 편집하기 전에
패브릭 네트워크 라이브러리에서 `FileSystemWallet` 및 `Gateway` 클래스를 가져온다는 점에 유의하십시오.

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

`Gateway` 클래스는 트랜잭션을 제출하는 데 사용할 게이트웨이를 구성하는 데 사용됩니다.

```
const gateway = new Gateway()
```
{:codeblock}

`FileSystemWallet` 클래스는 이전 단계에서 작성한 지갑을 로드하는 데 사용됩니다. 파일 시스템에서 지갑의 위치를
변경한 경우, 아래 행을 **편집**하십시오.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

지갑을 가져온 후, 다음 코드가 연결 프로파일 및 지갑을 새 게이트웨이로 전달합니다. 간결성을 위해 로그를 인쇄하는 행이 제거되었습니다.

```
const userName = 'User1@org1.example.com';

// Load connection profile; will be used to locate a gateway
let connectionProfile = yaml.safeLoad(fs.readFileSync('../gateway/networkConnection.yaml', 'utf8'));

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: false, asLocalhost: true }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

위 코드를 **편집**하여 콘솔에서 다운로드한 연결 프로파일을 가져오십시오. `enrollUser.js`의
`identityLabel`에 대해 선택한 사용자 이름을 업데이트하십시오. 또한
네트워크에서 서비스 발견을 활용하려면 발견 옵션을 업데이트해야 합니다. `discovery: { enabled: true, asLocalhost: false }`를
설정하십시오. 콘솔 연결 프로파일이 YAML이 아니라 JSON 형식임에 **유의**하십시오. 연결 프로파일을 가져오는 섹션을 변경해야 합니다. 편집한
후에 위 코드가 다음과 같이 표시됩니다.

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const ccp = JSON.parse(ccpJSON);

// Set connection options; identity and wallet
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

이 코드 스니펫이 게이트웨이를 사용하여 피어 및 순서 지정자 노드에 GRPC 연결을 열고 네트워크와 상호작용합니다.

### 5단계: 스마트 계약 호출

콘솔이 관리하는 네트워크에 연결하기 위해 게이트웨이를 구성한 후에 상업 어음 스마트 계약에
연결하는 `issue.js` 파일 부분을 편집할 것입니다. 게이트웨이에 계약 이름 및 스마트 계약을 인스턴스화한 채널을 제공해야 합니다.

아래 행을 **편집**하여 `mychannel`을 사용자의 채널 이름으로 바꾸십시오.

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

콘솔에 메시지를 인쇄하는 코드 행을 따라
게이트웨이에 스마트 계약 이름을 제공하는 행을 찾을 수 있습니다. 아래 행을 **편집**하여
`papercontract` 이름을 사용자가 설치한 계약의 이름으로 변경하십시오.

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

이제 게이트웨이가 트랜잭션을 제출하는 데 필요한 모든 정보를 보유하게 되었습니다. 다음 행이 새 상업 어음 자산을
정의하는 인수를 사용하여
상업 어음 계약에서
발행 함수인 `issue`를 호출합니다.

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

게이트웨이를 사용하여 트랜잭션을 제출한 후에 `issue.js` 파일이 게이트웨이 연결을 끊습니다. 

```
gateway.disconnect();
```
{:codeblock}

이 명령은 게이트웨이에서 열린 GRPC 연결을 닫습니다. 연결을 닫으면 네트워크 리소스가 절약되고 성능이 향상됩니다.

이 단계 및 **4단계**에서 편집을 완료하고 `issue.js`를 저장하고 닫으십시오. 다음 명령을 사용하여
새 상업 어음을 작성하는 트랜잭션을 제출하십시오.

```
node issue.js
```
{:codeblock}

트랜잭션이 완료되면 터미널에서 다음 출력을 볼 수 있어야 합니다.

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### 6단계: digibank로 샘플 작동

magnetocorp로 작동하는 상업 어음을 작성한 후에 digibank로 튜토리얼을 작동하여 상업 어음을 구매하고
상환할 수 있습니다. magnetocorp와 동일한 조직을 사용하는 digibank 애플리케이션 코드를
사용하거나 다른 조직의 CA, 피어 및 연결 프로파일을 사용할 수 있습니다. [네트워크 빌드 튜토리얼](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network)을
완료한 경우, Org2로 튜토리얼을 작동할 수 있는 좋은 기회입니다.

`digibank/application` 디렉토리로 이동하십시오. **3단계**에서 제공되는
지시사항에 따라 digibank로서 트랜잭션에 서명하는 인증서 및 지갑을 생성하십시오. 그런 다음, `buy.js` 파일을
사용하여 magnetocorp로부터 상업 어음을 구매하고 `redeem.js`를 사용하여 어음을 상환하십시오. **4단계** 및 **5단계**에
따라 해당 파일이 올바른 연결 프로파일, 채널 및 스마트 계약을 가리키도록 편집하십시오.

## 하위 레벨 Fabric SDK API를 사용하여 네트워크에 연결
{: #ibp-console-app-low-level}

기존 애플리케이션 코드를 보존하거나 Node.js 외의 언어에 대해 Fabric SDK를 사용하려면 계속 하위 레벨 Fabric SDK API를
사용하여 네트워크에 연결할 수 있습니다. 콘솔을 사용하여 [연결 프로파일을 다운로드](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)하십시오. 그런
다음 연결 프로파일에서 직접 채널의 피어 및 순서 지정 노드의 엔드포인트를 가져오거나 노드
엔드포인트 정보를 사용하여 수동으로 피어 및 순서 지정자 오브젝트를 추가하십시오. 또한
CA를 사용하여 [애플리케이션 ID를 작성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)한
다음 클라이언트 측에서 CA 엔드포인트 정보 등록을 사용하거나
콘솔을 사용하여 인증서를 생성해야 합니다.

[Fabric 노드 SDK ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io "Fabric 노드 SDK") 문서는
[연결 프로파일을 사용하여 네트워크에 연결하는 ![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/tutorial-network-config.html "프로파일 연결 튜토리얼"){:new_window} 방법에 대한 프로파일을 제공합니다. 튜토리얼에서는
연결 프로파일 내의 CA 엔드포인트 정보를 사용하여 SDK를 사용하는 키를 생성합니다. 또한 콘솔을 사용하여
공개 및 개인 키를 생성하고 해당 키를 PEM 형식으로 변환할 수 있습니다. 그런 다음 아래 코드를 사용하여 키를 직접
SDK의 [Fabric 클라이언트 클래스![외부 링크 아이콘](../images/external_link.svg "외부 링크 아이콘")](https://fabric-sdk-node.github.io/Client.html "Fabric 클라이언트 클래스")로 전달하여 사용자 컨텍스트를 설정할 수 있습니다.

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

하위 레벨 SDK API를 사용하여 네트워크에 연결하는 경우,
애플리케이션의 성능 및 가용성을 관리하기 위해 수행해야 하는 몇 가지 추가 단계가 있습니다. 자세한 정보는
[애플리케이션 연결성 및 가용성을 위한 우수 사례](/docs/services/blockchain/v10_application.html#dev-app-connectivity-availability)를 참조하십시오.

## CouchDB에서 인덱스 사용
{: #console-app-couchdb}

CouchDB를 상태 데이터베이스로 사용하는 경우, 채널의 상태 데이터에 대해 스마트 계약에서 JSON 데이터 조회를 시작할 수 있습니다. JSON 조회를 위한 색인을 작성한 후 스마트 계약에서
해당 색인을 사용할 것을 적극 권장합니다. 색인을 사용하는 경우 네트워크에서 트랜잭션 및 항목의 추가 블록을 세계 상태로 추가할 때 애플리케이션에서 효율적으로 데이터를 검색할 수 있습니다. 스마트 계약 및 애플리케이션을
사용하여 색인을 작성하는 방법을 배우려면 [CouchDB 사용 시의 우수 사례](/docs/services/blockchain/v10_application.html#dev-app-couchdb-indices)를 참조하십시오.

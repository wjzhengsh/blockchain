---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: vs code extension, Visual Studio Code extension, smart contract, development tools

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

# Visual Studio Code 확장을 사용하여 스마트 계약 개발
{: #develop-vscode}


{{site.data.keyword.blockchainfull}} Platform Visual Studio(VS) Code 확장은 Visual Studio Code 내에 스마트 계약을 개발, 패키지 및 테스트하기 위한 환경을 제공합니다. 이 확장을 사용하여 스마트 계약 프로젝트를 작성하고 비즈니스 로직 개발을 시작할 수 있습니다. 그런 다음 {{site.data.keyword.blockchainfull_notm}} Platform에 스마트 계약을 배치하기 전에 VS Code를 사용하여 Hyperledger Fabric의 사전 구성된 인스턴스로 로컬 시스템의 스마트 계약을 테스트할 수 있습니다. 이 튜토리얼에서는 VS Code 확장을 사용하는 방법에 대해 설명합니다.


![일반적인 스마트 계약 개발 워크플로우](images/SmartContractflow.png "일반적인 스마트 계약 개발 워크플로우")
*그림 1. {{site.data.keyword.blockchainfull_notm}} Platform VS Code 확장*을 사용한 일반적인 스마트 계약 개발 워크플로우  

<!--
<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="images/SmartContractflow.png" width="750" alt="Click a box to get more details on the process." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Create a smart contract project" title="Create a Smart contract project" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Develop contract code in VS Code" title="Create key pair" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract" alt="Package the smart contract" title="Package the smart contract" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-deploy" alt="Deploy locally to debug" title="Deploy locally to debug" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-exporting-deleting-smart-contract-package" alt="Export the package" title="Export the package" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-connecting-ibp" alt="Deploy to IBM Cloud" title="Deploy to IBM Cloud" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
-->

{{site.data.keyword.blockchainfull_notm}} Platform 확장은 Hyperledger Fabric 버전 1.4 이상을 사용하는 {{site.data.keyword.blockchainfull_notm}} Platform의 모든 인스턴스에서 원활하게 작동합니다.
{: note}

## 1단계: 무료로 {{site.data.keyword.blockchainfull_notm}} Platform VS Code 확장 설치
{: #develop-vscode-install}

{{site.data.keyword.blockchainfull_notm}} Platform VS Code 확장을 설치하기 전에 전제조건을 완료해야 합니다.

### 전제조건
{: #develop-vscode-prerequisites}

- **Visual Studio Code 설치**  
  [Visual Studio](https://code.visualstudio.com/) Code 편집기를 설치하십시오.  
- **Yeoman 설치**  
  Yeoman은 스켈레톤 스마트 계약 프로젝트를 작성하는 데 사용할 수 있는 생성기 도구입니다. `npm install -g yo` 명령을 사용하여 Yeoman을 설치하십시오.  
- **Docker 설치**  
Hyperledger Fabric의 사전 구성된 인스턴스를 실행하려면 [Docker ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.docker.com/)를 설치했는지 확인하십시오.  
- **운영 체제 요구사항**  
현재 확장은 Mac, Windows 및 Linux와 호환 가능합니다.  
- **Hyperledger Fabric 버전 요구사항**  
  이 확장은 Hyperledger Fabric 버전 1.4.0 이상과 호환 가능합니다.  

### 확장 설치
{: #develop-vscode-installing-the-extension}

1. [Visual Studio Code 확장 마켓플레이스 페이지 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform)로 이동하거나 Visual Studio Code 내 확장 패널에서 **{{site.data.keyword.blockchainfull_notm}} Platform**을 검색하십시오.
2. **설치**를 클릭하십시오.
3. Visual Studio Code를 다시 시작하여 확장 설치를 완료하십시오.

설치 후 VS Code 왼쪽에 있는 {{site.data.keyword.blockchainfull_notm}} 아이콘을 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform 패널을 열 수 있습니다.

이 확장은 Visual Studio Code 명령 팔레트에 새 명령을 추가합니다. 명령 팔레트를 사용하여 이 안내서에 자세히 설명되어 있는 많은 오퍼레이션을 완료할 수 있습니다.

## 2단계: 스마트 계약 프로젝트 작성
{: #develop-vscode-creating-a-project}

이 확장을 사용하여 Visual Studio Code에서 새 스마트 계약 프로젝트를 작성할 수 있습니다. 이 확장은 선택한 언어로 예제 자산을 관리하는 기본 스마트 계약을 작성합니다. 예제의 구조를 자체 비즈니스 로직을 개발하기 위한 시작점으로 사용할 수 있습니다. 이 확장은 Hyperledger Fabric 인스턴스에 스마트 계약을 배치하는 데 필요한 모든 종속 항목을 제공합니다.

1. **{{site.data.keyword.blockchainfull_notm}}** 탭을 여십시오. 스마트 계약 패키지 분할창에서 오버플로우 메뉴를 클릭하고 **스마트 계약 프로젝트 작성**을 클릭하십시오.
2. 스마트 계약을 작성할 언어를 선택하십시오. 현재 옵션은 JavaScript, TypeScript, Go 및 Java입니다. **참고:** {{site.data.keyword.blockchainfull_notm}} Platform은 Java 체인코드를 지원하지 않습니다.
3. 예제 계약에서 관리될 자산을 선택하십시오(예: ***채권***).
4. 프로젝트의 이름으로 폴더를 작성하고 이를 여십시오.
5. 새 프로젝트를 여는 방법을 선택하십시오. 이제 프로젝트 폴더가 열려야 합니다.

프로젝트가 열리면 왼쪽 분할창의 탐색기 창에서 새 스마트 계약을 찾을 수 있습니다. 프로젝트의 구조는 선택한 언어에 따라 다릅니다. 그러나 각 스마트 계약에는 다음과 같은 동일한 요소가 포함됩니다.
- 스마트 계약의 소스 코드. 이 확장은 예제 자산을 관리하는 일련의 함수에 `fabric-contract-api`를 사용하여 기본 스마트 계약을 빌드합니다. 예를 들어, ***채권***을 선택한 경우 `createBond`, `updateBond`, `readBond`, `bondExists` 및 `deleteBond` 함수를 찾습니다.
- 테스트 파일
- 수반하는 스마트 계약 종속 항목

## 3단계: 스마트 계약 패키지
{: #packaging-a-smart-contract}

{{site.data.keyword.blockchainfull_notm}} Platform 네트워크 또는 사전 구성된 Hyperledger Fabric 네트워크에서 설치하기 전에 스마트 계약을 `.cds` 형식으로 패키지해야 합니다. 스마트 계약을 패키지하려면 다음 단계를 완료하십시오.

1. VS Code에서 **{{site.data.keyword.blockchainfull_notm}} Platform** 패널로 이동하십시오. 스마트 계약 프로젝트가 파일 뷰어에 열려 있는지 확인하십시오.
2. **스마트 계약 패키지** 분할창에서 **+**를 클릭하십시오.
  - 하나의 스마트 계약 프로젝트가 있는 경우 자동으로 패키지되고 **스마트 계약 패키지** 분할창에 표시됩니다.
  - 여러 스마트 계약 폴더가 열려 있으면 어떠한 스마트 계약 폴더를 패키지할 것인지를 묻는 메시지가 표시됩니다.
  - 스마트 계약 폴더가 열려 있지 않으면 오류 메시지가 표시됩니다.

### 스마트 계약 패키지 내보내기, 가져오기 및 삭제
{: #develop-vscode-exporting-deleting-smart-contract-package}

스마트 계약 프로젝트를 패키지한 후 VS Code에서 이를 내보낼 수 있습니다.

1. {{site.data.keyword.blockchainfull_notm}} Platform 확장 패널에서 스마트 계약 패키지를 마우스 오른쪽 단추로 클릭하고 **패키지 내보내기**를 선택하십시오.
2. 스마트 계약 패키지를 저장할 디렉토리를 선택하고 **내보내기**를 클릭하십시오.

기존 스마트 계약 패키지를 {{site.data.keyword.blockchainfull_notm}} Platform 분할창으로 가져올 수도 있습니다.

1. **스마트 계약 패키지** 분할창에서 오버플로우 메뉴를 클릭하고 **패키지 가져오기**를 선택하십시오.
2. 가져올 스마트 계약 패키지를 찾아보고 **가져오기**를 클릭하십시오.

**패키지 삭제**를 클릭하여 스마트 계약 패키지를 패키지 목록에서 제거할 수도 있습니다.

## 4단계: 스마트 계약을 사전 구성된 Hyperledger Fabric 네트워크에 배치
{: #develop-vscode-deploy}

VS Code를 사용하여 이 확장이 로컬 시스템에 작성하는 사전 구성된 Hyperledger Fabric 네트워크에 스마트 계약을 배치할 수 있습니다. 이렇게 하면 스마트 계약을 라이브 네트워크에 배치하기 전에 설치, 인스턴스화 및 테스트할 수 있습니다.

### 사전 구성된 Hyperledger Fabric 네트워크 배치
{: #develop-vscode-connecting-and-disconnecting}

사전 구성된 네트워크를 배치하려면 다음 단계를 사용하십시오.

1. Docker가 시스템에서 실행 중인지 확인하십시오.
2. VS Code에서 **{{site.data.keyword.blockchainfull_notm}} Platform** 탭을 여십시오.
3. **로컬 Fabric 오퍼레이션** 분할창에서 **로컬 Fabric 런타임**을 클릭하십시오. Docker가 실행 중인 경우 로컬 Hyperledger Fabric 인스턴스가 다운로드되고 시작되어야 합니다.
4. **Fabric 게이트웨이** 분할창에서 **local_fabric**을 두 번 클릭하여 로컬 네트워크에 연결하십시오. 기본적으로 연결 시 Fabric 지갑 분할창의 관리자 ID를 사용합니다. **로컬 Fabric 오퍼레이션** 분할창에서 인증 기관 노드를 마우스 오른쪽 단추로 클릭하여 새 ID를 작성할 수 있습니다. 그런 다음 이 새로운 ID가 지갑에 추가되고 **local_fabric** 연결과 연관될 수 있습니다.

VS Code 확장은 하나의 순서 지정자, 하나의 피어 및 하나의 인증 기관을 포함하는 기본 Fabric 네트워크를 작성합니다. 피어가 `mychannel`이라는 채널에 가입됩니다. **로컬 Fabric 오퍼레이션** 분할창에서 네트워크에 속하는 노드, 조직 및 채널의 목록을 찾을 수 있습니다. 이러한 노드 위에서 설치되고 인스턴스화된 스마트 계약의 목록을 찾을 수 있습니다.

### 사전 구성된 네트워크 중지, 다시 시작 및 제거
{: #develop-vscode-stop-Fabric-runtime}

사전 구성된 네트워크가 작성된 후 이 네트워크를 중지하거나 다시 시작할 수 있습니다.

1. **로컬 Fabric 오퍼레이션** 분할창에서 오버플로우 메뉴를 클릭하십시오.
2. 컨테이너를 중지하거나 다시 시작하려면 **Fabric 런타임 다시 시작** 또는 **Fabric 런타임 중지**를 선택하십시오.

연결 세부사항은 현재 프로젝트 디렉토리에 포함된 **local_fabric**이라는 디렉토리에 저장됩니다. **Fabric 런타임 해체**를 선택하여 로컬 Fabric 네트워크를 완전히 제거할 수도 있습니다. **참고:** 이렇게 하면 원장 및 세계 상태 데이터가 손상됩니다.

### 사전 구성된 네트워크에 스마트 계약 배치
{: #develop-vscode-deploy-smart-contract}

**스마트 계약 패키지** 분할창에 있는 모든 패키지를 실행 중인 사전 구성된 네트워크에 배치할 수 있습니다.

먼저 피어에 스마트 계약을 설치해야 합니다.

1. **로컬 Fabric 오퍼레이션** 분할창에서 **스마트 계약 설치**를 클릭하십시오.
2. 스마트 계약을 설치할 피어를 선택하십시오.
3. 설치할 스마트 계약 패키지를 찾아보고 **설치**를 클릭하십시오.

다음으로 채널에서 스마트 계약을 인스턴스화할 수 있습니다.

1. **로컬 Fabric 오퍼레이션** 분할창에서 **스마트 계약 인스턴스화**를 클릭하십시오.
2. 인스턴스화할 설치된 스마트 계약을 선택하십시오.
3. (선택사항) 스마트 계약에서 인스턴스화 함수의 이름을 입력하십시오. 기본 스마트 계약 템플리트를 사용한 경우 인스턴스화 함수가 사용되지 않습니다.
4. (선택사항) 인스턴스화 함수에 필요한 인수를 입력하십시오.
5. (선택사항) 스마트 계약에서 개인용 데이터를 사용하는 경우 콜렉션 구성 파일을 찾아보십시오.
6. **인스턴스화**를 클릭하십시오.

스마트 계약 코드를 변경하고 다시 패키지하는 경우 인스턴스화된 스마트 계약을 업그레이드하여 새 버전을 네트워크에 배치할 수 있습니다.

1. 업그레이드할 스마트 계약이 인스턴스화되었는지 확인하십시오.
2. 동일한 네트워크에서 피어에 대한 스마트 계약의 새 버전을 설치하십시오.
3. 인스턴스화된 스마트 계약을 마우스 오른쪽 단추로 클릭하고 **스마트 계약 업그레이드**를 선택하십시오.
4. (선택사항) 새 스마트 계약이 인스턴스화된 후 트랜잭션을 실행하십시오.

### 스마트 계약과 상호작용
{: #develop-vscode-submitting-transactions}

스마트 계약이 설치되고 인스턴스화된 후 **Fabric 게이트웨이** 분할창을 사용하여 트랜잭션을 스마트 계약 내부의 기능에 제출할 수 있습니다.

1. 스마트 계약이 설치되고 인스턴스화되었으며 네트워크에 연결되어 있는지 확인하십시오.
2. 연결 분할창에서 **인스턴스화된 스마트 계약**을 펼치히십시오.
3. 상호작용할 스마트 계약을 펼치십시오. 스마트 계약 아래에 나열된 트랜잭션 목록을 찾습니다.
4. 제출할 트랜잭션을 마우스 오른쪽 단추로 클릭하고 **트랜잭션 제출**을 선택하십시오. 예를 들어, 예제 채권 스마트 계약을 작성하고 패키지한 경우 **createBond**를 클릭하십시오.
5. 트랜잭션에 필요한 인수를 입력하고 **Enter**를 누르십시오. 예를 들어, 첫 번째 채권을 작성하려면 `["bond01","100"]`을 입력하십시오.

### 애플리케이션을 사전 구성된 네트워크에 연결
{: #develop-vscode-exploring-connection-details}

클라이언트 애플리케이션을 사전 구성된 네트워크에 연결하고 트랜잭션을 스마트 계약에 제출하여 클라이언트 애플리케이션을 테스트할 수 있습니다.

먼저 연결 프로파일을 내보내야 합니다.

1. 네트워크를 시작하고 **로컬 Fabric 오퍼레이션** 분할창에서 **노드**를 펼치십시오.
2. 피어를 마우스 오른쪽 단추로 클릭하고 **연결 프로파일 내보내기**를 선택하십시오.

그런 다음 Fabric SDK 및 연결 프로파일을 사용하여 사용자 이름 `admin` 및 비밀번호 `adminpw`로 관리자 ID를 등록할 수 있습니다. 이후 이 ID를 사용하여 스마트 계약을 호출하거나 추가 사용자를 등록할 수 있습니다.

## 5단계: 개발 모드를 사용하여 스마트 계약 디버깅
{: #develop-vscode-development-mode}

변경할 때마다 스마트 계약을 다시 패키지하고 업그레이드할 필요 없이 **개발 모드**에서 사전 구성된 네트워크를 실행하여 스마트 계약을 반복적으로 개발하고 디버그할 수 있습니다. 스마트 계약을 디버깅하면 중단점 및 출력이 포함된 스마트 계약 트랜잭션을 실행할 수 있고, 이에 따라 트랜잭션이 예정대로 작동되는지 확인됩니다.

사전 구성된 네트워크에서 개발 모드를 사용하려면 다음 단계를 사용하십시오.

1. 네트워크가 시작된 후 **로컬 Fabric 오퍼레이션** 분할창에서 **노드** 섹션을 펼치십시오.
2. 피어를 마우르 오른쪽 단추로 클릭하고 **개발 모드 전환**을 선택하십시오.

정상 오퍼레이션에서는 피어가 인스턴스화된 스마트 계약을 실행하기 위한 체인코드 컨테이너를 작성하고 유지보수합니다. 개발 모드로 전환하면 피어에서 체인코드 컨테이너가 수동으로 실행되도록 허용하므로 VS Code **디버그** 보기를 사용하여 스마트 계약에 대한 업데이트를 실행 중인 네트워크에 직접 배치할 수 있습니다.

1. 개발 모드에 있는 **local_fabric** 연결에 연결되었는지 확인하십시오.
2. 스마트 계약 프로젝트를 여십시오.
3. 왼쪽 탐색줄에 있는 VS Code에서 **디버그** 보기를 여십시오.
4. 왼쪽 상단에 있는 드롭 다운 목록에서 **스마트 계약 디버깅 구성**을 선택하십시오.
5. **재생** 단추를 클릭하여 스마트 계약을 패키지하고 설치하십시오.
6. 스마트 계약 파일의 관련 행 번호를 클릭하여 스마트 계약에 중단점을 추가하십시오.
7. 디버그 도구 모음에서 **블록체인** 단추를 클릭하여 스마트 계약을 인스턴스화하십시오.
8. 디버그 도구 모음에서 **블록체인** 단추를 클릭하여 트랜잭션을 제출하거나 평가하십시오.

디버깅 중에 스마트 계약을 수정하려면 스마트 계약을 변경한 후 **다시 시작** 단추를 클릭하십시오. 디버깅 다시 시작은 계약을 다시 인스턴스화할 필요가 없음을 의미합니다.

## 6단계: 인스턴스화된 스마트 계약 테스트
{: #develop-vscode-testing-instantiated-smart-contract}

사용자가 연결하는 네트워크에서 인스턴스화된 스마트 계약에 대한 테스트를 생성할 수 있습니다. 테스트는 **JavaScript** 또는 **TypeScript**로 생성되고 실행되거나 디버깅될 수 있습니다.

1. 스마트 계약이 인스턴스화되었는지 확인하십시오.
2. **인스턴스화된 스마트 계약** 아래에서 테스트를 생성할 스마트 계약을 오른쪽 마우스 단추로 클릭하십시오.
3. **스마트 계약 테스트 생성**을 선택하십시오.
4. 테스트 파일의 언어를 **JavaScript** 또는 **TypeScript** 중에서 선택하십시오. {{site.data.keyword.blockchainfull_notm}} Platform 확장이 필수 npm 모듈을 설치하고 테스트 파일을 빌드합니다.

테스트 파일이 빌드된 후 파일에서 **테스트 실행** 단추를 클릭하여 테스트를 실행할 수 있습니다.


## 7단계: {{site.data.keyword.blockchainfull_notm}} Platform 네트워크에 연결
{: #develop-vscode-connecting-ibp}

이 확장을 사용하여 {{site.data.keyword.blockchainfull_notm}} Platform에 연결하고 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔 UI를 사용하여 설치되고 인스턴스화된 스마트 계약을 호출할 수도 있습니다.

{{site.data.keyword.blockchainfull_notm}} Platform의 인스턴스와 연관된 {{site.data.keyword.blockchainfull_notm}} Platform 콘솔을 여십시오. **스마트 계약** 탭으로 이동하십시오. 스마트 계약 탭의 **인스턴스화된 스마트 계약** 테이블을 사용하여 [연결 프로파일](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile)을 로컬 파일 시스템에 다운로드하십시오. 그런 다음 CA를 사용하여 [애플리케이션 ID를 작성](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities)하고 enrollID 및 시크릿을 저장하십시오. 아래의 단계에 따라 VS Code에서 {{site.data.keyword.blockchainfull_notm}} Platform에 연결하십시오.

1. _{{site.data.keyword.blockchainfull_notm}} Platform_ 탭을 여십시오.
2. **Fabric 게이트웨이** 분할창에서 **+**를 클릭하십시오.
3. 연결의 이름을 입력하십시오.
4. 연결 프로파일의 완전한 파일 경로를 입력하십시오. 이제 연결이 **local_fabric** 아래의 연결 목록에 표시되어야 합니다.
5. **Fabric 지갑** 분할창에서 **+**를 클릭하십시오.
6. 옵션에서 **새 지갑 작성 및 ID 추가**를 선택하십시오. 지갑과 ID의 이름을 제공하십시오.
7. 조직의 MSP ID를 입력하십시오.
8. **게이트웨이를 선택하고 등록 ID 및 시크릿** 옵션을 선택하고 위에서 작성한 게이트웨이를 선택하십시오.
9. 콘솔을 사용하여 작성한 애플리케이션 ID의 enrollID 및 시크릿을 입력하십시오. 새 ID가 **Fabric 지갑** 분할창에서 작성됩니다.
10. 이제 {{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 인스턴스에 연결할 수 있습니다. 연결 이름을 두 번 클릭하고 방금 작성한 지갑의 이름을 선택하십시오. 게이트웨이를 마우스 오른쪽 단추로 클릭하고 **지갑 연관**을 선택하여 사용자가 작성한 지갑을 게이트웨이와 연관시킬 수도 있습니다. 이렇게 하면 연결할 때마다 동일한 지갑을 사용할 수 있습니다.

VS Code에서 {{site.data.keyword.blockchainfull_notm}} Platform에 연결한 후 게이트웨이 아래에서 조직 피어가 가입한 채널의 목록을 볼 수 있습니다. 각 채널 아래에서 각 채널에서 인스턴스화된 스마트 계약과 각 스마트 계약 내의 함수 목록을 볼 수 있습니다. 함수를 마우스 오른쪽 단추로 클릭하고 **트랜잭션 제출**을 클릭한 후 필수 인수를 전달하여 트랜잭션을 네트워크에 제출할 수 있습니다. 채널에서 인스턴스화된 스마트 계약에 대한 테스트 파일을 생성할 수도 있습니다.

### 지갑 및 사용자 추가
{: #develop-vscode-add-a-wallet}

인증서 및 개인 키를 사용하여 새 지갑을 작성하려면 아래의 단계를 사용하십시오.

1. **Fabric 지갑** 분할창에서 **+**를 클릭하십시오.
2. 옵션에서 **새 지갑을 작성하고 ID를 추가**하도록 선택하십시오. 지갑과 ID의 이름을 제공하십시오.
3. 조직의 MSP ID를 입력하십시오.
4. 인증서 및 개인 키를 추가하도록 선택하십시오.
5. 인증서 및 개인 키를 사용하는 경우 인증서 및 개인 키를 찾아보십시오.

이미 작성된 지갑에 새 사용자를 추가할 수도 있습니다.

1. **Fabric 지갑** 분할창에서 지갑을 마우스 오른쪽 단추로 클릭하고 **ID 추가**를 선택하십시오.
2. ID와 MSPID의 이름을 제공하십시오.
3. 인증서 및 개인 키 또는 등록 ID 및 시크릿을 사용하도록 선택하십시오.
4. 인증서 및 개인 키를 사용하는 경우 인증서 및 개인 키를 찾아보십시오.
5. 등록 ID 및 시크릿을 사용하는 경우 등록할 게이트웨이를 선택하고 등록 ID 및 시크릿을 입력하십시오.

### 연결 편집, 끊기 및 삭제
{: #develop-vscode-editing-connection}

확장의 왼쪽 하단에서 연결을 마우스 오른쪽 단추로 클릭하여 ID 추가, 연결 편집 또는 연결 삭제를 위한 옵션이 포함된 컨텍스트 메뉴를 여십시오.

연결을 편집하려면 다음 단계를 완료하십시오.
1. **연결 편집** 옵션을 선택하십시오. **사용자 설정** 페이지가 열리며 연결 세부사항이 강조표시되어 있습니다.
2. 필요한 모든 변경을 수행하고 설정 페이지를 저장하십시오.

네트워크에서 연결을 끊을 준비가 되면 **Fabric 게이트웨이** 분할창의 오른쪽 상단에 있는 **연결 끊기** 아이콘을 클릭하십시오.

연결을 삭제하려면 연결을 마우스 오른쪽 단추로 클릭하고 **게이트웨이 삭제**를 선택하십시오.

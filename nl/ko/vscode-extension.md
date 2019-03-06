---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# 스마트 계약을 위한 도구
{: #develop-vscode}

***[이 페이지가 도움이 되었습니까? 알려주십시오.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform Visual Studio Code 확장은 스마트 계약 패키지의 개발, 패키지 및 배치를 위해 Visual Studio Code 내에 환경을 제공합니다. 확장에는 간소화된 로컬 스마트 계약 개발을 위해 Hyperledger Fabric의 사전 구성된 로컬 인스턴스를 설정할 명령도 포함됩니다.

**참고:** {{site.data.keyword.blockchainfull_notm}} Platform 확장은 {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜 네트워크 및 Hyperledger Fabric 버전 1.3.x 이상과 호환 가능합니다.

## 전제조건
{: #develop-vscode-prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 확장을 설치하기 전에 다음 전제조건을 완료하십시오.

- **Yeoman 설치**

  Yeoman은 스켈레톤 스마트 계약 프로젝트를 작성하는 데 사용되는 생성기 도구입니다. `npm install -g yo` 명령을 사용하여 Yeoman을 설치하십시오.

- **Yeoman을 위한 Fabric 생성기 설치**

  Yeoman 설치 후 스켈레톤 스마트한 계약 패키지를 작성하기 위한 Fabric 생성기를 설치하십시오. `npm install -g generator-fabric` 명령을 사용하여 Fabric 생성기를 설치하십시오.

- **Docker 설치**

  Hyperledger Fabric의 사전 구성된 인스턴스를 실행하려면 [Docker ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://www.docker.com/)를 설치했는지 확인하십시오.

- **운영 체제 요구사항**

  현재 확장은 Mac, Windows 및 Linux와 호환 가능합니다.

- **Hyperledger Fabric 버전 요구사항**

  이 확장은 Hyperledger Fabric 버전 1.3.0 이상과 호환 가능합니다.

- **Hyperledger Fabric 버전 요구사항**

  이 확장은 Hyperledger Fabric 버전 1.3.0 이상과 호환 가능합니다.

## 확장 설치
{: #develop-vscode-installing-the-extension}

1. [Visual Studio Code 확장 마켓플레이스 페이지 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform)로 이동하거나 Visual Studio Code 내 확장 패널에서 **{{site.data.keyword.blockchainfull_notm}} Platform**을 검색하십시오.
2. **설치**를 클릭하십시오.
3. Visual Studio Code를 다시 시작하여 확장 설치를 완료하십시오.

## Visual Studio Code 명령 팔레트
{: #develop-vscode-command-palette}

이 확장은 많은 명령을 Visual Studio Code 명령 팔레트에 추가합니다. 이 문서에 설명된 다수의 오퍼레이션도 Visual Studio Code 명령 팔레트에서 명령을 실행하여 시작될 수 있습니다.

## 프로젝트 작성
{: #develop-vscode-creating-a-project}

새 프로젝트를 작성하려면 다음 단계를 완료하십시오.

1. 명령 팔레트를 열고 **블록체인: 스마트 계약 프로젝트 작성** 명령을 실행하십시오.
2. 스마트 계약을 작성할 언어를 선택하십시오. 현재 옵션은 JavaScript 또는 TypeScript입니다.
3. 프로젝트의 이름으로 폴더를 작성하고 이를 여십시오.
4. 새 프로젝트를 여는 방법을 선택하십시오. 이제 프로젝트 폴더가 열려야 합니다.

## 네트워크에 연결 및 네트워크에서 연결 끊기
{: #develop-vscode-connecting-and-disconnecting}

이 확장을 사용하는 경우 Hyperledger Fabric 인스턴스에서 피어 및 채널에서 스마트 계약 패키지를 설치하고 시작합니다. 이 확장은 Docker를 사용하여 Hyperledger Fabric의 사전 구성된 로컬 인스턴스를 초기화할 수 있습니다.

### 사전 구성된 Hyperledger Fabric 인스턴스에 연결
{: #develop-vscode-connecting-to-preconfigured-Fabric-instance}

사전 구성된 Hyperledger Fabric 인스턴스에 연결하려면 먼저 Docker가 머신에서 실행 중인지 확인하십시오.

1. Visual Studio Code에서 **{{site.data.keyword.blockchainfull_notm}} Platform** 탭을 여십시오.
2. **{{site.data.keyword.blockchainfull_notm}} Platform** 분할창에서 **local_fabric**을 클릭하십시오. Docker가 실행 중인 경우 로컬 Hyperledger Fabric 인스턴스가 다운로드되고 시작되어야 합니다.
3. 로컬 Hyperledger Fabric 인스턴스가 시작된 후 **local_fabric**을 두 번 클릭하여 연결하십시오. 이제 `mychannel`이라는 채널이 표시되어야 합니다.

#### 사전 구성된 Hyperledger Fabric 런타임 다시 시작
{: #develop-vscode-restarting-Fabric-runtime}

`local_fabric` 런타임을 다시 시작하려면 다음을 수행하십시오.

1. `local_fabric` 연결이 설정되어 있으면 이를 오른쪽 마우스 단추로 클릭하십시오.
2. **Fabric 런타임 다시 시작**을 선택하십시오.

Hyperledger Fabric 컨테이너가 중지되고 다시 시작됩니다.

#### Hyperledger Fabric 런타임 분석
{: #develop-vscode-teardown-Fabric}

`local_fabric` 런타임을 분석하려면 다음을 수행하십시오.

1. `local_fabric` 연결이 설정되어 있으면 이를 오른쪽 마우스 단추로 클릭하십시오.
2. **Fabric Runtime 분석**을 선택하십시오.

`local_fabric` 네트워크의 분석을 수행하면 모든 Hyperledger Fabric 컨테이너가 닫힙니다. **참고**: 이로 인해 원장과 세계 상태 데이터가 손상됩니다.

#### 사전 구성된 Hyperledger Fabric 런타임의 개발 모드 사용
{: #develop-vscode-enabling-development-mode-Fabric}

일반적인 오퍼레이션에서 피어가 인스턴스화된 스마트 계약을 실행하도록 체인코드 컨테이너를 작성하고 유지보수합니다. 개발 모드로 전환하여 피어는 체인코드 컨테이너를 수동으로 실행할 수 있습니다. 체인코드 컨테이너를 수동으로 실행하면 명령행 또는 터미널에서 스마트 계약의 반복적인 개발 및 디버깅이 지원됩니다.

사전 구성된 Hyperledger Fabric 런타임으로 개발 모드를 사용으로 설정하려면 다음을 수행하십시오.

1. `local_fabric` 연결이 설정되어 있으면 이를 오른쪽 마우스 단추로 클릭하십시오.
2. **개발 모드 전환**을 선택하십시오.

개발 모드는 확장의 디버깅 기능을 사용하기 위해 사용으로 설정되어야 합니다.

### {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜에 연결
{: #develop-vscode-connecting-to-Starter-Plan}

{{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code 확장은 {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜의 인스턴스에 연결하는 데 사용될 수 있습니다. 연결된 후 확장은 스마트 계약을 개발하고 배치하는 데 사용될 수 있습니다.

**참고:** 현재 확장은 {{site.data.keyword.blockchainfull_notm}} Platform 엔터프라이즈 플랜과 호환 가능하지 않습니다.

1. {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜의 인스턴스가 없으면 [작성 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://console.bluemix.net/catalog/services/blockchain)하십시오.
2. **실행**을 클릭하여 {{site.data.keyword.blockchainfull_notm}} Platform UI를 여십시오.
3. **개요** > **연결 프로파일** > **다운로드**를 클릭하여 연결 프로파일을 검색하십시오.
4. **인증 기관** > **인증서 생성**을 클릭하고 인증서 및 개인 키를 파일 시스템에 저장하여 필요한 관리자 인증서를 생성하십시오.
5. **멤버** > **인증서** > **인증서 추가**를 클릭하고 이전 단계에서 작성된 인증서를 선택하여 인증서를 {{site.data.keyword.blockchainfull_notm}} Platform 스타터 플랜 인스턴스에 추가하십시오.
6. Visual Studio Code에서 {{site.data.keyword.blockchainfull_notm}} Platform 확장 보기를 열고 **새 연결 추가**를 클릭하십시오.
7. 연결 이름 및 연결 프로파일에 대한 경로를 입력하고 기존 파일 시스템 [지갑 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "지갑")을 선택하거나 인증서 및 개인 키를 사용하여 새 지갑을 작성하십시오.

### 고유한 Hyperledger Fabric 인스턴스에 연결
{: #develop-vscode-connecting-to-own-Fabric-instance}

이 확장을 사용하여 Hyperledger Fabric의 사전 구성된 로컬 인스턴스에 연결하거나 고유한 Hyperledger Fabric 인스턴스에 연결할 수 있습니다. 고유한 Fabric 인스턴스에 연결하도록 선택하는 경우 Fabric 1.3.0 이상이어야 합니다.

고유한 Hyperledger Fabric 인스턴스에 연결하려면 다음 단계를 완료하십시오.

1. Visual Studio Code에서 **{{site.data.keyword.blockchainfull_notm}} Platform** 탭을 여십시오.
2. **{{site.data.keyword.blockchainfull_notm}} Platform** 분할창에서 **새 연결 추가**를 클릭하십시오.
3. 연결의 이름을 입력하십시오. 이름은 **{{site.data.keyword.blockchainfull_notm}} Platform** 분할창에 표시됩니다.
4. Hyperledger Fabric 연결 프로파일의 완전한 파일 경로를 입력하십시오.
5. 기존 파일 시스템 [지갑 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "지갑")을 선택하거나 Fabric ID에 대한 인증서 및 개인 키의 파일 경로를 입력하여 새 파일 시스템 지갑을 작성하십시오.
6. 연결이 `local_fabric` 아래의 연결 목록에 표시되어야 합니다. 연결할 연결 이름을 두 번 클릭하십시오.

기존 Hyperledger Fabric 인스턴스 버전 1.3.0 이상이 없으면 사전 구성된 Hyperledger Fabric 인스턴스를 사용하여 연결하거나 다음 명령을 실행하여 버전 1.3.0 이미지를 가져오고 태그를 지정할 수 있습니다.

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### 네트워크에서 연결 끊기
{: #develop-vscode-disconnecting}

네트워크에 연결하는 경우 Blockchain Connections 분할창의 오른쪽 상단에 있는 연결 끊기 아이콘을 클릭하여 연결을 닫을 수 있습니다. 또한 명령 팔레트에서 **블록체인에서 연결 끊기** 명령을 실행하여 연결을 끊을 수도 있습니다.

## 연결 편집 또는 삭제
{: #develop-vscode-editing-or-deleting-connection}

연결은 연결 분할창에서 편집되거나 삭제될 수 있습니다.

### 연결 편집
{: #develop-vscode-editing-connection}

연결을 편집하여 연결 프로파일의 파일 경로, 연결에 지정된 이름 및 파일 경로를 변경하여 인증서를 식별할 수 있습니다.

연결을 편집하려면 다음을 수행하십시오.

1. 확장의 왼쪽 하단에서 편집할 연결을 마우스 오른쪽 단추로 클릭하면 ID 추가, 연결 편집 또는 연결 삭제를 위한 옵션이 포함된 컨텍스트 메뉴가 열립니다.
2. **연결 편집**을 선택하십시오.
3. **사용자 설정** 페이지가 열리며 연결 세부사항이 강조표시되어 있습니다.
4. 모든 변경을 수행하고 설정 페이지를 저장하십시오.

### 연결 끊기
{: #develop-vscode-deleting-connection}

연결을 삭제하려면 다음을 수행하십시오.

1. 확장의 왼쪽 하단에서 편집할 연결을 마우스 오른쪽 단추로 클릭하면 ID 추가, 연결 편집 또는 연결 삭제를 위한 옵션이 포함된 컨텍스트 메뉴가 열립니다.
2. **연결 삭제**를 선택하십시오.
3. 연결 삭제를 확인하기 위한 대화 상자가 표시됩니다. **예**를 클릭하십시오.

연결이 삭제됩니다.

## ID 추가
{: #develop-vscode-adding-identities}

연결을 추가할 때 사용할 ID를 포함하는 기존 지갑을 선택하거나 인증서 및 ID에 대한 개인 키를 사용하여 새 지갑을 작성해야 합니다. 지갑에 대한 자세한 정보는 Fabric 문서에서 [지갑 주제 ![외부 링크 아이콘](images/external_link.svg "외부 링크 아이콘")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "지갑")를 참조하십시오.

이미 설정된 연결에 ID를 추가하려면 다음을 수행하십시오.

1. 확장의 왼쪽 하단에서 편집할 연결을 마우스 오른쪽 단추로 클릭하면 ID 추가, 연결 편집 또는 연결 삭제를 위한 옵션이 포함된 컨텍스트 메뉴가 열립니다.
2. **ID 추가**를 선택하십시오.
3. 추가할 ID를 포함하는 기존 지갑을 선택하거나 인증서 및 개인 키의 파일 경로를 제공하여 새 지갑을 작성하십시오.

## 연결 탐색
{: #develop-vscode-exploring-connections}

Hyperledger Fabric 인스턴스에 연결한 후 **{{site.data.keyword.blockchainfull_notm}} Platform** 분할창의 해당 채널에서 사용 가능한 채널 및 피어의 목록을 볼 수 있습니다. 구조에 익숙해지려면 1분 정도 Hyperledger Fabric 인스턴스를 탐색해보십시오. 연결 내 채널이 먼저 나열되고 채널 아래에는 해당 채널의 멤버인 피어의 목록과 해당 채널에 인스턴스화된 스마트 계약 패키지이 목록이 있습니다. 목록의 각 피어 아래에는 해당 피어에 설치된 모든 스마트 계약 패키지의 목록이 있습니다. 또한 설치되거나 인스턴스화된 스마트 계약 패키지 버전도 확인할 수 있습니다.

### 사전 구성된 Hyperledger Fabric 런타임의 연결 세부사항 내보내기
{: #develop-vscode-exploring-connection-details}

`local_fabric` 연결에 연결하는 데 필요한 연결 세부사항도 내보낼 수 있습니다. `local_fabric` 연결의 연결 세부사항은 Hyperledger Fabric 인스턴스에 연결하거나 Hyperledger Fabric와 상호작용하기 위한 클라이언트 애플리케이션을 테스트하는 데 유용합니다.

`local_fabric` 연결 세부사항을 내보내려면 다음을 수행하십시오.

1. 사전 구성된 Hyperledger Fabric 런타임을 시작하십시오.
2. `local_fabric` 연결을 마우스 오른쪽 단추로 클릭하고 **연결 세부사항 내보내기**를 선택하십시오.

연결 세부사항은 현재 프로젝트 디렉토리에 포함된 `local_fabric`이라고 하는 디렉토리에 저장됩니다.

## 스마트 계약 패키징
{: #packaging-a-smart-contract}

스마트 계약을 배치할 준비가 되면 이를 먼저 패키지해야 합니다. 스마트 계약을 패키지하려면 다음 단계를 완료하십시오.

1. Visual Studio Code에서 **{{site.data.keyword.blockchainfull_notm}} Platform** 패널로 이동하십시오.
2. **스마트 계약 패키지** 분할창에서 + 아이콘을 클릭하십시오. 스마트 계약 프로젝트가 파일 뷰어에 열려 있으면 자동으로 패키지되고 **스마트 계약 패키지** 분할창에 표시됩니다. 여러 스마트 계약 폴더가 열려 있으면 어떠한 스마트 계약 폴더를 패키지할 것인지를 묻는 메시지가 표시됩니다. 스마트 계약 폴더가 열려 있지 않으면 오류 메시지가 표시됩니다.

## 스마트 계약 패키지 설치
{: #develop-vscode-installing-smart-contract-packages}

Hyperledger Fabric의 인스턴스에 연결한 후 피어에 스마트 계약 패키지를 설치하고 인스턴스화할 수 있습니다.

1. {{site.data.keyword.blockchainfull_notm}} Platform 분할창에서 스마트 계약 패키지를 설치할 피어로 이동하십시오.
2. 피어를 마우스 오른쪽 단추로 클릭하여 스마트 계약 패키지를 설치하고 **스마트 계약 설치**를 선택하십시오.

## 스마트 계약 패키지 인스턴스화
{: #develop-vscode-instantiating-smart-contract-packages}

채널에서 실행을 시작하려면 설치된 스마트 계약 패키지를 먼저 인스턴스화해야 합니다.

1. 스마트 계약 패키지가 설치된 Hyperledger Fabric 인스턴스에 연결하십시오.
2. 스마트 계약 패키지를 인스턴스화할 채널을 오른쪽 마우스 단추로 클릭하고 **스마트 계약 인스턴스화**를 클릭하십시오.
3. 인스턴스화할 스마트 계약 패키지와 버전을 선택하십시오. 스마트 계약 패키지는 이 채널의 멤버인 피어에 설치되어야 합니다.
4. 스마트 계약에서 인스턴스화 기능의 이름을 입력하십시오.
5. 인스턴스화 기능에 필요한 인수를 입력하십시오.

## 스마트 계약 패키지 내보내기 또는 삭제
{: #develop-vscode-exporting-deleting-smart-contract-package}

스마트 계약을 패키지한 후 더 이상 필요하지 않으면 `.cds` 파일로 스마트 계약 패키지를 내보내거나 삭제할 수 있습니다.

스마트 계약 패키지를 삭제하려면 다음을 수행하십시오.

1. {{site.data.keyword.blockchainfull_notm}} Platform 확장 패널에서 스마트 계약 패키지를 마우스 오른쪽 단추로 클릭하여 삭제하십시오.
2. **패키지 삭제**를 선택하십시오.

이제 패키지가 스마트 계약 패키지의 목록에서 삭제되어 표시되지 않습니다.

스마트 계약 패키지를 내보내려면 다음을 수행하십시오.

1. {{site.data.keyword.blockchainfull_notm}} Platform 확장 패널에서 스마트 계약 패키지를 마우스 오른쪽 단추로 클릭하여 내보내십시오.
2. **패키지 내보내기**를 선택하십시오.
3. 스마트 계약 패키지를 저장할 디렉토리를 선택하고 **내보내기**를 클릭하십시오.

## 인스턴스화된 스마트 계약 테스트
{: #develop-vscode-testing-instantiated-smart-contract}

스마트 계약의 테스트는 스마트 계약이 인스턴스화된 후 생성될 수 있습니다. 테스트는 JavaScript 또는 TypeScript로 생성되고 실행되거나 디버깅될 수 있습니다.

스마트 계약 테스트를 생성하려면 다음을 수행하십시오.

1. 스마트 계약이 인스턴스화되었는지 확인하십시오.
2. **인스턴스화된 스마트 계약** 아래에서 테스트를 생성할 스마트 계약을 오른쪽 마우스 단추로 클릭하십시오.
3. **스마트 계약 테스트 생성**을 선택하십시오.
4. 이제 테스트 파일의 언어를 **JavaScript** 또는 **TypeScript** 중에서 선택하십시오. {{site.data.keyword.blockchainfull_notm}} Platform 확장은 이제 필요한 npm 모듈을 설치하고 테스트 파일을 빌드합니다.

테스트 파일이 빌드된 후 파일에서 **테스트 실행** 단추를 클릭하여 테스트를 실행할 수 있습니다.

## 사전 구성된 Hyperledger Fabric 런타임을 사용하여 스마트 계약 디버깅
{: #develop-vscode-debugging}

스마트 계약을 로컬 형식으로 디버깅할 수 있는 기능은
스마트 계약 개발자가 인스턴스화 후에 함수를 호출하려고 시도하기 전에 스마트 계약 함수를 반복하고 버그를 수정하는 데 도움을 줍니다. 스마트 계약을 디버깅하면 중단점 및 출력이 포함된 스마트 계약 트랜잭션을 실행할 수 있고, 이에 따라 트랜잭션이 예정대로 작동되는지 확인됩니다. 스마트 계약을 디버깅하려면 다음을 수행하십시오.

1. 개발 모드에 있는 `local_fabric` 연결에 연결되는지 확인하십시오.
2. 스마트 계약 프로젝트를 여십시오.
3. 왼쪽 탐색줄을 사용하여 Visual Studio Code에서 디버그 보기를 여십시오.
4. 오른쪽 상단의 드롭 다운을 사용하여 스마트 계약 디버깅 구성을 선택하십시오.
5. **재생** 단추를 클릭하여 스마트 계약을 패키지하고 설치하십시오.
6. 스마트 계약 파일의 관련 행 번호를 클릭하여 스마트 계약에 중단점을 추가하십시오.
7. 설치된 스마트 계약을 오른쪽 마우스로 클릭하고 **인스턴스화**를 선택하십시오. 이제 마우스 오른쪽 단추를 클릭하여 트랜잭션을 제출할 수 있고 실행이 정의된 중단점에서 일시정지됩니다.

디버깅 중에 스마트 계약을 변경하려면 스마트 계약을 변경한 후 **다시 시작** 단추를 클릭하십시오. 디버깅 다시 시작은 계약을 다시 인스턴스화할 필요가 없음을 의미합니다.

**참고**: 디버깅을 다시 시작하면 대형 스마트 계약에 대한 여러 변경사항에 맞게 로컬 메모리에서 스마트 계약이 저장되고, 사용자는 스마트 계약을 다시 인스턴스화해야 할 수 있습니다.

## 인스턴스화된 스마트 계약 업그레이드
{: #develop-vscode-upgrading-instantiated-smart-contract}

스마트 계약이 피어에서 설치되고 채널에서 인스턴스화된 후 스마트 계약의 새 버전을 배포하도록 스마트 계약을 업그레이드할 수 있습니다.

1. 업그레이드할 스마트 계약이 인스턴스화되었는지 확인하십시오.
2. 동일한 네트워크에서 피어에 대한 스마트 계약의 새 버전을 설치하십시오.
3. 인스턴스화된 스마트 계약을 오른쪽 마우스로 클릭하고 **스마트 계약 업그레이드**를 선택하십시오.
4. 필요에 따라 새 스마트 계약이 인스턴스화된 후에 실행할 트랜잭션을 선택하십시오.

## 트랜잭션 제출
{: #develop-vscode-submitting-transactions}

스마트 계약이 설치되고 인스턴스화된 후 {{site.data.keyword.blockchainfull_notm}} Platform 확장 패널의 연결 분할창에서 트랜잭션을 제출할 수 있습니다.

트랜잭션을 제출하려면 다음을 수행하십시오.

1. 스마트 계약이 설치되고 인스턴스화되었으며, 네트워크에 연결되어 있는지 확인하십시오.
2. 연결 분할창에서 **인스턴스화된 스마트 계약**을 펼치히십시오.
3. 제출될 트랜잭션이 포함된 스마트 계약을 펼치십시오.
4. 제출할 트랜잭션을 마우스 오른쪽 단추로 클릭하고 **트랜잭션 제출**을 선택하십시오.
5. 트랜잭션에 필요한 인수를 입력하고 **Enter**를 누르십시오.
